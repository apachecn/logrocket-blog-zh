# 如何在 Next.js 中实现重定向

> 原文：<https://blog.logrocket.com/redirects-next-js/>

搜索引擎和用户都不希望找到不存在或不完整的页面。作为一个开发者，你也应该避免它，因为它会减少重复访问你的站点的次数，并影响它被搜索引擎索引的方式。

Next.js 是一个流行的框架，它构建在 React 库之上，并提供了许多有用的内置特性，其中之一就是处理重定向以避免这种情况。

在本文中，我们将设置一个应用程序，并仔细研究在 Next.js 项目中实现重定向的不同方法。我还将提供配置文件和路由的代码片段，并解释它们如何在 Next.js 中工作。

让我们开始吧。

## 什么是重定向？

重定向使用户能够将已执行的 URL 转移到新的 URL，或者换句话说，将传入的请求从一个路径重新路由到另一个路径。

这些通常由服务器通过 HTTP 重定向状态码(3xx)来处理，也可以被网络爬虫理解。

重定向常用于以下情况:站点的某个部分不存在或正在建设中，内容已被移动到不同的 URL，路由系统已更改，用户由于访问限制而被重定向，或许多其他情况。

## 设置 Next.js 项目

我们将使用`create-next-app`，这是 Next.js 官方支持的一种方式来设置开发服务器，我们将使用它来测试我们的重定向示例。

首先，打开您的终端并运行命令`npx create-next-app test-app`。这将创建一个新的项目文件夹，应用程序的所有逻辑都将存在其中。

接下来，通过运行`cd test-app`将工作目录更改为新创建的文件夹，然后运行`npm run dev`来启动开发服务器。

然后，打开浏览器，导航到`[https://localhost:3000](https://localhost:3000)`查看应用的实时预览。

## 定义的路线

在 Next.js 中创建重定向的最常见方式是使用`next.config.js`文件，该文件应该位于产品结构的根级别。如果不是，请创建一个并包含以下代码:

```
module.exports = {
  async redirects() {
    return [
      {
        source: '/',
        destination: '/welcome',
        permanent: true,
      },
    ]
  },
}

```

在上面的代码片段中，`source`属性是请求的路由，`destination`是我们希望将用户重定向到的路由，`permanent`控制我们是否希望为客户机和搜索引擎缓存重定向路由。

让我们为在配置中使用的`/welcome`创建一个新的路由。在`pages`文件夹的根级别，创建一个新文件`welcome.js`，并包含以下代码:

```
export default function Welcome() {
  return <h1>Welcome page</h1>;
}

```

现在，通过按键盘上的 **Ctrl+C** 重启开发服务器，然后运行；`npm run dev`重新开始。这对于我们在`next.config.js`中所做的更改生效是必要的。记住，对于本文中的其他示例，也要这样做。

要测试重定向，请打开浏览器并再次导航至`[https://localhost:3000](https://localhost:3000)`。您现在应该会被自动重定向到`[https://localhost:3000/welcome](https://localhost:3000/welcome)`。

## 废料浆匹配

js 支持访问 URL 的 slugs 并为它们配置重定向。对于这个例子，让我们将`next.config.js`编辑成这样:

```
module.exports = {
  async redirects() {
    return [
      {
        source: '/draft/:slug',
        destination: '/blog/:slug',
        permanent: true,
      },
    ]
  },
}

```

要设置路线，进入`pages`文件夹并创建两个名为`draft`和`blog`的新文件夹，然后在这两个文件夹中创建文件`article.js`。

在`draft/article.js`文件中，包含以下代码:

```
export default function Article() {
  return <h1>Source route</h1>;
}

```

在`blog/article.js`中，包括以下代码:

```
export default function Article() {
  return <h1>Destination route</h1>;
}

```

重启 dev 服务器后，尝试访问`[https://localhost:3000/draft/article](https://localhost:3000/draft/article)`，你将被重定向到`[https://localhost:3000/blog/article](https://localhost:3000/blog/article)`。该 slug 可以是 URL 中任何受支持的值，除非您为它创建了一个路由，并且没有在多个级别上嵌套它。

## 通配符

要重定向嵌套路由，您可以使用通配符，这实际上将采用最后一个已知级别之后的所有路径，并重定向到新路由。这很简单，只需在 URL 中的目标位置添加一个`*`字符。

切换回`next.config.js`并将其更改为:

```
module.exports = {
  async redirects() {
    return [
      {
        source: '/draft/:slug*',
        destination: '/blog/:slug*',
        permanent: true,
      },
    ]
  },
}

```

为了创建嵌套路线，我们必须在`draft`和`blog`文件夹中创建几个子文件夹。让我们假设我们想通过技术对文章进行分类，所以我们将两个文件夹都称为`react`。在两个新创建的文件夹中，添加文件`tutorial.js`。

在`draft/react/tutorial.js`中，包含以下代码:

```
export default function Tutorial() {
  return <h1>Nested source route</h1>;
}

```

在`blog/react/tutorial.js`中，包含以下代码:

```
export default function Tutorial() {
  return <h1>Nested destination route</h1>;
}

```

现在，重启开发服务器并访问`[https://localhost:3000/draft/react/tutorial](https://localhost:3000/draft/react/tutorial)`。您应该会立即被重定向到`[https://localhost:3000/blog/react/tutorial](https://localhost:3000/blog/react/tutorial)`。请注意，整个嵌套路径被重定向。

## 正则表达式查询

Regex 是一个强大的工具，可以用来更有效地访问 URL 路径的不同部分。您将对重定向行为有更多的控制权，并被允许为重定向创建自定义规则。

将`next.config.js`更改为以下代码:

```
module.exports = {
  async redirects() {
    return [
      {
        source: '/draft/:slug(^[a-z]+)',
        destination: '/blog/article',
        permanent: false,
      },
    ]
  },
}

```

在上面的代码片段中，我们只配置了由`a`到`z`字符组成的路由，这些路由被重定向到我们之前创建的`/blog/article`路由。

导航到项目结构中的`draft`文件夹，创建一个新文件`article123.js`，其中包含以下代码:

```
export default function Article123() {
  return <h1>Source route</h1>;
}

```

为了测试 regex 查询，重启 dev 服务器并尝试访问`[https://localhost:3000/draft/article](https://localhost:3000/draft/article)`。您将被重定向到`[https://localhost:3000/blog/article](https://localhost:3000/blog/article)`，因为该路线仅由字母组成。

现在尝试访问`[https://localhost:3000/draft/article123](https://localhost:3000/draft/article123)`。您将看到您输入的 URL 的内容，并且不会被重定向，因为路由包含数字。

这里有几个有用的网站可以帮助你编写正则表达式查询: [regex101](https://regex101.com/) 和 [regexr](https://regexr.com/) 。

## 基本路径支持

Next.js 还支持 URL 中基本路径的前缀。如果您必须设置多个重定向，并且不想为所有路由重复编写基本路径，这可能很有用。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

将`next.config.js`更改为以下代码:

```
module.exports = {
  basePath: '/content',

  async redirects() {
    return [
      {
        source: '/draft/article',
        destination: '/blog/article',
        permanent: true,
      },
      {
        source: '/draft/react/tutorial',
        destination: '/blog/react/tutorial',
        basePath: false,
        permanent: true,
      },
    ]
  },
}

```

在第一个重定向对象中，源变成了`/content/draft/article`，目的地变成了`/content/blog/article`，而在第二个重定向对象中，基本路径被忽略，因为我们将`basePath`设置为`false`。

## 请求参数

使用 Next.js，您可以进一步控制重定向、访问主机、头、cookie 和查询值。使用`has`字段，您可以编写定制规则来控制在不同情况下是否应该执行重定向。

将`next.config.js`更改为以下代码:

```
module.exports = {
  async redirects() {
    return [
      {
        source: '/',
        has: [
          {
            type: 'header',
            key: 'host',
            value: 'localhost:3000',
          },
        ],
        permanent: false,
        destination: '/welcome',
      },
    ];
  },
}

```

`type`必须是`header`、`cookie`或`query`。`key`必须是所选类型中要匹配的字符串。`value`是可选的，如果未定义，将匹配`key`的任何值。

在上面的代码片段中，我们使用了`header`并检查了`host`键以获得`localhost:3000`值。如果请求中满足这些值，将进行重定向。

重启开发服务器并尝试访问`[https://localhost:3000](https://localhost:3000)`。您将被重定向到`[https://localhost:3000/welcome](https://localhost:3000/welcome)`，因为`host`值匹配。

现在用 **Ctrl+C** 关闭开发服务器并运行`npm run dev -- -p 8000`。这将在不同的端口上启动您的应用程序。现在在`[https://localhost:8000](https://localhost:8000)`上访问您的应用程序。这次您将不会被重定向，因为`host`值与您的重定向配置不匹配。

## API 重定向

Next.js 自带了一种处理 API 调用的内置方式。如果某个响应成功，您可以使用`redirect`方法来执行重定向。这在登录用户、提交表单和其他用例时非常方便。

要创建一个新的 API 路由，导航到`pages`中的`api`文件夹，创建一个新文件`data.js`，代码如下:

```
export default async function handler(req, res) {
  console.log(`Name: ${req.body.name}`);
  try {
    // some await stuff here
    res.redirect(307, '/welcome');
  } catch (err) {
    res.status(500).send({ error: 'Error while fetching data' });
  }
}

```

然后，导航到`pages`文件夹的根级别并创建一个新文件`form.js`，以创建表单本身。在新创建的文件中包含以下代码:

```
export default function Form() {
  return (
    <form action='/api/data' method='post'>
      <label htmlFor='name'>Your name:</label>
      <input type='text' id='name' name='name' />
      <button type='submit'>Submit</button>
    </form>
  );
}

```

现在打开浏览器，导航至`[https://localhost:3000/form](https://localhost:3000/form)`。您将看到一个输入字段，输入您的姓名，然后单击 submit 按钮将值发送给 API。输入任何值，提交它，您应该被重定向到`[https://localhost:3000/welcome](https://localhost:3000/welcome)`。

为了确保 API 接收到您输入的值，切换回终端并检查打印的日志。该值应该显示在那里。

## 大写到小写的 URL

您可能想知道如何在 Next.js 中将大写 URL 重定向到小写 URL。例如，假设您想将用户从`/About`重定向到`/about`。使用带有`source: '/About', destination: '/about'`的标准重定向解决方案是行不通的，因为重定向不区分大小写。

要解决这个问题，可以使用 Next.js 中间件。它使您能够在处理请求之前执行代码，允许您根据需要改变响应和重定向用户。

在项目的根目录下创建`middleware.js`文件，并包含以下代码:

```
import { NextResponse } from "next/server";

const Middleware = (req) =&gt; {
  if (req.nextUrl.pathname === req.nextUrl.pathname.toLowerCase())
    return NextResponse.next();

  return NextResponse.redirect(
    `${req.nextUrl.origin + req.nextUrl.pathname.toLowerCase()}`
  );
};

export default Middleware;

```

为了测试它，尝试利用任何以前创建的路线；您将被成功重定向到全小写的路由版本。现在，删除`middleware.js`文件；您会发现您无法访问全部小写的路由版本。

## getStaticProps and getServerSideProps

如果你想通过 Next.js 的内置预渲染方法设置重定向，你可以在`getStaticProps`或`getServerSideProps`中包含它们。

使用`getStaticProps`，页面将在构建时预呈现(静态站点生成)。

要设置一个示例，导航到`pages`文件夹的根级别并编辑`index.js`:

```
export default function Home() {
  return <h1>Home page</h1>;
}

export async function getStaticProps() {
  const content = null;

  if (!content) {
    return {
      redirect: {
        permanent: false,
        destination: '/welcome',
      },
    };
  }

  return {
    props: {},
  };
}

```

类似地，对于服务器端呈现(SSG)，您将使用`getServerSideProps`，这将确保 Next.js 在每个请求上预先呈现页面。

要设置 SSG 示例，编辑如下所示的`index.js`:

```
export default function Home() {
  return <h1>Home page</h1>;
}

export async function getServerSideProps() {
  const content = null;

  if (!content) {
    return {
      redirect: {
        permanent: false,
        destination: '/welcome',
      },
    };
  }

  return {
    props: {},
  };
}

```

为了测试这两种情况中的任何一种，尝试访问`[https://localhost:3000](https://localhost:3000)`，由于执行了`getStaticProps`或`getServerSideProps`中的重定向规则，您将被自动重定向到`[https://localhost:3000/welcome](https://localhost:3000/welcome)`。

## 重定向未经身份验证的用户

在全栈开发中，您通常希望限制用户对私有 URL 的访问(那些需要验证才能查看的 URL)。在这些情况下，您需要将用户重定向回登录屏幕。

首先，让我们看看如何认证静态生成的页面，其中用户是从客户端获取的。

首先创建一条受保护的新路径。接下来，在`pages`目录的根目录下创建一个新文件`profile.js`，并包含以下代码:

```
import useUser from '../hooks/useUser';

const Profile = () =&gt; {
  const user = useUser();

  if (!user) return null;
  return &lt;h1&gt;Hello, {user}&lt;/h1&gt;;
};

export default Profile;

```

然后，导航到`src`目录，创建一个新文件夹`hooks`，其中包含一个`useUser.js`文件，并包含以下代码:

```
import { useEffect } from "react";
import Router from "next/router";

export function useUser() {
  const user = false;

  useEffect(() =&gt; {
    if (!user) Router.push("/");
  }, [user]);
  return user;
}

```

在本教程中，我们为用户创建了一个示例变量。在实际场景中，您可能会使用类似于 [swr](https://swr.vercel.app/) 的东西来进行 API 调用，以检查是否有活动会话。

现在，尝试进入`/profile`路线。由于没有活动用户，您将被重定向到`/`路线。

接下来，将`user`值更改为某个字符串名称，并再次尝试访问`/profile`路由。对于您选择的值，您应该拥有对问候语的完全访问权限。

在 Next.js 中，我们还可以验证服务器呈现的页面。您可以使用`getServerSideProps`函数，该函数将在预呈现每个请求的页面时从服务器返回数据。

为了进行试验，用下面的代码更新`profile.js`文件:

```
const Profile = ({ user }) =&gt; {
  return &lt;h1&gt;Hello, {user}&lt;/h1&gt;;
};

export function getServerSideProps() {
  const user = false;

  if (!user) {
    return {
      redirect: {
        destination: "/",
        permanent: false,
      },
    };
  }

  return {
    props: {user},
  };
}

export default Profile;

```

请注意，活动用户将在服务器端获取。在本教程中，我们使用了一个用户变量来定义登录用户是否存在。

要查看重定向的工作情况，请尝试访问`/profile`路线。您将被重定向到`/`，因为应用程序中没有可用的活动用户。

现在，将`user`值更改为某个名称字符串，并尝试再次访问`/profile`路线。您将可以完全访问您选择的字符串值的问候语，因为它是通过 props 从服务器传递过来的。

## 结论

在本文中，我们研究了在 Next.js 中实现重定向的几种方法。首先，我们使用`next.config.js`并为预定义的路由编写自定义配置，访问单级和嵌套路由，并使用 regex 增加对重定向的控制。

然后，我们进一步研究了如何基于收到的请求参数创建重定向，以及如何将大写路由重定向到小写路由。最后，我们研究了如何使用 API 路由、静态站点生成和服务器端呈现来实现重定向，以及如何重定向需要认证的路由。

我希望通过这篇文章你能学到一些新的东西，从这一点开始，你将能够在你未来的 Next.js 应用程序中为你的所有用例创建重定向。

## [LogRocket](https://lp.logrocket.com/blg/nextjs-signup) :全面了解生产 Next.js 应用

调试下一个应用程序可能会很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪状态、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/nextjs-signup)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png)](https://lp.logrocket.com/blg/nextjs-signup)[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/nextjs-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你的 Next.js 应用上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用程序的性能，报告客户端 CPU 负载、客户端内存使用等指标。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

让您调试 Next.js 应用的方式现代化— [开始免费监控](https://lp.logrocket.com/blg/nextjs-signup)。