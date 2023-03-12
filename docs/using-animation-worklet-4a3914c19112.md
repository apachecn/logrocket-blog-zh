# 使用动画 Worklet 

> 原文：<https://blog.logrocket.com/using-animation-worklet-4a3914c19112/>

![](img/0f8d25ad6fae054ddbb98744c77c62ea.png)

胡迪尼的新动画工作包是将本地动画带到网络上的更大计划的一部分，该计划还包括[网络动画 API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Animations_API) (WAAPI)。

与我们最近看到的其他努力不同，Animation Worklet 给游戏带来了一些我们过去无法获得的优势。本文解释了 Worklets 的新特性，以及它在哪些方面优于任何当前的代码或动画库。

简而言之，Animation Worklet(与其他动画技术如 [CSS 过渡](https://blog.logrocket.com/css-transitions-101-lets-animate-a-toggle-button-icon-333967f5b971)、[带关键帧的 CSS 动画](https://developer.mozilla.org/en-US/docs/Web/CSS/animation)或 [WAAPI](https://developer.mozilla.org/en-US/docs/Web/API/Web_Animations_API) 相比)不必基于时间。这使得我们可以基于用户输入来制作动画，比如滚动、以前的状态或者任何我们可以用 JavaScript 访问的东西。

作为一个轻量级工作者，Worklet 独立于主 JavaScript 线程运行，旨在将繁重的处理或频繁的工作卸载到主线程之外，因此在理想情况下，主线程中的任何故障或滞后都不会导致同一页面上的动画滞后。类似地，动画中的任何瓶颈都不会自动导致页面整体体验变慢。

### 语法

让我们深入研究一下代码，看看它到底是怎么回事。工作小程序的使用需要几个设置步骤。

### 先决条件

虽然动画工作流是一个新特性，工作流规范的状态是“工作草案”，但你现在可以尝试在 Chrome 的实验性 Web 平台特性标志下启用它。如果您打算尝试本文中的演示，我建议您这样做。

### 支持检查

虽然不建议在生产中使用 Worklet，但是仍然可以使用带有后备的 Worklet。检查 Worklets 的支持将有助于这一点。它还将防止中断整个 JavaScript 执行。

```
if(!('animationWorklet' in CSS)) {
    document.write("Nope, no animationWorklet.");
}
```

### 工作动画制作人

动画师是一个轻量级的工作者，作为浏览器本地功能的扩展，需要注册。它的注册方式起初令人困惑，但在浏览器的最新功能中很常见，比如[服务人员](https://developers.google.com/web/fundamentals/primers/service-workers/)。使用*CSS . animation work let . add module()*方法从一个单独的文件加载动画。该方法接受 animator 文件的路径，并返回一个承诺，在加载 animator 时给我们一个回调。

```
CSS.animationWorklet.addModule('./path/to/animator.js').then(function () {
  // we can use the animator here
});
```

使用 registerAnimator 函数注册动画师的实际代码非常简单。该函数接受两个参数，Worklet animator 的名称和一个类形式的 Worklet 构造函数。

该类可以像任何其他类一样拥有方法，但是我们感兴趣的主要方法是 *animate* 方法，它用于将当前时间的变化反映到动画中。在每一帧上都调用 *animate* 方法，与 *requestAnimationFrame* 不同，该动画方法被尽可能频繁地调用，仅受硬件限制(requestAnimationFrame 试图达到的标准 FPS 值是 60，而许多监视器具有更高的帧速率)。

```
registerAnimator('passthrough', class {
    animate(currentTime, effect) {
        effect.localTime = currentTime;
    }
});
```

该方法接受*当前时间*和*效果*对象，用于将*当前时间*的更改应用到效果。currentTime 是由时间线传递到工作小程序中的值，正如我前面提到的，当涉及到工作小程序时，不必用时间来表示。稍后会详细介绍。

现在，让我们从一个简单的例子开始，让时间轴通过，并把相同的值分配给*效果*对象作为*效果. localTime* 。然后，将 *localTime* 属性的值应用于进一步描述的工作小程序动画。

### 工作套件

一旦我们完成了所有这些设置，我们就可以在标准代码中使用 animator 来制作我们的动画了。动画是用接受三个参数的 WorkletAnimation 构造函数创建的——动画师的名字、关键帧效果的实例和时间轴。第四个选项以对象的形式传入 registerAnimator 类构造函数。

```
var worklet = new WorkletAnimation(
    'passthrough',  // name of animator
    new KeyframeEffect(
        document.querySelector('#box1'),
        [
            {
                transform: 'rotate(0)'
            },
            {
                transform: 'rotate(360deg)'
            }
        ],
        {  
            duration: 2000,
            iterations: Number.POSITIVE_INFINITY
        }
    ),
    document.timeline,  // timeline
    {}   // registerAnimator options
);
worklet.play();
```

上面的设置将创建一个简单的、无限的旋转盒子动画。通过扩展关键帧对象多一点，我们将实现一个做桶滚动的盒子。

请注意，演示已被修改为使用 blobs 而不是单独的文件，以便在 Codepen 中工作。你可以在 [GitHub](https://github.com/gmrchk/worklet-examples) 和 [GitHub 页面](https://gmrchk.github.io/worklet-examples/)上访问带有原始演示的资源库。

您会注意到这里的语法非常简单，选项非常自描述且易于使用。虽然这很好，但这里没有什么是我们不能用一个简单的 CSS 动画和关键帧来完成的。

### 作为数学函数的动画师

所以，让我们试试更复杂的。动画师和它的动画方法可以是我们想要的任何东西。这意味着我们可以将数学函数应用于我们的时间轴时间…

```
registerAnimator('sin', class {
    animate(currentTime, effect) {
        effect.localTime = Math.abs( Math.sin( currentTime ) );
    }
});
```

…或者一些自定义逻辑，如对当前时间线的数字进行舍入。

```
registerAnimator('sin', class {
    animate(currentTime, effect) {
        effect.localTime = Math.round( currentTime * 100 ) / 100;
    }
});
```

这种对时间轴当前值的操作为我们提供了一种运行动画的新方法。把它想象成类固醇的放松。我们不需要用三个或更多的点来将缓动描述为一条曲线，我们可以使用 JavaScript 来根据当前的时间轴值编写任何我们想要的行为。

当使用自定义逻辑修改 animator 时，记住 *effect.localTime* 被用作 Worklet 动画中动画时间轴的当前时间是很重要的。这意味着任何返回 NaN 的负数或计算都不能被处理到动画中。自然，我们的代码应该反映这一点并实现预防措施。

### 卷轴

动画工作件是专门为处理滚动等事件而设计的。很容易将动画时间轴建立在卷轴上，而不是时间上。我们需要做的就是使用一个 *ScrollTimeline* 构造函数来代替经典的 *document.timeline* 。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
new WorkletAnimation(
    'passthrough',
    new KeyframeEffect(
        ...
    ),
    new ScrollTimeline({
        scrollSource: document.querySelector('#scroll'),
        orientation: "vertical", 
        timeRange: 1000
    })
).play();
```

我们需要了解一些关于浏览器的事实，以理解为什么动画工作包值得使用。在某些情况下，使用动画工作件可以显著提高性能。

### 浏览器线程

浏览器是多线程的。事实上，他们已经有一段时间了。然而，这并不自动意味着我们得到所有的 CPU 核心和 GPU 来处理运行时的 web 应用程序。每个浏览器在实现多线程时实际上采取的方法略有不同。

Chrome 专注于将浏览器标签分离成独立的进程。这允许每个进程/标签在一个单独的内核上运行，并在工作时以某种方式利用所有的 CPU。它还有一个优点，当出现问题并且进程停滞时，只有一个没有响应的标签。

但是，每个进程都在运行通常可以被网页实例重用的所有部分。这极大地影响了 Chrome 运行所需的内存量，从而影响了电池寿命。但是，任何阅读和使用 Chrome 的人可能已经知道这个问题了。

火狐采取了不同的方法。对于所有选项卡，它最多运行四个进程。每个选项卡都被分配了一个负责该选项卡的进程。随着量子更新，它甚至能够在多个线程中处理一个标签的某些任务。但这仅限于实际上可以分成并行线程的任务，比如网站 CSS 的处理。

总而言之，浏览器在某种程度上是多线程的，它们试图实现尽可能多的进程分离。所有浏览器都有一定的限制。

### 事件循环

事件循环对于浏览器来说是非常重要的，但是很多人并不知道。事件循环负责调度和处理选项卡中发生的任务。无论如何，任务最终会在队列中结束，从那里开始，到时间了就执行，即使对于异步代码也是如此。这就是为什么主 JavaScript 线程很难分成多个线程，因为事情被设计成一个接一个地发生，而不是并行的。

### 频繁的函数调用

在一个线程中以串行方式运行任务没有任何问题，直到频繁的函数调用可能阻塞该线程。这种事件的一个完美的例子是卷轴。

滚动事件是应用程序中经常使用的东西——无论是显示/隐藏某种菜单，还是根据用户滚动的距离简单地变换 web 的一些装饰部分。有些人甚至会说，滚动是用户的主要交互方式，许多网络应用程序，如脸书、Instagram 或 Twitter，主要基于用户滚动内容。

也就是说，这种互动的任何滞后都可能非常恼人。虽然这是一个我们经常在网络上使用的事件，但是它很容易把性能搞糟。在滚动条上运行我们的代码会占用我们的主浏览器线程的很大一部分，尤其是当涉及到像动画这样导致重新渲染的昂贵代码时。

事实上，一些浏览器甚至开始在默认情况下将滚动侦听器实现为被动的(因此浏览器不必等待执行来查看 event.preventDefault()是否出现在代码中)，以使代码更加优化，除非另有说明。

这就是动画工作室的作用。由于本机功能将工作卸载到单独的线程，浏览器可以在单独的进程中完成所有需要的计算，并且只返回渲染指令。这在以前是不可能的，可以极大地改善基于滚动的动画。当然，这是假设浏览器的实现将为我们正确地进行优化。

### 国家

*registerAnimator* 是一个类，它自带了该类带来的所有可能性(甚至还有后面描述的一些意想不到的)。这个类可以独立工作，有一个范围，保存某种状态，可以扩展，等等。

这意味着动画不仅可以反映时间轴中的当前时间，还可以基于之前的行为或我们之前保存的一些状态进行操作。以下示例根据滚动方向更改框的颜色。通过保存先前的滚动时间线状态并基于影响框背景的方向将时间线的状态手动设置为开始/结束来检测方向。

```
registerAnimator('state', class {
    constructor(options, state) {
        this.prevTime = 0;
    }

    animate(currentTime, effect) {
        if (this.prevTime > currentTime) {
            effect.localTime = 1;
        } else if (this.prevTime < currentTime) {
            effect.localTime = 1000;
        }
        this.prevTime = currentTime;
    }
});
```

### 保存状态

将工作流代码分成多个部分有几个原因。一个原因是分成独立的线程。同样类似于服务人员，动画工作小程序可以在不使用时终止，并且可以在多次重新加载时保存状态。目前，我们将不得不使用一些其他技术(如本地存储)来达到同样的效果。

不幸的是，即使是 Chrome 目前也不支持保存状态，或者文档根本没有反映 API 最近的变化。但是考虑到这个特性是实验性的，这并没有错。

### 结论

Animation Worklet 是本地动画家族中一个很有前途的新成员。它专注于将工作负载分离到其他线程上，这无疑是一个很好的实践，我们将在未来实现的 API 中看到更多。

尽管功能很好，但这项技术仍有一些不太合理的地方。例如，我发现关于 *ScrollTimeline* 构造函数的 *timeRange* 选项的命名约定令人困惑。其实和时间没什么关系。

显然，基于许多因素，Worklet 仍然是一项正在进行的工作，比如状态的保存，它没有像预期的那样工作(或者更确切地说，文档没有说明 API 的当前状态)。

另外值得一提的是 API 的开放性，就像它是为扩展而构建的一样。你可以在一个 *CSS.animationWorklet* 对象中看到这一点，其中当前唯一实现的方法是本教程中使用的 *addModule* 方法。在 animator 类中应用当前时间是通过给 *effect.localTime* 赋值来实现的，这一事实向 effect 对象暗示了一些尚未实现的功能。

新的 Worklet APIs 包括像 PaintWorklet、AudioWorklet 或 LayoutWorklet 这样的东西，它们肯定会带来一些更有趣的东西，每个都专注于解决一个特定的任务。但是让我们下次再讨论这个问题。

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。