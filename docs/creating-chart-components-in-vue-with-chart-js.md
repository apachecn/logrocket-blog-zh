# 使用 Chart.js 在 Vue 中创建图表组件

> 原文：<https://blog.logrocket.com/creating-chart-components-in-vue-with-chart-js/>

几乎不可避免的是，前端开发人员将不得不使用图表。从使用它在仪表板上显示数据分析，到使用它在网站上显示简单信息，您很可能会遇到需要以视觉愉悦的格式显示数据的情况。

[Chart.js](https://www.chartjs.org/) 是一个 JavaScript 库，帮助开发者创建简单灵活的图表。

在这个简短的教程中，我将向您展示如何使用 Chart.js 库为您的 Vue 应用程序创建图表组件。

### 入门指南

我们将创建一个可以呈现条形图和折线图的图表组件。

> 注意:在我们开始之前，请确保在您的计算机上安装了 Node.js、Vue 和 Vue CLI。你可以从[这里](https://nodejs.org/en/download/)安装 Node.js，按照[安装 Vue CLI](https://cli.vuejs.org/guide/installation.html) 的说明进行操作。

首先，让我们创建一个新的 Vue 项目:

```
vue create vue-chart
```

您可以选择预设或选择任何适合您的选项。
下一步是将 Charts.js 添加到项目中:

```
cd vue-chart
npm install chart.js --save
```

要运行项目，请使用以下命令:

```
npm run serve
```

完成之后，删除`src/components`中默认的`HelloWorld.vue`组件，以及`App.vue`组件中与之相关的所有内容。

## 创建我们的图表组件

要开始创建图表，让我们在 components 文件夹中创建一个组件。我们可以将组件命名为“Chart.vue”。在你的模板中使用 Chart.js 所需要的就是 canvas 元素。

```
<template>
   <div>
     <canvas></canvas>
   </div>
</template>
```

通过将 Chart.js 放入脚本标记中，将其导入到组件中:

```
import Chart from "chart.js";
```

接下来，我们将为图表创建`constructor`方法。这个函数的作用是从我们的模板中获取 canvas 元素，然后创建一个`chart`类的新实例。然后，它将`canvas`元素作为第一个参数传递，并将`object`作为第二个参数传递。我们一会儿将谈到那个物体。

```
chartConstructor(chartType, chartData, chartOptions) {
    const chartElement = document.querySelector("canvas");
    const chart = new Chart(chartElement, {
    type: chartType,
    data: chartData,
    options: chartOptions,
  });
},
```

您会注意到我们作为第二个参数传递的对象有 3 个属性:`type`、`data`和`options`，它们的值是函数的参数。

在 Chart.js 中，对象中需要的 3 个主要属性就是我上面列出的。

`Type`是一个简单的字符串，告诉你想要创建什么样的图表。例子包括“线”、“甜甜圈”、“条”等。

`Data`是一个具有两个属性或标签的对象，这两个属性或标签是图表值的标签数组。

另一个是`datasets`，这是一个为每个数据集接收对象的数组。你可以在[文档](https://www.chartjs.org/docs/latest/charts/line.html#dataset-properties)中查看它的一些属性。

下面是一个关于折线图中的`data`对象的示例:

```
data: {
    labels: ["Jan1", "Jan2", "Jan3", "Jan4", "Jan5", "Jan6", "Jan7"],
    datasets: [
      {
        label: "This week",
        data:  [12, 19, 10, 17, 6, 3, 7],
        backgroundColor: "rgba(224, 248, 255, 0.4)",
        borderColor: "#5cddff",
        lineTension: 0,
        pointBackgroundColor: "#5cddff",
      },
      {
        label: "Last week",
        data:  [10, 25, 3, 25, 17, 4, 9],
        backgroundColor: "rgba(241, 225, 197, 0.4)",
        borderColor: "#ffc764",
        lineTension: 0,
        pointBackgroundColor: "#ffc764",
      },
    ],
  },
```

`Options`用于设置图表的配置，如动画、布局、图例等。

在我们的组件中，我们将创建组件将接收数据并将其传递给图表构造器的道具。

```
props:{
  chartType:String,
  chartData:Object,
  chartOptions:Object
},
```

让我们在组件挂载时调用图表构造函数，并为其参数提供来自 props 的数据:

```
mounted(){
  let {chartType,chartData,chartOptions} = this;
  this.chartConstructor(chartType, chartData, chartOptions);
}
```

这样，我们的图表组件就完成了。我们可以在任何地方导入它，并将必要的数据传递给道具，我们的图表就会变得栩栩如生。

### 例子

这是一个完整的折线图示例。

我们导入我们的图表组件，并用 props 声明它，如下所示:

```
<template>
  <div class="chart">
    <Chart 
    :chartData="chartData" 
    :chartOptions="chartOptions" 
    :chartType="chartType" />
  </div>
</template>
```

在我们的数据中，我们给出了`chartData`的值:

```
chartType: "line",
```

我们的`chartData`看起来是这样的:

```
chartData: {
        labels: 
          ["Jan1", "Jan2", "Jan3", "Jan4", "Jan5", "Jan6",  "Jan7"],
        datasets: [
          {
            label: "This week",
            data: [12, 19, 10, 17, 6, 3, 7],
            backgroundColor: "rgba(224, 248, 255, 0.4)",
            borderColor: "#5cddff",
            lineTension: 0,
            pointBackgroundColor: "#5cddff",
          },
          {
            label: "Last week",
            data: [10, 25, 3, 25, 17, 4, 9],
            backgroundColor: "rgba(241, 225, 197, 0.4)",
            borderColor: "#ffc764",
            lineTension: 0,
            pointBackgroundColor: "#ffc764",
          },
        ],
      },
```

在一个真实的应用程序中，我们的`datasets`对象中的数据可能来自一个 API。

最后，我们的`chartOptions`:

```
chartOptions: {
        scales: {
          xAxes: [
            {
              stacked: true,
              gridLines: { display: false },
            },
          ],
          yAxes: [
            {
              ticks: {
                stepSize: 1,
                callback: function(value, index, values) {
                  if (value % Math.round(values[0] / 6) == 0) {
                    return value;
                  } else if (value === 0) {
                    return value;
                  }
                },
              },
              // stacked: true
            },
          ],
        },
        maintainAspectRatio: false,
        legend: {
          labels: {
            boxWidth: 10,
          },
          position: "top",
        },
        animation: {
          duration: 2000,
          easing: "easeInOutQuart",
        },
      },
```

> 注意:您在这里看到的值只是我的偏好，请随意试验和尝试其他东西。

## 结论

我们构建的是一个简单的图表组件。您可以添加更多的内容，使其更加灵活和复杂，这可能会让您有能力接受更多的值。

我希望这能帮助你在你的项目中添加惊人的图表。

要查看所有内容并详细查看其他示例，您可以浏览此[回购](https://github.com/Ajiva-D/vuecharts)。

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。