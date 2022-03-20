# [position定位](https://github.com/Twlig/issuesBlog/issues/56)

## position定位

position属性指定一个元素（静态的，相对的，绝对或固定）的定位方法的类型。

### 1、position：relative  相对定位

特点：

 1）不影响元素本身的特性
 2）**不使元素脱离文档流（元素移动之后原始位置会被保留）**
 3）**相对自身位置移动**。如果没有定位偏移量，对元素本身没有任何影响
 4）提升层级

注：定位元素位置控制：top/right/bottom/left  定位元素偏移量

### 2、position：absolute  绝对定位

特点：

 1）**使元素完全脱离文档流**
 2）如果有定位父级相对于定位父级（relative，absolute，fixed等）发生偏移，没有定位父级相对于document发生偏移
 3）**相对于定位父级移动位置**
 4）相对定位一般都是配合绝对定位元素使用；
 5）提升层级

注意：

z-index:[number]；  定位层级
      a、定位元素默认后者层级高于前者；
      b、建议在兄弟标签之间比较层级



### 3、position：fixed  固定定位

特点：

 1）**使元素完全脱离文档流**；
 2）**相对于浏览器窗口定位（视口）**
 3）提升层级



### 4、position：sticky  粘性定位

该定位基于用户滚动的位置。

它的行为就像 position:relative; 而当页面滚动超出目标区域时，它的表现就像 position:fixed;，它会固定在目标位置。

```css
    .div3{
        background-color: green;
        position: sticky;
        top: 100px;
    }
```

