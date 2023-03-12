# 如何组织你的 nut.js 开发工作流

> 原文：<https://blog.logrocket.com/how-to-organize-your-nut-js-development-workflow/>

很多人声称你可以用 JavaScript 构建任何东西。web 开发的框架数不胜数，从 Node 等后端工具到 React 等面向移动的库。您甚至可以使用 Electron 构建桌面应用程序。但是用 JavaScript 执行桌面自动化有可能吗？

确实是！

使用节点插件，可以使用 OS APIs 来模拟输入或检索屏幕内容。

然而，为三个主要平台构建和发布现成的桌面自动化框架需要精心的开发设置。在本教程中，我将向你展示我如何组织我在 [nut.js](https://github.com/nut-tree/nut.js) 上的工作。我们将触及以下话题。

*   分支策略
*   以打字打的文件
*   生成文档
*   测试
*   CI/CD 设置
*   分支保护

我们开始吧！

## 回购设置

当谈到工具时，您还应该解决存储库的设置问题。虽然在一个分支上完成所有的开发是可能的，但是我是 GitFlow 的超级粉丝。我全职从事软件开发，所以我能花在个人项目上的时间很大程度上取决于我的工作量。每当我晚上还有精力或者周末有时间编码的时候，我会捡起一些旧的任务或者开始一项新的任务，如果我喜欢的话。遵循 GitFlow，所有的开发都发生在特性分支上，所以不管我目前在做什么，我总是能够无缝地切换到另一个任务。开发和主分支仅在以下情况下发生变化:

*   一个特征完成了
*   发布了一个新的稳定版本

这对贡献者来说是一个巨大的好处，因为他们总是能够检查出硕士或开发开始。我不需要告诉你，当你克隆一个项目，却发现它处于不一致的状态，并且不能构建时，这是多么令人沮丧。

## 以打字打的文件

这部分可能有点自以为是，但是 TypeScript 变成了我开始新项目时的默认。它的类型系统真的很有用，但又不具侵入性——对于日常使用来说是一个很好的组合。

虽然不是必需的，但我倾向于调整默认的`tsconfig.json`。

```
{
  "compilerOptions": {
    "outDir": "./dist",
    "declaration": true,
    "declarationMap": true,
    "sourceMap": true,
    "strict": true,
    "noImplicitAny": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noImplicitReturns": true,
    …
  },
  “include”: [
    “lib/**/*.ts”,
    “index.ts”
  ],
  “exclude”: [
    “node_modules”
  ]
}

```

TypeScript 配置附带以下脚本。

```
…
“scripts”: {
  “clean”: “rm -rf dist”,
  “compile”: “npm run clean && tsc -p .”,
  “prepublishOnly”: “npm run compile”,
  …
},
…

```

最终结果是一个完全符合我需求的设置。简单，但功能强大！💪

## 证明文件

当开发一个框架时，你可能倾向于只关注特性，但是如果你想让人们使用你的工作，你必须提供文档和样本。幸运的是，有多种解决方案可以让您托管您的样本和/或文档。

nut.js 使用 [TypeDoc](https://typedoc.org/) 自动生成 API 文档，包括来自文档注释的简短示例:

```
/**
 * {@link type} types a sequence of strings or single {@link Key}s via system keyboard
 * @example
```typescript
 *    await keyboard.type(Key.A, Key.S, Key.D, Key.F);
 *    await keyboard.type("Hello, world!");
```
 * @param input Sequence of strings or {@link Key}s to type
 */

```

![](img/83a8c82ef9c841c36ff7125e92f0fd21.png)

Typedoc is configured to output generated documentation to a `docs` folder in our repository.

```
"scripts": {
  ...
  "typedoc": "typedoc --options ./typedoc.js --out ./docs lib/"
},

```

GitHub Pages 能够使用主分支上的这个`docs`文件夹作为源。只需点击几下鼠标，文档就可以在 https://nut-tree.github.io/nut.js/的 T2 直播。

对于示例，我在 [nut-tree/trailmix](https://github.com/nut-tree/trailmix) 使用一个单独的存储库。这是一个 [Lerna monorepo](https://lerna.js.org) ，它包含了 nut.js 每个(当前)主要方面的包。遵循 monorepo 方法可以很容易地更新和测试 nut.js 的新版本，因为依赖关系是在根级别管理的，这有助于保持样本最新。由于所有的样本都被设计为 Jest 测试，它也使您能够尽早发现错误。

## 测试

测试是 nut.js 开发中最关键的部分。我们需要确保 nut.js 可以在多个平台上构建和运行。唯一可行的方法是高度依赖自动化。可靠地自动化流程的唯一方法是建立适当的测试来验证我们系统的行为。

我最喜欢的测试框架是 [Jest](https://jestjs.io/) 。它拥有我在测试框架中寻找的所有特性，并使用 [ts-jest](https://kulshekhar.github.io/ts-jest/) 预设与 TypeScript 配合得很好。

```
module.exports = {
  collectCoverageFrom: [
    "index.ts",
    "lib/**/*.ts",
    "!lib/**/*.spec.ts",
    "!<rootDir>/node_modules/",
  ],
  preset: "ts-jest",
  testEnvironment: "node",
  testMatch: process.env.E2E_TEST ?
    ["**/__tests__/?(e2e)/**/*.[jt]s?(x)", "**/?(*.)?(e2e.)+(spec|test).[jt]s?(x)"] :
    ["**/__tests__/!(e2e)/**/*.[jt]s?(x)", "**/!(*.e2e.*)+(spec|test).[jt]s?(x)"],
  testPathIgnorePatterns: [
    "/node_modules/",
    "/dist/",
  ],
};

```

这个单一的配置文件启用了 Jest 的 TypeScript 支持，允许我收集我感兴趣的文件的覆盖率，并分离了两种测试。nut.js 包括单元测试和 E2E 测试，单元测试可以在任何时候运行，而后者应该在具有特定 UI 的 Docker 容器中运行。只有设置了`E2E_TEST`环境变量，E2E 测试才会包含在测试运行中。

测试只是通过它们的文件名来区分。`feature.class.spec.ts`包含一个特性的单元测试，而`feature.class.e2e.spec.ts`包含一个完整的 E2E 测试，它依赖于一个固定的 UI。

在 CI 上，所有测试都在 Docker 容器中执行，以运行所有可用的测试。

## 持续集成和部署

nut.js 目前使用两个 CI 系统:

1.  [特拉维斯-CI](https://travis-ci.com/)
2.  [主持人](https://www.appveyor.com/)

总共有 16 个 CI 作业在三个受支持的平台(Windows、macOS 和 Linux)上针对五个受支持的节点版本(10、11、12、13、14)运行测试。第 16 个作业发布快照和稳定版本。

在 Travis 上，nut.js 强加了一个三阶段设置。第一阶段针对当前的节点 LTS 版本进行构建和测试。如果成功， [SonarCloud](https://sonarcloud.io/dashboard?id=nut-tree%3Anut.js) 用于静态代码分析。

![](img/5193e1f26c183df909f537e19e7dd423.png)

In the next stage, we’ll test against the remaining combinations of platform and node versions.

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

我们将运行最后的`deploy`阶段，以防在`develop`分支上标记提交或构建。如果我们推出一个新标签，一个稳定的版本会在默认的`@latest`标签下发布。`develop`分支的构建将在`@next`标签下进行快照发布。因此，每当一个功能完成并合并到`develop`时，就会发布一个新的快照版本。快照对于快速反馈非常有用，因为用户不必等待下一个稳定版本来测试新特性。

## 分支保护

拥有适当的测试和 CI 设置的一个好处是[分支保护](https://help.github.com/en/github/administering-a-repository/about-protected-branches)。我们希望保持代码的整洁，不希望在合并拉取请求后出现任何意外。启用分支保护后，我们可以在合并 PR 之前强制通过某些状态检查。

Sonarcloud 和 Travis/Appveyor 是可用的状态检查，因此，如果拉请求构建失败或错过了我们的质量关，PR 将不会被合并(除非由 repo 所有者或管理员强制)。

您可能会想，这些设置只有在与他人协作时才有意义，但是单独开发人员也可以从这些特性中受益。

## 结论

我刚才向您介绍的设置已经随着时间的推移而发生了变化。它让我自信地维护 [nut.js](https://github.com/nut-tree/nut.js) 。重构被测试覆盖，发布被自动化，文档被版本控制和自动化。

每个开发人员都有自己的设置，但是如果您刚刚开始熟悉 nut.js 或者正在寻找一种更好的组织方式，我希望本演练能够给您一些启发和坚实的基础。

如果你已经熟悉了 nut.js，你的设置会是什么样的？

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

## 您是否添加了新的 JS 库来提高性能或构建新特性？如果他们反其道而行之呢？

毫无疑问，前端变得越来越复杂。当您向应用程序添加新的 JavaScript 库和其他依赖项时，您将需要更多的可见性，以确保您的用户不会遇到未知的问题。

LogRocket 是一个前端应用程序监控解决方案，可以让您回放 JavaScript 错误，就像它们发生在您自己的浏览器中一样，这样您就可以更有效地对错误做出反应。

[![LogRocket Dashboard Free Trial Banner](img/e8a0ab42befa3b3b1ae08c1439527dc6.png)](https://lp.logrocket.com/blg/javascript-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/javascript-signup)

[LogRocket](https://lp.logrocket.com/blg/javascript-signup) 可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

自信地构建— [开始免费监控](https://lp.logrocket.com/blg/javascript-signup)。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.