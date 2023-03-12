# Next.js 使用 next/image 进行自动图像优化

> 原文：<https://blog.logrocket.com/next-js-automatic-image-optimization-next-image/>

一张图胜过千言万语，图像是网络与用户沟通的重要组成部分。以最基本的形式定义它们很简单，只需要简单的`<img>`元素:

```
<img src="image.jpg">
```

通常，源图像`image.jpg`被嵌入到一个网页中，假设该图像与 HTML 页面在同一个目录中。

如果浏览器或屏幕阅读器不能加载图像，您可以——也应该——通过添加描述图像的替代文本(`alt`)来进一步完善这种图像定义的规范形式:

```
<img src="image.jpg alt="describe the image here"/>

```

但是对于网络上的图片，魔鬼就在细节中。随着 web 的发展，图像优化的需求也在增长，无论是为了用户体验还是开发者体验。

为了确保为用户提供最佳可用图像，必须解决图像大小、web 格式和响应能力等方面的问题。

通过`img`元素的大量优化 API 可以解决用户的需求。然而，它们很快会变得难以打开。这就是自动图像优化可以让开发人员受益的地方。

## Next.js 中的自动图像优化案例

像 Next.js 这样的框架提供了一种抽象，可以解决最常见、最普通、最复杂的任务，比如路由、内部化和图像优化。

根据 Next.js 团队的说法，Next.js 的[目标是改善两件事:开发者和用户体验](https://nextjs.org/blog/next-10#built-in-image-component-and-automatic-image-optimization)。虽然大多数优化都集中在减少发送给用户的 JavaScript 数量上，但是还有其他方面也需要优化，比如图像。输入 Next.js 10。

Next.js 10 欢迎一个内置的图像优化 API，`next/image`，作为原生自动图像优化的规范形式，提供了五个好处。

### 改善用户体验

通过默认情况下延迟加载的优化图像，用户可以期待网站加载时间的性能提升，最终改善整体用户体验。

### 良好的开发者体验

有了`next/image`简单易用的 API，开发人员可以更好地定义基本图像，根据自己的喜好进行调整，或者深入研究缓存和加载器等高级配置选项。

### 不受影响的构建时间

构建时间的增加并不是优化的副作用，因为 Next.js 会在用户请求时按需优化图像，而不是在构建时。

### 现代图像技术和格式

默认情况下，图像是延迟加载的，可以在受支持的浏览器中以 WebP 等现代格式提供。

### 未来证明

Next.js 还可以自动采用未来的图像格式，并将其提供给支持这些格式的浏览器。

## 使用`next/image` API

在 Next.js 中，`next/image` API 是图像优化的最佳点。它将`<Image/>`组件公开为传统的单一事实来源。这意味着您只需要学习如何使用一个 API 来处理 Next.js 中的图像优化。

从设计上来说，在其最基本的形式中，`<image/>`组件基本上类似于 HTML `img`元素，因为它们都接受`src`和`alt`属性/特性:

```
// 1\. Import the `Image` component from the `next/image` API
import Image from 'next/image';

export default function CardImage({imageSrc, imageAltText}) {
    return (
        <div classname="cardImageWrapper">
            {/* 2\. Use the `Image` component as you would any other component  */}
            <Image src={imageSrc} alt={imageAltText}/>
        </div>
    )
}

```

组件`<Image/>`的功能可以通过许多可用的道具来扩展。让我们来看看它们。

### `src`道具

`src`是`<Image/>`组件的唯一真实来源:

```
<Image src="image.webp"/>

```

要使用图像`src`，它必须是以下之一:

1.  作为内部路径的路径字符串，就像上面的代码片段一样，[取决于加载器](https://nextjs.org/docs/api-reference/next/image#loader)
2.  作为绝对外部 URL 的路径字符串，在`next.config.js`中设置了域选项
3.  静态导入的图像文件

在这种情况下，我们将导入一个静态图像文件:

```
import Image from 'next/image';
// Import a static image file
import defaultCardImage from '../public/defaultCardImage.webp';

export default function CardImage({imageSrc = defaultCardImage, imageAltText}) {
    return (
        <div classname="cardImageWrapper">
            <Image src={imageSrc} alt={imageAltText}/>
        </div>
    )
}

```

### `width`和`height`道具

图像的绝对宽度以像素为单位。除静态导入的图像或布局属性设置为`fill`的图像外，这是必需的:

```
<Image
    src="image-src"
    alt="image-alt-text"
    width={40}
    height={40}
/>

```

### `loader`道具

`loader`是一个自定义函数，用于解析 URL。给定`src`、`width`和`quality`参数，它返回一个 URL 字符串:

```
import Image from 'next/image'

// You can add as many loader as you want, and use them conditionally.
// See https://nextjs.org/docs/basic-features/image-optimization#loader
const sanityIoImageLoader = ({ src, width, quality }) => {
  return `https://cdn.sanity.io/${src}?w=${width}&q=${quality || 75}`
}

function CardImage() {
  return (
    <Image
      loader={sanityIoImageLoader}
      src="image-src"
      alt="image-alt-text"
      width={500}
      height={500}
    />
  )
}

```

### `sizes`道具

属性类似于 HTML 元素的属性。这样，大小被设置为指示一组[源大小，如 MDN 文档](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/img#attr-sizes)中所述:

```
<img src="image-src" alt="image-alt-text" srcset="(max-height: 500px) 1000px"/>

```

这意味着如果视口小于 500 像素，则使用 1000 像素宽度的源。使用`<Image/>`组件，您可以如下指定`sizes`:

```
<Image
    src="image-src"
    alt="image-alt-text"
    sizes="320 640 750"
    layout="responsive"
/>

```

请记住，建议仅在使用`responsive`或`fill`布局时定义`sizes`。

### `quality`道具

`quality`属性提供了一个介于`1`和`100`之间的整数，用于定义优化图像的质量；`1`质量最差，`100`最好。默认为`75`:

```
<Image
    src="image-src"
    alt="image-alt-text"
    quality={100}
    layout="fill"
/>

```

### `priority`道具

默认情况下，图像没有优先级(因为它们是延迟加载的)，这意味着`priority`默认为`false`。当`true`时，图像被认为是高优先级的，并且[预载](https://web.dev/preload-responsive-images/)。

仅当图像在折叠上方可见时，才应使用此选项:

```
<Image
    src="image-src"
    alt="image-alt-text"
    width={500}
    height={300}
    priority
/>

```

### `layout`道具

当视窗尺寸改变时，`layout`道具控制图像的布局行为。接受的值是一个字符串，可以是`intrinsic`、`fixed`、`responsive`或`fill`。

每个布局值在以下方面都有细微差别:

1.  生成的`srcSet`和`sizes`
2.  对于使用它们的相应的`<Image/>`元素，期望在父元素上有`display`或`position`值
3.  有时必须与其他道具配对，以获得适当的行为

让我们来看看可能的布局`values`

#### `intrinsic`布局

`intrinsic`布局是四个布局值的默认值。将此布局用于较小的视口会缩小图像尺寸，而用于较大的视口会保持原始图像尺寸。你可以在这里看到[`intrinsic`布局的一个例子。](https://image-component.nextjs.gallery/layout-intrinsic)

#### `fixed`布局

`fixed`布局在行为上类似于本地`img`元素。使用它，无论视口如何变化，图像尺寸都保持固定在原始尺寸，这意味着没有响应。你可以在这里看到[一个`fixed`](https://image-component.nextjs.gallery/layout-fixed) 一个[布局的例子。](https://image-component.nextjs.gallery/layout-fixed)

#### `responsive`布局

`responsive`布局借鉴并覆盖了`fixed`和`intrinsic`布局。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

为了覆盖`fixed`布局，它响应视窗的变化，但是为了覆盖更大视窗的`intrinsic`布局，其中图像尺寸按比例增加，父元素必须是`display: block`。你可以在这里看到[一个](https://image-component.nextjs.gallery/layout-responsive) `[responsive](https://image-component.nextjs.gallery/layout-responsive)` [布局的例子。](https://image-component.nextjs.gallery/layout-responsive)

#### `fill`布局

`fill`布局响应其父尺寸。使用它，图像尺寸被拉伸到父尺寸，假设父尺寸是`position: relative`。

这通常与`objectFit`属性成对出现。你可以在这里看到[`responsive`](https://image-component.nextjs.gallery/layout-fill)[布局的一个例子](https://image-component.nextjs.gallery/layout-fill)。

### `placeholder`道具

这个`placeholder`属性被用作加载图像时的后备图像。它的可能值是`blur`或`empty`。

`placeholder`默认为`empty`，当它为`empty`时，图像加载时没有占位符，只有空白。

然而，当它是`blur`时，`blurDataURL`属性被用作占位符。如果图像`src`是带有 MIME 类型`jpg`、`png`或`webp`的静态导入，那么`blurDataURL`会自动填充。

对于动态图像，必须提供`blurDataURL`属性。像[牌架](https://github.com/joe-bell/plaiceholder)这样的解决方案可以帮助`base64`代。

### `objectFit`支柱

`objectFit`属性类似于[对象适配 CSS 属性](https://blog.logrocket.com/cropping-images-css-in-depth/#using-object-fit-object-position)，该属性设置图像应该如何调整大小以适合其容器。它与`layout=fill`一起使用，或者与设置了`width`和`height`的图像一起使用，可能值为`contain`、`cover`、`fill`、`none`和`scale-down`:

```
<Image 
    src="image-src"
    alt="image-alt-text"
    layout="fill"
    objectFit="contain"
/>

```

### `objectPosition`道具

这类似于指定图像在其容器中的位置的`object-position` CSS 属性。它与`layout=fill`一起使用，或者与设置了`width`、`height`和`position`值的图像一起使用；

```
<Image 
    src="image-src"
    alt="image-alt-text"
    layout="fill"
    objectPosition="top right"
/>

```

### `loading`道具

`loading`属性类似于用于延迟加载的 HTML `img`元素的`loading`属性。可能的值是一串`lazy`或`eager`:

```
<Image 
    src="image-src"
    alt="image-alt-text"
    layout="fill"
    loading="lazy"
/>

```

建议使用`priority`而不是`loading=“eager”`加载折叠上方的图像，因为这会影响性能。

### `onLoadingComplete`道具

`onLoadingComplete` prop 是一个回调函数，在图像完全加载并移除占位符后立即执行:

```
<Image
    src="image-src"
    alt="image-alt"
    layout="fill"
    // returns:
    // {naturalWidth: <imageNaturalWidth>, naturalHeight: <imageNaturalHeight>}
   onLoadingComplete={(imageDimension) => console.log(imageDimension)}
/>

```

### `blurDataURL`道具

`blurDataURL`属性是一个占位符图像，在`src`图像成功加载之前加载，并且必须是 base64 编码的数据 URL 图像，只有与`placeholder=“blur”`结合使用时才有效:

```
import Image from 'next/image';
import cardImage from '../public/defaultCardImage.webp';

export default function CardImage() {
    return (
        <div classname="cardImageWrapper">
            <Image
                src={cardImage}
                alt="image-alt-text"
                placeholder="blur"
                // width, height, and blurDataURL are automatically provided
            />
        </div>
    )
}

```

这些数据是为静态导入的图像自动提供的。对于动态或远程图像，您必须手动提供`width`、`height`和`blurDataURL`。

你可以看到这个[默认](https://image-component.nextjs.gallery/placeholder) `[blurDataURL](https://image-component.nextjs.gallery/placeholder)` [道具](https://image-component.nextjs.gallery/placeholder)的例子，以及 `[blurDataURL](https://image-component.nextjs.gallery/shimmer)` [道具](https://image-component.nextjs.gallery/shimmer)的[微光效果。](https://image-component.nextjs.gallery/shimmer)

### `lazyBoundary`道具

`lazyBoundary`道具类似于交叉点观察器 API 中的`[rootMargin](https://blog.logrocket.com/lazy-loading-using-the-intersection-observer-api/)` [，在触发惰性加载之前充当检测交叉点的边际阈值。默认为`200px`。](https://blog.logrocket.com/lazy-loading-using-the-intersection-observer-api/)

### `unoptimized`道具

当`unoptimized`道具为`true`时，`src`图像将保持原样，而不会改变质量、大小或格式:

```
<Image
    src="image-src"
    alt="image-alt-text"
    width={700}
    height={450}
    unoptimized
/>

```

这个道具默认为`false`。

## 其他属性的注意事项

除了上面列出的属性之外，传递给`<Image/>`组件的其他属性将传递给底层的`img`元素，但以下属性除外:

1.  `decoding`始终是`"async"`
2.  对于`ref`，使用`onLoadingComplete`代替
3.  对于`srcSet`，使用`deviceSizes`代替
4.  对于`style`，使用`className`代替

## 设置源图像属性的样式

为了保持图像的[纵横比，防止](https://blog.logrocket.com/a-closer-look-at-the-css-aspect-ratio/)[核心网页关键](https://web.dev/vitals/#core-web-vitals)和[累积布局偏移](https://vercel.com/blog/core-web-vitals#cumulative-layout-shift)，`next/image`用其他`div`元素包裹`img`元素。

要对源图像进行[样式化，用`className`属性命名它，然后在你的 CSS 中定位它:](https://www.smashingmagazine.com/2020/09/comparison-styling-methods-next-js/)

```
<Image
    src="image-src"
    alt="image-alt-text"
    width={700}
    height={450}
    // You can style this image component with the `cardImage` class name
    className="cardImage"
/>

```

## `next.config.js`中`next/image`的配置

`next/image`可以通过 [next.config.js](https://nextjs.org/docs/api-reference/next.config.js/introduction) 进行配置。我们来看看其中的一些配置。

### `domains`

默认情况下，Next.js 仅优化与您的 Next.js 应用程序位于同一域的图像。例如，如果您的映像在 CMS 外部托管，您必须指定允许优化哪些域。为了防止外部 URL 的滥用，需要这种特定级别:

```
module.exports = {
    images: {
        // assuming you were using the Sanity.io image CDN
        // domains is an array of comma-separated strings
        // ['cdn.sanity.io', 'cdn.not-sanity.io', 'another domain']
        domains: ['cdn.sanity.io'],
    }
}

```

请记住，当`loader`被设置为外部图像服务时，`domains`配置将被忽略。

### `loader`

默认情况下，Next.js 处理图像优化，但您可以将这一职责交给 Cloudinary 或 imgix 等云提供商，他们更专注于图像，而不仅仅是一般的优化。

为此，设置`loader`和`path`以允许使用相对 URL:

```
module.exports = {
    images: {
        loader: 'cloudinary',
        path: 'https://your-site.com/assets/images/'
    }
}

```

### 贮藏

缓存是相当复杂的，有人说是计算机科学中最难的两件事情之一。
在这种情况下，缓存加快了图像传送给用户的速度，因为它避免了网络上的重取。

[next . js 文档对默认加载器上的缓存](https://nextjs.org/docs/basic-features/image-optimization#caching)有充分的解释。但是，如果您使用不同的加载器，比如 Cloudinary，那么您必须参考该文档来了解如何启用缓存。

## 高级用例的配置

虽然不总是必要的，但有一些高级用例可用于优化图像。但是，请注意，如果在本节中配置任何属性，它将在未来的更新中覆盖对 Next.js 默认值的任何更改。

然而，让我们看看他们是什么。

### `deviceSizes`和`imageSizes`

设备和图像大小相似，但有细微差别。这两种方法的优点是，网站用户的设备宽度是预先知道的。它们表示设备宽度断点列表:

```
module.exports = {
    images: {
        // the sizes define below are the defaults
        deviceSizes: [640, 750, 828, 1080, 1200, 1920, 2048, 3840],
        imageSizes: [16, 32, 48, 64, 96, 128, 256, 384],
    }
}

```

`<Image/>`采用`responsive`或`fill`布局时`deviceSizes`有效，`<Image/>`采用`fixed`或`intrinsic`布局时`imageSizes`有效。

### `minimumCacheTTL`

您还可以使用`minimumCacheTTL`为缓存的优化图像配置多长时间或以秒为单位的生存时间(TTL):

```
module.exports = {
    image: {
        // This is 60 seconds
        minimumCacheTTL: 60,
    }
}

```

文档建议最好使用静态图像导入，它可以自动处理散列文件内容并永久缓存文件。但是，请记住，您可以禁用静态导入，所以明智地选择您的战斗。

### `disableStaticImports`

如果您有自己的理由(我个人还没有遇到过)，可以禁用静态导入:

```
module.exports = {
    images: {
        disableStaticImages: true
    }
}

```

## 图像格式，如 WebP 和核心 Web Vitals

为了确保您的用户在他们的设备上获得最佳图像，图像在受支持的浏览器中以 [WebP 等格式提供，允许开发人员向用户提供较亮的图像以提高速度，同时尽可能保持保真度和质量。](https://developer.mozilla.org/en-US/docs/Web/Media/Formats/Image_types#webp)

不同的图像格式有其使用案例。WebP 得到了很好的支持，是动画图像的最佳选择，提供了比 PNG 或 JPEG 更好的压缩。

由于 Next.js 优化了这种格式的图像，Next.js 网站的视觉稳定性不会受到累积布局变化的影响，并且您可以在核心网站生命体征上获得高分。

## 结论

Next.js 中的图像优化通过易于使用和扩展的改变游戏规则的强大 API 改善了用户和开发人员的体验。这无意中解决了一个主要的核心网站需求，帮助网站获得更高的搜索引擎优化排名，一切都以`next/image`开始和结束。

## [LogRocket](https://lp.logrocket.com/blg/nextjs-signup) :全面了解生产 Next.js 应用

调试下一个应用程序可能会很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪状态、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/nextjs-signup)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png)](https://lp.logrocket.com/blg/nextjs-signup)[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/nextjs-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你的 Next.js 应用上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用程序的性能，报告客户端 CPU 负载、客户端内存使用等指标。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

让您调试 Next.js 应用的方式现代化— [开始免费监控](https://lp.logrocket.com/blg/nextjs-signup)。