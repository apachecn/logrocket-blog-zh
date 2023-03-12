# 使用 node-cron - LogRocket Blog 在 Node.js 中调度任务

> 原文：<https://blog.logrocket.com/task-scheduling-or-cron-jobs-in-node-using-node-cron/>

没有一个开发者愿意把所有的时间都花在系统维护和管理、日常数据库备份、定期下载文件和邮件等繁琐的任务上。你更愿意专注于富有成效的任务，而不是跟踪这些烦人的杂务何时需要完成。当然，你更愿意在床上睡觉，而不是在某个不吉利的时间起床，睡眼惺忪地盯着显示器，运行那些在服务器资源消耗率较低时执行效果最好的任务。

这就是任务调度发挥作用的地方。

任务调度使您能够调度任意代码(方法/函数)和命令在固定的日期和时间、重复的时间间隔或指定的时间间隔后执行。在 Linux 操作系统中，任务调度通常由操作系统级的 cron 等实用服务来处理。对于 Node.js 应用程序，类似 cron 的功能是使用诸如 [node-cron](https://github.com/kelektiv/node-cron) 之类的包实现的，它自称是“NodeJs 纯 JavaScript 中的微型任务调度程序”

cron 的动作由一个 [crontab](https://www.gnu.org/software/mcron/manual/html_node/Crontab-file.html) (cron 表)文件驱动，这个文件是一个包含 cron 守护进程指令的配置文件。node-cron 模块允许您使用完整的 crontab 语法在 node 中调度任务。

crontab 语法如下所示:

```
 # ┌────────────── second (optional)
 # │ ┌──────────── minute
 # │ │ ┌────────── hour
 # │ │ │ ┌──────── day of month
 # │ │ │ │ ┌────── month
 # │ │ │ │ │ ┌──── day of week
 # │ │ │ │ │ │
 # │ │ │ │ │ │
 # * * * * * *

```

允许的 cron 值包括以下内容。

| **字段** | **值** |
| `second` | 0–59 |
| `minute` | 0–59 |
| `hour` | 0–23 |
| `day of the month` | 1–31 |
| `month` | 1–12(或姓名) |
| `day of the week` | 0–7(或名称，0 或 7 是星期日) |

```
使用节点克隆
使用 npm 安装 node-cron。

```
npm install --save node-cron

```

任务调度语法

```
cron.schedule(cronExpression: string, task: Function, options: Object)

```

选择

*   `scheduled`:一个`boolean`，用于设置创建的任务是否被调度(默认为`true`)
*   `timezone`:用于作业调度的时区

看看下面的例子。

```
import * as cron from 'node-cron'
cron.schedule('5 * * * * *', () => {
  console.log('running a task every minute at the 5th second');
});

```

时间规范的位置、二、三、四、五和六中的星号(`*`)类似于文件组或通配符，用于时间划分；它们分别指定“每分钟”、“每小时”、“每月的每一天”、“一周的每一天”。
下面的代码将在每天早上 5:03(35)运行。

```
import * as cron from 'node-cron'
cron.schedule('3 5 * * *', () => {
  console.log('running a task every day at 5:03 am');
});

```

任务计划提示和技巧
既然我们已经了解了基础知识，让我们做一些更有趣的事情。
假设您想在每周五下午 4 点运行一个特定的任务，代码如下所示:

```
import * as cron from 'node-cron'
cron.schedule('0 16 * * friday', () => {
  console.log('running a task every Friday at 4:00 pm');
});

```

或者您可能需要运行季度数据库备份。crontab 语法没有“一个月的最后一天”的选项，所以您可以使用下个月的第一天，如下所示。

```
import * as cron from 'node-cron'
cron.schedule('2 3 1 1,4,7,10 *', () => {
  console.log('running a task every quater on the first day of the month');
});

```

以下显示了在上午 10:05 到下午 6:05 之间每小时运行 5 分钟的任务

```
import * as cron from 'node-cron'
cron.schedule('5 10-18 * * *', () => {
  console.log('running a task five minutes past every hour between 10am and 6pm');
});

```

有些情况下，您可能需要每两小时、三小时或四小时运行一次任务。您可以通过按所需的时间间隔来划分时间，比如每四个小时运行一次`*/4`，或者在上午 12 点到下午 12 点之间每三个小时运行一次`0-12/3`。
分钟也可以类似划分。例如，`minutes`位置的表达式`*/10`表示“每 10 分钟运行一次作业”
以下任务在上午 8 点到下午 5 点 58 分之间每两小时运行五分钟

```
import * as cron from 'node-cron'
cron.schedule('*/5 8-18/2 * * *', () => {
  console.log('running a task every two hours between 8 a.m. and 5:58 p.m.');
});

```

**预定任务方法**
在我们结束之前，让我们回顾一些关键的预定任务方法。
开始一项任务
当您将`scheduled`选项值设置为`false`时，任务将被调度，但不能启动，即使 cron 表达式滴答。
要启动这样一个任务，需要调用调度任务`start`方法。

```
const cron = require('node-cron');

import * as cron from 'node-cron'
const task = cron.schedule('*/5 8-18/2 * * *', () => {
  console.log('running a task every two hours between 8 a.m. and 5:58 p.m.');
});
task.start();

```

停止任务
如果需要停止一个任务的运行，您可以使用调度任务`stop`方法将`scheduled`选项设置为`false`。除非重新启动，否则不会执行该任务。

```
const cron = require('node-cron');

import * as cron from 'node-cron'
const task = cron.schedule('*/5 8-18/2 * * *', () => {
  console.log('running a task every two hours between 8 a.m. and 5:58 p.m.');
});
task.stop();

```

破坏任务
`destroy`方法停止一个任务并完全销毁它。

```
const cron = require('node-cron');

import * as cron from 'node-cron'
const task = cron.schedule('*/5 8-18/2 * * *', () => {
  console.log('running a task every two hours between 8 a.m. and 5:58 p.m.');
});
task.destroy();

```

**结论**
在本教程中，我们介绍了大多数 node-cron 特性，您将需要这些特性来安排单调乏味的任务，并把自己从更重要——也更有成就感——的工作中解放出来。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png) 显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

. 

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

. 

```