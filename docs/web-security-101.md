# 网络安全 101 -日志火箭博客

> 原文：<https://blog.logrocket.com/web-security-101/>

网络安全是一个巨大的话题。为了保证你的用户(和你自己)的安全，你需要注意的事情可能会很多。尽管如此，还是有一些概念和建议可以解决最重要的问题，并且易于学习和理解。让我们来看看。

## 协议级别的安全性

### 使用 HTTPs

根据 Firefox 的数据，大约 80%的页面加载使用 HTTPs，所以这已经是一个很好的惯例了。使用 HTTPs 可以让您的用户拥有安全性和隐私。它会加密他们和你的服务器之间的信息，包括密码和其他敏感信息，如电子邮件，物理地址等。几年前，在你的服务器上设置 HTTPs 可能很难，而且很贵，但是现在多亏了[让我们加密](https://letsencrypt.org/)，这变得容易多了，而且免费。

### 明智地使用 HTTP 头

浏览器有能力启用一些安全机制，但前提是你通过 HTTP 头告诉它们这样做。例如，您可以告诉浏览器禁止使用`X-Frame-Options`头在 iframe 中加载您的网站。这将防止您的用户成为点击劫持攻击的目标。

在 helmetjs 网站上可以找到许多这样的标题和它们的作用。Helmet.js 是一个优秀且易于使用的库，它允许你轻松地为 [express](https://expressjs.com/) 应用启用这些安全机制。

这些头大部分都很容易使用，然后我们有一个`Content-Security-Policy`头，有点复杂，默认情况下 Helmet.js 不启用。通过这个头，你可以配置哪些 URL 允许或不允许加载 JavaScript、CSS、图像等。这个头的想法是减轻任何代码注入攻击。例如，如果攻击者知道如何在您的网站中注入 JavaScript，他们可能会从您的用户那里收集一些信息，并通过 AJAX 发送到他们控制的服务器。但是，如果您正确设置了内容安全策略头(CSP ),即使他们可以注入 JavaScript，他们也无法将窃取的信息发送到他们的服务器。

### 不要通过 HTTP 状态代码泄露信息

如果你在用户提供的 URL 中使用标识符(例如[http://example.com/my-super-secret-project-name](http://example.com/my-super-secret-project-name))，那么在实现授权时，如果资源存在但用户没有访问权，你就不应该返回 403。如果您这样做，就等于暗示攻击者该资源是存在的。在这种情况下，您应该返回 404，这样攻击者就不知道资源是否存在，他们只是没有访问它的权限，或者它根本不存在。

## 证明

在我看来，认证是 web 安全中最复杂的话题。你可以写一整本书，但仍然不能涵盖所有内容。然而，有几个方面是基本的，实现起来并不太难。

### 如何存储用户密码

当然，不要以纯文本形式存储密码。您需要以安全的方式存储一些东西，以便能够在用户登录时验证他们的密码。您不一定需要存储密码本身，而是存储一些允许您与用户在登录表单中输入的内容相匹配的内容。有一个很好的加密原语叫做 hash，它允许你这么做。

哈希函数接收纯文本，并输出一个通常存储为十六进制字符的值。问题是从输出中计算纯文本是相当困难的。因此，即使有人能够窃取您的数据库，他们也很难从哈希中计算出密码。但是，如何使用散列函数呢？很简单！当用户第一次输入密码(或想要更改密码)时，您存储`hash(users_input)`，当他们登录时，您将存储的值与他们提供的密码`hash(password) == stored_value`进行比较。但是，你最好使用一个定时安全比较，比如 [crypto.timingSafeEqual](https://nodejs.org/api/crypto.html#crypto_crypto_timingsafeequal_a_b) 来避免定时攻击。

这是一个好的开始，但是还有几件事情需要记住。首先，您应该给密码加盐，因为普通的散列函数对于相同的输入将返回相同的输出。这意味着您可以用最常见的密码生成一个散列列表，如`hash('1234')`、`hash('love')`，并将其与数据库中存储的内容进行比较。如果你是一个拥有数据库的攻击者，这不会给你所有人的密码，但会给你很多密码！

这叫做[彩虹桌](https://en.wikipedia.org/wiki/Rainbow_table)。为了防止这种情况，您可以生成一个随机数(称为 salt ),以明文形式存储在密码哈希附近，然后用`hash(salt + password)`计算哈希。

还有什么？嗯，你也应该选择一个慢哈希函数或者多次哈希，因为有些哈希函数非常快，这是一个安全问题。为什么？因为如果攻击者真的对某人的密码/访问权感兴趣，他们可能会尝试数千或数百万个密码，看看他们能否破解散列。在这种情况下，如果散列函数很快，他们的工作就简单多了。然而，如果散列函数很慢(例如 300 毫秒对 10 毫秒)，你就让事情变得更慢。想象一下慢 30 倍可能意味着要花 30 年而不是 1 年才能破解。

我应该使用什么散列函数？好的，好问题。SHA 和 MD5 等基本函数不适合哈希密码。对于散列密码，您更喜欢使用 bcrypt、 [scrypt](https://nodejs.org/api/crypto.html#crypto_crypto_scrypt_password_salt_keylen_options_callback) 或 [pbkdf2](https://nodejs.org/api/crypto.html#crypto_crypto_pbkdf2_password_salt_iterations_keylen_digest_callback) 。

### 如何重置用户密码

用户往往会忘记他们的密码，所以你需要一种机制，允许他们以某种方式识别自己，并能够设置一个新的密码。这可能很棘手，因为根据您的操作方式，您可能会引入安全漏洞。通常，你会发送一封电子邮件到他们的电子邮件地址，提供一个重置链接。此链接应该过期，并且应该具有一定的随机性，这样攻击者就无法建立重置链接。这是需要记住的两件基本事情。然而，还有许多其他的事情你可能想考虑进去，为此，我推荐[这个指南](https://www.troyhunt.com/everything-you-ever-wanted-to-know/)。

### 延迟错误的凭据响应

如果您在操作系统的登录屏幕中输入了无效的凭据，您可能会意识到需要一点时间才能再次启用登录表单。这是为什么呢？很简单，出于同样的原因，我们希望我们的散列函数很慢，我们希望通过做得慢一点来减轻暴力攻击。速度太慢，对用户来说没什么大不了的，但对攻击者来说，这是一个大问题。

然而，这只能阻止不并行处理请求的简单暴力攻击。为了获得额外的保护，您应该按 IP 对登录尝试进行速率限制，如果您想要更加安全，为了避免来自不同 IP 的对同一用户的暴力攻击，您应该按用户帐户进行速率限制。

很复杂吧。是的，就像我说的，你可以写一整本书专门讨论这个话题。然而，一切都取决于你所掌握的信息的价值。

### 无通行证通行

如你所见，密码是有问题的。即使你做了所有正确的事情(比如正确地计算和存储哈希值，尽可能安全地执行重置功能，等等)，你也无法阻止人们在许多服务中重复使用相同的密码，或者选择一个他们身边的人可能会猜到的弱密码。有其他选择吗？嗯，有，这里有几个:

*   **使用登录链接**——一些应用程序(如 Slack)允许你“发送一个神奇的链接”，让你访问该服务，而不是输入你的密码。这就像一个重置链接，但登录
*   **使用第三方服务实现认证/授权**——有服务(比如 [Auth0](https://auth0.com/) )打理一切(包括 2FA！实现起来相当复杂)并且您只需要使用他们的脚本和钩子来开始认证用户
*   使用 Twitter、脸书、GitHub 等第三方提供商——使用这种方法，你的控制力不如前面列出的方法，而且不是所有用户都有这些服务的账户，所以你可能会留下一些用户，但这是另一种选择，通常很容易实现

## 应用层的安全性

### 跨站点请求伪造

这是最常见的安全漏洞之一，修复起来并不困难。让我们看看它是如何工作的。实现会话管理的最常见方式是使用 cookies。一旦用户通过身份验证，您就可以设置一个 cookie，浏览器会接收到该 cookie，并在每个请求中自动将其发送到服务器。这很棒也很简单。然而，让我们想想这个。攻击者创建了一个隐藏有

现在攻击者只需要向受害者发送一个包含

如何才能避免这种情况？解决方案是生成一个令牌，并将这个令牌放在一个新的 cookie 和表单中的一个隐藏字段中。然后，当提交表单时，后端将检查 cookie 中的令牌是否等于表单中的令牌。攻击者看不到 cookies，因此无法用有效的 CSRF 令牌创建表单。

如果您正在使用 express，您可以使用将生成令牌的 [csurf](https://github.com/expressjs/csurf) 包，将它们放在 cookies 中并验证它们。

## SQL 注入

这可能是您可能遇到的最危险的安全漏洞，它包括修改输入参数来操纵应用程序代码中编写得很差的查询。例如，如果您的代码中有:

```
query = "SELECT * FROM users WHERE login = '" + input_login + "';"
```

攻击者可以发送恶意的`input_login`参数来改变 SQL 查询的意图，甚至包括由`;`分隔的多个句子。通过这种机制，攻击者可以绕过用户身份验证，甚至删除数据库中的记录。

避免这个问题的主要机制是对输入参数进行转义。任何好的 SQL 库都应该有办法实现这一点。例如， [pg](https://www.npmjs.com/package/pg) 库允许您这样做:

```
const text = 'INSERT INTO users(name, email) VALUES($1, $2) RETURNING *'
const values = ['brianc', '[email protected]']
const response = await client.query(text, values)

```

您使用占位符($1，$2)而不是手动插入值，库将使用值数组的转义版本替换这些占位符。

为了确保你永远不会忘记使用占位符，你可以设置一个 linter 来捕捉手动插入并给你一个错误。

### 跨站点脚本(XSS)

这是第三大安全漏洞。当 web 应用程序没有清理和/或转义值的输出时，就会发生这种情况。例如，如果您的应用程序允许用户相互发送消息，并且您在呈现您的站点时没有转义这些消息，如果用户在这些消息上插入 HTML，浏览器将直接呈现和评估 HTML，从而允许攻击者在其上插入 JavaScript。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

所以需要对输出进行转义。比如在 [EJS](https://ejs.co/) 你会做:

```
<div><%= message %></div>
```

如果`message`包含`<script>…</script>`，模板引擎会将其转义到`<script>…</script>`，浏览器不会评估脚本内容。

如果你想允许用户在他们的内容中使用一些 HTML，但是你想避免 XSS 攻击，那么你应该首先清理 HTML，只允许使用一些标签和属性。对于 JavaScript，你有这个[杀毒器](https://github.com/apostrophecms/sanitize-html)。

## 小心外部链接

有一种非常简单的攻击也非常容易避免，这就是我在这里提到它的原因。如果您的网站包含指向外部网站的链接，因为您将它们放在那里，或者因为用户可以在“信息”或他们的个人资料中或任何地方留下链接，您可能正在使用`<a target="_blank">`在新窗口或标签中打开这些链接。这很好，但这是一个潜在的安全问题，因为目标网站可以通过使用`window.opener`访问原始选项卡。解决方法很简单，只需为`rel`属性使用这些值:

```
<a href=”...” target=”_blank” rel=”noopener noreferrer”>Malicious link</a>
```

您应该对任何带有`target="_blank"`的链接都这样做，即使您知道您链接的网站没有恶意，因为它可能已经受到攻击，并且可能包含恶意的 JavaScript 代码。

## 分析你的网站

现在也有一些工具可以让你很容易地发现问题，包括安全问题。其中一个就是 [webhint](https://webhint.io/scanner/) 。它有一些捕捉问题的规则，比如糟糕的 HTTP 头、易受攻击的外部链接等。

如果你有兴趣深入研究这些主题，还有更高级的工具，如 [OWASP ZAP](https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project) 。

## 结论

正如我所说的，web 安全可能是压倒性的，但是我希望这篇文章让您了解最常见的攻击以及如何避免或减轻它们。让我们回顾一下最重要的事情:

*   使用 HTTPs
*   使用 HTTP 头来减少一些攻击
*   正确散列和重置密码，或者不设置密码
*   使用 CSRF 代币
*   执行 SQL 查询时转义输入参数
*   清理和/或转义 HTML 模板中的值
*   分析你的网站！

使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

## [LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)

Focus on the bugs that matter — [try LogRocket today](https://lp.logrocket.com/blg/signup-issue-free).