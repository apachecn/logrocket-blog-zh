# 用 Firebase cloud 函数、TypeScript 和 Firestore 构建 REST API

> 原文：<https://blog.logrocket.com/rest-api-firebase-cloud-functions-typescript-firestore/>

Firebase 是构建无服务器应用的领先解决方案之一。它为开发人员提供了一种轻松开发可扩展的高质量应用程序的方式，而无需从一开始就担心长期设置和维护服务器的成本。Firebase 还提供了与 Google Analytics 和 Firebase 文档数据库 Firestore 等 Google 服务的轻松集成。

在本文中，我们将了解如何使用 Firebase cloud 函数、TypeScript 和 Firestore 构建 REST API。您还需要一些关于 Express.js 的知识来构建我们的演示应用程序。然而，你不需要成为上述任何一种技术的专家。我们的演示应用程序将是一个日志 API，将能够接受新的条目，并检索，更新和删除现有的条目。

## 设置 Firebase

要开始使用我们的应用程序，您需要在计算机上安装 Node.js。点击[此处](https://nodejs.org/en/)查看 Node.js 的安装指南。如果您已经安装了 Node.js，在您的终端上运行以下命令应该会返回您当前的 node . js 版本:

```
node --version

```

要在没有任何警告的情况下遵循本教程，您需要安装我们将在 Firebase cloud 中使用的相同版本的 Node.js。目前，Firebase SDK 支持 Node.js 12 和 10。让我们为我们的应用程序使用版本 12。Node.js 安装附带了 npm，我们将使用它来[安装 Firebase CLI](https://firebase.google.com/docs/cli#setup_update_cli) 。让我们在终端上运行以下命令来实现这一点:

```
npm install -g firebase-tools

```

安装 Node.js 和 Firebase CLI 后，您就可以在浏览器上导航到 Firebase 控制台。您需要登录您的 Google 帐户。成功登录后，您应该会看到一个**创建项目**按钮，如下所示:

![Firebase Create a Project Button](img/a627b441a7a80b46db050a176f545d38.png)

根据您何时执行本演练，用户界面看起来可能会有所不同，但您应该仍然能够找到自己的路。当您点击**创建项目**按钮时，您会发现创建 Firebase 项目的过程非常简单。

接下来，您可以选择项目名称和 ID。我将为自己使用 journal-rest-api。因为 ID 是项目的唯一标识符，所以您需要选择不同的项目 ID。这样，您就可以成功地创建您的 Firebase 项目了。

成功创建后，您应该会看到如下所示的项目概述页面:

![Overview Page](img/1d8bcf4684d45732733c18badc1b07fa.png)

### 升级我们的计费方案

默认情况下，你的新账户应该在 Spark **免费账单计划**中。然而，为了能够使用 Firebase 功能，你需要将你的计费计划升级到 Blaze **即付即用。**我们不必为这个演示担心资金问题，但是 Firebase 仍然要求您升级您的计费方案，以便能够使用云功能。

要升级您的计费计划，请打开左侧导航中的**开发**下拉菜单，然后点击**功能。**在功能页面上，系统会提示您升级您的账单。消息应该是这样的:

![Upgrade Project Page](img/71eb3dffbdc502ff0391938521f32fdd.png)

## 创建 firestorm 数据库

随着我们的计费计划升级，让我们为我们的应用程序创建一个 Firestore 数据库。导航到左侧窗格中的**云 Firestore** 选项，然后点击**创建数据库**按钮。您应该会看到如下所示的模态:

![Start in Production Mode Option](img/c52236f83ab7d753cce790f24ab565f8.png)

在本演示中，我们将使用**在生产模式下启动**选项。随着数据库的创建，我们现在可以编写和部署我们的第一个云函数了。

## 编写我们的第一个云函数

为了编写我们的第一个云函数，我们将返回到我们的终端，运行以下命令来登录我们的 firebase 帐户:

```
firebase login

```

运行该命令应该会将您带到浏览器上的身份验证页面。身份验证成功后，在您的计算机上创建一个项目目录，并在其中运行以下命令来初始化 Firebase 函数:

```
firebase init functions

```

您可以使用设置的默认选项。选择**使用现有项目**选项，然后选择您刚刚在 Firebase 上创建的项目。当被问到**你想用什么语言写云函数？**选择**打字稿**。最后，接受安装 npm 依赖项的选项。如果一切顺利，您应该会看到如下所示的成功消息:

![Success Message](img/e6084f35e0890958cf7498b17d6f90d7.png)

我们新应用程序的项目结构应该如下所示:

```
├── .firebaserc
├── .gitignore
├── .firebase.json
└── functions
    ├── package.json
    ├── tsconfig.json
    ├── .gitignore
    └── src
        ├── index.ts

```

请注意，`package.json`文件位于`functions`目录中。这意味着当运行我们的`npm`命令时，我们需要导航到终端上的函数目录。当您打开`./functions/src/index.ts`文件时，您应该会看到:

```
import * as functions from 'firebase-functions';
// // Start writing Firebase Functions
// // https://firebase.google.com/docs/functions/typescript
//
// export const helloWorld = functions.https.onRequest((request, response) => {
//   functions.logger.info("Hello logs!", {structuredData: true});
//   response.send("Hello from Firebase!");
// });

```

取消对`helloworld`函数的注释应该会得到这样的结果:

```
import * as functions from 'firebase-functions';

export const helloWorld = functions.https.onRequest((request, response) => {
  functions.logger.info("Hello logs!", {structuredData: true});
  response.send("Hello from Firebase!");
});

```

## 部署我们的第一个功能

要部署默认的`helloworld`函数，导航到终端上的函数目录并运行以下命令:

```
npm run deploy

```

如果部署成功，您应该会收到如下所示的响应:

![Deploy Complete Message](img/a29ff49f6e2c824e9e04605a05b5113e.png)

完成后，当我们导航到 Firebase 控制台的**函数**部分时，我们应该会看到我们部署的`helloWorld`函数及其 URL。导航到我们浏览器上显示的 URL 应该会返回 Firebase 的响应 **Hello！**

如果我们的目标是进行简单的 API 调用和数据库查询，那么我们目前所做的应该足够了。然而，由于我们想要用中间件和多路径构建一个更健壮的 REST API，我们需要一个 Node.js 框架，比如 [Express](https://blog.logrocket.com/typescript-with-node-js-and-express/) 。要安装 Express.js，让我们在终端上运行下面的代码——记住，我们仍然在函数目录中:

```
npm i -s express

```

安装 Express 后，我们可以在应用程序中初始化它，方法是用以下代码替换`./index.ts`文件中的代码:

```
import * as functions from 'firebase-functions'
import * as express from 'express'

const app = express()
app.get('/', (req, res) => res.status(200).send('Hey there!'))
exports.app = functions.https.onRequest(app)

```

接下来，让我们通过在终端上运行`npm run deploy`来重新部署我们的应用程序。当出现删除初始`helloWorld`功能的警告时，选择**是**。

```
The following functions are found in your project but do not exist in your local
 source code:
   helloWorld(us-central1)
...
Would you like to proceed with deletion? Selecting no will continue the rest of the deployments. (y/N)

```

完成后，当你重新访问 Firebase 控制台上的函数页面时，你应该会看到一个名为 **app** 的新函数及其旁边的请求 URL。导航到那个 URL 应该会返回响应“**嘿，你好！**”如我们的`./index.ts`文件的`line 5`所示:

```
app.get('/', (req, res) => res.status(200).send('Hey there!'))

```

## 为我们的应用程序创建服务帐户

为了从我们的应用程序访问我们的 Firestore 数据库和管理工具，我们需要创建一个服务帐户。为此，单击左侧窗格中**项目概述**旁边的齿轮图标:

![Gear Icon Next to the Project Overview Tab](img/76b656d7890b4b19734f1aa1388f1616.png)

从设置页面，导航到**服务账户**，选择 **Node.js** ，点击**生成新私钥**按钮。

![Generate New Private Key Button](img/67374e2e684b3b742abd36e958cef7d4.png)

这应该会为您的新服务帐户生成一个 JSON 配置文件。JSON 文件应该是这样的:

```
{
  "type": "service_account",
  "project_id": "journal-rest-api",
  "private_key_id": "private_key_id",
  "private_key": "private_key",
  "client_email": "client_email",
  "client_id": "client_id",
  "auth_uri": "auth_url",
  "token_uri": "token_url",
  "auth_provider_x509_cert_url": "auth_provider_x509_cert_url",
  "client_x509_cert_url": "client_x509_cert_url"
}

```

### 访问我们的服务帐户配置

使用我们新的服务帐户信息的一个简单方法是将 JSON 文件移动到我们的项目目录中，然后在需要的地方导入它。然而，建议保持这些信息的私密性，如果我们的代码库被推送到一个远程存储库，那么将它存储在项目目录中会将其公开。

使用服务帐户信息的一个更好的方法是将所需的键值对存储为 Firebase 环境变量。对于我们的应用程序，我们需要`private_key`、`project_id`和`client_email`。让我们将这些存储为环境变量。为此，我们将返回到我们的终端并运行以下命令:

```
firebase functions:config:set private.key="YOUR API KEY" project.id="YOUR CLIENT ID" client.email="YOUR CLIENT EMAIL"

```

在运行上面的命令之前，记得用 JSON 文件中的值替换字符串值。需要注意的是，键中只接受小写字符，并且每个键都应该使用句点来命名，例如，`private.key`。如果更新成功，您应该会收到如下所示的响应:

```
+  Functions config updated.

Please deploy your functions for the change to take effect by running firebase deploy --only functions

```

让我们重新部署我们的功能，以使我们所做的更改生效:

```
npm run deploy

```

## 设置我们的 Firebase 配置

现在我们有了作为 Firebase 环境变量的服务帐户信息，让我们设置配置文件。我们将在`./functions/src`目录下创建一个名为`config`的新文件夹，并在其中创建一个名为`firebase.ts`的新文件。这个结构应该是这样的:

```
└── functions
    └── src
        └── config
           ├── firebase.ts

```

让我们将下面的代码粘贴到`firebase.ts`文件中:

```
import * as admin from 'firebase-admin'
import * as functions from 'firebase-functions'

admin.initializeApp({
  credential: admin.credential.cert({
    privateKey: functions.config().private.key.replace(/\\n/g, '\n'),
    projectId: functions.config().project.id,
    clientEmail: functions.config().client.email
  }),
  databaseURL: 'https://[your_project_id].firebaseio.com'
})

const db = admin.firestore()
export { admin, db }

```

我们首先导入 Firebase admin 来初始化我们的应用程序，导入 Firebase 函数来访问 Firebase 环境变量。我们继续调用`admin.initializeApp()`函数，并向它提供一个对象参数，其中包含我们的服务帐户凭证。我们还使用了`functions.config().name_of_variable`来访问我们的环境变量。

注意，我们向私钥添加了一个`.replace()`方法。这将私钥字符串中的`\\n`替换为`\n`。如果没有这个，当我们试图使用这个变量时，就会得到一个`invalid PEM format`错误消息。我们还在`credential`后面加了一个`databaseUrl`键。

您应该用您在创建 Firebase 项目时提供的名称替换`[your_project_id]`。接下来，我们为 Firestore 数据库创建了一个名为`db`的变量，并给它赋值`admin.firestore()`。最后，我们从`firebase.ts`文件中导出了`admin`和`db`。

## 使用 firestorm

Firestore 是一个用于移动、web 和服务器开发的 NoSQL 数据库。它提供了与 Firebase 云功能的无缝集成，其灵活和可伸缩的特性使其非常适合构建 web APIs。像所有 NoSQL 数据库一样，Firestore 是非关系数据库，由文档和集合组成。这些文档包含一组键值对，并存储在集合中。

在 Firestore 中，可以从 Firebase 控制台或通过云功能创建文档和集合。为了从我们的云函数中创建文档或集合，我们将使用从 Firebase 配置文件中导出的`db`:

```
db.collection('entries').doc().create({ document_object_here })

```

上面的代码行将在名为`entries`的集合中创建一个新文档，如果这样的集合不存在，将使用提供的名称创建一个新文档。我们还可以创建一个空文档，然后稍后为它提供键值对:

```
const entry = db.collection('entries').doc()

const entryObject = {
  id: entry.id,
  title: 'entry title here',
  text: 'entry text here'
}

entry.set(entryObject)

```

Firebase 文档有一个可以用来更新它们的`.set()`方法。随着我们的进展，我们将看到如何使用其他一些方法。

### 向我们的 firestorm 数据库添加条目

设置好数据库配置后，我们可以继续添加创建日志条目的功能。在`./functions/src`目录中，让我们创建一个名为`entryController.ts`的新文件。我们将使用这个文件来存放我们条目的所有控制器。在`entryController.ts`文件中，我们首先从`express`导入我们的`Response`类型，从`./config/firebase`导入`db`:

```
import { Response } from 'express'
import { db } from './config/firebase'

```

接下来，我们将为条目定义一个类型，然后用它来创建我们的`Request`类型:

```
...
type EntryType = {
  title: string,
  text: string,
  coverImageUrl: string
}

type Request = {
  body: EntryType,
  params: { entryId: string }
}

```

对于`Request`类型，我们将`entryId`作为`params`的属性。当我们想要编辑一个条目时，我们将使用这个属性。接下来，让我们创建我们的`addEntry`函数:

```
...
const addEntry = async (req: Request, res: Response) => {
  const { title, text } = req.body
  try {
    const entry = db.collection('entries').doc()
    const entryObject = {
      id: entry.id,
      title,
      text,
    }

    entry.set(entryObject)

    res.status(200).send({
      status: 'success',
      message: 'entry added successfully',
      data: entryObject
    })
  } catch(error) {
      res.status(500).json(error.message)
  }
}

```

在我们的`addEntry`函数中，我们从请求体中析构`title`和`text`开始。接下来，我们使用`db.collection().doc()`方法创建了一个新的 Firebase 文档，然后用我们的`entryObject`更新了这个文档。我们的新文档带有一个`id`。我们需要将这个`id`存储为它的键值对的一部分，以便我们可以轻松地访问它。

如果 API 请求成功，返回一个响应给我们的用户。我们还将我们的数据库操作包装在一个`try…catch`语句中，这样如果我们的数据库请求有错误，catch 块将响应一个`500`错误和来自`error`对象的消息。

HTTP 状态代码不包含用户输入错误或其他类型的错误，所以我们需要在发出数据库请求或使用中间件之前包含这些情况。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

要使用我们的`addEntry`函数，让我们从`entryController.ts`文件中导出它:

```
...
export { addEntry }

```

接下来，我们将把它导入到我们的`./functions/src/index.ts`文件中:

```
...
import { addEntry } from './entryController'

```

然后，我们将在初始化`express`的那一行下面添加以下代码:

```
app.post('/entries', addEntry)

```

我们的`index.ts`文件现在应该是这样的:

```
import * as functions from 'firebase-functions'
import * as express from 'express'
import { addEntry } from './entryController'

const app = express()

app.get('/', (req, res) => res.status(200).send('Hey there!'))
app.post('/entries', addEntry)

exports.app = functions.https.onRequest(app)

```

为了重新部署我们的应用程序，我们将返回到终端上的 **Functions** 目录，并运行以下命令:

```
npm run deploy

```

如果我们的部署成功，我们应该会得到如下所示的响应:

```
+  functions: Finished running predeploy script.
i  ...
+  functions[app(us-central1)]: Successful update operation.

+  Deploy complete!

```

为了测试我们的 API，让我们导航到 Firebase 控制台的**功能**部分。我们应该看到我们部署的函数名`app`和它的 URL。为了向 Firestore 数据库添加一个新条目，我们将把 route `/entries`追加到我们的 URL 中。它应该是这样的:`[https://us-central1-project-id-here.cloudfunctions.net/app/entries](https://us-central1-project-id-here.cloudfunctions.net/app/entries)`。

然后，我们将使用像 Postman 或失眠症这样的 API 客户端向我们的 URL 发送一个`POST`请求，在请求体中包含属性`title`和`text`:

```
{
  "title": "My first entry",
  "text": "Hey there! I'm awesome!"
}

```

对此我们应该得到类似的回应:

```
{
    "status": "success",
    "message": "entry added successfully",
    "data": {
        "id": "g8fBsSVQWlkby9GMf0LC",
        "title": "My first entry",
        "text": "Hey there! I'm awesome!"
    }
}

```

### 从我们的火风暴数据库中获取条目

既然我们已经看到了如何使用云函数向 Firestore 数据库添加条目，那么让我们看看如何从中获取数据。为此，我们将在`entryController.ts`文件中创建一个名为`getAllEntries`的新函数:

```
...
const getAllEntries = async (req: Request, res: Response) => {
  try {
    const allEntries = await db.collection('entries').get()
    return res.status(200).json(allEntries.docs)
  } catch(error) { return res.status(500).json(error.message) }
}

export { addEntry, getAllEntries }

```

在我们的`getAllEntries`函数中，我们使用了`db.collection('entries').get()`方法来检索我们的`entries`集合中的所有数据，然后使用`docs`属性来访问我们的响应语句中的文档。如果我们尝试使用这个函数通过 API 客户机检索条目，我们将得到一个包含敏感信息(如我们的私钥)的响应。让我们通过更新函数来避免这种情况，只返回条目数据:

```
const getAllEntries = async (req: Request, res: Response) => {
  try {
    const allEntries: EntryType[] = []
    const querySnapshot = await db.collection('entries').get()
    querySnapshot.forEach((doc: any) => allEntries.push(doc.data()))
    return res.status(200).json(allEntries)
  } catch(error) { return res.status(500).json(error.message) }
}

```

在我们的`getAllEntries`函数中，我们首先创建了一个名为`allEntries`的数组，类型为`EntryType[]`。接下来，我们将来自`db.collection().get()`方法的数据分配给一个名为`querySnapshot`的变量。在 Firestore 中，一个`QuerySnapshot`包含一个查询的结果。`forEach`方法循环遍历我们的`QuerySnapshot`并将文档数据推送到我们的`allEntries`数组。

为了使用我们的`getAllEntries`函数，我们将把它导入到`./functions/src/index.ts`文件中，然后把它添加到一个`GET`请求方法中:

```
...
import { addEntry, getAllEntries } from './entryController'

const app = express()

app.get('/', (req, res) => res.status(200).send('Hey there!'))
app.post('/entries', addEntry)
app.get('/entries', getAllEntries)

```

让我们重新部署我们的应用程序，并向`./entries`发出`GET`请求。我们应该会收到类似这样的响应:

```
[
    {
        "text": "Hey there! I'm awesome!",
        "id": "nS4so0TiUI0YLQH3xZLT",
        "title": "My first entry"
    }
]

```

### 更新和删除条目

既然我们已经看到了如何从 Firebase cloud 函数向 Firestore 数据库发出`POST`和`GET`请求，那么让我们看看如何更新和删除条目。为了添加更新条目的功能，我们将在`entryController.ts`文件中创建一个名为`updateEntry`的新函数:

```
...
const updateEntry = async (req: Request, res: Response) => {
  const { body: { text, title }, params: { entryId } } = req

  try {
    const entry = db.collection('entries').doc(entryId)
    const currentData = (await entry.get()).data() || {}
    const entryObject = {
      title: title || currentData.title,
      text: text || currentData.text,
    }

    await entry.set(entryObject).catch(error => {
      return res.status(400).json({
        status: 'error',
        message: error.message
      })
    })

    return res.status(200).json({
      status: 'success',
      message: 'entry updated successfully',
      data: entryObject
    })
  }
  catch(error) { return res.status(500).json(error.message) }
}

```

在我们的`updateEntry`函数中，我们通过请求参数中提供的 ID 获取请求的文档。我们为此使用了`db.collection().doc()`方法。接下来，我们用`.data()`方法获得当前文档数据对象，这样当我们用`.set()`方法更新条目时，我们可以用当前条目数据替换用户没有提供的任何字段。

让我们也创建一个删除条目的函数:

```
...
const deleteEntry = async (req: Request, res: Response) => {
  const { entryId } = req.params

  try {
    const entry = db.collection('entries').doc(entryId)

    await entry.delete().catch(error => {
      return res.status(400).json({
        status: 'error',
        message: error.message
      })
    })

    return res.status(200).json({
      status: 'success',
      message: 'entry deleted successfully',
    })
  }
  catch(error) { return res.status(500).json(error.message) }
}

```

我们的`deleteEntry`函数也使用来自请求参数的`entryId`和`db.collection().doc()`方法获取文档。接下来，我们使用`delete`方法删除我们的条目。我们还添加了`catch`方法来监视错误，并在数据库操作失败时返回响应。

为了使用我们的新函数，我们将从`entryController.ts`文件中导出它们:

```
...
export { addEntry, getAllEntries, updateEntry, deleteEntry }

```

我们现在可以将它们导入到`./functions/src/index.ts`文件中:

```
...
import { addEntry, getAllEntries, updateEntry, deleteEntry } from './entryController'

```

导入函数后，让我们添加`patch`和`delete`路线:

```
...
app.patch('/entries/:entryId', updateEntry)
app.delete('/entries/:entryId', deleteEntry)

```

我们的`index.ts`文件现在应该是这样的:

```
import * as functions from 'firebase-functions'
import * as express from 'express'
import { addEntry, getAllEntries, updateEntry, deleteEntry } from './entryController'

const app = express()

app.get('/', (req, res) => res.status(200).send('Hey there!'))
app.post('/entries', addEntry)
app.get('/entries', getAllEntries)
app.patch('/entries/:entryId', updateEntry)
app.delete('/entries/:entryId', deleteEntry)

exports.app = functions.https.onRequest(app)

```

为了测试新功能，我们将使用`npm run deploy`命令重新部署我们的应用程序，然后使用像 Postman 这样的 API 客户端向`/entries/:entryId`发送一个`PATCH`或`DELETE`请求。请记住，在创建条目时，我们在条目的主体中包含了一个`entryId`。我们可以通过向`./entries`发送`GET`请求来获得这些 id。

## 结论

在本文中，我们看到了如何设置 Firebase 并使用云函数通过 TypeScript 和 Firestore 构建 REST API。我们没有在这个演示中涵盖所有的 Firestore 方法，但是你可以在[官方文档](https://firebase.google.com/docs/firestore)中找到它们，根据你从这篇文章中获得的知识，它们会更容易理解。

在一个生产就绪的应用程序中，你需要设置中间件来处理很多情况；例如，我们可以有一个中间件，在尝试更新条目之前检查它是否存在。我们还可以有一个认证和授权中间件([这里有一个在控制器运行之前用 Firebase React apps](https://blog.logrocket.com/user-authentication-firebase-react-apps/) 处理认证的好方法。有了我们的应用程序结构，添加中间件将变得非常简单。

这是我们演示应用的 GitHub repo 的链接。如果你需要任何帮助，请随时在 [LinkedIn](https://linkedin.com/in/ebenezerdon) 上联系我！

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.