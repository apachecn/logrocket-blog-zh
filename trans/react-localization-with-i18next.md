# 使用 i18next - LogRocket 博客对本地化做出反应

> 原文：<https://blog.logrocket.com/react-localization-with-i18next/>

对于希望通过提高转化率来提高用户满意度和实现业务目标的开发人员来说，构建满足本地市场需求的应用程序至关重要。

对于 React 应用程序，有许多国际化选项可以针对这类受众。在本指南中，我们将回顾最流行的解决方案之一:i18next。

由于本地化过程(也称为 l10n ),该框架无缝地使应用适应特定的本地市场和文化。

本地化不仅仅是将单词从一种语言翻译成另一种语言。除了翻译，它还帮助我们考虑文化差异，如货币、单位位置、数字和日期格式、多元化，甚至当地的外观。

这是本地化应用程序的外观:

![Final Localized Application Using i18next](img/6e64cdc0c3168638900375822abe5bc1.png)

在本教程中，我们一起构建这个应用程序。您可以在这里与最终项目[互动。](https://react-i18next-project.herokuapp.com/)

## 为什么要使用 i18next 框架？

i18next 框架非常灵活，因为它支持插件，允许我们添加一些我们必须自己构建的特性。

我们还可以选择将翻译分成不同的文件，并在需要时加载它们。这意味着我们不需要在加载页面之前加载所有的翻译文件，从而减少缓慢的加载时间。

## 先决条件

*   对 React 的基本理解
*   安装在计算机上的 Node.js

## 下载启动项目

使用这个简单的 [React 项目与](https://github.com/Ibaslogic/react_i18next_starter)一起工作，让我们在我们的计算机终端中运行以下命令:

```
git clone https://github.com/Ibaslogic/react_i18next_starter

```

完成后，用代码编辑器打开`project`文件夹。在`project`目录下，运行`npm install`生成一个`node_modules`文件夹。

然后，通过运行`npm start`启动开发服务器，在 [http://localhost:3000/](http://localhost:3000/) 等待查看应用程序在浏览器中的加载情况。

![Reacti18next Home Screen Rendered In English](img/0dbfb0b0ee520a4a9ab109a2e36d7038.png)

`project`结构应该是这样的:

```
project_folder
    ├── node_modules
    ├── public
    ├── src
    │    ├── components
    │    │      ├── App.js
    │    │      ├── Content.js
    │    │      ├── Footer.js
    │    │      └── Header.js
    │    ├── index.css
    │    └── index.js
    ├── .gitignore
    ├── package-lock.json
    ├── package.json
    ├── README.md
    └── yarn.lock

```

目前，我们的重点是`src`文件夹。在那里，我们拥有所有包含我们可以本地化的内容的 React 组件。

## 安装`i18next`和`react-i18next`包

要使用 i18next 本地化 React 应用程序，我们必须将`i18next`和`react-i18next`包添加到我们的项目中。

`i18next`提供所有的翻译功能，而`react-i18next`增加了一些额外的 React 功能，如挂钩、hoc、渲染道具等。

通过运行以下命令来安装它们:

```
npm install react-i18next i18next

```

## 设置配置文件

在`src`文件夹中创建一个名为`i18n.js`的文件，并添加以下翻译配置:

```
import i18n from "i18next";
import { initReactI18next } from "react-i18next";

i18n
  .use(initReactI18next)
  .init({
    resources: {
      en: {
        translation: {
          //English translations here
        },
      },
      ja: {
        translation: {
          //Japanese translations here
        },
      },
    },
    lng: "en",
    fallbackLng: "en",
  });

export default i18n;

```

通过从`i18next`内核导入`i18n`实例，我们可以通过模块提供的`initReactI18next`对象将`react-i18next`模块绑定到它。这确保了 i18next 框架将`i18n`实例传递给`react-i18next`模块。

调用`use()`函数加载并绑定任何插件到`i18n`实例。

然后，我们通过调用定义基本配置选项的`init()`函数和[来初始化我们的配置。](https://www.i18next.com/overview/configuration-options)

我们还添加了`resources`、`lng`和`fallbackLng`对象属性。在`resources`中，我们为英语和日语两种语言提供了`translation`对象。

`en`和`ja`分别是代表英语和日语的 ISO 标准代码。在此查看所有 [ISO 标准](http://www.gnu.org/savannah-checkouts/gnu/gettext/manual/gettext.html#Language-Codes) [语言缩写](http://www.gnu.org/savannah-checkouts/gnu/gettext/manual/gettext.html#Language-Codes)。

接下来，在`src/index.js`中导入配置文件，就像这样:

```
import "./i18n";

```

别忘了保存文件！

## 翻译一条简单的短信

首先，让我们翻译来自我们的起始项目的一个简单的标题消息，“欢迎来到 react-i18 下一个教程。”

更新`src/i18n.js`文件以包含英语和日语翻译:

```
// ...
resources: {
  en: {
    translation: {
      //English translations here
      "welcome text": "Welcome to the react-i18next tutorial",
    },
  },
  ja: {
    translation: {
      //Japanese translations here
      "welcome text": "react-i18nextチュートリアルへようこそ",
    },
  },
},
// ...

```

保存文件。

正如在`translation`对象中所看到的，我们用 JSON 格式定义了我们的消息，在支持的地区中给它一个惟一的键。

在这个项目中，我们使用 [Google Translate](https://translate.google.com/) 翻译成目标语言。

### 通过`t()`访问翻译后的信息

`i18next`通过`i18n`实例提供了一个名为`t()`的翻译函数。它接受一个查找翻译对象的键，并返回与当前语言的键匹配的字符串。

根据 React 组件的类型，我们可以用不同的方式访问`t()`,比如使用:

在本指南中，我们将使用`useTranslation`挂钩。

### 使用`useTranslation`挂钩

打开`components/Content.js`文件，导入`useTranslation`钩子，像这样:

```
import { useTranslation } from 'react-i18next';

```

然后，在`return`语句的上方添加这一行:

```
const { t, i18n } = useTranslation();

```

从钩子中，我们可以访问`t`函数和`i18n`实例。我们只需要`t`功能来翻译我们的内容。在本指南的后面，我们将使用`i18n`实例来更改应用程序语言。目前，我们可以移除`i18n`。

接下来，用包含翻译键的`t`函数替换`h1`文本，这样我们就有了以下内容:

```
import { useTranslation } from "react-i18next";

const Content = () => {
  const { t } = useTranslation();

  return (
    <div className="container hero">
      <h1>{t("welcome text")}</h1>
      {/* ... */}
    </div>
  );
};

export default Content;

```

保存文件并重新加载前端。

什么都没变。但是，让我们暂时将`src/i18n.js`文件中的`lng`属性改为日语(`ja`)并保存该文件:

```
i18n
  .use(initReactI18next)
  .init({
    resources: {
      // ...
    },
    lng: "ja",
    fallbackLng: "en",
  });

```

重新加载页面后，我们将看到呈现的日文文本:

![Rendered App Text In Japanese With Page Reload](img/8816bb0aaebfd14ecb5a334f7fdba85b.png)

## 延迟从服务器加载翻译文件

目前，我们已经将所有的翻译加载到配置文件中。这并不理想，尤其是当我们在一个更大的项目中添加更多的语言和内容时。

通过将翻译从代码中分离出来并在需要时加载，提供了一种更好的优化代码的方法。为此，我们将安装一个插件。

### 安装`i18next-http-backend`

`i18next-http-backend`包从后端服务器加载翻译文件:

```
npm install i18next-http-backend

```

接下来，更新`i18n.js`配置文件以包含插件:

```
import i18n from "i18next";
import { initReactI18next } from "react-i18next";
import Backend from "i18next-http-backend";

i18n
  .use(Backend)
  .use(initReactI18next)
  .init({
    ...
  });

export default i18n;

```

默认情况下，这个插件希望从`public/locales/{lng}/translation.json`目录加载我们的翻译文件，其中`lng`是语言代码。

虽然我们也可以将目录从配置文件的[更改为自定义目录，但我们将坚持使用默认目录。](https://github.com/i18next/i18next-http-backend)

让我们为我们支持的每种语言创建`translation.json`文件。在这种情况下，我们将支持英语、阿拉伯语、法语和日语。

文件结构应该如下所示:

```
public
  ├── locales
  │     ├── ar
  │     │   └── translation.json 
  │     ├── en
  │     │   └── translation.json
  │     ├── fr
  │     │   └── translation.json
  │     ├── ja
  │     │   └── translation.json

```

在`en/translation.json`文件中，添加以下内容:

```
{
  "welcome text": "Welcome to the react-i18next tutorial"
}

```

在`ar/translation.json`文件中，添加以下内容:

```
{
  "welcome text": "مرحبًا بك في البرنامج التعليمي react-i18next"
}

```

对于其他支持的语言环境，请使用谷歌翻译。稍后，我们将更新我们的翻译文件，以适应其他文本。

记得保存所有文件。

我们现在可以删除`src/i18n.js`文件中的`resources`属性，因为我们现在可以从后端加载翻译。

配置文件现在看起来像这样:

```
// ...
i18n
  .use(Backend)
  .use(initReactI18next)
  .init({
    lng: "en",
    fallbackLng: "en",
  });
// ...

```

如果我们保存文件并重新加载前端，我们会遇到应用程序中断。i18next 框架希望我们在等待从后端加载翻译文本的同时处理应用程序的状态。

为此，我们将把顶层组件包装在 React 的`Suspense`组件中。这允许我们在等待阶段显示一些后备信息，例如装载指示器。

打开`src/index.js`文件并使用`Suspense`组件，如下所示:

```
ReactDOM.render(
  <React.StrictMode>
    <Suspense fallback={<div>Loading...</div>}>
      <App />
    </Suspense>
  </React.StrictMode>,
  document.getElementById("root")
);

```

接下来，在文件顶部导入`Suspense`组件:

```
import React, { Suspense } from "react";

```

属性接受一个文本字符串或者任何我们想要呈现的 React 元素。保存文件，应用程序应该工作。

如果我们将配置文件中的`lng`属性更改为`ar`或任何支持的语言，我们将在前端看到翻译。

![Change lng Property To Update Translation In The Frontend](img/115fffe9d7ccf22b07466919a61f1be0.png)

既然我们已经介绍了基础知识，让我们快速翻译一下其他页面内容。

## 插值、格式化和多元化

到目前为止，我们已经介绍了如何翻译一个简单的文本字符串。如前所述，本地化不仅仅是语言之间的翻译。必须考虑文化差异。

接下来，我们将讨论如何格式化和翻译文本丰富的消息。如果我们看一下我们的应用程序，我们会看到我们有数字、日期和货币位置要针对不同的地区进行格式化。

我们也有一个随着用户点击按钮而增加的数字计数，并且必须格式化文本以考虑复数形式。

### 插入文字

当文本包含必须格式化的数字或日期时，我们使用插值。这是允许在我们的翻译中插入值的功能之一。

使用前端文本“用所选语言格式化 3，000”，我们将在文本中插入“3000”。

在`en/translation.json`文件中，将其添加到 JSON:

```
{
  ...
  "formatNumber": "Formatting {{num}} in the selected language"
}

```

注意我们是如何用占位符替换数字的。

在阿拉伯文文件中，添加以下内容:

```
{
  ...
  "formatNumber": "تنسيق {{num}} في اللغة المختارة"
}

```

确保为其他支持的语言环境添加翻译并保存所有文件。

接下来，我们将使用翻译功能`t()`，根据当前语言翻译和格式化我们的消息。

在`components/Content.js`中，找到这个元素:

```
<p>Formatting 3,000 in the selected language</p>

```

替换为以下内容:

```
<p>{t("formatNumber", { num: 3000 })}</p>

```

这里，我们将要插值的数字作为`t()`函数的第二个参数进行传递。object 属性中使用的`num`必须匹配翻译文件中的占位符——在我们的例子中是`{{num}}`。

保存文件并测试工作。

如果我们将`i18n.js`文件中的`lng`属性改为`ar`，我们将得到:

![Interpolate The Number Translation To Update For The Arabic Format](img/f550a2a9af3e3fefdd8e6f99c7115be9.png)

接下来，我们必须根据当前语言格式化插入的数字。

### 格式化

默认情况下，i18next 不支持格式化数字或日期。但是，它确实允许我们使用 moment.js、Luxon 和 date-fns 等[库进行格式化，或者通过 Intl API 进行格式化。](https://blog.logrocket.com/javascript-date-libraries/)

在这个项目中，我们将[使用`Intl` API](https://blog.logrocket.com/react-intl-internationalize-your-react-apps/#Intl-API) 为我们格式化；很简单，和 i18next 配合很好。这个 Intl API 通过它的`Intl`对象为我们提供了所需的构造函数。

对于我们格式化，我们只需要`Intl.NumberFormat()`和`Intl.DateTimeFormat()`构造函数。

为了格式化我们之前插入的数字，我们必须在配置中添加另一个名为`interpolation`的转换选项，并定义一个处理格式化的函数。

现在的配置如下所示:

```
i18n
  .use(Backend)
  .use(initReactI18next)
  .init({
    lng: "ar",
    fallbackLng: "en",
    interpolation: {
      format: (value, format, lng) => {
        if (format === "number") {
          return new Intl.NumberFormat(lng).format(value);
        }
      },
    },
  });

```

重点是`format`功能。在那里，我们定义了一个条件来检查我们需要格式化的值是否是一个数字。然后，我们必须使用 Intl API 返回当前语言的格式化值。

为了确保条件返回 true，我们必须在翻译中更新占位符`{{ }}`，以包含`number`。

对于英语，更新`en`如下:

```
"formatNumber": "Formatting {{num, number}} in the selected language"

```

对于阿拉伯语，更新`ar`如下:

```
"formatNumber": "تنسيق {{num, number}} في اللغة المختارة"

```

更新其他支持的语言环境的翻译文件，并保存它们。

现在，应用程序看起来应该是这样的:

![Updated Arabic Number Format From Interpolated Number](img/374b876a9777ab0929d9212174bef52e.png)

使用同样的方法，让我们翻译`components/Content.js`文件中的其他文本，并格式化日期和货币位置。

更新`en/translation.json`以包括以下内容:

```
{
  ...
  "formatCurrency": "Displaying {{price, currency}} in the selected locale",
  "formatDate": "Today's date: {{today, date}}"
}

```

在`ar/translation.json`中，添加:

```
{
  ...
  "formatCurrency": "عرض {{price, currency}} دولارًا في اللغة المحددة",
  "formatDate": "تاريخ اليوم: {{today, date}}"
}

```

同样，为其他受支持的语言环境添加翻译。

之后，更新`components/Content.js`文件以访问翻译:

```
const Content = () => {
  // ...

  return (
    <div className="container hero">
      <h1>{t("welcome text")}</h1>
      <p>{t("formatCurrency", { price: 69.99 })}</p>
      <p>{t("formatNumber", { num: 3000 })}</p>
      <p>{t("formatDate", { today: new Date() })}</p>
    </div>
  );
};

```

最后，在`i18n.js`文件中，更新`format`函数:

```
format: (value, format, lng) => {
  // ...
  if (format === "date") {
    return new Intl.DateTimeFormat(lng).format(value);
  }
  if (format === "currency") {
    return new Intl.NumberFormat(lng, {
      style: "currency",
      currency: "USD",
    }).format(value);
  }
},

```

在这个文件中，我们使用了`Intl.DateTimeFormat()`构造函数来格式化日期。注意我们是如何为`Intl.NumberFormat()`提供额外的选项来格式化货币的。在这里找到[货币代码列表](https://www.currency-iso.org/dam/downloads/lists/list_one.xml)。

保存所有文件并重新加载前端。

默认情况下，i18next 对值进行转义，以减少跨站点脚本(XSS)攻击，如下图所示。

![i18next Escapes Values To Reduce Cross-Site Scripting ](img/e308f183597c16875475e17bbea486b7.png)

但是，React 应用是受 XSS 保护的。因此，让我们通过在`interpolation`中添加`escapeValue`并分配一个`false`值来防止 i18next 对值进行转义。

```
interpolation: {
  format: (value, format, lng) => {
    // ...
  },
  escapeValue: false // react already safes from xss
},

```

保存并重新加载前端，它应该工作。

### 多元化

目前在我们的应用程序中，点击前端计数按钮不会改变计数语句。例如，当计数为单数时，语句应该为“您点击了 1 次”

因此，让我们开始将翻译添加到各种翻译文件中。更新`en/translation.json`文件以包括以下内容:

```
{
  ...
  "clickCount": "You've clicked {{count}} time",
  "clickCount_plural": "You've clicked {{count}} times"
}

```

这里，我们用占位符替换消息中的动态计数。这个占位符变量必须称为`count`。我们也提供我们翻译的复数形式。

为了告诉 i18next 是呈现单数还是复数消息，我们必须将不带`_plural`的`clickCount`键和动态计数值一起传递给呈现中的`t`函数。

如果计数为`1`，则呈现单数消息，否则呈现复数形式。

让我们更新其他地区的翻译文件。对于`ja/translation.json`文件，添加以下内容:

```
{
  ...
  "clickCount": "{{count}}回クリックしました"
}

```

请注意，我们没有添加复数等价物。这是因为日语(`ja`)只有一个复数形式。参见每种语言的[复数形式。](http://docs.translatehouse.org/projects/localization-guide/en/latest/l10n/pluralforms.html?id=l10n/pluralforms)

和英语一样，法语也有两种复数形式。然而，有些单词的单复数形式相同。

例如，如果我们将消息翻译成法语，我们将在`fr/translation.json`文件中得到以下内容:

```
{
  ...
  "clickCount": "Vous avez cliqué {{count}} fois",
  "clickCount_plural": "Vous avez cliqué {{count}} fois"
}

```

因为两者的形式相同，所以我们可以忽略第二个，这样:

```
{
  ...
  "clickCount": "Vous avez cliqué {{count}} fois",
}

```

对于阿拉伯语，除了单数形式之外，还有五种复数形式，所以我们必须定义每一种形式，就像这样:

```
clickCount_0; //c = 0 i.e zero
clickCount_1; //c = 1 i.e singular
clickCount_2; //c =2 i.e two
clickCount_3; //3 <= c <=10 i.e few
clickCount_4; //11 <= c <=99 i.e many
clickCount_5; //>= 100 i.e other

```

我们必须维护相同的键名，在我们的例子中，维护的是`clickCount`，就像我们在其他翻译文件中使用的一样。

i18next 根据计数自动选择合适的表单。当计数为`0`、`1`或`2`时，i18next 渲染分配给它们各自键`clickCount_0`、`clickCount_1`和`clickCount_2`的翻译。

但是，当计数在`3`和`10`之间时，i18next 渲染分配给`clickCount_3`的翻译，`11`到`99`渲染分配给`clickCount_4`的翻译，在`100`或以上的计数渲染分配给`clickCount_5`的翻译。

如果我们将字符串“你点击了{{n}}次”翻译成阿拉伯语，其中“n”是一个从 0 到无穷大的数字，我们可以使用 Google Translate 得到以下结果:

```
{
  ...
  "clickCount_0": "لقد نقرت {{count}} مرة",
  "clickCount_1": "لقد نقرت مرة واحدة",
  "clickCount_2": "لقد نقرت مرتين",
  "clickCount_3": "لقد نقرت {{count}} مرات",
  "clickCount_4": "لقد نقرت {{count}} مرة",
  "clickCount_5": "لقد نقرت {{count}} مرة"
}

```

现在，保存文件。

打开`components/Footer.js`文件访问`t`功能。首先，导入翻译挂钩，就像这样:

```
import { useTranslation } from "react-i18next";

```

在`return`语句上方，从钩子中访问`t`函数:

```
const { t } = useTranslation();

```

现在，我们可以用它代替文本来翻译它；因此，找到以下元素:

```
<p>You've clicked {count} times</p>

```

然后，用这个替换它:

```
<p>{t("clickCount", { count: count })}</p>

```

保存文件，重新加载前端，并测试项目。

## `Trans`组件

要翻译用`strong`、`i`或`br`元素格式化的字符串，或者要包含一个链接，我们必须使用 i18next 中的`Trans`组件。

如果我们打开`components/Footer.js`文件，我们可以翻译并格式化以下元素:

```
<p>
  Let's <strong> click the button </strong> below:
</p>

```

这里的逻辑很简单；我们将把`p`元素文本分解成节点，并给它们分配一个索引号，如下所示:

```
  Let's --> index 0
  <strong> click the button </strong> --> index 1
  below: --> index 2

```

接下来，在`Footer.js`文件中导入`Trans`组件来换行:

```
// ...

import { useTranslation, Trans } from "react-i18next";

const Footer = () => {
  // ...

  return (
    <div className="container mt">
      {/* Footer content here */}
      <p>
        <Trans i18nKey="clickButtonText">
          Let's <strong> click the button </strong> below:
        </Trans>
      </p>
      {/* ... */}
    </div>
  );
};

// ...

```

注意，我们已经将`i18nKey`道具添加到了`Trans`组件中。我们将使用它的值作为翻译文件中的键。

我们现在可以使用这个索引号作为占位符来引用翻译文件中的内部元素。

在`en/translation.json`文件中，我们有以下内容:

```
{
  ...
  "clickButtonText": "Let's <1>click the button</1> below:"
}

```

钥匙来自`Trans` `i18nKey`道具。

在`ar/translation.json`文件中，我们有以下内容:

```
{
  ...
  "clickButtonText": "دعنا <1> انقر فوق الزر </ 1> أدناه: "
}

```

更新其他支持的翻译文件，保存它们，然后重新加载前端并查看更改。

### Translate button text

为了翻译按钮文本，让我们用`t`函数替换`component/Footer.js`文件中的文本:

```
<button onClick={onChange}>{t("click")}</button>

```

然后，更新翻译文件以包含键值对。

在`en/translation.json`文件中，添加:

```
"click": "Click here"

```

在`ar/translation.json`文件中，添加:

```
"click": "انقر هنا"

```

使用谷歌翻译更新其他翻译文件。

### 翻译菜单项

要翻译菜单项，首先将其添加到`en/translation.json`文件中:

```
"menu" : {
  "aboutProject" : "About the project",
  "contactUs": "Contact us" 
}

```

然后，将其添加到`ar/translation.json`文件:

```
"menu" : {
  "aboutProject" : "حول المشروع",
  "contactUs": "اتصل بنا" 
}

```

同样，使用谷歌翻译更新其他翻译文件并保存它们。

接下来，打开`components/Header.js`文件并导入顶部的`useTranslation`:

```
import { useTranslation } from "react-i18next";

```

然后，使用`t`功能翻译菜单标题:

```
import { useTranslation } from "react-i18next";

const Header = () => {
  const { t } = useTranslation();
  const menu = [
    {
      title: t("menu.aboutProject"),
      // ...
    },
    {
      title: t("menu.contactUs"),
      // ...
    },
  ];

  return (
    // ...
  );
};

// ...

```

如果我们保存文件并重新加载前端，我们将看到菜单项和所有页面内容被正确翻译。

![Translate Menu Items Into Arabic In Frontend](img/abd9086a31aa35919c8360233a093679.png)

## 检测用户的语言

我们必须为用户提供一个在前端切换语言的选项，同时将选定的语言环境保存在浏览器存储中。这使得内容能够在后续访问中以首选语言显示。

i18next 为此提供了一个插件，所以让我们使用以下命令来安装它:

```
npm install i18next-browser-languagedetector

```

安装后，将插件导入并添加到配置文件中:

```
// ...
import LanguageDetector from "i18next-browser-languagedetector";

i18n
  // ...
  .use(LanguageDetector)
  .init({
    fallbackLng: "en",
    detection: {
      order: ["path", "localStorage", "htmlTag", "cookie"],
      caches: ["localStorage", "cookie"], // cache user language on
    },
    interpolation: {
      // ...
    },
  });

export default i18n;

```

在设置中，我们移除了`lng`属性，因为应用程序现在依赖插件来检测当前语言。在`detection`属性中，我们已经指定了检测用户语言的顺序。

订单中的第一项具有最高优先级，并按该顺序递减。这意味着插件首先检查`path`，然后是`localStorage`，依此类推。

现在，我们可以从 URL 路径 [http://localhost:3000/ar](http://localhost:3000/ar) 访问区域设置内容。

我们必须将`path`指定为数组中的第一项，以通过路径访问区域设置内容。但是，请注意，仍然会从其他指定的存储中检测到用户的区域设置。

保存文件并重新加载前端；通过将一个区域设置传递给 URL 路径[http://localhost:3000/](http://localhost:3000/ar)ar 来测试项目。

## 创建语言切换器

要创建语言切换器，首先打开`components/Header.js`文件并在`return`语句上方添加支持的语言:

```
// Languages
const languages = [
  { name: "English", code: "en" },
  { name: "日本語", code: "ja" },
  { name: "Français", code: "fr" },
  { name: "العربية", code: "ar" },
];

```

接下来，在 JSX 中找到这个元素:

```
<div className="switcher">
  {/* Language switch dropdown here */}
</div>

```

更新它，这样我们就有了以下内容:

```
<div className="switcher">
  {/* Language switch dropdown here */}
  <span>Languages</span>{" "}
  <select>
    {languages.map(({ name, code }) => (
      <option key={code} value={code}>
        {name}
      </option>
    ))}
  </select>
</div>

```

保存文件。代码应该是不言自明的，因为它是[基本反应](https://ibaslogic.com/react-tutorial-for-beginners/)。

切换器现在应该显示在前端，虽然它还没有工作。我们需要让下拉元素成为一个[受控组件](https://blog.logrocket.com/forms-in-react-in-2020/)。

我们需要做的就是将一个`value`道具和`onChange`传递给`select`元素:

```
<select onChange="" value="">
  {/* ... */}
</select>

```

当`onChange`触发一个更新语言环境的函数时，`value`属性获取当前的语言环境。

我们可以通过安装一个名为`js-cookie`的包从存储器中获取当前的区域设置值。这允许我们从 cookie 中获取区域设置字符串:

```
npm install js-cookie

```

接下来，在`Header.js`文件的顶部导入`Cookies`对象:

```
import Cookies from "js-cookie";

```

然后，在`return`语句上方添加以下代码:

```
const currentLocale = Cookies.get("i18next") || "en";

```

这个包使用`get()`方法从存储器中读取一个 cookie，并接受 cookie 的名称。

对于 i18next 框架，cookie 名称是`i18next`。我们可以在浏览器 devtools 的存储 cookies 中找到这个名字。

接下来，定义一个状态，该状态接受当前的区域设置并将其分配给 dropdown 元素的`value`属性。

仍然在`Header.js`文件中，导入文件顶部的`useState`:

```
import { useState } from "react";

```

然后，定义一个`language`变量，并通过`useState`钩子传入当前区域设置。

在`return`语句上方添加以下内容:

```
const [language, setLanguage] = useState(currentLocale);

```

既然`language`已经有了当前的区域设置，我们可以将它传递给`value`属性:

```
<select onChange={handleChangeLocale} value={language}>
  {/* ... */}
</select>

```

注意，我们还添加了`handleChangeLocale`处理程序来触发更新。

让我们快速创建它。在`return`语句上方添加以下代码:

```
const handleChangeLocale = (e) => {
  const lang = e.target.value;
  setLanguage(lang);
};

```

代码根据用户的选择更新语言下拉列表。我们必须更进一步，更新应用程序语言。

如前所述，需要使用`i18n`实例来更改语言。现在，我们可以用它来抓取`changeLanguage()` API 来接受用户选择的语言。

像`t`函数一样，我们也可以从`useTranslation()`钩子访问`i18n`实例。

因此，更新钩子以包含`i18n`:

```
const { t, i18n } = useTranslation();

```

然后，在`handleChangeLocale`处理器中使用它:

```
const handleChangeLocale = (e) => {
  const lang = e.target.value;
  setLanguage(lang);
  i18n.changeLanguage(lang);
};

```

保存并测试应用程序。

## 从右向左呈现阿拉伯语内容

如果我们临时打开浏览器 devtools 并向`body`元素添加一个`dir="rtl"`，我们将看到内容从右向左显示。在我们的应用程序中，我们需要定位`body`元素，并在页面加载时添加`dir`属性。

更新`languages`数组中的阿拉伯语，将它的方向包含在`components/Header.js`文件中:

```
const languages = [
  // ...
  { name: "العربية", code: "ar", dir: "rtl" },
];

```

之后，在`return`语句上方添加以下代码:

```
const currentLangObj = languages.find((lang) => lang.code === currentLocale);

useEffect(() => {
  document.body.dir = currentLangObj.dir || 'ltr'
}, [currentLangObj])

```

[导入文件顶部的`useEffect`
勾](https://blog.logrocket.com/lifecycle-methods-with-the-useeffect-hook/):

```
import { useState, useEffect } from "react";

```

`find()`方法允许我们获取所选地区的语言对象。在这个对象中，我们可以访问阿拉伯语方向，当页面加载时，我们将它分配给`body.dir`属性。

## 翻译应用程序标题

在这里，我们将为我们的应用标题和下拉列表旁边的**语言**文本添加翻译。

在`en/translation.json`中，添加:

```
{
  ...
  "app_title": "React-i18next tutorial",
  "languages": "Languages"  
}

```

为其他支持的语言环境添加翻译并保存所有文件。

回到`components/Header.js`文件，更新`useEffect`以包含应用标题的翻译功能:

```
useEffect(() => {
  document.body.dir = currentLangObj.dir || "ltr";
  document.title = t("app_title");
}, [currentLangObj, t]);

```

最后，在 JSX 中找到这个元素:

```
<span>Languages</span>

```

用翻译功能替换它:

```
<span>{t('languages')}</span>

```

保存文件并测试应用程序。一切都应该按预期运行。

恭喜你！我们完了！

## 结论

我很高兴你在这里。关于在 React 应用程序中使用 i18next 框架，您已经学到了很多。我们几乎涵盖了利用 i18next 所需的所有用例。现在，您知道了如何使用它来本地化 React 应用程序。

如果你喜欢这个指南，请在网上分享。如果你有任何问题或贡献，请通过评论区告诉我。

你可以在这个 GitHub 库中看到[完整的 i18next 项目源代码。](https://github.com/Ibaslogic/react_i18next_project)

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)