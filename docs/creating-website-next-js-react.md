# 用 Next.js 和 React - LogRocket 博客创建网站

> 原文：<https://blog.logrocket.com/creating-website-next-js-react/>

***编者按:**本帖更新于 2021 年 10 月。*

[Next.js](https://nextjs.org) 是一个广泛使用的框架，用于构建 [React](https://reactjs.org) 应用程序，这些应用程序提供服务器端渲染、自动代码分割、静态导出选项和简单的产品构建。

它还减轻了许多与创建生产就绪的 React 应用程序相关的常见问题。

在本教程中，我们将向您展示如何引导 Next.js web 应用程序，并添加一些典型网站的基本组件和功能。

通过这个过程，您将了解如何使用这个框架。

我们开始吧！

## 使用 Next.js 和 React 创建网站的先决条件

在开始阅读本指南之前，您需要具备以下条件:

*   Node.js 安装在本地开发机器上。

可以按照 [Node.js 官方下载页面](https://nodejs.org/en/download/)上的说明安装 Node.js。

## 步骤 1 —引导 Next.js 应用程序

有两种方法可以引导 Next.js 应用程序。第一种是使用`create-next-app`命令。像许多流行的框架一样，Next.js 现在有了自己的命令，可以快速轻松地建立一个 Next.js 项目。

首先，我们需要打开一个新的终端窗口(Linux 上的`Ctrl+Alt+T`或 Mac 上的`Command+Space`)并运行:

```
npx [email protected]
# or
yarn create next-app
```

注意:如果我们想要一个 TypeScript 项目，我们需要在末尾添加一个`--typescript`标志(即`yarn create next-app --typescript`)。

该命令将提示我们输入项目名称。一旦完成，我们的新项目将可访问。我们可以通过运行以下命令来运行 Next.js 项目:

```
cd your-project

npm run dev
# or
yarn dev
```

打开你最喜欢的浏览器，访问`[http://localhost:3000](http://localhost:3000)`。我们应该看到“欢迎来到 Next.js！”显示在我们的屏幕上。

创建 Next.js 项目的第二种方法是手动完成。首先，我们需要首先创建一个项目目录，并使用 npm ( [Node.js 包管理器](https://www.npmjs.com))安装所需的依赖项。

打开一个新的终端窗口(Linux 上的`Ctrl+Alt+T`或 Mac 上的`Command+Space`)并执行下面的命令来创建一个新的项目文件夹，该文件夹将存放您的 Next.js 应用程序(用您的项目名称替换“your-project ”):

```
mkdir your-project
```

将`cd`添加到您的新目录:

```
cd your-project
```

接下来，运行这个命令在项目的根目录下创建一个`package.json`文件:

```
npm init -y
# or
yarn init -y

```

该命令在项目目录的根目录下创建一个`package.json`文件。

`-y`标志用默认值初始化文件。

`package.json`文件将允许您在项目中轻松安装和使用 npm 包依赖项。如果你将来想这样做的话，它还会使与其他开发者共享你的项目变得更加容易。

如果你想了解更多关于`package.json`文件的内容，请查阅 [npm 文档](https://docs.npmjs.com/files/package.json)。

现在我们已经创建了一个`package.json`文件，我们可以为您的 Next.js 网站安装所需的 npm 包依赖项了。

首先，我们需要[下一个](https://www.npmjs.com/package/next)、[反应](https://www.npmjs.com/package/react)和[反应-dom](https://www.npmjs.com/package/react-dom) npm 包。

您可以使用以下命令一次安装所有这些组件:

```
npm install next react react-dom
# or 
yarn add next react react-dom
```

当这些完成安装后，您会注意到在您的项目中创建了一个新的`node_modules`目录。

该目录存储项目的所有已安装的依赖项。

如果您查看内部，您会注意到您安装的三个 npm 包及其所有子依赖项都在那里。

将来当您与他人共享您的代码时，该列表中的所有包都将在应用程序的初始设置中或在 install 命令运行时安装。

现在我们已经安装了您的依赖项，我们需要一种方法来启动您的应用程序。

打开您的`package.json`文件，用以下代码替换“脚本”部分:

```
[label package.json]

"scripts": {
  "dev": "next dev"
},
```

`"dev"`脚本用于在开发模式下运行应用程序。

这意味着您的代码将运行特殊的错误处理、热重载和其他特性，使开发过程更加愉快。

稍后，我们将在这一部分添加更多的脚本来处理应用程序的生产版本。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

在您的终端中，使用以下命令以开发模式启动应用程序:

```
npm run dev
```

当您执行该命令时，您会看到一个错误:

```
[label Output]

Couldn't find a `pages` directory. Please create one under the project root
```

Next.js 寻找一个包含你的网站的所有不同路径的`/pages`目录，当它没有找到时抛出一个错误。

为了修复错误并让您的网站运行，我们需要创建一个名为`/pages`的新目录，并向其中添加一个 Next.js 可以呈现的页面。

首先，在项目的根目录下创建一个`/pages`目录:

```
mkdir pages
```

使用以下命令将`cd`添加到其中:

```
cd pages
```

然后，添加一个名为`index.js`的新文件:

```
touch index.js
```

我们需要向您的`/pages/index.js`文件添加一些代码，以便让 Next.js 呈现一些内容。
在您最喜欢的文本编辑器中打开`/pages/index.js`,并将以下代码添加到文件中:

```
[label /pages/index.js]

import React, { Component } from 'react'

export default class extends Component {
  render () {
    return (
      <div>Your Next.js App</div>
    )
  }
}
```

上面的代码创建了一个 React 类组件，并用`export default`将其导出。

保存对文件的更改，并使用以下命令重新启动应用程序:

```
npm run dev
```

打开你最喜欢的浏览器，访问`[http://localhost:3000](http://localhost:3000)`。

您应该会看到显示的文本“Your Next.js App”。

恭喜你，你现在已经创建了一个工作的 Next.js 网站！

## 步骤 2–了解页面文件夹

`/pages`目录将保存你网站的所有页面，而`index.js`文件将作为你在`/` URL 路径的主页。当您的网站被访问时，`/pages`目录中每个文件的名称将与浏览器中的 URL 路径相匹配。

例如，路径为`/pages/articles.js`的文件将有一个在浏览器中显示为`/articles`的 URL。所有这些都由 Next.js 自动处理。

唯一的例外是`index.js`文件，它们的 URL 将以它们所在目录的根目录命名。例如，`/pages/index.js`文件在`/`路径下作为主页，`/pages/articles/index.js`会变成`/pages/articles`。

## 步骤 3 —从外部 API 检索和显示数据

Next.js 很酷的一点是它提供的[服务器端渲染](https://blog.logrocket.com/ssg-vs-ssr-in-next-js/)特性。在网页加载之前，您可以从外部 API 获取和接收数据。

为了证明这一点，我们将使用 NASA 的公共 API 来显示来自他们的[天文图片](https://apod.nasa.gov/apod/astropix.html)项目的数据。

他们每天都发布一张宇宙的新照片，并附有一位天文学家写的简要说明。

我们将使用一个名为[同构解锁](https://www.npmjs.com/package/isomorphic-unfetch)的 npm 包发出请求。这个包非常适合 Next.js，因为它可以在客户机和服务器环境下工作。

运行此命令在您的项目中安装 npm 软件包:

```
npm install isomorphic-unfetch # or
yarn add isomorphic-unfetch

```

然后，重新打开您的`/pages/index.js`文件，并用以下代码替换其内容:

```
[label /pages/index.js]

import React, { Component } from 'react'
import fetch from 'isomorphic-unfetch'

export default class extends Component {
  render () {
    return (
      <div>
        <div>
          {this.props.title}
        </div>
        <div>
          <img src={this.props.imageUrl} />
        </div>
      </div>
    )
  }
}

export async function getStaticProps() {
  const res = await fetch(
    "https://api.nasa.gov/planetary/apod?api_key=DEMO_KEY"
  );
  const data = await res.json();

  return {
    props: {
      title: data.title,
      imageUrl: data.url,
    },
  };
}

```

您会注意到，我们在 React 组件的顶部添加了一个名为`getStaticProps()`的新异步函数。

这是一个内部 Next.js 函数，允许您在加载页面之前获取数据并通过其 props 填充 React 组件。

`getStaticProps()`函数中的代码在服务器上运行，其结果被发送回调用它的页面。

在您的`getStaticProps()`函数中，我们首先向 NASA REST API 端点请求当天的**天文图片**，并将数据响应转换为我们可以处理的对象。

使用这些数据，我们用`title`和`imageUrl`数据填充 React 组件的 props。

然后，我们为这两个项目添加了一些`<div>`元素，并使用`this.props` React 方法用数据填充它们。

保存文件并在浏览器中查看更改。

您应该会看到页面上显示的标题和当天的图像。

## 步骤 4 —为生产构建应用程序

Next.js 使部署过程变得简单而轻松。如果您手工构建了 Next.js 项目，那么您需要将“构建”和“启动”脚本添加到`package.json`文件中。

打开您的`package.json`文件，使“脚本”部分看起来像这样:

```
[label package.json]

"scripts": {
  "dev": "next dev",
  "build": "next build",
  "start": "next start"
},
```

“构建”脚本会将您的代码编译成服务器和浏览器代码，这些代码将在生产环境中的服务器上运行。“启动”脚本是您如何在生产环境中启动生产代码。

要构建应用程序，请运行命令:

```
npm run build
```

完成运行需要一些时间。

完成后，请注意创建了一个名为`/.next`的新目录。Next.js 向该目录添加了许多不同的目录和文件。

对于本教程，您只需要知道它是您在开发模式下运行的代码的优化版本。要在生产模式下启动应用程序并运行`/.next`目录中的代码，请运行以下命令:

```
npm start
```

网站的生产就绪版本现在应该在`[http://localhost:3000](http://localhost:3000)`运行。

## 结论

现在，您已经使用 Next.js 和 React 创建了一个网站。您现在应该能够:

*   引导新的 Next.js 应用程序
*   在 Next.js 应用程序中创建新页面
*   从外部 API 获取数据并显示在 Next.js 页面上
*   在生产环境中构建并运行 Next.js 应用程序

我们在本教程中建立的网站可以大大扩展。

您可以使用 CSS 添加定制样式，将更多页面添加到`/pages`目录，从您或其他人构建的另一个 API 中获取数据，和/或将应用程序部署到服务器并使其可供世界访问。

这些只是增强 Next.js 应用程序的几个例子。

祝您探索愉快，编码愉快！

## [LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。

## [LogRocket](https://lp.logrocket.com/blg/nextjs-signup) :全面了解生产 Next.js 应用

调试下一个应用程序可能会很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪状态、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/nextjs-signup)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png)](https://lp.logrocket.com/blg/nextjs-signup)[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/nextjs-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你的 Next.js 应用上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用程序的性能，报告客户端 CPU 负载、客户端内存使用等指标。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

让您调试 Next.js 应用的方式现代化— [开始免费监控](https://lp.logrocket.com/blg/nextjs-signup)。