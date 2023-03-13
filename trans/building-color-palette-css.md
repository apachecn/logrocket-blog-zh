# 用 CSS 构建调色板:3 种方法

> 原文：<https://blog.logrocket.com/building-color-palette-css/>

决定网站的调色板是一项艰难的工作。这个过程非常依赖于设计、色彩理论和对比的知识。

在一个完整的网页设计过程的开始，我们通常会从 Figma 这样的设计工具开始。使用这个设计工具，我们可以精心制作我们的设计，尝试不同的配色方案，并最终决定最终产品的外观。

然后，可以使用 CSS 和 HTML 的不同架构方法将完成的设计转化为工作原型。本教程主要关注 CSS 技术，用于设置配色方案并在设计实现过程中应用它们。

在我们学习本教程的过程中，我将向您展示三种 CSS 技术——其中两种在专业上值得信赖——用于创建和实现调色板。我还会用现在实验中的 LCH 和 LAB 颜色函数来讨论相对颜色。

有了对色彩概念的基本理解，无论有没有设计计划，你都可以使用这些技巧。在我们进入技术细节之前，让我们回顾一些基本的网页设计和着色原则。

向前跳:

## 理解 60-30-10 设计规则

作为一个经验法则，古老的 60-30-10 设计法则在色彩和装饰的所有领域都非常有效。网络也不例外；当你开始使用这个规则时，创建你自己的调色板就容易多了。

60-30-10 规则中的每一组数字代表一种颜色权重。为了简单起见，让我们分别称它们为原色、次色和强调色。

![Graphic Demonstrating Sixty Thirty Ten Design Rule Using Primary Yellow, Secondary Dark Green, Blue Accent Colors, Labeled At Bottom. Background Is Yellow, About Sixty Percent Of Image. Bars Representing Text Are Dark Green, About Thirty Percent Of Image. Some Bars Along With Rectangle Representing Button Are Blue, About Ten Percent Of Image.](img/9b2463403a53f5ae588c7a16a9c72874.png)

原色覆盖了设计“实际空间”的最大部分，换句话说，大约是页面上 60%的空间。例如，它通常是背景中使用的颜色。

接下来是次要颜色，它占据了设计区域的 30%。大多数时候，是文本元素的颜色浮动在原色之上。

最后，强调色突出小而重要的设计细节，这些细节应该只占设计区域的 10%。行动号召按钮和超链接是应用强调色最常见的地方。

这种在用户界面上分配颜色的方法将使我们避免在设计中使用太多的颜色，并以复杂的配色方案告终。

## 探索色彩和谐和对比的概念

色彩和谐在视觉体验中提供了秩序感和平衡感。当一个设计在视觉上不和谐时，它会给浏览者留下一种无聊或混乱的感觉。

作为色彩和谐的一个组成部分，色彩对比衡量一种颜色放在另一种颜色上时的可读性或可访问性。在最终确定作品中的颜色之前，设计师应该检查他们设计中的颜色对比度是否足够高，以满足颜色可访问性标准。

[色彩理论原理](https://en.wikipedia.org/wiki/Color_theory)也涵盖了一些选择和谐色彩的通用方法。让我们检查其中的两个，我们将在本文后面使用它们来生成调色板。

### 互补色

色轮围绕一个圆圈组织颜色。这允许您通过分配给颜色在控制盘上的位置的度数值来表示颜色。

色轮上任意两个相对的颜色都是互补色，比如红色和青色或者蓝色和黄色。您可以通过增加或减少 180 度来计算给定色调的互补匹配:

![Color Wheel Shown With Six Gray Spokes Set Every Sixty Degrees From Zero At Top To Three Hundred Moving Clockwise. Spokes Are Labeled As Follows: Red At Zero Degrees, Yellow At Sixty Degrees, Green At One Hundred Twenty Degrees, Cyan At One Hundred Eighty Degrees, Blue At Two Hundred Forty Degrees, Magenta At Three Hundred Degrees. Caption At Bottom Explains That Colors Connected With Grey Spokes Are Complementary](img/6ce67e4ee5d31e23b336f9343d1c81af.png)

互补色据说可以创造最大的对比度和稳定性，因此在颜色方面使设计更容易理解。在本文的后面，我们将使用互补色来生成给定色调的对比色。

### 相似的颜色

如果三种颜色一起出现在 12 部分色轮上，如黄绿色、黄色和橘黄色，则这三种颜色是相似的。给定色调的类似对应物通过增加或减少 30 度来计算:

![Labeled Color Wheel From Previous Image Shown With Differently Placed Gray Spokes. Top And Bottom Halves Of Wheel Each Show Four Grouped Spokes Sitting Thirty Degrees Apart. Label At Bottom Explains Groups Of Spokes Represent Analogous Colors](img/a5a07716901e6fe4d21c3496a0a3634d.png)

由相似颜色组成的颜色组合通常会让人平静和赏心悦目。使用这些类型的颜色组合将省去我们从色轮中手动选择随机颜色并再次检查它们的协调性的麻烦。

现在我们已经对颜色和设计理论有了一个大致的概念，让我们进入事物的技术方面——例如，用 CSS 生成调色板的不同方法。

为了方便和灵活的颜色计划，我们将使用 HSL 颜色模型。如果你还没有使用过 HSL，[以下是你应该使用的原因](https://blog.logrocket.com/using-hsl-colors-css/)。

## 用 CSS Sass 变量创建调色板

虽然使用自定义属性和`calc()`是当今处理 CSS 的首选方式，但是熟悉[预处理器(如 Sass](https://blog.logrocket.com/the-definitive-guide-to-scss/) )的开发人员可以使用这种技术。CSS 预处理器提供了比原生 CSS 更细粒度的控制，这可能是使用这种方法优于其他方法的最好原因。

让我们只使用 Sass 变量选择一种色调并生成一个调色板:

```
$hue: 150;

```

基于我们上面关于互补色和类似色的讨论，让我们写三个函数，取`$hue`值并生成它的互补色和类似色。有两个独立的函数来获得给定色调的相应左和右相似邻居:

```
//
// Complementary hue can be obtained by adding or 
// substracting 180 from the specified hue amount.
//
@function complementary($hue) {
  @return $hue + 180; 
}
//
// The right analogous neighbor of a given hue 
// amount can be obtained by adding 30 to it.
//
@function rightAnalogous($hue) {
  @return $hue + 30;
}

//
// The left analogous neighbor of a given hue 
// amount can be obtained by subtracting 30 
// from it.
//
@function leftAnalogous($hue) {
  @return $hue - 30;
}

```

使用这些 Sass 函数，让我们计算我们在本节开始定义的`$hue: 150;`的互补和相似色调:

```
$hue: 150;
$hueAnalogousV1: rightAnalogous($hue);
$hueAnalogousV2: leftAnalogous($hue);
$hueComplement: complementary($hue);

```

我们现在有四种不同的色调值可以使用，我们可以用它们来创建我们的原色、次色和强调色。记住每个颜色值的应用，下面是我们如何通过调整饱和度和亮度的 HSL 参数来声明它们:

```
$primaryColor: hsl($hue 30% 90%);
$secondaryColor: hsl($hueComplement 25% 20%);
$accentColor: hsl($hueAnalogousV1 50% 50%);

```

因为原色会占据大部分屏幕空间，所以次色需要与之形成良好的对比。互补色最适合这个目的，所以它被用作第二种颜色。

我们的强调色可以是我们上面创建的任何类似的色调，只要饱和度足够高，以保持强调与突出显示的细节相关。

最后，我们可能需要原色的一些较暗的变化来装饰边框和分隔符。同样，次要颜色的较浅变化可以用来表示不太重要的文本细节，如标题和标签。

```
$primaryDark500: hsl($hue 20% 85%);
$primaryDark600: hsl($hue 20% 75%);
$secondaryLight500: hsl($hueComplement 5% 30%),
$secondaryLight900: hsl($hueComplement 5% 95%),

```

在上面，我使用了像`500`、`600`和`900`这样的数字来代表特定颜色的相对亮度和暗度。这有助于我回忆特定颜色值的含义。

对于突出显示的组件，如 CTA，强调色将作为背景，因此设计一些与强调色形成鲜明对比的颜色将是明智之举:

```
$accentColor: hsl($hueRightAnalogous 40% 40%);
$accentColorLight900: hsl($hueRightAnalogous 40% 95%);
$accentColor2: hsl($hueLeftAnalogous 40% 40%);
$accentColor2Light900: hsl($hueLeftAnalogous 40% 90%);

```

让我们将目前为止所涉及的一切付诸行动。下面是一个简单页面的示例，该页面使用调和色进行装饰，符合所有颜色辅助功能和对比度准则:

参见 [CodePen](https://codepen.io) 上 Rahul Chhodde([@ _ Rahul](https://codepen.io/_rahul))
的笔 [CSS Sass 调色板](https://codepen.io/_rahul/pen/dyKagre)。

调色板重新构建，一切都会调整——几乎是奇迹般的！—在上面的示例中，一旦修改了`$hue`的值。

如果你正在使用谷歌 Chrome，这里有一个提示:你可以通过点击 kebab 菜单，导航到“更多工具”，然后选择“CSS 概述”来检查 Chrome 开发者工具中的对比度问题:

![Small Google Chrome Window Opened Above Autumn Leaves Desktop Background. Browser Shows Chrome Developer Tools Panel For Mozilla Developer Network Website. Panel Is Open To Contrast Issues Checker](img/17062cd7aae327f8c1e3aa17b646090b.png)

在我们刚刚演示过的 Codepen 演示上试试吧。

## 使用 CSS 变量和 HSL 创建调色板

本质上，这种方法与从上述技术中移除 Sass 特性并添加 CSS 变量代替 T2 是一样的。对于数学计算，我们可以简单地使用`calc()` CSS 函数。然而，这并没有给我们提供与 Sass 一样干巴巴的方法。

话虽如此，Sass 可以在开发方面与这种技术相结合，这意味着我们仍然可以利用它的优势。为了简单起见，我将保持这个例子没有 Sass，但是作为练习，您可以选择将 Sass 与这个方法结合起来:

```
:root {
  --hue: 300; /* Let's try a different hue this time */
  --hueComplement: calc(var(--hue) + 180);
  --hueRightAnalogous: calc(var(--hue) + 30);
  --hueLeftAnalogous: calc(var(--hue) - 30);
}

```

正如你在上面看到的，我们需要手动计算互补和相似的色调。除了使用 CSS 自定义属性(`var(--hue)`)来代替 Sass 变量之外，其余的过程与最后一种技术基本相同。

下面的示例说明了如何使用 CSS 变量来创建主要颜色和次要颜色的变体:

```
:root {
  ...
  --primaryDark500: hsl(var(--hue) 20% 85%);
  --primaryDark600: hsl(var(--hue) 20% 75%);
  --secondaryLight500: hsl(var(--hueComplement) 5% 30%);
  --secondaryLight900: hsl(var(--hueComplement) 5% 95%);
}

```

同样可以生成一些色彩，与强调色形成更好的对比:

```
:root {
  ...
  --accentV1: hsl(var(--hueRightAnalogous) 40% 40%);
  --accentV1Light900: color-mix(in hsl, var(--accentV1) 20%, white 80%);
  --accentV2: hsl(var(--hueLeftAnalogous) 40% 40%);
  --accentV2Light900: color-mix(in hsl, var(--accentV2) 20%, white 80%);
}

```

下面是我们将刚才讨论的技术中的步骤放在一起后的演示。您可以尝试将色调值设置回 150，看看结果如何与上一个技巧中共享的示例完全相同:

参见 [CodePen](https://codepen.io) 上 Rahul Chhodde([@ _ Rahul](https://codepen.io/_rahul))
的 [CSS 自定义属性调色板](https://codepen.io/_rahul/pen/ExRrMYv)。

## 用`color-mix()`和`color-contrast()`创建调色板

大多数浏览器还不支持`color-mix()`和`color-contrast()` CSS4 函数。因此，这种方法仍处于发展阶段，可能无法完全发挥作用。然而，由于网络发展如此迅速，了解这些功能可能是值得的。

### 使用`color-mix()` CSS 函数创建调色板

`color-mix()`功能是[,目前只在 Firefox 上作为实验功能](https://caniuse.com/?search=color-mix)提供。你可以通过打开火狐浏览器配置(`about:config`)，查找`layout.css.color-mix.enabled`，然后设置为`true`来启用它。

有了`color-mix()`，我们不必每次需要使用不同的阴影或色调时都调整颜色的饱和度和亮度。我们可以简单地将黑色或白色与给定的颜色混合在一起就能完成。

为了快速理解它的工作原理，这里有一个通过混合蓝色和黄色生成绿色的简单示例:

```
:root {
  --neon-green: color-mix(in hsl, yellow 60%, blue);
}

```

`color-mix()`中的第一个参数代表颜色配置文件，另外两个代表混合在一起生成新颜色的颜色。

如果我们在上一个方法中实现了这个函数，下面是它将如何简化事情以生成不同的变化:

```
:root{
  ...
  --primary: hsl(var(--hue) 25% 95%);
  --primaryDark500: color-mix(in hsl, var(--primary) 90%, black);
  --primaryLight500: color-mix(in hsl, var(--primary) 90%, white);
}

```

我用`color-mix()`替换了上面方法中的 HSL 调整，下面是我得到的结果。请确保您在启用了`color-mix()`支持的 Firefox 上查看此内容:

参见 [CodePen](https://codepen.io) 上 Rahul Chhodde([@ _ Rahul](https://codepen.io/_rahul))
的 [CSS 自定义属性调色板](https://codepen.io/_rahul/pen/ExRrMOp)。

在 [MDN 文档中阅读更多关于](https://developer.mozilla.org/en-US/docs/Web/CSS/color_value/color-mix) `[color-mix()](https://developer.mozilla.org/en-US/docs/Web/CSS/color_value/color-mix)`语法和参数的内容。

### 使用 color-contrast() CSS 函数创建调色板

`color-contrast()`功能是目前仅在 Safari 15 到 16.1 上支持的[。这意味着你不能毫无争议地在 Windows 或 Linux 上测试它，因为苹果很久以前就停止为 Windows 和 Linux 制作 Safari 了。](https://caniuse.com/mdn-css_types_color_color-contrast)

如 [MDN 文档中所述，](https://developer.mozilla.org/en-US/docs/Web/CSS/color_value/color-contrast) `[color-contrast()](https://developer.mozilla.org/en-US/docs/Web/CSS/color_value/color-contrast)` [功能](https://developer.mozilla.org/en-US/docs/Web/CSS/color_value/color-contrast)从一系列颜色选择中为给定颜色挑选最佳对比色。这里有一个例子来说明它是如何工作的:

```
:root{
  --accent: blue;
  --accentLight900: color-contrast(var(--accent) vs white, cyan, magenta, black);
}

```

我们将不得不等待一段时间，直到我们可以结合这个功能与我们现有的技术，以决定更好的对比配色方案。

## 与 LCH 和实验室一起探索 CSS 中的相对颜色语法

我们的设备显示能力比 CSS 对颜色的支持发展得更快。现代显示器的色彩范围或色域更接近于[P3 色彩空间](https://en.wikipedia.org/wiki/DCI-P3)，它覆盖了几乎 87%的反射表面颜色。

相比之下， [sRGB 覆盖了这个色域的不到 70%](https://en.wikipedia.org/wiki/SRGB)。目前无法使用 CSS 访问 sRGB 以外的颜色。

好消息是 [CSS 颜色模块 Level 4](https://w3c.github.io/csswg-drafts/css-color/#lab-colors) 正在积极地将[对](https://caniuse.com/css-lch-lab) `[lch](https://caniuse.com/css-lch-lab)` [和](https://caniuse.com/css-lch-lab) `[lab](https://caniuse.com/css-lch-lab)` [颜色空间](https://caniuse.com/css-lch-lab)的支持作为 CSS 功能引入我们的浏览器。

简而言之，LAB 和 LCH 是高级色彩空间，可以提供更准确、更生动的色彩。这些颜色空间几乎像人眼一样感知和产生颜色。

这两种色彩空间在许多方面都很相似。LCH 对应于极坐标，即亮度、色度和色调。同时，LAB 使用笛卡尔坐标——亮度和两个色度轴。

与 HSL 和 RGB 不同，LCH 和 LAB 以相等的亮度渲染所有颜色。类似地，所有具有相同色度值的颜色看起来也同样饱和。这样，我们可以让我们的颜色彼此更加相关，并使用 CSS 创建更加生动的调色板。

这里有一个例子，展示了我们如何使用 CSS 2022 文章中介绍的`lch`颜色空间[来获得相对颜色。这个例子显示了`lch`函数如何使用一个基本的十六进制颜色，并通过亮度、色度和色调定制将其更改为 LCH 变化:](https://web.dev/state-of-css-2022/#relative-color-syntax)

```
:root {
  --primary: #eef;
  --primaryDarker: lch(from var(--primary) 90% c h);
  --primaryGrayish: lch(from var(--primary) l 50% h);
  --primaryYellowish: lch(from var(--primary) l c 77%);
}

```

在未来，看到这些空间的变化与其他实验特征如`color-mix`和`color-contrast`相结合将是令人着迷的。

## 结论

在上面的文章中，我们学习了颜色协调和对比的基础知识，颜色分布的 60-30-10 法则，以及用 CSS 创建调色板的三种技巧。我们还快速浏览了 CSS 中相对颜色语法的潜在未来。

为一个伟大的调色板仔细选择颜色通常是一个设计决策。作为一个经常在浏览器中而不是在设计工具中构建界面的前端开发人员，这些技术可以帮助您更好地理解颜色协调和对比度。

除了编写更直观、更具交互性的界面之外，你可能还会发现，有了这些知识，将设计转换成代码的压力会更小。

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。