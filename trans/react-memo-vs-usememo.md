# React.memo()与 useMemo():主要区别和用例

> 原文：<https://blog.logrocket.com/react-memo-vs-usememo/>

在软件开发中，我们通常痴迷于性能提升，以及如何让我们的应用程序执行得更快，从而给用户带来更好的体验。

[记忆化](https://en.wikipedia.org/wiki/Memoization#:~:text=In%20computing%2C%20memoization%20or%20memoisation,the%20same%20inputs%20occur%20again.)是优化性能的方法之一。在本文中，我们将探索它在 React 中是如何工作的。

## 什么是记忆化？

简而言之，记忆化是一个允许我们缓存递归/昂贵的函数调用的值的过程，以便下次用相同的参数调用函数时，返回缓存的值，而不必重新计算函数。

这确保了我们的应用程序运行得更快，因为我们通过返回一个已经存储在内存中的值来避免重新执行函数所花费的时间。

## 为什么在 React 中使用记忆？

在 React 功能组件中，当组件中的道具改变时，默认情况下整个组件会重新渲染。换句话说，如果组件中的任何值更新，整个组件将重新呈现，包括其值/属性没有改变的函数/组件。

让我们看一个发生这种情况的简单例子。我们将构建一个基本的应用程序，告诉用户什么酒最适合他们选择的奶酪。

我们将从设置两个组件开始。第一个组件将允许用户选择奶酪。然后它会显示最适合这种奶酪的葡萄酒的名字。第二个组件将是第一个组件的子组件。在这个组件中，没有任何变化。我们将使用该组件来跟踪 React 重新渲染的次数。

先说我们的父组件:`<ParentComponent />`。

> **注意:**，本例中使用的`classNames`来自[顺风社](https://tailwindcss.com)。

```
// components/parent-component.js
import Counts from "./counts";
import Button from "./button";
import { useState, useEffect } from "react";
import constants from "../utils";
const { MOZARELLA, CHEDDAR, PARMESAN, CABERNET, CHARDONAY, MERLOT } = constants;

export default function ParentComponent() {
  const [cheeseType, setCheeseType] = useState("");
  const [wine, setWine] = useState("");
  const whichWineGoesBest = () => {
    switch (cheeseType) {
      case MOZARELLA:
        return setWine(CABERNET);
      case CHEDDAR:
        return setWine(CHARDONAY);
      case PARMESAN:
        return setWine(MERLOT);
      default:
        CHARDONAY;
    }
  };
  useEffect(() => {
    let mounted = true;
    if (mounted) {
      whichWineGoesBest();
    }
    return () => (mounted = false);
  }, [cheeseType]);

  return (
    <div className="flex flex-col justify-center items-center">
        <h3 className="text-center dark:text-gray-400 mt-10">
          Without React.memo() or useMemo()
        </h3>
      <h1 className="font-semibold text-2xl dark:text-white max-w-md text-center">
        Select a cheese and we will tell you which wine goes best!
      </h1>
      <div className="flex flex-col gap-4 mt-10">
        <Button text={MOZARELLA} onClick={() => setCheeseType(MOZARELLA)} />
        <Button text={CHEDDAR} onClick={() => setCheeseType(CHEDDAR)} />
        <Button text={PARMESAN} onClick={() => setCheeseType(PARMESAN)} />
      </div>
      {cheeseType && (
        <p className="mt-5 dark:text-green-400 font-semibold">
          For {cheeseType}, <span className="dark:text-yellow-500">{wine}</span>{" "}
          goes best.
        </p>
      )}
      <Counts />
    </div>
  );
}

```

第二个组件是一个`<Counts />`组件，它记录整个`<ParentComponent />`组件重新渲染的次数。

```
// components/counts.js
import { useRef } from "react";
export default function Counts() {
  const renderCount = useRef(0);
  return (
    <div className="mt-3">
      <p className="dark:text-white">
        Nothing has changed here but I've now rendered:{" "}
        <span className="dark:text-green-300 text-grey-900">
          {(renderCount.current ++)} time(s)
        </span>
      </p>
    </div>
  );
}

```

下面是上面的例子，当我们点击一个奶酪的名字时:

![Example of React Without Memoization](img/5dadb9fb77bc2ec6dfa5bef8f24286a2.png)

我们的`<ParentComponent />`中的`<Counts />`组件计算了多少次对`<ParentComponent />`的改变迫使`<Counts />`组件重新渲染。

目前，点击奶酪的名称将更新显示的奶酪名称。它还会更新要显示的葡萄酒的名称。不仅`<ParentComponent />`会重新呈现，而且`<Counts />`组件也会重新呈现，即使其中没有任何变化。

想象一下，有一个组件显示成千上万的数据，每次用户单击一个按钮，该组件或树中的每一部分数据都会在不需要的时候重新呈现。这就是`React.memo()`或`useMemo()`为我们提供性能优化所必需的地方。

现在，我们来探索一下`React.memo`，然后是`useMemo()`。之后，我们将比较它们之间的差异，并了解何时应该使用其中一个。

## 什么是`React.memo()`？

`React.memo()`与 [React v16.6](https://reactjs.org/blog/2018/10/23/react-v-16-6.html) 一同发布。虽然类组件已经允许你使用`[PureComponent](https://reactjs.org/docs/react-api.html#reactpurecomponent)`或`[shouldComponentUpdate](https://reactjs.org/docs/react-component.html#shouldcomponentupdate)`来控制重渲染，React 16.6 引入了对功能组件做同样事情的能力。

`React.memo()`是一个[高阶组件(HOC)](https://reactjs.org/docs/higher-order-components.html) ，这是一个组件的别称，它将一个组件作为道具，如果道具(或其中的值)没有改变，则返回一个阻止组件重新渲染的组件。

我们将采用上面相同的例子，但是在我们的`<Counts />`组件中使用`React.memo()`。我们需要做的就是用`React.memo()`包装我们的`<Counts />`组件，如下所示:

```
import { useRef } from "react";
function Counts() {
  const renderCount = useRef(0);
  return (
    <div className="mt-3">
      <p className="dark:text-white">
        Nothing has changed here but I've now rendered:{" "}
        <span className="dark:text-green-300 text-grey-900">
          {(renderCount.current ++)} time(s)
      </span>
      </p>
    </div>
  );
}
export default React.memo(Counts);

```

现在，当我们通过点击选择奶酪类型时，我们的`<Counts />`组件将不会重新呈现。

![Example Using React.memo()](img/3f2b6245cb1bfbba558cf530d11ce4ff.png)

## 什么是`useMemo()`？

而`React.memo()`是一个 HOC，`[useMemo()](https://blog.logrocket.com/react-reference-guide-hooks-api/#usememo)`是一个 React 钩子。使用`useMemo()`，如果函数的依赖关系没有改变，我们可以返回记忆值，避免重新渲染。

为了在我们的代码中使用`useMemo()`， [React 开发者给我们一些建议](https://blog.logrocket.com/rethinking-hooks-memoization/):

*   您可以依赖`useMemo()`作为性能优化，而不是语义保证
*   函数中引用的每个值也应该出现在依赖数组中

对于我们的下一个例子，我们将对我们的`<ParentComponent />`做一些修改。下面的代码只显示了我们之前创建的`<ParentComponent />`的新变化。

```
// components/parent-component.js
.
.
import { useState, useEffect, useRef, useMemo } from "react";
import UseMemoCounts from "./use-memo-counts";

export default function ParentComponent() {
  .
  .
  const [times, setTimes] = useState(0);
  const useMemoRef = useRef(0);

  const incrementUseMemoRef = () => useMemoRef.current++;

  // uncomment the next line to test that <UseMemoCounts /> will re-render every t ime the parent re-renders.
  // const memoizedValue = useMemoRef.current++;

// the next line ensures that <UseMemoCounts /> only renders when the times value changes
const memoizedValue = useMemo(() => incrementUseMemoRef(), [times]);

  .
  .

  return (
    <div className="flex flex-col justify-center items-center border-2 rounded-md mt-5 dark:border-yellow-200 max-w-lg m-auto pb-10 bg-gray-900">
      .
      .
        <div className="mt-4 text-center">
          <button
            className="bg-indigo-200 py-2 px-10 rounded-md"
            onClick={() => setTimes(times+1)}
          >
            Force render
          </button>

          <UseMemoCounts memoizedValue={memoizedValue} />
        </div>
    </div>
  );
}

```

首先，我们引入了最重要的`useMemo()`钩子。我们还引入了`useRef()`钩子来帮助我们跟踪组件中发生了多少次重渲染。接下来，我们声明一个`times`状态，稍后我们将更新它以触发/强制重新渲染。

之后，我们声明一个`memoizedValue`变量，存储由`useMemo()`钩子返回的值。`useMemo()`钩子调用我们的`incrementUseMemoRef`函数，每当依赖关系发生变化，即`times`值发生变化，我们的`useMemoRef.current`的值就增加 1。

然后我们创建一个按钮，当点击时更新`times`的值。点击这个按钮将导致我们的`useMemo()`钩子被触发，`memoizedValue`的值被更新，我们的`<UseMemoCounts />`组件被重新渲染。

对于这个例子，我们还将我们的`<Counts />`组件重命名为`<UseMemoCounts />`，现在它需要一个`memoizedValue`道具。

它看起来是这样的:

```
// components/use-memo-counts.js

function UseMemoCounts({memoizedValue}) {
  return (
    <div className="mt-3">
      <p className="dark:text-white max-w-md">
        I'll only re-render when you click <span className="font-bold text-indigo-400">Force render.</span> 
        </p>
      <p className="dark:text-white">I've now rendered: <span className="text-green-400">{memoizedValue} time(s)</span> </p>
    </div>
  );
}
export default UseMemoCounts;

```

现在，当我们点击任何一个**奶酪**按钮时，我们的`memoizedValue`不会更新。但是当我们点击**强制渲染**按钮时，我们看到我们的`memoizedValue`更新并且`<UseMemoCounts />`组件重新渲染。

![Usememo() example](img/0f72ad54122a32560b68ddc00ac8611f.png)

如果您注释掉我们当前的`memoizedValue`行，并取消注释掉它上面的行:

```
  const memoizedValue = useMemoRef.current++;

```

…你会看到每次`<ParentComponent />`渲染时,`<UseMemoCounts />`组件都会重新渲染。

## 总结:`React.memo()`和`useMemo()`的主要区别

从上面的例子中，我们可以看到`React.memo()`和`useMemo()`的主要区别:

*   是一个高阶组件，我们可以用它来包装那些我们不想重新渲染的组件，除非其中的道具发生了变化
*   是一个 React 钩子，我们可以用它来包装组件中的函数。我们可以利用这一点来确保只有当其中一个依赖项发生变化时，才重新计算该函数中的值

虽然内存化看起来似乎是一个随处都可以使用的小技巧，但是您应该只在绝对需要这些性能提升时才使用它。内存化会耗尽运行它的机器上的内存空间，因此可能会导致意想不到的后果。

这就结束了这篇文章！

你可以在 [GitHub](https://github.com/onedebos/react-memo-example) 上获得这个例子的完整代码，在这里可以看到一个真实的例子[。](https://react-memo-example.netlify.app/)

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