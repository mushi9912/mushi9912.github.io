---
title: Linux总结-任务调度
date: 2020-11-23 22:04:46
tags:
- Linux
categories:
- Linux
permalink: linux-task
---

# 六、Linux任务调度

## 6.1 crond任务调度

**crontab 选项**

1. -e：编辑crontab定时任务
2. -l：查询crontab定时任务
3. -r：删除当前用户所有的crontab定时任务

{% asset_img 1.jpg 选项参数说明 %}

{% asset_img 2.jpg 特殊符号和案例 %}

## 6.2 应用实例

编写sh文件，例如/test.sh，内容为ls -l / >> /test，然后给一个可执行权限chmod 744 /home/test.sh，接着输入crontab -e命令，写入*/1 * * * *  /test.sh，保存成功

1. conrtab –r：终止任务调度。

2. crontab –l：列出当前有那些任务调度

3. service crond restart   [重启任务调度]