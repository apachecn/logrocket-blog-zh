# 在 NestJS 中实现安全的单点登录认证

> 原文：<https://blog.logrocket.com/implement-secure-single-sign-on-nestjs-google/>

[NestJS](https://docs.nestjs.com/) 是一个进步的 [Node.js 框架](https://blog.logrocket.com/tag/node/)，用于构建高效、可靠、可伸缩的服务器端应用。在本教程中，我们将学习如何在 NestJS 后端服务中实现安全的 Google 单点登录(SSO ),并将其连接到 React 前端应用程序，以便在一个简单的 web 应用程序中对用户进行身份验证。

*向前跳转:*

## 在 NestJS 中设置`client`和`server`文件夹

首先，我们将创建前端`client`和后端`server`文件夹。接下来，我们将导航到`client`文件夹，并通过在终端中运行命令`npx create-react app react-nestjs`来初始化 React 应用程序。这将为我们的前端应用程序创建所有必要的文件。

对于后端，我们将在终端中运行`nest new server`命令。这将搭建一个新的 NestJS 项目目录，并用初始核心 NestJS 文件和支持模块填充该目录。

文件夹目录应该如下所示:

![Nest.js Project Directory](img/7c374efcf4424ef4ab519bdae6a3cebd.png)

然后，我们将导航到`client`并运行`npm run start`来启动并运行我们的前端应用程序。这将启动一个本地开发服务器作为`localhost:3000`。

### 启动开发服务器

为了开始我们的后端开发服务器，我们将导航到`server`文件夹并运行`npm run start`。因为我们已经使用了`localhost:3000`，我们将把后端的端口改为`main.ts`文件中的`localhost:8080`:

```
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';

async function bootstrap() {
 const app = await NestFactory.create(AppModule);
 await app.listen(8080);
}
bootstrap();

```

`npm run start`将在`localhost:8080`为我们启动开发服务器，并显示“Hello，World！”来自`app.controller.ts`的文件。

当开发服务器在`localhost:8080`启动时， [`app.controller.ts`](https://blog.logrocket.com/understanding-controllers-routes-nestjs/) 使用`app.service.ts`中定义的`getHello`方法返回文本。当你检查`app.controller.ts`时，你应该看到这个:

```
import { Controller, Get, Post, Body } from '@nestjs/common';
import { AppService } from './app.service';
@Controller()
export class AppController {
 constructor(private readonly appService: AppService) {}

 @Get()
 getHello(): string {
   return this.appService.getHello();
 }

```

这是`app.service.ts`文件中的结果:

```
import { Injectable } from '@nestjs/common';
@Injectable()
export class AppService {
  getHello(): string {
   return 'Hello World!';
 }

```

你可以在 nestj[这里](https://docs.nestjs.com/controllers)阅读更多关于控制器的内容。

## 在 Google 云平台上创建一个安全的 SSO 项目

搭建好我们的应用程序后，我们将在[谷歌云平台](https://cloud.google.com/)上建立一个新项目。首先，我们将导航到[控制台](https://console.cloud.google.com)并通过选择左上角的**项目栏**创建一个新项目。然后，点击**新建项目**并输入您的项目详情。之后，在搜索栏中搜索凭证并选择 **API 和服务。**

然后，在创建凭据之前，我们将通过单击**配置同意屏幕**来配置内容屏幕。接下来，选择**C**组态画面，输入 app 信息。接下来，我们将选择**保存并继续**。然后点击**凭证**，**创建凭证**，选择 **OAuth 客户端 ID** 。

这将提示我们创建 OAuth 客户端 ID 的页面，在这里我们将选择 **Web 应用程序**作为**应用程序类型**。然后，我们可以添加 JavaScript 源和重定向 URIs 作为`locahost:3000`。我们还将添加第二个值为 [http://localhost](http://localhost) 的 URI。最后，点击 **Create** 获取客户端 ID 和客户端密码:

![NestJS Secure Sign On With Google](img/5372db6348d20ca8e427c354b2fc9bb4.png)

## 为 Google 登录配置 React OAuth 2.0

为了建立 [Google Auth 库](https://blog.logrocket.com/guide-adding-google-login-react-app/)，我们将使用 [React OAuth 2.0](https://www.npmjs.com/package/@react-oauth/google) 。首先，让我们在终端中运行命令`npm i @react-oauth/google`。创建我们的客户端 ID 后，我们将使用`GoogleOAuthProvider`包装主页。

然后，在`App.js`中，我们将清理引导代码，并用以下代码替换它:

```
import "./App.css";
import { GoogleOAuthProvider, GoogleLogin } from "@react-oauth/google";

function App() {
 return (
   <div className="App">
         <h1>Welcome</h1>
         <GoogleOAuthProvider      clientId="192710632585-6dvq7283tr2pug31h5i6ii07fgdeu6q3.apps.googleusercontent.com
"
         >
           <GoogleLogin
             onSuccess={async (credentialResponse) => {
             console.log(credentialResponse);
                        }}
             onError={() => {
               console.log("Login Failed");
             }}
           />
         </GoogleOAuthProvider>
   </div>
 );
}

export default App;

```

这里，我们使用了`GoogleOAuthProvider`和`GoogleLogin`API 来实现 Google SSO 功能。然后，我们在控制台中记录了来自`GoogleLogin`的凭证响应。我们也把`clientId`传给了`GoogleOAuthProvider`。

### 使用`clientId`

现在，我们将使用电子邮件登录，并检查控制台中记录的凭据响应。在控制台中，我们应该有一个`clientId`响应对象:

![Example of NestJS ClientID Response Object](img/1e05350080922ae7a15d89ab4a9d5e3b.png)

最后，我们必须将传递给`GoogleOAuthProvider`的`clientId`存储在一个环境变量文件中。因为它是一个秘密的 ID，所以不应该直接暴露给浏览器，也不应该和其他代码一起放入存储库。

接下来，在`client`文件夹中创建一个`.env`文件，并将`.env`添加到`.gitignore`中，这样该文件在被推送到存储库时会被忽略。在`.env`中，创建一个`REACT_APP_GOOGLE_CLIENT_ID`变量并将其传入`clientId`原始值:

```
# google client id
REACT_APP_GOOGLE_CLIENT_ID=192710632585-6dvq7283tr2pug31h5i6ii07fgdeu6q3.apps.googleusercontent.com

```

值得注意的是，变量名必须以关键字`REACT_APP`为前缀。

然后，在`App.js`中，我们将把`process.env.REACT_APP_GOOGLE_CLIENT_ID`作为`clientId`的值传递给 React 来读取 。环境 :

```
import "./App.css";
import { GoogleOAuthProvider, GoogleLogin } from "@react-oauth/google";
function App() {
 return (
   <div className="App">
         <h1>Welcome</h1>
         <GoogleOAuthProvider
           clientId={process.env.REACT_APP_GOOGLE_CLIENT_ID}
         >
           <GoogleLogin
             onSuccess={async (credentialResponse) => {
               console.log(credentialResponse);
                         }}
             onError={() => {
               console.log("Login Failed");
             }}
           />
         </GoogleOAuthProvider>
       </div>
 );
}

export default App;

```

## 使用 Zustand 为您的 NestJS 项目进行状态管理

我们将使用 [Zustand](https://blog.logrocket.com/managing-react-state-zustand/) 进行全球状态管理。但是，你可以使用 [Redux](https://blog.logrocket.com/understanding-redux-tutorial-examples/) 、[反冲](https://recoiljs.org/)，或者任何你选择的状态管理。要开始，运行`npm install zustand`来安装该包:

然后，我们将创建一个`hook`文件夹，并创建`useStore.js`存储文件:

```
import create from "zustand";

export const useStore = create((set) => ({
 // get the data from local storage
 authData: localStorage.getItem("authData")
   ? JSON.parse(localStorage.getItem("authData"))
   : null,

 setAuthData: (authData) => {
   localStorage.setItem("authData", JSON.stringify(authData));
   set({ authData });
 },
}));

```

接下来，每当用户登录时，我们将使用`Axios.post`方法发出一个 [Axios POST](https://blog.logrocket.com/how-to-make-http-requests-like-a-pro-with-axios/) 请求。

首先，安装带有`npm install axios`的 Axios，并在选择`GoogleLogin`时发出`Post`请求:

```
import "./App.css";
import { GoogleOAuthProvider, GoogleLogin } from "@react-oauth/google";
import axios from "axios";
import { useStore } from "./hooks/useStore";

function App() {
 const setAuthData = useStore((state) => state.setAuthData);
 return (
   <div className="App">
              <h1>Welcome</h1>
         <GoogleOAuthProvider
           clientId={process.env.REACT_APP_GOOGLE_CLIENT_ID}
         >
           <GoogleLogin
             useOneTap={true}
             onSuccess={async (credentialResponse) => {
               console.log(credentialResponse);
               const { data } = await axios.post(
                 "http://localhost:8080/login",
                                );
               localStorage.setItem("AuthData", JSON.stringify(data));
               setAuthData(data);
             }}
             onError={() => {
               console.log("Login Failed");
             }}
           />
         </GoogleOAuthProvider>   
      </div>
 );
}

export default App;

```

记住，我们将后端的端口设置为`8080`，所以我们稍后将在后端请求`8080/login`。然后，我们将来自后端服务器的数据存储在本地存储器中，并将`setAuthData`设置为我们的后端服务将返回的数据。

## 构建 NestJS 文件夹

接下来，我们需要在之前创建的`server`中设置我们的 NestJS 文件夹:

![NestJS SSO Server Folder](img/b3672d410694709bdddc190f6b36f3bb.png)

为了确认我们成功地将 NestJS 连接到前端应用程序，我们将使用`POST HTTP`请求方法返回`app.controller.ts`中的`React x NestJS`:

```
import { Controller, Post } from '@nestjs/common';
import { AppService } from './app.service';
@Controller()
export class AppController {
 @Post('/login')
 login: string {
   return 'Reaxt x NestJS';
 }
}

```

现在，让我们登录前端应用程序，检查浏览器的本地存储:

![Checking the NestJS Browser Storage](img/0e026ab041e605a05599ce2417ad16a8.png)

一旦我们成功登录，我们就可以以文本形式发送响应。从这里，我们将创建一个模式并发送`name`、`email`、`picture`用户对象。

记住，对于 NestJS 和 React 之间的连接工作，我们需要在 NestJS `main.ts`文件中启用`cors`。否则，调用后端端点时会出现错误:

```
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';

async function bootstrap() {
 const app = await NestFactory.create(AppModule);
 //enable cors
 app.enableCors({
   origin: 'http://localhost:3000',
 });
 await app.listen(8080);
}
bootstrap();

```

因为我们的端口原点是`localhost:3000`，所以我们会在`enableCors`方法中将其传递给原点。

## 在 NestJS 中实现 Google OAuth

我们将从服务器端的 [Google Auth Library: Node.js 客户端](https://cloud.google.com/nodejs/docs/reference/google-auth-library/latest)开始。要安装这个包，运行`npm i google-auth-library`并导入`OAuth2Client`来检索访问令牌，然后刷新令牌。

接下来，我们将初始化一个新的`OAuth2Client`实例，传递`clientId`，并在设置 Google 云平台时生成秘密。

同样，我们不想在代码中简单地传递值，所以我们需要为服务器端实现`.env`。首先，创建一个`.env`并将其包含在`.gitignore`中。然后，在`.env`文件中添加两个变量:

```
GOOGLE_CLIENT_ID=192710632585-6dvq7283tr2pug31h5i6ii07fgdeu6q3.apps.googleusercontent.com
GOOGLE_CLIENT_SECRET=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

```

现在，我们需要从项目根目录加载并解析`.env`。我们还将把来自`.env`的键值对与环境变量合并，并将结果存储在一个通过`ConfigService`访问的私有结构中。为此，我们将在内部安装使用`dotenv`的`npm i --save @nestjs/config`。

然后，我们将把`nest/config`中的`ConfigModule`导入到根`AppModule`中，并使用`.forRoot()`静态方法控制它的行为:

```
import { Module } from '@nestjs/common';
import { AppController } from './app.controller';
import { AppService } from './app.service';
import { ConfigModule } from '@nestjs/config';

@Module({
 imports: [
   ConfigModule.forRoot({
     isGlobal: true,
     envFilePath: '.env',
   }),
  ],
 controllers: [AppController],
 providers: [AppService],
})
export class AppModule {}

```

现在，我们可以从项目的任何地方读取我们的`.env`变量。接下来，我们将在`app.controller.ts`中实现 Google 认证:

```
import { Controller, Get, Post, Body } from '@nestjs/common';
import { AppService } from './app.service';
import { OAuth2Client } from 'google-auth-library';

const client = new OAuth2Client(
 process.env.GOOGLE_CLIENT_ID,
 process.env.GOOGLE_CLIENT_SECRET,
);

@Controller()
export class AppController {
 @Post('/login')
 async login(@Body('token') token): Promise<any> {
   const ticket = await client.verifyIdToken({
     idToken: token,
     audience: process.env.GOOGLE_CLIENT_ID,
   });
    // log the ticket payload in the console to see what we have
   console.log(ticket.getPayload());
    }
}

```

这里，我们将`idToken`和`audience`传递给`Body`装饰器，相当于`req.body`。`audience`是`clientId`，而`token`将来自我们前端的`credentialObject`。

我们将把`App.js`中的`token`作为`credentialObject.credentials`与`Body`一起传递:

```
<GoogleLogin
             useOneTap={true}
             onSuccess={async (credentialResponse) => {
               console.log(credentialResponse);
               const { data } = await axios.post(
                 "http://localhost:8080/login",
                 {
                   // pass the token as part of the req body
                   token: credentialResponse.credential,
                 }
               );
               localStorage.setItem("AuthData", JSON.stringify(data));
               setAuthData(data);
             }}
             onError={() => {
               console.log("Login Failed");
             }}
           />

```

当我们成功登录时，我们的终端中应该有这个作为`ticket.getPayload() data`:

![NestJS SSO Terminal](img/9a811b5b83323aa26e2f47c662225a72.png)

## 将 NestJS SSO 项目数据保存到 MongoDB

对于本教程，我们将使用 [MongoDB](https://www.mongodb.com/) 在数据库中保存用户的信息。首先，注册或登录一个 [MongoDB 云服务账户](https://www.mongodb.com/cloud)。然后，创建一个新的自由层群集:

![Adding NestJS to the MongoDB Database](img/7616bfccb4ec8b40f6125aa4ec92b455.png)

![Creating the NestJS Shared Cluster](img/b4fbf792eae3494686ce57a931092035.png)

### 构建用户模式

创建集群后，连接您的应用程序以获得您的 MONGOURI **。**复制 URI 并粘贴到`server`的`.env`文件中。然后，我们将把我们的数据库与`app.module.ts`中的 NestJS 应用程序连接起来。

我们将通过创建`user.schema.ts`文件来创建一个用户模式。你可以在 NestJS [这里](https://docs.nestjs.com/techniques/mongodb)阅读更多关于连接 MongoDB 数据库的内容:

```
MONGO_URI=mongodb+srv://Taofiq:[email protected]/?retryWrites=true&w=majority

import { Prop, Schema, SchemaFactory } from '@nestjs/mongoose';
import { Document } from 'mongoose';

export type UserDocument = User & Document;

@Schema()
export class User {
 @Prop()
 name: string;

 @Prop()
 email: string;

 @Prop()
 image: string;
}

export const UserSchema = SchemaFactory.createForClass(User);

```

在我们的模式中，当向`Post`发出登录请求时，我们将发送`name`、`email`和`image`道具作为响应。

现在，在 app 模块中，我们将配置 MongoDB 连接:

```
import { Module } from '@nestjs/common';
import { AppController } from './app.controller';
import { AppService } from './app.service';
import { ConfigModule } from '@nestjs/config';
import { MongooseModule } from '@nestjs/mongoose';
import { User, UserSchema } from './user.schema';

@Module({
 imports: [
   ConfigModule.forRoot({
     isGlobal: true,
     envFilePath: '.env',
   }),
   MongooseModule.forRoot(process.env.MONGO_URI),
   MongooseModule.forFeature([{ name: User.name, schema: UserSchema }]),
 ],
 controllers: [AppController],
 providers: [AppService],
})
export class AppModule {}

```

## 使用来自 NestJS 的数据应用前端安全单点登录

接下来，我们将创建一个应用程序服务来创建新用户并让现有用户登录应用程序:

```
import { Injectable } from '@nestjs/common';
import { InjectModel } from '@nestjs/mongoose';
import { Model } from 'mongoose';
import { User, UserDocument } from './user.schema';

@Injectable()
export class AppService {
 constructor(@InjectModel(User.name) private userModel: Model<UserDocument>) {}
 async login({
   email,
   name,
   image,
 }: {
   email: string;
   name: string;
   image: string;
 }): Promise<any> {
   const user = await this.userModel.findOne({ email: email });
   if (!user) {
     const newUser = new this.userModel({ email, name, image });
     await newUser.save();
     return newUser;
   } else {
     console.log(user);
     return user;
   }
 }
}

```

这里，我们将创建一个新的`login`函数。首先，我们将验证用户电子邮件的存在。如果它们不在数据库中，使用`userModel`创建一个新用户。如果用户已经存在于数据库中，当端点被调用时，我们将返回现有用户的`name`、`email`和`image`。从这里，一个`ticket` i s 被创建，并且`token` 和`audience` 值被传入:

```
{
                   // pass the token as part of the req body
                   token: credentialResponse.credential,
                 }

```

然后，在我们的`app.controller.ts`中，我们将通过使用我们在`App.js`中传递的`token`来使用`login`。下面是用户成功登录时的`ticket.getPayload()`对象数据:

![Successful NestJS SSO Ticket Object Data](img/4654bf47f50f082f1a6a0be9967e38de.png)

由于我们的模式基于`name`、`email`和`image`，我们可以从`ticket.getPayload`中析构这些值，并在`login`中使用它们:

```
@Post('/login')
 async login(@Body('token') token): Promise<any> {
   const ticket = await client.verifyIdToken({
     idToken: token,
     audience: process.env.GOOGLE_CLIENT_ID,
   });
   console.log(ticket.getPayload(), 'ticket');
   const { email, name, picture } = ticket.getPayload();
   const data = await this.appService.login({ email, name, image: picture });
   return {
     data,
     message: 'success',
   };
 }

```

当用户试图登录时，析构的道具从`ticket`对象数据中取出并传递给`login`。如果用户存在，登录用户，将用户数据发送回前端 app。如果用户不存在，则为用户创建新数据，并将其发送回前端应用。

现在，我们可以返回到`client`并创建一个`User`组件，在用户登录时显示他们的详细信息。我们将用`User.js`文件创建一个`component`文件夹，并发回`name`、`email`和`image`。

我们将使用`useStore`钩子并在用户登录时呈现显示的数据。

我们还将检查`User`是否仅在数据从后端服务器发送时呈现，并使用 [`GoogleAuth.signOut()` API](https://developers.google.com/identity/sign-in/web/reference#googleauthsignout) 创建一个`Logout`按钮。点击后，按钮将被调用，从本地存储中删除`authData`，设置`setAuthData`为空，并重新加载窗口:

```
import React from "react";
import { useStore } from "../hooks/useStore";
import { googleLogout } from "@react-oauth/google";
import "../User.css";
const User = () => {
 const { authData, setAuthData } = useStore();
 return (
   <div className={"container"}>
     {authData && (
       <>
         <h1>{authData.data.name}</h1>
         <p>{authData.data.email}</p>
         <img src={authData.data.image} alt="profile" />

         <button
           onClick={() => {
             googleLogout();
             localStorage.removeItem("AuthData");
             setAuthData(null);
             window.location.reload();
           }}
           className={"button"}
         >
           Logout
         </button>
       </>
     )}
   </div>
 );
};

export default User;

```

现在，我们可以在`App.js`中使用`Logout`:

```
import "./App.css";
import { GoogleOAuthProvider, GoogleLogin } from "@react-oauth/google";
import axios from "axios";
import { useStore } from "./hooks/useStore";
import User from "./components/User";

function App() {
 const setAuthData = useStore((state) => state.setAuthData);
 return (
   <div className="App">
     {!useStore((state) => state.authData) ? (
       <>
         <h1>Welcome</h1>
         <GoogleOAuthProvider
           clientId={process.env.REACT_APP_GOOGLE_CLIENT_ID}
         >
           <GoogleLogin
             useOneTap={true}
             onSuccess={async (credentialResponse) => {
               console.log(credentialResponse);
               const { data } = await axios.post(
                 "http://localhost:8080/login",
                 {
                   // pass the token as part of the req body
                   token: credentialResponse.credential,
                 }
               );
               localStorage.setItem("AuthData", JSON.stringify(data));
               setAuthData(data);
             }}
             onError={() => {
               console.log("Login Failed");
             }}
           />
         </GoogleOAuthProvider>
       </>
     ) : (
       <>
         <h1>React x Nestjs Google Sign in</h1>
         <User />
       </>
     )}
   </div>
 );
}

export default App;

```

这里，我们将检查`authData`是否是用`useStore`钩子从后端服务器发送的。如果没有数据，它将呈现一个“欢迎”文本和谷歌登录按钮。但是，如果用户已经登录，我们将呈现`React x NestJs Google sign in`和`User Details`。

现在，让我们开始整个过程，看看它是如何运作的:

![Example of NestJS SSO Welcome in Google](img/1bdf45204ec61377b02843901901704e.png)

当我们登录时，我们有文本和用户详细信息:

![Example of NestJS Secure SSO](img/665e4d86d61d598cfee457db56a69283.png)

接下来，我们可以检查我们的数据库，看看用户是否被保存:

![Checking the React-NestJS Database](img/5e63f4483d2953c9a6a5a05ef690d557.png)

成功！我们的用户在我们的数据库中。总之，我们使用 Google sign-in 成功登录，从 NestJS 检索用户数据，并在用户登录后显示在 React 前端应用程序上。

现在，当我们注销时，我们会看到:

![NestJS SSO Log Out](img/980b5fbbf0e6c7fed2171f400967e5ae.png)

## 结论

在本教程中，我们学习了如何在 NestJS 项目中实现安全的 Google 登录。我们将它连接到一个基本的前端 React 应用程序，同时将用户信息保存在数据库中。你可以在我的库[这里](https://github.com/Taofiqq/react-nest-google-auth)找到这个项目的完整代码。编码快乐！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)