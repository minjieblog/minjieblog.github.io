---
title: "机器学习习题集"
date: 2025-12-13T20:26:19+08:00
draft: false
categories: ["研究生课程"] 
tags: ["机器学习", "数学推导", "算法"] 
math: true
summary: "胡老师上的机器学习课程习题集解答汇总"
description: "本习题集包含16道经典机器学习问题的完整数学推导，从梯度下降、交叉熵损失、最大似然估计，到核方法、支持向量机对偶问题、信息增益比|
计算，再到XGBoost的二阶泰勒展开。每道题目都配有详细的解题步骤和数学证明，帮助读者深入理解机器学习算法的理论基础。"
comments: true
---

## 第一题：线性回归梯度下降

**题目：** 对线性模型 $h_\theta(x) = \theta^\top x$，给定训练集 $\{(x^{(i)}, y^{(i)})\}$，推导其向量形式的最小二乘损失梯度下降更新公式为：

$$\theta := \theta + \alpha \sum_{i=1}^{n} (y^{(i)} - h_\theta(x^{(i)})) x^{(i)}$$

**解：**

最小二乘损失函数为：

$$J(\theta) = \frac{1}{2}\sum_{i=1}^{n}(h_\theta(x^{(i)}) - y^{(i)})^2 = \frac{1}{2}\sum_{i=1}^{n}(\theta^\top x^{(i)} - y^{(i)})^2$$

对 $\theta$ 求梯度：

$$\begin{aligned} \nabla_\theta J(\theta) &= \sum_{i=1}^{n}(\theta^\top x^{(i)} - y^{(i)}) \cdot x^{(i)} = \sum_{i=1}^{n}(h_\theta(x^{(i)}) - y^{(i)}) x^{(i)} \end{aligned}$$

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

