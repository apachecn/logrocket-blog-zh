# 风格化组件与情感处理 CSS 

> 原文：<https://blog.logrocket.com/styled-components-vs-emotion-for-handling-css/>

## 介绍

随着 JavaScript 在过去几年中经历了许多调整和改进，我们已经看到了几个在 JavaScript 代码中处理 CSS 的新库。样式组件(styled-components)和[情感(Emotion)](https://github.com/emotion-js/emotion)是两个主要的 CSS-in-JS 库，开发人员在设计流行的 JavaScript 框架(如 React)中的组件时通常会用到它们。

在本文中，我们将比较风格化组件和情感，包括差异、缺点和好处。

## 概观

### **样式**–**组件**

样式化组件库允许开发人员创建新的样式化 react 组件和重新样式化现有 react 组件。`Props`是样式化组件的[主要功能元素](https://styled-components.com/docs/basics#passed-props)，尤其是对象样式的变化。

在我看来，styled-components 的最大优点是，只要它接受一个`className`属性，你就可以使用这个特定的库来设计任何组件的样式。您还可以使用`withComponent`函数助手来改变附加到特定组件的渲染标签。如果您希望以后在不同的组件中重用特定组件的某些样式，这可能是需要的。在这种情况下，您必须更改呈现的标记来执行操作。

### **情感**

情感的使用与样式化组件的使用非常不同。这个库的主要特点是，对于使用 JavaScript 编写不同的 CSS 样式，样式组合是可预测的。该库支持字符串和对象样式。

一般来说，使用情感有两种主要方法:框架无关和 with React。每种方法都有优点和缺点，我们将在下面讨论。

#### **与框架无关的**

与其他类型的样式化方法不同，框架无关的方法不需要外部插件或额外的设置。Emotion 有一个可靠的支持系统——不管是什么框架——它能熟练处理自动供应商前缀、嵌套选择器和其他功能。

此外，使用与框架无关的方法，很容易生成不同的类名，因为您可以使用最简单的 CSS 函数来这样做。

#### **反应方法**

带着感情使用 React 方法是创建独特和非常规的[内联设计作品](https://blog.logrocket.com/the-best-react-inline-style-libraries-comparing-radium-aphrodite-emotion-849ef148c473/)的最佳方法，并且在可配置的构建环境中应用时效果最佳。当您使用这种方法时，您可以获得专门的 CSS prop 支持，确保增强的样式选项，以及零配置的服务器端呈现。

## 将风格成分与情感相比较

当谈到为 JavaScript 编写 CSS 时，styled-components 和 Emotion 都非常高效，性能良好，并且有一个专门的开发人员基础，他们将每个库用于特定的目的。

在接下来的几节中，我们将看看使用每个库的不同原因，以帮助您决定哪一个适合您。

### **你为什么会选择样式组件？**

样式化组件是 React 组件的简化版本，这使得向样式化组件传递属性变得相当容易。这个特性使得使用样式化组件编写 CSS 的过程更加容易。您还可以通过样式化组件重新设计现有常规组件的样式和主题。

对于执行主题化任务，样式化组件是一个很好的选择，它支持许多定制的外观、主题和感觉。样式化组件还允许适用于所有类型的样式化组件的全局样式化。当您[创建一个适用于所有组件的全局样式](https://blog.logrocket.com/build-your-own-styled-components-library/)时，您可以在同时处理不同样式的组件时节省时间和精力。

除了上面列出的特性，样式化组件可以很容易地从一个 HTML 组件切换到另一个 HTML 组件。就 CSS 编写而言，这种灵活性使这个库比其他方法更好。例如，一旦某些 HTML 元素由样式化组件呈现，开发人员就可以向这些 HTML 元素添加额外的属性。

最后，styled-components 与几乎所有 CSS 框架兼容，可以帮助您满足所有的主题化和样式化需求。

### 为什么要用情感来写 CSS？

总的来说，Emotion 比其他库对开发者更友好。Emotion 的最大优势是它易于处理的对象样式，可以用来编写 CSS。

例如，以样式化组件为例，开发人员必须为不同的组件创建唯一的名称，同时避免相同的命名样式。如果您未能遵循独特的风格，或者您在没有有意义的描述符的情况下命名一个组件，那么您所有的努力都可能在几秒钟内付诸东流。另一方面，命名任务在情感上相当简单，因为它们依赖于 CSS 道具的应用。

最后，许多开发人员更喜欢 Emotion，而不是其他 CSS-in-JS 替代方案，因为它体积小、性能高、整体灵活。

## 不同的造型选项，带有不同风格的组件和情感

### **使用** **s** tyled 组件进行造型

我们将从查看一些样式组件的示例代码开始:

```
import styled from '@emotion/styled'

const Button = styled.button`
  color: turquoise;
`

render(<Button>This my button component.</Button>)

```

请注意，您可以通过实现 props 来更改组件的样式，或者创建新的样式:

```
import styled from '@emotion/styled'

const Button = styled.button`
  color: ${props =>
    props.primary ? 'hotpink' : 'turquoise'};
`

const Container = styled.div(props => ({
  display: 'flex',
  flexDirection: props.column && 'column'
}))

render(
  <Container column>
    <Button>This is a regular button.</Button>
    <Button primary>This is a primary button.</Button>
  </Container>
)

```

在下面的例子中，我们可以使用`className`道具来创建样式:

```
import styled from '@emotion/styled'
const Basic = ({ className }) => (
  <div className={className}>Some text</div>
)

const Fancy = styled(Basic)`
  color: hotpink;
`

render(<Fancy />)

```

最后，我们可以更改样式化组件中的呈现标签。在下面的代码块中，请注意第二个组件如何与 Section 具有相同的样式，但呈现为旁白:

```
import styled from '@emotion/styled'

const Section = styled.section`
  background: #333;
  color: #fff;
`
const Aside = Section.withComponent('aside')
render(
  <div>
    <Section>This is a section</Section>
    <Aside>This is an aside</Aside>
  </div>
)

```

### **使用情感进行框架无关的造型**

由于 Emotion 简化了组件的样式，与框架无关的方法非常简单:

```
import { css, cx } from '@emotion/css'

const color = 'white'

render(
  <div
    className={css`
      padding: 32px;
      background-color: hotpink;
      font-size: 24px;
      border-radius: 4px;
      &:hover {
        color: ${color};
      }
    `}
  >
    Hover to change color.
  </div>
)

```

### **使用情感来设计 React 中组件的样式**

该方法在反应上同样简单:

```
// this comment tells babel to convert jsx to calls to a function called jsx instead of React.createElement
/** @jsx jsx */
import { css, jsx } from '@emotion/react'

const color = 'white'

render(
  <div
    css={css`
      padding: 32px;
      background-color: hotpink;
      font-size: 24px;
      border-radius: 4px;
      &:hover {
        color: ${color};
      }
    `}
  >
    Hover to change color.
  </div>
)

```

## 结论

对于简单、高效和不复杂的样式，Emotion 是一个很好的 CSS-to-JS 库。另一方面，对于更独特和复杂的样式选项，样式化组件可能是更好的选择。正如编写 CSS 时经常遇到的情况一样，大部分决策过程都取决于项目设置和个人偏好。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)