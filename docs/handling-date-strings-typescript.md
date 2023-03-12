# 在 TypeScript 中处理日期字符串

> 原文：<https://blog.logrocket.com/handling-date-strings-typescript/>

在我最近的一个项目中，我不得不处理多个自定义的字符串形式的日期表示，比如`YYYY-MM-DD`和`YYYYMMDD`。由于这些日期是字符串变量，TypeScript 默认推断出`string`类型。虽然这在技术上没有错，但是使用这样的类型定义是很宽泛的，这使得很难有效地使用这些日期字符串。例如，`const dog = 'alfie'`也被推断为`string`类型。

在本文中，我将向您介绍我通过输入这些日期字符串来改善开发人员体验和减少潜在错误的方法。你可以[跟随这个要点](https://gist.github.com/aperkaz/580e72b98eba5afac30549387562655d)。我们开始吧！

## 目录

在我们开始编写代码之前，让我们简要回顾一下我们将用来实现目标的 TypeScript 特性，[模板文字类型](https://www.typescriptlang.org/docs/handbook/2/template-literal-types.html)和通过类型谓词缩小的[。](https://www.typescriptlang.org/docs/handbook/2/narrowing.html#using-type-predicates)

## 模板文字类型

在 TypeScript v4.1 中引入的模板文本类型与 JavaScript 模板文本共享语法，但作为类型使用。类型模板文本解析为给定模板的所有字符串组合的并集。这听起来可能有点抽象，所以让我们来看看它的实际应用:

```
type Person = 'Jeff' | 'Maria'
type Greeting = `hi ${Person}!` //   Template literal type

const validGreeting: Greeting = `hi Jeff!` // 
//  note that the type of `validGreeting` is the union `"hi Jeff!" | "hi Maria!`
const invalidGreeting: Greeting = `bye Jeff!` // 
// Type '"bye Jeff!"' is not assignable to type '"hi Jeff!" | "hi Maria!"

```

模板文字类型非常强大，允许您对这些类型执行泛型类型操作。比如[大写](https://www.typescriptlang.org/docs/handbook/2/template-literal-types.html#capitalizestringtype):

```
type Person = 'Jeff' | 'Maria'
type Greeting = `hi ${Person}!`
type LoudGreeting = Uppercase<Greeting> // Capitalization of template literal type

const validGreeting: LoudGreeting = `HI JEFF!` // 
const invalidGreeting: LoudGreeting = `hi jeff!` // 
// Type '"hi Jeff!"' is not assignable to type '"HI JEFF!" | "HI MARIA!"

```

## 类型谓词缩小

例如，在下面的示例中，TypeScript 在缩小类型方面做得非常出色:

```
let age: string | number = getAge();

// `age` is of type `string` | `number`
if (typeof age === 'number') {
  // `age` is narrowed to type `number`
} else {
  // `age` is narrowed to type `string`
}

```

也就是说，在处理自定义类型时，告诉 TypeScript 编译器如何执行缩小可能会有所帮助，例如，当我们在执行运行时验证后想要缩小到某个类型时。在这种情况下，类型谓词收缩或用户定义的类型保护就派上了用场！

在下面的例子中，[`isDog`类型保护通过检查类型属性来帮助缩小`animal`变量](https://blog.logrocket.com/how-to-use-type-guards-typescript/)的类型:

```
type Dog = { type: 'dog' };
type Horse = { type: 'horse' };

//  custom type guard, `pet is Dog` is the type predicate
function isDog(pet: Dog | Horse): pet is Dog {
  return pet.type === 'dog';
}

let animal: Dog | Horse = getAnimal();
// `animal` is of type `Dog` | `Horse`
if (isDog(animal)) {
  // `animal` is narrowed to type `Dog`
} else {
  // `animal` is narrowed to type `Horse`
}

```

## 键入日期字符串

既然我们已经熟悉了 TypeScript 的构造块，那就让我们的日期字符串防弹吧。为了简洁起见，这个例子将只包含`YYYYMMDD`日期字符串的代码。所有的代码都可以在[下面的](https://gist.github.com/aperkaz/580e72b98eba5afac30549387562655d)中找到。

首先，我们需要定义模板文字类型来表示所有类似日期字符串的联合:

```
type oneToNine = 1|2|3|4|5|6|7|8|9
type zeroToNine = 0|1|2|3|4|5|6|7|8|9
/**
 * Years
 */
type YYYY = `19${zeroToNine}${zeroToNine}` | `20${zeroToNine}${zeroToNine}`
/**
 * Months
 */
type MM = `0${oneToNine}` | `1${0|1|2}`
/**
 * Days
 */
type DD = `${0}${oneToNine}` | `${1|2}${zeroToNine}` | `3${0|1}`
/**
 * YYYYMMDD
 */
type RawDateString = `${YYYY}${MM}${DD}`;

const date: RawDateString = '19990223' // 
const dateInvalid: RawDateString = '19990231' //31st of February is not a valid date, but the template literal doesnt know!
const dateWrong: RawDateString = '19990299'//  Type error, 99 is not a valid day

```

如上例所示，模板文字类型有助于指定日期字符串的形状，但是对于这些日期没有实际的验证。因此，编译器将`19990231`标记为有效日期，即使它不是，因为它符合模板的类型。

此外，当检查上面的变量如`date`、`dateInvalid`和`dateWrong`时，您会发现编辑器显示了这些模板文字的所有有效字符串的并集。虽然有用，但我更喜欢设置[名义类型](https://michalzalecki.com/nominal-typing-in-typescript/#approach-4-intersection-types-and-brands)，这样有效日期字符串的类型就是`DateString`而不是`"19000101" | "19000102" | "19000103" | ...`。当添加用户定义的类型保护时，标称类型也将派上用场:

```
type Brand<K, T> = K & { __brand: T };
type DateString = Brand<RawDateString, 'DateString'>;

const aDate: DateString = '19990101'; // 
// Type 'string' is not assignable to type 'DateString'

```

为了确保我们的`DateString`类型也表示有效的日期，我们将设置一个用户定义的类型保护来验证日期并缩小类型:

```
/**
 * Use `moment`, `luxon` or other date library
 */
const isValidDate = (str: string): boolean => {
  // ...
};

//User-defined type guard
function isValidDateString(str: string): str is DateString {
  return str.match(/^\d{4}\d{2}\d{2}$/) !== null && isValidDate(str);
}

```

现在，让我们看几个例子中的日期字符串类型。在下面的代码片段中，用户定义的类型保护被应用于类型收缩，从而允许 TypeScript 编译器将类型细化为比声明的类型更具体的类型。然后，在工厂函数中应用类型保护，从未组织的输入字符串创建有效的日期字符串:

```
/**
 *   Usage in type narrowing
 */

// valid string format, valid date
const date: string = '19990223';
if (isValidDateString(date)) {
  // evaluates to true, `date` is narrowed to type `DateString` 
}

//  valid string format, invalid date (February doenst have 31 days)
const dateWrong: string = '19990231';
if (isValidDateString(dateWrong)) {
  // evaluates to false, `dateWrong` is not a valid date, even if its shape is YYYYMMDD 
}

/**
 *   Usage in factory function
 */

function toDateString(str: RawDateString): DateString {
  if (isValidDateString(str)) return str;
  throw new Error(`Invalid date string: ${str}`);
}

//  valid string format, valid date
const date1 = toDateString('19990211');
// `date1`, is of type `DateString`

//  invalid string format
const date2 = toDateString('asdf');
//  Type error: Argument of type '"asdf"' is not assignable to parameter of type '"19000101" | ...

//  valid string format, invalid date (February doenst have 31 days)
const date3 = toDateString('19990231');
//  Throws Error: Invalid date string: 19990231

```

## 结论

我希望这篇文章能够揭示 TypeScript 在键入自定义字符串的上下文中的能力。请记住，这种方法也适用于其他自定义字符串，如自定义的`user-ids`、`user-XXXX`，以及其他日期字符串，如`YYYY-MM-DD`。

当组合用户定义的类型保护、模板文字字符串和名义类型时，可能性是无限的。有问题一定要留下评论，编码愉快！

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.