# React - LogRocket 博客视频播放综合指南

> 原文：<https://blog.logrocket.com/a-comprehensive-guide-to-video-playback-in-react/>

在思科最近的一项研究中，预计到 2022 年，82%的消费者互联网流量将采用视频内容的形式，比 2017 年高出 15 倍。了解支撑视频播放的技术以及如何最好地利用它来提供最佳体验，从来没有像现在这样重要。

在这篇文章中，我们将在重点介绍一些最流行的 JavaScript 视频播放器框架、它们的特性以及如何在 React 中实现它们之前，先浏览一下视频播放的基础知识。

### 视频播放基础

为了理解视频回放，首先理解用户点击播放按钮和正在播放的视频的第一帧之间涉及的过程是很重要的。在这两点之间有相当多的步骤可能出错，最终会影响您的视频性能，或者在更糟糕的情况下，意味着您的视频根本无法播放。

除非您的视频文件非常小，否则您的视频播放器不太可能在播放前一次性下载整个文件。

这将最终导致消费者在看到任何内容之前等待相当长的时间，这可能导致他们变得不耐烦并离开视频。

一次性下载整个视频的过程通常被称为单源播放，大多数情况下不建议这样做。

相反，视频内容通常被分成称为“块”的片段，视频播放器连续下载这些片段，以确保设备在任何给定时间只下载小块数据。

除了被分割成小块的数据之外，通常还有多个级别的视频质量可供播放器使用，以便客户能够选择更适合其网络条件的比特率。

如果你曾经见过可怕的缓冲符号，那是因为视频播放器未能及时降低比特率，播放器试图下载每个块。

客户端在不同质量级别的视频之间切换并尝试下载块的过程称为自适应比特率流(ABS ),在讨论视频回放时理解这一点非常重要。

MSS、HLS 和 DASH 是三种不同的技术，用于实现有效的 ABS。

## 手写本

MSS (Microsoft Smooth Streaming)由微软标准化，并随着 Silverlight 播放器的引入而首次出现。

MSS 以一系列小块的形式下载视频，这些小块缓存在网络边缘，这意味着客户端请求和接收这些小块的事务可以更快地发生。

为了开始播放，视频播放器从服务器请求一个清单文件，该文件展示了关于所请求的视频的细节，例如视频的持续时间、每个块的位置以及播放器可用的比特率。

以下是 MSS 清单示例，其中一些关键信息标有:

```
<SmoothStreamingMedia
  MajorVersion="2"
  MinorVersion="1"
  Duration="1209510000"> // Total video duration

  <!-- Video information -->
  <StreamIndex
    Type="video"
    Name="video"
    Chunks="4" // Number of chunks
    QualityLevels="4" // Number of bitrates available
    MaxWidth="1280"
    MaxHeight="720"
    DisplayWidth="1280"
    DisplayHeight="720"
    Url="QualityLevels({bitrate})/Fragments(video={start time})"> // URL template to request chunks

    <!-- Quality levels -->
    <QualityLevel
      Index="0"
      Bitrate="2962000"
      MaxWidth="1280"
      MaxHeight="720" />
    <QualityLevel
      Index="1"
      Bitrate="2056000"
      MaxWidth="992"
      MaxHeight="560" />
    <QualityLevel
      Index="2"
      Bitrate="1427000"
      MaxWidth="768"
      MaxHeight="432" />
    <QualityLevel
      Index="3"
      Bitrate="991000"
      MaxWidth="592"
      MaxHeight="332" />
    <QualityLevel
      Index="4"
      Bitrate="688000"
      MaxWidth="448"
      MaxHeight="252" />

    <!-- Chunks -->
    <c d="20020000" /> // Chunk duration
    <c d="20020000" />
    <c d="20020000" />
    <c d="20020000" />
  </StreamIndex>
</SmoothStreamingMedia>
```

MSS 是微软平台上最常用的 ABS 技术之一，旨在提供最小的缓冲和快速的启动时间。

### HLS

HLS 代表 HTTP Live Streaming，由苹果公司在 2009 年随着 iPhone 3 的发布而开发，作为流行的 Adobe flash 格式的替代方案。

HLS 将视频分成 10 秒钟的小段，索引在一个单独的播放列表文件中，是苹果 iOS 和 OSX 设备上唯一支持的标准。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

HLS 播放列表示例如下:

```
#EXTM3U
#EXT-X-PLAYLIST-TYPE:VOD
<!-- Video duration -->
#EXT-X-TARGETDURATION:10
#EXT-X-VERSION:4
#EXT-X-MEDIA-SEQUENCE:0
<!-- Chunk duration (10 seconds) -->
#EXTINF:10.0,
<!-- URL to request chunk -->
http://example.com/movie1/fileSequenceA.ts
#EXTINF:10.0,
http://example.com/movie1/fileSequenceB.ts
#EXTINF:10.0,
http://example.com/movie1/fileSequenceC.ts
#EXTINF:9.0,
http://example.com/movie1/fileSequenceD.ts
#EXT-X-ENDLIST
```

HLS 的主要好处是它是苹果设备唯一的原生 ABS 格式。如果你想在 iOS 设备上实现视频播放，HLS 是你唯一的选择。

### 破折号

HTTP 上的动态自适应流(DASH)是一项相当新的技术，旨在为所有设备提供支持，避免为同一视频源实施 MSS 和 HLS 等多种技术带来的不必要的复杂性。

在 2009 年 MPEG 征集提案后，包括谷歌和微软在内的 50 多家大公司合作制定了 MPEG-DASH 标准，并最终于 2012 年发布。

本质上，DASH 的目标是将当前所有的技术结合成一个，提供每个标准的优点并减少技术上的麻烦。通常，DASH 视频被分割成 2-4 秒的视频块，从而允许更快的视频下载并最终获得更好的性能。

仪表板清单样本:

```
<MPD  profiles="urn:mpeg:dash:profile:full:2011">
    <Period duration="PT10M"> // Period Duration
        <BaseURL>main/</BaseURL>
        <AdaptationSet mimeType="video/mp2t">
            <BaseURL>video/</BaseURL>
            <!-- 1st Bitrate -->
            <Representation id="720p" bandwidth="3200000" width="1280" height="720">
                <BaseURL>720p/</BaseURL>
                <!-- Chunk List -->
                <SegmentList timescale="90000" duration="5400000">
                    <RepresentationIndex sourceURL="representation-index.sidx"/>
                    <SegmentURL media="segment-1.ts"/>
                    <SegmentURL media="segment-2.ts"/>
                    <SegmentURL media="segment-3.ts"/>
                    <SegmentURL media="segment-4.ts"/>
                    <SegmentURL media="segment-5.ts"/>
                    <SegmentURL media="segment-6.ts"/>
                    <SegmentURL media="segment-7.ts"/>
                    <SegmentURL media="segment-8.ts"/>
                    <SegmentURL media="segment-9.ts"/>
                    <SegmentURL media="segment-10.ts"/>
                </SegmentList>
            </Representation>
            <!-- 2nd Bitrate -->
            <Representation id="1080p" bandwidth="6800000" width="1920" height="1080">
                <BaseURL>1080/</BaseURL>
                <!-- Chunk List -->
                <SegmentList timescale="90000" duration="5400000"> // Segment duration
                    <RepresentationIndex sourceURL="representation-index.sidx"/>
                    <SegmentURL media="segmentl-1.ts"/>
                    <SegmentURL media="segmentl-2.ts"/>
                    <SegmentURL media="segmentl-3.ts"/>
                    <SegmentURL media="segmentl-4.ts"/>
                    <SegmentURL media="segmentl-5.ts"/>
                    <SegmentURL media="segmentl-6.ts"/>
                    <SegmentURL media="segmentl-7.ts"/>
                    <SegmentURL media="segmentl-8.ts"/>
                    <SegmentURL media="segmentl-9.ts"/>
                    <SegmentURL media="segmentl-10.ts"/>
                </SegmentList>
            </Representation>
        </AdaptationSet>
    </Period>
</MPD>
```

## 视频播放器框架

现在，您已经了解了不同的 ABS 技术，我们可以看看可用于适应它们的各种 JavaScript 播放器框架。

虽然行业似乎正在向 DASH 发展，但它仍然是一项相对较新的技术，运行 MSS 和/或 HLS 的系统仍然存在于我们的视频基础设施中，特别是在 HLS 是唯一选项的苹果设备上。

让我们看看一些最流行的 JavaScript 视频播放器框架，它们支持的 ABS 技术，以及如何在 React 中实现它们。

### 视频. js

我们要研究的第一项技术可以说是当今最流行的开源视频播放器框架之一。Video.js 是在 2010 年从零开始构建的，并考虑到了 Javascript 世界，全球约有 500，000 个网站使用它。

其主要卖点在于，旨在支持所有类型的视频格式，包括自适应视频格式，如 HLS 和 DASH。

Video.js 被 Tumblr 和 LinkedIn 等公司使用，它易于设计风格，跨功能，易于实现。但是它在 React 世界中是怎样的呢？

幸运的是，在 React 中实现 Video.js 并不太困难，几分钟内就可以启动并运行。首先，你需要点击[这里的](https://videojs.com/getting-started/)来下载 Video.js 或者使用 npm 和`npm i video.js`来导入它。

在这里，Video.js 播放器需要在`componentDidMount`上进行实例化，然后才能使用:

```
import React from 'react';
import videojs from 'video.js'
export default class VideoPlayer extends React.Component {
  componentDidMount() {
    this.player = videojs(this.videoNode, this.props, function onPlayerReady() {
      console.log('Video.js Ready', this)
    });
  }
  componentWillUnmount() {
    if (this.player) {
      this.player.dispose()
    }
  }
  render() {
    return (
      <div> 
        <div data-vjs-player>
          <video ref={ node => this.videoNode = node } className="video-js"></video>
        </div>
      </div>
    )
  }
}
```

您可能会注意到播放器被包装在一个`data-vjs-player` div 中。这是一个额外的步骤，确保不会在 DOM 中创建额外的包装器。一旦创建了组件，就可以用各种选项来触发它以开始播放。

Video.js 可用选项的完整列表可以在[这里](https://docs.videojs.com/tutorial-options.html)找到。

```
const videoJsOptions = {
  autoplay: true,
  controls: true,
  sources: [{
    src: '/path/to/video.mp4',
    type: 'video/mp4'
  }]
}
return <VideoPlayer { ...videoJsOptions } />
```

### HLS.js

Video.js 为播放 HLS 内容提供了一个解决方案，但是作为处理 Apple 格式的更好的选择之一，一个正在获得关注的播放器框架是 HLS.js。

这项技术于 2015 年发布，现已在包括 Twitter 和纽约时报在内的数千家知名网站上投入使用。

HLS.js 的吸引力在于其实现的简单性和微小的占用空间(它只有本文提到的其他播放器的一半大小，只有 71.1KB)。

依靠 HTML5 视频和媒体源扩展来实现回放，您可以在浏览器中快速高效地提供可靠的 HLS 回放。

在 React 中实现 HLS.js 的第一步是下载[最新的库](https://github.com/video-dev/hls.js)，并将其包含在 React 项目中，或者使用`npm i hls.js`从 npm 安装。

从那里，我们在渲染函数中创建一个标准的`<video>`标签。

我们还将为播放器提供一个引用`this.player`,以便我们稍后可以使用它来实例化我们的播放器框架:

```
import React from "react";
import Hls from "hls.js";
export default class VideoPlayer extends React.Component {
  state = {};
  render() {
    return (
          <video
            className="videoCanvas"
            ref={player => (this.player = player)}
            autoPlay={true}
          />
    );
  }
}
```

一旦视频标签就位，在 HLS.js 中呈现视频就像更新我们的`componentDidUpdate`来附加媒体一样简单:

```
import React from "react";
import Hls from "hls.js";
export default class VideoPlayer extends React.Component {
  state = {};
  componentDidUpdate() {
      const video = this.player;
      const hls = new Hls();
      const url = "https://bitdash-a.akamaihd.net/content/sintel/hls/playlist.m3u8";

      hls.loadSource(url);
      hls.attachMedia(video);
      hls.on(Hls.Events.MANIFEST_PARSED, function() { video.play(); });
  }
  render() {
    return (
          <video
            className="videoCanvas"
            ref={player => (this.player = player)}
            autoPlay={true}
          />
    );
  }
}
```

这里你可以看到我们的`componentDidUpdate`正在加载源码流，将它附加到视频播放器，并附加一个监听器来显示事件，以便知道何时播放视频。

HLS.js 的完整文档，以及回放期间可以触发的许多事件，可以在[这里](https://github.com/video-dev/hls.js/blob/master/docs/API.md)找到。

### DASH.js

DASH.js 由 DASH 行业论坛创建，旨在设计一个视频播放器框架，允许开发人员使用日益流行的 MPEG-DASH 格式构建视频播放器，这种格式与浏览器无关，并且在生产环境中非常健壮。

实现 DASH.js 视频播放器框架的第一步是将该库导入到您的项目中，最新的库可以在[这里](https://cdn.dashjs.org/latest/dash.all.min.js)找到。

和我们的其他玩家一样，你需要创建你的`<video>`标签并把它放在你的渲染函数中。这就是 DASH.js 渲染视频的目标。

我们将再次给 video 标签一个引用`this.player`,这样我们稍后可以用它来初始化 DASH.js:

```
import React from "react";
import * as dashjs from "dash.js";
export default class VideoPlayer extends React.Component {
  state = {};
  render() {
    return (
          <video
            ref={player => (this.player = player)}
            autoPlay={true} 
          />
    );
  }
}
```

实现播放的最后一步是在`componentDidUpdate`上实例化您的播放器，并提供您的目标 URL:

```
import React from "react";
import * as dashjs from "dash.js";
export default class VideoPlayer extends React.Component {
  state = {};
  componentDidUpdate() {
      const url = "https://dash.akamaized.net/envivio/EnvivioDash3/manifest.mpd";
      const video = this.player;
      const dashjs = dashjs.MediaPlayer().create();
      dashjs.initialize(video, url, true);
  }
  render() {
    return (
          <video 
            ref={player => (this.player = player)}
            autoPlay={true} 
          />
    );
  }
}
```

此时，您应该会看到一些视频回放！DASH.js 有许多可用的设置、事件和定制选项，可以在它们的文档中找到[。](http://cdn.dashjs.org/latest/jsdoc/index.html)

## 结论

在这篇文章中，我们已经了解了视频播放的基本知识，以及一旦用户点击播放按钮，在幕后会发生什么。我们已经讨论了三种最流行的 ABS 格式以及每种格式的应用。

最后，在介绍 React 中的各种实现之前，我们回顾了一些流行的 Javascript 视频播放器框架。

随着视频成为绝大多数互联网用户的头号信息消费方式，现在是开始探索视频播放精彩世界的最佳时机。

我希望这篇文章能让你下次在你最喜欢的流媒体服务上看到那个缓冲图标时有所思考，并激发你开始使用 React 中的一个视频播放器框架创建自己的视频播放器。

对于进一步的阅读，我推荐 [Bitmovins 关于自适应比特率流的文章](https://bitmovin.com/adaptive-streaming/)，该文章深入探讨了 ABS 如何适应客户端的网络条件。

## [LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。