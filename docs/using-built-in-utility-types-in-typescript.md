# 在 TypeScript 中使用内置实用程序类型

> 原文：<https://blog.logrocket.com/using-built-in-utility-types-in-typescript/>

TypeScript 拥有所有编程语言中最强大的类型系统之一——主要是因为它已经发展到可以容纳所有可以在 JavaScript 中做的动态事情。

包含诸如条件类型、查找类型和映射类型等特性意味着您可以在 TypeScript 中编写一些非常高级的类型函数。

## **什么是** **t** 型 **f** 油膏？

TypeScript 允许您从任何现有类型创建类型别名。例如:

```
// Example
type Str = string; 

// Usage
let message: Str = 'Hello world';

```

TypeScript 类型别名也支持泛型。传统上，泛型用于基于一种类型约束另一种类型。例如，`value`、`setValue`对和泛型的类型用于确保`setValue`总是被`value`使用的相同类型调用:

```
// Example
type ValueControl<T> = {
  value: T,
  setValue: (newValue: T) => void,
};

// Usage 
const example: ValueControl<number> = {
  value: 0,
  setValue: (newValue) => example.value = newValue,
};

```

注意，在上面的例子中，我们可以为`ValueControl`传入一个类型。在我们的例子中，我们传入了类型`number`(即`ValueControl<number>`)。

TypeScript 真正强大的特性是，传递给泛型函数的类型可以在条件(条件类型)和映射(映射类型)中使用。下面是一个使用条件*从类型中排除* `null`和`undefined`的类型示例:

```
/**
 * Exclude null and undefined from T
 */
type NoEmpty<T> = T extends null | undefined ? never : T;

// Usage 
type StrOrNull = string | null;
type Str = NoEmpty<StrOrNull>; // string

```

然而，您不一定需要使用这些基础级特性，因为 TypeScript 还附带了许多方便的内置实用函数。

事实上，我们的类型`NoEmpty<T>`已经作为 TypeScript 的一部分发布了(它被称为`NonNullable<T>`，我们将在下面介绍它)。在本文中，我们将通过真实的例子来介绍这些类型的函数，以了解为什么您会想要使用它们。

## TypeScript 中的内置类型函数

从 TypeScript 4.0 开始，您可以在 TypeScript 中使用这些内置的类型函数，而不需要任何额外的包:

### `Partial<T>`

`Partial`将输入类型`T`的所有成员标记为可选。下面是一个简单的`Point`类型的例子:

```
type Point = { x: number, y: number };

// Same as `{ x?: number, y?: number }`
type PartialPoint = Partial<Point>;

```

一个常见的用例是在许多状态管理库中找到的 *update* 模式，在这种情况下，您只需提供想要更改的属性的子集。例如:

```
class State<T> {
  constructor(public current: T) { }
  // Only need to pass in the properties you want changed
  update(next: Partial<T>) {
    this.current = { ...this.current, ...next };
  }
}

// Usage
const state = new State({ x: 0, y: 0 });
state.update({ y: 123 }); // Partial. No need to provide `x`.
console.log(state.current); // Update successful: { x: 0, y: 123 }

```

### `Required<T>`

`Required`做与`Partial<T>`相反的事。它使得输入类型`T` *的所有成员都不可选*。换句话说，这使得它们*成为必需的*。下面是这种转变的一个例子:

```
type PartialPoint = { x?: number, y?: number };

// Same as `{ x: number, y: number }`
type Point = Required<PartialPoint>;

```

一个用例是当一个类型有可选的成员，但是你的部分代码需要提供所有的成员。你可以有一个带有可选成员的配置，但是在内部，你要初始化它们，这样你就不必处理所有代码的空检查:

```
// Optional members for consumers
type CircleConfig = {
  color?: string,
  radius?: number,
}

class Circle {
  // Required: Internally all members will always be present
  private config: Required<CircleConfig>;

  constructor(config: CircleConfig) {
    this.config = {
      color: config.color ?? 'green',
      radius: config.radius ?? 0,
    }
  }

  draw() {
    // No null checking needed :)
    console.log(
      'Drawing a circle.',
      'Color:', this.config.color,
      'Radius:', this.config.radius
    );
  }
}

```

### `Readonly<T>`

这将输入类型`T`的所有属性标记为`readonly`。下面是这种转变的一个例子:

```
type Point = { x: number, y: number };

// Same as `{ readonly x: number, readonly y: number }`
type ReadonlyPoint = Readonly<Point>;

```

这对于*冻结*对象以防止编辑的常见模式很有用。例如:

```
function makeReadonly<T>(object: T): Readonly<T> {
  return Object.freeze({ ...object });
}

const editablePoint = { x: 0, y: 0 };
editablePoint.x = 2; // Success: allowed

const readonlyPoint = makeReadonly(editablePoint);
readonlyPoint.x = 3; // Error: readonly

```

### `Pick<T, Keys>`

从`T`中仅选取指定的`Keys`。在下面的代码中，我们有一个带有按键`'x' | 'y' | 'z'`的`Point3D`，我们可以通过只选择按键`'x' | 'y'`来创建一个`Point2D`:

```
type Point3D = {
  x: number,
  y: number,
  z: number,
};

// Same as `{ x: number, y: number }`
type Point2D = Pick<Point3D, 'x' | 'y'>;

```

这对于获取对象的子集很有用，就像我们在上面的例子中通过创建`Point2D`看到的那样。

一个更常见的用例是简单地获取您感兴趣的属性。下面演示了这一点，我们从所有的`CSSProperties`中得到`width`和`height`:

```
// All the CSSProperties
type CSSProperties = {
  color?: string,
  backgroundColor?: string,
  width?: number,
  height?: number,
  // ... lots more
};

function setSize(
  element: HTMLElement,
  // Usage: Just need the size properties
  size: Pick<CSSProperties, 'width' | 'height'>
) {
  element.setAttribute('width', (size.width ?? 0) + 'px');
  element.setAttribute('height', (size.height ?? 0) + 'px');
}

```

### `Record<Keys, Value>`

给定一组由`Keys`指定的成员名，这将创建一个类型，其中每个成员都属于类型`Value`。这里有一个例子来说明这一点:

```
// Same as `{x: number, y: number}`
type Point = Record<'x' | 'y', number>;

```

当一个类型的所有成员都有相同的`Value`时，使用`Record`可以帮助你更好地阅读代码，因为很明显所有成员都有相同的`Value`类型。在上面的`Point`示例中，这一点很明显。

当有大量成员时，那么`Record`就更有用了。下面是不使用 Record 的代码:

```
type PageInfo = {
  id: string,
  title: string,
};

type Pages = {
  home: PageInfo,
  services: PageInfo,
  about: PageInfo,
  contact: PageInfo,
};

```

下面是使用`Record`的代码:

```
type Pages = Record<
  'home' | 'services' | 'about' | 'contact',
  { id: string, title: string }
>;

```

### `Exclude<T, Excluded>`

这将`T`中的`Excluded`类型排除在外。

```
type T0 = Exclude<'a' | 'b' | 'c', 'a'>; // 'b' | 'c'
type T1 = Exclude<'a' | 'b' | 'c', 'a' | 'b'>; // 'c'
type T2 = Exclude<string | number | (() => void), Function>; // string | number

```

最常见的用例是从对象中排除某些键。例如:

```
type Dimensions3D = 'x' | 'y' | 'z';
type Point3D = Record<Dimensions3D, number>;

// Use exclude to create Point2D
type Dimensions2D = Exclude<Dimensions3D, 'z'>;
type Point2D = Record<Dimensions2D, number>;

```

您还可以使用它从联合中排除其他不需要的成员(例如，`null`和`undefined`):

```
type StrOrNullOrUndefined = string | null | undefined;

// string
type Str = Exclude<StrOrNullOrUndefined, null | undefined>;

```

### `NonNullable<T>`

这从类型`T`中排除了`null`和`undefined`。和`Exclude<T, null | undefined>`有异曲同工之妙。

一个简单的 JavaScript premier: nullable 是可以被赋予一个[空值](https://developer.mozilla.org/en-US/docs/Glossary/Nullish)的东西，例如`null`或`undefined`。所以，不可空是不应该接受空值的东西。

这是我们用`Exclude`看到的同一个例子，但是这一次，让我们使用`NonNullable`:

```
type StrOrNullOrUndefined = string | null | undefined;

// Same as `string`
// Same as `Exclude<StrOrNullOrUndefined, null | undefined>`
type Str = NonNullable<StrOrNullOrUndefined>;

```

### `Extract<T, Extracted>`

这将从`T`中提取`Extracted`类型。你可以把它看作是`Exclude`的反义词，因为，不是指定你想要排除的类型(`Exclude`，而是指定你想要保留/提取的类型(`Extract`):

```
type T0 = Extract<'a' | 'b' | 'c', 'a'>; // 'a'
type T1 = Extract<'a' | 'b' | 'c', 'a' | 'b'>; // 'a' | 'b'
type T2 = Extract<string | number | (() => void), Function>; // () => void

```

Extract 可以被认为是两种给定类型的交集。这在下面进行了演示，其中提取了公共元素`'a' | 'b'`:

```
type T3 = Extract<'a' | 'b' | 'c', 'a' | 'b' | 'd'>; // 'a' | 'b'

```

`Extract`的一个用例是找到两种类型的公共基础，就像这样:

```
type Person = {
  id: string,
  name: string,
  email: string,
};

type Animal = {
  id: string,
  name: string,
  species: string,
};

/** Use Extract to get the common keys */
type CommonKeys = Extract<keyof Person, keyof Animal>;

/** 
 * Map over the keys to find the common structure 
 * Same as `{ id: string, name: string }`
 **/
type Base = {
  [K in CommonKeys]: (Animal & Person)[K]
};

```

### `Omit<T, Keys>`

从类型`T`中省略由`Keys`指定的键。这里有一个例子:

```
type Point3D = {
  x: number,
  y: number,
  z: number,
};

// Same as `{ x: number, y: number }`
type Point2D = Omit<Point3D, 'z'>;

```

在传递对象之前省略它的某些属性是 JavaScript 中的一种常见模式。

类型函数提供了一种方便的方式来注释这样的转换。例如，通常在登录前删除 PII(个人身份信息，如电子邮件地址和姓名)。您可以用`Omit`来注释这个转换。

```
type Person = {
  id: string,
  hasConsent: boolean,
  name: string,
  email: string,
};

// Utility to remove PII from `Person`
function cleanPerson(person: Person): Omit<Person, 'name' | 'email'> {
  const { name, email, ...clean } = person;
  return clean;
}

```

### `Parameters<Function>`

给定一个`Function`类型，该类型将函数参数的类型作为一个元组返回。下面是一个演示这种转换的示例:

```
function add(a: number, b: number) {
  return a + b;
}

// Same as `[a: number, b: number]`
type AddParameters = Parameters<typeof add>;

```

您可以将`Parameters`与 TypeScript 的索引*和查找类型*结合起来，以获得任何单个参数。我们甚至可以获取第一个参数的类型:

```
function add(a: number, b: number) {
  return a + b;
}

// Same as `number`
type A = Parameters<typeof add>[0];

```

`Parameters`的一个关键用例是捕获函数参数类型的能力，这样您就可以在代码中使用它来确保类型安全。

```
// A save function in an external library
function save(person: { id: string, name: string, email: string }) {
  console.log('Saving', person);
}

// Ensure that ryan matches what is expected by `save`
const ryan: Parameters<typeof save>[0] = {
  id: '1337',
  name: 'Ryan',
  email: '[email protected]',
};

```

### `ConstructorParameters<ClassConstructor>`

这和我们上面看到的`Parameters`型差不多。唯一的区别是`ConstructorParameters`在类构造函数上工作，就像这样:

```
class Point {
  private x: number;
  private y: number;
  constructor(initial: { x: number, y: number }) {
    this.x = initial.x;
    this.y = initial.y;
  }
}

// Same as `[initial: { x: number, y: number} ]`
type PointParameters = ConstructorParameters<typeof Point>;

```

当然，`ConstructorParamters`的主要用例也是类似的。在下面的例子中，我们使用它来确保我们的初始值将被`Point`类接受:

```
class Point {
  private x: number;
  private y: number;
  constructor(initial: { x: number, y: number }) {
    this.x = initial.x;
    this.y = initial.y;
  }
}

// Ensure that `center` matches what is expected by `Point` constructor
const center: ConstructorParameters<typeof Point>[0] = {
  x: 0,
  y: 0,
};

```

### `ReturnType<Function>`

给定一个`Function`类型，这将获得函数返回的类型。

```
function createUser(name: string) {
  return {
    id: Math.random(),
    name: name
  };
}

// Same as `{ id: number, name: string }`
type User = ReturnType<typeof createUser>;

```

一个可能的用例类似于我们在`Parameters`中看到的。它允许你得到一个函数的返回类型，这样你就可以用它来类型化其他变量。这实际上在上面的例子中得到了证明。

还可以使用`ReturnType`来确保一个函数的输出与另一个函数的输入相同。这在 React 中很常见，在 React 中，您有一个自定义钩子来管理 React 组件所需的状态。

```
import React from 'react';

// Custom hook
function useUser() {
  const [name, setName] = React.useState('');
  const [email, setEmail] = React.useState('');
  return {
    name,
    setName,
    email,
    setEmail,
  };
}

// Custom component uses the return value of the hook
function User(props: ReturnType<typeof useUser>) {
  return (
    <>
      <div>Name: {props.name}</div>
      <div>Email: {props.email}</div>
    </>
  );
}

```

### `InstanceType<ClassConstructor>`

`InstanceType`类似于我们上面看到的`ReturnType`。唯一的区别是`InstanceType`在一个类构造器上工作。

```
class Point {
  x: number;
  y: number;
  constructor(initial: { x: number, y: number }) {
    this.x = initial.x;
    this.y = initial.y;
  }
}

// Same as `{x: number, y: number}`
type PointInstance = InstanceType<typeof Point>;

```

你通常不需要对任何静态类使用这个，比如上面的`Point`类。这是因为您可以只使用类型注释`Point`，如下所示:

```
class Point {
  x: number;
  y: number;
  constructor(initial: { x: number, y: number }) {
    this.x = initial.x;
    this.y = initial.y;
  }
}

// You wouldn't do this
const verbose: InstanceType<typeof Point> = new Point({ x: 0, y: 0 });

// Because you can do this
const simple: Point = new Point({ x: 0, y: 0 });

```

然而，TypeScript 也允许您创建动态类，例如，下面的函数`DisposibleMixin`动态返回一个类:

```
type Class = new (...args: any[]) => any;

// creates a class dynamically and returns it
function DisposableMixin<Base extends Class>(base: Base) {
  return class extends base {
    isDisposed: boolean = false;
    dispose() {
      this.isDisposed = true;
    }
  };
}

```

现在我们可以使用`InstanceType`来获取通过调用`DisposiblePoint`创建的实例的类型:

```
type Class = new (...args: any[]) => any;

function DisposableMixin<Base extends Class>(base: Base) {
  return class extends base {
    isDisposed: boolean = false;
    dispose() {
      this.isDisposed = true;
    }
  };
}

// dynamically created class
const DisposiblePoint = DisposableMixin(class {
  x = 0;
  y = 0;
});

// Same as `{isDisposed, dispose, x, y}`
let example: InstanceType<typeof DisposiblePoint>;

```

## 结论

正如我们所看到的，TypeScript 有许多内置的实用程序类型。其中许多都是您可以自己编写的简单定义，例如，如果您想要排除 null 和 undefined，您可以自己轻松编写以下内容:

```
// Your custom creation
type NoEmpty<T> = T extends null | undefined ? never : T;

// Usage 
type StrOrNull = string | null;

// string - People need to look at `NoEmpty` to understand what it means
type Str = NoEmpty<StrOrNull>; 

```

然而，使用内置版本`NonNullable`(做同样的事情)可以提高代码的可读性，所以熟悉 TypeScript 标准库的人不需要解析主体`T extends null | undefined ? never : T;`来理解发生了什么。

下面演示了这一点:

```
// No need for creating something custom

// Usage 
type StrOrNull = string | null;

// string - People that know TS know what `NonNullable` does
type Str = NonNullable<StrOrNull>; 

```

最终，你应该尽可能使用像`ReadOnly` / `Partial` / `Required`这样的内置类型，而不是创建自定义类型。除了节省编写代码的时间之外，它还让您不必考虑用*命名*您的实用程序，因为它们已经由 TypeScript 团队为您命名了。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.