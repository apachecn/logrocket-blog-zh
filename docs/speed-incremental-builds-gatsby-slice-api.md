# 如何用 Gatsby 的 Slice API 加速增量构建

> 原文：<https://blog.logrocket.com/speed-incremental-builds-gatsby-slice-api/>

增量构建是任何 Gatsby 站点开发过程的重要部分。它们允许您对您的站点进行更改并实时查看结果，而不必等待完整的构建完成。

但是，如果您有大量数据或复杂的构建过程，增量构建会变得很慢。这可能会令人沮丧，并阻碍你的生产力。

用 Gatsby 加速增量构建的一种方法是使用新的 Slice API。在本文中，我们将探索 Gatsby 的 Slice API，包括如何使用它，它的优点是什么，它在引擎盖下是如何工作的，等等。

我们将涵盖:

如果您已经熟悉 Gatsby 中的[增量构建，那么按照本教程学习会有所帮助。](https://blog.logrocket.com/gatsby-incremental-builds/)

## 开始使用 Gatsby 中的 Slice API

Slice API 允许您将大量数据分解成较小的块或“片”，可以按需加载。通过减少每次构建过程中需要处理的数据量，切片有助于提高站点的性能。

在我们开始之前，请注意 Slice API 只在 Gatsby 5 中可用。你需要升级到 v5 才能使用它，或者启动一个全新的 Gatsby 5 项目。

### 定义切片

要使用 Slice API，首先需要使用 GraphQL 查询定义一个 slice [。该查询指定哪些字段应该包含在切片中，以及应该如何对它们进行筛选和排序。](https://blog.logrocket.com/data-fetching-gatsby-graphql/)

例如，假设您收集了大量的博客文章。如果您想要为最近的帖子创建一个切片，您可以像这样定义查询:

```
query RecentPostsSlice {
  allMdx(sort: {fields: frontmatter___date, order: DESC}, limit: 10) {
    nodes {
      frontmatter {
        title
        date
      }
      excerpt
    }
  }
} 
```

### 创建切片组件

接下来，您需要创建一个负责呈现切片的切片组件。该组件应该接收切片的数据作为道具，并使用它以您选择的任何方式呈现切片。

例如，您可能会创建一个`RecentPosts`组件来呈现最近的博客文章的链接列表:

```
const RecentPosts = ({ data }) => (
  <ul>
    {data.allMdx.nodes.map((post, index) => (
      <li key={index}>
        <Link to={post.frontmatter.slug}>{post.frontmatter.title}</Link>
      </li>
    ))}
  </ul>
) 
```

### 在页面上呈现切片

要在页面上加载切片，您可以使用`StaticQuery`组件，它允许您指定 GraphQL 查询和呈现函数。render 函数将接收切片的数据作为道具，您可以使用这些数据在页面上呈现切片。

例如，您可以像这样使用`RecentPosts`组件:

```
import { StaticQuery, graphql } from "gatsby"

const BlogSidebar = () => (
  <StaticQuery
    query={graphql`
      query RecentPostsSlice {
        allMdx(sort: {fields: frontmatter___date, order: DESC}, limit: 10) {
          nodes {
            frontmatter {
              title
              date
            }
            excerpt
          }
        }
      }
    `}
    render={data => <RecentPosts data={data} />}
  />
) 
```

以这种方式使用 Slice API 可以减少每次构建过程中需要处理的数据量，从而有助于提高站点的性能。这可以使增量构建更快，特别是当您有大量数据或复杂的构建过程时。

除了对数据进行切片，Gatsby Slice API 还包括对分页的支持，如果您有大量数据，这将非常有用。

## Slice API 的优势

在引入 Slice API 之前，还有其他几种方法来优化 Gatsby 站点的性能。这些选项包括延迟加载组件、优化图像和使用`gatsby-image`插件。

这些方法有助于提高网站的性能。但是，它们也有各自的局限性，它们不提供一种按需轻松加载大量数据的方法。

### 分解和存储数据

Slice API 的一个主要优点是，它允许您轻松地将大量数据分解成可以按需加载的较小数据块。如果您有大量并非每页都需要的数据，这可能特别有用。

通过将数据分割成更小的块，Slice API 允许您只加载需要的数据，从而减少需要处理的数据总量。

Slice API 的另一个优点是它提供了内置的缓存支持。这可以通过将 GraphQL 查询的结果存储在缓存中来进一步提高性能，这样就不必在每次页面加载时都重新执行这些查询。

### 将 Slice API 与传统的 Gatsby 模式进行比较

与现有的布局组件和页面组件模式相比，Slice API 在可重用性、可伸缩性、动态内容和性能方面更胜一筹。

Slice API 允许您创建可跨多个页面使用的可重用组件，从而更容易保持设计的一致性并减少代码的重复。

它还可以帮助您随着内容需求的变化而扩展您的网站。您可以根据需要添加或移除组件，而不必更新整个代码库。

此外，与页面组件模式和布局组件模式相比，Gatsby 的 Slice API 提供了一种更灵活的方式来处理动态内容。使用 Slice API，您可以轻松地从内容管理系统中检索和显示数据。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

最后，Slice API 使您能够通过在每个页面上只加载必要的组件来优化网站的性能。这导致了更快的页面加载和更好的用户体验。

## 使用 Slice API 时要考虑的实现权衡

Slice API 的主要缺点之一是缺乏对嵌套切片占位符的支持。这意味着，如果您有一个高级组件(如包含切片的布局)，则该布局中不能有其他切片组件。

这可能会限制页面布局的灵活性，并需要一些额外的变通办法来实现某些设计。

此外，要使用 Slice API，您需要遵循特定的文件夹结构和命名约定。这可能需要一些时间来适应，并要求您重新构建现有的代码库。

Slice API 还要求对 GraphQL 片段和查询有更深入的理解，这可能比常规的 Gatsby 模式更复杂。您还需要管理额外的模板文件和切片，这会给您的开发工作流程增加一些额外的开销。

最后，使用 Slice API 有一个学习曲线，尤其是如果您是 Gatsby 或 GraphQL 的新手。虽然一旦你熟悉了它，它会成为一个强大的工具，但你可能需要一段时间来适应并开始看到它的好处。

## 在引擎盖下探索盖茨比切片 API

我们已经看到了如何以及为什么使用 Slice API。现在，让我们看看 Slice API 是如何工作的，这样我们可以更好地理解这个有用的工具。

当您使用切片 API 在页面上加载数据时，切片的数据仅在呈现页面时加载。这意味着，如果您对共享组件进行了更改，只会重新生成当前正在呈现的页面，而不是站点上的所有页面。

例如，假设您有一个名为`RecentPosts`的共享组件，它显示最近的博客文章列表。假设您对这个组件进行了更改，并重新构建了您的站点。

在这种情况下，只会重建当前正在呈现的页面，而不是站点上的所有页面。这可以大大减少重建站点所需的时间，尤其是当您有大量页面时。

当然，在某些情况下，您可能需要重新构建站点上的所有页面，例如，当您对每个页面上使用的共享组件进行更改时。

然而，在大多数情况下，Slice API 可以帮助减少需要重新构建的页面数量，从而提高站点的性能和效率。

## 结论

我们已经讨论了使用 Slice API 的许多原因。让我们回顾一下我向您推荐它的一些主要原因。

Gatsby Slice API 是一个特性，它允许开发人员在 Gatsby 项目中构建模块化和可重用的组件。

开发人员可以创建更小、更集中的组件，这些组件可以很容易地在整个站点中重用。这使得开发更加高效、灵活和可维护，同时避免了一次又一次重复相同代码的需要。

此外，Slice API 允许更好地分离 Gatsby 项目中的关注点。开发人员可以创建特定于站点特定部分或功能的组件，而不必创建处理多种职责的大型整体组件。

Slice API 在 Gatsby 中允许开发人员创建一个导出 GraphQL 查询的组件。然后，这个组件可以被其他组件查询和组合，这使得构建模块化和可重用的组件变得很容易。

此外，Gatsby Slice API 提供了优于 Gatsby 项目中常用的其他模式的优势。

最后，Gatsby Slice API 相对较新，预计在未来的版本中会变得更强大，提供更大的灵活性和易用性。

总的来说，Slice API 是优化 Gatsby 站点性能的强大工具，它提供了一种按需加载大量数据的简单方法，提高了站点的速度和效率。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)