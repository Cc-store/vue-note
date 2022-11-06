

## 一. Vuex状态管理

### 1.1. 理解状态管理

> 保存数据并进行相对
>
> 应的处理

![image-20221019212527928](F:\Vue\image\image-20221019212527928.png)

### 1.2. Vuex基本使用

* 安装vuex
* 创建store
* app.use(store)
* tempate -> $store.state.counter

![image-20221020160313188](F:\Vue\image\image-20221020160313188.png)



### 1.3. 核心概念一State

> store本质上是一个容器，它包含着你的应用中大部分的状态（state）
>
> Vuex 的状态存储是响应式的
>
>  改变store中的状态的唯一途径就显示提交 (commit) mutation

#### 1.3.1. 基本使用

```js
const store = createStore({
  state: () => ({
    // 模拟数据
    // counter: 100,
    rootCounter: 100,
    name: "coderwhy",
    level: 100,
    avatarURL: "http://xxxxxx",
    friends: [
      { id: 111, name: "why", age: 20 },
      { id: 112, name: "kobe", age: 30 },
      { id: 113, name: "james", age: 25 }
    ],

    // 服务器数据
    // banners: [],
    // recommends: []
  })
})
```

```html
<template>
  <div class="app">
    <h2>Home当前计数: {{ $store.state.counter }}</h2>
    <h2>Computed当前计数: {{ storeCounter }}</h2>
    <h2>Setup当前计数: {{ counter }}</h2>
    <button @click="increment">+1</button>
  </div>
</template>

<script>
  export default {
    computed: {
      storeCounter() {
        return this.$store.state.counter
      }
    }
  }
</script>

<script setup>
  import { toRefs } from 'vue'
  import { useStore } from 'vuex'

  const store = useStore()
  const { counter } = toRefs(store.state)
  
  function increment() {
    // store.state.counter++
    store.commit("increment")
  }
</script>

```



#### 1.3.2. 映射使用

* options api

  ```html
  <script>
  //02_核心一State-mapState映射
  import { mapState } from 'vuex'
  
  export default {
    computed: {
      fullname() {
        return "xxx"
      },
      // name() {
      //   return this.$store.state.name
      // },
      ...mapState(["name", "level", "avatarURL"]),//函数
      ...mapState({
        sName: state => state.name,
        sLevel: state => state.level
      })
    }
  }
  </script>
  
  <template>
    <div class="app">
      <button @click="incrementLevel">修改level</button>
      <!-- 1.在模板中直接使用多个状态 -->
      <h2> name: {{ $store.state.name }}</h2>
      <h2>level: {{ $store.state.level }}</h2>
      <!-- <h2>avatar: {{ $store.state.avatarURL }}</h2> -->
  
      <!-- 2.计算属性(映射状态: 数组语法) -->
      <h2>name: {{ name }}</h2>
      <h2>level: {{ level }}</h2>
  
      <!-- 3.计算属性(映射状态: 对象语法) -->
      <!-- <h2>name: {{ sName }}</h2>
      <h2>level: {{ sLevel }}</h2> -->
    </div>
  </template>
  ```

  

* composition api

  ```html
  <script setup>
  import { computed, toRefs } from 'vue'
  import { mapState, useStore } from 'vuex'
  import useState from "../hooks/useState"
  
  // 1.一步步完成
  const { name, level } = mapState(["name", "level"])
  const store = useStore()
  const cName = computed(name.bind({ $store: store }))
  const cLevel = computed(level.bind({ $store: store }))
  
  // 2.使用useState
  // const { name, level } = useState(["name", "level"])
  
  // 3.直接对store.state进行解构(推荐)
  // const store = useStore()
  // const { name, level } = toRefs(store.state)
  
  function incrementLevel() {
    store.state.level++
  }
  
  </script>
  
  <template>
    <div class="app">
      <button @click="incrementLevel">修改level</button>
      <!-- 1.在模板中直接使用多个状态 -->
      <h2> name: {{ $store.state.name }}</h2>
      <h2>level: {{ $store.state.level }}</h2>
      <!-- <h2>avatar: {{ $store.state.avatarURL }}</h2> -->
  
      <!-- 4.setup计算属性(映射状态: 对象语法) -->
      <h2>name: {{ cName }}</h2>
      <h2>level: {{ cLevel }}</h2>
  
      <!-- 5.setup计算属性(映射状态: 对象语法) -->
      <h2>name: {{ name }}</h2>
      <h2>level: {{ level }}</h2>
    </div>
  </template>
  ```

* 使用hooks进行封装 ： 默认情况下，Vuex并没有提供非常方便的使用mapState的方式

  ```js
  import { computed } from 'vue'
  import { useStore, mapState } from 'vuex'
  
  export default function useState(mapper) {
    const store = useStore()
    const stateFnsObj = mapState(mapper)
    
    const newState = {}
    Object.keys(stateFnsObj).forEach(key => {
      newState[key] = computed(stateFnsObj[key].bind({ $store: store }))
    })
  
    return newState
  }
  
  setup(){
      const state = useState({
          name : state => state.name,
          age : state => state.age,
          height : state => state.height
      })
      
      return {
          ...state
      }
  }
  ```

  

### 1.4. 核心概念二getter

>  某些属性我们可能需要经过变化后来使用，这个时候可以使用getters：

#### 1.4.1. 基本使用

* 直接使用

  ```html
  <template>
    <div class="app">
      <!-- <button @click="incrementLevel">修改level</button> -->
      <h2>doubleCounter: {{ $store.getters.doubleCounter }}</h2>
      <h2>friendsTotalAge: {{ $store.getters.totalAge }}</h2>
      <h2>message: {{ $store.getters.message }}</h2>
  
      <!-- 根据id获取某一个朋友的信息 -->
      <h2>id-111的朋友信息: {{ $store.getters.getFriendById(111) }}</h2>
      <h2>id-112的朋友信息: {{ $store.getters.getFriendById(112) }}</h2>
    </div>
  </template>
  
  <script>//03_核心二Getters-基本使用
    export default {
      computed: {
      }
    }
  </script>
  ```

  

  ```js
  state: () => ({
      // 模拟数据
      counter: 100,
      // rootCounter: 100,
      name: "coderwhy",
      level: 100,
      avatarURL: "http://xxxxxx",
      friends: [
        { id: 111, name: "why", age: 20 },
        { id: 112, name: "kobe", age: 30 },
        { id: 113, name: "james", age: 25 }
      ],
  
      // 服务器数据
      // banners: [],
      // recommends: []
    }),
    getters: {
      // 1.基本使用
      doubleCounter(state) {
        return state.counter * 2
      },
      totalAge(state) {
        return state.friends.reduce((preValue, item) => {
          return preValue + item.age
        }, 0)
      },
      // 2.在该getters属性中, 获取其他的getters
      message(state, getters) {
        return `name:${state.name} level:${state.level} friendTotalAge:${getters.totalAge}`
      },
      // 3.getters是可以返回一个函数的, 调用这个函数可以传入参数(了解)
      getFriendById(state) {
        return function(id) {
          const friend = state.friends.find(item => item.id === id)
          return friend
        }
      }
    }
  ```

  ![image-20221020152229686](F:\Vue\image\image-20221020152229686.png)

* 引入别的getters

  ![image-20221020152336041](F:\Vue\image\image-20221020152336041.png)

* 返回函数, 接收参数

  ![image-20221020152306865](F:\Vue\image\image-20221020152306865.png)





#### 1.4.2. 映射使用

- options api

  ```html
  <template>
    <div class="app">
      <button @click="changeAge">修改name</button>
  
      <h2>doubleCounter: {{ doubleCounter }}</h2>
      <h2>friendsTotalAge: {{ totalAge }}</h2>
      <h2>message: {{ message }}</h2>
  
      <!-- 根据id获取某一个朋友的信息 -->
      <h2>id-111的朋友信息: {{ getFriendById(111) }}</h2>
      <h2>id-112的朋友信息: {{ getFriendById(112) }}</h2>
    </div>
  </template>
  
  <script>
    import { mapGetters } from 'vuex'
  
    export default {
      computed: {
        ...mapGetters(["doubleCounter", "totalAge"]),
        ...mapGetters(["getFriendById"])
      }
    }
  </script>
  ```

  

- composition api

  ```html
  
  <script setup>
  
    import { computed, toRefs } from 'vue';
    import { mapGetters, useStore } from 'vuex'
  
    const store = useStore()
  
    // 1.使用mapGetters
    // const { message: messageFn } = mapGetters(["message"])
    // const message = computed(messageFn.bind({ $store: store }))
  
    // 2.直接解构, 并且包裹成ref
    // const { message } = toRefs(store.getters)
  
    // 3.针对某一个getters属性使用computed
    const message = computed(() => store.getters.message)////Ref
  
    function changeAge() {
      store.state.name = "kobe"
    }
  
  </script>
  ```

  



### 1.5. 核心概念三Mutations

#### 1.5.1. 重要原则

* 修改state, 必须使用mutation



#### 1.5.2. 基本使用

* 直接使用

  ```html
  <template>
    <div class="app">
      <button @click="changeName">修改name</button>
      <button @click="incrementLevel">递增level</button>
      <button @click="changeInfo">修改info</button>
      <h2>Store Name: {{ $store.state.name }}</h2>
      <h2>Store Level: {{ $store.state.level }}</h2>
    </div>
  </template>
  
  <script>
  
    import { CHANGE_INFO } from "@/store/mutation_types"
  
    export default {
      computed: {
      },
      methods: {
        changeName() {
          // this.$store.state.name = "李银河"
          this.$store.commit("changeName", "王小波")
        },
        incrementLevel() {
          this.$store.commit("incrementLevel")
        },
        changeInfo() {
          this.$store.commit(CHANGE_INFO, {
            name: "王二",
            level: 200
          })
        }
      }
    }
  </script>
  ```

  

* 传入参数

```js
mutations: {
    increment(state) {
      state.counter++
    },
    changeName(state, payload) {
      state.name = payload
    },
    incrementLevel(state) {
      state.level++
    },
     //export const CHANGE_INFO = "changeInfo"
    [CHANGE_INFO](state, newInfo) {
      state.level = newInfo.level
      state.name = newInfo.name

      // 重要的原则: 不要在mutation方法中执行异步操作
      // fetch("xxxx").then(res => {
      //   res.json().then(res => {
      //     state.name = res.name
      //   })
      // })
    },
    // changeBanners(state, banners) {
    //   state.banners = banners
    // },
    // changeRecommends(state, recommends) {
    //   state.recommends = recommends
    // }
  }

<script>

  import { CHANGE_INFO } from "@/store/mutation_types"

  export default {
    computed: {
    },
    methods: {
      changeName() {
        // this.$store.state.name = "李银河"
        this.$store.commit("changeName", "王小波")
      },
      incrementLevel() {
        this.$store.commit("incrementLevel")
      },
      changeInfo() {
        this.$store.commit(CHANGE_INFO, {
          name: "王二",
          level: 200
        })
      }
    }
  }
</script>
```

![image-20221020154547633](F:\Vue\image\image-20221020154547633.png)

#### 1.5.3. 映射使用

* option api

  ```html
  <template>
    <div class="app">
      <button @click="changeName('王小波')">修改name</button>
      <button @click="incrementLevel">递增level</button>
      <button @click="changeInfo({ name: '王二', level: 200 })">修改info</button>
      <h2>Store Name: {{ $store.state.name }}</h2>
      <h2>Store Level: {{ $store.state.level }}</h2>
    </div>
  </template>
  
  <script>
    import { mapMutations } from 'vuex'
    import { CHANGE_INFO } from "@/store/mutation_types"
  
    export default {
      computed: {
      },
      methods: {
        btnClick() {
          console.log("btnClick")
        },
        // ...mapMutations(["changeName", "incrementLevel", CHANGE_INFO])
      }
    }
  </script>
  ```

  

* composition api

  ```html
  <script setup>
  
    import { mapMutations, useStore } from 'vuex'
    import { CHANGE_INFO } from "@/store/mutation_types"
  
    const store = useStore()
  
    // 1.手动的映射和绑定
    const mutations = mapMutations(["changeName", "incrementLevel", CHANGE_INFO])
    const newMutations = {}
    Object.keys(mutations).forEach(key => {
      newMutations[key] = mutations[key].bind({ $store: store })
    })
    const { changeName, incrementLevel, changeInfo } = newMutations
  
  </script>
  
  ```

  

* Mutation常量类型

  ![image-20221020154654058](F:\Vue\image\image-20221020154654058.png)

#### 1.5.4. 重要原则

* 不要在mutation执行异步操作, 必须同步操作
  *  这是因为devtool工具会记录mutation的日记；
  * 每一条mutation被记录，devtools都需要捕捉到前一状态和后一状态的快照；
  * 但是在mutation中执行异步操作，就无法追踪到数据的变化；





### 1.6. 核心概念四Actions

* Action类似于mutation，不同在于：
  * Action提交的是mutation，而不是直接变更状态；
  * Action可以包含任意异步操作；
* 参数context
  *  context是一个和store实例均有相同方法和属性的context对象；
  * 可以从其中获取到commit方法来提交一个mutation，或者通过 context.state 和 context.getters 来获取 state 和 getters；



#### 1.6.1 基本使用

```html
<template>
  <div class="home">
    <h2>当前计数: {{ $store.state.counter }}</h2>
    <button @click="counterBtnClick">发起action修改counter</button>
    <h2>name: {{ $store.state.name }}</h2>
    <button @click="nameBtnClick">发起action修改name</button>
  </div>
</template>

<script>

  export default {
    methods: {
      counterBtnClick() {
        this.$store.dispatch("incrementAction")
      },
      nameBtnClick() {
        this.$store.dispatch("changeNameAction", "aaa")
      }
    }
  }
</script>
```

![image-20221020160724032](F:\Vue\image\image-20221020160724032.png)

#### 1.6.2映射使用

* option api

  ```js
  <script>
    import { mapActions } from 'vuex'
  
    export default {
      methods: {
        // counterBtnClick() {
        //   this.$store.dispatch("incrementAction")
        // },
        // nameBtnClick() {
        //   this.$store.dispatch("changeNameAction", "aaa")
        // }
        // ...mapActions(["incrementAction", "changeNameAction"])
      }
    }
  </script>
  ```

  

* composition api

  ```js
  <script setup>
  
    import { useStore, mapActions } from 'vuex'
  
    const store = useStore()
  
    // 1.在setup中使用mapActions辅助函数
    // const actions = mapActions(["incrementAction", "changeNameAction"])
    // const newActions = {}
    // Object.keys(actions).forEach(key => {
    //   newActions[key] = actions[key].bind({ $store: store })
    // })
    // const { incrementAction, changeNameAction } = newActions
  
    // 2.使用默认的做法
    function increment() {
      store.dispatch("incrementAction")
    }
  
  </script>
  ```

![image-20221020160714907](F:\Vue\image\image-20221020160714907.png)

#### 1.6.3 发送网络请求  （异步操作）

> Action 通常是异步的，可以通过让action返回Promise，在Promise的then中来处理完成后的操作；

```html
<template>
  <div class="home">
    <h2>Home Page</h2>
    <ul>
      <template v-for="item in $store.state.banners" :key="item.acm">
        <li>{{ item.title }}</li>
      </template>
    </ul>
  </div>
</template>

<script>
</script>

<script setup>

  import { useStore } from 'vuex'

  // 告诉Vuex发起网络请求
  const store = useStore()
  store.dispatch("fetchHomeMultidataAction").then(res => {
    console.log("home中的then被回调:", res)
  })

</script>
```

```js
 actions: {
    incrementAction(context) {
      // console.log(context.commit) // 用于提交mutation
      // console.log(context.getters) // getters
      // console.log(context.state) // state
      context.commit("increment")
    },
    changeNameAction(context, payload) {
      context.commit("changeName", payload)
    },
    fetchHomeMultidataAction(context) {
      // 1.返回Promise, 给Promise设置then
      // fetch("http://123.207.32.32:8000/home/multidata").then(res => {
      //   res.json().then(data => {
      //     console.log(data)
      //   })
      // })
      
      // 2.Promise链式调用
      // fetch("http://123.207.32.32:8000/home/multidata").then(res => {
      //   return res.json()
      // }).then(data => {
      //   console.log(data)
      // })
      return new Promise(async (resolve, reject) => {
        // 3.await/async
        const res = await fetch("http://123.207.32.32:8000/home/multidata")
        const data = await res.json()
        
        // 修改state数据
        context.commit("changeBanners", data.data.banner.list)
        context.commit("changeRecommends", data.data.recommend.list)

        resolve("aaaaa")
      })
    }
  }
```

![image-20221020160809048](F:\Vue\image\image-20221020160809048.png)



### 1.7. 核心概念五Modules

![image-20221020161423522](F:\Vue\image\image-20221020161423522.png)



#### 1.7.1 什么是Module？

* 由于使用单一状态树，应用的所有状态会集中到一个比较大的对象，当应用变得非常复杂时，store 对象就有可能变得相当臃肿；
* 为了解决以上问题，Vuex 允许我们将 store 分割成模块（module）；
* 每个模块拥有自己的 state、mutation、action、getter、甚至是嵌套子模块；



#### 1.7.2 基本使用

![image-20221020161804455](F:\Vue\image\image-20221020161804455.png)



#### 1.7.3 局部状态

![image-20221020162759789](F:\Vue\image\image-20221020162759789.png)



#### 1.7.4 module的命名空间

1. 默认情况下，模块内部的action和mutation仍然是注册在全局的命名空间中的：

   * 这样使得多个模块能够对同一个 action 或 mutation 作出响应；
   * Getter 同样也默认注册在全局命名空间；
   * namespaced: true
   * getters["home/xxx"]
   * commit("home/xxx")

   ```html
   <template>
     <div class="home">
       <h2>Home Page</h2>
       <!-- 1.使用state时, 是需要state.moduleName.xxx -->
       <h2>Counter模块的counter: {{ $store.state.counter.count }}</h2>
       <!-- 2.使用getters时, 是直接getters.xxx -->
       <h2>Counter模块的doubleCounter: {{ $store.getters["counter/doubleCount"] }}</h2>
   
       <button @click="incrementCount">count模块+1</button>
     </div>
   </template>
   
   <script>
       const counter = {
     namespaced: true,
     state: () => ({
       count: 99
     }),
     mutations: {
       incrementCount(state) {
         console.log(state)
         state.count++
       }
     },
     getters: {
       doubleCount(state, getters, rootState) {
         return state.count + rootState.rootCounter
       }
     },
     actions: {
       incrementCountAction(context) {
         context.commit("incrementCount")
       }
     }
   }
   
   </script>
   ```

   

2. 希望模块具有更高的封装度和复用性，可以添加 namespaced: true 的方式使其成为带命名空间的模块：

   * 当模块被注册后，它的所有 getter、action 及 mutation 都会自动根据模块注册的路径调整命名；

![image-20221020163147947](F:\Vue\image\image-20221020163147947.png)

![image-20221020163158653](F:\Vue\image\image-20221020163158653.png)



#### 1.7.5 module修改或派发根组件

在action中修改root中的state

![image-20221020164207146](F:\Vue\image\image-20221020164207146.png)





