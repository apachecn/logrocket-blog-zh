# 剧作家和木偶师:你应该选择哪一个？

> 原文：<https://blog.logrocket.com/playwright-vs-puppeteer/>

[剧作家](https://github.com/microsoft/playwright)是一个用于浏览器自动化的 [Node.js](https://nodejs.org) 库。[木偶师](https://pptr.dev/)也是一个用于浏览器自动化的 Node.js 库。让我们看看是什么让它们变得有趣，并考虑在决定使用哪一个时应该考虑的因素。

## 历史背景

首先了解一些历史背景可能会有所帮助。

浏览器自动化并不是一项新技术。 [Selenium](https://www.selenium.dev/) 自 2004 年就已经出现，在很长一段时间里，它都是这一类别的首选工具。虽然它的主要用例是为网站实现测试套件，但程序员也用它来做一些事情，比如在网站不提供 API 时截屏或自动化任务。

然而，Selenium 有一个不可靠的名声。硒测试通常是不可靠的，由于难以重现的不明显的原因而间歇性地失败。

Selenium 也相当耗费资源，部分原因是它通常用于运行完整的浏览器。最终，程序员开始在某些情况下使用[无头浏览器](https://en.wikipedia.org/wiki/Headless_browser)。这些浏览器完成了完整浏览器的大部分工作，但它们实际上并不呈现 UI。[幻想曲](https://en.wikipedia.org/wiki/PhantomJS)是其中比较受欢迎的一种。

无头浏览器可以很好地测试简单的网站，但要测试网站可以做的一切，他们需要通过支持各种网络标准尽可能接近地模拟完整的浏览器。然而全面的支持是一个[几乎不可能完成的任务](https://drewdevault.com/2020/03/18/Reckless-limitless-scope.html)，因为浏览器已经变得如此复杂。

更复杂的网站也需要更多的测试，所以过去的无头浏览器的主要问题是，当你想更多地使用它们时，它们变得越来越不可用。

2017 年，情况发生了显著变化。PhantomJS 的作者反对它，他这样做的[动力](https://groups.google.com/forum/m/#!topic/phantomjs/9aI5d-LDuNE)是谷歌宣布的[无头 Chrome](https://developers.google.com/web/updates/2017/04/headless-chrome) ，它首先在 Chrome 59 中可用。当年晚些时候，火狐[又推出了无头模式](https://www.ghacks.net/2017/09/01/first-look-at-firefoxs-headless-mode/)。这些发展使得针对人们实际使用的相同浏览器编写高效的测试和脚本成为可能。

## 操纵木偶的人

几个月后，谷歌继 headless Chrome 之后发布了 Puppeteer 的公开版本。Chrome DevTools 团队开发了它，这使它比类似的项目具有更大的优势，因为它拥有来自同一家公司的机构支持，该公司制造了世界上使用最广泛的浏览器[。](https://en.wikipedia.org/wiki/Usage_share_of_web_browsers)

木偶师可以驱动 Chrome 或者[Chromium](https://en.wikipedia.org/wiki/Chromium_(web_browser))(Chrome 所基于的开源浏览器)，默认情况下，安装木偶师也会下载 Chromium 的兼容版本。这避免了可能发生的情况，即您的浏览器最终获得了破坏 Puppeteer 的更新。

Puppeteer 使浏览器自动化入门变得容易。这部分是因为它与浏览器的交互方式。Selenium 使用 [WebDriver 协议](https://www.w3.org/TR/webdriver/)，它需要运行一个服务器，作为 Selenium 和浏览器之间的中介。比如有 [ChromeDriver](https://chromedriver.chromium.org/) 、 [geckodriver](https://github.com/mozilla/geckodriver) (针对火狐)，还有 [safaridriver](https://developer.apple.com/documentation/webkit/testing_with_webdriver_in_safari) 。这种对特殊服务器的要求增加了复杂性。

相比之下，木偶师使用非标准的 [DevTools 协议](https://github.com/WICG/devtools-protocol/)控制 Chrome，因此它直接与浏览器对话，并通过 Selenium 提供额外的功能，如[拦截网络请求](https://pptr.dev/#?product=Puppeteer&version=master&show=api-pagesetrequestinterceptionvalue)。

我曾使用 Puppeteer 进行网站测试，并在 TurboTax 中自动输入数据，在这两种情况下，这都是一个相对轻松的过程。

## 剧作家

当微软在 2020 年 1 月 31 日发布了剧作家的第一个公开版本时，我们获得了一个新的选择。

如果你比较一下剧作家和木偶师[的贡献者页面，你会注意到木偶师的前两名贡献者现在都在为剧作家工作。木偶团队实质上是从谷歌转移到了微软，变成了剧作家团队。](https://github.com/puppeteer/puppeteer/graphs/contributors)

因此，剧作家在许多方面与木偶戏演员非常相似。API 方法在大多数情况下是相同的，并且在默认情况下，剧作家也捆绑了兼容的浏览器。

剧作家最大的差异化点是跨浏览器支持。它可以驱动 Chromium、WebKit(Safari 的浏览器引擎)和 Firefox。你可以在[剧作家准备好了吗？](https://aslushnikov.github.io/isplaywrightready/)网站。

然而，剧作家团队对跨浏览器支持的方法是有争议的。他们给剧作家提供了 WebKit 和 Firefox 的补丁版本。他们的[回应](https://github.com/microsoft/playwright#q-what-browser-versions-does-playwright-use)是他们只修补了 WebKit 和 Firefox 调试协议，而不是实际的渲染引擎。

然而，这些变化还没有与 WebKit 和 Firefox 团队一起开发。虽然这些变化最终可能会被合并进来，但这并不能保证。剧作家团队可能不得不无限期地维护这些更改，这可能会影响剧作家跨浏览器支持的长期可靠性。

开始一个新的库也允许剧作家团队以突破性的方式使 API 更符合人体工程学。例如，默认情况下，剧作家的`page.click`等待元素可用和可见。

这并不意味着木偶师不会在新的主要版本中得到类似的改进。这只是意味着，就目前而言，在某些情况下，剧作家可以更容易地使用。

最后一个值得注意的区别是，剧作家有一个更强大的浏览器上下文功能，允许您用一个浏览器实例模拟多个设备。

```
const { chromium, devices } = require("playwright");

(async () => {
    const browser = await chromium.launch();

    for (const deviceName of ["iPhone 11", "iPad Pro 11"]) {
        const context = await browser.newContext({
            ...devices[deviceName]
        });
        const page = await context.newPage();
        await page.goto('http://github.com');
        await page.screenshot({
            path: `github-${deviceName.replace(/ /g, "-")}.png`
        });
    }

    await browser.close();
})();
```

就 cookies、本地存储等而言，每个浏览器上下文都与其他上下文相隔离。，并且每个上下文下面还可以有多个页面。

## 选择图书馆

因为剧作家和木偶师基本上是由同一个团队开发的，并且有如此相似的 API，所以从木偶师转移到剧作家[并不是](https://medium.com/@davert/puppeteer-to-playwright-migration-guide-6c86ea66e85e) ' [太难](https://medium.com/@davert/puppeteer-to-playwright-migration-guide-6c86ea66e85e)。然而，你不应该仅仅因为剧作家是新的就选择剧作家而不是木偶师——剧作家还没有达到 1.0 版本，所以 API [可能会在那之前得到突破性的变化](https://github.com/microsoft/playwright#q-is-playwright-ready)。

我认为要考虑的三个主要因素是跨浏览器支持、长期库支持和您的浏览器自动化的特定用例。

对于跨浏览器支持，重要的是要知道木偶师[最近增加了](https://github.com/puppeteer/puppeteer/releases/tag/v2.1.0)对驱动 Firefox 的实验性支持，并且能够在不修补 Firefox 的情况下做到这一点。

```
const browser = await puppeteer.launch({product: "firefox"});
```

然而，[似乎没有任何关于 WebKit 的木偶师支持的公开工作。注意，即使是 iOS](https://github.com/puppeteer/puppeteer/issues/4887) [上的第三方浏览器也被要求](https://stackoverflow.com/a/48541760/1481479)使用 WebKit 作为它们的引擎。因此，如果对你的 iOS 用户进行测试很重要，那么剧作家应该很有吸引力。

另一方面，许多团队认为测试多个浏览器(通常是 Chrome)不值得边际收益。剧作家可以改变这种计算，因为它使跨浏览器支持更容易，但这仍然是坚持使用木偶师的潜在原因。

你也要考虑什么图书馆未来发展前景比较好。我们不确定剧作家修补 Firefox 和 WebKit 的方法是否能长期奏效。

然而，乐观的一个理由是，剧作家团队首先给了我们这些工具。新的木偶团队将不得不表明，它可以在改善各自的图书馆方面与剧作家团队保持同步。

无论如何，我很感激木偶师仍在积极开发中，绝不是因为最初的开发者离开了就放弃的项目。这是得到 Chrome 团队支持的一个好处。

最后，决定取决于您的用例。如果你正在做一些像自动化网站工作流程或截图的事情，跨浏览器支持可能对你来说并不重要。

如果硒对你有效，坚持使用硒或你目前正在使用的任何东西也是有意义的。web 开发社区积累了多年关于 Selenium 的智慧和工具，您不必仅仅因为有了新的选择就放弃它。

具体测试来说，[柏树](https://www.cypress.io/)也成为了热门选择。它有一个[独特的方法](https://www.cypress.io/how-it-works)与你的网站代码一起运行，使测试更加可靠。它[最近增加了](https://www.cypress.io/blog/2020/02/06/introducing-firefox-and-edge-support-in-cypress-4-0/) beta 版火狐和 Edge 支持。

## 结论

总的来说，我很高兴浏览器自动化领域存在良性竞争。我希望它们能互相推动，变得更好，让浏览器自动化变得越来越容易和可靠。