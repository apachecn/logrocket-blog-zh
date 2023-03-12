# 使用 TypeDI - LogRocket Blog 在 Node.js 中进行依赖注入

> 原文：<https://blog.logrocket.com/dependency-injection-node-js-typedi/>

依赖注入是面向对象编程中的一个基本概念。这是一种将对象的创建与使用分离的方法。在本文中，我们将学习什么是依赖注入，以及如何使用 [TypeDI 库](https://docs.typestack.community/typedi/v/develop/01-getting-started)在 Node.js 应用程序中使用它。

向前跳:

## 依赖注入

依赖注入是一种设计模式，它允许我们将依赖注入到类中，而不是在类中创建依赖实例。

依赖注入可以帮助我们:

*   编写灵活的类
*   轻松测试我们的代码
*   减少样板代码的数量
*   提高我们代码的可读性

因此，很清楚为什么依赖注入对您的应用程序来说是一件好事，但是我们如何才能做到呢？

首先，让我们看看依赖注入解决了什么痛点。

想象一个返回用户列表的标准 API。请求流有三个步骤:

1.  请求将到达`Controller`，它处理所有的路由
2.  `Controller`将调用处理所有业务逻辑的`Service`
3.  `Service`将调用`Repository`，后者处理所有的数据库调用

```
UserController -> UserService -> UserRepository
```

所以`Controller`取决于`Service`，`Service`取决于`Repository`。这是 Node.js 应用程序中典型的依赖流。

如果我们查看代码，我们可以看到,`Controller`创建了`UserService`的实例，而`Service`创建了`Repository`的实例。

服务类将如下所示:

```
import { UserRepository } from "./UserRepository";

export class UserService {
  userRepo: UserRepository;

  constructor() {
    this.userRepo = new UserRepository();
  }

  getUserData = () => {
    this.userRepo.getAll();
  };
}

```

控制器看起来会像这样:

```
import { UserService } from "./UserService";

export class UserController {
  userService: UserService;

  constructor() {
    this.userService = new UserService();
  }

  getUserData = () => {
    this.userService.getUserData();
  };
}

```

现在，如果我们想使用`UserService`类，我们必须在`UserService`类中创建`UserRepository`的实例。

在另一个类中创建一个类实例不是一个好的实践，因为现在，这两个类(即`UserRepository`和`UserService`)有紧密的耦合。

假设我们想要测试我们的`UserService`类。我们希望我们的测试代码与实际的数据库交互吗？

不—我们想模拟数据库调用并测试我们的`UserService`类。否则:

*   我们将不得不创建一个测试数据库
*   我们的测试套件将依赖于数据库。因此，如果数据库出现问题，您的测试套件也会出现问题
*   测试套件将会非常慢

所以我们需要一种方法将`UserRepository`的实例注入到`UserService`类中。这就是依赖注入发挥作用的地方。

## 用容器实现依赖注入

实现依赖注入最常见的方式是使用依赖注入容器。

我们可以创建一个全局容器对象来保存依赖关系的所有实例，并且我们可以将依赖关系注入到类中。

注入依赖项的最常见方式是使用构造函数。我们可以使用我们的`UserService`类的构造函数来注入`UserRepository`类的实例。

我们的类看起来会像这样:

```
import { UserRepository } from "./UserRepository";

export class UserService {
  userRepo: UserRepository;

  constructor(userRepo: UserRepository) {
    this.userRepo = userRepo;
  }

  getUserData = () => {
    this.userRepo.getAll();
  };
}

```

现在我们可以将`UserRepository`的实例传递给`UserService`类。你猜怎么着？

当我们测试`UserService`类时，我们可以将`UserRepository`的模拟实例传递给`UserService`类并测试它:

```
import { UserService } from "./UserService";
import { UserRepository } from "./UserRepository";

const mockUserRepo = {
  getAll: jest.fn(),
};

const userService = new UserService(mockUserRepo);

userService.getUserData();

expect(mockUserRepo.getAll).toHaveBeenCalled();

```

我们仍然必须创建`UserRepository`类的实例，并将其注入到`UserService`类中，每当我们想要使用`UserService`类时，我们都必须这样做。但是我们不想每次都这样做——只做一次。

让我们看看如何实现这一点。

## 使用 TypeDI 实现 Node.js 中的依赖注入

Node.js 中实现依赖注入有多种方式，我们可以创建自己的依赖容器，自己创建实例，或者注入运行时。

但是在 Node 中实现依赖注入有更好的方法。它是通过使用一个名为 [TypeDI](https://docs.typestack.community/typedi/01-getting-started) 的库来实现的，这个库支持多个 DI 容器，非常灵活和快速，并且简单易用。

依赖注入还有其他一些流行的选项，比如 [inversify](https://inversify.io/) 和 [awilix](https://github.com/jeffijoe/awilix) ，但是我发现 TypeDI 比其他的要干净得多。

## 启动项目

如果您已经有一个现有的 Express 项目，可以跳过这一步。否则，您可以使用以下命令用 Express.js 和 TypeScript 构建一个样板项目。

```
git clone https://github.com/Mohammad-Faisal/express-typescript-skeleton-boilerplate

```

首先，让我们在节点应用程序中安装依赖项。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
npm install typedi reflect-metadata

```

然后，修改我们的`tsconfig.json`文件以正确地使用`typedi`。在`compilerOptions`下增加以下三个选项:

```
"experimentalDecorators": true,
"emitDecoratorMetadata": true,
"strictPropertyInitialization": false // this one is for preventing the typescript 
                                         errors while using @Inject()

```

现在，在我们的应用程序的开始导入`reflect-metadata`，如下所示，在`index.ts`文件中:

```
import "reflect-metadata";

```

这将解决`reflect-metadata shim is required when using class decorators`错误。

基于用例，有多种方法可以使用 TypeDI。让我们看看其中的几个。

### 从全局容器中获取

我们可以从全局容器中获取`UserRepository`的实例。这是对 TypeDI 的直接使用:

```
import { UserRepository } from "./UserRepository";
import { Service, Inject, Container } from "typedi";

@Service()
export class UserService {
  getUserData = () => {
    const userRepo = Container.get(UserRepository);
    userRepo.getAll();
  };
}

```

但是你必须用`@Service()`装饰器来标记`UserRepository`类。否则，您将得到一个错误:

```
import { Service } from "typedi";

@Service()
export class UserRepository {
  getAll = () => {
    console.log("Getting all the users");
  };
}

```

你可能想知道为什么我们在这里使用`@Service()`装饰器。

`@Service()`装饰器用于在全局容器中将`UserRepository`注册为服务，这样我们就可以从全局容器中获得`UserRepository`的实例。

现在，当我们调用`Container.get(UserRepository)`时，它将返回`UserRepository`类的实例。

### 注入`UserRepository`的实例

我们还可以使用`@Inject()`装饰器将`UserRepository`的实例注入到`UserService`类中:

```
import { UserRepository } from "./UserRepository";
import { Service, Inject, Container } from "typedi";

@Service()
export class UserService {
  @Inject()   // <- notice here
  userRepo: UserRepository;

  getUserData = () => {
    this.userRepo.getAll();
  };
}

```

现在我们不必使用`Container.get(UserRepository)`来获得`UserRepository`类的实例。我们可以直接使用`this.userRepo`来访问`UserRepository`类的实例。

### 使用构造函数注入依赖项

我们可以使用类的构造函数注入依赖关系:

```
import { UserRepository } from "./UserRepository";
import { Service, Inject } from "typedi";

@Service()
export class UserService {
  userRepo: UserRepository;

  constructor(@Inject() userRepo: UserRepository) {
    this.userRepo = userRepo;
  }

  logUserData = () => {
    this.userRepo.someFunction();
  };
}

```

这是向类中注入依赖关系的一种非常常见的方式。它遵循依赖注入模式。

这就是如何使用 TypeDI 在 Node.js 中实现依赖注入。

## TypeDI 的其他优势

使用 TypeDI 库还有其他好处。我们可以使用`Container`类在应用程序中设置全局变量。

首先，我们必须设置需要跨应用程序访问的变量:

```
import 'reflect-metadata';
import { Container, Token } from 'typedi';

export const SOME_GLOBAL_CONFIG_VALUE = new Token<string>('SOME_CONFIG');
Container.set(SOME_GLOBAL_CONFIG_VALUE, 'very-secret-value');

```

现在，如果我们在应用程序中的任何地方需要这个值，我们可以使用下面这段代码:

```
import { Container, Token } from 'typedi';

const MY_SECRET = Container.get(SOME_GLOBAL_CONFIG_VALUE);

```

这也是非常类型安全的，因为`Token`是类型化的。

多酷啊。

## 结论

谢谢你读到这里。今天，我演示了 Node.js 应用程序上下文中的依赖注入。我们还在一个实际项目中学会了使用 TypeDI 库实现依赖注入。要了解更多信息，请查看这个项目的 [TypeDI 文档](https://docs.typestack.community/typedi/v/develop/01-getting-started)和 [GitHub 库](https://github.com/Mohammad-Faisal/dependency-injection-nodejs)。

我希望你今天学到了新东西。祝你今天休息愉快！

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.