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

#安装 hexo git 插件
npm install hexo-deployer-git --save

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

hexo 的配置存放在 [/_config.yml]{.kbd .red} 以及 [/themes/主题名称/_config.yml]{.kbd .red} [\(/_config.主题名称/.yml\)]{.kbd .red}中，
这里主要修改deploy值，让我们在运行 ==hexo d==时将生成的静态代码发布到该地址上面:
``` yml
deploy:
  type: 'git'
  repo:  你的 githubpage 项目地址
  branch: main
```

# 主题安装
因为本人是一个~~肥宅~~ [二次元]{.kbd .red},因此选择了 [shoka](https://shoka.lostyu.me/computer-science/note/theme-shoka-doc/) 这个主题
安装主题:
``` python
# cd your-blog
git clone https://github.com/amehime/hexo-theme-shoka.git ./themes/shoka
```
这里有两个选择 一种是直接clone,另外一种是使用 submodel 的方式,因为我需要修改源码，所以选择clone 下来然后删除.git文件夹。
安装依赖,主题配置:可以参考该文档：[shoka doc](https://shoka.lostyu.me/categories/computer-science/note/theme-shoka-doc/)
[Ps:]{.red} 这里，我们自定义自己的主题配置文件时最好在根目录创建[_config.主题名称/.yml]{.kbd .red}
[Ps1:]{.red} 搜索需要注册[Algolia](https://www.algolia.com/users/sign_in) 并且在运行[hexo g]{.kbd .red}之前运行[hexo algolia]{.kbd .red}。然后修改：
``` yml
algolia:
  appId: "xxxx"
  apiKey: "xxx"
  adminApiKey: "xxx"
  chunkSize: 5000
  indexName: "xxxx"
  fields:
    - title #必须配置
    - path #必须配置
    - categories #推荐配置
    - content:strip:truncate,0,4000
    - gallery
    - photos
    - tags
```


# Github Acition 自动化部署