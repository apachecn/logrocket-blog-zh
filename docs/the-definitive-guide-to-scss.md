# SCSS 的权威指南

> 原文：<https://blog.logrocket.com/the-definitive-guide-to-scss/>

最初，前端开发都是关于编写 HTML、CSS 和 JavaScript。然而，在过去十年左右的时间里，它变得越来越复杂，越来越有趣。该领域的不断发展使得掌握最新的前端技术和学习如何更有效地建立网站变得越来越重要。

在本指南中，我们将演示如何使用 SCSS——把它想象成一种用超能力编写 CSS 的方式。我把它分成三部分:

1.  SCSS 简介
2.  SCSS 语言语法
3.  在实际应用中使用 SCSS

如果你已经知道了这门语言，只是想学习如何在你的应用程序中使用它，可以直接跳到第三部分。

## 1.SCSS 介绍(和萨斯)

官方描述为“[具有超能力的 CSS](https://sass-lang.com/)”，SCSS(或 Sass)提供了一种用更增强的 CSS 语法为网站编写样式的方法。一般来说，浏览器不知道如何处理 SCSS 特性，比如函数、混合和嵌套。我们需要将它们转换成常规的 CSS 文件，以便在浏览器中运行。

### SCSS 和萨斯有什么区别？

这是大多数初学者感到困惑的地方。SCSS 和萨斯是同一概念的两种语法风格。[两者的区别是 UI](https://stackoverflow.com/questions/5654447/whats-the-difference-between-scss-and-sass/5732683#5732683) 。

这里有一个 SCSS 的例子:

下面是一个 Sass 的例子。

```
body {
  background-color:#000;
  color:#fff;
}

```

由你选择一个。不要强调决策——您可以使用`sass-convert`工具轻松地将 SCSS 转换为 Sass，反之亦然。

```
body
  background-color:#000;
  color:#fff;

```

在这篇文章中，我将集中讨论 SCSS，主要有两个原因。

SCSS 语法类似于 CSS，所以对于已经了解 CSS 的人来说，解释这些进步是很容易的

1.  一旦你了解了 SCSS，只需几分钟就能学会 Sass
2.  为什么是 SCSS？

### TL；DR: CSS 不够用。

不要误解我:CSS 很酷，但还不够酷。SCSS 的创造者有很多想法让开发者的生活更容易，并决定将它们构建成一种全新的语言。实际上，说清楚一点，Sass 是这种新语言的原始版本；SCSS 是他们后来创造的改进版本。

如何在你的网站中使用 SCSS

### 不幸的是，SCSS 的特性还没有在 [CSS 规范](https://www.w3.org/Style/CSS/specs.en.html)中引入，因此不被浏览器支持。要在 web 浏览器中运行 SCSS 代码，必须先将其转换为 CSS。我们将讨论您可以为此使用的工具，以及稍后如何自动化该过程。

2.SCSS 语言语法

## 对于本节，我假设您已经有了一些 CSS 经验。我将逐一介绍 SCSS 的特性，并解释每个特性的语法。

SCSS 规则的结构

### SCSS 的结构遵循 CSS 的结构。首先，使用 id、类或其他 [CSS 选择器](https://www.w3schools.com/cssref/css_selectors.asp)选择一个或多个元素。然后，添加样式。

在这个例子中，我们选择了带有`button`类的元素，并添加了一些属性。这是有效的 CSS 代码以及 s CSS 代码。值得注意的是，SCSS 支持所有的 CSS 属性。

SCSS 的评论

```
.button {
  display:inline-block;
  font-size:14px;
  padding:4px 10px;
}

```

### 在 SCSS，`//`(单行)和`/* */`(多行)注释都是允许的。

现在让我们来看看 SCSS 的基本增强功能。我们将在最后一节讨论如何将 SCSS 编译成 CSS。现在，您可以使用 [CodePen](https://codepen.io/) 来编写和测试您的代码。确保在 CSS 设置中选择 SCSS 作为预处理程序。

```
// this is a single-line comment

/* this is a multi-line comment */

```

对于下面的例子，我将使用一个简单的轶事，其中你正在使用 SCSS，而你的超级酷的老派开发者爷爷正在与 CSS 斗争。我们开始吧！

嵌套

### 你爷爷用 CSS 把他的导航条设计成这样:

但是你可以对 SCSS 这样做，就像这样:

```
nav {
  background-color:#333;
  padding:1em;
}
nav ul {
  margin:0;
  padding:0;
  list-style:none;
}
nav ul li {
  display:inline-block;
}

```

更有条理和简洁，对不对？你比你爷爷有两大优势:

```
nav {
  background-color:#333;
  padding:1em;
  ul {
    margin:0;
    padding:0;
    list-style:none;
    li {
      display:inline-block;
    }
  }
}

```

你知道`nav`及其子体的所有样式都在花括号之间，所以你不需要在文件或多个文件中找到它们

1.  您只需编写更少的代码，因此没有重复
2.  但是，请记住，当您深入嵌套时，CSS 文件可能会变得更大，浏览器需要做更多的工作来设置元素的样式。尽量让选择器浅一些。例如，我们可以通过将`li`的样式放到外部作用域来节省几个字节。

对于较大的项目，这可以节省大量的带宽。

```
nav {
  background-color:#333;
  padding:1em;
  ul {
    margin:0;
    padding:0;
    list-style:none;
  }
  li {
    display:inline-block;
  }
}

```

确保这个过程不会产生任何冲突是很重要的。例如:

在这种情况下，我们不能将`li`元素的样式带到外部范围，因为它们有不同的规则。

```
content {
  ul {
    li {
      font-style:italic;
    }
  }
  ol {
    li {
      font-decoration:underline;
    }
  }
}

```

底线是:嵌套使代码更加清晰、有组织、简洁，但是注意不要过度使用它。

使用 *`&`* 进行套料

### 你爷爷展示了他的神奇按钮，当你悬停在它上面时，它会改变颜色。他的 CSS 代码如下所示:

通过在嵌套中使用`&`字符，你可以更容易地用 SCSS 达到同样的效果。

```
button {
  background-color: #535353;
  color: #000;
}
button:hover {
  background-color: #000;
  color: #fff;
}

```

`&`总指上位选择。下面是一些使用案例。

```
button {
  background-color: #535353;
  color: #000;
  &:hover {
    background-color: #000;
    color: #fff;
  }
}

```

变量

```
.some-class {
  &:hover {
    /* when hovered */
  }
  &:focus {
    /* when focused */
  }
  & > button {
    /* selector equls to .some-class > button */
  }
  &-cool {
    /*** Notice this! ****/
    // selects .some-class-cool elements
  }
}

```

### 变量存储数据。在 SCSS，你可以在变量中保存任何 [CSS 值](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Values_and_Units)(即使有单位)。使用`$`符号定义变量。

变量声明:

变量用法:

```
$my-font: Nunito, sans-serif;
$my-font-color: #ffd969;
$content-width: 660px;

```

当 Sass 转换为 CSS 时，所有变量都被替换为它们的原始值。SCSS 变量有助于保持网站或 web 应用程序中字体、颜色和其他值的一致性。

```
body {
  font-family: $my-font;
  color: $my-font-color;
  content {
    width: $content-width;
  }
} 

```

SCSS 变量和 CSS 变量的区别

### 当转换成 CSS 变量时，SCSS 变量被替换为值。这些转换发生在它们被提供给浏览器之前，所以浏览器甚至不知道最初有变量，它们只是看到值。另一方面， [CSS 变量](https://blog.logrocket.com/how-to-create-better-themes-with-css-variables-5a3744105c74/)要强大得多。

[CSS-Tricks](https://css-tricks.com/difference-between-types-of-css-variables/#) 概述了 CSS 变量的几个优点:

他们级联；您可以在任何选择器中设置变量，以设置或覆盖其当前值

*   当变量值改变时(例如，媒体查询或另一个状态)，浏览器根据需要重新绘制
*   您可以在 JavaScript 中访问和操作变量
*   那意味着爷爷赢了吗？不完全是这样:你可以在 SCSS 样式表中使用 CSS 变量，因为任何有效的 CSS 也是有效的 SCSS。如果你对 CSS 变量很好奇，可以在这里了解更多[。](https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_custom_properties)

SCSS 可变范围

### 一些需要记住的事情:

顶层中定义的所有变量都是全局变量

*   块中定义的所有变量(即花括号内的变量)都是局部变量
*   块可以访问局部和全局变量
*   更改 SCSS 变量的值

```
$my-global-variable: "I'm global";
div {
$my-local-variables: "I'm local";
}

```

### 更改值的方式与声明值的方式相同。当您更改变量时，后续使用将具有新值，而之前的使用保持不变。

这里，`.content`将有背景色`#fefefe`，而`.footer`将有`#939393`。除非我们加上`!global`修饰符，否则全局变量不会改变。

```
$color: #fefefe;
.content {
  background-color: $color;
}

$color: #939393;
.footer {
  background-color: $color;
}

```

混合蛋白

```
$color: #111;
.content {
  $color: #222; // this is a new local variable
  background-color: $color; # 222
}
.footer {
  $color: #333 !global; // changes the global variable  
}

```

### 让我们再次超越你祖父的 CSS，这次是用 mixins。

mixin 是一组可以重用的 CSS 声明。语法类似于 JavaScript 中的[函数。使用`@mixin`指令代替`function`关键字。你也可以有争论。调用 mixin 是通过`@include`语句完成的。](https://blog.logrocket.com/defining-functions-in-javascript/)

下面是如何使用 mixins 将元素定位到绝对中心:

首先，我们定义了`absolute-center` mixin。然后，我们在多个块中使用它。

```
@mixin absolute-center() {
  position:absolute;
  left:50%;
  top:50%;
  transform:translate(-50%,-50%);
}
.element-1 {
  @include absolute-center();
}
.element-2 {
  @include absolute-center();
  color:blue;
}

```

带参数的混合

### 下面是一个如何在 mixins 中使用参数的例子。

带有可选参数的混合

```
@mixin square($size) {
  width:$size;
  height:$size;
}
div {
  @include square(60px);
  background-color:#000;
}

```

### 可选参数可以用我们声明 SCSS 变量的相同方式来定义。

包含内容块的混合

```
@mixin square($width: 40px) {
  width:$size;
  height:$size;
}

```

### 我们可以向 mixins 发送 CSS 规则，而不是参数。这些规则可以在 mixin 中使用`@content`。

这种方法允许我们减少`&:not([disabled]):hover`部分的重复。

```
@mixin hover-not-disabled {
  &:not([disabled]):hover {
    @content;
  }
}
.button {
  border: 1px solid black;
  @include hover-not-disabled {
    border-color: blue;
  }
}

```

进口 SCSS ( `@import`和`@use`)

### 创建大型应用程序时，代码分块是一个重要的实践。您的爷爷可以通过创建多个 CSS 文件并将其全部添加到 HTML 文档中来完成此操作。

Chunking code is an important practice when creating larger apps. Your grandpa can do this by creating multiple CSS files and adding them all to the HTML document.

```
<link rel="stylesheet" href="/path/to/css/1"></link>
<link rel="stylesheet" href="/path/to/css/2"></link> 
<link rel="stylesheet" href="/path/to/css/3"></link> 
<link rel="stylesheet" href="/path/to/css/4"></link> 
<link rel="stylesheet" href="/path/to/css/5"></link>

```

爷爷的过程繁琐。它需要浏览器进行许多 HTTP 请求，这可能会降低他的网站的速度。

SCSS 要好得多，因为它能让你在把代码发送到浏览器之前合并成块的文件。这样，你只需要链接一个 CSS 文件(通常命名为`something.bundle.css`)。

`@import`

### 下面的例子演示了如何使用`@import`将文件分块并导入到一个父文件中。

`normalize.scss`:

`styles.scss`:

```
body {
  padding:0;
  margin:0;
}
body, html {
  width:100%;
  min-height:100%;
}

```

假设`normalize.scss`和`styles.scss`都在同一个文件夹中，我们可以将一个导入到另一个，如上图所示。

```
@import 'normalize';

content {
  max-width:660px;
  // and more styles
}

```

使用`@import`时，所有变量、mixins 等。变得全局可访问，当您有复杂的文件结构和使用库时，这是一个问题。由于这个原因，[使用`@import`现在被官方劝阻](https://sass-lang.com/documentation/at-rules/import)。

解决方法是`@use`。

`@use`

### `@use`的基本用法与`@import`相同。

`styles.scss`:

用`@use`导入的文件称为模块。要使用这些模块的混合或变量，我们必须使用名称空间来调用它们。默认情况下，名称空间是文件名(不带扩展名)。

```
@use 'normalize';

// other styles

```

`src/_colors.scss`:

`styles.scss`:

```
$accent-color: #535353;
@mixin dark-background {
  background-color:#000;
  color:#fff;
}

```

您也可以使用`as`来使用自定义名称空间。

```
@use 'src/colors';
body {
  color: colors.$accent-color;
}
.dark-region {
  @include colors.dark-background;
}

```

当`_`被加在 SCSS 文件的文件名前面时，解析器知道它是一个部分文件 *，它只用于导入。导入时，`_`部分是可选的。注意，我们使用了`src/colors`来导入`src/_colors.scss`。*

```
@use 'src/colors' as c;
body  {
  color: c.$accent-color;
}

```

算术运算符

### 没有爷爷的 CSS `calc()`函数，你也可以在 SCSS 做一些数学运算。您可以使用`+,-,/,*,%`、直接值和变量进行计算。

流量控制规则

```
$content-width: 600px;
content {
  width:$content-width;
}
.inner-content {
  width: $content-width - 60px; // substraction
}
.outer-content {
  width: $content-width + 60px; // addition
}

```

### 流量控制规则有四种:`@if` / `@else`、`@each`、`@for`和`@while`。

`@if`和`@else`——类似于 JavaScript 中的`if`和`else`。

`@each`类似于 JavaScript 中的`for of`。

```
// ex: using in mixins
@mixin theme($is-dark: false) {
  @if $is-dark {
    // styles for dark
  }
  @else {
    // styles for light
  }
}

```

注意:`#{$size}`符号用于使用变量创建动态属性名和选择器。这叫做[插值](https://sass-lang.com/documentation/interpolation)。

```
// creating automated 
$sizes: 40px, 50px, 80px;
@each $size in $sizes {
  .icon-#{$size} {
    font-size: $size;
    height: $size;
    width: $size;
  }
}

```

`@for`类似于 JavaScript 中的`for`循环。

`@while`(不常使用)类似于 JavaScript 中的`while`循环。

```
@for $i from 1 through 4 {
  .bubble-#{$i} {
    transition-delay: .3 * $i;
  }
}

```

至此，您已经了解了 SCSS 的定义和历史，它是如何工作的，SCSS 和萨斯之间的区别，以及 SCSS 语言的语法。现在是时候实际创造一些东西来证明你的 SCSS 比你爷爷的 CSS 更好。

3.在实际应用中使用 SCSS

## 在这一部分中，我们将演示两种将 Sass 编译成 CSS 的方法。

在开始之前，我们必须安装`sass`命令行，这是预处理 CSS 最简单的工具。如果您使用 npm，请使用下面的代码。

查看[安装指南](https://sass-lang.com/install)，了解安装`sass`命令行的其他方法。

```
npm i -g sass

```

使用`sass`命令行，您可以将`.scss`和`.sass`文件解析成`.css`。它通过扩展名自动检测 SCSS 和 Sass 文件，并使用正确的解析器。

用 SCSS 设计一个简单的博客页面

```
sass source.scss destination.css

```

### 为了演示`sass`命令行并回顾上面概述的概念，让我们设计一个只有一个组件的简单博客:内容(为了更清楚起见，我们将跳过页眉和页脚)。

首先，在您的本地机器上创建一个文件夹(我将其命名为`my-blog`)并导航到那里。

然后，再创建两个文件夹。

```
cd /path/to/my-blog

```

我们将把`.scss`文件放在`source`文件夹中，预处理过的`.css`文件放在`build`文件夹中。

```
mkdir source build

```

接下来，在根目录下创建一个`index.html`文件，在您喜欢的文本编辑器中打开它，并添加一些 HTML 代码。

现在是时候创建我们新的 SCSS 文件了。我们的主文件会是`source/index.scss`，它会包含其他文件。

```
<!-- index.html -->
<html>
<head>
    <link rel="stylesheet" href="build/index.css">
</head>
<body>
    <content>
        <content>
            Blog content goes here
        </content>
    </content>
</body>
</html>

```

为了开始编写 SCSS，我们将设置一个观察器来实时地将`source/index.scss`编译成`build/index.css`。使用`sass`命令行中的`--watch`选项。

最后，创建分块的 SCSS 文件。

```
sass --watch source/index.scss build/index.css

```

为了便于识别，我将组件的样式文件命名为`Content.scss`格式。

```
// index.scss
@use 'normalize';

@use 'Content';
@use 'Footer';

body {
    background-color:#fafafa;
    font-family: Segoe UI, sans-serif;
}

// _normalize.scss
body, html {
    margin:0;
    padding:0;
}
* {
    box-sizing:border-box;
}
content {
    display:block;
}

// _colors.scss
$content-box: #fff;
$footer: #222;
$footer-font: #fff; 

```

现在我们已经创建了一个基本的博客页面，让我们添加一些媒体查询。

```
// Content.scss
@use 'colors' as colors;
body > content {
    padding:40px;
    margin:auto;
    width:660px;
    max-width:100%;
    & > content {
        background-color: colors.$content-box;
        min-height:600px; // to look cool
        border-radius:20px;
        box-shadow:10px 10px 40px rgba(0,0,0,0.06);
        padding:40px;
    }
}

```

添加媒体查询

### 假设我们需要删除移动设备(`<600px`)中父`<content>`元素的填充。我们可以用两种方法做到这一点。

传统的 CSS 方式是全局添加媒体查询。

SCSS 的方法是在选择器中添加媒体查询。

```
@media screen and (max-width:600px) {
  body > content {
    padding:0;
  }
}

```

两种方法都有效；你可以用你喜欢的任何一个。一些开发者将所有媒体查询保存在单个文件中(例如，`Mobile.scss`)。

```
body > content {
  @media screen and (max-width:600px) {
      padding:0;
  }
}

```

SCSS 与网络包或吞咽

### 虽然`sass`命令行简单易用，但如果你正在使用像 [webpack](https://webpack.js.org/) 或 [Gulp](http://zetcode.com/gulp/sass/) 这样的捆绑器，你可以使用插件来解析 SCSS 到 CSS 的捆绑。查看每个捆扎机的以下指南。bundler 的一个优点是，您可以使用它同时进行解析、自动前缀和缩小。

结论

## 现在，您应该了解了 SCSS 的基本知识以及它在现实世界中的应用。我们已经讨论了 SCSS 最广泛使用的特性，但是我鼓励你浏览一下 [SASS 文档](https://sass-lang.com/documentation)以获得更深入的了解。一旦你熟悉了 SCSS，你可能想看看[内置的 SCSS 模块](https://sass-lang.com/documentation/modules)，它可以让你在创建复杂的应用程序时更加轻松。

至于你超级酷的开发者爷爷——告诉他跟上时代，转到 SCSS 去。

你的前端是否占用了用户的 CPU？

## 随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。

Modernize how you debug web and mobile apps — [Start monitoring for free](https://lp.logrocket.com/blg/css-signup).*