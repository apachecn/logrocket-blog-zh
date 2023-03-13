# 带有丢失像素和反应的故事书视觉回归测试

> 原文：<https://blog.logrocket.com/storybook-visual-regression-testing-lost-pixel-react/>

测试是现代软件开发的重要部分；事实上，TDD 范式是[测试驱动开发的缩写，强调在编写哪怕一行代码之前编写测试](https://blog.logrocket.com/test-driven-development-methods-deno/)的重要性。

对于后端应用程序，这可能很简单；然而，测试以 UI 为中心的应用程序可能相当棘手。在这里，[丢失像素](https://www.lost-pixel.com/)派上了用场；Lost Pixel 是一个开源工具，它可以帮助我们识别用户界面中的每一个应用程序的退化。

在这篇文章中，我们将学习如何使用丢失像素来测试一些故事书组件。我们开始吧！

## 什么是丢失像素？

根据 [Lost Pixel](https://github.com/lost-pixel/lost-pixel) GitHub 页面显示，Lost Pixel 是一款开源的可视化回归测试工具。丢失像素渲染组件的实际图像；当我们对组件进行任何更改时，Lost Pixel 会将原始图像与更改后的图像进行比较，从而检测回归。

Lost Pixel 的方法与使用 Jest 的[快照测试](https://jestjs.io/docs/snapshot-testing)截然不同，在 Jest 中，[整个组件以其冗长的文本格式表示](https://blog.logrocket.com/jest-testing-top-features/)，然后与更改后的版本进行比较。

在编写的时候，Lost Pixel 支持 Storybook，Ladle，Next.js，Gatsby，和 Remix。在这篇文章中，我们将探索使用丢失像素以及故事书组件。

## 什么是故事书？

故事书是一个工具，可以帮助我们[优化我们的 React 开发流程](https://blog.logrocket.com/using-storybook-to-develop-react-components-faster/)。根据 Storybook 官方文档，Storybook 是一个隔离组件的 UI 开发工具，使开发更快更容易。

我们可以把 Storybook 想象成一个平台，让我们彼此独立地为我们的应用程序编写构建模块。当我们最终将所有这些整合到有意义的应用程序中时，我们在行为上遇到的错误和意外会更少。此外，Storybook 提供了我们所有组件的免费文档。为了更清楚地了解所有这些是如何工作的，让我们用 Storybook、React 和 Vite 构建一个动手项目。

## 使用 React 设置故事书

### 带反应和邀请的故事书

在本文中，我们将在 React 项目中设置 Storybook。为此，我们首先需要一个基本的 React 项目。首先，让我们[使用 Vite](https://blog.logrocket.com/setting-up-dev-environment-react-vite-tailwind/) 创建 React 项目:

```
npm create [email protected]

```

我们将按照屏幕上的提示用 JavaScript 创建一个 React 项目。一旦运行完成，我们就可以运行我们刚刚创建的项目，方法是进入该目录并启动 dev 服务器:

```
cd storybook-testing
npm run dev

```

通过在浏览器中访问相同的 URL，您应该会看到在`[http://127.0.0.1:5173/](http://127.0.0.1:5173/)`上运行的 Vite + React 主屏幕:

![Vite React Homescreen Browser](img/a4315c30dafdb7393b59a15aa23c3079.png)

### 故事书设置

现在，我们将通过运行以下代码在这个 repo 中设置 Storybook:

```
npx storybook init

```

上面的命令将添加必要的文件，并对`package.json`进行必要的修改，以便 Storybook 能够正常工作。如果出现如下错误，选择 **yes** 在 repo 中创建一个新的`.npmrc`文件:

![Error Install Npm Rc Package](img/bc6711aa15d8c19c7fd87fd5c273d14c.png)

接下来，我们可以使用下面的命令运行 Storybook:

```
npm run storybook

```

上面的命令将在`[http://localhost:6006/](http://localhost:6006/)`上打开故事书主页，如下图所示:

![Storybook Homepage View](img/0ddc42e5e614b3977ca489c89dfdbcd3.png)

请注意，Storybook 已经创建了几个`button`组件，我们可以使用 UI 来探索这些组件:

![Storybook Button Component Example Gif](img/334e72a997ff80c108466cd3b9cabb44.png)

我们将使用 Lost Pixel 对这些预定义的按钮进行回归测试。

### 回购设置

通过这些更改，我们可以将代码推送到一个新的 GitHub repo，如下所示:

```
git init
git add --all
git commit -m 'first commit'
git remote add origin <repo_origin_url>
git push -u origin main

```

既然 repo 已经准备好了，我们将添加丢失的像素代码。

### 丢失像素设置

设置好 UI 后，我们将把丢失的像素引入到同一个存储库中。首先，我们使用下面的命令安装丢失的像素:

```
npm install lost-pixel

```

接下来，我们在 Vite 项目的根目录下创建一个`lost-pixel.config.js`文件:

```
import { CustomProjectConfig } from 'lost-pixel';
export const config: CustomProjectConfig = {
  storybookShots: {
    storybookUrl: './storybook-static',
  },
  generateOnly: true,
  failOnDifference: true,
};

```

在上面的代码中，我们告诉 Lost Pixel 在哪里可以找到故事书组件。在我们的例子中，一旦 Storybook build 命令运行，Storybook 组件将在`./storybook-static` URL 中找到。

接下来，我们需要创建两个 GitHub 工作流。在 repo 的根目录下，创建一个名为`.github`的文件夹，并在其中创建另一个名为`workflows`的文件夹。我们将把下面两个文件放在`workflows`文件夹中。

### `lost-pixel-update.yml`

`lost-pixel-update.yml`工作流将帮助 Lost Pixel 创建基线，它将根据基线执行回归测试。在执行测试时，Lost Pixel 需要知道呈现组件的正确方式，以便确定组件是否偏离了该行为。

`lost-pixel-update.yml`文件如下所示:

```
on: [workflow_dispatch]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/[email protected]
      - name: Setup Node.js
        uses: actions/[email protected]
        with:
          node-version: 16.x
         cache: 'npm'
      - name: Install dependencies
        run: npm ci
      - name: Build Storybook components
        run: npm run build-storybook
      - name: Run Lost Pixel container
        id: lp
      uses: lost-pixel/[email protected]
        env:
          LOST_PIXEL_MODE: update
      - name: Create Pull request
        uses: peter-evans/[email protected]
        if: ${{ failure() && steps.lp.conclusion == 'failure' }}
        with:
          token: ${{ secrets.GH_TOKEN }}
          commit-message: update lost-pixel baselines
          delete-branch: true
          branch: "lost-pixel-baselines/${{ github.ref_name }}"
          title: "Lost Pixel updating baselines - ${{ github.ref_name }}"
          body: Automated baseline update PR - lost pixel

```

注意，这个 GitHub 操作的步骤很简单。我们创建一个 Node.js 容器，构建我们的 Storybook 组件，然后使用 GitHub action store 上发布的预构建动作运行 Lost Pixel，`lost-pixel/[[email protected]](/cdn-cgi/l/email-protection)`。

此动作在`update`模式下运行丢失像素动作。但是，请注意最后一步是特殊的，创建一个包含变更组件的 pull 请求，以便这些组件可以与 repo 合并，并作为新的基线组件。我们在创建 pull 请求时使用`secrets.GH_TOKEN`进行认证，稍后我们将对此进行回顾。

### `lost-pixel-test.yml`

`lost-pixel-test.yml`工作流将在每次推送到 repo 时被触发，从而为组件生成新的图像，并将它们与运行基线测试时生成的图像进行比较。如果两者之间有任何差异，测试将失败。`lost-pixel-test.yml`文件看起来像下面的代码:

```
on: [push]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/[email protected]
      - name: Setup Node
        uses: actions/[email protected]
        with:
          node-version: 16.x
          cache: 'npm'
      - name: Install dependencies
        run: npm ci
      - name: Build Storybook
        run: npm run build-storybook
      - name: Lost Pixel
        id: lp
        uses: lost-pixel/[email protected]

```

这个动作和上一个动作几乎一样，只是略有不同。创建 PR 的最后一步不见了，我们也没有在`update`模式下运行丢失像素。正如我们在`lost-pixel-update.yml`文件中注意到的，PR 创建步骤要求我们提供`secrets.GH_TOKEN`。

### 生成 GitHub 身份验证令牌

查看这个 [GitHub repo](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token#creating-a-personal-access-token-classic) ，了解生成个人访问令牌的详细步骤。简而言之，步骤如下:

*   访问您的 **GitHub** **个人资料设置**
*   点击**开发者设置**，然后点击**个人访问令牌**
*   点击**令牌(经典)**然后**生成新令牌**
*   选择令牌的范围为**工作流**

![Generate New Personal Access Token Github Classic](img/01e830df99a5d3774ac88268ed705d53.png)

生成令牌后，将其复制到剪贴板。我们需要将它作为秘密添加到回购协议中:

*   在存储库页面上，点击**设置**
*   点击**秘密**，然后点击**动作**
*   点击**新建存储库密码**

![Add Github Token Secret](img/876df68ff22caccad8e47adeb996bb0e.png)

将秘密命名为`GH_TOKEN`，粘贴秘密，然后点击**添加秘密**。这将增加回购的秘密，以便公关创建步骤顺利进行。

### 生成基线

设置完成后，我们现在可以运行操作来为我们所有的故事书组件生成基线。添加两个新创建的`.yml`文件，创建一个 commit，并将其推送到远程 repo。我们可以看到，测试在代码推送时立即被触发，但是它失败了，因为没有基线:

![Create Baselines Storybook Components](img/d35974836813cd525cc12dae64af4596.png)

让我们运行工作流来生成基线。访问 GitHub repo 的**动作**选项卡，然后选择`lost-pixel-update.yml`工作流。点击**运行工作流程**:

![Run Lost Pixel Storybook Github Action](img/3a9af97a1d8dafecbca17eb0a98ae8c2.png)

完成前面的所有步骤后，工作流顺利运行，结果创建了一个拉取请求:

![Storybook Lost Pixel Workflow Run Success](img/53f7db58ce070fc0a9aa524b6c392e05.png)

合并拉取请求，以便对应于基线的`.png`文件在 repo 中被合并。

### 测试视觉回归

配置好基线后，让我们在故事书组件中引入一些可视化回归。访问故事`Button.stories.jsx`文件，将主按钮的标签改为**测试按钮**:

```
export const Primary = Template.bind({});
// More on args: https://storybook.js.org/docs/react/writing-stories/args
Primary.args = {
  primary: true,
  label: 'Test Button',
};

```

保存文件，提交并推送到远程:

```
git commit -m 'testing regression'
git pull --rebase
git push

```

您应该看到`lost-pixel-test.yml`工作流被立即触发。它运行了，成功地指出在组件的`primary button`版本中有一个回归:

![Lost Pixel Test YML Workflow Triggered](img/3364090a301bb4e414b188acc4cb4644.png)

如果这是一个预期的回归，我们可以像以前一样触发`lost-pixel-update.yml`工作流，基线将被更新。

## 结论

Lost Pixel 为我们提供了一种替代方法来执行 UI 回归测试，这与快照测试的标准实践有很大不同。如果您的项目使用了大量的故事书组件，并且您不担心额外的`.png`图像被推送到存储库，您可以探索项目中丢失的像素来检测 UI 退化。

## [LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。