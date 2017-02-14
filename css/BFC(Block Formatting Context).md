# BFC(Block Formatting Context)

标签： css

---

## what is BFC 

### BFC 定义

BFC(Block formatting context) 直译为"块级格式化上下文"。它是一个独立的渲染区域，只有 **Block-level Box** [块级元素]参与， 它规定了内部的 Block-level Box 如何布局，并且**与这个区域外部毫不相干**。

### BFC 布局规则

- 内部的 Box 会在垂直方向，一个接一个地放置。
- Box 垂直方向的距离由 margin 决定。属于同一个 BFC 的两个相邻 Box 的 margin 会发生重叠.
- 每个元素的 margin box 的左边， 与包含块 border box 的左边相接触(对于从左往右的格式化，否则相反)。即使存在浮动也是如此。
 >关于这条规则的补充说明：
 当容器有足够的剩余空间容纳 BFC 的宽度时，所有浏览器都会将 BFC 放置在浮动元素所在行的剩余空间内.
 当 BFC  的宽度大于容器剩余宽度时，最新版本的浏览中只有firefox会在同一行显示，其它浏览器均换行。
- BFC 的区域不会与 float box 重叠。
- BFC 就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素。反之也如此。
- 计算 BFC 的高度时，浮动元素也参与计算。

### 补充

Formatting context 是 W3C CSS2.1 规范中的一个概念。它是页面中的一块渲染区域，并且有一套渲染规则，它决定了其子元素将如何定位，以及和其他元素的关系和相互作用。最常见的 Formatting context 有 Block fomatting context (简称BFC)和 Inline formatting context (简称IFC)。
CSS2.1 中只有 BFC 和 IFC, CSS3 中还增加了 GFC 和 FFC。

## How to create a BFC

W3C标准中这样描述：

> Floats, absolutely positioned elements, block containers (such as inline-blocks, table-cells, and table-captions) that are not block boxes, and block boxes with 'overflow' other than 'visible' (except when that value has been propagated to the viewport) establish new block formatting contexts for their contents.

中文大致是：

> 非块级盒子的浮动元素、绝对定位元素及块级容器(比如inline-blocks，table-cells和table-captions)，以及overflow属性是visible之外任意值的块级盒子，都会创建了一个BFC。

即当元素CSS属性设置了下列情况之一是，就可以创建一个BFC：

- **float**: 属性值不为 `none`
- **position**: 属性值为 `absolute` 或 `fixed`
- **display**: 属性值为 `table-cell`, `table-caption`, `inline-block`, `flex`, `inline-flex`
- **overflow**: 属性值不为 `visible`

## The practical application of BFC

### 1. Using BFC to Prevent Margin Collapse

首先，我们要知道什么是 **Margin Collapse**，详细内容可以从[这篇文章](https://www.sitepoint.com/web-foundations/collapsing-margins/)了解。

分两种情况理解：

1. 相邻元素之间的 Collapsing Margins

  当相邻的两个块级元素的 `margin` 都是正值的时候，则两个元素之间的 `margin` 取两者之间的较大值，另外一个元素的 `margin` 就会出现 `margin collapse` 的情况。看如下例子
  
  ```html
  <h1>heading content</h1>
  <p>paragraph content</p>
  ```
  
  ```css
  h1 {
      margin-bottom: 20px;
      background-color: red;
  }

  p {
      margin-top: 10px;
      background-color: lightgreen;
  }
  ```
  
  则上面代码中 h1 和 p 之间的 `margin` 的距离是 20px。
  
  当相邻的两个元素之间的 `margin` 出现一个或者两个都是负值的时候，则两个元素之间的 `margin` 取两次之间的和。如果把上面例子中的 p 的 css 修改为如下：
  
  ```css
  p {
      margin-top: -5px;
      background-color: lightgreen;
  }
  ```
  
  这时候，h1 和 p 之间的 `margin` 的距离就是 15px了。
  
2. 父元素和子元素之间的 Collapsing Margins

  当父元素的 `margin` 和第一个子元素的 `margin` 直接接触的时候，就是说他们之间 `padding` 和 `border` 都是 0 的时候，则两个元素之间的 `margin` 取两者之间的较大值，另外一个元素的 `margin` 就会出现 `margin collapse` 的情况。看如下例子
  
  ```html
  <h1>heading content</h1>
  <div>
      <p>paragraph content</p>
  </div>
  ```
  
  ```css
  h1 {
  	  margin-bottom: 0px;
	  background-color: red;
  }
  div {
      margin: 40px 0 25px 0;
      background: #cfc;
  }
  p {
      margin-top: 10px;
      background-color: lightgreen;
  }
  ```
  
  则上面代码中 div 和 p 之间的 `margin` 的距离是 40px。
  
  div 的 `margin` 和 p 的 `margin` 直接接触，所以选择了两者中较大的一个 40px 作为父元素 div 的 `margin`，所以 P 会紧紧地贴在 div 的顶部。
  
  如果两个元素之间的 `margin` 出现一个或者两个都是负值的时候，则同样，取两个元素之间的 `margin` 两次之间的和。
  
  如果父元素与子元素之间不是直接接触，比如给 div 设置 `padding` 或者 `border`，则两个元素之间的 `margin` 就是两者的和。

回到 BFC，于是 BFC 就可以解决 margin collapse 的问题。

如果父元素是 BFC，则就可以解决 margin collapse 的问题，继续看上面的例子，修改css如下

```css
div {
  margin: 40px 0 25px 0;
  background: #cfc;
  overflow: hidden;
}
```

于是 div 和 p 的 `margin` 的距离就变为 60px，也就解决了 margin collapse 的问题。

### 2. Using BFC to Contain Floats

在很多情况下，我们把元素设置为浮动元素之后，如果没有给父元素设置高度，则父元素高度为0，那是因为子元素设置 `float`，已经脱离了页面文档流了。

很多情况下，我们需要清除浮动来解决，不过利用 BFC 就可以很方便的解决这个问题。只需要将父元素设置为 BFC 即可。
如下例子：

```html
<div class="container">
  <div>Sibling</div>
  <div>Sibling</div>
</div>
```

```css
.container {
  background-color: green;
  overflow: hidden;
}

.container div {
  background-color: lightgreen;
  float: left;
  margin: 10px;
}
```

效果如下：

![](http://ww1.sinaimg.cn/large/005JoIL8gy1fcq2kn3my3j30m805xgmj)

### 3. Using BFC to Prevent Text Wrapping

在前面提到的 BFC 规则中的第四条说道： BFC 不会与 float box 元素重叠。这个规则可以来防止 Text Wrapping。如下例子：

```html
<div class="floated">
	Floated div
</div>
<p class="bfc">
	text content text content text content text content 
</p>
```

```css
.floated {
  float: left;
  margin: 5px;
}
p {
  margin: 0;
  text-align: left;
  background-color: green;
  color: white;
  padding: 10px;
	width: 200px;
}
.bfc {
  overflow: hidden;
}
```

一般情况下，是需要借助设置右边元素 `margin` 来实现，但是利用 BFC 这个特性，将右边 p 设置为 BFC 就可以更为方便就实现这样的左边定宽浮动，右边响应的两栏布局。

![](http://ww1.sinaimg.cn/large/005JoIL8gy1fcq3utyku6j30jg05nwfs)

## 4.Using BFC in Multi-column Layouts

同样利用 BFC 的这个特性来解决栅栏布局的最后一栏的问题

```html
<div class="container">
  <div class="column">column 1</div>
  <div class="column">column 2</div>
  <div class="column">column 3</div>
</div>
```

```css
.column {
  width: 31.33%;
  background-color: green;
  float: left;
  min-height: 100px;
  margin-right: 1%;
}
.column:last-child {
  float: none;
	overflow: hidden;
}
```

因为栅栏布局的最后一列有时候会下降到下一行,这可能是因为浏览器在计算过程四舍五入出现了宽度大于 container 的宽度原因, 借助 BFC 可以解决这个问题。

## 参考链接

- [Understanding Block Formatting Contexts in CSS](https://www.sitepoint.com/understanding-block-formatting-contexts-in-css/)
- [Collapsing Margins](https://www.sitepoint.com/web-foundations/collapsing-margins/)
- [BFC 神奇背后的原理](http://www.cnblogs.com/lhb25/p/inside-block-formatting-ontext.html)




