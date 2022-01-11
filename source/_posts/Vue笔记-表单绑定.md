---
title: Vue笔记--表单绑定
date: 2020-02-16 13:13:29
tags:
- Vue
- 入门
categories:
- Vue
- Vuejs
permalink: vue-form
---

# 七、Vue表单绑定

## 1、input与data数据的双向绑定

双向绑定是指数据改变，input的value跟着改变，input的value改变，数据也跟着改变

<!-- more -->

```html
<body>
  <div id="app">
    <h2>{{message}}</h2>
    <input type="text" v-model="message">
    <!-- 还有另一种方法可以实现 -->
    <input type="text" :value="message" @input="message = $event.target.value">
  </div>

  <script>
    const app = new Vue({
      el: '#app',
      data: {
        message: "hello"
      },
      methods: {
        
      }
    });
  </script>
</body>
```

2、各种input的绑定

```html
<body>
  <div id="app">
    <h2>{{message}}</h2>
    <!-- text类型 -->
    <input type="text" v-model="message">
    <!-- 还有另一种方法可以实现 -->
    <br>
    <input type="text" :value="message" @input="message = $event.target.value">
    <br>
    <!-- radio类型 -->
    <label for="male">
      <input type="radio" id="male" value="男" v-model="gender">男
    </label>
    <label for="female">
      <input type="radio" id="female" value="女" v-model="gender">女
    </label>
    <br>
    <h2>选中了：{{gender}}</h2>
    <!-- checkbox类型 -->
    <!-- checkbox单选框 -->
    <label for="license">
      <input type="checkbox" id="license" v-model="isLicense">同意
    </label>
    <button :disabled="!isLicense">同意</button>
    <br>
    <!-- checkbox多选框 -->
    <input type="checkbox" value="计算机原理" v-model="books">计算机原理
    <input type="checkbox" value="C语言程序设计" v-model="books">C语言程序设计
    <input type="checkbox" value="python从开始到入土" v-model="books">python从开始到入土
    <input type="checkbox" value="Java从基础到高级" v-model="books">Java从基础到高级
    <h2>您的选择是：{{books}}</h2>
    <br>
    <!-- select类型 -->
    <select name="university" v-model="university">
      <option value="北京大学">北京大学</option>
      <option value="清华大学">清华大学</option>
      <option value="西安交通大学">西安交通大学</option>
    </select>
    <h2>您的大学是：{{university}}</h2>
  </div>

  <script>
    const app = new Vue({
      el: '#app',
      data: {
        message: "hello",
        gender: '男',
        isLicense: false,
        books: [],
        university: '北京大学'
      },
      methods: {
        
      }
    });
  </script>
</body>
```

3、v-model的修饰符

```html
<body>
  <div id="app">
    <h2>{{message}}</h2>
    <!-- lazy修饰符，当敲回车和失去焦点时才会改变 -->
    <label for="message">
      <input type="text" id="message" v-model.lazy="message">
    </label>
    <!-- number修饰符，修改值默认是string类型，加了number后会变为number类型 -->
    <label for="score">
      <input type="number" id="score" v-model.number.lazy="score">
    </label>
    <h2>{{score}}--{{typeof score}}</h2>
    <!-- trim去除两端空格 -->
    <label for="name">
      <input type="text" id="name" v-model.trim.lazy="name">
    </label>
  </div>

  <script>
    const app = new Vue({
      el: '#app',
      data: {
        message: "hello",
        score: 0,
        name: ""
      },
      methods: {
        
      }
    });
  </script>
</body>
```

