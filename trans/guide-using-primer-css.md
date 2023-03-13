# Primer.css 使用指南

> 原文：<https://blog.logrocket.com/guide-using-primer-css/>

Primer.css 是 GitHub 用来处理样式和组织 css 的内部框架。css 的主要目的是在整个网站上实施一致的风格。在本文中，我们将了解 Primer.css，如何使用它，以及它从 BEM 中获得了什么。在我们构建一个登陆页面并评估它与 BEM 的相似性之前，我们将从了解它的组成开始。

*向前跳转:*

## Primer.css 入门

我们可以通过运行`npm install @primer/css`通过 npm 或者 Yarn 安装 Primer.css。之后，设置 [Sass](https://blog.logrocket.com/a-beginners-guide-to-programming-for-css-with-sass/) ，用`@import "@primer/css/index.scss";`命令导入到我们的主 [SCSS](https://blog.logrocket.com/the-definitive-guide-to-scss/) 文件中。

类似地，你可以通过[这个链接](https://unpkg.com/@primer/css@20.8.0/dist/primer.css)下载缩小版并把它放在你的代码中。然而，对于本文，我们将使用 CDN 版本，所以确保创建一个名为`primercss-cdn`的新文件夹。然后，创建一个新的`index.html`文件，并将下面的链接添加到`head`标签中:

```
<link href="https://unpkg.com/@primer/[email protected]^20.2.4/dist/primer.css" rel="stylesheet" />

```

您的代码应该如下所示:

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <link
      href="https://unpkg.com/@primer/[email protected]^20.2.4/dist/primer.css"
      rel="stylesheet"
    />
    <title>Primer CSS cdn</title>
  </head>
  <body></body>
</html>

```

## Primer.css 关键概念

Primer.css 是一个奇妙的框架，它被分成了几个小节，涵盖了实用程序、组件和营销风格。然而，我们需要理解断点、间距和主题化是如何工作的，以便开始子部分。

### 断点和间距

像其他流行的 CSS 框架一样，Primer.css 使用了四个依赖于媒体查询中设置的属性的断点。断点为`sm`、`md`、`lg`和`xl`，代表最小宽度`544px`、`768px`、`1012px`和`1280px`。

要指定断点，可以使用类似于在中小屏幕上设置显示的语法。Primer.css 使用从`0-12`开始的标度，表示从`0`到`4px`、`8px`、`16px`、`24px`、`32px`、`40px`、`48px`、`64px`、`80px`、`96px`、`112px`和`128px`的值。要设置一个元素的间距，你可以这样指定你想要的轴:< `h2 class="px-1 py-1">Content</h2>`。

### 主题

如今，用户对于主题化有两个基本偏好。这些偏好是亮和暗模式。css 提供了一组数据属性，可以用来为你的网站设置主题。这些数据属性将被添加到整个页面的根标签< `html>`。下面是它们的样子:

| 主题 | 数据属性 |
| 光 | 

```
<html data-color-mode="light" data-light-theme="light"> <!-- Other tags go in here --> </html>
```

 |
| 黑暗 | 

```
<html data-color-mode="dark" data-dark-theme="dark"> <!-- Other tags go in here -->  </html>
```

 |
| 暗暗的 | 

```
<html data-color-mode="dark" data-dark-theme="dark_dimmed"> <!-- Other tags go in here --> </html>
```

 |
| 暗高对比度 | 

```
<html data-color-mode="dark" data-dark-theme="dark_high_contrast"> <!-- Other tags go in here --> </html>
```

 |

## 了解 Primer.css 实用程序

Primer.css 实用程序样式包括在我们的代码中最常用的`[colors](https://blog.logrocket.com/advanced-guide-setting-colors-css/)`、`[box-shadows](https://blog.logrocket.com/three-ways-style-css-box-shadow-effects/)`和 [CSS 网格](https://blog.logrocket.com/css-grid-guide/)。本质上，这些风格帮助我们在网站上保持更一致的风格。css 提供了一组我们可以使用的工具，我们将在下面讨论。

### 填料

填充用于指定元素的内部间距。css 提供了可以一起使用来设置填充的快捷键。例如，我们可以结合上面讨论的任何间距来设置元素所有边的填充，如下所示:`p-2`。类似地，我们可以使用以下类名来设置左、右、上、下、垂直和水平轴的填充:`pl-2`、`pr-2`、`pt-2`、`pb-2`、`py-2`和`px-2`。

Primer.css 专注于响应性，为了实现这一目标，Primer.css 团队提供了一组用于设置不同屏幕尺寸的填充的类。你所要做的就是将`sm`、`md`、`lg`或`xl`附在衬垫上。它应该看起来像这样:`p-sm-2 py-md-3 pt-lg-4`。

### 页边距和版式

通常，通过边距，我们可以设置一个元素和另一个元素之间的间距。除了用`m`替换`p`之外，类名看起来非常类似于填充。此外，只有当`block`元素具有设定的宽度时，类名`mx-auto`才用于居中`block`元素，而`mb-auto`、`ml-auto`、`mr-auto`和`mt-auto`用于将`block`元素移动到顶部、右侧、左侧和底部。

同样需要注意的是，我们可以用类似于`mr-n6 m-n2 my-n9 my-md-n7`的类名来设置负边距。秘诀是在字母间距数字前面加上字母`n`。

在这种情况下，版式包括用于设置文本对齐、字体大小、行高、断字、列表样式、印刷样式等的样式。你可以在[官方文档](https://primer.style/css/support/typography)的 Primer.css 中了解更多关于排版的知识。

### 文本对齐和字体工具

文本对齐实用程序可用于元素内文本和图像的右边、中间或左边，分别使用以下样式:`text-right`、`text-center`和`text-right`。标题和文字缩放实用程序用于指定文本或 SVG 的字体大小。

标题实用程序设置`font size`和`font-weight`以匹配默认标题样式。这些款式从`h1`到`h6`不等，前者最大，后者最小；

```
<p class="h4">I love primer.css</p>

```

字体标尺只设置字体大小，范围从`f1`到`f6`。这里有一个例子:

```
<p class="f5">I love my Life</p>

```

### 颜色；色彩；色调

Primer.css 有 13 种颜色可供选择，包括:`default`、`muted`、`subtle`、`accent`、`success`、`attention`、`severe`、`danger`、`open`、`closed`、`done`、`sponsors`、`on`。

设置颜色的 CSS 类大致分为四个部分:`text`、`background`、`border`和`link`。文本颜色的类名是这样写的:`color-fg-danger`。本质上，最后一个文本可以是上面列出的任何颜色，包括`success`、`muted`等等。

要使用`on`文字颜色，应该这样写:`color-fg-on-emphasis`。背景颜色类似于文字颜色，如`color-bg-subtle`。但是，它有另一个更深的颜色集，在类名上附加了`emphasis`。

同时，设置边框颜色的类名也类似于背景颜色的类名，如下:`color-border-open`、`color-border-closed-emphasis`、`color-border-sponsors`。

然而，`Links`有一点不同。可以允许`primary`、`secondary`、`muted`以及`hover`、`nested`等状态的分类。主链接可以这样写:

```
<a class="Link--primary" href="/home">Link--primary</a>

```

而次要链接可以这样写:

```
<a class="Link--secondary" href="/home">Link--secondary</a>

```

静音链接可以使用`Link--muted`类来指示。`on-hover` link 类用于使链接中使用的任何文本颜色在悬停时变成蓝色。它可以这样使用:

```
<a class="color-fg-secondary no-underline" href="#url">
 This is a link <span class="Link--onHover">Link--onHover</span>
</a>

```

最后，嵌套链接用于强调链接中的某些文本，如下所示:

```
<a class="color-fg-secondary no-underline" href="#url">
 This is a link <span class="Link">Link</span>
</a>

```

### 边界

用于设置边框的类名分为五个部分:设置默认边框、边框样式、边框半径、边框颜色和响应边框。为了设置默认的边界，我们可以使用以下任意一个类名，例如:`border`、`border-left`、`border-bottom`、`border-right`、`border-x`和`border-y`。同时，Primer.css 允许在边框上附加一个`0-3`的刻度，比如`border-2`。值得注意的是，下面的类名:`border-top-0`、`border-right-1`、`border-bottom-2`、`border-left-3`可以用来设置元素边缘的边框。

要设置元素的边框样式，`border`的类名必须总是像这样提供:`border dashed`。类似地，我们也可以使用`rounded`类名和`0-3`或者`circle`类名来提供元素的边界半径，使元素呈圆形。它可以这样使用:

```
<div class="border rounded-2>
 .border .rounded-2
</div>

```

或者:

```
<div class="border circle" style="width: 80px; height: 80px;">
.border .circle
</div>

```

最后，就像填充和边距一样，我们也可以在不同的断点上设置元素的边框。我们可以在上面提到的任何边框图案上做，比如:`border-sm`、`border-md`、`border-lg`、`border-xl`、`border-sm-2`、`border-sm-top`、`border-sm-bottom-3`。

### 框阴影和伸缩

Primer.css 提供了五个类名来控制元素的方框阴影，包括:`color-shadow-small`、`color-shadow-medium`、`color-shadow-large`、`color-shadow-extra-large`、`box-shadow-none`。

它可以按如下方式使用:

```
<div class="color-shadow-extra-large p-3">
  .color-shadow-extra-large
</div>

```

处理 CSS flexbox 的类名本质上反映了样式的作用。要使一个元素成为 flexbox，我们可以使用`d-flex`和`d-inline-flex`，它们代表:

```
{ display: flex; }

```

并且:

```
 { display: inline-flex; }

```

同时，您可以使用`flex-row`、`flex-row-reverse`、`flex-column`和`flex-column-reverse`类来设置伸缩方向。有趣的是，`flex-wrap`也有同样的命名惯例。这意味着您可以使用`flex-wrap`、`flex-nowrap`和`flex-wrap-reverse`来选择 flex 项溢出时是应该换行还是换行。

值得注意的是，在应用其他 flex 类之前，您应该已经将一个元素设为 flexbox，如下所示:

```
<div class="border d-flex flex-wrap">
  <span class="p-4 border">Item 1</span>
  <span class="p-4 border">Item 2</span>
  <span class="p-4 border">Item 3</span>
</div>

```

为了应用用于在 flex 项目周围和之间分配空间的`justify-content`样式，我们可以使用以下类名:`flex-justify-start`、`flex-justify-end`、`flex-justify-center`、`flex-justify-between`和`flex-justify-around`。

此外，您可以使用下面的类名来应用`align item`样式:`flex-justify-around`、`flex-items-end`、`flex-items-end`、`flex-items-baseline`和`flex-items-stretch`。

虽然我们可以使用上面的样式创建简单和复杂的布局，但有时我们可能希望对 flex 项目有更多的控制。`flex-1`类名指示一个 flex 项填充任何可用空间。`flex-auto`类可用于让一个 flex 项目填充开放空间，并在其内容改变时自动调整其大小。而`flex-grow-0`和`flex-shrink-0`可用于防止柔性项目的增长和收缩。

Primer.css 提供的其他类名用于调整单个 flex 项目的对齐方式:

```
.flex-content-start    { align-content: flex-start; }

.flex-content-end      { align-content: flex-end; }

.flex-content-center   { align-content: center; }

.flex-content-between  { align-content: space-between; }

.flex-content-around   { align-content: space-around; }

.flex-content-stretch  { align-content: stretch; }

```

要设置顺序:

```
.flex-order-1    { order: 1; }

.flex-order-2    { order: 2; }

.flex-order-none { order: inherit; }

```

我们甚至可以通过将任何断点附加到上述 flexbox 类名来添加和应用响应样式。它通常遵循两条规则:

*   `d-[sm or md or lg or xl]-[the property]`
*   `flex-[sm or md or lg or xl]-[the property]-[the behavior]`

一个很好的例子就是在一个特定的断点中为 flexbox 设置一个条目，就像这样:`d-md-flex`；或者只为断点指定两端对齐的内容样式:`flex-lg-justify-center`。

## 探索 Primer.css 组件

Primer.css 中的组件是已经构建的项目，无论它们放在网页的什么位置，看起来都一样。它们遵循的模式只有在您组合适当的修饰符和类名时才有效。

### `Buttons`

Primer.css 默认`button`组件可以通过将`btn`类添加到`button`元素中来创建，如下所示:

```
 <button class="btn" type="button">Default button>
```

本质上，我们可以向一个`button`添加更多的类来设置它的类型、状态和变化。可以用来指定按钮类型的类名有`btn-primary`、`btn-outline`和`btn-danger`。至于状态，我们可以使用`aria-disabled="true"`和`aria-selected="true"`属性来禁用或启用按钮。这些属性可以按如下方式使用:

```
 <button class="btn mr-2" type="button" aria-disabled="true">Disabled

```

此外，我们可以指定`btn-sm`或`btn-large`来设置按钮的大小:

```
  <button class="btn btn-large">Large button</a>

```

类似地，当我们为按钮设置`btn-block`类名并使用`btn-invisible`类名移除按钮周围的边框时，我们可以使按钮增长到其父按钮的宽度。

### `Alerts`

警报用于通知用户其操作的状态。在 Primer.css 中，可以通过向元素添加`flash`类名来创建警告。默认情况下，这将生成一个浅蓝色背景的警报。然而，我们可以通过添加`flash-warn`、`flash-error`或`flash-success`类来改变警报。

大多数情况下，警报是可以解除的，并且有一个`close`按钮。我们可以使用`flash-close js-flash-close`来适当地添加它，并为动作定位图标。要创建可忽略警报的用户界面，您的标记应该如下所示:

```
    <div class="flash mt-3 flash-error">

      Alert message

      <button

        class="flash-close js-flash-close"

        type="button"

        aria-label="Close"

      >

        Close

      </button>

    </div>

```

### `Box`和`Loaders`

Primer.css 中的`Box`是一个带有背景色、边框和圆角的容器。默认情况下，它没有额外的样式。要创建一个`Box`，我们可以使用`Box`类:

```
<div class="Box">

  Box Content

</div>

```

一个`Box`可以有标题、正文和页脚。下面的类名可以用来表示盒子内部元素的功能:`Box-header`、`Box-body`和`Box-footer`。我们可以用类名来强调一个`Box`的标题。

我们可以使用修改器来进一步增强我们`Box`的外观和感觉。`Box-header--blue`可以用来改变带有`Box-header`类名的元素的背景和边框颜色:

```
<div class="Box">

  <div class="Box-header Box-header--blue">

    <h3 class="Box-title">This is the title</h3>

  </div>

  <div class="Box-body">

  This is  Box body

  </div>

</div>

```

其他修饰符，如`Box--danger`和`Box-row--unread`可以设置到`Box`容器和`Box`项中。它们实际上分别给`Box`添加了一个红色边框，给`Box`项添加了一个左边界。

`Loaders`用来表示待定状态。它应该与其他组件一起使用，并且可以使用`AnimatedEllipsis`类名来触发:

```
<h2><span>Loading</span><span class="AnimatedEllipsis"></span></h2>

```

## 使用 Primer.css 构建登录页面

在本节中，我们将构建一个登录页面，用于演示如何组合 Primer.css 中可用的组件和实用程序。

添加容器

### 构建我们的登录页面的第一步是打开我们之前创建的`primercss-cdn/index.html`。然后，我们将创建一个`div`来包装我们的应用程序:

接下来，我们将添加我们的导航栏:

```
  <div style="max-width: 1200px" class="mx-auto py-1 px-2">

  <!-- Content will go here -->

  </div>

```

让我们来分解一下:

```
      <div>

        <nav class="UnderlineNav" aria-label="Nav bar">

          <div class="UnderlineNav-body">

            <a

              class="UnderlineNav-item app-link"

              href="#home"

              aria-current="page"

              onclick="handleClick(this)"

              >Home</a

            >

            <a

              class="UnderlineNav-item app-link"

              href="#about"

              onclick="handleClick(this)"

              >About</a

            >

            <a

              class="UnderlineNav-item app-link"

              href="#newsletter"

              onclick="handleClick(this)"

              >Newsletter</a

            >

          </div>

          <div class="UnderlineNav-actions">

            <a href="mailto:[email protected]" class="btn btn-sm">Reach Us</a>

          </div>

        </nav>

      </div>

```

我们的导航条是由一个包装了一个元素的`div`组成的。`nav`包含了一个`UnderlineNav`类，并用一个`UnderlineNav-body`和`UnderlineNav-actions`包装了两个`div`,这两个符号表示导航条的两个部分。

同时，`UnderlineNav-body`包含三个`a`元素，它们都有来自 Primer.css 的`UnderlineNav-item`类、`app-link`类和`aria-current="page"`，我们将使用它们来显示当前选中的项目。同样，每个条目都有一个`onClick`处理程序，我们将用 JavaScript 来处理它。

添加一个`hero`部分

### 之后，我们可以添加我们的`hero`部分:

让我们来分解一下:

```
     <div id="hero" class="py-8 blankslate">

        <div

          style="width: 90%; max-height: 200px"

          class="mx-auto border p-3 rounded-2"

        >

          <h1>I love Primer.css</h1>

          <p>Click on the buttons below to find out why:</p>

          <div class="d-flex flex-wrap px-3 my-4" style="gap: 0.5rem">

            <!-- Pop up example -->

            <div class="position-relative text-center">

              <div

                class="Popover position-relative app-popover"

                style="display: none"

              >

                <div

                  class="Popover-message Popover-message--bottom p-4 mx-auto mb-2 text-left Box color-shadow-large"

                >

                  <h4 class="mb-2">Yes, this is the heading</h4>

                  <p>You are not out of luck! This is the message</p>

                  <button

                    type="submit"

                    class="btn btn-outline mt-2 text-bold app-popover-btn"

                    onclick="handlePopover()"

                  >

                    Got it!

                  </button>

                </div>

              </div>

              <button class="btn btn-primary" onclick="handlePopover()">

                View Popup

              </button>

            </div>

            <!-- End Pop up example -->

            <!-- Loading example -->

            <div class="position-relative text-center">

              <div

                class="Popover position-relative app-popover2"

                style="display: none"

              >

                <div

                  class="Popover-message Popover-message--bottom p-4 mx-auto mb-2 text-left Box color-shadow-large"

                >

                  <h2>

                    <span>Loading</span

                    ><span class="AnimatedEllipsis mb-2"></span>

                  </h2>

                 <button

                    type="submit"

                    class="btn btn-outline mt-2 text-bold app-popover-btn"

                    onclick="handlePopover2()"

                  >

                    Got it!

                  </button>

                </div>

              </div>

              <button

                class="btn-mktg btn-small-mktg mx-3"

                type="button"

                onclick="handlePopover2()"

              >

                View Others

              </button>

            </div>

            <!-- End loading example -->

            <!-- Dialog Example -->

            <details

              class="details-reset details-overlay details-overlay-light"

            >

              <summary class="btn" aria-haspopup="dialog">Open dialog</summary>

              <details-dialog

                class="Box Box--overlay d-flex flex-column anim-fade-in fast"

              >

                <div class="Box-header">

                  <button

                    class="Box-btn-octicon btn-octicon float-right"

                    type="button"

                    aria-label="Close dialog"

                    data-close-dialog

                  >

                    <!-- <%= octicon "x" %> -->

                    <svg

                      class="octicon octicon-x"

                      viewBox="0 0 12 16"

                      version="1.1"

                      width="12"

                      height="16"

                      aria-hidden="true"

                    >

                      <path

                        fill-rule="evenodd"

                        d="M7.48 8l3.75 3.75-1.48 1.48L6 9.48l-3.75 3.75-1.48-1.48L4.52 8 .77 4.25l1.48-1.48L6 6.52l3.75-3.75 1.48 1.48L7.48 8z"

                      </path>

                    </svg>

                  </button>

                  <h3 class="Box-title">What do you think about this?</h3>

                </div>

                <div class="overflow-auto">

                  <div class="Box-body overflow-auto">

                    <p>

                      You see how cool this CSS framework is??

                      <br />

                      It is what it is!

                    </p>

                  </div>

                  <ul>

                    <li class="Box-row">

                      <img

                        class="avatar v-align-middle mr-2"

                        src="https://avatars.githubusercontent.com/bonarhyme?s=48"

                        alt="bonarhyme"

                        width="24"

                        height="24"

                      />

                      @bonarhyme

                    </li>

                    <li class="Box-row border-bottom">

                      <img

                        class="avatar v-align-middle mr-2"

                        src="https://avatars.githubusercontent.com/bonarhyme?s=48"

                        alt="bonarhyme"

                        width="24"

                        height="24"

                      />

                      @bonarhyme

                    </li>

                    <li class="Box-row border-bottom">

                      <img

                        class="avatar v-align-middle mr-2"

                        src="https://avatars.githubusercontent.com/bonarhyme?s=48"

                        alt="bonarhyme"

                        width="24"

                        height="24"

                      />

                      @bonarhyme

                    </li>

                  </ul>

                </div>

                <div class="Box-footer">

                  <button type="button" class="btn btn-block" data-close-dialog>

                    Close

                  </button>

                </div>

              </details-dialog>

            </details>

            <!-- End of Dialog example -->

          </div>

        </div>

      </div>

```

英雄页面由一个包含一个 ID 和类名的 T0 组成。它用以下样式和类名包装另一个`div`:

内部`div`包含一个`h1`、`p`和一个`div`，样式和类别如下:

```
  style="width: 90%; max-height: 200px"

  class="mx-auto border p-3 rounded-2"

```

在`div`中，我们有一个弹出、加载和对话框的例子。使用 Primer.css 创建弹出窗口的秘诀是将`position-relative text-center`类添加到您想要创建弹出窗口的元素中。然后，它应该有用于显示内容和触发弹出窗口的`div`和`button`子元素。对于我们的例子，我们的`button`应该具有以下属性:

```
 class="d-flex flex-wrap px-3 my-4"

 style="gap: 0.5rem"

```

包含内容的`div`应该具有以下属性:

```
 class="btn btn-primary"

 onclick="handlePopover()"

```

它还应该有一个内部的`div`类:`Popover-message Popover-message--bottom p-4 mx-auto mb-2 text-left Box color-shadow-large`，最终将包含内容。我们还可以在带`onclick="handlePopover()"`的`Popover-message`里面的一个`button`上加一个`onclick`。

```
  class="Popover position-relative app-popover"

  style="display: none"

```

创建截面

### 接下来，我们将把“关于我们”部分添加到我们的登录页面。关于部分包含一个`h2`，它实际上是一个[工具提示](https://blog.logrocket.com/controlling-tooltips-pop-up-menus-using-compound-components-in-react-ccedc15c7526/)。它还包括另一个带有彩色`timeline`的`div`，里面有四个物品。简单来说，这里是到 [GitHub repo](https://github.com/bonarhyme/chunks-code-holder/blob/main/primercss/about.html) 的链接。

第一个`timeline`项包含`breadcrumbs`。而第二、第三和第四时间线具有[截断的](https://blog.logrocket.com/ways-truncate-text-css/)链接、具有悬停状态样式的普通链接和`toast`组件。

接下来，我们有了`newsletter`部分，我们可以添加以下内容:

我们来分解一下。`newsletter`部分包含一个`h2`和一个`form`。`form`包含一个`label`、`input`和一个`button`。接下来，我们有页脚，如下所示:

```
     <div id="newsletter">

        <h2 class="py-2 h3-mktg">Subscribe to my Newsletter</h2>

        <form>

          <div class="form-group">

            <div class="form-group-header">

              <label for="example-text">Email address</label>

            </div>

            <div class="form-group-body">

              <input

                class="form-control"

                type="text"

                placeholder="Email address"

                id="example-text"

              />

            </div>

          </div>

          <button class="btn btn-block btn-primary" style="max-width: 440px">

            Submit

          </button>

        </form>

      </div>

```

最后，我们在代码中有处理`onclick`事件的`script`:

```
  <div>

    <p class="text-center py-4">

      Made by

      <a href="https://github.com/bonarhyme">bonarhyme</a>

    </p>

  </div>

```

Primer.css 从 BEM 身上拿了什么？

```
// Internal JavaScript written inside the body of HTML

    <script>

      function handleClick(e) {

        document.querySelectorAll('.app-link').forEach((x) => {

          x.removeAttribute('aria-current');

        });

        e.setAttribute('aria-current', 'page');

      }

      function handlePopover() {

        const popup = document.querySelector('.app-popover');

        if (popup.style.display === 'none') {

          popup.style.display = 'block';

        } else {

          popup.style.display = 'none';

        }

      }

      function handlePopover2() {

        const popup = document.querySelector('.app-popover2');

        if (popup.style.display === 'none') {

          popup.style.display = 'block';

        } else {

          popup.style.display = 'none';

        }

      }

    </script>

```

## Primer.css 具有独特的架构，使用面向对象的 css 编程技术，并受 BEM 模型的影响。BEM 代表块、元素和修改器。本质上，BEM 是一种编写 CSS 和构建 HTML 的方法，有助于创建更清晰、更容易在团队中共享的组件。要了解更多关于边界元法及其方法论的知识，请查看这篇文章。

通常，块是一个独立的实体或组件，它本身是独立的，有意义的。一个很好的例子就是菜单、导航、框等等。应该这样写:

一个元素是一个块的一部分，在语义上与它的块联系在一起。大多数情况下，它没有独立的意义，应该总是绑定到一个块。导航项、框标题和菜单项都是元素的很好的例子。它可以用双下划线写成，如下所示:

```
<div class=”card”>

<!-- elements and modifiers go here –>

</div>

```

或者像这样:

```
<div class="navigation__item">A navigation item</div>

```

顾名思义，修饰符用于改变块或元素的行为和外观。这意味着修改器可以直接绑定到块或元素。对块应用修改器的例子包括:`navigation disabled`、`box danger`、`button large`等。向元素添加修饰符的例子包括:`navigation item disabled`、`box header danger`等等。一般来说，修饰语应该这样写:

```
<div class="card__header">This is a card header</div>

```

此外，以卡为例编写 BEM 的语法如下:

```
<div class=”card__button–danger”>A card button that indicates an error that has occurred</div>

```

和 CSS:

```
 <section class="card">

      <h1 class="card__title">The Title</h1>

      <div class="card__imageContainer">

 <img src="https://images.unsplash.com/photo-1676136358787-6510632d3e30?ixlib=rb-4.0.3&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=387&q=80"

          alt="an amazing landscape"

          class="card__image"

        />

      </div>

      <div class="card__content">

       <p class="card__description">

          Lorem ipsum dolor sit amet consectetur adipiscing elit. Aperiam porro

          quidem iure natus consequatur. A dolorum quis mollitia veritatis

          nostrum excepturi eligendi impedit doloremque, autem non reiciendis

          nemo, error quidem.

        </p>

        <div class="card__buttonGroup">

          <button class="card__button">Proceed</button>

          <button class="card__button card__button--disabled">Cancel</button>

          <button class="card__button card__button--danger">Delete</button>

        </div>

      </div>

    </section>

```

Primer.css 有何不同

```
.card {

        width: 100%;

        max-width: 400px;

        margin-left: auto;

        margin-right: auto;

        border: 1px solid gray;

        padding: 0.8rem;

        border-radius: 0.5rem;

      }

      .card__title {

        color: rgb(31, 31, 31);

      }

      .card__imageContainer {

        width: 100%;

        height: 300px;

      }

      .card__image {

        width: 100%;

        height: 100%;

        object-fit: cover;

      }

      .card__buttonGroup {

        display: flex;

        gap: 0.5rem;

      }

      .card__button {

        padding: 0.4rem 0.8rem;

        cursor: pointer;

        outline: none;

        border-radius: 0.3rem;

        border: 1px solid;

      }

      .card__button--disabled {

        cursor: not-allowed;

        background-color: gray;

        border-color: gray;

      }

      .card__button--danger {

        background-color: rgb(231, 36, 36);

        color: white;

        border-color: rgb(231, 36, 36);

      }

```

### css 使用了与 BEM 相同的模式。但是，它使用连字符，而不是用来标识元素的双下划线。但是对于修饰语，它使用了一个[咏叹调](https://blog.logrocket.com/using-ariakit-components-react-apps/)来表示`button`组件，并使用了一个双连字符来表示一些其他组件，比如`Box`。

Primer.css 中的一个块包括`Dropdowns`、`Pagination`、`Popovers`和`menu`等组件。应该注意的是，一些块是大写的，而一些是小写的。使用 BEM，元素不应该是独立的，而应该在块内部。同样适用于 Primer.css 元素必须在组件内部才能正常工作。此外，修改器必须与块或元素结合才能正常工作。

BEM 方法确保我们的 CSS 代码更加结构化，并且通过强制执行更加一致的生成类名的模式，可以在团队的各个应用程序部分中轻松重用。Primer.css 使用这种特殊的方法来确保我们的应用程序 UI 是一致的。

结论

## Primer.css 的简单性使它成为构建下一个项目的绝佳选择，应该被采用，因为它附带了许多响应性非常好且非常简洁的组件和实用程序。感谢您通读。我希望你喜欢这篇文章，如果你有任何问题，一定要评论。编码快乐！

你的前端是否占用了用户的 CPU？

## 随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。

Modernize how you debug web and mobile apps — [Start monitoring for free](https://lp.logrocket.com/blg/css-signup).