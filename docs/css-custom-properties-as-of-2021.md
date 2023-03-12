# 截至 2021 年的 CSS 自定义属性

> 原文：<https://blog.logrocket.com/css-custom-properties-as-of-2021/>

许多 web 应用程序包含大量 CSS，这些 CSS 通常包含重复的值。例如，如果一种颜色在多个组件中多次使用，为了改变颜色，将启动全局搜索和替换。这既费时又令人望而生畏。

自定义属性允许开发人员将值存储在单个文件中，并在许多组件中引用该值。自定义属性也允许语义标识符，例如如果在多个组件中使用,`--main-button-color`将比`#99efbe`更容易理解。同样重要的是，要注意自定义属性受 CSS 级联规则的约束。
在本文中，我将简要概述 CSS 自定义属性以及如何使用它们，并讨论它们与 JavaScript、API 和 React 的功能。

## 什么是自定义属性？

自定义属性也称为 CSS 变量，是由开发人员定义的特定值，可在 web 应用程序或文档中重用。通常 CSS 变量是用属性符号来设置的。

下面是一个基本自定义属性的示例:

```
button {
   background-color: var(--main-bg-color);
}

```

在上面的代码块中，button 元素被赋予了背景色`main`，这已经在一个单独的文件中定义了。下面让我们看看如何使用自定义属性。

## 声明自定义属性

要声明一个定制属性，首先我们通过在一个有效的 CSS 值旁边添加一个双连字符`(--)`来声明属性名。

像任何其他变量一样，我们将它写在一个规则集内，如下所示:

```
element {
  --main-color: red;
}

```

要在属性声明中使用一个值，我们需要用一个类似下面代码块的`var()`函数来调用它:

```
p {
 color: var(--color-primary);
}

```

值得注意的是，赋予规则集的选择器定义了自定义属性的使用范围。另一个常见的用法是将自定义属性的范围定义为`:root`，它是全局的。这样做是为了让它可以在全球范围内应用于许多文档和组件。

## CSS 自定义属性的用例

在这一节中，我们将看看自定义属性和 CSS 变量的用例。

### 颜色；色彩；色调

颜色可以设置为 CSS 自定义属性。此示例将包括颜色更改的自定义属性的范围。

在我们的例子中，我们可以为一个特定的元素定义一种颜色`danger`红色，比如说`h2`。然而，该应用程序还为`<article>`导航栏中的字幕使用了一个`h2`元素。

我们可以在一个`<article>`中声明`--color-love`值。现在字幕中的任何`<h2>`都将是蓝色而不是红色:

```
:root {
  --color-danger: red;
}

h2 {
  color: var(--color-danger);
}

article {
  --color-love: #0000FF;
}

```

### 字体大小

字体大小是使用自定义属性的另一种方式，因为字体大小可以使用 CSS 作用域来更改。

这方面的一个例子可以用与上述例子相同的方式找到:

```
:root {
  --heading-size: 24px;
}

h2 {
  font-size: var(--heading-size);
}

article {
  --heading-size: 18px;
}

```

在上面的代码块中，全局标题大小被设置为`24px`。在`article`选择器中，我们将其更新为`18px`。

### 网格布局

网格布局是 web 开发中最现代的趋势之一。我们可以使用 CSS 自定义属性为网格设置自定义布局，如下面的代码块所示:

```
:root {
  --grid-width: 30%;
}

.grid-box {
  align-items: flex-end;
  width: var(--grid-width);
}

.product-grid {
  --grid-width: 35%;
}

```

### 小跟班

按钮是使用自定义 CSS 属性的好方法。这方面的一个例子是，一个按钮被全局设置样式，并可以用另一个自定义值更新，如下所示:

```
:root {
  --button-color: blue;
  --button-color-white: white;
  --button-color-danger: red;
}

.button {
  background-color: var(--button-color);
  box-shadow: 2px 4px 0 var(--button-color-dark);
}
.button:hover {
  background-color: var(--button-color-dark);
  box-shadow: 0 0 0 var(--button-color-dark);
}

.form {
  --button-color: #ff571c;
  --button-color-dark: #cc5600;
  --button-color-light: #ffb64f;
}

```

## 更改 JavaScript 状态

自定义 CSS 属性可以使用 JavaScript 实时更改。当构建用户启用的主题、更改或演示多个布局或者为用户更改配色方案时，这很好。

CSS 自定义属性可用于更改任何应用程序的状态，如下面的代码块:

```
const body = document.getElementByTagName("body")[0];
function change_vars(){
  if (!body.classsList.contains('changed') ) {
    body.style.setProperty('--color-primary', '#ff54ef');
    body.classList.add('changed');
  } else {
    body.style.setProperty('--color-primary', '#13efdc');
    body.classList.remove('changed');
  }
}

```

## 作为 API 的 CSS 自定义属性

CSS 自定义属性可以用作 API，特别是当它们作为 cdn 托管时。

让我们看看下面的代码块:

```
// today's special is 
.today::after {
  content: var(--todaysSpecial); 
}

```

上面的代码块可以用来喜欢一个 API，使它易于使用，但是这是有挑战性的，不太容易访问。

## 带有 React 的 CSS 自定义属性

CSS 自定义属性可以在 React 应用程序中使用，这可以为 React 开发人员打开新的可能性。使用 CSS 变量，你可以做很多 JavaScript 做不到的事情。

要在 React 应用程序中使用自定义属性，首先需要一个文件。在我们的例子中，`constants.js`，保存所有与下面相似的设计符号:

```
const SIZES = [
  4,
  8,
  12,
  16,
  20,
  24,
  28,
  32,
  36
];

const COLORS = {
  text: 'black',
  background: 'white',
  primary: 'purple',
  secondary: 'blue'
};

```

接下来，将文件直接导入到组件中，如下面的代码块所示:

```
import { COLORS } from '../constant';

const Button = styled.button`
  background: ${COLORS.secondary};
`;

```

有了 CSS 变量，我们可以对变量做更多的事情，而不是改变它们，我们只能在需要的地方改变值。

在下一节中，我们将讨论一个著名的库，它使开发人员能够更容易地在 React 应用程序上执行自定义属性。

## 反应自定义属性

[React 自定义属性](https://github.com/danbahrami/react-custom-properties)是一个应用 CSS 变量的 React 组件。使用 React 自定义属性，开发人员可以声明性地应用 CSS 自定义属性。

React 自定义属性可以使用节点程序包管理器通过以下命令进行安装:

```
npm install react-custom-properties

```

接下来，像这样导入`CustomProperties`:

```
import CustomProperties from 'react-custom-properties';

```

React Custom Properties 提供了一个`<CustomProperties/>`组件，该组件可用于在挂载时将传递给`properties`的 CSS 变量应用到其子节点。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

我们可以如下使用它:

```
import React, { Component } from 'react';
import CustomProperties from 'react-custom-properties';

class App extends Component {
  render() {
    return {
      <div>
         <CustomProperties properties={{ '--branding-color': '#000FFE' }}>
          <div className="navbar">
            this will have the background color #000FFE
          </div>
        </CustomProperties>
      </div>
    );
  }
}

```

React 自定义属性还支持嵌套，以便父实例可以被子实例覆盖。你可以在这里了解更多关于 React 自定义属性的信息。

## 结论

自定义 CSS 属性不仅可以帮助用户定义布局，还可以更新应用程序中的 CSS 值和变量。

虽然处于实验阶段，CSS 变量可以用作 API 和控制 JavaScript 状态。像 React 自定义属性这样的库使得在 React 应用程序中使用自定义变量变得更加容易。

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。