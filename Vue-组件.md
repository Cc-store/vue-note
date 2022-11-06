# 内容回顾

## 一. 之前知识补充

### 1.1. jsconfig文件的作用

> 给VSCode来进行读取, VSCode在读取到其中的内容时, 给我们的代码更加友好的提示



### 1.2. vue不同版本的作用

* runtime: 运行时
  * 默认vue版本: runtime, vue-loader完成template的编译过程
* runtime + compile: 运行+编译
  * vue.esm-bundler: runtime + compile, 对template进行编译

```js
import { createApp } from 'vue' // 不支持template选项
// import { createApp } from 'vue/dist/vue.esm-bundler' // compile代码
import App from './App.vue' // vue-loader: template -> createVNode过程
元素 -> createVNode: vue中的源码来完成
```



### 1.3. css的scoped作用域





### 1.4. npm init vue@latest创建项目

* vite打包





## 二. 组件间的通信

### 2.1. 组件的嵌套关系

* 父组件/子组件

![image-20221013201532912](F:\Vue\image\image-20221013201532912.png)



### 2.2. 父传子 - props(重要)

> **通过props属性**；

![image-20221013201606255](F:\Vue\image\image-20221013201606255.png)

**Props有两种常见的用法：**

* 字符串数组，数组中的字符串就是attribute的名称；
* 对象类型，对象类型我们可以在指定attribute名称的同时，指定它需要传递的类型、是否是必须的、默认值等等；

```js
<script>
  export default {
    // inheritAttrs: false,
    
    // 作用: 接收父组件传递过来的属性
    // 1.props数组语法
    // 弊端: 1> 不能对类型进行验证 2.没有默认值的
    // props: ["name", "age", "height"]

    // 2.props对象语法(必须掌握)
	//type的类型 : String Number Boolean Array Object Date Function Symbol
    props: {
      name: {
        type: String,
        default: "我是默认name"
      },
      age: {
        type: Number,
        required: true,
        default: 0
      },
      height: {
        type: Number,
        default: 2
      },
      // 重要的原则: 对象类型写默认值时, 需要编写default的函数, 函数返回默认值
      friend: {
        type: Object,//指定传入的attribute的类型；
        default() {//指定传入的attribute是否是必传的；
          return { name: "james" }//指定没有传入时，attribute的默认值；
        }
      },
      hobbies: {
        type: Array,
        default: () => ["篮球", "rap", "唱跳"]
      },
      showMessage: {
        type: String,
        default: "我是showMessage"
      }
    }
  }
</script>

```

![image-20221013201754761](F:\Vue\image\image-20221013201754761.png)



![image-20221013203641490](F:\Vue\image\image-20221013203641490.png)



**Prop 的大小写命名**

> 使用 DOM 中的模板时，camelCase (驼峰命名法) 的 prop 名需要使用其等价的 kebab-case (短横线分隔命名)命名； showInfo => <show-info>

### 2.3. 非prop的attribute

> 传递给一个组件某个属性，但是该属性并没有定义对应的props或者emits时，就称之为 非Prop的Attribute；
>
>  常见的包括class、style、id属性等

**Attribute继承**

*  当组件有单个根节点时，非Prop的Attribute将自动添加到根节点的Attribute中：

![image-20221013204106090](F:\Vue\image\image-20221013204106090.png)



**禁用Attribute继承和多根节点**

* 不希望组件的根元素继承attribute，可以在组件中设置 inheritAttrs: false：
* 禁用attribute继承的**常见情况是需要将attribute应用于根元素之外的其他元素**；
*  $attrs来访问所有的 非props的attribute；



**多个根节点的attribute**

*  多个根节点的attribute如果没有显示的绑定，那么会报警告，我们必须手动的指定要绑定到哪一个属性上：

![image-20221013204237053](F:\Vue\image\image-20221013204237053.png)



### 2.4. 子传父 - $emit(重要)

> 1.子组件中定义好在某些情况下触发的事件名称；
>
> 2.父组件中以v-on的方式传入要监听的事件名称，并且绑定到对应的方法中；
>
> 3.在子组件中发生某个事件的时候，根据事件名称触发对应的事件；

```js
//父组件
<button @click="btnClick(1)">-1</button> 
//子组件
<script>
  export default {
    // 1.emits数组语法
    emits: ["add"],
    // 2.emmits对象语法
    // emits: {
    //   add: function(count) {
    //     if (count <= 10) {
    //       return true
    //     }
    //     return false
    //   }
    // },
    methods: {
      btnClick(count) {
        console.log("btnClick:", count)
        // 让子组件发出去一个自定义事件
        // 第一个参数自定义的事件名称
        // 第二个参数是传递的参数
        this.$emit("add", 100)
      }
    }
  }
</script>

```

![image-20221013205218633](F:\Vue\image\image-20221013205218633.png)





### 2.5. 阶段案例练习 - TabControl的封装





## 三. 组件的插槽Slot

### 3.1. 认识Slot的作用

> 可以决定某一块区域到底存放什么内容和元素

### 3.2. Slot的基本使用和默认值(重要)

```html
//子组件
<template>
  <h2>{{ title }}</h2>
  <div class="content">
    <slot>
      <p>我是默认内容, 哈哈哈</p>
    </slot>
  </div>
</template>

//父组件
<template>
  <div class="app">
    <!-- 1.内容是button -->
    <show-message title="哈哈哈">
      <button>我是按钮元素</button>
    </show-message>

    <!-- 2.内容是超链接 -->
    <show-message>
      <a href="#">百度一下</a>
    </show-message>

    <!-- 3.内容是一张图片 -->
    <show-message>
      <img src="@/img/kobe02.png" alt="">
    </show-message>

    <!-- 4.内容没有传递 -->
    <show-message></show-message>
  </div>
</template>
```

![image-20221013213358577](F:\Vue\image\image-20221013213358577.png)

![image-20221013213628543](F:\Vue\image\image-20221013213628543.png)



### 3.3. Slot的具名插槽(重要)

> 给插槽起一个名字，<slot> 元素有一个特殊的 attribute：name；
>
> 一个**不带 name 的slot，会带有隐含的名字 default**；

```vue
<template>
<slot name="why"></slot>
</template>
```

```vue
<nav-bar>
    <template v-slot:why></template>
</nav-bar>
```

![image-20221013213729889](F:\Vue\image\image-20221013213729889.png)



**缩写**

v-slot  =>  ` #`

```html
 <nav-bar>
    <template #left>
      <button>{{ leftText }}</button>
    </template>

    <template #center>
      <span>内容</span>
    </template>

    <template v-slot:right>
      <a href="#">登录</a>
    </template>
  </nav-bar>
 <!-- nav-bar只给一个插槽传入数据 -->
  <nav-bar>
    <template v-slot:[position]>
      <a href="#">注册</a>
    </template>
  </nav-bar>
  <button @click=" position = 'left' ">左边</button>
  <button @click=" position = 'center' ">中间</button>
  <button @click=" position = 'right' ">右边</button>
```



### 3.5. 动态插槽名

>  v-slot:[dynamicSlotName]方式动态绑定一个名称

![image-20221013214006527](F:\Vue\image\image-20221013214006527.png)



### 3.6. Vue编译作用域

* 父级模板里的所有内容都是在**父级作用域中**编译的；
* 子模板里的所有内容都是在子作用域中编译的；

![image-20221014100459659](F:\Vue\image\image-20221014100459659.png)



### 3.7. 作用域插槽使用

* 核心: 将子组件中的数据传递给父组件的插槽来使用

1.在App.vue中定义好数据
2.传递给ShowNames组件中
3.ShowNames组件中遍历names数据
4.定义插槽的prop
5.通过v-slot:default的方式获取到slot的props
6.使用slotProps中的item和index

![image-20221014100706668](F:\Vue\image\image-20221014100706668.png)



**独占默认插槽的缩写**

![image-20221014100843638](F:\Vue\image\image-20221014100843638.png)



**默认插槽和具名插槽混合**

![image-20221014100900544](F:\Vue\image\image-20221014100900544.png)





