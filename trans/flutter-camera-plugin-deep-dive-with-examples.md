# 颤振相机插件:一个深入的例子

> 原文：<https://blog.logrocket.com/flutter-camera-plugin-deep-dive-with-examples/>

每个移动设备都带有内置的相机应用程序，用于拍摄照片、录制视频，以及一些特定于每个设备的更引人注目的功能。但是如果你正在开发一个需要摄像头访问的应用，那么你必须自己实现摄像头功能。

你可能会问，当默认的相机应用程序已经可用时，为什么我还需要再次实现相机功能？

答案是，如果你想提供一个独特的用户界面来迎合你的应用程序，或者添加设备的默认相机应用程序中不存在的功能，这是必需的。

在本文中，您将学习使用支持 Android 和 iOS 平台的官方[相机包](https://pub.dev/packages/camera)为您的 Flutter 应用程序实现基本的相机功能。

## 应用概述

在深入研究代码之前，让我们回顾一下我们将要构建的应用程序。最终的应用将包含大多数基本的相机功能，包括:

我们还将介绍如何解决一些常见问题，并为 Flutter 相机添加一些受欢迎的功能，包括:

最终的应用程序用户界面将如下所示:

![The final Flutter camera demo ](img/57535822ec077a5a76fae7562a572bf2.png)

## 入门指南

使用以下命令创建一个新的颤振项目:

```
flutter create flutter_camera_demo

```

您可以使用您最喜欢的 IDE 打开项目，但是对于这个例子，我将使用 VS 代码:

```
code flutter_camera_demo

```

将以下依赖项添加到您的`pubspec.yaml`文件中:

用以下内容替换您的`main.dart`文件的内容:

```
import 'package:flutter/material.dart';

import 'screens/camera_screen.dart';

Future<void> main() async {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      debugShowCheckedModeBanner: false,
      home: CameraScreen(),
    );
  }
}

```

`CameraScreen`类将包含所有相机功能的代码及其用户界面。我们将在稍后添加它，但在此之前，我们必须在设备上安装可用的摄像头。

## 检索可用的摄像机

在`main.dart`文件中，定义一个名为`cameras`的全局变量，我们将在其中存储可用摄像机的列表。这将有助于我们以后方便地引用它们。

```
import 'package:camera/camera.dart';

List<CameraDescription> cameras = [];

```

在使用`availableCameras()`方法初始化应用程序之前，您可以在`main()`函数中检索相机——只要确保该函数是异步的，因为它必须等待检索设备的可用相机，通常 Flutter 的`main()`函数是一个简单的函数，只有`runApp()`调用:

```
Future<void> main() async {
  try {
    WidgetsFlutterBinding.ensureInitialized();
    cameras = await availableCameras();
  } on CameraException catch (e) {
    print('Error in fetching the cameras: $e');
  }
  runApp(MyApp());
}

```

## 初始化相机

创建一个名为`camera_screen.dart`的新文件，并在其中定义`CameraScreen`有状态小部件。

```
import 'package:camera/camera.dart';
import 'package:flutter/material.dart';

import '../main.dart';

class CameraScreen extends StatefulWidget {
  @override
  _CameraScreenState createState() => _CameraScreenState();
}

class _CameraScreenState extends State<CameraScreen> {
  @override
  Widget build(BuildContext context) {
    return Scaffold();
  }
}

```

为摄像机定义一个控制器，并为`isCameraInitialized`布尔变量定义一个值，您可以使用它来轻松了解摄像机是否已初始化，并相应地刷新 UI:

```
class _CameraScreenState extends State<CameraScreen> {
  CameraController? controller;
  bool _isCameraInitialized = false;

  @override
  Widget build(BuildContext context) {
    return Scaffold();
  }
}

```

控制器将帮助您访问相机的不同功能，但在使用它们之前，您必须初始化相机。

创建一个名为`onNewCameraSelected()`的新方法。这种方法将有助于处理两种情况:

1.  初始化新的摄像机控制器，这是启动摄像机屏幕所需要的
2.  当用户翻转相机视图或改变相机的质量时，处置先前的控制器并用具有不同属性的新控制器来替换它

```
class _CameraScreenState extends State {
   // ...

   void onNewCameraSelected(CameraDescription cameraDescription) async {
      final previousCameraController = controller;
      // Instantiating the camera controller
      final CameraController cameraController = CameraController(
        cameraDescription,
        ResolutionPreset.high,
        imageFormatGroup: ImageFormatGroup.jpeg,
      );

      // Dispose the previous controller
      await previousCameraController?.dispose();

      // Replace with the new controller
      if (mounted) {
         setState(() {
           controller = cameraController;
        });
      }

      // Update UI if controller updated
      cameraController.addListener(() {
        if (mounted) setState(() {});
      });

      // Initialize controller
      try {
        await cameraController.initialize();
      } on CameraException catch (e) {
        print('Error initializing camera: $e');
      }

      // Update the Boolean
      if (mounted) {
        setState(() {
           _isCameraInitialized = controller!.value.isInitialized;
        });
      }
   }

   @override
   Widget build(BuildContext context) {
      return Scaffold();
   }
}
```

在`initState()`方法中调用这个函数，并将`cameras[0]`作为`CameraDescription`传递。`camera`列表的第一个索引通常是设备的后置摄像头。

*   `cameras`列表的索引`0`—背部摄像头
*   `cameras`列表的索引`1`—前置摄像头

```
class _CameraScreenState extends State {
   // ...

   @override
   void initState() {
      onNewCameraSelected(cameras[0]);
      super.initState();
   }

   @override
   Widget build(BuildContext context) {
      return Scaffold();
   }
}

```

另外，不要忘记在相机不活动时释放`dispose()`方法中的内存:

```
@override
void dispose() {
  controller?.dispose();
  super.dispose();
}

```

## 处理相机生命周期状态

在任何设备上运行相机都被认为是一项内存密集型任务，因此如何以及何时释放内存资源非常重要。应用程序的生命周期状态有助于理解状态变化，以便作为开发人员的您可以做出相应的反应。

在 Flutter 中，您可以添加`[WidgetsBindingObserver](https://dart.dev/guides/language/language-tour#adding-features-to-a-class-mixins)` mixin，并通过覆盖`didChangeAppLifecycleState()`方法来管理生命周期的变化。

```
class _CameraScreenState extends State<CameraScreen>
    with WidgetsBindingObserver {

  // ...

  @override
  void didChangeAppLifecycleState(AppLifecycleState state) {
    final CameraController? cameraController = controller;

    // App state changed before we got the chance to initialize.
    if (cameraController == null || !cameraController.value.isInitialized) {
      return;
    }

    if (state == AppLifecycleState.inactive) {
      // Free up memory when camera not active
      cameraController.dispose();
    } else if (state == AppLifecycleState.resumed) {
      // Reinitialize the camera with same properties
      onNewCameraSelected(cameraController.description);
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold();
  }
}

```

## 添加相机预览

现在我们已经完成了初始化和管理相机状态，我们可以定义一个非常基本的用户界面来预览相机输出。

Flutter 的相机插件附带了一个名为`buildPreview()`的方法，用于显示相机输出，用户界面可以定义如下:

```
class _CameraScreenState extends State<CameraScreen>
    with WidgetsBindingObserver {

  // ...

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: _isCameraInitialized
          ? AspectRatio(
              aspectRatio: 1 / controller!.value.aspectRatio,
              child: controller!.buildPreview(),
            )
          : Container(),
    );
  }
}

```

预览将如下所示:

![Camera preview](img/a0583bde548b5973254db251238f3266.png)

你会注意到设备状态栏在顶部可见；您可以通过将以下内容添加到`initState()`方法中来隐藏它，以防止它遮挡摄像机视图:

```
@override
void initState() {
  // Hide the status bar
  SystemChrome.setEnabledSystemUIOverlays([]);

  onNewCameraSelected(cameras[0]);
  super.initState();
}

```

基本的相机预览准备好了！现在，我们可以开始给相机增加功能了。

## 添加捕获质量选择器

您可以使用`ResolutionPreset`来定义摄像机视图的质量。初始化相机时，我们使用了`ResolutionPreset.high`。

要更改摄像机视图的质量，必须用新值重新初始化摄像机控制器。我们将在相机视图的右上角添加一个下拉菜单，用户可以在这里选择分辨率预设。

在类中添加两个变量，一个用于保存所有的`ResolutionPreset`值，另一个用于存储`currentResolutionPreset`值。

```
final resolutionPresets = ResolutionPreset.values;
ResolutionPreset currentResolutionPreset = ResolutionPreset.high;

```

修改`onNewCameraSelected()`方法中的摄像机控制器实例，以使用`currentResolutionPreset`变量:

```
final CameraController cameraController = CameraController(
  cameraDescription,
  currentResolutionPreset,
  imageFormatGroup: ImageFormatGroup.jpeg,
);

```

`DropdownButton`可以定义如下:

```
DropdownButton<ResolutionPreset>(
  dropdownColor: Colors.black87,
  underline: Container(),
  value: currentResolutionPreset,
  items: [
    for (ResolutionPreset preset
        in resolutionPresets)
      DropdownMenuItem(
        child: Text(
          preset
              .toString()
              .split('.')[1]
              .toUpperCase(),
          style:
              TextStyle(color: Colors.white),
        ),
        value: preset,
      )
  ],
  onChanged: (value) {
    setState(() {
      currentResolutionPreset = value!;
      _isCameraInitialized = false;
    });
    onNewCameraSelected(controller!.description);
  },
  hint: Text("Select item"),
)

```

调用`onNewCameraSelected()`方法用新的质量值重新初始化摄像机控制器。

![A gif demonstrating the camera quality selector](img/1673211938c5abf227c0ef2f2f9911c8.png)

## 缩放控制

您可以使用控制器上的`setZoomLevel()`方法设置摄像机的变焦级别，并传递变焦值。

在确定缩放级别之前，您应该知道设备摄像头的最小和最大缩放级别。

定义三个变量:

```
double _minAvailableZoom = 1.0;
double _maxAvailableZoom = 1.0;
double _currentZoomLevel = 1.0;

```

获取这些值的最佳位置是在相机初始化后从`onNewCameraSelected()`方法内部获取。您可以使用以下方法获得最小和最大缩放级别:

```
cameraController
    .getMaxZoomLevel()
    .then((value) => _maxAvailableZoom = value);

cameraController
    .getMinZoomLevel()
    .then((value) => _minAvailableZoom = value);

```

您可以实现一个滑块，让用户选择适当的缩放级别；构建`Slider`的代码如下:

```
Row(
  children: [
    Expanded(
      child: Slider(
        value: _currentZoomLevel,
        min: _minAvailableZoom,
        max: _maxAvailableZoom,
        activeColor: Colors.white,
        inactiveColor: Colors.white30,
        onChanged: (value) async {
          setState(() {
            _currentZoomLevel = value;
          });
          await controller!.setZoomLevel(value);
        },
      ),
    ),
    Container(
      decoration: BoxDecoration(
        color: Colors.black87,
        borderRadius: BorderRadius.circular(10.0),
      ),
      child: Padding(
        padding: const EdgeInsets.all(8.0),
        child: Text(
          _currentZoomLevel.toStringAsFixed(1) +
              'x',
          style: TextStyle(color: Colors.white),
        ),
      ),
    ),
  ],
)

```

每次拖动滑块时，都会调用`setZoomLevel()`方法来更新缩放级别值。在上面的代码中，我们还添加了一个`Text`小部件来显示当前的缩放级别值。

![A gif demonstrating the camera zoom functionality](img/da7eb6b459ab9c2619c324b71882a7b2.png)

## 曝光控制

您可以使用控制器上的`setExposureOffset()`方法设置相机的曝光偏移值，并传递曝光值。

首先，让我们检索设备支持的相机曝光的最小值和最大值。

定义三个变量:

```
double _minAvailableExposureOffset = 0.0;
double _maxAvailableExposureOffset = 0.0;
double _currentExposureOffset = 0.0;

```

在`onNewCameraSelected()`方法中获取最小和最大相机曝光值:

```
cameraController
    .getMinExposureOffset()
    .then((value) => _minAvailableExposureOffset = value);

cameraController
    .getMaxExposureOffset()
    .then((value) => _maxAvailableExposureOffset = value);

```

我们将建立一个垂直滑块来显示和控制曝光偏移。材质设计没有提供垂直的`Slider`小部件，但是你可以使用带有三个直角转弯的 [`RotatedBox`类](https://api.flutter.dev/flutter/widgets/RotatedBox-class.html)来实现。

```
Container(
  decoration: BoxDecoration(
    color: Colors.white,
    borderRadius: BorderRadius.circular(10.0),
  ),
  child: Padding(
    padding: const EdgeInsets.all(8.0),
    child: Text(
      _currentExposureOffset.toStringAsFixed(1) + 'x',
      style: TextStyle(color: Colors.black),
    ),
  ),
),
Expanded(
  child: RotatedBox(
    quarterTurns: 3,
    child: Container(
      height: 30,
      child: Slider(
        value: _currentExposureOffset,
        min: _minAvailableExposureOffset,
        max: _maxAvailableExposureOffset,
        activeColor: Colors.white,
        inactiveColor: Colors.white30,
        onChanged: (value) async {
          setState(() {
            _currentExposureOffset = value;
          });
          await controller!.setExposureOffset(value);
        },
      ),
    ),
  ),
)

```

在上面的代码中，我们在滑块的顶部构建了一个`Text`小部件来显示当前的曝光偏移值。

![A gif demonstrating the camera exposure offset slider](img/040b4ca24617e08972f1572679864de2.png)

## 闪光模式选择器

您可以使用`setFlashMode()`方法并传递一个`FlashMode`值来设置相机的闪光模式。

定义一个变量来存储闪光模式的当前值:

```
FlashMode? _currentFlashMode;

```

然后在`onNewCameraSelected()`方法中获取初始闪光模式值:

```
_currentFlashMode = controller!.value.flashMode;

```

在用户界面上，我们将连续显示可用的闪光模式，用户可以点击其中任何一个来选择闪光模式。

```
Row(
  mainAxisAlignment: MainAxisAlignment.spaceBetween,
  children: [
    InkWell(
      onTap: () async {
        setState(() {
          _currentFlashMode = FlashMode.off;
        });
        await controller!.setFlashMode(
          FlashMode.off,
        );
      },
      child: Icon(
        Icons.flash_off,
        color: _currentFlashMode == FlashMode.off
            ? Colors.amber
            : Colors.white,
      ),
    ),
    InkWell(
      onTap: () async {
        setState(() {
          _currentFlashMode = FlashMode.auto;
        });
        await controller!.setFlashMode(
          FlashMode.auto,
        );
      },
      child: Icon(
        Icons.flash_auto,
        color: _currentFlashMode == FlashMode.auto
            ? Colors.amber
            : Colors.white,
      ),
    ),
    InkWell(
      onTap: () async {
        setState(() {
          _isCameraInitialized = false;
        });
        onNewCameraSelected(
          cameras[_isRearCameraSelected ? 1 : 0],
        );
        setState(() {
          _isRearCameraSelected = !_isRearCameraSelected;
        });
      },
      child: Icon(
        Icons.flash_on,
        color: _currentFlashMode == FlashMode.always
            ? Colors.amber
            : Colors.white,
      ),
    ),
    InkWell(
      onTap: () async {
        setState(() {
          _currentFlashMode = FlashMode.torch;
        });
        await controller!.setFlashMode(
          FlashMode.torch,
        );
      },
      child: Icon(
        Icons.highlight,
        color: _currentFlashMode == FlashMode.torch
            ? Colors.amber
            : Colors.white,
      ),
    ),
  ],
)

```

所选的闪光模式将以琥珀色而不是白色突出显示。

![A gif demonstrating the camera flash selector](img/b38319a7d37a58e3f8e949b94db8d2a8.png)

## 翻转相机开关

要在前后摄像头之间切换，您必须通过向`onNewCameraSelected()`方法提供新值来重新初始化摄像头。

定义一个布尔变量，了解是否选择后置摄像头，否则选择前置摄像头。

```
bool _isRearCameraSelected = true;

```

之前，我们使用后置摄像头进行初始化，所以我们将在这个布尔值中存储`true`。

现在，我们将展示一个在后置摄像头和前置摄像头之间切换的按钮:

```
InkWell(
  onTap: () {
    setState(() {
      _isCameraInitialized = false;
    });
    onNewCameraSelected(
      cameras[_isRearCameraSelected ? 0 : 1],
    );
    setState(() {
      _isRearCameraSelected = !_isRearCameraSelected;
    });
  },
  child: Stack(
    alignment: Alignment.center,
    children: [
      Icon(
        Icons.circle,
        color: Colors.black38,
        size: 60,
      ),
      Icon(
        _isRearCameraSelected
            ? Icons.camera_front
            : Icons.camera_rear,
        color: Colors.white,
        size: 30,
      ),
    ],
  ),
)

```

在上面的代码中，如果`_isRearCameraSelected`布尔值为`true`，则将`0`作为索引传递给`cameras`(翻转到前置摄像头)。否则，通过`1`作为索引(翻转到后置摄像头)。

![A gif demonstrating the camera flip toggle](img/597cc61ed825db748a15170cd47769d1.png)

## 捕捉图像

您可以使用相机控制器上的`takePicture()`方法使用设备相机拍照。捕获的图片作为一个`XFile`(这是一个跨平台的文件抽象)返回。

让我们定义一个函数来处理图片的捕获:

```
Future<XFile?> takePicture() async {
  final CameraController? cameraController = controller;
  if (cameraController!.value.isTakingPicture) {
    // A capture is already pending, do nothing.
    return null;
  }
  try {
    XFile file = await cameraController.takePicture();
    return file;
  } on CameraException catch (e) {
    print('Error occured while taking picture: $e');
    return null;
  }
}

```

如果抓取成功，该函数将抓取的图片返回为`XFile`，否则返回`null`。

捕获按钮可以定义如下:

```
InkWell(
  onTap: () async {
    XFile? rawImage = await takePicture();
    File imageFile = File(rawImage!.path);

    int currentUnix = DateTime.now().millisecondsSinceEpoch;
    final directory = await getApplicationDocumentsDirectory();
    String fileFormat = imageFile.path.split('.').last;

    await imageFile.copy(
      '${directory.path}/$currentUnix.$fileFormat',
    );
  },
  child: Stack(
    alignment: Alignment.center,
    children: [
      Icon(Icons.circle, color: Colors.white38, size: 80),
      Icon(Icons.circle, color: Colors.white, size: 65),
    ],
  ),
)

```

成功捕获后，它将图片保存到应用程序的 documents 目录中，并以时间戳作为图像名称，以便以后可以方便地访问所有捕获的图片。

![A gif demonstrating the camera capture preview screen](img/6331db46c3578c8046f846b96a529c9e.png)

## 在图像和视频模式之间切换

您可以连续使用两个`TextButton`在图像和视频模式之间切换。

定义一个布尔变量来存储所选模式:

```
bool _isVideoCameraSelected = false;

```

用户界面按钮可以这样定义:

```
Row(
  children: [
    Expanded(
      child: Padding(
        padding: const EdgeInsets.only(
          left: 8.0,
          right: 4.0,
        ),
        child: TextButton(
          onPressed: _isRecordingInProgress
              ? null
              : () {
                  if (_isVideoCameraSelected) {
                    setState(() {
                      _isVideoCameraSelected = false;
                    });
                  }
                },
          style: TextButton.styleFrom(
            primary: _isVideoCameraSelected
                ? Colors.black54
                : Colors.black,
            backgroundColor: _isVideoCameraSelected
                ? Colors.white30
                : Colors.white,
          ),
          child: Text('IMAGE'),
        ),
      ),
    ),
    Expanded(
      child: Padding(
        padding: const EdgeInsets.only(
            left: 4.0, right: 8.0),
        child: TextButton(
          onPressed: () {
            if (!_isVideoCameraSelected) {
              setState(() {
                _isVideoCameraSelected = true;
              });
            }
          },
          style: TextButton.styleFrom(
            primary: _isVideoCameraSelected
                ? Colors.black
                : Colors.black54,
            backgroundColor: _isVideoCameraSelected
                ? Colors.white
                : Colors.white30,
          ),
          child: Text('VIDEO'),
        ),
      ),
    ),
  ],
)

```

![A gif demonstrating the camera toggle for image and video mode](img/01e2899f1cad1df473b36717d282be03.png)

## 显像记录

要使用设备摄像机管理视频录制，您必须定义四个函数来处理录制过程的状态:

*   `startVideoRecording()`开始录像过程
*   `stopVideoRecording()`停止录像过程
*   `pauseVideoRecording()`暂停正在进行的录像
*   `resumeVideoRecording()`如果处于暂停状态，则恢复录制

此外，定义一个布尔变量来存储记录是否正在进行:

```
bool _isRecordingInProgress = false;

```

### 开始录音

您可以通过调用摄像机控制器上的`startVideoRecording()`方法开始录像:

```
Future<void> startVideoRecording() async {
  final CameraController? cameraController = controller;
  if (controller!.value.isRecordingVideo) {
    // A recording has already started, do nothing.
    return;
  }
  try {
    await cameraController!.startVideoRecording();
    setState(() {
      _isRecordingInProgress = true;
      print(_isRecordingInProgress);
    });
  } on CameraException catch (e) {
    print('Error starting to record video: $e');
  }
}

```

开始记录后，`_isRecordingInProgress`的布尔值被设置为`true`。

### 停止录制

可以通过调用控制器上的`stopVideoRecording()`方法来停止正在进行的录像:

```
Future<XFile?> stopVideoRecording() async {
  if (!controller!.value.isRecordingVideo) {
    // Recording is already is stopped state
    return null;
  }
  try {
    XFile file = await controller!.stopVideoRecording();
    setState(() {
      _isRecordingInProgress = false;
      print(_isRecordingInProgress);
    });
    return file;
  } on CameraException catch (e) {
    print('Error stopping video recording: $e');
    return null;
  }
}

```

记录停止后，布尔值`_isRecordingInProgress`被设置为`false`。`stopVideoRecording()`方法以`XFile`格式返回视频文件。

### 暂停录制

您可以通过调用控制器上的`pauseVideoRecording()`方法暂停正在进行的视频录制:

```
Future<void> pauseVideoRecording() async {
  if (!controller!.value.isRecordingVideo) {
    // Video recording is not in progress
    return;
  }
  try {
    await controller!.pauseVideoRecording();
  } on CameraException catch (e) {
    print('Error pausing video recording: $e');
  }
}

```

### 继续录制

您可以通过调用控制器上的`resumeVideoRecording()`方法来恢复暂停的视频录制:

```
Future<void> resumeVideoRecording() async {
  if (!controller!.value.isRecordingVideo) {
    // No video recording was in progress
    return;
  }
  try {
    await controller!.resumeVideoRecording();
  } on CameraException catch (e) {
    print('Error resuming video recording: $e');
  }
}

```

### 开始和停止录制的按钮

您可以通过检查`_isVideoCameraSelected`布尔值是否为`true`来修改拍照按钮，并在该位置显示视频开始/停止按钮。

```
InkWell(
  onTap: _isVideoCameraSelected
      ? () async {
          if (_isRecordingInProgress) {
            XFile? rawVideo = await stopVideoRecording();
            File videoFile = File(rawVideo!.path);

            int currentUnix = DateTime.now().millisecondsSinceEpoch;

            final directory = await getApplicationDocumentsDirectory();
            String fileFormat = videoFile.path.split('.').last;

            _videoFile = await videoFile.copy(
              '${directory.path}/$currentUnix.$fileFormat',
            );

            _startVideoPlayer();
          } else {
            await startVideoRecording();
          }
        }
      : () async {
          // code to handle image clicking
        },
  child: Stack(
    alignment: Alignment.center,
    children: [
      Icon(
        Icons.circle,
        color: _isVideoCameraSelected
            ? Colors.white
            : Colors.white38,
        size: 80,
      ),
      Icon(
        Icons.circle,
        color: _isVideoCameraSelected
            ? Colors.red
            : Colors.white,
        size: 65,
      ),
      _isVideoCameraSelected &&
              _isRecordingInProgress
          ? Icon(
              Icons.stop_rounded,
              color: Colors.white,
              size: 32,
            )
          : Container(),
    ],
  ),
) 

```

同样，在录制过程中，您可以检查布尔值`_isRecordingInProgress`是否为`true`，并显示暂停/继续按钮，而不是相机翻转按钮。

![A gif demonstrating the camera in video mode](img/0071090ce4518a95aad46df48b8beb03.png)

## 上次捕获的预览

让我们在相机视图的右下角显示上一次拍摄的图片或录制的视频的预览。

为了实现这一点，我们还必须定义一个视频回放的方法。

定义视频播放器控制器:

```
VideoPlayerController? videoController;

```

以下方法用于使用存储在`_videoFile`变量中的视频文件启动视频播放器:

```
Future<void> _startVideoPlayer() async {
  if (_videoFile != null) {
    videoController = VideoPlayerController.file(_videoFile!);
    await videoController!.initialize().then((_) {
      // Ensure the first frame is shown after the video is initialized,
      // even before the play button has been pressed.
      setState(() {});
    });
    await videoController!.setLooping(true);
    await videoController!.play();
  }
}

```

另外，不要忘记在`dispose()`方法中释放内存:

```
@override
void dispose() {
  // ...
  videoController?.dispose();
  super.dispose();
}

```

预览的用户界面可以定义如下:

```
Container(
  width: 60,
  height: 60,
  decoration: BoxDecoration(
    color: Colors.black,
    borderRadius: BorderRadius.circular(10.0),
    border: Border.all(color: Colors.white, width: 2),
    image: _imageFile != null
        ? DecorationImage(
            image: FileImage(_imageFile!),
            fit: BoxFit.cover,
          )
        : null,
  ),
  child: videoController != null && videoController!.value.isInitialized
      ? ClipRRect(
          borderRadius: BorderRadius.circular(8.0),
          child: AspectRatio(
            aspectRatio: videoController!.value.aspectRatio,
            child: VideoPlayer(videoController!),
          ),
        )
      : Container(),
)

```

## 检索图像/视频文件

由于我们已经将所有捕获的图像和录制的视频存储在应用程序文档目录的单个文件夹中，因此您可以轻松检索所有文件。如果您想在图库视图中显示它们，或者如果您只想在预览中显示最后捕获的图像或视频文件的缩略图，这可能是必要的。

我们将定义一种方法，当新的捕获或记录完成时，该方法也将刷新预览图像/视频。

```
// To store the retrieved files
List<File> allFileList = [];

refreshAlreadyCapturedImages() async {
  // Get the directory
  final directory = await getApplicationDocumentsDirectory();
  List<FileSystemEntity> fileList = await directory.list().toList();
  allFileList.clear();

  List<Map<int, dynamic>> fileNames = [];

  // Searching for all the image and video files using 
  // their default format, and storing them
  fileList.forEach((file) {
    if (file.path.contains('.jpg') || file.path.contains('.mp4')) {
      allFileList.add(File(file.path));

      String name = file.path.split('/').last.split('.').first;
      fileNames.add({0: int.parse(name), 1: file.path.split('/').last});
    }
  });

  // Retrieving the recent file
  if (fileNames.isNotEmpty) {
    final recentFile =
        fileNames.reduce((curr, next) => curr[0] > next[0] ? curr : next);
    String recentFileName = recentFile[1];
    // Checking whether it is an image or a video file
    if (recentFileName.contains('.mp4')) {
      _videoFile = File('${directory.path}/$recentFileName');
      _startVideoPlayer();
    } else {
      _imageFile = File('${directory.path}/$recentFileName');
    }

    setState(() {});
  }
}

```

## 常见问题和疑问

本文涵盖了在 Flutter 中使用相机插件的大部分重要主题，但在应用程序中实现相机时，您仍可能会面临某些问题。以下是一些常见问题:

### 解决拉伸相机预览

这是展示相机预览时最常见的错误之一。如果您尝试使用屏幕宽度和高度来估计预览大小，可能会出现拉伸的相机预览:

```
SizedBox(
  width: MediaQuery.of(context).size.width,
  height: MediaQuery.of(context).size.height,
  child: controller!.buildPreview(),
)

```

但这不是展示预览的正确方式。您应该始终使用可通过`CameraController`访问的摄像机预览的宽高比:

```
AspectRatio(
  aspectRatio: 1 / controller!.value.aspectRatio,
  child: controller!.buildPreview(),
)

```

> `CameraController`以(height : width)的形式返回纵横比值，但需要以(width : height)的格式传递给`AspectRatio`小部件，所以这里是通过取预览纵横比的倒数(即 1 / aspectRatio)来完成的。

还有另一种正确预览相机的方法，使用`CameraPreview`小部件:

```
CameraPreview(
  controller!,
)

```

![Flutter Camera Stretched Preview](img/ee0b1a1cbe5d20ad7d0ebe782f10b3bd.png)

### 向摄像机添加覆盖

在某些情况下，您可能想要在相机预览的顶部显示图像覆盖。让我们尝试添加相机取景器图像。

首先，在您的 Flutter 项目的根目录下创建一个名为`assets`的新文件夹。将图像文件添加到该文件夹，并指定其在`pubspec.yaml`文件中的路径:

```
flutter:
  assets:
    - assets/camera_aim.png

```

运行以下命令导入映像:

```
flutter pub get

```

要在相机预览上显示图像，您必须用`Stack`包装预览小部件，并在预览下方使用`Image`小部件:

```
AspectRatio(
  aspectRatio: 1 / controller!.value.aspectRatio,
  child: Stack(
    children: [
      controller!.buildPreview(),
      Center(
        child: Image.asset(
          'assets/camera_aim.png',
          width: 150,
          height: 150,
        ),
      ),
    ],
  ),
)

```

`Center`控件用于使取景器出现在预览的中心。如果想让图像半透明，用不透明度值在 0 到 1 之间的`Opacity`小部件包裹`Image`(0 表示完全透明，1 表示完全不透明)。

![Flutter Camera Overlay](img/675902059ba3bd4e121542d11c68cb71.png)

如果你想改变覆盖图的颜色，你可以使用`Image`部件的`color`属性，这有助于它与图像的每个像素混合:

```
Image.asset(
  'assets/camera_aim.png',
  color: Colors.greenAccent,
  width: 150,
  height: 150,
)

```

![Flutter Camera Overlay with Color](img/b5f4e8c83a26607058bebc9859005426.png)

### 正在检查相机权限状态

在将应用程序发布到生产环境之前，正确处理相机权限非常重要。在 Flutter 应用中管理权限的最好方法是使用 permission_handler 插件。

将插件添加到您的`pubspec.yaml`文件中:

```
dependencies:
  permission_handler: ^8.3.0

```

运行以下命令安装插件:

```
flutter pub get

```

对于 Android 平台，将相机权限添加到您的`AndroidManifest.xml`文件的`manifest`标签中:

```
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.souvikbiswas.flutter_camera_demo">
   <!-- Permission for accessing `camera` -->
   <uses-permission android:name="android.permission.CAMERA"/>
   <application ...>

```

另外，将 Android 的`compileSdkVersion`和`targetSdkVersion`设置为 31，并确保`AndroidManifest.xml`文件在`activity`标签中指定了`android:exported=``"``true``"`:

```
<activity
    android:name=".MainActivity"
    android:exported="true"

```

对于 iOS 平台，在您的`ios/Podfile`中添加以下内容:

```
post_install do |installer|
  installer.pods_project.targets.each do |target|
    flutter_additional_ios_build_settings(target)
    # Add the following -->
    target.build_configurations.each do |config|
      config.build_settings['GCC_PREPROCESSOR_DEFINITIONS'] ||= [
        '$(inherited)',
        ## dart: PermissionGroup.camera
        'PERMISSION_CAMERA=1',
      ]
    end
  end
end

```

现在，将`initState()`方法修改为:

```
@override
void initState() {
  // Hide the status bar in Android
  SystemChrome.setEnabledSystemUIOverlays([]);
  // Set and initialize the new camera
  onNewCameraSelected(cameras[0]);
  refreshAlreadyCapturedImages();
  super.initState();
}

```

致以下内容:

```
@override
void initState() {
  // Hide the status bar in Android
  SystemChrome.setEnabledSystemUIOverlays([]);
  getPermissionStatus();
  super.initState();
}

```

`onNewCameraSelected`和`refreshAlreadyCapturedImages`方法将被移到`getPermissionStatus`中:

```
getPermissionStatus() async {
  await Permission.camera.request();
  var status = await Permission.camera.status;
  if (status.isGranted) {
    log('Camera Permission: GRANTED');
    setState(() {
      _isCameraPermissionGranted = true;
    });
    // Set and initialize the new camera
    onNewCameraSelected(cameras[0]);
    refreshAlreadyCapturedImages();
  } else {
    log('Camera Permission: DENIED');
  }
}

```

> 注意:如果你也想用摄像机录制视频，你也需要征得麦克风的同意，方式类似。

在上面的代码片段中，首先，它请求相机权限。如果许可被授予，那么前面的两个方法被调用来帮助初始化摄像机，并且`_isCameraPermissionGranted`布尔值被更新为`true`。

最初可以将布尔值定义为`false`:

```
bool _isCameraPermissionGranted = false;

```

最后，当用户拒绝访问 camera 时，您需要更新 UI。您可以使用布尔值来显示替代小部件。在`Scaffold`正文中，添加以下内容:

```
_isCameraPermissionGranted
    ? PreviewWidgetHere()
    : Column(
        mainAxisAlignment: MainAxisAlignment.center,
        children: [
          Row(),
          Text(
            'Permission denied',
            style: TextStyle(color: Colors.white),
          ),
          SizedBox(height: 16),
          ElevatedButton(
            onPressed: () {
              getPermissionStatus();
            },
            child: Text('Give permission'),
          ),
        ],
      )

```

当权限被拒绝时，会显示文本和一个按钮，以触发`getPermissionStatus()`方法再次请求相机权限。

![Requesting Permission for Flutter Camera](img/b69f5da76ea0f34cca8dab2ea39d9b22.png)

### 设置相机焦点

`camera`插件带有`setFocusPoint`方法，通过传递偏移位置来帮助改变焦点。

将以下方法添加到`CameraScreen`类中:

```
void onViewFinderTap(TapDownDetails details, BoxConstraints constraints) {
  if (controller == null) {
    return;
  }
  final offset = Offset(
    details.localPosition.dx / constraints.maxWidth,
    details.localPosition.dy / constraints.maxHeight,
  );
  controller!.setExposurePoint(offset);
  controller!.setFocusPoint(offset);
}

```

`setExposurePoint`方法用于在聚焦的物体上设置正确的曝光水平，以使可见度更好。现在，您需要传递用户在相机内部点击的位置的详细信息。

这样修改`CameraPreview`:

```
 class="language-dart hljs">CameraPreview(
  controller!,
  child: LayoutBuilder(builder:
      (BuildContext context, BoxConstraints constraints) {
    return GestureDetector(
      behavior: HitTestBehavior.opaque,
      onTapDown: (details) =>
          onViewFinderTap(details, constraints),
    );
  }),
)

```

一个`GestureDetector`小部件用于获取用户点击的位置，并将其传递给`onViewFinderTap`方法。

![Flutter Camera Focus](img/7393618a22832b907b56dc537dc5e6f1.png)

## 包扎

恭喜🎉，您已经创建了一个具有所有基本功能的成熟相机应用程序。现在，您甚至可以向该应用程序添加自定义功能，并自定义用户界面以匹配应用程序的设计调色板。

感谢您阅读文章！如果你对这篇文章或例子有任何建议或问题，请随时通过 [Twitter](https://twitter.com/sbis04) 或 [LinkedIn](https://www.linkedin.com/in/sbis04/) 与我联系。你也可以在我的 GitHub 上找到这个应用[的资源库。](https://github.com/sbis04/flutter_camera_demo)

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)