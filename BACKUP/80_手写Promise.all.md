# [手写Promise.all](https://github.com/Twlig/issuesBlog/issues/80)

## 手写Promise.all


查看参考文章后，改进的代码：

```javascript
var all = function(arr) {
    let result = new Array(arr.length)
    let count = 0 //注意这里通过一个变量取保存它的成功的数量
    return new Promise((resolve, reject) => {
        arr.forEach((item, index) => {
            Promise.resolve(item).then(value => {  //用Promise去包装item，防止item不是Promise无法调用then函数。前面的Promise执行完成才会通过then把执行后的value放入result中
                result[index] = value
                count++
                if(count === arr.length) { //通过conter变量比较，而不是直接通过result.length去判断
                    resolve(result)
                }
            }, err => {
                reject(err)
            })
        })
    })
} 
```

参考文章代码如下：

```javascript
var all = (arr) => {
    console.log('my all called');
    let result = new Array(arr.length);
    let counter = 0;//注意这里通过一个变量取保存它的成功的数量
    return new Promise((resolve, reject) => {
        arr.forEach(async (item, index) => {
            let i = index;
            Promice.resolve(item).then(value => { 
                result[i] = value;
                counter ++;
                if (counter === arr.length) {//通过conter变量比较，而不是直接通过result.length去判断
                    resolve(result);
                }
            }).catch(err => {
                reject(err)
            })
        })
    });
}


var p = all([ 
    Promise.resolve(18),
    Promise.reject(5),
    Promise.reject(10),
    Promise.resolve(20),
    Promise.resolve(30)
]); 

/*18
20
30
5
10*/
```

上面的Promise.all会出现执行顺序的问题，如果都是resolve那没问题，但是如果有一个是reject，那么它的顺序会在所有resolve的后面，这是因为**在then中的reject会被后续的catch捕获**。过程如下：

- Promise.resolve(18).then加入微任务队列
  Promise.reject(5).then加入微任务队列
  Promise.reject(10).then加入微任务队列
  Promise.resolve(20).then加入微任务队列
  Promise.resolve(30).then加入微任务队列
- 运行Promise.resolve(18).then的resolve回调函数，处理完成
- 运行Promise.reject(5).then的resolve回调函数，发生reject，被catch捕获，catch(5)加入微任务队列
- 运行Promise.reject(10).then的resolve回调函数，发生reject，被catch捕获，catch(10)加入微任务队列
- 运行Promise.resolve(20).then的resolve回调函数，处理完成
- 运行Promise.resolve(30).then的resolve回调函数，处理完成
- 从微任务中取出catch(5)运行
- 从微任务中取出catch(10)运行





参考文章：

- [手写Promise](https://zoyi14.smartapps.cn/pages/note/index?origin=share&slug=12a38a723938&_swebfr=1&_swebFromHost=baiduboxapp)