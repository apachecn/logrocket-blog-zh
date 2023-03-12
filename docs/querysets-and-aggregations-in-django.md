# Django 中的查询集和聚合

> 原文：<https://blog.logrocket.com/querysets-and-aggregations-in-django/>

## 介绍

Django 中的对象关系映射器(ORM)使得开发人员在没有数据库和 SQL 工作知识的情况下也能轻松高效地工作。QuerySets 表示数据库中对象的集合，可以对其进行构造、过滤、切片或传递，而无需实际访问数据库。在我们对查询集进行评估之前，不会发生任何数据库活动。在本指南中，您将了解如何执行这些查询，包括基本查询和高级查询。

在整个指南中，我们将引用`django.contrib.auth.models.User`模型。您可以将多个用户插入到该模型中，以测试以下指南中讨论的不同查询集。

此外，我们将使用 Django shell 来运行和测试查询。您可以使用以下命令启动 Django shell:

```
python manage.py shell

```

## 基本查询

让我们从一些基本的 QuerySet 操作开始。

### 检索单个对象

对于您知道只有一个对象匹配查询的情况，您可以使用`get()`方法，它将返回该对象。不像`filter`，它总是返回`QuerySet`:

```
>>> user_id_1 = User.objects.get(id=1)

```

注意，如果没有找到结果，它将引发一个`DoesNotExist`异常，所以最好在 try-except 块中使用它:

```
try:
user_id_1 = User.objects.get(id=1)
except User.DoesNotExist:
print("User with id does not exists")

```

### 从查询集中获取对象

从 QuerySet 获取对象有两个选项。

第一种是使用`first()`和`last()`。`First()`返回与查询集匹配的第一个对象，`last()`返回与查询集匹配的最后一个对象:

```
from django.contrib.auth.models import User

>>> User.objects.filter(is_active=True).first()

>>> User.objects.filter(is_active=True).last()

```

上述查询将返回与 Queryset 匹配的第一个和最后一个对象。

第二个选项是`latest()`和`earliest()`。`Latest()`根据给定的字段返回表格中最新的对象，`earliest`根据给定的字段返回表格中最早的对象:

```
from django.contrib.auth.models import User

>>> User.objects.latest('date_joined')

>>> User.objects.earliest('date_joined')

```

### 字段查找

字段查找处理如何指定 SQL `WHERE`子句。基本的查找关键字参数采用`field__lookuptype=value`的形式。例如:

```
from datetime import datetime
## Get all users whose date_joined is less than today's date.
>>> User.objects.filter(date_joined__lte=datetime.today())

```

搜索特定字符串(区分大小写):

```
## Get all user whose username string contains "user"
>>> User.objects.filter(username__contains = "user")

```

或不区分大小写:

```
## Get all user whose username string contains "user" (case insensitive)
>>> User.objects.filter(username__icontains = "user")

```

或者，以搜索开始，以搜索结束:

```
## Get all user whose username string starts with "user"
>>> User.objects.filter(username__startswith = "user")
## Get all user whose username string ends with "user"
>>> User.objects.filter(username__endswith = "user")

```

您也可以使用不区分大小写的版本，称为`istartswith`和`iendswith`。

### 排序查询集

过滤查询集后，您可以根据给定的字段对其进行升序或降序排序。

下面的查询将首先根据`is_active`过滤用户，然后按照用户名升序，最后按照`date_joined`降序。注意`-`表示`date_joined`的降序:

```
from django.contrib.auth.models import User

>>> User.objects.filter(is_active=True).order_by('username', '-date_joined')

```

### 链接过滤器

Django 提供了将几个过滤器添加到链细化中的选项:

```
import datetime
from django.contrib.auth.models import User

>>> User.objects.filter(
... username__startswith='user'
... ).filter(
... date_joined__gte=datetime.date.today()
... ).exclude(
... is_active=False
... )

```

上面的查询最初获取所有用户，添加两个过滤器，并排除一个。最终结果是一个 QuerySet，包含所有用户，他们的`username`以`user`开始，他们的`date_joined`大于或等于今天的日期，最后，排除非活动用户。

## 高级查询

现在，您已经了解了基本的 QuerySet 操作，让我们跳到高级查询和 QuerySet 操作。

### 集合操作

`Union()`使用 SQL `UNION`运算符组合两个或多个查询集的结果:

```
>>> qs1.union(qs2, qs3, ...)

```

`Intersection()`使用 SQL `INTERSECTION`运算符查找两个或更多查询集的公共(共享)结果:

```
>>> qs1.intersection(qs2, qs3, ...)

```

`Difference()`使用 SQL `EXCEPT`操作符查找存在于查询集中但不存在于其他查询集中的元素:

```
>>> qs1.difference(qs2, qs3, ...)

```

### q 对象

一个`Q()`对象表示一个 SQL 条件，可以在与数据库相关的操作中使用。如果您想执行包含`OR`、`AND`和`NOT`语句的复杂查询，您可以使用`Q()`对象:

```
>>> from django.db.models import Q

>>> Q(username__startswith='user')
<Q: (AND: ('username__startswith', 'user'))>

```

例如，让我们查找所有员工或超级用户:

```
>>> from django.contrib.auth.models import User

>>> User.objects.filter(Q(is_staff=True) | Q(is_superuser=True))

```

类似地，你可以使用`AND`和`NOT`。在下面的查询中，它会找到所有的员工用户，并且这些用户的用户名不以`user`开头:

```
>>> User.objects.filter(Q(is_staff=True) & ~Q(username__startswith='user'))

```

### f 对象

`F()`对象表示模型字段或带注释的列的值。它使得引用模型字段值并使用它们执行数据库操作成为可能，而不必实际将它们从数据库中取出到 Python 内存中。

让我们举一个例子，用`id=1`的`HitCount`模型将点击计数增加 1。
通常，一个显而易见的方法是将其保存在内存中，递增计数，然后保存:

```
site = HitCount.objects.get(id=1)
site.hits += 1
site.save()

```

另一种完全由数据库处理的方法是引入`F()`对象。当 Django 遇到一个`F()`的实例时，它会覆盖标准的 Python 操作符来创建一个封装的 SQL 表达式:

```
from django.db.models import F

site = HitCount.objects.get(id=1)
site.hits = F('hits') + 1
site.save()

```

`F()`通过以下方式提供性能优势:

*   让数据库而不是 Python 来执行操作
*   减少某些操作所需的查询数量

## 执行原始 SQL 查询

Django 提供了两种使用`raw()`和`connection.cursor()`执行原始 SQL 查询的方法。

为了清楚起见，让我们进行一个获取非员工用户的基本查询:

```
from django.contrib.auth.models import User

User.objects.filter(is_staff = False)

```

### 执行原始查询

`Raw()`获取一个原始 SQL 查询，执行它，并返回一个`RawQuerySet`实例，可以像普通的 QuerySet 一样对其进行迭代，以提供对象实例:

```
query = "select * from auth_user where is_staff=False;"
results = User.objects.raw(query)
for result in results:
print(result)

```

### 直接执行自定义 SQL

有时甚至`raw`都不够；您可能需要执行不能完全映射到模型的查询，或者直接执行`UPDATE`、`INSERT`或`DELETE`查询。在这些情况下，您总是可以直接访问数据库，完全绕过模型层。

例如，您可以使用光标运行上述 SQL 查询，如下所示:

```
from django.db import connection

query = "select * from auth_user where is_staff=False;"
with connection.cursor() as cursor:
cursor.execute(query)
print(cursor.fetchall())

```

更多关于这个主题的信息请参考 Django 的文档[这里](https://docs.djangoproject.com/en/3.1/topics/db/sql/)。

### 获取给定查询集的原始 SQL

要从 Django QuerySet 中获取原始 SQL 查询，可以使用`.query`属性。这将返回`django.db.models.sql.query.Query`对象，然后可以使用`__str__()`将其转换为字符串:

```
>>> queryset = MyModel.objects.all()
>>> queryset.query.__str__()
from django.contrib.auth.models import User

>>> queryset = User.objects.all()
>>> queryset.query
<django.db.models.sql.query.Query at 0x1ff0dcf7b08>

>>> queryset.query.__str__()
'SELECT "auth_user"."id", "auth_user"."password", "auth_user"."last_login", "auth_user"."is_superuser", "auth_user"."username", "auth_user"."first_name", "auth_user"."last_name", "auth_user"."email", "auth_user"."is_staff", "auth_user"."is_active", "auth_user"."date_joined" FROM "auth_user"'

```

## 聚合

按查询分组是相当常见的 SQL 操作，有时在 ORM 中会引起混淆。在这一节中，我们将深入应用`GROUP BY`和聚合。

### 基本`GROUP BY`和聚合

让我们从基本的计数操作开始，它将返回包含用户计数的`dict`:

```
>>> User.objects.aggregate(total_users=Count('id'))

```

### 使用注释

`Aggregate`用于汇总整表。大多数情况下，我们希望将聚合应用于多组行，为此，可以使用`annotate`。

让我们看一个基于`is_staff`对用户分组的例子:

```
>>> User.objects.values("is_staff").annotate(user_count=Count('*')

```

要以 orm 风格执行 group by，我们必须使用下面的两种方法`values`和`annotate`:

*   `values(<col>)`:说明分组依据的字段
*   `annotate(<aggr function>)`:使用`SUM`、`COUNT`、`MAX`、`MIN`、`AVG`等函数，说明要聚合什么

### 多个聚合和字段

对于多个聚合，我们需要添加多个字段作为分组依据。在下面的例子中，我们执行了一个按列分组的查询(`is_active`，`is_staff`):

```
>>> User.objects.values("is_active", "is_staff").annotate(user_count = Count("*"))

```

### `HAVING`条款

`HAVING`子句用于过滤组。在下面的查询中，我过滤了计数大于 1 的组:

```
>>> User.objects.values("is_staff").annotate(user_count=Count("*")).filter(user_count__gt = 1)

```

等效的 SQL 查询是:

```
SELECT is_staff, COUNT(*) AS user_count
FROM auth_user
GROUP BY is_staff
HAVING COUNT(*) > 1;

```

## 结论

在本指南中，我们讨论了查询集的各种方法以及如何处理不同的查询。通过对 Django 的 QuerySets 背后的简单概念的一些关注和理解，您可以改进您的代码并成为一名更好的 Django 开发人员。您可以随时参考 [Queryset 文档](https://docs.djangoproject.com/en/3.1/ref/models/querysets/)和[聚合文档](https://docs.djangoproject.com/en/3.1/topics/db/aggregation/)进行进一步研究。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)