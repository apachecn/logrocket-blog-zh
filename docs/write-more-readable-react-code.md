# 如何写出更具可读性的 React 代码

> 原文：<https://blog.logrocket.com/write-more-readable-react-code/>

代码最重要的方面之一是它的可读性。易于阅读的代码是编写良好的代码的结果，它有很多长期的好处。它将更容易阅读、理解、维护、审查，更不容易出现意外错误，并且当其他开发人员必须与代码交互时，通常会使他们的生活变得容易得多。

由于其复合性，代码可读性的困难在 React 开发中尤为突出。产生的代码充满了大量的代码模式，非常零碎，并且通常分布在多个地方。这进一步增加了编写可读 React 代码的难度。

然而，编写可读的 React 代码并不是不可能的任务。这样做首先要让整个过程成为一个有意识的过程。为此，知道应该关注哪些方面是很重要的。

为了帮助您做到这一点，本文将涵盖在编写可读性更高的代码时应该考虑的多个主题及其对可读性的影响，包括:

1.  [代码长度](#length-code)
2.  [密切相关的代码如何组合在一起](#how-closely-related-code-grouped-together)
3.  [复杂的 JavaScript 结构](#complicated-javascript-constructions)
4.  [一次处理多少个流](#how-many-flows-handled-at-once)
5.  [变量命名](#variable-naming)
6.  [突出异同点](#highlighting-similarities-differences)

希望这些信息能够为您现在和将来编写更具可读性的 React 代码提供坚实的基础。

## 代码长度

在讨论代码可读性时，最常提到的话题是代码的长度。在垂直和水平方向上，代码越短，可读性越高。主要原因是代码越短，开发人员需要阅读的代码就越少。这减少了混淆的机会，否则会使开发人员更难通读代码。

然而，实际上，这种区别并不明显。虽然编写更少的代码可以大大提高可读性，但这并不是最终的保证。还有一个转折点，缩短代码会使可读性从有利变为有害。

当假设代码更短有利于代码可读性时，另一个经常被牺牲的方面是显式性。

以内联条件呈现的概念为例，它通常位于 AND 和三元运算符之间。

```
const Component = ({ linkUrl }) => (
    <div>
        { !!linkUrl && <PrettyLink url={linkUrl} /> }
    </div>
}

// -- OR --

const Component = ({ linkUrl }) => {
  return (
    <div>
      {linkUrl !== undefined && linkUrl !== null ? (
        <PrettyLink url={linkUrl} />
      ) : null}
    </div>
  );
};

```

前者被认为更短更简洁，而后者被认为冗长，只有在条件的两个分支都是必要的时候才合适。

但是使用`[&& operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_AND#:~:text=The%20logical%20AND%20(%20%26%26%20)%20operator,it%20returns%20a%20Boolean%20value.)`意味着一个分支没有被显式声明，所以由读者来决定另一个分支的预期行为是什么(即使它不呈现任何内容)，它是否被错误地遗漏了，并寻找没有提供给他们的信息。

为了节省代码长度，这显然牺牲了显式性。这是否更具可读性取决于场景，但并不总是像“越短越好”那样简单。

我们在 React 中创建定制组件、钩子和函数的原因之一是因为它将相关代码分组。它不是将代码分散在各处，而是在特定的上下文或目的下将所有东西打包在一个位置。

同样，相似代码分组的距离也影响可读性。

React 开发中这种情况最大的例子之一是引入了 [React 钩子](https://blog.logrocket.com/react-hooks-the-good-the-bad-and-the-ugly/)。在 Hooks 出现之前，React 组件包含逻辑的唯一方法是使用类组件。为此，我们必须实现生命周期方法，并将逻辑放在适当的位置。

不幸的是，这些生命周期方法分散在组件中，并且在某些情况下，是按照特定的顺序编写的——您的逻辑被分解并分布在组件中。这增加了相关代码块之间的距离，并且经常使其难以看到和理解逻辑流程。

随着钩子的引入，我们不仅获得了跨多个组件重用逻辑的方法，还获得了将所有相关代码紧密组合在一起的方法。这缩短了相似代码分组的距离。

这是代码可读性和可维护性的一个重要因素，因此应该尽可能记住。

## 复杂的 JavaScript 结构

最后，React 开发的主要部分是 JavaScript。实现 React 组件、逻辑、钩子等等都是用 JavaScript 完成的，这意味着所有的 JavaScript 都可以用于它。这可能是一个优点，也可能是一个缺点。

作为一种编程语言，JavaScript 非常广泛，允许许多不同的实现。但是这种扩展语言的一个主要缺点是，不是每个人都同样熟悉所有的语言细节。

JavaScript 中的许多语言特性都是基于内在的细节或隐含的行为，当与它的动态特性结合时，这就增加了它的复杂性。这两个因素使某些 JavaScript 结构变得更难理解，并且会根据开发人员对它们的熟悉程度对代码的可读性产生负面影响。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

让我们讨论几个常见的 JavaScript 结构示例，我注意到它们更难理解。对于所有这些结构，理解它们背后隐含的概念对于理解结构本身是至关重要的。没有这些信息会严重影响可读性。

虽然大多数 React 开发人员可能会意识到这些构造，但这不是一个给定的保证，因此需要记住。

### 用于数据转换的`Array.reduce`功能

```
const idObjects = ids.reduce((prev, curr) => {
    return {
        ...prev,
        [curr]: {
            id: curr,
            value: getValueFromId(id),
        }
    };
}, {});

```

`Array.reduce`函数通常用于将数组转换成不同的数据结构，比如对象。代码非常简洁，但也很难理解——需要跟踪很多细节:

*   原始数组
*   `reduce`通话
*   以前的值
*   当前值
*   初始结构
*   由此产生的结构
*   不同的值是如何组合的

这些信息的顺序也不自然，就像最初的结构是最后定义的。在此基础上改进的另一种结构是 for 循环。尽管它被认为更加丑陋和冗长，但由于信息的顺序更加简单，因此生成的代码通常更具可读性:

*   首先是变量的初始化
*   其次是循环的长度和限制
*   第三步是对相关变量的所有操作

### 用于条件渲染的`&&`运算符

```
const Component = ({ hasImage }) => {
    // ...
    return (
        <div>
            {hasImage && <Image />}
        </div>
    );
}

```

[内联条件渲染](https://www.chakshunyu.com/blog/react-readability-analysis-of-inline-conditional-rendering/)的一个非常常用的构造是`&&`操作符。基于左侧操作数的值，可以呈现右侧操作数。

然而，这种结构仅仅是由于被称为[短路](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_AND#short-circuit_evaluation)的隐式 JavaScript 行为才有效。当对`&&`表达式求值时，左边的运算符求值为 falsy 值，则返回该操作数，并完全跳过右边操作数的求值。

## 一次处理多少个流

任何给定的 web 应用程序都必须处理所有类型的信息流。随着 web 应用程序日益增加的复杂性，它也不再仅仅是处理一个数据流或逻辑流。任何 UI 都会有十几个、几百个甚至一千个更小的部分。每一个部分都与某种信息相关联，并有多种信息流通过它们。

React 为我们提供了很多工具来实现数据和逻辑流。想想像`useState`、`useReducer`、`useEffect`和`useLayoutEffect`这样的[现成钩子](https://blog.logrocket.com/react-hooks-cheat-sheet-unlock-solutions-to-common-problems-af4caf699e70/)，以及以[定制钩子](https://blog.logrocket.com/advanced-react-hooks-creating-custom-reusable-hooks/)的形式重用逻辑的能力。虽然这些工具允许 React 开发人员非常容易和有效地处理流，但它们在某些场景中也有缺点。

将大量的流纠缠在一个位置是非常容易的，因为将流实现到组件中是非常简单的。多个流通过一个组件，或者将多个流的逻辑片段组合到一个`useEffect`钩子中，这种情况并不少见。

```
const Component = ({ data }) => {
    // Logic...

    // Here, we're combining flows for the data request, empty state, filled logic state, and
    // server error feedback into a single `useEffect`. It feels natural, but is it readable?
    useEffect(() => {
        if (!data) {
            setRequestState("LOADING");
        } else if (data.length === 0) {
            setRequestState("DONE");
            triggerEmptyState();
        } else {
            setRequestState("DONE");
            if (dataIsValid(data)) {
                updateOtherLogicWithData(data);
            } else {
                informServerDataIsInvalid();
            }
        }
    }, [data, updateOtherLogicWithData, informServerDataIsInvalid, triggerEmptyState]);

    // Render...
}

```

像这样将来自多个流的逻辑组合到一个单独的`useEffect`钩子中的问题是，它会对代码的可读性产生负面影响。将不同的流紧密地放在一起，会使它们交织在一起，难以分开，并且紧密耦合。由此产生的代码将变得更加难以理解和维护。

## 变量命名

一般来说，软件开发中最困难的事情之一就是命名。正确的名称可以提高或降低代码的可读性。React development 也不例外。但是由于 React 的复合性质，有许多实体需要命名。钩子、组件、函数、变量、道具、回调、上下文——等等。

再加上对可重用性的关注，它们的名称不能太具体，以免妨碍可重用性，但也不能太一般化，因为它们应该反映它们的范围和上下文。

正确地命名它们可以让您在编写更可读的代码方面走得更远。它不仅有利于代码的可读性，还可以提高代码的质量，增加未来的可维护性。几个例子是:

*   在组件的属性中包含一个前缀来表示 API 的类型——这让用户清楚地知道它的预期行为以及如何使用它
    *   例如，不命名布尔属性`valid`，而是考虑将其命名为`isValid`；不叫`validate`，考虑叫它`onValidate`
*   如果您有多个配置相似方面的属性，那么您很可能简化 API 设计。这可以通过名字很快注意到，比如同时有一个`isLoading`和一个`isError`布尔属性
    *   考虑一个名为的枚举属性，它可以捕获前面的两个属性，可以使 API 设计更加清晰，更加整洁
*   考虑一下组件的使用范围——如果你有一个对整个平台通用的虚拟角色渲染组件，那么称它为`Avatar`是没问题的，但是如果它专门用于卡片的某个部分，那么在名称中将其明确化并称之为`CardAvatar`是有益的
*   根据回调做了什么来命名回调，而不是根据它们将如何被使用来命名:如果您需要提供一个对组件的`onChange`属性的回调，那么命名那个回调`onChange`将不会给读者增加任何有用的信息
    *   例如，相反，考虑以它们所做的事情来命名:`updateStateValue`增加了可读性，因为它阐明了回调所做的事情，以及当所使用的组件中发生适当的变更事件时将会发生什么

这些具体的例子说明了不同的变量命名会如何改变 React 代码的可读性和质量。但是这不仅仅局限于这些例子——最重要的是在写作时记住这个主题，考虑你的命名风格的质量和特殊性，并潜在地改进它。

## 突出 React 代码中的相似或不同之处

React 开发中有许多不同类型的代码——CSS、JS、HTML(或 JSX)——正因为如此，许多代码都位于一个位置。尤其是在这样一个以 UI 为中心的开发领域，这意味着将会有许多场景，其中的代码要么有微小的相似之处，要么有微小的差异。

正确地突出显示这些事件可以极大地提高代码的可读性。请考虑以下情况:

```
const SomeSection = ({ isEditable, value }) => {
    if (isEditable) {
        return (
            <OuterSection>
                <Header>Edit this content</Header>
                <Body>{value}</Body>
                <SectionButton>Clear content</SectionButton>
            </OuterSection>
        );
    }
    return (
        <OuterSection>
            <Header>Read this content</Header>
            <Body>{value}</Body>
        </OuterSection>
    );
}

// -- OR --

const SomeSection = ({ isEditable, value }) => {
    return (
        <OuterSection>
            <Header>
                { isEditable ? "Edit this content" : "Read this content"}
            </Header>
            <Body>{value}</Body>
            { isEditable ? <SectionButton>Clear content</SectionButton> : null }
        </OuterSection>
    );
}

```

当这些事件突出显示时，您可以更容易地看到某些流、分支或结果是如何相互关联的，哪些部分是连接的，哪些部分是不同的，等等。

如果您使用的方法不周全，可能会导致重复代码，或者需要更高认知负荷的代码——这意味着要跟踪更多的东西、不连贯的流程，以及更难理解的代码。

## 最后的想法

编写可读的代码是软件开发最重要的方面之一，这不是一项简单的任务。它的困难在 React 开发中尤为突出，因为它具有复合性、分散性和分布性。在处理代码可读性时，有更多的代码和因素要考虑，这可能是压倒性的，并使它非常困难。

在本文中，我讨论了在编写可读性更好的代码时要考虑的 React 代码的各个方面。这些因素包括代码的长度、相关代码块的分组程度、是否使用了复杂的 JavaScript 结构、一次处理多少个流、变量和实体的命名以及相似或不同之处的突出程度。对于每个主题，我们都讨论了它们为什么重要，它们对代码可读性的影响，以及如何控制它们的影响。

使用这些信息，您应该对应该考虑哪些方面以及如何编写更具可读性的 React 代码有一个坚实的基础。这不仅会直接影响代码的可读性，还会影响代码的可审查性和长期可维护性。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)