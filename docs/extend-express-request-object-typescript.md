# 如何扩展 TypeScript 中的 Express Request 对象

> 原文：<https://blog.logrocket.com/extend-express-request-object-typescript/>

Express 使用`Request`对象向 Node.js 服务器的控制器提供关于 HTTP 请求的数据。因此，来自应用程序层可用请求的所有数据都依赖于该对象。

您可能需要详细说明在 HTTP 请求中收到的数据，并向控制器提供定制信息。在 JavaScript 中，您可以简单地在`Request`对象中定义新属性，并在需要时使用它们。在 TypeScript 中，如果要定义自定义属性，需要扩展`Request`类型。

现在让我们了解一下 Express 中的`Request`是什么，并深入研究为什么在 TypeScript 中扩展`Request`类型会有用。然后，让我们看看如何通过内置在 TypeScript 中的快速演示应用程序来利用扩展的`Request`对象。

**TL；DR:** 让我们学习如何扩展 TypeScript 中的`Request`类型，使其实例存储您可以在控制器级别使用的自定义数据。

## Express 中的`Request`对象是什么？

[`Request`](http://(https://expressjs.com/en/5x/api.html#req) 对象表示客户端对快递服务器执行的 HTTP 请求。换句话说，Express 服务器可以通过`Request`实例读取从客户机接收的数据。因此，`Request`有几个属性可以访问 HTTP 请求中包含的所有信息，但最重要的是:

```
query: this object contains a property for each query string parameter present in the URL of the request:
app.get("/users", (req: Request, res: Response) => {
  // on GET "/users?id=4" this would print "4"
  console.log(req.query.id)
});
params: this object contains the parameters defined in the API URL according to the Express routing convention:
app.get("/users/:id", (req: Request, res: Response) => {
  // on GET "/users/1" this would print "1"
  console.log(req.params.id)
});
body: this object contains key-value pairs of data submitted in the body of the HTTP request:
app.get("/users", (req: Request<never, never, { name: string; surname: string }, never>, res: Response) => {
  const { name, surname } = req.body

  // ...
})
headers: this object contains a property for each HTTP header sent by the request.
cookies: when using the cookie-parser Express middleware, this object contains a property for each cookie sent by the request

```

## 为什么要延长`Request`？

[Express 控制器](https://developer.mozilla.org/en-US/docs/Learn/Server-side/Express_Nodejs/routes)可以使用`Request`对象访问 HTTP 请求中包含的所有数据。这并不意味着`Request`对象是与控制器交互的唯一方式。相反，Express 也支持[中间件](https://expressjs.com/en/guide/using-middleware.html)。快速中间件是可用于添加应用级或路由器级功能的功能。

中间件功能与路由器级别的端点相关联，如下所示:

```
const authenticationMiddleware = require("../middlewares/authenticationMiddleware")
const FooController = require("../controllers/foo")

app.get(
  "/helloWorld",
  FooController.helloWorld, // (req, res) => { res.send("Hello, World!") }
  // registering the authenticationMiddleware to the "/helloWorld" endpoint
  authenticationMiddleware,
)

```

注意，中间件功能是在包含 API 业务逻辑的控制器功能被调用之前执行的。点击这里了解更多关于他们如何工作以及快速中间件能提供什么的信息。

这里需要注意的是，中间件可以修改 Express `Request`对象，添加定制信息使其在控制器级别可用。例如，假设您想让您的 API 只对拥有有效身份验证令牌的用户可用。为此，您可以定义一个简单的身份验证中间件，如下所示:

```
import { Request, Response, NextFunction } from "express"

export function handleTokenBasedAuthentication(req: Request, res: Response, next: NextFunction) {
  const authenticationToken = req.headers["authorization"]

  if (authenticationToken !== undefined) {
    const isTokenValid = // verifying if authenticationToken is valid with a query or an API call...

    if (isTokenValid) {
      // moving to the next middleware
      return next()
    }
  }

  // if the authorization token is invalid or missing returning a 401 error
  res.status(401).send("Unauthorized")
}

```

当在 HTTP 请求的 [`Authorization`](http://(https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Authorization)) 报头中接收的认证令牌有效时，该值唯一地与您的服务的用户相关联。换句话说，身份验证令牌允许您识别发出请求的用户，了解这一点非常重要。例如，控制器级别的业务逻辑可能会根据用户的角色而变化。

如果几个控制器级别的函数需要知道执行 API 调用的用户是谁，您必须在多个地方复制从`Authorization`头中检索用户所需的逻辑。相反，您应该用一个`user`属性扩展 Express `Request`对象，并在认证中间件中给它一个值。

注意，TypeScript 中的 Express `Request`类型不涉及`user`属性。这意味着你不能简单地如下扩展`Request`对象:

```
import { Request, Response, NextFunction } from "express"

export function handleTokenBasedAuthentication(req: Request, res: Response, next: NextFunction) {
  const authenticationToken = req.headers["authorization"]

  if (authenticationToken !== undefined) {
    const isTokenValid = // verifying if authenticationToken is valid with a query or an API call...

    if (isTokenValid) {
      const user = // retrieving the user info based on authenticationToken  

      req["user"] = user // ERROR: Property 'user' does not exist on type 'Request'

      // moving to the next middleware
      return next()
    }
  }

  // if the authorization token is invalid or missing returning a 401 error
  res.status(401).send("Unauthorized")
}

```

这将导致以下错误:

```
Property 'user' does not exist on type 'Request<ParamsDictionary, any, any, ParsedQs, Record<string, any>>'.

```

类似地，您可以使用扩展的`Request`来避免控制器级别的类型转换，并使您的代码库更加整洁和健壮。假设您的后端应用程序只支持三种语言:英语、西班牙语和意大利语。换句话说，您已经知道了`Content-Language` HTTP 头只能接受`en`、`es`和`it`。当标头被省略或包含无效值时，您希望使用英语作为默认语言。

请记住，`req.headers["Content-Language"]`返回一个`string | string[] | undefined`类型。这意味着如果你想使用`Content-Language`头值作为一个`string`，你必须如下转换它:

```
const language = (req.headers["content-language"] || "en") as string | undefined

```

然而，用这种逻辑填充代码并不是一个好的解决方案。相反，您可以使用中间件来扩展`Request`,如下所示:

```
import { Request, Response, NextFunction } from "express"

const SUPPORTED_LANGUAGES = ["en", "es", "it"]
// this syntax is equals to "en" | "es" | "it"
export type Language = typeof SUPPORTED_LANGUAGES[number] 

export function handleCustomLanguageHeader(req: Request, res: Response, next: NextFunction) {
  const languageHeader = req.headers["content-language"]

  // default language: "en"
  let language: Language = SUPPORTED_LANGUAGES[0]

  if (typeof languageHeader === "string" && SUPPORTED_LANGUAGES.includes(languageHeader)) {
    language = languageHeader
  }

  // extending the Request object with a language property of type Language...

  return next()
}

```

这只是两个例子，但是还有其他一些场景，用定制数据扩展`Request`可以节省您的时间，并使您的代码库更加优雅和可维护。

## 本地扩展 Express `Request`接口

为了实现更易于维护的代码，您可能想到的第一个想法是在本地扩展`Request`类型。您可以为单个请求扩展 Express `Request`接口，如下所示:

```
import { NextFunction, Request, Response } from "express"

export type Language = "en" | "es" | "it

export interface LanguageRequest extends Request {
  language: Language
}

export const HelloWorldController = {
    default: async (req: LanguageRequest, res: Response, next: NextFunction) => {
        let message

        switch (req.language) {
            default:
            case "en": {
                message = "Hello, World!"
                break
            }
            case "es": {
                message = "¡Hola, mundo!"
                break
            }
            case "it": {
                message = "Ciao, mondo!"
                break
            }
        }

        res.json(message)
    },
    // other requests...
}

```

这是一个有效的解决方案。如您所见，`default()`请求可以访问定制的`language`属性。如果您需要在有限数量的请求上使用特殊的`Request`类型，您可以使用它。

同时，请记住，这种方法涉及样板代码，很容易导致代码重复。例如，您可能会在几个控制器上重复类型扩展逻辑。这是你想要避免的事情。

出于这个原因，您应该考虑全局扩展`Request`。我们来学习一下怎么做吧！

## 在 TypeScript 中全局扩展 Express `Request`类型

向 Express `Request`类型定义添加额外的字段只需要几行代码。应用程序中的每个请求都可以看到新的扩展类型。让我们通过一个基于前面介绍的中间件的演示应用程序来看看如何全局扩展`Request`并利用扩展的类型。

克隆支持文章的 [GitHub 存储库，并使用以下命令在本地启动示例后端应用程序:](https://github.com/Tonel/extend-express-request-ts-demo)

```
git clone https://github.com/Tonel/extend-express-request-ts-demo
cd extend-express-request-ts-demo
npm i
npm start

```

现在，跟着学习如何利用 TypeScript 中的 extended Express `Request`类型。

### 先决条件

为了复制本文的目标，您需要以下内容:

*   快速> = 4.x
*   @类型/表达式> = 4.x
*   类型脚本> = 4.x

如果您在 TypeScript 中没有 Express 项目，您可以在这里从头开始学习如何设置一个 [Express 和 TypeScript 项目](https://blog.logrocket.com/how-to-set-up-node-typescript-express/)。

### 了解 TypeScript 中的声明合并

`Request`是`Express`类型的一部分，用在`Express`函数中，所以你对它没有控制权。幸运的是，TypeScript 支持[声明合并](https://www.typescriptlang.org/docs/handbook/declaration-merging.html)。

由于声明合并，TypeScript 编译器可以将两个或更多具有相同名称的不同声明合并到一个定义中。这意味着产生的定义具有原始声明的所有特征。

换句话说，声明合并允许您向现有类型添加额外的属性和方法，这正是您在这里想要实现的。

让我们看一个例子来更好地理解声明合并是如何工作的:

```
// declaring an interface with 
// a single property
interface Person {
  fullName: string;
}

// declaring the Person interface again,
// this time with a different property
interface Person {
  age: number;
}

// the resulting Person interface has both 
// the 'fullName' and 'age' properties
const person: Person = {
  fullName: 'Maria Smith',
  age: 37
}

```

现在，让我们看看如何使用声明合并向 Express `Request`类型添加定制属性。

### 向`Request`类型添加自定义属性

扩展`Request`类型所要做的就是定义一个`index.d.ts`文件，如下所示:

```
// src/types/express/index.d.ts

import { Language, User } from "../custom";

// to make the file a module and avoid the TypeScript error
export {}

declare global {
  namespace Express {
    export interface Request {
      language?: Language;
      user?: User;
    }
  }
}

```

将该文件放在`src/types/express`文件夹中。TypeScript 使用 [`.d.ts`声明文件](https://en.wikipedia.org/wiki/TypeScript#Declaration_files)来加载关于用 JavaScript 编写的库的类型信息。这里， [`index.d.ts`全局模块](https://www.typescriptlang.org/docs/handbook/declaration-files/templates/global-d-ts.html)将被 TypeScript 使用，通过声明合并对 Express `Request`类型进行全局扩展。根据[明示](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/master/types/express-serve-static-core/index.d.ts#L18-L19) [源代码](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/master/types/express-serve-static-core/index.d.ts#L18-L19)，这是官方认可的扩展`Request`类型的方式。

现在，您所有的`Express`请求都将能够访问扩展的`Request`类型！

注意`Language`和`User`自定义类型在`src/types/custom.ts`文件中定义如下:

```
// src/types/custom.ts

export const SUPPORTED_LANGUAGES = ["en", "es", "it"]
// this syntax is equals to "en" | "es" | "it"
export type Language = typeof SUPPORTED_LANGUAGES[number]

export type User = {
    id: number,
    name: string,
    surname: string,
    authenticationToken?: string | null
}

```

这些类型将分别用在`handleCustomLanguageHeader()`和`handleTokenBasedAuthentication()`中间件功能中。让我们看看怎么做。

## 使用扩展的`Request`对象

现在，让我们学习如何使用扩展的`Request`对象。首先，让我们完成前面介绍的中间件功能。这是`authentication.middleware.ts`的样子:

```
// src/middlewares/authentication.middleware.ts 

import { Request, Response, NextFunction } from "express"
import { User } from "../types/custom"

// in-memory database
const users: User[] = [
    {
        id: 1,
        name: "Maria",
        surname: "Williams",
        authenticationToken: "$2b$08$syAMV/CyYt.ioZ3w5eT/G.omLoUdUWwTWu5WF4/cwnD.YBYVjLw2O",
    },
    {
        id: 2,
        name: "James",
        surname: "Smith",
        authenticationToken: null,
    },
    {
        id: 3,
        name: "Patricia",
        surname: "Johnson",
        authenticationToken: "$2b$89$taWEB/dykt.ipQ7w4aTPGdo/aLsURUWqTWi9SX5/cwnD.YBYOjLe90",
    },
]

export function handleTokenBasedAuthentication(req: Request, res: Response, next: NextFunction) {
    const authenticationToken = req.headers["authorization"]

    if (authenticationToken !== undefined) {
        // using the in-memory sample database to verify if authenticationToken is valid
        const isTokenValid = !!users.find((u) => u.authenticationToken === authenticationToken)

        if (isTokenValid) {
            // retrieving the user associated with the authenticationToken value
            const user = users.find((u) => u.authenticationToken === authenticationToken)

            req.user = user

            // moving to the next middleware
            return next()
        }
    }

    // if the authorization token is invalid or missing returning a 401 error
    res.status(401).send("Unauthorized")
}

```

为了简单起见，身份验证令牌通过内存中的数据库进行验证。在现实场景中，用一些查询或 API 调用替换这个简单的逻辑。注意现在如何将与令牌相关联的用户分配给`Request`自定义`user`属性。

另外，请注意，您扩展了`Request`接口，而不是对象本身。因此，当处理具有结构化类型的`Request`属性时，例如`user`属性，任何直接访问其字段的尝试都将导致错误:

```
// src/middlewares/authentication.middleware.ts 

// ...

export function handleTokenBasedAuthentication(req: Request, res: Response, next: NextFunction) {
    // ...

    // this will not work because user is not defined 
    req.user.id= 1
    req.user.name = "John"    
    req.user.surname = "Williams"       

    // ...
}

```

这个代码片段将返回一个`Object is possibly 'undefined'`错误。这是因为`user`还没有定义。换句话说，`req.user`是`undefined`，你不能访问它的内部属性。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

如果你想给嵌套属性的内部字段赋值，你可以使用如下的[扩展操作符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax):

```
// src/middlewares/authentication.middleware.ts 

// ...

export function handleTokenBasedAuthentication(req: Request, res: Response, next: NextFunction) {
    // ...

    // this line code of code will work as expected
    req.user = { ...req.user, id: 1, name: "John", surname: "Williams" }       

    // ...
}

```

这段代码编译时不会出错。具体来说，spread 操作符允许您覆盖`req.user`对象的属性。由于`req.user`是`undefined`，所以 spread 操作符会将`req.user`视为空对象。因此，考虑到`User`类型的所有必需字段都已经在赋值右边的对象上提供了，`req.user`可以被正确地赋值。

现在，让我们看看最终的`language.middleware.ts`中间件文件:

```
// src/middlewares/headers.middleware.ts

import { Request, Response, NextFunction } from "express"
import { Language, SUPPORTED_LANGUAGES } from "../types/custom"

export function handleLanguageHeader(req: Request, res: Response, next: NextFunction) {
    const languageHeader = req.headers["content-language"]

    // default language: "en"
    let language: Language = SUPPORTED_LANGUAGES[0]

    if (typeof languageHeader === "string" && SUPPORTED_LANGUAGES.includes(languageHeader)) {
        language = languageHeader
    }

    req.language = language

    return next()
}

```

`Request`自定义`language`属性用于存储`Language`信息。

接下来，让我们看看两个不同 API 中的`Request`自定义属性。这是`HelloWorldController`对象的样子:

```
// src/controllers/helloWorld.ts 

import { NextFunction, Request, Response } from "express"

export const HelloWorldController = {
    default: async (req: Request<never, never, never, never>, res: Response, next: NextFunction) => {
        let message

        switch (req.language) {
            default:
            case "en": {
                message = "Hello, World!"
                break
            }
            case "es": {
                message = "¡Hola, mundo!"
                break
            }
            case "it": {
                message = "Ciao, mondo!"
                break
            }
        }

        res.json(message)
    },
    hello: async (req: Request<never, never, never, never>, res: Response, next: NextFunction) => {
        res.json(`Hey, ${req.user?.name}`)
    },
}

```

如你所见，`HelloWorldController`定义了两个 API。第一个使用`Request`自定义`language`属性，而第二个使用`Request`自定义`user`属性。

最后，您必须在路由器文件中注册中间件函数及其端点，如下所示:

```
// src/routes/index.ts

import { Router } from "express"
import { HelloWorldController } from "../controllers/helloWorld"
import { handleLanguageHeader } from "../middleware/customHeaders.middleware"
import { handleTokenBasedAuthentication } from "../middleware/authentication.middleware"

export const router = Router()

router.get("/", handleLanguageHeader, HelloWorldController.default)
router.get("/hello", handleTokenBasedAuthentication, HelloWorldController.hello)

```

## 测试扩展的`Request`

让我们用 [`curl`](https://curl.se/docs/manpage.html) 来测试一下前面定义的 API。首先，用`npm run start`启动 demo Express 应用程序。

现在，让我们看看`/`端点的行为。

```
curl -i -H "Content-Language: it" http://localhost:8081/ returns Ciao, mondo!
curl -i http://localhost:8081/ returns the default Hello, World!
curl -i -H "Content-Language: es" http://localhost:8081/ returns ¡Hola, mundo!

```

正如您所看到的，API 返回的消息语言按照预期通过`Content-Language`头返回。

现在，让我们测试一下`/hello`端点。

```
curl -i http://localhost:8081/hello returns a 401 Unauthorized error
curl -i -H "Authorization: $2b$08$syAMV/CyYt.ioZ3w5eT/G.omLoUdUWwTWu5WF4/cwnD.YBYVjLw2O"http://localhost:8081/hello returns¡Hola, Maria!

```

同样，由于有了`Authorization`头值，响应取决于加载的用户。
瞧吧！您刚刚学习了如何扩展 Express `Request`类型，以及如何使用它向控制器提供定制信息。

## 结论

在本文中，我们研究了 Express `Request`对象是什么，为什么它如此重要，以及何时需要扩展它。`Request`对象存储关于 HTTP 请求的所有信息。能够扩展它是将自定义数据直接传递给控制器的有效方法。如图所示，这允许您避免代码重复。

在这里，您学习了如何在 TypeScript 中扩展 Express `Request`类型。这很简单，只需要几行代码。此外，它还可以为您的后端应用程序带来一些好处，正如用 TypeScript 开发的示例 demo Express 应用程序所示。

感谢阅读！我希望这篇文章对你有所帮助。如果有任何问题、意见或建议，请随时联系我。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.