# 从零开始用 React 生成静态站点

> 原文：<https://blog.logrocket.com/static-site-generation-with-react-from-scratch/>

如果你问我什么框架有最好的组件模型，我会说毫不犹豫地做出反应。

这有各种各样的原因。一方面，React 制造的组件没有太多的仪式和妥协。该模型也非常独立于 React。有了新的 JSX 工厂，你甚至看不到 React 在 TypeScript 或 Babel 中的导入。

显然，当涉及到在 React 中专门构建每个前端应用程序时，必须有一个警告。除了关于 React 是否真的是做前端的正确方法的明显的宗教讨论之外，房间里的大象是……毕竟……只是 JavaScript。

这是一个 JavaScript 库，它*需要* JavaScript 才能运行。这意味着更长的下载时间，更臃肿的页面，可能还有不太好的 SEO 排名。

## 不使用 JavaScript 使用 React 的选项

那么我们能做些什么来改善这种情况呢？最初的反应可能是以下情况之一:

> "哦，是的，让我们用 Node.js 服务器代替，也做 SSR . "
> 
> “难道没有生成静态标记的东西吗？那个盖茨比是谁？”
> 
> “嗯，我都想要，但显然，一个做类似盖茨比的事情的 SSR 框架会很好。这是 Next.js 级别的吧？”
> 
> “听起来太复杂了。还是再用哲基尔吧。”
> 
> “为什么？！让我们欢迎所有人来到 2021 年——将任何页面作为水疗中心都很酷。”

也许你倾向于认同其中的一个。就个人而言，根据问题的不同，我会选择后两者之一。当然，我用 React 做了相当多的服务器端渲染(SSR ),但我经常发现额外的复杂性不值得付出努力。

同样，我可能是少数几个真正不喜欢盖茨比的人之一。至少对我来说，它把几乎所有的事情都过度复杂化了，我没有看到太多的收获。

就这样了吗？当然，还有另一种方法。如果我们把我们的应用程序放在一个坚实的架构上，我们可以只写一个小脚本，实际上自己执行静态站点生成(SSG)——不需要 Gatsby 或其他任何东西。这不会那么复杂；然而，我们应该看到一些好的收获。

现在我们到底在期待什么呢？

## 设定正确的期望

在本帖中，我们将构建一个简单的解决方案，将使用 React 创建的页面转换为一组完全预生成的静态站点。我们将仍然能够水化这一点，让我们的网站充满活力。

我们的目标是提高初始渲染性能。在我们的 Lighthouse 测试中，我们发现我们自己的主页并不总是像我们希望的那样好。

![React SSR before](img/bd0a765f2573f88d77420780f5e6ff09.png)

我们不会做的是优化静态页面，使它们只有很小的 JavaScript 片段。我们将始终用完整的 JavaScript 来充实页面(可能仍然是延迟加载的，但以后会有更多)。

尽管 SPA 的静态预渲染可能有利于感知性能，但我们不会关注性能优化。如果你对性能优化感兴趣，你应该看看这篇关于使用 webpack 进行性能优化的[深度指南。](https://blog.logrocket.com/guide-performance-optimization-webpack/)

## 一个基本的 React 应用程序

作为本文的样板，我们使用一个相当简单但非常常见的 React 应用程序。我们安装了一些开发依赖项(是的，我们将使用 TypeScript 进行翻译):

```
npm i webpack webpack-dev-server webpack-cli typescript ts-loader file-loader html-webpack-plugin @types/react @types/react-dom @types/react-router @types/react-router-dom --save-dev

```

当然，还有一些运行时依赖性:

```
npm i react react-dom react-router-dom react-router --save

```

现在我们设置一个合适的`webpack.config.js`来捆绑应用程序。

```
module.exports = {
  mode: 'production',
  devtool: 'source-map',
  entry: './src/index.tsx',
  output: {
    filename: 'app.js',
  },
  resolve: {
    extensions: ['.ts', '.tsx', '.js'],
  },
  module: {
    rules: [
      { test: /\.tsx?$/, loader: 'ts-loader' },
      { test: /\.(png|jpe?g|gif)$/i, loader: 'file-loader' },
    ],
  },
};

```

这将支持类型脚本和文件作为资产。请注意，对于一个更大的 web 应用程序，我们可能需要许多其他东西，但这对我们的演示来说已经足够了。

我们还应该添加一些有内容的页面来查看这个工作。在我们的结构中，我们将使用一个`index.tsx`文件来聚合所有内容。这个文件可以像下面这样简单:

```
import * as React from 'react';
import { BrowserRouter, Route, Switch } from 'react-router-dom';
import { render } from 'react-dom';

const HomePage = React.lazy(() => import('./pages/home'));
const FirstPage = React.lazy(() => import('./pages/first'));
const NotFoundPage = React.lazy(() => import('./pages/not-found'));

const App = () => (
  <BrowserRouter>
    <Switch>
        <Route path="/" exact component={HomePage} />
        <Route path="/first" exact component={FirstPage} />
        <Route component={NotFoundPage} />
    </Switch>
  </BrowserRouter>
);

render(<App />, document.querySelector('#app'));

```

这种方法的问题是，我们需要为每个新页面编辑这个文件。如果我们使用将页面存储在`src/pages`目录中的惯例，我们可以得到更好的结果。我们有两个选择:

1.  使用 webpack 的`require.context`魔法来“读出”目录并获得一个我们可以使用的动态列表
2.  使用一个特殊的编译时生成的模块来获得延迟加载和路径

第二种方法提供了一个很大的优势，即路线可以是页面的一部分。对于这种方法，我们需要另一个 webpack 加载器:

```
npm i parcel-codegen-loader --save-dev

```

这现在允许我们重构`index.tsx`文件，看起来像这样:

```
import * as React from 'react';
import { BrowserRouter, Route, Switch } from 'react-router-dom';
import { render } from 'react-dom';
import Layout from './Layout';

const pages = require('./toc.codegen');
const [notFound] = pages.filter((m) => m.route === '*');
const standardPages = pages.filter((m) => m !== notFound);

const App = () => (
  <BrowserRouter>
    <Layout>
      <Switch>
        {standardPages.map((page) => (
          <Route key={page.route} path={page.route} exact component={page.content} />
        ))}
        {notFound && <Route component={notFound.content} />}
      </Switch>
    </Layout>
  </BrowserRouter>
);

render(<App />, document.querySelector('#app'));

```

现在所有页面都完全由`toc.codegen`决定，这是一个在捆绑过程中动态生成的模块。此外，我们添加了一个`Layout`组件，让我们的页面有点共享结构。

`toc`模块的代码生成器如下所示:

```
const { getPages } = require('./helpers');

module.exports = () => {
  const pageDetails = getPages();
  const pages = pageDetails.map((page) => {
    const meta = [
      `"content": lazy(() => import('./${page.folder}/${page.name}'))`,
      `${JSON.stringify('route')}: ${JSON.stringify(page.route)}`,
    ];

    return `{ ${meta.join(', ')} }`;
  });

  return `const { lazy } = require('react');
module.exports = [${pages.join(', ')}];`;
};

```

我们只是迭代所有页面并生成一个新模块，导出一个带有`route`和`content`属性的对象数组。

此时，我们的目标是预先呈现这个简单(但完整)的应用程序。

## SSG 的基础知识与反应

如果你知道 SSR 和 React，你已经知道做一些基本的 SSG 所需要的一切。在其核心，我们使用来自`react-dom/server`的`renderToString`函数，而不是来自`react-dom`的`render`。假设我们有一个嵌套在布局中的页面，下面的代码可能已经工作了:

```
const element = (
  <MemoryRouter>
    <Layout>
       <Page />
    </Layout>
  </MemoryRouter>
);
const content = renderToString(element);

```

在上面的代码片段中，我们假设布局完全由一个`Layout`组件给出。我们还假设 React 路由器用于客户端路由。因此，我们需要提供适当的路由上下文。幸运的是，我们是使用`HashRouter`、`BrowserRouter`还是`MemoryRouter`并不重要，它们都提供了路由上下文。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

现在，什么是`Page`？`Page`指实际显示待预渲染页面的组件。你可能有更多的组件，你想把每一页。一个很好的例子是一个`ScrollToTop`组件，它通常是这样集成的:

```
<Route component={ScrollToTop} />

```

但是，当我们的静态页面变成动态页面时，我们将把这个组件留给水合作用。不需要预先渲染这个。

否则，应用程序本身通常类似于:

```
const app = (
  <BrowserRouter>
    <Route component={ScrollToTop} />
    <Layout>
      <Switch>
        {pages
          .map(page => (
            <Route exact key={page.route} path={page.route} component={page.content} />
          ))}
        <Route component={NotFound} />
      </Switch>
    </Layout>
  </BrowserRouter>
);

hydrate(app, document.querySelector('#app'));

```

这非常接近静态生成的代码片段。主要区别在于，这里我们包括所有页面(和水合物)，而在上面的 SSG 场景中，我们将整个页面内容缩减为一个固定的单个页面。

## SSG 的陷阱

到目前为止，一切听起来简单明了，对吗？但是问题隐藏在引擎盖下。

### 资产支持

我们如何引用资产？假设我们使用如下代码:

```
<img src="/foo.png" />

```

鉴于`foo.png`存在于我们的服务器上，这可能行得通。使用完整的 URL 会更可靠一点，例如`[http://example.com/foo.png](http://example.com/foo.png)`。然而，我们会放弃一些关于环境的灵活性。

很多时候，我们会把这些资产留给像 webpack 这样的捆绑商。在这种情况下，我们会有这样的代码:

```
<img src={require('../assets/foo.png')} />

```

在这里，`foo.png`在构建时被本地解析，然后被散列、优化并复制到目标目录。这一切都很好。然而，对于上述过程，只需要 Node.js 中的模块是不行的。我们将遇到一个异常，即`foo.png`不是一个有效的模块。

解决这个其实并不复杂。幸运的是，我们可以为 Node.js 中的模块注册额外的扩展:

```
['.png', '.svg', '.jpg', '.jpeg', '.mp4', '.mp3', '.woff', '.tiff', '.tif', '.xml'].forEach(extension => {
  require.extensions[extension] = (module, file) => {
    module.exports = '/' + basename(file);
  };
});

```

上面的代码假设文件已经/将要被复制到根目录。因此，我们将把`require('../assets/foo.png')`转换成`"foo.png"`。

哈希部分呢？如果我们已经有了一个资产文件列表和它们的散列，我们可以使用这样的代码:

```
const parts = basename(file).split('.');
const ext = parts.pop();
const front = parts.join('.');
const ref = files.filter(m => m.startsWith(front) && m.endsWith(ext)).pop() || '';
module.exports = '/' + ref;

```

这将试图找到一个以“foo”开头、以“png”结尾的文件，比如`foo.23fa6b.png`。如上休息。

### 支持类型脚本

既然我们知道了如何处理通用资产，我们也可以为`.ts`和`.tsx`文件提供一个处理机制。毕竟，这些也可以在内存中传输到 JS。然而，所有这些工作都是不必要的，因为`ts-node`已经这么做了。所以我们的工作本质上归结为:

```
npm i ts-node --save-dev

```

然后使用以下命令为各自的文件扩展名注册处理程序:

```
require('ts-node').register({
  compilerOptions: {
    module: 'commonjs',
    target: 'es6',
    jsx: 'react',
    importHelpers: true,
    moduleResolution: 'node',
  },
  transpileOnly: true,
});

```

现在，这允许只需要定义为`.tsx`文件的模块。

### 惰性装载

如果我们已经编写了一个优化的 React SPA，那么我们还将在路由级别捆绑拆分我们的应用程序。这意味着每个页面都有自己的子包。因此，将有一个核心/公共包(通常包含路由机制、React 本身、一些共享依赖项等。)和每页一个包。如果我们有 10 页，我们最终会有 11(或更多)个包。

但是使用预渲染方法，这并不理想。毕竟，我们已经在预渲染单个页面了。如果我们在一个页面上有一个共享的子组件(例如，一个地图控件),但是它太大了，我们想把它放入自己的包中，那该怎么办？

也就是说，我们有这样一个组件:

```
const ActualMap = React.lazy(() => import('./actual-map'));

const Map = (props) => (
  <React.Suspense fallback={<div>Loading Map ...</div>}>
     <ActualMap {...props} />
  </React.Suspense>
);

```

其中实际的组件将被延迟加载。在这种情况下，我们会撞上一堵相当大的墙。React 不知道如何预渲染`lazy`。

幸运的是，我们可以重新定义`lazy`,只显示一些占位符(我们也可以在这里疯狂地加载或预呈现内容，但让我们保持简单，并假设这里被延迟加载的内容实际上应该在以后被延迟加载):

```
React.lazy = () => () => React.createElement('div', undefined, 'Loading ...');

```

在`renderToString`中不可用的另一部分是`Suspense`。好吧，老实说，实现这一点不会有什么坏处，但是让我们自己来做吧。

我们在这里所要做的就是假装`Suspense`根本不存在。所以我们只是使用提供的`children`用一个片段替换它。

```
React.Suspense = ({ children }) => React.createElement(React.Fragment, undefined, children);

```

现在，我们非常有效地处理了延迟加载，尽管根据场景，我们可以(甚至可能想要)做得更多。

### 其他事情

除了像`lazy`和`Suspense`这样的东西，React 中可能还缺少其他部分。一个很好的例子是`useLayoutEffect`。然而，由于`useLayoutEffect`通常只在运行时应用，支持它的简单方法就是完全避免它。

一个非常简单的实现是用一个无操作函数来代替它。这种方式，`useLayoutEffect`不符合我们的方式，被简单地忽略:

```
React.useLayoutEffect = () => {};

```

我们`require`的一些模块实际上不会是 CommonJS 格式的。这是个大问题。在撰写本文时，Node.js 仅支持 CommonJS 模块( [ES 模块仍处于实验阶段](https://blog.logrocket.com/es-modules-in-node-today/))。

幸运的是，有 [esm 包](https://github.com/standard-things/esm#readme)，它使用`import ...`和`export ...`语句为我们提供了对 es 模块的支持。太好了！

和`ts-node`一样，我们从安装依赖项开始:

```
npm i esm --save-dev

```

然后实际使用它。在这种情况下，我们需要用它的新版本替换“正常的”`require`。代码如下:

```
require = require('esm')(module);

```

现在我们也支持 ES 模块。唯一缺少的可能是一些与 DOM 相关的功能。

虽然大多数情况下我们应该像这样在代码中放置条件:

```
if (typeof window !== 'undefined') {
  // ...
}

```

我们也可以伪造一些其他的全局变量。例如，我们(或我们直接或间接使用的一些依赖关系)可能指`XMLHttpRequest`、`XDomainRequest`或`localStorage`。在这些情况下，我们可以通过扩展`global`对象来模拟它们。

按照同样的逻辑，我们可能会嘲笑`document`，或者至少是它的一部分。当然，还有`jsdom`包，[已经帮助他们完成了大部分](https://github.com/jsdom/jsdom)。但是现在让我们简单明了。
只是一个嘲讽的例子:

```
global.XMLHttpRequest = class {};
global.XDomainRequest = class {};
global.localStorage = {
  getItem() {
    return undefined;
  },
  setItem() {},
};
global.document = {
  title: 'sample',
  querySelector() {
    return {
      getAttribute() {
        return '';
      },
    };
  },
};

```

现在，我们已经做好了一切准备，可以毫不费力地开始预渲染我们的应用程序了。

## 将这一切结合在一起

我们有很多方法可以概括和使用上面的片段。就我个人而言，我喜欢在一个单独的模块中使用它们，该模块使用`fork`对每页进行评估/使用。这样，我们总是得到隔离的进程，也可以并行化。

下面显示了一个示例实现:

```
const path = require('path');
const { readFileSync, readdirSync } = require('fs');
const { fork } = require('child_process');
const { getPages } = require('./files');

function generatePage(page, files, html, dist) {
  const modPath = path.resolve(__dirname, 'ssg-kernel.js');

  console.log(`Processing page "${page.name}" ...`);

  return new Promise((resolve, reject) => {
    const ps = fork(modPath, [], {
      cwd: process.cwd(),
      stdio: 'ignore',
      detached: true,
    });

    ps.on('message', () => {
      console.log(`Finished processing page "${page.name}".`);
      resolve();
    });

    ps.on('error', () => reject(`Failed to process page "${page.name}".`));

    ps.send({
      source: page.path,
      target: page.route,
      files,
      html,
      dist,
    });
  });
}

function generatePages() {
  const dist = path.resolve(__dirname, 'dist');
  const index = path.resolve(dist, 'index.html');
  const files = readdirSync(dist);
  const html = readFileSync(index, 'utf8');
  const baseDir = path.resolve(__dirname, '..');
  const pages = getPages();
  return Promise.all(pages.map(page => generatePage(page, files, html, dist)));
}

if (require.main === module) {
  generatePages()
    .then(
      () => 0,
      () => 1,
    )
    .then(process.exit);
} else {
  module.exports = {
    generatePage,
    generatePages,
  };
}

```

这个实现可以作为 lib 使用，也可以直接通过`node`使用。这就是为什么我们使用`require.main`作为鉴别器来区分这两种情况。在 lib 的情况下，我们导出两个函数`generatePages`和`generatePage`。

这里唯一剩下的就是`getPages`的定义。为此，我们可以只使用我们在关于基本 React 应用程序的介绍部分中指定的定义。

`ssg-kernel.js`模块将包含上面的代码。包装在适当的信封中，以便在分叉过程中使用，我们最终得到:

```
// ...

process.on('message', msg => {
  const { source, target, files, html, dist } = msg;
  setupExtensions(files);

  setTimeout(() => {
    const { content, outPath } = renderApp(source, target, dist);
    makePage(outPath, html, content);

    process.send({
      content,
    });
  }, 100);
});

```

其中`setupExtensions`将为`require`设置必要的修改，而`renderApp`则执行实际的标记生成。`makePage`使用`renderApp`的输出实际写出生成的页面。

使用这样的设置，我们可以预渲染我们的网站并获得显著的性能提升，正如 Lighthouse 所证实的:

![React SSR After](img/0a786235a552a46eb1dd1c6578d724f3.png)

作为一个很好的副作用，我们自己的网站现在在某种程度上也适用于没有 JavaScript 的用户。完整的示例应用程序[可以在 GitHub](https://github.com/FlorianRappl/react-prerender-demo) 上找到。

## 结论

使用 React 创建优秀的静态页面实际上没什么大不了的。没有必要退回到臃肿复杂的框架。这样，我们可以很好地控制什么进入，什么需要留在外面。

此外，我们还了解了 Node.js、React 的内部结构，以及我们使用的一些依赖关系。了解我们实际使用的东西不仅仅是一个学术练习，而是在出现错误或其他问题时至关重要。

使用这种技术预渲染 SPA 的性能增益会很好。更重要的是，我们的页面变得更容易访问，并且在 SEO 中排名更高。

你在哪里看到预渲染发光？这仅仅是一个无意义的练习吗，或者 React 也可以是一个很好的开发模型来编写没有交互性的可重用组件吗？

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