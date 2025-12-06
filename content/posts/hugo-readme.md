+++
title = 'Hugo 常用指令与 Markdown 配置指南'
date = '2025-12-06T17:43:45+08:00'
lastmod = '2025-12-06T17:43:45+08:00'
draft = false

# 分类和标签
tags = ['Hugo', 'Markdown', '博客', '教程']
categories = ['官方文档', '技术指南']

# 摘要和描述
summary = '详细介绍 Hugo 常用指令、Markdown Front Matter 配置、基本语法和实用技巧的完整指南'
description = '一份完整的 Hugo 和 Markdown 使用手册，包含常用命令、配置说明和最佳实践'

# 显示控制
toc = true                    # 显示目录
showToc = true               # 显示目录
TocOpen = true              # 默认展开目录
ShowReadingTime = true       # 显示阅读时间
ShowBreadCrumbs = true       # 显示面包屑
ShowPostNavLinks = true      # 显示文章导航

# 作者信息（如果主题支持）
author = 'Minjie'

# 权重（数字越小越靠前）
weight = 1

#提供评论
comments = true
+++


## 一、Hugo 常用指令

### 1.1 创建新站点
```bash
# 创建一个新的 Hugo 站点
hugo new site mysite

# 进入站点目录
cd mysite
```

### 1.2 创建新文章
```bash
# 在 content/posts 目录下创建新文章
hugo new posts/my-first-post.md

# 在 content 目录下创建文章
hugo new about.md

# 创建在特定子目录
hugo new blog/2024/new-post.md
```

### 1.3 本地预览
```bash
# 启动本地开发服务器（默认 http://localhost:1313）
hugo server

# 启动并显示草稿文章
hugo server -D

# 启动并显示草稿、未来日期和过期的文章
hugo server -DEF

# 指定端口
hugo server --port 8080

# 实时重载并打开浏览器
hugo server -D --navigateToChanged
```

### 1.4 构建网站
```bash
# 构建静态网站到 public 目录
hugo

# 构建包含草稿
hugo -D

# 清理并构建
hugo --cleanDestinationDir

# 指定输出目录
hugo --destination docs
```

### 1.5 主题管理
```bash
# 添加主题（使用 Git submodule）
git submodule add https://github.com/username/theme-name.git themes/theme-name

# 更新主题
git submodule update --remote --merge

# 删除主题
git submodule deinit -f themes/theme-name
rm -rf .git/modules/themes/theme-name
git rm -f themes/theme-name
```

### 1.6 其他常用指令
```bash
# 查看 Hugo 版本
hugo version

# 查看帮助
hugo help

# 查看配置信息
hugo config

# 列出所有内容
hugo list all

# 列出草稿
hugo list drafts
```

---

## 二、Markdown Front Matter 配置

### 2.1 基础配置
```markdown
---
title: "文章标题"
date: 2025-12-06
draft: false
---
```

### 2.2 完整配置示例
```markdown
---
# 基本信息
title: "我的第一篇文章"
date: 2025-12-06T15:04:05+08:00
lastmod: 2025-12-07T10:00:00+08:00
draft: false

# 作者信息
author: "Minjie"
authorLink: "https://github.com/Minjie2003"

# 分类和标签
categories: ["技术"]
tags: ["Hugo", "Markdown", "博客"]
series: ["Hugo教程"]

# 摘要和描述
summary: "这是一篇关于 Hugo 和 Markdown 的完整指南"
description: "详细介绍 Hugo 的使用方法和 Markdown 配置"

# 特色图片
image: "/images/featured.jpg"
cover:
    image: "/images/cover.jpg"
    alt: "封面图片描述"
    caption: "图片说明文字"

# SEO 相关
keywords: ["Hugo", "静态博客", "Markdown"]
slug: "hugo-markdown-guide"
url: "/posts/hugo-guide/"

# 显示控制
toc: true                    # 显示目录
comments: true               # 允许评论
showToc: true               # 显示目录
TocOpen: false              # 默认展开目录
hidemeta: false             # 隐藏元信息
disableShare: false         # 禁用分享按钮
hideSummary: false          # 隐藏摘要
ShowReadingTime: true       # 显示阅读时间
ShowBreadCrumbs: true       # 显示面包屑导航
ShowPostNavLinks: true      # 显示文章导航链接

# 权重（用于排序）
weight: 1

# 外部链接
externalLink: "https://example.com"

# 是否在首页显示
showhome: true
---
```

### 2.3 常用字段说明

| 字段 | 说明 | 示例 |
|------|------|------|
| `title` | 文章标题 | `"我的文章"` |
| `date` | 发布日期 | `2025-12-06` 或 `2025-12-06T15:04:05+08:00` |
| `draft` | 是否为草稿 | `true` / `false` |
| `tags` | 标签（数组） | `["Hugo", "博客"]` |
| `categories` | 分类（数组） | `["技术", "教程"]` |
| `summary` | 文章摘要 | `"这是摘要"` |
| `description` | SEO 描述 | `"页面描述"` |
| `slug` | URL 友好名称 | `"my-post"` |
| `url` | 自定义完整 URL | `"/posts/my-post/"` |
| `weight` | 排序权重 | `1` (数字越小越靠前) |
| `toc` | 是否显示目录 | `true` / `false` |

---

## 三、Markdown 基本语法

### 3.1 标题
```markdown
# 一级标题
## 二级标题
### 三级标题
#### 四级标题
##### 五级标题
###### 六级标题
```

### 3.2 文本样式
```markdown
**粗体文字**
*斜体文字*
***粗斜体***
~~删除线~~
`行内代码`
```

### 3.3 列表
```markdown
# 无序列表
- 项目 1
- 项目 2
  - 子项目 2.1
  - 子项目 2.2

# 有序列表
1. 第一项
2. 第二项
   1. 子项 2.1
   2. 子项 2.2
```

### 3.4 链接和图片
```markdown
# 链接
[链接文字](https://example.com)
[带标题的链接](https://example.com "鼠标悬停显示")

# 图片
![图片描述](/images/photo.jpg)
![图片](https://example.com/image.jpg "图片标题")
```

### 3.5 引用
```markdown
> 这是一段引用文字
> 可以多行
>> 嵌套引用
```

### 3.6 代码块
````markdown
```python
def hello():
    print("Hello, Hugo!")
```

```javascript
console.log("Hello, World!");
```
````

### 3.7 表格
```markdown
| 列1 | 列2 | 列3 |
|-----|-----|-----|
| 数据1 | 数据2 | 数据3 |
| 数据4 | 数据5 | 数据6 |

# 对齐方式
| 左对齐 | 居中 | 右对齐 |
|:-------|:----:|-------:|
| 内容   | 内容 | 内容   |
```

### 3.8 分隔线
```markdown
---
***
___
```

### 3.9 任务列表
```markdown
- [x] 已完成的任务
- [ ] 未完成的任务
- [ ] 另一个任务
```

---

## 四、Hugo Shortcodes（短代码）

### 4.1 内置 Shortcodes
```text
# 显示图片
{{</* figure src="/images/photo.jpg" title="图片标题" */>}}

# 高亮文本
{{</* highlight python */>}}
def hello():
    print("Hello!")
{{</* /highlight */>}}

# YouTube 视频
{{</* youtube VIDEO_ID */>}}

# X (Twitter) 推文（新版）
{{</* x user="USERNAME" id="TWEET_ID" */>}}

# GitHub Gist（已废弃，建议直接嵌入代码）
# 可以使用 highlight 或直接用代码块替代

# 参数传递（需要在 Front Matter 中定义参数）
{{</* param "description" */>}}
```

### 4.2 自定义 Shortcode 示例
在 `layouts/shortcodes/note.html` 创建：
```html
<div class="note">
    {{ .Inner }}
</div>
```

使用时：
```text
{{</* note */>}}
这是一个提示框
{{</* /note */>}}
```

---

## 五、实用技巧

### 5.1 文章模板
在 `archetypes/default.md` 创建默认模板：
```markdown
---
title: "{{ replace .Name "-" " " | title }}"
date: {{ .Date }}
draft: true
tags: []
categories: []
summary: ""
---

## 简介

内容开始...
```

### 5.2 多语言配置
在 Front Matter 中：
```markdown
---
title: "English Title"
title.zh: "中文标题"
---
```

### 5.3 自定义 URL 结构
在 `hugo.toml` 中：
```toml
[permalinks]
  posts = "/:year/:month/:slug/"
```

### 5.4 草稿快速发布
```bash
# 将草稿改为发布状态
sed -i 's/draft: true/draft: false/' content/posts/my-post.md
```

---

## 六、常见问题

### 6.1 文章不显示？
- 检查 `draft: true` 是否改为 `false`
- 检查 `date` 是否为未来日期
- 使用 `hugo server -D` 查看草稿

### 6.2 图片无法显示？
- 图片放在 `static/images/` 目录
- 引用路径为 `/images/photo.jpg`（注意开头的 `/`）

### 6.3 主题样式不生效？
- 检查 `hugo.toml` 中 `theme = "theme-name"` 是否正确
- 清空缓存：`hugo --cleanDestinationDir`

### 6.4 中文 URL 乱码？
在 Front Matter 中设置 `slug`：
```markdown
---
slug: "my-post-url"
---
```

---

## 七、推荐配置（hugo.yaml）

```yaml
baseURL: "https://minjieblog.github.io/"
languageCode: "zh-cn"
title: "我的博客"
theme: "your-theme"

params:
  description: "博客描述"
  author: "Minjie"
  dateFormat: "2006-01-02"

menu:
  main:
    - name: "首页"
      url: "/"
      weight: 1
    - name: "文章"
      url: "/posts/"
      weight: 2
    - name: "关于"
      url: "/about/"
      weight: 3
```

---

## 八、快速参考

### 常用命令速查
```bash
hugo new posts/article.md    # 新建文章
hugo server -D              # 本地预览（含草稿）
hugo                        # 构建网站
```

### Front Matter 最小配置
```markdown
---
title: "标题"
date: 2025-12-06
draft: false
---
```

### Front Matter 推荐配置
```markdown
---
title: "标题"
date: 2025-12-06
tags: ["标签1", "标签2"]
categories: ["分类"]
summary: "文章摘要"
draft: false
---
```

---
