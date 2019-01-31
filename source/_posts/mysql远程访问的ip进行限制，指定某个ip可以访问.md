title: mysql远程访问的ip进行限制，指定某个ip可以访问
tags:
  - mysql
categories:
  - 数据库
author: 郭小黑
date: 2019-01-23 15:24:00
---

###### 关闭root账号所有远程ip可以访问的权限。

登录mysql数据库;执行如下语句：

<!--more-->

    use mysql;
    update user set host = 'localhost' where user = 'root' and host = '%';
    flush privileges;
    
    修改以后查询接口：
    
    select user,host,password from user;
    

![image](https://note.youdao.com/yws/api/personal/file/CA4F24BAE27F4DA6B3AA772D2F397D08?method=download&shareKey=b5141c32923da197f47859e23ac94c01)

 
###### 为指定的终端绑定ip远程访问数据库


添加远程ip访问权限

    use mysql;
    GRANT ALL PRIVILEGES ON *.* TO 'root'@'172.18.32.19' IDENTIFIED BY 'password' WITH GRANT OPTION;
    flush privileges;
    
    
    172.18.32.19:代表可以远程访问的ip
    root：代表上述地址可以访问的账号
    password:是密码
    
即，允许来自172.18.32.19的连接并使用root账户和password这个密码进行访问。

    ALL PRIVILEGES ON 后面的*.*表示所有数据库，即完全访问权限，可以指定为特定数据库。

    --  设置立刻生效。
    flush privileges; 
