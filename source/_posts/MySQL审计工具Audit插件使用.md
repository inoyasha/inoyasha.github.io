---
title: MySQL审计工具Audit插件使用
date: 2019-01-23 15:27:18
tags: mysql
categories: 数据库
---


MySQL审计工具Audit插件使用

> 一、介绍MySQL AUDIT

<code>MySQL AUDIT Plugin</code>是一个 <code>MySQL</code>安全审计插件，由McAfee提供，设计强调安全性和审计能力。该插件可用作独立审计解决方案，或配置为数据传送给外部监测工具。支持版本为<code>MySQL (5.1, 5.5, 5.6, 5.7)，MariaDB (5.5, 10.0, 10.1) ，Platform (32 or 64 bit)</code>。从Mariadb 10.0版本开始audit插件直接内嵌了，名称为server_audit.so，可以直接加载使用。

<!--more-->

源码地址：https://github.com/mcafee/mysql-audit/

WIKI地址：https://github.com/mcafee/mysql-audit/wiki

二进制地址：https://bintray.com/mcafee/mysql-audit-plugin/release

macfee的mysql audit插件虽然日志信息比较大，对性能影响大，但是如果想要开启审计，那也应该忍受了。



> 二、安装使用MySQL AUDIT

下载mysql 对应版本的 audit插件



![image](https://note.youdao.com/yws/api/personal/file/BBD312E0F3A84E0990C698D12D119837?method=download&shareKey=9d685b7c499e382dc08f5c815318cf52)


MySQL的插件目录为：

![image](https://note.youdao.com/yws/api/personal/file/01833155B9034D8F803784A12A36A930?method=download&shareKey=2c6daf0a68fcaf4d1626899db08e39cd)


复制库文件到MySQL库目录下

![image](https://note.youdao.com/yws/api/personal/file/3A7A1480CE3141039115B7DBB9AA717E?method=download&shareKey=5c1315d09e58c68272a14b1de0cc4ad1)



加载Audit插件

![image](https://note.youdao.com/yws/api/personal/file/941D88EF075743528B45782F8C89D4B9?method=download&shareKey=168b806d7fe7a35d15e884abd23cc94a)


查看版本


![image](https://note.youdao.com/yws/api/personal/file/0930F778B9774D7D88C31250B2941744?method=download&shareKey=accef686f2a8fe820a2f0d26468fa461)




开启Audit功能



![image](https://note.youdao.com/yws/api/personal/file/91247DDD2FD542D1BC3D471BD1BB8F49?method=download&shareKey=fa5cbbec764af4b91c336631e01b5b91)


执行任何语句(默认会记录任何语句)，然后去mysql数据目录查看mysql-audit.json文件(默认为该文件)。

当然，我们还可以通过命令查看audit相关的命令

![image](https://note.youdao.com/yws/api/personal/file/FD61736BFC774630A6AA751A19A468C3?method=download&shareKey=a957738d87f2119e6f1efaf6e74ca3b1)


其中我们需要关注的参数有：

1. **audit_json_file**

是否开启audit功能。

2. **audit_json_log_file**

记录文件的路径和名称信息。

3. **audit_record_cmds**

audit记录的命令，默认为记录所有命令。可以设置为任意dml、dcl、ddl的组合。如：<code>audit_record_cmds=select,insert,delete,update</code>。还可以在线设置<code>set global audit_record_cmds=NULL。</code>(表示记录所有命令)

4. **audit_record_objs**

audit记录操作的对象，默认为记录所有对象，可以用<code>SET GLOBAL audit_record_objs=NULL</code>设置为默认。也可以指定为下面的格式：<code>audit_record_objs=,test.*,mysql.*,information_schema.*</code>。

5. **audit_whitelist_users**

用户白名单。



> 三、查看审计数据

![image](https://note.youdao.com/yws/api/personal/file/100658DE828447A6A5DDB4C908254DA2?method=download&shareKey=c5743ebf90144dc512eea9d51dceaa42)


审计数据一般在mysql的data目录下面。


转载来源：[MySQL审计工具Audit插件使用](http://www.ywnds.com/?p=7992)


