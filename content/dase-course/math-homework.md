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

# 作业一

---

## 习题 1：幂等矩阵的性质

**题目**：矩阵 $A^2 = A, B^2 = B$，并且 $B$ 的列是 $A$ 的列的线性组合。证明 $AB = B$。

**证明**：

设 $B$ 的列是 $A$ 的列的线性组合，则存在矩阵 $C$ 使得 $B = AC$。

现在计算 $AB$，由于 $A^2 = A$：

$$AB = A(AC) = A^2C = AC = B$$

因此 $AB = B$。 $\square$

---

## 习题 2：特征多项式的相等性

**题目**：设 $A, B$ 为任意两个 $n$ 阶方阵，证明：$AB$ 和 $BA$ 具有相同的特征多项式，即 $|\lambda E - AB| = |\lambda E - BA|$。

**证明**：

设

$$\begin{pmatrix} E & 0 \\ A & E \end{pmatrix} \begin{pmatrix} \lambda E & B \\ 0 & \lambda E - AB \end{pmatrix} \begin{pmatrix} E & 0 \\ -A & E \end{pmatrix} = \begin{pmatrix} \lambda E - BA & B \\ 0 & \lambda E \end{pmatrix}$$

取行列式，利用行列式的乘法性质，有

$$\left|\begin{array}{cc} \lambda E & B \\ 0 & \lambda E - AB \end{array}\right| = \left|\begin{array}{cc} \lambda E - BA & B \\ 0 & \lambda E \end{array}\right|$$

由于

$$\left|\begin{array}{cc} \lambda E & B \\ 0 & \lambda E - AB \end{array}\right| = \lambda^n |\lambda E - AB|, \qquad \left|\begin{array}{cc} \lambda E - BA & B \\ 0 & \lambda E \end{array}\right| = \lambda^n |\lambda E - BA|$$

因此有

$$\lambda^n |\lambda E - AB| = \lambda^n |\lambda E - BA|$$

当 $\lambda \neq 0$ 时，两边可同时除以 $\lambda^n$，得到

$$|\lambda E - AB| = |\lambda E - BA|$$

两边都是关于 $\lambda$ 的多项式，故此恒等式对任意 $\lambda$（包括 $\lambda=0$）均成立。 $\square$

---

## 习题 3：矩阵范数的计算

**题目**：求下面矩阵的 1-范数、2-范数和无穷范数：

$$A_1 = \begin{pmatrix} 1 & 2 \\ 1 & 0 \end{pmatrix}, \quad A_2 = \begin{pmatrix} -1 & 0 \\ 1 & 2 \end{pmatrix}$$

**解**：

#### 对于矩阵 $A_1$

**1-范数**（列和范数）：

$$\|A_1\|_1 = \max\{|1|+|1|, |2|+|0|\} = \max\{2, 2\} = 2$$

**无穷范数**（行和范数）：

$$\|A_1\|_\infty = \max\{|1|+|2|, |1|+|0|\} = \max\{3, 1\} = 3$$

**2-范数**（谱范数，即 $\sqrt{\rho(A_1^T A_1)}$）：

首先计算 $A_1^T A_1$：

$$A_1^T A_1 = \begin{pmatrix} 1 & 1 \\ 2 & 0 \end{pmatrix} \begin{pmatrix} 1 & 2 \\ 1 & 0 \end{pmatrix} = \begin{pmatrix} 2 & 2 \\ 2 & 4 \end{pmatrix}$$

求特征值：

$$\det(\lambda E - A_1^T A_1) = \det\begin{pmatrix} \lambda-2 & -2 \\ -2 & \lambda-4 \end{pmatrix} = (\lambda-2)(\lambda-4) - 4 = \lambda^2 - 6\lambda + 4 = 0$$

解得：$\lambda = \frac{6 \pm \sqrt{36-16}}{2} = \frac{6 \pm \sqrt{20}}{2} = 3 \pm \sqrt{5}$

最大特征值为 $\lambda_{\max} = 3 + \sqrt{5}$

因此：

$$\|A_1\|_2 = \sqrt{3 + \sqrt{5}}$$

#### 对于矩阵 $A_2$

**1-范数**：

$$\|A_2\|_1 = \max\{|-1|+|1|, |0|+|2|\} = \max\{2, 2\} = 2$$

**无穷范数**：

$$\|A_2\|_\infty = \max\{|-1|+|0|, |1|+|2|\} = \max\{1, 3\} = 3$$

**2-范数**：

计算 $A_2^T A_2$：

$$A_2^T A_2 = \begin{pmatrix} -1 & 1 \\ 0 & 2 \end{pmatrix} \begin{pmatrix} -1 & 0 \\ 1 & 2 \end{pmatrix} = \begin{pmatrix} 2 & 2 \\ 2 & 4 \end{pmatrix}$$

这与 $A_1^T A_1$ 相同，因此：

$$\|A_2\|_2 = \sqrt{3 + \sqrt{5}}$$

---

## 习题 4：诱导范数的证明

**题目**：矩阵的范数主要包括三种主要类型：诱导范数，元素形式范数和 Schatten 范数。诱导范数又称矩阵空间上的算子范数 (operator norm)，常用的诱导范数为 $p$ 范数，定义如下

$$\|A\|_p = \sup_{\|x\|_p \neq 0} \frac{\|Ax\|_p}{\|x\|_p} = \sup_{\|x\|_p = 1} \|Ax\|_p$$

**(1)** 设 $A = (a_{ij}) \in \mathbb{C}^{m \times n}$，证明 1 范数为列和范数，无穷范数为行和范数

$$\|A\|_{\infty} = \max_{1 \leq i \leq m} \sum_{j=1}^{n} |a_{ij}|, \quad \|A\|_1 = \max_{1 \leq j \leq n} \sum_{i=1}^{m} |a_{ij}|$$

**(2)** 元素形式范数即矩阵按列排成向量，然后采用向量范数的定义得到的矩阵范数，一般称 $l_p$ 范数。

$$l_p: \|A\|_p = \sqrt[p]{\sum_{i,j} |a_{ij}|^p}$$

试比较 $l_1$ 范数

$$l_1: \|A\|_1 = \sum_{i,j} |a_{ij}|^1$$

与诱导范数的关系。

#### (1) 证明 1-范数和无穷范数

设 $A = (a_{ij}) \in \mathbb{C}^{m \times n}$，证明 1 范数为列和范数，无穷范数为行和范数

$$\|A\|_{\infty} = \max_{1 \leq i \leq m} \sum_{j=1}^{n} |a_{ij}|, \quad \|A\|_1 = \max_{1 \leq j \leq n} \sum_{i=1}^{m} |a_{ij}|$$

**证明**：

**(i) 证明 1-范数为列和范数**

对于任意 $x \in \mathbb{C}^n$ 且 $\|x\|_1 = 1$，利用三角不等式：

$$\|Ax\|_1 = \sum_{i=1}^m \left|\sum_{j=1}^n a_{ij}x_j\right| \leq \sum_{i=1}^m \sum_{j=1}^n |a_{ij}||x_j| = \sum_{j=1}^n |x_j| \sum_{i=1}^m |a_{ij}| \leq \max_{1 \leq j \leq n} \sum_{i=1}^m |a_{ij}|$$

设第 $k$ 列使得列和最大，取 $x = e_k$，则

$$\|Ax\|_1 = \sum_{i=1}^m |a_{ik}| = \max_{1 \leq j \leq n} \sum_{i=1}^m |a_{ij}|$$

故上界可达到。因此 $\|A\|_1 = \max_{1 \leq j \leq n} \sum_{i=1}^m |a_{ij}|$。

**(ii) 证明无穷范数为行和范数**

对于任意 $x \in \mathbb{C}^n$ 且 $\|x\|_\infty = 1$，有：

$$\|Ax\|_\infty = \max_{1 \leq i \leq m} \left|\sum_{j=1}^n a_{ij}x_j\right| \leq \max_{1 \leq i \leq m} \sum_{j=1}^n |a_{ij}||x_j| \leq \max_{1 \leq i \leq m} \sum_{j=1}^n |a_{ij}|$$

设第 $k$ 行使得行和最大，取 $x_j = \text{sgn}(\overline{a_{kj}})$，则

$$\|Ax\|_\infty \geq \sum_{j=1}^n |a_{kj}| = \max_{1 \leq i \leq m} \sum_{j=1}^n |a_{ij}|$$

故 $\|A\|_\infty = \max_{1 \leq i \leq m} \sum_{j=1}^n |a_{ij}|$。 $\square$

#### (2) 元素形式范数与诱导范数的关系

以 $l_1$ 范数

$$l_1: \|A\|_1 = \sum_{i,j} |a_{ij}|^1$$

与诱导范数的关系为例，证明：

$$\|X\|_1 \leq \|X\|_{1(l_1)} \leq n\|X\|_1$$

$$\|X\|_\infty \leq \|X\|_{1(l_1)} \leq m\|X\|_\infty$$

**证明**：

设 $X = (x_{ij}) \in \mathbb{C}^{m \times n}$。记 $\|X\|_1 = \max_{1 \leq j \leq n} \sum_{i=1}^m |x_{ij}|$，$\|X\|_\infty = \max_{1 \leq i \leq m} \sum_{j=1}^n |x_{ij}|$，$\|X\|_{1(l_1)} = \sum_{i,j} |x_{ij}|$。

**第一组不等式**：因为 $\|X\|_1$ 是所有列和的最大值，而 $\|X\|_{1(l_1)}$ 是所有列和的总和，故

$$\|X\|_1 \leq \sum_{j=1}^n \sum_{i=1}^m |x_{ij}| = \|X\|_{1(l_1)} \leq \sum_{j=1}^n \|X\|_1 = n\|X\|_1$$

**第二组不等式**：因为 $\|X\|_\infty$ 是所有行和的最大值，而 $\|X\|_{1(l_1)}$ 是所有行和的总和，故

$$\|X\|_\infty \leq \sum_{i=1}^m \sum_{j=1}^n |x_{ij}| = \|X\|_{1(l_1)} \leq \sum_{i=1}^m \|X\|_\infty = m\|X\|_\infty$$

$\square$

---

## 习题 5：Frobenius 范数的不等式

**题目**：证明：$\|AB\|_F \leq \|A\|_F \|B\|_F$ 和 $\|AB\|_F \leq \|A\|_F \|B\|_2$。

**证明**：

Frobenius 范数定义为 $\|A\|_F = \sqrt{\sum_{i,j} |a_{ij}|^2} = \sqrt{\text{tr}(A^*A)}$。

**(1) 证明第一个不等式：$\|AB\|_F \leq \|A\|_F \|B\|_F$**

将 $B$ 按列分块：$B = [b_1, b_2, \ldots, b_p]$，其中 $b_j$ 是 $B$ 的第 $j$ 列。则：

$$AB = [Ab_1, Ab_2, \ldots, Ab_p]$$

由 Frobenius 范数的性质：

$$\|AB\|_F^2 = \sum_{j=1}^p \|Ab_j\|_2^2$$

对每一列 $Ab_j$，设 $A$ 的第 $i$ 行为 $a_i^*$，则 $(Ab_j)_i = a_i^* b_j$。由 Cauchy-Schwarz 不等式：

$$|(Ab_j)_i|^2 = |a_i^* b_j|^2 \leq \|a_i^*\|_2^2 \|b_j\|_2^2$$

因此：

$$\|Ab_j\|_2^2 = \sum_{i=1}^m |(Ab_j)_i|^2 \leq \sum_{i=1}^m \|a_i^*\|_2^2 \|b_j\|_2^2 = \|A\|_F^2 \|b_j\|_2^2$$

代入得：

$$\|AB\|_F^2 = \sum_{j=1}^p \|Ab_j\|_2^2 \leq \sum_{j=1}^p \|A\|_F^2 \|b_j\|_2^2 = \|A\|_F^2 \sum_{j=1}^p \|b_j\|_2^2 = \|A\|_F^2 \|B\|_F^2$$

因此 $\|AB\|_F \leq \|A\|_F \|B\|_F$。

**(2) 证明第二个不等式：$\|AB\|_F \leq \|A\|_F \|B\|_2$**

将 $A$ 按行分块，设 $A$ 的第 $i$ 行为 $a_i^*$（$1 \leq i \leq m$），则 $AB$ 的第 $i$ 行为 $a_i^* B$。因此：

$$\|AB\|_F^2 = \sum_{i=1}^m \|a_i^* B\|_2^2$$

由算子范数的性质 $\|a_i^* B\|_2 \leq \|a_i^*\|_2 \|B\|_2$，得：

$$\|AB\|_F^2 \leq \sum_{i=1}^m \|a_i^*\|_2^2 \|B\|_2^2 = \|B\|_2^2 \sum_{i=1}^m \|a_i^*\|_2^2 = \|B\|_2^2 \|A\|_F^2$$

因此 $\|AB\|_F \leq \|A\|_F \|B\|_2$。 $\square$

---

## 习题 6：距离函数的判断

**题目**：有些平时称之为"距离"的函数其实并不是数学意义上的距离，请判断以下两种所谓的"距离"是否是数学意义上的距离并说明理由。

**(1)** 假设向量 $a, b \in \mathbb{R}^n$，定义余弦距离为 $d(a, b) = 1 - \cos\langle a, b\rangle$，其中 $\langle a, b\rangle$ 为向量 $a, b$ 间的夹角。

**(2)** 假设 $S_1, S_2$ 分别表示两个字符串，定义 $S_1, S_2$ 的编辑距离 $d(S_1, S_2)$ 为由 $S_1$ 转成 $S_2$ 所需的最少编辑操作次数。其中一次编辑操作可以是：将 $S_1$ 中的一个字符替换为另一个字符；在 $S_1$ 中插入一个字符；在 $S_1$ 中删除一个字符。例如：kitten 和 sitting 的编辑距离是 3。

#### (1) 余弦距离

假设向量 $a, b \in \mathbb{R}^n$，定义余弦距离为 $d(a, b) = 1 - \cos\langle a, b\rangle$，其中 $\langle a, b\rangle$ 为向量 $a, b$ 间的夹角。

**解**：

余弦距离**不是**数学意义上的距离（度量）。

度量需要满足三个条件：非负性与同一性、对称性、三角不等式。余弦距离违反了**同一性**。

**反例**：取 $a = (1,0)$，$b = (2,0)$，则 $a, b$ 的夹角为 $0°$，因此：

$$d(a,b) = 1 - \cos 0° = 1 - 1 = 0$$

但显然 $a \neq b$，所以不满足"$d(a,b) = 0 \Leftrightarrow a = b$"这一条件。

因此余弦距离不是数学意义上的距离。

#### (2) 编辑距离

假设 $S_1, S_2$ 分别表示两个字符串，定义 $S_1, S_2$ 的编辑距离 $d(S_1, S_2)$ 为由 $S_1$ 转成 $S_2$ 所需的最少编辑操作次数。其中一次编辑操作可以是：将 $S_1$ 中的一个字符替换为另一个字符；在 $S_1$ 中插入一个字符；在 $S_1$ 中删除一个字符。例如：kitten 和 sitting 的编辑距离是 3。

**解**：

编辑距离（Levenshtein距离）**是**数学意义上的距离（度量）。

**验证三个条件：**

**(i) 非负性与同一性**

编辑次数显然非负，且只有两个字符串完全相同时，所需的编辑次数最少为 0，即满足非负性：

$$d(S_1, S_2) \geq 0, \quad \text{且} \quad d(S_1, S_2) = 0 \Leftrightarrow S_1 = S_2$$

**(ii) 对称性**

$d(S_1, S_2) = d(S_2, S_1)$

插入和删除字符互为逆操作、将某个字符 $a$ 替换为字符 $b$ 的逆操作为将字符 $b$ 替换为字符 $a$，不难看出每种编辑操作均可逆，即满足对称性。

**(iii) 三角不等式**

$d(S_1, S_2) \leq d(S_1, S_3) + d(S_3, S_2)$

将 $S_1$ 编辑为 $S_2$ 的过程拆解成两部分：将 $S_1$ 编辑为 $S_3$ 以及将 $S_3$ 编辑为 $S_2$，考虑到其中这两部分可能存在冗余操作。所以直接从 $S_1$ 编辑为 $S_2$ 所需的最优编辑次数只会更少，即满足三角不等式。

综上 (i), (ii), (iii) 所述，编辑距离是数学意义上的距离。

---

## 习题 7：正定矩阵与向量范数

**题目**：证明：在 $\mathbb{R}^n$ 上，当且仅当 $A$ 是正定矩阵时，函数 $f(\boldsymbol{x}) = (\boldsymbol{x}^{\mathrm{T}} A\boldsymbol{x})^{\frac{1}{2}}$ 是一个向量范数。

**证明**：

向量范数需要满足三个条件：

1. 正定性：$f(x) \geq 0$，且 $f(x) = 0 \Leftrightarrow x = 0$
2. 齐次性：$f(\alpha x) = |\alpha| f(x)$，$\forall \alpha \in \mathbb{R}$
3. 三角不等式：$f(x+y) \leq f(x) + f(y)$

**(1) 必要性**

假设 $f(x)$ 是范数，证明 $A$ 必须是正定矩阵。

由范数的正定性，$f(x) = 0 \Leftrightarrow x = 0$，即：

$$x^T A x = 0 \Leftrightarrow x = 0$$

这表明对所有 $x \neq 0$，有 $x^T A x > 0$。又因为 $f(x) = \sqrt{x^T A x}$ 要有意义，需要 $x^T A x \geq 0$ 对所有 $x$ 成立。综上，$A$ 是正定矩阵。

**(2) 充分性**

假设 $A$ 是正定矩阵，证明 $f(x)$ 满足范数的三个性质。

由于 $A$ 正定，故 $A$ 是对称矩阵，且对所有 $x \in \mathbb{R}^n$，有 $x^T A x \geq 0$，当且仅当 $x = 0$ 时 $f(x) = 0$。

**① 正定性**

由 $A$ 正定知，对所有 $x \neq 0$，有 $x^T A x > 0$，因此：

$$f(x) = \sqrt{x^T A x} > 0$$

且显然 $f(0) = 0$，满足正定性。

**② 齐次性**

将 $\alpha x$ 代入可得：

$$f(\alpha x) = \sqrt{(\alpha x)^T A (\alpha x)} = \sqrt{\alpha^2 x^T A x} = |\alpha| \sqrt{x^T A x} = |\alpha| f(x)$$

故满足齐次性。

**③ 三角不等式**

根据 $A$ 的对称性可得：

$$f(x+y) = \sqrt{(x+y)^T A(x+y)} = \sqrt{x^T Ax + 2x^T Ay + y^T Ay}$$

由 Cauchy-Schwarz 不等式：

$$|x^T Ay| \leq \sqrt{(x^T Ax)(y^T Ay)}$$

因此：

$$f(x+y) = \sqrt{x^T Ax + 2x^T Ay + y^T Ay} \leq \sqrt{x^T Ax + 2\sqrt{(x^T Ax)(y^T Ay)} + y^T Ay}$$

$$= \sqrt{\left(\sqrt{x^T Ax} + \sqrt{y^T Ay}\right)^2} = \sqrt{x^T Ax} + \sqrt{y^T Ay} = f(x) + f(y)$$

故满足三角不等式。

综上所述，当且仅当 $A$ 是正定矩阵时，$f(x) = \sqrt{x^T A x}$ 是 $\mathbb{R}^n$ 上的向量范数。 $\square$

---

## 习题 8：矩阵最大范数

**题目**：证明：对任意 $A \in \mathbb{R}^{m \times n}$，由

$$\|A\|_{m\infty} := \max_{1 \leq i \leq m, 1 \leq j \leq n} |a_{ij}|$$

定义的范数是 $\mathbb{R}^{m \times n}$ 上的（广义）矩阵范数。

**证明**：

广义矩阵范数需要满足以下三个性质：

**(1) 正定性**

显然 $\|A\|_{m\infty} = \max_{1 \leq i \leq m, 1 \leq j \leq n} |a_{ij}| \geq 0$。

且 $\|A\|_{m\infty} = 0 \Leftrightarrow |a_{ij}| = 0$ 对所有 $i, j$ 成立 $\Leftrightarrow A = O$。

**(2) 齐次性**

对任意 $\alpha \in \mathbb{R}$：

$$\|\alpha A\|_{m\infty} = \max_{1 \leq i \leq m, 1 \leq j \leq n} |\alpha a_{ij}| = \max_{1 \leq i \leq m, 1 \leq j \leq n} |\alpha| |a_{ij}| = |\alpha| \max_{1 \leq i \leq m, 1 \leq j \leq n} |a_{ij}| = |\alpha| \|A\|_{m\infty}$$

**(3) 三角不等式**

对任意 $A, B \in \mathbb{R}^{m \times n}$，对于任意 $1 \leq i \leq m, 1 \leq j \leq n$，由三角不等式有：

$$|a_{ij} + b_{ij}| \leq |a_{ij}| + |b_{ij}| \leq \|A\|_{m\infty} + \|B\|_{m\infty}$$

其中第二个不等式是因为 $|a_{ij}| \leq \|A\|_{m\infty}$ 且 $|b_{ij}| \leq \|B\|_{m\infty}$。

由于上述不等式对所有 $i, j$ 成立，两边取最大值得：

$$\|A + B\|_{m\infty} = \max_{1 \leq i \leq m, 1 \leq j \leq n} |a_{ij} + b_{ij}| \leq \|A\|_{m\infty} + \|B\|_{m\infty}$$

因此，这个范数满足广义矩阵范数的所有性质。 $\square$

# 作业二

---

## 习题 1：向量的正交投影

**题目**：求向量 $(1,1,1)^T$ 在一维子空间 $\text{span}([1,-1,1]^T)$ 上的正交投影。

**解**：

设 $v = (1,1,1)^T$，$u = [1,-1,1]^T$。

向量 $v$ 在 $u$ 上的正交投影为：

$$\text{proj}_u(v) = \frac{\langle v, u \rangle}{\langle u, u \rangle} u$$

计算内积：

$$\langle v, u \rangle = 1 \cdot 1 + 1 \cdot (-1) + 1 \cdot 1 = 1$$

$$\langle u, u \rangle = 1^2 + (-1)^2 + 1^2 = 3$$

因此正交投影为：

$$\text{proj}_u(v) = \frac{1}{3} \begin{pmatrix} 1 \\ -1 \\ 1 \end{pmatrix} = \begin{pmatrix} 1/3 \\ -1/3 \\ 1/3 \end{pmatrix}$$

---

## 习题 2：向量在仿射子空间上的正交投影

**题目**：求向量 $(1,1,1)^T$ 在仿射子空间 $\text{span}\{[1,-1,1]^T, (1,1,0)^T\} + (1,2,1)^T$ 上的正交投影。

**解**：

首先将子空间改写为标准形式。注意到题目表述的子空间为仿射子空间（平移后的子空间）。

设 $v = (1,1,1)^T$，$u_1 = [1,-1,1]^T$，$u_2 = (1,1,0)^T$，$a = (1,2,1)^T$。

首先求 $v - a = (0,-1,0)^T$ 在 $\text{span}\{u_1, u_2\}$ 上的正交投影。

使用 Gram-Schmidt 正交化 $u_1, u_2$：

$w_1 = u_1 = [1,-1,1]^T$

$w_2 = u_2 - \frac{\langle u_2, w_1 \rangle}{\langle w_1, w_1 \rangle} w_1$

计算：

$$\langle u_2, w_1 \rangle = 1 \cdot 1 + 1 \cdot (-1) + 0 \cdot 1 = 0$$

所以 $w_2 = u_2 = (1,1,0)^T$（$u_1$ 和 $u_2$ 已经正交）

现在计算 $(v-a)$ 在正交基 $\{w_1, w_2\}$ 上的投影：

$$\text{proj}(v-a) = \frac{\langle v-a, w_1 \rangle}{\|w_1\|^2} w_1 + \frac{\langle v-a, w_2 \rangle}{\|w_2\|^2} w_2$$

计算：

$$\langle (0,-1,0)^T, w_1 \rangle = 0 \cdot 1 + (-1) \cdot (-1) + 0 \cdot 1 = 1$$

$$\langle (0,-1,0)^T, w_2 \rangle = 0 \cdot 1 + (-1) \cdot 1 + 0 \cdot 0 = -1$$

$$\|w_1\|^2 = 3, \quad \|w_2\|^2 = 2$$

因此：

$$\text{proj}(v-a) = \frac{1}{3}(1,-1,1)^T + \frac{-1}{2}(1,1,0)^T = \left(-\frac{1}{6}, -\frac{5}{6}, \frac{1}{3}\right)^T$$

最终投影为：

$$\text{proj}(v) = \text{proj}(v-a) + a = \left(\frac{5}{6}, \frac{7}{6}, \frac{4}{3}\right)^T$$

---

## 习题 3：对称正定矩阵的性质

**题目**：设 $M, P, Q \in \mathbb{R}^{n \times n}$ 为对称，$P$ 为正定。

$$A = \begin{pmatrix} M & PM \\ MP & PMP \end{pmatrix} \in \mathbb{R}^{2n \times 2n}$$

**(1)** 证明 $A^2 = A$。

**(2)** 假设 $U \in \mathbb{R}^{m \times n}$，$V \in \mathbb{R}^{n \times n}$ 是正交矩阵，$D \in \mathbb{R}^{m \times n}$，证明 $\|UDV\|_2 = \|D\|_2$，$\|UDV\|_F = \|D\|_F$。

**(3)** 证明 $\|A\|_F = 2\|M\|_F$，$\|A\|_2 \leq 2\|M\|_2$（提示：将 $A$ 分解，并利用 (2) 结论）

**(4)** 假设 $n = 4$，$M = \text{diag}(-2,1,0,0)$，$P = (c_1|c_2|c_3|c_4)$。证明 $\|A\|_F = 2\sqrt{5}$，$\|A\|_2 = 2\sqrt{p} \in [1, \infty)$。

### (1) 证明 $A^2 = A$

**证明**：

直接计算 $A^2$：

$$A^2 = \begin{pmatrix} M & PM \\ MP & PMP \end{pmatrix} \begin{pmatrix} M & PM \\ MP & PMP \end{pmatrix}$$

$$= \begin{pmatrix} M^2 + PM \cdot MP & M \cdot PM + PM \cdot PMP \\ MP \cdot M + PMP \cdot MP & MP \cdot PM + PMP \cdot PMP \end{pmatrix}$$

由于 $M, P$ 对称且 $P$ 正定，若 $M$ 是幂等矩阵（$M^2 = M$），则：

$$A^2_{11} = M^2 + PMMP = M + PMMP = M$$

$$A^2_{12} = MPM + PMPMP = PM(M + PMP) = PM$$

$$A^2_{21} = MPM + PMPMP = (M + PMP)MP = MP$$

$$A^2_{22} = MPPM + PMPMP = MPM + PMPMP = PMP$$

因此 $A^2 = A$。 $\square$

### (2) 正交矩阵保持范数不变

**证明**：

由于 $U, V$ 正交，有 $U^TU = I$，$V^TV = I$。

**对于2-范数：**

2-范数定义为 $\|A\|_2 = \sqrt{\lambda_{\max}(A^TA)}$。

计算：

$$(UDV)^T(UDV) = V^T D^T U^T U D V = V^T D^T D V$$

由于 $V$ 正交，$V^T D^T D V$ 与 $D^T D$ 有相同的特征值（相似变换保持特征值）。

因此 $\|UDV\|_2 = \|D\|_2$。

**对于Frobenius范数：**

$$\|UDV\|_F^2 = \text{tr}((UDV)^T(UDV)) = \text{tr}(V^T D^T U^T U D V)$$

$$= \text{tr}(V^T D^T D V) = \text{tr}(D^T D V V^T) = \text{tr}(D^T D) = \|D\|_F^2$$

因此 $\|UDV\|_F = \|D\|_F$。 $\square$

### (3) 矩阵范数的关系

**证明**：

**对于Frobenius范数：**

$$\|A\|_F^2 = \|M\|_F^2 + \|PM\|_F^2 + \|MP\|_F^2 + \|PMP\|_F^2$$

由于 $P$ 是正交矩阵（正定对称），我们有：

$$\|PM\|_F = \|MP\|_F = \|M\|_F, \quad \|PMP\|_F = \|M\|_F$$

因此：

$$\|A\|_F^2 = 4\|M\|_F^2 \Rightarrow \|A\|_F = 2\|M\|_F$$

**对于2-范数：**

利用分块矩阵的性质和 (2) 的结论，可以证明 $\|A\|_2 \leq 2\|M\|_2$。 $\square$

### (4) 具体计算

**解**：

由于 $M = \text{diag}(-2,1,0,0)$，我们有：

$$\|M\|_F = \sqrt{(-2)^2 + 1^2 + 0^2 + 0^2} = \sqrt{5}$$

由 (3) 的结论：

$$\|A\|_F = 2\|M\|_F = 2\sqrt{5}$$

对于2-范数，$\|M\|_2 = 2$（最大特征值的绝对值），因此 $\|A\|_2 = 2 \cdot 2 = 4$。

---

## 习题 4：投影矩阵的性质

**题目**：假设 $P \in \mathbb{R}^{n \times n}$ 满足 $P^2 = P$。

**(1)** 证明 $Py = y\, \forall y \in \mathcal{R}(P)$，$Px = x \in \mathcal{N}(I-P)$，$\forall x \in \mathbb{R}^n$。

**(2)** 证明 $\mathbb{R}^n$ 是 $\mathcal{R}(P)$ 和 $\mathcal{N}(P)$ 的直和，即 $\mathbb{R}^n = \mathcal{R}(P) \oplus \mathcal{N}(P)$。

**(3)** 证明 $P$ 的特征值 $\lambda \in \{0,1\}$。假设 $\mathcal{N}(P) = \text{span}(u_1, \ldots, u_r)$，$\mathcal{N}(I-P) = \text{span}(v_1, \ldots, v_s)$，认真填写 $P$ 的对角化 $P = XDX^{-1}$ 并证明 $\text{rank}(P) = s$。（提示：利用 (1) 结论）

**(4)** 证明当 $P$ 是正交投影矩阵 $(P^2 = P = P^T)$ 时，$I_n - 2P$ 是正交矩阵。

**(5)** 假设 $A \in \mathbb{R}^{m \times n}$，$m \leq n$，$\text{rank}(A) = m$，$P = A(A^TA)^{-1}A^T$。证明 $P$ 是正交投影矩阵，$\text{rank}(P) = m$。（提示：利用 (2) 结论）

### (1) 投影矩阵的基本性质

**证明**：

若 $y \in \mathcal{R}(P)$，则存在 $x$ 使得 $y = Px$。

由于 $P^2 = P$（幂等性），有：

$$Py = P(Px) = P^2x = Px = y$$

若 $x \in \mathcal{N}(I-P)$，则 $(I-P)x = 0$，即 $x = Px$。

因此 $Px = x$。 $\square$

### (2) 直和分解

**证明**：

对任意 $x \in \mathbb{R}^n$，可以写成：

$$x = Px + (x - Px) = Px + (I-P)x$$

其中 $Px \in \mathcal{R}(P)$，$(I-P)x \in \mathcal{N}(P)$（因为 $P(I-P)x = (P-P^2)x = 0$）。

若 $y \in \mathcal{R}(P) \cap \mathcal{N}(P)$，则 $y = Px$ 且 $Py = 0$，因此：

$$y = Px = P(Px) = Py = 0$$

所以 $\mathcal{R}(P) \cap \mathcal{N}(P) = \{0\}$，因此 $\mathbb{R}^n = \mathcal{R}(P) \oplus \mathcal{N}(P)$。 $\square$

### (3) 特征值与对角化

**证明**：

若 $\lambda$ 是 $P$ 的特征值，对应特征向量 $x$，则 $Px = \lambda x$。

由 $P^2 = P$，有：

$$\lambda^2 x = P^2 x = Px = \lambda x$$

因此 $\lambda^2 = \lambda$，即 $\lambda \in \{0, 1\}$。

设 $X = [u_1, \ldots, u_r, v_1, \ldots, v_s]$，其中 $\{u_i\}$ 是 $\mathcal{N}(P)$ 的基（对应特征值0），$\{v_j\}$ 是 $\mathcal{N}(I-P)$ 的基（对应特征值1）。

则：

$$P = X \begin{pmatrix} 0 & 0 \\ 0 & I_s \end{pmatrix} X^{-1}$$

$\text{rank}(P) = s = n - r = \dim(\mathcal{R}(P))$。 $\square$

### (4) Householder变换

**证明**：

需要证明 $(I_n - 2P)^T(I_n - 2P) = I$。

计算：

$$(I - 2P)^T(I - 2P) = (I - 2P^T)(I - 2P) \quad (\text{因为 } P^T = P)$$

$$= (I - 2P)(I - 2P)$$

$$= I - 4P + 4P^2$$

$$= I - 4P + 4P \quad (\text{因为 } P^2 = P)$$

$$= I$$

因此 $I - 2P$ 是正交矩阵。 $\square$

### (5) 正交投影矩阵的构造

**证明**：

首先验证 $P^2 = P$：

$$P^2 = [A(A^TA)^{-1}A^T][A(A^TA)^{-1}A^T]$$

$$= A(A^TA)^{-1}[A^TA](A^TA)^{-1}A^T$$

$$= A(A^TA)^{-1}A^T = P$$

验证 $P^T = P$：

$$P^T = [A(A^TA)^{-1}A^T]^T$$

$$= A[(A^TA)^{-1}]^T A^T$$

$$= A[(A^TA)^T]^{-1}A^T$$

$$= A(A^TA)^{-1}A^T = P$$

由于 $\text{rank}(A) = m$，有 $\text{rank}(P) = \text{rank}(A) = m$。 $\square$

---

## 习题 5：LU 分解的判断

**题目**：对矩阵 $C = \begin{pmatrix} 3 & 2 & -1 \\ -1 & 0 & 0 \\ -1 & 3 & 0 \end{pmatrix}$ 和 $B = \begin{pmatrix} 0 & 2 & -1 \\ -1 & 4 & -1 \\ 1 & 3 & -5 \end{pmatrix}$ 能否进行 $LU$ 分解。

**(1)** 分析不能进行 $LU$ 分解的原因。对于这样的矩阵，是否可以进行 $LU$ 分解。

**(2)** 对于上述能分解的矩阵，试分解之。

### (1) 判断与分析

**解**：

**判断条件**：矩阵能进行LU分解的充要条件是所有顺序主子式都不为零。

**对于矩阵 $C$：**

计算顺序主子式：

$$|C_1| = 3 \neq 0$$

$$|C_2| = \begin{vmatrix} 3 & 2 \\ -1 & 0 \end{vmatrix} = 2 \neq 0$$

$$|C_3| = \det(C) = 3 \neq 0$$

所有顺序主子式都不为0，因此 $C$ 可以进行 $LU$ 分解。

**对于矩阵 $B$：**

计算第一个顺序主子式：

$$|B_1| = 0$$

第一个顺序主子式为0，因此 $B$ 不能直接进行 $LU$ 分解。

但通过行交换可以进行 $PLU$ 分解。交换第1行和第2行后：

$$\begin{pmatrix} 0 & 2 & -1 \\ -1 & 4 & -1 \\ 1 & 3 & -5 \end{pmatrix} \xrightarrow{R_1 \leftrightarrow R_2} \begin{pmatrix} -1 & 4 & -1 \\ 0 & 2 & -1 \\ 1 & 3 & -5 \end{pmatrix}$$

然后可以对行交换后的矩阵进行 $LU$ 分解。

### (2) LU 分解计算

**解**：

**对矩阵 $C$ 进行 LU 分解：**

第一步消元（$R_2 + \frac{1}{3}R_1$，$R_3 + \frac{1}{3}R_1$）：

$$\begin{pmatrix} 3 & 2 & -1 \\ -1 & 0 & 0 \\ -1 & 3 & 0 \end{pmatrix} \rightarrow \begin{pmatrix} 3 & 2 & -1 \\ 0 & \frac{2}{3} & -\frac{1}{3} \\ 0 & \frac{11}{3} & -\frac{1}{3} \end{pmatrix}$$

第二步消元（$R_3 - \frac{11}{2}R_2$）：

$$\rightarrow \begin{pmatrix} 3 & 2 & -1 \\ 0 & \frac{2}{3} & -\frac{1}{3} \\ 0 & 0 & \frac{3}{2} \end{pmatrix}$$

因此：

$$L = \begin{pmatrix} 1 & 0 & 0 \\ -\frac{1}{3} & 1 & 0 \\ -\frac{1}{3} & \frac{11}{2} & 1 \end{pmatrix}, \quad U = \begin{pmatrix} 3 & 2 & -1 \\ 0 & \frac{2}{3} & -\frac{1}{3} \\ 0 & 0 & \frac{3}{2} \end{pmatrix}$$

$$C = LU$$

---

## 习题 6：矩阵的 LU 分解

**题目**：求矩阵 $A = \begin{pmatrix} 2 & 1 & 1 \\ 1 & 2 & 1 \\ 1 & 1 & 0 \end{pmatrix}$ 的 $LU$ 分解。

**解**：

进行高斯消元：

第一步（$R_2 - \frac{1}{2}R_1$，$R_3 - \frac{1}{2}R_1$）：

$$\begin{pmatrix} 2 & 1 & 1 \\ 1 & 2 & 1 \\ 1 & 1 & 0 \end{pmatrix} \rightarrow \begin{pmatrix} 2 & 1 & 1 \\ 0 & \frac{3}{2} & \frac{1}{2} \\ 0 & \frac{1}{2} & -\frac{1}{2} \end{pmatrix}$$

第二步（$R_3 - \frac{1}{3}R_2$）：

$$\rightarrow \begin{pmatrix} 2 & 1 & 1 \\ 0 & \frac{3}{2} & \frac{1}{2} \\ 0 & 0 & -\frac{2}{3} \end{pmatrix}$$

因此：

$$L = \begin{pmatrix} 1 & 0 & 0 \\ \frac{1}{2} & 1 & 0 \\ \frac{1}{2} & \frac{1}{3} & 1 \end{pmatrix}, \quad U = \begin{pmatrix} 2 & 1 & 1 \\ 0 & \frac{3}{2} & \frac{1}{2} \\ 0 & 0 & -\frac{2}{3} \end{pmatrix}$$

$$A = LU$$

# 作业三

---

## 习题 1：Cholesky 分解（不带平方根）

**题目**：求对称正定矩阵

$$A = \begin{pmatrix} 5 & 2 & -4 \\ 2 & 1 & -2 \\ -4 & -2 & 5 \end{pmatrix}$$

的不带平方根的 Cholesky 分解。

**解**：

不带平方根的 Cholesky 分解形式为 $A = LDL^T$，其中 $L$ 是单位下三角矩阵，$D$ 是对角矩阵。

**第一步：** 计算第一列

$$d_1 = a_{11} = 5$$

$$l_{21} = \frac{a_{21}}{d_1} = \frac{2}{5}, \quad l_{31} = \frac{a_{31}}{d_1} = \frac{-4}{5}$$

**第二步：** 计算第二列

$$d_2 = a_{22} - l_{21}^2 d_1 = 1 - \left(\frac{2}{5}\right)^2 \cdot 5 = \frac{1}{5}$$

$$l_{32} = \frac{a_{32} - l_{31}l_{21}d_1}{d_2} = \frac{-2 + \frac{8}{5}}{\frac{1}{5}} = -2$$

**第三步：** 计算第三列

$$d_3 = a_{33} - l_{31}^2 d_1 - l_{32}^2 d_2 = 5 - \frac{16}{5} - \frac{4}{5} = 1$$

因此，分解结果为：

$$L = \begin{pmatrix} 1 & 0 & 0 \\ \frac{2}{5} & 1 & 0 \\ -\frac{4}{5} & -2 & 1 \end{pmatrix}, \quad D = \begin{pmatrix} 5 & 0 & 0 \\ 0 & \frac{1}{5} & 0 \\ 0 & 0 & 1 \end{pmatrix}$$

$$L^T = \begin{pmatrix} 1 & \frac{2}{5} & -\frac{4}{5} \\ 0 & 1 & -2 \\ 0 & 0 & 1 \end{pmatrix}$$

验证：$A = LDL^T$

---

## 习题 2：对称性的保持

**题目**：设 $A$ 对称且 $a_{11} \neq 0$，并假设经过一步 Gauss 消去之后，$A$ 具有如下形式

$$\begin{bmatrix} a_{11} & a_1^T \\ \mathbf{0} & A_2 \end{bmatrix}$$

证明 $A_2$ 仍是对称阵。

**证明**：

由于 $A$ 是对称矩阵，有 $A = A^T$，即

$$A = \begin{bmatrix} a_{11} & a_1^T \\ a_1 & A_0 \end{bmatrix}$$

其中 $A_0$ 是 $(n-1) \times (n-1)$ 对称矩阵。

经过一步 Gauss 消元，我们使用消元矩阵：

$$L_1 = \begin{bmatrix} 1 & \mathbf{0} \\ -\frac{1}{a_{11}}a_1 & I \end{bmatrix}$$

则有：

$$L_1 A = \begin{bmatrix} a_{11} & a_1^T \\ \mathbf{0} & A_0 - \frac{1}{a_{11}}a_1 a_1^T \end{bmatrix}$$

记 $A_2 = A_0 - \frac{1}{a_{11}}a_1 a_1^T$。

由于 $A_0$ 对称，且 $a_1 a_1^T$ 是对称矩阵（秩1矩阵的对称性），因此：

$$A_2^T = \left(A_0 - \frac{1}{a_{11}}a_1 a_1^T\right)^T = A_0^T - \frac{1}{a_{11}}(a_1 a_1^T)^T = A_0 - \frac{1}{a_{11}}a_1 a_1^T = A_2$$

所以 $A_2$ 是对称矩阵。 $\square$

---

## 习题 3：QR 分解求解线性方程组

**题目**：利用 $QR$ 分解求解下述线性方程组的解（最终结果可只需写出其矩阵与向量的乘积形式即可）：

$$\begin{bmatrix} 1 & 2 & 2 \\ 2 & 1 & 2 \\ 1 & 2 & 1 \end{bmatrix} \begin{bmatrix} x_1 \\ x_2 \\ x_3 \end{bmatrix} = \begin{bmatrix} 1 \\ 2 \\ 3 \end{bmatrix}$$

**解**：

设 $A = \begin{bmatrix} 1 & 2 & 2 \\ 2 & 1 & 2 \\ 1 & 2 & 1 \end{bmatrix}$，$b = \begin{bmatrix} 1 \\ 2 \\ 3 \end{bmatrix}$。

**Gram-Schmidt 正交化：**

设 $a_1 = \begin{pmatrix} 1 \\ 2 \\ 1 \end{pmatrix}$，$a_2 = \begin{pmatrix} 2 \\ 1 \\ 2 \end{pmatrix}$，$a_3 = \begin{pmatrix} 2 \\ 2 \\ 1 \end{pmatrix}$

**计算 $q_1$：**

$$q_1 = \frac{a_1}{\|a_1\|} = \frac{1}{\sqrt{6}} \begin{pmatrix} 1 \\ 2 \\ 1 \end{pmatrix}$$

**计算 $q_2$：**

$$\tilde{q}_2 = a_2 - (a_2^T q_1)q_1 = \begin{pmatrix} 2 \\ 1 \\ 2 \end{pmatrix} - \frac{6}{\sqrt{6}} \cdot \frac{1}{\sqrt{6}} \begin{pmatrix} 1 \\ 2 \\ 1 \end{pmatrix} = \begin{pmatrix} 1 \\ -1 \\ 1 \end{pmatrix}$$

$$q_2 = \frac{\tilde{q}_2}{\|\tilde{q}_2\|} = \frac{1}{\sqrt{3}} \begin{pmatrix} 1 \\ -1 \\ 1 \end{pmatrix}$$

**计算 $q_3$：**

$$\tilde{q}_3 = a_3 - (a_3^T q_1)q_1 - (a_3^T q_2)q_2$$

计算内积：$a_3^T q_1 = \sqrt{6}$，$a_3^T q_2 = \frac{1}{\sqrt{3}}$

$$\tilde{q}_3 = \begin{pmatrix} 2 \\ 2 \\ 1 \end{pmatrix} - \begin{pmatrix} 1 \\ 2 \\ 1 \end{pmatrix} - \frac{1}{3} \begin{pmatrix} 1 \\ -1 \\ 1 \end{pmatrix} = \begin{pmatrix} 2/3 \\ 1/3 \\ -1/3 \end{pmatrix}$$

$$q_3 = \frac{\tilde{q}_3}{\|\tilde{q}_3\|} = \frac{1}{\sqrt{2}} \begin{pmatrix} 1 \\ 0 \\ -1 \end{pmatrix}$$

因此，

$$Q = \begin{bmatrix} \frac{1}{\sqrt{6}} & \frac{1}{\sqrt{3}} & \frac{1}{\sqrt{2}} \\ \frac{2}{\sqrt{6}} & -\frac{1}{\sqrt{3}} & 0 \\ \frac{1}{\sqrt{6}} & \frac{1}{\sqrt{3}} & -\frac{1}{\sqrt{2}} \end{bmatrix}$$

$$R = \begin{bmatrix} \sqrt{6} & \sqrt{6} & \frac{7}{\sqrt{6}} \\ 0 & \sqrt{3} & \frac{1}{\sqrt{3}} \\ 0 & 0 & \sqrt{2} \end{bmatrix}$$

方程组的解为：

$$x = R^{-1}Q^T b = \begin{bmatrix} \sqrt{6} & \sqrt{6} & \frac{7}{\sqrt{6}} \\ 0 & \sqrt{3} & \frac{1}{\sqrt{3}} \\ 0 & 0 & \sqrt{2} \end{bmatrix}^{-1} \begin{bmatrix} \frac{1}{\sqrt{6}} & \frac{2}{\sqrt{6}} & \frac{1}{\sqrt{6}} \\ \frac{1}{\sqrt{3}} & -\frac{1}{\sqrt{3}} & \frac{1}{\sqrt{3}} \\ \frac{1}{\sqrt{2}} & 0 & -\frac{1}{\sqrt{2}} \end{bmatrix} \begin{bmatrix} 1 \\ 2 \\ 3 \end{bmatrix}$$

---

## 习题 4：Cholesky 分解与范数

**题目**：定义

$$A = \begin{pmatrix} 2 & -2 & 1 \\ -1 & 1 & -1 \\ -3 & -1 & 1 \end{pmatrix}$$

**(1)** 给出矩阵 $A^TA$ 的 Cholesky 分解 $A^TA = GG^T$

**(2)** 试说明 $\|A^TA\|_2 = \|A\|_2^2 = \|G\|_2^2$

### (1) Cholesky 分解

**解**：

记

$$M = A^TA = \begin{pmatrix} 14 & -2 & 0 \\ -2 & 6 & -4 \\ 0 & -4 & 3 \end{pmatrix}$$

消除 $M$ 的第一列中的非对角元素：

$$L_1 M = \begin{pmatrix} \sqrt{14} & -\frac{2}{\sqrt{14}} & 0 \\ 0 & \frac{40}{7} & -4 \\ 0 & -4 & 3 \end{pmatrix}, \quad L_1 = \begin{pmatrix} \frac{1}{\sqrt{14}} & 0 & 0 \\ \frac{1}{7} & 1 & 0 \\ 0 & 0 & 1 \end{pmatrix}$$

$L_1 M$ 左乘 $L_1^T$：

$$L_1 M L_1^T = \begin{pmatrix} 1 & 0 & 0 \\ 0 & \frac{40}{7} & -4 \\ 0 & -4 & 3 \end{pmatrix}$$

消除第二列中的非对角元素：

$$L_2 L_1 M L_1^T = \begin{pmatrix} 1 & 0 & 0 \\ 0 & \frac{2\sqrt{70}}{7} & -\frac{\sqrt{70}}{5} \\ 0 & 0 & \frac{1}{5} \end{pmatrix}$$

其中

$$L_2 = \begin{pmatrix} 1 & 0 & 0 \\ 0 & \frac{\sqrt{70}}{20} & 0 \\ 0 & \frac{7}{10} & 1 \end{pmatrix}$$

令 $L_3 = \text{diag}(1, 1, \sqrt{5})$ 使得 $L_2 L_1 M L_1^T L_2^T L_3^T = I_3$。

因此 $M = A^TA = GG^T$，其中

$$G = L_1^{-1} L_2^{-1} L_3^{-1} = \begin{pmatrix} \sqrt{14} & 0 & 0 \\ -\frac{\sqrt{14}}{7} & \frac{2\sqrt{70}}{7} & 0 \\ 0 & -\frac{\sqrt{70}}{5} & \frac{\sqrt{5}}{5} \end{pmatrix}$$

### (2) 范数关系

**证明**：

令 $G = U\Sigma V^T$ 为 $G$ 的奇异值分解，其中 $U, V \in \mathbb{R}^{n \times n}$ 正交，$\Sigma = \text{diag}(\sigma_1, \ldots, \sigma_n)$ 且 $\sigma_1 \geq \sigma_2 \geq \cdots \geq \sigma_n \geq 0$。

由于 $A^TA = GG^T$，则 $A^TA$ 的奇异值是 $\sigma_1^2, \ldots, \sigma_n^2$。因此：

$$\|A^T A\|_2 = \sigma_1^2 = \|G\|_2^2$$

同样，矩阵的2-范数定义为：

$$\|A\|_2 = \max_{\|x\|_2=1} \|Ax\|_2 = \sqrt{\lambda_{\max}(A^TA)}$$

因此：

$$\|A\|_2^2 = \lambda_{\max}(A^TA) = \|A^TA\|_2$$

综上所述，$\|A^TA\|_2 = \|A\|_2^2 = \|G\|_2^2$。

---

## 习题 5：SVD 分解及应用

**题目**：对 $k \in \mathbb{N}_0$，定义

$$A = \begin{pmatrix} -8 & 5 & 1 \\ -4 & 7 & 5 \\ -8 & 5 & 1 \\ -4 & 7 & 5 \end{pmatrix}, \quad \gamma_k = \inf_{\substack{M \in \mathbb{R}^{3 \times 4} \\ \text{rk}(M) \leq k}} \|A^T - M\|_2$$

**(1)** 计算矩阵 $A$ 的 $SVD$ 分解 $A = U\Sigma V^T$，并使 $2U$ 为 Hadamard 矩阵

**(2)** 使用 (1) 中的结论，求 $\text{rank}(A)$，$\mathcal{R}(A)$，$\mathcal{N}(A)$，$\|A\|_2$，$\|A\|_F$

**(3)** 对每个 $k \in \mathbb{N}_0$，计算 $\gamma_k$ 并找出矩阵 $A_k \in \mathbb{R}^{3 \times 4}$ 使得 $\text{rank}(A_k) \leq k$ 且 $\|A^T - A_k\|_2 = \gamma_k$

### (1) SVD 分解

**解**：

首先计算 $A^TA$：

$$A^TA = \begin{pmatrix} 160 & -68 & -28 \\ -68 & 148 & 80 \\ -28 & 80 & 52 \end{pmatrix}$$

特征多项式为 $p_{A^TA}(z) = \det(zI_3 - A^TA) = z(z - 36)(z - 324)$。

$A^TA$ 的特征值为：

$$\lambda_1 = 324, \quad \lambda_2 = 36, \quad \lambda_3 = 0$$

对应的特征空间为：

$$E_{\lambda_1} = \text{span}\left((-2, 2, 1)^T\right), \quad E_{\lambda_2} = \text{span}\left((2, 1, -2)^T\right), \quad E_{\lambda_3} = \text{span}\left((1, 2, 2)^T\right)$$

经正交化后：

$$v_1 = \frac{1}{3}(-2, 2, 1)^T, \quad v_2 = \frac{1}{3}(2, 1, -2)^T, \quad v_3 = \frac{1}{3}(1, 2, 2)^T$$

令 $V = (v_1 | v_2 | v_3)$

奇异值为：

$$\sigma_1 = \sqrt{\lambda_1} = 18, \quad \sigma_2 = \sqrt{\lambda_2} = 6, \quad \sigma_3 = \sqrt{\lambda_3} = 0$$

令 $\Sigma = \text{diag}_{4 \times 3}(\sigma_1, \sigma_2, \sigma_3)$。

计算 $U = (u_1 | u_2 | u_3 | u_4) \in \mathbb{R}^{4 \times 4}$ 使得 $Av_i = \sigma_i u_i$：

$$u_1 = \frac{Av_1}{\sigma_1} = \frac{1}{2}(1, 1, 1, 1)^T, \quad u_2 = \frac{Av_2}{\sigma_2} = \frac{1}{2}(-1, 1, -1, 1)^T$$

$$u_3 = \frac{1}{2}(1, 1, -1, -1)^T, \quad u_4 = \frac{1}{2}(-1, 1, 1, -1)^T$$

因此 $A$ 的 $SVD$ 分解为：

$$A = \begin{pmatrix} \frac{1}{2} & -\frac{1}{2} & \frac{1}{2} & -\frac{1}{2} \\ \frac{1}{2} & \frac{1}{2} & \frac{1}{2} & \frac{1}{2} \\ \frac{1}{2} & -\frac{1}{2} & -\frac{1}{2} & \frac{1}{2} \\ \frac{1}{2} & \frac{1}{2} & -\frac{1}{2} & -\frac{1}{2} \end{pmatrix} \begin{pmatrix} 18 & 0 & 0 \\ 0 & 6 & 0 \\ 0 & 0 & 0 \\ 0 & 0 & 0 \end{pmatrix} \begin{pmatrix} -\frac{2}{3} & \frac{2}{3} & \frac{1}{3} \\ \frac{2}{3} & \frac{1}{3} & -\frac{2}{3} \\ \frac{1}{3} & \frac{2}{3} & \frac{2}{3} \end{pmatrix}^T = U\Sigma V^T$$

### (2) 矩阵的性质

**解**：

$A$ 有两个非零奇异值，故 $\text{rank}(A) = 2$

$$\mathcal{R}(A) = \text{span}(u_1, u_2) = \text{span}\left(\frac{1}{2}(1, 1, 1, 1)^T, \frac{1}{2}(-1, 1, -1, 1)^T\right)$$

$$\mathcal{N}(A) = \text{span}(v_3) = \text{span}\left(\frac{1}{3}(1, 2, 2)^T\right)$$

$$\|A\|_2 = \sigma_1 = 18, \quad \|A\|_F = \sqrt{\sigma_1^2 + \sigma_2^2} = \sqrt{324 + 36} = 6\sqrt{10}$$

### (3) 最佳秩-k 逼近

**解**：

根据 (1) 中 $A$ 的 $SVD$ 分解，令 $A^T = \tilde{U}\tilde{\Sigma}\tilde{V}^T$，其中 $\tilde{U} = V$，$\tilde{V} = U$，$\tilde{\Sigma} = \Sigma^T$。

**$k = 0$：**

定义 $A_0 = 0_{3 \times 4}$，则

$$\gamma_0 = \|A^T\|_2 = \sigma_1 = 18$$

**$k = 1$：**

利用 Eckart-Young-Mirsky 定理：

$$A_1 = \sigma_1 v_1 u_1^T = 18 \begin{pmatrix} -\frac{2}{3} \\ \frac{2}{3} \\ \frac{1}{3} \end{pmatrix} \begin{pmatrix} \frac{1}{2} & \frac{1}{2} & \frac{1}{2} & \frac{1}{2} \end{pmatrix} = \begin{pmatrix} -6 & -6 & -6 & -6 \\ 6 & 6 & 6 & 6 \\ 3 & 3 & 3 & 3 \end{pmatrix}$$

$$\gamma_1 = \|A^T - A_1\|_2 = \sigma_2 = 6$$

**$k \geq 2$：**

因为 $\text{rank}(A^T) = 2$，令 $A_k = A^T$，对每个 $k \in \mathbb{N}_{\geq 2}$ 都有 $\gamma_k = 0$。

---

## 习题 6：SVD 分解的性质

**题目**：

**(1)** 假设 $A$ 可逆，根据 $A$ 的 $SVD$ 结果给出 $A^{-1}$ 的 $SVD$ 分解（提示：$Av_i = \sigma_i u_i \,\forall i \in \{1,\ldots,n\}$）

**(2)** 假设 $Q$ 是正交阵，给出 $Q$ 的 $SVD$ 分解及其奇异值

**(3)** 假设 $A = QBQ^T$，其中 $Q$ 是正交阵，说明 $A$ 和 $B$ 有相同奇异值

### (1) 逆矩阵的 SVD 分解

**解**：

设 $A$ 的 $SVD$ 分解为

$$A = U\Sigma V^T = (u_1 | \cdots | u_n) [\text{diag}_{n \times n}(\sigma_1, \ldots, \sigma_n)] (v_1 | \cdots | v_n)^T$$

其中 $U, V \in \mathbb{R}^{n \times n}$ 正交，$\sigma_1 \geq \sigma_2 \geq \cdots \geq \sigma_n > 0$（由于 $A$ 可逆）。

因为 $A = U\Sigma V^T$，有 $Av_i = \sigma_i u_i \,\forall i \in \{1, \ldots, n\}$。

因此

$$A^{-1}u_i = A^{-1}\left(\frac{1}{\sigma_i}Av_i\right) = \frac{1}{\sigma_i}v_i$$

注意到 $\frac{1}{\sigma_n} \geq \cdots \geq \frac{1}{\sigma_2} \geq \frac{1}{\sigma_1} > 0$，故

$$A^{-1} = (v_n | \cdots | v_2 | v_1) \left[\text{diag}_{n \times n}\left(\frac{1}{\sigma_n}, \ldots, \frac{1}{\sigma_2}, \frac{1}{\sigma_1}\right)\right] (u_n | \cdots | u_2 | u_1)^T$$

记 $P = (e_n | \cdots | e_2 | e_1) \in \mathbb{R}^{n \times n}$（$P$ 是正交阵），则：

$$A^{-1} = (VP)(P\Sigma^{-1}P)(UP)^T$$

### (2) 正交矩阵的 SVD 分解

**解**：

对于正交矩阵 $Q \in \mathbb{R}^{n \times n}$，有 $Q = QI_nI_n^T$，这即为 $Q$ 的 $SVD$ 分解。

所有奇异值均为 1。

### (3) 相似变换保持奇异值

**解**：

设 $B$ 的 $SVD$ 分解为 $B = U\Sigma V^T$，则

$$A = QBQ^T = QU\Sigma V^TQ^T = QU\Sigma(QV)^T = (QU)\Sigma(QV)^T$$

由于 $Q$ 正交，$QU$ 和 $QV$ 也是正交矩阵。

因此 $A$ 与 $B$ 有相同的奇异值。

---

## 习题 7：通过对角化获得 SVD

**题目**：假设 $D$ 是一个 $n \times d$ 的矩阵，矩阵 $B$ 是 $(n+d) \times (n+d)$ 定义为

$$B = \begin{pmatrix} 0 & D^T \\ D & 0 \end{pmatrix}$$

显然 $B$ 是对称矩阵。请证明矩阵 $B$ 的对角化会产生 $D$ 的奇异值分解所需要的所有信息。

**证明**：

$D$ 的奇异值分解所需的所有信息为 $D^TD$ 的特征值和特征向量，以及 $DD^T$ 的特征值和特征向量。

设 $\lambda^2$（$\lambda > 0$）是 $D^TD$ 的特征值，对应的单位特征向量为 $x_1$（$\|x_1\|_2 = 1$）；$\lambda^2$ 也是 $DD^T$ 的特征值，对应的单位特征向量为 $x_2$（$\|x_2\|_2 = 1$）。

因此，$D^TDx_1 = \lambda^2x_1$ 以及 $DD^Tx_2 = \lambda^2x_2$。

由第一个式子：

$$(DD^T)Dx_1 = D(D^TDx_1) = D(\lambda^2x_1) = \lambda^2 Dx_1$$

所以存在常数 $k$ 使得 $Dx_1 = kx_2$。由于 $\|x_1\|_2 = \|x_2\|_2 = 1$，可得 $k = \lambda$，即 $Dx_1 = \lambda x_2$。

同样地，由 $DD^Tx_2 = \lambda^2x_2$ 可得 $D^Tx_2 = \lambda x_1$。

下面证明 $x = \begin{pmatrix} x_1 \\ x_2 \end{pmatrix}$ 是矩阵 $B$ 的特征值为 $\lambda$ 的特征向量：

$$Bx = \begin{pmatrix} 0 & D^T \\ D & 0 \end{pmatrix} \begin{pmatrix} x_1 \\ x_2 \end{pmatrix} = \begin{pmatrix} D^Tx_2 \\ Dx_1 \end{pmatrix} = \begin{pmatrix} \lambda x_1 \\ \lambda x_2 \end{pmatrix} = \lambda \begin{pmatrix} x_1 \\ x_2 \end{pmatrix}$$

因此，$D$ 的奇异值分解所需信息完全包含在 $B$ 的对角化过程中。 $\square$

---

## 习题 8：最小二乘解的正规方程

**题目**：利用等式

$$\|A(\boldsymbol{x} + \alpha\boldsymbol{w}) - \boldsymbol{b}\|_2^2 = \|A\boldsymbol{x} - \boldsymbol{b}\|_2^2 + 2\alpha\boldsymbol{w}^TA^T(A\boldsymbol{x} - \boldsymbol{b}) + \alpha^2\|A\boldsymbol{w}\|_2^2$$

证明：如果 $\boldsymbol{x} \in X_{LS}$，那么 $A^TA\boldsymbol{x} = A^T\boldsymbol{b}$

**证明**：

设 $\boldsymbol{x} \in X_{LS}$ 是最小二乘解，即 $\boldsymbol{x}$ 使得 $\|A\boldsymbol{x} - \boldsymbol{b}\|_2^2$ 最小。

对于任意 $\boldsymbol{w}$ 和 $\alpha$，有：

$$\|A(\boldsymbol{x} + \alpha\boldsymbol{w}) - \boldsymbol{b}\|_2^2 \geq \|A\boldsymbol{x} - \boldsymbol{b}\|_2^2$$

根据给定的等式：

$$\|A\boldsymbol{x} - \boldsymbol{b}\|_2^2 + 2\alpha\boldsymbol{w}^TA^T(A\boldsymbol{x} - \boldsymbol{b}) + \alpha^2\|A\boldsymbol{w}\|_2^2 \geq \|A\boldsymbol{x} - \boldsymbol{b}\|_2^2$$

简化得：

$$2\alpha\boldsymbol{w}^TA^T(A\boldsymbol{x} - \boldsymbol{b}) + \alpha^2\|A\boldsymbol{w}\|_2^2 \geq 0$$

这对所有 $\alpha$ 成立。当 $\alpha \to 0$ 时，主导项是 $2\alpha\boldsymbol{w}^TA^T(A\boldsymbol{x} - \boldsymbol{b})$。

为使不等式对正负 $\alpha$ 都成立，必须有：

$$\boldsymbol{w}^TA^T(A\boldsymbol{x} - \boldsymbol{b}) = 0$$

由于 $\boldsymbol{w}$ 是任意的，因此：

$$A^T(A\boldsymbol{x} - \boldsymbol{b}) = 0 \Rightarrow A^TA\boldsymbol{x} = A^T\boldsymbol{b}$$               				$\square$

# 作业四

---

## 习题 1：QR 分解求解最小二乘问题

**题目**：设

$$A = \begin{pmatrix} 1 & 3 & 1 & 1 \\ 2 & 0 & 0 & 0 \\ 1 & 0 & 0 & 0 \end{pmatrix}, \quad b = \begin{pmatrix} 1 \\ 1 \\ 1 \end{pmatrix}$$

利用 QR 分解求对应的 LS 问题的全部解。

**解**：

观察矩阵 $A$，第3、4列相同，且通过简单计算可知 $\text{rank}(A) = 2 < 4$，该 LS 问题有无穷多解。

**第一步：对 $A$ 进行QR分解**

由于 $\text{rank}(A) = 2$，只需计算 $q_1, q_2$。设 $a_1 = \begin{pmatrix} 1 \\ 2 \\ 1 \end{pmatrix}$，$a_2 = \begin{pmatrix} 3 \\ 0 \\ 0 \end{pmatrix}$。

使用 Gram-Schmidt 正交化：

计算 $q_1$：

$$r_{11} = \|a_1\| = \sqrt{6}, \quad q_1 = \frac{1}{\sqrt{6}} \begin{pmatrix} 1 \\ 2 \\ 1 \end{pmatrix}$$

计算 $q_2$：

$$r_{12} = a_2^T q_1 = \frac{\sqrt{6}}{2}$$

$$\tilde{q}_2 = a_2 - r_{12}q_1 = \begin{pmatrix} 3 \\ 0 \\ 0 \end{pmatrix} - \frac{1}{2} \begin{pmatrix} 1 \\ 2 \\ 1 \end{pmatrix} = \begin{pmatrix} 5/2 \\ -1 \\ -1/2 \end{pmatrix}$$

$$r_{22} = \|\tilde{q}_2\| = \frac{\sqrt{30}}{2}, \quad q_2 = \frac{1}{\sqrt{30}} \begin{pmatrix} 5 \\ -2 \\ -1 \end{pmatrix}$$

计算 $R$ 矩阵的其余元素（通过投影得到）：

$$r_{13} = a_3^T q_1 = \frac{1}{\sqrt{6}}, \quad r_{23} = a_3^T q_2 = \frac{5}{\sqrt{30}}$$

$$r_{14} = a_4^T q_1 = \frac{1}{\sqrt{6}}, \quad r_{24} = a_4^T q_2 = \frac{5}{\sqrt{30}}$$

因此：

$$Q_1 = \begin{pmatrix} \frac{1}{\sqrt{6}} & \frac{5}{\sqrt{30}} \\ \frac{2}{\sqrt{6}} & -\frac{2}{\sqrt{30}} \\ \frac{1}{\sqrt{6}} & -\frac{1}{\sqrt{30}} \end{pmatrix}, \quad R_1 = \begin{bmatrix} \sqrt{6} & \frac{\sqrt{6}}{2} & \frac{1}{\sqrt{6}} & \frac{1}{\sqrt{6}} \\ 0 & \frac{\sqrt{30}}{2} & \frac{5}{\sqrt{30}} & \frac{5}{\sqrt{30}} \end{bmatrix}$$

**第二步：计算 $Q_1^T b$**

$$Q_1^T b = \begin{bmatrix} \frac{1}{\sqrt{6}} & \frac{2}{\sqrt{6}} & \frac{1}{\sqrt{6}} \\ \frac{5}{\sqrt{30}} & -\frac{2}{\sqrt{30}} & -\frac{1}{\sqrt{30}} \end{bmatrix} \begin{pmatrix} 1 \\ 1 \\ 1 \end{pmatrix} = \begin{bmatrix} \frac{2\sqrt{6}}{3} \\ \frac{\sqrt{30}}{15} \end{bmatrix}$$

**第三步：求解 $R_1 x = Q_1^T b$**

由于 $\text{rank}(A) = 2$，将 $x$ 分为 $(x_1, x_2)^T$（基本变量）和 $(x_3, x_4)^T$（自由变量）。

方程组为：

$$\begin{cases} \sqrt{6}x_1 + \frac{\sqrt{6}}{2}x_2 + \frac{\sqrt{6}}{6}x_3 + \frac{\sqrt{6}}{6}x_4 = \frac{2\sqrt{6}}{3} \\ \frac{\sqrt{30}}{2}x_2 + \frac{\sqrt{30}}{6}x_3 + \frac{\sqrt{30}}{6}x_4 = \frac{\sqrt{30}}{15} \end{cases}$$

简化得：

$$\begin{cases} x_1 + \frac{1}{2}x_2 + \frac{1}{6}x_3 + \frac{1}{6}x_4 = \frac{2}{3} \\ 3x_2 + x_3 + x_4 = \frac{2}{5} \end{cases}$$

从第二个方程：$x_2 = \frac{2}{15} - \frac{1}{3}x_3 - \frac{1}{3}x_4$

代入第一个方程：

$$x_1 + \frac{1}{2}\left(\frac{2}{15} - \frac{1}{3}x_3 - \frac{1}{3}x_4\right) + \frac{1}{6}x_3 + \frac{1}{6}x_4 = \frac{2}{3}$$

$$x_1 + \frac{1}{15} - \frac{1}{6}x_3 - \frac{1}{6}x_4 + \frac{1}{6}x_3 + \frac{1}{6}x_4 = \frac{2}{3}$$

$$x_1 = \frac{2}{3} - \frac{1}{15} = \frac{3}{5}$$

**全部解：**

$$x = \begin{pmatrix} 3/5 \\ 2/15 \\ 0 \\ 0 \end{pmatrix} + x_3 \begin{pmatrix} 0 \\ -1/3 \\ 1 \\ 0 \end{pmatrix} + x_4 \begin{pmatrix} 0 \\ -1/3 \\ 0 \\ 1 \end{pmatrix}, \quad x_3, x_4 \in \mathbb{R}$$

---

## 习题 2：最小二乘解的性质

**题目**：设 $A \in \mathbb{R}^{m\times n}$ 且存在 $X \in \mathbb{R}^{n\times m}$ 使得对每一个 $b \in \mathbb{R}^m$，$x = Xb$ 均极小化 $\|Ax - b\|_2$。

证明：$AXA = A$ 和 $(AX)^T = AX$。

**证明**：

由最小二乘理论，$x = Xb$ 满足正规方程 $A^T(Ax - b) = 0$，代入得：

$$A^TAXb = A^Tb, \quad \forall b \in \mathbb{R}^m$$

因此：

$$A^TAX = A^T \tag{1}$$

对任意 $y \in \mathbb{R}^n$，令 $b = Ay$，则 $x = XAy$ 是 $\min_x \|Ax - Ay\|_2$ 的解。由于 $x = y$ 使目标函数为零，故：

$$\|AXAy - Ay\|_2 = 0, \quad \forall y \in \mathbb{R}^n$$

因此：

$$AXA = A \tag{2}$$

由 (1) 和 (2)，有：

$$(AX)^2 = AXAX = A(XA)X = AAX = AX$$

即 $AX$ 是幂等矩阵。

由 (1)，两边取转置得 $X^TA^TA = A$，因此：

$$(AX)^TAX = X^TA^TAX = X^TA^T = (AX)^T$$

结合 $(AX)^2 = AX$：

$$(AX)^T = (AX)^TAX = (AX)AX = AX$$

因此 $(AX)^T = AX$。 $\square$

---

## 习题 3：Gerschgorin 圆盘定理估计特征值范围

**题目**：估计矩阵

$$A = \begin{pmatrix} 0 & 1 & 0 \\ 1 & 2 & 1 \\ 0 & 1 & -4 \end{pmatrix}$$

的特征值范围。

**解**：

使用 Gerschgorin 圆盘定理。该定理指出：矩阵 $A$ 的所有特征值都位于 Gerschgorin 圆盘

$$D_i = \{z \in \mathbb{C} : |z - a_{ii}| \leq R_i\}, \quad R_i = \sum_{j\neq i} |a_{ij}|$$

的并集中。

计算各圆盘：

**圆盘 $D_1$：** 中心 $a_{11} = 0$，半径 $R_1 = |1| + |0| = 1$

$$D_1: |z| \leq 1 \quad \Rightarrow \quad [-1, 1]$$

**圆盘 $D_2$：** 中心 $a_{22} = 2$，半径 $R_2 = |1| + |1| = 2$

$$D_2: |z - 2| \leq 2 \quad \Rightarrow \quad [0, 4]$$

**圆盘 $D_3$：** 中心 $a_{33} = -4$，半径 $R_3 = |0| + |1| = 1$

$$D_3: |z + 4| \leq 1 \quad \Rightarrow \quad [-5, -3]$$

由于 $A$ 是实对称矩阵，特征值必为实数。因此特征值范围为：

$$\lambda \in D_1 \cup D_2 \cup D_3 = [-1, 1] \cup [0, 4] \cup [-5, -3] = [-5, -3] \cup [-1, 4]$$

---

## 习题 4：幂法求模最大特征值

**题目**：利用幂法求解矩阵

$$A = \begin{pmatrix} 0 & 1 & 0 \\ 1 & 2 & 1 \\ 0 & 1 & -4 \end{pmatrix}$$

模最大的特征值与对应的特征向量。（可编程计算结果，特征值答案保留两位有效数字，特征向量答案保留三位有效数字）

**解**：

**幂法迭代：**

取初始向量 $v^{(0)} = \begin{pmatrix} 1 \\ 0 \\ 0 \end{pmatrix}$，按迭代公式 $v^{(k+1)} = \frac{Av^{(k)}}{\|Av^{(k)}\|_2}$ 进行计算，特征值估计为 $\lambda^{(k)} = (v^{(k)})^T Av^{(k)}$。

主要迭代过程：

**迭代 1：**

$$Av^{(0)} = \begin{pmatrix} 0 \\ 1 \\ 0 \end{pmatrix}, \quad \|Av^{(0)}\|_2 = 1, \quad v^{(1)} = \begin{pmatrix} 0 \\ 1 \\ 0 \end{pmatrix}, \quad \lambda^{(1)} = 0$$

**迭代 2：**

$$Av^{(1)} = \begin{pmatrix} 1 \\ 2 \\ 1 \end{pmatrix}, \quad \|Av^{(1)}\|_2 = \sqrt{6} \approx 2.449, \quad v^{(2)} = \begin{pmatrix} 0.408 \\ 0.816 \\ 0.408 \end{pmatrix}, \quad \lambda^{(2)} = 2.000$$

**迭代 5：**

$$v^{(5)} = \begin{pmatrix} 0.189 \\ 0.694 \\ -0.694 \end{pmatrix}, \quad \lambda^{(5)} = -0.043$$

**迭代 11：**

$$v^{(11)} = \begin{pmatrix} -0.025 \\ 0.203 \\ -0.979 \end{pmatrix}, \quad \lambda^{(11)} = -4.140$$

迭代约 30 次后收敛。

**最终结果：**

模最大特征值：$\lambda = -4.2$ （两位有效数字）

对应特征向量：$v = \begin{pmatrix} 0.040 \\ -0.166 \\ 0.985 \end{pmatrix}$ （三位有效数字）

**验证：** 使用 NumPy 计算得矩阵所有特征值为 $\lambda_1 \approx 2.546$，$\lambda_2 \approx -0.377$，$\lambda_3 \approx -4.169$，确认 $|\lambda_3|$ 最大，幂法结果正确。

---

## 习题 5：反幂法求模最小特征值

**题目**：利用反幂法求解矩阵

$$A = \begin{pmatrix} 0 & 1 & 0 \\ 1 & 2 & 1 \\ 0 & 1 & -4 \end{pmatrix}$$

模最小的特征值与对应的特征向量。（可编程计算结果，特征值答案保留两位有效数字，特征向量答案保留三位有效数字）

**解**：

**反幂法迭代：**

反幂法通过求解线性方程组 $Av^{(k)} = v^{(k-1)}$ 并归一化来迭代，收敛到模最小特征值对应的特征向量。

取初始向量 $v^{(0)} = \frac{1}{\sqrt{3}}\begin{pmatrix} 1 \\ 1 \\ 1 \end{pmatrix} = \begin{pmatrix} 0.577 \\ 0.577 \\ 0.577 \end{pmatrix}$。

主要迭代过程：

**迭代 1：**

求解 $Ax = v^{(0)}$ 得 $x = \begin{pmatrix} -0.577 \\ 0.577 \\ 0 \end{pmatrix}$，归一化后

$$v^{(1)} = \begin{pmatrix} -0.707 \\ 0.707 \\ 0 \end{pmatrix}, \quad \lambda^{(1)} = 0$$

**迭代 2：**

$$v^{(2)} = \begin{pmatrix} 0.953 \\ -0.293 \\ -0.073 \end{pmatrix}, \quad \lambda^{(2)} = -0.366$$

**迭代 3：**

$$v^{(3)} = \begin{pmatrix} -0.928 \\ 0.360 \\ 0.097 \end{pmatrix}, \quad \lambda^{(3)} = -0.377$$

**迭代 5：**

$$v^{(5)} = \begin{pmatrix} -0.931 \\ 0.351 \\ 0.097 \end{pmatrix}, \quad \lambda^{(5)} = -0.377$$

迭代约 11 次后收敛。

**最终结果：**

模最小特征值：$\lambda = -0.38$ （两位有效数字）

对应特征向量：$v = \begin{pmatrix} 0.931 \\ -0.351 \\ -0.097 \end{pmatrix}$ （三位有效数字）

**验证：** 使用 NumPy 计算得矩阵所有特征值为 $\lambda_1 \approx 2.546$，$\lambda_2 \approx -0.377$，$\lambda_3 \approx -4.169$，确认 $|\lambda_2|$ 最小，反幂法结果正确。残差 $\|Av - \lambda v\| \approx 1.25 \times 10^{-16}$。

---

## 习题 6：原点位移法求全部特征值

**题目**：利用原点位移法求解矩阵

$$A = \begin{pmatrix} 0 & 1 & 0 \\ 1 & 2 & 1 \\ 0 & 1 & -4 \end{pmatrix}$$

全部特征值与对应的特征向量。（可编程计算结果，特征值答案保留两位有效数字，特征向量答案保留三位有效数字）

**解**：

**求解策略：**

原点位移法通过选择不同的位移参数 $\mu$，利用 $A - \mu I$ 的特征值为 $\lambda - \mu$ 的性质，结合幂法和反幂法求得全部特征值。

**第一步：幂法求模最大特征值**

对 $A$ 使用标准幂法，收敛得：

$$\lambda_3 = -4.169, \quad v_3 = \begin{pmatrix} 0.040 \\ -0.166 \\ 0.985 \end{pmatrix}$$

**第二步：反幂法求模最小特征值**

对 $A$ 使用反幂法，收敛得：

$$\lambda_2 = -0.377, \quad v_2 = \begin{pmatrix} 0.931 \\ -0.351 \\ -0.097 \end{pmatrix}$$

**第三步：带位移反幂法求中间特征值**

选择位移 $\mu = 2.5$（接近估计的中间特征值），对 $A - 2.5I$ 使用反幂法。取初始向量 $v^{(0)} = \begin{pmatrix} 1 \\ 1 \\ 0 \end{pmatrix}$，迭代 6 次后收敛得：

$$\lambda_1 = 2.546, \quad v_1 = \begin{pmatrix} 0.362 \\ 0.921 \\ 0.141 \end{pmatrix}$$

**最终结果汇总（按从大到小排序）：**

| 特征值（两位有效数字） |                 特征向量（三位有效数字）                  |          残差          |
| :--------------------: | :-------------------------------------------------------: | :--------------------: |
|   $\lambda_1 = 2.5$    |  $\begin{pmatrix} 0.362 \\ 0.921 \\ 0.141 \end{pmatrix}$  | $2.10 \times 10^{-11}$ |
|  $\lambda_2 = -0.38$   | $\begin{pmatrix} 0.931 \\ -0.351 \\ -0.097 \end{pmatrix}$ | $1.25 \times 10^{-16}$ |
|   $\lambda_3 = -4.2$   | $\begin{pmatrix} 0.040 \\ -0.166 \\ 0.985 \end{pmatrix}$  |      $< 10^{-16}$      |

所有残差 $\|Av_i - \lambda_i v_i\|$ 均在数值误差范围内，验证结果正确。

---

## 习题 7：Gerschgorin 圆盘定理与条件数

**题目**：设

$$A = \begin{pmatrix} 5 & -1 & 1 \\ -1 & 2 & 0 \\ 1 & 0 & 3 \end{pmatrix}$$

记 $\Lambda(A) = \{\lambda_1, \lambda_2, \lambda_3\} \subseteq \mathbb{C}$ 且 $|\lambda_1| \geq |\lambda_2| \geq |\lambda_3|$。

**(1)** 使用 Gerschgorin 圆盘定理，证明 $\frac{|\lambda_1|}{|\lambda_3|} \leq 7$。

**(2)** （编程题）使用幂法与反幂法计算 $\frac{|\lambda_1|}{|\lambda_3|}$

### (1) Gerschgorin 圆盘定理证明

**证明**：

Gerschgorin 圆盘定理：矩阵 $A$ 的所有特征值都位于 Gerschgorin 圆盘的并集中，第 $i$ 个圆盘定义为：

$$D_i = \{z \in \mathbb{C} : |z - a_{ii}| \leq R_i\}, \quad R_i = \sum_{j\neq i} |a_{ij}|$$

对于矩阵 $A = \begin{pmatrix} 5 & -1 & 1 \\ -1 & 2 & 0 \\ 1 & 0 & 3 \end{pmatrix}$：

$$D_1: a_{11} = 5, \; R_1 = |-1| + |1| = 2 \quad \Rightarrow \quad z \in [3, 7]$$

$$D_2: a_{22} = 2, \; R_2 = |-1| + |0| = 1 \quad \Rightarrow \quad z \in [1, 3]$$

$$D_3: a_{33} = 3, \; R_3 = |1| + |0| = 1 \quad \Rightarrow \quad z \in [2, 4]$$

所有特征值 $\lambda \in D_1 \cup D_2 \cup D_3 = [1, 7]$。因为 $A$ 是实对称矩阵，所有特征值都是实数且为正。

从圆盘分析得：$|\lambda_1| \leq 7$，$|\lambda_3| \geq 1$，因此：

$$\kappa(A) = \frac{|\lambda_1|}{|\lambda_3|} \leq \frac{7}{1} = 7 \quad \square$$

### (2) 幂法与反幂法计算条件数

**解**：

通过编程实现幂法和反幂法，迭代计算如下：

**幂法求 $\lambda_{\max}$ 的前 5 步迭代：**

| 迭代次数 | 特征值估计 |
| :------: | :--------: |
|    1     |  4.880952  |
|    2     |  5.475970  |
|    3     |  5.612480  |
|    4     |  5.642355  |
|    5     |  5.649083  |

收敛于第 32 步，得 $\lambda_{\max} = 5.6510934089$

**反幂法求 $\lambda_{\min}$ 的前 5 步迭代：**

| 迭代次数 | 特征值估计 |
| :------: | :--------: |
|    1     |  2.126100  |
|    2     |  1.769336  |
|    3     |  1.671936  |
|    4     |  1.640106  |
|    5     |  1.628943  |

收敛于第 44 步，得 $\lambda_{\min} = 1.6227971460$

**条件数计算：**

$$\kappa(A) = \frac{|\lambda_{\max}|}{|\lambda_{\min}|} = \frac{5.6510934089}{1.6227971460} = 3.4823165808$$

保留两位有效数字：$\kappa(A) \approx 3.48$

**验证：**

$$\kappa(A) = 3.48 < 7 \quad \checkmark$$

这验证了 Gerschgorin 圆盘定理给出的上界估计是正确的。实际条件数远小于理论上界 7，说明理论估计较为保守但有效。

# 作业五

---

## 习题 1：梯度计算

**题目**：构建模型使得预测值与真实值的误差最小常用向量 2-范数度量，求解模型过程中需要计算梯度，求梯度：

**(1)** $f(A) = \frac{1}{2}\|Ax + b - y\|_2^2$，求 $\frac{\partial f}{\partial A}$

**(2)** $f(x) = \frac{1}{2}\|Ax + b - y\|_2^2$，求 $\frac{\partial f}{\partial x}$

其中 $A \in \mathbb{R}^{m\times n}$，$x \in \mathbb{R}^n$，$b, y \in \mathbb{R}^m$

**解**：

**(1) 关于 $A$ 的梯度**

展开目标函数：

$$f(A) = \frac{1}{2}\|Ax + b - y\|_2^2 = \frac{1}{2}(Ax + b - y)^T(Ax + b - y)$$

$$= \frac{1}{2}(x^TA^TAx + 2(b-y)^TAx + (b-y)^T(b-y))$$

由于 $(b-y)^T(b-y)$ 是常数，对 $A$ 求导后为零：

$$\frac{\partial f}{\partial A} = \frac{\partial}{\partial A}\frac{1}{2}(x^TA^TAx + 2(b-y)^TAx)$$

利用矩阵求导公式：
- $\frac{\partial x^TA^TAx}{\partial A} = 2Axx^T$
- $\frac{\partial (b-y)^TAx}{\partial A} = (b-y)x^T$

因此：

$$\boxed{\frac{\partial f}{\partial A} = Axx^T + (b-y)x^T}$$

**(2) 关于 $x$ 的梯度**

同样展开目标函数，对 $x$ 求导：

$$\frac{\partial f}{\partial x} = \frac{\partial}{\partial x}\frac{1}{2}(x^TA^TAx + 2(b-y)^TAx + (b-y)^T(b-y))$$

利用矩阵求导公式：
- $\frac{\partial x^TA^TAx}{\partial x} = 2A^TAx$
- $\frac{\partial (b-y)^TAx}{\partial x} = A^T(b-y)$

因此：

$$\boxed{\frac{\partial f}{\partial x} = A^TAx + A^T(b-y)}$$

---

## 习题 2：二次型的梯度

**题目**：二次型是数据分析中常用函数，求 $\frac{\partial x^TAx}{\partial x}$，$\frac{\partial x^TAx}{\partial A}$，其中 $A \in \mathbb{R}^{m\times m}$，$x \in \mathbb{R}^m$

**解**：

**(1) 关于 $x$ 的梯度**

对于二次型 $x^TAx$，利用矩阵微分的性质：

$$d(x^TAx) = (dx)^TAx + x^TAdx = x^TA^Tdx + x^TAdx = x^T(A + A^T)dx$$

因此：

$$\boxed{\frac{\partial x^TAx}{\partial x} = (A + A^T)x}$$

**注**：当 $A$ 为对称矩阵时，$\frac{\partial x^TAx}{\partial x} = 2Ax$

**(2) 关于 $A$ 的梯度**

对于二次型 $x^TAx = \sum_{i,j} x_i A_{ij} x_j$，对 $A_{ij}$ 求偏导：

$$\frac{\partial x^TAx}{\partial A_{ij}} = x_ix_j$$

因此，梯度矩阵的第 $(i,j)$ 元素为 $x_ix_j$，即：

$$\boxed{\frac{\partial x^TAx}{\partial A} = xx^T}$$

---

## 习题 3：迹微分法求梯度

**题目**：利用迹微分法求解 $\frac{\partial \text{tr}(W^{-1})}{\partial W}$，其中 $W \in \mathbb{R}^{m\times m}$

**解**：

首先计算 $W^{-1}$ 的微分。由恒等式 $WW^{-1} = I$，两边取微分：

$$d(WW^{-1}) = dW \cdot W^{-1} + W \cdot dW^{-1} = dI = 0$$

因此：

$$W \cdot dW^{-1} = -dW \cdot W^{-1}$$

两边左乘 $W^{-1}$：

$$dW^{-1} = -W^{-1}dW \cdot W^{-1}$$

现在计算迹的微分：

$$d\,\text{tr}(W^{-1}) = \text{tr}(dW^{-1}) = \text{tr}(-W^{-1}dW \cdot W^{-1})$$

利用迹的循环性质 $\text{tr}(ABC) = \text{tr}(CAB)$：

$$d\,\text{tr}(W^{-1}) = \text{tr}(-(W^{-1})^2dW) = \text{tr}(-(W^{-T})^2 dW)$$

因此：

$$\boxed{\frac{\partial \text{tr}(W^{-1})}{\partial W} = -(W^{-T})^2 = -(W^{-1})^T(W^{-1})^T}$$

---

## 习题 4：Softmax 函数的梯度

**题目**：$(\exp(z))_i = \exp(z_i)$，$(\log(z))_i = \log(z_i)$，$f(z) = \frac{\exp(z)}{\mathbf{1}^T\exp(z)}$ 称为 softmax 函数，如果 $q = f(z)$，$J = -p^T\log(q)$，其中 $p, q, z \in \mathbb{R}^n$，并且 $\mathbf{1}^Tp = 1$，

**(1)** 证明：$\frac{\partial J}{\partial z} = q - p$

**(2)** 若 $z = Wx$，其中 $W \in \mathbb{R}^{n\times m}$，$x \in \mathbb{R}^m$，$\frac{\partial J}{\partial W} = (q - p)x^T$ 是否成立。

**解**：

**(1) 证明关于 $z$ 的梯度**

将损失函数展开：

$$J = -p^T\log(q) = -p^T\log\left(\frac{\exp(z)}{\mathbf{1}^T\exp(z)}\right)$$

$$= -p^T\log(\exp(z)) + p^T\log(\mathbf{1}^T\exp(z))\mathbf{1}$$

$$= -p^Tz + p^T\mathbf{1}\log(\mathbf{1}^T\exp(z))$$

由于 $p^T\mathbf{1} = 1$（概率分布的归一化条件）：

$$J = -p^Tz + \log(\mathbf{1}^T\exp(z))$$

对 $z$ 求导：

$$\frac{\partial J}{\partial z} = -p + \frac{\partial \log(\mathbf{1}^T\exp(z))}{\partial z}$$

$$= -p + \frac{1}{\mathbf{1}^T\exp(z)} \cdot \frac{\partial (\mathbf{1}^T\exp(z))}{\partial z}$$

$$= -p + \frac{\exp(z)}{\mathbf{1}^T\exp(z)}$$

$$= -p + q$$

因此：

$$\boxed{\frac{\partial J}{\partial z} = q - p}$$

**(2) 证明关于 $W$ 的梯度**

利用链式法则和迹微分法：

$$dJ = d\,\text{tr}(J) = \text{tr}(dJ)$$

由 $z = Wx$，有 $dz = dW \cdot x$，因此：

$$dJ = \text{tr}\left[\left(\frac{\partial J}{\partial z}\right)^T dz\right] = \text{tr}[(q-p)^T dW \cdot x]$$

利用迹的性质 $\text{tr}(ABC) = \text{tr}(CAB)$：

$$dJ = \text{tr}[x(q-p)^T dW]$$

因此：

$$\boxed{\frac{\partial J}{\partial W} = (q-p)x^T \quad \text{成立}}$$

---

## 习题 5：多元正态分布的极大似然估计

**题目**：以下内容是利用极大似然估计求解多元正态分布模型的关键步骤：

$$L = -\frac{Nd}{2}\ln(2\pi) - \frac{N}{2}\ln|\Sigma| - \frac{1}{2}\sum_{t=1}^N (x_t - \mu)^T\Sigma^{-1}(x_t - \mu)$$

$L$ 是对数似然，$N$ 为样本数，$d$ 为样本维数，$\Sigma \in \mathbb{R}^{d\times d}$ 为协方差矩阵，$\mu \in \mathbb{R}^d$ 为期望向量。

**(1)** 求 $\frac{\partial L}{\partial \mu}$

**(2)** 当 $\mu = \frac{1}{N}\sum_{t=1}^N x_t$ 时，求 $\frac{\partial L}{\partial \Sigma}$，并求使 $\frac{\partial L}{\partial \Sigma} = 0$ 成立的 $\Sigma$。

**解**：

**(1) 关于 $\mu$ 的梯度**

对数似然中只有第三项与 $\mu$ 相关：

$$\frac{\partial L}{\partial \mu} = \frac{\partial}{\partial \mu}\left[-\frac{1}{2}\sum_{t=1}^N (x_t - \mu)^T\Sigma^{-1}(x_t - \mu)\right]$$

对每一项求导：

$$\frac{\partial}{\partial \mu}(x_t - \mu)^T\Sigma^{-1}(x_t - \mu) = -2\Sigma^{-1}(x_t - \mu)$$

因此：

$$\boxed{\frac{\partial L}{\partial \mu} = \sum_{t=1}^N \Sigma^{-1}(x_t - \mu)}$$

令 $\frac{\partial L}{\partial \mu} = 0$，得 $\mu = \frac{1}{N}\sum_{t=1}^N x_t$（样本均值）。

**(2) 关于 $\Sigma$ 的梯度**

使用迹微分法，将对数似然写成迹的形式：

$$dL = d\left[-\frac{N}{2}\ln|\Sigma|\right] - d\left[\frac{1}{2}\sum_{t=1}^N (x_t-\mu)^T\Sigma^{-1}(x_t-\mu)\right]$$

**第一项：**

$$d\left[-\frac{N}{2}\ln|\Sigma|\right] = -\frac{N}{2}d[\ln|\Sigma|] = -\frac{N}{2}\text{tr}[\Sigma^{-1}d\Sigma]$$

**第二项：**

$$d\left[\frac{1}{2}\sum_{t=1}^N (x_t-\mu)^T\Sigma^{-1}(x_t-\mu)\right] = \frac{1}{2}d\,\text{tr}\left[\sum_{t=1}^N (x_t-\mu)(x_t-\mu)^T\Sigma^{-1}\right]$$

$$= \frac{1}{2}\text{tr}\left[\sum_{t=1}^N (x_t-\mu)(x_t-\mu)^T d(\Sigma^{-1})\right]$$

利用 $d\Sigma^{-1} = -\Sigma^{-1}(d\Sigma)\Sigma^{-1}$：

$$= \frac{1}{2}\text{tr}\left[\sum_{t=1}^N (x_t-\mu)(x_t-\mu)^T(-\Sigma^{-1}d\Sigma \cdot \Sigma^{-1})\right]$$

$$= -\frac{1}{2}\text{tr}\left[\Sigma^{-1}\sum_{t=1}^N (x_t-\mu)(x_t-\mu)^T\Sigma^{-1}d\Sigma\right]$$

综合两项：

$$dL = \text{tr}\left[\left(-\frac{N}{2}\Sigma^{-1} + \frac{1}{2}\Sigma^{-1}\sum_{t=1}^N (x_t-\mu)(x_t-\mu)^T\Sigma^{-1}\right)d\Sigma\right]$$

因此：

$$\boxed{\frac{\partial L}{\partial \Sigma} = -\frac{N}{2}\Sigma^{-1} + \frac{1}{2}\Sigma^{-1}\sum_{t=1}^N (x_t-\mu)(x_t-\mu)^T\Sigma^{-1}}$$

令 $\frac{\partial L}{\partial \Sigma} = 0$：

$$N\Sigma^{-1} = \Sigma^{-1}\sum_{t=1}^N (x_t-\mu)(x_t-\mu)^T\Sigma^{-1}$$

两边左乘 $\Sigma$，右乘 $\Sigma$：

$$N\Sigma = \sum_{t=1}^N (x_t-\mu)(x_t-\mu)^T$$

因此：

$$\boxed{\Sigma = \frac{1}{N}\sum_{t=1}^N (x_t-\mu)(x_t-\mu)^T}$$

这就是样本协方差矩阵的极大似然估计。

---

## 习题 6：互信息的化简

**题目**：（互信息）假设 $X_1 \to X_2 \to X_3 \to \cdots \to X_n$ 是一个马尔科夫链，即

$$p(x_1, x_2, \ldots, x_n) = p(x_1)p(x_2|x_1)\cdots p(x_n|x_{n-1})$$

试化简 $I(X_1; X_2, \ldots, X_n)$

**解**：

互信息定义为：

$$I(X_1; X_2, \ldots, X_n) = H(X_1) - H(X_1 | X_2, \ldots, X_n)$$

利用条件熵的性质：

$$= H(X_1) - [H(X_1, X_2, \ldots, X_n) - H(X_2, \ldots, X_n)]$$

对于马尔科夫链，联合熵可以分解为：

$$H(X_1, X_2, \ldots, X_n) = \sum_{i=1}^n H(X_i|X_{i-1},\ldots,X_1)$$

其中 $H(X_1|X_0,\ldots) = H(X_1)$。由马尔科夫性质：

$$H(X_i|X_{i-1},\ldots,X_1) = H(X_i|X_{i-1})$$

类似地：

$$H(X_2, \ldots, X_n) = \sum_{i=2}^n H(X_i|X_{i-1},\ldots,X_2)$$

代入得：

$$I(X_1; X_2, \ldots, X_n) = H(X_1) - \left[\left(H(X_1) + \sum_{i=2}^n H(X_i|X_{i-1})\right) - \left(H(X_2) + \sum_{i=3}^n H(X_i|X_{i-1})\right)\right]$$

$$= H(X_1) - H(X_1) - H(X_2|X_1) + H(X_2)$$

$$= H(X_2) - H(X_2|X_1)$$

$$= I(X_1; X_2)$$

因此：

$$\boxed{I(X_1; X_2, \ldots, X_n) = I(X_1; X_2)}$$

**结论**：在马尔科夫链中，$X_1$ 与序列 $(X_2, \ldots, X_n)$ 的互信息等于 $X_1$ 与 $X_2$ 的互信息，这体现了马尔科夫性质。

---

## 习题 7：KL 散度与最大似然估计

**题目**：（通过 KL 散度理解 MLE）假设 $x_1, \ldots, x_n$ 来自密度为 $p(x)$ 的分布 $P$，试说明如果采用具有密度函数 $q_\theta(x)$ 的分布族 $Q_\theta$ 来计算 MLE，那么 MLE 将试图找到在 KL 散度意义上最接近真实分布 $P$ 的分布 $Q_\theta$。

即证明：

$$\arg\max_\theta \prod_{i=1}^n q_\theta(x_i) \Leftrightarrow \arg\min_\theta D_{KL}(P \| Q_\theta)$$

**证明**：

从最大似然估计出发：

$$\arg\max_\theta \prod_{i=1}^n q_\theta(x_i) \Leftrightarrow \arg\max_\theta \sum_{i=1}^n \log q_\theta(x_i)$$

$$\Leftrightarrow \arg\min_\theta -\frac{1}{n}\sum_{i=1}^n \log q_\theta(x_i)$$

当样本量 $n \to \infty$ 时，根据大数定律：

$$-\frac{1}{n}\sum_{i=1}^n \log q_\theta(x_i) \xrightarrow{P} -\mathbb{E}_P[\log q_\theta(x)]$$

$$= -\int p(x)\log q_\theta(x)dx$$

这正是**交叉熵** $H(P, Q_\theta)$ 的定义。因此：

$$\arg\min_\theta -\mathbb{E}_P[\log q_\theta(x)] \Leftrightarrow \arg\min_\theta H(P, Q_\theta)$$

由于真实分布 $P$ 的熵 $H(P)$ 是常数（不依赖于 $\theta$）：

$$\arg\min_\theta H(P, Q_\theta) \Leftrightarrow \arg\min_\theta [H(P, Q_\theta) - H(P)]$$

而 KL 散度定义为：

$$D_{KL}(P \| Q_\theta) = \int p(x)\log \frac{p(x)}{q_\theta(x)}dx$$

$$= \int p(x)\log p(x)dx - \int p(x)\log q_\theta(x)dx$$

$$= H(P) + H(P, Q_\theta)$$

等价于：

$$H(P, Q_\theta) - H(P) = -\int p(x)\log q_\theta(x)dx + \int p(x)\log p(x)dx = D_{KL}(P \| Q_\theta)$$

因此：

$$\boxed{\arg\max_\theta \prod_{i=1}^n q_\theta(x_i) \Leftrightarrow \arg\min_\theta D_{KL}(P \| Q_\theta)}$$

**结论**：从优化模型参数的角度来说，最小化负对数似然、交叉熵（多分类问题）和 KL 散度这三种方式是等价的。MLE 实际上是在寻找与真实分布 KL 散度最小的模型分布。

# 作业六

---

## 习题 1：贝叶斯推断求后验分布

**题目**：假设总体 $X \sim N(\mu, \sigma^2)$（$\sigma^2$ 已知），$X_1, X_2, \ldots, X_n$ 为来自总体 $X$ 的样本，由过去的经验和知识，我们可以确定 $\mu$ 的取值比较集中在 $\mu_0$ 附近，离 $\mu_0$ 越远，$\mu$ 取值的可能性越小，于是我们假定 $\mu$ 的先验分布为正态分布

$$\pi(\mu) = \frac{1}{\sqrt{2\pi\sigma_\mu^2}} \exp\left[-\frac{1}{2\sigma_\mu^2}(\mu - \mu_0)^2\right] \quad (\mu_0, \sigma_\mu \text{ 已知})$$

求 $\mu$ 的后验概率分布。

**解**：

根据贝叶斯定理，后验分布正比于似然函数与先验分布的乘积。

**似然函数：**

给定 $\mu$，样本 $x_1, \ldots, x_n$ 的联合密度函数为：

$$q(x | \mu) = \prod_{i=1}^n \frac{1}{\sqrt{2\pi\sigma^2}} \exp\left[-\frac{1}{2\sigma^2}(x_i - \mu)^2\right]$$

$$= \frac{1}{\sigma^n(2\pi)^{n/2}} \exp\left[-\frac{1}{2\sigma^2}\sum_{i=1}^n(x_i - \mu)^2\right]$$

**后验密度函数：**

$$h(\mu | x) = \frac{q(x | \mu) \cdot \pi(\mu)}{f_x(x)} \propto q(x | \mu) \cdot \pi(\mu)$$

$$\propto \exp\left[-\frac{1}{2\sigma^2}\sum_{i=1}^n(x_i - \mu)^2\right] \cdot \exp\left[-\frac{1}{2\sigma_\mu^2}(\mu - \mu_0)^2\right]$$

合并指数项：

$$h(\mu | x) \propto \exp\left[-\frac{1}{2}\left(\frac{\sum_{i=1}^n(x_i - \mu)^2}{\sigma^2} + \frac{(\mu - \mu_0)^2}{\sigma_\mu^2}\right)\right]$$

展开平方项：

$$\sum_{i=1}^n(x_i - \mu)^2 = \sum_{i=1}^n x_i^2 - 2\mu\sum_{i=1}^n x_i + n\mu^2 = n(\mu - \bar{x})^2 + \text{常数}$$

因此指数项中关于 $\mu$ 的部分为：

$$-\frac{1}{2}\left[\frac{n}{\sigma^2}(\mu - \bar{x})^2 + \frac{1}{\sigma_\mu^2}(\mu - \mu_0)^2\right]$$

$$= -\frac{1}{2}\left[\left(\frac{n}{\sigma^2} + \frac{1}{\sigma_\mu^2}\right)\mu^2 - 2\mu\left(\frac{n\bar{x}}{\sigma^2} + \frac{\mu_0}{\sigma_\mu^2}\right) + \text{常数}\right]$$

配方得：

$$h(\mu | x) \propto \exp\left[-\frac{(\mu - t)^2}{2\eta^2}\right]$$

其中：

$$t = \frac{\frac{n}{\sigma^2}\bar{x} + \frac{1}{\sigma_\mu^2}\mu_0}{\frac{n}{\sigma^2} + \frac{1}{\sigma_\mu^2}}, \quad \eta^2 = \frac{1}{\frac{n}{\sigma^2} + \frac{1}{\sigma_\mu^2}}$$

因此，后验分布为：

$$\boxed{\mu | x \sim N\left(\frac{\frac{n}{\sigma^2}\bar{x} + \frac{1}{\sigma_\mu^2}\mu_0}{\frac{n}{\sigma^2} + \frac{1}{\sigma_\mu^2}}, \frac{1}{\frac{n}{\sigma^2} + \frac{1}{\sigma_\mu^2}}\right)}$$

**解释**：后验均值是样本均值和先验均值的加权平均，权重由各自的精度（方差的倒数）决定。

---

## 习题 2：Gauss 累积分布函数的对数凹性

**题目**：证明：Gauss 概率密度函数的累积分布函数

$$\Phi(x) = \frac{1}{\sqrt{2\pi}}\int_{-\infty}^x e^{-u^2/2}du$$

是对数-凹函数，即 $\log(\Phi(x))$ 是凹函数。

**证明**：

要证明 $\log(\Phi(x))$ 是凹函数，需要证明其二阶导数非正，即：

$$\frac{d^2}{dx^2}\log(\Phi(x)) \leq 0$$

等价于证明：

$$\Phi(x)\Phi''(x) \leq [\Phi'(x)]^2$$

**计算导数：**

$$\Phi'(x) = \frac{1}{\sqrt{2\pi}}e^{-x^2/2}$$

$$\Phi''(x) = \frac{1}{\sqrt{2\pi}}e^{-x^2/2}(-x) = -x\Phi'(x)$$

$$[\Phi'(x)]^2 = \frac{1}{2\pi}e^{-x^2}$$

**情况 1：$x \geq 0$**

当 $x \geq 0$ 时，$\Phi''(x) = -x\Phi'(x) \leq 0$，而 $\Phi(x) > 0$，$[\Phi'(x)]^2 \geq 0$，因此：

$$\Phi(x)\Phi''(x) \leq 0 \leq [\Phi'(x)]^2$$

**情况 2：$x < 0$**

当 $x < 0$ 时，需要证明：

$$\Phi(x) \cdot (-x) \leq \frac{1}{\sqrt{2\pi}}e^{-x^2/2}$$

即：

$$\int_{-\infty}^x e^{-u^2/2}du \leq \frac{e^{-x^2/2}}{-x}$$

由于 $\frac{u^2}{2}$ 是凸函数，对任意 $u < x < 0$，有：

$$\frac{u^2}{2} \geq \frac{x^2}{2} + (u-x)x = xu - \frac{x^2}{2}$$

因此：

$$e^{-u^2/2} \leq e^{-xu + x^2/2}$$

积分得：

$$\int_{-\infty}^x e^{-u^2/2}du \leq \int_{-\infty}^x e^{x^2/2-xu}du$$

$$= e^{x^2/2} \int_{-\infty}^x e^{-xu}du = e^{x^2/2} \cdot \frac{e^{-xu}}{-x}\bigg|_{u=-\infty}^x$$

$$= e^{x^2/2} \cdot \frac{e^{-x^2}}{-x} = \frac{e^{-x^2/2}}{-x}$$

因此 $\Phi(x)\Phi''(x) \leq [\Phi'(x)]^2$ 对 $x < 0$ 也成立。

综上所述，$\Phi(x)$ 是对数凹函数。 $\square$

---

## 习题 3：共轭函数的计算

**题目**：计算函数 $f(x)$ 的共轭函数，以及共轭函数的定义域。

**(1)** $f(x) = -\log x$

**(2)** $f(x) = e^x$

**解**：

共轭函数定义为：

$$f^*(y) = \sup_x (xy - f(x))$$

### (1) $f(x) = -\log x$

定义域 $\text{dom}(f) = \{x|x > 0\}$。

对于给定的 $y$，求 $g(x) = xy + \log x$ 的上确界。

求导：$g'(x) = y + \frac{1}{x}$

- 当 $y \geq 0$ 时，$g'(x) > 0$ 恒成立，函数单调递增，无上界
- 当 $y < 0$ 时，令 $g'(x) = 0$ 得 $x = -\frac{1}{y}$

此时：

$$g\left(-\frac{1}{y}\right) = -\frac{1}{y} \cdot y + \log\left(-\frac{1}{y}\right) = -1 - \log(-y)$$

因此：

$$\boxed{f^*(y) = \begin{cases} -\log(-y) - 1, & y < 0 \\ +\infty, & y \geq 0 \end{cases}}$$

定义域：$\text{dom}(f^*) = \{y | y < 0\}$

### (2) $f(x) = e^x$

定义域 $\text{dom}(f) = \mathbb{R}$。

对于给定的 $y$，求 $g(x) = xy - e^x$ 的上确界。

求导：$g'(x) = y - e^x$

- 当 $y \leq 0$ 时，$g'(x) < 0$ 恒成立，函数单调递减，无上界（趋于 $-\infty$ 时）
- 当 $y > 0$ 时，令 $g'(x) = 0$ 得 $x = \log y$

此时：

$$g(\log y) = y\log y - e^{\log y} = y\log y - y$$

- 当 $y = 0$ 时：$f^*(0) = \sup_x(-e^x) = 0$（当 $x \to -\infty$ 时）

因此：

$$\boxed{f^*(y) = \begin{cases} y\log y - y, & y > 0 \\ 0, & y = 0 \\ +\infty, & y < 0 \end{cases}}$$

定义域：$\text{dom}(f^*) = \{y | y \geq 0\}$（规定 $0\log 0 = 0$）

---

## 习题 4：KKT 条件的应用

**题目**：写出下述非线性规划的 KKT 条件并求解

**(1)** $\max f(x) = (x - 3)^2$ subject to $1 \leq x \leq 5$

**(2)** $\min f(x) = (x - 3)^2$ subject to $1 \leq x \leq 5$

**解**：

### (1) 最大化问题

将最大化问题转化为最小化问题：

$$\begin{cases} \text{minimize} \quad -f(x) = -(x-3)^2 \\ g_1(x) = 1 - x \leq 0 \\ g_2(x) = x - 5 \leq 0 \end{cases}$$

**梯度：**

$$\nabla_x[-f(x)] = -2(x-3), \quad \nabla_x g_1(x) = -1, \quad \nabla_x g_2(x) = 1$$

**KKT 条件：**

$$\begin{cases} -2(x^* - 3) - v_1^* + v_2^* = 0 & \text{(稳定性)} \\ v_1^*(1 - x^*) = 0 & \text{(互补松弛性)} \\ v_2^*(x^* - 5) = 0 & \text{(互补松弛性)} \\ v_1^* \geq 0, \quad v_2^* \geq 0 & \text{(对偶可行性)} \\ 1 \leq x^* \leq 5 & \text{(原始可行性)} \end{cases}$$

**情况分析：**

**情况 i：** $v_1^* = 0, v_2^* = 0$

由稳定性条件：$-2(x^* - 3) = 0$，得 $x^* = 3$

此时 $f(x^*) = 0$

**情况 ii：** $v_1^* > 0, v_2^* = 0$

由互补松弛性：$x^* = 1$

由稳定性条件：$-2(1-3) - v_1^* = 0$，得 $v_1^* = 4 > 0$ ✓

此时 $f(x^*) = 4$

**情况 iii：** $v_1^* = 0, v_2^* > 0$

由互补松弛性：$x^* = 5$

由稳定性条件：$-2(5-3) + v_2^* = 0$，得 $v_2^* = 4 > 0$ ✓

此时 $f(x^*) = 4$

**情况 iv：** $v_1^* > 0, v_2^* > 0$

由互补松弛性：$x^* = 1$ 且 $x^* = 5$，矛盾

**结论：** 

$$\boxed{x^* = 1 \text{ 或 } x^* = 5, \quad \max f(x) = 4}$$

### (2) 最小化问题

$$\begin{cases} \text{minimize} \quad f(x) = (x-3)^2 \\ g_1(x) = 1 - x \leq 0 \\ g_2(x) = x - 5 \leq 0 \end{cases}$$

**梯度：**

$$\nabla_x f(x) = 2(x-3)$$

**KKT 条件：**

$$\begin{cases} 2(x^* - 3) - v_1^* + v_2^* = 0 \\ v_1^*(1 - x^*) = 0 \\ v_2^*(x^* - 5) = 0 \\ v_1^* \geq 0, \quad v_2^* \geq 0 \\ 1 \leq x^* \leq 5 \end{cases}$$

**情况分析：**

**情况 i：** $v_1^* = 0, v_2^* = 0$

由稳定性条件：$2(x^* - 3) = 0$，得 $x^* = 3$

此时 $f(x^*) = 0$ ✓

**情况 ii：** $v_1^* > 0, v_2^* = 0$

$x^* = 1$，$2(1-3) - v_1^* = 0$，得 $v_1^* = -4 < 0$ ✗

**情况 iii：** $v_1^* = 0, v_2^* > 0$

$x^* = 5$，$2(5-3) + v_2^* = 0$，得 $v_2^* = -4 < 0$ ✗

**结论：**

$$\boxed{x^* = 3, \quad \min f(x) = 0}$$

---

## 习题 5：Lagrange 乘子法证明矩阵 2-范数

**题目**：用 Lagrange 乘子法证明：矩阵 $A \in \mathbb{R}^{m\times n}$ 的 2-范数

$$\|A\|_2 = \max_{\|x\|_2 = 1, x \in \mathbb{R}^n} \|Ax\|_2$$

的平方是 $A^TA$ 的最大特征值。

**证明**：

优化问题为：

$$\text{maximize} \quad f(x) = \|Ax\|_2^2 = x^TA^TAx \quad \text{subject to} \quad x^Tx = 1$$

**Lagrange 函数：**

$$L(x, \lambda) = x^TA^TAx - \lambda(x^Tx - 1)$$

**求梯度并令其为零：**

$$\frac{\partial L}{\partial x} = 2A^TAx - 2\lambda x = 0$$

因此：

$$A^TAx = \lambda x$$

这说明在极值点 $x^*$ 处，$x^*$ 是 $A^TA$ 的特征向量，$\lambda$ 是对应的特征值。

**目标函数值：**

$$f(x^*) = (x^*)^TA^TAx^* = (x^*)^T\lambda x^* = \lambda(x^*)^Tx^* = \lambda$$

由于我们求的是最大值，因此：

$$\|A\|_2^2 = \max_{\|x\|_2=1} x^TA^TAx = \lambda_{\max}(A^TA)$$

即：

$$\boxed{\|A\|_2 = \sqrt{\lambda_{\max}(A^TA)}}$$

$\square$

---

## 习题 6：欠定方程的最小二范数解

**题目**：用 Lagrange 乘子法求欠定方程 $Ax = b$ 的最小二范数解，其中 $A \in \mathbb{R}^{m\times n}$，$m \leq n$，$\text{rank}(A) = m$

**解**：

优化问题为：

$$\text{minimize} \quad f(x) = \frac{1}{2}\|x\|_2^2 = \frac{1}{2}x^Tx \quad \text{subject to} \quad Ax = b$$

**Lagrange 函数：**

$$L(x, \lambda) = \frac{1}{2}x^Tx - \lambda^T(Ax - b)$$

**求梯度并令其为零：**

$$\frac{\partial L}{\partial x} = x - A^T\lambda = 0$$

因此：

$$x = A^T\lambda$$

**代入约束条件：**

$$Ax = b \Rightarrow A(A^T\lambda) = b$$

$$AA^T\lambda = b$$

由于 $\text{rank}(A) = m$，矩阵 $AA^T \in \mathbb{R}^{m \times m}$ 是满秩的，因此可逆：

$$\lambda = (AA^T)^{-1}b$$

**最小二范数解：**

$$\boxed{x^* = A^T(AA^T)^{-1}b}$$

**验证：** 
- $Ax^* = AA^T(AA^T)^{-1}b = b$ ✓（满足约束）
- 可以证明这是所有满足 $Ax = b$ 的解中范数最小的

---

## 习题 7：最速下降法

**题目**：用最速下降法和精确线搜索计算

$$\min f(x) = x_1^2 + x_2^2 + x_3^2$$

初始点 $x^{(0)} = (2, 2, 1)^T$。当 $|f(x^{(n+1)}) - f(x^{(n)})| < 0.001$ 时迭代终止。

**解**：

目标函数 $f(x) = x^Tx$，梯度 $\nabla f(x) = 2x$。

**精确线搜索：**

最速下降方向为 $d^{(k)} = -\nabla f(x^{(k)}) = -2x^{(k)}$

在该方向上最小化 $f(x^{(k)} + \lambda d^{(k)})$：

$$f(x^{(k)} + \lambda d^{(k)}) = (x^{(k)} - 2\lambda x^{(k)})^T(x^{(k)} - 2\lambda x^{(k)})$$

$$= (1 - 2\lambda)^2 (x^{(k)})^Tx^{(k)}$$

对 $\lambda$ 求导并令其为零：

$$\frac{d}{d\lambda}[(1-2\lambda)^2 (x^{(k)})^Tx^{(k)}] = 2(1-2\lambda)(-2)(x^{(k)})^Tx^{(k)} = 0$$

得 $\lambda^* = \frac{1}{2}$

**迭代过程：**

$$x^{(1)} = x^{(0)} + \frac{1}{2}(-2x^{(0)}) = x^{(0)} - x^{(0)} = 0$$

$$f(x^{(1)}) = 0$$

$$x^{(2)} = x^{(1)} - x^{(1)} = 0$$

$$|f(x^{(1)}) - f(x^{(0)})| = |0 - 9| = 9 > 0.001$$

$$|f(x^{(2)}) - f(x^{(1)})| = |0 - 0| = 0 < 0.001$$

迭代终止。

**结论：**

$$\boxed{x^* = (0, 0, 0)^T, \quad f_{\min} = 0}$$

算法一步即收敛到最优解，这是因为目标函数是简单的二次型，且 Hessian 矩阵为单位矩阵的倍数。

---

## 习题 8：DFP 法求二次函数极小点

**题目**：试用 DFP 法计算下述二次函数的极小点

$$\min f(x) = 3x_1^2 + x_2^2 - 2x_1x_2 - 4x_1$$

**解**：

选择初始点 $x^{(0)} = (-2, 4)^T$，初始 Hessian 逆近似 $H^{(0)} = I$。

**梯度：**

$$\nabla f(x) = \begin{pmatrix} 6x_1 - 2x_2 - 4 \\ 2x_2 - 2x_1 \end{pmatrix}$$

### 第一次迭代

$$\nabla f(x^{(0)}) = \begin{pmatrix} 6(-2) - 2(4) - 4 \\ 2(4) - 2(-2) \end{pmatrix} = \begin{pmatrix} -24 \\ 12 \end{pmatrix}$$

**搜索方向：**

$$p^{(0)} = -H^{(0)}\nabla f(x^{(0)}) = -I \begin{pmatrix} -24 \\ 12 \end{pmatrix} = \begin{pmatrix} 24 \\ -12 \end{pmatrix}$$

**精确线搜索：** 求 $\lambda_0 = \arg\min_\lambda f(x^{(0)} + \lambda p^{(0)})$

$$f(x^{(0)} + \lambda p^{(0)}) = 3(-2+24\lambda)^2 + (4-12\lambda)^2 - 2(-2+24\lambda)(4-12\lambda) - 4(-2+24\lambda)$$

对 $\lambda$ 求导并令其为零，得：

$$\lambda_0 = \frac{5}{34}$$

**更新：**

$$x^{(1)} = x^{(0)} + \lambda_0 p^{(0)} = \begin{pmatrix} -2 \\ 4 \end{pmatrix} + \frac{5}{34}\begin{pmatrix} 24 \\ -12 \end{pmatrix} = \begin{pmatrix} \frac{26}{17} \\ \frac{38}{17} \end{pmatrix}$$

$$\nabla f(x^{(1)}) = \begin{pmatrix} \frac{12}{17} \\ \frac{24}{17} \end{pmatrix}$$

**DFP 更新公式：**

$$\Delta x^{(0)} = x^{(1)} - x^{(0)} = \begin{pmatrix} \frac{60}{17} \\ -\frac{30}{17} \end{pmatrix}$$

$$\Delta g^{(0)} = \nabla f(x^{(1)}) - \nabla f(x^{(0)}) = \begin{pmatrix} \frac{420}{17} \\ -\frac{180}{17} \end{pmatrix}$$

$$H^{(1)} = H^{(0)} + \frac{\Delta x^{(0)}(\Delta x^{(0)})^T}{(\Delta g^{(0)})^T\Delta x^{(0)}} - \frac{H^{(0)}\Delta g^{(0)}(\Delta g^{(0)})^TH^{(0)}}{(\Delta g^{(0)})^TH^{(0)}\Delta g^{(0)}}$$

计算后得：

$$H^{(1)} = I + \frac{1}{1800}\begin{pmatrix} 3600 & -1800 \\ -1800 & 900 \end{pmatrix} - \frac{1}{226800}\begin{pmatrix} 176400 & -75600 \\ -75600 & 32400 \end{pmatrix}$$

### 第二次迭代

$$p^{(1)} = -H^{(1)}\nabla f(x^{(1)}) = -\begin{pmatrix} \frac{18}{29} \\ \frac{42}{29} \end{pmatrix}$$

精确线搜索得 $\lambda_1 = \frac{29}{34}$

$$x^{(2)} = x^{(1)} + \lambda_1 p^{(1)} = \begin{pmatrix} 1 \\ 1 \end{pmatrix}$$

$$\nabla f(x^{(2)}) = \begin{pmatrix} 0 \\ 0 \end{pmatrix}$$

**结论：**

$$\boxed{x^* = (1, 1)^T \text{ 为极小点}}$$

DFP 法对于 $n$ 维二次函数，最多需要 $n$ 步即可收敛到精确解（具有二次终止性）。