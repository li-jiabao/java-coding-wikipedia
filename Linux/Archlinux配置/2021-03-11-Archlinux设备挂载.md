---
title: Typora-PicGo图床
author: lijiabao
date: 2021-03-11 12:00
description: 这篇文章主要介绍如何使用PicGo配置图床并应用到typora中
categories: Markdown
tags: Markdown
---



### USB设备挂载



`modprober usb-storage`先探测存在的usb设备

`fdisk -l`或者`lsblk`查看当前设备信息以及所处位置

![](https://cdn.jsdelivr.net/gh/li-jiabao/NoteImg@main/img/%E6%9F%A5%E7%9C%8B%E5%AD%98%E5%82%A8%E8%AE%BE%E5%A4%87%E5%90%8D.png)

然后将其挂载到你需要挂载的位置：

`mkdir /mnt/usb`创建挂载所需的文件夹

`mount /dev/sdb1 /mnt/usb`将设备挂载到mnt下面，之后便可以像访问文件夹一样去访问usb里面的文件内容了