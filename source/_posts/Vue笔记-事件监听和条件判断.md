---
title: Vue笔记--事件监听和条件判断
date: 2020-02-15 16:47:40
tags:
- Vue
- 入门
categories:
- Vue
- Vuejs
permalink: vue-listenerandif
---

# 五、Vue事件监听和条件判断

## 1、v-on的基本使用

可以查看之前的计数器案例

<!-- more -->

## 2、v-on参数

```html
<!DOCTYPE html>
<html lang="zh-CN">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
  <script src="../js/vue.js"></script>
</head>

<body>
  <div id="app">
    <h2>{{message}}</h2>
    <button @click="btu1">按钮1</button>
    <button @click="btu2">按钮2</button>
    <button @click="btu2(123, $event)">按钮3</button>
  </div>

  <script>
    const app = new Vue({
      el: '#app',
      data: {
        message: "hello"
      },
      // event是浏览器产生的对象，会自动传入
      methods: {
        btu1(event) {
          console.log(event);
        },
        btu2(abc, event) {
          console.log(abc);
          console.log(event);
        }
      }
    });
  </script>
</body>

</html>
```

## 3、v-on修饰符

```html
<!DOCTYPE html>
<html lang="zh-CN">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
  <script src="../js/vue.js"></script>
</head>

<body>
  <div id="app">
    <h2>{{message}}</h2>
    <div @click="divClick">
      <!-- stop修饰符阻止冒泡，只打印btn -->
      <button @click.stop="btnClick">按钮</button>
      <!-- prevent修饰符阻止默认行为，例如form提交不让浏览器自动提交，而是我们自己写函数 -->
      <!-- 点击enter松开会触发事件，@keyup=""监听任何按键 -->
      <input type="text" @keyup.enter="keyClick">
    </div>
    <!-- 只触发一次 -->
    <button @click.once="btnClick">按钮2</button>
  </div>

  <script>
    const app = new Vue({
      el: '#app',
      data: {
        message: "hello"
      },
      methods: {
        divClick() {
          console.log("div");
        },
        btnClick() {
          console.log("btn");
        },
        keyClick() {
          console.log("enter")
        }
      }
    });
  </script>
</body>

</html>
```

## 4、Vue条件判断

```html
<body>
  <div id="app">
    <h2 v-if="flag">{{message}}</h2>
    <!-- v-else 元素必须紧跟在带 v-if 或者 v-else-if 的元素的后面，否则它将不会被识别。 -->
    <h2 v-if="flag">{{message}}</h2>
    <h2 v-else>else测试</h2>

    <h2 v-if="score>=90">优秀</h2>
    <h2 v-else-if="score>=80">良好</h2>
    <h2 v-else-if="score>=60">及格</h2>
    <h2 v-else>不及格</h2>

  </div>

  <script>
    const app = new Vue({
      el: '#app',
      data: {
        message: "hello",
        flag: true,
        score: 86
      },
      methods: {
        
      }
    });
  </script>
</body>
```

## 5、登录类型切换案例

```html
<body>
  <div id="app">
    <h2>{{message}}</h2>
    <span v-if="isUser">
      <label for="username">用户账户</label>
      <input id="username" type="text" placeholder="用户名" key="abc">
    </span>
    <span v-else>
      <label for="email">用户邮箱</label>
      <input id="email" type="text" placeholder="邮箱名" key="def">
    </span>
    <button @click="isUser = !isUser">切换</button>
  </div>

  <script>
    const app = new Vue({
      el: '#app',
      data: {
        message: "hello",
        isUser: true,
      },
      methods: {
        
      }
    });
  </script>
</body>
```

Vue为了性能考虑会复用一些dom，例如在input输入数据后点击切换，会发现input里的东西还在，这就是因为vue复用了一些dom，加一个key=""就可以解决问题

## 6、v-show的使用

```html
<body>
  <div id="app">
    <!-- 区别当isShow为false时，v-if的元素不会存在于dom中，v-show只是增加了一个行内样式进行隐藏 -->
    <h2 v-if="isShow">{{message}}</h2>
    <h2 v-show="isShow">{{message}}</h2>
  </div>

  <script>
    const app = new Vue({
      el: '#app',
      data: {
        message: "hello",
        isShow: true
      },
      methods: {
        
      }
    });
  </script>
</body>
```

