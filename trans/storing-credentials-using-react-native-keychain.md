# 使用 react-native-keychain-log rocket 博客存储凭证

> 原文：<https://blog.logrocket.com/storing-credentials-using-react-native-keychain/>

在本教程中，我们将向您展示如何在 React Native 应用程序中安全地存储用户凭据，并使用这些凭据对用户进行身份验证。

当您在应用程序中实现登录系统时，您需要一种保存登录凭证的方法。例如，您可能想要保存用户登录令牌或刷新令牌，以避免用户在打开应用程序时重复输入用户名和密码。

所以问题是，我们可以在哪里保存登录凭证？有些人会说，“让我们把这些信息保存在`AsyncStorage`中，”但是相信我，你不应该把关键信息保存在一个简单、普通和不安全的环境中。

为了在本地安全地存储信息，您应该使用 [react-native-keychain](https://github.com/oblador/react-native-keychain) 。

想知道如何在自己的 React Native app 中使用 react-native-keychain？要理解一件事，最好的办法就是以身作则。

因此，我们将制作一个模拟登录流程的小型基本应用程序，它将向您展示如何安全地保存令牌并使用令牌来验证用户。

## 构建基本的用户界面

首先，让我们[为登录屏幕构建一个基本的 UI](https://blog.logrocket.com/top-9-ui-libraries-kits-react/) 。请看下图，我们的登录界面会是什么样子。

![Basic UI for react-native-keychain App](img/f9fe9eb59a0fa4e113712e807b7279e1.png)

一个非常基本的登录界面，有一个黑暗的主题(附注:我喜欢黑暗的主题😉 ).

以下是该用户界面的代码:

```
// App.js
import React from "react";
import { StyleSheet, Text, TextInput, View, Dimensions } from "react-native";

export default function App() {

  return (
    <View style={styles.container}>
        <View>
          <Text style={styles.helloText}>Hello There!</Text>
          <TextInput placeholder="email" style={styles.textInput} />
          <TextInput
            placeholder="password"
            secureTextEntry
            style={styles.textInput}
          />
          <Text style={styles.loginBtn}>
            Login
          </Text>
        </View>
    </View>
  );
}
const screenWidth = Dimensions.get("screen").width;
const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: "#1f1f1f",
    alignItems: "center",
    paddingTop: 250,
  },
  helloText: {
    color: "white",
    marginBottom: 20,
    fontSize: 30,
  },
  textInput: {
    padding: 5,
    paddingStart: 15,
    backgroundColor: "#3b3b3b",
    width: screenWidth * 0.8,
    borderRadius: 25,
    marginBottom: 15,
    color: "white",
    fontWeight: "600",
  },
  loginBtn: {
    paddingHorizontal: 25,
    paddingVertical: 10,
    backgroundColor: "#ff1178",
    borderRadius: 25,
    color: "white",
    textAlign: "center",
  }
});

```

## 安装 react-native-keychain

我们有登录界面；现在，让我们安装 keychain 来安全地存储登录凭证。

要安装，请在项目的根目录下运行以下命令:

```
yarn add react-native-keychain

```

或者，如果您使用 npm:

```
npm i -S react-native-keychain

```

安装 react-native-keychain 后，我们可以继续实现登录系统。

## 实现 react-native-keychain

这个钥匙串提供了许多我们可以在各种场景中使用的方法，但是为了简单起见，我们将使用三种主要方法来保存、检索和删除凭证。

这些方法是:

*   `setGenericPassword`
*   `getGenericPassword`
*   `resetGenericPassword`

这些方法的作用不言自明。但是，我将简要概述一下。

为了存储凭证，我们使用`setGenericPassword`方法并传递两个参数。第一个参数是用户名，第二个参数是令牌或密码。

要读取存储凭证的值，我们可以使用不带任何参数的`getGenericPassword`方法。

最后，要删除凭证，我们可以使用不带任何参数的`resetGenericPassword`方法。

让我们存储用户凭证:

```
// App.js
import React, { useEffect, useState } from "react";
import { StyleSheet, Text, TextInput, View, Dimensions } from "react-native";
import * as Keychain from "react-native-keychain";
export default function App() {
  const [isLoggedIn, setIsLoggedIn] = useState(false);
  const [userDetails, setUserDetails] = useState({});

  const handleLogin = async () => {
    // login api call here
    const token =
      "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c";
    const username = "Akshay";
    await Keychain.setGenericPassword(username, token);
    setIsLoggedIn(true);
    setUserDetails({token, username});
  };

  return (
    <View style={styles.container}>
        <View>
          <Text style={styles.helloText}>Hello There!</Text>
          <TextInput placeholder="email" style={styles.textInput} />
          <TextInput
            placeholder="password"
            secureTextEntry
            style={styles.textInput}
          />
          <Text style={styles.loginBtn} onPress={handleLogin}>
            Login
          </Text>
        </View>
    </View>
  );
}
const screenWidth = Dimensions.get("screen").width;
const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: "#1f1f1f",
    alignItems: "center",
    // justifyContent: 'center',
    paddingTop: 250,
  },
  helloText: {
    color: "white",
    marginBottom: 20,
    fontSize: 30,
  },
  textInput: {
    padding: 5,
    paddingStart: 15,
    backgroundColor: "#3b3b3b",
    width: screenWidth * 0.8,
    borderRadius: 25,
    marginBottom: 15,
    color: "white",
    fontWeight: "600",
  },
  loginBtn: {
    paddingHorizontal: 25,
    paddingVertical: 10,
    backgroundColor: "#ff1178",
    borderRadius: 25,
    color: "white",
    textAlign: "center",
  }
});

```

正如您在上面的代码中看到的，我已经创建了一个`handleLogin`函数，并将其设置为登录文本的`onPress`。

在`handleLogin`函数中，我们还没有实现一个完整的登录系统，但是我们假设用户得到了授权，并且登录服务器返回了用户详细信息和一个令牌。

因此，我们会将这些信息保存在安全的存储中。

注意，我们保存的是用户令牌，而不是密码本身。在客户端保存用户密码不是一个好的做法。永远带着有有效期的代币。

为了保存信息，我们使用了`setGenericPassword`方法，并将用户名和令牌作为参数传递。

成功保存后，我们正在更新状态，将登录屏幕更改为欢迎屏幕。

## 构建欢迎屏幕

登录后，我们将显示一个带有用户名的欢迎屏幕。我们的欢迎屏幕将如下所示:

![Welcome Screen for react-native-keychain App](img/6cdaf04077c7365549d5c50ed4999953.png)

只有一条欢迎消息，后面是用户名和注销按钮。

基于`isLoggedIn`的状态变化，我们会显示一个欢迎界面，隐藏登录界面。

我们的代码将如下所示:

```
// App.js
import React, { useEffect, useState } from "react";
import { StyleSheet, Text, TextInput, View, Dimensions } from "react-native";
import * as Keychain from "react-native-keychain";
export default function App() {
  const [isLoggedIn, setIsLoggedIn] = useState(false);
  const [userDetails, setUserDetails] = useState({});
  useEffect(() => {
    (async () => {
      try {
        const credentials = await Keychain.getGenericPassword();
        if (credentials) {
          setIsLoggedIn(true);
          setUserDetails(credentials);
        } else {
          console.log("No credentials stored");
        }
      } catch (error) {
        console.log("Keychain couldn't be accessed!", error);
      }
    })();
  }, []);
  const handleLogin = async () => {
    // login api call here
    const token =
      "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c";
    const username = "Akshay";
    await Keychain.setGenericPassword(username, token);
    setIsLoggedIn(true);
    setUserDetails({token, username});
  };
  const handleLogout = async()=>{
    const logout = await Keychain.resetGenericPassword();
    console.log({logout});
    if(logout){
      setIsLoggedIn(false);
      setUserDetails({});
    }
  }
  return (
    <View style={styles.container}>
      {!isLoggedIn ? (
        <View>
          <Text style={styles.helloText}>Hello There!</Text>
          <TextInput placeholder="email" style={styles.textInput} />
          <TextInput
            placeholder="password"
            secureTextEntry
            style={styles.textInput}
          />
          <Text style={styles.loginBtn} onPress={handleLogin}>
            Login
          </Text>
        </View>
      ) : (
        <View>
          <Text style={styles.welcomeText}>
            Welcome back! {userDetails.username}
          </Text>
          <Text style={styles.logoutBtn} onPress={handleLogout} >Logout</Text>
        </View>
      )}
    </View>
  );
}
const screenWidth = Dimensions.get("screen").width;
const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: "#1f1f1f",
    alignItems: "center",
    // justifyContent: 'center',
    paddingTop: 250,
  },
  helloText: {
    color: "white",
    marginBottom: 20,
    fontSize: 30,
  },
  textInput: {
    padding: 5,
    paddingStart: 15,
    backgroundColor: "#3b3b3b",
    width: screenWidth * 0.8,
    borderRadius: 25,
    marginBottom: 15,
    color: "white",
    fontWeight: "600",
  },
  loginBtn: {
    paddingHorizontal: 25,
    paddingVertical: 10,
    backgroundColor: "#ff1178",
    borderRadius: 25,
    color: "white",
    textAlign: "center",
  },
  welcomeText: {
    color: "white",
    marginBottom: 20,
    fontSize: 30,
  },
  logoutBtn: {
    paddingHorizontal: 25,
    paddingVertical: 10,
    backgroundColor: "#ff1178",
    borderRadius: 25,
    color: "white",
    textAlign: "center",
  },
});

```

我们的整个代码将是这样的:

首先，我们的状态`isLoggedIn`在初始阶段被设置为`false`。因此，我们将显示我们的登录用户界面，当用户成功登录时，我们将凭据保存在安全存储中，并更新登录状态和用户详细信息状态。

状态更新后，我们将显示一个带有用户名和注销按钮的欢迎用户界面。

在注销按钮上，我们增加了一个`handleLogout`功能。在这个函数中，我们从我们的安全存储中重置或删除用户的凭证，并将我们的状态设置为初始数据。

你可能已经注意到有一个`useEffect`钩子也被写了。在这个钩子中，我们从安全存储中检索用户凭证，并在用户返回应用程序时根据这些信息更新状态。

如果用户已经在存储器中保存了他们的信息，那么我们将向他们显示欢迎用户界面。否则，我们将向他们显示登录用户界面，告诉他们没有登录。

这是您可以轻松存储和读取用户凭证而不危及用户安全的方式。

## 结论

在本教程中，您学习了如何使用 react-native-keychain 安全地将用户的信息保存在他们的设备上，而不会危及他们的安全。现在你可以在你的应用程序中实现你自己完整的登录系统。

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)