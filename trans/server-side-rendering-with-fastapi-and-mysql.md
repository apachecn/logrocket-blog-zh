# 使用 FastAPI 和 MySQL - LogRocket 博客进行服务器端渲染

> 原文：<https://blog.logrocket.com/server-side-rendering-with-fastapi-and-mysql/>

FastAPI 是一个现代的高性能 web 框架，用于构建 API，这项任务通常需要使用前端工具来处理客户端。FastAPI 基于 Pydantic 和 Starlette，包含 Python ≥ v3.6.0 的服务器端渲染特性和类型提示，同时支持客户端和服务器端。此外，FastAPI 还包括以下特性:

*   速度堪比 Node.js 和 Go
*   降低人为错误的可能性
*   对代码编辑器的出色支持
*   消除代码重复
*   基于标准的

与 Flask 等其他 Python 框架不同，FastAPI 与 SQLAlchemy 集成，后者支持与 MySQL、PostgreSQL、SQLite、Oracle 和 Microsoft SQL Server 的数据库集成。

在本教程中，我们将通过构建一个简单的添加和删除电影标题的数据库来探索使用 FastAPI 的服务器端渲染。您可以通过克隆这个项目的 [GitHub 库](https://github.com/icode247/Serverside-rendering-FastAPI)来跟进。我们开始吧！

## 设置我们的项目

让我们从设置应用程序开始。我们的项目使用以下结构:

```
 ┣ static
 ┃ ┣ css
 ┃ ┃ ┣ overview.css
 ┃ ┃ ┗ style.css
 ┃ ┗ js
 ┃ ┃ ┗ script.js
 ┣ templates
 ┃ ┣ index.html
 ┃ ┗ overview.html
 ┣ .gitignore
 ┣ database.py
 ┣ main.py
 ┣ model.py
 ┣ requirements.txt
 ┗ schema.py

```

在`static`目录中，我们将存储静态文件。`templates`是我们 HTML 页面的目录，`database.py`是我们数据库连接的文件。`model.py`文件用于我们的数据库模型，`schema.py`文件用于我们的数据库模式。

### 设置虚拟环境

为您的 Python 项目创建隔离的 Python 环境是一个很好的实践。要确保您已经安装了`virtualenv`，请运行以下命令:

```
pip install virtualenv

```

现在，创建一个名为`server-side-rendering-with-fastapi`的新目录。导航到它并使用下面的命令创建一个虚拟环境:

```
python3 -m venv env

```

要激活我们刚刚创建的虚拟环境，请运行以下命令:

```
source env/bin/activate

```

### 安装依赖项

现在，让我们为我们的项目安装必要的包。我们将使用[uvicon](https://www.uvicorn.org/)作为我们的 ASGI 开发服务器， [Jinja2](https://jinja.palletsprojects.com/en/3.0.x/) 作为我们的模板引擎，[python-multi](https://pypi.org/project/python-multipart/)part 从客户端接收表单字段:

```
pip install fastapi uvicorn jinja2 python-multipart

```

## 创建 fastagi 服务器

随着项目的建立，我们可以创建我们的 FastAPI 服务器。在项目的根目录中创建一个`main.py`文件，并向其中添加以下代码:

```
from fastapi import FastAPI
app = FastAPI()

@app.get("/")
def read_root():
    return {"message": "welcome to FastAPI!"}

```

然后，使用以下命令运行服务器:

```
uvicorn main:app --reload

```

`--reload`标志告诉 Uvicorn 每当有新代码添加到应用程序中时就重新加载服务器。接下来，打开您的浏览器并导航到`[http://127.0.0.1:8000](http://127.0.0.1:8000)`，在这里您将看到一个类似于下面消息的 JSON 响应:

```
{"message": "welcome to FastAPI!"}

```

## 设置 SQLAlchemy

现在我们已经设置了 FastAPI 服务器，让我们开始使用 [SQLAlchemy ORM](https://www.sqlalchemy.org) (对象关系映射器)并创建一个数据库。让我们安装 SQLAlchemy 和 [MySQL 连接器/Python](https://dev.mysql.com/doc/connector-python/en/) :

```
pip install sqlalchemy mysql-connector-python

```

在您的终端中，运行以下命令在 MySQL 数据库中创建一个数据库:

```
//Login to MySQL
mysql -u root -p

//Create database named serversiderendering
CREATE DATABASE serversiderendering;

```

在项目的根目录下，创建一个`database.py`文件。我们将导入 SQLAlchemy `create_engine`、`declarative_base`和`sessionmaker`。我们使用 MySQLConnector 连接到我们的数据库，因此我们的连接字符串将类似于以下代码:

```
>DATABASE_URL = "mysql+mysqlconnector://[email protected]:3306/serversiderendering"

```

我们可以使用刚刚从 SQLAlchemy 导入的`create_engine`函数连接到我们的数据库。我们还将导入`sessionmaker`函数，该函数创建一个会话来消除应用程序中的安全问题。

然而，在从`sessionmaker`创建一个`Sessionlocal`类实例之前，不会创建会话。我们将禁用`autocommit`和`autoflush`，然后将数据库引擎绑定到会话。

我们将用来创建应用程序的数据库模型的`declarative_base`类也是我们的数据库连接所需要的。将下面的代码添加到`database.py`:

```
from sqlalchemy import create_engine
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker
DATABASE_URL = "mysql+mysqlconnector://[email protected]:3306/serversiderendering"
engine = create_engine(DATABASE_URL)
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)
Base = declarative_base()
```

## 创建数据库模型

现在让我们构建一个 SQLAlchemy 数据库模型，因为我们的应用程序已经连接到了 MySQL 数据库。首先，在项目的根目录下创建一个`model.py`文件。在 SQLAlchemy 中，数据库的每个属性都由一个`Column`表示。我们将导入`Column`并传递一个 SQLAlchemy 类类型，如`Integer`、`String`、`Text`或`Boolean`作为在数据库中定义类型的参数。

为了创建 SQLAlchemy 模型，我们将从我们的`database.py`文件中导入并使用`Base`类。然后，将`__tablename__`属性添加到`Movie`类中，告诉 SQLAlchemy 在数据库中为我们的模型使用什么名称。

为了接收唯一的数据，我们将参数`unique`添加到名称字段，将 ID 设为`primary_key`，将`index`设为 it。将下面的代码添加到`model.py`:

```
from sqlalchemy.schema import Column
from sqlalchemy.types import String, Integer, Text
from database import Base
class Movie(Base):
    __tablename__ = "Movie"
    id = Column(Integer, primary_key=True, index=True)
    name = Column(String(20), unique=True)
    desc = Column(Text())
    type = Column(String(20))
    url = Column(String(100))
    rating = Column(Integer)

```

## 创建数据库模式

现在，我们的数据库模型已经配置好了，但是我们仍然需要为我们的模型创建一个模式，它将读取数据并从 API 返回数据。为此，我们将为我们的模型创建一个 [Pydantic](https://pydantic-docs.helpmanual.io/) 模式。

首先，我们将定义我们的模型验证，确保来自客户端的数据与我们定义的字段具有相同的数据类型。接下来，Pydantic 的`orm_mode`将指示 Pydantic 模型以字典和属性的形式读取数据。

在项目的根目录下创建一个`schema.py`文件，并将下面的代码粘贴到其中:

```
from datetime import date
from pydantic import BaseModel
class Movie(BaseModel):
    id = int
    name = str
    desc = str
    type = str
    url = str
    rating = str

    class Config:
        orm_mode = True

```

现在，让我们回到我们的`main.py`文件，导入数据库、模式、`SessionLocal`变量、数据库引擎和模型:

```
import schema
from database import SessionLocal, engine
import model

```

然后，我们将通过调用`model.Base.metadata.create_all()`函数创建我们的表，并将我们的数据库引擎绑定到它:

```
model.Base.metadata.create_all(bind=engine)

```

最后，我们将在`main.py`中创建一个`get_database_session()`函数，它将创建和关闭我们所有路由中的会话:

```
def get_database_session():
    try:
        db = SessionLocal()
        yield db
    finally:
        db.close()

```

## 创建我们的模板

现在，我们将创建 HTML 页面并将它们呈现给客户端。首先，我们将在项目的根目录下创建一个`template`文件夹来存储我们的模板。

为了呈现页面，我们需要向服务器添加一些配置。在`main.py`中，我们将从 FastAPI 导入`Jinja2Templates`来配置我们的模板，导入`StaticFiles`来配置我们的静态文件:

```
from fastapi.staticfiles import StaticFiles
from fastapi.templating import Jinja2Templates

```

我们将使用 FastAPI `mount`方法将静态文件添加到我们的应用程序中，这需要静态文件的文件路径、目录和名称:

```
app.mount("/static", StaticFiles(directory="static"), name="static")
templates = Jinja2Templates(directory="templates")

```

然后，在项目的根目录中创建一个`static/css/style.css`文件，并向其中添加以下自定义样式:

```
body{
    background-color:rgb(236, 234, 232);
}
img {
   width: 100%;
   border-radius: 10px;
}
.image{
    width: 30%;
}
.details{
    width: 70%;
    margin: 12px
}
.card {
    border-radius: 20px;
}

```

您还可以将 JavaScript 图像文件夹和文件保存在静态文件夹中，以便呈现给客户端。最后，让我们在模板目录中创建`index.html`和`overview.html`文件。将您的 HTML 文档添加到这些文件中。

## 渲染我们的模板

现在我们已经创建并配置了模板，让我们将它们呈现给客户端。首先，我们必须为索引和概览页面创建一个路径。在`main.py`中，我们将从 FastAPI 导入`Depends`和`Request`，从 SQLAlchemy 导入`Session`，从 FastAPI 响应导入`HTMLResponse`:

```
from fastapi import FastAPI, Depends, Request
from sqlalchemy.orm import Session
from fastapi.responses import HTMLResponse

```

您必须在您的请求处理程序函数中，将`request`作为 Jinja2 上下文中的键值对的一部分进行传递，同时传递的还有数据库会话，这将依赖于我们创建的用于管理跨路由会话的`get_database_session()`函数。然后，我们将查询我们的数据库来检索我们的电影，并用我们的`index.html`模板渲染它们。

overview route 接受一个请求参数，该参数用于使用`filter`方法查询数据库中的特定数据。然后，它返回查询项的第一个匹配项。你可以在 [FastAPI 文档](https://fastapi.tiangolo.com/tutorial/sql-databases/)中了解更多关于数据库查询的信息。

当向这些端点发出请求时，我们用电影对象向客户机返回一个`TemplateResponse`。用下面的代码更新您的`main.py`文件:

```
@app.get("/movie", response_class=HTMLResponse)
async def read_movies(request: Request, db: Session = Depends(get_database_session)):
    records = db.query(Movie).all()
    return templates.TemplateResponse("index.html", {"request": request, "data": records})

@app.get("/movie/{name}", response_class=HTMLResponse)
def read_movie(request: Request, name: schema.Movie.name, db: Session = Depends(get_database_session)):
    item = db.query(Movie).filter(Movie.id==name).first()
    print(item)
    return templates.TemplateResponse("overview.html", {"request": request, "movie": item})

```

在我们的`index.html`文件中，我们将使用 Jinga2 加载静态文件并显示数据。我们使用`url_for`加载带有 Jinja 标签的静态文件，传递静态文件名和文件路径:

```
<link href="{{ url_for('static', path='/style.css') }}" rel="stylesheet">

```

然后，我们将遍历我们的电影对象，并在我们的 HTML 页面上显示它们。目前，我们尚未添加任何电影:

```
  <div class="col-md-4 mb-2">
    <div class="card">
      <div class="card-body d-flex flex-row justify-content-between align-items-center">
         <div class="image mb-2">
              <img src="{{movie.url}}" alt="">
         </div&gt;
         <div class="details">
            <a href="/movie/{{movie.id}}"> <h4>{{movie.name}}</h4></a>
            <div class="footer d-flex flex-row justify-content-between align-items-center">
               <h6>{{movie.type}}</h6>
               <h6>{{movie.rating}}</h6>        
           </div>
        </div>
      </div>
    </div>
  </div&gt;

```

## 创建电影形式

现在，我们已经成功地在客户端呈现了模板，让我们创建一个 HTML 表单，以便将电影保存到数据库中。首先，我们需要在`index.html`中创建一个 HTML 表单:

```
<form action="/movie/" method="POST" enctype="application/x-www-form-urlencoded">
    <div class="row">
        <div class="col-md-6">
            <label for="">Movie Name:</label>
            <input type="text" class="form-control" id="email" name="name" />
        </div>
        <div class="col-md-6">
            <label for="">Image URL:</label>
            <input type="text" class="form-control" name="url" />
        </div>
        <div class="col-md-6">
            <label for="">Type:</label>
            <select name="type" id="" class="form-control">
                <option value=""></option>
                <option value="movie">movie</option>
                <option value="series">series</option>
            </select>
        </div>
        <div class="col-md-6">
            <label for="">Rating:</label>
            <input type="number" class="form-control" name="rate" min="18" />
        </div>
        <div class="col-md-12">
            <label for="">Description:</label>
            <textarea name="desc" rows="5" class="form-control"></textarea>
        </div>
        <div class="col-md-6">
            <button type="submit" class="btn btn-info mt-4">Save</button>
        </div>
    </div>
</form>

```

当向 FastAPI 发送数据时，总是用`application/x-www-form-urlencoded`对 HTML 表单进行编码。

在我们的应用程序中使用这个表单之前，我们需要将 FastAPI 中的`Form`和 [S](https://www.starlette.io) 中的`RedirectResponse`导入到我们的`main.py`文件中:

```
from fastapi import Depends, FastAPI, Request, Form
from starlette.responses import RedirectResponse

```

接下来，我们将创建一个请求处理程序，配置我们的表单，并用我们的数据库模式验证它。然后，我们将创建电影模型的实例，将数据从用户传递到模型。最后，我们将使用`db.add`和`db.commit`方法向数据库添加和保存记录。

我们将使用 FastAPI `RedirectResponse`函数将用户重定向回应用程序的根路径，该函数接受一个 URL 和一个状态代码作为参数:

```
@app.post("/movie/")
async def create_movie(db: Session = Depends(get_database_session), name: schema.Movie.name = Form(...), url: schema.Movie.url = Form(...), rate: schema.Movie.rating = Form(...), type: schema.Movie.type = Form(...), desc: schema.Movie.desc = Form(...)):
    movie = Movie(name=name, url=url, rating=rate, type=type, desc=desc)
    db.add(movie)
    db.commit()
    response = RedirectResponse('/', status_code=303)
    return response

```

将用户从 POST 路由重定向到 GET 路由时，请始终包含 303 状态代码。

## 更新电影

我们需要创建一个路由，使用户能够更新电影。HTML 表单只支持`GET`和`POST`请求，所以我们将配置我们的更新路由来接受 JSON 数据。首先，我们需要将`JSONResponse`从`fastapi.responses`导入到`main.py`:

```
from starlette.responses import `JSONResponse

```

接下来，我们将创建一个接受电影 ID 作为参数的`patch`路由。然后，我们从`request.json()`方法中获取用户的输入，并在数据库中搜索具有特定 ID 的电影。

我们可以更新电影的名称和描述，刷新数据库，将电影对象转换为 JSON，并将其作为响应返回给客户端:

```
@app.patch("/movie/{id}")
async def update_movie(request: Request, id: int, db: Session = Depends(get_database_session)):
    requestBody = await request.json()
    movie = db.query(Movie).get(id)
    movie.name = requestBody['name']
    movie.desc = requestBody['desc']
    db.commit()
    db.refresh(movie)
    newMovie = jsonable_encoder(movie)
    return JSONResponse(status_code=200, content={
        "status_code": 200,
        "message": "success",
        "movie": newMovie
    })

```

现在打开`overview.html`文件并添加更新表单:

```
&lt;form method="POST" id="updateForm">
  <div class="row">
    <div class="col-md-12">
      <label for="">Movie Name:</label>
      <input type="text" class="form-control" id="name" />
    </div>
    <div class="col-md-12">
      <label for="">Description:</label>
      <textarea id="desc" rows="5" class="form-control"></textarea>
    </div>
    <input type="hidden" id="id" value="{{movie.id}}" />
    <div class="col-md-6">
      <button type="submit" class="btn btn-info mt-4">Update</button>
    </div>
  </div>
</form>

```

接下来，我们将使用 [JavaScript Fetch API 向服务器发送一个`Patch`请求](https://blog.logrocket.com/axios-or-fetch-api/)来更新电影。将下面的代码添加到`script.js`:

```
form = document.getElementById("updateForm");
function updateMovie(id, name, desc) {
  fetch("/movie/" + id, {
    method: "PATCH",
    body: JSON.stringify({
      name,
      desc,
    }),
  }).then((response) => response.json());
  window.location.reload();
}
form.addEventListener("submit", (e) => {
  e.preventDefault();
  const name = document.getElementById("name").value;
  const des = document.getElementById("desc").value;
  const id = document.getElementById("id").value;
  updateMovie(id, name, des);
});

```

## 删除电影

我们还需要从数据库中删除电影的选项。为此，我们需要创建一个删除路径，该路径接受电影 ID 作为我们的`main.py`文件夹中的参数。我们将在数据库中搜索具有该特定 ID 的电影，然后使用`db.delete()`方法删除它。当电影被成功删除后，我们将向客户端发送一个`null`值:

```
@app.delete("/movie/{id}")
async def delete_movie(request: Request, id: int, db: Session = Depends(get_database_session)):
    movie = db.query(Movie).get(id)
    db.delete(movie)
    db.commit()
    return JSONResponse(status_code=200, content={
        "status_code": 200,
        "message": "success",
        "movie": None
    })

```

接下来，在`scripts.js`中，我们将使用 JavaScript Fetch API 向服务器发送一个`Patch`请求来更新电影:

```
async function deleteMovie(id) {
  const res = await fetch("/movie/" + id, {
    method: "DELETE",
  }).then((response) => response.json());
  console.log(res);
}

```

## 结论

在本教程中，我们通过构建一个简单的电影数据库应用程序来探索 FastAPI。FastAPI 是创建 REST APIs 的一个伟大的现代框架。借助 FastAPI 的服务器端渲染特性，您可以构建和管理小规模应用程序，如网站和博客，而无需使用任何前端框架。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)