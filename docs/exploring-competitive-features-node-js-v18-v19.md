# 探索 Node.js v18 和 v19 中的竞争特性

> 原文：<https://blog.logrocket.com/exploring-competitive-features-node-js-v18-v19/>

自从 2009 年发布以来，Node.js 一直是一个流行的 JavaScript 运行时。但是两个新运行时的出现， [Deno](https://deno.land/) 和 [Bun](https://bun.sh/) ，为它们与 Node 形成对比的新特性带来了大量宣传。

从远处看，Node.js 似乎停滞不前，没有任何令人兴奋的事情发生——但事实并非如此。最近的两个 Node.js 版本，v18 和 v19，提供了许多重要的特性:

*   对浏览器 API 的实验性支持，如 [Fetch](https://blog.logrocket.com/fetch-api-node-js/) 和 web streams API
*   一个实验性的内置测试跑步者
*   支持 Chromium 最新版本的 V8 引擎
*   对`watch`模式的实验性支持，它取代了像 [nodemon](https://www.npmjs.com/package/nodemon) 这样的工具

在本教程中，我们将探索 Node.js v18 和 v19 中的以下新功能:

## Node.js v18 功能

Node.js v18 于 2022 年 4 月 19 日发布，并在 2022 年 10 月 Node.js v19 发布时成为当前版本。当前版本意味着该版本获得了 Node.js 新版本的不间断特性。

Node.js v18 获得了`watch`模式特性，该特性在 v19 发布时在 Node v18 中被反向移植。2022 年 10 月 25 日，Node.js v18 被提升为 LTS(长期支持)，并将继续接受支持，直到 2025 年。

以下是 Node.js v18 中可用的一些功能。

### 内置提取 API

在 Node.js v18 之前，您不能使用 Node.js 中的 Fetch API 从 API 请求数据。你必须用 Node.js v18 安装 [node-fetch](https://github.com/node-fetch/node-fetch) 、 [Axios](https://axios-http.com/) 或其他第三方包，由于 v18 的实验性 Fetch API，你不再需要安装任何一个包，这是全球可用的。

让我们看看如何在 Node.js v18 中使用 Fetch API。首先，创建一个`getData.js`文件，并添加以下向 API 发送请求的函数:

```
async function fetchData() {
  const response = await fetch(
    "https://random-data-api.com/api/name/random_name"
  );
  if (response.ok) {
    const data = await response.json();
    console.log(data);
  }
}

fetchData();

```

保存文件内容，然后用`node`命令运行文件:

```
node getData.js

```

该命令运行时，输出如下所示:

```
(node:29835) ExperimentalWarning: The Fetch API is an experimental feature. This feature could change at any time
(Use `node --trace-warnings ...` to show where the warning was created)
{
  id: 6638,
  uid: '75026571-e272-4298-b2c0-c3e9e6363437',
  name: 'Candy Kane',
  ...
  prefix: 'Rep.',
  initials: 'LBS'
}

```

在输出中，Node.js 记录了一条警告，指出 Fetch API 是实验性的。在警告之后，我们看到 API 返回的 JSON 数据。

### 内置**测试转轮模块**

开发人员通常使用单元测试来测试软件组件。在 Node.js 的早期版本中，我们可以用 [`assert`库](https://nodejs.org/api/assert.html)编写简单的测试。但是随着我们的测试越来越大，我们组织测试和编写描述性消息的需求也越来越大。

作为一种解决方案，测试运行程序如 [Jest](https://jestjs.io/) 、 [Jasmine](https://jasmine.github.io/) 和 [Mocha](https://mochajs.org/) 出现了，并且已经成为单元测试的首选工具。

随着 Node.js v18 的发布，Node.js 中现在包含了一个测试运行器，可以通过以下方式访问:

```
import test from 'node:test';

```

注意，我们使用`node:`方案来导入模块。您也可以使用 CommonJS:

```
const test = require('node:test')

```

让我们学习如何使用它。首先，使用以下内容初始化 npm:

```
npm init -y

```

在您的`package.json`文件中，启用 ES 模块:

```
{
  ...
  "license": "ISC"
  "type": "module",
}

```

接下来，创建一个`math.js`文件，并添加一个返回两个数相加结果的函数:

```
const sum = (a, b) => {
  return a + b;
};

export default sum;

```

要使用 Node.js 测试运行器测试该函数，请创建一个包含以下内容的`test.js`文件:

```
import test from "node:test";
import assert from "assert/strict";
import sum from "./math.js";

test("Sum function", async (t) => {
  await t.test("It should add two numbers", () => {
    assert.equal(sum(2, 2), 4);
  });
  await t.test("It should not subtract numbers", () => {
    assert.notEqual(sum(3, 2), 1);
  });
});

```

在第一行中，我们导入测试运行程序。在第二行中，我们导入了`assert`库，随后，导入了`math.js`文件中的`sum()`函数。

之后，我们创建一个包含两个子测试的测试用例，测试`sum()`函数是否正常工作。

现在，运行测试:

```
node test.js

```

您的输出将如下所示:

```
TAP version 13
# Subtest: Sum function
    # Subtest: It should add two numbers
    ok 1 - It should add two numbers
      ---
      duration_ms: 1.171389
      ...
    # Subtest: It should not subtract numbers
    ok 2 - It should not subtract numbers
      ---
      duration_ms: 0.279246
      ...
    1..2
ok 1 - Sum function
  ---
  duration_ms: 5.522232
  ...
1..1
# tests 1
# pass 1
# fail 0
# cancelled 0
# skipped 0
# todo 0

```

在输出中，我们可以看到 Node.js 有运行测试的描述消息。

### Web 流 API 支持

Web Streams API 是 Node.js 中的一个实验性特性，它允许您将一个大文件(如视频或文本文件)分解成可以逐渐使用的小块。这有助于避免内存问题。在 Node.js 的旧版本中，您可以使用 [Node.js 流](https://blog.logrocket.com/working-node-js-streams/)来消耗大文件。但是浏览器中的 JavaScript 应用程序没有这个功能。后来， [WHATWG](https://streams.spec.whatwg.org/) 定义了 Web Streams API，它现在已经成为 JavaScript 应用中的流数据标准。

Node.js 直到 v18 才支持这个 API。在 v18 中，所有的流 API 对象，比如`ReadableStream`、`WritableStream`和`TransformStream`，都是可用的。要了解更多关于如何使用 Streams API 的信息，请查看[文档](https://nodejs.org/api/webstreams.html)。

### 使用快照功能构建二进制文件

另一个令人兴奋的特性是能够构建单个可执行的 Node.js 二进制文件。在 Node.js v18 之前，构建 Node.js 二进制文件的唯一方法是使用第三方包，比如 [pkg](https://github.com/vercel/pkg) 。

但是现在，您可以利用实验性的快照标志`--node-snapshot-main`来构建二进制文件。有关该功能如何工作的更多细节，请参见[本教程](https://blog.logrocket.com/snapshot-flags-node-js-v18-8/)。

### V8 发动机升级至 v10.1

Node.js 构建在 V8 引擎之上，由 Google 创建，并为 Chromium 执行 JavaScript 而维护。在每个版本中，它都引入了新的特性和一些性能改进，这些都体现在 Node.js 中。

Google 发布了 V8 10.1，引入了一些新的数组方法，比如`findLast()`和`findLastIndex()`，还有 [`Intl.supportedValuesOf(code)`](https://v8.dev/blog/v8-release-99#intl-enumeration) 。V8 引擎还为 [`Intl.Locale` API](https://v8.dev/blog/v8-release-99#intl.locale-extensions) 增加了新方法，并优化了[类字段和私有方法](https://v8.dev/blog/faster-class-features)。

## `watch`模式和其他 Node.js v19 特性

Node.js v19 发布于 2022 年 10 月 18 日。由于 19 是奇数，它将永远不会被提升到 LTS，但将继续获得支持，直到 2023 年 4 月发布新的偶数版本 Node.js。

虽然与 Node.js v18 相比，Node.js v19 没有发布很多功能，但它也向过去的 Node 版本提供了一个最受欢迎的功能:`watch`模式。

当您在 Node.js 中创建和启动服务器，然后对该文件进行更改时，Node.js 不会自动获取新的更改。你要么需要重启服务器，要么使用像 [nodemon](https://blog.logrocket.com/configuring-nodemon-with-typescript/) 这样的工具，当它检测到新的变化时，会自动重新运行文件。

随着 Node.js v19 的发布，这不再是必要的。Node v19 以及 Node v 18 . 11 . 0 现在能够在使用`node --watch`功能检测到新变化时自动重启进程，该功能目前处于试验阶段。

要在监视模式下运行文件，请使用`--watch`标志:

```
node --watch index.js

```

当您编辑`index.js`文件时，您将看到进程自动重新启动，新的更改在不停止服务器的情况下得到反映。

如前所述，这个特性也被反向移植到 Node.js ≥ v18.11.0，这意味着如果这是你唯一需要的特性，你就不必使用 Node.js v19。

### 默认为 HTTP(S)/1.1 `KeepAlive`

Node.js 使用一个`[http.globalAgent](https://nodejs.org/api/http.html#class-httpagent)`用于输出 HTTP 连接，使用`[https.globalAgent](https://nodejs.org/api/https.html#class-httpsagent)`用于输出 HTTPS 连接。这些代理确保 TCP 连接的持久性，以及 HTTP 客户端可以为多个请求重用连接。

通过将 HTTP 1.1 [`keepAlive`](https://en.wikipedia.org/wiki/Keepalive) 选项设置为`true`，可以将代理配置为重用连接；否则，将它设置为`false`以避免重用连接，这会使事情变得更慢。

对于 Node.js 版本≤18，HTTP/HTTPS 的传出连接将`keepAlive`选项设置为`fal``se`，因此连接不会被多个请求重用，从而导致性能降低。在 Node.js v19 中，`keepAlive`选项现在被设置为`true`，这意味着您的出站连接将会更快，而无需进行任何配置。

我们来验证一下。假设你用的是 [nvm](https://github.com/nvm-sh/nvm) ，可以安装 Node.js ≤ v18，临时切换到它:

```
nvm install v18.12.1
node -v
// Output
// v18.12.1

```

创建一个`checkHttpAlive.js`文件，并添加以下代码来检查`http.globalAgent`:

```
const http = require('node:http');
console.log(http.globalAgent);

```

您的输出将如下所示:

```
// Output
Agent {
  ...
  keepAliveMsecs: 1000,
  keepAlive: false,  // this is the keepAlive option
  ...
}

```

在输出中，您会注意到节点 v18 上的`keepAlive`默认设置为`false`。

我们来对比一下 Node.js v19。使用 nvm 将 Node.js 版本切换到 v19:

```
nvm install v19.0.1
node -v
// output:
// v19.0.1

```

再次运行`checkHttpAlive.js`文件:

```
node checkHttpAlive.js

```

输出将与以下内容匹配:

```
// output
Agent {
  ...
  keepAliveMsecs: 1000,
  keepAlive: true,
  ...
}

```

在输出中，您可以看到 Node.js v19 中的`keepAlive`选项默认设置为`true`。

### **V8** **发动机升级至 10.7**

Node.js v19 的 V8 引擎已升级至 10.7 版本。它没有附带很多特性——它只是在 JavaScript API 中添加了`Intl.NumberFormat`特性。

`Intl.NumberFormat`将一个数字国际化为一种货币。一个例子:

```
> new Intl.NumberFormat('en-US', { style: 'currency', currency: 'GBP' }).format(3392.10)
'£3,392.10'     // output

```

## 结论

在本文中，我们探索了 Node.js v18 和 v19 中很酷的特性。首先，我们看了 v18 中的新特性，包括内置的 Fetch API、新的测试运行器和快照特性、`watch`模式，以及对 Web Streams API 的支持。然后我们查看了 Node v19 中的新特性，包括`watch`模式和 HTTP 1.1 `keepAlive`特性。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

尽管 Node.js 的新特性令人兴奋，但这些特性中的大部分已经存在于 Bun 和 Deno 中。运行时还包括一些有用的特性，比如原生类型脚本支持、web sockets API 以及比 Node.js 更快的执行速度

如果你不确定用哪个 Node.js 版本，我会推荐 v18。它的支持将持续到 2025 年，不像 Node v19，其支持将于明年结束。如果您想更深入地了解这些特性，请参考[文档页面](https://nodejs.org/en/docs/)。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.