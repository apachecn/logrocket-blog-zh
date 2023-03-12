# 用 SVG 创建更酷的加载动画

> 原文：<https://blog.logrocket.com/cooler-loading-animations-svg/>

几年前，我为丹麦一家大型电信公司的客户开发了一个工具，用于查找运营问题(你可以在这里看到它，但它是丹麦语的，所以你可能不关心)。我认为这项服务比大多数项目更胜一筹的一点是设计，特别是当用户在特定位置搜索问题时显示的自定义加载动画。

这个定制动画有点像[你看到的快速行驶的修理车](https://assets.codepen.io/1281712/YouSeePreloader.gif)去解决客户的问题。它最初是由 [Ronnie S. Jakobsen](https://www.linkedin.com/in/ronniesjakobsen/) 设计的，作为一个概念验证占位符，它被证明是如此受欢迎，以至于它被保留了下来。

随着 SVG 变得越来越普遍，我希望看到更多个性化的加载动画，在这篇文章中，我将分享一些技巧，告诉你如何将几乎任何 SVG 图标改编成加载动画。

## 寻找要使用的图标

所以，首先，我们应该有一个图标来玩。让我们以名词项目中的[这个为起点。](https://thenounproject.com/term/user/961/)

请看 [CodePen](https://codepen.io) 上布莱恩·拉斯姆森([@布莱恩·拉斯姆森](https://codepen.io/bryanrasmussen) )
的名词项目中的笔 [用户图标。](https://codepen.io/bryanrasmussen/pen/JjGwRJR)

我对此做了一些小改动，去掉了不需要的标记，并给图标的头部和主体部分添加了描述性的标识。

这个图标有几个地方我不喜欢:

1.  如果您查看标记，您会看到用户的头部由一个路径而不是一个圆形元素来表示——具体来说，这个路径:

    ```
    <path id="head" fill="#000000" d="M49.732-0.239L49.732-0.239h-0.001C38.062-0.193,28.649,9.407,28.725,21.194 c-0.076,11.904,9.337,21.501,21.006,21.441h0h0.001c11.668,0.061,21.082-9.537,21.006-21.441C70.814,9.407,61.4-0.193,49.732-0.239z"></path>
    ```

2.  如果你放大一点，你应该会看到顶部也被稍微切掉了。

因此，我对标记做了一些调整，这当然使它看起来略有不同，但我认为这些差异对于普通用户来说并不明显。

参见名词项目中的笔 [用户图标，由布莱恩·拉斯姆森(](https://codepen.io/bryanrasmussen/pen/qBbLaXq)[@布莱恩·拉斯姆森](https://codepen.io/bryanrasmussen) )
在 [CodePen](https://codepen.io) 上改进的标记。

制作加载动画

## 加载动画最常见的形式是无限重复，由页面中的某个事件打开和关闭，通常由 JavaScript 控制。

如果我们想把用户图标变成一个加载动画，最简单的方法就是使用一个 [SVG `<feFlood>`过滤器](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/feFlood)，让动画无限运行，如下所示。

参见 [CodePen](https://codepen.io) 上布莱恩·拉斯姆森([@布莱恩·拉斯姆森](https://codepen.io/bryanrasmussen) )
过滤 1 的笔[用户图标。](https://codepen.io/bryanrasmussen/pen/VweqPrY)

See the Pen [User Icon filtered 1](https://codepen.io/bryanrasmussen/pen/VweqPrY) by Bryan Rasmussen ([@bryanrasmussen](https://codepen.io/bryanrasmussen))
on [CodePen](https://codepen.io).

静态图标的相关变化是添加了一个过滤器:

以及将该过滤器应用于组成图标的图形元素组:

```
     <defs>
              <filter id="iconfilter" primitiveUnits="objectBoundingBox" >
                    <feFlood flood-color="green"/>

             <feOffset>
                      <animate attributeName="dy" from="1" to="0" dur="4s" repeatCount="indefinite" />
             </feOffset>

              <feComposite operator="in" in2="SourceGraphic" />
              <feComposite operator="over" in2="SourceGraphic" />
        </filter>
      </defs>
```

动画是如何工作的

```
     <g id="userIcon" filter="url(#iconfilter)">
```

### 我想我应该描述一下这个过滤器是如何工作的。如果过滤器中只有`<feFlood>`元素，那么基本上只有一个绿色方块，如下所示:

参见 [CodePen](https://codepen.io) 上的笔 [用户图标绿色方块](https://codepen.io/bryanrasmussen/pen/WNrWvwa)作者布莱恩·拉斯姆森([@布莱恩·拉斯姆森](https://codepen.io/bryanrasmussen) )
。

See the Pen [User Icon Green Square](https://codepen.io/bryanrasmussen/pen/WNrWvwa) by Bryan Rasmussen ([@bryanrasmussen](https://codepen.io/bryanrasmussen))
on [CodePen](https://codepen.io).

这是因为过滤器放在保存两条路径的`<g>`元素上。因此，洪水填充的子区域代表 SVG 本身的宽度和高度。

`objectBoundingBox`

### 如果过滤器上没有属性`primitiveUnits="objectBoundingBox"`,您只会看到一个绿色的用户图标，如下所示:

参见 [CodePen](https://codepen.io) 上的 Pen [用户图标泛滥 1](https://codepen.io/bryanrasmussen/pen/LYGvVZR)Bryan Rasmussen([@ Bryan Rasmussen](https://codepen.io/bryanrasmussen))
。

See the Pen [User Icon flooded 1](https://codepen.io/bryanrasmussen/pen/LYGvVZR) by Bryan Rasmussen ([@bryanrasmussen](https://codepen.io/bryanrasmussen))
on [CodePen](https://codepen.io).

`primitiveUnits`的工作方式就是这里所描述的[，但没有那么好。所以，澄清一些点:没有设置的时候，取默认值`userSpaceOnUse`，也就是说滤镜中使用的单位会和对象上的单位大小一样，一般是 1 个像素。](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/primitiveUnits)

我们希望它是`objectBoundingBox`的原因是，当应用过滤器时，它设置与`<g>`元素的对象[边界框](https://svgwg.org/svg2-draft/coords.html#BoundingBoxes)相关的值。这特别有用，因为当使用`objectBoundingBox`时，0 是边界框的开始，1 是结束，这意味着我们的动画可以从 0 移动到 1，反之亦然，并被理解。

`feOffset`如何工作

#### 这是使用`feOffset`完成的，它创建了图像的偏移。这个偏移图像基本上和我们的底层图像在同一个位置。`feOffset`是这个 MDN 资源中描述的[，但是，同样，没有那么好；你可以在规格](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/feOffset)中找到更好的描述[。](https://www.w3.org/TR/SVG11/filters.html#feOffsetElement)

在阅读规范时，你可以看到如果没有指定`feOffset`的`dx`和`dy`，那么默认值将是 0。因此，如果您的偏移如下所示:

然后，您将再次看到一个绿色图标:

```
    <feOffset dy="0" dx="0" />
```

参见 [CodePen](https://codepen.io) 上的 Pen [用户图标 feoffset dy 例 1](https://codepen.io/bryanrasmussen/pen/vYLwawv) 作者布莱恩·拉斯姆森([@布莱恩·拉斯姆森](https://codepen.io/bryanrasmussen) )
。

See the Pen [User Icon feoffset dy example 1](https://codepen.io/bryanrasmussen/pen/vYLwawv) by Bryan Rasmussen ([@bryanrasmussen](https://codepen.io/bryanrasmussen))
on [CodePen](https://codepen.io).

如果你使用`dy`属性——例如，如下所示:

你会看到头部的顶部是黑色的，图标的其余部分是绿色的洪水。如果您将那个`dy`增加到 1，您将在底部看到一条绿色的细线。

```
<feOffset dy="0.4" />
```

因此，如果您将`dy`从 1 设置为 0，它将从底部到顶部设置偏移动画，这意味着黑色偏移图像将从偏移到几乎覆盖原始图像，到根本看不见，无限重复。

很明显，如果你把它从 0 改成 1，那就是自上而下了。如果你把它改为动画从 1 到 0，它会从右到左，从 0 到 1 会从左到右。这对于用户图标来说不是一个令人满意的效果，但是看看这个箭头，它有一个从左到右的动画偏移:

请看 [CodePen](https://codepen.io) 上布莱恩·拉斯姆森([@布莱恩·拉斯姆森](https://codepen.io/bryanrasmussen) )
过滤 1 的钢笔 [箭头图标。](https://codepen.io/bryanrasmussen/pen/OJMYoMP)

See the Pen [Arrow Icon filtered 1](https://codepen.io/bryanrasmussen/pen/OJMYoMP) by Bryan Rasmussen ([@bryanrasmussen](https://codepen.io/bryanrasmussen))
on [CodePen](https://codepen.io).

在这个例子中，我们将`feFlood`的颜色改为较深的颜色，并将路径的填充改为绿色。你可以看到`dx`正在从 1 到 0 被动画化；如果我们没有这样做，它看起来就像是深色在“移动”，这不是一个加载动画想要的效果。

`feComposite`如何工作

#### 如果你没有这个:

然后你会有一个黑色的图标和一个绿色的正方形会从图形的底部动画覆盖图标，如下所示:

```
<feComposite operator="in" in2="SourceGraphic" />
```

参见 [CodePen](https://codepen.io) 上布莱恩·拉斯姆森([@布莱恩·拉斯姆森](https://codepen.io/bryanrasmussen) )
的笔[用户图标洪水 3](https://codepen.io/bryanrasmussen/pen/qBbwdmq) 。

如果你没有这个:

您会看到一个动画，但整个图标将从其区域的底部出现，而不仅仅是填充图标的颜色，如下所示:

参见 [CodePen](https://codepen.io) 上布莱恩·拉斯姆森([@布莱恩·拉斯姆森](https://codepen.io/bryanrasmussen) )
的笔[用户图标 flood 2](https://codepen.io/bryanrasmussen/pen/LYGvVRb) 。

```
<feComposite operator="over" in2="SourceGraphic" />
```

You would see an animation, but the whole icon would be coming up from the bottom of its area — not just of the color filling the icon — as seen here:

你可以在这里读到`feComposite`是如何工作的[，但它基本上是结合图像的。通过组合`in`和`over`操作符，我们得到了运行动画的顶层图像。](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/feComposite)

制作加载组件

现在我们已经对这个动画的工作原理有了足够的了解(虽然不是全部，因为 SVG 是一个大标准)，我认为将我们所学的转化为代码是有用的，这样我们就可以有一个通用的加载动画，它将采用任何 SVG 路径作为它的基础。

## 我将使用 React 来创建一个组件，尽管显然您可以在任何您喜欢的框架中，甚至在普通的 JavaScript 中做同样的事情。

下面的组件从它收到的任何路径中创建了一个简单的加载器，以及一条指令`startAt`，它可以接受字符串值`left`、`up`或`down`。如果不是这些值，则假设动画将从右向左运行。

它为您设置了一些默认值，当然，您可以覆盖这些值。假设在一个真实的项目中，你可能需要一些像样式这样的属性，或者总是从左边开始到右边，等等。

为了简洁起见，该组件还使用了嵌套的三元表达式，但是在现实环境中，为了便于理解，可能需要对这些表达式进行解包。

不管怎样，这里我们有一个工作示例:

所以，如果我们这样称呼它:

我们会得到一个矩形的装载图标，每三秒钟从黑色变成白色。你可以在这里看到:

```
    class Loader extends PureComponent {
        render() {
            const {
                    styles = {},
                    speed = "3s",
                    width = "100%",
                    height = "100%",
                    repeatCount = "indefinite",
                    startAt
            } = this.props;
            const isHoriz =  (startAt === "top" || startAt === "bottom");
            const from = (startAt === "left" || startAt === "bottom") ? 0 : 1;
            const to = (from === 1) ? 0 : 1;
            const attName = (isHoriz) ? "dy" : "dx";
            const fill = (styles.fill) ? styles.fill : (startAt === "left" || startAt === "bottom") ? "#fff" : "#000";
            const flood = (styles.flood) ? styles.flood : (fill === "#fff") ? "#000" : "#fff";
            const stroke = (styles.stroke) ? styles.stroke : "none";

            return (
                <svg  width={ width } height={ height }>
                    <defs>
                   <filter id="fullFill"
                    primitiveUnits="objectBoundingBox" 
                    x="0%" y="0%" 
                  width="100%" height="100%">
                          <feFlood x="0%" y="0%" 
                            width="100%" height="100%" 
                        floodColor={ flood } />
                          <feOffset>
                             <animate attributeName={attName}
                               from={from} to={to}
                               dur={ speed } 
                               repeatCount={ repeatCount }/>
                           </feOffset>
                           <feComposite operator="in"
                             in2="SourceGraphic" />
                          <feComposite operator="over"
                             in2="SourceGraphic" />
                            </filter>
                    </defs>
                    <g fill={ fill } filter={ `url(#fullFill)` } 
                         stroke={ stroke }>
                          { this.props.children }
                    </g>
                </svg>
            );
        }
    }
```

请看 [CodePen](https://codepen.io) 上布莱恩·拉斯姆森([@布莱恩·拉斯姆森](https://codepen.io/bryanrasmussen) )
的笔 [反应简单加载图标](https://codepen.io/bryanrasmussen/pen/MWKNyYQ)。

```
    <Loader startAt="left">
         <rect  x="0" width="100" height="10" />
    </Loader>
```

We would get a rectangular loading icon going from black to white every three seconds. You can see it here:

同样，如果我们想从底部到顶部制作动画，我们可以这样做:

看这里:

请看 [CodePen](https://codepen.io) 上布莱恩·拉斯姆森([@布莱恩·拉斯姆森](https://codepen.io/bryanrasmussen) )
的笔 [React 简单加载图标 2](https://codepen.io/bryanrasmussen/pen/BaKyyRP) 。

```
    <Loader startAt="bottom">
        <rect  x="0" height="100" width="10" />
    </Loader>
```

See here:

为了完整起见，这里有几个倒计数加载器的例子。从右到左:

请看 [CodePen](https://codepen.io) 上布莱恩·拉斯姆森([@布莱恩·拉斯姆森](https://codepen.io/bryanrasmussen) )
的笔 [React 简单加载图标 3](https://codepen.io/bryanrasmussen/pen/qBZEExr) 。

从上到下:

请看 [CodePen](https://codepen.io) 上布莱恩·拉斯姆森([@布莱恩·拉斯姆森](https://codepen.io/bryanrasmussen) )
的笔 [React 简单加载图标 4](https://codepen.io/bryanrasmussen/pen/KKzwwoO) 。

这里有一个例子，使用了我们之前选择的路径中的一个，该路径被赋予加载图标(`startAt="bottom"`):

请看 [CodePen](https://codepen.io) 上布莱恩·拉斯姆森([@布莱恩·拉斯姆森](https://codepen.io/bryanrasmussen) )
的笔[反应简单加载图标体](https://codepen.io/bryanrasmussen/pen/mdPyyKL)。

当然，这个组件需要一些其他的道具。在前面的例子中，它们的用法应该很明显，但是请看看下面的 CodePen 作为提示。这里，设置的样式是`{{flood: "green", fill: "white"}}`

在 [CodePen](https://codepen.io) 上看到 Bryan Rasmussen([@ Bryan Rasmussen](https://codepen.io/bryanrasmussen))
设定的反应笔[简单加载图标体样式。](https://codepen.io/bryanrasmussen/pen/QWNwjMa)

你应该注意到，如果你设置`Fill`为透明，那么洪水只会淹没笔画，所以你应该在那一点定义一个笔画。

如果您希望对象的边框不随加载滤镜而改变，您也可以在`styles`对象中设置描边颜色。在这里，我们将速度调整为 6 秒。

在 [CodePen](https://codepen.io) 上看到 Bryan Rasmussen([@ Bryan Rasmussen](https://codepen.io/bryanrasmussen))
设定的反应笔[简单加载图标体样式。](https://codepen.io/bryanrasmussen/pen/YzqPyEy)

You should note that if you set the `Fill` to be transparent, then the flood will only flood the stroke, so you should probably define a stroke at that point.

高度和宽度，如果没有设置，将默认为 100%，从而填充可用区域。在大多数情况下，我认为这些不需要设置，但你可能有理由这样做。当然，你也可以做一些傻事，比如将宽度设置为 50%，如下所示:

参见 Bryan Rasmussen([@ Bryan Rasmussen](https://codepen.io/bryanrasmussen))
上 [CodePen](https://codepen.io) 的笔 [React 简单加载图标体 50%](https://codepen.io/bryanrasmussen/pen/rNeVegG) 。

最后，`[repeatCount prop](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/repeatCount)`只是根据 SVG 属性说明我们希望动画多长时间重复一次。

您可能会考虑将其更改为只将类作为道具而不是样式传入，如以下代码笔所示:

参见 Bryan Rasmussen([@ Bryan Rasmussen](https://codepen.io/bryanrasmussen))
在 [CodePen](https://codepen.io) 上用 css 类反应简单加载图标体。

Finally, the `[repeatCount prop](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/repeatCount)` just says how often we want the animation to repeat, as per the SVG attribute.

为了节省空间，我没有在这里包括较小的代码更改，显然在现实生活中，您可能会选择更悦目的配色方案，但用法很清楚。无论如何，SVG 中有大量的属性可以由 CSS 设置；它们是规范中列出的[，这个属性列表](https://www.w3.org/TR/SVG/propidx.html)[会随着 SVG 2 的实现而增加](https://www.w3.org/TR/SVG2/styling.html#SVGStylingProperties)。

CSS 类的这种用法允许我们删除`fill`和`stroke`属性，不再拥有`style`对象——我们只需传入`flood`属性来定义路径填充的颜色。显然，这为通过我们通常的 React 状态管理方法来控制设置什么类提供了可能性。

结论

这样，我们就有了一个将简单的 SVG 图形(如图标)转化为加载动画的通用方法，以及一个示例 React 组件，我们可以将它与任何 SVG 路径集合一起用作子对象。我希望你会发现它很有用，并激励你在自己的应用程序中尝试 SVG 动画。

[LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

## 调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

.

## LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。

No more noisy alerting. Smart error tracking lets you triage and categorize issues, then learns from this. Get notified of impactful user issues, not false positives. Less alerts, way more useful signal.

The LogRocket Redux middleware package adds an extra layer of visibility into your user sessions. LogRocket logs all actions and state from your Redux stores.

Modernize how you debug your React apps — [start monitoring for free](https://lp.logrocket.com/blg/react-signup-general).