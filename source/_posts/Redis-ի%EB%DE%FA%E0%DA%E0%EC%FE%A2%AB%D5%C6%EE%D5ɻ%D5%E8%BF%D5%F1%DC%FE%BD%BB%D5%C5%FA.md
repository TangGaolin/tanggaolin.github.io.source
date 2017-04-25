---
title: Redis 安装配置和启动多端口
date: 2016-06-24 11:37:28
tags: redis
categories: redis 
---
redis 程序运行过程并不会消耗太多的 CPU 和 IO 资源（主要消耗memory），如是在单台机器上如果只启用一个redis实例会造成资源浪费 。同时为了增加可用性，一般也不会在单机上只运行一个redis实例，本篇就介绍下，如何在单上运行多个 redis 实例（运行在不同端口）。
<!-- more -->
### 安装redis
``` bash
// ubuntu 环境
sudo apt-get install redis-server
```
### 运行多实例redis
``` bash
/usr/bin/redis-server     // 为程序执行文件
/etc/redis/redis.conf     // 为配置文件 
/etc/init.d/redis-server   // 为自启动文件

$ cp redis.conf redis6380.conf
$ vi redis6380.conf

pidfile  /var/run/redis/redis_6380.pid
port 6380
logfile  /var/log/redis/redis_6380.log
```
### 启动多个redis实例：
``` bash
redis-server /etc/redis/redis6380.conf 
redis-cli -p 6380
```

### 启动多个redis实例方法2


``` bash
redis-server --port 6380 &
redis-server --port 6381 &
redis-server --port 6382 &

redis-cli -p 6380/6381/6382
```
