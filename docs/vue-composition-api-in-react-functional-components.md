# 在 React 功能组件中使用 Vue 组合 API

> 原文：<https://blog.logrocket.com/vue-composition-api-in-react-functional-components/>

自 16.8 版首次发布以来，React Hooks 的受欢迎程度已经飙升。这是有充分理由的——将组件编写和组合成函数的能力给了你难以置信的能力和灵活性。加上出色的类型脚本支持，钩子如此吸引人也就不足为奇了。

然后是 [Vue Composition API](https://v3.vuejs.org/guide/composition-api-introduction.html) ，拥有漂亮的反应系统和改进的静态类型支持。现在确实是做前端工程师的好时机，但决策瘫痪可能是一场真正的斗争。我是放弃写 Vue 和 port 去反应钩子，还是反过来？

等等！如果你不必做出选择呢？如果可以在 React 组件中使用 Vue 组合 API 会怎么样？来了`[reactivue](https://github.com/antfu/reactivue)`让你能够做到这一点。

## 什么是`reactivue`？

`reactivue` [将自己](https://github.com/antfu/reactivue/blob/master/README.md)描述为一个框架中 React 的最佳元素和 Vue.js 的最佳元素。简而言之，您可以在 React 中编写 Vue 片段——兼顾两个世界。

> **注意** : `reactivue`目前仍处于试验阶段，未来可能会有一些突破性的变化。暂时不要在生产中使用，但是可以随意试用。

## 入门指南

本文假设您对 Vue 与组合 API 的反应以及功能组件与钩子的反应有相当的理解。无论如何，你不需要成为一名专家，但是对这些概念的基本熟悉是必要的。我们不会深入讨论它们如何工作的细节，而是它们如何在`reactivue`的帮助下协同工作。

事不宜迟，我们来安装`reactivue`。安装很简单——只需添加带有 Yarn 或 npm 的包，如下所示:

```
npm i reactivue
 // or
yarn add reactivue
```

## 使用

让我们构建一个简单的颜色选择器来看看这一过程。为了让事情变得简单明了，我们将不再做样式设计，只需要理解设置是如何工作的。有趣的部分是可视化`reactivue`如何集成 Vue 组合 API 和 React 函数，使它们无缝地协同工作。

我们的颜色选择器只是从 HTML 颜色输入中选择一种颜色，并根据选择的颜色更新 div 的背景颜色。作为一项任务，您可以扩展它来设置各种样式，或者添加复杂的条件逻辑来更好地理解概念。下面是代码，后面是深潜。

```
import * as React from "react";
import { useState, ChangeEvent } from "react";
import { defineComponent, ref, computed, onUnmounted, onMounted } from "reactivue";

interface Props {
  color: string;
  height: number;
  width: number;
}

const MyColor = defineComponent(
  // setup function in Vue
  ({ color, width, height }: Props) => {
    const background = ref(color);
    const boxWidth = ref(width);
    const boxHeight = ref(height);
    const boxStyle: any = computed(() => {
      return {
        width: `${boxWidth.value}px`,
        height: `${boxHeight.value}px`,
        backgroundColor: background.value
      };
    });

    onMounted(() => console.log("Hello World"));
    onUnmounted(() => console.log("Goodbye World"));

    return { background, boxWidth, boxStyle, boxHeight };
  },
  // functional component in React
  ({ background, boxWidth, boxStyle }) => {
    // you are now in react territory. You can use all hooks and methods.
    const [newStyle, setNewStyle] = useState(boxStyle);
    const onChangeColor = ({
      target: { value }
    }: ChangeEvent<HTMLInputElement>) => {
      setNewStyle({
        ...boxStyle,
        backgroundColor: value
      });
    };
    return (
      <div>
        <input
          type="color"
          onChange={(e) => onChangeColor(e)}
          style={{ marginBottom: "20px", width: "300px" }}
        />

        <div style={newStyle}></div>
      </div>
    );
  }
);

// use it as you normally would
render(<MyColor color="red" width={300} height={200} >, el)
```

下面是一个工作示例:

这很容易做到。我们已经从`reactivue`导入了 React 和设置所需的方法。

在这里，您可以创建一个所谓的**工厂**组件，它从`@vue/reactivity`导入反应性 API。从这个意义上说，下面的几行是等价的:

`defineComponent`函数公开了 Vue 的生命周期挂钩。我们创建一个`Props`对象，它将被传递给 React 组件。使用组合 API，我们能够用`ref`设置内部状态，并创建一个计算属性来更新 React 组件内部的样式。

```
// both line are equivalent.
import { ref, reactive, computed } from 'reactivue'
import { ref, reactive, computed } from '@vue/reactivity
```

关于组合 API 是如何工作的，我就不赘述了。我们需要理解的是，`background`、`boxWidth`、`boxHeight`和`boxStyle`都是反应性的，并被传递给 React 组件，在那里它们可以像在任何 React 组件中一样被使用和转换。

在我们的例子中，我们使用 React 的`useState`钩子设置一个`newStyle`状态。我们接受背景道具，并在 React 组件中用新状态更新它。

在这里，您已经可以看到我们如何从 Vue 传递一个状态，并在 React 中更改它。然后，通过 Vue 的反应性(从`number`到`px`)和在 React 中重新计算，这将设置一个新的样式。

这一开始可能会令人困惑，但它出奇的简单。试验代码，尝试跟踪不同样式的变化，或者添加一些其他的计算属性。

钩住

### 你也可以把它当钩子用。

`defineComponent`工厂实际上是`useSetup`的语法糖。下面的代码类似于上面使用组件工厂的代码。

主要区别在于，在 React 中，从 Vue 访问状态是作为一个普通的状态对象。您可以简单地从状态中析构您需要的东西，并在 React 功能组件中使用它。

```
import * as React from "react";
import { useState, ChangeEvent } from "react";
import { useSetup, ref, computed } from "reactivue";

function MyColor(Props: Props) {
  const state = useSetup(
    ({ color, width, height }: Props) => {
      // shortened for brevity; same as above example
      const boxStyle: any = computed(() => {
        return {
          width: `${boxWidth.value}px`,
          height: `${boxHeight.value}px`,
          backgroundColor: background.value
        };
      });
      return { background, boxWidth, boxStyle, boxHeight };
    },
    Props // pass React props to it
  );

  // state is a plain object just like React state
  const { boxStyle } = state;

  const [newStyle, setNewStyle] = useState(boxStyle);
  const onChangeColor = ()=> { //shortened for brevity}

  return (<div>  /**/ </div);
}
```

钩子工厂

### React 钩子的另一个重要概念是组合和重用组合逻辑，也称为定制钩子。你可以用`reactivue`里的钩子工厂来实现这个。

假设您想编写一个自定义钩子来计算给定数字数组的平均值，并按输入值递增数组中的数字。我们来写一个`useCalculation`钩子。

那么这段逻辑就可以复用如下；

```
import { createSetup, ref, computed, onUnmounted, onMounted } from "reactivue";

export interface Props {
  numbers: Array<number>;
}

// create a custom hook that can be reused
export const useCalculation = createSetup((props: Props) => {
  const numbers = ref(props.numbers);
  const average = computed(
    () => numbers.value.reduce((a, b) => a + b) / numbers.value.length
  );
  const increment = (by: number) => {
    return numbers.value.map((num) => {
      return num + by;
    });
  };

  onMounted(() => console.log("Hello World"));
  onUnmounted(() => console.log("Goodbye World"));

  return { numbers, average, increment };
});

export default useCalculation;
```

下面是一个工作演示:

```
import * as React from "react";
import { ChangeEvent, useState } from "react";

import { Props, useCalculation } from "./useCalculation";

export const App = (props: Props) => {
  const [myNumbers, setMyNumbers] = useState(props.numbers);
  const { increment, average } = useCalculation({
    numbers: myNumbers
  });

  const onChange = ({
    target: { value }
  }: ChangeEvent<HTMLInputElement>) => {
    setMyNumbers(increment(parseInt(value,10)));
  };
  return (
    <div>
      <input type="number" onChange={(e) => onChange(e)} />
      <ul>
        {" "}
        {myNumbers.map((num, index) => {
          return <li key={index}>{num}</li>;
        })}
      </ul>
      <br />
      <div>The Average is of numbers = {average} </div>
    </div>
  );
};
```

额外的 API

生活过程

## 实现 Vue 的基本生命周期挂钩，然后将它们与 React 的生命周期挂钩绑定。对于一些没有 React 等价物的生命周期，它们将在应该被调用的时候被调用(例如，`onMounted`将在`onCreated`之后被调用)。

### 更多来自 LogRocket 的精彩文章:

它们可以像你在 Vue 中一样被使用。

* * *

### `defineComponent()`–接受将返回反应功能组件的设置函数和渲染函数

* * *

`useSetup()`–解析组合 API 的设置

*   将你的逻辑打包成可重用的定制钩子的工厂
*   结论
*   在这里，我们探索了将两个强大工具中的概念混合并匹配成一个工具的有趣可能性。尽管这在很大程度上仍然是实验性的，但我们可以看到我们将享受的巨大好处 Vue 及其反应系统的简单性以及 React 的最佳部分。

## 我们看了如何在 Vue 中设置反应数据和计算属性，以及如何通过道具或状态对象在 React 中使用它们。我们通过易于理解的例子和小应用程序来了解这种可能性。您可以通过派生示例并构建出色的项目来更进一步。这将是最好的 ***反应*** 离子从我的 ***Vue*** 😃。

像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

## .

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。

The LogRocket Vuex plugin logs Vuex mutations to the LogRocket console, giving you context around what led to an error, and what state the application was in when an issue occurred.

Modernize how you debug your Vue apps - [Start monitoring for free](https://lp.logrocket.com/blg/vue-signup).