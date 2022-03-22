# [npm run dev启动过程](https://github.com/Twlig/issuesBlog/issues/68)

## npm run dev启动过程


1. #### npm run *** 是执行配置在package.json中的脚本，比如：

   ```javascript
   "scripts": {
       "dev": "webpack-dev-server --inline --progress --config build/webpack.dev.conf.js",
       "start": "npm run dev",
       "lint": "eslint --ext .js,.vue src",
       "build": "node build/build.js"
     },
   ```

   npm run dev 执行的就是webpack-dev-server --inline --progress --config build/webpack.dev.conf.js命令。

2. #### webpack.dev.conf文件通过merge引用了webpack.base.conf.js文件。

   ```javascript
   const baseWebpackConfig = require('./webpack.base.conf')
   ```

3. #### 在webpack.base.conf.js文件中调用了./src/main.js

   ```javascript
   entry: {
   	app: './src/main.js'
    },
   ```

4. #### main.js用到了一个html元素#app，并创建了一个vue

   ```javascript
   new Vue({
        el: '#app',
        router,
        components: { App },
        template: '<App/>'
   })
   ```

5. #### 创建的vue会插入到webpack.dev.conf.js配置中HtmlWebpackPlugin配置的文件中

   ```javascript
    new HtmlWebpackPlugin({
      filename: 'index.html',
      template: 'index.html',
      inject: true
    }),
   ```

6. #### main.js中配置了router-view，router-view会加载HelloWorld.vue并插入到index.html的中

   ```javascript
   //main.js
   import router from './router'import router from './router'
   ```

   ```javascript
   //router/index.js
   {
      path: '/',
      name: 'HelloWorld',
      component: HelloWorld
    },
   ```

   ```html
   //App.Vue
   <template>
     <div id="app">
       <img src="./assets/logo.png">
       <router-view></router-view>
     </div>
   </template>
   ```

   

原文链接：[npm run dev启动过程](https://blog.csdn.net/cerastes/article/details/85065037)