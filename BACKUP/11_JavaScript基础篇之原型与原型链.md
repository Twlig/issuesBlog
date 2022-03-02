# [JavaScript基础篇之原型与原型链](https://github.com/Twlig/issuesBlog/issues/11)

### 原型

每个**函数**都会创建一个 **prototype 属性**，这个属性是一个**对象**（默认是空的Object对象），包含应该由**特定引用类型**（构造函数创建）的**实例**共享的**属性和方法**。上面提到的**prototype属性**就是通过调用构造函数创建的**对象的原型**。也就是说，只要创建一个函数，就会按照特定的规则为这个函数创建一个 prototype 属性（指向原型对象）。

#### 函数的显式原型

如下所示，定义了一个Person函数，JS会为Person函数自动创建prototype属性（空Object对象），并prototype中添加constructor属性指向Person。prototype属性就是函数的显式原型。原型定义的属性和方法可以由所有实例共享。

``` javascript
function Person(){};	// 内部语句：this.prototype = {}
console.log(Person.prototype)  //每个函数function都有一个prototype，即显示原型属性，默认指向一个空的Object对象
/*  constructor: Person       
	[[Prototype]]: Object*/
```

JS内部操作类似于：

```javascript
Person.prototype = new Object();
Person.prototype.constructor = Person;
```

#### 对象实例的隐式原型

当对函数使用**new**关键字执行**new Person()**时，`Person`就成为了一个构造函数。而`let p = new Person()`创建出来的`p`就是`Person`的实例对象。实例对象有**`__proto__`属性**就是隐式原型，也就是实例的内部`[[Prototype]]`指针。实例对象有**`__proto__`属性**等于**构造函数的原型对象**（`Person.prototype`）。也就是说：

```java
//定义构造函数
function Person(){};
//1. 每个函数function都有一个prototype，即显示原型属性，默认指向一个空的Object对象
console.log(Person.prototype);
//2. prototype显示原型属性还有constructor属性指向构造函数本身
console.log(Person.prototype.constructor);  //Person
//3. 每个实例对象都有一个__proto__，称为隐式原型
let p = new Person();       // 内部语句： this.__proto__ = Person.prototype
//4. 对象的隐式原型的值为其对应的构造函数的显式原型的值
p.__proto__ == Person.prototype; //true
console.log(p.__proto__)
/*  constructor: Person
	[[Prototype]]: Object*/
Person.prototype.test = function() {
    console.log("test")
}
//通过实例调用原型方法
p.test()
/*5.总结：
    函数的prototype属性：定义函数时自动添加，默认为一个空的Object对象,有constructor属性指向构造函数本身
    对象的__proto__属性：创建对象时自动添加，默认值为构造函数的prototype属性值
    对象的隐式原型__proto__的值为其对应的构造函数的显式原型prototype的值
*/
```

此外，在实例对象上可以通过隐式原型访问到构造函数的原型，实例对象还能访问到实例的属性和方法。

```javascript
function Person(){
    this.name = 'zzy';
    this.sayName = function(){ console.log(this.name)};
};
Person.prototype.sayHi = ()=>{console.log('Hi')};
let p = new Person();
console.log(p.__proto__) // == Person.prototype
/*  constructor: Person
	sayHi: function
	[[Prototype]]: Object*/
console.log(p) //查看p能访问到的属性和方法
/*	name: "zzy"  
	sayName: ƒ ()
	[[Prototype]]: Object    也就是上面的p.__proto__，也对应这Person.prototype
        constructor: Person
        sayHi: function
        [[Prototype]]: Object	
	*/
```

**注意**，在构造函数`Person`中通过this创建的属性和方法，最后都是`p`实例的属性和方法和`Person`没有关系，因为在`p`被创建出来的时候调用`new Person()`，构造函数中的`this`指向的是当前实例`p`。

### 原型链

原型链基本思想就是通过**原型**继承多个引用类型的属性和方法。实例对象的隐式原型等于对应构造函数的显示原型。前面的例子中`Person`函数的`prototype`是默认的`Object`对象，我们也可以更换为别的自定义对象，扩展**原型链**。

```javascript
function Person(){};
Person.prototype.name = 'Person'
function Student(){};
Student.prototype = new Person();
Student.prototype.sayName = function(){  //注意此处不能用箭头函数，否则this指向window对象
    console.log(this.name); //指向Greg
};
let Greg = new Student();
Greg.sayName();  //Person
console.log(Greg);
/*
	[[Prototype]]: Object    也就是上面的Greg.__proto__，也对应这Student.prototype
		//无constructor，因为Student.prototype = new Person()把原本默认的prototype覆盖了
        sayName: function
        [[Prototype]]: Object	Greg.__proto__.__proto__,对应Person.prototype
        	name: 'Person'
        	constructor: Person
        	[[Prototype]]: Object	Greg.__proto__.__proto__.__proto__,Object.prototype
        		constructor: Object
        		__proto__: null
	*/
```

再附上一张究极神图，吃透了这张图原型链就差不多了：

![image](https://user-images.githubusercontent.com/22440467/156288352-75aace7f-55ac-4cc5-a975-f9d0f61bd07f.png)

```javascript
//1. 函数的显式原型指向对象默认是空Object实例对象（但Object不满足）
console.log(Foo.prototype instanceof Object) //true
console.log(Object.prototype instanceof Object) //false  很好理解Object已经是起源了那Object.prototype当然不是Object的实例，而是一部分
console.log(Function.prototype instanceof Object) //true
//2. 所有函数都是Function的实例（包括Function自身）
console.log(Function.__proto__ === Function.prototype) //true
//3. Object的原型对象是原型链的尽头
console.log(Object.prototype.__proto__) //null
function Foo() {}
var Foo = new Function();   //Foo.__proto__ = Function.prototype
Function = new Function();  //Function.__proto__ = Function.prototype
```

### 注意事项

- **原型赋值**

  ​	由于`prototype`是一个对象，因此在对`prototype`对象上进行属性和方法的添加时，采用对象方法赋值会新建对象，把原本指向旧地址的`prototype`指向新对象地址。

  ```javascript
  function Person(){};
  let p = new Person();
  Person.prototype.sayHi = ()=>{console.log('Hi')}; 
  p.sayHi(); //Hi
  ```

  ```java
  function Person(){};
  let p = new Person();
  Person.prototype = {
      sayHi: ()=>{console.log('Hi')},
      constructor: Person  //此外由于prototype被重新定义，也没有constructor属性了，必要时需要自己重写
  };
  p.sayHi(); //Uncaught TypeError: p.sayHi is not a function
  ```

  ​	可以看到第二个代码展示中，由于`p`先实例化，指向原先的`Person.prototype`（如地址`0x1234`），此时没有`sayHi`函数。后续采用对象赋值`Person.prototype`添加`sayHi`产生的是新的地址（`0x2345`）。

- **原型层级**

  ​	可以通过实例**读取原型对象上的值**，但不可能通过**实例重写**这些值（实例.`__proto__`.属性可以重写值）。只要给对象实例添加一个属性，就会在**实例上创建这个属性**，这个属性就会**遮蔽**原型对象上的同名属性，也就是虽然不会修改它，但会屏蔽对它的访问。

  ​	在通过**对象实例**访问属性时，会按照这个属性的名称开始搜索。搜索开始于**对象实例**本身，如果没有找到这个属性，则搜索会沿着**原型链**寻找，再返回对应的值。也就是实例——》实例.`__proto__`——》...——》null。

  ``` javascript
  function Person() {} 
  Person.prototype.name = "Nicholas"; 
  let person1 = new Person(); 
  let person2 = new Person(); 
  person1.name = "Greg"; 
  console.log(person1.name); // "Greg"，来自实例
  console.log(person2.name); // "Nicholas"，来自原型
  console.log(person1)
  /*  name: "Greg"    //实例属性name
  	[[Prototype]]: Object
  		name: "Nicholas"     //原型属性name
  		constructor: Person
  		[[Prototype]]: Object
  */
  person1.__proto__.name = "Greg"
  console.log(person2.name); // "Greg"，来自原型
  Person.prototype.colors = ["green", "red"];   
  person1.colors = ["pink","white"]; //引用值同样会遮蔽
  /*  name: "Greg"    //实例属性name
  	colors: ["pink","white"]
  	[[Prototype]]: Object
  		name: "Nicholas"     //原型属性name
  		colors: ["green", "red"]
  		constructor: Person
  		[[Prototype]]: Object
  */
  ```

- **原型的引用值属性**

  由于prototype是所有实例所共享的，在prototype上定义的引用值属性存在一定问题。上一个例子中`person1.name = "Greg"`是在实例上添加了

  ```javascript
  function Person(){};
  let p = new Person();
  Person.prototype = {
      colors: ['red', 'pink'],
  };
  let p1 = new Person();
  let p2 = new Person();
  p1.colors.push('black')   //['red', 'pink', 'black']
  p2.colors.push('green')	  //['red', 'pink', 'black', 'green']
  ```

  ​	可以看到`colors`被`p1`和`p2`所共享，对它的更改也是共享的。但有时候需要不同实例拥有独立的属性，这样做就存在问题，需要避免。

  ​	可能会存在疑问，前面讲原型层级时，不是说会存在遮蔽吗？这里的colors应该是实例上的属性呀。看代码：

  ``` javascript
  function Person(){};
  let p = new Person();
  Person.prototype = {
      colors: ['red', 'pink'],
      name: 'Greg'
  };
  let p1 = new Person();
  let p2 = new Person();
  p1.name = p1.__proto__.name;
  p1.colors = p1.__proto__.colors;
  p1.colors[0] = "white";
  p1.name = "zzy"
  console.log(p1)
  /*  name: "zzy"    //实例属性name
  	colors: ['white', 'pink']
  	[[Prototype]]: Object
  		name: "Greg"     //原型属性name
  		colors: ['white', 'pink']
  		constructor: Person
  		[[Prototype]]: Object
  */
  p1.colors = ["blue","yellow"];
  p1.colors.push("green");
  console.log(p1)
  /*  name: "zzy"    //实例属性name
  	colors: ['blue', 'yellow', 'green']
  	[[Prototype]]: Object
  		name: "Greg"     //原型属性name
  		colors: ['white', 'pink']
  		constructor: Person
  		[[Prototype]]: Object
  */
  ```

  - ​	`p1.colors.push('black')`，仅仅是操作原型上的`colors`对象，没有生成新的实例属性。
  - 通过`p1.colors =xxx `的操作才会让实例生成新的属性遮蔽原本的原型属性。
    - 而通过简单的`p1.colors = p1.__proto__.colors` 生成的实例属性，仅仅是把原型的`colors`地址值赋值了，并没有新建对象，还是指向原型的`colors`数组。
    - `p1.colors = ["blue","yellow"]`这种赋值操作 等价于`p1.colors = new Array("blue","yellow")`，会让`p1.colors`指向新的对象，就切断了和原型`colors`的联系。

