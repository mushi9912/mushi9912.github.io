---
title: spring-boot笔记--检索
date: 2020-02-18 16:52:49
tags:
- Spring Boot
- Elasticsearch
categories:
- Spring Boot
- Elasticsearch
permalink: spring-boot-search
---

# 九、Spring Boot与检索

## 1、docker下载使用Elasticsearch

下载Elasticsearch

```shell
docker pull elasticsearch
```

启动

<!-- more -->

```shell
docker network create somenetwork
设置占用空间大小-e "ES_JAVA_OPTS=-Xms256m -Xmx256m"
docker run --net somenetwork -e "ES_JAVA_OPTS=-Xms256m -Xmx256m" -d -p 9200:9200 -p 9300:9300 --name mysearch [id]
```

输入IP地址:9200，出现json数据，就成功了

关于Elasticsearch的使用，就是使用Restful风格检索和插入数据，具体可看[《Elasticsearch 权威指南》中文版](https://www.elastic.co/guide/cn/elasticsearch/guide/current/index.html)

## 2、Spring Data操作Elasticsearch

