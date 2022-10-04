# 优化函数

## 参考文章
[深度学习-优化器算法Optimizer详解](https://www.cnblogs.com/guoyaohua/p/8542554.html)  
[深度学习各种优化函数详解](https://blog.csdn.net/qq_21460525/article/details/70146665)

## 目录
#### 一、优化算法
[1. Batch Gradient Descent（BGD）](#1batch-gradient-descent（bgd）)  
[2. Stochastic Gradient Descent（SGD）](#2stochastic-gradient-descent（sgd）)  
[3. Mini-Batch Gradient Descent（MBGD）](#3mini-batch-gradient-descent（mbgd）)  
[4. Momentum](#4momentum)  
[5. Nesterov Accelerated Gradient](#5nesterov-accelerated-gradient)  
[6. Adagrad（Adaptive Gradient Algorithm）](#6adagrad（adaptive-gradient-algorithm）)  
[7. Adadelta](#7adadelta)  
[8. RMSprop](#8rmsprop)  
[9. Adam：Adaptive Moment Estimation](#9adam：adaotive-moment-estimation)

#### 二、优化函数的选择

[如何选择优化算法](#如何选择优化算法)




> 前三种算法：BGD、SGD、MBGD，它们是梯度下降最常见的三种变形，主要区别就是使用多少数据来计算目标函数的梯度。这里就需要在参数更新准确率和运行时间之间进行权衡。

# 一、优化算法

## 1.Batch Gradient Descent（BGD）

BGD采用整个训练集的数据来计算cost function对参数的梯度：

$$\theta=\theta-\eta \cdot \nabla_{\theta} J(\theta)$$

缺点：

由于需要使用整个数据集计算梯度，所以比较耗时，并且无法投入新数据实时更新模型


## 2.Stochastic Gradient Descent（SGD）

SGD会对每个样本进行梯度更新

$$\theta=\theta-\eta \cdot \nabla_{\theta} J\left(\theta ; x^{(i)} ; y^{(i)}\right)$$

缺点：

1. SGD的噪声比BGD要多，造成SGD并不是每次迭代都向着整体最优的方向，虽然训练速度快，但准确度下降。
2. SGD更新比较频繁，会造成cost function有严重的震荡

## 3.Mini-Batch Gradient Descent（MBGD）

MBGD每次利用一小批样本进行计算，这可以降低参数计算更新时的方差，收敛更稳定，另一方面可以充分利用深度学习库中高度优化的矩阵操作来进行更有效的梯度计算。

$$\theta=\theta-\eta \cdot \nabla_{\theta} J\left(\theta ; x^{(i;i+n)} ; y^{(i;i+n)}\right)$$

n的取值一般在50-256

缺点：

1. MBGD不能保证很好的收敛性，Learning rate如果太小， 收敛速度慢，如果太大，loss function就会在极值处不停震荡甚至偏离。【有一种措施是先设定大一点的学习率，当两次迭代之间的变化低于某个阈值后，就减少learning rate】


## 4.Momentum

SGD在ravines（曲面的一个方向比另一个方向更抖）的情况下容易被困住，此时SGD会发生震荡而迟迟不能接近极小值

$$v_{t}=\gamma v_{t-1}+\eta \nabla_{\theta} J(\theta)$$
$$\theta=\theta-v_{t}$$

Momentum通过加入 $\gamma v_{t-1}$ （一般$\gamma$取值0.9左右），可以加速SGD，并且抑制震荡。这一项可以使梯度方向不变的维度上速度变快，梯度方向有所改变的维度上的更新速度变慢，这样可以加快收敛并减小震荡。


## 5.Nesterov Accelerated Gradient

$$v_{t}=\gamma v_{t-1}+\eta \nabla_{\theta} J(\theta-\gamma v_{t-1})$$
$$\theta=\theta-v_{t}$$

用 $\theta-\gamma v_{t-1})$ （$\gamma$取值0.9左右） 来近似当作参数下一步回变成的值，也就是说，在计算梯度时，不是在当前位置，而是未来的位置上。

NAG可以使RNN在很多任务上有更好的表现。


## 6.Adagrad（Adaptive Gradient Algorithm）

这个算法可以对低频参数左较大的更新，对高频的做较小的更新，因此，对稀疏的数据它能够表现得很好，提高了SGD得鲁棒性。

$$\theta_{t+1, i}=\theta_{t, i}-\frac{\eta}{\sqrt{G_{t, i i}+\epsilon}} \cdot g_{t, i}$$

其中 $g$ 为：$t$ 时刻参数 $\theta_i$得梯度：

$$g_{t, i}=\nabla_{\theta} J\left(\theta_{i}\right)$$

其中 $G_t$ 是对角矩阵， $(i,i)$ 元素就是 $t$ 时刻参数 $\theta_i$ 的梯度平方和。Adagrad能够减少学习率的手动调节，$\epsilon$ 一般取0.01

缺点：

1. 分母会不断积累，这样学习率就会收缩并最终会变得非常小。


## 7.Adadelta

这个算法是对Adagrad的改进。

$$\Delta \theta_{t}=-\frac{\eta}{\sqrt{E\left[g^{2}\right]_{t}+\epsilon}} g_{t}$$

与Adagrad相比，分母的G换成了过去的梯度平方的衰减平均值（指数衰减平均值）

分母相当于梯度的均方根root mean squared（RMS），使用RMS进行简化：

$$\Delta \theta_{t}=-\frac{\eta}{RMS[g]_t} g_{t}$$

E的计算公式如下，t时刻依赖于前一时刻的平均值和当前时刻的梯度

$$E[g^2]_t = \gamma E[g^2]_{t-1} + (1-\gamma)g^2_t$$

此外，若将学习率 $\eta$ 换成了 $RMS[\Delta \theta]$，这样的话，就不需要提前设定学习率（超参数 $\gamma$ 一般为0.9）：

$$\Delta \theta_{t} = -\frac{R M S[\Delta \theta]_{t-1}}{R M S[g]_{t}} g_{t}$$

$$\theta_{t+1} = \theta_{t}+\Delta \theta_{t}$$

## 8.RMSprop

RMSprop和Adadelta都是为了解决Adagrad学习率急剧下降的问题

$$E\left[g^{2}\right]_{t}=0.9 E\left[g^{2}\right]_{t-1}+0.1 g_{t}^{2} $$
$$\theta_{t+1}=\theta_{t}-\frac{\eta}{\sqrt{E\left[g^{2}\right]_{t}+\epsilon}} g_{t}$$

RMSprop与Adadelta的第一种形式相同（$\gamma$为0.9，$\eta$ 为0.001）

## 9.Adam：Adaotive Moment Estimation  

该算法是另一种计算每个参数的自适应学习率的方法。相当于RMSprop + Momentum。除了像Adadelta和RMSprop一样存储了过去梯度的平方 $v_t$ 的指数衰减平均值，也像momentum一样保持了过去梯度 $m_t$ 的指数衰减平均值

$$m_t = \beta_1m_{t-1} + (1-\beta_1)g_t$$
$$v_t = \beta_2v_{t-1} + (1-\beta_2)g_t^2$$

如果 $m_t$ 和 $v_t$ 被初始化为0向量，那它们就会向0偏置，所以做了偏差校正，通过计算偏差校正后的 $m_t$ 和 $v_t$来抵消这些偏差：

$$\hat{m}_t = \frac{m_t}{1-\beta_1^t}$$
$$\hat{v}_t = \frac{v_t}{1-\beta_2^t}$$

梯度更新规则：

$$\theta_{t+1} = \theta_t - \frac{\eta}{\sqrt{\hat{v}_t}+\epsilon}\hat{m}_t$$

超参数设定：$\beta_1 = 0.9,\beta_2 = 0.999, \epsilon = 10e-8$

实践证明，Adam比其他适应性学习方法效果要好


# 如何选择优化算法

1. 如果数据是稀疏的，就用自适应方法，即Adagrad、Adadelta、RMSprop、Adam
2. 综合来说，Adam是最好的选择
