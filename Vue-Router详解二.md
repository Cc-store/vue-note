

## 一. VueRouter

### 1.1. 动态管理路由

#### 1.1.1. 什么情况下需要动态管理路由

* 前端都会开发哪些项目;
* 后台管理系统





#### 1.1.2. 动态添加路由

* router.addRoute({})

  ```js
  router.addRoute({
      path: "/admin",
      component: () => import("../Views/Admin.vue")
    })
  ```



* router.addRoute("name", {})  ：  为route添加一个children路由

  ```js
  const homeMomentRoute = {
    path: "/moment",
    component: () => import("../Views/moment.vue")
  }
  router.addRoute('home',homeMomentRoute)
  ```

  



#### 1.1.3. 管理路由别的方法

* 移除

  ![image-20221019210505031](F:\Vue\image\image-20221019210505031.png)

  

* router.hasRoute()：检查路由是否存在。

*  router.getRoutes()：获取一个包含所有路由记录的数组。

  



### 1.2. 路由导航守卫

> 提供的导航守卫主要用来通过跳转或取消的方式守卫导航。

#### 1.2.1. beforeEach

* 跳转order时, 判断用户是否登录
* 没有登录:
  * 调到登录页面;
  * 进行登录的操作;
  * 保存了token
* 登录成功:
  * 调到order页面;

```js
// 2.路由导航守卫
// 进行任何的路由跳转之前, 传入的beforeEach中的函数都会被回调
// 需求: 进入到订单(order)页面时, 判断用户是否登录(isLogin -> localStorage保存token)
// 情况一: 用户没有登录, 那么跳转到登录页面, 进行登录的操作
// 情况二: 用户已经登录, 那么直接进入到订单页面
router.beforeEach((to, from) => {
  // 1.进入到任何别的页面时, 都跳转到login页面
  // if (to.path !== "/login") {
  //   return "/login"
  // }

  // 2.进入到订单页面时, 判断用户是否登录
  const token = localStorage.getItem("token")
  if (to.path === "/order" && !token) {
    return "/login"
  }
})



  function loginClick() {
    // 向服务器发送请求, 服务器会返回token
    localStorage.setItem("token", "coderwhy")

    // 跳转到order页面
    router.push("/order")
  }
```



* 全局的前置守卫beforeEach是在导航触发时会被回调的：
* 它有两个参数：
  * **to**：即将进入的路由Route对象；
  * **from**：即将离开的路由Route对象；
* 它有返回值：
  * **false**：取消当前导航；
  * **不返回或者undefined**：进行默认导航；
  * **返回一个路由地址**：
    ✓ 可以是一个string类型的路径；
    ✓ 可以是一个对象，对象中包含path、query、params等信息；
* 可选的第三个参数：next（不推荐使用）
  * 在Vue2中我们是通过next函数来决定如何进行跳转的；
  * 但是在Vue3中我们是通过返回值来控制的，不再推荐使用next函数，这是因为开发中很容易调用多次next；



![image-20221019211237423](F:\Vue\image\image-20221019211237423.png)



#### 1.2.2. 路由导航的流程解析

* Vue还提供了很多的其他守卫函数，目的都是在某一个时刻给予我们回调，让我们可以更好的控制程序的流程或者功能：
  https://next.router.vuejs.org/zh/guide/advanced/navigation-guards.html

* 完整的导航解析流程：

  ![image-20221019211314114](F:\Vue\image\image-20221019211314114.png)



![image-20221019212153129](F:\Vue\image\image-20221019212153129.png)

















