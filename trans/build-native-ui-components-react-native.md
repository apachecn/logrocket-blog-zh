# 在 React Native - LogRocket 博客中构建原生 UI 组件

> 原文：<https://blog.logrocket.com/build-native-ui-components-react-native/>

在本文中，我们将了解什么是原生 UI 组件，以及如何在 React Native 中构建和使用它们。

如果您想将现有的原生 Java 或 Objective-C 组件转换为 React 原生组件，同时重用您的原生代码逻辑，那么原生 UI 组件是一个不错的选择。

准备好了吗？让我们开始吧。

## 什么是 React Native？

React Native 是由脸书创建和维护的 JavaScript 框架，用于开发本地呈现的移动应用程序。它基于 React，这是一个 JavaScript 库，用于为 web 浏览器构建用户界面。

[React Native](https://blog.logrocket.com/tag/react-native/) 不仅仅局限于手机 app。我们还可以借助像 [react-native-windows](https://microsoft.github.io/react-native-windows/) 这样的第三方库，用 React Native 开发 macOS 和 Windows 桌面应用。

## React Native 中的原生 UI 组件是什么？

在幕后，React Native 使用 iOS 中的本机组件，如`UIView`和`UIImageView`，并将它们导出到 JavaScript 层。我们在日常开发过程中使用这些组件。

例如，当我们在 JSX 使用`View`组件时，我们在 JavaScript 代码中使用来自 iOS 的`UIView`。同样，对于`Image`组件，我们使用 iOS 中的`UIImage`组件，这样我们在 React Native 中使用的所有组件都有一个本机声明。

React Native 已经包装了所有主要的和广泛使用的 UI 组件，比如`[ScrollView](https://blog.logrocket.com/common-bugs-react-native-scrollview/)`、`Button`和`Switch`。

但是，如果组件没有被包装，或者，如果我们想要重用我们为本地应用程序构建的自定义本地组件，该怎么办呢？我们可以包装这些组件来创建我们自己的 UI 组件。

## 在 React Native 中设置项目

在本文中，我们将创建一个名为`CameraView`的原生 UI 组件，它将来自设备摄像头的实时摄像头馈送呈现到 UI。它还会从相机中捕捉图像，并将它们保存到用户的设备中。你可以在这里看到[全回购](https://github.com/hrupesh/RN-Native-UI-Components)。

我们将创建一个类似于下面给出的用户界面:

![iOS UI](img/a061a8f617ed88ea2a0e7f9533b429fa.png)

iOS UI

![Android UI](img/e92a8ada6d924d67cf916c577f9e8fef.png)

Android UI

> 如果你只是来看代码的，那么你可以在这里查看代码。

首先，我们需要创建一个裸露的 React 原生项目。打开您的终端，将当前工作目录更改为您的首选文件夹。在那里，键入以下命令创建一个新的 React 本地项目:

```
react-native init <your-project-name>

```

或者，如果您想要使用 React Native 的特定版本创建项目，您可以通过在命令中添加版本标志来实现:

```
react-native init <your-project-name> --version 0.63.0

```

现在，进入我们刚刚创建的新项目的目录，以便我们可以为 iOS 应用程序安装 pods:

```
cd ios
pod install

```

现在，让我们运行应用程序！

我们将通过 [XCode](https://developer.apple.com/xcode/) 和 [Android studio](https://developer.android.com/studio) 运行我们的应用程序。按照以下步骤，在 finder 中进入项目的 iOS 目录:

![Reveal Finder](img/ed1c0e53ea4d024e25bf08db47839b24.png)

在 iOS 目录中，您会看到一个`<your-project-name>.xc`工作区文件。打开它。

![Workspace File](img/3dd4948ab69db2de2f517c51049bd6da.png)

如果您已将 iOS 设备正确连接到系统，您应该会在 XCode 窗口中看到您的设备名称。要在您的设备上运行应用程序，请选择您的设备并单击**播放**按钮来构建并运行应用程序。

构建成功后，您将在应用程序中看到一些样板用户界面。现在，我们需要在 Android 设备上运行应用程序。

按照以下步骤在 Android studio 中打开您的 Android 项目:

*   打开 Android Studio
*   点击**进入文件**，然后**打开**
    ![Open](img/8c729ce50f3aaa2b4c4167a5beed0e70.png)
*   在项目的 Android 文件夹中，点击**打开**
    ![Android Folder](img/a45aefafd6863e36bc5c99ee3db662fc.png)
*   在 Android studio 窗口顶部选择你的设备，点击**播放**开始构建
    ![Play to Start](img/6f9f780d82634d90df0e84a18e6ea3cc.png)

一旦应用程序运行，您将看到一个类似于 iOS 中的 UI，这是 React 原生项目的默认样板代码。

我们现在已经成功地在 iOS 和 Android 上运行了我们的应用程序。

## 为 Android 和 iOS 创建通用的原生用户界面

要为 Android 和 iOS 创建一个通用的原生 UI 组件，我们需要从 JavaScript 修改两个文件:

首先，删除`App.js`中的样板 JS 和 JSX 代码。

```
import React from 'react';
import { View, StyleSheet } from 'react-native';

const App = () => {
  return (
    <View style={styles.container} />
  );
};

const styles = StyleSheet.create({
  flex: 1,
  backgroundColor: 'white'
});

export default App;

```

目前，这是我们唯一需要修改的代码。让我们从 Java 代码开始编写，为 Android 创建`CameraView`组件。

## 创建 Android 原生 UI 组件

根据 React Native 的官方[文档](https://reactnative.dev/docs/native-components-android)，创建一个 Android UI 组件有五个步骤:

*   创建`ViewManager`子类
*   实现`createViewInstance`方法
*   使用`@ReactProp`(或`@ReactPropGroup`)注释公开视图属性设置器。**注意:**我们不会使用这些
*   在应用程序包的`createViewManagers`中注册管理器
*   实现 JavaScript 模块

看起来很简单，对吧？

现在，切换到 Android Studio 窗口，在这里我们将创建一个扩展了`ViewManger`子类的 Java 类。在我们的例子中，我们将使用带有类型`TextureView`的`SimpleViewManager`类，我们将使用它来呈现我们的摄像机馈送。

在子菜单中点击**文件**、**新建、**然后 **Java 类**:

![Java Class](img/7f1b5a252d19e31f1314036d583acb26.png)

然后会提示您填写新 Java 类的类名。

![Class Name](img/7f5563d05de2ba710ed6236da1c74b38.png)

在文件名内键入`RNCSTMCameraManager`，按**键进入**。现在我们有了一个新的 Java 类，我们需要用`SimpleViewManager`类来扩展它。

我们将从它创建一个视图，在 React Native 中使用它，我们需要在`getName()`方法中给它一个名称，这样我们就可以从 JavaScript 访问这个视图。

下面是您的`RNCSTMCameraManager.java`文件的样子:

```
/** All the imports will be here */

public class RNCSTMCameraManager extends SimpleViewManager<TextureView> {

    public static final String REACT_CLASS = "RNCSTMCamera";
    public static final String ComponentTag = "RNCSTMCamera";
    ReactApplicationContext mCallerContext;
    TextureView textureView;

    public RNCSTMCameraManager(ReactApplicationContext reactContext) {
        mCallerContext = reactContext;
        textureView = new TextureView(reactContext);
    }

    @Override
    public String getName() {
        return REACT_CLASS;
    }
}

```

我们添加了四个全局变量:

1.  `REACT_CLASS`:在 JS 代码中我们的组件将被访问的名字。在这种情况下，我们将其命名为`RNCSTMCamera`
2.  `ComponentTag`:一个标签名，用来打印在日志中，以便于跟踪和调试我们的代码
3.  `mCallerContext`:在 [Android](https://developer.android.com/reference/android/content/Context) 中，上下文被用来访问各种底层原生特性。为了访问这些特性，我们需要一个名为`ReactApplicationContext`的上下文
4.  `textureView`:我们将返回到 JavaScript 的视图，这是将显示我们的实时摄像机馈送的视图

我们现在需要实现`createViewInstance`方法，它负责返回一个我们将在 UI 上显示的`View`。

```
@Override
public TextureView createViewInstance(ThemedReactContext context) {
    return textureView;
}

```

我们的原生 UI 组件将呈现一个`TextureView`，但是我们没有向它添加任何属性或样式，所以它将显示为一个空白的白色屏幕。

## 在`TextureView`中预览直播

现在，我们需要访问设备摄像头并在`TextureView`中返回实时提要，以及添加图像捕捉功能。但是在继续之前，我们需要对我们的`AndroidManifest.xml`文件做一些修改。我们需要权限来:

*   打开摄像机
*   从设备存储器写入和读取
*   录制视频设备音频
*   在没有外部存储卡的设备上写入外部存储器，我们需要访问它们的`LegacyExternalStorage`文件

让我们将这些权限添加到`AndroidManifest.xml`的`manifest`标签中。

```
<uses-permission Android:name="Android.permission.CAMERA" />
<uses-feature Android:name="Android.hardware.camera2.full" />
<uses-permission Android:name="Android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission Android:name="Android.permission.WRITE_EXTERNAL_STORAGE" />
<uses-permission Android:name="Android.permission.RECORD_AUDIO" />

```

然后将该属性添加到`application`标签中:

```
Android:requestLegacyExternalStorage="true"

```

我们的`AndroidManifest.xml`文件现在应该是这样的。

![Androidmanifest File](img/381ca6df519315c4211745df99b515bb.png)

在我们实现了这些更改之后，我们需要编写逻辑来访问设备摄像头并在`TextureView`中返回它的 feed，以便能够捕获图像并保存它。

该文件将如下所示:

```
/** All the imports will go here */

public class RNCSTMCameraManager extends SimpleViewManager<TextureView> {

    public static final String REACT_CLASS = "RNCSTMCamera";
    public static final String ComponentTag = "RNCSTMCamera";
    public final int CAPTURE_COMMAND = 1111;
    ReactApplicationContext mCallerContext;
    TextureView textureView;

    public String cameraId;
    CameraDevice cameraDevice;
    CameraCaptureSession cameraCaptureSession;
    CaptureRequest captureRequest;
    CaptureRequest.Builder captureRequestBuilder;

    private Size imageDimensions;
    private ImageReader imageReader;
    private File file;
    Handler mBackgroundHandler;
    HandlerThread mBackgroundThread;

    TextureView.SurfaceTextureListener textureListener = new TextureView.SurfaceTextureListener() {
        @Override
        public void onSurfaceTextureAvailable(@NonNull SurfaceTexture surface, int width, int height) {
            try {
                openCamera();
            } catch (CameraAccessException e) {
                Log.d(ComponentTag, "Error in onSurfaceTextureAvailable:"+e);
            }
        }

        @Override
        public void onSurfaceTextureSizeChanged(@NonNull SurfaceTexture surface, int width, int height) {

        }

        @Override
        public boolean onSurfaceTextureDestroyed(@NonNull SurfaceTexture surface) {
            return false;
        }

        @Override
        public void onSurfaceTextureUpdated(@NonNull SurfaceTexture surface) {

        }
    };

    public RNCSTMCameraManager(ReactApplicationContext reactContext) {
        mCallerContext = reactContext;
        textureView = new TextureView(reactContext);
        if(checkCameraHardware(reactContext))  getCameraPermissions(reactContext);
    }

    @Override
    public String getName() {
        return REACT_CLASS;
    }

    /** Check if this device has a camera */
    private boolean checkCameraHardware(Context context) {
        if (context.getPackageManager().hasSystemFeature(PackageManager.FEATURE_CAMERA)){
            // this device has a camera
            return true;
        } else {
            // no camera on this device
            return false;
        }
    }

    private void getCameraPermissions(Context context) {
        if (ContextCompat.checkSelfPermission(context, Manifest.permission.CAMERA)
                == PackageManager.PERMISSION_DENIED){
            ActivityCompat.requestPermissions(mCallerContext.getCurrentActivity(), new String[] {Manifest.permission.CAMERA}, 100);
        }
    }

    private final CameraDevice.StateCallback stateCallback = new CameraDevice.StateCallback() {
        @Override
        public void onOpened(@NonNull CameraDevice camera) {
            cameraDevice = camera;
            try {
                createCameraPreview();
            } catch (CameraAccessException e) {
                Log.d(ComponentTag, "Error in onOpened:"+e);
            }
        }

        @Override
        public void onDisconnected(@NonNull CameraDevice camera) {
            cameraDevice.close();
        }

        @Override
        public void onError(@NonNull CameraDevice camera, int error) {
            cameraDevice.close();
            cameraDevice = null;
        }
    };

    private void createCameraPreview() throws CameraAccessException {
        SurfaceTexture surfaceTexture = textureView.getSurfaceTexture();
        surfaceTexture.setDefaultBufferSize(imageDimensions.getWidth(),imageDimensions.getHeight());
        Surface surface = new Surface(surfaceTexture);
        captureRequestBuilder = cameraDevice.createCaptureRequest(CameraDevice.TEMPLATE_PREVIEW);
        captureRequestBuilder.addTarget(surface);

        cameraDevice.createCaptureSession(Arrays.asList(surface), new CameraCaptureSession.StateCallback() {
            @Override
            public void onConfigured(@NonNull CameraCaptureSession session) {
                if(cameraDevice == null) return;
                cameraCaptureSession = session;
                try {
                    updatePreview();
                } catch (CameraAccessException e) {
                    e.printStackTrace();
                }
            }

            @Override
            public void onConfigureFailed(@NonNull CameraCaptureSession session) {
                Log.d(ComponentTag, "onConfigure failed");
            }
        }, null);
    }

    private void updatePreview() throws CameraAccessException {
        if(cameraDevice == null) return;
        captureRequestBuilder.set(CaptureRequest.CONTROL_MODE, CameraMetadata.CONTROL_MODE_AUTO);
        cameraCaptureSession.setRepeatingRequest(captureRequestBuilder.build(),null, mBackgroundHandler);
    }

    private  void openCamera() throws CameraAccessException {
        Log.d(ComponentTag, "Open Camera was called from onSurface Available");
        CameraManager cameraManager = (CameraManager) mCallerContext.getSystemService(Context.CAMERA_SERVICE);
        cameraId = cameraManager.getCameraIdList()[1];
        CameraCharacteristics cameraCharacteristics = cameraManager.getCameraCharacteristics(cameraId);
        StreamConfigurationMap map = cameraCharacteristics.get(CameraCharacteristics.SCALER_STREAM_CONFIGURATION_MAP);
        imageDimensions = map.getOutputSizes(SurfaceTexture.class)[0];

        if(ActivityCompat.checkSelfPermission(mCallerContext, Manifest.permission.CAMERA) != PackageManager.PERMISSION_GRANTED){
            ActivityCompat.requestPermissions(mCallerContext.getCurrentActivity(), new String[] { Manifest.permission.CAMERA }, 101);
        }
        if(ActivityCompat.checkSelfPermission(mCallerContext, Manifest.permission.WRITE_EXTERNAL_STORAGE) != PackageManager.PERMISSION_GRANTED){
            ActivityCompat.requestPermissions(mCallerContext.getCurrentActivity(), new String[] { Manifest.permission.WRITE_EXTERNAL_STORAGE }, 101);
        }

        cameraManager.openCamera(cameraId, stateCallback,null);
    }

    @Override
    public TextureView createViewInstance(ThemedReactContext context) {
        textureView.setSurfaceTextureListener(textureListener);
        return textureView;
    }

    private void takePicture() throws CameraAccessException {
        Log.d(ComponentTag, "takePicture is initiated!");
        if(cameraDevice == null) return;
        CameraManager cameraManager = (CameraManager) mCallerContext.getSystemService(Context.CAMERA_SERVICE);
        CameraCharacteristics cameraCharacteristics = cameraManager.getCameraCharacteristics(cameraDevice.getId());
        Size[] jpegSizes = null;
        jpegSizes = cameraCharacteristics.get(CameraCharacteristics.SCALER_STREAM_CONFIGURATION_MAP).getOutputSizes(ImageFormat.JPEG);
        int width = 1080;
        int height = 1440;
        if(jpegSizes != null & jpegSizes.length > 0){
            width = jpegSizes[0].getWidth();
            height = jpegSizes[0].getHeight();
        }

        ImageReader imageReader = ImageReader.newInstance(width, height, ImageFormat.JPEG, 1);
        List<Surface> outputSurfaces = new ArrayList<>(2);
        outputSurfaces.add(imageReader.getSurface());
        outputSurfaces.add(new Surface(textureView.getSurfaceTexture()));
        final CaptureRequest.Builder captureRequestBuilder = cameraDevice.createCaptureRequest(CameraDevice.TEMPLATE_STILL_CAPTURE);
        captureRequestBuilder.addTarget(imageReader.getSurface());
        captureRequestBuilder.set(CaptureRequest.CONTROL_MODE, CameraMetadata.CONTROL_MODE_AUTO);

        Long timeStampInLong = System.currentTimeMillis();
        String timeStamp = timeStampInLong.toString();

        file = new File(Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_DCIM)+"/"+timeStamp+".jpg");
        ImageReader.OnImageAvailableListener readerListener = new ImageReader.OnImageAvailableListener() {
            @Override
            public void onImageAvailable(ImageReader reader) {
                Image image = null;
                image = reader.acquireLatestImage();
                ByteBuffer byteBuffer = image.getPlanes()[0].getBuffer();
                byte[] bytes = new byte[byteBuffer.capacity()];
                byteBuffer.get(bytes);
                try {
                    saveImageBytes(bytes);
                } catch (IOException e) {
                    e.printStackTrace();
                }finally {
                    if(image != null) image.close();
                }
            }
        };
        imageReader.setOnImageAvailableListener(readerListner, mBackgroundHandler);

        final  CameraCaptureSession.CaptureCallback captureCallback = new CameraCaptureSession.CaptureCallback() {
            @Override
            public void onCaptureCompleted(@NonNull CameraCaptureSession session, @NonNull CaptureRequest request, @NonNull TotalCaptureResult result) {
                super.onCaptureCompleted(session, request, result);
                Log.d(ComponentTag, "Image is saved!");
                new AlertDialog.Builder(mCallerContext.getCurrentActivity())
                        .setTitle("Photo Saved!")
                        .setMessage("The photo has been successfully saved to your Photos Gallery.")
                        .setPositiveButton("👍", null)
                        .setIcon(Android.R.drawable.ic_menu_gallery)
                        .show();
                try {
                    createCameraPreview();
                } catch (CameraAccessException e) {
                    e.printStackTrace();
                }
            }
        };

        cameraDevice.createCaptureSession(outputSurfaces, new CameraCaptureSession.StateCallback() {
            @Override
            public void onConfigured(@NonNull CameraCaptureSession session) {
                try {
                    session.capture(captureRequestBuilder.build(), captureCallback, mBackgroundHandler);
                } catch (CameraAccessException e) {
                    e.printStackTrace();
                }
            }

            @Override
            public void onConfigureFailed(@NonNull CameraCaptureSession session) {

            }
        }, mBackgroundHandler);
    }

    private void saveImageBytes(byte[] bytes) throws IOException {
        OutputStream outputStream = null;
        outputStream = new FileOutputStream(file);
        outputStream.write(bytes);
        outputStream.close();
    }

    /**
     * Map the "captureImage" command to an integer
     */
    @Nullable
    @Override
    public Map<String, Integer> getCommandsMap() {
        return MapBuilder.of("captureImage", CAPTURE_COMMAND);
    }

    /**
     * Handle "captureImage" command called from JS
     */
    @Override
    public void receiveCommand(@NonNull TextureView root, String commandId, @Nullable ReadableArray args) {
        super.receiveCommand(root, commandId, args);
        int reactNativeViewId = args.getInt(0);
        int commandIdInt = Integer.parseInt(commandId);

        switch (commandIdInt) {
            case CAPTURE_COMMAND:
                try {
                    takePicture();
                } catch (CameraAccessException e) {
                    e.printStackTrace();
                }
                break;
            default: {}
        }
    }
}

```

你可以在这里下载完整的源代码。

注意，我们添加了样板代码来访问相机，还包含了`getCommandsMap()`和`receiveCommand()`函数。这些函数用于跟踪我们是否在本地 UI 组件中接收到任何命令。

当它们收到某个命令时，它们执行特定的逻辑。可以把它想成类似于 Redux，当我们接收到一个`TYPE`然后分派一个`ACTION`来执行特定的逻辑。

在这里，当我们接收到`CAPTURE_COMMAND`时，我们调用`takePicture()`方法来捕获图像并将其保存到用户的照片库中。

接下来让我们注册我们的`viewManager`。首先，创建一个实现`ReactPackage`的包文件，并通过它的`createViewManagers`方法返回我们的`RNCSTMCameraManager`的一个实例。

现在，使用`MainApplication.java`的`getPackages()`方法将其导出为 JavaScript 代码。

使用与创建`RNCSTMCameraManager.java`文件相同的过程创建一个`RNCSTMCameraManagerPackage.java`文件。创建后，添加以下代码:

```
/** Imports will be here */

public class RNCSTMCameraManagerPackage implements ReactPackage {

    @NonNull
    @Override
    public List<NativeModule> createNativeModules(@NonNull ReactApplicationContext reactContext) {
        List<NativeModule> nativeModules = new ArrayList<>();
        return nativeModules;
    }

    @Override
    public List<ViewManager> createViewManagers(
            ReactApplicationContext reactContext) {
        return Arrays.<ViewManager>asList(
                new RNCSTMCameraManager(reactContext)
        );
    }
}

```

现在，将包添加到`MainApplication.java`的`getPackages()`方法中:

```
@Override
protected List<ReactPackage> getPackages() {
  @SuppressWarnings("UnnecessaryLocalVariable")
  List<ReactPackage> packages = new PackageList(this).getPackages();
  // Packages that cannot be autolinked yet can be added manually here, for example:
   packages.add(new RNCSTMCameraManagerPackage()); // Add this line to add our package
  return packages;
}

```

🎉就是这样！我们已经成功地创建了一个 Android 原生 UI 组件。

让我们在 JavaScript 代码中访问它。转到 VSCode，打开`App.js`，添加以下代码:

```
import React, { useRef } from 'react';
import {
  findNodeHandle,
  Platform,
  Pressable,
  StyleSheet,
  UIManager,
  View
} from 'react-native';
import { CameraView } from './CameraView';

const App = () => {
  const componentRef = useRef(null);
  const dispatchCaptureCommand = () => {
        UIManager?.dispatchViewManagerCommand(
          findNodeHandle(componentRef?.current),
          UIManager?.RNCSTMCamera?.Commands?.captureImage?.toString(),
          [findNodeHandle(componentRef?.current)],
        )
  };
  return (
    <View style={styles.container}>
      <CameraView ref={componentRef} style={styles.cameraView} />
      <View style={styles.captureBtnContainer}>
        <Pressable style={styles.captureBtn} onPress={dispatchCaptureCommand} />
      </View>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: 'white',
  },
  cameraView: {
    flex: 1,
  },
  captureBtnContainer: {
    backgroundColor: '#0006',
    position: 'absolute',
    bottom: 60,
    alignSelf: 'center',
    alignItems: 'center',
    justifyContent: 'center',
    height: 100,
    width: 100,
    borderRadius: 60,
  },
  captureBtn: {
    height: 75,
    width: 75,
    borderRadius: 37.5,
    backgroundColor: '#fff',
  },
});

export default App;

```

您可以看到我们使用了一个从`CameraView`导入的`CameraView`组件。

在同一个目录中创建一个新的 JS 文件，将其命名为`CameraView.js`，然后粘贴以下内容:

```
import {requireNativeComponent} from 'react-native';

const CameraView = requireNativeComponent('RNCSTMCamera');

export {CameraView};

```

我们正在使用 React Native 的`requireNativeComponent`访问我们的原生 Android UI 组件，并将其导出为`CameraView`。

对于 Android，我们的 JavaScript 端实现也已经完成。现在，您可以通过使用 Android studio 在您的 Android 设备上运行来测试该应用程序。

## 创建 iOS 原生 UI 组件

根据官方[文档](https://reactnative.dev/docs/native-components-ios)，我们需要完成这些步骤来创建一个 iOS 原生 UI 组件:

*   子类`RCTViewManager`为组件创建管理器
*   添加`RCT_EXPORT_MODULE()`标记宏
*   实现`-(UIView *)view`方法

类似于 Android，我们也需要为我们的组件创建一个管理器，它将扩展`RCTViewManager`类。我们将通过`RCT_EXPORT_MODULE()`标记宏将其导出。`-(UIView *)view`是返回在用户界面中显示什么的方法。

我们将在 iOS 中创建两种类型的文件，用于将本地 UI 组件导出到 JS 代码:

1.  一个管理器类，它将导出我们的视图，并将包含与处理来自 JS 端的事件或命令相关的逻辑
2.  将发送给 manager 类进行呈现的视图。该文件将包含与在`UIView`上显示实时摄像机画面相关的所有逻辑

对于这两个文件，我们将有两个文件:一个`.h`(头)文件和一个`.m`(实现)文件。

切换到 XCode 窗口。点击标题栏中的**文件**，然后点击子菜单中的**新建**，然后点击**文件**。

![File Submenu](img/a5ccce3992c8e5b8a2b13ca474860e8b.png)

将源类型设置为**头文件**，在**选择模板**对话框中点击**下一步**。

![Choose Template](img/87c6d8e105ea49f8157f586ff836cf1c.png)

将文件另存为`RNCSTMCameraManager`，点击**创建**。

![Create](img/8d5b64e2fff1939155cc341f0a0ff065.png)

将以下代码添加到`RNCSTMCameraManager.h`。

```
#import "AVFoundation/AVFoundation.h"
#import <React/RCTViewManager.h>

@interface RNCSTMCameraManager : RCTViewManager

@end

```

在这个文件中，我们刚刚声明了类`RNCSTMCameraManager`，它扩展了`RCTViewManager`。它的实现将在`.m`文件中。

要创建`RNCSTMCameraManager.m`，遵循与创建头文件相同的步骤，但是选择源代码类型为 **Objective-C** 。

![Objective-c](img/abf7cf66fce5a7ddcb503f1553408a3c.png)

另存为`RNCSTMCameraManager`，点击**下一步，**然后添加 app 目标，点击**创建**。

添加以下代码:

```
#import "RNCSTMCameraManager.h"
#import "RNCTSTMCamera.h"
#import "React/RCTUIManager.h"
#import "React/RCTLog.h"

@implementation RNCSTMCameraManager

RCT_EXPORT_MODULE(RNCSTMCamera)

- (UIView *)view
{
  return [[RNCSTMCamera alloc] initWithFrame:CGRectMake(UIScreen.mainScreen.bounds.origin.x,
    UIScreen.mainScreen.bounds.origin.y,
    UIScreen.mainScreen.bounds.size.width,
    UIScreen.mainScreen.bounds.size.height)];
}

+ (BOOL)requiresMainQueueSetup {
    return YES;
}

@end

```

我们告诉 JavaScript，我们可以在`RCT_EXPORT_MODULE`标记宏中使用`RNCSTMCamera`来访问这个类，并在`view`方法中返回一个`RNCSTMCamera`类的实例，该方法返回一个`UIView`。

我们还添加了一个`requiresMainQueueSetup`方法，它将在主线程上运行我们的组件。

您还会注意到，我们正在返回一个还没有创建的`RNCSTMCamera`类，所以让我们这样做吧。为`RNCSTMCamera`类的声明创建一个名为`RNCSTMCamera`的`.h`头文件，并向其中添加以下代码:

```
#import "UIKit/UIKit.h"
#import "AVFoundation/AVFoundation.h"

@interface RNCSTMCamera : UIView

@property (strong, nullable) AVCapturePhotoOutput *photoOutput;

@end

```

我们添加了类型为`AVCapturePhotoOutput`的`photoOutput`属性，用于捕捉照片。

现在，让我们创建这个类的一个实现。按照相同的步骤创建一个 Objective-C 实现(`.m`)文件，并将该文件命名为`RNCSTMCamera`。要访问设备摄像头并返回其实时反馈，请添加以下代码:

```
#import "RNCTSTMCamera.h"
#import "AVFoundation/AVFoundation.h"

@implementation RNCSTMCamera

- (instancetype)initWithFrame:(CGRect)frame
{
  _photoOutput = [[AVCapturePhotoOutput alloc] init];
  self = [super initWithFrame:frame];
  if (self) {
    NSLog(@"RNCSTMCamera was initialized in init with Frame");
    [self initializeCamera];
  }
  return self;
}

- (void) initializeCamera {
  AVCaptureSession *session = [[AVCaptureSession alloc] init];
  session.sessionPreset = AVCaptureSessionPresetHigh;
  CALayer *viewLayer = self.layer;
  AVCaptureVideoPreviewLayer *captureVideoPreviewLayer = [[AVCaptureVideoPreviewLayer alloc] initWithSession:session];
  captureVideoPreviewLayer.frame = CGRectMake(UIScreen.mainScreen.bounds.origin.x,UIScreen.mainScreen.bounds.origin.y,UIScreen.mainScreen.bounds.size.width,UIScreen.mainScreen.bounds.size.height);
  [captureVideoPreviewLayer setVideoGravity:AVLayerVideoGravityResizeAspectFill];
  [self.layer addSublayer:captureVideoPreviewLayer];
  AVCaptureDevice *device = nil;
  NSArray *devices = [AVCaptureDevice devicesWithMediaType:AVMediaTypeVideo];
  for(AVCaptureDevice *camera in devices) {
      if([camera position] == AVCaptureDevicePositionFront) {
          device = camera;
          break;
      }
  }
  NSError *error = nil;
  AVCaptureDeviceInput *input = [AVCaptureDeviceInput deviceInputWithDevice:device error:&error];
  if (!input) NSLog(@"ERROR: trying to open camera: %@", error);
  [session addInput:input];
  [session addOutput:_photoOutput];
  [session startRunning];
  [self setClipsToBounds:true];
}

@end

```

当我们调用`initWithFrame`时，我们调用`initializeCamera`方法，这将启动一个`AVCaptureSession`。然后我们创建一个`AVCaptureVideoPreviewLayer`并用`AVCaptureSession`初始化它。

一旦`AVCaptureVideoPreviewLayer`准备好了，我们添加`AVCaptureVideoPreviewLayer`作为我们的根`UIView`的子层，并设置`AVCaptureDevice`为前置摄像头。

接下来，我们创建一个`AVCaptureDeviceInput`对象，它决定在会话中显示哪个提要，然后将`photoOutput`作为输出添加到`session`中，用于从设备摄像头捕捉照片。最后，我们开始会议。

要访问相机并能够写入用户的照片库，我们需要向`info.plist`文件添加以下权限:

![Permissions](img/168d96d023bb89bf14f13980f7ab4edd.png)

要接收捕获照片命令，打开`RNCSTMCameraManager.m`并添加以下内容:

```
RCT_EXPORT_METHOD(captureImage: (nonnull NSNumber *)viewTag)
{
  [self.bridge.uiManager addUIBlock:^(RCTUIManager *uiManager, NSDictionary<NSNumber *, UIView *> *viewRegistry) {
    RNCSTMCamera *view = viewRegistry[viewTag];

    if ([view isKindOfClass:[UIView class]]) {
      NSLog(@"Capture Command was called");
      [self capturePhoto:view];
    } else {
      RCTLogError(@"view type must be UIView");
    }
  }];
}

-(void) capturePhoto:(RNCSTMCamera*) view{
  AVCapturePhotoSettings *settings = [AVCapturePhotoSettings photoSettingsWithFormat:@{AVVideoCodecKey: AVVideoCodecTypeJPEG}];
  [view.photoOutput capturePhotoWithSettings:settings delegate:self];
}

#pragma mark - AVCapturePhotoCaptureDelegate
- (void)captureOutput:(AVCapturePhotoOutput *)output didFinishProcessingPhoto:(AVCapturePhoto *)photo error:(nullable NSError *)error
{
  NSData *imageData = [photo fileDataRepresentation];
  UIImage *image = [UIImage imageWithData:imageData];
  NSLog(@"image is: %@",image);
  UIImageWriteToSavedPhotosAlbum(image, NULL, NULL, NULL);
  UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Photo Saved!" message:@"The Photo has been successfully saved in your photo library." delegate:self cancelButtonTitle:NULL otherButtonTitles:@"👍", nil];
  [alert show];
}

```

在这里，我们已经将`captureImage`导出到 JS 代码，因此，当被调用时，它将检查类型`UIView`。然后，我们调用`capturePhoto`从`RNCSTMCamera`类的`photoOutput`属性中捕获图像。

我们还添加了一个宏来覆盖`AVCapturePhotoCaptureDelegate`，它在我们调用`captureWithSettings`时被触发。在这个方法中，我们使用`fileDataRepresentation`将`AVCapturePhoto`转换为`imageData`。然后，我们使用该数据创建一个`UIImage`，并将其保存到照片库中。

我们的最后一步是发送命令来为 iOS 捕捉照片。打开 VSCode 中的`App.js`,将`dispatchCaptureCommand`中的代码更改如下:

```
const dispatchCaptureCommand = () => {
  Platform.OS === 'Android'
    ? UIManager?.dispatchViewManagerCommand(
        findNodeHandle(componentRef?.current),
        UIManager?.RNCSTMCamera?.Commands?.captureImage?.toString(),
        [findNodeHandle(componentRef?.current)],
      )
    : UIManager.dispatchViewManagerCommand(
        findNodeHandle(componentRef?.current),
        UIManager.getViewManagerConfig('RNCSTMCamera').Commands.captureImage,
        [],
      );
};

```

您可以看到，我们使用不同的语法在 iOS 中发送命令。这是因为 iOS 中的底层视图管理有一些不同的逻辑。

## 结论

如果您希望为自己的 React 本机应用程序实现一个不支持开箱即用的特性，那么在 React Native 中构建和使用本机 UI 组件会非常方便。如果您正在将现有的原生 iOS 和 Android 应用程序转换为 React Native，这也非常有用。感谢阅读！

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)