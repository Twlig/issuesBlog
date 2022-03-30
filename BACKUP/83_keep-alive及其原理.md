# [keep-alive及其原理](https://github.com/Twlig/issuesBlog/issues/83)

## keep-alive

### 是什么？

- `keep-alive`是一个`Vue全局组件`
- `keep-alive`本身不会渲染出来，也不会出现在父组件链中
- `keep-alive`包裹动态组件时，会缓存不活动的组件，而不是销毁它们

### 怎么用？

`keep-alive`接收三个参数：

- `include`：可传`字符串、正则表达式、数组`，名称匹配成功的组件会被缓存
- `exclude`：可传`字符串、正则表达式、数组`，名称匹配成功的组件不会被缓存
- `max`：可传`数字`，限制缓存组件的最大数量

`include`和`exclude`，传`数组`情况居多

#### 动态组件

```vue
<keep-alive :include="allowList" :exclude="noAllowList" :max="amount"> 
    <component :is="currentComponent"></component> 
</keep-alive>
```

#### 路由组件

```vue
<keep-alive :include="allowList" :exclude="noAllowList" :max="amount">
    <router-view></router-view>
</keep-alive>
```

## 源码

### 组件基础

前面说了，`keep-alive`是一个`Vue全局组件`，他接收三个参数：

- `include`：可传`字符串、正则表达式、数组`，名称匹配成功的组件会被缓存
- `exclude`：可传`字符串、正则表达式、数组`，名称匹配成功的组件不会被缓存
- `max`：可传`数字`，限制缓存组件的最大数量，超过`max`则按照`LRU算法`进行置换

顺便说说`keep-alive`在各个生命周期里都做了啥吧：

- `created`：初始化一个`cache、keys`，前者用来存缓存组件的虚拟dom集合，后者用来存缓存组件的key集合
- `mounted`：实时监听`include、exclude`这两个的变化，并执行相应操作
- `destroyed`：删除掉所有缓存相关的东西

> 之前说了，`keep-alive`不会被渲染到页面上，所以`abstract`这个属性至关重要！

```javascript
// src/core/components/keep-alive.js

export default {
  name: 'keep-alive',
  abstract: true, // 判断此组件是否需要在渲染成真实DOM
  props: {
    include: patternTypes,
    exclude: patternTypes,
    max: [String, Number]
  },
  created() {
    this.cache = Object.create(null) // 创建对象来存储  缓存虚拟dom
    this.keys = [] // 创建数组来存储  缓存key
  },
  mounted() {
    // 实时监听include、exclude的变动
    this.$watch('include', val => {
      pruneCache(this, name => matches(val, name))
    })
    this.$watch('exclude', val => {
      pruneCache(this, name => !matches(val, name))
    })
  },
  destroyed() {
    for (const key in this.cache) { // 删除所有的缓存
      pruneCacheEntry(this.cache, key, this.keys)
    }
  },
  render() {
      // 下面讲
  }
}
```


原文连接：

- [keep-alive](https://juejin.cn/post/7043401297302650917)