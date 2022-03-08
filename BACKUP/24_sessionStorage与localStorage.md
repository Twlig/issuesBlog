# [sessionStorage与localStorage](https://github.com/Twlig/issuesBlog/issues/24)

cookie中存在一个问题，就是存储了很多不需要经常发给服务器的数据，也就是很多冗余数据，有用但是也不是每次都需要。那每次都全部发给服务器就会带来不必要的资源浪费。

Web Storage 的目的是解决通过客户端存储不需要频繁发送回服务器的数据的问题。

Web Storage 的第 2 版定义了两个对象：localStorage 和 sessionStorage。localStorage 是永久存储机制，sessionStorage 是跨会话的存储机制。这两种浏览器存储 API 提供了在浏览器中**不受页面刷新影响**而存储数据的两种方式。

### **Storage实例方法**

- `clear()`：删除所有值；不在 Firefox 中实现。

- `getItem(name)`：取得给定 *name* 的值。

- `key(index)`：取得给定数值位置的名称。

- `removeItem(name)`：删除给定 *name* 的名/值对。

- `setItem(name, value)`：设置给定 *name* 的值。

### **sessionStorage 对象**

sessionStorage 对象只存储会话数据，这意味着数据只会存储到浏览器关闭。这跟浏览器关闭时会消失的会话 cookie 类似。存储在 sessionStorage 中的数据不受页面刷新影响，可以在浏览器崩溃并重启后恢复。

- 存储到浏览器关闭
- 不受刷新页面影响

1. 既可以使用方法存数据，也可以使用属性存数据：

```javascript
// 使用方法存储数据
sessionStorage.setItem("name", "Nicholas"); 
// 使用属性存储数据
sessionStorage.book = "Professional JavaScript";
```

2. 取数据：

```javascript
// 使用方法取得数据
let name = sessionStorage.getItem("name"); 
// 使用属性取得数据
let book = sessionStorage.book;
```

3. 获取长度

```javascript
let len = sessionStorage.length;
```

4. 删除数据

```javascript
// 使用方法删除值
sessionStorage.removeItem("book");
// 使用 delete 删除值
delete sessionStorage.name; 
```

✨**注意**：sessionStorage 对象应该主要用于存储**只在会话期间有效的小块数据**。如果需要跨会话持久存储数据，可以使用 globalStorage 或 localStorage。

### localStorage 对象

localStorage 对象跨会话持久存储数据。要访问同一个 localStorage 对象，页面必须来自同一个域（子域不可以）、在相同的端口上使用相同的协议。

方法与sessionStorage相同。

#### 区别

- localStorage 数据不受页面刷新影响，也不会因关闭窗口、标签页或重新启动浏览器而丢失。JavaScript 删除或者用户**清除浏览器缓存**才能清除localStorage 。
- sessionStorage存储到浏览器关闭，不受刷新页面影响

#### 相似

localStorage 和sessionStorage对象更新之后都会**触发storage事件**。

```javascript
window.addEventListener("storage", 
 (event) => alert('Storage changed for ${event.domain}'));
```