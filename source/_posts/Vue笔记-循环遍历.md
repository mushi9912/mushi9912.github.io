---
title: Vue笔记--循环遍历
date: 2020-02-15 18:40:56
tags:
- Vue
- 入门
categories:
- Vue
- Vuejs
permalink: vue-cycle
---

# 六、Vue循环遍历

## 1、遍历数组

<!-- more -->

```html
<body>
  <div id="app">
    <h2>{{message}}</h2>
    <ul>
      <li v-for="game in games">{{game}}</li>
    </ul>
    <!-- 获取下标 -->
    <ul>
      <li v-for="(game, index) in games">{{index}}-{{game}}</li>
    </ul>
  </div>

  <script>
    const app = new Vue({
      el: '#app',
      data: {
        message: "hello",
        games: ['Mincraft', 'Warcraft III', 'Ori']
      },
      methods: {
        
      }
    });
  </script>
</body>
```

## 2、遍历对象

```html
<body>
  <div id="app">
    <h2>{{message}}</h2>
    <!-- 如果只获取一个值，获取的是value -->
    <ul>
      <li v-for="value in game">{{value}}</li>
    </ul>
    <!-- 获取value和key -->
    <ul>
      <li v-for="(value, key) in game">{{key}}-{{value}}</li>
    </ul>
    <!-- 获取value、key和index -->
    <ul>
      <li v-for="(value, key, index) in game">{{index}}-{{key}}-{{value}}</li>
    </ul>
  </div>

  <script>
    const app = new Vue({
      el: '#app',
      data: {
        message: "hello",
        game: {
          id: 1,
          name: 'Ori',
          price: 68
        },
      },
      methods: {

      }
    });
  </script>
</body>
```

## 3、绑定与不绑定key的区别

使用v-for的时候尽量绑定key例如:key="item",这样例如在列表中间插入数据，会加快效率

## 4、数组的响应式方法和非响应式方法

```html
<body>
  <div id="app">
    <h2>{{message}}</h2>
    <ul>
      <li v-for="game in games" :key="game">{{game}}</li>
    </ul>
    <button @click="btnClick">按钮</button>
  </div>

  <script>
    const app = new Vue({
      el: '#app',
      data: {
        message: "hello",
        games: ["WarCraft III", "StarCraft II", "Ori II"]
      },
      methods: {
        btnClick() {
          // 响应式
          // 1、在队尾插入元素
          // this.games.push("ARK", "TouHou Project");
          // 2、在队尾删除元素
          // this.games.pop();
          // 3、删除数组的第一个元素
          // this.games.shift()
          // 4、在数组最前面插入元素
          // this.games.unshift("ARK", "TouHou Project");
          // 5、splice函数
          // 删除元素 this.games.splice(2)默认删除第三个(包括第三个)之后的所有元素 this.games.splice(1,1)删除第二个本身
          // 替换元素 this.games.splice(2, 1, "ARK", "TouHou Project")替换第二个后的元素(可理解为先删除第二个后的一个元素再插入)
          // 插入元素 this.games.splice(2, 0, "ARK")插入在第二个元素之后
          // 6、排序
          // this.games.sort();
          // 7、数组反转
          // this.games.reverse();


          // 非响应式
          // 通过索引修改数组的数据是非响应式的
          // this.games[0] = "WarCraft III Remake";
          // 如果想修改值，可以使用splice，也可以使用Vue.set(要修改的对象, 索引值, 修改的值)
          // Vue.set(this.games, 0, "WarCraft III Remake");
        }
      }
    });
  </script>
</body>
```

## 5、补充一些js高阶函数的使用

首先是Vue的filters

```html
使用方式
{{price | showPrice}}

filters: {
  showPrice(price) {
    return "¥" + price.toFixed(2);
  }
}
```

还有关于循环遍历的使用方式以及filter、map、reduce函数的使用

```javascript
computed: {
        // 默认是由get，set方法的fullname () {}就相当于调用了get方法，set方法一般不用
        fullname () {
          // 关于let和const在for中的应用可以看  https://www.cnblogs.com/SamWeb/p/10659352.html
          // 1、普通的for循环
          // let num = 0;
          // for(let i=0; i<this.games.length; i++){
          //   num += this.games[i].price;
          // }
          // 2、forin循环
          // for (const i in this.games) {
          //   console.log(i+ "---" + this.games[i]);
          // }
          // 3、forof循环
          // for (const game of this.games) {
          //   console.log(game);
          // }
          // 4、filter函数的使用
          // nums = [11,23,4,6,78,100]
          // let filter =nums.filter(function (n) {
          //   return n<100;  返回一个true或false，true则放进一个数组里
          // });
          // console.log(filter);
          // 5、map函数的使用
          // nums = [11,23,4,6,78,100]
          // let map = nums.map(function (n) {
          //   return n*2;  返回一个处理过的数组
          // })
          // console.log(map)
          // 6、reduce函数的使用
          // nums = [11,23,4,6,78,100]
          // let reduce = nums.reduce(function (preValue, n) {
          //   return preValue+n;  preValue是上一次return的值
          // }, 0);
          // 第一次运行 0,11
          // 第二次运行 11,23
          // 第三次运行 34,4
          // 还可以写链式函数
          // nums = [11,23,4,6,78,100]
          // let mapReduce = nums.filter(function (n) {
          //   return n <78;
          // }).map(function (n){
          //   return n*2;
          // }).reduce(function (preValue,n){
          //   return preValue+n;
          // }, 0);

          // 此外，还有一个箭头函数的写法，另外关于this的指向问题，请参考https://blog.csdn.net/weixin_39936058/article/details/87971593
          // 正常函数写法
          // [1,2,3].map(function (x) {
          //   return x + x;
          // });
          
          // 箭头函数写法
          // [1,2,3].map(x => x + x);
          // let mapReduce = nums.filter(n => n>78).map(n => n*2).reduce((preValue,n) => preValue+n, 0)
          
        },
},
```

