# 在 React 应用程序中比较 TypeScript 和 PropTypes

> 原文：<https://blog.logrocket.com/comparing-typescript-and-proptypes-in-react-applications/>

React 组件是返回 React 元素的 JavaScript 函数。这些决定了什么将被添加到 DOM 中。就像函数一样，React 组件可以接收名为 props 的参数，这会导致动态返回元素。

属性可以是任何数据类型，但是组件的预期数据类型可以不同。例如，组件 A 可能需要一个数据类型为`string`的`name`参数，而组件 B 可能需要一个数据类型为`number`的`age`参数。c 可能期望一个具有类似于`title`和`date`属性的`object`数据类型的`post`参数，而 D 可能期望一个具有`Function`数据类型的`onClick`参数。

我们如何指定组件应该期望的数据类型？

有许多方法可以做到这一点，但是本文主要关注 PropTypes 和 TypeScript。尽管目的相似，但这两种方法的工作方式不同。我们将介绍这些工具是什么，理解它们的区别，最后，尝试一些使用它们的高级方法。

要完全理解本文，需要预先了解 PropTypes 和 TypeScript。您还需要安装一个集成开发环境(IDE ),比如 VS Code。

## What is PropTypes?

PropTypes 是 React 应用程序中 props 的运行时类型检查工具。从 React 15.5 开始，PropTypes 实用程序可以通过 [prop-types](https://www.npmjs.com/package/prop-types) 包获得。使用 PropTypes，您可以定义组件中预期的属性类型，比如声明属性是必需的还是可选的。当您将不同的类型传递给一个组件或跳过一个必需的属性时，您将在 JavaScript 控制台中得到一个警告:

```
import React from 'react'
import PropTypes from 'prop-types'

const NotificationCard = ({ message, type, id }) => { 
  return <span>Notification</Notification>
}

NotificationCard.propTypes = {
  message: PropTypes.string.isRequired,
  type: PropTypes.oneOf(["error", "warning", "success"]),
  id: PropTypes.string.isRequired
}

export default NotificationCard

```

安装了 React 插件后， [IntelliSense](https://tailwindcss.com/docs/intellisense) 将在您键入时显示自动完成的道具名称以及道具的预期数据类型。这里有一个例子:

![Intellisense Auto Complete Prop Name](img/db8e5e58ced1137c3174da09bdfd25a0.png)

当我们传递一个意外的数据类型时会发生什么？或者当我们没有提供所需的道具时？

![Ide Display Wrong Prop](img/a3c8895ffc0d5d1c99a9f5a601a7a18b.png)

IDE 没有向我们显示任何错误或警告。现在让我们试着运行代码:

![Browser Console Screenshot Message Prop](img/b810389123521f9cb3e87336e7050015.png)

浏览器控制台截图显示了`message`属性的意外数据类型，因为传递了错误的数据类型。如果您将一个字符串传递给`message`，您将得到另一个错误，表明没有提供必需的道具`id`。

![String Message Error Missing Prop](img/6cf05c7d17aad65e0b9090d9dfcc5578.png)

请注意，PropTypes 在生产应用程序中不提供警告；警告仅在开发过程中打印。如果在使用应用程序的生产版本时收到意外类型，控制台将不会打印任何警告。

## 什么是 TypeScript？

使用带有 React 的 TypeScript 允许你给你的道具添加类型，这些类型将在编译 *时被验证。TypeScript 编译回 JavaScript，因为这是浏览器所理解的。*

 *在 React 应用程序中使用 TypeScript 之前，您可能需要提供必要的依赖项和配置。例如，如果您正在使用 Create React App，您可以按照本文档中的[在您现有的应用程序中添加类型脚本支持。](https://create-react-app.dev/docs/adding-typescript/)

下面是上面 PropTypes 定义的 TypeScript 解释:

```
import React from 'react'
import PropTypes from 'prop-types'

type Props = {
  message: string;
  type: "error" | "warning" | "success";
  id: string;
}

const NotificationCard = ({ message, type, id }: Props) => { 
  return <span>Notification</span>
}

export default NotificationCard

```

使用用于 TypeScript 的 IDE 工具，当您传递意外的属性时，您将在 IDE 中获得即时的 IntelliSense 警告。让我们通过提供错误的数据类型来测试这一点；我们会收到一条警告消息:

![IDE Wrong Data Message](img/aea5a6bef059c2d18e1c8006bb180684.png)

当我们为`message`提供正确的数据类型时，我们将收到以下通知:

![Message Correct Data Type Notification](img/fab4a2dff65a5c44a718824944c78443.png)

如上所述，IntelliSense 还提供了有关错误的更多信息来帮助您解决问题。

使用 TypeScript，当违反 props 要求时，运行`npm run build`将会失败:

![Npm Run Build Fail Props Requirement Violation](img/eda3c7054fec80b44f49990f85fbfb34.png)

## 比较 TypeScript 和 PropTypes

这两个工具都用于检查道具的类型。虽然 TypeScript 现在看起来可能是最好的选择，但是选择一个将不可避免地导致取舍。以下是一些需要考虑的关键差异:

### 1.运行时和编译时类型检查

当应用程序在浏览器中运行时，PropTypes 会在运行时进行类型检查。但是，当 TypeScript 代码被编译为 JavaScript 时，TypeScript 会在编译时进行类型检查。这一点值得注意，因为它会影响工具的使用方式。以下是一些例子:

#### 来自 API 的数据:

TypeScript 无法对来自 API 的数据进行类型检查。这种代码的编译不会成功，因为数据的内容只能在运行时知道。相反，如果违反了预期的类型，PropTypes 将给出警告。

可以将 PropTypes 看作是一种工具，它做的事情类似于(不完全是):

```
...
if (typeof age !== number) {
  console.warn("Age should have a number data type")
}
...

```

不管`age`是硬编码的还是从 API 获得的，类型检查器仍然运行。另一方面，TypeScript 不能到达浏览器，因此限制了对硬编码数据的类型检查。

#### 构建组件库

如果您正在创建一个组件库，您很可能会将生产代码发布给一个包管理器。在编译时，TypeScript 将被转换为 JavaScript。这意味着库的用户不能依赖于 props 的 TypeScript 类型定义，重申了 PropTypes 的相关性。PropTypes 是普通的 JavaScript 代码，这意味着在使用库时也可以进行验证。

### 2.语法和语义突出显示

这两个工具都有插件，提供关于组件属性信息的自动补全。但是，TypeScript 的突出显示工具优于 PropTypes。您会在诸如 VS Code、WebStorm 和 Atom 之类的 TypeScript IDE 工具中发现许多特性。当没有提供预期的 props 数据类型时，TypeScript 会适当地突出显示您的组件，并提供对解决方案的见解。

### 3.TypeScript 中的高级功能

有许多方法可以在 TypeScript 中为您的 props 指定类型，而 PropTypes 可能无法提供这些方法。例如，TypeScript 允许您将接口与类型结合起来，并执行条件类型检查，比如声明如果属性 A 为 true，则还必须提供属性 C。

下面是 TypeScript 的一个高级用法示例:

```
import React from "react";
import PropTypes from "prop-types";
type Props =
    | {
            type: "error";
            message: "";
      }
    | {
            type: "success";
      };
const NotificationCard = (props: Props) => {
    return <span>Notification</span>;
};
export default NotificationCard;

```

在上面的代码片段中，如果`type`是`error`，那么还需要一个`message`属性。但是如果是`success`，那么我们就不需要指定另一个属性了。

下面是当我们有一个`error`的`type`而没有提供`message`属性时得到的警告:

![Type Error Message Property](img/6bf597c49cd514e92706927881684dda.png)

注意，第一个没有`message`属性的`NotificationCard`提供了警告，但是第二个`NotificationCard`用法没有。

## 享受两个世界的精华

如前所述，在类型脚本和 PropTypes 之间做出选择需要权衡。是否希望通过使用 PropTypes 保留 TypeScript 的功能，如语法突出显示？还是宁愿放弃运行时的类型检查？

您的选择将受到应用程序使用方式的影响。例如，如果您将应用程序构建为一个将被许多人使用的库，那么运行时类型检查就非常重要。

然而，您可能不需要牺牲任何特性——有很多方法可以享受运行时和编译时类型检查。

首先，您可以选择为您的应用程序编写类型定义和 PropTypes 定义。从长远来看，当您必须为应用程序中的每个组件编写这两个定义时，这将是一项艰巨的任务。让我们回顾两种方法，您可以用它们来编写一个并自动生成另一个。

### 1.`InferProps`

来自`[@types/prop-types](https://www.npmjs.com/package/@types/prop-types)`的```InferPropTypes`可用于从 PropTypes 定义创建类型定义。这里有一个例子:``

 `````
import React from "react";
import PropTypes, { InferProps } from "prop-types";

const BlogCardPropTypes = {
    title: PropTypes.string.isRequired,
    createdAt: PropTypes.instanceOf(Date),
    authorName: PropTypes.string.isRequired,
};

type BlogCardTypes = InferProps<typeof BlogCardPropTypes>;
const BlogCard = ({ authorName, createdAt, title }: BlogCardTypes) => {
    return <span>Blog Card</span>;
};

BlogCard.propTypes = BlogCardPropTypes;

export default BlogCard;

```

当我们突出显示组件时，我们会看到以下内容:

![Infer Props Highlight Component](img/3aed2daf758b117e79b1edd3e217a59e.png)

该组件现在有一个 PropTypes 定义，并且在违反 TypeScript 类型时也会给出错误。正如所见，它显示的是`Property 'authorName' is missing`，但它没有显示`createdAt`丢失，因为我们没有在`BlogCardPropTypes`对象中为该属性添加一个`isRequired`。

### 2.`babel-plugin-typescript-to-proptypes`

您可以使用`[babel-plugin-typescript-to-proptypes](https://www.npmjs.com/package/babel-plugin-typescript-to-proptypes)`从 TypeScript 类型定义中生成 PropTypes。当您为您的 props 指定类型时，插件会将这些类型定义转换为 PropTypes 定义。

例如，下面的代码:

```
import React from "react";
type Props = {
    title: string;
    createdAt: Date;
    authorName: string;
};
const BlogCard = ({ title, createdAt, authorName }: Props) => {
    return <span>Blog card</span>;
};
export default BlogCard;

```

会像这样写一样工作:

```
import React from 'react'
import PropTypes from 'prop-types'

const BlogCard = ({ title, createdAt, authorName }) => {
    return <span>Blog card</span>;
};

BlogCard.propTypes = {
  title: PropTypes.string.isRequired,
  createdAt: PropTypes.instanceOf(Date),
  authorName: PropTypes.string.isRequired,
}

export default BlogCard

```

它将被相应地编译，从而赋予 React 应用程序在运行时进行类型检查的能力。

## 结论

围绕 PropTypes 和 TypeScript 有许多误解。有人会说一个重要，另一个不重要。对我来说，这两者都很重要，但是如果我不构建一个将被其他人使用的工具，我会更优先考虑 TypeScript。

在本文中，我们已经了解了什么是 PropTypes 和 TypeScript，它们在 React 应用程序中的相关性，它们的区别，以及它们可以一起使用的方式。

要继续阅读，请查看关于 [React PropTypes](https://blog.logrocket.com/validating-react-component-props-with-prop-types-ef14b29963fc/) 的详尽指南。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.``*