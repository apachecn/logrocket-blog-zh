# 创建交互式 SVG:五度圈

> 原文：<https://blog.logrocket.com/interactive-svg-circle-of-fifths/>

不久前，这些美丽的海报出现在我的一个社交订阅上的广告中:

![Circle Of Fifths Ad](img/e35c45965be3c40619b7c9a7b57a1af8.png)

我通常会忽略所有的广告，但这些广告立刻吸引了我——可能是因为我曾经在高中学习音乐并在乐队演奏，或者可能是因为我曾经从事平面设计，这些广告太美了！

我马上就想在`<svg>`中重现它们。我经常在`<svg>`重新创作艺术作品，作为提高技能的一种练习。但是我突然想到:如果我能让海报变得生动起来，让它们具有互动性、灵活性和响应性，会怎么样？

在本教程中，我们将使用 SVG、CSS 和一点数学知识重新创建上面的海报。

向前跳:

## 圆弧和圆

在音乐理论中(用维基百科的话说),[五度圈](https://en.wikipedia.org/wiki/Circle_of_fifths)是一种将 12 个半音音高组织成一系列完美五度音的方式。

这个圆圈有三个环。外环包含降半音(b)或升半音(#)的五线谱。中环包含大和弦，内环包含小和弦。

一个完整的圆是 360 度，所以每个“半音音高”将是 30 (360/12)度。

挪威开发者哈肯·利德[开发了一些有用的 JavaScript 函数](https://observablehq.com/@haakenlid/svg-circle)，用于创建`<svg>`圆形线段。出于我们的目的，我们将使用`polarToCartesian`和`segmentPath`方法:

```
function polarToCartesian(x, y, r, degrees) {
  const radians = degrees * Math.PI / 180.0;
  return [x + (r * Math.cos(radians)), y + (r * Math.sin(radians))]
}
function segmentPath(x, y, r0, r1, d0, d1) {
  const arc = Math.abs(d0 - d1) > 180 ? 1 : 0
  const point = (radius, degree) =>
    polarToCartesian(x, y, radius, degree)
      .map(n => n.toPrecision(5))
      .join(',')
  return [
    `M${point(r0, d0)}`,
    `A${r0},${r0},0,${arc},1,${point(r0, d1)}`,
    `L${point(r1, d1)}`,
    `A${r1},${r1},0,${arc},0,${point(r1, d0)}`,
    'Z',
  ].join('')
} 

```

第一种方法用于将极坐标转换为笛卡尔坐标，第二种方法用于在 SVG 中创建带圆弧的路径。

接下来，我们将创建自己的`segment`方法，该方法将为每个“块”调用 haken 的`segmentPath`方法:

```
function segment(index, segments, size, radius, width) {
  const center = size / 2
  const degrees = 360 / segments 
  const start = degrees * index 
  const end = (degrees * (index + 1) + 1)
  const path = segmentPath(center, center, radius, radius-width, start, end)
  return `<path d="${path}" />`
}

```

`index`是当前的“段”(在我们的例子中是 12 个中的一个)，而`segments`表示段的总量(在我们的例子中也是 12 个)。`size`是圆的直径(也是我们 SVG 的`viewBox`)。`radius`通常是直径的一半，但是因为我们需要三个“环”，我们需要能够为每个“环”改变它最后，`width`是弧的高度。

让我们使用一个循环调用这个方法 12 次，每次迭代更新`index`:

```
segment(index, 12, size = 300, radius = 150, width = 150)

```

如果`width`设置为与`radius`相同的值，圆弧将填充圆:

![The Arc Filling The Circle](img/8086e2b824e9e9ca265ab42938897949.png)

但是，如果我们把`width`改成 50，它只会填满圆的三分之一(因为 50 是 150 的三分之一):

![Changing The Inside Width Of The Circle](img/5c01c55bd86dd182f7f88bf5a7378ea9.png)

让我们通过在循环中多次调用我们的`segment`方法来添加其他的圆:

```
segment(index, 12, 300, 100, 30) /* radius = 100, width = 30)
segment(index, 12, 300, 70, 30) /* radius = 70, width = 30)

```

现在我们有了这个——它看起来几乎像蜘蛛侠😁：

![Adding More Circles Within The Larger Circle](img/c8c5295add1432f2bf684f540344ef76.png)

在我们的五度圈里，文本应该准确地放在我们上面看到的行上。然而，弧本身不应该。

让我们使用 CSS `transform`函数来旋转圆弧。由于每个“块”是 30 度，我们需要将它们旋转一半——15 度:

```
transform: rotate(-15deg);
transform-origin: 50% 50%;

```

这给了我们:

![Rotating The Arcs Within The Circle](img/8640cd111e0136114677821748df1be0.png)

越来越近了！

现在，让我们添加五线谱、降半音和升半音。我从[维基共享资源](https://commons.wikimedia.org/wiki/Category:SVG_musical_notation)中抓取了我需要的元素，用杰克·阿奇博尔德的 [SVGOMG](https://jakearchibald.github.io/svgomg/) 清理了它们，然后我将每个元素转换成一个`<symbol>`，这样我就可以多次`<use>`它们。

但是在我们添加这些元素和填充我们的圈子之前，我们应该组织我们的数据。让我们创建一个 12 个对象的数组，包含标签和降半音或升半音的数量:

```
{
  outer: {
    amount: 4,
    use: 'flat'
  },
  middle: {
    label: 'A<tspan baseline-shift="super">b</tspan>'
  },
  inner: {
    label: 'Fm'
  }
}, /* etc */

```

`<tspan baseline-shift="super">`怎么了？因为我们在`<svg>`土地，我们不能使用`<sup>`。所以对于降半音和弦，我们用`baseline-shift`代替`<sup>`。

## 围绕一个圆放置元素

要将元素放入圆中，我们需要圆的中心点、圆的半径、角度和一些数学公式:

```
function posXY(center, radius, angle) {
  return [
    center + radius * Math.cos(angle * Math.PI / 180.0), 
    center + radius * Math.sin(angle * Math.PI / 180.0)
  ]
}

```

现在让我们把所有的例子合并成一个大的“渲染”块。`data`是我们之前创建的对象数组:

```
const size = 300; /* diameter / viewBox */
const radius = size/2;
const svg =  data.map((obj, index) => {
  const angle = index * (360 / data.length);    
  const [x0, y0] = posXY(radius, 125, angle);
  const [x1, y1] = posXY(radius, 85, angle);
  const [x2, y2] = posXY(radius, 55, angle);
  return `
  <g class="cf-arcs">
    ${segment(index, data.length, size, radius, 0, 50)}
    ${segment(index, data.length, size, 100, 0, 30, obj.middle.notes)}
    ${segment(index, data.length, size, 70, 0, 30, obj.inner.notes)}
  </g>
  <g transform="translate(${x0-15}, ${y0-radius})">
    <use width="30" xlink:href="#staff"></use>
    ${Array.from(Array(obj.outer.amount).keys()).map(i => `
      <use width="2" xlink:href="#${obj.outer.use}" class="cf-${obj.outer.use}-${i+1}"></use>`
    ).join('')}
  </g>
  <text x="${x1}" y="${y1+3}" class="cf-text-middle">${obj.middle.label}</text>
  <text x="${x2}" y="${y2+2}" class="cf-text-inner">${obj.inner.label}</text>
  `
  }).join('')

```

## 用`fill` s 和`stroke` s 设计圆的样式

设置页面的`background-color`并居中放置圆圈是很琐碎的事情。最重要的部分是我们之前创建的`<path>`。

没有`fill`或`stroke`，我们的圆看起来是这样的:

![Styling The Circle With Fills And Strokes](img/3ba2bb5142a035a59e35f1cc80978a9c.png)

让我们添加一些简单的样式，使用与`background-color`相匹配的笔画:

```
path {
  fill: hsl(348, 60%, 10%);
  stroke: hsl(348, 60%, 52%);
}

```

…既然如此，为什么不添加一个`hover`效果呢:

```
path:hover {
  fill: hsl(348, 60%, 25%);
}

```

我们终于有了我们的五度圈！是不是很美？

参见 [CodePen](https://codepen.io) 上 Mads Stoumann([@ Stoumann](https://codepen.io/stoumann))
的 Pen [五度圈](https://codepen.io/stoumann/pen/abLeMVM)。

## 定制我们圈子的审美

现在，让我们创建灰蓝色版本——当我们在做的时候，让我们添加一些微妙的噪声，让它看起来像古董:

参见 [CodePen](https://codepen.io) 上 Mads Stoumann([@ Stoumann](https://codepen.io/stoumann))
的 Pen [五度圈——蓝色复古](https://codepen.io/stoumann/pen/qBPevxe)。

粒状，噪声过滤器是一个 [SVG](https://blog.logrocket.com/complete-guide-using-css-filters-svgs/) [过滤器](https://blog.logrocket.com/complete-guide-using-css-filters-svgs/)，用作 CSS 背景。

## 结论

在本教程中，我们学习了如何使用一点数学知识从头开始编写 SVG 代码。将数据放入一个圆中并不困难，随着 CSS 即将推出[三角函数](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Functions#trigonometric_functions) [i](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Functions#trigonometric_functions) [ons](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Functions#trigonometric_functions) ，这将变得更加容易。

我希望你读得开心——希望这篇文章能启发你用 SVG 做一些创造性的编码！如果你对在 SVG 中使用 [CSS 滤镜](https://blog.logrocket.com/complete-guide-using-css-filters-svgs/)或[用 CSS 制作 SVG 动画](https://blog.logrocket.com/how-to-animate-svg-css-tutorial-examples/)感兴趣，可以看看这些文章。

下面是启发这个教程的[原图](https://detkuloerteudvalg.dk/vare/det-kuloerte-udvalg-kvintcirklen-plakat/)。

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。