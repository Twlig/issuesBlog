# [require和import的区别](https://github.com/Twlig/issuesBlog/issues/87)

## require和import的区别

1. 导入`require` 导出 `exports/module.exports` 是 `CommonJS` 的标准，通常适用范围如 `Node.js`
2. `import/export` 是 `ES6` 的标准，通常适用范围如 `React`
3. `require` 是**赋值过程**并且是**运行时才执行**，也就是*同步加载*
4. `require` 可以理解为一个全局方法，因为它是一个方法所以意味着可以在任何地方执行。
5. `import` 是**解构过程**并且是**编译时执行**，理解为*异步加载*
6. `import` 会提升到整个模块的头部，具有置顶性，但是建议写在文件的顶部。

> `commonjs` 输出的，是一个值的拷贝，而`es6`输出的是值的引用；

> `commonjs` 是运行时加载，`es6`是编译时输出接口；

## require和import的性能

`require` 的性能相对于 `import` 稍低。

因为 `require` 是在运行时才引入模块并且还赋值给某个变量，而 `import` 只需要依据 `import` 中的接口在编译时引入指定模块所以性能稍高




参考文章：

- [require和import的区别](https://juejin.cn/post/7014011266796617736)