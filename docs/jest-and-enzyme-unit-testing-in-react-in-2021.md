# Jest 和 Enzyme:2021 年 React 中的单元测试

> 原文：<https://blog.logrocket.com/jest-and-enzyme-unit-testing-in-react-in-2021/>

自框架早期以来，React 2021 中的单元测试已经走过了漫长的道路。诸如 [Jest](https://jestjs.io/) 和 [Enzyme](https://enzymejs.github.io/enzyme/) 这样的工具将进入生产[的编码错误减少了 40%到 80%之多](https://www.researchgate.net/publication/3249271_Guest_Editors'_Introduction_TDD--The_Art_of_Fearless_Programming)。在部署这些广泛的框架时，不再需要推出自己的解决方案。

在本文中，我们将看看如何使用 Jest 和 Enzyme 为 React 构建单元测试，并学习如何可靠地测试组件树和函数。

## 先决条件

本教程要求具备[反应](https://reactjs.org/)的基础知识。我们依靠 [Node.js](https://nodejs.org/en/) 来提供内容。

你需要安装几个库来使用 Jest 和 Enzyme。在 Node.js `package.json`文件中包含以下部分，或者使用`npm`安装包:

```
"devDependencies": {

"@babel/preset-env": "7.12.7",

"@babel/preset-react": "^7.12.7",

"babel-jest": "^26.6.3",

"jest": "^26.6.3",

"react-test-renderer": "^17.0.1"

}
```

虽然 React 是一个前端框架，但我们将重点关注使用 Node.js 运行测试。

## 一个简单的反应程序

在开始单元测试之前，我们需要创建一个简单的程序。我们创建了一个返回简单计算结果的函数和一个呈现内容的函数。

将以下内容添加到您的`index.js`或自定义 JavaScript 文件中:

```
function getComputation(a, b){
return a + b;

}
function sayHello(){
return <div><p>Hello World!</p></div>;

}

```

启动 web 服务器，打开应用程序的索引页。确保页面显示“Hello World！”文字。

## React 中的单元测试是什么？

假设`sayHello`函数停止返回正确的内容或者完全失败。您可能想知道该方法在进入生产之前是否停止了工作。

单元测试确保您的代码在生产的早期就像预期的那样工作。开发人员在编写函数时将它们结合在一起，以确保每个函数都能正常工作。测试在与强大的日志记录相结合时非常有效。

## 编写好的单元测试

测试需要彻底，这需要时间和精力。尽管如此，编写可扩展的套件可以让您安心，同时确保您的应用程序不会意外失败并在此过程中赶走用户。

良好的单元测试:

*   测试单个功能
*   为预期的行为和功能编写测试
*   不同的资产和行为依赖于一种共同的语言
*   通过日志和其他信息深入了解问题

随着时间的推移，编写大量的测试变得越来越容易，正如测试随着时间的推移成为第二天性一样。

## 编码最佳实践对测试的影响

有一些最佳实践可以遵循，以提高测试能力和代码质量。[编码标准和最佳实践](https://www.aversan.com/coding-standards-and-best-practices-2/)与保持您的应用程序平稳运行密切相关。

要使运行测试和使用应用程序更容易:

*   将重复的代码分割成独立的和可测试的功能
*   避免组合功能
*   不要隐藏关键代码

难以通读的混乱代码，将许多功能包装在一个函数中，使得测试更加困难。不好的做法也让以后的发展更加困难。

## 笑话和酶的单元测试

Jest 和 Enzyme 允许您编写强大的单元测试，而无需从头构建框架。这些工具以一种比编写单个函数更自然的方式包装测试，并且还允许您访问报告和断言。

在你的`src`文件夹下创建`index.test.js`。在这个文件中为`getComputation`函数编写一个测试:

```
const idx = require('./index.js')
describe("testComputation", () =>{
it('adds 1 + 5', () =>{

    expect(idx.getComputation(1, 5)).toBe(6);

});

});
```

我们确保程序返回正确的结果。你仍然需要告诉测试者你的测试。

在名为`testconfig.json`的新文件中写入以下配置:

```
{

"verbose": true,

"testURL": "http://localhost/",

"testMatch": ["**index.test.js?(x)"]

}
```

更改`testMatch`数组以匹配到`test.index.js`的路径。您可以告诉 Jest 使用正则表达式从任何文件中读取测试。

最后，将以下内容添加到`package.json`中，告诉 Node 如何运行您的单元测试:

```
"scripts": {

"test": "jest",

}
```

从命令行运行测试:

```
jest testComputation –config=./testconfig.json
```

Jest 将运行`test.index.js`文件中的`testComputation`测试。每个测试的详细信息都会出现在您的控制台中。

## DOM 测试

React 提供了一个 DOM 渲染器。Enzyme 以此为基础，让您测试单个组件。您可以验证单个组件。

`sayHello`函数返回一个 div。您可以创建以下测试来确保页面正确呈现:

```
import {shallow} from 'enzyme';
describe("testRender", () =>{
it("should render hello world", () => {

    const html = idx.sayHello();

    const wrapper = shallow(html).toJSON();

    expect(wrapper.text()).toContain("Hello World!");

});

});
```

除非你改变树，否则划分看起来是一样的。这确保了结果内容保持一致。检测不需要的更改有助于改善用户体验。

## 酶是做什么的？

在上面的测试用例中，Enzyme 提供了挂载和遍历 React.js 组件树的机制。该框架让您可以轻松地断言、操作和遍历组件。

与过去你可能无法访问渲染器不同，Jest 可以渲染内容，而 Enzyme 允许你测试关于内容的假设。这可以使您避免编写可能有问题的用例，在这些用例中，您需要像上面所示的那样匹配字符串。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 用酶记录

酶也可以作为进入单元测试的窗口。没有什么比测试无缘无故失败更令人沮丧的了，尤其是如果您使用的依赖项没有提供像样的日志。

您可以包装对象并将其打印到控制台:

```
const html = idx.sayHello();

const wrapper = shallow(html).toJSON();

console.log(wrapper.debug());
```

如果您在持续集成框架中运行您的测试，将这些信息记录到一个中心位置。您选择的工具可能已经从控制台聚集了信息，但是您可以使用 [Logstash](https://www.npmjs.com/package/logstash-client) 或另一个可搜索的日志存储平台将这些日志存储在 [Elasticsearch](https://www.elastic.co/) 中。

## 持续集成中的单元测试

单元测试是确保应用程序健康的更广泛策略的一部分。它们是减少系统中错误数量的第一步。

在持续集成工具中使用它们可以防止应用程序在更新后崩溃。持续集成使构建自动化，如果意外中断，这可能是个问题。

像 [Circle CI 和 Gitlab CI](https://circleci.com/blog/continuously-testing-react-applications-with-jest-and-enzyme/) 这样的工具可以让你在 [Docker](https://www.docker.com/) 容器中运行测试，作为构建过程的一部分。如果您的测试失败了，整个构建就会失败。请确保登录到应用程序性能管理或其他日志工具，以跟踪您的构建中的问题。

## 代码覆盖率

随着应用程序的增长，确保彻底测试变得更加困难。代码覆盖率，即测试中运行的源代码的数量，是一个有用的跟踪统计数据。使用代码覆盖工具来查找项目中未测试的部分。

Jest 允许您在运行测试时跟踪这个统计数据。只需在控制台上使用`–coverage`选项，准备好 100%完成你的 [Jest GitHub 代码覆盖率](https://github.com/pamepeixinho/jest-coverage-badges)徽章。生成的报告显示语句、分支、函数和到达的行。

## 2021 年 React 中的单元测试

单元测试是开发的重要部分。它减少了错误，提高了生产率，并消除了调试代码所花费的时间。

Jest 是一个强大的平台，让你能够在 2021 年的 React 中执行彻底的单元测试。将您的测试部署到您选择的持续集成工具，并使日志可访问，以保持您的应用程序健康。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)