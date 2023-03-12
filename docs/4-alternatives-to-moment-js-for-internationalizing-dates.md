# 约会国际化的 4 种 Moment.js 替代方案

> 原文：<https://blog.logrocket.com/4-alternatives-to-moment-js-for-internationalizing-dates/>

当准备在不同语言中使用的应用程序时，日期格式是最重要的方面之一。

Moment.js 是最常用的 JavaScript 库之一，用于格式化和操作我们可以用于此目的的日期。然而，在某些情况下，关于这个库的一些事情[(比如它的大小或者它的构造方式)可能会让你想知道是否有其他的选择。](https://inventi.studio/en/blog/why-you-shouldnt-use-moment-js)

在本文中，我将回顾关于日期国际化的 Moment.js 的四种替代方案:

我将着重于将日期转换为不同地区的不同格式的字符串，包括相对时间。

让我们从 JavaScript 国际化 API 开始。

## JavaScript 国际化 API

`Intl`是一个全局对象，充当 ECMAScript 国际化 API 的名称空间。关于日期，该对象提供了以下构造函数:

*   `Intl.DateTimeFormat`，提供日期和时间格式
*   `Intl.RelativeTimeFormat`，它为日期和时间戳提供了语言敏感的易读短语

这些构造函数有两个可选参数，一个是 locale，另一个是带有自定义输出选项的对象。例如:

```
let rtf = new Intl.RelativeTimeFormat('en-GB', { style: 'long' });
let dtf = new Intl.DateTimeFormat('de');
```

区域设置参数是一个表示 [BCP 47 语言标签](https://github.com/libyal/libfwnt/wiki/Language-Code-identifiers)的字符串，由以下部分组成:

*   语言代码([T2/639-2【ISO 639-1】)。例如，`el`(现代希腊语)](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes)
*   脚本代码([T2【ISO 15924】)。例如，`Grek`(希腊语)](https://unicode.org/iso15924/iso15924-codes.html)
*   国家代码([T2【ISO 3166】)。例如，`GR`(希腊)](https://en.wikipedia.org/wiki/List_of_ISO_3166_country_codes)
*   变体(来自[iana.org](https://www.iana.org/assignments/language-subtag-registry/language-subtag-registry)，搜索“类型:变体”)。例如，`polyton`(多音希腊文)
*   扩展(来自 Unicode，更多信息[此处](https://www.w3.org/International/multilingualweb/dublin/slides/23b-davis.pdf))。例如，`u-nu-native`(原生数字)

以下是一个包含所有部件的示例:

```
let rtf = new Intl.RelativeTimeFormat('el-Grek-GR-polyton-u-nu-native');
```

只有第一部分(语言代码)是必需的，您可以传递一个字符串数组来定义后备语言:

```
// Requests Dutch as the primary language and if it is not available, it requests french
let dtf = new Intl.DateTimeFormat(['nl', 'fr'])
```

如果没有提供区域设置，则使用运行时环境的区域设置。

关于第二个参数，options 对象，它因构造函数而异。

`Intl.DateTimeFormat`接受诸如日期样式(`full`、`long`、`medium`和`short`)的选项，是使用 12 小时制还是 24 小时制，或者格式化一天中各部分的表示，如年、月、工作日等。

在 Intl 的[文档页面中。DateTimeFormat](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/DateTimeFormat#Syntax) ，您可以了解更多关于您可以用来定制该对象的所有选项。

关于`Intl.RelativeTimeFormat`，选项对象只有以下属性:

*   `localeMatcher`，要使用的区域设置匹配算法。可能的值是`lookup`(从更具体到不太具体，如果`en-us`不可用，则选择`en`)和`best fit`(默认值，如果`en-us`不可用，则可以选择类似于`en-uk`的值)
*   `numeric`，对输出信息进行格式化。可能的值是`always`(例如，`2 hours ago`)或`auto`，这并不总是允许输出中有数值(例如，`yesterday`)
*   `style`，对输出报文的长度进行格式化。可能的值有`long`、`short`和`narrow`

一旦有了类型为`Intl.DateTimeFormat`或`Intl.RelativeTimeFormat`的对象，就可以使用方法`format()`或`formatToParts()`(返回包含输出部分的数组)来格式化日期。

在`Intl.DateTimeFormat`的情况下，这些方法接受`Date`对象进行格式化:

```
const date = new Date(Date.UTC(2014, 8, 19, 14, 5, 0));
const options = {
   dateStyle: 'short',
   timeStyle: 'full',
   hour12: true,
   day: 'numeric',
   month: 'long',
   year: '2-digit',
   minute: '2-digit',
   second: '2-digit',
};
// Sample output: 19 septembre 14 à 05:00
console.log(new Intl.DateTimeFormat("fr", options).format(date));
// Sample output: 19\. September 14, 05:00
console.log(new Intl.DateTimeFormat("de-AT", options).format(date));
/* Sample output: [{"type":"day","value":"19"},{"type":"literal","value":" "},{"type":"month","value":"settembre"},{"type":"literal","value":" "},{"type":"year","value":"14"},{"type":"literal","value":", "},{"type":"minute","value":"05"},{"type":"literal","value":":"},{"type":"second","value":"00"}] */
console.log(new Intl.DateTimeFormat("it", options).formatToParts(date));
```

请注意，如果您仅在 options 对象中指定了几个日期时间组件，这些组件将出现在输出中:

```
const date = new Date(Date.UTC(2014, 08, 19, 14, 5, 0));
const options = {
   year: '2-digit',
};
// Output: 14
console.log(new Intl.DateTimeFormat("en", options).format(date));
```

在使用`Intl.RelativeTimeFormat`的情况下，`format()`使用消息中使用的数值和第二个参数来表示该值的单位(如`year`或`second`，可以是单数或复数形式):

```
const options = {
   localeMatcher: 'best fit',
   numeric: 'auto',
   style: 'short',
};
// Output: last mo.
console.log(new Intl.RelativeTimeFormat("en-CA", options).format(-1, 'month'));
// Output: la semana pasada
console.log(new Intl.RelativeTimeFormat("es-ES", options).format(-1, 'week'));
/* Output: [{"type":"integer","value":"60","unit":"minute"},{"type":"literal","value":" 分鐘前"}] */
console.log(new Intl.RelativeTimeFormat("zh-TW", options).formatToParts(-60, 'minutes'));
```

另外，请注意对`numeric`属性使用`always`和`auto`值的区别:

```
// Output: in 0 days
console.log(new Intl.RelativeTimeFormat("en", {numeric: 'always'}).format(0, 'day'));
// Output: today
console.log(new Intl.RelativeTimeFormat("en", {numeric: 'auto'}).format(0, 'day'));
```

你可以尝试修改上面所有的例子[这里](https://codesandbox.io/s/intl-datetimeformat-example-e32u4)和[这里](https://codesandbox.io/s/intl-relativetimeformat-example-5xy7l)，但是根据你使用的浏览器，你可能会得到一些错误。

`Intl.DateTimeFormat`的大部分功能在现代浏览器中都得到了很好的支持(更多信息[在这里](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/DateTimeFormat#Browser_compatibility))，然而，`Intl.RelativeTimeFormat`只有 Chrome 71 和 Firefox 70 才完全支持(在撰写本文时 Safari 或 Edge 还不支持)。

你可以使用[多边形填充](https://github.com/catamphetamine/relative-time-format)，但是你必须以不同的方式创建对象:

```
const myLocale = /* Import JSON file for the choosen locale */;
const localeTag = /* Tag for the above locale */;
const options = { /* Options object */ };
RelativeTimeFormat.addLocale(myLocale);
new RelativeTimeFormat(localeTag, options).format(3, 'day');
```

你可以试试这个例子[这里](https://codesandbox.io/s/intl-relativetimeformat-polyfill-example-eq2y9)。

所以你可以看到，`Intl.RelativeTimeFormat`类似于`moment.duration().humanize()`:

```
moment.duration(-1, 'weeks').humanize(true); // a week ago
```

如果您习惯于像 Moment.js 那样计算从现在开始的相对时间或相对于给定参考时间的日历时间:

```
moment('20140919', 'YYYYMMDD').fromNow(); // 5 years ago
moment().add(5, 'days').calendar(); // Tuesday at 1:15 PM
```

您需要手动计算两个日期之间的[差。](https://stackoverflow.com/questions/3224834/get-difference-between-2-dates-in-javascript)

没有什么比使用原生特性更好的了，但是如果这成为一个问题，还有其他的选择。

## 勒克斯

Luxon 是由 Moment 的一个维护者创建的一个库[，所以它借鉴了它的许多想法，同时在某些方面提供了改进。](https://github.com/moment/luxon/blob/master/docs/why.md)

出于国际化的目的，您可以将 Luxon 视为`Intl.DateTimeFormat`和`Intl.RelativeTimeFormat`的包装器。

例如，根据地区设置日期格式的一种方法是首先设置地区，然后使用方法`toFormat(fmt:string, opts: Object)`和来自[这个表](https://moment.github.io/luxon/docs/manual/formatting.html#table-of-tokens)的日期时间标记:

```
// Sample output: 2019 сентябрь
console.log(DateTime.local().setLocale('ru').toFormat('yyyy MMMM'));
```

您还可以在 options 对象中传递该方法可以作为参数的区域设置:

```
// Output: 2019 сентябрь
console.log(DateTime.local(2018, 9, 1).toFormat('yyyy MMMM', { locale: "ru" }));
```

或者，如果您使用的方法是来自 Object 的[、来自 ISO](https://moment.github.io/luxon/docs/class/src/datetime.js~DateTime.html#static-method-fromObject) 的[、来自 HTTP](https://moment.github.io/luxon/docs/class/src/datetime.js~DateTime.html#static-method-fromISO) 的[、来自 Format](https://moment.github.io/luxon/docs/class/src/datetime.js~DateTime.html#static-method-fromHTTP) 的[或来自 RFC2822](https://moment.github.io/luxon/docs/class/src/datetime.js~DateTime.html#static-method-fromFormat) 的[，您可以在创建时设置区域设置:](https://moment.github.io/luxon/docs/class/src/datetime.js~DateTime.html#static-method-fromRFC2822)

```
const italianDate = DateTime.fromISO("2014-09-19", { locale: "it" });
// Output: 2014 settembre 19
console.log(italianDate.toFormat("yyyy MMMM dd"));
```

然而，推荐的方法是使用方法`toLocaleString()`和`toLocaleParts()`，它们分别返回表示日期的本地化字符串和包含字符串各个部分的数组。

这些方法相当于`Intl.DateTimeFormat`的`format()`和`formatToParts()`方法，实际上它们采用相同的 options 对象(以及一些预置，如`DateTime.DATE_SHORT`、[等等](https://moment.github.io/luxon/docs/manual/formatting.html#presets)):

```
const date = DateTime.utc(2014, 9, 1, 14, 5, 0);
const options = {
  dateStyle: "short",
  timeStyle: "full",
  hour12: true,
  day: "numeric",
  month: "long",
  year: "2-digit",
  minute: "2-digit",
  second: "2-digit"
};
// Output: 1 septembre 14 à 05:00 
console.log(date.setLocale("fr").toLocaleString(options));
// Output: 1\. September 14, 05:00 
console.log(date.setLocale("de-AT").toLocaleString(options));
/* Output: [{"type":"day","value":"1"},{"type":"literal","value":" "},{"type":"month","value":"settembre"},{"type":"literal","value":" "},{"type":"year","value":"14"},{"type":"literal","value":", "},{"type":"minute","value":"05"},{"type":"literal","value":":"},{"type":"second","value":"00"}] */
console.log(
  JSON.stringify(date.setLocale("it").toLocaleParts(options), null, 3)
);
// Output: 2:05 PM
console.log(date.toLocaleString(DateTime.TIME_SIMPLE));
// Output: 01/09/2014 
console.log(date.toLocaleString({ locale: 'pt' }));
```

这意味着:

*   Luxon 可以使用与`Intl`对象相同的 [BCP 47](https://tools.ietf.org/html/rfc5646) 区域设置字符串进行配置
*   如果`Intl`对象在目标浏览器中不可用，这部分库将无法正常工作(对于 Node.js 应用程序，您可能需要采取[一些额外的步骤来设置库](https://moment.github.io/luxon/docs/manual/install.html#node)
*   关于国际化，Luxon 充当 JavaScript 国际化 API 的包装器，但是它在`DateTime` Luxon 对象的级别上设置语言环境(更多信息[在这里](https://moment.github.io/luxon/docs/manual/intl.html)

另一方面，方法 [toRelative](https://moment.github.io/luxon/docs/class/src/datetime.js~DateTime.html#instance-method-toRelative) (默认情况下，返回相对于现在的给定时间的字符串表示)和 [toRelativeCalendar](https://moment.github.io/luxon/docs/class/src/datetime.js~DateTime.html#instance-method-toRelativeCalendar) (默认情况下，返回相对于今天的给定日期的字符串表示)提供了类似于`Intl.RelativeTimeFormat`的功能:

```
// Sample output: in 23 hours 
console.log(DateTime.local().plus({ days: 1 }).toRelative());
// Sample output: tomorrow
console.log(DateTime.local().plus({ days: 1 }).toRelativeCalendar());
// Sample output: in 1 Tag 
console.log(DateTime.local().plus({ days: 1 }).toRelative({ locale: "de" }));
// Sample output: morgen 
console.log(DateTime.local().plus({ days: 1 }).toRelativeCalendar({ locale: "de" }));
// Sample output: il y a 1 semaine 
console.log(DateTime.local().setLocale("fr").minus({ days: 9 }).toRelative({ unit: "weeks" }));
// Sample output: la semaine dernière 
console.log(DateTime.local().setLocale("fr").minus({ days: 9 }).toRelativeCalendar({ unit: "weeks" }));
```

与`Intl.RelativeTimeFormat`不同，如果你的浏览器不支持这个 API，上述方法不会抛出错误，唯一的问题是它们不会被翻译成合适的语言。

你可以试试上面所有的例子[这里](https://codesandbox.io/s/luxon-example-gmi98)。

## Date-fns

[Date-fns](https://date-fns.org) 是另一个流行的用于日期处理和格式化的 JavaScript 库。在撰写本文时最新的版本 2 仅以 [NPM 包](https://date-fns.org/v2.2.1/docs/Getting-Started#installation)的形式出现，所以如果你想在浏览器中直接使用它，你必须使用像 [Browserify](http://browserify.org) 这样的捆绑器。

这个库包含大约 60 个不同的区域(这里[你可以看到所有的区域)。要使用一个或多个语言环境，您需要像这样导入它们:](https://github.com/date-fns/date-fns/tree/master/src/locale)

```
import { es, enCA, it, ptBR } from 'date-fns/locale'
```

接受区域设置作为参数的函数如下:

*   [format](https://date-fns.org/v2.2.1/docs/format) ，它返回格式化的日期，将日期、表示格式化日期的模式的字符串(基于 [Unicode 技术标准#35](https://www.unicode.org/reports/tr35/tr35-dates.html#Date_Field_Symbol_Table) 的日期字段符号)以及带有区域设置和一周第一天的索引等选项的对象作为参数
*   [formatDistance](https://date-fns.org/v2.2.1/docs/formatDistance) ，它返回给定日期之间的距离(以文字表示)，将要比较的日期和一个带有区域设置或是否包含秒等选项的对象作为参数
*   [formatDistanceToNow](https://date-fns.org/v2.2.1/docs/formatDistanceToNow) 与`formatDistance`相同，但只取一个日期(将与现在进行比较)
*   [格式距离限制](https://date-fns.org/v2.2.1/docs/formatDistanceStrict)与`formatDistance`相同，但是没有使用`almost`、`over`或`less than`这样的助手。options 对象具有强制时间单位和指定部分单位舍入方式的属性
*   [formatRelative](https://date-fns.org/v2.2.1/docs/formatRelative) ，用文字表示相对于给定基准日期的日期。它还可以接受一个 options 对象作为参数，来设置一周第一天的区域设置和索引

以下是一些例子:

```
import {
   format,
   formatDistance,
   formatDistanceToNow,
   formatDistanceStrict,
   formatRelative,
   addDays
 } from "date-fns";
 import { es, enCA, ro, it, ptBR } from "date-fns/locale";

 // Output: septiembre / 19
 console.log(format(new Date(), "MMMM '/' yy", { locale: es }));
 // Output: in less than 10 seconds
 console.log(
   formatDistance(
     new Date(2019, 8, 1, 0, 0, 15),
     new Date(2019, 8, 1, 0, 0, 10),
     { locale: enCA, includeSeconds: true, addSuffix: true }
   )
 );
 // Output: less than 10 seconds ago
 console.log(
   formatDistance(
     new Date(2019, 8, 1, 0, 0, 10),
     new Date(2019, 8, 1, 0, 0, 15),
     { locale: enCA, includeSeconds: true, addSuffix: true }
   )
 );
 // Output: circa 15 ore (assuming now is 9/20/2019 15:00)
 console.log(formatDistanceToNow(new Date(2019, 8, 20), { locale: ro }));
 // Output: 0 minuti
 console.log(
   formatDistanceStrict(
     new Date(2019, 8, 1, 0, 0, 15),
     new Date(2019, 8, 1, 0, 0, 10),
     { locale: it, unit: "minute" }
   )
 );
 // Output: un minuto
 console.log(
   formatDistanceStrict(
     new Date(2019, 8, 1, 0, 0, 10),
     new Date(2019, 8, 1, 0, 0, 15),
     { locale: it, unit: "minute", roundingMethod: "ceil" }
   )
 );
 // Output: amanhã às 14:48
 console.log(formatRelative(addDays(new Date(), 1), new Date(), { locale: ptBR }));
```

`formatRelative`通常与助手一起使用，以增加或减少不同的时间单位，如[增加周数](https://date-fns.org/v2.2.1/docs/addWeeks)、[子月数](https://date-fns.org/v2.2.1/docs/subMonths)、[增加季度数](https://date-fns.org/v2.2.1/docs/addQuarters)等等。

另外，如果日期之间的距离超过六天，`formatRelative`将返回作为第一个参数给出的日期:

```
// If today is September 20, 2019 the output will be 27/09/2019
console.log(formatRelative(addDays(new Date(), 7), new Date(), { locale: ptBR }));
```

你可以试试上面所有的例子[这里](https://codesandbox.io/s/date-fns-example-pvkpn)。

## 天天网

Day.js 是 Moment.js 的一个轻量级库[。](https://garbagevalue.com/blog/dayjs-the-best-lightweight-alternative-to-momentjs)

默认情况下，Day.js 附带美国英语区域设置。要使用其他语言环境，您需要像这样导入它们:

```
import 'dayjs/locale/pt';
import localeDe from 'dayjs/locale/de'; // With a custom alias for the locale object

dayjs.locale('pt') // use Portuguese locale globally
// To use the locale just in certain places
console.log(
  dayjs()
    .locale(localeDe)
    .format()
);
console.log( dayjs('2018-4-28', { locale: 'pt' }) );
```

在这里你可以找到所有支持的语言环境列表。

在上面的例子中， [format()](https://github.com/iamkun/dayjs/blob/dev/docs/en/API-reference.md#format-formatstringwithtokens-string) 方法返回一个带有格式化日期的字符串。它可以接受一个带有标记的字符串，以特定的方式格式化日期:

```
// Sample output: September 2019, Samstag
console.log(
  dayjs()
    .locale(localeDe)
    .format('MMMM YYYY, dddd')
);
```

[这里的](https://github.com/iamkun/dayjs/blob/dev/docs/en/API-reference.md#list-of-all-available-formats)是所有可用格式的列表。

然而，Day.js 的许多高级功能来自于[插件](https://github.com/iamkun/dayjs/blob/dev/docs/en/Plugin.md)，你可以根据自己的需要加载这些插件。例如， [UTC](https://github.com/iamkun/dayjs/blob/dev/docs/en/Plugin.md#utc) 插件添加了获取 UTC 和当地时间日期的方法:

```
import dayjs from "dayjs";
import utc from "dayjs/plugin/utc";

dayjs.extend(utc);

console.log(dayjs.utc().format()); // Sample output: 2019-09-21T11:31:55Z
```

关于国际化，我们可以使用[高级格式](https://github.com/iamkun/dayjs/blob/dev/docs/en/Plugin.md#advancedformat)、[本地化格式](https://github.com/iamkun/dayjs/blob/dev/docs/en/Plugin.md#localizedformat)、[相对时间](https://github.com/iamkun/dayjs/blob/dev/docs/en/Plugin.md#relativetime)和[日历](https://github.com/iamkun/dayjs/blob/dev/docs/en/Plugin.md#calendar)插件。

AdvancedFormat 和 LocalizedFormat 插件为`format()`方法增加了更多的格式选项(你可以在[插件文档页面](https://github.com/iamkun/dayjs/blob/dev/docs/en/Plugin.md#advancedformat)中看到所有选项):

```
// ...

// Plugins
import advancedFormat from "dayjs/plugin/advancedFormat";
import localizedFormat from "dayjs/plugin/localizedFormat";

// Load plugins
dayjs.extend(advancedFormat);
dayjs.extend(localizedFormat);

// Advanced format options
// If today is 2019/09/21 at 12:00 PM, the output will be 3 21º 12 12 1569087454 1569087454869
console.log(
  dayjs()
    .locale("pt")
    .format("Q Do k kk X x")
);
// Localized format options
// If today is 2019/09/21 at 12:00 PM, the output will be Sábado, 21 de Setembro de 2019 às 12:00 
console.log(
  dayjs()
    .locale("pt")
    .format("LLLL")
);
```

`RelativeTime`插件增加了将日期格式化为相对时间字符串的方法:

*   `.fromNow(withoutSuffix?: boolean)`返回一个表示从现在开始的相对时间的字符串
*   `.from(compared: Dayjs, withoutSuffix?: boolean)`返回一个表示从 X 开始的相对时间的字符串
*   `.toNow(withoutSuffix?: boolean)`返回一个表示到现在的相对时间的字符串
*   `.to(compared: Dayjs, withoutSuffix?: boolean)`返回表示相对于 X 的时间的字符串

以下是一些例子:

```
// ...
import relativeTime from "dayjs/plugin/relativeTime";

// Load plugin
dayjs.extend(relativeTime);

// Assuming now is 2019-09-21 at 12:00 PM
// Output: in einem Jahr 
console.log(
  dayjs()
    .locale(localeDe)
    .from(dayjs("2018-09-21"))
);
// Output: einem Jahr 
console.log(
  dayjs()
    .locale(localeDe)
    .from(dayjs("2018-09-21"), true)
);
// Output: vor einem Jahr 
console.log(
  dayjs("2018-09-21")
    .locale(localeDe)
    .fromNow()
);
// Output: vor 2 Jahren 
console.log(
  dayjs("2018-09-21")
    .locale(localeDe)
    .to(dayjs("2016-09-21"))
);
// Output: vor 11 Jahren 
console.log(
  dayjs("2030-09-21")
    .locale(localeDe)
    .toNow()
);
```

日历插件添加了`.calendar`方法来显示日历时间(七天内)。它似乎没有本地化输出:

```
// ...
import calendar from "dayjs/plugin/calendar";

// Load plugin
dayjs.extend(calendar);

// Assuming now is 2019-09-21 at 12:00 PM
// Output: Yesterday at 12:00 PM
console.log(
  dayjs()
    .locale('pt')
    .calendar(dayjs("2019-09-22"))
);
```

但是，它允许您使用字符串文字(用方括号括起来)和日期时间格式标记来手动定制同一天、第二天、上周末和下周的输出标签，以及其他任何内容:

```
// Assuming now is 2019-09-21 at 12:00 PM
// The output is Hoje às 12:00
console.log(
  dayjs().calendar(dayjs("2019-09-21"), {
    sameDay: "[Hoje às] h:m",
    nextDay: "[Amanhã]",
    nextWeek: "dddd",
    lastDay: "[Ontem]",
    lastWeek: "[Último] dddd",
    sameElse: "DD/MM/YYYY"
  })
);
```

你可以试试上面所有的例子[这里](https://codesandbox.io/s/dayjs-example-cgg4f)。

## 结论

对于数据处理来说，Moment.js 是一个健壮而成熟的库，但是，对于某些项目来说，它可能有些矫枉过正。在本文中，我比较了四个流行的库在国际化环境中处理日期格式的方式。

JavaScript 国际化 API 提供的特性对于简单的用例来说可能已经足够了，但是如果您需要一个更高级别的 API(例如，相对时间)和其他特性，比如时区或用于增加或减少时间单位的辅助方法，您可能需要考虑本文中讨论的其他库。

最后，这里有一些您可能会觉得有用的链接:

编码快乐！

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.