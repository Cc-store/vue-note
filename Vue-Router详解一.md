



## 一. Vue-Router

### 1.1. 前端路由发展历程

* 后端路由

![image-20221019144045308](F:\Vue\image\image-20221019144045308.png)

* 前后端分离

![image-20221019144246731](F:\Vue\image\image-20221019144246731.png)

* 单页面富应用
  * SPA: single page web application  ：  最主要的特点就是在**前后端分离的基础上加了一层前端路由.**
  * 前端路由  ： 一套路由规则.



### 1.2. 改变URL, 页面不刷新的两种模式

* hash模式 

  *  URL的hash也就是锚点(#), 本质上是改变window.location的href属性；
  * 可以通过直接赋值location.hash来改变href, 但是页面不发生刷新

  ```js
  // 指定采用的模式: hash
    history: createWebHashHistory()
  ```

  ![image-20221019153216374](F:\Vue\image\image-20221019153216374.png)



* history模式

  > history接口是HTML5新增的, 它有六种模式改变URL而不刷新页面：

  ```js
  history: createWebHistory()
  ```

  

  * replaceState：替换原来的路径；
  * pushState：使用新的路径；
  * popState：路径的回退；
  * go：向前或向后改变路径；
  * forward：向前改变路径；
  * back：向后改变路径；

![image-20221019153225136](F:\Vue\image\image-20221019153225136.png)

### 1.3. Vue-Router的使用过程

* 安装:
  * npm install vue-router
* 使用:
  * 创建router对象
    * createRouter
    * routes: 映射关系
    * history: createWebHashHistory()
  * app.use(router)
  * 使用路径:
    * router-view: 占位
    * router-link
      * 编程式导航

```js
import { createRouter, createWebHashHistory, createWebHistory } from 'vue-router'

// import Home from '../Views/Home.vue' //导入创建的组件
// import About from '../Views/About.vue'

// 路由的懒加载
// const Home = () => import(/* webpackChunkName: 'home' */"../Views/Home.vue")
// const About = () => import(/* webpackChunkName: 'about' */"../Views/About.vue")

// 创建一个路由: 映射关系
const router = createRouter({ //创建router对象
  // 指定采用的模式: hash
  history: createWebHashHistory(),
  // history: createWebHistory(),
  // 映射关系
  routes: [//配置路由的映射
    { 
      path: "/", 
      redirect: "/home" 
    },
    {  // {path: "/home", component: Home}//配置路由的映射
      name: "home",
      path: "/home", 
      component: () => import("../Views/Home.vue"),
      meta: {
        name: "why",
        age: 18
      },
      children: [
        // {
        //   path: "/home",
        //   // redirect: "/home/recommend"
        // },
        {
          path: "recommend", // /home/recommend
          component: () => import("../Views/HomeRecommend.vue")//路由的懒加载
        },
        {
          path: "ranking", // /home/ranking
          component: () => import("../Views/HomeRanking.vue")
        }
      ]
    },
    {
      // abc/cba/nba
      path: "/:pathMatch(.*)*",
      component: () => import("../Views/NotFound.vue")
    }
  ]
})
export default router
```



```html
import router from './router'
const app = createApp(App) //createApp(App).use(router).mount('#app')
app.use(router)
app.mount('#app')

<template>
  <div class="app">
    <h2>App Content</h2>
       <router-link to="/hone"> 首页</router-link>
       <router-link to="/about"> 关于</router-link>
    <router-view></router-view> //占位符
  </div>
</template>
```



### 1.4. Vue-Router知识点补充

#### 1.4.1. 默认路径

* path -> redirect
  *  path配置的是 **根路径: /**
  *  redirect是重定向, 也就是我们将根路径重定向到/home的路径下, 默认跳到到首页

```js
{ 
	path: "/", 
	redirect: "/home" 
},
```





#### 1.4.2. history模式

* createWebHistory()





#### 1.4.3. router-link其他属性

* **to**  :  是一个字符串，或者是一个对象
* **replace**  :   设置 replace 属性的话，当点击时，会调用 router.replace()，而不是 router.push()；
* **active-class**  :  设置激活a元素后应用的class，默认是router-link-active (鼠标选中哪个元素就会添加样式)
* **exact-active-class**  ：  链接精准激活时，应用于渲染的 <a> 的 class，默认是router-link-exact-active；（要精准定位到某个组件）

router.push : 跳转到指定URL，向history栈添加一个新的纪录，点击后退会返回至上一个页面

router.replace : 跳转到指定的URL，替换history栈中最后一个记录，点击后退会返回至上一个页面。（A----->B----->C 结果B被C替换 A----->C）



#### 1.4.4. 路由懒加载-分包处理

> 把不同路由对应的组件分割成不同的代码块，然后当路由被访问的时候才加载对应组件，这样就会更加高效；也可以提高首屏的渲染效率

因为component可以传入一个组件，也可以接收一个函数，该函数 需要返回一个Promise， 而import函数就是返回一个Promise；

```js
{
          path: "/home",
          redirect: "/home/recommend"
        },
        {
          path: "recommend", // /home/recommend
          component: () => import("../Views/HomeRecommend.vue")
        },
        {
          path: "ranking", // /home/ranking
          component: () => import("../Views/HomeRanking.vue")
}


// 路由的懒加载  webpack从3.x开始支持对分包进行命名（chunk name）
// const Home = () => import(/* webpackChunkName: 'home' */"../Views/Home.vue")
// const About = () => import(/* webpackChunkName: 'about' */"../Views/About.vue")

```

![image-20221019162623122](F:\Vue\image\image-20221019162623122.png)

#### 1.4.5. 其他属性

* name ： 路由记录独一无二的名称；
* meta ：  自定义的数据
  * route.meta

```js
{ 
      name: "home",
      path: "/home", 
      component: () => import("../Views/Home.vue"),
      meta: {
        name: "why",
        age: 18
      },
}
```



### 1.5. 动态路由使用

> 在Vue Router中，我们可以在路径中使用一个动态字段来实现，我们称之为 路径参数；

* path: `/user/:id`

```js
 {
      path: "/user/:id",
      component: () => import("../Views/User.vue")
},
   //router-link 进行如下跳转
   <router-link to="/user/123">用户123</router-link>
   <router-link to="/user/321">用户321</router-link>
```



#### 1.5.1获取动态路由的值

* 在template中，直接通过 $route.params获取值；
* 在created中，通过 this.$route.params获取值；
* 在setup中，我们要使用 vue-router库给我们提供的一个hook useRoute；
  * 该Hook会返回一个Route对象，对象中保存着当前路由相关的值；

```html
<template>
  <div class="user">
    <!-- 在模板中获取到id -->
    <h2>User: {{ $route.params.id }}</h2>
    
  </div>


  
</template>

<script setup>
  import { useRoute, onBeforeRouteUpdate } from 'vue-router'

  const route = useRoute()
  console.log(route)//proxy
  console.log(route.params.id)//123 321

  // 获取route跳转id
  onBeforeRouteUpdate((to, from) => {
    console.log("from:", from.params.id)//123 321
    console.log("to:", to.params.id)//123 321
  })

</script>
```

![image-20221019203100522](F:\Vue\image\image-20221019203100522.png)

### 1.6. NotFound页面匹配

> 可编写一个动态路由用于匹配所有的页面, ( 没有匹配到的路由 )；

* path: `/:pathMatch(.*)*`

```html
<template>
  <div class="not-found">
    <h2>NotFound: 您当前的路径{{ $route.params.pathMatch }}不正确, 请输入正确的路径!</h2>
  </div>
</template>


<script>
    {
      // abc/cba/nba
      path: "/:pathMatch(.*)*", //  path: "/:pathMatch(.*)"
      component: () => import("../Views/NotFound.vue")
	}
</script>
```



**它们的区别在于解析的时候，是否解析 /：**

![image-20221019203412661](F:\Vue\image\image-20221019203412661.png)



### 1.7. 路由的嵌套使用

* 1.在一层路由中添加 children属性:
  * { path: "recommend", component: () => import("../views/homerecomend.vue") }
* 2.在Home组件中添加 `<router-view>`

* 3.路径跳转 `<router-link>`

```html
<script>
     { 
      name: "home",
      path: "/home", 
      component: () => import("../Views/Home.vue"),
      meta: {
        name: "why",
        age: 18
      },
      children: [
        {
          path: "/home",
          redirect: "/home/recommend"
        },
        {
          path: "recommend", // /home/recommend
          component: () => import("../Views/HomeRecommend.vue")
        },
        {
          path: "ranking", // /home/ranking
          component: () => import("../Views/HomeRanking.vue")
        }
      ]
    }
</script>
<template>
  <div class="home">
    <h2>Home</h2>
    <div class="home-nav">
      <router-link to="/home/recommend">推荐</router-link>
      <router-link to="/home/ranking">排行</router-link>
    </div>

    <button @click="logoutClick">退出登录</button>

    <!-- 占位组件 -->
    <router-view></router-view>
  </div>
</template>
```



### 1.8. 编程式导航

#### 1.8.1. 跳转的方式

* push(路径)

  ```js
   // 监听元素的点击
    function homeSpanClick() {
      // 跳转到首页
      // router.push("/home")
      router.push({
        // name: "home"
        path: "/home"
      })
    }
  ```

  

* push({ path/query })

  ```js
  function aboutBtnClick() {
      // 跳转到关于
      router.push({
        path: "/about",
        query: {
          name: "why",
          age: 18
        }
      })
    }
  ```

  

* replace()

  ```js
  const router = useRouter()
  const jumpToProfile = () => {
      router.replace('/about')
  }
  ```

  

* query方式的参数

  ```js
   function aboutBtnClick() {
      // 跳转到关于
      router.push({
        path: "/about",
        query: {
          name: "why",
          age: 18
        }
      })
    }
  ```

* 在界面中通过 $route.query 来获取参数：

  ![image-20221019204744461](F:\Vue\image\image-20221019204744461.png)



* 替换当前的位置

  使用push的特点是压入一个新的页面，那么在用户点击返回时，上一个页面还可以回退，但是如果我们希望当前页面是一个替换操作，那么可以使用replace：

| 声明式                          | 编程式              |
| :------------------------------ | :------------------ |
| <router-link :to="..." replace> | router.replace(...) |



#### 1.8.2. 路径的切换

* back()

  调用 history.back() 回溯历史。相当于 router.go(-1)；

* forward()

  调用 history.forward() 在历史中前进。相当于 router.go(1)；

* go(number)

  ![image-20221019204959638](F:\Vue\image\image-20221019204959638.png)



