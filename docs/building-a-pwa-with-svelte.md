# 用苗条的博客建立一个 PWA

> 原文：<https://blog.logrocket.com/building-a-pwa-with-svelte/>

与本地应用程序类似，渐进式 web 应用程序(pwa)是在移动设备上运行 web 应用程序的一种流行解决方案。通过 PWAs，用户可以像体验移动应用程序一样轻松和熟悉地体验 web 应用程序；类似地，公司能够直接在互联网上托管应用，而不是在不同的移动应用商店。

在本教程中，我们将使用 Svelte 创建自己的 PWA，这是一个基于命令式和声明式应用程序的现代框架。使用 React 这样的传统框架，您必须单独构建应用程序的所有部分；然而，Svelte 只需少量代码就可以预渲染应用程序的构建。

首先，我们将通过在 Svelte 中构建我们自己的来探索 pwa 是如何工作的。然后，我们将看看 Sapper 和 SvelteKit 的一些功能，您可以使用它们将 PWA 功能引入您的应用程序。我们将引用一个示例项目，可以在 GitHub 上的 my repo 中访问。我们开始吧！

## PWA 功能

在构建我们自己的之前，让我们仔细看看 PWAs 是如何工作的。以下两个特性[与渐进式增强](https://blog.logrocket.com/a-quick-introduction-to-progressive-enhancement/)协同工作，创建类似于原生应用的体验。

服务工作者充当 web 应用程序的中介或代理，允许使用缓存资源并优雅地处理糟糕的互联网连接。

例如，当互联网连接丢失时，我们可能会使用服务人员向用户显示消息，以便应用程序不会突然崩溃。同样，服务人员可能会让我们的应用程序保存本地活动，并在恢复互联网连接后重新同步。

Web 清单使用户能够在特定平台上下载或安装应用程序。用户在浏览器中看到的应用程序可以离线运行，也可以在类似于本机实现的状态下运行。尽管清单仍被认为是实验性的，但它们得到了现代浏览器的大力支持。

## 建立一个苗条的 PWA

现在我们已经了解了定义 PWAs 的特性，让我们使用 Svelte 来构建我们自己的特性。本节中的示例基于来自 [GitHub](https://github.com/tretapey/svelte-pwa) 的示例。

首先，在您的终端上运行下面的代码，创建一个新的瘦项目:

```
npx degit sveltejs/template svelte-pwa

```

接下来，我们将安装 JavaScript 依赖项。导航到您刚刚创建的目录，并运行下面的代码:

```
cd svelte-pwa
npm install

```

当我们使用`degit`创建一个新项目时，Svelte 搭建了一个基本的应用程序，其结构如下:

![Scaffolded Svelte App Structure](img/58cd747e0ddf3046ecd8a965155bfd83.png)

我们将主要处理`src`目录中的文件。`App.svelte`文件是我们项目的入口点，包含以下代码:

```
&lt;script>
    export let name;
</script>
<main>
    <h1>Hello {name}!</h1>
    <p>Visit the <a href="https://svelte.dev/tutorial">Svelte tutorial</a> to learn how to build Svelte apps.</p>
</main>
<style>
    main {
        text-align: center;
        padding: 1em;
        max-width: 240px;
        margin: 0 auto;
    }
    h1 {
        color: #ff3e00;
        text-transform: uppercase;
        font-size: 4em;
        font-weight: 100;
    }
    @media (min-width: 640px) {
        main {
            max-width: none;
        }
    }
</style>

```

`.svelte`文件有单独的部分用于样式、视图`html`和任何 JavaScript 代码`<scripts>`。

将以下代码添加到您的`main.js`文件中以引导您的应用程序，并告诉捆绑器使用您的`.svelte`应用程序文件构建项目:

```
import App from './App.svelte';
const app = new App({
    target: document.body,
    props: {
        name: 'world'
    }
});
export default app;

```

要创建 PWA，首先，我们将在`public`目录中的`service-worker.js`文件内创建一个服务工作者:

```
"use strict";
// Cache Name
const CACHE_NAME = "static-cache-v1";
// Cache Files
const FILES_TO_CACHE = ["/offline.html"];
// install
self.addEventListener("install", (evt) => {
  console.log("[ServiceWorker] Install");
  evt.waitUntil(
    caches.open(CACHE_NAME).then((cache) => {
      console.log("[ServiceWorker] Pre-caching offline page");
      return cache.addAll(FILES_TO_CACHE);
    })
  );
  self.skipWaiting();
});
// Active PWA Cache and clear out anything older
self.addEventListener("activate", (evt) => {
  console.log("[ServiceWorker] Activate");
  evt.waitUntil(
    caches.keys().then((keyList) => {
      return Promise.all(
        keyList.map((key) => {
          if (key !== CACHE_NAME) {
            console.log("[ServiceWorker] Removing old cache", key);
            return caches.delete(key);
          }
        })
      );
    })
  );
  self.clients.claim();
});
// listen for fetch events in page navigation and return anything that has been cached
self.addEventListener("fetch", (evt) => {
  console.log("[ServiceWorker] Fetch", evt.request.url);
  // when not a navigation event return
  if (evt.request.mode !== "navigate") {
    return;
  }
  evt.respondWith(
    fetch(evt.request).catch(() => {
      return caches.open(CACHE_NAME).then((cache) => {
        return cache.match("offline.html");
      });
    })
  );
});

```

在上面的代码块中，我们注册了由服务工作者处理的事件，包括`install`和`fetch`事件。为了处理项目的离线使用，我们将缓存导航中使用的文件并记录我们缓存的内容。使用 PWAs，您可以只缓存需要的内容，从而提高您的 UX。

接下来，我们将创建一个`offline.html`文件，它将被缓存到导航中。在`public`目录中添加以下代码:

![Svelte Cache Public Directory](img/a2436ad59fc7d4c66a05f74e6d5c841e.png)

现在，让我们在`public`目录中创建一个`manifest.json`文件:

![Svelte Manifest JSON](img/b58c96133c67668ff84bc5876ac15ed2.png)

您可以使用`icons`条目为不同的设备设置安装图标，但是，为了简单起见，我将其留空。

现在，当 Svelte 构建您的应用程序时，它将读取这些文件，并创建一个运行的服务工作器，每当检测到离线活动时，它都会缓存一个导航资源。运行`npm run dev`将通过服务人员启动您的应用程序。如果你在 Chrome 中打开 DevTools，你会看到服务人员启动:

![Svelte App Run Service Worker](img/747beee54af846f73e929566f22c7476.png)

要查看服务人员的工作情况，您也可以进入`network`选项卡，将您的会话移至离线状态:

![Svelte Service Worker Offline](img/332c121fb2a05ee67e7488c314feb4cb.png)

![Svelte Final Service Worker](img/80ccfcacff194c9f6582a859e54bd8f4.png)

## 在 SvelteKit 中构建 PWA

现在，让我们使用 SvelteKit 创建一个 PWA。对于这个例子，我们将创建一个“Hello，World！”申请。在您的终端中运行以下代码:

```
npm init [email protected] sveltekit-pwa

```

CLI 将询问您是否想要创建演示应用程序或框架项目。选择**演示应用**:

![Svelte Sapper CLI Demo App](img/43d944ceb5120d0a78dc3f6548250526.png)

CLI 还会询问您是否愿意使用 TypeScript、Prettier 或 ESLint。添加以下配置:

![Svelte Sapper TypeScript Prettier Eslint Configuration](img/c9cedefc76d15d2c34859121898995ed.png)

现在，进入 SvelteKit 项目目录，用下面的代码安装所需的依赖项:

```
cd sveltekit-pwa
npm install

```

要运行您的项目，请添加以下代码:

```
npm run dev -- --open

```

![New SvelteKit App Homepage](img/3db9dfdb0b21228188edcff08c760e2f.png)

现在我们的项目已经创建好了，我们可以使用与创建基本的苗条 PWA 相同的步骤来创建一个 PWA。在`static`目录下创建一个`manifest.json`文件:

![SvelteKit PWA Manifest Json](img/01580b66e625298f56cf6e965818dc64.png)

接下来，修改`src`目录中的`app.html`文件，以包含对`manifest.json`文件的引用:

![Reference Manifest Json File](img/f220d397d66af51d4ca330b5083243b7.png)

最后，在`src`目录下创建一个`service-worker.js`文件:

```
import { build, files, timestamp } from '$service-worker';
const worker = (self as unknown) as any;
const FILES = `cache${timestamp}`;
const to_cache = build.concat(files);
const staticAssets = new Set(to_cache);
// listen for the install events
worker.addEventListener('install', (event) => {
    event.waitUntil(
        caches
            .open(FILES)
            .then((cache) => cache.addAll(to_cache))
            .then(() => {
                worker.skipWaiting();
            })
    );
});
// listen for the activate events
worker.addEventListener('activate', (event) => {
    event.waitUntil(
        caches.keys().then(async (keys) => {
            // delete old caches
            for (const key of keys) {
                if (key !== FILES) await caches.delete(key);
            }
            worker.clients.claim();
        })
    );
});
// attempt to process HTTP requests and rely on the cache if offline
async function fetchAndCache(request: Request) {
    const cache = await caches.open(`offline${timestamp}`);
    try {
        const response = await fetch(request);
        cache.put(request, response.clone());
        return response;
    } catch (err) {
        const response = await cache.match(request);
        if (response) return response;
        throw err;
    }
}
// listen for the fetch events
worker.addEventListener('fetch', (event) => {
    if (event.request.method !== 'GET' || event.request.headers.has('range')) return;
    const url = new URL(event.request.url);
    // only cache files that are local to your application
    const isHttp = url.protocol.startsWith('http');
    const isDevServerRequest =
        url.hostname === self.location.hostname && url.port !== self.location.port;
    const isStaticAsset = url.host === self.location.host && staticAssets.has(url.pathname);
    const skipBecauseUncached = event.request.cache === 'only-if-cached' && !isStaticAsset;
    if (isHttp && !isDevServerRequest && !skipBecauseUncached) {
        event.respondWith(
            (async () => {
                // always serve static files and bundler-generated assets from cache.
                // if your application has other URLs with data that will never change,
                // set this variable to true for them and they will only be fetched once.
                const cachedAsset = isStaticAsset && (await caches.match(event.request));
                return cachedAsset || fetchAndCache(event.request);
            })()
        );
    }
});

```

完成我们的 SvelteKit PWA 后，我们可以将它连接到互联网并查看它的运行情况:

![SvelteKit PWA Internet Connection](img/0134fbbfdf967e4059e0acd15814934f.png)

如果没有互联网连接，我们的应用程序将如下图所示:

![SvelteKit PWA Without Internet](img/2b9a2b5d627da091f5bc26df8c4f6439.png)

尽管有明显的 HTTP 错误，该应用程序能够优雅地处理与互联网断开连接。

## 在 Sapper 建造一个 PWA

> 如[Sapper 官方文档](https://sapper.svelte.dev/docs/#Introduction)所述:Sapper 的继承者 SvelteKit 目前已经可以使用。所有向前发展的努力将集中在 SvelteKit 上。Sapper 文档包括一份有用的[移民指南](https://sapper.svelte.dev/migrating/)。如果你喜欢用 Sapper 构建你的应用程序，尽管缺乏支持，请继续阅读:

Sapper 允许你像使用 Svelte 一样开发你的应用程序，但是，它有支持 PWAs 的额外好处。通过在终端中运行以下代码创建一个 Sapper 应用程序:

```
npx degit "sveltejs/sapper-template#rollup" sapper-pwa

```

通过运行以下代码安装所需的依赖项:

```
cd sapper-pwa
npm install

```

现在，要查看运行中的 Sapper 应用程序，添加本地 run 命令，如下所示:

```
npm run dev
```

![Svelte Sapper PWA Template](img/b7851528c90ed2fc1693d9faca6a6679.png)

查看生成的代码，您会看到项目已经包含了一个服务工作器和一个清单文件:

![Svelte Sapper Service Worker Manifest File](img/d584d9fa89dc123fbe494e3d28813b53.png)

通过这个过程，你基本上可以像开发 Svelte 一样开发一个 Sapper 应用程序。

## 结论

在本文中，我们探讨了构建 pwa 的好处，并研究了如何使用服务工作者和 web 清单来构建它们。然后，我们探索了使用 Svelte、SvelteKit 和 Sapper 构建 PWAs 的三种方法。

由于本地应用程序的流行，pwa 的使用可能会继续增长。幸运的是，当您使用像 Svelte 这样的现代框架时，构建 PWA 相当简单。我希望你喜欢这个教程！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)