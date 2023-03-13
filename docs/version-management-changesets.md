# 带有变更集的版本管理指南

> 原文：<https://blog.logrocket.com/version-management-changesets/>

升级软件包通常是开发软件最令人沮丧的方面之一。含糊不清的发行说明会使过程陷入困境和混乱。

包维护者使用一种叫做[语义版本化](https://semver.org/) (semver)的模式来描述新版本中的变化。semver 告诉消费应用程序如何处理更新。如果一个项目很小，维护人员很容易为一个版本选择 semver 类型。对于小项目来说，执行其他任务，比如编写发布说明，也很快。但是，随着项目越来越大，这种类型的工作会变得非常耗时。

例如， [Jest 是一个大型的 monorepo](https://blog.logrocket.com/end-to-end-testing-react-jest-puppeteer/) ，其中的包相互依赖，是公共的，并且是单独消费的。随着 pull 请求的频繁合并，Jest 维护人员常常很难弄清楚在一个包版本中发布了什么。

维护人员需要跟踪合并变更，记录应用程序应该如何升级，更新存储库中的包，并发布包。变更集是最重要的工具之一，旨在简化 monorepos 的版本管理和变更日志。

在本文中，我们将介绍变更集，并回顾一个例子来更好地说明使用变更集进行版本管理的好处。

我们开始吧！

*向前跳转:*

## 变更集的作用是什么？

在不使用工具的情况下，维护人员可以在拉式请求描述中提供清单，以提醒贡献者向他们的变更添加细节。维护者的目标是鼓励许多贡献，并希望使它们无缝和简单。这种方法的目标是减轻维护人员的负担，减轻他们的工作量。

除了当评论者遗漏某些东西时，这种策略是有效的。在这些情况下，维护人员仍然有工作要做。当多个拉请求被合并时，维护者必须将所有内容合并到一个单一的版本/文档中。他们可能很少有关于拉请求的上下文，因为在合并和包发布之间可能有几个星期。

changesets 是一个帮助 monorepos 进行版本管理的工具。它为贡献者提供了一个 CLI 界面来描述他们在拉请求中的更改以及 semver bump 类型。这些信息被恰当地称为“变更集”。

该工具用于执行版本控制，包括使用自上一个版本以来的所有“变更集”,确定最大 semver bump 类型，更新变更日志，以及更新适当的内部包。被卖方收回的汽车

## 示例场景:处理基本代码更改

为了展示使用变更集的能力和优势，让我们看看在有和没有变更集的情况下，如何处理对代码库的不同变更。

对于我们的例子，我们将考虑一个专门为宠物店开发的开源电子商务商店平台。应用程序背后的团队很难组织代码，他们希望让部分代码库更适用于不同的应用程序。

他们认为创建单一回购将是一个合适的解决方案。因此，他们将他们认为通用的代码片段分成不同的包:

![Pet Store Monorepo](img/11284688a9d1ffd475eb96247af08aec.png)

The pet store monorepo.

像`ordering`这样的包在代码库之外被采用。这个特殊的包通过处理商店中商品的订购，使其他开发人员的工作变得更加轻松。在接下来的一周里，核心团队的开发人员和外部贡献者对包进行修改。

内部开发人员添加了一个新功能，可以轻松更新商品库存:

```
// Updates the stock of an item in the database
export function updateStock(item, quantity) {}

```

这一变化需要一个小的改动，因为获得这一更新的应用程序或软件包可以安全地升级，而不需要对任何现有的 API 进行重大更改。

这不是唯一发生的变化。一个外部贡献者报告了一个严重的错误，在添加项目时,`sku`无法输入到数据库中。因此，`sku`被添加为`addItem`功能的必填字段:

```
// old
export function addItem(item) {}
// new
export function addItem(item, sku) {}

```

这一改变需要一个大的改动，因为应用程序或软件包必须修改代码才能安全地升级到这个新版本。

让我们看看这两个变更是如何被释放的，无论是没有变更集的还是有变更集的。

### 没有变更集

在最近的一个版本之后，一个外部开发人员打开了对 monorepo 包`ordering`的 pull 请求，以添加一个新特性:

![GitHub Pull Request Containing New Utility Function](img/a3fd8f578c12e569480e8fb11269f9dd.png)

GitHub pull request containing the new utility function.

仓库的维护者很高兴，但是注意到贡献者没有在 pull 请求中更新发布说明。这在指南中有所概述，但是贡献者有时会因为急于及时地做出改变而忽略了这一点。

维护者要求贡献者添加注释:

![Comment by a Repository's Maintainer](img/1bba0e2aa4c12ea8688649773a3b0b5a.png)

A comment by the repository’s maintainer.

这种变化，以及其他的变化，被合并在一起，维护者在几个星期后发布这个包。

作为这个过程的一部分，维护人员需要弄清楚 semver 凸起类型。他们还需要检查每一个拉取请求，看看是否有任何突破性的变化、特性添加或错误修复。

在这种情况下，维护人员发现`addItem`变更增加了`sku`，并决定需要一个完整的版本冲击。作为发布的一部分，他们需要找到依赖于`ordering`包的每个包，比如`pet-store`应用，并将其从 1.0.0 版升级到 2.0.0 版。维护者的任务是升级应用以适应变化。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

这个过程需要一段时间，因为贡献者没有提供太多的书面文档。接下来，维护人员必须创建一个 changelog 条目，再次检查自上次发布以来合并的所有 pull 请求。

这项工作，可能还有其他任务，很麻烦，容易出错，并且使维护人员的工作很乏味。

### 使用变更集

对于变更集，这个过程看起来有点不同。

当一个贡献者做出变更时，他们运行`yarn changeset`并创建一个“变更集”。它们描述了新的`updateStock`函数，消费包如何使用它，哪个包受到了变化的影响，以及 semver bump 类型。然后，他们推送变更并打开一个拉取请求:

![Changeset File Pull Request](img/3f3f1f3a29a91fada08664afd6a43ba5.png)

The “changeset” file inside the pull request.

维护人员简单地审查拉取请求并将其合并；没有问题问！“变更集”文件包含了简化发布过程所需的一切。

之后，会有额外的拉请求进来，包括一个主要版本的提升。这是由于`addItem`增加了`sku`参数:

```
---
"changesets-package-ordering": major
---
Add sku argument to addItem function
The `sku` argument was added to the `addItem` function. It is a required argument due to changing business needs.
Example usage:
...

```

现在维护者准备发布下一个版本的`ordering`包。为此，他们需要做的就是运行`yarn changeset version`命令。

该命令删除所有本地“变更集”文件，创建 changelog 条目，并自动在包本身和包依赖项中碰撞`ordering`包的版本，如`pet-store`应用程序:

```
## 2.0.0
### Major Changes
- ea13cc5: Add sku argument to addItem function
  The `sku` argument was added to the `addItem` function. It is a required argument due to changing business needs.
### Minor Changes
- 7034f8a: Add the updateStock utility function
  The `updateStock` utility function is used by applications to update the stock of an item.

```

由于贡献者必须为他们的突破性变化提供详细的文档，维护者很容易升级`pet-store`应用程序以符合这些变化。

在变更集的帮助下，维护人员能够轻松快速地创建一个版本，并以标准化和可复制的方式在 monorepo 中更新应用程序。

## 自动化变更集

我们希望确保贡献者和维护者始终遵循正确的步骤。这就是自动化可以使变更集工作更加容易的地方。

例如，我们可以通过安装[变更集 GitHub Bot](https://github.com/apps/changeset-bot) 来确保贡献者总是创建一个“变更集”。当“变更集”丢失时，该 bot 将对拉请求进行注释，如下所示:

![Changesets GitHub Bot Comment](img/390a425983fc081289186bb503ef5377.png)

changesets GitHub Bot commenting on our example pull request.

另一种确保没有“变更集”的拉请求不能被合并的方法是在 GitHub 动作中运行`yarn changeset status`命令。如果参与者忘记添加“变更集”，操作将失败:

![Action Failing Due to Lack of a Changeset](img/9b186d1f104c73eef1ecf0c653ecbc4c.png)

An action failing when a contributor doesn’t add a changeset.

当一个新版本准备好被创建时，维护人员可以利用`changesets` GitHub 动作进一步自动化这个步骤。这个操作将运行前面提到的`yarn changeset version`命令，并创建一个包含所有文件更改的 pull 请求。

## 结论

使用 changesets 包消除了许多使发布过程成为维护者负担的步骤。有了变更集，贡献者可以被提醒详细记录他们的变更，维护者可以使用一个 CLI 命令轻松地创建新版本的包。高质量、周期性的软件包发布也为消费者的升级过程提供了便利。