---
title: Jekyll + GitHub Pages + Chirpy 个人网站搭建记录
date: 2026-06-11 23:50:00 +0800
categories:
  - 博客
  - 网站搭建
tags:
  - GitHub Pages
  - Jekyll
  - Chirpy
  - GitHub Actions
  - Markdown
---

# Jekyll + GitHub Pages + Chirpy 个人网站搭建记录

## 前言

这篇文章用来记录我从零搭建个人网站的过程。

这次我选择的方案是：

- `GitHub Pages`：用于托管静态网站；
- `Jekyll`：用于把 Markdown 文件生成静态博客页面；
- `Chirpy`：作为 Jekyll 主题，用来提供博客布局、侧边栏、标签、分类、归档等功能；
- `GitHub Actions`：用于自动构建和部署网站。

这篇文章不是一篇完全标准化的教程，而是结合我自己搭建过程中真正遇到的问题来整理的记录，包括头像修改、权限疑惑、CDN 缓存、主题颜色、GitHub Actions 部署等问题。

## 为什么选择 GitHub Pages + Jekyll + Chirpy

### GitHub Pages

`GitHub Pages` 最大的优点是免费、简单，并且和 GitHub 仓库天然集成。对于个人博客来说，不需要自己购买服务器，也不需要额外部署后端，只要把网站源码放在 GitHub 仓库中，就可以通过 GitHub Pages 发布出去。

我的网站仓库是：

```text
https://github.com/Houstonzaihubei/houstonzaihubei.github.io
```

仓库名采用的是 GitHub Pages 的用户站点格式：

```text
用户名.github.io
```

因此对应的网站地址就是：

```text
https://houstonzaihubei.github.io
```

### Jekyll

`Jekyll` 是一个静态网站生成器。简单来说，它可以把 Markdown 文件、配置文件和主题模板一起转换成最终的 HTML 静态网页。

对于博客来说，这种方式比较适合，因为我平时写文章本身就习惯使用 Markdown。只要按照 Jekyll 的规则把文章放到指定目录中，网站构建时就会自动生成对应的博客页面。

### Chirpy

我使用的是 `Chirpy` 主题。相比一些非常简单的主题，Chirpy 更适合技术博客，原因主要有：

- 页面结构比较完整；
- 支持文章目录；
- 支持分类和标签；
- 支持归档页面；
- 默认样式比较适合写技术文章；
- 和 GitHub Pages、GitHub Actions 的组合比较常见。

对我来说，Chirpy 的好处是：我不需要从零写前端页面，但又能通过配置文件和 Markdown 文件控制网站内容。

## 整体搭建流程

我的搭建过程大致可以分为下面几个步骤：

1. 创建 GitHub Pages 仓库；
2. 使用 Chirpy 主题作为博客模板；
3. 修改 `_config.yml` 中的网站基础信息；
4. 修改头像、标题、描述、社交链接等内容；
5. 编写第一篇 Markdown 博客；
6. 配置 GitHub Actions 自动构建；
7. 推送到 GitHub，等待 Pages 部署完成；
8. 根据部署后的显示效果继续调整。

下面按照实际过程展开记录。

## 创建仓库

GitHub Pages 用户站点仓库的名称必须符合下面的形式：

```text
用户名.github.io
```

我的 GitHub 用户名是：

```text
Houstonzaihubei
```

所以仓库名设置为：

```text
Houstonzaihubei.github.io
```

这个仓库就是整个网站的源码仓库，后续所有博客文章、主题配置、GitHub Actions 配置都会放在这个仓库里。

## 配置 Chirpy 主题

Chirpy 主题的核心配置文件是：

```text
_config.yml
```

这个文件决定了网站的很多基础信息，例如：

- 网站标题；
- 网站副标题；
- 网站描述；
- 网站语言；
- 时区；
- 头像路径；
- 社交链接；
- 主题相关配置。

常见配置大致如下：

```yml
# _config.yml
title: Houston's Blog
tagline: 记录学习、项目与思考
description: 一个用于记录个人学习过程和项目经历的博客
url: "https://houstonzaihubei.github.io"
lang: zh-CN
timezone: Asia/Shanghai
```

这里需要注意的是，`url` 最好写成最终的网站地址。如果这里写错，后面可能会出现资源路径异常、页面链接错误等问题。

## 文章目录与 Markdown 格式

Jekyll 博客文章需要放在 `_posts` 目录下。

文章文件名必须遵守下面的格式：

```text
YYYY-MM-DD-文章标题.md
```

例如：

```text
2026-06-11-Jekyll-GitHub-Pages-Chirpy-个人网站搭建记录.md
```

每篇文章开头还需要写 front matter，也就是用 `---` 包起来的元信息：

```md
---
title: Jekyll + GitHub Pages + Chirpy 个人网站搭建记录
date: 2026-06-11 23:50:00 +0800
categories:
  - 博客
  - 网站搭建
tags:
  - GitHub Pages
  - Jekyll
  - Chirpy
  - GitHub Actions
  - Markdown
---
```

这些内容不会直接作为正文显示，而是会被 Jekyll 读取，用来生成文章标题、发布时间、分类和标签。

如果文件名格式不对，或者 front matter 写错，文章就可能无法正常显示。

## GitHub Actions 自动部署

Chirpy 主题通常会通过 GitHub Actions 来构建和部署网站。

我的工作流文件位于：

```text
.github/workflows/pages-deploy.yml
```

核心作用是：

1. 当代码推送到 `main` 或 `master` 分支时触发；
2. 检出仓库代码；
3. 配置构建环境；
4. 构建 Jekyll 网站；
5. 上传构建产物；
6. 部署到 GitHub Pages。

一个常见的触发配置如下：

```yml
on:
  push:
    branches:
      - main
      - master
  workflow_dispatch:
```

这里的 `workflow_dispatch` 表示可以在 GitHub Actions 页面手动运行工作流。

权限部分也很重要：

```yml
permissions:
  contents: read
  pages: write
  id-token: write
```

如果权限配置不正确，GitHub Actions 可能构建成功，但部署失败。

## 遇到的问题与解决过程

### 问题一：修改头像后页面显示异常

最开始我想修改博客头像，于是调整了头像相关的配置和资源文件。

一般来说，Chirpy 的头像可以在 `_config.yml` 中配置，例如：

```yml
avatar: /assets/img/avatar.png
```

然后把头像图片放到：

```text
assets/img/avatar.png
```

但是实际修改后，页面并没有立刻按照预期显示。这时我一开始怀疑是文件权限问题，也担心自己是不是误改了某些关键文件。

后来逐渐排查后发现，这类问题不一定是权限导致的，还可能和下面几个因素有关：

- 图片路径是否写对；
- 图片文件是否真的提交到了 GitHub；
- GitHub Actions 是否重新部署成功；
- 浏览器是否还在使用旧缓存；
- CDN 是否还没有刷新；
- 图片文件名大小写是否一致。

尤其是大小写问题很容易忽略。Windows 本地对大小写不敏感，但部署到线上后，路径大小写不一致就可能导致资源找不到。

最后我对这类问题的理解是：

> 修改头像不是只改一张图片，而是要保证“配置路径、文件位置、仓库提交、Actions 部署、浏览器缓存”整条链路都没有问题。

### 问题二：不清楚自己到底修改了哪些文件

搭建过程中还有一个让我比较不安的问题：我有时候不清楚自己为了修复某个问题到底修改了哪些文件。

这在刚开始使用 GitHub Pages 和 Jekyll 时很常见。因为网站不是单个 HTML 文件，而是由很多部分组成的：

```text
_config.yml              # 网站核心配置
_posts/                  # 博客文章
assets/                  # 图片、CSS、JS 等资源
.github/workflows/       # GitHub Actions 自动部署配置
Gemfile                  # Ruby/Jekyll 依赖
```

所以后面每次修改前，我都应该先看一眼 Git 状态：

```bash
git status
```

如果想查看某个文件具体改了什么，可以使用：

```bash
git diff
```

提交时也应该尽量使用清晰的 commit message，例如：

```bash
git add .
git commit -m "fix: update avatar path"
git push
```

这样以后页面出现问题时，至少可以通过 Git 提交记录回溯，而不是完全靠记忆猜测。

### 问题三：CDN 或缓存导致页面没有立刻变化

有一次我修改完配置后，网页看起来还是旧的效果，于是我开始怀疑自己是不是改错了。

后来才意识到，静态网站部署后，页面不一定会立刻在浏览器中显示最新结果。原因可能是：

- GitHub Actions 还没有部署完成；
- GitHub Pages 仍在更新；
- 浏览器缓存了旧页面；
- 静态资源被 CDN 缓存；
- 本地访问的是旧标签页，没有强制刷新。

遇到这种情况，可以按照下面顺序检查：

1. 打开 GitHub 仓库的 `Actions` 页面，确认最新 workflow 是否成功；
2. 打开 `Settings -> Pages`，确认 Pages 部署状态；
3. 使用浏览器强制刷新；
4. 尝试无痕窗口访问；
5. 检查线上网页源代码中的资源路径是否已经变化。

浏览器强制刷新快捷键：

```text
Ctrl + F5
```

如果无痕窗口能看到最新页面，而普通窗口不行，基本就可以判断是浏览器缓存问题。

### 问题四：GitHub Actions 构建和 Pages 部署不是一回事

刚开始我以为只要代码 push 到 GitHub，网页就会自动更新。但后来发现，中间至少有两个过程：

1. GitHub Actions 构建网站；
2. GitHub Pages 发布构建结果。

也就是说，push 成功不等于部署成功。

如果网页没有变化，应该先去看：

```text
GitHub 仓库 -> Actions
```

确认最新一次工作流是否成功。

如果 Actions 失败，就点进去看具体是哪一步报错。常见问题可能包括：

- Ruby 依赖安装失败；
- Jekyll 构建失败；
- 文件路径错误；
- front matter 格式错误；
- GitHub Pages 权限不足；
- workflow 配置不完整。

如果 Actions 成功，但网页没有变化，再去检查：

```text
Settings -> Pages
```

确认 GitHub Pages 的发布源是否正确。

## 我最终理解的部署链路

这次搭建之后，我对整个流程的理解变成了下面这样：

```text
本地修改 Markdown / 配置 / 图片
        ↓
git add / commit / push
        ↓
GitHub 仓库接收更新
        ↓
GitHub Actions 自动构建 Jekyll 网站
        ↓
生成静态 HTML / CSS / JS
        ↓
GitHub Pages 发布网站
        ↓
浏览器访问最终页面
```

所以，当网站出现问题时，也应该沿着这条链路逐步排查，而不是直接怀疑某一个文件。

这次搭建个人网站让我意识到，GitHub Pages + Jekyll + Chirpy 看起来只是一个静态博客方案，但它背后其实包含了很多真实的工程流程：

- 仓库结构；
- 配置文件；
- Markdown 规范；
- 静态网站生成；
- 自动化构建；
- GitHub Pages 部署；
- 浏览器缓存与资源路径；
- 问题排查和版本回溯。
