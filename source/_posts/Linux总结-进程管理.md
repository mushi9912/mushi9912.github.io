---
title: Linux总结-进程管理
date: 2020-12-02 20:46:18
tags:
- Linux
categories:
- Linux
permalink: linux-process
---

# 七、Linux进程管理

## 7.1 进程的基本介绍

1. 在Linux中，每个执行的程序（代码）都称为一个进程，每一个进程都分配一个ID号。

2. 每一个进程，都会对应一个父进程，而这个父进程可以复制多个子进程。例如www服务器。

3. 每个进程都可能以两种方式存在的。前台与后台，所谓前台进程就是用户目前的屏幕上可以进行操作的。后台进程则是实际在操作，但由于屏幕上无法看到的进程，通常使用后台方式执行。

4. 一般系统的服务都是以后台进程的方式存在，而且都会常驻在系统中。直到关机才才结束。

## 7.2 ps命令

**ps：**

​	ps命令用来查看当前运行了那些进程，可以不带任何参数

​	ps -a：显示当前终端的所有进程信息

​	ps -u：已用户的格式显示信息

​	ps -x：显示后台进程运行的参数

​	ps -e：显示所有进程

​	ps -f：全格式显示

<!-- more -->

{% asset_img psdetail.jpg 进程显示详情 %}

**ps指令详解：**

一般使用：ps –aux|grep xxx ，比如ps –aux|grep java

+ System V：展示风格
+ USER：用户名称
+ PID：进程号
+ %CPU：进程占用CPU的百分比
+ #MEM：进程占用物理内存的额百分比

+ VSZ：进程占用的虚拟内存大小（单位：KB）

+ RSS：进程占用的物理内存大小（单位：KB）

+ TT：终端名称,缩写 .

+ STAT：进程状态，其中 S-睡眠，s-表示该进程是会话的先导进程，N-表示进程拥有比普通优先级更低的优先级，R-正在运行，D-短期等待，Z-僵死进程，T-被跟踪或者被停止等等

+ STARTED：进程的启动时间

+ TIME：CPU 时间，即进程使用 CPU 的总时间

+ COMMAND：启动进程所用的命令和参数，如果过长会被截断显示

如果想查看父进程，可以用：ps -ef|grep xxx

+ UID：用户 ID

+ PID：进程 ID

+ PPID：父进程 ID

+ C：CPU 用于计算执行优先级的因子。数值越大，表明进程是 CPU 密集型运算，执行优先级会降低；数值越小，表明进程是 I/O 密集型运算，执行优先级会提高

+ STIME：进程启动的时间

+ TTY：完整的终端名称

+ TIME：CPU 时间

+ CMD：启动进程所用的命令和参数

## 7.3 终止进程

**kill、killall：**

​	kill [选项] 进程号（功能描述：通过进程号杀死进程）

​	killall 进程名称（功能描述：通过进程名称杀死进程，也支持通配符，这在系统负载过大而变得很慢时很有用）

​	-9：表示强迫进程立即停止

**pstree：**

​	以树状的形式展示进程

​	-p：显示进程的 PID

​	-u：显示进程的所属用户

## 7.4 服务管理

**防火墙：**

​	systemctl enable iptables
​	//其他设置
​	systemctl stop iptables
​	systemctl start iptables
​	systemctl restart iptables
​	systemctl reload iptables

**查看所有服务的状态：**

​	systemctl list-unit-files

​	systemctl list-dependencies [target]

## 7.5 监控服务

**动态监控进程：**

​	top命令和ps相似，但是可以在一定时间后更新进程

​	top -d 秒数：默认为3秒

​	top -i：使top不显示任何僵死或闲置进程

​	top -p：通过指定监控进程id，来监控某个进程的状态

​	**交互操作：**

​		p：以CPU使用率排序，默认

​		m：以内存的使用率排序

​		n：以pid排序

​		u：监视属于特定用户的进程

​		k：输入id，终止某个进程

​		q：退出

**监控网络状态：**

​	netstat -anp：查看所有的网络服务，没有-p的信息，请登录root

​	netstat -an：按照一定顺序输出

​	netstat -p：显示哪个进程在调用