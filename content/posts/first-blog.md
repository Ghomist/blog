+++
title = "Hugo + Blowfish 搭建个人 Blog"
date = 2023-11-02T16:09:27+08:00
draft = true
+++

> 这应该是第一篇文章…不知道写什么好干脆记录一下搭 blog 遇到的问题好了

## 准备工作

基于 Hugo 当然首先要[安装 Hugo](https://gohugo.io/installation/)，跟随官网教程即可

> 当然 Linux 直接走包管理器安装即可啦

## 快速开始

> Windows 用户应当要用 PowerShell 或 Linux 终端（如 WSL、Git Bash），而非 Windows PowerShell

### 创建项目

首先创建一个 Hugo 项目，`site-name` 记得替换成你的网站的名称（其实就是待会创建的文件夹名称）

```bash
hugo new site site-name
```

然后进入项目文件夹

```bash
cd site-name
```

可以看一眼它的项目结构，项目结构大概像这样：

```txt
site-name
├── archetypes  # 模板文件
├── assets      # 资源文件，如 Logo
├── config      # 配置文件夹
├── content     # 文章存放处
├── i18n        # 多语言资源
├── layouts     # 存放布局的文件夹
├── static      # 存放静态文件
├── themes      # 主题文件夹
└── hugo.toml   # 项目配置文件
```

现在大多数文件夹都应该是空的，因为还没有内容呢，现在添加第一篇文章

```bash
hugo new content posts/first-blog.md
```

这条命令会向 `content` 文件夹内新建一个 `posts` 文件夹，然后再根据默认模板创建一个 `first-blog.md` 文件

打开 `first-blog.md` 文件，里面的内容应该类似于下面这样

```md
+++
title = 'Hugo + Blowfish 搭建个人 Blog'
date = 2023-11-02T16:09:27+08:00
draft = true
+++
```

两行 `+++` 之间的内容是这篇文章的基本属性，`title` 是文章的标题，`date` 是根据你执行创建命令的时间自动填入的，`draft` 表示这篇文章还是草稿状态

在下面的空白处你就可以开始写你的文章啦，按照 markdown 的语法开始写即可，要注意你的文章里不需要有一级标题（也就是以一个`#`开头的标题），因为 `Hugo` 会自动帮你生成标题，内容就是 `title` 指定的字符串

示例：（来自[官方示例](https://gohugo.io/getting-started/quick-start/#add-content)）

```md
+++
title: "My First Post"
date: 2022-11-20T09:03:20-08:00
draft: true
+++

## Introduction

This is **bold** text, and this is *emphasized* text.

Visit the [Hugo](https://gohugo.io) website!
```

反正呢先随便写点东西，然后把服务器跑起来测试一下

```bash
hugo server -D  # -D 选项能让服务器渲染草稿文章，不加的话看不到草稿
```

此时命令行应该提醒你可以在某个形如：`Web Server is available at http://localhost:1313/ (bind address 127.0.0.1)` 的链接处打开你的博客

打开后应该是啥也没有的状态，对，就是啥也没有，因为还需要配置一点东西，所以我们先在终端按 `ctrl + c` 关掉服务器，继续下面的配置

### 主题模板

接下来需要安装一个主题模板，没有主题模板的话，Hugo 没办法给你显示页面，除非你自己写页面了~~这一点我本人也没太弄清咳咳~~

在[这里](https://themes.gohugo.io/)找一个你喜欢的模板，然后点进去，里面一般会有 Demo 可以预览，也可以直接点 Download 按钮跳转到它的 Github 页面

> 如果懒得选择，也可以直接用我博客选择的模板：[Blowfish](https://blowfish.page/)，它的 Github 页[在这](https://github.com/nunocoracao/blowfish)，这篇文章后续会有一部分专门记录如何配置 Blowfish，如果使用了别的模板，最好是前往他们的官方网址/官方文档去参考和配置

Hugo 的主题一般支持3种安装方式：

- git submodule（子模块）
- hugo module（hugo 内置的模块功能）
- 手动下载 + 安装

由于官方的 quick start 使用的是 git submodule，那我们也只介绍用 git 的方式（其它方式可以在 hugo 文档或者各主题模板的文档内找到）

首先确保你的命令行还在 site-name 这个文件夹内，然后初始化一个 git 仓库

```bash
git init
```

随后将主题模板的 git 仓库添加为 submodule，这里要注意的是，每一个主题安装方式可能会有不同，最好前去他们的文档页面看一眼他们专属的 installation，然后跟着他们阐述的步骤进行操作

如果~~懒得找~~没有找到，那么这里我假设你找到的主题叫 `theme-abc`，它的 Github 主页链接是 `url`，那么命令就是：

```bash
git submodule add url.git themes/theme-abc
```

这行命令的意思是，将 `url` 指向的这个仓库，作为你仓库的子模块，并且存放位置位于 `themes/theme-abc` 这个文件夹处

例如对于 blowfish，命令行就是这样：（多了个 `-b main`，这是选择main分支的意思）

```bash
git submodule add -b main https://github.com/nunocoracao/blowfish.git themes/blowfish
```

安装好了主题之后，找到根目录的 `hugo.toml`，打开它并加入一行：

```toml
theme = "theme-abc"
```

此时再重启一下你的服务器，你会发现，已经有一个主页了，并且不出意外的话能看到你写的文章，这样的话你就成功了一大半了

### 网站配置

你应该注意到了根目录下的 `hugo.toml`，这个文件控制着你网址的基本配置，包括语言、标题、主题之类的信息

> 要提一句这个 toml 只不过也是一种信息记录格式，它可以等效替换成 yaml 或 json，Hugo 也都是支持的

下面是一个简单的配置示例：

```toml
baseURL = "https://example.org/"

languageCode = "zh-cn"
defaultContentLanguage = "zh-cn"

title = "个人随笔"
theme = "blowfish"
```

更详细的配置项可以去到[官网的文档](https://gohugo.io/getting-started/configuration/)查看

### 部署/发布

写好的文章当然需要展示出来对叭~~不然你也不至于搭 blog 直接本地写日记多好呀~~，那就需要进行部署和发布，一般有这几种方式：

- 在服务器上直接运行 hugo server
- 使用 hugo 构建静态页面，然后部署发布
  - 搭配个人服务器 + 服务器后端（如 apache、nginx 等）
  - 搭配 [Github Pages](https://docs.github.com/zh/pages)

#### 静态构建

Hugo 的静态构建非常简单，直接在项目根目录运行

```bash
hugo
```

这样就可以构建了，构建结果位于根目录的 `public` 文件夹内，如果部署至自己服务器上，只需要把 `public` 文件夹上传服务器然后设为网页根目录就好

#### Github Pages 部署

由于个人服务器不是人人都愿意弄的（自己搭很折腾，租也要钱，还要域名备案什么的），所以只介绍一下如何用 Github Pages 搭建，[官方教程在这](https://gohugo.io/hosting-and-deployment/hosting-on-github/)，这篇教程简单明了，也可以直接跟官方教程操作

> Github Pages 应该算得上喜闻乐见的？毕竟不要钱直接白嫖，只要有个 Github 账号就行了，所以…首先确保你有 Github 账号，~~我可不教怎么注册啊~~

如果使用 Github Pages 的话，可以使用 workflow 自动构建，不需要每次都手动 `hugo` 一下

首先把你的本地仓库发布到 Github 上（自行搜索`将本地仓库上传至github`），然后前往仓库的 Settings 页面，Pages 栏，其中你应该可以看到有个 Source 选项，默认选择的是 `Deploy from a branch`，我们把它改成 `Github Actions` 就好

然后在项目内创建文件 `.github/workflows/hugo.yaml`，最后的文件名可以改，但是目录一定不能错啦（ `.github` 前面有个点，`workflows` 是复数形式）

文件内容如下（来自 Hugo 官方教程），其中的分支名默认是 `master`，记得根据自己的分支名修改一下，另外 Hugo 的版本 `HUGO_VERSION` 也在这个文件内阐明了，记得与你本地的 Hugo 版本号对上

```yaml
# Sample workflow for building and deploying a Hugo site to GitHub Pages
name: Deploy Hugo site to Pages

on:
  # Runs on pushes targeting the default branch
  push:
    branches:
      - master # 这里根据自己分支名称修改！

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# Sets permissions of the GITHUB_TOKEN to allow deployment to GitHub Pages
permissions:
  contents: read
  pages: write
  id-token: write

# Allow only one concurrent deployment, skipping runs queued between the run in-progress and latest queued.
# However, do NOT cancel in-progress runs as we want to allow these production deployments to complete.
concurrency:
  group: "pages"
  cancel-in-progress: false

# Default to bash
defaults:
  run:
    shell: bash

jobs:
  # Build job
  build:
    runs-on: ubuntu-latest
    env:
      HUGO_VERSION: 0.120.2 # 这里对应你的 Hugo 版本！
    steps:
      - name: Install Hugo CLI
        run: |
          wget -O ${{ runner.temp }}/hugo.deb https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb \
          && sudo dpkg -i ${{ runner.temp }}/hugo.deb          
      - name: Install Dart Sass
        run: sudo snap install dart-sass
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: recursive
          fetch-depth: 0
      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v3
      - name: Install Node.js dependencies
        run: "[[ -f package-lock.json || -f npm-shrinkwrap.json ]] && npm ci || true"
      - name: Build with Hugo
        env:
          # For maximum backward compatibility with Hugo modules
          HUGO_ENVIRONMENT: production
          HUGO_ENV: production
        run: |
          hugo \
            --gc \
            --minify \
            --baseURL "${{ steps.pages.outputs.base_url }}/"          
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v2
        with:
          path: ./public

  # Deployment job
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v2
```

然后 commit 和 push 一下就可以了

此时回到 Github 仓库界面的 Actions 页面，你应该能看到你刚刚的 commit message，如果是黄色说明正在构建/部署，如果变成绿色就是构建部署成功了，点进去能看到详细信息，其中就能看到 Github 最后为你部署的地址，一般是

```url
https://<用户名>.github.io/<仓库名>/
```

默认的构建也是不包括草稿文章的，可以在刚刚 workflows 那个 yaml 中手动修改加上 `-D` 选项

每次构建大约需要1min左右，这是 github 需要为你申请资源、创建环境和部署什么的，Hugo 本身的速度还是非常快的，构建时长大约在毫秒级
