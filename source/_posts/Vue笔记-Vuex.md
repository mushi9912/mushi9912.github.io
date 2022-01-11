---
title: Vue笔记--Vuex
date: 2020-02-28 19:38:15
tags:
- Vue
categories:
- Vue
- Vuejs
permalink: vue-vuex
---

# 十一、Vue与Vuex

## 1、Vuex的属性和使用

Vuex 是一个专为 Vue.js 应用程序开发的**状态管理模式**。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。Vuex 也集成到 Vue 的官方调试工具 [devtools extension](https://github.com/vuejs/vue-devtools)[ ](https://github.com/vuejs/vue-devtools)，提供了诸如零配置的 time-travel 调试、状态快照导入导出等高级调试功能。

<!-- more -->

```javascript
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

export default new Vuex.Store({
  // 可以理解为全局变量，
  state: {
  },
  //修改state最好不要自己修改，要通过mutations修改，必须是同步方法
  mutations: {
  },
  // 类似computed，还可以有第二个参数getters，重复使用mutations里的方法，如果有自定义的参数就返回一个函数
  getters: {
      
  },
  actions: {
  },
  modules: {
  }
})

```

修改实例（mutations）

```javascript
export default new Vuex.Store({
  state: {
    counter: 0
  },
  mutations: {
    // 定义方法
    increment(state) {
      state.counter++
    },
    decrement(state) {
      state.counter--
    },
    incrementNum(state, n) {
      state.counter += n.Number
    }
  },
  getters: {
  },
  actions: {
  },
  modules: {
  }
})
```

```html
<div id="app">
  <h2>{{message}}</h2>
  <h2>{{$store.state.counter}}</h2>
  <button @click="addition">+</button>
  <button @click="subtraction">-</button>
  <button @click="addNumber">+10</button>
</div>
<script>
  export default {
    name: "App",
    data() {
      return {
        message: "Vuex test"
      }
    },
    methods: {
      addition() {
        this.$store.commit("increment")
      },
      subtraction() {
        this.$store.commit("decrement")
      },
      addNumber() {
        const n = {Number: 10}
        this.$store.commit("incrementNum", n)
        // 第二种写法，注意传入的参数不同
        // this.$store.commit({
        //   type: "incrementNum",
        //   n
        // })
      }
    }
  }
</script>
```

计算实例（getters）

```javascript
export default new Vuex.Store({
  state: {
    counter: 0
  },
  mutations: {
    // 定义方法
    increment(state) {
      state.counter++
    },
    decrement(state) {
      state.counter--
    }
  },
  getters: {
    powerStates(state) {
      return state.counter * state.counter
    },
    addNumone(state, getters) {
      return getters.powerStates + 1
    },
    addNum(state, getters) {
      return function (n) {
        return getters.powerStates + n
      }
    }
  },
  actions: {
  },
  modules: {
  }
})
```

```html
<div id="app">
  <h2>{{message}}</h2>
  <h2>{{$store.state.counter}}</h2>
  <h3>{{$store.getters.powerStates}}</h3>
  <h3>{{$store.getters.addNumone}}</h3>
  <h3>{{$store.getters.addNum(10)}}</h3>
</div>
```

异步实例

```javascript
export default new Vuex.Store({
  state: {
    counter: 0,
    info: {
      name: "async"
    }
  },
  mutations: {
    asyncTest(state) {
      // state.info["gender"] = "男"
      Vue.set(state.info, "gender", "男")
    }
  },
  getters: {
  },
  actions: {
    asyncTest(context) {
      setTimeout(function () {
        context.commit("asyncTest")
      }, 2000)
    }
  },
  modules: {
  }
})
```

```html
<div id="app">
  <h2>{{message}}</h2>
  {{$store.state.info}}
  <button @click="asyncTest">测试</button>
</div>

<script>
  export default {
    name: "App",
    data() {
      return {
        message: "Vuex test"
      }
    },
    methods: {
      asyncTest() {
        this.$store.dispatch("asyncTest")
      }

    }
  }
</script>
```

模块实例

```js
const moduleA = {
  state: { ... },
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}

const moduleB = {
  state: { ... },
  mutations: { ... },
  actions: { ... }
}

const store = new Vuex.Store({
  modules: {
    a: moduleA,
    b: moduleB
  }
})

store.state.a // -> moduleA 的状态
store.state.b // -> moduleB 的状态
```

对于模块内部的 mutation 和 getter，接收的第一个参数是**模块的局部状态对象**。

```javascript
const moduleA = {
  state: { count: 0 },
  mutations: {
    increment (state) {
      // 这里的 `state` 对象是模块的局部状态
      state.count++
    }
  },

  getters: {
    doubleCount (state) {
      return state.count * 2
    }
  }
}
```

同样，对于模块内部的 action，局部状态通过 `context.state` 暴露出来，根节点状态则为 `context.rootState`：

```js
const moduleA = {
  // ...
  actions: {
    incrementIfOddOnRootSum ({ state, commit, rootState }) {
      if ((state.count + rootState.count) % 2 === 1) {
        commit('increment')
      }
    }
  }
}
```

对于模块内部的 getter，根节点状态会作为第三个参数暴露出来：

```js
const moduleA = {
  // ...
  getters: {
    sumWithRootCount (state, getters, rootState) {
      return state.count + rootState.count
    }
  }
}
```