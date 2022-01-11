---
title: Linux总结-文件系统
date: 2020-11-08 13:25:03
tags:
- Linux
categories:
- Linux
permalink: linux-filesystem
---

# 一、Linux文件系统

## 1.1 Linux目录结构

**/dev** 管理设备，例如CPU、disk等硬件设备，如同windows的设备管理器

**/media** 识别光盘、U盘，识别后会挂载到这个文件夹下

**/bin** 常用的命令：find、copy等

**/etc** 存放配置文件

**/home** 家文件，创建用户后这里就会产生对应的文件

**/lib** 系统开机所需要的动态连接共享库文件，类似于windoes下的DLL文件

**/lost+found** 一半是空的，用户非法关机后会存放一些文件

<!-- more -->

**/mnt** 挂载文件夹，让用户临时挂载一些别的文件系统，例如windows共享文件夹

**/opt** 软件安装目录，存放安装包

**/proc** 虚拟的目录，系统内存的映射，访问这个目录获取系统的信息，内核相关

**/root** root用户的文件

**/sbin** super bin，超级用户root才能使用的命令，，系统管理员使用的系统管理程序

**/selinux** 安全目录，类似windows下面的一些安全软件

**/boot** 启动Linux时使用的一些核心文件，包括连接文件和镜像文件

**/src** service的缩写，存放一些服务启动之后需要提取的数据，内核相关

**/sys** 内核相关

**/tmp** 临时文件夹

**/usr** 用户很多应用程序和文件都放在这个目录，类似windows下的program files目录

**/usr/local** 另一个安装软件的目录，一般用于编译源码方式安装的文件,存放安装过后的文件

**/var** 存放着不管扩充的东西，例如经常被修改的目录，包括各种日志文件

