# 5 大类型脚本依赖注入容器

> 原文：<https://blog.logrocket.com/top-five-typescript-dependency-injection-containers/>

作为一名以 Java 开始职业生涯的软件开发人员，我在向 JavaScript 过渡的过程中遇到了一些问题。最初的环境缺乏静态类型系统，并且实际上不支持容器化的依赖注入，这导致我编写的代码容易出现明显的错误并且几乎不可测试。

TypeScript 的编译时类型系统改变了这一切，允许复杂项目的持续开发。它支持设计模式的重新出现，如依赖注入，在对象构造期间正确地键入和传递依赖，这促进了更结构化的编程，并有助于编写测试，而无需猴子修补。

在本文中，我们将回顾五种用于在 TypeScript 中编写依赖注入系统的容器化依赖注入工具。我们开始吧！

### 先决条件

要阅读本文，您应该熟悉以下概念:

*   控制反转(IoC):一种设计模式，规定框架应该调用用户域代码，而不是用户域代码调用库代码
*   依赖注入(DI):IoC 的一种变体，其中对象接收其他对象作为依赖，而不是构造函数或设置函数
*   Decorators:支持组合的函数，可包装在类、函数、方法、访问器、属性和参数周围
*   Decorator 元数据:一种在运行时通过使用 decorator 定义目标来存储语言结构配置的方法

## 显式注入依赖关系

接口允许开发人员将抽象需求从实际实现中分离出来，这对于编写测试非常有帮助。请注意，接口只定义功能，不定义依赖关系。最后，接口不会留下运行时痕迹，但是类会。

让我们考虑三个接口示例:

```
export interface Logger {
    log: (s: string) => void;
}

export interface FileSystem<D> {
    createFile(descriptor: D, buffer: Buffer): Promise<void>;
    readFile(descriptor: D): Promise<Buffer>;
    updateFile(descriptor: D, buffer: Buffer): Promise<void>;
    deleteFile(descriptor: D): Promise<void>;
}

export interface SettingsService {
    upsertSettings(buffer: Buffer): Promise<void>;
    readSettings(): Promise<Buffer>;
    deleteSettings(): Promise<void>;
}

```

`Logger`接口抽象了同步日志记录，而通用的`FileSystem`接口抽象了文件 CRUD 操作。最后，`SettingsService`接口提供了设置管理的业务逻辑抽象。

我们可以推断，`SettingsService`的任何实现都依赖于`Logger`和`FileSystem`接口的某些实现。例如，我们可以创建一个`ConsoleLogger`类来将日志打印到控制台输出，创建一个`LocalFileSystem`来管理本地磁盘上的文件，或者创建一个`SettingsTxtService`类来将应用程序设置写入一个`settings.txt`文件。

依赖关系可以使用特殊函数显式传递:

```
export class ConsoleLogger implements Logger {
    // ...
}

export class LocalFileSystem implements FileSystem<string> {
    // ...
}

export class SettingsTxtService implements SettingsService {
    protected logger!: Logger;
    protected fileSystem!: FileSystem<string>;

    public setLogger(logger: SettingsTxtService["logger"]): void {
        this.logger = logger;
    }

    public setFileSystem(fileSystem: SettingsTxtService["fileSystem"]): void {
        this.fileSystem = fileSystem;
    }

    // ...
}

const logger = new ConsoleLogger();
const fileSystem = new LocalFileSystem();
const settingsService = new SettingsTxtService();

settingsService.setLogger(logger);
settingsService.setFileSystem(fileSystem);

```

`SettingsTxtService`类不依赖于像`ConsoleLogger`或`LocalFileSystem`这样的实现。相反，它依赖于前面提到的接口，`Logger`和`FileSystem<string>`。

然而，显式管理依赖关系对每个 DI 容器来说都是一个问题，因为接口在运行时并不存在。

### 依赖图

任何系统的大多数可注射成分都依赖于其他成分。你应该能在任何时候画出它们的图形，一个考虑周全的系统的图形将是无环的。根据我的经验，循环依赖是一种代码味道，而不是一种模式。

项目变得越复杂，依赖图也变得越复杂。换句话说，显式管理依赖关系并不能很好地扩展。我们可以通过自动化依赖管理来解决这个问题，这使得它是隐式的。为此，我们需要阿迪集装箱。

## 依赖注入容器

阿迪集装箱要求如下:

*   `ConsoleLogger`类与`Logger`接口的关联
*   `LocalFileSystem`类与`FileSystem<string>`接口的关联
*   `SettingsTxtService`对`Logger`和`FileSystem<string>`接口的依赖性

## 类型绑定

在运行时将特定类型或类绑定到特定接口有两种方式:

*   指定将实现绑定到它的名称或标记
*   将接口提升到抽象类并允许后者留下运行时跟踪

例如，我们可以使用容器的 API 明确声明`ConsoleLogger`类与`logger`令牌相关联。或者，我们可以使用一个类级装饰器，它接受令牌名作为它的参数。装饰者将使用容器的 API 来注册绑定。

如果`Logger`接口变成一个抽象类，我们可以对它和它的所有派生类应用一个类级装饰器。这样，装饰者将调用容器的 API 来跟踪运行时的关联。

## 解决依赖关系

在运行时解决依赖关系有两种方法:

*   在对象构造期间传递所有依赖关系
*   在对象构造后使用 setters 和 getters 传递所有依赖项

我们将关注第一个选项。阿迪容器负责实例化和维护每个组件的生命周期。因此，容器需要知道在哪里注入依赖项。

我们有两种方式来提供这些信息:

1.  使用能够调用 DI 容器的 API 的构造函数参数装饰器
2.  直接使用 DI 容器的 API 来通知它依赖关系

尽管装饰器和元数据，如 [Reflect API](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Reflect) ，是实验性的特性，但它们在使用 DI 容器时减少了开销。

现在，让我们看看五种流行的依赖注入容器。请注意，本教程中使用的顺序反映了 DI 在应用于 TypeScript 社区时是如何发展成一种模式的。

### 键入注入

[类型注入](https://github.com/nicojs/typed-inject)项目关注类型安全和显式。它既不使用 decorator 也不使用 decorator 元数据，而是选择手工声明依赖关系。它允许存在多个 DI 容器，并且依赖关系的范围要么是单例对象，要么是瞬态对象。

下面的代码片段概述了从上下文 DI 到类型化注入 DI 的转换，这在前面的代码片段中有所展示:

```
export class TypedInjectLogger implements Logger {
    // ...
}
export class TypedInjectFileSystem implements FileSystem<string> {
    // ...
}

export class TypedInjectSettingsTxtService extends SettingsTxtService {
    public static inject = ["logger", "fileSystem"] as const;

    constructor(
        protected logger: Logger,
        protected fileSystem: FileSystem<string>,
    ) {
        super();
    }
}

```

`TypedInjectLogger`和`TypedInjectFileSystem`类作为所需接口的具体实现。通过使用静态变量`inject`列出对象依赖关系，在类级别定义类型绑定。

以下代码片段演示了类型化注入环境中的所有主要容器操作:

```
const appInjector = createInjector()
    .provideClass("logger", TypedInjectLogger, Scope.Singleton)
    .provideClass("fileSystem", TypedInjectFileSystem, Scope.Singleton);

const logger = appInjector.resolve("logger");
const fileSystem = appInjector.resolve("fileSystem");
const settingsService = appInjector.injectClass(TypedInjectSettingsTxtService);

```

使用`createInjector`函数实例化容器，并显式声明令牌到类的绑定。开发人员可以使用`resolve`函数访问所提供的类的实例。可注射类可以使用`injectClass`方法获得。

### InversifyJS

项目提供了一个轻量级的 DI 容器，它使用了通过标记化创建的接口。它使用 decorator 和 decorator 的元数据进行注入。然而，将实现绑定到接口仍然需要一些手工工作。

支持依赖关系范围。对象的作用域可以是单一对象或瞬态对象，也可以绑定到一个请求。如果有必要，开发人员可以使用单独的 DI 容器。

下面的代码片段演示了如何转换上下文 DI 接口以使用 InversifyJS:

```
export const TYPES = {
    Logger: Symbol.for("Logger"),
    FileSystem: Symbol.for("FileSystem"),
    SettingsService: Symbol.for("SettingsService"),
};

@injectable()
export class InversifyLogger implements Logger {
    // ...
}

@injectable()
export class InversifyFileSystem implements FileSystem<string> {
    // ...
}

@injectable()
export class InversifySettingsTxtService implements SettingsService {
    constructor(
        @inject(TYPES.Logger) protected readonly logger: Logger,
        @inject(TYPES.FileSystem) protected readonly fileSystem: FileSystem<string>,
    ) {
        // ...
    }
}

```

根据[官方文档](https://inversify.io)，我创建了一个名为`TYPES`的地图，其中包含了我们稍后将用于注射的所有令牌。我实现了必要的接口，为每个接口添加了类级装饰器`@injectable`。`InversifySettingsTxtService`构造函数的参数使用`@inject`装饰器，帮助 DI 容器在运行时解析依赖关系。

DI 容器的代码可以在下面的代码片段中看到:

```
const container = new Container();
container.bind<Logger>(TYPES.Logger).to(InversifyLogger).inSingletonScope();
container.bind<FileSystem<string>>(TYPES.FileSystem).to(InversifyFileSystem).inSingletonScope();
container.bind<SettingsService>(TYPES.SettingsService).to(InversifySettingsTxtService).inSingletonScope();

const logger = container.get<InversifyLogger>(TYPES.Logger);
const fileSystem = container.get<InversifyFileSystem>(TYPES.FileSystem);
const settingsService = container.get<SettingsTxtService>(TYPES.SettingsService);

```

InversifyJS 使用 fluent 接口模式。IoC 容器通过在代码中显式声明来实现标记和类之间的类型绑定。获取托管类的实例只需要一次调用和适当的转换。

### typeid

TypeDI 项目旨在通过利用装饰者和装饰者元数据来实现简单性。它支持单例对象和瞬态对象的依赖范围，并允许存在多个 DI 容器。使用 TypeDI 有两种选择:

*   基于类的注入
*   基于令牌的注入

#### 基于类的注入

基于类的注入允许通过传递接口-类关系来插入类:

```
@Service({ global: true })
export class TypeDiLogger implements Logger {}

@Service({ global: true })
export class TypeDiFileSystem implements FileSystem<string> {}

@Service({ global: true })
export class TypeDiSettingsTxtService extends SettingsTxtService {
    constructor(
        protected logger: TypeDiLogger,
        protected fileSystem: TypeDiFileSystem,
    ) {
        super();
    }
}

```

每个类都使用类级别的`@Service`装饰器。`global`选项意味着所有的类都将在全局范围内被实例化为单例。`TypeDiSettingsTxtService`类的构造函数参数明确声明它需要一个`TypeDiLogger`类的实例和一个`TypeDiFileSystem`类的实例。

一旦我们声明了所有的依赖项，我们就可以如下使用 TypeDI 容器:

```
const container = Container.of();

const logger = container.get(TypeDiLogger);
const fileSystem = container.get(TypeDiFileSystem);
const settingsService = container.get(TypeDiSettingsTxtService);

```

#### TypeDI 中基于标记的注入

基于令牌的注入使用令牌作为中介将接口绑定到它们的实现。与基于类的注入相比，唯一的变化是使用`@Inject`装饰器:为每个构造参数声明适当的标记

```
@Service({ global: true })
export class TypeDiLogger extends FakeLogger {}

@Service({ global: true })
export class TypeDiFileSystem extends FakeFileSystem {}

@Service({ global: true })
export class ServiceNamedTypeDiSettingsTxtService extends SettingsTxtService {
    constructor(
        @Inject("logger") protected logger: Logger,
        @Inject("fileSystem") protected fileSystem: FileSystem<string>,
    ) {
        super();
    }
}

```

我们必须构造我们需要的类的实例，并将它们连接到容器:

```
const container = Container.of();

const logger = new TypeDiLogger();
const fileSystem = new TypeDiFileSystem();

container.set("logger", logger);
container.set("fileSystem", fileSystem);

const settingsService = container.get(ServiceNamedTypeDiSettingsTxtService);

```

#### 锡林盖

项目是由微软维护的阿迪容器。它是一个通用的容器，支持几乎所有标准的 DI 容器特性，包括解决循环依赖。与 TypeDI 类似，TSyringe 支持基于类和基于标记的注入。

#### TSyringe 中基于类的注入

开发人员必须用 TSyringe 的类级装饰器标记目标类。在下面的代码片段中，我们使用了`@singleton`装饰器:

```
@singleton()
export class TsyringeLogger implements Logger {
    // ...
}

@singleton()
export class TsyringeFileSystem implements FileSystem {
    // ...
}

@singleton()
export class TsyringeSettingsTxtService extends SettingsTxtService {
    constructor(
        protected logger: TsyringeLogger,
        protected fileSystem: TsyringeFileSystem,
    ) {
        super();
    }
}

```

然后，TSyringe 容器可以自动解析依赖关系:

```
const childContainer = container.createChildContainer();

const logger = childContainer.resolve(TsyringeLogger);
const fileSystem = childContainer.resolve(TsyringeFileSystem);
const settingsService = childContainer.resolve(TsyringeSettingsTxtService);

```

#### TSyringe 中基于令牌的注入

与其他库类似，TSyringe 要求程序员为基于令牌的注入使用构造函数参数装饰器:

```
@singleton()
export class TsyringeLogger implements Logger {
    // ...
}

@singleton()
export class TsyringeFileSystem implements FileSystem {
    // ...
}

@singleton()
export class TokenedTsyringeSettingsTxtService extends SettingsTxtService {
    constructor(
        @inject("logger") protected logger: Logger,
        @inject("fileSystem") protected fileSystem: FileSystem<string>,
    ) {
        super();
    }
}

```

在声明目标类之后，我们可以用相关的生命周期注册标记类元组。在下面的代码片段中，我使用了一个 singleton:

```
const childContainer = container.createChildContainer();

childContainer.register("logger", TsyringeLogger, { lifecycle: Lifecycle.Singleton });
childContainer.register("fileSystem", TsyringeFileSystem, { lifecycle: Lifecycle.Singleton });

const logger = childContainer.resolve<FakeLogger>("logger");
const fileSystem = childContainer.resolve<FakeFileSystem>("fileSystem");
const settingsService = childContainer.resolve(TokenedTsyringeSettingsTxtService);

```

### NestJS

NestJS 是一个框架，它使用了一个定制的 DI 容器。可以将 NestJS 作为一个独立的应用程序，作为其 DI 容器的包装器来运行。它使用 decorators 和它们的元数据进行注入。范围是允许的，您可以从单例对象、瞬态对象或请求绑定对象中进行选择。

下面的代码片段包括对 [NestJS](https://blog.logrocket.com/unit-testing-nestjs-applications-with-jest/) 功能的演示，从声明核心类开始:

```
@Injectable()
export class NestLogger implements Logger {
    // ...
}

@Injectable()
export class NestFileSystem extends FileSystem<string> {
    // ...
}

@Injectable()
export class NestSettingsTxtService extends SettingsTxtService {
    constructor(
        protected logger: NestLogger,
        protected fileSystem: NestFileSystem,
    ) {
        super();
    }
}

```

在上面的代码块中，所有目标类都用`@Injectable`装饰器标记。接下来，我们定义了应用程序的核心类`AppModule`，并指定了它的依赖项`providers`:

```
@Module({
    providers: [NestLogger, NestFileSystem, NestSettingsTxtService],
})
export class AppModule {}

```

最后，我们可以创建应用程序上下文并获得上述类的实例:

```
const applicationContext = await NestFactory.createApplicationContext(
    AppModule,
    { logger: false },
);

const logger = applicationContext.get(NestLogger);
const fileSystem = applicationContext.get(NestFileSystem);
const settingsService = applicationContext.get(NestSettingsTxtService);

```

## 摘要

在本教程中，我们介绍了什么是依赖注入容器，以及为什么要使用它。然后，我们探索了 TypeScript 的五种不同的依赖注入容器，通过一个例子学习了如何使用每一种容器。

既然 TypeScript 是一种主流编程语言，使用依赖注入这样的既定设计模式可能有助于开发人员从其他语言过渡。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.