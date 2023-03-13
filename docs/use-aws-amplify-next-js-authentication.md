# 如何使用 AWS Amplify 进行 Next.js 认证

> 原文：<https://blog.logrocket.com/use-aws-amplify-next-js-authentication/>

认证是全栈应用中[信息安全](https://blog.logrocket.com/web-security-101/)的基石之一。它的主要目的是通过检查提供的凭证是否与数据库中的记录匹配来证明用户的身份。创建一个[安全认证系统](https://blog.logrocket.com/implement-authentication-authorization-next-js/)会花费很多时间和精力。

在本文中，我们将进一步了解如何通过使用 [AWS Amplify](https://aws.amazon.com/amplify/) 来简化 Next.js 中身份验证的实现。这是一个完整的解决方案，可以创建功能丰富的全栈 web 和移动应用程序，这些应用程序易于设置并随着业务的增长而扩展。

*向前跳转:*

## 身份验证方法入门

最常见的认证方法之一是使用用户名[和密码](https://blog.logrocket.com/adding-login-authentication-secure-react-apps/)[进行认证。然而，还有许多其他方法，例如](https://blog.logrocket.com/implementing-secure-password-reset-node-js/)[魔法链接](https://blog.logrocket.com/authenticating-react-applications-with-magic-links/)、[生物数据](https://blog.logrocket.com/implementing-react-native-biometric-authentication-expo/)或[安全令牌](https://blog.logrocket.com/developers-guide-smart-contract-security-audits/)，等等。还值得一提的是，身份验证经常与授权混淆，授权是一个不同的过程，它决定了用户或服务在应用程序中的访问级别。

[Amplify](https://blog.logrocket.com/aws-amplify-a-review-of-advanced-features/) 拥有自己的 CLI、visual interface studio、Amplify UI 组件，以及开源的客户端库，为实现认证提供了流畅的设置。我们将在本教程中使用它们。

### 设置 Next.js 项目

首先，打开您的终端并运行`npm create-next-app amplify-auth`。这个 scaffold 命令将创建一个功能完整的 Next.js 项目。接下来，通过运行`cd amplify-auth`将当前工作目录更改为新创建的项目。然后，通过运行`npm install aws-amplify @aws-amplify/ui-react`安装 Amplify 库和 Amplify 组件 UI。

之后，通过运行`npm run dev`启动开发服务器。最后，打开浏览器，导航到 [http://localhost:3000](http://localhost:3000) 。您应该会看到 Next.js 欢迎屏幕:

![Next.js Welcome Screen](img/5e3f9dfe4f9c5ab3bdd5e1299fa76fe8.png)

### 创建 AWS 帐户并设置 CLI

首先，创建一个免费的 [AWS 账户](https://portal.aws.amazon.com/billing/signup#/start/email)，如果你还没有的话。切换回您的项目，并在您的终端中运行`npm install -g @aws-amplify/cli`。接下来，运行`amplify configure`。这将自动打开 AWS 控制台，以确保您拥有帐户并已登录。之后，您可以切换回您的代码编辑器，按 **`enter`** ，并为您的 AWS IAM 选择您的地区和用户名。

接下来，按住键盘上的 **`Control`** 键，单击给定的链接打开 AWS 控制台，并使用 GUI 完成设置配置:

![AWS Amplify GUI](img/f04c8f0b2a3c05b9475121bfe320b24a.png)

首先会显示您在终端向导中选择的用户名。点击**下一步:权限**继续。它应该是这样的:

![Using AWS With Next.js Part One](img/46e72c2ffe6c1fc9a9dfb73f536ecf34.png)

在下一步中，您将看到权限级别。对于我们的 Amplify 应用程序，我们将使用`AdministratorAccess-Amplify`(默认设置)。选择**下一步:标签**继续:

![Using AWS With Next.js Part Two](img/98083b886531e8c3e3bb3db810f52da8.png)

AWS 提出使用额外的标签，以便在应用规模扩大时更容易组织用户。这是一个可选步骤，我们现在不会设置任何步骤。点击**下一步:查看**继续:

![Using AWS With Next.js Part Three](img/bae929dd61f2fd38de80349683356eaf.png)

在此步骤中，您将看到您为创建新用户而选择的配置选项的摘要。点击**创建用户**继续:

![Using AWS With Next.js Part Four](img/651cd9caeaaca63e7efceb1e9097cf99.png)

最后，您将看到成功消息、访问密钥 ID 和秘密访问密钥。暂时保持此选项卡打开:

![Using AWS With Next.js Part Five](img/06c5c36df17b82fe06b20829e9429a2e.png)

现在，打开您的终端，将两个值从浏览器粘贴到终端向导中。为您的本地 AWS 配置文件设置用户名，您应该会在终端中收到一条成功消息，如下所示:

![AWS Amplify Success Screen](img/6df1c50b412540a5b43cfeb527df1759.png)

### 建立一个新的放大器后端

现在，要构建一个新的 Amplify 后端，请确保您仍然登录在 AWS 中，并导航到 [Amplify 控制台](https://console.aws.amazon.com/amplify/)，向下滚动并单击构建应用程序消息下的**开始**:

![Using Amplify With Next.js Part One](img/3149a21f41c6770f9bfb7aa32c2ed273.png)

接下来，给你的应用命名并选择**确认部署**。在本教程中，我们将使用`amplify-auth`:

![Using Amplify With Next.js Part Two](img/d14184d60b5332e422a45bdef33ec577.png)

等待部署完成，点击 **Launch studio** 打开可视化界面管理您的项目:

![Using Amplify With Next.js Part Three](img/e8da80dee653ea3a1e592c49efc16337.png)

接下来，选择左侧工具条中的**认证**。点击**删除电子邮件登录**，因为我们将选择通过用户名和密码登录:

![Using Amplify With Next.js Part Four](img/6164904c1acfd54a2e2cdeb1ef6c379c.png)

现在，在**添加登录**机制下拉菜单中选择**用户名**，并选择**部署**为您的项目设置认证，如下图所示:

![AWS Next.js Set Up](img/65d9aa750b46cd53bd963de1ffefa3ed.png)

该过程完成后，您将看到一个屏幕，其中包含一个终端命令来设置配置文件。复制命令并点击 **Done** :

![Using Amplify With Next.js Part Five](img/02c16cd3549fa0ba592d237b6e2d9f4d.png)

### 调出配置文件

现在，在您的终端中执行您刚刚从 Amplify Studio 复制的命令。它将为您的 Next.js 项目获取最新的客户端配置文件。

这将要求您登录到 Amplify CLI。点击**是**，返回终端继续配置 Amplify app。您将被要求选择您的代码编辑器、您正在构建的项目类型以及您正在使用的 JavaScript 框架。按下**键，输入**来选择这些的默认值。手动设置向导的其余选项，如下所示:

![Amplify Wizard](img/ff9c6ae8e1990e940b60a1c9d92490ba.png)

## 在 Next.js 中设置放大器

为了使整个应用程序都可以访问 Amplify 库和 UI，导航到`pages`文件夹并将`_app.js`文件更改为以下内容:

```
import { Amplify } from "aws-amplify";
import awsExports from "../aws-exports";

import "@aws-amplify/ui-react/styles.css";
import "../styles/globals.css";

Amplify.configure({ ...awsExports, ssr: true });

function MyApp({ Component, pageProps }) {
  return <Component {...pageProps} />;
}

export default MyApp;

```

我们首先为登录 UI 导入了 Amplify 库、设置和样式表，并为 Next.js 项目本身导入了全局样式表。然后，我们配置 Amplify 使用导入的参数，并将服务器端渲染设置为`true`。此时，我们准备开始使用 Amplify 来控制页面和组件中的用户身份验证状态。

### 创建和保护前端

打开`pages`文件夹中的`index.js`，包含以下代码:

```
import { Authenticator } from "@aws-amplify/ui-react";

export default function Home() {
  return (
    <Authenticator>
      {({ signOut, user }) => (
        <main>
          <h1>Hello, {user.username}!</h1>
          <button onClick={signOut}>Sign out</button>
        </main>
      )}
    </Authenticator>
  );
}

```

我们为主页创建了一个`Authenticator`组件，这样用户需要创建一个帐户或登录才能看到内容。运行`npm run dev`再次启动开发者服务器，打开 [http://localhost:3000](http://localhost:3000) :

![Example of AWS and Next.js Local Host](img/c5e98e6daa0ea129ec9b9455be936fb5.png)

现在，创建一个帐户并尝试登录。您将看到一条问候语，表明您已成功通过身份验证:

![AWS and Next.js Authentication Page](img/d3ba46815bdd0e368b2b2a7d2a63b4c7.png)

### 构建和保护 SSR 页面

将`pages`文件夹中的`index.js`更改为以下代码:

```
import { Authenticator } from "@aws-amplify/ui-react";

export function getServerSideProps() {
  const renderedAt = new Date();
  const buildTime = renderedAt.toLocaleTimeString("en-US");
  return {
    props: {
      renderedAt: buildTime,
    },
  };
}

export default function Home({ renderedAt }) {
  return (
    <Authenticator>
      {({ signOut, user }) => (
        <main>
          <h1>Hello, {user.username}!</h1>
          <button onClick={signOut}>Sign out</button>
          <p>This page was server-side rendered on {renderedAt}</p>
        </main>
      )}
    </Authenticator>
  );
}

```

然后，检查您的开发服务器是否还在工作。如果没有，运行`npm run dev`再次启动。您将看到相同的登录屏幕:

![AWS Auth With Next.js](img/8283595255920a21f92572007fc12c63.png)

再次登录，您将获得来自服务器的渲染时间戳数据:

![AWS Auth Part Three](img/67458c21dd1c44bc3e2dd1eb9e0f2346.png)

## 实现注册和登录定制

通常，您会想要不同的登录方法，而不仅仅是用户名和密码。幸运的是，Amplify 提供完全定制，并允许您在不同的登录机制之间切换。这些支持的方式是`username`、`email`和`phone_number`。

这里有一个例子:

```
import { Authenticator } from "@aws-amplify/ui-react";

export default function Home() {
  return (
    <Authenticator loginMechanisms={["phone_number"]}>
      ...
    </Authenticator>
  );
}

```

现在，登录屏幕应该是这样的:

![Advanced AWS Sign In](img/fbd3cde896dd2728ff94f2abbcf5d91b.png)

您还可以为您的登录页面添加社交提供商。支持的选项有`amazon`、`apple`、`facebook`和`google`。这里有一个例子:

```
import { Authenticator } from "@aws-amplify/ui-react";

export default function Home() {
  return (
    <Authenticator socialProviders={['amazon', 'apple', 'facebook', 'google']}>
      ...
    </Authenticator>
  );
}

```

上面的代码将把登录屏幕变成这样:

![Sign In With Socials](img/7681a098bb731dec222b0635ca5e5f64.png)

你也可以在注册界面询问更多的用户信息。支持的属性有`address`、`birthdate`、`email`、`family_name`、`gender`、`given_name`、`locale`、`middle_name`、`name`、`nickname`、`phone_number`、`picture`、`preferred_username`、`profile`、`updated_at`、`website`、`zoneinfo`。

其用法如下所示:

```
import { Authenticator } from "@aws-amplify/ui-react";

export default function Home() {
  return (
    <Authenticator signUpAttributes={['email', 'address', 'birthdate', 'phone_number', 'website']}>
      ...
    </Authenticator>
  );
}

```

这将把注册视图变成这样:

![Secure Sign In With Next.js](img/f2176b616bbd6330fd1065b98c552f34.png)

## 结论

在本教程中，我们使用 AWS Amplify 在 Next.js 中实现了一个全功能认证系统。我们从设置项目本身、创建 AWS 帐户和配置 CLI 开始。然后，我们创建了 Amplify 后端，并将其与 Next.js 项目配对。

我们回顾了如何保护客户端和服务器端呈现的两种情况。我们还回顾了登录和注册屏幕的各种定制选项，这样您就可以随时根据自己的想法调整特定数据模型的身份验证。总而言之，Amplify 可以显著加快身份验证的创建速度，现在您将知道如何在自己的项目中使用它。

## [LogRocket](https://lp.logrocket.com/blg/nextjs-signup) :全面了解生产 Next.js 应用

调试下一个应用程序可能会很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪状态、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/nextjs-signup)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png)](https://lp.logrocket.com/blg/nextjs-signup)[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/nextjs-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你的 Next.js 应用上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用程序的性能，报告客户端 CPU 负载、客户端内存使用等指标。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

让您调试 Next.js 应用的方式现代化— [开始免费监控](https://lp.logrocket.com/blg/nextjs-signup)。