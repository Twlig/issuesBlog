# [export和export default区别](https://github.com/Twlig/issuesBlog/issues/29)

### 导出差别

1、export

- export 可以直接导出或者先定义后导出都可以。
  示例：

```javascript
export let i = “hello”；
export function myFun(){};
```

- 上面的直接导出，也可以使用下面先定义后导出。

```javascript
let i = “hello";
function myFun(){};
export {i , myFun}
```



2、export default

- export default是模块的默认对外接口，只有一个，所以只能出现一次。
- export default只能直接输出，不能先定义后导出。

```javascript
export default {
    props: {
        foo: 'foo'
    }
}
```



### 导入差别

1、export导出的对象，导入时写法：

```javascript
import {i, myFun}
```

2、export default导出的对象，导入时写法：

```JavaScript
import 变量名 from ‘模块’
```

很明显，模块只有一个默认的导出的接口，所以只有一个对象被导出，导出的对象可以自定义一个变量名。




##### 原文链接：[export和export default区别](https://www.jianshu.com/p/bcdaf7092baa)