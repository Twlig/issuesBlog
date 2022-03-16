# [Vue的渲染流程（一）：模板编译成渲染函数](https://github.com/Twlig/issuesBlog/issues/41)

## Vue的渲染流程

![image](https://user-images.githubusercontent.com/22440467/158583912-cbfbabeb-efa2-43df-a62a-c13fce8e6e1c.png)



### 模板编译成渲染函数

模板编译分为三部分：

- 将模板解析为AST（抽象语法树）；（**解析器**）
- 遍历AST标记静态节点； （**优化器**）
- 使用AST生成渲染函数 （**代码生成器**）



1. #### 解析器

   解析器分为HTML解析器、文本解析器以及过滤器解析器，最主要的就是HTML解析器。HTML解析器在解析HTML的过程中会不断触发各种钩子函数，包括开始标签钩子函数、结束标签钩子函数、文本钩子函数和注释钩子函数。

   从前向后解析，遇到开始标签触发钩子函数start，把当前构建节点入栈；遇到结束标签触发钩子函数end，从栈中弹出一个节点。

   ```html
   <div>
       <h1>我是zzy</h1>
       <p>今年22岁</p>
   </div>
   ```

   上述模板被解析成AST过程：触发start钩子函数div入栈，触发start钩子函数h1入栈，触发text钩子函数，触发end钩子函数h1出栈，触发start钩子函数p入栈，触发text钩子函数，触发end钩子函数p出栈，触发end钩子函数div出栈。

   构建成一颗AST：三层，div—h1—文本

   ​                                                —p—文本

   

2. #### 优化器

   优化器的作用就是找出静态子树并打上标记。静态子树是指在AST中永远都不会变的节点。例如：一个纯文本节点，而带有变量的文本节点就不是静态子树。

   标记静态子树的好处：

   - 每次重新渲染时，不需要为静态子树创建新节点 （即不为该节点重新生成新的VNode。在重新渲染时会生成新的VNode，如果发现是静态子树，那么除了首次渲染会生成VNode之外，重新渲染时并不会生成新的子树，而是克隆已经存在的静态子树）
   - 在虚拟DOM中patch的过程可以跳过 （对比新旧VNode时，静态节点不需要对比）

   主要分为两步：

   - 先在AST中找出所有静态节点并打上标记
   - 后在AST中找出所有静态根节点并打上标记

   静态根节点是指，该节点下面所有子节点都是静态节点，并且该节点的父级节点是动态节点。

   ```javascript
   {
       type: 1,
       tag: 'p',
       staticRoot: true,
       static: true,
       ...
   }
   ```

   

3. #### 代码生成器

   - **获取代码字符串**

     代码生成器将AST转换成渲染函数中的内容，这个内容被称为“代码字符串”。
     执行渲染函数后会生成虚拟节点，之所以可以生成虚拟节点，是因为代码字符串中有许多函数调用。

     ##### 模板

     ```html
     <p title="nihao" @click="c">1</p>
     ```

     ##### 代码字符串

     ```
     with(this){
         return _c(
             'p',
             {
                 attrs:{"title":"nihao"},
                 on:{"click":c}
             },
             [_v("1")]
         );
     }
     ```

     其中`_c`是createElement函数，`_v`是createTextNode函数，`_s`是toString函数别名。

   - 使用代码字符串

     将代码字符串放到函数里，这个函数就是渲染函数。当渲染函数被导出到外界后，模板编译的任务就完成了。

     ```javascript
     const code = `with(this){return 'Hello!'}`
     const hello = new Function(code)
     hello() //Hello!
     ```

     

   


