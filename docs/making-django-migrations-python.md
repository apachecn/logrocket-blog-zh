# 在 Python 中进行 Django 迁移

> 原文：<https://blog.logrocket.com/making-django-migrations-python/>

Python 的 [Django web 框架](https://www.djangoproject.com/)抽象出了构建 web 应用程序时的许多复杂性。它的一个关键方面是代表用户与给定数据库进行动态交互的能力。

尽管如此，任何开发人员都需要了解幕后发生了什么，否则可能会导致灾难性的后果。

在本教程中，您将与 Django migrations 密切合作来管理 SQL 数据库，并学习如何排除代价高昂的错误。

## 姜戈是什么？

Django 是一个流行的 web 框架，允许开发人员快速启动 web 应用程序。被吹捧为“有期限的完美主义者的 web 框架”，Django 负责许多可能会减缓开发过程的底层功能。

Django 提供了路由 URL、认证用户和与数据库接口的现成功能。这对于那些很少或没有结构化查询语言( [SQL](https://en.wikipedia.org/wiki/SQL) )经验的人来说特别有用。

SQL 是一种特定于领域的编程语言，用于与关系数据库管理系统进行交互。使用 SQL，可以创建、读取、更新和删除给定数据库中的记录。然而，SQL 错综复杂，运行不正确的查询会很快导致敏感数据或不可恢复数据的丢失。

Django 通过使用一个对象关系映射器来解决这个问题。换句话说，您告诉 Django 您希望数据库具有什么样的结构，Django 负责将您的 Python 指令翻译成要在您选择的数据库上执行的 SQL 查询。如果需要的话，您仍然可以编写 SQL，但这不是必须的:您只需用 Python 定义您的数据模型，Django 会处理剩下的事情。

这里有一个 Django 数据模型的例子:

```
class Rocket(models.Model):
    """A model of a rocket ship."""
    name = models.CharField(max_length=200)
    launch_date = models.DateField()
    ready_to_launch = models.BooleanField(default=True)

```

这个数据模型定义了名为`Rocket`的数据库表的结构。该模型规定`Rocket`表应该有三列:`name`、`launch_date`和`ready_to_launch`。

注意，这个数据模型没有指定索引或行计数器；这是因为 Django 在引擎盖下自动实现了这一点。

一旦您准备好了，您将把这个数据模型传递给 Django，在那里它将被转换成下面的数据库表:

| 身份证明（identification） | 名字 | 发布日期 | 准备发射 |
| --- | --- | --- | --- |
| 一 | 阿波罗 | 2035-10-19 | 错误的 |
| 2 | 猎户星座 | 2022-04-05 | 真实的 |

数据模型的字段对应于数据库表中的标题。这个表已经填充了数据，Django 已经为每一行自动创建了一个新的`id`。

## 什么是迁移？

那么，Django 到底是如何把你的 Python 模型翻译成数据库表的呢？当然，将表结构定义为数据模型并不是全部。

好吧，为了从数据模型到数据库表，你需要一个迁移。这是一种特殊类型的 Python 文件，其中包含 Django 代表您创建数据库表所需的指令。

Django 使用您的数据模型来填充这些指令，然后运行一个`migrate`命令来将这些更改应用到数据库。

简而言之，Django 迁移遵循两个关键步骤:

1.  创建包含更改数据库表的说明的迁移文件
2.  通过运行迁移文件中包含的代码来迁移数据库表

这个两步过程确保只应用您明确希望进行的更改。如果迁移文件包含错误或缺少关键字段，您可以在将更改应用到数据库本身之前纠正问题。

这个过程使得即使是初级的 web 开发人员与健壮的数据库管理系统交互也变得极其简单。不需要复杂的 SQL 查询或使用基于浏览器的管理控制台来更改数据库。

只需定义您的数据模型，迁移您的更改，您就可以开始了！迁移是 Django 快速原型能力的一个很好的例子。

## 建立 Django 项目

让我们通过创建一个新文件夹并激活一个虚拟环境来初始化我们的项目:

```
$ mkdir django-migrations-tutorial
$ cd django-migrations-tutorial
$ python3 -m venv django-tut
$ source django-tut/bin/activate

```

`source django-tut/bin/activate`命令将激活 Linux 或 macOS 上的`django-tut`虚拟环境。如果您使用的是 Windows，该命令看起来会略有不同:

`django-tut/bin/activate`。

在您的虚拟环境中，安装 Django 的最新版本:

```
(django-tut) $ python3 -m pip install django

```

现在已经安装了 Django，您可以使用`startproject`命令启动一个新项目。让我们将我们的项目命名为`log_rocket`:

```
(django-tut) $ django-admin startproject log_rocket.

```

尾随句点告诉 Django 将项目安装在当前目录中。下面是项目目录现在的样子(不包括虚拟环境目录):

```
(django-tut) $ tree
.
├── log_rocket
│   ├── asgi.py
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
└── manage.py

1 directory, 6 files

```

## 执行 Django 迁移

因为 Django 充当了一个脚手架来帮助您引导您的 web 开发，所以它预先打包了几个内部数据模型，并且会在您应用初始迁移时自动为您创建一个 SQLite 数据库。

要迁移 Django 的内部数据模型并创建初始数据库，您将使用`migrate`管理命令:

```
(django-tut) $ python3 manage.py migrate
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying admin.0003_logentry_add_action_flag_choices... OK
  Applying contenttypes.0002_remove_content_type_name... OK
  Applying auth.0002_alter_permission_name_max_length... OK
  Applying auth.0003_alter_user_email_max_length... OK
  Applying auth.0004_alter_user_username_opts... OK
  Applying auth.0005_alter_user_last_login_null... OK
  Applying auth.0006_require_contenttypes_0002... OK
  Applying auth.0007_alter_validators_add_error_messages... OK
  Applying auth.0008_alter_user_username_max_length... OK
  Applying auth.0009_alter_user_last_name_max_length... OK
  Applying auth.0010_alter_group_name_max_length... OK
  Applying auth.0011_update_proxy_permissions... OK
  Applying auth.0012_alter_user_first_name_max_length... OK
  Applying sessions.0001_initial... OK

```

输出应该显示 Django 已经成功地运行了自己的内部指令来创建一个初始数据库。现在，如果您再次检查目录结构，您应该会看到一个新文件:

```
(django-tut) $ tree
.
├── db.sqlite3
├── log_rocket
│   ├── asgi.py
│   ├── __init__.py
│   ├── __pycache__
│   │   ├── __init__.cpython-38.pyc
│   │   ├── settings.cpython-38.pyc
│   │   └── urls.cpython-38.pyc
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
└── manage.py

2 directories, 10 files

```

在树的顶部，有一个新的数据库文件`db.sqlite3`。这是一个 SQLite 数据库，是 Django 为本地开发和测试目的创建的默认数据库。

您可以使用`dbshell`管理命令来检查新数据库，并确认 Django 已经创建了表:

```
(django-tut) $ python3 manage.py dbshell
SQLite version 3.28.0 2019-04-16 19:49:53
Enter ".help" for usage hints.
sqlite>

```

在提示符下，键入`.tables`以查看数据库中所有表的列表:

```
sqlite> .tables
auth_group                  auth_user_user_permissions
auth_group_permissions      django_admin_log
auth_permission             django_content_type
auth_user                   django_migrations
auth_user_groups            django_session
sqlite> .exit

```

您可以使用`.exit`命令退出数据库 shell。

## 为新模型创建迁移

Django 内置了一些数据模型，但是您需要从头开始定义其中的大部分。在这一节中，我们将创建一个 Django 应用程序，为它定义一个模型，并将您的更改迁移到数据库中。

之前，我们启动了一个名为`log_rocket`的 Django 项目。现在，您将创建一个附加到此项目的应用程序。Django 项目是一个目录，其中包含管理所有相关应用程序所需的全局设置。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

Django 应用程序是一个大型项目的独立部分，关注整体的一个独立部分。例如，一个 web 项目可能包含一个博客应用程序、一个用户应用程序、一个时事通讯应用程序等等。

让我们为我们的项目创建一个名为`rockets`的应用:

```
(django-tut) $ python3 manage.py startapp rockets

```

要告诉 Django 您的新应用程序，请将其名称添加到项目设置文件的`INSTALLED_APPS`设置中:

```
# log_rocket/settings.py
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'rockets' # add your new app name here
]

```

这将在您的项目目录旁边创建一个名为`rockets`的新目录。如果您检查这个新目录中的文件，您会看到 Django 已经提供了定义新数据模型所需的文件:

```
(django-tut) $ tree rockets/
rockets/
├── admin.py
├── apps.py
├── __init__.py
├── migrations
│   └── __init__.py
├── models.py
├── tests.py
└── views.py

1 directory, 7 files

```

有一个`models.py`文件，您将使用它来定义您的数据模型，还有一个`migrations/`文件夹，用于 Django 存储将您的数据模型转换成数据库表的指令。

让我们将之前看到的`Rocket`数据模型移动到`models.py`文件中。在文本编辑器中打开文件，然后粘贴代码:

```
from django.db import models

# Create your models here.
class Rocket(models.Model):
    """A model of a rocket ship."""
    name = models.CharField(max_length=200)
    launch_date = models.DateField()
    ready_to_launch = models.BooleanField(default=True)

```

保存并关闭文件。现在，您需要告诉 Django，您想在数据库中创建一个新表。换句话说，您需要创建一个迁移文件供 Django 使用。幸运的是，Django 提供了一个方便的管理命令，您可以使用它来完成这项工作，这个命令叫做`makemigrations`:

```
(django-tut) $ python3 manage.py makemigrations rockets
Migrations for 'rockets':
  rockets/migrations/0001_initial.py
    - Create model Rocket

```

您看到的输出是 Django，它确认您想要为`rockets`应用程序创建一个迁移文件，在这个文件中您已经定义了一个名为`Rocket`的新模型。

该数据模型的指令保存在`migrations/`文件夹中，文件名为`0001_initial.py`。您可以打开这个文件并确认 Django 将要做的更改与您在`models.py`文件中指定的相匹配。

一旦您确认了指令是正确的，您可以告诉 Django 使用`migrate`命令来应用它们:

```
(django-tut) $ python3 manage.py migrate rockets
Operations to perform:
  Apply all migrations: rockets
Running migrations:
  Applying rockets.0001_initial... OK

```

所有迁移都已成功应用。现在，当您打开`dbshell`并检查表格列表时，您应该会看到一个新的:

```
(django-tut) $ python3 manage.py dbshell
SQLite version 3.28.0 2019-04-16 19:49:53
Enter ".help" for usage hints.
sqlite> .tables
auth_group                  django_admin_log
auth_group_permissions      django_content_type
auth_permission             django_migrations
auth_user                   django_session
auth_user_groups            rockets_rocket
auth_user_user_permissions
sqlite> .quit

```

Django 以应用程序的名称(`rockets`)和您指定的模型的小写名称(`Rockets`)来命名新表`rockets_rocket`。

您可以使用一个`INSERT` SQL 查询向新表中手动添加数据:

```
sqlite> insert into rockets_rocket (name, launch_date, ready_to_launch) values ("Apollo", "2035-10-19", False);
sqlite> insert into rockets_rocket (name, launch_date, ready_to_launch) values ("Orion", "2022-04-05", True);
sqlite> select * from rockets_rocket;
1|Apollo|2035-10-19|0
2|Orion|2022-04-05|1

```

这里，您使用了您在`Rocket`模型中定义的相同的字段名称。每行被自动分配一个唯一的`id`。

注意，您已经手动将字符串插入到了`launch_date`列中，尽管您的数据模型指定这些字段应该是`date`对象。因为您已经将字符串放入了数据库，所以您需要更新数据模型，将该列中的值作为字符数据来处理。下一节将带您了解如何做到这一点。

## 将迁移应用到现有模型

你第一次做模型不太可能也是最后一次。通常情况下，您需要调整您的模型，以反映对底层数据库的期望更改。

例如，`launch_date`字段现在包含手动插入数据库的字符串数据。然而，在您的`Rocket`数据模型中，您最初决定这个字段应该包含一个`date`对象。

因为手动将字符串插入数据库更容易，所以让我们将模型上的这个字段更改为接受字符数据:

```
from django.db import models

# Create your models here.
class Rocket(models.Model):
    """A model of a rocket ship."""
    name = models.CharField(max_length=200)
    launch_date = models.CharField(max_length=200) # Update this line
    ready_to_launch = models.BooleanField(default=True)

```

现在，运行相同的命令告诉 Django 对数据模型的更改:

```
(django-tut) $ python3 manage.py makemigrations rockets
Migrations for 'rockets':
  rockets/migrations/0002_alter_rocket_launch_date.py
    - Alter field launch_date on rocket

```

Django 正确地认识到您已经修改了`launch_date`字段。因为这是您希望发生的，所以您可以应用此迁移:

```
(django-tut) $ python3 manage.py migrate rockets
Operations to perform:
  Apply all migrations: rockets
Running migrations:
  Applying rockets.0002_alter_rocket_launch_date... OK

```

从现在开始，Django 将确保所有动态生成的发布日期都呈现为字符串。

您可以使用`dbshell`来查看数据库模式并确认所做的更改:

```
sqlite> .schema rockets_rocket
CREATE TABLE IF NOT EXISTS "rockets_rocket" ("id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, "name" varchar(200) NOT NULL, "ready_to_launch" bool NOT NULL, "launch_date" varchar(200) NOT NULL);

```

在这里，您可以看到`"launch_date"`列应该包含`varchar(200)`数据，或者长度不超过 200 个字符的字符数据。

## 逆转迁移

假设您已经决定最好将`launch_date`呈现为日期对象。这将使安排火箭发射变得更加容易，因为您可以利用像`datetime.timedelta()`这样的 Python 函数。这意味着您需要撤销刚刚对数据库中的`launch_date`字段所做的更改。

Django 再次为您提供了简单的命令，您可以使用这些命令快速撤销您的更改。要取消迁移，您需要再次迁移数据库，将您想要恢复的迁移的名称作为参数传入。

Django 会帮你计算迁移的次数。更改`launch_date`字段是执行的第二次迁移，它被命名为`0002_alter_rocket_launch_date`。要撤消这一更改，您需要恢复到第一次迁移，它被命名为`0001_initial`:

```
(django-tut) $ python3 manage.py migrate rockets 0001_initial
Operations to perform:
  Target specific migration: 0001_initial, from rockets
Running migrations:
  Rendering model states... DONE
  Unapplying rockets.0002_alter_rocket_launch_date... OK

```

输出证实 Django 已经逆转了第二次迁移。

快速浏览一下数据库模式也证实了这一点:

```
sqlite> .schema rockets_rocket
CREATE TABLE IF NOT EXISTS "rockets_rocket" ("id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, "launch_date" date NOT NULL, "name" varchar(200) NOT NULL, "ready_to_launch" bool NOT NULL);

```

`"launch_date"`列的类型再次被设置为`date`对象。

## 显示迁移

如果您想快速查看 Django 项目中应用了哪些迁移，您可以使用`showmigrations`命令一次查看所有迁移:

```
(django-tut) $ python3 manage.py showmigrations
admin
 [X] 0001_initial
 [X] 0002_logentry_remove_auto_add
 [X] 0003_logentry_add_action_flag_choices
auth
 [X] 0001_initial
 [X] 0002_alter_permission_name_max_length
 [X] 0003_alter_user_email_max_length
 [X] 0004_alter_user_username_opts
 [X] 0005_alter_user_last_login_null
 [X] 0006_require_contenttypes_0002
 [X] 0007_alter_validators_add_error_messages
 [X] 0008_alter_user_username_max_length
 [X] 0009_alter_user_last_name_max_length
 [X] 0010_alter_group_name_max_length
 [X] 0011_update_proxy_permissions
 [X] 0012_alter_user_first_name_max_length
contenttypes
 [X] 0001_initial
 [X] 0002_remove_content_type_name
rockets
 [X] 0001_initial
 [ ] 0002_alter_rocket_launch_date
sessions
 [X] 0001_initial

```

仔细看看`rockets`应用程序的迁移。即使您撤销了对`launch_date`字段的更改，Django 仍然保留了这次迁移的记录。事实上，您可以列出迁移目录的内容，并看到文件仍然在那里。

这是一个非常有用的特性，因为它允许您重新应用以前逆转的迁移。但是，Django 会在您下次迁移整个项目时自动重新应用该迁移，所以请记住这一点。

## Django 迁移中的错误故障排除

本节将介绍一些您在使用 Django 迁移时可能会遇到的错误。本节中的概念被认为是先进的，因为关键数据丢失的可能性很大。

在练习下面几节中提到的技术时，请记住使用您有备份的数据集，或者您不介意完全丢失其数据的数据集。

### 删除迁移文件

在上一节中，您看到 Django 保存了所有迁移的记录，甚至包括您没有应用的迁移。此外，您知道如果您再次尝试迁移数据库，Django 将自动重新应用该迁移！你如何阻止这种事情发生？

您可以尝试删除迁移文件，并从那里迁移数据库:

```
(django-tut) $ rm rockets/migrations/0002_alter_rocket_launch_date.py

```

这将删除要求更改`launch_date`字段以接受字符数据而不是日期对象的文件。

但是，如果您现在尝试迁移数据库，Django 会向您显示一条错误消息:

```
(django-tut) $ python3 manage.py migrate rockets
Operations to perform:
  Apply all migrations: rockets
Running migrations:
  No migrations to apply.
  Your models in app(s): 'rockets' have changes that are not yet reflected in a migration, and so won't be applied.
  Run 'manage.py makemigrations' to make new migrations, and then re-run 'manage.py migrate' to apply them.

```

该错误消息表明您的数据模型与数据库中当前的数据模型不匹配。当您删除迁移字段时，您删除了要求更改`launch_date`字段的指令。但是，您的 Django 模型本身仍然反映了这种变化。

要解决这个问题，您需要进入您的`models.py`文件并手动撤销更改:

```
from django.db import models

# Create your models here.
class Rocket(models.Model):
    """A model of a rocket ship."""
    name = models.CharField(max_length=200)
    launch_date = models.DateField() # Update this line
    ready_to_launch = models.BooleanField(default=True)

```

现在，当您迁移数据库时，Django 将确认表模式和您的模型是同步的:

```
(django-tut) $ python3 manage.py migrate rockets
Operations to perform:
  Apply all migrations: rockets
Running migrations:
  No migrations to apply.

```

看起来不错！

### 删除模型字段

当您处理生产级项目时，您可能不是团队中唯一的开发人员。这意味着其他开发人员将与数据库交互并进行异步更改。

说你的一个队友误解了需求，不小心把`launch_date`字段完全去掉了。(要复制这种行为，继续从您的数据模型中删除`launch_date`字段，并应用迁移。)被截断的`showmigrations`输出显示了发生的事情:

```
rockets
 [X] 0001_initial
 [X] 0002_remove_rocket_launch_date

```

没问题，对吧？让我们尝试恢复迁移，看看会发生什么:

```
(django-tut) $ python3 manage.py migrate rockets 0001_initial
Operations to perform:
  Target specific migration: 0001_initial, from rockets
Running migrations:
  Rendering model states... DONE
  Unapplying rockets.0002_remove_rocket_launch_date...Traceback (most recent call last):
  File "/django-migrations-tutorial/django-tut/lib/python3.8/site-packages/django/db/backends/utils.py", line 84, in _execute
    return self.cursor.execute(sql, params)
  File "/django-migrations-tutorial/django-tut/lib/python3.8/site-packages/django/db/backends/sqlite3/base.py", line 423, in execute
    return Database.Cursor.execute(self, query, params)
sqlite3.IntegrityError: NOT NULL constraint failed: new__rockets_rocket.launch_date
... # truncated for length

```

啊哦… Django 不会应用迁移，因为这样做会违反数据库的约束。

仔细看看表模式:

```
sqlite> .schema rockets_rocket
CREATE TABLE IF NOT EXISTS "rockets_rocket" ("id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, "launch_date" date NOT NULL, "name" varchar(200) NOT NULL, "ready_to_launch" bool NOT NULL);

```

您可以看到,`"launch_date"`字段上有一个名为`NOT NULL`的约束。这意味着数据库中的每一行都必须在该字段中有一个值，它不能为空。Django 模型默认带有这种约束。

不幸的是，当您的同事删除了`launch_date`字段时，Django 将这一更改应用到了数据库中，也从表中删除了该列:

```
sqlite> select * from rockets_rocket;
1|Apollo|0
2|Orion|1

```

您不能简单地撤消更改，因为您将恢复到一个要求`launch_date`列中有值的数据库模式。然而，该表中的现有行没有该字段的值！Django 无法协调冲突，因此迁移不会运行。

要解决这个问题，您需要在数据模型中显式地重新定义已删除的表列:

```
from django.db import models

# Create your models here.
class Rocket(models.Model):
    """A model of a rocket ship."""
    name = models.CharField(max_length=200)
    launch_date = models.DateField() # Make sure this line exists
    ready_to_launch = models.BooleanField(default=True)

```

当您尝试进行新的迁移时，应该会看到以下提示:

```
(django-tut) $ python3 manage.py makemigrations rockets
You are trying to add a non-nullable field 'launch_date' to rocket without a default; we can't do that (the database needs something to populate existing rows).
Please select a fix:
 1) Provide a one-off default now (will be set on all existing rows with a null value for this column)
 2) Quit, and let me add a default in models.py
Select an option:

```

这一次，Django 可以明确地问你想对不可空的字段`launch_date`做什么。选择提供一次性默认值的选项:

```
Select an option: 1
Please enter the default value now, as valid Python
The datetime and django.utils.timezone modules are available, so you can do e.g. timezone.now
Type 'exit' to exit this prompt
>>> timezone.now()
Migrations for 'rockets':
  rockets/migrations/0003_rocket_launch_date.py
    - Add field launch_date to rocket

```

`launch_date`字段接受`date`对象，因此您可以使用`timezone.now()`函数提供当前日期和时间作为默认值。

您可以检查新创建的迁移文件，以确认 Django 将在数据库中的现有行上使用该默认值:

```
# rockets/migrations/0003_rocket_launch_date.py
class Migration(migrations.Migration):
    # ...
    operations = [
        migrations.AddField(
            model_name='rocket',
            name='launch_date',
            field=models.DateField(default=datetime.datetime(2021, 12, 2, 4, 9, 37, 82124, tzinfo=utc)),
            preserve_default=False,
        ),
    ]

```

现在，当您迁移数据库时，Django 会将`launch_date`添加回表中，并用指定的默认值填充现有的行:

```
sqlite> select * from rockets_rocket;
1|Apollo|0|2021-12-02
2|Orion|1|2021-12-02

```

如果您不想提供默认值，那么您可以通过显式删除`NOT NULL`约束来缓解这个问题。这是通过向您在数据模型中定义的字段传递一个参数来实现的:

```
from django.db import models

# Create your models here.
class Rocket(models.Model):
    """A model of a rocket ship."""
    name = models.CharField(max_length=200)
    launch_date = models.DateField(null=True) # Allow null values
    ready_to_launch = models.BooleanField(default=True)

```

现在，如果删除并重新添加`launch_date`列，Django 将应用迁移，即使现有的表行不包含该字段的值。

## 结论

Django 的对象关系映射器是一个强大的工具，对全世界的 Python web 开发人员来说是一个福音。在本教程中，您看到了如何使用 Django 迁移来快速启动 Python 中的数据库表，而无需在您的终端编写任何 SQL 查询。

您还学习了一些排除错误的技术，例如意外删除迁移文件或从数据库表中删除字段。处理数据是一项棘手的工作，虽然 Django 可以帮助您纠正一些错误，但它不能恢复数据本身——所以一定要备份您的表！

如果你有任何问题或意见，请在下面的评论区分享。

感谢阅读！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)