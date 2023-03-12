# DOM 观察的 CSS 动画

> 原文：<https://blog.logrocket.com/css-animations-for-dom-observation/>

Web 开发人员的任务通常是构建能够适应大量变化、使用条件和用户操作的应用程序。

简而言之，web 应用程序在任何给定时间的外观和行为都是许多变量的函数:应用程序状态、设备视口、设备能力、网络条件和用户偏好等等。为了跟踪这些变量并对变化做出反应，需要某种形式的观察和检测。

长期以来，观察一直是 web 开发中不可或缺的一部分。你可能已经知道像`IntersectionObserver`、`MutationObserver`、`PerformanceObserver`等 Web APIs。然而，很容易忽略事件监听器本身也是观察者。事实上，每次您为一个或多个事件目标上的特定事件设置事件监听器时，您就获得了一个观察者。

也就是说，这是我们的一个问题:

> 如果我们对观察和响应 DOM 中某种形式的变化感兴趣，比如节点插入、节点移除、属性改变等等，那该怎么办呢？？

有人可能会说，“嗯，那没有必要。”另一个可能会尖叫，“T0！”

但是如果我说 CSS 动画呢？想一想。

在本文中，我们将使用实际的例子来探索如何利用 CSS 动画来观察 DOM 中的变化，以及可以观察到的变化种类。

## 背景

想象一下，在一个项目中，您需要构建一个脚本，开发人员可以将该脚本添加到他们的 web 应用程序中，以嵌入某些元素，如 iframes、按钮、小部件等。在应用程序的不同部分。

比方说，对于每一个带有`.share-button`类的元素，你都需要用一种特殊的按钮来替换它，以便在某个平台上共享某些内容。

你可以很快做这样的事情:

```
document.addEventListener('DOMContentLoaded', () => {
  replaceWithShareButton(document.querySelectorAll('.share-button'));
});

function replaceWithShareButton (nodes) {
  Array.from(nodes).forEach(node => {
    const button = document.createElement('button');

    // set button attributes and properties here

    requestAnimationFrame(() => {
      node.parentNode.replaceChild(button, node);
    });
  });
}
```

只要所有的`.share-button`元素在加载时已经作为页面标记的一部分存在——例如，如果页面是服务器端呈现的，那么这就是完美的。然而，当更多的`.share-button`元素被动态添加到页面中时，什么都不会发生。

为了处理这个新的场景，您决定利用`MutationObserver` API 来观察 DOM 树中添加的新的`.share-button`元素，并用特殊的按钮有效地替换它们。

下面的代码片段显示了您可以对前面的`DOMContentLoaded`事件监听器进行的一些修改，以便设置突变观察器。

```
document.addEventListener('DOMContentLoaded', () => {
  replaceWithShareButton(document.querySelectorAll('.share-button'));

  const observer = new MutationObserver(mutations => {
    for (let mutation of mutations) {
      const nodes = Array.from(mutation.addedNodes)
        .filter(node => node.classList.contains('share-button'));

      replaceWithShareButton(nodes);
    }
  });

  observer.observe(document.body, { childList: true, subtree: true });
});
```

感谢令人敬畏的`MutationObserver` API，你已经成功地用很少的努力让按钮替换完美地工作。

几天后，项目经理找到你，说他已经收到了开发人员的投诉，说一些浏览器上没有按钮替换，要求你尽快修复。

在做了一些研究之后，你很快意识到这个问题是因为一些仍然在使用的浏览器不支持`MutationObserver` API。

![MutationObserver API Browser Support](img/ddc1d7d5066020c33836e5707c528050.png)

Browser support for the MutationObserver API, via Can I use…

现在的问题是:在确保支持更多浏览器的同时，你还有其他选择吗？

当然，您可以尝试长轮询技术，每隔一段时间检查刚刚添加的`.share-button`元素并替换它们。因此，您再次修改`DOMContentLoaded`事件监听器以使用长轮询，如下所示:

```
document.addEventListener('DOMContentLoaded', () => {
  replaceWithShareButton(document.querySelectorAll('.share-button'));

  const POLLING_INTERVAL = 2000;

  setTimeout(function replaceNewerNodes () {
    replaceWithShareButton(document.querySelectorAll('.share-button'));
    setTimeout(replaceNewerNodes, POLLING_INTERVAL);
  }, POLLING_INTERVAL);
});
```

虽然使用这种技术可以在更广泛的浏览器上工作(包括非常老的浏览器)，但是很明显它会有一些性能问题，尤其是在频繁调用`setTimeout()`的情况下。此外，由于轮询间隔，按钮替换不会实时发生，所以您决定不采用这种技术。

所以你又开始寻找替代品了。

你不认为你应该尝试将 CSS 动画融入其中吗？让我们看看这如何帮助解决你的小问题。

## 基本原理

几年前，大卫·沃什在他的博客上写了一种用 JavaScript 和 CSS 动画检测 DOM 节点插入的技术，他说这是由当时 Mozilla 的开发者 T2·丹尼尔·布赫纳介绍给他的。

这种技术的基本原理如下:

*   CSS 动画被添加到要观察的目标元素中
*   当这些元素被添加到 DOM 中时，CSS 动画开始播放
*   每当 CSS 动画开始播放时，就会触发`animationstart`事件

因此，要解决您之前遇到的问题，您必须首先在页面上设置以下样式:

```
.share-button {
  animation-name: button-inserted;
  animation-duration: 1ms;
}

@keyframes button-inserted {
  from { opacity: 0.9999999 }
  to   { opacity: 1 }
}
```

然后，您可以更新`DOMContentLoaded`事件监听器，如下所示:

```
document.addEventListener('DOMContentLoaded', () => {
  replaceWithShareButton(document.querySelectorAll('.share-button'));

  document.addEventListener('animationstart', handleButtonInsertion, false);
  document.addEventListener('MSAnimationStart', handleButtonInsertion, false);
  document.addEventListener('webkitAnimationStart', handleButtonInsertion, false);
});

function handleButtonInsertion (evt) {
  if (evt.animationName === 'button-inserted') {
    replaceWithShareButton([evt.target]);
  }
}
```

有了这些改变和添加，一切都像以前使用`MutationObserver` API 一样工作，但是对旧浏览器的支持增加了一点。然而，与`MutationObserver` API 相比，使用 CSS 动画技术在可以观察的 DOM 修改程度上非常有限。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 第一个，最后一个，也是唯一的孩子

我们已经看到了如何使用 CSS 动画技术来检测新的节点插入，但是我们还能做什么呢？

假设我们希望确保一个元素始终是其父元素的第一个、最后一个或唯一的子元素，而不管新节点是添加到父元素中还是从父元素中移除。

我们可以像这样使用 CSS 动画技术:

```
.force-first:not(:first-child),
.force-last:not(:last-child),
.force-only:not(:only-child) {
  animation-name: reset-child-position;
  animation-duration: 1ms;
}

@keyframes reset-child-position {
  from { opacity: 0.9999999 }
  to   { opacity: 1 }
}
```

这里，我们使用了三个类——`.force-first`、`.force-last`和`.force-only`——分别指定目标元素总是其父元素的第一个、最后一个或唯一的子元素。

请注意，`:not()`伪类与`:first-child`、`:last-child`和`:only-child`伪类一起使用来触发 CSS 动画，该动画将用于确保目标元素在每次节点插入后都正确地定位在其父元素中。

我们需要确保这些行为的事件侦听器如下:

```
document.addEventListener('DOMContentLoaded', () => {
  document.addEventListener('animationstart', resetChildNodePosition, false);
  document.addEventListener('MSAnimationStart', resetChildNodePosition, false);
  document.addEventListener('webkitAnimationStart', resetChildNodePosition, false);
});

function resetChildNodePosition (evt) {
  if (evt.animationName === 'reset-child-position') {
    const elem = evt.target;
    const parent = elem.parentNode;
    const classes = elem.classList;
    const clonedElem = elem.cloneNode(true);

    const lastChild = parent.lastChild;
    const firstChild = parent.firstChild;

    if (classes.contains('force-only')) {
      if (elem !== firstChild || elem !== lastChild) {
        parent.innerHTML = '';
        parent.appendChild(clonedElem);
      }
      return;
    }

    if (classes.contains('force-last')) {
      if (elem !== parent.lastChild) {
        parent.removeChild(elem);
        parent.appendChild(clonedElem);
      }
      return;
    }

    if (classes.contains('force-first')) {
      if (elem !== parent.firstChild) {
        parent.removeChild(elem);
        parent.insertBefore(clonedElem, parent.firstChild);
      }
      return;
    }
  }
}
```

仔细观察上面的代码片段会发现，在任何时候，父元素中只有一个元素被指定为`.force-first`、`.force-last`或`.force-only`。

那么，当父元素中有多个元素被指定为`.force-first`时会发生什么呢？我们的代码遇到了一些问题。

下面的 gif 展示了这个问题的一个简单演示，其中同一个父元素中的两个元素都在努力成为第一个子元素。

![Two Elements Struggling For The First Child Position](img/e6146d204024e8f62841500ce459f3be.png)

Two elements struggling for the first child position.

要解决这个问题，我们必须决定在多个元素争夺同一位置的情况下哪个元素获胜。以下是我们可以做出的一些合理决定:

*   对于相应的子位置，应该只考虑具有`.force-only`或`.force-first`类的父元素中的第一个元素
*   只有具有`.force-last`类的父元素中的最后一个元素才应该被认为是最后一个子元素

基于这些决定，我们必须做出以下修改:

```
*// ...code truncated here...*

if (classes.contains('force-only')) {
  const target = parent.querySelectorAll('.force-only')[0];

  if (elem === target && !(elem === firstChild && elem === lastChild)) {
    parent.innerHTML = '';
    parent.appendChild(clonedElem);
  }

  return;
}

if (classes.contains('force-last')) {
  const targets = parent.querySelectorAll('.force-last');
  const target = targets[targets.length - 1];

  if (elem === target && elem !== parent.lastChild) {
    parent.removeChild(elem);
    parent.appendChild(clonedElem);
  }

  return;
}

if (classes.contains('force-first')) {
  const target = parent.querySelectorAll('.force-first')[0];

  if (elem === target && elem !== parent.firstChild) {
    parent.removeChild(elem);
    parent.insertBefore(clonedElem, parent.firstChild);
  }

  return;
}

*// ...code truncated here...*
```

## 没有孩子

在上一节中，我们主要关心的是控制父元素中某些子元素的行为。看到如何使用这种技术来确保几个 DOM 元素在它们的父元素中保持特定的位置已经非常有趣了。

在这一节中，让我们探索使用这种技术的其他方法，通过控制一个元素在有或没有子元素(ren)时的行为。

假设我们想要确保一个元素没有子元素，也就是说，我们总是希望这个元素为空。同样，我们可以通过创建如下所示的样式规则来应用 CSS 动画技术:

```
.without-child:not(:empty) {
  animation-name: element-not-empty;
  animation-duration: 1ms;
}

@keyframes element-not-empty {
  from { opacity: 0.9999999 }
  to   { opacity: 1 }
}
```

此外，我们将像这样设置事件侦听器:

```
document.addEventListener('DOMContentLoaded', () => {
  document.addEventListener('animationstart', removeNodeChildren, false);
  document.addEventListener('MSAnimationStart', removeNodeChildren, false);
  document.addEventListener('webkitAnimationStart', removeNodeChildren, false);
});

function removeNodeChildren (evt) {
  if (evt.animationName === 'element-not-empty') {
    const elem = evt.target;

    if (elem.children.length > 0) {
      elem.innerHTML = '';
    }
  }
}
```

鉴于我们之前已经做过的，这很简单。也就是说，我们仍然可以探索处理空元素的其他方法。

假设我们想要确保一个元素不为空——这与我们之前的例子有些相反。如果元素碰巧是空的，假设我们想简单地从 DOM 中删除它。我们可以为此创建一些样式规则:

```
.no-without-child:empty {
  animation-name: element-empty;
  animation-duration: 1ms;
}

@keyframes element-empty {
  from { opacity: 0.9999999 }
  to   { opacity: 1 }
}
```

事件侦听器应该按如下方式设置:

```
document.addEventListener('DOMContentLoaded', () => {
  document.addEventListener('animationstart', removeEmptyNode, false);
  document.addEventListener('MSAnimationStart', removeEmptyNode, false);
  document.addEventListener('webkitAnimationStart', removeEmptyNode, false);
});

function removeEmptyNode (evt) {
  if (evt.animationName === 'element-empty') {
    const elem = evt.target;
    elem.parentNode.removeChild(elem);
  }
}
```

## 结论

在本文中，我们已经能够看到 CSS 动画如何被用来观察 DOM 突变——特别是节点插入和节点空。虽然这种技术可以在观察元素属性的领域中进一步探索，但是对于那个目的来说通常是不切实际的。

很明显，它在很多方面都不如`MutationObserver` API 强大，只有在特别关心支持旧浏览器的情况下，才应该考虑将其作为一种替代，并且要观察的变化在本文讨论的范围之内。

也就是说，我正在构建一个非常简单和轻量级的 JavaScript 库，目的是基于本文讨论的 CSS 动画技术观察 DOM 突变并做出反应。我会[发很多关于它的微博](https://twitter.com/gladchinda)——小心点。

我很高兴你看到了这篇文章的结尾。这是一个相当长的，我真的希望它值得你的时间。一如既往，请记住:

*   **评论**您的反馈
*   与某人分享
*   [**在 Twitter 上关注**](https://twitter.com/gladchinda) 我

编码快乐！！！

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。