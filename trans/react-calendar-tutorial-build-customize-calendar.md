# React-Calendar:构建和定制一个简单的日历日志博客

> 原文：<https://blog.logrocket.com/react-calendar-tutorial-build-customize-calendar/>

大多数现实世界的应用程序都包含一种管理和操作日期的方法。在这种情况下，拥有一个日历通常是最有效的解决方案。

在本教程中，我们将向您展示如何使用 React-Calendar 在 React 中创建一个简单的日历。我们将通过实际例子介绍以下内容:

## 什么是反应日历？

React-Calendar 是一个简单的日历库，提供了选择日、月、年甚至十年的能力。它还支持日期范围选择和用于更复杂用例的多种语言。

因为 React-Calendar 不依赖于`moment.js`，所以它是一个非常灵活和通用的库，几乎可以在任何应用程序中使用。

## 创建 React 项目

让我们使用 [Create react App](https://blog.logrocket.com/getting-started-with-create-react-app-d9314) 为本教程创建一个全新的 React 项目。确保您的机器上安装了 [Node.js](https://nodejs.org/en/) ≥v10.16 和 npm ≥v5.6。

要创建新项目，请运行以下命令:

```
npx create-react-app calendar-example

```

现在，使用 npm 将 React-Calendar 库添加到我们的项目中:

```
npm install react-calendar

```

现在我们有了一个最低限度的设置。删除 Create React App 添加的基本样板代码，让我们开始吧。

## 使用 React-Calendar 添加日历

第一步是将日历添加到我们的 React 应用程序中。从`react-calendar`导入`Calendar`组件并添加到`app.js`文件中。该文件将类似于以下内容:

```
// App.js 

import { useState } from 'react';
import Calendar from 'react-calendar';
import './App.css';

function App() {
  const [date, setDate] = useState(new Date());

  return (
    <div className='app'>
      <h1 className='text-center'>React Calendar</h1>
      <div className='calendar-container'>
        <Calendar onChange={setDate} value={date} />
      </div>
      <p className='text-center'>
        <span className='bold'>Selected Date:</span>{' '}
        {date.toDateString()}
      </p>
    </div>
  );
}

export default App;

```

这里，我们向我们的`app.js`文件添加了一个`Calendar`组件，并向它添加了两个属性。我们创建了一个状态来存储日期，并使用 JavaScript 的`Date`对象传递当前日期作为初始值。

准备好这个基本结构后，我们的初始日历将如下所示:

![Calendar With Dates On A Rectangular Grid In Black, White, And Grey](img/1dc5b4164ef9ac58060ad4fe7de36304.png)

Basic React Calendar.

如果您检查`app.js`文件的代码，我们创建了一个名为`date`的状态，并将其作为一个值传递给`Calendar`组件。另一个道具`onChange`被传递给`Calendar`，后者将`date`状态设置为用户点击的值。

传递给日历的初始值是当前日期。当用户单击日历时，它的值将被设置为用户的选择。对于这个应用程序，我们在日历下面打印日期值。

## 设计您的 React 日历

我们已经完成了最基本的日历实现。但是正如您所看到的，这种样式还没有应用到日历上，所以看起来很无聊。

React-Calendar 提供了一些默认样式，您可以通过导入其样式表来应用这些样式。为此，将以下行添加到您的`app.js`文件中:

```
import 'react-calendar/dist/Calendar.css';

```

![Calendar With Dates On A Square Grid In Black And White](img/94f99a7d65dd905d7e8bd7fbb3314f8e.png)

React Calendar with default styling.

如果您想添加自己的样式，可以重写这些类并添加自定义 CSS 属性。

## 带有自定义 CSS 的日历样式

React-Calendar 的自定义样式看起来很不错。但是我们总是希望我们的组件与产品的整体风格保持一致。React-Calendar 的元素已经应用了一些类，我们可以用它们来添加我们的样式。

应用您的样式的最佳方式是覆盖`Calendar.css`。你可以将整个日历 CSS 从`node_modules/react-calendar/dist`复制到一个本地文件中，并将同一个文件导入到`App.js`中，而不是默认的日历 CSS 中。

我改变了日历的一些默认字体颜色和背景颜色，并在范围选择中添加了一些边框半径。在这里，我重写了一些类，并添加了我的自定义样式，以获得类似如下的结果:

```
.react-calendar { 
 width: 400px;
 max-width: 100%;
 background-color: #fff;
 color: #222;
 border-radius: 8px;
 box-shadow: 0 12px 24px rgba(0, 0, 0, 0.2);
 font-family: Arial, Helvetica, sans-serif;
 line-height: 1.125em;
}
.react-calendar__navigation button {
 color: #6f48eb;
 min-width: 44px;
 background: none;
 font-size: 16px;
 margin-top: 8px;
}
.react-calendar__navigation button:enabled:hover,
.react-calendar__navigation button:enabled:focus {
 background-color: #f8f8fa;
}
.react-calendar__navigation button[disabled] {
 background-color: #f0f0f0;
}
abbr[title] {
 text-decoration: none;
}
/* .react-calendar__month-view__days__day--weekend {
 color: #d10000;
} */
.react-calendar__tile:enabled:hover,
.react-calendar__tile:enabled:focus {
 background: #f8f8fa;
 color: #6f48eb;
 border-radius: 6px;
}
.react-calendar__tile--now {
 background: #6f48eb33;
 border-radius: 6px;
 font-weight: bold;
 color: #6f48eb;
}
.react-calendar__tile--now:enabled:hover,
.react-calendar__tile--now:enabled:focus {
 background: #6f48eb33;
 border-radius: 6px;
 font-weight: bold;
 color: #6f48eb;
}
.react-calendar__tile--hasActive:enabled:hover,
.react-calendar__tile--hasActive:enabled:focus {
 background: #f8f8fa;
}
.react-calendar__tile--active {
 background: #6f48eb;
 border-radius: 6px;
 font-weight: bold;
 color: white;
}
.react-calendar__tile--active:enabled:hover,
.react-calendar__tile--active:enabled:focus {
 background: #6f48eb;
 color: white;
}
.react-calendar--selectRange .react-calendar__tile--hover {
 background-color: #f8f8fa;
}
.react-calendar__tile--range {
 background: #f8f8fa;
 color: #6f48eb;
 border-radius: 0;
}
.react-calendar__tile--rangeStart {
 border-top-right-radius: 0;
 border-bottom-right-radius: 0;
 border-top-left-radius: 6px;
 border-bottom-left-radius: 6px;
 background: #6f48eb;
 color: white;
}
.react-calendar__tile--rangeEnd {
 border-top-left-radius: 0;
 border-bottom-left-radius: 0;
 border-top-right-radius: 6px;
 border-bottom-right-radius: 6px;
 background: #6f48eb;
 color: white;
}
```

## ![styling react calendar with custom css](img/b605d3da063830738cea1300c578d45a.png)

## 选择日期范围

考虑一个用例，您需要在一个自定义日期范围内提供一些数据。用户选择他们想要的日期范围，您可以选择该日期范围，然后执行其余的操作。React-Calendar 非常有效地支持这个特性。

让我们以这个用例为例，改进我们的应用程序来选择日期范围。我们将在日历底部打印范围的开始和结束。

修改后的`app.js`将如下所示:

```
// App.js

import { useState } from 'react';
import Calendar from 'react-calendar';
import 'react-calendar/dist/Calendar.css';
import './App.css';

function App() {
  const [date, setDate] = useState(new Date());

  return (
    <div className='app'>
      <h1 className='text-center'>React Calendar with Range</h1>
      <div className='calendar-container'>
        <Calendar
          onChange={setDate}
          value={date}
          selectRange={true}
        />
      </div>
      {date.length > 0 ? (
        <p className='text-center'>
          <span className='bold'>Start:</span>{' '}
          {date[0].toDateString()}
          &nbsp;|&nbsp;
          <span className='bold'>End:</span> {date[1].toDateString()}
        </p>
      ) : (
        <p className='text-center'>
          <span className='bold'>Default selected date:</span>{' '}
          {date.toDateString()}
        </p>
      )}
    </div>
  );
}

export default App;

```

为了启用日期范围特性，我们将`selectRange` prop 传递给我们的`Calendar`组件。`selectRange`的默认值为假。启用该属性后，React-Calendar 返回一个包含两个日期的数组:开始日期和结束日期。突出显示选定的范围，以便用户理解所做的选择。

添加日期范围后，日历组件将如下所示:

![Calendar With A Range Of Dates Highlighted In Blue](img/ff6e4838f5514864721a3065b63739a7.png)

React Calendar with selected date range.

## 自定义反应日历

现在我们已经掌握了 React-Calendar 最有用的功能，让我们更深入一点，探索定制日历的方法。

### `defaultValue`

`defaultValue`属性使您能够设置默认选择值。该属性还支持默认的日期范围选择。如果你想选择一个日期，你可以传递一个日期对象。否则，您可以传递包含开始和结束日期值的数组。

可以这样添加`defaultValue`:

```
// App.js

function App() {
  const [date, setDate] = useState([
    new Date(2021, 6, 1),
    new Date(2021, 6, 10),
  ]);

  return (
    <div className='app'>
      <h1 className='text-center'>React Calendar with Range</h1>
      <div className='calendar-container'>
        <Calendar
          onChange={setDate}
          selectRange={true}
          defaultValue={date}                                                     
        />
      </div>
    </div>
  );
}

```

### `defaultView`

这个属性用于设置日历的日期视图。默认情况下，它设置为月。所以如果这个道具不见了，日历会显示月视图。

`defaultView`提供月、年、十年、世纪四个选项。即使这个属性被设置为某个值，您仍然可以在不同的日期/月份之间导航。

下面是如何实现`defaultView`:

```
// Calendar component
<Calendar
  onChange={setDate}
  selectRange={true}
  defaultView='decade'                                                    
/>

```

这个道具将改变日历的初始呈现，使它看起来像这样:

![Calendar Showing Years 2021 through 2030](img/2d7a87dbde0434b4c01aaf413beddab5.png)

React Calendar with a decade view.

### `maxDate`和`minDate`

如果您不希望用户选择某一天之后的日期，您可以通过向您的日历添加一个`maxDate`道具来阻止此操作。正如您可能猜到的那样，`minDate`属性限制了用户可以选择多早的开始日期。

如果应用程序启用了日期范围，用户可能能够选择晚于`maxDate`或早于`minDate`的日期。React-Calendar 通过只传递`maxDate`或`minDate`值来防止这种情况，除此之外什么也不传递。

```
// calendar component

<Calendar
  onChange={setDate}
  value={date}
  maxDate={new Date()} // will not allow date later than today
  minDate={new Date(2015, 6, 1)} // will not allow date before 1st July 2015
/>

```

### `maxDetail`和`minDetail`

`maxDetail`和`minDetail`属性对于限制日历的粒度非常重要。`maxDetail`定义用户可以看到的细节的最大数量。如果`maxDetail`值设置为**年**，用户可以一次看到日历中一年的详细信息。

类似地，如果`minDetail`设置为 year，用户将无法看到给定年份之外的详细信息。

```
// calendar component

<Calendar
  onChange={setDate}
  value={date}
  maxDetail='year'
/>

```

下面是`maxDetail`和`minDetail`的例子:

### ![Max Detail Calendar Showing The Months In A Year And Min Detail Calendar Showing The Days In A Month](img/dd55fb913e4399e7995544cd9423a6e8.png)
`Next`和`Prev`标签

通过`Next`和`Prev`标签，您可以定义用于在日历视图间导航的按钮名称。您也可以使用`aria-label`属性使其可访问。

用于改变下一个值的道具有`nextLabel`、`nextAriaLabel`、`next2Label`和`next2AriaLabel`。您可以添加任何字符串，或者如果您想禁用此导航，您可以将其设置为`null`。

除了 prev 是前缀之外，这些属性与 prev 按钮类似，例如，`prevLabel`、`prevAriaLabel`、`prev2Label`、`prev2AriaLabel`等。

添加了`Next`和`Prev`标签后，代码将如下所示:

```
// calendar component

<Calendar
  onChange={setDate}
  value={date}
  nextLabel='month>>'
  nextAriaLabel='Go to next month'
  next2Label='year>>'
  next2AriaLabel='Go to next year'
  prevLabel='<<month'
  prevAriaLabel='Go to prev month'
  prev2Label='<<year'
  prev2AriaLabel='Go to prev year'
/>

```

## 单击 React-日历中的事件

React-Calendar 支持各种点击事件的触发。当你想根据用户的动作触发一些函数调用时，React-Calendar 可以提供最有效的支持。以下是一些使用最广泛的事件。

### `onChange`

当用户点击最详细视图的一个项目时，这个函数被调用。在此帮助下，我们可以通过以下方式使用`alert('Clicked date is: ', value)}/>`接收用户选择

### `onClickDay`

当用户点击某一天时，这个函数被调用:`alert('Clicked day is: ', value)}/>`。与`onClickDay`类似，React-Calendar 也支持`onClickDecade`、`onClickMonth`、`onClickYear`、`onClickWeekNumber`等事件。

### `onViewChange`

当用户使用向上钻取按钮或单击一个图块从一个视图导航到另一个视图时，就会调用这个函数。这给出了视图改变的原因，并且可以是以下值之一:`prev`、`prev2`、`next`、`next2`、`drillUp`、`drillDown`和`onChange`。下面举个例子:`alert('New view is: ', view)}/>`。

## 结论

React-Calendar 是一个非常棒的库，在实现上提供了很大的灵活性。它是高度可定制的，并且依赖于原生 JavaScript `Date`对象，这使得 React-Calendar 很容易在任何应用程序中实现。

前往官方文档，了解 React-Calendar 的一些[复杂示例和用例。我希望这篇教程能为你提供实现和定制 React-Calendar 所需的基础知识，以适应你的应用程序及其受众。](https://github.com/wojtekmaj/react-calendar/wiki/Recipes)

如果你对使用 React-Calendar 有任何疑问，欢迎在下面的评论中提问。

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