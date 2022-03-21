# [Grid布局](https://github.com/Twlig/issuesBlog/issues/66)

## Grid

Grid 布局则是将容器划分成"行"和"列"，产生单元格，然后指定"项目所在"的单元格，可以看作是**二维布局**。

Gird布局由Grid容器和Grid项目构成。Grid项目（网格元素）只能是容器的顶层子元素。

Grid布局属性有两类：

- 容器属性
- 项目属性

### 容器属性

- ##### `display属性`

  在元素上设置`display:grid`或`display:inline-grid`可以创建一个网格容器。

```css
display:grid ;  容器元素为块级元素
display:inline-grid;   容器元素为行内元素
```

- ##### `grid-template-columns`属性和`grid-template-rows`属性

```css
.wrapper {
  display: grid;
  /*  声明了三列，宽度分别为 200px 200px 200px */
  grid-template-columns: 200px 200px 200px;
  /*  声明了两行，行高分别为 50px 50px  */
  grid-template-rows: 50px 50px;
}
```

对于重复的值可以通过repeat()函数重写：

```css
.wrapper {
  display: grid;
  grid-template-columns: repeat(3,200px);
  grid-template-rows:repeat(2,50px);
}
```

除了repeat函数外，还有以下关键字：

```css
grid-template-columns: repeat(auto-fill, 200px) -- 表示列宽是 200 px，但列的数量是不固定的，只要浏览器能够容纳得下，就可以放置元素
grid-template-columns: 200px 1fr 2fr  -- 表示第一个列宽设置为 200px，后面剩余的宽度分为两部分，宽度分别为剩余宽度的 1/3 和 2/3
minmax(100px, 1fr)  -- 表示列宽不小于100px，不大于1fr
grid-template-columns: 100px auto 100px -- 表示第一第三列为 100px，中间由浏览器决定长度
```

- ##### `gap属性`

  `grid-row-gap` 属性、`grid-column-gap` 属性分别设置行间距和列间距。`grid-gap `属性是两者的简写形式。

```css
grid-row-gap: 10px; 表示行间距是 10px
grid-column-gap: 20px; 表示列间距是 20px
grid-gap: 10px 20px; 等同上述两个属性
```

- ##### `grid-template-areas`属性

  用于定义区域，一个区域由一个或多个单元格组成

```css
.container {
  display: grid;
  grid-template-columns: 100px 100px 100px;
  grid-template-rows: 100px 100px 100px;
  grid-template-areas: 'a b c'
                        'd e f'
                        'g h i';
}
```

上面代码先划分出9个单元格，然后将其定名为a到i的九个区域，分别对应这九个单元格。
多个单元格合并成一个区域：

```
grid-template-areas: 'a a a'
                        'b b b'
                        'c c c';
```

上面代码将9个单元格分成a、b、c三个区域，如果某些区域不需要利用，则用`.`表示。

- ##### `grid-auto-flow`属性

  划分好网格后，容器的子元素会按照顺序，自动放置在每一个网格。
  顺序就是由grid-auto-flow决定，默认为**行**，代表"先行后列"，即先填满第一行，再开始放入第二行。

```
1 2 3
4 5 6
7 8 9
```

当换成列后，排列变成下面的情况：

```
1 4 7
2 5 8
3 6 9
```

- ##### 位置items属性

  `justify-items `属性设置单元格内容的水平位置（左中右），`align-items `属性设置单元格的垂直位置（上中下）。

```css
.container {
  justify-items: start | end | center | stretch;
  align-items: start | end | center | stretch;
}
```

属性对应如下：

```
start：对齐单元格的起始边缘
end：对齐单元格的结束边缘
center：单元格内部居中
stretch：拉伸，占满单元格的整个宽度（默认值）
```

place-items属性是align-items属性和justify-items属性的合并简写形式

- ##### 位置content属性

  `justify-content`属性是整个内容区域在容器里面的水平位置（左中右），`align-content`属性是整个内容区域的垂直位置（上中下）

```css
.container {
  justify-content: start | end | center | stretch | space-around | space-between | space-evenly;
  align-content: start | end | center | stretch | space-around | space-between | space-evenly;  
}
```

两个属性的写法完全相同，都可以取下面这些值：

```
start - 对齐容器的起始边框
end - 对齐容器的结束边框
center - 容器内部居中
space-around - 每个项目两侧的间隔相等。所以，项目之间的间隔比项目与容器边框的间隔大一倍
space-between - 项目与项目的间隔相等，项目与容器边框之间没有间隔
space-evenly - 项目与项目的间隔相等，项目与容器边框之间也是同样长度的间隔
stretch - 项目大小没有指定时，拉伸占据整个网格容器
```

- ##### `gird-auto`属性

  有时候，一些项目的指定位置，在现有网格的外部，就会产生显示网格和隐式网格

比如网格只有3列，但是某一个项目指定在第5行。这时，浏览器会自动生成多余的网格，以便放置项目。超出的部分就是隐式网格

而grid-auto-rows与grid-auto-columns就是专门用于指定隐式网格的宽高

### **项目属性**

grid-column-start 属性：左边框所在的垂直网格线
grid-column-end 属性：右边框所在的垂直网格线
grid-row-start 属性：上边框所在的水平网格线
grid-row-end 属性：下边框所在的水平网格线

```html
<style>
    #container{
        display: grid;
        grid-template-columns: 100px 100px 100px;
        grid-template-rows: 100px 100px 100px;
    }
    .item-1 {
        grid-column-start: 2;
        grid-column-end: 4;
    }
</style>

<div id="container">
    <div class="item item-1">1</div>
    <div class="item item-2">2</div>
    <div class="item item-3">3</div>
</div>
```

- `self`类属性
  justify-self属性设置单元格内容的水平位置（左中右），跟justify-items属性的用法完全一致，但只作用于单个项目。

  align-self属性设置单元格内容的垂直位置（上中下），跟align-items属性的用法完全一致，也是只作用于单个项目。

```css
.item {
  justify-self: start | end | center | stretch;
  align-self: start | end | center | stretch;
}
```

可取的值：

```
start：对齐单元格的起始边缘。
end：对齐单元格的结束边缘。
center：单元格内部居中。
stretch：拉伸，占满单元格的整个宽度（默认值）
```



容器属性13个：设置行列宽度（2），设置行列间距（3），设置区域名（1），设置行列排序（1），设置项目位置（3），设置整个内容区域的布局（2），多余项目的属性（1）

项目属性6个：设置项目行列起始位置（4），项目水平垂直布局（2）