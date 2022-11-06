# Vue基础 – v-model表单





### 1.2. 列表的选择(点击一项, 变成选中状态)

* currentIndex记录点击
  * 利用点击时获得的索引 和 currentIndex 实现选中元素的状态选择



## 二. v-model双向绑定

### 2.1. v-model基本使用

* input手动双向绑定

  ```html
   <!-- 1.手动的实现了双向绑定 -->
      <input type="text" :value="message" @input="inputChange">
  <script>
      // 1.创建app
      const app = Vue.createApp({
        // data: option api
        data() {
          return {
            message: "Hello Model",
            account: "",
            password: ""
          }
        },
        methods: {
          inputChange(event) {
            this.message = event.target.value
          }
        }
      })
  
      // 2.挂载app
      app.mount("#app")
    </script>
  ```

  

* v-model

  ```html
  <!-- 2.v-model实现双向绑定 -->
      <input type="text" v-model="message">
  
  ```

  

* 原理

  *  **v-bind绑定value属性的值**；
  * **v-on绑定input事件**监听到函数中，函数会获取最新的值赋值到绑定的属性中；

```html
<input v-model="searchText">
===
<input :value="searchText" @input="searchText = $event.target.value">
```



* 基本使用
  *  **v-model指令**可以在表单 input、textarea以及select元素上创建**双向数据绑定；**
  * 会根据**控件类型**自动选取正确的方法来更新元素
  *  **v-model 本质上不过是语法糖，它负责监听用户的输入事件来更新数据**，并在某种极端场景下进行一些特殊处理；

![image-20221013154751298](F:\Vue\image\image-20221013154751298.png)



### 2.2. v-model其他类型

* textarea

  ```html
   <div id="app">
      <textarea cols="30" rows="10" v-model="content"></textarea>
  
      <p>输入的内容: {{content}}</p>
    </div>
  ```

  ![image-20221013155432855](F:\Vue\image\image-20221013155432855.png)

* checkbox
  * 单选
  
    *  v-model即为**布尔值**。
    * 此时input的value属性并不影响v-model的值。
  
    ```html
     <!-- 1.checkbox单选框: 绑定到属性中的值是一个Boolean -->
        <label for="agree">
          <input id="agree" type="checkbox" v-model="isAgree"> 同意协议
        </label>
        <h2>单选框: {{isAgree}}</h2>
        <hr>
    ```
  
    
  
  * 多选
  
    * 当是**多个复选框**时，因为可以选中多个，所以对应的**data中属性是一个数组。**
    * 当选中某一个时，就会将input的value添加到数组中
  
    ```html
     <!-- 2.checkbox多选框: 绑定到属性中的值是一个Array -->
        <!-- 注意: 多选框当中, 必须明确的绑定一个value值 -->
        <div class="hobbies">// hobbies: []
          <h2>请选择你的爱好:</h2>
          <label for="sing">
            <input id="sing" type="checkbox" v-model="hobbies" value="sing"> 唱
          </label>
          <label for="jump">
            <input id="jump" type="checkbox" v-model="hobbies" value="jump"> 跳
          </label>
          <label for="rap">
            <input id="rap" type="checkbox" v-model="hobbies" value="rap"> rap
          </label>
          <label for="basketball">
            <input id="basketball" type="checkbox" v-model="hobbies" value="basketball"> 篮球
          </label>
          <h2>爱好: {{hobbies}}</h2>
        </div>
    
    ```

![image-20221013160244391](F:\Vue\image\image-20221013160244391.png)



* radio

  ```html
   <div class="gender">
        <label for="male">
          <input id="male" type="radio" v-model="gender" value="male"> 男
        </label>
        <label for="female">
          <input id="female" type="radio" v-model="gender" value="female"> 女
        </label>
        <h2>性别: {{gender}}</h2>
      </div>
  ```

  ![image-20221013160545635](F:\Vue\image\image-20221013160545635.png)

* select
  * 单选  ： 只能选中一个值
  
    *  v-model绑定的是一个值；
    * 当我们选中option中的一个时，会将它对应的value赋值到fruit中
  
    ```html
      <!-- select的单选 -->
        <select v-model="fruit">// fruit: "orange"
          <option value="apple">苹果</option>
          <option value="orange">橘子</option>
          <option value="banana">香蕉</option>
        </select>
        <h2>单选: {{fruit}}</h2>
    ```
  
    
  
  * 多选 ： 可以选中多个值
  
    *  v-model绑定的是一个数组；
    * 当选中多个值时，就会将选中的option对应的value添加到数组fruit中；
  
    ```html
     <!-- select的多选 -->
        <select multiple size="3" v-model="fruits">//fruits: []
          <option value="apple">苹果</option>
          <option value="orange">橘子</option>
          <option value="banana">香蕉</option>
        </select>
        <h2>多选: {{fruits}}</h2>
    ```
  
    ![image-20221013160650317](F:\Vue\image\image-20221013160650317.png)



### 2.3. v-model值绑定

```html
 <div id="app">
    <!-- 1.select的值绑定 -->
    <select multiple size="3" v-model="fruits">
      <option v-for="item in allFruits" 
              :key="item.value" 
              :value="item.value">
        {{item.text}}
      </option>
    </select>
    <h2>多选: {{fruits}}</h2>

    <hr>

    <!-- 2.checkbox的值绑定 -->
    <div class="hobbies">
      <h2>请选择你的爱好:</h2>
      <template v-for="item in allHobbies" :key="item.value">
        <label :for="item.value">
          <input :id="item.value" type="checkbox" v-model="hobbies" :value="item.value"> {{item.text}}
        </label>
      </template>
      <h2>爱好: {{hobbies}}</h2>
    </div>
  </div>
```

```html
<script>
	 return {
          // 水果
          allFruits: [
            { value: "apple", text: "苹果" },
            { value: "orange", text: "橘子" },
            { value: "banana", text: "香蕉" },
          ],
          fruits: [],

          // 爱好
          allHobbies: [
            { value: "sing", text: "唱" },
            { value: "jump", text: "跳" },
            { value: "rap", text: "rap" },
            { value: "basketball", text: "篮球" }
          ],
          hobbies: []
        }
</script>
```



### 2.4. v-model修饰符

* lazy 

  * **默认情况下**，会在每次内容输入后就将最新的值和绑定的属性进行同步；

  * **lazy修饰符**，会将绑定的事件切换为 **change 事件**，只有在提交时（比如回车）才会触发

* number

  *  message总是string类型，即使在我们设置type为number也是string类型；
  * 希望转换为数字类型，那么可以使用 .number 修饰符：

* trim

  * 自动过滤用户输入的守卫空白字符，可以给v-model添加 trim 修饰符：

```html
  <!-- 1.lazy: 绑定change事件  -->
    <input type="text" v-model.lazy="message">
    <h2>message: {{message}}</h2>

    <hr>

    <!-- 2.number: 自动将内容转换成数字 -->
    <input type="text" v-model.number="counter">
    <h2>counter:{{counter}}-{{typeof counter}}</h2>

    <input type="number" v-model="counter2">
    <h2>counter2:{{counter2}}-{{typeof counter2}}</h2>

    <hr>

    <!-- 3.trim: 去除收尾的空格 -->
    <input type="text" v-model.trim="content">
    <h2>content: {{content}}</h2>

    <hr>

    <!-- 4.使用多个修饰符 -->
    <input type="text" v-model.lazy.trim="content">
    <h2>content: {{content}}</h2>
```



## 三. 组件化的基础

### 3.1. 组件化的思想

* 我们将一个完整的页面分成很多个组件；
* 每个组件都用于实现页面的一个功能块；
* 而每一个组件又可以进行细分；
* 而组件本身又可以在多个地方进行复用；

**createApp函数传入了一个对象App，这个对象其实本质上就是一个组件，也是我们应用程序的根组件；**

```html
<script>
// 1.组件: App组件(根组件)
    const App = {
      data() {
        return {
          message: "Hello Vue"
        }
      }
    }

    // 1.创建app
    const app = Vue.createApp(App)

    // 2.挂载app
    app.mount("#app")
</script>
```

![image-20221013164954155](F:\Vue\image\image-20221013164954155.png)



### 3.2. 注册全局组件

```js
 /*
      1.通过app.component(组件名称, 组件的对象)
      2.在App组件的模板中, 可以直接使用product-item的组件
    */

    // 1.组件: App组件(根组件)
    const App = {}

    // 2.创建app
    const app = Vue.createApp(App)

    // 3.注册一个全局组件
    // product-item全局组件
     // 全局组件的特点: 一旦注册成功后, 可以在任意其他组件的template中使用
    app.component("product-item", {
      template: "#item"
    })
```



![image-20221013170428576](F:\Vue\image\image-20221013170428576.png)



### 3.3. 注册局部组件

```js
const App = {
  components: {
      ProductItem,
        HomeNav: {
          template: "#nav",
          components: {
            ProductItem
          }
     }
  }
}
```



### 3.4.组件逻辑

```js
  app.component("product-item", {
      template: "#product",
      data() {
        return {
          title: "我是商品Item",
          price: 9.9
        }
      },
      methods: {
        favarItem() {
          console.log("收藏了当前的item")
        }
      }
    })
```



### 3.5. 组件的名称

* 使用kebab-case（短横线分割符）
  * 当使用 kebab-case (短横线分隔命名) 定义一个组件时，你也必须在引用这个自定义元素时使用 kebab-case，例如 <my-component-name>；

* 使用PascalCase（驼峰标识符）
  * 当使用 PascalCase (首字母大写命名) 定义一个组件时,<my-component-name> 和 <MyComponentName> 都是可接受的

**components选项对应的是一个对象，对象中的键值对是 组件的名称: 组件对象；**

![image-20221013170747278](F:\Vue\image\image-20221013170747278.png)



## 四. Vue脚手架

### 4.1. Vue的开发模式

* SFC（ single-file components (单文件组件) ）
* .vue文件



### 4.2. Vue CLI安装和使用

![image-20221013171159058](F:\Vue\image\image-20221013171159058.png)



### 4.3. Vue项目目录结构

![image-20221013171039306](F:\Vue\image\image-20221013171039306.png)



### 4.4. browserslistrc文件作用(了解)





### 4.5. 从main.js入口开始, 如何一步步创建自己的组件

* App.vue
* ProductItem.vue



### Vue Cli的运行原理

![image-20221013171400884](F:\Vue\image\image-20221013171400884.png)











