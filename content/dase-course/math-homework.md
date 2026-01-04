---
title: "数学基础——数学篇作业集"
date: 2025-12-29T13:25:26+08:00
comments: true
draft: false
categories: ["研究生课程"]
tags: ["线性代数", "矩阵分解", "数值分析", "数学证明"]
math: true
summary: "数学基础课程矩阵理论相关作业完整解答"
description: |
  本作业集包含四次数学基础作业的完整解答，涵盖：
  矩阵范数理论、特征值与特征多项式、正交投影、
  LU分解、Cholesky分解、QR分解、SVD奇异值分解、
  最小二乘法、图像处理应用（旋转与压缩）、
  以及 kNN 分类器的实现。每道题目都配有详细的
  数学推导过程和部分编程实现代码。
---

## 作业一：矩阵范数与基础理论

### 习题 1：幂等矩阵的性质

**题目：** 矩阵 $A^2 = A, B^2 = B$，并且 $B$ 的列是 $A$ 的列的线性组合。证明 $AB = B$。

**证明：**

设 $B$ 的列是 $A$ 的列的线性组合，则存在矩阵 $C$ 使得 $B = AC$。

现在计算 $AB$，由于 $A^2 = A$：

$$AB = A(AC) = A^2C = AC = B$$

因此 $AB = B$。

---

### 习题 2：特征多项式的不变性

**题目：** 设 $A, B$ 为任意两个 $n$ 阶方阵，证明：$AB$ 和 $BA$ 具有相同的特征多项式，即 $|\lambda E - AB| = |\lambda E - BA|$。

**证明：**

考虑分块矩阵：

$$ \begin{pmatrix} E & 0 \\ A & E \end{pmatrix}
\begin{pmatrix} \lambda E & B \\ 0 & \lambda E - AB \end{pmatrix}
\begin{pmatrix} E & 0 \\ -A & E \end{pmatrix}
=\begin{pmatrix} \lambda E - BA & B \\ 0 & \lambda E \end{pmatrix}$$

取行列式，利用行列式的乘法性质：

$$ \left|\begin{array}{cc}
\lambda E & B \\ 0 & \lambda E - AB
\end{array}\right|
=\left|\begin{array}{cc} \lambda E - BA & B \\ 0 & \lambda E \end{array}\right| $$

由于分块上三角矩阵的行列式等于对角块行列式的乘积：

$$\lambda^n |\lambda E - AB| = \lambda^n |\lambda E - BA|$$

当 $\lambda \neq 0$ 时，两边同时除以 $\lambda^n$，得到：

$$|\lambda E - AB| = |\lambda E - BA|$$

两边都是关于 $\lambda$ 的多项式，故此恒等式对任意 $\lambda$（包括 $\lambda=0$）均成立。

---

### 习题 3：矩阵范数计算

**题目：** 求下面矩阵的 $1$-范数、$2$-范数和无穷范数：

$$A_1 = \begin{pmatrix} 1 & 2 \\ 1 & 0 \end{pmatrix}, \quad A_2 = \begin{pmatrix} -1 & 0 \\ 1 & 2 \end{pmatrix}$$

**解：**

**对于 $A_1$：**

- **1-范数**（列和范数）：
  $$\|A_1\|_1 = \max\{|1|+|1|, |2|+|0|\} = \max\{2, 2\} = 2$$

- **$\infty$-范数**（行和范数）：
  $$\|A_1\|_\infty = \max\{|1|+|2|, |1|+|0|\} = \max\{3, 1\} = 3$$

- **2-范数**：首先计算 $A_1^T A_1$：
  $$A_1^T A_1 = \begin{pmatrix} 2 & 2 \\ 2 & 4 \end{pmatrix}$$
  
  特征方程：$\det(\lambda E - A_1^T A_1) = \lambda^2 - 6\lambda + 4 = 0$
  
  解得：$\lambda = 3 \pm \sqrt{5}$，最大特征值为 $\lambda_{\max} = 3 + \sqrt{5}$
  
  因此：$\|A_1\|_2 = \sqrt{3 + \sqrt{5}}$

**对于 $A_2$：**

- **1-范数**：$\|A_2\|_1 = 2$
- **$\infty$-范数**：$\|A_2\|_\infty = 3$
- **2-范数**：$A_2^T A_2 = \begin{pmatrix} 2 & 2 \\ 2 & 4 \end{pmatrix}$（与 $A_1^T A_1$ 相同）
  
  因此：$\|A_2\|_2 = \sqrt{3 + \sqrt{5}}$

---

### 习题 4：诱导范数的证明

**题目：** 证明 $1$ 范数为列和范数，无穷范数为行和范数：

$$\|A\|_{\infty} = \max_{1 \leq i \leq m} \sum_{j=1}^{n} |a_{ij}|, \quad \|A\|_1 = \max_{1 \leq j \leq n} \sum_{i=1}^{m} |a_{ij}|$$

**证明：**

**证明1-范数：**

对于任意 $x \in \mathbb{C}^n$ 且 $\|x\|_1 = 1$：

$$\|Ax\|_1 = \sum_{i=1}^m \left|\sum_{j=1}^n a_{ij}x_j\right| \leq \sum_{i=1}^m \sum_{j=1}^n |a_{ij}||x_j| = \sum_{j=1}^n |x_j| \sum_{i=1}^m |a_{ij}|$$

$$\leq \max_{1 \leq j \leq n} \sum_{i=1}^m |a_{ij}|$$

取 $x = e_k$（第 $k$ 列和最大的单位向量）可达到等号。

**证明$\infty$-范数：**

对于任意 $x \in \mathbb{C}^n$ 且 $\|x\|_\infty = 1$：

$$\|Ax\|_\infty = \max_{1 \leq i \leq m} \left|\sum_{j=1}^n a_{ij}x_j\right| \leq \max_{1 \leq i \leq m} \sum_{j=1}^n |a_{ij}|$$

取适当的 $x$ 可达到等号。

---

### 习题 5：Frobenius 范数不等式

**题目：** 证明：$\|AB\|_F \leq \|A\|_F \|B\|_F$ 和 $\|AB\|_F \leq \|A\|_F \|B\|_2$。

**证明：**

**第一个不等式：**

由 Cauchy-Schwarz 不等式：

$$|(AB)_{ik}|^2 = \left|\sum_{j=1}^n a_{ij}b_{jk}\right|^2 \leq \left(\sum_{j=1}^n |a_{ij}|^2\right) \left(\sum_{j=1}^n |b_{jk}|^2\right)$$

对所有 $i,k$ 求和并放缩，得到 $\|AB\|_F \leq \|A\|_F \|B\|_F$。

**第二个不等式：**

设 $B = [b_1, \ldots, b_p]$，则：

$$\|AB\|_F^2 = \sum_{j=1}^p \|Ab_j\|_2^2 \leq \|A\|_2^2 \sum_{j=1}^p \|b_j\|_2^2 = \|A\|_2^2 \|B\|_F^2$$

由于 $\|A\|_2 \leq \|A\|_F$，因此 $\|AB\|_F \leq \|A\|_F \|B\|_2$。

---

### 习题 6：距离函数判定

**题目：** 判断以下"距离"是否是数学意义上的度量：
1. 余弦距离：$d(a, b) = 1 - \cos\langle a, b\rangle$
2. 编辑距离（Levenshtein距离）

**解：**

**(1) 余弦距离不是度量**

虽然满足非负性和对称性，但**违反三角不等式**。

**(2) 编辑距离是度量**

满足三个条件：
- **非负性**：操作次数非负，$d(S_1, S_2) = 0$ 当且仅当 $S_1 = S_2$
- **对称性**：插入的逆是删除，替换是对称的
- **三角不等式**：$d(S_1, S_3) \leq d(S_1, S_2) + d(S_2, S_3)$（通过串接操作序列）

---

### 习题 7：正定矩阵与向量范数

**题目：** 证明：在 $\mathbb{R}^n$ 上，当且仅当 $A$ 是正定矩阵时，函数 $f(\boldsymbol{x}) = (\boldsymbol{x}^{\mathrm{T}} A\boldsymbol{x})^{\frac{1}{2}}$ 是一个向量范数。

**证明：**

**必要性：** 若 $f$ 是范数，由正定性知 $x^T A x > 0$ 对所有 $x \neq 0$ 成立，故 $A$ 正定。

**充分性：** 若 $A$ 正定，验证三条性质：

1. **正定性**：$x^T A x > 0$ 对 $x \neq 0$，故 $f(x) > 0$
2. **齐次性**：$f(\alpha x) = |\alpha| f(x)$（直接验证）
3. **三角不等式**：由 Cholesky 分解 $A = B^T B$，有：
   $$f(x) = \|Bx\|_2$$
   利用欧氏范数的三角不等式：
   $$f(x+y) = \|B(x+y)\|_2 \leq \|Bx\|_2 + \|By\|_2 = f(x) + f(y)$$

因此 $f$ 是向量范数。

---

### 习题 8：矩阵最大范数

**题目：** 证明：$\|A\|_{m\infty} := \max_{1 \leq i \leq m, 1 \leq j \leq n} |a_{ij}|$ 是 $\mathbb{R}^{m \times n}$ 上的矩阵范数。

**证明：**

验证三条性质：
1. **正定性**：显然成立
2. **齐次性**：$\|\alpha A\|_{m\infty} = |\alpha| \|A\|_{m\infty}$
3. **三角不等式**：
   $$\|A + B\|_{m\infty} = \max_{i,j} |a_{ij} + b_{ij}| \leq \max_{i,j} |a_{ij}| + \max_{i,j} |b_{ij}| = \|A\|_{m\infty} + \|B\|_{m\infty}$$

因此是矩阵范数。

---

## 作业二：矩阵分解理论

### 习题 1：一维子空间的正交投影

**题目：** 求向量 $(1,1,1)^T$ 在 $\text{span}([1,-1,1]^T)$ 上的正交投影。

**解：**

设 $v = (1,1,1)^T$，$u = [1,-1,1]^T$

正交投影公式：

$$\text{proj}_u(v) = \frac{\langle v, u \rangle}{\langle u, u \rangle} u = \frac{1}{3} \begin{pmatrix} 1 \\ -1 \\ 1 \end{pmatrix} = \begin{pmatrix} 1/3 \\ -1/3 \\ 1/3 \end{pmatrix}$$

---

### 习题 2：仿射子空间的正交投影

**题目：** 求 $(1,1,1)^T$ 在 $\text{span}\{[1,-1,1]^T, (1,1,0)^T\} + (1,2,1)^T$ 上的正交投影。

**解：**

这是仿射子空间的投影问题。设 $v = (1,1,1)^T$，$a = (1,2,1)^T$

首先求 $v - a = (0,-1,0)^T$ 在 $\text{span}\{u_1, u_2\}$ 上的投影，其中：
- $u_1 = [1,-1,1]^T$
- $u_2 = (1,1,0)^T$

验证 $u_1 \perp u_2$：$\langle u_1, u_2 \rangle = 0$

计算投影：

$$\text{proj}(v-a) = \frac{1}{3}(1,-1,1)^T + \frac{-1}{2}(1,1,0)^T = \left(-\frac{1}{6}, -\frac{5}{6}, \frac{1}{3}\right)^T$$

最终投影：

$$\text{proj}(v) = \text{proj}(v-a) + a = \left(\frac{5}{6}, \frac{7}{6}, \frac{4}{3}\right)^T$$

---

### 习题 5：LU 分解

**题目：** 对矩阵 $C$ 和 $B$ 判断能否进行 $LU$ 分解。

$$C = \begin{pmatrix} 3 & 2 & -1 \\ -1 & 0 & 0 \\ -1 & 3 & 0 \end{pmatrix}, \quad B = \begin{pmatrix} 0 & 2 & -1 \\ -1 & 4 & -1 \\ 1 & 3 & -5 \end{pmatrix}$$

**解：**

**矩阵 $C$：** 所有顺序主子式非零，可以进行 $LU$ 分解：

$$C = \begin{pmatrix} 1 & 0 & 0 \\ -\frac{1}{3} & 1 & 0 \\ -\frac{1}{3} & \frac{11}{2} & 1 \end{pmatrix} \begin{pmatrix} 3 & 2 & -1 \\ 0 & \frac{2}{3} & -\frac{1}{3} \\ 0 & 0 & \frac{3}{2} \end{pmatrix}$$

**矩阵 $B$：** 第一个顺序主子式为 0，不能直接进行 $LU$ 分解，但可以通过行交换进行 $PLU$ 分解。

---

### 习题 6：LU 分解实例

**题目：** 求矩阵 $A$ 的 $LU$ 分解：

$$A = \begin{pmatrix} 2 & 1 & 1 \\ 1 & 2 & 1 \\ 1 & 1 & 0 \end{pmatrix}$$

**解：**

进行高斯消元：

$$A = \begin{pmatrix} 1 & 0 & 0 \\ 1/2 & 1 & 0 \\ 1/2 & 1/3 & 1 \end{pmatrix} \begin{pmatrix} 2 & 1 & 1 \\ 0 & 3/2 & 1/2 \\ 0 & 0 & -2/3 \end{pmatrix} = LU$$

---

## 作业三：高级矩阵分解

### 习题 1：Cholesky 分解（不带平方根）

**题目：** 求对称正定矩阵的 $LDL^T$ 分解：

$$A = \begin{pmatrix} 5 & 2 & -4 \\ 2 & 1 & -2 \\ -4 & -2 & 5 \end{pmatrix}$$

**解：**

$$L = \begin{pmatrix} 1 & 0 & 0 \\ \frac{2}{5} & 1 & 0 \\ -\frac{4}{5} & -2 & 1 \end{pmatrix}, \quad D = \begin{pmatrix} 5 & 0 & 0 \\ 0 & \frac{1}{5} & 0 \\ 0 & 0 & 1 \end{pmatrix}$$

---

### 习题 3：QR 分解求解线性方程组

**题目：** 利用 $QR$ 分解求解：

$$\begin{bmatrix} 1 & 2 & 2 \\ 2 & 1 & 2 \\ 1 & 2 & 1 \end{bmatrix} \begin{bmatrix} x_1 \\ x_2 \\ x_3 \end{bmatrix} = \begin{bmatrix} 1 \\ 2 \\ 3 \end{bmatrix}$$

**解：**

使用 Gram-Schmidt 正交化得到：

$$Q = \begin{bmatrix} 
\frac{1}{\sqrt{6}} & \frac{1}{\sqrt{3}} & \frac{1}{\sqrt{2}} \\ 
\frac{2}{\sqrt{6}} & -\frac{1}{\sqrt{3}} & 0 \\ 
\frac{1}{\sqrt{6}} & \frac{1}{\sqrt{3}} & -\frac{1}{\sqrt{2}} 
\end{bmatrix}$$

方程组的解为：$x = R^{-1}Q^T b$

---

### 习题 5：SVD 分解与应用

**题目：** 对矩阵进行 SVD 分解并应用于低秩逼近。

$$A = \begin{pmatrix} -8 & 5 & 1 \\ -4 & 7 & 5 \\ -8 & 5 & 1 \\ -4 & 7 & 5 \end{pmatrix}$$

**解：**

SVD 分解：$A = U\Sigma V^T$，其中奇异值为 $\sigma_1 = 18, \sigma_2 = 6, \sigma_3 = 0$

- $\text{rank}(A) = 2$
- $\|A\|_2 = 18$
- $\|A\|_F = 6\sqrt{10}$

**低秩逼近：**
- $k=1$: $\gamma_1 = 6$
- $k \geq 2$: $\gamma_k = 0$

---

### 习题 8：最小二乘法的法方程

**题目：** 利用等式证明最小二乘解满足法方程 $A^TA\boldsymbol{x} = A^T\boldsymbol{b}$。

**证明：**

对于最小二乘解 $\boldsymbol{x}$，有：

$$\|A(\boldsymbol{x} + \alpha\boldsymbol{w}) - \boldsymbol{b}\|_2^2 \geq \|A\boldsymbol{x} - \boldsymbol{b}\|_2^2$$

展开得：

$$2\alpha\boldsymbol{w}^TA^T(A\boldsymbol{x} - \boldsymbol{b}) + \alpha^2\|A\boldsymbol{w}\|_2^2 \geq 0$$

为使对所有 $\alpha$ 成立，必须：

$$\boldsymbol{w}^TA^T(A\boldsymbol{x} - \boldsymbol{b}) = 0$$

由于 $\boldsymbol{w}$ 任意，因此 $A^TA\boldsymbol{x} = A^T\boldsymbol{b}$。

---

## 总结

本作业集系统地涵盖了：
- 矩阵范数理论与证明
- 特征值与特征多项式
- 各类矩阵分解（LU, Cholesky, QR, SVD）
- 正交投影与最小二乘
- 图像处理应用

通过这些习题的训练，可以深入理解线性代数在数值分析和机器学习中的核心作用。