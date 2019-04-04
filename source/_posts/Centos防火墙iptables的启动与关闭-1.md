---
title: Centos防火墙iptables的启动与关闭
author: 郭小黑
tags:
  - Centos
categories:
  - Linux
copyright: true
translate_title: start-and-close-of-iptables-in-centos-firewall
date: 2019-01-29 17:29:00
---


![](/images/kali-linux.png)

#### 前言

centos（发行版）防火墙默认firewall 没有安装iptables,这样linux的端口都会被禁止访问。

<!--more-->

#### 关闭centos默认防火墙

```linux
//停止firewall
[root@localhost ~]# systemctl stop firewalld.service    
//禁止firewall开机启动
[root@localhost ~]# systemctl disable firewalld.service
```

#### 安装iptables

```linux
[root@localhost ~]# yum install iptables-services        
#重启防火墙使配置生效
[root@localhost ~]# systemctl restart iptables.service 
#设置防火墙开机启动
[root@localhost ~]# systemctl enable iptables.service 
#禁止防火墙开机启动
[root@localhost ~]# systemctl disable iptables.service
```

#### iptables命令

```linux
[root@localhost ~]# service iptable status      ---查看防火墙状态
[root@localhost ~]#servcie iptables stop           --临时关闭防火墙

[root@localhost ~]#service iptables start          --临时启动防火墙

[root@localhost ~]#service iptables restart          --重启防火墙
[root@localhost ~]#chkconfig iptables off          --永久关闭防火墙
[root@localhost ~]#chkconfig iptables on          --永久开启防火墙
```
