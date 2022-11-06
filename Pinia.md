

## 一. Pinia的使用

### 1.1. Pinia介绍和Vuex的区别(面试)

1. 比如mutations 不再存在：

   * 非常 冗长；
   * 他们最初带来了 devtools 集成，但这不再是问题；

2. 更友好的TypeScript支持，Vuex之前对TS的支持很不友好；

3. 不再有modules的嵌套结构：

   * 你可以灵活使用每一个store，它们是通过扁平化的方式来相互使用的；

4. 也不再有命名空间的概念，不需要记住它们的复杂关系；

   ![image-20221020205424318](F:\Vue\image\image-20221020205424318.png)



### 1.2. Pinia的安装和基本使用

* createPinia

  ```js
  import { createPinia } from 'pinia'
  
  const pinia = createPinia()
  
  export default pinia
  ```

  

* app.use

  ```js
  import pinia from './stores'
  
  createApp(App).use(pinia).mount('#app')
  ```



#### 1.2.1 认识Store

> 一个 Store （如 Pinia）是一个实体，它会持有为绑定到你组件树的状态和业务逻辑，也就是保存了全局的状态；
> 每个人都可以读取和写入的组件；
> 可以在你的应用程序中定义任意数量的Store来管理你的状态；

 **Store有三个核心概念：**

* **state**、**getters**、**actions**；
* 等同于组件的**data**、**computed**、**methods**；

* 定义store

  *  Store 是**使用 defineStore()** 定义的
  * 需要一个**唯一名称**，作为第一个参数传递

  ```js
  import { defineStore } from 'pinia'
  
  const useUser = defineStore("user", {
    state: () => ({
      name: "why",
      age: 18,
      level: 100
    })
  })
  
  export default useUser
  ```

* 这个 name，也称为 id，是必要的，Pinia 使用它来将 store 连接到 devtools。

* 返回的函数统一使用useX作为命名方案，这是约定的规范

**使用定义的Store**

* Store在它被使用之前是不会创建的，我们可以通过调用use函数来使用Store：

  ```html
  <template>
    <div class="home">
      <h2>Home View</h2>
      <h2>count: {{ counterStore.count }}</h2>
      <h2>count: {{ count }}</h2>
      <button @click="incrementCount">count+1</button>
    </div>
  </template>
  
  <script setup>
    import { toRefs } from 'vue'
    import { storeToRefs } from 'pinia'
    import useCounter from '@/stores/counter';
  
    const counterStore = useCounter()
  
    // const { count } = toRefs(counterStore)
    const { count } = storeToRefs(counterStore)
  
  
    function incrementCount() {
      counterStore.count++
    }
  
  </script>
  ```

* Store获取到后不能被解构，那么会失去响应式

  *  为了从 Store 中提取属性同时保持其响应式，您需要使用**storeToRefs()**

```js
  const { count } = counterStore //不是响应式的
  const { count : count2 } = toRefs(counterStore)//是响应式的
  const { count : count3 } = storeToRefs(counterStore)//是响应式的
```



### 1.3. Pinia核心state

> 在 Pinia 中，状态被定义为返回初始状态的函数；
>
> ```js
> export const useUser = defineStore("user", {
>   state: () => ({
>     name: "why",
>     age: 18,
>     level: 100
>   })
> })
> ```

* 使用和修改 store.xxx

  ```js
  // 1.一个个修改状态
      userStore.name = "kobe"
      userStore.age = 20
      userStore.level = 200
  
      // 2.一次性修改多个状态
      userStore.$patch({
        name: "james",
        age: 35
      })
  ```

  

* 了解:
  * store.$reset  : 通过调用 store 上的  `$reset()` 方法将状态 重置 到其初始值；
  
    ```js
     function resetState() {
        userStore.$reset()
      }
    ```
  
    
  
  * store.$patch :  允许使用部分“state”对象同时应用多个更改；
  
    ```js
    // 2.一次性修改多个状态
        userStore.$patch({
          name: "james",
          age: 35
        })
    ```
  
    
  
  * `store.$state = {}`  :   通过将其 $state 属性设置为新对象来替换 Store 的整个状态：
  
    * ```js
       // 3.替换state为新的对象
          const oldState = userStore.$state
          userStore.$state = {
            name: "curry",
            level: 200
          }
          console.log(oldState === userStore.$state) //true
      ```
  
      





### 1.4. Pinia核心getters

> Getters相当于Store的计算属性
>
>  getters中可以定义接受一个state作为参数的函数

* 基本使用

  ```html
  <template>
    <div class="home">
      <h2>Home View</h2>
      <h2>doubleCount: {{ counterStore.doubleCount }}</h2>
      <h2>doubleCountAddOne: {{ counterStore.doubleCountAddOne }}</h2>
      <h2>friend-111: {{ counterStore.getFriendById(111) }}</h2>
      <h2>friend-112: {{ counterStore.getFriendById(112) }}</h2>
      <h2>showMessage: {{ counterStore.showMessage }}</h2>
      <button @click="changeState">修改state</button>
      <button @click="resetState">重置state</button>
    </div>
  </template>
  
  <script setup>
    import useCounter from '@/stores/counter';
  
    const counterStore = useCounter()
  
  </script>
  ```

  

  ```js
  import useUser from './user'
  getters: {
      // 1.基本使用
      doubleCount(state) {
        return state.count * 2
      },
      // 2.一个getter引入另外一个getter
      doubleCountAddOne() {
        // this是store实例
        return this.doubleCount + 1
      },
      // 3.getters也支持返回一个函数
      getFriendById(state) {
        return function(id) {
          for (let i = 0; i < state.friends.length; i++) {
            const friend = state.friends[i]
            if (friend.id === id) {
              return friend
            }
          }
        }
      },
      // 4.getters中用到别的store中的数据
      showMessage(state) {
        // 1.获取user信息
        const userStore = useUser()
  
        // 2.获取自己的信息
  
        // 3.拼接信息
        return `name:${userStore.name}-count:${state.count}`
      }
    }
  ```

  

* 引入其他的getters

  ![image-20221020213810506](F:\Vue\image\image-20221020213810506.png)

* getters返回函数

   Getters也可以返回一个函数，这样就可以接受参数

  ![image-20221020213915978](F:\Vue\image\image-20221020213915978.png)

  

* getters引入其他store数据

  ![image-20221020214029022](F:\Vue\image\image-20221020214029022.png)





### 1.5. Pinia核心Actions

>  Actions 相当于组件中的 methods
>
> 和getters一样，在action中可以通过this访问整个store实例的所有操作

* 基本使用

  ![image-20221020214259671](F:\Vue\image\image-20221020214259671.png)

  ```html
  <template>
    <div class="home">
      <h2>Home View</h2>
      <h2>doubleCount: {{ counterStore.count }}</h2>
      <button @click="changeState">修改state</button>
  
      <!-- 展示数据 -->
      <h2>轮播的数据</h2>
      <ul>
        <template v-for="item in homeStore.banners">
          <li>{{ item.title }}</li>
        </template>
      </ul>
    </div>
  </template>
  
  <script setup>
    import useCounter from '@/stores/counter';
    import useHome from '@/stores/home';
  
    const counterStore = useCounter()
  
    function changeState() {
      // counterStore.increment()
      counterStore.incrementNum(10)
    }
  
    const homeStore = useHome()
    homeStore.fetchHomeMultidata().then(res => {
      console.log("fetchHomeMultidata的action已经完成了:", res)
    })
  
  </script>
  ```

  ```js
  import { defineStore } from 'pinia'
  
  const useHome = defineStore("home", {
    state: () => ({
      banners: [],
      recommends: []
    }),
    actions: {
      async fetchHomeMultidata() {
        const res = await fetch("http://123.207.32.32:8000/home/multidata")
        const data = await res.json()
  
        this.banners = data.data.banner.list
        this.recommends = data.data.recommend.list
        
        // return new Promise(async (resolve, reject) => {
        //   const res = await fetch("http://123.207.32.32:8000/home/multidata")
        //   const data = await res.json()
  
        //   this.banners = data.data.banner.list
        //   this.recommends = data.data.recommend.list
  
        //   resolve("bbb")
        // })
      }
    }
  })
  
  export default useHome
  
  ```

  

* 传入参数

  ```js
   function changeState() {
      // counterStore.increment()
      counterStore.incrementNum(10)
    }
  
   actions: {
      increment() {
        this.count++
      },
      incrementNum(num) {
        this.count += num
      }
    }
  ```

  

* 发送异步请求

  ![image-20221020214352783](F:\Vue\image\image-20221020214352783.png)

* 返回Promise回调

  ```js
   const homeStore = useHome()
    homeStore.fetchHomeMultidata().then(res => {
      console.log("fetchHomeMultidata的action已经完成了:", res)
    })
  ```

  ```js
  actions: {
      async fetchHomeMultidata() {
        const res = await fetch("http://123.207.32.32:8000/home/multidata")
        const data = await res.json()
  
        this.banners = data.data.banner.list
        this.recommends = data.data.recommend.list
        
        // return new Promise(async (resolve, reject) => {
        //   const res = await fetch("http://123.207.32.32:8000/home/multidata")
        //   const data = await res.json()
  
        //   this.banners = data.data.banner.list
        //   this.recommends = data.data.recommend.list
  
        //   resolve("bbb")
        // })
      }
  ```



