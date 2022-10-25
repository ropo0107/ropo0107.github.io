---
title:  【robot】 kalman filter
categories: [工程]
tags:   [Robot]
copyright: false
reward: false
rating: false
related_posts: false
date: 2022-10-24 23:34:55
---

# 核心
卡尔曼滤波：上一时刻最优估计值预测当前值，同时加入观测值修正当前值得到最优估计。


# 本质分析
- 系统动力学模型
![](/images/posts/robot/kalman_filter/dynamics.png)

    <!-- $$\left\{\begin{matrix}
    x_k= Ax_{k-1} + Bu_k + w_k
    \\ 
    y_k= Cx_k + v_k
    \end{matrix}\right.
    $$ -->

    $u_k$ 和 $v_k$ 为实际测量系统中产生的误差

- 先验估计 $\hat{x}^-$

    先验估计包括两部分：状态估计值和协方差
    $$\hat{x}^-_k = A\hat{x}_{k-1} + Bu_k$$

    $$P^-_k = AP_{k-1}A^T + Q$$

    - $P$ 为状态之间的协方差矩阵，表述预测值不确定性的度量（噪声），当只有一个状态时协方差即为方差

- 更新
    - 卡尔曼增益：
        $$ K_k= \frac{P_k^-C^T}{CP_k^-C^T + R}$$

        - 卡尔曼增益的目的使得最有估计值的方差最小，相当于一个权重

            ![](/images/posts/robot/kalman_filter/weight.png)
        - 当观测没有噪声 $R=0$ 时, $K_k = C^{-1}$, $\hat{x}_k = y_k$
        - 当状态估计没有噪声，即所有状态之间不相关 $P_k=0$ 时, $K_k = C^{-1}$, $\hat{x}_k = \hat{x}_k^-$

    - 协方差矩阵更新：
        $$ P_k= (I-K_kC)P_k^-$$
    - 状态更新

        $$\hat{x}_k = \hat{x}^-_k + K_k(y_k-C\hat{x}^-_k)$$

        ![](/images/posts/robot/kalman_filter/kalman_filter.png)

        后验估计 = 先验估计 + 更新状态

# 案例
一个简单的Matlab案例

不同维度的测试结果

![](/images/posts/robot/kalman_filter/example.png)


```Matlab

% GPS精度为1m、气压计精度为0.5m，加速度计的精度为 1cm/s^2
% 无人机按照螺旋线飞行，半径为 20m，螺距为40m，100s完成一圈飞行
% 数据采集频率为 10Hz

clear all

D = 1;                          % 维度，可取 1,2,3

dt = 0.1;                       % 0.1s采集一次数据
t0 = 0:dt:100;                  % 0~100s
N = length(t0);                 % 采样点数

A = eye(D);                     % 状态转移矩阵，和上一时刻状态没有换算，故取 D阶单位矩阵
x = zeros(D, N);                % 存储滤波后的数据
z = ones(D, N);                 % 存储滤波前的数据
x(:, 1) = ones(D,1);            % 初始值设为 1（可为任意数）
P = eye(D);                     % 初始值为 1（可为非零任意数），取 D阶单位矩阵
    
r = 20;                         % 绕圈半径，20m
w = 2*pi / 100;                 % 计算出角速度，100s绕一圈

Q = 1e-2*eye(D);                % 过程噪声协方差，估计一个

k = 1;                          % 采样点计数

if D==1                         % 一维仅高度，气压计数据
    true1D = t0*0.4;
    R = [1];                    % 测量噪声协方差，精度为多少取多少
elseif D==2                     % 二维 x,y 方向，GPS数据
    true2D = [r*cos(w*t0); r*sin(w*t0)];
    R = [1 0;
    0 0.5];                     % 测量噪声协方差，精度为多少取多少
elseif D==3                     % 三维 x,y,z方向，GPS和气压计
    true3D = [r * cos(w*t0); r * sin(w*t0); t0 * 0.4];
    R = [1 0 0;
    0 1 0;
    0 0 0.5];                   % 测量噪声协方差，精度为多少取多少
end

for t = dt:dt:100
    k = k + 1;                  
    x(:,k) = A * x(:,k-1);      % 卡尔曼公式1
    P = A * P * A' + Q;         % 卡尔曼公式2
    C = eye(D);
    K = P*C' * inv(C*P*C' + R); % 卡尔曼公式3
    if D==1                                     % 一维仅高度（z方向）
        z(:,k) = true1D(k) + randn;                 
    elseif D==2                                 % 二维 x,y 方向
        z(:,k) = [true2D(1,k) + randn; true2D(2,k) + randn];
    elseif D==3                                 % 三维 x,y,z 方向
        z(:,k) = [true3D(1,k) + randn; true3D(2,k) + randn; true3D(3,k) + randn];
    end
    x(:,k) = x(:,k) + K * (z(:,k)-C*x(:,k));    % 卡尔曼公式4
    P = (eye(D)-K*C) * P;                       % 卡尔曼公式5
end

if D==1                                         %% 一维情况
    plot(t0, z,'b.');                           % 绘制滤波前数据
    axis('equal');hold on;grid on;              % 坐标等距、继续绘图、添加网格
    plot(t0, x,'r.');                           % 绘制滤波后数据
    plot(t0, true1D ,'k-');                     % 绘制真实值
    legend('滤波前','滤波后','理想值');             % 标注
    xlabel('时间: s');                         
    ylabel('高度: m');hold off;  
elseif D==2                                     %% 二维情况
    plot(z(1,:),z(2,:),'b.');                   % 绘制滤波前数据
    axis('equal');grid on;hold on;              % 坐标等距、继续绘图、添加网格
    plot(x(1,:),x(2,:),'r.');                   % 绘制滤波后数据
    plot(true2D(1,:), true2D(2,:), 'k.');       % 绘制真实值
    legend('滤波前','滤波后','理想值'); 
    xlabel('x方向: m');
    ylabel('y方向: m');hold off;
elseif D==3                                     %% 三维情况
    plot3(z(1,:),z(2,:),z(3,:),'b.');           % 绘制滤波前数据
    axis('equal');grid on;hold on               % 坐标等距、继续绘图、添加网格
    plot3(x(1,:),x(2,:),x(3,:),'r.');           % 绘制滤波后数据
    plot3(true3D(1,:), true3D(2,:), true3D(3,:));% 绘制滤波后数据
    legend('滤波前','滤波后','理想值');           % 绘制真实值
    xlabel('x方向: m');
    ylabel('y方向: m');
    zlabel('高度: m');hold off;
end

```

# 参考链接：

- https://blog.csdn.net/weixin_41869763/article/details/104812479
- https://www.bilibili.com/video/BV1nR4y1u7mN?p=4&vd_source=e8cfb486e571f22f81defd80d9e13cfc