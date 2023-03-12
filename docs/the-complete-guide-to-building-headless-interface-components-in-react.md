# React - LogRocket 博客中构建无头接口组件的完整指南

> 原文：<https://blog.logrocket.com/the-complete-guide-to-building-headless-interface-components-in-react/>

### 介绍

React 组件是在 React 中创建 UI 的构建块。

这些年来出现了不同的模式。

今天，我们将看看最令人兴奋的 UI 组件构建模式之一:[无头组件](https://headless-countdown.netlify.com/)。

无头组件不一定特定于 React——它们只是帮助构建可重用 UI 组件的模式。

### 什么是无头组件？

在构建我们的示例之前，我们将首先定义什么是[无头组件](https://github.com/learnwithparam/logrocket-headless-component):

> 没有用户界面但具有功能的组件。

这到底是什么意思？

基本上，无头组件包括您用来构建具有以下功能的表格组件的任何东西:

*   按列排序
*   使用表格中的自由文本进行搜索
*   内嵌可编辑行

构建这种组件有两种选择。

### 构建智能组件

智能组件将获得表数据作为输入，将其存储在其内部状态，然后对数据执行所有神奇的功能。

它还将为表创建一个 UI，并在用户搜索数据和内部状态更新时在 DOM 中显示该表，或者获取远程数据并更新该表。

如果我们希望另一个页面中的另一个表格组件具有相同的表格特性，但 UI 不同，我们需要为完全不同的 UI 重用该逻辑。

有几种方法可以做到这一点:

*   构建一个独立的组件，无需重用任何逻辑
*   通过重用某个功能来构建一个无头组件

怎么会？我会解释的。

### 无头组件

正如我之前提到的，无头组件不关心 UI。相反，无头组件关心功能。您可以轻松地重用与这些组件相关联的智能，并将 UI 组件分离出来以实现可重用性。

让我们看一下创建表的例子。

headless 组件只是公开了对数据进行排序、过滤和执行所有功能的方法。它还将数据转换成一种简单的格式，只作为表格行运行。

然后，一个单独的 UI 组件—转储组件—呈现该表。每当有一些数据更改时，这个转储组件都会重新呈现。

这样，我们可以重用逻辑和 UI。

### 什么时候需要无头组件

在构建组件库时，您需要无头组件。给最终用户指定 UI 总是不好的——让最终用户自己制作 UI，自己处理功能。

当您在应用程序中使用不同的 UI 构建相同的功能时，无头组件也很有用。例如，无头组件适用于下拉组件、表格组件和标签组件。

### 当无头组件被过度使用时

如果您的应用程序中没有针对相同功能的多个 UI，或者如果您没有构建一个可重用的组件库供他人使用，那么就没有必要使用无头组件。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

本质上，headless UI 将 UI 和功能分离开来，使每一部分都可以单独重用。

现在，让我们构建一个`react-countdown`无头组件，看看它是如何工作的。

React 有三种高级模式来构建高度可重用的功能组件。

这包括高阶组件、渲染道具组件和自定义 React 挂钩。

我们将在示例中看到渲染道具组件和 React 挂钩。

在构建 headless 组件之前，让我们首先构建一个简单的 React countdown，然后重用其中的功能来创建我们的可重用 headless 组件。

### 用简单的用户界面构建反应倒计时组件

React-dropdown 组件的规格:

*   对于未来的日期，它将呈现该日期剩余的天数、小时数、分钟数和秒数的倒计时。
*   对于旧日期或非日期，它将显示相关的错误消息。

很简单，对吧？

让我们深入研究代码。

```
// App.js

    import React from "react";
    // Export the SimpleCOuntdown component, we have to build this component :)
    import SimpleCountdown from "./components/simple-countdown";

    function App() {
      // Create a future date and pass in to the SimpleCountdown
      const date = new Date("2021-01-01"); // New year - Another 3xx days more :)

      return (
        <div className="App">
          <SimpleCountdown date={date} />
          <hr />
        </div>
      );
    }

    export default App;
```

现在我们将构建不存在的`SimpleCountdown`组件:

```
import React, { useState, useEffect, useRef } from "react";

const SimpleCountdown = ({ date }) => {

/* 
  Need to calculate something from the date value which will give these variables

  `isValidDate` - False if not valid, True if valid date
  `isValidFutureDate` - False if its a past date, True if valid future date
  `timeLeft` - An object which updates every second automatically to give you the number of days, hours, minutes and seconds remaining.
*/
const isValidDate = false, isValidFutureDate = false, timeLeft = {};

// The UI just displays what we computed using the date value we received so that 
return (
    <div className="countdown">
      <h3 className="header">Simple Countdown</h3>
      {!isValidDate && <div>Pass in a valid date props</div>}
      {!isValidFutureDate && (
        <div>
          Time up, let's pass a future date to procrastinate more{" "}
          <span role="img" aria-label="sunglass-emoji">
            😎
          </span>
        </div>
      )}
      {isValidDate && isValidFutureDate && (
        <div>
          {timeLeft.days} days, {timeLeft.hours} hours, {timeLeft.minutes}{" "}
          minutes, {timeLeft.seconds} seconds
        </div>
      )}
    </div>
  );
};

export default SimpleCountdown;
```

上面的例子只是展示了一个 UI 的例子。

使用`date`道具，我们需要计算这三个值。每秒计算并更新一个对象变量。

在 React 中，这是一种每秒自动更新的状态。

`isValidDate`–无效时为假，有效日期时为真
`isValidFutureDate`–过去日期时为假，有效未来日期时为真
`timeLeft`–每秒自动更新以显示剩余天数、小时数、分钟数和秒数的对象。

让我们去掉简单的东西，然后从日期开始计算所有这些值:

```
// To check the date, we are using date-fns library
import isValid from "date-fns/isValid";

// This function calc the time remaining from the date and also check whether the date is a valid future date
export const calculateTimeLeft = date => {
  // Check valid date, if not valid, then return null
  if (!isValid(date)) return null;
  // Get the difference between current date and date props
  const difference = new Date(date) - new Date();
  let timeLeft = {};

  // If there is no difference, return empty object. i.e., the date is not a future date
  if (difference > 0) {
    // if there is a differece, then calculate days, hours, minutes and seconds
    timeLeft = {
      days: Math.floor(difference / (1000 * 60 * 60 * 24)),
      hours: Math.floor((difference / (1000 * 60 * 60)) % 24),
      minutes: Math.floor((difference / 1000 / 60) % 60),
      seconds: Math.floor((difference / 1000) % 60)
    };
  }
  // Return the timeLeft object
  return timeLeft;
};
```

让我们将这个函数放在一个单独的`utils.js`文件中，并将其导入我们的组件文件:

```
// simple-countdown.js

import React, { useState, useEffect, useRef } from "react";
// import our util function which calculate the time remaining
import { calculateTimeLeft } from "../utils";

const SimpleCountdown = ({ date }) => {
  // Calculate the initial time left
  const initialTimeLeft = calculateTimeLeft(date);
  // assign it to a state, so that we will update the state every second
  const [timeLeft, setTimeLeft] = useState(initialTimeLeft);
  const timer = useRef();

  // Inorder to update the state every second, we are using useEffect
  useEffect(() => {
    // Every second this setInterval runs and recalculate the current time left and update the counter in the UI
    timer.current = setInterval(() => {
      setTimeLeft(calculateTimeLeft(date));
    }, 1000);

    // Cleaning up the timer when unmounting
    return () => {
      if (timer.current !== undefined) {
        clearInterval(timer.current);
      }
    };
  }, [date]);

  let isValidDate = true,
    isValidFutureDate = true;

  // If timeLeft is Null, then it is not a valid date
  if (timeLeft === null) isValidDate = false;
  // if timeleft is not null but the object doesn't have any key or seconds key is undefined, then its not a future date
  if (timeLeft && timeLeft.seconds === undefined) isValidFutureDate = false;

  // Return the UI
  return (
    ....  
  );
};

export default SimpleCountdown;
```

很简单。

首先，我们计算初始剩余时间，然后将其分配给一个状态。然后我们创建一个`setInterval`来每秒更新状态，并重新计算剩余时间。

这样，它会重新计算每秒剩余的时间，并像倒计时一样更新用户界面。

我们已经成功地使用我们的功能创建了一个漂亮、简单的 UI。如你所见，我们所有的功能都与用户界面隔离开来。

尽管如此，UI 仍然驻留在`SimpleCountdown`组件中。

如果你想用 SVG 和 CSS 动画创建另一个倒计时 UI，那么你需要创建一个新的组件。如果你想避免这一点，提取功能，只是让用户界面哑和分离。

让我们将 UI 分离到单独的文件中，并创建它的多个版本:

```
// 1st version of React countdown UI
    import React from "react";

    const FirstCountdownUI = ({ timeLeft, isValidDate, isValidFutureDate }) => {
      return (
        <div className="countdown">
          <h3 className="header">First Countdown UI</h3>
          {!isValidDate && <div>Pass in a valid date props</div>}
          {!isValidFutureDate && (
            <div>
              Time up, let's pass a future date to procrastinate more{" "}
              <span role="img" aria-label="sunglass-emoji">
                😎
              </span>
            </div>
          )}
          {isValidDate && isValidFutureDate && (
            <div>
              <strong className="countdown-header">{timeLeft.days}</strong> days,{" "}
              <strong className="countdown-header">{timeLeft.hours}</strong> hours,{" "}
              <strong className="countdown-header">{timeLeft.minutes}</strong>{" "}
              minutes,{" "}
              <strong className="countdown-header">{timeLeft.seconds}</strong>{" "}
              seconds
            </div>
          )}
        </div>
      );
    };

    export default FirstCountdownUI;
```

```
// 2nd version of React countdown UI
    import React from "react";

    const SecondCountdownUI = ({ timeLeft, isValidDate, isValidFutureDate }) => {
      return (
        <div className="countdown">
          <h3 className="header">Second Countdown UI</h3>
            {!isValidDate && <div>Pass in a valid date props</div>}
            {!isValidFutureDate && (
              <div>
                Time up, let's pass a future date to procrastinate more{" "}
                <span role="img" aria-label="sunglass-emoji">
                  😎
                </span>
              </div>
            )}
            {isValidDate && isValidFutureDate && (
              <div>
                <strong className="countdown-header">{timeLeft.days} : </strong>
                <strong className="countdown-header">
                  {timeLeft.hours} :{" "}
                </strong>
                <strong className="countdown-header">
                  {timeLeft.minutes} :{" "}
                </strong>
                <strong className="countdown-header">{timeLeft.seconds}</strong>
              </div>
            )}
        </div>
      );
    };

    export default SecondCountdownUI;
```

我们创建了两个不同的 UI。现在，我们将创建无头组件，以便我们可以轻松地重用任何 UI 组件的功能。

### 使用渲染道具的无头组件

基本上，我们将重用我们创建的相同的逻辑，只是改变我们呈现 UI 的方式。

```
import { useState, useEffect, useRef } from "react";
    import { calculateTimeLeft } from "../utils";

    /* 
      All logic are same as previous implementation. 
      Only change is, Instead of rendering a UI, we just send the render props
    */
    const Countdown = ({ date, children }) => {
      const initialTimeLeft = calculateTimeLeft(date);
      const [timeLeft, setTimeLeft] = useState(initialTimeLeft);
      const timer = useRef();

      useEffect(() => {
        timer.current = setInterval(() => {
          setTimeLeft(calculateTimeLeft(date));
        }, 1000);

        return () => {
          if (timer.current !== undefined) {
            clearInterval(timer.current);
          }
        };
      }, [date]);

      let isValidDate = true,
        isValidFutureDate = true;

      if (timeLeft === null) isValidDate = false;
      if (timeLeft && timeLeft.seconds === undefined) isValidFutureDate = false;

      // Instead of rendering a UI, we are returning a function through the children props
      return children({
        isValidDate,
        isValidFutureDate,
        timeLeft
      });
    };

    export default Countdown;
```

您可以将其称为子道具、函数或渲染道具。

两者是一体的。它不需要成为孩子们的道具。它可以是任何可以作为函数返回的属性，并且父组件可以使用它通过渲染属性返回的变量来渲染 UI。这是做这件事的常用方法。

呈现 UI 很简单。

```
// On Page 1 - We render first countdown UI

import React from "react";
import FirstCountdownUI from './first-countdown-ui';
import Countdown from './countdown-render-props';

function App() {
  const date = new Date("2021-01-01"); // New year!

  return (
      <Countdown date={date}>
        {(renderProps) => (
          <FirstCountdownUI {...renderProps} />
        )}
      </Countdown>
  );
}
export default App;
```

在第二页上有 React 倒计时:

```
// On Page 2, we render second countdown UI

import React from "react";
import SecondCountdownUI from './second-countdown-ui';
import Countdown from './countdown-render-props';

function App() {
  const date = new Date("2021-01-01"); // New year!

  return (

        {(renderProps) => (

        )}

  );
}
export default App;
```

通过这种方式，您可以重用功能，并使用相同的功能组件创建多个不同的 UI。

同样的无头组件也可以使用定制钩子来实现。这样做比使用基于渲染道具的组件更简单。

让我们在下一步中这样做:

### 自定义 React 挂钩(无头组件)

首先，我们将构建自定义钩子，它将提供`timeLeft`、`isValidDate`和`isvalidFutureDate`变量。

```
// use-countdown.js - custom hooks

import { useState, useEffect, useRef } from "react";
import { calculateTimeLeft } from "../utils";

// All the computation are same as previous, only change is, we directly return the values instead of rendering anything.
const useCountdown = date => {
  const initialTimeLeft = calculateTimeLeft(date);
  const [timeLeft, setTimeLeft] = useState(initialTimeLeft);
  const timer = useRef();

  useEffect(() => {
    timer.current = setInterval(() => {
      setTimeLeft(calculateTimeLeft(date));
    }, 1000);

    return () => {
      if (timer.current !== undefined) {
        clearInterval(timer.current);
      }
    };
  }, [date]);

  let isValidDate = true,
    isValidFutureDate = true;

  if (timeLeft === null) isValidDate = false;
  if (timeLeft && timeLeft.seconds === undefined) isValidFutureDate = false;

  // We return these computed values for the passed date prop to our hook
  return { isValidDate, isValidFutureDate, timeLeft };
};

export default useCountdown;
```

这个钩子将抽象一切，每秒计算一次`timeLeft`,并返回给将要使用这个钩子的组件。

让我们用两个不同的用户界面和相同的自定义倒计时挂钩来渲染我们的两个页面:

```
// On Page 1 - We render first countdown UI

import React from "react";
import FirstCountdownUI from './first-countdown-ui';
import useCountdown from './use-countdown'; // importing the custom hook

function App() {
  const date = new Date("2021-01-01"); // New year!
  // pass in the date and get all the values from the hook, throw it to the UI
  const { timeLeft, isValidDate, isValidFutureDate } = useCountdown(date);

  return (
      <FirstCountdownUI 
        timeLeft={timeLeft} 
        isValidDate={isValidDate} 
        isValidFutureDate={isValidFutureDate} 
      />
  );
}

export default App;
```

在带有自定义倒计时挂钩的第二页上:

```
// On Page 2, we render second countdown UI

import React from "react";
import SecondCountdownUI from './second-countdown-ui';
import useCountdown from './use-countdown'; // importing the custom hook

function App() {
  const date = new Date("2021-01-01"); // New year!
  // pass in the date and get all the values from the hook, throw it to the UI
  const { timeLeft, isValidDate, isValidFutureDate } = useCountdown(date);

  return (
      <SecondCountdownUI 
        timeLeft={timeLeft} 
        isValidDate={isValidDate} 
        isValidFutureDate={isValidFutureDate} 
       />
  );
}

export default App;
```

通过这种方法，我们可以重用组件并将逻辑从 UI 中分离出来。

您甚至可以将这个无头组件单独发布为 NPM 库，并在多个项目中使用它。

### 结论

React 世界中一些常用的无头组件包括:

您可以查看这些代码库，了解大量信息，并了解这些库是如何优雅地创建的。

希望你在 React 中学到了一些技巧。

你可以在这里查看示例代码库[，在这里](https://github.com/learnwithparam/logrocket-headless-component)查看演示[。](https://headless-countdown.netlify.com/)

请在评论中分享你的想法。

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