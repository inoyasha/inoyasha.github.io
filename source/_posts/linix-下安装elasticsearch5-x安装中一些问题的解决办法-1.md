---
title: linix 下安装elasticsearch5.x安装中一些问题的解决办法
author: 郭小黑
tags:
  - elasticsearch
categories:
  - 数据库
translate_title: solutions-to-some-problems-in-installing-elastic-search-5.x-under-linix
date: 2019-01-29 17:11:00
---



![](/images/elasticsearch.jpg)

#### 引言

最近在学习elk，由于编译安装使用5.2.1版本的elasticsearch，所以遇到了很多问题，下面是一些问题及解决办法。

<!--more-->

#### 安装

[下载地址](https://www.elastic.co/)


#### 依赖环境

- jdk1.8

#### linux安装问题

##### root超级用户不能正常启动

由于elasticsearch2.0版本以后不能使用root来启动，所以需要创建一个普通用户来启动。

```vim
[root@bogon ~]# groupadd elasticsearch
[root@bogon ~]# useradd elasticsearch -g elasticsearch
[root@bogon ~]# chown -R elasticsearch.elasticsearch /usr/local/elk/elasticsearch-5.2.1  
[root@bogon ~]# su - elasticsearch
[elasticsearch@bogon ~]$ cd /usr/local/elk/elasticsearch-5.2.1/bin/
[elasticsearch@bogon bin]$ ./elasticsearch
```

由普通用户来启动，则可以正常启动服务。


##### ERROR: bootstrap checks failed

- 问题
```cmd
max file descriptors [4096] for elasticsearch process likely too low, increase to at least [65536]
max number of threads [1024] for user [lishang] likely too low, increase to at least [2048]
```


- 解决办法。

切换到root用户，编辑limits.conf 添加类似如下内容

```cmd
vi /etc/security/limits.conf 

添加如下内容:

* soft nofile 65536

* hard nofile 131072

* soft nproc 2048
* hard nproc 4096
```

##### max number of threads [1024] for user [lish] likely too low, increase to at least [2048]

解决：切换到root用户，进入limits.d目录下修改配置文件。

```cmd
vi /etc/security/limits.d/90-nproc.conf 

修改如下内容：

* soft nproc 1024

#修改为

* soft nproc 2048
```


##### max virtual memory areas vm.max_map_count [65530] likely too low, increase to at least [262144]

解决：切换到root用户修改配置sysctl.conf

```cmd
vi /etc/sysctl.conf 

添加下面配置：

vm.max_map_count=655360

并执行命令：

sysctl -p

然后，重新启动elasticsearch，即可启动成功。
```

转载:
[elasticsearch5.x安装中一些问题的解决办法](https://blog.csdn.net/weini1111/article/details/60468068)





