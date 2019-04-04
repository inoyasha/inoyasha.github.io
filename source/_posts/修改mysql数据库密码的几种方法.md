---
title: 修改mysql数据库密码的几种方法
tags:
  - mysql
categories:
  - 数据库
author: 郭小黑
copyright: true
translate_title: several-methods-of-modifying-mysql-database-password
date: 2019-01-23 15:19:00
---


####  修改数据库密码的几种方法：




##### 方法1： 用SET PASSWORD命令   

首先登录MySQL。  

    格式：mysql> set password for 用户名@host = password('新密码');  

    例子：mysql> set password for root@'172.18.32.19' = password('xxxxxxxx'); 
    
    用户名：代表需要修改密码的账号
    host：代表和user绑定的ip地址.
	
<!--more-->
    
    
##### 方法2：用mysqladmin   不用登录数据库，在mysql的bin目录打开命令行执行即可。（建议）

    格式：mysqladmin -u用户名 -p旧密码 password 新密码  
    
    例子：mysqladmin -uroot -proot password xxxxxx  
    
    更改以后可能会出现警告： Using a password on the command line interface can be insecure.
    
    这个警告不影响修改，这个警告为5.6以上mysql的一个策略。

##### 方法3：update表 

 首先登录MySQL。  

    mysql> use mysql;  
    
    mysql> update user set password=password('xxx') where user='root' and host='localhost';  
    
    mysql> flush privileges;  
