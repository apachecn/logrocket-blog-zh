# 用 Node.js - LogRocket Blog 创建一个开放的图形图像生成器

> 原文：<https://blog.logrocket.com/create-open-graph-image-generator-node-js/>

当我第一次遇到开放图(OG)图像时，我认为它们只是我们共享链接时出现的一种装饰性协议。没过多久，我就意识到 OG 图片对公共平台上共享的任何资源或网站都有很大的影响。

当图像与标题和描述元数据结合时，它们提供了关于共享资源的快速信息。例如，当我们在 Twitter 上分享一个链接时，元数据被解析并生成一个预览卡。

快速浏览一下，预览卡甚至在访问链接之前就提供了关于共享资源的信息。现在，如果没有可用的元数据，就不会生成预览，链接也会被截断，不会留下关于资源的有用信息。

![Screenshot Of Tweet Comparison With And Without Preview Card](img/035d16959ae828850de0773e475af9af.png)

然而，为许多页面或博客创建 OG 图像非常耗时。更好的方法是为各个类别设计一些模板，并使用简单的图像生成器服务动态创建图像。

在这篇文章中，我们将建立一个简单的服务器，它带有一个端点`/ogimage`,可以根据提供的查询参数动态生成图像。主要目的是减少创建 OG 图像时的手动工作。

出于本文的考虑，我们将使用 Node.js 和 Express 来设置服务器，并使用几个 npm 包来处理动态图像生成。请随意使用适合您偏好的工具。

那么，事不宜迟，让我们开始吧…

## 什么是开放图？

我们先来了解一下 OG 协议是什么。根据 [opg.me](https://ogp.me/) ，“开放图协议使得任何网页都可以成为社交图中的丰富对象。它提供了足够的信息来丰富地表现社交图中的任何网页。”

可在社会上共享的信息片段是通过元标签定义的。这些标签然后被 OG 机制分组，以在社交媒体上提供共享资源的预览。

在本帖中，我们将更多地关注`og:image`，以了解更多关于其他元标签(如`og:title`或`og:description`)和开放图协议本身的信息，[请参考这篇有见地的文章](https://blog.logrocket.com/open-graph-sharable-social-media-previews/)。

下面是构建 Node.js 驱动的 OG 图像生成器所需的步骤:

1.  设置`ogimage`终点
2.  创建图像模板
3.  使用木偶师生成图像
4.  保存并提供图像作为响应

## 设置`ogimage`终点

首先，让我们创建[一个简单的 Node.js 和 Express 应用程序](https://blog.logrocket.com/express-middleware-a-complete-guide/)，它只有一个`GET`端点`/ogimage`。进入`ogimage`的所有数据都来自 URL 的查询参数:

```
# Create a new directory and cd into it
mkdir og-imager
cd og-imager

# initialize npm
npm init
# or use "npm init -y" to initialize with default values

# add express
npm install express

```

接下来，创建一个`index.js`文件并添加下面的代码片段。这将导入并初始化一个 Express 应用程序，设置一个`GET /ogimage`端点，并监听请求:

```
// Import and initialize the express app
const express = require('express');
const app = express();
const port = process.env.PORT || 3000;

// setup GET endpoint
app.get('/ogimage', (req, res) => {
  res.send('OG Imager!');
});

// Listen for requests
app.listen(port, () => {
  console.log(`app listening at ${port}`)
});

```

我们现在可以将启动脚本添加到`package.json`来启动应用程序。使用 [nodemon](https://nodemon.io/) 进行本地开发，以便在发生更改时自动重新加载节点服务器:

```
# add nodemon as dev-dependency
npm install nodemon -D

```

```
# add start scripts
"scripts": {
  "start": "node index.js",
  "dev": "nodemon index.js"
},

```

启动服务器(`npm run start` / `npm run dev`)，当`[http://localhost:3000/ogimage](http://localhost:3000/ogimage)`加载时，我们应该会在浏览器上看到`OG Imager!`。

## 创建图像模板

图像模板是一个简单的 HTML 标记，带有一些占位符和 CSS 样式。占位符在[手柄语法](https://handlebarsjs.com/)，`{{placeholder}}`中，但是我们将在下一节中对此进行更多的讨论。

更简单地说，我们希望创建一个简单的 HTML 页面，并将页面捕获为具有相应维度的图像。下面是我们可以使用的示例模板的标记。请随意修改 HTML 和 CSS，因为你认为适合你自己的博客/应用程序:

```
const templateHTML = `
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    <style>{{styles}}</style>
  </head>
  <body id="body">
    <main>
      <div class='logo'>
        {{#if logoUrl}}
          <img src="{{logoUrl}}" alt="logo" />
        {{else}}
          <span>Example Logo</span>
        {{/if}}
      </div>
      <div class="title">{{title}}</div>
      <div>
        {{#if tags}}
          <ul class="tags">
          {{#each tags}}
            <li class="tag-item">#{{this}}</li>
          {{/each}}
          </ul>
        {{/if}}
        {{#if path}}
          <p class="path">{{path}}</p>
        {{/if}}
      </div>
    </main>
  </body>
</html>
`;

```

现在，让我们为模板添加样式。与 HTML 类似，CSS 将为动态内容提供占位符，如背景图像或标题字体大小:

```
const templateStyles = `
@font-face {
  font-family: Source Code Pro;
  src: url(https://fonts.googleapis.com/css2?family=Source+Code+Pro:[email protected]&display=swap);
}
* {
  box-sizing: border-box;
}
:root {
  font-size: 16px;
  font-family: Source Code Pro, monospace;
}
body {
  padding: 2.5rem;
  height: 90vh;
  background: #042f7d;
  {{#if bgUrl}}
  background-image: url({{bgUrl}});
  background-position: center;
  background-repeat: no-repeat;
  background-size: cover;
  {{else}}
  background: linear-gradient(to right, #042f7d, #007eff);
  color: #00ffae;
  {{/if}}
}
main {
  height: 100%;
  width: 100%;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}
.logo {
  width: 15rem;
  height: 3rem;
}
.logo img {
  width: 100%;
  height: 100%;
}
.logo span {
  font-size: 2rem;
  color: yellow;
  font-style: italic;
  text-decoration: wavy;
  font-variant: unicase;
}
.title {
  font-size: {{fontSize}};
  text-transform: capitalize;
  margin: 0.25rem 0;
  font-weight: bold;
}
.tags {
  display: flex;
  list-style-type: none;
  padding-left: 0;
  color: #ff00d2;
  font-size: 1.5rem;
}
.tag-item {
  margin-right: 0.5rem;
}
.path {
  color: #6dd6ff;
  font-size: 1.25rem;
}
`;

```

现在我们已经准备好了模板，下一步是从它生成一个图像。

## 使用木偶师生成图像

要从服务器上的 HTML 模板生成图像，可以启动一个无头浏览器，用模板中的 HTML 和 CSS 加载所需视口尺寸的页面。然后，加载的页面被捕获并保存/用作图像。

我们将使用 [Puppeteer 来启动无头浏览器，并对从上面创建的模板中加载的页面](https://blog.logrocket.com/creating-visuals-of-your-webpage-with-puppeteer/)进行截图。我们还需要手柄来编译模板化的 HTML 和 CSS，并用动态值替换占位符:

```
npm install puppeteer handlebars

```

### 用把手编译模板 html

在启动浏览器并捕获页面之前，让我们编译必须加载到页面中的模板 HTML:

```
const Handlebars = require("handlebars");

// Get dynamic font size for title depending on its length
function getFontSize(title="") {
  if (!title || typeof title !== 'string') return "";
  const titleLength = title.length;
  if (titleLength > 55) return "2.75rem";
  if (titleLength > 35) return "3.25rem";
  if (titleLength > 25) return "4.25rem";
  return "4.75rem";
}

// compile templateStyles
const compiledStyles = Handlebars.compile(templateStyles)({
  bgUrl: req.query.bgUrl,
  fontSize: getFontSize(req.query.title),
});

// compile templateHTML
const compiledHTML = Handlebars.compile(templateHTML)({
  logoUrl: req.query.logoUrl,
  title: req.query.title,
  tags: req.query.tags,
  path: req.query.path,
  styles: compiledStyles,
});

```

请注意，把手将[逃离不安全的 HTML](https://handlebarsjs.com/guide/#html-escaping) 。因此，只要我们的占位符带有`{{double-stash}}`，直接传递查询字符串值就是安全的。产生的 HTML 和样式将具有查询字符串接收的动态值。

### 加载 HTML 并用木偶师捕捉

接下来是旋转浏览器，用 Puppeteer 截取页面的截图。默认情况下，Puppeteer 将视口设置为`800x600`(在撰写本文时)。但是，这可以被启动方法时发送的`defaultViewport`属性覆盖:

```
 const puppeteer = require('puppeteer');
// ...
app.get('/ogimage', async (req, res) => { // Note the async
  // ...
  const browser = await puppeteer.launch({
    headless: true,
    args: ["--no-sandbox"],
    defaultViewport: {
      width: 1200,
      height: 630,
    }
  });
  const page = await browser.newPage();
  // ...
});

```

`1200x630`是 OG 图像最常见的尺寸。还可以通过使用`page.setViewport`从请求参数中设置值来动态控制视窗大小:

```
await page.setViewport({ width: Number(req.query.width), height: Number(req.query.height) });

```

接下来，将编译后的 HTML 设置为页面内容，并通过将`waitUntil`属性设置为`networkidle0`等待至少 500 毫秒，直到没有网络请求。这种等待确保了所有图像和内容的加载:

```
 await page.setContent(compiledHTML, { waitUntil: 'networkidle0' });

```

等一下，设置`networkidle0`表示每次等待 500ms。我们如何解决这个问题？

为了寻找答案，我在 GitHub 中找到了一个用于构建开放图形图像的框架[。](https://github.blog/2021-06-22-framework-building-open-graph-images/#some-performance-gotchas)

在文章中，Jason Etcovitch 写道，“我们将`waitUntil`改为`[domcontentloaded](https://developer.mozilla.org/en-US/docs/Web/API/Window/DOMContentLoaded_event)`以确保 HTML 已经完成解析，然后将一个自定义函数传递给`[page.evaluate](https://pptr.dev/#?product=Puppeteer&version=v8.0.0&show=api-pageevaluatepagefunction-args)`。

这[运行]在页面本身的上下文中，但是通过管道将返回值传递给外部上下文。这意味着我们可以监听图像加载事件并暂停执行，直到承诺得到兑现。”

下面的片段直接摘自这篇博文，旨在解决这个问题:

```
 // Set the content to our rendered HTML
 await page.setContent(compiledHTML, { waitUntil: "domcontentloaded" });

 // Wait until all images and fonts have loaded
 await page.evaluate(async () => {
   const selectors = Array.from(document.querySelectorAll("img"));
   await Promise.all([
     document.fonts.ready,
     ...selectors.map((img) => {
       // Image has already finished loading, let’s see if it worked
       if (img.complete) {
         // Image loaded and has presence
         if (img.naturalHeight !== 0) return;
         // Image failed, so it has no height
         throw new Error("Image failed to load");
       }
       // Image hasn’t loaded yet, added an event listener to know when it does
       return new Promise((resolve, reject) => {
         img.addEventListener("load", resolve);
         img.addEventListener("error", reject);
       });
     }),
   ]);
 });

```

所以，我们可以用`page.screenshot`对加载的页面上的 body 元素(可见的内容包装器)进行截图，并发送`omitBackground: true`属性忽略浏览器背景，只对加载的内容进行截图。

但是，如果没有设置背景属性，生成的屏幕截图将具有透明背景，而不是白色的浏览器默认背景:

```
const element = await page.$('#body');
const image = await element.screenshot({ omitBackground: true });  
await browser.close();

```

仅此而已；我们已经生成了一个图像，最后一步是提供图像。

## 保存/提供图像作为回应

为了保存/提供图像，我们必须首先设置`Content-Type`头以指示`ogimage`端点用图像响应，因此不需要额外的逻辑来处理响应。

我们可以直接使用端点作为图像 URL，并为缓存目的设置`Cache-Control`头:

```
app.get('/ogimage', (req, res) => {
  // Compile Template HTML & CSS with Handlebars
  .....
  // Load the template and take a screenshot with Puppeteer
  .....

  res.writeHead(200, {
    'Content-Type': 'image/png',
    'Cache-Control': `immutable, no-transform, s-max-age=2592000, max-age=2592000` // 30 days cache
  });
  res.end(image);
});

```

要在本地加载图像预览，请打开浏览器并使用查询参数访问位于`localhost:3000/ogimage`的`ogimage`端点。这会向服务发送一个`GET`请求，并在浏览器中显示图像响应:

```
http://localhost:3000/ogimage?title=Open%20Graph%20Image%20Generator%20with%20NodeJS&tags[]=nodejs&tags[]=og-image&path=blog.yourdomain.com/open-graph-image-generator-with-nodejs

```

图像预览如下所示:

![Dynamically Generated OG Image Preview](img/a89daafd04c7d9db3111aaec17dd4f2b.png)

这是最后的代码:

```
// index.js
const express = require('express');
const puppeteer = require('puppeteer');
const Handlebars = require("handlebars");

const app = express();
const port = process.env.PORT || 3000;

const templateStyles = `...`;

const templateHTML = `...`;

// Get dynamic font size for title depending on its length
function getFontSize(title="") {
  if (!title || typeof title !== 'string') return "";
  const titleLength = title.length;
  if (titleLength > 55) return "2.75rem";
  if (titleLength > 35) return "3.25rem";
  if (titleLength > 25) return "4.25rem";
  return "4.75rem";
}

app.get('/ogimage', async (req, res) => {
  // compiled styles
  const compiledStyles = Handlebars.compile(templateStyles)({
    bgUrl: req.query.bgUrl,
    fontSize: getFontSize(req.query.title),
  });
  // compiled HTML
  const compiledHTML = Handlebars.compile(templateHTML)({
    logoUrl: req.query.logoUrl,
    title: req.query.title,
    tags: req.query.tags,
    path: req.query.path,
    styles: compiledStyles,
  });
  // Launch Headless browser and capture creenshot
  const browser = await puppeteer.launch({
    headless: true,
    args: ["--no-sandbox"],
    defaultViewport: {
      width: 1200,
      height: 630,
    }
  });
  const page = await browser.newPage();
  // Set the content to our rendered HTML
  await page.setContent(compiledHTML, { waitUntil: "domcontentloaded" });  
  // Wait until all images and fonts have loaded
  await page.evaluate(async () => {
    const selectors = Array.from(document.querySelectorAll("img"));
    await Promise.all([
      document.fonts.ready,
     ...selectors.map((img) => {
        // Image has already finished loading, let’s see if it worked
        if (img.complete) {
          // Image loaded and has presence
          if (img.naturalHeight !== 0) return;
          // Image failed, so it has no height
          throw new Error("Image failed to load");
        }
        // Image hasn’t loaded yet, added an event listener to know when it does
        return new Promise((resolve, reject) => {
          img.addEventListener("load", resolve);
          img.addEventListener("error", reject);
        });
      }),
    ]);
  });

  const element = await page.$('#body');
  const image = await element.screenshot({ omitBackground: true });  
  await browser.close();

  res.writeHead(200, { 'Content-Type': 'image/png', 'Cache-Control': `immutable, no-transform, s-max-age=2592000, max-age=2592000` });
  res.end(image);
})

app.listen(port, () => {
  console.log(`app listening at ${port}`)
});

```

你也可以在 [GitHub](https://github.com/KRRISH96/og-imager/) 上找到完整的代码。请随意使用它，并扩展到模板之外，以满足您的需求。

一个很好的开发技巧是注释掉木偶师和内容类型头代码，然后发送`compiledHTML`作为响应，而不是发送生成的图像`res.status(200).send(compiledHTML)`:

```
// compiled HTML
  const compiledHTML = ...;

  // Comment out puppeteer, browser, page stuff
  // const browser = ...;
  // ...
  // await browser.close();

  // instead of image as response, send compiledHTML itself
  // res.writeHead(200, { 'Content-Type': 'image/png', 'Cache-Control': `immutable, no-transform, s-max-age=2592000, max-age=2592000` });
  // res.end(image);
  res.status(200).send(compiledHTML);

```

这将绕过图像生成，并通过快速迭代模板的 UI，在浏览器中呈现结果 HTML，从而加快开发过程。

要在元标记内进行链接，请添加包含动态 URL 的元图像标记作为内容。加载时，此 URL 将解析为预览中的图像。

`og:image`是 OG 图像的主要元标签。除了目标平台，你还可以添加 Twitter、Instagram 和任何其他特定于社交媒体的标签:

```
<meta property=”og:image” content=”https://{{your_domain.com}}/ogimage?title=Open%20Graph%20Image%20Generator%20with%20NodeJS&tags[]=nodejs&tags[]=og-image&path=blog.yourdomain.com/open-graph-image-generator-with-nodejs&logoUrl={{your_logo_url}}”>

```

请注意，您可能需要对查询字符串进行 URL 转义；可以用`[encodeURI](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/encodeURI)`。

## 结论

好了，我们有自己的 OG 图像生成器服务，可以为每个页面/博客帖子动态创建图像。

您还可以选择该服务的各个部分(模板、[手柄编译](https://blog.logrocket.com/data-visualization-d3-js-node-js/)、木偶师截图)来组装一个无服务器功能，或者在任何前端应用程序的构建过程中将其用作一个实用程序。

这篇文章是实现这一目标的众多方法之一。总的来说，上下文保持不变；变化的是语法/语言😛。

此外，生成的图像可以存储在 AWS S3、GCS 或任何符合您需求的服务中，并且可以在后续请求中使用该存储以节省生成时间。您还可以使用内存中的缓存，每隔一定天数进行缓存失效。

感谢您的阅读。我希望这篇文章对你有所帮助，请与那些可能从中受益的人分享。再见。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.