# 比较 SSR React 应用程序的 Next.js 和 after . js-log rocket 博客

> 原文：<https://blog.logrocket.com/comparing-next-js-after-js/>

***编者按:*** *本帖于 2021 年 12 月 1 日更新，新增代码块及相关信息。*

[Next.js](https://blog.logrocket.com/tag/nextjs/) 和 [After.js](https://github.com/jaredpalmer/after.js/) 都是框架，让我们可以构建服务器端渲染的 React 应用，而无需从 create-react-app 项目中从头开始设置 SSR。在本文中，我们将比较 Next.js 和 After.js，并探索它们的差异。

## Next.js 与 After.js

Next.js 和 After.js 的主要区别在于路由机制。

使用 Next.js，我们不直接指定路线；相反，我们让 Next.js 自动处理路由。它通过检查项目的文件结构并将 URL 直接映射到组件，从而将 URL 映射到我们的组件。

使用 After.js，我们需要显式地指定路由。After.js 使用 [React 路由器](https://reactrouter.com/)进行路由。

大多数其他特性，比如数据获取和页面创建方式，在这两者之间是非常相似的。创建项目的方式与创建自己的 CLI 程序的方式非常相似。

创建 Next.js 项目最简单的方法是使用 create-next-app。要开始，请运行:

```
npx [email protected]
# or
yarn create next-app
Note: For a Typescript project, add a ​​--typescript flag to this command.
```

为了创建 After.js 项目，我们运行:

```
yarn global add create-after-app
// or
npm install -g create-after-app
create-after-app myapp
```

注意:在撰写本文时，After.js 抛出了一个 React 路由器最新版本的错误(参见这里的 GitHub 问题)。一个快速的解决方法是删除我们的`node_modules`文件夹并更新`package.json`，就像这样:

```
"dependencies": {
    ...
    "react-router-dom": "^5.3.0"
  }
```

一旦完成，我们可以用`yarn install`或`npm install`重新安装软件包。

## 在 Next.js 和 After.js 中创建页面

Next.js 和 After.js 都允许我们通过添加 React 组件来创建页面。举例来说，我们将使用带有 Next.js 和 After.js 的[新闻 API](https://newsapi.org/) 创建一个应用程序。我们所要做的就是创建组件，然后它将显示在我们的应用程序中。

为了从新闻 API 中获取数据，我们将使用[同构解锁](https://github.com/developit/unfetch/tree/master/packages/isomorphic-unfetch)包。我们可以通过在新创建的项目中运行以下命令来获得它:

```
npm i isomorphic-unfetch
// or
yarn add isomorphic-unfetch
```

我们还需要一个来自新闻 API 的 API 键，我们可以免费创建。

在 Next.js 中，如果我们在`pages`文件夹中创建页面，我们可以通过同名的 URL 导航到它。

例如，在 Next.js 中，我们可以在`pages`文件夹中创建`index.js`，如下所示:

```
import NavBar from '../components/navbar'
import fetch from 'isomorphic-unfetch';
import HeadTag from '../components/head-tag';

export async function getStaticProps() {
  const res = await fetch(`https://newsapi.org/v2/top-headlines/?language=en&apiKey=${process.env.NEWS_API_KEY}`)
  const data = await res.json()
  return {
    props: {
      data,
    },
  }
}

const Home = ({ data }) => (
  <div>
    <HeadTag />
    <NavBar />
    {(data.articles) && data.articles.map(a => (
      <div key={a.title}>
        <h1>{a.title}</h1>
        <p>{a.description}</p>
        <p>{a.content}</p>
      </div>
    ))}
  </div>
)

export default Home
```

`getStaticProps`函数将让我们获取数据，然后我们可以从组件中同名的 props 中获取数据。

我们可以在`about.js`中创建一个关于页面，如下:

```
import NavBar from '../components/navbar'
import HeadTag from '../components/head-tag'

const Home = () => (
  <div>
    <HeadTag />
    <NavBar />
    <p>This is a news app.</p>
  </div>
)

export default Home
```

现在，我们可以通过分别转到`/`和`/about`来直接导航到它们。

在我们的根项目中创建了`components`文件夹之后，我们可以通过创建以下文件来创建我们可以在页面上引用的组件:

```
//head-tag.js

import Head from 'next/head'

const HeadTag = () => (
  <Head>
    <title>News App</title>
  </Head>
)

export default HeadTag
```

```
//navbar.js

import Link from 'next/link'

const NavBar = () => (
  <nav>
    <Link href="/">
      <a>Home</a>
    </Link>

    <Link href="/about">
      <a>About</a>
    </Link>
  </nav>
)

export default NavBar
```

在我们的 After.js 应用程序中，我们在`src`文件夹中创建了以下组件:

```
//Home.js

import React, { Component } from 'react';
import NavBar from './NavBar';
import fetch from 'isomorphic-unfetch';

class Home extends Component {
  static async getInitialProps() {
    const res = await fetch(`https://newsapi.org/v2/top-headlines/?language=en&apiKey=${process.env.RAZZLE_APIKEY}`)
    const data = await res.json();
    return { data };
  }

  render() {
    const { data } = this.props;
    return (
      <div>
        <NavBar />
        {data.articles.map(a => (
          <div key={a.title}>
            <h1>{a.title}</h1>
            <p>{a.description}</p>
            <p>{a.content}</p>
          </div>
        ))}
      </div>
    );
  }
}

export default Home;
```

我们在`initialProps`静态方法中获取数据，然后我们可以通过 props 访问它。

```
//About.js

import React, { Component } from 'react';
import NavBar from './NavBar';

class About extends Component {
  render() {
    return (
      <div>
        <NavBar />
        <p>This is a new app</p>
      </div>
    );
  }
}

export default About;
```

我们在页面中引用的组件可以在同一个文件夹中创建:

```
// NavBar.js
import React from 'react';
import { Link } from 'react-router-dom';

class NavBar extends React.Component {

  render() {
    return (
      <div>
        <Link to="/">Home</Link>
        <Link to="/about">About</Link>
      </div>
    );
  }
}

export default NavBar;
```

正如我们所见，这些都只是标准组件。不同的是，我们在 After.js 中使用了 React Router 的`Link`组件用于页面导航链接，而在 Next.js 项目中，我们使用 Next.js 自己的`Link`组件添加页面导航。

也就是说，在每个框架中，创建页面的体验几乎是相同的。

## Next.js 与 After.js 中的路由

路由是 Next.js 和 After.js 很不一样的地方。

[next . js’routing](https://blog.logrocket.com/prefetching-dynamic-routing-next-js/)开箱即用，没有太多麻烦。一旦我们创建了页面，我们就可以直接导航到它们，或者通过由`Link`组件呈现的链接导航到它们。

另一方面，After.js 要复杂得多。如果我们有一个使用`getInitialProps`方法获取数据的页面组件，那么我们不能将它作为异步组件添加到 routes 文件中。

在 After.js 项目的`src`文件夹中的`routes.js`文件中，我们有:

```
import React from 'react';

import { asyncComponent } from '@jaredpalmer/after';
import Home from './Home';

export default [
  {
    path: '/',
    exact: true,
    component: Home,
  },
  {
    path: '/about',
    exact: true,
    component: asyncComponent({
      loader: () => import('./About'), // required
      Placeholder: () => <div>...LOADING...</div>, // this is optional, just returns null by default
    }),
  },
];
```

About.js 在渲染之前不会获取数据，所以我们可以将它作为异步路由。然而，`Home`组件不能作为异步路由包含进来，因为我们有`getInitialProps`异步方法来获取数据。

在 Next.js 项目中，路由更容易处理。使用 After.js 可以更好地配置路由，但是我们必须自己配置。

## Next.js 和 After.js 中的数据提取

Next.js 和 After.js 中的数据提取都是在组件第一次呈现时完成的。从上一节我们可以看到，Next.js 项目中的`index.js`在 async `getStaticProps`函数中获取数据。

在 After.js 项目中，我们使用 component 类的`getInitialProps`静态方法在呈现页面之前提取数据。

在这两个组件中，获取的数据在组件中作为道具可用。

[在 Next.js](https://blog.logrocket.com/handling-data-fetching-next-js-useswr/) 中，为环境变量增加了内置支持。我们所需要的就是在我们的根项目中创建一个`.env.local file`,并添加:

```
NEWS_API_KEY=
```

一旦保存，文件将自动加载，变量将用于我们的应用程序。

而且，在 After.js 项目中，环境变量存储在`.env`文件中，我们可以在项目中使用的环境变量的键必须以`RAZZLE_`为前缀。

例如，我们可以这样写:

```
RAZZLE_APIKEY=your_api_key
```

在这两个框架中，环境变量都是作为`process.env`对象的属性提供的。

## 结论

Next.js 和 After.js 都可以用来构建服务器端呈现的项目。它们有助于轻松构建服务器端渲染应用。

Next.js 和 After.js 的主要区别在于路由。Next.js 根据组件的名称以及组件文件是否在`pages`文件夹中，将 URL 映射到组件。相比之下，After.js 使用 React 路由器进行路由，我们必须自己进行配置。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)