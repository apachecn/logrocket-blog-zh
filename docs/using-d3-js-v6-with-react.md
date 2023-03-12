# 在 React 中使用 D3.js v6

> 原文：<https://blog.logrocket.com/using-d3-js-v6-with-react/>

React 是世界上最流行的前端 web 框架，D3.js 是最流行的操纵屏幕图形的 JavaScript 库之一。

版本 6 是 D3.js 的最新版本，在本文中，我们将看看如何在 React 应用程序中使用 D3.js v6。

## 入门指南

首先，我们将使用 create-react-app 创建一个 React 项目，如下所示:

```
npx create-react-app d3-app
cd d3-app
npm start
```

然后我们安装 D3.js 包:

```
npm i d3
```

现在，我们可以在 React 应用程序中添加 D3 来添加一些图形。

## 在 React 应用中使用 D3.js v6

我们可以在应用程序中使用 D3，方法是将 D3 代码放在`useEffect`钩子回调中。这是因为我们用 DOM 选择了一个元素，然后用 D3 修改了它。

例如，我们可以写:

```
import React, { useEffect } from "react";
import * as d3 from "d3";

export default function App() {
  useEffect(() => {
    d3.select(".target").style("stroke-width", 5);
  }, []);
  return (
    <div className="App">
      <svg>
        <circle
          class="target"
          style={{ fill: "green" }}
          stroke="black"
          cx={50}
          cy={50}
          r={40}
        ></circle>
      </svg>
    </div>
  );
}
```

我们用`target`类获取 SVG，然后在选择它之后更改`stroke-width`。

我们也可以将 D3 代码放在一个函数中，当我们想使用它时就调用它。例如，我们可以写:

```
import React from "react";
import * as d3 from "d3";

export default function App() {
  const changeStroke = () => {
    d3.select(".target").style("stroke-width", 5);
  };

  return (
    <div className="App">
      <button onClick={changeStroke}>change stroke</button>
      <svg>
        <circle
          class="target"
          style={{ fill: "green" }}
          stroke="black"
          cx={50}
          cy={50}
          r={40}
        ></circle>
      </svg>
    </div>
  );
}
```

我们在`changeStroke`函数中设置 D3 代码来改变圆的笔划，并在单击按钮时调用它。

我们还可以将所有内容添加到另一个`svg`元素中。例如，我们可以通过编写以下代码来添加三个圆:

```
import React, { useEffect } from "react";
import * as d3 from "d3";

export default function App() {
  useEffect(() => {
    const svg = d3.select("#area");
    svg
      .append("circle")
      .attr("cx", 50)
      .attr("cy", 50)
      .attr("r", 40)
      .style("fill", "blue");
    svg
      .append("circle")
      .attr("cx", 140)
      .attr("cy", 70)
      .attr("r", 40)
      .style("fill", "red");
    svg
      .append("circle")
      .attr("cx", 300)
      .attr("cy", 100)
      .attr("r", 40)
      .style("fill", "green");
  }, []);

  return (
    <div className="App">
      <svg id="area" height={200} width={450}></svg>
    </div>
  );
}
```

让我们来分解一下:

*   带有`'circle'`参数的`append`方法添加圆
*   `attr`方法调用为圆添加属性
*   `cx`是圆心的 x 坐标
*   `cy`是圆心的 y 坐标
*   `r`是半径
*   `style`包含我们想要放入圆的`style`属性中的属性和值

## 缩放图形

通过使用`scaleLinear`方法，我们可以在 React 应用程序中用 D3.js 缩放图形。例如，我们可以编写以下代码，将 x 轴添加到我们的图表中:

```
import React, { useEffect } from "react";
import * as d3 from "d3";

export default function App() {
  useEffect(() => {
    const margin = { top: 10, right: 40, bottom: 30, left: 30 },
      width = 450 - margin.left - margin.right,
      height = 400 - margin.top - margin.bottom;

    const svg = d3
      .select("#area")
      .append("svg")
      .attr("width", width + margin.left + margin.right)
      .attr("height", height + margin.top + margin.bottom)
      .append("g")
      .attr("transform", `translate(${margin.left}, ${margin.top})`);

    const x = d3.scaleLinear().domain([0, 100]).range([0, width]);
    svg
      .append("g")
      .attr("transform", `translate(0, ${height})`)
      .call(d3.axisBottom(x));

    const y = d3.scaleLinear().domain([0, 100]).range([height, 0]);
    svg.append("g").call(d3.axisLeft(y));
  }, []);

  return (
    <div className="App">
      <svg id="area" height={400} width={500}></svg>
    </div>
  );
}
```

我们简单地用我们的`x`函数调用`d3.axisBottom`来添加 x 轴。它将把我们传递给`domain`函数的最小值和最大值相加。

## 将 y 轴添加到图表中

不用说，我们可以用 D3 给一个图添加一个 y 轴。

在上面的代码中，你会注意到我们添加了`margin`对象来让我们更容易地设置图形的边距。然后，我们使用以下代码将`svh`对象附加到页面主体:

```
const svg = d3
  .select("#area")
  .append("svg")
  .attr("width", width + margin.left + margin.right)
  .attr("height", height + margin.top + margin.bottom)
  .append("g")
  .attr("transform", `translate(${margin.left}, ${margin.top})`);
```

我们设置`width`和`height`，然后`translate`物体到我们想要的位置。

接下来，我们通过书写来添加 x 轴和刻度:

```
const x = d3.scaleLinear().domain([0, 100]).range([0, width]);
svg
  .append("g")
  .attr("transform", `translate(0, ${height})`)
  .call(d3.axisBottom(x));
```

我们像以前一样调用`domain`和`range`，我们调用`d3.axisBottom`方法来添加 x 轴。

然后，添加 y 轴，我们写:

```
const y = d3.scaleLinear().domain([0, 100]).range([height, 0]);
svg.append("g").call(d3.axisLeft(y));
```

我们调用了`domain`和`range`来创建 y 轴的最小值和最大值，然后我们调用`d3.axisLeft`来添加 y 轴。

## 创建散点图

要将点/点添加到图表中以创建散点图，我们可以像前面一样添加圆形。例如，我们可以编写以下内容来创建完整的散点图:

```
import React, { useEffect } from "react";
import * as d3 from "d3";

const DATA = [
  { x: 10, y: 20 },
  { x: 20, y: 50 },
  { x: 80, y: 90 }
];

export default function App() {
  useEffect(() => {
    const margin = { top: 10, right: 40, bottom: 30, left: 30 },
      width = 450 - margin.left - margin.right,
      height = 400 - margin.top - margin.bottom;

    const svg = d3
      .select("#area")
      .append("svg")
      .attr("width", width + margin.left + margin.right)
      .attr("height", height + margin.top + margin.bottom)
      .append("g")
      .attr("transform", `translate(${margin.left}, ${margin.top})`);

    const x = d3.scaleLinear().domain([0, 100]).range([0, width]);
    svg
      .append("g")
      .attr("transform", `translate(0, ${height})`)
      .call(d3.axisBottom(x));

    const y = d3.scaleLinear().domain([0, 100]).range([height, 0]);
    svg.append("g").call(d3.axisLeft(y));

    svg
      .selectAll("whatever")
      .data(DATA)
      .enter()
      .append("circle")
      .attr("cx", (d) => x(d.x))
      .attr("cy", (d) => y(d.y))
      .attr("r", 7);
  }, []);

  return (
    <div className="App">
      <svg id="area" height={400} width={500}></svg>
    </div>
  );
}
```

让我们把它分解一下。您会注意到我们在`useEffect`回调中添加了这段代码来呈现散点图上的点:

```
svg
  .selectAll("whatever")
  .data(DATA)
  .enter()
  .append("circle")
  .attr("cx", (d) => x(d.x))
  .attr("cy", (d) => y(d.y))
  .attr("r", 7);
```

我们用`data`方法读取数据。`DATA`数组是:

```
const DATA = [
  { x: 10, y: 20 },
  { x: 20, y: 50 },
  { x: 80, y: 90 }
];
```

然后，为了获得相对于域的 x 和 y 坐标，我们使用带点的`x`和`y`方法。这些值在 0 到 100 之间，它们将自动与`width`和`height`值成比例缩放。

所以如果`x`是 10，那么在屏幕上，`cx`就是`10 * (450 - margin.left - margin.right)`；如果`y`是 20，那么在屏幕上，`cy`就是`20 * (400 - margin.top - margin.bottom)`。

## 从数据创建图表

D3 最常见的用例之一是从数据中创建图表。为此，我们可以从 CSV 中读取数据。

例如，我们可以从`src`文件夹中的`data.csv`文件中读取数据:

```
date,close
01-May-20,58.13
30-Apr-20,53.98
27-Apr-20,67.00
26-Apr-20,89.70
25-Apr-20,99.00
24-Apr-20,130.28
23-Apr-20,166.70
20-Apr-20,234.98
19-Apr-20,345.44
18-Apr-20,443.34
17-Apr-20,543.70
16-Apr-20,580.13
13-Apr-20,605.23
12-Apr-20,622.77
11-Apr-20,626.20
10-Apr-20,628.44
09-Apr-20,636.23
05-Apr-20,633.68
04-Apr-20,624.31
03-Apr-20,629.32
02-Apr-20,618.63
```

在`App.js`中，我们写道:

```
import React, { useEffect } from "react";
import * as d3 from "d3";
import "d3-time-format";
const parseTime = d3.timeParse("%d-%b-%y");

const createGraph = async () => {
  const margin = { top: 20, right: 20, bottom: 50, left: 70 },
    width = 960 - margin.left - margin.right,
    height = 500 - margin.top - margin.bottom;
  const x = d3.scaleTime().range([0, width]);
  const y = d3.scaleLinear().range([height, 0]);

  const valueLine = d3.line()
    .x((d) => { return x(d.date); })
    .y((d) => { return y(d.close); });

  const svg = d3.select("body").append("svg")
    .attr("width", width + margin.left + margin.right)
    .attr("height", height + margin.top + margin.bottom)
    .append("g")
    .attr("transform", `translate(${margin.left}, ${margin.top})`);

  let data = await d3.csv(require("./data.csv"))

  data.forEach((d) => {
    d.date = parseTime(d.date);
    d.close = +d.close;
  });

  data = data.sort((a, b) => +a.date - +b.date)

  x.domain(d3.extent(data, (d) => { return d.date; }));
  y.domain([0, d3.max(data, (d) => { return d.close; })]);

  svg.append("path")
    .data([data])
    .attr("class", "line")
    .attr("d", valueLine);

  svg.append("g")
    .attr("transform", `translate(0, ${height})`)
    .call(d3.axisBottom(x));

  svg.append("g")
    .call(d3.axisLeft(y));
}

export default function App() {
  useEffect(() => {
    createGraph();
  }, []);

  return (
    <div>
      <style>{
        `
          .line {
            fill: none;
            stroke: steelblue;
            stroke-width: 2px;
          }
      `}
      </style>
    </div>
  );
}
```

我们将图形创建代码移至`createGraph`函数。我们还通过运行`npm i d3-time-format`添加了`d3-time-format`库。然后我们可以像在这个文件上一样导入它。

我们以与前面的例子相似的方式创建了轴。我们像这样创建`x`和`y`函数，这样我们以后可以用它们创建轴:

```
const x = d3.scaleTime().range([0, width]);
const y = d3.scaleLinear().range([height, 0]);
```

图表的容器是通过以下方式创建的:

```
const svg = d3.select("body").append("svg")
  .attr("width", width + margin.left + margin.right)
  .attr("height", height + margin.top + margin.bottom)
  .append("g")
  .attr("transform", `translate(${margin.left}, ${margin.top})`);
```

然后，我们使用以下内容读取 CSV 数据:

```
let data = await d3.csv(require("./data.csv"))
```

我们解析时间并将`close`属性改为数字:

```
data.forEach((d) => {
  d.date = parseTime(d.date);
  d.close = +d.close;
});
```

然后我们按日期对数据进行排序，如下所示:

```
data = data.sort((a, b) => +a.date - +b.date)
```

我们使用以下代码添加该行:

```
svg.append("path")
  .data([data])
  .attr("class", "line")
  .attr("d", valueLine);
```

我们分别添加 x 轴和 y 轴:

```
svg.append("g")
  .attr("transform", `translate(0, ${height})`)
  .call(d3.axisBottom(x));
```

```
svg.append("g")
  .call(d3.axisLeft(y));
```

最后，在`App`组件中，我们编写:

```
<style>{
  `
    .line {
      fill: none;
      stroke: steelblue;
      stroke-width: 2px;
    }
`}
```

这样，我们将看到一条线，而不是填充的形状。

## 结论

正如你刚刚看到的，我们可以轻松地用 D3.js 创建图形。它与 React 应用程序配合良好，无需额外工作即可集成。

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