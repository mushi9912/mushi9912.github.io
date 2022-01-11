---
title: Linux总结-组管理与权限管理
date: 2020-11-12 21:02:58
tags:
- Linux
categories:
- Linux
permalink: linux-authority
---

# 五、Linux组管理与权限管理

## 5.1 组管理

Linux中每个文件都有其所有者、所在组、其它组的概念。

查看文件的所有者：**ls -ahl**，**ls -al**，**ll**也可以查看

修改文件的所有者：**chown** 用户名 文件名，会改变所有者，但是不会改变所在组

修改文件的所在组：**chgrp** 组名 文件名，

改变用户的所在组：**usermod -g** 组名 用户名

## 5.2 权限管理

```shell
dr-xr-xr-x. 20 root root  4096 11月 13 20:35 .
dr-xr-xr-x. 20 root root  4096 11月 13 20:35 ..
-rw-r--r--   1 root root     0 8月  18 2017 .autorelabel
lrwxrwxrwx   1 root root     7 12月 24 2019 bin -> usr/bin
dr-xr-xr-x.  5 root root  4096 11月  7 19:05 boot
drwxr-xr-x  19 root root  2960 11月 16 18:42 dev
drwxr-xr-x   2 root root  4096 11月 12 21:46 download
drwxr-xr-x. 87 root root 12288 11月 21 12:28 etc
drwxr-xr-x.  5 root root  4096 11月 21 12:26 home
lrwxrwxrwx   1 root root     7 12月 24 2019 lib -> usr/lib
lrwxrwxrwx   1 root root     9 12月 24 2019 lib64 -> usr/lib64
drwxr-xr-x   3 root root  4096 11月 13 20:35 livedata
drwx------.  2 root root 16384 8月  18 2017 lost+found
drwxr-xr-x.  2 root root  4096 4月  11 2018 media
drwxr-xr-x.  2 root root  4096 4月  11 2018 mnt
drwxr-xr-x.  2 root root  4096 4月  11 2018 opt
dr-xr-xr-x  85 root root     0 11月 16 18:42 proc
dr-xr-x---.  7 root root  4096 11月 21 14:13 root
drwxr-xr-x  23 root root   680 11月 16 18:43 run
lrwxrwxrwx   1 root root     8 12月 24 2019 sbin -> usr/sbin
drwxr-xr-x.  2 root root  4096 4月  11 2018 srv
dr-xr-xr-x  13 root root     0 11月 21 12:31 sys
drwxrwxrwt.  9 root root  4096 11月 21 03:40 tmp
drwxr-xr-x. 13 root root  4096 12月 24 2019 usr
drwxr-xr-x. 19 root root  4096 12月 24 2019 var
-rw-r--r--   1 root root  3060 11月  8 15:27 临时文档.txt
```

1. 第一个字符是文件类型：-表示普通文件，d表示目录，l表示软链接，c表示字符设备(键盘、鼠标)，b表示块文件(硬盘)
2. 第一组rwx表示文件所有者的权限，第二组rwx表示文件所在组的权限，第三组rwx表示文件其它组的权限
3. 权限后面的数字，当是文件时，表示硬链接的数；当是目录时，表示子目录的个数
4. 之后的是文件所有者和所在组以及文件的大小为多少字节，目录则是4096
5. 时间为文件最后的修改时间

<!-- more -->

**rwx权限详解：**

**rwx 作用到文件**

1. [r]代表可读(read): 可以读取,查看

2. [w]代表可写(write): 可以修改,但是不代表可以删除该文件,删除一个文件的前提条件是对该文件所在的目录有写权限，才能删除该文件.

3. [x]代表可执行(execute):可以被执行

**rwx 作用到目录**

1. [r]代表可读(read): 可以读取，ls 查看目录内容

2. [w]代表可写(write): 可以修改,目录内创建+删除+重命名目录

3. [x]代表可执行(execute):可以进入该目录



**修改权限-chmod：**

**第一种方式：+ 、-、= 变更权限**

u:所有者  g:所有组  o:其他人  a:所有人(u、g、o 的总和)

1. chmod u=rwx,g=rx,o=x 文件目录名

2. chmod o+w 文件目录名

3. chmod a-x 文件目录名

**第二种方式：通过数字变更权限**

r=4 w=2 x=1，rwx=4+2+1=7

chmod u=rwx,g=rx,o=x 文件目录名

相当于 chmod 751文件目录名



**修改文件的所有者和所在组-chown：**

chown 用户名 文件名：改变文件的所有者

chown 用户名:组名 文件名：改变用户的所有者和所有组

-R 如果是目录 则使其下所有子文件或目录递归生效(包括自身)



**修改文件所在组-chgrp：**

chgrp 组名 文件名 改变文件的所在组