# [BOM](https://github.com/Twlig/issuesBlog/issues/18)

浏览器对象模型（BOM，Browser Object Model）,BOM 提供了与网页无关的**浏览器功能对象**。

### window对象

BOM 的核心是 window 对象，表示**浏览器的实例**。一个是ECMAScript 中的 Global 对象，另一个就是浏览器窗口的 JavaScript 接口。因为 window 对象被复用为 ECMAScript 的 Global 对象，所以通过 var 声明的所有全局变量和函数都会变成 window 对象的属性和方法。

```javascript
var name = 'zzy' //全局定义的name变量
window.name   //zzy，name添加到了window对象上，可以通过window访问
```

1. **窗口大小**

   - clientWidth和clientHeight

     document.documentElement.clientWidth和document.documentElement.clientHeight获取，返回浏览器窗口中页面视口的返回页面视口的宽度和高度。（不包含滚动条）

   - innerWidth、innerHeight

     window.innerWidth和 window.innerHeight 返回浏览器窗口中页面视口的大小（不包含浏览器边框和工具栏），但是包含滚动条。

   - outerWidth 和 outerHeight

     window.outerWidth 和 window.outerHeight 返回浏览器窗口自身的大小。

2. **视口位置**

   浏览器窗口可以通过滚动在有限的视口中查看文档。

   - 度量**文档**相对于**视口滚动距离**的属性有两对，返回相等的值：`window.pageXoffset/window.scrollX` 和 `window.pageYoffset/window.scrollY`。

   - 使用 scroll()、scrollTo()和 scrollBy()方法滚动页面。接收表示相对视口距离的 *x* 和 *y* 坐标，这两个参数在前两个方法中表示要滚动到的坐标，在最后一个方法中表示滚动的距离。

     ```javascript
     // 相对于当前视口向下滚动 100 像素
     window.scrollBy(0, 100); 
     // 滚动到页面左上角
     window.scrollTo(0, 0);
     
     //这几个方法也都接收一个 ScrollToOptions 字典，除了提供偏移值，还可以通过 behavior 属性告诉浏览器是否平滑滚动
     
     // 正常滚动 
     window.scrollTo({ 
      left: 100, 
      top: 100, 
      behavior: 'auto' 
     }); 
     // 平滑滚动
     window.scrollTo({ 
      left: 100, 
      top: 100, 
      behavior: 'smooth' 
     });
     ```

3. **系统对话框**

   - alert()

     警告框

     ```javascript
     alert("操作成功")  //用于提示用户
     ```

   - confirm()

     确认框，确认框有两个按钮：“Cancel”（取消）和“OK”（确定）。用户通过单击不同的按钮表明希望接下来执行什么操作。

     ```javascript
     if (confirm("Are you sure?")) { 
      alert("I'm so glad you're sure!"); 
     } else { 
      alert("I'm sorry to hear you're not sure."); 
     }
     ```

   -  prompt()

     提示框的用途是提示用户输入消息。除了 OK 和 Cancel 按钮，提示框还会显示一个文本框，让用户输入内容。

     prompt()方法接收两个参数：要显示给用户的文本，以及文本框的默认值（可以是空字符串）。

     如果用户单击了 OK 按钮，则 prompt()会返回文本框中的值。如果用户单击了 Cancel 按钮，或者对话框被关闭，则 prompt()会返回 null。

     ```javascript
     let result = prompt("What is your name? ", "");
     if (result !== null) { 
      alert("Welcome, " + result); 
     }
     ```

### location对象

location 提供了当前窗口中加载文档的信息，以及通常的导航功能。它既是 window 的属性，也是 document 的属性。window.location 和 document.location 指向同一个对象。

1. **操作地址**

   可以通过修改 location 对象修改浏览器的地址。

   - location.assign
   - location.href
   - window.location
   - window.open

   ```javascript
   location.assign("http://www.wrox.com");
   location.href = "http://www.wrox.com";
   window.location = "http://www.wrox.com"; 
   window.open("http://www.wrox.com");
   ```

2. **查询字符串**

   - **URLSearchParams**

     URLSearchParams 提供了一组标准 API 方法，通过它们可以检查和修改查询字符串。给URLSearchParams 构造函数传入一个查询字符串，就可以创建一个实例。这个实例上暴露了 get()、set()和 delete()等方法，可以对查询字符串执行相应操作。

     ```javascript
     let qs = "?q=javascript&num=10"; 
     let searchParams = new URLSearchParams(qs); 
     alert(searchParams.toString()); // " q=javascript&num=10" 
     searchParams.has("num"); // true 
     searchParams.get("num"); // 10 
     searchParams.set("page", "3"); 
     alert(searchParams.toString()); // " q=javascript&num=10&page=3" 
     searchParams.delete("q"); 
     alert(searchParams.toString()); // " num=10&page=3"
     ```

   - **location.search** 

     返回了从问号开始直到 URL 末尾的所有内容.

     假设网址为：`http://www.wrox.com?q=javascript&num=10 `

     ```javascript
     location.search   //?q=javascript&num=10 `
     ```

     把查询参数以及对应的值都给解析出来：

     ```javascript
     let getQueryStringArgs = function() { 
      // 取得没有开头问号的查询字符串
      let qs = (location.search.length > 0 ? location.search.substring(1) : ""), 
      // 保存数据的对象
      args = {}; 
      // 把每个参数添加到 args 对象
      for (let item of qs.split("&").map(kv => kv.split("="))) { 
      let name = decodeURIComponent(item[0]), 
      value = decodeURIComponent(item[1]); 
      if (name.length) { 
      args[name] = value; 
      } 
      }
          return args; 
     }
     let args = getQueryStringArgs(); 
     alert(args["q"]); // "javascript" 
     alert(args["num"]); // "10"
     ```

### history对象

history 对象表示当前窗口首次使用以来用户的导航历史记录。因为 history 是 window 的属性，所以每个window 都有自己的 history 对象。出于安全考虑，这个对象不会暴露用户访问过的 URL，但可以通过它在不知道实际 URL 的情况下前进和后退。

- go

  ```javascript
  // 后退一页
  history.go(-1); 
  // 前进一页
  history.go(1); 
  // 前进两页
  history.go(2);
  ```

- back和forward

  ```javascript
  // 后退一页
  history.back(); 
  // 前进一页
  history.forward();
  ```

- length

  历史记录中有多个条目。这个属性反映了历史记录的数量，包括可以前进和后退的页面。对于窗口或标签页中加载的第一个页面，history.length 等于 1。

> ✨**注意**：如果不希望增加历史记录，可以使用 **replace**方法。这个方法接收一个 URL 参数，但重新加载后不会增加历史记录。调用 **replace**之后，用户不能回到前一页。
>
> ```javascript
>  location.replace("http://www.wrox.com/");
> ```

