# 如何使用 SQLite - LogRocket Blog 在 Flutter 中持久化数据

> 原文：<https://blog.logrocket.com/flutter-sqlite-how-to-persist-data/>

定义一个应用程序是否高性能的最大因素之一是它如何在本地获取和存储数据。本地存储中的持久数据在移动应用程序开发的早期就已经存在。

我们将在本 Flutter 教程中使用的 SQLite 数据库是一个持久的数据存储，即使在应用程序关闭后也能保存数据。这意味着，即使用户重启应用程序或设备本身，我们存储在数据库中的所有内容都会保留下来。

在本指南中，我们将向您展示如何创建一个简单的 Flutter 应用程序，它接受用户使用表单的一些细节，并将其存储在数据库中。所以，启动你最喜欢的文本编辑器，让我们开始吧！

## 创建一个新的颤振项目

首先，打开您的终端并创建一个新的 Flutter 项目:

```
flutter create sqlite_demo
cd sqlite_demo

```

接下来，添加所需的依赖项。对于本文，您将需要使用 SQLite 数据库的 [sqflite 包](https://pub.dev/packages/sqflite)。

```
dependencies:
  flutter:
    sdk: flutter
  sqflite: any

```

打开模拟器设备或将真实设备连接到您的系统，并使用以下命令启动应用程序:

```
flutter run

```

对于这个应用程序，您需要一个[有状态小部件来本地管理表单的状态](https://blog.logrocket.com/flutter-state-management-methods/)。

```
import 'package:flutter/material.dart';

void main() {
  runApp(SqliteDemoApp());
}

class SqliteDemoApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'SQLite demo',
      theme: ThemeData(
        primarySwatch: Colors.purple,
      ),
      home: MainApp(title: 'SQLite demo'),
    );
  }
}

class MainApp extends StatefulWidget {
  MainApp({Key? key, this.title}) : super(key: key);
  final String? title;

  @override
  _MainAppState createState() => _MainAppState();
}

```

## `DatabaseHelper`类

为了访问和使用数据库，创建一个 singleton `DatabaseHelper`类。一个单例类将确保我们只有一个数据库连接的实例和对数据库的全局访问，可以从项目中的任何地方调用。

在 Dart 中，`factory`关键字用于创建一个只返回一个实例的构造函数。

```
import 'dart:async';

import 'package:path/path.dart';

class DatabaseHelper {
  static final DatabaseHelper _databaseHelper = DatabaseHelper._();

  DatabaseHelper._();

  late Database db;

  factory DatabaseHelper() {
    return _databaseHelper;
  }
}

```

在这个项目中，我们将在数据库中创建一个 users 表，其中包含 ID、姓名、电子邮件和年龄列。创建该表的 SQL 查询如下:

```
CREATE TABLE users (
  id INTEGER PRIMARY KEY AUTOINCREMENT, 
  name TEXT NOT NULL,
  age INTEGER NOT NULL, 
  email TEXT NOT NULL
)

```

在`DatabaseHelper`类中，添加一个`initDB()`异步函数，它将连接到 SQLite 数据库。`getDatabasesPath()`方法将给出数据库文件的默认位置，在本例中，您需要在该位置附加数据库的名称— `users_demo.db`。

`openDatabase()`函数用于打开数据库，。它接受数据库文件的路径、版本号和一个可选的`onCreate`回调，该回调在第一次创建数据库时执行。

在`onCreate`回调中，您需要创建 users 表，以便它可以用来存储用户信息和进行 CRUD 操作。

```
class DatabaseHelper {
  // ...
  Future<void> initDB() async {
    String path = await getDatabasesPath();
    db = await openDatabase(
      join(path, 'users_demo.db'),
      onCreate: (database, version) async {
        await database.execute(
          """
            CREATE TABLE users (
              id INTEGER PRIMARY KEY AUTOINCREMENT, 
              name TEXT NOT NULL,
              age INTEGER NOT NULL, 
              email TEXT NOT NULL
            )
          """,
        );
      },
      version: 1,
    );
  }
// ...
}

```

## 模型类

接下来，让我们创建一个模型类来表示数据库中的用户数据。一个简单的数据模型类将提供处理 SQLite 友好格式所需的方法，方法是将它转换成 Dart 对象，以便以后在应用程序中使用。

```
class User {
  int? id;
  String name;
  int age;
  String email;

  User({this.id, required this.name, required this.age, required this.email});

  User.fromMap(Map<String, dynamic> res)
      : id = res["id"],
        name = res["name"],
        age = res["age"],
        email = res["email"];

  Map<String, Object?> toMap() {
    return {'id': id, 'name': name, 'age': age, 'email': email};
  }
}

```

创建用户模型类是为了定义数据库中的用户表可以预期的属性。用户模型类由`name`、`age`和`email`属性组成，并且有一个用于创建新用户实例的构造函数。

对于 SQLite DB 使用的用户类和地图对象实例之间的转换，已经定义了方法`toMap()`和`fromMap()`。

## CRUD 操作

现在让我们回到`DatabaseHelper`类，编写 DB 方法来执行 CRUD 操作。

在`initDB()`方法中，`database`类的一个实例被分配给`DatabaseHelper`类的`db`属性。`database`实例可以访问诸如`insert()`、`update()`、`query()`和`delete()`之类的方法，用于在数据库上执行 CRUD 操作。

```
class DatabaseHelper {
  // ...
  Future<int> insertUser(User user) async {
    int result = await db.insert('users', user.toMap());
    return result;
  }

  Future<int> updateUser(User user) async {
    int result = await db.update(
      'users',
      user.toMap(),
      where: "id = ?",
      whereArgs: [user.id],
    );
    return result;
  }

  Future<List<User>> retrieveUsers() async {
    final List<Map<String, Object?>> queryResult = await db.query('users');
    return queryResult.map((e) => User.fromMap(e)).toList();
  }

  Future<void> deleteUser(int id) async {
    await db.delete(
      'users',
      where: "id = ?",
      whereArgs: [id],
    );
  }
// ...
}

```

## 添加用户

是时候开始构建应用程序的用户界面了。

在本节中，我们将创建一个如下所示的表单，其中包括姓名、年龄和电子邮件字段。当点击 **S** *ubmit* 按钮时，这些字段中的数据将被存储在用户表的相应列中。

在小部件的主 state 类中，初始化用于访问`DatabaseHelper`类、`User`类、姓名、年龄和电子邮件的属性。覆盖`initState()`生命周期方法并初始化数据库，以便在创建小部件时可用。

其余的 widget `build()`方法由基本的[F](https://blog.logrocket.com/best-practices-laying-out-flutter-app/)[lutter widget](https://blog.logrocket.com/best-practices-laying-out-flutter-app/)[s](https://blog.logrocket.com/best-practices-laying-out-flutter-app/)组成，例如`Form`、`TextFormField`和`ElevatedButton`，用于创建表单的 UI。

```
import 'package:flutter/services.dart';
import 'package:sqlite_demo/model/user.dart';
import 'package:sqlite_demo/utils/database_helper.dart';

// ...
class _MainAppState extends State<MainApp> {
  late DatabaseHelper dbHelper;
  final nameController = TextEditingController();
  final ageController = TextEditingController();
  final emailController = TextEditingController();
  bool isEditing = false;
  late User _user;

  @override
  void initState() {
    super.initState();
    this.dbHelper = DatabaseHelper();
    this.dbHelper.initDB().whenComplete(() async {
      setState(() {});
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
        resizeToAvoidBottomInset: false,
        appBar: AppBar(
          title: Text(widget.title!),
        ),
        body: Column(
          children: <Widget>[
            Expanded(
                child: new Column(
              children: [
                Padding(
                    padding: EdgeInsets.all(8.0),
                    child: Form(
                        child: Column(
                            crossAxisAlignment: CrossAxisAlignment.start,
                            children: [
                          TextFormField(
                            controller: nameController,
                            decoration: const InputDecoration(
                                hintText: 'Enter your name', labelText: 'Name'),
                          ),
                          TextFormField(
                            controller: ageController,
                            keyboardType: TextInputType.number,
                            inputFormatters: [
                              FilteringTextInputFormatter.allow(
                                  RegExp(r'[0-9]')),
                            ],
                            decoration: const InputDecoration(
                                hintText: 'Enter your age', labelText: 'Age'),
                          ),
                          TextFormField(
                            controller: emailController,
                            decoration: const InputDecoration(
                                hintText: 'Enter your email',
                                labelText: 'Email'),
                          ),
                          Row(
                              mainAxisAlignment: MainAxisAlignment.start,
                              children: [
                                new Container(
                                    margin:
                                        new EdgeInsets.symmetric(vertical: 10),
                                    child: new ElevatedButton(
                                      child: const Text('Submit'),
                                      onPressed: addOrEditUser,
                                    )),
                              ])
                        ]))),
                Expanded(
                  flex: 1,
                  child: SafeArea(child: userWidget()),
                )
              ],
            )),
          ],
        ));
  }

  // ...
}

```

让我们在`_MainAppState`类中添加 CRUD 逻辑。

在`addOrEditUser()`方法中，从 email、age 和 name 字段中获取值。根据当前是否正在编辑数据，使用`dbHelper`实例添加或更新表中的用户记录。

```
class _MainAppState extends State<MainApp> { 
  // ...
  Future<void> addOrEditUser() async {
    String email = emailController.text;
    String name = nameController.text;
    String age = ageController.text;

    if (!isEditing) {
      User user = new User(name: name, age: int.parse(age), email: email);
      await addUser(user);
    } else {
      _user.email = email;
      _user.age = int.parse(age);
      _user.name = name;
      await updateUser(_user);
    }
    resetData();
    setState(() {});
  }

  Future<int> addUser(User user) async {
    return await this.dbHelper.insertUser(user);
  }

  Future<int> updateUser(User user) async {
    return await this.dbHelper.updateUser(user);
  }

  void resetData() {
    nameController.clear();
    ageController.clear();
    emailController.clear();
    isEditing = false;
  }

  // ...
}

```

## 显示用户列表

使用 [`FutureBuilder`小部件](https://blog.logrocket.com/async-callbacks-with-flutter-futurebuilder/)，通过查询用户表显示用户列表。

从数据库查询是一个异步操作，因此需要由未来来处理。`FutureBuilder`小部件有助于确定未来的当前状态，并允许您在异步操作进行时显示不同的视图。

将`retrieveUsers()`方法作为未来传递给`FutureBuilder`小部件，并显示一个`CircularProgress`小部件，直到数据加载完毕。

使用 [`ListView`小部件](https://blog.logrocket.com/creating-listviews-in-flutter/)，迭代用户数据并将其显示在屏幕上。用`Dismissible`小部件包装单个用户数据，以便当用户滑动数据时，执行删除操作。

要删除用户记录，调用`Dismissible`小部件的`onDismissed`选项中的`deleteUser()`方法。

```
Widget userWidget() {
    return FutureBuilder(
      future: this.dbHelper.retrieveUsers(),
      builder: (BuildContext context, AsyncSnapshot<List<User>> snapshot) {
        if (snapshot.hasData) {
          return ListView.builder(
              itemCount: snapshot.data?.length,
              itemBuilder: (context, position) {
                return Dismissible(
                    direction: DismissDirection.endToStart,
                    background: Container(
                      color: Colors.red,
                      alignment: Alignment.centerRight,
                      padding: EdgeInsets.symmetric(horizontal: 10.0),
                      child: Icon(Icons.delete_forever),
                    ),
                    key: UniqueKey(),
                    onDismissed: (DismissDirection direction) async {
                      await this
                          .dbHelper
                          .deleteUser(snapshot.data![position].id!);
                    },
                    child: new GestureDetector(
                      behavior: HitTestBehavior.opaque,
                      onTap: () => populateFields(snapshot.data![position]),
                      child: Column(
                        children: <Widget>[
                          Row(
                            mainAxisAlignment: MainAxisAlignment.spaceBetween,
                            children: <Widget>[
                              Column(
                                crossAxisAlignment: CrossAxisAlignment.start,
                                children: <Widget>[
                                  Padding(
                                    padding: const EdgeInsets.fromLTRB(
                                        12.0, 12.0, 12.0, 6.0),
                                    child: Text(
                                      snapshot.data![position].name,
                                      style: TextStyle(
                                          fontSize: 22.0,
                                          fontWeight: FontWeight.bold),
                                    ),
                                  ),
                                  Padding(
                                    padding: const EdgeInsets.fromLTRB(
                                        12.0, 6.0, 12.0, 12.0),
                                    child: Text(
                                      snapshot.data![position].email.toString(),
                                      style: TextStyle(fontSize: 18.0),
                                    ),
                                  ),
                                ],
                              ),
                              Padding(
                                padding: const EdgeInsets.all(8.0),
                                child: Column(
                                  mainAxisAlignment:
                                      MainAxisAlignment.spaceEvenly,
                                  children: <Widget>[
                                    Container(
                                      decoration: BoxDecoration(
                                          color: Colors.black26,
                                          borderRadius:
                                              BorderRadius.circular(100)),
                                      child: Padding(
                                        padding: const EdgeInsets.all(8.0),
                                        child: Text(
                                          snapshot.data![position].age
                                              .toString(),
                                          style: TextStyle(
                                            fontSize: 16,
                                            color: Colors.white,
                                          ),
                                        ),
                                      ),
                                    )
                                  ],
                                ),
                              ),
                            ],
                          ),
                          Divider(
                            height: 2.0,
                            color: Colors.grey,
                          )
                        ],
                      ),
                    ));
              });
        } else {
          return Center(child: CircularProgressIndicator());
        }
      },
    );
  }

```

每当点击一个单独的用户数据时，我们将使用该数据填充表单，并将`isEditing`标志设置为`true`。

```
void populateFields(User user) {
    _user = user;
    nameController.text = _user.name;
    ageController.text = _user.age.toString();
    emailController.text = _user.email;
    isEditing = true;
}

```

## 结论

在本指南中，我们介绍了如何使用`sqflite` Flutter 包在 SQLite 中访问和运行数据库查询。

客户端应用程序级别的数据持久性至关重要。没有它，最终用户不得不依赖互联网来存储和访问数据。当用户拥有强大的网络连接时，您可以稍后将数据从本地数据库同步到您的服务器。

干杯，继续加油。