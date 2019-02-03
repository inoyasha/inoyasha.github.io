title: tomcat环境下服务器文件句柄耗尽（Too Many Open Files）的问题排查
author: 郭小黑
tags:
  - 问题排查
categories:
  - linux
copyright: true
date: 2019-01-29 18:18:00
---

![](/images/kali-linux.png)


#### 前言

之前遇到过一次句柄数耗尽的一个问题，具体问题如下。

<!--more-->

![](/images/tomcat linux服务器句柄数耗尽/1.png)


查询了一下资料：原因是服务器的文件句柄数不足，导致tomcat启动失败。

排除出现场的linux句柄只有50个，而tomcat占用的句柄数应该是超出了这个数值（从需改了linux的句柄数，服务起来了情况进行推断）。

参考:[tomcat环境下服务器文件句柄耗尽（Too Many Open Files）的问题排查](https://blog.csdn.net/shootyou/article/details/6579139)

**ps:** 如果想要查看linux的具体句柄数可以参考下面这篇文章。[Linux下查看某进程占用的文件句柄数和线程数](https://jingyan.baidu.com/album/75ab0bcbc67b3fd6864db2cc.html?picindex=1)