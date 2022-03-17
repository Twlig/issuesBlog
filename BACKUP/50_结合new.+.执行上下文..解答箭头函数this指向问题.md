# [结合new + 执行上下文  解答箭头函数this指向问题](https://github.com/Twlig/issuesBlog/issues/50)


问题：
```javascript
function Person(){
    (()=> {console.log(this)})();
}
Person(); //window 
new Person();//Person{}. 
```
为何Person()指向window，new Person()指向Person对象？

根据**箭头函数的this指向定义时的上下文的this**

- Person()

  ```javascript
      ECStack = [  //执行上下文栈
          PersonContext,
          globalContext
      ];
  
      globalContext = { //全局上下文
          VO: [global],
          Scope: [globalContext.VO],
          this: globalContext.VO
      }
      PersonContext = { //Person上下文
          AO: {  //活动对象
              arguments: {
                  length: 0
              },
              scope: undefined,
              f: reference to function f(){}
          },
          Scope: [AO, globalContext.VO],  //作用域，包括Person活动对象和全局对象
          this: undefined  //重点，因为没有new，所以this是undefined
      }
  ```

  定义时的上下文是PersonContext，但是由于PersonContext的this没有定义，因此采用上一级也就是globalContext的this，即window。由于**箭头函数的this指向定义时的上下文的this**，因此箭头函数的this指向window。

- new Person()

  ```javascript
      ECStack = [  //执行上下文栈
          PersonContext,
          globalContext
      ];
  
      globalContext = { //全局上下文
          VO: [global],
          Scope: [globalContext.VO],
          this: globalContext.VO
      }
      PersonContext = { //Person上下文
          AO: {  //活动对象
              arguments: {
                  length: 0
              },
              scope: undefined,
              f: reference to function f(){}
          },
          Scope: [AO, globalContext.VO],  //作用域，包括Person活动对象和全局对象
          this: Person  //重点，new，所以this是Person
      }
  ```

  定义时的上下文是PersonContext，PersonContext的this指向Person实例。由于**箭头函数的this指向定义时的上下文的this**，因此箭头函数的this指向Person实例。

