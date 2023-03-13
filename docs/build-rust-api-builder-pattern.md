# 如何用构建器模式构建 Rust API

> 原文：<https://blog.logrocket.com/build-rust-api-builder-pattern/>

让我们面对它；凭借其可选的命名参数，Python 比许多其他编程语言都有优势，比如 Rust。然而，Rust 库的作者可以使用[构建器模式](https://rust-unofficial.github.io/patterns/patterns/creational/builder.html)非常有效地解决这个缺点。这种模式背后的想法看似简单:创建一个可以但不需要保存所有值的对象，并让它在所有必需字段都存在时创建我们的类型。

在本文中，我们将探索 Rust 的构建器模式，包括以下内容:

## 构建器模式

为了熟悉 Rust 中的构建器模式，让我们首先比较一下我们的代码在有和没有构建器的情况下会是什么样子:

```
// without a builder
let base_time = DateTime::now();
let flux_capacitor = FluxCapacitor::new();
let mut option_load = None;
let mut mr_fusion = None;

if get_energy_level(&plutonium) > 1.21e9 {
    option_load = Some(plutonium.rods());
} else {
    // need an energy source, can fail
    mr_fusion = obtain_mr_fusion(base_time).ok();
}

TimeMachine::new(
    flux_capacitor,
    base_time,
    option_load,
    mr_fusion,
)
// with a builder
let builder = TimeMachineBuilder::new(base_time);
    .flux_capacitor(FluxCapacitor::new());

if get_energy_level(&plutonium) > 1.21e9 {
    builder.plutonium_rods(plutonium.rods())
} else {
    builder.mr_fusion()
}
.build()

```

本文中的所有例子都设计得很简单。在实践中，您会将生成器用于具有更多依赖项的复杂类型。

构建器的主要功能是将构建实例所需的数据保存在一个地方。只需定义`TimeMachineBuilder` struct，放入一堆`Option<_>`字段，添加一个带有`new`和`build`方法的`impl`，以及一些 setters，就大功告成了。就这样，你现在知道了所有关于建筑商的事情。下次见！

你还在这里？啊，我怀疑你不会上当。当然，对于建筑商来说，除了明显的数据收集，还有更多。

## 拥有的与可变引用的构建器

与一些垃圾收集语言不同，在 Rust 中，我们[区分拥有的值和借用的值](https://blog.logrocket.com/understanding-primitive-data-types-rust/)。因此，有多种方式来设置构建器方法。一个通过可变引用获取`&mut self`，另一个通过值获取`self`。前者有两个子变量，要么返回`&mut self`进行链接，要么返回`()`。允许链接稍微常见一些。

我们的示例使用了链接，因此使用了按值构建器。`new`的结果直接用来调用另一个方法。

可变借用构建器的好处是能够在同一个构建器上调用多个方法，同时仍然允许一些链接。然而，这是以构建器设置需要绑定为代价的。例如，以下代码会因`&mut self`返回方法而失败:

```
let builder= ByMutRefBuilder::new()
    .with_favorite_number(42); // this drops the builder while borrowed

```

然而，做完整的链仍然有效:

```
ByMutRefBuilder::new()
    .with_favorite_number(42)
    .with_favorite_programming_language("Rust")
    .build()

```

如果我们想要重用构建器，我们需要绑定`new()`调用的结果:

```
let mut builder = ByMutRefBuilder::new();
builder.with_favorite_number(42) // this returns `&mut builder` for further chaining

```

我们也可以忽略链接，而是多次调用同一个绑定:

```
let mut builder = ByMutRefBuilder::new();
builder.with_favorite_number(42);
builder.with_favorite_programming_language("Rust");
builder.build()

```

另一方面，按值构造器需要重新绑定以不丢弃它们的状态:

```
let builder = ByValueBuilder::new();
builder.with_favorite_number(42); // this consumes the builder :-(

```

因此，它们通常被锁住:

```
ByValueBuilder::new()
    .with_favorite_number(42)
    .with_favorite_programming_language("Rust")
    .build()

```

因此，对于按值构造器，我们需要链接。另一方面，可变引用的构建器将允许链接，只要构建器本身绑定到某个局部变量。此外，可变引用的构建器可以自由重用，因为它们不被它们的方法消费。

一般来说，链接是使用构建器的预期方式，所以这不是一个大的缺点。此外，根据构建器包含的数据量，移动构建器可能会在性能配置文件中变得可见，但是这种情况很少见。

如果构建器经常在有许多分支的复杂代码中使用，或者很可能从中间状态重用，我更喜欢可变引用的构建器。否则，我会使用按值生成器。

## `Into`和`AsRef`性状

当然，构建器方法可以做一些基本的转换。最流行的一种使用`Into`特征来绑定输入。

例如，您可以将一个索引视为具有`Into<usize>`实现的任何东西，或者允许构建器通过一个`Into<Cow<'static, str>>`参数来减少分配，这使得函数同时接受一个`&'static str`和`String`。对于可以作为引用给出的参数,`AsRef`特性可以在所提供的类型中允许更多的自由。

也有像`IntoIterator`和`ToString`这样的特殊特征有时会很有用。例如，如果我们有一个值序列，我们可以有扩展每个内部`Vec`的`add`和`add_all`方法:

```
impl FriendlyBuilder {
    fn add(&mut self, value: impl Into<String>) -> &mut Self {
        self.values.push(value.into())
        self
    }

    fn add_all(
        &mut self,
        values: impl IntoIterator<Item = impl Into<String>>
    ) -> &mut Self {
        self.values.extend(values.into_iter().map(Into::into));
        self
    }
}

```

## 默认值

类型通常可以有可用的默认值。因此，构建器可以预先设置这些默认值，只有在用户请求时才替换它们。在极少数情况下，获得缺省值会很昂贵。构建器可以使用一个`Option`，它有自己的默认`None`，或者执行另一个技巧来跟踪设置了哪些字段，我们将在下一节解释。

当然，我们不依赖于`Default`实现给我们的任何东西；我们可以设置自己的默认值。例如，我们可以决定越多越好，所以默认的数字是`u32::MAX`，而不是`Default`给我们的零。

对于涉及引用计数的更复杂的类型，我们可能有一个`static`默认值。以引用计数的运行时开销为代价，它每次都得到`Arc::clone(_)`。或者，如果我们允许借用静态实例，我们可以使用`Cow<'static, T>`作为缺省值，避免分配，同时仍然保持构建简单:

```
use std::sync::Arc;

static ARCD_BEHEMOTH: Arc<Behemoth> = Arc::new(Behemoth::dummy());
static DEFAULT_NAME: &str = "Fluffy";

impl WithDefaultsBuilder {
    fn new() -> Self {
        Self {
            // we can simply use `Default`
            some_defaulting_value: Default::default(),
            // we can of course set our own defaults
            number: 42,
            // for values not needed for construction
            optional_stuff: None,
            // for `Cow`s, we can borrow a default
            name: Cow::Borrowed(DEFAULT_NAME),
            // we can clone a `static`
            goliath: Arc::clone(ARCD_BEHHEMOTH),
        }
    }
}

```

## 使用类型状态跟踪集合字段

跟踪集合字段仅适用于所拥有的变体。这个想法是使用泛型将关于哪些字段已经被设置到类型中的信息。因此，我们可以避免双重设置，事实上，我们甚至可以禁止它们，并且只允许在所有必需的字段都被设置后进行构建。

让我们以一个简单的例子来说明最喜欢的数字、编程语言和颜色，其中只有第一个是必需的。我们的类型是`TypeStateBuilder<N, P, C>`，其中`N`表示数字是否已设置，`P`表示编程语言是否已设置，`C`表示颜色是否已设置。

然后我们可以创建`Unset`和`Set`类型来填充我们的泛型。我们的`new`函数将返回`TypeStateBuilder<Unset, Unset, Unset>`，只有`TypeStateBuilder<Set, _, _>`有一个`.build()`方法。

在我们的例子中，我们到处使用默认值，因为使用不安全的代码不会有助于理解模式。但是，使用这种方案当然可以避免不必要的初始化:

```
use std::marker::PhantomData;

/// A type denoting a set field
enum Set {}

/// A type denoting an unset field
enum Unset {}

/// Our builder. In this case, I just used the bare types.
struct<N, P, C> TypeStateBuilder<N, P, C> {
    number: u32,
    programming_language: String,
    color: Color,
    typestate: PhantomData<(N, P, C)>,
}

/// The `new` function leaves all fields unset
impl TypeStateBuilder<Unset, Unset, Unset> {
    fn new() -> Self {
        Self {
            number: 0,
            programming_language: "",
            color: Color::default(),
            typestate: PhantomData,
        }
    }
}

/// We can only call `.with_favorite_number(_)` once
impl<P, C> TypeStateBuilder<Unset, P, C> {
    fn with_favorite_number(
        self,
        number: u32,
    ) -> TypeStateBuilder<Set, P, C> {
        TypeStateBuilder {
            number,
            programming_language: self.programming_language,
            color: self.color,
            typestate: PhantomData,
        }
    }
}

impl<N, C> TypeStateBuilder<N, Unset, C> {
    fn with_favorite_programming_language(
        self,
        programming_language: &'static str,
    ) -> TypeStateBuilder<N, Set, C> {
        TypeStateBuilder {
            number: self.number,
            programming_language,
            color: self.color,
            typestate: PhantomData,
        }
    }
}

impl<N, P> TypeStateBuilder<N, P, Unset> {
    fn with_color(self, color: Color) -> TypeStateBuilder<N, P, Set> {
        TypeStateBuilder {
            number: self.number,
            programming_language: self.programming_language,
            color,
            typestate: PhantomData,
        }
    }
}

/// in practice this would be specialized for all variants of
/// `Set`/`Unset` typestate
impl<P, C> TypeStateBuilder<Set, P, C> {
    fn build(self) -> Favorites {
        todo!()
    }
}

```

该界面的工作方式与按值构建器完全相同，但不同之处在于，如果为这些情况添加了`impl`，用户只能设置一次或多次字段。我们甚至可以控制以什么顺序调用什么函数。例如，我们只能在已经调用了`.with_favorite_number(_)`之后才允许`.with_favorite_programming_language(_)`，并且 typestate 编译为零。

这样做的缺点显然是复杂；有人需要编写代码，编译器必须对代码进行解析和优化。因此，除非 typestate 用于实际控制函数调用的顺序或允许优化初始化，否则它可能不是一个好的投资。

## Rust builder 模式板条箱

因为构建者遵循这样一个简单的代码模式，所以在 [crates.io](https://crates.io) 上有许多板条箱可以自动生成它们。

[derive_builder](https://crates.io/crates/derive_builder) 机箱用来自`struct`定义的`Into`参数和可选默认值构建我们的标准可变引用构建器。您还可以提供验证函数。这是自动生成构建器的最流行的 proc 宏，是一个可靠的选择。写这篇文章的时候这个箱子已经有六年的历史了，所以这是自衍生产品稳定以来的第一批衍生产品之一。

如上所述，[类型生成器](https://crates.io/crates/typed-builder)机箱处理整个按值类型状态实现，所以你可以忘记你刚刚读到的一切。只需键入`cargo add typed-builder`就可以享受代码中的类型安全生成器。它还具有默认值和可选的`into`注释，还有一个`strip_option`注释，允许您拥有一个 setter 方法，该方法总是接受任何值并设置`Some(value)`。

[安全生成器派生的](https://crates.io/crates/safe-builder-derive)箱也实现了类型状态，但是，通过为设置/未设置字段的每个组合生成一个`impl`，它导致代码呈指数增长。对于最多有三到四个字段的小型构建者来说，这可能仍然是一个可以接受的选择，否则，编译时间成本可能是不值得的。

[整理构建器](https://crates.io/crates/tidy-builder)机箱与类型构建器基本相同，但是它使用`~const bool`来表示类型状态。 [buildstructor](https://crates.io/crates/buildstructor) 机箱也受到了类型化构建器的启发，但是它使用带注释的构造函数而不是结构。 [builder-pattern](https://crates.io/crates/builder-pattern) crate 也使用类型状态模式，并允许您注释惰性默认值和验证函数。

毫无疑问，未来还会有更多。如果您想在代码中使用自动生成的构建器，我认为大多数都是不错的选择。一如既往，您的里程可能会有所不同。例如，要求对`Into`参数进行注释对某些人来说可能是不太符合人类工程学的，但对其他人来说却降低了复杂性。一些用例需要验证，而另一些用例则不需要验证。

## 结论

构建者很容易弥补 Rust 中命名和可选参数的不足，甚至超越了编译时和运行时的自动转换和验证。此外，大多数开发人员都熟悉这种模式，因此您的用户会有宾至如归的感觉。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

与往常一样，缺点是需要维护和编译额外的代码。Derive crates 可以消除维护负担，但代价是另一点点编译时间。

那么，您应该为所有类型使用生成器吗？我个人只对至少有五个部分或复杂依赖关系的类型使用它们，但是，在那些情况下，认为它们是不可或缺的。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。