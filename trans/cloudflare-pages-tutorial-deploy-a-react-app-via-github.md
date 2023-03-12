# Cloudflare Pages 教程:通过 GitHub 部署 React 应用程序

> 原文：<https://blog.logrocket.com/cloudflare-pages-tutorial-deploy-a-react-app-via-github/>

## 介绍

React 是一个非常流行的前端库，用于构建用户界面。由于输出是 HTML、CSS 和 JavaScript，它可以托管在多个服务上，比如 Github pages 或 Netlify 等等。

最近，Cloudflare Pages 已经成为[普遍可用的](https://blog.cloudflare.com/cloudflare-pages-ga/)，这使得它成为另一个托管 React 应用程序的伟大服务。Cloudflare Pages 安全、快速且高度可扩展。

在本分步指南中，我们将在 Cloudflare 页面上演示 React 应用程序设置。我们走吧。

## 为什么选择 Cloudflare 页面？

当然，React(或任何其他静态输出)可以免费托管在至少六个服务上。我们为什么应该选择 Cloudflare 页面？

除了预览部署和 Github 集成等常规功能外，Cloudflare Pages 还具有自定义域、重定向和无限带宽，所有这些都是免费的。所有这些功能每月零美元，我们得到了快速的网络，安全与免费的 SSL，以及惊人的可扩展性。

![Screenshot of Cloudflare Pages homepage](img/4580bf9d0ba24377cdb5d88d49a376d3.png)

## 先决条件

在我们进入下面的代码之前，这里有一些先决条件:

1.  1.  以前的 JavaScript 和 React 知识
    2.  以前对 npm 和 npx 的了解
    3.  在您选择的环境中工作的 Node.js 和 npm/yarn
    4.  以前的 Git 知识，和一个工作的 Github 帐户
    5.  一个 Cloudflare 帐户，你可以免费注册这个帐户

是时候开始了。

## 使用创建 React 应用程序设置 React 应用程序

作为本指南的第一步，我们将使用[创建 React 应用程序](https://create-react-app.dev/) (CRA)来设置 React。如果你没有安装 CRA，你可以用`npm install -g create-react-app`来安装。要使用 CRA 启动 React 应用程序样板文件，我们将执行以下命令:

```
bash
npx create-react-app react-cloudflare-pages

```

这将为我们建立整个 React 样板文件。它一运行，我们就会看到如下所示的输出:

![Screenshot of development environment after installing Create React App](img/7f245fe6529f8a82d8085834ae3555d2.png)

下载并设置 React 样板文件需要几分钟时间，最后输出如下:

![Screenshot of Create React App output after install](img/6f4496782b83e77fd4e100c5a09afbcc.png)

如上图所示，为了检查我们的 React 应用程序是否正常工作，我们可以运行:

```
bash
cd react-cloudflare-pages
yarn start

```

它将运行开发服务器，您可以在默认浏览器中运行`[https://localhost:3000](https://localhost:3000)`,显示如下输出:

![Default Create React App screen](img/5dc77c7bc38124c4dc243ee9df55549f.png)

恭喜你。我们与 CRA 的反应样板工作。

接下来，我们将把这个基本代码推送到 Github，稍后修改它以从模拟 API 获得用户列表，并显示它的一些字段。

## 将样板代码推送到 Github

为了推送我们的基本 React 样板文件，我们将创建一个新的 Github 资源库，如下所示:

![Screenshot of Github's Create New Repository menu](img/3a0b676b162f428e469ffd8ee51befba.png)

保持它为公共的和空的是可以的。滚动到页面底部，点击**创建存储库**按钮。我们将看到一个类似于下图的页面:

![Screenshot of Github page after new repository is created](img/e02353bfd95c52badab77d6fc7983c0e.png)

接下来，将 SSH URL 复制到 Github 存储库，在我的例子中是`[[email protected]](/cdn-cgi/l/email-protection):geshan/react-cloudflare-pages.git`，并将其添加为远程，在我们的项目根目录中运行以下命令来推送代码:

```
bash
git remote add origin [email protected]:geshan/react-cloudflare-pages.git
git push origin master

```

它看起来会像下面这样:

![Screenshot of code being pushed to Github](img/8fec5fb0f1928ee1c33d56ae30a1825a.png)

因此，我们将更新代码来调用模拟 API，并列出一些用户详细信息。如果您愿意，您可以创建一个新的 Git 分支来完成下面的更改，但是我将把这留给您自己。

## 更新样板文件以调用模拟 API

我们的 React 样板文件已经建立并运行，它也已经被推送到 GitHub。现在，我们将更改代码来调用 10 个用户的模拟列表，并在 React 应用程序中显示一些字段。

为此，我们将修改`src/App.js`如下所示:

```
js
import {Component} from 'react';
import Users from './components/users';

class App extends Component {
  state = {
    users: []
  };

  componentDidMount() {
    fetch('https://jsonplaceholder.typicode.com/users')
      .then(res => res.json())
      .then((data) => {
        this.setState({ users: data })
      })
      .catch(console.log)
  }

  render() {
    return (
      <Users users={this.state.users} />
    )
  }
}

export default App;

```

请注意，我们已经删除了不必要的 CSS 文件和标志。

是时候更深入地研究一下代码在做什么了。首先，我们从 React 导入`Component`，然后导入另一个名为`users`的组件，接下来我们将编写这个组件。

然后在扩展 React 组件的`App`类中，我们用`users`数组引入了一个状态。当组件挂载时，我们从我们的模拟 API——`[http://jsonplaceholder.typicode.com/users](http://jsonplaceholder.typicode.com/users)`——获取 10 个用户的列表，并将其设置在`state`的`users`中。如果有错误，我们会记录下来。

对于主`App`组件中的最后一步，我们渲染了`Users`组件，我们将在接下来编写它。

当我们使用上面的`users`组件时，我们将创建一个包含以下内容的新文件`src/components/users.js`:

```
js
const Users = ({ users }) => {
  return (
    <div className="users">
      <center><h1>Application Users</h1></center>
      {Array.isArray(users) && users.map((user) => (
        <div className="user" key={user.id}>
          <div className="user-details">
            <h2 className="user-name">{user.name}</h2>
            <h3 className="user-email">{user.email}</h3>
            <h4 className="user-company">Company: {user.company.name}</h4>
            <hr/>
          </div>
        </div>
      ))}
    </div>
  )
};

export default Users

```

让我们看看这个文件是如何工作的。它是一个非常简单的组件，依赖于作为数组传入的用户。我们检查它是否是一个数组，并遍历用户。然后我们打印出用户名、电子邮件和用户的公司名称。

如果需要，我们可以在更改后使用以下命令删除不必要的文件:

```
bash
rm -rf src/App.css src/logo.svg src/App.test.js

```

之后，我们可以运行`yarn start`并看到以下输出:

![Screenshot of application users with name, email, and company name](img/93e295d316783404945d0814dc290ed0.png)

万岁！模拟 API 被正确调用，我们看到用户名、电子邮件和公司名称被正确呈现。在此之后，我们将把我们的代码更改推送到 GitHub，并将其与 Cloudflare 页面连接起来。如果您想查看哪些文件发生了更改，您可以查看这个[拉取请求](https://github.com/geshan/react-cloudflare-pages/pull/1/files)。

## 将应用推送到 Github

为了将我们的更改推送到主分支，我们将在项目根上运行以下命令:

```
bash
git add .
git add -u #because we have deleted files
git commit -m "Call the mock users API"
git push origin master # if you are in a different branch push that and open a PR

```

推送之后，您应该也能看到反映在 Github 存储库中的变化。现在是时候为我们的 React 应用程序获取一个 Cloudflare Pages URL 了。

## 登录到 Cloudflare 页面

要将 React 应用程序与 Cloudflare 页面连接，如果您尚未创建 Cloudflare 帐户，请创建一个。然后，登录 Cloudflare，点击右侧的**页面**:

![Screenshot of Google Cloudflare home page](img/b4c74b9ff80f96baa41c78cfff4777f8.png)

我们将看到一个类似如下的页面:

![Screenshot of cloudflare pages connect github tab](img/6aff0b8024eb77a9e749889ad3deafd7.png)

点击**连接 Github 账户**。我们将被重定向到 Github，在这里我们可以配置希望 CloudFlare 访问的帐户和存储库:

![Screenshot of Github install cloudflare pages tab](img/bb7b665d87e6fcac667b052c1e71c24d.png)

单击您将上述 React 应用程序推送至的帐户(可能是您的个人帐户)，并选择要连接的正确存储库:

![Screenshot of Github install and authorize Cloudflare pages tab](img/1c2bea5565e158c671f66f72a2e51652.png)

最好只授予对所需存储库的访问权限。授予访问权限后，我们将被重定向到 Cloudflare 页面，在这里我们可以部署 React 应用程序，选择存储库，然后单击**开始设置**，如下所示:

![Screenshot of Cloudflare Pages after successful link to Github repository](img/f413d60cbc8536c9b5d6a09d176cabf8.png)

接下来，我们将主要分支配置为主分支:

![Screenshot of Cloudflare pages set up builds and deployments tab](img/4c8522a75a6a39ae3244f6d517819a7f.png)

滚动页面下方以配置构建设置，如下所示:

![Screenshot of cloudflare pages build settings tab](img/50d2c970db1208cea6ba5e2c8a8ec651.png)

我必须在构建命令字段中用`yarn build`更新`npm build`,因为 CRA 使用 yarn 代替 npm。

然后点击**保存并部署**。如果一切顺利，项目将被创建并开始部署，如下所示:

![Screenshot of Cloudflare Pages with Github repo under Pages section](img/93a8025b25ad8d80ab95b81c16bc4756.png)

几分钟后，构建应该完成了，如果我们单击项目的标题 **react-cloudflare-pages** ，我们将看到如下所示的页面:

![Screenshot of github repo successfully deployed in Cloudflare pages](img/2dfadfab34e8df2fd549b046198576b3.png)

如果我们单击**部署、**下方的链接，这是一个`pages.dev` URL，我们应该能够看到部署在 Cloudflare 页面上的 React 应用程序，如下所示:

![Screenshot of application users app deployed via Cloudflare pages](img/8d5ddbbd75c68279c5644690ee0bff8b.png)

如上图所示，这是一个位于`pages.dev`的 HTTPS Cloudflare 页面的 URL。

## 预览实际部署

为了让我们看到预览部署是如何工作的，我将在`src/components/users.js`的第 10 行把`Company`标签改为`Company Name`。

我们将在一个名为`preview-deployment`的分支中推送它，当我们打开一个 pull 请求时，它会在 Github 上给我们一个预览。在 Cloudflare 页面上成功构建后，它将为我们提供一个指向更改的唯一链接作为注释，如下所示:

![Screenshot of Github repo with comment by Cloudflare pages stating successful deployment](img/8554a2994821b760b5ef0d7037dc1d5d.png)

这是一个非常简洁的特性，可以在一个功能完整的 URL 上看到变化。

## 后续步骤

我们可以轻松地在 Cloudflare 页面上为 React 应用程序配置一个自定义域，这也是免费的。此外，还可以配置预览部署的[重定向](https://developers.cloudflare.com/pages/platform/redirects)和[访问控制](https://developers.cloudflare.com/pages/getting-started#access-policies)。我将由你来探索它们。

## 结论

正如我们在这个分步教程中看到的，Cloudflare Pages 很容易为您的 React 应用程序或任何其他前端框架进行设置。没有必要在 Github actions 或其他 CI 系统上配置构建过程。

将您的 Github repo 与 Cloudflare 页面连接，并使用一次 build 命令，因为每个 pull 请求都会自动获得一个预览部署 URL。免费利用 Cloudflare 页面的强大功能，让您的网站如鱼得水。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)