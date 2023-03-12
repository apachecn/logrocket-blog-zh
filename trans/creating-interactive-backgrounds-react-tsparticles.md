# 在与粒子反应中创建交互式背景

> 原文：<https://blog.logrocket.com/creating-interactive-backgrounds-react-tsparticles/>

从 [3D 插图](https://threejs-journey.com/)到[动画背景](https://nova.app/)和[基于滚动的故事讲述](https://www.apple.com/macbook-pro/)——如果你想在网络上脱颖而出，互动内容是必不可少的解决方案。虽然它确实需要更多的时间和经验来很好地实现，但结果可能真的令人震惊。

也就是说，在这篇文章中，我想向你展示一个在你的网站上实现交互式背景的捷径，只需要一点点努力。这个快捷方式就是[ts particles](https://particles.js.org/)——一个帮助你创建交互式粒子背景的库。在它的帮助下，您将能够立即创建令人惊叹的视觉效果！

![The tsParticles landing page](img/eef497f77664a2fb9de36c707a4a7e59.png)

## 什么是粒子

tsParticles 是 [particles.js](https://vincentgarreau.com/particles.js/) 的精神继承者，后者是一个功能相似的老库。这些改进包括 TypeScript 的使用、与领先框架的第一方集成以及一些新特性。除此之外，该库向后兼容 particles.js —所有配置选项都是相同的。

该库使用 HTML Canvas 来创建比屏幕上浮动的点更高级的背景，一些示例包括用于底层图像的粒子遮罩，以产生良好的显示效果，或者用于在矢量形状内组织粒子的多边形遮罩。您还可以为粒子使用自定义图形，应用自定义动画，管理由不同用户操作触发的效果，等等！

总的来说，尽管只使用了一个 config 对象，这个库的通用性和可配置性令人难以置信。此外，TypeScript 类型和专用集成不仅适用于 React、Vue 和 Angular，还适用于像 Svelte 和 Solid 这样的框架，这是一种很好的开发体验。

## 创建交互式背景

为了展示如何使用粒子，我们将探索一些独特的，互动的背景。这应该让您很好地了解什么是可能的，并让您了解一些配置选项。

### 设置

在这个演示中，我们将使用 React 和官方的[ts 粒子 React 组件](https://github.com/matteobruni/tsparticles/tree/main/components/react)。下面的命令设置一个 React Vite 模板，安装必要的依赖项，并运行开发服务器，这样您只需几秒钟就可以开始工作了。

```
npm init [email protected] backgrounds --template react-ts
cd backgrounds
npm install react-tsparticles
npm run dev

```

要开始使用 tsParticles，请导入并渲染`src/app.tsx`文件中的`Particles`组件。

```
import Particles, { ISourceOptions } from "react-tsparticles";

const App = () => {
  const options: ISourceOptions = {
    // ...
  };

  return <Particles options={options} />;
};

export default App;

```

该组件接受几个道具——其中最重要的是`options`,因为它负责配置粒子视觉效果的几乎所有方面。[其他道具](https://github.com/matteobruni/tsparticles/tree/main/components/react#props%5C)配置画布或包装元素，提供对 tsParticles 实例的访问以进行额外的控制，等等。

### 配置选项

关注选项，让我们使用它们来创建我们的第一个交互背景。

```
// ...
const options: ISourceOptions = {
  background: {
    color: "#0d47a1",
  },
  interactivity: {
    events: {
      onClick: {
        enable: true,
        mode: "push",
      },
      onHover: {
        enable: true,
        mode: "repulse",
      },
      resize: true,
    },
    modes: {
      bubble: {
        distance: 400,
        duration: 2,
        opacity: 0.8,
        size: 40,
      },
      push: {
        quantity: 4,
      },
      repulse: {
        distance: 200,
        duration: 0.4,
      },
    },
  },
  particles: {
    color: {
      value: "#ffffff",
    },
    links: {
      color: "#ffffff",
      distance: 150,
      enable: true,
      opacity: 0.5,
      width: 1,
    },
    collisions: {
      enable: true,
    },
    move: {
      direction: "none",
      enable: true,
      outMode: "bounce",
      random: false,
      speed: 6,
      straight: false,
    },
    number: {
      density: {
        enable: true,
        value_area: 800,
      },
      value: 80,
    },
    opacity: {
      value: 0.5,
    },
    shape: {
      type: "circle",
    },
    size: {
      random: true,
      value: 5,
    },
  },
};
// ...

```

上述配置以互连点的形式创建了最容易识别的基于粒子的背景之一:

见 [CodePen](https://codepen.io) 上阿雷克·纳沃([@阿雷克那沃](https://codepen.io/areknawo) )
的笔 [tsParticles 浮游粒子](https://codepen.io/areknawo/pen/yLzRmOx)。

配置 tsParticles 相对简单，由于 TypeScript 类型化，您不必记忆或阅读文档来了解所有选项。
以下是一些帮助您入门的选项的详细信息:

*   `background`配置粒子后面的一切。您可以使用各种格式的纯色(十六进制、RGB、HSL 等。)、图像、不同的不透明度等等
*   `interactivity`控制当用户点击或悬停在背景上时会发生什么
*   `events`定义如何处理选定的事件
*   `modes`配置用于事件处理的动作
*   `particles`对控制粒子和相关视觉效果显示方式的所有属性进行分组。从这里，您可以配置一切，从粒子的颜色和运动行为，到它们之间的连接线

tsParticles 提供了更多的选项，所有这些都可以通过 TypeScript 支持的自动完成或官方文档来探索。

## 创造五彩纸屑的效果

现在我们对粒子的工作原理有了基本的了解，让我们来创造一些更有趣的东西。我们将实现一个五彩纸屑效果，类似于你可以在苹果 iMessages 中发送的内容。

我们将使用粒子发射器来实现五彩纸屑从一个点爆炸的效果，而不仅仅是粒子四处漂浮。让我们使用下面的配置创建一个。

```
// ...
const options: ISourceOptions = {
  fullScreen: true,
  fpsLimit: 60,
  detectRetina: true,
  emitters: {
    startCount: 0,
    position: { x: 50, y: 50 },
    size: {
      width: 0,
      height: 0,
    },
    rate: {
      delay: 0,
      quantity: 10,
    },
    life: {
      duration: 0,
      count: 1,
    },
  },
};
// ...

```

使用上述`emitters`部分的参数，我们在全屏画布的正中央创建一个不可见的发射器。`life`对象指定在无限长的时间内只有一个发射器。此外，根据`rate`对象的值，发射器将无延迟地发射十个粒子。

有了这样的设置，剩下要做的就是指定发射粒子的外观和行为。为此，在`particles`部分设置以下选项。

```
// ...
const options: ISourceOptions = {
  // ...
  emitters: {
    // ...
  },
  particles: {
    number: {
      value: 0,
    },
    color: {
      value: ["#FF5A86", "#953AFE", "#FFC326", "#46C0FF"],
    },
    shape: {
      type: ["square", "circle"],
    },
    opacity: {
      value: 1,
      animation: {
        enable: true,
        minimumValue: 0,
        speed: 0.5,
        startValue: "max",
        destroy: "min",
      },
    },
    size: {
      value: 5,
    },
    links: {
      enable: false,
    },
    life: {
      duration: {
        sync: true,
        value: 3,
      },
      count: 1,
    },
    move: {
      angle: {
        value: 45,
        offset: 0,
      },
      drift: {
        min: -0,
        max: 0,
      },
      enable: true,
      gravity: {
        enable: true,
        acceleration: 20,
      },
      speed: 90,
      decay: 1 - 0.9,
      direction: -90,
      random: true,
      straight: false,
      outModes: {
        default: "none",
        bottom: "destroy",
      },
    },
    rotate: {
      value: {
        min: 0,
        max: 360,
      },
      direction: "random",
      animation: {
        enable: true,
        speed: 60,
      },
    },
    tilt: {
      direction: "random",
      enable: true,
      value: {
        min: 0,
        max: 360,
      },
      animation: {
        enable: true,
        speed: 60,
      },
    },
    roll: {
      darken: {
        enable: true,
        value: 25,
      },
      enable: true,
      speed: {
        min: 15,
        max: 25,
      },
    },
    wobble: {
      distance: 20,
      enable: true,
      speed: {
        min: -15,
        max: 15,
      },
    },
  },
};
// ...

```

一般来说，上述配置使粒子模仿五彩纸屑。它们有不同的颜色、形状，可以移动、旋转、倾斜等等。这里最重要的选项是:

*   `number.value`，控制漂浮颗粒的数量(应为`0`)
*   `move.gravity`，这产生了一个将粒子拉下来的力

下面是最终结果:

请看 [CodePen](https://codepen.io) 上阿莱克·纳沃([@阿莱克纳沃](https://codepen.io/areknawo) )
的笔 [tsParticles 五彩纸屑](https://codepen.io/areknawo/pen/jOGeMXR)。

事先调整

### 正如你所看到的，粒子的结构变得非常复杂。为了解决这个问题，有一个预置的功能，这样你就可以共享和重用它们。

您还可以使用任何官方或社区预设，并在其上应用您的自定义选项，以更快地实现您想要的效果。

为了向您展示如何使用预设，我们将使用海葵预设，它从画布的中心产生触手状的粒子。

首先，从 npm 安装[预置包](https://www.npmjs.com/package/tsparticles-preset-sea-anemone):

要加载预设，您需要访问 tsParticles 实例。为此，使用`init`属性设置在初始化 tsParticles 时使用的回调函数。回调将接收所述实例作为参数。

```
npm install tsparticles-preset-sea-anemone

```

在选项中，确保通过名称指定`preset`,并包含您想要定制的任何其他属性。默认情况下，预设会产生以下效果:

```
import Particles, { ISourceOptions, Main } from "react-tsparticles";
import { loadSeaAnemonePreset } from "tsparticles-preset-sea-anemone";

const App = () => {
  const options: ISourceOptions = {
    preset: "seaAnemone",
  };
  const initialize = (instance: Main) => {
    loadSeaAnemonePreset(instance);
  };

  return <Particles options={options} init={initialize} />;
};

export default App;

```

参见 [CodePen](https://codepen.io) 上阿雷克·纳沃([@阿雷克那沃](https://codepen.io/areknawo) )
的笔 [tsParticles 海葵预置](https://codepen.io/areknawo/pen/vYeVoYL)。

See the Pen [tsParticles Sea Anemone Preset](https://codepen.io/areknawo/pen/vYeVoYL) by Arek Nawo ([@areknawo](https://codepen.io/areknawo))
on [CodePen](https://codepen.io).

正如我们之前提到的，tsParticles 还允许您定义自己的预设。为此，只需定义预设选项并创建一个加载函数，该函数接受您的 tsParticles 实例作为参数。在函数内部，使用实例的`addPreset`方法注册预置，传递您的自定义名称和定义的选项。

使用这样的预置和使用第三方的一模一样。

```
// ...
const options: ISourceOptions = {
  // preset options
};
const loadCustomPreset = (main: Main) => {
  main.addPreset("customPreset", options);
};

```

其他自定义

```
// ...

const App = () => {
  const options: ISourceOptions = {
    preset: "customPreset",
  };
  const initialize = (instance: Main) => {
    loadCustomPreset(instance);
  };

  return <Particles options={options} init={initialize} />;
};

```

### 最重要的是，tsParticles 允许通过插件和高级配置选项进行更高级的定制。

插件与预设非常相似——它是一个接受粒子实例并使用一种可用方法改变它的函数。除了用`addPreset`定义预设，你还可以用`addShape`添加自定义粒子形状，或者用`addInteractor`进行自定义交互。

为了给你一个创建你自己的插件是多么容易的例子，让我们创建一个给粒子添加六边形的插件。

更多来自 LogRocket 的精彩文章:

* * *

### 该形状是使用 HTML Canvas 上下文和提供的粒子配置绘制的，类似于我们的五彩纸屑演示中的样子。

* * *

```
const loadHexagonShape = (main: Main) => {
  main.addShape("hexagon", (context, particle, radius) => {
    const angle = (2 * Math.PI) / 6;

    context.beginPath();
    for (let i = 0; i < 6; i++) {
      context.lineTo(
        radius * Math.cos(angle * i),
        radius * Math.sin(angle * i)
      );
    }
    context.closePath();
    context.stroke();
  });
};

```

请看 [CodePen](https://codepen.io) 上阿雷克·纳沃([@阿雷克那沃](https://codepen.io/areknawo) )
的笔 [tsParticles 五彩六边形纸屑](https://codepen.io/areknawo/pen/zYEmVqR)。

See the Pen [tsParticles Colorful Hexagon Confetti](https://codepen.io/areknawo/pen/zYEmVqR) by Arek Nawo ([@areknawo](https://codepen.io/areknawo))
on [CodePen](https://codepen.io).

除了插件，还有许多高级配置选项可以帮助你达到想要的效果。五彩纸屑演示中使用的`emitters`属性就是其中之一，还有很多！值得注意的例子包括用于指定粒子应该覆盖什么矢量形状的`polygon`属性，以及用于创建发射器的反面的`absorbers`属性——一个吸收附近粒子的类似黑洞的实体。

在您的配置中添加以下位，以在特定位置创建一个吸收器，当它吸收更多的粒子时，它的大小会增长(达到某个极限)。

下面是我们将它应用于五彩纸屑演示时的情况。

```
// ...
const options: ISourceOptions = {
  // ...
  absorbers: {
    color: "#ffffff",
    position: { x: 70, y: 50 },
    size: {
      value: 50,
      limit: 150
    }
  }
  // ...
};
// ...

```

请看 [CodePen](https://codepen.io) 上阿莱克·纳沃([@阿莱克纳沃](https://codepen.io/areknawo) )
的笔 [tsParticles 五彩六边形纸屑](https://codepen.io/areknawo/pen/zYEmVqR)。

See the Pen [tsParticles Colorful Hexagon Confetti](https://codepen.io/areknawo/pen/zYEmVqR) by Arek Nawo ([@areknawo](https://codepen.io/areknawo))
on [CodePen](https://codepen.io).

总的来说，这个库肯定有很多定制的可能性。正因为如此，当人们试图达到某种特定的效果时，很容易迷失方向。在这种情况下，我建议查看库作者的 [CodePen 集合](https://codepen.io/collection/DPOage)，其中包含了各种可以使用并从中获取灵感的配置。以下是我最喜欢的几个:

参见 [CodePen](https://codepen.io) 上 Matteo Bruni([@ matteobruni](https://codepen.io/matteobruni))
的 Pen [tsParticles 多路径多边形遮罩](https://codepen.io/matteobruni/pen/MWaePMa)。

See the Pen [tsParticles polygon mask with multiple paths](https://codepen.io/matteobruni/pen/MWaePMa) by Matteo Bruni ([@matteobruni](https://codepen.io/matteobruni))
on [CodePen](https://codepen.io).

见 [CodePen](https://codepen.io) 上 Matteo Bruni([@ matteobruni](https://codepen.io/matteobruni))
的笔 [tsParticles 多边形遮罩](https://codepen.io/matteobruni/pen/yLNpgKW)。

See the Pen [tsParticles Polygon Mask](https://codepen.io/matteobruni/pen/yLNpgKW) by Matteo Bruni ([@matteobruni](https://codepen.io/matteobruni))
on [CodePen](https://codepen.io).

参见 Matteo Bruni([@ matteobruni](https://codepen.io/matteobruni))
上 [CodePen](https://codepen.io) 的 Pen [tsParticles 外置柏林噪声发生器](https://codepen.io/matteobruni/pen/MWKaRYy)。

结论

所以，如你所见，粒子库的可能性几乎是无限的。可以肯定地说，你可以定制这个库的几乎所有方面。然而，最好的一点是它是渐进的——你可以根据自己的需要定制，而库将永远支持你。

## 有了 tsParticles 这样的库，给你自己的网站带来互动的视觉效果变得前所未有的简单。现在轮到你好好利用它，创造一些特别的东西了！

[LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

## .

LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。

The LogRocket Redux middleware package adds an extra layer of visibility into your user sessions. LogRocket logs all actions and state from your Redux stores.

Modernize how you debug your React apps — [start monitoring for free](https://lp.logrocket.com/blg/react-signup-general).