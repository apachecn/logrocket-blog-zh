# 使用 es 模块替代 Node.js 中的 __dirname

> 原文：<https://blog.logrocket.com/alternatives-dirname-node-js-es-modules/>

所以，你已经听取了所有的建议，坐下来迁移你的代码或者学习一个新的标准，但是你有问题。ES 模块(也称为 ESM)就在这里，但是使用它们并不像将所有的`require()`表达式迁移到`import`语句中那么简单。

大约在 2019 年底，在 Node 13 中的 Node 中添加了 ES 模块。Node 12——最后一个没有 ESM 的版本*——将于 2022 年 4 月“寿终正寝”,所以:假设您的系统正在升级，没有原生支持的地方会越来越少。*

## 救命，我不见了`__dirname`

是啊！这是帖子的重点。

如果你正在编写一个扩展名为`mjs`的 ES 模块(强制 Node 进入 ESM 模式)，或者在你的`package.json`文件中设置了`{"type": "module"}`，或者你正在编写类型脚本并以其他方式运行代码…你可能会遇到这个错误:

```
    ReferenceError: __dirname is not defined in ES module scope 

```

类似地，提供给 CommonJS 代码的其他内置全局变量也不会存在。这些是`__filename`、`exports`、`module`和`require`。

要取回`__dirname`(和`__filename`)，您可以将这样的代码添加到任何需要它的文件的顶部:

```
    import * as url from 'url';
    const __filename = url.fileURLToPath(import.meta.url);
    const __dirname = url.fileURLToPath(new URL('.', import.meta.url));

```

太好了！

## 如何让`__dirname`回来？有什么警告吗？

我很高兴你继续读下去！上述代码之所以有效，是因为 ESM 提供了一个新的标准化全局变量`import.meta.url`。当运行模块代码时，它在所有浏览器和节点中都可用，它将是一个类似于:

`"file:///path/to/the/current/file.js"`

```
  "file://C:\Path\To\current\file.js"   // windows without WSL
    "https://example.com/source.js"   // if this is browser JS

```

这将使节点与浏览器中的 ESM 内联。作为 JS 开发人员，我们需要这个新的全局变量，因为我们的代码可以在本地或远程的任何地方运行，标准的 URL 格式为此提供了支持。当然，您可能记得 Node 不能直接从 web URL 导入，但是像 Deno 这样的新工具可以。

在上面的代码中创建的新的`__dirname`和`__filename`变量就像在 CommonJS 中一样工作——如果您传递它们，它们仍然具有原始文件的字符串名称。它们不是突然扮演指向目录或文件名角色的变量。(这是一个很长的说法，你可能不想`export`他们。)

但是请注意，虽然上面的助手`fileURLToPath`是一个快速的解决方案，如果您只是想升级旧代码，请注意，它不是标准化的，如果您的代码在 web 上共享，它将不起作用。

公平地说，这并不是一个新问题:`__dirname`和`__filename`也不是共享的，但是`import.meta.url`是！所以，直接用它(往下读！)实际上让我们更加多才多艺。

## 你的目标是什么？

为什么在我们的脚本中有`__dirname`和`__filename`很有用？

它能够围绕我们的代码与世界互动。这些有助于导入其他源文件，或者在与我们的路径相关的路径中操作。

例如，您可能有一个与代码对等的数据文件(“yourprogram.js”需要导入“helperdata.txt”)。这可能就是为什么你想要`__dirname`而不是`__filename`的原因:它更多的是关于你的文件在哪里，而不是文件本身。

但是！可以使用内置对象`URL`和许多 Node 的内置函数来实现各种目标，而不必简单地假装我们正在构建 CommonJS 代码。

在我们开始之前，请注意一些奇怪的事情:

*   URL 是可变的，我们通过传递(a)一个描述变化的字符串和(b)一个先前的`URL`实例来创建一个新的 URL。(从较小的变化部分开始的顺序可能会让人出错)
*   `import.meta.url`值不是`URL`的*实例*。它只是一个字符串，但是可以用来构造一个，所以下面所有的例子都需要我们创建新的对象

`import.meta.url`是一个简单的字符串有几个原因，其中之一是`URL` [是可变的](https://developer.mozilla.org/en-US/docs/Glossary/Mutable)。我们还要感谢 JS 在网络上的遗产——如果你改变了`window.location.pathname`，你就修改了页面的 URL 来加载新的页面。

这样，`window.location`本身仍然是同一个对象。在 ES 模块中，“改变”URL 是没有意义的——脚本是从一个地方加载的，一旦发生，我们就不能重定向它。

*注意，`window.location`实际上不是一个 URL，但它的行为基本上像一个 URL。*

### 目标:加载一个文件

通过构造一个新的 URL，我们可以在文件所在的目录中找到该文件的路径:

```
    const anotherFile = new URL('helperdata.txt', import.meta.url);
    console.info(anotherFile.toString());  // prints "file:///path/to/dirname/helperdata.txt"

```

好吧，这很好，但你可能会指出:我仍然有一个`URL`对象，而不是一个字符串，它仍然以`file:///`开头。

嗯，秘密在于 Node 的内部函数实际上可以很好地处理一个`file://`:

```
    import * as fs from 'fs';
    const anotherFile = new URL('helperdata.txt', import.meta.url);
    const data = fs.readFileSync(anotherFile, 'utf-8');

```

太好了！现在，您已经加载了一些数据，而没有求助于`path`助手库。

### 目标:动态导入代码

就像读取相邻文件一样，我们可以将一个`URL`传递给动态`import()` 助手:

```
    const script = 'subfolder/other.mjs';
    const anotherScript = new URL(script, import.meta.url);
    const module = await import(anotherScript);

```

同样，我们有一个`URL`对象，它被`import`愉快地理解。

### 目标:执行类似路径的操作和陷阱

在查找当前目录或导航文件夹时，URL 对象的工作方式与`path`助手略有不同。`path.dirname`助手就是一个很好的例子——它大致的意思是“为我找到当前路径的父路径”请继续阅读:

```
    path.dirname('/home/sam/testProject/')   // '/home/sam/'
    path.dirname('/home/sam/testProject')    // '/home/sam/'
    path.dirname('/home/sam/')    // '/home'

```

重要的是，注意上面的`path`并不真正关心后面的`/`——它只关心后面是否有什么东西。

为了在 URL 上执行类似的操作，我们添加了字符串`.`或`..`(意思是“进入一个目录”)，但是它的结果与`path.dirname`略有不同。看一看:

```
    // if import.meta.url is "/my/src/program.js"
    const dirUrl = new URL('.', import.meta.url);  // "file:///my/src/"
    const dirOfDirUrl = new URL('.', dirUrl);  // "file:///my/src/" - no change
    const parentDirUrl = new URL('..', import.meta.url);  // "file://my/"
    const parentDirOfDirUrl = new URL('..', dirUrl);  // "file://my/" - same as above

```

我们在这里学到的是,`URL`关心尾部的斜杠，将`.`添加到一个目录或该目录中的一个文件将总是给你一个一致的结果。如果您进入子文件夹，也会有类似的行为:

```
    const u1 = new URL('subfolder/file.txt', import.meta.url);   // "file:///my/src/subfolder/file.txt"
    const u1 = new URL('subfolder/file.txt', dirUrl);   // "file:///my/src/subfolder/file.txt"

```

我认为这比 Node `path.dirname`的内置特性更有帮助——因为文件和目录之间有很大的区别。

当然，您的观点可能会有所不同——也许您想尽快回到简单的字符串——这很好，但是理解`URL`的语义是值得的。这也是我们在网上可以得到的东西，这些规则都适用于`https://`计划，就像它们适用于`file://`一样。

## `URL`和`path`字符串之间的互操作性

虽然我很想告诉你`URL`是如何工作的，以及它的所有细微差别，但是作为可能与文件系统交互的开发人员，我们最终总是希望回到纯粹、简单的`path`字符串——比如“/Users/Sam/path/to/your/file . js”。你不能(轻易地)使用`URL`来生成文件之间的相对路径，就像使用`path.relative`一样，而且 URL 本身必须是绝对的(你不能处理像“relative/path/to/file.js”这样的非根路径)。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

您可能知道 URL 有一个名为`pathname`的属性。在 web 上，它包含您正在打开的域之后的部分。但是对于`file://`路径，这包含了完整的路径——例如，`file:///path/to/file`可能是“/路径/到/文件”。

但是等等！直接使用它实际上是危险的，原因有两个，这就是为什么我在这篇文章的顶部谈论使用 Node 的内置助手`url.fileURLToPath`。这为我们解决了两个问题:

*   文件名中的空格不能与`pathname`一起使用——在网络上，它们被编码为`%20`，这是你的电脑无法理解的
*   窗口路径没有用`pathname`规范化

所以，不要只使用 URL 的`pathname`,而是使用我一直在文件顶部介绍的助手:

```
    const __dirname = url.fileURLToPath(new URL('.', import.meta.url));
    // or
    const pathToFile = url.fileURLToPath('file:///some/path/to/a/file');

```

## 最后的想法

在写这篇文章的时候，我有几个在其他地方都不合适的想法:

*   ES 模块模式下的 Node 仍然提供`process.cwd()`，这只是一个常规的路径——就像“/foo/bar”——它现在不是一个`file:///foo/bar/`，只是因为你处于模块模式
*   您可以使用`url.filePathToURL`助手将字符串转换回 URL 它的工作方式正好相反。但是，您可能不需要经常这样做

感谢阅读！如果你有任何问题，请打电话给我。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.