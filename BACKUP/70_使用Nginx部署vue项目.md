# [使用Nginx部署vue项目](https://github.com/Twlig/issuesBlog/issues/70)

## 使用Nginx部署vue项目



#### 1.打包vue项目

```shell
npm run build
```

运行上述命令，会生成打包好的dist文件夹。

此处尝试之后发现，真的publicPath只能采用`/`，而不是使用`./`

#### 2.下载nginx

在window下，下载nginx并安装：

其中conf内有nginx.conf文件，是nginx运行的配置文件

html文件是默认的nginx配置的默认访问资源

#### 3.修改nginx配置文件

```
http {
    include       mime.types;
    default_type  application/octet-stream;
    sendfile        on;
    keepalive_timeout  65;
    
    server {
        listen       81;
        server_name  localhost;

        root D:\Project\bookstore_fe\dist;  
        location / {
            index  index.html index.htm;
        }
    }
```

- location /：表示匹配访问根目录
- root：用于指定访问根目录时，访问主机的web目录。把根目录映射到D:\Project\bookstore_fe\dist
- index：在不指定访问具体资源时，默认展示的资源文件列表

#### 4.启动nginx

```shell
start nginx
```

#### 5.杀死nginx进程

```shell
tasklist /fi "imagename eq nginx.exe"    //查询nginx进程
taskkill /f /pid 65984 /pid 55228       //杀死nginx进程
```

如果不手动删除nginx进程，nginx进程会一直存在，这**会导致修改配置不生效**。因此，建议手动删除，或者写个配置去自动删除。

