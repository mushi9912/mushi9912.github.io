---
title: Linux总结-实用命令
date: 2020-11-09 19:42:49
tags:
- Linux
categories:
- Linux
permalink: linux-command
---

# 四、Linux实用命令

## 4.1 Linux操作级别

0：关机

1：单用户(在找回密码的时候特别有用)

2：多用户无网络服务

3：多用户有网络服务

4：保留

5：图形界面

6：重启

常用的运行级别是3和5，要修改默认的运行级别可以修改/etc/inittab的id:5:initdafault:这一行的数字

CentOS7修改了位置，不再使用/etc/inittab

命令：init [012345]

## 4.2 帮助指令

1. man [命令或配置文件]
2. help [命令]
3. baidu

<!-- more -->

## 4.3 文件目录类

**pwd：**

​	显示当前工作目录的绝对路径

**ls：**

​	显示文件或目录

​	ls -a /目录：当前所有文件

​	ls -l /目录：以列表的形式显示信息

**cd：**

​	切换到指定目录

​	cd ~：回到家目录

​	cd ..：回到上一级目录

​	cd：回到家目录

**mkdir：**

​	mkdir /目录：创建目录

​	mkdir -p /目录：创建多级目录

**rmdir：**

​	rmdir /目录：删除目录(非空)

​	rm -rf /目录：删除非空的目录

**touch：**

​	touch a.txt b.txt：创建空文件(可一次性创建多个文件)

**cp：**

​	cp /目录：拷贝单个文件

​	cp -r /目录：拷贝目录

​	\cp -r /目录：拷贝并覆盖目录

**rm：**

​	rm /文件：删除文件

​	rm -r /目录：递归删除所有文件

​	rm -f /目录：删除而不提示

**mv：**

​	mv old.txt new.txt：重命名

​	mv old.txt /new：移动文件或目录

**cat：**

​	查看文件内容

​	cat 文件：查看文件

​	cat -n 文件：显示行号

​	cat -n 文件 | more：分页

**more：**

​	以全屏方式按页显示文件的内容

​	快捷键：

		1. 空格：向下翻一页
  		2. enter：向下翻一行
    		3. q：立刻离开more，不再显示该文件内容
      		4. Crtl+F：向下滚动一屏
        		5. Ctrl+B：返回上一屏
          		6. =：输出当前的行号
            		7. :f：输出文件名和当前行号

**less：**

​	与more类似，并不是一次将文件读取，读取大文件很有用

​	快捷键：

  		1. 空格：向下翻一页
    		2. q：立刻离开more，不再显示该文件内容
      		3. 方向键下：向下翻一页
        		4. 方向键上：向上翻一页
          		5. /字符：向下搜寻字符串
            		6. ?字符：向上搜寻字符串

**&gt;和&gt;&gt;：**

​	输出重定向和追加

​	ls -l > 文件：写入文件中(覆盖写)

​	ls -al >> 文件：追加写入文件

​	cat 文件1 >> 文件2：文件1的内容追加到文件2

​	echo "" >> 文件：自定义内容写入到文件

**echo：**

​	echo输出内容至控制台

​	echo $PATH：输出环境变量

**head：**

​	显示文件的开头部分

​	head 文件：查看文件头10行的内容

​	head -n 5 文件：查看文件头5行的内容

**tail：**

​	显示文件的最后部分

​	tail 文件：查看文件后10行的内容

​	tail-n 5 文件：查看文件后5行的内容

​	tail -f 文件：实时追踪文件内容

**ln：**

​	软链接指令，类似于快捷方式

​	ln  -s 源路径或文件 软链接名

​	删除软链接时最后不要带斜杠

​	通过pwd查看仍是当前目录

**history：**

​	查看已经执行过的历史指令

​	history：查询所有指令

​	history 10：查询最近的10个指令

​	!178：执行历史记录中第178个指令

## 4.4 时间日期类

**date：**

​	date：显示当前时间

​	date "+%Y-%m-%d %H:%M:%S"：格式化显示

​	date -s 字符串时间：设置时间

**cal：**

​	cal：显示当月日历

​	cal 2020：显示2020年的日历

## 4.5 搜索查询类

**find：**

​	find /home -name hello.txt：在/home目录下查找名为hello.txt的文件

​	find /opt -user root：在/opt目录下查找用户为root的文件

​	find / -size +20M：在/目录下查找文件大小大于20M的文件(+n 大于、-n 小于、n 等于)

**locate：**

​	快速定位文件，会先建立一个locate数据库，保持更新

​	updatedb：更新数据库

​	locate 文件名：查找文件

​	yum install mlocate 命令可以直接安装updatedb相关

**grep指令和管道符号|：**

​	grep过滤查找，管道符号|，将前一个命令的结果传递给后一个命令去处理

​	cat hello.txt | grep yes：将hello.txt的内容交给grep yes 处理

​	cat hello.txt | grep -n yes：将hello.txt的内容交给grep yes 处理，显示行号

​	cat hello.txt | grep -i yes：将hello.txt的内容交给grep yes 处理，忽略大小写

## 4.6 压缩和解压缩类

**gzip(压缩)/gunzip(解压缩)：**

​	gzip 文件1 文件2：将文件压缩为.gz文件，不会保留源文件

​	gunzip 文件1：解压.gz文件

**zip(压缩)/unzip(解压缩)：**

​	zip -r myzip.zip /home：压缩/home下的所有文件，生成myzip.zip

​	unzip -d /home myzip.zip：解压myzip.zip到/home

**tar：**

​	打包指令，打包后是.tar.gz文件

​	-c：产生.tar.gz压缩文件

​	-v：压缩和解压缩时显示详细信息

​	-f：指定压缩后的文件名

​	-z：打包同时压缩

​	-x：解压.tar.gz文件



​	tar -zcvf a.tar.gz 文件1 文件2：压缩文件1和文件2为a.tar.gz

​	tar -zcvf a.tar.gz /home：压缩/home为a.tar.gz

​	tar -zxvf a.tar.gz：解压到当前文件夹

​	tar -zxvf a.tar.gz -C /home：解压到/home，目录事先要存在，不加-C，会在当前目录也进行解压