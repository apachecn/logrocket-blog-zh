# 比较 Tailwind CSS 和 Bootstrap:是时候抛弃 UI 套件了吗？

> 原文：<https://blog.logrocket.com/comparing-tailwind-css-bootstrap-time-ditch-ui-kits/>

***编者按*** *:* *这篇文章于 2022 年 9 月 22 日更新，内容包括关于顺风 CSS 的好处，为什么应该使用它，如果顺风 CSS 比 Bootstrap 更容易和更受欢迎，以及顺风 CSS v3.0 版的一般更新信息。*

我们正在经历令人惊叹的网络平台和响应式设计的复兴。响应式用户界面通常使用 UI 工具包来实现，如 Bootstrap、Foundation、布尔玛和老式的媒体查询。

这些 UI 工具包使我们能够轻松地实现指令，用更少的代码实现我们所需要的精确的 UI 和适当的响应。但是我们做得对吗？

如果我们能够实现一个不受 UI 工具包规则约束的响应性 UI，会怎么样？有没有一种方法既能实现快速响应，又能保持我们定制的用户界面设计？对于重视高度可定制性的开发人员来说，Tailwind CSS 提供了比 Bootstrap 等 UI 工具包更大的灵活性。

在本指南中，我们将探索 Tailwind CSS 和 Bootstrap 之间的差异，回顾一些示例来展示使用实用程序优先的 CSS 框架相对于传统 UI 工具包的优势，并重点介绍不断发展的 Tailwind CSS 框架的一些最新重大变化。

## 什么是顺风 CSS？

根据[官方文档](https://tailwindcss.com/docs/what-is-tailwind)，Tailwind CSS 是一个实用程序优先的 CSS 框架，用于快速构建自定义用户界面。我认为这是一种很酷的方法，可以编写内联样式并实现一个很棒的界面，而无需编写自己的一行 CSS。

在我看来，大多数开发人员会发现 Tailwind CSS 有一点令人分心，那就是你的标记看起来比你想象的要繁忙得多。Tailwind CSS 不是第一个实用优先的 CSS 库，但它是目前最流行的。

## 为什么要用 Tailwind CSS？

与其他 CSS 框架不同，Tailwind CSS 是底层的、实用优先的，这意味着它为您提供了一组可以添加到 HTML 和样式中的类，所有这些都不需要用到 CSS。

Tailwind CSS 为开发人员提供了很大的灵活性和对组件外观和感觉的控制，而典型的 UI 工具包附带了几个预构建的组件，因此很难创建独特的 UI。它的实用程序类使得开发符合 UI 整体外观的可重用组件变得简单快捷。

## 使用顺风 CSS 的好处

与使用传统的 UI 工具包相比，使用 Tailwind CSS 有很多好处。让我们回顾一下一些重要的好处。

首先，顺风 CSS 给你一个更小的 CSS 文件大小。使用纯 CSS 来设计 web 应用程序的样式需要您在向 web 应用程序添加更多功能和组件时编写更多的 CSS。这会导致您的 CSS 文件变得越来越大，越来越重。使用 Tailwind CSS 实用程序类，您可以简单地创建和应用可重用的样式，因此您很少需要编写新的或任何 CSS。

接下来，您可以更少地担心破坏更改。与 CSS 不同，HTML 中的实用程序类是本地的。也就是说，您可以更新它们，而不必担心破坏 web 应用程序上的其他任何东西。然而，如果你使用传统的方法，对 CSS 进行修改可能会破坏你的 web 应用程序的不同部分。

Tailwind CSS 也是高度可定制的。这通常是一个非常灵活的框架。尽管它带有默认设置，但一个`tailwind.config.js`文件可以很容易地覆盖它。配置文件允许对调色板、样式、间距、主题等进行简单的调整。

接下来，我们有共同的效用模式。使用 Tailwind CSS，您可以避免命名类的工作。通用实用程序模式的可用性解决了各种困难，包括类规范、组织和级联等。实用程序类使得创建定制组件更加容易。

最后，您还可以删除未使用的 CSS 进行优化。PurgeCSS 可以用来优化 Tailwind CSS，这是一个显著的优势。PurgeCSS 可以通过扫描 HTML 和删除不必要的类来显著减小文件大小。

## Tailwind CSS 和 Bootstrap 有什么区别？

Bootstrap 是最流行的 HTML、CSS 和 JavaScript 框架，用于在 web 上构建响应迅速、移动优先的项目。另一方面，Tailwind CSS 是最流行的实用优先的 CSS 框架，用于快速 UI 开发。

TailwindCSS 和 Bootstrap 的主要[区别在于，Tailwind CSS 不是 UI 套件。与 Bootstrap、布尔玛和 Foundation 等 UI 工具包不同，Tailwind CSS 没有默认主题或内置 UI 组件。相反，它附带了预先设计好的小部件，您可以使用它们从头开始构建您的网站。](https://blog.logrocket.com/bootstrap-materialize-tailwind-css-which-is-best/)

Bootstrap 以其响应速度著称，而 Tailwind CSS 的支持者通常看重框架的可定制性。最适合你的选择取决于你的优先级和项目需求，但是让我们来谈谈为什么 Tailwind CSS 会迅速流行起来并得到更广泛的使用。

### 是 Tailwind CSS 还是 Bootstrap 更受欢迎？

Tailwind CSS 比 Bootstrap 应用更广泛吗？我们怎么能确定这一点？首先，让我们使用 GitHub 数据来确定两个 CSS 框架中哪一个更受欢迎。

在 GitHub 上，Tailwind CSS 有 [3.1k 叉和 61.1k 星](https://github.com/tailwindlabs/tailwindcss)，Bootstrap 有 [77.3k 叉和 160k 星](https://github.com/twbs/bootstrap)。由此我们可以做出一般的假设，Bootstrap 比 Tailwind CSS 更受欢迎。这里有另一个事实:根据认知标签上[CSS 状态](https://2021.stateofcss.com/en-US/technologies/css-frameworks/)的最新数据，自举似乎比跟风 CSS 更受欢迎。

### 顺风 CSS 比 Bootstrap 好吗？

像 Bootstrap 这样的框架已经将组件的创建抽象到了迫使开发人员只使用提供的可用模式的程度。其他 UI 工具包类型的框架也是如此。有些人可能会说用我们自己的 CSS 覆盖框架是一种选择，但是如果我们覆盖了很多，那么使用框架真的有意义吗？我们将把库拉进来，仍然编写我们自己的代码——这只是需要担心更多的文件，我们甚至没有节省时间。

我发现 Bootstrap 站点的另一个问题是它们看起来几乎总是一样的，所以这抑制了我们将创造力融入开发环境的能力。这是 Tailwind CSS 的优势之一:它能够轻松地构建复杂的用户界面，而不会鼓励任何两个站点看起来相同。

与 Bootstrap 相比，使用 Tailwind CSS 的另一个重要优势是，由于应用和网站是由预先设计好的小部件组成的，因此 Tailwind CSS 不会强加难以逆转的设计决策。使用 Tailwind CSS 意味着使用一组实用程序类，让您能够准确地处理您需要的内容。在我看来，这是一种创建用户界面的好方法，可以更灵活地发挥开发人员的创造力。

我非常欣赏的另一个优势——我相信每个前端开发人员也会欣赏——是永远不必担心一个元素的更改会影响另一个相关元素。在编辑器中，不再需要在 HTML 和样式表之间来回切换，也不再需要返回查看其他元素的名称。在我看来，这就是未来。

### 顺风 CSS 比 Bootstrap 容易吗？

Bootstrap 是一个基于组件的框架，这意味着它带有预构建的组件，并包括其他用于分层显示、间距等的实用程序。

另一方面，CSS 是一个实用优先的框架。使用 Tailwind CSS 类似于编写常规 CSS。与 Bootstrap 不同，它没有预构建的组件。

有了 Bootstrap 的预构建组件，您只需编写很少的 CSS。因此，设计中的细微变化可能会导致打嗝。使用 Tailwind CSS，您可以使用 Tailwind 的样式语法从头开始设置元素的样式。使用 Tailwind CSS 进行更改更容易，因为您只需删除一些 CSS 类。

那么 Tailwind CSS 和 Bootstrap 哪个更容易呢？这很大程度上取决于用例以及学习曲线。如果你已经掌握了 CSS，你就已经在掌握 Tailwind CSS 的路上了。因此，对于大多数项目来说，Tailwind CSS 是一个极好的选择。

## 如何安装顺风 CSS

虽然 CDN 是在项目中导入样式的好方法，但是使用 CDN 版本无法使用 Tailwind CSS 的许多功能。为了充分利用 Tailwind 的特性，您必须首先通过 npm 安装 Tailwind。

npm 上有[顺风，可使用 npm 或 Yarn 安装；](https://www.npmjs.com/package/tailwindcss)

```
# Using npm
npm install tailwindcss --save-dev

# Using Yarn
yarn add tailwindcss --dev

```

接下来，创建一个[顺风配置文件](https://tailwindcss.com/docs/configuration)。Tailwind 几乎完全是用普通 JavaScript 配置的。

为此，您需要为您的项目生成一个顺风配置文件。建议在项目的根目录下创建一个`tailwind.js`文件。CLI 实用程序可帮助您轻松处理这一问题:

```
# Using npm
npx tailwind init [filename]

# Using Yarn
yarn tailwind init [filename]

```

如果您是一个有经验的 Tailwind 用户，不需要配置文件中的注释，那么您可以在生成配置文件时使用`--no-comments`标志来删除它们。

## 效用至上是什么意思？

Tailwind CSS 是一个实用优先的库，这意味着与 Bootstrap 不同，Tailwind 不提供自动预样式组件。相反，它提供了实用程序类，帮助我们以特定的方式设计组件，并允许我们构建自己的类。

让我们看两个简单的例子来探讨这个概念。

### 示例 1:简单的按钮演示

参见钢笔 [框示例。](https://codepen.io/ekwunoobinna/pen/MRpNQj)by Ekwuno Obinna([@ ekwunobinna](https://codepen.io/ekwunoobinna))
on[CodePen](https://codepen.io)。

从上面的例子可以看出，用 Tailwind CSS 实现一个按钮组件是多么容易。

### 示例 2:简单的卡片演示

下面的代码是一个用 Tailwind CSS 框架创建的卡片的简单例子。如果你熟悉 CSS，你可能已经能够理解样式上的一些东西。一开始记住所有这些可能有点棘手，但是一旦你熟悉了语法，你就会好了。

下面的代码片段有一个容器，由于使用了`shadow-lg`类和用`bg-white`实现的白色背景，该容器有一个大屏幕阴影。您还会注意到`px-4`和`py-1`类——这些只是为了帮助在 x 轴和 y 轴上填充消息按钮。

```
<div class="bg-white mx-auto max-w-sm shadow-lg rounded-lg overflow-hidden"> 
  <div class="sm:flex sm:items-center px-6 py-4"> 
    <img class="block h-16 sm:h-24 rounded-full mx-auto mb-4 sm:mb-0 sm:mr-4 sm:ml-0" src="https://avatars2.githubusercontent.com/u/4323180?s=400&u=4962a4441fae9fba5f0f86456c6c506a21ffca4f&v=4" alt=""> <div class="text-center sm:text-left sm:flex-grow"> 
      <div class="mb-4"> 
        <p class="text-xl leading-tight">Adam Wathan</p> 
        <p class="text-sm leading-tight text-gray-400">Developer at NothingWorks Inc.</p> 
      </div> 
      <div> 
        <button class="text-xs font-semibold rounded-full px-4 py-1 leading-normal bg-white border-2 border-purple-400 text-purple-500 hover:bg-purple-600 hover:text-white">Message</button> 
      </div> 
    </div> 
  </div> 
</div>

```

下面是一个显示上述代码的代码笔:

参见 [CodePen](https://codepen.io) 上 chi dume David([@ philipsz-davido](https://codepen.io/philipsz-davido))
的 Pen[Tailwind Css 卡](https://codepen.io/philipsz-davido/pen/jOmwjxY)。

## 顺风 CSS 与自举:性能

默认的顺风配置带有 36.4KB 的 minified 和 g-zipped。与 22.1KB 的 Bootstrap 相比，Tailwind 重 14.3KB。您可能会想，“就性能而言，这真的是一条路吗？”

原因很简单:Tailwind 预装了许多选项和样式供用户选择，它打包了所有这些变化以减少编写自己的 CSS 的倾向。幸运的是，Tailwind 提供了一些策略，可以用来保持生成的 CSS 小而高效。

### 限制你的调色板

因为 Tailwind 中内置的实用模块使用了底层的插件系统，所以可以删除大量代码，使插件系统像在 Tailwind CSS 中注册新类一样。

这使得在项目中只包含我们实际需要的代码而忽略其他所有东西成为可能——不像 Bootstrap，其中有许多开销代码。这个升级特性将构建时间从 158 秒缩短到了 8 秒。

我们可以得到项目中所需的准确的颜色变化量，就像这样:

```
module.exports = {
  // ...

  textColors: {
    'black': colors['black'],
    'grey-darker': colors['grey-darker'],
    'grey-dark': colors['grey-dark'],
    'red-dark': colors['red-dark'],
    'red': colors['red'],
    'blue-dark': colors['blue-dark'],
    'blue': colors['blue'],
    // ...
  }
}

```

> 刚刚对 [@tailwindcss](https://twitter.com/tailwindcss?ref_src=twsrc%5Etfw) 构建过程进行了很好的 O(n)到 O(1)性能优化💪🏻你们这些怪物用 at-apply 申请一切都会开心的😇—亚当·瓦森(@亚当·瓦森)[2018 年 3 月 1 日](https://twitter.com/adamwathan/status/969013222471807014?ref_src=twsrc%5Etfw)

### 用 PurgeCSS 移除未使用的 CSS

Tailwind 也用 [PurgeCSS](https://github.com/FullHuman/purgecss) 移除未使用的 CSS，这是一个从项目中移除未使用的 CSS 的工具。它通过简单地将模板中可用的 CSS 类名与所使用的类名进行比较，然后移除未使用的 CSS 来实现这一点。

### 选择您需要的确切屏幕数量

Tailwind CSS 允许你选择你需要的屏幕尺寸。下面是定义更少的屏幕对输出的影响:

*   5 种屏幕尺寸(默认):36.4KB
*   4 个屏幕尺寸:29.4KB
*   3 种屏幕尺寸:22.4KB
*   2 个屏幕尺寸:15.4KB
*   1 屏幕尺寸:8.4KB

### 改进的顺风 CLI

Tailwind CSS 2.2 对 Tailwind CLI 工具进行了[重大优化。顺风 CLI 从一开始就进行了彻底的改进，以使其更具性能。](https://blog.tailwindcss.com/tailwindcss-2-2)

使用升级后的 CLI，Tailwind CSS 不需要安装或配置。您可以将子命令传递给 Tailwind CLI，以按照您的规范处理 CSS 文件。命令`npx tailwindcss`可以传递给 subargs，所以这消除了任何顺风配置；一切都可以通过命令行完成。

Tailwind CLI 可以在监视模式下运行。这意味着 Tailwind CLI 在后台运行，以观察 CSS 中的变化，并在检测到变化时重新构建 CSS。这是高性能的，因为它消除了每次更改后手动重建 CSS 的需要。

此外，Tailwind CLI 现在包括了对 [PostCSS](https://github.com/postcss/postcss) 插件的支持。通过创建一个`postcss.config.js`文件并包含 postscs 额外插件，你可以在你的`npx tailwind`命令中使用`--postcss`标志来包含 postscs 插件。

最后，修改后的 Tailwind CLI 可以设置为构建用于生产的 CSS。为生产而构建将导致顺风 CLI 移除未使用的 CSS。这导致了高度优化和更小的构建，由于文件大小占用空间小，从而带来了最佳性能。为了设置产品，我们使用`NODE_ENV=production`和`--minify`标志来压缩 CSS 以获得最佳构建。

## Tailwind CSS 有多灵活？

Tailwind 非常灵活，因为它允许您添加自己的实用程序，并提供一个指南来帮助您实现这一点。

让我们以一个简单的样式实现为例。正常的顺风设置是一个 CSS 文件，如下所示:

```
@tailwind preflight;

@tailwind components;

@tailwind utilities;

```

要覆盖默认实用程序，我们必须最后导入覆盖，以便首先应用它(这是一个常见的 CSS 规则):

```
@tailwind preflight;

@tailwind components;

@tailwind utilities;

.bg-cover-image {
  background-image: url('/path/to/image.jpg');
}

```

但是如果您使用的是`postcss-import`或 Less、Sass 或 Stylus 之类的预处理器，那么将您的实用程序保存在一个单独的文件中并导入它们将是最好的选择:

```
@tailwind preflight;

@tailwind components;

@tailwind utilities;

@import "leveledup-utilities";

```

对于响应式布局也可以这样做。只要操作得当，自定义实用程序将始终优先于带前缀的实用程序。

假设您在多个地方重用了某个按钮样式。为同一个组件重新键入相同类型的规范变得有点无聊。不用担心:Tailwind 有办法重用`@apply`的样式。

这里有一个例子:

```
<button class="btn-gray">
  Button 
</button> 

<style> 
.btn-blue { 
  @apply bg-gray text-white font-bold py-2 px-4 rounded; 
} 
.btn-gray:hover { 
  @apply bg-gray-dark; 
} 
</style>

```

> **注意:**、`hover:`、`focus:`和`{screen}:`效用变量不能直接混合。

## 2022 年的顺风 CSS:有什么新内容，我们将走向何方？

2021 年 6 月 17 日，Tailwind 发布了“有史以来功能最丰富的 Tailwind 版本之一”， [Tailwind CSS v2.2](https://blog.tailwindcss.com/tailwindcss-2-2) 。这个版本紧跟在 Tailwind CSS v2.1 之后，后者引入了[即时模式](https://tailwindcss.com/docs/just-in-time-mode)，这是一个新的 JIT 编译器，它在你编写模板时按需生成样式，而不是在初始构建时预先生成所有内容。

除了新的 Tailwind CLI 之外，最新的主要版本还引入了伪类支持、简写颜色不透明度语法、大量新的变体等等。查看 [Tailwind 的发布历史](https://github.com/tailwindlabs/tailwindcss/releases)以跟上最新的变化。

让我们重点介绍一下 Tailwind v2.2 中引入的几个特性。

### `first-letter`和`first-line`变体

这个特性是一个伪元素变体。它允许我们将`::first-letter`和`::first-line` CSS 伪选择器添加到元素中。

CSS 选择器选择元素文本节点的第一个字母并设置其样式。

```
<p class="first-letter:font-bold">
  This blog post represents everything you can find in today's work.
</p>

```

上面的代码将使`p`元素的文本节点的第一个字母比它的文本节点中的其他字母更粗。

```
<p class="first-letter:font-bold first-letter:text-4l">
  This blog post represents everything you can find in today's work.
</p>

```

上面的代码使`p`元素的文本节点的第一个字母比文本节点中的其他字母更粗更大。`::first-line`伪选择器定义了块级元素的第一行。

```
<p class="first-line:text-red-500">
  This blog post represents everything you can find in today's work.
</p>

```

这将把`p`元素的文本节点的第一行变成红色。这个特性用于 Tailwind CSS 的 JIT 模式。

### `before`和`after`变体

这个新特性使得样式化`before`和`after`伪元素成为可能。这些伪选择器在元素前后插入一些文本。

我们现在可以在 Tailwind 中使用`before`和`after`伪选择器。

这里有一个例子:

```
<p class="before:content-['B'] before:block before:bg-red-500">
  This blog post represents everything you can find in today's work.
</p>

```

上面添加了文本`B`添加到`p`元素的开头。内容的背景设置为深红色。让我们看看如何用同样的例子来影响`after`选择器:

```
<p class="after:content-['A'] after:block after:bg-red-500">
  This blog post represents everything you can find in today's work.
</p>

```

这将文本`A`添加到`p`元素的末尾。文本将以深红色显示。

这个特性在 Tailwind 的 JIT 模式下也是启用的。

### 选定的文本变体

Tailwind CSS 现在包含了一个`selection`文本变量，使您能够对元素上的选定文本进行样式化。

```
<p class="selection:bg-blue-500">
  This blog post represents everything you can find in today's work.
</p>

```

这将把`p`元素中所选文本的背景颜色设置为深蓝色。

像上面描述的其他新特性一样，`selection`文本变体在 JIT 模式下可用。

### 列出标记变体

`marker`变量向下编译到`::marker`伪选择器。它使您能够设置列表项标记的样式。这个选择器作用于`display:list-item`元素。

```
<ul class="marker:text-blue-500">
  <li>Reactjs</li>
  <li>Vuejs</li>
  <li>Angular</li>
  <li>Svelte</li>
  <li>Nextjs</li>
</ul>

```

上面的代码使`li`项的颜色变成深蓝色。Tailwind CSS 的新特性基本上添加了所有伪类，包括:

*   `only` ( `only-child`)
*   `first-of-type`
*   `last-of-type`
*   `only-of-type`
*   `target`
*   `default`
*   `indeterminate`
*   `placeholder-shown`
*   `autofill`
*   `required`
*   `valid`
*   `invalid`
*   `in-range`
*   `out-of-range`

要深入了解 Tailwind CSS v2.2 引入的新特性，请查看[发布历史](https://github.com/tailwindlabs/tailwindcss/releases/tag/v2.2.0)。

### 顺风 3.0 版的最新更新

Tailwind CSS v3.0 是 Tailwind CSS 的最新版本，这次重大升级带来了一些非常酷的功能。让我们探索一下:

*   稳定的即时引擎:Tailwind CSS v3.0 包括一个可靠的 JIT 引擎。JIT 引擎取代了旧的传统引擎。它有很多好处，例如:
    *   极快的构建时间
    *   启用所有默认变体
    *   改进的浏览器性能
*   Play CDN:要在应用程序中使用 Tailwind CSS 而不使用 NPM 或 Yarn 下载整个包，请使用 Play CDN。这个 CDN 不依赖于 CSS。而是基于脚本的 CDN。在将内容添加到 HTML 文件的头部之后，开始使用 Tailwind 的实用程序类来设计内容的样式
*   Scroll Snap API:Scroll Snap API 使开发人员能够直接在 HTML 中包含简单的滚动捕捉元素，从而让用户享受页面上的滚动捕捉
*   RTL 和 LTR 修改器:通过在多方向布局的顺风 CSS 3.0 中使用 RTL 和 LTR 修改器，设计者可以向左右两个方向移动元素
*   奇特的下划线样式:修改下划线的颜色、样式、粗细和偏移量
*   彩色背景上的彩色框阴影，增加了光泽、反射和更多的自然阴影

想了解更多的顺风 CSS v3.0 特性，请查看这篇博文。你也可以查看[的发布历史](https://github.com/tailwindlabs/tailwindcss/releases/tag/v3.1.8)，更深入地了解新特性。

## 结论

在本文中，我们讨论了顺风 CSS 和 Bootstrap 的所有事情。我们讨论了它们的一般差异，根据各种标准比较了两者，讨论了对 Tailwind 的更新，等等。

谢谢你坚持这么久！

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用情况、内存使用情况等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。