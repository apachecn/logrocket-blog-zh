# 如何使用 SendGrid - LogRocket Blog 用 Node.js 发送邮件

> 原文：<https://blog.logrocket.com/how-to-send-emails-with-node-js-using-sendgrid/>

电子邮件仍然是最广泛使用的书面交流方式之一，尤其是对企业而言。无论是分享营销信息、交易记录还是其他任何东西，发送电子邮件都有多种方式。在本帖中，我们将了解如何使用 Node.js 和 SendGrid 在客户下订单时向他们发送订单确认(事务性)电子邮件。

## 为什么要用 SendGrid 用 Node.js 发邮件？

我们可以用任何编程语言以各种方式发送电子邮件，包括 Node.js。例如，我们可以使用像 [Nodemailer](https://nodemailer.com/) 这样的包，它可以使用多种传输方式，比如 SMTP、Sendmail，甚至 Amazon SES。

然而，随机 SMTP 或 Sendmail 的问题是，这些电子邮件很可能被归类为垃圾邮件，并进入我们客户的垃圾文件夹。我们绝对不希望这种情况发生。

要解决这个问题，最好使用可信的第三方电子邮件 SaaS。可以说，SendGrid 是交易电子邮件领域的行业领导者。他们的[定价](https://sendgrid.com/pricing/)为每月 5 万封邮件 14.95 美元起。

然而，就我们的目的而言，他们有一个免费计划，仍然允许我们每天发送多达 100 封电子邮件。除了更好的交付之外，它们还提供了分析和模板编辑器等基本功能。

### 先决条件

以下是本实践教程的一些先决条件:

1.  您通常知道 JavaScript 和 Node.js 是如何工作的
2.  你知道 JavaScript 中的[异步/等待](https://blog.logrocket.com/evolution-async-programming-javascript/)语法
3.  您知道如何在命令行中使用`node`命令
4.  您知道 Express.js 函数以及中间件如何在 Express.js 中工作

接下来，我们将开始设置 SendGrid 来发送电子邮件。让我们开始吧！

## 如何用 Node.js 和 SendGrid 发送邮件

要使用 SendGrid 从 Node.js 发送电子邮件，我们首先需要注册一个免费的 [SendGrid 帐户](https://signup.sendgrid.com/)。然后我们可以按照他们的循序渐进的电子邮件 API 集成指南如下。

### 在 SendGrid 上注册并生成一个 API 密钥

要注册一个免费帐户，你需要一个有效的电子邮件地址和至少 16 个字符的密码(专业提示:你可以使用[密码生成器](https://passwordsgenerator.net/)):

![Sendgrid API Account Generate Homepage Display](img/8bf7ad1ff4de3c0b9fd6a3e2f9775009.png)

现在我们可以点击**创建账户**。随后，我们需要提供更多关于我们自己的详细信息，以便继续注册 SendGrid:

![SendGrid Create Account User Information Section](img/7a73cc0376e9a9612398ea161cbe552b.png)

注册(并验证我们的电子邮件)后，我们可以通过点击蓝色按钮**创建一个发件人**:

![SendGrid API Account Generate Homepage Display](img/1012d6b64640a50a425ec6d418a8ff4b.png)

在发件人创建表单中，填写如下详细信息(注意，最好不要使用 Gmail 之类的通用电子邮件):

![Sender Creation Form User Details](img/afbef6f3242cafbad895adbb5b1ffdf9.png)

之后，我们可以点击**下的蓝色**开始**按钮，使用我们的 Web API 或 SMTP 中继**进行整合，如下图所示:

![ Integrate Web API Button SMTP Relay Choice](img/d26a38470740f7f164df5c2fbbb7242d.png)

随后，我们将在使用 **Web API** 或 **SMTP 中继**之间做出选择。点击相应的**选择**按钮选择 Web API 选项:

![Web API SMTP Relay Setup Method](img/3a27a7bf5a171dfd0d3cba4e738ae23a.png)

现在选择 **Node.js** 选项，如下图:

![SendGrid Setup Guide Integrate](img/56d083ce0989b78ffb13761fb9ad720c.png)

然后，我们可以生成将在代码中使用的 API 密钥。我已经把它命名为`first-key`，但是你可以随意命名。复制 API 密钥并妥善保管，我们将在下一步中使用它:

![Generate API Key Code](img/b2f3a2952cddbb27516404845a82d671.png)

我们可以转到上面表格的末尾，选中复选框**我已经集成了**上面的代码，然后单击**下一步:验证集成**按钮。

如果您注销并重新登录到 SendGrid，您将被要求启用双因素身份验证。您可以使用您的手机号码和短信进行设置。

完成这些后，我们将看到如何使用 SendGrid API 键快速测试使用 Node.js 发送电子邮件。

### 设置一个简单的 Node.js 脚本来发送电子邮件

要用 npm 设置一个新的 Node.js 应用程序，我们可以运行以下命令:

```
npm init -y

```

`-y`标志将为我们设置所有的默认值。接下来，我们将在`sengrid.env`文件中设置我们创建的 API 密钥，并将其添加到`gitignore`，如下所示:

```
echo "export SENDGRID_API_KEY='YOUR_API_KEY'" > sendgrid.env && echo "sendgrid.env" >> .gitignore && source ./sendgrid.env

```

确保用我们在上一步中生成的 API 键替换上面块中的`YOUR_API_KEY`。

在此之后，我们安装`@sendgrid/mail` npm 包，因为我们只打算发送一封电子邮件，按照本教程的范围:

```
npm i --save @sendgrid/mail

```

上面的命令会将最新版本的`@sendgrid/mail` npm 包添加到我们的`package.json`中，并将其本地安装到我们的`node_modules`文件夹中。

接下来，我们将编写以下代码来测试我们是否能够使用 SendGrid 发送电子邮件:

```
const sendGridMail = require('@sendgrid/mail');
sendGridMail.setApiKey(process.env.SENDGRID_API_KEY);

function getMessage() {
  const body = 'This is a test email using SendGrid from Node.js';
  return {
    to: '[email protected]',
    from: '[email protected]',
    subject: 'Test email with Node.js and SendGrid',
    text: body,
    html: `<strong>${body}</strong>`,
  };
}

async function sendEmail() {
  try {
    await sendGridMail.send(getMessage());
    console.log('Test email sent successfully');
  } catch (error) {
    console.error('Error sending test email');
    console.error(error);
    if (error.response) {
      console.error(error.response.body)
    }
  }
}

(async () => {
  console.log('Sending test email');
  await sendEmail();
})();

```

让我们快速回顾一下上面的代码在做什么。

首先，我们包含了`@sendgrid/mail`包，然后我们从环境变量中设置 API 键。

之后，我们有一个`getMessage`函数，它以文本和 HTML 格式构建包含收件人、发件人、主题和正文的电子邮件消息。

接下来，我们添加了`sendEmail`函数，该函数使用 SendGrid 邮件包中的`send`方法获取消息并发送电子邮件。它被包裹在一个`try...catch`中，以便我们记录任何潜在的错误。如果成功，我们打印一个日志，上面写着`Test email sent successfully`。

这一切都是通过 ES8 async[life](https://developer.mozilla.org/en-US/docs/Glossary/IIFE)(立即调用函数表达式)粘合在一起的，以使其正常工作。我已经将它作为`send-test-email.js`保存在项目的根目录下。

下面是我们如何使用`node send-test-email.js`运行它的截图:

![eSendGrid Sendmail Package Node Run Code](img/abcc79e5ec727145f866e125c5b8ffb1.png)

几秒钟后，我看到了收件箱里的邮件:

![Nodejs SendGrid Test Email Inbox](img/ab52c316c7e1967aa101819726a19bcb.png)

此时，我们可以进入 SendGrid 上的 **[邮件活动](https://app.sendgrid.com/email_activity)** 页面，通过**搜索到邮件地址**。我们将看到电子邮件已发送并打开，如下所示:

![Sendgrid Email Activity Page](img/4bb776d0d4e31192bab73ad782ad69dc.png)

万岁！我们的概念验证正在起作用。您可以在[拉请求](https://github.com/geshan/nodejs-sendgird-example/pull/1/files)中查看到目前为止所做的所有代码更改。现在，我们将把它转换成一个 API，我们可以为任何交易电子邮件调用它。例如，我们将发送一封订单确认电子邮件。

## 创建发送订单确认电子邮件 API

对于一个更实际的真实例子，我们现在将创建一个发送订单确认电子邮件 API。每当用户成功下订单时，就会调用这个 API。假设我们有一个订单保存方法，它做了四件事:

![Order Save Method Four Tasks Diagram](img/1519cbc719453c894aca331358a8be11.png)

我们的 send order confirmation API 调用将在客户的订单成功保存到数据存储之后调用。

为了将我们的测试脚本转换成 API，我们需要一个 web 框架；为此，我们将使用 Express.js。要安装 Express.js，请在项目根目录下运行以下命令:

```
npm i --save express
```

然后我们再添加两个文件:`index.js`，web 服务器；以及`email.js`，发送邮件的服务。`index.js`文件如下:

```
const express = require('express');
const email = require('./email');

const app = express();
const port = 3000 || process.env.PORT;

app.use(express.json());
app.use(express.urlencoded({ extended: false }));

app.get('/', (req, res) => {
  res.json({message: 'alive'});
})

app.post('/api/email/order-confirmation', async (req, res, next) => {
  try {
    res.json(await email.sendOrderConfirmation(req.body));
  } catch (err) {
    next(err);
  }
});

app.use((err, req, res, next) => {
  const statusCode = err.statusCode || 500;
  console.error(err.message, err.stack);
  res.status(statusCode).json({'message': err.message});

  return;
});

app.listen(port, () => {
  console.log(`Example API listening at http://localhost:${port}`)
});

```

让我们仔细检查一下上面的 Express web 服务器的一些主要部分。

首先我们需要 Express 并初始化它。然后，我们使用 JSON 和 URL 编码的中间件来接受订单确认电子邮件的 POST 端点数据。

之后，我们创建一条`/`路线，显示类似于`alive`的消息。接下来，我们有了问题的关键，我们在`/api/email/order-confirmation`定义了一个 POST API。这调用了我们稍后将定义的`email`模块中的`sendOrderConfirmation`方法。

随后，我们看到了一个准系统错误处理器中间件，它可以处理上述路由中抛出的任何错误。最后，我们用`app.listen`启动服务器，并记录服务器已经启动。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

接下来，我们将看看我们的`email.js`文件是如何编写的。我已经使用 [Stripo.email](https://stripo.email/) 免费计划生成模拟订单确认邮件；这是他们提供的模板的一部分。下面是我们的`email.js`文件，它使用 SendGrid 编译并发送电子邮件:

```
const sendGridMail = require('@sendgrid/mail');
sendGridMail.setApiKey(process.env.SENDGRID_API_KEY);

function getOrderConfirmationEmailHtml(customerName, orderNr) {
  return `<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd"><html  xmlns:o="urn:schemas-microsoft-com:office:office" style="width:100%;font-family:Arial, sans-serif;-webkit-text-size-adjust:100%;-ms-text-size-adjust:100%;padding:0;Margin:0"><head><meta charset="UTF-8"><meta content="width=device-width, initial-scale=1" name="viewport"><meta name="x-apple-disable-message-reformatting"><meta http-equiv="X-UA-Compatible" content="IE=edge"><meta content="telephone=no" name="format-detection"><title>New email template 2021-02-21</title> <!--[if (mso 16)]><style type="text/css">     a {text-decoration: none;}     </style><![endif]--> <!--[if gte mso 9]><style>sup { font-size: 100% !important; }</style><![endif]--> <!--[if gte mso 9]><xml> <o:OfficeDocumentSettings> <o:AllowPNG></o:AllowPNG> <o:PixelsPerInch>96</o:PixelsPerInch> </o:OfficeDocumentSettings> </xml><![endif]--><style type="text/css">#outlook a {    padding:0;}.ExternalClass { width:100%;}.ExternalClass,.ExternalClass p,.ExternalClass span,.ExternalClass font,.ExternalClass td,.ExternalClass div {  line-height:100%;}.es-button {  mso-style-priority:100!important;   text-decoration:none!important;}a[x-apple-data-detectors] { color:inherit!important;    text-decoration:none!important; font-size:inherit!important;    font-family:inherit!important;  font-weight:inherit!important;  line-height:inherit!important;}.es-desk-hidden {    display:none;   float:left; overflow:hidden;    width:0;    max-height:0;   line-height:0;  mso-hide:all;}@media only screen and (max-width:600px) {p, ul li, ol li, a { font-size:16px!important; line-height:150%!important } h1 { font-size:30px!important; text-align:center; line-height:120%!important } h2 { font-size:26px!important; text-align:center; line-height:120%!important } h3 { font-size:20px!important; text-align:center; line-height:120%!important } h1 a { font-size:30px!important } h2 a { font-size:26px!important } h3 a { font-size:20px!important } .es-header-body p, .es-header-body ul li, .es-header-body ol li, .es-header-body a { font-size:16px!important } .es-footer-body p, .es-footer-body ul li, .es-footer-body ol li, .es-footer-body a { font-size:16px!important } .es-infoblock p, .es-infoblock ul li, .es-infoblock ol li, .es-infoblock a { font-size:12px!important } *[class="gmail-fix"] { display:none!important } .es-m-txt-c, .es-m-txt-c h1, .es-m-txt-c h2, .es-m-txt-c h3 { text-align:center!important } .es-m-txt-r, .es-m-txt-r h1, .es-m-txt-r h2, .es-m-txt-r h3 { text-align:right!important } .es-m-txt-l, .es-m-txt-l h1, .es-m-txt-l h2, .es-m-txt-l h3 { text-align:left!important } .es-m-txt-r img, .es-m-txt-c img, .es-m-txt-l img { display:inline!important } .es-button-border { display:block!important } .es-btn-fw { border-width:10px 0px!important; text-align:center!important } .es-adaptive table, .es-btn-fw, .es-btn-fw-brdr, .es-left, .es-right { width:100%!important } .es-content table, .es-header table, .es-footer table, .es-content, .es-footer, .es-header { width:100%!important; max-width:600px!important } .es-adapt-td { display:block!important; width:100%!important } .adapt-img { width:100%!important; height:auto!important } .es-m-p0 { padding:0px!important } .es-m-p0r { padding-right:0px!important } .es-m-p0l { padding-left:0px!important } .es-m-p0t { padding-top:0px!important } .es-m-p0b { padding-bottom:0!important } .es-m-p20b { padding-bottom:20px!important } .es-mobile-hidden, .es-hidden { display:none!important } tr.es-desk-hidden, td.es-desk-hidden, table.es-desk-hidden { width:auto!important; overflow:visible!important; float:none!important; max-height:inherit!important; line-height:inherit!important } tr.es-desk-hidden { display:table-row!important } table.es-desk-hidden { display:table!important } td.es-desk-menu-hidden { display:table-cell!important } .es-menu td { width:1%!important } table.es-table-not-adapt, .esd-block-html table { width:auto!important } table.es-social { display:inline-block!important } table.es-social td { display:inline-block!important } a.es-button, button.es-button { font-size:20px!important; display:block!important; border-width:10px 20px 10px 20px!important } }</style></head>
  <body style="width:100%;font-family:Arial, sans-serif;-webkit-text-size-adjust:100%;-ms-text-size-adjust:100%;padding:0;Margin:0"><div class="es-wrapper-color" style="background-color:#555555"> <!--[if gte mso 9]><v:background xmlns:v="urn:schemas-microsoft-com:vml" fill="t"> <v:fill type="tile" color="#555555"></v:fill> </v:background><![endif]--><table class="es-wrapper" width="100%" cellspacing="0" cellpadding="0" style="mso-table-lspace:0pt;mso-table-rspace:0pt;border-collapse:collapse;border-spacing:0px;padding:0;Margin:0;width:100%;height:100%;background-repeat:repeat;background-position:center top"><tr style="border-collapse:collapse"><td valign="top" style="padding:0;Margin:0"><table cellpadding="0" cellspacing="0" class="es-content" align="center" style="mso-table-lspace:0pt;mso-table-rspace:0pt;border-collapse:collapse;border-spacing:0px;table-layout:fixed !important;width:100%"><tr style="border-collapse:collapse"><td align="center" style="padding:0;Margin:0"><table class="es-content-body" style="mso-table-lspace:0pt;mso-table-rspace:0pt;border-collapse:collapse;border-spacing:0px;background-color:transparent;width:600px" cellspacing="0" cellpadding="0" align="center"><tr style="border-collapse:collapse"><td align="left" style="padding:0;Margin:0;padding-bottom:5px;padding-left:10px;padding-right:10px"> <!--[if mso]><table style="width:580px" cellpadding="0" cellspacing="0"><tr><td style="width:280px" valign="top"><![endif]--><table class="es-left" cellspacing="0" cellpadding="0" align="left" style="mso-table-lspace:0pt;mso-table-rspace:0pt;border-collapse:collapse;border-spacing:0px;float:left"><tr style="border-collapse:collapse"><td class="es-m-p0r es-m-p20b" valign="top" align="center" style="padding:0;Margin:0;width:280px"><table width="100%" cellspacing="0" cellpadding="0" role="presentation" style="mso-table-lspace:0pt;mso-table-rspace:0pt;border-collapse:collapse;border-spacing:0px"><tr style="border-collapse:collapse"><td class="es-infoblock" align="left" style="padding:0;Margin:0;line-height:14px;font-size:12px;color:#A0A7AC"><p style="Margin:0;-webkit-text-size-adjust:none;-ms-text-size-adjust:none;mso-line-height-rule:exactly;font-size:12px;font-family:Arial, sans-serif;line-height:14px;color:#A0A7AC">Put your preheader text here</p>
  </td></tr></table></td></tr></table> <!--[if mso]></td><td style="width:20px"></td>
  <td style="width:280px" valign="top"><![endif]--><table cellspacing="0" cellpadding="0" align="right" style="mso-table-lspace:0pt;mso-table-rspace:0pt;border-collapse:collapse;border-spacing:0px"><tr style="border-collapse:collapse"><td align="left" style="padding:0;Margin:0;width:280px"><table width="100%" cellspacing="0" cellpadding="0" role="presentation" style="mso-table-lspace:0pt;mso-table-rspace:0pt;border-collapse:collapse;border-spacing:0px"><tr style="border-collapse:collapse"><td align="right" class="es-infoblock" style="padding:0;Margin:0;line-height:14px;font-size:12px;color:#A0A7AC"><p style="Margin:0;-webkit-text-size-adjust:none;-ms-text-size-adjust:none;mso-line-height-rule:exactly;font-size:12px;font-family:Arial, sans-serif;line-height:14px;color:#A0A7AC"><a href="https://viewstripo.email" target="_blank" class="view" style="-webkit-text-size-adjust:none;-ms-text-size-adjust:none;mso-line-height-rule:exactly;font-family:Arial, sans-serif;font-size:12px;text-decoration:none;color:#A0A7AC;line-height:18px">SEE THIS EMAIL ONLINE</a></p>
  </td></tr></table></td></tr></table> <!--[if mso]></td></tr></table><![endif]--></td></tr></table></td>
  </tr></table><table class="es-content" cellspacing="0" cellpadding="0" align="center" style="mso-table-lspace:0pt;mso-table-rspace:0pt;border-collapse:collapse;border-spacing:0px;table-layout:fixed !important;width:100%"><tr style="border-collapse:collapse"><td align="center" style="padding:0;Margin:0"><table class="es-content-body" cellspacing="0" cellpadding="0" align="center" style="mso-table-lspace:0pt;mso-table-rspace:0pt;border-collapse:collapse;border-spacing:0px;background-color:#F8F8F8;width:600px"><tr style="border-collapse:collapse"><td style="Margin:0;padding-left:10px;padding-right:10px;padding-top:20px;padding-bottom:20px;background-color:#191919" bgcolor="#191919" align="left"><table width="100%" cellspacing="0" cellpadding="0" style="mso-table-lspace:0pt;mso-table-rspace:0pt;border-collapse:collapse;border-spacing:0px"><tr style="border-collapse:collapse"><td valign="top" align="center" style="padding:0;Margin:0;width:580px"><table width="100%" cellspacing="0" cellpadding="0" role="presentation" style="mso-table-lspace:0pt;mso-table-rspace:0pt;border-collapse:collapse;border-spacing:0px"><tr style="border-collapse:collapse"><td align="center" style="padding:0;Margin:0;font-size:0"><a target="_blank" href="https://viewstripo.email/" style="-webkit-text-size-adjust:none;-ms-text-size-adjust:none;mso-line-height-rule:exactly;font-family:Arial, sans-serif;font-size:14px;text-decoration:none;color:#3CA7F1"><img class="adapt-img" src="https://ognkca.stripocdn.email/content/guids/CABINET_fb4f0a16f1a866906d2478dd087a5ccb/images/69401502088531077.png" alt width="105" height="101" style="display:block;border:0;outline:none;text-decoration:none;-ms-interpolation-mode:bicubic"></a></td>
  </tr></table></td></tr></table></td>
  </tr><tr style="border-collapse:collapse"><td style="Margin:0;padding-top:20px;padding-bottom:20px;padding-left:20px;padding-right:20px;background-color:#FFCC99" bgcolor="#ffcc99" align="left"><table width="100%" cellspacing="0" cellpadding="0" style="mso-table-lspace:0pt;mso-table-rspace:0pt;border-collapse:collapse;border-spacing:0px"><tr style="border-collapse:collapse"><td valign="top" align="center" style="padding:0;Margin:0;width:560px"><table width="100%" cellspacing="0" cellpadding="0" role="presentation" style="mso-table-lspace:0pt;mso-table-rspace:0pt;border-collapse:collapse;border-spacing:0px"><tr style="border-collapse:collapse"><td align="center" style="padding:0;Margin:0;padding-top:15px;padding-bottom:15px"><div><h2 style="Margin:0;line-height:29px;mso-line-height-rule:exactly;font-family:Arial, sans-serif;font-size:24px;font-style:normal;font-weight:normal;color:#242424"><span style="font-size:30px"><strong>Your order is confirmed. </strong></span><br></h2>
  </div></td></tr><tr style="border-collapse:collapse"><td align="center" style="padding:0;Margin:0;padding-left:10px"><p style="Margin:0;-webkit-text-size-adjust:none;-ms-text-size-adjust:none;mso-line-height-rule:exactly;font-size:14px;font-family:Arial, sans-serif;line-height:21px;color:#242424">Hi ${customerName}, we've received order № ${orderNr} and are working on it now.<br></p><p style="Margin:0;-webkit-text-size-adjust:none;-ms-text-size-adjust:none;mso-line-height-rule:exactly;font-size:14px;font-family:Arial, sans-serif;line-height:21px;color:#242424">We'll email you an update when we've shipped it.<br></p></td>
  </tr><tr style="border-collapse:collapse"><td align="center" style="Margin:0;padding-left:10px;padding-right:10px;padding-top:15px;padding-bottom:15px"><span class="es-button-border" style="border-style:solid;border-color:#2CB543;background:#191919 none repeat scroll 0% 0%;border-width:0px;display:inline-block;border-radius:20px;width:auto"><a href="https://viewstripo.email/" class="es-button" target="_blank" style="mso-style-priority:100 !important;text-decoration:none;-webkit-text-size-adjust:none;-ms-text-size-adjust:none;mso-line-height-rule:exactly;font-family:'lucida sans unicode', 'lucida grande', sans-serif;font-size:18px;color:#FFFFFF;border-style:solid;border-color:#191919;border-width:10px 35px;display:inline-block;background:#191919 none repeat scroll 0% 0%;border-radius:20px;font-weight:normal;font-style:normal;line-height:22px;width:auto;text-align:center">View your order details</a></span></td></tr></table></td>
  </tr></table></td>
  </tr><tr style="border-collapse:collapse"><td style="Margin:0;padding-top:10px;padding-bottom:10px;padding-left:10px;padding-right:10px;background-color:#F8F8F8" bgcolor="#f8f8f8" align="left"><table width="100%" cellspacing="0" cellpadding="0" style="mso-table-lspace:0pt;mso-table-rspace:0pt;border-collapse:collapse;border-spacing:0px"><tr style="border-collapse:collapse"><td valign="top" align="center" style="padding:0;Margin:0;width:580px"><table width="100%" cellspacing="0" cellpadding="0" role="presentation" style="mso-table-lspace:0pt;mso-table-rspace:0pt;border-collapse:collapse;border-spacing:0px"><tr style="border-collapse:collapse"><td bgcolor="#f8f8f8" align="center" style="Margin:0;padding-left:10px;padding-right:10px;padding-top:20px;padding-bottom:20px;font-size:0"><table width="100%" height="100%" cellspacing="0" cellpadding="0" border="0" role="presentation" style="mso-table-lspace:0pt;mso-table-rspace:0pt;border-collapse:collapse;border-spacing:0px"><tr style="border-collapse:collapse"><td style="padding:0;Margin:0;border-bottom:1px solid #191919;background:#FFFFFF none repeat scroll 0% 0%;height:1px;width:100%;margin:0px"></td>
  </tr></table></td></tr></table></td></tr></table></td></tr></table></td>
  </tr></table><table cellpadding="0" cellspacing="0" class="es-footer" align="center" style="mso-table-lspace:0pt;mso-table-rspace:0pt;border-collapse:collapse;border-spacing:0px;table-layout:fixed !important;width:100%;background-color:transparent;background-repeat:repeat;background-position:center top"><tr style="border-collapse:collapse"><td align="center" style="padding:0;Margin:0"><table class="es-footer-body" cellspacing="0" cellpadding="0" align="center" style="mso-table-lspace:0pt;mso-table-rspace:0pt;border-collapse:collapse;border-spacing:0px;background-color:#242424;width:600px"><tr style="border-collapse:collapse"><td align="left" style="padding:20px;Margin:0"><table width="100%" cellspacing="0" cellpadding="0" style="mso-table-lspace:0pt;mso-table-rspace:0pt;border-collapse:collapse;border-spacing:0px"><tr style="border-collapse:collapse"><td valign="top" align="center" style="padding:0;Margin:0;width:560px"><table width="100%" cellspacing="0" cellpadding="0" style="mso-table-lspace:0pt;mso-table-rspace:0pt;border-collapse:collapse;border-spacing:0px"><tr style="border-collapse:collapse"><td align="center" style="padding:0;Margin:0;display:none"></td>
  </tr></table></td></tr></table></td></tr></table></td>
  </tr></table><table class="es-content" cellspacing="0" cellpadding="0" align="center" style="mso-table-lspace:0pt;mso-table-rspace:0pt;border-collapse:collapse;border-spacing:0px;table-layout:fixed !important;width:100%"><tr style="border-collapse:collapse"><td align="center" style="padding:0;Margin:0"><table class="es-content-body" style="mso-table-lspace:0pt;mso-table-rspace:0pt;border-collapse:collapse;border-spacing:0px;background-color:transparent;width:600px" cellspacing="0" cellpadding="0" align="center"><tr style="border-collapse:collapse"><td align="left" style="Margin:0;padding-left:20px;padding-right:20px;padding-top:30px;padding-bottom:30px"><table width="100%" cellspacing="0" cellpadding="0" style="mso-table-lspace:0pt;mso-table-rspace:0pt;border-collapse:collapse;border-spacing:0px"><tr style="border-collapse:collapse"><td valign="top" align="center" style="padding:0;Margin:0;width:560px"><table width="100%" cellspacing="0" cellpadding="0" style="mso-table-lspace:0pt;mso-table-rspace:0pt;border-collapse:collapse;border-spacing:0px"><tr style="border-collapse:collapse"><td align="center" style="padding:0;Margin:0;display:none"></td>
</tr></table></td></tr></table></td></tr></table></td></tr></table></td></tr></table></div></body></html>`;
}

function getMessage(emailParams) {
  return {
    to: emailParams.toEmail,
    from: '[email protected]',
    subject: 'We have got your order, you will receive it soon',
    text: `Hey ${emailParams.name}, we have received your order ${emailParams.orderNr}. We will ship it soon`,
    html: getOrderConfirmationEmailHtml(emailParams.name, emailParams.orderNr),
  };
}

async function sendOrderConfirmation(emailParams) {
  try {
    await sendGridMail.send(getMessage(emailParams));
    return  { message: `Order confirmation email sent successfully for orderNr: ${emailParams.orderNr}`};
  } catch (error) {
    const message = `Error sending order confirmation email or orderNr: ${emailParams.orderNr}`;
    console.error(message);
    console.error(error);
    if (error.response) {
      console.error(error.response.body)
    }
    return {message};
  }
}

module.exports = {
  sendOrderConfirmation
}

```

让我们更深入地看看`email.js`在做什么。

首先，我们包含了`sendGrid`邮件模块，并为其添加了 API 密钥。之后，我们看到一个很长的函数`getOrderConfirmationEmailHtml`，它有我们订单确认邮件的 HTML 模板。它接受两个变量，`customerName`和`orderNr`，这两个变量动态填充在[中](https://github.com/geshan/nodejs-sendgird-example/pull/2/files#diff-0f372b8522f748720b506fbbf043fec29181794e3de82c071add96c5635c6dfeR14)。

接下来，我们有一个`getMessage`方法，将电子邮件消息与收件人、发件人、主题、文本和 HTML 正文放在一起。HTML 主体是从上面的`getOrderConfirmationEmailHtml`方法拉进来的。

因此，我们有了 async `sendOrderConfirmation`函数，它将所有东西与错误处理结合在一起。这里，我们调用`sendGridMail`模块上的`send`方法，传递我们从`getMessage`函数获得的正确参数。

如果一切顺利，我们在一个对象内部用一个肯定的`message`来回应。如果有任何错误，我们会记录下来，并向调用者返回一条错误消息，在本例中就是路由。

只有`sendOrderConfirmation`在`email.js`文件的末尾从这个模块中暴露出来。

在我们正确设置了这两个文件之后，我们可以用`node index.js`启动服务器，它将显示如下内容:

![Node Indexjs Server Start Display](img/eba815fd1dce04e6b6221c153f54ff8f.png)

现在，我们可以用下面的 cURL 命令测试我们的订单确认电子邮件 API:

```
curl -i -X POST -H 'Accept: application/json' \
    -H 'Content-type: application/json' http://localhost:3000/api/email/order-confirmation \
    --data '{"name":"YourName", "orderNr": "12344", "toEmail": "[email protected]"}'

```

如果 cURL 命令一切顺利，我们将看到如下所示的 200 响应:

![Curl Command Success 200 Response](img/4b32d2ef32ea0ce3efe80c74605aa55a.png)

然后，我们也应该能够在收件箱中看到该电子邮件:

![Order Confirmation Email Inbox](img/660b5417eb9f09ec7c6e08f052dde765.png)

恭喜你！我们的基本订单确认电子邮件正在工作。如上所示，两个参数—客户的姓名和订单号—被动态地放在电子邮件中。Express.js web 服务器这一步的所有代码都可以在这个 [pull 请求](https://github.com/geshan/nodejs-sendgird-example/pull/2/files)中获得。

我知道电子邮件的 HTML 模板有点太长；我们可以用 SendGrid 的[动态模板](https://mc.sendgrid.com/dynamic-templates)来代替。这本[指南](https://sendgrid.com/docs/ui/sending-email/how-to-send-an-email-with-dynamic-transactional-templates/)展示了如何使用它。

## 可伸缩性考虑

根据你一天接到的订单数量，你可能会想扩大你的邮件发送任务。要做到这一点，我们主要有两个选择。

首先是[使用类似 RabbitMQ](https://blog.logrocket.com/understanding-message-queuing-systems-using-rabbitmq/) 的队列系统。每当用户成功下订单时，生产者将向交易所发送消息，该消息将根据配置被放入一个或多个队列中。

消费者会不断地监听队列，并在消息进入队列时对其进行处理。随着消息数量的增长，我们可以通过增加消费者的数量来扩展这种方法。

另一种扩展电子邮件发送任务的低维护方式是使用无服务器 FaaS(功能即服务)——类似于 [AWS Lambda](https://aws.amazon.com/lambda/) 或[Vercel](https://vercel.com/)——来部署我们的迷你电子邮件发送服务。订单放置逻辑可以以异步方式调用这个 API，这样即使调用失败，也仍然可以。

这里的可伸缩性是由 AWS 处理的，因为资源是按请求分配的，所以我们也不需要担心资源。如果规模真的很大，成本因素可能会发挥作用。我将把对这个主题的进一步探索留给您。你甚至可以找到满足你需求的[免费 Node.js 托管](https://geshan.com.np/blog/2021/01/free-nodejs-hosting/)选项。

## 结论

在本分步指南中，我们已经了解了如何使用 SendGrid 通过 Node.js 发送电子邮件。订单确认只是我们可以发送的众多交易电子邮件之一。如果电子邮件系统设置正确并且模板是动态的，那么使用 Node.js 和 SendGrid 发送电子邮件就变得轻而易举了。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.