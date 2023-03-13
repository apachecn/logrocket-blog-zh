# 对 Popper 和样式化组件使用 React

> 原文：<https://blog.logrocket.com/using-react-popper-styled-components/>

事实证明，工具提示和其他弹出窗口对于提高任何应用程序的 UX 都是必不可少的。它们使得进一步解释用户正在查看的项目变得容易——比如将鼠标悬停在工具提示上——或者导航到子部分，比如在下拉菜单中。

用 CSS 创建工具提示或下拉菜单很容易。然而，将它们放置在你的 UI 中总是很麻烦。通常，您必须引入 JavaScript 来实现这一点，这可能很繁琐。

这就是像 Popper.js 这样的库派上用场的地方。Popper 是一个 JavaScript 库，帮助您轻松管理 popovers。在本文中，我们将了解如何在 React 应用程序中使用 Popper。我们将涵盖:

注意，本文中使用的 Popper 版本是 v2，它在 v1 的基础上有许多改进。

## 什么是样式组件？

在我们真正开始研究 Popper 之前，让我们先看看本文中用来设计 popovers 样式的库。

波普不是预先设计好的。这是因为它不会创建你的工具提示或弹出窗口；这只会帮助他们用更少的代码更好地定位和执行。

你必须自己设计工具提示的样式。这就是样式化组件库的用武之地。

样式化组件反应库是一个很好的方式，可以很容易地对组件进行样式化反应。它使用基本的 CSS-in-JS 样式语法。

这个库允许你创建一个组件，并使用 [ES6 标记的模板文字](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals#tagged_templates)给它附加样式。下面的代码显示了一个样式化的`Button`组件:

```
import styled from 'styled-components';

const Button = styled.button`
  padding: 10px;
  border: 2px solid blue;
  border-radius: 4px;

  &:hover {
    border-color: red;
  }
`;

const Example1 = () => {
  return (
    <main>
      <Button>Click me</Button>
    </main>
  );
};

export default Example1;
```

结果应该是这样的:

![White Screen With Centered Light Grey Button Component Outlined In Blue. When Mouse Moves Over Button, Outline Changes To Red](img/1bb226b03c99751de8e1b8b412e5cec5.png)

&操作符是 styled-components 引用被样式化的组件的方式。可以把它想象成 JavaScript 在类中的`this`。

此外，在上面的例子中，我们发现在 ES6 模板中使用 CSS 来设计按钮的样式。`Button`组件——它是一个实际的 React 组件——是一个按钮，按钮元素的所有属性/特性都可以传递给`Button`组件:

```
const Example1 = () => {
  return (
    <main>
      <Button type="submit">Click me</Button>
    </main>
  );
};
```

我们不可能在这里详细介绍使用样式化组件的每一个细节，但是到目前为止，我所解释的关于样式化组件的内容对于本教程来说已经足够了。

## 波普是什么？

Popper 不是工具提示或下拉库。相反，它是一个使构建工具提示或下拉菜单更容易的库。换句话说，Popper 不会为您创建工具提示，但它会帮助您定位工具提示，使其始终适合您的 UI。

用 Popper 定位 popovers 是完全可定制的。您可以打开和关闭功能，自定义现有功能以满足您的需求，以及创建一些自定义功能。

Popper v2 的大小从超过 7kb 的 minzipped 减少到现在的 3kb minzipped。也可以通过树摇动从您的包[中删除未使用的代码。](https://blog.logrocket.com/tree-shaking-json-files-webpack/)

Popper 可用于任何弹出窗口，但它主要用于工具提示。虽然我将在本文中引用工具提示，但是您可以使用这些知识来放置您想要创建的任何弹出窗口。

Popper 默认支持 vanilla JS，但也支持 React。创建者创建了另一个名为 `[react-popper](https://www.npmjs.com/package/react-popper)`的[库来扩展核心 Popper 库，以便在 React 应用程序中使用。](https://www.npmjs.com/package/react-popper)

下面是一个用`react-popper`定位工具提示的基本交互示例:

![Dark Grey Background Demoing Interactive Tooltip Positioning With Centered Black Button And White Tooltip Above Line Of Tooltip Positioning Options. Tooltip Moves As User Clicks On Various Positioning Options](img/29d259f7b17f92b2c3a21444076e3d10.png)

你可以[在你的浏览器](https://e6n1w5.csb.app/example1)中与这个例子互动，看看它是如何为你自己工作的。

在本例中，我们提供了四种在按钮周围定位工具提示项的方法:

Popper 还提供了类似于`top-start`和`top-end`的其他位置，我们稍后会更详细地讨论这些位置。

## 开始使用`react-popper`

这是一个代码部分，所以试着跟着做。在这一节中，我们将讨论在 React 中开始使用 Popper 所需的一切。

### 设置新项目

首先，让我们使用下面的命令用 Vite 创建一个新的 React 项目:

```
npm create [email protected] popper-js -- --template react
```

我们的项目将被称为`popper-js`。你可以选择给你的名字取任何合适的名字。

接下来，我们将安装依赖项:

```
npm install styled-components @popperjs/core react-popper
```

现在我们已经建立了项目，您可以运行`npm run dev`在本地主机上启动项目。

接下来，让我们创建一个简单的工具提示来显示网站上的一些信息。清除`App.js`文件中的代码，并将以下内容粘贴到其中:

```
import {
 StyledAppContainer,
 LongEl,
 Tooltip,
 Button
} from "./App.styled"; 
import { useState } from "react"; 

export default function App() {
 const [referenceEl, setReferenceEl] = useState(null);
 const [popperEl, setPopperEl] = useState(null);

 return (
   <StyledAppContainer>
     <div>
       <LongEl />
       <Button ref={setReferenceEl}>I'm a mystery</Button>
       <Tooltip
         ref={setPopperEl}
       >
         <p>A tooltip</p>
       </Tooltip>
       <LongEl />
     </div>
   </StyledAppContainer>
 );
}
```

我们在上面的代码中所做的就是创建工具提示所需的组件。

`Button`组件是参考元素。Popper 使用 reference 元素作为放置工具提示的元素。工具提示组件是 popover 容器或元素，它表示将要弹出的内容。

我们还有几个`LongEl`组件，它们的唯一目的是使上下滚动视窗成为可能。这将有助于我们了解波普的工作。

reference 元素和 popper 或 popover 元素都被分配了[回调 refs](https://blog.logrocket.com/react-reference-guide-refs-dom/#callbackrefs) ，我们将使用它让 Popper 访问这些元素。

如果您保存之前创建的文件，您将会看到几个错误，因为我们还没有创建我们使用的样式组件。要解决这个问题，继续创建一个名为`App.styled.jsx`的新文件，并将以下代码粘贴到其中:

```
import styled from "styled-components";

export const LongEl = styled.div`
 min-height: 100vh;
 width: 100%;
`;

export const StyledAppContainer = styled.div`
 min-height: 100vh;
 margin: 0;
 font-family: sans-serif;
 text-align: center;
 background: #242424;
 display: flex;
 justify-content: center;
 align-items: center;
`;

export const Button = styled.button`
 background: #000;
 padding: 15px;
 border: 1px solid lightblue;
 border-radius: 8px;
 color: #fff;
 font-family: sans-serif;
 font-size: 0.8rem;
 font-weight: 500;
`;

export const Tooltip = styled.div`
 background: #fff;
 border-radius: 5px;
 padding: 10px;
 text-align: left;
 position: relative;
 font-size: 0.8rem;
 max-width: 140px;
`;
```

这将确保我们需要的所有组件都设计好了，随时可以使用。

### `usePopper`钩子

现在我们已经有了工具提示，可以用 Popper 来使用了。在`App.js`文件中，我们将从`react-popper`导入`usePopper`钩子，并在`App`组件中使用它:

```
import { usePopper } from "react-popper"; 

export default function App() {
 const [referenceEl, setReferenceEl] = useState(null);
 const [popperEl, setPopperEl] = useState(null);
  const { styles, attributes } = usePopper(referenceEl, popperEl, {
    placement: "bottom"
  });

 return (
   <StyledAppContainer>
     <div>
       <LongEl />
       <Button ref={setReferenceEl}>I'm a mystery</Button>
       <Tooltip
         ref={setPopperEl}
         style={styles.popper}
         {...attributes.popper}
       >
         <p>A tooltip</p>
       </Tooltip>
       <LongEl />
     </div>
   </StyledAppContainer>
 );
}
```

`usePopper`钩子有三个参数:

*   `referenceEl`—`popperEl`将沿其放置的参考元素
*   `popperEl`—popover 元素；在这种情况下，工具提示
*   `options` —帮助定制工具提示以满足您需求的对象。

`options`对象包括如下属性:

*   `modifiers` —一系列中间件
    *   用于更改 Popper 的默认功能和创建新功能
*   `placement` —工具提示的初始位置
*   `strategy` —描述要使用的定位策略
    *   默认情况下使用`absolute`
*   `onFirstUpdate` —第一次更新时调用的函数

稍后将详细讨论`modifiers`属性。对于`strategy`属性，在引用元素位于固定容器中的情况下，您可以很容易地将其从`absolute`更改为`fixed`，以帮助正确定位工具提示。

placement 属性接受工具提示的初始位置字符串，包括顶部、底部、左侧、右侧和其他位置。默认值是`auto`，它告诉 Popper 将工具提示放在空间最大的地方。其他可能的值有:

*   `top-start`
*   `top-end`
*   `right-start`
*   `right-end`
*   `bottom-start`
*   `bottom-end`
*   `left-start`
*   `left-end`

设置初始位置不会阻止 Popper 在需要时翻转工具提示。当我们讨论修饰语时，我们也将更多地关注翻转。

挂钩返回一个样式和属性的对象。两者都是用于为 tooltip 元素分配样式和属性的对象。两者都有一个 popper 属性，指定`popperEl`应该使用一组样式以及一组属性。

如果您将`styles.popper`登录到控制台，您实际上会有一个样式对象，Popper 知道您的工具提示将需要它来帮助工作。这些样式与我们使用的样式化组件完全不同。

当我们希望工具提示中有更多的功能时，也需要使用`attributes`。这方面的一个例子是让工具提示只在被触发时出现。另一个例子是在工具提示上有一个箭头，它指向工具提示去的任何地方，同时保持它在工具提示上的位置。

继续保存您的文件并运行项目。如果你没有关注，你可以在 CodeSandbox 中[打开这个例子。](https://codesandbox.io/s/popper-styled-components-e6n1w5?file=/src/Example3.js)

### 添加功能

到目前为止，我们只创建了一个总是可见的工具提示，这不是真正的工具提示，对不对？工具提示应该能够随时消失和出现。用 Popper 做到这一点非常容易。

让我们改进上面的例子，默认隐藏工具提示。然后，当参考元素被悬停时，它可以被显示。这意味着我们将向引用元素添加一个`onMouseEnter`和一个`onMouseLeave`事件，如下所示:

```
export default function App() {
 const [referenceEl, setReferenceEl] = useState(null);
 const [popperEl, setPopperEl] = useState(null);
 const { styles, attributes } = usePopper(referenceEl, popperEl, {
   placement: "bottom"
 });

 const showTooltip = () => {
   popperEl.setAttribute('data-show', true);
 }

 const hideTooltip = () => {
   popperEl.removeAttribute('data-show')
 }

 return (
   <StyledExampleContainer>
     <div>
       <LongEl />
       <Button 
         onMouseEnter={showTooltip}
         onMouseLeave={hideTooltip}
         ref={setReferenceEl}
       >
         I'm a mystery
       </Button>
       <Tooltip 
         ref={setPopperEl}
         style={styles.popper}
         {...attributes.popper}>
         <p>A tooltip</p>
       </Tooltip>
       <LongEl />
     </div>
   </StyledExampleContainer>
 );
}
```

下一步是将工具提示的样式更改为仅在设置了 data-show 属性时显示。转到`App.styled.jsx`文件，像这样更新`Tooltip`组件:

```
export const Tooltip = styled.div`
 background: #fff;
 border-radius: 5px;
 padding: 10px;
 text-align: left;
 position: relative;
 font-size: 0.8rem;
 max-width: 140px;
 visibility: hidden;

 &[data-show="true"] {
   visibility: visible;
 }
`
```

现在保存并运行项目来查看工作中的工具提示，或者您可以[在 CodeSandbox](https://codesandbox.io/s/popper-styled-components-e6n1w5?file=/src/Example4.js) 中查看更新的示例。

对于可访问性，您可能想要添加不仅仅是`onMouseEnter`和`onMouseLeave`事件。您还可以添加`onFocus`和`onBlur`事件，这样使用键盘导航的人也可以查看工具提示。

## 用修饰符扩展`react-popper`

到目前为止，我们已经看到了一个使用`react-popper`的基本例子，这可能是你的应用程序所需要的。但是如果你的应用程序需要比我们到目前为止所介绍的更多的功能，你也可以从这个库中得到更多。所以我们来看看吧！

当涉及到在 Popper 中添加或更改功能时，我们所需要的就是`usePopper`钩子的 modifiers 属性。默认情况下，Popper 附带了一些默认修改器，其中包括:

*   `popperOffset` —用于在参考元素周围定位弹出器元素
*   `offset` —用于沿其参考元素移动弹出器元素
    *   也用于将其移至其参考元素或从其参考元素移出
*   `popperOverflow` —通过防止弹出器被切断，帮助保持弹出器在其边界区域内可见
*   `arrow` —用于为工具提示指定一个箭头，并沿工具提示定位
*   `flip` —用于在需要时翻转工具提示
    *   也用于指定翻转功能的工作方式
*   `hide` —当 popper 元素与其参考元素分离时，用于隐藏 popper 元素

所有这些默认修改器，以及这里没有提到的任何其他修改器，都是 Popper 用来让你的工具提示完成它的工作的。我们可以创建更多的修改器或者更新当前的修改器来满足我们的需求。

首先，让我们看看如何创建修改器。

### 创建自定义修改器

修改器需要四个属性才能工作:

`name`属性表示修饰符的名称。就像每个默认修改器都有名字一样，你的也应该有。该名称使得从库的其他部分引用修改器成为可能。

每个修改器应该有一个唯一的名字，这样你就不会覆盖一个默认的或者自定义的修改器。因为修饰符的相关性以降序增加，所以在必要时，数组底部的修饰符将总是覆盖它前面的修饰符。

一个简单的例子是改变弹出元素和参考元素之间的距离:

```
const { styles, attributes } = usePopper(referenceEl, popperEl, {
   modifiers: [
     {
       name: "offset",
       options: {
         offset: [0, 10] // [skidding, distance]
       }
     }
   ]
 });
```

“阶段”属性与 Popper 修改器生命周期的三个不同核心阶段相关:

*   `main` —对于只想执行逻辑的修饰符
*   `read` —对于只想从 DOM 中读取的修饰符
*   `write`–对于只需要写入 DOM 的修饰符

这有助于分组 Popper 对 DOM 的访问。Popper 还为每个主要阶段提供了两个子阶段— `beforeMain`和`afterMain`。

`enabled`属性是一个布尔值，它指定修改器是否被启用。默认情况下，上面列出的默认修改器是启用的，但是您可以使用`enabled`属性轻松地禁用它们中的任何一个，如下所示:

```
const { styles, attributes } = usePopper(referenceEl, popperEl, {
  modifiers: [
    {
      name: 'flip',
      enabled: false
    }
  ]
});
```

上面的例子将阻止工具提示翻转。只是一个旁注，关闭翻转模式也会关闭将放置设置为自动的功能。换句话说，必须启用“翻转”修改器，自动放置才能工作。

`fn`属性是一个返回状态对象的函数，它实际上是创建的弹出器的状态。该函数还用于向修改器提供逻辑。

传统上，修饰符上的函数会创建一个无限循环，因为`react-popper`对所提供的配置进行浅层比较。对于内联函数，浅层比较会失败，并使代码认为配置发生了变化。

对此的解决方案是使用 [React 的](https://blog.logrocket.com/react-hooks-cheat-sheet-unlock-solutions-to-common-problems-af4caf699e70/#usememo) `[useMemo](https://blog.logrocket.com/react-hooks-cheat-sheet-unlock-solutions-to-common-problems-af4caf699e70/#usememo)` [钩子](https://blog.logrocket.com/react-hooks-cheat-sheet-unlock-solutions-to-common-problems-af4caf699e70/#usememo)或者在组件外声明函数。

还有其他属性用于[使用弹出器](https://popper.js.org/docs/v2/modifiers/#custom-modifiers)创建修改器。例如，有一个 requires 属性，用于指定正在创建的修改器所依赖的修改器。

此外，还有一个选项属性，用于指定用于配置修饰符的所有属性。如果你想要更多的道具，你应该去 [Popper docs 查看这些](https://popper.js.org/docs/v2/modifiers/#custom-modifiers)。

现在，让我们创建我们的第一个自定义修改器。当工具提示位于引用元素的顶部时，此修改器将简单地改变工具提示的背景颜色:

```
 const changeTooltipBg = useMemo(
   () => ({
     name: "tooltipBgChanger",
     enabled: true,
     phase: "write",
     fn: ({ state }) => {
       if (state.placement === "top") {
         popperEl.setAttribute('to-red', true);
       } else {
         popperEl.removeAttribute('to-red');
       }
     }
   }),
   [popperEl]
 );
  const { styles, attributes } = usePopper(referenceEl, popperEl, {
   placement: "bottom",
   modifiers: [
     changeTooltipBg
   ]
 });
```

这意味着你也必须调整你的风格。参见 CodeSandbox 中的[示例。](https://codesandbox.io/s/popper-styled-components-e6n1w5?file=/src/Example5.js)

请注意，我使用了`write`阶段，因为我们在这里[向 DOM](https://blog.logrocket.com/virtual-dom-react/) 写入数据。

### 使用默认修饰符

在我们结束这篇文章之前，我想让我们看看几个修饰语。我们将会看到一些对你的项目非常有用的默认修饰符。

#### 打滑和距离

`offset`修改器用于指定弹出器和参考元素之间的距离，以及弹出器元素沿参考元素的位移或滑动。

打滑和距离的值都作为数组传递，打滑作为第一个值。它们被传递到修改器的`options`对象的`offset`属性中:

```
const { styles, attributes } = usePopper(referenceEl, popperEl, {
   modifiers: [
     {
       name: "offset",
       options: {
         offset: [0, 0] // [skidding, distance]
       }
     }
   ]
 });
```

打滑和距离都接受正值和负值。对于滑动，如果工具提示当前位于顶部或底部，正值会向右移动或滑动工具提示。同样，如果工具提示当前位于左侧或右侧，它会将工具提示移动到顶部。同时，负值会产生相反的效果。

对于距离，正值会将工具提示推离参考元素。同时，负值会将工具提示推向参考元素，以便它可以覆盖在参考元素上。

除了直接指定值的数组之外，您还可以用函数来做这件事，这样您就可以动态地指定一个值。这是因为该函数返回一个具有`popper`、`reference`和`placement`属性的对象:

```
const changeDistance = useMemo(
   () => ({
     name: "offset",
     options: {
       offset: ({ popper, reference, placement }) => {
         if (placement === "top" || placement === 'bottom') {
           return [0, popper.height / 2];
         }

         return [0, 0];
       }
     }
   }),
   []
 );

 const { styles, attributes } = usePopper(referenceEl, popperEl, {
   placement: "bottom",
   modifiers: [changeDistance]
 });
```

popper 和 reference 属性分别返回 popper 元素和 reference 元素的高度和宽度。

#### 添加箭头

箭头对于指示弹出器指向的参考元素非常有用。就像使用 popper 元素一样，Popper 也为箭头元素提供样式和属性。

将箭头添加到我们之前构建的工具提示项目的步骤是:

*   创建箭头元素
*   引用箭头修饰符中的箭头元素
*   设置工具提示的样式

现在让我们实施这些步骤。

第一步，让我们转到`App.jsx`文件，为箭头添加一个元素:

```
 return (
   <StyledAppContainer>
     <div>
       <LongEl />
       <Button ref={setReferenceEl}>I'm a mystery</Button>
       <Tooltip
         style={styles.popper}
         data-show="true"
         {...attributes.popper}
         ref={setPopperEl}
       >
         <p>A tooltip with</p>
         <span
           ref={setArrowEl} // doesn't exist yet
           style={styles.arrow}
           {...attributes.arrow}
           className="arrow"
         />
       </Tooltip>
       <LongEl />
     </div>
   </StyledAppContainer>
 );
```

请注意连接到表示箭头的元素的箭头的属性和样式。这不会有任何影响，因为我们没有在 arrow 修饰符中引用 arrow 元素。

对于第二步，我们仍然在`App.js`文件中工作。创建引用箭头元素的新状态，如下所示:

```
const [arrowEl, setArrowEl] = useState(null);
```

接下来，我们引用箭头修饰符中的箭头元素，如下所示:

```
const { styles, attributes } = usePopper(referenceEl, popperEl, {
  placement: "left",
  modifiers: [
    { name: "arrow", options: { element: arrowEl } }
  ]
});
```

最后，对于第三步，将 Popper 中的样式添加到箭头不会创建箭头。相反，它有助于始终沿着工具提示移动箭头:

![Dark Grey Background With Small White Box Containing Multiple Text Lines. Arrow On Right Side Shown Moving Up And Down With Tooltip Moving Along With It](img/eb37f5f6725c2874eef43b6e7ec8da97.png)

我们负责创建工具提示，并根据箭头的位置对其进行定位。在`App.styled.jsx`文件中找到`Tooltip`样式的组件，并添加这些样式:

```
`
 .arrow,
 .arrow::before {
   position: absolute;
   width: 8px;
   height: 8px;
   background: inherit;
   transition: top 0.3s ease-in, bottom 0.3s ease-in, left 0.3s ease-in,
     right 0.3s ease-in;
 }

 .arrow {
   visibility: hidden;
 }

 .arrow::before {
   visibility: hidden;
   content: "";
   transform: rotate(45deg);
 }

 &[data-popper-placement^="top"] > .arrow {
   bottom: -4px;
 }

 &[data-popper-placement^="bottom"] > .arrow {
   top: -4px;
 }

 &[data-popper-placement^="left"] > .arrow {
   right: -4px;
 }

 &[data-popper-placement^="right"] > .arrow {
   left: -4px;
 }
`
```

我们可以做的另一件事是当箭头到达弹出器的边缘时隐藏它，因为它现在在边缘看起来不太好。这样做很容易！我们所要做的就是创建一个自定义修改器，并检查箭头何时到达边缘:

```
const hideArrowAtEdges = useMemo(
   () => ({
     name: "arrowHide",
     enabled: true,
     phase: "write",
     fn: ({ state }) => {
       const { arrow } = state.elements;

       if (arrow) {
         if (state.modifiersData.arrow.centerOffset !== 0) {
           arrowEl.setAttribute("data-hide", true);
         } else {
           arrowEl.removeAttribute("data-hide");
         }
       }
     }
   }),
   [arrowEl]
 );

 const { styles, attributes } = usePopper(referenceEl, popperEl, {
   placement: "left",
   modifiers: [
     { name: "arrow", options: { element: arrowEl } },
     hideArrowAtEdges
   ]
 });
```

请随意在 CodeSandbox 中与这个例子进行交互。

#### 隐藏 popper 元素

当参考元素在视口中不再可见时，可以隐藏弹出器元素。

当参考元素不再可见时，hide 修改器通过将属性`data-popper-reference-hidden`附加到 popper 元素来实现这一点。

我们所要做的就是选择属性并为其附加一个样式，如下所示:

```
&[data-popper-reference-hidden="true"] {
   visibility: hidden;
 }
```

[自己去看看](https://e6n1w5.csb.app/example6)。

## 通过`react-popper`使用 React 门户

门户对于管理大规模应用程序中的弹出窗口至关重要。它们帮助管理 popovers 的 DOM 层次结构。

通常，你会[想在小规模应用中使用](https://blog.logrocket.com/zindex-react-native-stack-elements-best-practices/) `[zIndex](https://blog.logrocket.com/zindex-react-native-stack-elements-best-practices/)`。然而，`zIndex`对于较大的应用程序不是很有效，因为很难大规模地跟踪所有的 zIndex 值。

门户允许您在 React 的默认 DOM 层次结构之外呈现 popovers。在 React 中使用门户非常简单。

使用带有`react-popper`的门户并不需要库有什么特别的东西。你不需要打开任何东西；您只需要用门户包装 popover 并指向目的地:

```
{createPortal(
    <Tooltip
      style={styles.popper}
      data-show="true"
      {...attributes.popper}
      ref={setPopperEl}
    >
     <p>A tooltip</p>
     <span
        ref={setArrowEl}
        style={styles.arrow}
        {...attributes.arrow}
        className="arrow"
      />
    </Tooltip>,
    document.getElementById("popper")
)}
```

[参见 CodeSandbox](https://codesandbox.io/s/popper-styled-components-e6n1w5?file=/src/Example7.js:1217-1663) 中的例子。

## 比较浮动 UI 和 Popper

2021 年，Popper 的[创造者宣布](https://github.com/atomiks) [Popper 现在是浮动 UI](https://opencollective.com/floating-ui/updates/popper-is-evolving-into-floating-ui) 。浮动 UI 只是 Popper 的下一个级别。

从特性、大小、跨平台支持、可伸缩性等方面来看，浮动 UI 客观上是更好的选择。

Popper v2 相比 v1 有了很大的改进，但是[浮动 UI 才是未来。](https://blog.logrocket.com/popper-vs-floating-ui/)与 Popper 只能在 vanilla JavaScript 和 React 中使用不同，浮动 UI 也可以在 React Native 和 Vue 中使用。

就他们的工作方式而言，没有太多不同。您可以随时从 Popper 轻松地[转换到浮动 UI。](https://floating-ui.com/docs/migration)

那么是什么让你在浮动 UI 上使用 Popper 呢？

浮动 UI 的一个缺点是它没有像 Popper 那样被广泛采用，所以与 Popper 相比，它的教程和项目样本更少。

然而，Popper 现在的一个缺点是，创作者还宣布该库将保持低维护状态，他信守了自己的诺言。如果可以的话，我会推荐你转到浮动 UI。

## 结论

在本教程中，我们了解了如何在 React 中使用 Popper v2 来更好地定位弹出器。我们研究了如何创建自定义修饰符来扩展`react-popper`，以及如何将 React Portals 与`react-popper`一起使用，这被证明是非常简单和有用的。

我们还研究了用样式化组件来设计我们的松饼的样式。顺便说一句，样式化组件不仅仅用于样式化 popovers。您还可以使用它来设计 React 中的任何东西，无论是本文中看到的简单元素还是库中的组件。

就是这样。感谢您的阅读，祝您黑客生涯愉快。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)