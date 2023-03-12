# 反冲 0.3 有什么新功能？

> 原文：<https://blog.logrocket.com/whats-new-in-recoil-0-3/>

自从脸书向世界推出作为其 React 应用新标准管理库的[反冲](https://github.com/facebookexperimental/Recoil)已经有一段时间了。尽管该库仍然处于开源项目的实验阶段，但反冲的有前途的特性已经证明了它的价值。

近日，脸书接连发布了[后坐力 0.2](https://github.com/facebookexperimental/Recoil/releases/tag/0.2.0) 和[后坐力 0.3](https://github.com/facebookexperimental/Recoil/releases/tag/0.3.0) 两个新版本。最近的更新引入了一系列错误修复和对整体开发体验的改进。在本指南中，我们将重点介绍一些最重要的新功能。

关于反冲如何工作的复习，请查看我们的[反冲介绍](https://blog.logrocket.com/simple-state-management-react-recoil/)和我们的[创建 CRUD 应用指南](https://blog.logrocket.com/crud-with-facebook-recoil/)。

## 后坐力的`Snapshot`

如果你以前[用过反冲](https://blog.logrocket.com/simple-state-management-react-recoil/)，你会对[原子](https://recoiljs.org/docs/api-reference/core/atom/)的概念很熟悉。原子基本上是一种反冲状态。它由一个`atom()`函数表示，该函数返回一个可以向其中写入数据的`RecoilState`对象。

因为我们主要使用 React 钩子来处理所有事情，反冲使你能够通过几个钩子来处理原子，包括`[useRecoilState()](https://recoiljs.org/docs/api-reference/core/useRecoilState)`。

一个 [`Snapshot`](https://recoiljs.org/docs/api-reference/core/Snapshot/) 物体，顾名思义，就是这些原子内部状态的不变快照。作为一名程序员，您不太可能在日常生活中使用快照，因为状态是不断变化的。然而，它对于状态同步、开发工具特性等非常有用。

基本上有三种主要方法来获取反冲中给定状态的快照:

*   通过`useRecoilCallback`钩子，允许异步访问`Snapshot`
*   通过`useRecoilSnapshot`钩子，允许同步访问`Snapshot`
*   通过`useRecoilTransactionObserver_UNSTABLE`钩子，它允许订阅发生在原子上的任何状态变化的`Snapshot`

[反冲 0.3](https://recoiljs.org/blog/2021/05/14/0.3.0-released/) 为快照引入了一个微小的突破性变化。现在，`Snapshot`对象可能只存在到回调或渲染过程结束。如果您当前的代码使用快照的时间超过了这个时间，您将会在开发人员模式下看到一些警告消息出现在您的控制台上。然而，该团队正在开发一个名为`retain()`的新 API，以支持其更长时间的使用。请注意即将发布的版本中的这一特性。

## `RecoilRoot`的`override`

[`RecoilRoot`](https://recoiljs.org/docs/api-reference/core/RecoilRoot/) 必须始终是任何使用反冲挂钩的组件的祖先。它通常被添加到 React 应用程序的根组件中。然而，这取决于您，因为您的应用程序也可以共享多个代表原子状态独立存储的`RecoilRoot`。

因为这个特性，反冲保证了每个原子对于它所属的每个根总是有它自己的值。例如:

```
const container = renderElements(
  <RecoilRoot>
    <ReadsAtom atom={myAtom} />
    <RecoilRoot>
      <ReadsAtom atom={myAtom} />
    </RecoilRoot>
  </RecoilRoot>
);

```

在这种情况下，嵌套根的行为保持不变，内部根屏蔽了外部根。为了防止这种情况，反冲 0.3 为`RecoilRoot`引入了一个名为`override`的新属性。

如果指定了一个内部`RecoilRoot`，并且将`override`属性(默认为`true`)设置为`false`，则不会对其祖先执行任何功能:

```
const container = renderElements(
  <RecoilRoot>
    <ReadsAtom atom={myAtom} />
    <RecoilRoot override={false}>
      <ReadsAtom atom={myAtom} />
    </RecoilRoot>
  </RecoilRoot>
);

```

例如，当您卸载带有`override`到`false.`的嵌套根集时，预期会出现相同的行为，该操作不会清理祖先根原子。

## 新选择器的`getCallback()`功能

反冲选择器是一个无副作用的“纯函数”,它为一组依赖值返回相同的值。反冲使用它们来确定状态何时发生变化，并通知订阅特定选择器的组件，以便它可以正确地重新呈现。

根据所提供的功能，选择器可以是只读的或可写的。如果你只提供一个`get`，那么选择器返回一个`RecoilValueReadOnly`对象。如果有一个`set`，它返回一个`RecoilState`对象。

下面是一个简单的例子，说明如何使用带反冲的选择器:

```
const doubleSelector = selector({
  key: 'DoubleSelector',
  get: ({get}) => get(myAtom) * 2,
});

```

逻辑是这样工作的:如果任何选择器依赖项被更新，选择器重新评估它的`get`方法。在我们的例子中，如果`myAtom`状态更新，`get`方法也会更新，因为我们现在有了 double 方程的新值。很简单，不是吗？

但是，有时您可能希望使用相同的选择器来返回包含回调的对象。假设您有一个选择器，每当列表中一个项目的 ID 更新时，它都返回一个组件。选择器检测到这一变化，并触发`get`方法来重新计算显示项目相关信息的模态。

通过反冲 0.3 中包含的新`getCallback()`功能，您可以轻松实现这一点:

```
const heySelector = selector({
  key: 'HeySelector',
  get: itemID => ({get, getCallback}) => {
    const onClick = getCallback(({snapshot}) => async () => {
      const item = await snapshot.getPromise(queryForSomethingById(itemID));
      showModal(item);
    });

    return {
      title: `Hey, I'm a component!`,
      onClick,
    };
  },
});

```

这与我们对`useRecoilCallback()`的了解非常相似。然而，`getCallback()`函数更适合于稍后需要访问状态的用例，比如当当前组件上下文之外的一些逻辑需要该对象执行给定操作时。

## 使用 HAMT 提高性能

[反冲 0.2](https://github.com/facebookexperimental/Recoil/releases/tag/0.2.0) 引入了一项增强功能，显著提高了克隆原子值的速度。

如今，当发生写操作时，反冲使用内置的映射数据结构来复制和设置原子值的映射。这一变化引入了使用[哈希数组映射的 trie (HAMT)](https://en.wikipedia.org/wiki/Hash_array_mapped_trie) 实现，它处理关联数组，以结合哈希表和数组映射的 trie(搜索树)的功能。

这一变化将这种写入操作的速度提高了 325 倍，最多可执行 1000 个条目。拥有 10，000 个条目的执行速度快了 3，000 倍，令人难以置信。

你可以看看 [GitHub](https://github.com/facebookexperimental/Recoil/commit/b7d1cfddec66daee6b2b0f84d1d3007c6376860d) 上的新实现。

## 结论

反冲版本 0.2 和 0.3 引入了大部分基本和非破坏性的变化。其中包括 atom 系列在时间和内存消耗的可伸缩性方面的改进，对各种用例更友好的错误抛出，对 Safari 的更好支持，等等。

当[下一个主要版本](https://github.com/facebookexperimental/Recoil/releases)发布，反冲工程从实验状态迁移到正式状态时，你可能会期待一个更大的实质性变化列表。

## [LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。