---
title: "机器学习习题集"
date: 2025-12-13T20:26:19+08:00
draft: false
categories: ["研究生课程"]
tags: ["机器学习", "数学推导", "算法"]
math: true
summary: "胡老师上的机器学习课程习题集解答汇总"
description: |
  本习题集包含16道经典机器学习问题的完整数学推导，
  从梯度下降、交叉熵损失、最大似然估计，
  到核方法、支持向量机对偶问题、信息增益比计算，
  再到 XGBoost 的二阶泰勒展开。
  每道题目都配有详细的解题步骤和数学证明，
  帮助读者深入理解机器学习算法的理论基础。
comments: true
---

## 第一题：线性回归梯度下降

**题目：** 对线性模型 $h_\theta(x) = \theta^\top x$，给定训练集 $\{(x^{(i)}, y^{(i)})\}$，推导其向量形式的最小二乘损失梯度下降更新公式为：

$$\theta := \theta + \alpha \sum_{i=1}^{n} (y^{(i)} - h_\theta(x^{(i)})) x^{(i)}$$

**解：**

最小二乘损失函数为：

$$J(\theta) = \frac{1}{2}\sum_{i=1}^{n}(h_\theta(x^{(i)}) - y^{(i)})^2 = \frac{1}{2}\sum_{i=1}^{n}(\theta^\top x^{(i)} - y^{(i)})^2$$

对 $\theta$ 求梯度：

$$\begin{aligned} \nabla_\theta J(\theta) &= \sum_{i=1}^{n}(\theta^\top x^{(i)} - y^{(i)}) \cdot x^{(i)} \\ &= \sum_{i=1}^{n}(h_\theta(x^{(i)}) - y^{(i)}) x^{(i)} \end{aligned}$$

梯度下降更新规则为 $\theta := \theta - \alpha \nabla_\theta J(\theta)$，因此：

$$\theta := \theta - \alpha \sum_{i=1}^{n}(h_\theta(x^{(i)}) - y^{(i)}) x^{(i)} = \theta + \alpha \sum_{i=1}^{n}(y^{(i)} - h_\theta(x^{(i)})) x^{(i)}$$

------

## 第二题：交叉熵损失梯度

**题目：** Cross Entropy Loss 定义如下：

$$l_{ce}((t_1,\ldots,t_k),y) = -\log\left(\frac{\exp(t_y)}{\sum_j \exp(t_j)}\right)$$

令向量 $t = (t_1,t_2,\ldots,t_k)$，推导 CEL 对任意 $t_i$ 求导为：

$$\frac{\partial l_{ce}(t,y)}{\partial t_i} = \phi_i - \mathbb{1}\{y=i\}$$

**解：**

记 $\phi_i = \frac{\exp(t_i)}{\sum_j \exp(t_j)}$ 为 softmax 函数。

首先简化损失函数：

$$l_{ce}(t,y) = -\log(\phi_y) = -t_y + \log\left(\sum_j \exp(t_j)\right)$$

对 $t_i$ 求导：

$$\begin{aligned} \frac{\partial l_{ce}(t,y)}{\partial t_i} &= -\frac{\partial t_y}{\partial t_i} + \frac{\partial}{\partial t_i}\log\left(\sum_j \exp(t_j)\right) \\ &= -\mathbb{1}\{y=i\} + \frac{\exp(t_i)}{\sum_j \exp(t_j)} \\ &= \phi_i - \mathbb{1}\{y=i\} \end{aligned}$$

其中 $\mathbb{1}\{y=i\}$ 是指示函数，当 $y=i$ 时为1，否则为0。

------

## 第三题：高斯假设下的最大似然估计

**题目：** 证明在高斯差异假定下，对线性模型 $h_\theta(x) = \theta^\top x$，最大化参数似然 $L(\theta)$ 等价于最小化二乘损失 $\sum_{i=1}^{n}(y^{(i)} - \theta^\top x^{(i)})^2$。

**解：**

假设误差 $\epsilon^{(i)} = y^{(i)} - \theta^\top x^{(i)}$ 服从独立同分布的高斯分布 $\mathcal{N}(0, \sigma^2)$，即：

$$p(\epsilon^{(i)}) = \frac{1}{\sqrt{2\pi}\sigma}\exp\left(-\frac{(\epsilon^{(i)})^2}{2\sigma^2}\right)$$

因此：

$$p(y^{(i)} | x^{(i)}; \theta) = \frac{1}{\sqrt{2\pi}\sigma}\exp\left(-\frac{(y^{(i)} - \theta^\top x^{(i)})^2}{2\sigma^2}\right)$$

似然函数为：

$$\begin{aligned} L(\theta) &= \prod_{i=1}^{n} p(y^{(i)} | x^{(i)}; \theta) \\ &= \prod_{i=1}^{n} \frac{1}{\sqrt{2\pi}\sigma}\exp\left(-\frac{(y^{(i)} - \theta^\top x^{(i)})^2}{2\sigma^2}\right) \end{aligned}$$

对数似然为：

$$\begin{aligned} \log L(\theta) &= \sum_{i=1}^{n}\left[\log\frac{1}{\sqrt{2\pi}\sigma} - \frac{(y^{(i)} - \theta^\top x^{(i)})^2}{2\sigma^2}\right] \\ &= n\log\frac{1}{\sqrt{2\pi}\sigma} - \frac{1}{2\sigma^2}\sum_{i=1}^{n}(y^{(i)} - \theta^\top x^{(i)})^2 \end{aligned}$$

最大化 $\log L(\theta)$ 等价于最小化 $\sum_{i=1}^{n}(y^{(i)} - \theta^\top x^{(i)})^2$。

------

## 第四题：Logistic回归的NLL损失

**题目：** 对Logistic回归模型 $h_\theta(x) = g(\theta^\top x) = \frac{1}{1+e^{-\theta^\top x}}$，推导其在单样本 $(x,y)$ 下的NLL（negative log likelihood）损失，以及损失对特定参数 $\theta_j$ 的导数为 $(h_\theta(x) - y)x_j$。

提示：Logistic回归预测概率的统一形式为 $P(y|x;\theta) = (h_\theta(x))^y(1-h_\theta(x))^{1-y}$

**解：**

根据提示，Logistic回归的概率模型为：

$$P(y|x;\theta) = (h_\theta(x))^y(1-h_\theta(x))^{1-y}$$

其中 $y \in \{0,1\}$，$h_\theta(x) = g(\theta^\top x) = \frac{1}{1+e^{-\theta^\top x}}$。

对数似然为：

$$\log P(y|x;\theta) = y\log(h_\theta(x)) + (1-y)\log(1-h_\theta(x))$$

NLL损失为：

$$\text{NLL}(x,y;\theta) = -\log P(y|x;\theta) = -y\log(h_\theta(x)) - (1-y)\log(1-h_\theta(x))$$

对 $\theta_j$ 求导。首先注意到：

$$\frac{\partial h_\theta(x)}{\partial \theta_j} = h_\theta(x)(1-h_\theta(x)) \cdot x_j$$

这是因为 $g'(z) = g(z)(1-g(z))$。

因此：

$$\begin{aligned} \frac{\partial \text{NLL}}{\partial \theta_j} &= -y\frac{1}{h_\theta(x)}\frac{\partial h_\theta(x)}{\partial \theta_j} - (1-y)\frac{1}{1-h_\theta(x)}\left(-\frac{\partial h_\theta(x)}{\partial \theta_j}\right) \\ &= -y\frac{1}{h_\theta(x)} \cdot h_\theta(x)(1-h_\theta(x))x_j + (1-y)\frac{1}{1-h_\theta(x)} \cdot h_\theta(x)(1-h_\theta(x))x_j \\ &= -y(1-h_\theta(x))x_j + (1-y)h_\theta(x)x_j \\ &= (h_\theta(x) - y)x_j \end{aligned}$$

------

## 第五题：Poisson分布的指数族形式

**题目：** 已知指数分布族定义如下：$p(y;\eta) = b(y)\exp(\eta^\top y - a(\eta))$。推导Poisson分布的指数分布族形式，并构建Poisson分布对应的广义线性模型。其中，Poisson分布 $\text{Pois}(\lambda)$ 的概率密度函数如下：

$$P(X=k) = \frac{\lambda^k e^{-\lambda}}{k!}$$

**解：**

将Poisson分布改写为指数族形式：

$$\begin{aligned} P(X=k) &= \frac{\lambda^k e^{-\lambda}}{k!} \\ &= \frac{1}{k!}\exp(k\log\lambda - \lambda) \\ &= \frac{1}{k!}\exp(\eta \cdot k - e^\eta) \end{aligned}$$

其中 $\eta = \log\lambda$（自然参数），因此 $\lambda = e^\eta$。

对应指数族形式：

- $b(y) = \frac{1}{y!}$
- $\eta = \log\lambda$
- $a(\eta) = e^\eta = \lambda$
- $y$ 的充分统计量就是 $y$ 本身

构建广义线性模型：

1. 假设 $y|x;\theta \sim \text{Pois}(\lambda)$
2. 自然参数 $\eta = \theta^\top x$
3. 因为 $\lambda = e^\eta$，所以 $\lambda = e^{\theta^\top x}$
4. 响应函数（期望）为：$h_\theta(x) = \mathbb{E}[y|x;\theta] = \lambda = e^{\theta^\top x}$

这就是Poisson回归模型。

------

## 第六题：Shapley值计算

**题目：** 计算以下3人团队的Shapley值 $\phi_1$、$\phi_2$、$\phi_3$。

给定：

- $C_{123} = 10000$，$C_0 = 0$
- $C_{12} = 7500$，$C_{13} = 7500$，$C_{23} = 5000$
- $C_1 = 5000$，$C_2 = 5000$，$C_3 = 0$

**解：**

Shapley值的公式为：

$$\phi_i = \sum_{S \subseteq N \setminus \{i\}} \frac{|S|!(|N|-|S|-1)!}{|N|!}[C(S \cup \{i\}) - C(S)]$$

对于3人团队，$|N| = 3$，计算每个玩家的边际贡献：

**玩家1的Shapley值：**

$$\begin{aligned} \phi_1 &= \frac{0!2!}{3!}[C_1 - C_0] + \frac{1!1!}{3!}[C_{12} - C_2] + \frac{1!1!}{3!}[C_{13} - C_3] + \frac{2!0!}{3!}[C_{123} - C_{23}] \\ &= \frac{1}{3}[5000 - 0] + \frac{1}{6}[7500 - 5000] + \frac{1}{6}[7500 - 0] + \frac{1}{3}[10000 - 5000] \\ &= \frac{5000}{3} + \frac{2500}{6} + \frac{7500}{6} + \frac{5000}{3} \\ &= \frac{10000}{3} + \frac{10000}{6} = \frac{20000 + 10000}{6} = 5000 \end{aligned}$$

**玩家2的Shapley值：**

$$\begin{aligned} \phi_2 &= \frac{0!2!}{3!}[C_2 - C_0] + \frac{1!1!}{3!}[C_{12} - C_1] + \frac{1!1!}{3!}[C_{23} - C_3] + \frac{2!0!}{3!}[C_{123} - C_{13}] \\ &= \frac{1}{3}[5000 - 0] + \frac{1}{6}[7500 - 5000] + \frac{1}{6}[5000 - 0] + \frac{1}{3}[10000 - 7500] \\ &= \frac{5000}{3} + \frac{2500}{6} + \frac{5000}{6} + \frac{2500}{3} \\ &= \frac{15000 + 2500 + 5000 + 5000}{6} = \frac{27500}{6} \approx 4583.33 \end{aligned}$$

**玩家3的Shapley值：**

由对称性或直接计算：

$$\phi_3 = 10000 - \phi_1 - \phi_2 = 10000 - 5000 - 4583.33 = 416.67$$

或直接计算：

$$\begin{aligned} \phi_3 &= \frac{1}{3}[0] + \frac{1}{6}[7500 - 5000] + \frac{1}{6}[5000 - 5000] + \frac{1}{3}[10000 - 7500] \\ &= 0 + \frac{2500}{6} + 0 + \frac{2500}{3} = \frac{5000}{6} \approx 416.67 \end{aligned}$$

**答案：** $\phi_1 = 5000$，$\phi_2 \approx 4583.33$，$\phi_3 \approx 416.67$

------

## 第七题：协方差矩阵性质

**题目：** 基于协方差矩阵定义 $\Sigma = \text{Cov}(X)$ 证明：

1. $\Sigma$ 为对称矩阵；
2. $\Sigma$ 半正定，记 $\Sigma \geq 0$，即对任意向量 $z \in \mathbb{R}^d$ 有 $z^\top \Sigma z \geq 0$。

**解：**

设 $X \in \mathbb{R}^d$ 为随机向量，$\mu = \mathbb{E}[X]$，则：

$$\Sigma = \text{Cov}(X) = \mathbb{E}[(X-\mu)(X-\mu)^\top]$$

**(1) 证明 $\Sigma$ 为对称矩阵：**

$$\Sigma^\top = \mathbb{E}[(X-\mu)(X-\mu)^\top]^\top = \mathbb{E}[((X-\mu)(X-\mu)^\top)^\top] = \mathbb{E}[(X-\mu)(X-\mu)^\top] = \Sigma$$

因此 $\Sigma$ 是对称矩阵。

**(2) 证明 $\Sigma$ 半正定：**

对任意 $z \in \mathbb{R}^d$：

$$\begin{aligned} z^\top \Sigma z &= z^\top \mathbb{E}[(X-\mu)(X-\mu)^\top] z \\ &= \mathbb{E}[z^\top(X-\mu)(X-\mu)^\top z] \\ &= \mathbb{E}[(z^\top(X-\mu))^2] \\ &\geq 0 \end{aligned}$$

最后一步是因为期望中的项是平方项，必然非负。因此 $\Sigma$ 半正定。

------

## 第八题：高斯判别分析的MLE

**题目：** 对高斯判别分析，已知各变量概率分布为：

$$\begin{aligned} p(y) &= \phi^y(1-\phi)^{1-y} \\ p(x|y=0) &= \frac{1}{(2\pi)^{d/2}|\Sigma|^{1/2}}\exp\left(-\frac{1}{2}(x-\mu_0)^\top\Sigma^{-1}(x-\mu_0)\right) \\ p(x|y=1) &= \frac{1}{(2\pi)^{d/2}|\Sigma|^{1/2}}\exp\left(-\frac{1}{2}(x-\mu_1)^\top\Sigma^{-1}(x-\mu_1)\right) \end{aligned}$$

证明在极大似然估计下，参数 $\phi$、$\mu_0$、$\mu_1$ 的形式为：

$$\begin{aligned} \phi &= \frac{1}{n}\sum_{i=1}^{n}\mathbb{1}\{y^{(i)}=1\} \\ \mu_0 &= \frac{\sum_{i=1}^{n}\mathbb{1}\{y^{(i)}=0\}x^{(i)}}{\sum_{i=1}^{n}\mathbb{1}\{y^{(i)}=0\}} \\ \mu_1 &= \frac{\sum_{i=1}^{n}\mathbb{1}\{y^{(i)}=1\}x^{(i)}}{\sum_{i=1}^{n}\mathbb{1}\{y^{(i)}=1\}} \end{aligned}$$

**解：**

对数似然函数为：

$$\log L = \sum_{i=1}^{n}\left[\log p(y^{(i)}) + \log p(x^{(i)}|y^{(i)})\right]$$

**估计 $\phi$：**

$$\log L_\phi = \sum_{i=1}^{n}\log p(y^{(i)}) = \sum_{i=1}^{n}[y^{(i)}\log\phi + (1-y^{(i)})\log(1-\phi)]$$

令 $\frac{\partial \log L_\phi}{\partial \phi} = 0$：

$$\sum_{i=1}^{n}\left[\frac{y^{(i)}}{\phi} - \frac{1-y^{(i)}}{1-\phi}\right] = 0$$

解得：

$$\phi = \frac{1}{n}\sum_{i=1}^{n}y^{(i)} = \frac{1}{n}\sum_{i=1}^{n}\mathbb{1}\{y^{(i)}=1\}$$

**估计 $\mu_0$：**

只考虑 $y=0$ 的样本：

$$\log L_{\mu_0} = \sum_{i:y^{(i)}=0}\left[-\frac{1}{2}(x^{(i)}-\mu_0)^\top\Sigma^{-1}(x^{(i)}-\mu_0) + \text{const}\right]$$

令 $\frac{\partial \log L_{\mu_0}}{\partial \mu_0} = 0$：

$$\sum_{i:y^{(i)}=0}\Sigma^{-1}(x^{(i)}-\mu_0) = 0$$

解得：

$$\mu_0 = \frac{\sum_{i:y^{(i)}=0}x^{(i)}}{\sum_{i:y^{(i)}=0}1} = \frac{\sum_{i=1}^{n}\mathbb{1}\{y^{(i)}=0\}x^{(i)}}{\sum_{i=1}^{n}\mathbb{1}\{y^{(i)}=0\}}$$

同理可得 $\mu_1$ 的估计。

------

## 第九题：GDA可转化为Logistic回归

**题目：** 证明GDA可转化为Logistic回归。提示：

1. $p(y=1|x) = \frac{p(x|y=1)p(y=1)}{p(x|y=1)p(y=1) + p(x|y=0)p(y=0)}$
2. 可记 $r(x) = \frac{p(x|y=1)p(y=1)}{p(x|y=0)p(y=0)}$
3. 给出 $p(x|y=0)$, $p(x|y=1)$, $p(y=1)$ 的表达式

**解：**

根据贝叶斯定理：

$$p(y=1|x) = \frac{p(x|y=1)p(y=1)}{p(x|y=1)p(y=1) + p(x|y=0)p(y=0)} = \frac{1}{1 + \frac{p(x|y=0)p(y=0)}{p(x|y=1)p(y=1)}} = \frac{1}{1 + \frac{1}{r(x)}}$$

其中：

$$r(x) = \frac{p(x|y=1)p(y=1)}{p(x|y=0)p(y=0)}$$

计算 $\log r(x)$：

$$\begin{aligned} \log r(x) &= \log p(x|y=1) + \log p(y=1) - \log p(x|y=0) - \log p(y=0) \\ &= -\frac{1}{2}(x-\mu_1)^\top\Sigma^{-1}(x-\mu_1) + \frac{1}{2}(x-\mu_0)^\top\Sigma^{-1}(x-\mu_0) + \log\frac{\phi}{1-\phi} \end{aligned}$$

展开：

$$\begin{aligned} \log r(x) &= -\frac{1}{2}x^\top\Sigma^{-1}x + x^\top\Sigma^{-1}\mu_1 - \frac{1}{2}\mu_1^\top\Sigma^{-1}\mu_1 \\ &\quad + \frac{1}{2}x^\top\Sigma^{-1}x - x^\top\Sigma^{-1}\mu_0 + \frac{1}{2}\mu_0^\top\Sigma^{-1}\mu_0 + \log\frac{\phi}{1-\phi} \\ &= x^\top\Sigma^{-1}(\mu_1 - \mu_0) + \frac{1}{2}(\mu_0^\top\Sigma^{-1}\mu_0 - \mu_1^\top\Sigma^{-1}\mu_1) + \log\frac{\phi}{1-\phi} \\ &= \theta^\top x + \theta_0 \end{aligned}$$

其中：

$$\theta = \Sigma^{-1}(\mu_1 - \mu_0), \quad \theta_0 = \frac{1}{2}(\mu_0^\top\Sigma^{-1}\mu_0 - \mu_1^\top\Sigma^{-1}\mu_1) + \log\frac{\phi}{1-\phi}$$

因此：

$$p(y=1|x) = \frac{1}{1+e^{-\theta^\top x - \theta_0}} = \frac{1}{1+e^{-\tilde{\theta}^\top \tilde{x}}}$$

这正是Logistic回归的形式（其中 $\tilde{x}$ 包含截距项）。

------

## 第十题：Kernel Method分析

**题目：** Kernel method中，若Kernel function $K(x,z) = (x^\top z + c)^2$，推导对应的feature mapping $\phi$，并讨论对于 $n$ 个样本一轮SGD，使用Kernel method和在feature map上的计算效率优化比。

提示：

1. 基于feature map的参数更新方法为：$\theta := \theta + \alpha\sum_{i=1}^{n}(y^{(i)} - \theta^\top\phi(x^{(i)}))\phi(x^{(i)})$
2. Kernel method的参数更新方法为：$\theta := \theta + \alpha(\tilde{y} - K\theta)$，其中 $K_j = K(x^{(i)}, x^{(j)})$

**解：**

**推导feature mapping：**

对于 $x,z \in \mathbb{R}^d$，展开核函数：

$$\begin{aligned} K(x,z) &= (x^\top z + c)^2 \\ &= (x_1z_1 + x_2z_2 + \cdots + x_dz_d + c)^2 \\ &= \sum_{i=1}^{d}x_i^2z_i^2 + \sum_{i \neq j}2x_ix_jz_iz_j + 2c\sum_{i=1}^{d}x_iz_i + c^2 \end{aligned}$$

因此，feature mapping为：

$$\phi(x) = (x_1^2, x_2^2, \ldots, x_d^2, \sqrt{2}x_1x_2, \sqrt{2}x_1x_3, \ldots, \sqrt{2}x_{d-1}x_d, \sqrt{2c}x_1, \ldots, \sqrt{2c}x_d, c)$$

维度为：$d + \binom{d}{2} + d + 1 = d + \frac{d(d-1)}{2} + d + 1 = \frac{d(d+3)}{2} + 1 = O(d^2)$

**计算效率比较：**

- **Feature map方法：**
  - 计算 $\phi(x^{(i)})$：$O(d^2)$ 每个样本
  - 内积 $\theta^\top\phi(x^{(i)})$：$O(d^2)$
  - 更新 $\theta$：$O(d^2)$
  - 总计：$O(nd^2)$ 每轮SGD
- **Kernel method：**
  - 计算核矩阵 $K$：$O(n^2d)$（一次性预计算）
  - 更新参数：$O(n^2)$（矩阵向量乘法）
  - 总计：$O(n^2d + n^2) = O(n^2d)$ 每轮

**效率比：**

$$\frac{\text{Feature map}}{\text{Kernel method}} = \frac{O(nd^2)}{O(n^2d)} = \frac{d}{n}$$

- 当 $n \ll d$ 时（样本少，特征多），Kernel method更高效
- 当 $n \gg d$ 时（样本多，特征少），Feature map方法更高效

------

## 第十一题：超平面的函数间隔和几何间隔

**题目：** 对超平面 $w^\top x + b = 0$，样本 $x^{(i)}$ 到的函数间隔 $\hat{\gamma}^{(i)}$ 与几何间隔 $\gamma^{(i)}$ 满足何关系？直接给出答案即可。

**解：**

函数间隔定义为：

$$\hat{\gamma}^{(i)} = y^{(i)}(w^\top x^{(i)} + b)$$

几何间隔定义为：

$\gamma^{(i)} = \frac{y^{(i)}(w^\top x^{(i)} + b)}{|w|} = \frac{\hat{\gamma}^{(i)}}{|w|}$

**关系：**

$\gamma^{(i)} = \frac{\hat{\gamma}^{(i)}}{|w|}$

几何间隔是函数间隔除以权重向量的范数，表示点到超平面的真实距离。

## 第十二题：SVM的Lagrange函数和对偶形式

**题目：** 已知SVM的优化目标为：

$$
\min_{w,b} \quad \frac{1}{2}\|w\|^2 \qquad (1)
$$

$$
\text{s.t.} \quad y^{(i)}(w^\top x^{(i)} + b) \geq 1, \quad i=1,\ldots,n
$$

请构造其Lagrange函数 $\mathcal{L}(w,b,\alpha)$。

已知 $\mathcal{L}(w,b,\alpha)$ 满足Slater条件，因此强对偶成立，问题(1)最终可转化为 $\max_{\alpha;\alpha_i\geq 0}\min_w \mathcal{L}(w,b,\alpha)$，证明该对偶形式问题可进一步转化为：

$$
\max_\alpha W(\alpha) = \max_\alpha \left(\sum_{i=1}^{n}\alpha_i - \frac{1}{2}\sum_{i,j=1}^{n}y^{(i)}y^{(j)}\alpha_i\alpha_j\langle x^{(i)}, x^{(j)}\rangle\right) \qquad (2)
$$

约束条件：

$$
\alpha_i \geq 0, \quad i=1,\ldots,n
$$

$$
\sum_{i=1}^{n}\alpha_i y^{(i)} = 0
$$

**解：**

**步骤1：构造Lagrange函数**

$\mathcal{L}(w,b,\alpha) = \frac{1}{2}|w|^2 - \sum_{i=1}^{n}\alpha_i[y^{(i)}(w^\top x^{(i)} + b) - 1]$

其中 $\alpha_i \geq 0$ 为Lagrange乘子。

**步骤2：固定 $\alpha$，对 $w$ 求导**

$\frac{\partial \mathcal{L}}{\partial w} = w - \sum_{i=1}^{n}\alpha_i y^{(i)} x^{(i)} = 0$

因此：

$w = \sum_{i=1}^{n}\alpha_i y^{(i)} x^{(i)}$

**步骤3：固定 $\alpha$，对 $b$ 求导**

$\frac{\partial \mathcal{L}}{\partial b} = -\sum_{i=1}^{n}\alpha_i y^{(i)} = 0$

因此：

$\sum_{i=1}^{n}\alpha_i y^{(i)} = 0$

**步骤4：代入Lagrange函数**

将 $w = \sum_{i=1}^{n}\alpha_i y^{(i)} x^{(i)}$ 代入 $\mathcal{L}$：

$\begin{aligned} \mathcal{L}(w,b,\alpha) &= \frac{1}{2}w^\top w - \sum_{i=1}^{n}\alpha_i y^{(i)} w^\top x^{(i)} - b\sum_{i=1}^{n}\alpha_i y^{(i)} + \sum_{i=1}^{n}\alpha_i \\ &= \frac{1}{2}\left(\sum_{i=1}^{n}\alpha_i y^{(i)} x^{(i)}\right)^\top\left(\sum_{j=1}^{n}\alpha_j y^{(j)} x^{(j)}\right) - \sum_{i=1}^{n}\alpha_i y^{(i)} \left(\sum_{j=1}^{n}\alpha_j y^{(j)} x^{(j)}\right)^\top x^{(i)} + \sum_{i=1}^{n}\alpha_i \\ &= \frac{1}{2}\sum_{i,j=1}^{n}\alpha_i\alpha_j y^{(i)}y^{(j)}\langle x^{(i)}, x^{(j)}\rangle - \sum_{i,j=1}^{n}\alpha_i\alpha_j y^{(i)}y^{(j)}\langle x^{(i)}, x^{(j)}\rangle + \sum_{i=1}^{n}\alpha_i \\ &= \sum_{i=1}^{n}\alpha_i - \frac{1}{2}\sum_{i,j=1}^{n}\alpha_i\alpha_j y^{(i)}y^{(j)}\langle x^{(i)}, x^{(j)}\rangle \end{aligned}$

其中使用了 $\sum_{i=1}^{n}\alpha_i y^{(i)} = 0$，所以 $b$ 项消失。

因此对偶问题为：

$\max_\alpha W(\alpha) = \max_\alpha \left(\sum_{i=1}^{n}\alpha_i - \frac{1}{2}\sum_{i,j=1}^{n}y^{(i)}y^{(j)}\alpha_i\alpha_j\langle x^{(i)}, x^{(j)}\rangle\right)$

约束条件为：

$\begin{aligned} \alpha_i &\geq 0, \quad i=1,\ldots,n \\ \sum_{i=1}^{n}\alpha_i y^{(i)} &= 0 \end{aligned}$

------

## 第十三题：线性不可分的SVM与L1正则

**题目：** 对线性不可分的训练集，SVM对应带L1正则的优化目标是什么？已知对线性可分情况的优化为：

$$ \min_{w,b} \quad \frac{1}{2}\|w\|^2 \qquad (1) $$ $$ \text{s.t.} \quad y^{(i)}(w^\top x^{(i)} + b) \geq 1, \quad i=1,\ldots,n $$

**解：**

对于线性不可分的情况，引入松弛变量 $\xi_i \geq 0$，允许某些样本违反间隔约束。

**带L1正则的软间隔SVM优化目标为：**

$\begin{aligned} \min_{w,b,\xi} &\quad \frac{1}{2}|w|^2 + C\sum_{i=1}^{n}\xi_i \\ \text{s.t.} &\quad y^{(i)}(w^\top x^{(i)} + b) \geq 1 - \xi_i, \quad i=1,\ldots,n \\ &\quad \xi_i \geq 0, \quad i=1,\ldots,n \end{aligned}$

其中：

- $\xi_i$ 是松弛变量，表示样本 $i$ 违反间隔的程度
- $C > 0$ 是惩罚参数，控制间隔最大化与违反程度之间的权衡
- $C\sum_{i=1}^{n}\xi_i$ 是L1正则项（对松弛变量的惩罚）

这个目标函数平衡了两个目标：

1. 最大化间隔（通过最小化 $|w|^2$）
2. 最小化分类错误（通过最小化 $\sum\xi_i$）

------

## 第十四题：SVM最优化问题分析

**题目：** 已知SVM的最终优化目标为：

$W(\alpha) = \sum_{i=1}^{n}\alpha_i - \frac{1}{2}\sum_{i,j=1}^{n}y^{(i)}y^{(j)}\alpha_i\alpha_j\langle x^{(i)}, x^{(j)}\rangle$

假设此时正在优化 $\alpha_1$ 与 $\alpha_2$，并有 $\alpha_1 = (\zeta - \alpha_2y^{(2)})y^{(1)}$。请推导此时 $\alpha_2$ 应当更新的值。

**解：**

**简化目标函数：**

在固定其他 $\alpha_i$ ($i \geq 3$) 的情况下，目标函数关于 $\alpha_1, \alpha_2$ 可写为：

$W(\alpha_1, \alpha_2) = \alpha_1 + \alpha_2 + W_0 - \frac{1}{2}[K_{11}\alpha_1^2 + K_{22}\alpha_2^2 + 2K_{12}\alpha_1\alpha_2y^{(1)}y^{(2)}] + \text{线性项}$

其中 $K_{ij} = \langle x^{(i)}, x^{(j)}\rangle$，$W_0$ 是常数项。

**利用约束 $\alpha_1 = (\zeta - \alpha_2y^{(2)})y^{(1)}$：**

这个约束来自 $\sum_{i=1}^{n}\alpha_i y^{(i)} = 0$，可以改写为：

$\alpha_1 y^{(1)} + \alpha_2 y^{(2)} = -\sum_{i=3}^{n}\alpha_i y^{(i)} = \zeta \quad \text{（常数）}$

将 $\alpha_1 = (\zeta - \alpha_2y^{(2)})y^{(1)}$ 代入目标函数，得到关于 $\alpha_2$ 的单变量优化问题。

对 $\alpha_2$ 求导并令其为0，经过复杂推导（涉及预测误差），得到 $\alpha_2$ 的无约束最优解：

$\alpha_2^{\text{new, unc}} = \alpha_2^{\text{old}} + \frac{y^{(2)}(E_1 - E_2)}{\eta}$

其中：

- $E_i = f(x^{(i)}) - y^{(i)}$ 是预测误差
- $\eta = K_{11} + K_{22} - 2K_{12} = \|x^{(1)} - x^{(2)}\|^2$（特征空间距离）

**考虑约束 $0 \leq \alpha_2 \leq C$：**

根据约束 $\alpha_1 y^{(1)} + \alpha_2 y^{(2)} = \zeta$：

- 若 $y^{(1)} \neq y^{(2)}$： $L = \max(0, \alpha_2^{\text{old}} - \alpha_1^{\text{old}}), \quad H = \min(C, C + \alpha_2^{\text{old}} - \alpha_1^{\text{old}})$
- 若 $y^{(1)} = y^{(2)}$： $L = \max(0, \alpha_1^{\text{old}} + \alpha_2^{\text{old}} - C), \quad H = \min(C, \alpha_1^{\text{old}} + \alpha_2^{\text{old}})$

**最终更新公式：**

$\alpha_2^{\text{new}} = \begin{cases} H & \text{if } \alpha_2^{\text{new, unc}} > H \\ \alpha_2^{\text{new, unc}} & \text{if } L \leq \alpha_2^{\text{new, unc}} \leq H \\ L & \text{if } \alpha_2^{\text{new, unc}} < L \end{cases}$

然后通过约束更新 $\alpha_1$：

$\alpha_1^{\text{new}} = \alpha_1^{\text{old}} + y^{(1)}y^{(2)}(\alpha_2^{\text{old}} - \alpha_2^{\text{new}})$

------

## 第十五题：信息增益比计算

**题目：** 计算给定数据集中四个特征的信息增益比。可保留log项，统一底数为2。

[![image.png](https://i.postimg.cc/Px8dD1mZ/image.png)](https://postimg.cc/N9Bhq9Cf)

**解：**

首先计算数据集的熵。类别分布：否=6，是=9，总计15。

$H(D) = -\frac{6}{15}\log_2\frac{6}{15} - \frac{9}{15}\log_2\frac{9}{15} = -0.4\log_2(0.4) - 0.6\log_2(0.6) = 0.971$

### 特征1：年龄

- 青年(5个)：否=3，是=2，$H = 0.971$
- 中年(5个)：否=1，是=4，$H = 0.722$
- 老年(5个)：否=2，是=3，$H = 0.971$

条件熵：

$H(D|\text{年龄}) = \frac{5}{15}(0.971) + \frac{5}{15}(0.722) + \frac{5}{15}(0.971) = 0.888$

信息增益：

$\text{Gain}(\text{年龄}) = 0.971 - 0.888 = 0.083$

特征熵（分裂信息）：

$H_A(\text{年龄}) = -3 \times \frac{5}{15}\log_2\frac{5}{15} = \log_2 3 = 1.585$

信息增益比：

$$ \text{Gain\_ratio}(\text{年龄}) = \frac{0.083}{1.585} = 0.052 $$

### 特征2：有工作

- 否(8个)：否=4，是=4，$H = 1.0$
- 是(7个)：否=2，是=5，$H = 0.863$

条件熵：$H(D|\text{有工作}) = 0.936$

信息增益：$\text{Gain}(\text{有工作}) = 0.035$

特征熵：$H_A(\text{有工作}) = 0.997$

信息增益比：$$ \text{Gain\_ratio}(\text{有工作}) = 0.035 $$

### 特征3：有自己的房子

- 否(9个)：否=3，是=6，$H = 0.918$
- 是(6个)：否=3，是=3，$H = 1.0$

条件熵：$H(D|\text{有房}) = 0.951$

信息增益：$\text{Gain}(\text{有房}) = 0.020$

特征熵：$H_A(\text{有房}) = 0.971$

信息增益比：$$ \text{Gain\_ratio}(\text{有房}) = 0.021 $$

### 特征4：信贷情况

- 一般(5个)：否=4，是=1，$H = 0.722$
- 好(6个)：否=2，是=4，$H = 0.918$
- 非常好(4个)：否=0，是=4，$H = 0$

条件熵：$H(D|\text{信贷}) = 0.608$

信息增益：$\text{Gain}(\text{信贷}) = 0.363$

特征熵：$H_A(\text{信贷}) = 1.557$

信息增益比：$$ \text{Gain\_ratio}(\text{信贷}) = 0.233 $$

### 总结（信息增益比排序）

1. **信贷情况：0.233** ⭐（最佳分裂特征）
2. 年龄：0.052
3. 有工作：0.035
4. 有自己的房子：0.021

应选择"信贷情况"作为根节点的分裂特征。

------

## 第十六题：XGBoost损失函数二阶泰勒展开

**题目：** 已知XGBoost优化第t棵树时的损失函数为：

$\mathcal{L}^{(t)} = \sum_{i=1}^{n}l(y_i, \hat{y}*i^{(t-1)} + f_t(x_i)) + \gamma T + \frac{1}{2}\lambda\sum*{j=1}^{T}w_j^2$

请推导 $l(y_i, \hat{y}_i^{(t-1)} + f_t(x_i))$ 在 $l(y_i, \hat{y}_i^{(t-1)})$ 处对于 $f_t(x_i)$ 的二阶泰勒展开。其中，一阶和二阶导数可使用：

$g_i = \frac{\partial l(y_i, \hat{y}_i)}{\partial \hat{y}*i}\Big|*{\hat{y}_i^{(t-1)}}, \quad h_i = \frac{\partial^2 l(y_i, \hat{y}_i)}{\partial \hat{y}*i^2}\Big|*{\hat{y}_i^{(t-1)}}$

在此基础上，推导叶子节点 $j$ 对应的 $w_j^*$ 满足：

$w_j^* = -\frac{\sum_{i \in \mathcal{I}*j}g_i}{\sum*{i \in \mathcal{I}_j}h_i + \lambda}$

其中，$\mathcal{I}_j = \{i \mid q(x_i) = j\}$ 表示属于叶子节点 $j$ 的样本集合。

**解：**

### 二阶泰勒展开

在 $\hat{y}_i^{(t-1)}$ 处对 $l(y_i, \hat{y}_i^{(t-1)} + f_t(x_i))$ 关于 $f_t(x_i)$ 进行二阶泰勒展开：

$\begin{aligned} l(y_i, \hat{y}_i^{(t-1)} + f_t(x_i)) &\approx l(y_i, \hat{y}_i^{(t-1)}) + \frac{\partial l(y_i, \hat{y}_i)}{\partial \hat{y}*i}\Big|*{\hat{y}_i^{(t-1)}} \cdot f_t(x_i) \\ &\quad + \frac{1}{2}\frac{\partial^2 l(y_i, \hat{y}_i)}{\partial \hat{y}*i^2}\Big|*{\hat{y}_i^{(t-1)}} \cdot f_t(x_i)^2 \\ &= l(y_i, \hat{y}_i^{(t-1)}) + g_i f_t(x_i) + \frac{1}{2}h_i f_t(x_i)^2 \end{aligned}$

因此损失函数变为：

$\mathcal{L}^{(t)} \approx \sum_{i=1}^{n}[l(y_i, \hat{y}*i^{(t-1)}) + g_i f_t(x_i) + \frac{1}{2}h_i f_t(x_i)^2] + \gamma T + \frac{1}{2}\lambda\sum*{j=1}^{T}w_j^2$

去掉常数项 $\sum_{i=1}^{n}l(y_i, \hat{y}_i^{(t-1)})$：

$\tilde{\mathcal{L}}^{(t)} = \sum_{i=1}^{n}[g_i f_t(x_i) + \frac{1}{2}h_i f_t(x_i)^2] + \gamma T + \frac{1}{2}\lambda\sum_{j=1}^{T}w_j^2$

### 推导叶子权重 $w_j^*$

对于树模型，$f_t(x_i) = w_{q(x_i)}$，其中 $q(x_i)$ 表示样本 $i$ 落在的叶子节点。

将样本按叶子节点分组：

$\tilde{\mathcal{L}}^{(t)} = \sum_{j=1}^{T}\left[\left(\sum_{i \in \mathcal{I}*j}g_i\right)w_j + \frac{1}{2}\left(\sum*{i \in \mathcal{I}_j}h_i + \lambda\right)w_j^2\right] + \gamma T$

记 $G_j = \sum_{i \in \mathcal{I}*j}g_i$，$H_j = \sum*{i \in \mathcal{I}_j}h_i$，则：

$\tilde{\mathcal{L}}^{(t)} = \sum_{j=1}^{T}\left[G_j w_j + \frac{1}{2}(H_j + \lambda)w_j^2\right] + \gamma T$

对 $w_j$ 求导并令其为0：

$\frac{\partial \tilde{\mathcal{L}}^{(t)}}{\partial w_j} = G_j + (H_j + \lambda)w_j = 0$

解得：

$w_j^* = -\frac{G_j}{H_j + \lambda} = -\frac{\sum_{i \in \mathcal{I}*j}g_i}{\sum*{i \in \mathcal{I}_j}h_i + \lambda}$

这就是叶子节点的最优权重。将其代入损失函数，得到：

$\tilde{\mathcal{L}}^{(t)} = -\frac{1}{2}\sum_{j=1}^{T}\frac{G_j^2}{H_j + \lambda} + \gamma T$

这个公式用于评估树结构的质量，指导分裂决策。