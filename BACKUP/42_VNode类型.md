# [VNode类型](https://github.com/Twlig/issuesBlog/issues/42)

## Vnode的类型

VNode是一个类，生成不同类型的VNode实例，不同类型的实例表示不同类型的真实DOM节点



### 1.注释节点

一个注释节点只有两个有效属性` text `和 `isComment`

```javascript
{
    text: "注释节点",
    isComment: true
}
```



### 2.文本节点

文本节点只有`text`属性

```javascript
{
    text: "Hello"
}
```



### 3.元素节点

#### 模板

```html
<p><span>Hello</span><span>World</span></p>
```

#### VNode

```javascript
{
    children: [VNode, VNode], // 当前节点的子节点列表
    context: {...}, // 当前组件的vue.js实例
    data: {...}, //　节点上的数据
    tag: "p", // 节点名称
    ...
}
```



### 4.组件节点

组件节点独有的属性：
`componentInstance`：组件实例
`componentOptions`：组件节点的选项参数，包含了propsData、tag和children

```javascript
// <child></child>
{
    componentInstance: {...},
    componentOptions: {...},
    context: {...},
    data: {...},
    tag: "vue-component-1-child",
    ...    
}
```



### 5.函数式节点

```javascript
{
    functionalContext: {...},
    functionalOptions: {...},
    context: {...},
    data: {...},
    tag: "div"
}
```

函数式节点独有的属性：`functionalContext`，`functionalOptions`



### 6.克隆节点

克隆节点是将现有节点的属性赋值给新节点中，作用是优化静态节点和插槽节点slot node

```javascript
export function cloneVNode(vnode, deep) {
        const cloned = new VNode(vnode.tag, vnode.data, vnode.children, vnode.text, vnode.elm, vnode.context, vnode.componentOptions, vnode.asyncFactory)
        cloned.ns = vnode.ns
        cloned.isStatic = vnode.isStatic
        cloned.key = vnode.key
        cloned.isComment = vnode.isComment
        cloned.isCloned = true
        if (deep && vnode.children) {
            cloned.children = cloneVNodes(vnode.children)
        }
        return cloned
    }
 // `isCloned `克隆节点为true，被克隆节点为false
```


