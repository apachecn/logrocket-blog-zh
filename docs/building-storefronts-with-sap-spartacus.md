# 使用 SAP Spartacus 构建店面

> 原文：<https://blog.logrocket.com/building-storefronts-with-sap-spartacus/>

Spartacus 是一个轻量级的开源框架，用于为 SAP 商务云构建在线电子商务商店。Spartacus 基于 JavaScript 和 Angular，以库的形式发布。开发人员可以将 Spartacus 导入到现有的应用程序中，让工具开箱即可用于店面。

在本文中，我们将介绍 Spartacus 的一些特性，讨论如何开始使用店面应用程序，并介绍一些提示和技巧。

## 斯巴达克斯的特征

利用斯巴达克斯的两个主要优势是它的可扩展性和升级能力。[开源库](https://github.com/SAP/spartacus)经常更新——大约每两周一次——因此开发者可以快速利用升级和新功能。由于 Spartacus 完全通过对 SAP 商务云的 REST API 调用来工作，店面独立于任何后端架构运行。

[Spartacus 框架](https://blog.logrocket.com/a-guide-to-sap-commerce-cloud-spartacus/)支持渐进式网络应用(PWA)。PWAs 包含响应式设计元素，允许客户在移动和桌面平台上利用店面，而无需开发人员编写额外的前端功能。

Spartacus 与各种内容管理系统(CMS)接口良好，完全支持提供个性化体验。Spartacus 专为电子商务店面设计，可轻松与 SmartEdit 集成。SmartEdit 包括一个预览 Spartacus 网站的功能，因为它们将出现在客户面前。

## 属国

斯巴达克斯由前端的角形平台支撑。它还使用了 yarn 和 Node.js。

不同版本的斯巴达克斯需要 Angular、Node.js、yarn 的具体版本，所以最好[查看需求](https://github.com/SAP/spartacus#requirements)。

在后端，Spartacus 与 SAP 商务云接口。建议使用 2005 版的 SAP 商务云，但 Spartacus 将支持 1905 版及更高版本。

## 入门指南

要安装 Spartacus，请将库导入现有的 JavaScript 或 Angular web 应用程序。在下面的例子中，我们将解释如何使用 schematics 将 Spartacus 安装到现有的 Angular 项目中。

### 先决条件

对于下面的工作流，以下先决条件是必需的:

*   Angular CLI 版本 9.1 或更高，但低于 10.0
*   角度等于或大于 9 度，但小于 10.0 度
*   节点版本小于或等于 13

## 工作流程和示例

### 添加斯巴达克斯库

要将 Spartacus 库导入 Angular 项目，请打开命令提示符，并将目录更改为您的项目目录。在这里，在提示符下键入以下命令:

```
ng add @spartacus/schematics

```

此命令添加基本配置，但可以扩展以进行自定义。

例如，为`baseUrl`添加一个参数会设置 CX OCC 后端的基本 URL。货币或语言等其他参数设置店面中的默认值。Spartacus 文档中提供了可用参数的完整列表。

除了参数之外，基本命令还可以进一步扩展，以扩展应用程序。这里有几个例子:

*   `ng g @spartacus/schematics:add-ssr`(配置服务器端渲染)
*   `ng g @spartacus/schematics:add-pwa`(包括为斯巴达克斯定制的 PWA 模块)
*   `ng g @spartacus/schematics:add-cms-component`(创建一个 CMS 组件并添加该组件)

### 幕后发生了什么

当 base 命令运行时，后台会发生几个步骤来在您的项目中设置 Spartacus:

*   所需的依赖项被添加到项目中
*   斯巴达克斯模块被导入`app.module`并设置默认配置
*   斯巴达克斯风格导入到`main.scss`
*   `cx-storefront`组件被添加到`app.component`

如果添加 PWA 或 SSR 之类的参数或标志，则会发生额外的步骤，例如，添加服务器端呈现依赖项和配置所需的额外文件。

### CMS 组件设置

建立一个新的电子商务网站的最重要的步骤之一是获得库存，所以斯巴达克斯示意图支持添加 CMS 组件。

虽然`add-cms-component`可以处理几乎所有的 CMS 设置，但是某些定制也是可用的，其中大部分与 CMD 组件数据相关。

*   `--declareCmsModule`:标识添加新 CMS 组件的位置。如果未指定现有模块，安装程序将生成一个新模块。
*   `--cmsComponentData`(也称`--cms`):默认为`true`。该选项将`CmsComponentData`添加到新组件中。
*   `--cmsComponentDataModel`(也称`--cms-model`):标识`CmsComponentData`的型号类别。如果`--cmsComponentData`设置为`true`，则该参数是必需的。
*   `--cmsComponentDataModelPath`(也称`--cms-model-path`):指定导入`CmsComponentData`的路径。默认设置为`@spartacus/core`。

下面是一个使用`add-cms-component`和`--declareCmsModule`的例子:

```
ng g @spartacus/schematics:add-cms-component mySiteCms
--cms-model=MySiteModel --module=app
--declareCmsModule=my-site-cms-path/my-site-cms
```

上面的语句创建了必要的`component.ts`文件，并将该文件添加到用户指定的模块位置`my-site-cms-path/my-site-cms.module.ts`。该命令还将`my-site-cms.module.ts`导入到`app.module.ts`。

在站点建立之后，CMS 仍然可以通过各种 JavaScript、Angular 和 CSS 变化来定制个性化体验。Spartacus 文档包括关于[定制 CMS 组件](https://sap.github.io/spartacus-docs/customizing-cms-components/)的附加信息。

### 定制斯巴达克斯

#### 页面定制

Spartacus 支持页面，即使它是单页面应用程序(SPA)。Spartacus 中的页面由 CMS 控制，因此对页面的任何添加或编辑都是在 CMS 端进行的。

#### 旗帜

Spartacus 利用横幅组件来呈现 CMS 中创建的横幅。横幅可以有一个或多个图像，也可以包含标题等可选内容。它们通常用于链接附加内容。

横幅可以用斯巴达克斯这样映射:

```
    SimpleResponsiveBannerComponent: {
      component: BannerComponent
    },
    BannerComponent: {
      component: BannerComponent
    },
    SimpleBannerComponent: {
      component: BannerComponent
    }
  }
}

```

#### 搜索框

添加一个搜索框组件可以为用户搜索提供两个重要的功能:自动完成和无需离开页面就可以搜索产品目录。搜索框组件被添加到 JavaScript 组件中，如下所示:

```
        SearchBoxComponent: {
          component: SearchBoxComponent
        }
    }
}

```

可以对其进行编辑，以定制搜索功能。

## 提示和技巧

### 配方模块

斯巴达克斯在`src/app/app.module.ts`中设置了提供基本实现的配方模块。目前，B2cStorefrontModule 是最流行的食谱模块。但是，如果您需要额外的灵活性来进行更深层次的定制，那么使用另一个 recipe 模块(如 StorefrontModule 或 StorefrontFoundationModule)可能更适合您的站点。这些都是值得研究的，看看什么是最适合你的网站。

### 参考库

我们建议参考斯巴达克斯库进行进一步定制。尽管将 Spartacus 源代码复制并粘贴到您的项目中很有诱惑力，但还有其他更简洁的方式来定制您的店面，例如，编辑 CMS 内容。

## 结论

Spartacus 是一个使用 SAP 商务云的面向店面的开源库。在本文中，我们介绍了斯巴达克斯的几个基本特性，以及如何将它与现有网站结合起来。我们讨论了几个如何为 CMS、横幅和搜索功能定制 Spartacus 的例子。我们还介绍了一些与斯巴达克斯合作的技巧和诀窍。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

有关使用斯巴达克斯和定制店面的更多信息，请访问[斯巴达克斯官方文档](https://sap.github.io/spartacus-docs/)。

## [LogRocket](https://lp.logrocket.com/blg/ecommerce-signup) :看看用户为什么不完成你的电子商务流程中的一个步骤的技术和 UX 原因。

[![](img/d60d88871d85e76e0dcca90f4bbaf78c.png)](https://lp.logrocket.com/blg/ecommerce-signup)

LogRocket 就像是一个网络和移动应用程序和网站的 DVR，记录你的电子商务应用程序上发生的一切。LogRocket 没有猜测用户不转化的原因，而是主动揭示了阻止你转化的根本原因，比如 JavaScript 错误或死点击。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

开始主动监控您的电子商务应用程序— [免费试用](https://lp.logrocket.com/blg/ecommerce-signup)。