# SVG z-index 的动画技巧

> 原文：<https://blog.logrocket.com/animation-tricks-svg-z-index/>

作为一种娱乐媒体，网络需要明智地使用特殊效果来抓住和保持人们的兴趣。然而，与其他媒体格式相比，网络受到带宽的限制。如果我们能找到用更少的数据传输产生有趣效果的方法，我们就都赢了。

在本文中，我将讨论用一些简单的 SVG 技巧做更多事情的技术。为了简洁起见，我假设读者对 SVG 有初步的了解，所以我不会解释更基本的概念。

同时，为了让代码更容易理解，我将避免各种优化，这样我就不必解释它们了。这意味着代码有时会比需要的更冗长，但是要认识到冗长是为了清晰起见。

## SVG 中的 z 索引

以下 SVG 代码片段代表一个圆:

```
<circle  cx="50" cy="50" r="25" fill="#ff0000" />
```

这些也是:

```
<circle  cx="50" cy="50" r="25" fill="#ffff00" />
<circle  cx="50" cy="50" r="25" fill="#ffffff" />
```

如果 SVG 中有前两个圆圈，您将看到一个黄色的圆圈。如果这三个都有，应该什么也看不到——假设它们所在的 SVG 是白色的。

请看 [CodePen](https://codepen.io) 上布莱恩·拉斯姆森([@布莱恩·拉斯姆森](https://codepen.io/bryanrasmussen) )
的笔 [层叠的圆圈](https://codepen.io/bryanrasmussen/pen/BajNOYK)。

其原因是 SVG 的 z 索引规则，该规则规定，当任意两个图形元素占据相同的空间时，在描述 SVG 的 XML 树中稍后表示的元素将被赋予更高的`z-index`。白色圆圈与黄色圆圈位置相同，并精确地覆盖了它。

每个圆上的`r`属性代表其半径。因此，如果您想要分层的圆，以便可以看到下面的圆，您可以通过增加半径来实现，如下所示:

参见布莱恩·拉斯姆森([@布莱恩·拉斯姆森](https://codepen.io/bryanrasmussen) )
在 [CodePen](https://codepen.io) 上看到的笔[。](https://codepen.io/bryanrasmussen/pen/qBbdMJv)

如果你没有白色的圆圈，它看起来就像你有一个黄色的圆圈，有一个红色的粗线条。同样的原理也适用于 SVG 中的其他图形元素。

假设我们把下面的矩形放在白色和黄色的圆圈之间。

```
<rect x="25" y="25" width="50" height="50" fill="#ff00ff" />
```

注意`x`和`y`是圆的`cx`和`cy`的一半——因为圆是从中心向外画的，而矩形是从左上角画的。它看起来会像这样:

请看 [CodePen](https://codepen.io) 上布莱恩·拉斯姆森([@布莱恩·拉斯姆森](https://codepen.io/bryanrasmussen) )
的笔[分层的圆圈和矩形可见](https://codepen.io/bryanrasmussen/pen/yLeNxWK)。

最后，我将在之前的矩形和白色圆圈之间放入另一个矩形，并旋转它以制作一个简单的星形:

```
<rect x="25" y="25" width="50" height="50" fill="#ff00ff" transform = "rotate(45 50 50)"/>
```

`50`值代表矩形的中心，当然，它与我们的圆的`cx`和`cy`值相同。

参见 [CodePen](https://codepen.io) 上布莱恩·拉斯姆森([@布莱恩·拉斯姆森](https://codepen.io/bryanrasmussen) )
的钢笔 [分层的圆和矩形 2](https://codepen.io/bryanrasmussen/pen/LYGVJww) 。

>

我认为在这一点上，我们有足够的小区域来玩，我们可以看到用动画制作一些有趣的效果。

## `animateTransform`

在 SVG 中制作动画的最简单的方法之一是使用`animateTransform`，它在目标元素上制作一个转换属性的动画。换句话说，如果我将一个`animateTransform`元素作为其中一个矩形的子元素，我可以将 transform 属性动画化，我们在前面的例子中使用它来旋转最后一个`rect`。

如果我们添加下面的`animateTransform`作为最后一个矩形的子元素…

```
<animateTransform
    attributeName="transform"
    type="rotate"
    from="0 50 50"
    to="360 50 50"
    dur="10s"
    repeatCount="indefinite" />
```

…我们将使该矩形每 10 秒钟无限旋转 360 度。

`from`属性的第一个数字来自于它在动画开始时应该旋转多少度；`to`的第一个数字是动画结束时它应该旋转多少度。如果我们这样做，我们可以删除矩形上的原始 transform 属性，因为它是冗余呈现的。像这样:

请看 [CodePen](https://codepen.io) 上布莱恩·拉斯姆森([@布莱恩·拉斯姆森](https://codepen.io/bryanrasmussen) )
制作的钢笔[《分层的圆和矩形 2》动画](https://codepen.io/bryanrasmussen/pen/eYJNPZQ)。

`animate`

## `animate`允许你以相同的方式，为没有变换属性复杂的属性制作动画。

作为一个例子，让我们添加下面的`animate`元素作为具有`animateTransform`的矩形的子元素:

此处可见:

```
  <animate 
    attributeName="fill" 
    values="#ff0000; #ffff00; #ff00ff;" 
    dur="10s" 
    repeatCount="indefinite"
  />
```

请看 [CodePen](https://codepen.io) 上布莱恩·拉斯姆森([@布莱恩·拉斯姆森](https://codepen.io/bryanrasmussen) )
的钢笔[《分层的圆和矩形 2》动画 2](https://codepen.io/bryanrasmussen/pen/BajNqMx) 。

See the Pen [Layered Circles And Rects 2 Animated 2](https://codepen.io/bryanrasmussen/pen/BajNqMx) by Bryan Rasmussen ([@bryanrasmussen](https://codepen.io/bryanrasmussen))
on [CodePen](https://codepen.io).

作为动画的其他属性(如持续时间和重复次数)如何创建有趣效果的一个示例，看看这个圆形分层。

每个圆都有不同的填充动画持续时间，重复的次数被设置为在不同的时间停止，除了 XML 树中的最后一个内圆，它的填充动画应该永远保持不变。

请看 [CodePen](https://codepen.io) 上布莱恩·拉斯姆森([@布莱恩·拉斯姆森](https://codepen.io/bryanrasmussen) )
的钢笔 [分层圆圈动画填充序列](https://codepen.io/bryanrasmussen/pen/xxZGQbE)。

See the Pen [Layered Circles animate fills in sequence](https://codepen.io/bryanrasmussen/pen/xxZGQbE) by Bryan Rasmussen ([@bryanrasmussen](https://codepen.io/bryanrasmussen))
on [CodePen](https://codepen.io).

至于如何制作其他 SVG 属性的动画，下面的例子制作了一些分层圆的`cx`属性的动画。每一层的持续时间相差一点，开始时间是这样的，底层先开始，顶层最后开始，它们都一个接一个地完成。

动画上的`fill`属性被设置为`freeze`，这意味着该属性将以结束动画的值结束(在本例中为`250`)。

更多来自 LogRocket 的精彩文章:

* * *

### 请看 [CodePen](https://codepen.io) 上布莱恩·拉斯姆森([@布莱恩·拉斯姆森](https://codepen.io/bryanrasmussen) )
的钢笔 [分层圆圈依次动画 CX](https://codepen.io/bryanrasmussen/pen/NWxqEpm)。

* * *

See the Pen [Layered Circles animated cx in sequence](https://codepen.io/bryanrasmussen/pen/NWxqEpm) by Bryan Rasmussen ([@bryanrasmussen](https://codepen.io/bryanrasmussen))
on [CodePen](https://codepen.io).

关于这些动画需要注意的最后一点是，透明是一种颜色——这个事实将在下一节中用到。

分层和定时动画

## 请看下面的动画:

在 [CodePen](https://codepen.io) 上查看布莱恩·拉斯姆森([@布莱恩·拉斯姆森](https://codepen.io/bryanrasmussen) )
)的钢笔 [分层圆圈动画 cx 与停止点和图像](https://codepen.io/bryanrasmussen/pen/jOWWGRx)。

See the Pen [Layered Circles animated cx with stop points and images](https://codepen.io/bryanrasmussen/pen/jOWWGRx) by Bryan Rasmussen ([@bryanrasmussen](https://codepen.io/bryanrasmussen))
on [CodePen](https://codepen.io).

这里发生的是在 SVG 中有 18 个圆。十二个圆成对地沿着 SVG 的长度放置——也就是说，在 x 半径上的`cx="100"`处有两个圆，在`cx="200"`处有两个圆，以此类推。下面是第一对圆的代码:

因为这两个圆位于相同的位置，所以标记树中后面的圆具有较高的 z 索引。因此，填充颜色为白色的圆位于由图像填充的圆之上。

```
  <circle  cx="100" cy="50" r="30" fill="url(#image1)"></circle>
  <circle  cx="100" cy="50" r="30" fill="#ffffff">
      <animate 
      attributeName="fill" 
      values="#ffffff;transparent; " 
      begin="6.1s"
      dur="0.1s"
      fill="freeze"
      repeatCount="1" 
  />
  </circle>
```

但是这个白色的圆圈有一个使它透明的动画，这意味着当动画完成时，你可以看到下面圆圈中的图像。

最后，有六个圆圈从相同的位置开始——`cx="50"`、`cy="50"`——并沿着它们的`cx`设置动画。这些圆圈中的每一个都停在某个地方，覆盖其中一个白色圆圈，而白色圆圈又覆盖有图像的圆圈。

六个圆圈移动到各自的`cx`位置后，白色圆圈动画开始。然后，六个圆的填充上有一个动画，从黄色或红色变为透明。当然，当他们完成动画，白色的圆圈已经完成动画；因此，在顶部图像的填充颜色变为透明后，我们会看到带有图像的圆圈。

结论

## 总之，有了 SVG 的 z 索引和适当定时的动画，通过简单的标记模式构造吸引用户注意力的高级效果是非常简单的。

使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

## [LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)

Focus on the bugs that matter — [try LogRocket today](https://lp.logrocket.com/blg/signup-issue-free).