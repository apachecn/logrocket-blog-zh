# 如何将音频波形添加到您的 Flutter 应用程序中

> 原文：<https://blog.logrocket.com/audio-waveforms-flutter-app/>

当在你的应用程序中添加录音功能时，向用户展示某种音频正在被录制的视觉表现通常是一个好主意。例如，您可以在 UI 中添加一个计时器，通知用户音频已经录制了多长时间。但是，如果你真的想给你的用户一个干净的视觉体验，波形可以改变游戏规则。

在这篇文章中，我们将学习当你在 Flutter 中录制音频或播放音频文件时，如何创建简单的波形。我们开始吧！

*向前跳转:*

## 什么是音频波形，我们为什么需要它们？

简而言之，音频波形是表示音频不同部分音量的图形。x 轴代表时间，y 轴代表振幅。所以，浪越高，声音越大。类似地，较低的波或较平坦的波代表音频中较柔和的区域。

波形让用户知道他们是轻声说话还是大声说话，这样他们就可以相应地调整音量。例如，用户可能试图轻声说话，但声音太轻，麦克风无法拾取。通过查看图表，他们可以很容易地决定提高麦克风的音量。

当您想要播放或流化音频文件时，波形也很方便。例如，当医生听听诊器传递的声音时，在屏幕上看到显示声音模式的图形只会使体验更好。

在本教程中，我们将为 Flutter 应用程序构建如下图所示的功能:

![Flutter Waveforms Recorder](img/5d5e6865cfd8bc53f19019277d774478.png)

Waveforms in recorder

![Wave Forms Player](img/cc0d739958d34eee8241120914579051.png)

Waveforms in player

## 设置我们的 Flutter 应用

我们将从添加所需的依赖项和权限开始，包括[音频波形](https://pub.dev/packages/audio_waveforms)包:

```
flutter pub add audio_waveforms

```

将包导入我们的`main.dart`文件:

```
import 'package:audio_waveforms/audio_waveforms.dart';

```

现在，将记录权限添加到 Android 清单中:

```
<uses-permission android:name="android.permission.RECORD_AUDIO" />

```

## 创建波形

`AudioWaveforms`小工具将创建我们的波形。但是，在我们继续之前，我们需要创建一个将被传递给我们的`AudioWaveforms`小部件的`recorderController`。让我们用下面的代码在我们的状态中声明一个`RecordController`:

```
late final RecorderController recorderController;

```

然后，要初始化控制器，请输入下面的代码:

```
void _initialiseController() {
 recorderController = RecorderController()
      ..androidEncoder = AndroidEncoder.aac
      ..androidOutputFormat = AndroidOutputFormat.mpeg4
      ..iosEncoder = IosEncoder.kAudioFormatMPEG4AAC
      ..sampleRate = 16000;
}

```

我们可以根据需要改变采样率和编码器。我们将在`initState`中调用这个方法:

```
void initState() {
  super.initState();
  _initialiseController();
}

```

现在，使用控制器来记录音频和显示波形就像将`AudioWaveform`小部件添加到我们的小部件树一样简单，如下所示:

```
AudioWaveforms(
  size: Size(MediaQuery.of(context).size.width, 200.0),
  recorderController: recorderController,
),

void _startRecording() async {
  await recorderController.record(path);
  // update state here to, for eample, change the button's state
}

```

为了开始记录，我们将通过单击一个按钮来调用上面的方法:

```
IconButton(
          icon: Icon(Icons.mic),
          tooltip: 'Start recording',
          onPressed: _startRecording
)

```

我们将使用以下代码停止记录:

```
final path = await recorderController.stop();

```

停止记录器将返回保存记录的文件的路径。

## 定制波形

我们已经看到我们可以控制`RecordController`中的采样率和编码。
`AudioWaveforms`也允许我们改变波浪的风格，包括`size`、`waveStyle`、`color`、`padding`和`margin`等方面:

```
AudioWaveforms(
              enableGesture: true,
              size: Size(MediaQuery.of(context).size.width / 2, 50),
              recorderCAudioWaveforms(
              enableGesture: true,
              size: Size(MediaQuery.of(context).size.width / 2, 50),
              recorderController: recorderController,
              waveStyle: const WaveStyle(
                waveColor: Colors.white,
                extendWaveform: true,
                showMiddleLine: false,
              ),
              decoration: BoxDecoration(
                borderRadius: BorderRadius.circular(12.0),
                color: const Color(0xFF1E1B26),
              ),
              padding: const EdgeInsets.only(left: 18),
              margin: const EdgeInsets.symmetric(horizontal: 15),
            )

```

我们也可以对波浪应用颜色渐变:

```
waveStyle: WaveStyle(
   gradient: ui.Gradient.linear(
    const Offset(70, 50),
    Offset(MediaQuery.of(context).size.width / 2, 0),
    [Colors.red, Colors.green],
   ),
  )

```

## 播放音频

现在，我们将学习如何播放音频文件并为它们生成波形。此处与前一个录音示例的主要区别在于:

1.  我们将创建一个`PlayerController`而不是一个`RecordController`
2.  我们将使用`AudioFileWaveforms`而不是`AudioWaveforms`

首先，我们将运行与之前相同的代码，用`PlayerController`替换`RecordController`:

```
late final PlayerController playerController;
```

但是，这一次，我们做了以下更改:

```
void _initialiseController() {
 playerController = PlayerController();
}

```

将此`playerController`传递给小部件树中的`AudioFileWaveforms`小部件:

```
AudioFileWaveforms(
 size: Size(MediaQuery.of(context).size.width, 100.0),
 playerController: playerController,
)

```

我们需要向控制器提供音频文件的路径。你可以随意抓取文件的路径。我们将使用`path_provider`来完成这个任务。将`path_provider`添加到您的`pubspec.yaml`中，然后在您的`main.dart`顶部添加以下代码:

```
import 'package:path_provider/path_provider.dart';

```

您可以将路径存储在一个名为`path`的变量中:

```
String? path;
late Directory directory;

void initState() {
  super.initState();
  _initialiseController();
  directory = await getApplicationDocumentsDirectory();
  path = "${directory.path}/test_audio.aac";
  playerController.preparePlayer(path);
}

```

注意，我们还通过提供音频文件的路径来调用控制器上的`preparePlayer`方法。我们可以像使用`recordController`一样启动和停止播放器:

```
await playerController.startPlayer();
await playerController.stopPlayer();

void _playandPause() async {
  playerController.playerState == PlayerState.playing
  ? await playerController.pausePlayer()
  : await playerController.startPlayer(finishMode: FinishMode.loop);
}

```

现在，我们可以在点击按钮时调用`_playandPause`方法。我们还为`startPlayer`方法提供了一个`finishMode`，以便在结束时循环音频和波形。您还可以添加其他选项，分别用`FinishMode.pause`和`FinishMode.stop`暂停波形或停止波形。

我们还可以添加使用波形上的手势搜索音频的功能:

```
AudioFileWaveforms(
 enableSeekGesture: true,
)

```

我们将波形设计如下:

```
AudioFileWaveforms(
 size: Size(MediaQuery.of(context).size.width / 2, 70),
              playerController: playerController,
              density: 1.5,
              playerWaveStyle: const PlayerWaveStyle(
                scaleFactor: 0.8,
                fixedWaveColor: Colors.white30,
                liveWaveColor: Colors.white,
                waveCap: StrokeCap.butt,
              ),
)

```

注意，我们使用`PlayerWaveStyle`而不是`WaveStyle`来提供样式，后者更适合记录器。

## 布置控制器

在结束之前，处理掉我们用来录制和播放音频的控制器是很重要的。在`dispose`方法中，我们将添加以下代码:

```
 @override
void dispose() {
  super.dispose();
  recorderController.dispose();
  playerController.dispose();
}

```

## 结论

我们刚刚看到使用音频波形包在 Flutter 中创建音频播放器和显示波形是多么容易。波形是音量的动态视觉表示；它们向用户提供麦克风捕捉他们所说的话的反馈，以便他们可以相应地提高或降低音量。

我们学习了如何对波形进行定制，使它们符合我们的视觉偏好，开始和停止记录，最后，处理用于记录和播放音频的控制器。我希望你喜欢这个教程；如果你有任何问题，一定要留下评论。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)