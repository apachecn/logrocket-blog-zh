# 使用 WebAssembly - LogRocket 博客将一个苗条的应用程序与 Rust 集成

> 原文：<https://blog.logrocket.com/integrating-svelte-app-rust-webassembly/>

Svelte 是一个 JavaScript 框架，在 web 应用程序开发人员中很受欢迎。但是，如果我们为了速度、安全性和其他好处而想使用 Rust 编程语言，而不是 JavaScript 或 TypeScript，该怎么办呢？多亏了 WebAssembly，我们才能做到这一点。

WebAssembly 像 TypeScript 一样是类型安全的，因为它是预先编译的，而不是在运行时编译，甚至比 JavaScript 快得多。将这一点与 Svelte 的易用性及其无需虚拟 DOM 即可更新 UI 的能力结合起来，我们甚至可以让大型应用程序运行得非常快！

在本文中，我们将看到如何通过将 Rust 编译成 WebAssembly 来连接一个带有 Rust 代码的瘦前端，以及如何在 JavaScript 和 Rust 之间调用函数和传递数据。具体来说，我们将涵盖:

您将需要 Rust 的基本知识来跟随本教程。这可能包括读、写和理解变量声明、`if…else`块、循环和结构。一些关于苗条的知识也会有所帮助。

## 什么是苗条？

Svelte 是[另一个前端框架，类似于 React](https://blog.logrocket.com/should-you-switch-react-svelte/) 或 Vue。像这两个，可以用 Svelte 做单页应用。然而，Svelte 的一些特性使它与其他框架有着显著的不同。

例如，Svelte 不像其他的，它主要是一个编译框架；大多数苗条库是一个`devDependency`而不是一个运行时依赖。这有助于使最终的应用程序更小，更快地供客户端下载。

另一个区别是 Svelte 不使用虚拟 DOM。相反，它使用各种策略来只更新页面中发生变化的特定部分。这减少了开销，使应用程序更快。

Svelte 还提供了易用性，不需要类或函数来声明组件。每个细长的文件被视为一个组件本身。

最后，Svelte 的钩子很少。虽然生命周期特性需要钩子，但是 Svelte 不需要复杂的状态管理钩子。状态可以简单地通过声明一个变量来存储和使用，就像在普通 JavaScript 中一样。

## Wasm 是什么？

WebAssembly (Wasm)是一种二进制指令格式，我们的浏览器可以与 JavaScript 一起运行。

然而，与 JavaScript 不同，它已经是二进制格式，在编译时已经解析了类型信息，并且不需要[解释或即时编译](https://blog.logrocket.com/how-javascript-works-optimizing-the-v8-compiler-for-efficiency/)。由于这些和其他一些原因，它在许多情况下比 JavaScript 快得多。

Wasm 是一个编译目标，几种语言——包括 Rust——都可以编译到它上面。因此，我们可以用 Rust 编写程序，编译成 Wasm，然后在浏览器中用 JavaScript 运行它。

## 为什么用 Wasm 带锈？

[使用带锈的 Wasm】可以有很多好处。让我们看几个实际的项目例子。](https://blog.logrocket.com/getting-started-with-webassembly-and-rust/)

你可以在 Rust 中编写一个包含核心函数的库，然后通过将其编译成 Wasm，将其用于 web 应用程序和桌面或命令行应用程序。8086 仿真器就是这样一个例子。

在 8086 仿真器中，核心仿真器和编译器是在一个平台不可知的库中编写的，然后通过编写一个瘦的特定于平台的接口来适应命令行和 web 格式。

另一个很好的用途是快速获得库的交互式演示，而不必编写复杂的命令行程序或纯粹基于文本的示例。我们可以创建一个图形化的 web 界面，并从事件处理程序(如按钮点击)中调用库函数。

看看这个 `[pcb-rs](https://yjdoc2.github.io/pcb-rs-examples/)` [库示例](https://yjdoc2.github.io/pcb-rs-examples/)的 [web 界面，它演示了这个用例。这个例子提供了一个硬件模拟库的演示，以及一个交互界面来使用它，用 Svelte 写的。](https://yjdoc2.github.io/pcb-rs-examples/)

这不仅仅限于小项目；甚至像 Figma 这样的大型项目也使用 Wasm 来使他们的桌面应用适应网络，这样他们就可以方便容易地使用。

顺便说一下，Wasm 并不是服务器端逻辑的替代品。它仍然运行在客户端的浏览器中，所以您仍然需要一些东西来服务 Wasm 文件，即使它只是一个静态文件服务器。

## 用 Rust、Wasm、Node 和其他软件包设置我们的系统

让我们从设置系统开始。

首先，[按照他们官网](https://www.rust-lang.org/learn/get-started)上的说明安装 Rust。这将在您的系统上安装和设置 Rust 语言工具，如编译器和标准库。

然后，[安装](https://rustwasm.github.io/wasm-pack/installer/) `[wasm-pack](https://rustwasm.github.io/wasm-pack/installer/)` [，一个把 Rust 编译成 Wasm](https://rustwasm.github.io/wasm-pack/installer/) 的辅助工具。该工具还负责下载编译到 Wasm 所需的工具链。

最后，按照安装指南通过[安装 Node.js、npm 和 npx。你可能需要](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm)[单独安装 npx 作为一个全局包](https://www.npmjs.com/package/npx)。

要检查所有设置是否正确，请尝试运行以下命令，并查看是否有任何内容出错:

```
> npm --version
> npx --version
> node --version
> cargo --version
> rustc --version
> wasm-pack --version
> rustc --print target-list # This list should have wasm32 targets

```

如果这些命令运行时没有任何错误，那么我们就成功地完成了系统设置。

## 建立我们的苗条+Wasm+Rust 项目

有多种方法可以建立一个使用 Svelte、Wasm 和 Rust 的 web 项目。例如， `[rust-wasm](https://github.com/rustwasm/wasm-pack)` [组](https://github.com/rustwasm/wasm-pack)的`[wasm-pack](https://github.com/rustwasm/wasm-pack)` [为简单的 HTML-CSS-JS 应用提供了模板。还有几个 React 或 Svelte with Rust 和 Wasm 的项目模板。](https://github.com/rustwasm/wasm-pack)

然而，代替任何模板，我们将使用一个 npm 插件，这将为我们的项目结构提供更多的灵活性。

### 创建我们苗条的应用程序

首先，创建一个项目目录。在该目录中，我们将首先通过运行以下命令，使用他们的模板创建一个 Svelte 应用程序:

```
> npx degit sveltejs/template svelte

```

在上面的代码中，我们将我们的应用程序命名为`svelte`，但是您可以随意命名。

再次在项目目录中，运行以下命令创建一个 Rust library 项目目录:

```
> cargo new --lib rust

```

在上面的代码中，我们将库命名为`rust`，但是您可以给它一个不同的名称。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

现在，进入`svelte`目录，运行下面的命令来安装基本的依赖项:

```
> npm install

```

在此之后，我们将安装`rollup-plugin-rust`，它会自动将 Rust 代码编译到 Wasm，并允许我们轻松地将 Rust 中的东西导入 JavaScript。

```
> npm install @wasm-tool/rollup-plugin-rust

```

接下来，打开`rollup.config.js`文件并添加以下导入语句:

```
import rust from '@wasm-tool/rollup-plugin-rust';

```

在同一个文件中，在`serve`函数之后，会有一个`export default`语句。此语句导出配置对象。在配置对象中，会有一个`plugins`数组。我们需要将 Rust 插件添加到这个数组中，就像这样:

```
...
plugins: [
     rust({ 
        verbose: true,
        serverPath: "build/"
     }),
     svelte({
...

```

[插件提供了几个选项](https://github.com/wasm-tool/rollup-plugin-rust)。最重要的是，对于我们的项目:

*   无论何时编译 Rust 代码，都会显示编译步骤及其输出
*   `serverPath`指定 Wasm 文件将从哪个路径提供

我们将`serverPath`设置为`build`,因为在开发模式下，Svelte 提供来自`build`目录的文件。如果我们想要部署我们的项目，需要相应地调整`serverPath`。

再次运行`npm install`来安装这个插件。这应该结束了我们的苗条应用程序的设置。

### 为我们的项目设置 Rust

要为我们的项目设置 Rust，请将目录更改为`rust`。打开`Cargo.toml`文件，在`[package]`键之后和`[dependencies]`键之前添加以下内容:

```
[lib]
crate-type = ["cdylib", "rlib"]

```

在`[dependencies]`键中，添加以下内容:

```
wasm-bindgen = "0.2.63"

```

运行一次`cargo build`来获取并安装依赖项。

至此，基本的项目设置已经就绪。

## 连接苗条和铁锈

我们将首先通过从 Rust 公开一个简单的`add`函数并从 Svelte 调用它来连接 Svelte 和 Rust。这一步很简单，但将帮助我们验证两者都已连接并按预期运行。

在`rust`目录中，打开`src/lib.rs`文件并从中删除默认测试。在现在为空的文件中，添加以下内容:

```
use wasm_bindgen::prelude::*;

#[wasm_bindgen]
pub fn add(a: usize, b: usize) -> usize {
    a + b
}

```

在上面的代码中，我们首先从`wasm_bindgen`导入了`prelude`，它提供了将 Rust 代码编译并绑定到 Wasm 所需的宏和其他项目。

此外，我们想要向 JavaScript 公开的任何东西都需要是公共的，因此函数上有了`pub`关键字。同时，`#[wasm_bindgen]`提供了必要的装订“胶水”

现在，回到`svelte`目录，打开`main.js`文件并添加下面的`import`命令:

```
import wasm from '../../rust/Cargo.toml';

```

在上面的代码中，我们将导入称为`wasm`，但是您可以随意命名它。路径应该指向我们要编译的项目的`Cargo.toml`文件，导入为 Wasm。我们可以像这样导入多个项目，用不同的名称和对应的路径。

有了这个，我们之前安装的插件将拦截导入，编译 Rust 代码，设置 Wasm，并为我们将其连接到我们的 Svelte 应用程序！

因为 Wasm 导入需要异步完成，所以我们将应用程序定义和`export default`语句更改如下:

```
import App from './App.svelte';
import wasm from '../../rust/Cargo.toml';

const init = async () => {
      const bindings = await wasm();
      const app = new App({
      target: document.body,
      props: {
                bindings,
              },
    });
};

init();

```

在上面的代码中，我们定义并调用了一个异步函数，它将等待导入，然后将其作为一个道具传递给应用程序。Rust 库使用`#[wasm_bindgen]`公开的一切现在都可以在这个导入的模块中访问；因此，我们的`add`函数可以作为`bindings.add`来访问。

现在我们将删除`App.svelte`文件中的所有内容，并添加以下内容:

```
<script>
export let bindings;
</script>

<h1>
{bindings.add(5,7)}
</h1>

```

接下来，运行`svelte`文件夹中的`npm run dev`。如果到目前为止一切都是正确的，它应该显示 Rust 编译(尽管在您第一次运行时可能需要一些时间)。

编译完成后，会显示服务器连接到`localhost`的细长消息。当你打开`localhost`网址时，它应该显示数字 12。

恭喜你，我们已经把铁锈和苗条联系起来了！

您可以通过添加和公开更多的函数并从 JavaScript 调用它们来进行一些尝试。

目前只能传递简单的参数，比如`usize`、`bool`等等。然而，我们将在后面看到如何向 JavaScript 公开结构和`impl`方法，以及如何从 Rust 调用 JavaScript 函数。

在我们开始之前，让我们给这个苗条的应用添加一点风格。

## 用`carbon-components-svelte`添加样式

这一小段将解释如何使用`carbon-components-svelte`为我们的苗条应用程序设置和添加风格。我们也可以[使用一些其他的库来进行造型，比如材质 UI](https://blog.logrocket.com/svelte-material-ui-advanced-material-design-components/) 或者 Bootstrap，这不会有太大的区别。

我们将使用最少的必要步骤快速添加`carbon-components-svelte`，但是您可以[查看库的文档](https://carbon-components-svelte.onrender.com/)以获得更多详细信息和示例。

首先，通过在`svelte/`目录中运行以下命令来安装`carbon-components-svelte`:

```
> npm i -D carbon-components-svelte

```

然后我们将样式表导入到`main.js`中。它支持一些开箱即用的主题，如`white`、`gray10`、`gray80`等，以及动态主题支持和自定义主题支持。我们将在这个例子中使用`gray80`主题。

在您的`main.js`文件中添加以下`import`命令:

```
import "carbon-components-svelte/css/g80.css";

```

在`App.svelte`文件中做如下微小的改变:

```
<script>
...
import {Content} from 'carbon-components-svelte';
...
</script>

<Content>
<h1>
{bindings.add(5,7)}
</h1>
</Content>

```

如果你现在看一下这个页面，它会有一个漂亮的灰色背景，数字 12 和以前一样，现在增加了边距和填充。

虽然你可以[用苗条的](https://blog.logrocket.com/build-web-components-svelte/)构建其他 web 组件，或者甚至[构建你自己的组件库](https://blog.logrocket.com/build-your-own-component-library-svelte/)，这是我们将要做的样式的范围。除了稍后使用输入组件之外，我们将把重点放在连接 Rust 和 Svelte 上。

## 公开结构和`impl`方法

下一节将稍微高级和复杂一些。我们将在 Rust 中定义一个结构，并在 JavaScript 中使用它来向函数传递值。我们还将看到如何公开`impl`方法并从 JavaScript 调用它们。

首先，让我们在 Rust 项目的`lib.rs`文件中添加一个名为`Car`的简单结构:

```
#[wasm_bindgen]
pub struct Car {
    pub number: usize,
    pub color:usize, // color in hex code
}

#[wasm_bindgen]
pub fn color(a: Car,color:usize) -> Car {
    Car {
      number: a.number,
      color
    }
}

```

在上面的代码中，我们将`#[wasm_bindgen]` Rust 库和一个`color`函数添加到结构中，该函数带有一个`Car`类型参数和一个`usize`类型的`color`。我们还在`Car`的`number`和`color`字段上显式添加了`pub`，以确保它们被暴露给 JavaScript。

在我们的`svelte`目录中，让我们如下更改我们的`App.svelte`文件:

```
<script>
export let bindings;
import {Content} from 'carbon-components-svelte';
let {Car,color} = bindings; // destructure for easier access
console.log(Car,color);
</script>

<Content>
<h1>
</h1>
</Content>

```

在上面的代码中，我们析构了`bindings`来提取`Car`和`color`。我们还删除了对`add`的调用。

如果我们看一下控制台，我们可以看到`Car`是一个类，`color`是一个函数。然而，我们不能使用`new Car()`直接将`Car`实例化为一个类，因为本质上，它是一个 Rust 结构，本身并不拥有`constructor`。

因此，要实例化它，我们必须将`new`方法添加到`Car`类中，并公开它:

```
#[wasm_bindgen]
impl Car {
    pub fn new() -> Self {
        Car { number: 0, color: 0 }
    }
}

```

注意，我们可以标记该结构的一个方法，比如上面代码中的`new`，作为一个`constructor`。然而，在下一节中，我们将只简要地触及这个概念。

接下来，在`App.svelte`中，添加以下内容:

```
let {Car,color} = bindings; // destructure for easier access

let c = Car.new();
c.number = 5;
c.color = 775577;
console.log(c);

let c2 = color(c,557755);

console.log(c2.number,c2.color);

```

如果我们现在看一下控制台，我们可以看到第一个`console.log`显示如下:

```
Object {ptr:...}

```

为了理解为什么`console.log(c)`会产生这个输出，我们必须理解`Car`实例到底是什么。

Wasm 在浏览器中的运行方式是基于堆栈的虚拟机。我们需要另外一整篇文章来讨论 WebAssembly 如何工作以及为什么工作。

出于我们的目的，我们需要理解的是 Wasm 有一个线性内存，就 Wasm 而言，它需要的一切都存储在那个内存中。

当我们从 Wasm 中获取一个对象时，它将它的指针存储在对象的线性内存中。因此，我们之前记录的对象只在线性内存中存储它们位置的指针。

对于较小的应用程序，我们可以忽略这些细节，但是随着应用程序规模的增长和传递的数据变得更加复杂，我们需要考虑这些细节。

例如，(几乎)所有跨越 JavaScript–Wasm 边界的东西都要从那个线性内存复制到 JavaScript 的内存；因此，具有许多值的大型对象将需要时间和计算能力来复制。

另一个问题是，在默认情况下，Wasm 有固定的 1MB 内存分配给它。如果需要一次传输的数据大于这个数，我们就会遇到内存限制错误。

事实上，由于 Wasm 代码中的其他信息也会占用一些 1MB 的内存，我们可能会比预期的更早达到内存限制。如果需要，我们将在本文的最后一节看到如何增加这个限制。

回到我们当前的实现，我们已经看到了如何公开和使用结构及其方法。接下来，让我们来看看如何公开相关的方法，或者采用`self`参数的函数:

```
#[wasm_bindgen]
impl Car {
    ...
    pub fn duplicate(&self) -> Self {
        Self {
            number: self.number + 1,
            mul: self.color,
        }
    }
    ...
}

```

在上面的代码中，我们不变地获取了`Car`，并使用`duplicate()`返回了一个新对象，其`number`比之前大一，其`color`相同。

接下来，在`App.svelte`中，运行以下程序:

```
let c = Car.new();
c.number = 5;
c.color = 775577;

...

let c3 = c2.duplicate();

console.log(c3.number,c3.color);

console.log(c2,c3);

```

我们在这里可以看到`c3`具有按照复制函数设置的值。更重要的是，我们可以看到`c2`和`c3`具有不同的`ptr`值，这表明它们确实是不同的对象。

如果我们添加一个方法来可变地获取`Car`，如下所示:

```
...
 pub fn change_number(&mut self,number:usize) {
    self.number = number;      
}
...

```

并在`c3`上这样称呼它:

```
c3.change_number(7);
console.log(c3.number);

```

然后我们会看到`c3`的数字已经改变。

这里有一个有用的提示，当你不知道哪些属性可以从 JavaScript 访问时，`console.log`那个对象并打开那个对象的原型描述。

在对象的原型描述中，您将能够看到 JavaScript 可以访问的函数和参数。事实上，您可以通过点击参数旁边的`>>`来检查参数的值，这将显示它们的当前值。

因此，即使我们在调用`change_number`之前已经记录了`c3`对象，我们现在也可以在控制台原型中扩展它的`number`信息。通过这样做，我们看到它有了新的值，而不是原来的值，因为`change_number`函数可变地采用了`Car`。

## 在 JavaScript 和 Rust 之间传递复杂的数据类型

到目前为止，我们传递的数据类型相当简单。甚至我们使用的结构也是由基本类型组成的。

我们也可以使用类似`Vec<_>`这样复杂的东西将向量从 Rust 传递给 JavaScript。所有这些工作的方式是`wasm_bindgen`实现了`derive`函数，该函数将 Rust 值转换成 JavaScript，反之亦然。

然而，没有被`wasm-bindgen`预定义的数据类型不能轻易传递。例如，让我们在我们的`Car`中添加一个`Box<usize>`，如下所示:

```
#[wasm_bindgen]
pub struct Car {
    pub number: usize,
    pub color:usize, // color in hex code
    pub boxed_value: Box<usize>
}
...
 pub fn new() -> Self {
        Car { number: 0, color: 0, boxed_value: Box::new(5) }
}

```

现在，如果我们看一下运行`npm run dev`的控制台，我们会看到一个错误，如下所示:

```
error[E0277]: the trait bound `Box<usize>: IntoWasmAbi` is not satisfied
 --> src/lib.rs:3:1
  |
3 | #[wasm_bindgen]
  | ^^^^^^^^^^^^^^^ the trait `IntoWasmAbi` is not implemented for `Box<usize>`
  |
  = help: the following implementations were found:
            <Box<[JsValue]> as IntoWasmAbi>
            <Box<[T]> as IntoWasmAbi>
            <Box<[f32]> as IntoWasmAbi>
            <Box<[f64]> as IntoWasmAbi>
          and 10 others
  = note: this error originates in the attribute macro `wasm_bindgen` (in Nightly builds, run with -Z macro-backtrace for more info)

error[E0277]: the trait bound `Box<usize>: FromWasmAbi` is not satisfied
 --> src/lib.rs:3:1
  |
3 | #[wasm_bindgen]
  | ^^^^^^^^^^^^^^^ the trait `FromWasmAbi` is not implemented for `Box<usize>`
  |
  = help: the following implementations were found:
            <Box<[JsValue]> as FromWasmAbi>
            <Box<[T]> as FromWasmAbi>
            <Box<[f32]> as FromWasmAbi>
            <Box<[f64]> as FromWasmAbi>
          and 10 others
  = note: this error originates in the attribute macro `wasm_bindgen` (in Nightly builds, run with -Z macro-backtrace for more info)

For more information about this error, try `rustc --explain E0277`.

waiting for changes..

```

这个错误基本上解释了当跨越 Rust-Wasm-JavaScript 边界传递值时，需要名为`FromWasmAbi`和`ToWasmAbi`的特征。由于`Box<usize>`没有实现这些特征，价值观就无法传递。

这背后的一个原因，特别是对`Box<_>`来说，是盒子是值的容器。盒子将值存储在堆上而不是堆栈上，并管理内存，直到值被丢弃，之后内存被释放。

将这些值传递给 JavaScript 需要回答一些问题，比如谁实际处理内存，JavaScript 的垃圾收集器如何与这些信息交互。除此之外，由于 JavaScript 允许完全的可变性，它可以打破`Box`需要的保证并创建一个不安全的内存状态。

出于诸如此类的原因，`wasm_bindgen`在默认情况下不会实现它。此外，由于`Box`和`From/ToWasmAbi`特征都在我们的机箱之外，我们也不能为它实现它们。

考虑另一个类似的场景。假设我们使用了另一个箱子中没有实现某些特征的东西，但是我们需要在我们的结构中有这些特征，这又需要暴露给 JavaScript。这可以通过两种方式解决。

首先，不能安全传递或不能使用`serde-json`序列化的对象可以通过句柄暴露。

其次，可以使用 `[serde-json](https://blog.logrocket.com/json-and-rust-why-serde_json-is-the-top-choice/)`进行[序列化的对象可以使用`serde-json` crate 在它们的 JavaScript 表示之间进行转换，并进行传递。](https://blog.logrocket.com/json-and-rust-why-serde_json-is-the-top-choice/)

我们将在接下来的章节中探索这些解决方案。

### 使用句柄并从 Rust 调用 JavaScript 函数

为了理解句柄的概念，记住`wasm_bindgen`只暴露那些被标记为`pub`的属性。任何私有属性很可能是不实现某些特征的类型。

让我们从`boxed_value`中移除`pub`，并在上面使用的代码中调整`color`函数:

```
#[wasm_bindgen]
pub struct Car {
    pub number: usize,
    pub color:usize, // color in hex code
    boxed_value: Box<u8>,
}

...

#[wasm_bindgen]
pub fn color(a: Car,color:usize) -> Car {
    Car {
      number: a.number,
      color,
      boxed_value:Box::new(0)
    }
}

```

我们的代码应该再次编译！如果我们使用之前的原型技巧，我们会看到`boxed_value`确实无法从 JS 访问。你可以试着登录控制台来确认你是否想亲眼看看。

这种使用 Wasm 不兼容值的方法可以被认为是句柄。本质上，我们不能直接访问内部值，但是我们可以访问包含这些值的结构。

因此，我们可以使用这些结构来处理这些值。因为这些值仍然可以从 Rust 中访问，所以我们可以从 Rust 内部的函数对它们进行操作。让我们删除对`Box`所做的所有更改，并添加另一个结构:

```
pub struct OwnerID {
    id: usize,
}

#[wasm_bindgen]
pub struct Car {
    pub number: usize,
    pub color: usize, // color in hex code
    pub owner: OwnerID,
}
...
pub fn new() -> Self {
    Self {
        add: 0,
        mul: 0,
        owner: OwnerID { id: 0 },
    }
}
...
#[wasm_bindgen]
pub fn color(a: Car,color:usize) -> Car {
    Car {
      number: a.number,
      color,
      owner: OwnerID { id: 0 }
    }
}

```

使用上面显示的代码，我们会得到与之前相同的错误:

```
error[E0277]: the trait bound `OwnerID: IntoWasmAbi` is not satisfied
 --> src/lib.rs:7:1
  |
7 | #[wasm_bindgen]
  | ^^^^^^^^^^^^^^^ the trait `IntoWasmAbi` is not implemented for `OwnerID`
  |
  = note: this error originates in the attribute macro `wasm_bindgen` (in Nightly builds, run with -Z macro-backtrace for more info)

error[E0277]: the trait bound `OwnerID: FromWasmAbi` is not satisfied
 --> src/lib.rs:7:1
  |
7 | #[wasm_bindgen]
  | ^^^^^^^^^^^^^^^ the trait `FromWasmAbi` is not implemented for `OwnerID`
  |
  = note: this error originates in the attribute macro `wasm_bindgen` (in Nightly builds, run with -Z macro-backtrace for more info)

```

为了解决这个问题，像以前一样，我们可以从`Car`的`owner`中删除`pub`，代码将再次编译。然而，现在我们没有办法从 JavaScript 访问`owner`中的值。

如前所述，`Car`现在充当`OwnerID`的句柄；我们可以通过`Car`获取`OwnerID`的值。例如，要从`OwnerID`内部获取`id`的值，我们可以在`Car`上定义一个方法，如下所示:

```
#[wasm_bindgen]
impl Car {
...
 pub fn get_id(&self) -> usize {
        self.owner.id
    }
...

```

在`App.svelte`中，运行以下程序:

```
console.log(c3.get_id());

```

现在我们可以看到`OwnerId`的值`id`。同样，我们可以定义方法来改变它的值。

此类句柄的另一个用途是当我们需要将不兼容的类型(如`Box`)传递给某个方法时。我们可以不公开该方法，而是公开另一个方法，该方法采用所需的值来构造不兼容的类型，然后从公开的方法内部调用不兼容的方法。

例如，假设我们有一个方法需要一个`OwnerID`作为参数。代替那个方法，我们可以公开另一个方法，它接受类型`usize`的`id`，从它构造`OwnerID`，然后调用带有构造的`OwnerID`作为参数的不兼容方法。

要从 Rust 中连接并调用一个 JavaScript 函数，我们需要将其声明为`extern "C"`，并用`#[wasm_bindgen]`进行注释，之后就可以调用该函数了。

值得注意的是，由于 Rust 没有函数重载或变量函数，我们需要声明我们想要在`extern`块中使用的每个参数组合。

例如，要从 Rust 调用`alert`,声明如下:

```
#[wasm_bindgen]
extern "C" {
    fn alert(s: &str);
}

#[wasm_bindgen]
pub fn greet() {
    alert("Hello in JS from rust!");
}

```

然后，我们可以从 JavaScript 调用它，就像这样:

```
...
let {Car,color,greet} = bindings; // destructure for easier access
...
greet();

```

要使用不同类型的参数调用`alert`,您需要声明另一个函数，并将其映射到 JavaScript 中的同一个函数。例如，对于以`usize`作为参数的警报，您需要如下声明它:

```
#[wasm_bindgen]
extern "C" {
    fn alert(s: &str);
    #[wasm_bindgen(js_name = alert)]
    fn alert_usize(a: usize);
}
#[wasm_bindgen]
pub fn greet() {
    alert("Hello in JS from rust!");
    alert_usize(5);
}

```

关于如何将 JavaScript 函数绑定到 Rust 函数，如何在模块内绑定函数，以及如何将 struct 的方法绑定为暴露类的构造函数的更多详细信息，[查看](https://rustwasm.github.io/wasm-bindgen/reference/attributes/on-js-imports/index.html) `[wasm-bindgen](https://rustwasm.github.io/wasm-bindgen/reference/attributes/on-js-imports/index.html)` [参考](https://rustwasm.github.io/wasm-bindgen/reference/attributes/on-js-imports/index.html)。

### 用`serde.json`方法通过 Wasm 使用 Rust

现在让我们来看看如何使用我们之前提到的`serde.json`方法，通过一个在 Svelte 中使用 Rust 通过 Wasm 的示例应用程序。

假设您正在开发一个 web 应用程序，它从用户的文件中生成大量数字数据，然后用户可以用一种特殊的文件格式下载这些数据。您希望允许用户上传该文件，然后解析数据，这样您就不必再次生成数据，并且您已经选择通过 Wasm 来这样做。

下面例子中的数据格式保持简单，以避免分散我们学习如何将 Rust 连接到 Svelte 的主要目标的注意力。这将很容易为这个项目生成虚拟数据。

我们使用的格式有点类似于 CSV 文件，第一行的列名用逗号分隔，其余包含数据类型的“行”用分号分隔。我们不会有任何缺失值或使用其他类型的值。

我们的示例文件可能如下所示:

```
A,B,C,D;10.2,5,-6.3,-7.8;8.77,5,89,-2.56,3.33

```

这里将不解释解析这些数据的详细代码，但是您可以在本文末尾链接的存储库中查看。对我们的目的来说，重要的是:

*   该功能在名为`parser`的模块中定义
*   这个函数的名字是`parse`，它接受一个`&str`作为输入
*   它返回一个`HashMap<String,Vec<f64>>`，其中字符串键将是第一行中定义的列名

让我们创建一个名为`parser.rs`的新文件，并在其中编写我们的函数:

```
use std::collections::HashMap;
use wasm_bindgen::prelude::*;

#[wasm_bindgen]
pub fn parse(input: &str) -> HashMap<String, Vec<f64>> {
...
}

```

默认情况下，任何公共模块都将其内容(用`#[wasm_bindgen]`注释)导出到 JavaScript。因此，让我们在我们的`lib.rs`文件中添加以下内容:

```
pub mod parser;

```

如果我们现在尝试运行我们的代码，我们会得到以下错误:

```
the trait IntoWasmAbi is not implemented for HashMap<String, Vec<f64>>

```

我们可以尝试创建一个句柄，然后通过该句柄访问数据，但这会很繁琐。因为我们知道 JavaScript 中的`Map`、`Vec`和`String`对象存在兼容的数据结构，我们将使用前面提到的`serde-json`方法。

如下所示更改`Cargo.toml`文件:

```
serde = { version = "1.0.137", features = ["derive"] }
wasm-bindgen = { version= "0.2.63", features = ["serde-serialize"] }

```

接下来，按如下方式更改函数:

```
pub fn parse(input: &str) -> JsValue {
  let mut ret: HashMap<String, Vec<f32>> = HashMap::new();
  ...
  JsValue::from_serde(&ret).unwrap()
}

```

现在它可以编译了，我们又回到了正轨。

我们可以从 JavaScript 通过如下所示的`App.svelte`访问`parse`函数:

```
let {Car,color,greet,parse} = bindings; // destructure for easier access
console.log(parse("A,b,c;5;6.3;7.8"));

```

我们可以看到它返回一个对象，键作为列名，值作为各自值的数组。

接下来，我们将它连接到前端的一个事件，这样用户就可以上传文件并解析其内容。

为此，只需使用`carbon-components-svelte`的`FileUploader`组件。我们将首先在 UI 中显示结果，这样我们就可以知道文件正在被正确地上传和访问:

```
<script>
...
import {Content,FileUploader} from 'carbon-components-svelte';

let files = []; // file handles will be added in this

let content = ""; // for us to see the contents

let reader = new FileReader();

// add listener to load event, which fires when file read is completed successfully
reader.addEventListener("load",() => {
  content = reader.result; // set content in ui, so we can see it
},false);

// this will be used as callback to file uploader
let add_handler = (e) => {
  reader.readAsText(e.detail[0]);
}
...
</script>

<Content>

<h1>
    Connecting rust to Svelte Through wasm !
</h1>

<br />

<div>
  <FileUploader
      labelTitle="Upload file"
      buttonLabel="Add file"
      labelDescription="Only txt files are accepted."
      accept={[".txt"]}
      bind:files
      status="complete"
      on:add={add_handler}
    />
</div>

<br />

<h3>
  File Contents Are :<br/>
  {content}
</h3>

</Content>

```

如果我们刷新页面并上传一个包含虚拟数据的文本文件，如下所示:

```
A,B,C,D;
1.5,1.5,5.1,5.1;
7.5,5.7,5.5,7.7;

```

我们可以看到它上传了文件，其内容可以被读取。

现在，我们可以在事件监听器中调用`parse`函数，而不是显示内容。我们将获得解析后的数据，然后应用程序可以根据需要使用这些数据。

这样，我们已经成功地将我们的苗条应用程序连接到我们的 Rust 代码，并在它们之间传递数据！

## 增加 Wasm 内存大小

如前所述，默认情况下，Wasm 代码有 1 MB 的堆栈内存供其使用。这个内存量对于许多应用程序来说已经足够了，但是有时候，我们可能需要更多的内存。在这种情况下，我们可以通过在一个`config`文件中设置一个特定的选项来增加大小。

比如我们前面提到的 8086 例子，虚拟机本身就需要 1 MB 的内存。除此之外，它还存储一些数据和一个小编译器。因此，默认的 1 MB 内存对于整个项目来说是不够的。

为了增加内存大小，我们必须在 Rust 项目目录中创建一个`.cargo`目录，在我们的例子中称为`rust`目录。接下来，在`.cargo`目录中创建一个名为`config`的文件。在这个文件中，我们可以指定分配给它的内存大小，如下所示:

```
[target.wasm32-unknown-unknown]
rustflags = [
"-C", "link-args=-z stack-size=2000000",
]

```

在上面的代码中，我们指定堆栈大小为 2 MB，以字节表示。现在，编译后的 Wasm 应用程序将被分配 2 MB 的堆栈内存，而不是默认的 1 MB。

## 结论

现在你知道了如何使用 Wasm 将一个苗条的应用程序连接到 Rust 库，以及如何从 JavaScript 调用用 Rust 编写的函数。您还可以从 Rust 调用 JavaScript 函数，并跨越 Rust-JavaScript 边界传递复杂的数据结构。

有了这些知识，你现在可以编写可用于 web 和桌面应用程序的库，或者制作一个快速的交互界面来展示库的使用。

你可以在我的 Github 库中获得这个项目的代码。感谢您的阅读！

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。