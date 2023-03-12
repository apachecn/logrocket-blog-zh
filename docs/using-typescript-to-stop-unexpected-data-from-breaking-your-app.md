# 使用 TypeScript 控制运行时的意外数据

> 原文：<https://blog.logrocket.com/using-typescript-to-stop-unexpected-data-from-breaking-your-app/>

在本文中，我们将探索如何使用 TypeScript 类型定义来获得更好的运行时安全性保证。我们将展示如何使用 [runtypes](https://github.com/pelotom/runtypes) 和 [io-ts](https://gcanti.github.io/io-ts/) 库，并解释它们最初为什么存在。

TypeScript 编译器是一个强大的朋友。它将帮助您理解您正在处理哪种类型的数据——函数参数、返回类型、常量、库定义等等。您可以用最少的配置避免令人惊讶的值并找到常见的错误。编译器将使您不必通过测试或在 UI 中手动验证一切，从而节省时间。如今，库趋向于有体面的类型定义，并且许多是用 TypeScript 编写的。使用附加标志，您可以提高代码的(类型)质量:

*   `strict`(推荐–包括`noImplicitThis`、`noImplicitAny`、强制类型)
*   `noEmitOnError`(应检查所有发出的代码)
*   `noImplicitReturns`
*   `noFallthroughCasesInSwitch`

有了严格的配置和大量的输入，你的应用程序能编译并且仍然崩溃吗？是的，它可以！

## 运行时安全

TypeScript 提供编译时安全性。如果在代码中错误地引用了一个熟悉的值，就会出现编译错误。属性名中的拼写错误？编译错误。运行时安全呢？运行时异常是 JavaScript 的一个特性，因此也是 Typescript 的一个特性。例如，`JSON.parse`如果遇到非 JSON 数据就会抛出异常。如果`foo`不存在，`foo.bar()`抛出一个`ReferenceError`。这些可以用`try {…} catch (e) {…}`来捕捉，或者作为拒绝承诺来处理。这很好，但我们可以做得更好。

通常，当抛出意外异常时，罪魁祸首是流入和流出应用程序的数据。假设您可以近乎完美地定义一个 TypeScript 应用程序如何处理符合已知`interface`的数据，这是怎么发生的呢？声明一个接口就像声明“这是我的数据的样子；请强制执行。”好吧，你可以撒谎——或者只是错了。

我将通过三个不同的例子展示如何在运行时处理意外数据。

## 探索未知

我得出结论，现有的静态站点生成器都不行，所以我决定自己构建一个。经过一番努力，我想到了这个:

```
import fs from 'fs'

interface Config {
  siteName: string
  siteUrl: string
  contentPath: string
}

try {
  const configFile = fs.readFileSync('./config.json', 'utf-8')
  const config: Config = JSON.parse(configFile)

  console.info(`🚧 Generating "${config.siteName}"`)

  const pages = fs.readdirSync(config.contentPath)
  // do something with the pages ...

  console.info('✅ Done')
} catch (e) {
  console.error('Something went wrong!', e)
}

```

作为一名经验丰富的开发人员，我对`fs`和`JSON.parse`抛出的异常保持警惕。我的应用程序编译。但是当我运行它的时候，我看到了这个:

```
$ node site_generator.js

🚧 Generating "undefined"
...

```

看起来我的配置文件中有一个打印错误。它拼写为“站点名”，而不是“站点名”这是一个简单的例子，说明进入你的应用程序的所有数据在技术上是未知的。幸运的是，有一种方法可以在运行时处理未知数据。我把 [`runtypes`](https://github.com/pelotom/runtypes) 库引入我的 app。

```
import fs from 'fs'
import { String, Record, Static } from 'runtypes'

const Config = Record({
  siteName: String,
  siteUrl: String,
  contentPath: String
})

// this is equivalent to the "Config" interface from before
type Config = Static<typeof Config>

try {
  const configFile = fs.readFileSync('./config.json', 'utf-8')
  const config: Config = JSON.parse(configFile)

  // The important bit
  Config.check(config)

  console.info(`🚧 Generating "${config.siteName}"`)

  const pages = fs.readdirSync(config.contentPath)
  // do something with the pages ...

  console.info('✅ Done')
} catch (e) {
  console.error('Something went wrong!', e)
}

```

应用程序看起来几乎一样。不同之处在于，我已经使用`runtypes`提供的类型声明了`Config`。术语[记录](https://en.wikipedia.org/wiki/Record_(computer_science))和相关术语字段类似于人们所说的对象和属性，但是因为 JavaScript 中的对象可以是任何东西，(`window`、`Array`等等)。)，区分是有用的。将记录想象成电子表格中的行——它们“只是”数据。`runtypes`从记录中构建的运行时对象有像`check`和`guard`这样的方法来验证一些数据是否与我声明的实际类型定义 *(第 11 行)兼容。*

 *您可能以前使用过一些表单验证库来做类似的事情。在一种形式中，我们验证值。这里，我们验证结构(“字段”及其值)。您可以添加[额外的验证约束](https://github.com/pelotom/runtypes#constraint-checking)来缩小有效值的范围。这对于在 API 处理程序中验证传入的有效负载非常有用。

这可能看起来很难接受。在我解释这一切的含义之前，让我们用相同的错误配置文件运行新版本(你可以在 [CodeSandbox](https://codesandbox.io/s/ts-runtime-safety-node-70dx2) 中尝试)。

```
$ node site_generator_safe.js

Something went wrong! ValidationError: Expected string, but was undefined
    at new ValidationError (./node_modules/runtypes/lib/errors.js:19:28)
    at Object.check (./node_modules/runtypes/lib/runtype.js:23:15)
    < some stack frames omitted >
    at Function.Module.runMain (internal/modules/cjs/loader.js:1047:10)
    at internal/main/run_main_module.js:17:11 {
  key: 'siteName',
  name: 'ValidationError'
}

```

未定义的值被识别。应用程序在有机会使用错误的配置之前就停止了。正如你可能想象的那样，一个稍微高级一点的应用程序会从尽早放弃中受益，而不是在漫长的过程中半途而废。稍微不正确的配置可能会对脚本造成严重破坏，例如，按月向客户收费。

在上面的结果中，我的应用程序在检查错误配置时抛出了一个带有`ValidationError`的异常。这就是`check`方法的工作方式:它将意想不到的数据带入您可以通过捕捉它来管理的领域。有一种方法可以在不使用异常的情况下处理坏数据——我们很快就会谈到这一点。首先，我们来谈谈 web APIs 的可靠性。

## 一个 API 撒谎怎么办？

没有外部数据，很少有 web 或移动应用程序是有用的。2019 年，大多数应用程序从基于云的 [REST 或 graph QL](https://blog.logrocket.com/graphql-vs-rest-what-you-didnt-know/)API 获取数据。这些 API 通常是有版本的，并且有希望附带某种文档，说明您可以预期接收(或者应该发送)什么类型的数据。文档可以是交互式的— [Swagger](https://swagger.io) 是一个很好的工具。

来自可疑设计的错误，比如从 JSON APIs 返回带有 OK (200)状态的 HTML 格式的错误响应，可以在 JSON 解析阶段被捕获。如果您足够幸运，在后端和前端都有可靠的 TypeScript 代码，那么您可以共享类型定义，最好的情况是，保证双方真正理解彼此。现实是，你经常希望另一边的开发人员知道他们在做什么。深思熟虑的开发人员使用集成测试、版本控制和冻结模式来为他们的 API 提供保证。在编写类型定义时，您可能仍然会出错。

在测试和 QA 期间，您所依赖的 API 的变化和任何奇怪的行为都可以用`runtypes`捕捉到。您可以使用运行时类型检查，在您这边构建集成测试，只验证 API 响应是否与您的假设一致(或者相反)。在生产中运行的应用程序可能没有合理的方法来从类型错误中恢复。您仍然可以得到描述性的错误消息或崩溃报告。也可以避免无限(重启)循环。

我将向您展示这在实践中是如何工作的。

## 给我拿一张打字安全的唱片来，好吗？

事实证明，静态站点生成器是一项太大的任务，所以我打算构建一个待办事项应用程序。让我们从使用[获取](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)从 API 检索数据开始。我们将看到让`runtypes`验证传入的数据也会带来其他好处。

```
import { String, Number, Boolean, Record, Static } from 'runtypes'

const Todo = Record({
  userId: Number,
  id: Number,
  title: String,
  completed: Boolean
})

type Todo = Static<typeof Todo>

function getTodo(id: number) {
  fetch(`https://jsonplaceholder.typicode.com/todos/${id}`)
    .then(response => response.json())
    .then(todo => Todo.check(todo))
    // todo is now verified to be a Todo and you can safely access the attributes
    .then(todo => console.log(`Todo: #${todo.id}: ${todo.title}`))
    .catch(e => console.error(e))
}

```

`getTodo`函数获取一些数据，解析 JSON 响应，然后在不同的步骤中对结果进行类型检查。类型检查中的任何错误，以及连接和 JSON 解析相关的错误，都将在`.catch`处理程序中被捕获。检查完类型后，您可以在没有显式类型注释的情况下处理`Todo`的内容。这将在 TypeScript 无法推断本质上未知的数据类型的情况下清理代码。

如果我向 Todo 记录添加一个名为“priority”的新字段，类型为`Number`(API 中没有)，调用`getTodo`会导致`ValidationError: Expected number, but was undefined`。如果我不需要应用程序中的所有字段，我可以指定比 API 提供的更少的字段。

支持 API 响应中可以是`null`的字段。他们长这样:`priority: Number.Or(Null)`。注意 Null 中的大写 N。这是由`runtypes`定义的。

待办事项应用程序使用的是基于承诺的流程。如果我使用了`async / await`，`getTodo`应该是这样的:

```
async function getTodo(id: number) {
  try {
    const response = await fetch(`https://jsonplaceholder.typicode.com/todos/${id}`)
    const data = await response.json()
    const todo = Todo.check(data)

    console.log(`Todo: #${todo.id}: ${todo.title}`)
  } catch (e) {
    console.error(e)
  }
}

```

由您决定哪种格式更适合您的用例。`runtimes`不限制应用程序设计的选项。事实上，现在是讨论如何完全避免与类型检查相关的异常的好时机。

## 作为数据的错误

我个人并不支持运行时异常的想法。我更喜欢使用一种语言或库，它能让我有机会将失败作为数据来处理，而不是像 [GOTO 一样的控制流](https://softwareengineering.stackexchange.com/questions/189222/are-exceptions-as-control-flow-considered-a-serious-antipattern-if-so-why)。失败作为数据是什么样子的？许多人都熟悉回调参数`(err, data)`的 NodeJS 约定，回调参数有时被用作函数返回值。

```
const [err, data] = myFunction("foo", "bar")

```

这种格式可以防止异常，但仍然很麻烦。你必须检查`err`或`data`的存在(想法是其中一个总是`null`，不保证)。这种设计可以被认为具有布尔逻辑——一个动作导致 err 或 data。更复杂的方法是使用联合类型。

```
type Success<T> = {
  success: true
  value: T
}

type Failure = {
  success: false
  message: string
  key?: string
}

type Result<T> = Success<T> | Failure

```

上面的片段来自`runtypes`源代码(我已经删除了导出语句和注释)。一个成功的操作被显示为一个带有相关联的`value`的记录。失败(错误)用`message`描述错误。这个想法不是`runtypes`独有的；它存在于许多编程语言中，如 Rust、Elm、Haskell 和 Swift。它也类似于期权/Maybe 的两重性。让我们看看使用这个想法如何改变`getTodo`函数。

```
function getTodo(id: number): Promise<Result<Todo>> {
  return fetch(`https://jsonplaceholder.typicode.com/todos/${id}`)
    .then(response => response.json())
    .then(Todo.validate)
}

getTodo(1)
  .then(result => {
    if (result.success) {
      console.log(`Todo: #${result.value.id}: ${result.value.title}`)
    } else {
      // result is Failure
      console.error(result.message)
    }
  })
  // the request or JSON parsing can still fail
  .catch(e => console.error(e))

```

对于那些熟悉 TypeScript 中泛型的人来说，函数返回类型是有意义的。如果在别人看来很怪异，不要惊慌！它只是不同类型数据中的特定类型数据。如果您愿意，可以使用函数的承诺链中的`result`,但是我已经选择将逻辑移出函数。这样，`Todo` s 可以被获取和验证，您可以对结果做任何您想做的事情。

关于上面的`.catch`处理程序，如果`fetch`与`Result`一起开箱即用，就有可能用我们的验证逻辑来链接它。您可以构建一个包装器来捕捉异常并返回一个`Result`。这超出了本文的范围。一些语言的标准库中内置了一个类似结果的类型，任何可能失败的东西都会使用它，这使得事情比默认情况下在 TS/JS 生态系统中更加安全和方便。

如果你已经走了这么远，太棒了！现在你有了一个新工具，可以极大地提高应用程序的质量。你可以在 [CodeSandbox](https://codesandbox.io/s/merhk?fontsize=14&hidenavigation=1&theme=dark) 里玩这个版本。如果我还能引起你的注意，我有东西要展示。

## 请不要报警，也不要惊喜

想想这个命题:如果你的应用程序可以编译，它就可以工作。如果你可以依靠它，你只需要修复任何编译错误，你就会有一个完全可预测的应用程序。然后你只需要确保你的算法是有效的，并且你没有忘记渲染一些结果。这使你工作时没有通常的焦虑和怀疑。

[Elm 编程语言](https://elm-lang.org)以承诺零运行时异常而闻名。这是一个重大的承诺。考虑第三方库:可以说，你怎么能确定别人创作的代码是完全安全的呢？只有当编程语言的作者仔细选择每个人都遵守的有限操作集时，这才有可能。好奇 Elm 是如何做到这一点的？所有库都是纯 Elm，从一个中央包注册中心检索，该注册中心执行以下操作:

*   语义版本——如果语义版本错误，库将不会编译。
*   安全性——库是用 Elm 编写的，所以每个人的代码都同样安全，不会出现异常。
*   库函数中的任何最终的`Result`都将由你来处理——库不会引起混乱。如果您愿意，您也可以忽略库中的任何错误

Elm 有自己的运行时，它要求您编码/解码(验证)流入和流出应用程序的所有数据。作为一门纯粹的函数式语言，app 和 libs 中的所有函数只处理作为其参数提供的值，并且只能返回对[副作用](https://jrsinclair.com/articles/2018/how-to-deal-with-dirty-side-effects-in-your-pure-functional-javascript/)的描述，而不能触发它们。每个函数都必须有一个有意义的返回值。

我是榆树的忠实粉丝。每当我选择使用 TypeScript(或者不能使用 Elm)的时候，我都尽量把 Elm 的远大目标记在心里。虽然 Elm 不像 TypeScript 那样受欢迎，但它非常有影响力。这就是为什么你会在 JavaScript 生态系统中找到直接模仿 Elm 特性的库。最流行的可能是 Redux，它是 Elm 状态管理的近似。让我给你介绍一下 [io-ts](https://gcanti.github.io/io-ts/) ，这是一种 FP/榆树风味的`runtypes`替代品。

## 无论哪种方式，都没有运行时异常

看看另一个版本的获取示例。

```
import * as t from 'io-ts'
import { Either, isRight } from 'fp-ts/lib/Either'

const Todo = t.type({
  userId: t.number,
  id: t.number,
  title: t.string,
  completed: t.boolean
})

type Todo = t.TypeOf<typeof Todo>

function getTodo(id: number): Promise<Either<t.Errors, Todo>> {
  return fetch(`https://jsonplaceholder.typicode.com/todos/${id}`)
    .then(response => response.json())
    .then(Todo.decode)
}

getTodo(1)
  .then(result => {
    // Success
    if (isRight(result)) {
      console.log(`Todo: #${result.right.id}: ${result.right.title}`)
    // Failure
    } else {
      console.error(result.left)
    }
  })
  .catch(e => console.error(e))

```

乍一看，这个例子的结构类似于`runtypes`例子的`Result`版本。相同的保证，没有类型相关的异常。甚至我定义 Todo 记录的方式也与以前的工作非常相似。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

注意到`fp-ts`导入了吗？它是函数式编程世界中常见数据类型的集合。`io-ts`在此基础上构建。有一个共享核心类型的库的[生态系统。如果你花时间去理解像](https://gcanti.github.io/fp-ts/introduction/ecosystem) [Task](https://gcanti.github.io/fp-ts/modules/Task.ts) 这样的与人们通常使用的 TypeScript (Promises)相关的概念，你可以在熟悉的语言环境中学习高级函数式编程。要真正沉浸在函数式编程中，我建议您至少尝试一下 Elm。

类似`Result`的`Either`型分为左右两侧。按照惯例，左边表示错误，右边表示有效值。如果这个命名约定似乎很难记住，我不怪你；我自己更喜欢`Result`的命名。你可以通过对自己说“好像我有`Right`数据”来提醒自己类型定义如下。

```
interface Left<E> {
  readonly _tag: 'Left'
  readonly left: E
}

interface Right<A> {
  readonly _tag: 'Right'
  readonly right: A
}

type Either<E, A> = Left<E> | Right<A>

```

`runtypes`’`Result`有一些好处。左侧的`E`值允许除字符串之外的其他错误表示——还记得`message` s 吗？用字符串而不是布尔作为标签使得它与其他[标签的联合](https://mariusschulz.com/blog/tagged-union-types-in-typescript)更加兼容(字符串允许在一个联合中有两个以上的可能值)。

到目前为止，使用`io-ts`优于`runtypes`的唯一实际好处似乎与命名和兼容性有关。如果我们后退一步，将经过验证的数据作为运营链的一部分，其他优势就会变得显而易见。忘掉待办事项应用程序吧——下一个例子有实际的业务逻辑。

我想打印一张购物摘要的收据，如下所示:

```
Receipt
========
Date: 2019-12-01T12:21:28.478Z
Total: 12.80€
Tax: 3.07€

```

一旦我熟悉了`fp-ts`的数据类型和它们的操作，应用程序就自然而然地出现了。

```
import * as t from 'io-ts'
import { map, fold } from 'fp-ts/lib/Either'
import { fold as foldMonoid, monoidSum } from 'fp-ts/lib/Monoid'
import { map as mapArray } from 'fp-ts/lib/Array'
import { pipe } from 'fp-ts/lib/pipeable'

const Purchase = t.type({
  vat: t.number,
  items: t.array(
    t.type({
      name: t.string,
      amount: t.number,
      priceEur: t.number
    })
  )
})

const Receipt = t.type({
  date: t.string,
  totalEur: t.number,
  taxEur: t.number
})

type Purchase = t.TypeOf<typeof Purchase>
type Receipt = t.TypeOf<typeof Receipt>

const data: unknown = {
  vat: 24,
  items: [
    { name: 'banana', amount: 3, priceEur: 0.7 },
    { name: 'coffee', amount: 1, priceEur: 3.5 },
    { name: 'beer', amount: 6, priceEur: 1.2 }
  ]
}

function purchaseToReceipt(purchase: Purchase): Receipt {
  const total = pipe(
    purchase.items,
    mapArray(item => item.amount * item.priceEur),
    foldMonoid(monoidSum)
  )

  return {
    date: new Date().toISOString(),
    totalEur: total,
    taxEur: (purchase.vat / 100) * total
  }
}

function formatReceipt(receipt: Receipt): string {
  return `
Receipt
========
Date: ${receipt.date}
Total: ${receipt.totalEur.toFixed(2)}€
Tax: ${receipt.taxEur.toFixed(2)}€
          `
}

function formatErrors(errors: t.Errors): string {
  return `Invalid data: ${JSON.stringify(errors)}`
}

const summary: string = pipe(
  Purchase.decode(data),
  // "map" only changes the "Right" value and keeps Left intact
  map(purchaseToReceipt),
  // Apply a certain function to left and right values (if present)
  fold(formatErrors, formatReceipt)
)

console.log(summary)

```

使用功能方法有什么好处？看到我建的`pipe`线了吗？只要一个操作的返回值是下一个操作的有效参数，这些操作就构成。由于像`Either`这样的类型的值在结构上是相同的，它们可以使用通用函数进行转换。有两个转换管道:

1.  计算商品价格的总和(`purchaseToReceipt`)
2.  将未知的`data`转换成摘要字符串

你可能很熟悉如何用类似于`items.filter(fn)`的操作来转换好的 ol' `Array`。这些是绑定到数组实例的操作。在这里，您可以使用任何地方的值，并根据自己的喜好组合它们。

我只是触及了你能对`fp-ts`家族做些什么的皮毛。如果这是你喜欢的东西，那么继续使用(仅仅)这些新概念来构建你自己的应用程序。你也可以试试 [CodeSandbox](https://codesandbox.io/s/dw1me?fontsize=14&hidenavigation=1&theme=dark) 中的例子。现在让我们总结一下我们在这次旅行中所做的事情。

## 插件:识别并消除生产中的运行时错误

跟踪产品 TypeScript 异常或错误的原因既耗时又令人沮丧。如果您对监控错误并了解它们如何影响用户感兴趣，请尝试 LogRocket 。[![LogRocket Dashboard Free Trial Banner](img/e8a0ab42befa3b3b1ae08c1439527dc6.png)](https://logrocket.com/signup/)[https://logrocket.com/signup/](https://logrocket.com/signup/)

LogRocket 就像是网络应用的 DVR，记录下你网站上发生的每一件事。LogRocket 使您能够聚合和报告错误，以查看它们发生的频率以及它们影响了多少用户群。您可以轻松地重放发生错误的特定用户会话，以查看导致错误的用户操作。

LogRocket 让你的应用程序记录带有标题+正文的请求/响应，以及关于用户的上下文信息，以全面了解问题。它还记录页面上的 HTML 和 CSS，甚至可以重建最复杂的单页面应用程序的像素级完美视频。

增强您的 JavaScript 错误监控能力–––[开始免费监控](https://logrocket.com/signup/)。

## 结论

我们从讨论运行时异常和构建一个工作正常但不可靠的静态站点生成器开始。我们向生成器添加了`runtypes`定义，并在配置关闭时让它提前停止。

然后，我们进入前台，试图获取一些待办事项。我们注意到运行时类型检查有助于识别我们的期望和 API 响应之间的差异。我们的应用程序仍然会抛出异常，所以我们尝试了一种不同的方式来使用错误作为数据。

然后，我们将`runtypes`与`io-ts`进行比较，看看这个`Result`的东西是否有什么好的(名字为`Either`)。一旦看起来有一个很好的理由使用这种双面模式，我们就探索如果我们沉浸在`fp-ts`生态系统中，事情会是什么样子。函数式编程有机会在收据生成脚本中展示其优势。我们以一种清晰的方式对其他运算的结果应用运算。

您应该使用这些不同风格的运行时类型检查中的哪一种？这取决于您正在构建什么样的应用程序。一个简单的自顶向下的脚本可能是好的，如果它只是在一个异常抛出时崩溃。长时间运行的应用程序，如移动应用程序，可能会从异常恢复中受益，或者更好的是，将错误作为数据处理。捕捉异常和处理错误让您可以自由选择是需要用户交互还是应该重试刚刚失败的事情。一些次要的事情甚至可以被有控制地忽略。无论如何，你最终可能会得到一个更可靠的应用。

我专注于验证完全外部的数据。应用程序也从用户输入中创建数据。基于类型脚本的表单库，如 [Formik](https://jaredpalmer.com/formik) 理解你的界面。它可以帮助您处理不可靠的用户输入可能导致的错误。这不同于 API 调用，因为应用程序决定如何收集数据。您可以使用[约束](https://github.com/pelotom/runtypes#constraint-checking)或[编码器](https://github.com/gcanti/io-ts#the-idea)将`runtypes`和`io-ts`用于表单验证。使用表单库使用的任何东西可能更符合人体工程学。福米克用[没错](https://github.com/jquense/yup)。

除了输入和输出，应用程序通常还有 UI 的内部状态。您可以使用状态机来验证应用程序的逻辑。状态机定义受控的运行时状态转换，并记录您的意图。它们也会引发副作用。见: [XState](https://github.com/davidkpiano/xstate) ，用打字稿写的。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.*