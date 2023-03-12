# React 中的渐进式图像加载:教程

> 原文：<https://blog.logrocket.com/progressive-image-loading-react-tutorial/>

图片会对网站产生很大的影响。他们的存在改善了用户体验，并有助于提高参与度。然而，加载高质量的图像可能会花费时间，并使这种体验更加令人沮丧，尤其是在慢速互联网连接上。

为了解决这个问题，开发人员需要部署一个支持积极加载体验的策略。一种这样的策略是渐进式图像加载。

在本教程中，我们将学习渐进式图像加载，如何在 React 中部署这一策略，等等。

## 为什么渐进式图像加载是有用的

通过渐进式图像加载，开发人员可以显示低分辨率或预览图像，直到实际图像加载完毕。这通过提供图像即将出现的意识来改善用户体验。

下面的 GIF 演示了如何使用本地`<img />`元素来渲染图像。

![Native Img Element Loading On Webpage](img/664d5e9fb289b034a4635fc5cb99ff4c.png)

正如我们所看到的，虽然页面已经加载，但是图像在呈现之前需要额外的一秒钟，从而导致空白。当我们的互联网连接速度非常慢时，这种体验会变得更糟。

通过使用渐进式加载技术，我们可以渲染一个小文件大小的图像版本，以减少加载时间。一旦加载了高分辨率版本，我们就可以交换图像文件。参见下面的 GIF 演示:

![Clicking Reload Button At Top Left Of Screen To Show Result Of Progressive Image Loading With Onload Event Handler](img/9b711486b669b2aed3708899233f2c6f.png)

由于占位符图像几乎立即加载，这种策略也有助于减少由网页图像引起的布局偏移问题。请注意，布局偏移主要是因为浏览器不知道要为图像保留多少空间。

我们可以通过向图像添加`width`和`height`属性来防止这种行为。这将通知浏览器为图像保留一定数量的空间。然后，我们必须在 CSS 文件中的图像上应用一个`max-width: 100%`和一个`height: auto`，以确保图像在响应布局中正确表现[。](https://blog.logrocket.com/developing-responsive-layouts-with-react-hooks/)

在本教程中，我们将学习如何通过从零开始逐步加载图像来改善用户体验和防止布局偏移。我们还将学习如何使用外部库来实现相同的结果。

要学习本教程，您必须具备 React 的应用知识。

## React 中的渐进式图像加载技术

渐进图像的魔力是通过创建两个图像版本实现的:实际图像和较小的文件版本，通常小于 2kB。

低质量图像首先被加载以便快速显示，并在主图像下载时被放大以适合主图像的宽度。然后，应用模糊过滤器和适当的 CSS 过渡。

像 Gatsby 和 Next.js 这样的 React 框架也在它们的图像组件中使用这种模式。但是框架自动从源图像中生成图像，而不是用户手动创建图像的小版本。

此外，这些框架使用高级图像处理选项，并支持在屏幕下方缓慢加载图像。

在我们的例子中，重点是 React 中的渐进图像加载。让我们开始实现吧。

### 创建图像组件

我们将创建一个名为`ProgressiveImg`的图像组件来封装一个`<img />`元素和渐进式加载的逻辑。然后，这个组件可以用来替换本地的`<img />`元素。

```
const ProgressiveImg = ({ placeholderSrc, src, ...props }) => {
  return (
    <img
      {...{ src: placeholderSrc, ...props }}
      alt={props.alt || ""}
      className="image"
    />
  );
};
export default ProgressiveImg;

```

在上面的代码中，组件接收实际的图像源、它的占位符源以及我们传递的所有其他属性。然后，我们将这些道具分配给`<img />`元素属性。

注意我们如何使用`…` spread 操作符来注入组件接收的任何其他道具。例如，组件将接收一个所需的图像`width`和`height`，我们马上就会看到。同时，正如我们前面提到的，我们为`src`分配了一个占位符图像源用于快速显示。

接下来，让我们确保像这样将上述所有道具传递给`<ProgressiveImg />`:

```
import ProgressiveImg from "./components/ProgressiveImg";
import image from "./images/large_.jpg";
import placeholderSrc from "./images/tiny_.jpg";

export default function App() {
  return (
    // ...
    <ProgressiveImg
      src={image}
      placeholderSrc={placeholderSrc}
      width="700"
      height="465"
    />
    // ...
  );
}

```

从代码中可以看出，我们传递了图像及其小尺寸版本，我们将其大小调整到小于 2kB。我们还必须传递图像`width`和`height`以防止布局偏移。

如果图像尺寸大于指定值，请确保保持纵横比。这样，前端看起来应该是这样的:

![Demonstrating How A Small File Size Image Appears In The Frontend](img/d0192e011d683e0705681c4a2d59d9c5.png)

### 将缩略图更新为实际图像

为了更新`img`的`src`并呈现实际的图像，我们必须通过`useState`钩子将默认的图像源存储在一个状态变量中。然后，一旦加载了实际的图像，我们就可以更新一个`useEffect`钩子中的变量。

让我们更新`ProgressiveImg`组件，这样我们就有了以下内容:

```
import { useState, useEffect } from "react";

const ProgressiveImg = ({ placeholderSrc, src, ...props }) => {
  const [imgSrc, setImgSrc] = useState(placeholderSrc || src);

  useEffect(() => {
    // update the image
  }, []);

  return (
    <img
      {...{ src: imgSrc, ...props }}
      alt={props.alt || ""}
      className="image"
    />
  );
};
export default ProgressiveImg;

```

请注意，`img`的`src`属性现在被赋予了一个状态变量的值。默认情况下，这个值被设置为占位符源(如果有的话)。否则，它将被指定为主图像。

接下来，让我们更新`useEffect`钩子，这样我们就有了以下内容:

```
useEffect(() => {
  const img = new Image();
  img.src = src;
  img.onload = () => {
    setImgSrc(src);
  };
}, [src]);

```

在这个钩子中，我们首先通过实例化一个`Image()`对象来创建一个`img`元素，并将`src`属性设置为实际的图像源。

使用 image 对象上的`onload`事件处理程序，我们可以检测实际图像何时已经完全加载到后台。然后，我们将图像`src`更新为实际图像。

请参见下面的结果:

![Clicking Reload Button At Top Left Of Screen To Show Result Of Progressive Image Loading With Onload Event Handler](img/9b711486b669b2aed3708899233f2c6f.png)

### 实现过渡模糊

让我们添加一个 CSS 过渡模糊的平滑效果。在`ProgressiveImg`组件中，在`return`语句上方添加以下代码:

```
const customClass =
    placeholderSrc && imgSrc === placeholderSrc ? "loading" : "loaded";

```

我们将根据加载状态向图像动态添加类名。

因此，更新`<img />`以包含自定义类名:

```
return (
  <img
    // ...
    className={`image ${customClass}`}
  />
);

```

如果实际的图像仍然在加载，我们给图像添加一个`loading`类。否则，我们添加一个`loaded`类。然后，我们可以更新 CSS 以包含样式规则:

```
.loading {
  filter: blur(10px);
  clip-path: inset(0);
}
.loaded {
  filter: blur(0px);
  transition: filter 0.5s linear;
}

```

保存并查看前端的更改。在 CodeSandbox 上查看完整的[代码。](https://codesandbox.io/s/white-currying-estdxf?file=/src/components/ProgressiveImg.js)

### 使用库进行渐进式图像加载

我们还可以通过使用一个名为`[react-progressive-graceful-image](https://www.npmjs.com/package/react-progressive-graceful-image)`的库来逐步加载图像。要使用它，我们必须安装它:

```
npm i react-progressive-graceful-image

```

然后我们可以导入`ProgressiveImage`组件，并像这样实现它:

```
import ProgressiveImage from "react-progressive-graceful-image";
import image from "./images/large_.jpg";
import placeholderSrc from "./images/tiny.jpg";

export default function App() {
  return (
    // ...
    <ProgressiveImage src={image} placeholder={placeholderSrc}>
      {(src, loading) => (
        <img
          className={`image${loading ? " loading" : " loaded"}`}
          src={src}
          alt="sea beach"
          width="700"
          height="465"
        />
      )}
    </ProgressiveImage>
    // ...
  );
}

```

组件使用渲染道具技术来实现渐进的图像加载。在其子函数 prop 中，我们可以访问渲染回调中的`src`和`loading`参数。

使用`loading`参数，我们可以动态地向`img`元素添加类。在加载实际图像时，`loading`返回 true 否则，它返回 false。

我们还为 CSS 文件中的类添加了样式规则。在 CodeSandbox 上查看完整的[代码和演示。](https://codesandbox.io/s/confident-shirley-bsd65e?file=/src/App.js)

## 结论

通过实现渐进的图像加载技术，我们可以极大地改善 React 项目中的用户体验。

在本教程中，我们介绍了如何在有外部库和没有外部库的情况下在 React 中逐步加载图像。我希望你已经学到了很多，并喜欢阅读本指南。如果你有问题或贡献，我在评论区。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)