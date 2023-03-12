# 在 NestJS 应用程序中使用 Firebase 身份验证

> 原文：<https://blog.logrocket.com/using-firebase-authentication-in-nestjs-apps/>

## 介绍

在本文中，我们将创建一个小项目，将 Firebase 身份验证集成到 NestJS 应用程序中。

身份验证是任何应用程序的重要组成部分，但是从头开始设置会很有压力。这是 Firebase 的认证产品解决的一个问题。

[Firebase](https://firebase.google.com/) 包括一系列产品和解决方案，使应用程序开发更加容易。Firebase 提供的一些服务包括数据库、认证、分析和托管等。Firebase 可以使用 [firebase-admin](https://www.npmjs.com/package/firebase-admin) npm 模块集成到 NodeJS 应用程序中。

[NestJS](https://nestjs.com/) 帮助您使用 TypeScript 创建服务器端 NodeJS 应用程序。在 npm 上每周有超过 60 万[的下载量，在 GitHub 上有 35000 的下载量，这个框架非常受欢迎。它有一个角型架构，具有控制器和模块等功能。NestJS 在幕后使用 Express，尽管它也可以配置为使用 Fastify。](https://www.npmjs.com/package/@nestjs/core)

## 该项目

我们将创建一个简单的应用程序，只允许经过身份验证的用户访问资源。用户可以通过 Firebase 客户端登录和注册来进行身份验证。在认证时，一个 [JSON Web 令牌(JWT)](https://jwt.io/) 被提供给用户，然后这个令牌与对受限资源的后续请求一起被发送。使用`firebase-admin` SDK 在服务器端验证提供的 JWT，并根据 JWT 的有效性允许或拒绝访问。

## 入门指南

首先，让我们创建一个 Firebase 应用程序。这将为我们提供一些配置，我们稍后将在我们的 NestJS 应用程序中使用这些配置。你可以通过 Firebase 控制台[这里的](https://console.firebase.google.com/)来做到这一点。点击**添加项目，**然后给你的项目命名。我们在这个项目中不需要谷歌分析，所以你不必启用它。然后你可以点击**创建项目**。

![Screenshot of Firebase Create Project screen](img/dc57683141fbf0a9b5184f6a5d2c5177.png)

Once your application has been created, click on the settings icon just beside **Project Overview** and select **Project** **Settings**. Under the service accounts tab, generate a new private key. This should download a JSON file with some credentials which we’ll use to initialize our Firebase Admin SDK on the server (NestJS) side.

![Screenshot of Firebase Project Overview menu](img/03796a1a88320cc3aa1979a61b8e66ff.png)

在同一个**项目设置**菜单中，在**常规**选项卡下，滚动到**你的应用**向 Firebase 注册你的应用(如果你已经向 Firebase 注册了一个应用，点击**添加应用**按钮)。

我们的应用程序是基于网络的，所以选择 **`</>`** 图标。接下来，给你的应用程序起一个昵称。你不需要选择 Firebase 托管，除非你打算这么做。

您将获得一些脚本链接以及 Firebase 配置，这些都是您的应用程序正常运行所必需的。将内容复制到您可以轻松访问的位置，因为以后会用到它。

之后，点击**认证**(位于**构建**侧边栏下)，在**登录方式**菜单下，启用**邮箱/密码**。我们将验证用户的电子邮件和密码。

## 初始化您的 NestJS 应用程序

接下来，我们将全局安装 Nest CLI 包。这将为我们提供一些命令，其中之一是`nest`命令，我们可以用它来引导一个新的 NestJS 应用程序:

```
npm i -g @nestjs/cli //install nest cli package globally

nest new firebase-auth-project //create a new nestjs project in a folder named firebase-auth-project

```

创建新项目的安装过程可能需要一点时间，因为需要安装所有必需的依赖项。新项目应该已经用自动添加到`.gitignore`的一些文件夹初始化了 git。将`*/**/firebase.config.json`添加到`.gitignore`。

使用`npm run start:dev`命令启动开发中的应用程序。默认情况下，NestJS 运行在端口 3000 上，保存文件时服务器会自动重启。每当您启动应用程序时，您的 TypeScript 文件都会被编译成普通 JavaScript，放在`dist`文件夹中。

我们将使用服务器上的[手柄](https://handlebarsjs.com/)文件。为此，我们需要使用以下命令安装的`hbs`模块:

```
npm i hbs
npm i @types/hbs

```

Handlebars 是一个模板引擎，帮助我们编写可重用的动态 HTML。你可以在这里阅读更多关于模板引擎的信息。

您现在可以修改您的`main.ts`文件，如下所示:

```
import { NestFactory } from '@nestjs/core';
import { NestExpressApplication } from '@nestjs/platform-express';
import { join } from 'path';
import { Logger } from '@nestjs/common';
import { AppModule } from './app.module';
import * as hbs from 'hbs';

async function bootstrap() {
  const app = await NestFactory.create<NestExpressApplication>(AppModule);
  const logger = new Logger('App');
  app.useStaticAssets(join(__dirname, '..', 'public'));
  app.setBaseViewsDir(join(__dirname, '..', 'views'));
  hbs.registerPartials(join(__dirname, '..', 'views/partials'));
  app.setViewEngine('hbs');
  app.set('view options', { layout: 'main' });
  await app.listen(3000);
  logger.log('Application started on port 3000');
}

bootstrap();

```

> 你可能在文件的每一行末尾都有一个`Delete`␍``错误，特别是当你运行 Windows 的时候。这是因为在 Windows 中，行尾序列由`CR(carriage-return character)`和换行符或者`LF(linefeed character)`表示，而 git 只使用换行符`LF`。运行`npm run lint`应该可以解决这个问题，或者您可以在代码编辑器中手动将行尾序列设置为`LF`。

`app.set('view options', { layout: 'main' });`表示`main.hbs`文件将作为我们的`hbs`文件的布局。

我们将在这个项目中使用几个包，所以在继续下一步之前，让我们把它们都安装好:

```
npm i @nestjs/passport class-transformer firebase-admin passport passport-firebase-jwt

```

Passport 是一个易于使用且非常流行的 NodeJS 认证库，通过@nestjs/passport 模块与 NestJS 很好地配合，提供了一个健壮的认证系统。

## 创建路线和`hbs`文件

让我们创建我们的第一条路线。在`app.controller.ts`文件中，添加以下代码:

```
import { Controller, Get, Render } from '@nestjs/common';
import { AppService } from './app.service';

@Controller('')
export class AppController {
  constructor(private readonly appService: AppService) {}
  @Get('login')
  @Render('login')
  login() {
    return;
  }

  @Get('signup')
  @Render('signup')
  signup() {
    return;
  }
}

```

这表明当我们向`/login`路由发送一个`GET`请求时，`login.hbs`文件应该为我们呈现，注册路由也是如此。现在让我们创建那些`hbs`文件。

在项目的根目录下，创建`public`和`views`文件夹。您的文件夹结构应该有点像这样:

```
├──-public
├──-src
├───test
├───views

```

请记住，我们已经将`main.hbs`指定为我们的布局文件，因此在 views 文件夹中，创建`main.hbs`文件并添加以下代码:

```
<html>
<head>
    <meta name="viewport" content="width=device-width" />
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css"
        integrity="sha384-JcKb8q3iqJ61gNV9KGb8thSsNjpSL0n8PARn9HuZOnIxN0hoP+VmmDGMN5t9UJ0Z" crossorigin="anonymous">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.min.css">
    <link rel="stylesheet" href="/styles/style.css">
</head>
<body>
    <nav class="navbar navbar-dark bg-primary navbar-expand">
        <div class="container"><a class="navbar-brand" href="#">Nest Auth</a>
        </div>
    </nav>
    {{{body}}}
    <div id="quotes" class="d-none">
    </div>
    <script src="https://www.gstatic.com/firebasejs/8.3.1/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.3.1/firebase-auth.js"></script>
    <script src='/scripts/main.js'></script>
</html>

```

注意文件底部的前两个脚本。这些是在 web 上使用 Firebase 特性的脚本。第一个是核心 FirebaseJS SDK，而第二个是用于 Firebase 身份验证。您需要为应用程序中所需的 Firebase 特性添加脚本。

在视图文件夹中创建一个`login.hbs`和`signup.hbs`文件，并添加以下代码。

`login.hbs`:

```
<div class='container'>
    <form id='login-form' class='mt-3'>
        <div class='form-group'>
            <label htmlFor='email'>Email address</label>
            <input type='email' class='form-control' id='email' placeholder='Enter email' required />
        </div>
        <div class='form-group'>
            <label htmlFor='password'>Password</label>
            <input type='password' class='form-control' id='password' placeholder='Password' required />
        </div>
        <p id="error" class="text-white text-small bg-danger"></p>
        <button type='submit' class='btn btn-primary pull-left'>
            Login
        </button>
    </form>
</div>
<script src='/scripts/login.js'></script>

```

`signup.hbs`:

```
<div class='container'>
    <form id='signup-form' class='mt-3'>
        <div class='form-group'>
            <label htmlFor='email'>Email address</label>
            <input type='email' class='form-control' id='email' placeholder='Enter email' required />
        </div>
        <div class='form-group'>
            <label htmlFor='password'>Password</label>
            <input type='password' class='form-control' id='password' placeholder='Password' required />
        </div>
        <p id="error" class="text-white text-small bg-danger"></p>
        <button type='submit' class='btn btn-primary'>
            Signup
        </button>
    </form>
</div>
<script src="/scripts/signup.js"></script>
>
```

现在是样式和脚本。在`public`文件夹中，添加脚本和样式子文件夹。在 styles 子文件夹中，添加一个`style.css`文件。

`style.css`:

```
blockquote {
  position: relative;
  text-align: left;
  padding: 1.2em 0 2em 38px;
  border: none;
  margin: 20px auto 20px;
  max-width: 800px;
  width: 100%;
  display: block;
}
blockquote:after {
  content: '';
  display: block;
  width: 2px;
  height: 100%;
  position: absolute;
  left: 0;
  color: #66cc66;
  top: 0;
  background: -moz-linear-gradient(
    top,
    #66cc66 0%,
    #66cc66 60%,
    rgba(255, 255, 255, 0) 100%
  );
  background: -webkit-linear-gradient(
    top,
    #66cc66 0%,
    #66cc66 60%,
    rgba(255, 255, 255, 0) 100%
  );
}
blockquote:before {
  content: '\f10d';
  font-family: 'fontawesome';
  font-size: 20px;
  display: block;
  margin-bottom: 0.8em;
  font-weight: 400;
  color: #66cc66;
}
blockquote > cite,
blockquote > p > cite {
  display: block;
  font-size: 16px;
  line-height: 1.3em;
  font-weight: 700;
  font-style: normal;
  margin-top: 1.1em;
  letter-spacing: 0;
  font-style: italic;
}

```

在脚本文件夹中，创建以下文件:`main.js`、`login.js`和`signup.js`。你可以暂时让它们空着，我们会回来的。您应该访问`/login`和`/signup`路线，以确保您的文件被正确渲染。

## 创造我们的资源

列表中的下一项是创建我们的受限资源。在这种情况下，它将是一个引用及其作者的列表。要创建新的`resources`文件夹(模块、控制器和服务都已设置好),运行:

```
nest g resource resources

```

选择 **REST API** 作为传输层，选择 **No** 作为“您想生成 CRUD 入口点吗？”

完成后，在`resources.service.ts`文件中添加以下代码:

```
import { Injectable } from '@nestjs/common';

@Injectable()
export class ResourcesService {
  private readonly resources: any[];
  constructor() {
    this.resources = [
      {
        quote: 'They taste like...burning.',
        character: 'Ralph Wiggum',
      },
      {
        quote: 'My eyes! The goggles do nothing!',
        character: 'Rainier Wolfcastle',
      },
      {
        quote:
          "Hello, Simpson. I'm riding the bus today becuase Mother hid my car keys to punish me for talking to a woman on the phone. She was right to do it.",
        character: 'Principal Skinner',
      },
      {
        quote:
          'I live in a single room above a bowling alley...and below another bowling alley.',
        character: 'Frank Grimes',
      },
      {
        quote:
          "All I'm gonna use this bed for is sleeping, eating and maybe building a little fort.",
        character: 'Homer Simpson',
      },
      {
        quote: 'In theory, Communism works! In theory.',
        character: 'Homer Simpson',
      },
      {
        quote: "Oh, wow, windows. I don't think I could afford this place.",
        character: 'Otto',
      },
    ];
  }

  getAll() {
    return this.resources;
  }
}

```

在那里你可以看到我们的引用(来自电视节目“辛普森一家”)和一个返回所有引用的方法`getAll()`。

将此添加到`resources.controller.ts`文件:

```
import { Controller, Get } from '@nestjs/common';
import { ResourcesService } from './resources.service';

@Controller('resources')
export class ResourcesController {
  constructor(private readonly resourcesService: ResourcesService) {}

  @Get('')
  getAll() {
    return this.resourcesService.getAll();
  }
}

```

`@Controller()`装饰符表示以`/resources`开始的路由指向这个端点。我们有一个`GET`端点，它使用`resources.service.ts`中的`getAll()`方法返回我们所有的报价。为了测试您的应用程序，向`[http://localhost:3000/resources](http://localhost:3000/resources)`发送一个`GET`请求应该会返回所有报价。

这个端点目前是公共的，是时候处理应用程序的身份验证部分了。

## Firebase 客户端

为了使用 Firebase 从客户端对用户进行身份验证，首先我们使用在 Firebase 控制台中创建新应用程序时提供的 Firebase web 配置来初始化我们的应用程序。您可以在项目设置菜单中的**常规**选项卡中获得。

以这种方式将设置添加到公共文件夹中的`main.js`文件中:

```
const quotes = document.getElementById('quotes');
const error = document.getElementById('error');

var firebaseConfig = {
  apiKey: 'AIzaSyB7oEYDje93lJI5bA1VKNPX9NVqqcubP1Q',
  authDomain: 'fir-auth-dcb9f.firebaseapp.com',
  projectId: 'fir-auth-dcb9f',
  storageBucket: 'fir-auth-dcb9f.appspot.com',
  messagingSenderId: '793102669717',
  appId: '1:793102669717:web:ff4c646e5b2242f518c89c',
};
// Initialize Firebase
firebase.initializeApp(firebaseConfig);
firebase.auth().setPersistence(firebase.auth.Auth.Persistence.NONE);

const displayQuotes = (allQuotes) => {
  let html = '';
  for (const quote of allQuotes) {
    html += `<blockquote class="wp-block-quote">
                <p>${quote.quote}. </p><cite>${quote.character}</cite>
            </blockquote>`;
  }
  return html;
};

```

`quotes`、`error`和`displayQuotes`是将被`login.js`和`signup.js`脚本使用的变量，所以在其他两个之前导入`main.js`文件是很重要的。因为 Firebase 脚本首先包含在`main.hbs`文件中，所以`main.js`又可以访问`firebase`变量。

现在，为了处理用户注册，将这个添加到`signup.js`:

```
const signupForm = document.getElementById('signup-form');
const emailField = document.getElementById('email');
const passwordField = document.getElementById('password');
signupForm.addEventListener('submit', (e) => {
  e.preventDefault();
  const email = emailField.value;
  const password = passwordField.value;
  firebase
    .auth()
    .createUserWithEmailAndPassword(email, password)
    .then(({ user }) => {
      return user.getIdToken().then((idToken) => {
        return fetch('/resources', {
          method: 'GET',
          headers: {
            Accept: 'application/json',
            Authorization: `Bearer ${idToken}`,
          },
        })
          .then((resp) => resp.json())
          .then((resp) => {
            const html = displayQuotes(resp);
            quotes.innerHTML = html;
            document.title = 'quotes';
            window.history.pushState(
              { html, pageTitle: 'quotes' },
              '',
              '/resources',
            );
            signupForm.style.display = 'none';
            quotes.classList.remove('d-none');
          })
          .catch((err) => {
            console.error(err.message);
            error.innerHTML = err.message;
          });
      });
    })
    .catch((err) => {
      console.error(err.message);
      error.innerHTML = err.message;
    });
});

```

并登录`login.js`:

```
const loginForm = document.getElementById('login-form');
const emailField = document.getElementById('email');
const passwordField = document.getElementById('password');
loginForm.addEventListener('submit', (e) => {
  e.preventDefault();
  const email = emailField.value;
  const password = passwordField.value;
  firebase
    .auth()
    .signInWithEmailAndPassword(email, password)
    .then(({ user }) => {
      return user.getIdToken().then((idToken) => {
        return fetch('/resources', {
          method: 'GET',
          headers: {
            Accept: 'application/json',
            Authorization: `Bearer ${idToken}`,
          },
        })
          .then((resp) => resp.json())
          .then((resp) => {
            const html = displayQuotes(resp);
            quotes.innerHTML = html;
            document.title = 'quotes';
            window.history.pushState(
              { html, pageTitle: 'quotes' },
              '',
              '/resources',
            );
            loginForm.style.display = 'none';
            quotes.classList.remove('d-none');
          })
          .catch((err) => {
            console.error(err.message);
            error.innerHTML = err.message;
          });
      });
    })
    .catch((err) => {
      console.error(err.message);
      error.innerHTML = err.message;
    });
});

```

## firebase-管理

虽然用户现在可以注册并登录我们的应用程序，但我们的`resources`路线仍然是开放的，任何人都可以访问。记住，我们在 NestJS 应用程序中安装了`firebase-admin`。正如我前面提到的，在允许或拒绝用户访问路由之前，这个包将帮助验证从客户端发送的 JWT 令牌。

在`src`文件夹中，创建一个名为`firebase`的文件夹。这将包含我们所有的 Firebase 设置。在`firebase`文件夹中，创建一个名为`firebase.config.json`的文件。这将包含在 service account 选项卡下生成私钥时下载的 JSON 文件的值:

```
{
  "type": "service_account",
  "project_id": "",
  "private_key_id": "",
  "private_key": "",
  "client_email": "",
  "client_id": "",
  "auth_uri": "",
  "token_uri": "",
  "auth_provider_x509_cert_url": "",
  "client_x509_cert_url": ""
}

```

保持这些值的私密性很重要，因为其中一些非常敏感。

接下来，我们将为 Firebase 创建一个通行证策略。策略是 Passport 中特定服务(在本例中为 Firebase)的身份验证机制。在`firebase`文件夹中创建一个`firebase-auth.strategy.ts`文件，并添加以下代码:

```
import { PassportStrategy } from '@nestjs/passport';
import { Injectable, UnauthorizedException } from '@nestjs/common';
import { Strategy, ExtractJwt } from 'passport-firebase-jwt';
import * as firebaseConfig from './firebase.config.json';
import * as firebase from 'firebase-admin';

const firebase_params = {
  type: firebaseConfig.type,
  projectId: firebaseConfig.project_id,
  privateKeyId: firebaseConfig.private_key_id,
  privateKey: firebaseConfig.private_key,
  clientEmail: firebaseConfig.client_email,
  clientId: firebaseConfig.client_id,
  authUri: firebaseConfig.auth_uri,
  tokenUri: firebaseConfig.token_uri,
  authProviderX509CertUrl: firebaseConfig.auth_provider_x509_cert_url,
  clientC509CertUrl: firebaseConfig.client_x509_cert_url,
};

@Injectable()
export class FirebaseAuthStrategy extends PassportStrategy(
  Strategy,
  'firebase-auth',
) {
  private defaultApp: any;
  constructor() {
    super({
      jwtFromRequest: ExtractJwt.fromAuthHeaderAsBearerToken(),
    });
    this.defaultApp = firebase.initializeApp({
      credential: firebase.credential.cert(firebase_params),
    });
  }
  async validate(token: string) {
    const firebaseUser: any = await this.defaultApp
      .auth()
      .verifyIdToken(token, true)
      .catch((err) => {
        console.log(err);
        throw new UnauthorizedException(err.message);
      });
    if (!firebaseUser) {
      throw new UnauthorizedException();
    }
    return firebaseUser;
  }
}

```

这里发生了什么事？JWT 作为一个承载令牌从请求头中提取出来，我们的 Firebase 应用程序用于验证这个令牌。如果令牌有效，则返回结果，否则拒绝用户的请求并抛出未经授权的异常。

> 如果您在导入 Firebase 配置时遇到 ESLint 错误，请将其添加到您的`tsconfig.json`文件:`"resolveJsonModule": true`。

## 整合战略

目前，我们的身份验证策略是一个独立的功能，帮助不大。我们可以让它成为中间件，并将其集成到需要认证的端点中，但 NestJS 有一种更简单、更好的方法来处理认证，称为 [Guards](https://docs.nestjs.com/guards) 。我们将创建一个防护来利用我们的 Firebase 策略，并用一个简单的装饰器将它包装在需要认证的路由周围。

创建一个名为`firebase-auth.guard.ts`的文件，并向其中添加以下代码:

```
import { ExecutionContext, Injectable } from '@nestjs/common';
import { AuthGuard } from '@nestjs/passport';
import { Reflector } from '@nestjs/core';

@Injectable()
export class FirebaseAuthGuard extends AuthGuard('firebase-auth') {
  constructor(private reflector: Reflector) {
    super();
  }
  canActivate(context: ExecutionContext) {
    const isPublic = this.reflector.getAllAndOverride<boolean>('public', [
      context.getHandler(),
      context.getClass(),
    ]);
    if (isPublic) {
      return true;
    }
    return super.canActivate(context);
  }
}

```

接下来，更新您的`resources.controller.ts`文件，如下所示:

```
import { Controller, Get, UseGuards } from '@nestjs/common';
import { FirebaseAuthGuard } from 'src/firebase/firebase-auth.guard';
import { ResourcesService } from './resources.service';
@Controller('resources')
export class ResourcesController {
  constructor(private readonly resourcesService: ResourcesService) {}
  @Get('')
  @UseGuards(FirebaseAuthGuard)
  getAll() {
    return this.resourcesService.getAll();
  }
}

```

您还需要通过将`FirebaseAuthStrategy`添加到提供者列表来更新您的`app.module.ts`文件:

```
import { Module } from '@nestjs/common';
import { AppController } from './app.controller';
import { AppService } from './app.service';
import { FirebaseAuthStrategy } from './firebase/firebase-auth.strategy';
import { ResourcesModule } from './resources/resources.module';

@Module({
  imports: [ResourcesModule],
  controllers: [AppController],
  providers: [AppService, FirebaseAuthStrategy],
})
export class AppModule {}

```

您可以再次测试您的应用程序，您会发现我们的资源路径现在得到了很好的保护。

## 结论

虽然这是一个基本的应用程序，但是您可以利用这些知识来创建使用 Firebase 身份验证的更大的应用程序。您还可以通过调用`firebase.auth().signOut()`轻松地从 Firebase 客户端注销用户。这个库可以在 [Github 上找到。](https://github.com/supercede/nest-firebase-auth)

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)