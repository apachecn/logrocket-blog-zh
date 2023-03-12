# 构建和构造 Node.js MVC 应用程序

> 原文：<https://blog.logrocket.com/building-structuring-node-js-mvc-application/>

开发人员在编程时遵循许多约定，这些约定可能是也可能不是硬性规则，但通常是伟大开发项目的构建块。这些惯例被如此频繁地使用，如此广泛地采用，因为它们被证明是有效的。

在本教程中，我们将学习流行的架构模式模型-视图-控制器(MVC ),然后使用 MVC 在 Node.js 中构建应用程序。

## 教程先决条件

要学习本教程，您需要以下内容:

*   节点. js
*   一个 MongoDB Atlas 帐户来创建我们的应用程序的数据库
*   Yarn(如果没有将 npm 与 Node.js 一起使用)
*   文本编辑器，如 Visual Studio 代码

## 什么是模型-视图-控制器？

MVC 只是软件工程中使用的一种设计或架构模式。虽然这不是一个硬性规定，但是这种模式可以帮助开发人员专注于应用程序的某个特定方面，一次一步。

MVC 的主要目标是将大型应用程序分成具有各自用途的特定部分。

它还允许开发人员以一种安全的方式对应用程序进行逻辑结构化，我们将在本教程中展示这一点。但是首先，让我们分析一下模式的每个方面提供了什么。

### 模型

顾名思义，[一个模型就是一个设计或者结构](https://blog.logrocket.com/dont-underestimate-the-model-in-mvc/)。在 MVC 的情况下，模型决定了数据库的结构，定义了应用程序中与数据库交互的部分。我们将在这里定义用户的属性，这些属性将存储在我们的数据库中。

控制器通过模型访问数据库。可以说模型是应用程序的核心。

### 视角

视图是最终用户在应用程序中进行交互的地方。简单地说，这是所有 HTML 模板文件的位置。

### 控制器

控制器与模型交互，并为视图提供响应和功能。当最终用户发出请求时，它被发送到与数据库交互的控制器。

您可以将控制器想象成餐馆中处理客户订单的服务员，在本例中就是视图。服务员然后去厨房，厨房是模型/数据库，得到食物为顾客服务，厨房是处理请求的控制器。

现在，让我们使用 MVC 模式构建一个应用程序！

## 设置应用程序

为了理解如何使用 MVC，我们将构建一个简单的登录和注册系统，该系统带有一个显示用户信息的仪表板。然而，本教程更多的是关于结构化，而不是我们正在构建的应用程序。

因此，在一个空文件夹中打开您的终端并运行以下命令:

```
npm init -y

```

这将创建一个`package.json`文件。

![Creating A Package JSON File In VSCode](img/e185cd6332ffe06dd8e92bb7c0cc11d5.png)

现在对于这个项目，我们需要安装一些包来开始:

```
npm install express ejs mongoose bcryptjs passport passport-local 

```

这些软件包提供以下内容:

完成之后，您应该会看到一个`node_module`文件夹(这是所有包下载的地方)。

现在创建三个文件夹来代表 MVC: `models`、`views`和`controllers`。

![Saving MVC Folders, Called Model, View, And Controller](img/c6e35a9ca8e31d6e5afa8743db5072ad.png)

## 设置服务器

虽然我们已经创建了自己的文件夹，但没有服务器他们什么也做不了。为了创建我们的服务器，让我们在根目录中创建一个`index.js`文件夹。我们可以随意命名这个文件，只要我们在`package.json`文件中这样声明。

创建`index.js`文件后，转到`package.json`文件，编辑`scripts`，如下所示:

```
//json

{
  "name": "Dashboard",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "develop": "node index.js"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "bcryptjs": "^2.4.3",
    "ejs": "^3.1.6",
    "express": "^4.17.1",
    "mongoose": "^6.0.3",
    "passport": "^0.4.1",
    "passport-local": "^1.0.0"
  }
}

```

注意`main`如何指向`index.js`，`scripts`如何让`develop`指向`index.js`。这意味着每当我们从应用程序根中的命令运行`npm run develop`时，它将运行入口点，在我们的例子中是`index.js`文件。

现在，放开`index.js`文件来创建我们的 Express 服务器。将以下代码复制并粘贴到其中:

```
//js
const express = require('express');
const app = express();
const PORT = process.env.PORT || 4111;
app.listen(PORT, console.log("Server don start for port: " + PORT))

```

记住我们已经安装了 Express。现在，我们只需要它并将`express()`函数分配给`app</code.`

在第 5 行，我们现在使用`listen()`方法启动一个运行在 [http://localhost:4111](http://localhost:4111) 的服务器。要确认我们的服务器现在已经启动并运行，请运行以下命令:

```
npm run develop

```

然后，它显示我们确认服务器在`port 4111`运行。

![Server Confirmation That It Is Connected To Port 4111](img/c99b1f5e1c274be309f0e6ab1b47fdb1.png)

## 创建路线、视图和控制器

服务器启动并运行后，让我们在`view`文件夹中创建一些`.ejs`文件。因为我们遵循 MVC 模式，所以我们需要所有的视图，也就是终端用户看到的，都在一个文件夹中。

在`views`文件夹中，创建以下文件:`login.ejs`、`register.ejs`、`dashboard.ejs`、`layout.ejs`。

![Creating The .ejs Files In The Views Folder](img/f3b596fd7b32d3d82b1af11f9b23ca3b.png)

你可以[从这里](https://github.com/bigpreshy/mvc/tree/main/views)复制代码。

`layout.ejs`代码包含在`view`文件夹的`.ejs`文件中:

```
<!DOCTYPE html>
<html>
<head>
    <meta charset='utf-8'>
    <meta http-equiv='X-UA-Compatible' content='IE=edge'>
    <title>MVC</title>
    <meta name='viewport' content='width=device-width, initial-scale=1'>
    <link href="//maxcdn.bootstrapcdn.com/bootstrap/4.1.1/css/bootstrap.min.css" rel="stylesheet" id="bootstrap-css">
<script src="//maxcdn.bootstrapcdn.com/bootstrap/4.1.1/js/bootstrap.min.js"></script>
<script src="//cdnjs.cloudflare.com/ajax/libs/jquery/3.2.1/jquery.min.js"></script>
</head>
<body><!-- Just an image -->
    <nav class="navbar navbar-light bg-light">
      <a class="navbar-brand" href="#">
        <img src="https://getbootstrap.com/docs/4.0/assets/brand/bootstrap-solid.svg" width="30" height="30" alt="">
      </a>
    </nav>

```

接下来，`login.ejs`代码呈现我们的登录页面，如下所示:

![Blank Rendered Login Page With Email Field, Password Field, And Login Button](img/587dab2b1cf7cecc4c6a5e973f83b06c.png)

然后，`register.ejs`呈现我们的注册页面:

![Blank Rendered Register Page With Name, Email, Password, Confirm Password Fields, and Register Button](img/c2db352d6207f5717a3f929d12774c84.png)

接下来，创建一个名为`routes`的文件夹；从技术上来说，这是控制器的一部分，但是将所有路由放在一个单独的文件夹中也不错。

![Creating The Routes Folder](img/88c00e23b61c9accf0612aac5432de4b.png)

以下是`controller`文件夹中的`loginController.js`文件:

```
//js

//For Register Page
const registerView = (req, res) => {
    res.render("register", {
    } );
}
// For View 
const loginView = (req, res) => {

    res.render("login", {
    } );
}
module.exports =  {
    registerView,
    loginView
};

```

这里，`registerView`和`loginView`分别渲染了`register.ejs`和`login.ejs`视图。两者都是出口。

其后是`routes`文件夹中的`login.js`代码。

```
//js
const express = require('express');
const {registerView, loginView } = require('../controllers/loginController');
const router = express.Router();
router.get('/register', registerView);
router.get('/login', loginView);
module.exports = router;

```

我们这里做的是使用`registerView`和`loginView`的 Express 路由器，从`loginController`出口到`controllers`下。

如果不将`index.js`文件更新为以下内容，上述两个代码块将不会呈现登录和注册页面:

```
//js
const express = require('express');
const app = express();
app.set('view engine', 'ejs');
//Routes
app.use('/', require('./routes/login'));
const PORT = process.env.PORT || 4111;
app.listen(PORT, console.log("Server has started at port " + PORT))

```

请注意，我们已经将视图引擎设置为使用`.ejs`，并将根路由定义为使用`routes`下的`login`中的内容。

## 设置 MongoDB Atlas

如前所述，我们必须用 Atlas 建立一个 [MongoDB 数据库。记住，我们安装了 Mongoose 作为我们需要的 npm 包的一部分。所以，如果你还没有一个新账户，那就创建一个吧。](https://www.mongodb.com/cloud/atlas)

现在，您应该已经准备好了您的 Atlas MongoDB 帐户。转到 Atlas 并创建一个新项目和一个集群(在本教程中使用 free 选项)。

![Creating Cluster For MongoDB In Atlas](img/4ca678b49474f2b38c8cdb0b3a478245.png)

创建集群后，会打开第二个页面，我们可以忽略该页面并保留默认设置。然后，点击**创建集群**。

![Default Settings And Clicking Create Cluster](img/7d112f2557d55cbae6104f98cc0e4e92.png)

要将创建的集群连接到我们的应用程序，我们需要做一些设置。

在**安全**选项卡上，选择**数据库访问**并通过决定用户名和密码创建一个用户。接下来，允许用户读写数据库。

同样，在**安全**选项卡上，选择**网络访问**，添加 IP 地址，选择**允许从任何地方访问**，并确认(这将需要大约 3 分钟才能激活)。

现在，回到集群，点击**连接**，并选择**连接您的应用**

![Connecting App To Cluster In MongoDB](img/f2a4f76df6f6eb5c0dc4eb45d3e06fd6.png)

驱动默认应该是 Node.js，版本应该是 3.6 或更高版本。

下面截图中突出显示的部分是您的主机所在的位置。请注意，你的将与我的不同。

![Finding The Host Within Highlighted Portion of String](img/5a7ee77f41dcd79c48601a3e4d162440.png)

复制主机并返回到项目。

### 连接到数据库

为了连接到我们的数据库，我们必须将我们复制的凭证存储在某个地方，并确保它是安全的。因此，最好将敏感凭证放在一个`.env`文件中。

让我们继续安装`.dotenv`并在我们的根目录下创建`.env`文件:

```
npm install dotenv

```

在`.env`文件中，添加以下内容:

```
 MONGOLAB_URI= "Your credential goes here"

```

注意，这个`.env`文件不应该上传到您的产品中，甚至不应该提交到 git 中；您必须将它包含在您的`.gitignore`文件中。`.env`文件存储了必须使用我们的配置在服务器上重建的虚拟环境。

现在，让我们到我们的`index.js`入口点导入 Mongoose 并将其连接到我们的数据库。记得我们安装了`dotenv`，必须和猫鼬一起导入使用。

现在我们的`index.js`变成了如下:

```
//js
const express = require('express');
const app = express();
const mongoose = require('mongoose');
const dotenv = require("dotenv");
dotenv.config();
// Mongo DB conncetion
const database = process.env.MONGOLAB_URI;
mongoose.connect(database, {useUnifiedTopology: true, useNewUrlParser: true })
.then(() => console.log('e don connect'))
.catch(err => console.log(err));

app.set('view engine', 'ejs');
//Routes
app.use('/', require('./routes/login'));
const PORT = process.env.PORT || 4111;
app.listen(PORT, console.log("Server has started at port " + PORT))

```

通过导入`mongoose`和`dotenv`，它们立即调用 config 方法，使我们的连接能够读取环境变量并将它们的内容分配给`process.env`。

我们还创建了一个`database`变量并将其分配给`process.env.MONGOLAB_URI`,后者从我们在`.env`文件中定义的内容中接收其值。第 9 到 11 行使用 Mongoose 方法连接到数据库，`mongoose.connect()`。

用 Ctrl + C 从您的终端停止服务器，并再次运行`npm run develop`。您应该得到这样的响应，让我们知道我们做的一切都是正确的。

![Verifying The Connection In The Terminal](img/159f4f6f4fb27d524a7de49a1d6b5e9c.png)

我们的应用程序现在连接到我们的 MongoDB 数据库。

## 创建用户注册模型

模型是直接与我们的数据库通信的东西。因此，在我们的`model`文件夹中，让我们创建一个`User.js.`文件并输入以下代码:

```
const mongoose = require("mongoose");
const UserSchema = new mongoose.Schema({
  name: {
    type: String,
    required: true,
  },
  email: {
    type: String,
    required: true,
  },
  password: {
    type: String,
    required: true,
  },
 location: {     
   type: String,    
   default: "New York",
   },
  date: {
    type: Date,
    default: Date.now,
  },
});
const User = mongoose.model("User", UserSchema);
module.exports = User;

```

每当新用户通过注册页面注册时，我们都希望将这些字段插入数据库。我们可以存储一个名称、密码、电子邮件地址，给用户一个默认位置纽约，并在注册完成时包含一个时间戳。

### 注册用户

我们已经创建了一个模式，将我们的用户信息存储在数据库中的`model`文件夹内的`User.js`文件夹中。要了解这是如何工作的，让我们在注册页面上发出一个`POST`请求。

每当最终用户点击**注册**按钮，一个`POST`请求就会被发送到`/register`路由(记住我们之前做的是一个`GET`请求*)。为了完成这项工作，我们必须去`loginController.js`并要求`User.js`模型`bycrypt`，因为我们必须散列密码:*

```
const User = require("../models/User");
const bcrypt = require("bcryptjs");

```

接下来，创建一个处理对`/register`路由的`POST`请求的函数。这里，`registerUser`函数使用通过注册页面的表单提交的数据:

```
//js
//Post Request that handles Register
const registerUser = (req, res) => {
  const { name, email, location, password, confirm } = req.body;
  if (!name || !email || !password || !confirm) {
    console.log("Fill empty fields");
  }
  //Confirm Passwords
  if (password !== confirm) {
    console.log("Password must match");
  } else {
    //Validation
    User.findOne({ email: email }).then((user) => {
      if (user) {
        console.log("email exists");
        res.render("register", {
          name,
          email,
          password,
          confirm,
        });
      } else {
        //Validation
        const newUser = new User({
          name,
          email,
          location,
          password,
        });
        //Password Hashing
        bcrypt.genSalt(10, (err, salt) =>
          bcrypt.hash(newUser.password, salt, (err, hash) => {
            if (err) throw err;
            newUser.password = hash;
            newUser
              .save()
              .then(res.redirect("/login"))
              .catch((err) => console.log(err));
          })
        );
      }
    });
  }
};

```

在第 4 行，我们得到用户提交到表单中的所有输入:

```
>const { name, email, location, password, confirm } = req.body;

```

`req.body`是一个 Express API，它通过我们应用程序的前端保存提交的参数。从第 5 行到第 7 行，我们检查是否有任何字段是空的；如果有空的，它记录，`"Fill empty fields"`。

第 8 行到第 10 行，检查密码字段是否匹配；如果它们不匹配，它记录`"Password must match"`。

第 11 到 21 行是一个`else if`语句，只有在前两个条件为`false`时才会发生。它所做的是检查，以确保多个用户没有注册同一个电子邮件地址。

通过检查数据库，如果用户存在相同的电子邮件地址，它将控制台日志`"email exists"`并呈现维护用户输入的注册页面。

通过向`index.js`文件添加以下内容，添加一个内置的 [Express 中间件来解析传入的请求](https://blog.logrocket.com/express-middleware-a-complete-guide/):

```
//js
//BodyParsing
app.use(express.urlencoded({extended: false}));

```

这个内置的 express 中间件使我们能够处理提交的数据并将其存储在`req.body`中。

在我们保存用户信息和散列密码之前，我们还必须检查数据库，以确保永远不会有多个电子邮件地址，因此每个用户的电子邮件都是唯一的。

最后，我们可以导出`registerUser`模块，并将其导入到我们的`login.js`文件中。

![Filled Out Registration Form](img/b652ada59d626e2d9a7842aa36f1a80f.png)

每当用户选择 **Register** 按钮时，如果一切正常，表单会在数据库中创建一个用户实例，并将它们重定向到登录页面。

![Collected User Information Showing In MongoDB, Redirects The User To The Login Page](img/f208bf2ab95d6ca72d0449201e1916a1.png)

## 登录和验证用户

我们已经使用户注册过程工作。现在让我们来看看应用程序的登录部分。

为了确保我们的登录页面工作，我们必须使用 Passport 验证用户。如果数据库中有用户，Passport 会将我们重定向到显示用户详细信息的仪表板。

为了更好地组织，让我们创建一个处理身份验证的文件夹。在我们的根文件夹中创建一个`auth`文件夹，并添加`passport.js` `protect.js`。

![Adding Passport.js And Protect.js Files](img/d586e27731cac7646f3a97a246fda4c3.png)

在`passport.js`文件中，粘贴以下代码:

```
//js
const bcrypt = require("bcryptjs");
LocalStrategy = require("passport-local").Strategy;
//Load model
const User = require("../models/User");
const loginCheck = passport => {
  passport.use(
    new LocalStrategy({ usernameField: "email" }, (email, password, done) => {
      //Check customer
      User.findOne({ email: email })
        .then((user) => {
          if (!user) {
            console.log("wrong email");
            return done();
          }
          //Match Password
          bcrypt.compare(password, user.password, (error, isMatch) => {
            if (error) throw error;
            if (isMatch) {
              return done(null, user);
            } else {
              console.log("Wrong password");
              return done();
            }
          });
        })
        .catch((error) => console.log(error));
    })
  );
  passport.serializeUser((user, done) => {
    done(null, user.id);
  });
  passport.deserializeUser((id, done) => {
    User.findById(id, (error, user) => {
      done(error, user);
    });
  });
};
module.exports = {
  loginCheck,
};

```

在第 2 到 5 行中，我们导入了`bcryptjs`、`passport-local`和我们的`User.js`模型文件。`bcryptjs`将登录表单中输入的明文与数据库中的加密数据进行比较。

第 6 行到第 45 行包含带有参数`passport`的`loginCheck`函数。
在函数内部，我们使用了`LocalStrategy`来检查数据库，看看是否已经有一个现有的电子邮件；如果没有，它会控制`"wrong email"`。

`bcrypt.compare()`然后将输入的密码与数据库中先前加密的密码进行比较。

随着`[passport.serializeUser](http://www.passportjs.org/docs/configure/)` [和](http://www.passportjs.org/docs/configure/) `[passport.deserializeUser](http://www.passportjs.org/docs/configure/)` [配置认证](http://www.passportjs.org/docs/configure/)的引入，如果代码中前面的条件为`false`，Passport 将对用户进行序列化和反序列化。

确认用户后，Passport 会创建一个会话，用户的浏览器会为后续请求维护该会话。

最后，我们导出`loginCheck`。这就是处理我们的认证，但是我们需要添加一些中间件到我们的`index.js`文件来初始化它。

中间件需要来自`./auth/passport.js`的`passport`包和`loginCheck`:

![Pointing To The Passport Packaged Required For LoginCheck](img/d4a3647ae544f38d9c317a500c893baa.png)

在第 22 行和第 23 行，我们在 Express `app`实例中使用了 Passport 提供的中间件来初始化 Passport 并维护一致的会话。

最后，我们必须为`POST`到`/login`的请求创建一个路由。在我们的一个`loginController.js``controller`文件夹里面，在`export`对象的正上方，粘贴下面的代码:

```
//js
const loginUser = (req, res) => {
  const { email, password } = req.body;
  //Required
  if (!email || !password) {
    console.log("Please fill in all the fields");
    res.render("login", {
      email,
      password,
    });
  } else {
    passport.authenticate("local", {
      successRedirect: "/dashboard",
      failureRedirect: "/login",
      failureFlash: true,
    })(req, res);
  }
};

```

这样，我们声明了一个`loginUser`函数*。*在这个函数中，我们查找输入到我们登录页面的电子邮件和密码，并检查密码或电子邮件字段是否为空。

如果其中任何一个为空，我们将控制台`"Please fill in all the fields"`并重新呈现登录页面。

现在，如果没有出现这种情况，并且电子邮件和密码是正确的，我们就让用户登录并重定向到`/dashboard`，但是，我们还没有创建这个路由。

最终确定的`loginController.js`如下:

```
//js
const passport = require("passport");
const User = require("../models/User");
const bcrypt = require("bcryptjs");
//For Register Page
const registerView = (req, res) => {
  res.render("register", {});
};
//Post Request for Register
const registerUser = (req, res) => {
  const { name, email, location, password, confirm } = req.body;
  if (!name || !email || !password || !confirm) {
    console.log("Fill empty fields");
  }
  //Confirm Passwords
  if (password !== confirm) {
    console.log("Password must match");
  } else {
    //Validation
    User.findOne({ email: email }).then((user) => {
      if (user) {
        console.log("email exists");
        res.render("register", {
          name,
          email,
          password,
          confirm,
        });
      } else {
        //Validation
        const newUser = new User({
          name,
          email,
          location,
          password,
        });
        //Password Hashing
        bcrypt.genSalt(10, (err, salt) =>
          bcrypt.hash(newUser.password, salt, (err, hash) => {
            if (err) throw err;
            newUser.password = hash;
            newUser
              .save()
              .then(res.redirect("/login"))
              .catch((err) => console.log(err));
          })
        );
      }
    });
  }
};
// For View
const loginView = (req, res) => {
  res.render("login", {});
};
//Logging in Function
const loginUser = (req, res) => {
  const { email, password } = req.body;
  //Required
  if (!email || !password) {
    console.log("Please fill in all the fields");
    res.render("login", {
      email,
      password,
    });
  } else {
    passport.authenticate("local", {
      successRedirect: "/dashboard",
      failureRedirect: "/login",
      failureFlash: true,
    })(req, res);
  }
};
module.exports = {
  registerView,
  loginView,
  registerUser,
  loginUser,
};

```

## 仪表板会话和注销

用户通过身份验证并且每个登录信息都正确无误后，让我们看看将显示用户姓名和位置的仪表板路径。我们必须防止未经授权的用户使用这条路线。在我们的`project.js`文件内的`auth`文件夹中，粘贴以下代码:

```
//js
const protectRoute = (req, res, next) =>{
  if (req.isAuthenticated()) {
    return next();
  }
  console.log('Please log in to continue');
  res.redirect('/login');
}
const allowIf = (req, res, next) =>{
  if (!req.isAuthenticated()) {
    return next();
  }
  res.redirect('/dashboard');      
}
module.exports = {
    protectRoute,
    allowIf,
  };

```

如果用户试图在没有通过登录页面登录进行身份验证的情况下访问仪表板，则`protectRoute`功能会将用户重定向到登录页面。

导出函数后，让我们首先向`dashboard.ejs`添加一些代码来定义我们想要保护的路线。

在`views`下的`dashboard.ejs`中，添加以下代码:

```
//ejs
<%- include('layout.ejs'); %>
<div class="container">
    <div class="row justify-content-center">
        <h3>My name is <%= user.name %> </h3> &nbsp;
        <h3>I stay at <%= user.location %> </h3> <br>
    </div>
</div>
<div>
    <a href="/logout"> <button type="submit" class="btn btn-primary btn-lg btn-block login-button">Logout</button> </a>
</div>
</body>
</html>

```

这只是使用了`layout.ejs`，提供了一些使用`.ejs`模板显示的动态用户信息和一个注销按钮。

为了有序，创建另一个`controller`文件。因此，在`controllers`文件夹中，创建`dashboardController.js`文件并添加以下代码:

```
//For Register Page
const dashboardView = (req, res) => {
  res.render("dashboard", {
    user: req.user
  });
};
module.exports = {
  dashboardView,
};

```

在这里，我们将`dashboard.ejs`呈现为视图，并访问`req.user`，一旦有一个由`expressjs`建立的活动会话，就可以使用这个视图。至此，我们已经成功登录到控制面板。

然后，在我们的`routes`下的`login.js`文件夹中，要求我们从`auth`下的`protect.js`导出的`protectRoute`:

```
const { protectRoute } = require("../auth/protect");

```

接下来，要求`controllers`下的`dashboardController.js`:

```
const { dashboardView } = require("../controllers/dashboardController");

```

最后，添加受保护的仪表板路线以通过`dashboardView`返回`dashboard.ejs`:

```
router.get("/dashboard", protectRoute, dashboardView);

```

我们的`routes`下的`login.js`应该是这样的:

![Login.js File Under Routes File](img/94290baf2a7125eb90e4242725848fc1.png)

需要一个最终的 Express 中间件来初始化会话。在这种情况下，我们可以使用`[express-session](https://www.npmjs.com/package/express-session)`。要安装，请运行:

```
npm i express-session

```

安装成功后，在我们的 express `app`实例后的`index.js`文件中需要它:

```
const session = require('express-session');

```

然后，在初始化您的 Passport 实例之前添加它。你可以[在这里找到完整的代码](https://github.com/bigpreshy/mvc/blob/main/index.js):

```
app.use(session({
    secret:'oneboy',
    saveUninitialized: true,
    resave: true
  }));

```

这将创建一个会话。注意，根据 Express 文档，“会话数据并不保存在 cookie 本身中，只有会话 ID。会话数据存储在服务器端”。

现在，当我们使用正确的电子邮件和密码登录时，我们应该会看到:

![Final Login Page With Name Displayed And Logout Button](img/7baba96c4a5cb222f4387d0088a3ac2f.png)

## 结论

恭喜你！你已经看透了。我希望您喜欢并学到了很多关于如何使用 MVC 架构模式构建下一个应用程序的知识。

我们能够阐明模型、视图和控制器的含义，并遵循该模式使用 Express.js 和 Passport 进行身份验证来构建用户注册和登录系统。您还看到了我们如何将所有这些与 MongoDB 数据库放在一起。

注意，使用这种结构，您可以将其扩展到您想要创建的任何应用程序，包括完整的应用程序。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.