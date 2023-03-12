# 用 CSS 实现垂直对齐的 15 种方法

> 原文：<https://blog.logrocket.com/15-ways-implement-vertical-alignment-css/>

***编者按*** : *本帖最后更新于**8**9 月 2 日* *022* *完善代码，更新任何过时信息。*

回到过去的美好时光，CSS 的局限性使得甚至像垂直居中这样“简单”的事情也成为一个挑战，一些开发人员甚至依赖于 JavaScript 解决方案。它是脆弱的，它是非常受约束的，并且总是有一个例外使它失败。

无论我们是试图在文本旁边对齐图标或图像，创建一个流行的英雄横幅，还是创建一个模态叠加，将东西放在垂直轴的中心总是一个难题。

但是 CSS 已经走了很长的路，提供了很多方法，每次都使垂直居中变得更容易。下面是对其中一些的总结，以及它们的使用案例和局限性。

在这篇文章中:

**补充说明:**除非明确说明，否则下面强调的每个策略也适用于内联元素，这是有意义的，因为我们将直接转换它们的位置或显示属性。

## 1.绝对定位和`margin: auto`

没有固有大小的元素可以通过简单地使用顶部和底部的相等值来居中。当一个元素有内在维度时，我们可以用`0`来表示顶部和底部，然后应用`margin: auto`。这将自动使元素居中:

```
.container{
  position:relative;
}
.element{
  position:absolute;
  top: 0; bottom: 0; left: 0; right: 0;
  margin: auto;
  height: 20px; /*requires explicit height*/
}

```

我们已经在 CodePen 中添加了额外的样式，以使演示更加美观。当然，限制是必须显式声明元素高度，否则它将占据整个容器。

## 2.经典`top:50%`，`translateY(-50%)`

对于许多开发人员来说，这是第一个技巧之一，并且仍然是一个关键。依靠绝对定位，内部元素位于距其父元素顶部 50%的位置，然后我们可以将其平移到其高度的 50 %:

```
.container{
  position: relative;
}
.element{
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
}

```

这是一个相当可靠的方法，唯一的主要限制是使用 translate 可能会妨碍其他变换，例如，在应用过渡或动画时。

## 3.以表格为中心

一个非常简单的方法，也是最早的方法之一(在过去，[一切都围绕着表格](https://blog.logrocket.com/building-a-table-component-with-tailwind-css/))，是使用表格单元格和`vertical-align`的行为将一个元素放在容器的中心。

这可以通过实际的表格或者使用语义 HTML 来完成，将元素的显示切换到`table-cell`:

```
.container{
  display: table;
  height: 100%;
}
.element{
  display: table-cell;
  text-align: center;
  vertical-align: middle;
}

```

即使两个元素的高度都未知，这也是可行的。当然，主要的限制是，如果你需要一个非居中的兄弟姐妹，背景限制可能会变得棘手。

此外，请记住，这在屏幕阅读器上完全失败(即使您的标记基于 div，将 CSS 显示设置为`table`和`table-cell`会使屏幕阅读器将其解释为实际的表格)。所以，就可访问性而言，它远非最佳。

## 4.鬼元素法

另一个因为某种原因而没有跟上的老方法是对一个具有父元素 100%高度的 ghost(伪)元素使用`inline-block`,然后为伪元素和我们想要居中的元素设置`vertical-align: middle`:

```
.container::before {
  content: '';
  display: inline-block;
  height: 100%;
  vertical-align: middle;
  margin-left: -0.5ch;
}
.element{
  display: inline-block;
  vertical-align: middle;
}

```

它实际上工作得很好，最明显的缺点是它将水平中心向右移动了一点点，因为`inline-block`元素之间的空白总是有褶皱的行为。

这可以通过调整伪元素的边距来解决。在我们的例子中，我们已经指定了`margin-left: -0.5ch`。我们还可以通过将容器上的字体大小设置为`0`，然后将其重置为元素上的`px`或`rem`来获得完美的居中:

```
.container {
  font-size: 0;
}
.container::before {
  ...
/*   margin-left: -0.5ch; */
}
.element{
  /* ... */
  font-size: 16px;
}

```

## 5.在`flex`项目上使用`margin: auto`

最后，进入[现代 CSS 领域，Flexbox](https://blog.logrocket.com/how-to-build-a-basic-flexbox-layout-a-tutorial-with-examples/) 为`auto`利润率引入了一个非常棒的行为。现在，它不仅像在块布局中一样将元素水平居中，而且还将元素在垂直轴上居中:

```
.container{
  display: flex;
}
.element{
  margin: auto;
}

```

这个策略是我最喜欢的策略之一，因为它很简单。唯一的主要限制是它只适用于单个元素。

## 6.一个`flex`容器上的伪元素

这不是世界上最实用的方法，但我们也可以使用灵活、空的伪元素将一个元素推到中心:

```
.container{
  display: flex;
  flex-direction: column;
}
.container::before,
.container::after {
  content: "";
  flex: 1;
}
.element{
  /* ... */
  margin: 0 auto;
}

```

当我们希望在包含多个项目的面向列的 flex 容器上保持灵活的间距时，这很有用。

## 7 号和 8 号。对准`flex`容器或`flex`物品

Flexbox 还引入了非常棒的对齐属性(现在被分支到它们自己的 [box 对齐模块](https://www.w3.org/TR/css-align-3/#propdef-align-content))。

这使我们能够控制项目的放置方式和空白空间的分布方式，这些方式要么需要 CSS 中的幻数来表示特定数量的元素，要么需要聪明的 JavaScript 来表示动态数量。

根据`flex-direction`，我们可以使用`justify-content`或`align-items`根据需要进行调整。

在容器上:

```
.container{
  display: flex;
  justify-content: center;
  align-items: center;
}

```

在特定的弹性项目上:
<

```
.container{
  display: flex;
}
.element{
  align-self: center;
  margin: 0 auto;
}

```

这并没有太多的缺点，除非你需要支持旧的浏览器。IE 11 应该可以工作，但是它的 Flexbox 实现有很多 bug，所以应该格外小心。

IE 10 需要具有不同语法的额外工作，并且需要`-ms`厂商前缀。

## 9.对行内元素使用`vertical-align`

您还可以使用 vertical-align 属性将内联、内联块或表格单元格元素垂直居中。这种方法的许多应用之一是垂直对齐图像和文本，或者垂直对齐表格单元格的内容。

```
.element {
  display: <inline OR inline-block>;
  vertical-align: middle;
}

```

> 没有描述

密码链接:[https://codepen.io/asaoluelijah/pen/gOzgjxo](https://codepen.io/asaoluelijah/pen/gOzgjxo)

> 这种与 block 元素不兼容的方法可能会导致交易失败。除此之外，它工作得相当好，并且也受到老版本浏览器的支持。

更多来自 LogRocket 的精彩文章:

* * *

### 10 和 11。对准`grid`容器或`grid`物品

* * *

## CSS grid 包括与 Flexbox 几乎相同的对齐选项，所以我们可以在`grid`容器上使用它:

或者我们可以只在特定的`grid`物品上使用它:

```
.container{
  display: grid;
  align-items: center;
  justify-content: center;
}

```

缺乏传统浏览器支持是这种技术的唯一限制。

```
.container{
  display: grid;
}
.element{
  justify-self: center;
  align-self: center
}

```

12.网格上的伪元素

## 与 Flexbox 替代方案类似，我们可以使用带有伪元素的三行网格:

记住`[1fr]([https://github.com/w3c/csswg-drafts/issues/1777](https://github.com/w3c/csswg-drafts/issues/1777))` [实际上是](https://github.com/w3c/csswg-drafts/issues/1777) `[minmax(auto, 1fr)]([https://github.com/w3c/csswg-drafts/issues/1777](https://github.com/w3c/csswg-drafts/issues/1777))`的意思，所以空行不一定会占集装箱高度的三分之一。它们会根据需要一路折叠到它们的最小值`auto`，没有内容就意味着`0`。

```
.container{
  display: grid;
  grid-template-columns: 1fr;
  grid-template-rows: repeat(3, 1fr);
}
.container::before,
.container::after{
  content:"";
}

```

这可能看起来像一个愚蠢的方法，但它允许我们轻松地实现我最喜欢的 CSS 网格技巧之一:将`fr`行与`minmax`行组合，这导致空的`fr`行首先折叠，然后是`mixmax`行。

因此，让伪对象接受完全可折叠的行允许自动放置算法在我们的实际元素上施展魔法，除非我们需要支持缺乏自动放置的 IE。这让我们想到了下一个方法…

13.显式网格行放置

## CSS grid 允许将项目显式地放置在特定的行上，因此与上面相同的网格声明和放置在第二行上的项目就足够了:

这可以工作到 IE10。信不信由你，IE 是 CSS grid 最早也是最强有力的支持者之一，早在 2011 年 IE10 发布时就开始支持了。它有一个完全不同的语法，但我们可以让它工作:

```
.container{
  display:grid;
  grid-template-columns:1fr;
  grid-template-rows: repeat(3, 1fr);
}
.element{
  grid-row: 2 / span 1; /* or grid-row: 2/3 */
}

```

14.`margin: auto`在一个`grid`项目上

```
.container{
  display: -ms-grid;
  -ms-grid-rows: (1fr)[3];
  -ms-grid-columns: 1fr;
}
.element{
  -ms-grid-column: 1;
  -ms-grid-row: 2;
}

```

## 与 Flexbox 类似，在`grid`项目上应用`margin:` `auto`会使其在两个轴上居中。

**15。** `grid` **+** `place-items`

```
.container{
  display: grid;
}
.element{
  margin: auto;
}

```

## 把最好的留到最后；另一个漂亮而简单的`grid`实现是将`center`值应用于同一个网格元素中的`place-items`属性，它的所有子元素都神奇地居中。

没有描述

```
.container{
  display: grid;
  place-items: center;
}
.element{
/* Additional styling for child element? */
}

```

> 密码链接:[https://codepen.io/asaoluelijah/pen/dyeNjYM](https://codepen.io/asaoluelijah/pen/dyeNjYM)

然而，`place-item`属性仍然相对较新，浏览器支持可能是一个问题，因为它与 IE11 和 Chrome 4-58 等旧浏览器不兼容。

> 一些(可能的)未来实现

根据 [CSS 框对齐模块 3 级规范](https://www.w3.org/TR/css-align-3/) , `align-content`应该在`block`容器和`multicol`容器的块轴上工作，所以(如果浏览器实现了它)我们应该能够将这些容器的内容居中，就像我们在`flex`或`grid`容器中所做的那样。

## 结论

仅此而已。曾经极其困难的事情现在可以通过十几种不同的方式来实现，我可能还会错过一些。如果你知道其他技巧，请在评论中分享。

## Conclusion

And that’s it. What was once extremely hard can now be achieved in a dozen-plus different ways, and I’m probably missing a couple more. If you know other techniques, please share them in the comments.