# 在 TypeScript 中探索高级编译器选项

> 原文：<https://blog.logrocket.com/exploring-advanced-compiler-options-typescript/>

## 介绍

`tsconfig.json`文件指定了 TypeScript 编译器使用的编译选项，它对我们的代码进行检查，并确定这些检查是否失败。这些选项包括我们的 TypeScript 代码将被编译成哪个版本的 JavaScript，输出目录应该是什么，以及是否允许 JavaScript 源文件在项目目录中。

在本文中，我们将深入了解一些高级编译器选项和一些其他选项，它们有助于我们发现 TypeScript 代码库中的潜在问题。深入理解这些编译器选项以及导致代码无法通过严格检查规则的原因，将有助于我们在构建 TypeScript 应用程序时避免常见错误。

具体来说，本文将涵盖以下选项:

## 嵌套的`tsconfig.json`文件

当编译当前目录中的代码时，TypeScript 编译器可以引用另一个目录中的`tsconfig.json`文件。

如果我们想在一个特定的目录中运行`tsc`时引用一个编译器选项，这个特性是很方便的。为此，`tsconfig.json`文件使用了`"references"`选项。

作为这种嵌套配置的一个示例，请考虑下面的源树:

```
├── dist
└── src
    ├── tsconfig.json
    ├── backend
    │   ├── index.ts
    │   └── tsconfig.json
    └── frontend
        ├── index.ts
        └── tsconfig.json 

```

这里，我们在项目的`src`目录中有一个`tsconfig.json`文件，以及两个名为`frontend`和`backend`的子目录。两个子目录都包含一个`tsconfig.json`文件和一个名为`index.ts`的类型脚本文件。

项目的`src`目录下的`tsconfig.json`文件如下:

```
{
    "compilerOptions": {
      "target": "es5", 
      "module": "commonjs", 
      "rootDir": ".",
      "outDir": "../dist/",
    },
    "files": [],
    "references": [
      { "path": "./backend" },
      { "path": "./frontend" }
    ]
  }

```

这里，我们指定了`outDir`属性来将所有 JavaScript 输出生成到`dist`目录中，然后配置两个子目录的引用路径。

可以使用以下命令编译整个项目:

```
tsc --build src

```

我们来看看`backend`目录下的`tsconfig.json`文件，如下:

```
{
    "compilerOptions": {
      "rootDir": ".",
      "outDir": "../../dist/backend",
    }
  }

```

这里，我们指定了`outDir`属性来将所有 JavaScript 输出生成到`dist`目录中。

这意味着 TypeScript 编译器将把这个目录中的所有 JavaScript 文件输出到上两级目录的`dist`目录中。

可以使用以下命令独立构建`frontend`子目录:

```
tsc --build src/frontend

```

我们来看看`backend`目录下的`tsconfig.json`文件:

```
{
    "compilerOptions": {
      "rootDir": ".",
      "outDir": "../../dist/frontend",
    },
    "references": [
      { "path": "../backend" }
      "composite": true
    ]
  }

```

类似地，我们已经指定了`outDir`属性来将该目录中的所有 JavaScript 输出生成到`dist`目录，这是两个目录级别之上的目录，然后配置`backend`子目录的引用路径。

请注意来自[打字稿文档](https://www.typescriptlang.org/docs/handbook/project-references.html)的信息:“引用的项目必须启用新的`composite`设置。需要此设置来确保 TypeScript 可以快速确定在哪里可以找到被引用项目的输出。

此外，可以使用以下命令独立构建`backend`子目录:

```
tsc --build src/backend

```

## `strictPropertyInitialization`

启用时，`strictPropertyInitialization`编译器选项确保类中的所有属性都被正确初始化。

让我们考虑下面的类定义:

```
class NoInitProperties { 
  a: number; 
  b: string; 
}

```

这里，我们有一个名为`NoInitProperties`的类，它有两个属性，名为`number`类型的`a`和`string`类型的`b`。上述代码将生成以下错误:

```
error TS2564: Property 'a' has no initializer and is not definitely assigned in the constructor 
error TS2564: Property 'b' has no initializer and is not definitely assigned in the constructor

```

产生这些错误是因为类的`a`和`b`属性都没有初始化。

### 解决`strictPropertyInitialization`问题

有四种方法可以修复这个代码。

第一种方法通常用于修复这些错误，它使用一个构造函数:

```
class NoInitProperties { 
    a: number; 
    b: string; 
    constructor(b: string) { 
      this.a = 5; 
      this.b = b; 
    } 
}

```

这里，我们定义了一个带有类型为`string`的参数`b`的`constructor`函数。在`constructor`中，我们已经将`b`参数的值赋给了内部`b`属性。此外，我们已经将字符串值`"letter"`赋给了名为`a`的属性。有了这个构造函数，
错误将被修复，因为两个属性现在都被正确初始化了。

第二种方法是使用类型联合:

```
class NoInitProperties { 
    a: number | undefined; 
    b: string | undefined; 
}

```

这里，联合类型用于将`undefined`类型添加到`a`和`b`属性中。这样，编译器知道我们知道这些属性可能是未定义的，这将允许我们自己处理结果。

我们可以用来修复这些错误的第三种方法是使用明确的赋值断言操作符:

```
class NoInitProperties { 
    a!: number; 
    b!: string; 
}

```

在每个属性后添加的`!`操作符告诉编译器，我们知道这些属性还没有初始化。

修复这些错误的第四种方法是为每个属性赋值:

```
class NoInitProperties { 
    a: number = 5; 
    b: string = "letter"; 
}

```

这里，我们将数值`5`赋给了属性 a，将字符串值`"letter"`赋给了属性`b`。

## `noImplicitThis`

`noImplicitThis`编译器选项将确保`this`关键字被正确访问，否则编译器将抛出一个错误，指示对`this`的不正确访问。
让我们考虑以下代码:

```
class NoImplicitThisClass { 
    name: string = "Tom"; 
    logToConsole() { 
        let callback = function () { 
            console.log(`this.name : ${this.name}`); 
        } 
      setTimeout(callback, 1000); 
    } 
}

```

这里，我们有一个名为`noImplicitThisClass`的类，它有一个用字符串值`Tom`初始化的`name`属性。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

此外，该类定义了一个名为`logToConsole`的函数，当该函数被调用时，会在两秒钟后触发函数`callback`。该类的用法如下:

```
let instanceOfClass = new NoImplicitThisClass(); 
instanceOfClass.logToConsole();

```

在这里，我们创建了一个名为`instanceOfClass`的变量来保存`NoImplicitThisClass`的实例，调用`logToConsole`函数将输出以下内容:

```
this.name : undefined

```

事情是这样的:`this`属性没有引用`NoImplicitThisClass`类。这是由于 JavaScript 中的`this`属性的范围。在 JavaScript 中，默认情况下，方法中的`this`范围不绑定到任何引用。

如果`noImplicitThis`编译器选项打开，编译器将产生以下错误:

```
error TS2683: 'this' implicitly has type 'any' because it does not have a type annotation

```

这里，编译器通知我们，回调函数中对`this.name`的引用没有引用`NoImplicitThisClass`类的`this`属性。

### 解决`noImplicitThis`问题

这个错误可以通过将`this`属性传递到`callback`函数来解决，如下所示:

```
let callback = function (_this) { 
    console.log(`this.name : ${_this.name}`); 
} 
setTimeout(callback, 2000, this);

```

这里，我们向回调函数添加了一个名为`_this`的参数，然后将这个参数的值传递给`setTimeout`调用。

解决此错误的另一种常见方法是使用箭头函数。这在 React 中很常见:

```
let callback = () => { 
    console.log(`this.name : ${this.name}`); 
} 
setTimeout(callback, 2000)

```

这里，我们用 arrow 函数语法替换了 function 关键字。

两种解决方案都将产生以下结果:

```
this.name : Tom

```

在我的 React 项目中，我经常使用 arrow 函数来处理`this`问题，我推荐使用 arrow 函数，因为它要干净得多。

## `noImplicitReturns`

`noImplicitReturns`编译器选项将确保每个用返回值声明的函数必须返回函数中定义的值。
让我们考虑以下代码:

```
function fetchUsernameById(id: number): string { 
    if (id === 2) return "Sam"; 
} 
console.log(`fetchUsernameById(4) : ${fetchUsernameById(4)}`)

```

这里，`fetchUsernameById`有一个 number 类型的参数`id`并返回一个字符串值。该函数检查作为参数传入的值是否等于`2`。如果是，则返回字符串值`Sam`。但是，如果参数的值不等于`2`，则不返回任何内容。

这将是运行此代码的输出:

```
fetchUsernameById(4) : undefined

```

在这里，我们可以看到，`fetchUsernameById`函数为不等于`2`的任何参数值返回`undefined`。
如果`noImplicitReturns`编译器选项设置为`true`，编译器会产生一个错误:

```
error TS7030: Not all code paths return a value.

```

### 解决`noImplicitReturns`问题

这个错误可以通过为不等于`2`的 id 返回一个字符串值来解决:

```
function fetchUsernameById(id: number): string { 
    if (id === 2) 
      return "Sam"; 
    return "No user with such id"
}

```

这里，我们在函数的末尾添加了一个 return 语句，每当使用一个值不等于`2`的参数调用函数时，该语句将返回字符串“No user with such id”。

## `strictNullChecks`

`strictNullChecks`编译器选项用于在我们的代码中查找在使用时变量值可能是`null`或`undefined`的实例。
让我们考虑以下代码:

```
let a: number; 
let b = a;

```

上述代码将生成以下错误:

```
error TS2454: Variable 'a' is used before being assigned

```

这个错误告诉我们，变量`a`的值在尚未赋值时被使用。
从技术上讲，`a`的值可以是`undefined`。

### 解决`strictNullChecks`问题

这个错误可以通过确保变量`a`在使用前被赋值来解决:

```
let a: number = 4; 
let b = a;

```

这里，我们简单地将`4`的值赋给名为`a`的变量，这将解决错误。

修复此错误的另一种方法是使用联合类型通知编译器，我们知道变量在使用时可能未赋值，我们将自己处理后果:

```
let a: number | undefined; 
let b = a;

```

## 结论

在本文中，我们研究了可用于配置 TypeScript 编译器的各种高级编译器选项。我们还看到了与每个编译器选项相关的错误消息以及如何解决它们。

查看 Typescript 官方文档，了解更多的[编译器选项](https://www.typescriptlang.org/tsconfig#strict)。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.