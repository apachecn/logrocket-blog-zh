# 使用 JWT 认证创建全栈 MERN 应用程序:第 4 部分

> 原文：<https://blog.logrocket.com/mern-app-jwt-authentication-part-4/>

这是我们使用 JWT 认证构建全栈 MERN 应用系列的最后一篇文章。在开始之前，通读[第一部分](https://blog.logrocket.com/mern-app-jwt-authentication-part-1/)、[第二部分](https://blog.logrocket.com/mern-app-jwt-authentication-part-2/)，尤其是[第三部分](https://blog.logrocket.com/mern-app-jwt-authentication-part-3/)——额外的上下文将帮助你更好地理解这一延续。

到目前为止，我们已经成功地创建了一个基本系统，它与 REST 端点对话并提供响应，根据需要更改状态，并显示正确的内容。它也有一个持久的登录。

## 添加新端点

在这里，我们将处理创建用户、在服务器端验证用户，以及生成不同类型的响应，比如找不到用户、不正确的凭证等。

我们将从服务器的示例存储开始，并验证用户。在此之前，我们需要一个供用户登录的端点。让我们首先编辑我们的`server.js`并添加一条新路线，如下所示:

```
app.post("/api/Users/SignIn", (req, res) => {
  res.json(req.body);
});
```

## 为用户创建商店

存储类似于数据存储，是一个静态数据库。我们要做的就是为用户创建键值对，并使它们共存。我们还需要导出模块，以便在 main `server.js`中导入它们。

所以，在`users.js`中，我们会添加几个用户。密钥是用户名，对象的值是密码。

```
const Users = {
  Praveen: "[email protected]",
  Cloudroit: "C!0uDr0!7"
};

module.exports = Users;
```

最后，我们使用`module.exports`来导出`Users`对象作为默认导出。

### 导入用户

现在我们应该使用`require`方法将用户存储导入到我们的`server.js`中，以使用`User`对象的内容。

```
const Users = require("./users");
```

### 用户验证逻辑

这是我们验证用户输入的地方(真实的人使用前端)。第一个验证是检查用户是否在系统中。这可以通过两种方式来检查:通过在`Object.keys(User)`中找到键，或者通过使用`typeof`来检查以确保类型不是`undefined`。

如果找不到用户，我们会发送一条错误消息，说明找不到用户。如果密钥存在，我们将根据值验证密码，如果不相等，我们将发送一条错误消息，指出凭证不正确。

在这两种情况下，我们都发送一个状态代码`HTTP 403 Forbidden`。如果找到并验证了用户，我们发送一条简单的消息说`"Successfully Signed In!"`。这包含一个状态代码`HTTP 200 OK`。

```
app.post("/api/Users/SignIn", (req, res) => {
  // Check if the Username is present in the database.
  if (typeof Users[req.body.Username] !== "undefined") {
    // Check if the password is right.
    if (Users[req.body.Username] === req.body.Password) {
      // Send a success message.
      // By default, the status code will be 200.
      res.json({
        Message: "Successfully Signed In!"
      });
    } else {
      // Send a forbidden error if incorrect credentials.
      res.status(403).json({
        Message: "Invalid Username or Password!"
      });
    }
  } else {
    // Send a forbidden error if invalid username.
    res.status(403).json({
      Message: "User Not Found!"
    });
  }
});
```

## 创建服务来消费用户逻辑

随着上述变化，我们需要更新前端的消费逻辑。我们目前还没有与`Users/SignIn` API 端点对话的服务，所以我们将创建一个 auth 服务来消费 API。

### 创建身份验证服务

让我们在`services`目录中创建一个名为`services/AuthService.js`的文件。函数`AuthUser`将把`Username`、`Password`和一个回调函数`cb`作为参数。将`Username`和`Password`作为`POST`数据参数发送给`/api/Users/SignIn`端点，在 promise 的`then()`中，以 response `res`作为参数调用回调函数。

错误条件下也会发生同样的事情，其中状态代码不是`2xx`。在这种情况下，我们将第二个参数作为`true`发送给回调函数，将错误对象作为第一个传递。我们将使用第二个参数在客户端适当地处理错误函数。

```
import axios from "axios";

export const AuthUser = (Username, Password, cb) => {
  axios
    .post("/api/Users/SignIn", {
      Username,
      Password
    })
    .then(function(res) {
      cb(res);
    })
    .catch(function(err) {
      cb(err, true);
    });
};
```

### 在客户端摆脱 JWT

因为我们没有在客户端生成任何 JWT，所以我们可以安全地移除`GenerateJWT()`函数的导入。否则，React 和 ESLint 可能会在编译阶段抛出错误`[no-unused-vars](https://eslint.org/docs/rules/no-unused-vars)`。

```
- import { GenerateJWT, DecodeJWT } from "../services/JWTService";
+ import { DecodeJWT } from "../services/JWTService";
+ import { AuthUser } from "../services/AuthService";
```

### 提交表单时调用授权服务

现在我们只需要用`AuthUser`和一个支持`err`参数的回调函数替换我们的`GenerateJWT`函数——以及该函数的其他依赖项，如`claims`和`header`。

这里处理错误非常简单。如果`err`参数为`true`，立即将接收到的消息设置为`Error`状态，由`res.response.data.Message`访问，通过返回`false`并突然停止该功能来停止运行。

如果不是，我们需要检查状态为`200`。这里是我们需要处理成功函数的地方。我们需要从服务器返回一个 JWT，但是就目前的情况来看，它并没有返回 JWT，因为它是一个伪对象。接下来让我们处理服务器端部分，让它返回 JWT。

```
handleSubmit = e => {
  // Here, e is the event.
  // Let's prevent the default submission event here.
  e.preventDefault();
  // We can do something when the button is clicked.
  // Here, we can also call the function that sends a request to the server.
  // Get the username and password from the state.
  const { Username, Password } = this.state;
  // Right now it even allows empty submissions.
  // At least we shouldn't allow empty submission.
  if (Username.trim().length < 3 || Password.trim().length < 3) {
    // If either of Username or Password is empty, set an error state.
    this.setState({ Error: "You have to enter both username and password." });
    // Stop proceeding.
    return false;
  }
  // Call the authentication service from the front end.
  AuthUser(Username, Password, (res, err) => {
    // If the request was an error, add an error state.
    if (err) {
      this.setState({ Error: res.response.data.Message });
    } else {
      // If there's no error, further check if it's 200.
      if (res.status === 200) {
        // We need a JWT to be returned from the server.
        // As it stands, it doesn't currently return the JWT, as it's dummy.
        // Let's work on the server side part now to make it return the JWT.
      }
    }
  });
};
```

### 在屏幕上显示错误

让我们也更新我们的小数据查看器，以反映错误信息，如果它是可用的。可以追加`<pre>`标签内容，下面显示的是`this.state.Error`的内容。

```
{this.state.Error && (
  <>
    <br />
    <br />
    Error
    <br />
    <br />
    {JSON.stringify(this.state.Error, null, 2)}
  </>
)}
```

## 从服务器生成并发送 JWT

目前，我们的登录 API `"/api/Users/SignIn"`响应只是发出`HTTP 200`。我们需要改变这一点，以便它发送一个成功消息以及在服务器上生成的 JWT。

### 正在更新登录响应

在检查数据库中是否存在`Username`之后，我们需要检查密码是否正确。如果两个条件都满足，我们必须在服务器端创建一个 JWT 并发送给客户端。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

让我们根据默认的标题创建一个 JWT。我们需要根据用户提供的`Username`进行索赔。我在这里没有使用`Password`,因为在响应中以明文形式添加密码是非常不安全的。

```
app.post("/api/Users/SignIn", (req, res) => {
  const { Username, Password } = req.body;
  // Check if the Username is present in the database.
  if (typeof Users[Username] !== "undefined") {
    // Check if the password is right.
    if (Users[Username] === Password) {
      // Let's create a JWT based on our default headers.
      const header = {
        alg: "HS512",
        typ: "JWT"
      };
      // Now we need to make the claims based on Username provided by the user.
      const claims = {
        Username
      };
      // Finally, we need to have the key saved on the server side.
      const key = "$PraveenIsAwesome!";
      // Send a success message.
      // By default, the status code will be 200.
      res.json({
        Message: "Successfully Signed In!",
        JWT: GenerateJWT(header, claims, key)
      });
    } else {
      // Send a forbidden error if incorrect credentials.
      res.status(403).json({
        Message: "Invalid Username or Password!"
      });
    }
  } else {
    // Send a forbidden error if invalid username.
    res.status(403).json({
      Message: "User Not Found!"
    });
  }
});
```

### 更新客户端登录逻辑

更新上面的代码后，`res.data`保存了`Message`和`JWT`。我们需要`JWT`，然后我们需要通过调用`DecodeJWT`服务对其进行解码，并将其存储在状态中。一旦完成，我们还需要在刷新后保持登录，因此我们将把`JWT`存储在`localStorage`中，正如在上一篇文章中所讨论的。

像往常一样，我们检查浏览器是否支持`localStorage`，如果支持，使用`localStorage.setItem()`函数将`JWT`保存在`localStore`中。

```
handleSubmit = e => {
  // Here, e is the event.
  // Let's prevent the default submission event here.
  e.preventDefault();
  // We can do something when the button is clicked.
  // Here, we can also call the function that sends a request to the server.
  // Get the username and password from the state.
  const { Username, Password } = this.state;
  // Right now it even allows empty submissions.
  // At least we shouldn't allow empty submission.
  if (Username.trim().length < 3 || Password.trim().length < 3) {
    // If either of the Username or Password is empty, set an error state.
    this.setState({ Error: "You have to enter both username and password." });
    // Stop proceeding.
    return false;
  }
  // Call the authentication service from the front end.
  AuthUser(Username, Password, (res, err) => {
    // If the request was an error, add an error state.
    if (err) {
      this.setState({ Error: res.response.data.Message });
    } else {
      // If there's no errors, further check if it's 200.
      if (res.status === 200) {
        // We need a JWT to be returned from the server.
        // The res.data holds both Message and JWT. We need the JWT.
        // Decode the JWT and store it in the state.
        DecodeJWT(res.data.JWT, data =>
          // Here, data.data will have the decoded data.
          this.setState({ Data: data.data })
          );
        // Now to persist the login after refresh, store in localStorage.
        // Check if localStorage support is there.
        if (typeof Storage !== "undefined") {
          // Set the JWT to the localStorage.
          localStorage.setItem("JWT", res.data.JWT);
        }
      }
    }
  });
};
```

## 错误修复和评论

在开发整个应用程序时，我们忽略了一些错误，如果我们像最终用户一样使用它，我们会注意到这些错误。让我们看看他们是怎么进来的，然后把它们都修好。

### 成功事件期间清除所有错误消息

成功登录并注销后，错误消息不会被清除。我们需要在成功登录后清除错误消息。

```
 AuthUser(Username, Password, (res, err) => {
   // If the request was an error, add an error state.
   if (err) {
     this.setState({ Error: res.response.data.Message });
   } else {
     // If there's no errors, further check if it's 200.
     if (res.status === 200) {
+      // Since there aren't any errors, we should remove the error text.
+      this.setState({ Error: null });
       // We need a JWT to be returned from the server.
       // The res.data holds both Message and JWT. We need the JWT.
       // Decode the JWT and store it in the state.
       DecodeJWT(res.data.JWT, data =>
         // Here, data.data will have the decoded data.
         this.setState({ Data: data.data })
          );
       // Now to persist the login after refresh, store in localStorage.
       // Check if localStorage support is there.
       if (typeof Storage !== "undefined") {
         // Set the JWT to the localStorage.
         localStorage.setItem("JWT", res.data.JWT);
       }
     }
   }
 });
```

### 注销后清除错误消息

这里也一样。注销后，最好对所有内容进行清理，即`Error`、`Response`和`Data`。我们已经将`Response`和`Data`设置为`null`，但没有设置`Error`。

```
 SignOutUser = e => {
   // Prevent the default event of reloading the page.
   e.preventDefault();
   // Clear the errors and other data.
   this.setState({
+    Error: null,
     Response: null,
     Data: null
   });
   // Check if localStorage support is there.
   if (typeof Storage !== "undefined") {
     // Check if JWT is already saved in the local storage.
     if (localStorage.getItem("JWT") !== null) {
       // If there's something, remove it.
       localStorage.removeItem("JWT");
     }
   }
 };
```

## 最终注释文件

#### `server/server.js`

```
const express = require("express");
const morgan = require("morgan");
const { GenerateJWT, DecodeJWT, ValidateJWT } = require("./dec-enc.js");
const Users = require("./users");

const app = express();
app.use(express.json());
app.use(morgan("dev"));
const port = process.env.PORT || 3100;

const welcomeMessage =
  "Welcome to the API Home Page. Please look at the documentation to learn how to use this web service.";

app.get("/", (req, res) => res.send(welcomeMessage));

app.post("/api/GenerateJWT", (req, res) => {
  let { header, claims, key } = req.body;
  // In case, due to security reasons, the client doesn't send a key,
  // use our default key.
  key = key || "$PraveenIsAwesome!";
  res.json(GenerateJWT(header, claims, key));
});
app.post("/api/DecodeJWT", (req, res) => {
  res.json(DecodeJWT(req.body.sJWS));
});
app.post("/api/ValidateJWT", (req, res) => {
  let { header, token, key } = req.body;
  // In case, due to security reasons, the client doesn't send a key,
  // use our default key.
  key = key || "$PraveenIsAwesome!";
  res.json(ValidateJWT(header, token, key));
});

app.post("/api/Users/SignIn", (req, res) => {
  const { Username, Password } = req.body;
  // Check if the Username is present in the database.
  if (typeof Users[Username] !== "undefined") {
    // Check if the password is right.
    if (Users[Username] === Password) {
      // Let's create a JWT based on our default headers.
      const header = {
        alg: "HS512",
        typ: "JWT"
      };
      // Now we need to make the claims based on Username provided by the user.
      const claims = {
        Username
      };
      // Finally, we need to have the key saved on the server side.
      const key = "$PraveenIsAwesome!";
      // Send a success message.
      // By default, the status code will be 200.
      res.json({
        Message: "Successfully Signed In!",
        JWT: GenerateJWT(header, claims, key)
      });
    } else {
      // Send a forbidden error if incorrect credentials.
      res.status(403).json({
        Message: "Invalid Username or Password!"
      });
    }
  } else {
    // Send a forbidden error if invalid username.
    res.status(403).json({
      Message: "User Not Found!"
    });
  }
});

app.listen(port, () => console.log(`Server listening on port ${port}!`));
```

### 客户端

#### `client/src/components/Login.js`

```
import React, { Component } from "react";
import { DecodeJWT } from "../services/JWTService";
import { AuthUser } from "../services/AuthService";

class Login extends Component {
  state = {
    Username: "",
    Password: ""
  };
  handleChange = e => {
    // Here, e is the event.
    // e.target is our element.
    // All we need to do is update the current state with the values here.
    this.setState({
      [e.target.name]: e.target.value
    });
  };
  handleSubmit = e => {
    // Here, e is the event.
    // Let's prevent the default submission event here.
    e.preventDefault();
    // We can do something when the button is clicked.
    // Here, we can also call the function that sends a request to the server.
    // Get the username and password from the state.
    const { Username, Password } = this.state;
    // Right now it even allows empty submissions.
    // At least we shouldn't allow empty submission.
    if (Username.trim().length < 3 || Password.trim().length < 3) {
      // If either of the Username or Password is empty, set an error state.
      this.setState({ Error: "You have to enter both username and password." });
      // Stop proceeding.
      return false;
    }
    // Call the authentication service from the front end.
    AuthUser(Username, Password, (res, err) => {
      // If the request was an error, add an error state.
      if (err) {
        this.setState({ Error: res.response.data.Message });
      } else {
        // If there's no errors, further check if it's 200.
        if (res.status === 200) {
          // Since there aren't any errors, we should remove the error text.
          this.setState({ Error: null });
          // We need a JWT to be returned from the server.
          // The res.data holds both Message and JWT. We need the JWT.
          // Decode the JWT and store it in the state.
          DecodeJWT(res.data.JWT, data =>
            // Here, data.data will have the decoded data.
            this.setState({ Data: data.data })
          );
          // Now to persist the login after refresh, store in localStorage.
          // Check if localStorage support is there.
          if (typeof Storage !== "undefined") {
            // Set the JWT to the localStorage.
            localStorage.setItem("JWT", res.data.JWT);
          }
        }
      }
    });
  };
  SignOutUser = e => {
    // Prevent the default event of reloading the page.
    e.preventDefault();
    // Clear the errors and other data.
    this.setState({
      Error: null,
      Response: null,
      Data: null
    });
    // Check if localStorage support is there.
    if (typeof Storage !== "undefined") {
      // Check if JWT is already saved in the local storage.
      if (localStorage.getItem("JWT") !== null) {
        // If there's something, remove it.
        localStorage.removeItem("JWT");
      }
    }
  };
  componentDidMount() {
    // When this component loads, check if JWT is already saved in the local storage.
    // So, first check if localStorage support is there.
    if (typeof Storage !== "undefined") {
      // Check if JWT is already saved in the local storage.
      if (localStorage.getItem("JWT") !== null) {
        // If there's something, try to parse and sign the current user in.
        this.setState({
          Response: localStorage.getItem("JWT")
        });
        DecodeJWT(localStorage.getItem("JWT"), data =>
          // Here, data.data will have the decoded data.
          this.setState({ Data: data.data })
        );
      }
    }
  }
  render() {
    return (
      <div className="login">
        <div className="container">
          <div className="row">
            <div className="col-6">
              <div className="card">
                {this.state.Data ? (
                  <div className="card-body">
                    <h5 className="card-title">Successfully Signed In</h5>
                    <p className="text-muted">
                      Hello {this.state.Data.Username}! How are you?
                    </p>
                    <p className="mb-0">
                      You might want to{" "}
                      <button
                        className="btn btn-link"
                        onClick={this.SignOutUser}
                      >
                        sign out
                      </button>
                      .
                    </p>
                  </div>
                ) : (
                  <div className="card-body">
                    <h5 className="card-title">Sign In</h5>
                    <h6 className="card-subtitle mb-2 text-muted">
                      Please sign in to continue.
                    </h6>
                    <form onSubmit={this.handleSubmit}>
                      {this.state.Error && (
                        <div className="alert alert-danger text-center">
                          <p className="m-0">{this.state.Error}</p>
                        </div>
                      )}
                      {["Username", "Password"].map((i, k) => (
                        <div className="form-group" key={k}>
                          <label htmlFor={i}>{i}</label>
                          <input
                            type={i === "Password" ? "password" : "text"}
                            name={i}
                            className="form-control"
                            id={i}
                            placeholder={i}
                            value={this.state[i]}
                            onChange={this.handleChange}
                          />
                        </div>
                      ))}
                      <button type="submit" className="btn btn-success">
                        Submit
                      </button>
                    </form>
                  </div>
                )}
              </div>
            </div>
            <div className="col-6">
              <pre>
                State Data
                <br />
                <br />
                {JSON.stringify(
                  {
                    Username: this.state.Username,
                    Password: this.state.Password
                  },
                  null,
                  2
                )}
                {this.state.Response && (
                  <>
                    <br />
                    <br />
                    Response Data (JWT)
                    <br />
                    <br />
                    {this.state.Response}
                  </>
                )}
                {this.state.Data && (
                  <>
                    <br />
                    <br />
                    Decoded Data
                    <br />
                    <br />
                    {JSON.stringify(this.state.Data, null, 2)}
                  </>
                )}
                {this.state.Error && (
                  <>
                    <br />
                    <br />
                    Error
                    <br />
                    <br />
                    {JSON.stringify(this.state.Error, null, 2)}
                  </>
                )}
              </pre>
            </div>
          </div>
        </div>
      </div>
    );
  }
}
export default Login;
```

#### `client/src/services/JWTService.js`

```
import axios from "axios";

export const GenerateJWT = (header, claims, key, cb) => {
  // Send POST request to /api/GenerateJWT
  axios
    .post("/api/GenerateJWT", {
      header,
      claims,
      key
    })
    .then(function(res) {
      cb(res);
    })
    .catch(function(err) {
      console.log(err);
    });
};
export const DecodeJWT = (sJWS, cb) => {
  // Send POST request to /api/DecodeJWT
  axios
    .post("/api/DecodeJWT", {
      sJWS
    })
    .then(function(res) {
      cb(res);
    })
    .catch(function(err) {
      console.log(err);
    });
};
export const ValidateJWT = (header, token, key, cb) => {
  // Send POST request to /api/ValidateJWT
  axios
    .post("/api/ValidateJWT", {
      header,
      token,
      key
    })
    .then(function(res) {
      cb(res);
    })
    .catch(function(err) {
      console.log(err);
    });
};
```

#### `client/src/services/AuthService.js`

```
import axios from "axios";

export const AuthUser = (Username, Password, cb) => {
  axios
    .post("/api/Users/SignIn", {
      Username,
      Password
    })
    .then(function(res) {
      cb(res);
    })
    .catch(function(err) {
      cb(err, true);
    });
};
```

## 部署完整的代码

### 使用 React 的生产版本

一旦你的应用被创建，我们需要通过[创建一个生产版本](https://create-react-app.dev/docs/production-build)来构建应用。命令`npm run build`创建一个`build`目录，其中包含应用程序的生产版本。你的 JavaScript 和 CSS 文件将在`build/static`目录下。

`build/static`中的每个文件名将包含文件内容的唯一散列。文件名中的这个散列启用了[长期缓存技术](https://create-react-app.dev/docs/production-build#static-file-caching)。您需要做的就是使用一个静态 HTTP web 服务器，并将`build/`目录的内容放入其中。

除此之外，您还必须在服务器根目录下的`api/`目录中部署您的 API。

### 使用 Heroku

因为我们已经为此使用了一个 [G](https://github.com/praveenscience/JWT-MERN-FullStack) [it 库](https://github.com/praveenscience/JWT-MERN-FullStack)，所以 Heroku 应用程序的一个基本要求就是位于 Git 库中。从项目的根目录开始，我们需要在 Heroku 中创建一个 app 实例。为此，让我们从项目的根目录在终端中使用下面的命令。

```
➜  JWT-MERN-App git:(master) $ heroku create [app-name]
```

在上面的行中，`[app-name]`将被替换为`jwt-mern`。一旦选择了唯一的应用程序名称，Heroku 将检查该名称的可用性，并继续或要求不同的名称。完成该步骤并选择唯一的应用程序名称后，我们可以使用以下命令部署到 Heroku:

```
➜  JWT-MERN-App git:(master) $ git push heroku master
```

你可以在 Heroku 的[文档](https://devcenter.heroku.com/articles/git)中读到更多关于部署到 Heroku 的信息。

## GitHub 库和最终想法

完整的代码和提交都在 GitHub 资源库中:[praveensscience/JWT-MERN-full stack:使用 JWT 认证创建全栈 MERN 应用](https://github.com/praveenscience/JWT-MERN-FullStack)。

希望这一整套文章是有益和有趣的。让我知道你的想法。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)