# 两种只加载你需要的 CSS 的方法

> 原文：<https://blog.logrocket.com/two-ways-load-only-css-you-need/>

CSS 代表级联样式表，是一种用于在网站上呈现内容的样式语言。

可以通过各种方式将样式表包含在网页中；你可以嵌入一个链接到外部 CSS 文件的`<link>`标签，你可以内嵌或者在`<head>`区域的`<style>`块中嵌入样式，或者你可以使用一个现代的 CSS 工具，比如 PostCSS，来使用一些额外的便利特性。

然而，在某些情况下，只有当指定的条件是`true`时，您才可能想要使用特定的样式表。例如，您可能希望在移动设备上查看站点时应用移动友好的样式表，而在桌面屏幕上查看站点时应用桌面版本。

在本教程中，我们将介绍两种有效的方法，使用媒体查询和 PostCSS，有选择地加载和应用样式表，并回顾每个过程的一些注意事项:

媒体查询是一种 CSS 技术，用于使网站在任何屏幕上都好看。它使用`@media`规则来包含一个 CSS 属性块，该属性块只有在条件为`true`时才能应用。

例如，使用 CSS 中的`@media`规则，您可以编写一个媒体查询，在宽于 500 像素的屏幕和低于 500 像素的屏幕上以不同的方式显示文本:

```
/* text is blue on screens smaller than 500px */
@media (max-width: 500px) {
    p {
       color: blue;
    }
}

/* text is red on screens larger than 500px */
@media (min-width: 500px) {
    p {
       color: red;
    }
}

```

媒体查询通常编写在样式表中，但是您也可以直接在 HTML 标记中编写媒体查询。

HTML 中的`<link>`标签建立了 HTML 文档和外部资源(如外部样式表)之间的链接。

尽管这个元素可以在一个文档中出现多次，但它只出现在`<head>`部分。`<link>`是一个空标签，因此它可以保存多个属性，这些属性向浏览器描述链接项如何与包含的文档相关联。

在处理复杂的网站时，您可以创建不同的 CSS 文件，所有文件都适合不同的屏幕大小或方向。然后，使用`<link>`标签上的`media`属性，您可以设置媒体查询来加载相应的样式表。

例如，考虑下面的 HTML:

```
<!DOCTYPE html>
<html lang="en">
<head>
  <title>Document</title>
  <link rel="stylesheet" media='screen and (max-width: 699px)' href="phone.css"/>
  <link rel="stylesheet" media='screen and (min-width: 700px)' href="tablet.css"/>
</head>
<body>
  <p>Text color: Blue in phones, red in tablets, green in even smaller screens</p>
</body>
</html>

```

下面是`phone.css`的风格:

```
p {
  color: blue;
}

```

下面是`tablet.css`的风格:

```
p {
  color: red;
}

```

在宽度小于 700px 的视口中，样式表`phone.css`将应用于文档，使文本变成蓝色。当屏幕宽度超过 699px 时，`tablet.css`将应用于文档，文本将变为红色。

在组织样式表时，这种方法为您提供了更多的灵活性。因此，您可以将所有样式和媒体查询分解到不同的文件中，并使用媒体查询直接从 HTML 加载它们，而不是将它们放在一个 CSS 文件中。

对于 HTML 媒体查询，无论是否满足媒体查询，两个 CSS 文件都会被下载。浏览器必须下载全部内容，以便在方向改变时重新评估。但是，浏览器解析不需要的 CSS 会延迟。

但是，使用 CSS 媒体查询，所有的样式块都被完全解析和处理，不管查询是否被满足。

当通过 HTML `<link>`加载 CSS 文件时，样式表可能包含自己的媒体查询。

为了演示这一点，我们将在`phone.css`中嵌套另一个媒体查询:

```
p {
  color: blue;
}

/* nested media query */
@media (max-width: 200px) {
    p {
       color: green;
    }
}

```

这里，我们说当屏幕宽度小于 200 像素时，文本应该变成绿色。当我们的浏览器调整到 200 像素或更小的宽度时，文本变成绿色。

但是，将屏幕加宽到高于 200px 的宽度，文本会变回蓝色。这表明浏览器也将处理任何嵌套的媒体查询。

## postscript 导入

[PostCSS 提供了一个广泛的插件生态系统](https://blog.logrocket.com/getting-started-with-postcss-in-2019-484262a4d725/)来帮助 CSS 作者改善他们的 CSS 写作体验。这包括一个插件，可以将不同 CSS 文件中的样式表合并成一个 CSS 文件。

在 web 项目中设置 PostCSS 的一种方法是使用 PostCSS CLI，这就是我们在这里要使用的。但是，请注意，运行 PostCSS 需要 Node 和 npm，所以请确保首先安装它们。

首先，让我们创建一个空的`postcss-demo`文件夹，然后通过从命令行运行`npm init -y`将`cd`放入该文件夹并初始化一个新的节点项目。这将在我们的`postcss-demo`文件夹中生成一个带有默认值的 package.json 文件。

现在，要一次性安装 PostCSS、PostCSS CLI 和 PostCSS imports 插件并将其保存为依赖项，请使用以下命令:

```
npm i -D postcss postcss-cli postcss-import

```

PostCSS 使用 PostCSS 导入插件将不同来源的不同样式表捆绑到一个 CSS 文件中。

例如，在`postcss-demo/src`文件夹中，我们可以决定根据样式表的作用将它们分组到不同的文件夹中。

重置文档的样式表放入基本文件夹，设计通用 UI 组件的样式表(例如，`button.css`用于按钮，`card.css`用于卡片，`form.css`用于表单)放入组件文件夹，依此类推。

基本上，你可以随意组织你的样式表。

然后，通过一些配置设置和 CLI 命令，我们可以将所有需要的 CSS 文件放入一个单独的 CSS 文件中进行生产。

作为参考，下面是我们项目此时的结构:

```
postcss-demo
├── src
│   ├── base
│         ├── base.css
│         ├── reset.css
│   ├── components
│         ├── button.css
│         ├── card.css
│         ├── form.css
│   ├── utilities
│         ├── container.css
│         ├── margin.css
│         ├── padding.css
│   └── style.css
├── node_modules
├── package.json
└── package-lock.json

```

现在我们已经安装了所有的依赖项，我们必须设置一个 PostCSS 配置文件。
为此，直接在`postcss-demo`(根文件夹)中创建一个`postcss.config.js`文件。

在这个文件中，我们将注册插件，这样我们就可以使用它了:

```
module.exports = {
    plugins: [
        require('postcss-import'),
    ]
}

```

接下来，在`postcss-demo/src/style.css`文件中，我们将使用`@import`指令指示 PostCSS 导入什么样的样式表:

```
@import 'base/base.css';
@import 'base/reset.css';

@import 'components/button.css';
@import 'components/card.css';
@import 'components/form.css';

@import 'utilities/container.css';
@import 'utilities/padding.css';
@import 'utilities/margin.css';

```

这里，我们指定了我们想要导入的所有样式表的相对路径。

指令和路径都准备好了，让我们将这些不同的样式表导入到`styles.css`中。为此，打开`package.json`文件并在`scripts`下包含以下脚本:

```
{
  //
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "postcss:watch": "postcss src/style.css --dir public"
  },
  //
}

```

这里，我们指定当脚本`postcss:watch`运行时做什么。除了运行`postcss`，我们告诉它进入我们的`src`文件夹，找到`styles.css`文件，然后我们指定将合并的样式表输出到哪个目录。

最后，运行以下脚本:

```
npm run postcss:watch

```

成功执行后，该脚本会生成一个包含文件`style.css`的公共文件夹。这个文件包含了之前导入到`src/style.css`中的所有样式表。

因此，任何导入到`src/styles.css`的样式表都会输出到`public/style.css`。

## 结论

在本教程中，我们详细讨论了通过媒体查询和 Post CSS 导入将 CSS 加载到网页中的两种方法。

媒体查询可以在 CSS 和 HTML 中编写。在 HTML 内部，`<link>`标签必须提供一个包含媒体查询和外部 CSS 文件 URL 链接的`media`属性，当查询条件为`true`时适用。

然而，PostCSS 使用一个特殊的导入插件将不同的 CSS 文件捆绑成一个单独的 CSS 文件。

如果你有任何关于这个话题的问题，请在评论中告诉我。

祝你一周愉快！

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。