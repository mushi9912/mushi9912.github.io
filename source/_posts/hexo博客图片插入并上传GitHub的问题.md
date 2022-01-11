---
title: hexo博客图片插入并上传GitHub的问题
date: 2019-12-15 20:41:16
tags:
---
# hexo上传图片的问题

由于要将hexo博客里的文件上传到GitHub，如何上传图片资源并且在GitHub实时显示出来。

首先设置**_config.yml**文件，将**post-asset-folder**字段设定为**true**

然后可在生成md文档时在同目录下生成一个同名的文件夹

<!-- more -->

然后可以查找[hexo文档](https://hexo.io/zh-cn/docs/)，在*目录*-*标签插件*-*引用资源*可以看到

{% asset_img 引用图片.jpg hexo博客图片插入并上传GitHub的问题 %}

代码示例
```
{% asset_img 引用图片.jpg hexo博客图片插入并上传GitHub的问题 %}

```

之后上传到GitHub就可以显示图片了。

