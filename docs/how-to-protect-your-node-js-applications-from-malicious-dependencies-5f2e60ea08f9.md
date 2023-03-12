# 如何保护 Node.js 应用程序免受恶意依赖

> 原文：<https://blog.logrocket.com/how-to-protect-your-node-js-applications-from-malicious-dependencies-5f2e60ea08f9/>

[Alberto Gimeno Follow](https://blog.logrocket.com/author/albertogimeno/) Ecosystem Engineer at GitHub. Sometimes I write about JavaScript, Node.js, and frontend development.

# 如何保护 Node.js 应用程序免受恶意依赖

## 

2018 年 12 月 7 日 3 分钟读完 1008

![](img/25acdbab710491c6cf0ca6c4a0bcf2e6.png)

你可能听说过[最近的一个事件](https://blog.npmjs.org/post/180565383195/details-about-the-event-stream-incident)，一个流行的 npm 包 [*事件流*](https://github.com/dominictarr/event-stream/issues/116#issue-382854428) ，包含了可能影响数千个应用程序(甚至更多)的恶意代码。).希望这次攻击是有针对性的，只影响一个特定的项目。

该库的原始作者是社会工程攻击的受害者，一个恶意黑客获得了发布权限。许多人认为原作者应该更加谨慎。

但这不是真正的问题。

为什么？

因为库的原始作者可能故意发布了恶意代码，所以任何拥有库的人都可以随时发布恶意代码。我们很多人都依赖于荣誉系统，希望没有人会发布恶意代码。

我们如何防止这种情况发生？

嗯，总是会有多种方式侵入系统，并向我们的应用程序中注入恶意代码。不仅通过依赖性，还通过无意的漏洞。

然而，我们仍然可以考虑如何防止这些事情发生，但更重要的是，我们需要考虑减轻其影响的方法。

[![](img/94b3e0f84b30cb0d321f60471481ab64.png)](https://logrocket.com/signup/)

### 预防

你现在可以采取一些预防措施:

*   **锁定你的依赖**。使用`package-lock.json`或`yarn.lock`来防止在部署时获得自动更新(在您的服务器中执行`npm/yarn install`时)。至少这样你会有更少的机会得到 npm 团队还没有清理的恶意更新。然而，这并不能阻止事件流对您的影响，因为恶意代码已经在 npm 注册表中存在数周了。但这可能会阻止你在 7 月份发生另一起事件。
*   **使用** [**npm 审计**](https://docs.npmjs.com/cli/audit) **、** [**Snyk**](https://snyk.io/) **和/或** [**GitHub 安全警报**](https://help.github.com/articles/about-security-alerts-for-vulnerable-dependencies/) 在您的任何依赖项可能包含安全漏洞时得到通知。

### 减轻

现在，一旦攻击被触发，我们如何减轻它的影响？

大多数攻击包括窃取数据、挖掘并将结果发送回服务器等。因此，您可以使用权限非常有限的用户来执行 Node.js:限制文件系统访问，配置 iptables 以限制应用程序只能连接到某些域，等等。问题是，在云服务时代，你可能无法在你的云提供商那里做到这一点。

在 Node.js 内部我们能做些什么吗？

Node.js 的贡献者已经开始考虑一个 [Node.js 安全模型](https://docs.google.com/document/d/1h__FmXsEWRuNrzAV_l3Iw9i_z8fCXSokGfBiW8-nDNg/view#heading=h.v9j0bjkph3tv)。因此，我们可以期待将来在 Node.js 中实现不同级别的安全性。

我个人喜欢一个权限系统，在这里你可以定义你需要在你的`package.json`中访问什么东西。例如:

```
{
  "permissions": {
    "fs": {
      "directories": {
        "$TEMP": "rw",
        "$SRC_ROOT": "r"
      }
    },
    "network": {
      "tcp": {
        "v4:*:$PORT": "LISTEN"
      }
    }
  }
}
```

这类似于现代浏览器中的内容安全策略。

当然，这只是我的建议，Node.js 安全模型的想法才刚刚开始评估。不要指望在不久的将来会实现。

那么，我们现在能做点什么吗？更具体地说，在不改变 Node.js 内部结构的情况下，我们能在 Userland 中做些什么呢？

答案是肯定的！

### 沙盒化你的应用——硬核方式

由于 Node.js 也遵循 JavaScript 的动态特性，我们能够*侵入*运行时。我们可以:

*   **劫持**`require()`电话并操纵里面的代码。这就是`ts-node/register`和`@babel/register`的工作方式。
*   **用 [vm 模块](https://nodejs.org/api/vm.html)在沙盒环境中运行**代码，通过自定义`require`函数阻止访问某些模块，或者包装核心模块阻止访问某些东西。

**或**

*   直接**覆盖**核心模块。让我们看看如何做到这一点:

我将展示一个覆盖`readFileSync`以防止访问特定目录中的文件的概念证明。在实践中，我们应该覆盖一些其他功能，我们也可以选择白名单，而不是黑名单某些目录。

但是作为一个例子，我只想防止恶意代码:

```
// malicious.js
const fs = require('fs')
const secrets = fs.readFileSync('/system/secrets.txt', 'utf8')
console.log(secrets);
```

我将实现一个覆盖`fs`核心模块的`cage.js`文件，我将拦截该函数并阻止访问`/system/`内部的文件:

```
// cage.js
const fs = require('fs')
const path = require('path')
const wrap = (module, name, wrapper) => {
  const original = module[name]
  module[name] = wrapper(original)
}
wrap(fs, 'readFileSync', (readFileSync) => (...args) => {
  const [filepath] = args
  const fullpath = path.resolve(filepath)
  if (fullpath.startsWith('/system/')) {
    throw new Error('You do not have permissions to access this file')
  }
  return readFileSync(...args)
})
// Prevent further changes
Object.freeze(fs)
```

瞧啊。就在那里。现在，如果我们直接运行恶意代码:

`node malicious.js`

我们将看到该文件的内容被打印到标准输出中。但是如果我们告诉 Node.js 首先像这样运行`cage.js`:

`node -r cage.js malicious.js`

我们将看到恶意代码无法访问文件的内容，并抛出了一个错误。

显然，这只是一个概念证明。下一步将是覆盖更多的函数，使其可配置，而不是硬编码文件路径，理想情况下，对其他核心模块也是如此。例如覆盖 [http(s)。请求](https://nodejs.org/api/http.html#http_http_request_options_callback)。

### 结论

*   我们的应用中的恶意代码(或只是易受攻击的代码)是一个日益严重的问题，因为我们的应用变得越来越复杂，依赖于更多的依赖项，使得攻击面越来越大
*   诸如 [npm 审计](https://docs.npmjs.com/cli/audit)、 [Snyk](https://snyk.io/) 和/或 [GitHub 安全警报](https://help.github.com/articles/about-security-alerts-for-vulnerable-dependencies/)之类的服务和工具非常有用，你现在就可以开始使用它们
*   我们需要减轻攻击的影响，Node.js 需要在这方面做些什么。然而，解决方案不是在不久的将来
*   如果你想走“硬核之路”，你可以！Node.js 足够灵活，允许你做一些疯狂的事情来保护自己。我们刚刚演示过了🙂

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.