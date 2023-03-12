# 5 大 React 钩子库比较

> 原文：<https://blog.logrocket.com/5-top-react-hooks-libraries-compared/>

React 的 Hooks API 现在是创建组件的实际方法。它与类组件 API 共存，让我们可以用 JavaScript 类创建组件。

除了 React 库附带的标准挂钩，开发人员还可以创建自己的挂钩。不出所料，许多定制钩子库已经出现，使得创建 React 应用程序更加容易。

在本文中，我们将看看五个有用的 React Hooks 库，并比较它们的效用

React 钩子库为我们提供了类似 React 类组件的生命周期方法的钩子。

例如，它提供了像`useDidMount`和`useDidUpdate`这样的挂钩，前者在组件安装时运行，后者在组件更新时运行。它也有管理状态的钩子，像`useCounter`，它添加了一个有自己功能的数字状态来管理它。

要安装这个包，我们可以运行:

```
npm i react-hooks-lib --save
```

然后我们可以从模块中导入一些钩子来使用它们。我们可以通过编写以下代码来使用`useDidMount`钩子:

```
import React from "react";
import { useDidMount } from "react-hooks-lib";

export default function App() {
  useDidMount(() => {
    console.log("did mount");
  });

  return (
    <div className="App">
      <h1>Hello world</h1>
    </div>
  );
}
```

同样，我们可以通过编写以下代码来使用`useCounter`钩子:

```
import React from "react";
import { useCounter } from "react-hooks-lib";

export default function App() {
  const { count, inc, dec, reset } = useCounter(0);
  return (
    <div>
      {count}
      <button onClick={() => inc(1)}>increment</button>
      <button onClick={() => dec(1)}>decrement</button>
      <button onClick={reset}>reset</button>
    </div>
  );
}
```

它用`count`状态和`inc`、`dec`和`reset`方法返回一个对象。让我们来分解一下这些方法的作用:

*   `inc`将`count`增加一个给定的数字
*   将`count`减少给定的数量
*   `reset`将`count`重置为初始值

React Hooks Lib 还附带了`useField`钩子，使得获取和设置输入值更加容易。我们可以通过书写来使用它:

```
import React from "react";
import { useField } from "react-hooks-lib";

export default function App() {
  const { value, bind } = useField("text");

  return (
    <div>
      <input type="text" {...bind} />
      <p>{value}</p>
    </div>
  );
}
```

我们只是将整个`bind`对象传入输入，我们可以用它从输入中获取数据，并将其设置为`value`的值。`bind`也有`value`属性来设置输入值。

它也适用于选择元素:

```
import React from "react";
import { useField } from "react-hooks-lib";

export default function App() {
  const { value, bind } = useField("text");

  return (
    <div>
      <select {...bind}>
        <option value="apple">apple</option>
        <option value="orange">orange</option>
      </select>
      <p>{value}</p>
    </div>
  );
}
```

React Hooks Lib 附带了许多其他提供类似功能的钩子。总的来说，它为我们提供了定制的钩子，可以做一些方便的事情。然而，我们应该注意，并不是所有提供的钩子都有文档记录。

react-hanger 是一个库，它为我们提供了 react 钩子，让我们可以更容易地管理各种状态。它带有以下挂钩:

*   `useInput`–获取并设置输入控制值
*   `useBoolean`–获取和设置布尔状态
*   `useNumber`–获取和设置状态数
*   `useArray`–获取和设置数组状态
*   `useOnMount`–安装组件时运行代码
*   `useOnUnmount`–卸载组件时运行代码
*   `useStateful`–获取并设置组件状态

我们可以如下使用`useInput`来简化输入值的处理:

```
import React from "react";
import { useInput } from "react-hanger";

export default function App() {
  const input = useInput("");

  return (
    <div>
      <input type="text" value={input.value} onChange={input.onChange} />
      <p>{input.value}</p>
    </div>
  );
}
```

我们可以使用`useNumber`来获取和设置一个数字状态:

```
import React from "react";
import { useNumber } from "react-hanger";

export default function App() {
  const counter = useNumber(3, { lowerLimit: 0, upperLimit: 5 });

  return (
    <div>
      <p> {counter.value} </p>
      <button onClick={() => counter.increase()}> increase </button>
      <button onClick={() => counter.decrease()}> decrease </button>
    </div>
  );
}
```

我们可以用第一个参数设置`counter.value`的初始值，用第二个参数设置`counter.value`状态的下限和上限。`useArray`和`useBoolean`挂钩的工作方式类似。

要设置任何一种状态，我们可以使用`useStateful`钩子:

```
import React from "react";
import { useStateful } from "react-hanger";

export default function App() {
  const username = useStateful("tom");

  return (
    <div>
      <p> {username.value} </p>
      <button onClick={() => username.setValue("tom")}> tom </button>
      <button onClick={() => username.setValue("jerry")}> jerry </button>
    </div>
  );
}
```

它比 React 的`useState`钩子更好，因为我们可以从`value`属性中获取状态的值，并且我们可以用返回对象的`setValue`方法来设置该值。与 React 内置的`useState`钩子不同，获取 state 和 setter 函数不需要析构。

像无数其他库一样，React hookedUp 让我们可以管理组件状态。但是我们也可以用它来管理 HTML 元素的焦点和悬停。

它附带了一些钩子，这些钩子复制了类组件生命周期钩子的功能，还附带了一些有用的定时器和网络钩子，这些钩子是我们到目前为止讨论过的其他库中没有提供的。

让我们来看看`useHover`钩子，它会检测我们是否将鼠标悬停在输入上:

```
import React from "react";
import { useHover } from "react-hookedup";

export default function App() {
  const { hovered, bind } = useHover();

  return (
    <div>
      <p>{hovered ? "hovered" : "not hovered"}</p>
      <input {...bind} />
    </div>
  );
}
```

我们只是将整个`bind`对象展开作为`input`的道具。`useFocus`钩也可以以类似的方式使用。

如果我们在类组件中错过了好的 ol' `componentDidMount`方法，我们可以使用`useOnMount`钩子在函数组件中提供等效的功能:

```
import React from "react";
import { useOnMount } from "react-hookedup";

export default function App() {
  useOnMount(() => console.log("mounted"));
  return <div> hello world </div>;
}
```

如果我们想在代码中调用`setInterval`，我们可以使用`useInterval`钩子。例如，我们可以写:

```
import React, { useState } from "react";
import { useInterval } from "react-hookedup";

export default function App() {
  const [time, setTime] = useState(new Date().toString());

  useInterval(() => setTime(new Date().toString()), 1000);

  return <p>{time}</p>;
}
```

我们只是传入回调函数作为第一个参数运行，传入间隔作为第二个参数，就像`setInterval`函数一样。

它还附带了一个`useTimeout`钩子，用于在给定的延迟后运行回调:

```
import React from "react";
import { useTimeout } from "react-hookedup";

export default function App() {
  useTimeout(() => alert("hello world"), 1500);

  return <h1>hello world</h1>;
}
```

React hookedUp 的另一个有用的钩子是`useOnlineStatus`，它允许我们查看我们的应用程序的在线状态。例如，我们可以写:

```
import React from "react";
import { useOnlineStatus } from "react-hookedup";

export default function App() {
  const { online } = useOnlineStatus();

  return <h1>{online ? "online" : "offline"}</h1>;
}
```

它返回`online`属性，当设备在线时是`true`。

就像我们之前提到的，React hookedUp 还附带了一套状态管理挂钩，这是我们之前看到的库附带的。这些功能与它们在其他库中的工作方式相似。

react-use 钩子库提供了比目前列出的其他库更大的钩子集合，包括利用浏览器可以访问的各种硬件的钩子。它还带有钩子来观察屏幕大小、运动、滚动、动画，以及动态调整 CSS 等等。

例如，我们可以使用`useMouse`钩子来观察用户鼠标的位置:

```
import React from "react";
import { useMouse } from "react-use";

export default function App() {
  const ref = React.useRef(null);
  const { docX, docY, posX, posY, elX, elY, elW, elH } = useMouse(ref);

  return (
    <div ref={ref}>
      <div>
        Mouse position in document - ({docX}, {docY})
      </div>
      <div>
        Mouse position in element - ({elX}, {elY})
      </div>
      <div>
        Element position- ({posX} , {posY})
      </div>
      <div>
        Element dimensions - {elW}x{elH}
      </div>
    </div>
  );
}
```

我们可以使用`docX`和`docY`来获取鼠标在文档中的 x 和 y 坐标。同样，我们可以使用`elX`和`elY`属性来获取鼠标在被监控元素中的 x 和 y 坐标。我们将 ref 分配给我们想要观察的元素。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

类似地，我们可以使用`useScroll`钩子来跟踪元素的滚动位置:

```
import React from "react";
import { useScroll } from "react-use";

export default function App() {
  const scrollRef = React.useRef(null);
  const { x, y } = useScroll(scrollRef);

  return (
    <div ref={scrollRef} style={{ height: 300, overflowY: "scroll" }}>
      <div style={{ position: "fixed" }}>
        <div>x: {x}</div>
        <div>y: {y}</div>
      </div>
      {Array(100)
        .fill()
        .map((_, i) => (
          <p key={i}>{i}</p>
        ))}
    </div>
  );
}
```

我们为想要查看其滚动位置的元素分配一个 ref，并将`height`和`overflowY`设置为`scroll`，这样我们就可以滚动内容。我们用返回的`x`和`y`属性获得滚动位置。

我们也可以使用钩子来提供动画。例如，我们可以使用`useSpring`钩子来动画显示数字:

```
import React, { useState } from "react";
import useSpring from "react-use/lib/useSpring";

export default function App() {
  const [target, setTarget] = useState(50);
  const value = useSpring(target);

  return (
    <div>
      {value}
      <br />
      <button onClick={() => setTarget(0)}>Set 0</button>
      <button onClick={() => setTarget(200)}>Set 100</button>
    </div>
  );
}
```

我们将动画的数字传递到`useSpring`钩子中。然后数字会一直显示，直到到达`target`。注意`rebound`是这个钩子工作所必需的。

这个库也有钩子让我们提交各种副作用，比如将数据复制到剪贴板和操作本地存储。要添加复制到剪贴板特性，我们可以使用`useCopyToClipboard`钩子:

```
import React, { useState } from "react";
import useCopyToClipboard from "react-use/lib/useCopyToClipboard";

export default function App() {
  const [text, setText] = useState("");
  const [state, copyToClipboard] = useCopyToClipboard();

  return (
    <div>
      <input value={text} onChange={(e) => setText(e.target.value)} />
      <button type="button" onClick={() => copyToClipboard(text)}>
        copy text
      </button>
      {state.error ? (
        <p>error: {state.error.message}</p>
      ) : (
        state.value && <p>Copied {state.value}</p>
      )}
    </div>
  );
}
```

我们只需在组件中调用`useCopyToClipboard`来获得`copyToClipboard`函数，然后我们可以调用它来复制任何作为参数传入剪贴板的内容。

同样，我们可以通过`useLocalStorage`钩子轻松地使用本地存储:

```
import React from "react";
import useLocalStorage from "react-use/lib/useLocalStorage";

export default function App() {
  const [value, setValue, remove] = useLocalStorage("key", "foo");

  return (
    <div>
      <div>Value: {value}</div>
      <button onClick={() => setValue("bar")}>bar</button>
      <button onClick={() => setValue("baz")}>baz</button>
      <button onClick={() => remove()}>Remove</button>
    </div>
  );
}
```

`value`具有带有给定键的本地存储条目的值，`setValue`让我们传入要设置的值，`remove`从本地存储中移除条目。

除了前面提到的 react-use 特有的钩子之外，这个库还附带了许多钩子，用于设置状态、使用浏览器 API、运行异步代码等等。react-use 是迄今为止我们看到的最全面的钩子库。

React Recipes 是另一个钩子库，带有许多自定义钩子。它提供了许多与 react-use 相同的钩子，比如使用浏览器 API、管理状态、运行异步代码等的钩子。

例如，我们可以使用`useSpeechSynthesis`钩子让浏览器说话:

```
import React, { useState } from "react";
import { useSpeechSynthesis } from "react-recipes";

export default function App() {
  const [value, setValue] = useState("");
  const [ended, setEnded] = useState(false);
  const onBoundary = (event) => {
    console.log(`${event.name}: ${event.elapsedTime} milliseconds.`);
  };
  const onEnd = () => setEnded(true);
  const onError = (event) => {
    console.warn(event);
  };

  const {
    cancel,
    speak,
    speaking,
    supported,
    voices,
    pause,
    resume
  } = useSpeechSynthesis({
    onEnd,
    onBoundary,
    onError
  });

  if (!supported) {
    return "Speech is not supported.";
  }

  return (
    <div>
      <input value={value} onChange={(event) => setValue(event.target.value)} />
      <button
        type="button"
        onClick={() => speak({ text: value, voice: voices[1] })}
      >
        Speak
      </button>
      <button type="button" onClick={cancel}>
        Cancel
      </button>
      <button type="button" onClick={pause}>
        Pause
      </button>
      <button type="button" onClick={resume}>
        Resume
      </button>
      <p>{speaking && "Voice is speaking"}</p>
      <p>{ended && "Voice has ended"}</p>
      <div>
        <h2>Voices:</h2>
        <div>
          {voices.map((voice) => (
            <p key={voice.name}>{voice.name}</p>
          ))}
        </div>
      </div>
    </div>
  );
}
```

我们称之为`useSpeechSynthesis`钩子，它返回一个具有各种属性的对象:

*   `cancel`取消语音合成
*   `speak`让浏览器开始说话
*   `speaking`是一个布尔值，它告诉我们语音合成是否正在进行
*   `supported`是一个布尔值，它告诉我们当前的浏览器是否支持语音合成
*   有可供选择的声音列表
*   让我们暂停说话
*   让我们继续说话

它附带了许多钩子，这里列出了这些钩子，它们为我们提供了许多 React 本身所没有的功能。它是一个真正有良好文档记录和全面的库，这一点也没有什么价值。

## 判决结果？

到目前为止，我们今天讨论的最全面、最有用的 React 钩子库是 react-use 和 React Recipes。它们为我们提供了许多针对各种用例的钩子，这样我们就不必自己从头开始编写了。

React Hooks Lib、react-hanger 和 React hookedUp 为状态管理提供了一些基本的钩子，可以在一定程度上帮助我们简化状态管理。如果这就是我们要从钩子库中寻找的，那么 React Hooks Lib、react-hanger 和 React hookedUp 是有用的。它们都很容易使用，所有的(除了 React Hooks Lib)都有清晰的文档。

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