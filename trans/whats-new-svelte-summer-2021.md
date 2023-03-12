# 《苗条身材:2021 年夏天》中的新内容

> 原文：<https://blog.logrocket.com/whats-new-svelte-summer-2021/>

## 介绍

Svelte 是一个令人兴奋的新 JavaScript 框架，它提供了一种构建 web 应用程序的新方法。

Svelte 借用了 React 和 Vue 等旧 JavaScript 框架的一些概念。有了这些方面的知识，我们可以很容易地过渡到苗条。

今年夏天，Svelte 的许多每月更新都集中在增强开发者体验上——`DX`，性能改进，更好的`TypeScript`支持，以及一些错误修复。让我们来看看其中的一些:

## 纤薄的新功能

### 1.`await`速记

根据[细长文档](https://svelte.dev/docs#await) , `await`模块允许你对一个承诺的三种可能状态进行分支:待定、完成或拒绝。

考虑下面的代码:

```
{#await promise}
        <!-- pending state -->
        <p>Loading...</p>
{:then value}
        <!-- fulfilled state -->
        <p>The value is {value}</p>
{:catch error}
        <!-- rejected state -->
        <p>An error occurred: {error.message}</p>
{/await}

```

在上面的代码中，我们有 pending、fulfilled 和 rejected。`{#await}`块匹配当前语法中的所有三个；但是，当我们不需要显示加载状态时，没有办法省略初始块。

`await`简写语法使我们能够省略 promise 的 resolve 值的加载状态块。
考虑下面的代码:

```
{#await promise then value}
        <p>Do somthing with value{value}</p>
{/await}

```

类似地，我们可以省略`then`块，如下所示:

```
{#await promise catch error}
        <p>Something went wrong: {error.message}</p>
{/await}

```

### 2.添加`trusted`事件修改器

Svelte 提供了不同的[事件修改器](https://svelte.dev/tutorial/event-modifiers)来改变事件的行为。我们通过使用[管道字符](https://en.wikipedia.org/wiki/Vertical_bar)将这些修饰符标记到事件的末尾。

考虑下面的代码:

```
<script>
    function handleSubmit() {
          console.log("submitting for data");
    }
</script>

<form on:submit|preventDefault={handleSubmit}>
  // do something
</form>

```

在上面的代码中，我们用`preventDefault`修改了`submit`事件。因此，我们防止页面在提交表单时被重新加载。

`isTrusted`修饰符使事件能够检查事件是否可信，并且只有当事件可信时才触发处理程序。也就是说，如果`event.isTrusted`是`true`。如果事件是由用户操作触发的，则该事件是可信的:

```
<button on:click|trusted={() => console.log('Trusted event'); }></button>

```

### 3.组件中对`export { ... } from`语法的支持

该功能旨在通过提供一种简洁的方式来增强开发人员的体验，以便在使用 bundler 文件处理程序(如 [@rollup/plugin-url](https://github.com/rollup/plugins/tree/master/packages/url) 和 [Webpack `file-loader`](https://v4.webpack.js.org/loaders/file-loader/) )时导入文件。

目前，我们要做的是:

```
import dogImgJpg from './dogImgJpg.jpg';
export let dogImg = dogImgJpg;

```

上面的模式有点冗长，因为我们必须显式地`import`和`export`文件。

像`export {…} from`这样的语法会抛出一个错误，因为它不包含必要的`import`。然而，Svelte 现在在 v3.41.0 中支持这个特性。所以我们可以简单地导出上面的`dogImgJpg.jpg`图像，方法是:

```
export { default as dogImg } from './dogImgJpg.jpg';

```

这隐式地导入了`dogImgJpg`。

### 4.导出道具时对象析构

类似地，该功能旨在通过使我们能够析构对象属性并在一行中导出它们来增强开发人员的体验:

```
const man = {surname: 'eagles', firstname: 'lawrence'};
export let {surname} = man;

```

### 5.添加一个`errorMode`编译器选项

这个特性给苗条的[编译器选项](https://svelte.dev/docs#svelte_compile) — `svelte.compile()`增加了一个`errorMode`。当`errorMode`设置为`warn`时，Svelte 会将错误记录为警告并继续编译，而不是抛出错误。

这也是 Svelte 在`[svelte/preproccess](https://github.com/sveltejs/svelte-preprocess/issues/318)`上工作的一部分，旨在改善 TypeScript 文件的预处理。

### 6.在阴影 DOM 中渲染苗条的组件

这个吸引人的特性使得在创建组件时通过指定一个`ShadowRoot`作为`target`在影子 DOM 中使用 Svelte 成为可能。

这使得利用阴影 DOM 的样式封装成为可能，这在 Svelte 中很难做到，因为 Svelte 总是将样式附加到`document.head`中。你可以[在这里](https://github.com/sveltejs/svelte/pull/5870)了解更多关于样式封装和影子 DOM 的知识。

### 7.引入带有 VS 代码扩展的新 TypeScript 插件

Svelte 增加了一个 TypeScript 插件和 VS 代码扩展。

VS 代码扩展——svelote for VS Code——使用 svelote 语言服务器为 VS 代码中的 svelote 文件提供智能感知。此外，这个扩展捆绑了一个更漂亮的插件，作为苗条文件的格式化程序。

我们可以通过调整我们的 VS 代码设置来用这个格式化程序格式化我们的苗条文件，如下所示:

```
...
"[svelte]": {
  "editor.defaultFormatter": "svelte.svelte-vscode"
},
...

```

此外，在安装和使用该扩展之前，需要注意以下几点:

*   如果你使用的是詹姆斯·比特莱斯的旧版本，卸载它。通过运行:code–uninstall-extension jamesbirtles . svelte-vs code，您可以很容易地通过`CLI`做到这一点

### 8.为 SvelteKit 推出新的`svelte/ssr`套装`ssr`

[svelite kit](https://blog.logrocket.com/exploring-sveltekit-the-newest-svelte-based-framework/)就是 svelite next . js 是什么反应。SvelteKit 是一个用于构建不同规模的 web 应用程序的框架。并且它提供了对路由、代码分割、服务器端渲染和离线支持等的支持。

有了这个特性，在为`ssr`构建时，`svelte`被解析为`svelte/ssr`。这为生命周期事件处理程序启用了[树摇动](https://developer.mozilla.org/en-US/docs/Glossary/Tree_shaking)，这是 JavaScript 中的一种技术，用于删除死代码并为生产准备我们的代码。

需要注意的是，在`SSR mode`中，所有简单的生命周期方法——`onMount`、`beforeUpate`、`afterUpdate`和`onDestroy`——都不做任何事情。因此，它们应该从最终束中移除。

然而，像 [Rollup](https://rollupjs.org/guide/en/) 和 [Webpack](https://webpack.js.org) 这样的构建工具无法检测到这一点。因此，它们最终出现在生成的包中。这为进一步优化我们的代码留下了空间。

新的`svelte/ssr`包是`svelte`的等效模块，但是生命周期方法被设计为`[noops](https://en.wikipedia.org/wiki/NOP_(code))`。`Noops`或`no-ops`可以设计在`ES6`中，如下图所示:

```
const noop = () => {};

```

通过这样做，这些生命周期方法现在可以由细长的 bundler 插件如`rollup-plugin-svelte`和`@sveltejs/vite-plugin-svelt`使用树摇动来移除。这就产生了一个更加优化和生产就绪的代码。

### 9.更新`svelte-check`

最新版本的`svelte-check`提供了到你的`tsconfig.json`或`jsconfig.json`的路径。因此，只能对该配置中引用的文件运行诊断。比如:`svelte-check --tsconfig "./tsconfig.json"`。

## 结论

Svelte 是一项了不起的技术，它为如何构建 web 应用程序提供了一个全新的视角。

即使你有使用框架如 Vue、Angular 或 React 进行 web 开发的经验，你仍然会被苗条的身材所吸引。

随着夏季的升温，Svelte 推出了几个有趣而诱人的新功能和增强功能，保持了凉爽。如果你还没有使用过[svelite](https://blog.logrocket.com/how-to-build-a-simple-svelte-js-app/)，现在是时候试试了。

你可以在 Svelte 的更新日志中了解更多关于 Svelte 的更新。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)