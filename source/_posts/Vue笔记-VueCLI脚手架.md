---
title: Vue笔记--VueCLI脚手架
date: 2020-02-17 16:55:57
tags:
- Vue
- VueCLI
categories:
- Vue
- Vuejs
permalinkL: vue-vuecli
---

# 九、脚手架的使用

## 1.VueCLI2的使用

**VueCLI2的文件解释**

build文件夹和config都是配置相关

.babelrc就是将ES6转为ES5，适配更多浏览器的配置文件

.editorconfig对文本进行统一

.eslintignore对配置的文件进行格式忽略

<!-- more -->

.gitignore对配置的文件进行git上传忽略

.eslintrc代码检测的配置

.postcssrc对css转化的配置

package.json管理node包，指定一个大概的版本

package-lock.json真实的版本



eslint虽然可以规范代码，但是有些规范和我们的习惯有些不同，如果想关掉它，可以在config里将useESlint改为false

Vue程序运行过程

先将template转换为抽象语法树（abstract syntax tree）缩写为ast，然后编译为一个render函数，再将render函数转换为一个虚拟dom，最后转换为一个真实dom

**runtime-compiler**

template -> ast -> render -> vdom -> UI

**runtime-only**

render -> vdom -> UI

```javascript
// App.vue本身就是组件
new Vue({
  el: "#app",
  template: "<App/>",
  components: {
    App
  }
})

new Vue({
  el: "#app",
  render: h => h(App)
})

// 第一种类型也可以写为
// createElement实际上就是h这个函数
// createElement("标签", {标签的属性}, [数组的内容])
new Vue({
  el: "#app", 
  render: function (createElement) {
    // 普通用法
    // render会把#app的内容替换为h2标签
    return createElement("h2", 
      {class: "box"}, 
      ["Hello World"])
    // 这样h2里会有一个按钮
    return createElement("h2", 
      {class: "box"}, 
      ["Hello World", createElement("button", ["按钮"])])
    //也可以传入一个组件，例如下面这个cpn组件
    return createElement(cpn)
  }
})

const cpn = {
  template: "<div>{{message}}</div>",
  data() {
    return {
      
    }
  }
}
```

如果在之后的开发中，你仍然用template，就需要选择Runtime-Compiler

如果在之后的开发中，使用的是.vue文件开发，那么可以选择Runtime-only

## 2、VueCLI3的使用

1. Please pick  a preset: (Use arrow keys)
   + default (babel, eslint)
   + Manually select features (选项按空格选中和取消)
2. Check the features needed for you project
   + Babel
   + TypeScript
   + Progresssive Web App (PWA) Support
   + Router (路由)
   + Vuex
   + CSS Pre-processors (CSS 预处理器)
   + Linter / Formatter (ESLint)
   + Unit Testing (单元测试)
   + E2E Testing (端到端测试)
3. Where do you prefer placing config for Babel, ESLint, etc.?
   + In dedicated config files (单独的配置文件)
   + In package.json (写到package.json里)
4. Save this as a preset for future project ? (保存一个自己的配置)
5. Save preset as: (保存配置的名字)

## 3、VueCLI3配置

可以使用vue ui来设置

也可以创建一个vue.config.js