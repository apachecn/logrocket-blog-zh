# 使用 React 语音识别钩子进行语音辅助

> 原文：<https://blog.logrocket.com/using-the-react-speech-recognition-hook-for-voice-assistance/>

在本教程中，我们将向您展示如何使用 React 语音识别在 React 应用程序中实现语音辅助。

我们将讨论以下内容:

## 什么是 React 语音识别？

[React 语音识别](https://github.com/JamesBrill/react-speech-recognition#readme)是一个 [React 挂钩](https://blog.logrocket.com/react-hooks-cheat-sheet-unlock-solutions-to-common-problems-af4caf699e70/)，它与 Web 语音 API 一起工作，将来自设备麦克风的语音翻译成文本。然后，React 应用程序可以读取这些文本并用来执行任务。

React 语音识别提供了一个命令选项，用于根据特定的语音短语执行特定的任务。例如，当用户询问天气信息时，您可以执行天气 API 调用。这只是一个基本的例子，但当涉及到语音辅助和控制时，[可能性是无限的](https://voicebot.ai/2021/01/01/over-100-voice-ai-predictions-for-2021-from-50-industry-leaders/)。

### 浏览器支持

截至 2021 年 2 月，React 语音识别支持以下浏览器:

*   谷歌浏览器(推荐)
*   微软 Edge
*   安卓版谷歌浏览器
*   Android Webview
*   三星互联网

遗憾的是，iOS 不支持这些 API。

## React 语音识别设置和安装

要将 React 语音识别添加到 React 项目中，只需打开终端并键入:

```
npm i --save react-speech-recognition

```

当你按下回车键时，这将把钩子添加到你的项目中。

### 构建演示用户界面

为了了解语音识别挂钩是如何工作的，我们将构建一个简单的 UI。

首先，我们将添加一个带有麦克风图标的圆形按钮，一个带有文本的按钮来指示我们是否在听用户讲话，以及一个停止按钮来停止听。

在这些元素下面，我们将展示用户的语音到文本的翻译，并创建一个重置按钮来清除文本并停止收听。

这是我们对上述组件的 JSX:

```
// App.js
function App() {
  return (
    <div className="microphone-wrapper">
      <div className="mircophone-container">
        <div className="microphone-icon-container">
          <img src={microPhoneIcon} className="microphone-icon" />
        </div>
        <div className="microphone-status">
          Click to start Listening
        </div>
        <button className="microphone-stop btn" >
          Stop
        </button>
      </div>

        <div className="microphone-result-container">
          <div className="microphone-result-text">Speech text here</div>
          <button className="microphone-reset btn" >
            Reset
          </button>
        </div>

    </div>
  );
}

```

设置完成后，我们现在可以添加一些样式:

```
// App.css
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
body {
  background-color: rgba(0, 0, 0, 0.8);
  font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
  color: white;
}
.mircophone-container {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100vw;
  height: 50vh;
}
.microphone-icon-container {
  width: 100px;
  height: 100px;
  border-radius: 50%;
  background-image: linear-gradient(128deg, #ffffff, #647c88);
  padding: 20px;
  margin-right: 20px;
  position: relative;
  cursor: pointer;
}
.microphone-icon-container.listening::before {
  content: "";
  width: 100px;
  height: 100px;
  background-color: #ffffff81;
  position: absolute;
  top: 50%;
  left: 50%;
  transform:translate(-50%, -50%) scale(1.4);
  border-radius: 50%;
  animation: listening infinite 1.5s;
}
@keyframes listening{
  0%{
    opacity: 1;
    transform: translate(-50%, -50%) scale(1);
  }
  100%{
    opacity: 0;
    transform: translate(-50%, -50%) scale(1.4);
  }
}
.microphone-icon {
  width: 100%;
  height: 100%;
}
.microphone-status {
  font-size: 22px;
  margin-right: 20px;
  min-width: 215px;
}
.btn {
  border: none;
  padding: 10px 30px;
  margin-right: 10px;
  outline: none;
  cursor: pointer;
  font-size: 20px;
  border-radius: 25px;
  box-shadow: 0px 0px 10px 5px #ffffff1a;
}
.microphone-result-container {
  text-align: center;
  height: 50vh;

  display: flex;
  flex-direction: column;
  justify-content: space-between;
  align-items: center;
  padding-bottom: 30px;

}
.microphone-result-text {
  margin-bottom: 30px;
  width: 70vw;
  overflow-y: auto;
}
.microphone-reset {
  border: 1px solid #fff;
  background: none;
  color: white;
  width: fit-content;
}

```

您可能已经注意到了，我们还包括了一个动画，当开始收听时会播放，从而提醒用户他们现在可以说话了。

## 添加 React 语音识别挂钩

要使用 React 语音识别，我们必须首先将其导入到组件中。我们将使用`useSpeechRecognition`钩子和`SpeechRecognition`对象。

要导入 React 语音识别:

```
import SpeechRecognition, { useSpeechRecognition } from "react-speech-recognition";

```

要开始听用户的声音，我们需要调用`startListening`函数:

```
SpeechRecognition.startListening()

```

要停止监听，我们可以调用`stopListening`:

```
SpeechRecognition.stopListening()

```

为了获得用户发言的文本，我们将使用`transcript`:

```
const { transcript } = useSpeechRecognition()

```

无论用户说什么，它都会记录下这个值。

要重置或清除抄本的值，您可以调用`resetTranscript`:

```
const { resetTranscript } = useSpeechRecognition()

```

使用`resetTranscript`功能会将抄本设置为空。

最后，为了检查浏览器是否支持 Web 语音 API，我们可以使用这个函数:

```
if (!SpeechRecognition.browserSupportsSpeechRecognition()) {
  // Browser not supported & return some useful info.
}

```

### 完整代码

至此，我们已经回顾了所有的内容，现在我们可以开始设置代码了。请注意，在下面的块中，我们添加了监听事件和相应的状态:

```
import { useRef, useState } from "react";
import SpeechRecognition, { useSpeechRecognition } from "react-speech-recognition";
import "./App.css";
import microPhoneIcon from "./microphone.svg";

function App() {
  const { transcript, resetTranscript } = useSpeechRecognition();
  const [isListening, setIsListening] = useState(false);
  const microphoneRef = useRef(null);
  if (!SpeechRecognition.browserSupportsSpeechRecognition()) {
    return (
      <div className="mircophone-container">
        Browser is not Support Speech Recognition.
      </div>
    );
  }
  const handleListing = () => {
    setIsListening(true);
    microphoneRef.current.classList.add("listening");
    SpeechRecognition.startListening({
      continuous: true,
    });
  };
  const stopHandle = () => {
    setIsListening(false);
    microphoneRef.current.classList.remove("listening");
    SpeechRecognition.stopListening();
  };
  const handleReset = () => {
    stopHandle();
    resetTranscript();
  };
  return (
    <div className="microphone-wrapper">
      <div className="mircophone-container">
        <div
          className="microphone-icon-container"
          ref={microphoneRef}
          onClick={handleListing}
        >
          <img src={microPhoneIcon} className="microphone-icon" />
        </div>
        <div className="microphone-status">
          {isListening ? "Listening........." : "Click to start Listening"}
        </div>
        {isListening && (
          <button className="microphone-stop btn" onClick={stopHandle}>
            Stop
          </button>
        )}
      </div>
      {transcript && (
        <div className="microphone-result-container">
          <div className="microphone-result-text">{transcript}</div>
          <button className="microphone-reset btn" onClick={handleReset}>
            Reset
          </button>
        </div>
      )}
    </div>
  );
}
export default App;

```

## 使用 React 语音识别执行任务

现在，我们已经设置了应用程序，以便当用户单击麦克风按钮时，应用程序将听到他们的声音，并输出下面的文字记录。第一次运行时，您需要允许麦克风权限。

现在有趣的部分来了:添加命令来执行基于用户语音/短语的任务。

### 添加命令

要添加命令，我们必须将一个数组作为命令选项传递给`useSpeechRecognition`。然而，在我们这样做之前，我们必须像这样准备我们的命令数组:

```
const commands = [
    {
      command: "open *",
      callback: (website) => {
        window.open("http://" + website.split(" ").join(""));
      },
    },
    {
      command: "change background colour to *",
      callback: (color) => {
        document.body.style.background = color;
      },
    },
    {
      command: "reset",
      callback: () => {
        handleReset();
      },
    },
    ,
    {
      command: "reset background colour",
      callback: () => {
        document.body.style.background = `rgba(0, 0, 0, 0.8)`;
      },
    },
  ];

```

记住`commands`是带有命令和回调属性的 JSON 对象的数组。出于我们的目的，命令是您将在其中编写命令的属性；回调会相应地触发。

在上面的例子中，您可能已经注意到我们在第一个和第二个命令中传递了一个星号。这个符号允许我们捕获多个单词，并在回调函数中将它们作为变量传递回去。

您可以像这样将`commands`变量传递给`useSpeechRecognition`:

```
const { transcript, resetTranscript } = useSpeechRecognition({ commands });

```

现在，您应该能够运行您的应用程序和命令了。

为了便于将来参考，我们使用 React 语音识别挂钩创建的应用程序的完整代码如下所示:

```
import { useRef, useState } from "react";
import SpeechRecognition, { useSpeechRecognition } from "react-speech-recognition";
import "./App.css";
import microPhoneIcon from "./microphone.svg";
function App() {
  const commands = [
    {
      command: "open *",
      callback: (website) => {
        window.open("http://" + website.split(" ").join(""));
      },
    },
    {
      command: "change background colour to *",
      callback: (color) => {
        document.body.style.background = color;
      },
    },
    {
      command: "reset",
      callback: () => {
        handleReset();
      },
    },
    ,
    {
      command: "reset background colour",
      callback: () => {
        document.body.style.background = `rgba(0, 0, 0, 0.8)`;
      },
    },
  ];
  const { transcript, resetTranscript } = useSpeechRecognition({ commands });
  const [isListening, setIsListening] = useState(false);
  const microphoneRef = useRef(null);
  if (!SpeechRecognition.browserSupportsSpeechRecognition()) {
    return (
      <div className="mircophone-container">
        Browser is not Support Speech Recognition.
      </div>
    );
  }
  const handleListing = () => {
    setIsListening(true);
    microphoneRef.current.classList.add("listening");
    SpeechRecognition.startListening({
      continuous: true,
    });
  };
  const stopHandle = () => {
    setIsListening(false);
    microphoneRef.current.classList.remove("listening");
    SpeechRecognition.stopListening();
  };
  const handleReset = () => {
    stopHandle();
    resetTranscript();
  };
  return (
    <div className="microphone-wrapper">
      <div className="mircophone-container">
        <div
          className="microphone-icon-container"
          ref={microphoneRef}
          onClick={handleListing}
        >
          <img src={microPhoneIcon} className="microphone-icon" />
        </div>
        <div className="microphone-status">
          {isListening ? "Listening........." : "Click to start Listening"}
        </div>
        {isListening && (
          <button className="microphone-stop btn" onClick={stopHandle}>
            Stop
          </button>
        )}
      </div>
      {transcript && (
        <div className="microphone-result-container">
          <div className="microphone-result-text">{transcript}</div>
          <button className="microphone-reset btn" onClick={handleReset}>
            Reset
          </button>
        </div>
      )}
    </div>
  );
}
export default App;

```

## 结论

到目前为止，您应该对如何在项目中使用 React 语音识别挂钩有了更好的理解。对于未来的阅读，我建议学习更多关于语音编程的知识，以及人工智能在你的编码工作中帮助 T2 的其他方法。

感谢您阅读文章。请在下面留下任何反馈或评论。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)