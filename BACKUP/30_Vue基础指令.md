# [Vue基础指令](https://github.com/Twlig/issuesBlog/issues/30)

Vue会自动通过状态来生成DOM，并渲染在网页上，而网页的DOM变化又会反过来更新Vue的数据状态。

### Vue实例

每个Vue应用都是通过Vue函数创建Vue实例

```javascript
var vm = new Vue({
    el: '#app',
    data() {
        return {
            name: 'zzy',
            age: 22
        }
    },
    computed: {
        year: function(oldV, newV) {
            return oldV + newV
        }
    },
    methods: {
        sayName() {
            console.log(this.name)
        },
        func2() {}
    },
    mounted() {
        
    },
    components: {
        A,
        B
    }
})
```

- el

  Vue实例挂载DOM。本例中Vue实例挂载到id为app的DOM元素上。

- data

  响应式数据。data 对象中的所有的 property 都会加入到 Vue 的响应式系统中。当这些 property 的值发生改变时，视图将会产生“响应”，DOM渲染新的值。此处，data采用函数式写法，还可以采用类似computed的对象式写法，但是对象式写法存在实例共享问题，这个vue要当作子组件，那它的每个实例的data就会共享。

- computed

  计算属性。当其依赖属性的值发生变化时，这个属性的值会自动更新，与之相关的DOM部分也会同步自动更新。**计算属性是基于它们的响应式依赖进行缓存的**。只在相关响应式依赖发生改变时它们才会重新求值。值没变就会直接从缓存中取原先的值，不需要重新计算。

- methods

  方法。定义各种事件触发之后的处理方法。比如点击事件。

- 生命周期函数

  Vue生命周期钩子函数，在DOM渲染的不同阶段添加自定义代码。总共有8个生命周期钩子函数：beforeCreate，created，beforeMount，mounted，beforUpdate，update，beforeDestroy，destroyed。

- components

  导入子组件。

下面是DOM的写法

```html
<div id="app">
  {{ name }}
</div>
```



### 基础指令

**指令的职责是，当表达式的值改变时，将其产生的连带影响，响应式地作用于 DOM。**

- #### v-if

  条件性渲染。`v-else-if`,`v-else` 元素必须紧跟在带 `v-if` 或者 `v-else-if` 的元素的后面，否则它将不会被识别。**会动态的更新节点。**

  ```html
  <div v-if="type === 'A'">
    A
  </div>
  <div v-else-if="type === 'B'">
    B
  </div>
  <div v-else>
    Not A/B
  </div>
  ```

- #### v-show

  条件性渲染。**不会动态更新节点。**

  ```javascript
  <h1 v-show="ok">Hello!</h1>
  ```

  不同的是带有 `v-show` 的元素始终会被渲染并保留在 DOM 中。`v-show` 只是简单地切换元素的 CSS的display。

- #### v-for

  列表渲染。可以是数组和对象两种。

  1. ##### 数组

      `items` 是源数据数组，而第一个参数 `item` 则是被迭代的数组元素的**别名**，第二个**可选参数**index是当前项的索引的**别名**（从0开始）。of/in都可以。

     ```html
     <ul id="example-2">
       <li v-for="(item, index) in items">
         {{ parentMessage }} - {{ index }} - {{ item.message }}
       </li>
     </ul>
     ```

     ```javascript
     var example2 = new Vue({
       el: '#example-2',
       data: {
         parentMessage: 'Parent',
         items: [
           { message: 'Foo' },
           { message: 'Bar' }
         ]
       }
     })
     ```

  2. ##### 对象

     第一个参数是原数据**对象的值**，第二个可选参数是源数据**对象的属性名**，第三个可选参数是**索引值**

     ```html
     <div v-for="(value, name, index) in object">
       {{ index }}. {{ name }}: {{ value }}
     </div>
     //0.title.Vue
     //1.author.zzy
     //2.publishedAt.3.10
     ```

     ```javascript
     new Vue({
       el: '#v-for-object',
       data: {
         object: {
           title: 'Vue',
           author: 'zzy',
           publishedAt: '3.10'
         }
       }
     })
     ```

- #### v-model 

  双向数据绑定。`<input>`、`<textarea>` 及 `<select>` 元素上创建双向数据绑定。

  ```html
  <input v-model="message" placeholder="edit me">
  <p>Message is: {{ message }}</p>
  ```

  - text 和 textarea 元素使用 `value` property 和 `input` 事件；

    ```html
    <input v-model="message">
    <!--等价于-->
    <input
      v-bind:value="message"
      v-on:input="message = $event.target.value" 
    >
    <!-- 1.绑定输入框value属性值为message。2.通过input输入事件触发，更新message为当前输入框的value值 -->
    ```

  - checkbox 和 radio 使用 `checked` property 和 `change` 事件；

    ```html
    <div id="example-4">
      <input type="radio" id="one" value="One" v-model="picked">
      <label for="one">One</label>
      <br>
      <input type="radio" id="two" value="Two" v-model="picked">
      <label for="two">Two</label>
      <br>
      <span>Picked: {{ picked }}</span>
    </div>
    ```

    等价于: 

    ```html
    <div id="example-4">
      <input name="ck" type="radio" id="one" value="One" v-bind:checked="picked=='One'" v-on:change="picked=$event.target.value">
      <label for="one">One</label>
      <br>
      <input name="ck" type="radio" id="two" value="Two" v-bind:checked="picked=='Two'" v-on:change="picked=$event.target.value">
      <label for="two">Two</label>
      <br>
      <span>Picked: {{ picked }}</span>
    </div>
    ```

    此处添加了name属性，不然点击单选框后，change事件无法再次触发。

  - select 字段将 `value` 作为 property 并将 `change` 作为事件

    ```html
    <div id="example-5">
      <select v-model="selected">
        <option disabled value="">请选择</option>
        <option>A</option>
        <option>B</option>
        <option>C</option>
      </select>
      <span>Selected: {{ selected }}</span>
    </div>
    ```

    等价于

    ```html
    <div id="example-5">
      <select v-bind:value="selected" v-on:change="selected=$event.target.value">
        <option disabled value="">请选择</option>
        <option>A</option>
        <option>B</option>
        <option>C</option>
      </select>
      <span>Selected: {{ selected }}</span>
    </div>
    ```

- #### v-on

  1. 绑定**事件**对应的**事件处理程序**。

     此处是发生click事件，触发MyFunction处理程序

     ```html
     <button v-on:click="MyFunction">click</button>
     ```

  2. 动态绑定事件

     使用方括号，myevent就是一个Vue变量，可以通过Vue动态改变myevent事件，如myevent='click',myevent='blur'等。

     ```html
     <button v-on:[myevent]="MyFunction">event</button>
     ```

  3. 缩写

     v-on还可以简写成`@`

     ```html
     <button @:[myevent]="MyFunction">click</button>
     ```

- #### v-bind

  1. 绑定属性对应的值

     输入框value值绑定Vue变量inputValue。

     ```html
     <input v-bind:value="inputValue">
     ```

  2. 动态属性

     输入框动态属性绑定Vue变量inputValue。使用方括号，inputAtrr就是一个Vue变量，可以通过Vue动态改变要绑定的inputAtrr属性，如inputAtrr='value',inputAtrr='name'等。

     ```html
     <input v-bind:[inputAtrr]="inputValue">
     ```

  3. 缩写

     直接使用`:`

     ```html
     <input :[inputAtrr]="inputValue">
     ```

     

---

### 生命周期钩子函数不能使用箭头函数

生命周期钩子的 `this` 上下文指向调用它的 Vue 实例。

比如：

```javascript
new Vue({
  data: {
    a: 1
  },
  created: function () {
    // `this` 指向 vm 实例
    console.log('a is: ' + this.a)
  }
})
// => "a is: 1"
```

不能在选项 property 或回调上使用箭头函数，比如 `created: () => console.log(this.a)` 或 `vm.$watch('a', newValue => this.myMethod())`。因为**箭头函数的this是指向定义时的上下文对象**，而不是调用函数的对象。而我们想要的this往往是当前实例vm，并通过this去访问vm实例上的属性和方法。

举个例子：

```javascript
window.color = 'red'; 
let o = { 
 	color: 'blue' ,
    sayColor: () => console.log(this.color)
}; 
o.sayColor(); // 'red'
```

可以看到虽然sayColor是定义在对象o上面的，且通过o调用，但是最终this指向的还是window。这和此处的Vue实例类似，虽然created箭头函数是创建在Vue实例上的，但是this指向的不是Vue实例。因此，最好不要使用箭头函数以免this指向不明确。



---

### 生命周期钩子函数不能使用箭头函数

生命周期钩子的 `this` 上下文指向调用它的 Vue 实例。

比如：

```javascript
new Vue({
  data: {
    a: 1
  },
  created: function () {
    // `this` 指向 vm 实例
    console.log('a is: ' + this.a)
  }
})
// => "a is: 1"
```

不能在选项 property 或回调上使用箭头函数，比如 `created: () => console.log(this.a)` 或 `vm.$watch('a', newValue => this.myMethod())`。因为**箭头函数的this是指向定义时的上下文对象**，而不是调用函数的对象。而我们想要的this往往是当前实例vm，并通过this去访问vm实例上的属性和方法。

举个例子：

```javascript
window.color = 'red'; 
let o = { 
 	color: 'blue' ,
    sayColor: () => console.log(this.color)
}; 
o.sayColor(); // 'red'
```

可以看到虽然sayColor是定义在对象o上面的，且通过o调用，但是最终this指向的还是window。这和此处的Vue实例类似，虽然created箭头函数是创建在Vue实例上的，但是this指向的不是Vue实例。因此，最好不要使用箭头函数以免this指向不明确。

