# GSAP 3 - LogRocket 博客中的新内容

> 原文：<https://blog.logrocket.com/whats-new-in-gsap-3/>

GreenSock 已经存在了十多年，是最古老的 JavaScript 动画库之一。它可以在 JavaScript 运行的任何地方工作，并在保持其性能的同时动画显示 DOM 元素和 JavaScript 对象。

GreenSock 是向后兼容的，与框架无关的，对于所有技能水平的开发人员来说都很容易掌握。因此，它是构建直观和交互式网站的最重要的工具之一。

最新版本 [GSAP 3](https://greensock.com/3/) ，在之前版本的基础上增加了大约 50 项新功能和大量改进，包括:

*   更易于使用的 API
*   减小文件大小
*   时间线默认值
*   全新的实用方法
*   运动路径插件
*   向后兼容性
*   相对位置前缀
*   高级蹒跚者
*   随机能力

让我们更详细地了解一下 GSAP 3 中一些最重要的新功能。

## 简化的 API

新版本附带了一个简化的、更加用户友好的 API。GreenSock 将“Lite”和“Max”功能整合到一个名为`gsap`的单一对象中，这两个功能构成了以前版本中的核心模块。

以下面的代码为例，它在以前版本的 GSAP 中应该是这样的。

```
TweenMax.method('selector', {});

```

在 GSAP 3 中，上面的代码看起来像这样:

```
gsap.method('selector', {});

```

类似地，下面一行也适用于以前的版本。

```
TweenLite.method('selector',{});

```

在 GSAP 3 中，它会被翻译成以下内容。

```
gsap.method('selector',{});

```

这一变化也影响了时间线的创建方式。下面的两个代码块在旧版本的 GSAP 中将如下所示。

```
const tl = new TimelineMax();
tl.method('selector',{})

const tl = new TimelineLite();
tl.method('selector',{})

```

在最新的版本中，它会被写成这样:

```
var tl = gsap.timeline();
tl.method("selector", {});

```

当链接到像`to()`或`from()`这样的方法时，`gsap`对象返回一个补间实例。

## 减小文件大小

GSAP 保留了几乎所有的旧功能，并增加了许多新功能。此外，GreenSock 从头开始重建核心作为现代 ES 模块。

## 向后兼容性

新的 GSAP 更新仍然识别旧的语法，因为以前版本的 Max 和 Lite 功能都是别名，以防止遗留代码库被破坏。这为开发人员省去重新编写代码来使用 GSAP 3 的麻烦。

## 持续时间

现在，补间的持续时间参数是在`vars`对象中定义的，而在以前的版本中，它被定义为方法的参数。

以下面的代码为例，它是为以前版本的 GSAP 编写的。

```
TweenMax.from('selector', 1, {});

```

在 GSAP 3 中，上述代码可以重写为:

```
gsap.from('selctor', {duration:1})

```

注意:旧的语法仍然有效，因为新的更新是向后兼容的。这有助于防止破坏使用旧语法的代码库。

## 时间线默认值

GSAP 3 使您能够为您的时间轴指定默认属性。子补间在创建时继承这些值。

在旧版本中，属性是针对每个补间单独设置的，这导致了代码重复。该更新有助于开发人员遵循不要重复自己(DRY)的原则，保持代码简单和更简洁。

以下示例是为旧版本的 GSAP 编写的。

```
var tl = new TimelineMax();
  tl.to(".selector1", 5 , {ease: Power2.Out, x:200})
    .to(".selector2", 5 , {ease: Power2.Out, y:500})

```

这在 GSAP 3 中被翻译成以下内容。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
gsap.timeline({defaults:{ease:"power2.out", duration:5}})
    .to(".selector1", {x:200})
    .to(".selector2", {y:500}) 

```

每个补间从父时间轴继承缓动和持续时间。当在子补间上定义另一个值时，继承的默认值很容易被覆盖。

## 高级蹒跚者

新的更新删除了用于错开的方法，如`staggerTo()`、`staggerFrom()`、`staggerFromTo()`。这现在是`vars`对象中的一个参数。

您可以使用以下格式将交错添加到补间。

```
gsap.method("selector", {
  x:500,
  duration:2,
  stagger: 1 // adds a stagger of 1 second
});

```

参见[代码本](https://codepen.io)上的[笔-错开](https://codepen.io/jola_adebayor/pen/KKKLjqQ)的 anjolaoluva([@卫英琦 _ 阿德巴约](https://codepen.io/jola_adebayor) )
笔。

您还可以通过使用对象语法来执行更高级的交错操作。

```
gsap.method("selector", {
  x:500,
  duration:2,
  stagger: {
    amount:2, // amount of time between staggered tweens
  }

```

交错对象还接受其他参数，例如:

*   `from`，定义了错开的起点
*   `axis`，定义要错开的轴
*   `ease`，定义交错项目应具有的缓动类型

## 新的随机功能

你现在可以定义一个随机值范围到(比如`random(-100, 100)`)或者一个数组来选择，GSAP 会选择一个随机值来制作动画。

这使得创建高级随机化效果更加容易。

```
gsap.method("selector", {
  x:"random(100, 200)" //chooses a random number between 1 and 100
}); 

```

下面是一个使用数组的例子。

```
gsap.method("selector", {
  x:"random([0, 100, 400, 500])" //chooses a number in the array at random
}); 

```

您甚至可以将随机数四舍五入到任何数字的最接近的增量。

```
gsap.method("selector", {
  x:"random(-100, 100, 5)" //chooses a random number between -100 and 100 for each target, rounding to the closest 5!
});

```

## 相对“>”和“

此功能有助于在时间轴中定位动画。它将补间相对于前一个补间的开始或结束时间放置，并消除了通过代码添加标签的需要。

```
gsap.method('selector',{}, "<" ) //Inserts a tween at the start of the previous tween

gsap.method('selector',{}, ">" ) //Inserts a tween at the end of the previous tween

```

## 新的实用方法

GSAP 已经提供了 15 种新的实用方法。它们中的许多都返回函数，以便可以直接添加到补间动画中。

这些方法包括:

## 关键帧

关键帧是定义单个补间动画应达到的多个状态的一种方式，而不是用同一目标创建多个补间动画。

你可以在`vars`对象中传递一组关键帧，它们将被完美地排序。

```
gsap.method("selector", {keyframes: [
  {x:500, duration:1,delay:2},//adds a delay of 2 seconds before the next animation
  {y:200, duration:1 }
]});

```

## 在您的项目中使用 GSAP3

您可以通过以下任一方法在项目中使用最新版本的 GreenSock。

### 使用 CDN

通过将以下内容添加到 HTML 文件中，您可以使用 CDN 将 GSAP 3 包含在您的项目中。

```
<script src="https://cdn.jsdelivr.net/npm/[email protected]/dist/gsap.min.js"></script>

```

### 通过软件包管理器安装

要通过包管理器安装，请使用下面的代码。

```
#Using Yarn

yarn add gsap

#or Using NPM

npm install gsap

```

然后，将其导入您的 JavaScript 文件中。

```
import { gsap } from "gsap";

```

## 结论

新发布的 GSAP 3 包括无数的更新，以帮助您创建更令人惊叹的动画。还有更多惊人的特性没有在本文中介绍；有关更新的完整列表，请阅读 [GSAP 3 发行说明](https://greensock.com/3-release-notes/)。GreenSock 团队还整理了一份《T2》五大特色的列表，以便在新版本中检验。

哪些新功能让你印象深刻？请在评论区告诉我们。

## 您是否添加了新的 JS 库来提高性能或构建新特性？如果他们反其道而行之呢？

毫无疑问，前端变得越来越复杂。当您向应用程序添加新的 JavaScript 库和其他依赖项时，您将需要更多的可见性，以确保您的用户不会遇到未知的问题。

LogRocket 是一个前端应用程序监控解决方案，可以让您回放 JavaScript 错误，就像它们发生在您自己的浏览器中一样，这样您就可以更有效地对错误做出反应。

[![LogRocket Dashboard Free Trial Banner](img/e8a0ab42befa3b3b1ae08c1439527dc6.png)](https://lp.logrocket.com/blg/javascript-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/javascript-signup)

[LogRocket](https://lp.logrocket.com/blg/javascript-signup) 可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

自信地构建— [开始免费监控](https://lp.logrocket.com/blg/javascript-signup)。