# 2021 年 React 延迟加载库的首选

> 原文：<https://blog.logrocket.com/the-top-choices-for-react-lazy-loading-libraries-in-2021/>

[惰性加载](https://blog.logrocket.com/lazy-loading-components-in-react-16-6-6cea535c0b52/)已经成为优化 React 应用加快加载时间的方法之一。这是一种用于防止加载网页中当前未查看或不需要的部分的方法。这是一种优化网络和移动应用的设计方法。

React 生态系统有许多通过延迟加载帮助优化应用程序的包。在这篇文章中，我将重点介绍延迟加载 React 应用程序的一些首选。

## 反应-lazyload

react-lazyload 是一个用于在 react 应用程序中延迟加载 React 组件和映像的包。

为了开始使用 react-lazyload，我们使用一个打包程序来安装它:

```
npm i react-lazyload
```

现在，我们可以延迟加载我们的组件:

```
import React from "react";
import LazyLoad from "react-lazyload";

export default function App() {
  return (
    <div className="list">
      <LazyLoad height={250} once>
        <img src="https://placedog.net/500/280" alt="dog" />
</LazyLoad>
      <LazyLoad height={250} once>
        <img src="https://placedog.net/500/300" alt="dog" />
      </LazyLoad>
      <LazyLoad height={250} offset={150}>
        <img src="https://placedog.net/500/300" alt="happy dog" />
      </LazyLoad>
    </div>
  );
}
```

上面，我们将`LazyLoad`组件包装在我们想要延迟加载的任何元素或内容上。使用 height 属性设置元素的高度。`once`是 react lazyload 的一个属性，意思是内容一旦显示，就不会被 react lazyload 管理。

react-lazyload 的一些优点包括:

*   对于所有延迟加载的组件，它只有两个事件侦听器，因此提高了性能
*   支持一次性惰性加载和连续惰性加载模式，允许您根据组件需求选择模式
*   它支持装饰者
*   它支持服务器端渲染

React-lazyload 并不是完美的库，因为它有一些缺点，其中之一是它有时会根据项目中的使用情况中断快照测试，如果添加到项目中，它会添加一个额外的 DOM 节点。

您可以查看 react lazy-load [文档](https://www.npmjs.com/package/react-lazyload)来了解更多关于 react-lazy-load 的信息。一个 CodeSandbox 的例子可以在[这里找到。](https://codesandbox.io/s/reverent-jepsen-u945i?file=/src/App.js)

## 反应迟缓加载图像组件

[react-lazy-load-image-component](https://www.npmjs.com/package/react-lazy-load-image-component)用于延迟加载 React 组件和图像，它拥有对交叉点观察器的支持，该观察器确定元素何时离开和进入视口，并且它还与服务器端渲染(SSR)兼容。

要使用，首先使用以下命令通过 npm 安装:

```
npm i --save react-lazy-load-image-component
```

用法如下所示:

```
import React from 'react';
import { LazyLoadImage } from 'react-lazy-load-image-component';

const ReactImage = ({ image }) => (
  <div>
    <LazyLoadImage
      alt={image.alt}
      src={image.src} />
    <span>{image.caption}</span>
  </div>
);

export default ReactImage;
```

在上面的代码中，我们从`react-lazy-load-image`导入`LazyLoadImage`并包装`LazyLoadImage`组件，其他图像属性作为道具传递。

react-lazy-load-image-component 的优点包括:

*   它允许您为组件指定自定义占位符
*   它允许您为`beforeLoad`和`afterLoad`指定事件
*   它与服务器端呈现的组件和应用程序兼容
*   它与类型脚本声明兼容
*   它内置了可视效果，如模糊和不透明过渡
*   它具有可定制的阈值

与其他组件类似，react-lazy-load-image-component 也有一些缺点，其中包括:

*   所有图像一次加载，react-lazy-load-image-component 加载所有在视窗中可见的图像，这意味着如果你有一个图像库，那么所有图像都将加载到你的网页的可见部分
*   效果不起作用——要使用这个包让效果起作用，你需要像他们一样导入效果 CSS
*   React `setState`挂钩只能在已安装或正在安装的组件上工作

你可以在 react-lazy-load-image-component 的[文档](https://www.npmjs.com/package/react-lazy-load-image-component)上了解更多信息。

## 反应迟缓装载

[react-lazy-load](https://www.npmjs.com/package/react-lazy-load) 根据它的[文档](https://www.npmjs.com/package/react-lazy-load)，react-lazy-load 是一个易于使用的 react 组件，它可以帮助你以一种可预测的方式推迟加载内容。它的特点是在滚动条等滚动容器中有一个自动组件。

react-lazy-load 可以通过首先安装:

```
npm install --save react-lazy-load
```

要使用 react-lazy-load 加载组件，您需要将组件元素包装在一个`LazyLoad`组件中:

```
import React from 'react';
import LazyLoad from 'react-lazy-load';

const App = () => {
  <div>
    List of cat images
    <div className="filler" />
    <LazyLoad height={500} offsetVertical={200}>
      <img src='http://placekitten.com/200/300' alt='cat' />
    </LazyLoad>    
    <LazyLoad height={500} offsetTop={200}>
      <img src='http://placekitten.com/200/300' alt='cat' />
    </LazyLoad>
    <div className="filler" />
    <LazyLoad height={480} offsetHorizontal={50}>
      <img src='http://placekitten.com/200/300' alt='cat' />
    </LazyLoad>
    <div className="filler" />
    <LazyLoad height={500} offsetTop={200}>
      <img src='http://placekitten.com/200/300' alt='cat' />
    </LazyLoad>
    <div className="filler" />
    <LazyLoad height={480} offsetHorizontal={50}>
      <img src='http://placekitten.com/200/300' alt='cat' />
    </LazyLoad>
    <div className="filler" />
```

与 React 的大多数延迟加载库类似，react-lazy-load 使用`LazyLoad`组件来确定延迟加载哪些元素。

react-lazy-load 有以下一些好处:

*   好快啊！
*   对 IE8+的内置支持
*   与其他包相比，react-lazy-load 的包较小(6kb)
*   内置去抖功能支持

react-lazy-load 的一些缺点包括它有时会被误认为是 [react-lazyload](https://www.npmjs.com/package/react-lazyload) ，另一个缺点是它需要你用`LazyLoad`包装每个可优化的组件和元素。

你可以在这里了解更多关于 react-lazy-load 的信息。

## 盖茨比形象

gatsby-image 是一个快速的图像优化库，旨在与 Gatsby GraphQL 查询一起顺利工作。它具有先进的图像加载功能，以帮助懒人加载您的网页图像。

要使用 gatsby-image，您需要安装:

```
npm install gatsby-image
```

gatsby-image 取决于启动文件，需要`gatsby-transformer-sharp`和`gatsby-plugin-sharp`，它们应该被安装并添加到您的 gatsby 配置文件中:

```
npm install gatsby-transformer-sharp gatsby-plugin-sharp
```

将它添加到下面的 gatsby 配置中:

```
plugins: [`gatsby-transformer-sharp`, `gatsby-plugin-sharp`]
```

下面是用`gatsby-image`优化组件的样子:

```
import React from 'react'
import { graphql } from 'gatsby'
import Img from "gatsby-image"

export default ({ data }) => (
  <div>
    <h1>Hello</h1>
    <Img fixed={data.file.childImageSharp.fixed} />
  </div>
)

export const query = graphql`
  query {
    file(relativePath: { eq: "blog/avatars/kyle-mathews.jpeg" }) {
      childImageSharp {
        fixed(width: 125, height: 125) {
          ...GatsbyImageSharpFixed
        }
      }
    }
  }
`
```

为了准确定义查询中图像处理的规范，我们使用了`childImageSharp`属性，这也用于更新网页中的 UI 变化。更多关于盖茨比形象的信息可以在[这里](https://medium.com/@kyle.robert.gill/ridiculously-easy-image-optimization-with-gatsby-js-59d48e15db6e)找到。

下面是 gatsby-image 在 React 应用程序中的一些好处:

*   它可以针对具有固定高度和宽度的图像进行优化
*   它可以针对超出容器范围的图像进行优化
*   它自动添加蓝色效果和懒惰加载图像
*   好快啊！

虽然经过了高度优化，但 gatsby-images 也有一些缺点，比如它难以处理非 gatsby 应用程序，对于没有 graphQL 基础知识的人来说，它也很难使用。

## 无用的

[use laszy](https://www.npmjs.com/package/uselazy)是一个 React 库，用于惰性加载和代码拆分 React 组件和映像。uselazy 处理动态和命名导入。

与大多数其他库一样，可以使用软件包管理器安装:

```
npm install uselazy
```

或者使用纱线:

```
yarn add uselazy
```

### 使用

```
/// navbar.jsx

import React from 'react';

const Navbar = () => <p> React is awesome </p>

export default Navbar;

/// Menu.js

import React from 'react';

export const Menu = () = <h2> This is a menu page </h2>

/// App.js

import React, { useState } from 'react';
import useLazy from 'uselazy';

const imports = [() => import('./Navbar'), () => import('./Menu')];

const App = () => {
   const [shouldImport, setShouldImport] = useState(false);
   const { isLoading, result: Components } = useLazy(
      useMemo(() => imports, []),
      shouldImport,
  );

 return (
    <div>
      <h1>I'm very lazy </h1>
      <button onClick={() => setShouldImport(!shouldImport)}>Click menu</button>

      {isLoading && <span>some spinner</span>}

      {Components && Components.map(Component => <Component />)}
    </div>
  );
};
```

在上面的代码中，`uselazy`保留了导入的身份，并将它们作为`useEffect`的依赖项添加到`uselazy`中。

下面是在 React 应用程序中使用`uselazy`的一些好处:

*   对动态和命名导入的内置支持
*   非常小的包大小(31kb)

你可以在这里了解更多关于无用的[。](https://www.npmjs.com/package/uselazy)

## 摘要

通过延迟加载组件和映像来优化 React 应用程序的性能在当今的开发中非常重要。这些库使提高 React 应用程序的性能和改善整体用户体验变得前所未有的容易。像 gatsby-image 和 react-lazyload 这样的库更适合延迟加载图像库应用程序，其他像 uselazy 这样的库更适合包含大量动态和命名导入的组件和 react 项目。像 react-lazy-load-image-component 及其交集观察器这样的库对于以 TypeScript 为中心的项目来说是理想的，因为它与 TypeScript 非常兼容，但在画廊应用程序中性能会很差，因为它同时加载所有图像。

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