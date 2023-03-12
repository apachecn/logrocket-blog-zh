# 用 React 和 ts-audio - LogRocket 博客构建一个 Spotify 克隆

> 原文：<https://blog.logrocket.com/build-spotify-clone-react-ts-audio/>

音乐播放器是允许您收听音频文件和录音的设备或应用程序。有许多可用的音乐播放器，但在本文中，我们将使用 React 和 [ts-audio](https://www.npmjs.com/package/ts-audio) 构建一个[流行音乐流媒体服务 Spotify](https://www.spotify.com/us/) 的克隆。

你可能认为本教程会使用 Spotify API，然而，Spotify 和其他音乐数据库并没有在它们的响应体中提供流媒体链接或 URL。Spotify API 确实提供了一个预览 URL，但是歌曲的时长仅限于 30 秒，这对于我们的例子来说是不够的。因此，我们不会使用 Spotify API 或向任何音乐 API 或数据库发出任何请求。

相反，我们将使用由歌曲和图片组成的虚拟数据。但是，如果您尝试使用一个带有可流式链接的 API，您也可以应用本文中使用的方法。你可以在 GitHub repo 找到本教程的完整代码[。我们开始吧！](https://github.com/iamfortune/Spotify-clone-with-Ts-audio)

## 什么是 ts-audio？

ts-audio 是一个不可知的库，它使得 [`AudioContext` API](https://developer.mozilla.org/en-US/docs/Web/API/AudioContext) 更容易交互。ts-audio 为您提供了播放、暂停等方法，并允许您创建播放列表。ts-audio 提供以下功能:

*   包括一个简单的 API，它抽象了`AudioContext` API 的复杂性
*   提供跨浏览器支持
*   轻松创建音频播放列表
*   适用于任何编译成 JavaScript 的语言

## 用 ts-audio 构建 Spotify 克隆

让我们用下面的命令创建一个新的 React 应用程序:

```
npx create-react-app ts-audio

```

如果您使用 Yarn，运行下面的命令:

```
yarn create react-app ts-audio

```

对于教程的其余部分，我将使用纱线。接下来，我们安装 ts-audio 包，如下所示:

```
yarn add ts-audio

```

在其核心，ts-audio 有两个组件，`Audio`和`AudioPlaylist`。组件是我们可以用特定参数调用的函数。

### 使用`Audio`组件

组件允许我们传入一首要播放的歌曲。它还为我们提供了某些方法，如`play()`、`pause()`、`stop()`等等:

```
// App.js

import Audio from 'ts-audio';
import Lazarus from './music/Lazarus.mp3';

export default function App() {
  const audio = Audio({
    file: Lazarus
  })

  const play = () => {
    audio.play()
  }

    const pause = () => {
    audio.pause()
  }

    const stop = () => {
    audio.stop()
  }

  return (
    <>
      <button onClick={play}>Play</button>
      <button onClick={pause}>Pause</button>
      <button onClick={stop}>Stop</button>
    </>
  )
}

```

在上面的代码块中，我们从 ts-audio 导入了`Audio`组件和我们想要播放的歌曲。我们创建了一个音频实例，将其设置为导入的`Audio`组件，然后将导入的音乐传递给由`Audio`元素公开的文件参数。

我们利用了 ts-audio 提供给我们的方法，比如`play()`和`pause()`，然后通过函数将它们传递给按钮。

### 使用`AudioPlaylist`组件

`AudioPlaylist`组件允许我们传入多首歌曲，但它们必须在一个数组中，否则 ts-audio 不会播放它们。`AudioPlaylist`组件为我们提供了`play()`、`pause()`、`stop()`、`next()`和`prev()`等方法。

下面的代码块是如何使用`AudioPlaylist`组件的例子:

```
// App.js

import { AudioPlaylist } from 'ts-audio';
import Lazarus from './music/Lazarus.mp3';
import Sia from './music/Sia - Bird Set Free.mp3';

export default function App() {
  const playlist = AudioPlaylist({
    files: [Lazarus, Sia]
  })

  const play = () => {
    playlist.play()
  }

  const pause = () => {
    playlist.pause()
  }

  const next = () => {
    playlist.next()
  }

  const previous = () => {
    playlist.prev()
  }

  const stop = () => {
    playlist.stop()
  }

  return (
    <>
      <button onClick={play}>Play</button>
      <button onClick={pause}>Pause</button>
      <button onClick={next}>Next</button>
      <button onClick={prev}>Prev</button>
      <button onClick={stop}>Stop</button>
    </>
  )
}

```

音乐播放器将具有以下功能:

*   每当我们点击**下一首**或**上一首**时，将艺术家更改为当前歌曲的艺术家
*   将图像更改为当前歌曲的图像
*   将歌曲标题更改为当前歌曲

在`src`文件夹中，分别创建两个名为`images`和`music`的文件夹。导航到`images`文件夹，粘贴您可能需要的任何照片。在`music`文件夹中，你可以粘贴任何你想要使用的音频文件。

在下面的 GitHub repos 中，你可以获得本教程使用的[图片文件](https://github.com/iamfortune/Spotify-clone-with-Ts-audio/tree/main/src/images)和[获取音频文件](https://github.com/iamfortune/Spotify-clone-with-Ts-audio/tree/main/src/images)。接下来，将歌曲和图像导入`App.js`，如下所示:

```
import { AudioPlaylist } from 'ts-audio';

// Music import
import Eyes from './music/01\. Jon Bellion - Eyes To The Sky.mp3';
import Mood from './music/24kGoldn-Mood-Official-Audio-ft.-Iann-Dior.mp3';
import Audio from './music/audio.mp3';
import Broken from './music/Cant Be Broken .mp3';
import Lazarus from './music/Lazarus.mp3';
import Sia from './music/Sia - Bird Set Free.mp3';
import Nobody from './music/T-Classic-Nobody-Fine-Pass-You.mp3';
import Yosemite from './music/Yosemite.mp3';

// Pictures import
import EyesImg from './images/Eyes to the sky.jpeg';
import MoodImg from './images/mood.jpeg';
import AudioImg from './images/lana.jpeg';
import BrokenImg from './images/lil wayne.jpeg';
import LazarusImg from './images/dave.jpeg';
import SiaImg from './images/sia.jpeg';
import NobodyImg from './images/nobody.jpeg';
import YosemiteImg from './images/travis.jpeg';

export default function App() {
  const songs =  [
      {
        title: 'Eyes to the sky',
        artist: 'Jon Bellion',
        img_src: EyesImg,
        src: Eyes,
      },
      {
        title: 'Lazarus',
        artist: 'Dave',
        img_src: LazarusImg,
        src: Lazarus,
      },
      {
        title: 'Yosemite',
        artist: 'Travis scott',
        img_src: YosemiteImg,
        src: Yosemite,
      },
      {
        title: 'Bird set free',
        artist: 'Sia',
        img_src: SiaImg,
        src: Sia,
      },
      {
        title: 'Cant be broken',
        artist: 'Lil wayne',
        img_src: BrokenImg,
        src: Broken,
      },
      {
        title: 'Mood',
        artist: '24kGoldn',
        img_src: MoodImg,
        src: Mood,
      },
      {
        title: 'Nobody fine pass you',
        artist: 'T-Classic',
        img_src: NobodyImg,
        src: Nobody,
      },
      {
        title: 'Dark paradise',
        artist: 'Lana Del Ray',
        img_src: AudioImg,
        src: Audio,
      },
    ]

  const playlist = AudioPlaylist({
      files: songs.map((song) => song.src),
    });

  const handlePlay = () => {
    playlist.play();
  };

  const handlePause = () => {
    playlist.pause();
  };

  const handleSkip = () => {
    playlist.next();
  };

  const handlePrevious = () => {
    playlist.prev();
  };

  return (
    <>
      <button onClick={handlePlay}>Play</button>
      <button onClick={handlePause}>Pause</button>
      <button onClick={handleSkip}>Next</button>
      <button onClick={handlePrevious}>Prev</button>     
    </>
  );
}

```

在上面的代码块中，我们导入了歌曲和图像。接下来，我们创建了一个包含对象的歌曲数组。每个对象都有一个用于导入图像的`title`、`artist`、`img_src`，以及用于导入歌曲的`src`。

之后，我们通过 song 数组映射到歌曲的`src`，并将其传递到 files 参数中。记住，我们必须把它作为一个数组传入，但是然后`map()`方法通过调用一个函数来创建一个新的数组。因此，我们可以将它传递给`files`参数。

我们还创建了自己的方法，并将它们传递给各个按钮。我们将创建一个`Player.js`文件来处理按钮，同时处理`App.js`中的功能:

```
// Player.js

export default function Player({ play, pause, next, prev }) {
  return (
    <div className="c-player--controls">
      <button onClick={play}>Play</button>
      <button onClick={pause}>Pause</button>
      <button onClick={next}>Next</button>
      <button onClick={prev}>Previous</button> 
    </div>
  );
}

```

在上面的代码块中，我们创建了一个`Player.js`文件，然后捕捉来自`App.js`的道具，最后将它们传递到按钮中。

### 创建功能

为了创建应用程序的功能，我们导入`useState`来获取歌曲的当前索引。然后，我们将图像设置为当前照片，将艺术家设置为当前艺术家，将标题设置为当前标题:

```
// App.js

import React, { useState } from 'react';
import Player from './Player';
import { AudioPlaylist } from 'ts-audio';
// Music import

// Pictures import

export default function App() {
  const [currentSong, setCurrentSong] = useState(0);
  const [isPlaying, setIsPlaying] = useState(false);

  // Songs Array

  const playlist =AudioPlaylist({
      files: songs.map((song) => song.src),
    });

  const handlePlay = () => {
    playlist.play();
    setIsPlaying(true);
  };

  const handlePause = () => {
    playlist.pause();
    setIsPlaying(false);
  };

  const handleSkip = () => {
    playlist.next();
    setIsPlaying(true);
    setCurrentSong(
      (currentSong) => (currentSong + 1 + songs.length) % songs.length
    );
  };

  const handlePrevious = () => {
    playlist.prev();
    setIsPlaying(true);
    setCurrentSong(
      (currentSong) => (currentSong - 1 + songs.length) % songs.length
    );
  };
  return (
    <>
      <div className="App">
        <div className="c-player">
          <div className="c-player--details">
            {' '}
            <div className="details-img">
              {' '}
              <img src={songs[currentSong].img_src} alt="img" />
            </div>
            <h1 className="details-title">{songs[currentSong].title}</h1>
            <h2 className="details-artist">{songs[currentSong].artist}</h2>
          </div>
          <Player
            play={handlePlay}
            pause={handlePause}
            isPlaying={isPlaying}
            setIsPlaying={setIsPlaying}
            next={handleSkip}
            prev={handlePrevious}
          />
        </div>
      </div>
    </>
  );
}

```

我们创建了一个状态事件，并将其设置为零。当我们点击**下一个**按钮时，我们将状态设置为当前状态的余数 1 和歌曲长度之和除以歌曲长度:

```
currentSong + 1 + songs.length) % songs.length
```

当我们点击**上一首**按钮时，我们将状态设置为当前歌曲的剩余部分，减去 1，加上歌曲的长度除以歌曲的长度:

```
currentSong - 1 + songs.length) % songs.length
```

我们还创建了一个状态事件来检查歌曲是否正在播放，然后我们将它作为道具传递给`Player`组件。最后，我们处理了更改图像、艺术家和歌名的功能。

当我们启动应用程序时，一切似乎都正常；点击**下一个**按钮时，图像会发生变化。但是，播放的歌曲与屏幕上显示的图片和艺术家姓名不匹配。有时，两首或多首歌曲会同时播放。

## 解决问题:不匹配的歌曲细节

当我们点击**下一个**或**上一个**按钮时，我们正在重新计算值并有效地导致重新渲染。为了停止这种情况，我们将歌曲数组和创建的播放列表实例包装在一个`useMemo`钩子中，如下所示:

```
// App.js

import React, { useState, useMemo } from 'react';
import Player from './Player';
import { AudioPlaylist } from 'ts-audio';
// Music import

// Pictures import

export default function App() {
  const [currentSong, setCurrentSong] = useState(0);

  const songs = useMemo(
    () => [
      {
        title: 'Eyes to the sky',
        artist: 'Jon Bellion',
        img_src: EyesImg,
        src: Eyes,
      },
      {
        title: 'Lazarus',
        artist: 'Dave',
        img_src: LazarusImg,
        src: Lazarus,
      },
      {
        title: 'Yosemite',
        artist: 'Travis scott',
        img_src: YosemiteImg,
        src: Yosemite,
      },
      {
        title: 'Bird set free',
        artist: 'Sia',
        img_src: SiaImg,
        src: Sia,
      },
      {
        title: 'Cant be broken',
        artist: 'Lil wayne',
        img_src: BrokenImg,
        src: Broken,
      },
      {
        title: 'Mood',
        artist: '24kGoldn',
        img_src: MoodImg,
        src: Mood,
      },
      {
        title: 'Nobody fine pass you',
        artist: 'T-Classic',
        img_src: NobodyImg,
        src: Nobody,
      },
      {
        title: 'Dark paradise',
        artist: 'Lana Del Ray',
        img_src: AudioImg,
        src: Audio,
      },
    ],
    []
  );

  const playlist = useMemo(() => {
    return AudioPlaylist({
      files: songs.map((song) => song.src),
    });
  }, [songs]);

```

`useMemo`钩子有效地缓存了这个值，这样就不需要重新计算，也就不会导致重新渲染。

## 添加样式

在本教程中，我们将使用[字体中的图标。您可以使用下面的命令安装 Font Awesome 软件包:](https://fontawesome.com/v5/docs/web/use-with/react)

```
yarn add @fortawesome/fontawesome-svg-core
yarn add @fortawesome/free-solid-svg-icons
yarn add @fortawesome/react-fontawesome

```

将以下代码复制并粘贴到`Player.js`文件中:

```
// Player.js

import { FontAwesomeIcon } from '@fortawesome/react-fontawesome';
import { faPlay, faPause, faForward, faBackward } from '@fortawesome/free-solid-svg-icons';
export default function Player({ play, pause, next, prev, isPlaying, setIsPlaying }) {
  return (
    <div className="c-player--controls">
      <button className="skip-btn" onClick={prev}>
        <FontAwesomeIcon icon={faBackward} />
      </button>
      <button
        className="play-btn"
        onClick={() => setIsPlaying(!isPlaying ? play : pause)}
      >
        <FontAwesomeIcon icon={isPlaying ? faPause : faPlay} />
      </button>
      <button className="skip-btn" onClick={next}>
        <FontAwesomeIcon icon={faForward} />
      </button>
    </div>
  );
}

```

在上面的代码块中，我们从`App.js`文件中获取道具，然后在`Player.js`文件中处理它们。对于样式，将下面的代码复制并粘贴到您的`index.css`文件中:

```
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: 'Fira Sans', sans-serif;
}
body {
  background-color: #ddd;
}
.App {
  display: flex;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
  max-width: 100vw;
}
.c-player {
  display: block;
  background-color: #0a54aa;
  max-width: 400px;
  display: block;
  margin: 0px auto;
  padding: 50px;
  border-radius: 16px;
  box-shadow: inset -6px -6px 12px rgba(0, 0, 0, 0.8),
    inset 6px 6px 12px rgba(255, 255, 255, 0.4);
}
.c-player > h4 {
  color: #fff;
  font-size: 14px;
  text-transform: uppercase;
  font-weight: 500;
  text-align: center;
}
.c-player > p {
  color: #aaa;
  font-size: 14px;
  text-align: center;
  font-weight: 600;
}
.c-player > p span {
  font-weight: 400;
}
.c-player--details .details-img {
  position: relative;
  width: fit-content;
  margin: 0 auto;
}
.c-player--details .details-img img {
  display: block;
  margin: 50px auto;
  width: 100%;
  max-width: 250px;
  border-radius: 50%;
  box-shadow: 6px 6px 12px rgba(0, 0, 0, 0.8),
    -6px -6px 12px rgba(255, 255, 255, 0.4);
}
.c-player--details .details-img:after {
  content: '';
  display: block;
  position: absolute;
  top: -25px;
  left: -25px;
  right: -25px;
  bottom: -25px;
  border-radius: 50%;
  border: 3px dashed rgb(255, 0, 0);
}
.c-player--details .details-title {
  color: #eee;
  font-size: 28px;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.8),
    -2px -2px 4px rgba(255, 255, 255, 0.4);
  text-align: center;
  margin-bottom: 10px;
}
.c-player--details .details-artist {
  color: #aaa;
  font-size: 20px;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.8),
    -2px -2px 4px rgba(255, 255, 255, 0.4);
  text-align: center;
  margin-bottom: 20px;
}
.c-player--controls {
  display: flex;
  align-items: center;
  justify-content: center;
  margin-bottom: 30px;
}
.c-player--controls .play-btn {
  display: flex;
  margin: 0 30px;
  padding: 20px;
  border-radius: 50%;
  box-shadow: 4px 4px 10px rgba(0, 0, 0, 0.8),
    -4px -4px 10px rgba(255, 255, 255, 0.4),
    inset -4px -4px 10px rgba(0, 0, 0, 0.4),
    inset 4px 4px 10px rgba(255, 255, 255, 0.4);
  border: none;
  outline: none;
  background-color: #ff0000;
  color: #fff;
  font-size: 24px;
  cursor: pointer;
}
.c-player--controls .skip-btn {
  background: none;
  border: none;
  outline: none;
  cursor: pointer;
  color: rgb(77, 148, 59);
  font-size: 18px;
}

```

## 结论

在本文中，我们已经了解了 ts-audio，这是一个与`AudioContext` API 一起工作的不可知的、易于使用的库。我们了解了 ts-audio 的方法，以及它如何使处理音频文件变得更加容易。最后，我们学习了如何使用 ts-audio 构建一个可工作的音乐播放器。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)