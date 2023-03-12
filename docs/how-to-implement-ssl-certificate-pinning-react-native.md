# 如何在 React Native 中实现 SSL 证书锁定

> 原文：<https://blog.logrocket.com/how-to-implement-ssl-certificate-pinning-react-native/>

## 介绍

当移动应用程序与服务器通信时，它们使用 SSL 来保护传输的数据免受窃听和篡改。如果您将浏览器导航到受保护的网站，您会在左上角看到一个小锁，表示您的连接受到保护。

![Lock Image Signifying Protected Connection](img/baac54a6624be9260a1f6c257ada78d3.png)

您可以看到受保护的站点有一个您的浏览器认为有效的证书。

仅连接到拥有有效证书的网站，您的系统可以通过使用[信任链](https://www.thesslstore.com/knowledgebase/ssl-support/explaining-the-chain-of-trust/)的概念来确定证书是否可信。

如果服务器证书由可信证书签名，并且该证书又由另一个可信证书签名，则该证书是可信的。这个过程一直持续到到达根证书。

在那里，根证书由可信的证书颁发机构签名，这些可信颁发机构的证书存储在您的操作系统信任存储中。这种相互签名的可信证书链被称为信任链。信任链显示了 SSL 证书如何链接回受信任的证书颁发机构。

现在，试图窃听该流量的黑客(中间人)没有由给定域的根可信机构签名的证书的私钥。因此，他不能用该证书签署数据。这意味着您的系统通常会阻止连接到攻击者主机。

但是，有两种方法可以绕过这一点:

*   如果攻击者在您的系统信任存储中植入证书
*   如果根系统证书遭到破坏—这种情况很少见

SSL 固定是一种缩小可信证书列表的方法，以防止上述攻击场景。使用 SSL 固定，您可以将受信任网站的证书数据存储在即时签名中心，您可以存储证书、公钥或该证书的哈希。因此，我们不仅接受您的信任库信任的任何证书，而且只接受这些证书中的单个证书或有限的子集。

其结果是 SSL 固定提供了一种有效的方法来保护我们的移动应用程序免受中间人攻击。

在本文中，我们将研究如何在 React Native 中实现 SSL 证书锁定的细节。但是在我们深入研究之前，下面是充分利用本文所需的先决条件，以及为什么 SSL 固定可能适合您的项目的一些原因。

## SSL 固定的优势

如果实施正确，SSL 固定将为移动应用程序增加以下优势:

*   极大地限制了暴露给中间人攻击的攻击面
*   针对黑客使用的恶意证书增加了安全性
*   增加了针对证书颁发机构错误、欺诈或受损证书颁发机构的保护
*   在设备中增加了针对无特权恶意软件的安全性
*   降低成本，因为我们可以使用自签名证书
*   增强用户隐私

## SSL 固定的缺点

虽然 SSL 固定为移动应用程序增加了额外的安全性和优势，但在某些情况下，其操作用途并不是最佳的。以下是 SSL 固定的一些缺点:

*   灵活性较低—这是因为不容易更改固定证书
*   与完美的前向保密不同，当锁定的证书私钥被泄露时，SSL 锁定不提供任何保护
*   与 TSL 不同，SSL 固定不保护通过网络传输的数据。因此，SSL 固定在通信期间不提供针对不可信网络节点的机密性和完整性保护
*   不能防止逆向工程。代码签名更适合这里
*   没有保护根或越狱设备

## 先决条件

*   React Native 的基础知识
*   节点 12 或更新版本—最好是 LTS。注意，最好使用 NVM 安装节点，以避免在设置 React 本机开发环境时出现错误
*   Android Studio 的最新版本
*   Java SE 开发工具包(JDK)。React Native 至少需要 Java 8

一旦满足了上述先决条件，按照这里给出的说明[设置 React 本地开发环境](https://reactnative.dev/docs/environment-setup)，我们将在下一节开始。

## 入门指南

在本节中，我们将学习如何在 React 本机应用程序中实现 SSL 固定。

确保您已经设置了 React 本地开发环境。现在克隆这个初学者应用程序，通过运行以下命令开始:

```
git clone https://github.com/lawrenceagles/react-native-android-ssl-pinning-starter 

```

在您喜欢的文本编辑器中打开您的项目，并在 Android 目录中创建一个`local.properties`文件。将以下内容添加到该文件中:

```
sdk.dir = /home/<!-- your pc name here -->/Android/Sdk

```

从这里，您可以通过运行`npx react-native start`来启动 Metro，我们得到:

![Start Metro By Running React Native](img/8722edfeab3aedaea36683de91e797a4.png)

接下来，打开一个新的终端，运行`npx react-native run-android`来运行你的应用。确保连接了设备或模拟器正在运行。

如果一切正常，您应该会看到您的新应用程序在您的 Android 模拟器中运行:

![App Running In Android Emulator](img/2ee2c487cbb75a151a6f7c50b3ac8b27.png)

starter 应用程序由一个简单的样板文件组成，有两个按钮:`fetch data`和`fetch secured data`。点击这些按钮会调用两个功能(如你所料):`fetchData`和`secureFetchData`。目前，这些功能只是记录一个文本到控制台，你可以在地铁站看到。

现在，我们将向该应用程序添加 SSL pin，并使用这些按钮测试功能。

有许多用于在 React 本机应用程序中添加 SSL 固定的包。然而，在本文中，我们将使用 [react-native-ssl-pinning](https://github.com/MaxToyberman/react-native-ssl-pinning) 包。

让我们在下一节安装并使用这个包。

### 实现证书锁定的方法

实现 SSL 证书锁定有两种方式:

*   证书绑定:在这种方法中，我们下载证书并将其与应用程序捆绑在一起。然后，应用程序会在运行时将嵌入的证书与服务器的证书进行比较。这就是我们将在本文中学习的方法
*   公钥锁定:在这种方法中，应用程序在运行时将我们的代码中提供的公钥字符串与证书公钥字符串进行比较

我们将在本文中使用的 [react-native-ssl-pinning](https://github.com/MaxToyberman/react-native-ssl-pinning) 包支持证书锁定和公钥锁定。而要使用公钥锁定，我们只需要将`pkPinning`属性设置为`true`。让我们在下一节安装并使用这个包。

## 实现 SSL 证书锁定

首先，通过运行以下命令安装 react-native-ssl-pinning 包:

```
npm install react-native-ssl-pinning

```

为了正确地实现 SSL 固定，我们需要一个来自与我们通信的服务器的可信证书。我们可以通过使用 [openssl](https://www.openssl.org/) 从服务器获取证书。

我们将与[https://jsonplaceholder.typicode.com](https://jsonplaceholder.typicode.com)合作，因此要获得证书，请打开您的终端并运行以下代码:

```
# Automatically extracts certifcate into a .pem file
openssl s_client -servername jsonplaceholder.typicode.com -connect jsonplaceholder.typicode.com:443 </dev/null | sed -n -e '/-.BEGIN/,/-.END/ p' > mycert.pem  

```

上面的代码将输出`jsonplaceholder`的证书，并保存在一个`.pem`文件— `mycer.pm`中。但是我们需要一个`.cer`文件中的证书，以便将它从`.pem`转换为`.cer`，所以运行下面的代码:

```
openssl x509 -in mycert.pem -outform der -out mycert.cer

```

并将`mycert.cer`文件复制到应用的`/android/app/src/main/assets/`目录中。

要获取无效证书，我们将从[https://expired.badssl.com/](https://expired.badssl.com/)处获取过期证书。

在您的终端中，运行以下代码:

```
openssl s_client -servername expired.badssl.com -connect expired.badssl.com:443 </dev/null | sed -n -e '/-.BEGIN/,/-.END/ p' > expcert.pem

```

接下来，如上所述，通过运行以下命令将`.pem`文件转换为`.cer`:

```
openssl x509 -in expcert.pem -outform der -out expcert.cer

```

之后，将`expcert.cer`文件添加到您的`/android/app/src/main/assets/`目录中。

现在，我们可以通过使用`react-native-ssl-pinning`包重构`fetchData`和`securedFetchData`函数来使用这个证书。

将这个包导入到您的`App.js`文件中，代码行:
`import { fetch } from 'react-native-ssl-pinning';`

用以下代码替换`fetchData`功能:

```
const fetchData = () => {
    fetch("https://jsonplaceholder.typicode.com/posts/1", {
      method: "GET",
      timeoutInterval: 10000,
      sslPinning: {
        certs: ["expcert"]
      }
    })
      .then(response => {
        console.log(JSON.stringify(response.bodyString, null, "\t"))
      })
      .catch(err => {
        console.log(`error: ${err}`)
      });
  }

```

此外，用以下代码替换`securedFetchData`函数:

```
const secureFetchData = () => {
    fetch("https://jsonplaceholder.typicode.com/posts/1", {
        method: "GET",
        timeoutInterval: 10000,
        sslPinning: {
            certs: ["mycert"]
        }
    })
        .then(response => {
            console.log(JSON.stringify(response.bodyString, null, "\t"))
        })
        .catch(err => {
            console.log(`error: ${err}`)
        })
}

```

在上面的代码中，我们更新了`fetchData`和`securedFetchData`函数，使用来自`react-native-ssl-pinning`的`fetch`方法从`jsonplaceholder.typicode.com`获取数据。这个`fetch`方法接受一个`sslPinning`选项，这是一个具有`cert`属性的对象。属性是我们所有证书的数组。

在`fetchData`中我们添加了`expcert`证书。所以不应该建立连接。但是在`securedFetchData`函数中，我们添加了`mycert`证书，这是一个可信的证书，所以应该建立连接。我们应该能够从`jsonplaceholder.typicode.com`获取数据。

为了测试这一点，我们需要通过运行以下命令来重建我们的应用程序:

`npx react-native run-android`

现在点击`Fetch secured data`按钮，查看地铁站。我们得到:

![Checking Metro Terminal For Fetched Data](img/a41d442b1b91e2af1f75eaf3ffbf0a63.png)

我们可以从图像中看到，数据提取成功。

现在点击`Fetch data`按钮，查看地铁站的日志。我们得到这个错误:
`LOG error: java.security.cert.CertPathValidatorException: Trust anchor for certification path not found`。

![Log Error In Metro Terminal](img/b09af0b890d410feccc180ad7018462c.png)

出现此错误是因为我们使用了过期的证书。SSL 证书会随着时间过期，这是出于安全原因。在下一节中，我们将了解更多这方面的内容。

## 语义版本和移动安全

SSL 证书不是永远有效的。它们有一个有效期，这对 SSL 的安全保证很重要。当证书过期时，它将失效，需要续订。

与用户通过浏览器与服务器通信的网络不同，移动应用程序需要安装在用户的手机上。任何功能升级、漏洞修复或安全补丁都需要发布新版本。

开发人员通常使用[语义版本](https://semver.org/)来处理移动应用程序的所有发布版本。

证书过期的移动应用程序将无法连接到服务器，这可能会导致糟糕的用户体验。要解决这个问题，必须更新证书，并使用这个可信证书发布新版本的应用程序。

我们可以通过使用`[react-native-version-check](https://github.com/kimxogus/react-native-version-check)`之类的包来强制用户升级他们的应用程序以获得新版本。

这个包提供了 API，使我们能够检查应用程序的语义版本，从而确定是否需要更新。

## 结论

在构建移动应用程序时，移动安全是无价的，在本文中，我们已经了解了 SSL，这是移动安全的一个非常重要的方面。

如果你对源代码感兴趣，可以获取我在 [GitHub](https://github.com/lawrenceagles/react-native-sslpinning) 上搭建的 app 的完整代码。

注意，在 React Native 中，向 Expo 应用程序添加 SSL 并不容易；这将需要一个复杂的解决方案才能实现。最好的方法是我们在本文中使用的——react-native CLI。

阅读完这篇文章后，我希望您已经准备好在 React 原生应用程序中实现 SSL pinning。

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)