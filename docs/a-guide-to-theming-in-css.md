# CSS - LogRocket 博客主题化指南

> 原文：<https://blog.logrocket.com/a-guide-to-theming-in-css/>

多年来，CSS 一直在发展，以迎合现代 UI 设计不断增长的需求和灵活性要求。对高度可定制的应用程序的需求正在迅速增长，因此需要能够创建这种网站的开发人员。

主题化是一种能力，可以根据不同的上下文来设计我们网站的不同方面，同时仍然保持视觉吸引力。它可以像改变颜色和背景或字体大小和图标一样简单。在 CSS 中，我们可以通过在一个上下文中拼凑各种 CSS 变量(道具)来实现主题化(例如，黑色和白色)，以便更好地呈现应用程序。

在本教程中，我们将介绍如何使用 CSS 变量开发主题感知的应用程序。我们还将学习如何使用 CSS 变量和 JavaScript 来定制网站和应用程序。

## CSS 变量

一个 **CSS 变量**(也称为自定义属性)是一个变量，您可以为它分配一个 CSS 值，并在整个应用程序中重用它。

在旧版本的 CSS 中，为 CSS 属性定义不同的值并在需要的地方重复应用它们是很常见的。在下面的示例中，`a`元素被设置为**黑色(#000)** ，尽管其他元素 **(p)** 也需要使用相同的颜色值 **(#000)** ，但是每次您想要在另一个元素上使用颜色值 **(#000)** 时，都需要显式地键入颜色值。

```
a {
  color: #000;
}

p {
  color: #000;
}
```

使用 CSS 变量，我们只需要定义一次值，然后在需要的地方引用它。这等同于在任何编程语言中定义变量。在下面的例子中，我们在 CSS 文档的顶层声明了一个变量 **black** ，然后在需要的地方引用它。

```
:root {
   --black: #000;
}

a {
   color: var(--black);
}

p {
   color: var(--black);
}
```

> N/B:声明 CSS 变量时，语法是`**--**`，后面跟变量名。为了引用它，我们在我们希望分配给它的属性上调用`var(--variable-name)`。

## 遗产

CSS 变量的作用范围可以是应用程序中的某些组件，必要时可以在内部组件中被覆盖。

这是让 CSS 变量脱颖而出的一个特性，也是让它非常适合创建主题感知网站的原因。在下面的例子中，用两种不同的背景色定义了内部 div 和外部 div。首先，我们通过将`.outer-div`的值设置为黑色来定义`--some-color`。然后我们在`.inner-div`重新定义它，将它的值设置为白色，使它覆盖这个元素下的值。

第一个`.text-color`获取在`.outer-div`定义的值，因为那是它的父节点，而第二个`.text-color` div 从`.inner-div`获取它，因为那是该 div 的直接子节点。

切换主题

## 有了上面的插图，要创建一个主题化的网站，剩下的工作就是找出一种方法，根据当前的上下文交换各种元素的属性值。

为了更好地理解这个概念，我们来谈谈黑白主题。在非常基本的层面上，为了支持网站的黑白主题，背景和文本颜色必须交替使用以保持可读性。因此，当我们查看黑色主题时，文本颜色应该是白色，而当我们查看白色主题时，文本颜色应该是黑色。

下面这支笔展示了网站在白色主题下的样子:

The following pen shows what the website looks like on a white theme:

下面这支笔展示了网站在黑色主题下的样子:

The following pen shows what the website looks like on a black theme:

通常，在一个网站上，我们可能有一个开关，使我们能够在可用的主题之间切换。为了实时改变 CSS 变量，我们需要添加一点 JavaScript 来控制这个过程。

让我们创建一个具有深色和浅色主题的页面，并添加一些 JavaScript，以便在单击复选框时切换:

Let’s create a page with dark and light themes and add some JavaScript so that it switches whenever the checkbox is clicked:

在上面的 CSS 中，我们定义了两个站点范围的颜色集:

根包含了浅色主题所需的默认颜色，`“data-theme=dark”`包含了我们网站各个部分深色主题所需的所有颜色。

```
:root {
    --primary-color: #302AE6;
    --secondary-color: #536390;
    --font-color: #424242;
    --bg-color: #fff;
    --heading-color: #292922;
}

AND 

[data-theme="dark"] {
    --primary-color: #9A97F3;
    --secondary-color: #818cab;
    --font-color: #e1e1ff;
    --bg-color: #161625;
    --heading-color: #818cab;
}
```

我们还添加了一个切换框，用于在用户点击时切换主题。

接下来，我们添加一些 JavaScript 来实现交换:

我们是如何实现这一目标的:

```
const toggleSwitch = document.querySelector('.theme-switch input[type="checkbox"]');

function switchTheme(e) {
    if (e.target.checked) {
        document.documentElement.setAttribute('data-theme', 'dark');
    }
    else {
        document.documentElement.setAttribute('data-theme', 'light');
    }    
}

toggleSwitch.addEventListener('change', switchTheme, false);
```

我们用查询选择器选择复选框

*   我们创建一个函数，在选中时将应用程序的上下文更改为暗色，在未选中时更改为亮色
*   最后，我们添加一个事件监听器来监听动作
*   主题化网站的一个好处是能够存储用户的偏好，所以下次用户访问网站时，它会自动加载到那个上下文中。为此，我们将利用本地存储。

将下面几行添加到上面的代码片段中:

既然我们保存了用户偏好，我们需要在每次站点加载时检查它，这样我们就知道向用户显示哪个版本。将此代码片段添加到 Javascript 文档的顶层:

```
if (e.target.checked) {
    document.documentElement.setAttribute('data-theme', 'dark');
    localStorage.setItem('theme', 'dark'); //this will be set to dark
  }
else {
    document.documentElement.setAttribute('data-theme', 'light');
    localStorage.setItem('theme', 'light'); //this will be set to light
}
```

这里，我们检查主题偏好是否已经存储在本地存储中。根据存储的值，我们切换框(通过将其设置为 true 或 false)来交换所需的变量。

```
const currentTheme = localStorage.getItem('theme') ? localStorage.getItem('theme') : null;

if (currentTheme) {
    document.documentElement.setAttribute('data-theme', currentTheme);

    if (currentTheme === 'dark') {
        toggleSwitch.checked = true;
    }
}
```

结论

## 在本教程中，我们学习了主题化以及如何使用 CSS 变量创建一个基本版本的主题化网站。我们还学习了变量继承和局部作用域，以及如何使用基本的 JavaScript 将它们结合在一起。要了解更多关于 CSS 主题化的知识，请点击这里查看官方文档。

你的前端是否占用了用户的 CPU？

## 随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用情况、内存使用情况等感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。

Modernize how you debug web and mobile apps — [Start monitoring for free](https://lp.logrocket.com/blg/css-signup).