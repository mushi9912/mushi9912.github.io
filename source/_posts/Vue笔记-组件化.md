---
title: Vue笔记--组件化
date: 2020-02-16 14:41:46
tags:
- Vue
- 入门
categories:
- Vue
- Vuejs
permalink: vue-component
---

# 八、Vue组件化

## 1、组件创建的步骤

1. 调用Vue.extend()方法创建组件构造器
2. 调用Vue.component()方法注册组件
3. 在Vue实例的作用范围内使用组件

<!-- more -->

## 2、Vue组件的基本使用

```html
<body>
  <div id="app">
    <ms-cpn></ms-cpn>
    <ms-cpn></ms-cpn>
    <ms-cpn></ms-cpn>
    <ms-cpn></ms-cpn>
  </div>

  <script>
    const cpnC = Vue.extend({
      template: `
        <h2>
          测试
        </h2>
      `
    });

    Vue.component("ms-cpn", cpnC);

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

## 3、全局组件和局部组件

```html
<body>
  <div id="app">
    <ms-cpn></ms-cpn>
    <ms-cpn></ms-cpn>
    <ms-cpn></ms-cpn>
    <ms-cpn></ms-cpn>
  </div>

  <script>
    const cpnC = Vue.extend({
      template: `
        <h2>
          测试
        </h2>
      `
    });

    // Vue.component("ms-cpn", cpnC);  //全局组件，可以在多个Vue实例下启用

    const app = new Vue({
      el: '#app',
      data: {
        message: "hello"
      },
      components: {
        msCpn: cpnC  //局部组件
      },
      methods: {
        
      }
    });
  </script>
</body>
```

## 4、父组件与子组件

```html
<body>
  <div id="app">
    <ms-cpn2></ms-cpn2>
  </div>

  <script>
    // cpnC1就是子组件
    const cpnC1 = Vue.extend({
      template: `
        <h2>
          测试
        </h2>
      `
    });

    // cpnC2就是父组件
    const cpnC2 = Vue.extend({
      // 注意子组件要用有一个根元素包括他
      template: `
        <div>
          <ms-cpn1></ms-cpn1>
          <h2>
            测试2
          </h2>
        </div>
      `,
      components: {
        // 在组件里注册后，就可以在组件里使用
        msCpn1: cpnC1 
      }
    });

    // Vue.component("ms-cpn", cpnC);  //全局组件，可以在多个Vue实例下启用

    // 也可以把app看成一个组件，root组件
    const app = new Vue({
      el: '#app',
      data: {
        message: "hello"
      },
      components: {
        msCpn2: cpnC2  //局部组件，只注册了cpnC2，是不能使用cpnC1的，即使是父子关系
      },
      methods: {
        
      }
    });
  </script>
</body>
```

## 5、组件语法糖

```html
<body>
  <div id="app">
    <ms-cpn2></ms-cpn2>
  </div>

  <script>
    // 全局组件的语法糖
    Vue.component("msCpn1",{
      template: `
        <h2>
          测试
        </h2>
      `
    })

    // 局部组件的语法糖
    const app = new Vue({
      el: '#app',
      data: {
        message: "hello"
      },
      components: {
        msCpn2: {
        // 注意子组件要用有一个根元素包括他
          template: `
            <div>
              <ms-cpn1></ms-cpn1>
              <h2>
                测试2
              </h2>
            </div>
          `,
          components: {
            // 在组件里注册后，就可以在组件里使用
            msCpn1: {
              template: `
                <h2>
                  测试
                </h2>
              `
            }
          }
        }
      },
      methods: {
        
      }
    });
  </script>
</body>
```

## 6、模板分离与组件访问data

```html
<body>
  <div id="app">
    <ms-cpn1></ms-cpn1>
  </div>


  <!-- 第一种写法 -->
  <script type="text/x-template" id="cpn1">
    <h2>
      测试：{{message}}
    </h2>
  </script>


  <!-- 第二种写法 -->
  <template id="cpn2">
    <h2>
      测试
    </h2>
  </template>


  <script>
    // 全局组件的语法糖
    Vue.component("msCpn1",{
      template: "#cpn1",
      // 组件里的data必须是一个函数，为什么必须是一个函数
      /*
        组件是一个模板，每次使用这个组件，都会按着模板复制一个实例，而所有实例用的data都是模板中的，共享的，
        这里把data项改成函数后每次复制都会调用函数为每个实例生成一份单独的data
      */
      data() {
        return {
          message: "hehehe"
        }
      }
    })

    // 局部组件的语法糖
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

## 7、父子组件通信

```html
<body>
  <div id="app">
    <!-- 这里@render-test不能用驼峰 -->
    <!-- ="renderTest"不写renderTest会默认把this.$emit传入的值传进去 -->
    <ms-cpn1 @render-test="renderTest"></ms-cpn1>
  </div>


  <!-- 第一种写法 -->
  <script type="text/x-template" id="cpn1">
    <div>
      <h2>
        测试cpn1：{{message}}
      </h2>
      <button v-for="category in categories" @click="btnClick(category)">{{category}}</button>
      <ms-cpn2 :messageInfo="messageInfo"></ms-cpn2>
    </div>
  </script>


  <!-- 第二种写法 -->
  <template id="cpn2">
    <h2>
      测试cpn2：{{messageInfo}}
      {{games}}
    </h2>
  </template>


  <script>
    const msCpn2 = {
      template: "#cpn2",
      // 数组写法
      // props: ["message"]
      // 对象写法
      props: {
        // 还可以限制类型
        // message: String,
        // 多种类型
        // message: [String, Number],
        // 还可以有默认值
        messageInfo: {
          type: String,
          default: "default",
          // 必须传值，不传报错
          required: true
        },
        games: {
          type: Array,
          // 类型是对象和数组时，默认值必须是一个函数
          default() {
            return []
          },
          // 自定义验证
          validator(value) {
            return true
          }
        }
      }
    };

    const msCpn1 = {
      template: "#cpn1",
      data() {
        return {
          messageInfo: "hehehe",
          categories: ["Vue", "Spring Boot", "react"]
        }
      },
      methods: {
        btnClick(value) {
          console.log(value);
          // 发射事件
          // 这里不能用驼峰
          this.$emit("render-test", value);
        }
      },
      props: {
        message: {
          type: String,
          default: "neeee"
        }
      },
      components: {
        msCpn2
      }
    };

    const app = new Vue({
      el: '#app',
      data: {
        message: "hello"
      },
      methods: {
        renderTest(value) {
          console.log(value+" renderTest被点击了")
        }
      },
      components: {
        msCpn1
      },
    });
  </script>
</body>
```

## 8、父子通信结合双向绑定

```html
<body>
  <template id="cpn">
    <div>
      <h2>{{number1}}</h2>
      <h2>{{number2}}</h2>
      <h2>{{snum1}}</h2>
      <h2>{{snum2}}</h2>
      <!-- <input type="text" v-model="snum1"> -->
      <input type="text" :value="snum1" @input="renderNum" id="snum1">
      <!-- <input type="text" v-model="snum2"> -->
      <input type="text" :value="snum2" @input="renderNum" id="snum2">
    </div>
  </template>

  <div id="app">
    <h2>{{message}}</h2>
    <cpn :number1="num1" :number2="num2" @render="getChange"></cpn>
  </div>

  <script>
    const app = new Vue({
      el: '#app',
      data: {
        message: "hello",
        num1: 1,
        num2: 0,
      },
      components: {
        cpn: {
          template: "#cpn",
          props: {
            number1: Number,
            number2: Number,
          },
          data() {
            return {
              snum1: this.number1,
              snum2: this.number2,
            }
          },
          methods: {
            renderNum(event) {
              console.log("renderNum", event.target.id, event.target.value)
              if (event.target.id == "snum1"){
                this.snum1 = event.target.value
                this.$emit("render", "snum1", this.snum1)
                this.snum2 = event.target.value * 100
                this.$emit("render", "snum2", this.snum2)
              }else{
                this.snum2 = event.target.value
                this.$emit("render", "snum2", this.snum2)
                this.snum1 = event.target.value / 100
                this.$emit("render", "snum1", this.snum1)
              }
            }
          }
        }
      },
      methods: {
        getChange(who, value) {
          console.log("getchange", who, value)
          if (who == "snum1")
            this.num1 = Number(value)
          else
            this.num2 = Number(value)
        }
      }
    });
  </script>
</body>
```

## 9、父子组件的访问

```html
<body>
  <div id="app">
    <h2>{{message}}</h2>
    <cpn></cpn>
    <cpn></cpn>
    <cpn ref="ceshi"></cpn>
    <button @click="showMessage">点击</button>
  </div>

  <template id="cpn">
    <div>
      <h2>cpn测试</h2>
    </div>
  </template>
  <script>
    const app = new Vue({
      el: '#app',
      data: {
        message: "hello"
      },
      methods: {
        showMessage() {
          console.log("app showMessage")
          // 1、$children的使用
          console.log(this.$children)
          console.log(this.$children[0].name)
          this.$children[0].showMessage()
          // 2、$refs是使用
          // 没给<cpn></cpn>加之前是空的
          console.log(this.$refs)
          console.log(this.$refs.ceshi)
        }
      },
      components: {
        cpn: {
          data() {
            return {
              name: "cpn name"
            }
          },
          template: "#cpn",
          methods: {
            showMessage() {
              console.log("cpn showMessage")
            }
          }
        }
      }
    });
  </script>
</body>
```

## 10、Vue插槽

```html
<body>
  <div id="app">
    <h2>{{message}}</h2>
    <button @click="getName">aaaa</button>

    <cpn></cpn>
    <cpn ref="ceshi"><a href="https://www.baidu.com">百度一下</a></cpn>
    <cpn ref="aaa">
      <!-- 使用子组件的插槽 -->
      <template v-slot:ceshi>
        <h2>插槽的测试</h2>
      </template>
      <!-- 两个hhhhhhh会一起输出 -->
      <p>hhhhhhhhhhhhhhhhh</p>
      <template v-slot:ceshi2="ma">
        <h2>{{ma.data}}</h2>
      </template>
      <p>hhhhhhhhhhhhhhhhh</p>
    </cpn>
  </div>

  <template id="cpn">
    <div>
      <h2>cpn测试</h2>
      <!-- 设置子组件的插槽 -->
      <!-- 这里的按钮会变为默认值 -->
      <slot name="ceshi"><h2>插槽的默认</h2></slot>
      <slot></slot>
      <slot name="ceshi2" data="data数据"></slot>
    </div>
  </template>
  <template id="cpn1">
    <div>
      <h2>cpn1测试</h2>
    </div>
  </template>

  <script>
    const app = new Vue({
      el: '#app',
      data: {
        message: "hello"
      },
      methods: {

      },
      components: {
        cpn: {
          data() {
            return {
              name: "cpn name"
            }
          },
          template: "#cpn",
          methods: {

          },
          components: {
            cpn1: {
              template: "#cpn1",
              methods: {

              }
            }
          }
        }
      }
    });
  </script>
</body>
```

## 11、Vue编译作用域

```html
<body>
  <div id="app">
    <h2>{{message}}</h2>
    <cpn v-show="isShow"></cpn>
  </div>

  <template id="cpn">
    <div>
      <h2>cpn组件</h2>
      <button v-show="isShow">按钮</button>
    </div>
  </template>

  <script>
    const app = new Vue({
      el: '#app',
      data: {
        message: "hello",
        isShow: true,
      },
      methods: {
        
      },
      components: {
        cpn: {
          template: "#cpn",
          data() {
          return {
            isShow: false,
          }
        }
        },
      }
    });
  </script>
</body>
```



