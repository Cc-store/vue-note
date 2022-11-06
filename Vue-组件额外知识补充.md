

## 一. 非父子组件的通信

### 1.1. Provide/Inject

* 基本使用

  ![image-20221014102555019](F:\Vue\image\image-20221014102555019.png)

* 函数写法

* 数据的响应式
  * computed

![image-20221014103242975](F:\Vue\image\image-20221014103242975.png)

![image-20221014103313041](F:\Vue\image\image-20221014103313041.png)

computed函数解决在**provide中引入的 this.names.length 本身并不是响应式** 的问题



### 1.2. 事件总线hy-event-store

* 在event-bus.js中创建eventBus对象
* 监听事件:
  * eventBus.on()
* 发出事件:
  * eventBus.emit()

![image-20221014103420403](F:\Vue\image\image-20221014103420403.png)



* Mitt的事件取消

  取消掉之前注册的函数监听

  ```js
   cancleListener() {
        eventBus.off("whyEvent", this.whyEventHandler)
      }
  ```

  



## 二. 额外知识补充

### 2.1. 生命周期函数

> 1.创建组件实例
> created(重要: 1.发送网络请求 2.事件监听 3.this.$watch()
> 2.template模板编译                                                                                   beforeMount
> 3.挂载到虚拟DOM -虚拟DOM -> 真实的DOM -> 界面看到h2/div                  mounted (重要:元素已经被挂载 获取DOM,使用DOM)
> 4.数据更新:message改变                                                                        beforeUpdate
> 根据最新数据生成新的VNode,生成新的虚拟DOM —>真实的DOM
> updated                                                                                                                5.不再使用 即v-if = "false”
> beforeUnmount
> 将之前挂载在虚拟DOM中VNode从虚拟DOM移除
>  unmounted(相对重要: 回收操作(取消事件监听）将组件实例销毁掉

![image-20221014105047785](F:\Vue\image\image-20221014105047785.png)

* created

  ```js
   // 1.组件被创建之前
      beforeCreate() {
        console.log("beforeCreate")
      },
      // 2.组件被创建完成
      created() {
        console.log("created")
        console.log("1.发送网络请求, 请求数据")
        console.log("2.监听eventbus事件")
        console.log("3.监听watch数据")
      }
  ```

  

* mounted

  ```js
  // 3.组件template准备被挂载
      beforeMount() {
        console.log("beforeMount")
      },
      // 4.组件template被挂载: 虚拟DOM -> 真实DOM
      mounted() {
        console.log("mounted")
        console.log("1.获取DOM")
        console.log("2.使用DOM")
      }
  ```

* update

  ```js
    // 5.数据发生改变
      // 5.1. 准备更新DOM
      beforeUpdate() {
        console.log("beforeUpdate")
      },
      // 5.2. 更新DOM
      updated() {
        console.log("updated")
      }
  ```

  

* unmounted

  ```js
  
      // 6.卸载VNode -> DOM元素
      // 6.1.卸载之前
      beforeUnmount() {
        console.log("beforeUnmount")
      },
      // 6.2.DOM元素被卸载完成
      unmounted() {
        console.log("unmounted")
      }
  ```

  

### 2.2. refs引入元素/组件

> 获取到元素对象或者子组件实例

* 在元素/组件中添加 ref 属性
* this.$refs.属性

```html
<template>
  <div class="app">
    <h2 ref="title" class="title" :style="{ color: titleColor }">{{ message }}</h2>
    <button ref="btn" @click="changeTitle">修改title</button>

    <banner ref="banner"/>
  </div>
</template>

<script>
  import Banner from "./Banner.vue"

  export default {
    components: {
      Banner
    },  
    data() {
      return {
        message: "Hello World",
        titleColor: "red"
      }
    },
    methods: {
      changeTitle() {
        // 1.不要主动的去获取DOM, 并且修改DOM内容
        // this.message = "你好啊, 李银河!"
        // this.titleColor = "blue"

        // 2.获取h2/button元素
        console.log(this.$refs.title)
        console.log(this.$refs.btn)

        // 3.获取banner组件: 组件实例
        console.log(this.$refs.banner)
        
        // 3.1.在父组件中可以主动的调用子组件的对象方法
        this.$refs.banner.bannerClick()

        // 3.2.获取banner组件实例, 获取banner中的元素
        console.log(this.$refs.banner.$el)

        // 3.3.如果banner template是多个根, 拿到的是第一个node节点
        // 注意: 开发中不推荐一个组件的template中有多个根元素
        // console.log(this.$refs.banner.$el.nextElementSibling)

        // 4.组件实例还有两个属性(了解):
        console.log(this.$parent) // 获取父组件
        console.log(this.$root) // 获取根组件 
      }
    }
  }
</script>

```



### 2.3. 动态组件的使用

>  动态组件是使用 component 组件，通过一个特殊的attribute is 来实现
>
> currentTab :                                                                                                                          全局注册：可以是通过component函数注册的组件；
> 布局注册：在一个组件对象的components对象中注册的组件；

```vue
component is
 <div class="view">
      <!-- 1.第一种做法: v-if进行判断逻辑, 决定要显示哪一个组件 -->
      <!-- <template v-if="currentIndex === 0">
        <home></home>
      </template>
      <template v-else-if="currentIndex === 1">
        <about></about>
      </template>
      <template v-else-if="currentIndex === 2">
        <category></category>
      </template> -->

      <!-- 2.第二种做法: 动态组件 component -->
      <!-- is中的组件需要来自两个地方: 1.全局注册的组件 2.局部注册的组件 -->
      <!-- <component :is="tabs[currentIndex]"></component> -->
      <component name="why" 
                 :age="18"
                 @homeClick="homeClick"
                 :is="currentTab">
      </component>
    </div>
<script>
     components: {
      Home,
      About,
      Category
    },
    data() {
      return {
        tabs: ["home", "about", "category"],
        // currentIndex: 0
        currentTab: "home"
      }
    },
</script>
```





### 2.4. keep-alive

* 让组件缓存起来, 存活下来

  ![image-20221014114348983](F:\Vue\image\image-20221014114348983.png)

* include/exclude/max

  *  include - string | RegExp | Array。只有名称匹配的组件会被缓存；
  *  exclude - string | RegExp | Array。任何名称匹配的组件都不会被缓存；
  * max - number | string。最多可以缓存多少组件实例，一旦达到这个数
    字，那么缓存组件中最近没有被访问的实例会被销毁；

  ![image-20221014114441294](F:\Vue\image\image-20221014114441294.png)

* 存活生命周期函数:
  
  >  对于缓存的组件来说，再次进入时，我们是**不会执行created或者mounted等生命周期函数的**
  
  * activated
  * deactivated

```js
  // 对于保持keep-alive组件, 监听有没有进行切换
    // keep-alive组件进入活跃状态
    activated() {
      console.log("home activated")
    },
    deactivated() {
      console.log("home deactivated")
    }
```



### 2.5. 异步组件的使用

#### 2.5.1. webpack分包处理

> 对于一些不需要立即使用的组件，我们可以单独对它们进行拆分，拆分成一些小的代码块chunk.js；
> 这些chunk.js会在需要时从服务器加载下来，并且运行代码，显示对应的内容；

* import()

![image-20221014115024912](F:\Vue\image\image-20221014115024912.png)



#### 2.5.2. 异步组件

> 异步的方式来进行加载（目的是可以对其进行分包处理）某些组件

```js
defineAsyncComponent(() => import(""))
```

**defineAsyncComponent接受两种类型的参数：**

* 类型一：工厂函数，该工厂函数需要返回一个Promise对象；

  ![image-20221014115612963](F:\Vue\image\image-20221014115612963.png)

* 类型二：接受一个对象类型，对异步函数进行配置；

  ![image-20221014115606008](F:\Vue\image\image-20221014115606008.png)





### 2.6. v-model组件上

![image-20221014154524171](F:\Vue\image\image-20221014154524171.png)

![image-20221014154651798](F:\Vue\image\image-20221014154651798.png)

```html
 <!-- 1.input v-model -->
    <!-- <input v-model="message">
    <input :value="message" @input="message = $event.target.value"> -->

    <!-- 2.组件的v-model: 默认modelValue -->
    <counter v-model="appCounter"></counter> //等价于
    <counter :modelValue="appCounter" @update:modelValue="appCounter = $event"></counter>
    
    <!-- 3.组件的v-model: 自定义名称counter -->
    <!-- <counter2 v-model:counter="appCounter" 
	v-model:why="appWhy"></counter2> -->
```

```vue
<template>
  <div>
    <h2>Counter: {{ modelValue }}</h2>
    <button @click="changeCounter">修改counter</button>
  </div>
</template>

<script>
  export default {
    props: {
      modelValue: {
        type: Number,
        default: 0
      }
    },
    emits: ["update:modelValue"],
    methods: {
      changeCounter() {
        this.$emit("update:modelValue", 999)
      } 
    }
  }
</script>
```



**绑定多个属性**

![image-20221014154723039](F:\Vue\image\image-20221014154723039.png)



### 2.7. 混入Mixin

> 相同的代码逻辑进行抽取，进行复用

* 在组件通过mixins: []

  ![image-20221014155039062](F:\Vue\image\image-20221014155039062.png)

* 全局混入: app.mixin({})

  ```js
  //全局混入的选项将会影响每一个组件；
  app.mixin({
    created() {
      console.log("mixin created")
    }
  })
  ```

  



### 2.7.1 Mixin的合并规则

1. 如果是data函数的返回值对象
   * 返回值对象默认情况下会**进行合并**；
   * 如果data返回值对象的属性发生了冲突，那么**会保留组件自身的数据**；
2. 如何生命周期钩子函数
   * 生命周期的钩子函数会被**合并到数组中**，都会被调用；
3. 值为对象的选项，例如 methods、components 和 directives，将被合并为同一个对象。
   * 比如都有**methods**选项，并且都定义了方法，那么它们都会生效；
   * 但是如果**对象的key相同，那么会取组件对象的键值对**



## 三. Composition API

### 3.1. 认识组合API

* options API -> Composition API

> 逻辑分散 ——> 同一个逻辑关注点相关的代码收集在一起

**setup函数的参数**

1.  第一个参数：**props**
   * 父组件传递过来的属性会被放到props对象中，我们在setup中如果需要使用，那么就可以直接通过props参数获取
     * **在setup函数中想要使用props，那么不可以通过 this 去获取**
2. 第二个参数：**context** （**SetupContext**）

*  attrs：所有的非prop的attribute；
* slots：父组件传递过来的插槽（这个在以渲染函数返回时会有作用，后面会讲到
* emit：当我们组件内部需要发出事件时会用到emit（因为我们不能访问this，所以不可以通过 this.$emit发出事件）；



**setup函数的返回值**

> setup的返回值可以**在模板template中被使用**；即通过setup的返回值来替代data选项；

![image-20221014162843905](F:\Vue\image\image-20221014162843905.png)

 **counter**  ： Vue并不会跟踪它的变化，来引起界面的响应式操作 （需要经过ref、reactive处理）



### 3.2. 组合API实现计数器案例

```html
<script>
import { ref } from 'vue'
import useCounter from './hooks/useCounter'

export default {
  setup() {
    // 1.定义counter的内容
    // 默认定义的数据都不是响应式数据
    // let counter = ref(100)
    // const increment = () => {
    //   counter.value++
    //   console.log(counter.value)
    // }
    // const decrement = () => {
    //   counter.value--
    // }
    // const { counter, increment, decrement } = useCounter()

    return {
      ...useCounter()
    }
  }
}
</script>
```











