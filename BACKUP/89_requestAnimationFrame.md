# [requestAnimationFrame](https://github.com/Twlig/issuesBlog/issues/89)

### requestAnimationFrame

告诉浏览器——你希望执行一个动画，并且要求浏览器在下次重绘之前调用指定的回调函数更新动画。该方法需要传入一个回调函数作为参数，该回调函数会在浏览器下一次重绘之前执行。

通俗点讲就是该API能以浏览器的显示频率来作为其动画动作的频率，比如浏览器每10ms刷新一次，动画回调也每10ms调用一次，这样就不会存在过度绘制的问题，动画不会掉帧，自然流畅。正常60Hz屏幕是16.7ms刷新。



#### requestAnimationFrame 宏任务？微任务？

requestAnimationFrame既不是宏任务也不是微任务。requestAnimationFrame的执行顺序：

- 同步代码
- 微任务
- requestAnimationFrame
- 宏任务

```javascript
setTimeout(()=>{console.log(1)}, 0)
Promise.resolve().then(()=> {console.log(2)})
requestAnimationFrame(() => {
 
    console.log(9);
 
});
Promise.resolve().then(()=>{console.log(3)})
var d1 = Date.now()
 for(let i = 0; i < 100000001; i++) {
     if(i == 100000000) {
         console.log(i)
     }
 }
var d2 = Date.now() - d1
/* 100000000
2
3
9
1*/
//d2 = 134ms
```

由上述结果可以看出，requestAnimationFrame确实是在微任务之后，宏任务之前执行。不独立。按理来说是16.7ms执行，但是超过了也并没有执行，还是会被同步代码阻塞。



参考文章：

- [[requestAnimationFrame 和 setTimeout 区别](https://juejin.cn/post/7078941440989986829)](https://juejin.cn/post/7078941440989986829)