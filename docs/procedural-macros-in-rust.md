# Rust 中的过程宏

> 原文：<https://blog.logrocket.com/procedural-macros-in-rust/>

如果您使用 Rust 已经有一段时间了，那么您可能会遇到过程宏，或者简称为 proc 宏，不管您是否意识到了这一点。最流行的 proc 宏很可能是 [Serde 的](https://serde.rs/)为 [`serialize`和`deserialize`](https://blog.logrocket.com/rust-serialization-whats-ready-for-production-today/) 派生的宏；如果你曾经使用过[Rocket web framework](https://rocket.rs/)、 [wasm-bindgen 项目](https://github.com/rustwasm/wasm-bindgen)或者任何数量的其他库，你可能也会遇到它们。

在本教程中，我们将告诉你你需要知道的关于 Rust 中程序宏的一切。我们将讨论以下内容:

## Rust 中的程序宏是什么？

简而言之，[过程宏](https://blog.rust-lang.org/2018/12/21/Procedural-Macros-in-Rust-2018.html)允许你获取一段 Rust 代码，分析它，并从中生成更多 Rust 代码。这是元编程的一种形式，一种使用 Rust 编写 Rust 代码的方式。

在《星球大战 II:克隆人的进攻》中，机器人 C-3PO 跌跌撞撞地走过一家机器人制造厂，说道:“机器制造机器？多么乖张。”

就我个人而言，我会说“棒极了”，但这可能是一个品味问题。

Anyway, proc macros allow you to generate Rust code at compile time. It’s a fairly advanced topic, but a very, very powerful feature of the Rust language.

### 声明宏和过程宏

Rust 有两种不同的宏:声明性宏(用`macro_rules!`声明)和过程性宏。这两种类型的宏都允许您编写更多代码，并在编译时进行评估，但它们的工作方式非常不同。

[声明性宏](https://doc.rust-lang.org/book/ch15-04-rc.html)(通俗地说——有些令人困惑——简称为“宏”)使您能够编写类似于`match`表达式的东西，对您作为参数提供的 Rust 代码进行操作。它使用您提供的代码来生成替换宏调用的代码。从某种意义上说，[声明性宏只是复杂的文本替换](https://blog.thomasheartman.com/posts/save-on-typing-and-improve-legibility-with-rusts-macros)。

[https://www.youtube.com/embed/q6paRBbLgNw](https://www.youtube.com/embed/q6paRBbLgNw)

视频

另一方面，过程宏允许您对给出的 Rust 代码的抽象语法树(AST)进行操作。就函数签名而言，proc 宏是从一个`TokenStream`(或两个)到另一个`TokenStream`的函数，其中输出代替了宏调用。这允许您在编译时检查 Rust 代码，并基于程序的 AST 促进复杂的代码生成。

简而言之，声明性宏允许您匹配代码模式，并基于该模式生成代码。过程性宏允许您在生成输出之前检查和操作提供给您的代码，这给了您更多的能力。

### 派生、类函数和属性宏

有三种类型的[程序宏](https://doc.rust-lang.org/reference/procedural-macros.html):派生宏、类功能宏和属性宏。它们都在`TokenStreams`上操作，但操作方式略有不同。

*   派生宏作用于结构、枚举和联合，并使用`#[derive(MyMacro)]`声明进行注释。它们还可以声明助手属性，这些属性可以附加到项的成员(如枚举变量或结构字段)
*   类似函数的宏类似于声明性宏，它们是用宏调用操作符`!` 调用的，看起来像函数调用。它们对你放在括号里的代码进行操作
*   属性宏定义新的外部属性，，可以附加到项目上。它们类似于派生宏，但是可以附加到更多的项目上，比如特征定义和函数

## 在 Rust 中创建衍生宏

让我们把手弄脏一点。现在我们已经对什么是 proc 宏有了一个大致的概念，让我们创建一个简单的来感受一下。我们将创建一个 derive 宏，它打印有关它所附加的结构、枚举或联合的信息，包括它是哪种类型的项以及它有哪些成员或变量(如果有的话)。

创建程序宏板条箱时，你需要做的第一件事是让 Cargo 在你的`Cargo.toml`文件中知道这一点。您可以通过添加如下所示的条目来实现这一点:

```
[lib]
proc-macro = true

```

在我们的例子中，为了测试这个宏，我们将使用 Cargo 的 workspaces 特性，并创建一个主可执行文件(`main.rs`)和一个保存 proc 宏的库(`derive-macro/src/lib.rs`)。整个目录结构如下所示:

```
.
├── Cargo.toml
├── derive-macro
│   ├── Cargo.toml
│   └── src
│       └── lib.rs
└── main.rs
>
```

顶层的`Cargo.toml`文件包含关于我们的二进制文件及其依赖项的信息:

```
[package]
name = "proc_macros"
version = "0.1.0"
edition = "2018"
publish = false

[workspace]

[[bin]]
name = "proc-macros"
path = "main.rs"

[dependencies]
derive-macro = { path = "derive-macro" }

```

`derive-macro/Cargo.toml`包含 proc 宏项目的依赖项以及前面提到的`proc-macro = true`注释。

```
[package]
name = "derive-macro"
version = "0.1.0"
edition = "2018"

[lib]
proc-macro = true

[dependencies]
syn = { version = "1.0", features = ["full"] }
quote = "1.0"
proc-macro2 = "1.0"

```

## 在 Rust 中编写程序宏

对编写 proc 宏的全面介绍超出了本文的范围。相反，请参考本教程末尾提到的[资源](#furtherreading)。

不过，我要做的是用 [`syn`](https://docs.rs/syn/1.0.53/syn/) 、 [`quote`](https://docs.rs/quote/1.0.7/quote/) 和 [`proc_macro2`](https://docs.rs/proc-macro2/0.4.30/proc_macro2/) 板条箱给你看一个简单的例子。当使用 proc 宏时，这些是你的面包和黄油。

如前所述，我们希望我们的宏描述它所附加的项目。让我们看看这是如何在`derive-macro/src/lib.rs`中处理的:

```
use proc_macro::{self, TokenStream};
use quote::quote;
use syn::{parse_macro_input, DataEnum, DataUnion, DeriveInput, FieldsNamed, FieldsUnnamed};

#[proc_macro_derive(Describe)]
pub fn describe(input: TokenStream) -> TokenStream {
    let DeriveInput { ident, data, .. } = parse_macro_input!(input);

    let description = match data {
    syn::Data::Struct(s) => match s.fields {
        syn::Fields::Named(FieldsNamed { named, .. }) => {
        let idents = named.iter().map(|f| &f.ident);
        format!(
            "a struct with these named fields: {}",
            quote! {#(#idents), *}
        )
        }
        syn::Fields::Unnamed(FieldsUnnamed { unnamed, .. }) => {
        let num_fields = unnamed.iter().count();
        format!("a struct with {} unnamed fields", num_fields)
        }
        syn::Fields::Unit => format!("a unit struct"),
    },
    syn::Data::Enum(DataEnum { variants, .. }) => {
        let vs = variants.iter().map(|v| &v.ident);
        format!("an enum with these variants: {}", quote! {#(#vs),*})
    }
    syn::Data::Union(DataUnion {
        fields: FieldsNamed { named, .. },
        ..
    }) => {
        let idents = named.iter().map(|f| &f.ident);
        format!("a union with these named fields: {}", quote! {#(#idents),*})
    }
    };

    let output = quote! {
    impl #ident {
        fn describe() {
        println!("{} is {}.", stringify!(#ident), #description);
        }
    }
    };

    output.into()
}

```

第一步是把我们收到的`TokenStream`作为一个参数。我们使用`syn`的 [`parse_macro_input`](https://docs.rs/syn/1.0.52/syn/macro.parse_macro_input.html) 宏来解析它。这使我们能够访问`ident`(该项目的标识符、名称)和关于该项包含什么的数据。我们对这些数据进行匹配，以确定该项是结构、枚举还是联合。如果它是一个结构，我们做进一步的匹配来检查它是否有字段以及它们是否被命名。根据项目的种类，我们检查它的字段或变量，并创建一个描述它的字符串。

虽然`syn`箱给了我们解析传入的`TokenStream`并对其进行处理的能力，但是`quote`箱让我们生成新的 Rust 代码。 [`quote`宏](https://docs.rs/quote/1.0.7/quote/macro.quote.html)可以让我们写出几乎规律的 Rust 代码，可以变成实际的 Rust 代码。奇怪的语法(如`quote! {#(#idents), *}`)是我们如何做变量插值和重复。

对于这个宏，让我们把它放在`main.rs`文件中:

```
use derive_macro::Describe;

#[derive(Describe)]
struct MyStruct {
    my_string: String,
    my_enum: MyEnum,
    my_number: f64,
}

#[derive(Describe)]
struct MyTupleStruct(u32, String, i8);

#[derive(Describe)]
enum MyEnum {
    VariantA,
    VariantB,
}

#[derive(Describe)]
union MyUnion {
    unsigned: u32,
    signed: i32,
}

fn main() {
    MyStruct::describe();
    MyTupleStruct::describe();
    MyEnum::describe();
    MyUnion::describe();
}

```

运行该程序将产生如下所示的输出。

```
MyStruct is a struct with these named fields: my_string, my_enum, my_number.
MyTupleStruct is a struct with 3 unnamed fields.
MyEnum is an enum with these variants: VariantA, VariantB.
MyUnion is a union with these named fields: unsigned, signed.

```

## 延伸阅读

过程宏是 Rust 语言的一个非常强大的特性，并且具有不可思议的潜力。但是它们也非常复杂，需要一些时间和深思熟虑的努力才能真正理解和适应。

本指南高度概括了它们是什么以及如何开始使用它们。如果你想往兔子洞更深处爬，我推荐以下资源:

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。