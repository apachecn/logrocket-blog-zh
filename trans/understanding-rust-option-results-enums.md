# 理解 Rust 选项和结果枚举

> 原文：<https://blog.logrocket.com/understanding-rust-option-results-enums/>

Rust 是一种专注于安全性和性能的系统编程语言，在 Stack Overflow 的年度调查中连续六年被选为“最受欢迎的语言”！Rust 编程如此令人愉快的原因之一是，尽管它专注于性能，但它有许多与高级语言相关的深思熟虑的便利性。

其中一个便利就是使用枚举，特别是`Option`和`Result`类型。因此，在这篇文章中，我们将涉及以下内容:

## `Option`型

Rust 的可空类型版本是`Option<T>`类型。它是一种枚举类型(在其他一些语言中也称为代数数据类型)，其中每个实例要么是:

在这里，`value`可以是任何类型`T`的值。例如，`Vec<T>`是 Rust 的类型，表示一个向量(或可变大小的数组)。它有一个返回`Option<T>`的`pop()`方法，如果向量为空，则返回`None`，或者返回包含向量最后一个值的`Some(value)`。

返回一个`Option`的 API 的好处之一是，为了得到里面的值，调用者必须检查这个值是否是`None`。这避免了没有可空类型的其他语言中的问题。

例如，在 C++中，`std::find()`返回一个迭代器，但是您必须记住检查它以确保它不是容器的`end()`——如果您忘记了这个检查并试图将项目从容器中取出，您会得到未定义的行为。

缺点是这往往会使代码冗长得令人恼火。但是，Rust 有很多锦囊妙计可以帮上忙！

### 使用`expect`和`unwrap`

如果你确定一个`Option`里面有一个真实的值，那么`expect()`和`unwrap()`就是你要的！它们返回里面的值，但是如果变量实际上是`None`，你的程序就退出。(这被称为恐慌，在某些情况下它是可恢复的，但是为了简单起见，我们在这里忽略它。)

唯一的区别是,`expect()`允许您指定一个定制的消息，当程序退出时打印到控制台。

还有一个`unwrap_or()`，它让你指定一个默认值，如果值是`None`，那么`Some(5).unwrap_or(7)`是`5`，而`None.unwrap_or(7)`是`7`。

如果您愿意，可以在调用`unwrap()`之前检查`Option<T>`是否有值，如下所示:

```
// t is an Option<T>
if t.is_some() {
  let real_value = t.unwrap();
}

```

但是，有更简洁的方法可以做到这一点(例如，使用`if let`，我们将在后面介绍)。

### 使用`match`

查看一个`Option`是否有值的最基本方法是使用带有一个`match`表达式的模式匹配。这适用于任何枚举类型，如下所示:

```
// t is an Option<T>
match t {
  None => println!("No value here!"), // one match arm
  Some(x) => println!("Got value {}", x) // the other match arm
};

```

需要注意的一点是，Rust 编译器强制要求`match`表达式必须是详尽的；也就是说，每个可能的值都必须被匹配臂覆盖。因此，下面的代码无法编译:

```
// t is an Option<T>
match t {
  Some(x) => println!("Got value {}", x)
};

```

我们得到一个错误:

```
error[E0004]: non-exhaustive patterns: `None` not covered

```

这实际上非常有助于避免您认为已经涵盖了所有案例，但实际上并没有！如果你明确地想要忽略所有其他情况，你可以使用`_` `match`表达式:

```
// t is an Option<T>
match t {
  Some(x) => println!("Got value {}", x), // the other match arm
  _ => println!("OK not handling this case.");
};

```

### 使用`if let`

只有在`Option`有真实值的情况下才想做某事，这是很常见的，而`if let`是一种将做这件事与获得潜在值结合起来的简洁方法。

例如，如果`t`有值，下面的代码将打印`"Got <value>"`，如果`t`为`None`，则不执行任何操作:

```
// t is an Option<T>
if let Some(i) = t {
    println!("Got {}", i);
}

```

实际上适用于任何枚举类型！

### 使用`map`

也有很多方法可以对一个`Option<T>`做事情而不检查它是否有值。举例来说，如果有实值，可以使用`map()`来转换，否则就保留为`None`。

所以，比如`Some(10).map(|i| i + 1)`是`Some(11)`，`None.map(|i| i + 1)`还是`None`。

### 使用`into_iter`和`Option`

如果你有一个`Vec<Option<T>>`，你可以把它转换成一个`Option<Vec<T>>`，如果原始向量中的任何一个条目是`None`，那么它就是`None`。

如果您考虑从许多操作中接收结果，并且希望在任何单个操作失败的情况下整体结果失败，那么这是有意义的。

所以，比如说`vec![Some(10), Some(20)].into_iter().collect()`是`Some([10, 20])`
而`vec![Some(10), Some(20), None].into_iter().collect()`是`None`。

## `Result`型

Rust 的`Result<T, E>`类型是一种返回值或错误的便捷方式。像`Option`类型一样，它是一个枚举类型，有两种可能的变体:

*   `Ok(T)`，表示操作成功，值为`T`
*   `Err(E)`，表示操作失败，出现错误`E`

很方便的知道如果一个函数返回一个错误，就会是这个类型，还有一堆很有帮助的使用方法！

### 使用`ok_or`

由于`Option`和`Result`非常相似，所以有一种简单的方法可以在两者之间进行区分。`Option`有`ok_or()`法:`Some(10).ok_or("uh-oh")`为`Ok(10)`，`None.ok_or("uh-oh")`为`Err("uh-oh")`。

那么，`Result`就有了`ok()`的方法:`Ok(10).ok()`就是`Some(10)`，`Err("uh-oh").ok()`就是`None`。

在`Result`上还有一个`err()`方法做相反的事情:错误被映射到`Some`，成功值被映射到`None`。

### 使用`expect`、`unwrap`、`match`和`if let`

就像`Option`一样，如果你确定`Result`是成功的(如果你错了，你不介意退出！)、`expect()`和`unwrap()`的工作方式与它们为`Option`工作的方式完全一样。

而且，由于`Result`是一个枚举类型，`match`和`if let`的工作方式也是一样的！

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

### 使用`?`操作符

好吧，这就是事情变得很酷的地方。假设您正在编写一个返回`Result`的函数，因为它可能会失败，而您正在调用另一个返回`Result`的函数，因为它可能会失败。

很多时候，如果另一个函数返回错误，您希望直接从该函数返回错误。因此，您的代码将如下所示:

```
let inner_result = other_function();
if let Err(some_error) = inner_result {
    return inner_result;
}
let real_result = inner_result().unwrap();
// now real_result has the actual value we care about, we can continue on...

```

但是，一遍又一遍地写这本书是一种痛苦。相反，您可以编写以下代码:

```
let real_result = other_function()?;

```

没错:单个`?`操作符就可以完成所有的工作！更好的是，您可以将呼叫链接在一起，就像这样:

```
let real_result = this_might_fail()?.also_might_fail()?.this_one_might_fail_too()?;

```

另一种常见的技术是使用类似于`map_err()`的东西将错误转换成对外部函数返回更有意义的东西，然后使用`?`操作符。

### 使用`must_use`

Rust 编译器是出了名的有用，它的帮助方式之一是警告你可能会犯的错误。

`Result`类型用`must_use`属性标记，这意味着如果函数返回一个`Result`，调用者一定不能忽略该值，否则编译器会发出警告。

这主要是没有实际值要返回的函数的问题，比如 I/O 函数；它们中的许多都返回类似于`Result<(), Err>` ( `()`被称为单元类型)的类型，在这种情况下，很容易忘记检查错误，因为没有要获取的成功值。

但是，编译器可以帮助你记忆！

### 使用`into_iter`和`Result`

类似于`Option`，如果你有一个`Vec<Result<T, E>>`，你可以使用`into_iter()`和`collect()`将它转换成一个`Result<Vec<T>, E>`，它将包含所有的成功值或者遇到的第一个错误。

所以，举个例子，下面是`Ok([10, 20])`:

```
vec![Ok(10), Ok(20)].into_iter().collect() 

```

然后，这是`Err("bad")`:

```
vec![Ok(10), Err("bad"), Ok(20), Err("also bad")].into_iter().collect()

```

如果您想收集所有的错误，而不仅仅是第一个错误，这就有点棘手了，但是您可以使用方便的`partition()`方法来区分成功和错误:

```
let v: Vec<Result<_, _>> = some_other_function();
let (successes, errors): (Vec<_>, Vec<_>) = v.into_iter().partition(Result::is_ok);
if !errors.is_empty() {
  return Err(errors.into_iter().map(Result::unwrap_err).collect());
}
else {
  return Ok(successes.into_iter().map(Result::unwrap).collect());
}

```

## 结论

`Option`和`Result`背后的想法并不新鲜。对我来说最突出的是这种语言通过检查错误来做正确的事情是多么容易，尤其是使用了`?`操作符。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。