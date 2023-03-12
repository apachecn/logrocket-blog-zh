# 在 Flutter - LogRocket 博客中实现存储库模式

> 原文：<https://blog.logrocket.com/implementing-repository-pattern-flutter/>

颤振开发人员使用各种设计模式来编写干净和可维护的代码库。他们经常在单独的 Dart 文件中编写可重用的小部件实现，将主应用程序屏幕分成不同的文件，并将大的孤立的小部件分解成私有的方法/类。

但是我们也可以在我们的 Flutter 应用中使用通用软件设计模式来提高代码库的质量。例如，像 MVC(模型-视图-控制器)架构、存储库模式、服务模型模式和数据传输对象(DTO)这样的设计概念也可以帮助我们编写可管理的代码库。存储库模式尤其促使我们通过额外的抽象层将数据访问逻辑(数据库层接口)从业务逻辑中分离出来。

几乎所有的 Flutter apps 都使用数据源进行数据持久化和检索。存储库模式有助于所有 Flutter 开发人员学习如何以更好、更易管理的方式组织他们的代码。在本教程中，我将解释如何在你的 Flutter 应用程序中实现一个存储库模式。

## 什么是存储库模式？

存储库模式是一种软件设计模式，它通过引入一个称为存储库的集中式组件，将数据访问逻辑与业务逻辑分离。看下图。![Diagram Of Repository Pattern](img/42b501261eed5b6e62fe726ab4876e8e.png)

As you can see in the above diagram, the generic repository pattern consists of three inter-connected components:

1.  客户端—指发起数据请求的组件，如控制器或服务
2.  存储库—通过特定的 API 以域友好的格式提供数据，不允许客户端直接访问数据源中的数据
3.  数据源—根据特定于数据层的格式提供数据记录；数据源可以是 RESTful API、SQLite 连接或 MongoDB 连接

假设我们要构建一个名为`BookRepository`的存储库。`BookRepository`可以公开一个方法来列出给定数据库中的所有书名。然后，在任何需要检索`books`的地方，都可以访问`BookRepository`，而不是编写重复的代码从数据源中查找书籍。

存储库模式为我们提供了以下主要好处:

*   它提供了一种从集中位置访问数据的方法，以防止与数据访问相关的代码重复
*   代码库变得更加单元可测试，因为数据层从业务逻辑中分离出来
*   我们可以轻松地切换数据源，而不需要进行耗时的代码更改

## 颤振存储库模式教程

现在我们知道了存储库模式背后的理论背景，让我们在我们的 Flutter 应用程序中实现一个。

与任何其他设计模式原则一样，关于如何在现实世界的应用程序中实现存储库模式，确实没有严格的规则——这种模式教给你一个通用的设计概念来解耦数据访问逻辑。然而，大多数开发人员建议从存储库中公开 CRUD(创建、读取、更新和删除)操作。此外，作为最佳实践，我们将为每个业务实体创建一个存储库。

让我们用基于 CRUD 的存储库模式实现一个简单的书店应用程序。注意，我们将使用一个虚拟数据层(一个模拟数据库提供者)作为我们的数据提供者基础设施，只关注存储库模式的实现。您可以用这个虚拟数据层模块替换任何数据层 API，比如 SQLite、Firebase 或其他 RESTful API。

## 设置环境

您可以通过创建一个新的 Flutter 应用程序来尝试这个示例应用程序，或者您可以在您现有的 Flutter 项目中使用这些设计原则。如果您计划开始一个新项目，请输入以下命令来创建书店应用程序。

```
flutter create repo_bookstore

```

使用以下命令运行应用程序以启动它。您可以在 Chrome、mobile 或桌面 window 上测试这个示例，因为 Flutter 是一个通用的应用程序开发框架。

```
cd repo_bookstore
flutter run

```

## 定义模型和建立存储库

存储库不公开内部数据源的结构或数据格式——它们通过模型公开数据，也称为 dto。

书店应用程序会显示一些关于书籍的细节，所以我们需要创建一个书籍模型。将以下代码添加到`./lib/models/book.dart`

```
class Book {
  final int id;
  final String title;
  final int year;

  Book(this.id, this.title, this.year);

  Book.fromMap(Map<String, dynamic> data) :
    id = data['id'],
    title = data['title'],
    year = data['year'];

  Map<String, dynamic> toMap() {
    return {
      'id': id,
      'title': title,
      'year': year
    };
  }
}

```

上面的代码用几个属性和方法为`Book`业务实体定义了一个模型。我们计划用 [Dart Maps](https://blog.logrocket.com/dart-flutter-data-structures-comprehensive-guide/) 构建一个虚拟数据库访问层，所以在这里，我们创建了`fromMap`和`toMap`辅助函数用于类型转换。

现在我们需要创建一个虚拟数据访问层。这个模块将使用一个简单的 Dart 类模拟一个数据提供者，如 RESTful API 或数据库连接。将以下代码添加到`lib/db/virtual_db.dart`。

```
import 'dart:math';

class VirtualDB {
  List<Map<String, dynamic>> _items = [];
  static final VirtualDB _db = VirtualDB._privateConstructor();

  VirtualDB._privateConstructor();

  factory VirtualDB() {
    return _db;
  }

  Future<void> insert(Map<String, dynamic> item) async {
    item['id'] = Random().nextInt(1000);
    _items.add(item);
  }

  Future<void> remove(int id) async {
    _items.removeWhere((item) => item['id'] == id);
  }

  Future<void> update(Map<String, dynamic> updatedItem) async {
    int i = _items.indexWhere((item) => item['id'] == updatedItem['id']);
    _items[i] = updatedItem;
  }

  Future<List<Map<String, dynamic>>> list() async {
    await Future.delayed(Duration(milliseconds: 800));
    return _items;
  }

  Future<Map<String, dynamic>?> findOne(int id) async {
    return _items.firstWhere((item) => item['id'] == id);
  }
}

```

上面的 singleton 类实现了几个函数来添加、编辑、删除和检索基于键值的数据记录。您可能已经注意到，我们让所有操作都是异步的，以模拟真实的数据层连接。此外，列表操作通过`Future.delayed`功能模拟数据读取延迟。

## 创建图书仓库

存储库模式促使我们通过存储库类消费数据层连接——因此，我们需要为`Book`业务实体创建一个存储库类。

但是，首先，我们需要创建基本存储库接口作为一个良好的实践。然后，我们可以通过实现相同的接口来编写多个具体的存储库。例如，您可以为各种数据源创建图书仓库实现——基于 SQLite 的图书仓库、完整的图书仓库等。—使用此基本接口。

将以下代码添加到`lib/repositories/book_interface.dart`:

```
import 'package:repo_bookstore/models/book.dart';

abstract class IBookRepository {
  Future<List<Book>> getAll();
  Future<Book?> getOne(int id);
  Future<void> insert(Book book);
  Future<void> update(Book book);
  Future<void> delete(int id);
}

```

现在，让我们使用上面的接口定义为图书仓库创建一个具体的实现。将以下代码添加到`lib/repositories/book.dart`:

```
import 'package:repo_bookstore/db/virtual_db.dart';
import 'package:repo_bookstore/repositories/book_interface.dart';
import 'package:repo_bookstore/models/book.dart';

class BookRepository implements IBookRepository {

  final VirtualDB _db;

  BookRepository(this._db);

  @override
  Future<List<Book>> getAll() async {
    var items = await _db.list();
    return items.map((item) => Book.fromMap(item)).toList();
  }

  @override
  Future<Book?> getOne(int id) async {
    var item = await _db.findOne(id);
    return item != null ? Book.fromMap(item) : null;
  }

  @override
  Future<void> insert(Book book) async {
    await _db.insert(book.toMap());
  }

  @override
  Future<void> update(Book book) async {
    await _db.update(book.toMap());
  }

  @override
  Future<void> delete(int id) async {
    await _db.remove(id);
  }
}

```

上面的代码片段通过覆盖所有抽象方法，经由先前的图书储存库接口定义来实现图书储存库。

注意，在这个层次上，我们只使用业务实体来公开功能。例如，我们可以通过发送一个`Book`实例将一本新书插入我们的虚拟数据库。在这一级，我们在数据访问层之上添加了一个抽象层，因此在接下来的步骤中，我们不需要考虑虚拟数据库层。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

此外，这个存储库层是我们直接访问数据库层的最高层。

## 使用 Flutter 应用程序前端的存储库

我们的图书仓库现在可以使用了。让我们继续为书店应用程序创建一个前端。

我们通常不在视图中添加数据处理逻辑(Flutter 小部件),以保持 UI 逻辑与主要业务逻辑的分离。因此，我们可以创建一个控制器/服务来从小部件级别操作数据。

让我们假设我们需要实现一个添加、显示和删除书籍的前端。然后，我们需要通过控制器公开所需的功能。

将以下代码添加到`lib/controllers/home.dart`中，为主页创建一个控制器。

```
import 'package:repo_bookstore/db/virtual_db.dart';
import 'package:repo_bookstore/repositories/book.dart';
import 'package:repo_bookstore/models/book.dart';

class HomeController {
  BookRepository _bookRepo = BookRepository(VirtualDB());

  Future<List<Book>> getAllBooks() {
    return _bookRepo.getAll();
  }

  Future<void> addBook(Book book) {
    return _bookRepo.insert(book);
  }

  Future<void> removeBook(int id) {
    return _bookRepo.delete(id);
  }
}

```

我们的控制器类已经准备好了；它通过包装图书仓库 API 来公开列出、添加和删除图书的功能。

现在，我们可以为书店应用程序构建前端。用下面的 Dart 代码替换现有的`lib/main.dart`文件的代码。

```
import 'package:flutter/material.dart';
import 'package:flutter/services.dart';
import 'package:repo_bookstore/controllers/home.dart';
import 'package:repo_bookstore/models/book.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
          appBar: AppBar(
            title: const Text('Repo Book Store'),
          ),
          body: HomePage()),
    );
  }
}

class HomePage extends StatefulWidget {
  final HomeController _homeController = HomeController();

  @override
  _HomePageState createState() => _HomePageState();
}

class _HomePageState extends State<HomePage> {
  void _refreshList() {
    setState(() {});
  }

  @override
  Widget build(BuildContext context) {
    return ListView(children: [
      _Form(widget._homeController, _refreshList),
      _BookTable(widget._homeController, _refreshList)
    ]);
  }
}

class _Form extends StatefulWidget {
  final HomeController _homeController;
  final VoidCallback _refreshList;

  _Form(this._homeController, this._refreshList);

  @override
  _FormState createState() => _FormState();
}

class _FormState extends State<_Form> {
  final GlobalKey<FormState> _formKey = GlobalKey<FormState>();
  final TextEditingController _titleFieldController = TextEditingController();
  final TextEditingController _yearFieldController = TextEditingController();

  @override
  void dispose() {
    _titleFieldController.dispose();
    _yearFieldController.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Form(
      key: _formKey,
      child: Container(
        padding: const EdgeInsets.all(8.0),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: <Widget>[
            TextFormField(
              controller: _titleFieldController,
              decoration: const InputDecoration(
                labelText: 'Title',
              ),
              validator: (String? value) {
                if (value == null || value.isEmpty) {
                  return 'Please enter book title';
                }
                return null;
              },
            ),
            TextFormField(
              controller: _yearFieldController,
              decoration: const InputDecoration(
                labelText: 'Year',
              ),
              keyboardType: TextInputType.number,
              inputFormatters: [
                FilteringTextInputFormatter.allow(RegExp(r'[\d]')),
              ],
              validator: (String? value) {
                if (value == null || value.isEmpty) {
                  return 'Please enter released year';
                }
                return null;
              },
            ),
            Container(
                margin: const EdgeInsets.only(top: 10.0),
                child: ElevatedButton(
                  onPressed: () async {
                    if (_formKey.currentState!.validate()) {
                      await widget._homeController.addBook(Book(
                          0,
                          _titleFieldController.text,
                          int.parse(_yearFieldController.text)));
                      _titleFieldController.clear();
                      _yearFieldController.clear();
                      widget._refreshList();
                    }
                  },
                  child: Text('Add book'),
                )),
          ],
        ),
      ),
    );
  }
}

class _BookTable extends StatelessWidget {
  final HomeController _homeController;
  final VoidCallback _refreshList;

  _BookTable(this._homeController, this._refreshList);

  @override
  Widget build(BuildContext context) {
    return FutureBuilder<List<Book>>(
        future: _homeController.getAllBooks(),
        builder: (context, snapshot) {
          if (!snapshot.hasData) {
            return const Center(child: Text('Loading..'));
          } else {
            return DataTable(
                columns: _createBookTableColumns(),
                rows: _createBookTableRows(snapshot.data ?? []));
          }
        });
  }

  List<DataColumn> _createBookTableColumns() {
    return [
      const DataColumn(label: Text('ID')),
      const DataColumn(label: Text('Book')),
      const DataColumn(label: Text('Action')),
    ];
  }

  List<DataRow> _createBookTableRows(List<Book> books) {
    return books
        .map((book) => DataRow(cells: [
              DataCell(Text('#' + book.id.toString())),
              DataCell(Text('${book.title} (${book.year.toString()})')),
              DataCell(IconButton(
                icon: const Icon(Icons.delete),
                onPressed: () async {
                  await _homeController.removeBook(book.id);
                  _refreshList();
                },
              )),
            ]))
        .toList();
  }
}

```

上面的 UI 代码实现了一个带有表单和数据表的多组件单页应用程序。表单组件允许用户添加新书，而[数据表组件](https://blog.logrocket.com/flutter-datatable-widget-guide-displaying-data/)显示所有书籍信息。此外，用户可以通过点击垃圾桶图标从商店中删除一本书。

这里，我们通过使用基于回调的状态管理方法将整个 UI 添加到一个 Dart 文件中。这主要是因为我们只有一个应用程序屏幕。然而，当你开发具有多个屏幕的大规模 Flutter 应用时，使用多个 Dart 文件并选择灵活的状态管理方法总是好的(Flutter 团队推荐 [Provider](https://docs.flutter.dev/development/data-and-backend/state-mgmt/options#provider) )。

请注意关于上述 UI 源代码的以下要点:

*   它不直接访问图书存储库或数据库层，而是通过`HomeController`实例访问存储的图书
*   我们使用`FutureBuilder`类通过异步数据检索有条件地呈现 UI。`FutureBuilder`类提供了一种高效的方法来显示另一个小部件，直到异步操作完成；在我们的例子中，我们显示一个文本标签，直到我们从存储库中收到所有的书
*   **Add book** 按钮通过用从文本编辑控制器获得的数据创建图书 DTO 的新实例，将一本新书插入虚拟数据库

检查您的应用程序，添加新书，并尝试删除现有书籍，如下所示。

![Adding And Removing Books Repo Book Store](img/41870ea75fc337582b0842519ccc73eb.png)

The bookstore app supports adding, listing, and removing book entities via the book repository instance. Similarly, you can add another screen to update a selected book by using the `getOne` and `update` repository functions. The pattern is the same  —  you need to expose necessary repository functions via the controller to the stateful widget.

完整的源代码可以在 [my GitHub repository](https://github.com/codezri/flutter-repo-bookstore) 获得。

## 如何为存储库模式编写单元测试

您可以为您的 Flutter 应用程序的不同元素实现单元测试，比如小部件、控制器、模型、服务和存储库。可以使用以下策略对基于存储库的颤振代码库进行单元测试:

*   实现一个模拟存储库类，并测试控制器/服务逻辑
*   实现一个模拟数据库类并测试存储库逻辑

您不需要自己实现模拟类——[mock ITO 包](https://pub.dev/packages/mockito)帮助您快速自动地生成它们。如果需要用模拟数据库类测试存储库，可以将模拟数据库实例传递给存储库，如下所示。

```
final mockDB = MockDB();
final bookRepo = BookRepository(mockDB);
// tests ...

```

## 创建多个存储库的指南

我们的书店应用程序只有一个业务对象和一个存储库。但是，正如我们前面提到的，如果您使用大规模的 Flutter 应用程序，您可能必须与许多业务实体合作。我们如何为许多业务对象创建多个存储库？

假设我们还需要在自己做的书店 app 中管理电子书。现在我们有两个业务实体:`Book`和`e``-books`。

我们可以用一些具有 Dart 泛型语法的附加类来组织我们的代码，如下面的步骤所示。

首先，我们需要定义我们的模型:

```
abstract class BaseEntity {
  // common structure
}
class Book extends BaseEntity {
  // book implementation 
}
class EBook extends BaseEntity {
  // e-book implementation
}

```

接下来，为基本存储库定义创建一个通用接口。

```
abstract class IRepository<T> {
  Future<List<T>> getAll();
  Future<void> insert(T item);
  // other methods....
}

```

现在，根据项目的架构需求，您可以编写多个具体的存储库或者一个通用的具体存储库。然而，添加一个通用存储库实现是避免所有场景中代码重复的好方法。看下面这个`EntityRepository`类。

```
class EntityRepository<T extends BaseEntity> implements IRepository<T> {

  @override
  Future<List<BaseEntity>> getAll() async {
    // implementation
  }

  @override
  Future<void> insert(T item) async {
    // implementation
  }
}

```

注意，在这里，我们只接受带有`T extends BaseEntity`定义的泛型的`BaseEntity`类型。最后，您可以将上面的`EntityRepository`与不同的子类一起使用，如下所示。

```
var bookRepo = EntityRepository<Book>();
var eBookRepo = EntityRepository<EBook>();
bookRepo.insert(Book());
eBookRepo.insert(EBook());

```

你可以从 [my DartPad](https://dartpad.dev/?id=162da61e6d92bbe209735e8e8ffb1474) 在线检查并运行这个例子。

## DAO、存储库和服务模式

存储库模式看起来有点类似于数据访问对象(DAO)——不要与分散自治组织(DAO)相混淆——和服务模式，但是有几个明显的区别，如下表所示。

## 结论

我们学习了如何通过一个示例应用程序来实现 Flutter 应用程序的存储库设计模式。Flutter 应用程序开发团队使用各种设计模式来组织代码和管理应用程序状态。例如，他们经常使用各种状态管理包，如 [Redux](https://pub.dev/packages/redux) 、 [Riverpod](https://pub.dev/packages/riverpod) 或 [Provider](https://pub.dev/packages/provider) 。他们也可以用 MVC 模式、可重用组件、继承和可重用通用函数来组织代码库。

存储库概念还提供了一种通过创建另一个抽象层来组织代码的方法。如果您需要处理业务实体，您可以像创建控制器一样，通过包装一个存储库实例(或多个)来创建一个服务类。

我们还可以通过创建带有存储库的 DAO 来隐藏数据持久层的复杂性，但不要过度设计带有许多类的中小型应用程序，因为这些复杂的设计模式主要是为了处理大规模应用程序的复杂性而存在的。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)