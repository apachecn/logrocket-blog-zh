# 全栈 web 开发人员的安全性:第 3 部分

> 原文：<https://blog.logrocket.com/security-for-fullstack-web-developers-part-3-aaf81da57acb/>

![](img/381c550d7b7bc919ab36d5a93b8868fd.png)

在这篇文章中，我想探讨一下 web 应用程序安全中最危险的元素——那些涉及用户和会话的元素。从后端的角度来看，用户和身份验证是我们的 web 应用程序的两个最深刻的属性，但我想从前端的角度来看这些属性。尽管处理像用户凭证安全这样的问题非常重要，但我们往往会忽略前端用户和会话安全问题的重要性，如 cookie 盗窃和跨站点请求伪造(CSRF)。

让我们首先讨论它到底是什么，以及我认为我们如何在现代 web 应用程序中完全减轻它。

### 跨站点请求伪造(CSRF)

跨站点请求伪造一直处于 web 应用程序安全的最前沿，比我们任何人想起来的都要长。它是如何工作的？本质上，恶意代理在用户登录和授权时从一个应用程序向另一个应用程序发送(伪造的)请求。恶意代理进入**并且**改变被请求应用上的受限动作，被请求应用完全相信这些改变是合法的。

让我展示给你看。

想象一下，如果你愿意，我是一个滥用安全的恶棍。想象一下，我碰巧知道 Twitter 没有 CSRF 保护。(是的，这仅仅是一个练习。不要有任何想法。)我还意识到，大多数访问我的网络应用的人可能会让他们的 Twitter 保持登录状态。这意味着他们的浏览器中存储了一个 cookie。

在我的网络应用上，我可以嵌入这样的东西:

```
<form action="https://twitter.com/tweet" method="POST" id="sendTweet">
<input type="hidden" name="tweet" value="Hey!  Check out my awesome spam site - spam.com">
```

当浏览器加载我的 web 应用程序时，它也会加载此表单(完全不可见。然后，我还会嵌入一小段 JS 来发布表单，而您永远不会知道:

```
document.getElementById("sendTweet").submit();
```

酷毙了。我刚刚用你的账号发了一条微博，根本不需要知道你的用户名和密码。你存储在浏览器中的 cookie 允许我的应用程序发送一个伪造的请求，假装是你——如果 Twitter 没有 CSRF 缓解，它也会成功的！

也许更好的方法是将它与您自己的 web 应用程序联系起来，使用这样的场景，像我们大多数人一样，您可能有一个为您自己的用户设置的路线来改变他们的用户配置文件。`/user/profile` 可能是最常见的例子，考虑到大多数滥用安全的恶棍都知道这一点(包括我自己)，向您的 web 应用程序发送 POST 请求，用新的电子邮件地址更新用户的个人资料是微不足道的。

虽然这似乎不是一个明显的问题，但是一旦用户的电子邮件地址被更改，我就可以为该用户帐户发出密码重置。当然，新的密码会发送到我在用户资料中留下的任何电子邮件地址。此时，用户帐户已完全受损。任何敏感数据现在都在我手里，而且，为了避免我们忘记，我是一个恶棍。可怕的东西。

多年来，我们一直试图通过检查 HTTP 头(如`Origin` 和`Referer`)来解决 CSRF 请求。虽然这些已经提供了相当强大的保护几年，现在有一个简单的指令，一旦应用，将完全减轻 CSRF 攻击。

进入[***same site***cookie 指令](https://tools.ietf.org/html/draft-west-first-party-cookies-07)。SameSite 相对较新，仍然鲜为人知。实质上，一旦应用了 *SameSite* 指令，它将告诉浏览器**当外部(跨站点)URL 发出请求时，永远不要**发送 cookie。我们可以通过如下方式修改我们的 cookies 来应用该指令:

```
Set-Cookie: sess=sessionid123; path=/; **SameSite**
```

真的就这么简单。我不会建议现在就删除你现有的 CSRF 保护，但我绝对会建议在你的 web 应用程序中包含该指令。

[![](img/94b3e0f84b30cb0d321f60471481ab64.png)](https://logrocket.com/signup/)

### 饼干

正如我在介绍中提到的，我在本系列中的第一篇文章谈到了几个 cookie 指令——即`HTTPOnly` 和`Secure `。众所周知，cookies 是我们 web 应用程序的一个重要特性，它携带的数据主要涉及我们的用户会话。虽然简单地执行上述指令就足以保护您的 cookie，并防止攻击，但我们实际上可以将 cookie 的安全性提高一步。

*Cookie 前缀*是一种相对未被充分利用的技术，我们可以利用它来确保 cookie *是*安全的:

*`***__Secure***` ***前缀***–如果 cookie 的名称以“__Secure”开头，则 cookie 必须是:*

 **   设置一个“*安全*属性
*   从其方案被用户
    代理视为安全的 URL 设置

当从任何来源设置时，下列 cookie 将被拒绝，因为没有设置“安全”标志:

```
Set-Cookie: __Secure-sess=12345; Domain=myapp.com
```

如果从安全来源(如`https://` )设置，则以下内容将被接受，否则将被拒绝:

```
Set-Cookie: __Secure-sess=12345; Secure; Domain=myapp.com
```

除了`__Secure` 前缀，我们还有`__Host` 前缀:

*`***__Host***` ***前缀***–如果 cookie 的名称以“__Host”开头，则 cookie 必须是:*

 **   用“安全”属性设置
*   从 URI 设置，其“方案”被用户代理认为是“安全的”
*   仅发送给设置 cookie 的主机。也就是说，从“https://example.com”*设置的名为“__Host-cookie1”的 cookie 不能包含“域”属性(因此将只发送到“example . com”，而不是“subdomain.example.com”)*
*   发送给每个主机请求。也就是说，名为“__Host-cookie1”的 cookie 必须包含一个值为“/”的“Path”属性

以下 cookies 将始终被拒绝:

```
Set-Cookie: __Host-sess=12345
Set-Cookie: __Host-sess=12345; Secure
Set-Cookie: __Host-sess=12345; Domain=example.com
Set-Cookie: __Host-sess=12345; Domain=example.com; Path=/
Set-Cookie: __Host-sess=12345; Secure; Domain=example.com; Path=/
```

如果从安全来源(如`[https:// ](https://)`)设置，则以下内容将被接受，否则将被拒绝:

```
Set-Cookie: __Host-sess=12345; Secure; Path=/
```

通过设置这些前缀，任何兼容的浏览器都会强制使用它们。

现在，如果我们包括我的第一篇文章中的提示，以及上面的提示，我们可以使 ***最安全的 cookie 成为可能的*** :

```
Set-Cookie: __Host-sess=id123; path=/; Secure; HttpOnly; SameSite
```

在这个最安全的 cookie 中，我们使用了`__Host`前缀，这意味着必须设置`Secure`属性，并且必须由安全主机提供服务。没有设置`Domain`属性，并且`Path`是`/`。我们设置了`HttpOnly`用于 XSS 保护，并启用*同一个站点*来防止 CSRF。当然，对于很多人来说，这不会是最好或最实用的解决方案，但理论上来说，它是我们可以从 web 应用程序设置的最安全的 cookie。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)**