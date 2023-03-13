# ARIA-JavaScript 框架的实时区域

> 原文：<https://blog.logrocket.com/aria-live-regions-for-javascript-frameworks/>

现代的网络应用充满了状态更新:祝酒词、通知、加载屏幕，以及通常你在屏幕上注意到的任何相关变化。视力正常的用户可以注意到这些状态更新，但是屏幕阅读器用户呢？您需要确保他们也能收到这些重要的更新。

这就是咏叹调现场区域发挥作用的地方。它是标记和 ARIA 角色和属性的组合；当用 JavaScript 将内容注入其中时，它会向辅助技术设备宣布其内容的任何变化。对 ARIA-live 区域有一个坚实的理解有助于制作每个人都可以访问的应用程序和网站。

在本文中，我将讨论可以用来创建 ARIA-live 区域的可用选项，每个选项都有不同的用途。我还将展示一些工具，您可以使用它们在 React、Angular 和 Vue.js 等现代 JavaScript 框架中创建 ARIA-live 区域。

*向前跳转:*

## ARIA-实时区域属性

创建 ARIA-live 区域有多种方法，包括一些带有现成标记的 ARIA 角色。但是，您也可以使用一个`<div>`和特定的 ARIA-live 区域属性来创建一个定制的 live 区域。这些属性在不同的辅助技术之间可能会产生非常不一致的结果，因此在尝试自己创建自定义活动区域标记之前，请记住这一点。在本文的稍后部分，我们将回顾 ARIA 角色。

### `aria-live`

`aria-live`属性定义了辅助技术将在什么时候宣布实况区域的内容变化。这个属性有三个值:`off`、`polite`和`assertive`。`off`意味着内容不会被公布，所以让我们关注另外两个属性:

属性将设置区域等待，直到屏幕阅读器宣布内容正在阅读，以宣布在活动区域中注入的内容。

另一方面，`assertive` live 区域会立即宣布内容，打断屏幕阅读器正在做的任何其他动作。因此，当您需要向辅助技术用户提供关键或时间敏感信息时，应使用`assertive`实时区域。

### `aria-atomic`

`aria-atomic`属性将告诉屏幕阅读器，实时区域的内容是全部公布，还是只公布发生变化的部分。

如果您将此属性设置为`false`，直播区域将只宣布已更改的内容。如果设置为`true`，它会一次性宣布所有内容。为了进一步澄清，让我们假设我们有这个活动区域:

```
<div aria-live="polite">
  <p>The amount spent is of <span>$25.00</span></p>
</div>

```

如果我们添加了`aria-atomic`属性，然后将`span`中的内容更改为$30.00，应该是这样的:

```
<div aria-live="polite" aria-atomic="false">
  <p>The amount spent is of <span>$30.00</span></p>
</div>

```

如果属性的值是`false`，如上所述，它将只在`span`中宣布内容。在这种情况下，它会显示“$30.00”如果属性的值是`true`，它将公布整个字符串。在这种情况下，它会说“花费的金额是$30.00。”

至少理论上应该是这样的。根据 a11ysupport 的 aria-atomic 测试，当属性设置为`false`时，一些屏幕阅读器(主要是 JAWS)在读取内部内容的变化时会出现问题。所以如果你要使用这个属性，请记住这一点。

### `aria-relevant`

属性允许你为辅助技术指定你想要实时区域宣布的更新类型。

该属性有多个值，您可以添加多个值，并用空格分隔:

| 价值 | 描述 |
| --- | --- |
| `additions` | 当一个新的节点被添加到活动区域中时发出通知 |
| `text` | 当任何文本或文本替换(如`aria-label`或`alt`代表`img`元素)被添加到活动区域时，会发出通知 |
| `removals` | 当任何文本或替代文本从活动区域中删除时，会发出通知 |
| `additions text`(默认) | 宣布文本更改和活动区域内节点的添加 |
| `all` | 宣布活动区域内的任何变化(相当于编写`additions text removals`) |

对该属性的支持相当不一致。在 [a11ysupport 关于`aria-relevant`](https://a11ysupport.io/tests/tech__aria__aria-relevant) 的词条中可以看到，它只有部分支持。主要是，它没有区分`additions`和`additions text`的行为。更重要的是，对于大多数屏幕阅读器来说，它直接无法按照需要使用`removals`属性。因此，如果您计划使用这个属性，请记住这一点。

## 咏叹调的类型-现场区域

既然我们已经回顾了如何创建一个定制的 ARIA-live 区域，那么让我们看看如何使用 ARIA `roles`和预构建的标记。

### `role="status"`

根据 [W3C 规范](https://www.w3.org/WAI/WCAG22/Techniques/aria/ARIA22)，创建`role="status"`是为了“为用户提供咨询信息，但并不重要到足以证明`alert`的合理性”，换句话说，创建该属性是为了向用户提供关于任何不需要立即关注或采取行动的相关变更的信息。

对于消息通知、成功消息或任何类型的不紧急且可以随时处理的事件来说,`role="status"`非常有用。这个区域有`aria-live="polite"`和`aria-atomic="true"`的隐含属性。然而，根据 [a11ysupport 对`status`角色](https://a11ysupport.io/tests/tech__aria__status-role-named)的测试，Orca (Linux 的屏幕阅读器)和 NVDA 不会考虑`role="status"`的`aria-atomic`隐含值。因此，我建议在使用这个活动区域时，以显式形式添加`aria-atomic="true"`属性，如下所示:

```
<div role="status" aria-atomic="true">
  Your message was successfully sent!
</div>

```

除此之外，`role="status"`是一个非常受支持的 ARIA 角色，很可能成为你创建现场区域的主要工具之一。

### `role="alert"`

`role="alert"`旨在为用户提供重要且具有时效性的信息。通知用户会话即将结束、信息未保存或任何其他重要错误消息的警报是`role="alert"`的良好用例。

该区域具有隐式值`aria-atomic="true"`和`aria-live="assertive"`。由于它的“侵入性”感觉——它会停止屏幕阅读器正在做的任何动作——记住要谨慎使用它，只在需要的时候使用。这种活动区域的标记应该是这样的:

```
<div role="alert">
  Your changes were not saved due to a network error. Please try again
</div>

```

如果您需要为该角色的状态消息添加交互元素，请使用 [`role="alertdialog"`](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Roles/alertdialog_role) 而不是`role="alert"`。例如，可以预期用户将使用`<button>`关闭状态消息。

### `role="log"`

根据 W3C 规范的说法，`role="log"`角色创建了一个活跃的区域，“新信息以有意义的顺序添加进来，旧信息可能会消失。”这在旧信息存储在活动区域中并且需要遵循某种顺序的情况下非常有用，比如消息历史或更改日志。

这个活动区域具有隐式值`aria-live="polite"`和`aria-atomic="false"`，这意味着所有的改变都被宣布而不中断任何动作。此外，它将只宣布被更改的标记部分。

如果将`aria-atomic`值改为`true`，它会在每次注入新内容时宣布所有内容，这是不可取的。这种活动区域的标记可能是这样的:

```
<div role="log">
  <ul aria-label="Message story">
    <li>
      <span>Gabrielle: </span> Hi, I'm Gabrielle from XYZ Co. How may I help you?
    </li>
    <li>
      <span>Diana: </span> Hi, I'm Diana. I need to cancel my appointment.
    </li>
  </ul>
</div>

```

`role="log"` live 区域得到了不错的支持，除了带微软 Edge 的旁白，你可以在 [a11ysupport 对`role="log"`](https://a11ysupport.io/tests/tech__aria__log-role-named) 的测试中看到。如果需要考虑讲述人和 Microsoft Edge 这两种场景，那么您最好使用带有`aria-live="polite"`和`aria-atomic="false"`的自定义区域。`log`角色不像`status`和`alert`角色那样经常需要，但是在某些用例中记住它仍然很重要。

## 其他实时区域角色

有几个额外的活区域角色， [`marquee`](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Roles/marquee_role) 和 [`timer`](%22https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Roles/timer_role) ，但是由于它们的支持较差，所以不推荐使用它们。默认情况下，两者都有属性`aria-live="off"`，这意味着它们不会宣布直播区域内容的任何变化。

根据 [W3C 规范](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Roles/timer_role)，`role="timer"`被创建来包含“一个数字计数器，指示从一个起点开始经过的时间，或者到一个终点剩余的时间。”因此，这个角色非常适合计时器功能(指示经过的时间)或秒表功能(指示剩余时间)。

`role="marquee"`是为了添加持续更新的不重要信息而创建的。像股票价格变化或图表这样的东西对于这种类型的实时区域来说是理想的。

由于不支持这些角色，因此使用变通方法可能会有所帮助。例如，要提供关于完成任务剩余时间的警告，您可以使用`role="status`或`role="alert"`来代替。

### `<output>`

根据 [HTML 规范](https://html.spec.whatwg.org/multipage/form-elements.html#the-output-element)的说法，`<output>` HTML 元素旨在公开“应用程序执行的计算结果，或者用户操作的结果”

此元素不是 ARIA-live 角色。它应该只在表单内部或外部使用，以向屏幕阅读器用户显示表单内计算或操作的结果。该元素具有以下属性:

*   `for`:这是由空格分隔的相关表单元素 id 的列表
*   `form`:该属性有助于将`output`元素与任何表单相关联；只有在表单元素之外使用它时才有必要
*   `name`:表示元素的名称；它的工作原理和表单中的任何`input`元素一样

下面是一些`<output>`元素的例子:

```
<form>
  <input type="number" id="b" name="b" value="1" /> +
  <input type="number" id="a" name="a" value="3" /> =
  <output name="result" for="a b">4</output>
</form>

```

在这种情况下，`<output>`元素在一个表单中，所以它只需要有一个`for`属性，该属性包含您想要的对其值有贡献的元素列表:

```
<form action="/results" role="search" id="search-form">
  <label for="search-input">Search articles</label>
  <input type="search" id="search-input" />
  <input type="submit" value="Submit search" />
</form>
<p>There are <output for="search" form="search-form"></output> articles</p>

```

这里的`<output>`元素在表单之外，所以最好不仅使用`f`或属性，还使用`form`属性来创建活动区域和表单之间的关系。

这个元素非常有用，有些人甚至用它来创建通常用`role="status"`或`role="alert"`制作的活动区域。然而，记住它的屏幕阅读器支持需要一些额外的工作。如果你想知道更多，你可以查看斯科特·奥哈拉关于 T2 元素 T4 的文章。

现在，在解释了 ARIA-live 区域的所有可能性之后，是时候研究在现代 JavaScript 框架中有哪些选项可以用来制作 live 区域了！

## 用 JavaScript 框架创建 ARIA-live 区域

在这里，我将向您展示如何使用三个最流行的前端框架创建实时区域:React、Angular 和 Vue。经过一些研究，我决定使用三个依赖项——每个框架一个:

这是我们演示的场景。我们将有一个`<button>`让我们结束购物过程。当用户点击`<button>`时，信息“您的购买已经成功！”将出现在活动区域内。

但首先，我想定几条基本规则:

1.  我将严格按照它们各自文档中的解释来使用这些工具。这些工具中的一些可能可以以更高级的方式使用以获得更好的结果，但是通过坚持使用文档，我们将能够检查默认实现是否工作良好
2.  我想检查这些工具是否允许我创建`polite`和`assertive`活动区域，所以我将评估这些工具生成的标记

解释完之后，让我们开始吧！

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

### `LiveAnnouncer`–棱角分明

Angular 在其组件开发工具包中提供了`LiveAnnouncer`包来创建活动区域。我从在`app.component.html`文件中添加这个标记开始:

```
<div>
  <h1>Aria Live region text with LiveAnnouncer</h1> <button (click)="announce()">Complete your purchase!</button> </div>
```

然后，我转到`app.components.ts`文件，导入`LiveAnnouncer`创建`announce()`函数:

```
import { Component } from "@angular/core";
import { LiveAnnouncer } from "@angular/cdk/a11y";

@Component({
  selector: "app-root",
  templateUrl: "./app.component.html",
})
export class AppComponent {
  constructor(private announcer: LiveAnnouncer) {}

  public announce() {
    this.announcer.announce("Your purchase has been successful!");
  }
}
```

让我们看看它为我们的活动区域创建了什么样的标记:

```
<div class="cdk-live-announcer-element cdk-visually-hidden" aria-atomic="true" aria-live="polite" id="cdk-live-announcer-6"></div>

```

当用户点击`<button>`时，字符串“您的购买已经成功！”将被添加到元素中，有效地向辅助技术宣布内容:

```
<div class="cdk-live-announcer-element cdk-visually-hidden" aria-atomic="true" aria-live="polite" id="cdk-live-announcer-6">
  Your purchase has been successful!
</div>

```

如果我们需要创建一个`assertive`实时区域，我们可以给`this.announcer.announce`添加一个参数来指定我们想要一个`assertive`实时区域:

```
public announce() {
  this.announcer.announce("Your purchase has been successful!", "assertive");
}

```

下面是生成的标记:

```
<div class="cdk-live-announcer-element cdk-visually-hidden" aria-atomic="true" aria-live="assertive" id="cdk-live-announcer-7">
  Your purchase has been successful!
</div>

```

`LiveAnnouncer`根据您的需要，通过添加属性`aria-atomic="true"`和`aria-live` ( `polite`或`assertive`)来完成创建活动区域的任务。

这种方法适用于辅助技术，但正如我之前提到的，最好使用更合适的 ARIA 角色，如`status`或`alert`，因为它们为辅助技术用户添加了更多的上下文。仅举一个例子，`alert`角色将被 NVDA 通过叙述“警报”有效地宣布为警报消息。您的购买已经成功！”

我可能遗漏了更多的设置，但据我所知，这些都是我们从`LiveAnnouncer`中得到的选项，而且很有效！不过，就个人而言，我更喜欢使用自定义标记，以确保屏幕阅读器用户在活动区域出现时不会错过任何重要的上下文。

### live utils-view . js

Live Utils 是 [`vue-a11y-utils`](https://github.com/Jinjiang/vue-a11y-utils) 库的一部分。它由两个名为`Live`的实用程序组成，创建一个添加活动区域的上下文和一个名为`useLive()`的创建活动区域的函数。

我首先创建了一个名为`LiveExample.vue`的单文件组件:

```
<template>
  <h1>Vue live region example with Live Utils</h1>
  <button @click="announce(message, isAssertive)">
    Complete your purchase!
  </button>
</template>

<script setup>
  import { ref } from "vue";
  import { useLive } from "vue-a11y-utils";
  const message = ref("Your purchase has been successful");
  const isAssertive = ref(false);
  const [announce] = useLive();
</script>

```

在这个组件中，我使用了`useLive`来创建用户点击`<button>`时的活动区域。该函数有两个参数:一个`message`和一个决定活动区域是`assertive`(如果是`true`)还是`polite`(如果是`false`)的布尔值。

接下来，我将组件导入到`App.vue`文件中。我需要创建显示实时区域的上下文，所以我必须调用组件`Live`使其工作:

```
<template>
  <Live>
    <live-example />
  </Live>
</template>

<script setup>
  import { Live } from "vue-a11y-utils";
  import LiveExample from "@/components/LiveExample.vue";
</script>

```

这是我面临的第一个问题，因为在 Vue a11y utils 的文档中，这个上下文显示为`<VueLive>`，而不是`<Live>`，但是如果你尝试使用`VueLive`，你会在你的控制台中看到错误消息:

`“export 'VueLive' (imported as 'VueLive') was not found in 'vue-a11y-utils' (possible exports: FocusTrap, Live, ariaToAttrs, directiveAria, genId, getTabindexByRole, useGlobalHotkey, useHotkey, useLive, useTravel)”`

这个错误不难修复。让我们直奔主题，检查现场区域输出:

```
<div style="position: absolute; height: 1px; width: 1px; margin: -1px; clip: rect(0px, 0px, 0px, 0px); overflow: hidden;">
  <div role="log" aria-live="assertive" aria-busy="false"></div>
  <div role="log" aria-live="assertive" aria-busy="false"></div>
  <div role="log" aria-live="polite" aria-busy="false"></div>
  <div role="log" aria-live="polite" aria-busy="false"></div>
</div>

```

这个输出有相当多的问题！

首先，`style`属性中的规则是为了在视觉上隐藏这个容器，而不是对屏幕阅读器隐藏。隐藏某些具有语义值的元素(如标题)是一种很好的可访问性技术。然而，对于实时区域，这种策略是有争议的。在大多数情况下，您还需要创建视觉反馈。

其次，由于某种原因，这个容器有四个活动区域，每个区域有两个副本。这可能与文档中提到的`isBusy`属性有些关系。然而，由于同一文档提到它是实验性的，我不会考虑它。感觉没必要的多余。

最后，那些活动区域包括`log`角色，由于其隐含的值`aria-atomic="false"`，该角色只对一些非常特殊的情况有用。我个人认为这个工具本应该尝试用更灵活的用例来创建 live 区域。

在本例中，如果`assertive`属性为`false`，当用户点击`<button>`时，消息将被注入第三个活动区域(具有`aria-live="polite"`属性的区域)。如果`assertive`属性设置为`true`，第一个直播区域将接收内容。

从最基本的意义上来说，这个工具只适用于一些不需要视觉反馈的特定情况。我不会在一般情况下使用这个工具。如果我要使用 Vue，我更愿意为这个任务创建一个自定义工具，或者使用另一个依赖项，比如 [`vue-announcer`](https://github.com/vue-a11y/vue-announcer/tree/next) 。我决定在本文中使用 Live Utils，因为它似乎有更多的更新，但是对于常见的用例来说，结果并不理想。

### `react-a11y-announcer`–做出反应

[`react-a11y-announcer`](https://github.com/thinkcompany/react-a11y-announcer) 是由思公司创建的。它创建了一个名为`Announcer`的组件，您可以在其中添加活动区域的内容。这很容易实现，你只需要这个组件和一个`useState`。

文档仍然使用类语法。这是可以的(意味着它会工作)，但以现代标准来看，它只是感觉有点过时。相反，我决定用更现代的语法创建这个例子。

下面是我在我的`App.jsx`文件中使用的代码:

```
import Announcer from 'react-a11y-announcer';
import { useState } from "react";

export default function App() {

  const [announcement, setAnnouncement] = useState("")

  const handleClick = () => {
    setAnnouncement("Your purchase has been successful!")
  }

  return (
    <div className="App">
      <h1>react-a11y-announcer test</h1> <button onClick={handleClick}>Complete your purchase!</button> <Announcer text={announcement} /> </div> ); }
```

在这里，我将活动区域的内容添加到一个`useState`中，创建一个函数来改变该状态，然后用属性`text`将它添加到`Announcer`组件中。

好了，让我们检查一下现场区域的输出:

```
<div aria-atomic="true" aria-live="polite" style="position: absolute; visibility: visible; overflow: hidden; display: block; width: 1px; height: 1px; margin: -1px; border: 0px; padding: 0px; clip: rect(0px, 0px, 0px, 0px); clip-path: polygon(0px 0px, 0px 0px, 0px 0px, 0px 0px); white-space: nowrap;">
  <p>Your purchase has been successful!&nbsp;</p>
</div>

```

就像来自 Vue 的 Live Utils 一样，`react-a11ly-announcer`创建了一个视觉上隐藏的 Live 区域，由于我之前解释的原因，这并不理想。但是不像活的实用程序，如果活的区域是`polite`或`assertive`，它没有任何种类的道具可以改变。

`react-a11ly-announcer`创建了一个默认为`polite`且(根据文档)不能更改的活动区域。因此，根据我对这个项目的最初规则，我不得不说这种依赖可能不是最好的。

然而，在做了一点试验后，我注意到你可以给`Announcer`组件添加更多的道具。您可以添加您想要的`role`，属性，甚至是一个`className`，它将删除`style`属性，使其可见。因此，理论上，如果我想要一个有`alert`角色的活动区域，我可以这样做:

```
<Announcer text={announcement} role="alert" aria-live="assertive" className="status" />

```

记住，它默认有属性`aria-live="polite"`，需要手动更改。这种变通方法解决了我们的问题，但是我有两个问题:

1.  它不在文档中，所以大多数人可能会错过这个功能
2.  如果我需要添加这些道具来创建一个活动区域，那么我为什么需要这种依赖呢？我最好自己创建一个组件，而不是使用第三方的包。为我的示例创建一个组件会更实际，因为我不需要添加`aria-live="assertive"`属性(因为我自己添加了一个`role="alert"`)

你可以修复`react-a11y-announcer`已经默认的错误，但是感觉还是不太直观。如果你需要用它来创建一个不需要在屏幕上显示的`polite`实时区域，那么使用这个包。否则，你最好自己创建它。

## 结论

我写这篇文章的目的绝不是贬低人们为构建使用 ARIA-live 区域来帮助创建更易访问的应用程序的工具所做的努力。然而，正如我们在本文中看到的，还有很大的改进空间。

当您创建可访问的应用程序并使用任何外部包时，测试结果并确保包适合您的用例是非常重要的。只有当你对你想测试的东西有了基本的了解，这才有可能。我希望这篇文章对 ARIA-live 区域以及如何根据您的具体需求构建一个区域提供了一些见解。

## 您是否添加了新的 JS 库来提高性能或构建新特性？如果他们反其道而行之呢？

毫无疑问，前端变得越来越复杂。当您向应用程序添加新的 JavaScript 库和其他依赖项时，您将需要更多的可见性，以确保您的用户不会遇到未知的问题。

LogRocket 是一个前端应用程序监控解决方案，可以让您回放 JavaScript 错误，就像它们发生在您自己的浏览器中一样，这样您就可以更有效地对错误做出反应。

[![LogRocket Dashboard Free Trial Banner](img/e8a0ab42befa3b3b1ae08c1439527dc6.png)](https://lp.logrocket.com/blg/javascript-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/javascript-signup)

[LogRocket](https://lp.logrocket.com/blg/javascript-signup) 可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

自信地构建— [开始免费监控](https://lp.logrocket.com/blg/javascript-signup)。