---
title: 用Vagrant + virtualbox配置开发环境
date: 2016-06-07 21:23:19
tags: Vagrant+virtualbox
categories: 开发环境
---
### 简述
Vagrant + virtualbox可以打造一个属于自己并且可以移动的开发环境，做为一个web开发者来说是必备开发工具啦！配置好Lnmp环境、自己常用软件或者库如redis等，这样就可以在本机编写代码，然后在虚拟机里运行啦。
Vagrant和Virtualbox都是免费的，安装起来也很方便，自行google咯~
### Vagrant 常用命令
``` bash
vagrant init        初始化vagrantfile
vagrant add box     添加box，自动帮你生成vagrantfile
vagrant halt        关闭虚拟机
vagrant destroy     销毁虚拟机
vagrant ssh         连接虚拟机
vagrant reload      重新加载vagarntfile文件
vagrant suspend     暂时挂起虚拟机
vagrant status      查看虚拟机运行状态
```
<!-- more -->
### 配置过程
####  1. 下载box
下载地址 Ubuntu precise 64 VirtualBox http://files.vagrantup.com/precise64.box
如果你要其他系统的镜像，可以来这里下载：http://www.vagrantbox.es/   

####  2. 虚拟机安装
``` bash
cd ~/vagrant/
mkdir lnmp_server
cd lnmp_server
vagrant box add ubuntu64 ~/virtualbox.box
vagrant init ubuntu64 //初始化
vagrant up //启动虚拟机lnmp_server
vagrant ssh //进入虚拟机lnmp_server
```
####  3. 个性化配置Vagrantfile
``` bash
config.vm.hostname = "server"  //配置host name
config.vm.network "private_network", ip: "192.168.10.12"  //配置固定ip
config.vm.synced_folder "../wwwroot", "/wwwroot/", create: true, owner: "www-data", group: "www-data" //配置共享目录

//配置虚拟机的cpu和内存
config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
     vb.memory = "1024"
     vb.cpus = 1
     vb.name = 'lnmpubuntu64' //修改虚拟机名称
end
```
####  4. 环境安装
``` bash
sudo apt-get update
sudo apt-get install nginx 
sudo apt-get install mysql-server mysql-client 
sudo apt-get install php5-fpm php5-cli  php5-gd php5-mysql
```
####  5. 导出自己的box
cd ~/VirtualBox\ VMs/lnmp_server_default_1465309534775_30
vagrant package  --output lnmpubuntu64.box --base lnmpubuntu64


