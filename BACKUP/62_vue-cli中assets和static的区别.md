# [vue-cli中assets和static的区别](https://github.com/Twlig/issuesBlog/issues/62)

## vue-cli中assets和static的区别

assets和static都被用于存放静态资源（js，css，image等）

- **assets目录中的文件会被webpack处理解析为模块依赖**

  只支持相对路径的形式

- **static/ 目录中的文件不会被Webpack处理：它们会直接被复制到最终的打包目录（默认是dist/static）下**

  网上很多说只支持绝对路径，但是我用相对路径也没有问题。我猜测是因为，我采用的默认配置，dist目录下面static子文件我没改成其他名称，刚好和项目中的static重名，因此在vue中：

  ```javascript
  require('../../static/css/images/pic04.jpg')
  ```

  并不会出错。还有一个问题就是dist中的index.html和static是并列关系，那么../../static明显会去访问上两级目录下的static。但是我猜测由于../../没有起到作用，并不会去上两级目录找，就跟在D盘中再怎么返回上一级也到不了其他路径一样。



### 解决背景图片显示问题

在模板中直接使用

```html
<div style="background-image: url('../../static/css/images/pic04.jpg')"></div>
```

打包之后引用会存在问题，找不到资源。

因此需要加载资源：

在data中：

```javascript
data() {
    urlImg: require('../../static/css/images/pic04.jpg')
}
```

在模板中：

```html
<div :style="{backgroundImage: 'url(' + urlImg + ')' }"></div>
```

