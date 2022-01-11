---
title: Vue笔记--初步学习
date: 2020-02-01 14:37:21
tags:
- Vue
- 入门
categories:
- Vue
- Vuejs
permalink: vue-start
---

# 一、初识Vue

## 1、Vue介绍

作者所用软件为vscode

vscode相关快捷键

1. 显示资源管理器：**Ctrl + Shift + E**
2. 显示搜索： **Ctrl + Shift + F**
3. 显示git：**Ctrl + Shift + G**
4. 显示Debug：**Ctrl + Shift + D**
5. 显示终端：**Ctrl + Shift + C**
6. 新建vscode终端：**Ctrl + Shift + `**

<!-- more -->

注释

1. 单行注释：**Ctrl + /**
2. 多行注释：**Shift + Alt + /**

查找替换

1. 查找：**Ctrl + F**
2. 全局查找： **Ctrl + Shift + F**
3. 查找替换：**Ctrl + H**

光标

1. 移动到行首： **Home**
2. 移动到行尾：**End**
3. 移动到文件头部：**Ctrl + Home**
4. 移动到文件结尾：**Ctrl + End**
5. 选择光标到行首：**Shift + Home**
6. 选择光标到行尾：**Shift + End**
7. 行内小范围选择：**Ctrl + Shift + Right / Left**
8. 选择多行复制到行上：**Shift + Alt + Top**
9. 选择多行复制到行下：**Shift + Alt + Bottom**
10. 同时选中所有匹配项：**Ctrl + Shift + L**
11. 回退到上一个光标：**Ctrl + U**
12. 光标行移动到上一行：**Alt + Top**
13. 光标行移动到下一行：**Alt + Bottom**
14. 折叠光标行内代码：**Ctrl + Shift + 【**
15. 展开光标行内代码：**Ctrl + Shift + 】**

## 2、HelloVue

```html
<body>
    <div id="test">{{message}}</div>
    <script src="../js/vue.js"></script>
    <script>
        // let 变量 const 常量
        const test =  new Vue({
            el: '#test', //用于挂载要管理的元素
            data: {
                message: "qwer"
            }
            // 将data的元素放入原html的位置
        })
    </script>
</body>
```

## 3、列表展示

```html
<body>
    <div id="app">
        <ul>
            <li v-for="item in games">{{item}}</li>
        </ul>
        {{games}}
    </div>
    <script src="../js/vue.js"></script>
    <script>
        const app = new Vue({
            el: '#app',
            data: {
                message: 'list测试',
                games: ['Mincraft', 'Warcraft III', 'Ori']
            }
        })
    </script>
</body>
```

## 4、计数器案例

```html
<body>
    <div id="app">
        <h2>当前计数：{{counter}}</h2>
        <!-- 
            整体也可以这么写
            <button v-on:click="counter++">+</button>
            <button v-on:click="counter--">-</button>
         -->
        <button v-on:click="add">+</button>
        <button v-on:click="sub">-</button>
        <!-- 
            v-on:click可用@click代替
            @click是一个语法糖
         -->
    </div>
    <script src="../js/vue.js"></script>
    <script>
        const app = new Vue({
            el: '#app',
            data: {
                counter: 0
            },
            methods: {
                add: function(){
                    this.counter++
                    console.log('add执行');
                },
                sub: function(){
                    this.counter--
                    console.log('sub执行');
                }
            }
        })
    </script>
</body>
```

## 5、Vue中的MVVM

+ View层

  + 视图层
  + 在前端开发中，通常就是DOM层
  + 只要作用是给用户展示各种信息

+ Model层

  + 数据层

  + 数据可以是写死的数据，更多是来自服务器和网络请求的数据

+ VueModel层
  + 视图模型层
  + 视图模型层是View和Model沟通的桥梁
  + 一方面实现了Data Binding，也就是数据绑定，将Model的改变实时的反应到View中
  + 另一方面它实现了DOM Listener，也就是DOM监听，当DOM发生一些时间点击、滚动、touch等时，可以监听到，并在需要的情况下改变对应的Data

在这里有一个初步理解，例如计数器的counter的值，如果设置一个常量num，然后让data: num，无论this.counter++可以得到num里面的counter值并加一，还是num.counter++直接改变数据的值都是一样的，初步理解为Vue将常量里的值加到new Vue里使它们成为了一个整体

## 6、options包括的选项和Vue的生命周期

```html
el: string|HTMLElement 
决定之后Vue实例会管理哪一个DOM
el: '#app',
// el: document.querySelector(),

data: Object|Function（在组建当中data必须是一个函数）
Vue实例对应的数据对象

methods: {[key:string]:Function}
定义属于Vue的一些方法，可以在其它方法调用，也可以在指令中使用
```

生命周期图例

{ asset_img lifecycle.png 生命周期图示 }

## 7、定义Vue的template

本人使用的是vscode

首先在我们先把tab对应的空格数从4个改为2个，在首选项 ->> 设置里搜索tabsize，将Detect Indentation设置为false，并把tab size改为2

再然后在首选项 ->> 用户代码片段里点击HTML或者HTML.json，写入

```json
  "Vue Template": {
    "prefix": "vue",
    "body": [
      "<!DOCTYPE html>",
      "<html lang=\"zh-CN\">\n",
      "<head>",
      "\t<meta charset=\"UTF-8\">",
      "\t<meta name=\"viewport\" content=\"width=device-width, initial-scale=1.0\">",
      "\t<meta http-equiv=\"X-UA-Compatible\" content=\"ie=edge\">",
      "\t<title>Document</title>",
      "\t<script src=\"../js/vue.js\"></script>",
      "</head>\n",
      "<body>",
      "\t<div id=\"app\">{{message}}</div>\n",
      "\t<script>",
      "\t\tconst app = new Vue({",
      "\t\t\tel: '#app',",
      "\t\t\tdata: {",
      "\t\t\t\tmessage: \"hello\"",
      "\t\t\t},",
      "\t\t\tmethods: {}",
      "\t\t});",
      "\t</script>",
      "</body>\n",
      "</html>"
    ],
    "description": "html5 vue template"
  }
```

然后在HTML文件中写入vue就会自动创建模板