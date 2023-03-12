# 在 Flutter 中使用 Supabase 进行认证

> 原文：<https://blog.logrocket.com/supabase-authentication-flutter/>

在我们的大多数应用程序中，我们对用户进行身份验证，以跟踪他们的活动，并为他们提供个性化的体验。有很多方法可以实现这一点，尤其是在颤振中。一些流行的方法包括通过使用 Firebase 或构建我们自己的自定义 API 将身份验证添加到我们的应用程序中，并将其集成到我们的应用程序中来处理用户身份验证。

然而，这些选项可能不是您的用例的最佳选择。本文将向您介绍 Firebase 替代方案 Supabase 的功能，以及如何使用 Supabase 向您的 Flutter 应用程序添加身份验证。

## 什么是 Supabase？

Supabase 是 Firebase 的开源替代方案。

Firebase 是谷歌推出的后端即服务(BaaS ),使开发人员能够创建即时可用的 iOS、Android 和 web 应用程序，以集成用于跟踪分析、报告和修复应用程序崩溃、身份验证、存储和 NoSQL 数据库的工具。

尽管 Firebase 有很多功能，但 Supabase 更健壮，可以在各种平台上运行。它的可移植性使得它非常适合寻找简单灵活的方式来集成第三方应用程序的开发人员。

Supabase 提供了一个简单的 API 来整合第三方认证服务，如 Google、Apple、Twitter、脸书、GitHub、Azure、GitLab 和 Bitbucket。它还支持基于 SAML 的企业登录。

Supabase 和 Firebase 的一个核心区别是，虽然 Firebase 不是开源的，但 Supabase 目前也是免费使用的，没有任何付费计划。请注意，当前唯一有效的主要特性是数据库、认证和存储；云功能等其他功能仍在开发中。

## 入门指南

在今天的文章中，我们将探索认证如何与 Supabase 一起工作。

为了使用 Supabase，您需要通过登录创建一个新项目。登录后，单击按钮创建一个新项目，如下图所示。

![Create A New Project](img/d4100953d16d7846ca2bf298a0cad208.png)

将出现一个窗口提示您输入您的组织名称，之后，您将进入如下所示的新屏幕。

![Proceed To Next Screen](img/7a40d8831676322e5ef7a5c12fc04e5c.png)

一旦你完成了基本细节的填写，并且你的项目已经在 Supabase 仪表板上设置好，从我们的 Flutter 应用程序连接到我们的 Supabase 后端需要做两件重要的事情。这些是:

```
-a Supabase URL 
-and a Supabase public API key

```

要获得这些，只需点击**设置**选项卡。

![Click On Settings Tab](img/87c4ef8c9ee78e8c5d7ea55827968581.png)

然后选择 **API** ，你会看到你的 URL 和 API 密匙。您现在可以复制这些并保存在您的应用程序中。

![Select API](img/ca60c432766988199c366a07928e4dc0.png)

Supabase 默认情况下会启用电子邮件验证，这意味着在启用此设置的情况下使用 Supabase 身份验证时，您的用户需要验证他们的电子邮件地址才能激活他们的帐户。

为简单起见，由于这只是一个教程，我们将禁用电子邮件验证。您可以在闲暇时随意使用和探索其他选项。

到你的 Supabase 仪表盘上的**设置**选项卡，点击**授权设置**，然后点击**配置**选项卡下的**设置**。

在那里，您可以关闭**启用电子邮件确认**。

![Toggle Off Enable Email Confirmations](img/061b84367f6f74090ba5b40223002ce7.png)

## 创建一个新的颤振项目

在这里，我们将创建一个简单的颤振项目。我们将有三个小部件:创建一个帐户，登录现有用户，以及一个简单的主屏幕，告诉你是否登录。

## 创建一个 Supabase 服务类

但在此之前，让我们创建一个 Supabase 管理器类来处理我们所有的初始化和认证功能:

```
import 'package:flutter/material.dart';
import 'package:supabase/supabase.dart';
import 'package:supabase_authentication/core/toast.dart';

const String supabaseUrl = "your supabase url goes here ";
const String token =
    "your supabase token goes here";

class SupabaseManager {
  final client = SupabaseClient(supabaseUrl, token);

  Future<void> signUpUser(context, {String? email, String? password}) async {
    debugPrint("email:$email password:$password");
    final result = await client.auth.signUp(email!, password!);

    debugPrint(result.data!.toJson().toString());

    if (result.data != null) {
      showToastMessage('Registration Success', isError: false);
      Navigator.pushReplacementNamed(context, 'login');
      showToastMessage('Success', isError: false);
    } else if (result.error?.message != null) {
      showToastMessage('Error:${result.error!.message.toString()}',
          isError: true);
    }
  }

  Future<void> signInUser(context, {String? email, String? password}) async {
    debugPrint("email:$email password:$password");
    final result = await client.auth.signIn(email: email!, password: password!);
    debugPrint(result.data!.toJson().toString());

    if (result.data != null) {
      showToastMessage('Login Success', isError: false);
      Navigator.pushReplacementNamed(context, '/home');
      showToastMessage('Success', isError: false);
    } else if (result.error?.message != null) {
      showToastMessage('Error:${result.error!.message.toString()}',
          isError: true);
    }
  }

  Future<void> logout (context)async{
    await client.auth.signOut();
    Navigator.pushReplacementNamed(context, 'login');
  }
}

```

在上面的文件中，我们有两个变量来存储我们的令牌和 Supabase URL。我们还创建了一个类，在这个类中我们有一个对象`SupabaseClient`,它是我们调用 Supabase 的所有 API 所必需的。它使用我们的 URL 和令牌实例化。

随后，我们有注册，登录和注销功能。

登录和注册函数都接受电子邮件和密码作为参数，然后将它们传递给`SupabaseClient`对象上的登录和注册方法。结果是来自`SupabaseClient`类的一个`session`对象，我们检查它以知道我们的请求是否成功。然后，向用户显示一条消息。

当登录完成时，用户被导航到主屏幕页面，对于注册，用户被带到登录页面。

注销功能就像它所说的那样，使用`SupabaseClient`对象注销用户并导航回我们的认证屏幕。

## 在我们的应用中设置路线

在我们的主要活动中，我们在材料应用程序中定义了路线:

```
import 'package:flutter/material.dart';
import 'package:supabase_authentication/login.dart';
import 'package:supabase_authentication/signup.dart';

import 'homescreen.dart';

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({Key? key}) : super(key: key);

  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Supabase Demo',
      debugShowCheckedModeBanner: false,
      initialRoute: 'login',
      routes: {
        'login': (_) => const LoginPage(),
        '/signup': (_) => const SignUpPage(),
        '/home': (_) => const HomeScreen(),
      },
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: const LoginPage(),
    );
  }
}

```

## 创建用户界面

在下一部分中，我们将简单地创建应用程序的可视部分，主要包括注册页面和登录页面，以及在成功认证后将用户带到的最终主屏幕。

这是我们注册页面的样子:

```
import 'package:flutter/material.dart';
import 'package:supabase_authentication/homescreen.dart';
import 'package:supabase_authentication/login.dart';
import 'package:supabase_authentication/supabase_utils.dart';

class SignUpPage extends StatefulWidget {
  const SignUpPage({Key? key}) : super(key: key);

  @override
  _SignUpPageState createState() => _SignUpPageState();
}

class _SignUpPageState extends State<SignUpPage> {
  final _supabaseClient = SupabaseManager();
  final _emailController = TextEditingController();
  final _passwordController = TextEditingController();
  final _formKey = GlobalKey<FormState>();

  @override
  void dispose() {
    _emailController.dispose();
    _passwordController.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Colors.white,
      appBar: AppBar(
        centerTitle: true,
        title: const Text("Sign Up Page"),
      ),
      body: SingleChildScrollView(
        child: Form(
          key: _formKey,
          child: Column(
            children: <Widget>[
              const SizedBox(
                height: 200,
              ),
              Padding(
                padding: const EdgeInsets.symmetric(horizontal: 15),
                child: TextFormField(
                  controller: _emailController,
                  decoration: const InputDecoration(
                      border: OutlineInputBorder(),
                      labelText: 'Email',
                      hintText: 'Enter a valid email'),
                  validator: (String? value) {
                    if (value!.isEmpty || !value.contains('@')) {
                      return 'Email is not valid';
                    }
                  },
                ),
              ),
              Padding(
                padding: const EdgeInsets.only(
                    left: 15.0, right: 15.0, top: 15, bottom: 0),
                //padding: EdgeInsets.symmetric(horizontal: 15),
                child: TextFormField(
                  obscureText: true,
                  controller: _passwordController,
                  decoration: const InputDecoration(
                      border: OutlineInputBorder(),
                      labelText: 'Password',
                      hintText: 'Enter secure password'),
                  validator: (String? value) {
                    if (value!.isEmpty) {
                      return 'Invalid password';
                    }
                  },
                ),
              ),
              const SizedBox(
                height: 20,
              ),
              Container(
                height: 50,
                width: 250,
                decoration: BoxDecoration(
                    color: Colors.blue,
                    borderRadius: BorderRadius.circular(20)),
                child: TextButton(
                  onPressed: () {
    if (_formKey.currentState!.validate()) {
                    _supabaseClient.signUpUser(context,
                        email: _emailController.text,
                        password: _passwordController.text);}
                  },
                  child: const Text(
                    'Sign Up',
                    style: TextStyle(color: Colors.white, fontSize: 25),
                  ),
                ),
              ),
              const SizedBox(
                height: 130,
              ),
              TextButton(
                  onPressed: () {
                      Navigator.push(context,
                          MaterialPageRoute(builder: (_) => const LoginPage()));

                  },
                  child: const Text('Already a User? Login'))
            ],
          ),
        ),
      ),
    );
  }
}

```

我们的登录小部件也很相似:

```
import 'package:flutter/material.dart';
import 'package:supabase_authentication/signup.dart';
import 'package:supabase_authentication/supabase_utils.dart';

class LoginPage extends StatefulWidget {
  const LoginPage({Key? key}) : super(key: key);

  @override
  _LoginPageState createState() => _LoginPageState();
}

class _LoginPageState extends State<LoginPage> {
  final _supabaseClient = SupabaseManager();
  final _emailController = TextEditingController();
  final _passwordController = TextEditingController();
  final _formKey = GlobalKey<FormState>();

  @override
  void dispose() {
    _emailController.dispose();
    _passwordController.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Colors.white,
      appBar: AppBar(
        centerTitle: true,
        title: const Text("Login Page"),
      ),
      body: SingleChildScrollView(
        child: Form(
          key: _formKey,
          child: Column(
            children: <Widget>[
              const SizedBox(
                height: 200,
              ),
              Padding(
                padding: const EdgeInsets.symmetric(horizontal: 15),
                child: TextFormField(
                  controller: _emailController,
                  decoration: const InputDecoration(
                      border: OutlineInputBorder(),
                      labelText: 'Email',
                      hintText: 'Enter a valid email'),
                  validator: (String? value) {
                    if (value!.isEmpty || !value.contains('@')) {
                      return 'Email is not valid';
                    }
                  },
                ),
              ),
              Padding(
                padding: const EdgeInsets.only(
                    left: 15.0, right: 15.0, top: 15, bottom: 0),
                //padding: EdgeInsets.symmetric(horizontal: 15),
                child: TextFormField(
                  controller: _passwordController,
                  obscureText: true,
                  decoration: const InputDecoration(
                      border: OutlineInputBorder(),
                      labelText: 'Password',
                      hintText: 'Enter secure password'),
                  validator: (String? value) {
                    if (value!.isEmpty) {
                      return 'Invalid password';
                    }
                  },
                ),
              ),
              TextButton(
                onPressed: () {},
                child: const Text(
                  'Forgot Password',
                  style: TextStyle(color: Colors.blue, fontSize: 15),
                ),
              ),
              Container(
                height: 50,
                width: 250,
                decoration: BoxDecoration(
                    color: Colors.blue,
                    borderRadius: BorderRadius.circular(20)),
                child: TextButton(
                  onPressed: () {
                    if (_formKey.currentState!.validate()) {
                    _supabaseClient.signInUser(context,
                        email: _emailController.text,
                        password: _passwordController.text);}
                  },
                  child: const Text(
                    'Login',
                    style: TextStyle(color: Colors.white, fontSize: 25),
                  ),
                ),
              ),
              const SizedBox(
                height: 130,
              ),
              TextButton(
                  onPressed: () {

                      Navigator.push(
                          context,
                          MaterialPageRoute(
                              builder: (_) => const SignUpPage()));

                  },
                  child: const Text('New User? Create Account')),
              const SizedBox(
                height: 30,
              ),
            ],
          ),
        ),
      ),
    );
  }
}

```

最后，我们有我们的主屏幕:

```
import 'package:flutter/material.dart';
import 'package:supabase_authentication/supabase_utils.dart';

class HomeScreen extends StatefulWidget {
  const HomeScreen({Key? key}) : super(key: key);

  @override
  _HomeScreenState createState() => _HomeScreenState();
}

class _HomeScreenState extends State<HomeScreen> {
  final _supabaseClient = SupabaseManager();
  @override
  Widget build(BuildContext context) {
    return  Scaffold(
        backgroundColor: Colors.white,
        appBar: AppBar(
        centerTitle: true,
        title: const Text("Home Page"),
          actions: <Widget>[
            PopupMenuButton<String>(
              onSelected: handleClick,
              itemBuilder: (BuildContext context) {
                return {'Logout',}.map((String choice) {
                  return PopupMenuItem<String>(
                    value: choice,
                    child: Text(choice),
                  );
                }).toList();
              },
            ),
          ],
    ),
    body:const SizedBox(
      height: double.infinity,
      child: Center(child: Text("You are successfully logged in"),)
    )

    );
  }

  void handleClick(String value) {
    switch (value) {
      case 'Logout':
_supabaseClient.logout(context);
        break;

    }
  }
}

```

## 最后的想法

Supabase 使向您的应用程序添加后端解决方案的过程变得无缝。身份验证是大多数应用程序中的一个基本特性，使用 Supabase，您可以轻松解决这个问题，而不必从头开始构建自己的定制解决方案。

还要记住，除了本文中介绍的认证形式之外，Supabase 还支持其他形式的认证。像谷歌、脸书、GitHub 等社交认证提供商也得到 Supabase 的支持。有关这方面的更多信息，只需查看 [Supabase 文档](https://supabase.com/docs)以了解更多信息。

如果你发现这篇文章是关于如何构建一个签名捕获应用程序的，或者有任何问题，请在下面留言。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)