# [模块导出module.exports和exports的区别](https://github.com/Twlig/issuesBlog/issues/5)

### module.exports和exports的区别

	module.exports和exports其实指向的是同一个对象`module.exports == exports //true`，但是在使用上存在一些不同。

- 通过exports只能使用`.`的方式来向外暴露内部变量
  - `exports.xxx=xxx`

- 而module.exports既可以通过`.`的形式，也可以直接赋值
  - `module.exports.xxx=xxx`
  - `module.exports = {}`

#### 案例

1. module.exports暴露变量

```
module.exports = {
Name: ‘zzy’,
Age: 22,
Say: function(name, age) {}
}
```

2. exports暴露变量

```
exports.name = ’zzy‘;
exports.age = 22;
exports.say = function(name, age){};
```

而如果对exports也采用`exports = {}`，则在引入模块后，调用`Name`等属性会发现是`undefined`

这就涉及到了原始值和引用值的不同

#### 原始值和引用值

1. 原始值

   ​	JS中基本数据类型：`Null,Undefined,Boolean,Number,String,Symbol`都是原始值，保存原始值是按值保存。即，在栈中开辟出空间，用来存放**变量名**以及其**对应的变量值**。

   ```
   var a = 10；
   var b = a;
   a++;
   ```

   <div align = center><img src="https://user-images.githubusercontent.com/22440467/155476890-a8340fb6-b0f3-493d-ae44-864297bbca86.png"></div>

   因此`a++`不会影响到`b`

2. 引用值

   引用值是指`Object`对象类型，该类型在存储的时候是在栈中存储**变量名**以及对应的**堆地址空间**

   ```
   var module= new Object();
   module.exports = {}; //0x1234
   var exports = module.exports; //0x1234
   exports.name = “zzy”; //0x1234
   //因为exports是对象，修改数据是在堆内存直接操作的，因此向exports变量添加name属性，也会指向的module堆内存。
   ```

   <div align = center><img src="https://user-images.githubusercontent.com/22440467/155477097-85bc8ad0-ef10-4383-b7e8-b85628484513.png" width=600></div>

   ```
   var module= new Object();
   module.exports = {}; //0x1234
   var exports = module.exports; //0x1234
   exports = {name: “zzy”,age:22};  //0x3456
   ```

   <div align = center><img src="https://user-images.githubusercontent.com/22440467/155477119-5b2d9c36-ac9b-402c-91ae-6a758c2a546e.png" width=600></div>

   ​	  exports会新建一个对象指向另一个堆内存空间，相当于切断了和module的联系。而node模块在导入的时候是采用module，所以在导入之后是去module.exports中找导出的变量和函数，也就是0x1234空间。使用exports={...}修改的是0x3456就无法成功导出。