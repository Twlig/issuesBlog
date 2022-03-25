# [cookie、session与token](https://github.com/Twlig/issuesBlog/issues/23)

想象一个场景，用户通过http连接登录网站，服务端响应用户所需的内容。之后用户还需要继续访问网站里的内容，那么在这期间用户并没有重新登录，服务端是如何确认用户身边的呢。要知道http是无状态响应。那么服务端怎么在后续的浏览器请求中确定用户的身份呢？可以每次请求服务端都把用户名和密码发送过去，但是这样太过于繁琐，对用户来说不友好。要是能http请求时自动带数据过去给服务器确定身份就好了。因此，cookie就诞生了。

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
可以笼统地将cookie划分为**会话cookie**和**持久cookie**。会话cookie在用户退出浏览器时被删除，持久cookie被存储在硬盘，计算机重启后依然存在。
会话cookie和持久cookie的唯一区别在于它们的过期时间。如果设置了Discard参数，或者没有Expires或Max-age参数来说明扩展的过期时间，这个cookie就是一个会话cookie。
Cookie在生成时就会被指定一个Expire值，这就是Cookie的生存周期，在这个周期内Cookie有效，超出周期Cookie就会被清除。有些页面将Cookie的生存周期设置为“0”或负值，这样在关闭浏览器时，就马上清除Cookie，不会记录用户信息，更加安全。

---

### Cookie属性
name----Cookie的名称，一旦建立不可修改
value----Cookie的值，如果值为unicode字符，则需要为字符编码。如果是二进制数据，需要使用BASE64编码
maxAge----Cookie失效的时间，单位秒。如果为整数，则该Cookie在maxAge秒后失效。如果为负数，该Cookie为临时Cookie，关闭浏览器即失效，浏览器也不会以任何形式保存该Cookie。如果为0，表示删除该Cookie。默认为-1。
secure----该Cookie是否仅被使用安全协议传输。安全协议。安全协议有HTTPS，SSL等，在网络上传输数据之前先将数据加密。默认为false。
path----Cookie的使用路径。如果设置为“/sessionWeb/”，则只有contextPath为“/sessionWeb”的程序可以访问该Cookie。如果设置为“/”，则本域名下contextPath都可以访问该Cookie。注意最后一个字符必须为“/”。
domain----可以访问该Cookie的域名。如果设置为“.google.com”，则所有以“google.com”结尾的域名都可以访问该Cookie。注意第一个字符必须为“.”。
comment----该Cookie的用处说明，浏览器显示Cookie信息的时候显示该说明。
version----Cookie使用的版本号。0表示遵循Netscape的Cookie规范，1表示遵循W3C的RFC 2109规范

### Cookie例子
```java
Set-cookie: user="xx";  domain="airtravelbargains.com"
Set-cookie: pref=compact; domain="airtravelbargains.com"; path = /autos/ 
//分配一个路径autos，可以生成一个特殊汽车租赁cookie
```

如果用户访问的是www.airtravelbargains.com，special.airtravelbargains.com，或者www.airtravelbargains.com/specilas.html，则只发回`user="xxx"`的cookie；

如果用户访问www.airtravelbargains.com/autos/index.html，则发回两个cookie。

```
Cookie: user="xxx"
Cookie: pref=compact
```

---

### session生命周期问题

Session的生命周期是从打开一个浏览器窗口发送请求到关闭浏览器窗口，这种说法是不正确的！
当用户第一次访问Web应用中支持Session的某个网页时，就会开始一个新的Session，那么接下来当用户浏览这个Web应用的不同网页时，始终处于一个Session中。

#### 结束session生命周期

结束Session生命周期有两种方法：

- Session.invalidate()方法，不过这个方法在实际的开发中，并不推荐，可能在强制注销用户的时候会使用
- 当前用户和服务器的交互时间超过默认时间后，Session会失效。

当把浏览器关闭时，浏览器并没有向服务器发送任何请求来关闭Session，自然Session也不会被销毁，但是可以做一点努力，在所有的客户端页面里使用js的window.onclose来监视浏览器的关闭动作，然后向服务器发送一个请求来关闭Session，但是这种做法在实际的开发中也是不推荐使用的，最正常的办法就是不去管它，让它等到默认的时间后，自动销毁。

#### 会话cookie

那么为什么当我们关闭浏览器后，就再也访问不到之前的session了呢？
其实之前的**Session一直都在服务器端**，session定义的Cookie是会话Cookie，当我们关闭浏览器，cookie就没有了。

当重新打开浏览器窗口时，之前的Cookie中存放的Sessionid已经不存在了，此时服务器从tpServletRequest对象中没有检查到sessionid，服务器会再发送一个新的存有Sessionid的Cookie到客户端的浏览器中，此时对应的是一个新的会话，而服务器上原先的session等到它的默认时间到之后，便会自动销毁。

#### session使用范围

- 当在同一个浏览器中同时打开多个标签，发送同一个请求或不同的请求，仍是同一个session;
- 当不在同一个窗口中打开相同的浏览器时，发送请求，仍是同一个session;
- 当使用不同的浏览器时，发送请求，即使发送相同的请求，是不同的session;（因为该浏览器没有对应的session ID，所以需要重新申请）

**原文**：[浏览器关闭后，Session会话结束了么？](https://blog.csdn.net/stanxl/article/details/47105051)