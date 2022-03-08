# [Ajax](https://github.com/Twlig/issuesBlog/issues/22)

**Ajax**（Asynchronous JavaScript+XML，即异步 JavaScript 加 XML）技术，这个技术涉及发送服务器请求额外数据而**不刷新页面**，只是**更新局部页面**，从而实现更好的用户体验。

把 Ajax 推到历史舞台上的关键技术是 **XMLHttpRequest（XHR）对象**，它为开发者提供了原生的浏览器通信能力。XHR 为发送服务器请求和获取响应提供了合理的接口。这个接口可以实现异步从服务器获取额外数据，意味着用户点击不用页面刷新也可以获取数据。通过 XHR 对象获取数据后，可以使用 DOM 方法把数据插入网页。

### Ajax请求流程

- **创建XHR对象**

  ```javascript
  let xhr = new XMLHttpRequest();
  ```

- **设置请求行**（还未发送请求）

  open()方法，这个方法接收 3 个参数：请求类型（"get"、"post"等）、请求 URL，以及表示请求是否异步的布尔值。

  ```javascript
  xhr.open("post", "example.txt", true); //post请求，true异步
  ```

- **设置请求头**

  使用 setRequestHeader()方法。这个方法接收两个参数：头部字段的名称和值。**setRequestHeader必须在open()方法之后，send()方法之前调用，否则会抛错**

  ```javascript
  xhr.setRequestHeader("content-Type","application/json");  //请求体是json格式数据
  ```

- **发送请求**

  send()方法，接收一个参数，是作为**请求体发送的数据**。如果不需要发送请求体，则必须传 null，因为这个参数在某些浏览器中是必需的。调用 send()之后，请求就会发送到服务器。

  ```javascript
  var personMsg = {username: "zzy",age: 22}
  xhr.send(JSON.stringify(personMsg))
  ```

- **接收服务器的响应数据**

  XHR 对象有一个 **readyState 属性**，表示**当前处在请求/响应过程的哪个阶段**。每次 readyState 从一个值变成另一个值，都会触发 **readystatechange 事件**。

  > 0：未初始化（Uninitialized）。尚未调用 open()方法。
  >
  > 1：已打开（Open）。已调用 open()方法，尚未调用 send()方法。
  >
  > 2：已发送（Sent）。已调用 send()方法，尚未收到响应。
  >
  > 3：接收中（Receiving）。已经收到部分响应。
  >
  > 4：完成（Complete）。已经收到所有响应，可以使用了。

  ```javascript
  xhr.onreadystatechange = function() {  //一定要在open方法前
       if (xhr.readyState == 4) { 
           if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) { 
               alert(xhr.responseText); 
           }
       } 
  }; 
  ```

  status状态码用于判断响应的HTTP状态，responseText响应体返回的文本。

  **完整代码：**

  ```javascript
  let xhr = new XMLHttpRequest();
  xhr.onreadystatechange = function() {  //一定要在open方法前
       if (xhr.readyState == 4) { 
           if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) { 
               alert(xhr.responseText); 
           }
       } 
  }; 
  xhr.open("post", "example.txt", true); //post请求，true异步
  xhr.setRequestHeader("Content-Type","application/json");  //发送json格式数据
  var personMsg = {username: "zzy",age: 22}
  xhr.send(JSON.stringify(personMsg))
  ```

  

  

---

**Content-Type请求头常见值**：

1. x-www-form-urlencoded

   - 是表单提交的一种，以x-www-form-urlencoded方式提交数据，会将表单内的数据转换为键值对

   - 查询参数的形式，如get请求url中?号后面的查询参数

     ```
     https://books.com?bookClass=10&year=2020
     ```

2. multipart/form-data。
   也是一种表单提交，以multipart/form-data方式提交数据，是使用包含文件上传控件的表单。

3. application/json

   请求体数据采用json格式

---

**XMLHttpRequest.response和XMLHttpRequest.responseText区别**

1. **XMLHttpRequest.response 属性**

   返回响应的正文。返回的类型为 ArrayBuffer 、 Blob 、 Document 、JavaScript Object 或 DOMString 中的一个。 这取决于 responseType 属性。

2. **XMLHttpRequest.responseText**

   返回类型为DOMString的纯文本的值。

**XMLHttpRequest.overrideMimeType()与XMLHttpRequest.responseType**

1. **XMLHttpRequest.responseType**

   responseType 决定了写入**response**的响应数据被解析成什么类型的数据。在这里XHR直接封装好了内部解析响应，省去手动解析响应主体的步骤。**要在调用 open() 初始化请求之后调用，并且要在调用 send() 发送请求到服务器之前调用**。

   ```javascript
   let xhr = new XMLHttpRequest();
   xhr.onreadystatechange = function() {  //一定要在open方法前
        if (xhr.readyState == 4) { 
            if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) { 
                alert(xhr.response); //json格式
            }
        } 
   }; 
   xhr.open("get", "example.txt", true); //get请求，true异步
   xhr.responseType = "json";
   xhr.send(null)
   ```

2. **XMLHttpRequest.overrideMimeType()**

   overrideMimeType 方法是指定一个MIME类型用于**替代服务器指定的类型**（相当于重写响应类型），使服务端响应信息中传输的数据按照该指定MIME类型处理。例如强制使流方式处理为"text/xml"类型处理时会被使用到，即使服务器在响应头中并没有这样指定。**此方法必须在send方法之前调用方为有效。**

   ```javascript
   let xhr = new XMLHttpRequest();
   xhr.overrideMimeType("text/plain");
   xhr.open("get", "example.txt", true); //get请求，true异步
   xhr.send(null)
   ```

   参考：[MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest)

---

**XMLHttpRequest.withCredentials与 CORS 什么关系**
在发同域请求时，浏览器会将cookie自动加在request header中。但在发送跨域请求时，cookie并没有自动加在request header中。
造成这个问题的原因是：在CORS标准中做了规定，默认情况下，浏览器在发送跨域请求时，不能发送任何认证信息（credentials）如"cookies"和"HTTP authentication schemes"。除非withCredentials为true（xhr对象有一个属性叫withCredentials，默认值为false）。
所以根本原因是cookies也是一种认证信息，在跨域请求中，client端必须手动设置withCredentials=true，且server端也必须允许request能携带认证信息（即response header中包含Access-Control-Allow-Credentials:true），这样浏览器才会自动将cookie加在request header中。

另外，要特别注意一点，一旦跨域request能够携带认证信息，server端一定不能将Access-Control-Allow-Origin设置为*，而必须设置为请求页面的域名。
参考：[cookies](https://segmentfault.com/a/1190000004322487#articleHeader13)