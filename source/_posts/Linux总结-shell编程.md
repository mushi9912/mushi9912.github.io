---
title: Linux总结-shell编程
date: 2020-12-12 17:09:46
tags:
- Linux
categories:
- Linux
permalink: linux-shell
---

# 八、Linux-Shell编程

## 8.1 快速入门

脚本格式的要求：

1. 脚本要以：#!/bin/bash开头
2. 脚本要有可执行权限，没有权限也可以使用sh test.sh来运行，但是不推荐

```shell
#!/bin/bash
echo "Hello World"
```

## 8.2 shell变量

Linux的shell变量分为两种，一种是系统变量，一种是用户自定义变量

显示当前shell中的所有变量：**set**

<!-- more -->

### 8.2.1 定义变量

```shell
A=100
echo $A
echo "A=$A" # A=100
unset A # 撤销变量
echo "A=$A" # A=

readonly A=99 # 静态变量，不能unset
```

### 8.2.2 定义变量的规则

+ 变量名称可以由字母、数字、下划线组成，不能以数字开头

+ 等号两侧不能有空格
+ 变量名称一般习惯为大写

**将命令返回值赋给变量**

+ `` A=`ls -l` ``
+ ` A=$(ls -l) `，等价于反引号

### 8.2.3 环境变量

```shell
export HOME=/root # 将变量设置为环境变量
source 文件名 # 设置立即生效
echo $环境变量 # 输出环境变量

#多行注释
:<<!
XXXXX
!
```

### 8.2.4 位置参数变量

+ $n （功能描述：n为数字，$0代表命令本身，$1-$9代表第一到第九个参数，十以上的参数，十以上的参数需要用大括号包含，如${10}）

+ $* （功能描述：这个变量代表命令行中所有的参数，$*把所有的参数看成一个整体）

+ $@（功能描述：这个变量也代表命令行中所有的参数，不过$@把每个参数区分对待）

+ $#（功能描述：这个变量代表命令行中所有参数的个数）

对于$*, $@，要注意：

1. 不加双引号时，两者均表示列表
2. 加双引号时，"$*"表示一个字符串；"$@"表示列表

### 8.2.5 预定义变量

预定义变量就是shell设计者事先已经定义好的变量，可以直接在shell脚本中使用

+ $$ （功能描述：当前进程的进程号（PID））

+ $! （功能描述：后台运行的最后一个进程的进程号（PID）），后台执行命令为`./myshell.sh &`

+ $? （功能描述：最后一次执行的命令的返回状态。如果这个变量的值为 0，证明上一个命令正确执行；如果这个变量的值为非 0（具体是哪个数，由命令自己来决定），则证明上一个命令执行不正确）

## 8.3 shell运算符

+ “$((运算式))”或“$[运算式]”

+ expr m + n，注意 expr 运算符间要有空格，以及赋给变量要用反引号包起来

+ expr m - n

+ expr  \\*, /, %    乘，除，取余

## 8.4 条件判断

[ condition ]，注意中括号两端有空格

**常用判断条件**

+ 两个数比较

  = 字符串比较

  -lt 小于

  -le 小于等于

  -eq 等于

  -gt 大于

  -ge 大于等于

  -ne 不等于

+ 按照文件权限进行判断

  -r 有读的权限 [ -r 文件 ]

  -w 有写的权限

  -x 有执行的权限

+ 按照文件类型进行判断

  -f 文件存在并且是一个常规的文件

  -e 文件存在

  -d 文件存在并是一个目录

+ 布尔运算

  !  非运算，表达式为 true 则返回 false，否则返回 true。

  -o 或运算，有一个表达式为 true 则返回 true。

  -a 与运算，两个表达式都为 true 才返回 true。

+ 逻辑运算符

  && 逻辑的 AND [[ $a -lt 100 && $b -gt 100 ]] 返回 false

  || 逻辑的 OR [[ $a -lt 100 || $b -gt 100 ]] 返回 true

```shell
if [ "ok" = "ok" ]
then
	echo "equal"
fi

if [ 23 -gt 22 ]
then
	echo "gt"
fi

if [ -e /临时文档.txt ]
then
	echo "文件存在"
fi
```

## 8.5 流程控制

### 8.5.1 if语句

```shell
if [ 条件判断式 ];then
	程序
fi

if [ 条件判断式 ]
then
	程序
elif [条件判断式] 
then
	程序
fi

if [ $1 -ge 60 ]
then
	echo "及格了"
elif [ $1 -lt 60 ]
then
	echo "不及格"
fi
```

### 8.5.2 case语句

```shell
case $1 in 
"1")
	echo "周一"
;;
"2")
	echo "周二"
;;
*)
	echo "其它"
esac
```

### 8.5.3 for语句

```shell
for i in "$*" # 注意加引号，不加为列表,
do
	echo "$i"
done

# 对于字符串的变量，一般最好是加上双引号；对于数值型的变量，可不加双引号

SUM=0
for((i=1;i<=100;i++))
do
	SUM=$[$SUM+$i]
done
echo "SUM=$SUM"
```

### 8.5.4 while语句

```shell
SUM=0
FLAG=0
while [ $FLAG -le $1 ]
do
	SUM=$[$SUM+$FLAG]
	FLAG=$[$FLAG+1]
done
echo "SUM=$SUM"
```

## 8.6 读取控制台输入

```shell
# -p 提示词
read -p "请输入一个值：" NUM
echo "你输入的值是：$NUM"

# -t 10秒内输入
read -t 10 -p "请输入一个值：" NUM1
echo "你输入的值是：$NUM1"
```

## 8.7 shell函数

函数分为系统函数和自定义函数

系统函数**basename**

返回完整路径最后/的部分，常用于获取文件名

```shell
basename /root/test.txt
# test.txt
basename /root/test.txt .txt
# test
```

系统函数**dirname**

返回完整路径最后/的前面的部分，常用于返回路径部分

```shell
dirname /root/test.txt
# /root
```

**自定义函数**

```shell
[ function ] funname [()]
{
    action;
    [return int;]
}

# 
```

- 可以带function fun()  定义，也可以直接fun() 定义,不带任何参数。
- 参数返回，可以显示加：return 返回，如果不加，将以最后一条命令运行结果，作为返回值。 return后跟数值n(0-255)，函数返回值在调用该函数后通过 $? 来获得。
- 在Shell中，调用函数时可以向其传递参数。在函数体内部，通过 $n 的形式来获取参数的值，例如，$1表示第一个参数，$2表示第二个参数...
- 注意，$10 不能获取第十个参数，获取第十个参数需要${10}。当n>=10时，需要使用${n}来获取参数。

