# [JavaScript基础篇之变量与作用域](https://github.com/Twlig/issuesBlog/issues/9)

### 变量

ECMAScript 变量是松散类型的，意思是变量可以用于保存任何类型的数据。有 3 个关键字可以声明变量：var、const 和 let。其中，var 在ECMAScript 的所有版本中都可以使用，而 const 和 let 只能在 ECMAScript 6 及更晚的版本中使用。

#### var 关键字

- **var声明作用域**

  使用 var 操作符定义的变量会成为包含它的**函数**的局部变量，函数外部无法访问。

  ```javascript
  function test() { 
   var message = "hi"; // 局部变量
  } 
  test(); 
  console.log(message); // 出错！
  ```

  此外，在函数内定义变量时省略 var 操作符，可以创建一个全局变量：

  ```javascript
  function test() { 
   message = "hi"; // 全局变量
  } 
  test(); 
  console.log(message); // "hi"
  ```

- **var声明提升**

  使用 var 时，下面的代码不会报错。这是因为使用**var关键字**声明的变量会自动提升到**函数作用域顶部**：

  ```javascript
  function foo() { 
   console.log(age); 
   var age = 26; 
  } 
  foo(); // undefined
  ```

  是因为 ECMAScript 运行时把它看成等价于如下代码：

  ``` javascript
  function foo() { 
   var age; //声明提升
   console.log(age); 
   age = 26; 
  } 
  foo(); // undefined 
  ```

- **全局声明**

  ```javascript
  var name = 'Matt'; 
  console.log(window.name); // 'Matt'
  ```

  使用 var在全局作用域中声明的变量会成为 window 对象的属性。let和const不行。

#### let关键字

let和var差不多，但是let 声明的范围是**块作用域**，而 var 声明的范围是**函数作用域**。

```javascript
if (true) { 
 let age = 26; 
 console.log(age); // 26 
} 
console.log(age); // ReferenceError: age 没有定义
```

age 变量之所以不能在 if 块外部被引用，是因为它的作用域仅限于该块内部。块作用域是函数作用域的子集，因此适用于 var 的作用域限制同样也适用于 let。

- **不允许冗余**

  ``` javascript
  let age; 
  let age; // SyntaxError；标识符 age 已经声明过了
  var name; 
  var name; //var可以
  ```

- **暂时性死区**

  ``` javascript
  // age 不会被提升
  console.log(age); // ReferenceError：age 没有定义
  let age = 26;
  ```

  let 与 var 的另一个重要的区别，就是 let 声明的变量不会在作用域中被提升。在 **let 声明之前**的执行瞬间被称为“**暂时性死区**”，在此阶段引用任何后面才声明的变量都会抛出 ReferenceError。

- **for 循环**

  使用 let 迭代变量不会渗透到循环体外部，因为迭代变量的作用域仅限于 for 循环块内部，弥补了var的不足。

  ``` javascript
  for (let i = 0; i < 5; ++i) { 
   // 循环逻辑
  } 
  console.log(i); // ReferenceError: i 没有定义
  ```

  var存在的问题：

  ```javascript
  for (var i = 0; i < 5; ++i) { 
   setTimeout(() => console.log(i), 0) 
  } 
  // 你可能以为会输出 0、1、2、3、4 , 实际上会输出 5、5、5、5、5
  ```

  在退出循环时，迭代变量保存的是导致循环退出的值：5。在之后执行超时逻辑时，所有的 i 都是同一个变量，因而输出的都是同一个最终值。

  使用 let 声明迭代变量时，JavaScript 引擎在后台会为每个迭代循环声明一个新的迭代变量。每个 setTimeout 引用的都是不同的变量实例:

  ``` javascript
  for (let i = 0; i < 5; ++i) { 
   setTimeout(() => console.log(i), 0) 
  } 
  // 会输出 0、1、2、3、4
  ```

#### const关键字

const 的行为与 let 基本相同，唯一一个重要的区别是用它声明变量时必须同时**初始化变量**，初始化后**不可修改**。

```javascript
const age = 26; 
age = 36; // TypeError: 给常量赋值
```

const 声明的限制只适用于它指向的变量的引用。如果 const 变量引用的是一个对象，**可修改对象内部属性**。

```javascript
const person = {}; 
person.name = 'Matt'; // ok
```

- for-in 循环

  ```javascript
  for (const key in {a: 1, b: 2}) { 
   console.log(key); 
  } 
  // a, b
  ```

- for-of 循环

  ```javascript
  for (const value of [1,2,3,4,5]) { 
   console.log(value); 
  } 
  // 1, 2, 3, 4, 5
  ```

### 原始值与引用值

ECMAScript 变量可以包含两种不同类型的数据：原始值和引用值。原始值就是最简单的数据，引用值则是由多个值构成的对象。

**原始值**：Undefined、Null、Boolean、Number、String 和 Symbol

**引用值**：Object

保存**原始值**的变量是**按值访问**的，因为我们操作的就是存储在变量中的实际值。**引用值**的变量是**按引用访问**的。引用值和原始值在使用过程中还存在一些不同，下面将详细展开：

- **动态属性**

  引用值可以添加、修改和删除其属性和方法，而原始值只能修改值

  ```javascript
  let person = new Object(); 
  person.name = "Nicholas"; 
  console.log(person.name); // "Nicholas"
  ```

- **复制值**

  - 原始值

    ```javascript
    let num1 = 5; 
    let num2 = num1;
    num1++; //num1 = 6, num2 = 5
    ```

    原始值赋值另一变量时，原始值会被复制到**新变量的位置**，两个变量相互独立。

  - 引用值

    ```javascript
    let obj1 = new Object(); 
    let obj2 = obj1; 
    obj1.name = "Nicholas"; 
    console.log(obj2.name); // "Nicholas"
    ```

    引用值在赋值的时候是复制引用地址，新变量也是指向obj1指向的堆地址。

- **传递参数**

  ECMAScript 中所有函数的参数（无论是原始值还是引用值）都是**按值传递**的。值会被复制到一个局部变量（即一个命名参数，是 arguments 对象中的一个槽位）

  - **传递原始值**

  ```javascript
  function addTen(num) { 
   num += 10; 
   return num; 
  } 
  let count = 20;
  let result = addTen(count); 
  console.log(count); // 20，没有变化
  console.log(result); // 30
  ```

  实际上在执行`addTen(count)`时，相当于

  ```javascript
  var num = count //把count值赋值给num，后续两个变量相互独立，num在函数结束时被销毁
  ```

  - **传递引用值**

  ```javascript
  function setName(obj) { 
   obj.name = "Nicholas"; 
  } 
  let person = new Object(); 
  setName(person); 
  console.log(person.name); // "Nicholas"
  ```

  此处person变化的主要原因是，参数obj复制person的引用值，因此修改参数obj时，相当于对内存地址做操作。但这并不是代表引用值参数传递是**按引用传递**。继续看后续

  ```javascript
  function setName(obj) { 
   obj.name = "Nicholas"; 
   obj = new Object(); 
   obj.name = "Greg";
  } 
  let person = new Object(); 
  setName(person); 
  console.log(person.name); // "Nicholas"，person并没指向新地址Greg
  ```

  现在obj就没有影响到person。如果 person 是按引用传递的，那么 person 应该自动将指针指向Greg

  在`setName(person)`执行时，它也相当于：

  ``` javascript
  var obj = person	//obj复制了person的引用值，后续函数执行结束，obj销毁，person不受影响
  ```
  <div argin=center><img src="https://user-images.githubusercontent.com/22440467/155715323-3435bfa0-4fd0-4f88-86cb-7779beb8ee08.png"></div>
``` javascript
   obj = new Object(); //如图所示，obj指向了新的堆空间
   obj.name = "Greg";	
```
  <div argin=center><img src="https://user-images.githubusercontent.com/22440467/155715354-4e98bfca-7019-4cfc-91ff-10ce045bdaaa.png"></div>

### 执行上下文与作用域

​	上下文其实就可以理解为代码运行所能访问的变量和函数环境。每个上下文都有一个关联的变量对象，上下文中定义的所有变量和函数都存在于这个对象上。**全局上下文**就是 window 对象，是**最外层的上下文**，所有通过 var 定义的全局变量和函数都会成为 window 对象的属性和方法。上下文在其所有代码都执行完毕后会被销毁，包括定义在它上面的所有变量和函数（全局上下文在应用程序退出前才会被销毁，比如关闭网页或退出浏览器）。

​	上下文中的代码在执行的时候，会创建变量对象的一个**作用域链**，这个作用域链决定了各级上下文中的代码在访问变量和函数时的顺序。

```javascript
var color = "blue"; 
function changeColor() { 
 let anotherColor = "red"; 
 function swapColors() { 
 let tempColor = anotherColor; 
 anotherColor = color; 
 color = tempColor; 
 // 这里可以访问 color、anotherColor 和 tempColor 
 } 
 // 这里可以访问 color 和 anotherColor，但访问不到 tempColor 
 swapColors(); 
} 
// 这里只能访问 color 
changeColor();
```

作用域链图：

<div argin=center><img src="https://user-images.githubusercontent.com/22440467/155717483-f1e4d55e-6569-4ef0-be34-992498aeafd7.png"></div>

**内部上下文**可以通过作用域链**访问外部**上下文中的一切，但**外部**上下文**无法访问内部**上下文中的任何东西。每个上下文都可以到上一级上下文中去搜索变量和函数，但任何上下文都不能到下一级上下文中去搜索。

如果在局部上下文中找到该标识符，则搜索停止，变量确定；如果没有找到变量名，则继续沿作用域链搜索。（注意，作用域链中的对象也有一个原型链，因此搜索可能涉及每个对象的原型链。）这个过程一直持续到搜索至全局上下文的变量对象。

```javascript
var color = 'blue'; 
function getColor() { 
 let color = 'red'; 
 return color; 
} 
console.log(getColor()); // 'red'
```

上下文之间的连接是线性的、有序的。找到了最近的就不会再往上去找了。