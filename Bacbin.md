# CSS经典布局

---
本文主要对一些常见的CSS布局问题进行总结，涉及三栏布局、负margin、清除浮动、居中布局、Flex布局，等等....

## 基础

一个界面友好的网站，能帮助了解一些大概的CSS布局知识。

- [学习CSS布局](http://zh.learnlayout.com/no-layout.html)

## 定位

定位是最基本的一个布局问题，其定义一个元素相对于其他元素的位置，这个元素可以是父级元素，也可以是浏览器窗口。

先从position属性说起，static、relative、absolute、fixed和sticky。

- static(默认)：元素框正常生成。块级元素生成一个矩形框，作为文档流的一部分；行内元素则会创建一个或多个行框，置于其父元素中。

- relative：元素框相对于之前正常文档流中的位置发生偏移，并且原先的位置仍然被占据。发生偏移的时候，可能会覆盖其他元素。

- absolute：元素框不再占有文档流位置，并且相对于包含块进行偏移(所谓的包含块就是最近一级外层元素position不为static的元素。

- fixed：元素框不再占有文档流位置，并且相对于视窗进行定位.

- sticky: "fixed" OR "relative",在屏幕范围内时，当作relative，当该元素移出屏幕范围（viewport）时，作为fixed。

## 三栏布局

三栏布局其实就是，两边的box宽度固定，中间的box宽度自适应。

```html
<div class="container">
    <div class="middle">中间栏
    </div>
    <div class="left">左边栏
    </div>
    <div class="right">右边栏
    </div>
</div>
```

圣杯布局，包含三个box，左中右

- 中间宽度设为`width：100%;`,占一行。

- 左右box使用负margin`margin-left`，将它们提至于中间box同一行。

  - 左边盒子`margin-left:-100%;`。

  - 右边盒子`margin-right:-右边盒子的长度px;`。

- Container再预设左右`padding:0 200px 0 220px;`，为左右盒子留出位置。

- 使用`position: relative;`占居*Padding*两边的空白。

```css
.container {
    padding: 0 220px 0 200px;
}
.left, .middle, .right {
    float: left;
    position: relative;
    min-height: 300px;
}
.middle {
    width: 100%;
    background-color: #fe9898;
}
.left {
    width: 200px;
    margin-left: -100%;
    left: -200px;
    background-color: #555;
}
.right {
    width: 220px;
    right: -220px;
    margin-left: -220px;
    background-color: blue;
}
```

## 清除浮动

清除浮动主要是为了解决高度塌陷的问题

### 何谓高度塌陷呢？

要解决这个问题，需要先明晰两个概念**普通流**和**浮动**

1. 普通流（normal flow）：将窗体自上而下分成一行一行,并在每行中按从左至右的挨次排放元素,即为普通流/文档流。内联元素不会独占一行，而每个非浮动块级元素都独有一行。

2. 浮动(float)：浮动的框可以左右移动，直至它的外边缘遇到包含框或者另一个浮动框的边缘。需要注意的是，浮动框不属于普通流，当一个元素浮动之后，不会影响到块级框的布局而只会影响内联框（通常是文本）的排列，文档中的普通流就会表现得和浮动框不存在一样。

正是因为浮动的这种特性，导致本属于普通流中的元素浮动之后，如果包含框内部不存在其他普通流元素，就会表现出高度为0（高度塌陷）。

### 方法

#### 1）添加额外标签

通过在浮动元素末尾添加一个空的标签，例如```<div style="clear:both"></div>```，亦或是```<br clear="all" />```,但缺点是添加了无意义的标签。

#### 2）父元素也设置浮动

#### 3）使用:after伪元素

```css
.box:after {
    display: block;
    content: "";
    clear: both;
    height: 0;
    visibility: hidden;
    overflow: hidden;
}
```

其效果相当于直接在div.box上添加一个宽高为0的`clear：both`的div元素.

BFC??

## 负margin

1.**作用于static元素上的负margin**

![none](http://www.w3cplus.com/sites/default/files/styles/print_image/public/blogs/2013/negative-margin-1.jpg)

Static元素是没有设定成浮动的元素，
当static元素的margin-top/margin-left被赋予负值时，元素将被拉进指定的方向，例如：

```css
/* 元素向上移动10px */
#mydiv1 {
    margin-top: -10px;
}
```

但如果你设置margin-bottom/right为负数，元素并不会如你所想的那样向下/右移动，而是将后续的元素拖拉进来，覆盖本来的元素。

```css
/*
*mydiv1后续的元素向上移动10px，mydiv1本身并不会动
*/
#mydiv1 {
    margin-bottom: -10px;
}
```

如果没有设定width属性，设定负margin-left/right会将元素拖向对应的方向，并增加宽度，此时的margin的作用就像padding一样。

2.**作用于浮动元素上的负margin**

定义三个box

```html
<div class="container">
    <div class="fl box1">box1</div>
    <div class="fl box2">box2</div>
    <div class="fl box3">box3</div>
</div>
```

```css
.fl {
    float: left;
    width: 100px;
    height: 100px;
}
.box1{background-color: rgba(33, 114, 214, 0.8);}
.box2{background-color: rgba(255, 82, 0, 0.8);}
.box3{background-color: rgba(90, 243, 151, 0.8);}
```

![none](https://upload-images.jianshu.io/upload_images/1429373-5cf45f1d12708512.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/540/format/webp)

- 给所有的box都加上`margin-left:-25px;`

![none](https://upload-images.jianshu.io/upload_images/1429373-e866b3bd75b3fa72.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/558/format/webp)

- 只给box3加上`margin-left:-200px;`

![none](https://upload-images.jianshu.io/upload_images/1429373-b37b58130a0b5570.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/515/format/webp)

## 居中布局

- [Centering in CSS:A Complete Guide](https://css-tricks.com/centering-css-complete-guide/):全面介绍各种情况下的居中布局，~~但是是全英的~~。

主体内容：

- 水平居中

  - 行内元素(inline)：`text-align:center;`

  - 块级元素(block)：设置宽度且`margin-left`和`margin-right`为`auto`

  - 多个块级元素：父元素设为`text-align:center;`，子元素设为`display:inline-block;`或用flex布局

- 垂直居中

  - 行内元素(inline)

    - 单行，设置上下padding相等，或设置`height`和`line-height`相等

    - 多行，设置上下padding相等，或设置`display:tabel-cell`和`vertical-align:middle`,或使用flex布局

  - 块级元素(block)，父元素需设相对布局~~relative~~

    - 已知高度：子元素使用绝对布局`top:50%`，再用负的margin-top向上拉

    - 未知高度：子元素使用绝对布局，`top:50%;transform:tranlateY(-50%);`

- 水平垂直居中

  - ~~To be continued...~~

## Flex布局

Flex布局，官称 *CSS Flexible Box*，是CSS3中的一种新的布局方式。Flexbox可以控制未知容器元素的对齐方式，排列方向，排列顺序等，甚至是在未知大小的容器。其主要特点是能够修改Flex容器内子元素(flex item)的宽度或高度。

### FLex 模块

Flex布局主要由父容器和它的子元素直接构成，，其中父元素称为Flex Container，子元素称为Flex Item。

![none](https://www.w3cplus.com/sites/default/files/blogs/2015/1504/CSS3-Flexbox-Model.jpg)

[有关Flex模块更详细的信息 ~~英语警告~~](https://www.w3.org/TR/css-flexbox/#box-model)

### 使用方法

使用Flex布局只需在父容器元素上设置`display`属性：

```css
.container {
    display: -webkit-flex; /* Safari */
    display: flex;
}
```

如果想设置为一个内联元素时：

```css
.container {
    display: -webkit-inline-flex; /* Safari */
    display: inline-flex;
}
```

**注意**:属性只要设置在父容器上，其所有子元素将自动成为Flex项目(item)。

### Flex容器属性

- **flex-direction**

这个属性主要设置flex容器的主轴方向，指定flex项目在flex容器中的排列方式。flex容器的主轴方向主要有水平和纵向两种.

```css
flex-container {
    flex-direction: row;
    flex-direction: row-reverse;/*反向*/
}
```

![none](https://www.w3cplus.com/sites/default/files/blogs/2015/1504/flexbox-flex-direction-row.jpg)

![none](https://www.w3cplus.com/sites/default/files/blogs/2015/1504/flexbox-flex-direction-row-reverse.jpg)

`flex-direction:column;`纵向排列，同理。

但注意，其默认值为row.

- **flex-wrap**

flex项目在flex容器中默认是只显示一行。如果希望控制flex项目在flex容器中按一行或多行排列，可以使用`flex-wrap`。

```css
.flex-container {
    flex-wrap: nowrap;
}
```

Flex项目在flex容器中默认显示一行，项目宽度将自动缩减来适应容器.

![none](https://www.w3cplus.com/sites/default/files/blogs/2015/1504/flexbox-flex-wrap-nowrap.jpg)

```css
.flex-container {
    flex-wrap: wrap;
}
```

使用wrap属性，item将从左到右由上到下在container中显示。

![none](https://www.w3cplus.com/sites/default/files/blogs/2015/1504/flexbox-flex-wrap-wrap.jpg)

- **justify-content**

该属性是`flex-direction`和`flex-wrap`的简写，默认是`flex-flow: row nowrap;`。

- **flex-flow**

`flex-flow`指定flex项目在flex容器中沿主轴在当前行的对齐方式

```css
.flex-container {
    justify-content: flex-start;/*向左靠齐*/
    justify-content: flex-end;/*向右靠齐*/
}
```

```css
.flex-container {
    justify-content: center;/*居中*/
}
```

![none](https://www.w3cplus.com/sites/default/files/blogs/2015/1504/flexbox-justify-content-center.jpg)

Flexitem之间间距相对，第一个item和最后一个item向container的边缘对齐。

```css
.flex-container {
    justify-content: space-between;
}
```

![none](https://www.w3cplus.com/sites/default/files/blogs/2015/1504/flexbox-justify-content-space-between.jpg)

```css
.flex-container {
    justify-content: space-around;
}
```

Flexitem前后间距相等

![none](https://www.w3cplus.com/sites/default/files/blogs/2015/1504/flexbox-justify-content-space-around.jpg)

默认：`flex-start`

- **algin-items**

Flex项目在容器侧轴对齐方式，类似于`justify-content`，只不过不是水平方向，而是纵向。这个属性可以设置所有flex项目对齐方式，并且包括匿名元素.

属性值：

- stretch(沿侧轴高度填满容器)

- flex-start

- flex-end

- center

- baseline（按文本基线在侧轴上排列）

### Flex项目属性

- **order**

order属性是用来控制flex容器中flex项目的排列顺序。默认情况flex项目在flex容器的顺序是flex项目出现的顺序。

![none](https://www.w3cplus.com/sites/default/files/blogs/2015/1504/flexbox-order.jpg)

- **flex-glow**

这个属性用来指定 flex项目的放大比例，其决定了flex项目相对flex容器自由空间进行放大.

![none](https://www.w3cplus.com/sites/default/files/blogs/2015/1504/flexbox-flex-grow-2.jpg)

- **flex-basis**

用来指定flexitem的大小。

![none](https://www.w3cplus.com/sites/default/files/blogs/2015/1504/flexbox-flex-basis.jpg)

- **align-self**

这个属性用来指定flexitem自身的对齐方式：

```css
.flex-item {
    -webkit-align-self: auto | flex-start | flex-end | center | baseline | stretch; /* Safari */
    align-self: auto | flex-start | flex-end | center | baseline | stretch;
 }
```

![none](https://www.w3cplus.com/sites/default/files/blogs/2015/1504/flexbox-align-self.jpg)

### Flex项目中的无效属性

float，clear和vertical-align属性应用在flex项目上将会无效和无法将其out-of-flow.

## 参考文献

- [CSS布局说——可能是最全的](https://segmentfault.com/a/1190000011358507#articleHeader4)

- [CSS 布局经典问题初步整理](http://brianway.github.io/2017/05/18/css-layout-classical-problems/#flexbox-%E5%B8%83%E5%B1%80)

- [margin为负值产生的影响和常见布局应用](https://www.jianshu.com/p/549aaa5fabaa)

- [负margin用法权威指南](https://www.w3cplus.com/css/the-definitive-guide-to-using-negative-margins.html)

- [图解CSS3 Flexbox属性](https://www.w3cplus.com/css3/a-visual-guide-to-css3-flexbox-properties.html)

- ......~~太多 不列了~~