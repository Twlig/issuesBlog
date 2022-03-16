# [实现sleep函数](https://github.com/Twlig/issuesBlog/issues/38)

### sleep函数

使计算机程序进入休眠。当函数设计的计时器到期，程序继续执行。

1. #### Promise.then

```javascript
function sleep(delay) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve();
        }, delay);
    })
}
sleep(1000).then(() => {
    console.log("sleep结束")
})
```

2. #### async await

```javascript
function sleep(delay) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve();
        }, delay);
    })
}
async function test() {
    console.log("开始");
    await sleep(1000);
    console.log("结束")
}
test();
```

