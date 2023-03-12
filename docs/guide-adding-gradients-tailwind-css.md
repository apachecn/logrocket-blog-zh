# 使用顺风 CSS 添加渐变的指南

> 原文：<https://blog.logrocket.com/guide-adding-gradients-tailwind-css/>

你正在寻找一个关于如何用 Tailwind CSS 添加渐变的深度指南吗？也许你想为你网站的英雄部分创建一个漂亮的文本渐变，或者为你网站的卡片添加一个吸引人的边框渐变。

你很幸运！我最近一直在玩 Tailwind CSS 中的渐变，觉得写一个关于如何添加渐变的指南会很有趣。

在这篇博客中，我们将使用 Tailwind CSS 来创建漂亮的背景、文本、边框和下划线渐变。我们还将介绍如何在顺风 CSS 中制作渐变动画。我们开始吧！

## 顺风 CSS 渐变简介

渐变是给你的网站注入额外活力的好方法。但是它们不再仅仅是背景——我们可以给任何元素添加渐变，包括文本、边框、卡片、输入等等！

在深入到顺风 CSS 渐变和编码一些非常酷的东西之前，我们需要了解顺风 CSS 渐变的基础知识以及它们是如何工作的。

为了在顺风 CSS 中创建一个基本的渐变，我们需要使用这三个顺风 CSS 类:

1.  `bg-gradient-to-{direction}`
2.  `from-{color}`
3.  `to-{color}`

我们来讨论第一个。`bg-gradient-to-{direction}`定义渐变的方向。`direction`可以是水平的、垂直的或对角的。

水平线性梯度有两类:

1.  `bg-gradient-to-r`:定义从左到右的水平线性渐变(HLG)
2.  `bg-gradient-to-l`:从右向左定义一个 HLG

垂直线性渐变也有两类:

1.  `bg-gradient-to-t`:定义从底部到顶部的垂直线性渐变(VLG)
2.  `bg-gradient-to-b`:从上到下定义一个 VLG

最后，对角线渐变有四个等级:

1.  `bg-gradient-to-tr`:定义从左下方到右上方的对角线线性渐变(DLG)
2.  `bg-gradient-to-tl`:从右下到左上定义一个 DLG
3.  `bg-gradient-to-br`:从左上到右下定义一个 DLG
4.  `bg-gradient-to-bl`:从右上到左下定义一个 DLG

接下来，我们有`from-{color}`。这定义了渐变的开始颜色。我们可以使用任何一个 Tailwind CSS `color`类作为渐变的开始颜色，比如`from-purple-500`。

最后，我们还有`to-{color}`。这定义了渐变的结束颜色。我们可以使用任何顺风 CSS `color`类作为渐变的结束颜色，比如`to-purple-800`。

## 如何在顺风 CSS 中添加线性背景渐变

我们已经学习了基本的顺风 CSS 渐变类。现在，让我们使用这些知识来创建一个线性背景渐变。

首先，我们需要一个`div`容器。让我们首先创建它，并给它一些高度:

```
<div class="h-72"></div>

```

接下来我们需要做的是添加背景渐变。让我们通过指定`from-{color}`和`to-{color}` Tailwind CSS 类来添加一个简单的线性渐变。

在这个例子中，我们将创建一个从`from-purple-600`到`to-blue-600`的线性渐变。让我们继续添加这些类:

```
<div class="h-72 from-purple-600 to-blue-600"></div>

```

接下来，我们需要使用`bg-gradient-to-{direction}`类指定一个方向。这告诉 Tailwind CSS 它应该流向哪个方向。

在本例中，我们希望它从左向右流动，所以我们将添加`bg-gradient-to-r`类:

```
<div class="h-72 bg-gradient-to-r from-purple-600 to-blue-600"></div>

```

你应该在下面的演示中看到一个线性背景渐变:

参见 [CodePen](https://codepen.io) 上 Rishi Purwar([@ Rishi 111](https://codepen.io/rishi111))
的笔 [背景渐变](https://codepen.io/rishi111/pen/PoRWzgm)。

让我们试着把渐变的方向从左到右改为从上到下。要改变方向，我们需要用`bg-gradient-to-b`类替换`bg-gradient-to-r`类:

```
<div class="h-72 bg-gradient-to-b from-purple-600 to-blue-600"></div>

```

您可以看到渐变现在是从上到下流动，而不是从左到右:

看笔 [背景渐变:自上而下](https://codepen.io/rishi111/pen/wvmgrOa)由日石 pur war([@日石 111](https://codepen.io/rishi111) )
上[码笔](https://codepen.io)。

快速提示:如果我们想要创建一个渐变效果，我们不需要添加一个`to-{color}`类。我们只需要使用`bg-gradient-to-{direction}`和`from-{color}`类在顺风 CSS 中创建一个渐变效果。

## 自定义带有两个以上色标的顺风 CSS 渐变

在这一节中，我们将看看如何在 Tailwind CSS 中创建具有两个以上色标的渐变。这使得我们可以制作更加丰富多彩和充满活力的渐变。

到目前为止，我们只使用了两种色标:`from-{color}`和`to-{color}`。现在，在我们的渐变中添加第三个色标，我们将使用`via-{color}`类以及`from-{color}`和`to-{color}`类来添加中间色。

让我们用之前的例子来做这件事。您的代码应该如下所示:

```
<div class="h-72 bg-gradient-to-r from-purple-600 via-pink-600 to-blue-600"></div>

```

在本演示中，您可以看到使用三种色标的背景渐变:

见笔 [背景渐变:超过两个色停](https://codepen.io/rishi111/pen/NWYdwqr)由日石 pur war([@日石 111](https://codepen.io/rishi111) )
上[码笔](https://codepen.io)。

## 添加径向背景渐变

接下来，我们将学习如何在顺风 CSS 中添加径向背景渐变。正如你已经知道的，Tailwind CSS 不提供直接的类来创建径向渐变。我们需要做一些配置来增加对它的支持。我们开始吧！

我们需要做的第一件事是添加对径向渐变的支持。我们通过在我们的`tailwind.config.js`文件中添加以下代码来扩展主题属性:

```
module.exports = {
  content: ["./src/**/*.{js,jsx}"],
  theme: {
    extend: {
      backgroundImage: {
        'gradient-radial': 'radial-gradient(var(--tw-gradient-stops))',
      }
    }
  }
}

```

我们现在准备开始创建我们的第一个径向背景渐变！

首先，复制并粘贴上一节的最终代码:

```
<div class="h-72 bg-gradient-to-r from-purple-600 via-pink-600 to-blue-600"></div>

```

为了创建径向背景渐变，我们需要用`bg-gradient-radial`类替换`bg-gradient-to-r`来添加径向背景渐变:

```
<div class="h-72 bg-gradient-radial from-purple-600 via-pink-600 to-blue-600"></div>

```

在下图中，您可以看到带有三个色标的径向背景渐变:

![Background Gradient With Three Color Stops](img/30b27c1ee3f288076dfc98d6c1c6e214.png)

## 添加文本渐变

文本渐变是给文本添加额外活力的好方法。在这一节，我们将学习如何在 Tailwind CSS 中添加文本渐变。

首先，我们需要一个`h1`标签。让我们首先创建它，并给它一些基本的样式。

```
<h1 class="text-5xl p-4 text-center font-bold">A Guide to Adding Gradients with Tailwind CSS</h1>

```

接下来我们需要做的是给我们的文本添加背景渐变。让我们通过指定色标来添加一个简单的线性渐变。我们将使用上一个例子中使用的相同的色标，`from-purple-600 via-pink-600 to-blue-600`。

接下来，我们需要使用`bg-gradient-to-r`类指定渐变的方向。继续添加这些类。

```
<h1 class="text-5xl p-4 text-center font-bold bg-gradient-to-r from-purple-600 via-pink-600 to-blue-600">A Guide to Adding Gradients with Tailwind CSS</h1>

```

您应该会看到类似这样的内容:

![Text Gradient Example](img/e4dbcef2502ebf871843b91b48487249.png)

正如我们所看到的，背景渐变作为背景应用于我们的文本，而不是文本渐变。那么我们如何让它工作呢？

为了获得我们想要的效果，我们需要使用允许我们裁剪元素背景的`bg-clip-text` Tailwind CSS 类。在我们的例子中，我们想要裁剪`h1`标签的背景以匹配文本的形状。继续添加该类:

```
<h1 class="text-5xl p-4 text-center font-bold bg-gradient-to-r from-purple-600 via-pink-600 to-blue-600 bg-clip-text">A Guide to Adding Gradients with Tailwind CSS</h1>

```

![Text Gradient Example With White Background](img/28abcf57802f2bba23f1943d61da0901.png)

这里，我们裁剪了元素的背景，但是看不到文本渐变。这是因为黑色文本颜色与背景渐变重叠。为了隐藏它，我们需要使用`text-transparent`类来使文本的颜色透明。继续添加该类:

```
<h1 class="text-5xl p-4 text-center font-bold bg-gradient-to-r from-purple-600 via-pink-600 to-blue-600 bg-clip-text text-transparent">A Guide to Adding Gradients with Tailwind CSS</h1>

```

太好了！在这个演示中，您可以看到文本渐变的效果:

参见 [CodePen 上](https://codepen.io) [@rishi111](https://codepen.io/rishi111) )
的笔[Tailwind CSS 文本渐变](https://codepen.io/rishi111/pen/YzaNENm)。

## 如何在 Tailwind CSS 中添加边框渐变

在这一节中，我们将看看如何在 Tailwind CSS 中为按钮和 input 元素添加边框渐变。我们还将看到如何在卡片组件周围添加渐变边框。

### 按钮边框渐变

首先，让我们看看如何给按钮元素添加边框渐变。让我们创建一个具有基本顺风 CSS 样式的按钮:

```
<button class="m-4 rounded-full">
  <span class="block text-black px-4 py-2 font-semibold rounded-full">Follow Me</span>
</button>

```

接下来，使用我们在前面章节中看到的背景渐变类为按钮添加渐变背景:

```
<button class="m-4 rounded-full bg-gradient-to-r from-rose-400 via-fuchsia-500 to-indigo-500">
  <span class="block text-black px-4 py-2 font-semibold rounded-full">Follow Me</span>
</button>

```

该按钮应该如下所示:

![Follow Me Button](img/fa7920fa7ae269236098ef24666222e3.png)

现在，我们需要向 span 标签添加一个`bg-white`类来隐藏按钮渐变背景。我们还需要给按钮元素添加一些填充，在`span`标签周围创建一个边框。继续添加这些类:

```
<button class="m-4 p-1 rounded-full bg-gradient-to-r from-rose-400 via-fuchsia-500 to-indigo-500">
  <span class="block text-black px-4 py-2 font-semibold rounded-full bg-white">Follow Me</span>
</button>

```

在本演示中，您可以看到带有渐变边框的按钮:

在 [CodePen](https://codepen.io) 上看到 Rishi pur war([@ Rishi 111](https://codepen.io/rishi111))
的带边框渐变的 Pen [Tailwind CSS 按钮。](https://codepen.io/rishi111/pen/BarWaeW)

输入边框渐变

### 接下来，我们将看看如何在`input`标签周围添加渐变边框。这可能有点棘手。

首先，让我们创建一个具有基本样式的输入:

使用背景渐变类，让我们给`div`标签添加一个背景渐变:

```
<div class="m-4 rounded-full max-w-sm">
  <label for="name" class="sr-only">Name</label>
  <input class="p-3 w-full rounded-full focus:outline-none" type="text" id="name" placeholder="Enter Your Name">
</div>

```

您应该会看到类似这样的内容:

```
<div class="m-4 rounded-full max-w-sm bg-gradient-to-r from-rose-400 via-fuchsia-500 to-indigo-500">
  <label for="name" class="sr-only">Name</label>
  <input class="p-3 w-full rounded-full focus:outline-none" type="text" id="name" placeholder="Enter Your Name">
</div>

```

![Enter Name Input Tag](img/16e01588f30e3d92eced84637a7e2f9f.png)

现在，我们看不到任何渐变背景。这是因为`input`背景与`div`的背景渐变重叠。

为了让渐变边框可见，我们需要给`div`元素添加一些填充，在`input`标签周围创建一个边框。继续添加`p-1`类:

🤩神奇！我们现在可以在标签`input`周围看到一个渐变边框！

```
><div class="m-4 p-1 rounded-full max-w-sm bg-gradient-to-r from-rose-400 via-fuchsia-500 to-indigo-500">
  <label for="name" class="sr-only">Name</label>
  <input class="p-3 w-full rounded-full focus:outline-none" type="text" id="name" placeholder="Enter Your Name">
</div>

```

那么我们到底做了什么？

我们使用`p-1`类在`div`中添加了一些空间。这使得`div`的背景渐变只在`input`标签周围的空白区域可见。

您可以在下面的演示中看到带有渐变边框的输入元素:

参见 [CodePen](https://codepen.io) 上 Rishi pur war([@ Rishi 111](https://codepen.io/rishi111))
用边框渐变输入的笔[。](https://codepen.io/rishi111/pen/PoRpQmy)

卡片边框渐变

现在，让我们看看如何在卡片组件周围添加边框渐变。在卡片组件周围添加渐变边框与我们在上一节中看到的类似，所以让我们首先创建一个具有基本样式的卡片组件。只需将以下 HTML 复制并粘贴到您的 HTML 文件中:

### 这将创建一个具有基本 Tailwind CSS 样式的卡片组件，如下所示:

![Webdev Skills Plain Card Component](img/26fcb32ab1cbf2f1157edca7555b456c.png)

```
<article class="mx-auto my-4 max-w-sm rounded-xl">
  <div class="p-5 rounded-lg">
    <h4 class="text-2xl font-bold">Take your Web Dev skills to the next level!</h4>
    <a class="hover:underline text-gray-600" href="https://coding-space.vercel.app" target="_blank" title="codingspace">Visit CodingSpace↗</a>
  </a>
</article>

```

让我们通过给`article`标签添加渐变类来给这张卡片添加渐变背景。继续添加这些类:

现在，卡组件应该如下所示:

![Webdev Skills With Gradient Background Over Entire Text](img/65bcc22d9a5754a5d5182590cbcf4c03.png)

```
<article class="mx-auto my-4 max-w-sm rounded-xl bg-gradient-to-r from-rose-400 via-fuchsia-500 to-indigo-500">
  <div class="p-5 rounded-lg">
    <h4 class="text-2xl font-bold">Take your Web Dev skills to the next level!</h4>
    <a class="hover:underline text-gray-600" href="https://coding-space.vercel.app" target="_blank" title="codingspace">Visit CodingSpace↗</a>
  </a>
</article>

```

你能猜到下一步应该是什么吗？🤔

我们将使`div`的背景为白色，以隐藏其背后的渐变背景。我们还将使用`p-1.5`类向`article`标签添加填充，以在`div`元素周围创建一个边框。

让我们添加这些类，在卡片组件周围创建一个渐变边框:

现在，卡组件应该如下所示:

在 [CodePen](https://codepen.io) 上看到 Rishi pur war([@ Rishi 111](https://codepen.io/rishi111))
的带边框渐变的 Pen [Tailwind CSS 卡组件。](https://codepen.io/rishi111/pen/mdxmPQG)

```
<article class="p-1.5 mx-auto my-4 max-w-sm rounded-xl bg-gradient-to-r from-rose-400 via-fuchsia-500 to-indigo-500">
  <div class="bg-white p-5 rounded-lg">
    <h4 class="text-2xl font-bold">Take your Web Dev skills to the next level!</h4>
    <a class="hover:underline text-gray-600" href="https://coding-space.vercel.app" target="_blank" title="codingspace">Visit CodingSpace↗</a>
  </a>
</article>

```

添加渐变下划线

在这一节中，我们将学习如何在 Tailwind CSS 中给一些文本添加渐变下划线。首先，让我们创建一个带有基本样式的`h1`标签:

我们应该看到这样的内容:

## ![Gradient Underline Plain](img/1b4dc4b301d974c2e3abf44e37db33bb.png)

现在，让我们给`anchor`标签添加渐变背景。像这样添加顺风 CSS 背景渐变类:

```
<h1 class="text-3xl font-bold m-4">Level Up your Web Dev Skills, visit <a href="https://coding-space.vercel.app" target="_blank">CodingSpace</a></h1>

```

您应该会看到类似这样的内容:

![Gradient Underline Background On One Word](img/7f514cf6e6a867c5c4b9dfa6c1f181c2.png)

让我们给背景渐变宽度和高度。我们将使用类似于`bg-[length:100%_6px]`的任意值`background-size`来给出`100%`的宽度和`6px`的高度。如果你不熟悉任意值，你可以参考 Tailwind CSS [文档](https://tailwindcss.com/docs/adding-custom-styles#using-arbitrary-values)来了解更多。

```
<h1 class="text-3xl font-bold m-4">Level Up your Web Dev Skills, visit <a href="https://coding-space.vercel.app" target="_blank" class="bg-gradient-to-r from-rose-400 via-fuchsia-500 to-indigo-500">CodingSpace</a></h1>

```

我们还需要添加`bg-no-repeat`类来防止渐变背景重复:

现在，您应该看到类似这样的内容:

![Gradient Background With Underline On Top](img/cf01a01d548ce6ab142e1aae52ac7a36.png)

我们需要做的最后一件事是使用`bg-bottom`类将背景的位置改为底部:

```
<h1 class="text-3xl font-bold m-4">Level Up your Web Dev Skills, visit <a href="https://coding-space.vercel.app" target="_blank" class="bg-gradient-to-r from-rose-400 via-fuchsia-500 to-indigo-500 bg-[length:100%_6px] bg-no-repeat">CodingSpace</a></h1>

```

你现在可以看到文本下面的渐变下划线！

参见 [CodePen](https://codepen.io) 上 Rishi pur war([@ Rishi 111](https://codepen.io/rishi111))
的笔[Tailwind CSS 下划线渐变](https://codepen.io/rishi111/pen/jOzmXgv)。

The last thing we need to do is change the position of this background to the bottom using the `bg-bottom` class:

```
<h1 class="text-3xl font-bold m-4">Level Up your Web Dev Skills, visit <a href="https://coding-space.vercel.app" target="_blank" class="bg-gradient-to-r from-rose-400 via-fuchsia-500 to-indigo-500 bg-[length:100%_6px] bg-no-repeat bg-bottom">CodingSpace</a></h1>

```

如何在顺风 CSS 中制作渐变动画

在 TailwindCSS 中制作动画可以给你的项目增加有趣和有意义的互动！在这一节中，我们将学习如何在悬停和聚焦状态下用 Tailwind CSS 制作渐变动画来创建漂亮的动画效果。本质上，我们将为本文中已经创建的组件制作动画。

按钮渐变背景动画

## 首先，我们将看到如何制作已经构建好的按钮组件的背景动画。复制并粘贴按钮边框渐变部分的最终代码:

要在悬停状态下添加渐变背景，我们需要使用 Tailwind CSS `hover:`伪类。在悬停状态下，我们将使用`bg-transparent`类使`span`元素的背景透明。我们还需要添加`transition-colors`类来使过渡平滑。去上那些课吧:

### 现在我们已经添加了所有这些类，你可以在这个演示中看到背景渐变动画:

参见 [CodePen](https://codepen.io) 上 Rishi Purwar([@ Rishi 111](https://codepen.io/rishi111))
的笔 [动画顺风 CSS 渐变背景按钮](https://codepen.io/rishi111/pen/JjLJEOp)。

```
<button class="m-4 p-1 rounded-full bg-gradient-to-r from-rose-400 via-fuchsia-500 to-indigo-500">
  <span class="block text-black px-4 py-2 font-semibold rounded-full bg-white">Follow Me</span>
</button>

```

To add a gradient background on hover state, we need to use the Tailwind CSS `hover:` pseudo-class. On hover state, we’ll make the background of the `span` element transparent using the `bg-transparent` class. We also need to add the `transition-colors` class to make the transition smooth. Go ahead and those classes:

```
<button class="m-4 p-1 rounded-full from-rose-400 via-fuchsia-500 to-indigo-500 bg-gradient-to-r">
  <span class="block text-black px-4 py-2 font-semibold rounded-full bg-white hover:bg-transparent hover:text-white transition">Follow Me</span>
</button>

```

输入边框渐变动画

我们现在来看看如何动画显示输入组件的边框。复制并粘贴输入边框渐变部分的最终代码:

我们将在悬停和焦点状态的输入周围添加渐变边框，当输入处于正常状态时，我们将使用纯色创建默认边框。

### 让我们使用`hover:`和`focus-within:`类，如果你不知道`focus-within`伪类，我先简单解释一下。

当元素(或其子元素之一)处于焦点状态时，我们使用`focus-within`伪类来设置元素的样式。在我们的例子中，当`div`有一个悬停状态或者我们的输入(子)有一个悬停或焦点状态时，我们想要添加一个渐变边框。让我们添加`hover:`和`focus-within:`伪类:

```
<div class="m-4 p-1 rounded-full max-w-sm bg-gradient-to-r from-rose-400 via-fuchsia-500 to-indigo-500">
  <label for="name" class="sr-only">Name</label>
  <input class="p-3 w-full rounded-full focus:outline-none" type="text" id="name" placeholder="Enter Your Name">
</div>

```

我们现在可以看到添加了`hover:`和`focus-within:`的边框渐变动画了！

现在，让我们给这个输入添加一个普通的边框。为此，将这些类`border border-fuchsia-500`添加到`input`元素中:

您应该会看到类似这样的内容:

```
<div class="m-4 p-1 rounded-full max-w-sm hover:bg-gradient-to-r focus-within:bg-gradient-to-r from-rose-400 via-fuchsia-500 to-indigo-500">
  <label for="name" class="sr-only">Name</label>
  <input class="p-3 w-full rounded-full focus:outline-none" type="text" id="name" placeholder="Enter Your Name">
</div>

```

![Enter Name Button Two](img/dcdc26741fa3cad31510c27dec34bed2.png)

哦，有一个问题。即使当`input`或`div`处于悬停或聚焦状态时，该默认边框也保持不变。为了隐藏它，我们需要添加一个`border-transparent`类，使它在悬停或聚焦状态下不可见。继续添加突出显示的类:

```
<div class="m-4 p-1 rounded-full max-w-sm hover:bg-gradient-to-r focus-within:bg-gradient-to-r focus from-rose-400 via-fuchsia-500 to-indigo-500">
  <label for="name" class="sr-only">Name</label>
  <input class="p-3 w-full rounded-full border border-fuchsia-500 focus:outline-none" type="text" id="name" placeholder="Enter Your Name">
</div>

```

您可以在本演示中看到最终的边框渐变动画:

在 [CodePen](https://codepen.io) 上看到 Rishi pur war([@ Rishi 111](https://codepen.io/rishi111))
用动画边框渐变输入的 Pen [。](https://codepen.io/rishi111/pen/abYwKze)

渐变下划线动画

```
<div class="m-4 p-1 rounded-full max-w-sm hover:bg-gradient-to-r focus-within:bg-gradient-to-r focus from-rose-400 via-fuchsia-500 to-indigo-500">
  <label for="name" class="sr-only">Name</label>
  <input class="p-3 w-full rounded-full border border-fuchsia-500 focus:outline-none focus:border-transparent hover:border-transparent" type="text" id="name" placeholder="Enter Your Name">
</div>

```

在这一节中，我们将看到如何在 Tailwind CSS 中添加渐变下划线动画。首先，让我们快速复制并粘贴渐变下划线部分的最终代码:

我们将动画下划线，使其高度从底部到顶部增加。当我们悬停在文本上时，它会覆盖文本的高度。

这里的关键是我们需要改变悬停时的下划线渐变高度。我们现在有一个固定的高度`6px`，为了制作动画，需要在悬停状态下将它的高度改为`100%`。

### 为了获得这种效果，让我们将`bg-[length:100%_100%]`类添加到悬停状态的`anchor`标签中。我们还需要添加`transition-[background-size]`类来使过渡平稳。继续添加这些类:

你可以在下面的演示中看到最终的下划线渐变动画。

```
<h1 class="text-3xl font-bold m-4">Level Up your Web Dev Skills, visit <a href="https://coding-space.vercel.app" target="_blank" class="bg-gradient-to-r from-rose-400 via-fuchsia-500 to-indigo-500 bg-no-repeat bg-bottom bg-[length:100%_6px]">CodingSpace</a></h1>

```

参见 [CodePen](https://codepen.io) 上 Rishi Purwar([@ Rishi 111](https://codepen.io/rishi111))
的笔 [Tailwind CSS 动画下划线渐变](https://codepen.io/rishi111/pen/eYMRgLR)。

The key here is that we need to change an underline gradient height on hover. We now have a fixed height of `6px`, and, to animate it, need to change its height to `100%` on the hover state.

至此，本教程就完成了！我希望你已经学到了很多关于如何使用 Tailwind CSS 来创建各种渐变组合的知识。

```
<h1 class="text-3xl font-bold m-4">Level Up your Web Dev Skills, visit <a href="https://coding-space.vercel.app" target="_blank" class="bg-gradient-to-r from-rose-400 via-fuchsia-500 to-indigo-500 bg-no-repeat bg-bottom bg-[length:100%_6px] hover:bg-[length:100%_100%] transition-[background-size]">CodingSpace</a></h1>

```

结论

总之，Tailwind CSS 使得在你的网站上添加漂亮的渐变变得很容易，并且是你的开发者应该拥有的一个很好的工具！

我希望你喜欢这篇关于如何用 Tailwind CSS 添加渐变的教程。但是为什么就此打住呢？我鼓励你在 Tailwind CSS 中尝试渐变，创建你自己的渐变自定义片段，并在下面的评论中与我们分享！

一如既往，如果你有任何问题或意见，请在下面留下。我会尽快回复你。感谢阅读！🙏

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

.

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

## 现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。

As web frontends get increasingly complex, resource-greedy features demand more and more from the browser. If you’re interested in monitoring and tracking client-side CPU usage, memory usage, and more for all of your users in production,

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

[LogRocket](https://lp.logrocket.com/blg/css-signup) is like a DVR for web and mobile apps, recording everything that happens in your web app or site. Instead of guessing why problems happen, you can aggregate and report on key frontend performance metrics, replay user sessions along with application state, log network requests, and automatically surface all errors.

Modernize how you debug web and mobile apps — [Start monitoring for free](https://lp.logrocket.com/blg/css-signup).