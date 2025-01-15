---
title: 李群 & 李代数 
categories: [控制]
tags:   [Control]
copyright: false
reward: false
rating: false
related_posts: false
date: 2024-05-15 16:51:55
---



## 代码变换

- ![](/images/posts/comtrol/trans.png)
    - 图中 $θ$ 是旋转角， $a$ 是旋转轴

```
#include <iostream>
#include <eigen3/Eigen/Eigen>
#include <sophus/se3.hpp>
#include <cmath>
using namespace std;
class Lie_group_algebra
{
public:
    Lie_group_algebra(){}
    //特殊正交群 R 到 Phi=theta_n 的转换
    void R_2_Phi(const Eigen::Matrix3d& R, Eigen::Vector3d& Phi);
    //李代数Phi=theta_n 到特殊正交群R 的转换
    void Phi_2_R(const Eigen::Vector3d& Phi, Eigen::Matrix3d& R);
    //特殊欧式群T 到 epsilon=(p, Phi) 的转换
    void T_2_epsilon(const Eigen::Isometry3d& T, Eigen::Matrix<double, 6, 1>& epsilon);
    //李代数epsilon=(p, Phi) 到 特殊欧式群T 的转换
    void epsilon_2_T(const Eigen::Matrix<double,6,1>& epsilon, Eigen::Isometry3d& T);
};


int main(int argc, char** argv)
{
    Lie_group_algebra Lie;
    Eigen::Matrix3d R = Eigen::AngleAxisd( - 12 * M_PI, Eigen::Vector3d(4, 5, 7).normalized()).toRotationMatrix();
    Eigen::Vector3d t(11, 33, 99);
    //验证R 和 theta_n(Phi)之间的转换是否正确
    cout << "验证R 和 theta_n(Phi)之间的转换是否正确\n";
    cout << "原始R = \n" << R << endl;
    Eigen::Vector3d Phi;
    Lie.R_2_Phi(R, Phi);
    cout << "计算Phi = \n" << Phi << endl;
    Sophus::SO3d SO3_R(R);
    Eigen::Vector3d so3_Phi = SO3_R.log();
    cout << "验证so3_Phi = 利用Sophus的对数映射计算的, 理应等于Phi\n" << so3_Phi << endl;
    cout << "------------" << endl;
    Eigen::Matrix3d my_R;
    Lie.Phi_2_R(Phi, my_R);
    cout << "验证 my_R = 理应等于R\n" << my_R << endl;
    Sophus::SO3d Sophus_R = Sophus::SO3d::exp(Phi);
    cout << "Sophus_R = \n" << Sophus_R.matrix() << endl;
    //确实相等，R - my_R = 0
    cout << "R - my_R = 理应等于0矩阵\n" << R - my_R << endl;

    //验证T 和 (p,theta_n(Phi))之间的转换是否正确
    cout << "\n----------------------------------------------------";
    cout << "\n验证T 和 epsilon = (p, theta_n(Phi))之间的转换是否正确\n\n";
    Eigen::Isometry3d T(R);
    T.pretranslate(t);
    cout << "原始T = \n" << T.matrix() << endl;
    Eigen::Matrix<double,6,1> epsilon;
    Lie.T_2_epsilon(T, epsilon);
    cout << "计算epsilon = \n" << epsilon << endl;
    Sophus::SE3d SE3_T(R, t);
    Eigen::Matrix<double,6,1> se3_epsilon = SE3_T.log();
    cout << "验证se3_epsilon = 利用Sophus的对数映射计算的, 理应等于epsilon \n" << se3_epsilon << endl;
    Eigen::Isometry3d my_T = Eigen::Isometry3d::Identity();
    Lie.epsilon_2_T(epsilon, my_T);
    cout << "验证 my_T = 理应等于T \n" << my_T.matrix() << endl;
    Sophus::SE3d Sophus_T = Sophus::SE3d::exp(epsilon);
    cout << "Sophus_T = \n" << Sophus_T.matrix() << endl;
    //验证 my_T 是否等于 T
    cout << "T - my_T = 理应等于0矩阵 \n" << T.matrix() - my_T.matrix() << endl;
    return 0;
}
/// @brief 罗德里格斯公式的逆公式，对数映射，用 R 计算 Phi = theta*n  
/// 注意，当theta = 0 或 PI 时， 当=0时，R=I,任意n都满足，旋转轴n不唯一。当=PI时，
/// @param R 
/// @param Phi 
void Lie_group_algebra::R_2_Phi(const Eigen::Matrix3d& R, Eigen::Vector3d& Phi)
{
    //R = cos(theta)I + (1-cos(theta))nnT + sin(theta)n^
    //trace(R) = 3cos(theta) + 1 - cos(theta)
    //cos(theta) = (trace(R) - 1) / 2
    double cos_theta = (R.trace() - 1.0) / 2;
    // 这样的话，theta一定在[0,PI]内了
    double theta = acos(cos_theta); 
    //注意|cos_theta| = 1的情况，此时sin_theta = 0
    double sin_theta = sqrt(1.0 - cos_theta * cos_theta);
    //根据 n^ = (R - RT) / (2*sin(theta)) 求n; sin_theta = 0时，不能这么求
    if(fabs(cos_theta - 1.0) < 0.000001) //用==1判断浮点数相等不太好
    {
        cout << "Situation: theta = 0\n";
        //theta = 0,n任意
        Eigen::Vector3d n(1,0,0);
        cout << "theta = " << theta << endl;
        cout << "n不唯一, 任意皆满足, 这里给n = \n" << n << endl;
        Phi = theta * n;
        return;
    }
    else if(fabs(cos_theta + 1.0) < 0.000001) //==-1
    {
        cout << "Situation: theta = PI\n";
        //theta = PI, R = -I + 2 * n*nT, 这时候n和-n都满足，因为是旋转半圈，正负方向都对
        //根据Rn = n, 求得的不是n就是-n
        //根据 Rn = n 求n;
        //n为R特征值1对应的特征向量
        Eigen::EigenSolver<Eigen::Matrix3d> es(R);
        //特征值
        Eigen::Vector3d eigen_values = es.pseudoEigenvalueMatrix().diagonal();
        // cout << "eigen_values = \n" << eigen_values << endl;
        //特征向量
        Eigen::Matrix3d eigen_vectors = es.pseudoEigenvectors();
        // cout << "eigen_vectors = \n" << eigen_vectors << endl;
        //特征值里等于1的位置设置为0,其他元素都大于0
        Eigen::Vector3d is_one = (eigen_values - Eigen::Vector3d::Ones()).cwiseAbs();
        // cout << "is_one = \n" << is_one << endl;
        //第k个特征值为1
        Eigen::Vector3d::Index k;
        is_one.minCoeff(&k);
        // cout << "k = " << k << endl;
        //n 就是第k列特征向量
        Eigen::Vector3d n = eigen_vectors.block<3,1>(0,k);
        n = -n;  //todo 这里要不要都可以，因为当theta=PI时，n和-n都是对的旋转轴
        cout << "theta = " << theta << endl;
        cout << "此时正负n皆可 n = \n" << n << endl;
        n.normalize();
        Phi = theta * n;
        return;
    }
    else
    {
        //一般情况, 根据 n^ = (R - RT) / (2*sin(theta)) 求n
        Eigen::Matrix3d n_hat = (R - R.transpose()) / (2 * sin_theta);
        // cout << "n_hat = \n" << n_hat << endl;
        Eigen::Vector3d n(-n_hat(1,2), n_hat(0,2), -n_hat(0,1));
        cout << "theta = " << theta << endl;
        cout << "n = \n" << n << endl;
        Phi = theta * n;
    }
}
/// @brief 罗德里格斯公式，指数映射，R = cos(theta)I + (1-cos(theta))nnT + sin(theta)n^
/// @param Phi 
/// @param R 
void Lie_group_algebra::Phi_2_R(const Eigen::Vector3d& Phi, Eigen::Matrix3d& R)
{
    double theta = Phi.norm();
    Eigen::Vector3d n = Phi.normalized();
    //R = cos(theta)I + (1-cos(theta))nnT + sin(theta)n^
    R.setZero();
    R(1,2) = -n(0); R(0,2) = n(1); R(0,1) = -n(2);
    R(2,1) = n(0); R(2,0) = -n(1); R(1,0) = n(2);
    //sin(theta)n^
    R = R * sin(theta);
    R = R + cos(theta) * Eigen::Matrix3d::Identity() + (1.0 - cos(theta)) * n * n.transpose();
}

/// @brief SE3 到 se3 的对数映射， T 到 epsilon
/// @param T 
/// @param epsilon 
void Lie_group_algebra::T_2_epsilon(const Eigen::Isometry3d& T, Eigen::Matrix<double, 6, 1>& epsilon)
{
    //epsilon = (p, Phi)
    //t = Jp  所以 p = J.inverse() * t
    //J = sin(theta)/theta * I + (1 - sin(theta)/theta) nnT + (1-cos(theta))/theta * n^
    Eigen::Matrix3d R = T.rotation();
    Eigen::Vector3d t = T.translation();
    Eigen::Vector3d Phi;
    R_2_Phi(R, Phi);
    double theta = Phi.norm();
    //这里要注意 theta=0 的情况
    if(fabs(theta) < 0.000001) //theta == 0
    {
        //!!!怎么计算p
        //根据Sophus库来开，theta=0时候,t=p
        Eigen::Vector3d p = t;
        epsilon.block<3,1>(0,0) = p;
        epsilon.block<3,1>(3,0) = Phi;
        return;
    }
    Eigen::Vector3d n = Phi.normalized();
    Eigen::Matrix3d J = Eigen::Matrix3d::Zero();
    J(1,2) = -n(0); J(0,2) = n(1); J(0,1) = -n(2);
    J(2,1) = n(0); J(2,0) = -n(1); J(1,0) = n(2);
    J = J *(1.0 - cos(theta)) / theta;
    J = J + Eigen::Matrix3d::Identity() * sin(theta) / theta + (1.0 - sin(theta)/theta) * n * n.transpose();
    Eigen::Vector3d p = J.inverse() * t;
    epsilon.block<3,1>(0,0) = p;
    epsilon.block<3,1>(3,0) = Phi;
}

/// @brief 指数映射se3到SE3
/// @param epsilon 
/// @param T 
void Lie_group_algebra::epsilon_2_T(const Eigen::Matrix<double,6,1>& epsilon, Eigen::Isometry3d& T)
{
    Eigen::Vector3d p = epsilon.block<3,1>(0,0);
    Eigen::Vector3d Phi = epsilon.block<3,1>(3,0);
    double theta = Phi.norm();
    Eigen::Vector3d n = Phi.normalized();
    Eigen::Matrix3d R;
    //罗德里格斯公式
    Phi_2_R(Phi, R);
    //t = Jp
    Eigen::Matrix3d J = Eigen::Matrix3d::Zero();
    //!同样，当theta = 0时， 怎么求J, 进而求t = Jp
    if(fabs(theta) < 0.000001) //theta == 0
    {
        //theta = 0时, 怎么求J, t=Jp
        //根据Sophus库来开，theta=0时候,t=p
        Eigen::Vector3d t = p;
        T = Eigen::Isometry3d::Identity();
        T.rotate(R);
        T.pretranslate(t);
        return;
    }
    J(1,2) = -n(0); J(0,2) = n(1); J(0,1) = -n(2);
    J(2,1) = n(0); J(2,0) = -n(1); J(1,0) = n(2);
    J = J *(1.0 - cos(theta)) / theta;
    J = J + Eigen::Matrix3d::Identity() * sin(theta) / theta + (1.0 - sin(theta)/theta) * n * n.transpose();
    Eigen::Vector3d t = J * p;
    T = Eigen::Isometry3d::Identity();
    T.rotate(R);
    T.pretranslate(t);
}


```


## 参考文献
- https://zhuanlan.zhihu.com/p/358455662
- https://blog.csdn.net/qq_41253960/article/details/129940463?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ECtr-1-129940463-blog-79644920.235%5Ev43%5Epc_blog_bottom_relevance_base4&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ECtr-1-129940463-blog-79644920.235%5Ev43%5Epc_blog_bottom_relevance_base4&utm_relevant_index=1
- https://blog.csdn.net/weixin_45929038/article/details/122851697