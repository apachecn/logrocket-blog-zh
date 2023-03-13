# 了解 Next.js 中的相对和绝对导入

> 原文：<https://blog.logrocket.com/understanding-relative-absolute-imports-next-js/>

构建一个基于组件的项目，就像 React 的 Next.js 框架，需要导入模块或组件文件来创建一个完整的用户界面。

当我们导入模块在文件中使用时，我们必须知道如何确定导入的模块相对于当前文件的位置。要做到这一点，我们必须了解什么是相对和绝对进口。

在这一课中，我们将讨论相对和绝对导入，并学习如何在 Next.js 应用程序中实现它们。我们将涵盖:

## Next.js 相对导入

在相对导入中，文件的导入路径应该相对于导入语句所在的位置。相对导入一般以`./`、`../`开头，以此类推。

让我们考虑一个典型的 Next.js 文件结构:

```
project
    ├── components
    │      ├── Footer.js
    │      ├── Header.js
    │      └── Layout.js

```

通常，当我们有像`Header.js`和`Footer.js`这样的包含可以跨多个页面共享的内容的文件时，我们会将它们导入到一个`Layout.js`文件中来组成网页。如果我们在`Layout.js`中正确导入这些组件文件，我们将在顶部得到如下导入:

```
import Header from './Header';
import Footer from './Footer';

```

文件扩展名默认为`.js`，所以我们在文件路径中忽略了它。

现在，让我们来分解一下`''`中的路径:

在路径中添加`./`意味着 JavaScript 将查找相对于“当前”目录的文件。这是因为`Header.js`和`Footer.js`文件与`Layout.js`文件位于同一个文件夹中——在本例中是`components`文件夹。

此外，我们在 Next.js 中有一个`pages/_app.js`文件，让我们使用`Layout`组件来包装顶级的`Component`。因此，让我们考虑以下更新的结构:

```
project
    ├── components
    │      ├── Footer.js
    │      ├── Header.js
    │      └── Layout.js     
    │── pages
    │      ├── _app.js
    │      ├── ...

```

现在，在`pages/_app.js`文件中导入`Layout.js`文件的`Layout`组件，如下所示:

```
import Layout from '../components/Layout';

```

在`pages/_app.js`文件内的相对文件路径中使用`../`可以让我们跳出当前目录——`pages`——这样我们就可以进入`components`文件夹来访问`Layout.js`文件。

让我们看另一个例子。假设我们有以下文件结构:

```
project
   ...
    │── pages
    │      ├── blog
    │      │    │── index.js
    │      │    └── ...
    │      ├── _app.js
    │      ├── ...    
    │── styles
    │      ├── Blog.module.css
    │      ├── ...

```

让我们分解一下如何将`Blog.module.css`文件导入到`pages/blog/index.js`文件中:

首先，我们将跳出当前目录——目录`blog`——进入父目录— `pages` —在我们的文件路径中使用`../`。

然后，从`pages`目录，我们也将进入它的父目录——根目录——所以相对路径现在看起来像这样:`../../`。

然后我们将从根目录进入`styles`目录，因此路径看起来像`../../styles`。

最后，我们可以访问`Blog.module.css`，所以我们有:`../../styles/Blog.module.css`。

如果我们将上述步骤付诸实施，导入和文件路径将如下所示:

```
import styles from '../../styles/Blog.module.css';

```

总之，我们在一个文件中使用`./`来引用一个与该文件位于同一目录中的模块。同样，我们在文件中使用`../`来引用父目录中的模块，在文件中使用`../../`来引用父目录中的模块，等等。

### 相对进口退税

相对进口并不总是友好的。事实上，他们可能会很困惑！正如我们在上面看到的，我们必须仔细跟踪我们当前所在的目录的级别。

此外，相对导入会导致糟糕的开发体验，尤其是在复杂的项目中。如果我们的应用程序增长，我们可能最终会得到一个看起来像这样的路径:

```
'../../../styles/Blog.module.css';

```

对于嵌套非常深的路径，该路径可能看起来更加复杂。

如果我们更改文件位置，问题可能会恶化，因为这可能需要我们更新文件路径。为了改善开发人员的体验，我们将学习如何配置 Next.js 项目来支持绝对导入。

## Next.js 绝对导入

绝对导入提供了一种直接导入模块的方法。此导入类型指定从项目根目录开始的路径。

现在，不用像在相对导入的情况下那样担心跟踪目录级别:

```
'../../../styles/Blog.module.css';

```

我们将有一个更干净的方法，看起来像这样:

```
'styles/Blog.module.css';

```

在上面的代码中，我假设项目根目录下存在`styles`目录。

我们还可以使用一个别名，如下所示:

```
'@styles/Blog.module.css';

```

让我们仔细看看在 Next.js 项目中使用绝对导入。

### 创建一个`jsconfig.json`文件

`jsconfig.json`文件让我们为一个项目指定一个基本目录。从 9.4 版本开始，Next.js 允许我们使用这个文件来标识根文件，并执行绝对导入所需的路径映射。

首先，我们将在项目的根目录下创建一个`jsconfig.json`,并添加以下配置:

```
{
  "compilerOptions": {
    "baseUrl": "."
  }
}

```

注意[如果我们使用的是 TypeScript](https://blog.logrocket.com/using-next-js-with-typescript/) ，我们会在`tsconfig.json`中添加代码。

`baseUrl`选项让我们指定基目录来解析模块。通过分配一个`.`，JavaScript 或 TypeScript 将在与`jsconfig.json`相同的目录中查找文件——即根目录。

因此，我们之前的相对导入示例如下所示:

```
import styles from '../../styles/Blog.module.css';

```

现在将如下所示:

```
import styles from 'styles/Blog.module.css';

```

同样，在上面的代码中，我假设项目根目录下存在`styles`目录。

注意，每当我们修改`jsconfig.json`或`tsconfig.json`文件时，我们必须重启 dev 服务器。

### 配置`baseUrl`

开发人员通常会在项目根目录下创建一个`src`文件夹来保存工作文件。让我们考虑以下结构:

```
project
   ...
    │── src
    │    ├── components    
    │    │── pages
    │    │── styles
    │    │── ...
    │
    │── jsconfig.json

```

在这种情况下，我们可以通过将`baseUrl`更新为指向`src`来告诉 JavaScript 或 TypeScript 从`src`文件夹开始查找文件:

```
{
  "compilerOptions": {
    "baseUrl": "src"
  }
}

```

文件中指定的`src`目录必须相对于项目根目录。现在，这个更新将允许从`src`目录绝对导入。

## 配置模块别名以简化 Next.js 绝对导入

对于包含文件的不同层次的深度嵌套目录的更重要的项目，我们可能希望创建定制的模块别名来匹配不同的目录，而不是只匹配基本目录。

考虑以下结构:

```
project
   ...
    │── src
    │    ├── components 
    │    │       │── homePage
    │    │       │      │── Hero.js
    │    │       │      │── Testimonial.js
    │    │       │      │── ...               
    │    │       │── blogPage
    │    │       │── ...  
    │    │── pages
    │    │     │── index.js
    │    │     │── ... 
    │    │── styles
    │    │── ...
    │
    │── jsconfig.json

```

如我们所知，通过使用相对导入，我们可以从`pages/index.js`内部导入`Testimonial.js`组件文件，如下所示:

```
import Testimonial from '../components/homePage/testimonial'

```

我们还学习了使用绝对导入来简化上述过程，因此我们有以下内容:

```
import Testimonial from 'components/homePage/testimonial'

```

现在，通过配置模块别名，我们可以进一步简化绝对导入，这样我们可以得到以下内容:

```
import Testimonial from '@homePage/testimonial' 

```

让我们探索一下如何使用`paths`选项来实现这一点。

### `paths`选项

在配置中添加一个`paths`选项可以让我们配置模块别名。考虑到上面的文件结构，我们将更新配置文件以包含`paths`条目。从根开始，`jsconfig.json`文件看起来像这样:

```
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@homePage/*": ["src/components/homePage/*"],
      "@blogPage/*": ["src/components/blogPage/*"],
      "@styles/*": ["src/styles/*"],
    }
  }
}

```

`paths`对象包含相对于`baseUrl`解析的条目。在上面的代码中，条目是相对于项目根的。

如果我们将`src`指定为`baseUrl`，那么路径条目将如下所示:

```
{
  "compilerOptions": {
    "baseUrl": "src",
    "paths": {
      "@homePage/*": ["components/homePage/*"],
      "@blogPage/*": ["components/blogPage/*"],
      "@styles/*": ["styles/*"],
    }
  }
}

```

在这种情况下，条目是相对于`src`目录的，而目录又是相对于项目根目录的。以上代码块都可以工作。

上述配置为中的所有文件创建路径别名:

1.  `homePage`文件夹使用`@homePage`
2.  `blogPage`文件夹使用`@blogPage`
3.  `styles`文件夹使用`@styles`

所以我们不用`components/homePage/Hero`，而是用`@homePage/Hero`。像 [VS Code 这样的代码编辑器也知道如何为路径自动完成提供适当的智能感知](https://blog.logrocket.com/understanding-typescripts-benefits-pitfalls/#excellent-code-completion)。

## 如何排除 Next.js 绝对导入不工作的故障

如果到目前为止您已经学习了这一课，那么您应该不会遇到绝对导入的问题。然而，我们将提到两个步骤，它们通常有助于解决或防止用户在为绝对导入配置 Next.js 时遇到的一些常见陷阱。

首先，我们必须确保我们的 Next.js 版本至少是 v9.4。然后，如果我们修改了`jsconfig.json`配置文件，我们必须总是重新启动 Next.js 项目。

遵循这两个步骤应该可以避免遇到 Next.js absolute imports 不工作的问题，或者帮助您解决现有的错误。

## 将 Next.js 项目从使用相对导入迁移到绝对导入

到目前为止，我们已经讨论了所有我们需要知道的关于相对和绝对进口的内容。这一节将实现我们在 Next.js 应用程序中学到的内容。

在另一篇博文中，我们讨论了如何将 RSS 提要添加到 Next.js 应用程序中。在那一课中，我们使用了一个 Next.js 项目，该项目使用相对导入来加载文件。我们将重构模块的路径以使用绝对导入。

让我们克隆这个项目，然后按照步骤来支持绝对导入。

### 克隆 Next.js 项目

使用以下命令克隆项目:

```
git clone https://github.com/Ibaslogic/nextjs-mdx-blog-rss

```

接下来，`cd`进入项目，并运行将依赖项安装到本地`node_modules`文件夹的命令:

```
cd nextjs-mdx-blog-rss

npm install
# or
yarn

```

最后，运行项目:

```
npm run dev
# or
yarn dev

```

该项目应该在 [http://localhost:3000](http://localhost:3000) 启动并运行。

### 项目结构

如果我们打开源代码，我们应该有一个更接近如下的文件结构:

```
nextjs-mdx-blog-rss
   ...
    ├── components
    │      ├── Footer.js
    │      ├── Header.js
    │      ├── Layout.js
    │      └── MdxComponents.js
    ├── pages
    │      ├── api
    │      ├── blog
    │      │    ├── [slug].js
    │      │    └── index.js
    │      ├── _app.js
    │      ├── ...
    │      └── index.js
    ├── posts
    ├── public
    ├── styles
    ├── utils
    │      ├── generateRSSFeed.js
    │      ├── mdx.js
    ├── menuItems.js
   ... 

```

### 添加对绝对导入的支持

如果我们浏览项目文件夹并打开文件，我们已经使用相对导入将文件内容包含在另一个文件中。为了添加对绝对导入的支持，我们将使用以下步骤。

首先，在项目的根目录中添加一个`jsconfig.json`文件，并添加以下代码:

```
{
  "compilerOptions": {
    "baseUrl": "."
  }
}

```

接下来，保存文件并重启 dev 服务器。

我们现在可以用上面的代码定义来自项目根的绝对导入。换句话说，现在所有的导入都是相对于项目的根的。所以，我们现在可以直接从根目录导入模块，比如`components`、`styles`和`utils`。

例如，在`blog/[slug].js`文件中查找`MDXComponents`导入，并使用相对导入进行更新，如下所示:

```
import MDXComponents from '../../components/MdxComponents';

```

…改为使用绝对导入，如下所示:

```
import MDXComponents from 'components/MdxComponents'; 

```

让我们对其他进口货也这样做。我们将在下一步中配置一个可选的模块别名，以进一步简化绝对导入。

让我们打开`jsconfig.json`文件并更新配置，这样我们就有了以下内容:

```
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@components/*": ["components/*"],
      "@styles/*": ["styles/*"],
      "@utils/*": ["utils/*"],
    }
  }
}

```

我们将保存文件并重启开发服务器。

现在，我们可以像这样指定一个路径:

```
import MDXComponents from '@components/MdxComponents';

```

而不是这个:

```
import MDXComponents from 'components/MdxComponents'; 

```

让我们再次为其他进口做同样的事情。如果我们想把工作文件放在可选的`src`文件夹中，我们需要遵循下面三个步骤。

在项目根目录下创建一个`src`文件夹，并将`components`、`pages`、`styles`和`utils`文件夹连同`menuItems.js`文件一起移动到`src`文件夹中。

接下来，更新配置`baseUrl`以指向`src`:

```
{
  "compilerOptions": {
    "baseUrl": "src",
    "paths": {
      "@components/*": ["components/*"],
      "@styles/*": ["styles/*"],
      "@utils/*": ["utils/*"],
    }
  }
}

```

重新启动开发服务器。

让我们确保项目按预期进行。看最后的[项目源代码这里](https://github.com/Ibaslogic/nextjs-mdx-rss-absolute-import)。

## 结论

正如我们所了解的，相对导入不需要像绝对导入那样的任何配置。然而，相对导入有时会令人困惑，并可能导致糟糕的开发人员体验。我们有了一个更精确简洁的方法，用绝对导入来导入 Next.js 中的模块。

在本课中，我们讨论了相对和绝对导入类型以及如何在 Next.js 项目中实现它们。我希望你喜欢阅读本指南。有什么想法可以在评论区分享。

## [LogRocket](https://lp.logrocket.com/blg/nextjs-signup) :全面了解生产 Next.js 应用

调试下一个应用程序可能会很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪状态、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/nextjs-signup)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png)](https://lp.logrocket.com/blg/nextjs-signup)[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/nextjs-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你的 Next.js 应用上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用程序的性能，报告客户端 CPU 负载、客户端内存使用等指标。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

让您调试 Next.js 应用的方式现代化— [开始免费监控](https://lp.logrocket.com/blg/nextjs-signup)。