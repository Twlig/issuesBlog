# [动态class和style](https://github.com/Twlig/issuesBlog/issues/33)

## 动态class

实现动态class有对象语法和数组语法两种。

- ### 对象语法

  对象内**属性值为true的属性名**会被渲染为元素的class名。

  1. #### 内联模板定义class对象

     ```html
     <div
       class="static"
       v-bind:class="{ active: isActive, 'text-danger': hasError }"
     ></div>
     ```

     上面的语法表示 `active` 这个 class 存在与否将取决于数据 `isActive` 是否为true。`text-danger`存在与否将取决于数据 `hasError` 是否为true。

     当`isActive=true`，`hasError=false` 时，渲染结果如下：

     ```html
     <div class="static active"></div>
     ```

  2. #### data中定义class对象

     ```vue
     <div v-bind:class="classObject"></div>
     data: {  //简写，实际是Vue实例内部的data
       classObject: {
         active: true,
         'text-danger': false //active和'active'一样都会被识别为属性名字符串
       }
     } 
     ```

     渲染结果同上。

  3. #### 计算属性定义class对象

     计算属性可以进行复杂的计算

     ```vue
     <div v-bind:class="classObject"></div>
     data: {
       isActive: true,
       error: null
     },
     computed: {
       classObject: function () {
         return {
           active: this.isActive && !this.error,
           'text-danger': this.error && this.error.type === 'fatal'
         }
       }
     }
     ```

- ### 数组语法

  数组中的元素渲染为元素class名

  1. #### 基础数组

     ```vue
     <div v-bind:class="[activeClass, errorClass]"></div>
     data: {
       activeClass: 'active',
       errorClass: 'text-danger'
     }
     ```

     渲染为：

     ```html
     <div class="active text-danger"></div>
     ```

  2. #### 数组内嵌三元表达式

     ```vue
     <div v-bind:class="[isActive ? activeClass : '', errorClass]"></div>
     ```

     根据isActive是true还是false决定数组的第一个元素是响应式数据activeClass还是''

  3. #### 数组内嵌对象

     ```html
     <div v-bind:class="[{ active: isActive }, errorClass]"></div>
     ```



## 动态style

- ### 对象语法

  1. #### 内联模板定义style

     CSS property 名可以用驼峰式或短横线分隔 (kebab-case，记得用引号`''`括起来否则会报错) 来命名：

     ```vue
     <div v-bind:style="{ color: activeColor, 'font-size': fontSize + 'px' }"></div>
     data: {
       activeColor: 'red',
       fontSize: 30
     }
     ```

  2. #### 在data中定义style对象

     ```vue
     <div v-bind:style="styleObject"></div>
     data: {
       styleObject: {
         color: 'red',
         'font-size': '13px'
       }
     }
     ```

  3. #### 计算属性定义style

     ```vue
     <div v-bind:style="styleObject"></div>
     data: {
       isActive: true
     },
     computed: {
       styleObject: function () {
         return {
           color: this.isActive ? 'red' : 'blue',
           'font-size': this.isActive ? '10px' : '20px'
         }
       }
     }
     ```

     

- ### 数组语法

  `v-bind:style` 的数组语法可以将**多个样式对象**应用到同一个元素上：

  ```html
  <div v-bind:style="[baseStyles, overridingStyles]"></div>
  ```

  