# Ruck vs. Aleph.js 在 Deno 中构建 React 应用

> 原文：<https://blog.logrocket.com/ruck-aleph-building-react-apps-deno/>

在现代时代建立一个前端是艰难的，因为你必须做出过多的选择。开发人员通常会使用流行的框架，比如 React，然后发现自己需要更多的工具来完成工作。

这些工具可能包括捆扎机、试运转机和棉绒机。不仅如此，他们还需要考虑 SEO、样式资产、路由、数据获取等等。开发人员在创建生产就绪、高性能的 React 应用程序时，应该评估所有这些因素。

像 [create-react-app](https://create-react-app.dev/) 和 [Next.js](https://nextjs.org/) 这样的项目已经因为提供了那些靠自己实现起来很乏味的功能而广受欢迎。 [Deno](https://deno.land) 是一个新的 JavaScript 运行时，正在获得社区的支持。Deno 通过支持 [ES 模块](https://flaviocopes.com/es-modules/)、[导入映射](https://github.com/WICG/import-maps)和[获取 API](https://deno.land/manual@v1.26.0/runtime/web_platform_apis#fetch-api) 与已建立的 web 标准保持一致。

现在的 React 框架大多只支持在服务器上使用 Node.js 进行渲染，但是[一些新的框架确实支持 Deno](https://blog.logrocket.com/using-ultra-new-react-web-framework/) 。Deno 为 Node.js 框架必须自己提供的功能提供了许多内置工具。

Deno 支持 ES 模块和 TypeScript，因此这些较新的框架可以避免构建步骤，如 transpilation。Deno 还有一个大型的标准库、用于常见任务(如林挺、格式化和测试)的开发工具，以及一个包管理器。

一些开发人员对 Deno 持谨慎态度，因为它不支持 npm，也不兼容所有 Node.js 第三方包。根据我的经验，有很多方法可以解决这些限制。

[Ruck](https://ruck.tech) 和 [Aleph.js](https://alephjs.org) 是 Deno-native React web 框架；两者都支持服务器端呈现、数据获取、路由和修改 HTTP 服务器响应等特性。

在本文中，我们将讨论 Ruck 和 Aleph.js 之间的主要相似之处和不同之处，在选择使用哪种框架时，这些相似之处和不同之处是很重要的。

*向前跳转:*

## Ruck 概述

Ruck 是用 Deno 构建 React 应用程序的最小框架。它倾向于 Deno 特有的特性，如 es 模块和导入映射，这使它成为新运行时的绝佳展示平台。

然而，Ruck 不使用捆绑器，所以它不支持用 JSX 编写 React 组件，所有配置都是在代码中定义的。到处使用`createElement`并不是最好的开发者体验！

我可以看到另一个框架采用 Ruck 来解决这些问题。如果你想控制正在发生的一切，并且不喜欢大多数框架的“魔力”，Ruck 就是你要找的东西。

下面是一个用 Ruck 编写的示例组件:

```
import { createElement as h } from "react";
import useOnClickRouteLink from "ruck/useOnClickRouteLink.mjs";
import useRoute from "ruck/useRoute.mjs";

export const css = new Set([
  "/components/ExampleComponent.css",
]);

export default function ExampleComponent({ href, children }) {
  return createElement("a", 
                { className: "NavLink__a", href, onClick: () => console.log('Hello World!') },
                children
        );
}

```

## Aleph.js 概述

Aleph.js 是一个使用 Deno 构建 React 应用的全栈 web 框架。它是仅次于 [Fresh](https://fresh.deno.dev) ，作为[最流行的 Deno-native React 框架](https://yoshixmk.github.io/deno-x-ranking/)。它倾向于 Deno 的一些特性，但也提供了更多。

Aleph.js 的灵感来自 Next.js 它甚至为一些特性提供了相同的语法。Aleph.js 支持服务器端呈现和静态站点生成，创建独立的 API、基于文件的路由和热模块重载。为了支持独立的文件类型，如 JSX 和 CSS，Aleph.js 使用 [esbuild](https://esbuild.github.io) 而不是 webpack。

下面是一个用 Aleph.js 编写的示例组件:

```
import React from 'react';
import Logo from '../components/logo.tsx

export default function ExampleComponent() {
  return (
    <div>
      <Logo />
      <h1>Hello World!</h1>
    </div>
  )
}

```

## Ruck 和 Aleph.js 之间的相似性

Ruck 和 Aleph.js 有很多相似之处，比如内置对[导入地图](https://github.com/WICG/import-maps#the-basic-idea)的支持。如果不使用 npm 或另一个包管理器，Deno 依赖于 [HTTP imports](https://deno.land/manual/linking_to_external_code) 。这意味着导入通常是这样的:

```
import React from "<https://esm.sh/stable/[email protected]/es2021/react.js”>;

```

Deno 建议将所有模块导入放到一个单独的`deps.ts`文件中，以便重新导出。这种方法的问题在于，进口产品仍与 Node.js/webpack 同类产品不兼容。

处理这个问题的一个更好的方法(与浏览器兼容的方法)是使用导入映射。导入映射是浏览器的一项最新功能，它让浏览器知道模块的依赖项位于何处。

下面是一个导入映射的示例:

```
{
  "imports": {
    "react": "<https://esm.sh/stable/[email protected]/es2021/react.js>",
  }
}

```

下面是一个使用导入映射的组件:

```
import React from "react";

export default function ExampleComponent() {
  return <div />;
}

```

要在 Aleph.js 中使用导入映射，我们需要在根目录中定义一个名为`import_map.json`的文件。在 Ruck 中使用导入地图也很简单；我们定义文件并在运行时将其传递给 Deno，如下所示:

```
deno run \\
    --allow-env \\
    --allow-net \\
    --allow-read \\
    --import-map=importMap.json \\
    scripts/ruck-serve.mjs

```

导入地图的问题是浏览器支持仍然很差；Safari 和 Firefox 都不提供内置支持。好消息是 Ruck 使用了一个 shim 来为老版本的浏览器提供支持。

Ruck 和 Aleph.js 之间的另一个相似之处是它们专注于服务器端呈现 React 组件。与客户端渲染相比，SSR 可以提供增强的性能、SEO 和其他好处。

如果 React 组件依赖于获取的数据，选择在服务器上这样做意味着组件可以在向客户端发送数据之前进行呈现。这意味着无需向用户显示加载状态，并且性能通常会更好。

Ruck 在组件级支持服务器上的数据获取，而其他框架通常只在页面级支持。Aleph.js 允许您通过在页面组件文件中定义一个 [SSR 函数](https://alephjs.vercel.app/docs/basic-features/ssr-and-ssg)来实现这一点。Aleph.js 还支持在组件中使用一个特殊的钩子`useDeno`。

下面的例子展示了在 Aleph.js 中使用`useDeno`在服务器端获取数据:

```
import React from 'react'
import { useDeno, useRouter } from 'aleph'

export default function Post() {
  const { params } = useRouter()
  const post = useDeno(async () => {
    return await (await fetch(`https://.../post/${params.id}`)).json()
  })

  return (
    <h1>{post.title}</h1>
  )
}

```

当使用 CSS 设计 React 应用时，Ruck 和 Aleph.js 都支持组件级 CSS 导入。这允许在浏览器请求时发送 CSS，比如在组件呈现时。

Ruck 通过一个名为`css`的导出组件变量来实现这一点。你可以用 Aleph.js 以多种方式在[中实现相同的行为，但是推荐的方法是使用](https://alephjs.vercel.app/docs/basic-features/built-in-css-support) [CSS 模块](https://github.com/css-modules/css-modules)。

下面的例子展示了 Ruck 中`css`函数的用法:

```
import React from 'react'
import Heading, { css as cssHeading } from "./Heading.mjs";
import Para, { CSS as CSS paragraph } from "./Para.mjs";

export const css = new Set([
  ...cssHeading,
  ...cssParagraph,
  "/components/ExampleComponent.css",
]);

export default function ExampleComponent() {
   ...
}

```

这里有一个例子演示了 Aleph.js 中的`css`模块的使用:

```
import React from 'react'
import styles from './exampleComponent.module.css'

export default function ExampleComponent() {
  return (
    <>
      <h1 className={styles.title}>Hi :)</h1>
    </>
  )
}

```

作为服务器端呈现应用程序的一个好处是在呈现生命周期中可以访问 HTTP 请求。如果您需要访问标头或更改响应，这可能会很有帮助。

使用 Ruck，HTTP 响应在 React 上下文中可用，`TransferContext`。在 Aleph.js 中我们可以使用`SSR`函数。

下面是一个在 Ruck 中修改 HTTP 响应的例子:

```
import React from 'react';
import TransferContext from "ruck/TransferContext.mjs";

export default function PageError({ errorStatusCode, title, description }) {
  const ruckTransfer = useContext(TransferContext);

  if (ruckTransfer) ruckTransfer.responseInit.status = errorStatusCode;

  ...
}

```

下面是一个在 Aleph.js 中修改 HTTP 响应的例子:

```
import React from 'react';
import { useDeno } from 'aleph';

export default function ExampleComponent() {
  const isLoggedIn = useDeno(req => {
    return req.headers.get('Auth') === 'XXX'
  }, { revalidate: true })

  return (
    <p>isLoggedIn: {isLoggedIn}</p>
  )
}

```

## Ruck 和 Aleph.js 的区别

Ruck 和 Aleph.js 框架之间有显著的差异，特别是在受欢迎程度和开发人员体验方面。因为 Ruck 是新的，它缺乏像 Aleph.js 这样的成熟框架所拥有的社区支持。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

Aleph.js 有 4.7k 颗 GitHub 星，而 Ruck 有 94 颗。当然，GitHub star count 并不总是框架功能的最佳衡量标准，但它能让您很好地理解开发人员的意图。

Ruck 更喜欢配置而不是惯例。它迎合了那些喜欢对他们的应用程序如何运行进行高度控制的开发人员。例如，使用 Ruck，您必须完全自己定义 web 应用程序路由器如何工作，而 Aleph.js 会为您处理大部分工作。例如，你可以在 [Ruck 库的自述文件](https://github.com/jaydenseric/ruck#examples)中看到路由器的例子。

Aleph.js 可以零配置运行，并提供项目模板帮助开发人员入门。您可以选择加入基于配置的功能。使用 Ruck，您必须花时间自己设置应用程序的基础。

如果您的 web 应用程序拥有构建时所需的所有数据，静态网站是理想的。这可以简化部署，因为不需要运行 Deno 服务器。将 HTML、CSS 和 JavaScript 的构建文件夹放置到一个部署目标，如 GitHub Pages 或 Cloudflare。

[Aleph.js 支持静态站点生成](https://alephjs.vercel.app/docs/basic-features/ssr-and-ssg)，这对于这些情况很有帮助，而 Ruck 不支持。像 Next.js 中的`getStaticPaths`一样，您可以在组件文件内的`ssr`函数中定义路径的键，以指定该路由可以处理的路径:

```
import type { SSROptions } from 'aleph/types';

export const ssr: SSROptions = {
  paths: async () => {
    const posts = await (await fetch('https://.../api/posts')).json()
    return posts.map(({ id }) => `/post/${id}`)
  }
}

```

接下来，运行`aleph build`；就这么简单！

## 最后的想法

随着 Deno 越来越受欢迎，Ruck 和 Aleph.js 都应该被视为在 Deno 中构建 react 应用程序的可行选项。这些基于 Deno 的 React web 框架迎合了两组不同的开发人员。由于 Ruck 是一个新人，它没有 Aleph.js 那样的水平，但它提供了更多的控制。

Aleph.js 提供了很好的开发者体验，不需要任何配置，而且有很多强大的特性。这些最小的框架提供了许多内置的现代浏览器功能，这可以导致最小和精简的技术堆栈。这与当今前端生态系统的复杂性形成了鲜明对比。

Deno 的大量内置特性减少了第三方工具的工作量。React 框架可以专注于开发创新和有趣的新功能，而开发人员可以放心地知道他们为自己的 web 应用程序技术堆栈做出了一个伟大的选择。

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