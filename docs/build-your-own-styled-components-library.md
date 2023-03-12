# 构建你自己的风格组件库

> 原文：<https://blog.logrocket.com/build-your-own-styled-components-library/>

风格化组件的出现引起了轰动，改变了我们对如何创建内联风格的 React 组件的看法。

在本教程中，我们将演示如何构建自己的样式组件。这样做，您将发现样式化组件和标记模板文字是如何工作的。

我们将讨论以下内容:

这个实现的完整源代码可以在 [GitHub](https://github.com/philipszdavido/styled-components/) 上找到。

## 什么是样式组件？

styled-组件旨在消除组件和样式之间的映射，因此当您定义样式时，您实际上只是构建一个附加了样式的常规 React 组件。

您可以编写一个快速的内联样式的组件，如下所示:

```
js
const Button = styled.button`
    background-color: green;

```

这将创建一个背景颜色设置为蓝色的按钮组件(React 组件)。Button 是一个常规的 React 组件，它将呈现一个 HTML 按钮。反勾号中的样式代码将应用于 HTML 按钮。

我们可以这样使用它:

```
js
<Button>Click Me</Button>

```

所以这和写这个是一样的:

```
js
class Button extends Component {
    render() {
        return (
            <button style={{
                background-color: blue
            }}>{this.props.children}</button>
        )
    }
}

```

样式化组件的特点是一组常规的 HTML 标签，我们可以用它们来创建样式化的 React 组件版本。例如:

*   `styled.button`创建一个 React 组件来呈现一个`button`元素
*   `styled.div`创建一个 React 组件来呈现一个`div`元素
*   `styled.a`创建一个 React 组件来呈现锚`a`元素，依此类推

## 什么是标记模板文字？

样式化组件使用 JavaScript 中的`[Tagged Template Literal]()`特性来样式化组件。带标签的模板文字让您对文字的解析有更多的控制。它允许你用一个函数解析模板文字。

带标记的模板文字语法如下:

```
js
taggedFunction`string here`

```

`taggedFunction`是一个函数，反斜线包含一个字符串。`taggedFunction`看起来是这样的:

```
js
function taggedFunction(strings) {
    // ...
}

```

反斜线中的字符串将被传递给数组中的`strings`参数中的`taggedFunction`函数。值可以包含在模板文字、反勾号字符串中。

```
js
const val = 90
taggedFunction`string here ${val}`

```

`val`是模板文本中的一个值。JavaScript 将把字符串传递给`taggedFunction`，后跟文本中的值。

```
js
function taggedFunction(strings, val1) {
    // ...
}

```

`strings` param 是一个包含模板文本中的字符串的数组。`val1`参数将保存`val`值。

如果我们的标记模板文字有两个值…

```
js
const val = 90
const val2 = 900
taggedFunction`string here ${val} string2 ${val2}`

```

…那么我们的`taggedFunction`将是这样的:

```
js
function taggedFunction(strings, val1, val2) {
    // ...
}

```

*   `strings`:将包含字符串。
*   `val1`:会按住`${val}`，90。
*   `val2`:会按住`${val2}`，900。

我们可以不为这些值定义参数，而是将它们放在一个数组中，如下所示:

```
js
function taggedFunction(strings, ...vals) {
    // ...
}

```

`vals`将是一个数组，它将保存模板文本中的所有值。

有了这个…

```
js
const val = 90
const val2 = 900
taggedFunction`string here ${val} string2 ${val2}`

```

…`taggedFunction`将收到以下内容。

`strings`:

```
[ "string here ", " string2 ", "" ]

```

`vals`:

```
[ 90, 900 ]

```

请注意，JavaScript 在值出现的地方断开字符串。

```
string here ${val} string2 ${val2}

```

以上将在点`${val}`和`${val2}`处断裂。

```
string here ${val} string2 ${val2}
["string here ", "string2 ", ""]

```

现在我们可以很容易地使用插值将它们与值连接起来，并且我们知道我们将在`strings`参数中接收 CSS 代码。

```
js
styled.button`
    background-color: blue;
`

```

所以`taggedFunction`或`styled.button`后面的函数将接收到这个:

`strings`:

```
[`
    background-color: blue;
`]

```

如果 CSS 代码包含这样的值…

```
js
const mainColor = "blue";
styled.button`
    background-color: ${mainColor};
`

```

被标记的函数将收到以下内容。

`strings`:

```
[`
    background-color: `, `;`]

```

`vals`:

```
[ "blue" ]
```

## 样式组件如何工作

我们从`styled-components`导入`styled`对象:

```
js
import styled from "styled-components"

```

我们在`styled`对象中使用 HTML 标签来创建内嵌样式的组件。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
js
styled.button
styled.div

```

所以我们知道`styled`对象包含 HTML 标签作为属性，它们有函数作为它们的值，所以`styled`看起来像这样:

```
js
const styled = {
    button: function(strings, ...vals) {},
    div: function(strings, ...vals) {},
    ...
}

```

`function(strings, ...vals) {}`是被标记的函数，它将接收`strings`参数中的 CSS 样式代码和`vals`参数中的值。

```
js
const Button = styled.button
const Div = styled.div

```

上面将返回一个 React 组件。`Button`和`Div`是 React 组件，将分别呈现一个按钮和一个 div。

## 如何构建自己的风格化组件

既然我们已经了解了标记模板文字和样式组件是如何工作的，那么让我们构建自己的样式组件库。

按照以下步骤在您的计算机中搭建 Node.js 项目:

```
mkdir styled-c
cd styled-c
npm init -y
touch index.js
```

我们所有的代码都在`index.js`文件中。我们将模仿样式组件的风格。

首先，将从`react`导入`Component`。

```
js
// index.js
import React, { Component } from 'react';
```

接下来，创建一个`styled`对象和一个数组来保存 HTML 标签名。

```
js
const tags = [
    "button",
    "div"
]
const styled = {}

```

我们将使用 HTML 标记名作为属性动态填充样式化的对象，并让它调用`genComponentStyle`函数:

```
js
const tags = [
    "button",
    "div"
]
const styled = {}
tags.forEach(tag => {
    styled[tag] = genComponentStyle(tag)
})

```

`tag`是标签数组中 HTML 标签的名称。

使用上面的代码，样式化的对象将标签数组中的 HTML 标签作为属性。它们的值应该是一个函数——带标签的函数，它将接收模板文本和其中的值。每个标签都会调用`genComponentStyle`函数。`genComponentStyle`在`tag`名称上提供了一个闭包，它必须返回一个 React 组件。

要实现`genComponentStyle`功能:

```
js
function genComponentStyle(tag) {
    return function(strings, ...vals) {
        return class extends Component {
            constructor(props) {
                super(props)
                this.style = {}
            }
            componentWillMount() {
                this.style = computeStyle(this.props, strings, vals)
            }
            componentWillUpdate(props) {
                this.style = computeStyle(props, strings, vals)
            }
            render() {
                return (
                    createElement(tag, { style: this.style, ...this.props }, [...this.props.children])
                )
            }
        }        
    }
}

```

`genComponentStyle`函数返回一个带标签的函数。这个函数被分配给`styled`对象中的 HTML 标签属性，并接收模板文本和 HTML 标签上调用的值。它返回一个 React 组件。

因为函数将接收反斜杠中的 CSS 代码，所以它必须解析字符串并从中生成一个`style`对象。

我们需要转换这个:

```
`
    color: white;
    background-color: blue;
    padding: 20px;
`

```

对此:

```
js
{
    "color": "white",
    "background-color": "blue",
    "padding": "20px"
}

```

这样做是因为我们通过将样式放在一个对象中并将其传递给一个`style`道具来样式化一个 React 组件。

```
js

```

`computeStyle`函数就是这么做的。它根据`strings`和`vals`参数计算样式，并将其设置为`this.style`。然后，组件使用`createElement`函数呈现`tag`中的元素。

```
js
createElement(
    tag,
    { style: this.style, ...this.props }, [...this.props.children])

```

第一个参数是要创建的 HTML 元素。第二个参数是道具——正如您将看到的，我们有一个值为`this.style`的`style`属性。这将把`style`属性添加到 HTML 元素中，有效地用反勾号字符串计算的样式对元素进行样式化。第三个参数设置要在组件标记之间呈现的子组件。

组件有两个生命周期挂钩:`componentWillMount`和`componentWillUpdate`。
`componentWillMount`被称为初始装上的构件；它计算样式并将其分配给`this.style`。这样做是为了在元素装载到 DOM 之前计算内联样式。

内联样式也在`componentWillUpdate`上计算。这样做是为了在组件重新呈现时刷新元素的内联样式，这导致元素在发生变化时更新其样式。

`computeStyle`的实现如下:

```
js
function computeStyle(props, strings, vals) {
    strings = evalInterpolation(props, strings, vals)
    const style = {}
    strings.split(";").forEach((str)=> {
        let [prop, val] = str.trim().split(":")
        if(prop !== undefined && val !== undefined) {
            prop = prop.trim()
            val = val.trim()
            style[prop] = val
        }
    });
    return style
}

```

`computeStyle`接受`props`参数中的组件属性、`strings`参数中的模板文字以及`vals`属性中的值。它根据传递给函数的反斜线计算样式。`evalInterpolation`函数评估模板文本中的值，并返回评估后的字符串。

`computeStyle`从`;`出现的任何地方分割字符串。这样做是为了我们可以得到字符串中的每个 CSS 选择器，因为 CSS 选择器被`;`除了。然后，循环遍历它以获取每个选择器。它将选择器从`:`中分离出来，以获得选择器属性和属性值。

我们将属性及其值分别赋给`prop`和`val`。然后，我们把它们组装成一个物体`style`。完成后，返回的对象`style`带有 CSS 选择器在对象中的属性和值。

`evalInterpolation`的实现如下:

```
js
function evalInterpolation(props, strings, vals) {
    let resultStr = ""
    for (var i = 0; i < strings.length; i++) {
        var str = strings[i];
        var val
        if(vals) {
            val = vals[i]
            if(val !== undefined) {
                if(typeof val === "function") {
                    val = val(props)
                }
                str += val
            }
        }
        resultStr += str
    }
    return resultStr
}

```

该函数通过遍历字符串数组并将其与相同的值索引连接起来，对字符串值进行插值。如果这个值是一个函数，它将被 props 调用，结果将与当前字符串连接。

这使得在模板文本中使用函数成为可能:

```
js
const Button = styled.button`
    background-color: ${(props) => props.theme.bgColor};
    padding: ${props => props.small ? '2px 4px' : '6px 14px'};
`

```

该函数必须始终接受`Component`道具作为参数。

这样，我们的代码就完成了。

```
js
// index.js
import React, { createElement, Component } from 'react';
const tags = [
    "button",
    "div"
]
function evalInterpolation(props, strings, vals) {
    let resultStr = ""
    for (var i = 0; i < strings.length; i++) { var str = strings[i]; var val if(vals) { val = vals[i] if(val !== undefined) { if(typeof val === "function") { val = val(props) } str += val } } resultStr += str } return resultStr } function computeStyle(props, strings, vals) { strings = evalInterpolation(props, strings, vals) const style = {} strings.split(";").forEach((str)=> {
        let [prop, val] = str.trim().split(":")
        if(prop !== undefined && val !== undefined) {
            prop = prop.trim()
            val = val.trim()
            style[prop] = val
        }
    });
    return style
}
function genComponentStyle(tag) {
    return function(strings, ...vals) {
        return class extends Component {
            constructor(props) {
                super(props)
                this.style = {}
            }
            componentWillMount() {
                this.style = computeStyle(this.props, strings, vals)
            }
            componentWillUpdate(props) {
                this.style = computeStyle(props, strings, vals)
            }
            render() {
                return (
                    createElement(tag, { style: this.style, ...this.props }, [ ...this.props.children ])
                )
            }
        }        
    }
}
const styled = {}
tags.forEach(tag => {
    styled[tag] = genComponentStyle(tag)
})
export default styled

```

要创建样式化的按钮组件:

```
js
// test.js
import styled from "./"
const Button = styled.button`
    padding: 6px 12px;
    background: palevioletred;
    color: white;
    border: 1px solid transparent;
    border-radius: 4px;
    font-size: 16px;
    margin: 2px;
`
<button>Button</button>

```

![Styled Button Component](img/75345665b28f40e4dcd9e84572af655a.png)

要在 React 应用程序中使用样式化的按钮组件:

```
js
// App.js
import React from 'react';
import "./App.css"
import styled from "./"
const Div = styled.div`
    border: 2px solid palevioletred;
    border-radius: 3px;
    padding: 20px;
`
const Button = styled.button`
    padding: 6px 12px;
    background: palevioletred;
    color: white;
    border: 1px solid transparent;
    border-radius: 4px;
    font-size: 16px;
    margin: 2px;
`
class App extends React.Component {
    render() {
        return (
          <div>
            <button>Button1</button> 
            <button>Button2</button> 
            <button>Button3</button>
          </div>
) } } export default App

```

![Styled Button Component in a React App](img/ca1f1d3dba06bc4607cfbfab3dc56903.png)

恭喜你！您已经构建了自己版本的样式化组件。

我们的样式组件只支持`button`和`div`标签。以下是添加其他 HTML 元素的方法:

```
js
const tags = [
    "button",
    "div",
    "a",
    "input",
    "select"
]

```

## 向样式化组件添加主题化功能

styled-components 导出一个用于主题化 styled-components 的`ThemeProvider`组件。

为了给你的样式化组件添加主题功能，将一个包含你的主题的对象传递给`ThemeProvide``r`中的`theme`属性。然后，你想要主题化的样式组件将被放置在`ThemeProvider`标签之间。您引用了样式化组件 CSS 中的`props.theme`属性。

要添加`ThemeProvider`组件，使用`createContext`创建一个`Context`，并使用`Provider`组件将主题道具中的主题传递到样式化组件树中。

```
js
import React, { createElement, Component, useContext } from 'react';
const ThemeContext = React.createContext()
...
function ThemeProvider(props) {
    const outerTheme = props.theme
    const innerTheme = useContext(ThemeContext)
    const theme = { ... outerTheme, ... innerTheme }
    return (

                {props.children}

    )
}
...
export {
    ThemeProvider
}

```

我们进口了`useContext`挂钩。然后，我们使用`React.createContext()`创建了一个上下文(`ThemeContext`)。

我们的`ThemeProvider`是一个函数组件。因为它将接受道具中的主题对象，所以我们从道具对象中引用主题，并将其存储在一个`outerTheme`变量中。然后，我们使用`useContext`钩子消费我们在`ThemeContext`中的内在主题。在我们的`Context`中没有初始主题，但是我们消耗了它，所以如果我们决定在`ThemeContext`中添加一个内部主题，我们的代码不会中断。

接下来，我们将`innerTheme`和`outerTheme`合并成一个`theme`。然后，我们呈现`ThemeProvider`组件的子组件。这个子道具被包裹在`ThemeContext`中的`ThemeContext.Provider`组件之间。我们通过值属性将`theme`传递给`ThemeContext.Provider`。这将使主题对其子组件可用。

我们通过这个`{ ThemeProvider }`导出要导入的`ThemeProvider`。

现在让我们修改为每个样式化组件返回的组件，这样它将使用主题上下文(如果提供的话)。

```
js
...
function genComponentStyle(tag) {
    return function(strings, ...vals) {
        return class extends Component {
            static contextType = ThemeContext
            constructor(props, context) {
                super(props, context)
                this.style = {}
            }
            componentWillMount() {
                if(this.context)
                    this.props = { ...this.props, theme: this.context}
                this.style = computeStyle(this.props, strings, vals)
            }
            componentWillUpdate(props) {
                if(this.context)
                    props = { ...props, theme: this.context}
                this.style = computeStyle(props, strings, vals)
            }
            render() {
                let props = this.props
                if(this.context) {
                    props = { ...this.props, theme: this.context }
                    this.style = computeStyle(props, strings, vals)
                }
                return (
                    createElement(tag, { style: this.style, ...props }, [...props.children])
                )
            }
        }        
    }
}
...

```

首先，我们将静态变量`contextType`设置为`ThemeContext`。这将使主题对象传递到组件中的`ThemeProvider`可用。主题会传给`this.context`。

所以我们修改了`componentWillMount`和`componentWillUpdate`中的代码，渲染以检查`this.context`，并将其中的主题对象合并到一个带有`props`的`theme`属性中。这使得传递给 styled-components 的 props 对象中的`theme`属性可用。

就是这样——我们已经为我们的样式化组件版本添加了主题化功能。

下面是为样式化组件添加主题化功能的完整代码:

```
import React, { createElement, Component, useContext } from 'react';
const ThemeContext = React.createContext()
const tags = [
    "button",
    "div"
]
function evalInterpolation(props, strings, vals) {
    let resultStr = ""
    for (var i = 0; i < strings.length; i++) {
        var str = strings[i];
        var val
        if(vals) {
            val = vals[i]
            if(val !== undefined) {
                if(typeof val === "function") {
                    val = val(props)
                }
                str += val
            }
        }
        resultStr += str
    }
    return resultStr
}
function computeStyle(props, strings, vals) {
    strings = evalInterpolation(props, strings, vals)
    const style = {}
    strings.split(";").forEach((str)=> {
        let [prop, val] = str.trim().split(":")
        if(prop !== undefined && val !== undefined) {
            prop = prop.trim()
            val = val.trim()
            style[prop] = val
        }
    });
    return style
}
function genComponentStyle(tag) {
    return function(strings, ...vals) {
        return class extends Component {
            static contextType = ThemeContext
            constructor(props, context) {
                super(props, context)
                this.style = {}
            }
            componentWillMount() {
                if(this.context)
                    this.props = { ...this.props, theme: this.context}
                this.style = computeStyle(this.props, strings, vals)
            }
            componentWillUpdate(props) {
                if(this.context)
                    props = { ...props, theme: this.context}
                this.style = computeStyle(props, strings, vals)
            }
            render() {
                let props = this.props
                if(this.context) {
                    props = { ...this.props, theme: this.context }
                    this.style = computeStyle(props, strings, vals)
                }
                return (
                    createElement(tag, { style: this.style, ...props }, [...props.children])
                )
            }
        }        
    }
}
function ThemeProvider(props) {
    const outerTheme = props.theme
    const innerTheme = useContext(ThemeContext)
    const theme = { ... outerTheme, ... innerTheme}
    return (
        <React.Fragment>
            <ThemeContext.Provider value={theme}>
                {props.children}
            </ThemeContext.Provider>
        </React.Fragment>
    )
}
export {
    ThemeProvider
}
const styled = {}
tags.forEach(tag => {
    styled[tag] = genComponentStyle(tag)
})
export default styled

```

要对一组样式化组件进行主题化:

```
import React from 'react';
import styled, { ThemeProvider } from "./styled.js"
const Div = styled.div`
    border-radius: 3px;
    border: 2px solid ${props => props.theme.bgColor};
    padding: 20px;
`
const Button = styled.button`
    padding: 6px 12px;
    background: ${(props) => props.theme.bgColor};
    color: white;
    border: 1px solid transparent;
    border-radius: 4px;
    font-size: 16px;
    margin: 2px;
`
export default class ThemeStyled extends React.Component {
    constructor() {
        super()
        this.state = {
            theme: {
                bgColor: "violet"
            }
        }
    }
    setTheme(bgColor) {
        this.setState({...this.state.theme, theme: { bgColor }})
    }
    render() {
        return (
            <ThemeProvider theme={this.state.theme}>
                <Div>
                    <Button onClick={()=> this.setTheme("red")}>Set Theme(Red)</Button>
                    <Button onClick={()=> this.setTheme("green")}>Set Theme(Green)</Button>
                    <Button onClick={()=> this.setTheme("violet")}>Set Theme Default</Button>
                </Div>
            </ThemeProvider>
        )
    }
}

```

我们维护一个主题状态，将`bgColor`属性设置为 violet。我们有一个`Div`和`Button`样式的组件。我们有由`theme`对象中的`bgColor`设置的`Div`组件的边框颜色。另外，`Button`组件的背景颜色由`theme.bgColor`设置。
我们渲染一个`Div`和三个`Button`:`"S``et Theme(Red``)"`、`"S``et Theme(Green``)"`、`"S``et Theme(Default``)"`。

当单击这些按钮中的任何一个时，它们都会改变状态对象的`bgColor`属性。`"Set Theme(Red)"`将`bgColor`更改为红色，这将`Div`边框颜色以及按钮的背景颜色更改为红色。同样，`"Set Theme(Green)"`和`"Set Theme(Default)"`按钮将分别把主题颜色改为绿色和紫色(默认颜色)。

## 满怀信心地接近风格化组件

如您所见，理解样式化组件的工作方式实际上非常容易。您甚至可以创建自己的样式组件。

关于样式化组件的大部分混淆源于标记模板文字特性。但是现在您也理解了带标签的模板文字是如何工作的了。

有了关于螺母和螺栓如何工作的更完整的图片，您应该能够充满信心和相对轻松地使用样式化的组件——甚至创建自己的组件。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.