# [HTTP headers 详解](https://github.com/Twlig/issuesBlog/issues/79)

## HTTP headers 详解 headers 常用属性

HTTP报文有请求报文和响应报文两种。请求报文是客服端发送给服务器的，响应报文是服务器发送给客户端的。

- 请求报文

![image](https://user-images.githubusercontent.com/22440467/160349749-2f6a02f2-5133-48f4-8fb6-e891f7fcf333.png)

第一行是请求行，描述的是这个请求的基本信息，剩下的就是请求头

- 响应报文

![image](https://user-images.githubusercontent.com/22440467/160349807-45e02109-502a-4771-b3ae-bee5bd9e9045.png)

第一行是状态行，描述http协议版本，响应状态信息。



## HTTP header 常用属性

#### 1、Host

请求资源的Internet主机和端口号，它通常从HTTP URL中提取出来的，例如我们在浏览器中输入：https://www.baidu.com/

Host请求报头域：

[www.baidu.com](https://github.com/Twlig/issuesBlog/issues/www.baidu.com)(此处使用缺省端口号443，若指定了端口号： www.baidu.com:443

#### 2、Referer

告诉服务器该请求是从哪个页面链接过来的。比如点击A网页的一个链接访问[www.baidu.com](https://github.com/Twlig/issuesBlog/issues/www.baidu.com)，referer就是A网页的URL。这个属性可以用于防止csrf攻击。

#### 3、User-Agent

告诉HTTP服务器， 客户端使用的操作系统和浏览器的名称和版本。
例如： User-Agent: Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 5.1; Trident/4.0; CIBA; .NET CLR 2.0.50727; .NET CLR 3.0.4506.2152; .NET CLR 3.5.30729; .NET4.0C; InfoPath.2; .NET4.0E)

#### 4、Content-type

表示请求体属于什么MIME类型。

**常见的媒体格式类型如下**：

text/html ： HTML格式
text/plain ：纯文本格式
text/xml ： XML格式
image/gif ：gif图片格式
image/jpeg ：jpg图片格式
image/png：png图片格式

**以application开头的媒体格式类型**：

application/xhtml+xml ：XHTML格式
application/xml ： XML数据格式
application/atom+xml ：Atom XML聚合格式
application/json ： JSON数据格式
application/pdf ：pdf格式
application/msword ： Word文档格式
application/octet-stream ： 二进制流数据（如常见的文件下载）
application/x-www-form-urlencoded ： 中默认的encType，form表单数据被编码为key/value格式发送到服务器（表单默认的提交数据的格式）

另外一种常见的媒体格式是上传文件之时使用的：

multipart/form-data ： 需要在表单中进行文件上传时，就需要使用该格式。

#### 5、Accept-Language

Accept-Langeuage：指出浏览器可以接受的语言种类，如en或en-us指英语，zh或者zh-cn指中文，当服务器能够提供一种以上的语言版本时要用到。

#### 6、Cookie

Cookie：浏览器用这个属性向服务器发送Cookie。Cookie是在浏览器中寄存的小型数据体，它可以记载和服务器相关的用户信息，也可以用来实现会话功能。

参考文章：
- [http headers 详解](https://blog.csdn.net/weixin_45598506/article/details/112917752)