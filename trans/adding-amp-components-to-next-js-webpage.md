# 将 AMP 组件添加到 Next.js 网页- LogRocket 博客

> 原文：<https://blog.logrocket.com/adding-amp-components-to-next-js-webpage/>

加速移动网页，或称 [AMP](https://amp.dev) ，是一个开源的 web 组件框架，可以提高网页的加载速度。AMP 对网页的 HTML、CSS 和 JavaScript 施加了严格的指导原则，控制媒体、广告和其他脚本等外部资源的加载方式。

在本教程中，我们将浏览 AMP 的一些准则，学习如何管理出现的一些权衡。我们将介绍如何在网页上显示消耗资源的元素，同时通过在一个 [Next.js](https://blog.logrocket.com/new-features-in-next-js-11/) 项目中使用 AMP 组件来遵守 AMP 的规则。

如果你想继续学习，本教程的所有代码都可以在我的 [GitHub](https://github.com/JurnW/nextjs-amp-demo) 上找到。我们开始吧！

## AMP 的局限性

AMP 通过阻止开发人员使用包含图像、视频和音频等资源的自定义元素来优化网页加载。同样，开发人员不能为显示动态广告、嵌入社交媒体帖子和创建交互式表单等任务编写自定义 JavaScript。为了解决这些限制，我们将使用放大器元件。

## 放大器组件

AMP 组件是自定义编写的 HTML 片段，我们可以使用它向我们的网页添加复杂的功能，同时仍然符合 AMP 的标准。

目前， [AMP 组件目录](https://amp.dev/documentation/components/)包括许多常见任务的内置组件，如添加图像、计算网页分析和显示广告。因为内置组件包含在 AMP 的基本库中，所以您不需要在代码中显式包含它们。

扩展组件是库基础的扩展，默认情况下不包括在内。在您的代码中，您需要将扩展组件作为自定义元素显式地包含在内。例如，您可能希望使用`amp-analytics`添加页面分析，或者使用`amp-lightbox`添加 lightbox。

该目录还包括实验组件，这些组件已经发布，但还不支持广泛使用。如果在元器件目录中找不到自己需要的，不妨自己编写 AMP 元器件！

现在我们知道了哪些 AMP 组件是可用的，让我们在网页上添加一个吧！

## 将网页转换为 AMP 页面

在我们向网页添加 AMP 组件之前，我们需要将网页配置为 AMP 页面。`amp`配置接受两种类型的值，`true`和`hybrid`。输入值`true`会将网页变成 AMP HTML 页面。输入值`hybrid`会创建网页的两个版本，一个是 AMP HTML，一个是标准 HTML，默认为标准 HTML。

让我们用`true`将我们的网页配置为 AMP 页面:

```
export const config = { amp: true }

```

现在，让我们通过运行下面的代码块来创建一个真正的 AMP 页面:

```
export default function Page = () => {
  return <h1>My First AMP Page</h1>
}

```

当我们导出 true AMP 页面时，Next.js 将创建页面的两个版本:一个为您的用户优化的版本和一个为搜索引擎优化的版本。

要创建混合 AMP 页面，您只需将页面的配置值更改为`hybrid`:

```
export const config = { amp: 'hybrid' }
export default function Page = () => {
  return <h1>My First AMP Page</h1>
}

```

要访问 AMP 页面，您需要在 URL 的末尾添加`?amp=1`。此外，因为`hybrid`不像`true`那样是布尔值，所以需要用引号将其括起来。

混合 AMP 页面要求您为 AMP 和标准 HTML 页面编写有效的 HTML。为了呈现正确的 HTML，我们可以使用`useAmp` React 钩子。

## 导入`useAmp` React 钩子

通过导入下面的包，将`useAmp` React 挂钩添加到您的网页:

```
import { useAmp } from 'next/amp'

```

接下来，创建一个变量来保存 React 挂钩的输出:

```
const isAmp = useAmp()

```

现在，您可以使用三元运算符或`if`语句来呈现正确的 HTML:

```
{isAmp ? (
    // AMP component
    ) : (
    // Standard HTML
    )
}

```

最后，我们会把所有的东西放在一起！在下面的例子中，我们将使用加载图像的 AMP 组件`amp-img`，在我们的网页上显示图像:

```
import { useAmp } from "next/amp";
import Image from "next/image";

export const config = { amp: "hybrid" };

export default function Hybrid() {
  const isAmp = useAmp();

  return (
    <div>
      <h1>Hybrid AMP Page&lt;/h1>
      {isAmp ? (
        <amp-img
          alt="A view of the sea"
          src="/chiangmai.jpeg"
          width="640"
          height="360"
        ></amp-img>
      ) : (
        <Image
          alt="A view of the sea"
          src="/chiangmai.jpeg"
          width="640"
          height="360"
        />
      )}
    </div>
  );
}

```

在上面例子中的 AMP 版本中，我们不必使用默认的 HTML `<img>`标签。此外，因为`amp-img`是作为内置组件包含在内的，所以我们不必将它导入到我们的网页中！如果 AMP 组件库中的一个组件没有包含在基本库中，我们将收到以下通知，告知它需要一个外部脚本:

```
<script async custom-element="amp-carousel" src="https://cdn.ampproject.org/v0/amp-carousel-0.2.js"></script>

```

在 Next.js 中，您需要在文档的头部包含这个`script`标记，如下面的代码块所示:

```
<Head>
    <script
      async
      custom-element="amp-carousel"
      src="https://cdn.ampproject.org/v0/amp-carousel-0.2.js"
    ></script>
</Head>

```

## 验证 AMP 页面

在我们导出 AMP 页面之前，让我们确保它符合 AMP 的要求。添加验证的方法有很多，例如，npm 包、命令行工具、浏览器扩展和开发人员控制台！下面我们来看看 Next.js， [`amphtml-validator`](https://www.npmjs.com/package/amphtml-validator) 中内置的 AMP 验证方法。

在构建和导出期间，`amphtml-validator`要求 AMP 页面符合 AMP 规范。任何警告或错误都将显示在终端中。

例如，假设您尝试使用常规的 HTML `<img>`标签，而不是`amp-img`。这将导致导出错误，如下面的代码所示:

```
Amp Validation

/hybrid?amp=1  error  The parent tag of tag 'img' is 'div', but it can only be 'i-amphtml-sizer-intrinsic'.
               error  The parent tag of tag 'img' is 'div', but it can only be 'i-amphtml-sizer-intrinsic'.

> Build error occurred
Error: AMP Validation caused the export to fail. https://nextjs.org/docs/messages/amp-export-validation

```

如果您想要使用自定义 AMP 验证程序，您可以在`next.config.js`文件中添加自定义验证规则的路径:

```
module.exports = {
  amp: {
    validator: './custom_validator.js',
  },
}

```

## 在 Next.js 中导出 AMP 页面

如果没有验证错误，运行`next export`会将我们所有的页面导出到静态 HTML 页面。Next.js 自动检测页面是否支持 AMP，并以正确的格式导出页面。

如前所述，混合 AMP 页面将导出为两个文件。例如，假设我们有一个名为`pages/blog.js`的混合页面。它将被导出为`out/blog.html`，一个带有客户端 React 运行时的标准 HTML 页面，以及`out/blog.amp.html`，一个 AMP HTML 页面。

Next.js 将自动插入一个从 AMP 页面到标准 HTML 页面的链接，反之亦然，分别如下面两个代码片段所示:

```
<link rel="amphtml" href="/blog.amp.html" />

<link rel="canonical" href="/blog" />

```

如果`pages/blog.js`是一个真正的 AMP 页面，它将导出一个名为`out/blog.html`的 HTML 文件。

## 结论

现在，你知道如何开始使用 Next.js 中的 AMP 组件了！

在本教程中，我们讨论了内置、可扩展和实验性 AMP 组件，真实和混合 AMP 页面，以及`useAmp` React 钩子。然后，我们学习了如何在 Next.js 中验证和导出 AMP 页面。

在撰写本文时，AMP 仅支持 [CSS-in-JS](https://amp.dev/documentation/guides-and-tutorials/develop/style_and_layout/style_pages/) 进行样式化。然而，对 CSS 模块的支持正在开发中，你可以在 [GitHub](https://github.com/vercel/next.js/issues/10549) 上找到一些变通办法。

同样，对于 [TypeScript](https://github.com/ampproject/amphtml/issues/13791) 也没有官方支持。虽然它在路线图上，但还没有预定的发布日期。作为一种变通方法，您可以在项目中创建一个名为`[amp.d.ts](https://stackoverflow.com/questions/50585952/typescript-and-google-amp-property-amp-img-does-not-exist-on-type-jsx-intrin/50601125#50601125)`的自定义类型的文件，并为 AMP 添加自定义类型。

我希望你喜欢这个教程！如果你发现自己被卡住了，就留下评论，我会尽力帮你解决。

## [LogRocket](https://lp.logrocket.com/blg/nextjs-signup) :全面了解生产 Next.js 应用

调试下一个应用程序可能会很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪状态、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/nextjs-signup)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png)](https://lp.logrocket.com/blg/nextjs-signup)[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/nextjs-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你的 Next.js 应用上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用程序的性能，报告客户端 CPU 负载、客户端内存使用等指标。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

让您调试 Next.js 应用的方式现代化— [开始免费监控](https://lp.logrocket.com/blg/nextjs-signup)。