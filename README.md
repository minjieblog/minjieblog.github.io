# Minjie's Blog

记录代码、学习和生活的个人博客，也是持续整理中的知识库。内容围绕 Java 后端开发、算法与面试准备、AI Agent、区块链论文阅读和课程学习展开，仓库中也保留了音乐、读书与日常笔记。

**访问博客：[minjieblog.github.io](https://minjieblog.github.io/)** · [关于我](https://minjieblog.github.io/about/) · [文章归档](https://minjieblog.github.io/archives/) · [搜索](https://minjieblog.github.io/search/)

## 📚 内容导航

| 栏目 | 主要内容 | 源文件 |
| --- | --- | --- |
| 面试笔记 | C++ STL 与 Python 算法模板 | [interview-notes](content/interview-notes/) |
| Java 学习 | 集合与算法、黑马点评项目笔记 | [java-notes](content/java-notes/) |
| AI / Agent | Agent 开发路线、项目实践与面试问题整理 | [agent](content/agent/) |
| 区块链研究 | DID、可验证凭证、凭证撤销、隐私保护与论文阅读 | [blockchain](content/blockchain/) |
| 数据库 | MySQL 常用语法与知识速查 | [database](content/database/) |
| 课程学习 | 数学算法、机器学习习题与课程作业 | [dase-course](content/dase-course/) |
| 工具与文档 | Hugo 使用、命令速查与开发工具笔记 | [doc-intro](content/doc-intro/) |
| 游戏攻略 | 游戏规则、玩法与经验整理 | [game-guide](content/game-guide/) |

以上是栏目配置，单篇文章还受草稿、发布日期和过期时间影响。`content/about.md`、`content/archives.md`、`content/search.md` 是独立页面，通过菜单访问，不需要加入 `mainSections`。

以下内容由 [hugo.yaml](hugo.yaml) 的 `ignoreFiles` 排除，不作为博客导航入口：`content/obsidian/`、`content/music/`、`content/reading-notes/`、`content/java-notes/core-java_volume_1.md` 和 `content/java-notes/design-patterns.md`。这些路径也已加入 `.gitignore`；历史上已跟踪的文件仍会留在 Git 中，详见下文。

### 从这里开始

- [Python 面试算法手册](content/interview-notes/python-algorithm-cheatsheet.md)：数据结构、经典刷题模板与 AI 算法实现。
- [C++ STL 刷题手册](content/interview-notes/cpp-stl-cheatsheet.md)：常用容器与算法模板速查。
- [Java 集合与算法模板](content/java-notes/algorithm-with-java.md)：Java 刷题常用方法与实现。
- [Agent 学习路线](content/agent/agent-roadmap.md)：Agent 开发方向的学习整理。
- [MySQL 速查笔记](content/database/mysql-cheatsheet.md)：数据库学习与复习参考。
- [Hugo 使用笔记](content/doc-intro/hugo-readme.md)：博客搭建与日常维护记录。

## 🛠️ 博客实现

- **静态站点生成**：Hugo Extended。
- **主题**：[PaperMod](https://github.com/adityatelange/hugo-PaperMod)，通过 Git 子模块管理。
- **内容格式**：Markdown + YAML front matter。
- **阅读功能**：文章目录、代码高亮与复制、分类、标签、归档、RSS 和站内搜索。
- **数学公式**：MathJax，配置位于 [mathjax.html](layouts/partials/mathjax.html)。
- **评论**：Giscus，相关配置位于 `hugo.yaml` 和 [comments.html](layouts/partials/comments.html)。
- **部署**：GitHub Actions 构建并发布到 GitHub Pages。

## 🚀 本地运行

### 1. 准备环境

安装 Git 和 Hugo Extended。当前 [部署工作流](.github/workflows/hugo.yaml) 使用 **Hugo Extended 0.152.2**；本地可采用相同版本，减少构建差异。

```shell
git --version
hugo version
```

`hugo version` 的输出应包含 `extended`。仓库的日常 Markdown 写作与预览不需要安装 Python 或 PyTorch；文章中的算法代码依赖与博客构建依赖是分开的。

### 2. 获取仓库和主题

```shell
git clone --recurse-submodules https://github.com/minjieblog/minjieblog.github.io.git
cd minjieblog.github.io
```

如果已经克隆仓库但缺少主题，在项目根目录执行：

```shell
git submodule update --init --recursive
```

### 3. 启动预览

```shell
hugo server -D
```

打开终端输出的本地地址，默认是 **http://localhost:1313/**。修改文章或样式后，Hugo 会自动重新构建。

`-D` 表示包含草稿，不会取消 `ignoreFiles` 的排除规则，也不会自动包含未来日期的文章；预览未来文章可使用 `hugo server -D -F`。

### 4. 构建静态页面

```shell
hugo
```

构建结果写入 `public/`。需要检查草稿时运行 `hugo -D`；正式部署工作流不带 `-D`。

## ✍️ 新增与维护文章

在项目根目录执行 `hugo new`，按栏目选择路径：

```shell
hugo new interview-notes/my-algorithm-note.md
hugo new java-notes/my-java-note.md
hugo new blockchain/my-paper-note.md
```

文章模板来自 [archetypes/default.md](archetypes/default.md)。生成后填写标题、标签、分类和摘要，再编写正文，例如：

```yaml
---
title: "我的算法笔记"
date: 2026-09-07T10:00:00+08:00
draft: true
tags: ["Python", "算法", "面试"]
categories: ["面试笔记"]
summary: "整理常用数据结构、解题思路与代码模板。"
description: "Python 算法学习与面试复习笔记。"
comments: true
---
```

**当前模板默认 `draft: false`**。未完成的文章请改为 `true`，确认内容后再切回 `false`。`date` 应填写文章实际日期；默认正式构建不会发布未来日期的文章。

写作时使用分级标题组织目录，为代码围栏指定语言，例如 `python`、`java`、`cpp`。技术笔记可以按“使用场景 → 方法或代码 → 复杂度与易错点”组织；论文笔记可以按“问题 → 方法 → 实验 → 局限”组织。

新增栏目时按需调整 `hugo.yaml` 中的 `params.mainSections`；它用于主题主文章列表的栏目选择，不能替代 `ignoreFiles` 来控制内容是否生成。

### 展示与提交规则

**不在 YAML 中逐一列出的文件，也可能被发布。** Hugo 会读取 `content/` 下的内容；当前 `agent/agent-roadmap.md` 和部分没有 front matter 的 Agent 笔记仍出现在 `hugo list published` 中。

| 需求 | 配置位置 | 作用 |
| --- | --- | --- |
| 栏目出现在首页与归档 | `hugo.yaml` → `params.mainSections` | 当前 PaperMod 的首页与归档按此筛选；不会阻止文章页面生成或进入搜索 |
| 内容不参与网站构建 | `hugo.yaml` → `ignoreFiles` | 按正则表达式排除文件或目录；当前已有规则见上文 |
| 文章暂不发布 | 文章 front matter → `draft: true` | 正式构建排除草稿；本地 `-D` 预览仍会生成 |
| 本地文件不再新增到 Git | `.gitignore` | 控制 Git 跟踪，不控制 Hugo 构建，也不会自动移除已跟踪文件 |

如果希望 Agent 栏目显示在首页与归档，在 `mainSections` 中添加 `- agent`；如果希望整个栏目不发布，应在 `ignoreFiles` 中添加 `'content/agent/.*'`。当前保持现有 Agent 展示配置。

`.gitignore` 还排除了各层级的 `.obsidian/`、`.claude/settings.local.json`、根目录 `tmp/`、`dump.rdb`，以及构建产物和常见系统缓存。正文使用的图片、PDF 等资源不应仅因没有列在 YAML 中就被忽略。

对于已经跟踪的文件，添加忽略规则后仍会出现在 Git 变更中。需要停止跟踪时，可针对确认的路径执行 `git rm --cached <文件>`，目录使用 `git rm -r --cached <目录>`，再提交对应变更；本地文件会保留，历史提交中的内容也仍然存在。例如：

```shell
git rm -r --cached content/.obsidian
```

维护时可用 `hugo list published` 核对正式构建的页面，用 `git check-ignore -v --no-index <路径>` 检查忽略规则是否匹配。页面列表不包含所有附件，需要核对附件时应检查构建输出。

## 🗂️ 项目结构

```text
.
├── .github/workflows/hugo.yaml  # 构建与部署工作流
├── archetypes/                 # 新文章模板
├── assets/css/extended/         # 主题扩展样式
├── content/                    # 文章、页面与笔记资源
├── layouts/                    # 自定义页面与公共模板
│   ├── _default/               # 关于页等布局
│   └── partials/               # 数学公式、评论、页面头尾扩展
├── static/                     # 直接复制到网站的静态资源
├── themes/PaperMod/            # 主题子模块
├── public/                     # Hugo 生成的静态网站
└── hugo.yaml                   # 站点、导航、搜索和渲染配置
```

修改文章请编辑 `content/`，修改样式和布局请编辑 `assets/`、`layouts/`，然后重新构建。`public/` 是生成结果，直接修改其中的 HTML 会在后续构建时被覆盖。

## 🌐 自动部署

[GitHub Actions 工作流](.github/workflows/hugo.yaml) 在代码推送到 **`master` 分支**时运行，也支持通过 Actions 页面手动触发。

流程为：安装 Hugo Extended 与 Dart Sass → 递归检出主题子模块 → 执行 Hugo 构建 → 上传 `public/` → 发布到 GitHub Pages。构建使用 `Asia/Shanghai` 时区，并根据 Pages 配置传入发布地址。

复用此项目时，需要将仓库的 Pages 发布来源配置为 **GitHub Actions**，并修改 `hugo.yaml` 中的 `baseURL`、作者信息、社交链接和 Giscus 配置。

## 🔎 常见问题

| 现象 | 检查方法 |
| --- | --- |
| 找不到 PaperMod 主题 | 执行 `git submodule update --init --recursive` |
| 新文章没有显示 | 检查 `draft`、`date` 和 `ignoreFiles`；首页列表还需检查 `mainSections` |
| 页面出现合并冲突标记 | 检查文章、`layouts/partials/` 和 CSS 的冲突块，合并有效内容后重新构建；公共模板的残留会影响多个页面 |
| 搜索内容没有更新 | 确认首页输出包含 JSON，并重新构建站点 |
| 本地正常但部署失败 | 查看 Actions 构建日志，核对 Hugo 版本、主题子模块及文件路径大小写 |

## 💬 交流

欢迎通过博客评论交流学习心得，也欢迎指出笔记中的错误。

- GitHub：[Minjie2003](https://github.com/Minjie2003)
- 邮箱：[zhongnagisa01@gmail.com](mailto:zhongnagisa01@gmail.com)

> 流水不争先，争的是滔滔不绝。
