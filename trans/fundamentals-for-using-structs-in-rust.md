# Rust - LogRocket 博客中使用结构的基础

> 原文：<https://blog.logrocket.com/fundamentals-for-using-structs-in-rust/>

## 什么是结构？

理解面向对象编程是任何一个开发人员都必须具备的。面向对象编程包括创建类，这些类充当对象的描述或蓝图。对象通常由几个变量或函数组成。

在 C、Go 和 Rust 这样的语言中，类不是一个特性。相反，这些语言使用仅定义一组属性的结构。虽然结构不允许你定义方法，但是 Rust 和 Go 都以一种提供结构访问的方式定义函数。

在本教程中，我们将学习 Rust 中结构操作的基础知识。我们开始吧！

## 什么是铁锈？

由 Mozilla 开发的编程语言 Rust T1 扮演了与 C 类似的角色，它是一种快速的低级语言，使用现代语法模式和中央包管理器。

## 在 Rust 中编写结构

在下面的代码中，我们将为包含属性`name`和`age`的`Cat`类型编写一个简单的结构。一旦我们定义了我们的结构，我们将定义我们的主函数。

我们将创建该结构的一个新的`string`和一个新的实例，传递给它`name`和`age`属性。我们将打印整个结构并在字符串中插入它的属性。对于`name`，我们将使用`Scratchy`。对于`age`，我们将使用`4`:

```
// This debug attribute implements fmt::Debug which will allow us
// to print the struct using {:?}
#[derive(Debug)]
// declaring a struct
struct Cat {
 // name property typed as a String type
 name: String,
 // age typed as unsigned 8 bit integer
 age: u8
}
fn main() {
 // create string object with cat's name
 let catname = String::from("Scratchy");
 // Create a struct instance and save in a variable
 let scratchy = Cat{ name: catname, age: 4 };

```

注意，我们使用了`derive`属性，我们将在后面详细介绍，来自动实现我们的结构上的某些特征。由于我们派生了`debug`特征，我们可以使用`{:?}`打印整个结构:

```
// using {:?} to print the entire struct
 println!("{:?}", scratchy);

 // using individual properties in a String
 println!("{} is {} years old!", scratchy.name, scratchy.age);
}

```

在这一部分有几件重要的事情需要注意。首先，与 Rust 中的任何值一样，struct 中的每个属性都必须是`types`。此外，一定要考虑字符串(字符串对象或结构)和`&str`(指向字符串的指针)之间的区别。因为我们使用的是 string 类型，所以我们必须从正确的字符串文字创建一个字符串。

## `derive`属性

默认情况下，结构是不可打印的。一个结构必须实现`stc::fmt::debug`函数来使用带有`println!`的`{:?}`格式化程序。然而，在上面的代码示例中，我们使用了`derive(Debug)`属性，而不是手动实现 trait。这个属性允许我们打印出结构以便于调试。

属性充当编译器写出样板文件的指令。Rust 中还有其他几个内置的 [`derive`属性](https://doc.rust-lang.org/reference/attributes/derive.html)，我们可以利用它们让编译器为我们实现某些特征:

*   `[#derive(hash)]`:将结构转换成散列
*   `[#derive(clone)]`:添加一个克隆方法来复制结构
*   `[#derive(eq)]`:实现`eq`特征，当所有属性具有相同的值时设置相等

## 结构特征

我们可以创建一个带有属性的结构，但是我们如何像在其他语言中创建类那样将它们绑定到函数上呢？

Rust 使用了一个名为 traits 的特性，它定义了一组函数供结构实现。特征的一个好处是你可以用它们来打字。您可以创建可由实现相同特征的任何结构使用的函数。本质上，只要实现了正确的特征，就可以将方法构建到结构中。

使用特征来提供方法允许一种称为组合的实践，这也在 Go 中使用。与通常从一个父类继承方法的类不同，任何结构都可以在不使用层次结构的情况下混合和匹配它需要的特征。

## 写一个特质

让我们继续上面的例子，定义一个`Cat`和`Dog`结构。我们希望两者都有`Birthday`和`Sound`功能。我们将在一个名为`Pet`的特征中定义这些函数的签名。

在下面的例子中，我们将使用`Spot`作为`Dog`的`name`。我们用`goes Ruff`作为`Sound`，用`0`作为`age`。对于`Cat`，我们将使用`goes Meow`作为声音，使用`1`作为`age`。生日的功能是`self.age += 1;`:

```
// Create structs
#[derive(Debug)]
struct Cat { name: String, age: u8 }
#[derive(Debug)]
struct Dog { name: String, age: u8 }
// Declare the struct
trait Pet {
 // This new function acts as a constructor
 // allowing us to add additional logic to instantiating a struct
 // This particular method belongs to the trait
 fn new (name: String) -> Self;
// Signature of other functions that belong to this trait
 // we include a mutable version of the struct in birthday
 fn birthday(&mut self);
 fn sound (&self);
}

// We implement the trait for cat
// we define the methods whose signatures were in the trait
impl Pet for Cat {

 fn new (name: String) -> Cat {
   return Cat {name, age: 0};
 }

 fn birthday (&mut self) {
   self.age += 1;
   println!("Happy Birthday {}, you are now {}", self.name, self.age);
 }

 fn sound(&self){
   println!("{} goes meow!", self.name);
 }
}

// We implement the trait for dog
// we only define sound. Birthday and name are already defined
impl Pet for Dog {

 fn new (name: String) -> Dog {
   return Dog {name, age: 0};
 }

 fn birthday (&mut self) {
   self.age += 1;
   println!("Happy Birthday {}, you are now {}", self.name, self.age);
 }

 fn sound(&self){
   println!("{} goes ruff!", self.name);
 }
}

```

请注意，我们定义了一个新方法，它的行为类似于一个构造函数。我们可以直接键入新的变量，而不是像在前面的代码片段中那样创建一个新的`Cat`！

当我们调用构造函数时，它将使用特定结构类型的新实现。因此，`Dog`和`Cat`都可以使用`Birthday`和`Sound`功能:

```
fn main() {
 // Create structs using the Pet new function
 // using the variable typing to determine which
 // implementation to use
 let mut scratchy: Cat = Pet::new(String::from("Scratchy"));
 let mut spot: Dog = Pet::new(String::from("Spot"));

 // using the birthday method
 scratchy.birthday();
 spot.birthday();

 // using the sound method
 scratchy.sound();
 spot.sound();
}

```

关于特征，有几件重要的事情需要注意。首先，您必须为实现该特征的每个结构定义函数。您可以通过在 trait 定义中创建默认定义来实现这一点。

我们使用关键字`mut`声明结构，因为结构可以被函数改变。例如，`birthday`增加年龄并改变结构的属性，因此，我们将参数作为可变引用传递给结构`(&mut self)`。

在这个例子中，我们使用了一个静态方法来初始化一个新的结构，这意味着新变量的类型是由结构的类型决定的。

## 返回结构

有时，一个函数可能返回几个可能的结构，当几个结构实现相同的特征时就会出现这种情况。要编写这种类型的函数，只需键入实现所需特征的结构的返回值。

让我们继续前面的例子，在一个`Box`对象中返回`Pet`:

```
// We dynamically return Pet inside a Box object
fn new_pet(species: &str, name: String) -> Box<dyn Pet> {

```

在上面的例子中，我们对返回值使用了`Box`类型，这允许我们为实现`Pet`特征的任何结构分配足够的内存。我们可以在函数中定义一个返回任何类型的`Pet`结构的函数，只要我们将它包装在一个新的`Box`中。

我们创建了一个函数，它通过传递一个类型为`Pet`和`name`的字符串来实例化我们的`Pet`，而没有指定`age`。使用`if`语句，我们可以确定实例化什么类型的`Pet`:

```
if species == "Cat" {
   return Box::new(Cat{name, age: 0});
 } else {
   return Box::new(Dog{name, age: 0});
 }
}

```

该函数返回一个`Box`类型，表示为实现`Pet`的对象分配的内存。当我们创建`Scratchy`和`Spot`时，我们不再需要输入变量。我们在返回`Dog`或`Cat`的函数中明确地布置了逻辑:

```
fn main() {
 // Create structs using the new_pet method
 let mut scratchy = new_pet("Cat", String::from("Scratchy"));
 let mut spot = new_pet("Dog", String::from("Spot"));

 // using the birthday method
 scratchy.birthday();
 spot.birthday();

 // using the sound method
 scratchy.sound();
 spot.sound();
}

```

## 摘要

我们已经了解了 Rust 中的以下结构:

1.  结构允许我们在单个数据结构中对属性进行分组
2.  使用特征，我们可以在一个结构上实现不同的方法
3.  使用特征类型化允许我们编写可以接收和返回结构的函数
4.  属性允许我们轻松地在结构中实现某些特征

现在，我们可以在 Rust 中使用复合而非继承来实现典型的面向对象设计模式。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。