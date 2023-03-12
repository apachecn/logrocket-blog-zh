# 使用 Azure 静态 Web 应用程序:一个网络替代方案

> 原文：<https://blog.logrocket.com/azure-static-web-apps-netlify-alternative/>

Jamstack 网站已经风靡全球，Netlify 和 Cloudflare 等产品之间的激烈竞争就是明证。Azure 最近通过其新的静态 Web 应用程序(SWAs)产品加入了这个高度竞争的圈子。

在本教程中，我们将向您展示如何使用[静态 Web 应用](https://azure.microsoft.com/en-us/services/app-service/static/)，并演示如何使用 GitHub 操作部署一个。

## Jamstack 和 Azure 静态 Web 应用

[Jamstack](https://en.m.wikipedia.org/wiki/Jamstack) 代表 JavaScript、API 和标记。在 [Jamstack 网站](https://blog.logrocket.com/jamstack-in-2021-past-present-and-future/)中，应用程序逻辑通常驻留在客户端。这些客户端通常被构建为[单页应用](https://en.m.wikipedia.org/wiki/Single-page_application)，并且通常为每个可能的路径静态生成 HTML 文件，以支持搜索引擎优化。

Azure Static Web Apps 于 2021 年 5 月发布，供大众使用。其特点包括:

*   面向生产应用的全球分布式内容
*   自动配置的预览环境
*   自定义域配置和免费 SSL 证书
*   对各种身份验证提供程序的内置访问
*   基于路由的授权
*   自定义路由
*   与由 Azure 函数支持的无服务器 API 集成
*   自定义的 [Visual Studio 代码开发扩展](https://blog.logrocket.com/top-10-vs-code-extensions-2021/)

值得注意的是，这些功能[可以免费使用](https://azure.microsoft.com/en-gb/pricing/details/app-service/static/)。有了 Netlify，还有一个[自由层](https://www.netlify.com/pricing/)；然而，很容易超出免费层的构建限制，并让自己承担意想不到的费用。通过将 Azure 静态 Web 应用程序与 GitHub 操作相结合，我们可以构建类似的体验并为自己省钱！

因此，让我们为自己构建一个简单的 SWA，并用 GitHub 操作部署它。

## 创建 Docusaurus 站点

在我们的库的根目录中，我们将创建一个 [Docusaurus 站点](https://blog.logrocket.com/easy-documentation-with-docusaurus/)。Docusaurus 是静态站点的一个很好的例子，这种静态站点非常适合 Jamstack。我们也可以用别的，比如[雨果](https://gohugo.io/)。

在命令行中，我们将输入:

```
npx [email protected] website classic

```

这将导致 Docusaurus 在`website`目录中创建一个新站点。

让我们承诺并推动此事，并将我们的注意力转向 Azure。

## 在 Azure 中创建静态 Web 应用程序

有很多方法可以在 Azure 中创建静态 Web 应用。有可能用 Bicep 这样的语言将[基础设施用作代码。但是对于本教程，我们将使用](https://blog.johnnyreilly.com/2021/08/15/bicep-azure-static-web-apps-azure-devops#bicep-template) [Azure 门户](https://portal.azure.com)来代替。

如果您还没有帐户，您可以很快免费注册一个。

登录后，点击**创建资源**并查找静态 Web 应用程序:

![Azure Static Web Apps: Create A Resource](img/b5ecc5ad23c1178a0aa7be64758a20f6.png)

点击**创建**，你将进入创建对话框:

![Azure Static Web Apps Creating Dialog](img/bfeab4986cb573fb4d15a26cf8c73987.png)

您需要为您的 SWA 创建一个资源组。给 app 起个名字，选择免费计划，输入 GitHub 作为部署源。

点击**使用 GitHub** 按钮登录，并授权 Azure 访问您的 GitHub 静态 Web 应用帐户。

![Azure Static Web Apps: Sign In With GitHub](img/9a28f875d813fec05a426713af9150e4.png)

此时，Azure 将代表您查询 GitHub，并查找您有权访问的组织和存储库。选择要部署到静态 Web 应用程序的存储库，并选择要部署的分支。

你还需要向 Azure 提供一些构建细节，帮助它理解你的应用是如何构建的。我们将提供一个预置的**自定义**。我们将把**应用程序的位置**(我们前端应用程序的根)设置为`"/website"`，以符合我们刚刚创建的应用程序。

我们将把 **API 位置**留空，并将输出位置设置为`"build"`——这是`website`下的目录，Docusaurus 将在这里创建我们的站点。

最后，点击**审核+创建**，然后点击**创建**。

Azure 现在将:

*   在 Azure 中创建 Azure 静态 Web 应用程序资源
*   更新您的存储库，添加一个 [GitHub 动作工作流](https://blog.logrocket.com/github-actions-how-to-autodeploy-your-app/)来部署您的静态 web 应用程序
*   开始 GitHub Actions 工作流的第一次运行，以部署您的 SWA。

很神奇，对吧？

当您查看 Azure 中的资源时，它看起来像这样:

![Azure Static Web Apps: Resources](img/bcc6eb3b7632fa566c871edaeef875f5.png)

如果您点击 GitHub 操作运行，您将看到您的 GitHub 操作:

![Azure Static Web Apps: GitHub Action](img/182a6abd73ee78f310aa0a40e9bfefa4.png)

运行完成后，您可以通过点击 Azure 门户中的 URL 来查看您部署的静态 Web 应用程序:

![Azure Static Web Apps: SWA Deployed](img/5bae2ff6feca2dea0fb90ea004624af4.png)

我们已经从一无所有到在 Azure 中拥有一个全新的网站，通过在 GitHub Actions 中的持续部署在几分钟内交付。这就是低摩擦高价值！

## 证明

现在我们已经完成了初始部署，让我们更进一步，添加身份验证。

静态 Web 应用程序的一大优点是[身份验证是现成可用的](https://docs.microsoft.com/en-us/azure/static-web-apps/authentication-authorization?tabs=invitations#login)。我们可以选择 GitHub、Azure Active Directory 和 Twitter 作为身份提供者。

让我们使用 GitHub 并修改我们的`website/src/pages/index.js`来支持认证:

```
import React, { useState, useEffect } from 'react';
import clsx from 'clsx';
import Layout from '@theme/Layout';
import useDocusaurusContext from '@docusaurus/useDocusaurusContext';
import styles from './index.module.css';

/**
 * @typedef {object} UserInfo
 * @prop {"github"} identityProvider
 * @prop {string} userId
 * @prop {string} userDetails
 * @prop {string[]} userRoles
 */

/**
 * @return {UserInfo | null}
 */
function useUserInfo() {
  const [userInfo, setUserInfo] = useState(null);

  useEffect(() => {
    async function getUserInfo() {
      const response = await fetch('/.auth/me');
      const payload = await response.json();
      const { clientPrincipal } = payload;
      return clientPrincipal;
    }

    getUserInfo().then((ui) => setUserInfo(ui));
  }, []);

  return userInfo;
}

export default function Home() {
  const { siteConfig } = useDocusaurusContext();
  const userInfo = useUserInfo();

  return (
    <Layout
      title={`Hello from ${siteConfig.title}`}
      description="Description will go into a meta tag in <head />"
    >
      <header className={clsx('hero hero--primary', styles.heroBanner)}>
        <div className="container">
          <h1 className="hero__title">{siteConfig.title}</h1>
          <p className="hero__subtitle">{siteConfig.tagline}</p>
          <div className={styles.buttons}>
            {userInfo ? (
              <p>Hello {userInfo.userDetails}</p>
            ) : (
              <a
                className="button button--secondary button--lg"
                href="/.auth/login/github"
              >
                Click here to login
              </a>
            )}
          </div>
        </div>
      </header>
    </Layout>
  );
}

```

上述代码执行以下操作:

*   实现一个名为`useUserInfo`的 React 钩子，它调用 SWA 的`/.auth/me`端点。这将在未通过身份验证时返回`null`，在通过身份验证时返回`UserInfo`
*   为未经身份验证的用户显示链接按钮。链接指向`/.auth/login/github`，从而触发 GitHub 认证流程
*   对于通过身份验证的用户，显示用户的`userDetails`，即 GitHub 用户名

让我们提交并推送它，当我们的构建运行完毕后，再次浏览我们的静态 Web 应用程序:

![Azure Static Web Apps Authentication](img/eca5c64b5f0a9c1cf18bac221c64be71.png)

如果我们点击登录，我们将通过 GitHub 认证流程:

![Azure Static Web Apps Authentication Flow](img/124208c53bc7f183e7fad12bcc980cf3.png)

授权并同意后，您将被重定向到您的应用程序，并看到您已登录:

![Azure Static Web Apps: Logged In](img/92005970fd63d31405c698e5805693cb.png)

如果我们打开 Chrome 的 devtools，我们会看到从`/.auth/me`端点返回的内容:

![Azure Static Web Apps Chrome DevTools](img/a58764ee52fc5f203bf0236e34dcfdbe.png)

```
{
  "clientPrincipal": {
    "identityProvider": "github",
    "userId": "1f5b4b7de7d445e29dd6188bcc7ee052",
    "userDetails": "johnnyreilly",
    "userRoles": ["anonymous", "authenticated"]
  }
}

```

现在，我们已经用 Azure 静态 Web 应用程序实现并演示了身份验证，而且不费吹灰之力。这太棒了！

## 暂存环境

最后，让我们看看静态 Web 应用程序默认提供的一个超级酷的功能。如果您看一下 SWA 的**环境**选项卡，您会看到:

![Azure Static Web Apps Resource Environment Portal](img/9b01e89e5ff43d416790ac3efb51ddd6.png)

### 分期

针对链接的存储库打开“拉”请求，以创建登台环境。

让我们试试吧！我们将创建一个新分支:

```
git checkout -b feat/show-me-staging

```

在我们的`index.js`中，我们将添加任意一段文本:

```
<p>I'm a staging environment!</p>

```

然后，我们将提交并将我们的分支推送到 GitHub，并创建一个 pull 请求。这将触发我们的 GitHub 操作再次运行。

时间，而不是在我们现有的静态 Web 应用程序上发布，它将随着我们的变化旋转出一个全新的。它还会在我们的 GitHub pull 请求中为我们显示一个链接，这样我们就可以直接浏览到它:

![Azure Static Web Apps Staging Environment](img/12f69f1338140375bd697b33aa5ff6c5.png)

这相当于 Netlify 部署预览，用 Azure 静态 Web 应用和 GitHub 动作实现。GitHub 的活动津贴目前是每月 2000 分钟(T1)，而 Netlify 的每月 300 分钟(T3)是免费的，所以你不太可能因为使用静态网络应用而收到账单。

这个暂存环境将只持续到拉请求关闭为止。此时，环境被 GitHub 动作破坏了。

## 结论

在本教程中，我们使用 GitHub 操作将一个网站部署到一个静态 Web 应用程序，并实现了身份验证。我们还演示了 Azure 的 Netlify 部署预览版，以及如何设置临时环境。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)