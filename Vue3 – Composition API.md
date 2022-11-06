

## 一. Composition API（一）

### 1.1. 使用reactive定义响应式数据

> 使用reactive函数处理我们的数据之后，数据再次被使用时就会进行依赖收集
>
>  当数据发生改变时，所有收集到的依赖都是进行对应的响应式操作（比如更新界面）
>
> data选项，也是在内部交给了reactive函数将其编程响应式对象的；
>
>  reactive API对传入的类型是有限制的，它要求我们必须传入的是一个**对象或者数组类型**

* 复杂类型

  ```js
     <!-- 默认情况下在template中使用ref时, vue会自动对其进行解包(取出其中value) -->
      <h2>当前计数: {{ counter }}</h2>
    	<hr>
      <!-- 使用的时候不需要写.value -->
      <h2>当前计数: {{ info.counter }}</h2>
      <!-- 修改的时候需要写.value -->
      <button @click="info.counter.value++">+1</button>
  
  
        // 2.定义响应式数据
        // 2.1.reactive函数: 定义复杂类型的数据
        const account = reactive({
          username: "coderwhy",
          password: "123456"
        })
      
  ```

  



### 1.2. 使用ref定义响应式数据

>  在模板中引入ref的值时，Vue会自动帮助我们进行解包操作，所以我们并不需要在模板中通过 ref.value 的方式来使用；
> 在 setup 函数内部，它依然是一个 ref引用， 所以对其进行操作时，我们依然需要使用 ref.value的方式；

* 基本类型
* 复杂类型
* 在template自动解包：浅层解包
  * info = { ref: xxxx }

![image-20221014204342521](F:\Vue\image\image-20221014204342521.png)

```js
  // 2.2.ref函数: 定义简单类型的数据(也可以定义复杂类型的数据)
      // counter定义响应式数据
      const counter = ref(0)
      function increment() {
        counter.value++
      }

      // 3.ref是浅层解包
      const info = {
        counter
      }
      
    <!-- 默认情况下在template中使用ref时, vue会自动对其进行解包(取出其中value) -->
    <h2>当前计数: {{ counter }}</h2>
    <button @click="increment">+1</button>
    <button @click="counter++">+1</button>

    <hr>
    <!-- 使用的时候不需要写.value -->
    <h2>当前计数: {{ info.counter }}</h2>
    <!-- 修改的时候需要写.value -->
    <button @click="info.counter.value++">+1</button>
```



### 1.3. 开发中选择reactive/ref

```js
  setup() {
      // 定义响应式数据: reactive/ref
      // 强调: ref也可以定义复杂的数据
      const info = ref({})
      console.log(info.value)

      // 1.reactive的应用场景
      // 1.1.条件一: reactive应用于本地的数据
      // 1.2.条件二: 多个数据之间是有关系/联系(聚合的数据, 组织在一起会有特定的作用)
      const account = reactive({
        username: "coderwhy",
        password: "1234567"
      })

      const username = ref("coderwhy")
      const password = ref("123456")

      // 2.ref的应用场景: 其他的场景基本都用ref(computed)
      // 2.1.定义本地的一些简单数据
      const message = ref("Hello World")
      const counter = ref(0)
      const name = ref("why")
      const age = ref(18)

      // 2.定义从网络中获取的数据也是使用ref
      // const musics = reactive([])
      const musics = ref([])
      onMounted(() => {
        const serverMusics = ["海阔天空", "小苹果", "野狼"]
        musics.value = serverMusics
      })


      return {
        account,
        username,
        password,
        name,
        age
      }
    }
```



### 1.4. readonly的使用

>  readonly会返回原始对象的只读代理（也就是它依然是一个Proxy，这是一个proxy的set方法被劫持，并且不能对其进行修改）

#### 1.4.1. 单项数据流

> reactive或者ref可以获取到一个响应式的对象，传入给其他地方（组件）的这个响应式对象在另外一个地方（组件）可以被使用，但是不能被修改

**常见的readonly方法会传入三个类型的参数：**

* 普通对象；
* reactive返回的对象；
* ref的对象；

![image-20221014205104362](F:\Vue\image\image-20221014205104362.png)





#### 14.2. readonly

> 本质上就是readonly返回的对象的setter方法被劫持了而已

1.  readonly返回的对象都是不允许修改的；
2. 但是经过readonly处理的原来的对象是允许被修改的；
   * 比如 const info = readonly(obj)，info对象是不允许被修改的；
   * 当obj被修改时，readonly返回的info对象也会被修改；
   * 但是我们不能去修改readonly返回的对象info；

![image-20221014205159062](F:\Vue\image\image-20221014205159062.png)





### 1.5. reactive函数补充

* isProxy : **检查对象是否是由 reactive 或 readonly创建的 proxy**
* isReactive : 
  * 检查对象**是否是由 reactive创建的响应式代理**：
  * 如果该代理是 readonly 建的，但包裹了由 reactive 创建的另一个代理，它也会返回 true；
* isReadonly :  **检查对象是否是由 readonly 创建的只读代理。**
* shallowReactive :  创建一个响应式代理，它跟踪其自身 property 的响应性，但**不执行嵌套对象的深层响应式转换** (深层还是原生对象)
* shallowReadonly : 创建一个 proxy，使其自身的 property 为只读，但**不执行嵌套对象的深度只读转换**（深层还是可读、可写的）。
* toRaw :  返回 **reactive 或 readonly 代理的原始对象**（不建议保留对原始对象的持久引用。请谨慎使用）。



### 1.6. ref函数补充

1. toRefs 
   * 使用ES6的解构语法，对reactive返回的对象进行解构获取值，那么之后无论是修改结构后的变量，还是修改reactive返回的state对象，**数据都不再是响应式**
   * toRefs的函数，可以将reactive返回的对象中的属性都转成ref；

![image-20221014211906302](F:\Vue\image\image-20221014211906302.png)

解构出来的 name 和 age 本身都是 ref的, 相当于已经在state.name和ref.value之间建立了 链接，任何一个修改都会引起另外一个变化



* toRef : reactive对象中的属性为ref, 那么可以使用toRef的方法

  ![image-20221014212010882](F:\Vue\image\image-20221014212010882.png)

* unref :  获取一个ref引用中的value

  * 如果参数是一个 ref，则返回内部值，否则返回参数本身
  *  val = isRef(val) ? val.value : val 的语法糖函数；

*  isRef :   判断值是否是一个ref对象

*  shallowRef

  * 创建一个浅层的ref对象；

*  triggerRef

  *   手动触发和 shallowRef 相关联的副作用

![image-20221014212237072](F:\Vue\image\image-20221014212237072.png)



### 1.7. setup中不能使用this

> this 没有被传入

![image-20221014212527064](F:\Vue\image\image-20221014212527064.png)

```js
setup(props, context) {
      function showInfobtnClick() {
        context.emit("changeInfoName", "kobe")
      }

      function roInfoBtnClick() {
        context.emit("changeRoInfoName", "james")
      }

      return {
        showInfobtnClick,
        roInfoBtnClick
      }
    }
```





## 二. Composition API（二）

### 2.1. computed计算属性(重点)

* 接收一个**getter函数**，并为 getter 函数返回的值，返回一个不变的 ref 对象；
* 接收一个具有 **get 和 set 的对象**，返回一个可变的（可读写）ref 对象；

![image-20221014212915440](F:\Vue\image\image-20221014212915440.png)

### 2.2. ref获取元素/组件(半个重点)

> 只需要定义一个ref对象，绑定到元素或者组件的ref属性上即可；

```html
<template>
  <!-- 1.获取元素 -->
  <h2 ref="titleRef">我是标题</h2>
  <button ref="btnRef">按钮</button>

  <!-- 2.获取组件实例 -->
  <show-info ref="showInfoRef"></show-info>

  <button @click="getElements">获取元素</button>
</template>

<script>
  import { ref, onMounted } from 'vue'
  import ShowInfo from './ShowInfo.vue'

  export default {
    components: {
      ShowInfo
    },
    setup() {
      const titleRef = ref()
      const btnRef = ref()
      const showInfoRef = ref()

      // mounted的生命周期函数
      onMounted(() => {
        console.log(titleRef.value)//<h2>我是标题</h2>
        console.log(btnRef.value)//<button>按钮</button>
        console.log(showInfoRef.value)//Proxy {…}

        showInfoRef.value.showInfoFoo()//showInfo foo function 调用子组件的方法
      })

      function getElements() {
        console.log(titleRef.value)//<h2>我是标题</h2>
      }

      return {
        titleRef,
        btnRef,
        showInfoRef,
        getElements
      }
    }
  }
</script>
```



### 2.3. 生命周期注册函数(重要)

> 可以使用直接导入的 onX 函数注册生命周期钩子

![image-20221014213736543](F:\Vue\image\image-20221014213736543.png)

```js
	setup() {
      // 在执行setup函数的过程中, 你需要注册别的生命周期函数
      onMounted(() => {
        console.log("onmounted")
      })
    }
```



* beforeCreate/Created -> setup(可直接看为set up)





### 2.4. provide/inject

provide可以传入两个参数：

* name：提供的属性名称；
* value：提供的属性值；

```js
 setup() {
      const name = ref("why")

      provide("name", name)
      provide("age", 18)

      return {
        name
      }
    }
```



 inject可以传入两个参数：

* 要 inject 的 property 的 name；
* 默认值；

```js
  // inject的options api注入, 那么依然需要手动来解包
    // inject: ["name", "age"],
    setup() {
      const name = inject("name")
      const age = inject("age")
      const height = inject("height", 1.88)

      return {
        name,
        age,
        height
      }
    }
```



**数据的响应式**

> 了增加 provide 值和 inject 值之间的响应性，我们可以在 provide 值时使用 ref 和 reactive。

![image-20221014214502193](F:\Vue\image\image-20221014214502193.png)



### 2.5. watch/watchEffect(半个重要)

* watch/watchEffect区别:
  * 1.watch必须制定数据源, watchEffect自动收集依赖
  * 2.watch监听到改变, 可以拿到改变前后value
  * 3.watchEffect默认直接执行一次, watch在不设置immediate第一次是不执行

1. Watch的使用
   * watch的API完全等同于组件watch选项的Property
     *  watch需要侦听特定的数据源，并且执行其回调函数；
     * 默认情况下它是惰性的，只有当被侦听的源发生变化时才会执行回调；
   * 侦听器还可以使用数组同时侦听多个源
   * 侦听一个深层的侦听，那么依然需要设置 deep 为true：

```html
<template>
  <div>AppContent</div>
  <button @click="message = '你好啊,李银河!'">修改message</button>
  <button @click="info.friend.name = 'james'">修改info</button>
</template>

<script>
  import { reactive, ref, watch } from 'vue'

  export default {
    setup() {
      // 1.定义数据
      const message = ref("Hello World")
      const info = reactive({
        name: "why",
        age: 18,
        friend: {
          name: "kobe"
        }
      })

      // 2.侦听数据的变化
      watch(message, (newValue, oldValue) => {
        console.log(newValue, oldValue)
      })
      watch(info, (newValue, oldValue) => {
        console.log(newValue, oldValue)
        console.log(newValue === oldValue)
      }, {
        immediate: true  //立即执行
      })

      // 3.监听reactive数据变化后, 获取普通对象
      watch(() => ({ ...info }), (newValue, oldValue) => {
        console.log(newValue, oldValue)
      }, {
        immediate: true,
        deep: true
      })

      return {
        message,
        info
      }
    }
  }
</script>

```



2. watchEffect

> 当侦听到某些响应式数据变化时，这个时候可以使用 watchEffect。

* watchEffect传入的函数会被立即执行一次，并且在执行的过程中会收集依赖；
* 只有收集的依赖发生变化时，watchEffect传入的函数才会再次执行；
* 在发生某些情况下，希望停止侦听，可以获取watchEffect的返回值函数，调用该函数即可 `stopWatch()`

```html
<template>
  <div>
    <h2>当前计数: {{ counter }}</h2>
    <button @click="counter++">+1</button>
    <button @click="name = 'kobe'">修改name</button>
  </div>
</template>

<script>
  import { watchEffect, watch, ref } from 'vue'

  export default {
    setup() {
      const counter = ref(0)
      const name = ref("why")

      // watch(counter, (newValue, oldValue) => {})

      // 1.watchEffect传入的函数默认会直接被执行
      // 2.在执行的过程中, 会自动的收集依赖(依赖哪些响应式的数据)
      const stopWatch = watchEffect(() => {
        console.log("-------", counter.value, name.value)

        // 判断counter.value > 10
        if (counter.value >= 10) {
          stopWatch()
        }
      })

      return {
        counter,
        name
      }
    }
  }
</script>
```





### 2.6. 自定义Hook的练习(思想)

#### 2.6.1. useCounter





#### 2.6.2. useTitle

```js
import { ref, watch } from "vue";

export default function useTitle(titleValue) {
  // document.title = title

  // 定义ref的引入数据
  const title = ref(titleValue)

  // 监听title的改变
  watch(title, (newValue) => {
    document.title = newValue
  }, {
    immediate: true
  })

  // 返回ref值
  return {
    title
  }
}

 // 2.修改标题
      const { title } = useTitle("首页")

      // 3.监听按钮的点击
      function popularClick() {
        title.value = "首页-流行"
      }
```



#### 2.6.3. useScrollPosition

```js
import { reactive } from 'vue'

export default function useScrollPosition() {
  // 1.使用reative记录位置
  const scrollPosition = reactive({
    x: 0,
    y: 0
  })

  // 2.监听滚动
  document.addEventListener("scroll", () => {
    scrollPosition.x = window.scrollX
    scrollPosition.y = window.scrollY
  })


  return {
    scrollPosition
  }
}
```



### 2.7. script setup语法糖(重要)

>  <script setup> 中的代码会在每次组件实例被创建的时候执行

* defineProps  : ` props `

  ```js
  // 定义props
  const props = defineProps({
    name: {
      type: String,
      default: "默认值"
    },
    age: {
      type: Number,
      default: 0
    }
  })
  ```

  

* defineEmits   :  ` emits`

  ```js
  // 绑定函数, 并且发出事件
  const emits = defineEmits(["infoBtnClick"])
  function showInfoBtnClick() {
    emits("infoBtnClick", "showInfo内部发生了点击")
  }
  
  ```

![image-20221015102256773](F:\Vue\image\image-20221015102256773.png)



* defineExpose  
  * 使用 <script setup> 的组件是默认关闭的
    * 通过模板 ref 或者 $parent 链获取到的组件的公开实例，不会暴露任何在 <script setup> 中声明的绑定
  * 通过 defineExpose 编译器宏来显式指定在 <script setup> 组件中要暴露出去的 property

![image-20221015102446903](F:\Vue\image\image-20221015102446903.png)



```html
<template>
  <div>AppContent: {{ message }}</div>
  <button @click="changeMessage">修改message</button>
  <show-info name="why" 
             :age="18"
             @info-btn-click="infoBtnClick"
             ref="showInfoRef">
  </show-info>
  <show-info></show-info>
  <show-info></show-info>
</template>

<script setup>
  // 1.所有编写在顶层中的代码, 都是默认暴露给template可以使用
  import { ref, onMounted } from 'vue'
  import ShowInfo from './ShowInfo.vue'

  // 2.定义响应式数据  通过ref、reactive来创建。
  const message = ref("Hello World")
  console.log(message.value)

  // 3.定义绑定的函数
  function changeMessage() {
    message.value = "你好啊, 李银河!"
  }

  function infoBtnClick(payload) {
    console.log("监听到showInfo内部的点击:", payload)
  }

  // 4.获取组件实例
  const showInfoRef = ref()
  onMounted(() => {
    showInfoRef.value.foo()
  })

</script>
```



















