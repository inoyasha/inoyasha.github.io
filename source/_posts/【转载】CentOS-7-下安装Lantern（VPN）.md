---
title: 【转载】CentOS 7 下安装Lantern（VPN）
copyright: false
author: 郭小黑
tags:
  - Centos
categories:
  - Linux
translate_title: install-lantern-(vpn)-under-centos-7
date: 2019-02-17 19:04:00
---

在实际的开发过程中，当我们遇到难题时，往往会询问“度娘”来解决问题。但是“度娘”的答案有的时候并不是尽善尽美。这个时候我们通过google来寻找答案。使用google搜索需要翻墙，故使用了lantern这个软件。lantern支持windows、Android以及Ubuntu系统，我本机安装的是Centos7，下面介绍如果在Centos上安装lantern。

<!--more--> 

#### 原理
 使用alien将Ubuntu的deb包转成rpm安装。
#### 结果图
![](/images/lantern/lantern01.jpg)

#### 安装必备的软件alien

```
# yum install epel-release
# rpm --import http://li.nux.ro/download/nux/RPM-GPG-KEY-nux.ro
# rpm -Uvh http://li.nux.ro/download/nux/dextop/el7/x86_64/nux-dextop-release-0-5.el7.nux.noarch.rpm
```

#### 下载ubuntn的deb包。
[下载地址](https://github.com/getlantern/lantern/releases/tag/latest)

#### 转化成rpm包

`sudo alien -r lantern-installer-64-bit.deb`

此时会出现：`Get Error:“conflicts with file from package filesystem”` 

**ps:** 我在实际操作时，没有遇到，直接转换成功。

#### 安装rpmrebuild

```
sudo yum install rpmrebuild
sudo rpmrebuild -pe lantern-4.0.1-2.x86_64.rpm
```

**ps:** `lantern-4.0.1-2.x86_64.rpm` 这个是转换成功的rpm包。

#### 替换文件(把里面的内容替换成下面的，大约在中间部位)


```
(Converted from a deb package by alien version 8.95.)
%files
#%dir %attr(0755, root, root) "/"
#%dir %attr(0755, root, root) "/usr"
#%dir %attr(0755, root, root) "/usr/bin"
%attr(0777, root, root) "/usr/bin/lantern"
#%dir %attr(0755, root, root) "/usr/lib"
%dir %attr(0755, root, root) "/usr/lib/lantern"
%attr(0644, root, root) "/usr/lib/lantern/.packaged-lantern.yaml"
%attr(0644, root, root) "/usr/lib/lantern/lantern-binary"
%attr(0755, root, root) "/usr/lib/lantern/lantern.sh"
%attr(0644, root, root) "/usr/lib/lantern/lantern.yaml"
#%dir %attr(0755, root, root) "/usr/share"
#%dir %attr(0755, root, root) "/usr/share/applications"
%attr(0644, root, root) "/usr/share/applications/lantern.desktop"
#%dir %attr(0755, root, root) "/usr/share/doc"
%dir %attr(0755, root, root) "/usr/share/doc/lantern"
%doc %attr(0644, root, root) "/usr/share/doc/lantern/changelog.gz"
%doc %attr(0644, root, root) "/usr/share/doc/lantern/copyright"
#%dir %attr(0755, root, root) "/usr/share/icons"
#%dir %attr(0755, root, root) "/usr/share/icons/hicolor"
#%dir %attr(0755, root, root) "/usr/share/icons/hicolor/128x128"
#%dir %attr(0755, root, root) "/usr/share/icons/hicolor/128x128/apps"
%attr(0644, root, root) "/usr/share/icons/hicolor/128x128/apps/lantern.png"
%changelog
```

以上替换文件是执行了`sudo rpmrebuild -pe lantern-4.0.1-2.x86_64.rpm` 重新构建了rpm包时，替换的。

#### 安装

`sudo rpm -i /root/rpmbuild/RPMS/x86_64/lantern-4.0.1-2.x86_64.rpm`

`lantern-4.0.1-2.x86_64.rpm` 这个rpm包时重新构建生成的。

#### 会出现错误(实际安装的时候，暂时没有遇到过)

`sudo yum install libappindicator-gtk3`

#### 复制sh文件到根目录

`cp /usr/lib/lantern/lantern.sh  ~./lantern.sh  
`

#### 设置开机启动

```
vi /etc/rc.d/rc.local

把/usr/lib/lantern/lantern.sh  

添加进去就可以了，保存退出。
```

**ps:** 在实际操作的时候，不要着急，仔细完成每个步骤，相信就会成功。

原文：[CentOS 7 下安装Lantern（VPN）](https://segmentfault.com/a/1190000013278021?utm_source=channel-hottest/*&^%$)
