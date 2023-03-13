# 使用 Tailwind 和 Storybook 构建 Next.js 应用程序

> 原文：<https://blog.logrocket.com/building-next-js-app-tailwind-storybook/>

组件驱动的开发改变了我们创建 web 应用程序的方式。这种开发方法使得管理和维护应用程序代码库更加容易，同时也促进了开发人员之间的设计一致性和协作。

组件驱动开发的主要优势之一是组件隔离，它允许您独立地处理组件，而不会被周围的应用程序分散注意力。Storybook.js 是一个可以帮助解决这个问题的工具。

在本教程中，我们将介绍 Storybook 是什么，它是如何工作的，以及如何开始使用 Tailwind CSS 样式化在 Next.js 应用程序[中创建故事。我们将涵盖:](https://blog.logrocket.com/using-tailwind-css-in-production/)

要跟随本教程，您必须熟悉 Next.js 和 Tailwind CSS。以前的故事书经验也可能是有益的；然而，本文将尝试快速涵盖基础知识。

## 什么是故事书？

Storybook 是一个 web 工具，用于开发和测试独立于特定应用程序的 UI 组件。它允许您为每个组件创建“故事”,这些故事是组件在不同上下文中应该如何表现的示例:

![Example Button Component Created In Storybook With Preview Of Blue Rounded Button At Top Of Display, List Of Various Components In Left Menu Bar, And Controls For Button Component Encompassing Bottom Half Of Image](img/183f2836677a7f896afb102471f75199.png)

然后，您创建的故事可以在开发环境中的浏览器中显示，从而允许您轻松地测试和调试您的组件。

Storybook 包括几个有用的特性，比如添加注释、控制组件状态和检查可访问性规则的能力。

它还允许您与组件道具进行交互，并预览组件在不同场景和状态下的外观，从而允许您有效地进行测试和调试。

## Next.js、Storybook 和 Tailwind 入门

让我们通过运行下面的命令开始创建一个新的 Next.js 应用程序:

```
npx create-next-app next-storybook
```

接下来，将目录(`cd`)更改为新的应用程序，然后运行以下命令来安装 Tailwind CSS 及其依赖项:

```
npm install -D tailwindcss postcss autoprefixer
```

之后，运行下面的命令，根据顺风 CSS 的要求生成`tailwind.config.js`和`postcss.config.js`:

```
npx tailwindcss init -p
```

打开`tailwind.config.js`并将其代码更新为下面的代码，以便内容导出包括我们项目的`/pages`和`/components`目录中的文件。

```
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: [
    "./pages/**/*.{js,ts,jsx,tsx}",
    "./components/**/*.{js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
};
```

最后，打开默认的`styles/global.css`文件，并将如下所示的`@tailwind`指令添加到代码的顶部:

```
@tailwind base;
@tailwind components;
@tailwind utilities;
```

至此，我们已经成功地配置了 Next.js 应用程序，可以使用 Tailwind CSS，并且可以开始使用 Tailwind 的实用程序类。

### 将故事书添加到 Next.js 应用程序

只需运行以下命令，就可以将 Storybook 添加到 Next.js 应用程序中:

```
npx sb init
```

如果您使用 Next.js v11 和更高版本，建议[使用 webpack 5](https://blog.logrocket.com/new-features-in-webpack-5-2559755adf5e/) 以提高集成和性能:

```
npx sb init --builder webpack5
```

运行这些命令中的任何一个都将安装所需的依赖项，并为您的项目设置一个故事书环境。还应该创建了两个新文件夹— `.storybook`和`stories`。

`.storybook`目录包含一个`main.js`文件和一个`preview.js`文件。这些让我们配置我们的应用程序的外观和感觉，并允许我们根据自己的喜好设计我们的故事书环境。

`stories`文件夹包含默认的故事和文档。我们一会儿将学习如何创建我们自己的。

为了进行测试，使用以下命令启动 Storybook 界面:

```
npm run storybook
# OR 
yarn storybook
```

一个新的网页应该在`[http://localhost:6006](http://localhost:6006)`打开，您应该会看到这样的内容:

![Example Storybook Interface For How A Page Component Would Appear In The Frontend For A Logged Out User](img/2581d04c3b045afc7253aab6309b0331.png)

在我们开始创建自己的故事书之前，先在这个环境中玩一玩，以便更好地理解故事书中的故事和文档是如何工作的。

一旦你玩完了，继续删除`/stories`目录，然后更新`.storybook/main.js`的内容以匹配以下内容:

```
module.exports = {
  stories: [
    "../components/**/*.stories.mdx",
    "../components/**/*[email protected](js|jsx|ts|tsx)",
  ],
  addons: [
    "@storybook/addon-links",
    "@storybook/addon-essentials",
    "@storybook/addon-controls",
  ],
  webpackFinal: async (config) => {
    config.module.rules.push({
      test: /\.scss$/,
      use: ["style-loader", "css-loader", "postcss-loader", "sass-loader"],
    });

    return config;
  },
};
```

在上面的代码中，我们将 Storybook 配置为在`/components`目录而不是默认的 `/stories`目录中查找故事/文档，从而允许我们将组件及其各自的故事存放在同一个目录中。

### 为故事书添加顺风 CSS 支持

默认情况下，Storybook 不支持 Tailwind CSS。因此，如果我们[使用 Tailwind](https://blog.logrocket.com/using-storybook-to-develop-react-components-faster/) 实用程序类创建一个 React 组件，然后基于该组件创建一个故事，那么 Tailwind CSS 的更改将不会反映在 Storybook 环境中。

为了解决这个问题，我们在前面的代码中向`.storybook/main.js`配置文件添加了一个`webpackFinal`选项。这将指示 Storybook 使用顺风 CSS 所需的必要依赖项进行构建:

```
. . .
  webpackFinal: async (config) => {
    config.module.rules.push({
      test: /\.scss$/,
      use: ["style-loader", "css-loader", "postcss-loader", "sass-loader"],
    });

    return config;
  },
```

另外，用下面的代码更新`.stories/preview.js`文件:

```
import "../styles/globals.css";

import * as nextImage from "next/image";

Object.defineProperty(nextImage, "default", {
  configurable: true,
  value: (props) => <img {...props} />,
});

export const parameters = {
  actions: { argTypesRegex: "^on[A-Z].*" },
};
```

更新了该文件以导入全局样式表并支持`next-image`。因此，在我们的故事书环境中，Tailwind CSS 和`next-image`应该都能正常工作。

## 为我们的 Next.js 和 Tailwind 项目创作故事

要创建一个新的故事，我们需要首先创建故事所基于的组件。在项目根目录下创建一个新的`/components/Card`文件夹。然后，在这个新目录中，创建两个文件:

*   `index.js` —包含我们卡组件的主要代码
*   `card.stories.js` —将包含我们卡组件的故事代码

完成后，我们的项目文件结构应该如下所示:

```
.
├── . . .
├── components
│   └── Card
│       ├── index.js
│       └── card.stories.js
├── pages
│   └── index.js
├── public
└── . . .
```

在`Card/index.js`文件中，粘贴以下代码:

```
import PropTypes from "prop-types";

const Card = ({ title, showSub, background, imgUrl, children }) => {
  return (
    <div class="flex justify-center">
      <div
        class={`flex flex-col md:flex-row md:max-w-xl rounded-lg bg-${background} shadow-lg`}
      >
        <img
          class=" w-full h-96 md:h-auto object-cover md:w-48 rounded-t-lg md:rounded-none md:rounded-l-lg"
          src={imgUrl}
        />
        <div class="p-6 flex flex-col justify-center">
          <h5 class="text-gray-900 text-xl font-medium mb-2">{title}</h5>
          {showSub && (
            <p class="text-gray-500 text-sm mt-2">This is the card subtitle</p>
          )}
          <p class="text-gray-700 text-base mt-4 mb-4">{children}</p>

          <button
            type="button"
            class="inline-block px-6 py-2.5 bg-blue-600 text-white font-medium text-xs uppercase rounded shadow-md"
          >
            Some action
          </button>
        </div>
      </div>
    </div>
  );
};

export default Card;

Card.propTypes = {
  title: PropTypes.string,
  sub: PropTypes.string,
  showSub: PropTypes.bool,
  imgUrl: PropTypes.string,
  background: PropTypes.string,
};
```

使用 Tailwind CSS 实用程序类，我们在上面的代码中创建了一个组件，它接受`title`、`showSub`、`imgUrl`、`background`和`children`道具，并使用传递的道具生成一个基本卡片。

你可能已经注意到我们也使用了[反应](https://blog.logrocket.com/validate-react-props-proptypes/) `[PropTypes](https://blog.logrocket.com/validate-react-props-proptypes/)` [包](https://blog.logrocket.com/validate-react-props-proptypes/)来定义我们的道具类型。我们需要像这样安装它:

```
npm install prop-types
```

要创建卡片故事，请将以下代码粘贴到`card.stories.js`文件中:

```
import Card from "./index";

export default {
  title: "My Cards",
};

const Template = (arguments_) => <Card {...arguments_} />;

export const GreenCard = Template.bind({});

GreenCard.args = {
  title: "Hello World",
  showSub: false,
  background: "yellow-600",
  imgUrl: "https://path/to/some/image",
  Children: "Lorem ipsum dolor sit amet, consectetur adipisicing elit. Nulla, soluta?",
};
```

在上面的代码中，我们创建了一个名为“My Cards”的新故事，将故事模板绑定到我们之前创建的`Card`组件。

然后，我们创建了一个名为`GreenCard`的`Card`故事的新实例，并将上面的`GreenCard.args`值作为道具传递给我们的`Card`组件。

运行以下命令来重新启动故事书环境:

```
npm run storybook
```

一旦开始，您应该会看到我们的卡片故事现在已经创建好了，如下图所示:

![Example Card Component Created In Storybook With Controls Displayed For Card Elements Like Title, Background Color, Image Url, And Children](img/24c5e55318d716ce0346891a1c07b580.png)

您还应该注意到，当我们在控件部分进行更改时，我们的组件会自动更新。

此外，我们在菜单栏中有大量的自定义选项，用于测量组件的大小以及它在各种视窗和条件下的显示方式:

![Storybook Card Component Preview With Red Boxes Indicating Where Customization Options And Other Controls Can Be Found And Edited](img/eb074a5fd3d1b350d141eed8e338c422.png)

这就是全部了！我们能够在 Next.js 项目中安装 Storybook，并设置它与 Tailwind CSS 一起工作。

## 结论

在本文中，我们强调了组件隔离的重要性。我们还讨论了如何在 Next.js 应用程序中使用 story book——一种用于独立开发和测试 UI 组件的 web 工具，以及如何为这样的项目配置 Tailwind CSS。

你也可以在 GitHub 上找到本教程的完整的[源代码。](https://github.com/AsaoluElijah/next-tailwind-storybook)

## [LogRocket](https://lp.logrocket.com/blg/nextjs-signup) :全面了解生产 Next.js 应用

调试下一个应用程序可能会很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪状态、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/nextjs-signup)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png)](https://lp.logrocket.com/blg/nextjs-signup)[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/nextjs-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你的 Next.js 应用上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用程序的性能，报告客户端 CPU 负载、客户端内存使用等指标。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

让您调试 Next.js 应用的方式现代化— [开始免费监控](https://lp.logrocket.com/blg/nextjs-signup)。