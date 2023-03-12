# 为乐趣和利润创造反应环境-博客

> 原文：<https://blog.logrocket.com/creating-react-contexts-for-fun-and-profit/>

React 总是有它的优点和缺点。回到过去，当[mixin 不被认为是有害的](https://reactjs.org/blog/2016/07/13/mixins-considered-harmful.html)时，最大的不满之一是，你必须将共享状态从你的应用程序的最顶层传递到呈现数据的叶组件，在那里你存储重要的东西。

为了解决这个问题，有一种叫做非官方的、不使用的、非常非官方的[上下文 API](https://react.christmas/2017/18) 的东西，一些库，比如 Redux，利用这个特性取得了巨大的成功。尽管它被《T2》记录为《T3》，但它还是附带了一个大大的警告，你可能不应该这么做。最初的 API 笨重且难以使用，它从来就不是面向用户的特性。它是出于需要而添加的，但它总是被预定为不被使用。

今天，我们拥有更好的工具。新的上下文 API 在 [16.3.0](https://github.com/facebook/react/pull/11818) 中发布，并带有上下文提供者和消费者的概念。随着 16.8 中钩子的加入，我们甚至可以通过钩子来使用上下文，进一步改善开发人员的人机工程学。

这篇文章将深入探讨什么是上下文，什么时候应该使用它们，以及创建它们的最佳方式。

## 到底什么是语境？

我一直将上下文描述为 React 的虫洞。你向它传递一些值，在里面嵌套一些组件树，然后你可以检索这些值，而不必把它们作为道具传递。太神奇了，真的。

另一种看待上下文的方式是，它们是代码的“捷径”。它们可以帮助您创建更流畅的 API，并将组件结构从数据流中分离出来。

然而，当您想到上下文时，它们是 React 中非常有用的特性。在我看来，这是一个不常使用的功能。

## 什么时候你应该去寻找一个背景？

当您不想通过 props 直接传递值时，上下文 API 是一个很好的工具。这有几个用例。

### 当您共享状态时

对于那些需要在应用程序中共享状态的情况，上下文是一个很好的工具。例如，当前的语言或配色方案就是这种状态的很好的例子。

对于应用程序的任何给定部分，您也可以在较低的级别使用上下文，所以不要觉得您需要在您的`index.tsx`文件中包含所有这些提供者。

### 当您创建复合组件时

有时，您创建的组件可以放在一起。你需要在他们之间共享一些信息，但是你不希望为他们所有人传递同一个道具。

我最喜欢的例子是表单控件和 id。标签需要知道它描述的输入字段的 ID，任何错误消息或进一步的描述元素也需要它来设置正确的 aria 标签。手动传递它是一个容易出错的过程，将它传递到顶级组件会更好。

## 单文件“文本-n-挂钩”

好了，我们已经讨论了什么是上下文以及什么时候它是一个好的工具。但是构造它们的最佳方式是什么呢？

前一段时间，我偶然看到了 Kent C. Dodds 的这篇精彩文章,这种方法深受那篇文章的影响。绝对推荐阅读。

这种技术创建了一个只公开两件事的文件——一个上下文提供者和一个消费者挂钩。它不公开底层的上下文对象，并且在您忘记做某件事的情况下，它没有后备值。

这里有一个例子，我们创建了一个语言切换器:

```
const LanguageContext = React.createContext();

export const LanguageProvider = ({ 
  defaultLanguage = 'en', 
  children 
}) => {
  const [language, setLanguage] = React.useState(props.defaultLanguage);
  const contextValue = React.useMemo(
    () => ({ language, setLanguage }), 
    [language]
  );
  return (
    <LanguageContext.Provider value={contextValue}>
      {props.children}
    </LanguageContext.Provider>
  );
}

export const useLanguage = () => {
  const context = React.useContext(LanguageContext);
  if (!context) {
    throw new Error("You have to wrap this component in a LanguageProvider");
  }
  return context;
}

```

那有很多代码，所以让我们一步一步来看:

```
const LanguageContext = React.createContext();
```

这是我们创建上下文本身的地方。注意，我们没有将默认值传递给上下文——这是有意的选择。尽管有些情况下默认值是有意义的，但我通常会避免使用它。这样，我可以警告开发人员，他们忘记在提供程序中包装应用程序的某个部分:

```
export const LanguageProvider = ({ 
  defaultLanguage = 'en', 
  children 
}) => {
  const [language, setLanguage] = React.useState(defaultLanguage);
  const contextValue = React.useMemo(
    () => ({ language, setLanguage }), 
    [language]
  );
  return (
    <LanguageContext.Provider value={contextValue}>
      {props.children}
    </LanguageContext.Provider>
  );
}

```

这是提供者组件。它的职责是包含我们传递的状态，然后将该值传递到我们在上一步中创建的上下文中。

您可能会注意到，我们在对`React.useMemo`钩子的调用中包装了上下文值。这样做是为了避免不必要的重新渲染。如果我们跳过它——每当这个提供者重新呈现时，这个上下文的所有消费者都会重新呈现。尽管你不应该做太多的预优化，但是像上面这样在`useMemo`中包装上下文通常是一个安全的选择。

请注意，您可能会为尽可能少的重新渲染而过度优化。理论上，您可以创建两个不同的上下文，一个用于值，另一个用于更新函数，这样只处理更改值而不显示值的组件就可以避免重新呈现。我还没有发现这种方法的实际用例，但这是一个很酷的技巧。不要因为网上这么说就去做。

```
export const useLanguage = () => {
  const context = React.useContext(LanguageContext);
  if (!context) {
    throw new Error("You have to wrap this component in a LanguageProvider");
  }
  return context;
}

```

谜题的最后一部分是消费者挂钩。这里，我们使用`React.useContext`钩子来检索上下文值。我们添加了一个检查，以确保使用这个钩子的开发人员记得将它包装在一个提供者组件中(这是一个容易犯的错误！)，然后只需返回上下文即可。

我喜欢通过上下文传递“原始”值和更新函数，并在钩子中创建更好的抽象。这样，我可以基于相同的 API 创建新的挂钩，并且我让最终的 API 由挂钩决定，而不是由提供者决定。但是在这个例子中并不需要这么做！

有了这些部分，您就拥有了用一个非常简洁的 API 创建真正好的上下文所需的一切。您所公开的只是两个项目——一个上下文提供者和一个获取该值的钩子。这真是太棒了！

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

我希望这篇文章能给你一些处理上下文的新技巧。感谢阅读！

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)