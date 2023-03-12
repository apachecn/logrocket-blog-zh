# 在 CSS-in-JS - LogRocket 博客中你不知道可以做的 5 件事

> 原文：<https://blog.logrocket.com/5-things-you-can-do-css-in-js/>

***编者按:**为了准确和清晰，这篇文章最后一次更新是在 2021 年 7 月 29 日。要了解更多关于 CSS-in-JS 的最新信息，您还可以[查看这篇关于 CSS-in-JS 库](https://blog.logrocket.com/comparing-the-top-zero-runtime-css-in-js-libraries/)的文章。*

## CSS-in-JS 解决的是什么？

除了传统的 CSS，内联样式和 CSS-in-JS 也可以用来对 React 应用程序进行样式化。

尽管内联样式允许您将 JavaScript 对象传递给 style 属性(如下面的代码片段所示)，但它并不支持所有的 CSS 特性:

```
import React from "react";

function App() {
  const myStyle = {
    fontSize: 24,
    lineHeight: "1.3em",
    fontWeight: "bold"
  };

  return (
    <div>
      <p style={myStyle}>Hello world</p>
    </div>
  );
}
export default App;

```

另一方面，CSS-in-JS 库像[阿芙罗狄蒂](https://github.com/Khan/aphrodite)、 [styled-components](https://styled-components.com) 、 [JSS](https://cssinjs.org/?v=v10.7.1) 、[情感](https://emotion.sh/docs/introduction)、[镭](https://formidable.com/open-source/radium/)等。让开发人员不仅能够用 JavaScript 设计组件的样式，还能够解决一些 CSS 限制，例如在使用它们时缺乏动态功能、作用域和可移植性:

```
// Here's an implementation of the inline style code snippet above using Aphrodite

import React from "react";
import { StyleSheet, css } from "aphrodite";

function App() {
  const styles = StyleSheet.create({
    myStyle: {
      fontSize: 24,
      lineHeight: "1.3em",
      fontWeight: "bold"
    }
  });
  return (
    <div>
      <span className={css(styles.myStyle)}>Hello World!</span>
    </div>
  );
}
export default App;

```

在本文中，我将重点介绍使用上述 CSS-in-JS 库作为案例研究，在 CSS-in-JS 中可以做的五件事。👇

## 1.CSS-in-JS 允许您引用其他样式的组件

像 styled-components 和 Emotion 这样的库允许您使用[标记的模板文字](https://blog.logrocket.com/build-your-own-styled-components-library/#taggedtemplateliterals)从样式中创建 React 组件:

```
// Create a component that renders a <p> element with blue text
import React from "react";
import styled from "styled-components";

function App() {
  const BlueText = styled.p`
    color: blue;
  `;

  return (
    <div>
      <BlueText>My blue text</BlueText>
    </div>
  );
}

export default App;

```

但是它们也允许你瞄准其他样式的组件(就像你使用 CSS 选择器一样):

```
import React from "react";
import styled from "styled-components";

function App() {
  const ImportantText = styled.div`
    font-weight: bold;
  `;

  const Text = styled.div`
    color: gray;
    ${ImportantText} {
      font-style: italic;
    }
  `;

  return (
    <div>
      <Text>
        Text in gray
        <ImportantText>Important text in gray, bold and italic</ImportantText>
      </Text>
      <ImportantText>Important text bold</ImportantText>
    </div>
  );
}

export default App;

```

当它与伪类结合时，这是有用的；例如，要更改悬停时组件的颜色:

```
import React from "react";
import styled from "styled-components";

function App() {
  const ImportantText = styled.div`
    font-weight: bold;
  `;

  const Text = styled.div`
    color: gray;
    &:hover ${ImportantText} {
      color: red;
    }
  `;

  return (
    <div>
      <Text>
        Text in gray
        <ImportantText>Important text in gray, bold and italic</ImportantText>
      </Text>
      <ImportantText>Important text bold</ImportantText>
    </div>
  );
}

export default App;

```

## 2.JSS(或其他库)可以扩展一些库的功能

假设您已经使用 Aphrodite 来设计您的应用程序，现在您需要支持主题。

问题是阿芙罗狄蒂不容易支持主题化。至少，不像情感那么容易。

然而，有两个项目将 JSS 的核心与 Aphrodite 和 styled-components 联系起来:aphrodite-jss 和 styled-jss。

这样，你可以保留 Aphrodite(或 styled-components)的优秀部分，并使用 JSS 的所有特性和[插件](http://cssinjs.org/plugins)，从规则缓存到规则隔离，对于主题， [theming](https://github.com/cssinjs/theming) 包提供了以下高阶组件:

*   `ThemeProvider`，它通过上下文将主题对象沿 React 树向下传递
*   `withTheme`，它允许你接收一个主题对象，它作为一个属性更新

例如:

```
import React from "react";
import { createUseStyles, ThemeProvider, useTheme } from "react-jss";

function App() {
  const useStyles = createUseStyles({
    wrapper: {
      padding: 40,
      background: ({ theme }) => theme.background,
      textAlign: "left"
    },
    title: {
      font: {
        size: 40,
        weight: 900
      },
      color: ({ theme }) => theme.color
    },
    link: {
      color: ({ theme }) => theme.color,
      "&:hover": {
        opacity: 0.5
      }
    }
  });

  const Comp = () => {
    const theme = useTheme();
    const classes = useStyles({ theme });
    return (
      <div className={classes.wrapper}>
        <h1 className={classes.title}>Hello There!</h1>
      </div>
    );
  };

  const theme = {
    background: "blue",
    color: "white"
  };

  return (
    <div>
      <ThemeProvider theme={theme}>
        <Comp />
      </ThemeProvider>
    </div>
  );
}

export default App;

```

在阿芙罗狄蒂和主题的特殊情况下，作为另一个例子，您还可以使用 [react-with-styles](https://github.com/airbnb/react-with-styles) ，它与阿芙罗狄蒂和 JSS 等接口，以在定义样式时访问主题信息。

## 3.CSS-in-JS 可以用关键帧链接多个动画

与内联样式不同，CSS-in-JS 允许您使用关键帧来定义动画。

例如，这是对样式化组件的处理方式:

```
import React from "react";
import styled, { keyframes } from "styled-components";

function App() {
  const MoveAnimation = keyframes`
  0% {
    transform: translate(0, 0);
  }
  50% {
    transform: translate(50px, 0);
  }
`;

  const MyComponent = styled.div`
    display: inline-block;
    margin: 50px;
    width: 200;
    position: relative;
    animation: ${MoveAnimation} 1.5s ease infinite;
  `;

  return (
    <div>
      <MyComponent>Hello There!</MyComponent>
    </div>
  );
}

export default App;

```

但是没有多少人知道的是，可以通过在`animation`属性中使用多个关键帧对象来链接多个动画。

下面是上面的例子，修改后合并了两个动画:

```
import React from "react";
import styled, { css, keyframes } from "styled-components";

function App() {
  const MoveAnimation = keyframes`
  0% {
    transform: translate(0, 0);
  }
  50% {
    transform: translate(50px, 0);
  }
`;

  const ColorAnimation = keyframes`
  from {color: red;}
  to {color: blue;}
`;

  const MyComponent = styled.div`
    display: inline-block;
    margin: 50px;
    width: 200;
    position: relative;
    animation: ${(props) => css`
      ${MoveAnimation} 1.5s ease infinite,
      ${ColorAnimation} 1.5s linear infinite
    `};
  `;

  return (
    <div>
      <MyComponent>Hello There!</MyComponent>
    </div>
  );
}

export default App;

```

## 4.你可以用 CSS-in-JS 声明全局样式

CSS 中的一切都是全局的，使用 CSS-in-JS 的目的之一就是消除全局样式定义。

但是，可能存在全局样式的有效使用；例如，当您希望对页面中的每个元素应用相同的字体样式时。

当然，你可以一直使用传统的 CSS，通过 Webpack 导入或者在`index.html`文件中声明。

但是如果你真的想把 JavaScript 用于你的所有风格，一些库实际上允许你通过帮助组件或扩展/插件来定义全局风格。

在 Radium 中，您可以使用 [Style](https://github.com/FormidableLabs/radium/tree/master/docs/api#style-component) 组件来呈现带有全局样式的样式元素。

例如:

```
<Style
  rules={{
    body: {
      fontFamily: 'Arial, Helvetica, sans-serif'
    }
  }}
/>
```

将返回:

```
<style>
body {
  font-family: 'Arial, Helvetica, sans-serif';
}
</style>
```

JSS 使用一个[插件](https://cssinjs.org/jss-plugin-global/?v=v10.7.1)来编写全局样式:

```
const styles = {
  '@global': {
    body: {
      fontFamily: 'Arial, Helvetica, sans-serif'
    }
  }
}
```

它将返回:

```
body {
  font-family: 'Arial, Helvetica, sans-serif';
}

```

在 Aphrodite 中，你可以使用第三方扩展来创建样式。例如:

```
import {injectGlobalStyles} from "aphrodite-globals";

injectGlobalStyles({
    "body": {
          fontFamily: 'Arial, Helvetica, sans-serif',
    }
});
```

这将返回:

```
    body {
      font-family: 'Arial, Helvetica, sans-serif';
    }

```

## 5.一些 CSS-in-JS 库可以在单元测试中用样式测试组件

有些库包含用于测试带有样式的组件的实用程序。

Aphrodite 提供了一个未记录的(至少在撰写本文时)对象，[stylesheetestutils](https://github.com/Khan/aphrodite/blob/62ee2f0a39f10be75322dc0f77d1346e9731ffbf/src/exports.js#L98)，它只适用于非生产环境(`process.env.NODE_ENV !== 'production'`)，有三个方法:

*   `suppressStyleInjection`，它防止样式被注入到 DOM 中，当您想要在没有 DOM 的情况下测试 Aphrodite 组件的输出时，它非常有用
*   `clearBufferAndResumeStyleInjection`，与`suppressStyleInjection`相反，应该与之配对
*   `getBufferedStyles`，返回一串尚未刷新的缓冲样式

这里有一个如何使用它们的例子:

```
import { StyleSheetTestUtils, css } from 'aphrodite';
//...

beforeEach(() => {
  StyleSheetTestUtils.suppressStyleInjection();
});

afterEach(() => {
  StyleSheetTestUtils.clearBufferAndResumeStyleInjection();
});

test('my test', () => {
  const sheet = StyleSheet.create({
    background: {
      backgroundColor: 'blue'
    },
  });
  css(sheet.background);

 const buffer = StyleSheetTestUtils.getBufferedStyles();
});
```

镭是另一个例子。它有一个 [TestMode](https://github.com/FormidableLabs/radium/tree/master/docs/api#testmode) 对象，用于在使用`clearState`、`enable`和`disable`方法进行测试时控制内部状态和行为。

在这里，你可以找到一个如何使用测试模式的例子。

## 结论

CSS-in-JS 是一种用 JavaScript 设计应用程序的技术，您可以用实现它的库做一些有趣的事情。

在这篇文章中，我向你展示了你可能不知道可以用这些库做的五件事。当然，[并不是所有的库都是生而平等的](https://blog.logrocket.com/comparing-the-top-zero-runtime-css-in-js-libraries/)，有些东西只适用于特定的库。

看看这个[游乐场，在这里你可以测试和比较许多 CSS-in-JS 库](https://css-in-js-playground.com)。

另一方面，也有其他库将 CSS、JavaScript 和类型的概念发展得更进一步。

其中一个库是 [stylable](https://github.com/wix/stylable) ，这是一个基于组件的库，带有一个预处理器，可以将 stylable 的 CSS 转换成最小的跨浏览器的普通 CSS。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)

[![](img/94b3e0f84b30cb0d321f60471481ab64.png)](https://logrocket.com/signup/)