# JavaScript 中 React 应用程序的样式技巧

> 原文：<https://blog.logrocket.com/tips-for-styling-react-apps-in-javascript-6838a9d289c8/>

# 用 JavaScript 设计 React 应用程序的技巧

## 

2017 年 3 月 7 日 1 分钟阅读 433

![](img/05e4d4fa5bb0486f7467c65900547418.png)

虽然许多开发人员仍然使用 CSS 来设计他们的 React 应用程序，但用 JavaScript 编写样式已经成为一种越来越流行的做法，因为它提供了 CSS 预处理程序的所有便利，而不需要学习新的语言。

大多数 CSS-in-JS 库都涉及到创建一个 JavaScript 样式对象。作为一个例子，这是一个样式对象使用我喜欢的库 Aphrodite 时的样子。

```
const styles = StyleSheet.create({
  container: {
    position: 'relative',
    width: 40,
    height: 50,
  },
)}
```

#### 变量

使用 JavaScript 样式时，SASS/LESS 工作流中的许多最佳实践仍然适用。颜色、字体和其他常量可以存储为变量，其用法如下:

```
// colors
const YELLOW = '#FFF83C';
// fonts
const BOLD = 700;
const TIMES_NEW_ROMAN = '"Times New Roman", Georgia, Serif';
// shared constants
const border = `1px solid ${YELLOW}`;
const styles = StyleSheet.create({
  textBox: {
    color: YELLOW,
    fontFamily: TIMES_NEW_ROMAN,
    fontWeight: BOLD,
    border, 
  },
)}
```

新的 ES6 语言特性对于干净地共享样式来说非常方便。在上面的例子中，`border`是用对象文字速记符号引用的。

#### 使用 JS 对象代替 mixins

在定义常用的样式序列时(在本例中，规则需要向溢出的文本添加省略号)，spread 运算符(…)特别有用。

```
const textEllipsis = {
  whiteSpace: 'nowrap',
  textOverflow: 'ellipsis',
  overflow: 'hidden',
  width: '100%',
};
const styles = StyleSheet.create({
  textBox: {
    ...textEllipsis,
    backgroundColor: 'gray',
  },
)}
```

#### 为更复杂的主题创建辅助功能

更复杂的规则序列可以抽象在辅助函数后面。例如，在对占位符文本进行样式化时，我使用了一个返回样式对象的函数:

```
const getPlaceholderStyles = obj => ({
  '::-webkit-input-placeholder': obj,
  '::-moz-placeholder': obj,
  ':-ms-input-placeholder': obj,
  ':-moz-placeholder': obj,
  ':placeholder': obj,
});
const styles = StyleSheet.create({
  input: getPlaceholderStyles({ color: RED }),
)}
```

#### 对命名方案保持一致

用 JavaScript 编写风格的最大优点之一是类名默认作用于每个组件。这意味着像 BEM 这样的约定是不必要的。尽管如此，与类名的命名方案保持一致还是很有帮助的。例如，我使用`container`作为每个 React 组件的最外层类名。

#### 查看很酷的 JS 库

有很多有助于创建 JavaScript 样式的库。我特别喜欢的一个叫做`color`，它处理常见的颜色操作:

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)

```
import Color from 'color';
const styles = StyleSheet.create({
  button: {
    backgroundColor: RED,
    ':hover': {
      backgroundColor: new Color(RED).lighten(0.2).string(),
    },
  },
)}
```

#### 有用的资源

* * *