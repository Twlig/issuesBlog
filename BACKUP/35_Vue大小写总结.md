# [Vue大小写总结](https://github.com/Twlig/issuesBlog/issues/35)

产生大小写问题的原因就是HTML 中的 attribute 名是大小写不敏感的，浏览器会把所有大写字符解释为小写字符。但是Vue中大小写是需要一一对应的，因此，存在大小写混乱问题。

### 组件名

1. #### kebab-case(短横线分隔命名)

   - 定义组件

   ```javascript
   Vue.component('my-component-name', { /* ... */ })
   ```

   - 引用组件

   ```html
   <div id="app">
     <my-component-name></my-component-name>
   </div>
   ```

   

2. #### PascalCase(首字母大写命名)

   - 定义组件

   ```javascript
   Vue.component('MyComponentName', { /* ... */ })
   ```

   - 引用这个自定义元素时两种命名法都可以使用。

   ```html
     <my-component-name></my-component-name>
     <MyComponentName></MyComponentName>
   ```

   > **注意**：直接在 DOM (即非字符串的模板）如：在单个组件的`<template></template>`中 或者 index.html中直接CDN引入vue.js的`<div id="app"></div>`中，使用时只有 kebab-case 是有效的。



⭐**建议**：定义时两种方式都可以，引用使用kebab-case。不过为了统一还是**建议定义和引用都用kebab-case**。



### prop

- 定义prop

  只能使用camelCase (驼峰命名法) 

  ```javascript
  Vue.component('blog-post', {
    // 在 JavaScript 中是 camelCase 的
    props: ['postTitle'],
    template: '<h3>{{ postTitle }}</h3>'
  })
  ```

  如果使用kebab-case：会报错，变量名不能使用kebab-case命名。

  ```javascript
  Vue.component('blog-post', {
    // 在 JavaScript 中是 camelCase 的
    props: ['post-title'],
    template: '<h3>{{ post-title }}</h3>'
  })
  ```

- 引用

  只能使用kebab-case格式引用

  ```html
  <!-- 在 HTML 中是 kebab-case 的 -->
  <blog-post post-title="hello!"></blog-post>
  ```



⭐**建议**：定义只能使用camelCase，引用使用kebab-case。



### 事件名

不同于组件和 prop，事件名不存在任何自动化的大小写转换。而是**触发的事件名需要完全匹配监听事件名称**。

1. 触发事件

   此处触发事件名为myEvent

   ```javascript
   this.$emit('myEvent')
   ```

2. 监听事件

   监听事件名为myevent

   ```html
   <!-- 有效果 -->
   <my-component v-on:myevent="doSomething"></my-component>
   ```

   这是因为HTML大小写不敏感，**事件监听器在 DOM 模板中会被自动转换为全小写**，因此只有小写的监听事件名`myevent`可以匹配上,而`myEvent`匹配不上。



⭐**建议**：使用**kebab-case 的事件名**。都写成`my-event`。



### CSS 样式名

CSS property 名可以用驼峰式或短横线分隔 (kebab-case，记得用引号`''`括起来否则会报错) 来命名：

```html
<div v-bind:style="{ color: activeColor, 'font-size': fontSize + 'px' }"></div>
data: {
  activeColor: 'red',
  fontSize: 30
}
```



⭐**建议**：两种写法都可以，但是kebab-case记得用引号`''`括起来。