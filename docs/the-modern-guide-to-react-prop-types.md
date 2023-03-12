# 反应正确类型现代指南

> 原文：<https://blog.logrocket.com/the-modern-guide-to-react-prop-types/>

JavaScript 等弱类型语言使编程变得容易。您不需要通过钻研文档来理解依赖关系中的每一种类型，尤其是在 IDE 中编码时。虽然这个特性很有用，但是随着变量的增加和老开发人员离开您的项目，它会成为您和您的团队的负担。

React 中的类型检查通过 [prop-types 包](https://github.com/jamiebuilds/react-loadable)确保您知道每个变量存储了什么，即使缺少文档或文档没有帮助。在这篇文章中，我们深入分析了适当类型，以帮助您构建高质量的代码库。

## 先决条件

在开始之前，请确保您对 React 有一个基本的了解。您可以将本教程中的代码导入到您自己的应用程序中，并开始试验。

我们使用 Node.js，你只需要在你的 **`package.json`** 文件中包含核心的 React 库:

```
"dependencies": {
 "react": "^17.0.1",
 "react-dom": "^17.0.1",
 "react-scripts": "4.0.0",
 "web-vitals": "^0.2.4"
}

```

确保您有一个完整的 React 和 Node.js 项目。现在，您可以开始使用道具类型了。

## 什么是反应道具类型？

React prop 类型是一种确保传递给函数的类型与您希望函数采用的类型相匹配的方法。适当的类型可以防止您进行不适当的方法调用，尤其是在与单元测试结合使用时。

在编写代码很久之后，发现代码中有一个函数并不完全符合您的需要，这种情况并不少见。例如，如果一个方法可靠地返回一个基于字符串的 ISO 时间戳，而您正在对数字 UTC 时间戳执行数学运算，那么在函数调用后的某个时刻，结果将是一个讨厌的未定义值。

在底层，React 根据您定义的属性类型检查传递给组件的属性类型。当它们不匹配时，框架会向控制台记录一条警告。

![React Check Failed Property Type Visual](img/bf6fc23d9b7c961abceb6198212552e6.png)

由于该功能仅记录一条警告，因此在投入生产之前，请确保检查您的控制台。否则，你可能会错过一场冲突。

## 为什么我应该在 React 和 JavaScript 中进行类型检查？

弱类型语言不要求您指定与不同变量相关联的类型——JavaScript 不检查您的变量。它们不同于强类型的服务器端语言，因为错误会在运行时致命地破坏程序，让用户和开发人员都很头疼。

考虑以下 React 组件:

```
import PropTypes from 'prop-types';

// Class-based component
class CustomComponent extends React.Component {

  static defaultProps = {
   mystring: "Hello",
   appid: "App-1"
  }

  constructor(props) {
   super();
   this.myString = props.message;
  }
  render() {
    return (
      <div>
        {this.myString} {this.props.appid}
      </div>)
  }
}

// Functional component
const Addition = (props) => {
  console.log(props);
  const outputNum = props.mynum + props.mynum;
  return (
     <div>
        Answer: {outputNum}
     </div>)
};

```

您必须设置多个变量和属性。`**myString**`变量是一个字符串， **`mynum`** 是一个数字，`**appid**`是应用程序基于字符串的唯一标识符。

如果将`**mynum**`作为字符串传递，组件将无法呈现，这可能会造成严重的问题。类型检查通过提醒您不恰当地设置属性，让您及早发现这些问题。

## React 中的类型检查

React through prop types 中的类型检查需要创建一个单独的对象，其中包含关于您的属性的元数据。`PropTypes`模块包含许多与 JavaScript 中可用的属性类型相匹配的属性类型。

将以下内容添加到组件所在的文件中，以验证您传递的属性是否正确:

```
Addition.propTypes = {
  mynum: PropTypes.number
}

CustomComponent.propTypes = {
  mystring: PropTypes.string,
  appid: PropTypes.string
}

```

这个对象确保 **`mynum`** 是一个数字。这也保证了 **`appid`** 和 **`mystring`** 都是字符串。当它们不在时，程序会记录到控制台。

## React 中可用正确类型

React 是一个 JavaScript 库。[可用的属性类型](https://reactjs.org/docs/typechecking-with-proptypes.html)与 JavaScript 中的属性类型相同。

可用的类型，与它们的 JavaScript 等价物相匹配，包括:

*   用于存储值列表的数组
*   **元素**表示一个 DOM 元素
*   **节点**代表一个 DOM 节点
*   **数字**代表 JavaScript 数字类型
*   代表一个 JavaScript 对象的对象
*   **字符串**存储字符
*   **符号**代表以独特方式存储值的 JavaScript 对象

也可以声明一个属性是一个类的实例。用 **`PropTypes.instanceOf(object)`** 这样做。

## 指定默认值

属性类型还有一个额外的用途。当您无法传递参数时，代码可能会中断。为了避免这种情况，您可以在整个代码中设置默认属性值。然而，随着程序的增长，这个过程变得很难处理，你需要跟踪无数的变量。

React 框架允许您在组件旁边设置有意义的默认值。您可以将 **`appid`** 设置为“App-1”，将 **`mystring`** 设置为“你好”:

```
CustomComponent.defaultProps = {
  mystring: "Hello",
  appid: "App-1"
}

```

此功能也适用于功能组件:

```
Addition.defaultProps = {
  mynum: 0
}

```

现在，如果没有设置属性，程序将不会无法呈现。相反，这些值与您设置的值相匹配。

## 巴别塔的默认值

如果使用 [Babel](https://babeljs.io/) 转换，你可以在设置默认值时编写更清晰的代码。不要使用单独的对象，而是将它们直接合并到目标组件中。Babel 将您的代码转换成适当的格式。

我们的默认组件变成了:

```
// Class-based component
class CustomComponent extends React.Component {

  static defaultProps = {
    mystring: "Hello",
    appid: "App-1"
}

constructor(props) {
  super();
  this.myString = props.message;
}
render() {
  return (
    <div>
      {this.myString} {this.props.appid}
    </div>)
  }
}

```

没有必要在更大的 JavaScript 文件中寻找默认值。它们直接位于附加对象中。对代码的编译时间有一个名义上的影响。但是，用户不会注意到差异。

## 恰当地使用默认值

设置默认值并不总是最佳做法。有些情况下，您希望检查是否没有提供值，并适当地设置呈现的内容。除了为 **`mynum`** 提供默认值之外，您还可以检查 **`undefined`** 值并设置警报。

当存在与您设置的类型相匹配的逻辑默认值时，使用这些值效果最佳。这是理想的 **`appid`** 或 **`mystring`** 。即使不提供值，设置它们也会呈现完整的消息。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 远程监控冲突的属性类型

虽然 React 将问题记录到控制台，并且您希望在投入生产之前测试组件，但是也可以监视生产中的问题。应用性能管理(APM)工具捕获发送到控制台的问题和信息，并将它们推到一个集中的位置。

通过这种方式，属性类型成为捕捉不知不觉中进入开发或生产环境的 bug 的强大方法。每次释放后都保持在你的 APM 之上。

## 消除 React 中与属性类型的混淆

虽然 JavaScript 的弱类型可以帮助您快速推送应用程序，但是随着代码库的增长，功能的初衷可能会丢失。检查属性类型并将冲突记录到一个可访问的位置，这使您的团队能够在不阅读更多代码或搜索文档的情况下使用您的代码库。

这里，我们讨论了如何在 React 中使用适当的类型来解决这个问题。我们还研究了如何创建默认值，以帮助避免未定义的变量成为主要问题。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)