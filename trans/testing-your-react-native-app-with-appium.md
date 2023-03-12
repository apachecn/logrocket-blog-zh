# 用 Appium - LogRocket 博客测试你的 React 原生应用

> 原文：<https://blog.logrocket.com/testing-your-react-native-app-with-appium/>

在构建应用程序时，软件测试至关重要。根据信息和软件质量联盟最近的一份报告，到 2020 年，糟糕的软件质量给美国所有部门的组织造成了超过 2.08 万亿美元的损失。

作为一名软件开发人员，构建一个可靠的、经过良好测试的应用程序可以提高用户和开发人员的满意度，从而使您脱颖而出。

所以，让我们来学习如何用 [Appium](http://appium.io/) 测试 [React 原生](https://reactnative.dev/)移动应用。

## 先决条件

*   您应该已经安装了 [Node.js](http://nodejs.org)
*   使用 React Native 开发移动应用程序的基本知识将会有所帮助

## 什么是 Appium？

Appium 是一个跨平台的自动化测试工具，用于本地、混合和移动 web 应用程序。它支持 iOS、Android 和 Windows UI 测试。

你会问，它是如何工作的？

Appium 有一个客户端-服务器架构。Appium 客户端向 Appium 服务器(Node.js HTTP 服务器)发送请求，然后 Appium 服务器向执行操作的设备发送请求。然后，服务器将结果返回给客户机，让您知道测试的状态。

![Appium Architecture](img/27b88e0f7af57ff22e0eff3c5f17ab72.png)

[Appium architecture](https://bestitcourseblog.wordpress.com/2017/07/31/appium-for-mobile-automation/)

## 设置 React 本机应用程序

让我们设置一个带有登录屏幕的基本 React 本地应用程序，并测试一些 UI 元素。

我们将使用[Expo](http://expo.io/)命令行工具来构建和运行我们将创建的示例 React 本地应用程序。

### 装置

运行以下命令在您的计算机上安装 Expo CLI:

```
npm install --global expo-cli

```

接下来，运行这个命令为 React 本机应用程序创建一个起点:

```
expo init my-app # my-app is your app's name. So, you can change it to whatever name you want.

```

当您运行上面的命令时，会提示您选择一些样板模板选项。例如:

```
? Choose a template: › - Use arrow-keys. Return to submit.
    ----- Managed workflow -----
    blank                 a minimal app as clean as an empty canvas
    blank (TypeScript)    same as blank but with TypeScript configuration
    tabs (TypeScript)     several example screens and tabs using react-navigation and TypeScript
    ----- Bare workflow -----
❯   minimal               bare and minimal, just the essentials to get you started
    minimal (TypeScript)  same as minimal but with TypeScript configuration

```

对于本教程，使用箭头键选择最小安装选项。然后等待安装完成。这大约需要一分钟或更长时间，取决于您的互联网连接强度。

完成后，运行`cd my-app`。现在你的应用已经准备好了。打开`App.js`文件，我们可以修改它。为登录页面添加以下代码。

```
import React, { useState } from 'react';
import { StyleSheet, Text, View, TextInput, TouchableOpacity } from 'react-native';
const App = () => {
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");
  return (
    <View style={styles.container}>
      <Text style={styles.logo}>Login</Text>
      <View style={styles.inputView} >
        <TextInput
          style={styles.inputText}
          placeholder="Email..."
          placeholderTextColor="#003f5c"
          onChangeText={text => setEmail(text)} />
      </View>
      <View style={styles.inputView} >
        <TextInput
          secureTextEntry
          style={styles.inputText}
          placeholder="Password..."
          placeholderTextColor="#003f5c"
          onChangeText={text => setPassword(text)} />
      </View>
      <TouchableOpacity>
        <Text style={styles.forgot}>Forgot Password?</Text>
      </TouchableOpacity>
      <TouchableOpacity style={styles.loginBtn}>
        <Text style={styles.loginText}>LOGIN</Text>
      </TouchableOpacity>
      <TouchableOpacity>
        <Text style={styles.loginText}>Signup</Text>
      </TouchableOpacity>

    </View>
  );
}
const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#003f5c',
    alignItems: 'center',
    justifyContent: 'center',
  },
  logo: {
    fontWeight: "bold",
    fontSize: 50,
    color: "#3CB371",
    marginBottom: 40
  },
  inputView: {
    width: "80%",
    backgroundColor: "#465881",
    borderRadius: 25,
    height: 50,
    marginBottom: 20,
    justifyContent: "center",
    padding: 20
  },
  inputText: {
    height: 50,
    color: "white"
  },
  forgot: {
    color: "white",
    fontSize: 11
  },
  loginBtn: {
    width: "80%",
    backgroundColor: "#3CB371",
    borderRadius: 25,
    height: 50,
    alignItems: "center",
    justifyContent: "center",
    marginTop: 40,
    marginBottom: 10
  },
  loginText: {
    color: "white"
  }
});
export default App;

```

运行`expo start`并选择您想要运行应用的设备。我将使用 [iOS 11 模拟器](https://www.browserstack.com/test-on-ios-simulator)(你可以使用你的 Android 或 iOS 手机)。如果运行该程序，您应该会看到以下登录页面:

![Login Page](img/8573c9612b1fdc1b1a4ab8934b75920b.png)

现在 app 设置好了，但是有一些重要的地方需要注意。因为 Appium 的元素查找算法与 UI 可访问性层一起工作，所以您需要添加一个 UI 可访问性标签，而不是通过 Appium 的 ID 访问 UI 元素来测试它们。因此，我们将在现有组件中添加这一层，如下所示:

```
        <TextInput
          style={styles.inputText}
          placeholder="Email..."
          placeholderTextColor="#003f5c"
          accessibilityLabel="email"
          onChangeText={text => setEmail(text)} />
      </View>

```

太好了！现在让我们设置 Appium 并运行我们的测试。

## 在 React Native 中安装应用程序

安装 Appium 服务器有两种方法。可以下载安装 [Appium desktop](https://github.com/appium/appium-desktop) 或者用 npm 安装。

如果您打算通过 npm 安装它，那么运行`npm install -g appium`，这是我们将在本教程中使用的模式。

之后，安装 [WD.js](https://www.npmjs.com/package/wd) web 驱动程序，我们将使用它与 Appium 服务器通信。

```
npm install wd

```

为了确保一切看起来都很好，你可以用 npm 安装 [appium-doctor](https://www.npmjs.com/package/appium-doctor) 。

```
npm install appium-doctor -g

```

然后在您的终端上运行它:

```
appium-doctor -h

```

接下来，创建一个测试文件，并向其中添加以下代码:

```
import wd from 'wd';

jasmine.DEFAULT_TIMEOUT_INTERVAL = 600000;
const PORT = 4723;

const config = {
    platformName: "iOS",
    platformVersion: "14.4",
    deviceName: "iPhone 11",
    app: "path/to/your.apk or yourapp.ipa",
    automationName: "XCUITest",// UiAutomator2, Espresso, or UiAutomator1 for Android
};

const driver = wd.promiseChainRemote('localhost', PORT);

beforeAll(async () => {
    await driver.init(config);
})

test('Test Accessibilty Id', async () => {
    expect(await driver.hasElementByAccessibilityId('email')).toBe(true);
    expect(await driver.hasElementByAccessibilityId('password')).toBe(true);
});

```

配置部分很重要，所以请确保为您的设备输入了正确的选项(iOS 设备配置)。

```
const config = {
    platformName: "iOS",
    platformVersion: "14.4",
    deviceName: "iPhone 11",
    app: "path/to/your.apk or yourapp.ipa",
    automationName: "XCUITest",// UiAutomator2, Espresso, or UiAutomator1 for Android
};

```

Android 设备的配置应该是这样的，例如:

```
  const config = { 
    platformName: "Android",
    platformVersion: "8",
    deviceName: "Android Emulator",
    app: "/path/to/the/downloaded/app.apk",
    automationName: "UiAutomator2"
}

```

这里，我们有驱动程序设置。

```
const driver = wd.promiseChainRemote('localhost', PORT);

```

现在我们需要配置 web 驱动程序并设置远程服务器和端口。在我们的例子中，Appium 运行在我们的本地机器上，所以我们将其设置为`localhost`，并使用默认端口`4723`。

现在开始测试。这里，我们只是确认为电子邮件和密码字段设置了可访问性标签。这是它应该有的样子。

```
test('Test Accessibilty Id', async () => {
    expect(await driver.hasElementByAccessibilityId('email')).toBe(true);
    expect(await driver.hasElementByAccessibilityId('password')).toBe(true);
});

```

## 在 Appium 中运行测试

要运行测试，我们首先通过在命令行上运行 Appium 来启动 Appium 服务器，如下所示:

```
[email protected] appium-app $ appium
[Appium] Welcome to Appium v1.20.2
[Appium] Appium REST http interface listener started on 0.0.0.0:4723

```

接下来，打开另一个终端，运行`npm test login`。您的结果应该如下所示:

```
Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   1 total
Time:        5.775s, estimated 7s
Ran all test suites matching /login/i.

```

Appium 将检查应用程序 UI，并测试是否为电子邮件和密码设置了可访问性标签。如果一切正常，它返回一个通过，否则，您的测试失败。

就是这样！你完了。

除了 Appium，你还可以使用其他选项，比如 [Selenium](https://www.selenium.dev/) 或 [Selendroid](http://selendroid.io/) 。然而，Appium [是最受欢迎的移动自动化测试工具](https://github.com/appium/appium/stargazers)，因为 Appium 的测试可以使用[相同的 API](https://appium.io/docs/en/about-appium/api/) 、*和*为 iOS 和 Android 编写，它是开源的。

## 结论

测试您的 React 原生移动应用程序可能会很耗时，但这对您未来的自己和管理应用程序的团队来说是一项关键投资，因为它可以防止错误，提高客户的信心和忠诚度，并让您晚上睡得好。

希望您已经了解了测试您的应用程序的重要性，以及如何开始使用 Appium 测试您的移动应用程序。

像往常一样，查看 [Appium 文档](https://appium.io/docs/en/about-appium/api/)来更深入地测试你的 React 原生应用。编码快乐！

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)