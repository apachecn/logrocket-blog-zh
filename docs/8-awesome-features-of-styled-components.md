# 风格化组件的 8 个令人敬畏的特性

> 原文：<https://blog.logrocket.com/8-awesome-features-of-styled-components/>

styled-components 是全世界 CSS-in-JS 库中的领先框架。它提供了一种简单而优雅的方式来实现 React 项目中的内联样式组件。

styled-components 有一些很多开发者不知道的很酷的特性。在本指南中，我们将描述其中的一些功能，并向您展示如何使用它们来美化您的下一个项目。

## 1.风格传承

我们可以通过简单地将样式组件传递给`styled`函数来继承它的样式。

```
import styled from "styled-components"

const Div = styled.div`
    padding: 10px;
    color: palevioletred;
`

```

这里我们有一个`Div`样式的组件。让我们创建另一个 div 元素来继承这个组件的样式。

```
const InheritedDiv = styled(Div)`
    border: 1px solid palevioletred;
`

```

这个`InheritedDiv`将具有`Div`组件的样式以及它自己的样式。

```
padding: 10px;
color: palevioletred;
border: 1px solid palevioletred;

```

## 2.传递道具

可以将 Props 传递给样式化的组件，就像它们传递给常规的 React 组件(类或函数)一样。这是可能的，因为样式化组件实际上是 React 组件。

```
const Button = styled.button`
    padding: 2px 5px;
    color: white;
    border-radius: 3px;
`

const Div = styled.div`
    padding: 10px;
    color: palevioletred;
    border: 1px solid palevioletred;
`

```

styled-components 创建一个 React 组件，该组件呈现与 styled 对象中的属性相对应的 HTML 标记。

`Button`将创建并呈现一个按钮 HTML 标签，而`Div`将创建并呈现一个 div 标签。它们是组件，所以我们可以向它们传递道具。

```
<Button color="black">Click Me</Button>
<Div borderColor="green"></Div>

```

这将使样式化组件将包含颜色的道具传递给`Button`组件，将包含`borderColor`的道具传递给`Div`组件。然后，我们可以通过使用一个函数来获取带标签的模板文本中的属性。

```
const Button = styled.button`
    padding: 2px 5px;
    color: ${props => props.color ? props.color : "white"};
    border-radius: 3px;
`

const Div = styled.div`
    padding: 10px;
    color: palevioletred;
    border: 1px solid ${props => props.borderColor ? props.borderColor : "palevioletred"};
`

```

标记模板文字中的函数将接收一个`props` args，它是传递给组件的`props`。这使我们能够引用传递给`Button`和`Div`组件的`color`和`borderColor`，这使得样式化组件的样式是动态的。

## 3.主题

styled-components 提供了主题化功能，使您能够支持多种外观和感觉。

为此，我们将使用`ThemeProvider`组件。

```
import { ThemeProvider } from "styled-components"

```

让我们设置一个主题对象来保存我们想要应用于样式化组件的 CSS 样式。

```
const theme = {
    boderColor: "green",
    color: "green",
    bgColor: "green"
}

```

主题对象保存了`border-color`、`color`和`bgColor`的颜色。

现在，我们有两个组件:`Button`和`Div`。让我们把主题用在他们身上。

```
const Button = styled.button`
    padding: 2px 5px;
    color: ${props => props.theme.color};
    border-radius: 3px;
`

const Div = styled.div`
    padding: 10px;
    color: ${props => props.theme.color};
    border: 1px solid ${props => props.theme.borderColor};
`

```

如您所见，他们正在访问道具中的主题属性。`ThemeProvider`将主题对象作为道具传递给组件。

最后，我们将渲染`ThemeProvider`标签之间的`Div`和`Button`组件，并将主题对象传递给`ThemeProvider`中的`theme`道具。

```
<ThemeProvider theme={theme}>
    <Div>
        <Button>Click Me</Button>
    </Div>
</ThemeProvider>

```

主题对象将在他们的道具中提供给`ThemeProvider`的孩子。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

所以现在，如果我们改变主题对象中的任何属性值，`ThemeProvider`将把改变传递给子对象，`Div`和`Button`将相应地改变它们的样式。

## 4.全局样式

到目前为止，我们所做的大多数样式都是特定于组件的。styled-components 框架还使您能够创建应用于所有样式化组件的全局样式。

首先，导入`createGlobalStyle`。

```
import { createGlobalStyle } from "styled-components/macro"

```

创建一个`globalStyles.js`文件。

```
// globalStyles.js

export default createGlobalStyle`
    html {
        margin: 0;
    }

    body {
        margin: 0;
    }
`

```

接下来，将其导入到`App`组件中。

```
import GlobalStyle from "./globalStyles"

function App() {
    return (
        <GlobalStyle />
        <AppTree />
    )
}

```

`GlobalStyle`在应用程序树的其余部分之前首先呈现。这将把全局样式应用到应用程序树中的所有组件。

## 5.切换组件类型

样式组件本质上是动态的。他们可以从创建和呈现一个 HTML 元素变成另一个。

```
const Button = styled.button`
    padding: 2px 5px;
    color: ${props => props.theme.color};
    border-radius: 3px;
`

```

组件将创建并呈现一个按钮元素。在呈现`Button`组件时，我们可以通过将`as`属性传递给它，并使用我们希望它更改的 HTML 标签名称来更改这一点。

```
<Button as="a">Click Me</Button>

```

这将创建并呈现一个锚元素。`as="a"`将它从呈现按钮元素改为呈现锚元素。

这也可以使用`withComponent`方法来完成。

```
const Button = styled.button`
    padding: 2px 5px;
    color: palevioletred;
    border-radius: 3px;
`

const Link = Button.withComponent("a")

```

`Link`是一个样式化的组件，它将呈现应用了`Button`CSS 样式的`a`锚元素。

## 6.设计常规组件的样式

通过用组件调用`styled()`函数，然后用包含样式代码的模板文字，可以将常规组件转换成样式组件。

```
function Button(props) {
    return (
        <button className={props.className}>
            {props.children}
        </button>
    )
}

```

这里我们有一个呈现按钮元素的`Button`组件。请注意，我们为按钮元素设置了一个`className`属性，并将其值指定为来自`props.className`。这是为了将继承的样式应用于按钮元素。

要将该组件转换成样式化的组件，请将其传递给`styled()`函数。

```
Button = styled(Button)`
    padding: 2px 5px;
    border-radius: 3px;
    border: 1px solid palevioletred;
`

```

这将使用模板文本中的 CSS 样式来样式化`Button`组件中的按钮元素。将使用下面的 CSS 代码呈现一个按钮元素。

```
padding: 2px 5px;
border-radius: 3px;
border: 1px solid palevioletred;

```

## 7.指定属性

您可以向由样式化组件呈现的 HTML 元素添加属性。

例如，您可以像这样创建一个`Input`组件:

```
const Input = styled.input`
    font-size: 14px;
    padding: 2px 5px;
    border: 1px solid green;
`

```

`Input`将元素渲染并输入。输入元素有不同的类型，包括:

*   `text`
*   `number`
*   `password`
*   `email`

这些是通过使用`type`属性在输入元素中指定的。要告诉样式化组件您想要的输入元素的类型，使用`attrs`方法。

```
const Input = styled.input.attrs({
    type: "text"
})`
    font-size: 14px;
    padding: 2px 5px;
    border: 1px solid green;
`

```

这将创建一个类型为`text`的输入元素。我们还可以向样式化组件添加其他属性。

```
const Input = styled.input.attrs({
    type: "text",
    placeholder: "Type anything here..."
})`
    font-size: 14px;
    padding: 2px 5px;
    border: 1px solid green;
`

const PasswordInput = styled.input.attrs({
    type: "password",
    placeholder: "Type your password here..."
})`
    font-size: 14px;
    padding: 2px 5px;
    border: 1px solid green;
`

```

## 8.与其他 CSS 框架一起使用

最后，您可以在任何 CSS 框架中使用样式化组件。

例如，让我们用引导样式创建一个`Button`组件。

```
const PrimaryButton = styled.button.attrs({
    className: "btn btn-prmiary"
})`
    outline: none;
`

```

我们使用了`attrs`方法向带有`btn btn-primary`值的组件添加一个`className`属性。这将使 Bootstrap 将其样式应用于组件。

其他 CSS 框架也是如此。

```
const MatButton = styled.button.attrs({
    className: "mat-button"
})`
    outline: none;
`

```

以上示例是一个材质设计样式的组件。

## 结论

除了我们在这里讨论的，还有许多特性被嵌入到样式组件中，但是这八个技巧和窍门将会帮助你从这个动态库中获得最大的收益。