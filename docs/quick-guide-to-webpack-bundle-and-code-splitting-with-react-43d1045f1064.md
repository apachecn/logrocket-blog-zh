# 使用 React 进行 webpack 捆绑包和代码拆分的快速指南

> 原文：<https://blog.logrocket.com/quick-guide-to-webpack-bundle-and-code-splitting-with-react-43d1045f1064/>

![](img/328c8a48d048eae619f8fe234947a1c3.png)

### **简介**

你之所以在这里，可能是因为你的应用已经发展到了迫使用户把整个东西作为一个文件下载的地步，这看起来很残忍，也很不寻常。事实是，大量的特性和复杂的 UX 必然会影响您正在处理的代码量。怎么办？

简单。制作“供应商包”供应商包包含每个应用程序特性所依赖的所有框架和库。通过将所有这些代码构建到一个包中，客户端可以有效地缓存这个包，并且您只需要在框架或库更新时重新构建这个包。

还值得考虑的是，每次更新或更改应用程序时，客户端都必须下载新的供应商依赖项。怎么办？

你猜对了。把他们捆起来。将更改的部分放在一个包中，将依赖项放在另一个包中。这样，客户端只下载它需要的东西。

在这篇文章中，我将带您了解 Webpack 中捆绑包拆分的基础知识。如你所料，我还会谈到代码分割。

### **如何创建供应商捆绑包**

假设您有一个基本的 React 应用程序，在 CLI 中执行`npm run build`以获得一个基线构建:

```
Hash: 9db5a05e09ad73897fd4
  Version: webpack 2.2.1
  Time: 2114ms
          Asset       Size  Chunks                    Chunk Names

    ...font.eot     166 kB          [emitted]
  ...font.woff2    77.2 kB          [emitted]
   ...font.woff      98 kB          [emitted]
    ...font.svg     444 kB          [emitted]  [big]
       logo.png      77 kB          [emitted]
    ...font.ttf     166 kB          [emitted]

         app.js     170 kB       0  [emitted]         app

        app.css    3.11 kB       0  [emitted]         app
     app.js.map     193 kB       0  [emitted]         app
    app.css.map   89 bytes       0  [emitted]         app
     index.html  248 bytes          [emitted]
     [0] ./~/process/browser.js 7.3 kB {0} [built]
     [3] ./~/react/lib/ReactElement.js 13.2 kB {0} [built]
    [18] ./app/component.js 292 bytes {0} [built]
  ...
```

如您所见，这个 app 非常大，大约 170kb。这是您将在我们接下来的示例中解决的问题。

[![](img/37fb366b01f8ba4b23129468d1953129.png)](https://logrocket.com/signup/)

### 代码拆分

代码分割是一个很棒的特性，它将你的代码库分割成不同的、更小的包，可以在需要的时候加载。

这意味着你的应用程序是由许多称为“块”的小代码文件(作为模块)组成的。如果使用得当，代码分割将降低加载时间。Webpack 允许你在你的代码库中定义分割点，然后处理依赖关系、输出文件和运行时的东西。

让我们继续浏览使用 Webpack 时最常见的代码分割方法。

#### 异步加载

这基本上就是通过异步加载模块来拆分代码。假设您有一个在`node_modules`中带有 Lodash 的目录:

```
webpack-demo 
|- package.json 
|- webpack.config.js 
|- /dist 
|- /src 
  |- index.js
|- /node_modules
```

在`index.js`文件中，使用通用 JS `require.ensure`导入`lodash`。正如方法名所示，它确保模块仅在需要代码时加载，并且是异步加载的:

```
require.ensure(['lodash'],  function() {
  var lodash = require('lodash');
  ...

})
```

您不需要更改 Webpack 配置中的任何内容:

```
const path = require('path');
const HTMLWebpackPlugin = require('html-webpack-plugin');
module.exports = {
  entry: {
    index: './src/index.js'
  },
  plugins: [
    // ...
  ],
  output: {
    filename: '[name].bundle.js',
    path: path.resolve(__dirname, 'dist')
  }
};
```

在我们的 CLI 中运行`npm run build`将产生以下构建结果:

```
Hash: a27e5bf1dd73c675d5c9
Version: webpack 2.6.1
Time: 544ms
           Asset     Size  Chunks                    Chunk Names
lodash.bundle.js   541 kB       0  [emitted]  [big]  lodash
 index.bundle.js  6.35 kB       1  [emitted]         index
   [0] ./~/lodash/lodash.js 540 kB {0} [built]
   [1] ./src/index.js 377 bytes {1} [built]
   [2] (webpack)/buildin/global.js 509 bytes {0} [built]
   [3] (webpack)/buildin/module.js 517 bytes {0} [built]
```