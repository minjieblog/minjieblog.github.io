---
title: "Codex 完整使用指南：安装、控制、代码管理与自动化"
date: 2026-09-02T11:12:43+08:00
lastmod: 2026-09-02T11:12:43+08:00
draft: false
tags: ["Codex", "AI 编程", "CLI", "OpenAI", "开发工具"]
categories: ["文档介绍"]
summary: "系统介绍 Codex 的安装与基本操作、功能模块、控制方式、计划模式、代码管理、记忆系统、插件与自动化，以及 Skills 的创建和使用。"
description: "一份面向实际开发的 Codex 中文参考手册，覆盖 CLI、IDE、桌面端、权限配置、AGENTS.md、Git Worktree、MCP、Hooks、计划任务与 Skills。"
toc: true
showToc: true
TocOpen: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
author: "Minjie"
comments: true
---

> 本文整理于 2026 年 9 月。Codex 更新较快，命令、界面和功能可用性应以当前客户端的 `/` 菜单、`codex --help` 和 OpenAI 官方文档为准。

Codex 是能够在真实开发环境中工作的编程智能体。它可以理解代码仓库、修改文件、执行命令、运行测试、审查差异，并在获得授权后使用浏览器、MCP 服务和其他外部工具。使用 Codex 的关键不是寻找一条“万能提示词”，而是给它清晰的任务、合适的权限、可靠的项目规则和可验证的完成标准。

## 一、Codex 安装与基本使用

### 1.1 选择使用方式

| 使用方式 | 适合场景 | 主要特点 |
|---|---|---|
| Codex CLI | 终端开发、服务器环境、脚本调用 | 离代码、Git 和构建命令最近 |
| IDE 扩展 | 日常编码、局部修改、查看差异 | 能结合当前文件、选区和编辑器上下文 |
| ChatGPT 桌面应用中的 Codex | 多项目管理、并行任务、计划任务、可视化审查 | 支持 Local、Worktree 和 Cloud 等环境 |
| Codex Cloud | 后台执行、耗时测试、远程仓库任务 | 在配置好的云端环境中独立运行 |

简单任务可以直接从 IDE 或 CLI 开始；需要并行任务、Worktree 或计划任务时，桌面应用更方便；需要无人值守执行时，再考虑 Cloud 或 `codex exec`。

### 1.2 安装 CLI

先确认本机已经安装 Node.js 和 npm：

```bash
node --version
npm --version
```

使用 npm 全局安装或更新 Codex CLI：

```bash
npm install -g @openai/codex@latest
codex --version
codex --help
```

如果终端提示找不到 `codex`，先执行 `npm config get prefix` 查看全局安装目录，并确认该目录已加入 `PATH`。修改 `PATH` 后要重新打开终端。公司设备如果限制全局安装，应遵循组织提供的安装方式，不要通过未知安装包绕过限制。

### 1.3 登录与首次启动

进入项目目录后启动 Codex：

```bash
cd <项目目录>
codex

# 从任意位置指定工作目录
codex -C path/to/project
```

首次启动时按界面提示登录。通常可以使用 ChatGPT 账号；模型、额度和部分功能取决于账号套餐、客户端版本与组织策略。API Key 属于另一种认证方式，不要把 Key 写进代码、提示词或提交到 Git。

启动后建议先运行 `/status`，确认当前工作目录、模型、权限模式、Git 分支和可用工具是否符合预期。

### 1.4 交互式使用

```bash
# 打开空白会话
codex

# 带着第一条任务进入
codex "阅读项目并说明启动方式、测试入口和主要模块"
```

一次完整的开发对话可以这样推进：

```text
先只读分析登录失败的原因，不要修改文件。给出调用链和证据。

现在实施最小修复，保持现有 API 兼容，并添加回归测试。

运行相关测试和类型检查，再审查最终 diff，列出剩余风险。
```

连续补充要求比一次写出超长提示词更容易控制。方向明显变化时，应创建新会话或派生会话，避免旧任务约束干扰新任务。

### 1.5 非交互式使用

`codex exec`（简写为 `codex e`）适合一次性任务、脚本和 CI：

```bash
codex exec "运行测试，解释失败原因，不修改代码"
codex e "审查当前工作区改动，按严重程度列出问题"
echo "总结这个仓库的模块边界" | codex exec -
```

自动化调用应明确输入范围、是否允许修改、验证命令、输出格式、退出条件，以及网络和外部写入权限。需要机器解析结果时，先用 `codex exec --help` 查看当前版本支持的结构化输出选项。

### 1.6 恢复、派生与压缩会话

```bash
codex resume
codex resume --last
codex exec resume --last "继续完成剩余测试"
codex fork
```

会话很长时可以使用 `/compact` 压缩上下文。压缩后最好重申当前目标、已完成内容、剩余任务和验收标准，避免重要约束只存在于早期对话中。

### 1.7 常用斜杠命令

在输入框中键入 `/` 可以查看当前版本的完整命令列表。

| 命令 | 用途 |
|---|---|
| `/status` | 查看目录、模型、权限和上下文状态 |
| `/model` | 查看或切换可用模型与推理强度 |
| `/permissions` | 调整当前会话的权限方式 |
| `/plan` | 进入或退出计划模式 |
| `/review` | 审查工作区或指定范围的代码变更 |
| `/skills` | 查看和选择可用 Skills |
| `/mcp` | 查看 MCP 服务和工具状态 |
| `/memories` | 控制当前会话如何使用或生成记忆 |
| `/compact` | 压缩长会话上下文 |
| `/init` | 为代码仓库生成项目指导文件初稿 |
| `/help` | 查看帮助 |

不同客户端和版本的命令可能不同，以实际 `/` 菜单为准。

## 二、Codex 功能模块详解

### 2.1 仓库理解与代码搜索

Codex 可以读取目录结构、配置文件、依赖、测试和调用关系，再根据任务缩小范围。第一次接触仓库时，推荐先让它说明：应用从哪里启动、主要目录负责什么、数据如何流动、验证命令有哪些，以及哪些文件禁止修改。

```text
只读分析当前仓库。说明启动入口、核心模块、主要数据流、测试方式，
并指出最容易引发回归的三个区域。引用具体文件和符号，不要修改代码。
```

### 2.2 文件编辑与命令执行

获得相应权限后，Codex 可以编辑文件、运行格式化工具、启动测试、读取日志并根据结果继续修正。为了让修改可控，任务中应明确：

- 允许修改的目录和接口；
- 必须保持不变的行为；
- 是否允许增加依赖或修改锁文件；
- 必须执行哪些检查；
- 什么结果才算完成。

### 2.3 测试、调试与验证

Codex 可以运行项目已有的单元测试、集成测试、类型检查、Lint 和构建，也可以根据错误信息追踪调用链。可靠的修复流程通常是：

```text
复现问题 → 收集证据 → 定位根因 → 添加失败测试 → 实施最小修复
→ 运行相关检查 → 审查 diff → 报告剩余风险
```

不要只要求“确保正确”。应该给出可执行命令，或要求 Codex 先从项目配置和 CI 文件中找出真实使用的命令。

### 2.4 代码审查

Codex 可以审查未提交改动、分支差异或 Pull Request。审查任务最好给出比较基线和关注点：

```text
审查当前分支相对 main 的改动。优先寻找数据丢失、权限绕过、
并发问题、兼容性回退和缺失测试。按严重程度输出，并标明文件位置。
```

审查结果是额外检查，不替代测试、静态分析和人工审批。安全、支付、权限、数据迁移等高风险变更仍应由具备相应职责的人复核。

### 2.5 浏览器、网络与外部信息

在客户端和权限允许时，Codex 可以使用网页搜索、浏览器或连接器获取最新资料，也可以检查页面状态、控制台错误和网络请求。需要外部资料时应优先使用官方来源，并区分仓库事实、实际运行结果、外部资料和模型推断。

网络访问和本地文件写入通常是两个独立权限。能够修改工作区不代表能够联网，能够联网也不代表可以向外部系统写数据。

### 2.6 子智能体与并行工作

对于可以独立处理的任务，Codex 可以把搜索、实现、测试或审查交给子智能体并行完成。例如分别检查前端、后端和测试影响，或在互不重叠的模块中实现独立功能。

强依赖、会修改同一文件或需要连续决策的步骤不适合强行并行。子智能体通常继承主会话的权限边界，因此应在开始前确认权限模式和任务范围。

### 2.7 扩展能力总览

| 扩展方式 | 解决的问题 | 典型用途 |
|---|---|---|
| `AGENTS.md` | 当前仓库必须遵守什么 | 构建命令、架构规范、完成标准 |
| `config.toml` | 设置运行方式和默认参数 | 模型、权限、MCP、功能开关 |
| Memory | 恢复有用的历史背景 | 个人偏好、反复出现的上下文 |
| Skill | 复用一套完成任务的方法 | 发布检查、迁移流程、文档更新 |
| MCP | 连接外部工具与实时数据 | GitHub、设计工具、内部文档、数据库 |
| Plugin | 分发 Skills、连接器和配置 | 团队级可安装能力包 |
| Hook | 在生命周期节点机械执行检查 | 密钥检查、日志记录、结束时验证 |
| Automation | 按时间或事件重复执行流程 | 定期扫描、发布说明、CI 汇总 |

## 三、Codex 控制与引导

### 3.1 控制层次

| 内容 | 放置位置 | 作用范围 |
|---|---|---|
| 本次任务的目标和限制 | 当前提示词 | 当前任务或会话 |
| 仓库长期规范 | `AGENTS.md` | 当前目录及适用子目录 |
| 个人或项目运行参数 | `config.toml` | 用户或受信任项目 |
| 跨项目重复流程 | Skill | 被调用的任务 |
| 可恢复的历史背景 | Memory | 后续符合条件的会话 |
| 强制组织策略 | 管理员配置、`requirements.toml` | 受管理设备或组织 |

显式任务要求通常比记忆更可靠；强制规则应写进版本控制中的 `AGENTS.md` 或组织策略，不能只依赖记忆。

### 3.2 写好任务描述

高质量任务至少包含目标、范围、约束、步骤和验收方式：

```text
目标：修复用户退出后刷新页面仍显示已登录的问题。
范围：只修改 auth 和 session 模块，不改变公开 API。
要求：
1. 先复现并说明根因；
2. 添加一个修复前会失败的回归测试；
3. 实施最小修改，不顺手重构；
4. 运行相关测试、类型检查和 Lint；
5. 审查最终 diff，列出修改文件和剩余风险。
```

如果任务仍然模糊，可以先要求 Codex 提问、列出假设或进入计划模式。不要在提示词中粘贴密码、Token、私钥或真实生产数据。

### 3.3 使用 `AGENTS.md` 固化项目规则

`AGENTS.md` 适合记录团队希望每次任务都遵守的规则。根目录文件适用于整个仓库，更深目录中的文件可以为子树增加更具体的要求。

```markdown
# AGENTS.md

## 项目结构
- `src/`：业务代码
- `tests/`：自动化测试
- `docs/`：用户文档

## 开发规则
- 使用 TypeScript strict 模式。
- 优先修改现有模块，不增加不必要的依赖。
- 未经任务要求，不修改生成文件和锁文件。

## 验证命令
- 单元测试：`npm test`
- 类型检查：`npm run typecheck`
- 格式检查：`npm run lint`

## 完成标准
- 新行为必须有测试。
- 不得通过跳过或删除失败测试使检查通过。
- 最终说明改动、验证结果和未解决风险。
```

适合写入的内容包括目录职责、命名规范、构建测试命令、禁止修改区域和完成标准。不适合写入秘密、某次任务的临时要求或无法执行的口号。

### 3.4 配置 `config.toml`

用户级配置通常位于 `~/.codex/config.toml`；受信任项目可以使用 `.codex/config.toml` 添加项目级覆盖。

```toml
model = "<可用模型名称>"
model_reasoning_effort = "medium"
approval_policy = "on-request"
sandbox_mode = "workspace-write"

[sandbox_workspace_write]
network_access = false
```

一般优先级是：一次性 CLI 参数高于项目配置，项目配置高于所选配置方案，配置方案高于用户配置。受管理设备还可能受到管理员策略限制。项目级配置只有在项目被信任时才会加载。

### 3.5 配置方案与临时覆盖

配置方案是放在 Codex Home 中的独立文件。例如创建 `~/.codex/review.config.toml`：

```toml
approval_policy = "never"
sandbox_mode = "read-only"
```

再创建 `~/.codex/develop.config.toml`：

```toml
approval_policy = "on-request"
sandbox_mode = "workspace-write"
```

```bash
codex --profile review
codex --profile develop
codex -c model_reasoning_effort='"high"'
codex exec -c sandbox_mode='"read-only"' "审查当前改动"
```

配置键会随版本演进，修改前应对照当前配置参考，并使用 `codex --help` 检查 CLI 参数。

### 3.6 权限、沙箱与审批

| 模式 | 行为 | 适合场景 |
|---|---|---|
| 只读 | 允许分析和部分只读检查，不修改文件 | 理解仓库、诊断、审查 |
| 工作区写入 | 可以修改当前工作区，越界操作受限 | 日常实现与修复 |
| 请求审批 | 越过边界、联网或高风险操作前暂停 | 推荐的通用方式 |
| 完全访问 | 接近完整系统权限 | 仅用于明确需要且隔离良好的环境 |

删除、覆盖、推送、创建 PR、部署、发送消息和修改外部数据都需要特别复核。允许编辑代码，不等于自动授权发布代码或影响其他人。

### 3.7 验收和反馈

至少检查 `git diff --stat`、完整 diff、验证命令结果、失败检查的解释，以及外部写入是否经过授权。反馈时指出具体偏差，例如：

```text
保留当前 API，不要新增 endpoint。测试缺少未登录场景，请补充后只运行 auth 相关测试。
```

## 四、Codex 计划模式

### 4.1 什么是计划模式

计划模式让 Codex 在实施前先收集上下文、提出必要问题、识别约束并整理可执行方案，适合复杂、含糊、跨模块或高风险任务。

在支持的界面中使用 `/plan` 进入。部分客户端支持 `Shift` + `Tab` 切换，具体以当前界面提示为准。计划模式的重点是形成决策和步骤，不是立刻修改代码；如果只希望分析，应明确写出“不要修改文件”。

### 4.2 适合与不适合的场景

适合先计划：

- 新功能横跨多个模块或服务；
- 需要数据库迁移、接口兼容或分阶段发布；
- 重构范围大，需要保持外部行为不变；
- 需求只有粗略想法，需要澄清边界；
- 风险高，必须先确定回滚和验证方案。

明确的小范围文案修改、简单样式调整、单一命令或已有完整方案的任务，通常不需要单独计划。

### 4.3 可执行计划的组成

好的计划应明确：当前状态、目标和非目标、关键假设、涉及模块、实施顺序、依赖关系、测试与兼容方案、回滚方式、待用户决定的问题，以及各阶段验收标准。

```text
进入计划模式，先阅读现有支付和订单模块，不修改文件。
为“订单部分退款”制定实施计划，重点说明：
- 状态机和数据库字段变化；
- 旧 API 的兼容方式；
- 幂等与并发风险；
- 数据迁移、灰度和回滚步骤；
- 单元测试与集成测试范围。
不确定的业务规则请列为待确认问题，不要自行假设。
```

### 4.4 从计划进入实施

计划确认后，给出清晰边界：

```text
按已确认计划实施第 1 和第 2 阶段。不要执行生产迁移，
不要推送远程分支。每阶段完成后运行对应测试并更新进度。
```

实施中如果发现关键假设错误，应暂停并更新计划，而不是为了保持原计划继续扩大修改。

### 4.5 `PLAN.md` 与 ExecPlans

短任务的计划保留在会话中即可。跨多天、多个会话或多人协作的任务，可以保存为 `PLAN.md`，或按照仓库约定使用 ExecPlan 文档。

长期计划应记录任务背景、最终状态、里程碑、关键决策、当前阻塞、验证结果和下一步。计划文档要持续更新，避免会话已经改变方向而仓库计划仍是旧版本。

## 五、Codex 代码管理

### 5.1 修改前确认状态

```bash
git status --short
git branch --show-current
git log -5 --oneline
```

工作区已有未提交改动时，应把它们视为用户工作，不要擅自清理、覆盖、暂存或回退。任务范围与现有改动重叠时，应先说明风险。

### 5.2 分支策略

```bash
git switch -c fix/session-refresh
```

推荐为独立任务建立独立分支，并保持提交主题单一。Codex 可以协助拟定分支名和提交信息，但创建提交、推送远程和发起 PR 是不同授权层次。

### 5.3 Local、Worktree 与 Cloud

| 环境 | 文件位置 | 适合场景 |
|---|---|---|
| Local | 直接使用当前本地项目 | 单任务、需要频繁在 IDE 中参与 |
| Worktree | 隔离的 Git 工作树 | 并行任务、后台任务、不干扰本地修改 |
| Cloud | 远程环境中的独立副本 | 耗时任务、远程执行、云端 PR 流程 |

Worktree 共享 Git 元数据，但有独立工作目录。同一个分支不能同时在两个 Worktree 中检出。被 Git 忽略的本地文件默认不会自动出现；确需复制时可按客户端支持方式使用 `.worktreeinclude`，但不要随意复制生产密钥。

### 5.4 查看和审查改动

```bash
git diff --stat
git diff
git diff --staged
git diff main...HEAD
```

在 Codex 中可运行 `/review`，也可以附带重点：

```text
/review Focus on authentication boundaries, error handling, and missing tests.
```

发现问题后先修复，再重新运行相关测试和 `/review`，不要只修改描述或忽略结论。

### 5.5 提交与 Pull Request

提交前应确保改动范围正确、验证已运行、没有秘密和临时文件，并让提交信息说明修改原因。

```bash
git add <明确的文件路径>
git diff --staged
git commit -m "fix: clear stale session after logout"
```

避免在混有用户改动时无脑使用 `git add .`。PR 描述建议包括背景、实现摘要、验证结果、截图或迁移说明，以及剩余风险。

### 5.6 回退与恢复

优先使用可恢复的方法：先看 diff，只恢复明确文件或补丁块；已共享提交优先通过新提交修复或 `git revert` 回退。不要用 `git reset --hard`、强制推送或批量删除处理不清楚的状态。

### 5.7 推荐工作流

```text
确认仓库状态 → 建立分支或 Worktree → 只读分析 → 明确验收标准
→ 实施最小改动 → 运行验证 → 审查 diff → 人工确认
→ 提交 → 推送 → 创建 PR → 根据审查继续修改
```

风险越高，越不应把实现、提交、推送和部署合并成一次不可见的自动操作。

## 六、Codex 记忆系统

### 6.1 记忆解决什么问题

Codex 记忆用于把历史会话中的有用背景带到后续任务，例如稳定偏好、常用工作方式或反复出现的项目背景。它用于减少重复说明，不取代项目文档和强制规则。

- 必须执行的仓库规则：`AGENTS.md`；
- 可复用的任务方法：Skill；
- 当前任务要求：提示词；
- 有帮助但不是强制规则的历史背景：Memory。

### 6.2 启用本地记忆

本地记忆默认可能处于关闭状态，可在支持的桌面应用设置中开启，或在 `config.toml` 中配置：

```toml
[features]
memories = true

[memories]
generate_memories = true
use_memories = true
disable_on_external_context = true
```

| 配置 | 作用 |
|---|---|
| `generate_memories` | 允许符合条件的会话成为记忆输入 |
| `use_memories` | 允许未来会话使用已有记忆 |
| `disable_on_external_context` | 使用 MCP、网页搜索等外部上下文时不生成记忆 |

记忆配置仍可能演进，实际键值应以当前配置参考为准。

### 6.3 按会话控制

在支持的 TUI 或桌面界面中使用 `/memories`，分别决定当前会话能否读取已有记忆，以及能否作为未来记忆的来源。会话级选择不会自动改变全局配置。

处理客户代码、敏感调查或不希望长期保留的内容时，应关闭相应能力。

### 6.4 存储位置与生成方式

本地记忆通常存放在：

```text
~/.codex/memories/
```

其中可能包含摘要、持久条目、近期输入和支持证据。这些是自动生成的状态数据，可以排障时检查，但不应把手工编辑当作主要管理方式。

记忆往往在会话空闲后后台整理，并不保证结束对话后立刻出现。短会话、仍在活动或不符合配置条件的会话可能不会生成记忆。

### 6.5 适合与不适合记忆的内容

适合：稳定输出偏好、多次任务都会用到的非敏感背景、常见工具链和验证习惯。

不适合：密码和 Token、必须绝对执行的合规规则、高频变化的外部事实、仅对当前任务有效的临时决定，以及未经核实的推断。

显式提示和当前仓库事实应优先于旧记忆。发现冲突时，应重新读取项目并以当前证据为准。

### 6.6 隐私与分享

不要把秘密交给记忆系统。即使客户端会尝试移除生成字段中的秘密，在分享 `~/.codex`、排障包或记忆文件前仍应人工检查。

ChatGPT 网页记忆与本地 Codex 记忆不是同一个存储层。IDE 扩展通常使用其连接的 Codex 主机上的本地记忆，因此更换电脑或远程主机后，可用记忆可能不同。

## 七、Codex 扩展能力概览

### 7.1 Plugin、MCP、Hook、Automation 与 Skill

| 能力 | 核心职责 | 是否连接外部系统 |
|---|---|---|
| Plugin | 可安装、可分发的能力包 | 可以包含连接器或 MCP |
| MCP | 把实时工具和数据暴露给 Codex | 通常是 |
| Hook | 在生命周期事件执行脚本或工具 | 可选 |
| Automation | 按时间或事件启动任务 | 取决于任务内容 |
| Skill | 定义任务应该如何完成 | 本身不一定连接外部系统 |

一个 Plugin 可以打包 Skills、连接器、MCP 配置、Hooks、资源和计划任务模板。Skill 负责“方法”，MCP 负责“能力与数据”，Automation 负责“何时运行”。

## 八、Codex 与 MCP：连接外部工具和上下文

MCP（Model Context Protocol，模型上下文协议）是一套让 AI 客户端连接外部工具和数据的标准协议。在 Codex 中，MCP 用来扩展可调用的工具与可访问的上下文：Codex 作为 Host 管理会话、模型、权限和审批，并通过 MCP Client 发现、调用 MCP Server 提供的能力。它解决的是“Codex 如何以统一、可描述、可授权的方式使用模型之外的能力”。

例如，连接 MCP Server 后，Codex 可以在授权范围内查询团队文档、读取设计稿、搜索代码托管平台、操作浏览器、查询数据库，或调用内部业务服务。如果数据已经在当前仓库中，直接读取文件通常更简单；如果只是查询一次公开信息，网页搜索可能更合适；需要稳定、结构化或带身份认证的外部能力时，才值得接入 MCP。

### 8.1 Codex 中的 MCP 架构

```text
用户
  ↓ 提出任务
Codex（Host）
  ↓ 管理模型、权限和会话
MCP Client
  ↓ 通过协议连接
MCP Server
  ↓ 调用真实系统
文档、浏览器、GitHub、数据库、内部 API……
```

三个主要角色：

- Host：承载会话、模型、审批、沙箱和用户界面；
- MCP Client：负责连接、能力发现、请求与响应；
- MCP Server：描述并执行外部能力，返回结构化结果。

一个 Codex Host 可以同时连接多个 MCP Server，一个 Server 也可以提供多个工具。典型调用流程如下：

1. Codex 根据配置启动本地 Server，或连接远程 Server；
2. 双方初始化并交换支持能力；
3. Codex 获取 Server 提供的工具和说明；
4. 用户提出任务，模型判断是否需要工具；
5. 客户端按照审批策略直接调用或请求确认；
6. Server 执行操作并返回结果；
7. 模型结合工具结果继续推理。

模型不能借助 MCP 任意执行操作，只能从客户端实际开放的工具中选择，并传入符合工具定义的参数。

### 8.2 Codex 可使用的 MCP 能力

从协议概念上看，MCP 可以围绕以下能力组织服务：

| 能力 | 含义 | 示例 |
|---|---|---|
| Tools | 可调用的操作 | 搜索文档、读取 Issue、创建记录 |
| Resources | 可读取的上下文资源 | 文件、文档、数据库条目 |
| Prompts | Server 提供的提示模板 | 固定格式的审查或分析流程 |
| Instructions | 整个 Server 的使用说明 | 工具顺序、限制、速率规则 |

客户端不一定支持协议中的全部能力。对 Codex 而言，应以 `/mcp` 实际显示的工具和当前官方说明为准。

工具描述会直接影响模型能否正确选用工具。描述应明确说明工具做什么、何时使用、所需参数、是否写入外部状态、失败情况和返回内容。例如，“按订单 ID 读取订单摘要，只读，不返回支付凭据”就比“处理订单”更安全、明确。

Codex 可以读取 Server 初始化时返回的 `instructions`。Server 维护者可在其中说明跨工具流程、操作限制和速率限制。最重要的规则应放在开头，并让前几百个字符可以独立表达核心约束。Instructions 不能替代客户端审批和服务端权限校验。

### 8.3 Codex 连接 MCP Server 的方式

Codex 支持两种常见连接方式：

| 连接方式 | 工作原理 | 适合场景 |
|---|---|---|
| STDIO | Codex 启动本地进程，通过标准输入输出通信 | 本地开发工具、npm/Python 包、个人工作流 |
| Streamable HTTP | Codex 通过 URL 访问远程服务 | 团队服务、SaaS、OAuth、多用户共享 |

STDIO 无需开放端口，配置简单，但安装它相当于在本机执行该 Server 的程序，必须审查来源和依赖。Streamable HTTP 适合集中升级、监控和权限管理，生产环境应使用 HTTPS；本机回环地址调试除外。

### 8.4 Codex MCP 的配置位置

Codex 将 MCP 配置放在 `config.toml` 中：

```text
用户级：~/.codex/config.toml
项目级：<repo>/.codex/config.toml
```

用户级配置适合个人跨项目使用的服务；项目级配置适合当前仓库共享的服务。出于安全考虑，只有项目被信任时，Codex 才加载项目级 `.codex` 配置。

连接到同一 Codex Host 的桌面应用、CLI 和 IDE 扩展可以共享 MCP 配置。ChatGPT Web 不会直接读取本机 `~/.codex/config.toml`。

### 8.5 使用 Codex CLI 添加和检查 Server

```bash
# 添加本地 STDIO Server
codex mcp add <server-name> -- <server-command>

# 携带环境变量
codex mcp add <server-name> --env TOKEN=<value> -- <server-command>

# 查看配置和帮助
codex mcp list
codex mcp --help

# 登录支持 OAuth 的远程 Server
codex mcp login <server-name>
```

PowerShell 多行命令使用反引号，而不是 Bash 的反斜杠：

```powershell
codex mcp add docs-server `
  --env DOCS_TOKEN=$env:DOCS_TOKEN `
  -- npx -y <mcp-package>
```

直接在命令行传入秘密可能进入终端历史。更稳妥的方式是先设置环境变量，再在配置中只引用变量名。

进入 Codex 后使用 `/mcp`，检查 Server 是否启用、是否初始化成功、是否需要 OAuth，以及实际提供了哪些工具。

### 8.6 为 Codex 配置 STDIO Server

```toml
[mcp_servers.local_docs]
command = "npx"
args = ["-y", "<mcp-package>"]
cwd = "C:/path/to/project"
env_vars = ["DOCS_TOKEN"]
startup_timeout_sec = 20
tool_timeout_sec = 60
enabled = true
required = false

[mcp_servers.local_docs.env]
LOG_LEVEL = "info"
LANGUAGE = "zh-CN"
```

| 字段 | 作用 |
|---|---|
| `command` | 启动 Server 的程序，STDIO 必需 |
| `args` | 传给程序的参数数组 |
| `cwd` | Server 启动时的工作目录 |
| `env` | 直接为 Server 设置环境变量 |
| `env_vars` | 从当前环境转发指定变量 |
| `startup_timeout_sec` | Server 初始化超时 |
| `tool_timeout_sec` | 单次工具调用超时 |
| `enabled` | 是否启用 Server |
| `required` | 初始化失败时是否让 Codex 启动失败 |

`env` 适合非敏感固定配置；真实 Token 应通过 `env_vars` 引用，不能写进可提交文件。可选辅助工具通常不应设置 `required = true`，否则临时故障可能阻止整个 Codex 会话启动。

### 8.7 为 Codex 配置 Streamable HTTP 与 OAuth

```toml
[mcp_servers.team_tools]
url = "https://mcp.example.com/mcp"
bearer_token_env_var = "TEAM_MCP_TOKEN"
http_headers = { "X-Region" = "cn-east" }
env_http_headers = { "X-Workspace-Token" = "WORKSPACE_TOKEN" }
startup_timeout_sec = 20
tool_timeout_sec = 60
enabled = true
```

| 字段 | 作用 |
|---|---|
| `url` | 远程 MCP 地址，HTTP 模式必需 |
| `auth` | 认证方式 |
| `bearer_token_env_var` | 从环境变量读取 Bearer Token |
| `http_headers` | 固定 HTTP Header，仅放非敏感值 |
| `env_http_headers` | 从环境变量读取 Header 值 |

远程 Server 支持 OAuth 时运行：

```bash
codex mcp login <server-name>
```

普通使用者通常只需在浏览器中确认正确的服务域名、账号和授权范围。授权前要检查 Scope，避免把只读查询授权成写入、删除或管理员权限。

认证只回答“当前用户是谁”，授权才决定“这个用户可以做什么”。即使 Token 有效，MCP Server 仍必须在服务端校验每次操作，不能只相信模型或客户端参数。

### 8.8 Codex 的工具白名单与审批

工具很多时应只开放任务需要的能力：

```toml
[mcp_servers.browser_tools]
url = "http://127.0.0.1:3000/mcp"
enabled_tools = ["open_page", "read_console", "screenshot"]
disabled_tools = ["screenshot"]
default_tools_approval_mode = "writes"
enabled = true

[mcp_servers.browser_tools.tools.open_page]
approval_mode = "approve"

[mcp_servers.browser_tools.tools.create_record]
approval_mode = "prompt"
```

`enabled_tools` 是允许列表；`disabled_tools` 是拒绝列表，并在允许列表之后应用。上例中的 `screenshot` 最终仍然被禁用。

审批模式的常见取值：

| 值 | 含义 |
|---|---|
| `auto` | 由 Codex 和当前策略决定 |
| `prompt` | 调用工具时询问用户 |
| `writes` | 非只读工具需要询问 |
| `approve` | 按配置允许调用 |

只读查询可以减少重复确认；创建、发送、更新、删除、部署和合并等操作应要求确认。实际行为还受当前权限模式、工具元数据和管理员策略约束。

高质量审批提示应展示 Server、工具、目标对象、关键参数、操作后果以及是否可撤销。不要批准一个看不到范围和目标的笼统 MCP 请求。

### 8.9 在 Codex 中使用 MCP

即使 Server 已连接，高风险任务也应在提示词中明确范围：

```text
使用文档 MCP 查询最新的内部 API 规范，只读。
不要调用任何创建、更新、发送或删除类工具。
回答中标明使用了哪些文档来源和仍不确定的内容。
```

需要写入时采用“先预览、再确认、最后执行”：

```text
先读取 Issue 模板和相关代码，再拟定 Issue 标题与正文供我确认。
在我确认前不要调用 create_issue。
```

MCP 返回的是外部数据，不自动等于可信结论。仍需检查时间、版本、环境、结果完整性和是否需要交叉验证。

多个 Server 应按业务域命名，例如 `github_readonly`、`internal_docs`、`staging_database`。Server 越多，启动时间、上下文占用、误选工具和权限面都会增加，不需要时应禁用。

### 8.10 Codex 使用 MCP 的安全边界

安装 STDIO Server 等于在本机运行它；连接 HTTP Server 等于把部分任务数据发送给该服务。使用前要检查发布者、代码和依赖、权限范围、数据保留政策、升级方式和撤销方法。

外部文档、网页、Issue 和工具结果可能包含提示注入。防护原则：

- 把外部内容视为数据，不把其中的文字自动当成高优先级指令；
- 不让读取文档的工具同时拥有高权限写入能力；
- 使用工具允许列表和最小权限 Token；
- 写操作要求确认，并在服务端再次校验权限；
- 不在工具结果、提示词和日志中暴露秘密；
- 删除、部署、合并和资金操作需要额外保护；
- 优先提供 `preview_*` 或 dry-run 工具。

推荐按用途拆分凭据：

```text
只读文档 Token ≠ 代码写入 Token ≠ 部署 Token ≠ 管理员 Token
```

### 8.11 Codex MCP 常见故障

| 现象 | 优先检查 |
|---|---|
| `/mcp` 中不显示 Server | 配置位置、表名、项目信任、`enabled`、管理员策略 |
| STDIO 进程立即退出 | 命令、参数、依赖、`cwd`、环境变量 |
| HTTP 401/403 | Token、OAuth、Scope、账号和服务端权限 |
| HTTP 404 | MCP URL 路径和反向代理配置 |
| 初始化超时 | Server 启动性能和 `startup_timeout_sec` |
| 单工具超时 | 查询范围、外部 API、`tool_timeout_sec` |
| 工具未出现 | Server 能力发现、允许列表和实现 |
| 工具存在但不调用 | 工具描述、参数定义、审批和任务匹配度 |
| 重启后配置消失 | 修改了错误 Host 的配置文件 |

STDIO Server 调试时，可先在同一终端手动运行启动命令。协议数据必须使用 stdout 时，调试日志通常应写到 stderr，避免破坏通信。HTTP 失败时应检查 URL、网络、代理、证书和认证，不要通过长期关闭 TLS 校验来规避问题。

工具超时不要只靠不断增大超时值，应先缩小查询、分页、减少返回字段，并检查外部 API 限流或 Server 死锁。

### 8.12 MCP 与 Codex Skill、Plugin 的关系

MCP 提供“能做什么”，Skill 规定“应该怎样做”，Plugin 负责“怎样安装和分发这组能力”。例如一个发布检查 Skill 可以通过 GitHub MCP 查询 CI，通过文档 MCP 核对发布流程，再由 Plugin 把 Skill 和 MCP 配置一起交付给团队。

个人调试可以直接配置 MCP；工作流稳定后写成 Skill；需要跨团队安装、统一升级或同时分发 Hooks、连接器和资源时，再打包成 Plugin。

## 九、Codex 插件、Hooks 与自动化

### 9.1 Plugins

Plugin 是可安装的分发单元，可以把相关能力一次交付给个人或团队。

```text
my-plugin/
├── .codex-plugin/
│   └── plugin.json       # 必需：插件清单
├── skills/               # 可选：Skills
├── .app.json             # 可选：应用或连接器映射
├── .mcp.json             # 可选：MCP 配置
├── hooks/                # 可选：生命周期 Hooks
└── assets/               # 可选：图标、截图等资源
```

可从支持界面的插件目录安装现有 Plugin。创建自定义 Plugin 时，可以使用内置 plugin creator；CLI 中通常通过 `$plugin-creator` 调用，支持 `@` 选择器的界面可从对应列表中选择。

安装前要审查清单、权限、连接器、MCP、Hooks 和依赖。功能没有出现时，依次检查插件是否启用、连接器授权、MCP 初始化、工作空间策略，以及是否需要重启或新建会话。

### 9.2 Hooks

Hooks 在 Codex 生命周期的特定节点运行脚本或 MCP 工具，适合机械执行：

- 提交提示词前检查 API Key；
- 工具调用前阻止危险命令；
- 工具执行后记录审计信息；
- 会话结束前运行验证；
- 压缩上下文前后保存必要状态。

常见位置：

```text
~/.codex/hooks.json
~/.codex/config.toml
<repo>/.codex/hooks.json
<repo>/.codex/config.toml
```

项目 Hooks 通常只在项目被信任时加载。多个来源的匹配 Hook 可能都会执行，而不是由高优先级简单覆盖。启用第三方 Hook 前必须阅读脚本。

### 9.3 计划任务与线程自动化

在支持的桌面应用中，可以为稳定流程设置计划任务，选择项目、提示词、运行频率和执行环境。Git 仓库中的后台任务适合在专用 Worktree 中运行。

适合自动化的任务包括：汇总提交、扫描回归、草拟发布说明、汇总 CI 失败、检查文档更新和生成团队摘要。部分客户端和账号还支持由已连接应用事件触发任务；事件来源、过滤条件和授权范围必须明确。

```text
先人工运行并稳定流程 → 把方法整理成 Skill → 再设置计划或事件触发
```

如果每次运行仍需大量临时解释，说明流程还不适合自动化。

### 9.4 CLI 与 CI 自动化

```powershell
codex exec `
  -c sandbox_mode='"read-only"' `
  "审查当前改动，输出潜在回归和缺少的测试"

if ($LASTEXITCODE -ne 0) {
    throw "Codex 检查失败"
}
```

CI 中应使用最小权限凭据，固定输入、输出、超时和失败策略；默认只读，需要写入时使用隔离分支；对日志脱敏。AI 审查不替代测试和人工审批，自动合并或部署需要额外保护条件。

### 9.5 自动化安全检查表

- [ ] 任务已人工成功运行过多次；
- [ ] 输入、输出和结束条件明确；
- [ ] 使用独立分支、Worktree 或隔离环境；
- [ ] 网络、MCP、文件和外部写入权限最小化；
- [ ] 凭据来自安全存储，不在提示词和日志中出现；
- [ ] 失败会停止并通知，而不是静默继续；
- [ ] 创建 PR、合并和部署是分开的授权步骤；
- [ ] 有人工审查、审计记录和恢复方案。

## 十、Codex Skills

### 10.1 Skill 是什么

Skill 是一套可复用的任务说明和配套资源，用来告诉 Codex 某类工作应该怎样完成，适合步骤稳定、需要统一格式或专门参考资料的工作流。

Skill 使用渐进式加载：Codex 启动时主要看到名称、描述和位置；当任务与描述匹配或用户显式调用时，才读取完整 `SKILL.md`。因此，`description` 决定能否被发现，正文决定被选中后如何执行。

### 10.2 目录结构

```text
my-skill/
├── SKILL.md              # 必需：元数据和完整说明
├── scripts/              # 可选：可执行脚本
├── references/           # 可选：按需读取的参考资料
├── assets/               # 可选：模板、示例文件、图像等
└── agents/
    └── openai.yaml       # 可选：界面、调用策略和工具依赖
```

只需说明步骤时，保持单个 `SKILL.md` 即可。只有确定性操作需要时才增加脚本；大段资料放入 `references/`，不要让主说明无限膨胀。

### 10.3 创建 Skill

```text
$skill-creator
```

也可以手动创建：

```markdown
---
name: release-check
description: 在准备发布版本时检查版本号、变更日志、测试和构建产物；不用于日常代码审查，也不自动发布。
---

# 发布检查

1. 阅读发布说明、版本配置和当前 diff。
2. 确认版本号、标签和变更日志一致。
3. 运行项目规定的测试与构建命令。
4. 检查生成产物和未提交文件。
5. 输出通过项、阻塞项和建议操作。
6. 未经明确授权，不创建标签、不推送、不发布。
```

`name` 应简短稳定；`description` 要在开头写明核心用途、触发词和边界。只写“帮助处理发布”过于模糊。

### 10.4 显式与隐式调用

1. 显式调用：使用 `/skills` 选择，或在提示词中写 `$skill-name`；
2. 隐式调用：任务与 `description` 匹配时，由 Codex 自动选择。

显式调用适合必须使用固定流程的任务；隐式调用适合边界清晰、误触发风险低的工作流。高风险 Skill 可通过可选元数据关闭隐式调用。

### 10.5 Skill 的加载位置

| 范围 | 位置 | 适合用途 |
|---|---|---|
| 当前目录 | `$CWD/.agents/skills` | 某个模块或子项目 |
| 仓库上级目录 | `$CWD/../.agents/skills` | 嵌套仓库共享区域 |
| 仓库根目录 | `$REPO_ROOT/.agents/skills` | 团队共享并纳入版本控制 |
| 当前用户 | `$HOME/.agents/skills` | 个人跨项目复用 |
| 管理员 | `/etc/codex/skills` | 机器或容器统一提供 |
| 系统 | Codex 内置 | 通用系统 Skills |

Codex 会从当前目录向仓库根目录扫描。两个 Skill 使用相同 `name` 时不会自动合并，因此应避免名称冲突。

### 10.6 怎样写好 `SKILL.md`

一份好 Skill 应包含适用与排除场景、输入要求、执行步骤、参考资料加载条件、允许与禁止操作、验证和失败处理，以及最终输出格式。

- 用命令式、可验证的步骤，不写空泛愿景；
- 不复制项目已有的 `AGENTS.md` 内容；
- 不写秘密、账号或环境专属绝对路径；
- 脚本保持小、确定、可审查，并校验参数；
- 参考资料按需加载，避免挤占上下文；
- 危险操作默认停在预览或报告阶段。

### 10.7 如何选择

| 需求 | 最合适的方式 |
|---|---|
| 只在这一次任务中有效 | 提示词 |
| 当前仓库长期遵守的规则 | `AGENTS.md` |
| 可跨项目复用的工作步骤 | Skill |
| 访问外部实时数据或执行外部操作 | MCP 或连接器 |
| 向团队分发多个 Skills 和工具 | Plugin |
| 到固定时间重复运行 | Automation |
| 在生命周期节点机械执行 | Hook |

这些方式可以组合：`AGENTS.md` 规定测试命令，Skill 规定发布检查步骤，MCP 读取发布状态，Automation 定期触发，Plugin 再统一分发。

### 10.8 测试和维护 Skill

创建后至少测试三类请求：应该触发的标准任务、不应该触发的相似任务、输入缺失或工具失败的异常任务。检查是否遗漏步骤、误用工具、范围过大或输出不稳定。

```toml
[[skills.config]]
path = "/path/to/skill/SKILL.md"
enabled = false
```

更新后如果未被发现，可以重启客户端或新建会话。本地编写和调试直接维护 Skill 目录即可；跨团队安装、统一升级，或与 MCP、Hooks 一起交付时，应打包成 Plugin。

### 10.9 官方参考

- [Codex 快速入门](https://learn.chatgpt.com/docs/quickstart)
- [Codex 最佳实践](https://learn.chatgpt.com/guides/best-practices)
- [权限模式](https://learn.chatgpt.com/docs/permission-modes)
- [配置参考](https://learn.chatgpt.com/docs/config-file/config-reference)
- [AGENTS.md](https://learn.chatgpt.com/docs/agent-configuration/agents-md)
- [Git Worktrees](https://learn.chatgpt.com/docs/environments/git-worktrees)
- [记忆系统](https://learn.chatgpt.com/docs/customization/memories)
- [MCP](https://learn.chatgpt.com/docs/extend/mcp)
- [Plugins](https://learn.chatgpt.com/docs/plugins)
- [Hooks](https://learn.chatgpt.com/docs/hooks)
- [Build Skills](https://learn.chatgpt.com/docs/build-skills)

> 推荐核对顺序：当前客户端的命令菜单与设置 → 本机 `codex --help` → OpenAI 官方文档。命令、配置项和功能可用范围都可能随版本更新。
