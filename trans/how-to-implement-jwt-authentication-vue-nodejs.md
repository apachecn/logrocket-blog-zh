# 如何用 Vue 和 Node.js 实现 JWT 认证- LogRocket Blog

> 原文：<https://blog.logrocket.com/how-to-implement-jwt-authentication-vue-nodejs/>

***编者按**:这篇文章最后一次更新是在 2022 年 3 月 23 日，目的是纠正示例代码中的错误，并包含最新的包。*

JWT 是 JSON Web Token 的首字母缩写，是一种开放标准，允许开发人员通过签名来验证一种称为声明的信息的真实性。签名可以是秘密的，也可以是公钥/私钥对。签名可以与报头和有效载荷一起用于生成或构造 JWT。

jwt 通常用于身份验证或在不同方之间安全地传输信息。用户登录应用程序后，服务器会创建一个 JWT，并返回给调用客户端，这是基于 JWT 的身份验证系统的常见流程。

每个后续请求将包括 JWT 作为授权报头，允许访问受保护的路由和资源。一旦后端服务器验证签名有效，它将根据需要从令牌中提取用户数据。为了确保 JWT 有效，只有持有密钥或秘密的一方负责签署信息。

在本文中，我们将学习如何使用 Node.js 后端在 Vue 客户端应用程序上执行身份验证请求。但首先，让我们详细回顾一下 JWT 是如何工作的。

## 目录

## JWT 认证是如何工作的？

在基于 JWT 身份验证的系统中，当用户使用他们的凭证成功登录时，一个 JSON Web 令牌将返回给调用客户端。每当用户想要访问受保护的路由或资源时，用户代理发送相同的 JWT，通常在使用`Bearer`模式的`Authorization`报头中。

标头的内容应该类似于下面的语法:

```
Authorization: Bearer <token>

```

服务器路由将必须检查在`Authorization`报头中是否存在有效的 JWT，以授权用户访问受保护的资源。另外，在`Authorization`头中发送 jwt 还[解决了一些与 CORS](https://github.com/nzoschke/gofaas/blob/master/docs/security-cors-jwt.md) 相关的问题，即使应用程序是从完全不同的域提供的，这也适用。

即使签署了 jwt，信息仍然暴露给用户或其他方，因为数据是未加密的。因此，建议用户不要在 JWT 负载中包含凭据等敏感信息。此外，令牌应该总是有到期时间。

## 引导 Node.js 应用程序

要使用 JWT 作为对后端 Node.js 服务器进行身份验证的方式来设置我们的 Vue 应用程序，首先，我们将构建应用程序的后端部分，它处理 JWT 的生成和随后的验证。本教程的完整代码可以在 [this GitHub repo](https://github.com/firebase007/JWT_VUE_APP) 中找到。

### 安装依赖项

首先，让我们打开我们的终端，安装我们的应用程序所需的所有依赖项，包括来自 npm 的 [`jsonwebtoken`包，用于我们服务器的 Express，以及为完整的 ES2015+环境提供所需的 polyfills 的`babel-polyfill`。](https://www.npmjs.com/package/jsonwebtoke)

要获得我们的应用程序所需的依赖项的完整列表，请查看`package.json`文件；全部内容如下所示:

```
{
  "name": "jwt-vue-node-backend",
  "version": "1.0.0",
  "description": "Backend server for the JWT-Vue-Node-App",
  "main": "server.js",
  "scripts": {
    "create-dev-tables": "babel-node ./app/db/dbConnection createUserTable",
    "drop-dev-tables": "babel-node ./app/db/dbConnection dropUserTable",
    "dev": "nodemon --watch . --exec babel-node -- server/server",
    "start-dev": "babel-node server/server.js",
    "build-server": "babel server -d dist",
    "build": "rm -rf dist && mkdir dist && npm run build-server",
    "start-prod": "node dist/server.js",
    "start": "npm-run-all -p start-prod",
    "revamp": "npm-run-all -p dev drop-dev-tables"
  },
  "esModuleInterop": true,
  "keywords": [
    "Node",
    "Postgres"
  ],
  "author": "Alexander Nnakwue",
  "license": "MIT",
  "devDependencies": {
    "@babel/cli": "^7.12.1",
    "@babel/core": "^7.12.3",
    "@babel/node": "^7.12.10",
    "@babel/preset-env": "^7.12.1",
    "@babel/register": "^7.12.10",
    "babel-preset-es2015": "^6.24.1",
    "babel-watch": "^7.0.0",
    "eslint": "^7.11.0",
    "eslint-config-airbnb-base": "^14.2.0",
    "eslint-plugin-import": "^2.22.1",
    "nodemon": "^2.0.5"
  },
  "dependencies": {
    "@hapi/joi": "^17.1.1",
    "babel-plugin-add-module-exports": "^1.0.4",
    "babel-polyfill": "^6.26.0",
    "bcryptjs": "^2.4.3",
    "body-parser": "^1.19.0",
    "cors": "^2.8.5",
    "dotenv": "^8.2.0",
    "express": "^4.17.1",
    "jsonwebtoken": "^8.5.1",
    "make-runnable": "^1.3.8",
    "moment": "^2.29.1",
    "npm-run-all": "^4.1.5",
    "pg": "^8.4.1"
  },
  "engines": {
    "node": "13.7.0",
    "npm": "6.13.6"
  }
}

```

正如在上面的文件中看到的，我们已经安装了必要的包来使 Babel 作为开发依赖项工作。我们首先确保在全球范围内安装了 Babel CLI，然后我们检查了其他依赖项，如`eslint`、`nodemon`、`pg`等。

请特别注意`package.json`文件的`scripts`部分，它向我们展示了如何创建和删除用户表，构建我们的应用程序的服务器文件，以及在我们的开发环境中运行应用程序。`scripts`部分还向我们展示了如何在生产中运行我们的应用程序。

我们最终的文件夹结构应该如下所示:

![Folder Structure JWT Node Vue App](img/3286aa0d90c5cbab2c4ab3b6a27411fc.png)

## 我们完成的应用程序的文件夹结构

正如你所看到的，我们的应用程序是一个 monorepo，它将很容易地允许我们添加`client`文件夹，构建，并将构建输出到`dist`目录。最后，我们将把构建路径添加到我们的`server.js`文件中。

`app`文件夹包含我们的后端成功运行所需的所有额外组件，包括控制器、数据库处理程序、助手、中间件文件夹等等。克隆 repo 后，确保从`./app/db/dbconnection.js`中删除以下两行:

```
import { createRequire } from 'module';
const require = createRequire(import.meta.url);

```

正如我们前面提到的，`client`文件夹包含了与应用程序面向用户部分相关的所有内容。`server`文件夹包含`server.js`文件，这是一个支持我们应用程序的基本 Express 服务器:

```
import express from 'express'
import 'babel-polyfill'
import cors from 'cors'
import usersRoute from '../app/route/user.js'
import path from 'path'

const __dirname = path.resolve();
const app = express()

// Add  cors middleware
app.use(cors())

// Add middleware for parsing JSON and urlencoded data
app.use(express.urlencoded({ extended: false }))
app.use(express.json())

// Serve static files from the Vue app
app.use(express.static(path.join(`${__dirname}/client/dist`)))

// The "catchall" handler: for any request that doesn't
// match one above, send back Vue's index.html file.
app.get('/*', (req, res) => {
    res.sendFile(path.join(`${__dirname}/client/dist/index.html`))
})

app.use('/api/v1', usersRoute)

app.get('/', (req, res) => {
    res.status(200).send('Welcome to the JWT authentication with Node and Vue.js tutorial')
})

// catch 404 and forward to error handler
app.use((req, res, next) => {
    const err = new Error('Not Found')
    console.log(err)
    err.status = 404
    res.send('Route not found')
    next(err)
})

const server = app.listen(process.env.PORT || 3000).on('listening', () => {
    console.log(`App live and listening on port: ${process.env.PORT}` || 3000)
})

export default app

```

在我们回顾上面服务器文件中发生的一切之前，我们需要为我们的应用程序设置 Babel 配置。`.babelrc`文件如下所示:

```
{
    "presets": ["@babel/preset-env"],
    "plugins": ["add-module-exports"]
}

```

我们在上面使用的[预置](https://babeljs.io/docs/en/babel-preset-env)让我们能够编写最新的 JavaScript 语法，消除了检查哪些语法转换以及可选地检查操作系统目标环境需要哪些浏览器填充的需要。该插件允许 Babel 理解和解释我们的`module.exports`语句和转换。

在上面的`server.js`文件中，我们从我们的客户端构建路径提供静态文件和资产。我们还从`route`路径导入我们的路由。最后，我们导入 [babel-polyfill](https://www.npmjs.com/package/@babel/polyfill) 包，我们在上面已经解释过了。查看 `eslint`和其他设置的[回购。](https://github.com/firebase007/JWT_VUE_APP)

现在，我们将快速设置我们的`config`文件，它从我们的`.env`文件中选取变量:

```
require('dotenv').config()
const { env } = process

module.exports = {
    name: env.APP_NAME,
    baseUrl: env.APP_BASE_URL,
    port: env.APP_PORT,
    databases: {
        postgres: {
            user: env.PG_USERNAME,
            password: env.PG_PASSWORD,
            host: env.PG_HOST,
            port: env.PG_PORT,
            db_name: env.PG_DATABASE,
            url: env.PG_URL,
        },
    },
}

```

Postgres 是我们在本教程中选择的数据库，这意味着我们需要提供必要的细节来连接到一个活动的 Postgres 数据库。现在，让我们看看代码库最重要的部分，它在`app`文件夹下。基本上，这里的所有文件都是我们的服务器响应 HTTP 请求所必需的。

让我们从`routes`文件夹中的`user.js`文件开始:

```
const express = require('express')
const { registerUser, loginUser, me } = require('../controller/users.js')
const  {
 createUser,
} = require( '../helpers/validation.js')
const {
  decodeHeader,
 } = require( '../middleware/verifyAuth.js')

const router = express.Router()

// user Routes

router.post('/auth/signup', createUser, registerUser)
router.post('/auth/login', loginUser)
router.get('/me', decodeHeader, me)

module.exports = router

```

我们只需要三个端点来演示 JWT 如何使用 Vue 和 Node.js。我们导入控制器文件和中间件文件`.../middleware/verifyAuth.js`，后者用于解码从客户端发送的`Authorization`头。我们稍后将对此进行探讨。

现在，让我们看看通过`~/controller/users.js`路径在`user.js`文件中找到的控制器。让我们以探索`login.js`方法为例:

```
/**
   * login User
   * @param {object} req
   * @param {object} res
   * @returns {object} user object
   */

const loginUser = async (req, res) => {
    const { email, password } = req.body

    if (isEmpty(email) || isEmpty(password)) {
        return Response.sendErrorResponse({
            res,
            message: 'Email or Password detail is missing',
            statusCode: 400,
        })
    }

    if (!isValidEmail(email) || !validatePassword(password)) {
        return Response.sendErrorResponse({
            res,
            message: 'Please enter a valid Email or Password',
            statusCode: 400,
        })
    }

    const loginUserQuery = 'SELECT * FROM users WHERE email = $1'

    try {

        const { rows } = await dbQuery.query(loginUserQuery, [email])
        const dbResponse = rows[0]

        if (!dbResponse) {
            return Response.sendErrorResponse({
                res,
                message: 'User with this email does not exist',
                statusCode: 400,
            })
        }

        if (!comparePassword(dbResponse.password, password)) {
            return Response.sendErrorResponse({
                res,
                message: 'The password you provided is incorrect',
                statusCode: 400,
            })
        }

        const token = Utils.generateJWT(dbResponse)
        const refreshExpiry = moment().utc().add(3, 'days').endOf('day')
            .format('X')
        const refreshtoken = Utils.generateJWT({ exp: parseInt(refreshExpiry), data: dbResponse._id })
        delete dbResponse.password
        return Response.sendResponse({
            res,
            responseBody: { user: dbResponse, token, refresh: refreshtoken },
            message: 'login successful',
        })
    } catch (error) {
        console.log(error)
        return Response.sendErrorResponse({
            res,
            message: error,
            statusCode: 500,
        })
    }
}

```

在我们完成通常的密码比较之后，我们生成一个 JWT，其中包含查询我们的`pg`数据库返回的用户详细信息。接下来，我们用`refreshExpiry`生成一个刷新令牌。最后，我们将令牌和刷新令牌作为响应的一部分返回:

```
const token = Utils.generateJWT(dbResponse)
const refreshExpiry = moment().utc().add(3, 'days').endOf('day')
            .format('X')
const refreshtoken = Utils.generateJWT({ exp: parseInt(refreshExpiry), data: dbResponse._id })

```

我已经用 Heroku Postgres 插件建立了一个 Postgres 数据库。当我们将应用程序部署到 Heroku 环境时，我们将讨论细节。

现在，让我们看看定义了`generateJWT`方法的`Utils`文件夹。我们在第 10 行的控制器文件顶部导入了`Utils`模块。导航到`helpers/utils.js`路径，我们将直接了解如何验证和签名令牌:

```
const bcrypt = require('bcryptjs')
const jwt = require('jsonwebtoken')
const fs = require('fs')

const privateKEY = fs.readFileSync('./private.key', 'utf8')
const publicKEY = fs.readFileSync('./public.key', 'utf8')

const i = 'jwt-node'
const s = 'jwt-node'
const a = 'jwt-node'

const verifyOptions = {
    issuer: i,
    subject: s,
    audience: a,
    expiresIn: '8784h',
    algorithm: ['RS256'],
}

const saltRounds = 10

const salt = bcrypt.genSaltSync(saltRounds)

    const generateJWT = (payload) => {
        const signOptions = {
            issuer: i,
            subject: s,
            audience: a,
            expiresIn: '8784h',
            algorithm: 'RS256',
        }

        const options = signOptions
        if (payload && payload.exp) {
            delete options.expiresIn
        }
        return jwt.sign(payload, privateKEY, options)
    }

    const verifyJWT = (payload) => {
        return jwt.verify(payload, publicKEY, verifyOptions)
    }

    const hashPassword = (password) => {
        const hash = bcrypt.hashSync(password, salt)
        return hash
    }

module.exports = {
    hashPassword, verifyJWT, generateJWT
}

```

`generateJWT`方法通过使用一个`privateKEY`和一个`options`参数，将给定的有效负载同步签名到一个 JSON Web 令牌字符串中。

从上面的代码片段中我们可以看到，我们感兴趣的有两个主要方法，`verifyJWT`和`generateJWT`。让我们探索后一种方法，它遵循 JWT 创建方法，由三个不同的元素构成，头、有效载荷和签名/加密数据，用点`.`分隔。

## JWT 结构

正如我们刚刚提到的，jwt 由三个不同的元素构成，包括头部、有效载荷和签名。

header 是一个 JSON 对象，它包含关于自身的声明，包括令牌的类型。它还包含正在使用的签名算法，如 HMAC、SHA-256 与 RSA 或 ES256。我们用的是 RS256 算法。它还定义了 JWT 是签名的还是加密的:

```
const signOptions = {
            issuer: i,
            subject: s,
            audience: a,
            expiresIn: '8784h',
            algorithm: 'RS256',
            "typ": "JWT"
        }

```

令牌的第二部分是有效负载，通常包含用户的详细信息。它还可能包含一些关于该用户的声明。有三种类型的债权，包括注册债权、公共债权和私人债权。其中一些声明是`iss`、发布者、`exp`、到期时间、`sub`、主题和`aud`、受众。

示例负载的结构如下所示:

```
{
  id: 2,
  email: '[email protected]',
  firstname: 'Kelechi',
  lastname: 'Oti',
  password: '$2a$10$yK1bInjD2KieruxwFE9UV.ZuYiwZHu8ciX2vMZmLSvbrOdZ4/Maju',
  createdon: 2020-10-31T23:00:00.000Z
}

```

> 注意:对于已签名的令牌，这些信息虽然受到保护，不会被篡改，但任何人都可以读取。确保不要将机密信息放在 JWT 的有效载荷或报头元素中，除非它经过加密。

第三部分是签名，用于验证消息在传输过程中没有被更改。对于用私钥签名的令牌，它还可以验证 JWT 的发送者就是他们所说的那个人。签名很大程度上取决于用于签名或加密的算法。对于未加密的 jwt，则没有。实际上，对于未加密的 jwt，标头如下所示:

```
{
    "alg": "none"
}

```

我们的 JWT 由回购根路径中可用的私钥/公钥对加密和签名。下面让我们来看一个 JWT 的例子。请注意分隔 JWT 三个元素的点，分别是标头、有效负载和签名，它们是 base64url 编码的:

```
eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6MiwiZW1haWwiOiJhbGV4Lm5uYWt3dWVAZ21haWwuY29tIiwiZmlyc3RuYW1lIjoiS2VsZWNoaSIsImxhc3RuYW1lIjoiT3RpIiwicGFzc3dvcmQiOiIkMmEkMTAkeUsxYkluakQyS2llcnV4d0ZFOVVWLlp1WWl3Wkh1OGNpWDJ2TVptTFN2YnJPZFo0L01hanUiLCJjcmVhdGVkb24iOiIyMDIwLTExLTAxVDAwOjAwOjAwLjAwMFoiLCJpYXQiOjE2MDgyNjY5MDYsImV4cCI6MTYzOTg4OTMwNiwiYXVkIjoicXVlc3Rpb25zLWdhbWUiLCJpc3MiOiJxdWVzdGlvbnMtZ2FtZSIsInN1YiI6InF1ZXN0aW9ucy1nYW1lIn0.FLXTabVVpZ_zuPEspoQe8U5kr8CwKNFKgXBwgQN6yKkW2RXmQGXyuML3dLy4XOEVEgaNoAjSI8De65CKlAq_dA

```

JWT.io 是一个互动平台，可以学习更多关于 JWT 的知识。例如，我们可以继续从上面复制令牌，然后[看看当我们编辑它时会发生什么](https://jwt.io/#debugger-io?token=eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6MiwiZW1haWwiOiJhbGV4Lm5uYWt3dWVAZ21haWwuY29tIiwiZmlyc3RuYW1lIjoiS2VsZWNoaSIsImxhc3RuYW1lIjoiT3RpIiwicGFzc3dvcmQiOiIkMmEkMTAkeUsxYkluakQyS2llcnV4d0ZFOVVWLlp1WWl3Wkh1OGNpWDJ2TVptTFN2YnJPZFo0L01hanUiLCJjcmVhdGVkb24iOiIyMDIwLTExLTAxVDAwOjAwOjAwLjAwMFoiLCJpYXQiOjE2MDgyNjY5MDYsImV4cCI6MTYzOTg4OTMwNiwiYXVkIjoicXVlc3Rpb25zLWdhbWUiLCJpc3MiOiJxdWVzdGlvbnMtZ2FtZSIsInN1YiI6InF1ZXN0aW9ucy1nYW1lIn0.FLXTabVVpZ_zuPEspoQe8U5kr8CwKNFKgXBwgQN6yKkW2RXmQGXyuML3dLy4XOEVEgaNoAjSI8De65CKlAq_dA&publicKey=-----BEGIN%20PUBLIC%20KEY-----%0AMFwwDQYJKoZIhvcNAQEBBQADSwAwSAJBAJ0hkTi8rfBavmlxxTGAARRA3UPWplxg%0Ao208NvREXfUlmkwRmDP/I/OTNCNHdp2hFtVpWtTem3Q1Ru9gRrHy8T8CAwEAAQ==%0A-----END%20PUBLIC%20KEY-----)。

接下来，让我们看看`verifyJWT`方法，它基本上允许我们使用`publicKEY`来验证`privateKEY`是否负责签署报头和有效载荷。下面显示了`verifyJWT`方法的一个片段:

```
const verifyJWT = (payload) => {
        return jwt.verify(payload, publicKEY, verifyOptions)
}

```

JWT 有一个`verify`方法，它使用一个秘密或公共密钥和用于验证的`options`同步验证一个给定的令牌。一旦令牌被验证，就返回该令牌的解码值。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

现在，当我们登录应用程序时，我们可以看到`user`对象、令牌和刷新令牌，如下所示:

```
{
    "data": {
        "user": {
            "id": 2,
            "email": "[email protected]",
            "firstname": "Kelechi",
            "lastname": "Oti",
            "createdon": "2020-10-31T23:00:00.000Z"
        },
        "token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6MiwiZW1haWwiOiJhbGV4Lm5uYWt3dWVAZ21haWwuY29tIiwiZmlyc3RuYW1lIjoiS2VsZWNoaSIsImxhc3RuYW1lIjoiT3RpIiwicGFzc3dvcmQiOiIkMmEkMTAkeUsxYkluakQyS2llcnV4d0ZFOVVWLlp1WWl3Wkh1OGNpWDJ2TVptTFN2YnJPZFo0L01hanUiLCJjcmVhdGVkb24iOiIyMDIwLTEwLTMxVDIzOjAwOjAwLjAwMFoiLCJpYXQiOjE2MDgyNzMxNTgsImV4cCI6MTYzOTg5NTU1OCwiYXVkIjoiand0LW5vZGUiLCJpc3MiOiJqd3Qtbm9kZSIsInN1YiI6Imp3dC1ub2RlIn0.ky4EYUP3t10qaH1aCc0g_jxX5zt8U03l7lrwfpVjViYGqufxRcLoYNV8rw8SygxouXOqSPiF_LxLGv3fi4MQyQ",
        "refresh": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE2MDg1OTUxOTksImlhdCI6MTYwODI3MzE1OCwiYXVkIjoiand0LW5vZGUiLCJpc3MiOiJqd3Qtbm9kZSIsInN1YiI6Imp3dC1ub2RlIn0.VvWQ17Wzyw-tJdRiIxsVzXNhl-nQSPf6Wo1iFOtC3BeueywB1lLaqOK4rmO0UAxKovx6sjZufu2mXEubKsiLOg"
    },
    "status": true,
    "message": "login successful"
}

```

### 令牌和刷新令牌

令牌使用户能够访问受保护的资源。它们通常是短暂的，并且可能在它们的标题上附有截止日期。它们还可能包含有关用户的附加信息。

另一方面，刷新令牌允许用户请求新令牌。例如，在令牌过期后，客户端可能会请求后端服务器生成新令牌，这需要刷新令牌。与访问令牌相反，刷新令牌通常是长期存在的。

位于中间件文件夹中的`verifyAuth`文件包含负责解码从客户端发送的`Authorization`并验证令牌的中间件方法。我们对`verifyAuth`中的`decodeHeader`方法感兴趣，如下所示:

```
const decodeHeader = (req, res, next) => {
    let token = req.headers['x-access-token'] || req.headers.authorization || req.body.token
    if (!token) {
        return Response.sendErrorResponse({ res, message: 'No token provided', statusCode: 401 })
    }
    if (token.startsWith('Bearer ')) {
        // Remove Bearer from string
        token = token.slice(7, token.length)
        if (!token || token === '') Response.sendErrorResponse({ res, message: 'No token provided', statusCode: 401 })
    }
    // call the verifyJWT method to verify the token is valid
    const decoded = Utils.verifyJWT(token)
    if (!decoded) Response.sendErrorResponse({ res, message: 'invalid signature', statusCode: 403 })
    // attach the decoded token to the res.user object
    if (decoded) res.user = decoded
    res.token = token
    return next()
}

```

正如我们从上面的`decodeHeader`方法中看到的，我们以授权头的形式或在`req.body`中接受来自客户端的令牌。如果没有提供令牌，我们将返回一个错误。我们还检查令牌是否带有`Bearer`模式；如果是，我们从`Utils`模块调用`verifyJWT`方法。

正如我们前面讨论的，这个方法使用公钥验证从客户端发送的令牌，并返回令牌的解码值。请注意，如果解码值没有返回，这意味着最初签名的令牌无效。最后，我们将解码后的值附加到响应对象中。

因为我们主要关注 JWT 认证，所以我们不会涉及应用程序的某些部分。例如，关于数据库连接的细节，您可以检查位于`app`目录中的 [`db`文件夹。](https://github.com/firebase007/JWT_VUE_APP)

## 构建我们的客户端 Vue 应用

现在，让我们关注应用程序的客户端部分。要引导 Vue 应用程序，我们可以导航到终端或命令提示符，并在根文件夹中运行以下命令:

```
vue create <folder-name>
```

如果您还没有安装，请使用以下命令之一在您的计算机上全局安装 Vue CLI:

```
npm install -g @vue/cli
#or
yarn global add @vue/cli

```

为了了解客户端应用程序可能需要的依赖关系，请看一下`package.json`文件:

```
{
  "name": "vue-jwt-app",
  "version": "0.1.0",
  "private": true,
  "scripts": {
    "serve": "vue-cli-service serve",
    "build": "vue-cli-service build",
    "lint": "vue-cli-service lint",
    "heroku-postinstall": "npm install && npm run build"
  },
  "proxy": "http://localhost:3000",
  "dependencies": {
    "@babel/preset-env": "7.3.4",
    "axios": "^0.20.0",
    "bootstrap": "^4.5.3",
    "bootstrap-vue": "^2.18.0",
    "core-js": "^3.6.5",
    "vue": "^2.6.11",
    "vue-jwt-decode": "^0.1.0",
    "vue-router": "^3.4.7"
  },
  "devDependencies": {
    "@vue/babel-preset-app": "^4.5.8",
    "@vue/cli-plugin-babel": "^4.5.8",
    "@vue/cli-plugin-eslint": "^4.5.0",
    "@vue/cli-service": "^4.5.0",
    "babel-eslint": "^10.1.0",
    "eslint": "^6.7.2",
    "eslint-config-prettier": "^6.14.0",
    "eslint-plugin-prettier": "^3.1.4",
    "eslint-plugin-vue": "^6.2.2",
    "less": "^3.12.2",
    "less-loader": "^7.0.2",
    "vue-template-compiler": "^2.6.11"
  },
  "eslintConfig": {
    "root": true,
    "env": {
      "node": true
    },
    "extends": [
      "plugin:vue/essential",
      "plugin:prettier/recommended",
      "eslint:recommended"
    ],
    "parserOptions": {
      "parser": "babel-eslint"
    },
    "rules": {}
  },
  "browserslist": [
    "> 1%",
    "last 2 versions",
    "not dead"
  ],
  "Author": "Alexander Nnakwue"
}

```

正如我们从上面的`package.json`文件中看到的，我们已经安装了运行客户端应用程序所需的一些依赖项。最重要的是，我们安装了 [`vue-jwt-decode`](https://github.com/oskar1233/vue-jwt-decode) ，一个用于 Vue.js 应用的 JWT 解码器。

我们可以看到，我们正在使用 Axios 对我们的后端服务器进行 API 调用。我们的客户端应用程序运行所需的其他重要依赖项可以在上面的`package.json`文件中找到。

导航到`src/components/auth`文件夹。在那里，您可以找到我们的客户端应用程序所需的所有组件。我们先来看看登录组件。当用户登录我们的应用程序时，我们将生成的 JWT 保存到本地存储。让我们看看登录组件是什么样子的:

```
&lt;template>
  <div class="container">
    <b-card
      bg-variant="dark"
      header="Vue_JWT_APP"
      text-variant="white"
      class="text-center"
    >
      <b-card-text>Already have an account? Login Here!</b-card-text>
      <div class="row">
        <div class="col-lg-6 offset-lg-3 col-sm-10 offset-sm-1">
          <form
            class="text-center border border-primary p-5"
            style="margin-top:70px;height:auto;padding-top:100px !important;"
            @submit.prevent="loginUser"
          >
            <input
              type="text"
              id="email"
              class="form-control mb-5"
              placeholder="Email"
              v-model="login.email"
            />
            <!-- Password -->
            <input
              type="password"
              id="password"
              class="form-control mb-5"
              placeholder="Password"
              v-model="login.password"
            />
            <p>
              Dont have an account? Click
              <router-link to="/register"> here </router-link> to sign up
            </p>
            <!-- Sign in button -->
            <center>
              <button class="btn btn-primary btn-block w-75 my-4" type="submit">
                Sign in
              </button>
            </center>
          </form>
        </div>
      </div>
    </b-card>
  </div>
</template>
<script>
export default {
  data() {
    return {
      login: {
        email: "",
        password: ""
      }
    };
  },
  methods: {
    async loginUser() {
      try {
        let response = await this.$http.post("/auth/login", this.login);
        let token = response.data.data.token;
        localStorage.setItem("user", token);
        // navigate to a protected resource 
        this.$router.push("/me");
      } catch (err) {
        console.log(err.response);
      }
    }
  }
};
</script>

```

我们使用 [BootstrapVue 来设计我们的组件](https://blog.logrocket.com/building-a-pricing-component-in-vue-js-with-bootstrapvue/)。一旦我们通过后端 API 登录到我们的应用程序，我们将返回的令牌设置为`localStorage`。一旦发生这种情况，我们使用路由器推送 API 导航到`Home`组件。

为了演示整个流程，我们从本地存储中检索令牌，使用`vue-jwt-decode`包对令牌进行解码，然后最终将其附加到`user`对象，然后我们可以在我们的`Home`组件中显示该对象:

```
<template>
  <div>
    <b-navbar id="navbar" toggleable="md" type="dark" variant="info">
      <b-navbar-brand href="#">
        You are currently viewing the Vue_JWT_App
      </b-navbar-brand>
      <b-navbar-nav class="ml-auto">
        <b-nav-text>{{ user.firstname }} | </b-nav-text>
        <b-nav-item @click="logUserOut" active>Logout</b-nav-item>
      </b-navbar-nav>
    </b-navbar>
    <section>
      <div class="container mt-5">
        <div class="row">
          <div class="col-md-12">
            <ul class="list-group">
              <li class="list-group-item">
                Name : {{ user.firstname }} {{ user.lastname }}
              </li>
              <li class="list-group-item">Email : {{ user.email }}</li>
            </ul>
          </div>
        </div>
      </div>
    </section>
  </div>
</template>
<script>
import VueJwtDecode from "vue-jwt-decode";
export default {
  data() {
    return {
      user: {}
    };
  },
  methods: {
    getUserDetails() {
      // get token from localstorage
      let token = localStorage.getItem("user");
      try {
      //decode token here and attach to the user object
      let decoded = VueJwtDecode.decode(token);
      this.user = decoded;    
      } catch (error) {
        // return error in production env
        console.log(error, 'error from decoding token')
      }
    },
    logUserOut() {
      localStorage.removeItem("user");
      this.$router.push("/login");
    }
  },
  created() {
    this.getUserDetails();
  }
};
</script>
<style>
#navbar {
  margin-bottom: 15px;
}
</style>

```

因为`vue-jwt-decode`包是轻量级的，我们需要通过将它包装在`try/catch`块中来处理我们这边的错误情况。此外，你可以在 GitHub 的 client 文件夹中找到关于`route`文件和整个客户端应用设置所需的其他文件的详细信息。

## 将我们的应用程序部署到 Heroku

如果您想在本地运行应用程序，更改`.env`中的数据库设置以匹配您的本地 Postgres 数据库，然后运行`npm run create-dev-tables`，这将创建您需要在本地运行代码而无需 Heroku CLI 的表。

要将我们的应用程序部署到 Heroku，我们首先需要一个 Heroku 帐户，并在我们的开发机器上安装 Heroku CLI。有关说明，请访问[文档。在我们完成了像节点引擎版本和启动脚本这样的设置之后，我们就可以继续在本地测试我们的应用程序了。](https://cli.heroku.com/)

要做到这一点，我们需要在项目的根目录中添加一个`Procfile`, Heroku 将使用它来告诉我们的应用程序运行服务器的路径。该文件的内容如下所示:

```
web: node ./dist/server.js

```

要在本地启动我们的应用程序，我们可以运行`heroku local web`。输出如下所示:

```
[OKAY] Loaded ENV .env File as KEY=VALUE Format
01:43:05 web.1   |  postgres://kdwqywwonzxzoa:8[email protected]ec2-54-156-149-189.compute-1.amazonaws.com:5432/daclachtll2s1g DATABASE_URL
01:43:05 web.1   |  3000 APP-PORT
01:43:05 web.1   |  App live and listening on port: 3000

```

正如我们所看到的，我们在 Heroku 仪表板上设置了一个 Postgres 插件，因为我们的应用程序使用了 Postgres 数据库。此外，查看我们的`package.json`文件的脚本部分，我们可以看到我们的应用程序生产所需的部分。

构建命令运行`"rm -rf dist && mkdir dist && npm run build-server"`，删除所有旧的`dist`文件夹，创建一个`dist`目录，并构建我们的应用程序。build-server 命令运行`"babel server -d dist"`。最终的启动脚本可以在上面相同文件的脚本部分找到。

最后，我们只需要将我们的应用程序部署到 Heroku。为此，我们应该确保在主分支上将我们的更改提交给 Git，然后在 Heroku CLI 上使用命令`heroku login`登录到我们的 Heroku 帐户。一旦我们输入了正确的凭证，我们就可以用`heroku create`命令创建一个新的 Heroku 应用程序。

接下来，我们可以通过运行`git push heroku master`将我们的 app 推送到 Heroku master 分支。然后我们可以看到我们的部署的 URL，我们可以在如下所示的输出的截断部分中找到它:

```
remote:        found 0 vulnerabilities
remote:        
remote:        
remote: -----> Build succeeded!
remote: -----> Discovering process types
remote:        Default types for buildpack -> web
remote: 
remote: -----> Compressing...
remote:        Done: 36.8M
remote: -----> Launching...
remote:        Released v5
remote:        https://secure-cliffs-18654.herokuapp.com/ deployed to Heroku
remote: 
remote: Verifying deploy... done.
To https://git.heroku.com/secure-cliffs-18654.git
   91ced12..28baa4c  master -> master

```

最后，复制 URL `[https://secure-cliffs-18654.herokuapp.com/](https://secure-cliffs-18654.herokuapp.com/)`，导航到我们的客户端应用程序的`src/main.js`文件夹，并更新基本 URL。该文件最终内容如下所示:

```
import Vue from "vue";
import App from "./App.vue";
import router from "./router";
import axios from "axios";
import BootstrapVue from "bootstrap-vue";
import "bootstrap/dist/css/bootstrap.css";
import "bootstrap-vue/dist/bootstrap-vue.css";
const base = axios.create({
  baseURL: "https://secure-cliffs-18654.herokuapp.com/api/v1" // replace on production env
});
import store from "./store/index";
Vue.config.productionTip = false;
Vue.use(BootstrapVue);
Vue.prototype.$http = base;
new Vue({
  store,
  router,
  render: h => h(App)
}).$mount("#app");

```

请注意我们用`axios.create`方法添加了`baseURL`的部分。之后，我们可以将这些新的更改再次提交给 Git，这样我们就上线了！最后，为了测试我们的应用程序，我们可以导航到`login`路线并测试我们的部署。路径如下所示:

```
https://secure-cliffs-18654.herokuapp.com/login

```

注册新帐户后，我们就可以登录了，我们会立即被重定向到`home`路径:

![Redirect Home Route Login](img/914aedef8893aa8e7337b8cf6aa19ce2.png)

我们的应用程序主页如下所示:

![App Homepage JWT](img/3866e18a0dceddee5085860a22547fbc.png)

一旦我们被重定向到主页，出于演示目的，我们将当前通过身份验证的用户的 JWT 负载记录到控制台。详细信息如下图所示:

![JWT Payload](img/f2ea74a08387fab143fdb726e450afc9.png)

## 结论

在本教程中，我们学习了如何在 Node.js 和 Vue 应用程序中集成 JWT。我们从整个后端设置开始，构建我们的应用程序，然后在应用程序的客户端工作。

JWT 提供了一种纯粹的无状态认证机制，因为用户状态从不保存在服务器内存或数据库中。服务器的受保护路由将检查授权头中的有效 JWT，如果有，用户将被授予访问权限。

我们已经回顾了 jwt 是如何工作的以及它们的结构。我们还学习了如何生成 JWT 和验证 JWT 数据。在客户端层，我们已经学习了如何使用`vue-jwt-decode`库解码 JWT。

最后，我们应该注意，在`localStorage`中存储令牌有其自身的风险，因为它们容易受到跨站点脚本(XSS)攻击。回顾一下关于用 JWTs 防止安全问题的细节是很重要的，这样你就会知道什么时候使用替代方案。

关于 jwt 的更多细节，请参考本[详细手册](https://auth0.com/resources/ebooks/jwt-handbook)和[文档](https://jwt.io/introduction)。请在评论区寻求问题和反馈。感谢您的阅读！

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。