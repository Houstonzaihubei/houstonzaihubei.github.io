---
title: Jekyll + GitHub Pages + Chirpy 个人博客搭建全记录
date: 2026-06-11 23:50:00 +0800
categories:
  - 技术记录
  - 环境搭建
tags:
  - Jekyll
  - GitHub Pages
  - Chirpy
  - GitHub Actions
  - Giscus
  - 个人博客
---

# Jekyll + GitHub Pages + Chirpy 个人博客搭建全记录

这篇文章记录一次从零搭建个人博客的完整过程。

最终使用的方案是：

```text
GitHub Pages + Jekyll + Chirpy + GitHub Actions + Giscus
```

其中：

- `GitHub Pages` 用来托管静态网站；
- `Jekyll` 用来把 Markdown 文章转换成网页；
- `Chirpy` 用作博客主题；
- `GitHub Actions` 用来自动构建和部署；
- `Giscus` 用来添加文章评论功能。

## 整体流程

完整流程可以概括为下面几步：

```text
1. 准备 GitHub 账号
2. 使用 Chirpy Starter 创建博客仓库
3. 克隆仓库到本地
4. 安装本地运行环境
5. 修改 _config.yml 基础配置
6. 配置头像、站点标题、语言、时区和主题
7. 配置 GitHub Pages 的部署方式
8. 配置 GitHub Actions 自动构建
9. 编写第一篇 Markdown 文章
10. 添加 Giscus 评论系统
11. 推送到 GitHub，等待构建完成
12. 访问 https://用户名.github.io 查看效果
```

后面的内容会按这个流程展开。

## 一、创建 GitHub Pages 仓库

### 1. 仓库命名规则

如果想要使用 GitHub Pages 的个人主页形式，仓库名必须符合下面的格式：

```text
用户名.github.io
```

例如 GitHub 用户名是：

```text
example-user
```

那么仓库名就应该是：

```text
example-user.github.io
```

需要注意：

- 仓库名不要写错；
- 用户名部分要和 GitHub 用户名对应；
- 如果 GitHub 用户名包含大写字母，实际访问地址通常仍然建议按小写处理；
- 仓库建议设置为 `Public`，这样 GitHub Pages 使用起来更直接。

如果仓库名不是 `用户名.github.io`，它就会更像项目站点，访问路径通常会带上仓库名，例如：

```text
https://用户名.github.io/仓库名/
```

这时 `_config.yml` 中的 `baseurl` 也需要跟着调整。本文讨论的是个人主页仓库，所以 `baseurl` 保持为空字符串。

## 二、使用 Chirpy Starter 创建博客项目

Chirpy 官方推荐使用 Starter 模板创建新博客，因为这种方式已经把大部分关键文件准备好了，也更适合只想专注写博客的人。

操作步骤如下：

1. 打开 Chirpy Starter 仓库；
2. 点击右上角的 `Use this template`；
3. 选择 `Create a new repository`；
4. 仓库名填写：

```text
用户名.github.io
```

5. 仓库可见性选择 `Public`；
6. 创建仓库。

创建完成后，仓库中通常会包含这些重要文件和目录：

```text
.github/workflows/       GitHub Actions 工作流
_data/                   主题数据配置
_posts/                  博客文章目录
_tabs/                   侧边栏页面，例如 About、Categories、Tags
assets/                  图片、样式等静态资源
_config.yml              Jekyll / Chirpy 核心配置文件
Gemfile                  Ruby 依赖配置
Gemfile.lock             依赖版本锁定文件
```

初学时最容易误改的是 `_config.yml` 和 `.github/workflows`。这两个文件很重要，但它们的作用完全不同。

- `_config.yml` 是 Jekyll / Chirpy 的站点配置；
- `.github/workflows/*.yml` 是 GitHub Actions 的自动部署配置。

不要把 GitHub Actions 里面的 `permissions`、`jobs`、`steps` 等内容写进 `_config.yml`。这类配置只属于工作流文件，不属于 Jekyll 站点配置。

## 三、克隆仓库到本地

创建仓库后，可以把它克隆到本地：

```bash
git clone https://github.com/用户名/用户名.github.io.git
cd 用户名.github.io
```

## 四、本地环境准备

Chirpy 本质上是 Jekyll 站点，所以本地预览需要 Ruby、Bundler 和相关依赖。

### 1. 安装 Ruby

Windows 用户可以使用 RubyInstaller。安装时建议勾选：

```text
Add Ruby executables to your PATH
```

安装完成后，在终端中检查：

```bash
ruby -v
gem -v
```

只要能正常显示版本号，说明 Ruby 和 RubyGems 已经安装成功。

### 2. 安装 Bundler

Bundler 是 Ruby 项目的依赖管理工具。

```bash
gem install bundler
```

检查：

```bash
bundle -v
```

### 3. 安装项目依赖

进入博客仓库根目录，执行：

```bash
bundle install
```

这一步会根据 `Gemfile` 和 `Gemfile.lock` 安装 Jekyll、Chirpy 主题以及其他插件。

如果在 Windows 上遇到平台相关警告，一般先不要急着改配置。只要最后能成功执行本地预览，说明依赖基本没有问题。

### 4. 本地启动博客

执行：

```bash
bundle exec jekyll serve
```

正常情况下，终端会显示类似：

```text
Server address: http://127.0.0.1:4000/
```

然后在浏览器打开：

```text
http://127.0.0.1:4000/
```

如果页面能打开，说明本地构建成功。

本地预览的意义很大。以后每次修改 `_config.yml`、文章、头像或者评论配置，都建议先在本地确认没有明显问题，再推送到 GitHub。

## 五、核心配置文件 _config.yml

`_config.yml` 是整个博客最关键的配置文件。第一次修改时，不建议整段删除原内容，而是找到对应字段逐项修改。

下面给出一个适合个人博客的基础配置示例。

```yaml
# 导入 Chirpy 主题
theme: jekyll-theme-chirpy

# 网站语言
lang: zh-CN

# 时区
timezone: Asia/Shanghai

# 网站标题
title: 你的博客标题

# 网站副标题
tagline: 你的博客副标题

# 网站描述，用于 SEO 和 feed
description: >-
  这里填写博客简介。

# 站点 URL，不要以 / 结尾
url: "https://用户名.github.io"

# GitHub 用户名
github:
  username: 用户名

# 头像路径
avatar: "/assets/img/avatar.jpg"

# 文章目录
toc: true

# baseurl：个人主页仓库保持为空
baseurl: ""
```

### url 和 baseurl 怎么填

这是最容易出错的地方之一。

如果你的仓库是：

```text
用户名.github.io
```

访问地址是：

```text
https://用户名.github.io
```

那么配置应该是：

```yaml
url: "https://用户名.github.io"
baseurl: ""
```

如果你的仓库是普通项目仓库，例如：

```text
blog-demo
```

访问地址是：

```text
https://用户名.github.io/blog-demo/
```

那么通常应该配置为：

```yaml
url: "https://用户名.github.io"
baseurl: "/blog-demo"
```

如果 `baseurl` 配错，常见现象是：

- 首页能打开，但样式丢失；
- 图片不显示；
- 文章链接跳转 404；
- CSS 和 JS 加载路径不对。

## 六、配置头像

Chirpy 的头像一般通过 `_config.yml` 中的 `avatar` 字段配置。

建议把头像文件放到：

```text
assets/img/avatar.jpg
```

然后配置：

```yaml
avatar: "/assets/img/avatar.jpg"
```

注意事项：

1. 文件名要完全一致。`avatar.jpg`、`avatar.JPG`、`avatar.png` 是不同文件名；
2. 路径前面建议保留 `/`，表示从站点根路径开始；
3. 图片不要太大，建议压缩到几百 KB 以内；
4. 修改头像后，如果网页还是旧头像，先尝试强制刷新浏览器缓存；
5. 如果配置了 CDN，头像路径可能会被拼接到 CDN 地址后面，需要确认 CDN 是否能访问该资源。

如果出现头像不显示，可以按下面顺序排查：

```text
1. 仓库中是否真的存在 assets/img/avatar.jpg
2. _config.yml 中 avatar 路径是否写对
3. GitHub Actions 是否构建成功
4. 浏览器是否缓存了旧资源
5. 是否配置了 cdn，导致图片从错误地址加载
```

## 七、配置深色 / 浅色主题

Chirpy 支持主题颜色模式设置。对应配置项是：

```yaml
theme_mode:
```

它有三种常见写法。

### 1. 跟随系统

```yaml
theme_mode:
```

保持为空时，通常会跟随用户系统偏好，并且侧边栏底部会有切换按钮。

### 2. 强制浅色

```yaml
theme_mode: light
```

### 3. 强制深色

```yaml
theme_mode: dark
```

如果想让网站默认显示黑色主题，可以改成：

```yaml
theme_mode: dark
```

如果修改后没有立刻生效，先检查：

- `_config.yml` 是否保存；
- 是否重新提交并推送；
- GitHub Actions 是否部署成功；
- 浏览器是否缓存旧页面；
- 是否本地预览和线上页面看的是同一个版本。

## 八、配置社交信息

Chirpy 侧边栏底部可以显示 GitHub、邮箱、RSS 等信息。

`_config.yml` 中通常有：

```yaml
social:
  name: 你的昵称
  email: your-email@example.com
  links:
    - https://github.com/用户名
```

不想公开的内容不要写在这里。个人网站是公开页面，邮箱、社交链接、昵称都会有被访问者看到的可能。

如果不想显示某些联系方式，可以到：

```text
_data/contact.yml
```

根据需要启用或禁用对应项。

## 九、配置 GitHub Pages 发布方式

进入仓库页面：

```text
Settings -> Pages
```

在 `Build and deployment` 中，建议选择：

```text
Source: GitHub Actions
```

原因是 Chirpy Starter 通常已经准备好了对应的 GitHub Actions 工作流。选择 GitHub Actions 后，每次推送到主分支，GitHub 会自动执行构建和部署。

如果选择 `Deploy from a branch`，也不是不能用，但对 Chirpy 这种需要构建的主题来说，GitHub Actions 更清晰，也更方便检查错误日志。

## 十、GitHub Actions 自动部署

Chirpy Starter 中通常会有类似文件：

```text
.github/workflows/pages-deploy.yml
```

工作流大致会做这些事情：

```text
1. 检出仓库代码
2. 安装 Node.js
3. 构建 CSS 和 JS
4. 安装 Ruby 和 Bundler 依赖
5. 执行 Jekyll 构建
6. 检查生成结果
7. 上传 _site 作为 Pages artifact
8. 部署到 GitHub Pages
```

一个简化后的工作流结构如下：

```yaml
name: "Build and Deploy"

on:
  push:
    branches:
      - main
      - master
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: true

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Node
        uses: actions/setup-node@v4
        with:
          node-version: 20

      - name: Setup Ruby
        uses: ruby/setup-ruby@v1
        with:
          ruby-version: 3.3
          bundler-cache: true

      - name: Build site
        run: bundle exec jekyll b -d "_site"
        env:
          JEKYLL_ENV: "production"

      - name: Upload site artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: "_site"

  deploy:
    environment:
      name: github-pages
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

实际项目中不一定要完全照抄这段，因为 Chirpy Starter 已经提供了更完整的版本。这里的重点是理解：

- `permissions` 属于 GitHub Actions；
- `jobs` 属于 GitHub Actions；
- `steps` 属于 GitHub Actions；
- 它们不应该出现在 `_config.yml` 里。

部署完成后，可以在仓库的 `Actions` 页面看到执行记录。如果构建失败，就点进失败的 workflow，看是哪一步报错。

## 十一、写第一篇文章

Chirpy 的文章放在：

```text
_posts/
```

文件名必须符合格式：

```text
YYYY-MM-DD-文章标题.md
```

例如：

```text
2026-06-11-Jekyll-GitHub-Pages-Chirpy-个人博客搭建全记录.md
```

文章开头需要 front matter：

```yaml
---
title: Jekyll + GitHub Pages + Chirpy 个人博客搭建全记录
date: 2026-06-11 23:50:00 +0800
categories:
  - 技术记录
  - 环境搭建
tags:
  - Jekyll
  - GitHub Pages
  - Chirpy
  - GitHub Actions
  - Giscus
---
```

然后才是正文。

### 文章不显示的常见原因

如果文章推送后不显示，优先检查：

1. 文件是否放在 `_posts` 目录；
2. 文件名是否是 `YYYY-MM-DD-title.md`；
3. 日期是否是未来时间；
4. front matter 的 `---` 是否成对出现；
5. YAML 缩进是否正确；
6. GitHub Actions 是否构建成功。

Jekyll 对文章日期比较敏感。如果文章日期晚于构建时间，有可能不会显示。为了避免这个问题，文章时间可以使用当前时间或稍早一点的时间。

## 十二、添加 Giscus 评论系统

评论功能使用 `Giscus`。它的原理是把 GitHub Discussions 当作评论区。读者在文章下面发表评论时，本质上会在对应仓库的 Discussions 中创建或回复讨论。

使用 Giscus 的优点：

- 不需要自己搭建数据库；
- 和 GitHub 账号体系结合；
- 适合技术博客；
- 可以免费使用；
- Chirpy 已经内置了 Giscus 配置项。

### 1. 开启 GitHub Discussions

进入博客仓库：

```text
Settings -> General -> Features
```

找到：

```text
Discussions
```

勾选启用。

如果是第一次启用 Discussions，GitHub 可能会提示初始化讨论区，按提示完成即可。

### 2. 安装 Giscus App

打开 GitHub App 页面：

```text
https://github.com/apps/giscus
```

点击 `Install`。

安装时建议选择：

```text
Only select repositories
```

然后只选择自己的博客仓库。

不要直接给 Giscus 授权所有仓库，除非你确实需要。

### 3. 在 giscus.app 生成配置

打开：

```text
https://giscus.app/zh-CN
```

按页面提示填写。

#### 仓库

```text
用户名/用户名.github.io
```

要求：

- 仓库必须是 public；
- 仓库必须开启 Discussions；
- 仓库必须安装 Giscus App。

如果其中一个条件不满足，页面通常会提示无法使用该仓库。

#### 页面 ↔ Discussion 映射关系

建议选择：

```text
pathname
```

这样每篇文章会根据路径对应到一个 discussion。

例如文章地址是：

```text
/posts/my-first-post/
```

Giscus 会根据这个路径去匹配对应讨论。

#### Discussion 分类

可以选择：

```text
Announcements
```

也可以新建一个专门的分类，例如：

```text
Comments
```

如果使用 `Announcements`，要确认这个分类存在，并且 giscus.app 能正确生成 `category_id`。

#### 特性选项

可以按需选择：

```text
启用主帖子上的反应
将评论框放在评论之后
懒加载评论
```

评论框位置常用：

```text
bottom
```

语言选择：

```text
zh-CN
```

### 4. 获取 repo_id 和 category_id

配置完成后，giscus.app 会生成一段类似下面的脚本：

```html
<script src="https://giscus.app/client.js"
        data-repo="用户名/用户名.github.io"
        data-repo-id="R_kgDOxxxxxxxxxx"
        data-category="Announcements"
        data-category-id="DIC_kwDOxxxxxxxxxx"
        data-mapping="pathname"
        data-strict="0"
        data-reactions-enabled="1"
        data-emit-metadata="0"
        data-input-position="bottom"
        data-theme="preferred_color_scheme"
        data-lang="zh-CN"
        crossorigin="anonymous"
        async>
</script>
```

对于 Chirpy 来说，不需要把整段 `<script>` 手动粘到页面里。重点是复制其中几个值：

```text
data-repo
data-repo-id
data-category
data-category-id
data-mapping
data-strict
data-input-position
data-lang
data-reactions-enabled
```

### 5. 修改 _config.yml

在 `_config.yml` 中找到 `comments` 部分，改成：

```yaml
comments:
  provider: giscus

  disqus:
    shortname:

  utterances:
    repo:
    issue_term:

  giscus:
    repo: 用户名/用户名.github.io
    repo_id: R_kgDOxxxxxxxxxx
    category: Announcements
    category_id: DIC_kwDOxxxxxxxxxx
    mapping: pathname
    strict: 0
    input_position: bottom
    lang: zh-CN
    reactions_enabled: 1
```

需要注意：

- `provider` 必须写成 `giscus`；
- `repo` 必须是 `用户名/仓库名`，不是完整 URL；
- `repo_id` 不能自己编，必须从 giscus.app 生成；
- `category_id` 也不能自己编，必须从 giscus.app 生成；
- `mapping` 建议使用 `pathname`；
- `lang` 可以写 `zh-CN`；
- YAML 缩进必须正确。

错误写法示例：

```yaml
comments:
provider: giscus
```

这是错的，因为 `provider` 没有缩进到 `comments` 下面。

正确写法：

```yaml
comments:
  provider: giscus
```

### 6. 确认文章默认开启评论

Chirpy 通常还会在 `_config.yml` 的 `defaults` 中控制文章是否默认开启评论。

建议确认存在类似配置：

```yaml
defaults:
  - scope:
      path: ""
      type: posts
    values:
      layout: post
      comments: true
      toc: true
```

如果这里的 `comments` 是 `false`，即使全局配置了 Giscus，文章底部也可能不显示评论框。

也可以在单篇文章 front matter 中单独控制：

```yaml
---
title: 示例文章
comments: true
---
```

如果某篇文章不想开放评论，可以写：

```yaml
comments: false
```

### 7. 测试评论功能

提交修改：

```bash
git add _config.yml
git commit -m "Enable giscus comments"
git push
```

然后等待 GitHub Actions 构建完成。

打开任意一篇文章，滑到页面底部。如果配置正确，应该能看到 Giscus 评论框。

如果没有出现，按下面顺序排查：

```text
1. GitHub Actions 是否构建成功
2. _config.yml 中 comments.provider 是否为 giscus
3. repo 是否写成 用户名/用户名.github.io
4. repo_id 是否从 giscus.app 正确复制
5. category 和 category_id 是否匹配
6. GitHub Discussions 是否已经开启
7. Giscus App 是否安装到当前仓库
8. 当前文章 comments 是否为 true
9. 浏览器是否拦截第三方脚本
10. 是否在本地预览而不是线上页面测试
```

本地预览有时也能显示评论框，但最终仍建议在线上页面测试，因为 Giscus 和实际 URL、仓库权限、GitHub Discussions 状态都有关系。

## 十三、CDN、头像和资源加载问题

Chirpy 支持通过 `cdn` 字段配置媒体资源前缀。

```yaml
cdn:
```

如果这个字段为空，资源一般从当前站点加载。

如果写成：

```yaml
cdn: "https://cdn.example.com"
```

那么一些以 `/` 开头的媒体资源可能会被拼接成：

```text
https://cdn.example.com/assets/img/avatar.jpg
```

这意味着：

- 如果 CDN 上没有这张图片，头像会加载失败；
- 如果 CDN 缓存了旧图片，修改后可能不会立刻更新；
- 如果 CDN 跨域或访问异常，页面资源可能显示不正常。

所以如果只是普通个人博客，前期可以先让 `cdn` 保持为空：

```yaml
cdn:
```

等网站稳定后，再考虑是否配置 CDN。

如果头像或图片不显示，优先看浏览器开发者工具：

```text
F12 -> Network -> Img
```

观察图片请求地址到底是什么。如果请求地址已经变成 CDN 地址，就说明问题可能不在本地图片路径，而在 CDN 或缓存。

## 十四、常见问题与解决方案

### 问题一：页面样式丢失，只剩白底黑字

现象：网站能打开，但没有正常样式，像普通 HTML 页面。

可能原因：

```text
1. baseurl 配错
2. CSS 文件没有构建出来
3. GitHub Actions 构建失败
4. 浏览器加载到了旧页面
5. 静态资源路径被 CDN 改错
```

解决方法：

```text
1. 个人主页仓库检查 baseurl 是否为 ""
2. 检查 Actions 是否成功
3. 打开浏览器开发者工具，看 CSS 请求是否 404
4. 强制刷新浏览器缓存
5. 临时清空 cdn 配置再测试
```

### 问题二：想要深色主题，但页面默认是白色

可能原因：

```text
1. theme_mode 为空，网站跟随系统偏好
2. 浏览器或系统当前是浅色模式
3. 修改后没有重新部署成功
4. 缓存导致仍显示旧页面
```

解决方法：

```yaml
theme_mode: dark
```

然后重新提交并等待部署。

### 问题三：头像不显示

可能原因：

```text
1. 文件路径写错
2. 文件扩展名大小写不一致
3. 图片没有提交到 GitHub
4. CDN 地址错误
5. 浏览器缓存旧资源
```

解决方法：

```text
1. 确认 assets/img/avatar.jpg 存在
2. 确认 _config.yml 中 avatar 路径一致
3. git status 检查图片是否已提交
4. F12 查看图片实际请求地址
5. 清空缓存或换浏览器测试
```

### 问题四：文章写了但不显示

可能原因：

```text
1. 文件不在 _posts 目录
2. 文件名格式错误
3. 日期是未来时间
4. front matter 格式错误
5. YAML 缩进错误
6. GitHub Actions 构建失败
```

解决方法：

```text
1. 文件名使用 YYYY-MM-DD-title.md
2. date 使用当前或过去时间
3. front matter 开头和结尾都有 ---
4. 本地执行 bundle exec jekyll serve 测试
5. 查看 Actions 日志
```

### 问题五：GitHub Actions 构建失败

解决这类问题时，不要只看最后一行红色报错，要点开失败的步骤。

常见位置：

```text
Actions -> 失败的 workflow -> build -> 展开失败步骤
```

常见原因：

```text
1. YAML 文件缩进错误
2. _config.yml 格式错误
3. Gemfile.lock 平台不匹配
4. Node 或 Ruby 版本不兼容
5. 某个插件安装失败
6. htmlproofer 检查链接失败
```

如果错误出现在 `_config.yml`，通常会提示类似：

```text
did not find expected key while parsing a block mapping
```

这类错误基本就是 YAML 格式问题。重点检查缩进、冒号、引号和列表格式。

### 问题六：评论框不显示

可能原因：

```text
1. 没有开启 GitHub Discussions
2. 没有安装 Giscus App
3. repo_id 或 category_id 填错
4. provider 没有设置为 giscus
5. defaults 中 comments 不是 true
6. 当前文章 front matter 禁用了评论
7. 浏览器插件拦截 giscus.app 脚本
```

解决方法：

```text
1. 去仓库 Settings 检查 Discussions
2. 去 GitHub Apps 检查 Giscus 是否安装到当前仓库
3. 重新到 giscus.app 生成配置
4. 不要复制别人的 repo_id 和 category_id
5. 检查文章底部是否有脚本加载报错
```

## 十五、推荐的提交流程

每次修改网站时，建议按下面流程走：

```bash
# 1. 查看当前修改
git status

# 2. 本地预览
bundle exec jekyll serve

# 3. 确认无明显问题后添加文件
git add .

# 4. 提交
git commit -m "Update blog configuration"

# 5. 推送
git push
```

推送后，打开 GitHub 仓库：

```text
Actions -> Build and Deploy
```

确认构建是否成功。

成功后再打开：

```text
https://用户名.github.io
```

如果页面没有马上更新，可以等待一会儿再刷新。也可以使用无痕窗口或清除缓存测试。

## 十六、我的最终结构

最终，一个基础可用的 Chirpy 博客大概是这样的结构：

```text
用户名.github.io/
├── .github/
│   └── workflows/
│       └── pages-deploy.yml
├── _data/
│   └── contact.yml
├── _posts/
│   └── 2026-06-11-Jekyll-GitHub-Pages-Chirpy-个人博客搭建全记录.md
├── _tabs/
│   ├── about.md
│   ├── archives.md
│   ├── categories.md
│   └── tags.md
├── assets/
│   └── img/
│       └── avatar.jpg
├── _config.yml
├── Gemfile
└── Gemfile.lock
```

最常修改的文件主要是：

```text
_config.yml              修改网站基础配置
_posts/                  写文章
assets/img/              放头像和文章图片
_data/contact.yml        修改侧边栏联系方式
_tabs/about.md           修改关于页面
```

不熟悉之前，不建议随意修改：

```text
.github/workflows/
_layouts/
_includes/
_sass/
```

这些地方一旦改错，可能会导致构建失败或页面样式异常。

## 总结

使用 `Jekyll + GitHub Pages + Chirpy` 搭建个人博客，真正的难点并不只是创建仓库，而是理解几个配置之间的关系：

```text
_posts        决定文章内容
_config.yml   决定站点配置
assets        决定图片和静态资源
GitHub Pages  决定网站如何发布
GitHub Actions 决定网站如何自动构建
Giscus        决定评论系统如何接入 GitHub Discussions
```

搭建过程中最容易出问题的地方是：

- `url` 和 `baseurl`；
- 头像路径；
- 深浅色主题配置；
- GitHub Actions 工作流；
- `_config.yml` 的 YAML 缩进；
- Giscus 的 `repo_id` 和 `category_id`；
- 文章文件名和日期。

只要把这些地方理清楚，后面写博客就会简单很多。以后新增文章基本只需要做三件事：

```text
1. 在 _posts 中新建 Markdown 文件
2. 写好 front matter 和正文
3. git add / commit / push，等待 GitHub Actions 自动部署
```

到这里，一个带有主题、分类、标签、文章目录、深色模式和评论系统的个人博客就基本搭建完成了。
