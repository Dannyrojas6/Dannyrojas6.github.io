---
title: Astro部署心得
published: 2026-04-05T02:06:59.792Z
description: ''
updated: ''
tags:
  - Astro
draft: false
pin: 0
toc: true
lang: ''
abbrlink: ''
---

其实部署很简单，之前一直不成功是因为顺序搞错了。

> 注意⚠️：此方法只在retypeset主题测试过，其他Astro主题可能不适用

## Getting Started

直接从`https://github.com/radishzzz/astro-theme-retypeset`模板创建仓库，名字必须用自己的用户名开头的github.io，如Dannyrojas6.github.io，创建完成后找到仓库上方的Settings，点击Pages，将Build and deployment下的source改为GitHub Actions。

## Clone Repository

克隆远程仓库到本地：

```bash
git clone https://github.com/Dannyrojas6/Dannyrojas6.github.io.git
```

切换到项目目录，使用pnpm管理(如果只想测试是否能部署到GitHub Pages可以跳过)：

```bash
# 全局安装pnpm（如果未安装）
npm install -g pnpm

# 安装依赖
pnpm install

# 启动开发服务器
pnpm dev
```

## Set Repo URL

编辑src\config.ts，将site url改为自己的地址：

```ts
export const themeConfig: ThemeConfig = {
  site: {
	******
    // site url
    url: "https://Dannyrojas6.github.io",
	******
  },
```

在.github下创建workflows/deploy.yml，retypeset主分支为master，修改main->master：

```yaml
name: Deploy to GitHub Pages

on:
  # 每次推送到 `main` 分支时触发这个“工作流程”
  # 如果你使用了别的分支名，请按需将 `main` 替换成你的分支名
  push:
    branches: [main]
  # 允许你在 GitHub 上的 Actions 标签中手动触发此“工作流程”
  workflow_dispatch:

# 允许 job 克隆 repo 并创建一个 page deployment
permissions:
  contents: read
  pages: write
  id-token: write

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout your repository using git
        uses: actions/checkout@v5
      - name: Install, build, and upload your site
        uses: withastro/action@v5
        # with:
        # path: . # 存储库中 Astro 项目的根位置。（可选）
        # node-version: 20 # 用于构建站点的特定 Node.js 版本，默认为 20。（可选）
        # package-manager: pnpm@latest # 应使用哪个 Node.js 包管理器来安装依赖项和构建站点。会根据存储库中的 lockfile 自动检测。（可选）
        # build-cmd: pnpm run build # 用于构建你的网站的命令。默认运行软件包的构建脚本或任务。（可选）
        # env:
        # PUBLIC_POKEAPI: 'https://pokeapi.co/api/v2' # 对变量值使用单引号。（可选）

  deploy:
    needs: build
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4

```

## Commit & Push

将修改提交并推送到`master`分支，在仓库中Actions点击Deploy to GitHub Pages（第一次推荐手动部署一遍），完成后访问自己的网站地址即可：

```url
https://dannyrojas6.github.io
```

