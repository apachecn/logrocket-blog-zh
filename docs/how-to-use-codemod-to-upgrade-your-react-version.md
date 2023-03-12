# 使用 Codemod 升级你的 React 版本

> 原文：<https://blog.logrocket.com/how-to-use-codemod-to-upgrade-your-react-version/>

重构大规模的代码库可能是混乱的、昂贵的和乏味的，这就是为什么许多工程团队认为根本不值得做，尤其是在处理一个不受欢迎但仍然“有效”的模式时

例如，一个不受欢迎的模式可能是您编写的一个函数，它最好被拆分成多个方法，一个带有几个可选参数的函数更容易使用，或者一个应该返回包含更多信息的结构而不是当前的布尔值的函数。

## codedom 简介

进入 [Codemod](https://github.com/facebook/codemod) ，这是一个为协助大规模代码库重构而构建的 Python 工具。它甚至可以部分自动化该过程，尽管人工监督和偶尔的手动干预仍然是必要的。

它由脸书的开发人员构建，作为开源软件发布，任何人都可以用它来以理智和系统的方式对成熟的代码库进行彻底的改变。

您可以使用您喜欢的 Python 包管理器安装 Codemod，如下所示:

```
pip install codemod

```

您还可以使用 sudo 在系统范围内安装该软件包:

```
sudo -H pip install codemod

```

让我们用一个简单的例子来演示 Codemod 的用处。假设我们不想使用`<font>`标签。我们将启动命令行并运行以下命令:

```
codemod -m -d /Users/janedoe/reponame --extensions php,html \ 
'<font color="?(.?)"?>(.*?)</font>' \ 
'<span style="color: \1;">\2</span>'
```

对于每个正则表达式匹配，一个彩色的 diff 将显示在终端上。然后会提示您接受更改(也就是说，用一个`<span>`标记替换那个`<font>`标记的实例)，拒绝它，或者在编辑器中编辑给定的行。

这样，使用 Codemod 重构代码库就像在您最喜欢的文字处理器中执行查找和替换一样。

## 案例研究:使用 Codemod 升级到较新的 React 版本

让我们假设你和你的工程师团队已经使用 React 构建了一个产品的前端。它工作得很好，运行得很平稳，并且涉众希望当前的产品保持原样，没有任何重大的中断。

当您开始构建该产品时，React 的最新版本是 16.5。但是现在您已经了解了[悬念](https://reactjs.org/docs/concurrent-mode-suspense.html)，这是一个简洁的组件，允许您“等待”一些特定的代码——包括数据、图像、脚本和其他异步加载的元素——来加载，同时，指定要显示的加载状态(例如，微调器或屏幕上的一些文本)。

这里有一个简单的例子来展示一个悬念用例:

```
const demoPage = React.lazy(() => import('./demoPage'));
// Display a spinner while the page is loading 
<Suspense fallback={<Spinner />}> <DemoPage /> </Suspense>
```

问题是，悬念只在 React 版本及更高版本中可用，而您的整个产品都是基于 16.5 版本构建的。更不用说，如果你用一个更新来打破一切，你的产品所有者和其他利益相关者将会非常愤怒。

所以，情况是没有希望的。你注定要永远使用旧版本，直到 React 发展到别无选择，只能彻底改造，投入时间和大量资源进行重构，对吗？

不对。我们可以使用 Codemod 来促进无痛升级，同时仍然保持关注和监督变化。

### 安装 [jscodeshift](https://github.com/facebook/jscodeshift) 和 React-Codemod

脸书提供了多个 Codemod 脚本，开发者可以用它们来启动重构。

### 将 React 升级到版本 16.9

首先，运行以下命令:

```
npm install --save [email protected]

```

或者，您也可以使用纱线:

```
yarn upgrade [email protected]

```

太好了！我们现在已经安装了 React 16.9，可以充分利用悬念。

然而，尽管 React 16.9 不包括任何重大更改，但有几个不安全的生命周期方法被重命名:

*   `componentWillMount` → `UNSAFE_componentWillMount`
*   `componentWillReceiveProps` → `UNSAFE_componentWillReceiveProps`
*   `componentWillUpdate` → `UNSAFE_componentWillUpdate`

那现在怎么办？

### 安装 React-Codemod

运行以下命令:

```
npm i react-codemod
```

这将安装 React-Codemod，这是一个 Codemod 脚本集合，用于专门为更新 React APIs 而创建的 jscodeshift。一旦你安装了它，你可以使用各种脸书提供的脚本来帮助你运行干净的重构。

如果您的应用程序使用许多不安全的生命周期方法，您可以使用这个 [Codemod](https://github.com/reactjs/react-codemod) 进行快速方便的重构，而不是费力地手动编辑它们以匹配新名称:

```
cd your_app_respository
npx react-codemod rename-unsafe-lifecycles
```

上面将会启动一个交互式提示，您可以使用它来查找并替换所有出现的旧的生命周期方法命名约定。

## jscodeshift 快速介绍

没有对 jscodeshift 的介绍，关于 React-Codemod 的讨论是不完整的。虽然我们不必使用 jscodeshift 来升级到 React 16.9，但在使用 React Codemod 脚本和一般 JavaScript 时，它是一个重要的工具包。该工具通过在多个 JavaScript(或 TypeScript)文件上运行 Codemod 来工作。

然而，jscodeshift 远远超出了通常的查找并替换 Codemod 脚本，使开发人员能够对代码库进行自动的重大更新。更新可以包括改变函数签名，然后在每个被调用的函数实例中重写代码。

您可以通过在命令行上运行以下命令来安装 jscodeshift:

```
npm install -g jscodeshift

```

下面的例子是一个简单的用例，用“bar”替换所有出现的变量“foo”

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
/**
 * Using jscodeshift to replace all occurrences of variable "foo" with "bar"
 */
module.exports = function(file, api) {
  return api
    .jscodeshift(file.source)
    .findVariableDeclarators("foo")
    .renameTo("bar")
    .toSource();
};
```

## 结论

React 是一个快速发展的库，对于开发团队来说，在保持产品和代码库完整性的同时保持更新是至关重要的。在本文中，我们探索了 Codemod，这是一个 Python 工具，它的脚本提供了一种交互式方法来重构和升级大型代码库。

我们还回顾了 React-Codemod，这是一个 npm 包，包含专门用于升级 React APIs 的脚本。最后，我们介绍了 jscodeshift，这是一个基于 Codemod 的工具包，具有更强大的自动化功能。

通过利用这些额外的工具，您可以使重构代码和 React 版本升级变得简单而轻松，使您的团队能够享受前沿的更新，而不会损害您的代码库或降低开发速度。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)