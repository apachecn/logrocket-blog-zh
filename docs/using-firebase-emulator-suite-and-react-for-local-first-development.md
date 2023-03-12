# 使用 Firebase 仿真器套件和 React 进行本地开发

> 原文：<https://blog.logrocket.com/using-firebase-emulator-suite-and-react-for-local-first-development/>

## 什么是 Firebase 模拟器？

[Firebase Emulator](https://firebase.google.com/docs/emulator-suite) 套件支持本地优先的开发工作流，这不仅非常适合原型开发，而且比直接与 Firebase 控制台通信更快，更安全，因为您的开发环境是完全隔离的(让您有机会测试边缘情况，而不用担心损坏或删除您的生产数据)，并且更便宜。

不管有多少开发人员在一个项目中工作，所有与 Firebase 的交互都是本地的。

## 先决条件

要使用本指南，您需要:

## 如何将 Firebase 模拟器与 React 集成

首先，使用`$ yarn create react-app todo && cd my-app`创建一个`react-app`，并通过运行`$ yarn global add firebase-tools`来全局安装`firebase-tools`。跑`$ firebase login`认证自己。

要创建一个 Firebase 项目，运行`$ firebase projects:create`并输入一个惟一的 ID。您可以将您的 ID 存储在变量`$ fbid=your-unique-id-here`(可以用作`$fbid`)中。)

接下来，通过运行`$ firebase apps:create --project $fbid web my-app`在项目中创建一个 Firebase web 应用程序。

在 react 应用程序目录的根目录下，通过运行`$ firebase init firestore --project $fbid`来启用 [Firestore](https://firebase.google.com/docs/firestore) ，这将出错并显示一个链接来启用 Firebase 控制台中的功能。这样做之后，重新运行`$ firebase init firestore --project $fbid`，这将创建几个文件。

做完这些，现在运行`$ firebase init emulators --project $fbid`并选择`Authentication`和`Firestore`仿真器。接受每个模拟器的默认端口，确保启用模拟器 UI，并下载模拟器。(同样， [Java](https://www.java.com/en/download/) 必须安装在您的系统上才能运行模拟器。)

将以下内容附加到您的`.gitignore`:

```
firebase-debug.log*
firestore-debug.log*
ui-debug.log*

```

然后安装`firebase`作为依赖项。

```
$ yarn add firebase

```

初始化 Firebase 项目和应用程序后，通过运行`$ firebase apps:sdkconfig --project $fbid > src/firebase.js`将应用程序的配置放在`src/firebase.js`中，并编辑文件，如下所示:

```
// src/firebase.js

import firebase from 'firebase/app';
import 'firebase/auth';
import 'firebase/firestore';

// TODO: Use a configuration object
firebase.initializeApp({
  projectId: '',
  appId: '',
  databaseURL: '',
  storageBucket: '',
  locationId: '',
  apiKey: '',
  authDomain: '',
  messagingSenderId: '',
});

const db = firebase.firestore();
const auth = firebase.auth;

// eslint-disable-next-line no-restricted-globals
if (location.hostname === 'localhost') {
  db.useEmulator('localhost', 8080);
  auth().useEmulator('http://localhost:9099/', { disableWarnings: true });
}

export default firebase;
export { db, auth };

```

值得注意的是，我们检查我们是否在`localhost`上，这包括`development`和`test`环境，并告诉`firebase.auth()`和`firebase.firestore()`到`useEmulator`。

在`auth` `useEmulator`调用中，我们禁用警告，警告您不要使用任何真实的凭据(例如，您的 Google 凭据)，因为您的应用程序和仿真器之间的数据是通过未加密的 HTTP 传输的。

## 运行 Firebase 模拟器

在两个独立的 shells 中，运行`$ yarn start`和`$ firebase emulators:start`。在`[http://localhost:4000/firestore](http://localhost:4000/firestore)`打开 Firestore 模拟器 UI，应该是这样的:

![Firebase Emulator Suite in Firestore Emulator](img/ac24e43fbbaf53600c33ef99fd3473b6.png)

或者，您可以添加 npm 脚本`"emulators": "firebase emulators:start"`并使用`$ yarn emulators`来启动模拟器。

## 模拟火风暴

在`src/App.js`中，导入`db`并将一些数据写入 Firestore 仿真器:

```
// src/App.js
import React from "react";
import { db } from "./firebase";

function App() {
  db.doc("hello/world").set({ hello: "world" });
  return <div />;
}

export default App;

```

`Firestore`选项卡现在应该有我们设置的数据了。如果没有，刷新页面或导航到标签页。

![Firestore Emulator With Written Data](img/b6b8f22ca1469e3d3d30f0ad1b722499.png)

要从 Firestore Emulator 读取数据，请先在 UI 中输入数据。

![Writing Firestore Database Data in Firestore Emulator UI](img/aae51f76ac6b6c71d369b03140ee1cca.png)

```
// src/App.js

import React from 'react';
import { db } from './firebase';

function App() {
  const [data, setData] = React.useState();
  React.useEffect(() => {
    db.doc('people/me')
      .get()
      .then((data) => setData(data.data()));
  }, []);

  return <p>{data?.hungry ? "I'm hungry" : "I'm full"}!</p>;
}

export default App;

```

快速提示:您可以运行任何 Firestore 方法，并直接从开发工具与您的本地 Firestore 实例进行交互。

## 从实时数据库模拟器 UI 导入和导出数据

默认情况下，写入仿真数据库的数据不会在会话之间持续，但是您可以从实时数据库仿真器 UI 手动导出和导入数据，或者更改您的`emulators` npm 脚本来自动执行此操作:

```
  "emulators": "firebase emulators:start --import=data --export-on-exit",

```

这将创建一个可以添加到`.gitignore`的`data`目录。值得注意的是，在当前的`firebase-tools`(版本`9.0.1`)中，认证模拟器不支持自动导入/导出。

但是，这个特性已经被[合并到上游](https://github.com/firebase/firebase-tools/pull/2955)中，所以不需要很长时间就可以在会话之间持久化用户。如果你需要这个特性并且迫不及待，看看这个[问题](https://github.com/firebase/firebase-tools/issues/2749)中的一系列解决方法。

## 在 Firebase 中模拟身份验证

要开始使用身份验证模拟器，请确保 Firebase 模拟器正在运行，并使用您想要的任何提供程序实现身份验证逻辑。我将使用带有弹出窗口的 Google 身份验证提供程序:

```
import React from 'react';
import { auth } from './firebase';

function Login() {
  const login = async () => {
    const provider = new auth.GoogleAuthProvider();
    try {
      await auth().signInWithPopup(provider);
    } catch (e) {}
  };
  return <button onClick={login}>login</button>;
}

export default Login;

```

点击`login`按钮会将我们重定向到一个登录页面，该页面可能有先前创建的用户:

![Authentication Emulator Sign-in on Google](img/9f8e0b4266282f849e7ffb247006ac1f.png)

同样，强烈建议您不要输入任何真实的凭据。任何新创建的用户都将出现在**认证**选项卡中:

![Authentication Emulator With Google Account](img/9f171cff778f088e4064c0f09f03eaad.png)

您可以通过两种方式检索用户。成功登录后，将用户设置为`auth().currentUser`:

```
try {
  await auth().signInWithPopup(provider);
  const user = await auth().currentUser;
  setUser(user);
} catch(e) {}

```

或者监听一个`useEffect`钩子内的认证变化:

```
React.useEffect(() => {
  return auth().onAuthStateChanged((user) => {
    if (user) {
      setUser(user);
    } else {
      setUser(null);
    }
  });
}, []);

```

无论哪种方式，身份验证都是模拟的，不会通过提供者创建实际用户。注意，我们不需要在 Firebase 控制台中启用 Google provider。但是，您必须在部署之前这样做。

## 结论

在本文中，您了解了如何使用命令行设置 Firebase 项目，以及如何使用 Firebase Emulator 对本地优先的工作流进行仿真 Firestore 和 Firebase 身份验证。感谢阅读。

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