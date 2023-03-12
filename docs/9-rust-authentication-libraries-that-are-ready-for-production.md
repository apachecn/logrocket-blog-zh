# 9 个 Rust 认证库已经准备好投入生产

> 原文：<https://blog.logrocket.com/9-rust-authentication-libraries-that-are-ready-for-production/>

Rust 是一种低级语言，具有高级人机工程学。它快速可靠，支持异步 I/O 到达稳定的 Rust。因此，Rust 正迅速成为注重性能的网络和 web 应用的首选。

几乎所有的网络应用都需要某种形式的认证，尤其是在一个数据隐私意识日益增强的时代。Rust 拥有越来越多的高质量、可生产的板条箱，可用于认证和授权。

在本指南中，我们将根据以下标准评估九个稳定的生产就绪型身份认证库:

*   流行
*   完全
*   维护
*   支持稳定生锈

我们还将预览一些尚未生产就绪但应该在您的雷达上的板条箱。

让我们开始吧！

## 1.`cookie`

*   生产就绪:是
*   异步支持:否

[`cookie`](https://docs.rs/cookie/0.14.1/cookie/) 是一个用于创建和解析 HTTP cookies 的机箱。它用于管理会话、加密和签署 cookies。

`cookie`是一款生产就绪的板条箱，可下载数千次。它是构建基于会话的身份验证策略的基础。板条箱支持在 cookies 中保存和记录更改。它与 web 框架无关，支持 actix、rocket 和所有其他框架。

## 2.`jsonwebtoken`

*   生产就绪:是
*   异步支持:否

JSON Web 令牌在授权方面非常流行；许多 OAuth 提供商发行 JWT。 [`jsonwebtoken`](https://docs.rs/jsonwebtoken/7.2.0/jsonwebtoken/) 提供验证和创建 JWT 令牌的支持。

板条箱支持所有标准签名算法以及对称和非对称加密算法。它带有标准声明的内置验证，以及强类型和高度可配置的 API。它还完全实现了 RFC 7519 。

jsonwebtoken 是一个高度稳定且被广泛使用的库，有数千次下载。最重要的是，它有一个活跃的维护团队，有巨大的社区支持。

## 3.`oauth2`

*   生产就绪:是
*   异步支持:是

[`oauth2`](https://docs.rs/oauth2/4.0.0-alpha.1/oauth2/index.html) 提供了 OAuth 2 协议的完整实现。它支持异步和同步 I/O、获取访问令牌、验证状态以及获取刷新令牌。

特性包括完全实现 RFC 6749 和强类型。机箱支持状态和`crf_state`验证以及 PKCE 挑战。此外，一个默认支持`reqwest`和`curl`的定制 HTTP 客户端被嵌入到机箱中。

最后，`oauth2`是框架无关的，支持直接使用密码和用户名。

其他 oAuth 提供者的例子包括:

`oauth2`易于使用，提供完整的 OAuth 2 客户端解决方案，包括更新令牌、管理声明、范围和授权。

## 4.`otpauth`

*   生产就绪:是
*   异步支持:否

[`otpauth`](https://messense.github.io/otpauth-rs/otpauth/index.html) 同时支持 [HOTP](https://tools.ietf.org/html/rfc4226) 和 [TOTP](https://tools.ietf.org/html/rfc6238.html) 算法。它用于生成一次性密码(OTP)。它可以生成基于时间的 OTP，并支持更改验证时间。

的 API 易于使用且直观，并且具有 HOTP 和 TOTP 的完整实现。

## 5.`yup-oauth2`

*   生产就绪:是
*   异步支持:是

`yup-oauth2`提供了服务器到服务器的 OAuth 2.0 的实现。它用于实现不同服务的客户端库，比如`google-api-rs`。

板条箱支持服务帐户和已安装的应用程序，并与任何实现 OAuth 2.0 的服务一起工作，以进行服务器到服务器的身份验证。`yup-ouath`通常用于使用[设备流](https://tools.ietf.org/html/rfc8628)在输入能力有限的设备上进行验证。它被用于谷歌云平台、Firebase 等服务，以及其他使用[服务流](https://developers.google.com/identity/protocols/oauth2/service-account)的谷歌服务。它还支持无法使用[安装的应用流程](https://developers.google.com/youtube/v3/live/guides/auth/installed-apps)安全保存认证令牌的应用。

## 6.尽快

*   生产就绪:是
*   异步支持:否

[ASAP](https://crates.io/crates/asap) 是由 Atlassian 创建并维护的认证机制。它支持非标准声明，并以闪电般的速度执行验证和令牌生成。

ASAP 基于`jsonwebtoken`板条箱。它完全支持 ASAP 规范。

## 7.JWKS-客户端

*   生产就绪:是
*   异步支持:是

JWKS-Client 支持使用 JWKS (JSON web keyset)验证 JSON web 令牌。crate 的主要功能是使用 JWKS 对 JWT 令牌进行对称签名验证。

crate 主要用于 Google 和 Firebase OAuth，但也可以很容易地用于任何其他提供商。它获取密钥参数的 URL 并验证令牌。它还支持密钥缓存。

## 8.`openssl`

*   生产就绪:是
*   异步支持:否

这个 [`openssl`](https://docs.rs/openssl/0.10.30/openssl/) 箱子是为 OpenSSL 库进行 Rust 绑定的。它提供了对签名令牌、哈希密码、生成随机身份验证令牌和加密数据的全面支持。

许多身份验证库使用 OpenSSL 对数据进行签名和哈希处理。`jsonwebtoken`使用 OpenSSL 对令牌进行签名。

板条箱支持静态和动态链接。它还支持 OpenSSL 的可用版本或下载所需的版本。

## 9.`pgen`

*   生产就绪:是
*   异步支持:否

[`pgen`](https://github.com/ctsrc/Pgen) 使用 EFF 的[单词表为随机密码](https://www.eff.org/deeplinks/2016/07/new-wordlists-random-passphrases)生成密码。

## 3 个信任认证库，让您时刻保持警惕

下面的库还不稳定，但是有很好的特性，应该引起你的注意。随着 Rust 的成长，它将支持越来越多的标准。

### 1.`boringauth`

*   生产就绪:否
*   异步支持:否

[`boringauth`](https://docs.rs/boringauth/0.9.0/boringauth/) 是任何 app 认证相关需求的一站式解决方案。它支持密码身份验证以及基于 HTOP 和 TOP 的身份验证。它还具有可定制的周期、哈希算法、输出长度和初始时间戳。

`boringauth`可用于 CLI 和桌面应用。它还不完全支持 HTOP 和 TOPT。对通用双因素认证的支持也即将推出。

### 2.`oxide-auth`

*   生产就绪:否
*   异步支持:否

[`oxide-auth`](https://docs.rs/oxide-auth/0.4.5/oxide_auth/) 机箱为服务器提供 OAuth 的实现。它支持许多 web 框架，包括 Actix、Iron、Rocket 和 Rouille。

简单地说，`oxide-auth`使得在服务器上管理 OAuth 令牌变得容易。它还支持处理自定义服务器的令牌。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

虽然它的 API 还不稳定，但`oxide-auth`是一个可靠的库。web 框架的支持框架仍在开发中。

### 3.弗兰克·JWT

*   生产就绪:否
*   异步支持:否

弗兰克 JWT 用于验证和产生 JWT。它可以从 path 中自动读取密钥。

这个板条箱还不能用于生产，因为它不支持对`iss`、`sub`和许多其他声明的验证。

## 摘要

总之，下面是一个综合表格，可以帮助您比较本指南中讨论的 Rust 身份验证库的特性、功能和优缺点。

| 名字 | 生产就绪？ | 类型 | 异步支持？ | 主要用途 | 缺点 | 利益 | 维护？ |
| `cookie` | 是 | 助手 | 不 | 

*   创建基于自定义会话和 cookies 的身份验证
*   将 JWT 保存在加密的 cookies 中
*   跟踪用户

 | 广泛的用户基础 | 支持解析器和创建 cookies

*   是
*   `oauth2`

 | 是 |
| OAuth 支持 | 是 | 获取 OAuth 令牌 | 支持多个 HTTPS 请求客户端(请求、curl、自定义) | 支持任何支持 OAuth 2 的 OAuth 提供程序

*   不支持验证获得的令牌

 | 全面实施 OAuth 2

*   是

 | `oauth-client`

*   不

 | OAuth 支持 |
| 不 | 不支持验证获得的令牌 | 未积极维护 | No | 不 | `oauth-client-async`

*   不
*   OAuth 支持

 | 是 | `oauth-client`的异步版本 |
| 未积极维护 | 相对较少的用户 | 不支持验证获得的令牌 | 不 | `oxide-auth`

*   不

 | OAuth 服务器

*   是
*   基于 OAuth 构建服务器
*   支持各种框架(`oxide-auth-actix`、`oxide-auth-iron`、`oxide-auth-rocket`、`oxide-auth-rouille`)

 | 是 | `inth-oauth2` |
| 不 | OAuth 服务器 | 是 | OAuth 客户端服务器 | 支持 Google、GitHub 和 Imgur 开箱即用

*   对 Slack 和其他提供商提供社区支持的可插拔接口
*   不

 | `yup-oauth2` | 是 | OAuth 服务器 |
| 是 | Google 服务的 OAuth | 令牌缓存 | 没有现成的身份验证提供程序支持 | 

*   对已安装应用程序的支持
*   服务账户
*   由`google-api-rs`使用

 | 是 | 弗兰克·JWT | 不 |
| JWT 助手 | 不 | JWT 创建和验证 | 对称和非对称模拟 | 

*   不支持从 JWKS 获取密钥

 | 支持多种算法

*   是

 | `jsonwebtoken`

*   是
*   JWT 帮手
*   不

 | JWT 创建和验证 |
| 对称和非对称模拟 |  | 不支持从 JWKS 获取密钥 | 巨大的社区支持 | 广泛使用

*   是
*   `otpauth`

 | 是

*   OTP 授权

 | 不

*   OTP 创建和验证

 | 不支持秘密生成 |
| 支持 TOPT 和 HTOP | 是 | `boringauth` | 不 | OTP 授权

*   不
*   OTP 创建和验证

 | 高度可配置

*   支持密码

 | 支持 TOPT 和 HTOP

*   是
*   尽快(As Soon As Possible)

 | 是 |
| 尽快授权 | 是 | ASAP 代币(JWT，带自定义申领) | 超快的 | 由 Atlassian 支持

*   是

 | JWKS-客户端

*   是

 | JWT 认证中间件

*   是

 | 使用 JWKS 的 JWT 验证 |
| 是 | `openssl` | 是 | 密码系统 | 不

*   哈希密码

 | 生成盐

*   OpenSSL 库的绑定
*   是

 | `pgen`

*   是

 | 释义生成器 |
| 不 | Yes | ASAP auth | Yes | 是 | [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性 | 调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

*   .
*   LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

 | 现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。 |
| JWKS-Client | Yes | JWT auth middleware | Yes |  |  |  | Yes |
| `openssl` | Yes | Cryptography | No |  |  |  | Yes |
| `pgen` | Yes | Paraphrases generator | No |  |  |  | Yes |

## [LogRocket](https://lp.logrocket.com/blg/rust-signup): Full visibility into web frontends for Rust apps

Debugging Rust applications can be difficult, especially when users experience issues that are difficult to reproduce. If you’re interested in monitoring and tracking performance of your Rust apps, automatically surfacing errors, and tracking slow network requests and load time,

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

[LogRocket](https://lp.logrocket.com/blg/rust-signup) is like a DVR for web and mobile apps, recording literally everything that happens on your Rust app. Instead of guessing why problems happen, you can aggregate and report on what state your application was in when an issue occurred. LogRocket also monitors your app’s performance, reporting metrics like client CPU load, client memory usage, and more.

Modernize how you debug your Rust apps — [start monitoring for free](https://lp.logrocket.com/blg/rust-signup).