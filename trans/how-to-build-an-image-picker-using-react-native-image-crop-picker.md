# 如何使用 react-native-image-crop-picker-log rocket 博客构建图像拾取器

> 原文：<https://blog.logrocket.com/how-to-build-an-image-picker-using-react-native-image-crop-picker/>

在我使用 React Native 的经验中，我发现自己最常做的任务之一是构建从用户设备上传图像的功能。如果你以前从未做过，这可能会很有挑战性。

在本教程中，我们将演示如何使用 [React 原生图像裁剪器](https://github.com/ivpusic/react-native-image-crop-picker)让您的用户能够从他们的设备中选择图像，或者使用设备的摄像头捕捉实时照片并上传到您的应用程序。

为了展示`react-native-image-crop-picker`是如何工作的，我们将创建一个可重用的图像拾取器组件，它处理从媒体库选择图像或使用相机拍摄新图像的权限。

在本教程结束时，您应该像这样使用图像拾取器组件:

```
import * as React from 'react';
import {View} from 'react-native';
import {ImageOrVideo} from 'react-native-image-crop-picker';
import {Avatar} from './Avatar';

export const Profile = () => {
  const onAvatarChange = (image: ImageOrVideo) => {
    console.log(image);
    // upload image to server here 
  };
  return (
        <Avatar
          onChange={onAvatarChange}
          source={require('./avatar-placeholder.png')}
        />
  );
}; 
```

下面是最终结果的简单演示:

![Demo of Final Result](img/020fa6c634fc512aca9eb6d72c81e5c5.png)

## 选择正确的 React 本机映像拾取器

有两个流行的库可以用来实现图像拾取器组件:`[react-native-image-picker](https://github.com/react-native-image-picker/react-native-image-picker)`和`react-native-image-crop-picker`。React Native Image Picker 是另一个 React Native 模块，用于从设备库或相机中选择媒体。那么为什么要使用 React 原生图像裁剪器呢？

使用`react-native-image-crop-picker`的好处是，不像`react-native-image-picker`，它可以让你裁剪和压缩图像。这在构建 iOS 应用程序时尤为重要，因为上传大文件可能会带来性能问题。压缩和裁剪图像对网速低的用户也有帮助。

## 安装`react-native-image-crop-picker`

让我们使用 TypeScript 模板创建一个新的 React 本机项目:

```
npx react-native init ImagePickerDemo  --template react-native-template-typescript 
```

接下来，安装`react-native-image-crop-picker`:

```
yarn add react-native-image-crop-picker 
```

因为`react-native-image-crop-picker`带有一些本机依赖项，我们需要安装`pod`并重新构建应用程序:

```
cd ios && pod install && cd .. 
```

要使用`react-native-image-crop-picker`，您应该将以下配置添加到`info.plist`:

```
<key>NSPhotoLibraryUsageDescription</key>
<string>$(PRODUCT_NAME) would like to upload photos from your photo gallery</string>
<key>NSCameraUsageDescription</key>
<string>$(PRODUCT_NAME) requires to access camera for uploading photos to your profile or posts</string>
<key>NSMicrophoneUsageDescription</key>
<string>$(PRODUCT_NAME) requires to access Audio recording to record and uplod videos</string> 
```

`NSPhotoLibraryUsageDescription`配置描述了你为什么需要访问用户照片以及你为什么要访问相机。如果您只想从设备库中访问图像，则无需添加`NSMicrophoneUsageDescription`或`NSCameraUsageDescription`键。

现在，我们准备构建并打开应用程序:

```
yarn ios 
```

### Android 配置

`react-native-image-crop-picker`的安卓配置如下。

先加上`useSupportLibrary` ( `android/app/build.gradle`):

```
android {
    ...

    defaultConfig {
        ...
        vectorDrawables.useSupportLibrary = true
        ...
    }
    ...
} 
```

如果您想在项目中使用摄像机拾取器，将以下内容添加到`app/src/main/AndroidManifest.xml`:

*   *   `<uses-permission android:name="android.permission.CAMERA"/>`

如果您希望允许用户使用他们的前置摄像头，您还应该将以下内容添加到`app/src/main/ AndroidManifest.xml`:

*   *   `<uses-feature android:name="android.hardware.camera" android:required="false" />`
    *   `<uses-feature android:name="android.hardware.camera.front" android:required="false" />`

需要特别提到的是，你需要升级到 [Android SDK 26+](https://developer.android.com/studio/releases/platforms) 。如果你使用的是 [React Native 0.64](https://blog.logrocket.com/whats-new-in-react-native-0-64/) ，你是安全的。如果不是这样，请确保更新`android/app/build.gradle`:

```
android {
    compileSdkVersion 27
    buildToolsVersion "27.0.3"
    ...

    defaultConfig {
      ...
      targetSdkVersion 27
      ...
    }
    ...
} 
```

## 构建头像档案更新

在本节中，我们将构建一个简单的屏幕来更新用户头像。这个想法是创建一个图像选择器组件，允许用户上传一个新的头像。虚拟角色组件应该从`Image`组件扩展它的`prop`，我们将添加`onChange`来处理从用户设备上传新图像。

我们将用一个可按压的组件包装我们的图像，以便当用户按压他们的个人资料图片时打开照片库。打开图像库就像从`react-native-image-crop-picker`调用`openPicker`函数一样简单。

让我们添加一个裁剪功能，允许用户在上传之前裁剪选定的图像:

```
import React from 'react';
import {Image, ImageProps, StyleSheet, TouchableOpacity} from 'react-native';
import ImagePicker, {ImageOrVideo} from 'react-native-image-crop-picker';

interface AvatarProps extends ImageProps {
  onChange?: (image: ImageOrVideo) => void;
}

export const Avatar = (props: AvatarProps) => {
  const [uri, setUri] = React.useState(props.source?.uri || undefined);

  const pickPicture = () => {
    ImagePicker.openPicker({
      width: 300,
      height: 400,
      cropping: true,
    }).then(image => {
      setUri(image.path);
      props.onChange?.(image);
    });
  };
  return (
    <TouchableOpacity onPress={pickPicture}>
      <Image
          style={styles.avatar}
          {...props}
          source={uri ? {uri} : props.source}
        />
    </TouchableOpacity>
  );
};

const styles = StyleSheet.create({
  avatar: {
    paddingTop: 20,
    height: 100,
    width: 100,
    borderRadius: 100,
    padding: 20,
  },
}); 
```

既然我们的头像组件已经可以使用了，让我们创建一个个人资料屏幕并更新用户的个人资料图片:

```
import * as React from 'react';
import {StatusBar, StyleSheet, View} from 'react-native';
import {ImageOrVideo} from 'react-native-image-crop-picker';
import {Avatar} from './Avatar';
import {UserInfo} from './UserInfo';

export const Profile = () => {
  const onAvatarChange = (image: ImageOrVideo) => {
    console.log(image);
    // upload image to server here 
  };
  return (
    <View style={styles.scroll}>
      <StatusBar barStyle="dark-content" />
      <View style={styles.userRow}>
        <Avatar
          onChange={onAvatarChange}
          source={require('./avatar-placeholder.png')}
        />
        <UserInfo />
      </View>
      <View style={styles.content} />
    </View>
  );
};

const styles = StyleSheet.create({
  scroll: {
    backgroundColor: 'white',
    flex: 1,
  },
  userRow: {
    alignItems: 'center',
    padding: 15,
    marginTop: 70,
  },
  content: {
    flex: 1,
    backgroundColor: '#d8d8db',
  },
}); 
```

这里我们给头像添加了`onChange`道具。这将为我们提供所有的文件信息，我们可以很容易地将其上传到我们的服务器。结果如下:

![Onchange Prop Added to Avatar](img/aac12dca779521e3d8e8cb3da8ab583b.png)

## 使用设备相机拍照

比方说，我们想给用户从他们的相机拍照并上传的能力。我们还应该给用户从库中选择一张照片或使用他们的设备相机拍摄新照片的选项。

注意`openCamera`在 iOS 模拟器上不起作用。为了测试它，你应该在一个真实的设备上运行这个应用。

要使用相机，我们只需要调用`openCamera`函数，而不是`openPicker`:

```
import React from 'react';
import {
  Image,
  ImageProps,
  Pressable,
  SafeAreaView,
  StyleSheet,
  Text,
  TouchableOpacity,
} from 'react-native';
import ImagePicker, {ImageOrVideo} from 'react-native-image-crop-picker';
import Modal from 'react-native-modal';
import {CameraIcon, ImageIcon} from './icons';

interface AvatarProps extends ImageProps {
  onChange?: (file: ImageOrVideo) => void;
}

export const Avatar = (props: AvatarProps) => {
  const [uri, setUri] = React.useState(props.source?.uri || undefined);
  const [visible, setVisible] = React.useState(false);
  const close = () => setVisible(false);
  const open = () => setVisible(true);
  const chooseImage = () => {
    ImagePicker.openPicker({
      width: 300,
      height: 400,
      cropping: true,
    })
      .then(image => {
        setUri(image.path);
        props.onChange?.(image);
      })
      .finally(close);
  };

  const openCamera = () => {
    ImagePicker.openCamera({
      width: 300,
      height: 400,
      cropping: true,
    })
      .then(image => {
        setUri(image.path);
        props.onChange?.(image);
      })
      .finally(close);
  };

  return (
    <>
      <TouchableOpacity onPress={chooseImage}>
        <Image
          style={styles.avatar}
          {...props}
          source={uri ? {uri} : props.source}
        />
      </TouchableOpacity>
      <Modal
        isVisible={visible}
        onBackButtonPress={close}
        onBackdropPress={close}
        style={{justifyContent: 'flex-end', margin: 0}}>
        <SafeAreaView style={styles.options}>
          <Pressable style={styles.option} onPress={chooseImage}>
            <ImageIcon />
            <Text>Library </Text>
          </Pressable>
          <Pressable style={styles.option} onPress={openCamera}>
            <CameraIcon />
            <Text>Camera</Text>
          </Pressable>
        </SafeAreaView>
      </Modal>
    </>
  );
};

const styles = StyleSheet.create({
  avatar: {
    paddingTop: 20,
    height: 100,
    width: 100,
    borderRadius: 100,
    padding: 20,
  },

  options: {
    backgroundColor: 'white',
    flexDirection: 'row',
    borderTopRightRadius: 30,
    borderTopLeftRadius: 30,
  },
  option: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
}); 
```

这是我们完成的 React 原生应用的演示:

![Demo of Final Result](img/020fa6c634fc512aca9eb6d72c81e5c5.png)

## 结论

现在，每当您需要在 React 本机应用程序中构建图像上传功能时，您都可以复制粘贴这个图像拾取器组件。

如果你想玩这个项目，可以在这个 [Github repo](https://github.com/yjose/react-native-image-picker-example) 中找到完整的代码。

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)