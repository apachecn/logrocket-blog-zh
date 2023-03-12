# Moment.js 的更多选择

> 原文：<https://blog.logrocket.com/more-alternatives-to-moment-js/>

毫无疑问，Moment.js 是 JavaScript 生态系统中最受欢迎的库之一，但现在[被认为是维护模式](https://momentjs.com/docs/#/-project-status/)中的遗留项目，并且不鼓励使用它，您可能正在寻找一些替代品。

起初，寻找一个替代库似乎不是一件容易的事情，因为使用 Moment.js 可以做很多事情。例如:

然而，大多数项目并不需要所有这些功能。虽然有些项目可能使用 Moment.js 以特定的方式格式化日期和时间(相对日期和时间更受欢迎)，但对于其他项目来说，更重要的是检查日期是在其他日期之前、之后还是之间，或者根据用户的区域设置显示日期。

所以很可能，许多项目可以通过使用本地 JavaScript 对象(比如 [Date](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Date) 和 [Intl](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl) )的组合来满足他们的需求，也可以选择使用一个轻量级库来满足特定的目的。

去年，我写了一篇文章，回顾了国际化背景下 Moment.js 的替代方案。那篇文章中评论的库( [luxon](https://moment.github.io/luxon/) 、 [date-fns](https://date-fns.org/) 、 [day.js](https://github.com/iamkun/dayjs) )仍然是 Moment.js 的很好的替代，但是在本文中，我将评论另外三个库的功能:

此外，我将再次访问 [Intl。RelativeTimeFormat](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl/RelativeTimeFormat) 对象，[在 2020 年初](https://github.com/tc39/ecma402/pull/391)达到第 4 阶段，现在被更多的浏览器支持。

让我们开始吧。

## js-joda

如果您使用过 Java，那么学习如何使用这个库并不困难，因为它是 Java 8 中引入的日期/时间 API 的一个端口(它又基于库 [Joda-Time](https://www.joda.org/joda-time/) ，因此得名)。

js-joda 被组织成一组不可变的核心类。主要的有:

*   [LocalDate](https://js-joda.github.io/js-joda/class/packages/core/src/LocalDate.js~LocalDate.html) ，表示没有时间信息的日期。例如，`2020-12-01`
*   [LocalTime](https://js-joda.github.io/js-joda/class/packages/core/src/LocalTime.js~LocalTime.html) ，代表时间。例如，`10:00:01.999999999`
*   [LocalDateTime](https://js-joda.github.io/js-joda/class/packages/core/src/LocalDateTime.js~LocalDateTime.html) ，表示带有时间信息的日期。例如，`2020-12-01T10:00:01.999999999`

这些类不提供时区信息。为此，您必须使用 [ZonedDateTime](https://js-joda.github.io/js-joda/class/packages/core/src/ZonedDateTime.js~ZonedDateTime.html) ，它存储日期、时间和时区信息，以及 [ZoneId](https://js-joda.github.io/js-joda/class/packages/core/src/class/packages/core/src/ZoneId.js~ZoneId.html) 或 [ZoneOffset](https://js-joda.github.io/js-joda/class/packages/core/src/class/packages/core/src/ZoneOffset.js~ZoneOffset.html) 来处理特定的时区(您还需要导入包`@js-joda/timezone`)。

该库还提供了其他类(如 [ChronoField](https://js-joda.github.io/js-joda/class/packages/core/src/temporal/ChronoField.js~ChronoField.html) )和类型来表示日期或时间的一部分(如[月](https://js-joda.github.io/js-joda/class/packages/core/src/Month.js~Month.html))。特别是，有一组表示数量和时间点的类:

*   [持续时间](https://js-joda.github.io/js-joda/class/packages/core/src/Duration.js~Duration.html)，表示从纳秒到天的时间量。例如，2 小时
*   [Period](https://js-joda.github.io/js-joda/class/packages/core/src/Period.js~Period.html) ，表示基于日期的时间量，使用日、月和年。例如，2 年零 5 天
*   [瞬间](https://js-joda.github.io/js-joda/class/packages/core/src/Instant.js~Instant.html)，代表从`1970-01-01T00:00:00Z`开始测量的单个时间点(包括时区信息)。例如，`2020-12-01T10:00:47.202Z`

这些类中的大多数都有一个公共接口，所以它们可以用不同的方式实现相同的方法。例如， [Temporal](https://js-joda.github.io/js-joda/class/packages/core/src/temporal/Temporal.js~Temporal.html) 提供了一个带有操作日期时间字段(如天或秒)的方法的契约，这些方法由`Instant`、`LocalTime`和`ZonedDateTime`等类实现。

这样，为了解析或创建日期-时间对象，所有的类都有不同的方法`of`、`from`、`parse`和`now`。以下是一些例子:

```
// Creates a LocalDate object from a year, month, and dayOfMonth value
const ld1 = LocalDate.of(2020, Month.DECEMBER, 1);
// Creates a LocalTime object from an ISO 8601 string
const lt1 = LocalTime.parse("10:01:00.123456789");
// Creates a LocalDateTime object from the current datetime in UTC time
const ldt1 = LocalDateTime.now(ZoneOffset.UTC);
// Creates a ZonedDateTime ojbect from from a local date, time, and a time zone
const zdt1 = ZonedDateTime.of(ld1, lt1, ZoneId.of("Europe/Paris"));
// Creates an instant from the ZonedDateTime object
const i1 = Instant.from(zdt1);
// Creates a Period object from a text string such as PnYnMnD
const p1 = Period.parse("P1Y10M");
// Creates a Duration object from a number of standard hours (positive or negative)
const d1 = Duration.ofHours(-48);
```

`with*`方法可以用作 setters(创建一个新的实例，因为一切都是不可变的):

```
// Returns a copy of the LocalDate with the value 2020/12/3
const ld2 = ld1.withDayOfMonth(3);
// Returns a copy of the Instant object with the specified seconds but without changing the nanoseconds part
const i2 = i1.withFieldValue(ChronoField.INSTANT_SECONDS, 923232434);
```

或者您可以使用`at*`方法来组合不同类型的两个实例:

```
// Combines a LocalDate and a LocalTime object to create a new LocalDateTime
const ldt2 = ld1.atTime(lt1);
// Combines a LocalDateTime object and a time zone to create a ZonedDateTime object
const zdt2 = ldt1.atZone(ZoneId.of("+04:00"));
```

类似地，查询和检查条件的方法从`is`开始:

```
console.log(ld1.isLeapYear());
console.log(ldt1.isBefore(ldt2));
```

并且有`plus`和`minus`方法来操作对象的日期/时间信息:

```
// Add 10 minutes: 10:11:00.123456789
const lt2 = lt1.plusMinutes(10);
// Add a duration
const ldt3 = ldt1.plus(d1);
// Subtract one hour: 09:01:00.123456789
const lt3 = lt1.minus(1, ChronoUnit.HOURS);
// Substract a period
const zdt3 = zdt1.minusAmount(p1);
```

关于日期格式，js-joda 没有 moment.js 那么多选项，但是它有一组用于定制格式的[模式字符串](https://js-joda.github.io/js-joda/manual/formatting.html)(与 Java 中使用的[模式相同)。以下是一些例子:](https://docs.oracle.com/en/java/javase/15/docs/api/java.base/java/text/SimpleDateFormat.html)

```
// 10:00
console.log(lt2.format(DateTimeFormatter.ofPattern("h:m")));
// 2019, 1
console.log(zdt3.format(DateTimeFormatter.ofPattern("y, Q")));
```

然而，如果您的模式包含文本，您将不得不使用一个地区。

为此，将包`@js-joda/timezone`和`@js-joda/locale`一起导入，以导入所有的语言环境，或者一个单独的语言环境包(例如`@js-joda/locale_de`):

```
import "@js-joda/timezone";
import { Locale } from "@js-joda/locale_de";

// ...

// Formatting text with the DE locate: Okt. 1 2:52 PM
console.log(
  ldt3.format(
    DateTimeFormatter.ofPattern("MMM d h:m a").withLocale(Locale.GERMAN)
  )
);
```

你可以试试[这个沙盒](https://codesandbox.io/s/js-joda-example-88s9u)里的所有例子。

## 糖

Sugar 是一个库，它提供了许多实用函数来处理数组、数字、对象、日期以及其他类型。

该库包含许多模块和聚合填充，总大小约为 38k gzip。但是，如果您不打算使用完整的功能集，您有三种选择:

糖有三种使用方式:

*   Default，使用`Sugar`全局对象，组织成与扩展本地类的模块相对应的名称空间。直接在对象上调用这些方法。例如，`Sugar.Date.create("2020-01-01")`
*   Chainable，它使用 Sugar 名称空间作为构造函数来构建一个实例。例如，`new Sugar.Date("2020-01-01").isLeapYear().raw`
*   扩展，将方法直接映射到本机类型:

```
Sugar.Date.extend();
console.log(new Date().isLeapYear());
```

这里我将使用默认模式。

要创建一个实例，使用`create()`方法传递各种[格式](https://sugarjs.com/docs/#/DateParsing)，其中许多是非传统格式:

```
const d1 = Sugar.Date.create("last month");
const d2 = Sugar.Date.create("in 2 hours");
const d3 = Sugar.Date.create("20th of May");
const d4 = Sugar.Date.create("13 Jan 2014 11:00:00 CST");
const d5 = Sugar.Date.create("the 2nd Friday of October 2009");
const d6 = Sugar.Date.create("6-2017"); // months are zero-based, this is actually May, 2017
const d7 = Sugar.Date.create("5 minutes ago");
```

你可以在库的[单元测试中看到很多如何创建日期的例子。](https://github.com/andrewplummer/Sugar/blob/3ca57818332473b601434001ac1445552d7753ff/test/tests/date.js#L217)

一旦创建了 Sugar 实例，就可以使用`set`方法对日期进行[操作，该方法还接受一个布尔参数来重置比传递的单位更具体的单位(记住月份是从零开始的):](https://sugarjs.com/dates/#/Manipulating)

```
// Sets month to January
Sugar.Date.set(d1, { month: 0 });
// Sets day to 10 and reset hours to midnight
Sugar.Date.set(d2, { day: 10 }, true);
```

在 Sugar 中，并不是所有的方法都是不可变的。在这里你可以找到改变日期对象的方法列表。

还有向前和向后移动日期的方法(分别为`advance()`和`rewind()`)，添加单个时间单位的方法`add*()`以及将日期移动到时间单位的开始或结束位置的方法(分别为`beginningOf*()`和`endOf*()`):

```
// Shifts the date forward one month
Sugar.Date.advance(d3, { months: 1 }); // Keys can be singular too: month
// Shifts the date backwards 2 hours
Sugar.Date.rewind(d4, { hours: 2 });
// Adds 1 month
Sugar.Date.addMonths(d5, 1);
// Sets the date to Jan 1st at midgnight of the same year
Sugar.Date.beginningOfYear(d6);
// Sets the time to 11:59:59
Sugar.Date.endOfDay(d7);
```

以`is`开头的方法允许我们比较或测试日期:

```
// Is the year of d1 2020?
Sugar.Date.is(d1, '2020');
// Is d1 before January 2nd, 2020?
Sugar.Date.isBefore(d1, 'January 2nd, 2020');
// Is d1 after December 2018?
Sugar.Date.isAfter(d1, 'December 2018');
// Is d1 Friday?
Sugar.Date.isFriday(d1);
// Is d1 a date in the future?
Sugar.Date.isFuture(d1);
// Is d1 a weekday?
Sugar.Date.isWeekday(d1);
```

Sugar 还提供了一种方法，通过方法`*Since`、`*Ago`、`*Until`和`*FromNow`获得许多单位的时差:

```
// How many months have passed since d4?
Sugar.Date.monthsSince(d4);
// How many years have passed between d4 and d5?
Sugar.Date.yearsSince(d4, d5);
// How many days ago was d4)
Sugar.Date.daysAgo(d4);
// How many hours aga from d5 was d4?
Sugar.Date.hoursAgo(d5, d4);
// How many weeks from d4 until now?
Sugar.Date.weeksUntil(d4);
// How many seconds until d5 from d4?
Sugar.Date.secondsUntil(d5, d4);
// How many ms from now to d4)
Sugar.Date.millisecondsFromNow(d4);
```

但是我最喜欢 Sugar 的是它为格式化 T2 提供的所有选项。`format`方法支持两种类型的令牌， [LDML、](http://unicode.org/reports/tr35/tr35-6.html)和 [strftime](http://man7.org/linux/man-pages/man3/strftime.3.html) 。

LDML，这是一种既简短又容易记忆的格式(在此搜索标记列表[):](https://sugarjs.com/dates/#/Formatting)

```
Sugar.Date.format(d3, "{Weekday}, {hours}:{mm}:{ss}{TT}"); // e.g. Saturday, 12:00:00AM
```

以及 strftime，用于其他编程语言，如 [Python](https://www.programiz.com/python-programming/datetime/strftime) (此处搜索令牌列表):

```
Sugar.Date.format(d3, "{Weekday}, {hours}:{mm}:{ss}{TT}"); // e.g. Saturday, 12:00:00AM
```

此外，还有四种预定义的格式模式(`short`、`medium`、`long`和`full`):

```
Sugar.Date.short(d6); // 01/01/2017
Sugar.Date.medium(d6); // January 1, 2017
Sugar.Date.long(d6); // January 1, 2017 12:00 AM
Sugar.Date.full(d6); // Sunday, January 1, 2017 12:00 AM
```

以及自动选择最合适单位的两种相对时间方法(`relative`和`relativeTo`):

```
Sugar.Date.relative(d2); // 2 weeks ago
Sugar.Date.relativeTo(d2, d5); // 10 years
```

英语是自动包含的默认区域设置。在撰写本文时，Sugar 支持 [17 个地区](https://github.com/andrewplummer/Sugar#date-locales)，这些地区[包含在官方版本中，或者通过](https://sugarjs.com/docs/#/DateLocales)[下载](https://sugarjs.com/download/)页面单独添加。

区域设置可以用`setLocale`方法全局设置，或者作为参数传递给依赖于区域设置的方法，如`isLastWeek`(这样它就可以知道一周的开始)、`create()`或`relative()`:

```
import "sugar-date/locales";

// Sets the locale to italian globaly
// Sugar.Date.setLocale("it");

// Parse the string with the spanish locale
const d8 = Sugar.Date.create("hace 5 dias", "es");

// Uses the default locale, english (or the one set with setLocale)
// It doesn't use the locale used to create the instance
Sugar.Date.full(d8);

// Uses the french locale
Sugar.Date.full(d8, "fr");
```

你可以试试[这个沙盒](https://codesandbox.io/s/sugar-example-0btlf)里的所有例子。

## 时空

[Spacetime](http://spacetime.how/) 是一个解析、操作、比较和格式化日期的库，特别关注[时区和夏令时(DST)](https://github.com/spencermountain/spacetime/wiki/How-it-works) ，以避免在使用不同 DST 规则的时区中操作时间时出现错误。

你必须注意一些[考虑事项](https://github.com/spencermountain/spacetime#limitations--caveats)，然而，spacetime 有一个与 Moment.js 非常相似的 API(有一些很好的补充)，区别在于它的所有方法都是不可变的。

例如，可以使用多种输入格式和一些辅助方法创建时空实例:

```
/ ISO Format
const s1 = spacetime("2020-12-01");
// As long date
const s2 = spacetime("Dec 02 2020 17:50");
// As epoch in ms
const s3 = spacetime(1606975200000);
// As an array (months are zero-based)
const s4 = spacetime([2020, 0, 1, 20, 0]);
// As an object (months are zero-based)
const s5 = spacetime({year:2020, month:0, date:1});
// Current time
const s6 = spacetime.now();
// Today at midgnight
const s7 = spacetime.today();
// Tomorrow at midnight
const s8 = spacetime.tomorrow();
```

Getters 和 setters 的处理方式与 Moment.js 相同，只是增加了一些有用的功能，如`season()`、`hourFloat()`和`progress()`:

```
// Sets a new date based on s1 with 400 milliseconds
const s9 = s1.millisecond(400);
// Get milliseconds: 400
console.log("s9.milliseconds(): " + s9.millisecond());
// Get month (zero-based): 11
console.log("s9.month(): " + s9.month());
// Get day of year: 336
console.log("s9.dayOfYear(): " + s9.dayOfYear());
// Get day of year: winter
console.log("s9.season(): " + s9.season());
// Set the hour + minute in decimal form
const s10 = s2.hourFloat(16.5);
// Get the time: 4:30pm
console.log("s10.time(): " + s10.time());
// How far the moment lands between the start and end of the day/week/month/year (percentage-based)
console.log("s3.progress('year'): " + s3.progress('year'));
```

查询或比较方法也是如此:

```
// s3: 2020-12-03
// s4: 2020-01-01
console.log("s3.isAfter(s4): " + s3.isAfter(s4));
console.log("s3.isBefore(s4): " + s3.isBefore(s4));
console.log("s3.isEqual(s4): " + s3.isEqual(s4));
console.log("s3.leapYear(): " + s3.leapYear());
// Detect if two date/times are the same day, week, or year, etc
console.log("s3.isSame(s4, 'year'): " + s3.isSame(s4, "year"));
// Given a date amd a unit, count how many of them you'd need to make the dates equal
console.log("s3.diff(s4, 'day'): " + s3.diff(s4, "day"));
// Is daylight-savings-time activated right now, for this timezone?
console.log("s3.inDST(): " + s3.inDST());
// Does this timezone ever use daylight-savings?
console.log("s3.hasDST(): " + s3.hasDST());
// The current, DST-aware time-difference from UTC, in hours
console.log("s3.offset(): " + s3.offset());
// Checks if the current time is between 10pm and 8am
console.log("s3.isAsleep(): " + s3.isAsleep());
```

以及操作方法:

```
// s5: 2020-01-10 1:31pm
// Move to the first millisecond of the day, week, month, year, etc.
const s11 = s5.startOf('month'); // 2020-01-01 12:00am
// Move to the last millisecond of the day, week, month, year, etc.
const s12 = s5.endOf('week'); // 2020-01-12 11:59pm
// Increment the date/time by a number and unit
const s13 = s5.add(1, 'season') // 2020-05-10 1:31pm
// Decrease the date/time by a number and unit
const s14 = s5\. subtract(2, 'years') // 2018-01-10 1:31pm
// Move forward/backward to the closest unit
const s15 = s5.nearest('hour'); // 2020-01-10 2:00pm
// Go to the beginning of the next unit
const s16 = s5.next('quarter'); // 2020-01-04 12:00am
// Go to the beginning of the previous unit
const s17 = s5.last('month'); // 2019-12-01 12:00am
```

为了格式化日期和时间，Spacetime 有一些预定义的[格式](https://github.com/spencermountain/spacetime/wiki/Formatting):

```
s11.format('numeric-uk') // 01/01/2020
s12.format('iso-utc') // 2020-01-13T05:59:59.999Z
s13.format('mm/dd') // 05/10
s14.format('nice') // Jan 10th, 1:31pm  
s15.format('quarter') // Q1     
// They can be combined using this syntax
s16.format('{day} {date-ordinal}, {month-short} {year}')); // Sunday 1st, Dec 2019
```

但是您也可以使用更标准的[日期格式模式](http://unicode.org/reports/tr35/tr35-25.html#Date_Format_Patterns):

```
s17.unixFmt('yyyy/MM/dd h a')); // 2019/12/01 12 AM
```

或相对时间的`since()`功能。例如，当您执行:

```
spacetime('September 1 2020').since('September 30 2020')
```

它将返回以下对象:

```
{
   "diff": {
      "years": 0,
      "months": 0,
      "days": -29,
      "hours": 0,
      "minutes": 0,
      "seconds": 0
   },
   "rounded": "in 29 days",
   "qualified": "in 29 days",
   "precise": "in 29 days"
}
```

关于时区，当你创建一个实例时，你可以传递一个额外的参数来指定时区(建议使用 [IANA 名](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)):

```
const s18 = spacetime(1601521200000, "Europe/Paris");
const s19 = spacetime([2020, 0, 1, 20, 0], "Lima"); // America/Lima
const s20 = spacetime.now("-4h");
```

但是一旦你有了一个实例，你也可以很容易地用`goto()`把它改成另一个时区(再次建议使用 [IANA 名字](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)):

```
const s21 = s18.goto("Australia/Sydney"); // Oct 1 2020, 1:00pm
const s22 = s18.goto("GMT-5"); //-5 is actually +5
```

此外，您可以获得一个数组，其中包含以您的本地时间为参考的一个小时范围内的所有时区:

```
spacetime.whereIts('12:00pm', '2:00pm') // ["asia/seoul", "asia/tokyo", "pacific/palau", "australia/adelaide", ...]
spacetime.whereIts('10am') // Within an hour, from 10am to 11am
```

并获取实例时区的元数据:

```
s21.timezone();
/* Returns:
{
   "name": "Australia/Sydney",
   "hasDst": true,
   "default_offset": 10,
   "hemisphere": "South",
   "current": {
      "offset": 10,
      "isDST": false
   },
   "change": {
      "start": "04/05:03",
      "back": "10/04:02"
   }
}
*/
```

你可以尝试这个[沙盒](https://codesandbox.io/s/spacetime-example-xnqg3)中的所有例子。

## Intl.RelativeTimeFormat

Moment.js 最有用的特性之一是将日期显示为相对时间的能力:

```
const start = moment('2020-12-01');
const end   = moment('2020-12-04');
start.to(end); // "in 3 days"
start.to(end, true); // "3 days"
```

不是所有的库都提供这个功能，但是现在大多数浏览器都完全支持`Intl.RelativeTimeFormat`和[，这不再是个问题了。](https://caniuse.com/?search=Intl.RelativeTimeFormat)

`Intl.RelativeTimeFormat`是一个标准的内置对象，允许您以本地化的方式将数字格式化为相对时间。

构造函数可选地接受两个参数，一个是 [BCP 47 语言标签](https://www.techonthenet.com/js/language_tags.php)(或者一个这样的字符串数组)，另一个是带有属性的对象，用于配置区域匹配算法、格式和输出消息的长度:

```
const rtf = new Intl.RelativeTimeFormat("es", {
    localeMatcher: "best fit",
    numeric: "auto",
    style: "short"
});
```

一旦有了这个对象的实例，就可以使用`format()`方法传递要在消息中使用的数值作为第一个参数，单位(如天或小时，单数或复数形式)作为第二个参数:

```
rtf.format(-4, 'second'); // hace 4 s
rtf.format(-1, 'week'); // la semana pasada
rtf.format(3, 'quarter'); // dentro de 3 trim.
rtf.format(2, 'year'); // dentro de 2 a
```

或者`formatToParts()`获得一个数组，其中消息的各个部分被分开:

```
rtf.formatToParts(-4, 'second');
/* Returns:
[
   {
      "type": "literal",
      "value": "hace "
   },
   {
      "type": "integer",
      "value": "4",
      "unit": "second"
   },
   {
      "type": "literal",
      "value": " s"
   }
]
**/

```

如果你想知道如何得到数字部分，换句话说，就是两个日期之间经过的时间，看看这个 StackOverflow 答案,它共享下面的函数:

```
// in miliseconds
var units = {
  year  : 24 * 60 * 60 * 1000 * 365,
  month : 24 * 60 * 60 * 1000 * 365/12,
  day   : 24 * 60 * 60 * 1000,
  hour  : 60 * 60 * 1000,
  minute: 60 * 1000,
  second: 1000
};

var rtf = new Intl.RelativeTimeFormat('en', { numeric: 'auto' });

var getRelativeTime = (d1, d2 = new Date()) => {
  var elapsed = d1 - d2;
  // "Math.abs" accounts for both "past" & "future" scenarios
  for (var u in units)
    if (Math.abs(elapsed) > units[u] || u == 'second')
      return rtf.format(Math.round(elapsed/units[u]), u);
}
```

你可以试试[这个沙盒](https://codesandbox.io/s/intl-relativetimeformat-example-8ie8z)里的所有例子。

## 结论

毫无疑问，Moment.js 有很多替代品。在本文中，我们回顾了三个提供类似功能的库，并在某些情况下增加了一些有用的功能。

在我看来，这些库中的每一个都在不同的用例中表现得更好:

*   js-joda 作为一个通用库非常有用，特别是如果你有 Java 背景的话
*   Sugar 特别适合格式化日期，而且由于它为[提供了许多除日期](https://sugarjs.com/docs/)之外的其他类型的方法，它对应用程序的其他部分也很有用
*   由于 Spacetime 计算远程时间的方式和在时区之间转换的方法，Spacetime 特别适用于必须支持多个时区的情况。

此外，现在浏览器对 [JavaScript 国际化 API](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl) 的支持更加广泛，考虑一下轻量级库和本地特性的结合，或者甚至你需要一个外部库。

编码快乐！

## 您是否添加了新的 JS 库来提高性能或构建新特性？如果他们反其道而行之呢？

毫无疑问，前端变得越来越复杂。当您向应用程序添加新的 JavaScript 库和其他依赖项时，您将需要更多的可见性，以确保您的用户不会遇到未知的问题。

LogRocket 是一个前端应用程序监控解决方案，可以让您回放 JavaScript 错误，就像它们发生在您自己的浏览器中一样，这样您就可以更有效地对错误做出反应。

[![LogRocket Dashboard Free Trial Banner](img/e8a0ab42befa3b3b1ae08c1439527dc6.png)](https://lp.logrocket.com/blg/javascript-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/javascript-signup)

[LogRocket](https://lp.logrocket.com/blg/javascript-signup) 可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

自信地构建— [开始免费监控](https://lp.logrocket.com/blg/javascript-signup)。