# cURL 介绍:传输工具的基础

> 原文：<https://blog.logrocket.com/an-intro-to-curl-the-basics-of-the-transfer-tool/>

## 介绍

cURL 是一种传输工具，用于从服务器传输数据或向服务器传输数据。它支持各种互联网传输协议，包括:

*   词典
*   文件
*   FTPS FTP
*   囊地鼠
*   HTTP，HTTPS
*   IMAP，IMAPS
*   LDAP，LDAPS
*   POP3，POP3S
*   RTSP RTMP
*   SFTP SCP
*   SMBS 中小企业
*   SMTP，SMTPS
*   TELNET 和 TFTP

我们可以使用 cURL 执行一些有用的技巧，比如代理支持、用户认证、FTP 上传、HTTP post、SSL 连接、cookies、文件传输恢复、Metalink 等等。

**重要提示** : cURL 由 libcurl 提供所有与传输相关的特性。

cURL 是从命令行使用的。它通常用于在软件开发过程中快速测试 API。当我想在 Node.js 服务器中测试所有 API 时，我个人会使用 cURL。对于开发者来说，这是一个非常方便的工具。

> 邮递员很酷，但是 cURL 超级酷。Chidume Nnamdi

在本帖中，我们将介绍基本的 cURL 函数和选项。我们还将学习如何使用 cURL 在 API 端点上执行 GET、POST 请求。

## 卷曲选项

### `--request`或`-X`

`--request`和`-X`指定与 HTTP 服务器通信时可以使用的定制请求方法。将使用指定的请求方法，而不是其他使用的方法(默认为 GET)。

要执行 POST 请求，请执行以下操作:

```
curl --request POST
```

要执行 GET 请求，请执行以下操作:

```
curl --request GET
```

如果我们不通过`--request`指定请求，它将默认为 GET。

### `--url</code`

这指定了我们将获取或传输数据的 URL。

从 cURL 文档中:

指定要提取的 URL。当您想要在配置文件中指定 URL 时，此选项非常方便。

如果给定的 URL 缺少一个方案名(如“[http://&# 8221](http://&#8221))；或者“[FTP://&# 8221](ftp://&#8221)；等等)然后 cURL 会根据主机进行猜测。

如果最外面的子域名与 DICT、FTP、IMAP、LDAP、POP3 或 SMTP 匹配，那么将使用该协议。否则，将使用 HTTP。

例如，如果您想在 localhost:3000 的本地服务器上执行一个 GET 请求，那么您需要将`--url`设置为`localhost:3000`:

```
curl --request GET \
    --url http://localhost:3000
```

要在相同的 URL 上执行发布:

```
curl --request POST \
    --url http://localhost:3000
```

**注意**:反斜杠`\`用于在 cURL 中分隔选项。

对于一个外部 API 来说，还是一样的。

假设您想从[https://moviesdb.com/movies/all](https://moviesdb.com/movies/all)获取一个电影列表。

```
curl --request GET \
    --url https://moviesdb.com/movies/all
```

将获取并打印`moviesdb`中所有电影的列表。

## `--header`或`-H`

此选项用于设置请求的标题。

从 cURL 文档中:

> 向服务器发送 HTTP 时包含在请求中的额外标头。您可以指定任意数量的额外标头。请注意，如果您添加了一个与 curl 使用的内部头文件同名的自定义头文件，那么将使用外部设置的
> 头文件，而不是内部头文件。

这反映了我们在普通编程中的做法，尤其是在使用`XMLHttpRequest`的 JavaScript 中:

```
const xhttp = new XMLHttpRequest()

xhttp.setHeader("Content-Type", "application/json")
```

标头用于向 web 服务器传达传入的数据类型或预期的数据类型。发送的数据类型应该与标头中指定的类型相同。

我们可以对 CORS 权限使用头，甚至对某些类型的请求方法使用头。我们可以用标题做很多事情。

所以在 cURL 中，我们可以使用`--header`选项来设置头:

```
curl --request POST \
  --url http://localhost:5000/api/user \
  --header 'content-type: application/json'
```

这里，我们向[http://localhost:5000/API/user](http://localhost:5000/api/user)端点发出一个 POST 请求，并通过`--header` `content-type: application/json'`告诉服务器我们发送的数据是 JSON 数据类型。

## `--data`或`-d`

此选项用于向 HTTP 服务器发送数据。这主要用于 POST 请求，因为我们将数据发送到我们想要添加到数据存储的服务器。所以在 cURL 中，我们可以通过设置`--data`选项来指定要发布的数据。

来自 cURL 手册:

> 将 POST 请求中的指定数据发送到 HTTP 服务器，就像用户填写 HTML 表单并按下 submit 按钮时浏览器所做的一样。

这里有一个例子:

```
curl --request POST \
  --url http://localhost:5000 \
  --header 'content-type: application/json' \
  --data '{"name":"Arrow","description":"bad movie","rating":"7.0","image":"michaeljackson.png"}'
```

在这里，我们对 [http://localhost:5000](http://localhost:5000) 端点执行一个 POST 请求。

我们在`--data`选项中设置要发送给服务器的数据，也就是:`'{"name":"Arrow","description":"bad movie","rating":"7.0","image":"michaeljackson.png"}`。

这是一个我们想要添加到 [http://localhost:5000](http://localhost:5000) 数据存储中的电影对象。

## 在 Node.js/Express.js 中使用 cURL

让我们看看如何设置 Node.js/Express.js 服务器并使用 cURL 测试端点:

```
// server.js

/** require dependencies */
const express = require("express")
const cors = require('cors')
const bodyParser = require('body-parser')
const helmet = require('helmet')

const app = express()

let port = 5000 || process.env.PORT

/** set up middlewares */
app.use(cors())
app.use(bodyParser.json({limit: '50mb'}))
app.use(helmet())

let users = []
let currId  Date.now()

app.get("api/user/:id", (req, res, next) => {
        const userId = req.params.id
        const users = users.filter(user => user.id === userId)
        if(users.length > 0)
            res.send({ user: users[0] })
        else
            res.send({mesg: "No user found."})
        next()
})

app.get("api/users", (req, res, next) => {
    res.send({ users })
    next()
})

app.post("api/user", (req, res, next) => {
        let bdy = req.body
        bdy = { id: ++currId, ...bdy }
        users.push(bdy)
        res.send({ user: bdy })
        next()    
})

/** start server */
app.listen(port, () => {
    console.log(`Server started at port: ${port}`);
});
```

我们这里有小的 API 端点:

*   `GEt api/users`
*   ``GET api/user/:id``
`*   ``POST api/user```

 ``它获取所有用户，通过其`id`获取特定用户，并可以添加新用户。

启动服务器:

```
node server.js

Server started at port: 5000
```

现在，为了在`api/user`执行 POST 请求，我们将把`--url`设置为`[http://localhost:5000/api/user](http://localhost:5000/api/user)`，把`--request`设置为 POST，把`--header`设置为`content-type: application/json`。

我们想添加用户:

```
name: "Chidume",
age: 28
```

所以数据会是`'{"name": "Chidume", "age": "28"}'`。

因此 cURL 命令将是:

```
curl --request POST \
  --url http://localhost:5000/api/user \
  --header 'content-type: application/json' \
  --data '{"name": "Chidume", "age": "28"}'

{ id: 5122435464, name: "Chidume", age: 28 }
```

我们看到来自服务器的结果:用户及其`id`被添加到数据存储中。

让我们添加另一个用户:

```
curl --request POST \
  --url http://localhost:5000/api/user \
  --header 'content-type: application/json' \
  --data '{"name": "Nnamdi", "age": "21"}'

{ id: 5122435465, name: "Nnamdi", age: 21 }
```

我们添加了另一个用户“Nnamdi”，我们可以看到来自服务器的结果及其`id`。

现在，让我们通过`api/user/:id`查询“Chidume”。在这里，`:id`将是`id` 5122435464。所以旋度会是:

```
curl --request GET \
  --url http://localhost:5000/api/user/5122435464

{ user: { id: 5122435464, name: "Chidume", age: 28 } }
```

来自服务器的结果向用户返回`id` 5122435464，即“Chidume”。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

要查询“Nnamdi”:

```
curl --request GET \
  --url http://localhost:5000/api/user/5122435465

{ user: { id: 5122435465, name: "Nnamdi", age: 21 } }
```

哒哒！！

现在让我们查询一个不存在的用户:

```
curl --request GET \
  --url http://localhost:5000/api/user/2000000000

{ mesg: "No user found." }
```

这是当我们点击一个不存在的用户时，服务器上返回的结果集。

现在，让我们查询所有用户:

```
curl --request GET \
  --url http://localhost:5000/api/users

{ users: [ { id: 5122435465, name: "Nnamdi", age: 21 }, { id: 5122435464, name: "Chidume", age: 28 } ] }
```

如您所见，返回了服务器中的所有用户。

## 结论

在本教程中，我们将不会讨论 cURL 的基本选项，并举例说明如何使用每一个选项，然后我们开发了一个具有三个端点的 Node/Express 服务器，以演示我们如何使用这里提到的 cURL 选项通过 cURL 向服务器执行 GET/POST 请求。

使用 cURL 有许多选项。我们在这里讨论的是可以用来快速测试 API 端点的基本选项。

如果你对此有任何问题，或者我应该添加、更正或删除的任何内容，请随时评论、发电子邮件或给我发短信。``