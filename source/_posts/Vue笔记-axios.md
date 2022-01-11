---
title: Vue笔记--axios
date: 2020-02-28 23:42:24
tags:
- Vue
- axios
categories:
- Vue
- Vuejs
permalink: vue-axios
---

# 十二、Vue与axios

## 1、axios的简单使用

+ 支持多种请求方式

  + axios(config)

  + axios.request(config)

  + axios.get(url[, config])

  + axios.delete(url[, config])

    <!-- more -->

  + axios.head(url[, config])

  + axios.post(url[, data[, config]])

  + axios.put(url[, data[, config]])

  + axios.patch(url[, data[, config]])

安装axios

```shell
npm install axios --save
```

简单使用

```js
import axios from "axios"
axios({
  url: "xxxxxx",
  // 设置请求方式，默认是get请求
  method: "post",
  // 专门针对get请求的参数拼接
  params: {
    info: "test"
  }
}).then(res => {
  console.log(res)
})
```

## 2、axios的并发请求

```js
axios.all([axios({
  url: "xxxxxx"
}), axios({
  url: "xxxxxx"
})]).then(res => {
  console.log(res);
})
```

## 3、axios的配置

```js
axios.defaults.baseURL="xxxxxx"
```

+ 请求地址

  url: "xxxxxx"

+ 请求类型

  method: "get"

+ 请求路径

  baseURL: "xxxxxx"

+ 请求前的数据处理

  transformRequest: [function(data){}]

+ 请求后的数据处理

  transformResponse: [function(data){}]

+ 自定义的请求头

  headers{“x-Requested-With”: "XMLHttpRequest"}

+ URL查询对象

  params: {id: 1}

+ 查询对象的序列化函数

  paramsSerializer: function(params){}

+ request body

  data: {key: "aa"}

+ 超时设置

  timeout: 1000

+ 跨域是否带token

  withCredentials: false

+ 自定义请求处理

  adapter: function(resolve, reject, config){}

+ 身份验证信息

  auth: {uname: "xxx", pwd: 123}

+ 响应的数据格式json/blob/document/arraybuffer/text/stream

  responseType: "json"

## 4、axios实例与模块封装

```js
const instance = axios.create({
  baseURL: "xxxxxx",
  timeout: 5000
})

instance({
  url: "xxxxxx"
  method: "post"
})
```

```js
import axios from "axios"
export function request(config) {
  const instance = axios.create({
    timeout: 5000,
    baseURL: "xxxxxx"
  })
  return instance(config)
}

request({
  url: "xxxxxx"
}).then(res => {
  console.log(res);
}).catch(err => {
  console.log(err);
})
```

## 5、axios拦截器

```js
import axios from "axios"
export function request(config) {
  const instance = axios.create({
    timeout: 5000,
    baseURL: "xxxxxx"
  })

  instance.interceptors.request.use(
    config => {
      return config

  },
    err => {

  })
  instance.interceptors.response.use(
    res => {

      // return res
      return res.data
    },
    err => {

    }
  )
  return instance(config)
}

request({
  url: "xxxxxx"
}).then(res => {
  console.log(res);
}).catch(err => {
  console.log(err);
})
```