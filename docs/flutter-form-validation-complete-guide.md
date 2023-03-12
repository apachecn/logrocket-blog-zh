# 颤振形式验证:完整指南

> 原文：<https://blog.logrocket.com/flutter-form-validation-complete-guide/>

***编者按:*** *这篇文章最后一次更新是在 2022 年 2 月 25 日，它包含了关于在 Flutter 中进行表单验证的更全面指南的信息，包括使用提供者包的表单验证、定制表单字段以及 Regex 和 Dart 扩展方法。*

表单验证是大多数应用程序不可或缺的一部分，也是任何移动应用程序开发人员的必备工具。随着 [Flutter](https://blog.logrocket.com/tag/flutter/) 越来越受欢迎，我们将探索表单验证是如何工作的，以及使它更有效工作的替代方法。

本文的目的是让您了解如何在 Flutter 中实现简洁且可伸缩的表单验证。在这篇博文的结尾，你将能够将这些概念应用到你未来所有的应用程序开发项目中。

## 内容

## Flutter 中的表单验证入门

Flutter SDK 为我们提供了一个开箱即用的小部件和功能，让我们在使用表单验证时更加轻松。在本文中，我们将介绍两种表单验证方法:表单小部件和提供者包。你可以在官方的 Flutter 文档中找到更多关于这两种方法的信息。

## 在颤动中创造一种形式

首先，我们将创建一个简单的登录页面，该页面包含以下字段:

*   电子邮件
*   名字
*   电话号码
*   密码

![Basic Flutter form](img/108e21e509983be7526a6bd717aa6fb0.png)

对于验证，我们希望我们的应用程序的用户在这些字段中填写正确的细节。逻辑将被定义如下:

首先，对于 name 字段，我们希望用户输入一个有效的名和姓，可以带有缩写。

对于电子邮件字段，我们希望在“@”符号之前包含一些字符的有效电子邮件，以及在电子邮件结尾的电子邮件域。

对于电话号码验证，用户需要输入从零开始的 11 位数字。

最后，对于我们的密码验证，我们希望用户使用大写字母、小写字母、数字和特殊字符的组合。

只有当用户的输入与上面提到的相匹配时，我们才希望在发出任何请求之前接受他们的输入，比如发送到服务器或保存在数据库中。

## 设置要验证的表单

首先在 VS Code 或 Android Studio 中创建一个新的 Flutter 项目。用您自己的有状态小部件替换`main.dart`中的 Flutter 默认计数器应用程序。

你应该有这样的东西:

```
import 'package:flutter/material.dart';

import 'form/form_page.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Form Validation Demo',
      theme: ThemeData(
        primarySwatch: Colors.blue,
        visualDensity: VisualDensity.adaptivePlatformDensity,
      ),
      home: FormPage(),
    );
  }
}

```

这是我们的`main.dart`文件目前的样子。现在，创建一个新的 dart 文件并将其命名为`form_page.dart`，然后用下面的代码在其中创建一个`FormPage`有状态小部件:

```
import 'package:flutter/material.dart';

class FormPage extends StatefulWidget {
  @override
  _FormPageState createState() => _FormPageState();
}

class _FormPageState extends State {

//This key will be used to identify the state of the form.
  final _formKey = GlobalKey<FormState>();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: SafeArea(
        child: Form(
          key: _formKey,
          child: Column(
            children: [
              TextFormField(),
              ElevatedButton(
                onPressed: () {},
                child: const Text('Submit'),
              )
            ],
          ),
        ),
      ),
    );
  }
}

```

`formKey`处理表单的状态、验证和保存。在这个列中，我们看到一个简单的`TextFormField`和一个`ElevatedButton`。该表单将包含多个这样的文本表单字段，并做了一些修改。这个`TextFormField`小部件将被提取到一个单独的无状态小部件中，并被一般化以获得更好的可重用性。

现在，让我们创建一个名为`custom_form_field.dart`的文件，并添加以下启动代码:

```
import 'package:flutter/material.dart';
class CustomFormField extends StatelessWidget {
  CustomFormField({Key? key,}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return TextFormField();
  }
}

```

该自定义文本字段将具有以下属性:

*   `hintText`，提供任何输入前显示的提示文本
*   `validator`，该功能在收到指示时验证我们的输入
*   `inputFormatter`，防止文本字段中出现不需要的输入类型

接下来，将以下代码添加到`custom_form_field.dart`:

```
import 'package:flutter/material.dart';
import 'package:flutter/services.dart';
class CustomFormField extends StatelessWidget {
  CustomFormField({
    Key? key,
    required this.hintText,
    this.inputFormatters,
    this.validator,
  }) : super(key: key);
  final String hintText;
  final List<TextInputFormatter>? inputFormatters;
  final String? Function(String?)? validator;
  @override
  Widget build(BuildContext context) {
    return Padding(
      padding: const EdgeInsets.all(8.0),
      child: TextFormField(
        inputFormatters: inputFormatters,
        validator: validator,
        decoration: InputDecoration(hintText: hintText),
      ),
    );
  }
}

```

## 输入验证和输入格式化程序

表单在进一步处理输入之前使用验证作为数据完整性检查。确保用户不会输入错误数据的另一种方法是不允许在文本字段中输入外来字符。这是使用输入格式化程序实现的。`inputFormatters`在 Flutter 中，取一个`TextInputFormatter`，它有一个关联的 RegExp，并决定在用户输入期间是允许还是忽略该 RegExp。

## 使用正则表达式方法和 Dart 扩展方法

为了使我们的生活更容易，避免编写多个 if-else 语句，我们将在应用程序中使用 [Regex](https://www.rexegg.com/regex-quickstart.html) 和 [Dart 的扩展方法](https://dart.dev/guides/language/extension-methods)。

让我们创建一个扩展类，它将包含我们将在本教程中使用的所有扩展方法:

```
extension extString on String {
  bool get isValidEmail {
    final emailRegExp = RegExp(r"^[a-zA-Z0-9.][email protected][a-zA-Z0-9]+\.[a-zA-Z]+");
    return emailRegExp.hasMatch(this);
  }

  bool get isValidName{
    final nameRegExp = new RegExp(r"^\s*([A-Za-z]{1,}([\.,] |[-']| ))+[A-Za-z]+\.?\s*$");
    return nameRegExp.hasMatch(this);
  }

  bool get isValidPassword{
final passwordRegExp = 
    RegExp(r'^(?=.*?[A-Z])(?=.*?[a-z])(?=.*?[0-9])(?=.*?[[email protected]#\><*~]).{8,}/pre>');
    return passwordRegExp.hasMatch(this);
  }

  bool get isNotNull{
    return this!=null;
}

  bool get isValidPhone{
    final phoneRegExp = RegExp(r"^\+?0[0-9]{10}$");
    return phoneRegExp.hasMatch(this);
  }

}

```

鉴于本文的范围，我们不会花太多时间来阐述扩展方法以及如何构造 Regex。如果您有兴趣了解更多关于 Dart 中扩展方法的知识，请查看 Dart 文档[这里](https://dart.dev/guides/language/extension-methods)。你也可以在这里了解[如何构建你自己的正则表达式。](https://www.rexegg.com/regex-quickstart.html)

您会注意到我们的字符串扩展包含五个方法:

*   `isValidEmail`
*   `isValidName`
*   `isValidPassword`
*   `isNotNull`
*   `isValidPhone`

上面所有的 Regex 方法都获取字符串并检查它是否匹配 Regex 模式，如果不匹配，则返回`true`或`false`。现在我们需要做的就是将这个文件导入到我们需要使用扩展方法的任何文件中。

## 创建输入字段

回到我们的`FormPage()`小部件，我们的小部件树由以下部分组成:脚手架- >安全区域- >容器- >表单- >列。

我们已经创建了一个`formKey`，它将被添加到我们的表单小部件中来标识表单的状态，这是在 Flutter 中默认创建的。

现在让我们为电子邮件创建一个文本字段:

```
CustomFormField(
                hintText: 'Email',
                validator: (val) {
                  if (!val.isValidName) 
                    return 'Enter valid email';
                },
              ),

```

`validator`字段接受用户输入，并检查它是否满足我们的正则表达式条件。如果是，该字段返回`null`。如果没有，它将返回一个字符串，这将是我们的文本字段中显示的错误消息。

我们简单地对其他输入字段重复这一过程，并使用扩展类中匹配的扩展方法。完成所有字段后，`form_field.dart`将如下所示:

```
class FormPage extends StatefulWidget {
  const FormPage({Key? key}) : super(key: key);
  @override
  _FormPageState createState() => _FormPageState();
}
class _FormPageState extends State<FormPage> {
  final _formKey = GlobalKey<FormState>();
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: SafeArea(
        child: Form(
          key: _formKey,
          child: Column(
            children: [
              CustomFormField(
                hintText: 'Name',
                inputFormatters: [
                  FilteringTextInputFormatter.allow(
                    RegExp(r"[a-zA-Z]+|\s"),
                  )
                ],
                validator: (val) {
                  if (!val.isValidName) return 'Enter valid name';
                },
              ),
              CustomFormField(
                hintText: 'Email',
                validator: (val) {
                  if (!val.isValidEmail) return 'Enter valid email';
                },
              ),
              CustomFormField(
                hintText: 'Phone',
                inputFormatters: [
                  FilteringTextInputFormatter.allow(
                    RegExp(r"[0-9]"),
                  )
                ],
                validator: (val) {
                  if (!val.isvalidPhone) return 'Enter valid phone';
                },
              ),
              CustomFormField(
                hintText: 'Password',
                validator: (val) {
                  if (!val.isValidPassword) return 'Enter valid password';
                },
              ),
              ElevatedButton(
                onPressed: () {},
                child: const Text('Submit'),
              )
            ],
          ),
        ),
      ),
    );
  }
}

```

对于电话号码的文本字段，使用输入格式化程序。该输入格式化程序只允许输入数字，不允许输入其他任何东西。

我们在此页面上触发验证的方式是使用我们创建的表单键变量，以便访问表单的状态:

```
ElevatedButton(
                onPressed: () {
                  if (_formKey.currentState!.validate()) {
                    Navigator.of(context).push(
                      MaterialPageRoute(
                        builder: (_) => SuccessPage(),
                      ),
                    );
                  }
                },
                child: const Text('Submit'),
              )

```

因此，每当用户点击按钮时，我们检查`_formKey.currentState!.validate()`，然后我们执行一个动作，在我们的例子中，这只是导航到一个新的屏幕。

您的成功页面可以是您希望在完成字段验证并使用用户输入的数据后将用户带到的任何内容或任何屏幕。

## 制作自定义表单域

有时需要不同的部件，这取决于你的应用程序的设计。这些小部件在 Flutter 中可能无法作为表单小部件使用。对于所有这样的实例，都有一个`FormField`小部件，它可以帮助我们构建定制的表单字段，添加像验证这样的特性。在这种情况下，我们将建立一个图像选择器表单域。

先把`file_picker`加到`pubspec.yaml`上。然后创建一个名为`custom_image_form_field.dart`的文件。在这里，我们添加以下代码:

```
import 'dart:io';
import 'package:file_picker/file_picker.dart';
import 'package:flutter/material.dart';
class CustomImageFormField extends StatelessWidget {
  CustomImageFormField({
    Key? key,
    required this.validator,
    required this.onChanged,
  }) : super(key: key);
  final String? Function(File?) validator;
  final Function(File) onChanged;
  File? _pickedFile;
  @override
  Widget build(BuildContext context) {
    return FormField<File>(
        validator: validator,
        builder: (formFieldState) {
          return Column(
            children: [
              GestureDetector(
                onTap: () async {
                  FilePickerResult? file = await FilePicker.platform
                      .pickFiles(type: FileType.image, allowMultiple: false);
                  if (file != null) {
                    _pickedFile = File(file.files.first.path!);
                    onChanged.call(_pickedFile!);
                  }
                },
                child: Container(
                  margin: const EdgeInsets.all(8),
                  padding:
                      const EdgeInsets.symmetric(horizontal: 32, vertical: 8),
                  decoration: BoxDecoration(
                    borderRadius: BorderRadius.circular(8),
                    color: const Color(0xff707070).withOpacity(0.1),
                  ),
                  child: Column(
                    children: const [
                      Icon(Icons.upload_file),
                      Text('Upload Image')
                    ],
                  ),
                ),
              ),
              if (formFieldState.hasError)
                Padding(
                  padding: const EdgeInsets.only(left: 8, top: 10),
                  child: Text(
                    formFieldState.errorText!,
                    style: TextStyle(
                        fontStyle: FontStyle.normal,
                        fontSize: 13,
                        color: Colors.red[700],
                        height: 0.5),
                  ),
                )
            ],
          );
        });
  }
}

```

表单小部件与其他小部件的区别在于`formState`。这包含了表单运行所必需的重要信息，我们在这里利用了其中的一些信息。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

如果用户选择了某个文件，这段代码中的`validator`回调将返回一个`File`对象。我们使用这个验证器，就像我们之前使用`CustomTextField`一样。接下来，`FilePicker`用于选择文件，根据用户是否选择了一个文件，调用`onChanged`回调函数，该函数也可以像`CustomTextField`一样使用。

错误文本必须在单独的小部件中手动显示。每当一个`errorText`存在时，它将被存储在`formFieldState`中，我们可以从那里获取它。

现在，我们可以像这样使用这个小部件:

```
class FormPage extends StatefulWidget {
  const FormPage({Key? key}) : super(key: key);
  @override
  _FormPageState createState() => _FormPageState();
}
class _FormPageState extends State<FormPage> {
  final _formKey = GlobalKey<FormState>();
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: SafeArea(
        child: Form(
          key: _formKey,
          child: Column(
            children: [
              CustomFormField(
                hintText: 'Name',
                inputFormatters: [
                  FilteringTextInputFormatter.allow(
                    RegExp(r"[a-zA-Z]+|\s"),
                  )
                ],
                validator: (val) {
                  if (!val.isValidName) return 'Enter valid name';
                },
              ),
              CustomFormField(
                hintText: 'Email',
                validator: (val) {
                  if (!val.isValidEmail) return 'Enter valid email';
                },
              ),
              CustomFormField(
                hintText: 'Phone',
                inputFormatters: [
                  FilteringTextInputFormatter.allow(
                    RegExp(r"[0-9]"),
                  )
                ],
                validator: (val) {
                  if (!val.isValidPhone) return 'Enter valid phone';
                },
              ),
              CustomFormField(
                hintText: 'Password',
                validator: (val) {
                  if (!val.isValidPassword) return 'Enter valid password';
                },
              ),
              CustomImageFormField(
                validator: (val) {
                  if (val == null) return 'Pick a picture';
                },
                onChanged: (_file) {},
              ),
              ElevatedButton(
                onPressed: () {
                  _formKey.currentState!.validate();
                },
                child: const Text('Submit'),
              )
            ],
          ),
        ),
      ),
    );
  }
}

```

当按下提交按钮时，我们会看到以下关于图像上传的错误消息。

![error message in a form field](img/2d3af3a1767e46c390bfa364cf52e954.png)

这种方法可以扩展到任何类型的小部件，如日期选择器或下拉菜单，以生成自定义的文本字段。

## 使用提供程序进行表单验证

使用 [Provider](https://blog.logrocket.com/quick-guide-provider-flutter-state-management/) 是在 Flutter 中验证字段的另一种方式。当我们需要在用户输入上执行一些任务，而又不需要用代码搞乱 UI 类时，通常会用到这种技术。

这就是为什么我们将逻辑移到我们的`Provider`类中。我们将使用`Provider`包并将其添加到我们的`pubspec.yaml`文件中:

```
# The following adds the Cupertino Icons font to your application.
# Use with the CupertinoIcons class for iOS style icons.
cupertino_icons: ^1.0.0
provider: ^6.0.2

```

我们的`pubspec.yaml`文件现在应该看起来如上，我们可以继续运行`flutter pub get`来下载所需的依赖项。

然后，我们可以创建一个名为`form_provider.dart`的新文件，并在其中创建一个扩展`ChangeNotifier`的类。`ChangeNotifier`类提供了一些方法，使我们能够使用`ChangeNotifier`来监听我们订阅的对象的变化。

这就是为什么这个类提供的最重要的方法之一是`notifylisteners()</code`。这个方法告诉我们的侦听器从它们订阅的对象或变量中获取最新的值。

在我们开始创建我们的`Provider`类之前，我们将创建一个包含两个变量的模型:一个`error`字符串，另一个我们暂时称之为`value`的字符串:

```
class ValidationModel {
  String? value;
  String? error;
  ValidationModel(this.value, this.error);
}

```

在我们的`Provider`类中，我们将在我们的`ValidationModel`中为我们从用户接收的输入创建四个字段:姓名、电子邮件、密码和电话号码。这些字段是私有的，所以我们将使用 getters 来公开它们:

```
class FormProvider extends ChangeNotifier {
  ValidationModel _email = ValidationModel(null, null);
  ValidationModel _password = ValidationModel(null, null);
  ValidationModel _phone = ValidationModel(null, null);
  ValidationModel _name = ValidationModel(null, null);
  ValidationModel get email => _email;
  ValidationModel get password => _password;
  ValidationModel get phone => _phone;
  ValidationModel get name => _name;
}

```

此外，我们创建从文本字段获取输入的方法，并根据我们的条件验证它们。

如果它们满足我们的要求，我们返回`ValidationModel`错误消息的`null`，如果用户输入不满足我们的标准，我们返回错误消息。

最后，我们将调用`notifylisteners`并将 getter 传递给每个文本字段中的错误消息字段。

这些方法看起来会是这样的:

```
  void validateEmail(String? val) {
    if (val != null && val.isValidEmail) {
      _email = ValidationModel(val, null);
    } else {
      _email = ValidationModel(null, 'Please Enter a Valid Email');
    }
    notifyListeners();
  }
  void validatePassword(String? val) {
    if (val != null && val.isValidPassword) {
      _password = ValidationModel(val, null);
    } else {
      _password = ValidationModel(null,
          'Password must contain an uppercase, lowercase, numeric digit and special character');
    }
    notifyListeners();
  }
  void validateName(String? val) {
    if (val != null && val.isValidName) {
      _name = ValidationModel(val, null);
    } else {
      _name = ValidationModel(null, 'Please enter a valid name');
    }
    notifyListeners();
  }
  void validatePhone(String? val) {
    if (val != null && val.isValidPhone) {
      _phone = ValidationModel(val, null);
    } else {
      _phone = ValidationModel(null, 'Phone Number must be up to 11 digits');
    }
    notifyListeners();
  }
  bool get validate {
    return _email.value != null &&
        _password.value != null &&
        _phone.value != null &&
        _name.value != null;
  }

```

现在，在我们的`Provider`类中，我们有一个名为`validate`的 getter 函数，如果我们所有的验证条件都满足，它将返回`true`。

两个新属性将被添加到`CustomFormField`中，一个`errorText`和一个`onChanged`回调。小部件看起来应该如下所示:

```
class CustomFormField extends StatelessWidget {
  const CustomFormField({
    Key? key,
    required this.hintText,
    required this.errorText,
    this.onChanged,
    this.validator,
    this.inputFormatters,
  }) : super(key: key);
  final String hintText;
  final List<TextInputFormatter>? inputFormatters;
  final String? errorText;
  final Function(String?)? onChanged;
  final String? Function(String?)? validator;
  @override
  Widget build(BuildContext context) {
    return Padding(
      padding: const EdgeInsets.all(8.0),
      child: TextFormField(
        onChanged: onChanged,
        validator: validator,
        inputFormatters: inputFormatters,
        decoration: InputDecoration(hintText: hintText, errorText: errorText),
      ),
    );
  }
}

```

在我们的 UI 类中，我们将用如下代码替换之前的代码:

```
class ProviderFormPage extends StatefulWidget {
  const ProviderFormPage({Key? key}) : super(key: key);
  @override
  _ProviderFormPageState createState() => _ProviderFormPageState();
}
class _ProviderFormPageState extends State<ProviderFormPage> {
  final _formKey = GlobalKey<FormState>();
  late FormProvider _formProvider;
  @override
  Widget build(BuildContext context) {
     = Provider.of<FormProvider>(context);
    return Scaffold(
      body: SafeArea(
        child: Form(
          key: _formKey,
          child: Column(
            children: [
              CustomFormField(
                hintText: 'Name',
                inputFormatters: [
                  FilteringTextInputFormatter.allow(
                    RegExp(r"[a-zA-Z]+|\s"),
                  )
                ],
                onChanged: _formProvider.validateName,
                errorText: _formProvider.name.error,
              ),
              CustomFormField(
                hintText: 'Email',
                onChanged: _formProvider.validateEmail,
                errorText: _formProvider.email.error,
              ),
              CustomFormField(
                hintText: 'Phone',
                onChanged: _formProvider.validatePhone,
                errorText: _formProvider.phone.error,
                inputFormatters: [
                  FilteringTextInputFormatter.allow(
                    RegExp(r"[0-9]"),
                  )
                ],

              ),
              CustomFormField(
                hintText: 'Password',
                onChanged: _formProvider.validatePassword,
                errorText: _formProvider.password.error,
              ),
              Consumer<FormProvider>(
                builder: (context, model, child) {
                  return ElevatedButton(
                    onPressed: () {
                      if (model.validate) {
                        Navigator.of(context).push(
                          MaterialPageRoute(
                            builder: (_) => SuccessPage(),
                          ),
                        );
                      }
                    },
                    child: const Text('Submit'),
                  );
                }
              )
            ],
          ),
        ),
      ),
    );
  }
}

```

最后，在使用`Provider`之前，我们需要将它注册到我们的小部件树的更高层。
让我们在`main.dart`文件中这样做:

```
void main() {
 runApp(ChangeNotifierProvider(
create: (_) => FormProvider(), child: MyApp()));
}

```

现在，我们可以继续运行我们的应用程序，并看到我们有类似于前一种方法的结果。使用第二种方法的主要原因是，如果您发现自己处于一种希望保持 UI 代码整洁并避免应用程序中的数据操作的情况下，即使这看起来需要更多的代码行。

使用提供者方法的另一个好处是，当用户与文本字段交互时，它会验证用户输入。这意味着用户不必等到点击“提交”按钮才知道他们的输入是否有效。

## 结论

除了这种在 UI 中验证表单的方法(这并不是全新的方法)之外，还有许多其他方法来验证表单。 [bloc 库](https://pub.dev/packages/flutter_bloc)也提供了一个用于验证字段的 Flutter 包——它被称为 [form_bloc](https://pub.dev/packages/form_bloc) 。你可以在这里查看文档。非常感谢你的阅读！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)