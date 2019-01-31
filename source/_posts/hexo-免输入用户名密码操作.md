title: hexo 免输入用户名密码操作
author: 郭小黑
tags:
  - hexo
categories:
  - 静态博客
date: 2019-01-29 14:42:00
---
![](/images/hexo.png)
#### 引言

hexo部署到github时，会要求你输入用户名密码，每次提交都要输入，肯定让你抓狂吧，下面的2步配置就能把你从痛苦中解救出来。


<!--more-->

- 在系统变量中添加一个环境变量：

```cmd
变量名：HOME
变量值：%USERPROFILE%
```

- 接着在你的用户目录（C:\Users\username）下新建一个叫 _netrc的文件编辑这个文件：

```cmd
machine github.com
login username
password password
```

设置好这些后，当你再次部署时，就不用输入用户名和密码了。

转载: 
[Hexo免输入密码部署到github](https://www.jianshu.com/p/e8c8ec320f0f)
