# 在 React apps 中使用 Firebase 处理用户认证

> 原文：<https://blog.logrocket.com/user-authentication-firebase-react-apps/>

***编者按*** *:这篇博文于 2021 年 8 月 6 日由 [Atharva Deosthale](https://blog.logrocket.com/author/atharvadeosthale/) 审阅并重写，以包含 Firebase 的最新特性并澄清原始教程的说明。它于 2022 年 1 月 10 日再次更新，以解决 Firebase v9 发布后的重大变化。*

现在的网站和 app，安全性非常重要。这主要是为了确保私人数据不会泄露给公众，也不会有人代表你采取行动。

有时，开发人员会制作他们自己的后端，并加入他们自己定制的安全方法。其他开发人员使用各种 BaaS(后端即服务)平台，这样他们就不需要担心安全细节，这些平台可以帮助开发人员以最安全的方式验证用户身份。

今天，我们将使用 Firebase，它是一个 BaaS，可以帮助我们提供各种服务，如数据库认证和云存储。我们将了解如何使用 Firebase 中的身份验证服务来保护 React 应用程序。

## react 中使用 firebase 进行身份验证的要求

*   Node.js 已安装
*   代码编辑器—我更喜欢 Visual Studio 代码
*   谷歌账户——我们需要这个来使用 Firebase
*   React 的基础知识——我不会向 React 的完全初学者推荐这个教程

如果你在整个教程中遇到任何问题，你可以参考 [GitHub 库](https://github.com/atharvadeosthale/firebase-auth-article)中的代码。

## 设置 Firebase

在我们深入 React 并开始编写一些好东西之前，我们需要通过 [Firebase 控制台](https://console.firebase.google.com/u/0/)建立我们自己的 Firebase 项目。要开始，请在浏览器中导航到 Firebase 控制台。确保您已登录您的 Google 帐户。

现在，点击**添加项目**，您将看到以下屏幕:

![Add a Project in Firebase](img/33110eaeeede6126ac33aa12469be067.png)

输入项目名称。在我的情况下，我将它命名为`firebase-auth-article`。

一旦你给它起了一个好听的名字，点击**继续**，你应该会被提示一个选项来启用谷歌分析。对于本教程，我们不需要谷歌分析，但打开它不会有什么坏处，所以如果你想，就打开它吧。

完成所有步骤后，您应该会看到类似如下的仪表板:

![Firebase Console Dashboard](img/5ec7e8d6b5971f82eb152c6bbe8e82a1.png)

首先，让我们设置身份验证。点击侧边栏上的**认证**，点击**入门**启用该模块。现在，您将看到各种身份验证选项:

![Authentication Page in Firebase](img/70cbcc17cf07873132593f5039f94a78.png)

首先点击**电子邮件/密码**，启用并保存:

![Email and Password Options in Firebase](img/5e499c5a09e540df88d8d9185ecc7f65.png)

现在按下**谷歌**:

![Google Options in Firebase](img/d7a0630b088a8cdd8f8e470f11961afe.png)

按“启用”，选择一个项目支持电子邮件地址，然后点击**保存**为我们的应用程序激活谷歌认证。

现在，让我们设置我们将在项目中使用的数据库，即 Cloud Firestore。点击侧边栏上的**云火商店**，点击**创建数据库**。您应该会看到以下对话框:

![Create Database Options in Firebase](img/07846a640eca711a50806ef6f63f0441.png)

记住在测试模式下选择**开始。我们使用测试模式，因为在本教程中我们不处理生产级应用程序。生产模式数据库需要配置安全规则，这超出了本教程的范围。**

点击**下一个**。选择区域。我会让它保持默认，然后按下**启用**。这应该完全设置好您的云 Firestore 数据库。

## 创建和设置 React 应用程序

导航到一个安全的文件夹，并在终端中键入以下命令来创建一个新的 React 应用程序:

```
npx create-react-app appname

```

记得用自己选择的名字替换`appname`。名称并不真正影响教程的工作方式。React 应用程序成功创建后，键入命令安装我们在整个项目中需要的几个 npm 包:

```
npm install firebase react-router-dom react-firebase-hooks

```

这里，我们安装了`firebase`来与 Firebase 服务通信，我们还安装了`[react-router-dom](https://blog.logrocket.com/react-router-dom-tutorial-examples/)`来处理应用程序的路由。我们使用`react-firebase-hooks`来管理用户的认证状态。

键入以下命令运行 React 应用程序:

```
cd appname && npm start

```

这将启动您的浏览器，您应该会看到以下屏幕:

![Beginning Setup for React App with Firebase](img/c1d859e06b84fe26ff6170486db9a80a.png)

现在，让我们做一些清理，以便我们可以继续编码。从`src`文件夹中删除以下文件:`App.test.js`、`logo.svg`和`setupTests.js`。一旦你删除这些文件，删除`App.css`的所有内容，你会在 React 应用上看到一个错误。不用担心；只需移除`App.js`中的徽标导入并清空`div`，这样您的`App.js`看起来就像这样:

```
import './App.css';
function App() {
  return (
    <div className="app">

    </div>
  );
}
export default App;

```

## 将 Firebase 集成到我们的 React 应用程序中

转到 Firebase 控制台仪表板，点击**项目设置**，向下滚动，您应该会看到类似这样的内容:

![Project Settings with No App for Firebase](img/8afd2fc13a9c4d8c530da728037af26d.png)

点击第三个图标(>)为 web 配置我们的 Firebase 项目。输入应用程序名称并点击**继续**。回到项目设置，您现在应该会看到如下配置:

![Web Config in Firebase Console](img/db1a301c679d914e6c369c3b8c261658.png)

复制配置。在`src`文件夹中创建一个名为`firebase.js`的新文件。让我们首先导入`firebase`模块，因为 Firebase 在 v9 中使用模块化用法::

```
import { initializeApp } from "firebase/app";
​​import {
​​  GoogleAuthProvider,
​​  getAuth,
​​  signInWithPopup,
​​  signInWithEmailAndPassword,
​​  createUserWithEmailAndPassword,
​​  sendPasswordResetEmail,
​​  signOut,
​​} from "firebase/auth";
​​import {
​​  getFirestore,
​​  query,
​​  getDocs,
​​  collection,
​​  where,
​​  addDoc,
​​} from "firebase/firestore";

```

现在粘贴我们刚刚复制的配置。让我们初始化我们的应用程序和服务，以便我们可以在整个应用程序中使用 Firebase:

```
const app = ​​initializeApp(firebaseConfig);
​​const auth = getAuth(app);
​​const db = getFirestore(app);

```

这将使用我们的配置来识别项目并初始化身份验证和数据库模块。

我们将在`firebase.js`中创建所有重要的认证相关功能。所以先来看看谷歌认证功能:

```
const googleProvider = new GoogleAuthProvider();
const signInWithGoogle = async () => {
  try {
    const res = await signInWithPopup(auth, googleProvider);
    const user = res.user;
    const q = query(collection(db, "users"), where("uid", "==", user.uid));
    const docs = await getDocs(q);
    if (docs.docs.length === 0) {
      await addDoc(collection(db, "users"), {
        uid: user.uid,
        name: user.displayName,
        authProvider: "google",
        email: user.email,
      });
    }
  } catch (err) {
    console.error(err);
    alert(err.message);
  }
};

```

在上面的代码块中，我们使用了一个`try…catch`块和异步函数，这样我们可以轻松地处理错误并尽可能避免回调。

首先，我们尝试使用 Firebase 提供的`GoogleAuthProvider`登录。如果认证失败，则该流被发送到`catch`块。

然后我们查询数据库，检查这个用户是否在我们的数据库中注册了用户`uid`。如果没有使用`uid`的用户，这也意味着该用户是我们应用程序的新用户，我们将在数据库中创建一个新记录，其中包含额外的数据以供将来参考。

现在让我们创建一个使用电子邮件和密码登录的函数:

```
const logInWithEmailAndPassword = async (email, password) => {
  try {
    await signInWithEmailAndPassword(auth, email, password);
  } catch (err) {
    console.error(err);
    alert(err.message);
  }
};

```

这段代码非常简单。因为我们知道用户已经在我们这里注册了，所以我们不需要检查数据库。我们可以马上进行认证。我们只需将 email 和密码传递给`signInWithEmailAndPassword`函数，这是 Firebase 提供给我们的。

现在，让我们创建一个用电子邮件和密码注册用户的函数:

```
const registerWithEmailAndPassword = async (name, email, password) => {
  try {
    const res = await createUserWithEmailAndPassword(auth, email, password);
    const user = res.user;
    await addDoc(collection(db, "users"), {
      uid: user.uid,
      name,
      authProvider: "local",
      email,
    });
  } catch (err) {
    console.error(err);
    alert(err.message);
  }
};

```

因为我们知道用户是我们应用程序的新用户，所以我们为用户创建一个记录，而不检查我们的数据库中是否存在记录。这类似于我们在 Google 认证中使用的方法，但是没有检查。

创建一个将密码重置链接发送到电子邮件地址的功能:

```
const sendPasswordReset = async (email) => {
  try {
    await sendPasswordResetEmail(auth, email);
    alert("Password reset link sent!");
  } catch (err) {
    console.error(err);
    alert(err.message);
  }
};

```

这段代码很简单。我们只是在 Firebase 提供的`sendPasswordResetEmail`函数中传入邮件。密码重置邮件将由 Firebase 发送。

最后，注销功能:

```
const logout = () => {
  signOut(auth);
};

```

这里没什么，只是从 Firebase 启动了`signOut`函数，Firebase 就会施展它的魔法，为我们注销用户。

最后，我们导出所有的函数，下面是您的`firebase.js`最终应该是什么样子:

```
import { initializeApp } from "firebase/app";
import {
  GoogleAuthProvider,
  getAuth,
  signInWithPopup,
  signInWithEmailAndPassword,
  createUserWithEmailAndPassword,
  sendPasswordResetEmail,
  signOut,
} from "firebase/auth";
import {
  getFirestore,
  query,
  getDocs,
  collection,
  where,
  addDoc,
} from "firebase/firestore";
const firebaseConfig = {
  apiKey: "AIzaSyDIXJ5YT7hoNbBFqK3TBcV41-TzIO-7n7w",
  authDomain: "fir-auth-6edd8.firebaseapp.com",
  projectId: "fir-auth-6edd8",
  storageBucket: "fir-auth-6edd8.appspot.com",
  messagingSenderId: "904760319835",
  appId: "1:904760319835:web:44fd0d957f114b4e51447e",
  measurementId: "G-Q4TYKH9GG7",
};
const app = initializeApp(firebaseConfig);
const auth = getAuth(app);
const db = getFirestore(app);
const googleProvider = new GoogleAuthProvider();
const signInWithGoogle = async () => {
  try {
    const res = await signInWithPopup(auth, googleProvider);
    const user = res.user;
    const q = query(collection(db, "users"), where("uid", "==", user.uid));
    const docs = await getDocs(q);
    if (docs.docs.length === 0) {
      await addDoc(collection(db, "users"), {
        uid: user.uid,
        name: user.displayName,
        authProvider: "google",
        email: user.email,
      });
    }
  } catch (err) {
    console.error(err);
    alert(err.message);
  }
};
const logInWithEmailAndPassword = async (email, password) => {
  try {
    await signInWithEmailAndPassword(auth, email, password);
  } catch (err) {
    console.error(err);
    alert(err.message);
  }
};
const registerWithEmailAndPassword = async (name, email, password) => {
  try {
    const res = await createUserWithEmailAndPassword(auth, email, password);
    const user = res.user;
    await addDoc(collection(db, "users"), {
      uid: user.uid,
      name,
      authProvider: "local",
      email,
    });
  } catch (err) {
    console.error(err);
    alert(err.message);
  }
};
const sendPasswordReset = async (email) => {
  try {
    await sendPasswordResetEmail(auth, email);
    alert("Password reset link sent!");
  } catch (err) {
    console.error(err);
    alert(err.message);
  }
};
const logout = () => {
  signOut(auth);
};
export {
  auth,
  db,
  signInWithGoogle,
  logInWithEmailAndPassword,
  registerWithEmailAndPassword,
  sendPasswordReset,
  logout,
};

```

接下来，让我们研究实际的功能。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 创建登录页面

创建两个新文件来创建一个新组件，`Login.js`和`Login.css`。我强烈推荐在 Visual Studio 代码中安装 ES7 代码片段，这样你就可以开始输入`rfce`并按回车键来创建一个组件样板文件。

现在，让我们将这个组件分配给一个路由。为此，我们需要配置 React 路由器。转到`App.js`并导入以下内容:

```
import { BrowserRouter as Router, Route, Routes } from "react-router-dom";

```

然后，在`App.js`的 JSX 部分，添加以下配置来为我们的应用程序启用路由:

```
<div className="app">
  <Router>
    <Switch>
      <Route exact path="/" component={Login} />
    </Switch>
  </Router>
</div>

```

记得导入顶部的`Login`组件！

转到`Login.css`并添加以下样式。我们不会把重点放在造型上，所以这里有一些风格供你使用:

```
.login {
  height: 100vh;
  width: 100vw;
  display: flex;
  align-items: center;
  justify-content: center;
}
.login__container {
  display: flex;
  flex-direction: column;
  text-align: center;
  background-color: #dcdcdc;
  padding: 30px;
}
.login__textBox {
  padding: 10px;
  font-size: 18px;
  margin-bottom: 10px;
}
.login__btn {
  padding: 10px;
  font-size: 18px;
  margin-bottom: 10px;
  border: none;
  color: white;
  background-color: black;
}
.login__google {
  background-color: #4285f4;
}
.login div {
  margin-top: 7px;
}

```

转到`Login.js`，让我们看看我们的登录功能是如何工作的:

```
import React, { useEffect, useState } from "react";
import { Link, useNavigate } from "react-router-dom";
import { auth, signInWithEmailAndPassword, signInWithGoogle } from "./firebase";
import { useAuthState } from "react-firebase-hooks/auth";
import "./Login.css";
function Login() {
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");
  const [user, loading, error] = useAuthState(auth);
  const navigate = useNavigate();
  useEffect(() => {
    if (loading) {
      // maybe trigger a loading screen
      return;
    }
    if (user) navigate("/dashboard");
  }, [user, loading]);
  return (
    <div className="login">
      <div className="login__container">
        <input
          type="text"
          className="login__textBox"
          value={email}
          onChange={(e) => setEmail(e.target.value)}
          placeholder="E-mail Address"
        />
        <input
          type="password"
          className="login__textBox"
          value={password}
          onChange={(e) => setPassword(e.target.value)}
          placeholder="Password"
        />
        <button
          className="login__btn"
          onClick={() => signInWithEmailAndPassword(email, password)}
        >
          Login
        </button>
        <button className="login__btn login__google" onClick={signInWithGoogle}>
          Login with Google
        </button>
        <div>
          <Link to="/reset">Forgot Password</Link>
        </div>
        <div>
          Don't have an account? <Link to="/register">Register</Link> now.
        </div>
      </div>
    </div>
  );
}
export default Login;

```

上面的代码可能看起来很长，很难理解，但事实并非如此。我们已经介绍了主要的身份验证部分，现在我们只是在布局中实现它们。

下面是上面代码块中发生的情况。我们使用在`firebase.js`中创建的函数进行认证。我们还使用`react-firebase-hooks`和`useEffect`来跟踪用户的认证状态。因此，如果用户通过身份验证，用户将自动重定向到仪表板，这是我们还没有做到的。

以下是您将在屏幕上看到的内容:

![Basic Login Page React Firebase](img/c5c2a23fd7bb5016140d1d25a7e16821.png)

创建一个名为`Register`的新组件来处理用户注册。以下是`Register.css`的款式:

```
.register {
  height: 100vh;
  width: 100vw;
  display: flex;
  align-items: center;
  justify-content: center;
}
.register__container {
  display: flex;
  flex-direction: column;
  text-align: center;
  background-color: #dcdcdc;
  padding: 30px;
}
.register__textBox {
  padding: 10px;
  font-size: 18px;
  margin-bottom: 10px;
}
.register__btn {
  padding: 10px;
  font-size: 18px;
  margin-bottom: 10px;
  border: none;
  color: white;
  background-color: black;
}
.register__google {
  background-color: #4285f4;
}
.register div {
  margin-top: 7px;
}

```

之后，让我们看看寄存器功能是如何在布局中实现的。在`Register.js`中使用该布局:

```
import React, { useEffect, useState } from "react";
import { useAuthState } from "react-firebase-hooks/auth";
import { Link, useHistory } from "react-router-dom";
import {
  auth,
  registerWithEmailAndPassword,
  signInWithGoogle,
} from "./firebase";
import "./Register.css";
function Register() {
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");
  const [name, setName] = useState("");
  const [user, loading, error] = useAuthState(auth);
  const history = useHistory();
  const register = () => {
    if (!name) alert("Please enter name");
    registerWithEmailAndPassword(name, email, password);
  };
  useEffect(() => {
    if (loading) return;
    if (user) history.replace("/dashboard");
  }, [user, loading]);
  return (
    <div className="register">
      <div className="register__container">
        <input
          type="text"
          className="register__textBox"
          value={name}
          onChange={(e) => setName(e.target.value)}
          placeholder="Full Name"
        />
        <input
          type="text"
          className="register__textBox"
          value={email}
          onChange={(e) => setEmail(e.target.value)}
          placeholder="E-mail Address"
        />
        <input
          type="password"
          className="register__textBox"
          value={password}
          onChange={(e) => setPassword(e.target.value)}
          placeholder="Password"
        />
        <button className="register__btn" onClick={register}>
          Register
        </button>
        <button
          className="register__btn register__google"
          onClick={signInWithGoogle}
        >
          Register with Google
        </button>
        <div>
          Already have an account? <Link to="/">Login</Link> now.
        </div>
      </div>
    </div>
  );
}
export default Register;

```

这里，我们使用的方法与在`Login`组件中使用的方法相似。我们只是在使用我们之前在`firebase.js`中创建的函数。同样，这里我们使用`useEffect`和`react-firebase-hooks`来跟踪用户认证状态。

让我们看看重置密码。创建一个名为`Reset`的新组件，下面是`Reset.css`的样式:

```
.reset {
  height: 100vh;
  width: 100vw;
  display: flex;
  align-items: center;
  justify-content: center;
}
.reset__container {
  display: flex;
  flex-direction: column;
  text-align: center;
  background-color: #dcdcdc;
  padding: 30px;
}
.reset__textBox {
  padding: 10px;
  font-size: 18px;
  margin-bottom: 10px;
}
.reset__btn {
  padding: 10px;
  font-size: 18px;
  margin-bottom: 10px;
  border: none;
  color: white;
  background-color: black;
}
.reset div {
  margin-top: 7px;
}

```

这是`Reset.js`的布局:

```
import React, { useEffect, useState } from "react";
import { useAuthState } from "react-firebase-hooks/auth";
import { useNavigate } from "react-router-dom";
import { Link } from "react-router-dom";
import { auth, sendPasswordResetEmail } from "./firebase";
import "./Reset.css";
function Reset() {
  const [email, setEmail] = useState("");
  const [user, loading, error] = useAuthState(auth);
  const navigate = useNavigate();
  useEffect(() => {
    if (loading) return;
    if (user) navigate("/dashboard");
  }, [user, loading]);
  return (
    <div className="reset">
      <div className="reset__container">
        <input
          type="text"
          className="reset__textBox"
          value={email}
          onChange={(e) => setEmail(e.target.value)}
          placeholder="E-mail Address"
        />
        <button
          className="reset__btn"
          onClick={() => sendPasswordResetEmail(email)}
        >
          Send password reset email
        </button>
        <div>
          Don't have an account? <Link to="/register">Register</Link> now.
        </div>
      </div>
    </div>
  );
}
export default Reset;

```

这类似于我们对`Login`和`Register`组件所做的。我们只是在使用我们之前创建的函数。

现在，让我们把注意力集中在仪表板上。创建一个名为`Dashboard`的新组件，下面是`Dashboard.css`的样式:

```
.dashboard {
  height: 100vh;
  width: 100vw;
  display: flex;
  align-items: center;
  justify-content: center;
}
.dashboard__container {
  display: flex;
  flex-direction: column;
  text-align: center;
  background-color: #dcdcdc;
  padding: 30px;
}
.dashboard__btn {
  padding: 10px;
  font-size: 18px;
  margin-top: 10px;
  border: none;
  color: white;
  background-color: black;
}
.dashboard div {
  margin-top: 7px;
}

```

这里是`Dashboard.js`的布局:

```
import React, { useEffect, useState } from "react";
import { useAuthState } from "react-firebase-hooks/auth";
import { useNavigate } from "react-router-dom";
import "./Dashboard.css";
import { auth, db, logout } from "./firebase";
import { query, collection, getDocs, where } from "firebase/firestore";
function Dashboard() {
  const [user, loading, error] = useAuthState(auth);
  const [name, setName] = useState("");
  const navigate = useNavigate();
  const fetchUserName = async () => {
    try {
      const q = query(collection(db, "users"), where("uid", "==", user?.uid));
      const doc = await getDocs(q);
      const data = doc.docs[0].data();
      setName(data.name);
    } catch (err) {
      console.error(err);
      alert("An error occured while fetching user data");
    }
  };
  useEffect(() => {
    if (loading) return;
    if (!user) return navigate("/");
    fetchUserName();
  }, [user, loading]);
  return (
    <div className="dashboard">
       <div className="dashboard__container">
        Logged in as
         <div>{name}</div>
         <div>{user?.email}</div>
         <button className="dashboard__btn" onClick={logout}>
          Logout
         </button>
       </div>
     </div>
  );
}
export default Dashboard;

```

与其他组件不同，我们在这里还做了其他事情。我们正在检查身份验证状态。如果用户没有通过身份验证，我们会将用户重定向到登录页面。

我们还获取数据库，并根据用户的`uid`检索用户名。最后，我们在屏幕上渲染出一切。

最后，让我们将所有内容添加到路由器中。你的`App.js`应该是这样的:

```
import "./App.css";
import { BrowserRouter as Router, Route, Routes } from "react-router-dom";
import Login from "./Login";
import Register from "./Register";
import Reset from "./Reset";
import Dashboard from "./Dashboard";
function App() {
  return (
    <div className="app">
      <Router>
        <Routes>
          <Route exact path="/" element={<Login />} />
          <Route exact path="/register" element={<Register />} />
          <Route exact path="/reset" element={<Reset />} />
          <Route exact path="/dashboard" element={<Dashboard />} />
        </Routes>
      </Router>
    </div>
  );
}
export default App;

```

该应用程序功能齐全！

## 确保您的用户可以无缝登录。尝试使用 [LogRocket](https://www2.logrocket.com/react-performance-monitoring) 来监控 React 应用程序中的登录流程

调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，请尝试 LogRocket 。【T2![](img/f300c244a1a1cf916df8b4cb02bec6c6.png)[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://www2.logrocket.com/react-performance-monitoring)

LogRocket 就像是网络应用程序的 DVR，记录 React 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用程序的性能，报告客户端 CPU 负载、客户端内存使用等指标。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://www2.logrocket.com/react-performance-monitoring)。

## 下一步是什么？

一旦你完成了这个构建，我希望你能玩玩这个。接下来尝试添加脸书身份验证。GitHub 认证呢？我会说继续尝试代码，因为这是你练习和学习的方式。如果你只是不停地复制代码，你就不会理解 Firebase 的基本原理。

同样，如果你对代码有任何错误或疑问，这里有 [GitHub 库](https://github.com/atharvadeosthale/firebase-auth-article)供你参考。