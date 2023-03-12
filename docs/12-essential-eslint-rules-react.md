# React 的 12 条基本原则

> 原文：<https://blog.logrocket.com/12-essential-eslint-rules-react/>

## 介绍

ESLint 为 JavaScript 代码提供了一套全面的规则，涵盖了风格选择和防止常见错误。单独使用 ESLint 将会提升你的项目，但是有 ESLint 插件可以添加 React 特定的规则，帮助你编写可靠的 React 应用程序。

在本帖中，我们将回顾这些 ESLint 规则和插件，包括它们在钩子上的应用。这里有一些快速链接供您参考:

## 安装 ESLint

如果您的项目中还没有安装 ESLint，您可以安装它并通过运行以下命令生成初始配置:

```
npm init @eslint/config

```

ESLint 会问一些关于你的项目的问题。其中，它会询问您是否正在使用 React。选择此选项，ESLint 将与`eslint-plugin-react`一起安装。

## 使用 Create React 应用程序的内置 ESLint 配置

如果您的应用程序使用 Create React App，您很可能不需要安装本文中提到的任何插件，甚至根本不需要安装 ESLint。v3 和更高版本的 React-scripts(CRA 的一个包)的所有版本都使用内置的 Create React App ESLint 配置，该配置已经设置为包括`eslint-plugin-react-hooks`插件(以及方便的`jsx-a11y`插件)。

***注意，**在 CRA app 中手动安装 ESLint，或本文提到的其他插件，可能会导致版本冲突或其他问题*

## 为 React 配置 ESLint

使用 React ESLint 插件时，还应该向 ESLint 配置的 settings 对象添加 React 部分。这允许您指定正在使用的 react 版本，或者使用`latest`或`detect`特殊值来分别使用最新的 react 版本或检测项目正在使用的 React 版本:

```
{
  ...other ESLint config
  settings: {
    react: {
      version: 'detect'
    }
  }
}

```

还有一些其他不太常用的配置选项，记录在`eslint-plugin-react`插件的 npm 页面上。

## 反应钩子规则(`eslint-plugin-react-hooks`)

这个插件只包含两条规则，但是它们对于避免用钩子编写[函数组件](https://blog.logrocket.com/what-are-react-pure-functional-components/)时的常见陷阱是至关重要的。

### 装置

从 npm 安装软件包:

```
npm install --save-dev eslint-plugin-react-hooks

```

要使用激活这两个规则的推荐配置，您需要更新您的 ESLint 配置文件，在`plugin:react-hooks/recommended`的`extends`部分添加一个条目:

```
{
  ... other ESLint config
  extends: [
    'eslint:recommended',
    'plugin:react-hooks/recommended'
  ]
}

```

如果您发现违反规则的行为未被标记，请确保您已将此内容包含在`extends`部分。

### 反应钩/钩规则

这条规则强制组件在使用钩子时遵循钩子的[规则。在](https://reactjs.org/docs/hooks-rules.html) [React 文档](https://reactjs.org/docs/hooks-rules.html#explanation)中详细讨论了这些规则，但是在使用钩子时有两条规则必须遵守:

1.  钩子应该只从组件的顶层代码中调用。这实际上意味着钩子不应该被有条件地调用——它们应该在每次渲染时被调用，以同样的顺序，以避免问题和微妙的错误
2.  钩子只能从一个函数组件或者另一个钩子中调用
    1.  定制钩子经常从内置的，甚至其他定制钩子中组合行为

在默认配置中，违反此规则将导致错误，从而导致 lint 检查失败。

### 反应-钩子/详尽-deps

这个规则强制执行关于传递给钩子的[依赖数组](https://reactjs.org/docs/hooks-effect.html#tip-optimizing-performance-by-skipping-effects)的内容的某些规则，比如`useEffect`、`useCallback`和`useMemo`。通常，在效果、回调或记忆值计算中引用的任何值都必须包含在依赖数组中。如果处理不当，可能会导致过时的状态数据或[无限渲染循环](https://blog.logrocket.com/solve-react-useeffect-hook-infinite-loop-patterns/)等问题。

这条规则有助于发现潜在的依赖相关的错误，但是有一些限制:

*   默认情况下，不使用此规则检查带有依赖数组的自定义挂钩。它只适用于内置挂钩。如果您有一个带有依赖数组的定制钩子，那么您可以设置配置选项`additionalHooks`，它需要一个正则表达式来定义您的定制钩子的名称。React 团队一般不建议这样做，正如在 [`eslint-plugin-react-hooks`自述文件](https://github.com/facebook/react/tree/main/packages/eslint-plugin-react-hooks#advanced-configuration)中所讨论的。相反，该团队建议提供更高级别的 API。
*   使用此规则将不检查带有依赖项数组的自定义挂钩。它只适用于内置挂钩
*   只有当规则是静态值数组时，它才能正确检查依赖关系。如果使用了对另一个数组的引用，或者将另一个数组扩展到该数组中，规则将发出警告，指出它无法确定依赖关系

这条规则多少有些争议；GitHub 上有几个[长的问题线程，但是 React 团队在征求和整合反馈方面做得很好。在默认配置中，违反此规则将被视为警告。](https://github.com/facebook/react/issues?q=is%3Aissue+exhaustive-deps+is%3Aclosed+sort%3Acomments-desc)

这条规则的细节可能会占据一整篇文章。要更深入地了解这条规则以及如何正确使用它，请参见 LogRocket 博客上的[了解 React exhaustive-deps 林挺警告](https://blog.logrocket.com/understanding-react-exhaustive-deps-linting-warning)一文。

## 反应规则(`eslint-plugin-react`)

这个插件包含了更多特定于 React 核心的规则(在写的时候有 100 条规则)。大多数规则涵盖一般的 React 实践，其他规则涵盖与 JSX 语法相关的问题。让我们来看看一些比较有用的。

### 安装和配置

如前所述，如果你的应用基于 Create React App(并使用 react-scripts)，那么你已经安装了这个插件。否则，您可以从 npm 安装它:

```
npm install --save-dev eslint-plugin-react

```

要使用推荐的规则集，只需将`'plugin:react/recommended'`添加到您的 ESLint 配置的`extends`部分:

```
{
  ... other ESLint config
  extends: [
    'eslint:recommended',
    'plugin:react/recommended',
    'plugin:react-hooks/recommended'
  ]
}

```

如果您使用 React 17 或更高版本，此推荐配置包括一些不适用的规则。React 的最新版本包括一个新的 JSX 变换，它不再要求 React 包含在您的组件中。这意味着您可以在使用 JSX 时删除`import React from` `'react';`等导入。

这将导致 ESLint 报告 React 不在范围内的错误(有关更多详细信息，请参见下面的规则`react/react-in-jsx-scope`)。如果这适用于您的应用程序，您需要将`plugin:react/jsx-runtime`添加到您的`extends`列表中:

```
{
  ... other ESLint config
  extends: [
    'eslint:recommended',
    'plugin:react/recommended',
    'plugin:react/jsx-runtime',
    'plugin:react-hooks/recommended'
  ]
}

```

这将禁用相关规则，并防止 ESLint 将其报告为错误。

现在我们已经安装并配置了插件，让我们来看看一些更有用的插件。

### 反应/按钮类型

出于可访问性的原因，组件中大多数可点击的元素，不是到另一个 URL [的简单链接，应该被实现为按钮](https://blog.logrocket.com/building-accessible-glossary-vanilla-javascript/)。一个常见的错误是，当这些按钮不用于提交表单时，忽略了它们的`type`属性。

当没有指定`type`时，按钮默认为`submit`类型。这可能会导致从`form`元素派生的按钮出现问题。在表单中单击这样的按钮将导致可能不需要的表单提交。

不打算提交表单的动作按钮应该有一个`button`的`type`属性。

这条规则强制所有按钮都显式地拥有一个`type`属性——即使是那些打算作为**提交**按钮的按钮。通过明确，避免了无意的提交，代码的意图也很清楚。

### 反应/推进类型

要求所有的 React 组件在一个`[PropTypes](https://reactjs.org/docs/typechecking-with-proptypes.html)`声明中描述它们的属性。这些检查只在开发模式中抛出错误，但是可以帮助捕捉由于错误的属性被传递给组件而产生的错误。

如果您的项目使用 TypeScript，也可以通过向描述它们的组件属性添加类型批注来满足此规则。

Dillion Megida 的[比较 React 应用程序中的 TypeScript 和 PropTypes](https://blog.logrocket.com/comparing-typescript-and-proptypes-in-react-applications/)中详细介绍了这两种方法。

### 反应/要求-默认-道具

根据组件的不同，有些道具可能是必需的，而有些是可选的。如果一个可选的道具没有传递给一个组件，它将会是`undefined`。这可能是意料之中的，但是如果不检查该值，可能会引入错误。

这条规则要求在组件的`defaultProps`声明中给每个可选属性一个默认值。如果这是组件所期望的，这个缺省值可以被显式地设置为`null`或`undefined`。

对于功能组件，有两种不同的策略可用于检查默认属性:

#### 默认道具

这个策略期望函数组件有一个默认的`defaultProps`对象。

```
const MyComponent = ({ action }) => { ... }

MyComponent.propTypes = {
  Action: PropTypes.string;
};

MyComponent.defaultProps = {
  action: 'init'
};
```

#### 默认参数

这种策略希望在函数声明中指定默认值，使用 JavaScript 的内置默认值语法。

```
const MyComponent = ({ action = 'init' }) => { ... }

MyComponent.propTypes = {
  Action: PropTypes.string;
};
```

如果使用`defaultArguments`策略，就不应该有`defaultProps`对象。如果有，这个规则就失效了。

### react/no-数组索引键

当在 React 中呈现一个项目列表时，我们通常在一个数组上调用`map`，映射函数返回一个组件。为了跟踪列表中的每一项，React 需要这些组件有一个`key`道具。

呈现列表的一个常见缺陷是使用数组索引作为键。这可能会导致不必要甚至不正确的渲染。React 文档[建议不要采用这种做法](https://reactjs.org/docs/lists-and-keys.html#keys)，因为它会导致一些问题(还有一个关于如何使用键的[更详细的讨论](https://reactjs.org/docs/reconciliation.html#recursing-on-children))。键应该是列表中该项目的唯一标识符，不会改变，就像数据库行中的主键值一样。

该规则确保数组索引不被用作键。

### react/在 jsx 范围内反应

考虑这个简单的 React 组件:

```
const Greeter = ({ name }) => <div>Hello {name}!</div>;
```

根本没有引用`React`对象。然而，`React`仍然需要被导入，否则您将遇到一个错误。这是由于 JSX 的蒸发过程。浏览器不理解 JSX，所以在构建过程中(通常使用 Babel 或 TypeScript 之类的工具)，JSX 元素被转换成有效的 JavaScript。

这生成的 JavaScript 代码调用`React.createElement`来代替 JSX 元素。上述组件可能会被转换成类似这样的内容:

```
const Greeter = ({ name }) => React.createElement("div", null, "Hello ", name, "!");
```

这里对`React`的引用是为什么`React`仍然必须被导入的原因。这个规则确保所有带有 JSX 标记的文件(甚至不一定是 React 组件)都有`React`(通常通过`import`或`require`调用)。

### react/jsx-uses-react

总是导入 React 对于正确的翻译是必要的，但是当 ESLint 查看文件时，它仍然是 JSX，所以它不会在任何地方看到引用的`React`。如果项目使用了`no-unused-vars`规则，这会导致一个错误，因为`React`被导入了，但是没有在任何地方被使用。

该规则捕捉这种情况，并防止`no-unused-vars`在`React`导入时失败。

### 反应/显示名称

为了正确调试输出，所有 React 组件都应该有一个显示名称。在许多情况下，这不需要任何额外的代码。如果组件是一个命名函数，显示名称将是该函数的名称。在下面的例子中，组件的显示名称将是`MyComponent`。

*   `const MyComponent = () => { … }`
*   `const MyComponent = function() { return …; }`
*   `export default function MyComponent() { return …; }`

在某些情况下，自动显示名称会丢失。这通常是当组件声明被另一个函数或更高级的组件包装时，如下面的两个示例所示:

*   `const MyComponent = React.memo(() => { … });`
*   `const MyComponent = React.forwardRef((props, ref) => { … });`

`MyComponent`名称被绑定到由`memo`和`forwardRef`返回的新的“外部”组件。组件本身现在没有显示名称，这将导致此规则失败。

当出现这些情况时，可以通过`displayName`属性手动指定显示名称，以满足规则:

```
const MyComponent = React.memo(() => { ... });
MyComponent.displayName = 'MyComponent';
```

### 反应/无儿童道具

React 组件接受一个名为`children`的特殊道具。这个属性的值将是元素的开始和结束标记中的任何内容。考虑这个简单的`MyList`组件:

```
const MyList = ({ children }) => {
  return <ul>{children}</ul>;
};
```

这将呈现外部的`ul`元素，我们放入该元素的任何子元素都将呈现在其中。

```
<MyList>
  <li>item1</li>
  <li>item2</li>
</MyList>
```

这是 React 组件的首选模式。虽然不建议这样做，但是可以将子对象作为显式子属性传递:

```
<MyList children={<li>item1</li><li>item2</li>} />
```

上述用法实际上会导致错误，因为 JSX 表达式，像作为显式子属性传递的表达式一样，必须有一个根元素。这就要求孩子们被包裹在一个片段里:

```
<MyList children={<><li>item1</li><li>item2</li></>} />
```

如第一个示例所示，子元素作为子元素直接传递给组件，因此组件是表达式的根元素。这里不需要片段或其他封闭元素。

这主要是一种风格上的选择/模式，但它确实可以防止无意中传递显式的`children` prop 和子元素:

```
<MyList children={<><li>item1</li><li>item2</li></>}>
  <li>item3</li>
  <li>item4</li>
</MyList>
```

在这种情况下，子元素(`item3`和`item4`)会被显示，但是`item1`和`item2`不会。这个规则确保子元素只以惯用的方式传递，作为子 JSX 元素。

### 对儿童有反应/无危险

React 的`dangerouslySetInnerHTML`属性允许将任意标记设置为元素的`innerHTML`属性。通常不建议这样做，因为这可能会将您的应用程序暴露给跨站点脚本(XSS)攻击。然而，如果您知道您可以信任输入，并且用例需要它，那么这种方法可能是必要的。

prop 需要一个带有`__html`属性的对象，其值是一个原始的 HTML 字符串。该字符串将被设置为`innerHTML`。

因为这会替换任何现有的子内容，所以将它与`children` prop 结合使用是没有意义的。事实上，如果您尝试这样做，React 将会抛出一个错误。不像一些只在开发模式下出现的错误(像`PropTypes`验证错误)，这个错误会让你的应用崩溃。

该规则实施相同的规则。如果`dangerouslySetInnerHTML`用于子节点，lint 规则将失效。最好在林挺或构建时捕获这些错误，而不是在应用程序部署后由用户报告！

### react/jsx-no-bind

每次渲染 React 组件时，都要付出性能代价。通常，某些模式或实践会导致组件不必要地重新呈现自身。这种行为有许多原因，这条规则有助于防止其中之一。

当组件内部定义了任何函数时，它将是每次渲染的一个新的函数对象。这意味着无论何时组件被重新渲染，道具都被认为是改变了的。即使有了`React.memo`，组件也会重新渲染。

如果子组件有任何将该函数作为依赖项的`useEffect`调用，这可能会导致效果再次运行，从而产生无限循环的可能性，这可能会冻结浏览器。

启用此规则后，任何作为属性传递的函数都将被标记。

有两种方法可以解决这个问题。如果函数不依赖于组件内部的任何其他东西，它可以被移到组件外部，在那里它只是一个普通的函数，将始终是同一个内存引用。这确保了每次都将相同的函数传递给 prop。

对于函数*在某种程度上依赖于组件的情况，通常的解决方法是用`[useCallback](https://reactjs.org/docs/hooks-reference.html#usecallback)`钩子来记忆它。函数中引用的任何属性都必须包含在`useCallback`依赖数组中；有时这需要多层次的价值或功能记忆。*

这增加了一些复杂性，但有助于减少额外的渲染和防止无限循环。

## 包扎

这里涉及的规则只是由`eslint-plugin-react`插件提供的一些规则。有些规则可能固执己见或过于热心，但大多数规则也有配置选项来使它们不那么严格。

还有另一个非常有用的 ESLint 插件，围绕 JSX 和可访问性实践:`[eslint-plugin-jsx-a11y](https://www.npmjs.com/package/eslint-plugin-jsx-a11y)`。这个插件中的规则检查你的 JSX 标记，以确保良好的 HTML 可访问性实践得到遵循。

这些 React ESLint 插件有助于避免常见的陷阱，尤其是如果您还是 React 新手的话。您甚至可以编写自己的规则和插件来涵盖其他情况！

## [LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。