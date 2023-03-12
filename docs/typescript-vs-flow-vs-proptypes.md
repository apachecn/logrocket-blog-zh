# 比较静态类型的 JavaScript 实现

> 原文：<https://blog.logrocket.com/typescript-vs-flow-vs-proptypes/>

*编者按:这篇文章于 2021 年 10 月 28 日编辑，以添加新的实现，提供更好、更易读的比较格式，并更彻底地讨论不同的选项。*

JavaScript 是一种动态类型语言，这意味着变量的数据类型由它们在运行时保存的值决定，并且当我们给它们赋予不同的值时，它们会在整个程序中发生变化。

没有内置的方法来注释或限制变量的类型。虽然这意味着我们可以更快地编写程序，而不必过多考虑变量应该具有的类型，但这也意味着当变量在不同类型之间移动时，我们的程序可能会出现错误和意外行为。

为了避免 JavaScript 的这些动态类型改变问题，我们需要用另一种语言编写程序，然后将其转换成 JavaScript。为此，我们需要一种 JavaScript 的类型化扩展语言来检查和限制变量的类型。

有几种语言和库提供 JavaScript 的静态类型扩展，例如:

在本文中，我们将重点关注 TypeScript、Flow 和 Prop 类型，比较和对比它们特性和功能。

## 比较类型检查库/语言

| **打字稿** | **流量** | **道具类型** | **开发商** |
| **微软** | **脸书** | **脸书** | **首次发布** |
| 2012 年 10 月 1 日 | 【2014 年 11 月 18 日 | 2017 年 4 月 8 日 | **执照** |
| 开源 | 麻省理工学院 | 麻省理工学院 | **社区** |
| 大 | 小 | 相对较小 | **依赖关系** |
| **性能** | 被认为是最快和错误最少的 | 一些报告的内存泄漏。 [流团队 2019 年](https://medium.com/flow-type/improvements-to-flow-in-2019-c8378e7aa007) 报告复检时间和内存使用量减少 | 未上报的性能统计数据 |
| [**类型检查**](#type-definition-checking-syntax) | 使用 JavaScript 类型，泛型类型定义，并有可选类型 可空 | 使用 JavaScript 类型，泛型类型定义，并有可选类型 也许是 | 使用自己的类型验证器对象 |
| [**型铸造**](#typecasting) | 使用关键字 作为 | 使用符号 : | 不适用 |
| **支持[接口、枚举和自定义类型对象](#interfaces-enums-custom-types)** | 支持用关键字 接口 和 枚举声明的接口和枚举 | 支持接口和枚举，用关键字 声明接口 和 枚举 | 支持 enum 使用自己的选项，proptypes . one of([…]) |
| [**文本编辑器支持**](#text-editor-support) | 多个编辑器/ide 提供了大量支持，包括智能感知(自动完成)、定位定义、错误/警告等功能。 | 多个编辑器/ide 提供了大量支持，包括智能感知(自动完成)、定位定义、错误/警告等功能。 | 有限支持 Visual Studio 代码和 WebStorm 自动生成组件的属性类型 |
| [**资源和文档**](#resources-documentation) | Massive support with several editors/IDEs providing functionality like IntelliSense (autocomplete), go-to definition, errors/warnings, etc. | Massive support with several editors/IDEs providing functionality like IntelliSense (autocomplete), go-to definition, errors/warnings, etc. | Limited support with Visual Studio Code and WebStorm to autogenerate propTypes of components |
| [**社区和项目支持**](#community-project-support) | 由多个现成的框架和库支持，例如 Vue、React、Angular、Express 等。，包括 Nest.js | 可以通过 Babel 安装在现有项目上。最常见的用法是与 React 一起使用。 | 主要用于 React(它是从那里提取的),但也可以通过手动调用proptypes . checkproptypes用于其他框架 |
| **堆栈溢出上发布的问题** | [超过 10 万+帖子](https://stackoverflow.com/questions/tagged/typescript) | [大约 500 个帖子](https://stackoverflow.com/search?q=facebook+flow&s=371fcc4b-982f-45e3-b960-e06b49d967e2) | [大约 500 个帖子](https://stackoverflow.com/search?q=proptypes&s=14bfb66b-084b-49e5-9d9c-5e4d2325a824) |
| **编译器错误检测** | 在 ide 和文本编辑器中可用 | 在支持的 ide 和编辑器中可用 | 不可用 |
| **语法** | 全面的类型检查，包括静态和动态类型注释 | 全面的类型检查，包括静态和动态类型注释 | 依赖于对 PropTypes 对象的引用 |
| **仿制药** | 支持的 | 支持的 | 不支持 |
| **对现有项目的支持** | 可以添加 TypeScript 包来支持 TypeScript | 用巴别塔添加支持 | 通过安装库和手动调用 `PropTypes.checkPropTypes` 添加支持 |
| 在本文中，我们将从许多特性和能力方面比较和对比这些 JavaScript 扩展技术。 | 当我们探讨它们的相似性和差异时，前面要注意的一个重要差异是，与 TypeScript 不同，Flow 和 PropTypes 不是编程语言。Flow 和 PropTypes 只是静态类型检查库，他的不同影响了哪些框架支持它们的使用。 | 在项目中设置 TypeScript | 在本文比较的三种技术中，TypeScript 拥有最多的库支持，在 Vue、Angular 或 React 中设置项目以使用 TypeScript 很容易，并且开箱即用。 |

例如，我们可以通过从 Vue CLI 选择 TypeScript 选项来构建带有 TypeScript 的 [Vue 项目，因为它是内置的。一旦我们选择了 TypeScript，那么我们可以在我们的`tsconfig.json`文件中将其配置为 transpile to JavaScript(通常设置 ECMAScript 2009 以最大化浏览器的支持)。](https://vuejs.org/v2/guide/typescript.html)

然后，当我们在组件创建期间引用 Vue 时，我们将得到 TypeScript 类型推断:

## 同样，使用 Vue，我们可以用自己的特殊方式用 TypeScript 和[类](https://blog.logrocket.com/when-how-use-interfaces-classes-typescript/)编写组件，就像这样:

这里，我们可以看到熟悉的 JavaScript 语法和一些 TypeScript 注释。例如，Angular 是用 TypeScript 构建的，它几乎只使用 TypeScript。曾经有一个用普通 JavaScript 编写 Angular 应用程序的选项，但它从未流行起来。

在项目中设置流程

```
// tsconfig.json
{
  "compilerOptions": {
    // this aligns with Vue's browser support
    "target": "es5",
    // this enables stricter inference for data properties on `this`
    "strict": true,
    // if using webpack 2+ or rollup, to leverage tree shaking:
    "module": "es2015",
    "moduleResolution": "node"
  }
}

```

另一方面，我们可以通过在 Babel 配置中添加流支持来为我们的项目添加流。

```
import Vue from 'vue'

const Component = Vue.extend({
  // ...
})

```

要添加流支持，我们只需运行:

```
<template>
  <button @click="onClick">Click!</button>
</template>

<script lang="ts">
import * as Vue from "vue";
import { Component } from "vue-property-decorator";

@Component()
class App extends Vue {
  public message: string = "Hello World";

  public onClick(): void {
    window.alert(this.message);
  }
}

export default App;
</script>

```

并将以下内容添加到`.babelrc`文件中:

## 在项目中设置属性类型

要在我们的项目中添加 PropTypes，我们只需安装 PropTypes 库，定义我们的组件 prop 类型，并手动调用`PropTypes.checkPropTypes`验证方法。

为了安装 PropTypes，我们运行:

```
npm install --save-dev @babel/preset-flow

```

然后，我们定义组件属性类型验证，用属性/值设置一个对象:

```
{
  "presets": ["@babel/preset-flow"]
}

```

## 并运行 PropType 检查:

类型定义和检查语法

如前所述，这三种技术都提供了对变量的类型检查和限制，这里我们将对它们进行比较。

```
npm install --save prop-types

```

流和类型脚本类型定义

```
 const myPropTypes = {
  name: PropTypes.string,
  age: PropTypes.number,
  // ... define your prop validations
};

const props = {
  name: 'hello', // is valid
  age: 'world', // not valid
};

```

Flow 和 TypeScript 具有非常相似的类型定义语法。他们都:

```
// Let's say your component is called 'MyComponent'

// Works with standalone PropTypes
PropTypes.checkPropTypes(myPropTypes, props, 'age', 'MyComponent');

// This will warn as follows:
// Warning: Failed prop type: Invalid prop `age` of type `string` supplied to
// `MyComponent`, expected `number`.

```

## 为类型检查变量支持 JavaScript 原始类型和派生(对象)类型

对于类型注释有相似的语法

### 使用运算符和各种实用程序类型来保持 JavaScript 类型系统的灵活性

具有可选属性的类型，即 TypeScript 具有`nullable`类型，而 Flow 具有`maybe`类型

*   有泛型，我们可以用它来编写采用变量类型的代码
*   有关最后一个项目符号的示例，请参见下文:
*   泛型采用一个变量类型，可以用不同的类型重载，例如`identity<string>('foo')`。
*   此外，下面的代码块在 TypeScript 和 Flow 中都是相同的，其中函数接受两个类型为`string`的变量，并将它们连接在一起返回。
*   PropTypes 类型验证程序

另一方面，PropTypes 使用自己的类型验证器来设置组件属性的类型——也就是说，在 PropTypes 中，我们将使用`PropTypes`对象来定义类型。

```
function identity<T>(value: T): T {
    return value
}

```

铅字铸造

Flow 和 TypeScript 都提供了将变量从一种类型显式转换为另一种类型的能力。

```
function concat(a: string, b: string) {
  return a + b;
}

```

### 对于 Flow，我们使用符号`:`进行造型，而在 TypeScript 中，我们使用关键字`as`进行造型。

PropTypes 不提供将属性或变量从一种类型转换为另一种类型的能力。

```
import React from 'react';
import PropTypes from 'prop-types'

class MyComponent extends React.Component {
  render() {
    // ... do things with the props
  }
}

MyComponent.propTypes = {
  optionalArray: PropTypes.array,
...

```

## 接口、枚举和自定义类型

接口是限制对象的结构和属性的类型定义。[枚举](https://blog.logrocket.com/writing-readable-code-with-typescript-enums-a84864f340e9/)是列出的值，可以通过将变量限制为保存其值之一来用作类型。Flow、TypeScript 和 PropTypes 允许定义自定义类型限制。

Flow 和 TypeScript 使用关键字`interface`定义这些自定义类型对象。它们都允许类使用`implements`关键字继承接口的结构。参见下面的示例。

```
// TypeScript
let value = 1 as number;

// Flow
let value = 1;
(value: number);

```

如果我们没有实现接口中列出的所有成员，那么流和 TypeScript transpilers 将会出错。

## Flow 和 TypeScript 还使用`enum`关键字定义了用于类型检查的枚举列表。

Flow 的一个独特特性是，我们可以将类型检查代码放在注释中，如下所示:

这在 TypeScript 中不可用。然而，这并没有太大的吸引力，因为注释没有自动完成或语法高亮。

```
interface PersonInterface {
  firstName: string;
  lastName: string;
  fullName(firstName: string, lastName: string): string
}

class Person implements PersonInterface {
    firstName: string;
    lastName: string;
    constructor(firstName: string, lastName: string){
        this.firstName = firstName;
        this.lastName = lastName;
    }

    fullName(firstName: string, lastName: string): string {
        return `${firstName} ${lastName}`;
    }
}

```

另一方面，PropTypes 使用`PropTypes.oneOf`定义枚举类型，如下所示:

PropTypes 还使用`PropTypes.shape`定义了对象类型结构。

```
enum Status {
  Active,
  Paused,
  Off,
}

const status: Status = Status.Active //correct
const wrongStatus: Status = 'test' //incorrect and not conforming to defined type

```

文本编辑器支持

```
function greet(greeting /*: string*/) /* : string */ {
  return greeting;
}

```

Flow 和 TypeScript 都有来自 Visual Studio Code 和 Sublime 等编辑器的内置或插件支持。

对 Flow 和 TypeScript 的编辑器支持意味着开发人员可以获得如下功能:

```
...
optionalEnum: PropTypes.oneOf(['News', 'Photos']),
...

```

智能感知(自动完成)

```
...
optionalObjectWithShape: PropTypes.shape({
    optionalProperty: PropTypes.string,
    requiredProperty: PropTypes.number.isRequired
}),
...

```

## 转到定义/查看定义

诊断(错误、警告)

悬停类型信息

*   可切换的代码覆盖率报告
*   还有[文档](https://flow.org/en/docs/install/)通过 Babel 在现有项目中安装流支持。阅读编辑器/IDE 安装的更多细节[获得流支持](https://flow.org/en/docs/editors/)和[打字稿编辑器支持](https://github.com/Microsoft/TypeScript/wiki/TypeScript-Editor-Support)。
*   PropTypes 还对 Visual Studio 代码提供了一些支持，用于为组件的赋值自动生成适当的组件类型。
*   PropTypes 也[有能力通过手动调用`PropTypes.checkPropTypes`应用到其他库](http://PropTypes.checkPropTypes)(在 React 中自动调用)。虽然在 PropTypes 的官方文档中提到了对其他库的支持，但是没有很多外部文档说明这如何适用于不同的库。
*   效率和性能

Flow 和 TypeScript 之间的性能差异很小。TypeScript 始终使用 500-600MB 的内存，流量也没有太大的不同。它们在计算资源效率方面没有太大的区别。

PropTypes 效率？根据这个 Reddit 线程的说法，TypeScript 速度更快，错误更少。

与对 Flow 和 PropTypes 的支持相比，社区对 TypeScript 的支持是最大的。除了官方来源之外，Flow 和 PropTypes 的文档也更少，关于 StackOverflow 的问题也更少，正如我们之前在本文顶部的表格中讨论的那样。

## 许多流行的前端库、框架和平台都内置了对 TypeScript 的支持，比如 Vue、Angular 和 React。像 [Express](https://expressjs.com/) 这样的 Node.js web 框架也可以添加 TypeScript 支持， [Nest.js](https://nestjs.com/) 内置了 TypeScript 支持，允许我们使用 TypeScript 进行开发，而无需对我们的项目进行大的更改。

例如，要在 Express 中使用 TypeScript，我们只需运行下面的命令来安装 TypeScript transpiler。

然后我们可以添加一个`tsconfig.json`文件来配置我们的 TypeScript transpiler，编写如下代码:

为了安装 Express 和 Node 的标准库的类型定义，我们运行:

正如我们所看到的，对现有的库使用 TypeScript 并不太难，因为许多库和框架都有 TypeScript 类型定义。关于 TypeScript 的更多类型定义，我们可以去 [DefinitelyTyped](http://definitelytyped.org/) 网站。

资源和文档

```
npm install --save-dev typescript

```

就可用的资源和文档数量而言，TypeScript 无疑胜出。
打字稿报价:

```
{
    "compilerOptions": {
        "module": "commonjs",
        "esModuleInterop": true,
        "target": "es6",
        "noImplicitAny": true,
        "moduleResolution": "node",
        "sourceMap": true,
        "outDir": "dist",
        "baseUrl": ".",
        "paths": {
            "*": [
                "node_modules/*"
            ]
        }
    },
    "include": [
        "src/**/*"
    ]
}

```

作为一个额外的好处，TypeScript playground 支持不同版本的 TypeScript，因此我们可以看到我们的代码如何处理不同版本的 TypeScript transpiler。

```
npm install --save-dev @types/node @types/express

```

结论

## 由于 TypeScript 受到许多库和框架的支持，许多人每天都在使用 TypeScript。TypeScript 中有大量关于每个特性的信息，包括示例和解释。这很可能是实现静态类型的最佳选择。

另一方面， [Flow 只有一些文档](https://flow.org/)和一个关于如何用 React 设置 [Flow 的简短文档，也可以应用到其他 JavaScript 框架上设置 Flow。然而，到目前为止，PropTypes 拥有三个选项中](https://flow.org/en/docs/react/)[最稀疏的文档](https://github.com/facebook/prop-types)，尽管存在一些帖子，如 [this](https://blog.logrocket.com/validating-react-component-props-with-prop-types-ef14b29963fc/) 。

[LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

## LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

.

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup): Full visibility into your web and mobile apps

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

[LogRocket](https://lp.logrocket.com/blg/typescript-signup) is a frontend application monitoring solution that lets you replay problems as if they happened in your own browser. Instead of guessing why errors happen, or asking users for screenshots and log dumps, LogRocket lets you replay the session to quickly understand what went wrong. It works perfectly with any app, regardless of framework, and has plugins to log additional context from Redux, Vuex, and @ngrx/store.

In addition to logging Redux actions and state, LogRocket records console logs, JavaScript errors, stacktraces, network requests/responses with headers + bodies, browser metadata, and custom logs. It also instruments the DOM to record the HTML and CSS on the page, recreating pixel-perfect videos of even the most complex single-page and mobile apps.

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.