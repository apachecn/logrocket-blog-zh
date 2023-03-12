# TypeScript 泛型入门- LogRocket 博客

> 原文：<https://blog.logrocket.com/getting-started-with-typescript-generics/>

TypeScript 通过在 JavaScript 中引入可选的静态类型检查来为我们解决 JavaScript 中的类型问题。开始使用 TypeScript 相当容易，只需几分钟，您就可以让 TypeScript 项目与您的第一批类型一起运行。但是在使用 TypeScript 并创建了第一个联合类型之后，您可能会开始注意到一个问题。你有很多类似的类型，它们是为某段特定的代码创建的。

换句话说，你正在创建比你需要的更多的代码。从长远来看，这不会帮助你扩展你的代码。但是如何解决这个问题呢？

为此，我们在 TypeScript 中有[泛型](https://www.typescriptlang.org/docs/handbook/generics.html)。你不需要创建很多类型，你可以从泛型的使用中获得更多可重用的代码。

## 无商标消费品

如果使用方式错误，TypeScript 会增加项目的代码复杂性。当您拥有的类型、接口和联合类型比您需要的多时，您的代码将变得更加难以阅读和维护。

为了理解什么是泛型以及它是如何工作的，让我们从一个例子开始。假设我们有一个名为`print`的函数，这个函数负责简单地返回我们的用户:

```
type User = {
  name: string;
  age: number;
};

const user: User = {
  name: "Leonardo",
  age: 22
};

function print(user: User): User {
  return user;
};
```

如您所见，目前没有什么太难或不寻常的事情。我们创建一个名为`User`的类型，一个名为`user`的对象，我们用我们的`User`类型来指定它。同样，在我们的`print`函数中，我们将收到一个需要等于`User`的`user`参数，作为最终结果，我们的函数将返回一个`User`。

现在，让我们想象我们想要改变函数中的一些东西。我们将需要返回类似于`User`的东西，而不是返回一个`User`，但略有不同。

假设我们有一个名为`Admin`的类型，它类似于`User`，但是有一个额外的属性:

```
type Admin = {
  name: string;
  age: number;
  admin: true;
};
```

我们现在有了一个新的类型，出于可重用性的目的，如果我们的`print`函数允许我们打印`User`和`Admin`类型会更好。

这是创建泛型类型的完美用例。使用泛型，我们可以处理各种类型，而不是单一类型。在我们的例子中，我们可以使用我们的`print`函数来打印我们的`User`和`Admin`类型。

这就是我们如何创建一个函数的泛型类型。在函数名之后，我们在`<>`之间传递我们的泛型类型。在我们的例子中，我们将把我们的泛型类型命名为`T`。我们函数的参数也是我们创建的泛型类型，我们函数的返回也是我们的泛型类型。我们使用`T`作为我们的通用类型，这个通用类型允许我们使用多种类型而不是单一类型。

你可能会问，“但是为什么呢？”`T`代表类型，常用于 TypeScript 引用第一个类型变量名。您可以随意命名它，但是对于类型变量，当使用泛型时，通常的做法是(特别是当您有多个泛型类型时)用另一个字母来命名它。

现在，如果我们调用我们的`print`函数来打印`user`和`admin`对象，它将完美地工作。我们使用一个泛型类型来确保我们打印的对象具有正确的属性，我们可以注意到我们的代码变得更加可读和可重用:

```
type User = {
  name: string;
  age: number;
};

type Admin = {
  name: string;
  age: number;
  admin: true;
};

const user: User = {
  name: "Leonardo",
  age: 22
};

const admin: Admin = {
  name: "Leonardo",
  age: 22,
  admin: true
};

function print<T>(user: T): T {
  return user;
};    

print<User>(user); // Result: { name: "Leonardo", age: 22 }
print<Admin>(admin); // Result: { name: "Leonardo", age: 22, admin: true }
```

这里要注意的一点是，泛型允许我们处理各种类型，而不是单一类型，当我们要调用或实例化代码时，我们需要将我们的类型作为“参数”传递。

在我们前面的例子中，当我们调用 print 函数来打印用户时，我们需要将用户类型作为参数传递，如果我们不传递任何东西，它将同样工作，但是没有 TypeScript 的好处。

## 班级

我们可以在 TypeScript 中使用泛型类。泛型的另一个好处是我们可以有多个类型参数。假设我们有一个名为`User`的类，我们想为这个类传递两个类型参数——一个用于`name`，另一个用于`age`。我们可以这样做:

```
class User<T, U> {
  name: T;
  age: U;
  constructor(name: T, age: U) {
    this.name = name;
    this.age = age;
  }

  print(): void {
    console.log(`Hello ${this.name}, you are ${this.age} years old!`);
  }
};

const newUser = new User<string, number>("Leonardo", 23);
newUser.print(); // Hello Leonardo, you are 23 years old!
```

## 接口

泛型的另一个好处是，我们可以创建泛型接口，我们可以轻松地创建可重用的接口，并使用不同的类型和接口。

假设我们有三个接口`Admin`、`User`和`Client`。我们将在我们的`Admin`接口上传递我们的泛型类型参数，以及我们将用于测试目的的另外两个接口:

```
interface User {
  firstName: string;
};

interface Client {
  firstName: string;
  lastName: string;
};

interface Admin<T> {
  values: T;
  isAdmin: true;
};
```

我们的`Client`接口和我们的`User`接口不一样。现在，让我们创建两个不同的对象，并为我们的通用接口`Admin`传递不同的接口:

```
interface User {
  firstName: string;
};

interface Client {
  firstName: string;
  lastName: string;
};

interface Admin<T> {
  values: T;
  isAdmin: true;
};

const user: Admin<User> = {
  values: {
    firstName: "Leonardo"
  },
  isAdmin: true
};

const client: Admin<Client> = {
  values: {
    firstName: "Leonardo",
    lastName: "Maldonado"
  },
  isAdmin: true
};
```

## 限制

当我们使用泛型时，我们可以使用约束来应用到我们的泛型类型参数。要实现它，我们所要做的就是使用 extends 关键字。在使用它之前，您需要确定一些事情——`extends`关键字只对接口和类有效，否则，它将抛出一个错误。

让我们以第一个打印函数为例，在我们的`T`类型参数中使用`extends`关键字应用一个约束:

```
type User = {
  name: string;
  age: number;
};

const user: User = {
  name: "Leonardo",
  age: 22
};

function print<T>(user: T): T {
  return user;
};
print<User>(user); // Result: { name: "Leonardo", age: 22 }
```

## 何时使用泛型？

一旦你对泛型有了更多的了解并理解了它的工作原理，你就可以开始更多地使用它，并有望实现它的真正好处。但是在开始到处使用它之前，您需要确定何时使用它来创建一段执行代码。

要确定何时使用泛型类型，可以考虑两件事:

1.  这个函数或类需要处理多种数据类型吗？
2.  这个函数或类会在不止一个地方使用吗？

这两个问题可以帮助你决定是否需要泛型。随着应用程序的增长，您工作和创建比您需要的更多类型的机会可能会增加。我们可能忘记考虑的是使用类似泛型的东西来帮助我们获得更可重用和可维护的代码。

从长远来看，不仅你会从中受益，你的整个团队也会从中受益。您的代码将变得更具可读性，这意味着如果需要的话，其他开发人员可以很容易地理解和维护它，您的代码将变得更简洁、写得更好，并且您会觉得您的应用程序比以往任何时候都更安全、更一致。

## 结论

在本文中，我们学习了如何在函数中使用泛型类型来创建更多可重用的函数。我们还学习了泛型类和泛型接口。当你试图实现一个更可重用和可维护的代码时，泛型的概念是最好的概念之一，它使你更容易重用类型，而不必创建一些类似的类型。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.