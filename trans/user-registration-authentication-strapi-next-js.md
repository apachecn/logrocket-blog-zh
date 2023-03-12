# 用 Strapi 和 Next.js - LogRocket Blog 实现用户注册和认证

> 原文：<https://blog.logrocket.com/user-registration-authentication-strapi-next-js/>

本文将带您使用 [Strapi CMS](https://strapi.io/) 和 Next.js 创建一个应用程序，您可以在其中注册和验证用户并持久化会话。

## 设置 Next.js 应用程序

让我们从设置 Next.js 应用程序开始。最简单的设置方法是使用`create-next-app`包。

在您的终端中，运行`npx create-next-app next-app`，其中`next-app`是您喜欢的项目名称。

这将生成一个名为`next-app`的文件夹。然后，在您的终端屏幕中导航到该文件夹并运行`npm run dev`。您将看到应用程序运行在本地主机端口 3000 上。

## 设置 Strapi

就像 Next.js 一样，Strapi 也有一个 npm 包，可以轻松启动一个项目。

运行`npx create-strapi-app strapi-app`来创建一个 Strapi 项目。出现提示时，选择`Quickstart`选项。

安装完成后，导航到`strapi-app`文件夹并运行`npm run dev`。当你访问`localhost:1337`时，你会被要求创建一个管理员账户。这样做之后，您应该会被引导到 Strapi 控制面板。

## 在 Strapi 中注册用户

在 Strapi 的侧栏中，在**集合类型**下，您应该看到默认创建的`Users`。在这里，您可以手动创建用户并查看所有现有用户。

默认情况下，Strapi 的 api 提供端点来创建、获取和验证用户。

`/auth/local/register`是我们需要发送用户名、电子邮件和密码以注册用户的终点。

对于这个示例应用程序，我们将有`/register`和`/profile`路线，在这里您可以分别注册一个用户，然后查看用户的个人资料。登录屏幕将在主页上。

让我们从`<RegisterComponent/>`组件开始。

该组件返回一个非常基本的表单，用户在其中输入用户名、电子邮件和密码。

然后，`userData`对象保存这些信息，并将其发送到`/api/register`端点，这是我们将在下一个应用程序的后端部分创建的东西。注意，为了进行 API 调用，我使用了`axios`包，所以要确保你把它作为一个依赖项安装了。

`npm install --save axios`

一旦对`/api/register`的请求成功，我们就将应用程序路由到`/profile`以显示用户信息。我们稍后将创建`/profile`页面。

下面的文件是`/components/registerComponent.jsx`，我们将在`/pages/register.jsx`中使用它:

```
import { useState } from 'react';
import { useRouter } from 'next/router';
import axios from 'axios';

const RegisterComponent = () => {
  const router = useRouter();
  const [userData, setUserData] = useState({
    username: '',
    email: '',
    password: '',
  })

  const handleSubmit = async (e) => {
    e.preventDefault();
    try {
      await axios.post('/api/register', userData);
      router.replace('/profile');
    } catch (err) {
      console.log(err.response.data);
    }
  }

  const handleChange = (e) => {
    const { name, value } = e.target;
    setUserData({...userData, [name]: value });
  }

  return (
    <form onSubmit={handleSubmit}>
      <label>
        Username:
        <input type="text" name="username" onChange={e => handleChange(e)} />
      </label>
      <br />
      <label>
        Email:
        <input type="text" name="email" onChange={e => handleChange(e)} />
      </label>
      <br />
      <label>
        Password:
        <input type="password" name="password" onChange={e => handleChange(e)} />
      </label>
      <br />
      <button>Register</button>
    </form>
  )
}

export default RegisterComponent;

```

现在在`pages`文件夹下创建`register.jsx`，并返回`<RegisterComponent/>`。

```
import RegisterComponent from "../components/registerComponent";
const Register = () => (
  <RegisterComponent />
)
export default Register;

```

我们需要做的下一件事是创建`/api/register`端点。

在`/pages`下，你会找到`/api`文件夹。在那个文件夹中，创建`register.js`。这样，当我们在 Next.js 客户机中向`/api/register`发出请求时，它将由这个文件处理。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
import axios from 'axios';
import { setCookie } from 'nookies'

export default async (req, res) => {
  const { username, password, email } = req.body;

  try {
    const response = await axios.post('http://localhost:1337/auth/local/register', {
      username,
      email,
      password,
    })

    setCookie({ res }, 'jwt', response.data.jwt, {
      httpOnly: true,
      secure: process.env.NODE_ENV !== 'development',
      maxAge: 30 * 24 * 60 * 60,
      path: '/',
    });

    res.status(200).end();
  } catch (e) {
    res.status(400).send(e.response.data.message[0].messages[0]);
  }
}

```

如前所述，注册用户的端点是`/auth/local/register`。因为 Strapi 运行在`localhost:1337`上，我们向`[http://localhost:1337/auth/local/register](http://localhost:1337/auth/local/register)`发送一个请求，以及从请求体中检索到的用户名、电子邮件和密码数据。

注意，如果您将 Strapi 应用程序部署到远程服务器，那么您需要相应地替换基本 URL。将基本 URL 存储在环境变量中，然后使用它而不是硬编码 URL，这是一个很好的做法。然而，为了简单起见，我将在整篇文章中使用`localhost:1337`。

POST 请求返回的响应包含一个 JWT 令牌。这个令牌是特定于该用户的，应该安全地存储，以便自动对用户进行身份验证。

`nookies`包是在 Next.js 应用程序中处理 cookies 的助手函数的集合。

`setCookie`函数将响应对象作为第一个参数。第二个参数是 cookie 的名称，可以是您选择的任何名称。

第三个参数是 cookie 的值。在我们的例子中，这是从请求响应返回的`JWT`标记，作为最后一个参数，我们可以传递一个带有选项的对象。

`httpOnly`标志防止客户端——浏览器上运行的 javascript 访问 cookie，以便您可以保护 cookie 免受可能的[跨站脚本](https://blog.logrocket.com/security-for-fullstack-web-developers-part-3-aaf81da57acb/) (XSS)攻击。

`secure`标志确保 cookie 只通过安全的`https`连接传输。因为 localhost 不是一个`https`连接，所以如果我们运行应用程序的环境是`development`，我们将它设置为`false`。

`maxAge`确定 cookie 的有效秒数。在本例中，它被设置为 30 天。

`path`确定 cookie 应该在哪个路径有效。它被设置为`/`以便使 cookie 可用于所有路径。

请注意，如果请求失败，我们将发送一条消息。从`e.response.data.message[0].messages[0]`获取的该消息包含关于请求失败原因的有用信息。这可能是一个无效的电子邮件，或者用户名已经被使用，等等。

因此，在注册失败的情况下，此信息可用于显示适当的错误消息。

为了简单起见，我们不在客户端处理这个错误消息。我们只是简单地将它记录下来，显示在浏览器控制台中。

## 在 Strapi 中创建用户配置文件页面

这是`/profile`路线，显示用户数据，如用户名和电子邮件。可以直接访问该路径，或者，用户可以在登录或注册时被路由到该路径。

因为这个页面需要使用 cookie 来认证用户，并且因为我们已经在服务器端用`httpsOnly`标志设置了 cookie，所以我们也需要读取服务器中的 cookie。

`getServerSideProps`是一个 Next.js 函数，它根据每个请求在服务器上运行。在这个函数中，我们可以解析 cookie 来访问 JWT 令牌，然后请求 Strapi 获取用户数据。然后，我们可以从函数中返回该数据，该函数将该数据公开为页面的道具。

`/pages/profile.jsx`的内容如下:

```
import { useRouter } from 'next/router';
import axios from 'axios';
import nookies from 'nookies';

const Profile = (props) => {
  const router = useRouter();
  const { user: { email, username } } = props;

  const logout = async () => {
    try {
      await axios.get('/api/logout');
      router.push('/');
    } catch (e) {
      console.log(e);
    }
  }

  return (
    <div>
      <div>Username: {username}</div>
      <div>Email: {email}</div>
      <button onClick={logout}>Logout</button>
    </div>
  )
}

export const getServerSideProps = async (ctx) => {
  const cookies = nookies.get(ctx)
  let user = null;

  if (cookies?.jwt) {
    try {
      const { data } = await axios.get('http://localhost:1337/users/me', {
        headers: {
          Authorization:
            `Bearer ${cookies.jwt}`,
          },
      });
      user = data;
    } catch (e) {
      console.log(e);
    }
  }

  if (!user) {
    return {
      redirect: {
        permanent: false,
        destination: '/'
      }
    }
  }

  return {
    props: {
      user
    }
  }
}

export default Profile;

```

我们先来关注一下`getServerSideProps`函数。我们再次使用`nookies`包从上下文对象(ctx)中获取 cookies。我们设置的 cookie 的名称是`jwt`，因此检查`cookies.jwt`的存在。

如果这个特定的 cookie 存在，那么我们向本地 Strapi 服务器的`/users/me`端点发送一个请求，其中的`Authorization`头包含 JWT 令牌，以获取用户信息。

如果 cookie 不存在或者 JWT 令牌无效，`user`变量将停留在`null`，我们将页面重定向回`/`，这是带有登录屏幕的主页。否则，我们在`props`对象中返回`user`，然后它在我们导出的`Profile`函数中成为可用的道具。

`Profile`函数返回一个非常基本的标记，其中显示了用户名和电子邮件，以及一个注销按钮，该按钮调用`logout`函数。

这个函数向`/api/logout`端点发送一个请求，以便删除 cookie，然后将页面路由到`/`。

下面是`/pages/api/logout.js`:

```
import { destroyCookie } from 'nookies'

export default async (req, res) => {
  destroyCookie({ res }, 'jwt', {
    path: '/',
  });

  res.status(200).end();
}

```

## 登录用户

这里是`/pages/index.jsx`的内容，这是主页，`<LoginComponent>`和一个注册按钮在这里，注册按钮将页面路由到`/register`路径。

```
import { useRouter } from 'next/router';
import axios from 'axios';
import nookies from 'nookies';
import LoginComponent from '../components/loginComponent';

const Home = () => {
  const router = useRouter();
  const goToRegister = () => {
    router.push('/register');
  }

  return (
    <div>
      <LoginComponent />
      <button onClick={goToRegister}>Register</button>
    </div>
  )
}

export const getServerSideProps = async (ctx) => {
  const cookies = nookies.get(ctx)
  let user = null;

  if (cookies?.jwt) {
    try {
      const { data } = await axios.get('http://localhost:1337/users/me', {
        headers: {
          Authorization:
            `Bearer ${cookies.jwt}`,
          },
      });
      user = data;
    } catch (e) {
      console.log(e);
    }
  }

  if (user) {
    return {
      redirect: {
        permanent: false,
        destination: '/profile'
      }
    }
  }

  return {
    props: {}
  }
}

export default Home;

```

正如我们在`/pages/profile.jsx`中所做的，我们再次使用`getServerSideProps`函数来获取 cookie，然后检查用户的存在。

如果用户确实存在，那么我们重定向到`/profile`路由。如果没有，我们返回一个空的`props`对象，并停留在相同的路线上来渲染`<LoginComponent/>`，它有以下内容:

```
import { useState } from 'react';
import { useRouter } from 'next/router';
import axios from 'axios';

const LoginComponent = () => {
  const router = useRouter();
  const [userData, setUserData] = useState({
    identifier: '',
    password: '',
  });

  const handleSubmit = async (e) => {
    e.preventDefault();
    try {
      await axios.post('/api/login', { ...userData });
      router.push('/profile');
    } catch (err) {
      console.log(err.response.data);
    }
  }

  const handleChange = (e) => {
    const { name, value } = e.target;
    setUserData({...userData, [name]: value })
  }

  return (
    <div>
      <form onSubmit={handleSubmit}>
        <label>
          Email:
          <input type="text" name="identifier" onChange={e => handleChange(e)} />
        </label>
        <br />
        <label>
          Password:
          <input type="password" name="password" onChange={e => handleChange(e)} />
        </label>
        <br />
        <button>Login</button>
      </form>
    </div>
  )
}

export default LoginComponent;

```

该组件呈现一个表单来获取电子邮件和密码。当提交表单时，向`/api/login`发出 POST 请求，页面被重定向到`/profile`路由。

下面你可以看到`/api/login.js`的内容

```
import axios from 'axios';
import { setCookie } from 'nookies'

export default async (req, res) => {
  const { password, identifier } = req.body;

  try {
    const postRes = await axios.post('http://localhost:1337/auth/local', {
      identifier,
      password,
    })

    setCookie({ res }, 'jwt', postRes.data.jwt, {
      httpOnly: true,
      secure: process.env.NODE_ENV !== 'development',
      maxAge: 30 * 24 * 60 * 60,
      path: '/',
    });

    res.status(200).end();
  } catch (e) {
    res.status(400).send(e.response.data.message[0].messages[0]);
  }
}

```

`/auth/local`是让用户登录的 Strapi 端点。发送到这个端点的对象应该有`identifier`和`password`键。

正如我们在`/api/register.js`中所做的，我们还设置了一个具有相同名称和选项的 cookie 来持久化用户会话。

## 结论

在本文中，我们演示了如何使用 Next.js 作为全栈应用程序来创建具有多个路由的用户界面，并创建 api 端点来与 Strapi 的 API 进行通信，以便注册和获取用户信息。为了持久化用户会话，还演示了在 Next.js 中在服务器端设置和获取 cookies。

## [LogRocket](https://lp.logrocket.com/blg/nextjs-signup) :全面了解生产 Next.js 应用

调试下一个应用程序可能会很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪状态、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/nextjs-signup)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png)](https://lp.logrocket.com/blg/nextjs-signup)[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/nextjs-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你的 Next.js 应用上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用程序的性能，报告客户端 CPU 负载、客户端内存使用等指标。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

让您调试 Next.js 应用的方式现代化— [开始免费监控](https://lp.logrocket.com/blg/nextjs-signup)。