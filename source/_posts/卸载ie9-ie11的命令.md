title: 卸载ie9~ie11的命令
tags:
  - windows
categories:
  - 操作系统
author: 郭小黑
date: 2019-01-24 16:45:00
---


#### 引言

卸载windows中默认安装的浏览器，需要删除windows的更新包，在控制面板的程序中找到浏览器的更新包然后将其卸载是一件比较麻烦的事。下面介绍一种使用命令行卸载ie9~11的命令。

<!-- more -->


#### 具体命令

**执行命令的前提是:必须登陆管理员账户或者拥有管理员权限。**

- ie9 卸载

```code
FORFILES /P %WINDIR%\servicing\Packages /M Microsoft-Windows-InternetExplorer-*9.*.mum /c "cmd /c echo Uninstalling package @fname && start /w pkgmgr /up:@fname /quiet /norestart
```

- ie 10 卸载

```code
FORFILES /P %WINDIR%\servicing\Packages /M Microsoft-Windows-InternetExplorer-*10.*.mum /c "cmd /c echo Uninstalling package @fname && start /w pkgmgr /up:@fname /quiet /norestart
```

- ie11 卸载

```code
FORFILES /P %WINDIR%\servicing\Packages /M Microsoft-Windows-InternetExplorer-*11.*.mum /c "cmd /c echo Uninstalling package @fname && start /w pkgmgr /up:@fname /quiet /norestart
```

**ps：** 以上命令执行完，必须重新电脑才能生效。