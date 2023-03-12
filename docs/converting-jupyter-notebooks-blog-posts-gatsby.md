# 使用 Gatsby 将 Jupyter 笔记本转换为博客文章

> 原文：<https://blog.logrocket.com/converting-jupyter-notebooks-blog-posts-gatsby/>

熟悉数据科学的每个人都知道 Jupyter 笔记本是一条出路。它们很容易让你把 Markdown 和实际代码混合起来，为研究和学习创造一个生动的环境。代码变得用户友好且格式良好——编写代码并随时生成动态图表、表格和图像。

写笔记本是如此之好，以至于很自然地想象你可能想要在互联网上分享它们。当然，你可以把它放在 GitHub 甚至 Google Colab 中，但是这需要一个运行的内核，而且它肯定没有一个好的网页友好。

在我们进一步讨论之前，理解 Jupyter 笔记本只不过是包含输入、输出和大量元数据的 JSON 对象的集合，这一点很重要。然后，它构建输出，并可以很容易地[转换成不同的格式](https://github.com/jupyter/nbconvert/)(比如 HTML)。

知道笔记本可以变成 HTML 文档是我们所需要的——剩下的就是找到一种方法来自动化这个过程，这样一个`.ipynb`文件就可以变成互联网上的一个静态页面。我对这个问题的解决方案是使用 GatsbyJS——值得注意的是，它是最好的静态站点生成器之一，如果不是唯一最好的。

Gatsby 可以很容易地从不同的格式——JSON，Markdown，YAML，你能想到的——获取数据，并静态地生成你可以在万维网上托管的网页。最后一部分变成了:不是将 Markdown 转换成 post，而是用一个`.ipynb`文件做同样的事情。这篇文章的目标是带你完成这个过程。

## 技术挑战

在网上快速搜索一下，你就会看到[盖茨比-变形金刚-ipynb](https://www.gatsbyjs.com/plugins/@gatsby-contrib/gatsby-transformer-ipynb/) 。基本上，这是一个 Gatsby 插件，它能够以一种我们稍后可以在 GraphQL 查询中访问的方式解析笔记本文件。这简直好得令人难以置信！

事实上，的确如此。这项艰苦的工作是由国际公司的优秀员工完成的。然而，这个插件已经有一段时间没有被维护了，而且事情并不是简单的开箱即用——更不用说缺乏人们期望从一个插件中得到的定制。

我就不说这些无聊的东西了，但是在忙完 GitHub 的阴暗角落之后，在[这篇文章](https://specific.solutions.limited/blog/rusty-jupyter/) by Specific Solutions 的大力帮助下，我设法[创建了我自己的《盖茨比-变形金刚-ipynb》的分支](https://www.npmjs.com/package/@rafaelquintanilha/gatsby-transformer-ipynb)，它解决了我的问题，也满足了这篇文章的目的。

但是，请注意，我无意成为一名积极的维护者，我所做的大部分工作仅仅是为了得到我需要的东西——使用它需要您自担风险！

前言说得够多了，让我们开始写代码吧。

## 创建项目

首先，我们将要构建的源代码可以在 GitHub 上找到[。我们将从创建一个 Gatsby 项目开始。确保您已经安装了](https://github.com/rafaelquintanilha/jupyter-blog)[Gatsby](https://www.gatsbyjs.com/docs/quick-start/)，并通过运行以下命令创建一个新项目:

```
gatsby new jupyter-blog
cd jupyter-blog
```

运行`gatsby develop`并转到`[http://localhost:8000/](http://localhost:8000/)`以确保一切正常。

## 创建您的第一个笔记本

由于 Jupyter 笔记本将成为我们全新博客的数据源，我们需要开始添加内容。在您的项目文件夹中，转到`src`并创建一个`notebooks`文件夹。我们将确保稍后从该文件夹中读取。

是时候创建我们的第一个笔记本了。出于本教程的目的，我将使用这个简单的笔记本作为基础。你可以在 GitHub 中看到动态输出，但是你可以随意使用。

在任何情况下，值得一提的是，一些丰富的输出，如由 [Plotly](https://plotly.com/) 生成的动态图表可能需要额外的关注——如果您希望我在以后的帖子中涉及这些，请告诉我！然而，为了使这篇文章简短，我们将只处理静态图像、表格和降价。

现在您已经有了一个包含数据的 Gatsby 项目，下一步是使用 GraphQL 查询它。

## 查询数据

Gatsby 的最大优势之一是获取数据时的灵活性。事实上，您想要的任何东西都可以成为用于生成静态内容的数据源。

如上所述，我们将使用我自己版本的变压器。继续安装它:

```
yarn add @rafaelquintanilha/gatsby-transformer-ipynb
```

下一步是配置插件。在`gatsby-config.js`中，将以下内容添加到您的`plugins`数组中(如果有疑问，您可以随时[检查 GitHub](https://github.com/rafaelquintanilha/jupyter-blog/blob/master/gatsby-config.js) ):

```
...
{
  resolve: `gatsby-source-filesystem`,
  options: {
    name: `notebooks`,
    path: `${__dirname}/src/notebooks`,
    ignore: [`**/.ipynb_checkpoints`],
  },
},
{
  resolve: `@rafaelquintanilha/gatsby-transformer-ipynb`,
  options: {
    notebookProps: {
      displayOrder: ["image/png", "text/html", "text/plain"],
      showPrompt: false,
    },
  },
},
...
```

我们来分解一下。

首先，我们在数组中添加一个`gatsby-source-filesystem`选项。我们告诉盖茨比去`src/notebooks`找文件，我们的`.ipynb`文件就在那里。接下来，我们将配置变压器并设置一些道具:

*   `displayOrder`–我们正在显示的输出的 MIME 类型
*   `showPrompt`–是否显示提示

虽然提示在笔记本上有意义，但在静态页面上，它们就失去了意义。就此而言，我们将隐藏它们，以便有清晰的内容。

是时候检查一切是否按计划进行了。转到`[http://localhost:8000/___graphql](http://localhost:8000/___graphql)`打开 GraphiQL 并运行以下查询:

```
query MyQuery {
  allJupyterNotebook {
    nodes {
      html
    }
  }
}
```

成功！请注意我们笔记本的 HTML 是如何生成的。剩下的就是将这个 HTML 注入到一个 React 组件中，我们的过程就完成了。

## 自动生成帖子

最坏的情况已经过去了。下一步是在`gatsby-node.js`中查询这些数据，这样我们就可以在`src/notebooks`中为每个笔记本生成静态页面。

但是，请注意，我们需要向笔记本添加额外的元数据，例如作者和文章标题。有几种方法可以做到这一点，最简单的可能是利用`.ipynb`文件是 JSON 并使用它们自己的`metadata`字段。打开`.ipynb`，添加你需要的信息:

```
{
 "metadata": {
  "author": "Rafael Quintanilha",
  "title": "My First Jupyter Post",
  "language_info": {
   "codemirror_mode": {
    "name": "ipython",
    "version": 3
   },
   "file_extension": ".py",
   "mimetype": "text/x-python",
   "name": "python",
   "nbconvert_exporter": "python",
   "pygments_lexer": "ipython3",
   "version": "3.7.4-final"
  },
  "orig_nbformat": 2,
  "kernelspec": {
   "name": "python3",
   "display_name": "Python 3"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 2,
 "cells": [
  ...
 ]
}
```

> **Pro 提示**:如果你使用的是 VS 代码，打开文件可能会启动 Jupyter 内核。您可以在配置中禁用它来编辑原始内容，但我通常只是用另一个编辑器(如 gedit 或 Notepad++)打开文件。

现在这个过程对于任何带有 Gatsby 的数据源都是完全一样的。我们将查询`gatsby-node.js`中的数据，并将相关信息传递给一个帖子模板，该模板又将成为我们领域中的一个独特页面。

然而，在此之前，打开`gatsby-node.js`并添加以下内容:

```
exports.onCreateNode = ({ node, actions }) => {
  const { createNodeField } = actions
  if (node.internal.type === "JupyterNotebook") {
    createNodeField({
      name: "slug",
      node,
      value: node.json.metadata.title
        .split(" ")
        .map(token => token.toLowerCase())
        .join("-"),
    })
  }
}
```

对于 GraphQL 中创建的每个节点，上面的摘录将检查那些属于 Jupyter 笔记本的节点，并用一个新字段`slug`扩展它们。我们在这里使用一种简单的方法，但是你可以使用一个健壮的库，比如 [slugify](https://www.npmjs.com/package/slugify) 。新字段将被查询并用于生成发布路径。在同一文件中，添加以下内容:

```
const path = require(`path`);
exports.createPages = async ({ graphql, actions: { createPage } }) => {
  const blogPostTemplate = path.resolve(`src/templates/BlogPost.js`);
  const results = await graphql(
    `
      {
        allJupyterNotebook() {
          nodes {
            fields {
              slug
            }
          }
        }
      }
    `
  );
  const posts = results.data.allJupyterNotebook.nodes;
  posts.forEach((post) => {
    createPage({
      path: post.fields.slug,
      component: blogPostTemplate,
      context: {
        slug: post.fields.slug,
      },
    });
  });
};
```

这个基本上是通过 slug 查询数据，并发送给`BlogPost.js`。让我们现在创建它:

```
import React from "react"
import { graphql } from "gatsby"
import SEO from "../components/seo"

const BlogPost = ({
  data: {
    jupyterNotebook: {
      json: { metadata },
      html,
    },
  },
}) => {
  return (
    <div>
      <SEO title={metadata.title} />
      <h1>{metadata.title}</h1>
      <p>Written by {metadata.author}</p>
      <div dangerouslySetInnerHTML={{ __html: html }} />
    </div>
  )
}
export default BlogPost
export const query = graphql`
  query BlogPostBySlug($slug: String!) {
    jupyterNotebook(fields: { slug: { eq: $slug } }) {
      json {
        metadata {
          title
          author
        }
      }
      html
    }
  }
`
```

就是这样！跳到`[http://localhost:8000/my-first-jupyter-post](http://localhost:8000/my-first-jupyter-post)`,把你的笔记本看成一个静态的 HTML 页面。

## 丰富

正如你所看到的，在风格和设计方面有很多可以改进的地方。这超出了本文的范围，但是作为一个提示，您可以使用 CSS 模块来增强布局，并删除不必要的 *stdout* (博客文章中您不关心的文本输出)。创建`BlogPost.module.css`并添加以下内容:

```
.content {
  max-width: 900px;
  margin-left: auto;
  margin-right: auto;
  padding: 40px 20px;
}
.content :global(.nteract-display-area-stdout),
.content :global(.nteract-outputs > .cell_display > pre) {
  display: none;
}
.content :global(.nteract-outputs > .cell_display > img) {
  display: block;
}
.content :global(.input-container) {
  margin-bottom: 20px;
}
.content :global(.input-container pre.input) {
  border-radius: 10px !important;
  padding: 1em !important;
}
.content :global(.input-container code) {
  line-height: 1.5 !important;
  font-size: 0.85rem !important;
}
.content :global(.input-container code:empty) {
  display: none;
}
@media only screen and (max-width: 940px) {
  .content {
    max-width: 100%;
    padding-left: 20px;
    padding-right: 20px;
    box-sizing: border-box;
  }
}
```

现在回到`BlogPost.js`并将该类添加到我们的 div 中:

```
...
import css from "./BlogPost.module.css"
...
return (
  <div className={css['content']}>
     ...
  </div>
);
```

注意现在看起来干净多了。最终结果(稍作调整)存放在 [Netlify](https://jupyter-blog.netlify.app/) 中。所有的修改都在[源代码](https://github.com/rafaelquintanilha/jupyter-blog)中。

## 最后的想法

将 Jupyter 笔记本转换成 HTML 页面并不复杂，但是需要很多小步骤和调整。希望这篇文章是如何开始的指南。

有大量的变化和改进可以做，比如支持丰富的输出(比如动态图表)，改善移动体验，更好的元数据管理，等等。

笔记本功能多样，使用起来很有趣，自动将它们转换成网页是它们非常好的功能。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)