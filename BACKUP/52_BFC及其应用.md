# [BFC及其应用](https://github.com/Twlig/issuesBlog/issues/52)

### 1.BFC

当元素在页面上垂直或水平排布时，它们之间如何相互影响，CSS有几套不同的规则，其中一套叫块级格式化上下文（Block Formatting Context）。

- 格式化上下文
  Formatting context 是 W3C CSS2.1 规范中的一个概念。它是页面中的一块渲染区域，并且有一套渲染规则，它决定了其子元素将如何定位，以及和其他元素的关系和相互作用。最常见的 Formatting context 有 Block fomatting context (简称BFC)和 Inline formatting context (简称IFC)。

> BFC是一个独立的布局环境，其中的元素布局是不受外界的影响，并且在一个BFC中，块盒与行盒（行盒由一行中所有的内联元素所组成）都会垂直的沿着其父元素的边框排列。

### 2.创建BFC

满足以下条件之一就是BFC元素：

- 根元素，即html元素；
- display的值是 inline-block | table-cell | flex | table-caption | inline-flex的；
- float的属性值不是none的元素；
- 绝对定位、固定定位的元素；
- overflow属性值不是visible的元素。

### 3.BFC的特性

- 内部的盒子会在垂直方向上一个接一个放置
- Box垂直方向的距离由margin决定。属于同一个BFC的两个相邻Box的margin会发生重叠。
- 每个盒子（块盒与行盒）的margin box的左边，与包含块border box的左边相接触(对于从左往右的格式化，否则相反)。即使存在浮动也是如此。
- BFC的区域不会与float的元素区域重叠
- 计算BFC的高度时，浮动子元素也参与计算
- BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素，反之亦然



BFC其实就是一个在html布局环境下，新开辟的类html元素**独立布局空间**，可以大致理解为BFC元素内部布局不会影响外界。

#### 对触发条件的理解：

- 这就可以反推出为啥浮动、固定定位、绝对定位会是BFC触发条件，因为他们已经脱离了文档流，不会影响到其他外界元素。
- 而overflow非visible值会触发BFC也可以理解，因为overflow的visible值会使得子元素超出父元素时呈现溢出的状态从而影响外界布局，而其他值则会限制子元素溢出，所以不会影响外界元素。
- display的值是 inline-block能触发也是因为修改后，子元素的撑开的内容虽然不会被BFC包裹，但是会以父元素+子元素的最大效果影响外界布局。就相当于是父子是整体。

#### 对于特性的理解：

- 类html
  - 内部的盒子会在垂直方向上一个接一个放置，其实就和html上放置块盒和行盒一样的原理
  - Box垂直方向的距离由margin决定。属于同一个BFC的两个相邻Box的margin会发生重叠。这样和html内部的布局是一样的。
- 由独立布局空间引发的特性
  - 子元素的margin box的左边，与BFC元素的border box左边相接触。（**就是这个属性解决了普通父子元素之间的margin重叠问题**）
  - BFC的区域不会与float的元素区域重叠
  - 计算BFC的高度时，浮动子元素也参与计算
  - BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素



### 4.BFC的作用

- 解决父子margin共享问题

  给父元素设置BFC属性就可以解决父子元素margin共享。

- 浮动高度塌陷问题

  常见的方式是为父元素设置overflow：hidden或者浮动父元素。根本原因在于创建BFC的元素，子浮动元素也会参与其高度计算。