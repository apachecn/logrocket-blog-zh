# 如何使用 Lingui.js - LogRocket Blog 在 React 中设置国际化

> 原文：<https://blog.logrocket.com/how-to-set-up-internationalization-in-react-using-lingui-js/>

## 概观

国际化(i18n)和本地化在软件开发中至关重要，可以扩展应用程序的范围。有时，甚至需要创建一个内置特定语言的应用程序。例如，大多数政府网站页面被翻译成他们自己国家的当地和国家语言。

大多数网站都依赖于 JavaScript ，难怪会有许多流行的 JavaScript 库来处理应用程序的国际化。一些最著名的图书馆是:

出于本文的目的，我将演示如何使用 Lingui.js。这是一个功能强大的库，不仅处理本地 JavaScript 应用程序的国际化，还处理最著名的 JavaScript 库之一 [React](https://reactjs.org/) 的国际化。Lingui.js 的[轻量级只有 5KB](https://lingui.js.org/index.html) ，对于初学者来说非常容易上手。

## Lingui.js 简介

Lingui.js 是一个相对较新的国际化库，但它提供了一些流行的竞争对手库中没有的功能，如 [react-intl](https://www.npmjs.com/package/react-intl) 和 [i18next](https://www.i18next.com/) 。例如，Lingui.js 打包了可以用来为组件生成消息语法的[宏](https://lingui.js.org/ref/macro.html)。Lingui.js 的一些其他突出特性是:

*   针对性能进行了优化(大小仅为 5KB)
*   易于与 React 和 React 本机应用程序集成
*   它的社区活跃在 GitHub 和 T2 的频谱上
*   Lingui.js 提供了全面的[文档](https://lingui.js.org/)和[分步指南](https://lingui.js.org/tutorials/react.html)以及 [API 参考](https://lingui.js.org/ref/core.html)
*   它使用 ICU MessageFormat，使得 UI 翻译可读
*   提供对富文本的支持，这是许多竞争对手的库中所没有的
*   Lingui.js 有自己的 CLI 来简化翻译用户界面的整个过程

一般来说，Lingui.js 可以集成到任何类型的静态应用程序中。如果你有一个像聊天系统或在线游戏这样的实时应用，那么 Lingui.js 可能是一个很好的选择，因为它被设计成对资源密集型应用是高效的。比如 [Caliopen](https://github.com/CaliOpen/Caliopen) (统一消息平台)就在积极使用 Lingui.js。

在本文中，我们将使用一个示例项目在 React 应用程序中集成 Lingui.js。为了更简单，我们将使用 [create-react-app](https://www.npmjs.com/package/create-react-app) 包。

## 先决条件

要跟随本教程，您必须已经在系统上安装了 [Node.js](https://nodejs.org/en/) 。它的最新版本打包了像 [npm](https://www.npmjs.com/) 和 [npx](https://www.npmjs.com/package/npx) 这样的工具，所以你不必单独安装它们。

## 入门指南

我们的第一步是在本地机器上安装 create-react-app 包的最新版本。为此，请打开系统上所需文件夹内的控制台/终端。之后，执行以下命令:

```
npx create-react-app my-app
```

安装所有的依赖项和设置一个基本的 React 应用程序需要一些时间，所以请耐心等待。安装完成后，运行以下命令:

```
cd my-app
```

现在，您将进入项目文件夹，并准备安装 Lingui.js。

实际上，Lingui.js 的开发人员将整个库分成了不同的模块。目前，我们感兴趣的是 [CLI](https://www.npmjs.com/package/@lingui/cli) 、[宏](https://www.npmjs.com/package/@lingui/macro)和[反应](https://www.npmjs.com/package/@lingui/react)。我们还需要 [Babel 编译器核心](https://www.npmjs.com/package/@babel/core)和 [babel-bridge](https://github.com/babel/babel-bridge) 来支持旧版本的 Babel。

让我们在 React 项目中安装所有这些依赖项:

```
npm i --save-dev @lingui/cli @lingui/macro @babel/core [email protected]
npm i --save @lingui/react
```

## Lingui.js 配置

是时候设置 Lingui.js 了，通知它消息文件的位置、使用的格式和默认的 ui 语言。为此，您必须在项目的根目录下创建一个名为`.linguirc`的文件。该文件的内容应该如下所示:

```
{
    "localeDir": "src/locales/",
    "srcPathDirs": ["src/"],
    "format": "minimal",
    "sourceLocale": "en"
}

```

`src`目录将保存消息的源文件，而 Lingui.js 将从这些文件中提取消息并将它们写入特定于语言的文件夹中。每种语言(例如，英语、乌尔都语、中文等。，)将在`src/locales/`文件夹中有自己的子文件夹。

此外，为消息翻译选择正确的格式也很重要。一些可用的格式包括:

*   `lingui`:每条消息都以预定义的格式返回，比如[这个](https://lingui.js.org/ref/conf.html#lingui)
*   `minimal`:每条消息都以简单的 JSON 格式返回，比如 [this](https://lingui.js.org/ref/conf.html#lingui)
*   `po`:每条消息都以文件的形式返回

Lingui.js 推荐`po`格式。一般来说，这是个人喜好和当前应用程序设置的问题。我们大多数人可能会发现`minimal`(即 JSON)格式比其他格式更容易阅读。因此，在本教程中，我们将使用`minimal`格式。要了解每种格式的更多信息，请参考文档的[这一部分。](https://lingui.js.org/ref/conf.html#std:config-format)

## 更新`package.json`

此时，我们必须在`package.json`中添加一些脚本。

```
{
    "scripts": {
      "add-locale": "lingui add-locale",
      "extract": "lingui extract",
      "compile": "lingui compile"
    }
}

```

使用上面的命令，我们将能够在应用程序中生成新的语言环境，以及编译和提取当前的语言环境。

## 添加用户界面语言

执行`add-locale`脚本将使我们能够为我们的用户界面创建不同的语言:

```
npm run add-locale en zh ur
```

为此，你可以在 IANA 官方网站上查看[支持的语言代码](https://www.iana.org/assignments/language-subtag-registry/language-subtag-registry)。

## 创建网页

CRA 的应用已经有了一个基本的主页。你可以在`/src/App.js`下找到这个文件。我们将像这样修改这个文件:

```
import React, { useState, useEffect } from 'react';
import { Trans } from '@lingui/macro';

import LanguageSelector from './components/LanguageSelector';

function App({ language, onLanguageChange }) {

  return (
    <div className="App">
      <LanguageSelector
        language={language}
        onChangeLangage={onLanguageChange}
      />
      <header className="App-header">
        <h1><Trans>Name of Countries</Trans></h1>
      </header>
      <ul>
        <li><Trans>United States</Trans></li>
        <li><Trans>Finland</Trans></li>
      </ul>
    </div>
  );
}

export default App;

```

你是否注意到我们正在使用一个`LanguageSelector`组件？

这有助于我们动态地改变用户界面的语言。它使用了一个简单的 HTML 选择下拉菜单。这个组件看起来是这样的:

```
import React from 'react';

function LanguageSelector({ language, onChangeLangage }) {

  function handleChange(event) {
    event.preventDefault();
    onChangeLangage(event.target.value);
  }

  return (
    <div className="select">
      <select onChange={handleChange} value={language}>
        <option value="en">English</option>
        <option value="ur">Urdu</option>
        <option value="zh">Chinese</option>
      </select>
    </div>
  );
}

export default LanguageSelector;

```

最后，我们需要更新我们的主`/src/index.js`文件。该文件负责导入所选语言的翻译并呈现整个网页。

```
import React, { useState } from 'react';
import ReactDOM from 'react-dom';
import { I18nProvider } from '@lingui/react';
import App from './App';

async function loadMessages(language) {
  return await import(`@lingui/loader!./locales/${language}/messages.json`);
}

function LocalizedApp() {
  const [catalogs, setCatalogs] = useState({})
  const [language, setLanguage] = useState('en');

  async function handleLanguageChange(language) {
    const newCatalog = await loadMessages(language);

    const newCatalogs = { ...catalogs, [language]: newCatalog };

    setCatalogs(newCatalogs);
    setLanguage(language);
  }

  return (
    <I18nProvider language={language} catalogs={catalogs}>
      <App
        language={language}
        onLanguageChange={handleLanguageChange}
      />
    </I18nProvider>
  );
}

ReactDOM.render(<LocalizedApp />, document.getElementById('root'));

```

## 从源代码中提取消息

现在我们已经在`<Trans> … </Trans>`宏中包装了一些文本，是时候从源代码中提取所有这些消息，然后在每个语言环境中创建键值对。这个键将作为一个 ID，其中的值是它在特定语言中的翻译。

在英语中，键和值对保持不变。但是我们必须改变其他语言的值。默认情况下，它们的值将是空字符串，我们必须手动添加翻译。

执行`npm run extract`命令从源代码中提取消息。

## 用户界面翻译

我们创建了一个网页，显示两个国家的列表:芬兰和美国。现在，我们必须将用户界面翻译成所有支持的语言。

之前，我们在项目中添加了英语、中文和乌尔都语地区。默认语言是英语，Lingui.js 足够智能，可以自动填充英语翻译:

```
{
  "Finland": "Finland",
  "Name of Countries": "Name of Countries",
  "United States": "United States"
}

```

现在，打开`/src/locales/ur/messages.json`，用下面的代码替换它的内容。

```
{
  "Finland": "فن لینڈ",
  "Name of Countries": "ممالک کا نام",
  "United States": "امریکہ"
}

```

类似地，打开`/src/locales/zh/messages.json`，用下面的翻译更新它。

```
{
  "Finland": "芬兰",
  "Name of Countries": "国家名称",
  "United States": "美国"
}

```

此时，我们已经准备好编译这些翻译，以便在我们的应用程序中使用它们。为此，执行编译命令`npm run compile`。

## 运行项目

要运行应用程序，请运行命令`npm start`。

## 结论

我们一起学习了如何轻松地将 Lingui.js 与 React 应用程序集成，并为您的应用程序添加国际化功能。

Lingui.js 最大的好处之一是它可以自动管理所有语言的翻译键值对。这将使用空字符串初始化消息，以防止运行时出错。之后，您只需为不同的键值添加特定的翻译，就可以用一种新的语言查看您的应用程序。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)