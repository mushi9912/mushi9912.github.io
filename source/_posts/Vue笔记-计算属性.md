---
title: Vue笔记--计算属性
date: 2020-02-15 10:22:51
tags:
- Vue
- 入门
categories:
- Vue
- Vuejs
permalink: vue-calculate
---

# 四、Vue计算属性

## 1、计算属性的使用

Vue计算属性的使用

<!-- more -->

```html
<body>
  <div id="app">
    <h2>总价格：{{fullname}}</h2>
  </div>

  <script>
    const app = new Vue({
      el: '#app',
      data: {
        message: "hello",
        games: [
          {id: 1, name: 'Ori', price: 68},
          {id: 2, name: 'Warcraft III', price: 256},
          {id: 3, name: 'Starcraft II', price: 168},
        ]
      },
      methods: {
        
      },
      computed: {
        fullname () {
          // 关于let和const在for中的应用可以看  https://www.cnblogs.com/SamWeb/p/10659352.html
          let num = 0;
          for(let i=0; i<this.games.length; i++){
            num += this.games[i].price;
          }
          for (const i in this.games) {
            console.log(i);
            console.log(this.games[i]);
          }
          for (const game of this.games) {
            console.log(game);
          }
          return num;
        },
      },
    });
  </script>
</body>
```

计算属性的fullname () {}其实是默认调用了get方法，也有set方法但是一般不用

## 2、computed和methods的对比

计算属性是有缓存的，只会执行一次，之后会从缓存里取，改变属性会再执行一次

## 3、ES6的增强写法

```javascript
const age = "13"
const name = "qwe"
const gender = "男"
// ES5
const person = {
  age: age,
  name: name,
  gender: gender
}
// ES6
const person = {
  age,
  name,
  gender
}


methods: {
  getClass: function () {
	return {active: this.isActive, title: this.isTitle}
  }
  getClass () {
	return {active: this.isActive, title: this.isTitle}
  }
}
```

