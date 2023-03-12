# 在 Flutter 中实现复制到剪贴板

> 原文：<https://blog.logrocket.com/implementing-copy-clipboard-flutter/>

在你最喜欢的媒体平台上滚动，你会看到一条漂亮的推文、帖子或消息。点击一个按钮，内容就被复制了，你进入 WhatsApp，点击粘贴，内容就被复制了。很简单，是吗？

完全正确——通过剪贴板，我们的生活变得如此简单。想象一下，如果你不得不逐字逐句地输入你在网上看到的每一条引用、推文或消息。可怕！现在你看到了剪贴板的重要性和它给我们带来的便利。

在本文中，我们将研究如何在一个示例应用程序中实现复制到剪贴板功能。通过一步一步的演练，我们将研究在有和没有外部依赖的情况下实现这个特性。

## 目录

## 在 Flutter 中探索剪贴板

### 什么是剪贴板？

让我们从什么是剪贴板开始。剪贴板是一个临时保存数据的位置，然后可以从这里将数据粘贴到设备上的新位置。在我们的上下文中，剪贴板保存我们想要复制的文本，直到我们将它粘贴到一个新的位置。考虑到如果没有剪贴板功能，我们将不得不手动复制和粘贴大量的文本，我们可以一致认为剪贴板是我们生活中一个超级重要的方面。

[Flutter](https://flutter.dev/) 框架给了我们访问和控制剪贴板的能力，当我们复制文本时向它写入，当我们粘贴时从它读取。根据您的使用情况，您可以在 Flutter 中实现复制到剪贴板，无论是否依赖。

### 使用依赖项实现复制到剪贴板

除了 Flutter 允许我们访问的实现之外，还有一些可用的包使得实现复制到剪贴板特性更加容易。其中一个包是`clipboard`。有了这个包，我们可以访问一些方法，通过这些方法我们可以操作剪贴板，在我们需要的时候复制/写入剪贴板，并从剪贴板中读取/粘贴到我们选择的新位置。

### 实现无依赖地复制到剪贴板

复制到剪贴板特性可以使用 Flutter 授予访问权限的`[Clipboard](https://api.flutter.dev/flutter/services/Clipboard-class.html/)`类来实现。`Clipboard`类公开了两个方法:`setData`和`getData`。

#### `setData`

`[setData](https://api.flutter.dev/flutter/services/Clipboard/setData.html/)`方法用于向键盘添加/复制数据。你所要做的就是调用`Clipboard`类上的方法，然后使用`ClipboardData`对象传入你的文本。下面是一个如何使用它的例子:

```
Clipboard.setData(ClipboardData(text: "your text"))
```

#### `getData`

`[getData](https://api.flutter.dev/flutter/services/Clipboard/getData.html/)`方法用于从剪贴板中读取/粘贴数据到新的位置。我们必须传入粘贴项的格式。在我们的例子中，项目是文本，所以格式是`Clipboard.kTextPlain`。下面是一个如何使用它的例子:

```
ClipboardData cdata = await Clipboard.getData(Clipboard.kTextPlain);
String copiedtext = cdata.text;
```

通过这两种方法，您可以在 Flutter 应用程序中轻松地使用剪贴板，从而改善整体用户体验。我们将在示例应用程序中对这两种实现进行演练。我们将创建一个示例应用程序，可以在其中输入文本、复制文本，然后粘贴到新位置。

## 如何在 Flutter 中复制到剪贴板

是时候把手弄脏了。在这一节中，我们将在一个示例应用程序中实现复制到剪贴板特性。我们将能够[输入一个自定义文本](https://blog.logrocket.com/the-ultimate-guide-to-text-fields-in-flutter/)，然后点击一个按钮，它就会被写入剪贴板。当我们单击另一个按钮时，我们可以粘贴到另一个位置。我们将在有依赖和没有依赖的情况下实现该特性。让我们开始吧！首先，使用下面的命令创建一个新项目:

```
flutter create copy_to_clipboard

```

这将生成应用程序运行所需的所有基本文件。在应用程序中，我们有两个页面:一个显示没有依赖关系的实现，另一个显示有依赖关系的实现。在你的`lib`文件夹中创建两个新文件，`home_view.dart`和`second_view.dart`。

在`home_view.dart`中，创建一个名为`HomeView`的无状态小部件。它将包含具有外部依赖性的实现。

```
class HomeView extends StatelessWidget {
  const HomeView({Key? key}) : super(key: key);
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Diary App'),
      ),
      body: Column(
        children: [],
      ),
    );
  }
}

```

在第二个文件`second_view.dart`中，创建一个无状态小部件，并将其命名为`SecondView`(因为目前没有更好的名称)。这将保持实现没有依赖性。

```
class SecondView extends StatelessWidget {
  const SecondView({Key? key}) : super(key: key);
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Second View'),
      ),
      body: Column(
        children: [],
      ),
    );
  }
}

```

### 依赖关系的实现

我们将在这个实现中使用`[Clipboard](https://pub.dev/packages/clipboard)`包。转到 pub.dev，将这个包作为一个依赖项添加到你的应用程序中。为此，转到您的`pubspec.yaml`文件，并将`clipboard`包作为依赖项添加到`dependencies`部分。

```
name: copy_to_clipboard
description: A new Flutter project.
publish_to: 'none' 
version: 1.0.0+1
environment:
  sdk: ">=2.15.1 <3.0.0"
dependencies:
  flutter:
    sdk: flutter
  cupertino_icons: ^1.0.2
  clipboard: ^0.1.3  
dev_dependencies:
  flutter_test:
    sdk: flutter
  flutter_lints: ^1.0.0

flutter:
  uses-material-design: true
```

之后，运行下面的命令来获取要在本地使用的包。

```
flutter pub get
```

接下来，转到我们之前创建的新文件`home_view.dart`。这是我们实现`copy`和`paste`功能的地方。

首先，添加两个`TextEditingControllers`，它们控制我们将使用的两个`TextFields`。

让我们以聊天消息的行为为例。您可以复制消息，移动到另一个`TextField`框，然后粘贴。第一个控制器获取文本，当您粘贴时，第一个控制器更新第二个控制器或您希望它更新的屏幕上的任何其他文本。

```
TextEditingController diaryControllerWithPackageOne =
                                TextEditingController();
TextEditingController diaryControllerWithPackageTwo =
                                TextEditingController();

```

转到有趣的部分，让我们实现`Copy`功能。增加一个`TextField`和`TextButton`，并将`diaryControllerWithPackageOne`控制器分配给`TextField`。

在`TextButton`的`onPressed`参数中，从包提供的`FlutterClipboard`类中调用`copy`方法。我们还添加了一个`SnackBar`来通知用户文本已经被复制。

之后，将来自`diaryControllerWithPackageOne.text`的文本传递给它。

```
TextField(
      controller: diaryControllerWithPackageOne,
      decoration: const InputDecoration(
        labelText: 'Enter diary input for today',
        border: OutlineInputBorder(),
      ),
    ),
    TextButton(
      onPressed: () async {
         FlutterClipboard.copy(diaryControllerWithPackageOne.text)
      .then(
    (value) {
      return ScaffoldMessenger.of(context).showSnackBar(
        const SnackBar(
          content: Text('Text Copied'),
        ),
      );
    },
  );
      },
      style: ButtonStyle(
        backgroundColor: MaterialStateProperty.all<Color>(Colors.blue),
      ),
      child: const Text(
        'Copy',
        style: TextStyle(color: Colors.white),
      ),
    ),

```

至此，`copy`功能已经成功实现！让我们实现`paste`功能。增加一个`TextField`和`TextButton`。将`diaryControllerWithPackageTwo`分配给`TextField`。

在`TextButton`的`onPressed`参数中，调用`clipboard`包中`FlutterClipboard`类的`paste`方法。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

将结果分配给`diaryControllerWithPackageTwo.text`，从而在`TextField`中显示复制的文本。

```
  TextField(
    controller: diaryControllerWithPackageTwo,
    decoration: const InputDecoration(
      labelText: 'Pasted text will appear here',
      border: OutlineInputBorder(),
    ),
  ),
  TextButton(
    onPressed: () async {
      FlutterClipboard.paste().then(
        (value) => diaryControllerWithPackageTwo.text = value,
      );
    },
    style: ButtonStyle(
      backgroundColor: MaterialStateProperty.all<Color>(Colors.blue),
    ),
    child: const Text(
      'Paste',
      style: TextStyle(color: Colors.white),
    ),
  ),

```

最后，让我们添加一个按钮来导航到下一页，这将包含没有依赖关系的实现。

```
ElevatedButton(
    onPressed: () {
      Navigator.push(
        context,
        MaterialPageRoute(
          builder: (context) => const HomeView(),
        ),
      );
    },
    child: const Text('Go To Second View'),
  ),

```

这样，我们已经使用外部依赖/包附带的`FlutterClipboard`类成功地实现了`copy`和`paste`。让我们继续实现无依赖性。

### 无依赖的实现

在`second_view.dart`的`SecondView`小部件中，添加两个`TextEditingControllers`:一个控制`TextField`，另一个接收我们要粘贴的文本。

```
TextEditingController diaryTextEditingController = 
                                TextEditingController();
TextEditingController diaryTwoTextEditingController =
                                TextEditingController();

```

接下来，添加一个`TextField`，并将第一个`TextEditingController`、`diaryTextEditingController`分配给它。添加一个`TextButton`，我们将使用它来触发`Copy`功能。

我们将使用 Flutter 的`Clipboard`类中的`setData`方法将我们想要的文本复制/写入剪贴板，准备粘贴到新的位置。另外，我们将添加一个`SnackBar`来通知用户文本已经被复制。

由于文本来自传递给第一个`TextField`的内容，我们将`diaryTextEditingController.text`值传递给`setData`方法的文本参数。

```
TextField(
      controller: diaryTextEditingController,
      decoration: const InputDecoration(
        labelText: 'Enter diary input for today',
        border: OutlineInputBorder(),
      ),
    ),

    TextButton(
      onPressed: () async {
      Clipboard.setData(
              ClipboardData(text: diaryTextEditingController.text))
          .then(
        (value) {
          return ScaffoldMessenger.of(context).showSnackBar(
            const SnackBar(
              content: Text('Text Copied'),
            ),
          );
  },
);
      },
      style: ButtonStyle(
        backgroundColor: MaterialStateProperty.all<Color>(Colors.blue),
      ),
      child: const Text(
        'Copy',
        style: TextStyle(color: Colors.white),
      ),
    ),

```

完成这些后，我们可以继续进行`Paste`功能。类似于`Copy`功能，增加一个`TextField`和一个`TextButton`。将第二个控制器`diaryTwoTextEditingController`分配给`TextField`。

在`TextButton`的`onPressed`参数中，调用`Clipboard`类中的`getData`方法。我们需要传递格式，所以在我们的例子中，使用`text/plain`——从剪贴板中检索文本时的格式。

```
TextField(
    controller: diaryTwoTextEditingController,
    decoration: const InputDecoration(
      labelText: 'Pasted text will appear here',
      border: OutlineInputBorder(),
    ),
  ),
  TextButton(
    onPressed: () async {
      Clipboard.getData('text/plain').then((value) {
        diaryTwoTextEditingController.text = value!.text!;
      });
    },
    style: ButtonStyle(
      backgroundColor: MaterialStateProperty.all<Color>(Colors.blue),
    ),
    child: const Text(
      'Paste',
      style: TextStyle(color: Colors.white),
    ),
  ),

```

最后，让我们添加一个按钮来导航回上一页:

```
ElevatedButton(
      onPressed: () {
        Navigator.pop(context);
      },
      child: const Text('Back'),
    ),

```

完成这些后，我们就可以开始在我们的 Flutter 应用程序中使用`Clipboard`类从/向我们的剪贴板复制和粘贴了。

我们现在可以继续运行我们的应用程序了。运行以下命令来运行示例应用程序:

```
flutter pub run

```

 [https://www.youtube.com/embed/uoBzo8SQwlc?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent](https://www.youtube.com/embed/uoBzo8SQwlc?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent)

视频

## 结论

万岁！我们已经走了很长一段路，您已经看到了在 Flutter 应用程序中实现复制到剪贴板特性的各种方法。至此，您已经成功地学会了一种在应用程序中简化用户体验的方法(您可能会同意，这种方法为您的用户减轻了很多压力)。

查看示例应用程序的[完整源代码](https://github.com/Blazebrain/copy_to_clipboard)。如果您有任何问题或咨询，请随时通过 [Twitter](https://twitter.com/blazebrain01) 或 [LinkedIn](https://www.linkedin.com/in/david-adegoke) 联系我。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)