# React 中的情感

> 原文：<https://blog.logrocket.com/emotion-in-react/>

CSS-in-JS 是解决 CSS 问题和设计现代应用程序时常用的概念。当开始一个新项目时，决定使用哪个 CSS-in-JS 库可能很棘手。

我们经常遇到的一个问题是，大多数时候，我们构建的应用程序会被很多人使用。一个应用程序可以迅速扩展到数百万用户，这取决于它的成功，如果为您的项目选择了错误的 CSS-in-JS 库，将来可能会付出巨大的代价。

CSS-in-JS 库的生态系统每天都在变得越来越好，库变得越来越成熟和健壮，新的特性和支持正在被添加，曾经很难解决的问题变得越来越容易。

Emotion 是这些 CSS-in-JS 库中较为成熟和一致的一个，[这要归功于整个社区的参与](https://github.com/emotion-js/emotion/graphs/contributors)。在这篇文章中，我们将学习更多关于情感的知识，并了解它给我们的应用程序带来的特性和优势。

## 情绪

Emotion 是一个高性能、灵活、高性能的 CSS-in-JS 库。Emotion 帮助我们以更快的方式设计我们的应用程序，使用一个体面和一致的 CSS 组合。

以下是情感的一些特征。

## 表演

Emotion 是一个真正的表演库，因为它使用了来自一个名为 [glam](https://github.com/threepointone/glam) 的库的哲学。其理念是在不牺牲运行时成本的情况下获得最佳的运行时性能。

结果是一个超级快速和小型的库，非常适合原型和设计系统。有了情感，对象可以在任何地方使用，并且很容易与数组组合。

## 框架不可知

Emotion 是一个与框架无关的库，这意味着它可以在不同的框架和库中使用。你可以用同样强大的 API 和特性在任何地方使用它。这不是情感独有的东西，我们还有其他一些框架不可知的 CSS-in-JS 库。然而，在不同的项目中重用情感的想法很吸引人，因为，例如，你可以仅仅使用情感来构建一个支持网络和移动的完整设计系统。

Emotion 有一个名为`@emotion/native`的包，是为 React Native 本身准备的，所以开发者不需要在 web 上使用 Emotion，在 mobile 上使用另一个 CSS-in-JS 库。

要开始使用 Emotion 设计 React 原生应用，您只需安装软件包:

```
yarn add @emotion/core @emotion/native
```

## 没有额外设置

Emotion 无需额外设置即可开始使用。它支持嵌套选择器、媒体查询和自动供应商前缀。它真的是可组合的，非常适合原型开发，你可以只使用`CSS`函数和`cx`来组合你的应用程序中的所有东西。

要开始使用 Emotion，您只需安装软件包:

```
yarn add emotion
```

现在，您可以使用情感创建您的第一个样式化组件了:

```
import { css, cx } from 'emotion'

render(
  <div
    className={css`
      padding: 10px;
      background-color: red;
      font-size: 16px;
      border-radius: 6px;
    `}
  >
    Emotion CSS-in-JS
  </div>
)
```

Emotion 在某些方面仍然不同于其他著名的库，如 styled-components 和 Radium。让我们来了解一下 Emotion 与其他 CSS-in-JS 库的区别，以及是什么让它如此高性能和灵活。

## 式样

有不同的情感包，它们是为不同的用例而创建的。当在一个应用程序中使用 Emotion 时，你需要知道这些包之间的区别，这样你就不会安装超过你需要的东西和增加你的包的大小。

`emotion`包是框架不可知的，所以如果你决定在其他不使用 React 的应用程序中使用 Emotion，这是适合你的包:

```
yarn add emotion
```

`@emotion/core`包需要 React，所以如果您在 React 应用程序中使用情感，这是一个合适的包:

```
yarn add @emotion/core
```

## CSS prop

与其他著名的 CSS-in-JS 库相比，我们注意到的第一个区别是样式。使用情感设计组件的主要方法是使用`CSS`道具。

prop 允许我们将样式直接应用到组件或元素上，而不必创建一个样式化的组件。有两种方法可以正确使用`CSS`道具。

首先，如果你在一个项目中工作，你能够配置一个定制的 babel 配置，你必须在你的`.babelrc`中使用`@emotion/babel-preset-css-prop`:

```
 {
  "presets": ["@emotion/babel-preset-css-prop"]
}
```

如果您正在使用一些样板文件或启动程序，如 [create-react-app](https://github.com/facebook/create-react-app) ，您可能无法配置自定义的 babel 配置，您将需要使用第二个选项，JSX 杂注。

你所要做的就是在使用 CSS prop 的文件顶部使用 JSX 杂注，并从`@emotion/core`导入`jsx`，就像这样:

```
/** @jsx jsx */
import { jsx } from '@emotion/core'
```

JSX 杂注基本上是一段代码，告诉巴别塔插件使用`jsx`函数而不是`React.createElement`。

现在，您可以在文件的任何元素中使用`CSS` prop:

```
/** @jsx jsx */
import { jsx } from '@emotion/core'
import React from "react";
const Header = () => {
  return (
    <header>
      <h3 css={{ color: 'lightgreen'}}>Hello World!</h3>
    </header>
  )
};
```

`CSS`道具的一个很好的特性是，通过使用一个接受主题作为`CSS`道具的函数，我们可以默认访问主题:

```
/** @jsx jsx */
import { jsx } from '@emotion/core'
import React from "react";
const Header = () => {
  return (
   <header>
     <h3 css={theme => ({ color: theme.color.primary}) }>Hello World!</h3>
   </header>
  )
};
```

## 风格 API

现在很多人都在使用样式组件，另一个著名的 CSS-in-JS 库。他们可能会错过的一个功能是`styled.div`风格的 API，这就是为什么 Emotion 有一个名为`@emotion/styled`的包。

要使用这种风格的 API，我们所要做的就是使用以下命令安装`@emotion/styled`包:

```
yarn add @emotion/styled
```

现在我们可以使用样式 API 结合模板文字非常容易地创建样式:

```
import styled from '@emotion/styled';

const Button = styled.button`
  width: 100px;
  height: 40px;
  background-color: black;
  color: white;
`;
```

## 主题

Emotion 也支持主题化，要使用它，我们需要做的就是安装`emotion-theming`包:

```
yarn add emotion-theming
```

`emotion-theming`包提供了`ThemeProvider`，我们应该将这个提供者添加到我们应用程序的顶层，然后我们就可以使用`props.theme`访问样式化组件中的主题:

```
import { ThemeProvider } from 'emotion-theming';

const theme = {
  colors: {
    primary: 'black',
    secondary: 'blue'
  },
  breakpoints: {
    sm: 380,
    md: 720,
  }
}

const App = () => {
  return (
    <ThemeProvider theme={theme}>
      ...
    </ThemeProvider>
  )
}
```

## 媒体查询

使用情感处理媒体查询很简单。例如，假设我们有一个断点数组:

```
const breakpoints = [576, 768, 992, 1200];
```

我们可以创建一个助手函数来返回我们想要的断点，就像这样:

```
const mq = (n) => `@media (min-width: ${breakpoints[n]}px)`;
```

现在，在我们的样式化组件中，我们简单地使用我们的`mq`助手函数并传递我们想要的媒体查询:

```
 const Text = styled.h3`
  font-size: 16px;
  color: black;
  font-family: Inter;
  ${mq(1)} {
    color: green;
  }
  ${mq(2)} {
    color: hotpink;
  }
`;
```

## 默认 SSR

服务器端渲染是一种流行的技术，如果你使用的是`@emotion/core`和`@emotion/styled`包，它可以带着情感开箱即用。

默认的方法是使用 React 中的`renderToString`,它将在元素上方插入一个`<style>`标签:

```
import { renderToString } from 'react-dom/server'
import App from './App'

let html = renderToString(<App />)
```

高级方法只是以防你需要使用`nth child`或类似的选择器，因为默认方法不是最好的。它需要多几行代码，但它工作得很好。

在您的服务器中，放置以下代码:

```
import { CacheProvider } from '@emotion/core'
import { renderToString } from 'react-dom/server'
import createEmotionServer from 'create-emotion-server'
import createCache from '@emotion/cache'

const cache = createCache()
const { extractCritical } = createEmotionServer(cache)

let element = (
  <CacheProvider value={cache}>
    <App />
  </CacheProvider>
)

let { html, css, ids } = extractCritical(renderToString(element))

res
  .status(200)
  .header('Content-Type', 'text/html')
  .send(`<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>My site</title>
    <style data-emotion-css="${ids.join(' ')}">${css}</style>
</head>
<body>
    <div id="root">${html}</div>

    <script src="./bundle.js"></script>
</body>
</html>`);
```

现在，在客户端，您需要做的是:

```
const cache = createCache();

ReactDOM.hydrate(
  <CacheProvider value={cache}>
    <App />
  </CacheProvider>,
  document.getElementById('root')
);
```

## 我应该用情感吗？

与其他库(如 styled-components)相比，Emotion 是一个真正高性能且一致的 CSS-in-JS 库。这两个库之间有很多内容和比较，说 [Emotion 比 styled-components 快 25 倍](https://medium.com/@tkh44/emotion-ad1c45c6d28b)等等。即使去年发布了 styled-components v5，Emotion 仍然比 styled-components 有一点优势，因为它的包更小，运行时间更快。除了性能和运行时，两个库都有[几乎相同的功能和特性](https://spectrum.chat/styled-components/general/emotion-vs-styled-components~706ff9c8-f06d-4433-a42c-cc158d385089)。

那么，该不该用情感呢？这个问题没有确切的答案，因为它取决于很多因素。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

如果这是你第一次使用 CSS-in-JS 库，你可能想从样式组件开始。为了更好地理解 CSS-in-JS，styled-components 将更好地为您服务，并有助于理解 CSS-in-JS 的基本概念以及它如何在实际项目中工作。社区提供了大量的内容，你可以很快了解 CSS-in-JS。

你以前用过 CSS-in-JS 库，并且正在寻找一个更小更快的库吗？我会带着感情走。如果您习惯于在项目中使用 CSS-in-JS 库，那么您会确切地知道如何以及何时使用它。Emotion 真的可以帮助你进入下一个层次，使用 CSS-in-JS 构建更具性能和可伸缩性的应用程序。

## 结论

在本文中，我们了解了更多关于 Emotion 的内容，这是一个强大的、高性能的 CSS-in-JS 库，它有很多不错的特性。我们学习了情感的核心，我们使用了道具，并学习了主题化。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)