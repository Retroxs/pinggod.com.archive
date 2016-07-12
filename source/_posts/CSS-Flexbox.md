title: CSS Flexbox
date: 2015-12-02 10:42:39
---

如果你正在学习使用 CSS 布局，推荐来 [learnlayout](http://zh.learnlayout.com/toc.html) 了解一下这些年前端所用到的各类布局手法。这里之所以使用“手法”一词，意指其中多少有些偏激取巧，比如使用浮动（float）来定位、使用 margin 来伸缩空间等等。

float、margin 本不应该用于布局，只是囿于早期的 CSS 布局模块发展缓慢且不合时宜，促使开发者另辟蹊径，借助其他样式来模拟布局效果。最近几年随着浏览器对布局模块的支持度越来越高，Flexible Box Layout（Flexbox）、Grid Layout、Multiple Column Layout 也逐渐为开发者所接受，其中以 Flexbox 的兼容性最好，拥护者也随之水涨船高。

就个人感受而言，未来的布局方式会归纳为两类：一类是纯粹使用浏览器兼容性高的布局模块，比如 Flexbox，这也是未来的发展趋势；另一类是使用预处理器或者框架自定义的布局模块，这只是目前的缓兵过渡之计。

<!-- more -->

在几个月前，我曾经使用过一段时间的 [Susy](http://susy.oddbird.net/)。 Susy 是基于 Sass 的一款布局框架，其核心是使用非布局样式来模拟布局效果，最大的优点在于封装布局样式之后提供了一套简洁明了的布局接口。比如，在下面的 Sass 代码中，`.contianer` 被附加了一套容器样式，嵌套在其中的 `item` 占据总体宽度的 4 / 10。

```scss
.container {
    @include container;  
    .item {
        @include span(4 of 10);
    }
}
```

> Susy 的理念是帮助开发者规避数学计算，所以它有一条大快人心的口号：YOUR MARKUP, YOUR DESIGN, YOUR OPINIONS, OUR MATH。

## Flexbox

在数学计算的问题上，我觉得 Flexbox 和 Susy 有异曲同工之妙，只是相比起来，原生的 Flexbox 更加简捷。在 Flexbox 中有两个核心元素：`container` 和 `item`，所有的样式也是围绕这两类元素计算的。下图中有两条红线，分别代表在水平方向和垂直方向进行布局的基线。

![Flexbox](/img/flexbox.png)

Flexbox 中的 contianer 元素需要解决两个问题：自身的类型以及内部 item 的排列方式。通过 `display: flex` 和 `display: inline-flex` 可以将 container 声明为块级或者行内块级，从而确定了 container 自身的类型。使用以下属性则可以确定 container 内部 item 的排列方式：

- flex-flow：flex-direction 和 flex-wrap 的缩写
- flex-direction：决定 item 的排列方向
- flex-wrap：决定 item 的溢出容器后的处理方式
- justify-content：决定 item 在水平方向上的对齐方式
- align-item：决定 item 在垂直方向上的对齐方式
- align-content：决定多个 main axis 在垂直方向上的对齐方式

![flex-container](/img/flexbox-container.png)

item 元素需要解决的问题集中于自身上，包括自身在 container 中的顺序、缩放、对齐方式。使用以下属性可以设置 item 自身的布局样式：

- order：决定 item 的顺序，默认值为 0，值越小越靠前
- flex：flex-grow、flex-shrink 和 flex-basis 的缩写
- flex-grow：决定 item 的放大比例，默认值为 0，0 表示不放大
- flex-shrink：决定 item 的缩小比例，默认值为 1，0 表示不缩小
- flex-basis：浏览器分配 container 剩余空间时，决定 item 获得的比重
- align-self: 决定自身在垂直方向的对齐方式

![flex-item](/img/flexbox-cell.png)

## box-sizing

话外提一下 `box-sizing`， 该属性用于声明 `width` 和 `height` 的约束范围：`border-box` 表示边框、内边距和内容块的宽高计入容器宽高；`content-box` 表示只有内容块的宽高计入容器宽高。此外，该属性可继承，可以通过设置 body 的 box-sizing 统一约束容器的宽高。

## FlexFroggy

这是一个寓教于乐的 Flexbox 布局小游戏，难度中下，很有意思：[http://flexboxfroggy.com](http://flexboxfroggy.com)。