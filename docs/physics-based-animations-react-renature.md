# 在 React with renature 中创建基于物理的动画

> 原文：<https://blog.logrocket.com/physics-based-animations-react-renature/>

## 介绍

`renature`是 React 基于物理学的动画库，灵感来自自然世界。它从我们宇宙的物理学中获得灵感，并模拟真实世界的力，如重力、摩擦、空气阻力和流体动力学。

`renature`受到其他流行的基于物理的动画库的影响，如[的 react-spring](https://www.react-spring.io/) 和[的 Framer Motion](https://www.framer.com/motion/) 的元素。在本文中，我们将通过构建几个例子和应用程序来学习如何在`renature`中创建动画。

## 基本实现

首先:通过运行下面的命令安装`renature`包:

```
npm install --save renature
# or
yarn add renature
```

提供了六个我们可以用来制作动画的钩子。前三种是`useFriction`、`useGravity`和`useFluidResistance`挂钩。这些钩子激活了单个元素的属性。

它们都以类似的方式工作，但正如它们的名字所暗示的，它们所模仿的自然力量和它们所依赖的物理学会有所不同。此外，根据您使用的力的不同，配置对象也不同。

还有`useFrictionGroup`、`useGravityGroup`和`useFluidResistanceGroup`钩子，我们可以用它们同时制作一组元素属性的动画。

让我们看看使用`useFriction`钩子的基本实现。

```
import { useFriction } from "renature";
import "./styles.css";

export default function App() {
  const [props] = useFriction({
    from: {
      transform: "translateX(100px)"
    },
    to: {
      transform: "translateX(300px)"
    },
    config: {
      mu: 0.2,
      mass: 20,
      initialVelocity: 5
    },
    repeat: Infinity
  });

  return (
    <div className="App">
      <div {...props} className="box" />
    </div>
  );
}
```

如果你熟悉 react-spring，你会注意到`renature`有一个类似的声明式 API。`renature`中的每一个钩子都需要`from`和`to`属性，在这些属性中我们描述了我们想要动画化的 CSS 状态。如果在`from`中为一个属性设置了一个值，而在`to`中没有，那么这个属性将被忽略，并且不会被激活。

有时，我们可能希望我们的动画不停顿地无限运行。我们可以通过将`repeat: Infinity`应用到我们的动画配置中来做到这一点。

因为我们使用了`useFriction`钩子，所以我们传入了相应的物理参数。顾名思义，`useFriction`挂钩是仿照摩擦力设计的。这些参数是:

*   `mu`，即动摩擦系数
*   `mass`，即运动物体的质量
*   `initialVelocity`，这是运动的初始速度

关于`renature`的奇妙之处在于，你不需要成为一个科学大师来制作令人敬畏的动画。

## 用`renature`制作多个属性的动画

所以我们已经看到了`useFriction`钩子是如何工作的，以及如何激活一个元素的`transform`属性。必要时，我们还可以同时制作多个属性的动画:

```
import { useFriction } from "renature";
import "./styles.css";

export default function App() {
  const [props] = useFriction({
    from: {
      transform: "translateX(100px), rotate(0deg)",
      background: "red",
      borderRadius: "0%"
    },
    to: {
      transform: "translateX(300px), rotate(360deg)",
      background: "steelblue",
      borderRadius: "50%"
    },
    config: {
      mu: 0.2,
      mass: 20,
      initialVelocity: 5
    },
    repeat: Infinity
  });

  return (
    <div className="App">
      <div {...props} className="box" />
    </div>
  );
}
```

若要制作元素的多个属性的动画，请添加要制作动画的元素的更多属性。

## 在组中创建动画

我们可能希望同时激活多个元素。成组的动画允许我们指定要制作动画的元素数量，并为每个元素单独设置配置。

```
import { useGravityGroup } from "renature";
import "./styles.css";
export default function App() {
  const [nodes] = useGravityGroup(3, (i) => ({
    from: {
      transform: "translate(0px, 0px) scale(1) skewY(0deg)",
      opacity: 0
    },
    to: {
      transform: "translate(20px, 20px) scale(1.2) skewY(5deg)",
      opacity: 1
    },
    config: {
      moverMass: 10000,
      attractorMass: 10000000000000,
      r: 10
    },
    repeat: Infinity,
    delay: i * 500
  }));
  return (
    <div className="App">
      <div className="container">
        {nodes.map((props, i) => {
          return <div className="box" key={i} {...props} />;
        })}
      </div>
    </div>
  );
}
```

我们使用`useGravityGroup`来创建上面的分组动画。所有成组的动画挂钩都采用相似的形式:

```
const [props] = use<Force>Group(n: number, fn: (index: number) => Config);
```

您可以通过在动画配置中指定`delay`属性来延迟`renature`中的动画。`delay`期待一个以毫秒为单位的数字，一旦指定的`delay`过去，就会启动动画。这最常用于分组动画，在这种情况下，您希望以固定的间隔交错设置子动画。

## 控制动画状态

我们来看看如何在`renature`中控制动画状态。我们可以启动、停止、暂停、延迟和运行动画特定的次数。我们已经了解了如何重复和延迟动画。

为了控制动画状态，`renature`提供了一个`controller` API。`controller`是由`renature`钩子返回的第二个对象，它有三个方法——`start`、`pause`和`stop`——用于与动画的播放状态进行交互。

```
import { useFriction } from "renature";
import "./styles.css";
import Button from "./Button";

export default function App() {
  const [props, controller] = useFriction({
    from: {
      transform: "translateY(0px)",
      opacity: 1,
      borderRadius: "10%"
    },
    to: {
      transform: "translateY(50px)",
      opacity: 0,
      borderRadius: "50%"
    },
    repeat: Infinity,
    pause: true //Signal that the animation should not run on mount.
  });

  return (
    <div className="App">
      <div className="btn-box">
        <Button action={controller.start} text="start" />
        <Button action={controller.pause} text="pause" />
        <Button action={controller.stop} text="stop" />
      </div>
      <div className="box" {...props} />
    </div>
  );
}
```

我们使用`controller` API 中可用的`controller.start`方法来启动动画。为此，我们需要在动画配置中包含`pause: true`,以防止动画立即在挂载上运行。

为了暂停正在运行的动画，我们使用了`controller.pause`方法。此方法将停止帧循环，但保留动画状态。这意味着我们可以使用`controller.start`随时恢复动画。

要停止一个正在运行的动画，我们使用`controller.stop`方法。不像`controller.pause`，这将破坏动画状态，所以我们应该只在我们确定想要动画结束时使用它。

## `renature`演示

现在我们知道了如何使用`renature`创建动画，让我们看看一些现实世界的应用程序。

### 卡片悬停演示

你是否曾经将鼠标悬停在一张卡片上，看到图像沿着其容器的宽度和高度放大或缩小？让我们使用`renature`重新创建那个效果。

这是我们将要构建的沙盒:

虽然 CSS 不是这个主题的重点，但我们需要一些 CSS 来实现它应有的效果。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
.card-box {
  width: 200px;
  border: 1px solid grey;
  overflow: hidden;
  margin-bottom: 2rem;
}
```

我们必须为`card-box` div 设置一个`overflow: hidden`,以确保当图像缩放时，它仍然被限制在它的容器中。解释完之后，让我们回到`renature`。

使用我们所学的关于控制动画状态的知识，每当我们悬停在`img-box` div 内外时，我们就调用`controller.start`和`controller.pause`方法。

```
export default function Card({ imgUrl, props, controller }) {
  return (
    <div className="card-box">
      <div
        className="img-box"
        {...props}
        onMouseEnter={controller.start}
        onMouseLeave={controller.pause}
      >
        <img src={imgUrl} />
      </div>

      //more stuff below...
```

### 通知 toast 演示

通知吐司是应用程序中常用的组件之一。让我们使用`renature`创建一个。

这是我们将要构建的沙盒:

对于 CSS，我们必须将 toast 的不透明度设置为 0，以便它只在按钮被单击时显示。

```
return (
    <div className="App">
      <button onClick={controller.start}>show toast</button>
      <Toast props={props} />
    </div>
);
```

当按钮被点击时，我们调用`controller.start`方法。这将启动动画，吐司下降到视图中。

## 无障碍问题

不是每个人都喜欢装饰性的动画或过渡，一些用户在面对视差滚动、缩放效果等时会经历明显的精神和身体上的困难。因此，我们必须确保我们的动画不会给应用程序用户带来可访问性问题。

在`renature`的配置对象中，我们可以包含一个可选的`reducedMotion`属性。如果未指定，并且最终用户喜欢减少运动，动画元素将立即设置为`to`状态，以避免潜在的有害动画。

## 结论

虽然我在本文中做了一些演示，展示了如何在现实世界的应用程序中使用`renature`,但如果团队能包含更多可能对开发人员有用的实用演示，那就太棒了。不管怎样，`renature`是一个非常棒的基于物理学的动画库。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)