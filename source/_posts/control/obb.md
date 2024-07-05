---
title: OBB包围盒碰撞检测
categories: [控制]
tags:   [Control]
copyright: false
reward: false
rating: false
related_posts: false
date: 2024-05-15 16:51:55
---

# OBB包围盒碰撞检测

## OBB的生成
    假设一堆点， 求最小OBB。
- 求点的协方差矩阵，
- 协方差矩阵对应的特征向量，即为基于基坐标系的旋转矩阵， 既可以作为OBB三个轴

## 碰撞检测
    存在平面是的能够将两个box分离
- 两个box共六个面
- 两个box， 两两叉乘作为分离面的法向， 3 * 3 共九个面


## 代码
### 头文件
```
#pragma once
#include <Eigen/Dense>
#include <math.h>

struct OBBInfo {
	Eigen::Vector3d center;
	Eigen::Matrix3d rot;
	Eigen::Vector3d halfSize;
};

class OBB
{
public:
    OBB();
    OBB(OBBInfo);
	~OBB();

	int setParameter(OBBInfo info);
	int fromPoints(Eigen::MatrixXd points);
	int isCollision(OBB detect, bool &isCollision);
	int transform(Eigen::Matrix4d T);

private:
	bool getSeparatingPlane(Eigen::Vector3d pos,
		Eigen::Vector3d plane_normal,
		OBBInfo* box1,
		OBBInfo* box2);
	Eigen::MatrixXd computeCovariance(Eigen::MatrixXd data);

public:
	OBBInfo* box = nullptr;
};

```

### 代码实现
```
#include "OBB.h"
OBB::OBB() {
}

OBB::OBB(OBBInfo info) {
    box = new OBBInfo;
    box->center = info.center;
    box->rot = info.rot;
    box->halfSize = info.halfSize;
}

OBB::~OBB() {
    delete box;
}

int OBB::setParameter(OBBInfo info) {
    if (box == nullptr) {
        box = new OBBInfo;
    }
    box->center = info.center;
    box->rot = info.rot;
    box->halfSize = info.halfSize;
	return 0;
}

int OBB::fromPoints(Eigen::MatrixXd points) {
    Eigen::MatrixXd cov = this->computeCovariance(points);
    Eigen::SelfAdjointEigenSolver<Eigen::MatrixXd> es(cov);
    Eigen::MatrixXd value = es.eigenvalues().real();
    Eigen::MatrixXd vectors = es.eigenvectors().real();
    Eigen::MatrixXd newPoints = vectors.transpose() * points.transpose();

    Eigen::VectorXd min = newPoints.rowwise().minCoeff();
    Eigen::VectorXd max = newPoints.rowwise().maxCoeff();

    Eigen::MatrixXd boxVertex(8, 3);
    boxVertex << min[0], min[1], min[2],
        min[0], max[1], min[2],
        max[0], max[1], min[2],
        max[0], min[1], min[2],
        min[0], min[1], max[2],
        min[0], max[1], max[2],
        max[0], max[1], max[2],
        max[0], min[1], max[2];

    Eigen::MatrixXd sourceBoxVertex = vectors * boxVertex.transpose();
    if (box == nullptr) {
        box = new OBBInfo;
    }
    box->center = (sourceBoxVertex.col(0) + sourceBoxVertex.col(6))/2;
    box->rot = vectors;
    box->halfSize = (max-min)/2;
	return 0;
}

int OBB::isCollision(OBB object, bool &isCollision) {
    if (box == nullptr || object.box == nullptr) {
        return -1;
    }
    Eigen::Vector3d pos = object.box->center - box->center;
    if (!(getSeparatingPlane(pos, box->rot.col(0), box, object.box) ||
        getSeparatingPlane(pos, box->rot.col(1), box, object.box) ||
        getSeparatingPlane(pos, box->rot.col(2), box, object.box) ||
        getSeparatingPlane(pos, object.box->rot.col(0), box, object.box) ||
        getSeparatingPlane(pos, object.box->rot.col(1), box, object.box) ||
        getSeparatingPlane(pos, object.box->rot.col(2), box, object.box) ||
        getSeparatingPlane(pos, box->rot.col(0).cross(object.box->rot.col(0)), box, object.box) ||
        getSeparatingPlane(pos, box->rot.col(0).cross(object.box->rot.col(1)), box, object.box) ||
        getSeparatingPlane(pos, box->rot.col(0).cross(object.box->rot.col(2)), box, object.box) ||
        getSeparatingPlane(pos, box->rot.col(1).cross(object.box->rot.col(0)), box, object.box) ||
        getSeparatingPlane(pos, box->rot.col(1).cross(object.box->rot.col(1)), box, object.box) ||
        getSeparatingPlane(pos, box->rot.col(1).cross(object.box->rot.col(2)), box, object.box) ||
        getSeparatingPlane(pos, box->rot.col(2).cross(object.box->rot.col(0)), box, object.box) ||
        getSeparatingPlane(pos, box->rot.col(2).cross(object.box->rot.col(1)), box, object.box) ||
        getSeparatingPlane(pos, box->rot.col(2).cross(object.box->rot.col(2)), box, object.box)
        )) {
        isCollision = true;
    }
    else {
        isCollision = false;
    }
	return 0;
}

int OBB::transform(Eigen::Matrix4d T) {
    // 当前的rot、center可看作一个坐标系， 左乘T，
    if (box == nullptr) {
        return -1;
    }
    Eigen::Matrix4d startT = Eigen::Matrix4d::Identity();
    startT.topLeftCorner(3, 3) = box->rot;
    startT.topRightCorner(3, 1) = box->center;
    Eigen::Matrix4d endT = T * startT;
    box->rot = endT.topLeftCorner(3, 3);
    box->center = endT.topRightCorner(3, 1);
    return 0;
}

bool OBB::getSeparatingPlane(Eigen::Vector3d pos,
    Eigen::Vector3d plane_normal,
    OBBInfo* box1,
    OBBInfo* box2) {
    Eigen::Vector3d a = pos.cwiseProduct(plane_normal);
    Eigen::Vector3d b = box1->halfSize(0) * box1->rot.col(0).cwiseProduct(plane_normal);
    Eigen::Vector3d c = box1->halfSize(1) * box1->rot.col(1).cwiseProduct(plane_normal);
    Eigen::Vector3d d = box1->halfSize(2) * box1->rot.col(2).cwiseProduct(plane_normal);

    Eigen::Vector3d e = box2->halfSize(0) * box2->rot.col(0).cwiseProduct(plane_normal);
    Eigen::Vector3d f = box2->halfSize(1) * box2->rot.col(1).cwiseProduct(plane_normal);
    Eigen::Vector3d g = box2->halfSize(2) * box2->rot.col(2).cwiseProduct(plane_normal);

    double sa = std::abs(a.sum());
    double sm = std::abs(b.sum()) + std::abs(c.sum()) + std::abs(d.sum());
    double sn = std::abs(e.sum()) + std::abs(f.sum()) + std::abs(g.sum());
    bool isExist = false;
    if (sa > sm + sn) {
        isExist = true;
    }
    return isExist;
}

Eigen::MatrixXd OBB::computeCovariance(Eigen::MatrixXd mat) {
    Eigen::MatrixXd centered = mat.rowwise() - mat.colwise().mean();
    Eigen::MatrixXd cov = (centered.adjoint() * centered) / double(mat.rows() - 1);
    return cov;
}


```