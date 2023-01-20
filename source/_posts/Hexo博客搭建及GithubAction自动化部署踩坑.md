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
[Ps3:]{.red} 评论需要注册[Learncode]( https://leancloud.cn/)并且修改配置文件
```yml
valine:
  appId: xxxx
  appKey: xxxx
  placeholder: ヽ(○´∀`)ﾉ♪ # Comment box placeholder
  avatar: mp # Gravatar style : mp, identicon, monsterid, wavatar, robohash, retro
  pageSize: 10 # Pagination size
  lang: en
  visitor: true # Article reading statistic
```
[Ps4:]{.red} 随机图片api失效 ,音乐播放api失效


# Github Acition 自动化部署
原本在本地修改完成代码或者[markdown]{.red} 文件之后需要[hexo clean hexo g hexo d]{.red} 这样存在两个弊端:
 1-麻烦 
 2-markdown存在本地 容易丢失
 因此，可以使用[GitHub Acition]{.kbd .red}进行自动化部署，脚本如下:
 ```yml
 name: Deploy

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    name: A job to deploy blog.
    steps:
    - name: Checkout
      uses: actions/checkout@v1
      with:
        submodules: true # Checkout private submodules(themes or something else).
    
    # Caching dependencies to speed up workflows. (GitHub will remove any cache entries that have not been accessed in over 7 days.)
    - name: Cache node modules
      uses: actions/cache@v1
      id: cache
      with:
        path: node_modules
        key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
        restore-keys: |
          ${{ runner.os }}-node-
    - name: Install Dependencies
      if: steps.cache.outputs.cache-hit != 'true'
      run: |
        npm ci &
        hexo clean &
        hexo g &
        hexo algolia

        

    # Deploy hexo blog website.
    - name: Deploy
      id: deploy
      uses: sma11black/hexo-action@v1.0.3
      with:
        deploy_key: ${{ secrets.DEPLOY_KEY }}
        user_name: xxx # (or delete this input setting to use bot account)
        user_email: xxx@xx.com  # (or delete this input setting to use bot account)
        commit_msg: ${{ github.event.head_commit.message }}  # (or delete this input setting to use hexo default settings)
        

        
    # Use the output from the `deploy` step(use for test action)
    - name: Get the output
      run: |
        echo "${{ steps.deploy.outputs.notify }}"
 ```
需要在设置里面添加secrets.DEPLOY_KEY 用于提交静态资源文件

 [参考连接](https://github.com/marketplace/actions/hexo-action) 