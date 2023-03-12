# 用 CSS Flexbox 伸缩

> 原文：<https://blog.logrocket.com/flexing-with-css-flexbox-b7940b329a8a/>

![](img/5fc1536c0a04103d6eb502c497b3550b.png)

Flexbox 也称为 flexible box，是 CSS 中的一种布局模型，使设计响应性布局变得非常容易。

Flexbox 布局模型背后的整个想法是允许元素在任何方向上布局，调整它们的大小以填充未使用的空间，或者收缩以避免溢出它们的父元素，无论是水平还是垂直。

为了真正能够使用 Flexbox 进行伸缩，我们必须了解它是如何工作的。

让我们把它分解成两个性质，即:

1.  柔性容器
2.  弹性项目

### 柔性容器

这是父 html 元素，包含您想要布局的项目。

要使用任何 flex 属性，必须创建该容器。

它创建了允许所有其他 flex 属性工作的上下文。

参见 [CodePen](https://codepen.io) 上 Obaseki Nosa([@ c0de panda](https://codepen.io/c0depanda))
的 Pen [Flex 1](https://codepen.io/c0depanda/pen/NMJEMy/) 。

对齐-项目

Creating a Flex container

#### 这允许您垂直对齐 flex 容器中的项目，而不管项目相对于其 flex 容器或彼此的高度。

它接受的值有:*|**|*伸缩结束*** | ***中心*|**|**|*基线*|**|**|*拉伸****

 *参见 [CodePen](https://codepen.io) 上 Obaseki Nosa([@ c0de panda](https://codepen.io/c0depanda))
的 Pen [Flex–align-items](https://codepen.io/c0depanda/pen/NMJeXJ/)。

调整内容

Example showing all the different value the **align-items** property takes

#### 这与 **align-items 相反，**它水平对齐其项目，而不考虑项目相对于其容器或彼此的宽度。

取值:***flex-start***|**|*flex-end*|**|**|*center*|**|**|*space-between*|**|*space-around*|**|**|*space-equally*****

见[笔
自圆其说内容](https://codepen.io/c0depanda/pen/MXJdvq/)由 Obaseki Nosa([@ c0de panda](https://codepen.io/c0depanda))
于 [CodePen](https://codepen.io) 。

柔性包装

Showcasing Justify-content values

#### `flex-wrap`属性指定伸缩项是否应该换到下一行。

默认情况下，所有的 flex 项都试图放在一行中，但是这个属性告诉浏览器当它们变得太多而不能放在一行中时，将它们分成另一行。

我们所说的这条线也被称为 F **lex 线**。

值:***nowrap***|**|*wrap***|**|*wrap-reverse***

参见 [CodePen](https://codepen.io) 上 Obaseki Nosa([@ c0de panda](https://codepen.io/c0depanda))
的笔 [Flex Wrap](https://codepen.io/c0depanda/pen/LrxopK/) 。

对齐内容

Flex-wrap with it’s values

#### 该属性修改了**柔性包装**属性的行为。

它的行为本质上类似于 **align-items** 属性，只是它对齐的是伸缩线而不是伸缩项。

要使该属性起作用，必须在 flex 容器上设置`flex-wrap: wrap`，并且 flex 行必须多于一条。

取值:***flex-start***|**|*flex-end*|**|**|*center*|**|**|*space-between*|**|*space-around*|**|**|*stretch*****

参见 [CodePen](https://codepen.io) 上 Obaseki Nosa([@ c0de panda](https://codepen.io/c0depanda))
的笔 [align-content](https://codepen.io/c0depanda/pen/xzgNGB/) 。

弯曲方向

Align content with all it’s accepted values

#### 这定义了浏览器应该在哪个方向堆叠伸缩项，即垂直还是水平。

取值: ***行***|**|*行-反转***|**|*列*|**|*列-反转*****

参见 [CodePen](https://codepen.io) 上 Obaseki Nosa([@ c0de panda](https://codepen.io/c0depanda))
的笔 [](https://codepen.io/c0depanda/pen/wXgbrq/) 。

柔性流

Flex-direction example showcasing the various values

#### 这是`flex-direction`和`flex-wrap`的简写，所以不用写下面的；

你可以这样做。

```
.container {
   display: flex;
   flex-direction: column;
   flex-wrap: wrap;
}
```

更多来自 LogRocket 的精彩文章:

* * *

### 弹性项目

* * *

```
.container {
   display: flex;
   flex-flow: column wrap;
}
```

### 这是 Flex 容器的直接子容器，就像 Flex 容器一样，它们也有可以应用的属性。

命令

#### 此属性允许您更改弹性项目的排列顺序。即根据 HTML 结构源代码。

同一容器中的 Flex 项目根据 **order** 值以升序排列，而具有相同 order 值的 flex 项目按照它们在源代码中出现的顺序排列。

数值: ***<整数>(正或负)***

参见 [CodePen](https://codepen.io) 上的[笔
订单](https://codepen.io/c0depanda/pen/XYMdow/)由 Obaseki Nosa([@ c0de panda](https://codepen.io/c0depanda))
。

自我对齐

Example showing how order for flex items work

#### 顾名思义，属性允许您在 flex 容器内垂直对齐选定的 flex 项。

它的工作方式与`align-items`基本相同，只是它是针对单个 Flex 项目的。

当您只想对齐单个 Flex 项目而不是 Flex 容器中的所有 Flex 项目时，这很有用。

**注意:**这个属性覆盖了 flex 容器的`align-items`属性。

值: ***自动|伸缩-开始|伸缩-结束|中心|基线|拉伸***

参见 [CodePen](https://codepen.io) 上 Obaseki Nosa([@ c0de panda](https://codepen.io/c0depanda))
的笔 [align-self](https://codepen.io/c0depanda/pen/pKeERb/) 。

弹性基础

Align-self Flex Property

#### 此属性设置选定伸缩项的初始大小。这与房地产的运作方式非常相似。

`flex-basis`属性接受以[长度单位](https://www.w3schools.com/cssref/css_units.asp)表示的值，即 px、em、rem、百分比等。它还接受`auto`,这基本上决定了如何根据是否设置了`flex-grow`或者根据伸缩项的内容大小来分配伸缩项。

参见 [CodePen](https://codepen.io) 上 Obaseki Nosa([@ c0de panda](https://codepen.io/c0depanda))
的 Pen [flex-basis](https://codepen.io/c0depanda/pen/LrWRJX/) 。

灵活增长

Flex basis showing auto, 200px & 40%

#### 这个属性赋予 flex 项目神奇的力量，使它们可以增长到填满一个 flex 容器，或者比容器中的其他元素占用更多的空间。

它基本上指定了项目应该在 flex 容器中占据多少空间。

数值: ***<整数>(正)***

参见 [CodePen](https://codepen.io) 上 Obaseki Nosa([@ c0de panda](https://codepen.io/c0depanda))
的笔 [flex-grow](https://codepen.io/c0depanda/pen/qKrRbN/) 。

弯曲收缩

Flex-grow with its values

#### 这与`flex-grow`相反，它基本上是让伸缩项在必要时收缩。即 flex 容器中的其他项目试图占用更多空间。

数值: ***<整数>(正)***

参见 [CodePen](https://codepen.io) 上 Obaseki Nosa([@ c0de panda](https://codepen.io/c0depanda))
的笔 [伸缩](https://codepen.io/c0depanda/pen/zaZoZy/)。

根据上面的例子，有 5 个设置了`flex-basis: 200px`的 Flex 项，总共是`1000px`。

Example showing Flex-shrink

另一方面，flex 容器已经被设置为比 Flex 项目小的`400px`，但是因为`flex-wrap`默认为`nowrap`，我们将让所有的项目尝试适应 Flex 容器，从而平均分配它们自己。

一旦在这些项目上设置了`flex-shrink`值，它们将开始根据这些值进行调整。

与其他弹性项目相比，`flex-shrink`值越高，收缩越多。

弯曲

#### 这是`flex-grow`、`flex-shrink`和`flex-basis`的简写属性，但是`flex-shrink`和`flex-basis`是可选的，也就是说，你可以只设置`flex-grow`，其余的将被设置为默认值。

默认情况下，这些值被设置为`flex: 0 1 auto`，即`flex-grow: 0`、`flex-shrink: 1`和`flex-basis: auto`。

觉得这篇文章有帮助？别忘了鼓掌和分享🙂

你的前端是否占用了用户的 CPU？

## 随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用情况、内存使用情况等感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。

Modernize how you debug web and mobile apps — [Start monitoring for free](https://lp.logrocket.com/blg/css-signup).

* * **