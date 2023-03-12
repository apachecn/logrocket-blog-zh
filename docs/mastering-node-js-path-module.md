# 掌握 Node.js 路径模块

> 原文：<https://blog.logrocket.com/mastering-node-js-path-module/>

[Node.js 路径模块](https://nodejs.org/api/path.html)是一个内置模块，可以帮助你以独立于操作系统的方式使用文件系统路径。如果您正在构建一个支持 OSX、Linux 和 Windows 的 CLI 工具，path 模块是必不可少的。

即使您正在构建一个只在 Linux 上运行的后端服务，path 模块仍然有助于在操作路径时避免边缘情况。

在这篇博文中，我将描述一些使用路径模块的常见模式，以及为什么应该使用路径模块而不是将路径转换成字符串。

## 在节点中加入路径模块

路径模块中最常用的函数是`path.join()`。`path.join()`函数将一个或多个路径段合并成一个字符串，如下所示。

```
const path = require('path');

path.join('/path', 'to', 'test.txt'); // '/path/to/test.txt'

```

您可能想知道为什么使用`path.join()`函数而不是使用[字符串连接](https://masteringjs.io/tutorials/fundamentals/string-concat)。

```
'/path' + '/' + 'to' + '/' + 'test.txt'; // '/path/to/test.txt'

['/path', 'to', 'test.txt'].join('/'); // '/path/to/test.txt'

```

有两个主要原因。

首先，对于 Windows 的支持。Windows 使用反斜杠(`\`)而不是正斜杠(`/`)作为路径分隔符。`path.join()`函数为您处理这个问题，因为`path.join('data', 'test.txt')`在 Linux 和 OSX 上都返回`'data/test.txt'`，在 Windows 上返回`'data\\test.txt'`。

其次，对于边缘案件的处理。使用文件系统路径时，会出现许多边缘情况。例如，如果您尝试手动连接两条路径，可能会意外地出现重复的路径分隔符。`path.join()`函数为您处理前导和尾随斜线，如下所示:

```
path.join('data', 'test.txt'); // 'data/test.txt'
path.join('data', '/test.txt'); // 'data/test.txt'
path.join('data/', 'test.txt'); // 'data/test.txt'
path.join('data/', '/test.txt'); // 'data/test.txt'

```

## 解析节点中的路径

path 模块还有几个用于提取路径成分的函数，比如文件扩展名或目录。例如，`path.extname()`函数以字符串形式返回文件扩展名:

```
path.extname('/path/to/test.txt'); // '.txt'

```

就像连接两条路径一样，获取文件扩展名比看起来要复杂得多。如果目录名中有一个`.`，或者如果路径是一个[点文件](https://medium.com/@webprolific/getting-started-with-dotfiles-43c3602fd789)，那么获取字符串中最后一个`.`之后的所有内容就不起作用了。

```
path.extname('/path/to/github.com/README'); // ''

path.extname('/path/to/.gitignore'); // ''

```

path 模块还有`path.basename()`和`path.dirname()`函数，分别获取文件名(包括扩展名)和目录。

```
path.basename('/path/to/test.txt'); // 'test.txt'

path.dirname('/path/to/test.txt'); // '/path/to'

```

你需要分机和目录吗？`path.parse()`函数返回一个包含路径的对象，该路径被分成五个不同的部分，包括扩展名和目录。`path.parse()`函数也是你获得没有任何扩展名的文件名的方法。

```
/*
{
  root: '/',
  dir: '/path/to',
  base: 'test.txt',
  ext: '.txt',
  name: 'test'
}
*/
path.parse('/path/to/test.txt');

```

## 使用`path.relative()`

像`path.join()`和`path.extname()`这样的函数涵盖了大多数使用文件路径的用例。但是路径模块有几个更高级的功能，比如`path.relative()`。

`path.relative()`函数采用两条路径，并将路径返回到相对于第一条路径的第二条路径。

```
// '../../layout/index.html'
path.relative('/app/views/home.html', '/app/layout/index.html');

```

当给定相对于一个目录的路径，但想要相对于另一个目录的路径时,`path.relative()`函数很有用。例如，流行的文件系统监视库 [Chokidar](https://www.npmjs.com/package/chokidar) 给出了相对于被监视目录的路径。

```
const watcher = chokidar.watch('mydir');

// if user adds 'mydir/path/to/test.txt', this
// prints 'mydir/path/to/test.txt'
watcher.on('add', path => console.log(path));

```

这就是为什么大量使用 [Chokidar](https://github.com/paulmillr/chokidar) 的工具，比如 Gatsby 或 webpack，也经常在内部大量使用`path.relative()`函数。

例如，这里的 [Gatsby 使用`path.relative()`函数来帮助同步一个静态文件目录](https://github.com/gatsbyjs/gatsby/blob/54d4721462b9303fed723fdcb15ac5d72e103778/packages/gatsby/src/utils/get-static-dir.ts#L50-L56)。

```
export const syncStaticDir = (): void => {
  const staticDir = nodePath.join(process.cwd(), `static`)
  chokidar
    .watch(staticDir)
    .on(`add`, path => {
      const relativePath = nodePath.relative(staticDir, path)
      fs.copy(path, `${process.cwd()}/public/${relativePath}`)
    })
    .on(`change`, path => {
      const relativePath = nodePath.relative(staticDir, path)
      fs.copy(path, `${process.cwd()}/public/${relativePath}`)
    })
}

```

现在，假设用户向`static`目录添加了一个新文件`main.js`。Chokidar 调用`on('add')`事件处理程序，将`path`设置为`static/main.js`。但是，当您将文件复制到`/public`时，您不会想要额外的`static/`。

调用`path.relative('static', 'static/main.js')`返回相对于`static`到`static/main.js`的路径，这正是你要把`static`的内容复制到`public`的目的。

## 跨操作系统路径和 URL

默认情况下，path 模块会根据节点进程运行的操作系统自动在 POSIX (OSX、Linux)和 Windows 模式之间切换。

但是，path 模块有办法在 POSIX 上使用 Windows path 模块，反之亦然。`path.posix`和`path.win32`属性分别包含路径模块的 POSIX 和 Windows 版本。

```
// Returns 'path\\to\\test.txt', regardless of OS
path.win32.join('path', 'to', 'test.txt');

// Returns 'path/to/test.txt', regardless of OS
path.posix.join('path', 'to', 'test.txt');

```

在大多数情况下，根据检测到的操作系统自动切换路径模块是正确的行为。但是使用`path.posix`和`path.win32`属性对于您总是想要输出 Windows 或 Linux 风格路径的测试或应用程序来说是有帮助的。

例如，一些应用程序使用像`path.join()`和`path.extname()`这样的函数来处理 URL 路径。

```
// 'https://api.mydomain.app/api/v2/me'
'https://api.mydomain.app/' + path.join('api', 'v2', 'me');

```

这种方法可以在 Linux 和 OSX 上运行，但是如果有人试图在 Azure Functions 上部署你的应用会怎么样呢？

你会以`'https://api.mydomain.app/api\\v2\\me'`结束，这不是一个有效的网址！如果您使用 path 模块来操作 URL，您应该使用`path.posix`。

## 结论

节点路径模块是处理文件系统路径的一个很好的工具，尤其是在连接和解析的时候。虽然您可以将文件路径作为字符串来操作，但是在处理路径时会遇到许多微妙的情况。

一般来说，您应该使用 path 模块来获取文件扩展名和连接路径，因为如果您将路径作为字符串来操作，很容易出错。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.