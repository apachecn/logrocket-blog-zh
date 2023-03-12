# 使用 Share Plus - LogRocket 博客在 Flutter 应用程序中共享内容

> 原文：<https://blog.logrocket.com/sharing-content-flutter-apps-share-plus/>

如果你是一个狂热的智能手机用户，很可能你在移动应用程序上看到了一些东西，你想与朋友分享，例如，在浏览你最喜欢的社交媒体平台或探索待售商品时。

让您的用户能够将您的应用程序中的内容共享给其他应用程序，可以改善整体用户体验。在本文中，您将学习如何使用 [Share Plus 插件](https://pub.dev/packages/share_plus)在 Flutter 应用程序中实现内容共享。要继续阅读本文，您可以查看示例应用程序的[完整源代码](https://github.com/Blazebrain/flutter_share)。我们开始吧！

### 目录

## 在应用程序之间共享文件、文本和图像

在本教程中，我们将介绍如何将示例应用程序中的文本和图像共享给其他移动应用程序。我们将从 [FishWatch API](https://www.fishwatch.gov/developers) 获取一个鱼类物种列表，将其显示在我们的应用程序中，然后添加共享功能，使我们能够与任何其他应用程序共享每个鱼类物种的图像、名称、栖息地和配额等信息。让我们开始吧！

## 操作中的文件共享:示例应用程序演练

### 项目设置

通过运行以下命令创建新的代码库:

```
flutter create flutter_share

```

上面的命令将创建基本文件，我们将基于这些文件构建我们的示例应用程序。接下来，我们将在`pubspec.yaml`文件中导入以下依赖项:

*   [`http`](https://pub.dev/packages/http) :向 FishWatch API 发出`GET`请求，检索各种鱼类的列表
*   [`stacked`](https://pub.dev/packages/stacked) :一个架构解决方案，它使用[提供者](https://blog.logrocket.com/provider-vs-riverpod-comparing-state-managers-in-flutter)在引擎盖下，让我们能够访问将会增加我们开发过程趣味的类
*   `[build_runner](https://pub.dev/packages/build_runner)`:提供从注释自动生成文件的运行命令
*   [`stacked_generator`](https://pub.dev/packages/stacked_generator) :从堆叠标注生成文件
*   [`logger`](https://pub.dev/packages/logger) :打印重要信息到调试控制台
*   [`share_plus`](https://pub.dev/packages/share_plus) :我们将用来实现共享功能的包
*   [`permission_handler`](https://pub.dev/packages/permission_handler) :允许我们请求使用手机的移动存储空间
*   [`image_downloader`](https://pub.dev/packages/image_downloader) :使我们能够下载我们想要共享的图像文件

```
dependencies:
      cupertinoicons: ^1.0.2
      flutter:
        sdk: flutter
      http: ^0.13.4
      imagedownloader: ^0.31.0
      logger: ^1.1.0
      permissionhandler: ^8.3.0
      shareplus: ^3.0.4
      stacked: ^2.2.8
    devdependencies:
      buildrunner: ^2.1.7
      flutterlints: ^1.0.0
      fluttertest:
        sdk: flutter
      stacked_generator: ^0.5.7
```

## 建立模型

接下来，我们将建立模型来转换我们收到的数据。从 FishWatch API 文档中，我们看到该 API 返回名称、描述、图像和许多其他细节。但是，我们只需要名称、图像、蛋白质含量和配额。

在`lib`目录中创建一个名为`models`的文件夹，它将保存我们将使用的所有模型。创建一个名为`fish_response_model.dart`的文件，我们将在其中创建`FishResponseModel`类和`ImageGallery`模型类:

```
class FishResponseModel {
  String? speciesName;
  String? protein;
  ImageGallery? speciesIllustrationPhoto;
  String? quote;
  FishResponseModel({
    required this.protein,
    required this.speciesIllustrationPhoto,
    required this.quote,
  });
  FishResponseModel.fromJson(Map<String, dynamic> json) {
    speciesName = json['Species Name'];
    protein = json['Protein'];
    quote = json['Quote'];
    speciesIllustrationPhoto =
        ImageGallery.fromJson(json['Species Illustration Photo']);
  }
}
class ImageGallery {
  String? src;
  String? alt;
  String? title;
  ImageGallery({this.src, this.alt, this.title});
  ImageGallery.fromJson(Map<String, dynamic> json) {
    src = json['src'];
    alt = json['alt'];
    title = json['title'];
  }
}

```

完成这些后，我们可以继续注册我们将在应用程序中使用的依赖项。

## 注册依赖关系

我们将把在应用程序中执行任务的服务注册为依赖项。我们将在本节中创建每个服务的基本结构，然后在后续的小节中充实它们。

在`lib`目录中创建一个名为`services`的文件夹，它将保存我们将在应用程序中使用的服务。`ApiService`处理来自应用程序的所有出站连接。创建一个名为`api_service.dart`的新文件。现在，我们将只添加基本结构:

```
class ApiService {}

```

`FishSpeciesService`服务类将处理与 FishWatch API 的交互。它将获取鱼类物种的列表，并将其传递到应用程序中。创建一个名为`fish_service.dart`的新文件，并将基本的类结构放在那里:

```
class FishSpecieService {}

```

服务类将处理共享功能，使其在应用程序的任何部分都可以重用。对于其他服务，创建一个名为`share_service.dart`的新文件并放置基本的类结构，如下所示:

```
class ShareService{}

```

接下来，我们将设置路线并注册服务。我们将使用来自[堆栈包](https://pub.dev/packages/stacked)的`@StackedApp`注释。`@StackedApp`注释允许我们访问两个参数，路由和依赖关系。我们将使用依赖块来注册我们的服务。

在`lib`目录中创建一个名为`app`的文件夹，它将保存我们应用程序的所有配置细节。在这个文件夹中，创建一个名为`app.dart`的新文件。创建一个空类并用`@StackedApp`注释标记它。同样将`StackedLogger`传递给`logger`参数。接下来，在依赖块中将服务注册为`LazySingleton`:

```
import 'package:flutter_share/services/api_service.dart';
import 'package:flutter_share/services/fish_species_service.dart';
import 'package:flutter_share/services/share_service.dart';
import 'package:stacked/stacked_annotations.dart';
@StackedApp(
  dependencies: [
    LazySingleton(classType: ApiService),
    LazySingleton(classType: FishSpecieService),
    LazySingleton(classType: ShareService)
  ],
  logger: StackedLogger(),
)
class AppSetup {}

```

现在，我们可以继续运行 Flutter 命令来生成安装配置所需的所有文件:

```
flutter pub run build_runner build --delete-conflicting-outputs

```

## 设置服务

现在我们已经注册了每个依赖项，我们可以继续从`ApiService`开始填充它们。该服务将处理来自我们应用程序的所有出站 HTTP 请求。它将为每种请求类型提供方法，`GET`、`POST`、`PUT`、`PATCH`和`DELETE`。然而，对于本文，我们将使用一个`GET`请求来调用 [FishWatch API](https://www.fishwatch.gov/developers) 端点。

将 HTTP 包作为`http`导入。在`ApiService`类中，创建一个新函数，它将接受一个 URL，我们将请求指向该 URL。使用 HTTP 包调用 URL，然后检查`statusCode`是否为`200`。如果是真的，我们会归还`decodedResponse`。

此外，我们用一个`try...catch`块包装整个调用，以便捕捉任何可能抛出的异常。这基本上是我们的`ApiService`文件中的所有内容:

```
import 'dart:async';
import 'dart:convert';
import 'dart:io';
import 'package:http/http.dart' as http;
class ApiService {
  Future<dynamic> get(url) async {
    try {
      final response = await http.get(url);
      if (response.statusCode == 200) {
        return json.decode(response.body);
      }
    } on SocketException {
      rethrow;
    } on Exception catch (e) {
      throw Exception(e);
    }
  }
}

```

接下来，我们创建一个类来处理与 API 调用相关的常量，它将保存方案、主机 URL、URIs 等内容。在`lib`目录下，创建一个名为`utils`的新文件夹和一个名为`api_constants.dart`的新文件:

```
class ApiConstants {
  static const scheme = 'https';
  static const baseUrl = 'fishwatch.gov';
  static const path = 'api/species';
  static get getFishSpecies => Uri(
        scheme: scheme,
        host: baseUrl,
        path: path,
      );
}

```

之后，`FishSpecieService`调用远程 API，获取数据并使用我们之前创建的模型解析数据:

```
import 'package:flutter_share/models/fish_response_model.dart';
import 'package:flutter_share/app/app.locator.dart';
import 'package:flutter_share/services/api_service.dart';
import 'package:flutter_share/utils/api_constants.dart';
class FishSpecieService {
  final _apiService = locator<ApiService>();
  Future<List<FishResponseModel?>?> getFishSpecie() async {
    List<FishResponseModel?> fishSpeciesList = [];
    try {
      final response = await _apiService.get(ApiConstants.getFishSpecies);
      if (response != null) {
        for (var i = 0; i < 15; i++) {
          final model = FishResponseModel.fromJson(response[i]);
          fishSpeciesList.add(model);
        }
        return fishSpeciesList;
      }
    } catch (e) {
      rethrow;
    }
  }
}

```

最后，我们创建了`ShareService`，它将公开一个方法来共享来自我们名为`onShare`的应用程序的数据。它将从要共享的列表中获取特定`fishSpecie`的模型。

首先，我们使用 [permission_handler](https://pub.dev/packages/permission_handler) 包来请求`PermissionStatus`存储权限。我们需要本地存储来保存我们想要共享的图像。我们下载图像，然后分享实际的图像，而不是图像的链接。

除此之外，在`AndroidManifest.xml`中，添加写入外部存储器的权限，即将图像下载到应用程序本地存储器:

```
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.flutter_share">
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
    ....
    </manifest>

```

接下来，我们检查状态是否被拒绝。如果是，我们请求许可。

因为我们想要共享一个图像文件，并且我们正在从 FishWatch API 接收到每种鱼的图像链接，所以我们将使用`ImageDownloader`包下载图像。我们将对它调用`downloadImage`方法，并从我们的模型传入图像 URL。我们将响应分配给一个变量`imageId`，它将是下载图像的`imageId`。

接下来，我们检查`imageId`是否为空。如果是，我们返回并中断该函数。在确认了`imageId`不为空之后，我们使用来自`ImageDownloader`的`findPath`方法来获取保存在本地存储器上的图像的路径。这将是移动设备上实际图像文件的路径。

最后，我们从`share_plus`包中调用`shareFiles`对象，并将路径传递给`imageId`，以及作为主题的报价和显示物种名称和蛋白质含量的文本:

```
import 'package:flutter_share/models/fish_response_model.dart';
import 'package:image_downloader/image_downloader.dart';
import 'package:permission_handler/permission_handler.dart';
import 'package:share_plus/share_plus.dart';
class ShareService {
  Future<void> onShare(FishResponseModel fishSpecie) async {
    var status = await Permission.storage.status;
    if (status.isDenied) {
      await Permission.storage.request();
    }
    var imageId = await ImageDownloader.downloadImage(
        fishSpecie.speciesIllustrationPhoto!.src!);
    if (imageId == null) {
      return;
    }
    var path = await ImageDownloader.findPath(imageId);
    await Share.shareFiles(
      [path!],
      text: '''Specie Name: ${fishSpecie.speciesName}\n'''
          '''Protein Content: ${fishSpecie.protein}\n''',
      subject: fishSpecie.quote,
    );
  }
}

```

至此，我们完成了对`ShareService`的设置。我们可以使用这个服务在应用程序的任何地方实现共享功能。

接下来，转到`main.dart`文件。在主块中，我们将调用`WidgetsFlutterBinding.ensureInitialized()`，然后调用`setUpLocator`函数，该函数将设置定位器，使所有注册的依赖项在整个应用程序中都可用:

```
import 'package:flutter/material.dart';
import 'package:flutter_share/app/app.locator.dart';
import 'package:flutter_share/ui/views/home/home_view.dart';
void main() {
  WidgetsFlutterBinding.ensureInitialized();
  setupLocator();
  runApp(const MyApp());
}
class MyApp extends StatelessWidget {
  const MyApp({Key? key}) : super(key: key);
  @override
  Widget build(BuildContext context) {
    return const MaterialApp(
      title: 'Material App',
      home: HomeView(),
    );
  }
}

```

在这个阶段，我们已经成功地为共享功能奠定了基础。现在，我们可以继续构建用户界面，并最终使用该服务。

## 实现共享功能

在`lib`目录中创建一个名为`ui`的新文件夹，它将包含应用程序中的用户界面。我们只会有一个，`home_view`。创建一个名为`home_view`的新文件夹，我们将在其中存储两个文件`home_view.dart`和`home_viewmodel.dart`，它们将分别保存 UI 代码和 UI 逻辑。

在`home_viewmodel.dart`文件中，创建一个名为`HomeViewModel`的类。它将有两个功能，一个是使用`FishSpecieService`从 API 调用`getFishSpecie`，另一个是共享选中的`fishSpecie`。

我们将使用定位器访问各自的服务，然后使用服务获取各自的数据:

```
import 'package:flutter/material.dart';
import 'package:flutter_share/app/app.locator.dart';
import 'package:flutter_share/models/fish_response_model.dart';
import 'package:flutter_share/services/fish_species_service.dart';
import 'package:flutter_share/services/share_service.dart';
import 'package:stacked/stacked.dart';

class HomeViewModel extends BaseViewModel {
  final _fishSpecieService = locator<FishSpecieService>();
  final _shareService = locator<ShareService>();
  List<FishResponseModel?>? fishSpeciesList = [];

  Future<void> getFishSpecies() async {
    try {
      fishSpeciesList = await runBusyFuture(
        _fishSpecieService.getFishSpecie(),
        throwException: true,
      );
      notifyListeners();
    } on Exception catch (e) {
      debugPrint(e.toString());
    }
  }

  Future<void> shareFishSeries(FishResponseModel fishSeries) async {
    try {
      await _shareService.onShare(fishSeries);
    } on Exception catch (e) {
      debugPrint(e.toString());
    }
  }

}

```

## 构建用户界面

最后，在`home_view.dart`文件中，创建一个无状态小部件，这将是这个视图的基础。`HomeView`将显示在`HomeViewModel`中获取并存储到`fishesSpeciesList`中的鱼类种类列表。

`HomeView`最重要的部分是共享按钮，我们将用它来触发共享功能。它将简单地调用`HomeViewModel`中的`shareFishSeries`函数，并向其传递所选种类的`fishSpecieModel`:

```
   Row(
              mainAxisAlignment: MainAxisAlignment.end,
              children: [
                const Icon(Icons.share, size: 18),
                const SizedBox(width: 8),
                GestureDetector(
                  onTap: () {
                    homeViewModel!.shareFishSeries(fishSpecieModel!);
                  },
                  child: const Text(
                    'Share',
                    style: TextStyle(
                      fontSize: 16,
                      fontWeight: FontWeight.w500,
                    ),
                  ),
                )
              ],
            )

```

查看我们的 [`HomeView`的完整代码，在此要诀](https://gist.github.com/Blazebrain/4ebc9c3bdc7924b514565cb04e08e224)。

有了这个，我们的示例应用程序就可以运行了！让我们构建并运行应用程序，看看这一切是如何实现的:

 [https://www.youtube.com/embed/dohGK3KzYAU?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent](https://www.youtube.com/embed/dohGK3KzYAU?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent)

视频

[https://youtu.be/dohGK3KzYAU](https://youtu.be/dohGK3KzYAU)

## 结论

在本教程中，我们已经成功地从一个 API 中提取数据，然后与手机上的其他应用程序共享该数据的详细信息。现在，您可以与其他应用程序共享应用程序中的图像和文本。您还可以随意在这个应用程序上构建更多内容，并根据自己的需要对其进行定制。

我希望你喜欢这个教程！快乐编码。如果你有任何问题，欢迎在 Twitter [@Blazebrain](https://www.twitter.com/Blazebrain01) 或 [LinkedIn](https://www.linkedin.com/in/david-adegoke) 上留言或联系我。