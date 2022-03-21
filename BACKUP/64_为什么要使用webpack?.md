# [为什么要使用webpack?](https://github.com/Twlig/issuesBlog/issues/64)

## 为什么要使用webpack？

项目中有很多文件会相互依赖，有几个文件就要发多少次请求来获取文件，如果一个文件因为网络问题延误了整个页面也会延误，而当项目逐渐变大，有几十个到上百个JavaScript文件的时候，那问题会更严重，不但有延迟问题，还会遇到很难维护的问题。

而webpack就解决了这些问题，把所有JavaScript文件合成一个文件，这样就减少了http请求数量，让页面加载和显示更快。不过这个合并的阶段是在开发完成之后才进行的，也就是说开发阶段我仍然是有a.js，b.js和c.js等等这些文件的，这样才好开发和维护，因为如果开发阶段就合并的话，就相当于基于一个可能上万行的文件进行开发，这样的代码是没法维护的。

在开发后完成的这个合并的过程就是打包。webpack在打包过程中：

- 会分析各个文件之间的依赖关系，然后生成一个依赖图并用文件的形式保存下来
- 并且针对浏览器无法识别的如less，es6等文件进行统一的处理，翻译成浏览器可以识别的js
- 浏览器运行代码的时候就读取这个文件，就知道了各个代码块之间的关联以及如何调用



## webpack基础结构

#### 1、Entry（入口）

指示 webpack 应该使用哪个模块，来作为构建其内部依赖图的开始。进入入口起点后，webpack 会找出有哪些模块和库是入口起点（直接和间接）依赖的。

#### 2、Output（出口）

告诉 webpack 在哪里输出它所创建的结果文件，以及如何命名这些文件，默认值为./dist。

#### 3、Loader（模块转换器）

将所有类型的文件转换为 webpack 能够处理的有效模块，然后你就可以利用 webpack 的打包能力，对它们进行处理。

#### 4、Plugins（插件）

在 Webpack 构建流程中的特定时机注入扩展逻辑来改变构建结果或做想要的事情。

#### 5、Module(模块)

开发者将程序分解成离散功能块，并称之为模块，在webpack里一个模块对应着一个文件，webpack会从配置的 Entry 开始递归找出所有依赖的模块。比如常见的，开发模式和生产模式环境。



## webpack基本用法

#### 1.创建webpack.config.js文件

```javascript
const path = require("path"); //Node.js内置模块
module.exports = {
    entry: './src/main.js', //配置入口文件
    output: {
   	path: path.resolve(__dirname, './dist'), //输出路径，__dirname：当前文件所在路径
        filename: 'bundle.js' //输出文件
    },
    module: {
        rules: [  
            {  
                test: /\.css$/,    //打包规则应用到以css结尾的文件上
                use: ['style-loader', 'css-loader'] 
            }  
        ]  
    }
}
```

webpack只能识别js和json，对于导入的css无法识别，因此需要采用loader翻译css文件。

#### 2.命令行执行

```javascript
webpack --mode=development
```

或者配置项目的npm运行命令，修改package.json文件

```javascript
"scripts": {
    //...,
    "dev": "webpack --mode=development"
 }
```

然后命令行运行：

```javascript
npm run dev
```

#### 3.在webpack目录下创建index.html

引用bundle.js

```html
<body>
    <script src="dist/bundle.js"></script>
</body>
```

#### 4.浏览器查看index.html