# 使用 Appwrite 和 Flutter:带示例的教程

> 原文：<https://blog.logrocket.com/appwrite-flutter-tutorial-with-examples/>

在本教程中，我们将演示如何用 Appwrite 开发一个 Flutter 应用程序。

我们将讨论以下内容:

在整个教程中，我们将通过构建一个演示应用程序来浏览一些实际示例，以便您可以看到这些 Appwrite 服务的运行。

## 什么是 Appwrite？

Appwrite 是一个开源、自托管的后端服务器，它实现了任何生产级应用程序后端所需的所有常见、繁琐和重复的任务。

Appwrite 可以在任何操作系统上运行。它提供控制台 UI 来管理各种服务，如用户身份验证、帐户管理、用户首选项、数据库和存储等等。

为了展示 Appwrite 可以做什么，我们将构建一个费用跟踪器应用程序，其中用户通过 Appwrite 用户服务进行身份验证。我们将使用数据库服务存储费用数据，并使用存储服务上传用户的个人资料图片。

下面是我们的示例应用程序完成后的样子:

![Example Expense Tracker App](img/be9b1ced6cfec68505639f0135f19aec.png)

## Appwrite 功能

Appwrite 提供以下服务:

### 数据库ˌ资料库

[数据库 API](https://appwrite.io/docs/server/database) 使您能够以集合和文档的形式存储与应用程序相关的数据。尽管它使用集合和文档，但数据是以结构化形式存储的，而不是 NoSql 格式。

数据库服务允许您查询、过滤和管理集合和文档。它还在集合级别强制执行读/写权限。

### 储存；储备

通过[存储](https://appwrite.io/docs/server/storage)服务，您可以上传和下载所有与应用相关的文件和媒体。您还可以在文件级别定义权限，以管理谁可以访问它。

### 用户

顾名思义， [Users](https://appwrite.io/docs/server/users) 服务用于管理项目中的用户。它使你能够在你的应用程序中实现认证，并支持广泛的 OAuth2 提供商，包括谷歌，脸书，Twitter 和 GitHub。

使用用户 API，您可以搜索、阻止和查看用户信息、当前会话和最新活动日志。

### 功能

[函数](https://appwrite.io/docs/server/functions) API 允许你基于任何事件运行任何后端相关代码。您可以基于 Appwrite 服务支持的事件触发函数。

此服务还使您能够在预定义的时间表中运行某项功能。

### 现场

[Locale](https://appwrite.io/docs/client/locale) 服务允许你找到用户的位置，并相应地定制应用。它还显示用户的 IP 地址、电话号码和当地货币。

## 安装 Appwrite

您可以在本地计算机或您选择的任何云提供商上安装 Appwrite 实例。

让我们来看看如何在你的电脑上安装 Appwrite。

首先，要在您的操作系统上运行 Appwrite 实例，您需要安装 [Docker Desktop](https://www.docker.com/products/docker-desktop) 应用程序。

安装 Docker 应用程序后，根据您的操作系统，在您的终端中点击以下命令之一。

对于 Mac 和 Linux:

```
docker run -it --rm \
    --volume /var/run/docker.sock:/var/run/docker.sock \
    --volume "$(pwd)"/appwrite:/usr/src/code/appwrite:rw \
    --entrypoint="install" \
    appwrite/appwrite:0.10.2

```

对于 Windows:

```
docker run -it --rm ^
    --volume //var/run/docker.sock:/var/run/docker.sock ^
    --volume "%cd%"/appwrite:/usr/src/code/appwrite:rw ^
    --entrypoint="install" ^
    appwrite/appwrite:0.10.2

```

点击上面的命令后，您将被询问一些与配置 Appwrite 实例相关的问题，比如设置端口号。您可以同意默认选项，也可以根据自己的喜好进行更改。例如，您可能决定将端口号更改为 4003。

安装完成后，确保可以通过访问[http://localhost:port number/](http://localhost:4003/)来访问 Appwrite 实例。

对于我们的例子，它是:

```
http://localhost:4003/

```

这是它的样子:

![Localhost Access](img/fa5cc01106ebf6ce41cb223a3c650657.png)

## 创建和配置 Appwrite 项目

现在是时候配置我们的 Appwrite 项目了。首先，您需要在控制台中创建一个项目。

要创建一个项目，点击底部的**创建项目**按钮，输入项目名称，点击**创建**。

创建项目后，您应该向项目中添加一个平台。平台只是简单的指不同的 app。如果你的目标是 Android 和 iOS 应用，那么你必须添加两个不同的平台。

要添加平台:

*   打开新创建的项目
*   点击底部的**添加平台**按钮
*   点击**新长笛应用**选项
*   在 **iOS** 选项卡中输入**应用名称**和**捆绑包 ID** 。您可以在 Xcode 中的 **General** 选项卡中找到您的应用程序主要目标的捆绑包 ID
*   现在选择**安卓**标签(在 **iOS** 标签旁边)，输入 **App 名称**和**包名**。你的包名一般是你 app 级`build.gradle`文件
    ![Register Flutter App ](img/ab1f8e53c627bfad79ed1f18efbd69db.png)中的`applicationId`

## 向 Flutter 应用程序添加 Appwrite

要使用任何 Appwrite 服务，您需要安装的最重要的插件是`[appwrite](https://pub.dev/packages/appwrite)`，它使 Flutter 应用程序能够与 Appwrite 服务器通信。

```
dependencies:
  flutter:
    sdk: flutter
  cupertino_icons: ^1.0.2
  appwrite: ^1.0.2

```

在`pubspec.yaml`文件中增加`[appwrite](https://pub.dev/packages/appwrite)`依赖项(如上图)，进入`pub get`命令:

```
flutter pub get

```

## 创建用户帐户

现在我们已经将 Appwrite SDK 集成到了我们的 Flutter 应用程序中，让我们从该应用程序创建一个用户帐户。

在我们开始与 Appwrite 服务器通信之前，首先我们需要初始化 SDK:

```
static const String endpoint = "http://192.168.0.2:4003/v1";
static const String projectId = "612f55b331ecf";

Client client = Client();
Account account;
client
    .setEndpoint(AppConstants.endpoint)
    .setProject(AppConstants.projectId);
account = Account(client);

```

在端点变量中，用您自己的私有 IP 地址替换该值。如果您有 Mac，您可以在网络设置中找到它。

![Network Settings IP Address](img/0e567dd95f8a436332a647a0f5a35ee7.png)

在`projectId`变量中，输入您的项目 ID。您可以从项目的**设置**页面(在左侧菜单上)获得它

设计注册页面的代码如下所示:

```
ListView(
  shrinkWrap: true,
  padding: const EdgeInsets.all(16.0),
  children: <Widget>[
    const SizedBox(height: 20.0),
    TextField(
      controller: _name,
      decoration: InputDecoration(hintText: "name"),
    ),
    const SizedBox(height: 10.0),
    TextField(
      controller: _email,
      decoration: InputDecoration(hintText: "email"),
    ),
    const SizedBox(height: 10.0),
    TextField(
      controller: _password,
      obscureText: true,
      decoration: InputDecoration(
        hintText: "password",
      ),
    ),
    const SizedBox(height: 10.0),
    Center(
      child: ElevatedButton(
        child: Text("Signup"),
        onPressed: () {
          AuthState state =
              Provider.of<AuthState>(context, listen: false);
          state.createAccount(_name.text, _email.text, _password.text);
        },
      ),
    )
  ],
)

```

下面是如何调用 API 来注册一个点击注册按钮的用户:

```
createAccount(String name, String email, String password) async {
  try {
    var result =
        await account.create(name: name, email: email, password: password);
    if (result.statusCode == 201) {
      _isLoggedIn = true;
      _user = await _getAccount();
      notifyListeners();
    }
  } catch (error) {
    print(error.message);
  }
}

```

`account.create`方法负责进行 API 调用。如果用户创建成功，我们将登录标志设置为`true`并刷新状态，以便显示主页。

新创建的用户将显示在 Appwrite 控制台的**用户**部分:

![Users Section](img/056f35192039f1fc7c37a7ff56f1c6d9.png)

现在，让我们使用刚刚创建的用户登录应用程序。登录页面的设计如下所示:

```
ListView(
  shrinkWrap: true,
  padding: const EdgeInsets.all(16.0),
  children: <Widget>[
    const SizedBox(height: 20.0),
    TextField(
      controller: _email,
      decoration: InputDecoration(hintText: "email"),
    ),
    const SizedBox(height: 10.0),
    TextField(
      controller: _password,
      obscureText: true,
      decoration: InputDecoration(
        hintText: "password",
      ),
    ),
    const SizedBox(height: 10.0),
    Center(
      child: ElevatedButton(
        child: Text("Login"),
        onPressed: () {
          AuthState state =
              Provider.of<AuthState>(context, listen: false);
          state.login(_email.text, _password.text);
        },
      ),
    ),
    const SizedBox(height: 20.0),
    TextButton(onPressed: () => Navigator.pushNamed(context, AppRoutes.signup), child: Text("Create account"))
  ],
)

```

登录页面由两个用于获取电子邮件和密码的`TextField`和一个用于调用登录 API 的`ElevatedButton`组成。

下面是实现登录方法的代码:

```
login(String email, String password) async {
  try {
    Response result =
        await account.createSession(email: email, password: password);
    if (result.statusCode == 201) {
      _isLoggedIn = true;
      _user = await _getAccount();
      notifyListeners();
    }
  } catch (error) {
    print(error.message);
  }
}

```

`account.createSession`方法负责登录用户。如果用户输入了有效且正确的凭证，我们将登录标志设置为`true`并刷新状态，以便显示主页。

## 向数据库添加数据

我们正在构建的演示应用程序的主要功能是记录日常开支的能力。要添加费用数据，我们首先需要在 Appwrite 控制台中创建一个数据库。

要在 Appwrite 中创建数据库:

*   点击左侧菜单上的**数据库**链接
*   点击**添加收藏**
*   输入收藏名称并点击**创建**
*   在集合内部，单击 **+ add** 为您创建的集合定义列名
*   您可以添加任意多的列(例如，标题、描述、用户 ID 数量、创建日期、更新日期等。)
*   最后，在集合级别设置权限。出于演示目的，我们将通过在**读写访问**输入框
    ![Create Users](img/6b70d56d41f594bed985288bbc6ae38c.png)中输入`*`值来保持其打开

添加费用条目的代码如下:

```
Client client = Client();
Database db;
client
    .setEndpoint(AppConstants.endpoint)
    .setProject(AppConstants.projectId);
db = Database(client);
final String collectionId = "xyz";
Future addTransaction(Transaction transaction) async {
  try {
    Response res = await db.createDocument(
        collectionId: collectionId,
        data: transaction.toJson(),
        read: ["user:${transaction.userId}"],
        write: ["user:${transaction.userId}"]);
    transactions.add(Transaction.fromJson(res.data));
    notifyListeners();
    print(res.data);
  } catch (e) {
    print(e.message);
  }
}

```

将`xyz`替换为您的收藏 ID，您可以在**设置**选项卡下的收藏中找到该 ID。

`db.createDocument`方法负责将费用条目作为文档添加到指定的集合中。

新创建的费用条目将显示在集合中，如下所示:

![Document List ](img/c04e2f684f6bb24f0c2ad59c3a122265.png)

## 使用存储服务上传图像

假设用户想要设置或更改他们的默认个人资料图片。我们将使用 Appwrite 的 Storge 服务来上传和存储用户的照片。

首先，通过将 [CircleAvtar](https://api.flutter.dev/flutter/material/CircleAvatar-class.html) 小部件(显示默认图片)包装在 [InkWell](https://api.flutter.dev/flutter/material/InkWell-class.html) 小部件中来启用 onclick 事件:

```
InkWell(
  onTap: () => _uploadPic(context),
  child: CircleAvatar(
    radius: 40,
    backgroundImage: file != null ? Image.file(
      file,
      //fit: BoxFit.cover,
    ).image : null,
  ),
)

```

现在，编写一个实际上传图像的方法:

```
_uploadPic(BuildContext context) async {
  XFile image = await ImagePicker().pickImage(source: ImageSource.gallery);
  setState(() {
    file = File(image.path);
  });
  if (file != null) {
    final upfile = await MultipartFile.fromFile(file.path,
        filename: file.path.split('/').last);
    AuthState state = context.read<AuthState>();
    Response res = await state.storage.createFile(
        file: upfile, read: ["*"], write: ["user:${state.user.id}"]);
    if (res.statusCode == 201) {
      String id = res.data["\$id"];

    }
  }
}

```

来自 [image_picker](https://pub.dev/packages/image_picker) 的`await ImagePicker().pickImage ()`方法用于从图库中选取图像。

所选图像被设置为 CircleAvatart 小部件，然后使用`await state.storage.createFile`方法上传到 Appwrite 服务器。

在 [GitHub](https://github.com/pinkeshdarji/appwrite) 上找到本演示中使用的完整代码。

## 结论

在本教程中，我们演示了如何将 Appwrite 集成到 Flutter 应用程序中。我们还通过实例详细了解了如何使用各种 Appwrite 服务，比如用户、数据库和存储 API。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)