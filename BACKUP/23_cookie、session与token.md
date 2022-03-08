# [cookie、session与token](https://github.com/Twlig/issuesBlog/issues/23)

想象一个场景，用户通过http连接登录网站，服务端响应用户所需的内容。之后用户还需要继续访问网站里的内容，那么在这期间用户并没有重新登录，服务端是如何确认用户身边的呢。要知道http是无状态响应，一旦响应完成就结束会话了。那么服务端怎么在后续的浏览器请求中确定用户的身份呢？可以每次请求服务端都把用户名和密码发送过去，但是这样太过于繁琐，对用户来说不友好。要是能http请求时自动带数据过去给服务器确定身份就好了。因此，cookie就诞生了。

### Cookie

cookie是服务器给每个客户端颁发的通行证，包含一些可以识别用户身份的信息。

**cookie工作流程**：

1. 用户第一次在浏览器登录网站输入用户名密码，请求服务器应答。
2. 服务器接收到用户浏览器发送的http请求，判断用户名密码是否匹配，如果成功，则在**响应信息**中设置Set-Cookie响应头，内含一些**用户信息**可以用于确定用户身份。（服务器不保存cookie）
3. 浏览器**客户端**收到cookie之后会**自动保存在本地浏览器**。
4. 下一次发送请求时http会自动在**请求头中添加cookie**，发送给服务器。
5. 服务器根据cookie中记录的信息确定用户身边。

### Session

session是对于cookie安全性的优化，cookie可以长期存储用户信息在本地浏览器，很容易被黑客攻击。如果信息是存在服务器就能降低被窃取的风险。下面介绍基于cookie的session。

**session工作流程：**

1. 用户第一次在浏览器登录网站输入用户名密码，请求服务器应答。
2. 服务器接收到用户浏览器发送的http请求，判断用户名密码是否匹配
   1. 如果成功，则在服务器创建session（有session ID和会话结束时间，还有用户身份信息），并**保存在服务器**。
   2. 并在响应头中设置Set-Cookie响应头，把session中的session ID做为cookie的值，会话结束时间做为cookie的有效期。
3. 浏览器保存cookie。
4. 下一次发送请求时http会自动在**请求头中添加含有session ID的cookie**，发送给服务器。
5. 服务器根据session ID确定用户身边。

session比cookie更加安全，用于识别用户身份的session ID中存储的是无规律的字符串，没有泄露任何用户信息，即使黑客拿到session ID也没有办法获取到用户信息。

### Token

session解决了cookie的安全性问题，但是又带来了资源消耗问题，session存储在服务器中，当大量用户访问服务器时，会带来很高的资源消耗，如果用多个服务器处理用户请求，又会带来session同步问题（原本A服务器的session转到负载低的B服务器）。因此，JSON Web Token（JWT）产生。

JWT 数据格式：

由三部分组成

```
header.payload.signature
```

header声明使用的加密签名算法，payload是特定的数据，signature是签名。

1. header和payload通过Base64编码。
2. 把header和payload编码之后的信息加密生成signature签名，**密钥保存在服务器。**

**JWT工作流程：**

1. 用户第一次在浏览器登录网站输入用户名密码，请求服务器应答。
2. 服务器接收到用户浏览器发送的http请求，判断用户名密码是否匹配
   1. 生成JWT,并**保存生成JWT的密钥**
   2. 把JWT发送给浏览器
3. 浏览器以cookie或者Storage存储。
4. 后续发送请求时把JWT一并发给服务器。
5. 服务器使用密钥把JWT解码出来，并识别用户身份

✨**思考**：这个密钥是一个用户对应一个还是整个服务器就一个密钥？

​	猜测应该是一个密钥，如果每个用户都有一个密钥，那还得知道用户是谁才能用对应的密钥解密。就又产生了新的存储问题。

参考文章：

- [[cookies](https://blog.csdn.net/playboyanta123/article/details/79464684?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_utm_term~default-1.pc_relevant_default&spm=1001.2101.3001.4242.2&utm_relevant_index=4)](https://blog.csdn.net/playboyanta123/article/details/79464684?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_utm_term~default-1.pc_relevant_default&spm=1001.2101.3001.4242.2&utm_relevant_index=4)
- [[讲的贼好的哔哩哔哩视频](https://www.bilibili.com/video/BV1ob4y1Y7Ep?spm_id_from=333.999.0.0)](https://www.bilibili.com/video/BV1ob4y1Y7Ep?spm_id_from=333.999.0.0)

---

### Cookie有效期
Cookie在生成时就会被指定一个Expire值，这就是Cookie的生存周期，在这个周期内Cookie有效，超出周期Cookie就会被清除。有些页面将Cookie的生存周期设置为“0”或负值，这样在关闭浏览器时，就马上清除Cookie，不会记录用户信息，更加安全。