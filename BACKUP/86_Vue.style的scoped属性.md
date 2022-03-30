# [Vue style的scoped属性](https://github.com/Twlig/issuesBlog/issues/86)

## style scoped属性

当 `style` 标签有 scoped 属性时，它的 CSS 只作用于当前组件中的元素。

```HTML
<style scoped>
  @media (min-width: 250px) {
    .list-container:hover {
      background: orange;
    }
  }
</style>
```

这个可选 `scoped` attribute 会自动添加一个唯一的 attribute (比如 `data-v-21e5b78`) 为组件内 CSS 指定作用域，编译的时候 `.list-container:hover` 会被编译成类似 `.list-container[data-v-21e5b78]:hover`。

## 原理

1. 给HTML的DOM节点加一个不重复data属性(形如：data-v-2311c06a)来表示他的唯一性。
2. 在每句css选择器的末尾（编译后的生成的css语句）加一个当前组件的data属性选择器的哈希特征值（如[data-v-2311c06a]）来私有化样式。

### 慎用 scoped

1. 父组件无`scoped`属性，子组件带有`scoped`，父组件是无法操作子组件的样式的（原因在原理中可知），虽然我们可以在全局中通过该类标签的标签选择器设置样式，但会影响到其他组件
2. 父组件有`scoped`属性，子组件无，父组件也无法设置子组件样式，因为父组件的所有标签都会带有data-v-469af010唯一标志，但子组件不会带有这个唯一标志属性，与1同理，虽然我们可以在全局中通过该类标签的标签选择器设置样式，但会影响到其他组件
3. 父子组建都有，同理也无法设置样式，更改起来增加代码量

### scoped 穿透

基于上面3个原因，或者Vue引用了第三方组件，需要在组件中局部修改第三方组件的样式，而又不想去除`coped`属性造成组件之间的样式污染。此时只能通过特殊的方式，穿透`scoped`。

那如果我就是想让父组件的样式覆盖子组件的样式，怎么办呢？

1. 使用两个style，一个用于私有样式，一个用于共有样式。

```html
<!--共有样式-->
<style>
.content .button {
  color: red;
}
</style>
<!--私有样式-->
<style scoped>
.content {
  width: 1000px;
  margin: 0 auto;
}
</style>
```

1. 深度作用选择器

```html
<style scoped>
.content {
  width: 1000px;
  margin: 0 auto;
}
.content >>> .button {
  color: red;
}
/* .content /deep/ .button {
  color: red;
} */
/* .content ::v-deep .button{
  color: red;
} */
</style>
```

上述代码将会编译成：

```HTML
<style type="text/css">
.content[data-v-7ba5bd90] {
  width: 1000px;
  margin: 0 auto;
}
.content[data-v-7ba5bd90] .button {
  color: red;
}
/* .content /deep/ .button {
  color: red;
} */
/* .content ::v-deep .button{
  color: red;
} */
</style>
```

有些像 Sass 之类的预处理器无法正确解析 >>>。这种情况下你可以使用 /deep/ 或 ::v-deep 操作符取而代之——两者都是 >>> 的别名，同样可以正常工作。


参考文章：

- [Vue scoped原理](https://juejin.cn/post/6898633179587215367)

