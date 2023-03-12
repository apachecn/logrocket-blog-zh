# 网络动画 API 与 CSS - LogRocket 博客

> 原文：<https://blog.logrocket.com/the-web-animations-api-vs-css/>

网页动画是网页设计中很受欢迎的一部分，它导致了许多 JavaScript 动画库的发展，包括 [Framer Motion](https://blog.logrocket.com/framer-motion-tutorial/) 和 [GreenSock](https://blog.logrocket.com/animating-vue-with-greensock/) 。然而，许多开发人员仍然选择使用 CSS 过渡、CSS 动画或 JavaScript web 动画 API (WAAPI)从头开始创建 Web 动画。

在本教程中，我们将深入讨论这三种方法，通过相关演示学习如何使用每一种方法。我们将比较每种方法，并讨论在我看来使[网络动画 API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Animations_API) 被低估的许多好处。我们开始吧！

## CSS 过渡

CSS 转换允许我们无缝地将 CSS 属性从一种状态改变到另一种状态。例如，假设我们想将元素的`background-color`属性值从黑色改为红色。当然，变化会立即发生，但是，CSS 转换让我们可以控制过程的某些方面。

例如，我们可以决定更改何时开始，动画显示哪些属性，以及动画将运行多长时间。要制作动画，该属性必须包含在 [CSS 动画属性](https://blog.logrocket.com/css-reference-guide-transitions/)的列表中。

### 性能

让我们回顾一下创建 CSS 过渡所需的 CSS 属性:

*   `transition-property`:我们将应用过渡的 CSS 属性
*   `transition-duration`:过渡应该运行的时间长度
*   动画开始前要等多长时间
*   `transition-timing-function`:过渡的速度

`transition-timing-function`要么是直线，要么是速度递增，要么是速度递减。通常，`transition-timing-function`要么是一个确定 CSS 属性在中间点的值的函数，要么是一个与该函数等价的关键字。

您可以使用`transition`速记属性来代替上述四个属性:

```
.element {
  transition: <property> <duration> <timing-function> <delay>;
}

```

### 演示

现在，让我们看看 CSS 转换的效果！在下面的代码笔中，当用户悬停在显示屏上时，背景颜色将从黑色变为红色。

参见 [CodePen](https://codepen.io) 上 Udoh Idorenyin([@ idorenyinudoh](https://codepen.io/idorenyinudoh))
的[笔 css 转场测试](https://codepen.io/idorenyinudoh/pen/PomdrEZ)。

多个动画

### 在下面的演示中，您将看到我们如何在多个 CSS 属性中应用 CSS 过渡，方法是用逗号分隔过渡属性的值:

参见 [CodePen](https://codepen.io) 上的笔 [css 转场测试 2](https://codepen.io/idorenyinudoh/pen/KKmxjBX) 由 Udoh Idorenyin([@ idorenyinudoh](https://codepen.io/idorenyinudoh))
。

See the Pen [css transitions test 2](https://codepen.io/idorenyinudoh/pen/KKmxjBX) by Udoh Idorenyin ([@idorenyinudoh](https://codepen.io/idorenyinudoh))
on [CodePen](https://codepen.io).

现在我们已经熟悉了 CSS 过渡，让我们来看看 CSS 动画。

CSS 动画

## CSS 动画让我们让我们的动画更有趣一点。我们将需要一个`@keyframes` CSS at-rule，它指定动画开始、结束和动画周期中的 CSS 规则。我们还需要一个初始化 CSS 动画的规则。

性能

### 让我们来看看创建 CSS 动画所需的属性:

`animation-name`:at 规则`@keyframes`的名称

*   `animation-play-state`:指定动画是`running`还是`paused`
*   `animation-direction`:指定动画的方向
*   `animation-iteration-count`:动画将完成的循环数
*   `animation-fill-mode`:动画如何在执行前后将样式应用到元素
*   如果`animation-iteration-count`设置为`infinite`，动画将连续播放。如果`animation-fill-mode`设置为`forward`，元素会保留最后一个关键帧的样式。如果设置为`backward`，第一个关键帧的样式将在动画开始前应用于元素。

`animation-delay`是应用和播放动画之间的时间。它类似于 CSS 转场中的`transition-delay`。`animation-duration`是动画完成一个循环所需的时间。它的对等物是`transition-duration`。

最后，`animation-timing-function`表示动画在其周期中的进展。它的对等物是`transition-timing-function`。像在 CSS 过渡中一样，CSS 动画有一个`animation`简写来处理这些属性:

演示

```
.element {
  animation: <name> <duration> <timing-function> <delay> <iteration-count> <direction> <fill-mode>;
}

```

### 为了了解 CSS 动画是如何工作的，让我们重新创建上面的演示，并将对象的背景色从黑色改为红色:

请看 [CodePen](https://codepen.io) 上 Udoh Idorenyin([@ idorenyinudoh](https://codepen.io/idorenyinudoh))
的笔 [css 动画测试](https://codepen.io/idorenyinudoh/pen/eYWMYrJ)。

See the Pen [css animations test](https://codepen.io/idorenyinudoh/pen/eYWMYrJ) by Udoh Idorenyin ([@idorenyinudoh](https://codepen.io/idorenyinudoh))
on [CodePen](https://codepen.io).

您可能已经注意到了 CSS 转换的一些关键差异。首先，动画会立即开始，而不是由用户初始化。CSS 动画属性可以利用`@keyframes` at-rule 来运行元素。

然而，在 CSS 转换中，定义动画结束状态的规则必须在不同于元素默认声明的声明中。出于这个原因，我们在第一个演示中使用了`:hover`伪类。

在 CSS 动画中，当动画完成时，元素会立即返回其默认样式。另一方面，默认情况下，CSS 转换是双向的。

例如，当我们的背景从黑色变为红色时，动画运行，反之亦然。为了用 CSS 动画实现这一功能，我们需要再次定义动画并将`animation-direction`属性设置为`reverse`。

多个动画

### 要使用 CSS 动画创建多个动画，我们可以在一个`@keyframes` at-rule 中输入我们想要制作动画的所有属性，并在一个`animation`属性中使用它，如下面的代码片段所示:

另一种方法是使用多个`@keyframes` at 规则，并用逗号分隔动画属性值。如果要对动画属性使用不同的值，以下方法是理想的:

```
.element {
  animation: color-change 2s ease-in-out 1s infinite forwards;
}

@keyframes color-width-change {
  0% {
    background: black;
  }
  25% {
    width: 200px;
  }
  100% {
    background: red;
    width: 500px;
  }
}

```

下面的代码笔包含上面代码片段的演示:

```
.element {
  animation: color-change 2s ease-in-out 1s infinite forwards, width-change 3s ease-in 0s infinite forwards;
}

@keyframes color-change {
  0% {
    background: black;
  }
  100% {
    background: red;
  }
}

@keyframes width-change {
  25% {
    width: 200px;
  }
  100% {
    width: 500px;
  }
}

```

参见 [CodePen](https://codepen.io) 上 Udoh Idorenyin([@ idorenyinudoh](https://codepen.io/idorenyinudoh))
的笔 [css 动画测试 2](https://codepen.io/idorenyinudoh/pen/WNjYBgm) 。

See the Pen [css animations test 2](https://codepen.io/idorenyinudoh/pen/WNjYBgm) by Udoh Idorenyin ([@idorenyinudoh](https://codepen.io/idorenyinudoh))
on [CodePen](https://codepen.io).

您会注意到当`color-change`动画完成时,`width-change`动画仍在运行。为了避免重复，您可以从简写中排除一些属性，而是用单个的`animation-*`属性在一个值中指定它们。

网络动画 API

## 动画的 JavaScript 方法，Web Animations API，给开发者更多的控制和 CSS 动画的细微差别。尽管 Web Animations API 最初在设置时可能看起来更复杂，但是您可以将它与元素的样式分离，从而使它比 CSS 更容易编写，也更简单。

为了用 Web Animations API 制作 DOM 元素的动画，我们在元素上调用了`animate()`方法。`animate()`方法有两个参数，关键帧和选项。与 CSS 过渡和 CSS 动画一样，元素必须是可动画的。

关键帧

### 关键帧可以是关键帧对象的数组，也可以是以值数组作为属性的对象:

在第一种方法中，关键帧对象在整个动画周期中均匀分布。例如，在上面的代码块中，默认情况下，点数将是 0%、50%和 100%。要更改点，请在关键帧对象中设置一个值在`0.0`和`1.0`之间的`offset`属性，其中`0.5`为 50%。

```
const keyframes = [
    { opacity: 1 },
    { opacity: 0 },
    { opacity: 1 }
]

// OR

const keyframes = {
    opacity: [ 1, 0, 1 ]
}

```

考虑 CSS 中的以下示例:

使用 Web Animations API 中的第一个关键帧方法，上面的 CSS 代码如下所示:

```
@keyframes fade-in-out {
    0% {
        opacity: 0;
    }
    60% {
        opacity: 1;
    }
    100% {
        opacity: 0;
    }
}

```

如果我们使用第二个关键帧方法，并使用一个值数组作为属性，我们的 CSS 代码将如下所示:

```
const keyframes = [
    { opacity: 1 },
    { opacity: 0, offset: 0.6 },
    { opacity: 1 }
]

```

在 CSS 中，您可以为动画使用计时功能，但是，使用 Web Animations API，您可以为每个关键帧以及整个动画指定计时功能。

```
const keyframes = {
  opacity: [0, 1, 0],
  offset: [0, 0.6, 1] //could also be [0, 0.6]
}

```

以包含`offset`属性的相同方式包含关键帧的`easing`属性。`easing`属性将从该关键帧应用到下一个指定的关键帧:

为了在整个动画中包含`easing`值，我们将把它包含在 options 对象中:

```
const keyframes = [
    { opacity: 1, easing: 'ease-in' },
    { opacity: 0, offset: 0.6, easing: 'ease-out' },
    { opacity: 1, easing: 'ease-in-out' }
]

// OR

const keyframes = {
    opacity: [ 1, 0, 1 ],
    offset: [0, 0.6],
    easing: ['ease-in', 'ease-out', 'ease-in-out']
}

```

`options`

### `options`可以是一个整数类型，以毫秒为单位指定动画的持续时间，也可以是一个`EffectTiming`对象，包含至少一个与 CSS 动画属性等效的对象。让我们回顾一下这些属性的 Web 动画 API 的等价物:

CSS 动画属性

| W eb 动画 API 等效 | `animation-name` |
| --- | --- |
| `id` | `animation-duration` |
| `duration` | `animation-timing-function` |
| `easing` | `animation-fill-mode` |
| `fill` | `animation-iteration-count` |
| `iterations` | `animation-direction` |
| `direction` | `animation-delay` |
| `delay` | 我们将很快介绍与`animation-play-state` CSS 属性等价的内容。然而，您可以在`options`对象中包含几个没有 CSS 对等物的属性: |

`endDelay`指定动画结束后的延迟时间，这在我们希望连续播放动画时很有帮助。

`iterationStart`指定动画应该开始的迭代点，范围从`0.0`到`1.0`。例如，`0.3`的值意味着动画应该从 30%的关键帧点开始。

更多来自 LogRocket 的精彩文章:

* * *

### `composite`指定动画的值如何与没有`composite`属性的其他动画组合。例如，值`add`会将下一个动画添加到上一个动画中。很自然，之前的那个会被覆盖。

* * *

`iterationComposite`指定如何在动画中从一次迭代到另一次迭代构建值。

`animate()`

### `animate()`方法返回一个`Animation`对象。让我们考虑对象提供的属性和方法。

性能

#### `currentTime`:动画的当前点，以毫秒为单位

*   `effect`:`AnimationEffect`对象，它是一个`KeyframeEffect`对象
*   当动画结束时，返回一个解决的承诺
*   `id`:用于标识动画的`id`
*   `pending`:动画正在等待操作完成
*   `playbackRate`:动画的播放速度
*   `ready`:返回一个承诺，当动画准备好播放时，这个承诺就生效
*   `replaceState`:返回动画的替换状态
*   `startTime`:动画预定开始的时间
*   `timeline`:与动画相关的时间线
*   `playState`:指定动画是否播放
*   方法

#### `cancel()`:清除动画创建的`KeyframeEffect`，结束播放

*   `finish()`:前进到动画的结尾
*   `pause()`:暂停动画
*   `persist()`:当浏览器已经移除动画时，将动画保留在元素中
*   `play()`:开始或恢复动画
*   `reverse()`:从头到尾播放动画
*   `updatePlaybackRate()`:设定动画开始后的速度
*   `commitStyles()`:将动画结束状态的样式提交给元素
*   该对象的其他事件包括`cancel`、`finish`和`remove`事件。

演示

### 让我们使用 Web Animations API 的属性、方法和选项重新创建之前的动画。我们的 JavaScript 文件应该类似于下面的代码块:

为了连续播放动画，我们使用 JavaScript 关键字`Infinity`作为`options`对象中`iterations`属性的值。

```
const element = document.querySelector('div');

const keyframes = {
    background: [ 'black', 'red' ]
}

const options = {
    id: "color-change",
    duration: 2000,
    delay: 1000,
    easing: "ease-in-out",
    iterations: Infinity,
    fill: "forwards"
}

element.animate(keyframes, options);

```

参见 [CodePen](https://codepen.io) 上 Udoh Idorenyin([@ idorenyinudoh](https://codepen.io/idorenyinudoh))
的 Pen [网页动画 api 测试](https://codepen.io/idorenyinudoh/pen/wvdmXKz)。

See the Pen [web animations api test](https://codepen.io/idorenyinudoh/pen/wvdmXKz) by Udoh Idorenyin ([@idorenyinudoh](https://codepen.io/idorenyinudoh))
on [CodePen](https://codepen.io).

要创建多个动画，我们可以在一个关键帧对象中包含多个属性。为了在`options`对象中使用不同的属性值，我们可以创建多个关键帧和`options`对象，并在一个元素上多次使用`animate()`方法。

参见 [CodePen](https://codepen.io) 上 Udoh Idorenyin([@ idorenyinudoh](https://codepen.io/idorenyinudoh))
的 Pen [网页动画 api 测试 2](https://codepen.io/idorenyinudoh/pen/bGWOEqg) 。

See the Pen [web animations api test 2](https://codepen.io/idorenyinudoh/pen/bGWOEqg) by Udoh Idorenyin ([@idorenyinudoh](https://codepen.io/idorenyinudoh))
on [CodePen](https://codepen.io).

使用 WAAPI 制作动画的优势

## 现在我们已经了解了如何使用 CSS 过渡、CSS 动画和 Web 动画 API 制作动画，让我们注意一下使用 Web 动画 API 的一些经常被忽略的主要优点。

首先，使用 CSS，我们只能为整个动画使用一个计时函数。然而，使用 Web Animations API，我们可以在`options`对象中为不同的关键帧和整个动画指定`easing`属性。

通过 Web Animations API，我们可以使用`Animation`对象的`currentTime`和`startTime`属性以及`Animation`事件向一个元素添加几个动画。在 CSS 中，您必须用一个动画覆盖另一个动画，一起播放所有动画，或者使用 JavaScript 向同一元素添加另一个动画。

Web 动画 API 中的`options`对象的`composite`属性允许我们向浏览器指示我们希望连续的动画如何在一个元素上运行。CSS 没有对等物。

结论

## web 动画 API 中的属性、方法和事件使得在 Web 上自定义动画变得很容易。根据我的经验，不复杂的动画最好用 CSS 实现，而复杂的动画最好用 JavaScript。

诚然，从头开始制作动画会变得非常困难。你可能想考虑第三方动画工具，比如 [Flow](https://createwithflow.com) ，它可以将动画导出为 Web Animations API 之类的格式。

我希望你喜欢这个教程。快乐动画！

你的前端是否占用了用户的 CPU？

## 随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。

Modernize how you debug web and mobile apps — [Start monitoring for free](https://lp.logrocket.com/blg/css-signup).