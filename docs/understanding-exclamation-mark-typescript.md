# 理解 TypeScript 中的惊叹号

> 原文：<https://blog.logrocket.com/understanding-exclamation-mark-typescript/>

感叹号`!`在 TypeScript 中被称为非空断言操作符。在本文中，我们将交替使用这些术语。但是这个操作符是做什么的呢？

在本文中，我们将了解:

## 什么是打字稿感叹号？

非空断言操作符告诉 TypeScript 编译器，作为可选类型的值不能是`null`或`undefined`。例如，如果我们定义一个变量可能是一个字符串或者是未定义的，那么`!`操作符告诉编译器忽略它未定义的可能性。

## 感叹号在打字稿中的作用

假设一个变量被定义为可能为空或未定义，就像这样:

```
let x: string | undefined 

```

或者，假设函数被定义为接受可选参数，如下所示:

```
function printString (str ?: string) { … }

```

在这些情况下，如果我们试图将该变量作为一个明确的类型来引用，那么 TypeScript 编译器会给我们一个错误消息，如下所示:

```
Object is possibly 'undefined'. ts(2532)

```

我们可以使用非空断言操作符明确地告诉编译器这个变量有一个值，并且不是`null`或`undefined`。让我们回顾几个例子，以便更好地理解 TypeScript 中的感叹号。

### 示例 1:对接受`string`的函数使用`string | null`类型的变量

假设我们定义了一个类型为`string | null`的变量`word`。这意味着在我们的代码中，`word`既可以保存一个`string`值，也可以保存一个`null`值。

如果我们试图使用一个仅对`word`上的`string`类型可用的函数，TypeScript 将拒绝它，因为在我们的代码中有可能`word`持有一个`null`值类型:

```
let word : string | null = null
const num = 1
if (num) {
    word = "Hello World!"    
}
console.log(word.toLowerCase()) // Error: Object is possibly 'null'.ts(2531)

```

使用`!`非空断言操作符，我们可以告诉 TypeScript 我们确信`word`永远不会成为`null`(或`undefined`)，因此它可以放心地对它应用字符串函数:

```
let word : string | null = null
const num = 1
if (num) {
    word = "Hello World!"    
}
console.log(word!.toLowerCase())

```

有了这个小小的添加，编译器不再认为`word`有可能为空。

### 示例 2:将可选参数的值赋给函数中的变量

在另一个例子中，假设我们创建了一个接受可选参数`personName`的函数`printName`。

请注意，使用`?:`将函数参数定义为可选与将类型定义为可能未定义是一样的。例如，`arg?: string`与`arg: string | undefined`相同。

如果我们试图将可选参数`personName`重新分配给另一个类型为`string`的变量，将会发生以下情况:

```
function printName(personName?: string) {
    const fullName: string = personName 
/** 
 * Error: Type 'string | undefined' is not assignable to type 'string'. 
 * Type 'undefined' is not assignable to type 'string'.
 */
    console.log(`The name is ${fullName}`)
}

```

我们可以使用`!`操作符修复上面代码片段中抛出的类型脚本错误:

```
function printName(personName?: string) {
    const fullName: string = personName! 
    console.log(`The name is ${fullName}`)
}

```

现在，编译器知道 personName 不能为空或未定义，这使得它可以赋给类型`string`。

### 示例 3:打印函数中可选对象参数的属性

在我们的最后一个例子中，我们将定义一个类型`Person`和一个函数`printName`，该函数接受类型`Person`的可选参数。让我们看看如果我们尝试使用`printName`来打印`Person`的 name 属性会发生什么:

```
interface Person {
    name: string
    age: number
}

function printName(person?: Person) {
    console.log(`The name is ${person.name}`) // Error: Object is possibly 'undefined'. ts(2532)
}

```

让我们使用`!`操作符来修复这个类型脚本错误:

```
interface Person {
    name: string
    age: number
}

function printName(person?: Person) {
    console.log(`The name is ${person!.name}`)
}

```

注意，TypeScript 有一个替代方法来引用可能为空或未定义的对象上的属性和函数，称为可选链接操作符`?.`。例如，如果变量未定义或为空，`person?.name`或`word?.toString()`将返回`undefined`。

然而，可选的链接操作符`?.`无法解决第二个例子中的类型错误，在这个例子中，我们试图将变量类型`string | undefined`的值赋给变量类型`string`。在本文最后一节中了解更多关于[可选链接的信息。](#alternatives-using-typescript-exclamation-mark)

## 打字稿感叹号的常见用例

正如我们在例子中看到的，当我们希望 TypeScript 将变量作为一个实体类型时,`!`操作符非常有用。当我们确定没有这样的情况时，这可以防止我们必须处理任何空的或未定义的情况。

现在我们已经看到了一些例子来更好地理解 TypeScript 感叹号，让我们来看看这个操作符的一些流行用例。

### 在数组上执行查找

假设我们有一个对象数组，我们想挑选一个具有特定属性值的对象，如下所示:

```
interface Person {
    name: string
    age: number
    sex: string
}

const people: Person[] = [
  {
      name: 'Gran',
      age: 70,
      sex: 'female'
  },
  {
      name: 'Papa',
      age: 72,
      sex: 'male'
  },
  {
      name: 'Mom',
      age: 35,
      sex: 'female'
  },
  {
      name: 'Dad',
      age: 38,
      sex: 'male'
  }
]

const femalePerson = people.find(p => p.sex === 'female')

```

在上面的代码片段中，TypeScript 将把`femalePerson`的类型定义为`Person | undefined`,因为`people.find`可能不会产生任何结果——换句话说，它将是未定义的。

然而，如果`femalePerson`的类型为`Person | undefined`，我们将无法将它作为参数传递给类型为`Person`的函数。

当我们在这些数组上执行查找时，我们通常确信它们已经定义了值，因此我们不相信存在任何未定义的情况。我们的`!`操作符可以将我们从额外的——或不必要的——无效或未定义的案例处理中解救出来。

添加非空断言操作符，如下所示:

```
const femalePerson = people.find(p => p.sex === 'female')!

```

这将使`femalePerson`具有类型`Person`。

### 反应引用和事件处理

[React 引用用于访问](https://blog.logrocket.com/complete-guide-react-refs/)呈现的 HTML DOM 节点或 React 元素。使用`React.createRef<HTMLDivElement>()`创建引用，然后使用`ref`属性将其附加到元素。

为了使用 React refs，我们访问当前属性`ref.current`。在元素被渲染之前，`ref.current`可能是`null`，因此它具有以下类型:

```
HTMLDivElement | null

```

要将一个事件附加到`ref.current`，我们首先必须处理可能的`null`值。这里有一个例子:

```
import React from 'react'

const ToggleDisplay = () => {
    const displayRef = React.createRef<HTMLDivElement>()

    const toggleDisplay = () => {
        if (displayRef.current) {
            displayRef.current.toggleAttribute('hidden')
        }
    }

    return (
        <div>
            <div class="display-panel" ref="displayRef">
                <p> some content </p>
            </div>
            <button onClick={toggleDisplay}>Toggle Content</button>
        <div>
    )
}

```

在上面的代码片段中，在调用`toggleAttribute`函数之前，我们必须使用`if`语句来处理`displayRef.current`的类型检查。

在大多数情况下，我们确信如果按钮`onClick`事件被触发，那么我们的元素已经被渲染了。因此，没有必要进行检查。使用`!`操作符可以消除这种不必要的检查，就像这样:

```
const displayRef = React.createRef<HTMLDivElement>()

const toggleDisplay = () => displayRef.current!.toggleAttribute('hidden')

return (
        <div>
        ...

```

## 在 TypeScript 中使用感叹号的缺点是

`!`操作符不会改变代码的运行时行为。如果您断言的值不是`null`或`undefined`实际上是`null`或`undefined`，将会出现一个错误并中断您代码的执行。

记住，TypeScript 和 JavaScript 的区别在于类型的断言。在 JavaScript 中，我们不需要也不使用`!`操作符，因为没有严格的类型限制。

JavaScript 变量可以用`string`进行实例化，并在代码执行期间更改为`object`、`null`或`number`。这就让开发人员来处理不同的情况。

使用`!`操作符剥夺了 TypeScript 防止运行时类型错误的“超级能力”。因此，使用`!`操作符并不是最佳实践。

### 非空断言的单个感叹号`!`

TypeScript 优于 JavaScript 的地方在于它为我们的代码提供了类型安全。然而，我们有时可能希望禁用 TypeScript 的严格类型检查—例如，为了灵活性或向后兼容性。在这种情况下，我们可以使用非空断言操作符`!`。

虽然这是一个有用的特性，但我鼓励您探索更安全的类型断言方法。通过[将`typescript-eslint`包](https://blog.logrocket.com/linting-typescript-using-eslint-and-prettier/)添加到您的项目中，[应用`no-non-null-assertion` lint 规则](https://github.com/typescript-eslint/typescript-eslint/blob/main/packages/eslint-plugin/docs/rules/no-non-null-assertion.md)，您可以进一步阻止在您的项目中和您的团队中使用这个操作。

## 使用 TypeScript 感叹号的替代方法

您可以使用可选的链接或类型谓词来替代非空断言。

[可选链接是一种打字稿速记](https://blog.logrocket.com/16-useful-typescript-javascript-shorthands-know/#optional-chaining)，它允许我们轻松地处理变量是否被定义的情况。当变量未定义或为空时，参考值默认为值`undefined`。下面是一个可选链接的示例:

```
interface Person {
    name: string
    age: number
    sex: string
}

function printName(person?: Person): void {
    console.log('The name of this person is', person?.name)
}

```

在上面的例子中，如果`person`未定义，我们的打印输出如下:

```
'The name of this person is undefined'
```

[在类型脚本](https://www.typescriptlang.org/docs/handbook/2/narrowing.html#using-type-predicates)中使用类型谓词是通过定义一个函数来完成的，该函数执行布尔测试并以`arg is Type`的形式返回类型谓词。这里有一个例子:

```
interface Person {
    name: string
    age: number
    sex: string
}

function validatePerson(person?: Person) person is Person {
    return !!person
}

```

使用这个类型谓词，我们可以在执行任何进一步的操作之前首先验证对象，如下所示:

```
function printName(person?: Person) {
    if (!validatePerson(person)) {
        console.log('Person is invalid')
        return
    }

    console.log(`The name is ${person.name}`)
}

```

## 打字稿中的双感叹号`!!`

当我们谈到感叹号`!`时，TypeScript 也使用双感叹号`!!`将非布尔类型值转换(也称为 cast)为布尔类型。这里有一个例子:

```
const emptyStr = ''
const nonEmptyStr = 'test'

const emptyStrBool = !!emptyStr //false
const nonEmptyStrBool = !!nonEmptyStr //true

```

在上面的例子中，我们使用`!!`操作符将字符串变量转换为布尔型。但是你可能想知道，为什么这两个字符串有不同的布尔结果？答案是一个字符串(空字符串)为假，而另一个(非空字符串)为真。让我们稍微探索一下。

### 真值和假值

true/falsy 指的是值在条件语句中的求值方式，而不管它们的实际类型。

想想`if/else`的陈述，他们评估的是表达的真假，而不是它的实际价值。以下是一些真实/虚假的表达:

```
const num1 = 12
const num2 = 26

// expression 1 - falsey
if (num1 > num2) {
   ...
}

// expression 2 - truthy
if (num1) {
   ...
}

```

总之，当一个 falsy 表达式被转换成 Boolean 时，它的计算结果是`false`，如果它的计算结果是`true`，那么它就是 the。

*注意，在 TypeScript 中(在 JavaScript 中也是如此)，空值如* `0` *、* `''` *、* `undefined` *和* `null` *都是假值，在转换为布尔值时将计算为* `false` *值。*

### 布尔转换的双感叹号`!!`

TypeScript(和 JavaScript)允许您使用双感叹号简写将非布尔值转换为布尔值。这在需要使用非布尔值结果或变量返回布尔值的 TypeScript(具有严格的类型定义)中尤其有用。

## 结论

TypeScript 优于 JavaScript 的地方在于它为我们的代码提供了类型安全。然而，我们有时可能希望禁用 TypeScript 的严格类型检查—例如，为了灵活性或向后兼容性。在这种情况下，我们可以使用非空断言操作符。

虽然这是一个有用的特性，但我鼓励您探索更安全的类型断言方法。您可以更进一步，通过[将](https://blog.logrocket.com/linting-typescript-using-eslint-and-prettier/) `[typescript-eslint](https://blog.logrocket.com/linting-typescript-using-eslint-and-prettier/)` [包](https://blog.logrocket.com/linting-typescript-using-eslint-and-prettier/)添加到您的项目中，并[应用](https://github.com/typescript-eslint/typescript-eslint/blob/main/packages/eslint-plugin/docs/rules/no-non-null-assertion.md) `[no-non-null-assertion](https://github.com/typescript-eslint/typescript-eslint/blob/main/packages/eslint-plugin/docs/rules/no-non-null-assertion.md)` [lint 规则](https://github.com/typescript-eslint/typescript-eslint/blob/main/packages/eslint-plugin/docs/rules/no-non-null-assertion.md)，来防止在您的项目和团队中使用该操作。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.