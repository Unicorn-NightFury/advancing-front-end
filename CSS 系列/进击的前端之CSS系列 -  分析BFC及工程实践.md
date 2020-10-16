## 进击的前端之CSS系列 -  分析BFC及工程实践

> 这是[进击的前端项目](https://github.com/Unicorn-NightFury/advancing-front-end.git)之CSS系列的`第一篇`。
>
> 进击的前端这个项目目的在于构建大前端的体系化知识，感兴趣的话可以给个`Star`关注一下这个项目

### 从定义及表现入手

首先我们要知道什么是`BFC`。`BFC(Block Formatting Context)`翻译过来名叫`格式化上下文`，是Web页面可视化`CSS`渲染的一部分。

要想深入了解一个东西，笔者认为理解其定义是有必要的。我们来看看啊`MDN`给出的关于`BFC`的定义以及相关的特征描述。

> 块格式化上下文（Block Formatting Context，BFC） 是Web页面的可视CSS渲染的一部分，是块盒子的布局过程发生的区域，也是浮动元素与其他元素交互的区域。
>
> 下列方式会创建块格式化上下文：
>
> 根元素（<html>）
> 浮动元素（元素的 float 不是 none）
> 绝对定位元素（元素的 position 为 absolute 或 fixed）
> 行内块元素（元素的 display 为 inline-block）
> 表格单元格（元素的 display 为 table-cell，HTML表格单元格默认为该值）
> 表格标题（元素的 display 为 table-caption，HTML表格标题默认为该值）
> 匿名表格单元格元素（元素的 display 为 table、table-row、 table-row-group、table-header-group、table-footer-group（分别是HTML table、row、tbody、thead、tfoot 的默认属性）或 inline-table）
> overflow 值不为 visible 的块元素
> display 值为 flow-root 的元素
> contain 值为 layout、content 或 paint 的元素
> 弹性元素（display 为 flex 或 inline-flex 元素的直接子元素）
> 网格元素（display 为 grid 或 inline-grid 元素的直接子元素）
> 多列容器（元素的 column-count 或 column-width 不为 auto，包括 column-count 为 1）
> column-span 为 all 的元素始终会创建一个新的BFC，即使该元素没有包裹在一个多列容器中（标准变更，Chrome bug）。
>
> 块格式化上下文包含创建它的元素内部的所有内容.
> 块格式化上下文对浮动定位（参见 float）与清除浮动（参见 clear）都很重要。浮动定位和清除浮动时只会应用于同一个BFC内的元素。浮动不会影响其它BFC中元素的布局，而清除浮动只能清除同一BFC中在它前面的元素的浮动。外边距折叠（Margin collapsing）也只会发生在属于同一BFC的块级元素之间。



关于`BFC`笔者先给出一个简单的解释，等下会结合官网以及代码实践来加深对`BFC`的理解。

首先我们需要知道`CSS`中的一个布局对象：`盒模型`（也称作Box)。Box是我们布局的基本单位，也就是说一个页面是有多个Box组成的，Box之间可能会有着嵌套关系。而元素的类型和`display`属性决定了这个Box的类型。不同类型的Box会参与到不同的格式化上下文（Formatting Context），从而导致Box会以不同的方式渲染。而`Formatting Context`常见的有`Block Formatting Context`（BFC) 以及`Inline Formatting Context`（IFC）。在CSS3中还增加了`GFC`和`FFC`。

也就是说，所谓的`BFC`其实就是一个渲染规则（或者说渲染方式）。这样是不是好理解多了？我们来看看它到底制定了哪些规则？

**1、BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素。反之也如此。**这点很重要，这是`BFC`最直观的体现。

2、内部的Box会在垂直方向，一个接一个地放置。Box垂直方向的距离由margin决定。属于同一个BFC的两个相邻Box的margin会发生重叠
3、每个元素的margin box的左边， 与包含块border box的左边相接触，即使存在浮动也是如此。
BFC的区域不会与float box重叠。

4、计算BFC的高度时，浮动元素也参与计算

接下来我们通过`MDN`给出的描述以及`BFC的渲染规则`来用代码理解下`BFC`以及如何触发`BFC`



#### 根元素会创建BFC

我们可以看到，`MDN`给出的第一个触发`BFC`的东西就是我们的`<html>`根元素。我们通过`BFC`中相邻BOX的`margin`会重叠这一点来验证一下吧：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>

        .box-one {
            height: 100px;
            width: 100px;
            background-color: pink;
            margin: 15px;
        }

        .box-two {
            height: 200px;
            width: 200px;
            background-color: black;
            margin: 20px;
        }

    </style>
</head>
<body>
    <div class="box-one"></div>
    <div class="box-two"></div>
</body>
</html>
```



![0bVVaT.png](https://s1.ax1x.com/2020/10/16/0bVVaT.png)

可以观察到，我们的两个Box的`margin`发生了重叠，两者之间真正的距离是较大的`margin`的值。

如何解决这种`margin`重叠问题呢？很简单，抽离其中一个`div`放到另一个`BFC`中，让他们不在同一个`BFC`下

![07hMQK.png](https://s1.ax1x.com/2020/10/16/07hMQK.png)

我们将代码做了一定的更改，将`box-one`放到一个新的`div`中，通过`overflow: hidden`实现了一个新的`BFC`。所以便能成功结局`merge`重叠问题

![07huz6.png](https://s1.ax1x.com/2020/10/16/07huz6.png)

可以看到，通过理解Box的渲染规则我们能够很巧妙地解决我们平时由于基础不牢带来的布局问题。



#### 通过BFC巧妙解决高度塌陷问题

有`CSS`基础的同学应该知道在普通文档流中，如果父元素没有设置高度，那么父元素的高度是有子元素撑开的，及子元素有多高，父元素就有多高。但是当子元素设置`float`浮动后，子元素就会完全脱离文档流，父元素还在文档流中，此时父元素的高度就没有子元素撑起（子元素无法撑起父元素的高度），从而导致父元素的高度塌陷，也就是父元素的高度为0了。

我们结合工程场景来看看这个问题：

*假设现在我们需要商品页面，现在有两行的商品展示栏。每一栏中有一些商品坑位，但是坑位的高度不统一。*

我们按照要求大致编写下样式

![0bElE8.png](https://s1.ax1x.com/2020/10/16/0bElE8.png)

我们想要达到的目标是`row-one`为第一行，`row-two`为第二行，`commodity-one`和`commodity-two`在`row-one`这样行中并排显示，`commodity-three`在`row-two`中显示。

但是发现我们的编码并没有达到我们的预期：

![0bEva8.png](https://s1.ax1x.com/2020/10/16/0bEva8.png)

想要多个块级元素并排有很多种方法，有时候为了方便我们会首先想到使用`float`让元素浮动。

基础较为薄弱的同学可能对于`float`的用法以及表项不太清楚，我们一起来尝试巩固一下吧：

**首先，仅对`commodity-two`这个元素添加`float:left`看看效果**

![0bZHBt.png](https://s1.ax1x.com/2020/10/16/0bZHBt.png)

可以看到`commodity-two`脱离了文档流，它后面的元素往上移动。注意，我们的`float`脱离文档流后，是不会对其前面的元素产生影响的。所以我们要想`commodity-tow`与`commodity-one`并排的话，我们需要`commodity-one`也加上`float:left`。

![0bmJoV.png](https://s1.ax1x.com/2020/10/16/0bmJoV.png)

我们可以看到，`commodity-one`和`commodity-two`两个元素达到了并列的效果，可是这两个元素的父元素却由于浮动产生了高度塌陷，`row-one`的高度变成了0。如何解决这个问题呢？通过笔者刚刚写的`BFC`渲染规则的第四点可以找到解决方案：**计算BFC的高度时，浮动元素也参与计算**

所以我们只需将`row-one`这个父元素设置为`BFC`，那么它的两个浮动子元素的高度便会被计算上，使得`row-one`像是有了高度一样。通过给`row-one`设置`overflow:hidden`达到`BFC`，以下是代码以及效果（注：笔者为了能够体现一个不断更改代码的过程，所以代码编写顺序不一定符合规范，不过这并不碍事，只要专注我们手头研究的东西即可）

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        
        .commodity-one {
            height: 100px;
            width: 100px;
            background-color: pink;
            float: left;
        }

        .commodity-two {
            height: 200px;
            width: 200px;
            background-color: black;
            float: left;
        }

        .commodity-three {
            height: 150px;
            width: 150px;
            background-color: yellow;
        }

        .row-one {
            overflow: hidden;   /* 触发BFC */
        }

    </style>
</head>
<body>

    <div class="row-one">
        <div class="commodity-one"></div>
        <div class="commodity-two"></div>
    </div>

    <div class="row-two">
        <div class="commodity-three"></div>
    </div>

</body>
</html>
```

![0buVC8.png](https://s1.ax1x.com/2020/10/16/0buVC8.png)

由此可见达到了我们的目的，只需再加上`margin`给我们的商品坑位之间一下距离即可。



#### 经典布局方案实现 - 自适应两栏布局

双栏布局是我们经常可以看到的一种布局方式，就是信息分为两列（也就是两栏）展示，例如国内有名的Web社区`掘金`就是采用这种布局：

![0bQuKe.png](https://s1.ax1x.com/2020/10/16/0bQuKe.png)

双栏布局的另一大特点便是自适应，确定其中一列（一般确定左列，这样实现较为简洁）的宽度，然后另一列撑满剩下的宽度。

我们先按照要求实现一下。首先，要想自适应，我们其中一列的宽度是无法给定的。其次，要想两列并排，我们的第一个想到`float`而不是`flex`。（因为`flex`虽然想，但是某些浏览器就是会有兼容问题，我就不点名是微软的某个E了，但是以后兼容性问题会越来越少的，该用`flex`的还是要放心用。这里只是为了达到文章目的所以我们采用`float`，而且`float`处理这种少量Box还是要优雅许多的。）

![0b18c8.png](https://s1.ax1x.com/2020/10/16/0b18c8.png)

[![0b1Yng.png](https://s1.ax1x.com/2020/10/16/0b1Yng.png)](https://imgchr.com/i/0b1Yng)

可以发现，我们的实现效果非常的糟糕。这是由于什么导致的呢？因为我们只对`left`元素设置了`float`，它脱离了文档里，`right`元素自动填满了整个页面，那我们给`right`元素也添加一个`float:left`呢？这个时候`right`元素也会脱离文档流，将不会再填充原本流中的宽度，其宽度变为其内容的宽度：

![0b3UKO.png](https://s1.ax1x.com/2020/10/16/0b3UKO.png)

我们来看看笔者之前给出的`BFC`的规则的第三条，**`BFC`区域不会跟`float box`重叠**，所以解决方案便是我们触发`right`元素的`BFC`

![0b8gYR.png](https://s1.ax1x.com/2020/10/16/0b8gYR.png)

![0b8btA.png](https://s1.ax1x.com/2020/10/16/0b8btA.png)

完美解决！



### One more thing

很多人会问，为什么大厂要问很多关于布局的问题或者说我们为什么要关注那么多布局的问题？其实一个好的布局，不光光是能够将信息很好地呈现，还能能够达到性能优化以及提高用户体验的效果。下一节`CSS系列`笔者想要为大家介绍工程中几种经典的布局。

