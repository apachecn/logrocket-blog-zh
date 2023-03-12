# 使用 Node.js - LogRocket Blog 自动生成并发布一个 changelog

> 原文：<https://blog.logrocket.com/automatically-generate-and-release-a-changelog-with-node-js/>

变更日志是一段时间内您对项目所做的任何更改的详细记录。changelog 不仅可以作为修复 bug 和错误的起点，而且在向您的项目介绍新的开发人员时，它还是一个有价值的教育资源。

在本教程中，我们将探索一种使用 git 挂钩和 Node.js 自动生成和发布 changelog 的方法。我们将使用一种称为[常规提交](http://conventionalcommits.org/)的特定提交格式和一种称为[提交](https://github.com/commitizen/cz-cli)的工具来创建一条常规提交消息。然后，我们将使用一个名为`[standard-version](https://www.npmjs.com/package/standard-version)`的库来自动生成一个 changelog 和一个遵循[语义版本化](https://blog.logrocket.com/never-guess-about-project-history-again-31f65091f668/)的新发布版本。

最后，我们将使我们的 changelog 在整个开发团队中共享，这样每个人在项目中都遵循相同的约定。如果您想继续学习，可以在这个 [GitHub 库](https://github.com/imsingh/changelog)中找到最终代码。

我们开始吧！

## 在传统提交中构造提交消息

传统的提交规范通过提供创建特定提交历史的规则来改进提交消息。传统提交通过创建一个使用语义版本控制的版本，使得生成变更日志变得容易。

根据惯例，提交消息的结构应该如下:

```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]

```

让我们检查一下结构的细节:

`<type>`是一种影响发布版本号的提交类型。在语义版本控制中，`fix`类型影响补丁，`feat`类型影响次要版本。还有其他类型，但是，这些不影响版本的版本号。

`scope`是一个可选名词，描述由提交更改或更新的代码库部分。比如在`feat(pages)`中，pages 就是作用域。

在语义版本中，`!`与专业相关。当在作用域后使用时，`!`表示提交中有重大更改。

`<description>`是对代码变更的简要书面说明。例如，如果我们为`feat(pages)`写一个描述，它可能看起来像这样:`feat(pages): add contact page in the side menu`。

`body`是一个可选字段，您可以使用它来更详细地描述提交。`body`必须在描述后开始一行。`footer`也是可选字段。例如，一个页脚是`BREAKING CHANGE`，它将与语义版本化专业相关联。

### 提交消息示例

让我们看一些不同提交消息的例子:

#### 仅使用`type`和`description`提交消息:

```
feat: add the charging option for cars

```

#### 用`type`、`scope`和`description`提交消息:

```
fix(homepage): change title width of title

```

#### 使用`BREAKING CHANGE`提交消息:

```
refactor(api): remove the get api from reservations

BREAKING CHANGE: refactor to use the trip api instead of reservations api

```

## 创建我们的项目

让我们通过添加必要的工具来自动化我们的 changelog 和发布来开始我们的项目。首先，创建一个`command`提示符，我们将在其中添加以下代码块。

让我们创建一个基于 npm 的项目，并使它成为一个 git 存储库。如果您想自动化现有的存储库，可以跳过这一步:

```
# create project directory
mkdir changelog

# cd into project
cd changelog

# initialize npm project
npm init -y

# initialize git
git init

```

上面的代码块将用 1.0.0 版创建一个 git 存储库和一个 npm 包。

### 将`standard-version`添加到我们的项目

现在，让我们开始为我们的项目创建发布！您需要将`standard-version` npm 包安装到您的项目中，如下所示:

```
npm install --save-dev standard-version

```

您还需要将其添加到 npm 脚本中:

```
...
"scripts": {
  "release": "standard-version"
}
...

```

### 创建版本

创建一个名为`new-feature`的虚拟文件，并按如下方式提交:

```
touch new-feature
git add new-feature
git commit

```

添加以下 git 提交消息:

```
feat(new-feature): add a new-feature to our project

```

最后，让我们通过运行新添加的脚本在项目中创建一个发布:

```
npm run release

```

运行上面的命令将在屏幕上显示以下消息:

```
> [email protected] release /home/imsingh/Develop/inder/changelog
> standard-version

✔ bumping version in package.json from 1.0.0 to 1.1.0
✔ bumping version in package-lock.json from 1.0.0 to 1.1.0
✔ created CHANGELOG.md
✔ outputting changes to CHANGELOG.md
✔ committing package-lock.json and package.json and CHANGELOG.md
✔ tagging release v1.1.0
ℹ Run `git push --follow-tags origin master && npm publish` to publish

```

上面的消息执行以下操作:

*   将 SemVer 版本号从`1.0.0`增加到`1.1.0`我们增加了一个特性，因此，MINOR 从`0`更新到`1`
*   创建一个`CHANGELOG.md`文件，向其中添加所需的内容
*   提交上面的更改，创建一个`v1.1.0`标签
*   如果需要，打印出一条消息来推送标签并将我们的包发布到 npm

## `CHANGELOG.md`

现在，如果你打开`CHANGELOG.md`，你会看到下面的代码块，它包含了上面所做的修改:

```
# Changelog

All notable changes to this project will be documented in this file. See \[standard-version\](https://github.com/conventional-changelog/standard-version) for commit guidelines.

## 1.1.0 (2021-07-12)

### Features

* **new-feature:** add a new-feature to our project 11c0322

```

您还将看到创建的提交消息`standard-version`，它使用`git log -1`命令进行释放:

```
commit  #COMMIT_HASH (HEAD -> master, tag: v1.1.0)
Author: #AUTHOR_NAME <#AUTHOR_EMAIL>
Date:   #COMMIT_DATE

    chore(release): 1.1.0

```

提交消息的类型为`chore`，范围为`release`，`description`为`1.1.0`。

现在，您已经拥有了自动化您的 changelog 和发布所需的一切！然而，手动编写提交是乏味且容易出错的。让我们引入一些工具来平滑这个过程！

## 添加提交

您可以使用 Commitizen 来自动生成提交，而不是自己编写传统的提交。Commitizen 在`command`提示符下向您提问，并根据您的回答生成提交。

安装 Commitizen 软件包，如下所示:

```
npm install --save-dev commitizen

```

现在，初始化 Commitizen 以使用传统的 changelog 适配器:

```
npx commitizen init cz-conventional-changelog --save-dev --save-exact

```

适配器是一种配置，它告诉 Commitizen 在提示符下显示不同类型的提交。目前，有各种各样的[适配器](https://github.com/commitizen/cz-cli#adapters)可用，但是如果你愿意，你可以创建你自己的适配器。

现在，要使用 Commitizen，我们将添加一个 npm 脚本:

```
...
"scripts": {
    "commit": "cz"
}
...

```

此时，您应该创建一个`.gitignore`文件并忽略`node_modules`目录。

使用`git add`将`package.json`和`package-lock.json`添加到 git 暂存区。我们将通过运行下面的代码块进行提交:

```
npm run commit

```

上面的代码块还会提示您回答下面的指令。

`type`显示`types`列表，您可以从中选择。下面的列表来自我们之前安装的适配器:

```
? Select the type of change that you're committing: 
  feat:     A new feature 
  fix:      A bug fix 
  docs:     Documentation only changes 
❯ style:    Changes that do not affect the meaning of the code (white-space, formatting, missing semi-col
ons, etc) 
  refactor: A code change that neither fixes a bug nor adds a feature 
  perf:     A code change that improves performance 
(Move up and down to reveal more choices)

```

下面代码块中的`scope`表示常规提交的范围:

```
? What is the scope of this change (e.g. component or file name): (press enter to skip) 

```

对于`short description`，写下常规提交的简要说明:

```
? Write a short, imperative tense description of the change (max 82 chars):

```

在`longer description`中，描述常规提交的`body`:

```
? Provide a longer description of the change: (press enter to skip)

```

下面代码块中的两个问题会生成一个包含重大更改的提交:

```
? Are there any breaking changes?
? Describe the breaking changes:

```

在`issues related to commit`部分，您可以参考来自 JIRA GitHub 或其他类似工具的问题:

```
? Does this change affect any open issues?
? Add issue references (e.g. "fix #123", "re #123".):

```

一旦您根据需要回答了这些提示，您将得到如下所示的提交:

```
Author: #AUTHOR_NAME <#AUTHOR_EMAIL>
Date:   Mon Jul 12 21:10:17 2021 +0200

    feat(some-scope): a short description

    a long description

    BREAKING CHANGE: it breaks

    123

```

## 添加 commitlist 以实施规则

为了确保我们项目中的所有开发人员都遵循相同的约定，我们将使用带有 [Husky](https://typicode.github.io/husky/#/) 和 commitlint 的 git 挂钩。

### 安装所需工具

首先，让我们通过运行下面的代码块来安装 commitlint 和 Husky:

```
# Install commitlint cli and conventional config
npm install --save-dev @commitlint/config-conventional @commitlint/cli

# Install Husky
npm install husky --save-dev

```

### 配置提交列表

要配置 commitlint，我们需要创建一个名为`commitlint.config.js`的配置文件，并添加以下代码:

```
module.exports = {extends: ['@commitlint/config-conventional']}

```

为了在提交之前 lint 消息，我们需要通过运行以下命令使用 Husky 的`commit-msg`钩子:

```
# Activate hooks
npx husky install

# Add hook
npx husky add .husky/commit-msg 'npx --no-install commitlint --edit "$1"'

```

您可以添加`husky install`作为 npm 准备脚本，但是，这一步是可选的。`husky install`将确保每个使用本回购协议的开发商在使用本项目前安装哈斯基挂钩:

```
... 
"scripts": {
...
  "prepare": "husky install"
}

```

我们仍将使用 git commit 来使我们的提交遵循前面描述的约定。如果 git 消息中有错误，commitlint 将引发以下错误:

```
git commit -m "This is a commit"  
⧗   input: This is a commit  
✖   subject may not be empty [subject-empty]  
✖   type may not be empty [type-empty]  

✖   found 2 problems, 0 warnings  
ⓘ   Get help: \[https://github.com/conventional-changelog/commitlint/#what-is-commitlint\](https://github.com/conventional-changelog/commitlint/#what-is-commitlint)  

husky - commit-msg hook exited with code 1 (error)

```

### 管理发布的最终工作流程

要管理您的发布，您可以遵循下面列出的工作流程:

1.  创建您的要素并提交它们。如果提交消息不符合约定，commitlint 将引发错误
2.  在命令行中执行`npm run commit`,使用 Commitizen 进行提交
3.  运行`npm run release`来创建一个变更日志和一个基于语义版本的发布

要使用 CI/CD 创建一个发布，请查看[语义发布](https://github.com/semantic-release/semantic-release)。

## 摘要

在这篇文章中，您学习了如何使用 git hooks 和 Node.js 创建一个自动的 changelog 和一个基于语义版本的发布。接下来，我们使用 commitlint 和 Husky 来自动编写我们的提交。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.