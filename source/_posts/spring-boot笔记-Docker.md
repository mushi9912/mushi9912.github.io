---
title: spring-boot笔记--Docker
date: 2020-02-18 10:38:43
tags:
- Spring Boot
- Docker
- Linux
categories:
- Spring Boot
- Docker
permalink: spring-boot-docker
---

# 八、Spring Boot与Docker

## 1、Docker的安装

1. 安装Linux虚拟机，这里我们使用一款免费开源的虚拟机软件**Oracle VM VirtualBox**，这款虚拟软件机体积小，而且是免费的，而虚拟机我使用的是最新的centos8版本

2. 为了方便写命令，还可以使用客户端连接虚拟机，这里使用的是xshell6，可以免费下载非商用版本，[家庭/学校免费版本](https://www.netsarang.com/zh/free-for-home-school/)，填写姓名和邮箱，勾选两者，会把下载链接发送到邮箱

3. 虚拟机网络设置，选择桥接网卡，有Wireless的是无线网卡，有Family Controller的是有线网卡，勾选高级里的接入网线

   <!-- more -->

4. [参考Docker官方文档](https://docs.docker.com/install/linux/docker-ce/centos/)，到时间2020-02-18时，在执行下面的命令时

   ```shell
   sudo yum install docker-ce docker-ce-cli containerd.io
   ```

   会出错，因为containerd.io的版本问题，我们可以下载[最新版本](https://download.docker.com/linux/centos/7/x86_64/stable/Packages/)，然后再安装

   ```shell
   sudo dnf install https://download.docker.com/linux/centos/7/x86_64/stable/Packages/containerd.io-1.2.6-3.3.el7.x86_64.rpm
   ```

   centos8里使用dnf代替yum但是yum仍可以使用

   ```shell
   sudo yum install docker-ce docker-ce-cli
   ```

## 2、Docker的操作

1. 检索

   ```shell
   docker search ...
   ```

2. 拉取/下载

   ```shell
   docker pull ...
   docker pull mysql:版本号
   不加：默认是最新版
   ```

   如果pull过慢，我使用的是阿里云的镜像加速器，首先得有阿里云的账号

3. 查看本地所有镜像

   ```shell
   docker images
   ```

4. 删除

   ```html
   docker rmi images-id
   ```

## 3、docker容器操作

首先有一个软件镜像，运行镜像产生一个容器（代表正在运行的软件）

1. 运行

   ```shell
   docker run --name [container-name] -d [image-name]
   	--name: 自定义容器名
   	-d: 后台运行
   	image-name: 指定镜像模板
   ```

2. 查看

   ```shell
   docker ps
   查看运行中的容器
   docker ps -a
   查看是否有启动失败的容器，查看所有容器
   docker logs [id]
   查看启动失败原因，查看日志
   ```

3. 停止

   ```shell
   docker stop [id]/[name]
   ```

4. 删除

   ```shell
   docker rm [id]
   ```

5. 启动

   ```shell
   docker start [id]
   ```

6. 端口映射

   ```shell
   -p 8080(主机端口):8080(容器端口)
   ```

7. 有时Linux的防火墙会禁止我们访问

   ```shell
   service firewalld status
   查看防火墙状态
   service firewalld stop
   关闭防火墙
   ```

8. 更多命令参考[官方文档](https://docs.docker.com/engine/reference/commandline/docker/)

## 4、安装mysql

```shell
docker pull mysql

错误的启动
docker run --name mydata -d mysql
mysql运行时必须指定密码

在docker hub搜索镜像时，也有介绍如何启动镜像
正确的启动
docker run -p 3306:3306 --name mydata -e MYSQL_ROOT_PASSWORD=123456 -d mysql

但是用Navicat连接还会出现错误
2059错误: Authentication plugin 'caching_sha2_password' cannot be loaded

解决方法：
1. docker exec -it 236b2624632d(你的id) bash  进入容器，在容器中执行命令
2. mysql -u root -p  连接mysql
3. alter user 'root'@'%' identified with mysql_native_password by '123456';
   将用户的加密方式改为mysql_native_password
4. flush privileges;  使权限配置项立即生效


如果在使用docker过程中出现错误
docker: Error response from daemon: driver failed programming external connectivity on endpoint mydata (33a937d07a142ebc1dad11c33adccfbe6fb68071a5d1043f8f40c527c15d4ec4):  (iptables failed: iptables --wait -t nat -A DOCKER -p tcp -d 0/0 --dport 3306 -j DNAT --to-destination 172.17.0.2:3306 ! -i docker0: iptables: No chain/target/match by that name.

重启即可


几个其它的操作
docker run --name some-mysql -v /my/custom:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag

将主机的/my/custom文件夹挂载到mysql docker容器中的/etc/mysql/conf.d文件夹里面
改mysql的配置只需要把mysql配置文件放在/my/custom

docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci

不用配置文件，配置mysql
```



