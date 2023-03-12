# 用苗条、电子和重载博客构建跨平台桌面应用

> 原文：<https://blog.logrocket.com/building-cross-platform-desktop-apps-with-svelte-electron-and-reloadly/>

## 介绍

当构建应用程序时，拥有跨不同平台的可重用性选项是非常好的。电子为网络开发者提供了一种在跨平台桌面应用上拓展技能的方式。 [Svelte](https://svelte.dev/) 是一个最近被证明适用于不同环境的框架。

在本文中，我们将与 Svelte 和 Electron 一起构建一个移动充值应用程序。以前使用 JavaScript 和 Svelte 的经验将有助于理解这篇文章。

## 先决条件

这些是您机器上应该有的工具:

*   [节点](https://nodejs.org/en/):运行在 V8 引擎上的后端 JavaScript 运行时环境，在 web 浏览器之外执行 JavaScript 代码
*   npm :节点的包管理器

## 苗条身材入门

为了开始，我们将启动一个苗条的项目，因为这将作为我们进行大部分改变的界面。

在您的终端中，使用`npx degit svelte/template`命令创建您的新的苗条项目:

```
// Add your preferred project name to the command:

npx degit sveltejs/template reloadly-app-svelte

// When complete, navigate to the newly created app:

cd reloadly-app-svelte

// Then install the necessary dependencies:

npm install 

```

在这个阶段，你的苗条项目应该有一个类似这样的文件夹结构:

![Screenshot of folder structure in Svelte](img/e04aa17b29c29988c7af1f22a974e01e.png)

一旦你完成安装苗条，下一步是安装电子。导航到您的终端，在您新创建的苗条项目中，使用`npm`安装电子:

```
npm install electron --save-dev

```

完成后，在你的苗条项目中创建一个`index.js`文件，在这个文件中，指出每当你的电子应用程序启动时将打开的桌面窗口:

```
// index.js

const { app, BrowserWindow } = require("electron");
const path = require("path");
app.on("ready", () => {
  const mainWindow = new BrowserWindow();
  mainWindow.loadFile(path.join(__dirname, "public/index.html"));
  mainWindow.webContents.openDevTools();
});

```

您的应用程序需要进行一些配置:

*   让`index.js`成为你的应用的入口
*   配置 [Rollup](https://rollupjs.org/guide/en/) (一个在你创建你的苗条项目时安装的模块捆绑器)来控制你的应用程序的构建过程
*   设置电子启动您的应用程序

为了实现这些，编辑您的`package.json`文件以包含下面的代码示例:

```
{
  ...
  "main": "index.js"
  "scripts": {
    "build": "rollup -c",
    "dev": "rollup -c -w",
    "start": "electron .",
  }
}

```

上线前，确保已检查以下内容:

*   在`index.js`中，主窗口的路径是`public/index.html`
*   在`public/index.html`中，样式表和脚本链接是`global.css`、`build/bundle.css`和`build/bundle.js`

一旦您验证了这一点，您就可以通过在终端上运行`npm run dev`命令来启动您的应用程序。您的应用程序应该通过以下界面启动:

![Screenshot of Svelte app reading "hello world"](img/6987898e5791803038e13541cb14ddeb.png)

## 获取并保护您的访问令牌

您正在构建的移动充值应用程序的一个关键部分在于以苗条的方式发出 HTTP 请求，并在您的电子应用程序上显示它们的响应。对于 API 来说， [Reloadly](https://www.reloadly.com/) 是一个可行的选项，因为它提供了为手机号码充值的端点。

要向 Reloadly 的 API 发出安全的 HTTP 请求，您需要客户端凭证和访问令牌。凭证可以从 Reloadly 的[仪表板](https://www.reloadly.com/registration)访问，然后用于对访问令牌进行 POST 请求。提出这一请求时有很多选择，但 [Reqbin](https://reqbin.com/) 因简洁而更受青睐:

![Screenshot of reloadly request](img/267da563edc4401bc8478c782cb9965a.png)

上图中发生了什么？

*   向以下 URL 发出了发布请求:`[https://auth.reloadly.com/oauth/token](https://auth.reloadly.com/oauth/token)`
*   这个请求的主体将客户端凭证(`client-id`和`client-secret`)、`grant-type`和`audience`作为参数
*   此请求的响应包含访问令牌、其权限以及过期前的持续时间(以秒为单位)

为了保护您的访问令牌，您需要将它存储在与您的代码库不同的环境中。这可以通过将它保存到一个`.env`文件中，然后安装 [dotenv](https://www.npmjs.com/package/dotenv) 来将这个文件链接到你的代码库，而不暴露你的访问令牌。

首先，在您的 Svelte 项目中创建`.env`文件，并保存您的访问令牌:

```
/ .env

ACCESS_TOKEN = Bearer eyJraWQiOiIwMDA1YzFmMC0xMjQ3LTRmNmUtYjU2ZC1jM2ZkZDVmMzhhOTIiLCJ0eXAiOiJKV1QiLCJ5jZSByZWFkLXByZXBhaWQtY29tbWlzc2lvbnMiLCJleHAiOjE2MjE3Njc1NzQsImh0dHBzOi8vcmVsb2FkbHkuY29tL2p0aSI6ImZmYjgyMGEyLTE1MmEtNGYhbGciOiJIUzI1NiJ9.eyJzdWIiOiI2ODkzIiwiaXNzIjoiaHR0cHM6Ly9yZWxvYWRseS5hdXRoMC5jb20vIiwiaHR0cHM6Ly9yZWxvYWRseS5jb20vc2FuZGJveCI6ZmFsc2UsImh0dHBzOi8vcmVsb2FkbHkuY29tL3ByZXBhaWRVc2VIiwiYXVkIjoiaHR0cHM6Ly90b3B1cHMtaHMyNTYucmODkzIiwiZ3R5IjoiY2xpZW50LWNyZWRlbnRpYWxzGY4ZSIsImlhdCI6MTYxNjU4MzU3NCwianRpIjoiYjVhOTMyNGUtODFiOC00OGIyLWI5MWMtYzZVsb2FkbHkuY29tIiwibmJmIjoxNjE2NTgzNTc0LCJhenAiOiI2ODkzIiwic2NvcGUiOiJzZW5kLXRvcHVwcyByZWFkLW9wZXJhdG9ycyByZWFkLXByb21vdGlvbnMgcmVhZC10b3B1cHMtaGlzdG9yeSByZWFkLXByZXBhaWQtYmFsYW0ZC1hMmY4LWEwMTcwZDFiOySWQiOiI2kZWRiNDQ1YmRjIn0.z6_BQMwHbr_breyDm4YgRTiu3RdhJXKmu8fnlHrBHc4

```

接下来，在`rollup/plugin-replace`旁边安装 dotenv，这是一个替换文件中目标字符串的 Rollup 插件:

```
npm install --save-dev dotenv @rollup/plugin-replace

```

之后，更新您的`rollupconfig.js`文件以导入已安装的包并附加`.env`配置:

```
// rollup.config.js

import {config} from 'dotenv';
import replace from '@rollup/plugin-replace';

const production = !process.env.ROLLUP_WATCH;

export default {
    plugins: [
      replace({
        // stringify the object       
        __myapp: JSON.stringify({
          env: {
            isProd: production,
            ...config().parsed // attached the .env config
          }
        }),
      }),
    ],
  };

```

一旦完成，您就可以继续构建您的 API 请求来进行充值。

## 构建 Reloadly 的 API 请求

导航到您的`App.svelte`文件，使用 Reloadly 的 API 发出 POST 请求。

首先，定义请求所需的所有参数:

```
// .src/App.svelte 

<script>
    const accessToken = __myapp.env.ACCESS_TOKEN  // fetches your access token
    let results;
    let operatorId = '341'
    let recipientPhone = {
        'countryCode' : 'NG',
        'number' : ''
    }
    let amount = ''
    let customIdentifier = ''

    let headers = {
        'Content-Type' : 'application/json',
        'Authorization' :  accessToken 
    }
</script>

```

为了处理您将从 API 获得的请求和呈现数据的响应，您需要在您的苗条应用程序中定义一些函数:

*   `handleClick()`:这包装了 POST 请求，并将其执行设置为按钮
*   `hold(customIdentifier)`:这确保了在所有输入都被填充之前，应用程序不会发出请求

然后请求重载:

```
// .src/App.svelte

<script>
...
    const doPost = async () => {
        const response = await fetch('https://topups.reloadly.com/topups', {
            method: 'POST',
            body: JSON.stringify({
                recipientPhone,
                amount,
                operatorId,
                customIdentifier
            }),
            headers: headers
        })
        if (response.status === 200) {
      return await response.json();
    } else {
      throw new Error(response.statusText);
    }   
  }
</script>

```

一旦收到成功的响应，就使用 HTML 和 Svelte 元素的组合来表示数据。

首先，创建输入标签和一个按钮来处理请求:

```
// . src/App.svelte

<div class = 'request'>
    <h1>RECHARGE ON THE GO  &#127757;</h1> <!-- &#127757 is an emoji -->
    <p> Phone number:   <input type=number bind:value={recipientPhone.number} /> </p>
    <p> Amount (NGN): <input type=number bind:value={amount} /> </p>
    <p> Transaction Reference: <input bind:value={customIdentifier} /> </p>
    <button disabled='{hold(customIdentifier)}' on:click={handleClick}>
      RECHARGE
    </button>
</div>

```

然后创建一个简单的逻辑/await 块来处理响应:

```
// .src/App.svelte

<div class = 'response'>
    {#await results }
        <p>Loading...</p>
    {:then res}
        <p>{res ? `Transaction ID: ${res.operatorTransactionId}` : ''}</p>
        <p>{res ? `Custom Identifier: ${res.customIdentifier}` : ''}</p>
        <p>{res ? `Operator Name: ${res.operatorName}` : ''}</p>
        <p>{res ? `Trannsaction Date: ${res.transactionDate}` : ''}</p>  
    {:catch res}
        <p>Error message: {res ? `${res.message}` : ''}</p>
    {/await}
</div>

```

应用程序的样式类似于默认 CSS。这里有一个例子:

```
// .src/App.svelte

<style>
   .request {
    padding: 20px;
    background-color: #242D3D;
    color: #1EBAD5;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    align-items: center;
    text-align: center;
  }
</style>

```

此时，您的应用程序应该是完整的。通过运行`npm run dev`启动它，并为一个手机号码充值:

 [https://www.youtube.com/embed/wKjM45-Sscw?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent](https://www.youtube.com/embed/wKjM45-Sscw?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent)

视频

## 电子分布组态

现在，您已经完成了桌面应用程序的构建。最后的程序是分发它。要实现这一点，你需要安装`electron-builder`，这是一个打包并构建一个随时可以发布的电子应用的工具。

导航到您的终端并安装此工具:

```
npm install electron-builder --save-dev

```

接下来，在您的应用程序的`package.json`文件中，添加分发配置以包含`electron-builder`:

```
scripts: {
  ...
  "dist": "npm run build && electron-builder"
}

```

然后在你的终端中运行`npm run dist`。这将构建您的应用程序，并创建您的应用程序的可分发版本:

![Screenshot of folders in Svelte](img/ab0dd3252702777a0c7e73ea514a13ed.png)

## 结论

使用不同的框架不仅是提高技能的好方法，更好的方法是知道不同的选项存在，并且当组合起来时，可以用来创建惊人的应用程序。你可以在 [GitHub 上查看这个桌面应用的源代码。](https://github.com/fullstackmafia/reloadly-svelte-app)