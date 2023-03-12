# 用 web 组件构建条形图库

> 原文：<https://blog.logrocket.com/build-a-bar-chart-library-with-web-components/>

条形图是分类数据集的直观表示，其中条形是类别的直接映射，其大小(垂直条形的高度)与它们所代表的值成比例。

如果一个轴具有线性刻度(以匹配条形的大小)，条形相对于另一个轴(类别)的位置通常并不重要，它们只是均匀地占据空间。

在本文中，我们将介绍如何使用 web 组件构建条形图库。

## 在条形图库中创建线段单位

为了首先计算一个条形的比例，我们需要一个简单的函数来将一个值投射到一个单元的一个段上，该段代表我们想要显示的可能值的域:

```
const createScale = ({domainMin, domainMax}) => (value) => (value - domainMin) / (domainMax - domainMin);

```

例如，如果一个*单元*的一段从 0 到 100，值 50 将正好在该段的中间，而 25 将在四分之一处。

```
const scale = createScale({domainMin: 0, domainMax: 100});

scale(50) // > 0.5

scale(25) // > 0.25

```

你希望线段的物理单位是什么由你决定(900 像素，4 厘米等等)。我们还需要注意域所定义的范围之外的值(即，您无法放入该段的值)。

通常，如果该值较高，它将位于该段的末尾，而如果该值较低，相对比例将为空。

```
// an utility to compose functions together
const compose = (...fns) => (arg) => fns.reduceRight((acc, cfn) => cfn(acc), arg);

const greaterOrEqual = (min) => (value) => Math.max(min, value);

const lowerOrEqual = (max) => (value) => Math.min(max, value);

const createProjection = ({domainMin, domainMax}) => compose(
    lowerOrEqual(1),
    greaterOrEqual(0),
    createScale({
        domainMin,
        domainMax
    })
);

// example
const project = createProjection({domainMin: 0, domainMax: 100});

project(50); // > 0.5 "unit"

project(120); // > 1 "unit"

project(-40); // > 0 "unit

```

## 什么是 web 组件？

[Web components](https://developer.mozilla.org/en-US/docs/Web/Web_Components) 是一组三种技术，为开发人员提供了创建可共享 UI 控件作为常规 DOM 元素的能力:

*   自定义元素提供了一个低级 API 来创建全新的 HTML 元素
*   影子 DOM 将让我们封装一个*私有的* DOM 子树，并对文档的其余部分隐藏它
*   **HTML 模板** ( `<template>`和`<slot>`)有助于子树的设计以及它在其他 DOM 树中的位置

您不必为了创建 web 组件而同时使用所有这些组件。人们经常将 web 组件与影子 DOM 混淆，但是您可以创建一个完全没有影子 DOM 的自定义元素。

## 使用自定义元素创建 bar 组件

自定义元素的强大之处在于，它们是有效的 HTML 元素，您可以通过 HTML 以声明的方式使用，或者通过与任何 HTML 元素相同的 API(属性、事件、选择器等)以编程方式使用。).

要创建自定义元素，您需要一个扩展 HTML 元素基类的类。然后，您可以访问一些生命周期和挂钩方法:

```
export class Bar extends HTMLElement {

    static get observedAttributes() {
        return ['size'];
    }

    get size() {
        return Number(this.getAttribute('size'));
    }

    set size(value) {
        this.setAttribute('size', value);
    }

    // the absolute value mapped to the bar
    get value() {
        return Number(this.getAttribute('value'));
    }

    set value(val) {
        this.setAttribute('value', val);
    }

    attributeChangedCallback() {
        this.style.setProperty('--bar-size', `${this.size}%`);
    }
}

customElements.define('app-bar', Bar);

```

通常，您通过 HTML 属性(在我们的例子中是`size`)定义声明性 API，并通过 getters 和 setters 定义编程访问。定制元素通过静态 getter `observedAttributes`和反应式回调`attributeChangedCallback`公开可观察的属性，从而提供了某种反应式绑定(正如您可以在常见的 UI Javascript 框架中找到的那样)。

在我们的例子中，每当`size`属性改变时，我们就更新组件样式属性`--bar-size`，这是一个 [CSS 变量](https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_custom_properties)，我们可以用它来设置条形比例。

理想情况下，访问器应该反映属性，因此只使用简单的数据类型(字符串、数字、布尔值)，因为您不知道使用者将如何使用组件(通过属性、编程等)。).

最后，您需要将定制元素注册到一个全局注册表中，以便浏览器知道如何处理它在 DOM 中找到的新 HTML 元素。

现在，您可以在 HTML 文档中放置`app-bar`标签。与任何 HTML 元素一样，您可以使用 CSS 样式表将样式与它关联起来。在我们的例子中，例如，我们可以利用反应性 CSS 变量`--bar-size`来管理条的高度。

您将找到一个运行示例，使用以下[代码笔](https://codepen.io/lorenzofox3/full/xxEpyWz)或 [stackblitz](https://stackblitz.com/edit/basic-bar-chart?file=index.js) (用于更有组织的示例)。除了酒吧的高度，我们还添加了一些动画和一些增强来证明我们的观点。自定义元素在所有 HTML 元素之前，这使得它们在使用 CSS 和 HTML 等标准 web 技术时非常有表现力。

## 创建条形图区域

在上一节中，由于一个简单的 web 组件和一个样式表，我们成功地创建了一个接近实际条形图的东西。但是，如果所应用的一些样式是定制的，那么其中很大一部分是任何条形图的功能需求的一部分:

*   条形高度的比例
*   分类栏占据空间的方式(均匀以避免视觉偏见)

因此，我们需要将该部分封装在我们的组件中，以减少消费者对其使用的繁琐和重复。进入[阴影 DOM](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Using_shadow_DOM) 。

影子 DOM 使 web 组件能够创建自己的独立于文档其余部分的 DOM 树。这意味着你可以在其他元素不知道的情况下设置内部结构，就像黑盒一样。

同样，您可以定义专用于内部部件的私有和限定范围的样式规则。让我们看看下面的例子是如何进行的:

```
import {createProjection} from './util.js';

const template = document.createElement('template');

/// language=css
const style = `
:host{
    display: grid;
    width:100%;
    height: 100%;
}

:host([hidden]){
    display:none;
}

#bar-area{
    align-items: flex-end;
    display:flex;
    justify-content: space-around;
}

::slotted(app-bar){
    flex-grow: 1;
    height: var(--bar-size, 0%);
    background: salmon; // default color which can be overwritten by the consumer
}
`;

template.innerHTML = `
<style>${style}</style>
<div id="bar-area">
    <slot></slot>
</div>
`;

export class BarChart extends HTMLElement {

    static get observedAttributes() {
        return ['domainmin', 'domainmax'];
    }

    get domainMin() {
        return this.hasAttribute('domainmin') ?
            Number(this.getAttribute('domainmin')) :
            Math.min(...[...this.querySelectorAll('app-bar')].map(b => b.value));
    }

    set domainMin(val) {
        this.setAttribute('domainmin', val);
    }

    get domainMax() {
        return this.hasAttribute('domainmax') ?
            Number(this.getAttribute('domainmax')) :
            Math.max(...[...this.querySelectorAll('app-bar')].map(b => b.value));
    }

    set domainMax(val) {
        this.setAttribute('domainmax', val);
    }

    attributeChangedCallback(...args) {
        this.update();
    }

    constructor() {
        super();
        this.attachShadow({mode: 'open'});
        this.shadowRoot.appendChild(template.content.cloneNode(true));
    }

    update() {
        const project = createProjection({domainMin: this.domainMin, domainMax: this.domainMax});
        const bars = this.querySelectorAll('app-bar');

        for (const bar of bars) {
            bar.size = project(bar.value);
        }
    }

    connectedCallback() {
        this.shadowRoot.querySelector('slot')
            .addEventListener('slotchange', () => this.update());
    }
}

customElements.define('app-bar-chart', BarChart);

```

这里没什么新鲜事。首先，我们用 DOM 树创建一个`template`元素，由于附加了 shadow DOM (cf 构造函数)，它将被用作文档的私有树。

注意，这个模板有一个 [slot](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/slot) 元素，它实际上是一个洞，组件的消费者可以用其他 HTML 元素来填充。在这种情况下，这些元素不属于 web 组件的影子 DOM，而是保留在上层作用域中。然而，它们将按照 shadow DOM 布局的定义占据自己的位置。

我们还使用一种新的生命周期方法，命名为`connectedCallback`。每当组件安装到文档中时，该函数都会运行。我们注册了一个事件侦听器，每当分时段内容(条)发生变化时，它都会要求组件重新呈现。

我们有一个作用域样式，允许我们实现和封装条形图的功能需求(以前通过全局样式表实现的)。伪元素`:host`指的是 web 组件的根节点，而`::slotted`允许组件在“接收到的”元素(在我们的例子中是条)上定义一些默认样式。

默认情况下，自定义元素的`display`属性设置为`inline`；这里，我们用一个`grid`覆盖默认设置。但是，由于 CSS 特殊性规则，我们需要处理组件具有`hidden`属性的情况。

同样，投影高度的计算现在是组件内部的一部分。像以前一样，组件具有反应属性/特性，因此每当定义的域范围改变时，条形的比例也会改变。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

现在，我们可以将两个 web 组件结合在一起，用 HTML 创建条形图。在保持广泛的可定制性的同时，消费者不再有处理条形高度的计算和它们的呈现的负担。

你会注意到这两个组件之间有一个隐含的契约:`app-bar`的`size`属性将由`app-bar-chart`组件管理。

从技术上来说，消费者可以破坏干扰 css 变量`--bar-size`(封装泄漏)的行为，但这种权衡同时给了我们很大的灵活性。

```
<app-bar-chart>
    <app-bar value="7"></app-bar>
    <app-bar value="2.5"></app-bar>
    <app-bar value="3.3"></app-bar>
    <app-bar value="2.2"></app-bar>
    <app-bar value="4"></app-bar>
    <app-bar value="8.3"></app-bar>
    <app-bar value="3.1"></app-bar>
    <app-bar value="7.6"></app-bar>
 <app-bar-chart>

```

你会在下面的[代码栏](https://codepen.io/lorenzofox3/full/jOMYQJv) ( [Stackblitz](https://stackblitz.com/edit/chart-sample-2?file=index.js) )中找到一个更高级的例子，在这里你也可以定义条的方向。

## 定义条形图轴

到目前为止，该组件可以让读者快速掌握类别的相对比例。

然而，如果没有任何轴，仍然很难将这些比例映射到绝对值，并给给定的条形一个标签或类别。

**Categories 轴**
我们前面说过，条形的位置没有太大的意义，它们只需要均匀地占据空间即可。类别标签将遵循相同的逻辑。

首先，我们需要更改栏区域的模板，为轴添加一个槽，并添加一些样式以保持布局一致。CSS `grid`让一切变得简单:

```
// bar-chart.js
template.innerHTML = `
<style>
<!-- ...  -->

:host{
    /* ... */
    grid-template-areas:
    "bar-area"
    "axis-bottom";
    grid-template-rows: 1fr auto;
    grid-template-columns: auto 1fr;
}

#bar-area{
    /* ... */
    grid-area: bar-area;
}

#axis-bottom{
    display: flex;
    grid-area: axis-bottom;
}

</style>
<div id="bar-area">
    <slot name="bar-area"></slot>
</div>
<div id="axis-bottom">
    <slot name="axis-bottom"></slot>
</div>
`
```

现在条形图有两个不同的命名槽。然后我们需要指定子元素将被插入哪个槽。对于条形，我们将它们放入`bar-area`部分。我们将属性`slot`添加到带有值`bar-area`的条上。

我们将此行为作为默认行为添加到我们的 bar 组件中:

```
// bar.js
export class Bar extends HTMLElement {
    /* ... */
    connectedCallback() {
        if (!this.hasAttribute('slot')) {
            this.setAttribute('slot', 'bar-area');
        }
    }
}

```

在`connectedCallback`中，我们有条件地添加上述属性。请注意，对于默认属性，优先考虑用户指定的属性(因此也是条件)通常是一种好的做法，因为您不知道使用者将如何使用或扩展您的组件。

现在，让我们创建一个类别轴和一个标签组件，这将是一对简单的无逻辑组件，具有基本的样式来加强布局:

```
// label.js
const template = document.createElement('template');

/// language=css
const style = `
:host{
    display:flex;
}

:host([hidden]){
    display:none;
}

#label-text{
    flex-grow: 1;
    text-align: center;
}

:host(:last-child) #tick-after{
    display: none;
}

:host(:first-child) #tick-before{
    display: none;
}
`;

template.innerHTML = `
<style>${style}</style>
<div part="tick" id="tick-before"></div>
<div id="label-text"><slot></slot></div>
<div part="tick" id="tick-after"></div>
`;

export class Label extends HTMLElement {
    constructor() {
        super();
        this.attachShadow({mode: 'open'});
        this.shadowRoot.appendChild(template.content.cloneNode(true));
    }
}

customElements.define('app-label', Label);

// category-axis.js
const template = document.createElement('template');

/// language=css
const style = `
:host{
    display:flex;
    border-top: 1px solid gray;
}

:host([hidden]){
    display:none;
}

::slotted(app-label){
    flex-grow:1;
}

app-label::part(tick){
    width: 1px;
    height: 5px;
    background: gray;
}
`;

template.innerHTML = `
<style>${style}</style>
<slot></slot>
`;

export class CategoryAxis extends HTMLElement {
    constructor() {
        super();
        this.attachShadow({mode: 'open'});
        this.shadowRoot.appendChild(template.content.cloneNode(true));
    }

    connectedCallback() {
        if (!this.hasAttribute('slot')) {
            this.setAttribute('slot', 'axis-bottom');
        }
    }
}

customElements.define('app-category-axis', CategoryAxis);

```

现在，您可以将这些组件添加到 HTML 文档中:

```
<app-bar-chart domainmin="0" domainmax="10">
    <app-bar value="2.5"></app-bar>
    <app-bar value="3.3"></app-bar>
    <app-bar value="8.3"></app-bar>
    <app-bar value="3.1"></app-bar>
    <app-bar value="7.6"></app-bar>
    <app-category-axis>
        <app-label>
            <!-- custom template if you want -->
            <span>cat-1</span>
        </app-label>
        <app-label>cat-2</app-label>
        <app-label>cat-3</app-label>
        <app-label>cat-4</app-label>
        <app-label>cat-5</app-label>
    </app-category-axis>
</app-bar-chart>

```

除了一点之外，这里没有什么新东西:标签模板有两个带有`part`属性的元素。这允许您定制影子 DOM 的特定部分，而它们通常不能从组件外部访问。

你可以在下面的[代码笔](https://codepen.io/lorenzofox3/full/bGwazRd) ( [Stackblitz](https://stackblitz.com/edit/chart-sample-category-axis?file=index.js) )中看到它的作用。

**线性标度轴**
对于线性标度轴，我们将主要混合使用目前为止我们已经看到的技术，但是我们也将引入一个新概念:[自定义事件](https://developer.mozilla.org/en-US/docs/Web/API/CustomEvent)。

正如我们前面对条形图组件所做的那样，线性轴组件将公开一个声明性 API 来定义域范围值和两个连续刻度之间的间隔。

的确，让这个组件驱动域范围是有意义的，但同时，我们不想在条和轴之间添加耦合。

相反，我们将使用父条形图组件作为它们之间的[中介](https://en.wikipedia.org/wiki/Mediator_pattern),以便每当轴看到域改变时，它将通知条形图重新呈现条形图。

我们可以通过自定义事件实现这种模式:

```
// linear-axis.js

// ...

export class LinearAxis extends HTMLElement {

   static get observedAttributes() {
      return ['domainmin', 'domainmax', 'gap'];
   }

   // ...

   attributeChangedCallback() {
      const {domainMin, domainMax, gap} = this;
      if (domainMin !== void 0 && domainMax !== void 0 && gap) {
         this.update();
         this.dispatchEvent(new CustomEvent('domain', {
            bubbles: true,
            composed:true,
            detail: {
               domainMax,
               domainMin,
               gap
            }
         }));
      }
   }
}

```

除了调用更新之外，该组件还发出一个 CustomEvent，传递域值细节。我们传递两个标志`bubbles`和`composed`,以确保事件在树层次结构中上升，并且可以超出影子树的边界。

然后，在条形图组件中:

```
// bar-chart.js

// ...

class BarChar extends HTMLElement {

   // ... 

   connectedCallback() {
      this.addEventListener('domain', ev => {
         const {detail} = ev;
         const {domainMin, domainMax} = detail;
         // the setters will trigger the update of the bars
         this.domainMin = domainMin;  
         this.domainMax = domainMax;
         ev.stopPropagation();
      });
   }

}

```

我们只需像以前一样使用属性设置器向自定义事件注册一个对工具条更新的调用。我们已经决定停止事件的传播，因为在这种情况下，我们只使用事件来实现中介模式。

像往常一样，如果你对细节感兴趣，你可以看看[码笔](https://codepen.io/lorenzofox3/pen/BaLPmzv?editors=0010)或[栈利兹](https://stackblitz.com/edit/chart-sample-linear-axis?file=linear-axis.js)。

## 结论

我们现在已经拥有了以声明方式构建条形图的所有基本构件。然而，在编写代码时，您通常不会有可用的数据，而是在以后动态加载。这并不重要——关键是将数据转换成相应的 DOM 树。

有了 React、Vue.js 等库，这是一个非常简单的过程。请记住，将 web 组件集成到任何 web 应用程序中是微不足道的，因为它们毕竟是常规的 HTML 元素。

使用 web 组件的另一个好处是能够用少量代码定制图表和处理大量不同的用例。

虽然图表库通常很大，并且需要公开大量的配置来提供一些灵活性，但是 web 组件允许您简单地使用一点 CSS 和 Javascript 来创建您的条形图库。

感谢阅读！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)