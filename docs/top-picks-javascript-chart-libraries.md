# JavaScript 图表库的 5 个最佳选择

> 原文：<https://blog.logrocket.com/top-picks-javascript-chart-libraries/>

在商业应用中，我们经常需要在图表中显示数据。为了使这项任务更容易，有许多 JavaScript 图表库来使我们作为前端开发人员的生活更容易。在本文中，我们将研究五个可以用来制作和显示图表的 JavaScript 图表库。

## Chart.js

[Chart.js](https://www.chartjs.org/) 是一个图表库，作为客户端 JavaScript 包提供。还有其他前端框架的衍生产品，如 React、Vue 和 Angular。它在 HTML 画布元素上显示图表。

要使用它，我们可以通过 CDN 包含 Chart.js 库。然后我们添加一个`canvas`元素来显示我们的图表。

我们的 HTML 代码如下:

```
<script src='https://cdn.jsdelivr.net/npm/[email protected]/dist/Chart.min.js'></script>
<canvas id="chart" width="400" height="400"></canvas>
```

然后，在我们的 JavaScript 代码中，我们编写:

```
const ctx = document.getElementById('chart').getContext('2d');
const chart = new Chart(ctx, {
  type: 'bar',
  data: {
    labels: ['red', 'green', 'blue'],
    datasets: [{
      label: '# of Votes',
      data: [12, 19, 3],
      backgroundColor: [
        'red',
        'green',
        'blue'
      ],
      borderColor: [
        'red',
        'green',
        'blue'
      ],
      borderWidth: 1
    }]
  },
  options: {
    scales: {
      yAxes: [{
        ticks: {
          beginAtZero: true
        }
      }]
    }
  }
});

```

我们来分解一下上面的例子。我们获得刚刚创建的 canvas 元素，并用它创建一个`Chart`实例。`label`是图例的标签，`datasets`是条形图上每个条形的数据。

`backgroundColor`和`borderColor`，顾名思义，分别设置每个条的颜色和每个条的边框颜色。`borderWidth`是每个条形的宽度，以像素为单位。

在`options`中，我们可以设置图表的选项。我们有`yAxes`来设置 y 轴的一些选项，我们指定`beginAtZero`到`true`，这样 y 轴从 0 开始。

为了使我们的图表具有响应性，我们可以在`options`对象中指定`responsive`选项，并将其设置为`true`。

## 宪章派

Chartist 是另一个让我们创建简单图表的库。它还让我们创建响应图表。为了用 Chartist 创建与上面相同的图表，我们首先添加以下 HTML:

```
<script src='https://cdn.jsdelivr.net/npm/[email protected]/dist/chartist.min.js'></script>
<link rel="stylesheet" href="//cdn.jsdelivr.net/chartist.js/latest/chartist.min.css">
<div class="ct-chart ct-perfect-fourth"></div>
```

然后我们必须用 CSS 来改变条形的颜色:

```
.ct-chart .ct-bar:nth-of-type(1) {
  stroke: red;
}

.ct-chart .ct-bar:nth-of-type(2) {
  stroke: green;
}

.ct-chart .ct-bar:nth-of-type(3) {
  stroke: blue;
}
```

这将设置三个条形的颜色。然后，我们编写以下 JavaScript 代码:

```
const data = {
  labels: ['red', 'green', 'blue'],
  series: [
    [12, 9, 3]
  ]
};

const options = {
  high: 10,
  low: 0,
};
new Chartist.Bar('.ct-chart', data);
```

`data`对象在`labels`数组中有标签名称，而`series`数组中有我们想要显示的条形的大小。

`options`具有在 y 轴上显示的数值范围。y 轴上的最小值为 0，最大值为 10。

## C3.js

[C3.js](https://c3js.org/) 是另一个易于使用的 JavaScript 库，用于创建图表。它使用 D3 图形库，所以要用它创建图表，我们需要 D3 和 C3 库本身。

我们首先在 HTML 代码中包含 D3 和 C3 JavaScript 和 CSS 文件:

```
<script src="https://cdnjs.cloudflare.com/ajax/libs/c3/0.7.15/c3.min.js"></script>
<script src="https://d3js.org/d3.v5.min.js"></script>
<link href="https://cdnjs.cloudflare.com/ajax/libs/c3/0.7.15/c3.min.css" rel="stylesheet">

<div id="chart"></div>
```

默认情况下，C3 在 ID 为`chart`的 div 中显示图表。如果我们想在不同的元素中显示图表，我们可以将`bindto`属性添加到传递给`c3.generate`函数的对象中。

为了创建条形图，我们添加了以下 JavaScript 代码:

```
const chart = c3.generate({
  bindto: '#chart',
  data: {
    columns: [
      ['red', 12],
      ['green', 9],
      ['blue', 3]
    ],
    type: 'bar',
    colors: {
      red: 'red',
      green: 'green',
      blue: 'blue'
    },
  },
  bar: {
    width: {
      ratio: 0.5
    }
  }
});
```

我们使用`c3.generate`函数来创建图表。它只需要一个带有我们想要更改的所有选项的对象来呈现图表:

*   `columns`有列选项。每个条目都有列名和值。
*   `type`拥有我们想要显示的图表类型。
*   `colors`有各条的颜色。关键字是列名，关键字是颜色名。
*   `bar`有条形选项。条的宽度和宽度的比率。

其他种类的图表也有自己的选项。

## MetricsGraphics.js

MetricsGraphics.js 是另一个基于 D3 的图表库，让我们可以轻松地创建图表。它只支持用折线图、散点图、直方图和数据表显示时序数据。

我们可以通过包含 JavaScript 和 CSS 来使用它，如下所示:

```
<script src='https://cdnjs.cloudflare.com/ajax/libs/jquery/3.5.0/jquery.min.js'></script>
<script src="https://d3js.org/d3.v5.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/metrics-graphics/2.15.6/metricsgraphics.min.js"></script>
<link href="https://cdnjs.cloudflare.com/ajax/libs/metrics-graphics/2.15.6/metricsgraphics.min.css">

<div id="chart"></div>
```

显示工具提示需要 jQuery。然后，我们可以通过编写以下 JavaScript 代码来显示图表:

```
MG.data_graphic({
  title: "# of downloads",
  data: [{
      'date': new Date('2014-11-01'),
      'value': 12
    },
    {
      'date': new Date('2014-11-02'),
      'value': 9
    }
  ],
  width: 600,
  height: 250,
  color: 'green',
  target: '#chart',
  x_accessor: 'date',
  y_accessor: 'value',
})
```

在`data`属性中，我们有一个带有`date`和`value`属性的对象数组，它们分别具有日期和与日期对应的值。

`width`和`height`具有图形的尺寸，`color`具有线条和 x 轴之间的颜色值，`x_accessor`和`y_accessor`是 x 轴和 y 轴从中获取值的键。使用上面的代码，我们会得到一个在直线和 x 轴之间的绿色线图。

## Plotly

是一个图形库，可用于各种运行时环境，包括浏览器。它支持多种图表和图形，我们可以用各种选项对其进行配置。

内置了对折线图、条形图、饼图和散点图等基本图表的支持。还支持更复杂的图表，如误差线、箱线图和直方图。它还支持渲染地图和 3D 图表。

要开始，我们只需要包括 Plotly 库本身。我们首先添加以下 HTML:

```
<script src='https://cdn.plot.ly/plotly-latest.min.js'></script>

<div id="chart"></div>
```

然后我们可以添加以下 JavaScript 来创建一个条形图:

```
const data = [{
  x: ['red', 'green', 'blue'],
  y: [12, 9, 3],
  type: 'bar',
  marker: {
    color: ['red', 'green', 'blue']
  }
}];

const layout = {
  title: '# of votes',
};

Plotly.newPlot('chart', data, layout);
```

`data`数组有一个对象，它的 x 轴值存储在`x`属性中。`y`属性有酒吧高度的数据。

`type`指定了要呈现的图表类型。在我们的例子中，`'bar'`指定我们想要呈现一个条形图。`marker`有`color`属性为条形颜色。

`layout`是具有各种布局选项的对象。在我们的例子中，我们设置`title`属性来设置图表的标题，我们得到一个显示红色、绿色和蓝色的图表，就像大多数其他例子一样。

## 结论

上面的五个库都让我们可以轻松地创建图表。如果我们想要创建简单的 2D 图表，那么除了 MetricGraphics 之外，几乎所有人都可以创建它们——metric graphics 主要限于创建折线图。条形图等图表无法正常工作或不受支持。

另一方面，Plotly 支持大多数类型的图表。它还有一个 Python 库，所以我们可以在 Python 程序和网页中使用它。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.