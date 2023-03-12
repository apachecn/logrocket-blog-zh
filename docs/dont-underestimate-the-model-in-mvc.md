# 不要低估 MVC 中的模型

> 原文：<https://blog.logrocket.com/dont-underestimate-the-model-in-mvc/>

[Kelvin Omereshone Follow](https://blog.logrocket.com/author/kelvinomereshone/) Kelvin is an independent software maker currently building Sailscasts, a platform to learn server-side JavaScript. He is also a technical writer and works as a Node.js consultant, helping clients build and maintain their Node.js applications.

# 不要低估 MVC 中的模型

## 

2021 年 3 月 4 日 3 min 读 921

MVC 是构建软件应用程序的著名架构模式。它最初用于构建桌面 GUI，现在是构建 web 应用程序和移动应用程序最流行的方式之一。

MVC 将软件应用程序分解成三个组件:

*   模型——代表我们的应用程序中数据的形状
*   视图–用户与之交互的应用程序的一部分。它代表了应用程序的用户界面
*   控制器——你的应用程序的大脑。控制器负责您的大部分业务逻辑。如果您的应用程序是一个 web 应用程序，控制器将处理进入您的应用程序的请求。如果您的应用程序是一个移动应用程序，控制器将为您的视图处理应用程序的状态

现在我们已经对 MVC 框架有了一个简短的回顾，在本文中，我们将聚焦于 MVC 的一个组件，这个组件在大多数用例中没有被充分利用，或者说没有被充分利用。

## 模型

尽管许多 web 开发人员都知道 MVC 架构，但是仍然没有完全理解如何使用每个组件的全部潜力和架构意图，而 MVC 中最被低估或未被充分利用的组件之一就是模型。

大多数定义认为模型代表了应用程序中的数据。但是，按照设计，代表应用程序底层数据结构的模型还应该包含:

*   表示特定数据的属性
*   像验证规则这样的业务逻辑
*   将对数据执行的逻辑

## 表示特定数据的属性

这部分是最容易掌握的部分，因为大多数 web 框架都强调在这里定义属性。例如，让我们使用一个 MVC Node.js 框架 Sails 的 web 应用程序，我们可以有一个这样定义的用户模型:

```
// api/models/User.js
module.exports = {
  attributes: {
        id: {},
        name: {},
        EmailAddress: {},
    }
  }
}

```

您可以看到，我们的模型在 Sails 中表示为导出对象的 JavaScript 模块。在这个对象中，有一个`attributes`键，我们在其中定义了这个模型表示的特定数据。

## 业务逻辑

MVC 中的模型还应该能够在它所代表的数据上实现业务逻辑。例如，web 应用程序的通用业务逻辑包括验证规则。

验证规则强制进入应用程序的数据满足所需系统设计的约束。一个误解是在控制器级别进行这种验证。然而，这是模特的工作。

以上面的 Sails 模型为例，让我们看看这个模型应该如何处理业务逻辑，比如验证:

```
// api/models/User.js
module.exports = {
  attributes: {
        id: {
          type: 'string',
          required: true
          isUuid: true
        },
        name: {
          type: 'string',
          allowNull: true
        },
        EmailAddress: {
          type: 'string',
          isEmail: true,
           unique: true
        },
    }
  }
}

```

您可以看到，我们遵循 MVC 原则的模型为它将要表示的数据提供了验证属性。让我们看看实现的所有逻辑:

*   `type`–强制数据类型(在这种情况下，应该是字符串)
*   `required`–强制在创建该模型的新实例时，始终提供该值设置为 true 的数据
*   `allowNull`–当某个特定数据的值设置为 true 时，模型将知道让实例在该特定数据值设置为 null 的情况下创建
*   `unique`–将此项设置为 true 会通知模型确保只允许一个实例拥有该数据的特定值。这意味着该数据的值必须是唯一的
*   `isEmail`–大多数情况下，我们会在控制器中进行类似的检查，但在使用模型的最大容量时，我们会在模型中设置这一点，以确保特定数据是电子邮件
*   `isUuid`–如果为模型数据设置为真，模型将确保数据为 UUID 格式(v1、v2 或 v3)

## 将对数据执行的逻辑

模型应该包含处理数据操作的逻辑。例如，假设我们有一个帖子模型，该模型应该包含获取特定帖子的评论的方法。

在 Sails 模型中，每个模型都有静态方法，如`find`、`findOne`、`update`等。这有助于您从控制器调用数据访问。

## 充分利用模型的优势

在一个实现良好的 MVC 架构中，模型通常承载大部分的业务逻辑，因为这个逻辑与数据操作紧密耦合，并且模型负责那个数据级别。

最大限度地利用模型鼓励精益控制器，这种控制器更专注于将模型连接到视图，反之亦然，而不是实际实现业务逻辑。

## 模型的最佳实践

*   模型不应使用与最终用户的输入或请求相关的变量。这应该留给控制器传递给模型
*   避免在模型中嵌入表示代码。这是因为表示代码根据最终用户的输入而变化，所以这应该留给 MVC 来看

## 结论

尽管我们使用 Sails 作为 MVC 框架的一个例子，来看看如何在 MVC 中使用模型来发挥它的全部潜力，但是这些原则是框架不可知的，并且应该适用于你所选择的 MVC 框架。以下是关键要点:

1.  在模型中定义数据的业务逻辑，比如验证规则
2.  定义将在模型内部的模型上执行的逻辑
3.  让你的控制器精益