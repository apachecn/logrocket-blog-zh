# TypeScript 中的类型与接口

> 原文：<https://blog.logrocket.com/types-vs-interfaces-in-typescript/>

在 JavaScript 中进行静态类型检查的想法非常棒，TypeScript 的采用每天都在增加。

你开始在你的项目中使用 TypeScript，你创建了你的第一个类型，然后你跳转到你的第一个接口，你让它工作。您的结论是，TypeScript 实际上有助于您的开发并节省了您的宝贵时间，但是当您开始使用 TypeScript 中的类型和接口时，您可能犯了一些错误并且没有遵循最佳实践。

这是许多开发人员的情况，他们并不真正知道类型别名和 TypeScript 中的接口之间的真正区别。

开始使用 TypeScript 非常简单，但有时我们需要更多地考虑对我们来说最好的用例。在这种情况下，是类型还是接口？

## 类型和类型别名

在我们开始讨论 TypeScript 中类型和接口的区别之前，我们需要了解一些事情。

在 TypeScript 中，我们有很多基本类型，比如 string、boolean 和 number。这些是类型脚本的基本类型。你可以在这里查看所有基本类型[的列表。同样，在 TypeScript 中，我们有高级类型，在这些](https://www.typescriptlang.org/docs/handbook/basic-types.html#table-of-contents)[高级类型](https://www.typescriptlang.org/docs/handbook/advanced-types.html)中，我们有被称为[类型别名](https://www.typescriptlang.org/docs/handbook/advanced-types.html#type-aliases)的东西。使用类型别名，我们可以为类型创建一个新的名称，但是我们不能定义一个新的类型。

我们使用`type`关键字来创建一个新的类型别名，这就是为什么有些人可能会感到困惑，认为这是在创建一个新的类型，而他们只是在为一个类型创建一个新的名称。所以，当你听到有人谈论类型和接口之间的区别时，就像在这篇文章中，你可以假设这个人谈论的是类型别名和接口。

我们将使用 [TypeScript Playground](https://www.typescriptlang.org/play/index.html#) 作为代码示例。 [TypeScript Playground](https://www.typescriptlang.org/play/index.html#) 允许我们工作和测试最新版本的 TypeScript(或者我们想要的任何其他版本)，通过使用这个 Playground，我们将节省时间，而不是仅仅为了示例而创建一个新的 TypeScript 项目。

## 类型与接口

在 TypeScript 中，类型和接口之间的区别曾经更加明显，但是随着 TypeScript 的最新版本，它们变得更加相似。

接口基本上是描述数据形状的一种方式，例如，一个对象。

类型是数据类型的定义，例如，联合、基元、交集、元组或任何其他类型。

### 声明合并

有一件事可以用接口来做，但不能用类型来做，那就是声明合并。当 TypeScript 编译器将两个或更多共享相同名称的接口合并到一个声明中时，就会发生声明合并。

假设我们有两个名为`Song`的接口，具有不同的属性:

```
interface Song {
  artistName: string;
};

interface Song {
  songName: string;
};

const song: Song = {
  artistName: "Freddie",
  songName: "The Chain"
};
```

TypeScript 会自动将两个接口声明合并成一个，所以当我们使用这个`Song`接口时，我们将拥有两个属性。

声明合并不适用于类型。如果我们试图创建两个名称相同但属性不同的类型，TypeScript 仍然会抛出一个错误。

```
Duplicate identifier Song.
```

## 扩展和实现

在 TypeScript 中，我们可以轻松地扩展和实现接口。但是这对于类型是不可能的。

TypeScript 中的接口可以扩展类，这是一个非常棒的概念，在更面向对象的编程方式中有很大帮助。我们也可以创建实现接口的类。

例如，假设我们有一个名为`Car`的类和一个名为`NewCar`的接口，我们可以使用一个接口轻松扩展这个类:

```
class Car {
  printCar = () => {
    console.log("this is my car")
  }
};

interface NewCar extends Car {
  name: string;
};

class NewestCar implements NewCar {
  name: "Car";
  constructor(engine:string) {
    this.name = name
  }
  printCar = () => {
    console.log("this is my car")
  }
};
```

## 交集

交集允许我们将多种类型组合成一种类型。要创建交集类型，我们必须使用`&`关键字:

```
type Name = {
  name: “string”
};

type Age = {
  age: number
};

type Person = Name & Age;
```

这里好的一点是，我们可以创建一个新的交集类型，例如，合并两个接口，但不能反过来。我们不能创建一个结合两种类型的接口，因为它不起作用:

```
interface Name {
  name: “string”
};

interface Age {
  age: number
};

type Person = Name & Age;
```

### 联盟

联合类型允许我们创建一个新类型，它可以有一个或几个类型的值。要创建一个联合类型，我们必须使用`|`关键字。

```
type Man = {
  name: “string”
};

type Woman = {
  name: “string”
};

type Person = Man | Woman;
```

例如，与交集类似，我们可以创建一个新的联合类型来组合两个接口，但不能反过来:

```
interface Man {
  name: "string"
};

interface Woman {
  name: "string"
};

type Person = Man | Woman;
```

### 元组

[元组](https://www.typescriptlang.org/docs/handbook/basic-types.html#tuple)是 TypeScript 中一个非常有用的概念，它给我们带来了这种新的数据类型，它包括两组不同数据类型的值。

```
type Reponse = [string, number]
```

但是，在 TypeScript 中，我们只能使用类型而不是接口来声明元组。我们无法使用接口在 TypeScript 中声明元组，但是您仍然可以在接口中使用元组，如下所示:

```
interface Response {
  value: [string, number]
}
```

我们可以看到，使用带有接口的类型可以获得相同的结果。因此，很多开发人员可能会有一个问题——我应该使用类型而不是接口吗？如果是，什么时候应该使用类型？

让我们来了解两者的最佳用例，这样你就不需要为了一个而抛弃另一个。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 我应该用什么？

这个问题非常棘手，你可能会猜测，它的答案取决于你正在构建什么和你正在做什么。

当你需要定义一个新的对象或对象的方法时，接口会更好。例如，在 React 应用程序中，当您需要定义特定组件将要接收的属性时，最好使用接口而不是类型:

```
interface TodoProps {
  name: string;
  isCompleted: boolean
};

const Todo: React.FC<TodoProps> = ({ name, isCompleted }) => {
  ...
};
```

例如，当您需要创建函数时，类型更好。假设我们有一个函数将返回一个名为的对象，这种方法更推荐使用类型别名:

```
type Person = {
  name: string,
  age: number
};

type ReturnPerson = (
  person: Person
) => Person;

const returnPerson: ReturnPerson = (person) => {
  return person;
};
```

在一天结束时，要决定是否应该使用类型别名或接口，您应该仔细考虑和分析情况——您正在做什么，具体的代码，等等。

接口更适合处理对象和方法对象，类型更适合处理函数和复杂类型等。

你不应该开始使用一个而删除另一个。与其这样做，不如开始慢慢重构，考虑什么对特定情况更有意义。

请记住，您可以一起使用两者，它们会工作得很好。这里的想法只是阐明类型和接口之间的区别，以及两者的最佳用例。

## 结论

在本文中，我们了解了更多关于 TypeScript 中类型和接口之间的区别。我们了解了类型别名是 TypeScript 中的高级类型，我们了解了 TypeScript 中类型和接口的最佳用例，以及我们如何在实际项目中应用它们。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.