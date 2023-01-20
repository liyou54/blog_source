---
title: Hexo博客搭建及GithubAction自动化部署踩坑
date: 2023-01-20 18:54:27
tags:
---

[Hexo](https://hexo.io/)是一个静态博客框架，能够允许用户将 Markdown 转化为html，生成静态网页，其支持gitbub page作为服务器进行部署。

# 环境部署
##  安装git 
##  安装nodejs及Hexo
``` python
https://nodejs.org/zh-tw/download/ # node js 下载地址

# npm 切换淘宝源
npm config set registry https://registry.npm.taobao.org

# 安装 hexo
npm install -g hexo-cli

```
## Hexo项目初始化
``` python
#创建项目文件夹，blog _source,这里blog_source 代表项目源文件，但是我们github需要创建两个仓库，一个仓库跟这个文件夹关联，并且自动化发布，另外一个项目用于储存的静态资源。
mkdir blog _source

# 初始化项目
hexo init

# 下载依赖
npm i

## hexo 清理、生成、预览、发布
hexo clean
hexo g
hexo s
hexo d

```

## hexo 配置文件

hexo 的配置存放在 /_config.yml 以及 /themes/[主题名称]/_config.yml \(/_config.[主题名称]/.yml\)中，
这里主要修改deploy值，让我们在运行 ==hexo d==时将生成的静态代码发布到该地址上面:
```
deploy:
  type: 'git'
  repo:  你的 githubpage 项目地址
  branch: main
```

# 主题安装
因为本人是一个~~肥宅~~ [二次元]{.red}

# Github Acition 自动化部署