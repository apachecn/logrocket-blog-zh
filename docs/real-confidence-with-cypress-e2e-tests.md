# 真正的信心与柏树 E2E 测试-日志火箭博客

> 原文：<https://blog.logrocket.com/real-confidence-with-cypress-e2e-tests/>

让我给你描述一下。你正在开发一个你不太了解的应用程序，你想确保你所做的影响深远的改变没有破坏任何东西。QA 部门正在休假，他们讨厌做那些全面的回归测试。此外，您需要将这些完全安全的更改立即发布给用户！

从一个报酬过高的开发人员到另一个，我已经多次中断生产，因为我没有想到一些奇怪的边缘情况，或者在我按下红色的大部署按钮之前没有费心运行所有的手动测试。这太麻烦了，我可能无论如何都不会发现这个错误，而且有时候我就是懒。

我以前听说过端到端测试，但是它们总是被认为是我们忽略的那些古怪的、难以运行的、不可能保持最新的怪物。Selenium 是免费的，而且很糟糕，市场上的其他选择也要几千美元(可能也好不到哪里去)。所以自动化那些无聊的回归测试也不是一个真正的选择。

所以，想象一下当我开始听说这个新工具可以使端到端测试变得轻而易举时，我的怀疑态度。它叫做 [Cypress](https://www.cypress.io/) ，我在很长一段时间里把它写为“又一个拥有伟大营销团队的 [Selenium](https://www.selenium.dev/) ”。当然，它甚至不值得探索。

然而，有一天，我被指派做一个概念验证，关于我们应该如何为一个工作中的应用程序编写端到端和集成测试。积压工作正在接近尾声，现在绝对是时候提高我们应用程序的测试覆盖率了。所以，也许是时候了——让我们给赛普拉斯一个机会。这篇文章将说服你也这样做。

在本文中，我们将经历 [Cypress](https://www.cypress.io/) 、 [Cypress 测试库](https://testing-library.com/docs/cypress-testing-library/intro)和[Axe](https://www.deque.com/axe/)——这三个工具将一起给你真正的信心，让你的应用程序按预期工作。

## 工具链

因此，在我们进入本质细节之前，让我们看看这三个工具是什么，以及它们如何帮助你实现创建更好的应用程序的目标。

Cypress 是一个创建和运行端到端测试的工具。它启动一个浏览器，访问你的应用程序，并像普通用户一样运行一组预定义的步骤。最后，它验证结果是否是您所期望的。

与单元测试和集成测试相比，这类测试比较慢，但是它们在确保你的应用程序像最终用户期望的那样工作方面做得非常出色。你不应该写太多，而是要覆盖应用程序的主要路径。

Cypress 测试库是一个插入到 Cypress 中的库，它使得编写鼓励可访问代码的测试变得容易。它消除了 Cypress 的一个缺陷——选择元素的方式——并为您提供了一个 API，您可能从 React 测试库、Vue 测试库或同一系列中的其他库的单元测试中了解到了这个 API。

最后， **Axe** 是一个验证你的应用程序是否可访问和 WCAG 兼容的工具。它可以作为浏览器插件使用，但它只能验证你的应用程序现在的样子——而不是在 13 次交互和一次导航事件之后。幸运的是，Cypress 和 Axe 的结合也使最后一部分变得轻而易举——我将在本文后面向您展示如何实现。

## 安装

因此，我们已经找到了一个看起来很有前途的工具链——但是我们如何设置呢？首先，让我们安装来自 npm 的工具:

```
npm install cypress @testing-library/cypress cypress-axe --save-dev
```

这将创建一个空的`cypress.json`文件，以及一个包含一些配置文件和示例的`cypress`文件夹。

默认值`cypress.json`是空的，因为 Cypress 提供了真正合理的默认值。通过，有很多方法可以定制它！我们现在不会做很多，但是您可能想要指定您的应用程序的基本路径，这样您就不必通过导航到它来开始每个测试。这是通过设置 baseUrl 选项来实现的:

```
{
    "baseUrl": "http://localhost:3000",
}
```

Cypress 有定制命令的概念，您可以在测试时调用，Cypress 测试库和 cypress-axe 都为您提供了一些额外的“命令”。因此，要设置它们，请导航到新创建的文件 cypress/support/commands.js，并将其内容更改为:

```
import '@testing-library/cypress/add-commands';
import 'cypress-axe';
```

如果您正在使用 ESLint，您可能希望在 cypress 文件夹中创建一个包含以下内容的`.eslintrc`文件:

```
module.exports = {
    root: true,
    plugins: ['eslint-plugin-cypress'],
    extends: ['plugin:cypress/recommended'],
    env: { 'cypress/globals': true },
};
```

如果您使用的是 TypeScript，您还想在`cypress`文件夹中添加一个自定义的`tsconfig.json`:

```
{
    "compilerOptions": {
        "strict": true,
        "baseUrl": "../node_modules",
        "target": "es5",
        "lib": ["es5", "dom"],
        "types": ["cypress"]
    },
    "include": ["**/*.ts"]
}
```

您还需要创建一个类型定义文件来包含 cypress-axe 的类型。我们称之为`cypress/support/index.d.ts`，用这个填充它:

```
/// <reference types="cypress" />

declare namespace Cypress {
    interface Chainable {
        injectAxe(): Chainable<EventEmitter>;
        checkA11y(): Chainable<EventEmitter>;
    }
}
```

最后，让我们向 package.json 文件添加两个新脚本，这样我们也可以运行我们的测试:

```
{
    "scripts": {
        "test:e2e": "cypress open",
        "test:e2e:ci": "cypress run"
    }
}

```

`test:e2e`脚本打开了 Cypress 的内置 UI，允许您触发测试并逐步检查它们。`test:e2e:ci`在一个无头浏览器中运行测试——非常适合作为持续集成管道的一部分运行。

## 决定测试什么

所以设置已经完成，剩下的就是编写测试了。但是什么是好的端到端测试用例呢？

正如我最初提到的，你真的不应该有太多这样的端到端测试。它们速度慢，占用大量资源，并且需要您保持内容最新。因此，您应该关注应用程序的主要路径，将其余的工作留给您的集成和单元测试。

通过遵循这种方法，您可以为应用程序的所有主要路径添加测试，同时仍然保持您的测试套件足够快以经常运行。

## 编写测试

足够的设置和理论-让我们开始测试！我们将与一个想象的应用程序进行交互，以使事情变得简单。

首先，我们在 cypress/integration 文件夹中创建一个新文件，我们将其命名为`todo-tests.ts`。我们将从添加一个测试开始，为我们的列表添加一个 todo，我想这是 todo 应用程序的主要用途😅它看起来会像这样:

```
describe('todo', () => {
    beforeEach(() => {
        cy.injectAxe();
    })
    test('adds a todo', () => {
        cy.checkA11y();
        cy.findByText("Learn Cypress").should('not.exist')
        cy.findByLabelText(/What do you want to do/i)
            .type('Learn Cypress{enter}');

        cy.findByText("Learn Cypress").should('exist')
        cy.checkA11y();
    })
})

```

这里发生了很多事情，让我们一步一步来。

`cy`是我们运行测试时与之交互的变量。这是我们之前添加了所有自定义命令的那个！

首先，我们确保在开始每个测试之前调用它的`injectAxe`函数，以便 axe 插件被加载并准备好运行。

我们通过调用`cy.checkA11y()`开始我们的第一个测试。这将在当前状态下对您的应用程序进行全面的可访问性审核。接下来，在我们开始之前，我们确保 todo“Learn Cypress”没有被添加到列表中。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

现在，是时候添加我们的待办事项了。我们通过用`cy.findByLabelText`查找输入字段的标签来找到它。这是一个很好的额外检查，以确保我们的输入字段是可访问的！

我们通过调用我们想要的文本的`.type`方法在输入字段中输入。我们可以通过写“{enter}”来触发 enter 按钮。这也确保了我们将输入字段放在了一个`<form/>`标签中。

添加 todo 后，我们要确保“学习柏树”-todo 被添加到我们的列表中。我们使用`findByText`函数来查找它，并断言它应该存在。

最后，我们检查添加了 todo 项的应用程序是否仍然可以访问。

## 添加更多测试

我可能还想增加几个测试。我想确保我可以将一个`todo`设置为完成，并且我可以过滤掉我已经完成的。也许我想测试一下，如果我试图添加一个空的`todo`，我会得到一个错误消息。

为了简洁起见，我不会在本文中进行更多的测试，但是它们都遵循相同的模式。确保你的应用程序总是处于可访问状态，并使用可访问选择器，从一开始就推动你编写可访问的应用程序。

## 自动化肮脏的工作

我们可以通过在浏览器中手动完成这些步骤，非常容易地验证我们的应用程序是否正常工作。这些自动化的端到端测试并没有真正改变什么——但是它们确实非常方便！

如果你足够幸运，在你的团队中有一个 QA 工程师，他们也会喜欢你编写这类测试。你不会抢走他们的工作，但你会帮助他们自动完成单调乏味的工作！

事实上，您可以将测试驱动开发带到一个新的水平。您可以创建一个您希望您的应用程序做什么的规范，并且在您构建应用程序时，您可以让您的工具验证您的进度，而不是关注小功能！

## 变得自信

手动验证已经成为过去。这是一种脆弱的技术，非常适合忘记边缘情况和难以达到的状态。取而代之的是，加入这个项目，加入到柏树的革命中来。不用亲自检查，你就能知道你的应用程序是否如你的用户所期望的那样工作。你将知道你的应用程序的主要流程是工作的，你将知道它也将为使用辅助技术的人工作。最后，您将知道您将通过任何可访问性审查，因为可访问性是构建在您如何编写测试中的。

我希望这篇文章能让你像我一样对测试充满热情。不是因为测试很酷，而是因为它极其无聊。所以跳过手工测试，全部自动化。

## 入门资源

我在 Cypress 上最喜欢的资源是肯特·C·多兹关于 TestingJavaScript.com 的课程。这是对您测试技能的巨大投资，我怎么推荐都不为过。官方的 Cypress 文档也是一个很好的读物，包括真实世界的例子、入门指南和许多简洁的教程。

祝你好运！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)