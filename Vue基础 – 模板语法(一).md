## 一. 邂逅Vue.js开发

### 1.1. Vue下载和使用

* CDN引入  ：[<script src="https://unpkg.com/vue@next"></script>]()
* 下载引入   ： [<script src="../js/vue.js"></script>]() 自己创建文件复制vue.js
* 通过npm包管理工具安装使用
* 通过Vue CLI创建项目，并且使用



### 1.2. Vue的三个案例

#### 1.2.1. 动态数据展示

```html
<div id="app"> </div>

const app = Vue.createApp({
     // 插值语法: {{title}}
    template : `<h2>{{ message }}</h2>`
    data： function(){
    	return {
            title: "Hello world",
            message: "Hello,Vue3"
        }
	}
})
app.mount("#app")
```



#### 1.2.2. 动态展示列表

* v-for

```html
<div :class="{ active: isActive }" id="app">
    <ul> 列表
      <li v-for=" item in movie">
        {{ item }}</li>
    </ul>
    <button :class="{ active: isActive }" @click="btnClick">我是按钮</button>
  </div>


  <script src="../lib/vue.js"></script>
  <script>
    // 1.创建app
    const app = Vue.createApp({
      // data: option api
      data: function () {
        return {
          classes: "abc cba nba",
          isActive: true,
          className: "why",
          movie: [111, 222, 333, 444]
        }
      },

      methods: {
        btnClick: function () {
          this.isActive = !this.isActive
        },
        getDynamicClasses: function () {
          return { active: this.isActive, why: true, kobe: false }
        }
      }
    })
    app.mount("#app")
  </script>
```



#### 1.2.3. 计数器案例

* counter
* increment
* decrement

```html
<div id="app">
    <h2>当前计数: {{ counter }}</h2>
    <button @click="increment"> +1</button>
    <button @click="decrement"> -1</button>
  </div>
  <script src="./lib/vue.js"></script>
  <script>
    const app = Vue.createApp({
      data(){
        return {
          counter : 0
        }
      },
      methods :{
        increment(){
          this.counter++
        },
        decrement(){
          this.counter--
        }
      }
    })
    app.mount("#app")
```



### 1.3. 命令式和声明式编程的区别

* 原生实现计时器

```html
<h2>当前计数: <span class="counter"></span></h2>
  <button class="add">+1</button>
  <button class="sub">-1</button>

  <script>
    // 1.获取dom
    const h2El = document.querySelector("h2")
    const counterEl = document.querySelector(".counter")
    const addBtnEl = document.querySelector(".add")
    const subBtnEl = document.querySelector(".sub")

    // 2.定义一个变量记录数据
    let counter = 100
    counterEl.textContent = counter

    // 2.监听按钮的点击
    addBtnEl.onclick = function() {
      counter++
      counterEl.textContent = counter
    }
    subBtnEl.onclick = function() {
      counter--
      counterEl.textContent = counter
    }
```



1. **命令式编程**
   * 关注的是 `“how to do”`自己完成整个how的过程；
2. **声明式编程**
   * 关注的是 **“what to do”**，由框架(机器)完成 “how”的过程；

3. 原生的实现过程中
   * 每完成一个操作，需要通过JavaScript编写一条代码，来给浏览器一个指令 (命令式编程)
4. Vue的实现过程
   * 在createApp传入的对象中声明需要的内容，模板template、数据data、方法methods. **声明式编程**



### 1.4. MVC和MVVM的模型区别

1. MVC是Model – View –Controller的简称，是在前期被使用非常框架的架构模式，比如iOS、前端；
2. MVVM是Model-View-ViewModel的简称，是目前非常流行的架构模式；

![image-20221008143923671](F:\Vue\image\image-20221008143923671.png)

### 1.5. options api的data详解

* data必须是一个函数, 函数会返回一个对象
* data返回的对象, 会被**Vue的响应式系统劫持**(放到响应式系统中), 所以data的数据发生改变时, 界面会重新渲染



### 1.6. options api的methods详解

* 对象 -> 很多函数 

  * 可以使用**this关键字**来直接访问到data中返回的对象的属性

* 里面函数不能是箭头函数:
  * this
  
  ```html
  不应该使用箭头函数来定义 method函数(例如 plus:()→this.a++)。
  理由是箭头函数绑定了父级作用域的上下文，
  所以`this` 将不会按照期望指向组件实例，
  `this.a` 将是 undefined
  ```

1. this是必须有值的，并且应该可以通过this获取到data返回对象中的数据。
2. 不可以是window，因为window中我们无法获取到data返回对象中的数据；
   * 使用箭头函数，那么这个this就会是window了；
3. Vue的源码当中就是对methods中的所有函数进行了遍历，并且通过bind绑定了this



## 二. 基础 - 模板语法

### 2.1. mustache语法(插值语法)

* 表达式

```html
 <!-- 1.基本使用 -->
    <h2>{{ message }}</h2>
    <h2>当前计数: {{ counter }} </h2>

    <!-- 2.表达式 -->
    <h2>计数双倍: {{ counter * 2 }}</h2>
    <h2>展示的信息: {{ info.split(" ") }}</h2>

    <!-- 3.三元运算符 -->
    <h2>{{ age >= 18? "成年人": "未成年人" }}</h2>

    <!-- 4.调用methods中函数 -->
    <h2>{{ formatDate(time) }}</h2>

    <!-- 5.注意: 这里不能定义语句 -->
    <!-- <h2>{{ const name = "why" }}</h2> -->

```



### 2.2. 不算常用的指令

1. v-once

   * **v-once用于指定元素或者组件只渲染一次**

     *  当数据发生变化时，**元素或者组件以及其所有的子元素将视为静态内容并且跳过；**
     * 该指令可以用于**性能优化**；

     

   * **如果是子节点，也是只会渲染一次：**

```html
<div id="app">

    <!-- 指令: v-once -->
    <h2 v-once>
      {{ message }}
      <span>数字: {{counter}}</span>
    </h2>

    <h1>{{message}}</h1>

    <button @click="changeMessage">改变message</button>
  </div>
```



2. v-text

   > 用于更新元素的 textContent：

```html
 <div id="app">
    <h2 v-text="message">aaa</h2>
	<!-- 等价于 -->
    <h2> aa {{message}} bbb</h2>
  </div>
```



3. v-html

* 默认情况下，如果我们**展示的内容本身是 html 的，那么vue并不会对其进行特殊的解析。**
  * 希望这个内容**被Vue可以解析出来**，那么可以使用 **v-html** 来展示；

![image-20221008150856389](F:\Vue\image\image-20221008150856389.png)

![image-20221008150904762](F:\Vue\image\image-20221008150904762.png)



4. v - pre

 v-pre用于**跳过元素和它的子元素的编译过程**，显示原始的Mustache标签：

* 跳过不需要编译的节点，加快编译的速度；

![image-20221008151447132](F:\Vue\image\image-20221008151447132.png)



5. v - cloak

**这个指令保持在元素上直到关联组件实例结束编译**

*  和 CSS 规则如 [v-cloak] { display: none } 一起用时，这个指令可以隐藏未编译的 Mustache 标签直到组件实例准备完毕。

![image-20221008151905560](F:\Vue\image\image-20221008151905560.png)

* div 不会显示，直到编译结束，最后会显示出来



### 2.3. 新的指令 v-memo

**v-memo:可以做性能优化，v-memo中值若不发生变化，整个子树的更新会被跳过。**

```html
 <div id="app">
    <div v-memo="[name, age]">//name，age，其中一个改变或者都改变，height才改变
      <h2>姓名: {{ name }}</h2>
      <h2>年龄: {{ age }}</h2>
      <h2>身高: {{ height }}</h2>
    </div>
    <button @click="updateInfo">改变信息</button>
  </div>
  
  <script src="../lib/vue.js"></script>
  <script>
    // 1.创建app
    const app = Vue.createApp({
      // data: option api
      data: function() {
        return {
          name: "why",
          age: 18,
          height: 1.88
        }
      },

      methods: {
        updateInfo: function() {
          this.name = "kobe"
          this.age = 20
          this.height = 100
        }
      }
    })
```



### 2.4. v-bind绑定属性

#### 2.4.1. v-bind绑定基本属性

* src
* href

```html
<!-- 1.绑定img的src属性 -->
    <img v-bind:src="showImgUrl" alt="">
    <!-- 语法糖: v-bind -> : -->
    <img :src="showImgUrl" alt="">

    <!-- 2.绑定a的href属性 -->
    <a :href="href">百度一下</a>
```



#### 2.4.2. v-bind绑定class

* 基本绑定
* 对象语法:
  * { className: Boolean }
* 数组语法: 把一个数组传给 :class，以应用一个 class 列表；

```html
 <!-- 1.基本绑定class -->
    <h2 :class="classes">Hello World</h2>

    <!-- 2.动态class可以写对象语法 -->
    <button :class=" isActive ? 'active': '' " @click="btnClick">我是按钮</button>

    <!-- 2.1.对象语法的基本使用(掌握) -->
    <button :class="{ active: isActive }" @click="btnClick">我是按钮</button>

    <!-- 2.2.对象语法的多个键值对 -->
    <button :class="{ active: isActive, why: true, kobe: false }" @click="btnClick">我是按钮</button>
    
    <!-- 2.3.动态绑定的class是可以和普通的class同时的使用 -->
    <button class="abc cba" :class="{ active: isActive, why: true, kobe: false }" @click="btnClick">我是按钮</button>
    
    <!-- 2.4.动态绑定的class是可以和普通的class同时的使用 -->
    <button class="abc cba" :class="getDynamicClasses()" @click="btnClick">我是按钮</button>

    <!-- 3.动态class可以写数组语法(了解) -->
    <h2 :class="['abc', 'cba']">Hello Array</h2>
    <h2 :class="['abc', className]">Hello Array</h2>
    <h2 :class="['abc', className, isActive? 'active': '']">Hello Array</h2>
    <h2 :class="['abc', className, { active: isActive }]">Hello Array</h2>
```



#### 2.4.3. v-bind绑定style

* 对象语法:
  * { cssname: cssvalue }
* 数组语法:
  * [obj1, obj2]
*  CSS property 名可以用驼峰式 (camelCase) 或短横线分隔 (kebab-case，记得用引号括起来) 来命名；

```html
<div id="app">
    <!-- 1.普通的html写法 -->
    <h2 style="color: red; font-size: 30px;">哈哈哈哈</h2>

    <!-- 2.style中的某些值, 来自data中 -->
    <!-- 2.1.动态绑定style, 在后面跟上 对象类型 (重要)-->
    <h2 v-bind:style="{ color: fontColor, fontSize: fontSize + 'px' }">哈哈哈哈</h2>
    <!-- 2.2.动态的绑定属性, 这个属性是一个对象 -->
    <h2 :style="objStyle">呵呵呵呵</h2>

    <!-- 3.style的数组语法 -->
    <h2 :style="[objStyle, { backgroundColor: 'purple' }]">嘿嘿嘿嘿</h2>
  </div>
  
  <script src="../lib/vue.js"></script>
  <script>
    // 1.创建app
    const app = Vue.createApp({
      // data: option api
      data: function() {
        return {
          fontColor: "blue",
          fontSize: 30,
          objStyle: {
            fontSize: '50px',
            color: "green"
          }
        }
      },
    })
   // 2.挂载app
    app.mount("#app")

  </script>
```



### 2.5. 动态绑定属性名

> 无论绑定src、href、class、style，属性名称都是固定的, 属性名称不是固定的，我们可以使用 :[属性名]=“值” 的格式来定义

```html
:[name]=""

<div id="app">
    <h2 :[name]="'aaaa'">Hello World</h2>
  </div>
  
  <script src="../lib/vue.js"></script>
  <script>
    // 1.创建app
    const app = Vue.createApp({
      // data: option api
      data: function() {
        return {
          name: "class"
        }
      },
    })
```



### 2.6. v-bind绑定对象

* 将对象中所有key/value, 作为属性绑定到元素(组件)上

```html
<div id="app">
    <h2 :name="name" :age="age" :height="height">Hello World</h2>

    <!-- v-bind绑定对象: 给组件传递参数 -->
    <h2 v-bind="infos">Hello Bind</h2>
  </div>
  
  <script src="../lib/vue.js"></script>
  <script>
    // 1.创建app
    const app = Vue.createApp({
      // data: option api
      data: function() {
        return {
          infos: { name: "why", age: 18, height: 1.88, address: "广州市" },

          name: "why",
          age: 18,
          height: 1.88
        }
      },
    })
    // 2.挂载app
    app.mount("#app")
  </script>
```

