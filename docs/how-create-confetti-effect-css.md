# 如何在 CSS 中创建五彩纸屑效果

> 原文：<https://blog.logrocket.com/how-create-confetti-effect-css/>

给网站添加五彩纸屑效果通常意味着安装一个软件包。如果我们只用纯粹的 CSS 和 HTML 就能为自己创建一个，会怎么样？好吧，我们就这么办！

在这篇文章中，我们将学习如何用 CSS 创建一个五彩纸屑效果。我们将学习如何在 CSS 中制作不同的形状，包括正方形、矩形、六边形、五角星形、十边形和波浪线，以及学习制作形状动画和随机化它们的特征以创建五彩纸屑效果。

## 设置我们的文件

我们要做的第一件事是在我们的机器上创建一个文件夹。我给我的取名为`app`，但是你可以随意给你的取名。

用您选择的代码编辑器打开文件夹。接下来，在名为`index.html`和`style.css`的文件夹中创建两个文件。

`index.html`文件将有这样的起始标记:

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <link rel="stylesheet" href="style.css" />
    <title>Confetti</title>
  </head>
  <body>
    <label for="check">Check to stop</label>
    <input type="checkbox" id="check" />

    <div class="confetti-container">
      <div class="confetti">
        <i class="square"></i>
        <i class="rectangle"></i>
        <i class="hexagram"></i>
        <i class="pentagram"></i>
        <i class="dodecagram"></i>
        <i class="wavy-line"></i>
      </div>
    </div>
  </body>
</html>

```

在上面的标记中，我们有一些 meta 标签，一个标题，以及一个到 CSS 文件的链接。body 元素包含一个标签和一个复选框，仅用于演示目的。

`confetti-container` 类包含`confetti`,`confetti`包含各种`i`标签，这些标签的类与我们将要创建的形状相匹配。

## 用 CSS 创建不同风格的五彩纸屑

为了创建不同风格的纸屑，我们将打开`style.css`文件。

在我们创建形状之前，第一个目标元素是`root`、`body`、`html`、`label`和`input`。我们将为`root`和`html`添加以下样式:

```
:root {
  --bg: yellow;
}

html,
body {
  background-color: #101e35;
  font-size: 10px; /* Makes 1rem = 10px */
}

```

接下来是`label`和`input`:

```
label {
  color: white;
  font-size: 1.5rem;
}
input {
  width: 40px;
  height: 40px;
  cursor: pointer;
}

```

然后我们为`.confetti-container`和`.confetti`设置如下样式:

```
.confetti-container {
  user-select: none;
  pointer-events: none;
  z-index: 10;
}

.confetti {
  position: fixed;
  left: 0;
  right: 0;
  display: flex;
}

```

让我们把它分解一下。

`user-select`和`pointer-events`确保`.confetti-container`的内容不会被用户高亮显示。

然后，`z-index`确保`.confetti-container`的内容保持在页面上的每个元素之上。

`.confetti`中的样式确保了五彩纸屑的行为独立于页面上的其他内容。设置`display`是为了确保`.confetti`的内容总是水平堆叠。

## 创造不同的五彩纸屑形状

现在，我们将学习如何为我们的五彩纸屑效果创建不同的形状。我们这样做是为了给五彩纸屑效果增加一些变化！

### 创建一个正方形

为了创建一个正方形，我们将选择`.square`类并向其添加以下样式:

```
.confetti .square {
  width: 1rem;
  height: 1rem;
  background-color: var(--bg);
  transform: rotate(140deg);
}

```

`var(--bg)`是一个 CSS 变量的例子。这是在`:root`中设置的，但是，它也将直接设置在 HTML 中的每个形状中。

`transform`属性用于旋转正方形。

### 创建一个矩形

长方形和正方形相似，但是宽度和高度不一样。以下样式将生成一个矩形:

```
.confetti .rectangle {
  width: 1rem;
  height: 0.5rem;
  background-color: var(--bg);
}

```

### 创造一个六边形

是一颗有六个面的星星。下面的代码将为我们生成一个:

```
.confetti .hexagram {
  width: 0;
  height: 0;
  border-left: 0.5rem solid transparent;
  border-right: 0.5rem solid transparent;
  border-bottom: 1rem solid var(--bg);
  position: relative;
}

.confetti .hexagram:after {
  content: "";
  width: 0;
  height: 0;
  border-left: 0.5rem solid transparent;
  border-right: 0.5rem solid transparent;
  border-top: 1rem solid var(--bg);
  position: absolute;
  top: 0.33rem;
  left: -0.5rem;
}

```

在`.confetti .hexagram`中，将宽度和高度设置为`0`，并以同样的方式设置边界，确保我们生成一个三角形。`position`元素确保其中的属性可以独立移动。

在`.confetti .hexagram:after`中，我们生成另一个反转的三角形。当`position`设置为`absolute`时，我们可以设置`top`和`left`的值，以正确定位并形成六边形星形。

### 创建五角星形

要创建五角星，我们需要制作五角星。为此，我们将向`.pentagram`添加以下样式:

```
.confetti .pentagram {
  width: 0rem;
  height: 0rem;
  display: block;
  margin: 0.5rem 0;
  border-right: 1rem solid transparent;
  border-bottom: 0.7rem solid var(--bg);
  border-left: 1rem solid transparent;
  transform: rotate(35deg);
  position: relative;
}
.confetti .pentagram:before {
  border-bottom: 0.8rem solid var(--bg);
  border-left: 0.3rem solid transparent;
  border-right: 0.3rem solid transparent;
  position: absolute;
  height: 0;
  width: 0;
  top: -0.45rem;
  left: -0.65rem;
  display: block;
  content: "";
  transform: rotate(-35deg);
}
.confetti .pentagram:after {
  position: absolute;
  display: block;
  color: var(--bg);
  top: 0.03rem;
  left: -1.05rem;
  width: 0rem;
  height: 0rem;
  border-right: 1rem solid transparent;
  border-bottom: 0.7rem solid var(--bg);
  border-left: 1rem solid transparent;
  transform: rotate(-70deg);
  content: "";
}

```

我们来分解一下。

`.confetti .pentagram`生成一个三角形并将其旋转`35deg`。

`.confetti .pentagram:before`也生成一个三角形，并将其旋转`-35deg`。`position`被设置为垂直和水平移动元素。

`.confetti .pentagram:after`生成另一个三角形并将其旋转`70deg`。此外，`position`用于移动顶部和`bottom`位移。

### 创建十二个字母

十二边形是一个 12 面星。它把正方形组合成它的形状。让我们在`.dodecagram`元素中构建它:

```
.confetti .dodecagram {
  background: var(--bg);
  width: 8px;
  height: 8px;
  position: relative;
}

.confetti .dodecagram:before {
  content: "";
  height: 8px;
  width: 8px;
  background: var(--bg);
  transform: rotate(30deg);
  position: absolute;
  top: 0;
  left: 0;
}
.confetti .dodecagram:after {
  content: "";
  height: 8px;
  width: 8px;
  background: var(--bg);
  transform: rotate(60deg);
  position: absolute;
  top: 0;
  left: 0;
}

```

`.confetti .dodecagram`生成一个正方形，但是`confetti .dodecagram:before`和`.confetti .dodecagram:after`生成两个旋转的正方形。

### 用 CSS 创建波浪线

为了创建我们的`.wavy-line`元素，我们将编写以下代码:

```
.confetti .wavy-line {
  position: relative;
}
.confetti .wavy-line::after,
.confetti .wavy-line::before {
  content: "";
  height: 10px;
  width: 80px;
  background-size: 20px 10px;
  position: absolute;
  left: -9rem;
  transform: rotate(90deg);
}

.confetti .wavy-line::before {
  background-image: linear-gradient(
    45deg,
    transparent,
    transparent 50%,
    var(--bg) 50%,
    transparent 60%
  );
  top: 1rem;
}
.confetti .wavy-line::after {
  background-image: linear-gradient(
    -45deg,
    transparent,
    transparent 50%,
    var(--bg) 50%,
    transparent 60%
  );
}

```

让我们更详细地检查一下这个。
`.confetti .wavy-line`用于设置其内容的相对位置。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

在`.confetti .wavy-line::after`和`.confetti .wavy-line::before, background-size`中用于指定伪元素的水平和垂直尺寸。

然后使用`background-image`绘制具有特定角度的线条。

## 用瀑布效果制作形状动画

为了给形状添加动画，使它们模拟瀑布，我们将添加以下样式:

```
.confetti i {
  width: 3rem;
  height: 3rem;
  margin: 0 0.2rem;
  animation-name: confetti;
  animation-timing-function: linear;
  animation-iteration-count: infinite;
  animation-duration: calc(60s / var(--speed));
}

.confetti i:nth-child(even) {
  transform: rotate(90deg);
}

@keyframes confetti {
  0% {
    transform: translateY(-100vh);
  }

  100% {
    transform: translateY(100vh);
  }
}

```

在代码中，`.confetti i`用于选择每个形状并对其应用动画属性。

`calc()`是 CSS 中用于执行计算的函数。

我们选择`.confetti i:nth-child(even)`来旋转索引为偶数的每个形状。

最后，在 `@keyframes confetti`下，我们创建一个动画来使形状从页面的顶部移动到底部。

## 随机化形状、动画、持续时间和颜色

为了使形状以不同的持续时间落下并具有不同的颜色，我们必须使用 HTML 将变量直接传递给形状，然后完全去掉`:root`:

```
<div class="confetti-container">
      <div class="confetti">
        <i style="--bg: red; --speed: 20" class="square"></i>
        <i style="--bg: green; --speed: 24" class="rectangle"></i>
        <i style="--bg: white; --speed: 13" class="hexagram"></i>
        <i style="--bg: yellow; --speed: 10" class="pentagram"></i>
        <i style="--bg: purple; --speed: 17" class="dodecagram"></i>
        <i style="--bg: pink; --speed: 15" class="wavy-line"></i>
      </div>
</div>

```

接下来，我们将创建更多这样的形状，添加随机的颜色，并给它们一些速度来生成我们的五彩纸屑:

```
<div class="confetti-container">
      <div class="confetti">
        <i style="--speed: 10; --bg: yellow" class="square"></i>
        <i style="--speed: 18; --bg: white" class="pentagram"></i>
        <i style="--speed: 29; --bg: green" class="rectangle"></i>
        <i style="--speed: 17; --bg: blue" class="hexagram"></i>
        <i style="--speed: 33; --bg: red" class="pentagram"></i>
        <i style="--speed: 26; --bg: yellow" class="dodecagram"></i>
        <i style="--speed: 24; --bg: pink" class="wavy-line"> </i>
        <i style="--speed: 5; --bg: blue" class="wavy-line"></i>
        <i style="--speed: 40; --bg: white" class="square"></i>
        <i style="--speed: 17; --bg: green" class="rectangle"></i>
        <i style="--speed: 25; --bg: white" class="square"></i>
        <i style="--speed: 18; --bg: green" class="rectangle"></i>
        <i style="--speed: 15; --bg: yellow" class="wavy-line"> </i>
        <i style="--speed: 32; --bg: yellow" class="pentagram"></i>
        <i style="--speed: 25; --bg: white" class="square"></i>
        <i style="--speed: 18; --bg: green" class="rectangle"></i>
        <i style="--speed: 37; --bg: yellow" class="dodecagram"></i>
        <i style="--speed: 23; --bg: pink" class="wavy-line"></i>
        <i style="--speed: 37; --bg: red" class="dodecagram"></i>
        <i style="--speed: 37; --bg: pink" class="wavy-line"></i>
        <i style="--speed: 36; --bg: white" class="hexagram"></i>
        <i style="--speed: 32; --bg: green" class="wavy-line"></i>
        <i style="--speed: 32; --bg: yellow" class="pentagram"></i>
        <i style="--speed: 29; --bg: white" class="square"></i>
        <i style="--speed: 18; --bg: green" class="rectangle"></i>
        <i style="--speed: 37; --bg: red" class="dodecagram"></i>
        <i style="--speed: 23; --bg: pink" class="wavy-line"> </i>
        <i style="--speed: 30; --bg: pink" class="rectangle"></i>
        <i style="--speed: 30; --bg: red" class="square"></i>
        <i style="--speed: 18; --bg: red" class="pentagram"></i>
        <i style="--speed: 19; --bg: green" class="rectangle"></i>
        <i style="--speed: 16; --bg: blue" class="hexagram"></i>
        <i style="--speed: 23; --bg: red" class="pentagram"></i>
        <i style="--speed: 34; --bg: yellow" class="dodecagram"></i>
        <i style="--speed: 39; --bg: pink" class="wavy-line"></i>
        <i style="--speed: 40; --bg: purple" class="square"></i>
        <i style="--speed: 21; --bg: green" class="rectangle"></i>
        <i style="--speed: 14; --bg: white" class="square"></i>
        <i style="--speed: 38; --bg: green" class="rectangle"></i>
        <i style="--speed: 19; --bg: red" class="dodecagram"></i>
        <i style="--speed: 29; --bg: pink" class="wavy-line"> </i>
        <i style="--speed: 21; --bg: white" class="hexagram"></i>
        <i style="--speed: 17; --bg: purple" class="wavy-line"></i>
        <i style="--speed: 32; --bg: yellow" class="pentagram"></i>
        <i style="--speed: 23; --bg: white" class="square"></i>
        <i style="--speed: 18; --bg: green" class="rectangle"></i>
        <i style="--speed: 37; --bg: red" class="dodecagram"></i>
        <i style="--speed: 48; --bg: pink" class="wavy-line"> </i>
        <i style="--speed: 38; --bg: pink" class="rectangle"></i>
        <i style="--speed: 13; --bg: red" class="pentagram"></i>
        <i style="--speed: 49; --bg: yellow" class="dodecagram"></i>
        <i style="--speed: 19; --bg: cyan" class="wavy-line"></i>
        <i style="--speed: 15; --bg: steelblue" class="square"></i>
        <i style="--speed: 10; --bg: yellow" class="square"></i>
        <i style="--speed: 18; --bg: white" class="pentagram"></i>
        <i style="--speed: 29; --bg: green" class="rectangle"></i>
        <i style="--speed: 17; --bg: blue" class="hexagram"></i>
        <i style="--speed: 33; --bg: red" class="pentagram"></i>
        <i style="--speed: 26; --bg: yellow" class="dodecagram"></i>
        <i style="--speed: 24; --bg: pink" class="wavy-line"> </i>
        <i style="--speed: 5; --bg: white" class="wavy-line"></i>
        <i style="--speed: 40; --bg: purple" class="square"></i>
        <i style="--speed: 17; --bg: green" class="rectangle"></i>
        <i style="--speed: 25; --bg: white" class="square"></i>
        <i style="--speed: 18; --bg: green" class="rectangle"></i>
        <i style="--speed: 15; --bg: cyan" class="wavy-line"> </i>
        <i style="--speed: 32; --bg: yellow" class="pentagram"></i>
        <i style="--speed: 45; --bg: white" class="square"></i>
        <i style="--speed: 18; --bg: green" class="rectangle"></i>
        <i style="--speed: 37; --bg: red" class="dodecagram"></i>
        <i style="--speed: 23; --bg: pink" class="wavy-line"> </i>
        <i style="--speed: 37; --bg: red" class="dodecagram"></i>
        <i style="--speed: 37; --bg: pink" class="wavy-line"> </i>
        <i style="--speed: 26; --bg: white" class="hexagram"></i>
        <i style="--speed: 32; --bg: cyan" class="wavy-line"></i>
        <i style="--speed: 32; --bg: yellow" class="pentagram"></i>
        <i style="--speed: 45; --bg: white" class="square"></i>
        <i style="--speed: 18; --bg: green" class="rectangle"></i>
        <i style="--speed: 37; --bg: red" class="dodecagram"></i>
        <i style="--speed: 23; --bg: pink" class="wavy-line"> </i>
        <i style="--speed: 50; --bg: pink" class="rectangle"></i>
        <i style="--speed: 30; --bg: red" class="square"></i>
        <i style="--speed: 18; --bg: red" class="pentagram"></i>
        <i style="--speed: 19; --bg: green" class="rectangle"></i>
        <i style="--speed: 16; --bg: blue" class="hexagram"></i>
        <i style="--speed: 23; --bg: red" class="pentagram"></i>
        <i style="--speed: 33; --bg: yellow" class="dodecagram"></i>
        <i style="--speed: 39; --bg: white" class="wavy-line"></i>
        <i style="--speed: 40; --bg: orange" class="square"></i>
        <i style="--speed: 21; --bg: green" class="rectangle"></i>
        <i style="--speed: 14; --bg: white" class="square"></i>
        <i style="--speed: 38; --bg: green" class="rectangle"></i>
        <i style="--speed: 19; --bg: red" class="dodecagram"></i>
        <i style="--speed: 29; --bg: pink" class="wavy-line"> </i>
        <i style="--speed: 34; --bg: white" class="hexagram"></i>
        <i style="--speed: 17; --bg: indigo" class="wavy-line"></i>
        <i style="--speed: 32; --bg: yellow" class="pentagram"></i>
        <i style="--speed: 23; --bg: white" class="square"></i>
        <i style="--speed: 18; --bg: green" class="rectangle"></i>
        <i style="--speed: 37; --bg: red" class="dodecagram"></i>
        <i style="--speed: 48; --bg: pink" class="wavy-line"> </i>
        <i style="--speed: 38; --bg: pink" class="rectangle"></i>
        <i style="--speed: 13; --bg: red" class="pentagram"></i>
        <i style="--speed: 49; --bg: yellow" class="dodecagram"></i>
        <i style="--speed: 19; --bg: purple" class="wavy-line"></i>
        <i style="--speed: 15; --bg: cyan" class="square"></i>
    </div>
</div>

```

你可以在下面的代码栏中看到我们的 CSS 五彩纸屑效果的最终结果:

见 [CodePen](https://codepen.io) 上 ONU orah Bonaventure chuk wudi([@ bona rhyme](https://codepen.io/bonarhyme))
的笔 [CSS 纸屑](https://codepen.io/bonarhyme/pen/ExLZgdE)。

奖金风格

## 我们还可以为用户提供一个暂停五彩纸屑的选项。添加以下样式，以便我们可以在单击复选框时停止动画:

您还可以使用以下样式指定五彩纸屑的尺寸:

```
input:checked + .confetti-container i {
  /* display: none; */
  animation-play-state: paused;
}

```

***注意:**，添加一个显示`none`从屏幕上移除纸屑。*

```
.confetti {
  position: fixed;
  left: 0;
  right: 0;
  display: flex;
  width: 600px;
  height: 600px;
  overflow: hidden;
}

```

具有这些额外风格的完整代码可以在 CodePen 和 GitHub 上查看、编辑和分叉:

请看 [CodePen](https://codepen.io) 上 ONU orah Bonaventure chuk wudi([@ bona rhyme](https://codepen.io/bonarhyme))
的带奖金风格的 CSS 纸屑。

结论

虽然这看起来令人生畏，但我们能够用 CSS 创建一个很酷的五彩纸屑效果！这一切都是为了表明 CSS 不应该看起来可怕，你可以利用它的超能力来创建惊人的动画和布局。

## 感谢您通读。我希望你喜欢这篇文章，如果你有任何问题，一定要留下评论。编码快乐！

你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

## .

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

[LogRocket](https://lp.logrocket.com/blg/css-signup) is like a DVR for web and mobile apps, recording everything that happens in your web app or site. Instead of guessing why problems happen, you can aggregate and report on key frontend performance metrics, replay user sessions along with application state, log network requests, and automatically surface all errors.

Modernize how you debug web and mobile apps — [Start monitoring for free](https://lp.logrocket.com/blg/css-signup).