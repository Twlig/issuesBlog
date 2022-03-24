# [Array及常用方法](https://github.com/Twlig/issuesBlog/issues/25)

### Array

Array数组是一组有序的数据，数组中每个槽位可以**存储任意类型的数据**。这意味着可以创建一个数组，它的第一个元素是字符串，第二个元素是数值，第三个是对象。ECMAScript 数组也是动态大小的，会随着数据添加而自动增长。

1. #### 创建数组

   1. #####  Array 构造函数

      ```javascript
      let colors = new Array();   //不指定长度
      let colors = new Array(20);  //指定数组长度
      let colors = new Array("red", "blue", "green"); //给 Array 构造函数传入要保存的元素
      ```

   2. ##### 数组字面量表示法

      ```javascript
      let colors = ["red", "blue", "green"]; // 创建一个包含 3 个元素的数组
      let names = []; // 创建一个空数组
      let values = [1,2,]; // 创建一个包含 2 个元素的数组
      ```

2. #### Array静态函数

   - **Array.from()**

     from()用于将**类数组结构**转换为**数组实例**。

     接收三个参数：

     - 第一个参数：一个类数组对象，即任何可迭代的结构，或者有一个 length 属性和可索引元素的结构。
     - 第二个参数：映射函数参数。
     - 第三个参数：指定映射函数中 this 的值。（this在箭头函数中不适用）

     ```javascript
     // 一个参数， 字符串会被拆分为单字符数组
     console.log(Array.from("Matt")); // ["M", "a", "t", "t"]
     
     const a1 = [1, 2, 3, 4]; 
     //两个参数
     const a2 = Array.from(a1, x => x**2); 
     //三个参数
     const a3 = Array.from(a1, function(x) {return x**this.exponent}, {exponent: 2});
     //[1, 4, 9, 16]
     ```

   - **Array.of()**

     Array.of()可以把**一组参数**转换为**数组**。

     ```javascript
     console.log(Array.of(1, 2, 3, 4)); // [1, 2, 3, 4]
     ```

3. #### 检测数组

   - instanceof

     ```javascript
     if (value instanceof Array){ 
      // 操作数组
     }
     ```

   - Array.isArray()

     ```javascript
     if (Array.isArray(value)){ 
      // 操作数组
     }
     ```

4. #### 迭代器方法

   Array 的原型上暴露了 3 个用于检索数组内容的方法：keys()、values()和entries()。

   - keys()返回数组索引的迭代器
   - values()返回数组元素的迭代器
   - entries()返回索引/值对的迭代器

   ```javascript
   const a = ["foo", "bar", "baz", "qux"]; 
   // 因为这些方法都返回迭代器，所以可以将它们的内容
   // 通过 Array.from()直接转换为数组实例
   const aKeys = Array.from(a.keys()); 
   const aValues = Array.from(a.values()); 
   const aEntries = Array.from(a.entries()); 
   console.log(aKeys); // [0, 1, 2, 3] 
   console.log(aValues); // ["foo", "bar", "baz", "qux"] 
   console.log(aEntries); // [[0, "foo"], [1, "bar"], [2, "baz"], [3, "qux"]]
   ```

5. #### 复制和填充方法

   复制和填充方法都是指定既有数组实例上的一个范围，**包含开始索引，不包含结束索引**。

   - 复制方法copyWithin()

     copyWithin()会按照指定范围**浅复制**数组中的部分内容，然后将它们插入到指定索引开始的位置。

     接收三个参数：插入位置的索引，复制开始索引，复制结束索引

     ```javascript
     // 从 ints 中复制索引 0 开始到索引 3 结束的内容
     // 插入到索引 4 开始的位置
     var ints = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9];
     ints.copyWithin(4, 0, 3); 
     alert(ints); // [0, 1, 2, 3, 0, 1, 2, 7, 8, 9]
     ```

   - 填充方法fill()

     ```javascript
     const zeroes = [0, 0, 0, 0, 0];
     // 用 7 填充索引大于等于 1 且小于 3 的元素
     zeroes.fill(7, 1, 3); 
     console.log(zeroes); // [0, 7, 7, 0, 0]; 
     zeroes.fill(0); // 重置
     
     // 用 8 填充索引大于等于 1 且小于 4 的元素
     // (-4 + zeroes.length = 1) 
     // (-1 + zeroes.length = 4) 
     zeroes.fill(8, -4, -1); 
     console.log(zeroes); // [0, 8, 8, 8, 0];
     ```

     **fill()静默忽略超出数组边界、零长度及方向相反的索引范围.**

6. #### 栈方法

   栈是一种后进先出（LIFO，Last-In-First-Out）的结构，也就是最近添加的项先被删除。数据项的插入（称为推入，push）和删除（称为弹出，pop）只在栈顶。

   - push()

     接收**任意数量的参数**，并将它们添加到数组末尾，返回数组的最新长度。

     ```javascript
     let colors = new Array(); // 创建一个数组
     let count = colors.push("red", "green"); // 推入两项
     //["red","green"]
     ```

   - pop()

     **删除数组的最后一项**，同时减少数组的 length 值，返回被删除的项。

     ```javascript
     let ints = [1, 2, 3, 4];
     let ass = new Array(1, 2);
     ass.push(...ints); //[1,2,1,2,3,4]
     ass.pop(); //4  弹出4
     ```

7. #### 队列方法

   队列以先进先出（FIFO，First-In-First-Out）形式限制访问。队列在列表末尾添加数据，但从列表开头获取数据。push模拟在队尾进，shift模拟在头部出。

   - shift()

     它会删除数组的第一项并返回它，然后数组长度减 1。

     ```javascript
     let colors = new Array(); // 创建一个数组
     let count = colors.push("red", "green"); // 推入两项
     let item = colors.shift(); // 取得第一项 red
     //colors: ["green"]
     ```

   - unshift()

     unshift()执行跟 shift()相反的操作：在数组开头添加任意多个值，然后返回新的数组长度。

     ```javascript
     let colors = ["blue", "white"];
     let count = colors.unshift("red", "green");
     //colors:  ['red', 'green', 'blue', 'white']
     ```

8. #### 排序方法

   - reverse()

     reverse()方法就是将数组元素反向排列

     ```javascript
     let values = [1, 2, 3, 4, 5]; 
     values.reverse(); 
     alert(values); // 5,4,3,2,1
     ```

   - sort()

     默认情况下，sort()会按照升序重新排列数组元素，即最小的值在前面，最大的值在后面。

     - sort()会在每一项上调用 String()转型函数，然后**比较字符串来决定顺序**。
     - 即使数组的元素都是数值，也会先把数组转换为字符串再比较、排序。

     ```javascript
     let values = [0, 1, 5, 10, 15]; 
     values.sort(); 
     alert(values); // 0,1,10,15,5
     ```

     一开始数组中数值的顺序是正确的，但调用 sort()会按照这些数值的字符串形式重新排序。因此，即使 5 小于 10，但字符串"10"在字符串"5"的前头，所以 10 还是会排到 5 前面。很明显，这在多数情况下都不是最合适的。为此，sort()方法可以接收一个**比较函数**，**用于判断哪个值应该排在前面**。

     **比较函数接收两个参数**，如果**第一个参数应该排在第二个参数前面，就返回负值**；如果两个参数相

     等，就返回 0；**如果第一个参数应该排在第二个参数后面，就返回正值**。 （可以记成-1不交换，1交换）

     ```javascript
     let values = [0, 1, 5, 10, 15]; 
     values.sort((a,b) => a > b ? 1 : a < b ? -1 : 0);  //升序
     alert(values); // 0,1,5,10,15
     values.sort((a, b) => a > b ? -1 : a < b ? 1 : 0) //降序
     ```

9. #### 操作方法

   - concat()

     在现有数组全部元素基础上**创建一个新数组**。**（不影响原数组）**

     ```javascript
     //默认打平
     let colors = ["red", "green", "blue"]; 
     let colors2 = colors.concat("yellow", ["black", "brown"]); 
     console.log(colors); // ["red", "green","blue"] 
     console.log(colors2); // ["red", "green", "blue", "yellow", "black", "brown"]
     
     //不打平
     let newColors = ["black", "brown"];
     newColors[Symbol.isConcatSpreadable] = false;
     // 强制不打平数组
     let colors2 = colors.concat("yellow", newColors);
     console.log(colors2); // ["red", "green", "blue", "yellow", ["black", "brown"]]
     ```

   - slice()

     用于**创建**一个包含原有数组中一个或多个元素的新数组。**（不影响原数组）**

     接收：一个或两个参数

     - 只有一个参数，则 slice()会返回该索引到数组末尾的所有元素。
     - 有两个参数，则 slice()返回从开始索引到结束索引对应的所有元素，**不包含结束索引对应的元素**。

     返回：元素的开始索引到结束索引。

     ```javascript
     let colors = ["red", "green", "blue", "yellow", "purple"]; 
     let colors2 = colors.slice(1); // green,blue,yellow,purple
     let colors3 = colors.slice(1, 4);  // green,blue,yellow
     ```

   - splice()

     在数组中间插入元素。三种用法：

     - 删除。传 2 个参数：要删除的开始位置和要删除的元素数量。可以从数组中删除任意多个元素，比如 splice(0, 2)会删除前两个元素。**返回删除的元素**。只有删除返回。

       ```javascript
       let colors = ["red", "green", "blue"]; 
       let removed = colors.splice(0,1); // 删除从0位置开始的1个元素，即第一项
       //removed = ["red"]
       alert(colors); // green,blue
       ```

     - 插入。需要给 splice()传 3 个参数：开始位置、0（要删除的元素数量）和要插入的元素，可以在数组中指定的位置插入元素。第三个参数之后还可以传第四个、第五个参数，乃至任意多个要插入的元素。比如，splice(2, 0, "red", "green")会从数组位置 2 开始插入字符串"red"和"green"。 

       ```javascript
       colors.splice(1, 0, "yellow", "orange"); //green,yellow,orange,blue
       //从数组位置1开始插入，包括位置1
       ```

     - 替换。splice()在删除元素的同时可以在指定位置插入新元素，同样要传入 3 个参数：开始位置、要删除元素的数量和要插入的任意多个元素。要插入的元素数量不一定跟删除的元素数量一致。也会**返回被删除的元素。**

       ```javascript
       colors.splice(1, 1, "red", "purple"); // 插入两个值，删除从位置1开始的1个元素
       alert(colors); // green,red,purple,orange,blue
       ```

10. #### 搜索和位置方法

    有按严格相等搜索和按断言函数搜索两种。

    - 严格相等

      - 三个方法：indexOf()、lastIndexOf()和 includes()。

      - 方法都接收两个参数：要查找的元素和一个可选的起始搜索位置。

      - indexOf()和 includes()方法从前向后搜索，而 lastIndexOf()从后开始向前搜索。
      - 使用全等（===）比较

      ```javascript
      let numbers = [1, 2, 3, 4, 5, 4, 3, 2, 1]; 
      alert(numbers.indexOf(4)); // 3 
      alert(numbers.lastIndexOf(4)); // 5 
      alert(numbers.includes(4)); // true
      ```

    - 断言函数

      断言函数接收 3 个参数：元素、索引和数组本身。

      find()返回第一个匹配的元素，findIndex()返回第一个匹配元素的索引。

      ```javascript
      const people = [ 
       { 
       name: "Matt", 
       age: 27 
       }, 
       { 
       name: "Nicholas", 
       age: 29 
       } 
      ]; 
      alert(people.find((element, index, array) => element.age < 28)); 
      // {name: "Matt", age: 27} 
      alert(people.findIndex((element, index, array) => element.age < 28)); 
      // 0
      ```

11. #### 迭代方法

    - every()：对数组每一项都运行传入的函数，如果对**每一项函数都返回 true，则这个方法返回 true**。
    - some()：对数组每一项都运行传入的函数，如果**有一项函数返回 true，则这个方法返回 true**。 

    - filter()：对数组每一项都运行传入的函数，函数返回 true 的项会组成数组之后返回。

    - map()：对数组每一项都运行传入的函数，返回由每次函数调用的结果构成的数组。
    - forEach()：对数组每一项都运行传入的函数，没有返回值。

    **这些方法都不改变调用它们的数组。**

    ```javascript
    let numbers = [1, 2, 3, 4, 5, 4, 3, 2, 1]; 
    let everyResult = numbers.every((item, index, array) => item > 2); // false 
    let someResult = numbers.some((item, index, array) => item > 2); // true
    let filterResult = numbers.filter((item, index, array) => item > 2); // 3,4,5,4,3
    let mapResult = numbers.map((item, index, array) => item * 2);  // 2,4,6,8,10,8,6,4,2
    numbers.forEach((item, index, array) => { 
     // 执行某些操作 
    });//本质上，forEach()方法相当于使用 for 循环遍历数组。
    ```

12. #### 归并方法

    reduce()和 reduceRight()。这两个方法都会迭代数组的所有项，并在此基础上构建一个最终返回值。reduce()方法从数组第一项开始遍历到最后一项。而 reduceRight()从最后一项开始遍历至第一项。

    - 接收两个参数：对每一项都会运行的归并函数，以及可选的以之为归并起点的初始值。
    - 归并函数函数接收 4 个参数：上一个归并值、当前项、当前项的索引和数组本身。这个函数返回的任何值都会作为下一次调用同一个函数的第一个参数。如果没有给这两个方法传入可选的第二个参数（作为归并起点值），则第一次迭代将从数组的第二项开始，因此传给归并函数的第一个参数是数组的第一项，第二个参数是数组的第二项。

    ```javascript
    let values = [1, 2, 3, 4, 5]; 
    let sum = values.reduce((prev, cur, index, array) => prev + cur); // 15
    sum = values.reduce((prev, cur, index, array) => prev + cur, 3);  //18
    ```

    第一次执行归并函数时，prev 是 1，cur 是 2。第二次执行时，prev 是 3（1 + 2），cur 是 3（数组第三项）。如此递进，直到把所有项都遍历一次，最后返回归并结果。



---

## split函数、join函数、substr函数

### split函数

把一个字符串分割成字符串数组。

接收两个参数：

- 参数一：字符串或正则表达式，从该参数指定的地方分割字符串
- 参数二：指定返回数组的最大个数

返回：分割后的字符串数组

```javascript
var str="How are you doing today?";
var n=str.split(" ",3);
//['How', 'are', 'you']

var str="How are you doing today?";
var n=str.split("o");
//['H', 'w are y', 'u d', 'ing t', 'day?']
```



### join函数

把数组中的所有元素转换一个字符串。

接收一个参数：指定要使用的分隔符。**如果省略该参数，则使用逗号作为分隔符**。

返回字符串

```javascript
var fruits = ["Banana", "Orange", "Apple", "Mango"];
var energy = fruits.join("&");
//'Banana&Orange&Apple&Mango'
energy = fruits.join();
//'Banana,Orange,Apple,Mango'
```
### substr函数

在字符串中抽取从 start 下标开始的指定数目的字符。

接收两个参数：

- 参数一：要抽取的子串的起始下标。必须是数值。如果是负数，那么该参数声明从字符串的尾部开始算起的位置。也就是说，-1 指字符串中最后一个字符，-2 指倒数第二个字符，以此类推。
- 参数二：可选，子串中的字符数。必须是数值。如果省略了该参数，那么返回从 *stringObject* 的开始位置到结尾的字串。

返回：一个新的字符串，包含从 *stringObject* 的 *start*（包括 start 所指的字符） 处开始的 *length* 个字符。

```javascript
var str="Hello world!"
console.log(str.substr(3))
//lo world!
```


