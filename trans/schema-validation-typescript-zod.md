# 使用 Zod 在 TypeScript 中进行模式验证

> 原文：<https://blog.logrocket.com/schema-validation-typescript-zod/>

TypeScript 太牛了！近年来，它提高了开发人员的生产力和工具。TypeScript 不仅有助于静态类型检查，还增加了一组面向对象编程(OOP)的概念，如泛型、模块、类、接口等等。

可以说，如果您曾经使用过 TypeScript，那么回到只有 JavaScript 的代码库可能会很困难。虽然 TypeScript 在各方面看起来都很棒，但它有一个盲点——它只在编译时进行静态类型检查，根本没有任何运行时检查。

这就是佐德的切入点。在本文中，您将了解 Zod 中的模式设计和验证，以及如何在运行时在 TypeScript 代码库中运行它。

## 什么是佐德，我们为什么需要它？

您可能会问自己，为什么有人首先需要运行时检查？

嗯，运行时检查有助于在服务器端获得正确验证的数据。在用户填写某种形式的表单的情况下，TypeScript 不知道用户输入是否像您期望的那样在运行时在服务器上。

因此，Zod 有助于数据完整性，并防止向数据库发送垃圾值。此外，最好在 UI 本身上记录一个错误，比如当您期望一个字符串时，用户输入了数字。

Zod 就是解决这个问题的工具。它填补了这个 TypeScript 盲点，有助于运行时的类型安全。Zod 可以帮助您构建非常灵活的模式设计，并针对表单或用户输入运行它。

已经有一些工具，比如 Yup、Joi、io-ts 等等，做着和 Zod 一样的事情。虽然所有这些库对于模式设计和运行时验证来说都很棒，但是 Zod 在以下几个方面表现突出:

*   灵活性:Zod 非常灵活，可以将几个实例链接在一起，从而防止静态类型的重复
*   它没有依赖关系
*   尽管最好将 Zod 与 TypeScript 一起使用，但是如果您想要有限的、仅 Zod 类型的安全性，也可以在普通的 JavaScript 项目中使用它。它不依赖于类型脚本
*   它关注的是不变性，因此像`optional()`这样的方法返回一个新的实例，而不是完全改变同一个对象

## 使用 Zod 和 TypeScript 的好处

Zod 与 TypeScript 配合得特别好。在典型的 TypeScript 代码库中，您要确保所有的静态类型安全都将由 TypeScript 在编译时处理。甚至大多数第三方包都将它们的代码与它们的`types`一起发布，比如 React 如何在一个名为`@types/react`的 npm 包下拥有它的类型。

使用 Zod 进行额外的运行时检查可以解决 TypeScript 的这些问题。您可能会遇到这样的问题，即使在使用了相应的`@types`包之后，您仍然需要将`undefined`或`unknown`分配给一个类型——唯一的原因是您事先不知道用户会输入什么或者响应结构是什么样的。

Zod 以非常简洁的方式应用这些运行时检查。这实际上就像从 TypeScript 中提取数据类型并将它们扩展到特定于 Zod 的方法。Zod 自动推断您已经在 TypeScript 中提到的类型，防止类型重复。

这些可链接的 Zod 的实用程序方法也启发了 TypeScript 实用程序，证明了这两者是如何一起发展的。

此外，在有些情况下，TypeScript 的错误处理不是最理想的，Zod 可以在运行时根据用户与 UI 的交互更好地显示错误。

## Zod 中的原语

让我们从一个非常基本的 Zod 示例开始讨论模式验证:

```
import { z } from "zod"
const dataInputFromUser = z.string().min(8).max(16)
dataInputFromUser.parse("A long text")

```

上面的代码将安全地解析。根据您的用例，您可以将用户导航到下一个输入或页面。

如果我们稍微调整一下线条，像这样:

```
dataInputFromUser.parse("A really long text")

```

它会抛出一个异常:

```
errors: [
    {
      code: 'too_big',
      maximum: 16,
      type: 'string',
      inclusive: true,
      message: 'String must contain at most 16 character(s)',
      path: []
    }
  ]

```

如果您需要更安全的异常处理，您可以使用`.safeParse()`方法简单地记录错误。

这是在 Zod 中使用原语的最简单的例子之一。原始值不仅仅局限于`string`，还提供了`number`、`bigint`、`boolean`、`date`等其他方法。还有一些空类型，如`undefined`、`null`和`void`。

利用这些原语和一些特定的方法可以实现非常灵活的模式设计:

```
// email validation //
z.string().email().startsWith(string).trim().max(18).min(1)
// can be used for Terms & Conditions check at runtime //
z.boolean()

// can be used for high-precision large values if they are being calculated at runtime //
z.bigint()

```

上面的例子将这些原语链接在一起，为电子邮件输入字段创建一个非常实用的运行时类型安全。

## Zod 中的对象

大多数面向用户的表单需要一些数据输入和不同数据类型的验证。这就是在 Zod 中使用对象更好的地方。您可以在运行时检查时为一组需要的属性创建一个模式。

```
import { z } from 'zod'

const FormData = z.object({
  firstName: z.string().min(1).max(18),
  lastName: z.string().min(1).max(18),
  phone: z.string().min(10).max(14).optional(),
  email: z.string().email(),
  url: z.string().url().optional(),
});

const validateFormData = (inputs: unknown) => {
  const isValidData = FormData.parse(inputs);
  return isValidData;
};

```

在上面的 TypeScript 代码中，无法仅使用 TypeScript 在运行时实施模式验证，因此使用了`inputs: unknown`。

这就是可以使用`z.Object()`构建可扩展模式验证的地方。如果用户输入满足您的模式定义的字段，数据将被安全地解析。然后，您可以将数据发送到服务器。否则，将会抛出异常，就像我们在开始时看到的那样。

## 构成复杂的模式对象

可能会有这样的情况，除了一些额外的或缺少的类型之外，两个表单/字段的模式对象设计几乎是相同的。在这些情况下，您不需要一次又一次地复制相同的对象模式。相反，您可以通过使用 Zod 方法来防止重复，比如`merge()`和`extend()`。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

上面的代码可以通过删除`firstName`和`lastName`类型的重复来进一步改进，如下所示:

```
const GenericStringContraint = z.string().min(1).max(18),

const FormData = z.object({
  firstName: GenericStringContraint, 
  lastName: GenericStringContraint, 
  // ...
});

```

这类似于以几种形式重复的数据类型块。比方说，`userId`和`fullName`在`Article`和`UserProfie`模式定义中重复出现，那么我们可以简单地将这两个“扩展”为:

```
const UserData = z.object({
  userId: z.string().min(1).max(5),
  fullName : z.string().min(1).max(18),
});

const Article = UserData.extend({
  title: z.string().min(5),
 date: z.date()
});

const UserProfile = UserData.extend({
  isVerifield: z.boolean(),
  numberOfArticles: z.number().positive()
});

```

上述方法是一种更好的方法，可以使代码可维护，并避免数据重复。使用`extend()`时需要注意的一点是，它会改变模式对象，即覆盖它们。

与`extend()`方法非常相似，Zod 也提供了一个`merge()`方法，两者略有不同。这在合并两个模式对象时很有用，不一定要“扩展”它们:

```
import { z } from "zod"

const User = z.object({
  url: z.string().email().min(8),
  name: z.string(),
  age: z.number().min(2).max(3).optional()
})

const Skill = z.object({
  title: z.string().min(1),
})
const SkilledUser = User.merge(Skill)

const Data = SkilledUser.parse({
  url: "[email protected]",
  name: "Nancy",
  age: 21,
  title: 'water surfing',
})

console.log(Data) // parses successfully

```

正如您在上面的代码中看到的，`title`数据字段已经合并到了`User`模式对象中，因此 Zod 会安全地解析它。在`title`字段是一个数字的情况下，比如说`20`，Zod 会抛出一个错误，指出`title`只被限制为`z.string().min(1)`的类型。

Zod 的`merge()`属性不像`extends()`那样重写字段。

## Zod 中的类型推理

假设您已经在某个地方定义了自己的类型，并且希望一个新创建的变量从现有的变量中推导出它的类型。在这种情况下，Zod 有一个方法可以推断出它的类型，如下所示:

```
let fullName = z.string(); 
type fullName = z.infer<typeof fullName> // string

const userAge: fullName = 12; // Zod throws a Type Error Exception
const name: fullName = "Nancy"; // Parses safely

```

## 结论

Zod 提供了更多现成的 API，但是我们已经介绍了几乎所有使用 Zod 和 TypeScript 构建模式验证的基本实体。我们还介绍了这些原语的功能有多强大，如果将它们链接在一起，可以为您的应用程序构建非常健壮和灵活的模式设计。

拥有运行时验证总是一个好主意，因为它使您的代码更具可读性，并在将用户输入重定向到服务器之前净化它们。

Zod 强调 DRY 原则，并确保在一个地方定义的模式可以被标记化并用于其他类型，无论是通过推断那些类型还是扩展它们。这使得 Zod 从其他类似的库中脱颖而出。

将 Zod 与强大的编译类型检查系统 TypeScript 结合使用，可以使应用程序在当前的 JAMstack 应用程序中更加健壮。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.