# React 和 React Native - LogRocket 博客对 Lefthook 的深入研究

> 原文：<https://blog.logrocket.com/deep-dive-into-lefthook-react-native/>

Git 挂钩是当今开发工作流程中最重要的组成部分之一。钩子有助于维护有多个贡献者的大型代码库的代码质量。随着林挺、代码格式化和类型检查的引入，Git Hook 任务比以往任何时候都多。

对于前端开发人员(或者一般的 JavaScipt 开发人员)， [Husky](https://github.com/typicode/husky) 是管理 Git 挂钩的首选。Husky 虽然流行，但是缺乏并行性，而并行性可以加快钩子的执行速度。它还增加了对`node_modules`的依赖，这可能是一个问题。

在本文中，我们将研究 Lefthook，它是面向前端开发人员的一个比 Husky 更快、更简单、更强大的替代工具。我们将通过将 Lefthook 添加到 React 和 React 本机应用程序的示例中来了解它与 Husky 的比较。

## 什么是左勾拳？

基于 JS 的前端社区已经发展到了用其他语言开发的工具现在支持 JS 开发的地步。 [Lefthook](https://evilmartians.com/chronicles/lefthook-knock-your-teams-code-back-into-shape?utm_source=lefthook) 就是这种趋势的产物；它是用 Go 开发的，号称是 Node.js 和 Ruby 最快的多语言 Git 钩子管理器，但我相信它对其他栈也很有效。由于它是通过一个二进制文件工作的，`node_modules`目录受到的污染更少。

## 突出的特点

Lefthook 在 Git Hook 管理器中脱颖而出，因为它具有令人难以置信的特性，可以支持开发工作流，而没有任何管理上的麻烦。这里列出的所有功能在 Husky 中都不可用。

### 并行执行

Lefthook 是用 Go 开发的，让它更有潜力利用机器的并行性；支持任务的并行执行只需要一行代码。

并行执行是大规模 app 最重要的需求之一。对于较大的提交，可以并行使用拼写检查、类型检查、ESLint 和 StyleLint，为开发人员节省大量时间。稍后我会在 React 应用程序中分享一个例子。

### 选择要检查的文件

Lefthook 通过预构建的快捷键、glob 和 RegEx 过滤器，以及在子目录中运行的选项，帮助在有限的文件集上执行命令。

如果您在预提交任务中运行 JS linters 和 StyleLints，这将非常有用；理想情况下，类型检查任务将包含完整的文件列表，而 ESLint 和 StyleLint 将应用于暂存文件。以下是选择文件来执行任务的示例:

1.  全球

    ```
    pre-commit:   commands:     lint:       glob: "*.{js,ts,jsx,tsx}"       run: yarn eslint
    ```

2.  速记

    ```
    pre-commit:   commands:     frontend-linter:       glob: "*.{js,ts,jsx,tsx}" # glob filter for list of files       run: yarn eslint {staged_files} # {staged_files} or {all_files} - list of files
    ```

3.  自定义列表

    ```
    pre-push:   commands:     frontend-style:       files: git diff --name-only master # files with, diff with master.       glob: "*.js"       run: yarn stylelint {files}
    ```

### 单独的本地配置

虽然使用 Git 钩子的目的是在几个开发人员都在使用它的时候保持代码库的质量不变，但是人们可能需要一种方法来绕过它，在他们的本地机器上不同地执行特定的钩子。Lefthook 通过添加一个单独的文件，在本地环境中以不同的方式管理钩子，完全支持这种优化。

让我们创建一个`commit-msg`钩子来更好地理解本地配置。使用以下代码创建一个新挂钩:

```
lefthook add -d commit-msg

```

添加`-d`将创建`.lefthook/commit-msg`和`.lefthook-local/commit-msg`目录。第一个是通用的项目级脚本；第二种用于个人脚本，当任务只在创建者的机器上本地运行时，将使用这种脚本。理想情况下，`.lefthook-local`应该是`.gitignore`的一部分。

### 脚本和运行程序

使用 Lefthook，您可以用任何编程语言创建自定义任务。这给了我们通过提高速度来优化 Git 挂钩的自由。

除此之外，我们可以为这些脚本选择运行程序，比如 Docker VMs。下面是一个用 Bash runner 处理定制脚本的简单例子。

让我们创建一个 Bash 脚本来检查`.lefthook/commit-msg/template_checker`中的提交模板:

```
INPUT_FILE=$1
START_LINE=`head -n1 $INPUT_FILE`
PATTERN="^(ISSUE)-[[:digit:]]+: "
if ! [[ "$START_LINE" =~ $PATTERN ]]; then
  echo "Bad commit message, see example: ISSUE-123: some text"
  exit 1
fi

```

现在我们可以请求 Lefthook 运行我们的 Bash 脚本，方法是将这段代码添加到`lefthook.yml`文件中:

```
commit-msg:
  scripts:
    "template_checker":
      runner: bash
#    "template_checker.js": # We can also use JS file and reference it here
#      runner: node         # It will be executed using node as a runner
#    USING DOCKER AS RUNNER
#    "docker_hook.js":      # Similar file reference
#      runner: docker run -it --rm <container_id_or_name> {cmd} # Here {cmd} => command 

```

## 管道执行

尽管并行执行是一个突出的特性，但是 Lefthook 也支持命令的管道执行。如果任务执行需要函数管道，并且序列中的任何命令失败，Lefthook 将不会执行其他命令，从而确保任务的顺利完成。

这对于需要在执行前设置数据库的任务非常有帮助。这里有一个小例子:

```
database:
  piped: true
  commandy
    1_create:
      run: rake db:create
    2_migrate:
      run: rake db:migrate
    3_seed:
      run: rake db:seed

```

### 配置的继承

Lefthook 中的配置设置不仅限于本地和项目级别；我们还可以扩展配置。

配置中的继承有助于确保提交消息、安全审计和其他检查在产品或公司范围内的同步。以下是如何扩展任何其他配置:

```
extends:
  - ~/unicorns/configs/lefthook-extend-2.yml

```

## 在 React/React Native 中实现 Lefthook

作为 React 或 React 原生开发人员，如果上面的例子对您来说不够直观，请不要担心；下面是如何在你的应用中使用 Lefthook 的演示。

我们将从安装 Lefthook 开始:

```
npm install @arkweid/lefthook --save-dev # or yarn add @arkweid/lefthook -D

```

一旦安装了 Lefthook，你会在你的项目的根目录下看到`lefthook.yml`；这是保存我们所有作业和钩子的文件。请记住，这是一个 dev 依赖项，并且绑定到本地或全局安装的 Lefthook 二进制文件。它将你从依赖的噩梦中解放出来。

## 更换哈士奇

本节假设您的应用程序集成了 Husky 如果不是这样，请转到下一部分。

从从项目中卸载 Husky 开始:

```
npm uninstall husky # or yarn remove husky

```

卸载后，您可以从项目中删除`.husky`目录，或者根据您的实现从`package.json`中删除它的声明。

接下来，让我们将 Git 挂钩重置为默认值:

```
git config --unset core.hooksPath
rm .git/hooks/pre-commit
rm .git/hooks/pre-push

```

现在，将 Husky 实现移动到`lefthook.yml`:

```
pre-commit:
  commands:
    sometest:
      run: npm lint

pre-push:
  commands:
    anothertest:
      run: npm audit

```

## 提交前挂钩

让我们添加一个快速并行运行的预提交挂钩，以确保我们的应用程序代码在开发人员提交时始终适合合并。确保在应用程序中启用了 TypeScript。

因为我们已经安装了 Lefthook(如果您从上面跳到了这一节，请查看前面的代码以获得安装帮助)，所以让我们从为我们的应用程序添加一个预提交钩子开始。我们将预提交挂钩分成了四个任务，所有任务都可以并行运行:

*   类型检查
*   埃斯林特
*   代码拼写检查器
*   降价链接检查

这是我们的预提交挂钩，所有四个任务并行运行:

```
pre-commit:
  parallel: true
  commands:
    type-check:
      glob: '*.{ts,tsx}'
      run: yarn typecheck
    eslint:
      glob: '*.{js,ts,jsx,tsx}'
      run: yarn lint:eslint:fix {staged_files}
    stylelint:
      glob: '*.{js,ts,jsx,tsx}' # For CSS-in-JS
      run: yarn lint:style {staged_files}
    spelling:
      glob: '*.{js,ts,jsx,tsx,md}'
      run: yarn cspell {staged_files}
    markdown-link-check:
      glob: '*.md'
      run: npx markdown-link-check {staged_files}

```

如您所见，我们利用了 Lefthook 的功能来选择一大块文件来执行作业。文件的选择取决于任务，例如，必须在整个应用程序中执行类型检查以确保类型安全，而 lint、拼写检查和链接检查仅适用于暂存文件。

## 提交消息挂钩

接下来是提交消息挂钩。这个钩子将我们从管理变更日志和提交消息的可读性的麻烦中解救出来。

我们在这里使用的是[commit list](https://commitlint.js.org/#/)，但是如前所述，你可以使用另一个库或者你的定制脚本。以下是我们的`commit-msg`钩子外观代码:

```
commit-msg:
  commands:
    parallel: true
    lint-commit-msg:
      run: npx commitlint --edit
    spell-check:
      run: yarn cspell --no-summary {1}

```

## 预推钩

现在，是时候确保无论何时将代码放入我们的存储库中，它都经过了良好的质量和安全性测试。就像我们的预提交钩子一样，这些任务可以并行执行。这是我们的预推挂钩:

```
pre-push:
  parallel: true
  commands:
    test:
      run: yarn test
    packages-audit:
      run: yarn audit

```

## 简单地

在我看来，Lefthook 比其他 Git Hook 管理库更强大，同时给了我们更多的自由和简单性。有了并行化，任务执行得更快，而且有了定制运行程序的支持，Lefthook 是 CI/CD 环境中使用的一个好选择。配置的继承对于跨团队实施通用的编码实践非常有帮助。

如果您对 Lefthook 印象深刻，并希望从 Husky 迁移，这里是迁移指南。

我还准备了一个要点，将这里讨论的钩子添加到 React 和 React 本地应用中。试试看！

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

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)