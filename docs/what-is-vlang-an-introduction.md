# Vlang 是什么？介绍

> 原文：<https://blog.logrocket.com/what-is-vlang-an-introduction/>

## 那么…V 是什么？

如果您最终看到了这篇文章，可能是因为您不知道 Vlang 是什么，或者想了解更多。无论哪种方式，都很简单:Vlang 是一种编程语言。这样就清楚了吗？(开个玩笑。)

更具体地说，Vlang 是一种静态类型的编程语言，由[Alexander medvedenikov](https://github.com/medvednikov)创建，于 2019 年 7 月开源。根据创建者的说法，这个名字被缩短为 V 是为了避免弄乱 Git 的历史。它类似于围棋的[，灵感来自于 Rust](https://blog.logrocket.com/when-to-use-rust-and-when-to-use-golang/) 、Swift 和 Oberon。

在文档中，V 吹嘘了类似 C 的编译时间(主要后端代码编译成 C)、更少的内存分配、简单的学习曲线、内置的序列化(将对象转换成字节流)以及不依赖于编译。v 的安全性也是一流的，没有空值，没有全局变量，没有未定义，并且支持边界检查。默认情况下，它还支持纯函数、不可变变量和不可变结构。

有了这些基础知识，我们将在文章的剩余部分更多地讨论这些声明以及 V 未来的发展方向。

## 开始使用 V

要跟上，您必须已经安装了`git` *、*、`gcc`等 C 编译器和`make`；如果没有，请在我们继续之前设置好。

从 Vlang 开始，最好直接从源码(Github)安装。

如果在 MacOS，Linux，FreeBSD 等上。，运行:

```
git clone https://github.com/vlang/v && cd v && make

```

在 Windows 上:

```
git clone https://github.com/vlang/v
cd v
make

```

这将在您的机器上自我编译和构建 V。

接下来，为了避免重复输入完整的执行路径，将 V 添加到您的路径中，如下所示。

在 MacOS 上:

```
sudo ./v symlink

```

在 Windows 上:

```
.\v.exe symlink

```

### 编写我们的第一个 Vlang 程序

对于我们的第一个程序，我们将使用熟悉的`hello world`:

```
// hello_world.v
fn main() {
  println('Hello World!')
}

```

将该文件保存为`hello_world.v`并运行`v run hello_world.v`以获得您的输出。

请记住，V 是静态类型的，所以类型在参数名之后，就像这样:

```
fn add(x int, y int) int {
  return x + y
}

```

### 在 V 中使用函数

像 C++一样，V 不允许函数重载，这意味着两个函数不能有相同的名字和不同的参数。限制函数重载是为了让代码更容易维护。

在 V 中，默认情况下函数也是私有的，所以其他模块不能使用它们，除非我们在它们前面加上`pub`,就像这样:

```
// a public function
pub fn add() {
}

// a private function 
fn sum() {
}

```

### 在 V 中使用变量

V 中的变量被赋予了`:=`，如下面的代码片段所示:

```
name := 'Victor'
age := 23
large_number := i64(9999999999)
println(name)
println(age)
println(large_number)

```

它们也是不可改变的；为了能够重新声明/重新分配一个变量，你必须在它前面加上`mut`,就像这样:

```
mut age := 23
println(age)
age = 24
println(age)

```

### 在 V 中使用类型

众所周知，V 是静态类型的。以下是其原始类型:

```
bool

string

i8    i16  int  i64      i128 (soon)
byte  u16  u32  u64      u128 (soon)

rune // represents a Unicode code point

f32 f64

any_int, any_float // internal intermediate types of number literals

byteptr, voidptr, charptr, size_t // these are mostly used for C interoperability

any // similar to C's void* and Go's interface{}

```

其他数据类型包括:

v 也支持字符串插值:

```
name := 'Victor'
println('Hello, $name!') // Hello, Victor!

```

### HTTP 支持和 JSON 解码

Vlang 内置了对数据获取和 JSON 解码的 HTTP 支持。要使用它们，您需要导入它们各自的模块:

```
import http
import json

fn fetchUsers() {
  users := http.get('http://jsonplaceholder.typicode.com/users') or {
    println('failed to fetch users from server')
    return
  }
}

```

如果您计划像上面的例子一样使用 JSON API，那么您需要解码来自 URL 的响应(将数据从字符串更改为对象)，然后才能处理它。

为此，首先需要为您的响应类型定义一个结构，如下例所示:

```
struct User {
  name string
  email int
}

import http
import json

fn fetchUsers() []User {
  users := http.get('http://jsonplaceholder.typicode.com/users') 
  users := json.decode([]User, users) or {
    return []User{}
  }
}

```

### 包管理器

v 有一个名为 vpm 的内置包管理器。要使用 vpm，请运行:

```
v install [package]

```

### V 中的内存管理

与 Rust 类似，Vlang 使用值类型和字符串缓冲区进行内存管理，但不进行垃圾收集。下面是来自 V 的官方演示:

 [https://www.youtube.com/embed/gmB8ea8uLsM?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent](https://www.youtube.com/embed/gmB8ea8uLsM?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent)

视频

也可以禁用 Autofree，以便执行手动内存释放`-noautofree`。

## 为什么要用 Vlang？

v 拥有比许多其他框架([如 Rust](https://blog.logrocket.com/v-lang-vs-rust-comparing-build-languages/) )更简单、更易读的语法，使其简洁易用。它的主要哲学是用很少的规则以一种方式做每一件事。

在我看来，V 是 Go 的一个更干净的版本，拥有像热重载、选项/结果和强制错误检查这样的酷功能，没有 null、没有全局变量和没有未定义的行为。v 还有一个优点，它可以用于大多数领域，比如 web 开发、游戏开发、系统编程、GUI、嵌入式、工具、移动(WIP)等等。

虽然 V 要超越其他一些框架还有很多工作要做，但现在是了解这种语言并为其发展做出贡献的大好时机。

## 结论

有这么多其他编程语言，重要的是要记住 V 仍处于 alpha 阶段，这意味着大多数部分仍在开发中。尽管如此，由于它的“一种做事方式”的方法，它似乎是一种相当好的语言。这使得代码具有可预测性和可维护性。

V 文档也增加了它的简单性，并且很好地帮助用户理解每样东西是什么以及它是如何工作的。

有关 Vlang 的更多信息，请查看下面的一些参考资料。同时，我希望这篇文章已经回答了你关于 V 语言及其在现代编程中的作用的一些问题。

## 关于 Vlang 的更多信息