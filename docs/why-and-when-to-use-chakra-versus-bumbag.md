# 为什么和什么时候使用查克拉与 Bumbag - LogRocket 博客

> 原文：<https://blog.logrocket.com/why-and-when-to-use-chakra-versus-bumbag/>

在这个不断发展 UI 组件包以实现快速前端开发的时代，无论项目的规模或范围如何，开发人员在开发我们的前端应用程序时都有许多选项可供选择。

在本文中，我们将比较和分析两个 UI 组件包: [Chakra UI](https://chakra-ui.com/) 和 [Bumbag UI](https://bumbag.style/) 。我们将看看它们的不同点、相似点和特性，这样你就可以决定在你的下一个项目中使用哪一个。让我们开始吧。

## 先决条件

对于本文，您需要 JavaScript 和前端开发的基础知识。

## 什么是查克拉 UI？

根据开发者 Segun Adebayo 的说法，“Chakra UI 是一个简单、模块化和可访问的组件库，它为您提供了构建 React 应用程序所需的所有构件。”

Chakra UI 最初是一个基于 React 的组件包，但已经扩展到许多其他前端框架，如 Vue、Nuxt、React-Native 和 Angular。

## 查克拉用户界面的特征

Chakra UI 提供了许多强大的功能，例如:

1.  易用性:在 Chakra UI 中设计布局组件很容易，这要归功于它的可定制的`Box`和`Stack`组件，你可以将它们传递给道具
2.  灵活性:Chakra UI 组件使用 React UI 原语实现灵活性和易适应性
3.  可组合:你可以很容易地从现有的 Chakra UI 组件中创建任何组件
4.  主题:您可以在任何组件的整个应用程序中快速引用主题中的值
5.  可访问性:Chakra UI 具有开箱即用的 web 可访问性支持，这意味着它严格遵循 WAI-ARIA 标准。Chakra UI 的每个组件都有正确的属性，它也有正确的键盘交互

## Chakra UI 入门

让我们学习如何开始使用 Chakra UI 进行反应。如果你正在使用 Vue，你可以在这里学习如何[设置它。](https://vue.chakra-ui.com/)

## 装置

首先，安装 Chakra UI 及其对等依赖项。

```
npm install @chakra-ui/core @emotion/core @emotion/styled emotion-theming

```

总是用来自`chakra-ul/core`的`ThemeProvider`来包装你的应用。我还建议您总是添加`CSSReset`组件来重置浏览器样式。

```
import { ThemeProvider as ChakraProvider, CSSReset as CSSDefaults } from "@chakra-ui/core"

function App({ children }) {
  return (
    <ChakraProvider>
      <CSSDefaults />
      {children}
    </ChakraProvider>
  )
}

```

现在，您可以像这样开始使用组件:

```
import { Button } from "@chakra-ui/core"

function Example() {
  return <Button>Welcome to ⚡️Chakra!</Button>
}

```

## 用查克拉用户界面建立联系方式

我们将使用 Chakra UI 创建一个联系表单，允许用户与我们联系。Chakra UI 是一个创建 HTML 表单的伟大库，因为它提供了丰富的工具来轻松开发表单。

我们将从导入创建该表单所需的必要组件开始。

```
import React from 'react';
import { ThemeProvider, CSSReset, theme, Button, Input, Textarea, FormLabel, FormControl, Heading, IconButton, Flex, Box, } from '@chakra-ui/core';

```

导入组件后，我们将用必要的颜色重置和其他外壳初始化 Chakra UI。

## 用 React 设置 Chakra UI

```
const App = () => {
  return (
    <ThemeProvider theme={theme}>
      <ColorModeProvider>
        <CSSReset />
        <ContactObj />
      </ColorModeProvider>
    </ThemeProvider>
  )
}

```

接下来，我们将创建保存表单的`ContactForm`对象。

## 创建`ContactObj`组件

```
const ContactObj = () => {
  return (
    <Flex minHeight="100vh" width="full" align="center" justifyContent="center">
      <Box
        borderWidth={1}
        px={4}
        width="full"
        maxWidth="1000px"
        borderRadius={4}
        textAlign="center"
        boxShadow="lg"
      >
        <ThemeSelector />
        <Box p={4}>
          <ContactHeader />
          <ContactForm />
        </Box>
      </Box>
    </Flex>
  )
}

```

`ContactObj`利用 Chakra UI 的`Box`组件，并包括`ContactHeader`和`ContactForm`组件来创建联系人表单。

## 创建`ContactHeader`组件

```
const ContactHeader = () => {
  return (
    <Box textAlign="center">
      <Heading>Contact Us</Heading>
    </Box>
  )
}

```

`ContactHeader`只包含联系人表单的标题代码。

## 创建`ContactForm`组件

```
const ContactForm = () => {
  return (
    <Box my={8} textAlign="left">
      <form>
        <FormControl>
          <FormLabel>Name</FormLabel>
          <Input type="text" placeholder="Enter your name" />
        </FormControl>
        <FormControl>
          <FormLabel>Email</FormLabel>
          <Input type="email" placeholder="Enter your email" />
        </FormControl>
        <FormControl>
          <FormLabel>Message</FormLabel>
          <Textarea type="textarea" placeholder="Enter your message" />
        </FormControl>
        <Button variantColor="blue" type="submit" mt={4}>
          Submit
        </Button>
      </form>
    </Box>
  )
}

```

`ContactForm`包含带有 Chakra UI 的 HTML 表单字段样式。

## 预览联系人表单

如果您成功上传了所有内容，您将看到以下联系页面:

![Chakram UI Contact Us Form](img/46da8f16b85dd48959d615c6c4a83d40.png)

## 使用查克拉的好处

*   易于使用:Chakra 将出色的开发者体验铭记于心，并且易于上手
*   风格道具:由于脉轮风格系统的本质，使用道具来覆盖和扩展风格是容易和可能的
*   组成:Chakra 将组件分解成可组合的和功能性的小单元，使其易于调试
*   黑暗模式:Chakra 支持开箱即用的黑暗模式主题，因此不需要任何代码或配置
*   Chakra 支持 Vue 和 React 框架
*   使用脉轮的缺点

## Chakra 不支持日期选择器，这是 Chakra UI 的一个显著限制

*   Chakra UI 的一些组件是不可能设计风格的，所以你只能用默认的风格来设计
*   查克拉提供了默认的主题，因此很难主题化一些组件
*   您需要额外的包装器来配置一些组件。例如，当使用悬停/激活时，您也可以使用`psuedobox`来设置样式
*   用例

## 查克拉无疑是受欢迎的，并获得了很大的牵引力。当构建需要切换主题和颜色的项目时，Chakra 是最好的选择。它还在不断发展和改进代码库，因此将来会添加更多有趣的组件和灵活性。

现在我们来看看 [Bumbag](https://bumbag.style/) 。

什么是 Bumbag UI？

## “Bumbag 是一个友好的 React UI 套件，适用于 MVP 或大规模应用程序，”开发人员 Jake Moxey 说。

用 Bumbag UI 编码仅仅意味着你在用一个设计者理解的直观的 API 编码。

Bumbag，原名 [Fannypack](https://fannypack.style/) ，始于 2018 年，最近因其在创建 React 组件套件方面的独特功能而广受欢迎。

Bumbag UI 的功能

## Bumbag UI 有一些有趣的特性，比如:

默认可访问性:BumbagUI 中的可访问性由 [Reakit](https://reakit.io/) 提供支持，所有组件都带有现成的可访问 HTML 属性和键盘交互，并遵循 WAI-ARIA 标准

1.  主题:Bumbag UI 使你能够通过在全局或组件级别改变主题来定制任何组件
2.  灵活性:使用 Bumbag 构建自己的组件、联系表单，甚至是动作调用都很简单。你可以在 Bumbag [这里](https://bumbag.style/composition/)了解更多关于构图的知识
3.  Bumbag UI 入门

## 首先，安装 Bumbag:

安装后，用`BumbagProvider`包装您的应用程序:

```
npm install bumbag

```

现在，您可以开始使用 Bumbag 组件了:

```
import { Provider as BumbagProvider } from 'bumbag';
const App = () => (
  <BumbagProvider>
    Hello world!
  </BumbagProvider>
);
```

用 Bumbag 构建联系人表单

```
import { Button, Provider as BumbagRoot } from 'bumbag';
const App = () => (
  <BumbagRoot>
    <Button>
      Welcome to Bumbag!
    </Button>
  </BumbagRoot>
);

```

## 现在，我们还将使用 Bumbag 构建一个联系表单。首先，导入必要的组件:

要设置 Bumbag，请使用以下方法:

```
import React from 'react';
import { Provider as BumbagProvider, defaultTheme, Button, Input, Textarea, Label, FieldWrapper, FieldStack, Flex, Box, } from 'bumbag';

```

现在，设置`contactObj`:

```
 const App = () => (
     <BumbagProvider isStandalone theme={defaultTheme()}>
        // ... your app
      </BumbagProvider>
  );

```

接下来，您将设置您的标题:

```
const ContactObj = () => {
  return (
    <Flex minHeight="100vh" width="full" alignX="center" alignY="center">
      <Box
        borderWidth={1}
        px={4}
        width="full"
        maxWidth="1000px"
        borderRadius={4}
        textAlign="center"
        boxShadow="lg"
      >
        <ThemeSelector />
        <Box p={4}>
          <ContactHeader />
          <ContactForm />
        </Box>
      </Box>
    </Flex>
  )
}

```

现在你应该有一个联系方式了。

```
const ContactHeader = () => {
  return (
    <Box textAlign="center">
      <Heading>Contact Us</Heading>
    </Box>
  )
}

const ContactForm = () => {
  return (
    <Box my={8} textAlign="left">
      <FieldStack>
        <FieldWrapper label="Name" placeholder="Enter your name">
          <Input />
        </FieldWrapper>
        <FieldWrapper type="email" label="Email" placeholder="Enter your email">
          <Input/>
        </FieldWrapper>
        <FieldWrapper label="Message">
          <Textarea placeholder="Enter your message"/>
        </FieldWrapper>
        <Button palette="primary">
          Submit
        </Button>
      </FieldStack>
    </Box>
  )
}

```

![Bumbag UI contact form](img/c80724ad6011d9194bd27ecb53387c04.png)

使用 Bumbag UI 的优点

## 和查克拉 UI 一样，Bumbag 也有内置黑暗模式

*   Bumbag 内置了情感，本质上是 API 风格的组件
*   Bumbag 比 Chakra 存在的时间更长，但有一个不同的名字，以前被称为 Fannypack
*   Bumbag 有 70 多个可访问的组件
*   它支持全局和组件级别的样式和主题
*   使用垃圾袋的缺点

## Bumbag 不像 Chakra UI 那样受欢迎，所以找到错误的解决方案会更加困难

*   设置黑暗模式需要比脉轮所需更多的代码
*   Bumbag 还不支持 Vue，尽管它目前正在开发中
*   用例

## 如果您正在考虑创建一个快速的[最小可行产品(MVP)](https://blog.logrocket.com/product-management/12-agile-manifesto-principles-how-to-adopt-them/#3-deliver-working-software-frequently) 或支持许多和多个组件(包括日期选择器)的大规模应用程序，那么 Bumbag UI 是一个不错的选择。

结论

## 为一个项目选择一个技术堆栈取决于许多因素，包括团队偏好和对手头项目的观点。

在本文中，我们详细了解了 Chakra UI 和 Bumbag UI，它们的功能有何不同，以及如何开始使用它们。

查克拉和 Bumbag 是类似的，可以用来完成相同的任务。那么你应该选择哪一个呢？在选择技术组合时，请考虑以下因素:

受欢迎程度:受欢迎程度并不总是选择库的最佳理由，但是产品越受欢迎，如果项目中出现问题或 bug，您可以支配的资源就越多。

1.  项目类型:当然，你应该总是考虑项目——项目的类型，规模等等。—当你决定下一个科技属性时，你正在建造一座图书馆。请确保选择完全适合您的项目类型的库。
2.  可维护性:您应该选择一个被积极维护的库，以避免在项目中引入错误或不推荐使用的代码。
3.  从上面的一般观点来看，你应该能够为你的下一个项目选择一个好的组件包。编码快乐！

使用 LogRocket 消除传统反应错误报告的噪音

## 是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)

Focus on the React bugs that matter — [try LogRocket today](https://lp.logrocket.com/blg/react-signup-issue-free).