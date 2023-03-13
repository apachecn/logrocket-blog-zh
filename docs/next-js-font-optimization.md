# Next.js 字体优化:添加自定义字体和谷歌字体

> 原文：<https://blog.logrocket.com/next-js-font-optimization/>

我们如何在 web 项目中加载字体可以决定用户体验的成败。使用系统字体，我们可以保证文本会在尽可能早的时间呈现。但是我们可能不需要这些字体类型，更重要的是，它们在操作系统之间并不一致。

另一方面，网络字体允许我们保持一致的外观。添加 web 字体可能是一项简单的任务。然而，如果我们关心性能优化，它也会变得乏味。

与由诸如 Google Fonts 的字体传送服务托管的 web 字体相关的一个问题是外部网络请求的成本。虽然我们可以通过自托管字体来大大降低这一成本，但我们也要处理布局变化的问题。

Next.js v13 引入了一个名为`@next/font`的字体系统来帮助抽象优化字体的复杂性。本课讲述如何使用这个字体系统在 Next.js 项目中添加自定义字体和 Google 字体，以及优化字体加载体验。

向前跳:

在这里看[演示项目。您可以与项目进行交互，以查看页面元素如何呈现不同的字体。下面是](https://next-js-fonts-optimization.vercel.app/)[项目的源代码](https://github.com/Ibaslogic/Next.js-fonts-optimization)。

现在，让我们开始吧。

## 在 Next.js 中添加字体

[在 Next.js 应用程序中添加像谷歌字体](https://blog.logrocket.com/how-to-use-web-fonts-css/)这样的网络字体可以简单到将字体交付服务生成的`<link>`标签嵌入到`pages/_document.js`文件的`Head`组件中。

### 创建您的项目

如果没有 Next.js 项目，可以使用以下命令创建一个项目:

```
npx [email protected] nextjs-fonts 
cd nextjs-fonts

```

然后，用`npm run dev`运行您的构建过程。

现在，在`pages/index.js`文件中添加下面的`Home`组件:

```
export default function Home() {
  return (
    <div>
      <h1>Home page</h1>
    </div>
  );
}

```

让我们通过将字体交付服务提供的`<link>`标签添加到 `pages/_document.js`文件的`[Head](https://github.com/Ibaslogic/Next.js-fonts-optimization/blob/main/pages/_document.js)` [组件中，从谷歌字体实现一个名为](https://github.com/Ibaslogic/Next.js-fonts-optimization/blob/main/pages/_document.js)[舞蹈脚本](https://fonts.google.com/specimen/Dancing+Script?query=danc&vfonly=true)的可变字体[，如下所示:](https://fonts.google.com/knowledge/glossary/variable_fonts)

```
<Head>
  {/* preconnect scripts... */}
  <link
    href="https://fonts.googleapis.com/css2?family=Dancing+Script:[email protected]&display=swap"
    rel="stylesheet"
  />
</Head>

```

之后，我们将更新`styles/globals.css`中的字体系列:

```
body {
  /* ... */
  font-family: 'Dancing Script', cursive;
}

```

输出如下所示:

![Browser Opened To Localhost In Light Mode With Text In Dancing Script Font Reading Home Page](img/d3efc04878eeb6147def3cf5327e4826.png)

这很简单，对吗？让我们在下一节看看这个实现的问题。

### 字体渲染过程

理想情况下，当 web 字体应用于 web 文档时，浏览器将在解析 HTML 后开始下载字体。这发生在文本呈现之前。

虽然我们希望字体能够快速加载和渲染，但事实并非总是如此。字体加载可能需要额外的时间，尤其是当它涉及到外部请求时，比如谷歌字体。

请参见下面的请求 URL:

![Browser Developer Pane Open In Dark Mode With Red Arrow Pointing To External Request Url For Font Rendering](img/d59bfee91b80ac593f3dcabac9a4a888.png)

因此，在下载字体完成之前，浏览器会退回到使用系统字体。

正如我们在 Google 字体中看到的，URL 中的`display=swap`参数将触发一个`font-display: swap`，指示浏览器使用备用字体。网页字体下载完成后，它会交换字体。

当字体改变时，这种切换会导致文本布局的改变。这种效果被称为[无样式文本的闪现](https://fonts.google.com/knowledge/glossary/fout)，也称为 FOUT。

我们可以使用三种策略来改善装载体验:

1.  通过从我们的域本地提供字体来消除外部网络请求
2.  使用`font-display: optional`防止布局移动，同时预加载字体
3.  使用 CSS `size-adjust`属性减少字体切换时的布局偏移

虽然我们可以手动实现这些优化策略，但我们将看看 Next.js 13 如何通过`@next/font`系统使这个过程变得更容易。

## 在 Next.js 中,`@next/font`系统如何帮助字体优化

系统是 Next.js 13 中引入的众多强大特性之一。这个字体系统大大简化了字体优化。它通过将任何 Google 字体与其他 web 组件(如 HTML 和 CSS 文件)一起包含在部署中来自动托管它们。

由于浏览器不涉及外部请求，因此加载速度更快。它使用 [CSS](https://blog.logrocket.com/css-font-size-adjust-how-to/) `[size-adjust](https://blog.logrocket.com/css-font-size-adjust-how-to/)` [属性](https://blog.logrocket.com/css-font-size-adjust-how-to/)实现了一种防止布局偏移或显著降低影响的策略。

### 安装`@next/font`

在 Next.js 中，`@next/font`是一个独立的包，所以它不会使内核膨胀。让我们将它安装在我们的项目中:

```
npm install @next/font

```

请确保您使用的是 Next.js v13 或更高版本。

## 用`@next/font`添加谷歌字体

Web 字体，包括来自 Google Fonts 的字体，可以是可变字体，也可以是不可变字体。可变字体的优势在于可以将一种字体的多种变体整合到一个文件中。与非可变类型相比，Next.js 推荐这种类型的字体。所以，让我们从这个开始。

### 在单页上添加字体

[来自谷歌字体的舞蹈脚本](https://fonts.google.com/specimen/Dancing+Script?query=danc&vfonly=true)是一种可变字体。如果您在之前我们讨论`<link>` tags 方法时将该字体添加到您的项目中，请确保在继续之前将其移除。

现在，让我们使用`@next/font`系统加载舞蹈脚本。在`pages/index.js`文件中，让我们从`@next/font/google`导入字体作为一个函数，并创建它的一个新实例:

```
import { Dancing_Script } from '@next/font/google';
const dancingScript = Dancing_Script();
console.log(dancingScript);

export default function Home() {
  return (
    // ...
  );
}

```

在代码中，我们将字体实例分配给一个`dancingScript`变量，并记录它以查看它返回什么。如果我们保存文件并重新加载页面，我们应该在 browser developer tools 面板和终端中看到返回的对象:

![Browser Developer Tools Panel Showing Returned Object For Dancing Script Variable](img/5ebdabba48e88695c04822f6405ad727.png)

字体加载器返回一个包含选项的对象，我们可以用它来应用字体样式。

### 应用字体样式

如上所示，返回的对象包含了`className`属性和一个`style`对象，我们可以用它来应用字体。使用`className`，我们可以将一种字体应用于一个元素，如下所示:

```
export default function Home() {
  return (
    <div>
      <h1 className={dancingScript.className}>Home page</h1>
    </div>
  );
}

```

我们可以保存文件并重启开发服务器。

如果我们检查前端，我们应该看到应用的字体。然而，由于谷歌浏览器的行为方式，可能会使用备用字体。

出于优化目的，每种字体都应用了一个`font-display`属性。默认情况下，应用于来自`@next/font`系统的字体的值是`font-display:optional`。这意味着如果字体在短期内不可用，将使用后备字体，不会发生交换。

我们可以指定一个值`font-display:swap`来在字体加载完成后立即切换到该字体，如下所示:

```
const dancingScript = Dancing_Script({ display: 'swap' });

```

这应该行得通。然而，在某些情况下，我们可能会注意到轻微的变化。

出于这个原因，我们将使用默认的`display: optional`属性，同时预加载字体，这样我们就可以省略 shift。这种方法产生最有效的结果。

要预加载字体，我们必须将它分成所需字符的子集—例如，`latin`。因此，让我们通过`subsets`选项添加一个子集:

```
const dancingScript = Dancing_Script({ subsets: ['latin'] });

```

该选项将从 web 字体中去除不需要的语言，并确保在`<head>`元素中添加所需语言的预加载链接。如果我们保存文件，我们应该会看到注入的链接:

![Head Element In Html File With Red Arrow Pointing To Injected Preload Link For Dancing Script Font](img/40e40f09e504e8d5f205124afee7956f.png)

预加载提示浏览器应该安排字体提前下载，Next.js 将确保资源可供使用。

添加后，该字体现在应该也适用于 Chrome 浏览器。下图还显示，该请求来自我们的基础设施，而不是来自交付服务:

![Browser Developer Tools Panel With Red Arrow Pointing To Updated Request Url Showing Request Now Comes From Within Infrastructure](img/3d8460df2f64efe4c5eb87e86a05e7d6.png)

请记住，有时`@next/font`中的默认`display: optional`会导致 Chrome 浏览器出现问题。在我们手动刷新 chrome 选项卡之前，应用的字体可能会在导航离开后恢复到后退状态。我们将在本课稍后讨论这个问题，并学习如何克服它。

### 添加多种字体

使用`@next/font`添加多个 Next.js 字体就像定义多个字体实例并将自动生成的类应用到文档元素一样简单:

```
import { Dancing_Script, Oswald } from '@next/font/google';

const dancingScript = Dancing_Script({ subsets: ['latin'] });
const oswald = Oswald({ subsets: ['latin'] });

export default function Home() {
  return (
    <div className={oswald.className}>
      <h1 className={dancingScript.className}>Home page</h1>
      <p>This paragraph uses another font</p>
    </div>
  );
}

```

在上面的代码中，我们从 Google 导入了一个`Oswald`变量字体，并将其应用于包装元素，使其可用于子元素。包装内的段落将继承该字体。同时，标题元素上的字体将覆盖包装字体。

请参见下面的输出:

![Browser Open To Localhost With Title Text In Dancing Script Font Reading Home Page Above Paragraph Text In Oswald Font. Developer Tools Panel Is Open With Red Arrow Pointing To Overridden Wrapper Font In H1](img/28485cfbdd029ff57bf357d05170a581.png)

### 使用`style`道具

我们也可以使用字体加载器返回的`style`选项，通过`style`属性应用字体。以下代码使用`style`选项将字体应用于包装元素:

```
export default function Home() {
  return (
    <div style={oswald.style}>
      <h1 className={dancingScript.className}>Home page</h1>
      <p>This paragraph uses another font</p>
    </div>
  );
}

```

上面的代码会给我们和以前一样的结果。

### 不可变字体

与可变字体不同，非可变字体需要一个`weight`选项。`weight`可以是一个字符串或一组值。

Merriweather 就是一个[非可变字体的例子，它也可以通过 Google Fonts 获得。让我们从导入它开始:](https://fonts.google.com/specimen/Merriweather)

```
import {
  // ...
  Merriweather,
} from '@next/font/google';

```

然后，向字体实例添加一个必需的`weight`选项，并将生成的类应用于文档元素:

```
const merriweather = Merriweather({
  weight: ['300', '400', '700'],
  style: ['normal', 'italic'],
  subsets: ['latin'],
});

export default function Home() {
  return (
    // ...
    <p className={merriweather.className}>
      This text uses a non variable font
    </p>
    // ...
  );
}

```

我们还在 font 实例中添加了一些可选的`style`值。保存文件，字体应该适用。

### 添加全局字体样式

为了在整个 web 文档中应用字体，我们将在`pages/_app.js`文件中导入字体，并将生成的类传递给`Component`包装元素，如下所示:

```
import { Raleway } from '@next/font/google';
const raleway = Raleway({ subsets: ['latin'] });

function MyApp({ Component, pageProps }) {
  return (
    <div className={raleway.className}>
      <Component {...pageProps} />
    </div>
  );
}

export default MyApp;

```

在上面的代码中，我们导入了一个`Raleway`字体，也是可变字体。因此，在配置中添加重量选项是可选的。

### 在`<head>`中注入字体

另一种在文档中应用字体的方法是在`<head>`中注入字体。在`pages/_app.js`文件中，我们可以在`Component`前使用`<style>`标签来应用全局字体:

```
function MyApp({ Component, pageProps }) {
  return (
    <>
      <style jsx global>{`
        html {
          font-family: ${raleway.style.fontFamily};
        }
      `}</style>
      <Component {...pageProps} />
    </>
  );
}

export default MyApp;

```

记住，我们可以从`style`对象访问字体系列。

### 用 CSS 变量语法声明全局字体

CSS 变量的语法提供了在样式表中声明多种字体的灵活性。下面的代码在`pages/_app.js`文件中导入了两种不同的字体，然后我们用它来声明全局 CSS 变量:

```
import { Raleway, IBM_Plex_Sans } from '@next/font/google';

const raleway = Raleway({ subsets: ['latin'] });
const ibmSans = IBM_Plex_Sans({
  weight: '700',
  subsets: ['latin'],
});

function MyApp({ Component, pageProps }) {
  return (
    <>
      <style jsx global>{`
        :root {
          --raleway-font: ${raleway.style.fontFamily};
          --ibmSans-font: ${ibmSans.style.fontFamily};
        }
      `}</style>
      <Component {...pageProps} />
    </>
  );
}

export default MyApp;

```

代码中的`:root`伪类将选择根元素，即`<html>`。

现在，我们可以在 CSS 文件中应用 CSS 变量。以下代码应用了一个`styles/globals.css`文件中的变量:

```
body {
  font-family: var(--raleway-font);
}

h1, h2, h3 {
  font-family: var(--ibmSans-font);
}

```

### `variable`键选项

在创建字体实例时，我们还可以添加一个`variable`键，让我们声明一个 CSS 变量。这个选项让我们不用使用全局的`<style>`语法就可以轻松地将字体用于 Tailwind CSS 和 CSS 模块。

下面的代码通过`variable`键选项声明了一个名为`--antonio-font`的 CSS 变量:

```
const antonio = Antonio({
  subsets: ['latin'],
  variable: '--antonio-font',
});

```

如果我们记录结果，我们应该看到返回的对象包含一个`variable`属性，它将允许我们访问字体:

![Browser Developer Tools Panel Showing Returned Object For Antonio Font Variable](img/3f1f4c9cbd92e2da7db57f0d54521c57.png)

我们现在可以通过容器元素上的`className`属性来应用变量名。如果我们在`pages/_app.js`文件中声明了一个全局字体，`Component`包装器应该是这样的:

```
<div className={antonio.variable}>
  <Component {...pageProps} />
</div>

```

从这里开始，我们可以在 Tailwind 中使用字体——你将在本课后面看到——或者在 CSS 模块中使用[。对于 CSS 模块，假设我们有一个使用该模块的`About`页面组件，如下所示:](https://blog.logrocket.com/a-deep-dive-into-css-modules/)

```
import styles from './About.module.css';

const About = () => {
  return (
    <div>
      <h1>About page</h1>
      <p className={styles.text}>This is about page content</p>
    </div>
  );
};

export default About;

```

我们可以使用之前声明的 CSS 变量来访问`About.module.css`文件中的字体，如下所示:

```
.text {
  font-family: var(--antonio-font);
  font-style: italic;
}

```

## 在 Next.js 中添加自定义字体

在某些场景中，我们想要在 Next.js 中使用的字体可能不是来自 Google Fonts。相反，它可以是我们创建的自定义字体，我们购买的字体，或者我们从另一个字体网站下载的字体。

通过使用`@next/font/local`配置它们，我们可以在本地使用这些字体中的任何一种。在本节中，我们将通过从字体松鼠下载一个名为[库珀休伊特](https://www.fontsquirrel.com/fonts/cooper-hewitt)的字体来演示如何做到这一点。

库珀·休伊特字体没有更现代、压缩更好的格式。因此，我们的第一步是使用[字体](https://www.fontsquirrel.com/tools/webfont-generator) [S](https://www.fontsquirrel.com/tools/webfont-generator) [奇洛](https://www.fontsquirrel.com/tools/webfont-generator)[W](https://www.fontsquirrel.com/tools/webfont-generator)[EB Font](https://www.fontsquirrel.com/tools/webfont-generator)[G](https://www.fontsquirrel.com/tools/webfont-generator)[生成器](https://www.fontsquirrel.com/tools/webfont-generator)对其进行转换。之后，我们将在我们项目的`[public/fonts](https://github.com/Ibaslogic/Next.js-fonts-optimization/tree/main/public/fonts)` [文件夹中添加字体的`bold`和`normal`粗细变化:](https://github.com/Ibaslogic/Next.js-fonts-optimization/tree/main/public/fonts)

![Project File Structure With Public Folder Open To Show Fonts Subfolder, Which Is Also Open And Contains Two Cooper Hewitt Font Variations](img/a1d23a199967ba63466681f6e7f48427.png)

### 使用本地配置的字体

就像在 Next.js 中使用 Google 字体一样，我们可以加载整个文档或特定页面的本地字体。为了加载站点范围内的本地字体，我们将从`pages/_app.js`文件中的`@next/font/local`导入字体加载器:

```
import localFont from '@next/font/local';

```

之后，我们将定义一个新的实例，将本地字体文件的`src`指定为一个对象数组:

```
const cooper = localFont({
  src: [
    {
      path: '../public/fonts/cooperhewitt-book-webfont.woff2',
      weight: '400',
    },
    {
      path: '../public/fonts/cooperhewitt-bold-webfont.woff2',
      weight: '700',
    },
  ],
});

```

每个对象都代表特定粗细的字体。如果我们使用可变字体，我们可以将`src`指定为字符串，如下所示:

```
const variableFont = localFont({ src: '../public/fonts/my-variable-font.woff2' });

```

从这一点来看，我们如何应用字体样式与前面讨论的 Google 字体的方法是一样的。我们可以使用`className`语法、`style`或 CSS 变量。

让我们用`<style>`语法为本地字体声明一个 CSS 变量:

```
function MyApp({ Component, pageProps }) {
  return (
    <>
      <style jsx global>{`
        :root {
          /* ... */
          --cooper-font: ${cooper.style.fontFamily};
        }
      `}</style>
      <Component {...pageProps} />
    </>
  );
}

```

我们现在可以在组件样式表中使用`--cooper-font` CSS 变量。假设我们有一个`Contact`页面组件，如下所示:

```
import styles from './Contact.module.css';

const contact = () => {
  return (
    <div className={styles.contact}>
      <h1>Contact page</h1>
      <p>This is contact content uses a local font</p>
    </div>
  );
};

export default contact;

```

我们可以使用 CSS 变量来访问`Contact.module.css`文件中的字体，就像这样:

```
.contact > *{
  font-family: var(--cooper-font);
}

```

## 使用 Tailwind CSS 向 Next.js 添加字体

为了将`@next/font`包[与 Tailwind CSS](https://blog.logrocket.com/how-to-use-custom-fonts-tailwind-css/) 一起使用，我们将使用 CSS 变量语法。幸运的是，我们已经在`pages/_app.js`文件中声明了几个 CSS 变量。

回想一下之前，我们在`<head>`中注入了以下内容:

```
<style jsx global>{`
  :root {
    --raleway-font: ${raleway.style.fontFamily};
    --ibmSans-font: ${ibmSans.style.fontFamily};
    --cooper-font: ${cooper.style.fontFamily};
  }
`}</style>

```

我们还用`variable`选项定义了一个 CSS 变量名，如下所示:

```
const antonio = Antonio({
  subsets: ['latin'],
  variable: '--antonio-font',
});

```

我们可以使用这些 CSS 变量声明中的任何一个，用 Tailwind CSS 添加 Next.js 字体。让我们在下一节看看如何实现。

### 正在配置`tailwind.config.js`文件

我们将 CSS 变量作为字体系列添加到 Tailwind CSS 配置文件中:

```
const { fontFamily } = require('tailwindcss/defaultTheme');

/** @type {import('tailwindcss').Config} */
module.exports = {
  content: [
    './pages/**/*.{js,ts,jsx,tsx}',
    './components/**/*.{js,ts,jsx,tsx}',
  ],
  theme: {
    extend: {
      fontFamily: {
        antonio: ['var(--antonio-font)', ...fontFamily.sans],
        ibm: ['var(--ibmSans-font)', ...fontFamily.sans],
        cooper: ['var(--cooper-font)', 'ui-serif', 'Georgia'],
      },
    },
  },
  plugins: [],
};

```

我们现在可以使用`font-antonio`、`font-ibm`和`font-cooper`实用程序类名来应用字体。实现这些实用程序的组件将如下所示:

```
const Tailwind = () => {
  return (
    <div>
      <h1 className="font-ibm">With Tailwind CSS</h1>
      <p className="font-antonio">This is a first paragraph</p>
      <p className="font-cooper">This is a second paragraph</p>
    </div>
  );
};

export default Tailwind;

```

## Chrome 浏览器中的一个问题

在单个页面中使用默认的`display: optional`可能会遇到 Chrome 问题。此问题会导致元素在导航到其他页面重新加载后恢复回退字体。请参见下面的演示:

![Browser Opened To Localhost 3000 In Light Mode With Menu Bar And Four Lines Of Text In Varying Fonts. User Shown Navigating To Different Page In Menu Bar, Refreshing Page, Then Returning To Home Page, Where Fonts Have Reverted To Fallback Fonts. User Then Refreshes Home Page To Reapply Proper Web Fonts](img/011ff5a91bc48915fb0ee896f1c6ea73.png)

如上面的 GIF 所示，在手动刷新其他页面后，应用于主页元素的字体恢复为备用字体。我们不得不再次刷新主页以重新应用正确的 web 字体。

解决这个问题的一个方法是在字体实例中使用`display: 'swap'`而不是默认的。然而，正如我们已经知道的，交换字体可能会导致明显的布局变化。

相反，我建议将字体实例移动到`pages/_app.js`文件中，并使用`className`属性、`style`对象或 CSS 变量在任何页面上应用字体。

对于我们的项目，我们将把字体声明从`pages/index.js`文件移动到`pages/_app.js`文件，并为字体系列声明 CSS 变量，如下所示:

```
<style jsx global>{`
  :root {
    /* ... */
    --oswald-font: ${oswald.style.fontFamily};
    --merriweather-font: ${merriweather.style.fontFamily};
    --dancingScript-font: ${dancingScript.style.fontFamily};
  }
`}</style>

```

我们现在可以重构`pages/index.js`文件来使用 CSS 模块，就像这样:

```
import styles from './Home.module.css';

export default function Home() {
  return (
    <>
      <div className={styles.oswald}>
        <h1 className={styles.dancingScript}>Home page</h1>
        <p className={styles.merriweather}>
          This text uses a non variable font
        </p>
        <p>This paragraph uses another font</p>
      </div>
      <p>This text should take global styles</p>
    </>
  );
}

```

然后，使用我们声明的 CSS 变量来访问`Home.module.css`文件中的字体，就像这样:

```
.oswald {
  font-family: var(--oswald-font);
}
.dancingScript {
  font-family: var(--dancingScript-font);
}
.merriweather {
  font-family: var(--merriweather-font);
}

```

如果我们保存了所有文件，这些字体现在应该可以像在其他浏览器中一样在 Chrome 上正常使用了。

## 结论

正如我们在本课中看到的，Next.js v13 中引入的`@next/font`系统通过抽象其复杂性来简化字体优化。我们已经使用这个系统在 Next.js 应用程序中添加了自定义字体和 Google 字体。

[在我们的演示项目](https://next-js-fonts-optimization.vercel.app/)中，我们添加了多种字体来帮助演示在 Next.js 中使用字体的各种方法。然而，在生产站点中，我们应该考虑尽量减少预加载字体的数量。

我希望你喜欢这一课。如果你有问题或贡献，请在评论区分享你的想法。参见 GitHub 上项目的[完整源代码。](https://github.com/Ibaslogic/Next.js-fonts-optimization)

## [LogRocket](https://lp.logrocket.com/blg/nextjs-signup) :全面了解生产 Next.js 应用

调试下一个应用程序可能会很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪状态、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/nextjs-signup)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png)](https://lp.logrocket.com/blg/nextjs-signup)[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/nextjs-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你的 Next.js 应用上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用程序的性能，报告客户端 CPU 负载、客户端内存使用等指标。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

让您调试 Next.js 应用的方式现代化— [开始免费监控](https://lp.logrocket.com/blg/nextjs-signup)。