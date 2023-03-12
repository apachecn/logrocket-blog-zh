# 在 Django - LogRocket 博客中动态过滤查询集

> 原文：<https://blog.logrocket.com/filtering-querysets-dynamically-in-django/>

## 介绍

要构建一个允许过滤和分页的列表页面，您必须将一些独立的东西组合在一起。Django 的对象关系映射器(ORM)和内置的分页类使开发人员在不了解如何处理数据库和 SQL 的情况下也能轻松提高工作效率。在本指南中，您将学习如何使用 AJAX 动态过滤查询集。

对于本文中的例子，我在这里按国家[取了 Spotify 上排名前 50 的歌曲的数据集](https://www.kaggle.com/leonardopena/top-50-spotify-songs-by-each-country)。你也可以从[这里](https://github.com/krazygaurav/qs-filter-django/blob/master/top50contry.csv)下载相同的数据集。像往常一样，本指南中使用的代码可以在 GitHub 上获得。您可以在本指南的末尾找到链接。

## 入门指南

首先，像这样开始一个新的 Django 项目:

```
django-admin startproject my_proj

```

然后，创建一个示例应用程序:

```
cd my_proj
python manage.py startapp my_app

```

更新`settings.py`:

```
INSTALLED_APPS += [
    'my_app'
]

```

以下是您将在指南中遵循的目录结构:

```
├── db.sqlite3
├── manage.py
├── my_app/
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── migrations/
│   ├── models.py
│   ├── templates/
│   │   ├── base.html
│   │   └── index.html
│   ├── tests.py
│   └── views.py
├── my_proj/
│   ├── __init__.py
│   ├── asgi.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
└── top50contry.csv
└── requirements.txt

```

## 数据准备

在跳转到实际代码之前，我们首先需要将所有数据推送到数据库。

我已经创建了一个名为`TopSongPoularity`的基本模型来存储来自数据集的必要信息。

下面是`my_app`的`models.py`:

```
## my_app/models.py

from django.db import models

class TopSongPoularity(models.Model):
    title = models.CharField(max_length = 220)
    artist = models.CharField(max_length = 220)
    top_genre = models.CharField(max_length = 220)
    year = models.IntegerField()
    pop = models.IntegerField()
    duration = models.IntegerField()
    country = models.CharField(max_length = 100)

    def __str__(self):
        return self.title

```

现在您已经创建了模型，通过以下方式将其迁移到数据库:

```
python manage.py makemigrations
python manage.py migrate

```

接下来，我们必须将所有 CSV 数据推送到数据库，因此我们将使用 shell 来执行一个脚本:

```
python manage.py shell

```

在 shell 中运行以下脚本，将 CSV 数据推送到数据库:

```
#Django Shell
import csv
from datetime import datetime

from my_app.models import TopSongPoularity

with open('top50contry.csv', 'r') as fin:
    reader = csv.reader(fin)
    headers = next(reader, None)
    for row in reader:
        obj = {
            "title": row[1],
            "artist": row[2],
            "top_genre": row[3],
            "year": int(row[4]),
            "pop": int(row[15]),
            "duration": int(row[12]),
            "country": row[16]
        }
        TopSongPoularity.objects.create(**obj)

```

## 创建视图

接下来，我们来写观点。`ListTopSongs`是一个基于类的视图(CBV ),它继承了`View`类。在该类的`get()`方法中，它接受查询参数并相应地过滤查询集。在 QuerySet 被过滤后，它调用`get_paginated_context()`来获取序列化格式的分页数据。

`getCountries()`是一个基于函数的视图(FBV ),它返回数据库中所有唯一国家的 JSON 输出:

```
#my_app/views.py
import json

from django.core.paginator import Paginator
from django.core.serializers import serialize
from django.http import JsonResponse
from django.shortcuts import render
from django.views import View

from .models import TopSongPoularity

def index(request):
    return render(request, "index.html", {})

class ListTopSongs(View):
    # set default page limit as 10
    page_limit = 10 # default

    '''
    Helper method to get the pagination context
    out of queryset of given page number with limit.
    Args: 
        queryset: Filtered queryset object
        page: a number representing the page number
        limit: the result count, per page.

    Returns the JSON of queryset for the given page, 
        with pagination meta info.
    '''
    def get_paginated_context(self, queryset, page, limit):
        if not page:    page = 1 # if no page provided, set 1

        # if limit specified, set the page limit
        if limit:   
            self.page_limit = limit  

        # instantiate the paginator object with queryset and page limit
        paginator = Paginator(queryset, self.page_limit)
        # get the page object
        page_obj = paginator.get_page(page)
        # serialize the objects to json
        serialized_page = serialize("json", page_obj.object_list)
        # get only required fields from the serialized_page json.
        serialized_page = [obj["fields"] for obj in json.loads(serialized_page)]

        # return the context.
        return {
            "data": serialized_page,
            "pagination": {
                "page": page,
                "limit": limit,
                "has_next": page_obj.has_next(),
                "has_prev": page_obj.has_previous(),
                "total": queryset.count()
            }
        }

    '''
    GET method for this View.
    '''
    def get(self, request, *args, **kwargs):
        # fetch the query params
        page = request.GET.get('page')
        limit = request.GET.get('limit')
        country = request.GET.get('country')
        start = request.GET.get('start')
        end = request.GET.get('end')

        sort_by = request.GET.get('sort_by')
        # get all results from DB.
        queryset = TopSongPoularity.objects.all()

        '''filter the queryset object based on query params'''
        # 1\. on basis of country
        if country and country != "all":
            queryset = queryset.filter(country=country)
        # 2\. On basis of date (start and end date)
        if start and end:
            if start != "0" and end != "0":
                queryset = queryset.filter(
                    year__gte = start, 
                    year__lte = end
                )

        # 3\. Sorting the filtered queryset
        if sort_by and sort_by != "0":
            queryset = queryset.order_by(sort_by)

        # return the serialized output by 
        # calling method 'get_paginated_context'
        to_return = self.get_paginated_context(queryset, page, limit)
        return JsonResponse(to_return, status = 200)

def getCountries(request):
    # get Countries from the database 
    # excluding null and blank values
    if request.method == "GET" and request.is_ajax():
        country = TopSongPoularity.objects.all().\
                values_list('country').distinct()
        country = [c[0] for c in list(country)]

        return JsonResponse({
            "country": country, 
        }, status = 200)

```

## 创建 URL

现在，让我们路由视图:

```
#my_proj/urls.py
from django.urls import path
from my_app.views import ListTopSongs, index, getCountries

urlpatterns = [
    path('api/get/top_songs', ListTopSongs.as_view()),
    path('api/get/countries', getCountries, name = "get_countries"),
    path('', index)
]

```

## 创建模板

既然后端代码已经完成，让我们转到前端。

我使用了一个基本模板(`base.html`)，如下所示，它包括引导和 jQuery 库:

```
<!--templates/base.html-->
<!--doctype HTML-->
<html>
<head>
    <meta charset="utf-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta property="og:locale" content="en_US" />
    <meta charset="utf-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">

    <title>Log rocket</title>
    <!-- css cdn includes -->
    <link rel="stylesheet" href = "https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css">
    {% block style %}
    {% endblock style %}
</head>

<body>
    {% block content %}
    {% endblock %}
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.1.1/jquery.min.js"></script>
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/js/bootstrap.min.js"></script>
    {% block javascript %}
    {% endblock javascript %}
</body>

</html>

```

现在让我们创建`index.html`来显示带有过滤器的表格。这个模板文件继承了`base.html`并创建了一个带有表头和空表体的表格。最后，它还包含“下一页”和“上一页”两个按钮。

`index.html`的剩余部分，即 JavaScript 部分，解释如下:

```
<!--templates/index.html-->
{% extends 'base.html' %}

{% block content %}
<section>
    <div class="container-fluid">
        <div class="row">
            <div class="col-sm-2 col-2">
                <div class="form-group">
                    <label for="country">Country</label>
                    <select class="form-control" id="countries" url={% url 'get_countries' %}>
                    </select>
                </div>
            </div>
            <div class="col-sm-2 col-2">
                <div class="form-group">
                    <label for="year">Year</label>
                    <select class="form-control" id="year">
                        <option value="0" start=0 end=0>All years</option>
                        <option value="1" start=2019 end=2020>2019-2020</option>
                        <option value="2" start=2018 end=2019>2018-2019</option>
                        <option value="3" start=2016 end=2018>2016-2018</option>
                        <option value="4" start=2010 end=2016>2010-2016</option>
                        <option value="5" start=1900 end=2010>1900-2010</option>
                    </select>
                </div>
            </div>

            <div class="col-sm-2 col-2">
                <div class="form-group">
                    <label for="sort">Sort By</label>
                    <select class="form-control" id="sort">
                        <option value="0">No option selected</option>
                        <option value="duration">Duration</option>
                        <option value="pop">Pop</option>
                        <option value="year">Year</option>
                    </select>
                </div>
            </div>
        </div>
    </div>
</section>

<section>
    <div class="container">
        <div class="row justify-content-center table-responsive">
            <div id="result-count" class="text-right">
                <span class='font-weight-bold'></span> results found.
            </div>
            <div id="page-count" class="text-right">Page:
                <span class='font-weight-bold'></span>
            </div>

            <table class="table table-light table-bordered table-hover" id="hero_table" data-toggle="table">
                <thead class="thead-dark">
                    <tr>
                        <th data-field="title">Title</th>
                        <th data-field="country">Country</th>
                        <th data-field="top_genre">Top Genre</th>
                        <th data-field="artist">Artist</th>
                        <th data-field="duration">Duration</th>
                        <th data-field="pop">Pop</th>
                        <th data-field="year">Year</th>
                    </tr>
                </thead>
                <tbody id="table_body">
                </tbody>
            </table>
        </div>
        <div class="row justify-content-center">
            <nav aria-label="navigation">
                <ul class="pagination">
                    <li class="page-item">
                        <button class="btn btn-primary page-link" id = "previous">Previous</button>
                    </li>
                    <li class="page-item pull-right">
                        <button class="btn btn-primary page-link" id="next">Next</button>
                    </li>
                </ul>
            </nav>
        </div>
    </div>
</section>
{% endblock content %}

```

## 创建客户端脚本

本指南的最后一部分是使用 AJAX 连接前端和后端。请参考下面代码片段中提到的注释:

```
<!---templates/index.html--->
{% block javascript %}
<script>
    // maintaining the state of each variable.
    var current_page = 1; // maintains the current page
    var page_limit = 10; // the limit of results shown on page.
    var sort_by = ""; // maintains the select option for sort_by
    var country = ""; // maintains the select option for country
    var start_year = ""; // maintains the select option for start_yr
    var end_year = ""; // maintains the select option for end_yr

    function get_list_url(page) {
        // returns the consructed url with query params.
        return `api/get/top_songs?page=${page}&limit=${page_limit}&country=${country}&sort_by=${sort_by}&start=${start_year}&end=${end_year}`;
    }

    function getCountries() {
        // call the ajax and populates the country select options
        $.ajax({
            method: 'GET',
            url: $("#countries").attr("url"),
            success: function (response) {
                countries_option = "<option value='all' selected>All Countries</option>";
                $.each(response["country"], function (a, b) {
                    countries_option += "<option>" + b + "</option>"
                });
                $("#countries").html(countries_option)
            },
            error: function (response) {
                console.log(response)
            }
        });
    }

    // On select change of the country select, call the getAPIData
    $("#countries").on("change", function (e) {
        current_page = 1;
        country = this.value
        getAPIData(get_list_url(current_page));
    });
    // On select change of the year select, call the getAPIData
    $("#year").on("change", function (e) {
        current_page = 1;
        start_year = $(this).find(':selected').attr("start");
        end_year = $(this).find(':selected').attr("end");
        getAPIData(get_list_url(current_page));
    })
    // On select change of the sort select, call the getAPIData with sortby.
    $("#sort").on("change", function (e) {
        current_page = 1;
        sort_by = this.value
        getAPIData(get_list_url(current_page));
    })

    // Helper method that popluates the html table with next and prev
    // url, and current page number.
    function putTableData(response) {
        // creating table row for each response and
        // pushing to the html cntent of table body of table_body table
        let row;
        $("#table_body").html("");
        if (response["data"].length > 0) {
            $.each(response["data"], function (a, b) {
                row = "<tr> <td>" + b.title + "</td>" +
                    "<td>" + b.country + "</td>" +
                    "<td>" + b.top_genre + "</td>" +
                    "<td>" + b.artist + "</td>" +
                    "<td>" + b.duration + "</td>" +
                    "<td>" + b.pop + "</td>" +
                    "<td>" + b.year + "</td>" +
                    $("#table_body").append(row);
            });
        }
        else{
            // if there is no results found!
           $("#table_body").html("No results found."); 
        }
        if (response.pagination.has_prev) {
            // sets the previous page url.
            $("#previous").attr("data-url", get_list_url(current_page - 1));
            $("#previous").attr("disabled", false);
        } else {
            // if there is no prev page available, disable the btn.
            $("#previous").attr("disabled", true);
        }
        if (response.pagination.has_next) {
            // sets the next page url.
            $("#next").attr("data-url", get_list_url(current_page + 1));
            $("#next").attr("disabled", false);
        } else {
            // if there is no next page available, disable the btn.
            $("#next").attr("disabled", true)
        }
    }

    // On click of next/prev button, call the getAPIData with the given url.
    $(".page-link").click(function (e) {
        e.preventDefault();
        let url = $(this).attr("data-url");
        getAPIData(url);
    })

    // Main method which calls AJAX to get the data from backend.
    function getAPIData(url) {
        $.ajax({
            method: 'GET',
            url: url,
            success: function (response) {
                current_page = parseInt(response.pagination.page)
                putTableData(response);
                // put the total result count.
                $("#result-count span").html(response.pagination.total)
                $("#page-count span").html(response.pagination.page)
            },
            error: function (response) {
                $("#hero_table").hide();
            }
        });
    }

    //on page load, call this two methods.
    getAPIData(get_list_url(current_page));
    getCountries()
</script>
{% endblock javascript %}

```

## 结论

在本指南中，您已经学习了如何使用 AJAX 以及如何与后端异步通信。过滤表格数据是一个需要处理的常见场景，我希望本指南能让您更好地理解如何处理数据过滤。

如果您愿意，您也可以使用 REST 框架，如 [Django REST framework](https://www.django-rest-framework.org/) 来简化事情。

如果您在遵循指南的过程中遇到任何问题，您可以随时查看我的 [Github 库](https://github.com/krazygaurav/qs-filter-django)来查看整个项目。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)