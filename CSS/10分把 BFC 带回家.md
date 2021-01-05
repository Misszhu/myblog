# 10分把 BFC 带回家

BFC 是面试中一个非常常见的考点，也是解决常见 CSS 问题：清除浮动、外边距重叠等问题的一个必不可少的知识点。接下来我会从：BFC 是什么、创建 BFC 的方式、BFC 的特性以及 BFC 这5个方面，带大家一起攻克这个知识点，花10分钟时间把 BFC 带回家！

## 1. 基本概念

BFC 是 Block Formatting Context 的缩写，翻译过来就是**块级格式化上下文**。它是 Web 页面中一块**独立**的渲染区域，只有块级元素参与。BFC有它自己的一套渲染规则，规定了它内部的块级元素是如何布局的，并且区域内部的块级元素与区域外部毫不相干。

### 1.1 Formatting Context

格式化上下文页面中独立的一块渲染区域。页面上的所有内容都是格式化上下文的一部分，或者是已定义为以特定方式布局内容的区域。每个格式上下文都有关于布局在该上下文中的行为的特定规则。除了BFC，常见的格式化上下文还有：

1. Inline Formatting Context(IFC)
2. Grid Formatting Context(GFC)
3. Flex Formatting Context(FFC)

## 2. 特性

1. 包含创建它的元素内部的所有内容，换句话说，就是计算 BFC 的高度时，容器里面的所有子元素都会参与计算；
2. BFC 的区域不会与 float 的元素区域重叠
3. 属于同一个 BFC 的相邻两个块级元素的 margin 会发生重叠（垂直方向和水平方向都会）
4. 一个 BFC和它的第一个子元素（这个子元素必须要是块级元素）的 margin 会发生折叠

## 3. 创建 BFC 的方式

下面只介绍常见的几个创建方式，可查阅 [MDN](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Block_formatting_context)了解更多。

1. 根元素
2. 浮动元素（元素的 float 不为 none）
3. 绝对定位元素（position 为 absolute 或 fixed）
4. 行内块级元素
5. overflow 不为 visible 的元素
6. display 不为 flow-root 的元素
7. 弹性元素 （display 为 flex or inline-flex 的直接子元素）
8. 网格元素（display 为 grid or inline-grid 的直接子元素）

etc.

### 4. BFC 的应用

#### 4.1 清除浮动

设置了**浮动**的box会脱离标准文档流，导致父元素的**高度塌陷**，这往往不是我们想要的结果。

```html
<style type="text/css">
  .float-wrapper {
    /* overflow: hidden; */
    /* float: left;  */
    border: 5px solid rebeccapurple;
    background-color: rgb(224, 206, 247);
  }
  .float-box {
    float: left;
    width: 300px;
    height: 300px;
    border: 5px solid #006fa0;
    background-color: #e1f3fa;
  }
</style>

<div class="float-wrapper">
  <div class="float-box">I am a float-box</div>
  <p>I am a paragraph in float-wrapper</p>
</div>
```

[在线运行传送门](https://jsfiddle.net/zhuhuiting/b7kz8ap4/25/)

![image](https://github.com/Misszhu/myblog/raw/master/CSS/image/BFC1.png)

回顾我们上面说到的第一条 BFC 的特性：

> 1. 包含创建它的元素内部的所有内容，换句话说，就是计算 BFC 的高度时，容器里面的所有子元素都会参与计算；

因此，我们只需要想办法创建一个BFC，高度塌陷的方法就能解决。

##### 清除浮动方法一：父元素设置也设置浮动（不推荐）

由于浮动元素就是一个 BFC，给父元素设置浮动属性（不为 none ）从而创建了 BFC 之后，父元素会包裹住他的所有子元素，从而把高度撑开，以清除浮动。



要注意的是，父元素设置浮动会导致这个父元素也脱离标准文档流，接着父元素的父元素也需要清除浮动，是不是有俄罗斯套娃的味道了~另外，设置了 `float: left`，父元素的宽度会变为由子元素自动撑开，这也不一定是我们想要的。

##### 清除浮动方法一：overflow: hidden

同样是因为 BFC 会包含它的元素内部的所有内容，父元素设置`overflow: hidden` 创建 BFC，使得浮动的子元素能撑开父元素的高度以清除浮动。当使用这个属性只是为了创建 BFC 的时候，我们可能会遇到一些不想要的问题，比如滚动条或者一些剪切的阴影，需要注意。另外，对于后续的开发，可能不是很清楚当时为什么使用 overflow。所以最好添加一些注释来解释为什么这样做。

#### 外边距塌陷

##### 相邻方向的外边距坍塌

BFC 的属性第二条：

> 属于同一个 BFC 的相邻两个块级元素的 margin 会发生重叠（垂直方向和水平方向都会）

```html
<style type="text/css">
.box1 {
  width: 200px;
  height: 200px;
  margin-bottom: 20px;
  background-color: #ffb6b9;
}
.box2 {
  width: 200px;
  height: 200px;
  margin-top: 10px;
  background-color: #bbded6;
}
</style>
<div class="box1"></div>
<div class="box2"></div>
```

运行效果：

![image](https://github.com/Misszhu/myblog/raw/master/CSS/image/BFC2.png)既然属于同一个 BFC 的相邻块级元素才会发生外边距塌陷，那我们把其中一个块级元素变为 BFC 即可：

```html
<style type="text/css">
.box1 {
  width: 200px;
  height: 200px;
  margin-bottom: 20px;
  background-color: #ffb6b9;
}
.box2 {
  width: 200px;
  height: 200px;
  margin-top: 10px;
  background-color: #bbded6;
}
.box2-wrapper {
  overflow: hidden;
}
</style>
<div class="box1"></div>
<div class="box2-wrapper">
   <div class="box2"></div>
</div>
```

运行效果：

![image](https://github.com/Misszhu/myblog/raw/master/CSS/image/BFC3.png)

##### 嵌套元素的margin重叠

如果没有内容将父元素和后代元素分开，则父元素的外边距和这个后代元素的外边距会发生重叠。

```html
<style type="text/css">
* {
  margin: 0;
  padding: 0
}
.box {
  width: 200px;
  height: 200px;
  margin-top: 10px;
  background-color: #bbded6;
}
.box-wrapper {
  /* display: flex; */
  margin-top: 20px;
  background-color: #8ac6d1;
}
</style>
<div class="box-wrapper">
   <div class="box"></div>
</div>
```

![image](https://github.com/Misszhu/myblog/raw/master/CSS/image/BFC4.png)

这时我们想办法把父块元素变成 BFC，就能解决外边距重叠的问题。（把上面示例代码的注释去掉即可）。运行效果：

![image](https://github.com/Misszhu/myblog/raw/master/CSS/image/BFC5.png)

[在线运行传送门](https://jsfiddle.net/zhuhuiting/vken1dcL/14/)

#### 自适应两栏布局

自适应两栏布局的特点是左边侧边栏固定，右边主体内容根据页面宽度自动撑开。根据 BFC 规则的第二条，我们就能轻松实现这个需求：

> 2. BFC 的区域不会与 float 的元素区域重叠

```html
<style type="text/css">
  .aside {
    width: 100px;
    height: 200px;
    float: left;
    background-color: rgba(217,217,243,1);
  }
  .main {
    /* display: flex; */
    height: 300px;
    padding-left: 10px;
    color: rgba(32,54,107,1);
    background-color: rgba(206,239,228,1);
  }
</style>
<div class="aside"></div>
<div class="main">
  I am a main box.
</div>
```

[在线运行传送门](https://jsfiddle.net/zhuhuiting/9jx1g70u/13/)

运行效果：

![image](https://github.com/Misszhu/myblog/raw/master/CSS/image/BFC6.png)

不出我们所料，float 属性为 left 的盒子，会脱离标准文档流，遮挡住部分 main 盒子的内容。这不是我们想要的。不过由于**BFC 的区域不会与 float 的元素区域重叠**，把 main 盒子 设置成 BFC，aside 盒子 和 main 盒子就不会重叠了（参考实现方式：把示例代码注释去掉即可）。运行结果：

![image](https://github.com/Misszhu/myblog/raw/master/CSS/image/BFC7.png)

## 总结

一句话总结：

**BFC 就是页面中一块独立的渲染区域，它会包含它内部的所有元素，并且这块区域内部的元素不会影响到它外部的元素。**

清除浮动、处理外边距重叠等只是我们解决问题的方法，重要的是我们要透过现象发现 BFC 的本质，才能游刃有余地解决更多的问题。



参考链接：

 [BFC 原理剖析](https://github.com/zuopf769/notebook/blob/master/fe/BFC%E5%8E%9F%E7%90%86%E5%89%96%E6%9E%90/README.md)

[BFC](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Block_formatting_context)

[格式化上下文简介](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Flow_Layout/Intro_to_formatting_contexts)

[CSS-overflow](https://developer.mozilla.org/zh-CN/docs/Web/CSS/overflow)

[CSS-float](https://developer.mozilla.org/zh-CN/docs/CSS/float)

[CSS-clear](https://developer.mozilla.org/zh-CN/docs/Web/CSS/clear)

[外边距重叠](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Box_Model/Mastering_margin_collapsing)

