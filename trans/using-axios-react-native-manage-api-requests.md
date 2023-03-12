# 使用 Axios 和 React Native 来管理 API 请求

> 原文：<https://blog.logrocket.com/using-axios-react-native-manage-api-requests/>

## 介绍

在构建 web 或移动应用程序时，您通常需要向 API 发出网络请求。您可以发出这些网络请求来验证用户、更新资源或从您自己的服务器或第三方 API 检索资源。如果您想在浏览器环境中发出 API 请求，Fetch API 会很方便。

React Native 也有一个内置的 [Fetch API](https://blog.logrocket.com/data-fetching-react-native/) 类似于浏览器的，专门用于与移动应用程序的 API 联网。但是，有一些替代库，比如 Axios，您可以使用它们而不依赖于本机 Fetch API。

如果您只想从服务器检索资源，内置的 fetch API 可能就足够了。对于更复杂的网络需求，Axios 可能是更好的选择，因为它提供了额外的功能，例如拦截网络请求和响应。

此外，除了其他原因，大多数开发人员更喜欢 Axios 而不是内置的 fetch API，因为它的同构特性和开箱即用的 JSON 转换。

在本文中，您将了解如何在 React 本机应用程序中使用 Axios 管理 API 请求。

## Axios 简介

Axios 是一个流行的同构 HTTP 客户端。这意味着它可以在浏览器和节点运行时环境中运行。因此，您可以在 Node、浏览器和 React Native 中使用相同的代码库来发出 API 请求。

Axios 有几个特性，比如支持 Promise API、自动 JSON 转换、拦截网络请求和响应等等。

## 使用 Expo CLI 设置简单的 React 本地应用程序

在本教程中，我们将使用一个简单的 React 本地应用程序，该应用程序使用 Expo 的托管工作流进行设置。

如果已经设置了 React 本地应用程序，可以继续下一节。此外， [React 本地文档](https://reactnative.dev/docs/environment-setup)中有 Expo 管理工作流程的设置说明，可以让您在几分钟内启动并运行。

## Axios 简介

Axios 是最容易学习和使用的 HTTP 客户端之一。发出 API 请求非常简单，只需将一个配置对象传递给 Axios，或者使用必要的参数调用适当的方法。在本节中，您将学习 Axios 的基础知识。

以下小节中突出显示的功能是您在使用 Axios 时最常用的功能。

### 如何安装 Axios

根据您使用的软件包管理器，在终端窗口中键入以下命令之一，然后点击 **return** 安装 Axios:

```
// axios
npm install axios

// yarn
yarn add axios

```

### 如何使用 Axios 向 API 发出请求

当使用 Axios 调用 API 时，可以将一个配置对象传递给 Axios，或者为想要执行的相应 CRUD 操作调用一个方法。

例如，您可以通过以下两种方式之一向`/api/users`端点发出 GET 请求:

```
import axios from 'axios';
const baseUrl = 'https://reqres.in';

// Passing configuration object to axios
axios({
  method: 'get',
  url: `${baseUrl}/api/users/1`,
}).then((response) => {
  console.log(response.data);
});

// Invoking get method to perform a GET request
axios.get(`${baseUrl}/api/users/1`).then((response) => {
  console.log(response.data);
});

```

你也可以使用 async/await 来代替上面例子中的承诺链。

还有其他几个字段，如`baseURL`、`transformRequest`、`transformResponse`和`headers`，您可以将它们包含在传递给 Axios 的配置对象中:

```
import axios from 'axios';
const baseUrl = 'https://reqres.in';

// Passing configuration object to axios
const fetchUser = async () => {
  const configurationObject = {
    method: 'get',
    url: `${baseUrl}/api/users/1`,
  };
  const response = await axios(configurationObject);
  console.log(response.data);
};

// Invoking get method to perform a GET request
const fetchUser = async () => {
  const url = `${baseUrl}/api/users/1`;
  const response = await axios.get(url);
  console.log(response.data);
};

```

与内置的 Fetch API 不同，Axios 会立即将响应转换为 JSON。

### 如何使用 Axios 发出多个并发 API 请求

您可以通过 Axios 使用 Promise API 的`Promise.all`或`Promise.allSettled`方法，从 React 本地应用程序发出多个并发 API 请求。

在下面的代码片段中，所有 API 请求都将成功；将传递给`axios.get`方法的 URI 更改为不存在的，看看如果一些请求不成功会发生什么:

```
const concurrentRequests = [
      axios.get(`${baseUrl}/api/users/1`),
      axios.get(`${baseUrl}/api/users/2`),
      axios.get(`${baseUrl}/api/users/3`),
    ];
   // Using Promise.all
    Promise.all(concurrentRequests)
      .then((result) => {
        console.log(result);
      })
      .catch((err) => {
        console.log(err);
      });
    // Using Promise.allSettled
    Promise.allSettled(concurrentRequests)
      .then((result) => {
        console.log(result);
      })
      .catch((err) => {
        console.log(err);
      });

```

注意，如果其中一个输入承诺拒绝，那么`Promise.all`方法会立即拒绝。如果您希望看到所有的 API 请求都成功或者都不成功，请使用`Promise.all`。

另一方面，`Promise.allSettled`等待所有的输入承诺被拒绝或履行。然后，您可以检查每个响应对象的`fulfilled`或`rejected`状态。

### 如何在 Axios 中中止网络请求

Axios 提供了中止网络请求的功能。React Native 中该特性的一个典型用例是，当数据仍在传输中而组件被卸载时，在`useEffect`钩子中取消网络请求。

您可以阅读下面的代码片段来了解如何使用该功能:

```
useEffect(() => {
    const source = axios.CancelToken.source();
    const url = `${baseUrl}/api/users/${userId}`;
    const fetchUsers = async () => {
      try {
        const response = await axios.get(url, { cancelToken: source.token });
        console.log(response.data);
      } catch (error) {
        if(axios.isCancel(error)){
          console.log('Data fetching cancelled');
        }else{
         // Handle error
        }
      }
    };
    fetchUsers();
    return () => source.cancel("Data fetching cancelled");
  }, [userId]);

```

### 如何创建 Axios 的实例

您也可以使用自定义配置创建 Axios 实例。然后，使用实例公开的方法发出网络请求。

Axios 会将创建实例时传递的配置对象与传递给实例方法的配置合并:

```
const axiosInstance = axios.create({ baseURL: 'https://reqres.in/' });

axiosInstance.get('api/users/1').then((response) => {
  console.log(response.data);
});

```

## 使用带有 React Native 的 Axios 来管理 API 请求

在本节中，您将学习在 React 本机应用程序中使用 Axios 管理 API 请求。您将使用 Axios 执行一个简单的 CRUD(创建、读取、更新和删除)操作。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

### 如何在 React Native 中管理 API 键

大多数第三方 API 需要秘密凭证才能访问。将您的秘密 API 密钥保存在客户端的源代码中并不是一个好主意。如果您这样做了，任何检查您的捆绑应用程序代码的人都可以访问您的私钥。

管理 API 密钥的一个推荐方法是在第三方 API 和您的应用程序之间创建一个编排层。例如，您可以使用无服务器功能来安全地访问您的 API 密钥。

您的应用程序将调用无服务器函数公开的端点。无服务器功能将安全地访问您的 API 密钥，调用第三方 API，检索您需要的资源，并将其转发到您的移动应用程序。

### 在网络请求-响应周期中管理应用程序状态

当您向 API 发起网络请求时，请求要么成功，要么失败。因此，从请求到收到服务器的响应，跟踪应用程序的不同状态非常重要。

当数据仍在传输中时，您可以显示一个加载指示器。如果 CRUD 操作成功，您将向用户显示一条“成功”消息。如果失败，您将显示一条适当的错误消息。

这一点很重要，因为使用您的应用程序的客户端可能有一个缓慢的互联网连接或没有互联网接入。API 服务器有时可能会停机。跟踪应用程序的状态并显示适当的消息将提供良好的用户体验。

我们将在本文中使用 [reqres](https://reqres.in/) REST API。它是由虚拟数据组成的占位符 API。您可以使用 API 测试工具(如 Postman 或失眠症)来测试端点。

### 如何在 React Native 中使用 Axios 发出 GET 请求

在本节中，我们将向`/api/users`端点发出 GET 请求来检索用户。GET 是当您想从服务器请求资源时使用的 HTTP 方法。

我们将用户 ID 存储在 state 中，如下面的代码片段所示。您可以在附加到`Load User`按钮的`onPress`事件处理程序中更改用户 ID。改变用户 ID 将触发对 [`useEffect`钩子](https://blog.logrocket.com/guide-to-react-useeffect-hook/)内部 API 的 GET 请求。

在触发一个网络请求后，我们在屏幕上显示一个负载指示器。如果我们成功获取数据，我们将更新状态并删除加载指示器。如果我们由于某种原因未能检索到数据，我们将停止加载指示器并显示一条适当的错误消息。

如果用户在从服务器获得响应之前决定关闭应用程序，我们会在清理功能中中止网络请求。检查`useEffect`钩子中效果函数的返回值。

下面是在`App.js`组件中的代码:

```
import axios from "axios";
import React, { useState, useEffect } from "react";
import {
  StyleSheet,
  Text,
  ScrollView,
  View,
  Button,
  Image,
  Platform,
} from "react-native";
import Constants from "expo-constants";
const baseUrl = "https://reqres.in";
function User({ userObject }) {
  return (
    <View>
      <Image
        source={{ uri: userObject.avatar }}
        style={{ width: 128, height: 128, borderRadius: 64 }}
      />
      <Text style={{ textAlign: "center", color: "white" }}>
        {`${userObject.first_name} ${userObject.last_name}`}
      </Text>
    </View>
  );
}
export default function App() {
  const [userId, setUserId] = useState(1);
  const [user, setUser] = useState(null);
  const [isLoading, setIsLoading] = useState(false);
  const [hasError, setErrorFlag] = useState(false);
  const changeUserIdHandler = () => {
    setUserId((userId) => (userId === 3 ? 1 : userId + 1));
  };
  useEffect(() => {
    const source = axios.CancelToken.source();
    const url = `${baseUrl}/api/users/${userId}`;
    const fetchUsers = async () => {
      try {
        setIsLoading(true);
        const response = await axios.get(url, { cancelToken: source.token });
        if (response.status === 200) {
          setUser(response.data.data);
          setIsLoading(false);
          return;
        } else {
          throw new Error("Failed to fetch users");
        }
      } catch (error) {
        if(axios.isCancel(error)){
          console.log('Data fetching cancelled');
        }else{
          setErrorFlag(true);
          setIsLoading(false);
        }
      }
    };
    fetchUsers();
    return () => source.cancel("Data fetching cancelled");
  }, [userId]);
  return (
    <ScrollView contentContainerStyle={styles.container}>
      <View style={styles.wrapperStyle}>
        {!isLoading && !hasError && user && <User userObject={user} />}
      </View>
      <View style={styles.wrapperStyle}>
        {isLoading && <Text> Loading </Text>}
        {!isLoading && hasError && <Text> An error has occurred </Text>}
      </View>
      <View>
        <Button
          title="Load user"
          onPress={changeUserIdHandler}
          disabled={isLoading}
          style={styles.buttonStyles}
        />
      </View>
    </ScrollView>
  );
}
const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: "dodgerblue",
    alignItems: "center",
    justifyContent: "center",
    marginTop: Platform.OS === "ios" ? 0 : Constants.statusBarHeight,
  },
  wrapperStyle: {
    minHeight: 128,
  },
  buttonStyles: {
    padding: 100,
  },
});

```

> 在上面的组件中，我们使用`useEffect`钩子来执行副作用，比如从 API 获取数据。但是，这可能会在 React 的未来版本中发生变化。您可以查看 React 文档以了解更多关于 React 悬念的信息；这是一个数据获取特性，将很快在 React 的稳定版本中实现。

### 如何在 React Native 中使用 Axios 发出 POST 请求

在本节中，您将学习如何提出发布请求。POST 是用于向服务器发送数据以更新或创建资源的 HTTP 方法。

我们使用的占位符 API 公开了用于创建资源的`/api/users`端点。成功创建资源后，您将得到一个带有 201 状态代码的响应。

在 Axios 中发出 POST 请求类似于发出 GET 请求。大多数情况下，POST 请求是使用表单提交的用户生成的数据发出的。提交的数据可以来自客户的登录、注册或反馈表单。这种数据在提交之前需要在客户端进行验证。

构建复杂的应用程序时，您可以使用其中一个表单包进行数据验证。大多数软件包都经过了很好的架构和优化，并且背后有一个很好的社区。但是，在将库集成到应用程序中之前，请研究一下权衡。尤其是您添加到应用程序的额外捆绑包大小，以及它可能引入的潜在安全漏洞。

有两个主要的 React 包用于管理表单。这些包是 [Formik 和 React Hook Form](https://blog.logrocket.com/react-hook-form-vs-formik-comparison/) 。如果你有兴趣，可以在 React 中找到大量关于表单验证的[文章。](https://blog.logrocket.com/the-ultimate-roundup-of-react-form-validation-solutions/)

在下面的代码片段中，我们为用户的全名和电子邮件提供了一个 React 本地表单。两个`TextInput`组件都是受控组件。理想情况下，当用户填写表单时，您可以实时执行数据验证。然而，这里的情况并非如此，因为表单数据验证超出了本文的范围。

单击 submit 按钮后，`TextInput`字段和 submit 按钮被禁用，然后显示一条消息表明您正在创建资源。禁用提交按钮可以确保用户不会多次提交。

成功提交 POST 请求后，您会向用户显示一条成功消息:

```
import axios from "axios";
import React, { useState } from "react";
import {
  StyleSheet,
  Text,
  ScrollView,
  View,
  Button,
  Platform,
  TextInput,
} from "react-native";
import Constants from "expo-constants";

const baseUrl = "https://reqres.in";

export default function App() {
  const [fullName, setFullName] = useState("");
  const [email, setEmail] = useState("");
  const [isLoading, setIsLoading] = useState(false);

  const onChangeNameHandler = (fullName) => {
    setFullName(fullName);
  };

  const onChangeEmailHandler = (email) => {
    setEmail(email);
  };

  const onSubmitFormHandler = async (event) => {
    if (!fullName.trim() || !email.trim()) {
      alert("Name or Email is invalid");
      return;
    }
    setIsLoading(true);
    try {
      const response = await axios.post(`${baseUrl}/api/users`, {
        fullName,
        email,
      });
      if (response.status === 201) {
        alert(` You have created: ${JSON.stringify(response.data)}`);
        setIsLoading(false);
        setFullName('');
        setEmail('');
      } else {
        throw new Error("An error has occurred");
      }
    } catch (error) {
      alert("An error has occurred");
      setIsLoading(false);
    }
  };

  return (
    <ScrollView contentContainerStyle={styles.container}>
      <View>
        <View style={styles.wrapper}>
          {isLoading ? (
            <Text style={styles.formHeading}> Creating resource </Text>
          ) : (
            <Text style={styles.formHeading}>Create new user</Text>
          )}
        </View>
        <View style={styles.wrapper}>
          <TextInput
            placeholder="Full Name"
            placeholderTextColor="#ffffff"
            style={styles.input}
            value={fullName}
            editable={!isLoading}
            onChangeText={onChangeNameHandler}
          />
        </View>
        <View style={styles.wrapper}>
          <TextInput
            placeholder="Email"
            placeholderTextColor="#ffffff"
            style={styles.input}
            value={email}
            editable={!isLoading}
            onChangeText={onChangeEmailHandler}
          />
        </View>
        <View>
          <Button
            title="Submit"
            onPress={onSubmitFormHandler}
            style={styles.submitButton}
            disabled={isLoading}
          />
        </View>
      </View>
    </ScrollView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: "#252526",
    alignItems: "center",
    justifyContent: "center",
    marginTop: Platform.OS === "ios" ? 0 : Constants.statusBarHeight,
  },
  formHeading: {
    color: "#ffffff",
  },
  wrapper: {
    marginBottom: 10,
  },
  input: {
    borderWidth: 2,
    borderColor: "grey",
    minWidth: 200,
    textAlignVertical: "center",
    paddingLeft: 10,
    borderRadius: 20,
    color: "#ffffff",
  },
  submitButton: {
    backgroundColor: "gray",
    padding: 100,
  },
});

```

### 如何在 React Native 中使用 Axios 发出上传请求

更新资源需要 PUT 或 PATCH 方法，不过我将重点放在 PUT 上。

如果资源存在，使用 PUT 方法会完全覆盖它，如果不存在，则会创建一个新资源。另一方面，如果资源存在，PATCH 对其进行部分更新，如果不存在，则不做任何事情。

向 API 发出 PUT 请求类似于发出 POST 请求。唯一的区别是传递给 Axios 的配置对象，或者是向 API 发出 PUT 请求时需要调用的 HTTP 方法。

您可以用下面的代码替换 POST 请求的`onSubmitFormHandler`来发出 PUT 请求。为了完整起见，我在下面的事件处理程序中使用了承诺链，而不是异步/等待:

```
 const onSubmitFormHandler = (event) => {
    if (!fullName.trim() || !email.trim()) {
      alert("Name or Email is invalid");
      return;
    }
    setIsLoading(true);

    const configurationObject = {
      url: `${baseUrl}/api/users/2`,
      method: "PUT",
      data: { fullName, email },
    };

    axios(configurationObject)
      .then((response) => {
        if (response.status === 200) {
          alert(` You have updated: ${JSON.stringify(response.data)}`);
          setIsLoading(false);
          setFullName("");
          setEmail("");
        } else {
          throw new Error("An error has occurred");
        }
      })
      .catch((error) => {
        alert("An error has occurred");
        setIsLoading(false);
      });
  };

```

### 如何在 React Native 中使用 Axios 发出删除请求

您可以使用 Axios 发出删除请求，就像发出 POST 和 PUT 请求一样。

顾名思义，删除请求将从服务器端删除资源。您可以用下面的事件处理程序替换发出 POST 请求的代码中的`onSubmitFormHandler`,以发出删除请求。代码的其余部分保持不变:

```
const onSubmitFormHandler = async (event) => {
    if (!fullName.trim() || !email.trim()) {
      alert("Name or Email is invalid");
      return;
    }
    setIsLoading(true);
    try {
      const response = await axios.delete(`${baseUrl}/api/users/2`, {
        fullName,
        email,
      });
      if (response.status === 204) {
        alert(` You have deleted: ${JSON.stringify(response.data)}`);
        setIsLoading(false);
        setFullName('');
        setEmail('');
      } else {
        throw new Error("Failed to delete resource");
      }
    } catch (error) {
      alert("Failed to delete resource");
      setIsLoading(false);
    }
  }; 

```

## 结论

在构建移动应用程序时，向 API 发出网络请求是不可避免的，Axios 是最受欢迎的 HTTP 客户端之一。它增加了一些功能，使网络尽可能简单。

您的应用程序与之交互的 API 可以是自托管或第三方 API。为了改善用户体验，有效管理网络请求-响应周期至关重要。

另一方面，您需要权衡将 Axios 等第三方软件包添加到移动应用程序中的利弊。虽然 Axios 是一个受欢迎且维护良好的软件包，但根据 Packagephobia 的说法，它会将您的软件包大小增加 367kB。

虽然在使用功能强大的移动设备时，捆绑包的大小可能看起来像是应用程序的一个小增加，但您需要考虑它对使用功能不太强大的移动设备的用户的影响。同样，您需要确保 Axios 等第三方软件包不会给您的应用程序带来安全漏洞。

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)