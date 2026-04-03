# 一、灵感来源

did-vc-survey-2025综述给出了一些VC吊销的情况
### 威胁——VC过期：[[did-vc-survey-2025#3.2 凭证有效性]]

### 研究结论：[[did-vc-survey-2025#7.5 安全与隐私（Security and Privacy）]]

### 初步思考：我们能做什么？

####  方向1：针对IoT领域——改进 EVOKE (有现成可以参考，实验困难)

 在 EVOKE 基础上改进：

- 更小存储（<1.5KB）
- 更快验证
- 更少通信

---

#### 方向2：针对隐私保护——VC 撤销 + ZKP/累加器（密码学基础薄弱）

论文也提到了：累加器 + 零知识证明（ZKP）

使用密码

- 不仅证明“未被撤销”
- 还做到：
    - 不暴露身份
    - 不暴露 credential ID

例如：
- zk-SNARK / zk-STARK

---

#### 方向3：跨系统撤销（但难）

问题：

- 不同 DID 系统：
    - Ethereum DID
    - Hyperledger Indy
    - DIDKit
	
- 不能互通 ❌
- 设计到不同系统之间的VC吊销状态，感觉会很难做

---

# Phase 1：目前VC吊销的相关工作

## IoT —— [[Evoke Efficient Revocation Verifiable Credentials Iot]]

## IoT——[[VCER-certificate-validation-constrained-networks]]

## 隐私保护——[[crset-private-vc-revocation]]



---

## 你要做的事情

建立一个分类体系（taxonomy）：

```text
1 Status List（RL2020 / StatusList2021）
2 Accumulator-based
3 ZKP-based
4 Short-lived credential
5 新型结构（Bloom Filter / Vector Commitment）
```

---

## 输出

一张表（必须做）：

| 方案  | 是否隐私 | 是否可扩展 | 是否离线验证 | 问题  |
| --- | ---- | ----- | ------ | --- |
|     |      |       |        |     |

---

👉 这一步的本质：

```text
为后续“找问题”做准备
```

---

# 🟡 Phase 2：找 Research Gap（最关键，2–5 天）

## 🎯 目标

```text
找到一个“别人解决不好的问题”
```

---

## 方法（核心技巧）

你需要做“多维对比”，而不是逐篇总结：

---

### 📊 推荐分析维度：

|维度|说明|
|---|---|
|隐私|是否泄露身份 / index|
|效率|验证时间 / 证明大小|
|交互性|是否需要在线查询|
|可扩展性|是否适合大规模|
|适用场景|IoT / 通用网络|

---

## 🎯 最终你要得到一句话：

例如：

```text
现有 VC 吊销方案无法同时满足：
隐私保护 + 非交互验证 + 高可扩展性
```

---

👉 这句话就是：

```text
你的论文动机（Motivation）
```

---

# 🔴 Phase 3：确定论文问题（Research Question）

## 🎯 标准形式：

```text
如何在 X 条件下，实现 Y 目标？
```

---

## 🎯 示例（你可以直接用）

---

### 方向 1（推荐🔥）

```text
如何在不泄露用户身份的情况下，实现非交互式 VC 吊销验证？
```

---

### 方向 2

```text
如何设计适用于 IoT 场景的轻量级 VC 吊销机制？
```

---

### 方向 3

```text
如何实现跨 DID 系统的可互操作 VC 吊销机制？
```

---

👉 注意：

```text
问题一定要“具体 + 可验证”
```

---

# 🧩 Phase 4：设计你的方案（核心创新）

## 🎯 思路

你不是“发明一个系统”，而是：

```text
在现有方法上做组合 / 优化 / 改进
```

---

## 常见技术组合（可以选）

---

### 🔹 路线 1（最主流）

```text
Accumulator + ZKP
```

目标：

- 隐私保护
    
- 不暴露 membership
    

问题：

- 计算开销大
    

👉 你的改进空间：

```text
降低 proving 成本
```

---

### 🔹 路线 2（工程导向）

```text
Status List + 压缩结构（Bloom Filter）
```

目标：

- 高扩展性
    

问题：

- 误判（false positive）
    

👉 改进：

```text
降低误判率 + 保持压缩率
```

---

### 🔹 路线 3（新颖方向）

```text
ZKP + Vector Commitment
```

目标：

- 隐私 + 高效验证
    

---

# 🧪 Phase 5：实验与评估（决定你能不能发）

## 🎯 必须做的 3 类实验

---

### 1️⃣ 性能测试

```text
- 证明生成时间
- 验证时间
- 存储大小
```

---

### 2️⃣ 对比实验

和已有方案对比：

```text
RL2020
Accumulator
ZKP-based
```

---

### 3️⃣ 安全分析

```text
是否泄露信息？
是否可伪造？
```

---

# 📝 Phase 6：论文结构（直接可用）

---

## 标准结构：

```text
1 Introduction
   - 背景
   - 问题
   - 贡献

2 Related Work
   - 分类（你 Phase 1 的成果）

3 Preliminaries
   - VC / DID / ZKP

4 Problem Definition
   - 系统模型
   - 威胁模型

5 Proposed Method
   - 你的方案（核心）

6 Evaluation
   - 实验

7 Conclusion
```

---

# 🎯 四、给你一个“可投稿级选题”（直接参考）

---

## 🧠 示例题目（你可以改写）

---

### ⭐ 推荐方向（强烈建议）

```text
A Privacy-Preserving and Non-Interactive Revocation Mechanism for Verifiable Credentials
```

---

### 核心思想：

```text
用 ZKP 实现：
- 不暴露 VC
- 不依赖在线查询
- 可验证吊销状态
```

