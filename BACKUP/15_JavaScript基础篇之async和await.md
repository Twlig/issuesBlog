# [JavaScript基础篇之async和await](https://github.com/Twlig/issuesBlog/issues/15)

ES8 的 async/await 旨在解决利用异步结构组织代码的问题。

### async

async 关键字用于声明异步函数。这个关键字可以用在函数声明、函数表达式、箭头函数和方法上：

```javascript
async function foo() {} 
let bar = async function() {}; 
let baz = async () => {}; 
class Qux { 
 async qux() {} 
}
```

- 异步函数如果使用 return 关键字返回了值（如果没有 return 则会返回 undefined），这个值会被 Promise.resolve()包装成一个期约对象
- 在异步函数中抛出(throw)错误会返回拒绝的期约可以被catch捕获
- 拒绝期约的错误不会被异步函数捕获

### await

使用 await关键字可以暂停异步函数代码的执行，等待期约解决。

- 会暂停执行await 关键字后面的代码。
- await 关键字必须在异步函数中使用

```javascript
async function foo() { 
 console.log(await Promise.resolve('foo')); 
} 
foo();
```

async/await 中真正起作用的是 await。async 关键字，无论从哪方面来看，都不过是一个标识符。异步函数如果不包含 await 关键字，其执行基本上跟普通函数没有什么区别。

- JavaScript 运行时在碰到 await 关键字时，会记录在哪里暂停执行。等到 await 右边的值可用了，JavaScript 运行时会向消息队列中推送一个任务，这个任务会恢复异步函数的执行。
- 即使 await 后面跟着一个立即可用的值，函数的其余部分也会被异步求值。

```java
async function foo() { 
 console.log(2); 
 await null; 
 console.log(4); 
} 
console.log(1); 
foo(); 
console.log(3); 
// 1 
// 2 
// 3 
// 4
```

控制台中输出结果的顺序很好地解释了运行时的工作过程：

1. 打印 1；
2. 调用异步函数 foo()；
3. （在 foo()中）打印 2；
4. （在 foo()中）await 关键字暂停执行，为立即可用的值 null 向消息队列中添加一个任务；
5. foo()退出；
6.  打印 3；
7. 同步线程的代码执行完毕；
8.  JavaScript 运行时从消息队列中取出任务，恢复异步函数执行；
9. 在（ foo()中）恢复执行，await 取得 null 值（这里并没有使用）；
10. （在 foo()中）打印 4；
11. foo()返回。

✨注意：先执行同步代码，再执行异步代码，异步代码有两类，一类微任务，一类宏任务。每次先执行微任务，全部微任务执行完，再执行一个宏任务。然后执行完宏任务，再在消息队列中执行所有微任务，再宏任务，如此循环。

微任务：`MutationObserver`、`Promise.then()或catch()`、`Promise为基础开发的其它技术，比如fetch API`、`V8`的垃圾回收过程、`Node独有的process.nextTick`。

宏任务：`script` 、`setTimeout`、`setInterval` 、`setImmediate` 、`I/O` 、`UI rendering`。

⚠️**注意**：在所有任务开始的时候，由于宏任务中包括了`script`，所以浏览器会先执行一个宏任务，在这个过程中你看到的延迟任务(例如`setTimeout`)将被放到下一轮宏任务中来执行。

题目集锦：

- [[【建议星星】要就来45道Promise面试题一次爽到底(1.1w字用心整理) - 掘金 (juejin.cn)](https://juejin.cn/post/6844904077537574919#heading-33%EF%BC%88%E6%8E%98%E9%87%91-%E9%9C%96%E5%91%86%E5%91%86%EF%BC%89)](https://juejin.cn/post/6844904077537574919#heading-33（掘金-霖呆呆）)