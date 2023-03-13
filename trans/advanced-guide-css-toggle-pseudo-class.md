# CSS toggle()伪类高级指南

> 原文：<https://blog.logrocket.com/advanced-guide-css-toggle-pseudo-class/>

2022 年 4 月中旬，Chromium 宣布它将开始与 CSS Toggles 兼容的原型开发，这是一个新的 CSS 功能，目前[只作为非官方草案](https://tabatkins.github.io/css-toggle/)提供。

CSS Toggles 是一种声明性机制，用于将“可切换值”与元素相关联，以及设置和修改该值。您还可以在 CSS 中的选择器中使用 CSS 切换来选择一个元素，以便设置样式或添加交互性。

草案中包含了`toggle()`，这是一个功能性的伪类，可以根据切换状态选择元素。在本文中，我们将探索目前对这个即将到来的 CSS 特性的了解，包括它令人兴奋的地方和它的一些潜在用例。

向前跳:

请注意，因为 CSS `toggle()`目前仍然是一个非官方的草案，我们对这个伪类的了解可能会改变。

## 为什么 CSS `toggle()`是一个令人兴奋的新特性

在`toggle()`之前，你只能利用内置的浏览器状态来设计元素或者增加交互性。例如，checkbox 输入有`:checked`，这是一个与表单输入相关的内置浏览器状态，一旦选中，您可以使用它来更改输入的外观。

使用`toggle()`，您可以在任何元素上创建自己的定制状态，并为不同的状态定义样式变化。该规范还附带了诸如`toggle-visibility`和`toggle-group`这样的属性，这两个属性对于构建标签和折叠这样的公开窗口小部件特别有用。

让我们看一些例子。

## 使用`toggle()`根据柠檬的切换状态选择柠檬

你可以在 CSS 中用`toggle-root`属性定义一个元素的状态，并描述该状态如何根据用户与`toggle-trigger`属性的交互而改变。然后，您可以使用`toggle()`伪类让元素响应状态变化。

考虑以下按钮:

```
<div>
    <button>TOGGLE ME!</button>
</div>

<div>
    <p>Demo for <em><a href="https://blog.logrocket.com" target"_top">https://blog.logrocket.com</a></em></p>
</div>

```

我们希望按钮有两种可切换状态——尽管你可以有两种以上的状态。每个状态都有一组与之关联的给定样式。

下面的例子展示了使用 CSS `toggle()`伪类实现这一点的简单方法。我们在 HTML 根元素中注册 toggle，将按钮定义为 toggle activator，然后描述当 toggle 切换时应该发生什么:

```
html {
  toggle-root: switch;
}

button {
  toggle-trigger: switch;
  background-color: black;
  color: white;
}

html:toggle(switch) button {
  background-color: white;
  color: black;
}

```

使用上面的 CSS，当通过单击按钮改变切换值时，我们只需将按钮的背景和文本颜色分别改为白色和黑色。下面是这种行为的演示:

请看 [CodePen](https://codepen.io) 上金斯利乌巴([@金斯利乌巴](https://codepen.io/kingsleyubah) )
的笔[拨动按钮演示](https://codepen.io/kingsleyubah/pen/ExRKGMm)。

如果你想让你的按钮看起来更动态，看看我们的指南[用 CSS](https://blog.logrocket.com/css-transitions-animating-hamburger-menu-button/) 制作按钮动画。

注意，一个元素既可以是切换根，也可以是切换触发器。在这种情况下，使用`toggle`速记属性。以下示例演示了这种情况:

```
li {
  toggle: check self
}

li:toggle(check) {
  color: silver;
  text-decoration: line-through;
}

```

这里，列表项同时是切换根和触发器。因此，当单击一个项目时，我们将文本颜色更改为银色，并在其中划一条线:

请看 [CodePen](https://codepen.io) 上金斯利乌巴([@金斯利乌巴](https://codepen.io/kingsleyubah) )
的笔[切换列表项目演示](https://codepen.io/kingsleyubah/pen/MWXyKpY)。

接下来，我们来看看切换可见性。

## 使用`toggle-visibility`切换元素的可见性

使用`toggle-visibility`属性，您可以构建不同种类的扩展器——[可折叠组件，如](https://blog.logrocket.com/create-collapsible-react-components-react-collapsed/)选项卡、折叠、弹出窗口以及详细或摘要小部件——而无需编写一行 JavaScript 代码。此属性允许元素自动将其显示绑定到特定切换的状态。

使用`toggle-visibility`也可以[提供额外的可访问性优势](https://blog.logrocket.com/a-guide-to-improving-web-accessibility-with-css/)，允许未呈现的内容在搜索或访问时具有页面查找、基于哈希的导航或 tab 键顺序等功能。

让我们考虑下面的描述列表:

```
<dl class="accordion">
  <dt>What is HTML?
  <dd>Hyper Text Markup Language is the standard markup language for documents intended to be viewed in a web browser
  <dt>What is CSS?
  <dd>
Cascading Style Sheets is used to describe the appearance of a document wtitten in HTML or some other markup language
</dl>

```

默认情况下，描述`<dd>`是隐藏的。我们只希望在相应的标题被点击时展开描述。下面是添加该效果的 CSS:

```
.accordion > dt {
  background-color: black;
  margin-bottom: 10px;
  padding: 5px 5px;
  width: 120px;
  color: white;
  toggle: show;
  }
  .accordion > dd {
    margin-bottom: 10px;
    toggle-visibility: toggle show;
  }

```

这是这种行为的演示:

请看 [CodePen](https://codepen.io) 上金斯利乌巴([@金斯利乌巴](https://codepen.io/kingsleyubah) )
的切换可见效果的笔[演示。](https://codepen.io/kingsleyubah/pen/qBKZZrM)

您还可以使用这个 CSS 伪类创建切换组。接下来让我们来看看。

## 使用`toggle-group`对互斥开关进行分组

如果有多个切换，可以使用 toggle-group 属性将它们组合在一起。请记住，该组中一次只能有一个切换处于活动状态。这类似于 HTML 单选按钮的行为，其中不能同时选中两个按钮。

考虑到这种行为，您可以使用切换组来描述具有多个互斥内容分支的模式，这意味着一次只能显示其中一个分支。两个例子是标签和[折叠或下拉菜单](https://blog.logrocket.com/making-dropdown-menus-css/)。

考虑下面的 HTML:

```
<div>
  <button>tab one</button>
  <p>tab one content</p>
  <button>tab two</button>
  <p>tab two content</p>
  <button>tab three</button>
  <p>tab three content</p>
</div>

```

假设在给定时间三个段落中只有一个要显示，我们可以使用切换来定义独占或分组行为:

```
div {
  /* Set group on a common ancestor */
  toggle-group: tab;
}

button {
  /* Creates sticky tabs and declares itself a toggle activator */  
  toggle: tab 1 group sticky;
}

button:first-of-type {
  /* The first tab is active by default */
  toggle: tab 1 at 1 group sticky;
}

button:toggle(tab) {
  /* Specify how you want the active
  tab/button to look */
}

p {
  /* Visibility of paragraph is 
  linked to toggle state */
  toggle-visibility: toggle tab;
}

```

在上面的 CSS 中，每个段落的可见性依赖于它的激活器——在这个例子中，是它各自的按钮——并且一次只能出现一个段落。默认情况下，第一段是可见的。

## CSS `toggle()`伪类的其他特性

除了我们提到的基本例子之外，草案还有很多内容。以下是一些需要记住的其他行为:

*   一个切换根可以包含多个切换
*   在一个切换中可能有多个活动状态
*   默认情况下，切换状态设置为非活动状态，但这可以被覆盖
*   你也可以用一组单词来表示状态，而不仅仅是数字
*   粘性切换总是活跃的，因为总是至少有一个活跃的项目
*   正在考虑带转换的状态机

再次提醒，请记住 CSS `toggle()`目前仍然只是一个非正式的草案。这些行为可能会随着时间的推移而更新或改变。

## 结论

当在 web 浏览器上完全实现时，CSS 开关对于 CSS 作者来说将是一个非常有用的工具。

这是一个非常强大的功能，无疑会使向 HTML 元素添加交互性的过程变得更加容易。当然，正确掌握整个语法需要时间，但这是值得的。

关于这项新功能，您有什么问题吗？请在下面的评论中告诉我。

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。