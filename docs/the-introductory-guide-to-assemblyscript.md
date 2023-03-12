# AssemblyScript 入门指南

> 原文：<https://blog.logrocket.com/the-introductory-guide-to-assemblyscript/>

WebAssembly(或 Wasm)是 web 浏览器的一个相对较新的功能，但它有可能极大地扩展 web 作为应用服务平台的能力。

虽然 web 开发人员开始使用 WebAssembly 可能会有一个陡峭的学习曲线，但 [AssemblyScript](https://assemblyscript.org/) 提供了一种绕过它的方法。让我们首先看看为什么 WebAssembly 是一项如此有前途的技术，然后我们将看到 AssemblyScript 如何帮助释放其潜力。

## web 程序集

WebAssembly 是浏览器的底层语言，给开发者提供了除 JavaScript 之外的 web 编译目标。它使网站代码在安全的沙盒环境中以接近本地的速度运行成为可能。

它是在所有主要浏览器(Chrome、Firefox、Safari 和 Edge)代表的参与下开发的，他们[在 2017 年初达成了设计共识](https://lists.w3.org/Archives/Public/public-webassembly/2017Feb/0002.html)。所有这些浏览器现在都支持 WebAssembly，它可以在[大约 87%的浏览器中使用。](https://caniuse.com/#feat=wasm)

WebAssembly 以二进制格式交付，这意味着它在大小和加载时间上都比 JavaScript 有优势。然而它也有一个人类可读的文本表示。

当 WebAssembly 首次发布时，一些开发人员认为它有可能最终取代 JavaScript 成为 web 的主要语言。但是最好把 WebAssembly 看作是一个新的工具，与现有的 web 平台很好地集成，这是它的[高层目标](https://webassembly.org/docs/high-level-goals/)之一。

WebAssembly 更吸引人，而不是取代现有用例的 JavaScript，因为它支持新的用例。WebAssembly 还不能直接访问 DOM，大多数现有网站都希望坚持使用 JavaScript，经过多年的优化，JavaScript 已经非常快了。以下是 web assembly[自己的可能用例列表](https://webassembly.org/docs/use-cases/)的示例:

*   比赛
*   科学可视化和模拟
*   CAD 应用
*   图像/视频编辑

这些应用程序的共同特点是，我们通常认为它们是桌面应用程序。通过为 CPU 密集型任务提供接近本机的性能，WebAssembly 使得将更多这些应用程序转移到 web 上变得可行。

现有网站也可以从 WebAssembly 中受益。 [Figma](https://www.figma.com/) 提供了一个现实世界的例子，他们使用 WebAssembly 来显著改善加载时间。如果一个网站使用大量计算的代码，用 WebAssembly 只替换那些代码来提高性能是有意义的。

所以现在你可能对 WebAssembly 感兴趣了。你可以学习语言本身，然后[直接编写它](https://blog.scottlogic.com/2018/04/26/webassembly-by-hand.html)，但它实际上是为了成为其他语言的[编译目标。它](https://github.com/appcypher/awesome-wasm-langs)[被设计](https://webassembly.org/docs/c-and-c++/)对 C 和 C++有很好的支持，Go [在 1.11 版本中为它增加了实验性支持](https://golang.org/doc/go1.11#wasm)，Rust 也[在其中投入巨资](https://www.rust-lang.org/what/wasm)。

但也许你不想为了使用 WebAssembly 而学习或使用这些语言中的一种。这就是 AssemblyScript 发挥作用的地方。

## 汇编脚本

AssemblyScript 是一个类型脚本到 WebAssembly 的编译器。由微软开发的 TypeScript 为 JavaScript 增加了类型。它已经变得非常流行，即使对于不熟悉它的人来说，AssemblyScript 也只允许有限的一部分类型脚本特性，所以不需要很长时间就能上手。

因为它与 JavaScript 非常相似，AssemblyScript 让 web 开发人员可以轻松地将 WebAssembly 集成到他们的网站中，而不必使用完全不同的语言。

### 尝试一下

让我们编写我们的第一个 AssemblyScript 模块(以下所有代码都可以在 [this GitHub repository](https://github.com/dguo/assemblyscript-demo) 中找到)。我们需要最低版本为 8 的 [Node.js](https://nodejs.org/) 来支持[web assembly](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WebAssembly#Browser_compatibility)。

转到一个空目录，创建一个`package.json`文件，并安装 AssemblyScript。注意，我们需要直接从[的 GitHub 库](https://github.com/AssemblyScript/assemblyscript)安装它。它没有在 npm 上发布，因为 AssemblyScript 开发者[认为编译器还没有准备好](https://github.com/AssemblyScript/assemblyscript/issues/442#issuecomment-458523778)用于一般用途。

```
mkdir assemblyscript-demo
cd assemblyscript-demo
npm init
npm install --save-dev github:AssemblyScript/assemblyscript
```

使用附带的`asinit`命令生成脚手架文件:

```
npx asinit .
```

我们的`package.json`现在应该包括这些脚本:

```
{
  "scripts": {
    "asbuild:untouched": "asc assembly/index.ts -b build/untouched.wasm -t build/untouched.wat --sourceMap --validate --debug",
    "asbuild:optimized": "asc assembly/index.ts -b build/optimized.wasm -t build/optimized.wat --sourceMap --validate --optimize",
    "asbuild": "npm run asbuild:untouched && npm run asbuild:optimized"
  }
}
```

顶层`index.js`看起来像这样:

```
const fs = require("fs");
const compiled = new WebAssembly.Module(fs.readFileSync(__dirname + "/build/optimized.wasm"));
const imports = {
  env: {
    abort(_msg, _file, line, column) {
       console.error("abort called at index.ts:" + line + ":" + column);
    }
  }
};
Object.defineProperty(module, "exports", {
  get: () => new WebAssembly.Instance(compiled, imports).exports
});
```

它允许我们轻松地`[require](https://nodejs.org/api/modules.html#modules_require_id)`我们的 WebAssembly 模块，就像普通的 JavaScript 模块一样。

`assembly`目录包含我们的 AssemblyScript 源代码。生成的示例是一个简单的加法函数。

```
export function add(a: i32, b: i32): i32 {
  return a + b;
}
```

如果您希望函数签名看起来像`add(a: number, b: number): number`，就像在 TypeScript 中一样，它使用`i32`的原因是 AssemblyScript 使用 WebAssembly 的[特定整数和浮点类型](https://docs.assemblyscript.org/basics/types)，而不是 TypeScript 的[通用`number`类型](https://www.typescriptlang.org/docs/handbook/basic-types.html#number)。

让我们来构建这个例子。

```
npm run asbuild
```

`build`目录现在应该包括以下文件:

```
optimized.wasm
optimized.wasm.map
optimized.wat
untouched.wasm
untouched.wasm.map
untouched.wat
```

我们得到了简单的和优化的版本。对于每个构建版本，我们得到一个`.wasm`二进制文件，一个`.wasm.map` [源映射](https://developer.mozilla.org/en-US/docs/Tools/Debugger/How_to/Use_a_source_map)，以及一个`.wat`二进制文件的文本表示。文本表示被设计为人类可读的，但是对于我们的目的，我们不需要阅读或理解它——使用 AssemblyScript 的部分原因是我们不需要使用原始 WebAssembly。

启动节点，像使用其他模块一样使用我们编译的模块。

```
$ node
Welcome to Node.js v12.10.0.
Type ".help" for more information.
> const add = require('./index').add;
undefined
> add(3, 5)
8
```

这就是从 Node 调用 WebAssembly 所做的全部工作！

### 添加观察脚本

对于开发来说，我推荐使用 [onchange](https://github.com/Qard/onchange) 在你修改源代码的时候自动重建模块，因为 AssemblyScript [还不包括观察模式](https://github.com/AssemblyScript/assemblyscript/issues/624)。

```
npm install --save-dev onchange
```

给`package.json`添加一个`asbuild:watch`脚本。包含`-i` [标志](https://github.com/Qard/onchange#initial--i---initial)，以便在运行命令时立即运行初始构建。

```
{
  "scripts": {
    "asbuild:untouched": "asc assembly/index.ts -b build/untouched.wasm -t build/untouched.wat --sourceMap --validate --debug",
    "asbuild:optimized": "asc assembly/index.ts -b build/optimized.wasm -t build/optimized.wat --sourceMap --validate --optimize",
    "asbuild": "npm run asbuild:untouched && npm run asbuild:optimized",
    "asbuild:watch": "onchange -i 'assembly/**/*' -- npm run asbuild"
  }
}
```

现在你可以运行`asbuild:watch`而不是不断地重新运行`asbuild`。

### 表演

让我们编写一个基本的基准测试来了解我们可以获得什么样的性能提升。WebAssembly 的专长是处理 CPU 密集型任务，如数值计算，所以让我们用一个函数来确定一个整数是否是质数。

我们的参考实现如下所示。这是一个天真的、蛮力的解决方案，因为我们的目标是执行大量的计算。

```
function isPrime(x) {
    if (x < 2) {
        return false;
    }

    for (let i = 2; i < x; i++) {
        if (x % i === 0) {
            return false;
        }
    }

    return true;
}
```

等效的 AssemblyScript 版本只需要一些类型注释:

```
function isPrime(x: u32): bool {
    if (x < 2) {
        return false;
    }

    for (let i: u32 = 2; i < x; i++) {
        if (x % i === 0) {
            return false;
        }
    }

    return true;
}
```

我们将使用 [Benchmark.js](https://benchmarkjs.com/) 。

```
npm install --save-dev benchmark
```

创建`benchmark.js`:

```
const Benchmark = require('benchmark');

const assemblyScriptIsPrime = require('./index').isPrime;

function isPrime(x) {
    for (let i = 2; i < x; i++) {
        if (x % i === 0) {
            return false;
        }
    }

    return true;
}

const suite = new Benchmark.Suite;
const startNumber = 2;
const stopNumber = 10000;

suite.add('AssemblyScript isPrime', function () {
    for (let i = startNumber; i < stopNumber; i++) {
        assemblyScriptIsPrime(i);
    }
}).add('JavaScript isPrime', function () {
    for (let i = startNumber; i < stopNumber; i++) {
        isPrime(i);
    }
}).on('cycle', function (event) {
    console.log(String(event.target));
}).on('complete', function () {
    const fastest = this.filter('fastest');
    const slowest = this.filter('slowest');
    const difference = (fastest.map('hz') - slowest.map('hz')) / slowest.map('hz') * 100;
    console.log(`${fastest.map('name')} is ~${difference.toFixed(1)}% faster.`);
}).run();
```

在我的机器上，当我运行`node benchmark`时，我得到了这些结果:

```
AssemblyScript isPrime x 74.00 ops/sec ±0.43% (76 runs sampled)
JavaScript isPrime x 61.56 ops/sec ±0.30% (64 runs sampled)
AssemblyScript isPrime is ~20.2% faster.
```

注意，这个测试是一个[微基准测试](https://stackoverflow.com/a/2842707/1481479)，我们应该小心不要过度解读它。

对于一些更复杂的 AssemblyScript 基准，我推荐查看[这是一个波动方程基准](https://wasmboy.app/benchmark/)和[这是一个波动方程基准](https://jtiscione.github.io/webassembly-wave/index.html)。

### 加载模块

接下来，让我们在一个网站中使用我们的模块。创建`index.html`:

```
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8" />
        <title>AssemblyScript isPrime demo</title>
    </head>
    <body>
        <form id="prime-checker">
            <label for="number">Enter a number to check if it is prime:</label>
            <input name="number" type="number" />
            <button type="submit">Submit</button>
        </form>

        <p id="result"></p>

        <script src="demo.js"></script>
    </body>
</html>
```

创建`demo.js`。加载 WebAssembly 模块的[有多种方式](https://developers.google.com/web/updates/2018/04/loading-wasm)，但最有效的是用`[WebAssembly.instantiateStreaming](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WebAssembly/instantiateStreaming)`函数以流的方式编译和实例化它们。注意，我们需要[提供一个中止函数](https://docs.assemblyscript.org/details/debugging#overriding-abort)，如果[断言](https://docs.assemblyscript.org/basics/environment#utility)失败，就会调用这个函数。

```
(async () => {
    const importObject = {
        env: {
            abort(_msg, _file, line, column) {
                console.error("abort called at index.ts:" + line + ":" + column);
            }
        }
    };
    const module = await WebAssembly.instantiateStreaming(
        fetch("build/optimized.wasm"),
        importObject
    );
    const isPrime = module.instance.exports.isPrime;

    const result = document.querySelector("#result");
    document.querySelector("#prime-checker").addEventListener("submit", event => {
        event.preventDefault();
        result.innerText = "";
        const number = event.target.elements.number.value;
        result.innerText = `${number} is ${isPrime(number) ? '' : 'not '}prime.`;
    });
})();
```

现在安装[静态服务器](https://github.com/nbluis/static-server)。我们需要一个服务器，因为为了使用`WebAssembly.instantiateStreaming`，模块需要使用`application/wasm`的 [MIME 类型](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types)。

```
npm install --save-dev static-server

```

向`package.json`添加脚本。

```
{
  "scripts": {
    "serve-demo": "static-server"
  }
}
```

运行`npm run serve-demo`并在浏览器中打开本地主机 URL。在表单中提交一个数字，您应该会得到一条消息，表明这个数字是否是质数。现在我们已经从编写 AssemblyScript 一路走到了在网站中实际使用它。

## 结论

WebAssembly 和 AssemblyScript 不会神奇地让每个网站都变得更快，但这从来都不是重点。WebAssembly 令人兴奋，因为它可以使 web 适用于更多的应用程序。

类似地，AssemblyScript 使更多的开发人员可以访问 WebAssembly，这使得我们可以很容易地在默认情况下坚持使用 JavaScript，但当我们有需要大量数字处理的工作时，就引入 WebAssembly。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.