# Python 日期时间模块:处理日期和时间

> 原文：<https://blog.logrocket.com/python-datetime-module-handling-dates-time/>

Python 是一种高级的、通用的、解释型编程语言，构建时考虑了简单性和可读性。有了各种简化语言的模块，Python 对初学者友好且易于使用；一个例子是 Python `datetime`模块，它帮助我们管理 Python 中复杂的日期和时间计算。

每个开发人员在处理日期和时间时都会遇到的一个主要问题是全球时区的差异。使用 Python `datetime`模块，您可以编写以分钟、小时、秒或毫秒为单位获取一个月中的某一天、一周中的某一天以及本地日期和时间的程序。

Python 的`datetime`模块由五个主要的类组成，`date`、`time`、`tzinfo`、`DateTime`和`timedelta`。在本文中，我们将讨论这些类，涵盖每个类的相关示例。

### 先决条件

要阅读本文，您需要以下内容:

*   对 Python 的基本理解
*   您的计算机上安装的最新版本的 Python
*   IDE，[最好是 PyCharm](https://blog.logrocket.com/pycharm-vs-vscode/)

我们开始吧！

## `datetime`模块类

Python `datetime`模块帮助我们处理与时间相关的事件，如年、月、周、日、小时、分钟、秒等。虽然最常用的类是`DateTime`、`Date`、`Time`、`Tzinfo`和`Timedelta`，但是为了让[获得 Python `datetime`模块](https://docs.python.org/3/library/datetime.html)中的其他元素，请运行下面的代码:

```
import datetime
print(dir(datetime))

```

## `datetime`阶级

`datetime`类为 Python 开发人员提供了操纵日期和时间的能力。为了在我们的 Python 程序中使用`datetime`类，我们需要从`datetime`模块中导入它。让我们编写一个简单的 Python 程序，使用 Python `datetime`模块打印时间和日期:

```
from datetime import datetime
# create a variable
todays_date = datetime.now()
print(todays_date)

```

上面的代码将打印当前时间，包括年、月、日、小时、分钟和秒。

## 使用字符串

Python `datetime`模块有两个内置方法，`strptime()`和`strftime()`，分别帮助 Python 程序员将`time`字符串转换或解析为对象，将 Python 字符串`time`转换或解析为`DateTime`对象。我们来回顾一下这些。

### `strptime()`

`strptime()`方法将字符串`date`和`time`数据转换成一个`DateTime`对象。以下代码说明了如何在 Python 中使用这些方法:

```
from datetime import datetime
date_in_string = ‘2021-11-19’
convert_date_to_object = datetime.strptime(date_in_string, ‘%Y-%m-%d’)
print(convert_date_to_object)

```

在上面的代码中，`strptime`函数有两个参数，`date_in_string`变量和第二个字符串，第二个字符串显示一个格式或占位符，演示第一个变量应该如何表示。

以下列表显示了表示 Python `DateTime`变量的各种格式:

```
%a:  abbreviated weekday as Sun, Mon
%A:  weekdays full name 
%w:  weekdays as number
%d:  days in number and zero-padded 01, 02
%b:  Months abbreviate as Apr, Jun
%B:  Months full name April, June
%m:  months in number and zero-padded 03, 06
%y:   Year without century 21, 20, 19
%Y:  Year with century 2021, 2020, 2019
%H:  24 hours clock 00 - 23 zero-padded
%I:   12 hours clock 01 - 12 zero-padded
%p:   Period of the day as AM/PM
%M:  Minutes from 00 - 59 zero-padded
%s:   seconds from 00 - 59 zero-padded
%f:    microseconds 6 decimal places

```

要确认输出是一个对象，通过运行以下代码使用`type`函数:

```
print(type(convert_date-to_object))

```

### `strftime()`

`strftime()`方法将`DateTime`对象转换成字符串。下面的代码演示了如何在 Python 中使用`strftime()`方法:

```
from datetime import datetime
time_as_object = datetime.today()
print(time_as_object)
# to check the type use the code below
print(type(time_as_object))
# convert time_as_object to string
time_as_string = time_as_object.strftime(“%Y-%m-%d %H:%M:%S”)
print(time_as_string)
# to add the milliseconds use .%f
time_as_string = time_as_object.strftime(“%Y-%m-%d %H:%M:%S.%f”)
print(time_as_string)
# check type
print(type(time_as_string))
```

> 注意:还有更多可用的格式，我没有包括在这里。你可以参考上面的列表，尝试不同的格式进行练习。

## `date`对象:提取星期几和月几

Python `date`对象将日期表示为年、月和日。在这一节中，我们将从`date`类中提取星期几、月几和年几。我们还将使用日历模块获得日期名称。

Python 编程语言从周一开始计算一周中的某一天。作为一般的编程规则，第一天从索引`0`开始。

在我们提取`date`类的内容之前，让我们用下面的代码片段来说明 Python 如何读取一周中的日期:

```
import calendar
for i in calendar.day_name:
        print(i)
# i represent items in calendar.day_name

```

上述程序的结果如下:

```
Monday
Tuesday
Wednesday
Thursday
Friday
Saturday
Sunday

```

有了这些细节，我们可以开始从`date`中提取日期和月份。下面的代码片段说明了如何提取各种组件:

```
from datetime import datetime
import calendar
day_variable = datetime.now
print(day_variable)
# this will print year, month, date and the time the code was run

# to print index of day of the week
print(day_variable.weekday)

# to print the date
print(day_variable.day)

# to print the month in number
print(day_variable.month)

# to print the year
print(day_variable.year)

# to print the name of the day
print(calendar.day_name[day_variable.weekday()])

```

## 对象:提取小时、分钟和秒

`time`对象是 Python `datetime`模块中的一个类，表示一天中的本地时间。让我们看看如何从时间类中提取`hour`、`minutes`和`second`组件。Python 时间构造函数采用一些可选参数，最常用的是小时、分钟、秒和毫秒。

下面的代码片段说明了如何使用 Python `time`对象:

```
from datetime import time

# instantiate a new time object
time_variable = time()

# extract its component
print('The hour is: ', time_variable.hour)

print('The miniute: ', time_variable.minute)

print('The second is: ', time_variable.second)

```

上面代码的结果如下:

```
0 
0 
0\. 

```

上面的代码表示 Python `time`类中`hour`、`minute`和`second`的默认值。让我们接下来实例化一个带三个参数的构造函数:

```
from datetime import time

# instantiate a new time object
time = time(7, 57, 5)

# extract its component
print(“The hour is:  ”,  time.hour)
print(“The minute is: ”,  time.minute)
print(“The second is:  ”,  time.second)

```

上面代码的结果将是:

```
The hour is: 7
The minute is: 57
The second is: 5

```

在上面的例子中，我们对值进行了硬编码，但是我们需要编写一个程序，从您的计算机中获取本地时间。然后，我们将按照下面的例子提取`time`组件:

```
from datetime import datetime
import calendar

time = datetime.now()

# print year, month, day, and time (hour, minute, seconds, and microseconds)
print(time.today())

# extract the component by printing them out
print(time.year)

# this will print the current year
# print the month
print(time.month)

# print the index of the day of the week
print(time.weekday())

# print the date of the month in a number format
print(time.day)

# print the name of the day of the month
print(calendar.day_name[time.weekday()])

```

## `tzinfo`:使用时区信息

您可能还记得，由于时区的差异，Python `datetime`模块是必要的。`datetime.now()`使用电脑上的时间，因为它没有任何关于时区的信息。

假设一名开发人员正在从事一个面向全球受众的项目，他们需要根据用户的时区显示时间。Python 为处理这种情况提供了一个非常有用的模块，即`pytz`模块。

`pytz`模块帮助开发人员处理时区转换。下面的代码片段说明了如何使用 Python `pytz`模块:

```
from datetime import datetime
import pytz

# get the local time
local_time = datetime.now()
print(“Local time is: “, local_time)

tz_Lagos = pytz.timezone(‘Africa/Lagos’)
datetime_in_Lagos = datetime.now(tz_Lagos)
print(datetime_in_Lagos)

# use f string and print timezone and time together
print(f‘ Time in {tz_Lagos} is {datetime_in_Lagos}’)

```

根据您在地球上的位置，您可以使用这个 Python 模块打印任何时区的时间。例如，我在非洲的拉各斯，我想打印欧洲莫斯科的当前时间。我可以使用下面的代码片段做到这一点:

```
from datetime import datetime
import pytz

timeZone_in_Moscow = pytz.timezone(‘Europe/Moscow’)
datetime_in_Moscow = datetime.now(timeZone_in_Moscow)
print(datetime_in_Moscow)

```

这个程序将把莫斯科的当前时间打印到控制台上，即使我在非洲。既然我们知道了如何获取不同时区的时间，那就来说说`timedelta`。

## Python `timedelta`对象

Python `timedelta`是一个表示持续时间的对象，持续时间是两个时间或日期之间的差值。在 Python `datetime`模块中，`timedelta`采用可选参数，所有初始值都设置为零。

为了得到两个时间或日期之间的差异，我们首先需要导入`timedelta`:

```
# import timedelta 
from datetime import timedelta, datetime

# get current time
today = datetime.now()

# create a timedelta
weeks_ago = timedelta(weeks=4)

# print the difference between today and 4 weeks ago

difference = today - week_ago
print(difference)

```

`timedelta`对象可以接受以下参数:周、秒、分钟、毫秒、微秒、小时和天。上述代码的结果会根据您运行程序的时间而有所不同。

## 了解`tzinfo`类

Python `datetime`模块中的另一个类`tzinfo`，在处理特定时区的细节时非常有用。Python `tzinfo`类是一个抽象类，所以它不能被实例化。

要实现这个类中的各种方法，必须从它派生出一个具体的子类。现在可以将`tzinfo`的实例传递给`datetime`和`time`构造函数对象。下面列出了`tzinfo`类中的一些方法:

1.  `utcoffset(dt)`:返回本地时间相对于 UTC 的偏移量作为`timedelta`对象
2.  `dst(dt)`:如果夏令时不可用，返回`None`。否则，它返回夏令时作为`timedelta`对象
3.  `tzname(dt)`:以字符串形式返回对应的`datetime`对象时区名称

## Python `datetime`模块示例应用

让我们使用我们到目前为止已经介绍过的信息来构建一个生日计算器，它将打印用户的当前年龄和他们下一个生日的倒计时。倒计时将包括他们下一个生日前剩余的天数、小时数、分钟数和秒数，以及他们的生日是星期几。我们的生日计算器将用户的生日作为`Line 20`的输入:

```
import calendar
import datetime
# the neccessary modules we need

current_time_and_date = datetime.datetime.now()
# access the current date and time

# access today name
today_name = calendar.day_name[current_time_and_date.weekday()]

# declare a time object
class Time(object):
  current_time_and_date = datetime.now()

  def __init__(self, year=1, month=1, day=1, hour=0, minute=0, second=0):
        self.date = datetime.datetime(year, month, day, hour, minute, second

# initialize two variables to hold today's date and the birthday respectively
today = Time().current_time_and_date
birthday = Time(1960, 12, 4).date

# declare a function that returns today using f-string

def name_of_day_of_the_week():
  return f'Today is {today_name}'

# declare a function that receives birthday as an argument

def birthday_records(birthday_param):
  age = today.year - birthday_param.year

  if (birthday_param.month == today.month) and (birthday_param.day <= today.day):
      pass
  elif birthday_param.month < today.month:
      pass
  else
      age = age - 1

  birthday_now = Time(today.year, birthday_param.month, birthday_param.day).date

  next_birthday = str(birthday_now - today).split()

  if len(next_birthday) > 1:
      days = int(next_birthday[0])
      time = next_birthday[2].split(":")

  else:
      days = 365
      time = next_birthday[0].split(":")

  hours = [0]
  minutes = [1]
  seconds = \[2\][:2]

  if days < 0 and days != 365:
      days += 365

  elif days == 365:
        days = 0

  else:
      days = days

print("2 You are %s years old; and is next birthday is %sd:%sh:%sm:%ss" & (age, days, hours, minutes, seconds))

#invoke the necessary functions

print(name_of_day_of_the_week())
birthday_records(birthday)

```

## 结论

在本教程中，我们学习了如何使用 Python `datetime`模块处理日期和时间。
我们还讲述了如何使用 Python `datetime`模块提供的各种类。最后，我们通过构建一个示例应用程序来使用我们的知识，该应用程序倒计时到用户下一个生日的天数、分钟数和秒数。

在 Python 中处理日期和时间并不总是那么简单。在本文中，我们学习了如何使用 Python `datetime`模块来简化这个过程。希望这篇文章提供了一些帮助您开始的说明。如果你有任何问题，一定要留下评论。

## 用[log 火箭](https://lp.logrocket.com/blg/signup)突破传统错误报告的噪音

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)