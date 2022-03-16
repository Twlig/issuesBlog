# [vue性能提升](https://github.com/Twlig/issuesBlog/issues/43)

### vue性能提升

1. ### key

   `key` 的特殊 attribute 主要用在 Vue 的虚拟 DOM 算法，在新旧 nodes 对比时辨识 VNodes。如果不使用 key，Vue 会使用一种最大限度减少动态元素并且尽可能的尝试就地修改/复用相同类型元素的算法。而使用 key 时，它会基于 key 的变化重新排列元素顺序，并且会移除 key 不存在的元素。

   ```html
   <ul>
     <li v-for="item in items" :key="item.id">...</li>
   </ul>
   ```

   当向items中添加元素时，key会避免li标签内部文本的一一替换。而是添加新li并插入相应位置。

   

2. ### v-once

   只渲染元素和组件**一次**。随后的重新渲染，元素/组件及其所有的子节点将被视为静态内容并跳过。这可以用于优化更新性能。

   ```html
   <!-- 单个元素 -->
   <span v-once>This will never change: {{msg}}</span>
   <!-- 有子元素 -->
   <div v-once>
     <h1>comment</h1>
     <p>{{msg}}</p>
   </div>
   <!-- 组件 -->
   <my-component v-once :comment="msg"></my-component>
   <!-- `v-for` 指令-->
   <ul>
     <li v-for="i in list" v-once>{{i}}</li>
   </ul>
   ```

   

3. ### v-pre

   跳过这个元素和它的子元素的编译过程。可以用来显示原始 Mustache 标签（{{}}）。跳过大量没有指令的节点会加快编译。

   ```html
   <p v-per>{{msg}}</p>
   ```
    v-pre 以原始的信息进行显示，跳过编译过程，因为它不编译，所以就直接显示{{msg}}
   

4. ### v-cloak

   这个指令保持在元素上直到关联实例结束编译。和 CSS 规则如 `[v-cloak] { display: none }` 一起用时，这个指令可以隐藏未编译的 Mustache 标签直到实例准备完毕。

   ``` html
   [v-cloak] {
     display: none;
   }
   <div v-cloak>
     {{ message }}
   </div>
   ```

   不会显示，直到编译结束。

