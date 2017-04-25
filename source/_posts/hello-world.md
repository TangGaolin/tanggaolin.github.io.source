---
title: GitHub & hexo 搭建博客
date: 2016-06-06 09:39:18
tags: github hexo
categories: 随笔
---
花了些时间用的GitHub 和 hexo 搭建了属于自己Blog，这里简单记录下搭建的过程吧。 
简单介绍下hexo，hexo是一款基于Node.js的静态博客框架,可以理解为它是用node写的一个软件，用于根据你写的文章内容生成Blog的静态网页。生成好的网页是要分享给其他人看的，所以需要把生成好的网页托管到服务器上，可以自己买一个域名解析自己网页上。然而服务器，域名啥的太折腾，还是挂在github上吧，简单方便，关键是免费 ^_^
<!-- more -->
### 环境要求
这里只介绍mac 其她自行google解决吧 ^_^
如果没有安装 homebrew
``` bash
ruby -e "$(curl -fsSkL https://raw.github.com/Homebrew/homebrew/go/install)"
```
安装 git
``` bash
brew install git
```
安装 node.js npm
``` bash
brew install node //该命令执行后，自动装好node和npm。
```
安装 hexo

``` bash
npm install hexo-cli -g
hexo init blog  //blog为你博客项目的文件夹
cd blog
npm install
hexo server   //这一步，你可以在浏览器访问 http://localhost:4000/
```


### 开始GitHub
首先在GitHub上申请自己的账号 https://github.com/.
建一个仓库为 ：<font color=#FF3333>你的用户名.github.io</font>
注：为了之后更新文章方便，可以将自己电脑的 ssh秘钥添加到github上。

### 开始Hexo

[Hexo 官网](https://hexo.io/) [hexo 文档](https://hexo.io/docs/)
#### 创建新文章

``` bash
$ hexo new "My New Post" //or hexo n "My New Post"
```
More info: [Writing](https://hexo.io/docs/writing.html)

#### 启动本地可访问服务

``` bash
$ hexo server //or hexo s
```
More info: [Server](https://hexo.io/docs/server.html)

#### 生成你的静态文件

``` bash
$ hexo generate //or hexo g
```
More info: [Generating](https://hexo.io/docs/generating.html)

#### 发布你的文章到github上

``` bash
$ hexo deploy // or hexo d
```
More info: [Deployment](https://hexo.io/docs/deployment.html)
注意：需要配置 __config.yml的deploy值,如:
``` bash
deploy:
  type: git
  repo: git@github.com:TangGaolin/tanggaolin.github.io.git
  branch: master
```

#### hexo 主题 * Next
hexo 有很多丰富主题，也可以自己开发一套主题（有时间可以尝试下）
这里推荐一个nice的主题 Next [Next开始文档](http://theme-next.iissnan.com/getting-started.html)

#### hexo 项目目录说明
{% img photo /images/20160707_hexo_menu.png hexo 目录说明%}

