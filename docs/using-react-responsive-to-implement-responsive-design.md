# 使用 react-responsive 实现响应式设计

> 原文：<https://blog.logrocket.com/using-react-responsive-to-implement-responsive-design/>

在 [Tailwind CSS 或 Bootstrap](https://blog.logrocket.com/tailwind-css-vs-bootstrap-ui-kits/) 出现之前，有一个古老的神秘魔法，当所有 CSS 和前端开发人员想要创建真正响应的应用程序时，他们会参考并使用它:媒体查询。

从移动设备到平板电脑和台式机等等，前端开发人员知道，掌握媒体查询是让任何用户都能访问他们的艺术作品的关键。

在这篇博文中，你将学习如何使用这个神奇的工具，现在它被定义为在 [npm](https://www.npmjs.com/package/react-responsive) 上找到的 react-response 包。跳进来吧，年轻的魔术师，该是你建造知识金字塔的时候了。

## 什么是反应式响应？

react-responsive 是一个 npm 包，允许您在 react 项目中创建真正响应性的设计。它使用媒体查询和断点的组合能力来定义开发人员想要显示和隐藏的 DOM 元素。作为 React 开发人员，这是您武库中的另一个强大工具。

如果你喜欢移动优先的设计，响应式设计，或者如果你希望你的应用程序可以根据屏幕大小进行缩放，同时保持其结构完整性，那么 react-responsive 就是你的选择。

## react-responsive 入门

首先，创建一个没有依赖项的新 React 项目。我们将使用`npm i -S react-responsive`执行 react-response 包的 npm 安装。

正如你所知道的，react-responsive 预期不同的用例，所以我们可以用钩子或组件来使用它。接下来，转到您的`app.js`，用`import { useMediaQuery } from 'react-responsive'.`导入包

## 定义媒体查询

在`app.js`函数中，我们将像这样定义媒体查询:

```
const isMobileDevice = useMediaQuery({
    query: "(min-device-width: 480px)",
  });

  const isTabletDevice = useMediaQuery({
    query: "(min-device-width: 768px)",
  });

  const isLaptop = useMediaQuery({
    query: "(min-device-width: 1024px)",
  });

  const isDesktop = useMediaQuery({
    query: "(min-device-width: 1200px)",
  });

  const isBigScreen = useMediaQuery({
    query: "(min-device-width: 1201px )",
  });

```

这里，如果设备是特定的大小，我们使用媒体查询来提供内容。说起来，react-responsive 太棒了，以至于我们可以用`device`道具来强制内容遵循某个大小。

这里面有`orientation`、`scan`、`aspectRatio`、`deviceAspectRatio`、`height`等按键。，它们允许您在通常不允许的环境中模拟设备进行测试。

现在，回到我们的代码。

在你的`src`中创建一个文件夹，命名为`components`。在那里，创建五个新文件夹:`big-screen`、`desktop`、`laptop`、`mobile`和`tablet-mobile`。

每个文件夹都有一个`component.jsx`文件和一个`styles.css`文件。我们现在会有很长的代码块，因为我们想建造金字塔，所以做好准备，年轻的魔术师。你准备好了吗？好吧，我们开始吧。

```
// big-screen.component.jsx

import React from 'react'
import './big-screen.styles.css'
export const BigScreen = () => {
    return (
        <div className="big-screen">
            <p>Whoops! I'm in big screen mode.</p>
            <p>This is the base of the pyramid</p>
        </div>
    )
}

```

```
// big-screen.styles.css

.big-screen {
  width: 600px;
  height: 100px;
  background: #7c52bf;
  margin: 0 auto;  
}
.big-screen p {
  padding-top: 2%;
}

```

```
// desktop.component.jsx

import React from 'react'
import './desktop.styles.css'
export const Desktop = () => {
    return (
        <div className="desktop">
            <p>Whoops! I'm in desktop mode.</p>
            <p>But if you see anything below me, i am now in Laptop mode</p>
        </div>
    )
}

```

```
// desktop.styles.css

.desktop {
  width: 400px;
  height: 100px;
  background: #b097d8;
  margin: 0 auto;
}
.desktop p {
  padding-top: 2%;
}

```

```
// laptop.component.jsx

import React from 'react'
import './laptop.styles.css'
export const Laptop = () => {
    return (
        <div className="laptop">
            <p>Whoops! I'm in laptop mode.</p>
            <p>But if you see anything below me, i am now in Big Screen mode</p>
        </div>
    )
}

```

```
// laptop.styles.css

.laptop {
    width: 500px;
    height: 100px;
    background: #9674cb;
    margin: 0 auto;
  }

.laptop p {
  padding-top: 2%;  
}

```

```
// tablet-mobile.component.jsx

import React from "react";
import "./tablet-mobile.styles.css";
export const TabletMobile = () => {
  return (
    <div className="tablet-mobile">
      <p>Whoops! I'm in tablet-mobile mode.</p>
      <p>But if you see anything below me, i am now in Desktop mode</p>
    </div>
  );
};

```

```
// tablet-mobile.styles.css

.tablet-mobile {
    width: 300px;
    height: 100px;
    background: #cab9e5;
    margin: 0 auto;
}
.tablet-mobile p {
    padding-top: 2%;
}

```

```
// mobile.component.jsx

import React from 'react'
import './mobile.styles.css'
export const Mobile = () => {
    return (
        <div className="mobile">
            {/* <p>Whoops! I'm in mobile mode.</p> */}
        </div>
    )
}

```

```
// mobile.styles.css

.mobile {
    width: 80px;
    height: 80px;
    margin: 0% auto;
    border-radius: 50%;8
    box-shadow: 15px 15px 0 0 #e4dcf2;
}

```

咻！代码太多了！但是别担心，年轻的魔术师，这很容易理解。我们在上面的文件中所做的就是为不同的屏幕尺寸创建不同的组件，这样当你输入一个新的屏幕分辨率时，我们的金字塔就会出现一个新的高度。

如果你需要了解这是如何工作的，你可以查看一下 [my GitHub repo](https://github.com/jeffersonnnn/react-responsive-article) 和 [live 版本的 app](https://trusting-wilson-27fffa.netlify.app/) 。

现在，让我们回到`app.js`。在 return 语句中，将其放入:

```
<h1>React Responsive - a guide</h1>      
{isMobileDevice && <Mobile />}
  {isTabletDevice && <>
  <TabletMobile />
  {isDesktop && <Desktop />}
  {isLaptop && <Laptop />}
  {isBigScreen && <BigScreen />}
</>}

```

这里，我们使用 React 的 truthy 语句来定义视口大小和内容。除了处理`app.js`中定义的方法的断点，我们还可以通过组件来完成。

在这种情况下，您将使用`import MediaQuery from react-responsove`导入组件 MediaQuery，并在如下组件中使用它:

```
<MediaQuery minDeviceWidth={1224}>
  <p>Manipulate me with the powers of React Responsive</p>
</MediaQuery>

```

## 在应用程序中测试 react-response

祝贺你学完了这一课！现在我们要做的就是测试我们刚刚一起构建的应用程序。

为此，打开浏览器的开发工具窗口，将`viewport`设置为`responsive`。然后，通过从最小尺寸调整到最大尺寸来创建金字塔形状。

我很高兴我们一起做到了，年轻的魔术师。现在，随着真理的金字塔向你揭开，前进并征服你的世界。我迫不及待地想看到您将利用这些新知识构建的惊人应用程序。下次见。

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