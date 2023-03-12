# Django v4.0 中的新内容- LogRocket 博客

> 原文：<https://blog.logrocket.com/whats-new-django-v4/>

2021 年 12 月， [Django 团队发布了 Django v4](https://docs.djangoproject.com/en/4.0/releases/4.0/) ，它包含了对框架的各种升级，比如改进的定制和对表单、表单集和`ErrorList`的模板引擎的使用。

但是宣布只有 Python v3.6、3.9、3.10 版本会支持 Django v4.0，另外 Django v3.2.x 系列是支持 Python v3.6、3.7 的最终版本。

在本文中，我们将介绍 Django v4.0 中的一些新特性，以及 Django v4.0 中已经删除的一些旧的第三方版本。让我们开始吧！

## 升级到 Django v4.0 版

在升级到 Django v4.0 之前，确保在使用当前的 Django 版本时解决项目报告的任何不赞成使用的警告。默认情况下，不推荐使用警告是关闭的。要启用弃用警告，请使用下面的测试命令:

```
$ python -Wa manage.py test

```

### 装置

一旦你修复了任何反对警告，你就可以继续安装 Django v4.0。如果你[用 pip](https://docs.djangoproject.com/en/4.0/topics/install/) 安装了 Django，你可以使用`--upgrade`或`-U`标志:

```
$ python -m pip install -U Django

```

## Django v4.0 版不再支持

随着 Django v4.0 的推出，一些较低的第三方包版本将不再受支持。下面，你会发现一个列表和每个的描述。

### postgresql9.6

Django v4.0 不支持 PostgreSQL v9.6 及更早版本，Django v4.0 将只支持 PostgreSQL ≥v10。

### Oracle v12.2 和 18c

Django v4.0 将只支持 Oracle ≥v19。Django 团队已经正式移除了对 Oracle 版本 18c 和更早版本的支持。根据公告，截至 2024 年 4 月，Django v3.2 将不再支持 Oracle ≤v18c。

### Django v4.0 中增加的包和函数

Django v4.0 版不支持以下功能和第三方软件包:

*   PostGIS 2.3
*   GDAL 2.0 和 GEOS 3.5
*   cx_ORACLE 7.0
*   `django.utils.text.unescape_entities()`功能已被弃用
*   `django.utils.http.is_safe url()`功能已被弃用

## Django v4.0 有什么新功能？

### `zoneinfo`默认时区

在 Django v4.0 中，[默认的`pytz`时区已经迁移到了`zoneinfo`](https://blog.logrocket.com/python-datetime-module-handling-dates-time/) 。对`pytz`时区的支持现在被否决了，在即将发布的 Django v5.0 和更高版本中将不再支持。

迁移到`zoneinfo`相当简单。您可以选择当前时区，并将表单和模板中的`datetime`实例转换为当前时区。UTC 中对 aware `datetime`实例的操作不受影响。

### 基于模板的表单呈现

表单、表单集和`ErrorList`现在使用模板引擎呈现，以增强定制。

Django 团队对 Django v4.0 中如何使用`render()`、`get_context()`和`template_name`进行了修改。`render()`选项现在是可选的，其中`None`是所有选项的默认值。

使用以下代码时:

```
render(template_name=None, context=None, renderer=None)

```

如果没有传入参数值，参数将默认为以下值:

*   `template_name` : `template_name()`
*   `Context`:包含由`get_context()`返回的值
*   `renderer`:由`default_renderer`返回的值

为了灵活起见，`formset`渲染已经被移到 Django v4.0 版的模板引擎中。

当在视图中使用`formset`时，您将在模板中使用`management`表单。让我们来看一个`view`的例子:

```
from django.forms import formset_factory
from django.shortcuts import render
from myapp.forms import ArticleForm
def manage_articles(request):
    ArticleFormSet = formset_factory(ArticleForm)
    if request.method == 'POST':
        formset = ArticleFormSet(request.POST, request.FILES)
        if formset.is_valid():
            # do something with the formset.cleaned_data
            pass
    else:
        formset = ArticleFormSet()
    return render(request, 'manage_articles.html', {'formset': formset})

```

`manage_articles.html`模板看起来像下面的代码:

```
<form method="post">
    <table>
        {{ formset }}
    </table>
</form>

```

### 国际化

Django v4.0 现在支持马来语翻译，这在 Django 以前的版本中是不可用的。开发人员现在可以在他们的项目中转换马来语内容。

### 本地化

在 Django v4.0 中，参数`USE_L10N`的默认值从`False`更改为`True`，以遵循最佳实践。

随着 Dango v4.0 的发布，`USE_L10N`已经被弃用。还需要注意的是，在 Django v5.x 中，任何日期或数字都会被默认本地化。

### `CSRF_TRUSTED_ORIGINS`

设置`CSRF_TRUSTED_ORIGINS`设置时，Django v4.0 不启用仅使用`hostname`值；相反，这些值必须包含方案，例如`http://`或`https://`。

此外，以点开头的值现在必须以星号开头。例如，你可以用`[https://*.example.com](https://*.example.com)`代替`.example.com`。

### scrypt 密码散列器

Django v4.0 中添加了 [scrypt 密码散列器](https://docs.djangoproject.com/en/4.0/topics/auth/passwords/#scrypt-usage)以提供额外的安全性，建议您使用 [scrypt 而不是 PBKDF2](https://docs.djangoproject.com/en/4.0/topics/auth/passwords/) 来限制攻击者可以利用的并行数量。

scrypt 被设计为比其他基于密码的密钥派生技术使用更多的内存。

### 功能唯一约束

`UniqueConstraint()`现在有了一个新的`*expressions`位置选项，允许程序员使用与`Index.expressions`相同的数据库限制来实现函数唯一约束，如下面的代码所示:

```
from django.db import models
from django.db.models import UniqueConstraint
from django.db.models.functions import Lower

class MyModel(models.Model):
    first_name = models.CharField(max_length=255)
    last_name = models.CharField(max_length=255)
    class Meta:
        constraints = [
            UniqueConstraint(
                Lower('first_name'),
                Lower('last_name').desc(),
                name='first_last_name_unique',
            ),
        ]

```

`Meta.restrictions`选项用于将功能独特的约束应用于模型。

## 结论

在本文中，我们讨论了 Django 4.0 中的一些新特性，Django 4.0 不再支持的一些第三方包，以及将现有版本升级到 Django 4.0 所需的步骤。

Django v4.0 的新特性不仅限于本文中提到的。关于新功能的完整列表，请查看 Django 的官方声明。我希望你喜欢这个教程！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)