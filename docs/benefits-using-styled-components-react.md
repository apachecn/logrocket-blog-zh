# 在 React 中使用样式化组件的好处

> 原文：<https://blog.logrocket.com/benefits-using-styled-components-react/>

***编者按:**本文于 2021 年 7 月更新了相关信息，包括 styled-components 当前的 GitHub 评级以及围绕使用 styled-components 的其他好处和关注点。*

在本文中，我们将探讨样式化组件的各种好处，以及为什么您应该考虑将它作为首选的 CSS-in-JS 框架。

## 什么是样式组件？

[Styled-components](http://styled-components.com/) 是一个 CSS-in-JS 样式框架，它使用 JavaScript 中的标记模板文字和 CSS 的力量来提供一个平台，允许您编写实际的 CSS 来样式化 React 组件。

样式化组件是用[样式化组件](http://styled-components.com)库编写的易于制作的 React 组件，在这里，您可以用 JavaScript 代码中的普通 CSS 来样式化组件。在[官方文档页面](https://styled-components.com/docs)上，您会看到下面的例子:

```
const Button = styled.a`
  display: inline-block;
  border-radius: 3px;
  padding: 0.5rem 0;
  margin: 0.5rem 1rem;
  width: 11rem;
  background: transparent;
  color: white;
  border: 2px solid white;

  ${props => props.primary && css`
    background: white;
    color: palevioletred;
  `}
`

render(
  <div>
    <Button
      href="https://github.com/styled-components/styled-components"
      target="_blank"
      rel="noopener"
      primary>
      GitHub
    </Button>

    <Button as={Link} href="/docs" prefetch>
      Documentation
    </Button>
  </div>
)
```

我们可以清楚地看到按钮是一个 JavaScript 变量，反斜线中定义的样式是普通的 CSS 样式。我们还看到了普通 CSS 样式的嵌套样式属性。这就是 styled-components 在 JavaScript 中呈现 CSS 的方式。

我知道 CSS-in-JS 的主题在前端社区，尤其是 React 开发人员中引起了激烈的争论，所以我希望你保持开放的心态。

## 样式反应组件

有许多方法可以对 React 组件进行样式化。传统的方法是在外部 CSS 文件中使用 CSS，然后传递一个字符串作为`className`属性来引用它们，就像这样:

```
render() {
  return <span className="menu navigation-menu">Menu</span>
}
```

如果您使用过大型 web 应用程序，您可以证明 CSS 文件开始变得庞大、笨拙和复杂。一个很好的解决方案是引入 SASS。尽管 SASS 有所帮助，但由于一个项目可能有大量的 SCSS 文件，您最终还是会遇到同样的问题。甚至抽象本身也开始变得复杂。

您还可以在 React 组件中内联 CSS:

```
const divStyle = {
  color: 'blue',
  backgroundImage: 'url(' + imgUrl + ')',
};

function HelloWorldComponent() {
  return <div style={divStyle}>Hello World!</div>;
}
```

React 非常不鼓励这种方法，因为它既不可持续也不可伸缩(稍后将详细介绍)。

最后，CSS-in-JS 是一种使用 JavaScript 来设计组件样式的技术。当解析这个 JavaScript 时，CSS 作为一个样式元素生成，并直接附加在 DOM 之上。

有许多 CSS-in-JS 框架:风格化组件、[情感](https://emotion.sh/docs/styled)、 [JSS](https://cssinjs.org/?v=v10.7.1) 、[镭](https://formidable.com/open-source/radium/)、[阿芙罗狄蒂](https://github.com/Khan/aphrodite)等等。你可以在这里[看到一个对比表](https://michelebertoli.github.io/css-in-js/)。然而，样式化组件可能是最受欢迎的。这个选项看起来很有前景——难怪会有一个[非常快速、广泛的采用](http://www.npmtrends.com/jss-vs-aphrodite-vs-radium-vs-styled-components-vs-glamorous-vs-emotion-vs-styletron)，在过去的一个月里有超过 60 万次下载。

## 为什么应该使用样式化组件

### 用 CSS 构建定制组件的自由

使用样式化组件，UI 设计的重点从仅仅设计 HTML 元素或 React 组件的样式转移到定义包含它们自己的样式并且在整个项目中容易重用的样式化组件。最初，一个副标题声明看起来像这样:

```
<h2 className="subTitle">Gucci Snakes </h2>
```

样式定义如下:

```
h2.subTitle{
  font-size: 2em;
  color: blue;
}
```

会变成这样:

```
import styled from 'styled-components';
const Subtitle = styled.h2`
  font-size: 2em;
  color: blue;
`;
<Subtitle>Gucci Snakes</Subtitle>
```

在这里，样式成为组件不可分割的一部分，从而消除了 CSS 类最初通过消除样式和组件之间的映射而扮演的关键角色。这都是用普通的 CSS 构建的，所以当你使用你已经习惯的东西时，总会有一种熟悉的感觉。

这种感觉已经为样式化组件的每个用户小心翼翼地保留了下来，因为语法在很大程度上仍然是普通的 CSS。

### 带造型组件的类固醇内联造型

通常，React 团队不鼓励内联样式，这种担心是有道理的，因为内联样式不允许使用伪样式和媒体查询。此外，由于担心浏览器兼容性、骆驼大小写和自动追加标量，不应使用内联样式。

但是对于样式化组件，我们可以看到一种内联样式，但是没有前面提到的内联样式包袱。我称之为*振动*能量。下面是演示的示例代码:

```
const paragraphStyles = {
color: red,
backgroundColor: black,
padding: 2px
}
<p style={paragraphStyles}> inline styles applied here </p>
```

编译输出:

```
<p style="color: red; background-color: black;">
inline styles applied here </p>
```

但是对于样式化组件…

```
import styled from 'styled-components';
const Text = styled.div`
color: red,
background: black
`
<Text>Styled with CSS-in-JS </Text>
```

编译输出:

```
<style>
.hash12345tf {
background-color: black;
color: red;
}
</style>
<p class="hash12345tf">Styled with CSS-in-JS</p>
```

看看 styled-components 如何在 DOM 上附加一个 style 标签，而 inline style 只是处理 DOM 节点的属性。

### styled-components 提供 React 本地移动支持

对于拥有 React 代码库并使用 React Native 进行移动开发的团队来说，styled-components 是最好的统一解决方案之一。如果跨平台的一致性是优先考虑的，那么您会很高兴知道样式化组件可以捆绑到 React Native 中。

使用 React Native 中的样式化组件，您可以给注释分配别名，使您的 JSX 代码非常易读。此外，您可以通过简单地调用`styled()`将任何组件，甚至是定制组件，转换成样式化的组件。

### 范围样式

在前端工具世界中， [Vue](https://vuejs.org/) 的团队(在我看来)是第一个完善这个范围样式概念的团队。使用 CSS 有一件非常恼人的事情，那就是对于一个非专业的 CSS 用户来说，当您更改样式表中某个特定元素或类的样式时，它会对 DOM 中另一个看似不相关的元素或行为产生负面影响，这是非常令人沮丧的。这是使用样式化组件的一个很好的理由，因为它是基于组件和限定范围的，就像 Vue 一样。

### 样式化组件中的无类别策略

样式化组件在类的位置强制使用道具。这种无类别策略帮助他们让更多的开发人员遵循控制组件行为的最佳实践。

最初，您应该写:

```
<h2 className="title primary">Hello World</h2> 
h2.Subtitle{
  font-size: 2em;
  color: green;

  &.primary{
    color: red;
  }
}
```

但是现在，应该是这样的:

```
const Subtitle = styled.h2`
  font-size: 2em;
  color: ${props => props.primary ? 'red' : 'green'};
`;
<Subtitle primary>Hello World</Subtitle>
```

请注意我们是如何设法将 HTML 和 CSS 操作排除在组件之外的。

或者，您也可以直接返回到`className`，同样会得到相同的结果。

```
const Subtitle = styled.h2`
  font-size: 2em;
  color: green;

  &.primary{
    color: red;
  }
`;
<Subtitle className="primary">Hello World</Subtitle>
```

### 服务器端渲染

styled-components 支持使用样式表重新水化的并发服务器端呈现。其基本思想是，每次在服务器上呈现应用程序时，都可以创建一个`ServerStyleSheet`，并将一个提供程序添加到 React 树中，该提供程序通过上下文 API 接受样式。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

这不会干扰全局样式，例如关键帧或`createGlobalStyle`，并且它允许您将样式化组件与 React DOM 的各种 SSR APIs 一起使用。

### 用 Jest 测试 CSS 单元和快照

由于 styled-components 确实是由组件组成的，所以您可以对它们进行单元测试。这是 CSS 的一个重大突破，styled-component 团队已经提供了 Jest 集成支持。

Jest 风格组件是一套用 [Jest](https://github.com/facebook/jest) 测试风格组件的工具。这个包改善了快照测试体验，并提供了一个全新的匹配器来对样式规则做出期望。它可以这样安装:

```
npm install --dev jest-styled-components
```

这里有一个测试的例子:

```
import React from 'react'
import styled from 'styled-components'
import renderer from 'react-test-renderer'
import 'jest-styled-components'
const Button = styled.button`
  color: red;
`
test('it works', () => {
  const tree = renderer.create(<Button />).toJSON()
  expect(tree).toMatchSnapshot()
})
```

这是结果快照的一个示例:

```
exports[`it works 1`] = `
.c0 {
  color: green;
}
<button
  className="c0"
/>
`
```

### Sass 和抛光支持

如果您一直关注这篇文章，那么您一定已经注意到了一些嵌套的样式， [Sass](https://sass-lang.com/) ，甚至是团队为进一步扩展 Sass 的功能而创建的工具集:

```
const Link = styled.a`
  cursor: pointer;
  text-decoration: none;
  &:hover {
    color: blue;
    text-decoration: underline;
  }
`;
```

支持 Sass 暴露了 styled-components 的核心任务:创建完美的 CSS-in-JS 体验，而不失去我们已经喜欢的 CSS 的一切，包括减少代码行和 Sass 的其他优化功能。

## 反对使用样式化组件的理由

我很难想出不使用样式化组件的理由(特别是考虑到文章的标题)，但是如果我不在本文中指出一些关于 CSS-in-JS 框架的(有效的)担忧，那将是不公平的。

### JavaScript、React 和样式化组件锁定

对于样式化组件的每个用户来说，都存在一种嵌套的锁定，您应该意识到这一点，并对此感到满意。您被锁定在 JavaScript 中，然后是 React 库，最后是样式化组件。如果它们中的任何一个消失了，你将不得不痛苦地重构你的代码库。

### 学习曲线

一些人还指出样式化组件的学习曲线很陡，以及 CSS-in-JS 框架之间的独特差异。我强烈建议使用并坚持使用样式化组件。

### 连续性问题

[Styled-components 始于 2017 年，](https://medium.com/styled-components/announcing-primitives-support-for-truly-universal-component-systems-5772c7d14bc7)而 React 开发者总是会问，“如果它不再被支持怎么办？”在生产中使用它们之前，您必须确保意识到这一点。

### 个人偏好

人们不喜欢改变。事实上，这是一个有效的论点，因为单独文档中的 CSS 仍然是 web 开发中持续时间最长的标准之一。

### 社区关注

有些人觉得样式组件社区很小，当他们遇到困难的 bug、用例或错误时，他们可能无法尽快获得足够的支持。这也是有效的，但是您必须考虑样式化组件的锁定特性。

### 表演

事实是，如果您没有使用原生 CSS 内联样式，那么您选择用于样式化的任何库都有可能产生一些性能开销。然而，您获得的少量性能开销是您从样式化组件获得的能力和灵活性的代价。好的一面是 styled-components 专注于在每个新版本中提高库的性能。越来越好了。

## 结论

围绕样式组件有一个大规模增长的社区，有超过 33，000 个⭐️on github(t1)，大部分来自 React 开发者，这是非常令人鼓舞的，并且说明了长寿的前景。

它也是定期维护的，你可以[点击这里](https://www.styled-components.com/releases)查看发布页面。如果你是一个 CSS 爱好者，你可以[看看这个目前遍布互联网的 CSS 调查状态](https://stateofcss.com/?source=announcement)。编码快乐！

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)