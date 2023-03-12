# 最佳反应动画图书馆:前 7 名图书馆比较

> 原文：<https://blog.logrocket.com/best-react-animation-library-top-7-libraries-compared/>

***编者按:*** *本帖于 2022 年 7 月 12 日更新，以反映 React 18 的当前版本和提到的所有 React 动画库，以及在对比列表中添加 Remotion 和 React Reveal。*

对于 React 前端开发人员来说，在网页上实现动画是日常工作不可或缺的一部分，从制作文本或图像动画到复杂的 3D 动画。动画可以帮助改善 React 应用程序的整体用户体验。

在本文中，我们将比较前七个 React 动画库，并评估每个库的受欢迎程度、开发人员体验、可读性、文档和软件包大小，以帮助您为下一个 React 项目选择正确的库。

我们将介绍 React 的以下动画库:

## 反作用弹簧

React Spring 是一个基于 Spring 物理学的现代动画库。它非常灵活，涵盖了用户界面所需的大部分动画。React Spring 继承了 [React Motion](https://github.com/chenglou/react-motion) 的一些属性，比如易用性、插值和性能。

要查看`react-spring`的运行，使用下面的命令之一安装库:

```
npm install react-spring

```

或者:

```
yarn add react-spring

```

接下来，添加下面的代码来创建一个`Hello React Spring`文本并制作动画:

```
import React, { useState } from 'react';
import { createRoot } from "react-dom/client";
import { useSpring, animated } from 'react-spring';
import './styles.css';

function SpringDemo() {
  const [state, toggle] = useState(true)
  const { x } = useSpring({ from: { x: 0 }, x: state ? 1 : 0, config: { duration: 1000 } })
  return (
    <div onClick={() => toggle(!state)}>
      <animated.div
        style={{
          opacity: x.interpolate({ range: [0, 1], output: [0.3, 1] }),
          transform: x
            .interpolate({
              range: [0, 0.25, 0.35, 0.45, 0.55, 0.65, 0.75, 1],
              output: [1, 0.97, 0.9, 1.1, 0.9, 1.1, 1.03, 1]
            })
            .interpolate(x => `scale(${x})`)
        }}>
        Hello React Spring
      </animated.div>
    </div>
  )
}

export default SpringDemo

```

在上面的代码中，我们首先从`react-spring`导入了`useSpring`和`animated`。接下来，使用`useState`钩子，我们初始化了一个对象`x`。使用插值，我们将多个动画值作为范围和输出，形成一个缩放到`x`的结果。

插值是一种允许您获取多个值并形成一个结果的函数。`react-spring`中的插值也可以用于 CSS 关键帧、颜色等一系列状态。大多数动画是通过将我们的动画包装在一个动画`div`组件中完成的。

`react-spring`为 React 应用的动画制作提供了一个强大的平台。它的道具和方法可读性强，容易理解。

让我们看看`react-spring`与其他 React 动画库相比如何:

*   人气:
*   文档:
    *   编写良好且对初学者友好的文档
    *   允许您从文档中复制代码段，并测试或预览 CodeSandbox
*   捆绑包大小:

## 框架运动

[Framer Motion 是一个流行的 React 动画库](https://blog.logrocket.com/framer-motion-tutorial/)，它使创建动画变得容易。它拥有一个简化的 API，抽象出动画背后的复杂性，并允许开发人员轻松创建动画。更好的是，它支持服务器端渲染、手势和 CSS 变量。

要安装 Framer Motion，请在终端中运行以下两个命令之一:

```
yarn add framer-motion

```

或者:

```
npm install framer-motion

```

接下来，添加以下代码块，向 box 组件添加一个简单的动画:

```
import React from "react";
import { motion } from "framer-motion";
import "./styles.css";

export default function App() {
  const [isActive, setIsActive] = React.useState(false);
  return (
    <motion.div
      className="block"
      onClick={() => setIsActive(!isActive)}
      animate={{
        rotate: isActive ? 180 : 360
      }}
    >
      Hello Framer motion
    </motion.div>
  );
}

```

与`react-spring`类似，Framer Motion 为动画提供了内置组件。

例如，`motion.div`用于为动画包装对象。它帮助您更快地设置组件和元素的样式，还提高了代码的可读性。这样做的一个缺点是，随着动画元素的增长，它可能会变得越来越庞大。

总的来说，Framer Motion 是一个非常强大、高度可定制且功能强大的库:

*   人气:
*   文档:
    *   易于理解和初学者友好
    *   您可以在文档中找到给定组件的源代码，甚至可以在 CodeSandbox 中查看它
*   捆绑包大小:

Framer Motion 具有比`react-spring`大得多的包大小，并且在 stars 方面没有那么受欢迎，但是它的文档更容易理解，值得在您的下一个 React 项目中考虑。

## 反应过渡基团

[React Transition Group 是一个开发工具](https://blog.logrocket.com/react-transition-group-examples-tutorial/)，它将对样板代码的需求降至最低。与许多其他 React 动画库(如`react-spring`)不同，React 过渡组使用简单的组件来定义动画。

该库本身并不定义样式，而是以有用的方式操纵 DOM，使得开发人员实现过渡和动画更加方便。换句话说，React 过渡组库提供了一种更简单的动画和过渡方法。

在您的终端中运行以下命令之一来安装`react-transition-group`:

```
npm i react-transition-group

```

或者:

```
yarn add react-transition-group

```

接下来，添加此代码块以使用 React 过渡组创建基本动画:

```
import React from "react";
import { createRoot } from "react-dom/client";
import { Transition } from "react-transition-group";
import "./styles.css";
class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      in: false
    };
  }
  componentDidMount() {}
  onClick = () => {
    this.setState(state => ({ in: !state.in }));
  };
  render() {
    return (
      <div className="App">
        <button onClick={this.onClick}>BUTTON</button>
        <Transition in={this.state.in} timeout={5000} mountOnEnter appear>
          {state => <div className={`item item--${state}`} />}
        </Transition>
      </div>
    );
  }
}

export default app;

```

React 过渡组使用内置组件(如`Transition`)向元素添加动画和过渡，从而将元素从动画中分离出来。

现在是成绩单:

*   人气:
*   文档:
    *   初学者友好的
    *   例子很清楚，用 CodeSandbox 例子可以很容易理解
*   这是给你的:

React Transition Group 还提供了对 TypeScript 的支持，可以使用下面的命令进行安装:

```
npm i @types/react-transition-group

```

React 过渡组是一个很好的动画库，并且具有非常小的包大小。这是最受欢迎的动画库之一，应该考虑用于您的下一个 React 项目。

## 反作用运动

React Motion 是一个流行的 React 动画库，它以一种更简单的方式来创建和实现逼真的动画。它利用物理定律为 React 元素创建看起来自然的动画。

通过在您的终端上运行以下命令之一来安装`react-motion`:

```
yarn add react-motion

```

或者:

```
npm i react-motion

```

要使用 React Motion 创建基本动画组件，请添加以下代码行:

```
import React from 'react'
import { StaggeredMotion, spring } from 'react-motion'
const AnimatedGridContents = props => {
  return (
    <StaggeredMotion
      defaultStyles={props.items.map(() => ({ opacity: 1, translateY: -30 }))}
      styles={prevInterpolatedStyles =>
        prevInterpolatedStyles.map((_, i) => {
          return i === 0
            ? { opacity: spring(1), translateY: spring(0) }
            : {
              opacity: prevInterpolatedStyles[i - 1].opacity,
              translateY: spring(prevInterpolatedStyles[i - 1].translateY)
            }
        })
      }
    >
      {interpolatingStyles => (
        <div className="grid">
          {interpolatingStyles.map((style, i) => (
            <div
              className="card"
              key={props.items[i]}
              style={{
                opacity: style.opacity,
                transform: `translateY(${style.translateY}px)`
              }}
            >
              {props.items[i]}
            </div>
          ))}
        </div>
      )}
    </StaggeredMotion>
  )
}

```

在上面的代码块中，使用`StaggeredMotion`，我们向卡片组件添加了过渡。使用 React Motion，您可以利用简化 React 中动画组件的 API。

现在我们来分析一下 React Motion 的流行程度和代码质量:

*   人气:
*   文档:
    *   文档是交互式的
    *   您可以从文档中复制给定组件的源代码
*   捆绑包大小:

总的来说，React Motion 是 React 应用程序的一个声音动画库，尤其是其近乎逼真的动画行为。

## 反应移动

[React Move](https://github.com/sghall/react-move) 是一个为创建漂亮的数据驱动动画而设计的库。它被设计成支持开箱即用的打字稿，[也支持自定义补间函数](https://www.adobe.com/creativecloud/video/discover/tweening.html#:~:text=Tweening%20in%20animation%20is%20a,images%20that%20go%20between%20keyframes.&text=In%20the%20early%20days%20of,frame%20animation%20between%20those%20keyframes)。

补间是“中间”的缩写，是为关键帧之间的逐帧动画生成图像的过程。React Move 还在其过渡中包含生命周期事件，并允许开发人员在动画中传递自定义补间。

让我们看看 React 如何将堆栈向上移动到其他组件库:

*   人气:
*   文档:
    *   太棒了。
    *   提供在 CodeSandbox 上测试所提供代码的机会
*   捆绑包大小:

React Move 在开发人员社区中越来越受欢迎。该库可用于各种反应动画和过渡。它的自定义补间更加出色，这使得开发人员能够在他们的 React 应用程序中自定义动画。

## 移动

[Remotion React 库创建于 2021 年](https://www.remotion.dev/)。它被设计成使用 HTML、CSS 和 JavaScript 来制作动画。

使用 Remotion，您可以创建视频，并在使用 React 组件和合成时在浏览器上播放和查看它们。Remotion 还允许 React 开发人员使用服务器端渲染和参数化来缩放视频动画和制作。

参数化是使用方程找到曲线和曲面的方程的过程，这在为视频创建网格时非常有用。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

通过在您的终端中运行以下任一命令来安装`remotion`:

```
npm init video

```

或者:

```
yarn add remotion

```

要使用 React Motion 创建基本动画组件，请添加以下代码行:

```
import { useVideoConfig } from "remotion";

export const MyVideo = () => {
  const { fps, durationInFrames, width, height } = useVideoConfig();
  const opacity =frame >= 30 ? 1 : frame / 30; 

  return (
    <div
      style={{
        flex: 1,
        textAlign: "center",
        fontSize: "9em",
        opacity: opacity,
       }}
      >
      This {width}px x {height}px video is {durationInFrames / fps} seconds long.
    </div>
  );
};

```

在上面的代码中，我们使用`opacity`添加了动画属性。我们还使用`useVideoConfig()`钩子添加了一个视频动画。当创建一个视频动画时，我们需要`width`、`height`、`durationInFrames`和`fps`参数。你可以在 Remotion 的文档中了解更多信息。

让我们看看 Remotion 与其他 React 动画库相比如何:

*   人气:
*   文档:
    *   非常简洁和优秀
    *   提供指向沙盒中测试代码的链接
*   捆绑包大小:

对于使用 React 组件的 React 视频动画，远程操作非常有用。它更多的是以其特色和低学习曲线而闪耀。

## 反应显示

[React Reveal 是一个高性能且易于使用的库](https://www.react-reveal.com/)，用于向 React 应用程序添加动画。

与大多数动画库不同，React Reveal 仅在向下滚动到动画元素时显示动画和效果，而不是在加载时显示。这通过允许动画组件延迟加载来提高页面速度。

同样重要的是要注意到 [React Reveal 支持高阶组件](https://www.react-reveal.com/docs/with-reveal/)的一些特性。

您可以使用以下命令安装 React Reveal:

```
npm install react-reveal --save

```

要创建基本动画，请添加以下代码行:

```
import "./styles.css";
import React,  { useState } from "react";
import Zoom from "react-reveal/Zoom";

function AnExample(props) {
  let [reveal, setReveal] = useState(false);

  function toggleShow() {
    setShow(!reveal);
  }
  return (
    <div>
      <Zoom when={reveal}>
        <h3>This is an example of React reveal</h3>
      </Zoom>
      <button type="button" onClick={toggleReveal}>
        Toggle this button to reveal {reveal ? "Hide this button" : "Show the button"}
      </button>
    </div>
  );
}

export default function Nav() {
  return (
    <div className="Nav">
      <AnExample />
    </div>
  );
}

```

在上面的代码块中，使用 React Reveal 中的`Zoom`,我们创建了一个基本的动画，显示了按钮被切换时的动画。

让我们来看看 React Reveal 的受欢迎程度和文档:

*   人气:
*   文档:
    *   写得很好，很简洁
    *   包含几个代码块和示例
*   捆绑包大小:

虽然这是一个较新的动画库，但正如你从它日益受欢迎的程度可以看到的，React Reveal 已经存在了！

## 结论

无论您正在进行什么项目，都有许多动画库可以帮助您轻松快速地创建用户友好的动画和过渡。

在这份名单之外，截至本文撰写之时，React Spring 在 GitHub 上拥有最多的明星，而 React Transition Group 在 NPM 的周下载量方面胜出。但是，其他库可能会提供您需要的特定功能，例如使用 React Move 的补间动画或使用 React Motion 的逼真动画。

许多这些库都是可定制的，包含了很棒的内置特性和变化。希望有了这个比较列表，您可以为下一个 React 应用程序选择正确的库。

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