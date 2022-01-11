---
title: Vue笔记--插值操作
date: 2020-02-14 18:35:07
tags:
- Vue
- 入门
categories:
- Vue
- Vuejs
permalink: vue-interpolation
---

# 二、Vue插值操作

## 1、Mustache语法

```html
  <div id="app">
    <h2>{{message}}</h2>
    <h2>{{message + ' '+ messageTwo}}</h2>
    <h2>{{message}} {{messageTwo}}</h2>
    <h2>{{counter * 2}}</h2>
  </div>
```

<!-- more -->

## 2、v-xxx指令的使用

```html
<style>
  [v-cloak] {
    display: none;
  }
</style>
<body>
  <div id="app">
    <h2>{{message}}</h2>
    <!-- 使用v-text代替{{message}} -->
    <h2 v-text="message"></h2>
    <!-- 只渲染一次，接下来改变值，v-once使被标记的标签内容不再改变 -->
    <h2 v-once>{{message}}</h2>
    <!-- 原封不动的显示{{message}}s -->
    <h2 v-pre>{{message}}</h2>
    <h2>{{url}}</h2>
    <!-- 渲染HTML标签 -->
    <h2 v-html="url"></h2>
  </div>

  <!-- 加上v-cloak进行判断，在vue解析前，有v-cloak，解析后就没有了v-cloak，所以我们可以判断有v-cloak的为未解析完的进行隐藏 -->
  <div id="cloak">
    <h2 v-cloak>{{message}}</h2>
  </div>
  <script>
    const app = new Vue({
      el: '#app',
      data: {
        message: "hello",
        url: '<a href="https://www.baidu.com">百度一下</a>'
      },
      methods: {
        
      }
    });

    setTimeout(function (){
      const cloak = new Vue({
        el: "#cloak",
        data: {
          message: "cloak的测试"
        }
      })
    }, 2000)
    // 暂停2秒
  </script>
</body>
```

