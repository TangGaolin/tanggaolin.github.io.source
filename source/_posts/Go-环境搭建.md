---
title: Go 环境搭建
date: 2016-06-22 09:26:06
tags: Go 环境搭建
categories: Go
---
貌似现在Go越来流行了，很多公司也慢慢用Go来做项目，最近也接触了Go的项目，便让我产生了对GO的兴趣，嘿嘿！
记录下Go的环境配置，配置结束之后，有点像在大学时，配java环境的感觉，哈哈！
Go环境的安装有多种方式，我这就简单记录下，基础简单的方式（window系统自行google）！


### 下载Go安装包
``` bash
//下载go安装包
wget https://storage.googleapis.com/golang/go1.6.2.linux-amd64.tar.gz 
//解压安装 $GO_INSTALL_DIR 一般默认为 /usr/local/
tar zxvf go1.6.2.linux-amd64.tar.gz  -C $GO_INSTALL_DIR
```
<!-- more -->
### 环境变量配置
解压之后，需要配置go的环境变量才可以命令行中使用go这个命令
``` bash
$ export GOROOT=/usr/local/go
$ export PATH=$PATH:$GOROOT/bin
$ go
go version go1.6.2 darwin/amd64
```


### 环境变量 GOPATH
go 命令依赖一个重要的环境变量：$GOPATH,
Go从1.1版本开始必须设置这个变量，而且不能和Go的安装目录一样，这个目录用来存放Go源码，Go的可运行文件，以及相应的编译之后的包文件。
``` bash
$ export GOPATH=/wwwroot/mygo
```
以上 $GOPATH 目录约定有三个子目录：
src 存放源代码（比如：.go .c .h .s等）
pkg 编译后生成的文件（比如：.a）
bin 编译后生成的可执行文件（为了方便，可以把此目录加入到 $PATH 变量中，如果有多个gopath，那么使用${GOPATH//://bin:}/bin添加所有的bin目录）

配置好环境之后就可以开始尝试写个hello world啦

这里贴上Go 编程的学习资料
https://github.com/astaxie/build-web-application-with-golang/tree/master/zh
非常感谢这位作者！

