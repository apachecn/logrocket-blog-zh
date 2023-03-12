# NestJS 中的序列化:一种不同的方法

> 原文：<https://blog.logrocket.com/serialization-in-nestjs-a-different-approach/>

当我们在应用程序中处理对象时，它们通常包含敏感信息。例如，表示从数据库中提取的用户的对象可能包含密码。

其他时候，保存在对象中的许多信息对最终客户端没有用，应该在通过网络发送对象之前删除，以节省带宽。兼顾这两者的过程称为序列化。

序列化是准备通过网络发送给最终客户端的对象的过程。准备一个对象可能是排除它的一些敏感或不必要的属性，或者添加一些额外的属性。

NestJS 提供了一种方法，通过使用一点装饰魔术和一个名为 class-transformer 的库来序列化从我们的 API 端点返回的对象。这个解决方案对于非常基本的情况是好的，但是在更复杂的情况下就不行了。

例如，要序列化一个对象，我们必须返回对象本身。虽然这看起来不是问题，但请考虑以下情况:

```
@UseInterceptors(ClassSerializerInterceptor)
@Get()
findOne(): UserEntity {
  return new UserEntity({
    id: 1,
    firstName: 'Kamil',
    lastName: 'Mysliwiec',
    password: 'password',
  });
}
```

这个例子来自官方的 Nest 文档。

正如所料，`findOne`方法返回的对象将被序列化。现在，如果我们想创建一个返回许多用户对象的`findAll`方法会怎么样呢？通常这种端点不仅仅返回实体，比如分页信息。

```
@UseInterceptors(ClassSerializerInterceptor)
@Get()
findMany(): UserEntity {
  const users = [user1, user2];

  return {
    users,
    ...paginationInfo,
  }
}
```

用户属性不会被序列化…为什么会被序列化？文件中明确说明:

> **注意:**我们必须返回该类的一个实例。如果您返回一个普通的 JavaScript 对象—例如，`{ user: new UserEntity() }` —该对象将不会被正确序列化。

因此，上面的伪代码说明了开箱即用序列化所提供的灵活性的缺乏。

幸运的是，使用 Nest 生态系统创建一个可重用的序列化解决方案轻而易举。让我们开门见山吧。

## 创建我们自己的序列化机制

为了创建我们自己的序列化，我们必须实现两件事。

首先，我们必须创建一个每个序列化程序都将扩展的“父”类。在那里，我们将放置所有可重用的方法，如 serialize 或`serializeCollection`。第二，就像上面的例子一样，我们必须创建自己的拦截器，负责实际运行我们的序列化程序。

序列化通常包括检查用户的角色，以查看允许他们检索给定对象的哪种属性。

除了从对象中排除不需要的值，我们的序列化还将提供其他功能，如异步序列化、嵌套序列化和添加原始对象中没有的附加属性。

下面是我们的序列化流程。

每个控制器标记哪些属性应该序列化，如下所示:

```
return {
  user: this.userSerializerService.markSerializableValue(user),
  otherProperty: true,
};
```

然后，拦截器检查返回对象的键，并序列化被标记的值。要“标记”一个对象，我们只需将它包装成一个名为`Serializable`的类。这样，从拦截器内部，我们可以很容易地检查属性值是否是`Serializable`类的实例。

```
export class Serializable<T> {
 public constructor(public readonly serialize: () => Promise<T | T[]>) {}
}
```

该类本身不做任何事情，除了保存对用于序列化值的函数的引用。该函数将由序列化程序提供。

所以上面物体的最终形状是:

```
return {
  user: Serializable<User>,
  otherProperty: true,
};
```

记住这一点，让我们创建基本序列化程序。

## `BaseSerializerService`

我们将创建一个名为`BaseSerializerService`的抽象类，它将为所有序列化程序提供所有可重用的方法。

```
export abstract class BaseSerializerService<E, T> {
  // ...
}
```

该类有两个泛型类型，`E`和`T`，分别代表一个实体和一个序列化值。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

### 序列化方法

```
 public abstract async serialize(entity: E, role: UserRole): Promise<T>;

 private serializeCollection(values: E[], role: UserRole): Promise<T[]> {
   return Promise.all<T>(values.map((v) => this.serialize(v, role)));
 }
```

每个序列化器将实现它自己的`serialize`方法；因此，该方法是`abstract`并且没有实现。`serialize`方法接受一个实体和一个用户角色。然后，考虑到用户角色，它序列化实体。之后，序列化的对象就可以发送给最终客户端了。

第二个方法叫做`serializeCollection`，它接受一个实体数组并返回一个序列化对象数组。

诚然，我们可以使用一个名为`serialize`的方法，自己检查所提供的值是否是一个数组，但是最好让 API 尽可能明确。

### 包装值

为了将返回值标记为可序列化，以便拦截器可以在以后序列化它，我们提供了两个方法:

```
 public markSerializableValue(value: E): Serializable<T> {
   return new Serializable<T>(this.serialize.bind(this, value));
 }

 public markSerializableCollection(values: E[]): Serializable<T[]> {
   return new Serializable<T[]>(this.serializeCollection.bind(this, values));
 }
```

这两个函数都接受一个参数:在第一种情况下，它是一个实体，在第二种情况下，它是实体的集合。

序列化器方法就绪后，我们只需将它们传递给`Serializable`类，这样拦截器就可以稍后调用它们。请记住，在拦截器调用提供的函数之前，序列化不会发生。

同样，`Serializable`类除了保存对所提供函数的引用之外不做任何事情，以便稍后可以在拦截器中使用它。

## `SerializerInterceptor`

Nest 中的拦截器在处理请求之前和之后被调用，为我们提供了转换从控制器方法返回的对象的机会。

```
export interface AuthenticatedRequest extends Request {
 readonly user: User;
}

@Injectable()
export class SerializerInterceptor implements NestInterceptor {
 private async serializeResponse(
   response: Response,
   role: UserRole,
 ): Promise<Record<string, any>> {
   const serializedProperties = await Promise.all(
     Object.keys(response).map(async (key) => {
       const value = response[key];

       if (!(value instanceof Serializable)) {
         return {
           key,
           value,
         };
       }

       const serializedValue = await value.serialize(role);

       return {
         key,
         value: serializedValue,
       };
     }),
   );

   return serializedProperties.reduce((result, { key, value }) => {
     result[key] = value;

     return result;
   }, {});
 }

 public intercept(
   context: ExecutionContext,
   next: CallHandler,
 ): Observable<any> {
   const request = context.switchToHttp().getRequest<AuthenticatedRequest>();

   return next.handle().pipe(
     switchMap((response) => {
       if (typeof response !== 'object' || response === null) {
         return of(response);
       }

       return from(this.serializeResponse(response, request.user?.role));
     }),
   );
 }
}
```

Nest 需要公共方法`intercept`，在每次请求前调用。它有两个参数:`context`和`next`。

由于有了`context`对象，我们可以很容易地访问底层的`http`请求。

我们将假设有一些守卫或中间件在`request.user`属性中设置了经过身份验证的用户对象。

通过访问`user`对象，我们可以很容易地获得认证用户的角色。为了安全起见，我们使用最近在 TypeScript 中引入的可选链接操作符`?`，以防用户对象尚未设置。

对象有一个恢复请求处理的方法。如果我们决定一个请求不应该被处理，我们可以提前结束执行并返回一个空的可观察值，而不是调用`next.handle()`。

`next.handle()`方法返回一个将在某个时间点发出响应的可观察对象。我们使用 RxJS' `[switchMap](https://www.learnrxjs.io/learn-rxjs/operators/transformation/switchmap)`操作符，确保只返回一个响应。在有些情况下，这不是预期的行为——例如，如果拦截器与 WebSockets 一起使用。

在我们提供给`switchMap`操作符的函数中，我们检查响应是否

甚至是一个对象，因为如果它不是，那么就没有什么可序列化的。注意，不是返回`response`本身，我们必须使用`of`函数将它包装在一个可观察对象中，因为`switchMap`期望我们返回一个可观察对象。

如果`response`确实是一个对象，我们将使用`serializeResponse`方法。因为我们支持异步序列化，所以我们将返回的承诺包装在一个`from`函数中，以从承诺中创建一个可观察对象。

我们来看看`serializeResponse`方法。

```
 private async serializeResponse(
   response: Response,
   role: UserRole,
 ): Promise<Record<string, any>> {
   const serializedProperties = await Promise.all(
     Object.keys(response).map(async (key) => {
       const value = response[key];

       if (!(value instanceof Serializable)) {
         return {
           key,
           value,
         };
       }

       const serializedValue = await value.serialize(role);

       return {
         key,
         value: serializedValue,
       };
     }),
   );
```

知道作为参数提供的响应是一个对象，我们可以安全地使用`Object.keys`方法来迭代对象的键。

该方法可以分为两部分:序列化属性和形成响应对象。

首先，我们映射这些键，并检查它们各自的值是否是`Serializable`的实例。如果是，我们执行`serialize`方法并等待其结果，将其作为新值返回。否则，我们只返回现有的值。

我们必须将映射包装在`Promise.all`方法中，以确保在继续之前每个承诺都被解析。结果，在这个过程发生之后，我们得到了一个具有以下形状的对象数组:`{ key, value }`。由于使用了`Promise.all`，我们能够同时运行许多属性的序列化。

接下来，我们将对象和值的数组简化为一个对象，返回与原始对象形状完全相同的对象，但是所有属性都被序列化。

## 真实世界的用例

假设有一个代表作者的用户实体。可以参考作者写的很多文章。下面是使用 TypeORM 库的实体的样子:

```
@Entity(USER_TABLE_NAME)
export class User {
 @PrimaryGeneratedColumn('uuid')
 public id: string;

 @Column('text', { unique: true })
 public email: string;

 @Column('text')
 public password: string;

 @Column({ type: 'enum', enum: UserRole })
 public role: UserRole;

 @OneToMany(
   () => Article,
   (article) => article.author,
 )
 public articles: Article[];
}
```

我们序列化的目标是确保删除`password`属性，并且嵌套的文章也被序列化。为了保持代码的整洁和可重用性，最好使用`articleSerializerService`来序列化一篇文章，而不是在`userSerializerService`中编写相同的逻辑。

```
@Injectable()
export class UserSerializatorService extends BaseSerializerService<
 User,
 SerializedUserDTO
> {
 public constructor(
   private readonly articleSerializatorService: ArticleSerializatorService,
 ) {
   super();
 }

 public async serialize(
   entity: User,
   role: UserRole,
 ): Promise<SerializedUserDTO> {
   const strippedEntity = _.omit(entity, [
     'password',
   ]);
   const articles = await this.articleSerializatorService.serializeCollectionForRole(
     entity.charters,
     role,
   );

   return {
     ...strippedEntity,
     articles,
   };
 }
}
```

```
@UseInterceptors(SerializerInterceptor)
@Controller(USER_ENDPOINT)
export class UserController {
 public constructor(
   private readonly userSerializatorService: UserSerializatorService,
 ) {}
 @Get(USER_ID_ROUTE)
 public async get(
   @Param(USER_ID_PARAM) userId: string,
 ): Promise<GetUserResDTO> {
   const user = await this.userService.findOne({ userId })

   return {
     user: this.userSerializatorService.markSerializableValue(user),
   };
 }
}
```

我们还可以访问请求用户的角色(从拦截器传递过来)，因此我们可以基于此剥离一些属性。

由于序列化器的结构，`articleSerializer`也可能有一些嵌套的属性。每个序列化程序确保来自其域的实体被正确序列化，并将其他实体的序列化委托给它们各自的序列化程序。

## 摘要

Nest 核心中提供的默认序列化很容易设置，而且非常简单。通过实现我们自己的机制，我们在如何执行流程方面获得了很大的灵活性和控制力。该实现是可重用的，并且只需稍作修改，就可以快速适应任何现有的或新创建的项目。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)