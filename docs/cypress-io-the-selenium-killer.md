# 柏树与硒:为什么柏树是更好的选择

> 原文：<https://blog.logrocket.com/cypress-io-the-selenium-killer/>

## 硒的问题是

编者按:这篇文章于 2021 年 1 月 19 日更新，以反映 Cypress 6.1.0 引入的变化和改进。

在我开始之前，我想强调的是，这篇文章不是关于一个特定的项目或者我曾经工作过的任何自动化测试人员。我在最近的三个项目中看到了这种行为，几乎每个和我一起工作过的自动化测试人员都竭尽全力让这台有故障的机器工作。

我相当肯定的是，我最近参与的每个合同都有一份备忘录，规定需要[一百万次自动化测试](https://blog.logrocket.com/automated-testing-is-not-working/)来保证成功。我们不能停下来质疑这些测试的价值。我们必须像保护我们的孩子一样保护他们。

这些测试必须用 [Selenium](https://www.seleniumhq.org/) 编写，尽管几乎每个人都有过相当糟糕的经历，原因是我将在后面陈述的固有的已知问题。根据他们的文档，Selenium 提供了一系列工具和库来支持 web 浏览器的自动化，并提供了模拟用户与浏览器交互的扩展，以及用于扩展浏览器分配的分发服务器。它还拥有实现 [W3C WebDriver 规范](https://www.w3.org/TR/webdriver/)的基础设施，可以让你为所有主流 web 浏览器编写可互换的代码。

编写 Selenium 测试具有极大的挑战性，但是我们不会因此而退缩。取而代之的是，我们的测试人员可能很晚才开始编程，或者是开发新手。我们将让这些经验不足的开发人员编写这些困难的测试。

Selenium 测试可能很难编写，但是它们很容易复制和粘贴。这当然会导致各种各样的问题。

我们经常听到“如果它动了，就给它写个硒测试”。自动化测试必须针对 [API](https://blog.logrocket.com/common-api-mistakes-and-how-to-avoid-them-804fbcb9cc4b/) ，前端、后端、中端、快乐路径、悲伤路径、颠倒路径等进行编写。

我们没有时间进行手动测试，我们怎么可能呢？我们要编写和维护所有这些古怪的硒测试。这个冲刺我们已经迟到了，每个故事都要有自动化测试。

经过一年左右的时间和一个疯狂漫长的建设，我们将决定这是有点傻，并删除它们。或者更糟——重新开始。

### 为什么大家还在用硒？

我想如果我能回答上面的问题，我会更接近理解我们存在的真正本质。玩笑归玩笑，为什么硒的使用如此广泛？这确实让我感到震惊，但这里有一些建议:

公平地说，编写一百万个验收测试的突然激增并不是 Selenium 的错。在我看来，自动化测试的正确数量是*一个* 快乐路径测试，没有悲伤路径或颠倒路径。这个测试是一个冒烟测试，以确保我们的系统对业务开放。

单元测试和集成测试运行、实现和维护起来更便宜，应该成为我们测试的主体。大家都忘了[测试金字塔](https://martinfowler.com/bliki/TestPyramid.html)了吗？

## 为什么硒对你不起作用的原因

硒的问题可以用一个词来表达:*时机*。

在我们开始编写代码来断言我们的测试是正确的之前，我们需要确保我们需要与之交互的任何元素都是可见的，并且处于接受模拟输入的状态。远程 API 调用需要解决，动画和微调需要结束。现在构成我们应用程序大部分的动态内容需要从 API 调用的当前检索数据中完成渲染。

那么，当这种可怕的异步哑剧发生时，我们该做些什么呢？我们如何阻止我们的测试刚刚完成或触底，因为一个特定的文本输入被禁用，直到一个 API 调用已经完成或一个美丽的 SVG 旋转覆盖已经把我们的虚拟世界的黑暗面纱？

通俗地说，我们等待 HTML 元素处于就绪状态。在 Selenium speak 中，我们编写了许多定制的`waitForXXXXX`代码助手，例如

或者更现实地说…

```
wait.until(ExpectedConditions.visibilityOfElementLocated(By.xpath("//input[@id='text3']")));

```

最糟糕的罪行之一就是使用`Thread.sleep`。这是一个令人发指的罪行，从空气中随机抽取一个数字，作为我们认为 UI 处于就绪状态时的胡乱猜测。请不要这样做。

以下是我一直以来最喜欢的 Selenium 异常，是我在艰难地阅读 CI 构建报告时发现的:

*   继续前进，你不会在这里找到你的输入
*   这种厚颜无耻的行为意味着你已经非常接近了，但是还不够接近，它就在大教堂里，但是你却什么也做不了
*   `StaleElementReferenceException`–元素结束了一天的工作，去了酒吧。请明天再试一次
*   你可以等到时间的尽头，无论你想做什么都不会发生。你刚刚掷出了一个 7

## 看:鳞片

我经历过的最令人心碎的时刻之一是，由于自动化测试失败而导致构建失败，只是通过再次运行构建而神奇地通过。这种现象或僵尸自动化测试通常被称为*一个薄片*。

薄片的主要问题在于它是[非确定性的](https://martinfowler.com/articles/nonDeterminism.html)，这意味着当在不同的时间使用相同的输入执行时，测试会表现出不同的行为。随着非确定性测试数量的增加，您可以看到对您的回归测试套件的信心化为乌有。

一个古怪的测试很可能是由于计时、延迟和可怕的异步操作，我们正试图用我们的`Thread.sleep`和`waitForAHero`助手来驯服它们，我们需要继续写以保持理智。

试想一下，如果我们能够以某种方式让所有这些异步编程消失，如果我们的世界开始以线性或同步方式运行，这将会变得多么容易。我们将有一个多么自然的世界来测试。

Cypress.io 正着手这么做。

## Cypress.io:硒的替代品

### 柏树是什么？

Cypress 是一个基于 JavaScript 的端到端测试框架。它构建在 Mocha 之上，运行在浏览器中，支持异步测试。根据 [Cypress 文档](https://docs.cypress.io/guides/overview/why-cypress.html#In-a-nutshell)，除了端到端测试，Cypress 还能帮你编写集成测试和单元测试。

Cypress 包括以下功能:

*   **时间旅行:** Cypress 在测试运行时拍摄快照
*   **调试:**可读的错误和堆栈跟踪使调试更容易
*   **自动等待:**自动等待命令和断言，然后继续
*   **间谍、存根和时钟:**验证和控制函数、服务器响应或定时器的行为
*   **网络流量控制:**不涉及服务器的控制、存根和测试边缘案例
*   **截图和视频:**查看失败时自动拍摄的截图，或者从 CLI 运行时查看整个测试套件的视频
*   **跨浏览器测试:**在本地运行 Firefox 和 Chrome 系列浏览器(包括 Edge 和 electronic)中的测试

### 柏树和硒的区别

[Cypress.io](https://www.cypress.io/) 和 [Selenium](https://www.seleniumhq.org/) 的主要区别之一是 Selenium 在我们测试的浏览器或设备之外的进程中执行。Cypress 在浏览器中执行，并在与被测设备相同的运行循环中执行。

Cypress 在浏览器内部执行绝大多数命令，因此没有网络延迟。命令以应用程序能够呈现的速度运行并驱动应用程序。为了处理具有复杂 UI 的现代 JavaScript 框架，您使用断言来告诉 Cypress 您的应用程序的期望状态是什么。

Cypress 会自动等待您的应用程序达到这个状态，然后再继续。您完全不必担心手动等待或重试。Cypress 会自动等待元素的存在，并且永远不会产生已经从 [DOM](https://blog.logrocket.com/8-dom-features-you-didnt-know-existed-ec2a0a28fd89/) 中分离出来的陈旧元素。

这是主要的收获。Cypress 通过在与设备相同的运行循环中执行，消除了 Selenium 的主要问题。Cypress 负责等待 DOM 元素出现。

我再说一遍:赛普拉斯会处理好所有等待的事情。没有`Thread.sleep`，没有`waitForTheMoon`帮手。你不明白这意味着什么吗？

要真正理解这有多好，你必须经历痛苦。

下面是一些柏树测试的例子。

他们缺席的一个同义词是任何时机或淫秽的`waitFor`帮手:

```
context("Login", () => {
  beforeEach(() => {
    cy.visit("localhost:8080/login");
  });

  it("can find and type in email", () => {
    cy.get("#email")
      .type("[email protected]")
      .should("have.value", "[email protected]");
  });

  it("can find and type in password", () => {
    cy.get("#password")
      .type("fakepassword")
      .should("have.value", "fakepassword");
  });

  it("will fail when type invalid user credentials", () => {
    cy.get("#email").type("[email protected]");

    cy.get("#password").type("fakepassword");

    cy.get("input[type=submit]").click();

    cy.get("#login-message").should("have.text", "Login failed");
  });
});

```

我喜欢这些测试。它们清楚地陈述了自己的目的，并且不会被弥补平台局限性的代码混淆。

下面是我写的通过 Cypress 运行 [axe 辅助工具](https://www.deque.com/axe/)的一些测试:

```
import { AxeConfig } from "../support/axeConfig";

describe("Axe violations", () => {
  beforeEach(() => {
    cy.visit("/");
    cy.injectAxe();
  });

  it("home page should have no axe violations", () => {
    cy.configureAxe(AxeConfig);
    cy.checkA11yAndReportViolations();
  });
});

```

这里有一个使用`webdriver`的类似测试:

```
// in e2e/home.test.js
import assert from 'assert';
import { By, until } from 'selenium-webdriver';
import {
    getDriver,
    analyzeAccessibility,
} from './helpers';

describe('Home page', () => {
    let driver;

    before(() => {
        driver = getDriver();
    });

    it('has no accessibility issues', async () => {
        await driver.get(`http://localhost:3000`);

        // The dreaded wait until.  Abandon hope
        await driver.wait(until.elementLocated(By.css('h1')));

        const results = await analyzeAccessibility();
        assert.equal(results.violations.length, 0);
    });
});

```

主要的显著区别和令我担忧的是延迟。有两个`await`电话和可怕的`wait(until.elementLocated)`。这是一个简单的测试，但是你的互动越多，你就需要越多的`waitFor`助手，并且这种剥落开始蔓延。

如果你有兴趣了解更多，这里有一个用 Cypress 编写端到端测试的教程。

### JavaScript 一路向下

Cypress 显然是针对前端开发者的。安装 Cypress 是一件轻而易举的事情，通过您最喜欢的包管理器选择来执行:npm 或 yarn。

```
npm install cypress --save-dev
```

这真的再简单不过了。与下载 Chrome WebDriver 和 Selenium 世界中的朋友相比。

没有 Selenium 这样的多语言支持。你可以有任何你喜欢的编程语言，只要是 JavaScript 或者 TypeScript。

### 赛普拉斯监狱

当然，也有缺点，其中一些是显而易见的，所以我不能不列举这些。

*   柏树是相对较新的物种，它没有硒那样庞大的群落
*   如前所述，要么是 JavaScript，要么是 bust。你不会用 C#和 java 这些陈旧的静态语言来编写 Cypress 测试
*   因为它在浏览器中运行，所以你不能支持多个标签

同样需要注意的是，Cypress [不支持本地移动应用](https://docs.cypress.io/faq/questions/general-questions-faq.html#Do-you-support-native-mobile-apps)。但是，您可以使用 Cypress 来测试移动 web 浏览器的某些功能，并测试在使用 Ionic 等框架的浏览器中开发的移动应用程序。

## 柏树会取代硒吗？

虽然我很想说是，但我有疑虑。有一大批自动化测试人员，除了 selenium 之外，他们还不知道任何其他世界，可能很难很快离开。

## 测试只是开始——确保通过测试意味着用户满意

虽然 Cypress 引入了一个引人注目的新测试框架，但是让测试更进一步是很重要的。LogRocket 监控应用程序的整个客户端体验，并自动发现任何问题(尤其是那些测试可能遗漏的问题)。要通过前端监控获得对生产环境的宝贵见解，[请尝试 LogRocket](https://logrocket.com/signup/) 。[T3](https://logrocket.com/signup/)

[https://logrocket.com/signup/](https://logrocket.com/signup/)

LogRocket 就像是网络应用的 DVR，记录下你网站上发生的每一件事。您可以汇总并报告性能问题以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 让你的应用程序记录带有标题+正文的请求/响应，以及关于用户的上下文信息，以全面了解问题。它还记录页面上的 HTML 和 CSS，甚至可以重建最复杂的单页面应用程序的像素级完美视频。

优先考虑性能–[开始免费监控](https://logrocket.com/signup/)。

## 结论

正如我在本文开始时所说的，我对自动化测试的体验并不好。大量的金钱、时间和痛苦花费在维持数以千计难以维持的测试上，却得不到令人满意的回报。根据我的经验，自动化测试只能保证长时间的 CI 构建。

作为开发人员，我们需要更好地进行自动化测试。我们需要编写更少的测试来做更多有用的事情。我们把一些最难写的代码留给了一些经验最少的开发人员。我们已经让手工测试看起来过时了，在我看来，这仍然是发现真正错误的地方。

我们需要对自动化测试能够达到的目标有一个清醒的认识。

Cypress 很棒，因为它使事情同步。这消除了整个世界的痛苦，为此，我坚决支持。然而这并不是开绿灯写千柏考。我们的大部分测试是单元测试，在我们进行一些愉快的自动化测试之前有一层集成测试。

当然，这是一个永远不会发生的非常明智的策略。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.