# Rust 中的竞技场使用指南

> 原文：<https://blog.logrocket.com/guide-using-arenas-rust/>

Rust 编程语言的优势之一是它被设计成高性能的。事实上，在“为什么生锈？”在 [Rust 网站](https://www.rust-lang.org/)上，第一个回答就是“性能”！

Arena 分配是一种机制，可以在许多语言中使用，包括 Rust，以进一步提高内存分配和释放的性能。在本指南中，我们将介绍:

## 什么是竞技场？

竞技场分配器，也简称为竞技场，是一个可以用来分配一系列对象的对象，这些对象都有相对较短的生命周期。这种策略被称为基于区域的内存管理。

竞技场也称为区域、地带、地区或记忆环境。

## 竞技场是如何运作的？

通常，当构建竞技场时，系统会保留一块内存。然后 arena 设置一个指针指向该内存块中的下一个空闲位置，它被初始化为该空间的开始。

当请求从 arena 分配内存时，它只是将指针返回到下一个空闲位置，并将指针递增到刚刚返回的分配位置。在程序完成所有对象后，[整个底层内存块被释放](https://blog.logrocket.com/understanding-ownership-in-rust/)。

通常情况下，为了获得更好的性能，arena 中的对象不会运行析构函数，这样可以避免不必要的工作。然而，如果你的用例需要的话，一些竞技场确实提供了在一些对象上运行析构函数的方法。请参见下面的具体用例示例，了解更多信息。

arena 的分配和释放都比单独从系统中分配和释放多个小对象要快得多。缓存还有一个好处，因为同时使用的对象在内存中靠得很近。

唯一的限制是在同一个舞台上单独释放对象并不容易。因此，如果需要在相同或相似的时间段内分配和使用多个对象，那么使用 arena 是最有意义的，在此之后就不再需要它们了。

## 竞技场的用例

在很多情况下，使用 arena 可以提高应用程序的性能。让我们回顾三个常见的例子:游戏、编译器和 web 服务器。

### 比赛

对于一次渲染一帧的游戏，每一帧都可以有一个与之关联的竞技场。使用这种策略，渲染该帧所需的所有对象都可以从竞技场分配。

当帧渲染完成时，不再需要这些对象，所以整个竞技场可以被释放。

### 编译程序

编译器的不同阶段通常是独立的，所以每个阶段的中间对象可以从一个 arena 中分配。当每个阶段完成时，整个相关的竞技场可以被解除分配。

### 网络服务器

Web 请求有一个明确定义的生存期。如果对 web 请求的响应需要大量的分配来计算响应，那么可以在 arena 中进行。一旦请求完成，arena 就可以被释放，这有助于提高性能。

## 铁锈中竞技场的特殊考虑

众所周知，在 Rust 中，包含指向其他元素的元素的数据结构很难实现和使用。说真的——有一整本书都是关于在 Rust 中实现各种链表的！

这是因为 [Rust borrow checker](https://blog.logrocket.com/introducing-the-rust-borrow-checker/) ，这是这种语言的一个基本组件，可以帮助你管理所有权。

在 Rust 中使用竞技场可以更容易地让元素以循环的方式指向彼此，因为在竞技场中，每个元素的生命周期都与竞技场的生命周期相同。

## 如何在 Rust 中实现竞技场

如果你在 [crates.io](https://crates.io/keywords/arena) 上查看，你可以找到很多在 Rust 中实现竞技场的好选择。下面就来看看几款最受欢迎的吧！

### 竞技场分配器

是 crates.io 上下载量最大的竞技场分配器。它非常容易使用。这里有一个例子:

```
struct SomeData {
    name: String,
    value: u32
}

fn bumpalo_example() {
    // Note that arena needs to be declared mutable
    // so we can call reset() on it below.
    let mut arena = bumpalo::Bump::new();
    for  _ in 0..10 {
        let data1: &mut SomeData = arena.alloc(SomeData {
            name: "some data".to_string(),
            value: 1
        });
        let data2: &mut String = arena.alloc(String::from("some more data"));
        // use data1 and data2 here, now we're all done
        // We can call arena.reset() to free all the memory and start fresh
        // This also invalidates data1 and data2, so the compiler won't let us
        // use them anymore.
        arena.reset();
    }
}

```

`bumpalo`支持分配任何种类的对象。默认情况下，它不会对分配的对象运行析构函数(即`Drop`实现)。如果你想让一个对象运行它的`Drop`实现，你可以把它包装在一个`bumpalo::boxed::Box<T>`中。

请注意，这样做可以防止该对象处于循环中，所以如果您需要在循环数据结构上运行`Drop`实现，比如带有节点的图，这将不起作用。

### 竞技场分配器

另一个流行的竞技场板条箱是`[typed-arena](https://crates.io/crates/typed-arena)`。这里有一个如何使用它的例子:

```
struct SomeData {
    name: String,
    value: u32
}

fn typedarena_example()
{
    for _ in 0..10 {
        let arena: typed_arena::Arena<SomeData> = typed_arena::Arena::new();
        let data1: &mut SomeData = arena.alloc(SomeData {
            name: "some data".to_string(),
            value: 1
        });
        let data2: &mut SomeData = arena.alloc(SomeData {
            name: "some more data".to_string(),
            value: 2
        });
        // use data1 and data2 here, now we're all done
        // There is no explicit way to reset the arena, but when it goes
        // out of scope all of its memory will be freed.
    }
}

```

顾名思义，每个`typed_arena::Arena`只支持分配一种类型的对象。这比`bumpalo`更受限制，但使实现更简单。它还允许通过`[into_vec()](https://docs.rs/typed-arena/2.0.1/typed_arena/struct.Arena.html#method.into_vec)`方法轻松访问整个竞技场的内容。

注意，`typed-arena`分配器确实为分配的对象运行`Drop`实现。

### 竞技场分配器

与`bumpalo`或`typed-arena`相比，`[id_arena](https://crates.io/crates/id-arena)`的界面略有不同。这里有一个例子:

```
struct SomeData {
    name: String,
    value: u32
}

fn idarena_example()
{
    for _ in 0..10 {
        let mut arena: id_arena::Arena<SomeData> = id_arena::Arena::new();
        let data1_index: id_arena::Id<SomeData> = arena.alloc(SomeData {
            name: "some data".to_string(),
            value: 1
        });
        let data2_index: id_arena::Id<SomeData> = arena.alloc(SomeData {
            name: "some more data".to_string(),
            value: 2
        });
        // This will panic if data1_index has no associated data.
        let data1: &SomeData = &arena[data1_index];
        // get_mut() returns an Option, which will be None if data2_index
        // has no associated data.
        let data2: &mut SomeData = arena.get_mut(data2_index).unwrap();
        // use data1 and data2 here, now we're all done
        // There is no explicit way to reset the arena, but when it goes
        // out of scope all of its memory will be freed.
    }
}

```

注意，对`arena.alloc()`的调用返回一个`id_arena::Id`，而不是对分配数据的引用。

这有点烦人，因为您必须调用另一个方法来实际获取数据。但是如果你正在处理循环数据结构，这是非常方便的，因为你可以让对象持有它们所引用的其他对象的`Id`。

和`typed-arena`一样，`id_arena`中每个竞技场只能分配一种类型的物体。这个 arena 分配器为分配的对象运行`Drop`实现。

另一个注意:`id_arena::Arena::new()`分配一个空的`Vec`作为它的后备存储器。这种后备存储将根据需要扩展。然而，这样创建竞技场会更有效:

```
id_arena::Arena::with_capacity()

```

此方法允许您传入对您的应用程序来说合理的初始容量。

### 竞技场分配器

从表面上看，`[generational-arena](https://crates.io/crates/generational-arena)`板条箱与`id_arena`非常相似:

```
struct SomeData {
    name: String,
    value: u32
}

fn generationalarena_example() {
    let mut arena: generational_arena::Arena<SomeData> = generational_arena::Arena::new();
    for _ in 0..10 {
        let data1_index: generational_arena::Index = arena.insert(SomeData {
            name: "some data".to_string(),
            value: 1
        });
        let data2_index: generational_arena::Index = arena.insert(SomeData {
            name: "some more data".to_string(),
            value: 2
        });
        // This will panic if data1_index has no associated data.
        let data1: &SomeData = &arena[data1_index];
        // get_mut() returns an Option, which will be None if data2_index
        // has no associated data.        
        let data2: &mut SomeData = arena.get_mut(data2_index).unwrap();
        // use data1 and data2 here, now we're all done
        // There is no explicit way to reset the arena, but when it goes
        // out of scope all of its memory will be freed.
        arena.clear();
    }
}

```

这里最大的不同是，这种竞技场允许删除单个对象，这对于竞技场来说是不寻常的。然而，这也意味着 arena 的代码中包含了更多的簿记工作，所以最好避免使用这个板条箱，除非你需要这个功能。

和`typed-arena`和`id_arena`一样，每个竞技场只能分配一种类型的对象。它为分配的对象运行`Drop`实现。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 结论

在某些情况下，使用竞技场可以大大加快分配许多对象的速度。他们很容易在铁锈中测试，只需要一个箱子。所以，如果你正在寻找让你的代码运行得更快的方法，试试吧！

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。