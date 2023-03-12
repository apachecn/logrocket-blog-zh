# 在 Next.js 中使用 styled-components 和 useDarkMode - LogRocket 博客进行主题化

> 原文：<https://blog.logrocket.com/theming-in-next-js-with-styled-components-and-usedarkmode/>

除了功能性，颜色可以说是应用程序第二重要的方面。正确的配色方案有助于你为你的应用程序设置基调，并向用户展示如何与之互动。

在许多情况下，让用户选择他们喜欢的配色方案并在主题之间切换可能是一个卖点。但是你不想让你的用户被众多的主题所淹没。通常情况下，浅色和深色主题是你创建一个漂亮的用户界面所需要的。

在本教程中，我们将向您展示如何使用[s](https://styled-components.com/)[tyled-](https://styled-components.com/)[c](https://styled-components.com/)[components](https://styled-components.com/)库和 [`useDarkMode`](https://github.com/donavon/use-dark-mode) 钩子有效地实现黑暗模式和主题 Next.js 应用程序。

我们将讨论以下内容:

## 什么是黑暗模式？

深色模式是在深色背景上显示浅色文本和界面元素的任何界面的配色方案，这使得屏幕在手机、平板电脑和计算机上更容易观看。黑暗模式减少了屏幕发出的光线，同时保持可读性所需的最低颜色对比度。

浅色模式是更流行的配色方案，它在明亮的背景上显示较暗的文本。

使用黑暗模式的好处包括:

*   节能
*   减少对眼睛的压力
*   看起来很酷

## 什么是样式组件？

[styled-components](https://blog.logrocket.com/using-styled-components-in-react/) 是一个 [CSS-in-JS](https://blog.logrocket.com/5-things-you-can-do-in-css-in-js-that-you-didnt-know-about-c422fb67ceb6/) 库，它极大地改善了现代前端开发人员的开发体验，同时提供了近乎完美的用户体验。除了让开发人员能够编写组件范围的 CSS 之外，styled-components 还有许多其他好处，包括:

*   自动供应商前缀
*   每个样式组件的唯一类名
*   更容易维护样式——开发人员可以删除或修改样式，而不会影响其他组件

## 使用样式化组件进行主题化

首先使用 create-next-app 设置一个 Next.js 应用程序，然后安装 styled-components 包。

```
npm i styled-components

```

要在 Next.js 应用中使用[s](https://blog.logrocket.com/build-your-own-styled-components-library/)[tyled-](https://blog.logrocket.com/build-your-own-styled-components-library/)[c](https://blog.logrocket.com/build-your-own-styled-components-library/)[组件](https://blog.logrocket.com/build-your-own-styled-components-library/)，进入`_app.js`文件，导入`ThemeProvider`组件，用`ThemeProvider`包装应用。

接受一个主题对象作为道具，然后将该对象动态地重新暴露给组件树中任何更深层次的样式化组件。

```
import { ThemeProvider } from "styled-components"

```

接下来，创建一个`ThemeConfig.js`文件。

```
import { createGlobalStyle} from "styled-components"

export const lightTheme = {
  body: '#FFF',
  text: '#363537',
  toggleBorder: '#FFF',
  background: '#363537',
}

export const darkTheme = {
  body: '#363537',
  text: '#FAFAFA',
  toggleBorder: '#6B8096',
  background: '#999',
}

export const GlobalStyles = createGlobalStyle`
  body {
    background: ${({ theme }) => theme.body};
    color: ${({ theme }) => theme.text};
    font-family: Tahoma, Helvetica, Arial, Roboto, sans-serif;
    transition: all 0.50s linear;
  }
`

```

在这里，我们定义了`lightTheme`、`darkTheme`和`GlobalStyles`。我们需要他们稍后应用程序的主题。在`lightTheme`中，我们定义了灯光模式样式。在`darkTheme`中，我们定义了黑暗模式的样式。

我们也从样式化组件中导入 [`createGlobalStyle`](https://styled-components.com/docs/api#createglobalstyle) 。该方法生成一个 React 组件，当该组件被添加到组件树中时，会将全局样式注入到文档中。

在`GlobalStyles`中，我们定义了应用程序主体的样式。当我们从亮模式切换到暗模式时，我们希望背景颜色和文本颜色发生变化。

我们从`ThemeProvider`中获取当前活动主题的上下文，无论是`lightTheme`还是`darkTheme`，并使用该主题的样式来确定应用程序的背景颜色和文本颜色。这样，当我们从亮模式切换到暗模式时，应用程序的主题会动态变化。

现在我们已经为应用程序设置了亮暗主题，我们需要实现切换功能来在亮暗模式之间切换。

为此，在`_app.js`文件中创建一个主题状态，并设置一个`toggleTheme`函数:

```
const [theme, setTheme] = useState("light") 

const toggleTheme = () => {
    theme == 'light' ? setTheme('dark') : setTheme('light')
}

```

我们将主题状态传递给`ThemeProvider`，并根据状态在`lightTheme`和`darkTheme`之间切换。

`toggleTheme`是负责检查从`light`到`dark`的主题状态的函数。然后，我们将`toggleTheme`函数传递给一个按钮:

```
import { useState } from "react"
import { ThemeProvider } from "styled-components";

const [theme, setTheme] = useState("light") 

const toggleTheme = () => {
    theme == 'light' ? setTheme('dark') : setTheme('light')
}

return (
  <ThemeProvider>
    <button onClick={toggleTheme}>Switch Theme</button>
    <Component {...pageProps} />
  </ThemeProvider>
) 

```

到目前为止，我们已经为应用程序定义了`lightTheme`、`darkTheme`、`GlobalStyles`和切换功能。现在，让我们把所有东西都放到`_app.js`文件中:

```
import { useState } from "react"
import { ThemeProvider } from "styled-components";
import { lightTheme, darkTheme, GlobalStyles } from "../themeConfig" 

function MyApp({ Component, pageProps }) {
  const [theme, setTheme] = useState("light") 

  const toggleTheme = () => {
    theme == 'light' ? setTheme('dark') : setTheme('light')
  }

  return (
    <ThemeProvider theme={theme == 'light' ? lightTheme : darkTheme}>
      <GlobalStyles />
      <button onClick={toggleTheme}>Switch Theme</button>
      <Component {...pageProps} />
    </ThemeProvider&gt;
  ) 
}
export default MyApp

```

这样，我们就成功地使用样式化组件为 Next.js 应用程序设置了主题。

## 用`useDarkMode`钩子进行主题化

钩子是一个有用的样式选择，可以用于主题化。当与样式组件结合使用时，它使应用程序主题化变得简单而有趣。

有了`useDarkMode`钩子，我们不需要设置一个`theme`状态或者创建一个从亮到暗模式切换的`toggleTheme`函数。`useDarkMode`我们会处理好的。

要开始使用`useDarkMode`挂钩，首先安装它:

```
npm i use-dark-mode

```

接下来，创建钩子的新实例并开始使用它。我在我的`_app.js`文件中这样做了:

```
const darkmode = useDarkMode(true)

```

我们创建的`darkmode`对象有几个属性。我们需要的是它的`value`属性。这是一个布尔值，用于检查黑暗模式是否处于活动状态。

```
const theme = darkmode.value ? darkTheme : lightTheme

return (
  <ThemeProvider theme={theme}>
    <GlobalStyles />
    <button onClick={darkmode.toggle}>Switch Mode</button>
    <button onClick={darkmode.enable}>Dark Mode</button>
    <button onClick={darkmode.disable}>Light Mode</button>
    <Component {...pageProps} />
  </ThemeProvider>
) 

```

基于`darkmode`的`value`，我们在`lightTheme`和`darkTheme`之间切换，并将活动主题传递给`theme`常量。我们将这个活动主题传递到`ThemeProvider`的`theme`道具中。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

我们要设置按钮在亮暗模式之间切换，`darkmode`对象自带`enable()`、`disable()`和`toggle()`功能。

因此，我们通过将样式化组件与`useDarkMode`相结合，为我们的应用程序设定了主题。

Next.js 在服务器上呈现页面。虽然这有它的好处，但也有一个代价:SSR 不知道特定于客户端的偏好，比如`prefers-color-scheme`。这意味着当页面加载到服务器上时，最初没有选择正确的主题。这导致亮暗主题之间的“闪烁”。

为了解决这个问题，我们设置了一个`isMounted`状态，并将其设置为`false`。

```
const [isMounted, setIsMounted] = useState(false)

useEffect(() => {
  setIsMounted(true)
}, [])

return (
  <ThemeProvider theme={theme}>
    <GlobalStyles />
    ...
    {isMounted && <Component {...pageProps} />}
  </ThemeProvider>
) 

```

我们使用`useEffect`来确定应用程序何时安装，并且仅当`isMounted`为真时才显示它。这种方式消除了短暂的闪烁。

让我们把这些放在一起:

```
import { useState, useEffect } from "react"
import useDarkMode from "use-dark-mode"
import { ThemeProvider } from "styled-components";
import { lightTheme, darkTheme, GlobalStyles } from "../themeConfig"

function MyApp({ Component, pageProps }) {
  const [isMounted, setIsMounted] = useState(false)
  const darkmode = useDarkMode(true)
  const theme = darkmode.value ? darkTheme : lightTheme

  useEffect(() => {
    setIsMounted(true)
  }, [])

  return (
    <ThemeProvider theme={theme}>
      <GlobalStyles />
      <button onClick={darkmode.toggle}>Switch Mode</button>
      <button onClick={darkmode.enable}>Dark Mode</button>
      <button onClick={darkmode.disable}>Light Mode</button>
      {isMounted && <Component {...pageProps} />}
    </ThemeProvider>
  ) 
}

export default MyApp

```

## 使用`useDarkMode`和 CSS

根据你的项目的需要和要求，你可能不需要像样式组件一样使用 CSS-in-JS 库来为你的应用程序设置主题。你也可以使用 CSS 用`useDarkMode`为你的应用设置主题。

要使用 CSS 将你的应用主题化为`useDarkMode`，在你的`globals.css`文件中创建`body.light-mode`和`body.dark-mode`类。这些是被`useDarkMode`认可的特殊阶层。

```
body.dark-mode {
  color: #fff;
  background: #363537;

//dark mode styles
}

body.light-mode {
  color: #363537;
  background: #fafafa;

//light mode styles
} 

```

将您在`lightTheme`和`darkTheme`中定义的样式复制到这些类中。就是这么快这么简单。

## 结论

无论是与 CSS-in-JS 库(如 styled-components)结合使用，还是与 CSS 结合使用,`useDarkMode` Hook 都使应用程序主题化变得更加容易和快速。

当涉及到你的 Next.js 应用的主题化时，你有很多选择。

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。