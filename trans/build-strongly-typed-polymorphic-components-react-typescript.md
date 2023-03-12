# 用 React 和 TypeScript 构建强类型多态组件

> 原文：<https://blog.logrocket.com/build-strongly-typed-polymorphic-components-react-typescript/>

在这篇详细的(解释性的)指南中，我将讨论如何用 TypeScript 构建强类型多态 React 组件。我们将涵盖以下部分:

如您所见，这相当长，所以可以随意跳过。如果你想跟着做，在我的 GitHub 上启动[官方代码库](https://github.com/ohansemmanuel/polymorphic-react-component)作为参考。

## 多态组件的真实例子

有一个非零的机会，你已经使用了多态组件。开源[组件库](https://blog.logrocket.com/build-component-library-react-typescript/)通常实现某种多态组件。

让我们考虑一些你可能熟悉的:脉轮 UI `as`道具和 MUI `component`道具。

### 查克拉 UI 的`as`道具

![Chakra UI](img/e970b8dfb7f886b3ee300aa945d5ee53.png)

[查克拉 UI](https://chakra-ui.com/) 如何实现多态道具？答案是通过暴露一个`as`道具。将`as`属性传递给一个组件，以确定它最终应该呈现什么容器元素。

[![Chakra UI As Prop](img/cdb05b0b601e5be305d795d40b69ccc5.png)](http://chakra-ui.com/docs/components/layout/box#as-prop)

要使用`as`属性，您需要做的就是将它传递给组件，在本例中是`Box`:

```
<Box as='button' borderRadius='md' bg='tomato' color='white' px={4} h={8}>
  Button
</Box>

```

现在，组件将呈现一个`button`元素。

![The Box Component Rendered As A Button](img/a9ff7c9e3cfb01e1059911ce318cbe2e.png)

如果你把`as`道具改成了`h1`:

```
<Box as="h1"> Hello </Box>

```

现在，`Box`组件呈现一个`h1`:

![The Box Component Rendered As An H1](img/943fb125b2ffb960cfae7fb3da07fa2d.png)

那是多态组件在起作用！它可以被渲染成完全独特的元素，只需传递一个道具。

### 梅的`component`道具

![MUI's Component Prop](img/6b3c87bb73f388418a2fc445818b0f67.png)

与 Chakra UI 类似， [MUI](https://blog.logrocket.com/definitive-guide-react-material/) 允许一个名为`component`的多态道具，其实现方式类似:将它传递给一个组件，并声明想要呈现的元素或定制组件。

下面是官方文件中的一个例子:

[![The MUI Component Prop](img/e7b880a805fd9da35dea9f6e9c26b761.png)](https://mui.com/material-ui/guides/composition/#component-prop)

```
<List component="nav">
  <ListItem button>
    <ListItemText primary="Trash" />
  </ListItem>
</List>

```

`List`被传递一个`nav`的组件道具；当这个被渲染时，它将渲染一个`nav`容器元素。

另一个用户可能使用相同的组件，但不是用于导航；相反，他们可能想要呈现一个待办事项列表:

```
<List component="ol">
  ...
</List>

```

在这种情况下，`List`将呈现一个有序列表`ol`元素。

说到灵活性！请参见多态组件用例的[摘要。](https://github.com/ohansemmanuel/polymorphic-react-component/blob/master/use-cases.pdf)

正如您将在本文接下来的部分中看到的，多态组件非常强大。除了接受元素类型的道具之外，它们还可以接受自定义组件作为道具。

这将在本文的下一节讨论。现在，让我们开始构建我们的第一个多态组件！

## 构建简单的多态组件

与您所想的相反，构建您的第一个多态组件非常简单。下面是一个基本实现:

```
const MyComponent = ({ as, children }) => {
  const Component = as || "span";

  return <Component>{children}</Component>;
};

```

这里注意多态道具`as`类似于查克拉 UI 的。这是我们用来控制多态组件的渲染元素的道具。

其次，注意`as`道具不是直接渲染的。以下是错误的:

```
const MyComponent = ({ as, children }) => {
  // wrong render below 👇 
  return <as>{children}</as>;
};

```

当[在运行时](https://reactjs.org/docs/jsx-in-depth.html#choosing-the-type-at-runtime)呈现一个元素类型的时候，必须先把它赋给一个大写的变量，然后再呈现这个大写的变量。

![Using A Capitalized Variable](img/5f9ed41ae4b8d81974bbbc146cd99cfb.png)

现在，您可以继续使用该组件，如下所示:

```
<MyComponent as="button">Hello Polymorphic!<MyComponent>
<MyComponent as="div">Hello Polymorphic!</MyComponent>
<MyComponent as="span">Hello Polymorphic!</MyComponent>
<MyComponent as="em">Hello Polymorphic!</MyComponent>

```

注意，不同的`as`道具被传递给上面的渲染组件。

![The Different Rendered Elements](img/84b03409c0f9b3b3413d326fe0fcd4e6.png)

## 这个简单实现的问题是

上一节中的实现虽然相当标准，但有许多缺点。让我们来探索其中的一些。

### 1.`as`属性可以接收无效的 HTML 元素

目前，用户可以编写以下内容:

```
<MyComponent as="emmanuel">Hello Wrong Element</MyComponent>

```

这里传的`as`道具是`emmanuel`。Emmanuel 显然是一个错误的 HTML 元素，但是浏览器也试图渲染这个元素。

![Rendering A Wrong HTML Element Type](img/f3a4824ee27fcadaa15628ee91fe0c19.png)

理想的开发体验是在开发过程中显示出某种错误。例如，用户可能打了一个简单的错别字——`divv`而不是`div`——却没有得到任何错误的提示。

### 2.可以为有效元素传递错误的属性

考虑以下组件用法:

```
<MyComponent as="span" href="https://www.google.com">
   Hello Wrong Attribute
</MyComponent>

```

消费者可以将一个`span`元素传递给`as`道具，以及一个`href`道具。

这在技术上是无效的。一个`span`元素不(也不应该)接受一个`href`属性。这是无效的 HTML 语法。然而，我们构建的组件的消费者仍然可以继续编写，并且在开发过程中不会出现错误。

### 3.没有属性支持！

再次考虑简单的实现:

```
const MyComponent = ({ as, children }) => {
  const Component = as || "span";

  return <Component>{children}</Component>;
};

```

这个组件接受的唯一道具是`as`和`children`，没有其他的。甚至对有效的`as`元素属性也没有属性支持，也就是说，如果`as`是锚元素`a`，我们也应该支持向组件传递一个`href`。

```
<MyComponent as="a" href="...">A link </MyComponent>

```

即使在上面的例子中传递了`href`,组件实现也没有收到其他的支持。只有`as`和`children`被解构。

您最初的想法可能是继续将每个其他属性传递给组件，如下所示:

```
const MyComponent = ({ as, children, ...rest }) => {
  const Component = as || "span";

  return <Component {...rest}>{children}</Component>;
};

```

这似乎是一个体面的解决方案，但现在它突出了上面提到的第二个问题。错误的属性现在也会传递给组件。

请考虑以下情况:

```
<MyComponent as="span" href="https://www.google.com">
   Hello Wrong Attribute
</MyComponent>

```

并注意最终呈现的标记:

![A Span Element With An Href Attribute](img/bf2387151da3eb5b58e78fc0dec2e339.png)

带有`href`的`span`是无效的 HTML。

### 为什么这样不好？

总的来说，我们简单实现的当前问题很糟糕，因为:

*   它提供了糟糕的开发者体验
*   它不是类型安全的。虫子可以(也将会)爬进来

我们如何解决这些问题？需要明确的是，这里没有魔杖可以挥舞。然而，我们将利用 TypeScript 来确保您构建强类型多态组件。

完成后，使用您的组件的开发人员将避免上述运行时错误，而是在开发或构建时发现它们——这都要感谢 TypeScript。

## 如何在 React 中使用 TypeScript 构建强类型多态组件

如果您正在阅读本文，前提条件是您已经了解一些 TypeScript——至少是基础知识。如果你不知道什么是 TypeScript，我强烈建议你先阅读一下这个文档。

在本节中，我们将使用 TypeScript 来解决上述问题，并构建强类型多态组件。

我们首先要满足的前两个要求包括:

*   `as`属性不应该接收无效的 HTML 元素字符串
*   不应为有效元素传递错误的属性

在下一节中，我们将介绍 TypeScript 泛型，以使我们的解决方案更加健壮、对开发人员更友好、更适合生产。

### 确保`as` prop 只接收有效的 HTML 元素字符串

这是我们目前的解决方案:

```
const MyComponent = ({ as, children }) => {
  const Component = as || "span";

  return <Component>{children}</Component>;
};

```

为了使本指南的下一节更加实用，我们将把组件的名称从`MyComponent`改为`Text`，并假设我们正在构建一个多态的`Text`组件。

```
const Text = ({ as, children }) => {
  const Component = as || "span";

  return <Component>{children}</Component>;
};

```

现在，根据你对泛型的了解，很明显我们最好用泛型来表示`as`，也就是说，基于用户传入的变量类型。

![The As Prop](img/4740027cd7533d1d64f651f3a72cec47.png)

让我们继续，并按如下方式迈出第一步:

```
export const Text = <C>({
  as,
  children,
}: {
  as?: C;
  children: React.ReactNode;
}) => {
  const Component = as || "span";

  return <Component>{children}</Component>;
};

```

注意泛型`C`是如何定义的，然后在属性`as`的类型定义中传递。

然而，如果您编写了这个看似完美的代码，您将会看到 TypeScript 用比您想要的更多的弯曲的红线喊出许多错误🤷‍♀️

![The JSX Generic Error](img/5d06856dcdbd51fd57cfce86a50ae016.png)

这里发生的是`.tsx`文件中泛型的[语法中的一个缺陷。有两种方法可以解决这个问题。](https://stackoverflow.com/questions/32308370/what-is-the-syntax-for-typescript-arrow-functions-with-generics?)

### 1.在泛型声明后添加一个逗号

这是声明多个泛型的语法。一旦这样做了，TypeScript 编译器就会清楚地理解您的意图，错误就会被排除。

```
// note the comma after "C" below 👇
export const Text = <C,>({
  as,
  children,
}: {
  as?: C;
  children: React.ReactNode;
}) => {
  const Component = as || "span";

  return <Component>{children}</Component>;
};
```

### 2.约束类属

第二种选择是在您认为合适的时候约束类属。对于初学者，您可以使用如下的`unknown`类型:

```
// note the extends keyword below 👇
export const Text = <C extends unknown>({
  as,
  children,
}: {
  as?: C;
  children: React.ReactNode;
}) => {
  const Component = as || "span";

  return <Component>{children}</Component>;
};
```

现在，我将坚持第二个解决方案，因为它更接近我们的最终解决方案。不过，在大多数情况下，我使用多重通用语法，只添加一个逗号。

然而，对于我们当前的解决方案，我们得到了另一个 TypeScript 错误:

> JSX 元素类型“Component”没有任何构造或调用签名。ts(2604)

![No Construct Or Call Error](img/77623ab1f5a70fb3c46fb36f930f58a0.png)

这类似于我们使用`echoLength`函数时的错误。就像访问未知变量类型的`length`属性一样，这里可以这么说:试图将任何泛型类型呈现为有效的 React 组件是没有意义的。

我们只需要约束泛型来适应有效的 React 元素类型。

为了实现这一点，我们将利用内部 React 类型:`React.ElementType`，并确保泛型被约束为适合该类型:

```
// look just after the extends keyword 👇
export const Text = <C extends React.ElementType>({
  as,
  children,
}: {
  as?: C;
  children: React.ReactNode;
}) => {
  const Component = as || "span";

  return <Component>{children}</Component>;
};

```

请注意，如果您使用的是旧版本的 React，您可能需要导入新版本的 React，如下所示:

```
import React from 'react'

```

有了这个，我们就不会再有错误了！

现在，如果您继续按如下方式使用这个组件，它会工作得很好:

```
<Text as="div">Hello Text world</Text>

```

然而，如果您传递一个无效的`as`属性，您现在将得到一个适当的 TypeScript 错误。考虑下面的例子:

```
<Text as="emmanuel">Hello Text world</Text>

```

并抛出错误:

> 类型“emmanuel”不能赋给类型“ElementType | undefined”。

![Type Emmanuel Is Not Assignable Error](img/6cd2419345effb5e7bbe8d1c23cd94c6.png)

This is excellent! We now have a solution that doesn’t accept gibberish for the `as` prop and will also prevent against nasty typos, e.g., `divv` instead of `div`.

这是一个更好的开发者体验！

## 用 TypeScript 泛型处理有效的组件属性

在解决第二个用例时，您将会体会到泛型的真正强大之处。首先，让我们了解我们在这里试图完成什么。

一旦我们收到一个通用的`as`类型，我们希望确保传递给我们的组件的剩余属性是相关的，基于`as`属性。

例如，如果一个用户传入了一个`img`的`as`道具，我们希望`href`同样是一个有效的道具！

![As Prop And Href](img/3985285f9fc772a8203898a05d45fa75.png)

为了让您了解我们是如何实现这一目标的，请看一下我们解决方案的当前状态:

```
export const Text = <C extends React.ElementType>({
  as,
  children,
}: {
  as?: C;
  children: React.ReactNode;
}) => {
  const Component = as || "span";

  return <Component>{children}</Component>;
};

```

该组件的属性现在由对象类型表示:

```
{
  as?: C;
  children: React.ReactNode;
}

```

在伪代码中，我们希望如下所示:

```
{
  as?: C;
  children: React.ReactNode;
} & {
  ...otherValidPropsBasedOnTheValueOfAs
}

```

![Pseudocode](img/ed99b08c6dd8f60beacaba30c52b982f.png)

这个要求足以让人抓住救命稻草。我们不可能编写一个基于`as`的值来确定适当类型的函数，手动列出一个联合类型并不明智。

那么，如果有一个从`React`提供的类型作为一个“函数”,根据你传递给它的内容返回有效的元素类型呢？

在介绍解决方案之前，让我们先做一点重构。让我们把组件的道具抽成一个单独的类型:

```
// 👇 See TextProps pulled out below 
type TextProps<C extends React.ElementType> = {
  as?: C;
  children: React.ReactNode;
} 

export const Text = <C extends React.ElementType>({
  as,
  children,
}: TextProps<C>) => { // 👈 see TextProps used 
  const Component = as || "span";
  return <Component>{children}</Component>;
};

```

这里重要的是注意泛型是如何传递给`TextProps<C>`的。类似于 JavaScript 中的函数调用——但有尖括号。

这里的魔术棒是利用如下所示的`React.ComponentPropsWithoutRef`类型:

```
type TextProps<C extends React.ElementType> = {
  as?: C;
  children: React.ReactNode;
} & React.ComponentPropsWithoutRef<C>; // 👈 look here 

export const Text = <C extends React.ElementType>({
  as,
  children,
}: TextProps<C>) => {
  const Component = as || "span";
  return <Component>{children}</Component>;
};

```

请注意，我们在这里引入了一个交叉点。本质上，我们说，`TextProps`的类型是一个包含`as`、`children`和其他一些由`React.ComponentPropsWithoutRef`表示的类型的对象类型。

![React.ComponentPropsWithoutRef](img/6dc76d978b355056a9047d2a703c4810.png)

如果您阅读代码，可能会明白这里发生了什么。

基于由泛型`C`表示的`as`的类型，`React.componentPropsWithoutRef`将返回与传递给`as`属性的字符串属性相关的有效组件属性。

还有更重要的一点需要注意。

![Different ComponentProps Type Variants](img/1591997b1b5654b3943ca546ff6dc004.png)

如果你刚开始打字并依赖编辑器的智能感知，你会发现有三种类型的`React.ComponentProps...`:

1.  `React.ComponentProps`
2.  `React.ComponentPropsWithRef`
3.  `React.ComponentPropsWithoutRef`

如果您尝试使用第一个选项`ComponentProps`，您会看到一个相关的注释，内容如下:

> 如果`ref`被转发，则*优先于`ComponentPropsWithRef`，如果不支持 refs，则优先于`ComponentPropsWithoutRef`。*

![Note To Prefer ComponentPropsWithRef Or ComponentPropsWithoutRef](img/323e9db3fdb8b0fdb91f94f864c82fc8.png)

这正是我们所做的。现在，我们将忽略支持`ref`道具的用例，坚持使用`ComponentPropsWithoutRef`。

现在，让我们试一试解决方案！

如果您继续错误地使用这个组件，例如，将一个有效的`as`道具与其他不兼容的道具一起传递，您将会得到一个错误。

```
<Text as="div" href="www.google.com">Hello Text world</Text>

```

值`div`对于`as`属性完全有效，但是`div`不应该有`href`属性。

这是错误的，并且被 TypeScript 用错误:`Property 'href' does not exist on type ...`正确地捕捉到。

![Property Href Does Not Exist Error](img/3c9b8e54cef98ce82eb7798c79c11c19.png)

这太棒了！我们有一个更好、更强大的解决方案。

最后，确保[将其他道具向下](https://blog.logrocket.com/solving-prop-drilling-react-apps/)传递给渲染元素:

```
type TextProps<C extends React.ElementType> = {
  as?: C;
  children: React.ReactNode;
} & React.ComponentPropsWithoutRef<C>; 

export const Text = <C extends React.ElementType>({
  as,
  children,
  ...restProps, // 👈 look here
}: TextProps<C>) => {
  const Component = as || "span";

  // see restProps passed 👇
  return <Component {...restProps}>{children}</Component>;
};

```

我们继续吧。

## 处理默认`as`属性

再次考虑我们当前的解决方案:

```
export const Text = <C extends React.ElementType>({
  as,
  children,
  ...restProps
}: TextProps<C>) => {
  const Component = as || "span"; // 👈 look here

  return <Component {...restProps}>{children}</Component>;
};

```

特别是，如果省略了`as`属性，要注意缺省元素是在哪里提供的。

```
const Component = as || "span"

```

这在 JavaScript 世界中通过实现得到了恰当的体现:如果`as`是可选的，它将默认为`span`。

问题是，在`as`没有通过的情况下，TypeScript 如何处理这种情况？我们是否同样传递了一个默认类型？

答案是否定的，但下面是一个实际的例子。假设您继续使用`Text`组件，如下所示:

```
<Text>Hello Text world</Text>

```

请注意，我们在这里没有通过`as`道具。TypeScript 会知道这个组件的有效属性吗？

让我们继续添加一个`href`:

```
<Text href="https://www.google.com">Hello Text world</Text>

```

如果你继续这样做，你不会得到任何错误。那很糟糕。

一个`span`不应该接收一个`href`属性。虽然我们在实现中默认使用了一个`span`，但是 TypeScript 并不知道这个默认值。让我们用一个简单、通用的默认赋值来解决这个问题:

```
type TextProps<C extends React.ElementType> = {
  as?: C;
  children: React.ReactNode;
} & React.ComponentPropsWithoutRef<C>;

/**
* See default below. TS will treat the rendered element as a 
span and provide typings accordingly
*/
export const Text = <C extends React.ElementType = "span">({
  as,
  children,
  ...restProps
}: TextProps<C>) => {
  const Component = as || "span";
  return <Component {...restProps}>{children}</Component>;
};

```

下面突出了重要的一点:

```
<C extends React.ElementType = "span">

```

瞧啊！我们之前的例子现在应该在没有`as`道具的情况下将`href`传递给`Text`组件时抛出一个错误。

错误应为:`Property 'href' does not exist on type ...`。

![Property Href Does Not Exist On Type](img/dcb7c844cf16ae0ba17685131512b3aa.png)

## 使组件及其道具可重用

我们当前的解决方案比我们开始时的要好得多。为自己走到这一步而感到欣慰——从这里开始，事情只会变得更加有趣。

本节中要满足的用例在现实世界中非常适用。如果您正在构建某种组件，那么该组件很有可能还会接受该组件特有的一些特定道具。

我们当前的解决方案考虑了`as`、`children`以及基于`as`道具的其他组件道具。然而，如果我们想让这个组件处理自己的道具呢？

让我们来做点实际的。我们将让`Text`组件接收一个`color`道具。这里的`color`要么是彩虹的颜色，要么是`black`。

我们将继续进行，并表示如下:

```
type Rainbow =
  | "red"
  | "orange"
  | "yellow"
  | "green"
  | "blue"
  | "indigo"
  | "violet";

```

接下来，我们必须如下定义`TextProps`对象中的`color`属性:

```
type TextProps<C extends React.ElementType> = {
  as?: C;
  color?: Rainbow | "black"; // 👈 look here
  children: React.ReactNode;
} & React.ComponentPropsWithoutRef<C>;

```

在我们继续之前，让我们进行一点重构。让我们用一个`Props`对象来表示`Text`组件的实际道具，并且在`TextProps`对象中只输入我们组件特有的道具。

这将变得显而易见，您将在下面看到:

```
// new "Props" type
type Props <C extends React.ElementType> = TextProps<C>

export const Text = <C extends React.ElementType = "span">({
  as,
  children,
  ...restProps,
}: Props<C>) => {
  const Component = as || "span";
  return <Component {...restProps}>{children}</Component>;
};

```

现在我们来清理一下`TextProps`:

```
// before 
type TextProps<C extends React.ElementType> = {
  as?: C;
  color?: Rainbow | "black"; // 👈 look here
  children: React.ReactNode;
} & React.ComponentPropsWithoutRef<C>;

// after
type TextProps<C extends React.ElementType> = {
  as?: C;
  color?: Rainbow | "black";
};

```

现在，`TextProps`应该只包含特定于我们的`Text`组件的道具:`as`和`color`。

我们现在必须更新`Props`的定义，以包含我们已经从`TextProps`中移除的类型，即`children`和`React.ComponentPropsWithoutRef<C>`。

对于`children`道具，我们将利用`React.PropsWithChildren`道具。

![The PropsWithChildren Type](img/ca4f78c6569d727d8bf00f50c68fce0a.png)

很容易推理出来。您将组件道具传递给它，它将为您注入子道具定义:

```
type Props <C extends React.ElementType> = 
React.PropsWithChildren<TextProps<C>>

```

注意我们如何使用尖括号；这是传递泛型的语法。本质上，`React.PropsWithChildren`接受你的组件属性作为一个类属，并用`children`属性增加它。太棒了。

对于`React.ComponentPropsWithoutRef<C>`，我们将继续利用这里的交集类型:

```
type Props <C extends React.ElementType> = 
React.PropsWithChildren<TextProps<C>> & 
React.ComponentPropsWithoutRef<C>

```

这是完整的当前解决方案:

```
type Rainbow =
  | "red"
  | "orange"
  | "yellow"
  | "green"
  | "blue"
  | "indigo"
  | "violet";

type TextProps<C extends React.ElementType> = {
  as?: C;
  color?: Rainbow | "black";
};

type Props <C extends React.ElementType> = 
React.PropsWithChildren<TextProps<C>> & 
React.ComponentPropsWithoutRef<C>

export const Text = <C extends React.ElementType = "span">({
  as,
  children,
}: Props<C>) => {
  const Component = as || "span";
  return <Component> {children} </Component>;
};

```

我知道这些可能感觉很多，但是当你仔细看的时候就会明白了。它实际上只是把你到目前为止学到的所有东西放在一起！

完成这个必要的重构后，我们现在可以继续我们的解决方案了。我们现在所拥有的实际上是可行的。我们已经显式地输入了`color`属性，您可以如下使用它:

```
<Text color="violet">Hello world</Text>

```

## 严格省略通用组件属性

只有一件事让我不太舒服:`color`对许多 HTML 标签来说也是一个有效的属性，HTML5 之前也是如此。因此，如果我们从类型定义中移除了`color`，它将被接受为任何有效的字符串。

见下文:

```
type TextProps<C extends React.ElementType> = {
  as?: C;
  // remove color from the definition here
};

```

![Removing The Color Type Definition](img/43725560639c1f34e4a6c5aabfce7995.png)

现在，如果你继续像以前一样使用`Text`，它同样有效:

```
<Text color="violet">Hello world</Text>

```

这里唯一的区别是它是如何被输入的。`color`现在由以下定义表示:

```
color?: string | undefined

```

![The Default Color Type](img/0d7c741721e444beac1b90f7dabd70df.png)

同样，这不是我们在类型中写的定义！

这是默认的 HTML 类型，其中`color`是大多数 HTML 元素的有效属性。参见[堆栈溢出问题](https://stackoverflow.com/questions/67142430/why-color-appears-as-html-attribute-on-a-div)了解更多上下文。

### 两种可能的解决方案

现在，有两条路可以走。第一个是保留我们的初始解决方案，其中我们明确声明了`color` prop:

```
type TextProps<C extends React.ElementType> = {
  as?: C;
  color?: Rainbow | "black"; // 👈 look here
};

```

第二个选项可以说提供了更多的类型安全。为了实现这一点，您必须意识到前面的默认`color`定义来自于哪里:T1。这是在`as`类型的基础上增加的其他道具。

因此，有了这些信息，我们可以显式地从`React.ComponentPropsWithoutRef<C>`中删除组件类型中存在的任何定义。

在实际操作之前，这可能很难理解，所以让我们一步一步来。

如前所述，`React.ComponentPropsWithoutRef<C>`包含基于`as`类型的所有其他有效道具，例如`href`、`color`等。，其中这些类型都有自己的定义，例如，`color?: string | undefined`等。：

![The ComponentPropsWithoutRef Type](img/ccd21ecfeeba11a2ba2ad41d393e9f2e.png)

有可能存在于`React.ComponentPropsWithoutRef<C>`中的一些值也存在于我们的组件 props 类型定义中。在我们的例子中，`color`存在于两者之中！

![ComponentPropsWithoutRef And TextProps](img/defa8258a163827ed38c46eb62375ca1.png)

我们将显式删除组件类型定义中存在的任何类型，而不是依赖我们的`color`定义来覆盖来自`React.ComponentPropsWithoutRef<C>`的内容。

![Removing existing props from ComponentPropsWithoutRef](img/767c6a63fcf60f8dacb2885e0aa720f4.png)

因此，如果我们的组件类型定义中存在任何类型，我们将从`React.ComponentPropsWithoutRef<C>`中显式删除这些类型。

### 从`React.ComponentPropsWithoutRef<C>`中移除类型

这是我们之前看到的:

```
type Props <C extends React.ElementType> = 
React.PropsWithChildren<TextProps<C>> & 
React.ComponentPropsWithoutRef<C>

```

我们将更有选择性，而不是像交集类型那样添加来自`React.ComponentPropsWithoutRef<C>`的所有内容。我们将使用`Omit`和`keyof` TypeScript 实用程序类型来执行一些 TypeScript 魔术。

看一看:

```
// before 
type Props <C extends React.ElementType> = 
React.PropsWithChildren<TextProps<C>> & 
React.ComponentPropsWithoutRef<C>

// after
type Props <C extends React.ElementType> = 
React.PropsWithChildren<TextProps<C>> &   
Omit<React.ComponentPropsWithoutRef<C>, keyof TextProps<C>>;

```

这是重要的一点:

```
Omit<React.ComponentPropsWithoutRef<C>, keyof TextProps<C>>;

```

`Omit`接受两个泛型。第一个是对象类型，第二个是您希望从对象类型中“省略”的类型的并集。

这是我最喜欢的例子。考虑如下的`Vowel`对象类型:

```
type Vowels = {
  a: 'a',
  e: 'e',
  i: 'i',
  o: 'o',
  u: 'u'
}

```

这是键和值的对象类型。假设我想从`Vowels`中派生出一个新的类型，叫做`VowelsInOhans`。

嗯，我确实知道名字`Ohans`包含两个元音，`o`和`a`。代替手动声明这些:

```
type VowelsInOhans = {
  a: 'a',
  o: 'o'
}

```

我可以继续利用`Omit`如下:

```
type VowelsInOhans = Omit<Vowels, 'e' | 'i' | 'u'>

```

![The VowelsInOhans Type Using Omit](img/0281f33a00384e8b579fff0f7d0caa44.png)

`Omit`将从对象类型`Vowels`中“省略”掉`e`、`i`和`u`键。

另一方面， [TypeScript 的`keyof`操作符](https://blog.logrocket.com/how-to-use-keyof-operator-typescript/)可以像你想象的那样工作。想想 JavaScript 中的`Object.keys`:给定一个`object`类型，`keyof`将返回对象键的联合类型。

唷！那是一口。这里有一个例子:

```
type Vowels = {
  a: 'a',
  e: 'e',
  i: 'i',
  o: 'o',
  u: 'u'
}

type Vowel = keyof Vowels 

```

现在，`Vowel`将是`Vowels`的键的联合类型，即:

```
type Vowel = 'a' | 'e' | 'i' | 'o' | 'u'

```

如果将这些放在一起，再看一看我们的解决方案，就会发现一切都很完美:

```
Omit<React.ComponentPropsWithoutRef<C>, keyof TextProps<C>>;

```

返回我们的组件道具的键的联合类型。这又被传递到`Omit`以从`React.ComponentPropsWithoutRef<C>`中省略它们。

太棒了。🕺

最后，让我们继续将`color`道具传递给渲染元素:

```
export const Text = <C extends React.ElementType = "span">({
  as,
  color, // 👈 look here
  children,
  ...restProps
}: Props<C>) => {
  const Component = as || "span";

  // 👇 compose an inline style object
  const style = color ? { style: { color } } : {};

  // 👇 pass the inline style to the rendered element
  return (
    <Component {...restProps} {...style}>
      {children}
    </Component>
  );
};

```

## 为多态类型创建可重用的实用程序

我们终于找到了一个行之有效的解决方案。然而，现在让我们更进一步。

我们的解决方案非常适合我们的`Text`组件。然而，如果您更希望有一个可以在您选择的任何组件上重用的解决方案，以便您可以为每个用例拥有一个可重用的解决方案，那该怎么办呢？

让我们开始吧。首先，这是当前没有注释的完整解决方案:

```
type Rainbow =
  | "red"
  | "orange"
  | "yellow"
  | "green"
  | "blue"
  | "indigo"
  | "violet";

type TextProps<C extends React.ElementType> = {
  as?: C;
  color?: Rainbow | "black";
};

type Props<C extends React.ElementType> = React.PropsWithChildren<
  TextProps<C>
> &
  Omit<React.ComponentPropsWithoutRef<C>, keyof TextProps<C>>;

export const Text = <C extends React.ElementType = "span">({
  as,
  color,
  children,
  ...restProps
}: Props<C>) => {
  const Component = as || "span";

  const style = color ? { style: { color } } : {};

  return (
    <Component {...restProps} {...style}>
      {children}
    </Component>
  );
};

```

简洁实用。

如果我们使它可重复使用，那么它必须适用于任何组件。这意味着移除硬编码的`TextProps`并用一个泛型来表示它——这样任何人都可以传入他们需要的任何组件道具。

目前，我们用定义`Props<C>`来表示我们的组件道具。其中`C`表示为`as`属性传递的元素类型。

我们现在将把它改为:

```
// before
Props<C>

// after 
PolymorphicProps<C, TextProps>

```

`PolymorphicProps`代表我们将很快编写的实用程序类型。但是，请注意，它接受两个泛型类型，第二个是正在讨论的组件属性:`TextProps`。

继续定义`PolymorphicProps`类型:

```
type PolymorphicComponentProp<
  C extends React.ElementType,
  Props = {}
> = {} // 👈 empty object for now 

```

上面的定义应该是可以理解的。`C`表示`as`中传入的元素类型，`Props`是实际的组件道具，`TextProps`。

首先，让我们将之前的`TextProps`分解成以下内容:

```
type AsProp<C extends React.ElementType> = {
  as?: C;
};

type TextProps = { color?: Rainbow | "black" };

```

所以，我们已经把`AsProp`和`TextProps`分开了。平心而论，它们代表了两种不同的东西。这是一个更好的表示。

现在，让我们改变`PolymorphicComponentProp`工具定义，以包括`as`道具、组件道具和`children`道具，就像我们过去所做的那样:

```
type AsProp<C extends React.ElementType> = {
  as?: C;
};

type PolymorphicComponentProp<
  C extends React.ElementType,
  Props = {}
> = React.PropsWithChildren<Props & AsProp<C>>

```

我相信现在你已经明白这里发生了什么:我们有一个交集类型`Props`(代表组件道具)和`AsProp`代表`as`道具。这些都被传入`PropsWithChildren`来添加`children`道具定义。太棒了。

现在，我们需要包含添加了`React.ComponentPropsWithoutRef<C>`定义的位。但是，我们必须记住忽略组件定义中存在的属性。
让我们想出一个稳健的解决方案。

写出一个新的类型，只包含我们想要省略的道具。即`AsProp`的按键以及组件道具。

```
type PropsToOmit<C extends React.ElementType, P> = keyof (AsProp<C> & P);

```

还记得`keyof`实用程序类型吗？

`PropsToOmit`现在将包含我们想要省略的道具的联合类型，这是由`P`表示的组件的每个道具和由`AsProps`表示的实际多态道具`as`。

将所有这些很好地放在`PolymorphicComponentProp`定义中:

```
type AsProp<C extends React.ElementType> = {
  as?: C;
};

// before 
type PolymorphicComponentProp<
  C extends React.ElementType,
  Props = {}
> = React.PropsWithChildren<Props & AsProp<C>>

// after
type PolymorphicComponentProp<
  C extends React.ElementType,
  Props = {}
> = React.PropsWithChildren<Props & AsProp<C>> &
  Omit<React.ComponentPropsWithoutRef<C>, 
   PropsToOmit<C, Props>>;

```

这里重要的是我们添加了以下定义:

```
Omit<React.ComponentPropsWithoutRef<C>, 
   PropsToOmit<C, Props>>;

```

这基本上省略了`React.componentPropsWithoutRef`中正确的类型。你还记得`omit`如何运作吗[？](https://www.typescriptlang.org/docs/handbook/utility-types.html#omittype-keys)

虽然看起来很简单，但是您现在有了一个可以在不同项目的多个组件上重用的解决方案！

下面是完整的实现:

```
type PropsToOmit<C extends React.ElementType, P> = keyof (AsProp<C> & P);

type PolymorphicComponentProp<
  C extends React.ElementType,
  Props = {}
> = React.PropsWithChildren<Props & AsProp<C>> &
  Omit<React.ComponentPropsWithoutRef<C>, PropsToOmit<C, Props>>;

```

现在我们可以继续在我们的`Text`组件上使用`PolymorphicComponentProp`,如下所示:

```
export const Text = <C extends React.ElementType = "span">({
  as,
  color,
  children,
  // look here 👇
}: PolymorphicComponentProp<C, TextProps>) => {
  const Component = as || "span";
  const style = color ? { style: { color } } : {};
  return <Component {...style}>{children}</Component>;
};

```

多好啊！如果您构建了另一个组件，您可以像这样输入它:

```
PolymorphicComponentProp<C, MyNewComponentProps>

```

你听到那声音了吗？那是胜利的声音——你已经走了这么远！

## 多态组件中的支持引用

到目前为止，你还记得每一次提到`React.ComponentPropsWithoutRef`吗？😅组件道具… *不带*参赛。好了，现在是把裁判放进去的时候了！

这是我们解决方案的最后也是最复杂的部分。我需要你耐心等待，但我也会尽我所能详细解释每一步。

先说第一件事，你还记得[如何在](https://blog.logrocket.com/complete-guide-react-refs/)工作中做出反应吗？这里最重要的概念是，你不能将`ref`作为一个道具传递，并期望它像其他道具一样传递到你的组件中。在功能组件中处理`refs`的推荐方式是使用 [`forwardRef`函数](https://blog.logrocket.com/cleaning-up-the-dom-with-forwardref-in-react/)。

让我们从实际问题开始。

如果您现在继续传递一个`ref`到我们的`Text`组件，您将得到一个显示为`Property 'ref' does not exist on type ...`的错误。

```
// Create the ref object 
const divRef = useRef<HTMLDivElement | null>(null);
... 
// Pass the ref to the rendered Text component
<Text as="div" ref={divRef}>
  Hello Text world
</Text>

```

![Property Ref Does Not Exist Error](img/30ba977047cfb788c35c01d882c6dafd.png)

这是意料之中的。

我们支持 refs 的第一步是在如下所示的`Text`组件中使用`forwardRef`:

```
// before 
export const Text = <C extends React.ElementType = "span">({
  as,
  color,
  children,
}: PolymorphicComponentProp<C, TextProps>) => {
  ...
};

// after
import React from "react";

export const Text = React.forwardRef(
  <C extends React.ElementType = "span">({
    as,
    color,
    children,
  }: PolymorphicComponentProp<C, TextProps>) => {
    ...
  }
);

```

这本质上只是将前面的代码包装在`React.forwardRef`中，仅此而已。

现在，`React.forwardRef`有以下签名:

```
React.forwardRef((props, ref) ... )

```

本质上，收到的第二个参数是`ref`对象。让我们继续处理这个问题:

```
type PolymorphicRef<C extends React.ElementType> = unknown;

export const Text = React.forwardRef(
  <C extends React.ElementType = "span">(
    { as, color, children }: PolymorphicComponentProp<C, TextProps>,
    // 👇 look here
    ref?: PolymorphicRef<C>
  ) => {
    ...
  }
);

```

我们在这里做的是添加第二个参数，`ref`，并声明其类型为`PolymorphicRef`，现在它只是指向`unknown`。

注意`PolymorphicRef`接受泛型`C`。这类似于以前的解决方案——`div`的`ref`对象不同于`span`的对象，因此我们需要考虑传递给`as`属性的元素类型。

请注意`PolymorphicRef`类型。我们如何基于`as`道具得到`ref`对象类型？

给你点提示:`React.ComponentPropsWithRef`！

请注意，这里用 ref 表示*。没有*裁判就没有*。*

本质上，如果这是一个键包(事实上就是这样)，它将包含所有基于元素类型的相关组件属性，加上 ref 对象。

![The ComponentPropsWithRef Type](img/f8384bffe4d8c4bb4bfc836b589ca17b.png)

所以现在，如果我们知道这个对象类型包含了`ref`键，我们也可以通过下面的方法来得到这个引用类型:

```
// before 
type PolymorphicRef<C extends React.ElementType> = unknown;

// after 
type PolymorphicRef<C extends React.ElementType> =
  React.ComponentPropsWithRef<C>["ref"];

```

本质上，`React.ComponentPropsWithRef<C>`返回一个对象类型，例如，

```
{
  ref: SomeRefDefinition, 
  // ... other keys, 
  color: string 
  href: string 
  // ... etc
}

```

为了挑选出`ref`类型，我们可以这样做:

```
React.ComponentPropsWithRef<C>["ref"];

```

注意，语法类似于 JavaScript 中的属性访问器语法，即`["ref"]`。现在我们已经输入了`ref`属性，我们可以继续将它传递给呈现的元素:

```
export const Text = React.forwardRef(
  <C extends React.ElementType = "span">(
    { as, color, children }: PolymorphicComponentProp<C, TextProps>,
    ref?: PolymorphicRef<C>
  ) => {
    //...

    return (
      <Component {...style} ref={ref}> // 👈 look here
        {children}
      </Component>
    );
  }
);

```

我们已经取得了相当大的进步！事实上，如果您像我们之前一样继续检查`Text`的用法，就不会再有错误了:

```
// create the ref object 
const divRef = useRef<HTMLDivElement | null>(null);
... 
// pass ref to the rendered Text component
<Text as="div" ref={divRef}>
  Hello Text world
</Text>

```

然而，我们的解决方案仍然没有我想要的那么强类型化。让我们继续更改传递给`Text`的 ref，如下所示:

```
// create a "button" ref object 
const buttonRef = useRef<HTMLButtonElement | null>(null);
... 
// pass a button ref to a "div". NB: as = "div"
<Text as="div" ref={buttonRef}>
  Hello Text world
</Text>

```

TypeScript 应该在这里抛出一个错误，但它没有。我们正在创建一个`button` ref，但是将它传递给一个`div`元素。这是不对的。

![No Error Thrown When A Wrong Element Ref Is Passed](img/1e84a4a02287c3005831a08395564dfc.png)

如果你看一下`ref`的确切类型，它看起来是这样的:

```
React.RefAttributes<unknown>.ref?: React.Ref<unknown>

```

你看到那里的`unknown`了吗？那是打字弱的表现。理想情况下，我们应该让`HTMLDivElement`将 ref 对象显式定义为一个`div`元素 ref。

我们有工作要做。让我们首先看看`Text`组件的其他道具的类型，它们仍然引用了`PolymorphicComponentProp`类型。把这个换成一种新型的叫做`PolymorphicComponentPropWithRef`。这将只是`PolymorphicComponentProp`和 ref 道具的联合。(你猜对了。)

这是:

```
type PolymorphicComponentPropWithRef<
  C extends React.ElementType,
  Props = {}
> = PolymorphicComponentProp<C, Props> & 
{ ref?: PolymorphicRef<C> };

```

这只是前面的`PolymorphicComponentProp`和`{ ref?: PolymorphicRef<C> }`的并集。

现在我们需要改变组件的属性来引用新的`PolymorphicComponentPropWithRef`类型:

```
// before
type TextProps = { color?: Rainbow | "black" };

export const Text = React.forwardRef(
  <C extends React.ElementType = "span">(
    { as, color, children }: PolymorphicComponentProp<C, TextProps>,
    ref?: PolymorphicRef<C>
  ) => {
    ...
  }
);

// now 
type TextProps<C extends React.ElementType> = 
PolymorphicComponentPropWithRef<
  C,
  { color?: Rainbow | "black" }
>;

export const Text = React.forwardRef(
  <C extends React.ElementType = "span">(
    { as, color, children }: TextProps<C>, // 👈 look here
    ref?: PolymorphicRef<C>
  ) => {
    ...
  }
);

```

我们已经更新了`TextProps`来引用`PolymorphicComponentPropWithRef`,现在它作为`Text`组件的道具被传递。可爱！

还有最后一件事要做:为`Text`组件提供一个类型注释。它看起来类似于:

```
export const Text : TextComponent = ...

```

`TextComponent`是我们将要写的类型注释。在这里，它被完整地写出来:

```
type TextComponent = <C extends React.ElementType = "span">(
  props: TextProps<C>
) => React.ReactElement | null;

```

这本质上是一个函数组件，它接收`TextProps`并返回`React.ReactElement | null`，其中`TextProps`如前所述:

```
type TextProps<C extends React.ElementType> = 
PolymorphicComponentPropWithRef<
  C,
  { color?: Rainbow | "black" }
>;

```

这样，我们现在有了一个完整的解决方案！

我现在要分享完整的解决方案。乍一看，这似乎令人望而生畏，但是请记住，我们已经一行一行地完成了您在这里看到的所有内容。带着那份自信去读吧。

```
import React from "react";

type Rainbow =
  | "red"
  | "orange"
  | "yellow"
  | "green"
  | "blue"
  | "indigo"
  | "violet";

type AsProp<C extends React.ElementType> = {
  as?: C;
};

type PropsToOmit<C extends React.ElementType, P> = keyof (AsProp<C> & P);

// This is the first reusable type utility we built
type PolymorphicComponentProp<
  C extends React.ElementType,
  Props = {}
> = React.PropsWithChildren<Props & AsProp<C>> &
  Omit<React.ComponentPropsWithoutRef<C>, PropsToOmit<C, Props>>;

// This is a new type utitlity with ref!
type PolymorphicComponentPropWithRef<
  C extends React.ElementType,
  Props = {}
> = PolymorphicComponentProp<C, Props> & { ref?: PolymorphicRef<C> };

// This is the type for the "ref" only
type PolymorphicRef<C extends React.ElementType> =
  React.ComponentPropsWithRef<C>["ref"];

/**
* This is the updated component props using PolymorphicComponentPropWithRef
*/
type TextProps<C extends React.ElementType> = 
PolymorphicComponentPropWithRef<
  C,
  { color?: Rainbow | "black" }
>;

/**
* This is the type used in the type annotation for the component
*/
type TextComponent = <C extends React.ElementType = "span">(
  props: TextProps<C>
) => React.ReactElement | null;

export const Text: TextComponent = React.forwardRef(
  <C extends React.ElementType = "span">(
    { as, color, children }: TextProps<C>,
    ref?: PolymorphicRef<C>
  ) => {
    const Component = as || "span";

    const style = color ? { style: { color } } : {};

    return (
      <Component {...style} ref={ref}>
        {children}
      </Component>
    );
  }
);

```

这就对了。

## 结论和下一步的想法

您已经在 React with TypeScript 中成功构建了一个用于处理多态组件的健壮解决方案。我知道这不容易，但你做到了。

感谢跟随。记得启动官方的 [GitHub 库](https://github.com/ohansemmanuel/polymorphic-react-component)，在那里你会找到本指南的所有代码。如果你想和我分享你对这个教程的想法，或者只是简单地联系，你可以在 [GitHub](https://github.com/ohansemmanuel) 、 [LinkedIn](https://www.linkedin.com/in/ohans-emmanuel/) 或 [Twitter](https://twitter.com/ohansemmanuel) 上找到/关注我。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)