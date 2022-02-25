# [Express中间件](https://github.com/Twlig/issuesBlog/issues/7)

### Express中间件的调用流程

​	当一个请求到达Express 的服务器之后，可以连续调用多个中间件，从而对这次请求进行**预处理。**

<div align = center><img src="https://user-images.githubusercontent.com/22440467/155481646-da751036-312d-48b9-a703-6182474c03e8.png" width=400></div>

### Express中间件格式

​	Express 的中间件，本质上就是一个function处理函数，Express中间件的格式如下:

```node
function(req, res, next){
	next()
}
```

**注意**：中间件函数的形参列表中，必须包含`next` 参数。而路由处理函数中只包含`req`和`res`。`next`函数是实现多个中间件连续调用的关键，它表示把流转关系转交给下一个中间件或路由。

### 定义Express中间件

- **一个全局中间件**

```node
const express = require("express")
const app = express()
const mw = (req, res, next) => {
    const time = Date.now()
    req.startTime = time  //可以向req中写入数据，和后续的中间件共享
    console.log("这是一个中间件" + req.startTime)
    next() //如果没有next函数会导致，客户端请求到了mw中间件后无法向下走，后续get就没法执行
}
app.use(mw)  //全局注册mw中间件
app.use(express.json())
app.get('/user/:id',(req, res) => {
    console.log("user/id")
    res.send('get请求成功'+ req.startTime) //取出第一个中间件写入的数据
})
app.post('/user', (req, res) => {
    console.log(req.body)
    res.send("请求成功")
})
app.listen(80,() =>{
    console.log("监听80")
}) 
```

- **多个全局中间件**

```node
app.use(mw1)  //全局注册mw1中间件
app.use(mw2)  //全局注册mw2中间件
app.get('/user/:id',(req, res) => { //这个请求会依次触发前两个中间件，然后在执行get里的回调函数
    console.log("user/id")
    res.send('get请求成功') 
})
```

- **局部中间件**

```node
// mw1中间件只在当前路由生效
app.get('/user/:id',mw1, (req, res) => { 
    console.log("user/id")
    res.send('get请求成功') 
})
```

- **多个局部中间件**

```node
//两种写法都行
app.get('/user/:id',mw1,mw2, (req, res) => {})
app.get('/user/:id',[mw1,mw2], (req, res) => {})
```

### 中间件类型

1. **应用级别中间件**

   通过`app.use()`或 `app.get0`或 `app.post()`，**绑定到`app`实例**上的中间件，叫做应用级别的中间件，代码示例如下:

   ```node
   app.use(mw1)
   app.get('/',mw1,(req,res)=>{})
   ```

2. **路由级别中间件**

   绑定到 **`express.Router()`实例**上的中间件，叫做路由级别的中间件。它的用法和应用级别中间件没有任何区别。只不过，应用级别中间件是绑定到 `app`实例上，路由级别中间件绑定到router实例上，代码示例如下:

   ```node
   var router = express.Router()
   router.use(mw1)
   ```

3. **错误级别中间件**

   **作用**：用来**捕获异常错误**，从而防止项目异常崩溃的问题
   **格式**：错误级别中间件的 function处理函数中，必须有4个形参，形参顺序从前到后，分别是`(err, req, res, next)`

   ```node
   app.get('/', (req,res) =>{
   	throw new Error('服务器内部错误')
   })
   app.use((err, req, res, next) =>{
   	console.log('发生错误：'+err.message)
   }) //注意错误级别中间件必须写在最后面，不然就捕获不到异常
   ```

4. **Express内置中间件**

   自Express 4.16.0版本开始，Express 内置了3个常用的中间件，极大的提高了Express 项目的开发效率和体验:

   - `express.static`快速托管静态资源的内置中间件，例如:HTML文件、图片、CSS样式等(无兼容性)
   - `express.json`解析JSON格式的请求体数据（有兼容性，仅在4.16.0+版本中可用)
   - `express.urlencoded`解析URL-encoded格式的请求体数据（有兼容性，仅在4.16.0+版本中可用)

5. **第三方中间件**
   非 Express官方内置的，而是由第三方开发出来的中间件。需`npm install`下载并`require`导入第三方中间件，比如`body-parser`。

### 自定义中间件

自定义一个post 处理中间件

- `postMw.js`

```node
const querystring = require('querystring')
module.exports = function (req, res, next) {
	var str = ""
	req.on("data", function (data) {
		str += data
	})
	req.on("end", function () {
		// 把数据绑定到 req 对象上，然后在后面就能够使用
		req.body = querystring.parse(str)
		next()
	})
}
```

- `main.js`

```node
const express = require('express')
const postmw = require('./postMw.js')
var app = express()
app.listen(8080)
app.use(postmw)
app.use("/", function (req, res, next) {
	console.log("拿到数据", req.body)
})
```

