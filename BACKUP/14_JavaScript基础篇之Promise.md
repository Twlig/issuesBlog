# [JavaScript基础篇之Promise](https://github.com/Twlig/issuesBlog/issues/14)

### Promise

Promise（期约）是一种封装未来值的易于复用的异步任务管理机制，可以有效解决回调地狱问题。

Promise是异步编程的一种解决方案，可以替代传统的解决方案--回调函数和事件。作为对象，Promise有以下两个特点： （1）对象的状态不受外界影响（2）只要从待定转换为兑现或拒绝，期约的状态就不再改变。

### 状态

Promise是一个有状态的对象，有三种状态：pending（ 待定 ）、resolved（完成）、rejected（拒绝）

```
- 等待（pending）：初始状态；
- 已完成（fulfilled/resolved）：操作成功完成；
- 被拒绝（rejected）：操作失败；
```

 - 待定（pending）是Promise的最初始状态。
 - 在待定状态下，Promise可以落定为代表成功的fulfilled，或者代表失败的拒绝rejected。
 - 无论落定为哪种状态都是不可逆的。只要从待定转换为兑现或拒绝，Promise的状态就不再改变。

### Promise用法

Promise可以通过Promise构造函数，创建Promise对象，构造函数参数传入一个执行器函数。如下：

``` javascript
var promise = new Promise((resolve, reject) => {})  //pending状态
```

其中`(resolve, reject) => {}`就是执行器函数，主要用于初始化Promise的异步行为和控制Promise状态的最终转换。执行器函数需要传入两个参数resolve和reject。

该函数的两个参数是resolve和reject，它们由JavaScript引擎提供。

- resolve函数
  作用是当Promise对象转移到成功态时，调用resolve函数并将操作结果作为其参数传递进去
- reject函数
  作用是当Promise对象的状态变为失败时，将操作报出的错误作为其参数传递出去

```javascript
var promise = new Promise((resolve, reject) => {
    resolve("success")
})
var p2 = promise.then(x => console.log(x))   //success
p2.then(() => Promise.reject("error")).catch((e) => console.log(e)) //error
```

### Promise静态方法

1. **Promise.resolve()**

   该方法可以实例化一个解决的期约。下面两个期约实例实际上是一样的:

   ```javascript
   let p1 = new Promise((resolve, reject) => resolve("success")); 
   let p2 = Promise.resolve("success");
   ```

   此外，该方法是幂等方法：

   ```javascript
   let p = Promise.resolve("success");
   p === Promise.resolve(Promise.resolve(p); //true
   ```

2. **Promise.reject()**

   实例化一个拒绝的期约并抛出一个异步错误（这个错误不能通过 try/catch 捕获，而只能通过拒绝处理程序捕获）。下面两个语句等价：

   ```javascript
   let p1 = new Promise((resolve, reject) => reject("error")); 
   let p2 = Promise.reject("error");
   ```

   `Promise.reject()`并**没有幂等逻辑**。如果给它传一个期约对象，则这个期约会成为它返回的拒绝期约的理由：

   ```javascript
   setTimeout(console.log, 0, Promise.reject(Promise.resolve())); 
   // Promise <rejected>: Promise <resolved>
   ```

   ✨**注意：**

   Promise的错误不能通过 try/catch 捕获，而只能通过拒绝处理程序捕获。这是因为try/catch 是同步代码不能捕获Promise抛出的错误，只有异步模式才能捕获Promise错误。

   ```javascript
   try { 
    Promise.reject(new Error('bar')); 
   } catch(e) { 
    console.log(e); 
   } 
   // Uncaught (in promise) Error: bar
   ```

   拒绝Promise的错误并没有抛到执行同步代码的线程里，而是通过浏览器异步消息队列来处理的。因此，try/catch 块并不能捕获该错误。代码一旦开始以异步模式执行，则唯一与之交互的方式就是使用异步结构——更具体地说，就是Promise的方法。

3. **Promise.all()**

   Promise.all()静态方法创建的期Promise在一组期约全部解决之后再解决。这个静态方法接收一个可迭代对象，

   - 返回一个新期约

   - 合成的期约只会在每个包含的期约都解决之后才解决

   - 如果所有期约都成功解决，则合成期约的解决值就是所有包含期约解决值的数组，按照迭代器顺序

   - 如果有一个包含的期约拒绝，则合成的期约也会拒绝

   - 如果有期约拒绝，则第一个拒绝的期约会将自己的理由作为合成期约的拒绝理由。之后再拒绝的期

     约不会影响最终期约的拒绝理由

   ```javascript
   let p = Promise.all([ 
    Promise.resolve(), 
    new Promise((resolve, reject) => setTimeout(resolve, 1000)) 
   ]); 
   setTimeout(console.log, 0, p); // Promise <pending> 
   p.then(() => setTimeout(console.log, 0, 'all() resolved!')); 
   // all() resolved!（大约 1 秒后）
   ```

4. **Promise.race()**

   Promise.race()静态方法返回一个包装期约，是一组集合中最先解决或拒绝的期约的镜像。这个方法接收一个可迭代对象，返回一个新期约：

   ```javascript
   let p1 = Promise.race([ 
    Promise.resolve(3), 
    new Promise((resolve, reject) => setTimeout(reject, 1000)) 
   ]); 
   setTimeout(console.log, 0, p1); // Promise <resolved>: 3
   ```

### Promise的实例方法

1. #### then()

   为Promise实例添加处理程序的主要方法。接收最多两个参数：onResolved 处理程序和 onRejected 处理程序，返回一个新的Promise实例。处理程序会在Promise分别进入“兑现”和“拒绝”状态时执行。因为Promise只能转换为最终状态一次，所以这两个操作一定是互斥的。

   ``` javascript
   function onResolved(id) { 
    setTimeout(console.log, 0, id, 'resolved');
   } 
   function onRejected(id) { 
    setTimeout(console.log, 0, id, 'rejected'); 
   } 
   let p1 = new Promise((resolve, reject) => setTimeout(resolve, 3000)); 
   let p2 = new Promise((resolve, reject) => setTimeout(reject, 3000)); 
   p1.then(() => onResolved('p1'), 
    () => onRejected('p1')); 
   p2.then(() => onResolved('p2'), 
    () => onRejected('p2')); 
   //（3 秒后）
   // p1 resolved 
   // p2 rejected
   ```

   - 传给 then()的任何非函数类型的参数都会被静默忽略。如果想只提供 onRejected 参数，那就要在onResolved 参数的位置上传入 undefined。

   ```javascript
   // 不传 onResolved 处理程序的规范写法
   p2.then(null, () => onRejected('p2'));
   ```

   - then()方法返回一个新的Promise实例：

   ```javascript
   let p1 = new Promise(() => {}); 
   let p2 = p1.then(); 
   setTimeout(console.log, 0, p1); // Promise <pending> 
   setTimeout(console.log, 0, p2); // Promise <pending> 
   setTimeout(console.log, 0, p1 === p2); // false
   ```

   - 若调用 then()时不传处理程序，则原样向后传

   ```javascript
   let p2 = p1.then(); 
   ```

   ✨**注意**：**onResolved 和onRejected 处理程序返回的值都会被 Promise.resolve()包装**

2. #### catch()

   用于给期约添加拒绝处理程序。这个方法只接收一个参数：onRejected 处理程序，返回一个新的Promise实例。事实上，这个方法就是一个语法糖，调用它就相当于调用 Promise.prototype.then(null, onRejected)

   ```javascript
   let p = Promise.reject(); 
   let onRejected = function(e) { 
    setTimeout(console.log, 0, 'rejected'); 
   }; 
   // 这两种添加拒绝处理程序的方式是一样的：
   p.then(null, onRejected); // rejected 
   p.catch(onRejected); // rejected
   ```

3. **finally()**

   finally()方法用于给期约添加 onFinally 处理程序，这个处理程序**在期约转换为解决或拒绝状态时都会执行**。方法返回一个新的期约实例:

   ```javascript
   let p1 = Promise.resolve(); 
   let p2 = Promise.reject(); 
   let onFinally = function() { 
    setTimeout(console.log, 0, 'Finally!') 
   } 
   p1.finally(onFinally); // Finally 
   p2.finally(onFinally); // Finally
   ```

   

