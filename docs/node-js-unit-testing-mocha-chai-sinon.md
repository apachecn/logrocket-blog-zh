# 使用 Mocha、Chai 和 Sinon 进行 Node.js 单元测试

> 原文：<https://blog.logrocket.com/node-js-unit-testing-mocha-chai-sinon/>

***编者按**:本文最后更新于 2023 年 1 月 5 日，以确保在撰写本文时所有信息都与摩卡、柴和西农的最新版本兼容。*

测试有助于记录应用程序的核心特性。正确编写的测试确保新特性不会引入破坏应用程序的变化。

维护代码库的工程师不一定就是编写初始代码的工程师。如果代码经过适当的测试，另一个工程师可以放心地添加新代码或修改现有代码，并期望新的更改不会破坏其他功能，或者至少不会对其他功能产生副作用。

JavaScript 和 Node.js 有很多测试和断言库，像 [Jest](https://jestjs.io/) 、 [Jasmine](https://jasmine.github.io/) 、 [Qunit](https://qunitjs.com/) 和 [Mocha](https://mochajs.org/) 。在本文中，我们将了解如何使用 [Mocha](https://mochajs.org/) 进行测试，使用 [Chai](https://www.chaijs.com/) 进行断言，使用 [Sinon](https://sinonjs.org/) 进行模仿、间谍和存根。

向前跳:

*   什么是单元测试？
*   摩卡
*   为什么要用摩卡？
*   柴
*   西农
*   构建示例节点应用程序

## 什么是单元测试？

单元测试是检查当与应用程序的其他组件分离时，函数是否按预期执行的代码片段。单元测试允许我们测试应用程序中的不同功能。考虑为您的应用程序编写单元测试有几个原因:

1.  单元测试确保您的代码在多种情况下都能按预期运行
2.  单元测试还可以帮助您在开发周期的早期找到损坏的代码
3.  因为任何测试失败都会暴露出有缺陷的代码，所以编写单元测试会灌输一定程度的信任。一旦每个测试都通过了，你可以确定你的代码是有效的

## 摩卡

[Mocha](https://mochajs.org/) 是一个功能丰富的 JavaScript 测试框架，运行在 Node.js 和浏览器中。它将测试封装在测试套件(`describe`块)和测试用例(`it`块)中。

摩卡有很多有趣的特点:

*   浏览器支持
*   简单的异步支持，包括承诺
*   测试覆盖报告
*   异步测试超时支持
*   `before`、`after`、`beforeEach`、`afterEach`挂钩等。

## 为什么要用摩卡？

Mocha 已经使用了很多年，是一个成熟的测试工具。它得到了很好的支持，并拥有相当大的用户群。就易用性而言，Mocha 比其他一些测试工具要复杂一些，但是如果使用得当，Mocha 会非常强大。因为 Mocha 是一个开发得更好的产品，有更大的贡献者社区，所以它提供了更多现成的功能。

## 柴

要检查相等性或将预期结果与实际结果进行比较，我们可以使用 Node.js 内置断言模块。然而，当错误发生时，测试用例仍然会通过。所以 Mocha 建议使用其他断言库。在本教程中，我们将使用[柴](https://www.chaijs.com/)。

柴公开了三个断言接口:`expect()`、`assert()`、`should()`。它们中的任何一个都可以用于断言。

## 不然呢

通常，被测试的方法需要与其他外部方法交互或调用其他外部方法。因此，您需要一个实用程序来探查、存根或模仿这些外部方法。这正是 [Sinon](https://sinonjs.org/) 为你做的。

Stubs、mocks 和 spies 使得测试更加健壮，并且在相关代码发展或内部修改时不容易出错。

### 间谍

一个 [spy](https://sinonjs.org/releases/latest/spies/) 是一个伪函数，它跟踪所有它调用的以下内容:

1.  争论
2.  返回值
3.  `this`的值
4.  引发的异常(如果有)

### 烟蒂

一个[存根](https://sinonjs.org/releases/latest/stubs/)是一个有预定行为的间谍。

我们可以使用存根来:

*   采取预先确定的动作，比如抛出异常
*   提供预定的响应
*   防止直接调用特定的方法(特别是当它触发了不希望的行为，如 HTTP 请求)

### 模拟的

一个[模拟](https://sinonjs.org/releases/latest/mocks/)是一个伪函数(像一个间谍),具有预编程的行为(像一个存根)和预编程的期望。

我们可以使用模拟来:

*   验证被测代码和它调用的外部方法之间的契约
*   验证外部方法被调用的次数是否正确
*   验证是否使用正确的参数调用了外部方法

模拟的经验法则是:如果你不打算为某个特定的调用添加断言，就不要模拟它。请改用存根。

## 构建示例节点应用程序

为了演示我们上面解释的内容，我们将构建一个简单的节点应用程序来创建和检索用户。本文的完整代码示例可以在 [CodeSandbox](https://codesandbox.io/s/github/GodwinEkuma/mocha-chai-unit-test) 上找到。

### 项目设置

让我们为我们的用户应用程序项目创建一个新的项目目录:

```
mkdir mocha-unit-test && cd mocha-unit-test
mkdir src

```

在源文件夹中创建一个`package.json`文件，并添加以下代码:

```
// src/package.json
{
  "name": "mocha-unit-test",
  "version": "1.0.0",
  "description": "",
  "main": "app.js",
  "scripts": {
    "test": "mocha './src/**/*.test.js'",
    "start": "node src/app.js"
  },
  "keywords": [
    "mocha",
    "chai"
  ],
  "author": "Godwin Ekuma",
  "license": "ISC",
   "dependencies": {
    "dotenv": "^6.2.0",
    "express": "^4.18.2",
    "jsonwebtoken": "^8.5.1",
    "morgan": "^1.10.0",
    "mysql2": "^2.3.3",
    "pg": "^7.18.2",
    "pg-hstore": "^2.3.4",
    "sequelize": "^5.22.5"
  },
  "devDependencies": {
    "chai": "^4.3.7",
    "faker": "^4.1.0",
    "mocha": "^10.2.0",
    "sinon": "^15.0.1"
  }
}

```

运行`npm install`来安装项目依赖项。

注意，测试相关的包`mocha`、`chai`、`sinon`和`faker`保存在开发依赖项中。

`test`脚本使用一个定制的 [glob](https://en.wikipedia.org/wiki/Glob_(programming)) ( `./src/**/*.test.js`)来配置测试文件的文件路径。Mocha 将在`src`文件夹的目录和子目录中寻找测试文件(以`.test.js`结尾的文件)。

### 储存库、服务和控制器

我们将使用[控制器、服务和](https://stackoverflow.com/questions/40911731/what-are-repositories-services-and-actions-controllers)、[存储库](https://stackoverflow.com/questions/40911731/what-are-repositories-services-and-actions-controllers)模式来构建我们的应用程序，因此我们的应用程序将被分成存储库、服务和控制器。这种模式将应用程序的业务层分成三个不同的层:

*   repository 类处理数据进出我们的数据存储。存储库用于服务层和模型层之间。例如，在`UserRepository`中，您将创建向数据库写入/从数据库读取用户的方法
*   服务类调用存储库类，并可以组合它们的数据来形成新的、更复杂的业务对象。它是控制器和存储库之间的抽象。例如，`UserService`将负责执行所需的逻辑，以便创建一个新用户
*   控制器包含很少的逻辑，用于调用服务。除非有正当的理由，否则控制器很少直接调用存储库。控制器将对从服务返回的数据执行基本检查，以便将响应发送回客户端

以这种方式分解应用程序使测试变得容易。

### `UserRepository`类

让我们从创建一个存储库类开始:

```
// src/user/user.repository.js
const { UserModel } = require("../database");
class UserRepository {
  constructor() {
    this.user = UserModel;
    this.user.sync({ force: true });
  }
  async create(name, email) {
    return this.user.create({
      name,
      email
    });
  }
  async getUser(id) {
    return this.user.findOne({ id });
  }
}
module.exports = UserRepository;

```

`UserRepository`类有两个方法:`create`和`getUser`。`create`方法向数据库添加新用户，而`getUser`从数据库中搜索用户。

让我们测试下面的`userRepository`方法:

```
// src/user/user.repository.test.js
const chai = require("chai");
const sinon = require("sinon");
const expect = chai.expect;
const faker = require("faker");
const { UserModel } = require("../database");
const UserRepository = require("./user.repository");
describe("UserRepository", function() {
  const stubValue = {
    id: faker.random.uuid(),
    name: faker.name.findName(),
    email: faker.internet.email(),
    createdAt: faker.date.past(),
    updatedAt: faker.date.past()
  };
  describe("create", function() {
    it("should add a new user to the db", async function() {
      const stub = sinon.stub(UserModel, "create").returns(stubValue);
      const userRepository = new UserRepository();
      const user = await userRepository.create(stubValue.name, stubValue.email);
      expect(stub.calledOnce).to.be.true;
      expect(user.id).to.equal(stubValue.id);
      expect(user.name).to.equal(stubValue.name);
      expect(user.email).to.equal(stubValue.email);
      expect(user.createdAt).to.equal(stubValue.createdAt);
      expect(user.updatedAt).to.equal(stubValue.updatedAt);
    });
  });
});

```

上面的代码是在测试`UserRepository`的`create`方法。注意，我们放弃了`UserModel.create`方法。存根是必要的，因为我们的目标是测试存储库而不是模型。我们使用 [`faker`](https://github.com/faker-js/faker) 作为测试夹具:

```
// src/user/user.repository.test.js

const chai = require("chai");
const sinon = require("sinon");
const expect = chai.expect;
const faker = require("faker");
const { UserModel } = require("../database");
const UserRepository = require("./user.repository");

describe("UserRepository", function() {
  const stubValue = {
    id: faker.random.uuid(),
    name: faker.name.findName(),
    email: faker.internet.email(),
    createdAt: faker.date.past(),
    updatedAt: faker.date.past()
  };
   describe("getUser", function() {
    it("should retrieve a user with specific id", async function() {
      const stub = sinon.stub(UserModel, "findOne").returns(stubValue);
      const userRepository = new UserRepository();
      const user = await userRepository.getUser(stubValue.id);
      expect(stub.calledOnce).to.be.true;
      expect(user.id).to.equal(stubValue.id);
      expect(user.name).to.equal(stubValue.name);
      expect(user.email).to.equal(stubValue.email);
      expect(user.createdAt).to.equal(stubValue.createdAt);
      expect(user.updatedAt).to.equal(stubValue.updatedAt);
    });
  });
});

```

为了测试`getUser`方法，我们还必须存根`UserModel.findone`。我们使用`expect(stub.calledOnce).to.be.true`来断言存根至少被调用一次。其他断言检查由`getUser`方法返回的值。

### `UserService`类

```
// src/user/user.service.js

const UserRepository = require("./user.repository");
class UserService {
  constructor(userRepository) {
    this.userRepository = userRepository;
  }
  async create(name, email) {
    return this.userRepository.create(name, email);
  }
  getUser(id) {
    return this.userRepository.getUser(id);
  }
}
module.exports = UserService;

```

`UserService`类也有两个方法`create`和`getUser`。`create`方法调用`create`存储库方法，传递新用户的名字和电子邮件作为参数。`getUser`调用存储库`getUser`方法。

让我们测试下面的`userService`方法:

```
// src/user/user.service.test.js

const chai = require("chai");
const sinon = require("sinon");
const UserRepository = require("./user.repository");
const expect = chai.expect;
const faker = require("faker");
const UserService = require("./user.service");
describe("UserService", function() {
  describe("create", function() {
    it("should create a new user", async function() {
      const stubValue = {
        id: faker.random.uuid(),
        name: faker.name.findName(),
        email: faker.internet.email(),
        createdAt: faker.date.past(),
        updatedAt: faker.date.past()
      };
      const userRepo = new UserRepository();
      const stub = sinon.stub(userRepo, "create").returns(stubValue);
      const userService = new UserService(userRepo);
      const user = await userService.create(stubValue.name, stubValue.email);
      expect(stub.calledOnce).to.be.true;
      expect(user.id).to.equal(stubValue.id);
      expect(user.name).to.equal(stubValue.name);
      expect(user.email).to.equal(stubValue.email);
      expect(user.createdAt).to.equal(stubValue.createdAt);
      expect(user.updatedAt).to.equal(stubValue.updatedAt);
    });

//test a case of no user
    it("should return an empty object if no user matches the provided id", async function() {
      const stubValue = {};
      const userRepo = new UserRepository();
      const stub = sinon.stub(userRepo, "getUser").returns(stubValue);
      const userService = new UserService(userRepo);
      const user = await userService.getUser(1);
      expect(stub.calledOnce).to.be.true;
      expect(user).to.deep.equal({})
    });
  });
});

```

上面的代码是在测试`UserService create`方法。我们已经为存储库`create`方法创建了一个存根。下面的代码将测试`getUser`服务方法:

```
const chai = require("chai");
const sinon = require("sinon");
const UserRepository = require("./user.repository");
const expect = chai.expect;
const faker = require("faker");
const UserService = require("./user.service");
describe("UserService", function() {
  describe("getUser", function() {
    it("should return a user that matches the provided id", async function() {
      const stubValue = {
        id: faker.random.uuid(),
        name: faker.name.findName(),
        email: faker.internet.email(),
        createdAt: faker.date.past(),
        updatedAt: faker.date.past()
      };
      const userRepo = new UserRepository();
      const stub = sinon.stub(userRepo, "getUser").returns(stubValue);
      const userService = new UserService(userRepo);
      const user = await userService.getUser(stubValue.id);
      expect(stub.calledOnce).to.be.true;
      expect(user.id).to.equal(stubValue.id);
      expect(user.name).to.equal(stubValue.name);
      expect(user.email).to.equal(stubValue.email);
      expect(user.createdAt).to.equal(stubValue.createdAt);
      expect(user.updatedAt).to.equal(stubValue.updatedAt);
    });
  });
});

```

我们再次放弃了`UserRepository getUser`方法。我们还断言存根至少被调用一次，然后断言方法的返回值是正确的。

### `UserController`类

```
/ src/user/user.controller.js

class UserController {
  constructor(userService) {
    this.userService = userService;
  }
  async register(req, res, next) {
    const { name, email } = req.body;
    if (
      !name ||
      typeof name !== "string" ||
      (!email || typeof email !== "string")
    ) {
      return res.status(400).json({
        message: "Invalid Params"
      });
    }
    const user = await this.userService.create(name, email);
    return res.status(201).json({
      data: user
    });
  }
  async getUser(req, res) {
    const { id } = req.params;
    const user = await this.userService.getUser(id);
    return res.json({
      data: user
    });
  }
}
module.exports = UserController;

```

`UserController`类也有`register`和`getUser`方法。这些方法中的每一个都接受两个参数`req`和`res`对象:

```
// src/user/user.controller.test.js

describe("UserController", function() {
  describe("register", function() {
    let status json, res, userController, userService;
    beforeEach(() => {
      status = sinon.stub();
      json = sinon.spy();
      res = { json, status };
      status.returns(res);
      const userRepo = sinon.spy();
      userService = new UserService(userRepo);
    });
    it("should not register a user when name param is not provided", async function() {
      const req = { body: { email: faker.internet.email() } };
      await new UserController().register(req, res);
      expect(status.calledOnce).to.be.true;
      expect(status.args\[0\][0]).to.equal(400);
      expect(json.calledOnce).to.be.true;
      expect(json.args\[0\][0].message).to.equal("Invalid Params");
    });
    it("should not register a user when name and email params are not provided", async function() {
      const req = { body: {} };
      await new UserController().register(req, res);
      expect(status.calledOnce).to.be.true;
      expect(status.args\[0\][0]).to.equal(400);
      expect(json.calledOnce).to.be.true;
      expect(json.args\[0\][0].message).to.equal("Invalid Params");
    });
    it("should not register a user when email param is not provided", async function() {
      const req = { body: { name: faker.name.findName() } };
      await new UserController().register(req, res);
      expect(status.calledOnce).to.be.true;
      expect(status.args\[0\][0]).to.equal(400);
      expect(json.calledOnce).to.be.true;
      expect(json.args\[0\][0].message).to.equal("Invalid Params");
    });
    it("should register a user when email and name params are provided", async function() {
      const req = {
        body: { name: faker.name.findName(), email: faker.internet.email() }
      };
      const stubValue = {
        id: faker.random.uuid(),
        name: faker.name.findName(),
        email: faker.internet.email(),
        createdAt: faker.date.past(),
        updatedAt: faker.date.past()
      };
      const stub = sinon.stub(userService, "create").returns(stubValue);
      userController = new UserController(userService);
      await userController.register(req, res);
      expect(stub.calledOnce).to.be.true;
      expect(status.calledOnce).to.be.true;
      expect(status.args\[0\][0]).to.equal(201);
      expect(json.calledOnce).to.be.true;
      expect(json.args\[0\][0].data).to.equal(stubValue);
    });
  });
});

```

在前三个`it`模块中，我们测试了当一个或两个必需的参数(电子邮件和姓名)未提供时，将不会创建用户。请注意，我们正在踩断`res.status`并监视`res.json`:

```
describe("UserController", function() {
  describe("getUser", function() {
    let req;
    let res;
    let userService;
    beforeEach(() => {
      req = { params: { id: faker.random.uuid() } };
      res = { json: function() {} };
      const userRepo = sinon.spy();
      userService = new UserService(userRepo);
    });
    it("should return a user that matches the id param", async function() {
      const stubValue = {
        id: req.params.id,
        name: faker.name.findName(),
        email: faker.internet.email(),
        createdAt: faker.date.past(),
        updatedAt: faker.date.past()
      };
      const mock = sinon.mock(res);
      mock
        .expects("json")
        .once()
        .withExactArgs({ data: stubValue });
      const stub = sinon.stub(userService, "getUser").returns(stubValue);
      userController = new UserController(userService);
      const user = await userController.getUser(req, res);
      expect(stub.calledOnce).to.be.true;
      mock.verify();
    });
  });
});

```

对于`getUser`测试，我们模拟了`json`方法。注意，在创建新的`UserService`实例时，我们还必须使用一个间谍`UserRepository`。

## 结论

使用以下命令运行测试:

```
npm test

```

您应该看到测试通过了:

![unit tests passing in mocha chai](img/0b9dcac116794ae1e804da11a20d68d4.png)

我们已经看到了如何使用 [Mocha](https://mochajs.org/) 、 [Chai](https://www.chaijs.com) 和 [Sinon](https://sinonjs.org) 的组合来为节点应用程序创建健壮的测试。请务必查阅它们各自的文档，以拓宽您对这些工具的了解。有问题或意见吗？请把它们放在下面的评论区。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.