# 从 Rust 切换到 TypeScript(反之亦然)

> 原文：<https://blog.logrocket.com/switching-from-rust-to-typescript-and-vice-versa/>

开发人员从一种编程语言切换到另一种编程语言是很常见的。例如，您可以将 JavaScript 应用程序转换为 TypeScript，以利用后者的代码可维护性和可重用性优势。

你知道你也可以用铁锈做这个吗？Rust 和 TypeScript 各有优缺点，学习用 Rust 和 TypeScript 编写应用程序将有助于你成为一名更好、更全面的开发人员。

在本教程中，我们将向您展示如何从在 Rust 中编写应用程序过渡到 TypeScript，反之亦然。我们将探索这些语言的相似之处、不同之处以及与每种语言相关的共同挑战。然后我们将讨论在 Rust 中采用 TypeScript 的好处以及如何这样做的最佳实践。

以下是我们将要介绍的内容:

## Rust 与 TypeScript:管理依赖关系

Rust 在`cargo.toml`文件中管理它的依赖关系。一旦创建了 Rust 项目，这个文件就创建在项目的根目录下。要将一个新的依赖项导入到您的项目中，请将其添加到您的`cargo.toml`文件中。

```
// cargo.toml file
[package]
name = "[email protected]"
version = "0.0.1"
authors = ["Helloworld"]

[dependencies]
chrono = "0.4"
futures = "0.3"
atk = "^0"

```

在您的`cargo.toml`文件中添加了依赖项之后，运行您的项目。

TypeScript 管理依赖项的方式与 Rust 管理依赖项的方式非常相似。你所需要做的就是创建一个 TypeScript 项目，然后一个`package.json`文件将被创建在你的项目文件夹的根目录下。

```
// package.json file
{
  "name": "[email protected]",
  "description": "",
  "version": "1.0.0",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "repository": {
    "type": "git",
    "url": "https://github.com/me/[email protected]"
  },
  "keywords": [],
  "author": "Helloworld",
  "license": "ISC",
  "bugs": {
    "url": "https://github.com/me/[email protected]/issues"
  },
  "homepage": "https://github.com/me/[email protected]"
}

```

接下来，安装您的依赖项。你可以使用一个包管理器，比如 [npm](https://www.npmjs.com/) 。您安装的依赖项将出现在您的`package.json`文件中。

与 Rust 不同，您不需要通过编辑您的`package.json`文件将依赖项导入到您的项目中。当您安装依赖项时，它会在`package.json`中注册。如果您在`package.json`中添加了依赖项而没有安装它(像在 Rust 中)，当您试图运行您的项目时，会提示您安装它。

## Rust 和 TypeScript 中的数据类型

Rust 和 TypeScript 都是静态类型语言。这意味着编译器试图在编译期间推断数据类型。

让我们看看下面的 Rust 示例:

```
fn main() {
    // NUMBERS
    let i:i32 = 1;
    println!("The value of i is: {}", i); // 1
    // This will throw an error at compile time because 1.1 is type f64
    let j:i32 = 1.1; 
    // error[E0308]: mismatched types, expected `i32`, found floating-point number
    println!("The value of j is: {}", j); // 1.1
}

```

虽然 TypeScript 是静态类型的，但它实际上并不强制您在编译时访问正确的数据类型。

```
  const i:Number = 1;
  console.log(`The value of i is: ${i}`); // 1
  const j:String = 1.1;
  console.log(`The value of j is: ${j}`); // 1.1

```

上面的程序在编译时抛出一个错误:`expected `i32`, found floating-point number`。然而，相同的代码在运行时执行。这种行为会导致更多的问题，并可能导致异常。

为了避免这种情况，下面的程序使用了一个类型脚本库 [runtypes](https://github.com/pelotom/runtypes) 。它不仅在编译时给出警告，而且每当你访问一个不同于你所赋类型的类型时，它都会强制执行数据类型检查。

```
// install library
npm install --save runtypes

import { Boolean, Number, String, Literal, Tuple, Record, Union } from 'runtypes';

const Vector = Tuple(Number, Number, Number);

const AcademicStaff = Record({
  type: Literal('academicStaff'),
  location: Vector,
  population: Number,
});

const Student = Record({
  type: Literal('student'),
  location: Vector,
  population: Number,
  punctual: Boolean
});

const NonAcademicStaff = Record({
  type: Literal('nonAcademicStaff'),
  location: Vector,
  population: Number,
  name: String,
});

const UniversityObject = Union(AcademicStaff, Student, NonAcademicStaff);

// spaceObject: SpaceObject
const universityObject = UniversityObject.check(obj);//check will throw an exception if the object doesn't conform to the type specification

```

## 代码块和异常

Rust 作为一种静态类型的编程语言，允许及时捕捉异常。对于数据类型，编译器可以推断出您赋值的数据类型。

这种方法有助于在编译时、运行时之前捕获错误。这样，您就不会[对流控制](https://dzone.com/articles/exceptions-as-controlflow-in-java#:~:text=One%20of%20these%20common%20bad,makes%20your%20code%20less%20readable.)使用异常，这会影响代码的性能。一旦编译成功，您可以确信您的程序在运行时不会抛出错误。

Rust 处理错误的方式确保了没有[异常](https://doc.rust-lang.org/book/ch09-00-error-handling.html#:~:text=Rust%20doesn't%20have%20exceptions,program%20encounters%20an%20unrecoverable%20error.)。对于可恢复的错误，有一种`Result<T, E>`类型。当错误不可恢复时，一个`panic!`宏在编译时停止程序的执行。

```
// This error isn't recoverable
fn main() {
    let v = vec![1, 2, 3];
    v[99];
}

// This is a rust recoverable error
use std::fs::File;

fn main() {
    let f = File::open("hello.txt");
}

// A typical rust program that utilizes panic! to get results from a recoverable error (file not found)
use std::fs::File;

fn main() {
    let f = File::open("hello.txt");

    let f = match f {
        Ok(file) => file,
        Err(error) => panic!("Problem opening the file: {:?}", error),
    };
}

```

下面是一个 TypeScript 如何用`try`，`catch`语句处理异常的例子。

```
// Typescript runs program, shuts at exceptions
try {
  try_statements
}
[catch [(exception_var)] { //what if you're not really catching the exception?
  catch_statements
}]
[finally {
  finally_statements
}]

```

`try`，`catch`方法的问题在于它专门处理运行时错误。因此，编译器不能告诉你什么时候你的`try`、`catch`块有错误。此外，您不能在异步代码中使用`try`、`catch`，因为它会在异步代码完成执行之前完成执行。TypeScript 使用`type Type<T>`的类型安全错误处理更有能力处理异常。

```
<// How to handle exceptions in Typescript codes better,
type Result<T> = T | Error;
export type Type<T> = Result<T>;

export function isError<T>(result: Result<T>): result is Error {
  return result instanceof Error;
}

export function isSuccess<T>(result: Result<T>): result is T {
  return !isError(result);
}
  function doIt(): Result.Type<Thing>{
    if(true) {
      return {name: "Todd"};
    } else {
      return new Error("boom")
    }
  }

```

## 循环和条件语句

在 TypeScript 中编写条件语句很快就会变得模糊不清。为了解决这个问题，大多数开发人员在 TypeScript 中使用`switch case`语句。然而，随着时间的推移，这也会变得冗长。

```
type Option<A> = { _tag: 'Some'; value: A } | { _tag: 'None' }

type Foo = {
  _tag: 'Foo'
}

type Bar = {
  _tag: 'Bar'
}

type FooBar = Foo | Bar

declare const foobar: FooBar

switch (foobar._tag) {
  case 'Some':
    // Double switch!
    switch (foobar.value._tag) {
      case 'Foo':
        break
      case 'Bar':
        break
    }
    break
  default:
    break
}

```

上面的例子显示了当您在嵌套的 ADT 中编写条件语句时，TypeScript 会变得多么冗长。这是因为每一层嵌套都需要一个 switch 语句。

同样的例子可以在 Rust 中重写，如下所示:

```
 pub enum FooBar {
  Foo,
  Bar
}

fn main() {
  let foobar: Option<FooBar> = Some (FooBar::Foo);
  match foobar {
      Some(FooBar::Foo) => {}
      Some(FooBar::Bar) => {}
      _ => {}
  }
}

```

## 打字稿中的`Any`和铁锈中的`Unsafe`

TypeScript 的`Any`类型和 Rust 的`unsafe`有很多相似之处。它们主要用于解锁附加功能。例如，Rust 的`unsafe`可以让你解引用一个原始指针，访问一个不安全的函数，等等。

```
fn main() {
    unsafe fn dangerous() {
        println!("I'm Unsafe") //prints I'm Unsafe
    }
    unsafe {
        dangerous();
    }
  }

```

使用 TypeScript 中的`any`类型，您可以不受限制地访问属性，甚至是不存在的属性。例如，开发人员可以访问函数，而 TypeScript 不会检查它们的类型:

```
let Hello: any = 4;
// OK, ifItExists might exist at runtime, it dosen't exist now. 
Hello.ifItExists();
// OK, itExists exists (but the compiler doesn't check)
Hello.itExists();

```

在 Rust 的例子中，我们刚刚评估了一个不安全的函数。在这个程序中，Rust 不强制执行内存安全，而是在堆上分配内存。TypeScript 的`any`类型的变量也存储在堆中，因为变量类型在执行过程中可能会改变。

## 可变性和不变性

可变对象或变量在执行过程中甚至在赋值后也可以改变。在 Rust 中，变量在默认情况下是不可变的，因为编译器想确保你不会给一个变量赋值超过一次。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

您不必跟踪值可能会在哪里发生变化。当您运行下面的程序时，编译器会抛出一个错误:

```
fn main() {
    let a = 5;
    println!("The value of a is: {}", a);
    a = 6;
    println!("The value of a is: {}", a);
}

```

Typescript 不是为变量不变性而设计的。然而，如果你使用`keyword const`给变量赋值，你不能给它们赋值:

```
const a = 5;
console.log(a);
a = 6;
console.log(a);

```

上面的程序在运行时到达`a = 6;`时会抛出一个错误。这是因为它意识到`a`是一个常数，不应该给它赋值。

有时候可变性是有益的，甚至是必要的。你可以通过在变量前添加`mut`来改变 Rust 中的变量。让我们看看我们处理的第一个例子:

```
fn main() {
    let mut a = 5;
    println!("The value of a is: {}", a);
    a = 6;
    println!("The value of a is: {}", a);
}

```

上面的程序运行没有错误，因为我们将`mut`添加到变量中。在 TypeScript 中，可以变异类型为`let`和`var`的变量，如下例所示:

```
//let
let a = 5;
console.log(a);
a = 6;
console.log(a);

//var
var a = 5;
console.log(a);
a = 6;
console.log(a);

```

Rust 有`const`关键词。但是，您只能在编译时单独设置变量值，而不能在运行时设置。

```
fn another_function(x: i32) -> i32 {
    return x + 1;
}

fn main() {
    // RUN-TIME ASSIGNMENT, if you replace const with let, there's no compile error
    const z = another_function(5);
    println!("The value of z is: {}", z); // 6
}

```

因为`let`可以在编译时设置，而`const`不能，所以代码会在编译时抛出一个错误。尽管默认情况下 Rust 变量是不可变的，但是您可以重定义或隐藏`let`类型的变量:

```
fn main() {
    let a = 5;
    let a = a + 1;
    let a = a * 2;
    println!("The value of a is: {}", a); // 12
}

```

在 TypeScript 中，默认情况下只能隐藏或重定义类型为`var`的变量:

```
    var a = 6;
    var a = a + 1;
    var a = a * 2;
    console.log(a);

```

## struts 和对象

对象是 TypeScript 的一个非常重要的部分。你可以把一个物体想象成一个具有不同特征的人，这些特征被称为属性。您希望能够链接和访问该人的这些特征，就好像它们是链接在一起的一样:

```
var person = {
    name: 'Ugochi',
    country: 'Nigeria',
    age: 10
  }
console.log(person.name); //Ugochi
console.log(person.country); //Nigeria
console.log(person.age); //10

```

然而，在 Rust 中，你不能创建对象本身。您创建了一个带有`struct`类型的实例。铁锈中的结构称为`structs`。结构将相关属性放入一种数据类型中。

让我们用一个结构从我们的 TypeScript 对象重新创建对象示例:

```
fn main() {
    let person = Person {
        name: String::from("Ugochi"),
        country: String::from("Nigeria"),
        age: i32::from(10)
    };
    println!("{}", person.name); //Ugochi
    println!("{}", person.country); //Nigeria
    println!("{}", person.age); //10
}

struct Person {
    name: String,
    country: String,
    age: i32,
}

```

您可以从 TypeScript 中的其他对象生成对象，如下面的示例所示:

```
const Manager = {
  name: "John",
  age: 27,
  job: "Software Engineer"
}
const intern = Manager;
console.log(intern.name); //John
console.log(intern.age); // 27
console.log(intern.job); // Software Engineer

```

结构也可以从其他结构构建:

```
fn main() {
    let person = Person {
        name: String::from("Ugochi"),
        country: String::from("Nigeria"),
        age: i32::from(10)
    };
    println!("{}", person.name); //Ugochi
    println!("{}", person.country); //Nigeria
    println!("{}", person.age); //10
  let intern = person;
    println!("{}", intern.name); //Ugochi
    println!("{}", intern.country); //Nigeria
    println!("{}", intern.age); //10
}

struct Person {
    name: String,
    country: String,
    age: i32,
}

```

## 所有权

开发人员喜欢 Rust T1 的一个原因是它的内存管理能力。在 Rust 中，您不必担心垃圾收集器如何从堆中清除不再需要的内存。你所需要做的就是从内存分配器中请求内存。当你完成后，Rust 会自动将这些内存返回给分配器。

```
fn main() {
    let mut s = String::from("Welcome");

    s.push_str(", Ugochi!"); // push_str() appends a literal to a String

    println!("{}", s); // This will print `Welcome, Ugochi!`
  }// This scope is over and s is no longer valid.

```

在上面的例子中，我们从内存分配器中请求内存。由于我们不知道字符串的大小(可以在程序`mut`中更改)，所以它被发送到堆中，而不是堆栈中。

马上，范围就结束了。Rust 将内存`s`放回堆中。我们不需要写程序来清理内存，或者在使用完内存后将内存放回堆中。

TypeScript 使用垃圾收集器来管理堆中的内存。就像在 Rust 中一样，它会自动完成这项工作。

让我们看看下面这个来自 Mozilla 的例子:

```
var y = {
  a: {
    b: 2
  }
};
// two objects are created. One is referenced by being assigned to the 'y' variable
// The other is referenced by the other as one of its properties.
// None can be garbage collecte.

var y = x;      // The 'x' variable is the second thing that has a reference to one of the object.

y = 1;          // The object that was originally in 'y' has a unique reference from the 'x' variable.

var z = x.a;    // Reference to 'a' property of the object.
                //   The object now has 2 references: one as a property, the other as the 'z' variable.

x = 'mozilla';  // The object that was originally in 'y' has no zero references to it. It can be garbage-collected.
                //   However its 'a' property is still referenced by the 'z' variable, so it cannot be freed.

z = null;       // The 'a' property of the object originally in 'y' has no references to it. It can be garbage-collected.

```

内存不会在作用域结束时释放回堆，因为变量的某个属性仍在使用中。

## 轻便

可移植性和稳定性是从一种编程语言切换到另一种编程语言的两个常见原因。支持多种操作系统和架构的语言呢？

Rust 允许交叉编译。它有针对 Windows、MacOS、Linux 等的编译器的二进制版本。您可以使用任何操作系统的二进制版本来构建其他体系结构。

尽管 TypeScript 不像 Rust 那样具有可移植性，但它几乎可以在你能想到的任何操作系统上运行。您不需要安装任何运行时环境来执行 TypeScript 代码。

## Rust 与 TypeScript:为什么应该转换

换成 Rust 最常见的原因就是它的[保证内存安全](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Memory_Management#reference-counting_garbage_collection)。选择 TypeScript 的开发人员通常看重其在 JavaScript 中的[直观性和类型安全性。](https://ageek.dev/type-safety-in-javascript)

将您的应用程序从一种编程语言切换到另一种编程语言可能会很紧张和复杂。您可能想知道让应用程序堆栈保持原样是否更好。没有人想成为自己领域的后来者；随着前端和 web 开发生态系统的发展，找到自己的位置至关重要。学习用多种编程语言编写相同类型的应用程序和执行相同的功能可以帮助你成为一名更全面的开发人员。

在本文中，我们演示了如何从 TypeScript 切换到 Rust，反之亦然。我们还介绍了在进行这种改变时可能会遇到的一些挑战，并探讨了 TypeScript 和 Rust 之间的许多相似之处。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.