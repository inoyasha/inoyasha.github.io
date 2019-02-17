title: 【转载】CentOS7下安装chrome浏览器
copyright: false
author: 郭小黑
date: 2019-02-17 20:30:50
tags:
categories:
---

![](/images/kali-linux.png)

Centos下安装chrome浏览器。

<!--more-->

#### 从[google官网](http://www.google.cn/chrome/browser/desktop/index.html)下载google chrome安装包

 选择linux系统，并下载google-chrome-stable_current_x86_64.
 
 #### 使用root权限进行安装
 
 `rpm -ivh google-chrome-stable_current_x86_64.rpm`
 
 #### 安装错误
 
 ```
  错误：依赖检测失败：
    lsb >= 4.0 被 google-chrome-stable-54.0.2840.59-1.x86_64 需要
    libXss.so.1()(64bit) 被 google-chrome-stable-54.0.2840.59-1.x86_64 需要
```

#### 安装依赖包

```
  yum install pax*
  yum install redhat-lsb*
  yum -y install libXss*
 ```
 
 #### 依赖检测失败
 
 `yum install libappindicato`
 
 #### 再次安装rpm软件包
 `rpm -ivh google-chrome-stable_current_x86_64.rpm`
 
```    准备中...                          ################################# [100%]
正在升级/安装...
   1:google-chrome-stable-54.0.2840.59################################# [100%]
```

至此，google chrome安装成功！此时应用程序中已经有Google Chrome标志啦！

**ps:** chrome默认是不能给root用户运行的。右击打开属性在命令后面加上下面内容 

`--no-sandbox`

原文:[CentOS7下安装chrome浏览器](https://blog.csdn.net/gejian1208/article/details/80605391)
