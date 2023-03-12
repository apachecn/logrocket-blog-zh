# 在 Supabase 中使用 Edge 函数:完整指南

> 原文：<https://blog.logrocket.com/using-edge-functions-supabase-complete-guide/>

无服务器计算是软件开发界的一个热门话题，这是有充分理由的！它承诺了一种更高效、更经济的方式来构建和运行可弹性伸缩的应用程序。

Supabase 是一个无服务器的云平台，允许开发者在没有服务器的情况下构建复杂的网络和移动应用。Supabase 最近推出了 Edge Functions ,作为那些寻找简单方法将无服务器功能添加到应用程序中的人的一个选项。Edge 功能是用 TypeScript 编写的，可以通过 Supabase CLI 分发和部署到 29 个地理区域，以覆盖全球用户。

在撰写本文时，Edge 功能仍处于 Supabase 的试验阶段，可能会有突破性的变化。尽管如此，这个特性作为一种在没有大量服务器资源的情况下构建更强大功能的手段，正迅速受到开发人员的欢迎。

但是，边缘函数是如何工作的呢？那么，如何编写无服务器代码呢？在本文中，我们将涵盖所有这一切，甚至更多！

*向前跳转:*

在进一步讨论之前，我们先来探讨一下 Edge 函数是如何工作的，以及 Supabase 是如何管理运行函数代码的平台的。

## 了解 Supabase Edge 功能平台

Supabase Edge 功能在安全 [Deno](https://deno.land) 环境中执行。使用 [Deno Deploy 托管服务](https://deno.com/deploy)，它们在几秒钟内就可以部署到世界各地，无需任何人工干预。这些都由 Supabase 处理，因此您可以完全专注于应用程序的逻辑，而不用担心底层技术。

当 Supabase Edge 功能接收到传入的请求时，该请求首先到达“中继”。该中继充当请求的 API 网关，并对报头中传递的 JWT 进行身份验证。它还提供了一些额外的功能，如日志和速率限制。

在收到对某个函数的请求后，中继检索关于该函数的信息以及一个惟一的标识符，称为部署 ID，并将其传递给 Deno Deploy 平台。该平台安全地执行功能代码，并将响应传回中继器，然后由最终用户接收。

现在，让我们创建并部署一个示例 Edge 函数。

## 入门指南

要开始使用 Supabase Edge 功能，您需要首先安装 Supabase CLI 并设置一个项目。请遵循以下步骤:

1.  安装 Supabase CLI: `npm install -g supabase`
2.  使用命令登录到 CLI:`supabase login`
3.  使用命令:`supabase init`初始化 Supabase 项目
4.  将您的本地项目链接到远程 Supabase 项目，就像这样:`supabase link` `--project-ref <your-project-ref>`

## 创建边缘函数

要使用 Supabase 创建新的 Edge 函数，请在项目中运行以下命令:

```
supabase functions new hello

```

这里，我们正在创建一个名为`hello`的函数。

这将在您的 Supabase 文件夹中创建一个样板函数代码，位于:`/functions/hello/index.ts`。

```
import { serve } from "https://deno.land/[email protected]/http/server.ts";

console.log("Hello from Functions!");

serve(async (req) => {
  const { name } = await req.json();
  const data = {
    message: `Hello ${name}!`,
  };

  return new Response(JSON.stringify(data), {
    headers: { "Content-Type": "application/json" },
  });
});

```

正如您在上面的代码块中所看到的，默认的功能代码非常简单，随时可以部署。`serve`函数创建一个 HTTP 服务器，并开始监听传入的请求。

## 部署边缘功能

要使用 Supabase 部署 Edge 功能，请运行以下命令:

```
supabase functions deploy hello

```

`functions deploy`命令将打包您的函数代码，并将其部署到远程 Supabase 项目。在 Supabase 仪表板中的 Invoke 下，单击带有您的项目名称的 URL(见下文)以了解更多详细信息。

![URL Project Name](img/a207d83917c954604eb2937870d6e71b.png)

您可以从终端复制`curl`请求来测试您的功能。

![Supabase Curl](img/c165404166101b6375a55720973f881c.png)

## 本地运行边缘功能

要在本地开发和运行 Edge 功能，您需要使用 Docker 在本地机器上设置 Supabase。您可以查看本指南以获得在您的系统上设置 Supabase 的帮助。

通过运行以下命令启动 Supabase 项目:

```
supabase start

```

接下来，启动`hello`功能，就像这样:

```
supabase functions serve hello

```

该命令将为该功能启动一个本地服务器，并将侦听本地主机端口 54321。

要调用该函数，从您的终端发出一个`curl`请求:

```
curl --request POST 'http://localhost:54321/functions/v1/hello' \
  --header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZS1kZW1vIiwicm9sZSI6ImFub24ifQ.625_WdcF3KHqz5amU0x2X5WWHP-OEs_4qj0ssLNHzTs' \
  --header 'Content-Type: application/json' \
  --data '{ "name":"Vijit" }'

```

认证的报头中需要有`Bearer`标记。它可以是你的项目的`ANON`键、`SERVICE_ROLE`键或者用户的 JWT 令牌。

## 真实世界用例

Supabase Edge 函数能帮你做的不仅仅是搭建一个简单的 CRUD app 它们还使您能够连接到任何数据库，实时处理数据，甚至构建复杂的工作流。

让我们来看一个实际的用例。我们将创建一个新的 Edge 函数`send-message`，它将[使用](https://www.twilio.com/blog/sending-sms-messages-deno-typescript-twilio-messaging) [Twilio 消息 API](https://www.twilio.com/sms) 发送短信。

要创建`send-message`函数，运行以下命令:

```
supabase functions new send-message

```

您将在`/functions/send-message/index.ts`找到默认功能代码

要使用 Twilio 消息传递 API，您需要 Twilio 帐户 SID 密钥、认证令牌和用于发送 SMS 的虚拟号码。

在项目中创建一个`.env`文件，并将以下值添加到该文件中:

```
// .env
TWILIO_ACCOUNT_SID=
TWILIO_AUTH_TOKEN=
TWILIO_PHONE_NUMBER=

```

***注意，**确保不要在您的代码中暴露这些凭证，或者将`.env` *文件添加到您的 GitHub 历史中。**

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

接下来，定义一个接口来表示 SMS 有效负载:

```
// ./send-message/types/sms.interface.ts 
export interface Sms {
  [index: string]: string;
  From: string;
  To: string;
  Body: string;
}

```

现在，创建一个助手类`TwilioSms`来使用 Twilio 消息传递 API 发送 SMS。类构造函数将接受帐户 SID 和 auth 令牌。

SID 和 auth 令牌一起编码，并将作为 API 请求中的授权头传递。

```
// ./send-message/helpers/twilio-sms.ts 
import * as base64 from "https://denopkg.com/chiefbiiko/base64/mod.ts";
import { Sms } from "../types/sms.interface.ts";

export class TwilioSms {
  private authorizationHeader: string;

  constructor(private accountSID: string, authToken: string) {
    this.authorizationHeader =
      "Basic " +
      base64.fromUint8Array(
        new TextEncoder().encode(accountSID + ":" + authToken)
      );
  }

  async sendSms(payload: Sms): Promise<any> {
    const res = await fetch(
      "https://api.twilio.com/2010-04-01/Accounts/" +
        this.accountSID +
        "/Messages.json",
      {
        method: "POST",
        headers: {
          "Content-Type": "application/x-www-form-urlencoded;charset=UTF-8",
          Authorization: this.authorizationHeader,
        },
        body: new URLSearchParams(payload).toString(),
      }
    );

    const data = await res.json();

    return data;
  }
}

```

在主函数处理程序中，您需要使用`Deno.env.get()`方法加载环境变量，并从助手中导入`TwilioSms`类。

接下来，使用`sendSms()`方法将文本消息发送到请求正文中指定的给定手机号码。

```
// ./send-message/index.ts 
import { serve } from "https://deno.land/[email protected]/http/server.ts";

import { TwilioSms } from "./helpers/twilio-sms.ts";

const accountSid = Deno.env.get("TWILIO_ACCOUNT_SID") || "";
const authToken = Deno.env.get("TWILIO_AUTH_TOKEN") || "";
const fromMobile = Deno.env.get("TWILIO_PHONE_NUMBER") || "";

serve(async (req) => {
  const { textMessage, toMobile } = await req.json();

  const twilioClient = new TwilioSms(accountSid, authToken);

  const message = await twilioClient.sendSms({
    Body: textMessage,
    From: fromMobile,
    To: toMobile,
  });

  console.log({ message });

  const data = {
    isSuccess: false,
  };

  if (message.status === "queued") {
    data.isSuccess = true;
  }

  return new Response(JSON.stringify(data), {
    headers: { "Content-Type": "application/json" },
  });
});

```

要在本地测试该函数，运行`supabase functions serve`命令并在`--env-file`参数中传递`.env`文件路径，以便该函数可以访问环境变量。

```
supabase functions serve send-message --env-file ./supabase/.env

```

现在，使用`curl`命令调用该函数。

```
curl -i --location --request POST 'http://localhost:54321/functions/v1/' \
  --header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZS1kZW1vIiwicm9sZSI6ImFub24ifQ.625_WdcF3KHqz5amU0x2X5WWHP-OEs_4qj0ssLNHzTs' \
  --header 'Content-Type: application/json' \
  --data '{ "textMessage":"Hello Developer!", "toMobile": "+91XXXXXXXXXX" }'

```

要将本地环境变量推送到远程 Supabase 项目，运行`supabase secrets set`命令:

```
supabase secrets set --env-file ./supabase/.env

```

一旦您的功能经过本地测试并准备好部署，运行`supabase functions deploy`命令:

```
supabase functions deploy send-message

```

## 限制

Supabase Edge 功能提供了许多好处，但是对于您可以使用这项新功能做什么，仍然有一些限制。对于数据密集型且执行起来可能很耗时的服务，您应该选择使用 Supabase 的[数据库功能](https://supabase.com/docs/guides/database/functions)。

在撰写本文时，Edge 功能仍处于试验阶段，在未来的更新中会有突破性的变化。边缘功能目前无法建立到端口 25、465 或 587 的出站连接。另外，这个特性只支持 POST 请求，不支持 HTML 响应。最后，在本地开发中，一次只能提供一个边缘功能。

## 结论

Supabase Edge 功能是扩展应用程序功能的绝佳解决方案。通过使用它们，您可以向您的应用程序添加通常需要单独的服务器端应用程序的功能。

在本文中，我们研究了 Supabase Edge 函数如何工作，并研究了如何创建、部署和运行 Edge 函数。我们还浏览了一个真实的使用案例，使用 Edge 功能在没有服务器的情况下发送 SMS。

如果你正在寻找一种方法来为你的应用添加定制功能，而不必自己管理基础设施，Supabase Edge 功能绝对值得一试。我希望这篇文章能帮助你开始使用 Supabase 和 Edge 函数来构建你自己的应用程序。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)