title: mysql权限分离
tags:
  - mysql
categories:
  - 数据库
author: 郭小黑
date: 2019-01-23 15:26:00
---

###### 使用root登录mysql数据库

安全管理员：

    use mysql;
    grant all privileges on *.* to 账号@host identified by '密码';
    flush privileges;
    
    
将所有的操作权限给此用户

账号：代表创建的账号
host:为创建的账号允许访问的远程地址。

<!--more-->

###### mysql 创建用户并赋权的命令：

登录到mysql数据库

    grant select,insert,update,index,delete... on *.* to 账号@host identified by '密码';
    
select,insert:权限


\*.\*：第一个"\*"代表的是所有数据库，第二个"\*"代表的是所有的表。

账号：要赋权的账号名

密码：此账户的密码

host：允许远程访问的ip或者域名。


参考：[mysql创建用户、赋予指定权限命令](https://www.2cto.com/database/201709/686796.html)

      
