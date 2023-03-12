# 用 D3.js 和 Node.js 实现数据可视化

> 原文：<https://blog.logrocket.com/data-visualization-d3-js-node-js/>

理解大量数据既费时又困难。然而，使用图形、图表、地图等的数据可视化。是理解大型数据集的最有效的方法之一。

但是你问，什么是数据可视化？

数据可视化是将大型数据集和指标呈现为图表、图形和其他视觉效果的实践，以便于概览和分析。

许多工具允许您可视化不同级别的数据，但在本文中，我们将探索 [D3.js](https://d3js.org/) ，这是一个强大的 JavaScript 库，允许开发人员创建和呈现易于消化、有吸引力的交互式数据可视化体验。

## 你会学到什么

*   如何用 Express 设置 Node.js 应用程序并集成 D3.js
*   如何使用 D3 以条形图为例可视化数据

## 设置 Node.js 和 Express

您需要安装 Node.js 才能继续。可以从[官网](https://nodejs.org/en/)下载。

运行以下命令来安装依赖项，我们需要用 [Express](https://expressjs.com/) 和 [Handlebars](https://www.npmjs.com/package/express-handlebars) 来设置一个基本的节点应用程序:

```
npm install express express-handlebars nodemon  

```

或者，将以下对象添加到您的`package.json`文件中，并在您的终端上运行`npm install`。

```
{ 
 "express": "^4.17.1",
  "express-handlebars": "^6.0.2",
  "nodemon": "^2.0.15"
}

```

创建如下所示的开发目录:

```
.
├── package-lock.json
├── package.json
├── public
│   └── static
│       ├── css
│       │   └── index.css
│       └── js
│           ├── index.js
├── readme.md
├── server.js
└── views
    ├── home.handlebars
    └── layouts
        └── main.handlebars

```

将带有 Express 服务器代码的简单节点服务器添加到`server.js`文件中。

```
const express = require('express');
const { engine } = require('express-handlebars');
const app = express();
app.use(express.static('public'));
app.engine('handlebars', engine());
app.set('view engine', 'handlebars');
app.set('views', './views');

app.get('/', (req, res) => {
    res.render('home');
});
app.get('/api/data', (req, res) => {
    const data = [100, 50, 300, 40, 350, 250]; // assuming this is coming from the database
    res.json(data);
});

app.listen(3000);

```

在上面的代码中，我们有一个 API 路由(`/api/data`)和一个 home 路由(`/`)。API route 将与数据库对话，并返回我们需要在 home route 中可视化的数据。

在前端，我们将使用 D3 来可视化数据。我们将使用从 API 获得的数据创建一个条形图。假设来自 API 的数据是一些高中生的成绩，我们将创建一个可视化视图，以查看学生成绩模式的概况。

将以下 HTML 代码添加到您的车把模板中:

`/view/home.handlebars`

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>D3 Bar Chart</title>
</head>
<body>
    <h2> D3 Bar Chart</h2>
    <svg class="bar-chart"></svg>
</body>
</html>

<script type="module" src="./static/js/index.js"></script>

```

注意，我们添加了一个 SVG 元素和一个类`class="bar-chart"`,因为我们将把图表作为 SVG 加载。现在让我们使用 D3 来创建条形图。

注意:除了图表之外，还可以用 D3 创建许多可视化效果。例如，您可以用地图、图形、文字云和热图来可视化数据。

下面是前端的 JavaScript 代码。这里是我们使用 D3 创建条形图的地方:

`/public/static/index.js`

```
import * as d3 from "https://cdn.skypack.dev/[email protected]";
import axios from 'https://cdn.skypack.dev/axios';

const dataSet = async function getData() {
    return await axios.get('/api/data');
}
async function drawChart() {
    const data = await dataSet();
    const svgWidth = 500;
    const svgHeight = 500;
    const barPadding = 5;
    const barWidth = svgWidth / data.data.length;

    let svg = d3.select("svg");
    let width = svg
        .attr("width", svgWidth)
        .attr("height", svgHeight);

    svg
        .selectAll("rect")
        .data(data.data)
        .enter()
        .append("rect")
        .attr("y", (d) => svgHeight - d)
        .attr("height", (d) => d)
        .attr("width", () => barWidth - barPadding)
        .attr("transform", (d, i) => {
            let translate = [barWidth * i, 0];
            return `translate(${translate})`;
        })
        .style("fill", "steelblue");
}
drawChart();

```

我们将使用`getData`函数通过一个简单的 Axios get 请求从 API 获取数据，如下所示:

```
const dataSet = async function getData() {
    return await axios.get('/api/data');
}

```

在`drawChart`函数中，我们将首先设置我们需要的属性:获取要呈现的数据、SVG 高度和宽度以及图表的参数，比如填充和宽度。

```
    const data = await dataSet();
    const svgWidth = 500;
    const svgHeight = 500;
    const barPadding = 5;
    const barWidth = svgWidth / data.data.length;

```

接下来，我们将使用`d3.select`函数选择`<svg>`标签，如下所示:

```
let svg = d3.select("svg");

```

这将允许我们修改 SVG 并创建条形图。因此，让我们设置 SVG 元素的宽度。

```
    let width = svg
        .attr("width", svgWidth)
        .attr("height", svgHeight);

```

然后，选择 SVG 中的所有矩形，即使此时没有矩形。接下来，我们将把想要可视化的数据添加到`data`链接函数中:

```
.data(data.data)

```

然后我们将使用`enter`函数迭代数据以创建矩形并将它们附加到 SVG 中。

```
.enter()

```

对于每一次迭代，`enter()`函数都会生成一个矩形，最终形成图表。

```
.append("rect")

```

生成的 HTML 应该是这样的:

![HTML Example](img/a4b8658b9da8185a9777ea23a5f6ea85.png)

接下来，设置每个矩形的高度和宽度。矩形的高度将基于数据，特别是数组项，而宽度将是我们已经设置的条形的宽度，减去我们已经设置的条形图的填充。

此外，我们需要将 Y 轴设置为 SVG 的高度，减去数据项。否则，我们的棒线会看起来颠倒:

![Inverted Bar Graph](img/91bc0ac202a8196360fdb6f65336b278.png)

```
        .attr("y", (d) => svgHeight - d)
        .attr("height", (d) => d)
        .attr("width", () => barWidth - barPadding)

```

最后，我们将使用 SVG 的 transform 属性的 translate 形式来可视化地分离每个矩形。否则，所有的矩形将连接成一个矩形，如下所示:

![One Long Rectangle Bar Graph](img/faa3c5859129bd66a126226c20901d80.png)

当我们转换时，它看起来像这样:

![SVG With Transform Translate](img/d4e6bc88aabb19004d630403f066498a.png)

```
.attr("transform", (d, i) => {
      let translate = [barWidth * i, 0];
      return `translate(${translate})`;
})

```

在最后一部分，我们添加了风格。您可以使用`.style`属性向 D3 添加 CSS 样式。

```
.style("fill", "steelblue");

```

最后，图表看起来是这样的:

![Student Score Bar Chart](img/930ad16d9a10089f9d0ca4ca272aa5a4.png)

当然，还可以做更多的工作来改进这个图表。您可以使用 D3 创建更加复杂的数据可视化体验。

你应该看一下[文档](https://d3js.org/)，因为它提供了许多你可以直接编辑的例子，这样你就能感受到它是如何工作的。

## 结论

D3 是一个强大的工具，可以让你创建复杂的数据可视化，而不仅仅是条形图。你可以在 [D3 图库](https://observablehq.com/@d3/gallery)中查看可能的例子。

我们只是触及了表面。我期待你用 D3 和 Node 构建的东西。编码快乐！

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.