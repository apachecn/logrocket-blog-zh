# Chrome 74 - LogRocket 博客中的新内容

> 原文：<https://blog.logrocket.com/whats-new-in-chrome-74-6f8b82919c68/>

Chrome 74 已经到来，虽然从面向用户的角度来看没有太多令人兴奋的东西，但对有开发意识的人来说还是有一些好处的。新版本中新增了 Javascript 的私有类字段，允许用户减少动画的媒体查询，Windows 的黑暗模式等等。

### 公共类字段，见私有类字段

你可能还记得 Chrome 72 在一月份增加了对 Javascript 新的公共类字段语法的支持。这是一种简化语法的好方法，允许您直接在类定义中定义类字段，不需要构造函数。

现在在 Chrome 74 中，私有类字段加入了它们的公共表亲。私有类字段的功能大致相同，但是使用#来表示它们是私有的还是公共的，当然，它们只能在类内部访问。

复习一下，公共类字段如下所示:

```
class IncreasingCounter {
  // Public class field
  _publicValue = 0;
  get value() {
    return this._publicValue;
  }
  increment() {
    this._publicValue++;
  }
}
```

私有类字段添加了#:

```
class IncreasingCounter {
  // Private class field
  #privateValue = 0;
  get value() {
    return this.#privateValue;
  }
  increment() {
    this.#privateValue++;
  }
}
```

### 没那么快

事实证明，有些人并不喜欢一些现代网站上的浮华动画。事实上，视差滚动、缩放和跳动的运动效果会使一些运动不舒服。想象一下在浏览网站的时候晕车。不好玩。操作系统已经开始增加选项来减少这种运动，现在有了 Chrome 74，你可以使用媒体查询，偏好-减少运动，来设计这些人。

这是如何工作的？假设你有一个动画按钮。

你可以像这样使用@ media(prefers-reduced-motion:reduce ):

```
button {
  animation: vibrate 0.3s linear infinite both;
}
@media (prefers-reduced-motion: reduce) {
  button {
    animation: none;
  }
}
```

现在，当有人在 MacOS 或另一个操作系统中打开减少运动偏好时，他们将看不到动画。

### 监听 CSS 转换事件

好消息，各位！您现在可以监听 CSS 转换事件，如`transitionrun`、`transitionstart`、`transitionend`和`transitioncancel`。其他浏览器支持这个已经有一段时间了，但是迟做总比不做好。如果您想在转换运行时跟踪或更改行为，那么监听这些事件会很有用。

只是一点代码…

```
element.addEventListener(‘transitionstart’, () => {
  console.log(‘Started transitioning’);
});
```

瞧！您正在您的网站上记录过渡。

你能用这个做什么？嗯，也许你的网站上有一个引人注目的动画来吸引用户的注意力。它运行后，他们被迷住了，你想传递一个重要的信息。你怎么能这样做？过渡事件(transitionend)！

### 利用功能策略 API 进行控制

Chrome 的新功能政策使得启用、禁用或修改 API 和其他网站功能的行为变得容易。有了它们，你可以做一些事情，比如允许 iframes 使用全屏 API，或者改变手机和第三方视频自动播放的默认行为。您可以通过 Feature-Policy 头或 iframe 的 allow 属性来利用这一新功能:

```
HTTP Header: Feature-Policy: geolocation ‘self’
<iframe … allow=”geolocation self”></iframe>
```

要更深入地了解特性政策，请看一下谷歌关于这个主题的文章。

### 拥抱黑暗模式

或者不要。关键是，现在你可以选择。在 Chrome 73 中，为 Mac 用户添加了黑暗模式，但 Windows 没有。Chrome 74 也开始在 Windows 上推出。与 Mac 版本一样，Windows 中的黑暗模式看起来有点像隐姓埋名模式，新标签页、书签栏等应用了不同的主题。

根据谷歌的说法，这将会逐渐发生，所以如果你还不能完全做到，不要担心。黑暗模式来了。

### 还有什么？

这些只是 Chrome 74 的部分亮点。如果你正在寻找真相，请访问谷歌官方网站[chromestatus.com](https://www.chromestatus.com/features#milestone%3D74)，了解所有 Chrome 更新。他们深入研究了这些特性，甚至让你先睹为快，看看未来的版本。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)

* * *