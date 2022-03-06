# [JavaScript基础篇之setTimeout和setInterval](https://github.com/Twlig/issuesBlog/issues/17)

### setTimeout

**作用：**`setTimeout()`用于指定在一定时间后执行某些代码

**用法：**接收两个参数，要执行的代码和在执行回调函数前等待的时间（**毫秒**）

- 参数一：包含 JavaScript 代码的字符串（类似于传给 eval()的字符串）或者一个函数
- 参数二：要等待的毫秒数，而不是要执行代码的确切时间

✨**注意**：**`setTimeout()`的第二个参数只是告诉 JavaScript 引擎在指定的毫秒数过后把任务添加到这个队列。如果队列是空的，则会立即执行该代码。如果队列不是空的，则代码必须等待前面的任务执行完才能执行。**

**返回值：**调用 `setTimeout()`时，**会返回一个表示该超时排期的数值 ID**。这个超时 ID 是被排期执行代码的唯一标识符，可**用于取消该任务**。

### clearTimeout

要取消等待中的排期任务，可以调用 `clearTimeout()`方法并传入超时 ID

```
// 设置超时任务
let timeoutId = setTimeout(() => alert("Hello world!"), 1000); 
// 取消超时任务
clearTimeout(timeoutId);
```

### setInterval

**作用：**`setInterval()`用于指定每隔一段时间执行某些代码。**直到取消循环定时或者页面卸载。**

**用法：**`setInterval()`与 `setTimeout()`的使用方法类似，接收两个参数，要执行的代码和在执行回调函数前等待的时间（**毫秒**）

```javascript
setInterval(() => alert("Hello world!"), 3000);
```

> ✨**注意**：第二个参数，也就是间隔时间，指的是向队列添加新任务之前等待的时间。比如，调用 `setInterval()`的时间为 01:00:00，间隔时间为 3000 毫秒。这意味着 01:00:03 时，浏览器会把任务添加到执行队列。浏览器不关心这个任务什么时候执行或者执行要花多长时间。因此，到了 01:00:06，它会再向队列中添加一个任务。由此可看出，执行时间短、非阻塞的回调函数比较适合 `setInterval()`。

**返回值：** **会返回一个循环定时的数值 ID**。要取消循环定时，可以调用 clearInterval()并传入定时 ID。

### clearInterval

```javascript
let num = 0, intervalId = null, max = 10;
let incrementNumber = function() {
    num++;
    if(num == max) {   //达到最大值则取消定时任务
        clearInterval(intervarId);
        alert("Done");
    }
}
intervarId = setInterval(incrementNumber, 500);
```

在这个例子中，变量 num 会每半秒递增一次，直至达到最大限制值。此时循环定时会被取消。这个模式也可以使用 setTimeout()来实现，比如：

```javascript
let num = 0, max = 10;
let incrementNumber = function() {
    num++;
    if(num < max) {
        setTimeout(incrementNumber, 500);
    }else {  //不调用自身了，循环结束，无需清理定时器
        alert("Done");
    }
}
setTimeout(incrementNumber, 500);
```

