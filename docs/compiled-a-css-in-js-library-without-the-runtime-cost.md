# 编译:一个 CSS-in-JS 库，没有运行时成本日志博客

> 原文：<https://blog.logrocket.com/compiled-a-css-in-js-library-without-the-runtime-cost/>

CSS-in-JS 是一个模式和思想的集合，旨在解决开发人员在为他们的应用程序编写 CSS 时面临的一些最令人烦恼的挑战。它解决了与缩放 CSS 相关的一些困难，例如使用 JavaScript 模块系统将 CSS 代码分成较小的文件，以及通过生成唯一的本地名称来确定 CSS 样式的范围。

但是 CSS-in-JS 不是没有代价的。当您使用 React 等现代 UI 库时，浏览器会在屏幕上呈现任何内容之前等待 JavaScript 包文件下载，这会增加加载时间。

## CSS-in-JS 的问题是

谷歌创造了[第一个有意义的绘画(FMP)](https://web.dev/first-meaningful-paint/) 的概念来衡量一个页面的主要内容何时对用户可见。随着 React 在构建复杂项目的公司和开发人员中获得采用，React 应用程序的包大小可以从 2MB 增长到 20MB 以上。

为了解决包大小的问题，开发人员已经创建了几种解决方案。例如，你可以把这个包分成几个部分，或者延迟加载应用程序(例如，首先加载最重要的部分)。

CSS-in-JS 模式在现代 UI 库中也有类似的问题。CSS 过去是静态编写的，所以浏览器只需要读取代码。使用 CSS-in-JS，浏览器需要动态生成和更新`<style>`标签来响应状态和属性的变化。

开发人员在 JavaScipt 文件中编写 CSS 的美妙体验是以运行时生成 CSS 为代价的。

## 编译到救援

[Compiled](https://compiledcssinjs.com/) 是一个 CSS-in-JS 库，由 Atlassian Labs 创建，旨在提供无运行时成本的优秀开发人员体验。它的工作方式是在构建时静态分析您的代码，将其转换为编译后的组件，然后在运行时将样式代码移动到文档的头部。

Compiled 反映了在 [styled-components](https://blog.logrocket.com/8-reasons-to-use-styled-components-cf3788f0bb4d/) 和 Emotion 中发现的样式模式，所以如果您使用过其中的任何一个，您都会感觉很舒服。

下面是一个简单的例子。

```
import { styled } from '@compiled/css-in-js';

export const ColoredText = styled.span`
  color: #ff5630;
`;

```

上面的内容将被转换成下面的编译组件。

```
import React from 'react';
import { CC, CS } from '@compiled/css-in-js';
export const ColoredText = /*#__PURE__*/ React.forwardRef(
  ({ as: C = 'span', ...props }, ref) => (
    <CC>
      <CS hash="zd46j1">{['.cc-zd46j1{color:#ff5630}']}</CS>
      <C
        {...props}
        ref={ref}
        className={'cc-zd46j1' + (props.className ? ' ' + props.className : '')}
      />
    </CC>
  )
);
if (process.env.NODE_ENV === 'development') {
  ColoredText.displayName = 'ColoredText';
}

```

就像普通的 CSS-in-JS 一样，组件将拥有一个惟一的 hash 作为类，支持本地作用域。

这种转换使您能够在没有任何配置、设置或工具的情况下使用组件。只需导入组件并使用它。

```
const EmphasisText = (props) => {
  const color = props.massive ? '#00B8D9' : '#36B37E';
  return (
    <span
      css={{
        color,
        textTransform: 'uppercase',
        fontWeight: 600,
      }}>
      {props.children}
    </span>
  );
};
export default EmphasisText

//... import from other component

import EmphasisText from './EmphasisText';
const WelcomeComponent = () => {
    return (
        <EmphasisText massive>
            Welcome to Jumanji!
        </EmphasisText>
    );
}

```

这是完全相同的 CSS-in-JS 模式，但是没有在运行时生成 CSS。这个约束解决了由动态 CSS 生成引起的性能问题。

## 使用带 React 的编译

要使用 React 编译，您需要安装软件包和编译器。

要安装软件包，请执行以下操作:

```
npm i @compiled/css-in-js

```

接下来，安装编译器。您可以将 Babel 或 TypeScript 编译器与 Compiled 一起使用。如果您使用 Create React App，您需要从包中弹出(您需要编辑配置文件来设置两个编译器)。

要安装 Babel 插件:

```
npm install @compiled/babel-plugin-css-in-js

```

确保插件是数组中的第一个条目。

```
{
  "plugins": ["@compiled/babel-plugin-css-in-js"]
}

```

对于 TypeScript:

```
npm install @compiled/ts-transform-css-in-js ttypescript

```

将插件放在您的`tsconfig.json`文件中。

```
{
  "compilerOptions": {
    "plugins": [{ "transform": "@compiled/ts-transform-css-in-js" }]
  }
}

```

接下来，让编译器使用`[ttypescript](https://github.com/cevek/ttypescript)`库从您的配置中选择插件。

```
npm i ttypescript -D

```

要编译文件，请使用`ttsc`命令，而不是`tsc`。

或者，在 webpack 内部使用它。

```
{
  loader: require.resolve('ts-loader'),
  options: {
    compiler: 'ttypescript',
  },
},

```

你也可以用包裹。

```
npm i parcel-plugin-ttypescript

```

只需通过从 Compiled 的主库导入来编写 CSS-in-JS 文件。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
import React from 'react';
import '@compiled/css-in-js';

const EmphasisText = (props) => {
  const color = props.massive ? '#00B8D9' : '#36B37E';
  return (
    <span
      css={{
        color,
        textTransform: 'uppercase',
        fontWeight: 600,
      }}>
      {props.children}
    </span>
  );
};

export default EmphasisText

```

接下来，导入组件。

```
import React from "react";
import ReactDOM from "react-dom";
import EmphasisText from './EmphasisText';

const WelcomeComponent = () => {
    return (
        <EmphasisText massive>
            Welcome to Jumanji!
        </EmphasisText>
    );
}

ReactDOM.render(<WelcomeComponent />, document.getElementById("root"));

```

## 结论

CSS-in-JS 模式解决了与缩放 CSS 相关的最大问题之一。通过使用带有局部作用域的模块化方法，CSS 现在更易于维护，更不可预测。然而，它也带来了一些折衷，导致了与其动态渲染相关的性能问题。

Compiled 是一个 CSS-in-JS 库，它在构建时分析您的代码，并将其转换为已编译的组件，以确保在运行时不会生成 CSS 代码。这样，您可以享受编写 CSS-in-JS 的出色开发体验，而不会牺牲性能。

[编译文档](https://compiledcssinjs.com/docs/)有额外的指南，用于从样式化组件或情感和[测试编译组件](https://compiledcssinjs.com/docs/testing)的[迁移。你甚至可以用编译过的](https://compiledcssinjs.com/docs/migrating)做[服务器端渲染。](https://compiledcssinjs.com/docs/server-side-rendering)