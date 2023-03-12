# Rust - LogRocket 博客的前三大模板库

> 原文：<https://blog.logrocket.com/top-3-templating-libraries-for-rust/>

Rust 有很多很好的模板箱，包括一些稳定的、准备用于生产的模板箱。其中一些是其他语言模板库的移植，而另一些是专门为 Rust 创建的。

在本指南中，我们将比较其中一些板条箱，并向您展示如何开始使用每种板条箱。

## 1.把手

手柄是一个最初为 JavaScript 开发的最小模板系统。有了[车把板条箱](https://crates.io/crates/handlebars)，我们可以在 Rust 中使用相同的系统。这个板条箱是最适合生产的铁锈模板板条箱之一，甚至被用来渲染 rust-lang.org。尽管如此，handlebars crate 与 JavaScript 实现并不是 100%兼容，所以请记住以下区别。

1.  不支持小胡子块
2.  [Chained else](https://github.com/sunng87/handlebars-rust/issues/12) 尚未实现

如果这些限制不是交易破坏者，车把可能是一个很好的选择。

让我们来看看如何使用这个板条箱。

首先，用 cargo 创建一个新项目，并将其添加到您的依赖项中。

```
[dependencies]
handlebars = "3"

```

我们可以从 hello world 的例子开始。

```
use handlebars::Handlebars;
use std::collections::HashMap;

fn main() {
    let mut handlebars = Handlebars::new();
    let source = "Hello {{ name }}";
    handlebars
        .register_template_string("hello", source)
        .unwrap();

    let mut data = HashMap::new();
    data.insert("name", "Rust");

    println!("{}", handlebars.render("hello", &data).unwrap());
}

```

这里我们使用一个字符串注册一个模板，并在一个`HashMap`中提供所需的数据作为键值对。这段代码输出“Hello Rust”尝试用其他东西替换哈希表中的“name”键，然后再次运行代码。您应该看到它输出“Hello”，因为在呈现时“name”字段永远不会提供给模板。

有时候这不是我们想要的；模板期望给定一个名称，但如果没有给定，它会自动跳过。幸运的是，handlebars crate 有一个严格的模式，当试图访问不存在的字段时会产生一个`[RenderError](https://docs.rs/handlebars/3.4.0/handlebars/struct.RenderError.html)`，这在 JavaScript 版本中是没有的。在渲染之前，我们可以使用下面的代码行启用严格模式。

```
handlebars.set_strict_mode(true);

```

现在，当渲染时没有给模板提供“名称”字段时，上面的示例将会死机。

对于较长的模板，将它们移动到自己的文件中可能是个好主意。一种方法是使用 [`include_str`](https://doc.rust-lang.org/std/macro.include_str.html) 宏。

```
handlebars
    .register_template_string("hello", include_str!("templates/hello.hbs"))
    .unwrap();

```

`template/hello.hbs`是模板文件的位置，相对于 Rust 源文件。这个宏很酷的一点是它包含了编译时文件的内容，产生了一个`&'static str`。这意味着模板字符串将包含在编译后的二进制文件中，我们不需要在运行时加载文件。

然而，如果我们想在运行时加载模板，Handlebars 提供了一些实用函数来帮助我们。

要从文件中加载和注册单个模板:

```
handlebars.register*template*file("template_name", "templates/index.hbs");
```

要加载并注册模板文件的整个目录:

```
handlebars.register*templates*directory(".hbs", "path/to/templates");
```

第一个参数(`.hbs`)是要查找的文件扩展名。在这种情况下，注册模板的名称将是模板目录中模板的相对路径，不带扩展名。例如，`path/to/templates/blog/hello.hbs`会有一个带有上述代码的名字`blog/hello`。注意，以这种方式加载模板目录需要`dir_source`特性。

```
[dependencies]
handlebars = { version = "3", features = ["dir_source"] }

```

需要注意的最后一件事是，`render`方法接受任何实现了 [Serde 的`Serialize`特征](https://docs.rs/serde/1.0.115/serde/ser/trait.Serialize.html)的数据。之前我们使用了`HashMap`，但是在适当的时候可以随意使用其他的东西。

总的来说，如果你想在模板中嵌入尽可能少的逻辑，我会推荐把手板条箱。它在 Rust 和 JavaScript 社区中被广泛使用，Rust 实现非常稳定。

## 2.特拉

[Tera](https://tera.netlify.com) 是一种受 [Jinja2](http://jinja.pocoo.org) 和 [Django 模板语言](https://docs.djangoproject.com/en/1.9/topics/templates)启发的模板语言。不像车把，这个板条箱不是一个直接端口，也不打算 100%兼容。与手柄不同的是，Tera 模板语言允许在模板中使用复杂的逻辑，功能更加丰富。

首先，将`tera`的依赖项添加到`Cargo.toml`中。

```
[dependencies]
tera = "1"

```

与我们在把手示例中使用的“hello world”类似，如下所示。

```
use tera::{Context, Tera};

fn main() {
    let mut tera = Tera::default();

    let source = "Hello {{ name }}";
    tera.add_raw_template("hello", source).unwrap();

    let mut context = Context::new();
    context.insert("name", "Rust");

    println!("{}", tera.render("hello", &context).unwrap());
}

```

如你所见，非常相似。首先，我们注册一个模板字符串，然后定义一些数据`context`，最后使用模板和数据呈现一个输出字符串。这里的一个重要区别是，`Tera::render`的第二个参数采用了由 Tera crate 提供的类型`&Context`。幸运的是，我们没有失去 Serde 的`Serialize`的灵活性，因为`Context`类型本身可以从实现`Serialize`的任何值构建。

```
let value = // something that implements `Serialize`
let context = Context::from_serialize(value);

```

就像我们之前看到的一样，`include_str`宏可以用来在编译时包含外部模板文件。运行时读取模板可以手动完成，也可以使用 Tera 提供的助手方法，我们通常使用的是`Tera::new`。

```
let tera = Tera::new("templates/**/*").unwrap();

```

这需要一个 glob，然后加载并注册与扩展的 glob 匹配的每个模板。

### **万亿模板语言**

由于 Tera 模板语言有很多特性，而且在 Rust 之外并不存在，我们将回顾一下使用它的基础知识。如需完整参考，请查看优秀的 [Tera 文档](https://tera.netlify.app/docs)。

Tera 模板有三种特殊分隔符:

1.  `{{`和`}}`为表达式
2.  用于语句的`{%`和`%}`(`{%-`和`-%}`可以分别用于去除前导/尾随空格)
3.  `{#`和`#}`进行评论

它们支持数学和比较。

```
<h1>Flower shop</h1>
{% if visit_count % 10000 == 0 %}
<p>Congratulations lucky visitor! You win a flower!</p>
{% else %}
<p>Welcome to the flower shop.</p>
{% endif %}

```

这个例子还演示了带有`if`和`else`的条件控制结构的语法。除此之外，您还可以在模板条件中使用逻辑运算符`and`、`or`和`not`。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
{% if month == 1 and day == 1 %}
<p>Happy new year!</p>
{% endif %}

```

Tera 包括一个称为过滤器的概念，可用于修改模板中的数据。过滤器可以链接，您可以注册自己的自定义过滤器。

```
tera.register_filter("upper", string::upper);

```

然后可以在类似下面的模板中使用它。

```
<h2>Hello, {{ name | upper }}!</h2>

```

虽然我们可以创建自己的过滤器，但 Tera 有一些内置的过滤器，用于人们想做的一些常见事情，这可能就足够了。

对于数组和结构的迭代，Tera 提供了循环。

```
{% for student in students %}
<div>
  <h3>{{ student.name }}</h3>
  <p>{{ student.score }}</p>
</div>
{% endfor %}

<ul>
  {% for key, value in books %}
  <li>{{ key }} - {{ value.author }}</li>
  {% endfor %}
</ul>

```

最后，我们可以使用`include`来构建模板。

```
{% include "header.html" %}
<h1>Blog</h1>
<p>Welcome to my blog</p>
{% include "footer.html" %}

```

这只是您可以使用 Tera 模板做的事情的皮毛。它们还支持函数、宏、继承等等，但是为了简洁起见，我在这里就不赘述了。

Tera 可以被认为是稳定的和生产就绪的。我相信通常最好不要在模板中包含太多的逻辑，但是当需要额外的灵活性时，Tera 是目前 Rust 的最佳选择之一。

## 3.液体

我们要看的最后一个箱子是[液体](https://crates.io/crates/liquid)，这是流行的[液体模板语言](https://shopify.github.io/liquid)的一个端口，最初是用 Ruby 编写的。这个机箱的一个明确目标是与 [Shopify/liquid](https://github.com/Shopify/liquid) 百分之百兼容。

和往常一样，我们可以从向依赖项添加液体开始。

```
[dependencies]
liquid = "0.21"

```

同样，等效的“hello world”示例如下所示:

```
use liquid::ParserBuilder;

fn main() {
    let source = "Hello {{ name }}";
    let template = ParserBuilder::with_stdlib()
        .build()
        .unwrap()
        .parse(source)
        .unwrap();

    let globals = liquid::object!({
        "name": "Rust"
    });

    println!("{}", template.render(&globals).unwrap());
}

```

我们一次创建一个模板结构，而不是像`Handlebars`和`Tera`那样拥有一个包含所有模板的结构。这一次，我们的数据`globals`是用`liquid::object`宏定义的，但是我们也可以通过直接使用`liquid::Object`类型来使用类似于`HashMap`的 API。像其他箱子一样，数据对象可以直接从实现`Serialize`的类型中创建。

```
let value = // something that implements `Serialize`
let globals = liquid::to_object(&value).unwrap();

```

一般来说，liquid crate 没有太多的[公共 API 表面](https://docs.rs/liquid/0.21.1/liquid),并且缺少从文件中加载模板之类的实用功能。也就是说，在运行时将文件加载到字符串中是一件你可以自己轻松完成的事情。

液体模板与 Tera 模板有许多相似之处。它们共享相同的表达式和语句分隔符，并且还具有语法相似的过滤器。

```
<p>{{ "rust!" | capitalize | prepend: "Hello " }}</p>

```

这个模板将呈现字符串`"<p>Hello Rust!</p>"`。

在我们观察的三个板条箱中，液体是最不适合生产的，这一事实得到了它缺乏 1.0 版本的支持。尽管如此，对于来自 Ruby 版本的 liquid 或者只是喜欢模板语言本身的人来说，它仍然是一个不错的选择。

## 结论

看了三个最流行的模板箱之后，很容易说模板对于用 Rust 编写的 web 项目来说不是问题，而且 Rust 已经为它们提供了强大的支持。车把和 Tera 都是稳定的、可生产的板条箱，虽然液体可能还没有达到相同的水平，但它仍然是一个可靠的选择。

值得注意的是，在 Rust 中，除了这三种模板之外，还有很多其他选择，但大多数其他选择都不太受欢迎，也没有稳定的版本。无论您决定使用哪种机箱，您都将能够获得用 Rust 编写 web 应用程序的性能和可靠性优势。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。