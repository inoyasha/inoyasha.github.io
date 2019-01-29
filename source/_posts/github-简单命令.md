title: github 简单命令
author: ginuyasha
date: 2019-01-29 14:20:57
tags:
categories:
---
#### 引言

github是一个基于git的代码托管平台，付费用户可以建私人仓库，我们一般的免费用户只能使用公共仓库，也就是代码要公开。 Github 由Chris Wanstrath, PJ Hyett 与Tom Preston-Werner三位开发者在2008年4月创办。迄今拥有59名全职员工，主要提供基于git的版本托管服务。

<!-- more -->

##### 本地初始化git

```git
git init
```
##### 添加本地文件

```git
git add 文件名
```

##### 提交到本地仓库

```git
git commit -m "first commit" 提交并添加注释
```

##### 将本地仓库和远程仓库关联

```git
git remote add origin https://github.com/inoyasha/test.git
```

##### 推送到远程仓库

```git
// 推到github分支上
git push -u origin master 
```
