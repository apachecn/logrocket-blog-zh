# 动态 HTML 页面的顶级 Express.js 模板引擎

> 原文：<https://blog.logrocket.com/top-express-js-template-engines-for-dynamic-html-pages/>

虽然可以从服务器呈现静态网站，但这种方法有很多限制，包括代码重复和缺乏灵活性——特别是在从数据库读取数据时。幸运的是，Express.js 为我们提供了一种通过模板引擎从服务器端应用程序创建动态 HTML 页面的方法。

模板引擎的工作方式非常简单:您创建一个模板，并使用适当的语法将变量传递给它。然后，在呈现模板的适当路径上，将值赋给模板文件中声明的变量。这些是在模板渲染时实时编译的。

模板引擎的一个重要特性是，它们允许我们创建可重用的组件，称为 partials，可以在其他文件中重用。这有助于防止代码重复，并使更改更容易实现。

现在有很多模板引擎可用，比较流行的包括 Pug (fka Jade)、Handlebars、EJS、Mustache、Swig 等等。本帖将讨论以下 Express 模板引擎:

### 入门指南

通过在终端中键入以下命令，设置新的 npm 项目并安装 Express:

```
npm init
npm i express

```

创建一个`src`文件夹。这是我们为这个项目编写所有代码的地方。在文件夹中，创建一个名为`app.js`的文件和一个名为`views`的文件夹来保存我们将通过 Express 渲染的视图。在`views`文件夹中添加一个`partials`子文件夹来保存片段。您的文件夹结构应该如下所示:

```
├──src
  ├───views
    ├───partials
  app.js

```

将这个快速样板代码添加到`app.js`文件中:

```
const express = require('express');
const path = require('path');
const app = express();
app.get('/', (request, response) => {
  return response.send('OK');
});
app.listen(5000, () => {
  console.log('App is listening on port 5000');
});

```

### 集成 Express 模板引擎

将模板引擎集成到 Express 应用程序中只需要几行代码。在分配快速功能后(创建路线前)，添加以下应用程序设置:

*   `views`，模板文件所在的目录(如`app.set('views', './views')`)。这默认为应用程序根目录中的`views`目录
*   `view engine`，你的模板引擎。例如，使用 Pug 模板引擎:`app.set('view engine', 'pug')`

## 哈巴狗

Pug 有一个非常独特的语法，在 HTML 标签中支持缩进和空格，而不是传统的尖括号。包含页眉和正文的典型页面如下所示:

```
doctype html
html
    head
        meta(name='viewport', content='width=device-width')
        link(rel="stylesheet", href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css")
        title= subject
    body
        div.container.mt-2
            header
                h2 Welcome
                p  Here is the homepage for #{name}
            section
                h2 Here is the body
                p Lorem ipsum dolor sit, amet consectetur adipisicing elit. Totam, repellendus!
            footer
                h2 Here is the footer
                p
                    a(href=link) Unsubscribe

```

从上面的例子中，您可以看到没有开始或结束标记。相反，封闭标签被声明，它的子标签被缩进，就像 Python 中一样。每个标签的内容在标签旁边声明，而属性在括号内声明。等级用`.`表示，`ids`用`#`表示。

变量可以用两种方式定义:

*   **使用等号(`=`)符号**——这通常在要声明的变量是相应标签或属性的唯一内容时使用，如我们的`title`和`a`标签所示
*   **使用`#{variable}`语法**–当变量是标签/属性的唯一内容时，以及当变量是一个较长字符串的一部分时，都可以使用该方法

要在 Express 中呈现上述内容，首先从 npm 安装 Pug 包:

```
npm i pug

```

接下来，将上面的代码复制到`views`文件夹下的`index.pug`文件中，在`app.js`中，将 Pug 注册为首选模板引擎:

```
app.set('view engine', 'pug');
app.set('views', path.join(__dirname, 'views'));

```

在同一个文件中，创建一个以如下方式呈现文件的路径:

```
app.get('/index', (request, response) => {
  response.render('index', {
    subject: 'Pug template engine',
    name: 'our template',
    link: 'https://google.com'
  });
});

```

render 方法获取文件名(不带扩展名)，然后获取模板文件中变量的值。

### 在 Pug 中使用偏旁音

让我们在 partials 子文件夹中创建一个名为`_nav.pug`的可重用 nav 文件；我喜欢在部分前面加上下划线(`_`)。这将包含我们的导航菜单，代码如下:

```
nav.navbar.navbar-dark.bg-primary.navbar-expand
    .container
        a.navbar-brand(href='#') TMP
        ul.navbar-nav.mr-auto
            li.nav-item
                a.nav-link(href='#')
                    span Home
            li.nav-item
                a.nav-link(href='#')
                    span About
            li.nav-item
                a.nav-link(href='#')
                    span Menu
            li.nav-item
                a.nav-link(href='#')
                    span Contact
        span.navbar-text
            a.nav-link(href='#')
                span  Login

```

然后，我们可以在`body`标签中使用`include`关键字将部分内容包含在我们的`index.pug`文件中:

```
body
    include  partials/_nav  // partial included here
    div.container.mt-2

```

访问`localhost:5000/index`,你应该会看到这样的内容:

![Partials Pug Template Homepage Localhost](img/48d52d7c16c901462a419b854ea2ee0e.png)

这是对 Pug 模板的简单介绍。可以从官方 [pug 网站](https://pugjs.org/api/getting-started.html)了解更多。

## EJS

EJS 比 Pug 更类似于 HTML，保留了打开和关闭标签以及指定属性的常用方法。变量使用尖括号和百分号声明，如下所示:`<%= name %>`。

EJS 标签可以以不同的方式使用:

*   `<%=`–对提供的值进行转义，并输出到模板
*   `<%-`–输出给定值，不转义。建议您在渲染之前对所有 HTML 变量进行转义，以防止跨站脚本(XSS)攻击
*   `<%`–执行控制操作，例如使用条件 or 循环

### 将 EJS 集成到 Express 中

首先，从 npm 安装`ejs`包:

```
npm i ejs

```

接下来，将 app `view engine`设置从帕格切换到 EJS:

```
app.set('view engine', 'ejs');

```

现在，在 EJS 重写我们上面的 Pug 代码，在您的`views`文件夹中创建一个`index.ejs`文件，并添加以下代码:

```
<!DOCTYPE html>
<html>
  <head>
    <meta name="viewport" content="width=device-width" />
    <link
      rel="stylesheet"
      href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css"
    />
    <title><%= subject %></title>
  </head>
  <body>
    <div class="container mt-2">
      <header>
        <h2>Welcome</h2>
        <p>Here is the homepage for <%= name %></p>
      </header>
      <section>
        <h2>Here is the body</h2>
        <p>
          Lorem ipsum dolor sit, amet consectetur adipisicing elit. Totam,
          repellendus!
        </p>
      </section>
      <footer>
        <h2>Here is the footer</h2>
        <p><a href="<%= link %>">Unsubscribe</a></p>
      </footer>
    </div>
  </body>
</html>

```

### 在 EJS 使用偏音

在`partials`子文件夹中，创建一个`_nav.ejs`文件并添加以下代码:

```
<nav class="navbar navbar-dark bg-primary navbar-expand">
  <div class="container"><a class="navbar-brand" href="#">TMP</a>
    <ul class="navbar-nav mr-auto">
      <li class="nav-item"><a class="nav-link" href="#"><span>Home</span></a></li>
      <li class="nav-item"><a class="nav-link" href="#"><span>About</span></a></li>
      <li class="nav-item"><a class="nav-link" href="#"><span>Menu</span></a></li>
      <li class="nav-item"><a class="nav-link" href="#"><span>Contact</span></a></li>
    </ul><span class="navbar-text"><a class="nav-link" href="#"><span> Login</span></a></span>
  </div>
</nav>

```

您可以将 nav partial 添加到您的索引文件中，方法是将 partial 文件的相对路径添加到`include`关键字中，修改刚好在`body`标签下面的行:

```
<body>
    <%- include('./partials/_nav'); %> // partial included here
    <div class="container mt-2">
      <header>

```

将以下路线添加到`app.js`文件中:

```
app.get('/index', (request, response) => {
  response.render('index', {
    subject: 'EJS template engine',
    name: 'our template',
    link: 'https://google.com'
  });
});

```

现在，当您启动您的服务器并访问`/index`路线时，页面应该使用 Pug 模板呈现类似的内容。

## 把手

像 EJS 一样，Handlebars 试图忠实于通常的 HTML 风格，用一种简单的方法插入变量，使用两个括号——例如，`{{variable}}`——或三个括号表示 HTML 非转义字符。它是基于[小胡子](https://mustache.github.io/)模板引擎构建的，所以它们有一些相似之处。

有许多 npm 包可用于在 Express 中编译和渲染手柄，但我将使用`hbs`包。这是通过以下命令安装的:

```
npm i hbs

```

然后，我们将视图引擎设置为`hbs`,使用:

```
app.set('view engine', 'hbs');

```

对我们以前的索引页面的重写看起来像这样:

```
<!DOCTYPE html>
<html>
<head>
    <meta name="viewport" content="width=device-width" />
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" />
    <title>
        {{subject}}
    </title>
</head>
<body>
    <div class="container mt-2">
        <header>
            <h2>Welcome</h2>
            <p>Here is the homepage for {{name}}
            </p>
        </header>
        <section>
            <h2>Here is the body</h2>
            <p>
                Lorem ipsum dolor sit, amet consectetur adipisicing elit. Totam,
                repellendus!
            </p>
        </section>
        <footer>
            <h2>Here is the footer</h2>
            <p><a href="{{link}}">Unsubscribe</a></p>
        </footer>
    </div>
</body>
</html>

```

### 在车把上使用零件

使用与前面相同的文件结构，在`partials`文件夹中创建一个`_nav.hbs`文件。我们将在上面的`_nav.ejs`文件中使用相同的代码，因为它是普通的 HTML 语法，没有定义任何变量。

与其他模板引擎不同，在使用之前，您必须在手柄中注册您的 partials。在您的`app.js`文件中导入`hbs`模块，并使用`registerPartials`方法:

```
const hbs = require('hbs');
hbs.registerPartials(path.join(__dirname, 'views/partials'));

```

注册后，在将它包含在一个`hbs`文件中时，只需使用部分的文件名而不是相对路径。将一个部分包含到另一个`hbs`文件中的语法是`{{>partialname}}`。您可以在索引文件中包含您的 nav partial，方法是在开始的`body`标记下面添加修改行:

```
<div class="container mt-2">
    {{>_nav}}  // partial included here
    <header>

```

将以下路径添加到`app.js`中以呈现索引文件:

```
app.get('/index', (request, response) => {
  response.render('index', {
    subject: 'hbs template engine',
    name: 'our template',
    link: 'https://google.com'
  });
});

```

访问`/index`路线应该在 Pug 应用程序中呈现一个类似于相同路线的页面。

## 结论

模板引擎非常容易设置，几乎不需要样板文件，您可以用它们创建大型应用程序，而不必花太多时间学习语法。您可以通过访问他们的文档开始创建从服务器渲染的动态网页来了解他们的更多信息。

这个应用程序的源代码在 [GitHub](https://github.com/supercede/express-templates.git) 上，每个模板引擎在一个不同的分支中，以相同的名字命名。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.