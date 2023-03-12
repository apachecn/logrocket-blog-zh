# Visx 简介

> 原文：<https://blog.logrocket.com/introduction-to-visx/>

Visx 是 Airbnb 开发的基于 React 的数据可视化工具集合。Visx 代表 visualization component，它不是一个可视化库，而是一个原语或组件的集合，可以根据项目的需求混合在一起创建一个自定义的可视化库。

虽然构建在流行的 D3 可视化库之上，但是 Visx 将 DOM 操作工作委托给了 React 库。同时，D3 主要用于计算。这样做的好处是减少了两个库争夺 DOM 控制权时可能出现的错误。

Visx 并不固执己见，这意味着它可以适应任何 React 应用程序，而不管其架构如何。也是纯 React，也就是说简单易学。此外，您可以随时从 Visx 工具箱中导入您需要的内容，从而保持您的代码库小而高效。

Visx 非常稳定可靠。它的开发始于 3 年多前的 Airbnb，唯一的目标是统一整个公司的可视化堆栈。它已经在 Airbnb 内部使用了 2 年多。

## 与其他可视化库的比较

根据 Airbnb 工程团队的说法，Visx 的主要目标是性能、可学性和表现力。在撰写本文时，这是一个非常罕见的组合，在任何其他基于 React 的前端可视化库中都不存在。

虽然 D3 在 React 中构建表达可视化方面是个老手，但它有一个非常陡峭的学习曲线。React 开发人员不能仅仅根据以前的 React 经验就开始使用它。还有其他可视化工具可以解决可学性的问题，比如 React-vis，但是这是以牺牲表达能力和性能为代价的。因此，Visx 不是 Airbnb 漫无目的的展示工程实力。它实际上为构建可视化应用程序的 React 工程师解决了一个巨大的问题。

### Visx 入门

开始使用 Visx 相当容易。您只需熟悉 Visx 组件及其属性。这些部件是正常的反应部件。

在本例中，我们将构建一个垂直堆积条形图，显示三个欧洲城市(巴黎、伦敦和柏林)14 天的日平均气温。该代码可在 [Github](https://github.com/EniolaOluwa/visx-chart) 上获得。

![Bar chart built with Visx.](img/04ee8cd482004749958f7067fd79a80b.png)

The final bar chart we’ll build with Visx.

## 装置

安装过程的第一步是安装 create-react-app 样板文件。运行`npx create-react-app visx-chart`开始。

下一步是安装 Visx 包。因为 Visx 是一个原语集合，所以将安装相当多的包，作为我们今天要创建的图表的构建块。

转到项目的根目录，运行下面的命令来安装软件包。如果你使用`npm`来管理你的应用程序包，记住用`npm install`替换`yarn add`。

```
yarn add @visx/shape @visx/group @visx/grid @visx/axis @visx/scale @visx/tooltip @visx/legend @visx/responsive
```

以下是每个已安装软件包的用途摘要:

`@visx/shape`包含数据可视化中使用的不同形状。对于这个示例项目，堆叠的垂直条将从这个包中获得。

`@visx/group`是 SVG `<g />`元素的一个更简单的实现，用于对其他 SVG 对象进行分组。

`@visx/grid`包含用于在图表中创建网格线的组件。

`@visx/axis`包含在为图表绘制轴时使用的组件。它非常灵活，因此可以定制。

`@visx/scale`是一个“帮助您将数据值映射到图形所需的物理像素大小的函数”的集合；根据 Visx [文档](https://airbnb.io/visx/docs/scale)。

包含钩子、组件和其他工具来简化可视化工具提示的添加。

用于向可视化添加不同类型图例的组件集合。

软件包安装过程的最后一步是运行`yarn add d3-time-format`来安装 d3-time-format 软件包，该软件包有助于将要显示的数据中的日期和时间转换为首选格式。

使可视化反应灵敏。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 图表组件

图表组件包含所有逻辑和组件，这些逻辑和组件放在一起生成最终的垂直堆叠条形图。在应用程序的`src`目录中创建一个名为`TemperatureBarStack.js`的文件，并将下面的代码粘贴到其中:

```
import React from "react";
import { BarStack } from "@visx/shape";
import { Group } from "@visx/group";
import { Grid } from "@visx/grid";
import { AxisBottom } from "@visx/axis";
import { scaleBand, scaleLinear, scaleOrdinal } from "@visx/scale";
import { timeFormat, timeParse } from "d3-time-format";
import { useTooltip, useTooltipInPortal, defaultStyles } from "@visx/tooltip";
import { LegendOrdinal } from "@visx/legend";
import "./index.css";

const purple1 = "#6c5efb";
const purple2 = "#c998ff";
const purple3 = "#a44afe";
const background = "#eaedff";
const defaultMargin = { top: 40, right: 0, bottom: 0, left: 0 };
const tooltipStyles = {
  ...defaultStyles,
  minWidth: 60,
  backgroundColor: "rgba(0,0,0,0.9)",
  color: "white"
};

const data = [
  {
    date: "2020-10-01",
    London: "60",
    Paris: "68",
    Berlin: "61"
  },
  {
    date: "2020-10-02",
    London: "57",
    Paris: "58",
    Berlin: "62"
  },
  {
    date: "2020-10-03",
    London: "59",
    Paris: "57",
    Berlin: "72"
  },
  {
    date: "2020-10-04",
    London: "52",
    Paris: "59",
    Berlin: "68"
  },
  {
    date: "2020-10-05",
    London: "63",
    Paris: "57",
    Berlin: "63"
  },
  {
    date: "2020-10-06",
    London: "61",
    Paris: "62",
    Berlin: "61"
  },
  {
    date: "2020-10-07",
    London: "61",
    Paris: "64",
    Berlin: "61"
  },
  {
    date: "2020-10-08",
    London: "64",
    Paris: "66",
    Berlin: "60"
  },
  {
    date: "2020-10-09",
    London: "58",
    Paris: "62",
    Berlin: "60"
  },
  {
    date: "2020-10-10",
    London: "56",
    Paris: "59",
    Berlin: "55"
  },
  {
    date: "2020-10-11",
    London: "57",
    Paris: "58",
    Berlin: "52"
  },
  {
    date: "2020-10-12",
    London: "56",
    Paris: "58",
    Berlin: "54"
  },
  {
    date: "2020-10-13",
    London: "52",
    Paris: "56",
    Berlin: "55"
  },
  {
    date: "2020-10-14",
    London: "58",
    Paris: "57",
    Berlin: "51"
  }
];
const keys = ["London", "Paris", "Berlin"];

const temperatureTotals = data.reduce((allTotals, currentDate) => {
  const totalTemperature = keys.reduce((dailyTotal, k) => {
    dailyTotal += Number(currentDate[k]);
    return dailyTotal;
  }, 0);

  allTotals.push(totalTemperature);
  return allTotals;
}, []);

const parseDate = timeParse("%Y-%m-%d");
const format = timeFormat("%b %d");
const formatDate = (date) => format(parseDate(date));

const getDate = (d) => d.date;

const dateScale = scaleBand({ domain: data.map(getDate), padding: 0.2 });
const temparatureScale = scaleLinear({
  domain: [0, Math.max(...temperatureTotals)],
  nice: true
});
const colorScale = scaleOrdinal({
  domain: keys,
  range: [purple1, purple2, purple3]
});

let tooltipTimeout;

export default function TemperatureBarStack({
  width,
  height,
  event = false,
  margin = defaultMargin
}) {
  const {
    tooltipOpen,
    tooltipTop,
    tooltipLeft,
    hideTooltip,
    showTooltip,
    tooltipData
  } = useTooltip();

  const { containerRef, TooltipInPortal } = useTooltipInPortal();

  if (width < 10) return null;

  const xMax = width;
  const yMax = height - margin.top - 100;

  dateScale.rangeRound([0, xMax]);
  temparatureScale.range([yMax, 0]);

  return width < 10 ? null : (
    <div style={{ position: "relative" }}>
      <svg ref={containerRef} width={width} height={height}>
        <rect
          x={0}
          y={0}
          width={width}
          height={height}
          fill={background}
          rx={14}
        />
        <Grid
          top={margin.top}
          left={margin.left}
          xScale={dateScale}
          yScale={temparatureScale}
          width={xMax}
          height={yMax}
          stroke="black"
          strokeOpacity={0.1}
          xOffset={dateScale.bandwidth() / 2}
        />
        <Group top={margin.top}>
          <BarStack
            data={data}
            keys={keys}
            x={getDate}
            xScale={dateScale}
            yScale={temparatureScale}
            color={colorScale}
          >
            {(barStacks) =>
              barStacks.map((barStack) =>
                barStack.bars.map((bar) => (
                  <rect
                    key={`bar-stack-${barStack.index}-${bar.index}`}
                    x={bar.x}
                    y={bar.y}
                    height={bar.height}
                    width={bar.width}
                    fill={bar.color}
                    onClick={() => {
                      if (event) alert(`Clicked: ${JSON.stringify(bar)}`);
                    }}
                    onMouseLeave={() => {
                      tooltipTimeout = window.setTimeout(() => {
                        hideTooltip();
                      }, 300);
                    }}
                    onMouseMove={(event) => {
                      if (tooltipTimeout) clearTimeout(tooltipTimeout);
                      const top = event.clientY - margin.top - bar.height;
                      const left = bar.x + bar.width / 2;
                      showTooltip({
                        tooltipData: bar,
                        tooltipTop: top,
                        tooltipLeft: left
                      });
                    }}
                  />
                ))
              )
            }
          </BarStack>
        </Group>
        <AxisBottom
          top={yMax + margin.top}
          scale={dateScale}
          tickFormat={formatDate}
          stroke={purple3}
          tickStroke={purple3}
          tickLabelProps={() => ({
            fill: purple3,
            fontSize: 11,
            textAnchor: "middle"
          })}
        />
      </svg>
      <div
        style={{
          position: "absolute",
          top: margin.top / 2 - 10,
          width: "100%",
          display: "flex",
          justifyContent: "center",
          fontSize: 14
        }}
      >
        <LegendOrdinal
          scale={colorScale}
          direction="row"
          labelMargin="0 15px 0 0"
        />
      </div>
      {tooltipOpen && tooltipData && (
        <TooltipInPortal
          key={Math.random()}
          top={tooltipTop}
          left={tooltipLeft}
          style={tooltipStyles}
        >
          <div style={{ color: colorScale(tooltipData.key) }}>
            <strong>{tooltipData.key}</strong>
          </div>
          <div>{tooltipData.bar.data[tooltipData.key]}℉</div>
          <div>
            <small>{formatDate(getDate(tooltipData.bar.data))}</small>
          </div>
        </TooltipInPortal>
      )}
    </div>
  );
}

```

下面是对上面代码的解释:

预安装的`@visx`包和可视化的 CSS 样式被导入。

```
import React from "react";
import { BarStack } from "@visx/shape";
import { Group } from "@visx/group";
import { Grid } from "@visx/grid";
import { AxisBottom } from "@visx/axis";
import { scaleBand, scaleLinear, scaleOrdinal } from "@visx/scale";
import { timeFormat, timeParse } from "d3-time-format";
import { useTooltip, useTooltipInPortal, defaultStyles } from "@visx/tooltip";
import { LegendOrdinal } from "@visx/legend";
import "./styles.css";
```

这是定义可视化的重要样式属性的地方。

此外，要可视化的数据(即`data`)与包含每个温度值标签的`keys`数组一起提供。这些键将被映射到堆叠图表的适当部分，并且也用于创建图表的图例。

```
const purple1 = "#6c5efb";
const purple2 = "#c998ff";
const purple3 = "#a44afe";
const background = "#eaedff";
const defaultMargin = { top: 40, right: 0, bottom: 0, left: 0 };
const tooltipStyles = {
  ...defaultStyles,
  minWidth: 60,
  backgroundColor: "rgba(0,0,0,0.9)",
  color: "white"
};

const data = [...];
const keys = ["London", "Paris", "Berlin"];
```

这里的数据是为图表准备的。`temperatureTotals`数组包含三个城市每天的平均温度之和。这三个城市的每日气温总和决定了每天堆积条形图的高度。

`formatDate()`函数会将日期转换成可读性更好的格式——在这个场景中，它会将`2020-10-14`改为`Oct 14`。

```
const temperatureTotals = data.reduce((allTotals, currentDate) => {
  const totalTemperature = keys.reduce((dailyTotal, k) => {
    dailyTotal += Number(currentDate[k]);
    return dailyTotal;
  }, 0);

  allTotals.push(totalTemperature);
  return allTotals;
}, []);

const parseDate = timeParse("%Y-%m-%d");
const format = timeFormat("%b %d");
const formatDate = (date) => format(parseDate(date));

const getDate = (d) => d.date;
```

`@visx/scale`基于 d3 级封装。它包含接收数据并返回可视值的函数。下面，`scaleBand()`负责条的水平属性，即宽度和填充。`scaleLinear()`负责条形的垂直属性。最后，`scaleOrdinal()`负责将各个城市映射到一组预定义的颜色。你可以在 d3 [这里](https://medium.com/@mbostock/introducing-d3-scale-61980c51545f)阅读更多关于音阶的内容。

```
const dateScale = scaleBand({ domain: data.map(getDate), padding: 0.2 });
const temparatureScale = scaleLinear({
  domain: [0, Math.max(...temperatureTotals)],
  nice: true
});
const colorScale = scaleOrdinal({
  domain: keys,
  range: [purple1, purple2, purple3]
});

let tooltipTimeout;
```

这是 React 函数组件，将返回一个垂直堆叠的条形图。几个道具——`width`、`height`、`event`和`margin`将从`ParentSize`组件传递给它(稍后将详细介绍)。

另外，`useTooltip`包含了帮助设置工具提示物理特征显示的属性和功能。此外，我们设置了`useTooltipInPortal`钩子，它在[门户](https://airbnb.io/visx/docs/tooltip#Portal)中呈现工具提示。

```
export default function TemperatureBarStack({
  width,
  height,
  event = false,
  margin = defaultMargin
}) {
  const {
    tooltipOpen,
    tooltipTop,
    tooltipLeft,
    hideTooltip,
    showTooltip,
    tooltipData
  } = useTooltip();

  const { containerRef, TooltipInPortal } = useTooltipInPortal();
  ...
}
```

这里，如果宽度不超过 10px，组件被设置为返回 null。

另外:

```
if (width < 10) return null;

  const xMax = width;
  const yMax = height - margin.top - 100;

  dateScale.rangeRound([0, xMax]);
  temparatureScale.range([yMax, 0]);
```

这是使用大多数原语的地方。可视化是基于 SVG 的。因此，我们需要为图表使用一个父元素`SVG`。元素负责图表的矩形背景。`Grid`组件负责在图表背景中绘制网格线。

如前所述，`Group`有助于围绕一组`SVG`元素创建一个容器。`BarStack`组件将数据呈现在垂直堆叠的条形图中。它还管理生成的 bar 订阅的事件。`AxisButtom`用于自定义图表的底轴。

`LegendOrdinal`用于设置图表图例，最后，`TooltipInPortal`组件将管理工具提示的显示。

```
return width < 10 ? null : (
    <div style={{ position: "relative" }}>
      <svg ref={containerRef} width={width} height={height}>
        <rect
          x={0}
          y={0}
          width={width}
          height={height}
          fill={background}
          rx={14}
        />
        <Grid
          top={margin.top}
          left={margin.left}
          xScale={dateScale}
          yScale={temparatureScale}
          width={xMax}
          height={yMax}
          stroke="black"
          strokeOpacity={0.1}
          xOffset={dateScale.bandwidth() / 2}
        />
        <Group top={margin.top}>
          <BarStack
            data={data}
            keys={keys}
            x={getDate}
            xScale={dateScale}
            yScale={temparatureScale}
            color={colorScale}
          >
            {(barStacks) =>
              barStacks.map((barStack) =>
                barStack.bars.map((bar) => (
                  <rect
                    key={`bar-stack-${barStack.index}-${bar.index}`}
                    x={bar.x}
                    y={bar.y}
                    height={bar.height}
                    width={bar.width}
                    fill={bar.color}
                    onClick={() => {
                      if (event) alert(`Clicked: ${JSON.stringify(bar)}`);
                    }}
                    onMouseLeave={() => {
                      tooltipTimeout = window.setTimeout(() => {
                        hideTooltip();
                      }, 300);
                    }}
                    onMouseMove={(event) => {
                      if (tooltipTimeout) clearTimeout(tooltipTimeout);
                      const top = event.clientY - margin.top - bar.height;
                      const left = bar.x + bar.width / 2;
                      showTooltip({
                        tooltipData: bar,
                        tooltipTop: top,
                        tooltipLeft: left
                      });
                    }}
                  />
                ))
              )
            }
          </BarStack>
        </Group>
        <AxisBottom
          top={yMax + margin.top}
          scale={dateScale}
          tickFormat={formatDate}
          stroke={purple3}
          tickStroke={purple3}
          tickLabelProps={() => ({
            fill: purple3,
            fontSize: 11,
            textAnchor: "middle"
          })}
        />
      </svg>
      <div
        style={{
          position: "absolute",
          top: margin.top / 2 - 10,
          width: "100%",
          display: "flex",
          justifyContent: "center",
          fontSize: 14
        }}
      >
        <LegendOrdinal
          scale={colorScale}
          direction="row"
          labelMargin="0 15px 0 0"
        />
      </div>
      {tooltipOpen && tooltipData && (
        <TooltipInPortal
          key={Math.random()}
          top={tooltipTop}
          left={tooltipLeft}
          style={tooltipStyles}
        >
          <div style={{ color: colorScale(tooltipData.key) }}>
            <strong>{tooltipData.key}</strong>
          </div>
          <div>{tooltipData.bar.data[tooltipData.key]}℉</div>
          <div>
            <small>{formatDate(getDate(tooltipData.bar.data))}</small>
          </div>
        </TooltipInPortal>
      )}
    </div>
  );
```

### 式样

用下面的代码替换`index.css`文件中的 CSS 代码:

```
html,
body,
#root {
  height: 100%;
  line-height: 2em;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Oxygen,
    Ubuntu, Cantarell, "Open Sans", "Helvetica Neue", sans-serif;
}
```

### 响应性

Visx 有一些实用程序可以使可视化响应迅速。`ParentSize`就是其中之一。它将父组件的宽度和高度向下传递给包装的组件。要实现这个项目，用下面的代码替换`index.js`文件的内容:

```
import React from "react";
import ReactDOM from "react-dom";
import ParentSize from "@visx/responsive/lib/components/ParentSize";

import TemperatureBarStack from "./TemperatureBarStack";

ReactDOM.render(

    {({ width, height }) => (

    )}
  ,
  document.getElementById("root")
);
```

不要忘记使用`yarn start`运行 React 应用程序。

## 结论

在数据可视化方面，Visx 绝对是 React 开发人员的游戏规则改变者，因为它具有可学性和表达性。然而，由于用 React 构建它的决定显然受到了 React 是 Airbnb 的主要前端库这一事实的影响，所以可能永远也不会看到类似的可视化包被开发出来。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)