# 使用 GSAP 3 制作网络动画

> 原文：<https://blog.logrocket.com/using-gsap-3-for-web-animation/>

Greensock 动画平台(GSAP)是一个易于使用的网页动画 JavaScript 库。它可以让您制作任何可以用 JavaScript 访问的东西的动画，包括 SVG、通用对象、画布等等。

## 动画 GSAP:介绍

Web 动画最基本的形式是每秒尽可能多次地改变元素的属性值。GSAP 通过获取开始和结束值，然后在这两个值之间间歇过渡来创建动画效果。例如，淡出效果是通过将元素的开始和结束不透明度分别设置为 1 和 0 来实现的。结果是随着不透明度属性值从 1 过渡到 0，指定元素逐渐淡化。

对于那些优先考虑速度、灵活性和控制的人来说，GSAP 是网络动画库的一个很好的选择。GSAP 是一个速度极快的库(估计比 jquery 库快 20 倍)，这意味着你的 GSAP 动画不会给你的应用程序带来明显的延迟。

有大量的方法可供选择，GSAP 还可以让你以最理想的方式制作几乎所有 CSS 属性的动画。有了 GSAP，你可以准确地决定动画将持续多长时间以及要更改的确切属性，而不必编写可能会降低应用程序速度的高级自定义动画。

最后，GSAP 不依赖于任何外部库，所以没有必要安装或加载任何额外的库到你的应用程序中来开始动画。

在本教程中，我们将使用 GSAP 3。你可以在这里了解更多关于这个图书馆的信息。

## GSAP 入门

可以通过多种方式将 GSAP 加载到您的应用程序中，但最快的方法是将 GSAP CDN 添加到您的 HTML 文件中:

```
<script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.6.0/gsap.min.js"></script> 
```

或者，可以使用 npm 和 yarn 安装 GSAP。对于 npm 安装，运行:

```
 npm install gsap 
```

对于场地安装:

```
yarn add gsap 
```

你也可以从 GSAP 官方网站[这里](https://greensock.com/docs/v3/Installation)下载 zip 文件，或者从 [github](https://github.com/greensock/GSAP) 获取。

**在 GSAP 使用补间**
由于属性改变，补间是一个单一的运动。在 GSAP，典型的补间包含一个方法、要动画的元素、动画持续时间和一个名为`vars`的属性对象。以下是任何给定补间的基本语法:

```
TweenMax.method(element, duration, vars) 
```

`TimeLine`是一个用于补间动画的容器，允许您实时定位动画。它可以借助不同的 GSAP 方法来实现这一点。下面是一个典型的 GSAP 时间表的例子:

```
var tl = gsap.timeline(); 
```

然后，您可以使用任何 JavaScript 方法创建动画。在本例中，我们将使用`to()`方法，该方法确定最终值:

```
tl.to(".box", {duration: 1, opacity: 0 }); 
```

有了这个基本的理解，我们现在可以继续学习如何使用各种 JavaScript 方法在 GSAP 创建动画。

## 使用 GSAP 方法

方法是 GSAP 任何动画的关键。方法有助于定义动画目标值以及起始值。在接下来的部分，我们将讨论 GSAP 方法，从最基本的到更高级的，以及如何在你的应用程序中使用它们制作动画。

### 到()

这是用于设置给定动画的最终值的最常见的 GSAP 方法。使用此方法时，GSAP 会根据给定元素的 CSS 属性自动设置初始值。

参见《笔 [GSAP 到()方法](https://codepen.io/amaechi1/pen/wvovKPX)由伊丽莎白阿玛奇([@阿玛奇 1](https://codepen.io/amaechi1) )
于 [CodePen](https://codepen.io) 。

来自()

### `from()`方法最适合用于反向动画。当你设置初始 CSS 属性时，GSAP 将动画显示为默认值。例如，如果不透明度设置为 0，那么 GSAP 开始从 0 不透明度动画，直到它达到最初设置的不透明度(在我们的例子中，1)。下面是一个使用`from()`方法的动画示例。

参见《笔 [》GSAP 撰()法](https://codepen.io/amaechi1/pen/mdOdwYX)伊丽莎白阿玛奇([@阿玛奇 1](https://codepen.io/amaechi1) )
上 [CodePen](https://codepen.io) 。

fromTo()

使用`fromTo() method`，开发者能够定义给定动画的开始和结束属性。该方法的语法与上面提到的其他两种方法略有不同，因为它采用了两个不同的对象来指定起始值和结束值:

### 见[笔
GSAP fromTO()方法](https://codepen.io/amaechi1/pen/MWbWoQR)by Elizabeth amae chi([@ amae chi](https://codepen.io/amaechi1))
on[code Pen](https://codepen.io)。

集合()

```
gsap.fromTo(".box", {x: 20 , y: 10}, { x: 200 , y : 100, duration: 1}); 
```

`set()`方法是一种零方向方法，用于设置给定元素的属性。当您想要设置稍后将使用 GSAP 制作动画的属性时，此方法非常方便。

从上面可以看出，带有`"``.box``"`类的元素的 x 和 y 坐标分别设置为 10 和 20。然后，您可以在以后使用已经设置的初始值来制作元素的动画。

方法链式反应和基本测序

```
gsap.set(".box", {x: 10 , y: 20}); 
```

为了实现一个更简洁的动画，其中我们确切地定义哪个动画先出现，我们需要链接`to()`方法并按照我们想要的顺序列出动画(补间动画)。

为此，我们将首先创建一个时间线；

### 然后，我们可以继续将每个补间添加到已经创建的时间轴中:

动画控制功能

动画控制功能让您完全控制动画，允许您执行暂停和反转等操作。控制功能包括`play()`、`pause()`、`reverse()`、`kill()`、`resume()`等。

```
var tl = gsap.timeline({repeat: 30, repeatDelay: 1}); 
```

请注意，使用任何给定 GSAP 方法的动画都会返回一个补间实例。正是在这种情况下，您的动画控制功能被调用。以下面的补间实例为例:

```
tl.add( gsap.to("#box", {duration: 1, x: 100}) );
tl.add( gsap.to("#box2", {duration: 2, x: 100 , scale: 1.1}) );
tl.add( gsap.to("#box3", {duration: 3, x: 100 , scale: 2}) );

```

## 请注意我们如何使用点击事件上的`pause()`函数来控制它:

看笔 [GSAP 动画控](https://codepen.io/amaechi1/pen/mdOdMGv)by Elizabeth amae chi([@ amae chi](https://codepen.io/amaechi1))
上 [CodePen](https://codepen.io) 。

GSAP 的特殊属性

```
// create a reference to the animation
//
const instance = gsap.to(".box", {x: 100 , y: 100}); 
```

GSAP 区别对待特殊属性，如招摇和回调，以达到更好的效果。

```
document.querySelector(".pause").onclick = () => instance.pause(); 
```

游荡者

漫游器用于动画一组元素。swagger 允许您指定为组中的每个给定元素设置动画的开始时间。

## 更多来自 LogRocket 的精彩文章:

看 [CodePen](https://codepen.io) 上伊丽莎白阿玛奇([@阿玛奇 1](https://codepen.io/amaechi1) )
的笔[GSAP 组动画昂首阔步](https://codepen.io/amaechi1/pen/OJbJjKJ)。

回收

### 如果您希望在事件(如完成或更新)发生后执行特定的操作，则需要回调。一些在 GSAP 可用的回调包括:`onComplete`、`onStart`和`onUpdate`。

您可以使用`onComplete`回调将一个句子记录到控制台，如下所示:

* * *

### 回调也可以使用回调的`param`属性接受一个参数，如下所示；

* * *

属性接受函数的参数。

GSAP 插件

### GSAP 插件提供了传统 GSAP 图书馆没有的额外功能。它们包含特殊属性，这些属性稍后会动态插入到 GSAP 中。一些最流行的 GSAP 插件包括:Draggable，DrawSVGPlugin 和 MorphSVGPlugin。

请注意，在使用任何 GSAP 插件之前，您需要将它注册到 GSAP 核心，如下所示:

可拖动的

```
gsap.to(".box", {duration: 1, x:150, onComplete: tweenComplete});

function tweenComplete() {
    console.log("completed");
} 
```

GSAP 可拖动插件让你用 GSAP 创建可怕的可拖动元素。该插件支持触摸，因此用户可以在平板电脑和手机上使用它。使用 Draggable，您可以使用`trigger`属性设置应用程序中应该触发拖动的确切部分。

```
gsap.to(".box", {duration: 1, x: 100, onComplete: tweenComplete, onCompleteParams: ["message parameter"]});

function tweenComplete(message) {
  console.log(message);
} 
```

Draggable 插件在转换后的容器上运行良好，并且可以在 SVG 上使用，效果令人印象深刻。

## 要开始使用 Draggable，我们可以简单地创建一个可拖动的元素，如下所示:

这将允许水平和垂直拖动元素。

使用上面的例子，您可以为配置接受另一个参数。您可以定义拖动位置，设置 bounds 元素，并在单击元素或拖动完成时调用函数。

```
gsap.registerPlugin( Draggable , MotionPathPlugin, TextPlugin); 
```

在这个代码片段中，`inertia`属性用于使元素在可拖动插件中可旋转:

### 在可拖动上捕捉

`snap`和`liveSnap`属性定义了在拖动后或拖动过程中，被拖动的项目应该在何时何地对齐。在这里，`liveSnap`让拖动的元素捕捉到数组中最近的点，当它在半径的 15px 以内时，用 points 属性定义:

你可以在这里了解更多上面[列出的其他插件。](https://greensock.com/docs/v3/Plugins)

GSAP 动画在反应

```
Draggable.create("#draggable"); 
```

对于想开始使用 GSAP 的 React 开发人员，这里有一个快速指南，介绍 react.js 的不同之处

GSAP 装置

GSAP 可以通过运行以下命令来安装 npm:

```
Draggable.create("#draggable", {
    type:"x",
    bounds: document.getElementById("container"),
    inertia: true,
    onClick: function() {
         console.log("clicked");
    },
    onDragEnd: function() {
         console.log("drag is complete");
    }
});

```

或者，用纱线:

### 对于安装 GSAP 奖励插件:

或者，用纱线:

```
Draggable.create("#draggable", {
  type: "x,y",
    liveSnap: {
        points: [{x: 0, y: 0}, {x: 150, y: 0}, {x: 250, y: 50}],
        radius: 15
    }
})

```

GSAP 进口

## 然后，您可以继续导入项目文件中的 GSAP:

插件可以单独导入，就像这样:

### 如前所述，记得在使用前注册你的插件:

```
npm install gsap

```

创作你的第一部 GSAP 动画

```
yarn add gsap

```

首先，像对待任何 React 项目一样，创建基于类的组件:

```
npm install ./gsap-bonus.tgz

```

结论

```
yarn add ./gsap-bonus.tgz
```

### GSAP 仍然是最灵活的网络动画库之一，有了 GSAP 3，动画变得更加容易。

在本教程中，你有希望了解到你不必将你的动画仅仅局限于基本元素。GSAP 提供了许多方法，使得用 JavaScript 编写动画代码变得更加容易。

```
import { gsap } from "gsap";

```

您是否添加了新的 JS 库来提高性能或构建新特性？如果他们反其道而行之呢？

```
import { PixiPlugin } from "gsap/PixiPlugin.js";
import { MotionPathPlugin } from "gsap/MotionPathPlugin.js";

```

毫无疑问，前端变得越来越复杂。当您向应用程序添加新的 JavaScript 库和其他依赖项时，您将需要更多的可见性，以确保您的用户不会遇到未知的问题。

```
gsap.registerPlugin(PixiPlugin, MotionPathPlugin);

```

LogRocket 是一个前端应用程序监控解决方案，可以让您回放 JavaScript 错误，就像它们发生在您自己的浏览器中一样，这样您就可以更有效地对错误做出反应。

### [LogRocket](https://lp.logrocket.com/blg/javascript-signup) 可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

自信地构建— [开始免费监控](https://lp.logrocket.com/blg/javascript-signup)。

```
import React from 'react'
class App extends React.Component{
    constructor(props){
        super(props)
    }
    render(){
        return(<h1>hello world</h1>)
    }
}
/pre>
We will be using ref to target the specific element to be animated. Our tween will be created once our React application is mounted using the componentDidMount() method. The element to be animated as well as the resulting tween instance will be stored in a state.
```

```
import React from 'react'
import { gsap } from 'gsap'
class App extends React.Component{
    constructor(props){
        super(props)
        this.myElement = null;
        this.myTween = null;
    }
    componentDidMount(){
        this.myTween = gsap.to(this.myElement, 1, {x: 100, y: 100});
    }
    render(){
        return <div ref={div => this.myElement = div} />
 }
}

```

## Conclusion

GSAP remains one of the most flexible libraries for web animation, and with GSAP 3, animation has become even easier.

In this tutorial, you’ve hopefully learned that you do not have to limit your animation to just basic elements. GSAP offers many methods make writing animation code in JavaScript that much easier.

## Are you adding new JS libraries to improve performance or build new features? What if they’re doing the opposite?

There’s no doubt that frontends are getting more complex. As you add new JavaScript libraries and other dependencies to your app, you’ll need more visibility to ensure your users don’t run into unknown issues.

[LogRocket](https://lp.logrocket.com/blg/javascript-signup) is a frontend application monitoring solution that lets you replay JavaScript errors as if they happened in your own browser so you can react to bugs more effectively.

[![LogRocket Dashboard Free Trial Banner](img/e8a0ab42befa3b3b1ae08c1439527dc6.png)](https://lp.logrocket.com/blg/javascript-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/javascript-signup)

[LogRocket](https://lp.logrocket.com/blg/javascript-signup) works perfectly with any app, regardless of framework, and has plugins to log additional context from Redux, Vuex, and @ngrx/store. Instead of guessing why problems happen, you can aggregate and report on what state your application was in when an issue occurred. LogRocket also monitors your app’s performance, reporting metrics like client CPU load, client memory usage, and more.

Build confidently — [Start monitoring for free](https://lp.logrocket.com/blg/javascript-signup).