---
title: Centos下jdk bin格式安装
author: 郭小黑
tags:
  - jdk
categories: []
translate_title: installation-of-jdk-bin-format-under-centos
date: 2019-01-29 19:04:00
---
![](/images/java.jpg)

#### 前言

Cenots下，使用jdk-bin文件安装jdk。

<!--more-->
    
#### 下载bin文件并赋予权限

```cmd
chmod 755 jdk-6u33-linux-i586-rpm.bin
```



#### 执行bin文件

```cmd
./ jdk-6u33-linux-i586-rpm.bin
```

安装完成以后文件会默认安装在bin所在文件夹的 <code>java /jdk1.6.0_33</code>下.

#### 配置环境变量在/etc/profile 中加入：



```cmd
  JAVA_HOME=/usr/java/jdk1.6.0_33/
  CLASSPATH=.:$JAVA_HOME/lib.tools.jar
  PATH=$JAVA_HOME/bin:$PATH
  export JAVA_HOME CLASSPATH PATH
```

#### 运行命令使安装生效

```cmd
source /etc/profile 使配置文件生效
```

#### 验证安装是否成功

```cmd
javac
java -version
```