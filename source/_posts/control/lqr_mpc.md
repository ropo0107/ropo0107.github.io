---
title: LQR & MPC
categories: [控制]
tags:   [Control]
copyright: false
reward: false
rating: false
related_posts: false
date: 2024-05-15 16:51:55
---

## LQR
### code
```
#pragma once
#include <iostream>
#include <Eigen/Dense>

class LQR
{
public:
	LQR(Eigen::MatrixXd A,
		Eigen::MatrixXd B,
		Eigen::MatrixXd Q,
		Eigen::MatrixXd R);

	LQR(double Ts,
		Eigen::MatrixXd Q = Eigen::MatrixXd::Identity(2,2),
		Eigen::MatrixXd R = Eigen::MatrixXd::Identity(1, 1));

	~LQR();
	int initial();
	int step(Eigen::MatrixXd xd, Eigen::MatrixXd& xout);
	int step(double xin, double& xout);

private:

	int getLQRGain();
	Eigen::MatrixXd A;
	Eigen::MatrixXd B;
	Eigen::MatrixXd Q;
	Eigen::MatrixXd R;

	Eigen::MatrixXd K;
	Eigen::MatrixXd x;
	Eigen::MatrixXd u;

	bool alignment;
};

################################################

#include "lqr.h"

LQR::LQR(Eigen::MatrixXd A,
		 Eigen::MatrixXd B,
		 Eigen::MatrixXd Q,
		 Eigen::MatrixXd R){
	this->A = A;
	this->B = B;
	this->Q = Q;
	this->R = R;
	this->x = Eigen::MatrixXd::Zero(A.rows(), 1);
	this->u = Eigen::MatrixXd::Zero(B.cols(), 1);
	this->alignment = true;

	this->getLQRGain();
}

LQR::LQR(double Ts,
		 Eigen::MatrixXd Q,
		 Eigen::MatrixXd R) {
	Eigen::MatrixXd A(2, 2), B(2, 1);
	A << 1, Ts, 0, 1;
	B << 0, Ts;

	this->A = A;
	this->B = B;
	this->Q = Q;
	this->R = R;
	this->x = Eigen::MatrixXd::Zero(A.rows(), 1);
	this->u = Eigen::MatrixXd::Zero(B.cols(), 1);
	this->alignment = true;

	this->getLQRGain();
}

LQR::~LQR(){
}

int LQR::initial() {
	this->alignment = true;
	return 0;
}

int LQR::getLQRGain()
{
	// 获取增广矩阵
	size_t n = this->A.rows();
	size_t p = this->B.cols();

	Eigen::MatrixXd Ca = Eigen::MatrixXd::Zero(n, 2 * n + p);
	Ca.block(0, 0, n, n) = Eigen::MatrixXd::Identity(n, n);
	Ca.block(0, n, n, n) = -Eigen::MatrixXd::Identity(n, n);

	Eigen::MatrixXd Aa = Eigen::MatrixXd::Zero(2 * n + p, 2 * n + p);
	Aa.block(0, 0, n, n) = this->A;
	Aa.block(0, 2 * n, n, p) = this->B;
	Aa.block(n, n, n, n) = this->A;
	Aa.block(2 * n, 2 * n, p,p) = Eigen::MatrixXd::Identity(p, p);

	Eigen::MatrixXd Ba = Eigen::MatrixXd::Zero(2 * n + p, p);
	Ba.block(0, 0, n, p) = this->B;
	Ba.block(2 * n, 0, p, p) = Eigen::MatrixXd::Identity(p, p);

	Eigen::MatrixXd Qa = Ca.transpose() * this->Q * Ca;
	Eigen::MatrixXd Ra = this->R;

	std::cout << "Ca:" << std::endl << Ca << std::endl;
	std::cout << "Aa:" << std::endl << Aa << std::endl;
	std::cout << "Ba:" << std::endl << Ba << std::endl;
	std::cout << "Qa:" << std::endl << Qa << std::endl;
	std::cout << "Ra:" << std::endl << Ra << std::endl;

	// 计算LQR增益
	Eigen::MatrixXd P = Qa;
	int iter = 0;
	int maxIter = 150;
	double eps = 1e-2;
	double delta = P.norm();

	while (delta > eps)
	{
		Eigen::MatrixXd Pn = Aa.transpose() * P * Aa
			- Aa.transpose() * P * Ba * (Ra + Ba.transpose() * P * Ba).inverse()
			* Ba.transpose() * P * Aa + Qa;
		delta = fabs((Pn - P).norm());
		P = Pn;
		iter++;
		if (iter > maxIter)
		{
			break;
		}
	}

	this->K = -(Ba.transpose() * P * Ba + Ra).inverse()
		* Ba.transpose() * P * Aa;
	return 0;
}

int LQR::step(Eigen::MatrixXd xd, Eigen::MatrixXd& xout){
	size_t n = this->A.rows();
	size_t p = this->B.cols();

	if (this->alignment)
	{
		this->alignment = false;
		this->x = xd;
		this->u = Eigen::MatrixXd::Zero(p, 1);
	}

	Eigen::MatrixXd xa(2 * n + p, 1);
	xa << this->x, xd, this->u;
	Eigen::MatrixXd deltaU = this->K * xa;
	this->u = deltaU + this->u;
	this->x = this->A * this->x + this->B * this->u;
	xout = this->x;
	return 0;
}

int LQR::step(double xin, double& xout) {
	size_t n = this->A.rows();
	size_t p = this->B.cols();

	if (this->alignment)
	{
		this->alignment = false;
		this->x = Eigen::MatrixXd::Zero(n, 1);
		this->x(0, 0) = xin;
		this->u = Eigen::MatrixXd::Zero(p, 1);
	}
	Eigen::MatrixXd xa(2 * n + p, 1);
	Eigen::MatrixXd xd = Eigen::MatrixXd::Zero(n, 1);
	xd(0, 0) = xin;
	xa << this->x, xd, this->u;
	Eigen::MatrixXd deltaU = this->K * xa;
	this->u = deltaU + this->u;
	this->x = this->A * this->x + this->B * this->u;
	xout = this->x(0, 0);
	return 0;
}

```


## MPC
### code
```

# Maciejowski, J. M. (2002). Predictive control: with constraints.

#pragma once
#include <iostream>
#include <vector>
#include <Eigen/Dense>


class MPC
{
public:
	MPC(int predict,
		Eigen::MatrixXd A,
		Eigen::MatrixXd B,
		Eigen::MatrixXd Q,
		Eigen::MatrixXd R);

	MPC(double Ts, int predict,
		Eigen::MatrixXd Q = Eigen::MatrixXd::Identity(2, 2),
		Eigen::MatrixXd R = Eigen::MatrixXd::Identity(1, 1));

	~MPC();

	int initial();
	int step(double xin, double& xout);

private:

	int setup();
	int pred;					// MPC预测长度
	Eigen::MatrixXd A;
	Eigen::MatrixXd B;
	Eigen::MatrixXd Q;
	Eigen::MatrixXd R;
	Eigen::MatrixXd Phi;
	Eigen::MatrixXd Gamma;
	Eigen::MatrixXd Theta;

	Eigen::MatrixXd Qs;
	Eigen::MatrixXd Rs;

	Eigen::MatrixXd xdList;
	Eigen::MatrixXd x;
	Eigen::MatrixXd u;

	bool alignment;
};


##########################################################

#include "mpc.h"

MPC::MPC(int predict,
		 Eigen::MatrixXd A,
		 Eigen::MatrixXd B,
		 Eigen::MatrixXd Q,
		 Eigen::MatrixXd R){
	this->A = A;
	this->B = B;
	this->Q = Q;
	this->R = R;
	this->pred = predict;
	this->x = Eigen::MatrixXd::Zero(A.rows(), 1);
	this->u = Eigen::MatrixXd::Zero(B.cols(), 1);

	this->setup();
}

MPC::MPC(double Ts, int predict,
		 Eigen::MatrixXd Q,
		 Eigen::MatrixXd R) {
	Eigen::MatrixXd A(2, 2), B(2, 1);
	A << 1, Ts, 0, 1;
	B << 0, Ts;
	this->A = A;
	this->B = B;
	this->Q = Q;
	this->R = R;
	this->pred = predict;
	this->x = Eigen::MatrixXd::Zero(A.rows(), 1);
	this->u = Eigen::MatrixXd::Zero(B.cols(), 1);

	this->setup();
}


MPC::~MPC()
{
}

int MPC::initial() {
	this->alignment = true;


	return 1;
}

int MPC::setup()
{
	size_t n = this->A.rows();
	size_t p = this->B.cols();

	this->Phi.resize(n * this->pred, n);
	this->Gamma.resize(n * this->pred, p);
	this->Theta.resize(n * this->pred, this->pred * p);
	this->Theta = Eigen::MatrixXd::Zero(n * this->pred, this->pred * p);

	this->Phi.block(0, 0, n, n) = this->A;
	this->Gamma.block(0, 0, n, p) = this->B;
	for (size_t i = 1; i < this->pred; i++)
	{
		this->Phi.block(i * n, 0, n, n) = this->Phi.block((i - 1) * n, 0, n, n) * this->A;
		this->Gamma.block(i * n, 0, n, p) = this->Gamma.block((i - 1) * n, 0, n, p)
			+ this->Phi.block((i - 1) * n, 0, n, n) * this->B;
	}
	for (size_t i = 0; i < this->pred; i++)
	{
		this->Theta.block(i * n, i * p, n * (this->pred -i), p)
			= this->Gamma.block(0, 0, n * (this->pred - i), p);
	}

	Eigen::VectorXd diag_Qs = Q.diagonal().replicate(this->pred, 1);
	Eigen::VectorXd diag_Rs = R.diagonal().replicate(this->pred, 1);

	this->Qs = diag_Qs.asDiagonal();
	this->Rs = diag_Rs.asDiagonal();
	return 0;
}

int MPC::step(double xin, double& xout){
	size_t n = this->A.rows();
	size_t p = this->B.cols();

	Eigen::MatrixXd xd = Eigen::MatrixXd::Zero(n, 1);
	xd(0, 0) = xin;
	if (this->alignment)
	{
		this->alignment = false;
		this->x = xd;
		this->xdList.resize(n * this->pred, 1);
		this->xdList = xd.replicate(this->pred, 1); // 将xd重复预测长度
		this->u = Eigen::MatrixXd::Zero(p, 1);
	}
	else
	{
		this->xdList.topRows(n * (this->pred-1)) = this->xdList.bottomRows(n * (this->pred - 1));
		this->xdList.bottomRows(n) = xd;
	}
	Eigen::MatrixXd error = this->xdList - this->Phi * this->x - this->Gamma * this->u;
	Eigen::MatrixXd G = 2 * this->Theta.transpose() * this->Qs * error;
	Eigen::MatrixXd H = this->Theta.transpose() * this->Qs * this->Theta + this->Rs;

	Eigen::MatrixXd delatU = 0.5 * H.inverse() * G;
	this->u = delatU.topRows(p) + this->u;
	this->x = this->A * this->x + this->B * this->u;
	xout = this->x(0, 0);

	return 0;
}

```



## 参考
https://www.youtube.com/watch?v=1_UobILf3cc

https://rofunc.readthedocs.io/en/latest/planning/ilqr.html
