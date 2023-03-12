# 在 React Native 中缓存图像:示例教程

> 原文：<https://blog.logrocket.com/caching-images-react-native-tutorial-with-examples/>

从社交媒体服务到拼车应用，再到博客平台，图像在数据表示中占据着相当重要的位置。它们在增强用户体验方面起着很大的作用，对于你的应用程序的用户友好性来说也是至关重要的。

从开发人员的角度来看，在 React Native 中加载远程映像并不是一个大问题。但是即使在`Component`中添加了最好的优化，不管是类还是功能组件，图像加载和重新渲染都会降低应用程序的速度，导致界面滞后。

在本教程中，我们将首先向您展示如何使用`react-native-fast-image`库在 React Native 中缓存图像。然后，我们将通过一步一步的说明和详细的示例演示如何从头开始构建自己的 React 本机映像缓存组件。

以下是我们将要介绍的内容:

要继续学习，您应该熟悉 React Native 的基础知识——例如，JSX、组件(类和函数)和样式。您可以简单地复制并粘贴本指南中的代码块，但是我建议您通读整个教程，以便更好地理解。

## React Native 中的图像缓存是什么？

缓存是解决从远程端点加载和重新渲染图像相关问题的好方法。图像缓存本质上是指将图像下载到应用程序缓存目录(或应用程序可访问的任何其他目录)中的本地存储中，并在下次加载图像时从本地存储中加载。

有几种方法可以在 React Native 中实现图像缓存。如果你正在构建一个基本的 React Native 应用程序，有一个很棒的组件可以自动处理所有的图像缓存，而无需编写任何额外的代码，名为 [React Native FastImage](https://github.com/DylanVann/react-native-fast-image) 。或者，如果您正在使用 Expo 或从事一个更复杂的项目，您可能会决定从头开始构建自己的图像缓存组件。

## 什么是`react-native-fast-image`？

[`react-native-fast-image`](https://github.com/DylanVann/react-native-fast-image) 是一个用于加载图像的 performant React 本地组件。FastImage 主动缓存所有加载的图像。您可以添加自己的请求验证头和预加载图像。`react-native-fast-image`甚至有 GIF 缓存支持。

要开始使用 React Native FastImage，首先导入`FastImage`组件:

```
import FastImage from 'react-native-fast-image';

```

下面是`FastImage`组件的基本实现:

```
<FastImage
  style={{ width: 200, height: 200 }}
  source={{uri: 'https://unsplash.it/400/400?image=1'}}
/>

```

这里有一个预览是什么样子的:

![react-native-fast-image Example](img/bec092db6490074f45d1f76c31dafa3c.png)

## 使用`FastImage`组件:一个实际例子

让我们看一个使用带有一些道具的`FastImage`组件的基本例子:

```
<FastImage
  style={{ width: 200, height: 200 }}
  source={{
      uri: '...image...url...',
      headers: { Authorization: 'someAuthToken' },
      priority: FastImage.priority.normal,
  }}
  resizeMode={FastImage.resizeMode.contain}
/>

```

正如您所看到的，这个例子几乎与基本的 React 原生图像组件相同，但是使用了类固醇。让我们更详细地分解代码。

*   `source`包含图像、图像头等等
*   `uri`代表您想要加载的图像的路径
*   `headers`表示您可能需要的头(上面例子中的 auth token)
*   `priority`表示图像的优先级——例如，如果您需要首先加载某个图像，您可以将优先级设置为`FastImage.priority.high`

## 对本机`cache`属性做出反应

是事情变得令人兴奋的地方。你可能熟悉`uri`、`header`以及`Image`组件的其他道具。对于`FastImage`来说也是一样，只是略有变化。`cache`是用来改变图像缓存和图像加载的行为。

您可以在`cache`中使用三个属性:

1.  `FastImage.cacheControl.immutable`是`FastImage`组件的默认属性。只有当`uri`改变时，图像才会缓存或更新
2.  `FastImage.cacheControl.web`使您能够配置`FastImage`组件像浏览器一样缓存图像，使用标题和正常的缓存过程
3.  `FastImage.cacheControl.cacheOnly`使您能够限制`FastImage`组件从已经缓存的图像中获取数据——即，不发出任何新的网络请求。

下面是一个带有`cache`属性的图像示例:

```
<FastImage
  style={{ width: 200, height: 200 }}
  source={{
    uri: 'https://unsplash.it/400/400?image=1',
    cache: FastImage.cacheControl.cacheOnly
  }}
/>

```

简单地说，如果您可以维护一个加载一次的图像的本地数据库，那么您可以使用这个`cache`属性通过从设备存储中获取缓存的图像来节省带宽成本。

## 如何从头开始构建图像缓存组件

FastImage 非常适合基本的 React 本地项目，但是如果您正在使用 Expo 或者有`react-native-fast-image`无法满足的需求，您可能想要编写自己的图像缓存组件。

在构建自己的图像缓存组件之前，理解缓存图像的基础知识是至关重要的。让我们回顾一下:缓存图像是指将它存储在设备的本地存储器中，以便下次无需任何网络请求就可以快速访问它。

为了缓存图像，我们需要网络 URI，或者该图像的 URL，以及一个`string`标识符，以便下次获取它。我们需要每个资源的唯一标识符，因为多个图像可能有相同的名称，这在区分本地缓存和具有冗余名称的图像时可能是一个问题。

对于本指南，我将假设您要么使用 expo 构建您的应用程序，要么在 bare React Native 中通过[单模](https://docs.expo.io/bare/installing-unimodules/)使用`expo-file-system`。

我们的组件应该接受三个基本的支持:

1.  `source`对于 URI 的网络形象
2.  `cacheKey`为图像的唯一标识符
3.  `style`对图像组件进行样式化:

    ```
    let image = {     uri: "https://post.medicalnewstoday.com/wp-content/uploads/sites/3/2020/02/322868_1100-800x825.jpg",     id: "MFdcdcdjvCDCcnh", //the unique id that you can store in your local db   };
    ```

    ```
    <CustomFastImage   source={{ uri: image.uri }}   cacheKey={image.id}   style={{ width: 200, height: 200 }} />
    ```

对于自定义图像缓存组件的逻辑，我们将导入`expo-file-system`:

```
import * as FileSystem from "expo-file-system";

```

首先，我们需要使用`cacheKey`(惟一 ID)为我们的远程映像创建一个新的本地路径，以检查它是否已经存在于本地缓存中，如果不存在，就下载它。

我们需要初始化将要收到的道具:

```
const CustomFastImage = (props) => {
  const {
    source: { uri },
    cacheKey,
    style,
  } = props;
...

```

以及从`uri`获取图像扩展的函数:

```
function getImgXtension(uri) {
  var basename = uri.split(/[\\/]/).pop();
  return /[.]/.exec(basename) ? /[^.]+$/.exec(basename) : undefined;
}

```

这个函数返回一个扩展数组。你可以只使用数组的第一项。

然后，我们将调用这个函数从组件的 [`useEffect`钩子](https://blog.logrocket.com/guide-to-react-useeffect-hook/)中获取扩展，并使用返回的扩展为图像创建本地缓存路径:

```
useEffect(() => {
    async function loadImg() {
      let imgXt = getImgXtension(uri);
      if (!imgXt || !imgXt.length) {
        Alert.alert(`Couldn't load Image!`);
        return;
      }
      const cacheFileUri = `${FileSystem.cacheDirectory}${cacheKey}.${imgXt[0]}`;
    }
    loadImg();
  }, []);

```

`FileSystem.cacheDirectory`是缓存目录的路径。你可以根据自己的喜好来改变这一点。

现在，我们需要使用如下函数检查该路径下的图像是否已经存在:

```
async function findImageInCache(uri) {
  try {
    let info = await FileSystem.getInfoAsync(uri);
    return { ...info, err: false };
  } catch (error) {
    return {
      exists: false,
      err: true,
      msg: error,
    };
  }
}

```

将此添加到`useEffect > loadImg()`:

```
useEffect(() => {
    async function loadImg() {
      let imgXt = getImgXtension(uri);
      if (!imgXt || !imgXt.length) {
        Alert.alert(`Couldn't load Image!`);
        return;
      }
      const cacheFileUri = `${FileSystem.cacheDirectory}${cacheKey}.${imgXt[0]}`;
      let imgXistsInCache = await findImageInCache(cacheFileUri);
    }
    loadImg();
  }, []);

```

现在我们需要一个函数来将图像缓存到本地存储，如果它还没有被缓存的话，并返回期望的输出:

```
async function cacheImage(uri, cacheUri, callback) {
  try {
    const downloadImage = FileSystem.createDownloadResumable(
      uri,
      cacheUri,
      {},
      callback
    );
    const downloaded = await downloadImage.downloadAsync();
    return {
      cached: true,
      err: false,
      path: downloaded.uri,
    };
  } catch (error) {
    return {
      cached: false,
      err: true,
      msg: error,
    };
  }
}

```

我们还需要一个带有`useState()`钩子的`const`来存储图像加载后的路径:

```
const [imgUri, setUri] = useState("");

```

为了获得更好的用户体验，您可以添加一个`ActivityIndicator`(或者根据您的喜好添加任何类似的加载指示器)，并根据`imgUri`状态的变化来实现它。

```
return (
    <>
      {imgUri ? (
        <Image source={{ uri: imgUri }} style={style} />
      ) : (
        <View
          style={{ ...style, alignItems: "center", justifyContent: "center" }}
        >
          <ActivityIndicator size={33} />
        </View>
      )}
  </>
);

```

在`useEffect`钩子中，当图像被缓存或者已经在本地存储中可用时，我们需要更新`imgUri`:

```
  useEffect(() => {
    async function loadImg() {
      let imgXt = getImgXtension(uri);
      if (!imgXt || !imgXt.length) {
        Alert.alert(`Couldn't load Image!`);
        return;
      }
      const cacheFileUri = `${FileSystem.cacheDirectory}${cacheKey}.${imgXt[0]}`;
      let imgXistsInCache = await findImageInCache(cacheFileUri);
      if (imgXistsInCache.exists) {
        console.log("cached!");
        setUri(cacheFileUri);
      } else {
        let cached = await cacheImage(uri, cacheFileUri, () => {});
        if (cached.cached) {
          console.log("cached NEw!");
          setUri(cached.path);
        } else {
          Alert.alert(`Couldn't load Image!`);
        }
      }
    }
    loadImg();
  }, []);

```

下面是我们构建的`CustomFastImage`组件的完整代码:

```
import React, { useEffect, useRef, useState } from "react";
import { Alert, Image, View, ActivityIndicator } from "react-native";
import * as FileSystem from "expo-file-system";
function getImgXtension(uri) {
  var basename = uri.split(/[\\/]/).pop();
  return /[.]/.exec(basename) ? /[^.]+$/.exec(basename) : undefined;
}
async function findImageInCache(uri) {
  try {
    let info = await FileSystem.getInfoAsync(uri);
    return { ...info, err: false };
  } catch (error) {
    return {
      exists: false,
      err: true,
      msg: error,
    };
  }
}
async function cacheImage(uri, cacheUri, callback) {
  try {
    const downloadImage = FileSystem.createDownloadResumable(
      uri,
      cacheUri,
      {},
      callback
    );
    const downloaded = await downloadImage.downloadAsync();
    return {
      cached: true,
      err: false,
      path: downloaded.uri,
    };
  } catch (error) {
    return {
      cached: false,
      err: true,
      msg: error,
    };
  }
}
const CustomFastImage = (props) => {
  const {
    source: { uri },
    cacheKey,
    style,
  } = props;
  const isMounted = useRef(true);
  const [imgUri, setUri] = useState("");
  useEffect(() => {
    async function loadImg() {
      let imgXt = getImgXtension(uri);
      if (!imgXt || !imgXt.length) {
        Alert.alert(`Couldn't load Image!`);
        return;
      }
      const cacheFileUri = `${FileSystem.cacheDirectory}${cacheKey}.${imgXt[0]}`;
      let imgXistsInCache = await findImageInCache(cacheFileUri);
      if (imgXistsInCache.exists) {
        console.log("cached!");
        setUri(cacheFileUri);
      } else {
        let cached = await cacheImage(uri, cacheFileUri, () => {});
        if (cached.cached) {
          console.log("cached NEw!");
          setUri(cached.path);
        } else {
          Alert.alert(`Couldn't load Image!`);
        }
      }
    }
    loadImg();
    return () => (isMounted.current = false);
  }, []);
  return (
    <>
      {imgUri ? (
        <Image source={{ uri: imgUri }} style={style} />
      ) : (
        <View
          style={{ ...style, alignItems: "center", justifyContent: "center" }}
        >
          <ActivityIndicator size={33} />
        </View>
      )}
    </>
  );
};
export default CustomFastImage;

```

## React Native 中图像缓存的其他方法

我们已经介绍了在 React Native 中缓存图像的两种方法，但是，还有其他缓存方法，我的意思是编程，您可以构建自己的做事方法，但我们将讨论另外两种方法，它们允许我们在 React Native 应用程序中缓存图像。

### React Native 的内置图像组件

大多数新开发人员错过了 React Native 默认提供的功能。其中一个功能是使用`Image`组件的`prefetch()`方法缓存图像。`Prefetch`，顾名思义，从远程服务器获取图像，并将其存储在本地设备的存储器中，以便更快地加载。`prefetch`的基本用法是:

```
await Image.prefetch(URL_OF_AN_IMAGE)

```

为了使用这种方法，您可能需要添加一个占位符，构建一个 lambda 条件，或者使用这两者构建一个自定义组件，以使用户体验更加流畅。关键是在渲染器中显示图像之前使用 async/await 加载图像。

您可以[在这里](https://blog.logrocket.com/displaying-images-with-the-react-native-image-component/)了解更多关于图像组件的信息。

### `react-native-cached-image`

这是 React Native 中缓存图像的另一种方式。它基本上使用了一个提供者，即`ImageCacheProvider`，我们向其中添加了一个需要由应用程序缓存的图像 URL 数组。`CachedImage`组件用于显示使用`ImageCacheProvider`缓存的图像。我们可以看到下面的实现:

```
const imgs = [url1, url2, url3, ...];
const ImgExample = () => {
return (
  <ImageCacheProvider
    urlsToPreload={imgs}
    onPreloadComplete={() => {
      console.log('You can use this callback to show the CachedImage component.');
    })
    >
      <CachedImage source={{uri: imgs[0]}}/>
      <CachedImage source={{uri: imgs[1]}}/>
      <CachedImage source={{uri: imgs[2]}}/>
    </ImageCacheProvider>
);
}

```

这个模块还包含`ImageCacheManager`，它可以使用各种可用的方法从缓存中删除图像。你可以在这里查看整个模块。

*注意，此组件的最后一次更新是在 2017 年发布的，这往往会使一个模块不可靠。慎用。*

## 结论

在本教程中，我们介绍了关于 React Native 中图像缓存的所有知识。我们讨论了如何使用`react-native-fast-image`在 React Native 中缓存图像，以及如何从头开始构建自己的图像缓存组件。

在接下来的步骤中，您可能会考虑向组件添加动画、加载指示器和其他附加功能。您还可以使用[文件系统 API](https://docs.expo.io/versions/latest/sdk/filesystem/#downloading-files) 添加进度指示器或更好的回调函数。

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)