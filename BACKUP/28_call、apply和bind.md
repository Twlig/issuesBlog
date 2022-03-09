# [call、apply和bind](https://github.com/Twlig/issuesBlog/issues/28)

这三个函数都是为了改变调用函数的对象。也就是改变函数内部的this指向。

### call

第一个参数是this要指向的新对象。后面的**参数以参数列表**的形式传递。call方法是**立即调用**。

```javascript
var o1 = {
    name: "lili",
    age: 20,
    func: function(country, city) {
        console.log("My name is " + this.name + ", I am " + this.age + " years old." + "I come from " + country + ", " + city)
    }
}
var o2 = {
    name: "zzy",
    age: 22
}
o1.func("USA", "NY"); //My name is lili, I am 20 years old.I come from USA, NY
o1.func.call(o2, "China", "NJ"); //My name is zzy, I am 22 years old.I come from China, NJ
```



### apply

第一个参数是this要指向的新对象。后面的**参数以数组的形式传递**。apply方法是**立即调用**。

```javascript
o1.func("USA", "NY"); //My name is lili, I am 20 years old.I come from USA, NY
o1.func.apply(o2, ["China", "NJ"]); //My name is zzy, I am 22 years old.I come from China, NJ
```



### bind

第一个参数是this要指向的新对象。后面的**参数以参数列表的形式传递**。bind方法是**返回函数，不是立即调用**。因此，要写一个()才能调用。

```javascript
o1.func("USA", "NY"); //My name is lili, I am 20 years old.I come from USA, NY
o1.func.bind(o2, "China", "NJ")(); //My name is zzy, I am 22 years old.I come from China, NJ
```

