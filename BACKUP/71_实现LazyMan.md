# [实现LazyMan](https://github.com/Twlig/issuesBlog/issues/71)

**题目**：实现LazyMan

```javascript
LazyMan('Tony');
// Hi I am Tony

LazyMan('Tony').sleep(10).eat('lunch');
// Hi I am Tony
// 等待了10秒...
// I am eating lunch

LazyMan('Tony').eat('lunch').sleep(10).eat('dinner');
// Hi I am Tony
// I am eating lunch
// 等待了10秒...
// I am eating diner

LazyMan('Tony').eat('lunch').eat('dinner').sleepFirst(5).sleep(10).eat('junk food');
// Hi I am Tony
// 等待了5秒...
// I am eating lunch
// I am eating dinner
// 等待了10秒...
// I am eating junk food
```

**解题思路**：主要思想就是采用一个taskList数组记录事件触发函数以及触发的顺序，eat是按照顺序push进数组，sleepFirst是unshift，sleep也是按顺序push。在构造方法中通过添加宏任务，使得最后调用next函数，一一触发之前记录的事件。

```javascript
class LazyManClass { 
    constructor(name) {
        this.taskList = []
        this.name = name
        console.log(`I am ${this.name}`)
        setTimeout(()=>{    //设置宏任务，等待eat，sleep等函数被调用之后再触发next
            this.next()
        },0)
    }
    eat(name) {
        let that = this
        let fn = (function (name) {
            return function() {
                console.log(`I eat ${name}`)
                that.next()
            }
        })(name)
        this.taskList.push(fn)
        return this
    }
    sleepFirst(time) {
        let that = this
        let fn = (function(time) {
            return function() {
                setTimeout(()=>{
                    console.log(`sleep first ${time}`)
                    that.next()
                }, time*1000)
            }
        })(time)
        this.taskList.unshift(fn)
        return this
    }
    sleep(time) {
        let that = this
        let fn = (function(time) {
            return function() {
                setTimeout(()=>{
                    console.log(`sleep ${time}`)
                    that.next()
                },time*1000)
            }
        })(time)
        this.taskList.push(fn)
        return this
    }
    next() {
        let fn = this.taskList.shift()
        fn && fn()
    }
}

function LazyMan(name) {
    return new LazyManClass(name)
}
```



```javascript
LazyMan('Tony').eat('lunch').eat('dinner').sleepFirst(5).sleep(10).eat('junk food')
/* I am Tony
sleep first 5
I eat lunch
I eat dinner
sleep 10
I eat junk food*/
```

