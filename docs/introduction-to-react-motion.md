# React Motion 简介

> 原文：<https://blog.logrocket.com/introduction-to-react-motion/>

React Motion 是 React 应用程序的动画库，可以轻松创建和实现逼真的动画。在本指南中，我们将演示如何安装该库，并分享一些基本技巧来帮助您为 React 项目构建[看起来自然、基于物理的动画](https://blog.logrocket.com/5-react-motion-use-cases-with-examples-78fc07104d58/)。

## 安装反应运动

通过运行以下命令创建一个新的 React 项目:

```
npx create-react-app intro-react-motion

```

要安装 React Motion，请在项目根目录下运行以下命令。

```
npm i react-motion

```

## 了解出口

`react-motion`库导出了以下内容。

*   `spring` —一个帮助器函数，指示组件如何动画化
*   `presets` —预定义动画属性的对象
*   `Motion` —一个用于动画组件的组件
*   `StaggeredMotion` —一个组件，用于动画化那些动画相互依赖的组件
*   `TransitionMotion` —用于动画显示组件安装和卸载的组件

为了简洁起见，本指南将集中于`spring`、`presets`和`Motion`。

## 助手:`spring()`和`presets`

`spring()`辅助函数定义如何从初始样式值到目标值制作动画。它接受两个参数:值和一个选项动画配置参数。

例如，`spring(10, { stiffness: 130, damping: 42 })`将该值设置为 10，刚度为 130，阻尼为 42。`stiffness`和`damping`是定义动画行为的动画属性。不要担心，不理解这些属性是如何工作的是正常的。

`presets`属性被用作动画配置。比如`spring(25, presets.wobbly)`。其他预设值包括`presets.gentle`、`presets.noWobble`、`presets.stiff`。

## `<Motion />`组件

`<Motion />`组件接受两个道具:`defaultStyle`和`style`。`defaultStyle`属性定义了样式对象的初始值。`style`道具是一个定义任意给定点的样式值的对象。使用`spring()`辅助函数确定`style`属性的值。如果`defaultStyle`是原始样式，那么`style`是组件动画的最终样式。

组件期望一个函数作为它的子道具，这意味着它使用渲染道具模式。该函数接收一个插值样式对象，该对象包含动画完成前任何给定时间的样式值。

```
<<Motion
  defaultStyle={{
    scale: 0,
    translateX: -300
  }}
  style={{
    scale: spring(1),
    translateX: spring(0, presets.stiff)
  }}
>
  {interpolatesStyles => <>{/* React Component */}</>}
</Motion>

```

## 对动作做出反应

让我们看一个基本的例子。

在组件文件的顶部，从`react-motion`库中导入`Motion`、`spring`和`presets`，以便在组件中使用它们。

```
import { Motion, spring, presets } from "react-motion";

```

在组件中创建一个`<h1>`元素，它将在`App.js`中被动画化。

```
// ...
function App() {
  return (
    <div className="App">
      <div>
        <h1>Basic Animation Example</h1>
      </div>
    </div>
  );
}
// ...

```

现在用`<Motion />`组件包装你的组件，并从 render prop 函数返回`<h1>`组件。通过`defaultStyle`道具中的`{ opacity: 0, translateY: 30 }`。在`style`道具中，使用`spring()`辅助函数插入样式值。

```
// ...
<Motion
  defaultStyle={{
    opacity: 0,
    translateY: 30
  }}
  style={{
    opacity: spring(1),
    translateY: spring(0, presets.wobbly)
  }}
>
  {interpolatedStyles => (
    <div
      style={{
        transform: `translateY(${interpolatedStyles.translateY}px)`,
        opacity: interpolatedStyles.opacity
      }}
    >
      <h1>Basic Animation Example</h1>
    </div>
  )}
</Motion>
// ...

```

下面是最终的代码:

```
import React from "react";
import "./styles.css";
import { Motion, spring, presets } from "react-motion";

function App() {
  return (
    <div className="App">
      <Motion
        defaultStyle={{
          opacity: 0,
          translateY: 30
        }}
        style={{
          opacity: spring(1),
          translateY: spring(0, presets.wobbly)
        }}
      >
        {interpolatedStyles => (
          <div
            style={{
              transform: `translateY(${interpolatedStyles.translateY}px)`,
              opacity: interpolatedStyles.opacity
            }}
          >
            <h1>Basic Animation Example</h1>
          </div>
        )}
      </Motion>
    </div>
  );
}

export default App;

```

运行下面的命令来查看上面的代码。

```
npm start

```

一旦组件装载到 DOM 上，动画就会开始。现在，让我们看看如何通过点击按钮来触发动画。

## 使用按钮触发动画

使用 state，您可以动态添加样式来插入样式值。在变量中，存储动画的初始样式。

```
function App() {
  const [startAnimation, setAnimation] = useState(false);
  const initialStyle = { opacity: 0, translateY: 30 };
  // ...
}

```

在`<Motion />`组件中，您不必指定`defaultStyle`属性，因为`style`属性将动态变化。

```
// ...
<Motion
  style={
    startAnimation
      ? {
          opacity: spring(1),
          translateY: spring(0, presets.wobbly)
        }
      : initialStyle
  }
>
  {interpolatedStyles => (
    <div
      style={{
        transform: `translateY(${interpolatedStyles.translateY}px)`,
        opacity: interpolatedStyles.opacity
      }}
    >
      <h1>Triggered Animation</h1>
    </div>
  )}
</Motion>
// ...

```

添加两个按钮:一个触发动画，另一个重置动画。

```
// ...
<button onClick={() => setAnimation(true)}>Trigger Animation</button>
<button onClick={() => setAnimation(false)}>Reset Animation</button>
// ...

```

当`startAnimation`状态设置为`true`时，`style`道具将获得初始样式值。当设置为`false`时，`style`道具将拥有最终值。

您的`App.js`应该如下所示。

```
import React, { useState } from "react";
import "./styles.css";
import { Motion, spring, presets } from "react-motion";

export default function App() {
  const [startAnimation, setAnimation] = useState(false);
  const initialStyle = { opacity: 0, translateY: 30 };
  return (
    <div className="App">
      <Motion
        style={
          startAnimation
            ? {
                opacity: spring(1),
                translateY: spring(0, presets.wobbly)
              }
            : initialStyle
        }
      >
        {interpolatedStyles => (
          <div
            style={{
              transform: `translateY(${interpolatedStyles.translateY}px)`,
              opacity: interpolatedStyles.opacity
            }}
          >
            <h1>Triggered Animation</h1>
          </div>
        )}
      </Motion>
      <button onClick={() => setAnimation(true)}>Trigger Animation</button>
      <button onClick={() => setAnimation(false)}>Reset Animation</button>
    </div>
  );
}

```

## 通过`styled-components`使用反应运动

您可以将`react-motion`与 React 的任何其他 UI 库一起使用。让我们看看如何将`react-motion`用于`styled-components`库。

通过在项目根目录下运行以下命令来安装`styled-components`。

```
npm i styled-components

```

如下创建样式化的`<Title />`组件。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
// ..
import styled from "styled-components";

const Title = styled.h1`
  color: #007bff;
  font-size: 32px;
  ${props =>
    `transform: translateY(${props.translateY}px); 
   opacity: ${props.opacity};
  `}
`;

// ..

```

与上面的例子类似，从 render prop 函数返回`<Title />`组件。将插值作为道具传递给`<Title />`组件。

```
<Motion
  style={
    startAnimation
      ? {
          opacity: spring(1),
          translateY: spring(0, presets.wobbly)
        }
      : initialStyle
  }
>
  {interpolatedStyles => (
    <Title
      opacity={interpolatedStyles.opacity}
      translateY={interpolatedStyles.translateY}
    >
      Triggered Animation
    </Title>
  )}
</Motion>

```

您完成的`App.js`应该如下所示。

```
import React, { useState } from "react";
import "./styles.css";
import { Motion, spring, presets } from "react-motion";
import styled from "styled-components";

const Title = styled.h1`
  color: #007bff;
  font-size: 32px;
  ${props =>
    `transform: translateY(${props.translateY}px); 
   opacity: ${props.opacity};
  `}
`;

export default function App() {
  const [startAnimation, setAnimation] = useState(false);
  const initialStyle = { opacity: 0, translateY: 30 };
  return (
    <div className="App">
      <Motion
        style={
          startAnimation
            ? {
                opacity: spring(1),
                translateY: spring(0, presets.wobbly)
              }
            : initialStyle
        }
      >
        {interpolatedStyles => (
          <Title
            opacity={interpolatedStyles.opacity}
            translateY={interpolatedStyles.translateY}
          >
            Triggered Animation
          </Title>
        )}
      </Motion>
      <button onClick={() => setAnimation(true)}>Trigger Animation</button>
      <button onClick={() => setAnimation(false)}>Reset Animation</button>
    </div>
  );
}

```

无论您使用哪个库，`react-motion`都将工作，只要该库支持自定义样式。

如果您遇到错误、贬值警告或不能按预期运行的情况，通过用以下版本替换您在`package.json`文件中的依赖项，恢复到这些库的原始版本。

```
//...

"dependencies": {
    "@testing-library/jest-dom": "^4.2.4",
    "@testing-library/react": "^9.5.0",
    "@testing-library/user-event": "^7.2.1",
    "react": "^16.13.0",
    "react-dom": "^16.13.0",
    "react-motion": "^0.5.2",
    "react-scripts": "3.4.0",
    "styled-components": "^5.0.1"
 }

//...

```

然后运行:

```
npm i

```

这将安装与测试这些示例时完全相同的依赖项。

## 结论

React Motion 是 React 中最容易制作组件动画的动画库之一。这只是对`react-motion`库的简单介绍。下一步，我会推荐看看像`<StaggeredMotion />`和`<TransitionMotion />`这样的组件，它们类似于`<Motion />`组件，但是有一个更复杂动画的高级实现。

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