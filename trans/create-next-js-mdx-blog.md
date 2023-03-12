# 创建一个 Next.js 和 MDX 博客- LogRocket 博客

> 原文：<https://blog.logrocket.com/create-next-js-mdx-blog/>

Markdown 是一种被开发人员广泛用于编写不同类型内容的语言，如帖子、博客和文档。如果您没有使用过它，那么很可能您已经看到了它的实际应用。比如 GitHub 和 npm 中的`README.md`文件就是用 Markdown 写的。

与常规的 HTML 或其他编写工具相比，Markdown 为编写和组织内容提供了一种简单的语法。例如，在降价中，我们可以说

```
## Hello World!

I am here to see [John Doe](https://johndoe.com)

```

但是在 HTML 中，相同的文本应该是:

```
<h2>Hello World!</h2>
<p>I am here to see <a href="https://johndoe.com">John Doe</a></p>

```

用 Markdown 写作感觉就像写纯文本，用一点点额外的细节来组织你的内容。这就是为什么 Markdown 主要被开发人员用来编写文档和文章。

但是，Markdown 也有局限性，其中之一就是编写交互式内容。

通常在我们的文章或文档中，我们可能想要添加一个交互式的一次性小部件来向读者演示一些东西，但是在 Markdown 中我们不能。

这就是 MDX 的用武之地。MDX 是一种使这成为可能的语言，因为它允许您在 Markdown 中使用 JSX。

因此，在这篇文章中，我们将看看如何使用这个工具来[在 Next.js 中构建这个博客](https://pressblog.vercel.app/)，而不使用 Gatsby 或 Strapi——一切从零开始。我们将涵盖:

如果你愿意跟随，这里是我的 GitHub 上的[代码。](https://github.com/Elijah-trillionz/pressblog-mdx-nextjs)

## 什么是 MDX？

在我们开始构建这个博客之前，让我们先简单介绍一下 MDX 是什么以及我们可以用它做什么。

在使用 MDX 之前，有必要熟悉 Markdown 和 JSX，因为 MDX 只是它们的组合。

### MDX 语法

MDX [没有新的或特殊的语法](https://blog.logrocket.com/getting-started-with-mdx-and-vue-js-nuxt-js/)，因为它只是在一个地方结合了 Markdown 和 JSX。此外，还可以保存扩展名为`.mdx`的 MDX 文件。

您可以动态地、交互式地使用降价，如导入组件或在降价中编写类似 XML 的语法:

```
import Head from 'next/head';

<Head>
  <title>Hello World - My Blog</title>
</Head>

# Hello World

```

在上面的例子中，我们从 Next.js 导入了一个组件`Head`,并用它来指定我们文章的标题。您还可以注意到带有一个`#`(在 markdown 中是一个`h1`)的“Hello World”消息。这是马克丹和 JSX 一起工作。

### 用 MDX 编写表达式

MDX 的 [v2 版本使得用 MDX 编写表达式成为可能，就像在 JSX 一样。下面是一个简单的例子:](https://blog.logrocket.com/whats-new-mdx-v2/)

```
# Hello world

I think 2 + 2 is {2+2}

```

当你想引用一个 JavaScript 变量、函数表达式、对象、数组时，这在文档或文章中会很有用。

### 在 MDX 中编写 ESM

MDX 还支持导入和导出。例如，下面的`BlogTitle`组件可以用作任何其他 JSX 组件:

```
import Head from 'next/head';

export const BlogTitle = () => <h3>Hello World</h3>

<Head>
  <title>Hello World - My Blog</title>
</Head>

# Hello World

<BlogTitle />

```

除了组件之外，对象、变量和函数等非组件也可以导出和导入:

```
import { year } from '../utils/date.js'

# Top Ten Languages To Learn In {year}

```

因为我们可以在 MDX 中使用表达式，所以我们可以很容易地将年份传递到我们的头中。把你传递的任何数据想象成 JSX 的表达式；现在你也可以在减价时这样做:

```
import { posts } from '../posts/node.js'

# A List of My NodeJS Posts

<ul>
  {posts.map(post => <li>{post.title}</li>)}
</ul>

```

也可以在 MDX 中创建一个变量，就像我们看到的函数组件(`BlogTitle`)一样，但是我们需要给它加上前缀`export`关键字:

```
export const languages = ['Java', 'JavaScript', 'Python']
export const year = new Date().getFullYear();

# Top Ten Languages in {year}

<ul>
  {languages.map(language => <li>{language}</li>)}
</ul>

```

在每个声明前面加上`export`可能很奇怪，但是好消息是你不必这样做——你可以简单地在别处声明一次，然后导入它。

有一个例外，您可能需要在降价中声明和使用一个对象。当你使用元数据时:

```
export const meta = {
  title: "Your article doesn't beat this",
  publishedOn: "January, 6th. 2022",
  slug: "your-article-doesnt-beat-this"
}

# {meta.title}

```

虽然这不是强制性的，但它有助于组织您的内容。你也可以简单地使用 Front Matter(稍后我们将在我们的博客中使用)。

关于 MDX 的一件有趣的事情是，它的文件被编译成 JSX 组件。他们的编译器(来自 MDX 的集成)将 MDX 文件(`.mdx`中的文件)和 Markdown 文件(`.md`中的文件)编译成 JSX 组件，这些组件可以用作任何其他组件。

假设我们将下面的例子保存为`article.mdx`:

```
import Head from 'next/head';

export const BlogTitle = () => <h3>Hello World</h3>

<Head>
  <title>Hello World - My Blog</title>
</Head>

# Hello World

```

我们可以这样导入它:

```
import Article, { BlogTitle } from 'article.mdx';

const MyBlog = () => {
  <Article />;
  <BlogTitle />;
};

export default MyBlog;

```

默认情况下，`article.mdx`文件被编译成 JSX 组件并导出，因此我们可以默认将其导入到`.js/.jsx/.mdx`文件中。同样，回想一下`BlogTitle`组件也是从`article.mdx`出口的。

### 在 MDX 中传递道具

“如果他们可以把它作为任何其他 JSX 组件，那么他们也应该能够通过道具，对不对？”所以我推测 MDX 开发者认为。

它们肯定有，因为可以通过 props 将数据传递给任何组件，包括您创建的组件和默认编译成组件的组件。

我们可以重构最后一个代码块，如下所示:

```
import Article, { BlogTitle } from 'article.mdx';
import { year } from '../utils/date.js';

const MyBlog = () => {
  <Article year={year} />;
  <BlogTitle />;
};

export default MyBlog;

```

我们的`article.mdx`现在看起来如下:

```
import Head from 'next/head';

export const BlogTitle = () => <h3>Hello World</h3>

<Head>
  <title>Hello World - My Blog</title>
</Head>

# Hello World

This year is {props.year}

```

请注意，MDX 中并不允许所有的 JSX 语法。它们之间也有例外，你可以在 [GitHub](https://github.com/micromark/mdx-state-machine#73-deviations-from-jsx) 上找到。

还有为不同文本编辑器和 ide 提供语言支持的[扩展，比如 MDX 的语法高亮。如果你使用的是 VS 代码，只需安装](https://mdxjs.com/docs/getting-started/#editor) [MDX](https://marketplace.visualstudio.com/items?itemName=silvenon.mdx) ，就万事大吉了。

介绍完了，让我们直接进入代码，看看 MDX 是如何工作的。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 用 MDX 创建博客

创建此博客时，我们将执行以下操作:

*   在其中一篇文章中使用小部件
*   使用 Next.js `getStaticProps`和`api`在主页上显示文章
*   将每篇文章开发为 MDX 文件
*   使用[封面内容](https://frontmatter.codes/docs/index)作为我们的文章元数据
*   仅使用 CSS 对我们的文章内容应用样式
*   使用 [highlight.js](https://highlightjs.org/) 进行语法高亮显示
*   对主页应用分页

如果您以前从未使用过 Next.js，只要您知道 React、Markdown 和 JSX，您仍然能够理解。

### 创建和配置 Next.js 应用程序

原谅我，如果这对你来说是显而易见的，但是我想试着把每个人都带上。

要创建 Next.js 应用程序，请运行以下命令:

```
npx [email protected]

```

系统将提示您命名应用程序；您可以给自己的项目取任何名称，但是对于本教程，我将把项目命名为`PressBlog`。

#### 安装依赖项

让我们现在就安装我们所有的依赖项。首先，运行下面的命令:

```
npm install @mdx-js/[email protected] @mdx-js/[email protected] gray-matter remark-frontmatter rehype-highlight @reach/tooltip @reach/disclosure

```

您必须使用`@next`来确保您使用的是当前版本的`mdx-js`。在撰写本文时，当前版本是 2.0.0。现在，让我们回顾一下这些依赖项的作用。

1.  `@mdx-js/[[email protected]](/cdn-cgi/l/email-protection)`是由 MDX 为 webpack bundlers 提供的集成，它将 Markdown 和 MDX 编译成 JavaScript。我们将在我们的`next.config.js`文件中配置它
2.  `@mdx-js/[[email protected]](/cdn-cgi/l/email-protection)`为我们的应用程序提供上下文，我们可以用它来包装我们的组件，并轻松设计我们的减价内容
3.  我们将在这个博客中使用 Front Matter，gray-matter 将把它解析成一个元数据对象
4.  `remark-frontmatter` : MDX 不支持前台内容，所以当我们在浏览器中以页面形式查看 MDX 文件时，我们需要使用这个包来忽略前台内容
5.  retype-highlight 允许我们使用 highlight.js 对任何代码块应用语法高亮显示
6.  提供了一个组件，我们将使用它在我们的博客文章中创建工具提示
7.  `@reach/disclosure`提供了一个组件，我们将使用它在我们的博客文章中撰写披露。

#### 配置 Next.js

要配置 Next.js 应用程序，请将以下内容复制到您的`next.config.js`中:

```
import remarkFrontmatter from 'remark-frontmatter';
import rehypeHighlight from 'rehype-highlight';

export default {
  webpack: (config, options) => {
    config.module.rules.push({
      test: /\.mdx?$/,
      use: [
        options.defaultLoaders.babel,
        {
          loader: '@mdx-js/loader',
          options: {
            providerImportSource: '@mdx-js/react',
            remarkPlugins: [remarkFrontmatter],
            rehypePlugins: [rehypeHighlight],
          },
        },
      ],
    });

    return config;
  },
  reactStrictMode: true,
  pageExtensions: ['js', 'jsx', 'md', 'mdx'],
};

```

注意，我们使用 ES2015 `import`和`export`语句，因为我们只能导入`remark-frontmatter`，而不能使用`require`语句。因此，你可以将`next.config.js`改名为`next.config.mjs`。

上面的代码简单地配置了 Next.js，所以我们可以使用`.mdx`文件作为 Next.js 中的页面。`options`参数是我们可以输入插件的地方([备注](https://remark.js.org/)和[重新类型](https://github.com/rehypejs/rehype#readme))。它们是不同的，分别工作:remark 插件解析 Markdown，而 rehype 插件解析 HTML。

另外，在`options`参数中，我们有`providerImportSource`来解析来自`@mdx-js/react`的 React 上下文提供者，这是我们之前安装的。

就在`webpack`方法的最后，我们传入了一些其他的 Next.js 配置。但是，让我们为 Next.js `Image`加载器添加一个配置:

```
export default {
  //..
  pageExtensions: ['js', 'jsx', 'md', 'mdx'],
  images: {
    loader: 'imgix',
    path: 'https://images.unsplash.com/',
  },
  //...
};

```

#### 在 MDX 中创建第一个 Next.js 页面

在`pages`目录下，创建一个名为`about.mdx`的新文件。现在，您可以编写任何降价或/和 JSX 内容，或者简单地复制粘贴以下内容:

```
import Head from 'next/head';

<Head>
  <title>Hello World - PressBlog</title>
</Head>

# Hello World

```

用`npm run dev`启动 Next.js 服务器，在浏览器中访问新的`/about` 页面。您应该会收到标题为“Hello World-press blog”的“Hello World”消息。

这通常会抛出 404 错误，但是多亏了我们在`next.config.mjs`中添加的配置，我们现在可以用 MDX 创建页面了。

现在我们已经设置好了一切，让我们开始组织我们的博客，好吗？

## 组织我们的 MDX 博客文件结构

你会如何组织你的文件？我们刚刚在 MDX 中创建了一个页面，对吗？那么，我们应该在 MDX 中创建所有的页面，然后在 JavaScript 中创建定制的组件吗？但是，如果我们也可以在 MDX 中创建定制组件，那么是什么阻止了我们在 MDX 中创建任何东西呢？

嗯，事实是，用 MDX 组织你的博客一开始会很混乱。现在你有了两个几乎可以做同样事情的工具，你应该问的问题是，你应该使用哪一个，什么时候使用？

要回答这个问题，我们需要首先理解 MDX 的目的不是在所有用例中取代 JSX。MDX 的目的是帮助我们在 Markdown 中轻松制作交互式的动态丰富内容。

因此，虽然 MDX 可以用来制作一个`header`组件，例如，只包含博客的名称和徽标以及一些链接，但我们不应该使用它，因为那只是对工具的浪费。

当你有很多内容要写的时候，你应该使用 MDX 而不是 JSX。在这样的博客中，我们的文章中只有大量的内容。这就是为什么在大多数 MDX 用例中，您很难在 components 文件夹中找到一个`.mdx`文件——因为他们不需要它。

既然我们决定在文章中只使用 MDX，我们现在必须决定文章的结构。我们前面已经看到，在 MDX 中创建页面是可能的。把我们所有的文章放在 MDX 的一个页面中不是更简单吗？

这意味着我们可以在 Next.js 应用程序的`pages`目录下有一个`posts`目录，我们可以简单地在这个目录中用 MDX 编写我们的所有帖子。

例如，我们的文章看起来像这样:

*   `pages/posts/what-is-react-native.mdx`
*   `pages/posts/where-to-learn-react-native.mdx`

传统上，或者说，在 Next.js 中，你会在`pages`目录外有一个`posts`目录(包含你在 Markdown 中的文章),在`pages`目录内有另一个`posts`目录，只有一个动态文件(路径)。

也就是比如 pages/posts/[slug]。js 并在[slug]中获取所需的 post。基于子弹的 js。

这仍然是可能的，但是它有点扼杀了 MDX 的有用性。在本文中，我们将只使用第一种方法，但是我还将向您展示如何从目录中获取和解析 MDX 文件，这基本上是第二种方法的工作方式。

在本教程结束时，我们的项目结构将是这样的(不包括默认情况下 Next.js 附带的未更改的目录和文件):

```
PressBlog
|
|___ next.config.mjs
|
|___ components
|     |___ Header.js
|     |___ MDXComponents.js
|     |___ MeetMe.js
|     |___ Meta.js
|     |___ PostItem.js
|
|___ layouts
|     |___ Layout.js
|
|___ api
|     |___ posts.js
|
|___ pages
|     |___ index.js
|     |___ about.mdx
|     |___ _app.js
|     |___ _document.js
|     |___ posts
|           |___ learn-react-navigation.mdx
|           |___ what-is-react-native.mdx
|
|___ scripts
|     |___ utils.js
|
|___ styles
|     |___ globals.css
|     |___ Header.module.css
|     |___ Home.module.css
|     |___ Markdown.module.css

```

## 使用 CSS 进行样式设计

在本教程中，我们将只使用 Next.js 样式方法；没有顺风，就没有自举。但是，可以使用[风格化组件](https://blog.logrocket.com/theming-in-next-js-with-styled-components-and-usedarkmode/)或者任何实用优先的框架。

现在，让我们将所有使用过的风格(最少的)应用到这个博客中。

首先，让我们添加`Header.module.css`，它只适用于我们的头。因此，将以下样式复制并粘贴到其中:

```
.header {
  padding: 10px 0;
  background-color: rgb(164, 233, 228);
}

.header > div {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.header li {
  list-style: none;
  display: inline-block;
  margin: 0 10px;
}

```

接下来，我们将更新已经存在的`globals.css`，因此您不必创建它，只需向它添加以下样式:

```
a {
  color: inherit;
  text-decoration: underline transparent;
  transition: 0.25s ease-in-out;
}

a:hover {
  text-decoration-color: currentColor;
}

.max-width-container {
  max-width: 680px;
  margin-left: auto;
  margin-right: auto;
  padding: 0 10px;
}

.max-width-container.main {
  padding: 50px 30px;
}

p {
  line-height: 1.6;
}

```

然后，应用`Home.module.css`样式(替换任何可用的样式)，这将只应用于主页:

```
.mainContainer {
  padding: 15px 30px;
  padding-top: 45px;
}

.articleList {
  margin-top: 55px;
}

.desc {
  color: #3b3b3b;
  font-style: italic;
  font-size: 0.85rem;
}

.postItem {
  margin-bottom: 40px;
}

.img {
  max-width: 100%;
  height: auto;
  border-radius: 50%;
}
.button {
  display: block;
  max-width: 300px;
  width: 100%;
  cursor: pointer;
  border-radius: 4px;
  margin: 0 auto;
  padding: 10px;
  border: none;
  background-color: #3b3b3b;
  color: #fff;
}

```

最后，使用`Markdown.module.css`，它仅适用于我们文章的内容:

```
.postTitle {
  border-bottom: 2px solid rgb(164, 233, 228);
  padding-bottom: 13px;
}

.link {
  color: rgb(0, 110, 255);
}

.tooltipText {
  background: hsla(0, 0%, 0%, 0.75);
  color: white;
  border: none;
  border-radius: 4px;
  padding: 0.5em 1em;
}

```

## 创建“关于”页面

现在，让我们在“关于”页面上工作。回想一下，我们已经创建了一个`about.mdx`页面；现在，我们可以将 Lorem ipsum 文本插入其中。但在此之前，我们先创建一个名为`MeetMe.js`的组件。该组件将包含一个图像和作者的简短描述。

通过将它创建为组件，我们也可以在主页上使用它。所以，继续在应用程序的根目录下创建一个`components`目录。然后，创建一个名为`MeetMe.js`的文件，将以下内容复制并粘贴到其中:

```
import Image from 'next/image';
import styles from '../styles/Home.module.css';

const MeetMe = () => {
  return (
    <div>
      <Image
        src='photo-1618077360395-f3068be8e001?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxzZWFyY2h8NHx8bWFufGVufDB8MnwwfHw%3D&auto=format&fit=crop&w=500&q=60'
        alt='john doe avatar'
        width={150}
        height={150}
        className={styles.img}
      />
      <p className={styles.p}>
        Hey, I am <strong>John Doe</strong>. I love coding. Lorem ipsum dolor
        sit, amet consectetur adipisicing elit. Reiciendis commodi numquam
        incidunt blanditiis quibusdam atque natus inventore sunt autem iusto.
      </p>
    </div>
  );
};

export default MeetMe;

```

这里发生的事情并不多；我们只是从 [next/image](https://blog.logrocket.com/next-js-automatic-image-optimization-next-image/) 中导入`Image`组件，并使用我们已经创建的`Home.module.css`样式将样式应用到段落和图像中。

现在我们已经设置了`MeetMe`组件，让我们在`about.mdx`中使用它:

```
import MeetMe from '../components/MeetMe.js';

<MeetMe />

#### Let's dive into more of me

Lorem, ipsum dolor sit amet consectetur adipisicing elit. Ea deserunt ab maiores eligendi nemo, ipsa, pariatur blanditiis, ullam exercitationem beatae incidunt deleniti ut sit est accusantium dolorum temporibus ipsam quae.

Lorem, ipsum dolor sit amet consectetur adipisicing elit. Ea deserunt ab maiores eligendi nemo, ipsa, pariatur blanditiis, ullam exercitationem beatae incidunt deleniti ut sit est accusantium dolorum temporibus ipsam quae.

Lorem, ipsum dolor sit amet consectetur adipisicing elit. Ea deserunt ab maiores eligendi nemo, ipsa, pariatur blanditiis, ullam exercitationem beatae incidunt deleniti ut sit est accusantium dolorum temporibus ipsam quae.

```

最后，在我们离开这个页面之前，让我们创建一个包含动态元标签和标题的`Meta`组件。因此，前往`components`目录，创建一个名为`Meta.js`的新文件，复制并粘贴以下内容:

```
import Head from 'next/head';

const Meta = ({ title }) => {
  return (
    <Head>
      <title>{title}</title>
      <meta
        name='keywords'
        content='react native, blog, John Doe, tutorial, react navigation'
      />
    </Head>
  );
};

export default Meta;

// let's set a default title
Meta.defaultProps = {
  title: 'PressBlog - Your one stop blog for everything React Native',
};

```

为了简单起见，我们将只使用静态元关键字和动态标题。在真正的博客中，你需要针对搜索引擎进行优化。next-seo 是一个很好的工具。

让我们将新组件`Meta.js`导入到`about.mdx`中，并像这样使用

```
{
  /* .. */
}
import Meta from '../components/Meta.js';

<Meta title='About John Doe - PressBlog' />;

{
  /* .. */
}

```

这样，我们可以保存文件并测试我们的 about 页面。

## 创建布局

接下来我们要做的是博客的布局。在我们继续之前，我们需要一个 header 组件，所以让我们创建它。前往`components`目录，创建一个名为`Header.js`的新文件，复制并粘贴以下内容:

```
import Link from 'next/link';
import styles from '../styles/Header.module.css';

const Header = () => {
  return (
    <header className={styles.header}>
      <div className='max-width-container'>
        <h2>
          <Link href='/'>PressBlog</Link>
        </h2>
        <ul>
          <li>
            <Link href='/about'>About</Link>
          </li>
        </ul>
      </div>
    </header>
  );
};

export default Header;

```

这里，我们使用来自`next/link`的`Link`组件。此外，我们使用了之前创建的`Header.module.css`样式。

然而，风格上有一点小小的变化。注意，我们使用了类名`max-width-container`，它不是来自`Header.module.css`。不是错别字；这个类名以前在`globals.css`中使用过，这是因为我们在全球范围内都需要它。

接下来，让我们在根目录下创建一个名为`layouts`的目录，并在其中创建一个名为`Layout.js`的文件:

```
import Header from '../components/Header';

const Layout = ({ children }) => {
  return (
    <div>
      <Header />
      <main className='max-width-container main'>{children}</main>
    </div>
  );
};

export default Layout;

```

在这里，我们导入刚刚创建的`Header`组件，并将其作为博客布局的标题进行传递。你可以用页脚做同样的事情，但是我们没有在这个博客中使用页脚。

现在，要将`Layout`组件应用到我们的应用程序中，请前往`pages/_app.js`并将`Layout`组件包裹在`Component`组件周围:

```
//..
return (
  <Layout>
    <Component {...pageProps} />
  </Layout>
);

//..

```

### 添加文章

在本节中，我们将创建两篇文章。我们稍后将在主页上使用这些文章。，但是现在，我们只是将它们作为单个页面来创建和测试。

让我们在`pages`目录下创建一个名为 posts 的新目录。在这个新目录中，创建以下 MDX 文件。

我们从`learn-react-navigation.mdx`开始:

```
---
title: React Navigation Tutorial
publishedOn: January, 6th. 2022
excerpt: React Navigation is a number one tool to learn in React Native. You would always use them; these are some of the best practices in using React Navigation
---

import Meta from '../../components/Meta';
import { Disclosure, DisclosureButton, DisclosurePanel } from "@reach/disclosure";

<Meta title='React Navigation Tutorial - PressBlog' />

# React Navigation Tutorial

React Navigation is one of the best things that happened to [**React Native**](https://reactnative.org)

## Benefits of using React Navigation

1\. It is cool to use
2\. It is simple to learn
3\. It has an extensive community
4\. And more and more

<Disclosure>
  <DisclosureButton as='div'>React Navigation won't be here forever. Click to find why</DisclosureButton>
  <DisclosurePanel>Nothing lasts forever, yeah! That's all I got to say</DisclosurePanel>
</Disclosure>

```

组件和它们的对应物仅仅展示了在 MDX 中使用组件和小部件，以及它们对你的文章有多么有用。我们的下一篇文章使用了工具提示，所以让我们通过使用`what-is-react-native.mdx`来检查一下:

```
---
title: What is React Native?
publishedOn: January, 7th. 2022
excerpt: Lorem, ipsum dolor sit amet consectetur adipisicing elit. Ea deserunt ab maiores eligendi nemo, ipsa, pariatur
---

import Meta from '../../components/Meta.js';
import Tooltip from '@reach/tooltip';
import '@reach/tooltip/styles.css';

<Meta title='What is React Native - PressBlog' />

# What is React Native?

React Native is a lovely language

<Tooltip label="It's not actually a language though">
  <button>Fact about React native</button>
</Tooltip>
### React Native App Example:

```js
const App = () => {
  return (
    <View>
      <Text>Hello World Example</Text>
    </View>
  );
};

export default App;
```

```

你可以在浏览器中测试你的每一篇文章，看看它是否有效。要进行测试，请使用 URL `/posts/what-is-react-native`或`/posts/learn-react-navigation`。请注意，包含我们的元数据的部分没有显示在浏览器中，这是一件好事，因为我们不希望我们的读者看到这一点。

这可以通过我们在`next.config.mjs`中安装和配置的`remark-frontmatter`来实现。我们编写的 JavaScript 代码没有样式化，尽管我们已经配置了`rehype-highlight`——这是因为我们需要一个 highlight.js 主题。我们也可以使用 cdnjs 上的 GitHub dark 主题，通过 HTML `link`标签将它添加到我们的应用程序中。

我们可以通过更新`_document.js`文件，在 Next.js 应用程序中添加全局和静态 HTML 链接标签。现在还没有，所以让我们创建一个。前往`pages`目录并创建一个新文件`_document.js`，然后粘贴以下内容:

```
import { Html, Head, Main, NextScript } from 'next/document';

const MyDocument = () => {
  return (
    <Html lang='en'>
      <Head>
        <link
          rel='stylesheet'
          href='https://cdnjs.cloudflare.com/ajax/libs/highlight.js/11.4.0/styles/github-dark.min.css'
        />
      </Head>
      <body>
        <Main />
        <NextScript />
      </body>
    </Html>
  );
};

export default MyDocument;

```

我们已经创建了一个[定制文档](https://nextjs.org/docs/advanced-features/custom-document)来呈现 Next.js 中的每个页面。现在，当您在 URL 处查看文章时，代码应该会高亮显示并进行样式化。

### 添加文章特定的样式

让我们只添加针对我们博客文章的样式。使用我们将使用的方法，我们将能够设计 MDX 中任何元素的样式。

注意，当我说元素时，我指的是当它编译到 JSX 时，例如，MDX 中的 `# Hello world`编译到 JSX 的`h1`。因此，使用这种方法，我们可以设计 MDX 中所有`h1`元素的样式。

方法是用来自`@mdx-js/react`的`MDXProvider`包装我们的应用组件，并提供一个组件对象来映射所需的元素。

因此，转到 components 目录，创建一个名为`MDXComponents.js`的新文件，并将以下代码复制并粘贴到其中:

```
import styles from '../styles/Markdown.module.css';

const MDXComponents = {
  p: (props) => <p {...props} className={styles.p} />,
  a: (props) => <a {...props} className={styles.link} />,
  h1: (props) => <h1 {...props} className={styles.postTitle} />,
};

export default MDXComponents;

```

这里，我们只为降价内容导入我们之前创建的样式。`MDXComponents`对象的每个键都是一个组件，对应于 MDX 编译成的任何内容。

现在，您可以为任何 JSX 元素创建自定义组件:

```
import { Sparky } from 'sparky-text'; // a dummy module - does not exist
import Link from 'next/link';
import styles from '../styles/Markdown.module.css';

const MDXComponents = {
  strong: ({ children }) => <Sparky color='gold'>{children}</Sparky>,
  p: (props) => <p {...props} className={styles.p} />,
  a: (props) => <Link {...props} className={styles.link} />,
  h1: (props) => <h2 {...props} className={styles.postTitle} />, // for some reasons I want h1 to be mapped, styled and displayed as h2
};

// please do not use this example, this is only a demonstration

```

现在，我们的`MDXComponents.js`没有效果。因此，要解决这个问题，请转到`pages/_app.js`并从`@mdx-js/react`导入`MDXProvider`，以及我们刚刚创建的`MDXComponents`组件。

将`MDXProvider`缠绕在`Layout`组件上，并将`MDXComponents`作为`MDXProvider`的组件支撑。综上所述，`your _app.js`应该是这样的:

```
import '../styles/globals.css';
import { MDXProvider } from '@mdx-js/react';
import MDXComponents from '../components/MDXComponents';
import Layout from '../layouts/Layout';

function MyApp({ Component, pageProps }) {
  return (
    <MDXProvider components={MDXComponents}>
      <Layout>
        <Component {...pageProps} />
      </Layout>
    </MDXProvider>
  );
}

export default MyApp;

```

## 创建主页

主页上有我们所有的文章。通过以`5`的增量复制可用的文章，我们将有十篇文章。这将有助于演示分页。

让我们从使用 [Node.js 文件系统和路径模块](https://blog.logrocket.com/mastering-node-js-path-module/)获取两篇可用文章开始。让我们前往`pages/api`目录并创建一个新文件`posts.js`。

在`api`目录中，我们可以为我们的应用程序构建 [API 路由](https://nextjs.org/docs/api-routes/introduction)。我们只需要一个路径来获取 MDX 中的所有帖子(在`pages/posts`目录中可用)。

我们将不会在 Next.js' `getStaticProps`函数中对该路径提出任何请求，原因是它所使用的页面不会在生产版本中预先呈现。Next.js 建议用`api route`编写的、打算在`getStaticProp`函数[中使用的服务器端代码应该用 getStaticProp 函数](https://nextjs.org/docs/basic-features/data-fetching/get-static-props#write-server-side-code-directly)编写。我们最终将需要用于分页的`posts api`路径。

这一切很快就会变得更加清晰。现在，只需知道获取文章将在一个`api`路径和一个`getStaticProps`函数中完成。

在根目录下创建一个`scripts`目录，并在其中创建一个`utils.js`文件。这个文件将包含一个获取文章的`getPosts`函数(和其他一些函数)。

这就是我们在`getPosts`函数中获取所有帖子的方法:

1.  使用 Node.js 系统模块的`readdirSync`和`readFileSync`方法获取`pages/posts`中的`.mdx`文件
2.  使用`gray-matter`将每个文件的前端解析成一个元数据对象
3.  使用文件名为每个文件生成一个 slug

现在，让我们看看代码。在`scripts/utils.js`中，复制并粘贴以下代码:

```
import fs from 'fs';
import path from 'path';
import matter from 'gray-matter';

export const getPosts = (pageIndex) => {
  const dirFiles = fs.readdirSync(path.join(process.cwd(), 'pages', 'posts'), {
    withFileTypes: true,
  });

  const posts = dirFiles
    .map((file) => {
      if (!file.name.endsWith('.mdx')) return;

      const fileContent = fs.readFileSync(
        path.join(process.cwd(), 'pages', 'posts', file.name),
        'utf-8'
      );
      const { data, content } = matter(fileContent);

      const slug = file.name.replace(/.mdx$/, '');
      return { data, content, slug };
    })
    .filter((post) => post);

  return posts
};

```

这是相当多的，但让我们通过每一行。

我们做的第一件事是导入我们的模块，它们是`fs`(来自 node . js)`path`(来自 node . js)`matter`(来自`gray-matter`)。

`readdirSync`获取`pages/posts`目录中的所有文件及其文件扩展名，并将其赋给`dirFiles`变量。

然后我们遍历`dirFiles`来过滤掉所有不是 MDX 的文件。在循环中，我们可以使用`readFileSync`方法读取每个文件的内容，并将内容作为参数传递给`matter`函数。

`matter`函数返回一个我们只需要`data`和`content`的对象。`data`是我们在每篇文章中使用 Front Matter 编写的元数据，`content`是文章的剩余内容。

最后，在循环中，我们从文件名(没有扩展名)生成一个 slug，并从我们的循环函数返回一个对象`data`、`content`和`slug`。

你要确保你没有异步地获得所有的帖子，这就是使用`readdirSync`和`readFileSync`的原因，因为它们都是同步的。

现在，让我们回到`api/posts.js`并创建一个函数来处理对这个路由的请求。复制并粘贴到`api/posts.js`中:

```
import { getPosts } from '../../scripts/utils';

export default function handler(req, res) {
  const posts = getPosts(2); // argument will change

  res.status(200).json(posts);
}
```

`handler`函数就像 Express.js 中的函数一样——`req`和`res`参数分别用于查看请求参数和处理响应。保存这个文件，我们以后会用到它。

现在，我们可以打开`pages/index.js`并清除所有标签(在`return`语句中)并导入语句(它们默认出现在每个 Next.js 应用程序中)。在`Home`函数之外创建一个新函数`getStaticProps`，并将下面的代码复制并粘贴到其中:

```
export const getStaticProps = () => {
  const posts = getPosts(1); // the argument has no effect yet

  return {
    props: {
      posts,
    },
  };
};

```

如果您使用过 Next.js，这非常简单。但是如果你没有，它是一个[函数，用于 Next.js](https://blog.logrocket.com/the-latest-features-in-next-js/#getstaticprops) 中的数据获取。Next.js 使用从导出的`getStaticProps`函数返回的属性在构建时预渲染我们的`index.js`页面。这个过程叫做[静态站点生成](https://blog.logrocket.com/ssg-vs-ssr-in-next-js/)。

您可以在这里执行大量的过滤和排序。例如，如果您想获得最近发表的 10 篇文章——而不是任何 10 篇——您可以用每篇文章返回的`publishedOn`元数据对其进行分类。

但是，请注意，您必须导出`getStaticProps`函数，否则它的行为就像任何其他函数一样。在这个函数中，您可以从 CMS 数据库中获取数据，因为从技术上来说，它运行在服务器上，而不是在客户机上。

Next.js 让我们可以在我们的`Home`函数中舒适地使用从`getStaticProps`返回的道具。这意味着我们在这个页面上的`Home`函数(`pages/index.js`)将把帖子作为道具传递给它:

```
const Home = ({ posts }) => {
  return (
    <>
      <Meta />
      <MeetMe />
      <Link href='/about'>More about me</Link>

      <div className={styles.articleList}>
        <p className={styles.desc}>Newly Published</p>
        {posts.map((post) => (
          <p key={post.slug}>{post.data.title}</p>
        ))}
      </div>
    </>
  );
};
export default Home;

```

我们使用`Meta`组件向页面添加 meta 标签。`MeetMe`组件显示作者的简短描述，其中有一个到 About 页面上完整描述的链接。

最后，我们循环通过作为来自我们的`Home`函数的道具的`posts`数组。您应该从`Home.module.css`导入`Home`功能中使用的组件和功能，包括`styles`。

至于现在，我们只是在一个段落中显示每篇文章的标题，这与我们想要的相差甚远。让我们继续创建一个名为`PostItem`的新组件。

为此，在`components`目录中创建一个名为`PostItem.js`的文件，复制并粘贴以下代码:

```
import Link from 'next/link';
import styles from '../styles/Home.module.css';

const PostItem = ({ post }) => {
  return (
    <div className={styles.postItem}>
      <h3>
        <Link href={`/posts/${post.slug}`}>{post.data.title}</Link>
      </h3>
      <p>{post.data.excerpt}</p>
      <Link href={`/posts/${post.slug}`}>Read more</Link>
    </div>
  );
};

export default PostItem;

```

现在，返回到`pages/index.js`并导入我们刚刚创建的`PostItem`组件。然后，将显示每个帖子标题的段落替换为`<PostItem key={post.slug} post={post} />`:

```
//..
<div className={styles.articleList}>
  <p className={styles.desc}>Newly Published</p>
  {posts.map((post) => (
    <PostItem key={post.slug} post={post} />
  ))}
</div>
//..

```

最后，保存文件并在浏览器中测试主页。这也是我们将应用于博客页面的原则。

对于大型数据集来说，一次加载所有文章确实不是一个好的做法。一个页面上有大量的数据会影响你的应用程序的性能，所以我们最好分块加载它们。例如，如果我们有十篇文章，我们可以每页加载五篇。

让我们在博客中演示一下。通过在`scripts/utils.js`中创建一个函数来复制我们现有的文章，从而创建十篇文章。将下面的代码粘贴在`getPosts`函数之前或之后。

```
const createMultiplePosts = (posts) => {
  const multiplePosts = [];

  posts.forEach((post) => {
    for (let i = 0; i < 5; i++) {
      multiplePosts.push(post);
    }
  });

  return multiplePosts;
};
```

我们遍历所有可用的文章，每篇文章生成五篇。

我们需要处理分页的函数也在这里，在`utils.js`中，所以粘贴下面的代码，在`createMultiplePosts`函数之前或之后。

```
const filterPostsByPageIndex = (posts, pageIndex) => {
  const postPerPage = 5;
  // get the total posts from page 1 to current page
  const totalPagePosts = +pageIndex * postPerPage;

  // get the total posts from page 1 to previous page
  const prevPagePosts = totalPagePosts - postPerPage;

  return posts.filter(
    (post, index) => index < totalPagePosts && index >= prevPagePosts
  );
};
```

每当有请求时，这个函数将帮助我们向页面添加五篇文章。

现在，让我们更新`getPosts`函数来返回一个复制和过滤后的帖子。回想一下，我们已经有一个`pageIndex`参数传入函数，所以我们可以用下面的语句替换`return`语句:

```
return filterPostsByPageIndex(createMultiplePosts(posts), pageIndex);
```

我们还必须更新`posts` API 路由，以便在有请求时请求页面索引作为查询。获取页面查询并将其添加到`api/posts.js`处理函数的开头:

```
  const { page } = req.query;
```

所以你会有:

```
export default function handler(req, res) {
  const { page } = req.query;

//...
```

最后，将`getPosts`参数从 2 改为`page,`，即:

```
//..
const posts = getPosts(page);
//..
```

下面是上面的工作原理:

1.  在`getStaticProps`函数和预呈现器中加载前五篇文章
2.  根据用户的请求，在页面上增量加载并显示另外五篇文章。对于这个博客，用户可以通过“加载更多”按钮提出这样的请求。

也就是说，`pages/index.js`中的`Home`函数将会有大量的变化。粘贴下面的代码来替换`Home`函数:

```
const Home = ({ posts }) => {
  const [filteredPosts, setFilteredPosts] = useState(posts);
  const [currentPageIndex, setCurrentPageIndex] = useState(1);

  const loadMorePosts = async () => {
    const res = await fetch(`/api/posts?page=${currentPageIndex + 1}`); // absolute url is supported here
    const posts = await res.json();

    setFilteredPosts((_posts) => [..._posts, ...posts]);
    setCurrentPageIndex((_pageIndex) => _pageIndex + 1);
  };

  return (
    <>
      <Meta title='PressBlog - Your one stop blog for anything React Native' />
      <MeetMe />
      <Link href='/about'>More about me</Link>

      <div className={styles.articleList}>
        <p className={styles.desc}>Newly Published</p>
        {filteredPosts.map((post, index) => (
          <PostItem key={index} post={post} />
        ))}
        <button onClick={loadMorePosts} className={styles.button}>
          Load more
        </button>
      </div>
    </>
  );
};

export default Home;
```

当点击`loadMorePosts`函数时，它将向我们的 API `posts` route 发出 get 请求，并获取接下来的五篇文章。总而言之，这是`pages/index.js`的页面:

```
import MeetMe from '../components/MeetMe.js';
import Link from 'next/link';
import PostItem from '../components/PostItem';
import styles from '../styles/Home.module.css';
import Meta from '../components/Meta';
import { useState } from 'react';

const index = ({ posts }) => {
  const [filteredPosts, setFilteredPosts] = useState(posts);
  const [currentPageIndex, setCurrentPageIndex] = useState(1);

  const loadMorePosts = async () => {
    const res = await fetch(`/api/posts?page=${currentPageIndex + 1}`); // absolute url is supported here
    const posts = await res.json();

    setFilteredPosts((_posts) => [..._posts, ...posts]);
    setCurrentPageIndex((_pageIndex) => _pageIndex + 1);
  };

  return (
    <>
      <Meta title='PressBlog - Your one stop blog for anything React Native' />
      <MeetMe />
      <Link href='/about'>More about me</Link>

      <div className={styles.articleList}>
        <p className={styles.desc}>Newly Published</p>
        {filteredPosts.map((post, index) => (
          <PostItem key={index} post={post} />
        ))}
        <button onClick={loadMorePosts} className={styles.button}>
          Load more
        </button>
      </div>
    </>
  );
};

export default index;

export const getStaticProps = () => {
  const posts = getPosts(1);

  return {
    props: {
      posts,
    },
  };
}
```

现在，保存文件并测试新的博客页面(在浏览器中访问`/`)。这样，我们就成功地用 Next.js 和 MDX 构建了一个简单的博客。

您可能想知道为什么我们在`loadMorePosts`函数中使用了`getPosts`函数，而不是创建一个 API 路由。嗯，这是因为`getPosts`函数是服务器端代码，如果用在客户端会出错。当然，你不能在生产中使用`getStaticProps`中的任何内部 API 路径。
因此，这是用静态站点生成实现分页的唯一简单方法。

## 结论

使用 Markdown 创建交互式博客或文档从未如此简单。如果你跟着做，你就离拥有自己的博客只有一步之遥了。您可以在部署之前决定是否添加更多功能。

如果您希望构建文档，本教程也可以帮助您，或者您可以简单地使用 [Nextra](https://nextra.vercel.app/) 。Nextra 使用 Next.js 和 MDX 为您的文档创建一个网站，当然，您可以用 MDX 编写所有内容。

总之，在本文中，我们学习了一个叫做 MDX 的有趣工具，我们可以用它做什么，比如写表达式和传递道具，以及用 MDX 和 Next.js 创建一个博客。

在创建这个博客的过程中，我们看到了如何配置 MDX 以在 Next.js 中工作，我们应该如何构建我们的应用程序，以及我们如何获取 MDX 文件并将其解析为元数据(来自前端)和内容的对象。

所以，就这样了。感谢阅读。

## [LogRocket](https://lp.logrocket.com/blg/nextjs-signup) :全面了解生产 Next.js 应用

调试下一个应用程序可能会很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪状态、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/nextjs-signup)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png)](https://lp.logrocket.com/blg/nextjs-signup)[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/nextjs-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你的 Next.js 应用上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用程序的性能，报告客户端 CPU 负载、客户端内存使用等指标。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

让您调试 Next.js 应用的方式现代化— [开始免费监控](https://lp.logrocket.com/blg/nextjs-signup)。