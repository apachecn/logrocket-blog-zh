# 使用 Node.js 进行服务器端 A/B 测试

> 原文：<https://blog.logrocket.com/server-side-a-b-testing-node-js/>

A/B 测试是开发人员和产品团队了解用户如何使用他们的工具的常用方法。

例如，如果一个团队正在发布一个新的特性，它会想知道这个特性是否达到了它的预期目的——增加参与度、注册数、购买数等等。通过在实验中测试新功能，他们将能够确定新功能相对于对照组影响用户体验的确切方式。

在我的网站 Solitaired 上，我们一直在进行 A/B 测试。我们测试新功能(使用彩绘门)、新游戏和新布局。我们从 10%开始我们的 A/B 测试，然后当我们看到积极的参与时扩展我们的测试。

对我们来说，一个大问题是首先设置 A/B 测试。当然，有一些工具声称可以简化 A/B 测试——比如 Optimizely 和 Google Optimize。

然而，这些工具的主要焦点是客户端——这意味着 A/B 测试更改发生在页面加载之后。虽然易用性是客户端测试的好处之一，但是客户端测试也有一些主要的缺点:

*   当 A/B 测试开始时，页面闪烁
*   主要限于视觉变化，如文本、颜色等。
*   多页测试几乎是不可能的

这就是为什么大多数应用程序或严肃的 A/B 测试团队使用服务器端测试的原因。服务器端测试的设置有点棘手(但也没那么棘手)，但是有一些额外的好处:

*   速度提高，没有页面闪烁
*   多页/多步测试
*   能够与后端数据库同步

我们对我们的 A/B 测试软件非常满意，所以我们开源发布了它。在这里，我们将介绍如何使用我们的中间件对 Node.js 应用程序进行 A/B 测试。

## 安装 A/B 测试中间件

要求

您可以从安装 npm 库开始， [easy-abtest](https://www.npmjs.com/package/easy-abtest) :

```
npm install easy-abtest

```

然后将包添加到您的`app.js`文件中:

```
const abtest = require('easy-abtest');

```

在文件的下面，添加带有`options`参数的中间件(我们将在下面讨论):

```
let options = {
  enabled: true,
  name: 'experiment-ID-here',
  buckets: [
    {variant: 0, weight: 0.40},
    {variant: 1, weight: 0.60}
  ]
}
app.use(abtest(options));

```

注意:如果使用`express.static`，在其后添加中间件代码。否则，它将在每次静态资产调用时运行。

选项对象可以描述如下:

*   (Boolean):这样你就可以很容易地打开或关闭你的 A/B 测试代码
*   `name`:实验名称。这是一个你可以添加的 slug，或者如果你正在使用 Google Analytics 或 Mixpanel，你需要将他们的 slug 添加到这个字段中
*   这是好东西。这是一个描述变量的数组。每个变体都是一个具有以下键的对象:
    *   `variant` : `0`为对照，`1`为第一个单元格，`2`为第二个单元格，依此类推。只有`0`桶是真正需要的，但你也应该有一个实验单元
    *   `weight`:这是该信元应该占用的通信量的百分比。例如，`0.1`的值等于 10%。你所有的体重加起来应该是 100%

现在，当一个新用户来到您的应用程序时，中间件将运行并为用户的会话分配一个存储桶。它还将 bucket 分配给可以在视图模板中使用的局部变量。

*   在您的路由器中:`req.session.test`
*   在你看来:`abTest`

通过在您的路由器和视图中都可用，bucket 可用于以您喜欢的任何方式对您的用户进行细分，例如:

*   如果您想向您的控制用户发送一个视图模板，向您的实验单元发送一个不同的视图模板，您可以调用不同的`render()` 函数:

```
if (req.session.test.bucket == 0) {
  return res.render('index');
} else if (req.session.test.bucket == 1) {
  return res.render('index-new');
}

```

*   如果您想在视图中向用户显示不同的标题，您也可以这样做:

```
in homepage.pug
if abTest.bucket == 0
  h1 The best thing since sliced bread.
else if abTest.bucket == 1
  h1 The best thing since apple pie.

```

这就是设置方面的内容。有了对后端、视图模板和客户端的访问，您可以用任何您想要的方式来测试您的测试。(例如， [Mojomox](https://mojomox.com/) 的团队使用 easy-abtest 来计算出在多页面体验中给用户的步骤顺序)。

## 将测试系统连接到报告系统

虽然你现在可以在你的应用程序中运行 A/B 测试，但你仍然需要知道哪些测试获胜。这意味着您需要将您的实验连接到一些报告后端。我提出了以下几个建议:

## 连接到谷歌分析

假设您想要跟踪一个实验是否比另一个实验导致更多的按钮点击。

通过将 A/B 测试桶数据作为一个 JSON 对象添加到视图中，然后上推适当的事件，可以很容易地做到这一点:

```
script.
    let abTest = !{JSON.stringify(abTest)};

  if abTest.bucket == 0
    button#cta Click here now
  else if abTest.bucket == 1
    button#cta Start today!

  script.
    $('#cta').on('click', function() {
      gtag('event', abTest.bucket, {
        'event_category': abTest.name,
        'event_label': 'start today'
      });
    });

```

如果你想为你的 A/B 测试产品使用 Google Optimize，你只需要[遵循这里的教程](https://developers.google.com/optimize/devguides/experiments)并使用 Optimize Experiment ID 作为中间件选项中的实验 slug。

## 创建自己的数据库

同样，您可以在自己编写的数据库表中跟踪事件。请记住可能发生的大量交易。无论如何，您都应该在表中包含以下字段:

1.  实验名称
2.  桶变体
3.  值(例如，1 表示“一键点击”)
4.  日期/时间

## 结论

服务器端的 A/B 测试显然比[客户端](https://blog.logrocket.com/client-render-vs-server-render-vs-serverless/)更有优势，但是在过去，它需要考虑如何设置它。使用 easy-abtest 中间件，您可以轻松地将 A/B 测试集成到您的应用程序中。

接下来你要考什么？产品价格上涨，或者一个新的标志？通过 A/B 测试，你可以不断地试验，看看什么样的产品最适合你的用户和你的企业。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.