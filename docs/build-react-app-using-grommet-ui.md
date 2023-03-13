# 使用 Grommet 为 UI 构建一个 React 应用程序

> 原文：<https://blog.logrocket.com/build-react-app-using-grommet-ui/>

构建 React 应用程序可能是一个复杂且耗时的过程，尤其是在创建视觉上吸引人且直观的用户界面时。这就是使用像 [Grommet](https://v2.grommet.io) 这样的 UI 组件库的价值所在。

UI 组件库是预先构建和预先设计的 UI 组件的集合，可以很容易地集成到前端应用程序中。在构建使用 JavaScript 前端框架的应用程序时，利用 UI 组件库的重要性在于它能够节省时间和精力。

在本文中，我们将逐步完成建立一个新的 React 项目和集成 Grommet 的过程，为应用程序的 UI 提供一个完美的用户友好的设计。

*向前跳转:*

# 什么是 Grommet UI？

Grommet(有时也称为 Grommet UI)是一个现代设计系统和 UI 组件库，用于构建美观、直观和可访问的应用程序。它建立在 React JavaScript 库的基础上，提供了一致的、响应性强的设计语言和各种预构建和预样式的组件，可以轻松集成到 React 应用程序中。

Grommet 还包括诸如可访问性支持、主题化和国际化等特性，使其成为构建用户友好且可访问的应用程序的强大工具。

# 索环与脉轮 UI

Grommet 和 Chakra UI 都是流行的 React UI 框架，它们为构建用户界面提供了一组预构建和预样式化的 UI 组件。

这两个框架的主要区别之一是它们的设计语言。索环的设计是一个现代和极简的设计系统，具有干净和简单的美感。相比之下， [Chakra UI 被设计得更加灵活和可定制](https://blog.logrocket.com/advanced-techniques-chakra-ui/)，有广泛的主题和设计风格可供选择。

另一个区别是每个框架提供的组件和特性的范围。Grommet 提供了广泛的组件，包括按钮、表单、图标、菜单等等。相比之下，Chakra UI 提供了更有限的组件范围，但包括独特的选项，如模态和抽屉。

在性能和可访问性方面，Chakra UI 和 Grommet 都经过了很好的优化，并为可访问性标准提供了很好的支持。然而，Chakra UI 使用 [Emotion](https://emotion.sh/docs/introduction) CSS-in-JS 库来设计其组件的样式，与 Grommet 的内联样式方法相比，这会导致更大的包大小和更慢的性能。

总的来说，Chakra UI 和 Grommet 都是构建 React 应用程序的强大而有用的工具。这两者之间的选择将取决于开发人员的特定需求和偏好，例如所需的设计语言、组件和功能的范围以及性能和可访问性要求。

# 带 React 的索环教程

让我们探索使用 Grommet 的预构建和预设计的 UI 组件创建一个简单注册页面的过程。

要开始，您需要在计算机上安装 Node.js 和 npm。如果你还没有这些工具，可以从 [Node.js 网站](https://nodejs.org/en/)下载安装。

### 设置 React 应用程序

安装 Node.js 和 npm 后，可以使用以下命令创建一个新的 React 项目:

```
// terminal command
npx create-react-app grommet-demo

```

这将创建一个新目录`grommet-demo`，其中包含 React 项目所需的所有文件和依赖项。

现在，导航到新目录并启动开发服务器，如下所示:

```
// terminal command
cd my-app
npm start

```

这将启动开发服务器并打开您的默认 web 浏览器到 [http://localhost:3000](http://localhost:3000/) ，在这里您将看到默认的 React 欢迎页面。

既然您已经建立了一个基本的 React 应用程序，让我们清理 React 应用程序，因为我们有许多样板代码。

这项任务很简单。只需删除不在下图中的每个文件，然后清除剩余文件中的所有导入语句:

![React App Grommet Demo](img/390fbaf4a1d36b598c5b7a5ef60c5a8a.png)

您的样式表应该是这样的:

```
html, body{
      margin:0;
      padding:0;
}

```

在上面的代码中，我们只是对整个网页的边距和填充属性进行了简单的重置。

### 安装索环及其附件

接下来，您可以通过运行以下命令将索环添加到项目中:

```
// terminal command
npm install grommet grommet-icon

```

这将安装索环库、其依赖项和索环 SVG 图标集合。安装完成后，您可以通过在应用程序的入口点文件(通常为`index.js`)的顶部添加以下代码行，将索环组件和样式导入 React 应用程序:

```
// JavaScript [React]
import { Grommet } from 'grommet';

```

然后，用`Grommet`组件包装整个应用程序，就像这样:

```
// JavaScript [React]

import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App';
import { Grommet } from 'grommet';

const theme = {
  global: {
    font:{
      family: "Roboto",
      size:"18px"
    },
  },
}
const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <React.StrictMode>
    <Grommet theme={theme}>
      <App />
    </Grommet>
  </React.StrictMode>
);

```

这将使您能够在 React 应用程序中访问索环组件和样式。然后，您可以使用这些组件为您的应用程序创建一个支持 Grommet 的 UI。

注意被声明然后作为道具传递给`Grommet`组件的`theme`对象。我们将使用它来定制我们的索环用户界面。主题对象中声明的值将传播到下面的所有注释中。

### 构建和设计用户界面

假设您想用 Grommet 创建一个简单的登录表单。您可以使用`<Form>`和`<FormField>`组件创建表单，使用`<Button>`组件创建提交按钮。

您的代码可能如下所示:

```
// JavaScript [React]
import { Box, Form, FormField, Button } from 'grommet';

function App() {
  return (
      <Box width="medium" margin="auto">
        <Form>
          <FormField label="Username" name="username" />
          <FormField label="Password" name="password" type="password" />
          <Button type="submit" primary label="Submit" />
        </Form>
      </Box>
  );
}

export default App;

```

这段代码将创建一个带有用户名和密码字段以及提交按钮的注册表单。由于有了`<Box>`组件，表单将在页面上居中，宽度适中。
Grommet 还提供了广泛的可定制主题选项，可用于改变表单的外观和感觉。

在本教程中，我们将只使用索环构建一个带有旋转木马的注册页面。这是我们最终项目的样子:

![React App UI Built with Grommet](img/aada0519aca7bbafd1532c9314a2b606.png)

对于这个演示，我们将从一个组件`app.js`构建整个应用程序；除了来自索环的组件，我们不会编写或导入任何其他组件。

首先，打开`app.js`文件，清除该组件返回的当前代码。您的`App`组件应该如下所示:

```
function App() {
  return (
    <div className="App">
    </div>
  );
}

```

接下来，从`app.js`文件顶部的 Grommet 导入您需要的所有内容，就像这样:

```
import './main.css';
import { Box, Carousel, Image, Heading, Text, TextInput, FormField, Button } from 'grommet';
import { Google } from 'grommet-icons'

function App() {
  return (
    <div className="App">
    </div>
  );
}

```

现在，让我们设置注册页面布局。正如你在上面的视频中看到的，我们的页面有两列布局。左栏保存转盘，右栏保存注册表单。

为了实现这一点，我们将使用`<Box>`组件。这只是对 CSS [flexbox](https://developer.mozilla.org/en-US/docs/Learn/CSS/CSS_layout/Flexbox) 的抽象；我们可以通过在它上面设置不同的道具来定义我们的布局。在使用索环时，您很可能会非常频繁地使用`<Box>`组件。

这是更新后的应用程序:

```
import './main.css';
import { Box, Carousel, Image, Heading, Text, TextInput, FormField, Button } from 'grommet';
import { Google } from 'grommet-icons'

function App() {
  return (
    <div className="App">
      <Box direction="row" height="100vh">
        <Box width="60%"></Box>
        <Box width="40%" height="100vh" pad="xlarge" background="light-3" justify="center"></Box>
      </Box>
    </div>
  );
}

```

现在，让我们添加旋转木马。注意在上面的代码中，有两个内部的`<Box>`组件。让我们将旋转木马添加到第一个组件中:

```
import './main.css';
import { Box, Carousel, Image, Heading, Text, TextInput, FormField, Button } from 'grommet';
import { Google } from 'grommet-icons'

function App() {
  return (
    <div className="App">
      <Box direction="row" height="100vh">
        <Box width="60%">
          <Carousel controls={false} play={3000} height="100vh">
            <Image fit="cover" src="https://images.unsplash.com/photo-1668063497279-7ecb0f0ffa8a?ixlib=rb-4.0.3&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=774&q=80" />
            <Image fit="cover" src="https://images.unsplash.com/photo-1639332793139-32eed37bdc99?ixlib=rb-4.0.3&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=774&q=80" />
            <Image fit="cover" src="https://images.unsplash.com/photo-1655742260938-82ab000acc2d?ixlib=rb-4.0.3&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=1740&q=80" />
          </Carousel>
        </Box>

     <Box width="40%" height="100vh" pad="xlarge" background="light-3" justify="center"></Box>
     </Box>
   </div>
  );
}

```

旋转木马有三个滑道。我们使用带有三个`<``Image` `>`子组件的`<``Carousel``>`组件实现了这一点。

最后一步是添加注册表单。这是应用程序组件的最终外观:

```
import './main.css';
import { Box, Carousel, Image, Heading, Text, TextInput, FormField, Button } from 'grommet';
import { Google } from 'grommet-icons'
function App() {
  return (
    <div className="App">
      <Box
        direction='row'
        height="100vh"
      >
        <Box width="60%" >
          <Carousel controls={false} play={3000} height="100vh" >
            <Image fit="cover" src="https://images.unsplash.com/photo-1668063497279-7ecb0f0ffa8a?ixlib=rb-4.0.3&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=774&q=80" />
            <Image fit="cover" src="https://images.unsplash.com/photo-1639332793139-32eed37bdc99?ixlib=rb-4.0.3&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=774&q=80" />
            <Image fit="cover" src="https://images.unsplash.com/photo-1655742260938-82ab000acc2d?ixlib=rb-4.0.3&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=1740&q=80" />
          </Carousel>
        </Box>
        <Box width="40%" height="100vh"  pad="xlarge" background="light-3" justify='center'>
          <Box width="80%" fill="horizontal" align='center' >
            <Box as="span" fill="horizontal">
              <Heading
                responsive={true}
                textAlign="start"
                size="xsmall"
                margin="none"
                a11yTitle="Create an account"
              >Create an account</Heading>
              <Text
                textAlign="start"
                size="14px"
                margin={{
                  vertical: "10px"
                }}
                a11yTitle="Let's get started with your 30 days free trial"
              >Let's get started with your 30 days free trial</Text>
            </Box>
            <Box margin={{
              vertical: "2em"
            }}
              fill="horizontal"
            >
              <FormField
                margin={{
                  vertical: ".5em"
                }}
              >
                <TextInput placeholder="Name" size="14px" a11yTitle ="Enter your name"/>
              </FormField>
              <FormField
                margin={{
                  vertical: ".5em"
                }}
              >
                <TextInput placeholder="Email" type="email" size="14px" a11yTitle="Enter your email address" />
              </FormField>
              <FormField
                margin={{
                  top: ".5em",
                  bottom:"2em"
                }}

              >
                <TextInput placeholder="Password" type="password" size="14px" a11yTitle="Enter your password" />
              </FormField>
              <Button 
              primary 
              label="Create account"
                margin={{
                  vertical: ".5em",
                }}
                color="#101727"
                style={{padding:".5em", borderRadius:"8px", fontSize:"14px"}}
                a11yTitle="Create account button"
              />
              <Button 
              icon={ <Google />}  
              label="Sign up with Google"
                a11yTitle="Sign up with Google button"
                style={{ padding: ".5em", borderRadius: "8px", fontSize: "14px", borderColor:"#AEAFB860" }}
                margin={{
                  bottom: "1.5em",
                }}
              />
              <Box as ="span" >
                <Text size='14px' color="#AEAFB8" textAlign='center'>Already have an account? <Button href="/" style={{ fontWeight: "900", color: "#01010180" }} a11yTitle="Log in if you already have an account">Log in</Button></Text>
              </Box>
            </Box>
          </Box>
        </Box>
      </Box>
    </div>
  );
}
export default App;

```

如果您回头参考上面的注册页面视频，您会看到这段代码创建了一个注册表单，其中包含以下 UI 组件:

*   **父框**:父框`Box`有浅色背景，边缘有一些填充；它占据了屏幕宽度的 40%和高度的 100%
*   **内盒**:内盒`Box`位于母盒`Box`内；它占据了父`Box`宽度的 80%,并水平伸展以填充空间。内层`Box`包含一个`Heading`和一段`Text`，都与`Box`的`start`对齐
*   **表单域**:内层`Box`的`Text`下面是另一个`box`，有三个`FormField`，供用户输入姓名、邮箱、密码。`FormField`之间有一些垂直的空白来创造空间
*   **按钮**:在`FormField`的下方是两个按钮。当点击“创建账户”`Button`时，表单将被提交。“注册谷歌”`Button`有一个来自索环图标库的谷歌图标；它允许用户使用他们的谷歌账户注册。
*   **登录文本**:在表单下方是一段`Text`文字，告诉用户如果他们已经有了账户，就可以登录。这个`Text`与`Box`的中心对齐，并链接到登录页面

### 使用户界面可访问

索环被设计成可访问的，并符合[网页内容可访问性指南(WCAG](https://www.w3.org/WAI/standards-guidelines/wcag/) )的要求。注意我们的`<TextI``nput``>`和`<Button>`组件有一个`a11yTitle`道具。这有助于辅助技术，如屏幕阅读器，理解和浏览内容。此外，Grommet 使用语义 HTML 标签为页面上的内容提供上下文和含义，进一步提高了其可访问性。

这里有一个[链接](https://github.com/nelsonmic/grommet-logrocket-demo)到 GitHub 上的完整项目。

## 使用索环的好处

索环是一个强大的前端工具。以下是在 React 中使用 Grommet 构建 UI 的一些好处:

*   **易于使用和定制**:索环提供了广泛的预建用户界面组件，可根据您的应用需求轻松定制
*   **响应式设计**:索环自动调整您的应用布局以适应不同的屏幕尺寸，确保您的应用在所有设备上看起来都很棒
*   **可访问性** : Grommet 遵循可访问性的最佳实践，使构建残疾用户可访问的应用程序变得容易
*   **移动优先方法**:索环拥有移动优先的设计理念，这意味着它优先考虑移动设备上的设计和用户体验
*   **与 React 的集成** : Grommet 构建在 React 之上，因此它与 React 应用程序无缝集成，并且对于熟悉 React 的开发人员来说易于使用

## 结论

索环提供了一个强大而有效的方法来创建一个现代的，直观的，和可访问的用户界面。Grommet 提供了广泛的预构建和预样式的组件，可以轻松地集成到 React 应用程序中，还提供了主题化、国际化和可访问性支持等功能。

通过遵循本文中概述的步骤，您可以快速、轻松地用 Grommet 建立一个 React 项目，并开始为您的应用程序构建一个用户界面。无论您是经验丰富的 React 开发人员还是前端开发领域的新手，Grommet 都是一个有价值的工具，可以帮助您创建专业且用户友好的应用程序。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)