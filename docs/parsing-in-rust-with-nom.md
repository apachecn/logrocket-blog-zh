# 用 nom 解析 Rust

> 原文：<https://blog.logrocket.com/parsing-in-rust-with-nom/>

在本教程中，我们将演示如何使用`nom`解析器组合子库在 Rust 中编写一个非常基本的 URL 解析器。我们将详细介绍以下内容:

什么是解析器组合子？

## 解析器组合子是高阶函数，可以接受几个解析器作为输入，并返回一个新的解析器作为输出。

这种方法使您能够为简单的任务构建解析器，比如解析某个字符串或数字，并使用组合函数将它们组合成一个完整的递归下降解析器。

组合解析的好处包括可测试性、可维护性和可读性；每个移动部分都相当小，并且是自隔离的，这使得整个解析器是由模块化组件组成的。

如果你是这个概念的新手，我强烈推荐阅读 Bodil Stokke 关于 Rust 中解析器组合子的优秀教程[。](https://bodil.lol/parser-combinators/)

`nom`是如何工作的？

## [`nom`](https://github.com/Geal/nom) 是一个用 Rust 编写的解析器组合器库，它使你能够创建安全的解析器，而不会占用内存或影响性能。它依靠 Rust 强大的类型和内存安全性来生成正确且高性能的解析器，以及抽象容易出错的管道的函数、宏和特征。

为了展示`nom`是如何工作的，我们将创建一个基本的 URL 解析器。我们不会实现整个 [URL 规范](https://url.spec.whatwg.org/)；这远远超出了这个代码示例的范围。相反，我们将走一些捷径。

最终的目标是能够将有效的 URL，比如`[https://www.zupzup.org/about/?someVal=5&anotherVal=hello#anchor](https://www.zupzup.org/about/?someVal=5&anotherVal=hello#anchor)`和`[http://user:[email protected]:8080](http://user:pw@127.0.0.1:8080)`，解析成一个连贯的结构，在这个过程中为无效的 URL 返回一个有用的错误。

由于可测试性被吹捧为解析器组合子的好处之一，我们将为大多数组件编写测试，以查看这种优势的实际效果。

我们开始吧！

设置`nom`

## 接下来，你只需要一个最新的 Rust 安装(1.44+)。

首先，创建一个新的 Rust 项目:

接下来，编辑`Cargo.toml`文件并添加您需要的依赖项:

```
cargo new --lib rust-nom-example
cd rust-nom-example

```

是的，我们所需要的就是最新版本的`nom`库(在撰写本文时是 6.0)。

```
[dependencies]
nom = "6.0"

```

数据类型

## 在编写解析器时，首先定义输出结构通常是有意义的，这样可以感觉到您需要哪些部分以及它们看起来是什么样子。

在本例中，我们正在解析一个 URL，所以让我们为它定义一个结构:

让我们一行一行地过一遍。

```
#[derive(Debug, PartialEq, Eq)]
pub struct URI<'a> {
    scheme: Scheme,
    authority: Option<Authority<'a>>,
    host: Host,
    port: Option<u16>,
    path: Option<Vec<&'a str>>,
    query: Option<QueryParams<'a>>,
    fragment: Option<&'a str>,
}

#[derive(Debug, PartialEq, Eq)]
pub enum Scheme {
    HTTP,
    HTTPS,
}

pub type Authority<'a> = (&'a str, Option<&'a str>);

#[derive(Debug, PartialEq, Eq)]
pub enum Host {
    HOST(String),
    IP([u8; 4]),
}

pub type QueryParam<'a> = (&'a str, &'a str);
pub type QueryParams<'a> = Vec<QueryParam<'a>>;

```

这些字段按照它们在常规 URI 中出现的顺序排列。首先，我们有计划。在这种情况下，我们将限制自己使用`http://`和`https://`，但是请注意，还有许多其他可用的方案。

接下来是`authority`部分，由用户名和可选密码组成，通常是完全可选的。

主机可以是一个 IP(在我们的例子中，只有一个 IPv4)，也可以是一个主机字符串，比如`example.org`，后跟一个可选的`port`，它只是一个数字，比如`localhost:8080`。

在端口之后，我们有路径，它是一系列由`/`分隔的字符串，比如`/some/important/path`。这也是可选的，与查询和片段部分一样，表示 URL 的`?query=some-value&another=5`和`#anchor`部分。查询是字符串元组的可选列表，片段只是可选字符串。

如果你对这些类型中使用的寿命(`'a`)感到困惑，不要太担心它们；它们不会真正影响我们如何编写代码。本质上，我们可以使用指向部分输入字符串的指针，而不是为 URL 的每个部分分配一个新的字符串，只要输入和我们的 URI 结构一样长，我们就没事了。

在开始解析之前，让我们定义一个助手，用于将有效的方案转换为我们的`Scheme`枚举:

解决了这个问题，让我们从头开始解析`scheme`。

```
impl From<&str> for Scheme {
    fn from(i: &str) -> Self {
        match i.to_lowercase().as_str() {
            "http://" => Scheme::HTTP,
            "https://" => Scheme::HTTPS,
            _ => unimplemented!("no other schemes supported"),
        }
    }
}

```

`nom`中的错误处理

## 在开始之前，我们先来谈谈`nom`中的错误处理。虽然我们不打算在这里深入讨论，但我们将尝试至少让调用者大致了解在哪个解析步骤中出现了什么问题。

出于我们的目的，我们将使用`nom`的`context`组合子。在`nom`中，解析器通常返回以下类型:

在我们的例子中，我们有一个结果类型，它返回一个输入值的元组(`&str` —输入字符串)，这是要解析的剩余字符串，还有一个输出值。如果解析失败，它还会返回一个错误值。

```
type IResult<I, O, E = (I, ErrorKind)> = Result<(I, O), Err<E>>;

```

标准的`IResult`只允许我们使用 Nom 的内置错误类型，但是如果我们想要创建自定义错误或者给这些错误添加一些上下文呢？

`ParseError`特征和`VerboseError`类型使我们能够构建自己的错误，并向现有错误添加上下文。对于这个简单的例子，我们将为解析错误添加一些上下文。为了方便起见，让我们定义自己的结果类型:

这本质上是相同的，除了它需要一个`VerboseError`。这意味着我们可以使用 Nom 的`context`组合子，它允许我们隐式地向任何解析器添加错误上下文。

```
type Res<T, U> = IResult<T, U, VerboseError<T>>;

```

更多来自 LogRocket 的精彩文章:

* * *

### [`nom`](https://docs.rs/nom/6.0.0/nom/error/index.html)的官方文档涵盖了这些选项，但是错误处理并不是最直观的东西。

* * *

为了查看它的运行情况，让我们为该方案创建第一个解析器。

用 Rust 编写解析器

## 为了解析 URL 方案，我们希望在`http://`或`https://`上进行匹配，而不是其他。由于我们使用了强大的解析器组合子库，我们不需要手工编写这样的低级解析器。`nom`掩护我们。

这个[宏解析器和组合器的列表](https://github.com/Geal/nom/blob/master/doc/choosing_a_combinator.md)很好地概述了在某些用例中使用什么。

我们将使用`tag_no_case`解析器和`alt`组合子来说，“要么小写(输入)应该是 [http://](http://) ，要么是 [https://”对于本教程，我们将只使用普通的函数，但是请注意，`nom`中的许多解析器和组合器也可以作为宏使用。](https://.%E2%80%9D)

下面是它在 Rust 中使用`nom`的样子:

正如你所看到的，我们使用`context`组合子来包装我们实际的解析器，并向它添加`scheme`上下文，所以这里触发的任何错误都会在结果中用`scheme`标记。

```
fn scheme(input: &str) -> Res<&str, Scheme> {
    context(
        "scheme",
        alt((tag_no_case("HTTP://"), tag_no_case("HTTPS://"))),
    )(input)
    .map(|(next_input, res)| (next_input, res.into()))
}

```

一旦使用解析器和组合子组装了整个解析器，就用`input`字符串调用它，这是我们唯一的输入参数。然后我们将结果——如上所述，由剩余的输入和解析输出组成——转换成不同的形式，将我们的解析模式转换成带有前面提到的特征实现的`.into()`的`Scheme`枚举。

让我们通过编写几个测试来看看它是否有效:

正如您所看到的，在成功的案例中，我们获得了解析后的`Scheme` enum 和剩余的要解析的字符串(`"yay"`)。同样，如果有一个错误，我们会得到一个被触发的不同错误的列表和我们定义的上下文(`"scheme"`)。

```
#[cfg(test)]
mod tests {
    use super::*;
    use nom::{
        error::{ErrorKind, VerboseError, VerboseErrorKind},
        Err as NomErr,
    };

    #[test]
    fn test_scheme() {
        assert_eq!(scheme("https://yay"), Ok(("yay", Scheme::HTTPS)));
        assert_eq!(scheme("http://yay"), Ok(("yay", Scheme::HTTP)));
        assert_eq!(
            scheme("bla://yay"),
            Err(NomErr::Error(VerboseError {
                errors: vec![
                    ("bla://yay", VerboseErrorKind::Nom(ErrorKind::Tag)),
                    ("bla://yay", VerboseErrorKind::Nom(ErrorKind::Alt)),
                    ("bla://yay", VerboseErrorKind::Context("scheme")),
                ]
            }))
        );
    }
}

```

在这种情况下，两个`tag`调用都失败了，因此，`alt`组合子也失败了，因为它不能产生一个值。

这并不难。同样，我们基本上只是解析了一个常量字符串。让我们通过尝试解析`authority`部分来尝试一些更高级的东西。

使用权限解析 URL

## 如果我们记得之前的 URI 结构，尤其是权威部分，我们会发现我们在寻找一个完全可选的结构。如果存在，需要有一个用户名和一个可选的密码。

这是我们使用的类型别名:

我们该怎么做呢？在 URL 中，它看起来像这样:

```
pub type Authority<'a> = (&'a str, Option<&'a str>);

```

`[https://username:[email protected]](https://username:password@example.org)`

`:password`是可选的，但是在任何情况下，结尾都会有一个`@`，所以我们可以从使用`terminated`解析器开始。这给了我们一个字符串，它被另一个字符串终止。

在权限内，我们将`:`视为分隔符。根据便利的[文档](https://github.com/Geal/nom/blob/master/doc/choosing_a_combinator.md)的说法，看起来我们可以使用`separated_pair`组合子，它给我们两个由某个字符串分隔的值。但是我们如何处理实际的文本呢？有几个选项，其中之一是使用`alphanumeric1`解析器。这将生成至少包含一个字符的字母数字字符串。

为了简单起见，我们不会担心 URL 的不同部分可以使用哪些字符。它与编写和构造解析器无关，只会让一切变得更长、更不方便。出于我们的目的，我们将假设 URL 的大部分可以由字母数字组成，在某些情况下，可以由连字符和点组成——根据 [URL 标准](https://url.spec.whatwg.org/#url-code-points)，这当然是错误的。

让我们看看组装好的`authority`解析器:

让我们通过运行几个测试来看看它是否有效:

```
fn authority(input: &str) -> Res<&str, (&str, Option<&str>)> {
    context(
        "authority",
        terminated(
            separated_pair(alphanumeric1, opt(tag(":")), opt(alphanumeric1)),
            tag("@"),
        ),
    )(input)
}

```

看起来不错！我们有两个值都存在、密码丢失、`@`丢失的情况，以及其他一些错误情况。

```
    #[test]
    fn test_authority() {
        assert_eq!(
            authority("username:[email protected]"),
            Ok(("zupzup.org", ("username", Some("password"))))
        );
        assert_eq!(
            authority("[email protected]"),
            Ok(("zupzup.org", ("username", None)))
        );
        assert_eq!(
            authority("zupzup.org"),
            Err(NomErr::Error(VerboseError {
                errors: vec![
                    (".org", VerboseErrorKind::Nom(ErrorKind::Tag)),
                    ("zupzup.org", VerboseErrorKind::Context("authority")),
                ]
            }))
        );
        assert_eq!(
            authority(":zupzup.org"),
            Err(NomErr::Error(VerboseError {
                errors: vec![
                    (
                        ":zupzup.org",
                        VerboseErrorKind::Nom(ErrorKind::AlphaNumeric)
                    ),
                    (":zupzup.org", VerboseErrorKind::Context("authority")),
                ]
            }))
        );
        assert_eq!(
            authority("username:passwordzupzup.org"),
            Err(NomErr::Error(VerboseError {
                errors: vec![
                    (".org", VerboseErrorKind::Nom(ErrorKind::Tag)),
                    (
                        "username:passwordzupzup.org",
                        VerboseErrorKind::Context("authority")
                    ),
                ]
            }))
        );
        assert_eq!(
            authority("@zupzup.org"),
            Err(NomErr::Error(VerboseError {
                errors: vec![
                    (
                        "@zupzup.org",
                        VerboseErrorKind::Nom(ErrorKind::AlphaNumeric)
                    ),
                    ("@zupzup.org", VerboseErrorKind::Context("authority")),
                ]
            }))
        )
    }

```

让我们进入`host`部分。

Rust 解析:主机、IP 和端口

## 因为主机部分可以由主机字符串或 IP 组成，所以这一步会更复杂。更糟糕的是，我们还可以在结尾有一个可选的`:port`。

为了简单起见，我们将只支持 IPv4 IPs。我们从主持人开始。让我们来看一下实现，并一步一步地检查它:

您首先会注意到有两个选项(`alt`)。在这两种情况下，都有一个`tuple`，一系列的解析器，它们都必须工作。

```
fn host(input: &str) -> Res<&str, Host> {
    context(
        "host",
        alt((
            tuple((many1(terminated(alphanumerichyphen1, tag("."))), alpha1)),
            tuple((many_m_n(1, 1, alphanumerichyphen1), take(0 as usize))),
        )),
    )(input)
    .map(|(next_input, mut res)| {
        if !res.1.is_empty() {
            res.0.push(res.1);
        }
        (next_input, Host::HOST(res.0.join(".")))
    })
}

```

在第一种情况下，我们希望有一个或多个(`many1`)字母数字字符串，包括连字符，以`.`结尾，以 TLD (alpha1)结尾。

`alphanumerichyphen1`解析器看起来像这样:

这有点复杂，但基本上只是复制了`nom`的`alphanumeric1`解析器，并添加了`-`。我不确定这是不是最好的方法，但它确实有效！

```
fn alphanumerichyphen1<T>(i: T) -> Res<T, T>
where
    T: InputTakeAtPosition,
    <T as InputTakeAtPosition>::Item: AsChar,
{
    i.split_at_position1_complete(
        |item| {
            let char_item = item.as_char();
            !(char_item == '-') && !char_item.is_alphanum()
        },
        ErrorKind::AlphaNumeric,
    )
}

```

在任何情况下，主机中都有第二个选项，这是单个字符串的情况，比如`localhost`。

为什么我们要以如此复杂的方式，用这个看似无用的 1 和 1 来表示它呢？这里的问题是，在`alt`组合子中，所有选项必须返回相同的类型——在本例中，是一个字符串向量和另一个字符串的元组。

我们在`map`函数中也看到了这一点，如果元组的第二部分不为空(顶级域)，我们将它添加到元组的第一部分。最后，我们创建一个主机枚举，用一个`.`连接字符串部分并创建原始的主机字符串。

让我们编写一些测试:

让我们继续讨论知识产权的问题。首先，我们需要能够解析 IPv4 IP 的每个单独部分(例如:127.0.0.1):

```
    #[test]
    fn test_host() {
        assert_eq!(
            host("localhost:8080"),
            Ok((":8080", Host::HOST("localhost".to_string())))
        );
        assert_eq!(
            host("example.org:8080"),
            Ok((":8080", Host::HOST("example.org".to_string())))
        );
        assert_eq!(
            host("some-subsite.example.org:8080"),
            Ok((":8080", Host::HOST("some-subsite.example.org".to_string())))
        );
        assert_eq!(
            host("example.123"),
            Ok((".123", Host::HOST("example".to_string())))
        );
        assert_eq!(
            host("$$$.com"),
            Err(NomErr::Error(VerboseError {
                errors: vec![
                    ("$$$.com", VerboseErrorKind::Nom(ErrorKind::AlphaNumeric)),
                    ("$$$.com", VerboseErrorKind::Nom(ErrorKind::ManyMN)),
                    ("$$$.com", VerboseErrorKind::Nom(ErrorKind::Alt)),
                    ("$$$.com", VerboseErrorKind::Context("host")),
                ]
            }))
        );
        assert_eq!(
            host(".com"),
            Err(NomErr::Error(VerboseError {
                errors: vec![
                    (".com", VerboseErrorKind::Nom(ErrorKind::AlphaNumeric)),
                    (".com", VerboseErrorKind::Nom(ErrorKind::ManyMN)),
                    (".com", VerboseErrorKind::Nom(ErrorKind::Alt)),
                    (".com", VerboseErrorKind::Context("host")),
                ]
            }))
        );
    }

```

为了得到每个单独的数字，我们试图使用我们的`n_to_m_digits`解析器找到一到三个连续的数字，并将它们转换成一个`u8`。

```
fn ip_num(input: &str) -> Res<&str, u8> {
    context("ip number", n_to_m_digits(1, 3))(input).and_then(|(next_input, result)| {
        match result.parse::<u8>() {
            Ok(n) => Ok((next_input, n)),
            Err(_) => Err(NomErr::Error(VerboseError { errors: vec![] })),
        }
    })
}

fn n_to_m_digits<'a>(n: usize, m: usize) -> impl FnMut(&'a str) -> Res<&str, String> {
    move |input| {
        many_m_n(n, m, one_of("0123456789"))(input)
            .map(|(next_input, result)| (next_input, result.into_iter().collect()))
    }
}

```

这样一来，我们可以看看如何将一个完整的 IP 解析为一个数组`u8`:

在这种情况下，我们正在寻找正好三个`ip_num`，接着是一个`.`和另一个`ip_num`。在映射函数中，我们用一个数组`u8`将这些单独的结果连接到`Host::IP`枚举。

```
fn ip(input: &str) -> Res<&str, Host> {
    context(
        "ip",
        tuple((count(terminated(ip_num, tag(".")), 3), ip_num)),
    )(input)
    .map(|(next_input, res)| {
        let mut result: [u8; 4] = [0, 0, 0, 0];
        res.0
            .into_iter()
            .enumerate()
            .for_each(|(i, v)| result[i] = v);
        result[3] = res.1;
        (next_input, Host::IP(result))
    })
}

```

同样，我们将编写一些测试来确保这一点:

综上所述，我们需要另一个解析器来尝试 IP 和主机并返回一个`Host`:

```
    #[test]
    fn test_ipv4() {
        assert_eq!(
            ip("192.168.0.1:8080"),
            Ok((":8080", Host::IP([192, 168, 0, 1])))
        );
        assert_eq!(ip("0.0.0.0:8080"), Ok((":8080", Host::IP([0, 0, 0, 0]))));
        assert_eq!(
            ip("1924.168.0.1:8080"),
            Err(NomErr::Error(VerboseError {
                errors: vec![
                    ("4.168.0.1:8080", VerboseErrorKind::Nom(ErrorKind::Tag)),
                    ("1924.168.0.1:8080", VerboseErrorKind::Nom(ErrorKind::Count)),
                    ("1924.168.0.1:8080", VerboseErrorKind::Context("ip")),
                ]
            }))
        );
        assert_eq!(
            ip("192.168.0000.144:8080"),
            Err(NomErr::Error(VerboseError {
                errors: vec![
                    ("0.144:8080", VerboseErrorKind::Nom(ErrorKind::Tag)),
                    (
                        "192.168.0000.144:8080",
                        VerboseErrorKind::Nom(ErrorKind::Count)
                    ),
                    ("192.168.0000.144:8080", VerboseErrorKind::Context("ip")),
                ]
            }))
        );
        assert_eq!(
            ip("192.168.0.1444:8080"),
            Ok(("4:8080", Host::IP([192, 168, 0, 144])))
        );
        assert_eq!(
            ip("192.168.0:8080"),
            Err(NomErr::Error(VerboseError {
                errors: vec![
                    (":8080", VerboseErrorKind::Nom(ErrorKind::Tag)),
                    ("192.168.0:8080", VerboseErrorKind::Nom(ErrorKind::Count)),
                    ("192.168.0:8080", VerboseErrorKind::Context("ip")),
                ]
            }))
        );
        assert_eq!(
            ip("999.168.0.0:8080"),
            Err(NomErr::Error(VerboseError {
                errors: vec![
                    ("999.168.0.0:8080", VerboseErrorKind::Nom(ErrorKind::Count)),
                    ("999.168.0.0:8080", VerboseErrorKind::Context("ip")),
                ]
            }))
        );
    }

```

解析铁锈中的`path`

```
fn ip_or_host(input: &str) -> Res<&str, Host> {
    context("ip or host", alt((ip, host)))(input)
}

```

## 接下来要解决的是`path`。这里，我们将再次假设路径中的字符串只能包含带有连字符和点的字母数字字符串，使用下面的助手来解析它们:

为了解析`path`，我们希望将由`/`分隔的字符串解析为一个字符串向量:

```
fn url_code_points<T>(i: T) -> Res<T, T>
where
    T: InputTakeAtPosition,
    <T as InputTakeAtPosition>::Item: AsChar,
{
    i.split_at_position1_complete(
        |item| {
            let char_item = item.as_char();
            !(char_item == '-') && !char_item.is_alphanum() && !(char_item == '.')
            // ... actual ascii code points and url encoding...: https://infra.spec.whatwg.org/#ascii-code-point
        },
        ErrorKind::AlphaNumeric,
    )
}

```

我们总是从`/`开始。这已经是一个有效的路径，但是我们还可以有一个额外的`0`，或者更多(`many0` ) `/`)终止的字符串，以及一个最终的可选字符串(例如`index.php`)。

```
fn path(input: &str) -> Res<&str, Vec<&str>> {
    context(
        "path",
        tuple((
            tag("/"),
            many0(terminated(url_code_points, tag("/"))),
            opt(url_code_points),
        )),
    )(input)
    .map(|(next_input, res)| {
        let mut path: Vec<&str> = res.1.iter().map(|p| p.to_owned()).collect();
        if let Some(last) = res.2 {
            path.push(last);
        }
        (next_input, path)
    })
}

```

在映射器中，我们检查元组的第三部分(最后一部分)是否存在，如果存在，则将其推送到路径向量。

让我们也为路径编写一些测试:

看起来不错！我们得到路径的不同部分和剩余的字符串，所有东西都在一个有用的向量中。

```
    #[test]
    fn test_path() {
        assert_eq!(path("/a/b/c?d"), Ok(("?d", vec!["a", "b", "c"])));
        assert_eq!(path("/a/b/c/?d"), Ok(("?d", vec!["a", "b", "c"])));
        assert_eq!(path("/a/b-c-d/c/?d"), Ok(("?d", vec!["a", "b-c-d", "c"])));
        assert_eq!(path("/a/1234/c/?d"), Ok(("?d", vec!["a", "1234", "c"])));
        assert_eq!(
            path("/a/1234/c.txt?d"),
            Ok(("?d", vec!["a", "1234", "c.txt"]))
        );
    }

```

让我们通过解析查询和 URL 的片段部分来完成 strong。

查询和分段

## 路径基本上是一个键值对:第一个跟在`?`后面，其余的用`&`分隔。同样，我们将把自己限制在有限的`url_code_points`。

这实际上很好，因为解析器直观易读。我们正在解析`?`之后的第一个键-值对的元组，用`=`和`0`或更多类似的元素分隔，从`&`而不是`?`开始。

```
fn query_params(input: &str) -> Res<&str, QueryParams> {
    context(
        "query params",
        tuple((
            tag("?"),
            url_code_points,
            tag("="),
            url_code_points,
            many0(tuple((
                tag("&"),
                url_code_points,
                tag("="),
                url_code_points,
            ))),
        )),
    )(input)
    .map(|(next_input, res)| {
        let mut qps = Vec::new();
        qps.push((res.1, res.3));
        for qp in res.4 {
            qps.push((qp.1, qp.3));
        }
        (next_input, qps)
    })
}

```

然后，在映射器中，我们简单地将所有的键值对放入一个向量中，并在开始时定义我们的结构:

这里有几个基本的测试:

```
pub type QueryParam<'a> = (&'a str, &'a str);
pub type QueryParams<'a> = Vec<QueryParam<'a>>;

```

最后一部分是片段，它只是一个`#`后跟一个字符串:

```
    #[test]
    fn test_query_params() {
        assert_eq!(
            query_params("?bla=5&blub=val#yay"),
            Ok(("#yay", vec![("bla", "5"), ("blub", "val")]))
        );

        assert_eq!(
            query_params("?bla-blub=arr-arr#yay"),
            Ok(("#yay", vec![("bla-blub", "arr-arr"),]))
        );
    }

```

在我们讨论了所有这些复杂的解析器之后，这简直是小菜一碟。为了更好地衡量，让我们编写一些健全性检查测试:

```
fn fragment(input: &str) -> Res<&str, &str> {
    context("fragment", tuple((tag("#"), url_code_points)))(input)
        .map(|(next_input, res)| (next_input, res.1))
}

```

用`nom`解析 Rust:最终测试

```
    #[test]
    fn test_fragment() {
        assert_eq!(fragment("#bla"), Ok(("", "bla")));
        assert_eq!(fragment("#bla-blub"), Ok(("", "bla-blub")));
    }

```

## 让我们把它们放在一个顶级的`uri`解析器函数中，这个函数使用了我们上面定义的所有部分:

我们有一个强制的`scheme`，接着是可选的`authority`，接着是强制的`ip or host`。然后我们有可选的`port`、`path`、`query parameters`和一个`fragment`。

```
pub fn uri(input: &str) -> Res<&str, URI> {
    context(
        "uri",
        tuple((
            scheme,
            opt(authority),
            ip_or_host,
            opt(port),
            opt(path),
            opt(query_params),
            opt(fragment),
        )),
    )(input)
    .map(|(next_input, res)| {
        let (scheme, authority, host, port, path, query, fragment) = res;
        (
            next_input,
            URI {
                scheme,
                authority,
                host,
                port,
                path,
                query,
                fragment,
            },
        )
    })
}

```

在映射器中，剩下的唯一事情就是从解析的元素中创建我们的`URI`结构。

这就是你可以看到整个结构有多好和模块化的地方。如果`uri`函数是您的起点，您可以从上到下查看每个单独的解析器，以了解整个事情在做什么。

当然，我们还需要对`uri`进行一些测试:

有用！你可以在 [GitHub](https://github.com/zupzup/rust-nom-parsing) 找到完整的示例代码。

```
    #[test]
    fn test_uri() {
        assert_eq!(
            uri("https://www.zupzup.org/about/"),
            Ok((
                "",
                URI {
                    scheme: Scheme::HTTPS,
                    authority: None,
                    host: Host::HOST("www.zupzup.org".to_string()),
                    port: None,
                    path: Some(vec!["about"]),
                    query: None,
                    fragment: None
                }
            ))
        );

        assert_eq!(
            uri("http://localhost"),
            Ok((
                "",
                URI {
                    scheme: Scheme::HTTP,
                    authority: None,
                    host: Host::HOST("localhost".to_string()),
                    port: None,
                    path: None,
                    query: None,
                    fragment: None
                }
            ))
        );

        assert_eq!(
            uri("https://www.zupzup.org:443/about/?someVal=5#anchor"),
            Ok((
                "",
                URI {
                    scheme: Scheme::HTTPS,
                    authority: None,
                    host: Host::HOST("www.zupzup.org".to_string()),
                    port: Some(443),
                    path: Some(vec!["about"]),
                    query: Some(vec![("someVal", "5")]),
                    fragment: Some("anchor")
                }
            ))
        );

        assert_eq!(
            uri("http://user:[email protected]:8080"),
            Ok((
                "",
                URI {
                    scheme: Scheme::HTTP,
                    authority: Some(("user", Some("pw"))),
                    host: Host::IP([127, 0, 0, 1]),
                    port: Some(8080),
                    path: None,
                    query: None,
                    fragment: None
                }
            ))
        );
    }

```

结论

## 多好的旅程啊！我希望这篇文章让您对 Rust 中的解析器，尤其是解析器组合子感到兴奋。

这个库，除了快得离谱，是许多生产级库和系统的基础之外，还提供了一个很棒的 API 和一些非常好的文档。

Rust 生态系统提供了更多解析选项，比如 [`combine`](https://github.com/Marwes/combine) 库，它也实现了解析器组合子，还有 [`pest`](https://github.com/pest-parser/pest) 。

[log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

## 调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。

Modernize how you debug your Rust apps — [start monitoring for free](https://lp.logrocket.com/blg/rust-signup).