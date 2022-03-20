# [flex布局](https://github.com/Twlig/issuesBlog/issues/55)

## 什么是flex布局？

Flex 是 Flexible Box 的缩写，意为"弹性布局"，用来为盒状模型提供最大的灵活性。

使用display: flex可以指定一个元素为弹性容器。

> - 行内元素可以使用 display: inline-flex
>
> - Webkit 内核的浏览器，必须加上`-webkit`前缀：display: -webkit-flex
>
> - 设为 Flex 布局以后，子元素的`float`、`clear`和`vertical-align`属性将失效



## flex基本概念

flex包含**弹性容器**和**弹性元素**。弹性容器是指设定为display: flex的元素1，而弹性元素是弹性容器内的子元素。

弹性容器有两根轴线，一根主轴，一根交叉轴。主轴默认是row （从左至右），交叉轴为column（从上到下）。元素就是在这些row和column中设置布局样式。



## 弹性容器

1. display

   设置display: flex开启弹性容器

2. flex-direction

   设置弹性容器的主轴方向

   ```css
   .box {
     flex-direction: row | row-reverse | column | column-reverse;
   }
   ```

3. flex-wrap

   设置弹性元素如何换行

   ```css
   .box{
     flex-wrap: nowrap | wrap | wrap-reverse;
   }
   ```

4. flex-flow

   flex-direction属性和flex-wrap属性的简写形式，默认值为row nowrap。

   ```css
   .box {
     flex-flow: <flex-direction> || <flex-wrap>;
   }
   ```

5. justify-content

   弹性元素在主轴上的对齐方式。默认相当于**在行上设置每列间隔模式**

   ```css
   .box {
     justify-content: flex-start | flex-end | center | space-between | space-around;
   }
   ```

   如主轴为row，则是在水平方向上的对齐方式。

   - flex-start：相当于靠左，左对齐
   - flex-end：靠右，右对齐
   - center：居中对齐两边空出来
   - space-between：相当于两端对齐，项目之间的间隔都相等（左右顶格，中间自适应）
   - space-around：每个项目两侧的间隔相等，所以项目之间的间隔比项目与边框的间隔大一倍

6. align-content

   弹性元素在交叉轴的对齐方式。默认相当于**在列上设置每行的间隔模式**

   ```css
   .box {
     align-content: flex-start | flex-end | center | space-between | space-around | stretch;
   }
   ```

   - `stretch`（默认值）：占满整个交叉轴。即元素在高度上自动拉伸占满整个弹性容器的高度。

7. align-items

   定义弹性元素在单个交叉轴上如何对齐。通俗点，该行的弹性元素，竖直方向上如何摆放。

   ```css
   .box {
     align-items: flex-start | flex-end | center | baseline | stretch;
   }
   ```

   - `flex-start`：上对齐。
   - `flex-end`：下对齐。
   - `center`：中间对齐。
   - `baseline`: 元素的第一行文字的基线对齐。
   - `stretch`（默认值）：如果项目未设置高度或设为auto，将占满整个容器的高度，自动拉长元素。

## 弹性元素

1. flex-basis

   定义元素的基础width或者height（视主轴而定）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为`auto`，即项目的本来大小。

   ```css
   .item {
     flex-basis: <length> | auto; /* default auto */
   }
   ```

2. flex-grow

   定义元素的放大比例，默认为`0`，即如果存在剩余空间，也不放大。

   ```css
   .item {
     flex-grow: <number>; /* default 0 */
   }
   ```

3. flex-shrink

   定义元素的缩小比例，默认为1，即如果空间不足，该项目将缩小。如果一个项目的`flex-shrink`属性为0，其他项目都为1，则空间不足时，前者不缩小。

   ```css
   .item {
     flex-shrink: <number>; /* default 1 */
   }
   ```

4. flex

   `flex`属性是`flex-grow`, `flex-shrink` 和 `flex-basis`的简写，默认值为`0 1 auto`。后两个属性可选。

   ```css
   .item {
     flex: none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ]
   }
   ```

   该属性有两个快捷值：`auto` (`1 1 auto`) 和 none (`0 0 auto`)。

5. order

   定义元素的排列顺序。数值越小，排列越靠前，默认为0。

   ```css
   .item {
     order: <integer>;
   }
   ```

6. align-self

   `align-self`属性允许单个元素有与其他元素不一样的对齐方式，**可覆盖`align-items`属性**。默认值为`auto`，表示继承父元素的`align-items`属性，如果没有父元素，则等同于`stretch`。

   ```css
   .item {
     align-self: auto | flex-start | flex-end | center | baseline | stretch;
   }
   ```

   

