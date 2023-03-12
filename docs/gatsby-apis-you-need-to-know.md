# 你需要知道的盖茨比 API

> 原文：<https://blog.logrocket.com/gatsby-apis-you-need-to-know/>

盖茨比生态系统有很多合作在进行。这可以归因于它的开源性质，[许多人向 Gatsby 存储库发送 pull 请求](https://github.com/gatsbyjs/gatsby/pulls?q=is%3Apr+is%3Aopen+sort%3Aupdated-desc)来贡献文档、框架本身，或者创建 starters/showcases——这是用 Gatsby 构建的网站，显示在[官方网站](https://www.gatsbyjs.org/showcase/)上。

生态系统的大部分利用 Gatsby 构建，这很棒，尽管 Gatsby 为想要真正扩展框架功能的人提供了一些 API。在这篇博文中，我们将会看到这些 API 中的一些，以及它们派上用场的一些用例。

## 盖茨比是怎么工作的？

Gatsby 为 web 提供了最好的构建方式，同时可以针对任何目的进行无限的定制和扩展。认为 Gatsby 只适用于特定用例(博客、附带项目)的想法是不准确的，因为 Gatsby 提供了一个允许它扩展其功能的层，这意味着随着应用程序的增长，Gatsby 的功能也会随之增长，同时考虑到性能。

例如，如果您的应用程序需要更加自定义的模式定制，这就是 Gatsby 的模式定制 API 所提供的。另外，如果你想为你的平台扩展 [Webpack](https://webpack.js.org/) 配置，或者你还没有使用插件来处理这个问题，那么`onCreateWebpackConfig`可以在`gatsby-node.js`文件中导出。

让我们从查看处理 Gatsby 应用程序中插件功能的`gatsby-confi`文件开始。在这个文件中，我们可以定义站点的元数据和其他常规配置。这个文件应该在你的 Gatsby 站点的根目录下。

如果您用`gatsby new`命令创建了一个 Gatsby 站点，那么在您站点的目录中应该已经有一个示例配置文件了。配置文件应该导出一个 JavaScript 对象。在这个对象中，您可以定义几个不同的配置选项。例如:

```
module.exports = {
  siteMetadata: {
    title: `Name of your application`,
  },
  plugins: [
    `Name-of-plugin`,
    {
      resolve: `Name-of-plugin`,
      options: {
        optionA: true,
        optionB: `Another option`,
      },
    },
  ],
}
```

> 插件作者注意:如果你的插件执行异步操作(磁盘 I/O，数据库访问，调用远程 API，等等。)必须返回一个承诺(显式地使用`Promise` API 或者隐式地使用`async` / `await`语法)或者使用传递给第三个参数的回调。Gatsby 需要知道插件何时完成，因为为了正确工作，一些 API 需要先完成以前的 API。–[盖茨比文件](https://www.gatsbyjs.org/docs/)

## 盖茨比·阿皮斯

### 扩展模式功能

在本文的前面，我提到了一个 API 来帮助扩展您的数据的 GraphQL 模式，这些特性通常是插件作者、试图修复由自动类型推断创建的 GraphQL 模式的用户、为更大的站点优化构建的开发人员以及任何对定制 Gatsby 的模式生成感兴趣的人所需要的。

这个例子中的 API 是`a href="https://www.gatsbyjs.org/docs/node-apis/#createSchemaCustomization" target="_blank" rel="noopener">createSchemaCustomization`(在 Gatsby v2.12 和更高版本中可用)和`[sourceNodes](https://www.gatsbyjs.org/docs/node-apis/#sourceNodes)`API。

一个直接的用例是当自动生成的方案中的一个特定字段有一个没有明确定义的类型时。例如，下面的代码块是一个作者的 Gatsby 的 GraphQL 层中自动生成的节点表示:

```
type AuthorJson implements Node {
  id: ID!
  parent: Node!
  children: [Node!]!
  internal: Internal!
  name: String
  firstName: String
  email: String
  joinedAt: Date
}
// Below is the query that results to the Scheme above.
[
  {
    "name": "Doe",
    "firstName": "Jane",
    "email": "[email protected]",
    "joinedAt": "2018-01-01"
  }
]
```

> 值得注意的是，`author.json`中的数据本身并不提供关于作者字段的类型信息。
> 
> 为了将数据形状转换成 GraphQL 类型定义，Gatsby 必须检查每个字段的内容并检查其类型。这种方法的问题在于它非常耗时，并且还会导致可伸缩性问题。此外，如果一个字段中的值属于不同的类型，Gatsby 无法确定哪一个是正确的。这样做的后果是，如果数据源发生变化，类型推断可能会突然失败。

在添加新作者的情况下，我们可以从下面的代码块中看到,`joinedAt`的类型是`Date`和`String`值:

```
{
  "name": "Doe",
  "firstName": "John",
   "email": "[email protected]",
   "joinedAt": "201-04-02"
 }
]
```

通过使用 [`createTypes`](https://www.gatsbyjs.org/docs/actions/#createTypes) 动作向 Gatsby 提供显式类型定义，我们可以确保模式将只显示一个`Date`类型。它接受 GraphQL 模式定义语言中的类型定义:

```
exports.createSchemaCustomization = ({ actions }) => {
  const { createTypes } = actions
  const typeDefs = `
    type AuthorJson implements Node {
      joinedAt: Date
    }
  `
  createTypes(typeDefs)
}
```

> 注意:其余的字段不必提供，它们仍将由 Gatsby 的类型推断来处理。

你也可以完全控制这个方案，看看官方文档了解更多信息。

### 添加第三方架构

除了能够定制现有模式之外，Gatsby 还提供了在构建前端时从其他应用程序导入现有模式的能力，而无需修改。使用`[addThirdPartySchema](https://www.gatsbyjs.org/docs/actions/#addThirdPartySchema)` API 可以做到这一点，模式必须是 graphql-js GraphQLSchema 对象。

值得注意的是，该模式很容易破坏主 Gatsby 模式，因此建议确保这种情况不会发生(例如，通过给模式命名空间):

```
addThirdPartySchema: (
  {schema }: { schema: GraphQLSchema },
  plugin: IGatsbyPlugin,
  traceId?: string):
 IAddThirdPartySchema => {
  return {
  type: `ADD_THIRD_PARTY_SCHEMA`,
    traceId,
  payload: schema,
  }
},
```

### 从其他节点扩展/转换节点

> “节点”是盖茨比数据系统的中心。添加到 Gatsby 的所有数据都是使用节点建模的。

当创建一个插件来作用于 Gatsby 代码库时，它被称为[节点创建](https://www.gatsbyjs.org/docs/node-interface/)。

有不同类型的 [Gatsby 插件](https://www.gatsbyjs.org/docs/plugins/)，随着用例的出现，可以创建插件来作用于其他插件，以将数据从一种格式(例如 CSV、YAML)转换为 JavaScript 对象。他们通常遵循命名惯例`gatsby-transformer-*`。这些插件被称为 transformer 插件，例如`[gatsby-transformer-yaml](https://github.com/gatsbyjs/gatsby/tree/master/packages/gatsby-transformer-yaml)`。

上面使用了`onCreateNode` API 来扩展或转换其他插件创建的节点。大多数 API 代码都在`gatsby-node.js`文件中实现。下面是 Yaml 如何转换成 JavaScript 对象的代码示例:

```
const jsYaml = require(`js-yaml`)
const _ = require(`lodash`)

async function onCreateNode({
  node,
  actions,
  loadNodeContent,
  createNodeId,
  createContentDigest,
}) {
  function transformObject(obj, id, type) {
    const yamlNode = {
      ...obj,
      id,
      children: [],
      parent: node.id,
      internal: {
        contentDigest: createContentDigest(obj),
        type,
      },
    }
    createNode(yamlNode)
    createParentChildLink({ parent: node, child: yamlNode })
  }

  const { createNode, createParentChildLink } = actions

  if (node.internal.mediaType !== `text/yaml`) {
    return
  }

  const content = await loadNodeContent(node)
  const parsedContent = jsYaml.load(content)

  parsedContent.forEach((obj, i) => {
    transformObject(
      obj,
      obj.id ? obj.id : createNodeId(`${node.id} [${i}] >>> YAML`),
      _.upperFirst(_.camelCase(`${node.name} Yaml`))
    )
  })
}

exports.onCreateNode = onCreateNode
```

要获得更多关于这个 API 如何工作的信息，你可以查看官方的 Gatsby 文档。

### 自定义 Webpack 配置

您的定制 Webpack 配置可以作为插件来支持您的特定用例，也可以贡献给社区。如果您想处理您的捆绑逻辑，可以在`gatsby-node.js`中使用`onCreateWebpackConfig`来完成。

默认情况下，Gatsby 处理配置略有不同的多个 Webpack 构建。当 Gatsby 创建它的 Webpack 配置时，这个函数将被调用，允许您使用 [webpack-merge](https://github.com/survivejs/webpack-merge) 修改默认的 Webpack 配置。

### replaceWebpackConfig

您可以使用`replaceWebpackConfig`来完全控制配置合并逻辑:

```
actions.replaceWebpackConfig = () => {
  return {
    type: `REPLACE_WEBPACK_CONFIG`,
    plugin,
    payload:config,
  }
}
```

如果插件选项被改变或者与`gatsby-config.js`中的原始插件配置有冲突，这可能是危险的并且会破坏 Gatsby。一般来说，这仅在您需要自己处理配置合并逻辑的情况下有用，在这种情况下可以考虑使用`webpack-merge`。

## 结论

在本文中，我们研究了一些 API，它们可以帮助创建插件，并以更容易理解的方式扩展 Gatsby 的功能。我们还看了展示实现的代码示例。我希望看到更多使用这些 API 的实现。快乐编码。😄

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)