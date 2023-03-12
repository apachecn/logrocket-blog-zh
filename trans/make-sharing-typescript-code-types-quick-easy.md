# 让共享类型脚本代码和类型变得快速而简单

> 原文：<https://blog.logrocket.com/make-sharing-typescript-code-types-quick-easy/>

在我以前的生活中，我用 C++和后来的 C#编写，并通过将代码编译成“库文件”来共享代码，然后我和我的团队可以将它复制到其他项目中。像这样共享代码库是很重要的，这样我们就不会为新项目一次又一次地重写或复制公共代码。

在 TypeScript 中这样做似乎应该很简单。我们只是编译成 JavaScript(我们可以把它看作是我们的“库文件”的新版本)，然后把 JavaScript 代码复制到其他项目中。

然而在实践中，事情并不那么简单。根据定义，复杂的应用程序——就像我们现在用 TypeScript 构建的现代应用程序——由多个相互作用的部分组成。

你有没有在 TypeScript 中编码时看到过这样的错误信息？

```
File 'X’ is not under 'rootDir' 'Y'. 'rootDir' is expected to contain all source files. ts(6059)
```

这意味着您正试图从当前项目之外导入代码。最有可能的是，您只是试图从一个项目到另一个项目共享代码！

但是，我们如何在项目之间共享代码库呢？

一种方法是创建单独的代码库，通过功能来分解我们的应用程序，允许我们在项目之间重用最常用和最有用的代码。请参见图 1 中的表示。

![Figure 1: Sharing code libraries between projects](img/73eef3dbcd0e7dc86d3da8d1c4aa3707.png)

Figure 1: Sharing code libraries between projects. The folder image is from [OpenClipArt](https://openclipart.org/detail/257145/folder)

事实上，在 TypeScript v3.0 之前，还没有一种优雅的方式来做到这一点，但是我们很快就会看到这一点。

这篇博客文章展示了我寻找一种类似的在 TypeScript 中共享代码库的轻量级方法的最新结果。在这篇文章中，我们将先解决这个问题，然后再讨论更高级的共享 TypeScript 代码的方法，比如微服务和 Docker 映像之间的共享，或者后端和前端之间的共享。

我们可以从中获得很多价值，即使是在一个应用程序中。当应用程序结构的性质被分成组件(例如，微服务、后端/前端、微前端等)时。)，我们可以很容易地看到在单个应用程序中共享公共功能和数据结构的需要。

如果你想在应用程序组件之间[干燥你的代码](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)，这是你必须知道的。

## 审查标准方法

在处理示例代码之前，让我们简单回顾一下 JavaScript 社区中共享代码的标准方式。

最常见的方法是将我们的代码发布到 npm 注册表中，这样它就可以作为一个依赖项安装在我们想要使用它的任何其他项目中。

另一种方法是将我们的代码发布到 Git 仓库(不一定是 GitHub，但通常是 GitHub ),然后[使用 npm 从那里直接安装它](https://remarkablemark.org/blog/2016/09/19/npm-install-from-github/)。

请记住，无论是发布到 npm 注册中心还是发布到 GitHub，都可以公开或私下完成。当然，公开发布是默认的，但是当在非开源的代码库上工作时，您也可以私下发布。

这些最常见的 JavaScript 发布方法是相关的，因为它们也是发布类型脚本代码的最常见方式。(如果你需要这方面的快速复习，可以看看这个[出版指南](https://cameronnokes.com/blog/the-30-second-guide-to-publishing-a-typescript-package-to-npm/)。)

我们通常很少直接发布类型脚本代码，而不是将其编译成 JavaScript 并发布。然而，对于许多项目来说，发布共享代码库似乎有些矫枉过正。

首先，这是快速开发的巨大障碍，其次，发布一个我只用于少数微服务的库，或者当我想在 REST API 的任何一端重用数据模型时，这似乎是一种浪费。真的，它通常不值得发布，除非你想与世界分享你的数据模型。

### 标准方法的局限性

在日常编码过程中，我对代码库进行频繁的迭代修改。(当你必须以这种方式处理 npm 包时，确保你使用的是 npm-link 。)这些变化中的一些将被证明是不必要的或不好的(例如，它们破坏了一些东西)，但是我们需要自由地试验我们的代码，然后当它不起作用时退出它。最基本的是，我们可以实验和测试我们的工作，而不需要先提交和发布它。

在 npm 上发布代码库之前，我通常会问自己以下问题:

*   该库是为在任何其他项目中独立使用而设计的吗？
*   我想和其他人分享吗？
*   这个库的代码是开源的还是应该开源？

尽管如此，在其他一些情况下，发布到 npm 可能会拖累有效的开发——这就是我们需要找到共享代码的替代方法的地方。

## 探索共享类型脚本代码的替代方法

因此，在上文中，我们为开发共享代码的替代方法建立了以下准则:

*   在复杂的项目中，我们希望利用共享的代码库，这样我们就可以在应用程序的组件之间重用代码
*   视情况而定，将代码发布到 Git 存储库或 npm 可能是不必要的，甚至会阻碍我们工作流的速度

记住这一点，让我们继续讨论主要问题:寻找在我们的项目之间共享 TypeScript 代码库的替代方法。

## 入门指南

在这篇博文中，我们将从几个例子中浏览代码。

[第一个例子](#example-one)是一个基本但必要的起点。你需要[安装 Node.js](https://nodejs.org/en/download/) 来运行它。

[第二个例子](#example-two)更高级，向您展示了如何将代码库共享到基于 Docker 的微服务中。你需要[安装 Docker](https://docs.docker.com/get-docker/) 来运行它。

[第三个也是最后一个例子](#example-three)展示了如何在基于 Docker 的微服务和用 React、TypeScript 和 Parcel 构建的前端之间共享代码。您需要安装 Docker 和 Node.js 来完整地构建和运行这个示例，但是如果您只想构建前端，您只需要 Node.js。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

代码可以在我的 GitHub 上找到[，如果你愿意，你可以自己克隆代码:](https://github.com/ashleydavis/sharing-typescript-code-libraries)

```
git clone [email protected]:ashleydavis/sharing-typescript-code-libraries.git
```

或者[下载 zip 文件](https://github.com/ashleydavis/sharing-typescript-code-libraries/archive/refs/heads/main.zip)并解压到您的本地计算机。

我下面介绍的方法允许灵活的项目结构。你可以将这些技术与[单一回购](https://en.wikipedia.org/wiki/Monorepo)一起使用，或者在[使用元工具](https://github.com/mateodelnorte/meta)时使用元回购。如果出于某种奇怪的原因，您不使用版本控制，那么您不必使用任何 repo 相反，您可以在本地计算机上使用这些技术和特定的目录结构。

这些例子遵循特定的布局，但这并不重要。您可以根据自己的喜好和需求来排列文件和文件夹。

最后，这里介绍的方法也是可伸缩的。您可以向每个示例添加更多的共享代码库，并且可以添加更多的主项目(例如，每个微服务一个项目)。

## 示例 TypeScript 项目引用入门

如果您已经了解如何使用 TypeScript 项目引用，请随意[跳过本节](#example-two)。

[TypeScript 项目引用](https://www.typescriptlang.org/docs/handbook/project-references.html)其实挺新的！它们从 2018 年发布的 TypeScript v3.0 开始才可用。根据文档，[项目参考](https://blog.logrocket.com/boost-your-productivity-with-typescript-project-references/)允许你将你的 TypeScript 程序组织成更小的片段，这有助于改善构建时间，加强组件之间的逻辑分离，并以新的更好的方式组织你的代码。

先说最基本的例子:a node . js“Hello，World！”演示如何将共享的 TypeScript 代码库包含到 Node.js 项目中的程序。这相当简单，但是对于更高级的示例来说，这是一个必要的构建块。

这个例子只使用了一个共享库，但是您可以通过将`references`添加到您的`tsconfig.json`文件中来添加更多的库。每个被引用的项目必须是有效的 TypeScript 项目，并且有自己的`tsconfig.json`文件。

下面的图 2 解释了示例项目的结构。如果你克隆了[这个项目](https://github.com/ashleydavis/sharing-typescript-code-libraries)，你可以在 GitHub 或者你的本地计算机上自由探索它。

![Figure 2: Sharing TypeScript libraries, a simple Node.js example](img/54a3061e9478d718f334cebc935dc15f.png)

Figure 2: Using a shared TypeScript library in a Node.js project

共享库的代码如下面的清单 1 所示。上面印着“你好，世界！”到控制台。

**清单 1:共享库中的代码**

```
export function showMessage(): void {
   console.log("Hello world!n");
}
```

主项目的代码从共享库中导入`showMessage`函数并调用它，如清单 2 所示。

**清单 2:主项目使用共享库中的代码**

```
import { showMessage } from "../../libs/my-library";
showMessage();
```

就像我之前说的，这个例子再简单不过了。

有趣的部分是如何在主项目的`tsconfig.json`文件中为其配置项目引用。清单 3 中显示了一段摘录。

**清单 3:摘自主项目的`tsconfig.json`**

```
"references": [
   {
      "path": "../libs/my-library"
   }
]
```

在清单 3 中，我们为其他 TypeScript 项目定义了一个数组`references`。这将我们的主项目连接到它的共享库。

那么我们如何构建 Node.js 示例项目呢？

首先，打开一个终端窗口，导航到主 repo 的目录，然后进入 Node.js 示例:

```
cd sharing-typescript-code-libraries/nodejs-example
```

现在，向下导航到主项目:

```
cd my-project
```

我们现在可以构建项目了。

对于普通的 TypeScript 项目，我们将调用 TypeScript 编译器，如下所示:

```
npx tsc
```

但是，现在我们正在使用 TypeScript 项目引用，我们必须在末尾添加`--build`参数:

```
npx tsc --build
```

`--build`参数使 TypeScript 编译器从`tsconfig.json`中读取`references`字段。然后，在生成主项目之前，它会生成每个引用的项目。

就是这样。在最基本的层面上，没有更多的。

这是一个巨大的时间节省！这意味着我们不必为每个共享代码库分别调用`npx` `tsc`，更重要的是，这意味着我们永远不会忘记在更改代码后构建一个代码库——这将为我们节省大量时间，因为我们不知道为什么我们的代码更改没有通过主项目。

作为我个人惯例的一部分，我将此封装在一个名为`build`的 npm 脚本中。如果您想看看它是什么样子，请访问`[package.json](https://github.com/ashleydavis/sharing-typescript-code-libraries/blob/main/nodejs-example/my-project/package.json)`文件。这样做意味着我可以为任何项目调用`npm run build` ，并且它会自动转换为带有共享库的 TypeScript 项目的`npx tsc --build`。我觉得这是一个很好的接触，因为这意味着我不必每次都记得添加`--build`。

这种共享方法的好处是它高度可扩展。我们可以将它扩展到更多的共享代码库，我们需要做的就是编译主项目。

## 示例 2:在微服务和 Docker 映像之间共享代码库

让我们考虑一个更高级的例子。假设我们正在创建一个微服务应用程序，其中每个微服务都是从 Docker 映像部署的。我们已经编译了希望在微服务之间共享的代码，我们需要将它烘焙到每个 Docker 映像中。

虽然这将更加高级，但是这里的示例代码与上一个示例基本相同，只是这次我们将在 Docker 构建过程中编译我们的主项目和共享库。然后，我们将编译后的代码打包并复制到我们的生产 Docker 映像中。

图 3 解释了这个项目的结构。

![Figure 3: Sharing TypeScript libraries, the microservices example](img/a536f6533a6d98ea4a06f778a5b0e2c5.png)

Figure 3: Sharing code between microservices

这个示例项目的重要部分是我们用来构建 Docker 图像的 Docker 文件。

图 4 是 Dockerfile 文件的注释版本，突出了最重要的部分。

![Figure 4: Annotated Docker file that can share TypeScript code libraries](img/46d45e726c9617392d44ec338e92724c.png)

Figure 4: Annotated Dockerfile that can share TypeScript code libraries

注意这里我们是如何复制整个根项目的。这将我们的共享库和主项目的代码复制到 Docker 映像的构建阶段。

接下来，我们调用`npm run build`将 TypeScript 编译成 JavaScript 并绑定结果。然后，我们使用[递归安装](https://www.npmjs.com/package/recursive-install)工具为共享库和主项目安装仅生产依赖项。

最后，为了生成产品 Docker 映像，我们必须将编译后的代码包从构建阶段复制到最终映像中。最终的图像应该省略 TypeScript 源代码和开发依赖，这在生产中是不必要的。只有编译后的 JavaScript 代码和生产依赖项会被复制。

因此，我们可以说，生产映像是精简的，而不是充斥着不必要的开发、调试和测试碎片。

还有一个问题。我们到底如何捆绑编译好的 JavaScript 代码？

你没有错过。它其实就藏在`npm run build`里面。您可以在清单 4 中看到这是如何工作的，清单 4 是主项目的`package.json`文件的摘录。

**清单 4:来自 package.json 的摘录**

```
"scripts": {
    "build": "tsc --build && ts-project-bundle --out=build",
  },

```

我们的问题是编译后的 JavaScript 代码嵌入在每个独立的 TypeScript 项目中。我们必须分离编译后的代码，留下生产中不需要的原始 TypeScript 源代码。

我们有几个解决方案可供选择。我们可以通过向 Docker 文件中添加一组复制命令，轻松地将代码复制到我们的生产 Docker 映像中，但这样一来，我们就需要为我们添加的任何新库添加新的复制命令，这是不太可伸缩的。此外，如果我们能有一些东西来替代我们现在拥有的和我们将来可能添加的东西，那就更好了。

这就是`ts-project-bundle`发挥作用的地方，如清单 4 所示。这是我创建的一个简单的小命令行工具(当然，通过 npm 分享[，因为我希望每个人都使用它)。](https://www.npmjs.com/package/ts-project-bundle)

它首先读取主项目的`tsconfig.json`文件，然后读取每个引用项目的文件。然后，它将编译后的代码复制到您选择的输出目录中。这就是编译后的 JavaScript 代码被放置在`build`目录中并准备好复制到最终的生产 Docker 映像中的方法。

令人难以置信的是，TypeScript 编译器中没有包括这种代码提取和捆绑——我希望他们将来会添加这一功能，并使 ts-project-bundle 成为多余的。

现在，我们想要构建 Docker 映像。

导航到主项目的目录，然后调用 Docker build 命令，如下所示:

```
docker build .. -f ./Dockerfile -t hello-world
```

请注意我们是如何使用父目录作为构建上下文的。这是因为 Docker 构建阶段需要访问父目录，其中包括主项目和共享库的代码。

Dockerfile 与主项目在同一个目录中，以保持它与那个微服务的相关性和连接。其他微服务也应该有自己的 Dockerfile，你可能想分享一个模板化的 docker file——但那是不同的博文。

因为 Dockerfile 与构建上下文在不同的目录中，所以我们必须手动指定它，这就是为什么我们使用上面的`-f`参数。

构建 Docker 映像后，您可以像这样运行容器:

```
docker run hello-world
```

这种共享代码的方法是可扩展的。同样，这里的例子相对简单，但是我们可以很容易地向它添加更多的代码库和微服务。概括一下:

*   我们使用 TypeScript 项目引用来确保构建任何主项目也构建其共享库
*   我们使用`ts-project-bundle`来捆绑微服务的代码，包括它所依赖的所有库
*   我们正在使用`recursive-install`为每个微服务及其所有共享库安装 npm 依赖项

如果您想知道捆绑的代码是什么样子，请参见下面的图 5。您可能也喜欢为自己构建代码并检查它。你甚至不需要安装 Docker 来完成这项工作——只需导航到主项目，调用`npm run build`,并在生成的`build`子目录中浏览以查看编译和捆绑的代码。

![Figure 5: Output of the build process](img/45c36345dea445306cb57b0be1ccc943.png)

Figure 5: Output of the build process

## 示例 3:在后端和前端之间共享代码库

这个例子也有一个 Docker 微服务，但是它的工作方式和上一个例子一样，所以我不再解释了。

我们现在将关注于向前端共享代码库。示例 UI 是用 TypeScript 和 React 创建的，并与 Parcel 捆绑在一起。

图 6 解释了这个项目的结构。

![Figure 6: Sharing code between backend and frontend](img/d946da79cab35e2625737eb2cf3c1193.png)

Figure 6: Sharing code between backend and frontend

这个例子有目录`backend`、`frontend`和`libs`。同样，这个项目布局是可扩展的:您可以向`libs`目录添加更多的库，向`backend`目录添加更多的微服务。

这种结构也很灵活:你可以把它放在一个 mono-repo 中，也可以把它完全分离出来，作为一个元 repo，为每个微服务、每个库和前端提供单独的代码存储库。

清单 5 显示了我们的示例前端的简单的 [HTML 代码。](https://github.com/ashleydavis/sharing-typescript-code-libraries/blob/main/microservices-and-frontend-example/frontend/index.html)

**清单 5:前端的简单 HTML 文件**

```
<!DOCTYPE html>
<html lang="en">
   <head>
      <title>A simple React frontend using Parcel</title>
   </head>
   <body>
      <div id="root"></div>
      <script src="./src/index.tsx"></script>
   </body>
</html>
```

这个例子[使用了 React 框架](https://blog.logrocket.com/using-typescript-with-react-tutorial-examples/)，在清单 5 中您可以看到我们的 React UI 将在其中呈现的`root`元素。随后的`script`标签导入类型脚本代码文件`index.tsx`。这是我们的 React UI 的主要代码文件。它也是主项目中唯一的 TypeScript 代码文件，因为这是一个简单的示例。

清单 6 展示了使用 React 为前端呈现 UI 的`index.tsx`。请注意我们是如何从共享库中导入该函数，并使用它来呈现“Hello，World！”前端中的消息。

**清单 6:前端的 React 代码**

```
import React from "react";
import ReactDOM from "react-dom";

import { showMessage } from "../../libs/my-library";
   class App extends React.Component {
      render() {
   return <div>{showMessage()}</div>;
      }
   }
ReactDOM.render(<App />, document.getElementById("root"));
```

为了链接到共享库，我们再次使用 TypeScript 项目引用。你可以在前端的`tsconfig.json`文件中看到这个[，但是它和你在前面的例子中看到的没有什么不同。](https://github.com/ashleydavis/sharing-typescript-code-libraries/blob/main/microservices-and-frontend-example/frontend/tsconfig.json)

为了使这个前端在 web 浏览器中可用，我们现在必须将它编译成一个静态网页。[包裹](https://parceljs.org/)让这一切变得简单。它是一个零配置捆绑器，可以自动理解 TypeScript。它理解开箱即用的最常见的资产类型，对于其他一切，有一个插件。

我目前使用的是包 v1，但当它更成熟时会转换到 v2。如果你想了解更多，包文档中有一个很棒的部分是关于使用 TypeScript 和 React 的。

为了编译我们的前端，我们将调用`parcel build`。我将提醒你我个人的习惯，我将它总结在`npm run build`中——记住这是有帮助的，因为无论我在什么类型的项目中，我只需要记住这一个命令，而不是试图记住不同命令行工具的变化。

您可以在下面的清单 7 中看到这一点，这是前端的`package.json` 的[的简化摘录。](https://github.com/ashleydavis/sharing-typescript-code-libraries/blob/main/microservices-and-frontend-example/frontend/package.json)

**清单 7:显示 npm 脚本的 package.json 摘录**

```
"scripts": {
   "build": "tsc --build && parcel build index.html --out-dir=out",
},
```

清单 7 首先使用`--build`参数调用 TypeScript 编译器来构建前端项目和所有共享库。然后我们调用`parcel build`并将其指向`index.html`，后者又指向`index.tsx`并指定输出目录。这将我们的 TypeScript 代码编译成 JavaScript，并将其打包成一个 JavaScript 文件，该文件包含在编译后的 HTML 文件中。

您应该尝试运行这个程序，并亲自确认生成的静态网页确实包含来自我们共享库的代码。导航到`frontend`目录并运行以下命令之一:

```
npx tsc
parcel build index.html --out-dir=out
```

或者，更简单地说，运行以下命令:

```
npm run build
```

现在，导航到`out`子目录，查看编译后的 HTML 和 JavaScript 文件。搜索“你好，世界！”您会发现来自共享库中的代码被捆绑到静态网页中。

这就是全部了。我告诉过你这个例子没有上一个复杂！

现在，你可能会说，这是伟大的包裹，但 bundler X 呢？我对此的第一选择实际上是不使用 Parcel——我试图使用 [Create React App](https://reactjs.org/docs/create-a-new-react-app.html) 来构建这个例子，但不幸的是， [React 还不支持项目引用](https://github.com/facebook/create-react-app/issues/10695)。

在我自己的开发中，我正在远离 webpack，因为 package 更简单，所以我还没有用 webpack 尝试过。如果它不起作用，我会感到惊讶，因为在幕后，webpack 将使用 TypeScript 编译器——或者可能是 Babel——所以项目引用应该起作用。

如果你试图用 [webpack 或任何其他捆绑器](https://blog.logrocket.com/babel-vs-typescript/)来实现这个，请[让我知道](https://twitter.com/ashleydavis75)它是如何为你工作的！

## 结论

在这篇文章中，我们探索了三种不同的共享 TypeScript 代码库的方法。在简要查看了共享代码的标准方法(通过 npm registry 或 GitHub)之后，我们探索了在更大的应用程序中的组件之间共享库的替代性轻量级方法，例如在微服务之间或后端和前端之间。

我在这里展示的例子是可扩展的。我们可以将它们扩展到包含更多代码库、更多微服务等。这种方法在单一回购或元回购中都适用。它甚至可以很好地处理你电脑上的临时文件夹——虽然，在这种情况下，我不知道你为什么不使用版本控制，但是嘿。

我为自己构建的拼图缺少的部分是 ts-project-bundle，你可以在 [npm](https://www.npmjs.com/package/ts-project-bundle) 和 [GitHub](https://github.com/ashleydavis/ts-project-bundle) 上找到它。我希望有一天 ts-project-bundle 将成为历史的遗迹，并且 TypeScript 编译器本身将支持有效的捆绑或导出编译后的 JavaScript 代码的方法。让我们希望他们纠正这一点，因为在我看来这是一个明显的疏忽！

如果你喜欢我在这里写的东西，也请[观看我关于主题](https://www.youtube.com/watch?v=y76n6Ow83YQ)的视频。你也可以[在 Twitter 上关注我](https://twitter.com/ashleydavis75)。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.