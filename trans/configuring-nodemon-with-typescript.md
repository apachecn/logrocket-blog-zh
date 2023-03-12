# 使用 TypeScript 配置 nodemon-log rocket 博客

> 原文：<https://blog.logrocket.com/configuring-nodemon-with-typescript/>

[nodemon](https://nodemon.io) 是 Node.js 的一个 CLI，它通过在文件更新时重新启动一个执行过程来加快 JavaScript 开发。例如，如果您有一个带有`index.js`文件的项目，您想要快速测试和迭代，您可以运行`nodemon index.js`，并且一个新的 Node.js 执行过程将为`index.js`开始，每当项目中的一个文件被更新时重新启动。简单吧？

嗯，nodemon 提供的简单性随着您将 TypeScript 引入到项目中以及项目复杂性的增加而降低。但是不要害怕！在本文中，我们将回顾配置 nodemon 的三种方法，每种方法都提供了不同的特性和功能，可以满足您的 TypeScript 项目的需要。

如果您正在寻找能够更好地满足项目需求的 nodemon 替代方案，我们还将回顾三个具有额外特性和更多可定制性的 nodemon 替代方案。由于每个选项都有自己的优点和缺点，我们将讨论每个选项是否适合我们项目的需要，如果不适合，哪个选项是更好的选择。

## 方法 1:无配置工作流

从 v1.19.0 开始， [nodemon 在`ts-node`的帮助下内置了对类型脚本文件](https://github.com/remy/nodemon/releases/tag/v1.19.0)的支持，不需要手动配置。默认情况下，nodemon 使用`node` CLI 作为运行 JavaScript 文件的执行程序；对于类型脚本文件，nodemon 使用`ts-node`作为执行程序。

`ts-node`是一个编译并运行类型脚本文件的类型脚本执行引擎。`ts-node`作为`node` CLI 的替代，因此可以将与`node` CLI 相同的参数传递给`ts-node` CLI。

此方法要求安装 node mon 1 . 19 . 0 以上的版本。此外，`ts-node`必须安装在您的项目中。因为这两个包很可能只在开发期间使用，所以它们应该作为`devDependencies`安装。

```
yarn add --dev nodemon ts-node

```

一旦安装了这两个依赖项，就可以像传递 JavaScript 文件一样向 nodemon 传递 TypeScript 文件。

```
npx nodemon ./main.ts

```

### 优点和缺点

这是迄今为止最简单的方法，因为它需要最少的设置。它内置于 nodemon 本身，所以所需要的只是安装必要的包。

然而，这种方法在灵活性和定制方面有所欠缺。许多项目不仅仅需要默认的`ts-node`使用的`tsc`类型脚本编译器，还有一些项目需要更高级的配置；如果这个场景描述了您的需求，请继续方法二。

## 方法 2:手动配置

内置的 nodemon TypeScript runner 提供了一种以最少的设置启动并运行的方法:[手动配置](https://github.com/remy/nodemon#config-files)。

如果您的项目需要更灵活地执行文件，nodemon 允许用户创建一个配置文件来满足项目的具体规范。通过使用定制的配置文件，您可以获得 nodemon 灵活性的最大好处，并利用它提供的所有设置。

我们将配置的具体设置是`execMap`，即执行图。该设置通知 nodemon 对于不同的文件类型要运行哪些可执行文件或命令。现在，我们将讨论如何专门为 TypeScript 文件设置执行映射。

要创建一个配置文件，在项目的根目录下创建一个名为`nodemon.json`的新文件。

```
touch ./nodemon.json

```

在`nodemon.json`文件中，创建一个带有`execMap`属性的新 JSON 对象。`execMap`属性的值应该是一个对象。

```
{
    "execMap": {}
}

```

在`execMap`对象中，为`ts`文件创建一个新属性。该属性的值应该是执行 TypeScript 文件时要运行的任何命令。例如，您可以将其设置为`ts-node`，或者任何其他执行脚本或命令。

```
{
    "execMap": {
        "ts": "ts-node"
    }
}

```

瞧，nodemon 现在被配置为运行 TypeScript 文件的自定义命令。当你用一个类型脚本文件(即`nodemon index.ts`)调用 nodemon 时，nodemon 会在`execMap`中找到与`.ts`文件相关的命令，然后运行那个命令，将文件作为最后一个参数传递(即`ts-node index.ts`)。

### 额外小费

如果您想在命令的其他地方传递文件路径(即，不作为最后一个参数)，请在命令中文件路径应该放置的位置键入`{{pwd}}`。例如，如果您对`.js`文件的`execMap`命令是`node {{pwd}} && echo "Hello world"`，那么调用`nodemon index.js`将运行`node index.js && echo "Hello world"`。

### 优点和缺点

使用定制的 nodemon 配置文件提供了许多项目所需的灵活性。正如 nodemon 文档中的[所解释的，您可以配置很多设置。](https://github.com/remy/nodemon/tree/master#readme)

在这种程度上，这种方法应该只在第一种方法不能满足项目需求的情况下使用。如果您的项目只需要编译和运行您的类型脚本文件，那么内置的 nodemon 类型脚本支持`ts-node`(方法一)可能是您的项目的最佳选择。

如果您的项目碰巧需要更多的定制，请考虑方法三。

## 方法 3:自定义执行命令

nodemon 作为一个工具大放异彩，当项目中的任何文件被更新时，它可以帮助运行和重启单个文件的执行。然而，并不是所有的项目都有单一的入口点；也就是说，许多现代项目需要使用外部工具来引导或执行您的项目。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

方法一和方法二提供了执行单个文件的方法，而这个方法将为[提供执行单个命令](https://github.com/remy/nodemon#running-non-node-scripts)的方法，从而提供了这些方法的最大灵活性。

在您的`package.json`文件中，创建一个`start`脚本。当文件发生更改时，这将作为 nodemon 运行和重新启动的命令。

要使用 nodemon 执行该命令，请运行:

```
nodemon --exec "yarn start"
# or
nodemon --exec "npm run start"

```

这将通过 nodemon 将`start`脚本作为可执行命令传递给您的项目。

### 额外小费

您可以将完整的 nodemon 命令(即`nodemon --exec "yarn start"`)制作成一个`dev`脚本，这样调用`yarn dev`将使用您定制的执行命令运行 nodemon。

### 优点和缺点

虽然这种方法在可以运行什么方面提供了最大的灵活性，但它否定了 nodemon 最显著的特性:(当项目中的文件被更新时，重新运行单个文件的执行)。

在选择此方法之前，请考虑方法一或方法二是否更适合您的项目需求。

## nodemon 有哪些替代方案？

nodemon 无疑是使用 Node.js 进行快速开发的强大工具。

在这篇文章的下一部分，我们将考虑 nodemon 的三种替代方案:`ts-node-dev`、`pm2`和一个用包构建的 DIY 文件监视器[。](https://blog.logrocket.com/what-you-need-to-know-about-parcel-2/)

## 备选方案 1:终端服务节点开发

在第一种方法中，我们讨论了 nodemon 如何使用`ts-node`来编译和运行 TypeScript 文件。`[ts-node-dev]([https://github.com/wclr/ts-node-dev](https://github.com/wclr/ts-node-dev))`将 nodemon 的文件查看功能与来自`ts-node`的 TypeScript 支持结合到一个类似 nodemon 的服务中，该服务是专门为 TypeScript 定制的。

`ts-node-dev`直接与 TypeScript 执行引擎和编译过程接口，为 TypeScript 文件提供比 nodemon 更有效的系统。`ts-node-dev`仅在对条目文件的依赖文件(即由条目文件导入的文件)进行更改时重新加载。此外，`ts-node-dev`在重启之间共享一个单独的编译过程，以最大化效率并使重启更快。

要使用`ts-node-dev`，首先将其安装为`devDependency`:

```
yarn add --dev ts-node-dev

```

然后，要运行您的文件并根据文件更改重新启动，请运行:

```
ts-node-dev --respawn index.ts
# or
tsnd --respawn index.ts

```

用项目的入口文件替换`index.ts`。

### 优点和缺点

`ts-node-dev`是快速 TypeScript 开发的一个很好的选择，因为它比 nodemon 更有效，并且是专门为 TypeScript 开发的。

然而，尽管它确实提供了某种级别的配置，但可以说,`ts-node-dev`的可定制性比 nodemon 差得多。它也不会因为静态资产的更改而重新启动，这在 web 服务器上提供图像时非常有用。在为您的项目选择`ts-node-dev`之前，请务必考虑这些不利因素。

## 备选方案 2: `pm2`

[pm2](https://github.com/Unitech/pm2) 是 Node.js 程序的一个久经考验的生产就绪流程管理器，它加载了许多特性和配置选项。它用于管理多个 Node.js 应用程序和进程，并附带一个负载平衡器来管理具有大量查询的繁重应用程序。

`pm2`支持热重装、应用监控、详细流程管理。除了所有这些特性之外，`pm2`还提供了一个自动重启功能，当文件被更改时，它会重启你的程序。

要开始使用`pm2`，请在您的系统上全局安装它。

```
npm install pm2 -g

```

接下来，我们将不得不做一点配置。创建一个名为`ecosystem.config.json`的文件，并输入以下内容:

```
module.exports = {
    apps: [
        {
            name: "TSServer",
            script: "ts-node",
            args: "index.ts", // replace this with your project's entry file
        }
    ]
}

```

这将创建一个名为“TSServer”的新应用程序，它将运行`ts-node index.ts`。最后，运行:

```
pm2 start ecosystem.config.js --only TSServer --watch

```

这将运行 TSServer 应用程序，并使用`watch`参数重新启动文件更改。终端上应该打印出一个关于您的应用程序的精美表格，标题为**观察**的一栏应该显示为您的应用程序启用了**。该应用程序将在后台运行，直到您调用`pm2 stop TSServer`。**

 **### 优点和缺点

如前所述，`pm2`包含了令人兴奋的特性，对于大型生产应用程序来说非常有用。然而，由于这个原因，`pm2`对于您的项目来说可能是多余的。

如果您只是在寻找一种简单的方法来重新启动 TypeScript 项目，这种方法可能不是您项目的最佳选择，您应该考虑其他替代方法或 nodemon 方法。

## 替代方案 3: DIY 文件查看器和包裹

有时候，做一件事的最好方法是完全由你自己从头做起。

正如我们在所有以前的方法和选择中所看到的，使用一个选项而不是另一个选项总是有潜在的负面或缺点。您可以通过从头开始创建一个文件监视器来避免这些限制，甚至可以在这个过程中学习一些东西！

对于这个 DIY 文件监视器，我们将利用包文件捆绑器提供的功能，它可以用于开发 web 应用程序或 Node.js 库。

Parcel 公开了一个 JavaScript API 来监视捆绑过程中的事件。每次更新文件时，我们的 TypeScript 项目的绑定过程都会重新开始。当绑定过程完成时，我们将产生一个子过程来执行绑定和编译的 JavaScript 文件。
下面是一个用包裹制作的[我的 DIY 文件查看器的例子:](https://gist.github.com/chroline/c84b14d76f2c6305ec388cb8bd5756ee)

```
// make sure you have @parcel/core and @parcel/config-default
// installed as devDependencies

import {Parcel} from '@parcel/core';
import {spawn, ChildProcessWithoutNullStreams} from 'child_process';

let bundler = new Parcel({
    entries: 'src/index.ts',
    defaultConfig: '@parcel/config-default',
    defaultTargetOptions: { distDir: `${process.cwd()}/dist` },
});

async function main() {
    let cp: ChildProcessWithoutNullStreams;

    await bundler.watch(() => {
        cp?.kill()
        cp = spawn("node",[`${process.cwd()}/dist/index.js`])
        cp.stderr.on('data', (data) => {
            console.log(`stderr: ${data}`);
        })
        cp.stdout.on('data', (data) => {
            console.log(`stdout: ${data}`);
        });
    });
}

main()

```

这种方法的另一个好处是，您实际上可以用 TypeScript 编写整个文件监视器！要运行文件监视器，只需用`ts-node`运行文件。

```
ts-node runner.ts

```

### 优点和缺点

到目前为止，这种方法提供了最大的可定制性，因为您是自己创建文件监视过程的。如果需要，您可以生成一个不同的子进程，或者生成多个子进程，并且当文件更新时，您可以根据需要运行任何其他 JavaScript/TypeScript 代码。

然而，由于这是一个 DIY 解决方案，维护和保养 runner 是您自己的责任，而对于本文中提供的所有其他选项，这是由知识丰富的开源开发团队为您完成的。然而，只要你知道你在做什么，这个可选择的选项当然不应该被忽视！

## 结论

有许多方法可以配置 nodemon 来满足您项目的需求和要求。但是，如果这些方法都不适合您，那么还有很多替代方法可以为您的项目提供不同于 nodemon 的优势。我希望您已经在本文中找到了适合您的特定用例的方法。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.**