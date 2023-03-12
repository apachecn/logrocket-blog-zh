# 如何在 React Native 中构建转录应用

> 原文：<https://blog.logrocket.com/build-transcription-app-react-native/>

当使用亚马逊的 Alexa 或苹果的 Siri 等工具时，你可能会想知道它们如何理解你说的话和你的声音。本质上，你所说的话被转换成文本，并输入到一个系统中，作为结果提供一个响应。

最近，我一直在探索原生移动应用程序中的语音识别。根据我自己的经验， [React Native Voice](https://github.com/react-native-voice/voice) 是创建 React Native transcription app 最容易访问的库。然而，如果你不熟悉语音识别或 React Native，正确配置该应用程序可能会非常困难。

在本教程中，我将带你在 React Native 中使用 React Native 语音库创建一个简单的转录应用程序。我们的 React 原生转录应用程序将允许用户录制音频，然后将其转录为文本。我们开始吧！

## 目录

## 为什么使用 React Native 作为您的转录应用程序？

React Native 是一个 [JavaScript 框架，允许你为 iOS 和 Android 创建原生应用](https://blog.logrocket.com/complete-guide-react-native-web/)，通过在两个平台上使用相同的代码来节省你的时间和金钱。

与其他框架相比，React Native 提供了许多好处，包括文件更小、性能更快以及对第三方库的更好支持。此外，React Native 是开源的，这意味着有一个大型的开发人员社区可以为项目做出贡献并帮助改进它。总之，这使得 React Native 成为构建我们的转录应用程序的绝佳选择。

## 反应原生语音方法

React Native Voice 包括大量有用的事件触发方法，用于处理应用程序中的语音。

*   `onSpeechStart`:当应用程序识别到有人开始说话时触发
*   `onSpeechRecognized`:当应用程序确定可以准确转录输入的语音数据时激活
*   当某人停止说话并有片刻的沉默时触发
*   `onSpeechError`:语音识别库抛出异常时触发
*   `onSpeechResults`:语音识别算法转录完成并返回时触发
*   `onSpeechVolume`:当应用程序检测到扬声器音量变化时触发

## 入门指南

首先，您应该熟悉 React Native 及其语法。您需要在计算机上安装 Sublime Text 或 Atom 之类的文本编辑器，最后，您需要安装 React Native CLI 工具。

一旦你安装了这些东西，你就可以开始开发你的转录应用了。要创建一个新的 React 本地项目，首先需要打开终端并导航到您希望项目所在的目录。然后，运行命令`react-native init`来创建一个新的 React 本地项目。

创建项目后，在文本编辑器中打开它。我们的转录应用程序将需要一些不同的组件。首先，我们需要一个组件来呈现转录文本。我们还需要一个允许用户输入音频的组件，以及一个将音频转换为文本的组件。一旦你编写了这些组件，你就可以把它们放在一起，创建一个完整的转录应用程序。

对于语音到文本的转换，我们将使用 React Native Voice 库提供的`Voice`组件，它包含许多事件，您可以使用这些事件来启动或停止语音识别，并获得语音识别的结果。

当我们初始化屏幕时，我们在构造函数中设置某些事件回调，如下面的代码示例所示。如你所见，我们有用于`SpeechStart`和`SpeechEnd`的函数。下面是事件发生时将自动调用的回调:

```
import { NativeModules, NativeEventEmitter, Platform } from 'react-native';
import invariant from 'invariant';
import {
  VoiceModule,
  SpeechEvents,
  SpeechRecognizedEvent,
  SpeechErrorEvent,
  SpeechResultsEvent,
  SpeechStartEvent,
  SpeechEndEvent,
  SpeechVolumeChangeEvent,
} from './VoiceModuleTypes';

const Voice = NativeModules.Voice as VoiceModule;

// NativeEventEmitter is only availabe on React Native platforms, so this conditional is used to avoid import conflicts in the browser/server
const voiceEmitter =
  Platform.OS !== 'web' ? new NativeEventEmitter(Voice) : null;
type SpeechEvent = keyof SpeechEvents;

class RCTVoice {
  _loaded: boolean;
  _listeners: any[] | null;
  _events: Required<SpeechEvents>;

  constructor() {
    this._loaded = false;
    this._listeners = null;
    this._events = {
      onSpeechStart: () => {},
      onSpeechRecognized: () => {},
      onSpeechEnd: () => {},
      onSpeechError: () => {},
      onSpeechResults: () => {},
      onSpeechPartialResults: () => {},
      onSpeechVolumeChanged: () => {},
    };
  }

  removeAllListeners() {
    Voice.onSpeechStart = undefined;
    Voice.onSpeechRecognized = undefined;
    Voice.onSpeechEnd = undefined;
    Voice.onSpeechError = undefined;
    Voice.onSpeechResults = undefined;
    Voice.onSpeechPartialResults = undefined;
    Voice.onSpeechVolumeChanged = undefined;
  }

  destroy() {
    if (!this._loaded && !this._listeners) {
      return Promise.resolve();
    }
    return new Promise((resolve, reject) => {
      Voice.destroySpeech((error: string) => {
        if (error) {
          reject(new Error(error));
        } else {
          if (this._listeners) {
            this._listeners.map(listener => listener.remove());
            this._listeners = null;
          }
          resolve();
        }
      });
    });
  }

  start(locale: any, options = {}) {
    if (!this._loaded && !this._listeners && voiceEmitter !== null) {
      this._listeners = (Object.keys(this._events) as SpeechEvent[]).map(
        (key: SpeechEvent) => voiceEmitter.addListener(key, this._events[key]),
      );
    }

    return new Promise((resolve, reject) => {
      const callback = (error: string) => {
        if (error) {
          reject(new Error(error));
        } else {
          resolve();
        }
      };
      if (Platform.OS === 'android') {
        Voice.startSpeech(
          locale,
          Object.assign(
            {
              EXTRA_LANGUAGE_MODEL: 'LANGUAGE_MODEL_FREE_FORM',
              EXTRA_MAX_RESULTS: 5,
              EXTRA_PARTIAL_RESULTS: true,
              REQUEST_PERMISSIONS_AUTO: true,
            },
            options,
          ),
          callback,
        );
      } else {
        Voice.startSpeech(locale, callback);
      }
    });
  }
  stop() {
    if (!this._loaded && !this._listeners) {
      return Promise.resolve();
    }
    return new Promise((resolve, reject) => {
      Voice.stopSpeech(error => {
        if (error) {
          reject(new Error(error));
        } else {
          resolve();
        }
      });
    });
  }
  cancel() {
    if (!this._loaded && !this._listeners) {
      return Promise.resolve();
    }
    return new Promise((resolve, reject) => {
      Voice.cancelSpeech(error => {
        if (error) {
          reject(new Error(error));
        } else {
          resolve();
        }
      });
    });
  }
  isAvailable(): Promise<0 | 1> {
    return new Promise((resolve, reject) => {
      Voice.isSpeechAvailable((isAvailable: 0 | 1, error: string) => {
        if (error) {
          reject(new Error(error));
        } else {
          resolve(isAvailable);
        }
      });
    });
  }

  /**
   * (Android) Get a list of the speech recognition engines available on the device
   * */
  getSpeechRecognitionServices() {
    if (Platform.OS !== 'android') {
      invariant(
        Voice,
        'Speech recognition services can be queried for only on Android',
      );
      return;
    }

    return Voice.getSpeechRecognitionServices();
  }

  isRecognizing(): Promise<0 | 1> {
    return new Promise(resolve => {
      Voice.isRecognizing((isRecognizing: 0 | 1) => resolve(isRecognizing));
    });
  }

  set onSpeechStart(fn: (e: SpeechStartEvent) => void) {
    this._events.onSpeechStart = fn;
  }

  set onSpeechRecognized(fn: (e: SpeechRecognizedEvent) => void) {
    this._events.onSpeechRecognized = fn;
  }
  set onSpeechEnd(fn: (e: SpeechEndEvent) => void) {
    this._events.onSpeechEnd = fn;
  }
  set onSpeechError(fn: (e: SpeechErrorEvent) => void) {
    this._events.onSpeechError = fn;
  }
  set onSpeechResults(fn: (e: SpeechResultsEvent) => void) {
    this._events.onSpeechResults = fn;
  }
  set onSpeechPartialResults(fn: (e: SpeechResultsEvent) => void) {
    this._events.onSpeechPartialResults = fn;
  }
  set onSpeechVolumeChanged(fn: (e: SpeechVolumeChangeEvent) => void) {
    this._events.onSpeechVolumeChanged = fn;
  }
}

export {
  SpeechEndEvent,
  SpeechErrorEvent,
  SpeechEvents,
  SpeechStartEvent,
  SpeechRecognizedEvent,
  SpeechResultsEvent,
  SpeechVolumeChangeEvent,
};
export default new RCTVoice();

```

我们使用上面的回调事件来确定语音识别的状态。现在，让我们看看如何开始、停止、取消和删除语音识别过程。

## 启动语音识别方法

当您按下**开始**按钮时，语音识别方法启动。这是一种异步方法，它只是尝试启动语音识别引擎，如果失败，将错误记录到控制台。

现在我们已经熟悉了 React 原生语音库，让我们继续看代码！在这个例子中，我们将创建一个带有麦克风符号的屏幕作为可点击的按钮。点击按钮后，我们将开始语音识别；通过这个过程，我们可以检索回调函数中所有内容的状态。要停止语音到文本的翻译，我们可以使用 stop、cancel 和 destroy 按钮。

在语音识别期间和之后，我们会得到两种结果。当语音识别器完成识别后，将会显示结果。语音识别器会在最终结果之前识别一些单词，因此，在计算结果时会出现部分结果。因为它是一个中间结果，所以一项认可的部分结果可能很多。

## 构建 React 本机应用程序

为了创建我们的 React 本地应用程序，我们将利用`react-native init`。假设您的计算机上安装了 Node.js，您可以使用 npm 安装 React Native CLI 命令行实用程序。

转到工作区，启动终端，并执行以下命令:

```
npm install -g react-native-cli

```

要启动新的 React 本地项目，请使用以下命令:

```
react-native init ProjectName

```

要使用特定的 React 本地版本启动一个新项目，请使用`—version`参数:

```
react-native init ProjectName --version X.XX.X
react-native init ProjectName --version [email protected]

```

上面的命令将在您的项目目录中创建一个项目结构，并带有一个名为`App.js`的索引文件。

## 依赖安装

在使用`Voice`组件之前，您必须安装`react-native-voice`依赖项。打开终端并导航到您的项目以安装需求:

```
cd ProjectName & npm install react-native-voice --save

```

## iOS 麦克风和语音识别权限

要让 React Native Voice 工作，您必须拥有使用麦克风的权限，而 React Native for iOS 需要向`Info.plist`文件添加键。按照以下说明授权在 iOS 项目中使用麦克风和语音识别:

在 Xcode 中，打开项目`TranscriptionExample`->-`ios`->-`yourprj.xcworkspace`

在 Xcode 中启动项目后，点按左侧边栏中的项目以查看右侧工作区中的各种选项。选择**信息**页签，即`**info.plist**`。

接下来，创建两个权限密钥，`Privacy-Microphone Usage Description`和`Privacy-Speech Recognition Usage Description`。您还可以在权限对话框出现时显示该值，如下图所示:

![Ios Button Microphone Permission Example](img/d820e323ffd88f9c12ef76aef4e4b274.png)

## 将语音转换为文本

现在，在一个功能组件中打开`App.js`，用下面的完整代码替换现有代码:

```
package com.wenkesj.voice;

import android.Manifest;
import java.util.ArrayList;
import java.util.List;
import java.util.Locale;

import javax.annotation.Nullable;

public class VoiceModule extends ReactContextBaseJavaModule implements RecognitionListener {

  final ReactApplicationContext reactContext;
  private SpeechRecognizer speech = null;
  private boolean isRecognizing = false;
  private String locale = null;

  public VoiceModule(ReactApplicationContext reactContext) {
    super(reactContext);
    this.reactContext = reactContext;
  }

  private String getLocale(String locale) {
    if (locale != null && !locale.equals("")) {
      return locale;
    }

    return Locale.getDefault().toString();
  }

  private void startListening(ReadableMap opts) {
    if (speech != null) {
      speech.destroy();
      speech = null;
    }

    if(opts.hasKey("RECOGNIZER_ENGINE")) {
      switch (opts.getString("RECOGNIZER_ENGINE")) {
        case "GOOGLE": {
          speech = SpeechRecognizer.createSpeechRecognizer(this.reactContext, ComponentName.unflattenFromString("com.google.android.googlequicksearchbox/com.google.android.voicesearch.serviceapi.GoogleRecognitionService"));
          break;
        }
        default:
          speech = SpeechRecognizer.createSpeechRecognizer(this.reactContext);
      }
    } else {
      speech = SpeechRecognizer.createSpeechRecognizer(this.reactContext);
    }

    speech.setRecognitionListener(this);

    final Intent intent = new Intent(RecognizerIntent.ACTION_RECOGNIZE_SPEECH);

    // Load the intent with options from JS
    ReadableMapKeySetIterator iterator = opts.keySetIterator();
    while (iterator.hasNextKey()) {
      String key = iterator.nextKey();
      switch (key) {
        case "EXTRA_LANGUAGE_MODEL":
          switch (opts.getString(key)) {
            case "LANGUAGE_MODEL_FREE_FORM":
              intent.putExtra(RecognizerIntent.EXTRA_LANGUAGE_MODEL, RecognizerIntent.LANGUAGE_MODEL_FREE_FORM);
              break;
            case "LANGUAGE_MODEL_WEB_SEARCH":
              intent.putExtra(RecognizerIntent.EXTRA_LANGUAGE_MODEL, RecognizerIntent.LANGUAGE_MODEL_WEB_SEARCH);
              break;
            default:
              intent.putExtra(RecognizerIntent.EXTRA_LANGUAGE_MODEL, RecognizerIntent.LANGUAGE_MODEL_FREE_FORM);
              break;
          }
          break;
        case "EXTRA_MAX_RESULTS": {
          Double extras = opts.getDouble(key);
          intent.putExtra(RecognizerIntent.EXTRA_MAX_RESULTS, extras.intValue());
          break;
        }
        case "EXTRA_PARTIAL_RESULTS": {
          intent.putExtra(RecognizerIntent.EXTRA_PARTIAL_RESULTS, opts.getBoolean(key));
          break;
        }
        case "EXTRA_SPEECH_INPUT_MINIMUM_LENGTH_MILLIS": {
          Double extras = opts.getDouble(key);
          intent.putExtra(RecognizerIntent.EXTRA_SPEECH_INPUT_MINIMUM_LENGTH_MILLIS, extras.intValue());
          break;
        }
        case "EXTRA_SPEECH_INPUT_COMPLETE_SILENCE_LENGTH_MILLIS": {
          Double extras = opts.getDouble(key);
          intent.putExtra(RecognizerIntent.EXTRA_SPEECH_INPUT_COMPLETE_SILENCE_LENGTH_MILLIS, extras.intValue());
          break;
        }
        case "EXTRA_SPEECH_INPUT_POSSIBLY_COMPLETE_SILENCE_LENGTH_MILLIS": {
          Double extras = opts.getDouble(key);
          intent.putExtra(RecognizerIntent.EXTRA_SPEECH_INPUT_POSSIBLY_COMPLETE_SILENCE_LENGTH_MILLIS, extras.intValue());
          break;
        }
      }
    }

    intent.putExtra(RecognizerIntent.EXTRA_LANGUAGE, getLocale(this.locale));
    speech.startListening(intent);
  }

  private void startSpeechWithPermissions(final String locale, final ReadableMap opts, final Callback callback) {
    this.locale = locale;

    Handler mainHandler = new Handler(this.reactContext.getMainLooper());
    mainHandler.post(new Runnable() {
      @Override
      public void run() {
        try {
          startListening(opts);
          isRecognizing = true;
          callback.invoke(false);
        } catch (Exception e) {
          callback.invoke(e.getMessage());
        }
      }
    });
  }

  @Override
  public String getName() {
    return "RCTVoice";
  }

  @ReactMethod
  public void startSpeech(final String locale, final ReadableMap opts, final Callback callback) {
    if (!isPermissionGranted() && opts.getBoolean("REQUEST_PERMISSIONS_AUTO")) {
      String[] PERMISSIONS = {Manifest.permission.RECORD_AUDIO};
      if (this.getCurrentActivity() != null) {
        ((PermissionAwareActivity) this.getCurrentActivity()).requestPermissions(PERMISSIONS, 1, new PermissionListener() {
          public boolean onRequestPermissionsResult(final int requestCode,
                                                    @NonNull final String[] permissions,
                                                    @NonNull final int[] grantResults) {
            boolean permissionsGranted = true;
            for (int i = 0; i < permissions.length; i++) {
              final boolean granted = grantResults[i] == PackageManager.PERMISSION_GRANTED;
              permissionsGranted = permissionsGranted && granted;
            }
            startSpeechWithPermissions(locale, opts, callback);
            return permissionsGranted;
          }
        });
      }
      return;
    }
    startSpeechWithPermissions(locale, opts, callback);
  }

  @ReactMethod
  public void stopSpeech(final Callback callback) {
    Handler mainHandler = new Handler(this.reactContext.getMainLooper());
    mainHandler.post(new Runnable() {
      @Override
      public void run() {
        try {
          if (speech != null) {
            speech.stopListening();
          }
          isRecognizing = false;
          callback.invoke(false);
        } catch(Exception e) {
          callback.invoke(e.getMessage());
        }
      }
    });
  }

  @ReactMethod
  public void cancelSpeech(final Callback callback) {
    Handler mainHandler = new Handler(this.reactContext.getMainLooper());
    mainHandler.post(new Runnable() {
      @Override
      public void run() {
        try {
          if (speech != null) {
            speech.cancel();
          }
          isRecognizing = false;
          callback.invoke(false);
        } catch(Exception e) {
          callback.invoke(e.getMessage());
        }
      }
    });
  }

  @ReactMethod
  public void destroySpeech(final Callback callback) {
    Handler mainHandler = new Handler(this.reactContext.getMainLooper());
    mainHandler.post(new Runnable() {
      @Override
      public void run() {
        try {
          if (speech != null) {
            speech.destroy();
          }
          speech = null;
          isRecognizing = false;
          callback.invoke(false);
        } catch(Exception e) {
          callback.invoke(e.getMessage());
        }
      }
    });
  }

  @ReactMethod
  public void isSpeechAvailable(final Callback callback) {
    final VoiceModule self = this;
    Handler mainHandler = new Handler(this.reactContext.getMainLooper());
    mainHandler.post(new Runnable() {
      @Override
      public void run() {
        try {
          Boolean isSpeechAvailable = SpeechRecognizer.isRecognitionAvailable(self.reactContext);
          callback.invoke(isSpeechAvailable, false);
        } catch(Exception e) {
          callback.invoke(false, e.getMessage());
        }
      }
    });
  }

  @ReactMethod
  public void getSpeechRecognitionServices(Promise promise) {
    final List<ResolveInfo> services = this.reactContext.getPackageManager()
        .queryIntentServices(new Intent(RecognitionService.SERVICE_INTERFACE), 0);
    WritableArray serviceNames = Arguments.createArray();
    for (ResolveInfo service : services) {
      serviceNames.pushString(service.serviceInfo.packageName);
    }

    promise.resolve(serviceNames);
  }

  private boolean isPermissionGranted() {
    String permission = Manifest.permission.RECORD_AUDIO;
    int res = getReactApplicationContext().checkCallingOrSelfPermission(permission);
    return res == PackageManager.PERMISSION_GRANTED;
  }

  @ReactMethod
  public void isRecognizing(Callback callback) {
    callback.invoke(isRecognizing);
  }

  private void sendEvent(String eventName, @Nullable WritableMap params) {
    this.reactContext
      .getJSModule(DeviceEventManagerModule.RCTDeviceEventEmitter.class)
      .emit(eventName, params);
  }

  @Override
  public void onBeginningOfSpeech() {
    WritableMap event = Arguments.createMap();
    event.putBoolean("error", false);
    sendEvent("onSpeechStart", event);
    Log.d("ASR", "onBeginningOfSpeech()");
  }

  @Override
  public void onBufferReceived(byte[] buffer) {
    WritableMap event = Arguments.createMap();
    event.putBoolean("error", false);
    sendEvent("onSpeechRecognized", event);
    Log.d("ASR", "onBufferReceived()");
  }

  @Override
  public void onEndOfSpeech() {
    WritableMap event = Arguments.createMap();
    event.putBoolean("error", false);
    sendEvent("onSpeechEnd", event);
    Log.d("ASR", "onEndOfSpeech()");
    isRecognizing = false;
  }

  @Override
  public void onError(int errorCode) {
    String errorMessage = String.format("%d/%s", errorCode, getErrorText(errorCode));
    WritableMap error = Arguments.createMap();
    error.putString("message", errorMessage);
    error.putString("code", String.valueOf(errorCode));
    WritableMap event = Arguments.createMap();
    event.putMap("error", error);
    sendEvent("onSpeechError", event);
    Log.d("ASR", "onError() - " + errorMessage);
  }

  @Override
  public void onEvent(int arg0, Bundle arg1) { }

  @Override
  public void onPartialResults(Bundle results) {
    WritableArray arr = Arguments.createArray();

    ArrayList<String> matches = results.getStringArrayList(SpeechRecognizer.RESULTS_RECOGNITION);
    for (String result : matches) {
      arr.pushString(result);
    }

    WritableMap event = Arguments.createMap();
    event.putArray("value", arr);
    sendEvent("onSpeechPartialResults", event);
    Log.d("ASR", "onPartialResults()");
  }

  @Override
  public void onReadyForSpeech(Bundle arg0) {
    WritableMap event = Arguments.createMap();
    event.putBoolean("error", false);
    sendEvent("onSpeechStart", event);
    Log.d("ASR", "onReadyForSpeech()");
  }

  @Override
  public void onResults(Bundle results) {
    WritableArray arr = Arguments.createArray();

    ArrayList<String> matches = results.getStringArrayList(SpeechRecognizer.RESULTS_RECOGNITION);
    for (String result : matches) {
      arr.pushString(result);
    }

    WritableMap event = Arguments.createMap();
    event.putArray("value", arr);
    sendEvent("onSpeechResults", event);
    Log.d("ASR", "onResults()");
  }

  @Override
  public void onRmsChanged(float rmsdB) {
    WritableMap event = Arguments.createMap();
    event.putDouble("value", (double) rmsdB);
    sendEvent("onSpeechVolumeChanged", event);
  }

  public static String getErrorText(int errorCode) {
    String message;
    switch (errorCode) {
      case SpeechRecognizer.ERROR_AUDIO:
        message = "Audio recording error";
        break;
      case SpeechRecognizer.ERROR_CLIENT:
        message = "Client side error";
        break;
      case SpeechRecognizer.ERROR_INSUFFICIENT_PERMISSIONS:
        message = "Insufficient permissions";
        break;
      case SpeechRecognizer.ERROR_NETWORK:
        message = "Network error";
        break;
      case SpeechRecognizer.ERROR_NETWORK_TIMEOUT:
        message = "Network timeout";
        break;
      case SpeechRecognizer.ERROR_NO_MATCH:
        message = "No match";
        break;
      case SpeechRecognizer.ERROR_RECOGNIZER_BUSY:
        message = "RecognitionService busy";
        break;
      case SpeechRecognizer.ERROR_SERVER:
        message = "error from server";
        break;
      case SpeechRecognizer.ERROR_SPEECH_TIMEOUT:
        message = "No speech input";
        break;
      default:
        message = "Didn't understand, please try again.";
        break;
    }
    return message;
  }
}

```

## 启动 React 本机应用程序

重新打开终端，并使用下面的命令前往您的项目:

```
cd ProjectName

```

要在 Android 虚拟设备或真实调试设备上执行项目，请使用以下命令:

```
react-native run-android 

```

仅对于 macOS 上的 iOS 模拟器，使用以下命令:

```
run-ios with react-native

```

## 结论

你可以在这个 [GitHub 库](https://github.com/martinwairegi/TranscriptionReactNative)找到这个项目的完整代码。我提取并修改了用于转录和文本到语音转换功能的部分。

令人难以置信的是，语音识别已经取得了如此大的进步，而且只需很少甚至没有理论上的转录技能，就可以简单地将其集成到我们的应用程序中。如果您想在应用程序中使用语音识别，但缺乏设计独特模型的技能或时间，我强烈建议您采用这一功能。

您也可以基于本教程中提供的信息，向您的转录应用程序添加附加功能。例如，您可以允许用户在他们的转录文本中搜索特定的关键字或术语。您可以包含一个共享功能，以便用户可以与其他人共享他们的转录，或者最后，您可以为用户提供一种方法，将他们的转录导出为其他格式，如 pdf 或 Word 文档。

我希望这篇文章是有帮助的。如果您有任何疑问或问题，请务必留下评论。编码快乐！

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)