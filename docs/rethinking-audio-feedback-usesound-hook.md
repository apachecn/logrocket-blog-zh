# 用 useSound Hook 反思网络音频反馈

> 原文：<https://blog.logrocket.com/rethinking-audio-feedback-usesound-hook/>

从历史上看，网络上的声音受到了不好的指责——这是理所当然的。它们可能会让用户感到不和谐、分散注意力，有时还会让用户感到吃惊。然而，在应用程序中正确使用声音可以为最终用户提供有用的线索，从整体上丰富用户交互。

声音可以用来强调特定的用户动作和重要的反馈。如果处理得当，声音可以给枯燥的用户体验带来活力。声音可以丰富用户体验的用例有很多。游戏和移动应用程序可能会立即浮现在脑海中，但网络也可以从这种丰富的用户体验中受益。

要记住的一条黄金法则是可访问性，稍后我们将更详细地探讨这一点。用户必须能够选择退出，没有明确的用户同意，声音永远不会自动播放。考虑到这一点，可能性是无穷的。

考虑重要的通知，当用户离开标签或浏览器时聊天中的新消息，等等。这就是`[useSound](https://github.com/joshwcomeau/use-sound)`钩子真正有用的地方。它有助于将声音无缝集成到基于 React 的 UI 中。

## 概观

`useSound`是一个 React 挂钩，允许您轻松地将声音添加到 React 项目中。它为大多数常见用例提供了许多选项。它还扩展了 howler.js 库，使您能够扩展它已经提供的功能。

大约 1KB 的 gzipped，异步加载大约 10KB 的 howler.js，它足够小，不会显著影响应用程序的性能。根据 [a](https://joshwcomeau.com/react/announcing-use-sound-react-hook/) [n](https://joshwcomeau.com/react/announcing-use-sound-react-hook/) [发布的博客](https://joshwcomeau.com/react/announcing-use-sound-react-hook/)，你可以获得以下开箱即用的功能，以及更多功能:

*   过早停止声音，或暂停/恢复声音
*   加载一个音频精灵，并把它分成许多单独的声音
*   调整播放速度来加快/减慢声音
*   大量的事件监听器
*   howler.js 让许多其他先进的东西成为可能

## 入门指南

### 装置

软件包可以通过`yarn`或`npm`安装:

```
#  yarn
yarn add use-sound
 # npm
npm install use-sound
```

## 进口

这个包导出了一个缺省值:`useSound`钩子。

```
import useSound from 'use-sound';
```

这是你开始使用钩子所需要的一切。当然，您也需要导入要使用的声音。使用`create-react-app`，您可以像导入任何其他任意文件(如图像)一样导入它。你可以很容易地从像 [Freesound](https://freesound.org/) 或 [ZapSplat](https://www.zapsplat.com/) 这样的资源中获得免费的声音。

例如:

```
import ping from '../../sounds/ping.mp3';
const [play, { stop }] = useSound(ping);
```

## 核心概念

从上面的导入和使用示例中，您可能已经注意到，我们从钩子中析构了接受`ping`声音的`play`和`stop`。

这是可用于播放和暂停声音的两种基本方法。默认情况下，声音不会播放，直到用户与元素交互或被有意触发。这对于可访问性非常好，并且允许我们延迟加载声音和第三方库。

另外，`useSound`钩子可以直接接受声音的路径作为第一个参数。您还可以添加一个由`hookOptions`组成的配置对象，以获得更多的控制和灵活性——例如，`playbackRate`、`volume`、`interrupt`等。这是反应性的，并与组件的状态同步。

```
const [volume, setVolume] = React.useState(0.75);
const [play] = useSound('/path/to/sound', { volume });
```

### `hookOptions`

当调用`useSound`时，可以传递给它各种被称为`hookOptions`的选项。下面的图表，以及额外的细节和详尽的 API 列表，可以在`useSound` [API 文档](https://github.com/joshwcomeau/use-sound/blob/master/README.md#api-documentation)中找到:

| **名称** | **值** |
| --- | --- |
| `volume` | 数字 |
| `playbackRate` | 数字 |
| `interrupt` | 布尔代数学体系的 |
| `soundEnabled` | Boolean |
| `sprite` | `spriteMap` |
| `[delegated]` | — |

除了`play`方法，您还可以访问`exposedData`对象，扩展您的 UI 控件可能性:

| **名称** | **值** |
| --- | --- |
| `stop` | 功能-`(id?: string) => void` |
| `pause` | 功能-`(id?: string) => void` |
| `isPlaying` | Boolean |
| `duration` | 编号(或`null`) |
| `sound` | `Howl`(或`null`) |

### 用 howler.js 逃生舱口

[howler.js](https://howlerjs.com/) 是一个音频库，使得在所有平台上使用 JavaScript 处理音频变得简单可靠。你传递给`hookOptions`的任何未被识别的选项都将被委托给 howler.js。你可以在 [howler.js 文档](https://github.com/goldfire/howler.js#documentation)中看到选项的完整列表。

这里有一个例子，说明当出现错误时，我们如何使用`onPlayError`来触发一个函数:

```
const [play] = useSound('/beep.mp3', {
  onPlayError: () => {
    console.error('Error occured!');
  },
})

```

或者在静音时发出回拨:

```
const [play] = useSound('/thong.mp3', {
  onmute: () => {
    myCallback()
  },
})
```

在下一节中，我们将通过所有核心概念的具体例子来研究用例。

## 使用案例和示例

在这一节中，我们将探索一些带有代码样本和菜谱的用例。所有的例子都可以在 [CodeSandbox](https://codesandbox.io/s/use-sound-hjtow) 上直接浏览或编辑。

弹出窗口和通知

### 两个更常见的用例场景是弹出窗口和通知。想想类似于脸书通知标签的东西；当用户在帖子上有新的通知、好友请求、消息或类似内容时，你想引起他们的注意。

为了模拟这个场景，我们将构建一个简单的类似 navbar 的通知图标。然后我们将有一个随机设置通知的`setInterval`逻辑。我不会深入讨论`setInterval`的实现细节，它在 CodeSandbox 中有完整的版本。相反，我们将专注于处理与`useSound`的这种特殊情况。

首先，创建`AppBar`组件。请注意，我还添加了一个复选框切换来演示给用户永久关闭或打开声音的能力，如果他们愿意。这对于良好的用户体验和可访问性非常重要。

和 CSS:

```
import React, { useState } from "react";
import { FontAwesomeIcon } from "@fortawesome/react-fontawesome";
import { faBell } from "@fortawesome/free-solid-svg-icons";
import useSound from "use-sound";

import CheckBox from "./CheckBox";
import useInterval from "../hooks/useInterval";
import sound1 from "../assets/sound1.mp3";

const AppBar = () => {
  const [isRunning, setIsRunning] = useState(true);
  const [checked, setChecked] = useState(false);
  const [count, setCount] = useState(0);
  const [play] = useSound(sound1, { volume: 0.2 });

  useInterval(
    () => {
      setCount(count + 1);
      if (checked) {
        play();
      }
    },
    isRunning ? 3000 : null
  );

  const reset = () => {
    setIsRunning(false);
  };

  const toggle = () => {
    setChecked(!checked);
  };

  return (
    <nav className="appbar">
      <div className="toggle">
        <CheckBox handleChange={toggle} checked={checked} />
      </div>
      <span className="notification">
        <FontAwesomeIcon icon={faBell} onClick={() => reset()} />
        {!!count &&amp; <span className="badge">{count}</span>}
      </span>
    </nav>
  );
};
export default AppBar;
```

首先，让我们回顾一下我们打算实现的目标。我们希望每 *x* 秒继续发出通知，直到用户检查通知。当用户导航离开标签或浏览器时，这很有用，但我们希望保持他们的注意力。

```
.appbar {
  display: flex;
  justify-content: space-between;
  background-color: blue;
  align-items: center;
  color: white;
  height: 50px;
}

.toggle {
  margin-left: 5px;
}
.icons * {
  margin: 0 5px;
}
```

在这里，只要我们的条件是`true`，我们就简单地调用`play()`方法。当`isRunning`或`notification`为`false`时，我们可以简单地选择退出游戏来重置或取消游戏。

播放/暂停按钮

### 另一个常见的例子是播放、暂停，然后恢复声音。想想 Spotify 或任何其他音频流应用程序。让我们快速构建这个组件(CodeSandbox 中提供了完整的代码)。

让我们尝试一下上面的代码。`Player`组件在`play`和`stop`之间切换。就像前面的例子一样，我们已经分别委托了`play()`和`stop()`方法来处理这些点击案例。

```
import React from "react";
import useSound from "use-sound";

const Pause = ({ stop }) => {
  return (
    <svg className="button" viewBox="0 0 60 60" onClick={()=>stop()}>
      <polygon points="0,0 15,0 15,60 0,60" />
      <polygon points="25,0 40,0 40,60 25,60" />
    </svg>
  );
};

const Play = ({ play }) => {
  return (
    <svg className="button" viewBox="0 0 60 60" onClick={play}>
      <polygon points="0,0 50,30 0,60" />
    </svg>
  );
};

const Player = () => {
  const [play, { stop, isPlaying }] = useSound(sound3);
  return (
    <div className="player">
      {isPlaying ? <Pause stop={stop} /> : <Play play={play} />}
    </div>
  );
};

export default Player;
```

这里另一个有用的数据是`isPlaying`属性。这是一个布尔值，它告诉我们声音当前是否正在播放。对于这个用例，我们使用这个属性在播放和停止之间切换。

增加音高/音量

### 另一个有趣的例子是提高音调或音量。

更多来自 LogRocket 的精彩文章:

* * *

### 为了演示这一点，我们将使用一个简单的进度条。我们将随着每次点击增加进度条的长度。这个例子常见于显示健康条，游戏状态，进度等。随着条形的增长，我们还将增加音量和音调。

* * *

你会注意到传递给`useSound`的`playbackRate`和`volume`是反应式的，并自动与状态同步。操纵任何一个`exposedData`就像将它绑定到组件中的一个状态一样简单。

同样，完整的代码可以在 [CodeSandbox](https://codesandbox.io/s/rising-pitch-iw86h) 上获得。

```
import React, { useState } from "react";
import Progress from "react-progressbar";
import useSound from "use-sound";

import sound from "./sound3.mp3";

const ProgressBar = () => {
  const [status, setStatus] = useState(10);
  const [playbackRate, setPlaybackRate] = useState(0.75);
  const [ volume, setVolume]=  useState(0.4);

  const [play] = useSound(sound, {
    playbackRate,
    volume
  });

  const handleIncrease = () => {
    setPlaybackRate(playbackRate => playbackRate + 0.1);
    setStatus(status => status + 10);
    setVolume(volume=>volume+1)
    play();
  };

  return (
    <div>
      <Progress completed={status} onClick={handleIncrease} />
    </div>
  );
};

export default ProgressBar;
```

鬼怪；雪碧

### 当我们必须在应用程序中处理大量声音时，精灵就派上了用场。精灵将许多小声音文件合并成一个。这减小了文件大小，最重要的是，它对性能更好，因为它避免了许多并行 HTTP 旅行来获取不同的声音文件。

我们将构建一组简单的按钮，并将 ID 绑定到 sprite 中的声音，这样每个按钮负责在 sprite 中播放不同的声音。

有更多的选择和可能性——你只是被你的创造力所限制。文档中有更多的用法。

```
import React from "react";
import useSound from "use-sound";

import sound from "./sound3.mp3";

function SpriteDemo() {
  const [play] = useSound(sound, {
    sprite: {
      kick: [0, 350],
      pong: [374, 160],
      bell: [666, 290],
      cowbell: [968, 200]
    }
  });

  const playSound = (e) => {
    e.preventDefault();
    play(e.target.id);
  };

  return (
    <>
      <button id="kick" onClick={e => playSound(e)}>
        Kick
      </button>
      <button id="pong" onClick={e => playSound(e)}>
        Pong
      </button>
      <button id="bell" onClick={e => playSound(e)}>
        Bell
      </button>
      <button id="cowbell" onClick={e => playSound(e)}>
        Cowbell
      </button>
    </>
  );
}
```

无障碍问题

## 许多用户不同意声音增强了网络上的 UX。这不仅仅是一种听觉偏好——如果处理不当，它可能会导致严重的烦恼和可访问性问题。

许多视障用户依靠屏幕阅读器将网页上的文本解析成声音，然后向他们讲述。在网上塞满令人困惑的声音可能会让他们不舒服，并产生与我们预想的相反的效果。因此，批判性地思考网络上的声音是至关重要的。为了确保更广泛的可用性和可访问性，有几条黄金法则需要记住。

所有用户都必须选择使用声音，也就是说，用户可以决定是否要接收声音。用户必须能够轻松地静音或停止声音，并且必须能够永久禁用声音，直到他们另有决定。实现这一点的控件应该易于使用键盘，例如，使用 Tab 键。

更重要的是，web 应用程序在没有声音的情况下应该 100%可用。对于听力受损的用户来说，声音几乎毫无用处；如果没有其他方式可以在没有声音的情况下与网站进行有意义的互动，那么网站本身就毫无用处。在较长的音频的情况下，应该尝试提供替代方案，例如抄本。

要点是首先考虑所有用户和使用声音的最终目标。例如，在上面的通知示例中，用户仍然可以看到有或没有音频的通知；徽章、颜色变化、计数等。会让这个功能在没有声音的情况下 100%可用。

结论

## 网络上的音频未被充分开发和利用。在网络上优雅地、经过深思熟虑地使用声音可以极大地丰富用户体验。

在上面的例子中，当谈到可能性时，我们只是刚刚开始触及表面。几乎所有现代浏览器都支持音频，但是本地 HTML 解决方案可能很难配置。像`useSound`和 howler.js 这样的第三方库的组合，加上一些创造力，可以产生惊人的结果。

在牢记可访问性的同时，我恳请产品设计师和开发人员进行试验，重新审视音频增强。体验一段难忘的时光。

[LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

## 调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。

Modernize how you debug your React apps — [start monitoring for free](https://lp.logrocket.com/blg/react-signup-general).