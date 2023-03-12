# 使用有丝分裂和 Builder 创建可重用组件

> 原文：<https://blog.logrocket.com/creating-reusable-components-mitosis-builder-io/>

开发团队面临的一个共同挑战是使用相同的语言；当一个子团队正在使用 Vue 时，另一个子团队可能正在使用 React，这会导致多余的工作，并迫使您创建两次共享组件。

在本教程中，我们将探索[有丝分裂](https://github.com/BuilderIO/mitosis)，这是一个工具，除了像 Angular、React 和 Vue 这样的框架和库之外，它还可以将代码编译成标准 JavaScript，允许您创建可重用的组件。

我们将回顾一些背景信息，以澄清您何时应该使用有丝分裂，然后在一个新项目中安装有丝分裂以查看它的运行情况。我们开始吧！

## 有丝分裂与 web 组件

虽然一些开发人员最初转向 web 组件来创建可重用组件，但他们遇到了诸如 web 组件的浏览器兼容性及其底层方法等问题，这使得创建可重用组件成为一个密集的过程。

类似地，像 React 这样的库的工作流使得整合 web 组件变得困难，导致了像 Google 的 LitElement、Ionic 和 Stencil.js 这样的解决方案的产生。

与此同时，苗条身材和结实身材也出现了类似的趋势。Svelte 和 SolidJS 旨在构建应用程序，通过将源代码编译成标准 JavaScript，创建比 web 组件更小、更快的包，获得了巨大的性能收益。

有丝分裂建立在 Svelte 和 SolidJS 的功能上，采用相同的编译速度，并允许您从相同的代码库将组件重新编译成不同的框架和库。编译后的组件像框架中的任何其他组件一样工作。

与 SolidJS 类似，Mitosis 使用 JSX 的一个版本将组件编译成 JSON。插件然后将组件编译到不同的目标，允许您在两个方向上创建工具:

*   可以转换成有丝分裂 JSON 的代码
*   将 JSON 编译或序列化到目标框架的插件

由于这些原因，有丝分裂支持无代码工具。例如， [Builder.io](https://www.builder.io/) 允许你使用可视化工具创建你的网站，然后编译成你选择的框架。Builder.io 充当 CMS，但由有丝分裂驱动。

有丝分裂使用 WYSIWYG 编辑和 SPA 框架进行编译。现在我们已经了解了有丝分裂是如何工作的，让我们用有丝分裂创建一个组件。

## 有丝分裂入门

首先，我们将安装有丝分裂 CLI:

```
npm install -g @builder.io/mitosis-cli

```

在您的计算机上创建一个空文件夹。在您的终端中，打开文件夹并创建一个新的 npm 项目:

```
npm init -y

```

接下来，我们将安装有丝分裂:

```
npm install @builder.io/mitosis

```

创建一个名为`component.lite.jsx`的文件。`lite.jsx`是有丝分裂项目的扩展。接下来，我们将安装 Builder.io VS 代码扩展，它为`lite.jsx`文件提供语法高亮显示。

在`component.lite.jsx`文件中，添加以下代码:

```
import { useState, Show, For } from "@builder.io/mitosis";
export default function Component(props){
const state = useState({count: 0})

```

在上面的代码中，我们使用`useState`钩子声明状态。现在，当状态对象中的任何属性发生变化时，UI 都会呈现。

接下来，将下面的代码块添加到`component.lite.jsx`，它将像 React 或 SolidJS 一样返回 JSX:

```
    return (<div>
        {/* DISPLAY SOME JSX CONDITIONALLY */}
        <Show when={state.count > 10}>
            <h1>You Win!!!</h1>    
        </Show>

        {/* DISPLAY THE COUNT */}
        <h1>{state.count}</h1>

        {/* BUTTON TO ADD TO THE COUNT */}
        <button onClick={(event) => {state.count += 1}}>Click to Add One</button>
    </div>)
}

```

在上面的代码中，`show`组件允许我们有条件地呈现 UI。因为状态是用简单的重新分配来更新的，所以不需要添加我们在 React 中使用的`setState`函数。最后，请注意，我们所有的状态都可以捆绑到一个对象中。

现在我们的组件已经构建好了，让我们来看一些编译到不同框架的组件的例子！

## 有丝分裂中的编译

### 反应

让我们使用有丝分裂将组件编译成 React 组件:

```
mitosis compile --to=react component.lite.jsx > component.jsx
```

`--to=`标志让我们选择将有丝分裂组件编译到的框架。当编译为 React 时，我们将得到以下输出:

```
import { useContext } from "react";
import { useLocalObservable } from "mobx-react-lite";
export default function Component(props) {
  const state = useLocalObservable(() => ({ count: 0 }));
  return (
    <div>
      {state.count > 10 && (
        <>
          <h1>You Win!!!</h1>
        </>
      )}
      <h1>{state.count}</h1>
      <button
        onClick={(event) => {
          state.count += 1;
        }}
      >
        Click to Add One
      </button>
    </div>
  );
}

```

### 某视频剪辑软件

```
--to=vue component.lite.jsx > component.vue

```

当编译成 Vue 时，我们的组件看起来像下面的代码:

```
<template>
  <div>
    {{/* DISPLAY SOME JSX CONDITIONALLY */}}
    <template v-if="count > 10">
      <h1>You Win!!!</h1>
    </template>

    {{/* DISPLAY THE COUNT */}}
    <h1>{{ count }}</h1>

    {{/* BUTTON TO ADD TO THE COUNT */}}
    <button @click="count += 1">Click to Add One</button>
  </div>
</template>
<script>
export default {
  name: "Component",

  data: () => ({ count: 0 }),
};
</script>

```

### 苗条的

```
--to=svelte component.lite.jsx > component.svelte

```

当我们将有丝分裂组件编译成 Svelte 时，我们将得到以下输出:

```
<script>

     let  count= 0

</script>

<div >

  {#if count > 10 }       
    <h1 >You Win!!!</h1>
  {/if}

  <h1 >{count}</h1>

  <button  on:click="{event => 
    count += 1;
  }" >Click to Add One</button>

</div>

```

### 有角的

```
--to=angular component.lite.jsx > component.tsx

```

当我们将有丝分裂组件编译成 Angular 时，它看起来像下面的代码:

```
import { Component } from "@angular/core";

@Component({
  selector: "component",
  template: 
    <div>
      <ng-container *ngIf="count > 10">
        <h1>You Win!!!</h1>
      </ng-container>

      <h1>{{count}}</h1>

      <button
        (click)="
      count += 1;
    "
      >
        Click to Add One
      </button>
    </div>
  ,
})
export default class Component {
  count = 0;
}

```

### web 组件

```
--to=customElement component.lite.jsx > component.js

```

当编译成 web 组件时，我们将得到以下输出:

```
/**
 * Usage:
 *
 *  <component></component>
 *
 */
class Component extends HTMLElement {
  constructor() {
    super();

    const self = this;
    this.state = { count: 0 };

    // Event handler for 'click' event on button-1
    this.onButton1Click = (event) => {
      this.state.count += 1;
      this.update();
    };
  }

  connectedCallback() {
    this.innerHTML = `
      <div>
        <span data-name="show">
          <h1>You Win!!!</h1>
        </span>

        <h1>
         <span data-name="div-1"><!-- state.count --></span>
        </h1>

        <button data-name="button-1">Click to Add One</button>
      </div>
     <style></style>`;
    this.update();
  }

  update() {
    this.querySelectorAll("[data-name='show']").forEach((el) => {
      el.style.display = this.state.count > 10 ? "inline" : "none";
    });

    this.querySelectorAll("[data-name='div-1']").forEach((el) => {
      el.innerText = this.state.count;
    });

    this.querySelectorAll("[data-name='button-1']").forEach((el) => {
      el.removeEventListener("click", this.onButton1Click);
      el.addEventListener("click", this.onButton1Click);
    });
  }
}

customElements.define("component", Component);

```

正如您所看到的，安装有丝分裂，创建一个组件，然后将其编译成您选择的语言、库或框架是非常简单的。我们在本教程中讨论了几个例子，但是仅仅触及了皮毛；其他编译目标包括 Swift、Liquid.js、SolidJS、React Native 等。

## 结论

当不可能和你的团队中的其他人使用相同的语言时，有丝分裂是一个有用的工具，通过减少多余的工作来节省时间。

有丝分裂允许你为一个单独的组件编写一个代码库，然后将它编译成许多目标中的一个。它促进了低代码和无代码解决方案，以创建快速、反应式的应用程序。

我们探索了 Builder.io 插件，但是另一个流行的插件是 [figma-html](https://github.com/builderio/figma-html) 插件，它允许你把你的 figma 设计变成任何框架的代码。

当您构建自己的可重用组件时，您可以使用 Builder.io 的 [JSX-lite fiddle](https://jsx-lite.builder.io/?outputTab=vue) 来查看可视化结果。然而，在撰写本文时，该功能仍处于早期预览阶段。我希望你喜欢这个教程！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)