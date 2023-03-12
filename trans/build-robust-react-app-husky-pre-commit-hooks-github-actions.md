# 用 Husky 预提交钩子和 GitHub 动作构建一个健壮的 React 应用程序

> 原文：<https://blog.logrocket.com/build-robust-react-app-husky-pre-commit-hooks-github-actions/>

从头开始构建复杂的 React 项目可能具有难以置信的挑战性。他们需要前期知识和对整个生态系统的深刻理解。

在这个已经复杂的环境中，React 应用程序的强大设置对其成功至关重要，因为它可以立即改善开发人员的体验，并让您的团队专注于真正重要的事情——构建令人惊叹的产品。

好奇如何着手构建一个健壮的 React 应用程序吗？让我们开始吧。

## 使用 TypeScript、React 测试库、ESLint 和更漂亮的工具构建基础

为我们的 React 应用程序创建健壮设置的第一步很简单。我们只需要四种主要原料:

*   TypeScript :在 React 应用中利用静态类型的强大工具。使用 TypeScript 为您的团队创建文档、重构和调试变得更加容易
*   [React 测试库](https://testing-library.com/docs/react-testing-library/intro/):确保开发人员拥有编写基本单元测试所需的设置。健康的测试覆盖有助于自信地开发、重构和部署应用
*   一个可靠的和高度可定制的 linter 对于一个健壮的项目设置是必不可少的
*   更漂亮的:确保你的团队使用一致的代码格式

让我们把他们安置好！

## 从创建 React 应用程序开始

首先，让我们用下面的命令创建一个 React 应用程序:

```
npx create-react-app react-app-setup --template typescript

```

因为我们使用了 TypeScript 模板，所以我们已经有了 TypeScript。让我们也将下面的脚本添加到`package.json`中，以使类型检查更容易一些:

```
"typescript": "tsc --project tsconfig.json --noEmit"

```

此外， [Create React App](https://create-react-app.dev/) 给了我们一个非常基本的 React 测试库设置，`package.json`中的一个测试脚本，以及一个可以使用的通过测试。

这是一个良好的开端，但还有更多的事情要做。

> **注意**:如果您无法使用 Create React App 进行初始项目设置或者更喜欢不同的工具链，您可能需要手动设置 TypeScript 和 React 测试库。

## 为林挺安装 ESLint

要设置我们的 linter，我们首先需要安装它:

```
 npm install eslint --save-dev

```

然后用下面的命令初始化它:

```
npm run eslint --init

```

根据提示，我们的最终输出应该类似于下面的屏幕截图:

![Final Output](img/efa184e1c845885cecaba45ebd9043ea.png)

最后，我们应该在项目的根目录下有一个类似于下面的`.eslintrc.json`文件:

```
{
    "env": {
        "browser": true,
        "es2021": true
    },
    "extends": [
        "eslint:recommended",
        "plugin:react/recommended",
        "plugin:@typescript-eslint/recommended"
    ],
    "parser": "@typescript-eslint/parser",
    "parserOptions": {
        "ecmaFeatures": {
            "jsx": true
        },
        "ecmaVersion": 12,
        "sourceType": "module"
    },
    "plugins": [
        "react",
        "@typescript-eslint"
    ],
    "rules": {
    }
}

```

我们可以根据需要额外配置棉绒设置。让我们也给`package.json`添加一个 lint 脚本:

```
"lint": "eslint src --color"

```

## 更漂亮的格式

最后，我们需要安装更漂亮的:

```
npm install prettier --save-dev

```

并在项目的根目录下创建一个基本配置文件`.prettierrc.json`。该文件中的设置完全取决于项目的需要和要求。

```
{
    "printWidth": 80,
    "trailingComma": "all",
    "tabWidth": 4,
    "semi": false,
    "singleQuote": true,
    "arrowParens": "avoid"
}

```

让我们也给`package.json`添加一个脚本来检查我们的代码格式是否正确:

```
"prettier": "prettier src -check"

```

## 使用 Husky 和 lint-staged 添加预提交挂钩

我们现在已经有了一些优秀的工具，但是没有简单的方法来自动利用它。进一步改进我们的设置的一个好方法是给我们的项目添加一个`pre-commit`钩子，它将触发我们的工具执行某些检查。

在我们的钩子中，我们将检查 linter 错误和警告、类型错误、失败的测试，并且它还将自动格式化我们的代码。对于预提交钩子来说，这可能听起来太多了——因为确实如此！为了使这个过程更有效，这些检查不会在整个项目中执行，只会在我们将要提交的暂存文件中执行。

首先，我们需要安装[哈士奇](https://typicode.github.io/husky/#)和[皮棉阶段](https://github.com/okonet/lint-staged):

```
 npm install husky lint-staged --save-dev

```

为了确保我们只能检查暂存文件的类型，我们还应该安装 [tsc-files](https://github.com/gustavopch/tsc-files) :

```
npm install tsc-files --save-dev

```

让我们添加 lint-staged 配置，在项目的根位置定义对`lint-staged.js`的必要检查:

```
module.exports = {
    '*.{js,jsx,ts,tsx}': [
        'eslint --max-warnings=0', 
        'react-scripts test --bail --watchAll=false --findRelatedTests --passWithNoTests',
        () => 'tsc-files --noEmit',
    ],
    '*.{js,jsx,ts,tsx,json,css,js}': ['prettier --write'],
}

```

接下来，在`package.json`中为它添加一个脚本:

```
"lint-staged": "lint-staged --config lint-staged.js",
```

> **注意:**根据我们正在进行的项目，即使`lint-staged`已经就位，在每次提交时执行所有四个检查可能会变得太重，并给开发人员尽早提交和经常提交造成障碍。如果事实证明是这样的话，那么减少预提交钩子中的负载以支持在 CI 中执行相同的检查是一个很好的实践(见下文)。

要设置预提交挂钩，在`package.json`中创建一个脚本:

```
"husky-install": "husky install"

```

并使用以下命令运行它:

```
npm run husky-install

```

然后使用以下命令创建挂钩:

```
npx husky add .husky/pre-commit "npm run lint-staged"

```

项目根目录下的`.husky`文件夹中会自动生成以下文件:

```
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"
npm run lint-staged

```

从现在开始，`lint-staged`配置中定义的检查将在每次提交时运行。我们已经成功地自动化了我们的工具，并且当我们进行新的提交时，我们不必担心它。

但是等等，我们目前只对暂存文件执行检查。把整个项目考虑进去怎么样？有了这些部分检查，我们怎么能确定我们的应用程序构建成功，并且它真的准备好部署了呢？

## 具有 GitHub 操作的持续集成(CI)工作流

如上所述，我们可以通过预提交钩子执行的本地检查是有限制的。为了充分利用我们的工具，我们需要对整个项目进行全面的检查，并在出现问题时通知我们和我们的团队。怎么会？

让我们用 [GitHub Actions](https://github.com/features/actions) 建立一个连续的集成工作流程。

首先，我们需要创建一个 GitHub repo，并将我们的项目推进其中:

```
git remote add origin [email protected]:[your-username]/react-app-setup.git
git branch -M master
git push -u origin master

```

然后，要设置我们的 CI 工作流，我们进入 **GitHub 操作**，点击**新工作流**，然后选择**自行设置工作流**。最后，我们需要将以下内容添加到我们的`main.yml`文件中。

```
name: React App CI
# Controls when the action will run.
on:
    # Triggers the workflow on push or pull request events but only for the master branch
    push:
        branches: [master]
    pull_request:
        branches: [master]
# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
    react-app-workflow:
        # The type of runner that the job will run on
        runs-on: ubuntu-latest
        # Steps represent a sequence of tasks that will be executed as part of the job
        steps:
            - uses: actions/[email protected]
            - name: Use Node.js 14.x
              uses: actions/[email protected]
              with:
                  node-version: 14.x
            - name: npm ci
              run: npm ci
            - name: test
              run: npm test
            - name: format
              run: npm run prettier
            - name: lint
              run: npm run lint
            - name: typescript
              run: npm run typescript
            - name: build
              run: npm run build --if-present

```

点击`Start commit`，填写表格，并提交新的工作流以查看其运行情况。我们来分解一下配置。

首先，我们定义何时触发工作流。在我们的例子中，这将在主分支的`push`和`pull_request`发生。

然后我们定义我们的跑步者，`ubuntu-latest`。最后，我们描述了成功运行工作流所需完成的步骤。

如您所见，我们正在执行与提交前挂钩相同的检查——lint、测试、格式和类型检查——但这一次，我们不仅限于暂存文件。

此外，我们测试我们的项目依赖项是否在工作流开始时正确安装，以及构建是否成功。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

这个工作流可能非常不同，在真实的 React 项目中可能会复杂得多。它可能至少包括多个环境、不同的集成、e2e 测试和一个部署步骤——CI/CD 的 CD 部分。您还可以使用许多其他 CI/CD 工具获得类似的结果。以上只是一个基本的说明，以突出 CI 检查的力量以及它们通过自动化使我们的生活更加轻松的能力。

## 结论

两个基本元素一起形成了一个健壮的项目设置:合适的工具和合理的自动化。上面的例子集中在一个 React 应用程序上，展示了一种创建健壮设置的可能方法。

许多相同的原则、工具和概念适用于更广泛的软件项目。

此外，请记住，上面的设置是高度简化的。它旨在作为一个说明，而不是一个明确的指南，不能取代对您的项目和团队的理想设置的批判性思考。

好奇自己玩代码？在[本报告](https://github.com/IvaKop/react-app-setup)中找到一个工作示例。

如果您觉得这篇文章很有用，请在 Twitter 上关注我，了解更多技术内容！

编码快乐！✨

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