# [new到底做了什么？](https://github.com/Twlig/issuesBlog/issues/49)

要创建 Person 的新实例，必须使用 new 操作符。

```javascript
var obj = new Person();
```

new的过程中经历以下4个步骤：

(1) 创建一个空对象

```javascript
obj = new Object();
```

(2)设置原型链

```javascript
obj.__proto__ = Person.prototype;
```

(3)让Person中的this指向obj，并执行Person函数体

```javascript
Person.call(obj);
```

(4) 返回新对象
