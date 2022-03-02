# [JavaScript基础篇之函数](https://github.com/Twlig/issuesBlog/issues/12)

### 函数

函数是实际上是对象。每个函数都是Function类型的实例，而 Function 也有属性和方法，跟其他引用类型一样。因为函数是对象，所以函数名就是指向函数对象的指针。

#### 定义函数

1. **采用function关键字定义函数**

   - **函数声明**

     ``` javascript
     function sum (num1, num2) { 
      return num1 + num2; 
     }
     ```

   - **函数表达式**

     ```javascript
     let sum = function (num1, num2) { 
      return num1 + num2; 
     }
     ```

2. **箭头函数**

   ```javascript
   let sum = (num1, num2) => { 
    return num1 + num2; 
   };
   ```

   **使用事项：**

   - 如果只有一个参数，那也可以不用括号。只有没有参数，或者多个参数的情况下，才需要使用括号：

   ```javascript
   // 以下两种写法都有效
   let double = (x) => { return 2 * x; }; 
   let triple = x => { return 3 * x; };
   // 没有参数需要括号
   let getRandom = () => { return Math.random(); }; 
   // 多个参数需要括号
   let sum = (a, b) => { return a + b; }; 
   // 无效的写法：多参数需要括号
   let multiply = a, b => { return a * b; };
   ```

   - 箭头函数也可以不用大括号，但这样会改变函数的行为。

     ```javascript
     // 以下两种写法都有效，而且返回相应的值
     let double = (x) => { return 2 * x; }; 
     let triple = (x) => 3 * x; 
     // 可以赋值
     let value = {}; 
     let setName = (x) => x.name = "Matt"; 
     setName(value); 
     console.log(value.name); // "Matt" 
     // 无效的写法： 不需要return
     let multiply = (a, b) => return a * b;
     //正确写法，省略大括号会隐式返回这行代码的值
     let multiply = (a, b) =>  a * b;
     ```

#### 函数事项

1. **函数名**

   因为函数名就是**指向函数的指针**，所以它们跟其他包含对象指针的变量具有相同的行为。

   - 多个名称

     ```javascript
     function sum(num1, num2) { 
      return num1 + num2; 
     } 
     console.log(sum(10, 10)); // 20 
     let anotherSum = sum; 
     console.log(anotherSum(10, 10)); // 20 
     sum = null;   //只是把指向函数的sum指针置空了
     console.log(anotherSum(10, 10)); // 20，anotherSum还是指向函数地址
     ```

   - name属性

     ECMAScript 6 的所有函数对象都会暴露一个只读的 name 属性，其中包含关于函数的信息。多数情

     况下，这个属性中保存的就是一个函数标识符，或者说是一个字符串化的变量名。即使函数没有名称，

     也会如实显示成空字符串。如果它是使用 Function 构造函数创建的，则会标识成"anonymous"：

     ```javascript
     function foo() {} 
     let bar = function() {}; 
     let baz = () => {}; 
     console.log(foo.name); // foo 
     console.log(bar.name); // bar 
     console.log(baz.name); // baz 
     console.log((() => {}).name); //（空字符串）
     console.log((new Function()).name); // anonymous
     ```

     如果函数是一个获取函数、设置函数，或者使用 bind()实例化，那么标识符前面会加上一个前缀：

     ```javascript
     function foo() {} 
     console.log(foo.bind(null).name); // bound foo
     let dog = { 
      years: 1, 
      get age() { 
      return this.years; 
      }
     } 
     let propertyDescriptor = Object.getOwnPropertyDescriptor(dog, 'age'); 
     console.log(propertyDescriptor.get.name); // get age
     ```

2. **参数**

   ECMAScript 函数的参数跟大多数其他语言不同。ECMAScript 函数既**不关心传入的参数个数**，也不关心这些参数的数据类型。定义函数时要接收两个参数，并不意味着调用时就传两个参数。你可以传一个、三个，甚至一个也不传，解释器都不会报错。主要是因为 ECMAScript **函数的参数在内部表现为一个数组**。函数被调用时总会接收一个数组，但函数并不关心这个数组中包含什么。

   在使用 function 关键字定义**（非箭头）**函数时，可以在函数内部访问 arguments 对象（**箭头函数没有该对象**），从中取得传进来的每个参数值。

   **arguments 对象是一个类数组对象**（但不是 Array 的实例），因此可以使用中括号语法访问其中的元素（第一个参数是 arguments[0]，第二个参数是 arguments[1]）。而要确定传进来多少个参数，可以访问 arguments.length 属性。

   ```javascript
   function sayHi(name, message) { 
    console.log("Hello " + name + ", " + message); 
   } 
   \\可以通过 arguments[0]取得相同的参数值。因此，把函数重写成不声明参数也可以：
   function sayHi() { 
    console.log("Hello " + arguments[0] + ", " + arguments[1]); 
   }
   ```

   **注意**：箭头语法定义的函数，将不能使用 arguments 关键字访问参数，而只能通过定义的命名参数访问。

3. **没有重载**

   ECMAScript 函数没有签名，因为参数是由包含零个或多个值的数组表示的。没有函数签名（接收参数的类型和数量），自然也就没有重载。如果在 ECMAScript 中定义了两个同名函数，则**后定义的会覆盖先定义的**。

4. **默认参数值**

   ```javascript
   function makeKing(name = 'Henry') { 
    return `King ${name} VIII`; 
   } 
   console.log(makeKing('Louis')); // 'King Louis VIII' 
   console.log(makeKing()); // 'King Henry VIII'
   ```

   **注意**：修改命名参数也不会影响 arguments 对象，它始终以调用函数时传入的值为准

   ```javascript
   function makeKing(name = 'Henry') { 
    return `King ${name} VIII`; 
   } 
   console.log(makeKing('Louis')); // 'King Louis VIII' 
   console.log(makeKing()); // 'King Henry VIII'
   ```

5. **默认参数作用域与暂时性死区**

   给多个参数定义默认值实际上跟使用 let 关键字顺序声明变量一样。

   ```javascript
   function makeKing(name = 'Henry', numerals = 'VIII') { 
    return `King ${name} ${numerals}`; 
   }
   //等价于
   function makeKing() { 
    let name = 'Henry'; 
    let numerals = 'VIII'; 
    return `King ${name} ${numerals}`; 
   }
   ```

   - 参数是按顺序初始化的，所以后定义默认值的参数**可以引用先定义的参数**。	

     ```javascript
     function makeKing(name = 'Henry', numerals = name) { 
      return `King ${name} ${numerals}`;
     }
     ```

   - 参数初始化顺序遵循“暂时性死区”规则，即前面定义的参数**不能引用后面定义**的。

     ```javascript
     // 调用时不传第一个参数会报错
     function makeKing(name = numerals, numerals = 'VIII') { 
      return `King ${name} ${numerals}`; 
     }
     ```

   - 参数也存在于自己的作用域中，它们**不能引用函数体的作用域**

     ```javascript
     // 调用时不传第二个参数会报错,因为默认的numerals值在函数体内
     function makeKing(name = 'Henry', numerals = defaultNumeral) { 
      let defaultNumeral = 'VIII'; 
      return `King ${name} ${numerals}`; 
     }
     ```

6. **函数声明与函数表达式**

   **函数声明**会在任何代码执行之前先被读取并**添加到执行上下文**。这个过程叫作**函数声明提升**。

   ```javascript
   // 没问题 
   console.log(sum(10, 10)); 
   function sum(num1, num2) { 
    return num1 + num2; 
   }
   ```

   **函数表达式**

   ```javascript
   // 会出错
   console.log(sum(10, 10)); //Uncaught ReferenceError: sum is not defined 
   let sum = function(num1, num2) { 
    return num1 + num2; 
   };
   ```

   函数定义包含在一个变量初始化语句中，而不是函数声明中。这意味着代码如果没有执行到加粗的那一行，那么执行上下文中就没有函数的定义。换成var也是存在错误，不过由于var的变量提升错误类型不一样了。sum能找到，但是是`undefined`，因此会是`TypeError`。

   ```javascript
   console.log(sum(10, 10)); //Uncaught TypeError: sum is not a function
   let sum = function(num1, num2) { 
    return num1 + num2; 
   };
   ```

   **注意**：`ReferenceError`就是在作用域中找不到、`TypeError`是在作用域中找到了但是 做了它不可能做的事情。

7. **函数内部**

   - **arguments**

     arguments对象只有以 **function 关键字定义函数**（相对于使用箭头语法创建函数）时才会有。虽然主要用于包含函数参数，但 arguments 对象其实还有一个 **callee 属性**，是一个**指向 arguments 对象所在函数**的指针。

     ```javascript
     function factorial(num) { 
      if (num <= 1) { 
      return 1; 
      } else { 
      return num * arguments.callee(num - 1); 
      } 
     }
     ```

   - **this**

     它在标准函数和箭头函数中有不同的行为。在标准函数中，this 引用的是**把函数当成方法调用的上下文对象**，这时候通常称其为 this 值（在网页的全局上下文中调用函数时，this 指向 windows）。

     1.**标准函数this指向：**

     - **windows对象**

       ```javascript
       var color = 'red';
       function sayColor() { 
        console.log(this.color); 
       } 
       sayColor(); // 全局上下文中调用this为window，'red'
       ```

       ```javascript
       function sayColor() { 
        (function (){console.log(this) })(); //windows
       } 
       //没有对象调用的函数，this指向就是windows
       ```

     - **普通实例对象**

       ```javascript
       window.color = 'red'; 
       let o = { 
        color: 'blue' 
       }; 
       function sayColor() { 
        console.log(this.color); 
       } 
       sayColor(); // 'red' 
       o.sayColor = sayColor; 
       o.sayColor(); // 'blue'
       ```

       因为`o.sayColor`函数中的this对象是o，沿着o的~~作用域链~~（原型链，注意此处o是对象访问不到window对象上的color）先找到的是o实例上的color属性。

     2.**箭头函数this指向：**

     在箭头函数中，this引用的是**定义箭头函数的上下文**。下面的例子演示了这一点。在对`sayColor()`的两次调用中，this 引用的都是 window 对象，因为这个箭头函数是在 window 上下文中定义的：

     ```javascript
     window.color = 'red'; 
     let o = { 
      color: 'blue' 
     }; 
     let sayColor = () => console.log(this.color); 
     sayColor(); // 'red' 
     o.sayColor = sayColor; 
     o.sayColor(); // 'red'
     ```

     &#x1F308;箭头函数妙用：

     ​	在**事件回调**或定时回调中调用某个函数时，**this 值指向的并非想要的对象**。此时将回调函数写成箭头函数就可以解决问题。这是因为箭头函数中的 this 会保留**定义该函数时的上下文**：

     ```javascript
     function King() { 
      this.royaltyName = 'Henry'; 
      // this 引用 King 的实例
      setTimeout(() => console.log(this.royaltyName), 1000); 
     }
     function Queen() { 
      this.royaltyName = 'Elizabeth'; 
      // this 引用 window 对象
      setTimeout(function() { console.log(this.royaltyName); }, 1000); 
     } 
     new King(); // Henry 
     new Queen(); // undefined
     ```

     此处箭头函数this指向定义时的上下文，~~也就保留着作用域链~~也就是King函数内部的属性和方法。前面作用域链删除是因为这个说法验证有问题，如果this指向定义时的上下文，那么这个King的上下文中的作用域链应该是保存了外部属性的，但其实this却访问不了，如下：

     ```javascript
     var age = 22;
     function King() { 
      this.royaltyName = 'Henry'; 
      // this 引用 King 的实例
      setTimeout(() => console.log(this.age), 1000); 
     }
     new King();  //undefined
     ```

     但是如果不采用this.age其实可以访问到，函数内部沿着作用域链可以找到age：

     ```javascript
     var age = 22;
     function King() { 
      this.royaltyName = 'Henry'; 
      // this 引用 King 的实例
      setTimeout(() => console.log(age), 1000); 
     }
     new King();  //undefined
     ```

     也就是说箭头函数this指向定义时的上下文就只是保留了箭头函数**外部一级函数**作用域内的属性和方法。如下，只能访问到F1内部。

     ``` javascript
     var age = 22;
     function King() { 
         this.royaltyName = 'Henry'; 
         function F1() {
         // this 引用 King 的实例
          setTimeout(() => console.log(this.royaltyName), 1000); 
         }
         F1();
     }
     new King(); //undefined
     ```

   3. **caller**

        这个属性引用的是调用当前函数的函数，或者如果是在**全局作用域中调用**的则为 null。

        ``` javascript
        function outer() { 
         inner(); 
        } 
        function inner() { 
         console.log(arguments.callee.caller); 
        } 
        outer(); // outer
        ```

   4. **new.target**

        ECMAScript 6 新增了检测函数是否使用 new 关键字调用的 new.target 属性。如果函数是正常调用的，则 new.target 的值是 undefined；如果是使用 new 关键字调用的，则 new.target 将引用被调用的构造函数。

        ```javascript
        function King() { 
         if (!new.target) { 
         throw 'King must be instantiated using "new"' 
         } 
         console.log('King instantiated using "new"'); 
        } 
        new King(); // King instantiated using "new" 
        King(); // Error: King must be instantiated using "new"
        ```

8. **闭包**

   闭包指的是那些**引用了另一个函数作用域中变量**的**函数**，通常是在嵌套函数中实现的。

   - 调用一个函数时，会为这个函数调用创建一个执行上下文，并创建一个作用域链。
   - 然后用 arguments和其他命名参数来初始化这个函数的活动对象。
   - 外部函数的**活动对象**是内部函数**作用域链上的第二个对象**。
   - 内部函数作用域链上的第一个对象是自身内部的属性和方法。
   - 这个作用域链一直向外串起了所有包含函数的活动对象，直到全局执行上下文才终止。

   全局上下文中的叫**变量对象**，它会在代码执行期间始终存在。而函数局部上下文中的叫**活动对象**，只在函数执行期间存在。

   如下代码：

   ```javascript
   function createComparisonFunction(propertyName) { 
    return function(object1, object2) { 
    let value1 = object1[propertyName]; 
    let value2 = object2[propertyName]; 
    if (value1 < value2) { 
    return -1; 
    } else if (value1 > value2) { 
    return 1; 
    } else { 
    return 0; 
    } 
    }; 
   }
   let compare = createComparisonFunction('name'); 
   let result = compare({ name: 'Nicholas' }, { name: 'Matt' });
   ```

   ![image](https://user-images.githubusercontent.com/22440467/156366385-d7abdeae-9d7b-445a-b3f2-5d62ec9c3e1f.png)

