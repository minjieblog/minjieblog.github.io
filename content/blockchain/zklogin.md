---
title: "zkLogin: 基于现有凭证的隐私保护区块链认证"
date: 2026-01-21T21:20:09+08:00
draft: false
tags: ["区块链", "零知识证明", "身份认证", "OpenID", "隐私保护"]
categories: ["论文笔记", "密码学"]
summary: "本文详细介绍 zkLogin 系统,一个发表在 CCS 2024 的区块链认证方案。它利用 OpenID Connect 和零知识证明,让用户无需记忆助记词或管理私钥,仅用现有社交账户即可安全地进行区块链交易。系统已在 Sui 区块链部署,生成了数十万账户。"
description: "zkLogin 是一种创新的区块链认证方案,允许用户使用 Google、Facebook 等现有 OpenID 账户进行区块链交易认证,通过零知识证明技术保护隐私,无需管理私钥"
comments: true
---

## 论文基本信息

- **标题**: zkLogin: Privacy-Preserving Blockchain Authentication with Existing Credentials
- **会议**: ACM CCS 2024
- **作者**: Foteini Baldimtsi 等 (Mysten Labs, Cornell Tech, KU Leuven)
- **链接**: https://doi.org/10.1145/3658644.3690356

## 一、研究背景与动机

### 1.1 区块链钱包的用户体验困境

传统区块链钱包面临严重的用户体验问题,成为区块链应用普及的主要障碍:

**私钥管理的挑战**:
- 用户需要妥善保管助记词或使用硬件钱包
- 一旦私钥丢失,资产将永久无法找回
- 据估计,7% 的比特币已永久丢失

**托管服务的信任问题**:
- Mt. Gox、FTX、Quadriga 等知名托管平台相继倒闭
- 用户难以信任新兴的托管服务
- 中心化托管违背了区块链去中心化的理念

**现有方案的局限**:
- 直接使用 OAuth 需要引入可信的预言机服务器
- 暴露完整的 JWT payload 会泄露敏感信息(姓名、邮箱等)
- 在公开区块链上,这种隐私泄露尤其严重

### 1.2 核心研究问题

> **能否利用现有的认证系统来管理加密货币钱包,同时不依赖额外的可信第三方?**

zkLogin 给出了肯定的答案。

## 二、zkLogin 核心思想

### 2.1 基本方法

zkLogin 利用 OpenID Connect 规范,该规范被 Google、Facebook、Apple、Microsoft 等主流 OAuth 2.0 提供商广泛支持。

**OpenID Provider (OP) 发放的 JWT 结构**:

```json
// Header
{
  "alg": "RS256",
  "kid": "a987akjasnb",
  "typ": "JWT"
}

// Payload
{
  "sub": "1234567890",      // 用户 ID
  "iss": "google.com",       // 发行者
  "aud": "4074087",          // 应用 ID
  "iat": 1676415809,         // 签发时间
  "exp": 1676419409,         // 过期时间
  "name": "John Doe",
  "email": "john.doe@gmail.com",
  "nonce": "7-VU9fuWeWtgDLHmVJ2UtRrine8"
}

// Signature: RS256(secret_key, base64(header) + "." + base64(payload))
```

### 2.2 朴素方案的问题

一个直接的想法是:

1. 用户登录 OpenID Provider 获取 JWT
2. 将 JWT 发送到区块链
3. 智能合约验证 JWT 签名
4. 使用 JWT 中的 `sub` 标识用户

**致命缺陷**: 这会在公开区块链上暴露用户的所有敏感信息(姓名、邮箱、头像等)。

### 2.3 zkLogin 的解决方案

zkLogin 的核心创新是**将临时密钥对嵌入 nonce**:

```
nonce ← H(vk_u, T_max, r)
```

其中:

- `vk_u`: 临时公钥
- `T_max`: 过期时间
- `r`: 随机数

**zkLogin 签名包含两部分**:

1. **零知识证明 (ZKP)**: 证明 JWT 有效且包含 `vk_u`
2. **传统签名**: 使用 `sk_u` 对交易签名

**关键优势**:

- 单个 ZKP 可重用于多笔交易(摊销成本)
- 敏感信息通过 ZKP 隐藏
- 可安全地将 ZKP 生成委托给服务器(服务器无法获取 `sk_u`)

## 三、系统架构

### 3.1 参与实体

zkLogin 系统涉及四个主要实体:

1. **OpenID Provider (OP)**: Google、Facebook 等,负责签发 JWT
2. **用户**: 持有 OP 账户,可能使用资源受限设备
3. 应用 
   - 前端(FE): 浏览器扩展、移动或 Web 应用
   - 后端(BE): 可选的服务组件
4. **区块链**: 由验证者组成,需支持 ZKP 验证和 JWK 预言机

**信任模型**:

- 应用后端不受信任
- 应用前端可信(代码公开,接受公众审查)
- OP 仅用于活性,非安全性(OP 不是托管方)

### 3.2 地址派生机制

**基本公式**:

```
zkaddr = H(stid, aud, iss, salt)
```

**参数说明**:

- `stid`: 稳定标识符(Stable Identifier),如 `sub`、`email`
- `aud`: 应用的受众标识符(防止恶意应用窃取资产)
- `iss`: OpenID Provider 标识符
- `salt`: 用于隐私保护的盐值(可选但推荐)

**为什么需要 salt?**

当稳定标识符敏感时(如 email),或者 OP 使用公开标识符时(同一用户在不同应用的 `sub` 相同),salt 可防止链上地址与链下身份被关联。

**Subject Identifier 的两种类型**:

- **公开标识符**: Google、Twitch、Slack(同一用户跨应用 `sub` 相同)
- **成对标识符**: Apple、Facebook、Microsoft(每个应用分配唯一 `sub`)

### 3.3 完整工作流程

```
用户 → 应用前端 → OpenID Provider → 区块链

第一步: 获取 JWT
├─ 生成临时密钥对 (sk_u, vk_u)
├─ 计算 nonce = H(vk_u, T_max, r)
├─ 通过 OAuth 流程登录 OP
└─ OP 返回签名的 JWT(包含 nonce)

第二步: 获取 Salt
├─ 从 Salt Service 获取(应用管理)
└─ 或从本地存储获取(用户管理)

第三步: 生成零知识证明
├─ 公开输入: (pk_OP, iss, zkaddr, vk_u, T_max)
├─ 私密见证: (jwt, salt, r)
└─ 证明:
    ├─ H(jwt.stid, jwt.aud, jwt.iss, salt) = zkaddr
    ├─ H(vk_u, T_max, r) = jwt.nonce
    └─ JWT.Verify(pk_OP, jwt) = 1

第四步: 提交交易
├─ 使用 sk_u 签名交易: σ_u ← Sign(sk_u, tx)
├─ 完整签名: (vk_u, T_max, σ_u, π)
└─ 验证者验证:
    ├─ 验证 ZKP
    ├─ 验证 pk_OP 是当前有效密钥
    ├─ 验证传统签名
    └─ 检查 T_max 未过期
```

## 四、Tagged Witness Signature 形式化

### 4.1 密码学原语定义

zkLogin 的核心是一个新的密码学原语: **Tagged Witness Signature (TWS)**。

**与传统签名的区别**:

- 传统签名: 需要长期持有密钥
- TWS: 用有效 witness 替代密钥

**与 Signatures of Knowledge (SoK) 的区别**:

- SoK: 无需密钥,知识即可签名
- TWS: 增加了前向安全性 —— 即使旧 witness 泄露,也无法伪造新 tag 的签名

**语法定义**:

```
Gen(1^λ) → pk
  输入: 安全参数
  输出: 公钥

Sign(t, pk, w, m) → σ
  输入: tag t, 公钥 pk, witness w, 消息 m
  输出: 签名 σ

Verify(t, pk, m, σ) → 0/1
  输入: tag t, 公钥 pk, 消息 m, 签名 σ
  输出: 接受(1)或拒绝(0)
```

### 4.2 安全属性

**完备性 (Completeness)**: 诚实生成的签名能够验证通过。

**不可伪造性 (Unforgeability)**: 即使攻击者获得其他 tag 的 witness,也无法伪造新 tag 的签名。这通过 witness 预言机建模:

- 攻击者可查询任意 tag 的 witness
- 但无法为未查询的 tag 伪造签名

**Witness 隐藏 (Witness Hiding)**: 签名不泄露关于 witness 的信息,实现隐私保护。

### 4.3 zkLogin 的 TWS 实例化

**谓词定义**:

```
P_zkLogin(tag = (pk_OP, iss, zkaddr, T),
          w = (jwt, salt, r, vk_u, sk_u)):
  
  zkaddr = H(jwt.stid, jwt.aud, jwt.iss, salt) ∧
  jwt.iss = iss ∧
  jwt.nonce = H(vk_u, T, r) ∧
  JWT.Verify(pk_OP, jwt) = 1 ∧
  (sk_u, vk_u) is valid key-pair
```

**电路 (Circuit)**:

```
Ckt(zkx = (pk_OP, iss, zkaddr, T, vk_u),
    zkw = (jwt, salt, r)):
  
  // 验证地址派生
  zkaddr = H(jwt.stid, jwt.aud, jwt.iss, salt)
  
  // 验证发行者
  jwt.iss = iss
  
  // 验证 nonce
  jwt.nonce = H(vk_u, T, r)
  
  // 验证 JWT 签名
  JWT.Verify(pk_OP, jwt) = 1
```

## 五、实现与优化

### 5.1 证明系统选择

**选用 Groth16 的原因**:

- 成熟的工具生态系统
- 紧凑的证明尺寸
- 使用 circom DSL 编写电路

**可信设置仪式**:

- Phase 1: 采用 Perpetual Powers of Tau 第 81 轮(80+ 社区贡献)
- Phase 2: 100+ 外部贡献者参与 zkLogin 专用设置
- 安全假设: 只要一个参与者诚实,系统即安全

### 5.2 电路优化

**总约束数**: ~1,100,000 (刚超过 2^20)

**约束分布**:

```
SHA-256 哈希        : 750,000 (66%)
RSA 大整数运算      : 155,000 (14%)
JWT 解析           : 235,000 (20%)
```

#### 5.2.1 JWT 验证

验证 RS256 签名(RSASSA-PKCS1-v1_5 using SHA-256):

1. 使用 SHA-2 哈希 JWT 的 header 和 payload
2. 模幂运算验证: `σ^65537 % p = pad(h)`

**优化**:

- 重用现有的 RSA 验证电路
- 采用高效的模乘技术

#### 5.2.2 JWT 解析优化

**朴素方法的问题**: 完整解析 JSON 需要实现完整的 JSON 语法规则,在 R1CS 中极其低效。

**zkLogin 的优化策略**:

1. **公开输入 Header**:

   - Header 不包含敏感信息
   - 直接作为公开输入,避免 Base64 解码

2. **选择性 Payload 解析**: 基于 JWT payload 的简化特性:

   - Provider 遵循 JSON 规范
   - 用户输入的字符串被正确转义
   - 关注的字段都在顶层
   - JSON 值为字符串或布尔值
   - JSON 键中不含转义引号

3. **Base64 解码**:

   ```
   解码单字符: 73 约束
   完整 payload (L_max=1500 字节): 73 × 1500 ≈ 110k 约束
   ```

4. **字符串切片优化**:

   **朴素算法**:

   ```
   对于每个输出索引 j:
     计算点积: S · O (O[j] = 1)
     总约束: n × m
   ```

   **优化算法**:

   ```
   - 将 16 个 8-bit 元素打包为 128-bit
   - 在打包元素上切片
   - 解包回 8-bit
   - 约束数: 18m + (n×m)/32
   ```

   **性能提升**:

   ```
   n=1600, m=100:
   朴素: 160k 约束
   优化: 33k 约束
   提升: 4.8倍
   ```

5. **JSON 键值对提取**:

   对于字符串 `S = {"sub":"123","aud":"mywallet","nonce":"ajshda"}`:

   ```
   给定起始索引 i 和长度 l:
   1. 切片: S' = S[i:i+l]  // 例如: "sub":"123",
   2. 检查最后字符是 ',' 或 '}'
   3. 定位冒号位置 j
   4. 提取: key = S'[0:j], value = S'[j+1:-1]
   5. 验证首尾是引号
   ```

**安全性考虑**:

攻击者可能尝试过度扩展:

```json
{
  "sub":"1320606","aud":"mywallet",
     ↑---------------↑
  // 提取: "1320606","aud":"mywallet"
}
```

但这不是有效的 JSON 字符串(内部引号未转义),因此不会造成安全威胁。

### 5.3 Salt 管理方案

#### 方案一: 应用管理 Salt Service

**基本实现**:

```
salt = F(k_seed, sub‖aud‖iss)
```

其中 `F` 可以是:

- 哈希函数
- 确定性签名(BLS, EdDSA)

**优点**:

- 用户体验简单
- 无需额外记忆

**隐私增强选项**:

| 方案          | 去链接性 | JWT 隐私 | 说明                                |
| ------------- | -------- | -------- | ----------------------------------- |
| 明文存储      | ❌        | ❌        | 最简单但无隐私                      |
| TEE (Enclave) | ✅        | ✅        | 使用 AWS Nitro/Intel SGX            |
| Plain MPC     | ✅        | ❌        | t-out-of-n 门限签名                 |
| MPC + ZK      | ✅        | ✅        | 用户生成 n 个 ZKP,秘密分享 JWT 字段 |

**TEE 实现细节**:

```
1. 在 Nitro enclave 中生成 k_seed
2. 使用 AWS KMS 管理,仅特定 enclave 可解密
3. 用户提交 JWT 认证
4. Enclave 派生并返回 salt
5. 备份: 使用 Shamir 秘密分享给可信委员会
```

**MPC + ZK 方案**:

```
Setup:
  n 个节点秘密分享 k_seed
  F 设为 MPC 友好的 PRF (如 MiMC)

Request:
  用户计算 n 个 ZKP π_i
  每个 π_i 输入 JWT,输出秘密分享的 sub_i, aud_i, iss

Response:
  节点验证 ZKP
  联合计算 salt = F(k_seed, sub‖aud‖iss)
```

#### 方案二: 用户管理

**优点**:

- 完全去中心化
- 不依赖任何应用

**实现策略**:

1. 存储在本地设备(浏览器 local storage, 设备 enclave)
2. 使用 Passkeys 跨设备同步
3. 通过确定性签名派生: `salt = Sign(passkey, email)`

**挑战**:

- 设备丢失恢复
- 跨设备/跨浏览器同步
- 需要额外的用户教育

### 5.4 ZKP 生成策略

#### 策略一: 本地生成

**优点**: 最大化隐私

**挑战**:

- 在浏览器中生成约 1M 约束的证明可能崩溃或延迟
- 移动设备性能受限

#### 策略二: 普通委托

**流程**:

```
用户 → ZK Service:
  发送完整 witness (jwt, salt, r)
  
ZK Service:
  生成 ZKP
  返回证明 π
  
用户:
  验证 π
  使用 sk_u 完成签名
```

**安全性**: ✅ (ZK Service 无法获取 sk_u)
 **隐私性**: ❌ (ZK Service 可计算 zkaddr)

#### 策略三: 完全隐私委托

**方法 1 - 委员会**:

```
将 ZKP 生成委托给节点委员会
只要少数节点不串谋,witness 保密
```

**方法 2 - Enclave**:

```
在 TEE 内生成 ZKP
隐私依赖于 TEE 安全性
```

## 六、性能评估

### 6.1 实验配置

**Salt Service**:

- 平台: AWS Nitro Enclave on m5.xlarge
- 配置: 4 vCPU, 16GB RAM

**ZK Service**:

- 平台: Google Cloud n2d-standard-16
- 配置: 16 vCPU, 64GB RAM
- 实现: rapidsnark (C++/Assembly Groth16 prover)

### 6.2 性能指标

| 操作       | zkLogin | Ed25519  |
| ---------- | ------- | -------- |
| 获取 Salt  | 0.2s    | N/A      |
| 生成 ZKP   | 2.78s   | N/A      |
| 签名验证   | 2.04ms  | 56.3μs   |
| 端到端确认 | 3.52s   | 120.74ms |

**ZKP 生成详细分解**:

```
Circom witness calculator: 550.05 ± 22.42 ms
Groth16 证明生成:       2.1 ± 0.15 s
其他开销(验证、缓存):    ~130 ms
───────────────────────────────────────
总计:                   2.78 ± 0.25 s
```

**内存使用**:

```
峰值 RAM: 1.19 GB
平均 RAM: 0.82 GB
策略: 每台机器一次处理一个请求,横向扩展
```

### 6.3 对验证者的影响

**压力测试配置**:

```
8 个验证者节点
每节点: 8 cores, 128GB RAM
地理分布: New York + Los Angeles
负载: 1000 TPS
```

**吞吐量对比**:

```
Ed25519:  850 TPS
zkLogin:  750 TPS
下降:     ~11%
```

**原因分析**: 签名验证只是验证者工作的一小部分,因此影响有限。

### 6.4 用户体验优化

**延迟感知缓解**:

1. **ZKP 重用**:

   ```
   首次交易: 3.52s (包含 ZKP 生成)
   后续交易: ~120ms (重用 ZKP)
   ```

2. **预取策略**:

   ```
   在用户签名前,后台预先获取 ZKP
   隐藏额外延迟
   ```

3. **会话管理**:

   ```
   单个会话内重用临时密钥对
   只在会话开始时生成一次 ZKP
   ```

## 七、安全分析

### 7.1 形式化安全证明

**定理 1 (不可伪造性)**:

```
给定:
  - Π 满足知识健全性
  - JWT 和 Sig 满足 EUF-CMA 安全
  - H(·) 是抗碰撞哈希函数

则: ΣzkLogin 满足不可伪造性
```

**证明思路** (游戏序列):

```
Game 0: 真实攻击
Game 1: 检查 vk_u 是否被重用(依赖 Sig 安全性)
Game 2: 提取 witness (依赖 NIZK 知识健全性)
Game 3: 检查 JWT 是否伪造(依赖 JWT 签名安全性)

在 Game 3 中攻击者永不获胜 → 原协议安全
```

**定理 2 (Witness 隐藏)**:

```
给定:
  - Π 满足零知识性

则: ΣzkLogin 满足 witness 隐藏
```

**定理 3 (zkLogin 安全性)**:

```
ΣzkLogin 不可伪造 → zkLogin 安全
```

**定理 4 (去链接性)**:

```
给定:
  - ΣzkLogin 满足 witness 隐藏
  - zkaddr 是对 claimset 的隐藏承诺

则: zkLogin 满足去链接性
```

### 7.2 各组件安全性分析

#### OpenID Provider

**角色**: 用于活性,非安全性

**威胁场景**:

```
如果 JWT 单独可获取 salt:
  恶意 OP 可签任意 JWT → 破坏安全性

缓解措施:
  ✅ Salt 由用户管理
  ✅ Salt Service 需第二因素(TOTP)
```

**隐私分析**:

```
去链接性:
  ✅ JWT 不足以获取 salt → OP 无法追踪交易
  ⚠️ OP 知道用户使用了哪个应用(OAuth 固有)
  
侧信道:
  ⚠️ JWT 预取和 ZKP 重用可掩盖时序关联
```

#### 应用

**活性**: 应用不可用 → 资产锁定

**缓解**:

```
设置多签:
  1-out-of-2 Multi-sig 在 App1 和 App2 之间
  任一应用可用即可访问资产
```

**安全性**: ✅ 恶意应用无法窃取资产

#### Salt Service

**安全性**: ✅ 无 sk_u 访问权限,无法签名

**隐私**:

```
明文存储:     ❌❌ 无隐私
TEE:          ✅✅ 去链接 + JWT 隐私
Plain MPC:    ✅❌ 去链接,JWT 可见
MPC + ZK:     ✅✅ 完全隐私
```

#### 区块链

**公开信息**:

- zkLogin 地址
- 发行者标识符 (iss)
- ZKP 和临时公钥

**隐藏信息**:

- 稳定标识符 (sub, email)
- 受众标识符 (aud)
- Salt
- JWT 其他字段

### 7.3 威胁模型总结

| 实体         | 安全影响                       | 隐私影响                   | 缓解措施                   |
| ------------ | ------------------------------ | -------------------------- | -------------------------- |
| OP           | JWT 单独获取 salt 时可破坏安全 | JWT 单独获取 salt 时可追踪 | 用户管理 salt 或多因素认证 |
| 应用         | 仅影响活性                     | 取决于 salt 管理           | 多签缓解活性问题           |
| Salt Service | 无影响(无 sk_u)                | TEE/MPC 保护               | 使用 TEE 或 MPC            |
| ZK Service   | 无影响(无 sk_u)                | 普通委托泄露               | Enclave 或委员会           |
| 区块链       | N/A                            | 仅 iss 可见                | Salt 保护其他字段          |