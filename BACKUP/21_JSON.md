# [JSON](https://github.com/Twlig/issuesBlog/issues/21)

JSON 是一种数据格式，而不是编程语言。

### JSON语法

JSON 语法支持表示 3 种类型的值。

- **简单值**：字符串、数值、布尔值和 null 可以在 JSON 中出现，就像在 JavaScript 中一样。**特殊值 undefined 不可以。**

  下面这个字符串也是有效的 JSON:

  ```javascript
  "Hello world!"
  ```

- **对象**：第一种复杂数据类型，对象表示**有序键/值对**。每个值可以是简单值，也可以是复杂类型。

  ```javascript
  {     //json
   "name": "Nicholas", 
   "age": 29 
  }
  
  let person = {  //javascript
   name: "Nicholas", 
   age: 29 
  };
  ```

  区别：

  - 首先，没有变量声明（JSON 中没有变量）
  - 其次，最后没有分号（不需要，因为不是 JavaScript 语句）
  - 同样，用引号将属性名包围起来才是有效的JSON

- **数组**：第二种复杂数据类型，数组表示可以通过数值索引访问的值的有序列表。

  ```javascript
  [25, "hi", true]
  ```

复杂的JSON例子：

```json
[ 
 { 
 "title": "Professional JavaScript", 
 "authors": [ 
 "Nicholas C. Zakas", 
 "Matt Frisbie" 
 ], 
 "edition": 4, 
 "year": 2017 
 }, 
 { 
 "title": "Professional JavaScript", 
 "authors": [ 
 "Nicholas C. Zakas" 
 ], 
 "edition": 3, 
 "year": 2011 
 }, 
 { 
 "title": "Professional JavaScript", 
 "authors": [ 
 "Nicholas C. Zakas" 
 ], 
 "edition": 2, 
 "year": 2009 
 }
]
```

假设把前面的数据结构保存在了变量 books 中，获取第三本书的书名：

```
books[2].title 
```



### 解析与序列化

ECMAScript 5 增加了 JSON全局对象，正式引入解析 JSON 的能力。JSON 对象有两个方法：stringify()和 parse()。这两个方法分别可以将JavaScript 序列化为 JSON 字符串，以及将 JSON 解析为原生 JavaScript 值。

1. **`JSON.stringify()`**

   JSON.stringify()把一个 **JavaScript 对象**序列化为一个 **JSON 字符串**。JSON.stringify()接收三个参数，后两个不是必须的。

   - 参数一：JavaScript 对象

     ```javascript
     let book = { 
      title: "Professional JavaScript", 
      authors: [ 
      "Nicholas C. Zakas", 
      "Matt Frisbie" 
      ], 
      edition: 4, 
      year: 2017 
     }; 
     let jsonText = JSON.stringify(book);
     //{"title":"Professional JavaScript","authors":["Nicholas C. Zakas","Matt Frisbie"], 
     //"edition":4,"year":2017}
     ```

   - 参数二：数组或函数

     - 如果第二个参数是一个数组，那么 JSON.stringify()返回的结果只会包含该数组中列出的对象

       属性。

       ```javascript
       let jsonText = JSON.stringify(book, ["title", "edition"]);
       ```

     - 如果第二个参数是一个函数，则行为又有不同。提供的函数接收两个参数：属性名（key）和属性

       值（value）。

       ```javascript
       let jsonText = JSON.stringify(book, (key, value) => { 
        switch(key) { 
            case "authors": 
                return value.join(",") 
            case "year": 
                return 5000; 
            case "edition": 
                return undefined; 
            default: 
                return value; 
        } 
       });
       //{"title":"Professional JavaScript","authors":"Nicholas C. Zakas,Matt 
       //Frisbie","year":5000}
       ```

       **注意：**

       > - 键是"edition"，则返回 undefined **忽略该属性**。
       >
       > - **最后一定要提供默认返回值，以便返回其他属性传入的值。**

   - 参数三：缩进结果JSON字符串

     JSON.stringify()方法的第三个参数控制缩进和空格。在这个参数是数值时，表示每一级缩进的空格数。

     ```javascript
     let jsonText = JSON.stringify(book, null, 4);
     /*{ 
      "title": "Professional JavaScript", 
      "authors": [ 
      "Nicholas C. Zakas", 
      "Matt Frisbie" 
      ], 
      "edition": 4, 
      "year": 2017 
     }*/
     ```

   ✨在序列化 JavaScript 对象时: 

   - **所有函数和原型成员都会有意地在结果中省略**
   - **值为 undefined的任何属性会被跳过**
   - 所有**实例属性**均为有效 JSON 数据类型

2. **`JSON.parse()`**

   把JSON字符串转为JavaScript对象，接收两个参数。

   - 参数一：JSON字符串

   - 参数二：还原函数

     **如果还原函数返回 undefined，则结果中就会删除相应的键**。如果返回了其他任何值，则该值就

     会成为相应键的值插入到结果中。

     ```javascript
     let book = { 
      title: "Professional JavaScript", 
      authors: [ 
      "Nicholas C. Zakas", 
      "Matt Frisbie" 
      ], 
      edition: 4, 
      year: 2017, 
      releaseDate: new Date(2017, 11, 1) 
     }; 
     let jsonText = JSON.stringify(book); 
     let bookCopy = JSON.parse(jsonText, 
      (key, value) => key == "releaseDate" ? new Date(value) : value);
     ```

