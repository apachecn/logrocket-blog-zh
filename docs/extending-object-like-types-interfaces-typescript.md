# 在 TypeScript 中用接口扩展类对象类型

> 原文：<https://blog.logrocket.com/extending-object-like-types-interfaces-typescript/>

接口是 TypeScript 的核心特性之一，它允许开发人员灵活而富有表现力地对他们的代码施加约束，以减少错误并提高代码可读性。让我们深入探索接口的各种特性，以及如何在我们的程序中更好地利用它们。

向前跳:

*   [扩展 TypeScript 中的接口](#expanding-interfaces-typescript)*   [TypeScript 中接口的用例](#use-cases-interfaces-typescript)*   [TypeScript 中接口的利与弊](#pros-cons-interfaces-typescript)

## 什么是 TypeScript 接口？

首先，一点背景。接口是开发人员命名类型的一种方式，以便以后在程序中引用。例如，一个公共图书馆的管理软件可能有一个代表图书馆藏书的数据的`Book`接口:

```
interface Book {
  title: string;
  author: string;
  isbn: string;
}

```

有了它，我们可以确保程序中的图书数据包含标题、作者和 ISBN 的基本信息。如果没有，TypeScript 编译器将抛出一个错误:

```
const tale: Book = {
  title: 'A Tale of Two Cities',
  author: 'Charles Dickens',
  // Error: Property 'isbn' is missing in type '{ title: string; author: string; }' but required in type 'Book'.
};

```

在我们上面的`Book`接口示例中，所有的属性都是根据需要定义的。然而，我们也可以定义一个接口来期望一些可选的属性。这可以通过在属性定义中添加`?`符号来实现。

为了演示这一点，让我们向我们的`Book`接口添加一个可选的布尔属性`signedCopy`:

```
interface Book {
  title: string;
  author: string;
  isbn: string;
  signedCopy?: boolean;
}

```

### 接口与类型

如果您以前编写过 TypeScript 代码，您可能熟悉类型别名，这是命名类型的另一种常用方法，您可能会问，“为什么使用接口而不是类型，反之亦然？”

主要的区别在于，接口可以被重新打开，以便在程序的不同部分添加额外的属性(通过[声明合并](https://www.typescriptlang.org/docs/handbook/declaration-merging.html)),而类型别名则不能。让我们看看如何利用声明合并，以及您可能需要的一些情况。

## 在 TypeScript 中扩展接口

### 选项 1:声明合并

如上所述，可以重新打开接口来添加新的属性和扩展类型的定义。下面是一个无意义的例子来说明这种能力:

```
interface Stock {
  value: number;
}

interface Stock {
  tickerSymbol: string;
}

```

当然，同样的界面不太可能像这样在附近重新打开。用一句话来定义会更清楚:

```
interface Stock {
  value: number;
  tickerSymbol: string;
}

```

那么，你什么时候想在程序的不同部分扩展一个接口呢？让我们看一个真实的用例。

### 合并声明以争论不同的用户偏好

假设您正在编写一个 React 应用程序，并且您需要一些页面来允许用户配置信息，比如他们的配置文件、通知首选项和可访问性设置。

为了清晰和用户体验，您将这三个关注点分成了三个单独的页面，其中源代码将位于三个文件中:`Profile.tsx`、`Notifications.tsx`和`Accessibility.tsx`。

从应用程序架构的角度来看，如果所有用户的偏好都包含在一个对象中就好了，这个对象遵循一个我们称之为`Preferences`的接口。这样，只需一两个端点，而不是几个端点，就可以轻松地用后端 API 加载和保存 preferences 对象。

下一个问题是:“应该在哪里定义`Preferences`接口？”您可以将接口放在它自己的文件中，`preferences.ts`，并将`import`放到三个页面中——或者，您可以利用声明合并，让每个页面只定义它关心的`Preferences`的属性，如下所示:

```
// Profile.tsx

interface Preferences {
  avatarUrl: string;
  username: string;
}

const Profile = (props) => {
  // ... UI for managing the user's profile ...
}

```

```
// Notifications.tsx

interface Preferences {
  smsEnabled: boolean;
  emailEnabled: boolean;
}

const Notification = (props) => {
  // ... UI for managing the user's notification preferences ...
}

```

```
// Accessibility.tsx

interface Preferences {
  highContrastMode: boolean;
}

const Accessibility = (props) => {
  // ... UI for managing the user's accessibility settings ...
}

```

最后，`Preferences`接口将解析为完全包含所有属性，如所期望的:

```
interface Preferences {
  avatarUrl: string;
  username: string;
  smsEnabled: boolean;
  emailEnabled: boolean;
  highContrastMode: boolean;
}

```

UI 代码现在只与它管理的`Preferences`的属性放在一起，使得程序更容易理解和维护。不错！

### 选项 2:在 TypeScript 中扩展接口

在 TypeScript 中扩展接口的另一种方法是将一个或多个接口混合到一个新接口中:

```
interface Pet {
  name: string;
  age: number;
}

interface Dog extends Pet {
  breed: string;
}

interface Fish extends Pet {
  finColor: string;
}

const betta: Fish = {
  name: 'Sophie',
  age: 2,
  finColor: 'black',
};

```

这对于面向对象的程序员来说可能很熟悉。然而，接口提供了一个在传统面向对象编程中通常找不到的关键特性:多重继承。

### 在 TypeScript 中扩展多个接口

多重继承允许我们将多个接口的行为和属性合并到一个接口中。

扩展多个接口指的是组合的概念，其中接口被设计为扩展它需要的属性。它不同于面向对象程序设计中的继承概念，在面向对象程序设计中，一个对象是一个给定类的子对象，并强制扩展父对象的属性。

让我们来看一个用例，看看什么时候您可能想要这样做。

### 扩展接口以形成类型安全的全局状态存储

假设您正在构建一个应用程序，使用户能够在一个地方跟踪他们的待办事项列表和日常日程。您将拥有一些不同的 UI 组件来跟踪这些任务:

```
// todo-list.ts

interface ToDoListItem {
  title: string;
  completedDate: Date | null;
}

interface ToDoList {
  todos: ToDoListItem[];
}

// ... application code for managing to-do lists ...

```

```
// calendar.ts

interface CalendarEvent {
  title: string;
  start: Date;
  end: Date;
}

interface Calendar {
  events: CalendarEvent[];
}

// ... application code for managing the calendar ...

```

既然您已经创建了跟踪两个状态的基本接口，那么您可能希望用一个接口来表示整个应用程序的状态。我们可以使用`extends`关键字来创建这样一个接口。我们还将添加一个`modified`字段，以便我们知道我们的状态最后一次更新是什么时候:

```
interface AppState extends ToDoList, Calendar {
  modified: Date;
}

```

现在您可以使用`AppState`接口来确保应用程序正确处理状态:

```
function persist(state: AppState) {
  // ... save the state to a storage layer ...
}

persist({
  todos: [
    { title: 'Text Marcy', completedDate: new Date('2022-02-05') },
    { title: 'Buy groceries', completedDate: null },
  ],
  events: [
    {
      title: 'Study',
      start: new Date('2022-02-11 08:00:00'),
      end: new Date('2022-02-11 10:00:00'),
    },
  ],
  modified: new Date('2022-02-06'),
});

```

### 扩展类型

虽然使用类型别名无法重新打开接口，但这种扩展类型的方法是可行的，只是在语法上有一些细微的差别。下面是使用`type`代替`interface`的等效示例:

```
type ToDoListItem = {
  title: string;
  completedDate: Date | null;
}
type ToDoList = {
  todos: ToDoListItem[];
}
type CalendarEvent = {
  title: string;
  start: Date;
  end: Date;
}
type Calendar = {
  events: CalendarEvent[];
}
type AppState = ToDoList & Calendar & {
  modified: Date;
}
function persist(state: AppState) {
  // ... save the state to a storage layer ...
}
persist({
  todos: [
    { title: 'Text Marcy', completedDate: new Date('2022-02-05') },
    { title: 'Buy groceries', completedDate: null },
  ],
  events: [
    {
      title: 'Study',
      start: new Date('2022-02-11 08:00:00'),
      end: new Date('2022-02-11 10:00:00'),
    },
  ],
  modified: new Date('2022-02-06'),
});

```

组合多个类型定义的过程称为交集，并使用`&`符号来执行。通过阅读 TypeScript 中的[强大的类型选项来了解更多信息。](https://blog.logrocket.com/types-vs-interfaces-in-typescript/)

## TypeScript 中接口的用例

接口可用于定义函数或类的预期属性，包括必需属性和可选属性。让我们看几个用例。

### 为函数使用接口

我们可以使用接口来指定函数的预期参数和结果:

```
 // function
interface Person {
  firstName: string
  lastName: string
  age?: number
}

interface Bio {
  fullName: string
  yearOfBirth?: number
}

function getBio(person: Person): Bio {
  let yearOfBirth: number // is initially undefined

  if (person?.age) {
      const today = new Date()

      yearOfBirth = today.getFullYear() - person.age
  }

  return {
    fullName: `${person.firstName} ${person.lastName}`,
    yearOfBirth
  }
}

```

在上面的例子中，我们定义了一个接口`Person`。这个`Person`对象具有必需的属性`firstName`和`lastName`以及可选属性`age`，这是预期的函数参数。

我们还定义了一个接口`Bio`，它有一个必需属性`fullName`和一个可选属性`yearOfBirth`，函数会返回这个属性。

### 为类定义使用接口

我们可以为一个类定义一个接口，指定它的预期属性和它们的形状，然后使用`implements`关键字将这个定义应用到这个类:

```
interface Animal {
   name: string
   canWalk(): boolean
}

class Cat implements Animal {
   name: string

   constructor() {
      this.name = 'Cat'
   }

   canWalk() {
     return true
   }
}

const cat = new Cat()
console.log(cat.canWalk())

```

在上面的例子中，我们已经定义了一个接口`Animal`，由类`Cat`实现。这意味着`Cat`必须拥有`Animal`中定义的所有必需属性。

## TypeScript 中接口的利与弊

当我们讨论接口的利弊时，我们在很大程度上是在讨论 TypeScript 和 JavaScript 的利弊。这是因为通过接口键入和定义形状是 TypeScript 的核心功能。

先说优点。

使用 TypeScript 接口，我们定义了预期的内容，并赋予代码一定程度的一致性和可靠性。通过设置变量、函数参数或函数结果的类型，我们知道会发生什么，并且会得到一些编译时错误，提醒我们发生了不允许的事情。

在下面的示例中，我们的 JavaScript 函数没有使用类型来描述预期的内容，这可能会导致意外的结果:

```
// JavaScript
function (a, b) => a + b

```

```
// TypeScript
function (a: number, b: number): number => a + b

```

在我们的 JavaScript 示例中，一个可能的错误是传递一个字符串而不是一个数字。结果将是一个字符串结果(来自连接)，其中应该是一个数字。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

TypeScript 中的编译时错误发生在生成时，最重要的是发生在函数或代码运行之前。这意味着可以更快地检测到错误，并且在它们造成任何损害之前就检测到。

最后，使用接口定义代码形状使得代码库更容易管理。推而广之，这也有可能提高团队工作的整体绩效。

现在，让我们探讨一下在 TypeScript 中使用接口的一些缺点。

很容易完全依赖接口和类型来防止错误，并产生错误的安全感。虽然它有助于定义预期和非预期的形状，但它并不是防弹的。

此外，实现类型和接口可以非常容易且快速地使代码库变得复杂。只要想想接受和处理不同形状或类型的变量的函数，以及随之而来的无穷无尽的函数重载。

## 结论

在 TypeScript 中，有几种不同的方法可以用接口扩展类似对象的类型，有时，您可以使用类型别名。

在这种情况下，甚至官方文件也说它们大多可以互换，这取决于风格、偏好、组织、习惯等。但是，如果您想在程序的不同部分声明同一类型的不同属性，请使用 TypeScript 接口。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.