# 自定义动画和过渡的 CSS ::before 和::after

> 原文：<https://blog.logrocket.com/css-before-after-custom-animations-transitions/>

***编者按:**这本使用 CSS `::before`和`::after`创建自定义动画和过渡的指南于 2022 年 11 月 20 日更新，以包括更多关于伪元素和伪类之间差异的信息，并添加交互式代码示例。*

你有没有在网站上看到一个精美的动画组件，心里想:“哇！这很棒——我希望我能做到这一点,”但很快就放弃了这个想法，因为你认为这要么超出了你的技能范围，要么只能使用某种动画库来实现？

你会惊讶地发现，你每天看到的大多数复杂设计都是用普通的 CSS 创建的，使用了伪元素的力量。在这篇文章中，我们将看看如何使用这些伪元素来创建惊人的效果。

我们将了解伪元素——特别是`::before`和`::after`伪元素——以及我们如何利用它们来创造令人惊叹的动画过渡。我们将首先创建一个简单但有创意的动画按钮来感受一下。然后，我们将通过创建一个动画简介卡来展示`::before`和`::after`伪元素的真正力量，从而更上一层楼。

在我们开始有趣的部分之前，我们必须先了解一些基础知识，让我们自己熟悉制作动画所需的一切。你应该对 HTML 和 CSS 有一个基本的了解。你还需要一个代码编辑器和浏览器。

*向前跳转:*

## 为什么要用动画？

动画创造了用户和网站之间的微观互动。抓住用户的注意力可能很难，但是一个设计良好、位置恰当的动画可以通过让用户对你网站的内容感兴趣来吸引他们。

酷酷的动画也给看起来简单的界面设计注入了生命，当围绕用户行为设计时，通过提供视觉反馈，有助于改善 UX。

## 什么是 CSS 伪元素？

[伪元素是 CSS 选择器](https://blog.logrocket.com/css-pseudo-elements-guide/)用来插入人工的或装饰性的内容。例如，在实际的 HTML 标记中找不到的内容。伪元素还对元素的某些部分进行样式化。有几个伪元素，如`::before`、`::after`、`::placeholder`、`::first-letter`等等。但是在这篇文章中，我们将关注`::before`和`::after`。

### `::before`伪元素

元素的 [`::before`伪元素](https://developer.mozilla.org/en-US/docs/Web/CSS/::before)在元素前插入一些内容。伪元素是所选元素的第一个子元素:

```
h1::before {
  content: "😀";
}
```

上面的输出将是:

![Emoji goes before Hello World](img/13a9b83c4de8819863d2176166c30b61.png)

### `::after`伪元素

[`::after`伪元素](https://developer.mozilla.org/en-US/docs/Web/CSS/::after)用于在元素内容之后插入一些内容。伪元素是所选元素的最后一个子元素:

```
h1::after{
  content: "😀";
}
```

类似地，上面的输出将是:

![Emoji goes after Hello World](img/18c261afd6ddf292059b0ed27bdd9e1f.png)

## 伪元素和伪类有什么区别？

伪元素有时会被混淆为伪类，因为它们看起来和听起来很相似，但事实并非如此。

伪元素是附加到选择器的关键字，它允许您对所选元素的特定部分进行样式化。例如， [`::first-letter`](https://developer.mozilla.org/en-US/docs/Web/CSS/::first-letter) 可以用来样式化一个段落的第一个字母。伪元素是以双冒号开头的关键字:`::pseudo-class-name`:

```
/* the first letter of every paragraph is colored red and given a bigger font 
size */
p::first-letter{
  color:red;
  font-size: 1.25rem;
}
```

`::before`和`::after`伪元素是特殊的，因为它们将内容添加到所选元素的特定部分。

相比之下，伪类是附加到选择器的关键字，以元素的状态为目标。例如，它可以识别元素是否是第一个子元素(位置状态)，输入元素是否处于有效状态，或者元素是否被鼠标指针悬停(用户操作状态)。

伪类是以单个冒号开头的关键字:`:pseudo-class-name`。

一个典型的例子是`:hover`伪类，当用户悬停在一个元素上时，它用于应用 CSS 规则。这通常用于交互式元素，如链接和按钮:

```
/* Any button which the user's pointer is hovering over changes color */
button:hover {
  background-color: lightgrey;
}
```

还有更多针对不同类别的伪类:

回到我们之前的第一个字母的例子，我们可能不希望每个段落都使用这种风格。第一段的第一个字母以这种方式书写更为常见，就像旧手稿的规范一样。我们可以将 [`:first-of-type`](https://developer.mozilla.org/en-US/docs/Web/CSS/:first-of-type) 伪类与 [`::first-letter`](https://developer.mozilla.org/en-US/docs/Web/CSS/::first-letter) 伪元素结合使用:

```
/* the first letter of the first paragraph is colored red and given a bigger font 
size */
p:first-of-type::first-letter{
  color:red;
  font-size: 1.25rem;
}
```

## 使用伪元素制作动画

在我们进入文章的项目部分之前，我们必须达成共识。让我们确保对一些 CSS 属性有一个基本的了解，这些属性使得用 CSS 制作动画成为可能:

*   [`transform`](https://developer.mozilla.org/en-US/docs/Web/CSS/transform) :这个属性可以让你旋转，缩放，倾斜，移动一个元素。这是通过它的[转换函数来完成的，比如`rotate()`和`scale()`T6](https://blog.logrocket.com/deep-dive-css-individual-transform-properties/)
*   [`transition`](https://developer.mozilla.org/en-US/docs/Web/CSS/transition) :通过元素从一种状态到另一种状态的转换来定义动画
*   [`position`](https://blog.logrocket.com/css-reference-guide-position/) :设置元素在文档中的定位方式。相对于父元素定位伪元素是很常见的
*   [`z-index`](https://developer.mozilla.org/en-US/docs/Web/CSS/z-index) :设置[定位](https://developer.mozilla.org/en-US/docs/Web/CSS/position)元素的`z-order`。重叠的元素根据它们的`z-index`进行堆叠。具有较大`z-index`的元素将覆盖那些具有较小`z-index`的元素

我假设您可能熟悉其中的大多数，如果不是全部的话，但是为了以防万一，我们将粗略地看一下它们。如果你已经熟悉它们，请随意[跳到教程](#creating-animated-button-pseudo-elements)。

### 一看 CSS `transform`和`transition`

我们将在我们的项目中使用 CSS `transform`和`transition`属性，所以您对它们是什么以及它们如何工作有一个基本的了解是很重要的。 [CSS 变换属性](https://blog.logrocket.com/deep-dive-css-individual-transform-properties/)基本上允许你平移(移动)、旋转、缩放和倾斜一个元素:

```
.box-1 {  
 transform: translate(100px, 100px);
}
.box-2 {  
 transform: rotate(60deg);
}
.box-3 {  
 transform: scale(2);
}
.box-4 {  
  transform: skew(-30deg);
}
```

![CSS Translate, Rotate, Scale, and Skew Effects Diagram](img/31c9d6725b1384ad0b341cc29a0cd7bf.png)

“过渡”属性允许您设置这些变化从一种状态到另一种状态发生的持续时间，从而平滑整个动画过程。要了解 CSS 中动画和过渡计时功能的更多信息，请查看这篇文章。

## 用`relative`和`absolute`属性定位

有几个 CSS 属性可以帮助您轻松地控制 HTML 文档中元素的流向和位置，但是对于本文来说，我们只关注`relative`和`absolute`位置属性。

### `relative`位置

将元素的位置设置为`relative`可让您控制元素相对于其正常文档流的位置。例如，您可以四处移动它，并将默认情况下它所在的位置用作参考点。

这里有一个例子:

```
.box-2 {
  position: relative;
  right: -200px;
  top: 0;
}

```

看笔 [相对位置](https://codepen.io/robatronbobby/pen/wvXxeaj)由 rob 2([@ robatronbobby](https://codepen.io/robatronbobby))
上[码笔](https://codepen.io)。

![CSS Setting Relative Position in the Flow](img/ea969a9b73deaa20248c755dbf57b799.png)

As you can see, the second box gets moved from the right by 200px from its original position, which also doesn’t affect the natural flow of the document as the previous space it occupied is respected by the surrounding elements.

### `absolute`位置

当将元素的位置设置为`absolute`时，该元素将从正常的文档流中提取，并且不会在页面布局中为该元素创建任何空间(可能会使其与其他元素重叠)。它相对于其最近的祖先(位置不同于`static`的元素)定位。当它找不到任何包含定位元素时，它使用文档的主体作为相对参考点。

其最终位置由`top`、`right`、`bottom`和`left`的值决定:

看笔 [绝对定位](https://codepen.io/robatronbobby/pen/ZERjydW)由 rob 2([@ robatronbobby](https://codepen.io/robatronbobby))
在 [CodePen](https://codepen.io) 上。

在这个例子中，我们创建一个`relative`定位的父节点和`absolute`定位的子节点:

```
.parent-container {
  position: relative;
}

.box-1 {
  position: absolute;
  left: 20px;
  top: 20px;
}

.box-2 {
  position: absolute;
  right: 50px;
  bottom: 40px;
}
```

![Setting Absolute Position Inside a Relative Container ](img/d09ca5b177cf92863b5dbd4b4f5d149f.png)

如您所料，父容器成为使用提供的坐标定位其绝对子容器的相对参考点。你可以试试 Ahmad Shadeed 的这篇互动文章，以了解更多关于 CSS 定位的知识。

## 使用`z-index`控制元素的堆叠顺序

属性让我们在页面的堆叠上下文中把元素堆叠在一起。如果某个元素的堆栈顺序较高，它将始终出现在堆栈顺序较低的元素之前:

```
.box-1 {
  z-index: 1;
}
.box-2 {
  z-index: 2;
}
.box-3 {
  z-index: 3;
}
.box-4 {
  z-index: 4;
}
```

![The Stacking Context Working Along the z-index](img/9c6b063a48e572c369c6ffe79f9f1319.png)

同样值得注意的是，`z-index`只对使用`position`属性定位的元素有效。如果两个元素有相同的`z-index`，那么最后出现在 HTML 标记中的元素会留在最上面。

既然我们已经介绍了我们的基础知识，让我们继续我们的起始项目。

## 使用 CSS 伪元素创建动画按钮

对于我们的第一个项目，我们将从创建一个简单的动画按钮开始，感受使用伪元素制作动画的感觉。然后，我们将转向更复杂的项目。

这是我们将要经历的事情的代码:

参见 [CodePen](https://codepen.io) 上大卫·赫伯特([@达维赫特](https://codepen.io/davehert) )
的笔 [动画按钮](https://codepen.io/davehert/pen/LYzdmBq)。

首先，我们将在 HTML 模型中创建一个锚标记，稍后我们将把它设计成一个按钮:

```
<a href="#" class="btn">Hover Me</a>
```

下面是我们的输出:
![Our HTML Button](img/352e6dbdea2e56a82b251100da027e67.png)

让我们跳到我们的 CSS 文件，将这个链接设计成一个典型的按钮:

```
.btn {
  text-decoration: none;
  border: 2px solid #764abc;
  color: #764abc;
  padding: 10px 20px;
  border-radius: 25px;
    transition: all 1s;
  position: relative;
}
```

代码应该是不言自明的。我们移除了默认的下划线装饰，添加了实心边框`2px`，并使按钮的颜色与文本相同。我们还添加了填充以在文本和边框之间留出一些空间，并添加了边框半径以弯曲按钮的边缘。

最后，我们为所有属性添加了一个持续时间为`1`秒的过渡。例如，对这个按钮的任何更改都应该在一秒钟内平滑地发生并动画化——并且将位置设置为`relative`,因为我们打算在这个按钮内放置一个伪元素。

还记得我说过要用绝对位置定位子元素，父容器必须设置为`relative`？这个按钮将是父容器。下面是我们的输出:![CSS Pseudo-Element Button Container](img/1ad4eefa14bfc238c8c92d9cc98b184b.png)

现在，我们将创建此按钮的伪元素:

```
.btn::before {
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: #764abc;
  transition: all 1s;
}
```

您必须为一个`:before`伪元素指定`content`属性。否则，不会显示。我们将`content`设置为一个空字符串来满足这个要求；我们不希望我们的伪元素有任何文本。我们将它的位置设置为`absolute`，这将它从正常流程中移除(使它与按钮重叠)，然后将`top`和`left`的坐标设置为`0`。这将空的伪元素固定到按钮的准确位置。

之后，我们将宽度和高度设置为父按钮的 100 %,使其与按钮本身大小相同。

最后，我们让伪元素的背景和按钮的颜色一样，并添加了一个`1`秒的过渡。下面是输出:
![The CSS Pseudo-Element Overlaps the Button](img/2c919026277b0caa12cb2888cac936d5.png)

如您所见，伪元素与按钮重叠，因为我们使用了一个`absolute`位置。

为了解决这个问题，我们必须使用`z-index`属性来改变它们的堆栈上下文，方法是使用负值将伪元素拖到按钮后面。然后，我们使用`translateX()`转换函数沿着水平轴定位伪元素。提供一个值`-100%`将伪元素定位在元素附近，在左侧完全齐平:

```
.btn::before {
  /*...previous code */
  z-index: -1;
  transform: translateX(-100%);
}
```

这里是:“t0”![Change the Stacking Context of the CSS Pseudo-Element Over the Button](img/d11516226640b517805742dc3a45670a.png)

Now, we will animate this pseudo-element so that it returns to its original position when we hover over the button, using the `:hover` pseudo-class:

```
.btn:hover::before {
  transform: translateX(0);
}
```

在上面的代码中，我们基本上是在说，当有人悬停在这个按钮上时，这个伪元素应该移回到它的初始绝对位置。这是我们的输出:

![CSS Pseudo-Element Moving Position on Hover](img/e1856ae381cd1ae8207a9f5515374f20.png)

但是，文本仍然是隐藏的，因为文本和伪元素都是相同的颜色。让我们将按钮悬停时文本的颜色改为白色显示:

```
.btn:hover {
  color: #fff;
}
```

![Text Changing to White on Hover](img/3408773544f2be13c9591235f810e4b8.png)

因为我们给按钮本身添加了一个`translate`属性，所以这种变化会顺利发生。

最后一步，我们将对按钮应用一个`overflow: hidden`属性来隐藏任何从容器中溢出的元素。当应用于按钮时，它将隐藏已翻译的伪元素，仅当它移回位置时才显示它:

```
.btn {
  /*...previous code. */
  overflow: hidden;
}
```

最后，我们将按钮放在页面的中心，让主体成为一个 [CSS 网格容器](https://blog.logrocket.com/css-grid-getting-started/)，并使用 [`place-items`](https://developer.mozilla.org/en-US/docs/Web/CSS/place-items) 属性:

```
body {
  /* other styles */
  display: grid;
  place-items: center;
} 

```

最终输出:
![Final CSS Animated Button](img/3769226cf6115007ec3a9978b037eef2.png)

你有它！我们已经成功地使用 CSS 伪元素创建了一个漂亮的动画按钮。

## 使用多个伪元素构建高级动画轮廓卡

现在，对于主事件，我们将使用多个伪元素(准确地说是四个)创建一个更复杂的动画个人资料卡，以创建一个真正令人惊叹的悬停效果。

这是我们将要经历的事情的代码:

参见 [CodePen](https://codepen.io) 上大卫·赫伯特([@达维赫特](https://codepen.io/davehert) )
的 Pen [动画简介卡](https://codepen.io/davehert/pen/dyVmjZZ)。

事不宜迟，让我们直接进入 HTML 标记:

```
<div class="profile-card">
      <div class="info">
        <h2>John Doe</h2>
        <p>Ceo / Founder</p>
      </div>
</div>
```

我们已经创建了一个简单的卡片`div`，它保存了用户的个人资料(包括姓名和职位):
![CSS Div Card Example](img/127844c102d18af8823d17bf46628900.png)

让我们跳到 CSS 文件并设计这张卡片的样式:

```
.profile-card {
  width: 300px;
  height: 400px;
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
  display: grid;
  place-items: center;
  position: relative;
  background: url("/image.jpg") no-repeat center center/cover;
}
```

我们创建了一个固定的`width/height`卡片，使用 CSS `grid`将里面的内容放在中心，并添加了一个`box-shadow`给边缘一点阴影，这样看起来更真实。最后，我们将卡片设置为`relative`，使其成为伪元素的父容器，并添加了居中的背景图像。让我们看看我们的输出:
![CSS Grid and Box-Shadow New Card](img/c3e9940dfef0053ed10ff3034fa2dd0e.png)

With that out of the way, let’s get on with creating the pseudo-elements.

这是棘手的部分。我们打算使用四个伪元素，但是一个元素只能分别有一个`::before`和一个`::after`伪元素。为了解决这个问题，我们将使用卡片本身来创建两个伪元素。然后，我们将在它里面放置另一个`div`和`info class`，我们将使用它来创建另外两个伪元素，它们包装内容。

让我们从`info div`开始:

```
.info::before {
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: #764abc;
  transform: skew(30deg) translateX(100%);
  opacity: 0.3;
  z-index: -1;
  transition: all 0.6s ease;
}
```

现在，因为`info div`本身没有固定的宽度或高度，伪元素取父卡的全部宽度和高度，使其与卡的大小相同。

然后我们使用`skew(30deg)`变换创建一个倾斜的外观，然后使用`translateX(100%)`变换将其移动到卡片的右边。负的`z-index`确保它留在文本后面，同时给`opacity`一个值`0.3`给它一个部分透明的外观:

![Pair of CSS Pseudo-Elements Adding a Purple Card](img/a9fad76a692dd9bca9c1541018deb19e.png)

Moving onto the second pseudo-element:

```
.info::after {
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: #764abc;
  transform: skew(-30deg) translate(90%);
  box-shadow: 0 0 20px rgba(0, 0, 0, 0.7);
  opacity: 0.3;
  z-index: -1;
  transition: all 0.6s ease;
}
```

在这里，我们做了与`::before`伪元素基本相同的事情，但是将`skew`方向切换到相反的方向。我们还添加了一个`box-shadow`，这样阴影就被添加到了侧面。这样看起来更 3D:
![Duplicate and Inverse the Box Shadow](img/8b06b8d8218311ebed7f47a16936016c.png)

Now, we’ll make it so that whenever this card is hovered over, the pseudo-elements move further into the card. You can rest assured this will happen smoothly because we added a `transition` to the pseudo-elements:

```
.profile-card:hover .info::before {
  transform: skew(30deg) translateX(50%);
}
.profile-card:hover .info::after {
  transform: skew(-30deg) translateX(40%);
  opacity: 0.7;
}
```

下面是我们的输出:
![CSS Pseudo-Element Animation](img/79859b9c3dc0b25bbe541e52c738909a.png)

Now, let’s create two more pseudo-elements using the card itself:

```
.profile-card::before {
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: #764abc;
  opacity: 0.3;
  transform: skew(30deg) translate(100%);
  transition: all 0.6s ease;
  z-index: -1;
}
```

至此，这段代码应该是不言自明的了。我们简单地做了与上面相同的事情，但是这一次我们只将卡片自己的`::before`伪元素向右平移了 85%。看一看:
![Add a New ::before Pseudo-Element and Move it 85 Percent to the Right](img/7e063aa92e1f4f9588777f7d64a634ab.png)

接下来，我们将创建最后一个伪元素，并从`::before`向相反的方向倾斜它:

```
.profile-card::after {
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: #764abc;
  opacity: 0.3;
  transform: skew(-30deg) translate(85%);
  transition: all 0.6s ease;
  z-index: -1;
}
```

![Added Four CSS Pseudo-Elements](img/03ecb2a239c483691bd501ecb21f84e1.png)

正如您可能已经猜到的，我们还将这样做，当这个 profile 卡被悬停时，这些新创建的伪元素会像前面两个一样移动。但这一次，我们将比前两次更进一步:

```
.profile-card:hover:before {
  transform: skew(30deg) translateX(30%);
}
.profile-card:hover:after {
  transform: skew(-30deg) translateX(20%);
}
```

下面是它的样子:
![CSS Pseudo-Elements Animated](img/8df71260e030e1d7d65de0dd78197294.png)

As you can see, our profile card is coming together. Now, for the last piece of the puzzle, we will set the `overflow` property of this card to `hidden` to hide the overflowing parts:

```
.profile-card {
 /*...previous code. */
  overflow: hidden;
}
```

下面是输出:
![Add the Overflow Property](img/c13adce9e9a10d6a504511662fd3cec1.png)

最后，我们将改变文字的颜色为白色，使其不透明度完全透明。然而，当卡片悬停在上方时，我们将把不透明度改回正常，使它们可见:

```
.info h2, .info p {
  color: #fff;
  opacity: 0;
  transition: all 0.6s;
}

.profile-card:hover .info h2,
.profile-card:hover .info p {
  opacity: 1;
}
```

查看最终结果:
![Change the Text to White](img/be7303ee65766390a882265a8c135d95.png)

结论

## 恭喜你坚持到最后。正如你所看到的，`::before`和`::after`伪元素可以以多种方式产生有趣的动画效果，赋予我们的设计以生命。

您可以进一步探索这些来创建更复杂的设计和动画，因为使用`::before`和`::after` CSS 伪元素可以做更多的事情，而我们只是触及了表面。

你的前端是否占用了用户的 CPU？

## 随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。

Modernize how you debug web and mobile apps — [Start monitoring for free](https://lp.logrocket.com/blg/css-signup).