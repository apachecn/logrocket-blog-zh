# 在 TypeScript - LogRocket 博客中构建类型安全字典

> 原文：<https://blog.logrocket.com/building-type-safe-dictionary-typescript/>

字典是编程语言中常见的数据结构。作为 JavaScript 开发人员，使用类型安全的字典非常重要，因为如果我们在同一个字典中存储不同类型的数据，就会出现冲突或意外问题。

在本文中，我们将介绍如何使用 TypeScript 构建类型安全的字典。

## 什么是类型安全字典？

首先，我们需要知道什么是字典。在编程语言中，字典是一种典型的数据结构，它以键值对的形式存储数据。然而，JavaScript 不提供`Dictionary`类型。幸运的是，我们可以用一些简单的方法创建一个类型安全的字典。

## 在 JavaScript 中使用`Object`类型

在 JavaScript 中创建字典有两种主要方法:使用`Object`类型和使用键值对。JavaScript 中最流行的实现是用`Object`类型创建一个。让我们使用下面的代码创建一个字典:

```
// Using the built-in Object
let dictionaryViaObject = new Object();

// Using the Object literal notation
let dictionaryViaLiteral = {};

```

我们可以用初始数据作为键值对创建一个字典:

```
let dictionaryViaLiteral = {
  'firstName': 'Gapur',
  'lastName': 'Kassym',
  'country': 'Kazakhstan'
};

```

我们用字符串类型的键和值创建了`dictionaryViaLiteral`字典。

如果您想要更改或向字典中添加值，可以通过调用字典键来设置新值，如下所示:

```
// Using bracket
dictionaryViaLiteral['firstName'] = 'New Name';

// Using directly by property name via dot
dictionaryViaLiteral.firstName = 'Tom';

```

我们可以通过直接调用属性名或索引器从字典中访问值:

```
// Using bracket/indexer
const firstName = dictionaryViaLiteral['firstName'];

// Using directly by property name via dot
const firstName = dictionaryViaLiteral.firstName;

```

## 在 JavaScript 中使用`Map`

一个`[Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map)`是键值对的集合，就像一个对象一样。主要区别在于`Map`允许您使用任何类型的键和值。`Map`提供了惊人的实例方法来操作字典。如果你有兴趣，可以在这里阅读更多[。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map#instance_methods)

```
// Using Map
const dictionaryViaMap = new Map();

// Add value using string key
dictionaryViaMap.set("1", "string1");

// Add value using number key
dictionaryViaMap.set(1, "number1");     

// Add value using boolean key
dictionaryViaMap.set(true, "boolean1");

```

`Map`通过任何类型的键存储值，因此，它们返回两个不同的值:

```
// Return string1
const string1 = dictionaryViaMap.get('1');

// Return number1
const number1 = dictionaryViaMap.get(1);

```

为了更新`Map`字典中的值，我们应该通过键调用 set 方法:

```
// Update the value
dictionaryViaMap.set('1', 'updatedString1');

```

## TypeScript 中的字典类型错误

以前在 JavaScript 中使用过字典之后，当我们在 TypeScript 中使用它时，我们会遇到错误，因为 TypeScript 需要知道对象的数据类型才能访问它。

这意味着我们在 JavaScript 中使用下面的代码不会有问题，但是在 TypeScript 中会有问题。让我们来看看。

```
const dictionary = {};
dictionary.firstName; // Property 'firstName' does not exist on type '{}'

```

这里，`dictionary.lastName`在 JavaScript 中返回`undefined`，但是在 TypeScript 中，会抛出错误。

```
const dictionary = { firstName: 'Gapur' };
// Return the firstName Gapur
dictionary.firstName;

// Property 'lastName' does not exist on type '{ firstName: string; }'
dictionary.lastName;

```

当然，我们可以在代码中使用类型`any`,但是为什么要使用 TypeScript 而不进行类型检查呢？

```
const dictionary: any = {};
dictionary.firstName = 'Gapur'; // It works
dictionary.lastName = 'Kassym'; // It works

```

## 在 TypeScript 中构建类型安全字典

有三种方法可以避免 TypeScript 中的类型问题。

### 1.使用索引对象符号

我们可以通过使用[索引对象符号](https://www.typescriptlang.org/docs/handbook/2/objects.html#index-signatures)来检查数据的类型。让我们用字符串类型`key`和`value`创建字典:

```
const dictionary: { [key: string]: string } = {};
dictionary.firstName = 'Gapur'; // It works very well
dictionary.lastName = true; // Type 'boolean' is not assignable to type 'string'

```

我们想叫什么就叫什么。对于这个例子，我想将其命名为`key`。

同样，根据语法规则，我们不能省略键名或使用联合类型。

```
// 'string' only refers to a type, but is being used as a value here.
const dictionaryWithoutKeyName: { [string]: string } = {}; // Error
dictionary.firstName = 'Gapur';

// An index signature parameter type cannot be a literal type or generic type. Consider using a mapped object type instead
const dictionaryWithUnionType: { [key: 'firstName' | 'lastName']: string } = {}; // Error
dictionary.firstName = 'Tom';

```

让我们把例子复杂化:

```
type User = {
    firstName: string;
    lastName: string;
}
const dictionary: { [key: number]: User } = {};
// Create user with firstName and lastName
dictionary[1] = {
    firstName: 'Gapur',
    lastName: 'Kassym'
};

// We can't add location property because User type doens't exist in the location
dictionary[2] = {
    firstName: 'Tom',
    lastName: 'Jones',
    location: 'London', // Type '{ firstName: string; lastName: string; location: string; }' is not assignable to type 'User'.
}

```

如果我们想省略一个属性，我们可以使用`[Partial](https://www.typescriptlang.org/docs/handbook/utility-types.html#partialtype)` utils。

```
type User = {
    firstName: string;
    lastName: string;
}
const dictionary: { [key: number]: User } = {};
// Property 'lastName' is missing in type '{ firstName: string; }' but required in type 'User'.
dictionary[1] = {
    firstName: 'Gapur',
}; // Error

const dictionaryWithPartial: { [key: number]: Partial<User> } = {};
// Works very well
dictionaryWithPartial[1] = {
    firstName: 'Tom'
}

```

### 2.使用`Record<Keys, Type>`实用程序

[记录<键，Type >](https://www.typescriptlang.org/docs/handbook/utility-types.html#recordkeystype) 是一个用于创建键值对象的类型脚本实用程序。如果你想创建一个键类型为`unions`或`enums`，这是一个很好的选择。

```
const dictionary: Record<string, string> = {};
dictionary.firstName = 'Gapur';
dictionary.lastName = 'Kassym';

```

让我们使用一个`union`类型:

```
type UserFields = 'firstName' | 'lastName';
let dictionaryUnion: Record<UserFields, string> = {
    firstName: 'Tom',
    lastName: 'Jones'
}; // Works very well

dictionaryUnion = {
    firstName: 'Aidana',
    lastName: 'Kassym',
    location: 'London' // Type is not assignable to type 'Record<UserFields, string>'
}; // Error

```

### 3.在 TypeScript 中使用`Map`

我们讨论了使用`Map`在 JavaScript 中创建类型安全的字典。让我们构建一个简单的字典，在 TypeScript 中将键作为字符串，值作为数字:

```
const dictionary = new Map<string, number>();
dictionary.set('JavaScript', 4); // No Error
dictionary.set('HTML', 4); // Works
// Argument of type 'string' is not assignable to parameter of type 'number'
dictionary.set('react', '4'); // Error

```

我们还可以使用键作为联合类型，使用值作为对象类型:

```
type JobInfo = {
    language: string,
    workExperience: number,
}
type JobPosition = 'Frontend' | 'Backend';
const dictionary = new Map<JobPosition, JobInfo>();
dictionary.set('Frontend', {
    language: 'JavaScript',
    workExperience: 5
});
dictionary.set('Backend', {
    language: 'Python',
    workExperience: 4
});

```

## 结论

通常，如何最好地构建类型安全字典取决于您的用例。如果你想构建一个简单的字典，你可以使用`indexed object`符号。如果您使用联合、枚举或更复杂的数据，那么使用`Record<Keys, Type>` util 是最好的。

一般来说，使用`Map`是解决各种类型数据的类型安全问题的一个非常实用的方法。

感谢阅读。我希望这篇文章对你有用。编码快乐！

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.