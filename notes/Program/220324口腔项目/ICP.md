# 三维点云配准 ICP算法

参考文章

[三维点云配准 -- ICP 算法原理及推导](https://zhuanlan.zhihu.com/p/104735380)

## 1 问题描述

点云配准（Point Cloud Registration）指的是输入两个点云 $P^s$ 和 $P^t$ ，输出一个变换 $T$ ，使得 $T·P^s$ 和 $P^t$ 的重合程度尽可能高。

点云配准可以分为粗配准（Coarse Registration）和精配准（Fine Registration）两步。

目前应用最广泛的点云配准算法是迭代最近点算法（Iterative Closest Point，ICP）及各种变种算法。

## 2 算法描述

对于 $T$ 是刚性变换的情形，点云配准问题可以描述为：

$$R^{*}, t^{*}=\underset{R, t}{\arg \min } \frac{1}{\left|P^{s}\right|} \sum_{i=1}^{\left|P^{s}\right|}\left\|p_{t}^{i}-\left(R \cdot p_{s}^{i}+t\right)\right\|^{2}$$

这里 $p_s$ 和 $p_t$ 是源点云和目标点云中的对应点

ICP算法的目标是让配准后两个点云对应点的距离和最小，ICP算法将离某点的欧氏距离最近的点作为对应点，有了点的对应关系，我们就可以通过奇异值分解（最小二乘法Least Squares）求 $R$ 和 $t$

ICP算法的一般流程：
1. 点云预处理：滤波、清理数据等
2. 匹配：找最近点与点的对应关系
3. 加权：调整一些点对的权重
4. 剔除不合理的对应点对
5. 求解最优变换
6. 计算loss
7. 判断是否收敛，未收敛则回到步骤二进行迭代

整体看ICP算法有两个关键点：找最近点和找最优变换

### 2.1 找最近对应点

首先利用上一次迭代得到的 $R_{k-1}, t_{k-1}$ 对初始点云 $P_0^s$ 进行变换，得到上一次迭代的 $P_{k-1}^s$，将 $P_{k-1}^s$ 和 $P_0^t$ 进行比较，找到 $P_{k-1}^s$ 中每一个点在 $P_0^t$ 中的最近邻点。

### 2.2 求解最优变换

对于 point-to-point ICP问题，求解最优变换是有闭形式解（closed-form solution）的，可以借助SVD分解来计算。

结论：

在已知点的对应关系的情况下，设 $ \bar{p}_s, \bar{p}_{t}$ 分别表示源点云和目标点云的质心，令 $\hat{p}_{s}^{i}=p_{s}^{i}-\bar{p}_{s}$ ，$\hat{p}_{t}^{i}=p_{s}^{i}-\bar{p}_{t}$ ，令 $H=\sum_{i=1}^{|P^{s}|} \hat{p}_s^i \hat{p}_{t}^{i}$ ，它是一个3×3的矩阵，对 $H$ 进行SVD分解得到 $H=U \Sigma V^{T}$ ，则point-to-point ICP问题最优旋转为：
$$R^* = VU^T$$
最优平移为：
$$t^* = \bar{p}_t - R*\bar{p}_s$$

两部分的推到过程也可以看[参考连接](https://zhuanlan.zhihu.com/p/104735380)

[参考代码](https://github.com/ClayFlannigan/icp)