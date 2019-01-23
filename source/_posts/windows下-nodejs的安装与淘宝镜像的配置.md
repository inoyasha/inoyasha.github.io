---
title: windows下 nodejs的安装与淘宝镜像的配置
date: 2019-01-23 19:51:08
tags: nodejs
categories: 前端
---

#### windows下 nodejs的安装与淘宝镜像的配置


- 下载

&nbsp;&nbsp;&nbsp;&nbsp;[官方地址](https://nodejs.org/en/)

- 安装nodejs.exe 文件

&nbsp;&nbsp;&nbsp;&nbsp;安装完成校验:

```code
 node -v
 npm -v
```

<!-- more -->

- 建立全局目录 

```code
E:\nodejs\node_global
```

- 设置nodejs的全局目录。
所有已全局安装的包都被安装在这。

```code
npm config set cache "E:\nodejs\node_cache"
npm config set prefix "E:\nodejs\node_global"
```

- 设置全局变量

&nbsp;&nbsp;&nbsp;&nbsp;1.将用户变量path中<code>C:\Users\Administrator\AppData\Roaming\npm;</code>改成<code>E:\nodejs\node_global</code>
&nbsp;&nbsp;&nbsp;&nbsp;2. 在系统变量中 新增变量<code>NODE_PATH</code>,变量值为<code> E:\nodejs\node_global\node_modules</code>
&nbsp;&nbsp;&nbsp;&nbsp;3.修改系统变量。<code>path:C:\Program Files\nodejs;E:\nodejs\node_global\node_modules;</code>

- 配置淘宝镜像 

&nbsp;&nbsp;&nbsp;&nbsp;1.[淘宝镜像官网](https://npm.taobao.org/)
&nbsp;&nbsp;&nbsp;&nbsp;2. 安装cnpm 

```code
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

来源：[nodejs window下安装与配置淘宝镜像](https://www.cnblogs.com/liangsongbai/p/5506073.html)

