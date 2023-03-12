# 在 Rust 中使用通用 impl 块

> 原文：<https://blog.logrocket.com/generic-impl-blocks-rust/>

Rust 泛型实现块使得实现泛型类型的方法更加容易。

在本文中，我们将通过首先理解没有泛型的世界的乏味以及当我们使用它们时的结果来说明泛型实现块的价值。

在这篇文章中:

## 生锈结构和`impl`块

与 C++、JavaScript 和 C#等语言不同，在 Rust 中，没有用于定义对象类型的对象(属性和方法组)或类。

相反， [Rust 和 Go](https://blog.logrocket.com/when-to-use-rust-when-to-use-golang/) 一样，选择仅仅是属性组的结构。在 Rust 中，通过使用实现将方法添加到结构中，并且使用`impl`块来定义方法。参考下面的例子。

```
// Struct Definition
struct Person {
    name: String
}

// Impl block defining method
impl Person {
  fn say_name(&self){
    println!("{}", self.name);
  }
}

fn main() {
    // Instantiate a person
    let alex = Person {name: "Alex Merced".to_string()};

    // Call methods
    alex.say_name();
}

```

在上面的代码中，我们创建了一个带有一个名为`name`的`String`属性的`Person`结构。然后，我们使用一个`impl`块来定义和实现一个`say_name`方法。在 main 函数中，我们创建了一个`Person`结构并调用了`say_name()`方法，该方法将打印`Alex Merced`。

## 泛型类型

有时，我们的结构可能具有不同类型定义的属性。为了指定，我们可以列出未知类型作为变量，比如`<T>`或`<T, U>`，这取决于我们需要指定多少变量类型。

然后，我们需要为我们想要支持的每种类型实现任何方法。如果我们为许多类型实现一个方法，这可能是非常乏味的。

```
// Generic Struct Definition, T is a variable for unknown type
struct Stats<T> {
    age: T,
    height:T
}

// Impl blocks defining methods for different types
impl Stats<i32> {
  fn print_stats(&self){
    println!("Age is {} years and Height is {} inches", self.age, self.height);
  }
}

impl Stats<f32> {
  fn print_stats(&self){
    println!("Age is {} years and Height is {} feet", self.age, self.height);
  }
}

fn main() {
    // Instantiate using i32 stats
    let alex = Stats {age: 37, height: 70};
    // Instantiate using f32 stats
    let alex2 = Stats {age: 37.0, height: 5.83};
    // Call methods
    alex.print_stats();
    alex2.print_stats();
}

```

在上面的示例代码中，我们定义了一个`Stats`结构，它有两个相同类型的属性，用`T`表示。因为我们在写`<T>`的时候定义为通用类型，所以这个类型可以是任何类型，但是年龄和身高必须相同。

我们希望任何使用两个 32 位数的`Stats`对象，不管是整数还是浮点数，都有一个`print_stats`方法。在这种情况下，我们必须为每种可能性创建两个实现块。

然后我们实例化两个`Stats`结构来使用`i32`值和`f32`值。我们调用 structs 的`print_stats`方法来获得以下输出:

```
Age is 37 years and Height is 70 inches
Age is 37 years and Height is 5.83 feet

```

请注意不同的输出，因为我们从正确的实现块调用了实现。

## 通用实现模块

为每种类型编写一个单独的实现块可能会很乏味，尤其是在有多种可能性的情况下。

如果几个类型的实现是相同的，我们可以使用泛型实现块，它和泛型类型一样，允许我们定义表示类型的变量:

```
use std::fmt::Display;

// Generic Struct Definition, T is a variable for unknown type
struct Stats<T> {
    age: T,
    height:T
}

// Impl blocks defining methods for different types
impl<T:Display> Stats<T> {
  fn print_stats(&self){
    println!("Age is {} years and Height is {}", self.age, self.height);
  }
}

fn main() {
    // Instantiate using i32 stats
    let alex = Stats {age: 37, height: 70};
    // Instantiate using f32 stats
    let alex2 = Stats {age: 37.0, height: 5.83};
    // Instantiate using String stats
    let alex3 = Stats {age: "37".to_string(), height: "5'10ft".to_string()};
    // Call methods
    alex.print_stats();
    alex2.print_stats();
    alex3.print_stats();
}

```

在上面的代码中，我们从标准库中导入了`Display`特征，因为我们以后需要引用它。

我们将`Stats`结构定义为具有相同泛型类型的两个属性的结构，这两个属性分别是年龄和高度。

我们只使用了一个实现块来定义泛型类型`<T:Display>`，这意味着`T`是泛型类型的变量。`:Display`意味着它必须是实现了`Display`特征的类型。这是必需的，所以我们可以在这个实现中使用`println!`宏。

然后我们定义了三个结构:一个使用`i32`，另一个使用`f32`，最后一个使用字符串作为属性值。(我们使用了三种不同的类型，只需要一个`impl`块。多酷啊！)

当它运行时，您应该得到下面的输出:

```
Age is 37 years and Height is 70
Age is 37 years and Height is 5.83
Age is 37 years and Height is 5'10ft

```

## 使用通用`impl`块实施列车

当我们需要在许多类型上实现一个特征时，泛型`impl`块也很有帮助。在这种情况下，我们可以使用通用实现来节省时间，然后根据需要定义特定的实现。您可以在下面的代码中看到这一点。

```
use std::fmt::Display;

// Generic Struct Definition, T is a variable for unknown type
struct Stats<T> {
    age: T,
    height:T
}

// Generic Tuple Struct that holds one value
struct Number<T> (T);

// trait with default implementation of print_stats method
trait ViewStats {
  fn print_stats(&self){
    println!("The type of age and height doesn't implement the display trait")
  }
}

// Impl blocks defining methods for different types
impl<T:Display> ViewStats for Stats<T> {
  fn print_stats(&self){
    println!("Age is {} years and Height is {}", self.age, self.height);
  }
}

//Impl block to ViewStats trait to number but use default implementation
impl<T> ViewStats for Stats<Number<T>> {}

fn main() {
    // Instantiate using i32 stats
    let alex = Stats {age: 37, height: 70};
    // Instantiate using f32 stats
    let alex2 = Stats {age: 37.0, height: 5.83};
    // Instantiate using String stats
    let alex3 = Stats {age: "37".to_string(), height: "5'10ft".to_string()};
    // Instantiate using String stats
    let alex4 = Stats {age: Number(37), height: Number(70)};
    // Call methods
    alex.print_stats();
    alex2.print_stats();
    alex3.print_stats();
    alex4.print_stats();
}

```

在上面的代码中，我们从标准库中导入了`Display`特征。我们将`Stats`结构定义为具有除匹配类型之外的任何类型的`age`和`height`属性的结构。

然后，我们定义了一个`Number`结构，它是一个只有一个值的元组。这只是为了让我们能够演示当我们使用不实现 display 的类型创建 stats 时会发生什么。

接下来，我们定义了一个`ViewStats`特征，其中`print_stats`方法被赋予了一个默认实现。如果`age`和`height`的值是有效的类型，但是没有它们自己的实现，这个实现将被调用。

然后，我们为`Stats<T>`定义了一个`ViewStats`的实现，其中`T`是实现`Display`特征的任何类型，比如`i32`、`f32`和`String`。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

然后，我们为`Number`实现了`ViewStats`。因为我们没有定义`print_stats`的新版本，它将使用 trait 声明块中的默认版本。

然后我们创建了四个结构。前三个和前面一样，第四个是一个`Stats`结构，其中年龄和高度由`Number`结构表示。

当我们运行该脚本时，我们得到以下输出:

```
Age is 37 years and Height is 70
Age is 37 years and Height is 5.83
Age is 37 years and Height is 5'10ft
The type of age and height doesn't implement the display trait

```

注意，最后一行显示了我们在 train 块中默认实现的结果，因为`Number<T>`没有实现 display，所以它不能使用与前三个`Stats<T>`实例相同的实现。

## 结论

泛型简化了应该与许多类型一起工作的代码的编写，无论是通过使用泛型来定义属性类型，还是使用泛型`impl`块来定义方法，而没有重复这么做的繁琐。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。