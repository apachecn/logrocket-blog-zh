# 游戏开发中 Rust 脚本语言的比较

> 原文：<https://blog.logrocket.com/comparing-rust-scripting-language-game-development/>

Rust 最近已经成为一种摇滚明星语言，因为它有很多好处，所以越来越受欢迎；比如在性能和编译成 WebAssembly 的能力方面非常接近 C/C++。

游戏行业的当前状态主要集中在用于开发和游戏引擎的编程语言上，例如 C/C++、Java 和 C#。

直到 Rust 的出现，它带来了一种不同的方法，即创建合适的编程语言，在高性能、与其他语言接口等方面对游戏足够灵活。

## 要知道的术语

*   脚本语言:脚本语言是一种用于运行时系统的语言，主要用于自动化任务的执行——换句话说，脚本语言具有快速的执行，因为它们在运行时被解释
*   **游戏引擎:**游戏引擎是用大型支持库为游戏开发而开发的软件框架；它解决了从零开始构建游戏组件的耗时问题
*   **WebAssembly:** WebAssembly(缩写为 Wasm)是一个将使用 Rust 和 Go 等语言构建的软件与可移植二进制代码格式的输出连接到不同环境的概念，例如通过 JavaScript 的浏览器
*   Lua: Lua 是一种用于游戏开发的脚本语言，具有快速执行、学习曲线短、易于嵌入现有系统的能力
*   **Lisp:** (面向游戏的汇编 Lisp (GOAL)):这是 Lisp 的一种方言，也用于游戏开发。此外，Lisp 作为一种语言可以嵌入到其他语言中以提高它们的性能

我将带您了解为 Rust 游戏开发的八种脚本语言，它们基于以下内容进行分类:

*   基于 GitHub stars 的社区增长
*   基于发布的稳定性
*   主要特点

GameLisp 是一种脚本语言，它有一个独特的垃圾收集器，设计为每帧运行一次，不会导致延迟高峰。

它的工作方式是加载 Rust 代码中扩展名为`.glsp`的 glsp 文件。GameLisp 用于构建 2D 游戏，因为它也具有现代语言中的通用特性。

功能包括以下内容:

### **无垃圾收集暂停**

开发 GameLisp 是为了处理垃圾收集；通过这种方式，它能够收集或获得已经分配给对象的内存。

### **缓和******Rust API 集成****

 **GameLisp 是一个 Rust crate，可以通过加载 GameLisp 文件在 Rust 程序中安装和实例化——这个过程使得 GameLisp 很容易集成到 Rust 代码中。

### **内存安全**

GameLisp 不会实现不安全的代码，因为 GameLisp 的核心逻辑完全是用 Rust 编写的，几乎没有依赖关系。

### **简单的实体脚本**

GameLisp 的另一个好处是围绕 mixins 和状态机构建的对象系统，专门用于游戏开发实体。

*   社区:小型
*   稳定:没有

GameLisp 示例:

```
# GameLisp
(let fibs (arr 0 1))

(while (< (len fibs) 31)
  (push! fibs (+ \[fibs -1\] [fibs -2])))

(prn [fibs -1]) ; 

```

```
// Rust
use glsp::prelude::*;

fn main() {
    let app = Runtime::new();
    app.run(|| {
        glsp::load("external.glsp")?;
        Ok(())
    });
}

```

王座是游戏原型和故事逻辑的脚本语言；换句话说，王座是用来创建游戏的语法规则，可以嵌入到其他引擎中。

它通过 WebAssembly 与 JavaScript 接口。王座可以用来讲故事和原型的游戏概念。它的文件扩展名为`.throne`。

其特征包括:

### **原型制作**

王座原型使用各种上下文，包括语义、语法等。，表达和评估游戏开发分析的精度。

### **故事逻辑**

讲故事的逻辑是基于叙述，角色的行为，事件等。用户对特定游戏的观点。

*   社区:小型
*   稳定性:否

王座示例:

```
// Throne file
// initialize state
fall-time 5 . default-fall-time 5 . block-id 0 . gravity-shape-id 0 . max-width 10 . max-height 20

#update . !shape _X _Y _BLOCKS . gravity-shape-id ID . + ID 1 ID' = gravity-shape-id ID' . #new-shape

#new-shape . $max-height H = new-shape 4 H ((block -1 0) ((block 0 0) ((block 1 0) ((block 0 1) (cons))))) . #shape-to-blocks (#input-lr)

```

```
// Rust
#[cfg(not(target_arch = "wasm32"))]
fn main() {
  let mut context = throne::ContextBuilder::new()
  .text(include_str!("blocks.throne"))
  .build()
  .unwrap_or_else(|e| panic!("{}", e));
}

```

Wgpu 是一个跨平台的 Rust graphics WebGPU 标准 API，可以在 Vulkan、Metal、D3D12、D3D11 和 OpenGLES 上运行，同样也可以通过 WebAssembly (wasm)在 WebGPU 上运行。

它旨在允许 web 代码以安全可靠的方式访问 GPU 功能，并通过 wgpu-native 对语言提供绑定支持。

这些功能包括:

### **内部 WebGPU 实现**

Wgpu 附带 WebGL 作为其核心标准 API，该 API 也用于在没有外部插件的情况下在 web 浏览器上渲染 2D 和 3D 等图形。

### **内部 GPU API 抽象层**

Wgpu 的性能完全依赖于 gpu(图形处理单元)抽象层，该抽象层旨在增强图形的渲染过程。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

*   社区:崛起
*   稳定性:是

Wgpu 示例:

```
// wgsl file
@vertex
fn main_vs(
  @location(0) particle_pos: vec2<f32>,
) -> @builtin(position) vec4<f32> {
  let angle = -atan2(particle_vel.x, particle_vel.y);
  let pos = vec2<f32>(
  position.x * cos(angle) - position.y * sin(angle),
  position.x * sin(angle) + position.y * cos(angle)
);
return vec4<f32>(pos + particle_pos, 0.0, 1.0);
}
@fragment
fn main_fs() -> @location(0) vec4<f32> {
  return vec4<f32>(1.0, 1.0, 1.0, 1.0);
}

fn main() {
  let draw = device.create_shader_module(&wgpu::ShaderModuleDescriptor {
            label: None,
            source: wgpu::ShaderSource::Wgsl(Cow::Borrowed(include_str!("draw.wgsl"))),
        });
}

```

Mlua 是 lua 语言的高级绑定，提供安全灵活的 API。它具有以下特点:

### **多 Lua 版本支持**

这允许在 Rust 中访问其他 Lua 版本的 API 模块，以及在独立环境中访问 Lua。

### **异步/等待支持**

它启用了异步/等待支持，其中可以使用像 [Tokio](https://github.com/tokio-rs/tokio) 和 [asyn-std](https://github.com/async-rs/async-std) 这样的执行器。

### **序列化**

支持序列化和反序列化，支持带有 [Serde](https://github.com/serde-rs/serde) 的`mlua`类型。

### **独立模式**

独立模式是 Lua 的代码直接在 Rust 程序中声明，不需要外部导入。

*   社区:小型
*   生产就绪:否

司法协助的例子:

```
fn main() {
    let mut lua = hlua::Lua::new();

    lua.execute_from_reader::<()>(File::open(&Path::new("script.lua")).unwrap())

    lua.set("add", hlua::function2(add));
    lua.execute::<()>("local c = add(2, 4)");   // calls the `add` function above
    let c: i32 = lua.get("c").unwrap(); 
    //standalone mode
    lua.set("a", 12);
    let val: i32 = lua.execute(r#"return a * 5;"#).unwrap();

}

```

Dyon 是一种动态类型语言，为游戏引擎和交互式应用程序设计，扩展名为. dyon。

它被设计为在有限的内存模式下工作，因为它缺少垃圾收集器。它具有以下特点:

### **内置支持 4D 矢量；HTML 十六进制颜色**

这使得可以在 Rust 中使用的类型更加灵活。

### **类 Go 协程**

这是一个多线程的概念，但是具有类似 Go 的风格，即在函数名前指定`go`。

### **宏**

它还提供了一组宏，使得用 Rust 嵌入代码更加容易。

*   社区:崛起
*   稳定性:是

Dyon 的例子:

```
fn main() {
    shape := unwrap(load("src/shape.dyon"))
    game := unwrap(load(source: "src/game.dyon", imports: [spape]))
    call(game, "main", [])
}

```

Ketos 是一种脚本语言，用于提供访问 Lisp APIs 的接口。它被编译成字节码，由 Rust 代码解释。它的扩展名是`.ket`。具有以下特点:

### **将表达式编译成字节码对象**

Ketos 支持编译。将文件转换成字节码，这样可以提高运行时的性能。

### **提供了一个帮助宏**

它为从 Ketos 值中提取参数和从 Rust 中进行值转换提供了宏支持。

### **实现编码和解码**

它还通过将数据分解成操作码来支持字节码指令的修改。

*   社区:小型
*   稳定性:否

酮的例子:

```
//ket
(define (range a :optional b (step 1))
  (cond
    ((null b)   (range 0 a))
    ((> step 0) (range-pos a b step ()))
    ((< step 0) (range-neg a b step ()))
    (else       (panic "`range` got 0 step"))))

```

```
// Rust
fn main() {
    let interp = Interpreter::new();
    interp.run_code(r#"
        (define (factorial n)
          (cond
            ((< n 0) (panic "factorial got negative integer"))
            ((<= n 1) 1)
            (else (* n (factorial (- n 1))))))
        "#, None).unwrap();
}

```

Mun 是 Lua JIT 的创新，旨在提高生产率和速度。它是为了在提前编译(AOT)期间轻松检测错误而构建的，而不是用 JIT(及时)来解释或编译。

它具有以下特性的`.munlib`扩展名:

### **提前编译**

它通过在这个过程中检测代码中的错误来提前编译(AOT)；有了它，开发人员可以很容易地在他们的 ide(集成开发环境)上调试运行时错误。

### **静态类型化**

它在编译时而不是运行时解决类型检查，这导致在没有正确指定时的即时纠正反馈。

### **性能**

提前(AOT)使用静态类型和 LLVM(低级虚拟机)进行优化，强制将 Mun 编译为机器代码并跨平台执行，以获得最佳运行时性能。

### **IDE 集成**

它支持代码完成和重构工具的 IDE 集成，这使得开发人员的生活更加轻松。

### **交叉编译**

Mun 支持从任何支持的编译器编译到所有目标平台。

*   社区:崛起
*   稳定性:否

Mun 示例:

```
// munlib
extern fn random() -> i64;

pub fn random_bool() -> bool {
    random() % 2 == 0
}

```

```
// Rust
fn main() {
    let app = RuntimeBuilder::new("main.munlib")
        .spawn()
        .expect("Failed to spawn Runtime");

    let app_ref = app.borrow();
    let result: bool = invoke_fn!(app_ref, "random_bool").unwrap();
    println!("random bool: {}", result);
}

```

开发 LuaJIT 是为了通过轻松访问直接对应于底层 Lua C 本机代码 API 的函数来连接 Rust 中的 Lua 代码。它具有以下特点:

### **跨平台**

它通过本机绑定支持其他语言，因为本机 API 是用 c 编写的。

### **宏**

LuaJIT 还提供了对宏的支持，以方便与 c 直接通信/接口。

*   社区:小型
*   稳定性:是

LuaJIT 的例子:

```
pub fn main() {
    let mut state = State::new();
    state.open_libs();
    state.do_string(r#"print("Hello world!")"#);

    state.push(lua_fn!(return_42));
    state.set_global("return_42");
    state.do_string(r#"print(return_42())"#);
}

```

## 结论

我们已经能够在 Rust 中看到几种用于游戏的脚本语言，其中大多数仍处于初级阶段。

这个列表中最重要和最受欢迎的是 [Wgpu](https://github.com/gfx-rs/wgpu) ，因为它是 Firefox、Servo 和 Deno 中 WebGPU 集成的核心。

在其他地方， [Dyon](https://www.piston.rs/dyon-tutorial) 作为一种动态语言，支持 4D 向量数据类型，以增强 2D 和 3D 编程，数学迭代和 Unicode 符号，以提高可读性等等。其他的是为与其他平台接口而构建的，用于性能优化等等。

你在游戏项目中使用什么 Rust 脚本语言，为什么使用它？我很想在评论区知道。谢谢。☺️

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。**