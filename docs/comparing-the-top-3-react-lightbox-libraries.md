# 比较排名前三的 React lightbox 库

> 原文：<https://blog.logrocket.com/comparing-the-top-3-react-lightbox-libraries/>

在本指南中，我们将根据 npm 每周下载量比较 React lightbox 实现的前三个库:

我们将比较这三个 lightbox 库的安装方法、实现、优点和局限性，并帮助您确定应该为 React 项目使用哪一个 lightbox 实现。

## 什么是灯箱？

lightbox 实现在您的屏幕上覆盖一个图像或视频播放器，填充整个屏幕并使屏幕的其余内容变暗。一个简单的 lightbox 实现如下所示:

![A simple lightbox implementation](img/fa51fe7cb8254d3a826b25b98d8802d4.png)

The original lightbox was a simple JavaScript implementation that was later configured for jQuery in 2012\. The jQuery implementation was very popular due to its ease of implementation and elegant style.

这个库后来被许多不同的库采用，就像我们将在本文中讨论的那些库。这些库也已经过修改，以服务于当今可用的许多前端框架。

## 反应照片擦拭库

[React Photoswipe Gallery](https://www.npmjs.com/package/react-photoswipe-gallery) 是围绕 Photoswipe 制作的 React 组件包装器，Photoswipe 是 Dmitry Semenov 在 2014 年开发的开源 JavaScript lightbox 插件，具有零依赖性。React Photoswipe 为跨平台应用提供了一些不错的触摸手势，例如:

*   触摸以缩放或点击缩放
*   在移动设备上合拢手指
*   智能延迟加载
*   响应图像支持

要安装 React Photoswipe Gallery，请运行以下命令:

```
npm install photoswipe react-photoswipe-gallery --save

```

### 基本用法

React Photoswipe Gallery 包附带了可用于任何基本 lightbox 实现的`<Gallery />`和`<Items />`组件。`<Gallery />`组件是主要的 React 图片擦拭库包装器，它实现了原始的图片擦拭 UI 和布局。

`<Items />`组件类似于`<Gallery />`组件的子组件，用于传递显示灯箱所需的预定义图像尺寸。使用下面的代码片段实现单个图像灯箱。

```
import "photoswipe/dist/photoswipe.css";
import "photoswipe/dist/default-skin/default-skin.css";

import { Gallery, Item } from "react-photoswipe-gallery";

function App() {
return (
  <Gallery>
    <Item
      original="https://picsum.photos/1024/768?image=2"
      thumbnail="https://picsum.photos/200/300?image=2"
      width="1024"
      height="768"
    >
      {({ ref, open }) => (
        <img
          ref={ref}
          onClick={open}
          src="https://picsum.photos/200/300?image=2"
        />
      )}
    </Item>
  </Gallery>
);
}

export default App;

```

![A single-image React Photoswipe lightbox gallery](img/c265317bfeecebc4af7720f79c1e2e61.png)

要创建多图像灯箱图库，只需向`<Gallery />`组件添加更多项目。

```
import "photoswipe/dist/photoswipe.css";
import "photoswipe/dist/default-skin/default-skin.css";

import { Gallery, Item } from "react-photoswipe-gallery";

function App() {
return (
  <Gallery>
    <Item
      original="https://picsum.photos/1024/768?image=2"
      thumbnail="https://picsum.photos/200/300?image=2"
      width="1024"
      height="768"
    >
      {({ ref, open }) => (
        <img
          ref={ref} onClick={open}
 src="https://picsum.photos/200/300?image=2" />
      )}
    </Item>
    <Item
      original="https://picsum.photos/1024/768?image=3"
      thumbnail="https://picsum.photos/200/300?image=3"
      width="1024"
      height="768"
    >
      {({ ref, open }) => (
        <img ref={ref} onClick={open} 
src="https://picsum.photos/200/300?image=3" />
      )}
    </Item>
  </Gallery>
);
}

export default App;

```

React Photoswipe Gallery 还提供了一个`<CustomGallery />`组件和一个`<DefaultLayout />`组件，用于编辑原始 Photoswipe UI 和布局。例如，你可以通过创建一个 CSS 文件来定制你的 UI，然后[在你的`<CustomGallery />`组件中引用 CSS 文件](https://blog.logrocket.com/css-only-components/)作为`UI`道具。

你可以[在他们的文档中了解更多关于 React Photoswipe Gallery](https://github.com/dromru/react-photoswipe-gallery) 的信息。

### 优势

*   易于设置和实施
*   积极维护、记录良好的库
*   易于配置和定制
*   附带平滑过渡、触摸手势和缩放等出色功能，这使得在移动设备和网络上实现相同的功能变得非常容易

### 限制

*   不支持音频和视频文件
*   不支持内嵌图片库
*   需要预设的图像尺寸

这最后一点对 lightbox 图像在不同屏幕尺寸上的表现有很大影响。解决这个问题的方法是创建同一个图像的多个 lightbox 版本，但是具有不同的尺寸，根据响应 CSS 的屏幕大小向用户显示不同的内容。

## 简单反应灯箱

简单的反应灯箱库是三个灯箱包中最受欢迎的。简单的 React Lightbox 提供了一个 React 组件包装器(`<SRLWrapper />`)，用于在来自任何外部源(包括您可以定义的源)的图像、音频或视频文件上实现 Lightbox 功能。

值得一提的是，Simple React Lightbox 附带了一个免费版本和一个付费的 pro 版本，目前[处于中断状态](https://simple-react-lightbox.dev/)。免费版使您能够将 lightbox 功能仅添加到图像，而专业版使您能够在音频和视频文件中实现 lightbox。

与 React Photoswipe Gallery 类似，Simple React Lightbox 也允许您在 Lightbox 的基础上进行构建，例如自定义您的 UI 和布局，添加触摸手势和缩略图画廊等。

要开始使用简单的 React Lightbox，请运行以下命令将其安装到您的项目中。

```
npm install --save simple-react-lightbox

```

然后，将简单的 React Lightbox 上下文添加到主 React 组件中。

```
import SimpleReactLightbox from 'simple-react-lightbox'

ReactDOM.render(
 <React.StrictMode>
   <SimpleReactLightbox>
     <App />
   </SimpleReactLightbox>
 </React.StrictMode>,
 document.getElementById("root")
);
```

### 基本用法

如前所述，简单的 React Lightbox 提供了一个`<SRLWrapper />`组件，可以包装在图像周围以实现 Lightbox 功能。

您可以使用下面的代码片段实现一个简单的单图像 lightbox。标签应该包含 lightbox 图像的链接，而标签应该包含缩略图的链接。

```
import { SRLWrapper } from "simple-react-lightbox";

function App() {
 return (
   <SRLWrapper>
     <div>
       <a href="https://picsum.photos/1024/768?image=2">
         <img src="https://picsum.photos/200/300?image=2" alt="lightbox" />
       </a>
     </div>
   </SRLWrapper>
 );
}

export default App;
```

![A Simple React Lightbox single-image gallery](img/2085eb3b948a28b0412f25ff3dca5de8.png)

您还可以通过向`<SRLWrapper />`组件添加更多图像来创建缩略图画廊。内嵌图库会自动添加到灯箱中。

```
import { SRLWrapper } from "simple-react-lightbox";

function App() {
 return (
   <SRLWrapper>
     <div>
       <a href="https://picsum.photos/1024/768?image=2">
         <img src="https://picsum.photos/200/300?image=2" alt="lightbox" />
       </a>
       <a href="https://picsum.photos/1024/768?image=3">
         <img src="https://picsum.photos/200/300?image=3" alt="lightbox1" />
       </a>
       <a href="https://picsum.photos/1024/768?image=4">
         <img src="https://picsum.photos/200/300?image=4" alt="lightbox2" />
       </a>
       <a href="https://picsum.photos/1024/768?image=5">
         <img src="https://picsum.photos/200/300?image=5" alt="lightbox3" />
       </a>
     </div>
   </SRLWrapper>
 );
}

export default App;
```

![A multi-image gallery with Simple React Lightbox](img/a44f034e64b153db2768d4b4c02b763a.png)

与 React Photoswipe 相比，简单的 React Lightbox UI 更具可定制性。通过将设置、标题、缩略图和按钮道具作为选项传递给`<SRLWrapper />`组件，您可以轻松地编辑 UI。例如:

```
import { SRLWrapper } from "simple-react-lightbox";

const options = {
 buttons: {
   iconColor: "##610604",
   iconPadding: "10px"
 },
 caption: {
   captionColor: "#61O6O4",
   captionFontSize: "20px",
 },
 settings: {
   overlayColor: "#D8B863"
 },
 thumbnails: {
   thumbnailsAlignment: "center",
 },
};

function App() {
 return (
   <SRLWrapper options={options}>
     <div>
       <a href="https://picsum.photos/1024/768?image=2">
         <img src="https://picsum.photos/200/300?image=2" alt="lightbox" />
       </a>
       <a href="https://picsum.photos/1024/768?image=3">
         <img src="https://picsum.photos/200/300?image=3" alt="lightbox1" />
       </a>
       <a href="https://picsum.photos/1024/768?image=4">
         <img src="https://picsum.photos/200/300?image=4" alt="lightbox2" />
       </a>
       <a href="https://picsum.photos/1024/768?image=5">
         <img src="https://picsum.photos/200/300?image=5" alt="lightbox3" />
       </a>
     </div>
   </SRLWrapper>
 );
}

export default App;
```

上面的代码将用户界面重新设计成这样:

![The redesigned UI in Simple React Lightbox](img/06b574e67024034a3707b6b41da6008d.png)

### 优势

*   简单的 React Lightbox 非常容易设置、实现和定制
*   积极维护、文档完善的库，非常易于配置
*   配备了触摸手势、缩略图支持等令人惊叹的功能。

### 限制

*   该软件包只支持付费版本的视频和音频文件

## 反应灯光画廊

[React Lightgallery](https://www.npmjs.com/package/react-lightgallery) 是围绕 [lightGallery.js](https://www.lightgalleryjs.com/) 开发的另一个 React 组件包装器，这是一个模块化的 JavaScript 图像和视频灯箱图库插件。React Lightgallery 预装了一些功能，如内嵌图库、捏放缩放和滑动关闭等。它也是跨设备的，在移动设备和网络上都运行良好。

您可以使用以下命令在项目中安装 React Lightgallery。

```
npm install --save react-lightgallery

```

然后，导入 Lightgallery CSS 文件，并将 React Lightgallery 上下文添加到主 React 组件中。

```
import "lightgallery.js/dist/css/lightgallery.css";
import { LightgalleryProvider } from "react-lightgallery";

ReactDOM.render(
 <React.StrictMode>
   <LightgalleryProvider>
     <App />
   </LightgalleryProvider>
 </React.StrictMode>,
 document.getElementById("root")
);
```

### 基本用法

React Lightgallery 提供了一个`<LightgalleryItem />`组件，可以用来轻松实现单图像灯箱功能。

```
import { LightgalleryItem } from "react-lightgallery";

function App() {
 return (
   <div>
     <LightgalleryItem src="https://picsum.photos/1024/768?image=2">
       <img src="https://picsum.photos/200/300?image=2" />
     </LightgalleryItem>
   </div>
 );
}

export default App;

```

![A single-image lightbox implementation with React Lightgallery](img/616181662466b7e59770f29a5f26ff71.png)

React Lightgallery 还提供了对内嵌图库的支持。

```
import { LightgalleryItem } from "react-lightgallery";

function App() {
 return (
   <div>
     <LightgalleryItem src="https://picsum.photos/1024/768?image=2">
       <img src="https://picsum.photos/200/300?image=2" />
     </LightgalleryItem>
     <LightgalleryItem src="https://picsum.photos/1024/768?image=1">
       <img src="https://picsum.photos/200/300?image=1" />
     </LightgalleryItem>
   </div>
 );
}

export default App;

```

![An inline gallery implementation with React Lightgallery](img/eb4be0ce1e4feee86e56f24685199d7d.png)

React Lightgallery 软件包可以根据您的需求轻松配置和定制。这个包提供了一个受支持的原始 lightGallery.js 插件列表和扩展`<LightgalleryItem />`组件的道具。例如，您可以通过在`<LightgalleryItem />`组件中添加`lg-share.j` 插件作为`plugins`道具来轻松实现社交共享。

React Lightgallery 包还提供了对 React 高阶组件(`withLightgallery`)和在`<LightgalleryProvider />`内部使用的 React 钩子(`useLightgallery`)的访问。

你可以通过访问 [React Lightgallery 文档](https://www.npmjs.com/package/react-lightgallery)了解更多关于如何使用插件、道具、高阶组件和 React Hook 的信息。

### 优势

*   易于设置、实施和定制
*   优秀的文档
*   附带令人惊叹的功能，如嵌入式图库、捏放缩放、滑动关闭等。

### 限制

*   这个包还不支持视频和音频灯箱

## React 项目应该使用哪种 Lightbox 实现？

这三个 React lightbox 库是在您的项目中实现 lightbox 功能的绝佳选择，但是，为您的项目选择最终的库应该取决于您项目的特定需求。

React Photoswipe Gallery 和 React Lightgallery 提供了许多令人惊叹的功能，但都缺乏开箱即用的视频和音频文件支持。虽然 Simple React Lightbox 提供视频和音频 Lightbox 支持，但它只在付费版本中提供这种支持，该版本目前处于中断状态。如果除了照片灯箱之外，你还需要实现一个视频灯箱，你可以使用像 [React Image 和视频灯箱](https://github.com/Ngineer101/react-image-video-lightbox)这样的包来实现。

最后，值得一提的是，Simple React Lightbox 拥有三者中最大的社区。这意味着当你使用这个软件包时，你将获得更多的支持。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)