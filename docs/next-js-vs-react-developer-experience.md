# Next.js vs. React:开发者体验- LogRocket 博客

> 原文：<https://blog.logrocket.com/next-js-vs-react-developer-experience/>

***编者按**:本文于 2022 年 2 月 3 日更新，纳入了关于 [React 18](https://reactjs.org/blog/2021/06/08/the-plan-for-react-18.html) 中开发者体验的信息。*

当选择任何软件库或框架时，人们通常会考虑开发人员的体验。当我在这里说“开发人员体验”时，我的意思是说开发人员实际完成任务是什么样的。开发人员通常喜欢有趣且易于使用的库或框架。这是我们今天拥有领先的库和框架的主要原因。

在 React 的世界里，Next.js 已经成为“立即进入角色”的流行框架。作为 React 的粉丝，我在几个月前开始使用 Next.js，并且非常喜欢在我的项目中使用它。我非常喜欢它，甚至用 Next.js 重写了我的个人博客(查看[我在上面的帖子](https://rhythmandbinary.com/post/2020-06-08-hello-rhythmandbinary-com))。

Next.js 构建在 React 之上，提供了简化的开发体验。Next.js 有一个小的学习曲线，但是即使是不熟悉前端世界的开发人员也可以相对较快地上手并运行。话虽如此，但用 Next.js 与 React 构建项目时，体验肯定是不一样的。

这篇文章比较了 Next.js 和 React 的开发者体验。我将首先介绍一些背景知识，然后深入到更具体的内容，讨论最初启动一个项目、构建页面、检索数据、使用文档以及使用 Next.js 和 React 执行高级操作的情况。

我将提到一个示例项目，你可以在[我的 GitHub repo 这里](https://github.com/andrewevans0102/developer-experience-react-nextjs)找到。这个项目展示了一个关于热播剧《曼达洛人》的粉丝网站的两种不同实现。项目中的`react`文件夹当然是 React 版本，`next.js`文件夹包含 Next.js 版本。这两个项目的工作，应该只需要标准的`npm install`启动和运行。

您可以使用以下内容跳转到教程的相关部分:

## Next.js 和 React 有什么不同？

在我们深入实际的开发人员体验之前，了解一些背景知识会有所帮助。

![A react banner](img/964a03bc735c62aa35665372024bc96f.png)

最初由脸书创建，如今已经成为前端世界最受欢迎的图书馆之一。React 很容易扩展，可以包含路由等特性以及带有 Redux 等库的状态管理模式。React 占用的空间很小，但几乎可以针对任何项目进行定制。更多关于 React 的高级信息，请查看[官方 React 文档](https://reactjs.org/)。

![Next.js logo](img/587557e18ff4dc065c259917bcdb7d57.png)

Next.js 创建于 React 之上，旨在构建一个易于使用的开发框架。它是由 Vercel(以前的 Zeit)开发的，利用了 React 的许多流行特性。开箱即用，Next.js 提供了预渲染、路由、代码分割和 webpack 支持等功能。关于 Next.js 的更多信息，请查看[官方 Next.js 文档](https://nextjs.org/docs/getting-started)。

## React vs. Next.js 项目是什么样子的？

使用 React，您可以通过在机器上安装 Node.js 并运行`npx create-react-app my-app`来启动并运行。这将创建一个基本的项目结构，以`src/App.js`文件作为应用程序的入口点。

您还将有一个`public`文件夹，您可以在其中存储资产，最初的脚手架包括一个服务人员和一个方法来引入 [Jest](https://blog.logrocket.com/testing-apps-with-jest-and-react-testing-library/) 进行测试。最初的脚手架看起来像这样:

```
.
├── README.md
├── package.json
├── node_modules
├── public
│   ├── favicon.ico
│   ├── index.html
│   ├── logo192.png
│   ├── logo512.png
│   ├── manifest.json
│   └── robots.txt
├── src
│   ├── App.css
│   ├── App.js
│   ├── App.test.js
│   ├── index.css
│   ├── index.js
│   ├── logo.svg
│   ├── reportWebVitals.js
│   └── setupTests.js
└── yarn.lock (or package-lock.json)

```

有了 Next.js，运行`npx create-next-app`就可以开始了。这将搭建出一个项目，该项目已经有一个用于页面或路径的`pages`文件夹和一个用于托管您的资产的`public`目录。最初的脚手架看起来像这样:

```
.
├── README.md
├── package.json
├── node_modules
├── pages
│   ├── _app.js
│   ├── api
│   └── index.js
├── public
│   ├── favicon.ico
│   └── vercel.svg
├── styles
│   ├── Home.module.css
│   └── globals.css
└── yarn.lock (package-lock.json)

```

`pages`目录中的文件与应用程序中的路线相关。`public`目录保存你想要提供的静态文件或图片，可以直接访问——不需要使用`require`或其他传统的 React 方法将图片导入组件。

在`pages`目录中，您会看到一个`index.js`文件，这是您的应用程序的入口点。如果你想导航到其他页面，你可以使用带有`Link`的路由器，如下所示:

```
        <div className="header__links">
            <Link href="/">
                <a className="header__anchor">Home</a>
            </Link>
            <Link href="/about">
                <a className="header__anchor">About</a>
            </Link>
        </div>

```

关于开发人员的体验，对于 Next.js 和 React 来说，最初的搭建过程非常简单。然而，React 确实需要您添加像 [React Router](https://blog.logrocket.com/migrating-react-router-v6-complete-guide/) 这样的库来进行路由，而 Next.js 通过`Link`组件提供了开箱即用的功能。

此外，你的应用程序的整体结构已经由 Next.js 引导，用`pages`目录保存你的容器等。

## 构建页面

现在我们可以开始讨论 React 与 Next.js 的真实例子，以及我在开始时提到的示例应用程序。同样，你可以在回购协议中找到它。

使用 React 构建页面需要您创建一个组件，然后引入 React Router 来编排站点中的转换。如果您查看示例应用程序中的`react`文件夹，您会看到传统 React 应用程序可能会出现的情况:

```
export default function App() {
    return (
        <Router>
            <section>
                <Header />
                <Switch>
                    <Route path="/episodes">
                        <EpisodesPage />
                    </Route>
                    <Route path="/season2">
                        <Season2Page />
                    </Route>
                    <Route path="/quotes">
                        <QuotesPage />
                    </Route>
                    <Route path="/">
                        <HomePage />
                    </Route>
                </Switch>
            </section>
        </Router>
    );
}

```

这里的`Header`、`EpisodesPage`、`Season2Page2`、`QuotesPage`和`HomePage`都是 React Router 路由 URL 路径进行渲染的组件。

如果你查看这个项目的`Next.js`文件夹，你会注意到这个项目更加精简，因为所有的路线都内置在`pages`文件夹中。`Header`组件使用`Link`路由到不同的页面，如下所示:

```
import Link from "next/link";
const Header = () => {
    return (
        <nav className="header">
            <span>
                <Link href="/">Home</Link>
            </span>
            <span>
                <Link href="/episodes">Episodes</Link>
            </span>
            <span>
                <Link href="/season2">Season 2</Link>
            </span>
            <span>
                <Link href="/quotes">Quotes</Link>
            </span>
        </nav>
    );
};
export default Header;

```

Next.js 项目的高级视图也显示了它是多么容易理解:

```
.
├── README.md
├── package-lock.json
├── package.json
├── pages
│   ├── _app.js
│   ├── _document.js
│   ├── components
│   │   └── Header.js
│   ├── episodes.js
│   ├── index.js
│   ├── quotes.js
│   ├── season2.js
│   └── styles
│       ├── _contact.scss
│       ├── _episodes.scss
│       ├── _header.scss
│       ├── _home.scss
│       ├── _quotes.scss
│       ├── _season2.scss
│       └── styles.scss
├── public
│   ├── HomePage.jpg
│   └── favicon.ico
└── yarn.lock

```

当您想要为 React 项目构建页面时，您必须构建组件，然后将其添加到路由器。当您想要为 Next.js 项目构建页面时，您只需将页面添加到`pages`文件夹，并将必要的`Link`添加到`Header`组件。这使您的生活变得更容易，因为您编写的代码更少，并且项目很容易跟进。

## 拉入数据

对于任何应用程序，您总是需要检索数据。无论是静态站点还是利用多个 API 的站点，数据都是重要的组成部分。

如果您查看我的[示例项目](https://github.com/andrewevans0102/developer-experience-react-nextjs)中的`react`文件夹，您会看到`EpisodesPage`组件使用一个 Redux 动作来检索`episodes`数据，如下所示:

```
    const dispatch = useDispatch();
    // first read in the values from the store through a selector here
    const episodes = useSelector((state) => state.Episodes.episodes);
    useEffect(() => {
        // if the value is empty, send a dispatch action to the store to load the episodes correctly
        if (episodes.length === 0) {
            dispatch(EpisodesActions.retrieveEpisodes());
        }
    });
    return (
        <section className="episodes">
            <h1>Episodes</h1>
            {episodes !== null &&
                episodes.map((episodesItem) => (
                    <article key={episodesItem.key}>
                        <h2>
                            <a href={episodesItem.link}>{episodesItem.key}</a>
                        </h2>
                        <p>{episodesItem.value}</p>
                    </article>
                ))}
            <div className="episodes__source">
                <p>
                    original content copied from
                    <a href="https://www.vulture.com/tv/the-mandalorian/">
                        here
                    </a>
                </p>
            </div>
        </section>
    );

```

Redux 操作从本地文件中检索值:

```
import episodes from '../../config/episodes';

// here we introduce a side effect
// best practice is to have these alongside actions rather than an "effects" folder
export function retrieveEpisodes() {
    return function (dispatch) {
        // first call get about to clear values
        dispatch(getEpisodes());
        // return a dispatch of set while pulling in the about information (this is considered a "side effect")
        return dispatch(setEpisodes(episodes));
    };
}

```

使用 Next.js，您可以利用其内置的[数据获取 API](https://nextjs.org/docs/basic-features/data-fetching)来格式化您的数据并预先呈现您的站点。你也可以做所有你通常用 React 钩子和 API 调用做的事情。使用 Next.js 拉入数据的额外优势是，生成的包是预先呈现的，这使得网站的消费者更容易使用。

在我的示例项目中，如果你转到`nextjs`文件夹和`episodes.js`页面，你会看到关于*曼达洛人*剧集的信息实际上是通过调用`getStaticProps`构建的，所以实际的数据检索只发生在网站首次构建时:

```
function EpisodesPage({ episodes }) {
    return (
        <>
            <section className="episodes">
                <h1>Episodes</h1>
                {episodes !== null &&
                    episodes.map((episodesItem) => (
                        <article key={episodesItem.key}>
                            <h2>
                                <a href={episodesItem.link}>{episodesItem.key}</a>
                            </h2>
                            <p>{episodesItem.value}</p>
                        </article>
                    ))}
                <div className="episodes__source">
                    <p>
                        original content copied from
                        <a href="https://www.vulture.com/tv/the-mandalorian/">here</a>
                    </p>
                </div>
            </section>
        </>
    );
}
export default EpisodesPage;
export async function getStaticProps(context) {
    const episodes= [...];
    return {
        props: { episodes }, // will be passed to the page component as props
    };
}

```

## 更高级的操作

除了我们在这里介绍的基本功能，您最终还需要做一些更复杂的事情。

在 React 应用程序中，最常见的模式之一是使用 Redux。Redux 很棒，因为它扩展了处理应用程序状态的常用方法。无论您的应用程序在做什么，创建动作、缩减器、选择器和副作用的过程都可以很好地伸缩。

使用 React，这就是定义一个存储，然后在整个应用程序中构建流的问题。我做的第一件事就是看看我能否在我的项目中用 Next.js 做到这一点。经过一些谷歌搜索(和一些失败的尝试)，我发现由于 Next.js 预先和重新呈现每个页面的方式，使用商店非常困难。有几个人已经用 Next.js 实现了 Redux，但这并不像你在普通 React 应用中看到的那样简单。

Next.js 没有使用 Redux，而是使用支持预渲染的数据获取 API。这些都很棒，因为你的网站变成了一组静态的片段，可以很容易地被网络爬虫读取，从而提高你的网站的搜索引擎优化。

这是一个巨大的胜利，因为 JS 包通常很难被爬虫理解。此外，您可以更灵活地使用这些 API 和构建时生成的资产，如 RSS 提要。

我的个人博客网站是用 Next.js 编写的。实际上，我通过使用 Next.js 附带的`getStaticProps` API 构建了自己的 RSS 提要:

```
export async function getStaticProps() {
  const allPosts = getAllPosts(["title", "date", "slug", "content", "snippet"]);

  allPosts.forEach(async (post) => {
    unified()
      .use(markdown)
      .use(html)
      .process(post.content, function (err, file) {
        if (err) throw err;
        post.content = file;
      });
  });

  const XMLPosts = getRssXml(allPosts);
  saveRSSXMLPosts(XMLPosts);

  return {
    props: { XMLPosts },
  };
}

```

`getAllPosts`和`getRssXml`功能将 Markdown 转换成 RSS 标准。然后可以将它部署到我的站点，启用 RSS 提要。

当涉及到 Redux 或预渲染等更高级的功能时，React 和 Next.js 都有权衡。适用于 React 的模式不一定适用于 Next.js，这并不是一件坏事，因为 Next.js 有自己的优势。

总的来说，在实现更高级的操作时，开发人员使用 Next.js 的体验有时会比您通常在 React 项目中看到的更具指导性。

## 使用文档

对于任何软件项目，好的文档确实可以帮助你容易地使用工具，理解使用什么库，等等。React 和 Next.js 都有很棒的文档选项。

正如我在介绍中提到的，Next.js 有一套“边做边学”[的文档](https://nextjs.org/learn/basics/create-nextjs-app)，指导你如何做诸如路由和构建组件之类的事情。React 也有类似的设置，有多个教程[解释基础](https://reactjs.org/tutorial/tutorial.html)。

有了 React，你还可以依靠一个伟大的开发者社区，他们已经在[博客文章](https://blog.logrocket.com/tag/react/)、YouTube 视频、Stack Overflow 甚至 React 文档本身中创建了内容。随着图书馆的成熟，这是经过多年发展建立起来的。

有了 Next.js，正式教程的方式少了，GitHub 问题和对话的方式多了。当我建立我的个人博客网站时，有时我不得不做大量的谷歌搜索来解决 Next.js 的问题。然而，Next.js 的团队成员本身在开源世界中非常容易接近。

当我今年夏天早些时候在 Next.js 上写了一篇帖子时，Next.js 团队成员之一的 Tim Neutkens 实际上直接在 Twitter 上回复了我。他帮我解决了一个问题，和他一起工作真的很棒。让社区成员变得平易近人是一种巨大的力量。

在 React 社区中，有许多关键成员也同样容易接近。在 React 和 Next.js 中，活跃的社区提供了非常积极的开发人员体验。

## React 18 中的开发者体验

目前，Next.js 和 Create React App 都在使用 React 17 . 0 . 2 版本。但是很快，React 18 将会发布，这将为 React 开发者体验带来一些变化。

最大的变化之一将是新的 [React root API](https://blog.logrocket.com/exploring-react-18-three-new-apis/) ，它改变了应用组件呈现到 DOM 的方式。在 React 17 和之前的版本中，这是通过使用`ReactDOM.render`方法完成的，该方法将 JSX 渲染到 DOM 目标:

```
import * as ReactDOM from 'react-dom';
import App from 'App';

const container = document.getElementById('app');

// Initial render.
ReactDOM.render(<App tab="home" />, container);

// During an update, React would access
// the root of the DOM element.
ReactDOM.render(<App tab="profile" />, container);

```

使用新的根 API，您将首先将 DOM 容器标识为根，然后向它呈现 JSX。
这使得多次调用变得不那么重复，同时对水合作用进行了一些更改，以更好地促进现代用例，如部分水合作用:

```
import * as ReactDOM from 'react-dom';
import App from 'App';

const container = document.getElementById('app');

// Create a root.
const root = ReactDOM.createRoot(container);

// Initial render: Render an element to the root.
root.render(<App tab="home" />);

// During an update, there's no need to pass the container again.
root.render(<App tab="profile" />);

```

React 还将尝试批处理状态更改，这意味着它会将状态更改分组到一个更新中，以减少重复呈现。在 React 18 之前，批处理只为来自事件处理程序的状态更改保留，但其他情况(如异步状态更改)不进行批处理(这意味着它们会触发单独的呈现)。在 React 18 中，所有的状态更新都将被批处理。

React 18 中的另一大新增功能是`startTransition` API。在 React 18 之前，所有的状态变化都被同等优先处理，所以较大的状态变化会导致 UI 挂起，这不是最好的用户体验。

使用 React 18，您可以将较慢的状态更改包装在一个`startTransition`回调中，这将允许 React 确保在处理状态更改时 UI 不会挂起:

```
import { startTransition } from 'react';

// Urgent: important state changes the UI must wait for work like normal
urgentState(input);

// Mark any state updates inside as transitions
startTransition(() => {
  // Transition: state changes that shouldn't hang the UI
  nonUrgentState(input);
}); 

```

因此，如果在输入表单时触发上述操作，第一个状态更改将总是在我继续输入之前完成，但是“转换”状态更改将在额外输入时取消，而不是挂起 UI 直到它完成。你可以在这里阅读[更多关于过渡的内容。](https://github.com/reactwg/react-18/discussions/41)

最后，React 18 对其处理应用程序服务器端渲染的方式进行了一些架构上的改变，这将为 Next.js、 [Remix](https://blog.logrocket.com/remix-guide-newly-open-sourced-react-framework/) 和 Gatsby 等 metaframeworks 带来速度上的提升。不是整个应用程序必须完成服务器端渲染才能在用户浏览器中合成，而是使用流 HTML 完成的部分可以部分合成，使用户感觉加载时间更快。你可以在这里阅读更多关于这些[对 SSR 的更新。](https://github.com/reactwg/react-18/discussions/37)

底线是 React 18 将为 React 和 Next.js 的开发者体验带来许多改进。

## 关于 Next.js 与 React 的最终想法

开发者体验让工程师热爱他们的工作。我是一名专业的工程师，但是专业的环境并不总是有助于最好的开发者体验。

在一个完美的世界中，你会发现自己在一个伟大的工程师团队中，拥有伟大的产品、强大的用户社区和强大的工具。在现实世界中，你会发现其中的一些，但通常缺少其中的一个(或多个)。

React 和 Next.js 都以自己的方式提供了优秀的开发者体验。React 让你以自己想要的方式构建东西，并得到一个强大社区的支持。Next.js 通过一些现成的工具和约定让您的生活变得更加轻松，并且它还受到一个非常活跃的开源社区的支持。

说到工具本身，React 和 Next.js 都很容易上手。超越简单的“hello world”项目，找到帮助是相当容易的，无论是在文档中还是在社区资源中。

React 和 Next.js 都有自己擅长的具体事情。正如我提到的，我用 Next.js 重写了我的个人博客，因为我是一个粉丝。对于静态站点来说，这是一个很好的工具，可以很容易地与我设置的 CI/CD 管道连接。此外，当我想为不同的页面向我的站点添加组件时，它很容易导航。

React 对任何项目都是一个很好的补充，如果有机会，它还可以扩展。React 比 Next.js 更通用只是因为它是一个库；由工程师决定其实现。

最终，React 和 Next.js 都提供了坚实的开发人员体验。我希望我在这里所做的比较和讨论能给你一些启示，让你知道如何在你的项目中使用它们。我鼓励你看看它们，也看看我的示例项目。

感谢您阅读我的帖子！在 [andrewevans.dev](https://www.andrewevans.dev) 上关注我，在 [@AndrewEvans0102](https://twitter.com/AndrewEvans0102) 的 Twitter 上与我联系。

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