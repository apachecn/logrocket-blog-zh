# 电子商务店面与顺风 CSS -日志火箭博客

> 原文：<https://blog.logrocket.com/storefront-ui-ecommerce-app-tailwind-css/>

***编者按**:这篇文章最后一次更新是在 2022 年 10 月 14 日，包含了关于 Vue 3 和* *的信息，反映了对 Tailwind CSS 的更新。*

Storefront UI 是一个 Vue 组件库，它提供了一个经过深思熟虑的电子商务组件设计系统，并遵循原子设计原则，这对于构建可扩展和可维护的前端应用程序非常有用。[店面 UI](https://www.storefrontui.io/) 也兼容零售的[谷歌 UX 剧本](https://services.google.com/fh/files/events/pdf_retail_ux_playbook.pdf)。

在撰写本文时，Storefront UI 处于稳定测试阶段，但它还不能在 Vue 3 上运行。在本文中，我们将使用 Storefront UI 和 Tailwind CSS 构建一个店面。为了跟进，您可以[从 GitHub repo](https://github.com/Akhigbe-E/vitty-store) 访问代码。我们开始吧！

## 店面 UI 功能

店面 UI 提供了很好的定制选项；您可以使用 props、slots 和 CSS 变量轻松定制 Storefront UI 组件，如原子、分子和有机体。你也可以用 Tailwind CSS 定制组件的外观，我们将在本教程中介绍。

Storefront UI 中的组件响应迅速，因此您可以放心地知道您的组件是移动友好的。Storefront UI 允许您仅导入必要的组件，将它们捆绑在一起以保持高性能。

Storefront UI 提供原始源代码，这意味着您可以从构建时优化中受益，如树抖动或公共块分组。Storefront UI 致力于电子商务，拥有构建现代在线商店所需的所有组件。基于谷歌 UX 剧本，店面 UI 组件也是可访问的。

## 什么是顺风 CSS？

Tailwind CSS 是一个令人敬畏的[实用优先的 CSS 框架](https://blog.logrocket.com/top-utility-first-css-frameworks/)，它使组件的样式化变得简单而快速。对于样式，您可以在标记的`class`属性中添加类似`flex`、`pt-4`和`text-center`的类。

### 为什么要用顺风 CSS？

*   快速样式化:不需要手动编写 CSS、SCSS 或者更少的样式
*   简单的样式化:在样式化的时候你不需要离开你的标记文件
*   直观的类名:类名不能再简单了

例如，`mb-0`表示`margin-bottom`设置为`0`，`pt-0`表示`padding-top`设置为`0`。与颜色选择、间距、排版、阴影以及构成一个设计良好的设计系统的其他东西保持一致也很容易。您只需定义一次类，这些类将在整个应用程序中使用。

## 建设店面

为了更好地理解店面 UI，我们将构建如下所示的店面:

![Finished UI Storefront UI Project](img/eb81d3a40870b921ff2a00bebc9b79c2.png)

### 安装和文件设置

为了创建新的应用程序，我们将安装 Vue CLI 运行下面的命令:

```
npm install -g @vue/cli

```

然后，生成新的应用程序:

```
vue create storefront-app

```

导航到应用程序的目录，使用下面的命令启动开发服务器:

```
cd storefront-app
npm install
npm run dev

```

以上命令将用于`localhost:8080`上的应用。导航到该 URL 后，我们会看到一个活动的应用程序:

![Live Vue Application](img/dea61d4e18bb9e325a455988ab9fb64d.png)

现在，运行下面的命令来安装 Storefront UI:

```
npm install --save @storefront-ui/vue 

```

将包含全局样式和 SCSS 变量的样式表导入应用程序的`main.js`文件:

```
import "@storefront-ui/vue/styles.scss";

```

接下来，通过运行以下命令安装 Tailwind CSS:

```
npm install -D tailwindcss postcss autoprefixer

```

生成您的`tailwind.config.js`和`postcss.config.js`文件:

```
npx tailwindcss init -p

```

打开`tailwind.config.js`文件，配置模板路径:

```
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: [
    "./index.html",
    "./src/**/*.{js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}

```

创建一个`src/index.css`文件，并添加以下代码来导入 Tailwind 的基础、组件和实用程序样式:

```
@tailwind base;
@tailwind components;
@tailwind utilities;

```

将`index.css`文件导入到根`main.js`文件中:

```
import './index.css'

```

您可以通过启动 Vue 项目并将 Storefront UI 链接 atom 导入到您的`App.vue`文件中来检查您的安装是否正常工作:

```
// App.vue file
<template>
  <div>
        <SfLink link="/">Home</SfLink>
  </div>
</template>
 <script>
  import { SfLink} from "@storefront-ui/vue";
  export default {
    components: {
    SfLink,
    },
  };
</script>
<style></style>

```

如果一切正常，您应该会看到一个链接；你可以使用 Tailwind 的[字体大小类](https://tailwindcss.com/docs/font-size)来调整链接的字体大小。为 [`SfLink`](https://docs.storefrontui.io/?path=/docs/components-atoms-link--common) 组件添加一个 class 属性，并赋予其值`text-lg`。如果您保存文件并检查您的浏览器，您应该会看到字体大小发生了变化。

您还可以通过 Tailwind CSS 使用和访问 Storefront UI CSS 变量。要访问 CSS 变量，您的`tailwind.config.js`文件应该类似于下面的代码:

```
module.exports = {
  purge: [],
  important: true,
  theme: {
    extend: {
      fontSize: {
        "sf-xs": "var(--font-size--xs)", //12px
        "sf-sm": "var(--font-size--sm)", //14px
        "sf-base": "var(--font-size--base)", //16px
        "sf-lg": "var(--font-size--lg)", //18px
    },
      fontWeight: {
        "sf-light": "var(--font-weight--light)", //300
        "sf-normal": "var(--font-weight--normal)", //400
        "sf-medium": "var(--font-weight--medium)", //500
        "sf-semibold": "var(--font-weight--semibold)", //600
        "sf-bold": "var(--font-weight--bold)", //700
    },
    colors: {
        "sf-c-black": "var(--c-black)", // #1d1f22
        "sf-c-black-base": "var(--c-black-base)", // #1d1f22
       "sf-c-black-lighten": "var(--c-black-lighten)", // #292c30
        "sf-c-black-darken": "var( --c-black-darken)", // #111214
        "sf-c-white": "var(--c-white)", // #ffffff
        "sf-c-body": "var(--c-body)", // #ffffff
        "sf-c-text": "var(--c-text)", // #1d1f22
        "sf-c-text-muted": "var(--c-text-muted)", // #72757E
        "sf-c-text-disabled": "var(--c-text-disabled)", // #e0e0e1
        "sf-c-link": "var(--c-link)", // #43464E
        "sf-c-link-hover": "var(--c-link-hover)", // // #1d1f22
        "sf-c-primary": "var(--c-primary)", // #5ece7b
        "sf-c-primary-base": "var(--c-primary-base)", // #5ece7b
        "sf-c-primary-lighten": "var(--c-primary-lighten)", // #72d48b
        "sf-c-primary-darken": "var(--c-primary-darken)", // #4ac86b
        "sf-c-primary-variant": "var(--c-primary-variant)", // #9ee2b0
        "sf-c-on-primary": "var(--c-on-primary)", // #ffffff
        "sf-c-secondary": "var( --c-secondary)", // #1d1f22
        "sf-c-secondary-base": "var(--c-secondary-base)", // #1d1f22
        "sf-c-secondary-lighten": "var(--c-secondary-lighten)", // #292c30
        "sf-c-secondary-darken": "var(--c-secondary-darken)", // #111214
        "sf-c-secondary-variant": "var(--c-secondary-variant)", // #43464E
        "sf-c-on-secondary": "var(--c-on-secondary)", //  #ffffff
        "sf-c-light": "var(--c-light)", // #f1f2f3
        "sf-c-light-base": "var(--c-light-base)", // #f1f2f3
        "sf-c-light-lighten": "var(--c-light-lighten)", // #ffffff
        "sf-c-light-darken": "var(--c-light-darken)", // #e3e5e7
        "sf-c-light-variant": "var(--c-light-variant)", //  #ffffff
        "sf-c-on-light": "var(--c-on-light)", // #1d1f22
        "sf-c-gray": "var(--c-gray)", // #72757E
        "sf-c-gray-base": "var(--c-gray-base)", // #72757E
        "sf-c-gray-lighten": "var(--c-gray-lighten)", // #7f828b
        "sf-c-gray-darken": "var(--c-gray-darken)", // #666971
        "sf-c-gray-variant": "var(--c-gray-variant)", // #8D8F9A
        "sf-c-on-gray": "var(--c-on-gray)", // #1d1f22
        "sf-c-dark": "var(--c-dark)", // #1d1f22
        "sf-c-dark-base": "var(--c-dark-base)", // #1d1f22
        "sf-c-dark-lighten": "var(--c-dark-lighten)", // #292c30
        "sf-c-dark-darken": "var(--c-dark-darken)", // #111214
        "sf-c-dark-variant": "var(--c-dark-variant)", // #43464E
        "sf-c-on-dark": "var(--c-on-dark)", //  #ffffff
        "sf-c-info": "var(--c-info)", //  #0468DB
        "sf-c-info-base": "var(--c-info-base)", //  #0468DB
        "sf-c-info-lighten": "var(--c-info-lighten)", // #0474f4
        "sf-c-info-darken": "var(--c-info-darken)", // #045cc2
        "sf-c-info-variant": "var(--c-info-variant)", // #e1f4fe
        "sf-c-on-info": "var(--c-on-info)", //  #ffffff
        "sf-c-success": "var(--c-success)", // #5ece7b
        "sf-c-success-base": "var(--c-success-base)", // #5ece7b
        "sf-c-success-lighten": "var(--c-success-lighten)", // #72d48b
        "sf-c-success-darken": "var(--c-success-darken)", // #4ac86b
        "sf-c-success-variant": "var(--c-success-variant)", // #9ee2b0
        "sf-c-on-success": "var(--c-on-success)", //  #ffffff
        "sf-c-warning": "var(--c-warning)", // #ecc713
        "sf-c-warning-base": "var(--c-warning-base)", // #ecc713
        "sf-c-warning-lighten": "var(--c-warning-lighten)", //  #eecd2b
        "sf-c-warning-darken": "var(--c-warning-darken)", // #d4b311
        "sf-c-warning-variant": "var(--c-warning-variant)", // #f6e389
        "sf-c-on-warning": "var(--c-on-warning)", //  #ffffff
        "sf-c-danger": "var(--c-danger)", //  #d12727
        "sf-c-danger-base": "var(--c-danger-base)", //  #d12727
        "sf-c-danger-lighten": "var(--c-danger-lighten)", // #da3838
        "sf-c-danger-darken": "var(--c-danger-darken)", //  #bc2323
        "sf-c-danger-variant": "var(--c-danger-variant)", // #fcede8
        "sf-c-on-danger": "var(--c-on-danger)", // #ffffff
    },
      spacing: {
        "sf-2xs": "var(--spacer-2xs)", // 4px
        "sf-xs": "var( --spacer-xs)", // 8px
        "sf-sm": "var(--spacer-sm)", // 16px
        "sf-base": "var(--spacer-base)", // 24px
        "sf-lg": "var(--spacer-lg)", // 32px
        "sf-xl": "var(--spacer-xl)", // 40px
        "sf-2xl": "var(--spacer-2xl)", // 80px
        "sf-3xl": "var(--spacer-3xl)", // 160px
    },
      fontFamily: {
        "sf-primary": "var(--font-family--primary)", // "Roboto", serif
        "sf-secondary": `var(--font-family--secondary)`, // "Raleway", sans-serif
    },
    },
  },
  variants: {},
  plugins: [],
};

```

将上面的代码粘贴到您的`tailwind.config.js`文件中并保存。返回到您的`App.vue`文件，并将`SfInput`组件中的类值更改为`text-sf-lg`。您现在通过 Tailwind CSS 使用 Storefront UI 的 CSS 变量来设计组件。

### 构建导航栏

要为我们的店面构建导航栏，在 components 目录中创建一个`header`文件，并向其中添加以下代码:

```
//VittyHeader.vue
<template>
  <div>
    <SfHeader
      logo="https://www.svgrepo.com/show/9395/ribbon-banner-silhouette.svg"
      title="Storefront UI"
      cartIcon="empty_cart"
      wishlistIcon="heart"
      accountIcon="profile"
      searchPlaceholder="Search for items"
      searchValue=""
      wishlistItemsQty="0"
      cartItemsQty="0"
    >
      <template v-slot:navigation>
        <navigation-links />
      </template>
    </SfHeader>
  &lt;/div>
</template>

<script>
  import { SfHeader } from "@storefront-ui/vue";
  import NavigationLinks from "./NavigationLinks.vue";
  export default {
    components: {
      SfHeader,
      NavigationLinks,
    },
  };
</script>
<style></style>

```

在上面的代码中，我们导入了 [`SfHeader`](https://docs.storefrontui.io/?path=/story/components-organisms-header--common) 有机体。该组件使用[命名插槽](https://vuejs.org/v2/guide/components-slots.html#Named-Slots)，其中一个是`navigation`。我们将使用这个插槽向`SfHeader`组件添加导航链接:

```
//NavigationLinks.vue
<template>
  <ul class="flex gap-x-5">
    <li><SfLink class="text-sf-lg" link="/">Home</SfLink></li>
    <li><SfLink class="text-sf-lg" link="/about">About</SfLink></li>
    <li><SfLink class="text-sf-lg" link="/contact">Contact</SfLink></li>
  </ul>
</template>
<script>
  import { SfLink } from "@storefront-ui/vue";
  export default {
    components: {
      SfLink,
    },
  };
</script>
<style></style>

```

记得清空您的`App.vue`文件，导入您的 header 组件，看看它在浏览器中的样子。

### 构建旋转木马分子

Storefront UI 使用 [Glide.js](https://glidejs.com/) ，这是一个滑块和转盘，包括用于定制的[选项](https://glidejs.com/docs/options/)，如`perView`属性，它控制一次可见的幻灯片数量。我们将使用`SfHero`在店面中创建滑块，但是如果您需要使用 Glide.js 提供的选项，您可以使用 [carousel 组件](https://docs.storefrontui.io/?path=/docs/components-organisms-carousel--common):

```
// VittyHero.vue
<template>
  <SfHero>
    <SfHeroItem
      title="Colorful summer dresses are already in store"
      subtitle="Summer Collection 2019"
      buttonText="Learn more"
      background="#ECEFF1"
      image="https://images.unsplash.com/photo-1483985988355-763728e1935b?ixlib=rb-1.2.1&ixid=MXwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHw%3D&auto=format&fit=crop&w=1950&q=80"
    />
    <SfHeroItem
      buttonText="Learn more"
      background="#ECEFF1"
      image="https://images.unsplash.com/photo-1596149615678-8488f200b301?ixid=MXwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHw%3D&ixlib=rb-1.2.1&auto=format&fit=crop&w=1189&q=80"
    >
      <template v-slot:subtitle>
        <h1 class="sf-hero-item__subtitle text-white">Summer Collection 2019</h1>
      </template>
      <template v-slot:title>
        <h1 class="sf-hero-item__title text-white">Colorful summer dresses are already in store</h1>
      </template>
    </SfHeroItem>
  </SfHero>
</template>
<script>
  import { SfHero } from "@storefront-ui/vue";
  export default {
    data: () => ({}),
    components: {
      SfHero,
    },
  };
</script>
<style></style>

```

`SfHeroItem`组件作为`SfHero`的子组件自动导入。在上面的代码片段中，我们使用了名为`subtitle`和`title`的槽来改变文本的默认颜色，这样它在英雄图像上更容易看到。

### 构建家庭产品分子

为了构建家庭产品部分，我们将使用`[SfSection](https://docs.storefrontui.io/?path=/docs/components-molecules-section--common)`组件在店面中创建一个部分。 [`SfCarousel`](https://docs.storefrontui.io/?path=/docs/components-organisms-carousel--common) 组件将接受[转盘定制](https://glidejs.com/docs/options/)的设置属性，`[SfProductCard](https://docs.storefrontui.io/?path=/docs/components-organisms-productcard--common)`组件将呈现产品信息。

我们使用`SfSection`组件中的`titleHeading`属性来设置部分标题文本，而`levelHeading`属性决定了该文本标题的大小:

```
<template>
  <div>
    <SfSection :titleHeading="title" :levelHeading="2">
      <SfCarousel :settings="settings">
        <SfCarouselItem v-for="(n, index) in new Array(6)" :key="index">
          <SfProductCard
            image="https://img.rawpixel.com/s3fs-private/rawpixel_images/website_content/363-mj-3796-ae-a-l.jpg?w=800&dpr=1&fit=default&crop=default&q=65&vib=3&con=3&usm=15&bg=F4F4F3&ixlib=js-2.2.1&s=67561fcc6e2a6442637577f7abb7ad68"
            :imageWidth="216"
            :imageHeight="326"
            title="Cotton Sweater"
            :scoreRating="4"
            :reviewsCount="7"
            :maxRating="5"
            wishlistIcon="heart"
            isOnWishlistIcon="heart_fill"
            showAddToCartButton
          />
        </SfCarouselItem>
      </SfCarousel>
    </SfSection>
  </div>
</template>

<script>
  import { SfProductCard, SfCarousel, SfSection } from "@storefront-ui/vue";
  export default {
    data: () => ({
      settings: { perView: 3 },
    }),
    components: {
      SfProductCard,
      SfCarousel,
      SfSection,
    },
    props: {
      title: {
        type: String,
        required: true,
    },
    },
  };
</script>
<style></style>

```

### 构建横幅部分

我们将使用店面 UI 的`[SfBanner](https://docs.storefrontui.io/?path=/docs/components-molecules-banner--common)`来构建横幅部分。它有一个名为`call-to-action`的插槽，在这里我们可以使用 [`SfButton`](https://docs.storefrontui.io/?path=/docs/components-atoms-button--primary) 组件:

```
<template>
  <div class="container w-full mx-auto" style="max-width:1100px">
    <SfBanner
      title="Eco Sandals"
      subtitle="Summer shoes"
      description="The collection features formal and casual comfort shoes with a Danish design focus. Made from premium leathers and comfort."
      image="https://images.unsplash.com/photo-1572206443494-b07cc12d6921?ixlib=rb-1.2.1&ixid=MXwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHw%3D&auto=format&fit=crop&w=633&q=80"
    >
      <template v-slot:call-to-action>
        <SfButton>
          Shop Now
        </SfButton>
      </template>
    </SfBanner>
  </div>
</template>
&lt;script>
  import { SfBanner, SfButton } from "@storefront-ui/vue";
  export default {
    components: {
      SfBanner,
      SfButton,
    },
  };
</script>
<style></style>

```

### 建立展示区

为了构建 showcase 部分，我们将使用`SfSection`组件和`SfImage`组件。 [`SfImage`](https://docs.storefrontui.io/?path=/docs/components-atoms-image--common) 组件在图像上投射一个黑色覆盖，并显示作为子代传递的文本，您可以在前三个图像中看到:

```
<template>
  <SfSection titleHeading="Be In Tune" subtitleHeading="#OppOpgOpk" :levelHeading="2">
    <div class="px-14 grid w-full">
      <div class="a p-2">
        <SfImage          src="https://images.unsplash.com/photo-1556905055-8f358a7a47b2?ixid=MXwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHw%3D&ixlib=rb-1.2.1&auto=format&fit=crop&w=1050&q=80"
          :srcsets="[]"
          alt="Vila stripe maxi shirt dress"
          width=""
          height=""
        >
          A
        </SfImage>
      </div>
      <div class="b p-2">
        <SfImage
          class="object-cover"         src="https://images.unsplash.com/photo-1562157873-818bc0726f68?ixid=MXwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHw%3D&ixlib=rb-1.2.1&auto=format&fit=crop&w=564&q=80"
         :srcsets="[]"
          alt="Vila stripe maxi shirt dress"
          width=""
          height="100%"
        >
          B
        </SfImage>
      </div>
      <div class="c p-2">
        <SfImage
          class="object-cover"        src="https://images.unsplash.com/photo-1562157873-818bc0726f68?ixid=MXwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHw%3D&ixlib=rb-1.2.1&auto=format&fit=crop&w=564&q=80"
          :srcsets="[]"
          alt="Vila stripe maxi shirt dress"
          width=""
          height="100%"
        >
          C
        </SfImage>
      </div>
      <div class="d p-2">
        <SfImage       src="https://images.unsplash.com/photo-1556905055-8f358a7a47b2?ixid=MXwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHw%3D&ixlib=rb-1.2.1&auto=format&fit=crop&w=1050&q=80"
          :srcsets="[]"
          alt="Vila stripe maxi shirt dress"
          width=""
          height=""
        />
      </div>
    </div>
  </SfSection>
</template>
<script>
  import { SfSection, SfImage } from "@storefront-ui/vue";
  export default {
    components: {
      SfSection,
      SfImage,
    },
  };
</script>
<style>
  .a {
    grid-column: 1;
    grid-row: 1;
  }
  .b {
    grid-column: 1;
    grid-row: 2 / span 2;
  }
  .c {
    grid-column: 2;
    grid-row: 1 / span 2;
  }
  .d {
    grid-column: 2;
    grid-row: 3;
  }
</style>

```

## 结论

在本文中，我们学习了什么是店面 UI，如何设置它，以及如何使用 Tailwind CSS 添加可视化定制。尽管在撰写本文时，Storefront UI 还处于稳定的测试阶段，但它为您的项目提供了许多好处，比如响应组件和构建时优化。

我希望你喜欢这篇文章，如果你有任何问题，一定要留下评论。编码快乐！

## [LogRocket](https://lp.logrocket.com/blg/ecommerce-signup) :看看用户为什么不完成你的电子商务流程中的一个步骤的技术和 UX 原因。

[![](img/d60d88871d85e76e0dcca90f4bbaf78c.png)](https://lp.logrocket.com/blg/ecommerce-signup)

LogRocket 就像是一个网络和移动应用程序和网站的 DVR，记录你的电子商务应用程序上发生的一切。LogRocket 没有猜测用户不转化的原因，而是主动揭示了阻止你转化的根本原因，比如 JavaScript 错误或死点击。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

开始主动监控您的电子商务应用程序— [免费试用](https://lp.logrocket.com/blg/ecommerce-signup)。