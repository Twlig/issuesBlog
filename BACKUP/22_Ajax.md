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
  xhr.open("post", "example.txt", true); //get请求，true异步
  ```

- **设置请求头**

  使用 setRequestHeader()方法。这个方法接收两个参数：头部字段的名称和值。

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
  xhr.open("post", "example.txt", true); //get请求，true异步
  xhr.setRequestHeader("content-Type","application/json");  //发送json格式数据
  var personMsg = {username: "zzy",age: 22}
  xhr.send(JSON.stringify(personMsg))
  ```

  

  