# Jest - LogRocket 博客的自动化可视化回归测试

> 原文：<https://blog.logrocket.com/automated-visual-regression-testing-with-jest/>

开发人员编写测试来获得信心。

相信他们的应用程序或站点对每个用户来说都像预期的那样工作。相信每一个逻辑流程都有一个合适的结尾。并且确信当他们修改现有代码时，他们不会意外地破坏其他东西。

在所有不同的测试策略中，最少被提及的一种方法是从视觉上确认设计是否符合预期。编写单元、集成或端到端测试来确认用户流的功能要容易得多。所有这些测试都是用更多的代码编写的，作为开发人员，我们对此很满意。

但是，传统上是一个手动的过程来检查应用程序，从视觉上检查应用程序是否符合设计者的意图。这篇文章着眼于自动化可视化回归测试和在发布中释放更大信心的新方法。

## 你为什么想这么做

单元、集成和端到端测试都是自信地更新或发布新代码的必要元素，但在这些实现中通常缺少一个重要部分:视觉效果。

仔细想想，这些测试的大多数查询都类似于`findByTestId('submit-button')`或`findByLabelText('email-address')`，在这种情况下，您可以成功地单步调试整个应用程序，功能正常，但表示风格完全错误。

这种可能性有点言过其实，但是一些包更新、新组件或重构可以而且有时确实会以您意想不到的方式改变您的应用程序的外观。作为开发人员，我们的工作是确保我们的用户获得与 UX 同事设计的相同的体验，并相信我们能够实现。

那么，我们如何确保视觉效果总是正确的呢？你可以手动点击通过每一个可能的用户旅程，但必须有一个更好的方法。

Storybook 的自动化视觉测试页面上的文档包括一个讨论自动化视觉测试的挑战的部分，特别是一对一的像素匹配在不同的机器上很难实现。

他们很快就提出了一些付费服务，这些服务提供具有机器学习能力的视觉测试，能够检测一对一的像素差异，并理解所比较的图像足以通过人眼测试。

虽然这些付费服务可能不错，但我觉得当您可以不用太多努力就建立自己的流程时，转向服务的建议可能为时过早。

本文是我的研究和解决方案，旨在让您自己进行简单的可视化回归测试，并为您提供成功开始自动化您的应用程序的可视化回归测试所需的一切。

## 跟随一个示例项目

为了只强调代码的测试部分，我将引用本文中的一些步骤，其中只有一个到 git commit 的链接。供您参考，[GitHub](https://github.com/tvthatsme/visual-regression-jest)上有完整的项目。

我们将采取以下步骤来创建一个可视化测试回归的自动化方法:

1.  [创建一个简单的 React 应用程序](https://github.com/tvthatsme/visual-regression-jest/commit/0a024fd823f4741c04ef8864af15a3549e7b1e6c)。这真的可以是任何风格的现代 JavaScript 应用程序，不管是不是现有的项目。如果你读过很多关于这个主题的内容，你可能会认为你需要[从一个 create-react-app 项目中退出，以便运行可视化测试](https://gist.github.com/dferber90/6fe76cde582b8746191478fac34c8b7d#set-up-create-react-app)，但是实际上没有必要改变你的 Jest 配置的`setupTestFrameworkScriptFile`或`testEnvironment`，它们都不受 create-react-app 的支持。
2.  [增加](https://github.com/tvthatsme/visual-regression-jest/commit/22bda8234fb02abc0a7ea542acfd385d48064b1e) [J](https://github.com/tvthatsme/visual-regression-jest/commit/22bda8234fb02abc0a7ea542acfd385d48064b1e) [est 和一个初始测试](https://github.com/tvthatsme/visual-regression-jest/commit/22bda8234fb02abc0a7ea542acfd385d48064b1e)。这是非常标准的，在这篇文章的范围之外，您可以找到许多关于如何设置 Jest 测试的优秀文章。
3.  [用截图设置视觉测试](https://github.com/tvthatsme/visual-regression-jest/commit/f6cc68c3ac2f159d22feca1502794f1f53b563c5)。这部分对你来说可能是新的，但是在几个库的帮助下，我们很快就可以开始运行了。

第一部分是安装我们需要的库:

```
npm i --save-dev jest-image-snapshot jest-transform-css jest-transform-file jsdom-screenshot
```

我们还需要更新 Jest 配置，以便 Jest 知道如何使用图像快照工具。

```
// jest.config.js
module.exports = {
  verbose: true,
  setupFilesAfterEnv: ['./setupTests.js'],
  transform: {
    '^.+\\.js$': 'babel-jest',
    '^.+\\.css$': 'jest-transform-css',
    '\\.(jpg|jpeg|png|gif|webp|svg)$': 'jest-transform-file'
  }
};
```

`[setupFilesAfterEnv](https://jestjs.io/docs/en/configuration#setupfilesafterenv-array)`在环境中安装测试框架后，为 Jest 提供一个立即运行设置程序的路径。默认情况下，Jest 不知道 jest-image-snapshot 给我们的断言`toMatchImageSnapshot`。所以我们需要在安装文件中扩展 Jest 来理解如何使用这个新断言。

```
// setupTests.js
import { toMatchImageSnapshot } from 'jest-image-snapshot';
expect.extend({ toMatchImageSnapshot });
```

一旦我们完成了这些，我们就可以将可视化回归测试添加到我们的测试中了！

```
// src/App.test.js
import React from 'react';
import { generateImage } from 'jsdom-screenshot';
import { render } from '@testing-library/react';
import App from './App';

it('has no visual regressions', async () => {
  render(<App />);

  const screenshot = await generateImage();
  expect(screenshot).toMatchImageSnapshot();
});
```

这是怎么回事？我们从 jsdom-screenshot 导入`generateImage`，然后用 [react-testing-library](https://testing-library.com/docs/react-testing-library/intro) 呈现应用程序。一旦我们这样做了，我们就可以等待生成的图像，并使用我们设置的`toMatchImageSnapshot`断言来断言视觉效果匹配。

现在运行测试，您将看到创建了一个`__image_snapshots__`目录，其中存储了每个`generateImage`调用的图像。您将希望确保这些文件在您的源代码控制中提交，以便它们在其他开发人员和 CI 环境之间共享。

如果您现在对您的组件或应用程序进行可视化的更改，并重新运行测试，您将会得到一条类似于以下内容的失败测试的错误消息:

```
Expected image to match or be a close match to snapshot but was 0.12333333333333332% different from snapshot (592 differing pixels).
```

太棒了。我们现在有了一种方法，当我们在代码中做了一个影响应用程序视觉外观的改变时，我们会得到提醒。

## “在我的机器上工作”的问题

如果您的项目只在您的计算机上开发，您没有与任何团队成员协作，并且您不想使用任何 CI 方法来部署您的项目，那么上面所介绍的可能就足够了。

然而，有可能的是，您正在将您的代码推到一个集中的地方供其他开发人员共享，并且您正在使用一些类型的 CI 来自动化测试，至少在部署之前或者在合并到 master 之前。面对所有这些不同的计算环境，我们针对视觉回归的逐像素测试将会遇到严重的问题，因为每个环境呈现同一应用程序的方式都略有不同。

例如，假设我们希望在每次创建对 GitHub 中主分支的 pull 请求时，将测试作为一个 [GitHub 动作](https://github.com/features/actions)来运行。如果不对上一节中的代码进行任何修改，我们最终会得到一个失败和一条如下所示的消息:

```
Expected image to match or be a close match to snapshot but was 0.12333333333333332% different from snapshot (592 differing pixels).
```

在当地，一切都通过了考验。然而，测试运行的 Linux 版本可能与您在开发环境中使用的版本不同。因此，当这个测试在 CI 环境中运行时，产生的映像与保存在源代码控制中的`__image_snapshots__`目录中的预期映像不太匹配，测试失败。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

您可以尝试修改测试中的`toMatchImageSnapshot`断言，并提供一个百分比阈值，其中任何小于 0.5%的差异都被视为相同:

```
// src/App.test.js
// ..
expect(screenshot).toMatchImageSnapshot({
  failureThreshold: 0.005,
  failureThresholdType: 'percent'
});
// ..
```

但这只是一个更大问题的权宜之计。本文的“应用程序”仅仅是一个段落。如果区分一个段落的图像会导致本地环境和 CI 环境之间如此大的差异，想象一下一个有图片或许多组件的真实应用程序会做什么。你必须将失败阈值提高到一个甚至不可能检测到视觉退化的程度。

一定有更好的方法来解决这个问题。

嗯，是的——这就是 Docker 解决的问题。如果你在一个容器中运行你的测试，假如代码没有引入任何视觉回归，测试将总是产生相同的图像。随着一些[新的配置文件被添加到项目](https://github.com/tvthatsme/visual-regression-jest/commit/ca8862c2ef986a375729d856e3cbb0a927ec126b)中，应用程序可以在 Docker 容器中进行测试，问题就解决了！

配置 Jest 在 Docker 容器中工作的细节对于本文来说太多了，所以请参考演示 GitHub 项目的代码，或者参考 [Alan Foster](https://twitter.com/alanfosterdev) 关于[在 Docker 中运行 React 单元测试的伟大参考。](https://www.alanfoster.me/posts/running-react-unit-tests-within-docker/)

## 使用 GitHub 操作添加 CI 测试

既然我们已经在一个容器中运行了测试，那么是时候在一个实际的持续集成环境中进行测试了。出于本文和演示项目的目的，GitHub Actions 是演示这一点的最佳场所，但是对于您的特殊需求，GitHub、GitLab、Jenkins 或您正在使用的任何风格的 CI/CD 都应该工作得很好。毕竟是个容器！

```
# .github/workflows/test.yml
name: Test
on: [push]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/[email protected]
    - uses: actions/[email protected]
    - name: Run the containerized tests
      run: npm run test-in-docker:ci
    - name: Archive visual regression failures
      uses: actions/[email protected]
      if: failure()
      with:
        name: visual-regression-report
        path: /home/runner/work/visual-regression-jest/visual-regression-jest/src/__image_snapshots__/__diff_output__
```

这里有趣的部分是用`npm run test-in-docker:ci`在 Docker 环境中运行测试，在测试失败的情况下，作业将上传显示失败差异的图像，作为该作业的工件。这样，即使在容器和作业被关闭之后，您也可以知道测试出了什么问题。

## 关于故事书整合的最后思考

当我最初开始研究这个话题时，使用故事书似乎是一个不错的选择。他们有关于可视化回归测试的内容，这是许多开发人员都熟悉的工具。

然而，在浏览他们关于视觉回归的内容时，我犹豫是否直接采用故事书的解决方案，因为他们对一对一的像素匹配问题提出了强烈的警告。经过进一步的研究，我最终找到了本文中提到的解决方案，但我仍然考虑回到 Storybook 作为一种测试策略:也许您可以使用 Jest 或 Storybook 进行测试？

所以我开始按照 Storybook 提供的[步骤](https://storybook.js.org/docs/testing/automated-visual-testing/#custom-solutions)来实现一个定制的解决方案，用 Storybook 对回归进行可视化测试。在他们的指导进行到一半的时候(看起来并不开箱即用)，我意识到故事书对于视觉回归测试来说根本没有必要。给出的方法建议启动一个 Storybook 服务器，然后找出一种方法来获得测试所需的组件的 iframe。

但是我们已经可以更容易地做到这一点，使用本文中介绍的 Jest 测试方法。尝试添加故事书作为这类测试的选项会增加不必要的复杂性。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)