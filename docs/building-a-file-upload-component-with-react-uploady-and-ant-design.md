# 用 react-uploady 和 Ant Design - LogRocket Blog 构建文件上传组件

> 原文：<https://blog.logrocket.com/building-a-file-upload-component-with-react-uploady-and-ant-design/>

当涉及到任何网站的使用时，用户输入是关键，在今天的许多网站中，用户可能会因为这样或那样的原因被要求将文件上传到网站。这可以是任何内容，从新用户配置文件的图像到帮助用户在帮助论坛上排除故障所需的诊断文件。因此，让文件上传过程对用户来说简单无缝是至关重要的。您需要允许用户快速访问他们的文件选择器，选择文件，上传文件，并获得关于上传状态的反馈。这就是 [react-uploady](https://github.com/rpldy/react-uploady) 的用武之地。

react-uploady 库构建简单且可定制，允许我们根据自己的喜好创建和定制上传者的视图。使用该库的开发人员可以保持简单，即插即用，也可以定制整个上传流程。对于本文，我们将利用 [Ant Design](https://ant.design/) ，它提供了一个构建良好且可定制的 React UI 组件库，来创建一个简单而吸引人的上传组件，允许我们打开文件浏览器并挑选出要上传的文件。

## 为什么使用 react-uploady？

你可能想知道为什么开发人员可能倾向于使用 react-uploady 而不是其他文件上传库，所以让我们看看 react-uploady 具有的一些其他库没有的优势。

*   它是可定制的——为开发人员提供了几个选项来设置他们的上传程序，允许他们根据自己的喜好定制上传过程，他们可以使用原样或创建自定义 UI，他们可以显示下载进度和预览等选项
*   这个库是非常轻量级的，通过分离各个部分来减少不必要的软件包安装。例如，如果开发人员希望监控和显示上传预览或进度，他们可以单独安装与每个部分相关的包，并省去那些他们不需要的包，从而减少包的大小

## 创建我们的上传者

### 应用程序设置

首先，让我们用 [create-react-app](https://reactjs.org/docs/create-a-new-react-app.html) 引导我们的应用程序，如果您还没有安装它，您可以通过运行:

```
npm install -g create-react-app
```

现在您可以通过运行以下命令来创建您的应用程序:

```
create-react-app react-uploady-demo
```

我们有一些应用程序需要的依赖项，要安装它们，请运行以下命令:

```
yarn add @rpldy/uploady @rpldy/sender antd
```

这些软件包是:

*   **@ rpldy/uploady**–react-uploady 主包，它包含一个上传上下文提供程序以及一些挂钩，这些挂钩揭示了上传程序的功能以及客户端应用程序的其他高级特性和数据
*   **@ rpldy/sender**–react-uploady 的默认 XHR 请求发送器，我们在本例中使用它来模拟一个发送的请求，而不是建立一个实际的服务器请求。我们通过创建一个模拟增强器来做到这一点，我们将在本文的后面介绍它
*   **Ant d**–Ant 设计组件库

## 创建我们的`Uploader`组件

创建一个`Uploader.js`文件，并创建一个`Uploader`功能组件，如下所示:

```
//Uploader.js
import React, { useState, useCallback, useContext } from "react";
import "./App.css";
import Uploady, { useItemProgressListener, UploadyContext } from "@rpldy/uploady";
import { createMockSender } from "@rpldy/sender";
import { Button, Progress } from "antd";

const Uploader = () => {
  return (
    <Uploady
      destination={{ url: "http://mock-server.com" }}
      enhancer={mockEnhancer}
    >
      <div className="Uploader">
        <CustomButton />
        <br />
        <br />
        <UploadProgress />
      </div>
    </Uploady>
  );
}
const mockEnhancer = uploader => {
  const mockSender = createMockSender({ delay: 1000 });
  uploader.update({ send: mockSender.send });
  return uploader;
};

export default Uploader;
```

在上面的代码中，我们导入了来自 react-uploady 的上下文提供者`Uploady`组件。它允许我们配置我们的上传选项，如目的地以及我们可能想要添加的任何增强器。

`destination` prop 接受一个对象，该对象配置我们将上传的文件发送到的端点。在我们的例子中，我们只是提供了一个 URl，但是您可以在这里设置请求参数、方法和请求所需的头。

另一方面,`enhancer` prop 有一个增强功能，我们可以用它来改变上传者的行为。例如，您可以在这里添加一个功能，告诉上传者重试任何失败的下载。在我们的例子中，我们正在输入一个使用`createMockSender`创建的`mockEnhancer`函数，它告诉我们的上传者上传已经在 1000 毫秒后成功发送。关于增强器的更多信息，请查看关于增强器的[文档。](https://github.com/rpldy/react-uploady/blob/master/README.md#enhancer)

您还会注意到我们缺少两个组件，`CustomButton`和`UploadProgress`，现在让我们创建这两个组件。

## 创建上传按钮

我们的 upload 按钮是一个简单的功能组件，它被授权使用 React 的`useContext`钩子访问`UploadyContext`，并使用它打开本地文件浏览器来选择要发送到服务器的文件。这个过程是通过点击 Ant Design 的一个定制按钮来触发的，这个按钮调用我们在组件中定义的`handleUpload`函数。为此，我们将以下代码添加到`Uploader.js`中:

```
const CustomButton = () => {
    const uploady = useContext(UploadyContext);
    const hanldeUpload = useCallback(()=> {
            uploady.showFileUpload();
        },[uploady]);
    return <Button onClick={hanldeUpload} type="primary">Custom Upload Button</Button>
}
```

## 监控和显示上传进度

当试图创建良好的用户体验时，向用户提供关于上传过程如何进行的反馈是关键，一个好的方法是通过进度条或一些类似的 UI 显示下载的进度。在我们的例子中，我们将使用来自 Ant Design 的`Progress`组件，特别是圆形变体，这就是我们如何处理它。

将以下功能组件添加到`Uploader.js`:

```
const UploadProgress = () => {
  const [progress, setProgess] = useState(0);
  const progressData = useItemProgressListener();
  if (progressData && progressData.completed > progress) {
    setProgess(() => progressData.completed);
  }
  return (
    progressData && (
      <Progress
        type="circle"
        percent={progress}
      />
    )
  );
};
```

我们使用 react-uploady 的`useItemProgressListener`钩子从上传者那里得到当前的进度。这个钩子监听并捕获来自发送者的每次下载的进度信息，然后我们将这个进度保存到 state 中，并将其输入到`Progress`组件中，该组件将显示已经完成的上传量。当然，当没有正在进行的上传时，我们隐藏这个组件。

## 造型和包装

我们的最后一步是添加一些 CSS，以确保我们的组件被很好地放置在浏览器中，然后将我们的上传组件添加到主`App.js`文件中，以便它可以被显示。将这几行添加到`App.css`中，使组件居中并稍微隔开:

```
.Uploader{
  padding: 5rem;
  margin: 0 auto;
}
```

现在让我们用下面几行代码替换所有的 create-react-app 样板代码来更新`App.js`:

```
import './App.css';
import 'antd/dist/antd.css';
import Uploader from './Uploader'
function App() {
  return (
    <div className="App">
      <Uploader />
    </div>
  );
}
export default App;
```

就是这样！我们都准备好了！用`yarn start`启动你的应用程序，看看它有多神奇！

你可以点击查看最终应用[。](https://codesandbox.io/s/react-uploady-demo-owih6?file=/src/App.js)

## 结论和建议

有了这些信息，您就可以开始在 react 应用程序中使用 react-uploady 来创建直观的用户上传组件了。然而，这只是该软件包提供的一种上传文件的方法，该库还支持拖放输入以及从给定的 URL 上传文件。该库还支持许多发送方，如分块 XHR 请求和使用 TUS 协议。在一篇文章中深入研究以上所有方法几乎是不可能的，我建议在使用 react-uploady 时查看一下[指南](https://github.com/rpldy/react-uploady/tree/master/guides)和[文档](https://github.com/rpldy/react-uploady)。维护人员也很友好地创建了一本[故事书](https://react-uploady-storybook.netlify.app/)，里面有几个不同复杂程度的例子。

当然，你还可以采取其他措施来进一步提高上传者的 UX，比如创建可恢复的下载，以及向用户提供祝酒词反馈。希望您可以在这篇文章中获得的知识的基础上，研究这些改进以及更多。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)