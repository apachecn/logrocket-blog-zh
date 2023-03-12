# Vue 组合 API 与 React Hooks 的比较

> 原文：<https://blog.logrocket.com/vue-composition-api-vs-react-hooks/>

[Vue 组合 API](https://v3.vuejs.org/guide/composition-api-introduction.html) 和 [React 钩子](https://reactjs.org/docs/hooks-intro.html)都是处理状态和组件中逻辑重用的函数集。

在本文中，我们将比较 Vue Composition API 和 React Hooks，并研究它们在各自的生态系统中扮演的角色。

我们将讨论以下内容:

## 什么是 Vue 合成 API？

Vue 组合 API 是一组附加、基于功能的 API，随着组件逻辑变得越来越大，它们有助于组件逻辑的灵活组合和维护。引入它是为了解决与使用 Options API 创建组件(尤其是大型组件)相关的一些缺点。

让我们检查一些与选项 API 相关的问题，并展示 Vue 组合 API 如何帮助解决这些问题。

### 低效的组件重用模式和逻辑

当您想要提供需要在其他组件中重用的组件数据时，Vue mixins 就派上了用场。Mixins 在为 Vue.js 组件分发可重用功能时非常灵活。它们可以包含任何组件选项，只要它已经在该组件中使用。

在处理小规模应用程序时，mixin 的理念非常完美。随着项目越来越大，您可能需要创建更多的 mixins 来处理其他类型的数据，这些数据也将在其他组件中重用。

Vue Composition API 有助于解决这个问题，它允许您在单独的 JavaScript 文件中定义您需要的任何函数，并在您需要导入它的任何地方导入它，以避免命名冲突。

这里有一个例子:

```
export default function userInfo(name, UserId) {
    var object = {
        name: name,
        age: age
    };
    return object;
}

```

然后，您可以将用户详细信息导出到任何想要使用它的地方:

```
<script>
   import userInfo from './userInfo.js'
   export default {
     name: 'app',
     setup() {
         const userInfoDetails = userInfo('Favour Woka', '5671');

         return {
             userInfoDetails
         }
     }
 }
 </script>

```

这里，我们创建了一个单独的文件，并编写了一个保存用户名和唯一 ID 的函数，然后我们将用户信息导入到我们想要使用的页面中。显然，这只是一个普通的 JavaScript。Vue Composition API 让您可以灵活地表达自己。

### 随着组件变大，可读性下降

为了解释为什么可读性在较大的应用程序中受到影响，让我们使用`setup()`方法构建一个简单的计算器。

从`<template>`标签开始:

```
<template>
  <div>
    <h1>Simple Calculator</h1>
    <form action="">
      <input type="number" v-model="firstAppend" @keyup="addNum()" maxlength="2">
      <span class="append"> + </span>
      <input type="number" v-model="secondAppend" @keyup="addNum()" maxlength="2">
      <span> = </span>
      <span> {{ answer }}</span>
    </form>
  </div>
</template>

```

我们创建了一个`input`标签并绑定`v-model`来获取输入的值。我们还使用了`@keyup`事件监听器来监听用户输入值时的输入。最后，我们使用`maxlength`只接受`2`或更少的条目。

```
<script>
import { ref } from 'vue'
export default {
  name: "Home",
  components: {
  },
  setup() {
    let firstAppend = ref(0);
    let secondAppend = ref(0);
    let answer = ref(0);
    console.log("A: ",firstAppend.value, secondAppend.value, answer.value)
    function addNum() {
        answer.value = parseInt(firstAppend.value?firstAppend.value:"0") + parseInt(secondAppend.value?secondAppend.value:"0");
    }
    return{
      firstAppend,
      secondAppend,
      answer,
      addNum
    }
  }
};
</script>

```

在`<script>`部分，我们从导入`ref()`开始，它接受一个参数并返回一个`value`属性。使用`setup()`，我们声明了一个变量，并在`ref()`方法中赋值，使其具有反应性。然后，我们返回该值，并将其绑定到我们想要绑定的元素。

如果我们看一下输入，现在我们可以看到它有一个默认值`0`。那是因为我们在`ref(0)`方法中传递了一个`0`参数。但是如果我们想改变这个值并得到一个结果呢？

我们可以使用组件选项，比如`computed`、`data`或者[、`mounted()`和](https://vuejs.org/v2/api/#mounted)生命周期挂钩来解决这个问题。这是可行的，我们最终会编写大量代码来解决一个简单的问题。我们可以使用`setup()`的方法来解决这个问题，而不需要添加任何其他组件选项。如果我们在另一个组件中重用相同的代码，我们可能会失去价值。

```
function addNum() {
        answer.value = parseInt(firstAppend.value?firstAppend.value:"0") + parseInt(secondAppend.value?secondAppend.value:"0");
    }

```

这里，我们在`setup()`方法中创建了作为参数`@kepup="addNum"`传递的函数`addnum()`，计算了值，并将函数作为值返回。现在，如果我们在任何输入中输入一个值，我们会在输入时立即打印出结果。

## 什么是 React 钩子？

[React Hooks](https://blog.logrocket.com/react-hooks-cheat-sheet-unlock-solutions-to-common-problems-af4caf699e70/) 是 React 16.8 新增的功能。钩子允许我们在不写类的情况下使用状态和其他 React 特性。

React 挂钩的引入是为了提供更直接的 API 概念，并解决与 React 旧版本相关的缺点，包括:

### 组件间有状态逻辑的硬重用

在 React 的旧版本中，组件之间没有可重用的有状态逻辑行为的附件。取而代之的是诸如[渲染道具](https://reactjs.org/docs/render-props.html)和[高阶组件](https://reactjs.org/docs/higher-order-components.html)的模式。当在组件之间共享数据时，您经常不得不重构组件，使得代码更难阅读。有了 React 钩子，我们可以通过使用 [`useState`](https://blog.logrocket.com/a-guide-to-usestate-in-react-ecb9952e406c/) 和 [`useEffect`](https://blog.logrocket.com/guide-to-react-useeffect-hook/) 钩子来解决这个问题。

举以下例子:

```
import './App.css';
import { useEffect, useState } from "react";
import Axios from "axios"
import Details from './details';
function App() {
  const [someData, setSomeDate] = useState("");
  useEffect(()=> {
    Axios.get("https://www.googleapis.com/books/v1/volumes?q=flowers+inauthor:keyes&key=yourkey").then(data => {
      setSomeDate(data.data.items[0].searchInfo.textSnippet);
    })
  });
  return (
    <div className="App">
      <header className="App-header">
        <Details data={someData}/>
      </header>
    </div>
  );
}
export default App;

```

我们开始从`react`进口`useState`和`useEffect`，从`axios`进口`Axios`。我们声明了一个名为`someData`的状态变量，并将其设置为接受一个字符串。我们仍然可以随意称呼这些论点。我们还传入了另一个名为`setSomeData`的变量来更新`someData`变量。

接下来，我们在`useEffect`钩子内部传递了一个 Axios 调用，它告诉 React 我们需要一个效果来更新我们的 DOM。

一旦 API 调用成功，我们将值设置为`setSomeDate`，这将更新我们创建的`someDate`变量。现在，我们可以轻松地将该值共享给其他组件。

在我们返回该值(现在是`someDate`)之后，我们将该值绑定到一个组件，并在组件内部呈现它。

```
import React from "react";
export default function Details({data}) {
  console.log(data)
  return (
    <div>
      <p>Bread hip hop hip</p>
      <h2>{data}</h2>
    </div>
  );
}

```

### 复杂的组件很难理解

当组件变得复杂时，React Hooks 使理解组件变得更加容易。使用钩子，你可以把一个组件分解成更小的功能。

就像前面的例子一样，我们在`app.js`组件上获取 Axios API，并使用另一个组件上的数据。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

在 React Hooks 之前，我们不能将组件分解成更小的组件，因为有状态逻辑无处不在。大多数情况下，我们求助于将 React 与一个单独的状态管理库相结合来破坏我们的一些组件。

### 混淆人和机器的类

最后，React 挂钩修复了与冲突类相关的问题，这有助于代码重用和改进代码组织。现在你可以不用类来使用更多的 React 特性。

## Vue 组合 API 与 React 挂钩

React Hooks 和 Vue Composition API 都是函数集。尽管他们处理事情的方式不同，但他们的目标是一样的，比如组件之间的逻辑重用，更大组件的可读性等等。

Vue Composition API 和 React Hooks 的[区别](https://dev.to/voluntadpear/comparing-react-hooks-with-vue-composition-api-4b32)在于 React Hooks 在渲染时可以多次运行；创建组件时，Vue 的`setup`函数只运行一次。

Vue 组合 API 提供了两种状态:

*   `Ref()`返回一个对象，其内部值可以通过其`value`属性访问
*   将一个对象作为其输入，并返回该函数的反应代理

React Hooks 还提供了两个钩子:

*   `useState`使您能够向功能组件添加反应状态:

```
const [someData, setSomeDate] = useState("");
```

*   `useEffect`允许您在功能组件中执行副作用

```
  useEffect(()=> {
    Axios.get("https://www.googleapis.com/books/v1/volumes?q=flowers+inauthor:keyes&key=AIzaSyAGdE2qCnOPJsg6du5r99cu1NS01jPsY9g").then(data => {
      console.log(data.data.items[0])
      setSomeDate(data.data.items[0].searchInfo.textSnippet);
    })
  });

 return (
        <div >
          {someData}
        </div>
  );
```

## 结论

在这个 Vue Composition API 教程中，我们解释了在 Vue.js 中添加新功能的原因，并回顾了这些功能旨在解决的问题。我们还通过示例演示了如何使用 Vue 组合 API 和 React 挂钩，并回顾了 Vue 组合 API 和 React 挂钩之间的主要区别。

要了解更多，请查看 [Vue 组合 API](https://v3.vuejs.org/guide/composition-api-introduction.html) 和 [React 钩子](https://reactjs.org/docs/hooks-intro.html)文档。

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。