# ArrowJS 如何与 React 和 Vue.js 进行比较

> 原文：<https://blog.logrocket.com/how-arrowjs-compares-react-vue-js/>

前端生态系统充斥着过多的框架，似乎每天都有新的框架发布。虽然这些框架中的许多可能没有更流行的框架使用广泛，但其中一些提供了独特的方法，可能会引起您的兴趣。

在本文中，我们将介绍用于构建反应式用户界面的 JavaScript 工具 [ArrowJS](https://www.arrow-js.com) ，并将其方法与 React 和 Vue.js 等流行的 UI 框架进行比较。我们将探索 ArrowJS 与这些传统框架的不同之处以及它的与众不同之处。

*向前跳转:*

## 先决条件

要理解本文中共享的概念和示例，您应该具备以下条件:

*   系统上安装的 Node.js
*   JavaScript、Vue 和 React 的基础知识

## 什么是 ArrowJS？

ArrowJS 是一个使用纯 JavaScript 构建反应式用户界面的实验性工具。它使用现代 JavaScript 特性，如模板文字、模块和代理，来实现其模板结构、可观察数据和声明性/反应性 DOM 呈现功能。

ArrowJS 的创造者认为，没有必要用复杂的框架来创建令人印象深刻和高性能的网络用户界面，因为 JavaScript 已经发展得足够强大，可以在本地处理这些任务。

因此，ArrowJS 没有依赖项，没有虚拟 DOM，没有构建工具，也没有特殊的模板语言。它也非常轻量级，重量不到 3kB (min+Gzip)。这使得它比 React 和 Vue 等具有类似功能的框架更快。

与许多其他 JavaScript 框架不同，ArrowJS 不使用构建工具或模板语言。这意味着在构建期间不需要编译或转换，从而获得更好的性能。

相比之下，React 使用 JSX 作为其模板语言，必须编译并转换为本机 JavaScript 渲染函数，才能在浏览器中运行。

## 为什么要用 ArrowJS？

考虑使用 ArrowJS 有几个原因，包括以下主要好处:

*   使用普通 JavaScript 构建用户界面:使用 ArrowJS，您无需学习新的语言或框架，因为您可以使用现有的 JavaScript 知识来创建强大的用户界面
*   **反应式编程模型**:使用 ArrowJS，每当底层数据发生变化时，您的 UI 都会自动更新，这使得构建能够实时对变化做出反应的动态响应用户界面变得更加容易
*   轻量级和易于使用:ArrowJS 有一个简单的 API 和一个小的内存，所以你可以把它添加到你的项目中，而不需要增加额外的代码和复杂性

在本文的稍后部分，我们将深入探讨这些好处。

## 箭头入门

设置 ArrowJS 应用程序有三种可能的方法:使用包管理器、本地安装或通过 CDN。让我们来看看每个选项。

### 从软件包管理器安装

如前所述，默认情况下，ArrowJS 不包含构建工具或捆绑器。但是，如果您想使用 npm 或 Yarn 之类的包管理器来引导您的项目，并利用热模块重载之类的特性，您可以使用 Vite 或 Snowpack 之类的[工具来捆绑您的项目](https://blog.logrocket.com/vite-vs-snowpack-a-comparison-of-frontend-build-tools/)。

使用以下命令通过 npm 或 Yarn 安装 ArrowJS:

```
//npm 
npm install @arrow-js/core

//Yarn
yarn add @arrow-js/core

```

### 从本地文件系统安装

要在本地安装 ArrowJS，您需要从 GitHub 下载 ArrowJS 包，并将其直接添加到您的项目中。然后，在脚本模块中引用它，如下所示:

```
<script type="module">
 import { reactive, html } from '/js/arrow.js';
 //your app’s code goes here
</script>

```

### 从 CDN 安装

通过 CDN 安装 ArrowJS 非常简单，只需在脚本中添加以下 import 语句:

```
<script type="module">
 import { reactive, html } from npm install @arrow-js/core'https://cdn.skypack.dev/@arrow-js/core';
 // Start your app here!
</script>

```

## 了解箭头构建模块

ArrowJS 基于两个组合表达式:静态和反应式。然而，该工具的创造者认为，反应应该是一个选项，而不是一个要求。因此，ArrowJS 在默认情况下是静态的，在选择时是反应性的，并且只提供三个函数来处理模板和反应性功能:

*   `reactive` ( `r`)
*   `watch` ( `w`)
*   `html` ( `t`)

### ArrowJS `reactive`功能

`reactive`函数，简称为`r`,是一个 ArrowJS 函数，它将通用数据对象转换为观察数据对象。该函数监视对象的变化，并根据需要自动更新任何相关的模板或依赖项。

使用`reactive`函数很简单；只需将您想要监控的对象作为参数传递给`reactive`函数调用`reactive(object)`，如下所示:

```
import { reactive } from '@arrow-js/core'

const users = reactive({
 name: “John”,
 age: 25
})

```

在本例中，假设我们有一个依赖于反应数据中任何对象属性的变量。当对象属性改变时，应变量的值将相应地更新:

```
import { reactive } from '@arrow-js/core'

const users = reactive({
 name: "John",
 age: 25
})

users.name = "Mike"

const data = user.name;

console.log(data);

//logs 'Mike' to the console

```

然而，ArrowJS 提供了`$on`和`$off`方法，允许我们观察反应属性的变化。

`$on`方法接受两个参数:属性名和回调函数。此方法观察指定属性的更改，并在发生更改时运行回调函数:

```
import { reactive } from "@arrow-js/core";

const users = reactive({
 name: "John",
 age: 25,
});

const nameFunc = (value) => {
 console.log(`name changed to ${value}`);
 const data = value;
 }

users.$on("name", nameFunc);

setTimeout(() => {
 users.name = "Mike";
}, 2000);

//"name changed to Mike" will get logged to the console two seconds after initialization

```

在上面的例子中，`$o` n 回调函数将在 name 属性改变时运行。

另一方面，`$off`方法用于从`$on`方法中移除附加的回调。

例如，如果我们想要阻止`reactive`函数观察`name`属性，我们将使用下面的代码:

```
const users = reactive({
 name: "John",
 age: 25,
});

const nameFunc = (value) => {
 console.log(`name changed to ${value}`);
 const data = value;
};

users.$on("name", nameFunc);

setTimeout(() => {
 users.name = "Mike";
}, 2000);

users.$off("name", nameFunc);

//We stopped observing 'name' with $off
// so changing users.name will not log anything

```

### ArrowJS `watch`功能

或者，我们可以使用 ArrowJS `watch`函数来跟踪和取消跟踪反应属性。`watch`函数，简称`w`，是一个内置的 ArrowJS 函数，就像`reactive`函数一样。但是，与`r`函数不同的是，`watch`接受一个函数并跟踪该函数的任何反应依赖性:

```
import { reactive, watch } from "@arrow-js/core";
 price: 25,
 tax: 10,
});

function total() {
 console.log(`Total: ${data.price + data.tax}`);
}

setTimeout(() => {
 data.price = 35;
}, 2000);

watch(total);

//"Total: 45" will be logged to the console two seconds after initialization

```

`watch`功能将监控`price`和`tax`无功属性的任何值变化。它还为这些属性激活`$on`观察器，并自动检测它们何时不再被函数使用，此时它将调用`$off`观察器并停止跟踪属性。

### ArrowJS `html`功能

ArrowJS `html`函数，简称为`t`，用于创建箭头模板并安装到 DOM 中。它使用带标签的模板文本以声明方式呈现内容。

要创建一个 ArrowJS 模板，您可以用关键字`html`或其简写`t`作为记号标记的前缀，并添加要呈现的元素。

例如，关键字或其简写后跟一个开始记号(`html'`或`t'`)表示模板的开始，而结束记号表示模板的结束。

然后，我们可以在刻度内添加要呈现的元素，就像这样:`html`elements to be rendered``或`t`elements to be rendered``

这里有一个例子:

```
import { html } from '@arrow-js/core'

const appElement = document.getElementById('app');

const template = html`<p>Hello World</p>`

template(appElement)

```

在上面的代码中，我们引用了 DOM 中的一个元素，并将我们的模板安装到它上面。

## 特征比较

让我们仔细看看 ArrowJS 的一些特性，看看它们与 React 和 Vue 的特性相比如何。

### 模块结构和语法

与 Vue、React 和大多数其他 JavaScript 框架不同，ArrowJS 不使用基于组件的方法。相反，它依赖于函数。但是由于 JavaScript 组件本质上是幕后的函数，所以这两种方法实际上非常相似。

这些框架的不同之处在于它们封装的模板种类和它们的整体结构。例如，Vue 使用单文件组件结构，其中组件的模板、逻辑和样式都包含在单个`.vue`文件中:

```
<template>
 <!-- html markup -->
</template>

<script>
 // JavaScript code
</script>

<style>
 /* CSS styles */
</style>

```

相比之下，ArrowJS 使用标准的 JavaScript 脚本模块和本机代码和函数:

```
import { r, t } from "@arrow-js/core";

const appElement = document.getElementById("app");

// JavaScript code
const user = r({});

const template = t`
 <div class="container">
 <!-- html markup -->
 </div>
`;

template(appElement);

```

乍一看，ArrowJS 似乎与 Vue 没有太大的不同，因为两个框架都将它们的模板和逻辑封装在一个模块中。然而，ArrowJS 使用[模板文字](https://blog.logrocket.com/16-useful-typescript-javascript-shorthands-know/#template-literals)(一个本地 JavaScript 特性)来插入表达式并直接将元素呈现到 DOM 中。同时，Vue 依赖于虚拟 DOM，它是实际 DOM 的抽象。

### 事件

ArrowJS 和其他框架的另一个显著区别是将事件侦听器绑定到 DOM 元素的语法。该工具使用后面跟有事件名称的`@`符号来表示事件监听器:

```
const clickHandler = () =>{
 console.log("clicked");
}

const template = t`
 <button @click="${clickHandler}">Click</button>
`;

```

这将自动转换成一个等效的表达式:

```
document.getElementById("btn").addEventListener("click", () => {
 console.log("clicked");
});

```

相比之下，React 和 Vue 在将事件侦听器绑定到元素时使用 camelCase 命名约定和事件指令:

```
//React
const MyButton = () => {
 handleClick = () => {
 // do something when the button is clicked
 };

 return <button onClick={handleClick}>Click me</button>;
};
Vue
<template>
 <button v-on:click="handleClick">
 Click me
 </button>
</template>

<script>
 export default {
 methods: {
 handleClick() {
 // do something when the button is clicked
 }
 }
 }
</script>

```

### 反应

反应性是一种编程范式，它允许我们声明性地调整以适应变化。这仅仅是一个概念，而不是编程语言的默认特性。然而，它可以使用不同的工具和技术来实现，这取决于给定系统的特定需求和约束。

在 JavaScript 中，可以使用 React 和 Vue 等框架和库来实现反应性，这些框架和库是为构建反应性用户界面而设计的。这些框架使用虚拟 DOM 来处理反应，当发生变化时，更新它以反映最新的数据。然后，该框架将虚拟 DOM 与真实 DOM 进行比较，以确定 UI 的哪些部分需要更新。

鉴于 ArrowJS 使用原生 JavaScript，您可能会好奇它是如何处理反应的。解决方案是一个自定义依赖类。

JavaScript 中的反应性可以使用一个依赖类来实现，该类接受两个属性:一个值 getter 和一个回调函数。值 getter 是一个依赖于多个变量或依赖项来获取其值的函数。

每当依赖项的值改变时，JavaScript 将自动调用回调函数，并将当前值与先前值进行比较。

这就是 ArrowJS 如何使用`reactive`和`watch`功能来处理引擎盖下的反应。而且，因为 ArrowJS 不依赖于像虚拟 DOM 这样的机制，所以它的效果是即时的。

另一件要考虑的事情是，与其他框架相比，ArrowJS 中反应式实现的简洁性。例如，下面的代码演示了如何在显示用户的`name`和`age`的 React 组件中实现反应:

```
import { useState, useEffect } from 'react';

function ReactiveComponent() {
 // Create a state variable called "user" to hold the reactive data
 const [user, setUser] = useState({ name: 'John Doe', age: 32 });

 // Use the useEffect hook to specify a function that will be executed
 // whenever the user's data changes
 useEffect(() => {
 // Update the UI to reflect the latest user data
 // (e.g., update the name and age displayed on the screen)
 });

 // Return the JSX for the component, which includes the current
 // user's name and age
 return (
 <div>
 <p>Name: {user.name}</p>
 <p>Age: {user.age}</p>
 </div>
 );
}

```

下面是 ArrowJS 中相同的实现:

```
import { r, html } from "@arrow-js/core";

const appElement = document.getElementById("app");

// Create a state variable called "user" to hold the reactive data
const user = r({ name: "John Doe", age: 32 });

 // Dom template which includes the current
 // user's name and age
const template = html`
 <div>
 <p>Name: ${user.name}</p>
 <p>Age: ${user.age}</p>
 </div>
`;

// Map template to Dom
template(appElement);

```

### 声明性渲染

这些框架共有的一个方面是它们如何在模板中呈现声明性数据属性。React 使用一个由单花括号表示的占位符将反应内容和变量插入到字符串中，如下所示:

```
<div>
 <p>Name: {user.name}</p>
 <p>Age: {user.age}</p>
</div>

```

相比之下，Vue 使用双花括号，通常称为 mustache 语法，将动态值插入到字符串中。例如，上面的模板将在 Vue 中呈现如下:

```
<div>
 <p>Name: {{user.name}}</p>
 <p>Age: {{user.age}}</p>
</div>

```

因为 ArrowJS 使用本地 JavaScript 来呈现元素，所以它可以访问模板文字提供的特殊占位符语法。这些占位符称为模板标签，使用`${}`语法表示，用于在运行时将动态值插入模板。这使得 ArrowJS 可以创建包含动态数据的模板，只要数据发生变化，这些模板就会更新。

模板标签用于将 JavaScript 表达式的结果插入模板。要创建模板标签，只需在`${}`占位符中放置一个表达式。计算字符串时，会计算表达式，其值会插入到模板中的占位符位置:

```
const template = html`
 <div>
 <p>Name: ${user.name}</p>
 <p>Age: ${user.age}</p>
 </div>
`;

```

如您所见，模板标签的语法类似于 React 和 Vue 中使用的语法，除了符号`$`。

模板标签提供了一种简单明了的方法将动态值插入模板。这些标记可以与其他语言特性(如箭头函数和析构)结合使用，以创建清晰易读的代码。

## 结论

在本文中，我们介绍了 ArrowJS，讨论了它的许多优点，并将它的特性与 React 和 Vue 进行了比较。ArrowJS 仍处于试验阶段，但事实证明它是一个强大的工具，可以用原生 JavaScript 构建快速、反应式的用户界面。

与传统方法相比，用 ArrowJS 构建 ui 有几个优点，包括改进的性能和与现有代码的轻松集成。原生 JavaScript 的使用使得 ArrowJS 成为任何开发人员工具箱中有价值的补充。

## 您是否添加了新的 JS 库来提高性能或构建新特性？如果他们反其道而行之呢？

毫无疑问，前端变得越来越复杂。当您向应用程序添加新的 JavaScript 库和其他依赖项时，您将需要更多的可见性，以确保您的用户不会遇到未知的问题。

LogRocket 是一个前端应用程序监控解决方案，可以让您回放 JavaScript 错误，就像它们发生在您自己的浏览器中一样，这样您就可以更有效地对错误做出反应。

[![LogRocket Dashboard Free Trial Banner](img/e8a0ab42befa3b3b1ae08c1439527dc6.png)](https://lp.logrocket.com/blg/javascript-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/javascript-signup)

[LogRocket](https://lp.logrocket.com/blg/javascript-signup) 可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

自信地构建— [开始免费监控](https://lp.logrocket.com/blg/javascript-signup)。