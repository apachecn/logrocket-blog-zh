# Rust 中基于属性的测试

> 原文：<https://blog.logrocket.com/property-based-testing-in-rust-with-proptest/>

软件测试是行业标准的实践，但是测试方法和技术在实用性和有效性上有很大的不同。

今天你将学习基于属性的测试(PBT ),包括它如何工作，什么时候有意义，以及如何在 Rust 中进行。

## 什么是基于属性的测试？

为了说明 PBT 是如何工作的，考虑一个基本的单元测试。假设你写了一个函数，`maybe_works`，你想和一个你知道能正常工作的函数进行比较，比如，`definitely_works`。对于某些输入(`input`)，比较这两个函数的单元测试如下所示。

```
fn test_maybe_works() {
    let input = ...;
    assert_eq!(maybe_works(input), definitely_works(input));
}

```

这个测试很容易编写，但是需要注意一些问题。首先，您需要确切地知道在您的测试中使用哪些输入来提醒您存在错误。如果您想要针对各种输入来测试`maybe_works`和`definitely_works`，这将变得很乏味。

根据`definitely_works`测试`maybe_works`的更有效的方法是使用各种随机生成的输入运行测试几次。对于这些随机生成的输入，您不知道提供给`maybe_works`或`definitely_works`的精确值，但您通常可以做一个概括性的陈述，比如“给定相同的输入，`maybe_works`和`definitely_works`的输出应该相同”。实际上，随机生成的输入很少是真正的随机。您通常以某种方式约束输入，这样您就可以将某段代码作为目标，并避免假阳性或假阴性。

然而，通常没有像`definitely_works`这样的参考实现。在这种情况下，您需要更加努力、更加抽象地思考代码的属性(因此，基于属性的测试)以及如何验证它们。

使用 PBT 的一种方式是作为一段代码设计的指南。在本教程中，我们将使用 PBT 来帮助我们编写一个解析器。

## 入门指南

创建一个名为`sentence-parser`的新`cargo`项目。

```
$ cargo new --lib sentence-parser

```

您将使用 [`pest`](https://github.com/pest-parser/pest) 箱来编写解析器，所以将`pest`添加到您的`Cargo.toml`中。

```
d[dependencies]
pest = "~2.1"
pest_derive = "~2.1"

```

`pest` crate 从一个用户定义的语法文件中生成一个解析器，所以创建一个名为`sentence.pest`的文件，并把它放在`src`目录中。将以下内容粘贴到文件中。

```
word = { ASCII_ALPHA+ }
WHITESPACE = _{" "}

```

这个箱子不是本教程的重点，但是粗略地理解一下这个语法文件是有帮助的。文件中的每个规则告诉`pest`如何解析特定类型的输入。您可以在其他规则的定义中使用规则，最终构建理解复杂输入的解析器。有关语法的详细概述，请参见`pest` [语法指南](https://pest.rs/book/grammars/syntax.html)。

为了简单起见，我们将大大放宽“单词”和“句子”的定义我们将“单词”定义为一个或多个 ASCII 字母字符的任意序列。这意味着`flkjasdfAQTCcs`对于我们的目的来说是一个有效的词。

要从这个文件创建一个解析器，将下面的代码片段添加到`lib.rs`。

```
#[macro_use] extern crate pest_derive;
use pest::Parser;

#[derive(Parser)]
#[grammar = "sentence.pest"]
pub struct SentenceParser;

```

`#[derive(Parser)]`和`#[grammar =` `"sentence.pest"]`属性将读取语法文件并基于该语法生成一个解析器。这还将生成一个名为`Rule`的枚举，它为语法文件中的每个规则都提供了一个变量。当您想要解析一个字符串的内容时，您可以将一个`Rule`变量和字符串一起传递给`SentenceParser::parse`。我们将使用这个特性来测试语法中的单个规则，而不是一次测试全部规则。

最后，把`proptest`加到你的`Cargo.toml`上。

```
[dev-dependencies]
proptest = "~0.9"

```

## 使用`proptest`

用`proptest`做 PBT 看起来和写普通测试差不多。有两个主要的区别，我将用下面的片段来说明。

```
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn a_normal_test() {
        ...
        assert!(...)
    }

    proptest!{
        #[test]
        fn a_property_based_test(foo in some_strategy()) {
            ...
            prop_assert!(...)
        }
    }
}

```

首先要注意的是，您可以将 PBT 与普通的单元测试混合使用；PBT 不是全有或全无。接下来，基于属性的测试必须包装在`proptest!`宏中。

另外需要注意的一点是用`prop_assert!`代替了`assert!`。`proptest`测试运行者将试图通过用越来越简单的输入导致测试失败来找到最小的失败输入。使用`prop_assert!`和`prop_assert_eq!`宏将导致测试失败信息(紧急信息)仅针对最小的失败输入打印，而不是针对单个测试多次打印。

最后，请注意基于属性的测试的函数参数的形式是`argname in strategy`。

从概念上讲，“策略”是一种生成类型实例的方式，可能带有一些约束。更具体地说，策略是实现`Strategy`特征的类型。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

此外，实现`Arbitrary`特征定义了类型的默认或主要策略。这个`Arbitrary`实现通常是为该类型生成最大范围值的策略。由`proptest`提供的`any<T>()`函数使用这个`Arbitrary`实现来生成真正任意的`T`类型的实例。如果您想生成受某些约束的实例，您需要定义返回`impl Strategy`的函数。

`proptest`箱为各种 Rust 原语和集合提供了`Arbitrary`的实现。为您自己的类型实现`Arbitrary`非常简单。下面的例子大部分是样板文件。

```
impl Arbitrary for T {
    type Parameters = ();
    type Strategy = BoxedStrategy<Self>;

    fn arbitrary_with(_: Self::Parameters) -> Self::Strategy {
        ...
    }
}

```

唯一真正需要您思考的部分是`…`，它定义了如何生成值。您可以使用`Parameters`类型定义来参数化策略如何生成值。在大多数情况下，这是不必要的，所以将其定义为`()`。`arbitrary_with`返回的策略的具体类型由`Strategy`类型定义定义。您可以声明一个特定的类型，或者选择最简单的路径，通过`BoxedStrategy`类型返回一个 trait 对象。

由`Arbitrary`特征定义的策略可能会生成对某些场景来说过于通用的值，或者您可能只是想为一个给定的类型定义多个策略。无论哪种情况，您都希望编写一个返回策略的函数。

```
fn my_strategy() -> impl Strategy<Value = T> {
    ...
}

```

以上两种方法共享一些由`…`表示的公共模式。`proptest`一书中的“[转变策略](https://altsysrq.github.io/proptest-book/proptest/tutorial/transforming-strategies.html)一节详细说明了这是如何实现的，但我们将简要讨论一下，因为在使用`proptest`时这是一种常见的做法。

### 战略

这个`proptest`机箱有几个内置的策略——即`any<T>`、`proptest::collection`模块、`proptest::string::string_regex`以及其他各种各样的策略。

此外，策略的元组和数组本身就是策略。这意味着您可以生成一个值元组，其中每个值来自不同的策略，然后应用来自`Strategy`特征的方法，并将元组的元素转换为另一种类型。

### `Strategy::prop_map`

`prop_map`方法允许您将策略生成的值转换成新的类型。这是从基本类型或其他类型构建自定义类型的主要方法之一。

下面的示例说明了如何通过转换基元类型元组来生成结构。

```
struct Point {
    x: f32,
    y: f32
}

fn point_strat() -> impl Strategy<Value = Point> {
    (any<f32>::(), any<f32>::()).prop_map(|(x, y)| {
        Point {
            x: x,
            y: y
        }
    })
}

```

简而言之，一个策略产生一个随机值，而`prop_map`使用这个随机值来计算一个不同的值。

### `Strategy::prop_filter`

`prop_filter`方法允许您通过用谓词过滤来约束由一个策略产生的值。下面的函数返回一个生成大于 100 的`u8`的策略。

```
fn u8_greater_than_100() -> impl Strategy<Value = u8> {
    any::<u8>().prop_filter("reason for filtering", |x| x > &100u8)
}

```

### `Strategy::prop_flat_map`

有时，您希望生成以某种方式相互依赖的值。这就是`prop_flat_map`方法发挥作用的地方。当对一个策略调用时，`prop_flat_map`方法从该策略中获取一个随机值，并使用它来产生另一个策略。

注意`prop_map`和`prop_flat_map`的区别。使用`prop_map`，来自一个策略的值被用来产生一个新值，而使用`prop_flat_map`，来自一个策略的值被用来产生一个新策略。

来自`proptest`文档的典型用例是一个策略，它产生一个`Vec`和一个进入该`Vec`的随机有效索引。

```
fn vec_and_index() -> impl Strategy<Value = (Vec<String>, usize)> {
    prop::collection::vec(".*", 1..100)
        .prop_flat_map(|vec| {
            let len = vec.len();
            (Just(vec), 0..len)
        })
}

```

### `Just`

这种类型创建一个总是返回相同值的策略。上面的`prop_flat_map`示例中显示了一个用例。另一个用例是使用`prop_oneof!`宏。

### `prop_oneof!`

该宏创建一个策略，该策略从策略列表中产生值。最常见的用例是生成枚举变量。

```
enum MyEnum {
    Foo,
    Bar,
    Baz,
}

fn myenum_strategy() -> impl Strategy<Value = MyEnum> {
    prop_oneof![
        Just(MyEnum::Foo),
        Just(MyEnum::Bar),
        Just(MyEnum::Baz),
    ]
}

```

请注意，您必须提供策略列表，而不是值列表。这就是为什么您必须在`Just`中包装枚举变量。

## 编写解析器

现在您已经知道了一些常见的`proptest`模式，您可以开始编写和测试解析器了。

### `word`

在`test`模块中创建一个`proptest!`模块。首先，创建一个名为`valid_word`的函数，它返回一个策略，该策略产生一个有效的单词。

```
#[cfg(test)]
mod test {
    use super::*;

    proptest!{
        fn valid_word() -> impl Strategy<Value = String> {
            proptest::string::string_regex("[a-zA-Z]+").unwrap()
        }

        #[test]
        fn parses_valid_word(w in valid_word()) {
            let parsed = SentenceParser::parse(Rule::word, w.as_str());
            prop_assert!(parsed.is_ok());
        }
    }
}

```

`proptest::string::string_regex`函数返回一个策略，该策略产生与提供的正则表达式匹配的字符串。在这种情况下，这是一个或多个 ASCII 字母的序列。

您可以使用简写`w in` `[a-zA-Z]+`来完成同样的事情，但是我发现将策略写成一个函数可以让您给正则表达式起一个描述性的名字。注意，在`_ in valid_word()`中，`_`定义了策略产生的值的名称，这样您就可以在测试主体中使用它。

测试试图使用规则`Rule::word`解析生成的单词。如果您运行`cargo test`，您应该看到您的测试通过了(以及其他编译语句)。

在输入有效输入时通过的测试很重要，但是在输入无效输入时检测到错误的测试同样重要。这一次，您将创建一个测试，向解析器提供非字母字符以及长度为零的字符串。

创建另一个测试，如下所示:

```
fn invalid_word() -> impl Strategy<Value = String> {
    proptest::string::string_regex("[^a-zA-Z]*").unwrap()
}

#[test]
fn rejects_invalid_word(w in invalid_word()) {
    let parsed = SentenceParser::parse(Rule::word, w.as_str());
    prop_assert!(parsed.is_err());
}

```

再次运行测试套件，看看测试是否通过。

### `words`

现在创建一个类似下面的`words`规则:

```
words = { word+ }

```

该规则将匹配由空格连接的`word`序列。

我们将遵循与之前相同的模式来测试我们是否创建了一个好的规则。首先，创建一个试图解析有效输入的测试。这次，我们将使用`Rule::words`而不是`Rule::word`。

```
fn words() -> impl Strategy<Value = String> {
    proptest::string::string_regex("[a-z]+( [a-z]+)*").unwrap()
}

#[test]
fn parses_valid_words(ws in words()) {
    let parsed = SentenceParser::parse(Rule::words, ws.as_str());
    prop_assert!(parsed.is_ok());
}

```

运行测试以确保一切正常。

接下来，我们将创建一个试图解析无效输入的测试。但是首先我们需要决定无效输入应该是什么样子。

我们已经知道我们可以正确地解析一个`word`，所以让我们测试一下`words`规则是否可以解析零个`word`以及它是否可以解析由字符而不是空格分隔的`word`。

您不需要对空字符串进行基于属性的测试，所以继续在`test`模块内，但在`proptest!`块外创建下面的测试。

```
#[test]
fn words_rejects_empty_string() {
    let parsed = SentenceParser::parse(Rule::words, "");
    assert!(parsed.is_err());
}

```

剩下的工作就是测试当字符不仅仅是在`word`之间有一个空格时会发生什么，比如`\t`、`\r`和`\n`。不幸的是，像这样提供输入字符串并期望解析失败是行不通的。

考虑字符串`"a\tb"`。问题是`\t`不是一个可以出现在`word`中的有效字符，所以解析器在`\t`停止消耗字符，只消耗了`a`。但是，`a`匹配`word`，T6 匹配`words`(“至少一个`word`”)。因此，我们的包含无效单词分隔符的字符串仍然成功地匹配了`words`规则，因为`words`只是匹配了输入的一部分。

我们可以通过改变我们的规则来纠正这一点，解析到输入的末尾，而不是解析成功的输入的某个最小前缀。

```
words = { word+ ~ EOI }

```

`EOI`规则由`pest`提供，代表“输入结束”这暂时解决了我们的问题，但是当我们以后试图解析整个句子时，它会破坏事情。

现在，我们将保持`words`规则不变，并放弃向它提供这种无效输入。

### `enclosed`

现在，我们将通过创建一个规则`enclosed`来构建`words`规则，该规则简单地将一个`words`包装在某种分隔符中，比如逗号或括号。

```
enclosed = ${
    "(" ~ words ~ ")" |
    ", " ~ words ~ ","
}

```

注意，这条规则以`$`开始，这表明我们不希望`~`隐式地吞噬空白。我们希望这样，因为`(foo bar)`有效，但`( foo bar )`无效。

为了测试这条规则，我们将从将要定义的策略中生成更复杂的输入。

首先，我们将创建一个策略，生成一个可以被`enclosed`规则解析的字符串。为此，我们需要一种方法来生成匹配的分隔符。

在您的`tests`模块中，创建以下类型。

```
#[derive(Debug, PartialEq, Clone)]
enum EnclosedDelimiter {
    OpenParen,
    CloseParen,
    OpenComma,
    CloseComma,
}

```

接下来，创建一个将各种枚举变量转换成`&str`的方法。

```
impl EnclosedDelimiter {
    fn to_str(&self) -> &str {
        match self {
            EnclosedDelimiter::OpenParen => "(",
            EnclosedDelimiter::CloseParen => ")",
            EnclosedDelimiter::OpenComma => ", ",
            EnclosedDelimiter::CloseComma => ","
        }
    }
}

```

现在创建一个名为`opening_delimiter`的函数。该函数将只生成有效的开始分隔符。

```
fn opening_delimiter() -> impl Strategy<Value = EnclosedDelimiter> {
    prop_oneof![
        Just(EnclosedDelimiter::OpenParen),
        Just(EnclosedDelimiter::OpenComma)
    ].boxed()
}

```

创建一个名为`valid_enclosed`的函数。该函数将返回一个策略，该策略只生成字符串，通过将随机选择的有效开始分隔符与其匹配的结束分隔符进行匹配，`enclosed`规则可以成功地解析这些字符串。这是`prop_flat_map`方法派上用场的一个例子。

```
fn valid_enclosed() -> impl Strategy<Value = String> {
    let words_strat = proptest::string::string_regex("[a-z]+( [a-z]+)*").unwrap();
    (opening_delimiter(), words_strat).prop_flat_map(|(open, words)| {
        let close = match open {
            EnclosedDelimiter::OpenParen => Just(EnclosedDelimiter::CloseParen),
            EnclosedDelimiter::OpenComma => Just(EnclosedDelimiter::CloseComma),
            _ => unreachable!()
        };
        (Just(open), close, Just(words))
    }).prop_map(|(open, close, words)| {
        let mut enclosed = String::new();
        enclosed.push_str(open.to_str());
        enclosed.push_str(words.as_str());
        enclosed.push_str(close.to_str());
        enclosed
    })
}

```

该策略以开始分隔符和有效的单词序列开始，然后使用`prop_flat_map`选择有效的结束分隔符。一旦分隔符和单词被生成，它们就被组合成一个`String`。包含`_ => unreachable!()`行是因为`match`必须是详尽的，我们知道`opening_delimiter`不能生成任何一个结束分隔符。

编写`parses_valid_enclosed`测试以确保它能够工作。

```
#[test]
fn parses_valid_enclosed(enc in valid_enclosed()) {
    let parsed = SentenceParser::parse(Rule::enclosed, enc.as_str());
    prop_assert!(parsed.is_ok());
}

```

哦，看起来这个测试失败了。这种情况下的最小失败输入是`(a a)`。除非你熟悉`pest`，否则原因可能并不明显，但它与空白的处理方式有关。

记住`enclosed`规则是从`$`开始的。以`@`或`$`开头的规则被称为“原子的”,不会隐式地消耗标记之间的空白，这个属性级联到当前规则中的子规则。

在这种情况下，`enclosed`引用了`words`规则，这改变了`words`在`enclosed`规则的上下文中匹配空白的方式。我们所需要做的就是在`words`规则前面放一个`!`来防止这种级联。

新规则应该是这样的:

```
word = { ASCII_ALPHA+ }
words = !{ word+ }
enclosed = ${
    "(" ~ words ~ ")" |
    ", " ~ words ~ ","
}
WHITESPACE = _{" "}

```

现在测试应该都通过了。

下一个任务是为`enclosed`规则生成无效字符串。开始分隔符可以是任何分隔符，结束分隔符将被明确地选择为不与开始分隔符匹配。

首先，实现`Arbitrary`特征，这样就可以生成随机选择的`EnclosedDelimiter`实例。

```
impl Arbitrary for EnclosedDelimiter {
    type Parameters = ();
    type Strategy = BoxedStrategy<Self>;
    fn arbitrary_with(_: Self::Parameters) -> Self::Strategy {
        prop_oneof![
            Just(EnclosedDelimiter::OpenParen),
            Just(EnclosedDelimiter::CloseParen),
            Just(EnclosedDelimiter::OpenComma),
            Just(EnclosedDelimiter::CloseComma),
        ].boxed()
    }
}

```

接下来，创建故意不匹配分隔符的函数。

```
fn invalid_enclosed() -> impl Strategy<Value = String> {
    let words_strat = proptest::string::string_regex("[a-z]+( [a-z]+)*").unwrap();
    (any::<EnclosedDelimiter>(), words_strat).prop_flat_map(|(open, words)| {
        let close = match open {
            EnclosedDelimiter::OpenParen => prop_oneof![
                Just(EnclosedDelimiter::OpenParen),
                Just(EnclosedDelimiter::OpenComma),
                Just(EnclosedDelimiter::CloseComma)
            ].boxed(),
            EnclosedDelimiter::CloseParen => any::<EnclosedDelimiter>(),
            EnclosedDelimiter::OpenComma => prop_oneof![
                Just(EnclosedDelimiter::OpenParen),
                Just(EnclosedDelimiter::CloseParen),
                // Just(EnclosedDelimiter::OpenComma),
            ].boxed(),
            EnclosedDelimiter::CloseComma => any::<EnclosedDelimiter>(),
        };
        (Just(open), close, Just(words))
    }).prop_map(|(open, close, words)| {
        let mut enc = String::new();
        enc.push_str(open.to_str());
        enc.push_str(words.as_str());
        enc.push_str(close.to_str());
        enc
    })
}

```

您会注意到其中一行被注释掉了。如果取消对这一行的注释，您将看到测试失败，因为它成功地解析了应该是无效的输入。

`OpenComma`变量的字符串表示以空格结尾，因此解析器将`foo,`视为`foo`，后面是`CloseComma`，再后面是。这意味着`OpenComma` / `OpenComma`对可能会意外地被解析为`OpenComma` / `CloseComma`，并且不会产生错误。这个错误将在后面的规则中解决，所以我们将在这个规则中跳过测试。

测试看起来是这样的:

```
#[test]
fn rejects_mismatched_enclosed_delimiters(enc in invalid_enclosed()) {
    let parsed = SentenceParser::parse(Rule::enclosed, enc.as_str());
    prop_assert!(parsed.is_err());
}

```

我们可以做另一个测试，仅仅去掉结束定界符。

```
fn missing_closing_delimiter() -> impl Strategy<Value = String> {
    (any::<EnclosedDelimiter>(), words()).prop_map(|(open, ws)| {
        let mut enc = String::new();
        enc.push_str(open.to_str());
        enc.push_str(ws.as_str());
        enc
    })
}

#[test]
fn rejects_missing_closing_delimiter(enc in missing_closing_delimiter()) {
    let parsed = SentenceParser::parse(Rule::enclosed, enc.as_str());
    prop_assert!(parsed.is_err());
}

```

再次运行测试，并验证它们仍然全部通过。

### `chunk`

`words`和`enclosed`规则是相似的，因为它们都表示单词序列。为了对这种相似性进行编码，我们将制定一个与`words`或`enclosed`匹配的`chunk`规则，这样我们就可以将一个单词序列表示为一个`chunk`序列，其中一些单词可能包含在分隔符中

```
chunk = ${ words | enclosed }

```

幸运的是，我们已经为`words`和`enclosed`编写了策略，所以我们可以直接跳到为这条规则编写测试。

```
#[test]
fn parses_valid_words_chunk(ws in words()) {
    let parsed = SentenceParser::parse(Rule::chunk, ws.as_str());
    prop_assert!(parsed.is_ok());
}

#[test]
fn parses_valid_enclosed_chunk(enc in valid_enclosed()) {
    let parsed = SentenceParser::parse(Rule::chunk, enc.as_str());
    prop_assert!(parsed.is_ok());
}

#[test]
fn rejects_mismatched_delim_chunk(enc in invalid_enclosed()) {
    let parsed = SentenceParser::parse(Rule::chunk, enc.as_str());
    prop_assert!(parsed.is_err());
}

#[test]
fn rejects_missing_delim_chunk(enc in missing_closing_delimiter()) {
    let parsed = SentenceParser::parse(Rule::chunk, enc.as_str());
    prop_assert!(parsed.is_err());
}

```

运行测试以确保它们都通过。

### `punctuation`

由于句子以标点符号结尾，所以让我们创建一个匹配标点符号的规则。

```
punctuation = { "." | "!" | "?" }

```

这是一个相对简单的规则，所以策略也很简单。有效的标点符号可以是`.`、`!`或`?`，其他任何字符都是无效的标点符号。

```
fn punctuation() -> impl Strategy<Value = String> {
    prop_oneof![
        Just(String::from(".")),
        Just(String::from("?")),
        Just(String::from("!")),
    ]
}

fn invalid_punctuation() -> impl Strategy<Value = String> {
    proptest::string::string_regex("[^\\.\\?!]").unwrap()
}

#[test]
fn parses_valid_punctuation(s in punctuation()) {
    let parsed = SentenceParser::parse(Rule::punctuation, s.as_str());
    prop_assert!(parsed.is_ok());
}

#[test]
fn rejects_invalid_punctuation(s in invalid_punctuation()) {
    let parsed = SentenceParser::parse(Rule::punctuation, s.as_str());
    prop_assert!(parsed.is_err());
}

```

这些测试应该会顺利通过。

### `sentence`

终于到了将所有这些整合成一条`sentence`规则的时候了。

```
sentence = ${ SOI ~ chunk+ ~ punctuation ~ EOI }

```

这个规则中的`SOI`和`EOI`确保整个输入都被解析，所以像`a b c.def`这样的输入不是有效的句子。

我们将从最基本类型的有效句子开始:一个或多个`chunk`后跟一个有效的`punctuation`。

首先，编写一个产生由空格连接的一系列`chunk`的策略。

```
fn chunks() -> impl Strategy<Value = String> {
    proptest::collection::vec(chunk(), 1..10).prop_map(|cs| {
        cs.join(" ")
    })
}

```

现在用这个`chunks`策略做一个`valid_sentence`策略。

```
fn valid_sentence() -> impl Strategy<Value = String> {
    (chunks(), punctuation()).prop_map(|(cs, p)| {
        let mut sentence = String::new();
        sentence.push_str(cs.as_str());
        sentence.push_str(p.as_str());
        sentence
    })
}

```

编写一个测试，试图解析一个有效的句子。

```
#[test]
fn parses_valid_sentence(s in valid_sentence()) {
    let parsed = SentenceParser::parse(Rule::sentence, s.as_str());
    prop_assert!(parsed.is_ok());
}

```

运行测试以确保它—等一下，这个测试失败了！

注意，最小的失败输入`"(a) a."`是一个有效的句子(根据我们的定义)。这意味着我们一定错误地告诉了解析器如何解析一个句子。

经过一点调查，您会记得我们的`sentence`规则是原子的(即，它从`$`开始)。

```
sentence = ${ SOI ~ chunk+ ~ punctuation ~ EOI }

```

提醒一下，这意味着规则之间的空白没有被使用。当我们的解析器试图解析`"(a) a."`时，它真正看到的是`<chunk><whitespace><chunk><punctuation>`。那个`<whitespace>`不匹配`sentence`规则的`chunk+`，所以解析失败。

为了解决这个问题，我们需要手动处理重复出现的空白。

```
words = ${ word ~ (" " ~ word)* }
sentence = ${ SOI ~ chunk ~ (" " ~ chunk)* ~ punctuation ~ EOI }

```

现在测试应该通过了。

我们还可以编写一些其他的测试，所有这些都应该可以通过当前的规则。

```
#[test]
fn rejects_missing_punctuation(s in chunks()) {
    let parsed = SentenceParser::parse(Rule::sentence, s.as_str());
    prop_assert!(parsed.is_err());
}

#[test]
fn rejects_trailing_characters(s in valid_sentence(), t in "[\\sa-zA-Z]+") {
    let input = [s.as_str(), t.as_str()].join(" ");
    let parsed = SentenceParser::parse(Rule::sentence, input.as_str());
    prop_assert!(parsed.is_err());
}

#[test]
fn rejects_missing_space_between_chunks(
    w in words(),
    enc in valid_enclosed(),
    p in punctuation()
) {
    let input = [w, enc, p].join("");
    let parsed = SentenceParser::parse(Rule::sentence, input.as_str());
    prop_assert!(parsed.is_err());
}

```

## 结论

至此，您应该对如何编写基于属性的测试有了初步的了解。PBT 并不总是答案，但是思考代码的抽象属性这一简单行为可以帮助您更好地理解它。

本教程中使用的代码可以在 [GitHub](https://github.com/zmitchell/sentence-parser) 上获得。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。