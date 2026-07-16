---
title: "博客搭建指南"
date: 2026-07-16
lastmod: 2026-07-16
slug: "guide"
layout: "single"
menu:
    main: 
        weight: -40
        params:
            icon: book
---

这篇文档介绍了本博客的搭建过程、技术原理和维护方法。

## 整体架构

```
┌─────────────────────────────────────────────────────────────┐
│                        你的电脑                              │
│  ┌─────────────┐    ┌─────────────┐    ┌─────────────┐     │
│  │   Hugo      │    │   Git       │    │   VS Code   │     │
│  │  静态网站   │    │  版本控制   │    │   编辑器    │     │
│  │  生成器     │    │             │    │             │     │
│  └──────┬──────┘    └──────┬──────┘    └─────────────┘     │
│         │                  │                                │
│         ▼                  ▼                                │
│  ┌─────────────────────────────────────┐                   │
│  │         D:\BLOG 项目目录             │                   │
│  │  content/  →  文章 markdown 文件     │                   │
│  │  config/   →  网站配置              │                   │
│  │  themes/   →  主题模板              │                   │
│  │  public/   →  构建输出（自动生成）   │                   │
│  └──────────────────┬──────────────────┘                   │
└─────────────────────┼───────────────────────────────────────┘
                      │ git push
                      ▼
┌─────────────────────────────────────────────────────────────┐
│                      GitHub                                 │
│  ┌─────────────────────────────────────────────────────┐   │
│  │              GitHub Actions                          │   │
│  │  1. 拉取代码                                         │   │
│  │  2. 安装 Hugo                                        │   │
│  │  3. 构建网站 (hugo --gc --minify)                    │   │
│  │  4. 部署到 GitHub Pages                              │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │              GitHub Pages                            │   │
│  │  托管静态网站，提供访问地址                           │   │
│  └─────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
                      │
                      ▼
              https://METEOR-ECHO.github.io/
```

## 用到的工具

| 工具 | 作用 | 官网 |
|------|------|------|
| **Hugo** | 静态网站生成器，把 Markdown 转成 HTML | gohugo.io |
| **Hugo Theme Stack** | 网站主题，决定外观和功能 | github.com/CaiJimmy/hugo-theme-stack |
| **Git** | 版本控制，管理代码变更 | git-scm.com |
| **GitHub** | 托管代码，提供 Pages 服务 | github.com |
| **GitHub Actions** | 自动化构建和部署 | docs.github.com/actions |
| **VS Code** | 代码编辑器 | code.visualstudio.com |
| **VS Code 扩展** | 简化新建文章和发布流程 | 本地开发 |

## 搭建步骤详解

### 1. 安装 Hugo

Hugo 是一个用 Go 语言写的静态网站生成器，速度极快。

```powershell
# Windows 安装
winget install Hugo.Hugo.Extended

# 验证安装
hugo version
```

**为什么需要 Extended 版本？** 因为主题使用了 SCSS 样式，只有 Extended 版本支持 SCSS 编译。

### 2. 创建 Hugo 项目

```powershell
hugo new site .    # 在当前目录创建项目
```

这会生成以下目录结构：

```
D:\BLOG\
├── archetypes/     # 文章模板
├── content/        # 文章内容（你主要编辑这里）
├── data/           # 数据文件
├── layouts/        # 页面模板（覆盖主题用）
├── static/         # 静态资源（图片、CSS等）
├── themes/         # 主题文件
└── config/         # 配置文件
```

### 3. 安装主题

```powershell
git init
git submodule add https://github.com/CaiJimmy/hugo-theme-stack.git themes/hugo-theme-stack
```

使用 git submodule 可以方便地更新主题。

### 4. 配置网站

配置文件在 `config/_default/` 目录下：

| 文件 | 作用 |
|------|------|
| `hugo.toml` | 主配置（URL、标题、语言等） |
| `languages.toml` | 多语言配置 |
| `params.toml` | 主题参数（头像、侧边栏等） |
| `menu.toml` | 菜单和社交链接 |
| `markup.toml` | Markdown 渲染配置 |

### 5. 撰写文章

在 `content/post/` 目录下创建 `.md` 文件：

```markdown
---
title: "文章标题"
date: 2026-07-16
draft: false
tags: ["标签1", "标签2"]
categories: ["分类"]
---

这里是文章内容...
```

**Front Matter 说明：**
- `title`：文章标题
- `date`：发布日期
- `draft: false`：false 表示发布，true 表示草稿
- `tags`：标签
- `categories`：分类

### 6. 部署到 GitHub

创建 GitHub 仓库，推送代码后，GitHub Actions 自动构建部署。

## 自动部署原理

推送代码后，GitHub Actions 自动执行以下步骤：

```yaml
1. Checkout     → 拉取你的代码
2. Install Hugo → 安装 Hugo Extended
3. Build        → 运行 hugo --gc --minify 生成 HTML
4. Deploy       → 部署到 GitHub Pages
```

整个过程约 1-2 分钟，完成后网站自动更新。

## 日常维护指南

### 修改博客信息

| 想改什么 | 改哪个文件 |
|----------|------------|
| 博客标题 | `config/_default/hugo.toml` 的 `title` |
| 博客描述 | `config/_default/languages.toml` 的 `subtitle` |
| 头像 | `config/_default/params.toml` 的 `avatar` |
| GitHub 链接 | `config/_default/menu.toml` |
| 网站 URL | `config/_default/hugo.toml` 的 `baseURL` |

### 新建文章

**方法一：使用 VS Code 扩展**
- `Ctrl+Shift+P` → 输入"新建文章"

**方法二：手动创建**
在 `content/post/` 下新建 `.md` 文件，填写 Front Matter 和内容。

### 发布文章

**方法一：使用 VS Code 扩展**
- `Ctrl+Shift+P` → 输入"一键发布"

**方法二：手动发布**
```powershell
git add -A
git commit -m "发布新文章"
git push origin main
```

### 本地预览

```powershell
hugo server -D    # -D 表示包含草稿
```

浏览器打开 `http://localhost:1313` 即可实时预览。

### 文件结构速查

```
content/
├── _index.md              # 首页
├── _index.zh.md           # 首页（中文）
├── post/                  # 博客文章目录
│   ├── my-article.md      # 你的文章
│   └── my-article.zh.md   # 文章中文版
└── page/                  # 固定页面
    ├── about/             # 关于页面
    ├── archives/          # 归档页面
    ├── links/             # 友链页面
    └── search/            # 搜索页面
```

### 常见问题

**Q: 文章不显示？**
检查 `draft` 是否为 `false`。

**Q: 样式不对？**
清除 `public/` 和 `resources/` 目录后重新构建。

**Q: 部署失败？**
检查 GitHub Actions 页面的构建日志，通常是 Hugo 版本或配置问题。

---

*最后更新：2026-07-16*
