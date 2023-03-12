# 使用 MomentJS 在 JavaScript 中操作日期和时间

> 原文：<https://blog.logrocket.com/manipulate-date-and-time-in-javascript-using-momentjs-439dc8725c5b/>

都快 2018 年了！我们生活中的每一天都在使用日期和时间，我就是这样做的，如果你是一个设计产品、工具或服务的开发人员，你很可能需要在你构建的任何东西中操纵日期。

你如何在剧本中处理日期？`new Date()`？这样的操作充其量只能算是麻烦。 [Moment](https://momentjs.com) 是一个 JavaScript 库，通过其可接近的 API 方法，使得日期处理变得更加容易。

在本教程中，我将讨论如何使用`moment`在 JavaScript 中有效地操作日期和时间。

### 装置

可以在浏览器和服务器端运行。在服务器端使用 Node.js，通过在命令行运行以下命令，通过 npm 安装 moment:

```
npm install -g moment
```

`moment`然后可以简单地要求:

```
var moment = require('moment')
var today = moment().format()
```

我们刚刚创建了`moment`对象，可以操作它来返回任何期望的日期值。一旦用`moment()`调用矩函数，它默认为当前日期，就像`new Date()`一样。

在浏览器中，`moment`被下载到[这里的](https://momentjs.com/)，并包含在一个脚本标签中，如下所示:

```
<script src="moment.js"></script>
```

或者，它可以从一个 [CDN](https://cdnjs.com/libraries/moment.js) 导入，并在脚本标签中用作源。其他安装方法可以在[这里](https://momentjs.com/docs/)找到。

### 操纵日期和时间

`moment`对象让我们能够以我们选择的方式操作 date 对象，对它应用几个(链接)`moment`方法返回期望的格式化日期值。

例如，我们用`new Date()`得到以下格式:

```
new Date();

// Wed Oct 18 2017 19:36:39 GMT+0100 (WAT)
```

如果在我们的用例中，向用户显示相对日期会更直观呢？使用 moment，您可以简单地这样做:

```
var then = moment('2010-09-17').fromNow() 
// "7 years Ago"
```

不带任何参数调用`moment()`将返回您所在时区的当前日期。

```
var today = moment() // returns today's date in current timezone
```

让我们深入了解 Moment 提供的更多格式化方法。

### 日期格式

以前，用`Date()`字符串格式化日期需要对返回的日期字符串执行几个字符串操作。使用`moment`,我们只需要添加`format()`方法来格式化日期。该方法还接收定义日期显示方式的参数。

```
var today = moment().format() //returns 2017-10-04T21:07:52+01:00
var today = moment().format('YYYY-MM-DD') //returns 2017-10-04
var today = moment().format('YY/MMM/DD') // returns 17/Oct/04
var today = moment().format('D/MMMM/Y') // returns 4/October/17
var time = moment().format('ddd DD/MM/YYYY') //returns Wed 04/10/2017
```

要将某个特定的`moment`作为字符串，在调用`moment()`时将字符串值作为参数传递。

```
var time = moment('2017/10/5') // returns Thu Oct 05 2017 00:00:00 GMT+0100
```

如果知道的话，指定的日期格式也可以作为参数传入。如果日期的值无法解析，则该日期被视为`invalid`。

```
var time = moment('2017/10/5', 'YYYY/DD/MM')// returns Wed May 10 2017 00:00:00 GMT+0100
```

其他日期格式可以在[这里](https://momentjs.com/docs/#/parsing/now/)找到。

当调用`moment`时，`moment`使用方法链接来完成令人惊叹的操作，例如:

```
var newMoment = moment().add().subtract().fromDate()
```

这只是`moment`中链接方法的一个例子。还要注意，矩是可变的，因此会改变最初获取的`moment`。为了创建一个新的被操纵的`moment`，原来的`moment`可以被克隆成一个新的变量。

### 减去

这种方法从原始时刻减去时间。这里，调用了`subtract()`方法，并向其传递了一个表示要删除的时间量和时间值的参数。这个时间值也可以用速记法书写，它们包括:

*   d:天
*   y:年
*   问:25 美分
*   几个月
*   w:几周
*   小时
*   男:分钟
*   斯:秒
*   毫秒:毫秒

```
moment().subtract(val, string)
```

```
var lastWeek = moment().subtract(7, 'days') //subtracts 7 days from the current date
```

多个组合也可以用减去时间的方法通过链接来完成。

```
var someWeek = moment().subtract(1, 'week').subtract(2, 'days').subtract(5, 'weeks')
```

### 增加

这类似于`subtract()`方法，只是给`moment`增加了所需的时间。

```
moment().add(val, string)
```

```
var nextHour = moment().add(1, 'hour')
```

### 开始于

该方法将`moment`突变为指定时间的开始。这会将时间设置为当前时间的开始，可以是日、月、年或分钟的开始。

```
var time = moment().startOf('day') // returns time at beginning of the day - 12:00am
var time = moment().startOf('week')// Uses the locale aware start of week day
var time = moment().startOf('year')
var time = moment().startOf('minute')
```

### 结束

这与`startOf()`相反，将`moment`突变到一个单位时间的末尾。这包括周、月、日或年。

```
var endOfMonth = moment().endOf('Month') // Returns the last time at the end of the month.
```

### 从现在开始的时间

此显示相对时间用于给出两个时间范围之间差异的整体情况。

```
var then = moment('2010-09-17').fromNow() //returns "7 years Ago"
var justNow = moment().fromNow() //returns "A few seconds ago"
```

`fromNow()`方法也接受一个布尔值，传入`true`会删除‘ago’后缀。

```
var since = moment("27/08/2017", "DD/MM/YYYY").fromNow(true) // a month
```

要查看特定日期的准确时间，可以使用`from()`方法。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
var time = moment("27/08/2017", "DD/MM/YYYY").from(moment('27/09/2017', "DD/MM/YYYY"))
//a month ago
```

### 时间到了

与`fromNow()`类似，`toNow()`方法返回与`fromNow()`方法相反的结果。`to()`方法也用于显示两个时间值之间的相对时间。

```
var time = moment("27/08/2016", "DD/MM/YYYY")
var time2 = moment("27/09/2017", "DD/MM/YYYY")
console.log(time.to(time2)) // in a year

var time2 = moment("27/09/2017", "DD/MM/YYYY")
console.log(time2.toNow())// in 8 days
```

### 差异

这是操作时间和日期时遇到的常见问题之一。对于`moment`，`diff()`方法用于计算两个时刻之间的差值，并返回以毫秒为单位的差值。但是，可以返回其他时间单位，如年、月、周、日、小时、分钟和秒。`diff()`方法将时间单位作为第一个参数，并将所需的时间单位作为第二个参数。

```
momentA.diff(momentB, 'unit')

```

```
var time = moment("27/08/2018", "DD/MM/YYYY")
var time2 = moment("27/09/2017", "DD/MM/YYYY")
console.log(time.diff(time2,"days") + ' days') // 334days
```

### 询问

这些是返回布尔值的`moment`方法，用于比较两个日期值。

### isBefore()

顾名思义，`isBefore()`方法验证一个日期值是否在另一个日期值之前。

```
var then = moment("27/08/2018", "DD/MM/YYYY")
var now = moment("27/09/2017", "DD/MM/YYYY")
console.log(then.isBefore(now)) // false
console.log(now.isBefore(then)) // true
```

### isAfter()

类似于`isBefore()`方法，该方法验证 after 日期是否在另一个日期值之后，并返回`true`或`false`。

```
var time = moment("27/08/2019", "DD/MM/YYYY")
var time2 = moment("27/12/2018", "DD/MM/YYYY")
document.write(time.isAfter(time2)) // true
```

用于比较两个日期值的其他查询方法包括:

*   伊萨梅()
*   isBetween()
*   伊萨米奥拉夫特()
*   isSameOrBefore()

这些方法都遵循相同的语法并返回`true`或`false`。还有一些方法可以验证某个日期的有效性。这些是:

### isDaylightSavingTime

`isDST()`验证`moment`是否处于夏令时。

```
var time2 = moment("27/09/2017", "DD/MM/YYYY").isDST()
console.log(time2) // false
```

### isLeapYear

方法验证一年是否是闰年，并返回一个布尔值。

```
var time = moment(2012, "YYYY").isLeapYear(); 
console.log(time) //returns true
```

### isValid

使用`isValid()`方法，可以确定`moment`的有效性。

```
var time = moment('2012/34/28', "YYYY/MM/DD").isValid(); //returns false
console.log(time) //returns false
var time = moment('2012/02/18', "YYYY/MM/DD").isValid();
console.log(time) //returns true
```

### 结论

在本文中，我们已经看到了`moment`如何无缝地处理和管理日期和时间。通过简单地在`moment`对象上应用方法，无论是单独地还是以链的形式，我们都可以有效地操纵我们的日期和时间，以获得想要的结果。

在官方 moment.js [文档](http://momentjs.com/docs/)上找到更多资源。