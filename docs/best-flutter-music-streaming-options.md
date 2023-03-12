# 最佳颤振音乐流媒体选项-日志火箭博客

> 原文：<https://blog.logrocket.com/best-flutter-music-streaming-options/>

所以你想做一个音乐流媒体 app，却不知道从何下手？嗯，你来对地方了。本指南将概述创建应用程序时需要考虑的重要因素，并将比较三个开源音频播放器插件的特性和优势: [Just Audio](https://pub.dev/packages/just_audio) 、 [AudioPlayers](https://pub.dev/packages/audioplayers) 和 [Assets Audio Player](https://pub.dev/packages/assets_audio_player) 。此外，本文还将提供如何让您的应用程序在第三方软件包停止工作的潜在情况下经得起未来考验的信息。

我们开始吧！

## 内容

## 音频流应用考虑因素

你可能想直接选择一个音频播放器插件，但是最好尽可能推迟实现细节。首先，你需要仔细考虑你希望你的应用程序做什么，以及它的用户界面看起来如何。

要计划你的应用程序，你可以使用 Sketch、Adobe XD 或 Figma 等工具。或者，你可以选择好的笔记本纸。画出每一个屏幕，决定什么样的用户交互是可能的。经历这个过程会迫使你考虑你之前没有想过的情况。

在规划应用的用户界面/UX 时，以下几个主题和问题可能会对你有所帮助:

*   **播放列表**:你将如何组织歌曲，按流派艺术家，还是按专辑？用户可以创建自己的自定义播放列表吗？
*   **随机播放**:你会允许用户随机重新排序播放列表吗？
*   **循环**:用户可以重复播放一首歌曲或专辑吗？
*   **下载**:除了流媒体，用户还能下载一首歌离线播放吗？
*   **播放状态**:如何用一个圆形的进度指示器显示一首歌曲正在加载？是否也要显示缓冲进度？

除非你有一个直播流，比如电台，你还需要考虑应用程序将从哪里获得歌曲。一个音乐流媒体应用程序需要从某个地方播放歌曲。

此外，你将在哪里存放音频文件？你会像 AWS 一样把它们放在云存储中，使用 SoundCloud 这样的服务，还是把歌曲托管在自己的服务器上？如果你决定在自己的服务器上托管，你有足够的带宽来处理所有的流媒体吗？如果没有，您可以考虑用内容交付网络来包装您的服务器。

与主机相关的另一个问题是版权问题。您要么需要拥有音乐的版权，要么需要获得将歌曲播放给用户的许可。Google Play 和苹果的 App Store 都不会允许提供盗版内容的应用。

除了 UI/UX 和托管相关的问题，考虑与制作音乐流媒体应用程序相关的某些技术因素也很重要，如通知、权限、处理耳机和闪避。

### 背景音频和系统通知

对于您的音乐流媒体应用程序，您几乎肯定会希望支持背景音频播放。这使得音乐可以继续播放，即使用户切换到不同的应用程序或将手机放在口袋里。

将您的音频播放器与系统连接后，用户可以在通知栏和锁定屏幕上进行 UI 控制，这样他们就可以暂停音频或跳到播放列表中的下一首歌曲。

### 许可

一个音乐流媒体应用程序从互联网上播放音乐，所以你显然需要获得访问互联网的许可。如果歌曲的网址使用 HTTP，而不是 HTTPS，你也需要一个特殊的设置。

根据您的应用程序，您可能还需要使用蓝牙、保持屏幕打开和允许前台服务的权限。您需要在应用程序的配置文件中请求这些权限。

### 处理耳机

你需要决定当用户拔掉耳机时，你的应用是应该继续播放还是暂停。

### 闪避

当用户在使用谷歌地图导航的同时从你的汽车应用程序中播放音乐时，他们可能希望导航助手说话时音乐音量降低。这叫闪避。另一方面，播客播放器通常会暂停播放。

### 从应用程序中隐藏插件(可选)

这一步不是必需的，但是你永远不知道第三方包什么时候会停止工作。Pub 上的大多数开源包都是由热心的开发人员制作的，他们自愿贡献自己的时间来帮助社区。工作量很大。

包维护者可能会变得太累、太不舒服或太忙而无法维护包。也有可能你的应用需要一个插件不支持的特性。

在这种情况下，让你的应用程序经得起未来考验的一个方法是将插件隐藏在接口后面。这将插件代码限制在一个位置，而不是分散在你的应用程序中。只有 app 知道接口，所以切换插件相对来说不太痛苦。你可以试用一个插件，如果你不喜欢，就换一个不同的插件。

创建一个界面当然不是必须的，它确实会给你的应用程序增加一层额外的复杂性。然而，现在从架构上考虑可以在将来为您省去很多麻烦。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

要创建一个接口，请使用类似如下的 API 创建一个抽象类:

```
abstract class MusicPlayer {
 Future<void> init();
 Future<void> load(String url);
 void play();
 void pause();
 void seek(Duration position);
 Stream<Duration> get position;
 Stream<Duration> get totalDuration;
 Future<void> dispose();
}

```

以下是对每种方法的解释:

*   一些 Flutter 插件需要自己初始化，所以你会在应用启动时调用这个方法
*   `load`:从一个 URL 加载一首歌曲
*   `play`:从头开始播放歌曲，或者如果歌曲先前暂停，则继续播放
*   `pause`:暂停播放歌曲
*   `seek`:改变播放位置；如果用户滑动到音频进度条上的新位置，您将调用这个函数
*   报告当前的播放位置，这样你就可以在你的进度条上制作标记动画
*   `totalDuration` stream:报告当前歌曲的长度；当您第一次加载歌曲时，此持续时间可能不可用，因此将其报告为流允许您的 UI 对当前播放状态做出反应
*   `dispose`:关闭音频播放器，释放其资源

这个 API 是极简的。在一个完整的应用程序中，你可能想要加载一个完整的播放列表，并启用其他功能，如跳到下一首歌曲、循环和随机播放。此外，您可能想要听更多的流，例如回放状态流(初始、播放、暂停、加载)和缓冲位置流。

您将为您选择的任何插件制作这个 API 的具体实现，但是您的 Flutter 小部件或状态管理代码将只知道这个接口。他们不在乎你用的是什么插件。

为了简单起见，下面的例子将`MusicPlayer`接口代码直接混合在 Flutter 小部件中。在更复杂的应用程序中，您可能希望将`MusicPlayer`调用转移到您的状态管理层，无论是 Bloc、Provider、RiverPod 还是其他。

插件初始化需要在应用程序运行之前进行。将它添加到您的`main`函数中，就像这样:

```
Future<void> main() async {
 WidgetsFlutterBinding.ensureInitialized();
 await player.init();
 runApp(const MyApp());
}

```

`player`是一个具体的`MusicPlayer`实现的实例。您可以使用`[GetIt](https://pub.dev/packages/get_it)`或某种形式的依赖注入来实例化它。

在有状态小部件的生命周期方法中处理加载和释放，就像这样:

```
@override
void initState() {
 super.initState();
 _loadSong();
}

Future<void> _loadSong() async {
 const url = 'https://www.example.com/song.mp3';
 await player.load(url);
}

@override
void dispose() {
 player.dispose();
 super.dispose();
}

```

您可以通过收听总持续时间和位置流来更新进度条:

```
StreamBuilder<Duration>(
 stream: player.totalDuration,
 builder: (context, totalDurationSnapshot) {
   return StreamBuilder<Duration>(
     stream: player.position,
     builder: (context, positionSnapshot) {
       return ProgressBar(
         progress: positionSnapshot.data ?? Duration.zero,
         total: totalDurationSnapshot.data ?? Duration.zero,
         onSeek: player.seek,
       );
     },
   );
 },
),

```

与其嵌套两个流构建器，您可能想用一个类似于`[rxdart](https://pub.dev/packages/rxdart)`的包来组合这些流，或者重新设计您的`MusicPlayer` API 来在单个流中提供位置和总持续时间。

上面代码中的`ProgressBar`小部件来自我为在自己的音频应用中使用而制作的 [audio_video_progress_bar](https://pub.dev/packages/audio_video_progress_bar) 包。

![Progress Bar](img/138c66d8f1781e7340bb297a7c719a51.png)

您也可以使用`Slider`小部件实现类似的功能。

现在你已经设置好了用户界面，你可以用任何你喜欢的音频插件来实现它。让我们仔细看看三个流行的音频流包。

## 选择音频播放器插件

有许多音频播放器包支持 Flutter 应用程序中的流媒体音乐。本文将回顾三个流行的软件包的特性和优点:Just Audio、AudioPlayers 和 Assets Audio Player。

不过，在选择软件包之前，请考虑一下本文前面讨论的面向未来的应用程序。

### 只有音频

Just Audio 是一个音频插件，可以在 Flutter 支持的所有主要平台上播放流媒体音乐。它提供了播放列表、随机播放、循环播放和许多其他内置功能。在这篇文章中评论的三个音频包中，Just Audio 在 Pub 上有最多的喜欢(在撰写本文时超过 2200 个),并且是唯一一个被指定为 Flutter Favorite 的包。

只是音频本身不支持背景音频和系统通知。然而，Just Audio 的作者还创建了另一个名为[音频服务](https://pub.dev/packages/audio_service)的包。这个包处理在后台播放音乐，并与系统通信，以在锁屏和通知区域显示 UI 控件。您仍然只使用音频，但是您将使用音频服务来包装它。我在这篇[深度教程](https://suragch.medium.com/background-audio-in-flutter-with-audio-service-and-just-audio-3cce17b4a7d)中描述了如何做到这一点。

音频服务不是最容易使用的插件，因为它需要管理音频播放器和系统通知。然而，这并不是不可能的，通过一些研究和实验，你可以让它全部工作起来。

同一个软件包作者还创建了另一个插件，名为 [Just Audio Background](https://pub.dev/packages/just_audio_background) ，它结合了 Just Audio 和音频服务。对于标准用例，这可能是您的最佳选择。如果你需要更复杂的功能，比如同时播放两个音频源，你只能分别使用音频和音频服务。

以下是你如何仅使用音频背景来实现你的`MusicPlayer` API:

```
class JustAudioPlayer implements MusicPlayer {
 final AudioPlayer _player = AudioPlayer();

 @override
 Future<void> init() async {
   await JustAudioBackground.init(
     androidNotificationChannelId: 'com.logrocket.demo.channel.audio',
     androidNotificationChannelName: 'Audio playback',
     androidNotificationOngoing: true,
   );
 }

 @override
 Future<Duration> load(String url) async {
   final source = AudioSource.uri(
     Uri.parse(url),
     tag: const MediaItem(
       id: '1',
       title: "My song",
     ),
   );
   return await _player.setAudioSource(source) ?? Duration.zero;
 }

 @override
 void play() => _player.play();

 @override
 void pause() => _player.pause();

 @override
 void seek(Duration position) => _player.seek(position);

 @override
 Stream<Duration> get position => _player.positionStream;

 @override
 Stream<Duration> get totalDuration => _player.durationStream.map(
       (duration) => duration ?? Duration.zero,
     );

 @override
 Future<void> dispose() async => await _player.dispose();
}

```

大多数方法只是将你的`MusicPlayer` API 转发到插件的音频播放器上。`init`方法为背景音频做一些注册工作。`AudioSource`是 Just Audio 用来设定当前歌曲或播放列表的工具。`MediaItem`是音频服务跟踪当前歌曲并显示在系统通知区域的方式。

除了编码工作，你还必须在 Android 和 iOS 配置文件中做一些设置。这在[文档](https://pub.dev/packages/just_audio_background)中有描述，但这里有一个总结:

*   对于 Android，编辑您的 Android 清单并请求对`INTERNET`、`WAKE_LOCK`和`FOREGROUND_SERVICE`的权限；您还需要添加服务和接收器元素
*   对于 iOS，在你的`Info.plist`文件中，将`audio`作为一个项目添加到`UIBackgroundModes`中；如果你的应用程序不需要录音，你也应该编辑你的`Podfile`，去掉一些录音 API
*   如果你的音频文件网址是 HTTP 而不是 HTTPS，你需要做一些额外的改变，因为 Android 和 iOS 默认不支持 HTTP 源

以下是一些仅使用音频、音频服务或仅使用音频背景的开源存储库:

你也不妨咨询一下 [Just Audio](https://github.com/ryanheise/just_audio/tree/minor/just_audio/example) 、 [Just Audio Background](https://github.com/ryanheise/just_audio/tree/minor/just_audio_background/example) 和 [Audio Service](https://github.com/ryanheise/audio_service/tree/master/audio_service/example) 的官方示例 app。

### 音频播放器

AudioPlayers 插件最初是另一个名为 [AudioPlayer](https://pub.dev/packages/audioplayer) 的插件的分支。然而，AudioPlayers 已经超越了它的前辈。在本文讨论的三个插件中，AudioPlayers 是最老的，它比第一个稳定版本的 Flutter 早了两年半。虽然在 Pub 上的 upvotes(截至本文撰写时超过 1，720)上仅略落后于音频，但在 Pub 神秘计算的受欢迎度指标上，它达到了 100%，超过了音频的 99%受欢迎度。

《AudioPlayers》的作者也参与了[火焰游戏引擎开发](https://blog.logrocket.com/comparing-flutter-game-engines/)。这大概解释了为什么 AudioPlayers 被嵌入到了[火焰音频插件](https://pub.dev/packages/flame_audio)中，可以用来播放颤振游戏中的声音。

和刚才的音频一样，AudioPlayers 本身并不支持背景音频。所以，如果你想让用户能够在锁屏状态下控制你的歌曲，你需要用音频服务插件来包装音频播放器。对于音频播放器来说，没有比音频背景更方便的了。

以下是为音频播放器设置音频服务的基本步骤:

*   创建一个自定义的`AudioHandler`类来包装你的 AudioPlayers 播放器
*   对于您在音频处理程序中实现的每个方法，您需要将音频服务方法转发给音频播放器，然后告诉系统您正在做什么，以便它可以更新锁屏和通知 UI
*   在`runApp`之前，初始化`main`方法中的音频处理程序
*   Android 和 iOS 的配置设置类似于前面 Just Audio 部分描述的设置；欲了解更多信息，请参见[音频服务文档](https://pub.dev/packages/audio_service)

AudioPlayers 播放器本身的方法类似于 Just Audio player。这里有一个简单的对比:

```
_player.setAudioSource(AudioSource.uri(...)); // Just Audio
_player.setSourceUrl(url);                    // AudioPlayers

_player.play();   // Just Audio
_player.resume(); // AudioPlayers

_player.pause(); // Just Audio
_player.pause(); // AudioPlayers

_player.seek(duration); // Just Audio
_player.seek(duration); // AudioPlayers

_player.dispose(); // Just Audio
_player.release(); // AudioPlayers

```

Just Audio 和 AudioPlayers 最大的区别之一就是，Just Audio 支持开箱即用的播放列表。对于音频播放器，你必须自己实现这个功能。

以下是一些使用音频播放器的开源存储库:

还可以查看一下[官方 AudioPlayer 示例 app](https://github.com/bluefireteam/audioplayers/tree/main/packages/audioplayers/example) 。

### 资产音频播放器

顾名思义，Assets Audio Player 插件不仅仅可以播放 Assets 文件夹中的音频。在它的众多功能中，它可以从一个 URL 传输音频，这意味着你也可以在音乐流媒体应用程序中使用这个插件。

资产音频播放器为播放列表、随机播放、循环播放、系统通知、蓝牙、暂停通话、音频闪避等提供内置支持。开发者显然在这个插件上投入了大量的时间。就易用性而言，它可能是本文评论的三个插件中最好的。

与音频和音频播放器相比，Assets Audio Player 插件在 Pub 上的点赞数更少(在撰写本文时接近 800 个)，但其受欢迎程度得分紧随其后。和本文中讨论的其他两个插件一样，Assets Audio Player 得到了积极的维护。

这里有一个由您的`MusicPlayer`界面包装的资产音频播放器设置:

```
class AssetsAudioPlayerPlayer implements MusicPlayer {
 final _player = AssetsAudioPlayer();

 @override
 Future<void> init() async {
   AssetsAudioPlayer.setupNotificationsOpenAction((notification) {
     return true;
   });
 }

 @override
 Future<void> load(String url) async {
   await _player.open(
     Audio.network(
       url,
       metas: Metas(title: "My song"),
     ),
     showNotification: true,
   );
 }

 @override
 void pause() => _player.pause();

 @override
 void play() => _player.play();

 @override
 Stream<Duration> get position => _player.currentPosition;

 @override
 Stream<Duration> get totalDuration => _player.current.map(
       (playing) => playing?.audio.duration ?? Duration.zero,
     );

 @override
 void seek(Duration position) => _player.seek(position);

 @override
 Future<void> dispose() => _player.dispose();
}

```

这种设置与只有音频背景的设置非常相似。然而，在 Android 和 iOS 端就没什么可做的了。Android 只需要请求`INTERNET`权限。和其他插件一样，Android 和 iOS 需要通知系统是否使用 HTTP，而不是 HTTPS 来获取 URL。

以下是一些使用 Assets Audio Player 的开源存储库:

此外，请查看官方资产音频播放器示例应用程序

## Flutter 音乐流包比较

为了帮助您做出最终决定，这里总结了 Just Audio、Audio Player 和 Assets Audio Player Flutter 音乐流软件包的优缺点。

| **颤振音乐流媒体包** | **优点** | **缺点** |
| 只有音频 | 对所有主要平台(Android、iOS、web、macOS、Windows、Linux)的坚实支持

*   坚实的支持播放本地资产和文件，以及从网址流
*   功能丰富，有一个开发良好的系统来处理播放列表
*   具有良好的关注点分离，音频回放仅由音频处理，系统通知由音频服务处理；还有一个相对容易使用的音频背景插件，它结合了两者
*   开发人员积极维护项目，并快速响应格式良好的 GitHub 问题
*   文档相当广泛(完全公开:我已经写了一些基于社区的教程)
*   支持缓存，所以你不需要保持同一首歌曲流
*   颤振最爱
*   如果你需要自己设置音频服务，学习曲线很陡

 | 只是音频背景仍处于测试阶段，没有太多的文档

*   音频播放器
*   对所有主要平台(Android、iOS、web、macOS、Windows、Linux)的坚实支持

 |
| 坚实的支持播放本地资产和文件，以及从网址流 | 悠久的发展历史和持续的发展势头；该软件包作为火焰游戏引擎的音频播放器的采用是对其长期稳定性的信任投票

*   提供播放短音频剪辑的低延迟模式，这在游戏中非常有用
*   强大的社区支持
*   没有内置的播放列表支持，这意味着没有洗牌和循环；用户必须自己编写这个逻辑
*   没有对背景音频和系统通知的内置支持；用户必须用相对难以使用的音频服务插件来实现这一点
*   关于更高级主题的文档有些缺乏

 | 资产音频播放器

*   支持 Android、iOS、web 和 macOS
*   坚实的支持播放本地资产和文件，以及从网址流
*   提供许多内置功能

 |
| 支持带随机播放和循环播放的播放列表 | 提供内置背景音频和系统通知

*   相对容易使用
*   名字有点误导；该插件不仅限于资产
*   目前不支持 Windows 和 Linux
*   缺少一些总体的整洁(例如，API 文档在一个案例中已经过期，发布者还没有在 Pub 上验证，GitHub 问题没有得到响应，示例项目有些混乱，注释掉的代码还没有被删除)
*   结论
*   如果你想建立一个能播放音乐的 Flutter 应用程序，这里提到的三个音频播放器插件中的任何一个都可以完成这项工作。作为一个简单的开始，考虑资产音频播放器或只是音频的只是音频背景版本。如果你想对正在发生的事情有更多的控制，尝试用音频服务来包装音频或音频播放器。然而，如果你比较纯音频和音频播放器，纯音频更适合音乐流媒体应用程序，因为它提供了对播放列表的内置支持。

 | 在写这篇文章的时候，我说服自己用一个接口包装音频插件的好处。在测试过程中，我可以通过修改一行代码在插件实现之间切换。

*   制作一个音乐流媒体应用程序可能很有挑战性。如果你卡住了，研究一下插件 repo 中的示例应用程序是如何工作的。只要有一点毅力，你就会得到它。
*   现在，出去做点音乐吧！
*   使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

 |

## [LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)

Now, get out there and make some music!

## Cut through the noise of traditional error reporting with [LogRocket](https://lp.logrocket.com/blg/signup)

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) is a digital experience analytics solution that shields you from the hundreds of false-positive errors alerts to just a few truly important items. LogRocket tells you the most impactful bugs and UX issues actually impacting users in your applications.

Then, use session replay with deep technical telemetry to see exactly what the user saw and what caused the problem, as if you were looking over their shoulder.

LogRocket automatically aggregates client side errors, JS exceptions, frontend performance metrics, and user interactions. Then LogRocket uses machine learning to tell you which problems are affecting the most users and provides the context you need to fix it.

Focus on the bugs that matter — [try LogRocket today](https://lp.logrocket.com/blg/signup-issue-free).