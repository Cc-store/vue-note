

## 一. 模板语法

### 1.1. 事件绑定 v-on用法

#### 1.1.1. v-on各种写法

![image-20221008162340680](F:\Vue\image\image-20221008162340680.png)

```html
<!-- 1.基本的写法 -->
    <div class="box" v-on:click="divClick"></div>

    <!-- 2.语法糖写法(重点掌握) -->
    <div class="box" @click="divClick"></div>

    <!-- 3.绑定的方法位置, 也可以写成一个表达式(不常用, 不推荐) -->
    <h2>{{ counter }}</h2>
    <button @click="increment">+1</button>
    <button @click="counter++">+1</button>

    <!-- 4.绑定其他方法(掌握) -->
    <div class="box" @mousemove="divMousemove"></div>

    <!-- 5.元素绑定多个事件(掌握) -->
    <div class="box" @click="divClick" @mousemove="divMousemove"></div>
    <!-- <div class="box" v-on="{ click: divClick, mousemove: divMousemove }"></div> -->
    <!-- <div class="box" @="{ click: divClick, mousemove: divMousemove }"></div> -->
```



#### 1.1.2. 各种参数方式

* 默认传递 event
* 自定义参数:
  * name, age, $event

```html
 <!-- 1.默认传递event对象 -->
    <button @click="btn1Click">按钮1</button>

    <!-- 2.只有自己的参数 -->
    <button @click="btn2Click('why', age)">按钮2</button>

    <!-- 3.自己的参数和event对象 -->
    <!-- 在模板中想要明确的获取event对象: $event -->
    <button @click="btn3Click('why', age, $event)">按钮3</button>
  </div>
  
  <script src="../lib/vue.js"></script>
  <script>
    // 1.创建app
    const app = Vue.createApp({
      // data: option api
      data: function() {
        return {
          message: "Hello Vue",
          age: 18
        }
      },
      methods: {
        // 1.默认参数: event对象
        // 总结: 如果在绑定事件的时候, 没有传递任何的参数, 那么event对象会被默认传递进来
        btn1Click(event) {
          console.log("btn1Click:", event)
        },

        // 2.明确参数:
        btn2Click(name, age) {
          console.log("btn2Click:", name, age)
        },

        // 3.明确参数+event对象
        btn3Click(name, age, event) {
          console.log("btn3Click:", name, age, event)
        }
      }
    })

    // 2.挂载app
    app.mount("#app")
  </script>
```



#### 1.1.3. 修饰符stop

![image-20221008164114202](F:\Vue\image\image-20221008164114202.png)

```html
 <div id="app">
    <div class="box" @click="divClick">
      <button @click.stop="btnClick">按钮</button>
    </div>
  </div>
```



### 1.2. 条件渲染

#### 1.2.1. v-if/else/else-if

* v-if的渲染原理：
  * v-if是惰性的；
  * 当条件为false时，其判断的内容完全不会被渲染或者会被销毁掉；
  * 当条件为true时，才会真正渲染条件块中的内容；

```html

  <!-- 模板语法 -->
  <div id="app">
    <ul v-if="names.length > 0"> // names: []
      <li v-for="item in names">{{item}}</li>
    </ul>

    <h2 v-else>当前names没有数据, 请求获取数据后展示</h2>
  </div>
```



#### 1.2.2. template元素

>  template元素可以当做不可见的包裹元素，并且在v-if上使用，但是最终template不会被**渲染**出来：

* v-if
* v-for

```html
 <template v-if="Object.keys(info).length">
      <h2>个人信息</h2>
      <ul>
        <li>姓名: {{info.name}}</li>
        <li>年龄: {{info.age}}</li>
      </ul>
    </template>
```



#### 1.2.3. v-show

* if用法区别:
  * v-show不能和template结合
  * v-show不可以和v-else一起使用

* if的本质区别:
  * **v-if为false 元素会销毁/不存在 ( 不会被渲染到DOM中；)**
  * **v-show为false** 元素的display none  ( **style="display: none;"**)
  * v-show元素无论是否需要显示到浏览器上，它的**DOM实际都是有存在**的，只是通过CSS的display属性来进行切换；
* 选择:
  * 切换非常频繁使用v-show
  * 不频繁 v-if

```html
  <div id="app">
    <div>
      <button @click="toggle">切换</button>
    </div>
    
    <div v-show="isShowCode">
      <img src="https://game.gtimg.cn/images/yxzj/web201706/images/comm/floatwindow/wzry_qrcode.jpg" alt="">
    </div>

    <div v-if="isShowCode"> // isShowCode: true
      <img src="https://game.gtimg.cn/images/yxzj/web201706/images/comm/floatwindow/wzry_qrcode.jpg" alt="">
    </div>
  </div>
```



## 二. 列表渲染

### 1.1. v-for的基本使用

* item in 数组
* (item, index) in 数组
* (item, index) of 数组

```html
 <!-- 1.电影列表进行渲染 -->
    <h2>电影列表</h2>
    <ul>
      <li v-for="movie in movies">{{ movie }}</li>
    </ul>

    <!-- 2.电影列表同时有索引 -->
    <ul>
      <li v-for="(movie, index) in movies">{{index + 1}} - {{ movie }}</li>
    </ul>

```



### 1.2. v-for其他的类型

* 对象
  * (value, key, index) in obj
* 数字
  * item in 10
* 可迭代对象(字符串)

```html
 <div id="app">
    <!-- 1.遍历数组 -->

    <!-- 2.遍历对象 -->
    <ul>
      <li v-for="(value, key, index) in info">{{value}}-{{key}}-{{index}}</li>
    </ul>

    <!-- 3.遍历字符串(iterable) -->
    <ul>
      <li v-for="item in message">{{item}}</li>
    </ul>

    <!-- 4.遍历数字 -->
    <ul>
      <li v-for="item in 100">{{item}}</li>//迭代1 到 100
    </ul>
  </div>
  
```



### template元素

可以使用 template 元素来循环渲染一段包含多个元素的内容，而不是div

```html
 <!-- 如果div没有实际的意义, 那么可以使用template替换 -->
    <div v-for="(value, key, index) in infos">
      <span>{{value}}</span>
      <strong>{{key}}</strong>
      <i>{{index}}</i>
    </div>
```



### 数组更新检测

*  Vue 将被侦听的数组的变更方法进行了包裹，所以它们也将会触发视图更新。

* 方法

  ![image-20221008172108280](F:\Vue\image\image-20221008172108280.png)

* 替换数组的方法
  * 某些方法不会替换原来的数组，而是会生成新的数组，比如 filter()、concat() 和 slice()；

```js
 methods: {
        changeArray() {
          // 1.直接将数组修改为一个新的数组
          // this.names = ["why", "kobe"]

          // 2.通过一些数组的方法, 修改数组中的元素
          // this.names.push("why")
          // this.names.pop()
          // this.names.splice(2, 1, "why")
          // this.names.sort()
          // this.names.reverse()

          // 3.不修改原数组的方法是不能侦听(watch)
          const newNames = this.names.map(item => item + "why")
          this.names = newNames
        }
      }
```



### 1.3. v-for绑定key属性

> 1.  key属性主要用在**Vue的虚拟DOM算法**，在**新旧nodes**对比时辨识VNodes；
> 2. 如果**不使用key**，Vue会使用一种最大限度减少动态元素并且尽可能的尝试就地**修改/复用相同类型元素**的算法；
> 3. 而**使用key**时，它会基于key的变化重新排列元素顺序，并且**会移除/销毁key不存在的元素**

#### 1.3.1. VNode/虚拟DOM

* template元素 -> VNode : 本质是一个J**avaScript的对象**
* 虚拟DOM作用之一:
  * 跨平台

![image-20221008194820458](F:\Vue\image\image-20221008194820458.png)



**虚拟DOM**

![image-20221008194915703](F:\Vue\image\image-20221008194915703.png)

![image-20221008194940818](F:\Vue\image\image-20221008194940818.png)



#### 1.3.2. key的作用

* 有key的操作:
  * 根据key找到之前的VNode进行复用;
  * 没有VNode可以复用, 再创建新的VNode
* 没有key的操作:
  * diff算法, 后续VNode复用性就不强

![image-20221008195118824](F:\Vue\image\image-20221008195118824.png)



*  **没有key的过程如下**
  *  c和d来说它们事实上并不需要有任何的改动；
  * 但是因为我们的c被f所使用了，所有后续所有的内容都要一次进行改动，并且最后进行新增；

![image-20221008195204609](F:\Vue\image\image-20221008195204609.png)



* 有key的diff算法如下

  ![image-20221008195250422](F:\Vue\image\image-20221008195250422.png)



![image-20221008195308512](F:\Vue\image\image-20221008195308512.png)



![image-20221008195319021](F:\Vue\image\image-20221008195319021.png)



#### 1.3.3. key绑定id

```html
 <ul>
      <!-- key要求是唯一: id -->
      <li v-for="item in letters" :key="item">{{item}}</li>
    </ul>
```



## 三. Options API

### 3.1. 计算属性 computed

#### 3.1.1. 复杂数据的处理方式

* mustache插值语法自己写逻辑

  ```html
   <div id="app">
      <!-- 插值语法表达式直接进行拼接 -->
      <!-- 1.拼接名字 -->
      <h2>{{ firstName + " " + lastName }}</h2>
      <h2>{{ firstName + " " + lastName }}</h2>
      <h2>{{ firstName + " " + lastName }}</h2>
  
      <!-- 2.显示分数等级 -->
      <h2>{{ score >= 60 ? '及格': '不及格' }}</h2>
  
      <!-- 3.反转单词显示文本 -->
      <h2>{{ message.split(" ").reverse().join(" ") }}</h2>
    </div>
  ```

  

* methods完成逻辑

```html
 <!-- 插值语法表达式直接进行拼接 -->
    <!-- 1.拼接名字 -->
    <h2>{{ fullname }}</h2>
    <h2>{{ fullname }}</h2>
    <h2>{{ fullname }}</h2>

    <!-- 2.显示分数等级 -->
    <h2>{{ scoreLevel }}</h2>

    <!-- 3.反转单词显示文本 -->
    <h2>{{ reverseMessage }}</h2>
    
   <script>
      computed: {
        // 1.计算属性默认对应的是一个函数
        fullname() {
          return this.firstName + " " + this.lastName
        },

        scoreLevel() {
          return this.score >= 60 ? "及格": "不及格"
        },

        reverseMessage() {
          return this.message.split(" ").reverse().join(" ")
        }
      }
    })
  </script>
```



#### 3.1.2. 计算属性用法

* **computed: { fullname() {} }**



#### 3.1.3. computed和methods区别

* computed底层会缓存, 性能更高

![image-20221008200728197](F:\Vue\image\image-20221008200728197.png)



这是因为计算属性会基于它们的**依赖关系进行缓存**；

* 在**数据不发生变化**时，计算属性是不需要重新计算的；
* 但是如果**依赖的数据发生变化**，在使用时，计算属性依然会**重新进行计算**；

![image-20221008201001850](F:\Vue\image\image-20221008201001850.png)

#### 3.1.4. computed的完整写法

* set
* get

```html

  <div id="app">
    <h2>{{ fullname }}</h2>

    <button @click="setFullname">设置fullname</button>
  </div>
  
  <script src="../lib/vue.js"></script>
  <script>
      computed: {
        // 语法糖的写法
        // fullname() {
        //   return this.firstname + " " + this.lastname
        // },
        
        // 完整的写法:
        fullname: {
          get: function() {
            return this.firstname + " " + this.lastname
          },
          set: function(value) {
            const names = value.split(" ")
            this.firstname = names[0]
            this.lastname = names[1]
          }
        }
      }

  </script>
```



![image-20221008201123164](F:\Vue\image\image-20221008201123164.png)



### 3.2. 侦听器 watch

#### 3.2.1. 基本侦听watch

* watch: { message(newValue, oldValue) {} }
* 注意: 对象类型
  * Proxy对象 -> Vue.toRaw(newValue)

```html
 <div id="app">
    <h2>{{message}}</h2>
    <button @click="changeMessage">修改message</button>
  </div>
<script>
     watch: {
        // 1.默认有两个参数: newValue/oldValue
        message(newValue, oldValue) {
          console.log("message数据发生了变化:", newValue, oldValue)
        },
        info(newValue, oldValue) {
          // 2.如果是对象类型, 那么拿到的是代理对象
          // console.log("info数据发生了变化:", newValue, oldValue)
          // console.log(newValue.name, oldValue.name)

          // 3.获取原生对象
          // console.log({ ...newValue })
          console.log(Vue.toRaw(newValue))
        }
      }
</script>
```



#### 3.2.2. 侦听的选项

* deep
* immediate : 一开始的就会立即执行一次, 无论后面数据是否有变化，侦听的函数都会有限执行一次；

```html
 <div id="app">
    <h2>{{ info.name }}</h2>
    <button @click="changeInfo">修改info</button>
  </div>
  
  <script src="../lib/vue.js"></script>
  <script>
    // 1.创建app
    const app = Vue.createApp({
      // data: option api
      data() {
        return {
          info: { name: "why", age: 18 }
        }
      },
      methods: {
        changeInfo() {
          // 1.创建一个新对象, 赋值给info
          // this.info = { name: "kobe" }

          // 2.直接修改原对象某一个属性
          this.info.name = "kobe"
        }
      },
      watch: {
        // 默认watch监听不会进行深度监听
        // info(newValue, oldValue) {
        //   console.log("侦听到info改变:", newValue, oldValue)
        // }

        // 进行深度监听
        info: {
          handler(newValue, oldValue) {
            console.log("侦听到info改变:", newValue, oldValue)//最后数据还是一样
            console.log(newValue === oldValue)
          },
          // 监听器选项:
          // info进行深度监听
          deep: true,
          // 第一次渲染直接执行一次监听器
          immediate: true
        },
        "info.name": function(newValue, oldValue) {
          console.log("name发生改变:", newValue, oldValue)
        }
      }
    })

    // 2.挂载app
    app.mount("#app")
  </script>
```

```html
<div id="app">
    <h2>{{ infos }}</h2>
    <button @click="changeInfo">修改info</button>
  </div>

  <script src="../lib/vue.js"></script>
  <script>
    // 1.创建app
    const app = Vue.createApp({
      // data: option api
      data() {
        return {
          info: { name: "why", age: 18 }
        }
      },
      methods: {
        changeInfo() {
          // 1.创建一个新对象, 赋值给info
          // this.info = { name: "kobe" }

          // 2.直接修改原对象某一个属性
          this.info.name = "kobe"
        }
      },
      computed: {
        infos() {
          return JSON.stringify(this.info);//深拷贝
        }
      },
      watch: {
        // 默认watch监听不会进行深度监听
        // info(newValue, oldValue) {
        //   console.log("侦听到info改变:", newValue, oldValue)
        // }

        // 进行深度监听
        infos: {
          handler(newValue, oldValue) {//深拷贝作用
            console.log("侦听到info改变:", newValue, oldValue)//数据不一样
            console.log(newValue === oldValue)//false
          },
          // 监听器选项:
          // info进行深度监听
          deep: true,
          // 第一次渲染直接执行一次监听器
          immediate: true
        },
        "info.name": function (newValue, oldValue) {
          console.log("name发生改变:", newValue, oldValue)
        }
      }
    })

    // 2.挂载app
    app.mount("#app")
  </script>
```



#### 3.2.3. 其他的写法

* "info.name"

  ```js
    "info.name": function(newValue, oldValue) {
            console.log("name发生改变:", newValue, oldValue)
          }
  ```

  

* 别的写法

  ![image-20221008202415133](F:\Vue\image\image-20221008202415133.png)

* created -> this.$watch()

  * 可以在created的生命周期中，使用 this.$watchs 来侦听；
    * 第一个参数是**要侦听的源**；
    * 第二个参数是侦听的回调函数callback；
    * 第三个参数是**额外的其他选项，比如deep、immediate**；

```js
 // 生命周期回调函数: 当前的组件被创建时自动执行
      // 一般在该函数中, 会进行网络请求
      created() {
        // ajax/fetch/axios
        console.log("created")

        this.$watch("message", (newValue, oldValue) => {
          console.log("message数据变化:", newValue, oldValue)
        }, { deep: true })
      }
```



