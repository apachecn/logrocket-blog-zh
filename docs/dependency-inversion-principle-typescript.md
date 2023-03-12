# 理解 TypeScript 中的依赖倒置原则

> 原文：<https://blog.logrocket.com/dependency-inversion-principle-typescript/>

***编者按:**本指南旨在理解 TypeScript 中的依赖倒置原则，最近一次更新是在 2023 年 1 月 13 日，包括定义高级/低级模块、比较松耦合和紧耦合、依赖倒置与依赖注入的章节，并解释了设计的坚实原则。*

当构建软件时，开发人员通常将大问题分解成小问题。我们在组件或模块中实现子问题的解决方案，然后将这些组件组合在一起，创建一个解决原始问题的系统。

决定我们构建的系统和应用程序质量的一个因素是组成项目的软件模块之间的相互依赖程度。这种相互依赖的程度被称为[依赖性](https://blog.logrocket.com/managing-dependency-boundaries-typescript/)或耦合。

例如，如果我们有一个模块(或类)A 使用另一个模块(或类)B，我们说 A 依赖于 B，就像这样:

```
  class B {
  }
  class A {
      private b = new B();
  }
  // class A depends on class B

```

下图表示了 A 和 B 之间的关系:

![Diagram Representing Interdependence](img/2a441260ff159e8b96cd260a4f3ad304.png)

然而，在我们讨论依赖性反转原则之前，我们必须首先讨论松耦合和紧耦合的概念。在本文中，我们将深入理解 TypeScript 中的依赖倒置原则。

*向前跳转:*

## 理解松耦合和紧耦合

松散耦合是结构良好的应用程序的标志，在这种情况下，系统的组件或模块之间的相互依赖最小。当高级模块依赖抽象时，它促进了松散耦合，使得在不影响高级模块的情况下更改低级模块的实现变得更加容易。另一方面，组件相互依赖的紧耦合系统并不理想。

紧密耦合系统的一个缺点是，一个模块中的变化会对依赖于修改后的模块的其他模块产生连锁反应。另一个不利因素是，一个模块将变得难以重用和测试，因为它的依赖模块必须包含在内。

### TypeScript 应用程序中的松散耦合

如上所述，松耦合是结构良好的应用程序的标志。那么，我们如何实现这一点呢？下面是如何在 TypeScript 应用程序中实现松散耦合的一些示例:

```
// Example 1: Using interfaces to achieve loose coupling

// High-level module
class UserService {
  constructor(private repository: UserRepository) {}

  save(user: User) {
    this.repository.save(user);
  }
}

// Abstraction (interface)
interface UserRepository {
  save(user: User): void;
}

// Implementation of the abstraction
class UserRepositoryImpl implements UserRepository {
  save(user: User) {
    // Save the user to the database
  }
}

// The UserService depends on the abstraction, not the implementation
const userService = new UserService(new UserRepositoryImpl());

// Example 2: Using dependency injection to achieve loose coupling

class UserService {
  constructor(private repository: UserRepository) {}

  save(user: User) {
    this.repository.save(user);
  }
}

class UserRepository {
  save(user: User) {
    // Save the user to the database
  }
}

// The UserRepository is injected into the UserService
const userService = new UserService(new UserRepository());

```

现在我们知道了松散耦合的样子，下面是一些紧密耦合的例子:

```
// Example 1: Directly instantiating a dependent class

class UserService {
  repository = new UserRepository();  // Tight coupling

  save(user: User) {
    this.repository.save(user);
  }
}

class UserRepository {
  save(user: User) {
    // Save the user to the database
  }
}

// Example 2: Hardcoding a dependent class in a function

function saveUser(user: User) {
  const repository = new UserRepository();  // Tight coupling
  repository.save(user);
}

class UserRepository {
  save(user: User) {
    // Save the user to the database
  }
}

```

## 依存倒置原则

依赖倒置原则是一个[设计原则](http://staff.cs.utu.fi/~jounsmed/doos_06/material/DesignPrinciplesAndPatterns.pdf)，它声明高级模块应该依赖于抽象而不是具体的实现。这有助于高级和低级模块的解耦，使得在不影响高级模块的情况下更改低级模块变得更加容易。

依赖倒置原则帮助我们松散地耦合软件模块。该原则是在耦合软件模块多年后开发的，它规定:

*   高级模块不应该从低级模块导入任何东西；它们都应该依赖于抽象
*   抽象不应该依赖于具体的实现；具体的实现应该依赖于抽象

### 依赖于抽象

当我们[抽象](https://blog.logrocket.com/typescript-abstract-classes-and-constructors/)的时候，我们可以说我们是在处理一件事情的整体思路，而不关心细节。我们抽象事物的一个重要方式是通过使用接口的[。](https://blog.logrocket.com/understanding-using-interfaces-typescript/)

第一个原则声明高级和低级模块应该依赖于相同的抽象。如果一个模块依赖于一个抽象——一个[接口](https://blog.logrocket.com/understanding-using-interfaces-typescript/)或者[抽象类](https://blog.logrocket.com/typescript-abstract-classes-and-constructors/)——我们可以把它的依赖关系换成任何其他遵循该接口的实现。为了进行现实生活中的类比，请考虑下面的笔记本电脑充电器插头:

![Example of a Laptop Plug to Show Dependency Inversion](img/b8ad160f79dcb2df4fb05ea058fa0654.png)

这个插头可以接任何插座，只要插座满足三针的接口。这样，只要符合接口要求，插头就可以与各种插座配合使用。

## TypeScript 中依赖项反转的示例

让我们看一个例子，它在购物车的上下文中说明了依赖倒置原则:

```
// High-level module
class ShoppingCartService {
  constructor(private paymentProcessor: PaymentProcessor) {}

  checkout(cart: ShoppingCart) {
    return this.paymentProcessor.processPayment(cart);
  }
}

// Low-level module
class PaymentProcessor {
  processPayment(cart: ShoppingCart) {
    // Process the payment for the items in the shopping cart
  }
}

// Abstraction
interface PaymentProcessor {
  processPayment(cart: ShoppingCart): boolean;
}

// Implementation of the abstraction
class StripePaymentProcessor implements PaymentProcessor {
  processPayment(cart: ShoppingCart): boolean {
    // Use the Stripe API to process the payment for the items in the shopping cart
  }
}

// Now the ShoppingCartService depends on the abstraction, not the implementation
const shoppingCartService = new ShoppingCartService(new StripePaymentProcessor());

```

在这个例子中，`ShoppingCartService`类是一个高级模块，它为检查购物车提供了一个更抽象的接口。它依赖于底层模块`PaymentProcessor`类来处理购物车中商品的付款。

然而，它不依赖于具体的`PaymentProcessor`类，而是依赖于抽象的`PaymentProcessor`接口。这将`ShoppingCartService`从`PaymentProcessor`实现中解耦，并允许您在不影响`ShoppingCartService`的情况下轻松地更改`PaymentProcessor`的实现。这是一个可视化的例子:

![The Dependency Inversion Principle in TypeScript](img/6deb4380953af9093d81b626356b5877.png)

## 注入依赖关系

[依赖注入](https://blog.logrocket.com/roll-your-own-dependency-injection/)是一种技术，通过为低级模块提供抽象，允许我们将高级模块从低级模块中分离出来。在我们的购物车示例中，我们可以使用依赖注入为`ShoppingCartService`类提供一个`PaymentProcessor`接口的实例。

这允许我们在不影响`ShoppingCartService`的情况下改变`PaymentProcessor`的实现。下面是一个我们如何将`PaymentProcessor`依赖注入到`ShoppingCartService`类中的例子:

```
class ShoppingCartService {
    private paymentProcessor: PaymentProcessor;

    constructor(paymentProcessor: PaymentProcessor) {
        this.paymentProcessor = paymentProcessor;
    }

    public checkout(cart: Cart) {
        // do some logic
        this.paymentProcessor.processPayment(cart);
    }
}

```

在这个例子中，我们使用了`ShoppingCartService`的构造函数来注入`PaymentProcessor`依赖项。这将创建一个`PaymentProcessor`的新实例，并将其分配给`ShoppingCartService`的`paymentProcessor`属性。

为了在我们的应用程序中使用这个服务，我们可以创建一个`ShoppingCartService`类的实例，并传入一个`PaymentProcessor`接口的实例，如下所示:

```
const paymentProcessor = new PayPalPaymentProcessor();
const shoppingCartService = new ShoppingCartService(paymentProcessor);
```

在这里，依赖注入帮助我们实现松耦合，使系统更加灵活、可维护，并且[更容易测试](https://blog.logrocket.com/testing-typescript-apps-using-jest/)。

## 依赖倒置与注入

[依赖注入](https://blog.logrocket.com/top-five-typescript-dependency-injection-containers/)是一种实现依赖倒置的技术。在依赖注入中，一个类或模块接收它的依赖作为它的构造函数或函数的参数，而不是自己创建它们。这允许在测试期间用模拟实现替换依赖项，并使在运行时更改它们变得更容易。

### 依赖倒置的好处

使用依赖倒置的一个原因是为了更容易测试[高级模块](https://blog.logrocket.com/organize-code-in-typescript-using-modules/)。通过依赖抽象，可以使用低级模块的模拟实现来测试高级模块，而不需要使用真正的低级模块。这可以使测试更快、更可靠，因为它避免了建立真实数据库或其他外部资源的需要。

使用依赖倒置的另一个原因是为了更容易重用高级模块。通过依赖抽象，高级模块可以在不同的上下文中使用，而不需要改变它们所依赖的低级模块。这使得重用代码和避免重复变得更加容易。依赖倒置原则可以帮助创建更加灵活、可维护和可测试的软件。

## 在类型脚本中应用依赖倒置原则

依赖倒置原则声明高级模块不应该依赖于低级模块，而是两者都应该依赖于抽象。在我们的购物车示例中，我们通过创建一个接口`PaymentProcessor`来应用这个原则，该接口定义了支付处理器应该拥有的方法。

这允许高级模块`ShoppingCartService`类依赖于`PaymentProcessor`接口，而不是支付处理器的特定实现。然而，这不足以完全应用依赖倒置原则。

我们还需要确保底层模块，比如`PayPalPaymentProcessor`和`StripePaymentProcessor`类依赖于相同的抽象。我们可以通过在这些类中实现`PaymentProcessor`接口来达到这个目的:

```
class PayPalPaymentProcessor implements PaymentProcessor {
    public processPayment(cart: Cart) {
        // implementation for processing payment with PayPal
    }
}

class StripePaymentProcessor implements PaymentProcessor {
    public processPayment(cart: Cart) {
        // implementation for processing payment with Stripe
    }
}

```

通过让高层和低层模块依赖于相同的抽象，我们实现了模块之间的松散耦合。这允许我们在不影响`ShoppingCartService`类的情况下，轻松地替换掉`PaymentProcessor`的实现。

此外，这种设计模式允许更容易的测试，因为我们可以创建用于测试`ShoppingCartService`的`PaymentProcessor`接口的模拟实现，而不依赖于真实的支付处理器。总之，通过在 TypeScript 中应用依赖倒置原则，我们可以创建更灵活、更易维护、更易测试的代码库。

## 高级和低级模块

在依赖倒置原则的上下文中，高级模块在应用程序中提供了更抽象、更通用的功能。它们通常更接近用户，并且可能依赖于较低级别的模块来执行更具体的任务。

另一方面，低级模块提供更具体的功能。它们通常在应用程序的体系结构中处于较低的位置，更高级别的模块可能会依赖它们来执行更一般的任务。

例如，在 web 应用程序的情况下，高级模块可能是处理来自用户的 [HTTP 请求](https://blog.logrocket.com/axios-vs-fetch-best-http-requests/)并协调低级模块的动作的控制器，比如与数据库交互的存储库。

### TypeScript 中高级和低级模块的示例

通过遵循依赖倒置原则，您可以设计您的应用程序，以便高级模块依赖于抽象，而不是低级模块的具体实现。

这可以使您的代码更加灵活和易于维护，因为它减少了组件之间的耦合，并允许您轻松地更改低级模块的实现，而不会影响高级模块。下面是 TypeScript 中高级和低级模块的一个示例:

```
// High-level module
class UserService {
  constructor(private repository: UserRepository) {}

  save(user: User) {
    this.repository.save(user);
  }
}

// Low-level module
class UserRepository {
  save(user: User) {
    // Save the user to the database
  }
}

// Abstraction
interface UserRepository {
  save(user: User): void;
}

// Implementation of the abstraction
class UserRepositoryImpl implements UserRepository {
  save(user: User) {
    // Save the user to the database
  }
}

// Now the UserService depends on the abstraction, not the implementation
const userService = new UserService(new UserRepositoryImpl());

```

在这个例子中，`UserService`类是一个高级模块，它为保存用户提供了一个更抽象的接口。它依赖于底层模块`UserRepository`类将用户保存到数据库中。

然而，它不依赖于具体的`UserRepository`类，而是依赖于抽象的`UserRepository`接口。这将`UserService`从`UserRepository`实现中解耦，并允许您在不影响`UserService`的情况下轻松地更改`UserRepository`的实现。这是一个形象化的例子:

![Diagram Illustrating High-Level and Low-Level Modules](img/4fb21216bab83cd7a42a84e62f59276f.png)

## 依赖性倒置原则如何适应固体原则

坚实的原则是帮助你设计更易维护和升级的软件的指导方针。依赖倒置原则声明高级模块应该依赖抽象而不是低级模块。

这有助于分离应用程序中的组件，并使您的代码更加灵活、易于维护和测试。从属倒置原则在以下方面与实体原则相关:

*   单一责任原则:依赖倒置原则允许您将关注点分离到不同的模块中，从而有助于遵守单一责任原则。例如，高级模块可以提供更抽象的接口，而低级模块可以专注于实现细节
*   开闭原则:依赖倒置原则可以帮助你以一种对扩展开放但对修改封闭的方式设计你的代码。通过依赖抽象而不是具体的实现，您可以更容易地扩展代码的功能，而无需修改现有的代码
*   Liskov 替换原则:依赖倒置原则可以帮助您遵守 Liskov 替换原则，它允许您在不影响高级模块的情况下替换模块的不同实现
*   接口分离原则:依赖倒置原则提倡使用小的、特定的接口，这些接口只公开高级模块所需的方法。这可以通过不强迫高级模块依赖不必要的方法来帮助您遵守接口分离原则
*   依赖倒置原则:依赖倒置原则通过促进组件之间的松散耦合和坚持坚实的原则，帮助您设计更易维护和可伸缩的软件

要了解这方面的更多信息，请查看我们关于将坚实的原则应用于 TypeScript 的文章。

## 结论

在本文中，我们已经了解了什么是依赖关系，以及为什么我们希望组成 TypeScript 应用程序的组件或模块之间是松散耦合的。我们还研究了依赖倒置原则，这是一个如何应用它的实际例子，以及它如何使我们能够轻松地交换实现。

依赖倒置是流行的可靠原则之一，是罗伯特·c·马丁的前五个面向对象设计原则的首字母缩写。你可以在这里了解更多关于《坚实的原则》的其余部分[。](https://en.wikipedia.org/wiki/SOLID)

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.