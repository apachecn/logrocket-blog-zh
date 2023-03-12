# 处理 Next.js 中的链接

> 原文：<https://blog.logrocket.com/dealing-with-links-in-next-js/>

[Next.js](https://nextjs.org) 是用 React 构建 web 应用的优秀工具。我把它看作是 React 应用程序的 [Ruby on Rails](https://rubyonrails.org/) 。它包了很多好东西。

其中一个好处是它可以为您处理路由。

然而，这些年来，我使用了各种路由库——几个版本的 [react-router](https://reacttraining.com/react-router/) 、 [found](https://github.com/4Catalyzer/found) 、 [Navi](https://frontarm.com/navi/en/) ，现在还有 Next。

我经常不得不切换库或更新 react-router，这在每个主要版本中都像是一个新的库。因此，我养成了将路由与应用程序的其他部分隔离开来的习惯。

在本文中，我将解释我在应用程序中隔离路由的两种技术。我使用 Next 作为例子，但是它们可以应用于几乎所有的路由库:

*   使用定制的`Link`组件
*   将所有路径放在一个文件中

## 技术 1:定制`Link`组件

我的第一个技巧是包装`Link`组件。每个路由库都有类似的组件；它被用来代替`<a>`标签。单击时，它会更改 URL，而不需要整个页面重定向，然后路由处理新页面的加载和显示。

在我几乎所有的项目中，我都使用自己的名为`Link`的组件。这个组件包装了底层路由库`Link`组件。

Next 有类似的`[Link](https://nextjs.org/docs/api-reference/next/link)`组件。它的界面与其他界面稍有不同，但功能相同:

```
<Link href="/about">
  <a>About</a>
</Link>
```

我理解他们为什么这样设计。它相当聪明；它在内部使用`[React.cloneElement](https://reactjs.org/docs/react-api.html#cloneelement)`。你可以在这里查看它的代码[。不过，对我的口味来说有点繁琐。它给你的页面增加了很多视觉效果。](https://github.com/zeit/next.js/blob/canary/packages/next/client/link.tsx)

仅此一点就足以成为包装组件的理由。然而，在这种情况下，我有更大的理由。比方说我想从类似[盖茨比](https://www.gatsbyjs.org/)的地方迁移出来。我不得不改变大量的代码结构；它不会仅仅取代从`next/link`到`gatsby/link`的进口。

下面是一个包装版本的`Link`是如何工作的:

```
import * as React from 'react';
import Link from 'next/link';

// allow this component to accept all properties of "a" tag
interface IProps extends React.AnchorHTMLAttributes<HTMLAnchorElement> {
  to: string;
  // we can add more properties we need from next/link in the future
}

// Forward Refs, is useful
export default React.forwardRef(({ to, ...props }: IProps, ref: any) => {
  return (
    <Link href={to}>
      <a {...props} ref={ref} />
    </Link>
  );
});
```

> 注意:我对所有的例子都使用了 TypeScript。没有类型，代码也能工作。

这是它的使用方法:

```
<Link to="/about">About</Link>
```

新的`Link`组件开始很简单，但是随着时间的推移，您可以添加更多的功能。添加的一个很好的选择是覆盖库的缺省值。

在 Next 9 中，[自动预取](https://nextjs.org/blog/next-9#production-optimizations)默认开启。这将在链接内容位于页面的视口中时预取链接内容。Next 使用一个名为`[IntersectionObserver](https://developer.mozilla.org/en-US/docs/Web/API/IntersectionObserver)`的新浏览器 API 来检测这一点。

这是一个很方便的特性，但是如果你有很多动态的链接和页面，那就有点过头了。静态方面是可以的。通常，我想为特定的页面设置这个，而不是为所有的页面设置。或者，您可能希望只在鼠标悬停在链接上时进行预取。

我们的`Link`组件使得关闭这个特性变得很简单:

```
interface IProps extends React.AnchorHTMLAttributes<HTMLAnchorElement> {
  to: string;
  prefetch?: boolean;
}

export default React.forwardRef(({ to, prefetch, ...props }: IProps, ref: any) => {
  return (
    <Link href={to} prefetch={prefetch || false}>
      <a {...props} ref={ref} />
    </Link>
  );
});
```

现在想象一下，如果我们没有我们的`Link`组件，我们必须为每个链接关闭预取。

## 技术 2:将所有路径放在一个文件中

我看到人们在 React 应用程序中做的一件事是硬编码链接。类似于以下内容:

```
<Link to="/about">About</Link>
<Link to="/contact">Contact</Link>
```

这很脆。它不是类型安全的，并且使重命名 URL 或改变 URL 结构变得困难。

我解决这个问题的方法是在项目的根目录下放一个名为`path.ts`的文件。它看起来像下面这样:

```
export default {
  about: '/about',
  contact: '/contact',
}
```

该文件包含我的应用程序中的所有路线。

这是它的使用方法:

```
import paths from '~/paths';

<Link to={paths.about}>About</Link>
<Link to={paths.contact}>Contact</Link>
```

通过这种方式，我可以更改路线，并且不会出现拼写错误。

## 下一步处理动态路由

接下来的 9 年是一个史诗发行。它最重要的特性是支持[动态路段](https://nextjs.org/blog/next-9#dynamic-route-segments)。

在此之前，Next 并不支持类似`/products/1`开箱即用的动态路由。你必须使用像[下一个路由器](https://github.com/pvtri96/next-router)这样的外部包，或者使用像`/products?id=1`这样的 URL。

处理[d](https://nextjs.org/docs/api-reference/next/link#dynamic-routes)动态路线的方式，我们需要传递两个道具给`Link`:

*   `href`:这是`pages`文件夹中的哪个文件
*   `as`:该页面如何显示在地址栏中

这是必要的，因为下一个客户端路由器相当轻，不知道你整个路由的结构。这种方法的伸缩性很好，因为您不像在其他路由系统中那样，在浏览器内存中保存复杂的路由结构。

下面是它在实践中的样子:

```
<Link href="/products/[id]" as="/product/1">
  <a>Product 1</a>
</Link>
```

这使得处理链接更加麻烦。幸运的是，我们有自己的习俗`Link`和`paths`。我们可以将它们结合起来，得到如下结果:

```
<Link to={paths.product(product)}Product 1</Link>
```

这是如何实现的？

首先，我们在`paths`中添加一个函数，返回页面的两个属性:

```
export default {
  about: '/about',

  contact: '/contact',

  // paths can be functions
  // this also makes it easier to change from "id" to "slug" in the future
  product(product: { id: string }) {
    return {
      href: '/products/[id],
      as: `/products/${id}`,
    };
  }
}
```

其次，我们必须处理好这些道具:

```
interface IProps extends React.AnchorHTMLAttributes<HTMLAnchorElement> {
  // allow both static and dynamic routes
  to: string | { href: string, as: string };
  prefetch?: boolean;
}

export default React.forwardRef(({ to, prefetch, ...props }: IProps, ref: any) => {
  // when we just have a normal url we jsut use it
  if (typeof to === 'string') {
    return (
      <Link href={to} prefetch={prefetch || false}>
        <a {...props} ref={ref} />
      </Link>
    );
  }

  // otherwise pass both "href" / "as"
  return (
    <Link href={to.href} as={to.as} prefetch={prefetch || false}>
      <a {...props} ref={ref} />
    </Link>
  );
});
```

## 迁移故事

在版本 9 之前，Next 不支持动态路由。这是一个大问题，我一直在使用[下一个路由器](https://github.com/pvtri96/next-router)进行动态路由。它有一个中心文件，您可以在其中创建从 URL 到`pages`文件夹中的文件的映射。它的`Link`组件工作方式完全不同。

在接下来的 9 个月之前，它是一个救生员。但是当动态路由被添加到 Next 时，就该停止使用该库了；它现在甚至处于维护模式。

想象一下，有一个包含数百个链接的大型应用程序。你认为像这样的迁移需要多长时间？

对我来说，不到一个小时。我只是替换了`Link`组件中的代码，并更改了动态路径以返回一个对象，而不是下一个路由器需要的路由/参数。

## 结论

这些技术在我使用 React 应用程序的这些年里帮助了我很多。它们相对简单，但有助于将应用程序与底层库分离，使系统易于更改，并具有类型安全性。

我希望它们对你也有用。对于任何问题或评论，您可以在 [Twitter](https://twitter.com/rstankov) 上 ping 我。

## [LogRocket](https://lp.logrocket.com/blg/nextjs-signup) :全面了解生产 Next.js 应用

调试下一个应用程序可能会很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪状态、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/nextjs-signup)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png)](https://lp.logrocket.com/blg/nextjs-signup)[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/nextjs-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你的 Next.js 应用上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用程序的性能，报告客户端 CPU 负载、客户端内存使用等指标。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

让您调试 Next.js 应用的方式现代化— [开始免费监控](https://lp.logrocket.com/blg/nextjs-signup)。