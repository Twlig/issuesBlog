# [Node.js模块化](https://github.com/Twlig/issuesBlog/issues/4)

### Node.js简介

- 在**服务器端**运行JavaScript的开放源代码、跨平台JavaScript运行环境

- 采用Google开发的V8引擎运行JavaScript代码，使用**事件驱动**、**非阻塞**和**异步I/O模型**等技术

- 在Node出现之前，JS通常作为**客户端**程序设计语言使用，常在用户的浏览器上运行

  简单来说，Node.js运行在**服务器端**，JS运行在**客户端**，Node.js把战场从浏览器扩大到服务器。

### Node.js模块化

​	JavaScript 是一个强大面向对象语言，它有很多快速高效的解释器。然而， JavaScript标准定义的 API 是为了构建基于浏览器的应用程序。JavaScript没有模块系统，标准库较少，没有标准接口，缺乏管理系统。CommonJS 规范的提出,弥补了当前 JavaScript 没有标准的缺陷。

在Node.js中，应用由模块组成，采用commonJS模块规范：

- 一个js文件就是一个模块
- 每个模块都是一个独立的作用域，在这个而文件中定义的变量、函数、对象都是私有的，对其他文件不可见
- 模块需像外部暴露属性或者方法。每一个js文件中的js代码都是独立运行在一个函数中，而不是全局作用域，所以一个模块的中的变量和函数在其他模块中无法访问，需要主动暴露内容，其他模块才能访问

 CommonJS对模块的定义十分简单︰

- 模块定义

  就是一个js文件，采用module.exports导出模块信息，如：`module.exports.name = "zzy"`，采用exports也可以。如：`exports.name = "zzy"`

- 模块引用  

  采用 require引用模块，如：`require(fs)`表示在当前文件中引入fs模块

- 模块标识

  引用模块之后会返回引用的模块信息，自定义标识以供后续使用。如：

  `var fs = require("fs")`

  `var data = fs.readFileSync('input.txt')`

#### Node.js模块分类

- 核心模块

  由 node 本身提供，不需要单独安装（npm）

  **使用方法**：可直接引入使用，如：

  `var http = require("http")`

- 第三方模块

  由社区或个人提供，需要通过npm安装后使用，安装后在node_modules文件夹中

  **安装**：`npm install express --save` （安装express，并写入package.json中的依赖）

  **使用方法**：`var express = require("express")`

- 自定义模块

  由我们自己创建，比如：tool.js 、 user.js

  **使用方法**：`var tool = require("./tool.js")`

  注意：就算被引模块和当前js文件是同一目录下，也需要使用`./`相对路径，不能省略。

#### 自定义模块案例

- 新建test.js和testArg.js文件

- 模块文件testArg.js写入如下内容：

  ``` var age = 20
  var name = "zzy"
  module.exports.age = age
  module.exports.name = name
  module.exports.sayMsg = (name, age) => {
    console.log("My name is " + name + "," + age +" years old.")
  } 
  ```

- test.js文件内容：

  ```
  const md = require("./testArg.js")   //引入testArg模块，声明为md标识
  md.sayMsg(md.name,md.age)	//使用md标识调用模块内容
  ```

- 运行test.js，成功在test.js里面调用testArg模块：

  ![image](https://user-images.githubusercontent.com/22440467/155465427-3e5060f0-7507-44b5-9dcb-46869ab10e4e.png)

### Node.js模块工作原理

​	前面提到模块就是一个js文件，实际上模块中的代码都是包装在一个函数中执行。在JS中var变量作用域是函数内，let，const都是块级作用域。在模块中用到的变量都相当于函数内部的变量，外界无法访问。只对于想暴露的变量采用exports主动导出。这个特性更加便于维护工作环境，防止变量相互间污染。

在自定义模块案例中，testArg.js文件添加如下代码，打印封装函数实参的伪数组对象arguments：

![image](https://user-images.githubusercontent.com/22440467/155467045-251847ef-fa70-4ce8-a8f0-712287eb62c2.png)

可以看到如下信息：

![image](https://user-images.githubusercontent.com/22440467/155467182-ae823964-a298-4445-8d7f-c3ea24d348ce.png)

这说明testArg模块确实是封装在函数内执行，并且还有五个参数：

- exports
  该对象用来将变量或函数暴露到外部

- require
  用来引入外部的模块

- module

  - module代表的是当前模块本身
  - exports就是module的属性
  - 既可以使用exports 导出，也可以使用module.exports导出

- _filename

  当前模块的完整路径

- _dirname

  当前模块的文件夹路径

**注意**：前面提到只有通过exports暴露的属性和方法才能被访问到，但是如果在testArg模块内声明的是全局变量如：`globalTest = 10`，引用testArg模块后，`globalTest` 变量也会被访问到，但是和被暴露的变量不同，`globalTest`是直接使用，而不是采用模块标识调用。 其实也很好理解，在JS中函数内部如果声明了不带var，let，const等关键字的变量则是全局变量，在函数外部也可以访问。因此，我们在自定义模块的时候要避免使用全局变量，会造成变量使用混乱等一系列问题。