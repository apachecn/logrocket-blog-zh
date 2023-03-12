# 用 Flask、Connexion 和 SQLAlchemy 创建 Python REST APIs

> 原文：<https://blog.logrocket.com/creating-python-rest-apis-flask-connexion-sqlalchemy/>

没有文档的 API 有多大用处？

我可以说它并不真正有用，因为如果没有适当的文档，你的同事与你合作可能会很痛苦。

但是大多数开发人员不喜欢写文档，因为这不会妨碍他们的代码工作。然而，这是开发 API 时最重要的最佳实践之一。

好消息是，如果你决定用 [Flask](https://flask.palletsprojects.com/en/2.0.x/) 来构建你的 API，你可以利用[connection](https://connexion.readthedocs.io/en/latest/)的力量来开发你的 API，同时编写文档，因为它迫使你使用 [OpenAPI 规范](https://swagger.io/specification/)来编写你的文档，然后将你的端点映射到你的 Python 函数。

## 什么是连接？

Connexion 允许您详细描述您的 REST API，并且您几乎可以保证它将按照您指定的方式工作。

要指定 Flask API 路径，您必须使用如下所示的`@app.route`装饰器:

```
@app.route("/")
def hello_world():
    return "<p>Hello, World!</p>"

```

但是使用 Connexion，您将编写不带装饰器的普通函数，然后使用`operationId`将您的函数直接映射到一个`yaml`文件中的文档:

```
paths:
  /hello_world:
    post:
      operationId: hello_world

```

所以，让我们开始吧。

## 先决条件

## 安装烧瓶

设置 Flask 应用程序非常简单。我们需要安装所需的依赖项，并对它们进行适当的设置，以便使用 Flask。

让我们使用 pip 安装它们。

`pip install Flask marshmallow-sqlalchemy marshmallow flask-marshmallow` `"connexion[swagger-ui]"`

在本教程中，我们将构建一个基本的装运 API，它允许我们创建装运、查看可用的装运和更新装运。

### 在 Flask 中构建数据库模型

让我们为我们的 API 创建模型。我们将使用 [Flask-SQLAlchemy](https://flask-sqlalchemy.palletsprojects.com/en/2.x/) 来创建和管理我们的数据库模型。

我们的`model.py`会是这样的:

```
from flask_sqlalchemy import SQLAlchemy
db = SQLAlchemy()

class Shipment(db.Model):
    """
    Shipment Model
    """
    id = db.Column(db.Integer, primary_key=True)
    destination = db.Column(db.String(80))
    source = db.Column(db.String(120))
    current_location = db.Column(db.String(120))
    status = db.Column(db.String(120))
    item = db.Column(db.String(120))
    description = db.Column(db.String(120))
    tracking_number = db.Column(db.String(120), nullable=True)
    arrival = db.Column(db.String(120))
    def __repr__(self):
        return '<Shipment %r>' % self.item

    def __init__(self, description, source, current_location, status, item, tracking_number, arrival, destination):

        self.description =  description
        self.destination =  destination
        self.source = source
        self.current_location = current_location
        self.status = status
        self.item = item
        self.tracking_number = tracking_number
        self.arrival = arrival

```

接下来，我们将使用 [Marshmallow](https://marshmallow.readthedocs.io/en/stable/) 创建数据库模式，这是一个 Python ORM，它简化了复杂数据类型(如对象)与 Python 原生数据类型之间的转换过程。

以下是`schema.py`的代码:

```
from flask_marshmallow import Marshmallow
ma = Marshmallow()
class ShipmentSchema(ma.Schema):
    """
    Schema
    """
    class Meta:
        fields = (
        'id', 
        'item', 
        'description', 
        'status', 
        'tracking_number',
        'current_location',
        'source',
        'destination',
        'description',
        'arrival'
        )

```

数据库模型字段应该与我们在模式中拥有的相匹配。

接下来，设置一个配置文件，我们将在其中设置基本的应用程序需求。我们将插入一个 [SQLite](https://www.sqlite.org/index.html) 数据库，并建立与 Flask 的连接。

下面是`config.py`的样子:

```
import connexion
import os

basedir = os.path.abspath(os.path.dirname(__file__))
application = connexion.FlaskApp(__name__)
application.add_API("swagger.yaml")
app = application.app
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:////' + os.path.join(basedir, 'db.sqlite')

```

通常，当你建立一个 Flask 应用程序时，你将初始化 Flask，就像这样:

```
from flask import Flask
app = Flask(__name__)

```

然而，因为我们将使用 Connexion，它将扩展 Flask 的特性，所以我们将以不同的方式来做这件事。让我们使用连接来初始化 Flask:

```
application = connexion.FlaskApp(__name__)

```

接下来，我们将添加 API 文档`yaml`文件，它将包含我们的应用程序的整个路径。

```
application.add_API("swagger.yaml")

```

如果您的`yaml`文件在不同的目录中，您可以通过将`specification_dir='directory_name/'`添加到 Flask 连接实例来指定确切的目录:

```
application = connexion.FlaskApp(__name__, specification_dir='directory_name/')

```

为了直接访问 Flask 应用程序，我们将从 Connexion Flask 实例中引用它:

```
app = application.app

```

注意，如果我们不使用 Connexion，我们将直接使用`application`而不是`application.app`。

最后，我们将添加数据库配置并设置数据库文件的路径，因为我们使用 SQLite 作为数据库。

```
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:////' + os.path.join(basedir, 'db.sqlite')

```

我们现在已经与 Flask 连接并运行了！

现在让我们创建表格。打开您的 shell 并运行以下代码来创建我们的模型定义中指定的所有表:

```
from app import db
db.create_all() 

```

接下来，我们将设置主应用程序入口点。

`main.py`

```
from app_config import app
from model import db
if __name__ == '__main__':
    db.init_app(app)
    app.run(debug=True)

```

在这里，我们将导入应用程序，初始化数据库，并运行它。应用程序现在已经设置好了，所以我们只需要添加功能和路线。

## 在`.yaml`文件中创建路线

让我们从在文档中创建路线开始。首先在 app 目录中创建一个`swagger.yaml`文件，然后添加以下 OpenAPI 规范:

`swagger.yaml`

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
openAPI: 3.0.0
servers:
  - description: Shipment Sample App
    url: http://localhost:5000/
info:
  description: This is a simple API
  version: "1.0.0"
  title: Simple shipment API
  contact:
    email: [email protected]
  license:
    name: Apache 2.0
    url: 'http://www.apache.org/licenses/LICENSE-2.0.html'
paths:
  /shipment/list:
    get:
      tags:
        - shipment
      summary: searches shipment
      operationId: view.get
      description: |
        By passing in the appropriate options, you can search for
        available shipment in the system
      responses:
        '200':
          description: search results matching criteria
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/shipmentItem'
        '400':
          description: bad input parameter

  /shipment/create:
    post:
      tags:
        - shipment
      summary: adds a shipment item
      operationId: view.post
      description: Adds an item to the system
      responses:
        '201':
          description: item created
        '400':
          description: 'invalid input, object invalid'
        '409':
          description: an existing item already exists
      requestBody:
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/shipmentItem'
        description: shipment item to add

  /shipment/update/{id}:
    put:
      operationId: view.put
      tags:
        - shipment
      summary: Update a Shipment in the people list
      description: Update a Shipment in the Shipments list
      parameters:
        - name: id
          in: path
          description: Shipment ID
          required: true
          schema:
            type: integer
            format: int64

      responses:
        200:
          description: Successfully updated a shipment in Shipments list
components:
  schemas:
    shipmentItem:
      type: object
      properties:
        item:
          type: string
        description:
          type: string
        status:
          type: string
        tracking_number:
          type: string
        current_location: 
          type: string
        source:
          type: string
        destination":
          type: string
        arrival:
          type: string 

```

让我们来看看我们将与函数匹配的路径。这些函数将与`operationId`匹配，如下面的示例代码所示。

`view`是包含所有 API 函数的文件。get 函数是这个特定路由的目标，所以我们使用`view.get`。

```
/shipment/list:
    get:
      tags:
        - shipment
      summary: searches shipment
      operationId: view.get

```

我们必须将我们引用的`get`函数放在`view.py`文件中。这个函数将搜索数据库以获得所有的发货。

下面是`view.py`代码的样子:

```
def get(id=None):
    """
    get Shipment
    """
    try:
        if id is None:
            shipment = Shipment.query.filter().all()
            shipment_schema =  ShipmentSchema(many=True)
            return shipment_schema.jsonify(shipment)
        else:
            shipment = Shipment.query.filter_by(id=id).first()
            shipment_schema = ShipmentSchema()
            return shipment_schema.jsonify(shipment)
    except Exception as e:
        jsonify({"error":"There was an error please contact the administrator"})

```

我们将对其他函数和端点做同样的事情。你可以在 GitHub 上克隆[这个库来查看其他功能的完整代码。基本都是一样的模式。](https://github.com/ezesundayeze/simple-shipment-flask)

为了更新货物，我们需要在 URL 参数中传递一个 ID。那么我们该怎么做呢？

让我们以 put 请求的函数为例:

```
def put(id): #note that the name of this function can be anything, like update_shipment 
    """
    Update shipment
    """
    try:

        data = request.get_json()
        shipment = Shipment.query.filter_by(id=id).first()
        shipment = Shipment.query.filter_by(id=id)
        shipment.update(data)
        db.session.commit()

        return jsonify(data)
    except Exception as e:
        jsonify({"error":"There was an error please contact the administrator"})# Routes

```

文档将如下所示。请注意，我们在 route 和 parameters 对象中都传递了`{id}`？

```
 /shipment/update/{id}:
    put:
      operationId: view.put
      tags:
        - shipment
      summary: Update a Shipment in the Shipments list
      description: Update a Shipment in the Shipments list
      parameters:
        - name: id
          in: path
          description: Shipment ID
          required: true
          schema:
            type: integer
            format: int64

      responses:
        200:
          description: Successfully updated a shipment in Shipments list

```

您可以看到 route 需要一个参数，并且该参数在 parameters 对象中被定义为一个`ID`。

您现在可以使用`python main.py`运行您的应用程序。

一旦完成，维奥拉！您的 API 已经准备好了，包括完整的文档。默认情况下，API 文档将在路径`/ui`中可用。

以下是我们到目前为止所创建内容的截图:

![Simple Shipment API](img/e9741cc15d972beb87fd9dcc21582942.png)

您甚至可以直接从 API 文档中与 API 进行交互。我还在这里部署了 API [进行测试。你可以随意摆弄它。](https://flask-connexion.herokuapp.com/ui)

你可以在 GitHub 上查看完整的源代码。

## 结论

将 API 和文档放在一起构建并不容易，但是 Connexion 使这成为可能。它也很容易集成到 Flask 中，而不必学习一个全新的框架。黑客快乐！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)