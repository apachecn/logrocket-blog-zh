# Babel vs. TypeScript:为您的项目选择正确的编译器

> 原文：<https://blog.logrocket.com/babel-vs-typescript/>

**编者按:** *本帖最后更新于 2021 年 7 月 30 日。随着[巴别塔 7.15](https://babeljs.io/blog/2021/07/26/7.15.0) 的更新，一些部分被重写以保持准确。*

自 Babel 7 于 2018 年发布以来，使用 Babel 的项目可以使用 TypeScript，而无需使用 TypeScript 编译器来复杂化他们的构建。

但是使用 Babel 和使用 TypeScript 编译器有什么区别呢？你的下一个项目应该使用 Babel 还是 TypeScript？

[![](img/94b3e0f84b30cb0d321f60471481ab64.png)](https://logrocket.com/signup/)

## 巴别塔和打字稿的区别

使用 TypeScript 和将 TypeScript 与 Babel 一起使用有一些主要区别。

在这篇文章中，我们将看看五个最重要的区别。

### 1.Babel 中没有类型检查

巴贝尔不关心你的花式打字稿类型。它只是把它们扔进垃圾桶，而不检查它们是否正确。以下示例使用 Babel 编译时没有任何错误或警告，但使用 TypeScript 时没有:

```
const myCoolString : string = 9;
```

`9` is definitely not a string Babel.

移除类型对于您希望代码编译的快速原型开发来说是非常好的，即使您的类型并不合适。

如果你正在努力输入东西，在某些时候你可能会想检查它们是否正确。幸运的是，这不是什么大事。您可以让您的编辑器来处理它，或者运行`tsc --noEmit`，它在不编译任何东西的情况下对您的项目进行类型检查。

### 2.巴贝尔做不到`const enums`

默认情况下，TypeScript 一次编译整个项目，而 Babel 一次只编译一个文件。

以前，这意味着 Babel 不支持需要读取多个文件的 TypeScript 特性——比如`const enums`。

然而，自从 2021 年 7 月 26 日发布的巴别塔 7.15(T1)以来，情况就不一样了。这实质上意味着，如果您使用的是最新版本的 Babel，您应该能够编译所有有效的 TypeScript 代码库。

### 3.装饰者和元数据:TypeScript 有优势

TypeScript 对于 decorator 党来说有点早(如果你不确定 decorator 是什么，[这是对 decorator 的很好的介绍](https://blog.logrocket.com/a-practical-guide-to-typescript-decorators/))。在 TypeScript 实现了 decorator 之后，decorator 提案已经更改了多次，仍然没有最终确定。

这意味着目前 [ECMAScript](https://blog.logrocket.com/how-to-use-ecmascript-modules-with-node-js/) 规范和 TypeScript 在装饰者应该如何表现的问题上意见不一致。Babel 的插件遵循 ECMAScript 规范，这意味着 Babel 不会像 TypeScript 那样编译 decorators。幸运的是，Babel 有一个`legacy`模式来编译带有旧行为的 decorators。

只需添加巴别塔插件

`“@babel/plugin-proposal-decorators”`

将`legacy`选项设置为`true` **。**

还有一个我们应该讨论的 TypeScript decorators 特性:`emitDecoratorMetadata`。

TypeScript 通常会清除所有类型信息，使其在运行时不存在。`emitDecoratorMetadata`是一个为应用了装饰器的类和方法保留类型的特性。

在运行时拥有该类型允许我们做各种各样有趣的事情，比如依赖注入和将 TypeScript 类型映射到 SQL 数据库中的类型。

该特性在这两个领域得到了大量使用，诸如 TypeORM、TypeGoose、inversifyJS 等库，甚至 [Angular 的依赖注入系统](https://blog.logrocket.com/how-to-use-ecmascript-modules-with-node-js/)都依赖于该特性。

由于 Babel 不关心你的类型信息，这个特性需要一个自定义插件，[Babel-plugin-transform-typescript-metadata](https://www.npmjs.com/package/babel-plugin-transform-typescript-metadata)。添加这个插件和前面提到的`plugin-proposal-decorators`应该会让 Babel 的特性在 decorators 方面与 TypeScript 平起平坐。

### 4.巴别塔擅长自定义转换

Babel 比 TypeScript 更具可扩展性。有大量的插件可以优化你的代码，帮助你去掉不用的导入、内联、常量等等。

虽然 TypeScript 有自己的 Transformer API，允许自定义转换，但 Babel 生态系统在插件选择上更丰富，也更容易访问。

如果您需要自定义转换，您将需要使用 Babel。好消息是，大多数 TypeScript 工具都允许您使用 TypeScript，然后通过 Babel 运行代码，这样可以两全其美。但是这显然会增加构建链的复杂性。

### 5.TypeScript 和 Babel 具有相似的性能

比较 Babel 和 TypeScript 的性能是很困难的，并且可能不会给你完整的描述。执行类型检查的 TypeScript 肯定会比 Babel 慢，因为有额外的步骤。

为了达到大致相同的速度，您可以通过使用类似于[fork-ts-checker-web pack-plugin](https://github.com/TypeStrong/fork-ts-checker-webpack-plugin)的东西来缓解这种减速，它在一个进程中运行不带类型的编译，在一个后台进程中运行类型检查。

当然，任何试图配置 webpack 的人都知道，JavaScript 工具链非常复杂。你有源映射插件，缓存，选择你应该使用多少线程——还有很多。没有一个简单的基准可以考虑全部情况，但是如果你期望使用 Babel 比使用 TypeScript 编译器有多倍的提高，你将不得不在其他地方寻找你的性能提升——[也许是你的 bundler](https://blog.logrocket.com/benchmarking-bundlers-2020-rollup-parcel-webpack/) ？

## Babel 和 TypeScript 你该选哪个？

在这一点上，TypeScript 和 Babel 在您的构建链中扮演的角色大致相同。Babel 现在完全支持`const enums`、decorator 和 decorator 元数据。

使用 Babel 的唯一缺点是，您需要将类型检查作为一个单独的过程来运行。

如果你已经有了一个适合你的构建管道，我看不出有什么令人信服的理由去转换。然而，如果你开始一个项目，我可能会倾向于使用 TypeScript 编译器，可能通过类似于 [ts-loader](https://github.com/TypeStrong/ts-loader) 的东西。然后，如果你发现你需要一些只有 Babel 提供的转换，你可以把 transpiled 的 TypeScript 输出传递给 Babel。

对我来说，这有点太复杂了，但是嘿——没有人说过 JavaScript 构建工具链是容易的。

你有使用 TypeScript 和 Babel 的经验吗？我很想听听。 [*@GeeWengel*](https://twitter.com/GeeWengel)

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.