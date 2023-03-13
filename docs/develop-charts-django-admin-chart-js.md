# 如何使用 Chart.js 在 Django admin 中开发图表

> 原文：<https://blog.logrocket.com/develop-charts-django-admin-chart-js/>

Django web 框架提供了一个内置的管理界面来管理 web 应用程序的数据。然而，默认情况下， [Django 管理接口](https://docs.djangoproject.com/en/4.1/ref/contrib/admin/)只提供该数据的列表视图，能够过滤、排序和搜索数据。但是，有时，以图形格式显示数据会很有帮助，例如，使用图表。

在本文中，我们将学习如何使用 [Chart.js](https://www.chartjs.org/) 向 Django 管理界面添加图表，这是一个用于创建图表的流行 JavaScript 库。首先，我们将通过创建一个呈现图表的新管理视图来更新 Django 管理。然后，我们将使用 Chart.js 通过应用程序提供的数据创建图表。

[Chart.js 提供了广泛的图表类型](https://blog.logrocket.com/comparing-most-popular-javascript-charting-libraries/#chart-js)，包括条形图、折线图和饼图，以及定制选项，帮助您创建符合您需求的完美图表。一旦创建了图表，它将被嵌入 Django 管理页面，并随着应用程序中的数据变化而自动更新，使您能够获得对数据的新见解，并做出更明智的决策。

*向前跳转:*

## 开发 Django 应用程序

首先，我们将开发用于测试图表的模型。对于本教程，我们将构建一个博客应用程序，用户可以在其中创建文章并将它们保存在数据库中。因为我们将只与 Django 管理员一起工作，所以我们将只为这个应用程序创建模型。

首先，创建一个名为`django-chartjs`的项目文件夹。然后，通过运行以下命令创建虚拟环境:

```
python3 -m venv venv

```

按如下方式激活虚拟环境:

```
python3 -m venv/bin/activate

```

现在，安装 Django:

```
$(venv) pip install django

```

现在我们已经安装了 Django，我们将通过运行以下命令创建一个新的 Django 项目:

```
django-admin startproject project .
python3 manage.py startapp app 

```

将您刚刚创建的应用程序添加到您的`settings.py`文件中:

```
INSTALLED_APPS = [
   ...
    "django.contrib.staticfiles",
    "app"
]

```

### 开发模型

正如我前面提到的，我们的应用程序将允许作者创建新的文章。因此，我们将有两个模型，一个用于作者，一个用于文章细节。打开您的`app/models.py`文件并粘贴以下代码:

```
from django.db import models
class Writer(models.Model):
    name = models.CharField(max_length=200)
    createdDate = models.DateTimeField(auto_now_add=True)
    def __str__(self):
        return u'%s' % (self.name)

class Article(models.Model):
    text_title = models.CharField(max_length=50, null=True, blank=True)
    text = models.TextField(max_length=200, null=True, blank=True)
    refWriter = models.ForeignKey(Writer, on_delete=models.CASCADE)
    createdDate = models.DateTimeField(auto_now_add=True)
    def __str__(self):
        return u'[%s] : %s' % (self.refWriter,self.text_title)

```

要设置数据库，请运行以下迁移命令:

```
python manage.py makemigrations
python manage.py migrate

```

## 自定义管理模板

安装 Django 时，会在虚拟环境中创建一个文件夹，其中包含构建 Django 应用程序所需的所有文件，包括管理页面的 HTML。要编辑 Django 管理页面，您需要将虚拟环境中的`Django`文件夹中的 HTML 文件放到您的 Django 项目中。

这样做将覆盖虚拟环境中`Django`文件夹中的 admin HTML 文件。因为我们将在列表页面上工作，我们将需要`venv/lib/python3.10/site-packages/django/contrib/admin/templates/admin/change_list.html`文件。

首先，在项目的根目录下，创建一个带有`templates/admin/`路径的目录。在刚刚创建的 admin 目录中，创建一个名为`change_list.html`的新文件。

现在，复制`venv/lib/python3.10/site-packages/django/contrib/admin/templates/admin/change_list.html to templates/admin/change_list.html`中的内容。

我们需要将`templates/`添加到 Django 项目的基本目录中，以便 Django 知道这个目录。一旦完成，管理页面将利用`templates/admin/change_list.html`。用下面的代码更新你的`settings.py`文件:

```
import os
...
TEMPLATES = [
    {
        "BACKEND": "django.template.backends.django.DjangoTemplates",
        "DIRS": [
            BASE_DIR,
            os.path.join(BASE_DIR, 'templates') 
        ],
...

```

### 更新`Admin.py`文件

我们希望更新 writer list 页面，以显示基于按日期创建的 writer 数量的图表。

在呈现`change_list.html`页面时，Django 使用了一种叫做`changelist_view()`的方法。我们可以在`WriterAdmin`类中覆盖这个方法，稍后我们将创建这个类来实现业务逻辑。

现在，让我们更新`app/admin.py`文件来实现这个逻辑，并在 JSON 中获得相应的数据。将以下代码粘贴到您的`admin.py`文件中:

```
from django.contrib import admin
from app.models import *
from django.db.models.functions import TruncDay
from django.db.models import Count
from django.core.serializers.json import DjangoJSONEncoder
import json

# Register your models here.
class WriterAdmin(admin.ModelAdmin):
    # change_list.html
    def changelist_view(self, request, extra_context=None):
        # Aggregate new authors per day
        chart_data = (
            Writer.objects.annotate(date=TruncDay("createdDate"))
            .values("date")
            .annotate(y=Count("id"))
            .order_by("-date")
        )
        # Serialize and attach the chart data to the template context
        as_json = json.dumps(list(chart_data), cls=DjangoJSONEncoder)
        print("Json %s"%as_json)
        extra_context = extra_context or {"chart_data": as_json}
        # Call the superclass changelist_view to render the page

        return super().changelist_view(request, extra_context=extra_context)

admin.site.register(Writer, WriterAdmin)
admin.site.register(Article)
```

上面的代码是 Django 管理视图，它每天聚集新的作者，并将数据作为 JSON 对象附加到模板上下文。`WriterAdmin`类覆盖了`changelist_view`方法来将图表数据添加到上下文中，并且调用了`super().changelist_view`方法来呈现页面。`Writer`和`Article`型号也在管理站点注册。

## 在 Django 上配置 Chart.js

为了配置 Chart.js 以显示所需的图表，我们首先引入 Chart.js 的 JavaScript 和 CSS 文件。将以下代码粘贴到您在`templates/admin/change_list.html`中看到的第一个`{{ block.super }}`标签下面:

```
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/2.8.0/Chart.min.css"/>
<script src= "https://cdnjs.cloudflare.com/ajax/libs/Chart.js/2.8.0/Chart.bundle.min.js"> </script>

```

## 用 Chart.js 显示图表

现在，我们将使用从`admin.py`文件中检索的数据来实现显示我们想要的图表的逻辑。我们将在文档中添加一个事件监听器，等待触发`DOMContentLoaded`事件。

一旦事件触发，它将检索 HTML canvas 元素的 2D 上下文，该元素带有从 Django 模板传入的 ID 和变量。然后，它将`chartData`中的日期解析成 JavaScript `Date`对象。最后，它使用 2D 上下文、`chartData`和一些配置选项创建一个新的 Chart.js 图表，比如图表类型、背景颜色、刻度和刻度选项。

图表将呈现在 ID 为`myChart`的 canvas 元素上，我们将在后面实现。要实现这一点，将以下代码粘贴到最后一个`{% endblock %}`模板标签之上:

```
 <script>
    document.addEventListener('DOMContentLoaded', () => {
        const ctx = document.getElementById('myChart').getContext('2d');
        const chartData = {{ chart_data | safe }};

        // Parse the dates to JS
        chartData.forEach((d) => {d.x = new Date(d.date);});

        // Add your javascript chart presentation below this comment
        var myChart = new Chart(ctx, {
          type: 'bar',
          data: {
              datasets: [{
                  label: 'number of writers',
                  data: chartData,
                  backgroundColor: 'rgba(61,61,131,0.5)',
              }]
          },
          options: {
              responsive: true,
              scales: {
                  xAxes: [{
                      type: 'time',
                      time: {
                          unit: 'day',
                          round: 'day',
                          displayFormats: {
                              day: 'MMM D',
                          },
                      },
                  }, ],
                  yAxes: [{
                      ticks: {
                          beginAtZero: true,
                      },
                  }, ],
              }
          }
      });

    });
    </script>

```

将以下代码粘贴到您希望图表显示的位置。对于本教程，我们将把下面的代码粘贴到`templates/admin/change_list.html`中的
`<div id="content-main">`标签之上，从而把它放在作者列表之上:

```
<canvas id="myChart" width="400" height="50"></canvas>

```

现在所有的配置都已完成，您需要一个超级用户来访问项目的管理页面。您可以使用以下命令创建超级用户:

```
python manage.py createsuperuser

```

通过运行以下命令启动 Django 应用程序:

```
python manage.py runserver

```

您可以使用您的超级用户详细信息访问`[http://127.0.0.1:8000/admin](http://127.0.0.1:8000/admin)`上的管理员。用不同的日期创建新的作者，您会看到一个图表，显示某一天创建的用户数量:

![Access Django Admin ChartJS](img/0fac7b4ec1d885dfee08dcc5e9fb78c9.png)

## 结论

在本文中，我们学习了如何使用 [Chart.js 向 Django 管理界面添加图表，这是一个作为 JavaScript 包](https://blog.logrocket.com/using-chart-js-react/)提供的图表库。通过创建一个新的管理视图，您可以使用 Chart.js 创建各种图表类型，并根据您的需要定制它们。

该图表将被嵌入 Django 管理页面，并将根据不断变化的数据自动更新，为明智的决策提供新的见解。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)