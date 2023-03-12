# 帧运动教程:如何轻松创建反应动画

> 原文：<https://blog.logrocket.com/framer-motion-tutorial/>

***编者按:**本帖最后更新于 2021 年 7 月 29 日。一些信息可能仍然是过时的。*

## 什么是框架运动？

如果你像我一样，当你读到这个标题时，你的第一个想法可能是，“为什么我们还需要 React 的另一个动画库？这越来越累了！”

将 Framer Motion 视为对现有动画库的改进或改造，而不是全新的动画库。

帧运动是 Pose 的继承者，Pose 是 React 使用的最流行的动画库之一。像 Pose 一样，它是建立在 promotion 之上的，promotion 是一个低级的、非个性化的动画库，但是它提供了抽象来简化这个过程。

Framer Motion 改进并简化了 API，这是在不中断更改和重写的情况下无法做到的。一个区别是帧运动只支持 React，而 Pose 支持 React-Native 和 Vue。如果你目前正在使用 Pose，我会推荐更新到帧运动，因为 [Pose 已经被弃用](https://popmotion.io/blog/20200115-pose-is-deprecated/)。

要了解有关成帧器运动的更多信息，我们的教程将涵盖以下主题:

## 什么是春季动画？

默认情况下，帧运动使用[弹簧动画](https://blog.logrocket.com/animations-with-react-spring/)。如果你想要不同的行为，你需要定制这些。近年来，春季动画大受欢迎。另一种方法是使用 CSS 创建动画，例如`1s ease-in`。缓动动画的行为和设定的持续时间看起来不自然或不真实。

弹簧动画应用物理定律来制作更流畅、更自然的动画。他们通过物理原理如动量来做到这一点。它们不会简单地到达并停止在最终状态；它们会反弹过去，然后固定下来。两个值用于定义弹簧动画:刚度和阻尼。调整这些值将使动画表现不同。

## 为什么使用帧运动？

如果大多数动画库使用基于 spring 的动画，那么，为什么要使用 Framer Motion 呢？首先，它有一个很好的 API，很简单，不会用额外的代码填充组件。在大多数情况下，您可以简单地将 HTML 元素替换为 motion 元素——例如，将`div`替换为`motion.div`,这将产生相同的标记，但具有额外的动画道具。

[Framer Motion 官网](https://www.framer.com/motion/)上的文档和例子是我在动画库中见过的最好的。对于大多数常见的用例，您可以在文档中找到 CodeSandbox 示例。有些很简单，有些更复杂，但它们都让您有了很好的理解，并使您能够调整以构建自己的解决方案。与大多数库不同，Framer Motion 还可以处理 SVG 动画。

最后，由于它是 Framer 的一部分，并与 Framer X 设计工具集成，使用这两种工具可以帮助您的工作流程更加顺畅和高效(我个人对此没有任何经验，但我想象它们在一起会很棒)。

## 成帧器动作如何工作

如前所述，Framer Motion 用 Motion 元素替换 HTML 元素。每个 HTML 和 SVG 元素都有一个 motion 元素(例如`<motion.div>`)。这些运动元素与帧运动挂钩，并接受定义动画行为的附加道具。这些道具中最简单的是`animate`，也是你最常使用的一个。传递给`animate`的任何有效值都会使组件在挂载时动画化到那个状态。

```
<motion.div 
  animate={{
    x: '100px'    
  }}
>
Weeee I'm animated
</motion.div>

```

这将导致 div 在加载时向右滑动 100 个像素。如果您想让它在一系列状态中产生动画效果，可以使用关键帧，关键帧是状态值的数组。

```
<motion.div 
  animate={{
    x: ['100px', '0px', '100px']  
  }}
>
Weeee I'm animated
</motion.div>

```

这将使它向右滑动 100 个像素，回到其原始位置，然后再向右滑动 100 个像素。默认情况下，这些都需要相同的时间，但是如果需要，您可以使用`times`按钮来调整设置。

默认情况下，一个元素将从其自然样式的地方开始动画到在`animation.`中定义的状态。在某些情况下，您可能想要定义动画开始的状态。以不透明度为例。假设您希望它不是从`0`开始，而是从一个可见的值开始，比如`0.5`。这可以通过将状态传递给`initial` prop 来定义。如果您传递一个值`false`，初始状态将是动画中的值，并且不会出现初始动画。

```
<motion.div
  initial={{
    opacity: 0.5
  }}
  animation={{
    opacity: 1
  }}
>
</motion.div>

```

虽然我提到 Framer Motion 是一个基于 spring 的库，但从技术上来说，这并不是 100%正确的。对于不涉及运动的动画，弹簧是不可能的。这些使用补间动画，如不透明度和颜色。此外，惯性是根据初速度计算的，并用于`dragTransition`。

通过`transition`道具，可以改变动画以使用不同的类型或以其他方式调整。例如，如果您想要将持续时间设置为`2`并使用补间样式，那么您应该传递以下内容。

```
<motion.div 
  animate={{
    x: '100px'    
  }}
  transition={{
    type: 'tween',
    duration: 2
  }}
>
Weeee I'm animated
</motion.div>

```

还有其他强大的转换可用，比如`stagger`和`delay`子元素。这些允许您轻松地执行传统上涉及多个子元素的复杂动画。

通常，您希望在元素被单击、悬停或以其他方式交互时触发动画。令人欣慰的是，Framer Motion 内置了名为手势的道具。

可用的手势有`hover`、`tap`、`pan`和`drag`。`hover`和`tap`以`while`为前缀，如你所料，仅在鼠标悬停或点击时应用动画。当用户不再悬停或点击时，它将恢复到默认风格，并显示平滑的动画。`drag`和`pan`工作原理不同。启用拖动允许您将元素拖动到页面上的任何位置，它将保留在被拖动到的位置。默认的物理拖动感觉非常自然；拖动会增加动量，当释放时，元素会根据拖动的力度和速度继续向那个方向移动。

这些是我认为的框架运动的基础构件。有了这些技术，你几乎可以创建你能想到的任何类型的动画。

更高级的技术对于动画组件的卸载是必要的，但是我们将在后面用一个例子来介绍其中的一些。

## 如何在 React 中使用帧运动？

关于图书馆本身就足够了。现在让我们构建一个实用的模型来展示常见的技术，并帮助我们了解这个库的运行情况。

从一个基本的 React 应用开始(比如 [Create React App](https://blog.logrocket.com/getting-started-with-create-react-app-d93147444a27/) ，我们需要安装 Framer Motion。我们还将安装一个包来创建名为`uuid`的惟一 id。

```
npm install framer-motion uuid

```

我们的基本应用程序将如下所示，我们将用`App`编写我们的应用程序。

```
// index.js
import React from "react";
import ReactDOM from "react-dom";
import App from "./App";
import "./styles.css";

ReactDOM.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
  document.getElementById("root"),
);

```

只有一些基本样式的规则。

```
// styles.css
html,
body,
#root {
  height: 100%;
  width: 100%;
}

.app {
  height: 100%;
  width: 100%;
}

.list {
  display: flex;
  flex-wrap: wrap;
  width: 1200px;
}

img {
  // disables dragging on img elements
  pointer-events: none;
}

```

我们还将使用 [Bootstrap](https://blog.logrocket.com/how-to-use-bootstrap-with-react-a354715d1121/) 来简化样式。将下面的链接添加到您的 HTML 的头部以包含它。

```
<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css"
integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh"
crossorigin="anonymous"
/>

```

这里是我们的开始`App.js`。我们将有一个项目/卡的列表，可以添加额外的项目或删除现有的项目。它们将跨越多行和多列，您可以通过拖动对它们进行重新排序。我加入了 Unsplash 的随机图片，让它更有趣。

```
import React, { useState } from 'react';
import { motion } from 'framer-motion';
import { v4 as uuid } from ‘uuid’;

const App = () => {
  const [cards, setCards] = useState(defaultCards);

  async function addCard() {
    const res = await fetch('https://source.unsplash.com/random');
    if (!res.ok) return;
    const id = uuid()
    const card = { id, img: res.url };
    setCards([...cards, card]);
  }

 function removeCard() {
    setCards(cards.filter((card) => card !== card.id));
  }

  return (
    <div className='app'>
      <div>
        <h1>Sweet Animations!</h1>
      </div>
      <button className="btn btn-primary" onClick={addCard}>
        Add Card
      </button
      <div className='list'>
        {cards.map(card => (
          <Card
            card={card}
            setCards={setCards}
            removeCard={removeCard}
            key={card.id} />
        ))}
      </div>
    </div>  
  )
}

const Card = ({ card, setCards, removeCard }) => {
  function handleRemove() {
    removeCard(card.id);
  }

  return (
    <div className="card" style={{ width: "18rem" }}>
      <img src={card.img} className="card-img-top" alt="..." />
      <div className="card-body">
        <h5 className="card-title">Cool Image</h5>
      </div>
    </div>
  );
}

const defaultCards = [
  {
    id: 0,
    img: "https://images.unsplash.com/photo-1587900437942-8758241767ef?crop=entropy&cs=tinysrgb&fit=crop&fm=jpg&h=400&ixlib=rb-1.2.1&q=80&w=300",
  },
  {
    id: 1,
    img: "https://images.unsplash.com/photo-1586336900429-71f0642f66fd?crop=entropy&cs=tinysrgb&fit=crop&fm=jpg&h=400&ixlib=rb-1.2.1&q=80&w=300",
  },
];

```

让我们从添加新图像时播放的动画开始。首先，我们需要转换我们的`Card`组件来使用`motion.div`而不是`div`，这样我们就可以访问所有的动画。

```
const Card = ({ card, removeCard }) => {
  function handleRemove() {
    removeCard(card.id);
  }

  return (
    <motion.div className="card" style={{ width: "18rem" }}>
      <img src={card.img} className="card-img-top" alt="..." />
      <div className="card-body">
        <h5 className="card-title">Cool Image</h5>
      </div>
    </motion.div>
  );
};

```

然后我们需要给我们的`motion.div`加上一个`initial`值和一个`animate`值。我们将使它从左边滑动和淡入。

```
initial={{ x: "-300px", opacity: 0 }}
animate={{ x: 0, opacity: 1 }

```

## 成帧器运动中的 SVG 动画

制作 SVGs 动画是我最喜欢的 [React Motion](https://blog.logrocket.com/introduction-to-react-motion/) 功能之一。我们将添加一个 remove 按钮，它将是一个带有`motion.path` s 的`motion.svg`。您可以动画显示路径元素的`pathLength`、`pathSpacing`和`pathOffset`属性。

```
<motion.div className="card" style={{ width: "18rem" }}>
      <motion.svg

        width="24"
        height="24"
        viewBox="0 0 24 24"
        fill="none"
        stroke="#333"
        stroke-width="2"
        stroke-linecap="round"
        stroke-linejoin="round"
        onClick={handleRemove}
      >
        <motion.path d="M 18 6 L 6 18"  />
        <motion.path d="M 6 6 L 18 18"  />
      </motion.svg>      
      <img src={card.img} className="card-img-top" alt="..." />
      <div className="card-body">
        <h5 className="card-title">Cool Image</h5>
      </div>
    </motion.div>

```

让我们给这个 SVG 添加几个动画。在 mount 上，它会一个接一个地画两条线。悬停时，它会使线条变粗。

为了达到这个效果，我们需要父`motion.svg`和子`motion.path`链接起来，这样我们就可以错开子路径的动画。

我们可以使用名为`variants`的成帧器运动道具来做到这一点。`variants`接受具有已定义状态属性的对象。然后你可以将它们作为动画状态的值来传递，比如`animate`或者`initial`。除非被覆盖，否则这些值将自动传递给子代。

```
// example usage
<motion.div
  variants={{ 
    mount: { opacity: 0.5 },
    rest: { opacity: 1 }
  }}
  initial='mount'
  animate='rest'
/>

```

我们将关注三种状态:`initial`、`animate`和`hover`。在`animate`内，我们还需要定义一个转场，这个转场将决定动画需要多长时间以及孩子的蹒跚。

`staggerChildren`定义每个孩子的动画开始之间应该经过多长时间。`when`定义这些动画应该开始的点。值为`afterChildren`意味着交错延迟将发生在子动画之后。`beforeChildren`表示相反的意思——在动画开始前等待规定的时间。

这里的主要区别在于第一个动画，它是立即开始(`afterChildren`)还是短暂等待(`beforeChildren`)。我们不想耽误第一个孩子的动画，所以我们将使用`afterChildren`。

```
const variants = {
  initial: {
    strokeWidth: 2,
    pathLength: 0,
  },
  animate: {
    pathLength: 1,
    transition: { duration: 1, when: "afterChildren", staggerChildren: 1 },
  },
  hover: {
    strokeWidth: 4,
  },
};

<motion.svg

  width="24"
  height="24"
  viewBox="0 0 24 24"
  fill="none"
  stroke="#333"
  stroke-width="2"
  stroke-linecap="round"
  stroke-linejoin="round"
  variants={variants}
  initial="initial"
  animate="animate"
  whileHover="hover"
  onClick={handleRemove}
>
  <motion.path d="M 18 6 L 6 18" variants={variants} />
  <motion.path d="M 6 6 L 18 18" variants={variants} />
</motion.svg>

```

类似于我们在 mount 上创建的动画，我们想为退出动画创建一个，它将向上滑动并淡出。

当一个`Card`被移除时，React 从 DOM 中移除该组件。因此，我们需要用一个特殊的组件`AnimatePresence`来包装我们的`Card`组件。这允许 Framer Motion 跟踪组件何时卸载，并延迟它，直到动画结束。

我们实际上可以包装组件列表，而不是单独包装每个组件，但是我们需要确保它们是直接子组件。

```
<div className="list">
    <AnimatePresence>
      {cards.map((card) => (
        <Card key={card.id} card={card} removeCard={removeCard} />
      ))}
    </AnimatePresence>
</div>

```

一旦被这个组件包装，`motion.div`接受一个`exit`道具，这将是退出时的动画状态。

```
<motion.div
  initial={{ x: "-300px", opacity: 0 }}
  animate={{ x: 0, opacity: 1 }}
  exit={{ y: '-300px', opacity: 0 }}
>
</motion.div>

```

当一个`Card`被移除时，假设我们希望其他的`Card`滑过而不是跳到位置上。成帧器运动使这项通常困难的任务变得轻而易举。一个运动组件接受一个道具`layout`，它将自动动画显示它在布局中位置的任何变化。

```
 <motion.div
  initial={{ x: "-300px", opacity: 0 }}
  animate={{ x: 0, opacity: 1 }
  exit={{ exit={{ y: '-300px', opacity: 0 }}
  layout
>
</motion.div>

```

您可能会注意到，不幸的是，在移除的项目动画完成之前，新位置的动画不会出现。在动画完成之前，该项仍然存在于 DOM 中，因此，其他项的位置还没有改变。这不是一个容易解决的问题，并且《框架运动》的作者[已经承认这是一个困难的案例](https://github.com/framer/motion/issues/215)。

不确定图书馆是否会提供解决方案。幸运的是，您可以通过向项目添加一些条件样式来解决这个问题，这将在退出动画出现时将它们从 DOM 的布局中移除。这允许位置转换同时发生。

为了实现这一点，我们需要知道卡片何时被取出以及它在此时的位置。Framer Motion 有两个内置挂钩，可供`AnimatePresence`的孩子使用，他们是`usePresence`和`useIsPresent`。这两个钩子都返回`isPresent`,通知你项目何时被移除，但是在任何退出动画出现之前。`usePresence`还返回了一个`safeToRemove`函数，您必须手动调用该函数才能从 DOM 中完全移除元素。

在这种情况下，我们只需要`isPresent`变量，所以我们将使用`useIsPresent`和帧运动将处理删除元素。

为了获得卡的位置，我们将使用一个`ref`，这是 React 访问 DOM 节点的方法，以及`getBoundingClientRect`。当删除一个项目时，它会首先更新`position`。当`isPresent`为假时，我们将用`position: absolute`来设计`Card`的样式，将它从布局中移除，并将`left`和`top`设置到计算的位置。

```
const Card = ({ card, removeCard }) => {
  const [position, setPosition ] = useState({ left: 0, top: 0})
  const cardRef = useRef(null);
  const isPresent = useIsPresent();

  function handleRemove() {
    const position = cardRef.current.getBoundingClientRect();
    setPosition(position);
    removeCard(card.id);    
  }
  return (
    <motion.div
      initial={{ x: "-300px", opacity: 0 }}
      animate={{ x: 0, opacity: 1 }}
      exit={{ y: "-300px", opacity: 0 }}
      layout
      className="card"
      style={{
        width: "18rem",
        position: !isPresent ? "absolute" : "block",
        left: !isPresent ? position.left : "auto",
        top: !isPresent ? position.top : "auto",
      }}
      ref={cardRef}
    >
      <img src={card.img} className="card-img-top" alt="..." />
      // ... svg
      <div className="card-body">
        <h5 className="card-title">Cool Image</h5>
      </div>
    </motion.div>
  );
}

```

## 在帧运动中拖动

另一个常见的特性是能够通过拖动项目来重新排序项目列表。我们将启用拖动来向左/向右或向上/向下移动卡片。首先，我们需要使用`drag`道具来启用拖动。我们允许向任何方向拖动，但是如果你有一个垂直/水平的列表，你可以用`drag='x'`或`drag='y'`来限制方向。

注意:由于我们使用的是图像，我们还必须禁用对`*img*`元素的自然拖动。根据您的浏览器支持，您可以通过几种方式实现这一点。这里我们用一些 CSS 来简单的移除指针事件，这已经在我们的 CSS 文件中完成了。

```
<motion.div
  // other properties
  drag
/>

```

这将允许您将元素拖动到页面上的任何位置，但它实际上会停留在那个位置。您可以使用`dragConstraints`定义您希望元素被拖动到的区域。

 ``dragConstraints`取一个具有上/下/左/右属性的对象。我们将这些都设置为`0`，这样元素在拖动后会返回到相同的位置(您仍然可以将它拖动到这些界限之外)。如果您想调整元素可以从这些约束拖动多远，您可以使用`dragElastic`道具，它取一个在`0`(没有超出约束拖动)和`1`(从约束拖动到您想要的距离)之间的值。

```
<motion.div
  // other properties
  drag
  dragConstraints={{ top: 0, right: 0, bottom: 0, left: 0 }} 
/>

```

还有一些回调函数道具可以用来插入不同的行为——即`onDrag`、`onDragStart`、`onDragEnd`。这些函数传递事件的参数和一个包含相关信息的拖动对象。我们将使用`onDragEnd`来计算我们的重新定位。

```
<motion.div
  // other properties
  drag
  dragConstraints={{ top: 0, right: 0, bottom: 0, left: 0 }}
  onDragEnd={(e, drag) => moveCard(drag.offset, i)} 
  // we will write the move card function in a moment
/>

```

通过传递给`onDragEnd`的拖动参数，我们可以得到元素从其自然位置被拖动的距离。在`moveCard`中，我们将使用这个点来确定方向，以及它是否被拖得足够远以构成重新排序。

```
// App.js
function moveCard(offset, index) {
  let reorderedCards = [...cards];
  let movedFrom = index;
  let movedTo = index;

  if (offset.x <= -100) {
    movedTo = index - 1;
  }
  if (offset.x >= 100) {
    movedTo = index + 1;
  }
  if (offset.y >= 100) {
    movedTo = index + 4;
  }
  if (offset.y <= -100) {
    movedTo = index - 4;
  }

  if (movedFrom !== movedTo && cards[movedFrom] && cards[movedTo]) {
    reorderedCards[movedFrom] = cards[movedTo];
    reorderedCards[movedTo] = cards[movedFrom];
    setCards(reorderedCards);
  }
}

// don't forget to pass it down as a prop to the `Card` element.

```

## 摘要

现在我们有了一个基本的应用程序，它实现了各种常见的功能和动画。希望本教程给你一个坚实的基础，添加一些很棒的动画到你的库中，并随着时间的推移建立它们来满足你的所有需求。

## [LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。`