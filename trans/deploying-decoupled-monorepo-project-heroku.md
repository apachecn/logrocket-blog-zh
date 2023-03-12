# 在 Heroku - LogRocket 博客上部署解耦的 monorepo 项目

> 原文：<https://blog.logrocket.com/deploying-decoupled-monorepo-project-heroku/>

## 什么是单向回购？

monolith 的目标是为整个项目提供一个单一的代码库，而一个解耦的项目意味着功能不相互依赖。

当一个代码库被设置为 monorepo 时，很容易看到整个项目是如何构造的，特别是如果有多个项目，比如多个前端或几个微服务。

此外，在每个项目之间共享代码变得很容易。例如，如果有多个前端，它们可能很容易共享 UI 组件，因为整个代码库位于同一个 repo 中。因此，使用 monorepos 使管理您的项目更容易，并提供更好的开发体验。

## 构建解耦的 monorepo 项目

当一个项目是分离的，并且每个功能有几个代码库时，最好的做法是为每个系统有单独的`package.json`文件，这样它们可以很容易地被移动，并且如果必要的话可以集成到另一个项目中。

虽然有可能将单个`package.json`作为包的真实来源，但是这种方法不可伸缩，并且可能很快变得混乱。然而，拥有单个`package.json`文件也可以实现依赖共享。

有一些工具可以管理和利用这样的 monorepo 项目，例如 [Lerna](https://lerna.js.org/) ，这是一个在单个存储库中管理多个项目的工具。Lerna 可以帮助开发者在根目录下拥有通用的依赖关系，并为每个项目管理特定文件夹下的特定依赖关系。这使得依赖项管理更加容易，因为共享的依赖项是从一个文件中控制的。

## 将 monorepo 部署到 Heroku

在本文中，我们将向 Heroku 部署一个基本的 monorepo，它有一个客户端和一个服务器应用程序。

我们将在两个应用程序上使用 TypeScript，并通过项目根目录中的一个`package.json`文件来控制每个应用程序的构建过程。这个文件是 Heroku 检测到的，它包含控制属于客户端和服务器应用程序的单独的`package.json`文件的脚本。

目标是编译 TypeScript 代码并构建客户端应用程序，用 TypeScript 编译服务器应用程序，然后使其服务于客户端应用程序的分发。我们还将实现一个简单的 REST API 端点来演示开发和生产中客户机和服务器之间的连接。

## 使用 Vite 构建客户端应用程序

创建一个文件夹，然后在该文件夹中运行`npm init -y`来生成一个`package.json`文件。接下来，为客户机和服务器创建两个单独的文件夹。对于客户端应用程序，让我们使用 [Vite](https://vitejs.dev/) ，它是一个支持 React、Vue 和 Svelte 的构建工具。

[Vite](https://blog.logrocket.com/getting-started-with-vite/) 为你的代码提供开发服务，并将其捆绑用于生产。它使用 ESLint，并支持热模块替换，这有助于您在开发时看到代码中的变化，而不会丢失应用程序的状态。

要用 Vite 创建一个前端应用程序，使用下面的命令，其中`client`是项目和文件夹的名称:

```
npm init vite client

```

运行该命令后，将提示您选择一个框架。我选择 React 和 [react-ts](https://blog.logrocket.com/using-typescript-with-react-tutorial-examples/) 作为变体，它作为后续提示出现。

现在我们的项目文件夹有一个`package.json`文件和一个`client`文件夹。在继续下一步之前，进入`client`文件夹并运行`npm install`来安装所有的包。

我们需要在`vite.config.ts`文件中配置代理设置。如果我们想向服务器应用程序发出请求，我们可以将代理设置配置为`localhost:8080`，其中`8080`是我们将要使用的端口号。

这样，我们可以在客户端应用程序中向`/api/test`发出请求，例如，这个请求将被发送到`localhost:8080/api/test`。这仅用于开发，因为这两个应用程序将在生产中由同一来源提供服务。

更新`vite.config.ts`文件，使其包含`server`对象，如下所示:

```
export default defineConfig({
  plugins: [react()],
  server: {
    proxy: {
      '/api': 'http://localhost:8080'
    }
  }
})

```

## 构建服务器应用程序

现在让我们创建一个服务器文件夹来存储服务器的文件。在其中，运行`npm init -y`来生成一个`package.json`文件。

因为我们使用了 React with TypeScript，所以将 TypeScript 用于服务器应用程序也是一个很好的实践。

在`server`文件夹中，运行`npx tsc --init`为 TypeScript 生成一个配置文件。默认情况下，生成的文件带有几个选项集，但是我们将添加额外的参数来定制它以满足我们的需要。

配置文件在`./dist`文件夹中生成编译后的`.ts`文件，通过将`rootDir`键设置为`./src`，我们保证了`./src`的内容在编译时会直接出现在`./dist`的下面。

```
{
  "compilerOptions": {
    "target": "es6",
    "module": "commonjs",
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "moduleResolution": "node",
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true
  },
  "exclude":[
    "./node_modules"
  ]
}

```

接下来，让我们安装所需的依赖项。我们需要`typescript`、`@types/node`、`@types/express`和`ts-node-dev`作为开发依赖项，以及`express`作为依赖项，这是我们将用来服务客户端应用程序和创建端点的框架。

```
npm instal --save-dev typescript ts-node-dev @types/node @types/express
npm install --save express

```

[ts-node-dev](https://www.npmjs.com/package/ts-node-dev) 是一个用于观察用 TypeScript 编写的 Node.js 中的变化的包。它基本上是带有 Node 的 TypeScript 的一个`nodemon`等价物。

现在我们可以编辑`package.json`文件来添加脚本，以构建和运行开发项目。将以下脚本添加到`package.json`文件中:

```
"scripts": {
  "build": "tsc --build",
  "dev": "ts-node-dev --respawn ./src/index.ts"
},

```

我们需要的最后一个文件是要忽略`node_modules`的`.gitignore`文件。用以下内容创建一个`.gitignore`文件:

```
node_modules

```

对于客户端应用程序，我们不需要这样做，因为 Vite 创建的样板文件已经有了一个`.gitignore`文件。

到目前为止，我们已经完成了客户机和服务器应用程序的设置。现在我们将编写一个带有端点的小型服务器作为用例。

在`/server/src`下，创建一个包含以下内容的`index.ts`文件:

```
import express from 'express';
import path from 'path';

const app = express();
const PORT = process.env.PORT || 8080;
const pathName = path.join(__dirname, '/../../client/dist');

app
  .use(express.static(pathName))
  .listen(PORT, () => console.log(`Listening on ${PORT}`));

app.get('/api/test', (req, res) => {
  res.send({ foo: 'bar' });
});

app.get('*', (req, res) => {
  res.sendFile(pathName);
});

```

这是一个运行在端口`8080`上的基本 Express 服务器，服务于`client/dist`文件夹中的内容，该文件夹是包含来自客户端应用程序的构建过程输出的目录。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

我们在`/api/test`上也有一个可访问的端点，它用一个用于测试目的的对象来响应。

## 测试服务器

现在，我们可以通过从客户端发送请求来快速测试服务器应用程序。Vite 生成了一个示例应用程序，所以我们可以用它来创建一个函数和一个对服务器的 GET 请求，然后在组件挂载时调用这个函数。

在`client/src`下，找到`App.tsx`并添加以下代码片段:

```
const get = async () => {
  const res = await fetch('/api/test');
  const body = await res.json()
  console.log(body)
}

useEffect(() => {
  get();
})

```

在我们为客户机应用程序运行开发服务器之前，我们应该启动服务器应用程序，以便可以访问`/api/test`端点。在`/server`目录下，运行`npm run dev`以手表模式启动服务器。

现在，使用`/client`目录下的`npm run dev`命令运行客户端应用程序的开发服务器。这将在`localhost:3000`上启动一个开发服务器。如果您访问该页面并打开浏览器控制台，您应该会看到从服务器返回的对象。

为了将这两个应用程序部署到一个 Heroku dyno 上，我们需要在主项目目录的`package.json`中添加一些脚本。

```
|- server
|- client
|- package.json

```

因为我们有多个文件夹，它们有自己的`package.json`文件，我们应该告诉 Heroku 在这些文件夹中安装依赖项和`devDependencies`。为此，进入这些目录并调用`npm install --dev`。我们需要`devDependencies`的原因是我们需要用`typescript`包编译打字稿，这个包列在`devDependencies`里。

这同样适用于构建过程。我们进入这些文件夹并调用`npm run build`命令。最后，我们需要启动应用程序，这只是服务器应用程序。

```
"scripts": {
  "install": "cd client && npm install --dev && cd ../server && npm install --dev",
  "build": "cd client && npm run build && cd ../server && npm run build",
  "start": "cd server/dist && node index.js"
},

```

## 结论

在本文中，我们讨论了如何将解耦的 monorepo 项目部署到 Heroku 上的单个 dyno，而不是为一个服务器和一个客户端应用程序部署多个 dyno。在拥有多个微服务以及客户端和服务器应用程序的情况下，您将需要多个 dynos，因为每个服务都应该独立运行。

在没有任何附加服务的全栈应用的例子中，只有运行在 dyno 上的服务器为客户端提供服务，并可能实现客户端和可能的微服务之间的通信。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

专注于重要的 bug-[试试今天的 log 火箭](https://lp.logrocket.com/blg/signup-issue-free)。