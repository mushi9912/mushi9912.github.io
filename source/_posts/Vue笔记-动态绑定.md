---
title: Vue笔记--动态绑定
date: 2020-02-14 21:29:33
tags:
- Vue
- 入门
categories:
- Vue
- Vuejs
permalink: vue-binding
---

# 三、Vue动态绑定

## 1、动态绑定class

动态绑定class

<!-- more -->

```html
<body>
  <div id="app">
    <!-- 绑定属性 -->
    <h2 v-bind:class="bind">{{message}}</h2>
    <!-- 语法糖的写法 -->
    <h2 :class="bind">{{message}}</h2>
  </div>

  <!-- 动态绑定对象 -->
  <!-- 对象就是{key: value, key: value} -->
  <div id="object">
    <!-- 对象语法 -->
    <h2 class="ceshi" :class="getClass()">{{message}}</h2>
    <!-- 数组语法 -->
    <h2 class="ceshi" :class="['active', 'title']">{{message}}</h2>
  </div>

  <script>
    const app = new Vue({
      el: '#app',
      data: {
        message: "hello",
        bind: "binding"
      },
      methods: {
        
      }
    });

    const object = new Vue({
      el: "#object",
      data: {
        message: "object binding",
        isActive: true,
        isTitle: true
      },
      methods: {
        getClass: function () {
          return {active: this.isActive, title: this.isTitle}
          // return {'active': this.isActive, 'title': this.isTitle}  加不加引号都可以
        }
      }
    });
  </script>
</body>
```

## 2、响应式文字点击高亮写法

要注意是响应式的，数据改变，HTML也随之改变，如果将:class=""里写为一个函数return的值，每次改变findex的数据，该函数也会重新运行

```html
<body>
  <div id="app">
    <ul>
      <li v-for="(game, index) in games" class="game" :class="{active: index == findex}" @click="isClick(index)">{{game}}</li>
    </ul>
  </div>

  <script>
    const app = new Vue({
      el: '#app',
      data: {
        message: "hello",
        games: ['Mincraft', 'Warcraft III', 'Ori'],
        findex: 0
      },
      methods: {
        isClick: function (index) {
          this.findex = index;
        }
      }
    });
  </script>
</body>
```

## 3、动态绑定style

```html
<body>
  <div id="app">
    <!-- 动态绑定style -->
    <!-- 对象就是{key(css属性): value, key: value} -->
    <!-- 属性可以使用驼峰命名法，如果不用驼峰命名则属性需要加上单引号 -->
    <!-- 错误写法 {font-style: '50px'} -->
    <h2 :style="{fontSize: '50px'}">{{message}}</h2>
    <h2 :style="[myStyle, myStyle2]">{{message}}</h2>
  </div>

  <script>
    const app = new Vue({
      el: '#app',
      data: {
        message: "hello",
        myStyle: {fontSize: '50px'},
        myStyle2: {color: 'red'},
      },
      methods: {
        
      },
    });
  </script>
</body>
```

