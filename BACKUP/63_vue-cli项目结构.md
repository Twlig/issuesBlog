# [vue-cli项目结构](https://github.com/Twlig/issuesBlog/issues/63)

## vue-cli项目结构

```markdown
├── README.md // 项目说明文档

├── node_modules // 依赖包目录

├── build // webpack相关配置文件（都已配置好，一般无需再配置）

│       ├── build.js  //生成环境构建

│       ├── check-versions.js  //版本检查（node，npm）

│       ├── dev-client.js    //开发服务器热重载 （实现页面的自动刷新） 

│       ├── dev-server.js    //构建本地服务器（npm run dev）

│       ├── utils.js         // 构建相关工具

│       ├── vue-loader.conf.js   //csss 加载器配置

│       ├── webpack.base.conf.js    //webpack基础配置

│       ├── webpack.dev.conf.js     // webpack开发环境配置

│       └── webpack.prod.conf.js     //webpack生产环境配置

├── config // vue基本配置文件（可配置监听端口，打包输出等）

│       ├── dev.env.js // 项目开发环境配置

│       ├── index.js //   项目主要配置（包括监听端口、打包路径等）

│       └── prod.env.js // 生产环境配置

├── index.html // 项目入口文件

├── package-lock.json // npm5 新增文件，优化性能

├── package.json // 项目依赖包配置文件
├── src // 项目核心文件（存放我们编写的源码文件）

│       ├── App.vue // 根组件
        
│       ├── assets // 静态资源（样式类文件、如css，less，和一些外部的js文件）

│       │       └── css  //样式

│       │       └── font  //字体
│       │       └── images  //图片
│       ├── components // 组件目录

│       │       └── Hello.vue // 测试组件

│       ├── main.js // 入口js文件

│       └── router // 路由配置文件夹

│       └── index.js // 路由配置文件

└── static // 静态资源目录（一般存放图片类）
```

在config/index.js中

```javascript
module.exports = {
  dev: {
    // Paths
    assetsSubDirectory: 'static',
    assetsPublicPath: '/',
    proxyTable: {},

    // Various Dev Server settings
    host: 'localhost', // can be overwritten by process.env.HOST
    port: 8080,  //监听端口
    ...
  },
  build: {
    // Template for index.html
    index: path.resolve(__dirname, '../dist/index.html'),
    // Paths
    assetsRoot: path.resolve(__dirname, '../dist'),
    assetsSubDirectory: 'static',
    assetsPublicPath: './',  //如果不使用相对路径打包后会存在找不到资源的问题
    ...
  }
}
```

dev是开发模式的打包配置，build是生产模式的打包配置。

- assetsRoot : 在入口文件（src/main.js）的上一级，dist 目录下输出资源文件

- assetsSubDirectory: 把所有的静态资源打包到 dist下的 static文件夹下

- assetsPublicPath : 代表生成的index.html文件，里面引入资源时，路径前面要加上 `./`

  比如：

  ```html
  <script type="text/javascript" src="./static/js/app.js"></script>
  ```

使用npm run dev项目以开发模式运行，npm run build项目以生产模式运行，打包生成dist文件夹