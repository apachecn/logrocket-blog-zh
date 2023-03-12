# remix vs . next . js vs . SvelteKit-log rocket 博客

> 原文：<https://blog.logrocket.com/react-remix-vs-next-js-vs-sveltekit/>

***编者按**:这篇文章最后一次更新是在 2022 年 12 月 15 日，包括了关于部署和错误处理的章节。*

Remix 是 React 路由器创建者的一个全栈 web 框架，已经从付费模式过渡到免费模式，这对 React 和更大的元框架社区来说都是一个大新闻。

在过去的几年中，SaaS 范式的使用，即通常由开源技术在云中使用的业务模型，已经在行业内得到巩固。例如，React 元框架 Next.js 和 Gatsby 都提供付费托管服务，这些服务具有为优化而定制的附加功能。

同样，Shopify 发布了一个名为 [Hydrogen](https://hydrogen.shopify.dev/) 的 React 元框架，以及一个名为 Oxygen 的[托管服务。Neo4j、ArangoDB 和 MongoDB 等数据库都提供云数据库服务，使得采用和使用更加容易。最终，混音创作者也计划发布一个个性化的优化平台。](https://shopify.dev/custom-storefronts/oxygen)

与此同时，Remix 竞争对手 Next.js 背后的创作者 Vercel 在雇佣苗条创作者 Rich Harris 全职工作于主要的苗条元框架 SvelteKit 方面有了有趣的发展。

作为一个用于服务器端渲染的[框架，Remix](https://blog.logrocket.com/guide-to-remix-react-framework/) 旨在满足与 Next.js 和 SvelteKit 等框架相同的一些需求。在本文中，我们将比较它们的一些特性，从启动项目到添加样式。在本文结束时，您应该能够更好地为您独特的项目选择一个。我们开始吧！

> **N.B.** ，Vue、Angular 和 [SolidJS](https://blog.logrocket.com/introduction-solidjs/) 的等价元框架分别是 Nuxt.js、Angular Universal 和 SolidStart。

## 启动一个项目

首先，我们将考虑在每个框架中创建新项目的命令:

```
//Remix
npx [email protected]

```

当生成一个 Remix 项目时，您可以选择一个为部署到不同的托管服务而定制的模板，如 Netlify、Cloudflare、Fly.io、Vercel 等。每个都配有文档，使部署变得轻而易举:

```
//Next.js
npx [email protected]
//SvelteKit
npm init [email protected] my-app

```

## 按指定路线发送

路由决定了访问网站上不同页面所需的 URL。所有三个元框架都使用基于文件的路由，这是所有元框架主要使用的。该 URL 基于该特定页面的文件的名称和位置。

下面，您将看到一些不同文件如何在每个元框架中映射到 URL 的例子，包括一个带有 URL 参数的例子，它将 URL 的一部分定义为一个可以检索的变量。

### 再搅拌

Remix 是建立在 [React 路由器 v6](https://blog.logrocket.com/react-router-v6/) 之上的。因此，您可以在客户端代码中使用许多最新的[钩子，如`useParams`和`useNavigate`](https://blog.logrocket.com/using-hooks-react-router/) 来处理导航，[类似于正常使用 React Router v6](https://blog.logrocket.com/migrating-react-router-v6-guide/) :

`/`→`app/routes/index.js`
`/hello`→`app/routes/hello/index.js or app/routes/hello.js`
`/use_param/:id`→`app/routes/use_param/$id.js`

### Next.js

`/`→`pages/index.js`
→`/hello`→`pages/hello/index.js`或者`pages/hello.js`
`/use_param/:id` → `pages/use_param/[id].js`

### 苗条套装

`/`→`src/routes/index.svelte`
→`/hello` →`src/routes/hello/index.svelte`或者`src/routes/hello.svelte`
`/use_param/:id` → `src/routes/use_param/[id].svelte`

## 在服务器端加载数据

使用元框架的一个主要好处是在页面水合之前处理大量的数据准备，比如 API 调用、转换等。当您使用元框架时，您不必准备加载器或类似于`useEffect`钩子的东西来处理这些问题的异步本质。

在所有三个元框架中，每个页面上都有一个我们可以定义的功能，在将页面发送到用户浏览器之前，该功能将从服务器上运行。

### 再搅拌

在 Remix 中，我们定义了一个名为`loader`的函数，它将被传递一个对象，该对象包含 URL 参数和请求数据等内容，我们将使用这些内容来准备任何数据。然后，`loader`函数可以返回页面需要的任何数据。然后可以使用`useLoaderData`钩子在组件中检索这些数据，如下所示:

```
import { useLoaderData } from "remix";

export let loader = async ({ params, request }) => {
   // get a param from the url
   const id = params.id
   // getting data from the url query string
   const url = new URL(request.url)
   const limit = url.searchParams.get("limit")

   return {id, limit}
};

export default function SomePage() {
  let {id, limit} = useLoaderData();
  return (
    <div>
      <h1>The params is: {id}</h1>
      <h1>The limit url query is: {limit}</h1>
    </div>
  );
}

```

### Next.js

同样，在 Next.js 中，可以导出一个名为`getServerSideProps`的函数。返回值可以定义对`page`组件的支持:

```
export const getServerSideProps = async ({ params, query }) => {
   // get a param from the url
   const id = params.id
   // getting data from the url query string
   const limit = query.limit

   return {props: {id, limit}}
};

export default function SomePage() {
  let {id, limit} = useLoaderData();
  return (
    <div>
      <h1>The params is: {id}</h1>
      <h1>The limit url query is: {limit}</h1>
    </div>
  );
}

```

### 苗条套装

使用 SvelteKit，您可以在单独指定的脚本块中定义一个名为`load`的函数。就像前面的例子一样，您可以处理任何 API 调用和数据准备，然后将用作道具的数据返回给`page`组件:

```
<script context="module">
        // Load function to define data
        export function load({ page }) {
   // get params from url
   const id = page.params.id
   // get data from url query
   const limit = page.query.get("limit")
                return {
                        props: {
                                id,
            limit
                        }
                };
        }
</script>

<script>
   // normal client side javascript block
        export let id;
   export let limit
</script>

<div>
   <h1>The params is: {id}</h1>
   <h1>The limit url query is: {limit}</h1>
</div>

```

## 将页面预呈现为静态站点生成器

将页面预渲染为静态站点生成器可能是特性集中最大的转移。在撰写本文时，Remix 不支持页面的预渲染，而 Next.js 和 SvelteKit 支持，这意味着您也可以将它们用作静态站点生成器。

### 再搅拌

在撰写本文时，Remix 还不支持静态站点生成，但它提供了一个关于使用分布式云技术来优化你的应用的[指南。](https://remix.run/docs/en/v1/guides/performance)

### Next.js

如果您喜欢页面被预渲染，只需导出`getStaticProps`而不是`getServerSideProps`。否则，我们将观察到与之前相同的模式:

### 苗条套装

如果您希望在模块脚本博客中预先呈现您的页面，只需设置以下代码:

```
export const prerender = true;

```

上面的代码将告诉 SvelteKit 预先呈现页面，而不是在每次请求时都呈现。

## API 路由

虽然我们可以用`loader`、`getServerSideProps`或`load`函数在服务器端处理逻辑，但是 API 键和其他数据不应该出现在这段代码中。您可能仍然需要专用的 API URLs，其中的代码只在服务器端可见并运行。

### 再搅拌

如果您创建一个不导出组件的文件，那么它将被视为一个资源路径，可以创建一个 JSON 响应，如下所示:

```
export function loader({ params }) {
  const id = params.id
  return new Response({id}, {
    status: 200,
    headers: {
      "Content-Type": "application/json"
    }
  });
}

```

### Next.js

如果您创建一个导出路由功能的路由，如在 Express 中的`pages/api`文件夹中，它将被视为类似于 API 路由:

```
export default function handler(req, res) {
  res.status(200).json({ id: req.params.id })
}

```

### 苗条套装

如果你有一个 JavaScript 或 TypeScript 文件而不是一个 Svelte 文件，导出一个函数，它将被当作一个 API 路由。函数的名称将决定它是对哪种方法的响应:

```
export async function get({ params }) {
        return {
          body: {id: params.id}
        }
}

```

## 式样

谈到样式，这三个框架可以有很大的不同。

### 再搅拌

Remix 有一种内置的方式，通过链接标签链接传统的 CSS 样式表，方法是在`pages` JavaScript 文件中导出一个链接函数。如果链接标签出现在根目录或模板中，页面的链接标签将在后面插入。因此，您不需要将所有的 CSS 都显示在每个页面上来优化每页发送的 CSS 数量:

```
export function links() {
  return [
    {
      rel: "stylesheet",
      href: "https://unpkg.com/[email protected]/dist/reset.min.css"
    }
  ];
}

```

### Next.js

您也可以使用`helmet`组件来添加链接标签，但是您也可以使用 s[styled-components、JSS、Emotion、Sass 或任何其他 CSS 抽象](https://blog.logrocket.com/best-styling-options-nextjs/)来导入标准 CSS 样式表。

### 苗条套装

像 Vue 一样，Svelte 使用单文件组件，所以 CSS 在组件文件中。

## 错误处理

尽管您尽了最大努力来发布高性能、无错误的应用程序，但错误还是会发生。网络不能在你的`localhost`上运行，因此你需要为这些错误做好准备，并在它们出现时处理它们。

### 再搅拌

Remix 内置了对服务器和客户端渲染的错误处理支持。错误边界不会阻止整个页面的呈现，因为它们只替换视图中发生错误的部分。

重新混合中的错误在组件树中冒泡，直到它们找到一个错误边界来呈现它们:

```
import { ErrorBoundaryComponent } from "remix";

export const ErrorBoundary: ErrorBoundaryComponent = ({ error }) => {
  return (
    <div>
      <p>An error occured!</p>
      <pre className="mt-4">{error.message}</pre>
    </div>
  );
};

```

### Next.js

为了处理错误， [Next.js 使用 React 的`ErrorBoundary`组件](https://blog.logrocket.com/react-error-handling-react-error-boundary/):

```
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props)
    // Define a state variable to track whether is an error or not
    this.state = { hasError: false }
  }

  static getDerivedStateFromError(error) {
    // Update state so the next render will show the fallback UI
    return { hasError: true }
  }

  componentDidCatch(error, errorInfo) {
    // You can use your own error logging service here
    console.log({ error, errorInfo })
  }

  render() {
    // Check if the error is thrown
    if (this.state.hasError) {
      // You can render any custom fallback UI
      return (
        <div>
          <h2>Oops, there is an error!</h2>
          <button
            type="button"
            onClick={() => this.setState({ hasError: false })}
          >
            Try again?
          </button>
        </div>
      )
    }

    // Return children components in case of no error
    return this.props.children
  }
}

export default ErrorBoundary

```

`ErrorBoundary`组件跟踪一个`hasError`状态变量，这是一个布尔值。如果有错误，`ErrorBoundary`组件将呈现一个回退 UI，如果没有，它将呈现其子组件。

### 苗条套装

与 Remix 和 Next.js 不同，SvelteKit 没有任何形式的`ErrorBoundary`组件来处理错误。相反，您必须通过创建一个`+error.svelte`来处理错误，它将捕获并呈现错误。

Sveltekit 中的 Error 对象包含一个带有`string`值的`message`属性，它提供了关于错误性质的更多细节。

您可以使用从`@sveltejs/kit`导入的`[error](https://kit.svelte.dev/docs/modules#sveltejs-kit-error)`助手创建错误。考虑这样一个场景，您想要获取一篇文章的详细信息，但是没有找到:

```
export async function load({ params }) {
  const post = await db.getPost(params.slug);

  if (!post) {
    throw error(500, {
      message: 'An error occured!'
    });
  }

  return { post };
}

```

`+error.svelte`组件将自动捕捉错误并显示出来:

```
<script>
  import { page } from '$app/stores';
</script>

<h1>{$page.error.message}</h1> 

```

## 部署

### 再搅拌

像 [Fly](https://fly.io/docs/languages-and-frameworks/remix/) 、 [Vercel](https://vercel.com/guides/deploying-remix-with-vercel) 、 [Render](https://render.com/docs/deploy-remix) 、 [Netlify](https://www.netlify.com/blog/how-to-deploy-remix-apps-on-netlify/) 和 [Cloudflare](https://developers.cloudflare.com/pages/framework-guides/remix/) 这样的几个平台提供了涵盖如何部署 Remix 应用的演练指南。Remix 还[提供了几个模板](https://remix.run/docs/en/v1/guides/deployment)，您可以使用 CLI 利用它们进行快速部署。

### Next.js

Next.js 背后的创造者 Vercel 通常是部署 Next.js 应用的首选。Next.js 提供了更大的部署灵活性，因为它[可以部署到任何支持 Node.js 的托管提供商](https://nextjs.org/learn/basics/deploying-nextjs-app/other-hosting-options)，其中一些托管提供商包括 Netlify、Cloudflare、Render、AWS 和 Heroku。注意 [Next.js 比 SvelteKit 和 Remix 有更多的部署选项](https://blog.logrocket.com/choosing-best-nextjs-hosting-platform/)。

### 苗条套装

Vercel 和 [Render](https://render.com/docs/deploy-sveltekit) 为从 GitHub、GitBucket 和 GitLab 快速部署 SvelteKit 应用程序提供直接、一流的支持。对于其他平台，您可以利用 SvelteKit 的[适配器 API](https://kit.svelte.dev/docs/adapters) 来优化部署。 [Netlify](https://docs.netlify.com/integrations/frameworks/sveltekit/#automatic-framework-detection) 和 [Cloudflare](https://developers.cloudflare.com/pages/framework-guides/deploy-a-svelte-site/) 提供了如何使用适配器部署 SvelteKit 应用程序的指南。

## 混音真正的区别在哪里？

Remix 在处理表单的方式上确实有独特而有价值的不同。在现代框架中，我们已经放弃了表单的传统功能，而不是从 JavaScript 内部劫持流程。对于那些很久以前开发 web 应用程序的人来说，您可能还记得这样的表单:

```
<form method="post" action="/user/new">
        <input type="text" name="username">
        <input type="password" name="password">
        <input type="submit" value="new user">
</form>

```

请求方法和发出请求的位置都完全在表单中定义，所以不需要`onSubmit`处理程序或`preventDefault`。在我们的例子中，它工作正常，因为我们有 Perl 或 PHP 脚本在另一端等待处理该请求。混音有一个自定义的`Form`组件，包含了这种体验的感觉。

下面是来自 [Remix 文档](https://remix.run/docs/en/v1/guides/data-writes#building-the-form)的示例组件，展示了`Form`组件的运行:

```
import type { ActionFunction } from "remix";
import { redirect } from "remix";

// Note the "action" export name, this will handle our form POST
export const action = async ({
  request
}) => {
  const formData = await request.formData();
  const project = await createProject(formData);
  return redirect(`/projects/${project.id}`);
};

export default function NewProject() {
  return (
    <form method="post" action="/projects/new">
      <p>
        <label>
          Name: <input name="name" type="text" />
        </label>
      </p>
      <p>
        <label>
          Description:
          <br />
          <textarea name="description" />
        </label>
      </p>
      <p>
        <button type="submit">Create</button>
      </p>
    </form>
  );
}

```

当提交表单时，它会向`/projects/new`发出一个`POST`请求。前者将使用导出操作函数进行处理，然后重定向到正确的路由。旧的又是新的！

## 结论

如果您想带来一些老式、全栈的 web 应用程序的感觉，但仍然想在需要时在客户端获得 React 的好处，Remix 是元框架的一个很好的选择。

虽然缺乏静态预渲染使得它很难用于某些用例，如博客和营销渠道，但 Remix 对于具有大量动态内容的网站来说是一个强大的补充。

有一点是肯定的，目前在前端框架领域寻找创新的地方实际上是在元框架中，如 Remix、Next.js、SvelteKit、Nuxt.js、Gatsby、SolidStart、Gridsome 等。我们甚至正在进入一个元-元框架存在的时代。我在看你， [Blitz.js](https://blitzjs.com/) ，建立在 Next.js 之上。