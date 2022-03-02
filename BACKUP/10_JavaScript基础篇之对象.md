# [JavaScript基础篇之对象](https://github.com/Twlig/issuesBlog/issues/10)

### 函数

函数是实际上是对象。每个函数都是Function类型的实例，而 Function 也有属性和方法，跟其他引用类型一样。因为函数是对象，所以函数名就是指向函数对象的指针。

#### 定义函数

1. **采用function定义**

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



