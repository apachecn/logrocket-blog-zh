# React 原生相机简介

> 原文：<https://blog.logrocket.com/intro-to-react-native-camera/>

## 介绍

对于 React 原生应用程序来说，使用摄像头、QR 扫描和文本识别是一个复杂的问题。在本地开发中，这是一项需要操作摄像机的大量工作。如果您在使用相机时遇到 React Native 中跨平台移动开发的问题，React Native Camera 正是您所需要的。

今天我回来谈谈我们如何利用 React Native 轻松处理手机摄像头。我来给你介绍一下 React 原生相机。

在本文中，我将通过开发一个二维码扫描仪应用程序来演示 React Native Camera。该应用程序将使我们能够实时扫描二维码，并通过应用程序在屏幕上显示其内容。

## 什么是 React 原生相机？

[React Native 相机](https://github.com/react-native-camera/react-native-camera)是 React Native 中的一个综合性相机组件。它可以让你控制相机，并与本机操作系统和设备硬件进行通信。

React 原生相机支持以下功能:

*   照片
*   录像
*   人脸检测
*   条形码扫描
*   文本识别

这是一个完全开源的项目，随时欢迎拉取请求。它还附带了很棒的[文档](https://react-native-camera.github.io/react-native-camera/)。

它在 [GitHub](https://github.com/react-native-camera/react-native-camera) 上有 9.2k 颗星，在 [npm](https://www.npmjs.com/package/react-native-camera) 上每月有 17.5 万次用户下载。

React 原生相机基于 [`expo-camera`](https://docs.expo.io/versions/v41.0.0/sdk/camera/) 模块。所以，Expo 可以用 React 原生相机。

您可以通过运行以下命令来安装`expo-camera`:

```
expo install expo-camera

```

它还可以进行人脸检测、条形码扫描和拍照。你只需要从`expo-camera`导入`Camera`:

```
import { Camera } from 'expo-camera';

```

## 用 React 原生相机构建 QR 扫描仪

现在，为了正确理解 React Native Camera，让我们用 QR 扫描仪创建一个简单的 React Native 项目。我将使用 iOS 设备进行构建和测试。

### 设置项目

在开始之前，我需要用下面几行代码创建一个新的 React 本地项目:

```
react-native init react_native_scanner
cd react_native_scanner
npm run ios

```

太棒了，我们已经成功创建了 React 本机应用程序。

接下来，我们应该为我们的项目安装 React 原生相机包。让我们使用以下命令进行安装:

```
npm install react-native-camera --save
cd ios && pod install && cd ..

```

接下来，我们需要为我们的应用程序`Info.plist`添加 iOS 权限:

```
<key>NSCameraUsageDescription</key>
<string>Your message to user when the camera is accessed for the first time</string>

<key>NSPhotoLibraryAddUsageDescription</key>
<string>Your message to user when the photo library is accessed for the first time</string>

<key>NSPhotoLibraryUsageDescription</key>
<string>Your message to user when the photo library is accessed for the first time</string>

<key>NSMicrophoneUsageDescription</key>
<string>Your message to user when the microphone is accessed for the first time</string>

```

现在，我们应该在我们的`android/app/src/main/AndroidManifest.xml`文件中插入以下代码行:

```
<uses-permission android:name="android.permission.CAMERA" />

<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />

<uses-permission android:name="android.permission.RECORD_AUDIO"/>

```

最后但同样重要的是，我们应该更改`android/app/build.gradle`文件:

```
android {
  ...
  defaultConfig {
    ...
    missingDimensionStrategy 'react-native-camera', 'general' // <--- insert this line
  }
}

```

之后，如果我们运行应用程序，一切正常，那么我们就可以开始编码了！

### 设计应用程序

让我们通过添加以下导入来更改我们的`App.js`文件:

```
import React, { useState } from 'react';
import { StyleSheet, Text, View, TouchableOpacity, SafeAreaView } from 'react-native';
import { RNCamera } from 'react-native-camera'

```

我们将导入并使用`RNCamera`组件与摄像机通信。如果你有兴趣，可以在这里阅读更多关于 RNCamera 的[。](https://react-native-camera.github.io/react-native-camera/docs/rncamera)

接下来，我们将修改我们的主屏幕。让我们删除之前生成的代码，以便添加一个带有`SafeAreaView`的`topBar`和一个带有`View`和`Text`的标题。

然后，我将添加如上所述的`RNCamera`以与相机通信，并添加一个`TouchableOpacity`按钮以允许用户扫描二维码:

```
<View style={styles.screen}>
  <SafeAreaView style={styles.saveArea}>
    <View style={styles.topBar}>
      <Text style={styles.topBarTitleText}>React Native Scanner</Text>
    </View>
  </SafeAreaView>

  <View style={styles.caption}>
    <Text style={styles.captionTitleText}>Welcome To React-Native-Camera Tutorial</Text>
  </View>

  <RNCamera style={styles.rnCamera} />

  <View style={styles.cameraControl}>
    <TouchableOpacity style={styles.btn}>
      <Text style={styles.btnText}>New QR Scan</Text>
    </TouchableOpacity>
  </View>
</View>

```

让我们更新`topBar`的样式，使其为绿色，标题文本为黑色:

```
const styles = StyleSheet.create({
  topBar: {
    height: 50,
    backgroundColor: '#62d1bc', // green
    alignItems: 'center',
    justifyContent: 'center',
  },
  topBarTitleText: {
    color: '#ffffff', // white
    fontSize: 20,
  },
  caption: {
    height: 120,
    justifyContent: 'center',
    alignItems: 'center',
  },
  captionTitleText: {
    color: '#121B0D', // black
    fontSize: 16,
    fontWeight: '600'
  },
});

```

我将使用`useState`来存储 React 原生相机的二维码。让我们用初始状态`null`将下面一行添加到我们的`App.js`中:

```
function App() {
  const [barcode, setBarcode] = useState(null);
  ...
}

```

我们会简单的通过调用`setBarcode`来存储二维码，用`barcode`来显示。

此外，我还添加了`RNCamera`这样的样式。这样就不会占用太多屏幕空间，我们可以看到应用程序的其余部分:

```
const styles = StyleSheet.create({
  ...
  rnCamera: {
    flex: 1,
    width: '94%',
    alignSelf: 'center',
  }
})

```

如果你想查看所有的样式选项，你可以在这里查看样式表。

现在，如果您运行该应用程序，我们的用户界面将如下图所示:

![Screenshot of React Native scanner app without QR scanning functionality](img/e3a2bbe5e21cbede3f19e0270f98a7cd.png)

### 扫描二维码

如果你尝试扫描一个二维码，你将无法阅读它。因此，当相机检测到二维码时，我将使用 [`onBarCodeRead`](https://react-native-camera.github.io/react-native-camera/docs/rncamera#onbarcoderead) 方法来检索条形码信息。

该函数返回几个属性，包括:

*   `data`，二维码的文字呈现
*   `rawData`，二维码中的编码数据
*   `uri`，应用缓存中保存图像的路径(仅限 iOS)
*   `type`，二维码的类型(QR，aztec，code93 等。)
*   `bounds`、图像尺寸和原点数据(x 和 y)

![Screenshot of React Native Scanner app scanning QR code](img/6e0a2c934b7f8720a607847128f59eb0.png)

让我们更新我们的`App.js`文件，以显示从二维码读取的信息:

```
function App() {
  const [barcode, setBarcode] = useState(null);

  return (
    <View style={styles.screen}>
      <SafeAreaView style={styles.saveArea}>
        <View style={styles.topBar}>
          <Text style={styles.topBarTitleText}>React Native Scanner</Text>
        </View>
      </SafeAreaView>

      <View style={styles.caption}>
        <Text style={styles.captionTitleText}>Welcome To React-Native-Camera Tutorial</Text>
      </View>

      {barcode ? (
        <View style={[styles.rnCamera, styles.rmCameraResult]}>
          <Text style={styles.rmCameraResultText}>{barcode.data}</Text>
          <Text style={styles.rmCameraResultText}>{barcode.type}</Text>
        </View>
      ) : (
        <RNCamera
          style={styles.rnCamera}
          onBarCodeRead={setBarcode}
        />
      )}

      <View style={styles.cameraControl}>
        <TouchableOpacity style={styles.btn} onPress={() => setBarcode(null)}>
          <Text style={styles.btnText}>New QR Scan</Text>
        </TouchableOpacity>
      </View>
    </View>
  );
};

```

上面，我们已经使用`onBarCodeRead`函数从 QR 码中提取了信息，并通过调用`setBarcode`方法将其保存在 React 状态中。

然后，我们显示了条形码数据和类型，如果条形码不是`null`。如果想再次扫描，可以按下`New QR Scan`按钮，状态数据将被设置为`null`。

就是这样！这是最后一个应用程序:

![Screenshot of final react native scanner app](img/daf9c086fc176d168801e97c66c4719f.png)

如果你想看到所有的代码，你可以点击这里查看 Github repo。

## 结论

React Native Camera 是一个令人难以置信的包，可以帮助开发人员将设备摄像头用于用 React Native 构建的 iOS 和 Android 应用程序。除了我们的示例 QR 码扫描仪，您还可以进行文本识别、人脸检测以及捕获视频和图像。

感谢阅读。我希望这篇文章对你有用。编码快乐！

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)