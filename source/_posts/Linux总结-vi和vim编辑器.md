---
title: Linux总结-vi和vim编辑器
date: 2020-11-08 14:34:56
tags:
- Linux
- vim
categories:
- Linux
permalink: linux-vim
---

# 二、Linux编辑器

## 2.1 vi和vim的使用

所有的 Linux 系统都会内建 vi 文本编辑器。

vim 具有程序编辑的能力，可以看做是 vi 的增强版本，可以主动的以字体颜色辨别语法的正确性，方便程序设计。代码补完、编译及错误跳转等方便编程的功能特别丰富，在程序员中被广泛使用。

## 2.2 vi和vim常用的三种模式

### 2.2.1 正常模式

在正常模式下，我们可以使用快捷键。

以 vim 打开一个档案就直接进入一般模式了(这是默认的模式)。在这个模式中， 你可以使用『上下左右』按键来移动光标，你可以使用『删除字符』或『删除整行』来处理档案内容， 也可以使用『复制、粘贴』来处理你的文件数据。

<!-- more -->

### 2.2.2 插入模式/编辑模式

在模式下，程序员可以输入内容。

按下 i, I, o, O, a, A, r, R 等任何一个字母之后才会进入编辑模式, 一般来说按 i 即可

### 2.2.3 命令模式

在这个模式当中， 可以提供你相关指令，完成读取、存盘、替换、离开 vim 、显示行号等的动作则是在此模式中达成的

## 2.3 vi和vim常用快捷键

1. 拷贝当前行，在正常模式下按**yy**，拷贝当前行向下的 5 行**5yy**，并粘贴**p**。

2. 删除当前行**dd**，删除当前行向下的 5 行**5dd**。

3. 在文件中查找某个单词，在命令行模式下输入**/关键字**，回车查找，输入**n**就是查找下一个。

4. 设置文件的行号，取消文件的行号，在命令行模式下输入**:set nu**和**:set nonu**。
5. 使用快捷键到底文档的最末行**G**和最首行**gg**，注意这些都是在正常模式下执行的。
6. 在一个文件中输入后，然后又撤销这个动作，在正常模式下输入**u**。
7. 将光标移动到  第 20 行，首先显示行号**:set nu**，然后输入20这个数，最后输入**shift+g**。在此基础上输入10，然后按回车会到第30行。

## 2.4 中文乱码

```shell
首先查看系统语言，命令是"locale"，编辑配置
一般情况下，大家的路径都是 /etc/sysconfig/i18n

如果你的计算机里没有这个文件怎么办？
其实也很简单：

# Centos 系统 ： /etc/locale.conf 
# Ubuntu 系统 ： /etc/locale.gen (我这里用的是aliyun的云服务器）

为什么这里会使用这个文件呢？这个跟 "Linux正常启动的时候加载的环境变量文件"有关！

# 查看你的 /etc/profile.d/lang.sh 文件
 11   for langfile in /etc/locale.conf "$HOME/.i18n" ; do
 12         [ -f $langfile ] && . $langfile && sourced=1
 13     done
：set nu
看见没 ，上面有个路径 "/etc/locale.conf"  这个就是相当于 "/etc/sysconfig/i18n"

在编辑vim的配置文件，编辑/etc/vimrc，修改为

if v:lang =~ "utf8$" || v:lang =~ "UTF-8$"
   set fileencodings=ucs-bom,utf-8,utf-16,gbk,big5,gb18030,latin1
   set termencoding=utf-8
   set encoding=utf-8
endif

```