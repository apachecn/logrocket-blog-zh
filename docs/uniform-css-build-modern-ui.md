# 使用统一 CSS 构建现代 UI 

> 原文：<https://blog.logrocket.com/uniform-css-build-modern-ui/>

前端 web 开发是一个不断变化的领域，我们使用的工具，如框架和库，使我们的开发过程更加有效和令人兴奋，也在不断发展。

CSS 框架是前端开发的一个重要方面。它们帮助我们快速一致地创建现代用户界面(UI ),并通过一组执行特定任务的 CSS 类提供一定程度的抽象。

在本文中，我们将介绍 [Uniform CSS](https://uniformcss.com/) ，一个可配置的实用类生成器和 CSS 框架，完全在[synthesized awesome 样式表(Sass)](https://blog.logrocket.com/how-to-write-reusable-css-with-sass/) 中开发。

## 什么是统一 CSS？

据其背后的团队称，Uniform CSS 是“完全在 Sass 中构建的完全可配置的实用程序生成器和 CSS 框架，因此您可以获得实用程序优先工作流的能力，而不会失去 Sass 的生产力。”

Uniform CSS 使用类似于 [Tailwind CSS](https://blog.logrocket.com/building-a-pricing-component-with-tailwind-css/) 或 [StemCSS](https://github.com/wearelighthouse/stemCSS) 的工作流，开发者使用框架提供的预定义 CSS 类设计令人惊叹的 UI。统一 CSS 的独特之处在于，它完全是用 Sass 构建的，并且完全可以用功能性 CSS 进行配置。

## 均匀 CSS 与顺风 CSS

那么，是什么让统一 CSS 不同于其他 CSS 框架，比如 Tailwind CSS？首先，它是完全可配置的，因为它允许开发人员在框架内定制一切。他们可以自定义前缀和分隔符，删除和扩展颜色，甚至修改语法和添加或删除属性。

我的意思是:

```
// All configs are done in the scss file 
// main.scss 
@use "uniform" as * with (
// add configurations here... 
 $config : (
   important : true,
   prefix : example,
   delimiter : "-",
   colors: (
      green-color : green,
      yellow-color : yellow
  ), 
   exclude : (
      padding,
      color,
      margin,
  ),
  include : (
      all 
      // includes all properties.
  )
 )
);
```

上面的代码会输出到我们的 CSS 文件中，在那里我们可以访问这些类，就像这样:

```
// main.css 
.example-bg-green-color {
   background-color : green !important; 
}

.example-bg-yellow-color {
   background-color : yellow !important; 
}
```

现在我们可以在 HTML 代码中使用这些实用程序。

因为统一 CSS 是建立在 Sass 之上的，所以它可以编译成普通的 CSS。但是它也提供了通常在 Sass 中使用的优势，比如 CSS 属性、混合、函数等等的供应商前缀。

而且，如果这些额外的好处还不够，当您使用统一 CSS 时，您不需要等待它来访问新的 CSS 属性——您可以直接将您自己的属性添加到您的 Sass 配置中。

下面举一个来自 [制服](https://uniformcss.com/docs/overview/#how-uniform-is-different) 的例子:

```
// main.scss
@use "uniform" as * with (
  $config: (
    utilities: (
      leading-trim: (
        responsive: true,
        shorthand: leading,
        properties: (leading-trim),
        variants: (
          trim: both
        )
      ),
      text-edge: (
        shorthand: text,
        properties: (text-edge),
        variants: (
          cap: cap alphabetic
        )
      )
    )
  )
);
```

这将编译为:

```
/* main.css */
.leading-trim {
       leading-trim: both;
 }
.text-cap {
       text-edge: cap alphabetic;
 }
```

## 安装统一 CSS

制服的设置非常简单。在我们的项目中有三种使用统一 CSS 的方法。让我们看一看每一个。

### 克隆 GitHub 存储库

只需将 GitHub 库克隆到您的项目文件夹中:

```
git clone https://github.com/ThinkUniform/uniformcss
```

现在我们需要将 Uniform 添加到我们的`.scss`文件中，编译它，并观察变化。以下是如何做到这一点:

```
// add this to your .scss file
@use "uniform" as *;

// now we compile using one of the build scripts made available to us or just do it using a sass build process of your choice

yarn uniform
```

以这种方式安装它使我们能够完全配置 Uniform 来满足我们的需求。

### 通过 npm 安装

如果您安装了 Node.js，那么只需使用以下命令:

```
npm install uniformcss
// add this to your .scss file
@use "uniform" as *;
```

现在，我们可以使用首选的 Sass 构建过程进行编译并观察变化，例如在 [Visual Studio 代码](https://code.visualstudio.com/)中的`watch Sass`扩展。以这种方式安装 Uniform 也使我们能够完全配置它来满足我们的需求。

### 从 CDN 安装

要快速使用 Uniform，只需将 CDN 链接添加到 HTML 文档的 head 标签中:

```
https://cdn.jsdelivr.net/npm/[email protected]/dist/uniform.min.css
```

使用此选项，如果您使用预编译版本，您将只能进行基本的调整，因此您将无法访问更广泛的定制选项。

要在使用 CDN 选项时配置 Uniform，只需将 CSS 变量定义为`:root`伪元素中的覆盖，如下所示:

```
:root{
  --font-sans : 'Poppins', sans-serif;
  --primary-700 : 255, 224, 249
}
```

现在，您可以在 HTML 文档中使用这些变量:

```
<h1 class="font-sans color-primary-700">Hi there!</h1>
```

## 在项目中使用统一的 CSS

现在让我们使用统一的实用程序类构建一个简单的卡组件。

因为这是一个小项目，我们将首先使用 CDN 安装 Uniform。但是，如果您想要额外的配置选项，您应该通过 npm 安装，因为 CDN 选项没有为我们提供太多的灵活性。

继续将下面的 CDN 链接添加到 HTML 文件的 head 标签中:

```
https://cdn.jsdelivr.net/npm/[email protected]/dist/uniform.min.css
```

我们现在可以将所有统一的 CSS 实用程序类应用到我们的 HTML card 组件中:

```
<main class="flex justify-content-center relative top-80 font-serif">
  <div class="flex flex-col justify-content-between shadow-lg w-448 radius-lg">
      <img src="https://images.unsplash.com/photo-1593990965215-075c1f918806?ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&ixlib=rb-1.2.1&auto=format&fit=crop&w=634&q=80" alt="A black man with dark shades on" class="h-288 object-cover object-center radius-lg">

      <div class="text-center p-12">
        <h2 class="ultrabold text-2xl p-10">Nelson Michael</h2>        
        <p class="text-sm">[email protected]</p>
      </div>

      <div class="flex justify-content-between m-auto w-320 p-20 text-center">
        <div>
          <h3 class="extrabold text-xl">25</h3>
          <p class="text-sm">Posts</p>
        </div>

        <div>
          <h3 class="extrabold text-xl">350</h3>
          <p class="text-sm">Following</p>
        </div>

        <div>
          <h3 class="extrabold text-xl">1.5k</h3>
          <p class="text-sm">Followers</p>
        </div>
      </div>
  </div>
</main>
```

这里有一个 [API 引用](https://uniformcss.com/api-reference/)，它显示了所有可用的类，这是我们最终构建的样子:

参见 [CodePen](https://codepen.io) 上纳尔逊国王( [@D_kingnelson](https://codepen.io/D_kingnelson) )
的 Pen[uniform-CSS-example](https://codepen.io/D_kingnelson/pen/MWmmZJg)。

我们能够通过简单地添加几个类来创建这个简单的卡片组件，而不需要接触我们的 CSS 文件。

这是另一个例子:

参见 [CodePen](https://codepen.io) 上纳尔逊国王( [@D_kingnelson](https://codepen.io/D_kingnelson) )
的笔[uniform-CSS-example-2](https://codepen.io/D_kingnelson/pen/ExmevMK)。

上面的例子展示了用 Uniform 创建可重用组件是多么简单。除了需要调整配置的时候，我们在不接触 CSS 文件的情况下完成了这个任务。

## 浏览器对统一 CSS 的支持

统一 CSS 目前支持 Chrome、Firefox、Edge、Safari 的稳定版本。它不适用于任何版本的 Internet Explorer，包括 Internet Explorer 11。

## 结论

统一 CSS 是一个伟大的前端开发工具，因为它提供了许多预定义的类，同时允许定制和随意添加和删除类的能力。我希望这篇教程对你有所帮助。

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。