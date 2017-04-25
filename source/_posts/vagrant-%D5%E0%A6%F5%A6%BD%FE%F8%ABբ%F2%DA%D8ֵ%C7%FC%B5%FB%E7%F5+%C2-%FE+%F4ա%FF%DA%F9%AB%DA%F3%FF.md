---
title: vagrant 共享目录静态文件 缓存问题
date: 2016-08-28 11:36:10
tags: vagrant nginx 缓存
categories: 开发环境
---
用vagrant部署的开发环境已经有一段时间了，也一直使用很好。最近因为需要，帮前端也搭建这样的环境，已经项目调试修改。但是出现了一个问题，前端调试过程中只要修改了js代码，那浏览器总会报出异常，js加载不全或者尾部出现乱码，通过各种调试(304，js文件过大，重启nginx,重启VM等等)，未果。。。。崩溃了...

最后咨询了经验丰富的同事才得知，这应该是vagrant的一个bug，可以通过这样配置解决....

<!--more-->

#### 对于nginx

在config上加上

``` bash
sendfile off;
```

#### 对于Apache
``` bash
EnableSendfile off
```


果然，it works for me!


