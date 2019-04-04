---
title: 【装载】centos 7 安装 恢复windows引导项
copyright: false
tags:
  - Linux
categories:
  - 操作系统
translate_title: centos-7-installation-restores-windows-boot-item
date: 2019-02-17 17:50:20
---



在电脑Windows10系统上安装Centos7，安装后找不到Windows7引导菜单。
原因是CentOS 7已采用新式的grub2系统，所以需要进入/boot/grub2目录后使用vi编辑grub.cfg文件。

<!--more-->

#### 解决方法一：修改Centos 7的Grub2引导，添加Windows的启动项

```
1.sudo vi /boot/grub2/grub.cfg
2.找到 ### BEGIN /etc/grub.d/30_os-prober ###
   在后面添加
   menuentry "Windows 10 (loader) (on /dev/sda1)" {
     insmod ntfs
     set root=(hd0,1) #由于我的windows安装在硬盘的C盘，故（hd0,1)
     chainloader +1
   }
```

#### 系统自动检测是否有其他系统

`grub2-mkconfig -o /boot/grub2/grub.cfg ``

Linux只能认识Ext3/4，fat32格式，如果Windows是NTFS格式磁盘分区，Grub2将无法识别。

步骤如下:

- 安装epel-release.noarch:  

`yum install epel-release.noarch`

- 安装ntfs-3g用于识别ntfs分区  
`yum install ntfs-3g`

- 重新生成引导项  
`grub2-mkconfig -o /boot/grub2/grub.cfg`

- 重新启动系统。

原文：[安装centos后无法引导启动windows7的解决方法](http://www.cnblogs.com/duzishanglu/p/4687958.html安装centos后无法引导启动windows7的解决方法)

