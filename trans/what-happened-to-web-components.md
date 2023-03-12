# web 组件发生了什么变化？- LogRocket 博客

> 原文：<https://blog.logrocket.com/what-happened-to-web-components/>

Web 组件是一组 web 技术，允许我们创建可重用的 HTML 元素。它们使得开发使用 HTML 语法的框架无关的定制组件成为可能。浏览器可以在没有 React 或 Vue.js 等第三方库的情况下原生解释它们。

如今， [web 组件](https://developer.mozilla.org/en-US/docs/Web/Web_Components)是一个有争议的话题。他们曾经被期望革新前端开发，但是他们仍然在努力实现全行业的采用。一些开发人员说 web 组件已经死亡，而其他人认为它们是 web 开发的未来。

让我们拉近距离，探索什么是 web 组件，为什么开发人员不愿意使用它们，以及未来会怎样。

## 在哪里可以找到 web 组件？

关于 web 组件最常见的问题之一是在哪里或者如何找到它们。目前，web 组件还没有一个明确的归属。并不是说他们完全无家可归。相反，他们是 W3C 扩展生态系统中的沙发冲浪者。

最初，web 组件有一个独立的 W3C 规范，叫做[自定义元素](https://www.w3.org/TR/custom-elements/)。自定义元素在 2018 年被弃用，其部分内容分别移入了 HTML 和 DOM 标准:

*   自定义元素(在 [HTML 生活标准](https://html.spec.whatwg.org/multipage/custom-elements.html#custom-elements)规范中定义)
*   HTML 模板(由`[<template>](https://html.spec.whatwg.org/multipage/scripting.html#the-template-element)`和`[<slot>](https://html.spec.whatwg.org/multipage/scripting.html#the-slot-element)`元素表示，也在 HTML 生活标准规范中定义)
*   阴影 DOM(在 DOM 生活标准中定义为[阴影树](https://dom.spec.whatwg.org/#shadow-trees))

web 组件技术曾经有第四个元素， [HTML 导入](https://developer.mozilla.org/en-US/docs/Web/Web_Components/HTML_Imports)s。HTML 导入旨在将 HTML 模块打包成`.html`文件，这样它们就可以在没有 JavaScript 的情况下轻松导入。浏览器供应商放弃了 HTML 导入，所以即使他们仍然在使用 polyfill，也不建议在新的应用程序中使用它们。

就像没有明确的规范之家一样，也没有可以找到和下载开源 web 组件的中心位置或库。即使有一些有前途的项目(我们将在下面看到其中的一些)，你也必须自己去寻找。

## web 组件是如何工作的？

在深入研究 web 组件的问题之前，让我们快速回顾一下它们是如何工作的。

下面是设置 web 组件的基本过程:

1.  创建一个扩展`[HTMLElement](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement)`类的`MyElement`类(使用 ES6 语法，因为以前的 JavaScript 版本没有类语法)
2.  定义将在 HTML 文档中代表`MyElement` JavaScript 类的`<my-element></my-element>`定制 HTML 标签。这里，您可以使用`[CustomElementRegistry.define()](https://developer.mozilla.org/en-US/docs/Web/API/CustomElementRegistry/define)`方法或 DOM API 的`[document.createElement()](https://developer.mozilla.org/en-US/docs/Web/API/Document/createElement)`方法
3.  使用`[Element.attachShadow()](https://developer.mozilla.org/en-US/docs/Web/API/Element/attachShadow)`方法将阴影 DOM 树附加到自定义元素的构造函数。这一步是可选的，只有当 web 组件有子元素时才适用——参见一个不带(`[<flag-icon></flag-icon>](https://html.spec.whatwg.org/multipage/custom-elements.html#custom-elements-autonomous-example)`)和带(`[<popup-info></popup-info>](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Using_shadow_DOM#working_through_a_simple_example)`)阴影 DOM 的 web 组件示例
4.  使用`[<template>](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/template)`标签创建一个 HTML 模板。将定制的`MyElement` web 组件的可重用内容放在模板中，并将它的一个克隆附加到 shadow DOM 中。您还可以向模板添加`<style>`和`[<slot>](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/slot)`标签(后者用于模板内的动态内容)。这一步也是可选的，您只需在需要的时候添加一个模板
5.  将`MyElement`作为 [ES6 模块](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules)导入
6.  您可以像使用任何标准 HTML 元素一样在页面上使用`<my-element></my-element>` web 组件

## 为什么人们不使用 web 组件

Web 组件已经承诺了许多了不起的事情。为什么没有更多的开发者使用它们？

这并不是因为对扩展 HTML 并使模块化前端开发简单快速的浏览器原生技术缺乏兴趣。web 组件的一个问题是进入门槛太高。现有的 web 组件，在很大程度上，不是非常用户友好的，规范本身也存在一些技术问题。

换句话说，web 组件在理论上是一个很好的想法，但在实现方面仍然有所欠缺。

让我们看看使用 web 组件的其他一些缺点。

### Web 组件不遵循本地 HTML 行为

Web 组件的行为与原生 HTML 元素类似。这意味着开发人员应该能够使用 HTML 标记来控制它们，例如标签、父元素和子元素之间的关系(想想`<select>`和`<option>`)以及属性——而不必编写 JavaScript。

对于大多数可用的 web 组件来说，这还没有实现。您需要对 JavaScript 有稳定的了解，以便通过所属的 API 设置和配置 web 组件，并添加它们需要的所有依赖项。Lea Verou 将这种现象描述为 HTML 没有“在这些组件的设计中得到适当的尊重”。

理论上，创建符合本地 HTML 行为的设计良好的定制元素是可能的。这在实践中很少发生。杰里米·基思(Jeremy Keith)在 2017 年的会议演讲“评估技术”中总结了这个问题(从那以后，情况没有太大改善):

> “当我看到使用中的 web 组件时，我倾向于看到这样的情况，它实际上是一个开始标记、结束标记，所有的内容、所有的行为和所有的样式都在其他地方，通过 JavaScript 被拉进来，这造成了一种单一的失败源。”

### 向后兼容和 SEO 的问题

显然，如果 web 组件不遵循 HTML 设计原则，就不能为旧的或不支持的浏览器添加内容，因此使用这些浏览器的人在屏幕上看不到任何东西。

理想情况下，自定义和标准 HTML 元素应该一起使用，并相互补充。只有当没有相应的标准元素时，才需要自定义元素。

例如，假设您想要创建一个水平的 tab 元素。如果你想让它与不支持的浏览器兼容，你可以把它设计成与一个标准的 HTML 元素一起工作，比如`<p>`、`<section>`或`<div>`，这取决于进入标签的内容类型:

```
<horizontal-tab>
  <p>CONTENT</p>
  <p>CONTENT</p>
  <p>CONTENT</p>
</horizontal-tab>

```

对于较低层次的兼容性，您可以为选项卡项目创建另一个自定义元素，但仍然通过 HTML 添加内容。引入`<tab-item>`是不必要的，因为您可以将内部内容存储在一个标准的 HTML 标签中。这里有一个这个解决方案的[真实世界的例子](https://www.webcomponents.org/element/HTMLElements/smart-tabs)。

```
<horizontal-tab>
  <tab-item>CONTENT</tab-item>
  <tab-item>CONTENT</tab-item>
  <tab-item>CONTENT</tab-item>
</horizontal-tab>

```

你可以设计一种通过 JavaScript 添加所有内容的方式。同样，不支持浏览器的用户在屏幕上什么也看不到。当然，您可以使用 polyfill，但是这会增加复杂性，这对许多开发人员来说是不值得的。

```
<horizontal-tab></horizontal-tab>

```

此外，通过 JavaScript 添加内容的 web 组件并不总是被搜索引擎索引。这种类型的设计[对 SEO 有负面影响](https://leofavre.github.io/web-components-seo/)。

### 删除 HTML 导入

放弃 HTML 导入规范是开发人员不愿意使用 web 组件的另一个原因。HTML 导入使您能够使用以下简单、初学者友好的语法在没有 JavaScript 的情况下导入模块:

```
<link rel="import" href="module.html">

```

由于浏览器供应商预计 ES6 模块会随着时间的推移取代 HTML 导入，他们决定不实现该特性； [Mozilla](https://hacks.mozilla.org/2014/12/mozilla-and-web-components/) 、 [Webkit](https://webkit.org/status/#feature-html-imports) 、 [Chrome](https://www.chromestatus.com/feature/5144752345317376) 都停止考虑添加 HTML 导入。

目前，web 开发者使用 [ES6 模块](https://blog.logrocket.com/es-modules-in-node-today/)代替 HTML 导入来打包 web 组件。这进一步增加了过程的复杂性，该过程本应是简单的和初学者友好的。

### web 组件规范的技术限制

正如我前面提到的，可以在三个地方找到与 web 组件相关的规范:

1.  自定义元素
2.  HTML 模板
3.  阴影 DOM

但是，这只是规格方面的问题之一。如果您开始开发定制元素，还会遇到许多其他技术问题。

Michael L. Haufe 在他的优秀文章“对 Web 组件的批评”中收集了其中一些。最令人不安的问题包括:

*   CSS 伪元素，比如`:checked`，不支持自定义元素
*   如果定制元素的行为相互关联，标准 HTML 元素就会忽略定制元素——例如，标准的`form`元素会忽略定制的输入元素
*   自定义元素不能扩展`HTMLButtonElement`等更高级别的标准元素，但需要扩展`HTMLElement`
*   ARIA 角色必须重新添加到自定义元素及其子元素中，即使这些是带有默认 ARIA 角色的标准 HTML 标记(请参见 [Salesforce 开发人员博客](https://developer.salesforce.com/blogs/2020/01/accessibility-for-web-components.html)上的更多信息)

这些技术问题源于 web 组件标准的局限性，必须使用各种黑客手段或额外的代码来解决(这进一步增加了复杂性)。

### 没有中央目录

找 web 组件不容易。没有中央目录或索引，所以你必须在网上冲浪找到你需要的东西。

如果您发现不止一个相同类型的 web 组件，那么很难找出哪一个更适合您的项目，主要是因为许多 web 组件缺乏良好的文档。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

### UI 库与 web 组件

UI 库，如 React、Vue 和 Angular，与 web 组件的用途相同:它们使基于组件的前端开发成为可能。尽管它们不是 web 浏览器的原生库(当 web 组件使用内置在浏览器中的[web API](https://developer.mozilla.org/en-US/docs/Web/API)，如`DOM`和`CustomElementRegistry`，你必须单独添加库)，但它们有一个巨大的生态系统、良好的文档和许多开发人员友好的特性。

因此，许多公司选择这些流行的 UI 库而不是尝试 web 组件是很自然的——特别是因为在就业市场上也更容易找到具有这种知识的开发人员。

## web 组件的未来

尽管 web 组件技术面临许多问题，但使用 web 组件仍然有一些优势。

最重要的是，尽管框架层出不穷(想想几乎已经完全消失的[backbone . j](https://backbonejs.org/)T2【s】T3)，但 web 组件是浏览器原生技术。这意味着，如果您正在进行长期规划，您可以通过选择定制开发的 web 组件来使您的项目经得起未来的考验。

许多流行的 UI 库也将它们的元素发布为 web 组件，因此您可以以一种与框架无关的方式在您的项目中使用它们——例如，这里有打包为定制元素的 [Angular 组件](https://angular.io/guide/elements)。这也表明，从长远来看，这些框架的创建者与 web 组件是有关系的。

有一些很有前途的 web 组件项目，都是由行业巨头开发的，包括微软的 [FAST components](https://github.com/microsoft/fast) 和 Salesforce 的 [Lightning Web Components](https://developer.salesforce.com/docs/component-library/documentation/en/lwc) 。此外，谷歌仍然保持着[聚合物项目](https://github.com/Polymer/polymer)，尽管有一个新的基于组件的类。

如果你不想使用别人的 web 组件，而是想创建自己的组件，有一些开源工具和资源，如 Ionic 的 [Stencil.js](https://stenciljs.com/) web 组件编译器(苹果也在使用)、[开放 web 组件](https://github.com/open-wc/open-wc)工具包和[Web 组件黄金标准清单](https://github.com/webcomponents/gold-standard/wiki)，可以帮助你遵循最佳实践。

鉴于这些发展，web 组件在未来被更广泛地采用还是有希望的。

## 结论

总的来说，能够访问更容易访问、更易于使用的 web 组件，类似于更原生的 HTML 元素，将是前端开发的一大进步。

为了实现这一点，web 组件的开发人员需要遵循最佳实践，更加尊重 HTML 的原则，创建更好的文档，并考虑向后兼容性。

web 组件标准也需要进一步改进，以解决使标准 HTML、CSS 和 JavaScript 难以使用定制元素的技术限制，以及可访问性和搜索引擎优化的问题。

要了解最新动态，您可以在 web 孵化器社区组(WICG)维护的 [webcomponents GitHub repo](https://github.com/WICG/webcomponents) 中跟踪与 Web 组件相关的最新新闻、问题和讨论。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)