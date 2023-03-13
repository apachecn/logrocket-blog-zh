# 用 UX 思维为移动浏览器优化图像

> 原文：<https://blog.logrocket.com/ux-design/optimizing-images-mobile-browsers-ux-mindset/>

从来没有人说过:“请让我再等一会儿。”但是，如果你没有优化你网站上的图片，那正是你正在做的:你让你的用户不必要地等待，并在此过程中浪费他们的移动数据。

火上浇油的是， [HTTP Archive 的 2022 年年鉴](https://almanac.httparchive.org/en/2022/page-weight)发现图像是网页权重的最大贡献者，约占其大小的一半。

因此，优化图像是显而易见的。如果你想最大化你的网站的性能，改善用户体验，提高你的网站的搜索引擎优化，这是最好的目标。

在本教程中，我们将浏览为移动浏览器优化图像的基本策略。主要思想很简单:在不牺牲图像质量的前提下，尽可能地缩小图像尺寸。我将解释成为图像优化专家需要知道的概念、技术和工具。

为了帮助我们可视化优化性能，我创建了一个演示来比较我们将要讨论的策略。

![Optimization Demo Cactus](img/a399f9febc82a5e74c4b665687960acf.png)

该演示概述了图像加载时间和文件大小，并让您比较不同图像的保真度。为了夸大结果，使用您的开发工具节流您的连接。例如，选择**快速 3G** 将导致图像需要相当长的时间来加载，并突出我们的优化带来的性能优势。

![Optimization Demo Options](img/3f497e3247c6a6bf9b83d5145a3004c2.png)

在进入这些策略之前，让我们建立一个基线来衡量我们的进展。

请看这张由杜尔西·利马拍摄的美丽的仙人掌图片。图片来自 [Unsplash](https://unsplash.com) ，这是一个寻找免费使用的高质量图片的惊人资源。

![Image Optimization Example Image](img/e823b5841dca71b89f0d54ac4c84bcd4.png)

从 Unsplash 下载这张图片会产生一个巨大的 4，678×3，175 像素的 JPEG，大小相当于 3MB。虽然这可能会产生很好的打印效果，但在快速 3G 连接的移动设备上加载这张图片需要 53 秒以上。那要等很长时间！

鉴于 iPhone 只有 390 像素宽，4000 多像素的图像似乎有点大材小用。让我们通过调整图片的大小来优化图片。

## 为大胜利调整图像大小

大多数图像编辑器允许你调整图像的大小。在[预览(在 Mac 上)](https://support.apple.com/guide/preview/resize-rotate-or-flip-an-image-prvw2015/mac#:~:text=In%20the%20Preview%20app%20on,menu%20and%20choose%20a%20size.)中打开图像并调整大小，或者在 Windows 上使用[PowerToys](https://learn.microsoft.com/en-us/windows/powertoys/image-resizer)从文件资源管理器中直接这样做。

无论你想怎么做，想法都是一样的:让图像比以前小。

通过将 cactus 图像的宽度调整为 1，500px，我们将加载时间减少到大约 47 秒。下降了 11%。确保您提供的是大小合适的图像，这是为移动浏览器优化图像的重要的第一步。

[观看演示，比较图像质量。](https://royalfig.github.io/image-optimization-test/)

但是你应该拍多大尺寸的呢？

通常，您希望提供的图像大小与它在浏览器中的显示大小相同。例如，我们 1500 像素宽的调整后图像非常适合显示为 1500 像素宽的网页。如果宽度只有 500 像素，那么最佳图像大小将是 500 像素。

我们有一个金凤花原则:提供一个恰到好处的图像，不要太大也不要太小。(太小的图像会显得模糊和像素化。)

这里的一个难题是，并非所有的显示器都是一样的。根据设备的分辨率，500 像素并不总是等于 500 像素。对于高密度显示器，设备上的多个物理像素构成一个 CSS 像素。

比如 iPhone 14 显示屏宽 390px。然而，这些像素中的每一个都包括三个物理像素。物理像素与 CSS 像素的比率被称为**设备像素比率** (DPR)。在我的谷歌 Pixel 7 上，DPR 是 2.61。参观 whatismyviewport.com 的，看看你现在的 DPR。

理解 DPR 很重要，因为它影响了我们的金发姑娘原则。对于 DPR 为 3 的 iPhone 14，我们需要提供比页面宽度大三倍的图像。这意味着页面上 500 像素宽的容器需要 1，500 像素宽的图像。但是，桌面显示器的 DPR 通常为 1，因此页面上的 500 像素容器需要 500 像素宽的图像。

正如您所想象的，管理不同 DPR 的图像可能会很快变得复杂而乏味。下面，在响应图像部分，我将分享我们如何利用`<picture>` 标签来为我们做这项工作。不过，首先让我们讨论下一个优化策略:压缩。

## 压缩课程

图像压缩通过使用不同的算法删除数据，在指定的阈值内缩小图像的大小。压缩可以是无损的，即图像质量不受影响，也可以是有损的，即牺牲较小的质量损失来大幅缩小图像大小。

图像压缩适合作为一种优化策略，因为它可以缩小图像大小并减少加载时间。压缩我们的原始 cactus 映像为我们节省了 1.36MB，并将加载时间减少了 17%。通过调整 cactus 图像的大小并对其进行压缩，与原始图像相比，我们节省了 2.7MB，加载时间大幅减少了 81%。

我最喜欢用来调整大小、压缩和转换图像的应用是谷歌的 Squoosh——不仅仅是因为它有一个有趣的名字。通过[可下载的网络应用](https://squoosh.app/)和 [CLI 工具](https://github.com/GoogleChromeLabs/squoosh/tree/dev/cli)，Squoosh 为快速图像优化提供了直观的用户界面。

![Image Optimization Compression Options](img/227628300f0a59614aa1dc9ab99859c0.png)

这款应用的部分功能来自转换图像格式的能力，这决定了图像的压缩方式。JPEG、PNG 和 GIF 都是当今广泛使用的常见格式。

最近，WebP 和 AVIF 这两种新的前沿格式出现了。它们在不显著降低质量的情况下显著减小了文件大小，并在浏览器中获得了足够广泛的支持，开始在生产中使用它们。

## 获胜的形式

WebP 发布于 2010 年，是一种支持透明和动画的压缩格式。在撰写本文时，[超过 97%的浏览器支持它](https://caniuse.com/?search=webp)。这种格式由谷歌的工程师开发，旨在取代 JPEGs。

在测试中，工程师们从网上随机选择了 100 万张图片(JPEGs、png 和 gif)。然后，他们使用 WebP 对图像进行了重新编码，没有产生可察觉的质量下降，文件大小减少了 39%。将 cactus 图像重新编码为 WebP 时，文件大小减少了 55 %,加载时间减少了 26%。

AVIF 也有类似的故事。由开放媒体联盟开发——该联盟在 2019 年发布了这种格式——AVIF 目前在 76%的浏览器中得到支持。网飞为该格式的发展做出了贡献，并创建了一个基准框架，[展示了出色的成果](https://netflixtechblog.com/avif-for-next-generation-image-coding-b1d75675fe4)，其中 AVIF 甚至在不牺牲图像保真度的情况下，在缩小图像尺寸方面胜过了 WebP。当我们将 cactus 图像重新编码为 AVIF 时，文件大小减少了 74 %,加载时间减少了 53 %!

上面提到的工具 Squoosh 可以将图像转换为 WebP 和 AVIF。我鼓励你尝试一个图像，看看你自己节省了多少。结果令人难以置信。

让我们花点时间来回顾一下我们迄今为止的进展。我们的目标是展示为移动设备优化图像的最佳策略(通过扩展，这也适用于更大的视窗)。基本策略很简单:在不牺牲质量的情况下减小文件大小。到目前为止，我们已经回顾了如何通过调整大小、压缩和使用最新最好的图像格式来实现这一点。

但是如果我们能把这一切结合起来呢？在下一节关于响应式图像的内容中，我将向您展示如何做到这一点:每次都提供完美的图像。

## 响应性负载

有了 HTML `[<picture> element](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/picture)`，我们可以以正确的大小和格式加载图像，不管是什么设备。它能自动考虑设备的 DPR，确保每次都能获得最佳的图像加载体验。

不要破坏惊喜，但是使用这种方法比未优化的 cactus 映像的加载时间减少了 91 %,文件大小减少了 97%。

如前所述，秘制酱就是`<picture>`元素。我将首先介绍标记，然后介绍它的含义:

```
<picture>
    <source srcset="
        assets/cactus-300.avif 300w,
        assets/cactus-500.avif 500w,
        assets/cactus-700.avif 700w,
        assets/cactus-900.avif 900w,
        assets/cactus-1100.avif 1100w,
        assets/cactus-1300.avif 1300w,
        assets/cactus-1500.avif 1500w,
        assets/cactus-1700.avif 1700w,
        assets/cactus-1900.avif 1900w,
        assets/cactus-2100.avif 2100w
        " sizes="(max-width: 1300px) 100vw, 1300px" type="image/avif" height="747" width="1100">
    <source srcset="
        assets/cactus-300.webp 300w,
        assets/cactus-500.webp 500w,
        assets/cactus-700.webp 700w,
        assets/cactus-900.webp 900w,
        assets/cactus-1100.webp 1100w,
        assets/cactus-1300.webp 1300w,
        assets/cactus-1500.webp 1500w,
        assets/cactus-1700.webp 1700w,
        assets/cactus-1900.webp 1900w,
        assets/cactus-2100.webp 2100w
        " sizes="(max-width: 1300px) 100vw, 1300px" type="image/webp" height="747" width="1100">
    <img srcset="
        assets/cactus-300.jpg 300w,
        assets/cactus-500.jpg 500w,
        assets/cactus-700.jpg 700w,
        assets/cactus-900.jpg 900w,
        assets/cactus-1100.jpg 1100w,
        assets/cactus-1300.jpg 1300w,
        assets/cactus-1500.jpg 1500w,
        assets/cactus-1700.jpg 1700w,
        assets/cactus-1900.jpg 1900w,
        assets/cactus-2100.jpg 2100w
        " sizes="(max-width: 1300px) 100vw, 1300px" src="assets/cactus-1100.jpg" alt="Cactus image loaded responsively" height="747" width="1100">
    </picture>
```

图片元素包含三个元素:一个 AVIF `<source>`元素，一个网页`<source>`元素，以及大家熟悉的`<img>`元素。

如果浏览器支持列出的第一种格式(在本例中为 AVIF ),那么浏览器将提供 AVIF 图像。如果没有，它将转到列表中的下一个图像(WebP ),直到找到支持的图像类型。然后，picture 元素允许您指定多种图像格式，同时还确保只向用户提供支持的图像类型。

在我们的用法中，`[<source> element](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/source)`有五个属性。`[<srcset>](https://developer.mozilla.org/en-US/docs/Web/API/HTMLImageElement/srcset)`通过路径和宽度定义可用图像。例如，`500w`表示图像宽度为 500 像素。上面的`<srcset>`显示有十种不同的图像尺寸。您可以根据需要生成任意多或任意少的图像，但它们应该具有相同的纵横比。

type 属性告诉浏览器图像的 [MIME 媒体类型](https://developer.mozilla.org/en-US/docs/Web/Media/Formats/Image_types)。对于 AVIF，我们使用`type="image/avif"**.**`

下一个属性是`[sizes](https://developer.mozilla.org/en-US/docs/Web/API/HTMLImageElement/sizes)`，这可能是最难理解的。Sizes 使用媒体查询来告诉浏览器应该根据用户的视口使用什么尺寸的图像。

对于 cactus 演示，大小定义是`"(min-width: 1300px) 1300px, 100vw"`。这意味着，如果视窗是 1300 像素或更大，使用 1300 像素宽的图像。因为我在 1300 像素的容器上设置了一个`max-width`，所以我永远不需要比 1300 像素宽的图像。

而且，每当视窗小于 1300px 时，浏览器应该提供大约整个视窗宽度或 100vw 的图像。如果视窗是 500 像素，那么浏览器将提供 500 像素的图像。在演示中，您可以通过调整浏览器大小、刷新并查看 Dev Tools 的 network 选项卡中的图像名称来测试这一点。你会看到图片的大小与浏览器的大小相适应。相当酷。

这里巧妙的地方在于浏览器考虑了显示器的 DPR。如果移动设备的 DPR 为 2，那么当视口超过 1300 像素时，它将提供可能的最大图像(在我们的例子中为 2100 像素)。您可以通过在响应模式下打开图像优化演示并更改 DPR 来测试这一点。您将看到，当该值改变时，浏览器将获取不同的图像。

![Image Optimization DPR](img/d755f034d519207e97455bcbbe153916.png)

![Image Optimization Comparison Demo](img/12f1442ecef1709c252702633ca8ca6b.png)

添加到`<source>`元素的最后一个属性是`width`和`height`。这可能看起来有点违反直觉，因为这里的要点是提供不同高度和宽度的图像，但是[设置图像的高度和宽度也很重要](https://www.smashingmagazine.com/2020/03/setting-height-width-images-important-again/)。

设置宽度和高度给浏览器一个提示，告诉它你的图像将占用多少空间。有了这些信息，浏览器就可以为图像保留适当的空间，并防止出现图像加载后页面跳转的尴尬情况。

但是我们用哪个高度和宽度呢？我认为[奇普·卡伦的建议](https://chipcullen.com/what-width-and-height-attributes-to-use-with-responsive-images/)是最好的方法:使用后退图像的宽度和高度。在演示中，那是`width="1100"`和`height="747"`。请注意，我们省略了单位。

`<img>`标签遵循相同的模式，增加了两项内容。添加一个`alt`标签来改善可访问性和 SEO，并使用`<src>`元素来指定一个后备图像，以防其他标记不受支持。

咻！深呼吸，因为这是你需要知道的关于使用响应图像来优化用户在移动浏览器上的体验的一切。如您所见，响应式图像需要大量的标记。

为了让您的生活更轻松，请选择支持即时响应图像的平台或工具。比如发布平台 [Ghost](https://ghost.org) 自动为你优化图片，并提供[助手，方便生成你自己的](https://ghost.org/docs/themes/assets/)。静态站点生成器 [11ty](https://www.11ty.dev/) 提供了一个[图像插件](https://www.11ty.dev/docs/plugins/image/)来做同样的事情。[盖茨比](https://www.gatsbyjs.com/plugins/gatsby-image/)、 [Next.js](https://nextjs.org/docs/api-reference/next/image) 等平台也包含此功能。

另一个神奇的工具是 Cloudinary 的[响应图像断点生成器](https://www.responsivebreakpoints.com/)。您上传您的图像，指定您的断点，然后 Cloudinary 为您生成图像和标记。除了这个工具， [Cloudinary](https://cloudinary.com/) 还为图像提供云托管服务，它使用比这里提到的更复杂的技术来自动提供完美的图像。

![Cloudinary Demo](img/542fc6f4b5a8041ae722b95743e64561.png)

不同的命令行工具也可用于生成响应图像。对于 cactus 演示，我编写了两个脚本来生成所有的图像。一个脚本使用 [ImageMagick](https://imagemagick.org/index.php) ，另一个使用 [Squoosh](https://github.com/GoogleChromeLabs/squoosh/tree/dev/cli) 。最终，Squoosh 给了我更好的结果，所以我最终使用了它生成的图像。

快速提示:在 Squoosh web 应用程序中，有一个 CLI 按钮，它将复制代码以在 JavaScript 中执行图像转换。这很有帮助，因为您可以使用 UI 来定义调整大小值、格式和其他选项，然后直接复制代码。

![Squoosh App Optimization](img/b4f3d9e8cd7b9d690e4fd1cd6f67269d.png)

您可能认为已经完成了，但是现在不是偷懒的时候，因为还有最后一个优化策略要讨论:延迟加载。

## 用于图像优化的延迟加载

我们的指导原则是以最小的文件大小提供正确大小的图像，尽可能不牺牲图像质量。这是一个非常好的原则，除了一个致命的缺陷。用户可能永远也不会向下滚动到足以看到这样白白下载的图像。

[延迟加载](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/img#attr-loading)解决了这个问题，它只在保证用户可以看到的情况下下载图像。对于延迟加载，如果用户没有在页面上一直向下移动，那么底部的图像根本不会被加载。因此，您的网页加载速度更快，用户下载的数据更少。

直到最近，延迟加载还需要编辑 HTML 标记和使用 JavaScript 库。然而，现在只需将`loading="lazy"`添加到任何`<img>`元素，以及上面提到的宽度和高度属性。就是这样！超过 92%的浏览器支持[，现在可以对你的图片进行延迟加载。](https://caniuse.com/?search=lazy%20load)

延迟加载最适用于用户视图之外的图像(屏幕外)。不要对折叠上方的图像使用该属性，因为这些图像应该立即加载。

## 完美的形象

考虑到我们所涉及的所有技术细节，让我们回忆一下为什么我们应该首先优化图像。我们希望给我们的用户最好的体验，这意味着不要让他们在不必要的时候等待。这些优化策略有额外的好处，提高我们网站的搜索引擎优化。

从调整大小，到压缩，到最先进的格式，再到将所有内容组合到强大的图片标签中，您现在已经掌握了为您的网站优化图片所需的一切。

你还在等什么？走出去，优化。

*精选图片来源:[icon scout](https://iconscout.com/icon/image-laptop-3)*

## [LogRocket](https://lp.logrocket.com/blg/signup) :无需采访即可获得 UX 洞察的分析

[![](img/1af2ef21ae5da387d71d92a7a09c08e8.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 让您可以回放用户的产品体验，以可视化竞争，了解影响采用的问题，并结合定性和定量数据，以便您可以创建令人惊叹的数字体验。

查看设计选择、交互和问题如何影响您的用户— [立即尝试 LogRocket】。](hhttps://lp.logrocket.com/blg/signup)