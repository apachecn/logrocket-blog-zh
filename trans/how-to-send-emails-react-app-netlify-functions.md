# 如何从具有 Netlify 功能的 React 应用程序发送电子邮件

> 原文：<https://blog.logrocket.com/how-to-send-emails-react-app-netlify-functions/>

## 介绍

借助 React、Angular 和 Vue 等流行的 JavaScript 框架和库，只需几个步骤，您就可以开发复杂的单页面应用程序，并在 Netlify 等平台上托管它们。

但是，您可能想要使用这些应用程序来运行作业，例如发送电子邮件和访问秘密 API 凭据，这通常需要设置服务器。这样的工作通常是不可能的，或者在前端运行是不明智的。然而，Netlify 提供了从 Jamstack 应用程序中执行这些操作的特性，而无需部署服务器。

其中一个功能就是 Netlify 函数。本文的目的是教你如何使用事件驱动的 Netlify 函数和 SendGrid 在 React 应用程序中发送电子邮件。该电子邮件将对客户提交的表格做出回应。

尽管我们将使用 React，但对于其他框架来说，这个过程应该是类似的，只是略有不同。

## 网络功能的背景

Netlify 函数，顾名思义，是一个简单的函数，可以在 JavaScript、Typescript 或 Go 中声明，并使用您的前端代码部署到 Netlify。然后，Netlify 将您的功能部署到 AWS，而无需您设置 AWS 帐户。

## 如何声明网络函数

您在前端项目目录的根目录下的文件夹中声明 Netlify 函数。函数的路径用作触发函数的端点。

默认情况下， [Netlify](https://blog.logrocket.com/firebase-vs-netlify-which-one-is-right-for-you/) 会在你的项目根目录下的`netlify/functions`文件夹中寻找你的无服务器函数。如果您想在不同的目录中声明函数，请包含一个`netlify.toml`配置文件，并向其中添加关于函数位置的信息:

```
shell
project-folder
├── node_modules
├── netlify
|   ├── functions
|       ├── send-emails.js
├── package.json
├── package-lock.json

```

你可以在网络文档中阅读更多关于`netlify.toml`配置文件[的信息。](https://docs.netlify.com/configure-builds/file-based-configuration/)

在上面的项目结构中，函数`send-emails.js`已经在`netlify/functions`文件夹中声明。您可以通过向`/.netlify/functions/send-emails`端点发出请求来调用它。

## 事件驱动的网络功能

虽然需要通过调用端点来调用 Netlify 函数，但是 Netlify 函数还有另一种风格，称为事件驱动函数。它们在某些事件被注册后被调用。

一个这样的事件是`submission-created`事件，它在表单提交被 Netlify 验证后发出。函数必须以事件命名，才能成为事件驱动的函数。

在本文中，我们将使用事件驱动函数在验证表单提交后发送电子邮件。

您可以在 Netlify 文档中查看可用事件触发器的完整列表。

## 后台网络功能

普通网络功能在十秒钟后超时。如果你想执行一个比这更长的任务，你将不得不使用后台功能。

后台功能在超时前会在后台异步运行长达 15 分钟。你可以通过在名字上添加一个`-background`扩展名来将普通的 Netlify 函数变成后台函数。比如`send-emails-background.js`就是一个后台函数。

但是，请记住，在撰写本文时，后台函数仍处于试验阶段。

现在让我们创建一个简单的 React 应用程序来学习如何使用 Netlify 函数。

## 创建 React 应用程序

在本节中，我们将使用`create-react-app`搭建一个 React 应用程序。在终端上运行下面的命令，在`email-app`目录中创建一个完全配置的 React 应用程序:

```
npx create-react-app email-app

```

成功运行上述命令后，您应该会看到包含 React 应用程序的`email-app`文件夹。

现在，让我们在应用程序中添加一个联系表单。

## 添加一个`JSX`表单

在`src`目录下创建`ContactForm.js`文件，并粘贴以下代码:

```
export default function ContactForm() {
  return (
    <form name="contact-form" method="post">
      <div>
        <label htmlFor="name">Full Name</label> <br />
        <input
          type="text"
          id="name"
          name="name"
          placeholder="Jane Doe"
          required
        />
      </div>
      <div>
        <label htmlFor="email">E-mail</label> <br />
        <input
          type="email"
          id="email"
          name="email"
          placeholder="[email protected]"
          required
        />
      </div>
      <div>
        <label htmlFor="message">Message</label> <br />
        <textarea
          id="message"
          name="message"
          placeholder="Your message here!"
          required
        ></textarea>
      </div>
      <div>
        <input type="submit" className="submit" value="Send Message" />
      </div>
    </form>
  );
}

```

将下面的代码复制并粘贴到`App.js`中，替换样板代码:

```
import "./App.css";
import ContactForm from "./ContactForm";

export default function App() {
  return (
    <div className="App">
      <ContactForm />
    </div>
  );
}

```

让我们给表单添加一些样式。将以下代码复制并粘贴到`App.css`文件中:

```
body {
  min-height: 95vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: rgb(54, 53, 53);
  color: #c5cdd3;
  font-family: sans-serif;
}
form {
  background-color: rgb(26, 24, 24);
  padding: 1em;
  border-radius: 10px;
}
div {
  padding-top: 1em;
}
input,
textarea {
  min-width: 250px;
  box-sizing: border-box;
  padding: 0.5em;
  outline: none;
  background-color: #222d35;
  border: 2px solid transparent;
  border-radius: 5px;
}
input:focus,
textarea:focus {
  border: 2px solid brown;
  color: #c5cdd3;
}
textarea {
  min-height: 250px;
  resize: vertical;
}
.submit {
  color: #c5cdd3;
}
.App {
  padding: 1em;
}

```

Netlify 有一个管理表单提交的特性；标记文件在部署时被解析。向您的`form`元素添加`data-netlify="true"`或`netlify`属性足以让 Netlify 检测到它并管理开箱即用的表单提交。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

然而，由于我们正在处理 React，所以我们在上面添加的是`JSX`形式。您需要做更多的工作，使您的表单在部署时能够被 Netlify 机器人检测到。

将下面的隐藏表单复制并粘贴到`public`目录下的`index.html`文件中的开始`body`标签之后:

```
<form name="contact-form" netlify netlify-honeypot="bot-field" hidden>
  <input type="text" name="name" />
  <input type="email" name="email" />
  <textarea name="message"></textarea>
</form>

```

上面的表单对用户是隐藏的，因为它将被 Netlify 机器人使用。我们已经剥离了某些元素的形式，留下了最基本的元素。

将您的`JSX`表单中下面的`input`元素复制并粘贴到`ContactForm`组件中的开始`form`标记之后:

```
<input type="hidden" name="form-name" value="contact-form" />

```

默认情况下，Netlify 将开始过滤提交的表单中的垃圾邮件。您还可以配置 Netlify，以便在表单提交通过验证后通过电子邮件、webhook 或 Slack 通知您。

在收到表单提交后，您会希望通知您的客户，他们的邮件已收到，他们的问题正在处理中。然而，Netlify 没有一个内置的功能来发送自动电子邮件回复，确认收到客户的信息。

这将需要您使用 Netlify 函数和第三方 SMTP 服务。有几个 SMTP 服务提供商，如 SendGrid 和 Mailgun 等。在本文中，我们将使用 SendGrid，因为它提供了不需要信用卡的免费试用计划。它还允许你每天发送多达 100 封电子邮件。

在下一节中，我们将注册 SendGrid，验证我们的发送者身份，并获得一个 API 密钥。

## 获取 SendGrid API 密钥

我们将需要一个 API 键来以编程方式与 SendGrid API 进行交互。

导航到 [SendGrid](https://signup.sendgrid.com/) 并使用您的电子邮件地址注册。登录后，使用单一发件人验证来验证您的发件人身份。发件人身份是客户或电子邮件收件人眼中的电子邮件发件人。

通过点击**电子邮件 API** 选项卡，然后点击**集成指南**，获取 API 密钥。选择 **Web API** 设置方法，然后选择 **Node.js** 作为首选语言。应该会打开一个页面，显示如何在 Node 中使用 SendGrid 发送电子邮件的分步指南。

现在我们可以通过给它一个合适的名称来创建一个 API 键。

接下来，在项目目录的根目录下创建一个`.env`文件，并向其中添加以下代码行:

```
# API key from SendGrid
SENDGRID_API_KEY=
# The email address which appears in the from field
SENDER_EMAIL=

```

环境变量`SENDGRID_API_KEY`的值是我们的 SendGrid API 键。`SENDER_EMAIL`的地址是我们希望在客户收到我们的邮件时显示的发件人电子邮件地址。

通过将`.env`文件添加到`.gitignore`来忽略它，这样我们就不会意外地将它推送到 GitHub、GitLab 或 BitBucket 等远程 g it 托管服务；我们不想暴露我们的 API 密钥。

通常，我们需要`dotenv`包来使用 Node 中的环境变量。这里没有必要，因为`react-scripts`在引擎盖下使用了`dotenv`。

在下一节中，我们将添加发送电子邮件的 Netlify 函数。

## 添加发送电子邮件的网络功能

现在，让我们添加一个 Netlify 函数，用于在客户提交表单时自动发送电子邮件回复。我们将在 Netlify 发出`submission-created`事件后发送电子邮件。

事件驱动的网络功能必须与触发它的事件同名。Netlify 发出了几个事件，但是我们只对`submission-created`事件感兴趣，每当表单提交被验证时就会发出这个事件。

如前所述，需要由`submission-created`事件触发的函数必须命名为`submission-created.js`。

运行下面的命令来安装`netlify-cli`。这是一个方便的 npm 包，我们可以通过命令行使用 Netlify:

```
npm i netlify-cli -g

```

成功运行上述命令将全局安装`netlify-cli`。

在项目目录的根目录下创建一个文件夹，并将其命名为`netlify`。在刚刚创建的`netlify`文件夹中创建另一个文件夹，并将其命名为`functions`。

运行下面的命令创建 Netlify 函数样板代码，并在出现提示时选择`submission-created`样板。如果您没有看到`submission-created`选项，请使用上下箭头键浏览列表:

```
netlify functions:create submission-created

```

在成功运行上述命令后，我们应该能够在`netlify/functions`文件夹中看到`submission-created.js`文件。该文件的内容应该是这样的:

![Screenshot of code in the submission-created.js file](img/c15ac84626e59851e9f9d891c3fb6ea6.png)

一个 Netlify 函数文件将总是导出一个带有`event`和`context`参数的函数。但是，请注意，发送电子邮件并不需要`context`参数。

我们可以在`event`对象的`body`字段中访问提交表单的内容。`event`物体的形状如下所示:

```
{
  path: '',
  httpMethod: '',
  queryStringParameters: {},
  multiValueQueryStringParameters: {},
  headers: {},
  multiValueHeaders: {},
  body: '{payload:{data:{}}}',
  isBase64Encoded: false
}

```

对于我们通过 SendGrid 发送电子邮件，我们需要`@sendgrid/mail`包。我们可以像前端应用程序中的任何其他依赖项一样，为 Netlify 函数安装所需的依赖项。当应用程序的构建过程完成时，Netlify 将提取依赖项，并使用我们的函数将它们部署到 AWS。

运行下面的命令来安装`@sendgrid/mail` npm 包，这样我们就可以通过我们的函数与 SendGrid web API 进行交互:

```
npm i @sendgrid/mail

```

成功安装`@sendgrid/mail`后，将下面的代码复制并粘贴到我们上面创建的`submission-created.js`文件中:

```
const sendGridMail = require("@sendgrid/mail");

const handler = async (event) => {
  try {
    const { name, email, message } = JSON.parse(event.body).payload.data;

    console.log(`name: ${name}, email: ${email}, message: ${message}`);

    sendGridMail.setApiKey(process.env.SENDGRID_API_KEY);
    const html = `
      <div> 
         Hi ${name}! <br><br>
         Thanks for getting in touch.
         We have received your message
         and one of our customer care
         representatives will get in
         touch shortly
         <br><br>
         Best <br>
         John Doe
      </div>
    `;
    const mail = {
      from: process.env.SENDER_EMAIL,
      to: email,
      subject: "We have received your message",
      html,
    };
    await sendGridMail.send(mail);
    return {
      statusCode: 200,
      body: JSON.stringify({ message: "Email sent" }),
    };
  } catch (error) {
    return { statusCode: 422, body: String(error) };
  }
};

module.exports = { handler };

```

在上面的代码中，我们需要像任何其他节点包一样的`@sendgrid/mail`包，并从`payload`中析构提交的电子邮件。将`mail`对象传递给`sendGridMail.send`方法，该方法包含 SendGrid 发送电子邮件所需的所有必要信息。

在下一节中，我们将测试刚刚添加的功能。

## 测试应用程序

是时候测试我们的应用程序了。通过在终端上运行以下命令来启动本地开发服务器:

```
netlify dev

```

成功运行上述命令将在端口 8888 上启动开发服务器。

![Screenshot of example app with input fields for name, email, and email message.](img/61b3d6e663a17a4f5a1dfdc4e2fc337c.png)

提交表单后，您应该会看到一封电子邮件发送到您的收件箱。您可以继续使用`netlify-cli`或通过 GitHub、Gitlab 或 Bitbucket 使用持续部署功能将应用程序部署到 Netlify。

我已经在 GitHub 上部署了这个应用的代码库[，在这里你可以克隆存储库并探索平台的更多特性。](https://github.com/nibble0101/netlify-sendGrid-demo-app)

这就是你如何使用网络功能和第三方 SMTP 服务器发送电子邮件！如果您想使用 Netlify 函数发送电子邮件，但不是为了响应`submission-created`事件，您将不得不使用普通函数，尽管没有 Netlify 处理表单的内置特性。

## 结论

前端开发人员使用无服务器函数在 Jamstack 应用程序中提供全栈体验已经变得很容易。如上所述，您可以用一个简单的函数编写您的服务器端代码，并与您的前端应用程序一起部署，然后让 Netlify 之类的平台为您完成剩下的工作。

## [LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。