

## 一. axios库

### 1.1. 为什么选择axios

* 在浏览器中发送 XMLHttpRequests 请求
* 在 node.js 中发送 http请求
* 支持 Promise API
* 拦截请求和响应
* 转换请求和响应数据
* 等等



### 1.2. axios发送请求

* get/post/request

  ```js
  // 1.发送request请求
  // axios.request({
  //   url: "http://123.207.32.32:8000/home/multidata",
  //   method: "get"
  // }).then(res => {
  //   console.log("res:", res.data)
  // })
  
  // 2.发送get请求
  // axios.get(`http://123.207.32.32:9001/lyric?id=500665346`).then(res => {
  //   console.log("res:", res.data.lrc)
  // })
  // axios.get("http://123.207.32.32:9001/lyric", {
  //   params: {
  //     id: 500665346
  //   }
  // }).then(res => {
  //   console.log("res:", res.data.lrc)
  // })
  
  
  // 3.发送post请求
  // axios.post("http://123.207.32.32:1888/02_param/postjson", {
  //   name: "coderwhy",
  //   password: 123456
  // }).then(res => {
  //   console.log("res", res.data)
  // })
  
  axios.post("http://123.207.32.32:1888/02_param/postjson", {
    data: {
      name: "coderwhy",
      password: 123456
    }
  }).then(res => {
    console.log("res", res.data)
  })
  ```

  

* config传入

![image-20221021093038298](F:\Vue\image\image-20221021093038298.png)



**常见配置请求**

![image-20221021103545193](F:\Vue\image\image-20221021103545193.png)



### 1.3. axios额外补充

* axios.defaults.baseURL
* axios.defaults.timeout/headers
* axios.all -> 底层是 Promise.all 
  * axios.all([]) 返回的结果是一个数组，使用 axios.spread 可将数组 [res1,res2] 展开为 res1, res2

```js
// 1.baseURL
const baseURL = "http://123.207.32.32:8000"

// 给axios实例配置公共的基础配置
axios.defaults.baseURL = baseURL
axios.defaults.timeout = 10000
axios.defaults.headers = {}

// 1.1.get: /home/multidata
axios.get("/home/multidata").then(res => {
  console.log("res:", res.data)
})

// 1.2.get: /home/data

// 2.axios发送多个请求
// Promise.all
axios.all([
  axios.get("/home/multidata"),
  axios.get("http://123.207.32.32:9001/lyric?id=500665346")
]).then(res => {
  console.log("res:", res)
})

```



### 1.4. axios创建实例

* 为什么需要创建实例
  * 从axios模块中导入对象时, 使用的实例是默认的实例，**某些请求需要使用特定的baseURL或者timeout等，这个时候, 我们就可以创建新的实例, 并且传入属于该实例的配置信息**
* axios.create()

```js

// axios默认库提供给我们的实例对象
axios.get("http://123.207.32.32:9001/lyric?id=500665346")

// 创建其他的实例发送网络请求
const instance1 = axios.create({
  baseURL: "http://123.207.32.32:9001",
  timeout: 6000,
  headers: {}
})

instance1.get("/lyric", {
  params: {
    id: 500665346
  }
}).then(res => {
  console.log("res:", res.data)
})

const instance2 = axios.create({
  baseURL: "http://123.207.32.32:8000",
  timeout: 10000,
  headers: {}
})

```



### 1.5. axios的拦截器

> 拦截每次请求和响应

* axios.interceptors.request.use(请求成功拦截, 请求失败拦截)
*  axios.interceptors.response.use(响应成功拦截, 响应失败拦截)

```js

// 对实例配置拦截器
axios.interceptors.request.use((config) => {
  console.log("请求成功的拦截")
  // 1.开始loading的动画

  // 2.对原来的配置进行一些修改
  // 2.1. header
  // 2.2. 认证登录: token/cookie
  // 2.3. 请求参数进行某些转化

  return config
}, (err) => {
  console.log("请求失败的拦截")
  return err
})

axios.interceptors.response.use((res) => {
  console.log("响应成功的拦截")

  // 1.结束loading的动画

  // 2.对数据进行转化, 再返回数据
  return res.data
}, (err) => {
  console.log("响应失败的拦截:", err)
  return err
})

axios.get("http://123.207.32.32:9001/lyric?id=500665346").then(res => {
  console.log("res:", res)
}).catch(err => {
  console.log("err:", err)
})
```



### 1.6. axios的简洁封装

```js
import axios from 'axios'

class MyRequest {
  constructor(baseURL, timeout=10000) {
    this.instance = axios.create({
      baseURL,
      timeout
    })
  }

  request(config) {
    return new Promise((resolve, reject) => {
      this.instance.request(config).then(res => {
        resolve(res.data)
      }).catch(err => {
        reject(err)
      })
    })
  }

  get(config) {
    return this.request({ ...config, method: "get" })
  }

  post(config) {
    return this.request({ ...config, method: "post" })
  }
}

export default new MyRequest("http://123.207.32.32:9001")


```



```js
import { createApp } from 'vue'
import axios from 'axios'
import App from './App.vue'
import MyRequest from './service'

createApp(App).mount('#app')

MyRequest.request({
  url: "/lyric?id=500665346"
}).then(res => {
  console.log("res:", res)
})

MyRequest.get({
  url: "/lyric",
  params: {
    id: 500665346
  }
}).then(res => {
  console.log("res:", res)
})

```

