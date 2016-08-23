title: Flexbox
date: 2016-07-20 10:42:39
desc: flexbox 的使用方式和常见误区
---

几年前学习 [learnlayout](http://zh.learnlayout.com/toc.html) 时第一次了解到 flexbox，后来写 CSS 的任务越来越少，但还是挺关注 flexbox 的兼容情况，这几年移动端对 flexbox 的使用越来越多，相关的实践也越来越成熟。去年了解到 React Native 也在使用 flexbox 做布局设计的时候，我第一次完整地学习了 flexbox 的使用方式并写了这篇博客，但最近重新阅读之后，发现自己对相关概念仍然存在误区，所以花了一天左右的时间做各种布局实践，来检验自己对相关概念的理解并整理如下。

在 flexbox 布局中有两个核心元素：`container` 和 `item`。item 是 container 所包含的直接子节点，它们的布局方式会受到 container 的约束。对于 container，必须理解两条轴和四个点：main axis、cross axis、main start、main end、cross start、cross end。下图是两轴四点在**默认情况**下的位置，这里的默认情况是指 `flex-flow: row nowrap;`。理解 main axis 和 cross axis 能够根据 `flex-flow`动态变化这一点很重要，这也是它为什么不叫作 x 轴和 y 轴的原因，以 main axis 举例来说，`flex-flow: row nowrap` 时它是水平方向的，`flex-flow: column nowrap` 则是垂直方向的。

![Flexbox](/img/flexbox.png)

<!-- more -->

## Container

一个元素要成为 flexbox 中的 container，有两种声明类型：

```scss
.container {
    display: flex;
}

// or
.container {
    display: inline-flex
}
```

每一个 flexbox container 可以使用五种规则对 item 进行约束：

- `flex-flow: <flex-direction> <flex-wrpa>`，一种缩写形式
- `flex-direction`，约束 item 的布局排列方向，决定 main axis 的方向
- `flex-wrap`，约束 item 在超出布局边界的处理方式，决定 cross axis 的方向
- `justify-content`，约束 item 在 main axis 上的排列方式
- `align-items`，约束 item 在 cross axis 上的排列方式
- `align-content`，约束 main axis 在 cross axis 上的排列方式

如果使用 flexbox 布局的结果与预期不一致，问题很可能出现在对上述属性的误解上。这些属性从上到下是一种相互制约的关系，如果你无法在思维中明确 flex-flow 所指定的 main axis 和 cross axis，那么后续的布局都是盲目的：

- `flex-direction: row`，默认值，main axis 与水平方向平行，由左到右走向
- `flex-direction: row-reverse`，main axis 与水平方向平行，由右到左走向
- `flex-direction: column`，main axis 与垂直方向平行，由上到下走向
- `flex-direction: column-reverse`，main axis 与垂直方向平行，由下到上走向
- `flex-wrap: nowrap`，默认值，cross axis 与 main axis 方向垂直，由上到下或者由左到右走向
- `flex-wrap: wrap`，cross axis 与 main axis 方向垂直，由上到下或者由左到右走向
- `flex-wrap: wrap-reverse`，cross axis 与 main axis 方向垂直，由上到下或者由右到左走向

每一个 flexbox item 可以使用六种规则对自身在 container 中的布局方式进行微调：

- `order`，默认值为 0，数值越小，排列时越靠前
- `align-self`，调整 item 在 cross axis 上的排列方式
- `flex: <flex-grow> <flex-shrink> <flex-basis>`，一种缩写形式
- `flex-grow`，当 container 空间多余 item 所需空间时，该属性决定 item 的放大比例，默认值为 0，表示不放大
- `flex-shrink`，当 container 空间不足时，该属性决定 item 的缩小比例，默认值为 1，如果值为 0，则表示不收缩
- `flex-basis`，决定 item 在 main axis 的大小

这里的 `flex-basis` 与 `width` 是不同的概念，当 `flex-direction: row` main axis 与水平方向平行时，`flex-basis` 用于控制 `item` 的宽度；当 `flex-direction: column` main-axis 与垂直方向平行时，`flex-basis` 用于控制 `item` 的高度。更多 `flex-basis` 与 `width` 和 `height` 的不同，请参考 Stack Overflow 上的问题[《What are the differences between flex-basis and width?》](http://stackoverflow.com/questions/34352140/what-are-the-differences-between-flex-basis-and-width)。

## box-sizing

话外提一下 `box-sizing`， 该属性用于声明 `width` 和 `height` 的约束范围：`border-box` 表示边框、内边距和内容块的宽高计入容器宽高；`content-box` 表示只有内容块的宽高计入容器宽高。此外，该属性可继承，可以通过设置 body 的 box-sizing 统一约束容器的宽高。

###### 参考资料

- [flexfroggy](http://flexboxfroggy.com)
- [learnlayout](http://zh.learnlayout.com/toc.html)
- [What are the differences between flex-basis and width?](http://stackoverflow.com/questions/34352140/what-are-the-differences-between-flex-basis-and-width)
- [flexbox 布局实例](http://www.ruanyifeng.com/blog/2015/07/flex-examples.html)