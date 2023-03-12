# 用 react-native-webrtc-log rocket Blog 创建 RN 视频通话 app

> 原文：<https://blog.logrocket.com/creating-rn-video-calling-app-react-native-webrtc/>

[WebRTC](https://webrtc.org/) (web 实时通信)是一个开源项目，使 web 应用程序能够捕获和传输音频和视频流。该技术在所有现代浏览器和主要本机平台上都可用。它是由谷歌、苹果、微软等公司开发和维护的。

在这一点上，WebRTC 是一种成熟的技术，并且是构建视频会议应用程序的事实上的方法。

## 构建我们的演示:一个 React 本地聊天应用程序

我们将使用 [react-native-webrtc](https://github.com/react-native-webrtc/react-native-webrtc) 插件构建一个 React 原生应用程序。当我们实现应用程序时，我们将学习 WebRTC 的基础知识。

这是该应用程序的最终版本。它实际上是在我的手机上发起的视频通话，并在我的笔记本电脑上接听。启动网络摄像头后，我们可以开始新的视频通话或加入现有的视频通话。

如果我们开始通话，我们会得到一个唯一的 ID。其他人可以通过在文本框中键入该 ID 并按下**应答呼叫**按钮来加入该呼叫。整个项目的[代码可以在这里](https://github.com/cristian-rita/react-native-webrtc-example)找到。

![Chat Apps](img/6636ea81554bbf0b4645ff4aaa531294.png)

## 装置

首先，让我们创建一个空白的 React 原生项目:

```
npx react-native init ReactNativeWebRTCExample
```

然后，我们需要安装 react-native-webrtc:

```
npm install react-native-webrtc
```

为了完成安装，根据平台的不同，我们有一些额外的步骤。

### iOS 实施

安装 CocoaPods:

```
npx pod-install
```

更新`Info.plist`文件中的权限:

```
<key>NSCameraUsageDescription</key>
<string>Camera permission description</string>
<key>NSMicrophoneUsageDescription</key>
<string>Microphone permission description</string>
```

### Android 实现

类似地，在 Android 上，我们需要在`AndroidManifest.xml`文件中请求这些权限:

```
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.BLUETOOTH" />
    <uses-permission android:name="android.permission.CAMERA" />
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.MODIFY_AUDIO_SETTINGS" />
    <uses-permission android:name="android.permission.RECORD_AUDIO" />
    <uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW" />
    <uses-permission android:name="android.permission.WAKE_LOCK" />
```

我们还需要通过在 Android 部分中向`android/app/build.gradle`添加以下代码来启用 Java 8 支持:

```
compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
}
```

我们完成了安装。

## 创建实时聊天应用程序

接下来，我们需要创建一个`RTCPeerConnection`的实例，它将管理本地计算机和远程对等体之间的连接。

虽然数据传输是点对点的，但是我们确实需要一些服务器。其中之一是 ICE(交互式连接建立)服务器。为了建立对等连接，两个客户端需要找到发现彼此的方法。因为 NAT，这可能会很棘手，所以 ICE 服务器负责为我们做所有的工作。谢天谢地，谷歌免费提供 ICE 服务器:

```
//App.js

  const [remoteStream, setRemoteStream] = useState(null);
  const [localStream, setLocalStream] = useState(null);
  const [webcamStarted, setWebcamStarted] = useState(false);
  const [channelId, setChannelId] = useState(null);
  const pc = useRef();
  const servers = {
    iceServers: [
      {
        urls: [
          'stun:stun1.l.google.com:19302',
          'stun:stun2.l.google.com:19302',
        ],
      },
    ],
    iceCandidatePoolSize: 10,
  };
```

这里我们声明 ICE 服务器的配置。我们还声明了一些很快就会用到的状态变量。

下一步，我们将从用户的摄像机中捕获本地流，并将其添加到 RTCPeerConnection 中。`MediaDevices`接口提供对相机和麦克风等已连接媒体输入的访问。通过调用`mediaDevices.getUserMedia()`方法，我们要求用户授予访问这些媒体输入的权限。我们还将更新本地和远程曲目(音频和视频曲目):

```
const startWebcam = async () => {
    pc.current = new RTCPeerConnection(servers);
    const local = await mediaDevices.getUserMedia({
      video: true,
      audio: true,
    });
    pc.current.addStream(local);
    setLocalStream(local);

    const remote = new MediaStream();
    setRemoteStream(remote);

    // Push tracks from local stream to peer connection
    local.getTracks().forEach(track => {
      pc.current.getLocalStreams()[0].addTrack(track);
    });

    // Pull tracks from peer connection, add to remote video stream
    pc.current.ontrack = event => {
      event.streams[0].getTracks().forEach(track => {
        remote.addTrack(track);
      });
    };

    pc.current.onaddstream = event => {
      setRemoteStream(event.stream);
    };
  };
```

此时，我们创建了一个`RTCPeerConnection`来管理我们的对等连接。我们从本地对等点捕获视频和音频流，并将该流添加到`RTCPeerConnection`。

我们现在准备好连接到另一个对等体。为此，我们需要一个信令服务器。它的工作是充当中介，让两个对等体建立连接。作为发起者，你需要发出开始打电话的信号。其他对等方也必须发出信号，表示他们想要连接到您的特定视频通话。

WebRTC 对如何发信号没有自己的看法。我们可以通过 WebSockets、HTTP 或任何我们喜欢的方式来实现。在本演示中，我们将使用 Firebase Firestore 作为信令服务器。

连接到 [Firebase](https://blog.logrocket.com/storing-retrieving-data-react-native-apps-firebase/) 和使用 Firestore 不在本文讨论范围之内，所以我将让您创建 Firebase 应用程序并从 React Native 连接到它们。我将使用 https://rnfirebase.io/的[与 Firebase 交互。](https://rnfirebase.io/)

所有这些都准备好了，我们现在可以创建发起呼叫的函数:

```
   const startCall = async () => {
    const channelDoc = firestore().collection('channels').doc();
    const offerCandidates = channelDoc.collection('offerCandidates');
    const answerCandidates = channelDoc.collection('answerCandidates');

    setChannelId(channelDoc.id);

    pc.current.onicecandidate = async event => {
      if (event.candidate) {
        await offerCandidates.add(event.candidate.toJSON());
      }
    };

    //create offer
    const offerDescription = await pc.current.createOffer();
    await pc.current.setLocalDescription(offerDescription);

    const offer = {
      sdp: offerDescription.sdp,
      type: offerDescription.type,
    };

    await channelDoc.set({offer});

    // Listen for remote answer
    channelDoc.onSnapshot(snapshot => {
      const data = snapshot.data();
      if (!pc.current.currentRemoteDescription && data?.answer) {
        const answerDescription = new RTCSessionDescription(data.answer);
        pc.current.setRemoteDescription(answerDescription);
      }
    });

    // When answered, add candidate to peer connection
    answerCandidates.onSnapshot(snapshot => {
      snapshot.docChanges().forEach(change => {
        if (change.type === 'added') {
          const data = change.doc.data();
          pc.current.addIceCandidate(new RTCIceCandidate(data));
        }
      });
    });
  };
```

我知道这很多，但是让我们试着分解一下。

在 Firestore，我们保存了一个文档`channels`，包含所有的沟通渠道。每个信道 ID 代表一个唯一的呼叫 ID。作为信令机制的一部分，我们创建了两个子集合:`offerCandidates`和`answerCandidates`。

`createOffer()`方法启动 SDP 提议的创建，目的是启动到远程对等点的新 WebRTC 连接。我们将此提议写入 Firestore 上的频道文档。然后我们监听`channelDoc`更新。在收到一个回答后，我们创建一个`RTCSessionDescription`对象。协商两个对等体之间的连接包括来回交换`RTCSessionDescription`对象。

如果一个新的文档被添加到`answerCandidates`子集合中，这意味着有人已经回答了，所以我们将这个新的候选文档添加到`RTCPeerConnection`中。

类似地，让我们实现用于应答呼叫的函数:

```
const joinCall = async () => {
    const channelDoc = firestore().collection('channels').doc(channelId);
    const offerCandidates = channelDoc.collection('offerCandidates');
    const answerCandidates = channelDoc.collection('answerCandidates');

    pc.current.onicecandidate = async event => {
      if (event.candidate) {
        await answerCandidates.add(event.candidate.toJSON());
      }
    };

    const channelDocument = await channelDoc.get();
    const channelData = channelDocument.data();

    const offerDescription = channelData.offer;

    await pc.current.setRemoteDescription(
      new RTCSessionDescription(offerDescription),
    );

    const answerDescription = await pc.current.createAnswer();
    await pc.current.setLocalDescription(answerDescription);

    const answer = {
      type: answerDescription.type,
      sdp: answerDescription.sdp,
    };

    await channelDoc.update({answer});

    offerCandidates.onSnapshot(snapshot => {
      snapshot.docChanges().forEach(change => {
        if (change.type === 'added') {
          const data = change.doc.data();
          pc.current.addIceCandidate(new RTCIceCandidate(data));
        }
      });
    });
  };
```

在这种情况下，我们创建一个`answerOffer`并更新 Firestore 中的`channelDoc`。我们还监听`offerCandidates`子集合中的任何变化。或多或少，我们在模仿开始呼叫的行为。

最后，当我们想要开始或加入一个呼叫时，我们需要调用这些函数:

```
<KeyboardAvoidingView style={styles.body} behavior="position">
      <SafeAreaView>
        {localStream && (
          <RTCView
            streamURL={localStream?.toURL()}
            style={styles.stream}
            objectFit="cover"
            mirror
          />
        )}

        {remoteStream && (
          <RTCView
            streamURL={remoteStream?.toURL()}
            style={styles.stream}
            objectFit="cover"
            mirror
          />
        )}
        <View style={styles.buttons}>
          {!webcamStarted && (
            <Button title="Start webcam" onPress={startWebcam} />
          )}
          {webcamStarted && <Button title="Start call" onPress={startCall} />}
          {webcamStarted && (
            <View style={{flexDirection: 'row'}}>
              <Button title="Join call" onPress={joinCall} />
              <TextInput
                value={channelId}
                placeholder="callId"
                minLength={45}
                style={{borderWidth: 1, padding: 5}}
                onChangeText={newText => setChannelId(newText)}
              />
            </View>
          )}
        </View>
      </SafeAreaView>
```

## 结论

WebRTC 是一项强大的技术，使用`react-native-webrtc`我们可以用浏览器上可用的相同 API 构建 React 本地应用程序。

您可以用这个项目的[代码亲自尝试一下。](https://github.com/cristian-rita/react-native-webrtc-example)

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)