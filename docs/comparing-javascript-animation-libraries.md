# 比较 JavaScript 动画库- LogRocket 博客

> 原文：<https://blog.logrocket.com/comparing-javascript-animation-libraries/>

如果你今天做任何类型的前端 web 开发，动画可能是你日常工作的一部分，或者至少是你正在做的项目的一部分。近年来，JavaScript 中的动画已经有了很大的发展，从制作文本或图像动画到使用像 [WebGL](https://get.webgl.org/) 这样的工具制作成熟的 3D 动画。

有很多 JavaScript 框架提供动画功能。还有几个库与 canvas 和 WebGL 一起创建交互式体验。

在这篇文章中，我将对四个不同的 JavaScript 动画库进行比较。我在这里列出的库绝不是唯一的选择，但希望它们能向您展示在您的项目中添加任何动画时可以遵循的模式。

在这篇文章中，我们将关注以下内容:

我将用一个 [React](https://reactjs.org/) 项目来介绍实现，但是您应该能够遵循任何前端框架(或者普通 JavaScript)的类似模式。你可以在这里查看我建的[项目](https://github.com/andrewevans0102/compare-javascript-animation-libraries)。我还为每个库构建了示例组件，你可以在这里看到。

在接下来的部分中，我将讨论如何使用上述每个库。我将在一个 [React](https://reactjs.org/) 项目中介绍基础知识及其实现。我还会提供一些我在与他们一起工作时发现的优点和缺点。

这篇文章假设读者对 React 和 JavaScript 项目有所了解。我讨论的所有库都可以应用于任何 JavaScript 框架，这只是正确导入库并调用所讨论的 API 的问题。

## Anime.js

Anime.js 提供了一个基本的 API，可以让你制作几乎任何你能想到的动画。使用 Anime.js，您可以做基本的动画，来回移动对象，或者可以做更高级的动画，用动作重新设计组件的样式。

Anime.js 还提供了对时间线之类的东西的支持，在那里你可以创建一个事件的动画序列。这在同时呈现几个事件时特别有用。

要使用 Anime.js，你首先要通过 npm 安装或者直接从 GitHub 项目下载。

因为示例项目基于 React，所以我使用 npm:

```
npm install animejs --save
```

安装完成后，您可以使用标准 JavaScript 导入将它导入到您的组件中:

```
import anime from "animejs";
```

导入后，您可以用`anime`对象定义动画:

```
    anime({
      targets: ".anime__label",
      translateX: "250px",
      rotate: "1turn",
      backgroundColor: "#FFC0CB",
      duration: 800,
      direction: "alternate"
    });
```

正如你在这里看到的，Anime.js 总是需要一个“目标”。目标可以包括用来标识 DOM 元素的任何东西。在本例中，我已经标识了包含`.container__label`类的元素。

除了定义目标之外，通常还要定义 CSS 属性——在本例中，我定义了一个`backgroundColor`。

您还需要定义“属性参数”和“动画参数”，就像我在本例中使用的一样:

*   `translateX`
*   `rotate`
*   `duration`
*   `direction`
*   以及其他等等

所以如果你像我上面那样定义动画，你就是在说:

1.  动画`.container__label`类元素向右移动 250 像素
2.  旋转一次
3.  应用`#FFC0CB`的背景色
4.  播放动画，时长 800 毫秒
5.  完成动画，然后重复(`direction:` `"``alternate``"`)

综合起来，应该是这样的:

 [https://www.youtube.com/embed/hJ7SekzMvzg?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent](https://www.youtube.com/embed/hJ7SekzMvzg?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent)

视频

现在，如果您想要制作多个对象的动画，您可以使用时间轴将动画连接在一起。这个过程只是定义一个时间线，然后添加额外的动画，如下所示(这个例子是从 Anime.js 文档中复制的):

```
    const tl = anime.timeline({
      easing: 'easeOutExpo',
      duration: 800,
      direction: "alternate"
    });
    tl
    .add({
      targets: '.anime__timeline--pink',
      translateX: 250,
    })
    .add({
      targets: '.anime__timeline--blue',
      translateX: 250,
    })
    .add({
      targets: '.anime__timeline--yellow',
      translateX: 250,
    });
```

因此，这将定义一个初始动画事件，该事件使用持续 800 毫秒的缓动(移动行为)并像文本动画一样交替出现。

然后，使用`.add`方法，我们添加特定于具有`.anime__timeline--pink`、`.anime__timeline--blue`和`.anime__timeline--yellow`类的元素的附加动画。

产生的行为如下所示:

 [https://www.youtube.com/embed/LcXEL30r5Ks?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent](https://www.youtube.com/embed/LcXEL30r5Ks?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent)

视频

要获得这些元素代码的完整副本，请查看这里的`animejs`组件[。](https://github.com/andrewevans0102/compare-javascript-animation-libraries/blob/master/src/components/Anime.js)

这两个例子只是触及了 Anime.js 能为你的项目做些什么的表面。他们的文档里有多个例子[在这里](https://animejs.com/documentation/)。此外，在 [codepen 上有很多很棒的例子。](https://codepen.io/collection/XLebem)

**优点**:

*   超级容易设置
*   相当直观
*   很多好例子
*   与现代浏览器兼容

**缺点**:

*   文档有时有点过于稀疏
*   关于 CSS 属性的信息不是很容易理解
*   放松很酷，但是定制的图案需要一秒钟来阅读
*   使用选择器是好的，但是需要在样式和动画定义之间进行协调

最终，我真的很喜欢 Anime.js，除了我肯定会建议添加更多的文档。此外，由于动画需要选择器，有时将元素样式转换成我想要的动画有点困难。

## p5.js

这个 [p5.js](https://p5js.org/) 库是对麻省理工学院[凯西·雷阿斯](https://en.wikipedia.org/wiki/Casey_Reas)和[本·弗莱](https://en.wikipedia.org/wiki/Ben_Fry)开始的最初[处理项目](https://processing.org/)的解释。处理包括编辑器和语言，试图使视觉设计对艺术家和创作者来说更容易。

最初的项目支持多种语言，这使得创建可视化元素比其他基础库(比如 Java 的 Swing)更容易。p5.js 将这些概念带到了 JavaScript 中，使您能够使用 HTML 画布快速构建动画。p5.js 还允许您创建 3D 图像和音频。

要开始使用，您可以直接下载 p5.js 库或使用 npm 安装它:

```
npm i p5
```

只要您想使用 p5.js，您就可以将动画创建为一个“草图”对象。

`setup`方法使你能够启动你的画布对象并应用任何尺寸，等等。`draw`方法允许您在画布刷新时将任何循环行为应用到页面。

如果你查看 p5.js 的[入门](https://p5js.org/get-started/)页面，他们定义了一个简单的例子(带动画)如下:

```
function setup() {
  createCanvas(640, 480);
}

function draw() {
  if (mouseIsPressed) {
    fill("#000000");
  } else {
    fill("#FFFFFF");
  }
  ellipse(mouseX, mouseY, 80, 80);
}
```

在上面的`setup`中，对`createCanvas`的调用创建了一个 640x480px 的画布。

然后，`draw`方法为`mouseIsPressed`事件添加一个事件监听器，根据鼠标是否被点击来应用一个`fill`属性。这个`fill`属性基本上是应用括号中指定的颜色(在我们的例子中，当按下时是黑色，当未按下时是白色)。

然后，调用`ellipse`方法在屏幕上画一个椭圆。因为每当画布翻页或刷新时都会调用该方法，所以它会在屏幕上创建一个画圆的动画效果。

因为在我们的示例应用程序中，我们使用 React，所以这有一点不同。在 React 中，我们只需引用 p5 库，然后向返回的 DOM 添加一个草图，如下所示:

```
import React, { Component } from "react";
import "./../styles/_title.scss";
import p5 from 'p5';
class P5WithSketch extends Component {
  constructor(props) {
    super(props)
    this.myRef = React.createRef()
  }

  Sketch = (p) => {
    let x = 100;
    let y = 100;
    p.setup = () => {
      p.createCanvas(640, 480);
    }
    p.draw = () => {
      if (p.mouseIsPressed) {
        p.fill("#000000");
      } else {
        p.fill("#FFFFFF");
      }
      p.ellipse(p.mouseX, p.mouseY, 80, 80);
    }
 }
  componentDidMount() {
    this.myP5 = new p5(this.Sketch, this.myRef.current);
  }
  render() {
    return (
      <div>
        <section className="title">
          <a
            className="title__heading"
            href="https://p5js.org/"
          >
            P5.js
          </a>
        </section>
        <section ref={this.myRef}>
        </section>
      </div>
    );
  }
}
export default P5WithSketch;
```

创建的最终动画如下所示:

 [https://www.youtube.com/embed/gTz_5GQJDBQ?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent](https://www.youtube.com/embed/gTz_5GQJDBQ?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent)

视频

这只是你可以用 p5.js 做的事情的开始。你可以很容易地扩展这里的基本动画，以对用户输入做出反应，并呈现完整的 3D 元素。有很多 p5.js 草图的很好的例子展示了这种行为。点击这里查看他们的示例页面，了解更多信息。

我的示例项目中的完整工作组件可以在这里找到[。](https://github.com/andrewevans0102/compare-javascript-animation-libraries/blob/master/src/components/p5WithSketch.js)

**优点**:

*   能够将动画和行为扩展到画布元素的初始设置和刷新
*   有很多例子的好文档

**缺点**:

*   难以创建“草图”对象来实际执行动画和行为
*   DOM 元素和呈现的画布之间的连接需要自定义引用

## 绿袜子动画平台(GSAP)

绿袜子动画平台(GSAP) 提供了一个相当强大的库，里面有你的项目可能需要的几乎任何类型效果的动画。此外，他们有非常强大的文档，包括如何与他们的 API 交互的例子。

要开始使用 GSAP，您首先只需要将它作为项目的一个依赖项进行安装:

```
npm i gsap
```

一旦你把它加载到你的项目中，那么就只需要用`gsap`对象定义动画行为了，就像你在这里看到的:

```
  animateText = () => {
    gsap.to(".gsap__label", { duration: 3, rotation: 360, scale: 0.5 });
  };
  animateSquare = () => {
    gsap.to(".gsap__square", { duration: 2, x: 200, ease: "bounce" });
  };
```

当使用 GSAP 时，你会经常注意到文档将动画称为“补间动画”，这类似于我们看到 p5.js 将动画称为“草图”

使用 GSAP 时，使用`to`和`from`方法来表示开始和停止行为。在我放在这里的两个例子中，他们将动画应用于具有`.container__label`和`.container__square`风格的元素。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

类似于我们与 Anime.js 合作的方式，GSAP 提供了像`duration`、`rotation`、`ease`和`scale`这样的属性。

当应用于模板时，上面的示例如下所示:

 [https://www.youtube.com/embed/xGgUDxj3FQo?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent](https://www.youtube.com/embed/xGgUDxj3FQo?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent)

视频

类似于 Anime.js，你可以用 GSAP 做很多很酷的事情。你也可以做时间线和其他序列动画。要更深入地了解，请查看[GSAP 入门页面](https://greensock.com/get-started/)。完整的例子列表，你可以查看 [GSAP 密码页](https://codepen.io/GreenSock)。

我介绍的组件的完整工作副本在我的示例项目[这里](https://github.com/andrewevans0102/compare-javascript-animation-libraries/blob/master/src/components/GSAP.js)中。

**优点**:

*   非常健壮的 API，有很多可能的动画
*   非常好的带有示例的文档

**缺点**:

*   特定的应用程序可能有特殊情况。我并没有真正涵盖这一点，但 GSAP 也包括周围的角度，反应等指示。
*   大量的 API 选项可能会让初学者望而生畏

## 三. js

到目前为止，所有的动画要么直接与 DOM 元素交互，要么添加自定义元素。 [Three.js](https://threejs.org/) 库使用 [WebGL](https://get.webgl.org/) 渲染动画。

WebGL 是什么？WebGL 是一个 DOM API，使您能够在浏览器中呈现图形。它确实使用了 canvas 元素，但与我们在 p5.js 中看到的生成画布并在其上书写不同，WebGL 允许您调用 API 来为您进行渲染。

Three.js 是一个协调 WebGL 调用的库，用于在浏览器中呈现图像和图形。如果您想创建与项目相关的动画或 3D 图形，这真的很棒。

Three.js 有一个很棒的演练样本项目[，可以在这里](https://threejs.org/docs/index.html#manual/en/introduction/Creating-a-scene)到达。正如我提到的，我的示例项目使用 React，因此设置略有不同。核心概念和 API 调用都是一样的。

如果您对理解(或让示例工作)有任何问题，我建议在这里查看 Three.js 文档中的解释[。](https://threejs.org/docs/#manual/en/introduction/Creating-a-scene)

实现这一点是一个多步骤的过程。我们必须首先定义用于动画的渲染器:

```
    const scene = new THREE.Scene();
    let camera = new THREE.PerspectiveCamera(75, 400 / 400, 0.1, 1000);
    const renderer = new THREE.WebGLRenderer();
    renderer.setSize(400, 400);
    this.mount.appendChild(renderer.domElement);
```

Three.js 称之为“创造场景”。它的长与短基本上是创造动画发生的区域。

接下来，我们定义想要制作动画的对象:

```
    const geometry = new THREE.BoxGeometry(1, 1, 1);
    const material = new THREE.MeshNormalMaterial();
    const cube = new THREE.Mesh(geometry, material);
    scene.add(cube);
```

这里，我们使用 Three.js 全局对象来定义立方体和与它相关联的动画材质。

接下来，我们定义动画方法:

```
    camera.position.z = 5;
    const animate = function () {
      requestAnimationFrame(animate);
      cube.rotation.x += 0.01;
      cube.rotation.y += 0.01;
      renderer.render(scene, camera);
    };
```

这就是将要调用的内容，以及 Three.js 如何调用 WebGL API 方法来显示动画。

最后，我们直接调用`animate`方法来渲染动画:

```
    animate();
```

为了让所有这些都与 React 一起工作，我们只需将它放在我们想要显示的组件的`componentDidMount`生命周期方法中:

```
  componentDidMount() {
    // create the scene and renderer for the animation
    const scene = new THREE.Scene();
    let camera = new THREE.PerspectiveCamera(75, 400 / 400, 0.1, 1000);
    const renderer = new THREE.WebGLRenderer();
    renderer.setSize(400, 400);
    this.mount.appendChild(renderer.domElement);

    // create the elements that become a rotating cube and add them to the scene
    const geometry = new THREE.BoxGeometry(1, 1, 1);
    const material = new THREE.MeshNormalMaterial();
    const cube = new THREE.Mesh(geometry, material);
    scene.add(cube);
    // create the actual animation function that will draw the animation with WebGL
    camera.position.z = 5;
    const animate = function () {
      requestAnimationFrame(animate);
      cube.rotation.x += 0.01;
      cube.rotation.y += 0.01;
      renderer.render(scene, camera);
    };
    // call the animation function to show the rotating cube on the page
    animate();
  }
```

生成的动画如下所示:

 [https://www.youtube.com/embed/SKvuFvrdYTc?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent](https://www.youtube.com/embed/SKvuFvrdYTc?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent)

视频

你可以用 Three.js 做很多很酷的事情。我推荐在这里查看他们的[文档](https://threejs.org/docs/index.html#manual/en/introduction/Creating-a-scene)和在这里查看[示例](https://threejs.org/examples/#webgl_animation_cloth)。

在我的示例项目[这里](https://github.com/andrewevans0102/compare-javascript-animation-libraries/blob/master/src/components/Three.js)中可以找到我所涉及的组件的完整工作副本。

**优点**:

*   您可以利用 API 与 WebGL 进行交互，这使得使用 API 变得更加容易
*   您可以利用 Three.js 来创建图形

**缺点**:

*   它需要手动向 DOM 添加一个元素
*   有一个与库和 WebGL 概念相关的学习曲线

## 结束语

我希望这篇文章能让你对目前可用的一些不同的 JavaScript 动画库有一个基本的了解。

我想指出我介绍的四个库之间的一些共性。

对于 Anime.js 和 GSAP，他们都是通过导入一个全局对象，识别要应用动画的元素，然后定义动画来完成动画的，就像这样:

```
// anime.js
    anime({
      targets: ".anime__label",
      translateX: "250px",
      rotate: "1turn",
      backgroundColor: "#FFC0CB",
      duration: 800,
      direction: "alternate"
    });

// GSAP
gsap.to(".gsap__label", { duration: 3, rotation: 360, scale: 0.5 });
```

使用 p5.js 和 Three.js，可以创建定制元素并附加到 DOM 中。两者都利用 HTML 画布来生成相关联的动画，如下所示:

```
// P5.js
Sketch = (p) => {
  let x = 100;
  let y = 100;
  p.setup = () => {
    p.createCanvas(640, 480);
  }
  p.draw = () => {
    if (p.mouseIsPressed) {
      p.fill("#000000");
    } else {
      p.fill("#FFFFFF");
    }
    p.ellipse(p.mouseX, p.mouseY, 80, 80);
  }
}

// Three.js
const scene = new THREE.Scene();
let camera = new THREE.PerspectiveCamera(75, 400 / 400, 0.1, 1000);
const renderer = new THREE.WebGLRenderer();
renderer.setSize(400, 400);
this.mount.appendChild(renderer.domElement);
```

看到这些常见的行为会让您对任何 JavaScript 动画库有所了解。正如我在介绍中所说的，虽然这篇文章专门介绍了这四个库，但是今天仍然有许多其他的库可供您使用。

最好的部分是，随着 web 开发和浏览器技术的进步，JavaScript 动画可以做比以前更多的事情。我鼓励您阅读与本文所涉及的库相关的文档，以获得更多信息。

感谢您阅读我的帖子！在 Twitter 上关注我，地址: [@AndrewEvans0102](https://twitter.com/AndrewEvans0102) ！

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.