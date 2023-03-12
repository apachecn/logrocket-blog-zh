# 如何用 Django REST 框架创建 REST API

> 原文：<https://blog.logrocket.com/django-rest-framework-create-api/>

编者按:本教程最后一次更新是在 2022 年 3 月 17 日，以反映 Django 的更新。

Django REST 框架(DRF)是一个用于构建 web APIs 的强大而灵活的工具包。在本教程中，我们将学习如何使用 Django REST 框架在 15 分钟内构建一个 CRUD API。

为了构建我们的示例待办事项列表应用程序，我们将从在 Django 项目中设置 Django REST 框架开始，然后是如何使用 [Django REST 框架](https://www.django-rest-framework.org/)创建 CRUD REST API 的完整教程。

我们开始吧！

### 目录

## 姜戈是什么？

Django 是一个[免费、开源、基于 Python 的 web 框架](https://blog.logrocket.com/whats-new-django-v4/)，它遵循模型-视图-模板(MVT)架构模式。它减少了 web 开发的麻烦，因此您可以专注于编写应用程序，而不是重新发明轮子。

## 什么是 REST API？

REST API 是系统公开有用函数和数据的一种流行方式。REST 代表表述性状态转移，可以由一个或多个资源组成，这些资源可以在给定的 URL 上访问，并以各种格式返回，比如 JSON、images、HTML 等等。

## 为什么选择 Django REST 框架？

Django REST 框架(DRF)是一个用于构建 Web APIs 的强大而灵活的工具包。它的主要好处是[使得序列化更加容易](https://blog.logrocket.com/understanding-typescript-object-serialization/)。

Django REST 框架基于 Django 基于类的视图，所以如果你熟悉 Django，这是一个很好的选择。它采用了基于类的视图、表单、模型验证器、QuerySet 等实现。

## 设置 Django REST 框架

理想情况下，您会希望创建一个虚拟环境来隔离依赖关系，但是，这是可选的。从项目文件夹中运行命令`python -m venv django_env`来创建虚拟环境。然后，运行`source ./django_env/bin/activate`将其打开。

请记住，您需要在每个新的终端会话中重新激活您的虚拟环境。您将知道它是打开的，因为环境的名称将成为 shell 提示符的一部分。

导航到终端中的一个空文件夹，使用下面的命令在项目中安装 Django 和 Django REST 框架:

```
pip install django
pip install django_rest_framework

```

使用以下命令创建一个名为`todo`的 Django 项目:

```
django-admin startproject todo

```

然后，`cd`进入新的`todo`文件夹，为您的 API 创建一个新的应用程序:

```
django-admin startapp todo_api

```

运行内置用户模型的初始迁移:

```
python manage.py migrate

```

接下来，将`rest_framework`和`todo`添加到`todo/todo/settings.py`文件中的`INSTALLED_APPS`中:

```
# settings.py
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'rest_framework',
    'todo_api'
]

```

在`todo/todo_api`中创建一个`serializers.py`和`urls.py`文件，并按照下面的目录结构配置添加新文件:

```
├── todo
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
├── db.sqlite3
├── manage.py
└── todo_api
    ├── admin.py
    ├── serializers.py
    ├── __init__.py
    ├── models.py
    ├── urls.py
    └── views.py

```

确保在主`urls.py`文件中包含如下所示的`rest_framework`和 URL:

```
# todo/todo/urls.py : Main urls.py
from django.contrib import admin
from django.urls import path, include
from todo_api import urls as todo_urls

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api-auth/', include('rest_framework.urls')),
    path('todos/', include(todo_urls)),
]

```

接下来，创建超级用户。我们稍后将回到这一点:

```
python manage.py createsuperuser

```

## RESTful 结构:`GET`、`POST`、`PUT`和`DELETE`方法

在 RESTful API 中，端点用`GET`、`POST`、`PUT`和`DELETE` HTTP 方法定义结构和用法。你必须有逻辑地组织这些方法。

为了展示如何使用 Django REST 框架构建 RESTful 应用程序，我们将创建一个示例待办事项 API。我们将使用两个端点及其各自的 HTTP 方法，如下表所示:

| 端点 | T2`GET` | T2`POST` | T2`PUT` | T2`DELETE` |
| --- | --- | --- | --- | --- |
| T2`todos/api/` | 1.全部列出:列出请求用户的所有待办事项 | 2.创建:添加新的待办事项 | 不适用的 | 不适用的 |
| T2`todos/api/<int:todo_id>` | 3.检索:用给定的 `todo_id` 获取待办事项 | 不适用的 | 4.更新:用给定的 `todo_id` 更新待办事项 | 5.删除:用给定的 `todo_id` 删除一条待办事项 |

## 为 Django 应用程序创建模型

让我们从为待办事项列表创建模型开始:

```
# todo/todo_api/models.py
from django.db import models
from django.contrib.auth.models import User

class Todo(models.Model):
    task = models.CharField(max_length = 180)
    timestamp = models.DateTimeField(auto_now_add = True, auto_now = False, blank = True)
    completed = models.BooleanField(default = False, blank = True)
    updated = models.DateTimeField(auto_now = True, blank = True)
    user = models.ForeignKey(User, on_delete = models.CASCADE, blank = True, null = True)

    def __str__(self):
        return self.task

```

创建模型后，将其迁移到数据库中。

```
python manage.py makemigrations
python manage.py migrate

```

### 模型序列化程序

为了将`Model`对象转换成适合 API 的格式，如 JSON，Django REST framework 使用`ModelSerializer`类将任何模型转换成序列化的 JSON 对象:

```
# todo/todo_api/serializers.py
from rest_framework import serializers
from .models import Todo
class TodoSerializer(serializers.ModelSerializer):
    class Meta:
        model = Todo
        fields = ["task", "completed", "timestamp", "updated", "user"]

```

## 在 Django 中创建 API 视图

在这一节中，我们将介绍如何创建两个 API 视图，列表视图和细节视图。

### 列表视图

第一个 API 视图类处理`todos/api/`端点，其中它处理用于列出给定请求用户的所有待办事项的`GET`和用于创建新待办事项的`POST`。请注意，我们添加了`permission_classes`，它只允许经过身份验证的用户:

```
# todo/todo_api/views.py
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
from rest_framework import permissions
from .models import Todo
from .serializers import TodoSerializer

class TodoListApiView(APIView):
    # add permission to check if user is authenticated
    permission_classes = [permissions.IsAuthenticated]

    # 1\. List all
    def get(self, request, *args, **kwargs):
        '''
        List all the todo items for given requested user
        '''
        todos = Todo.objects.filter(user = request.user.id)
        serializer = TodoSerializer(todos, many=True)
        return Response(serializer.data, status=status.HTTP_200_OK)

    # 2\. Create
    def post(self, request, *args, **kwargs):
        '''
        Create the Todo with given todo data
        '''
        data = {
            'task': request.data.get('task'), 
            'completed': request.data.get('completed'), 
            'user': request.user.id
        }
        serializer = TodoSerializer(data=data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)

        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

```

`GET()`方法首先通过使用请求的用户 ID 过滤从模型中获取所有对象。然后，它从模型对象序列化为 JSON 序列化对象。接下来，它返回带有序列化数据和状态的响应，如`200_OK`。

`POST()`方法获取请求的数据，并将请求的用户 ID 添加到`data`字典中。接下来，它创建一个序列化的对象，并保存有效的对象。如果有效，它返回`serializer.data`，这是一个新创建的对象，状态为`201_CREATED`。否则，返回状态为`400_BAD_REQUEST`的`serializer.errors`。

为上面基于类的视图创建一个端点:

```
# todo/todo_api/urls.py : API urls.py
from django.conf.urls import url
from django.urls import path, include
from .views import (
    TodoListApiView,
)

urlpatterns = [
    path('api', TodoListApiView.as_view()),
]

```

运行 Django 服务器:

```
python manage.py runserver

```

现在，我们准备好进行第一次测试。导航至`[http://127.0.0.1:8000/todos/api/](http://127.0.0.1:8000/todos/api/)`。确保您使用超级用户凭据登录:

![Django Server First Test](img/30f82c5b8cf229dfe18bc068c627ad92.png)

您可以通过发布以下代码来创建新的待办事项:

```
{
    "task": "New Task",
    "completed": false
}

```

### 详细视图

现在我们已经成功创建了第一个端点视图，让我们创建第二个端点`todos/api/<int:todo_id>` API 视图。

在这个 API 视图类中，我们需要创建三个方法来处理相应的 HTTP 方法，`GET`、`PUT`和`DELETE`，如上所述:

```
# todo/api/views.py
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
from todo.models import Todo
from .serializers import TodoSerializer
from rest_framework import permissions

class TodoDetailApiView(APIView):
    # add permission to check if user is authenticated
    permission_classes = [permissions.IsAuthenticated]

    def get_object(self, todo_id, user_id):
        '''
        Helper method to get the object with given todo_id, and user_id
        '''
        try:
            return Todo.objects.get(id=todo_id, user = user_id)
        except Todo.DoesNotExist:
            return None

    # 3\. Retrieve
    def get(self, request, todo_id, *args, **kwargs):
        '''
        Retrieves the Todo with given todo_id
        '''
        todo_instance = self.get_object(todo_id, request.user.id)
        if not todo_instance:
            return Response(
                {"res": "Object with todo id does not exists"},
                status=status.HTTP_400_BAD_REQUEST
            )

        serializer = TodoSerializer(todo_instance)
        return Response(serializer.data, status=status.HTTP_200_OK)

    # 4\. Update
    def put(self, request, todo_id, *args, **kwargs):
        '''
        Updates the todo item with given todo_id if exists
        '''
        todo_instance = self.get_object(todo_id, request.user.id)
        if not todo_instance:
            return Response(
                {"res": "Object with todo id does not exists"}, 
                status=status.HTTP_400_BAD_REQUEST
            )
        data = {
            'task': request.data.get('task'), 
            'completed': request.data.get('completed'), 
            'user': request.user.id
        }
        serializer = TodoSerializer(instance = todo_instance, data=data, partial = True)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_200_OK)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

    # 5\. Delete
    def delete(self, request, todo_id, *args, **kwargs):
        '''
        Deletes the todo item with given todo_id if exists
        '''
        todo_instance = self.get_object(todo_id, request.user.id)
        if not todo_instance:
            return Response(
                {"res": "Object with todo id does not exists"}, 
                status=status.HTTP_400_BAD_REQUEST
            )
        todo_instance.delete()
        return Response(
            {"res": "Object deleted!"},
            status=status.HTTP_200_OK
        )

```

`GET()`方法首先从 to-do 模型中获取 ID 为`todo_id`的对象和作为请求用户的用户。如果请求的对象不可用，它将返回状态为`400_BAD_REQUEST`的响应。否则，它将模型对象序列化为 JSON 序列化对象，并返回带有`serializer.data`和状态为`200_OK`的响应。

`PUT()`方法获取数据库中可用的 to-do 对象，用请求的数据更新其数据，并将更新的数据保存在数据库中。

`DELETE()`方法获取数据库中可用的待办事项对象，删除它，并用一个响应进行响应。

如下所示更新 API `urls.py`:

```
# todo/api/urls.py : API urls.py
from django.conf.urls import url
from django.urls import path, include
from .views import (
    TodoListApiView,
    TodoDetailApiView
)

urlpatterns = [
    path('api', TodoListApiView.as_view()),
    path('api/<int:todo_id>/', TodoDetailApiView.as_view()),
]

```

现在，如果您导航到`[http://127.0.0.1:8000/todos/api/<id>/](http://127.0.0.1:8000/todos/api/<id>/)`，它将显示详细的 API 视图页面。请注意，您正确地导航到了一个有效的 id。在下面的截图中，我使用了`7`作为 ID:

![Detail API View Page Final](img/e4a8edcfd017095d6bac2e2b48e904ea.png)

## 结论

恭喜你！您已经成功地构建了第一个功能完整的 CRUD Django REST API。

构建 RESTful API 可能很复杂，但是 Django REST 框架很好地处理了这种复杂性。我希望您在使用 Django REST 框架构建新 API 的过程中获得乐趣，如果您有任何问题，请务必留下评论。编码快乐！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)