# 使用 Pest 和 PEG 构建 Rust 解析器

> 原文：<https://blog.logrocket.com/building-rust-parser-pest-peg/>

编写一个高效的词法分析器-解析器对来解析复杂的结构是一个挑战。如果格式或结构是固定的，并且您必须以一种易于理解、维护和扩展以适应未来变化的方式编写解析器，那么这将变得更加复杂。

在这些情况下，我们可以使用解析器生成器，而不是手写解析器或者手动解析我们的项目。在本文中，我们将回顾什么是解析器生成器，并探索一个名为 Pest 的 Rust 解析工具。我们将涵盖:

请注意，您应该能够熟练地读写基本的 Rust 代码，如函数、结构和循环。

## 什么是解析器生成器？

解析器生成器是一些程序，它们接受解析器需要考虑的规则，然后以编程方式为您生成一个解析器，该解析器将基于这些规则解析输入。

大多数情况下，规则是以简化的语言提供给解析器生成器的，比如正则表达式。因此，当您想要通过更改规则或添加新规则来更新解析器时，您只需更新或添加规则的正则表达式。然后，当您运行解析器生成器时，它将重写解析器以适应这些规则。

你可以想象使用这样的解析工具可以节省多少时间。许多解析器生成器也会生成一个 lexer，这样您就不必自己编写了。如果生成的词法分析器不适合您，如果需要，您可以选择用自己的词法分析器运行解析器。

目前，Rust 生态系统中有几个解析器生成器可供您使用。其中最流行的三个是 LalrPop、Nom 和 Pest。

在定义规则和相应动作的方式上，LalrPop 与 Yacc 非常相似。我曾亲自用它为我的 8086 模拟器项目[编写规则。](https://yjdoc2.github.io/8086-emulator-web/)

Nom 是一个解析器组合子库，在其中你将规则写成函数的组合。这更倾向于解析二进制输入，但也可以用于解析字符串。

最后， [Pest 使用解析表达式语法](https://crates.io/crates/pest)来定义解析规则。在本帖中，我们将详细探讨用 Pest 解析 Rust。

## Pest 中解析表达式语法是什么？

解析表达式语法(PEG)是在 Rust with Pest 中定义我们解析“规则”的方法之一。Pest 接受具有这种规则定义的文件的输入，并生成一个遵循这些规则定义的 Rust 解析器。

在编写规则时，您应该考虑 Pest 和 PEG 的三个定义特征。

第一个特点是贪婪匹配。Pest 将总是尝试将输入的最大值与规则匹配。例如，假设我们编写了如下规则:

```
match one or more alphabets

```

在这种情况下，Pest 将消耗输入中的所有内容，直到它到达一个数字、空格或符号。在那之前它不会停止。

要考虑的第二个特征是交替匹配是有序的。为了理解这意味着什么，假设我们给出了多个匹配来满足一个规则，就像这样:

```
rule1 | rule2 | rule3

```

害虫将首先尝试匹配`rule1`。当且仅当`rule1`失败时，Pest 将尝试匹配`rule2`，以此类推。如果第一个规则匹配，Pest 不会尝试匹配任何其他规则来找到最佳匹配。

因此，当编写这样的替代方案时，我们必须将最具体的替代方案放在第一位，将最一般的放在最后。这将在下一节详细解释。

第三个特征——无回溯——意味着如果一个规则不匹配，解析器不会回溯。相反，Pest 会尝试寻找更好的规则或选择最佳替代匹配。

这与使用普通正则表达式或其他类型的解析器不同，后者可以返回几个标记，即使没有给出替代选择，也可以尝试找到替代规则。

您可以在有害生物文档中更详细地了解这些和其他特征[。](https://pest.rs/book/grammars/peg.html)

## 使用 Pest 中的 PEG 为 Rust 解析器声明规则

在 Pest 中，我们使用一种有点类似于正则表达式的语法来定义规则，但有一些不同。让我们通过写出一些例子来看看实际的语法。

任何规则的基本语法如下:

```
RULE_NAME = { rule definition }

```

花括号前面可以有符号，如`_`和`@`。我们将在后面看到这些符号的含义。

### 使用 Pest 中的内置规则匹配单个字符

Pest 有一些匹配某些字符或字符集的内置规则。

引号中的任何字符或字符串都与其自身匹配。例如，`"a"`将匹配`a`字符，`"true"`将匹配字符串`true`，以此类推。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

`ANY`是匹配任何 Unicode 字符的规则，包括空格、换行符和逗号、分号等符号。

`ASCII_DIGIT`将匹配一个数字或数字字符——换句话说，匹配下列字符中的任何一个:

```
0123456789

```

`ASCII_ALPHA`将匹配小写和大写字母字符——换句话说，是下列字符中的任何一个:

```
abcdefghijklmnopqrstuvwxyz
ABCDEFGHIJKLMNOPQRSTUVWXYZ

```

您可以使用`ASCII_ALPHA_LOWER`和`ASCII_ALPHA_UPPER`来分别匹配大写和小写字符。

最后，`NEWLINE`将匹配代表换行符的控制字符，如`\n`、`\n\r`或`\r`。

害虫文档中[解释了其他几个内置规则，但是对于我们将要构建的小程序，上述规则应该足够了。](https://pest.rs/book/grammars/built-ins.html)

请注意，上述所有规则都只匹配同类型的单个字符，而不是多个字符。例如，对于输入`abcde`，考虑如下规则:

```
my_rule = { ASCII_ALPHA }

```

这个规则将只匹配字符`a`，而输入的其余部分——`bcde`——将被传递以进行进一步的解析。

### 使用重复匹配多个字符

现在我们知道了如何匹配单个字符，让我们看看如何匹配多个字符。有三种重要的重复类型，我们将在下面介绍。

加号`+`表示“一个或多个”这里，Rust 解析器将尝试匹配规则的至少一个匹配项。如果出现不止一次，Pest 将匹配所有事件。如果找不到任何匹配，它将被视为一个错误。例如，我们可以这样定义一个数:

```
NUMBER = { ASCII_DIGIT+ }

```

如果存在非数字字符，如字母字符或符号，此规则将给出错误。

星号`*`表示“零或更多”当我们希望允许多次出现，但即使没有错误也不给出任何错误时，这很有用。例如，在定义列表时，第一个值后面可以有零个或多个逗号值对。您现在可以忽略下面代码中的波浪号`~`,因为我们将在下一节看到它的含义:

```
LIST = { "[" ~ NUMBER ~ ("," ~ NUMBER)* "]" }

```

上面的规则表明，一个列表以一个`[`开始，然后是一个`NUMBER`，在此之后可以有零对或更多对`, number`组。这些在括号中组合在一起，并在整个括号组后标有星号`*`。最后，列表以一个右括号`]`结束。

这个规则将匹配`[0]`、`[1,2]`、`[1,2,3]`，以此类推。然而，它将在`1`失败，因为没有左括号`[`存在，并且在`[1`失败，因为没有右括号`]`存在。

最后，一个问号`?`匹配零或一个，但不超过一个。例如，为了在上面的列表中允许一个尾随逗号，我们可以将它声明如下:

```
LIST = { "[" ~ NUMBER ~ ("," ~ NUMBER)* ","? "]" }

```

这一改变将允许`[1]`和`[1,]`。

除了这三个选项，还有其他方法来指定重复，包括允许重复固定次数，或最多`n`次，或在`n`和`m`次之间。这些细节可以在害虫文档中找到[，但是以上三个例子就足够了。](https://pest.rs/book/grammars/syntax.html#repetition)

### 序列的隐式匹配

我们在上一节中看到的波浪号`~`用于表示一个序列。例如，`A ~ B ~ C`翻译成，“匹配规则 A，然后匹配规则 B，然后匹配规则 c。”

使用显式的`~`来表示这个序列是可行的，因为在符号`~`周围有一些隐式的空白匹配。这是因为在许多情况下，编码规则和语法会忽略空白。比如`if ( true)`和`if( true )`、`if (true )`一样。

因此，不需要开发人员在每个地方手动检查，有害程序生成的解析器会自动为我们完成这项工作。这种隐式匹配的具体细节将在后面的章节中解释。

### 表达替代选择

有时，您可能希望表达多个允许与定义的规则相匹配的规则。例如，在 JSON 中，值可以是字符串、数组或对象，等等。

对于这样的“或”场景，我们可以使用竖线`|`符号来表达替代选择的想法。上面的 JSON 概念可以写成这样的规则:

```
VALUE = { STRING | ARRAY | OBJECT }

```

注意，如上所述，在解释 PEG 特征时，这些选择是在首次匹配的基础上做出的。因此，考虑如下规则:

```
RULE = { "cat" | "cataract" | "catastrophe" }

```

这条规则永远不会匹配`cataract`和`catastrophe`，因为解析器会将两者的起始`cat-`部分匹配到第一条规则`cat`，然后它不会尝试匹配任何更多的字母。在匹配了`cat`之后，解析器将把剩余的输入——`-aract`和`-astrophe`——传递给下一步，在下一步中，它可能什么都不匹配，导致解析失败。

为了确保竖线`|`有预期的效果，记住总是在开始指定最具体的规则，在最后指定最通用的规则。例如，正确的表达方式如下:

```
RULE = { "catastrophe" | "cataract" | "cat" }

```

这里，`cataract`和`catastrophe`的顺序无关紧要，因为它们不是彼此的子串，匹配其中一个的输入不会匹配另一个。让我们快速看一下可能发生这种情况的示例案例:

```
all_queues = { "queue" | "que" | "q" }

```

匹配`queue`及其变体`que`和`q`时使用的顺序在这里很重要，因为后面的字符串是第一个字符串的子字符串。解析器将首先尝试将输入匹配到`queue`；只有当它失败时，解析器才会尝试将它与`que`匹配。如果同样失败，解析器将最终尝试匹配`q`。

### 理解无声、原子和间隔

现在让我们看看下划线`_`和 at `@`符号是什么意思。

在由 Pest 生成的解析器中，每个匹配的规则产生一个由匹配的输入和匹配的规则组成的`Pair`。然而，有时，我们可能想要匹配一些规则以确保语法被遵循，但是另外想要忽略那些规则的内容，因为它们不重要。

在这些情况下，我们可以通过在规则定义中的左花括号`{`前加一个下划线`_`来表示该规则是无声的，而不是产生我们在代码中忽略的标记。

这种策略最常见的用例是忽略评论。尽管我们希望注释遵循语法约定——例如，它们必须以`//`开头，以换行符结尾——但我们不希望它们在处理过程中出现。因此，为了忽略注释，我们可以这样定义它们:

```
comments = _{ "//" ~ (!NEWLINE ~ ANY)* ~ NEWLINE }

```

这将尝试匹配任何评论。如果有任何语法错误——例如，如果注释只以一个`/`开头——就会产生一个错误。但是，如果注释匹配成功，解析器不会为这个规则生成对。

理论上，评论可以出现在任何地方。因此，我们需要在每条规则的末尾加上一个`comments?`,这将是非常繁琐的。

为了解决这个问题，Pest 提供了两个固定的规则名称——`COMMENT`或`WHITESPACE`——生成的解析器将自动允许这些标注的注释或空格存在于输入中的任何地方。如果我们定义了这些规则中的任何一个，那么生成的解析器将会在每一个蒂尔达`~`和所有重复处隐式地检查它们。

另一方面，我们并不总是想要这种行为。例如，当我们编写需要有特定数量空白的规则时，我们不能让这些隐含的规则消耗掉这些空间。

作为一种变通方法，我们可以定义原子规则——它不执行隐式匹配——在定义规则时，在左花括号`{`前添加 at `@`或 dollar `$`符号。`@`使规则原子化和沉默，而`$`将使规则原子化并像其他规则一样产生令牌。

### 内置输入规则的开始和结束

`SOI`和`EOI`是两个特殊的内置规则，它们不匹配任何东西，而是表示输入的开始和结束。当您想要确保整个输入被解析而不仅仅是它的一部分时，或者允许在规则的开始有空白和注释时，这些是很有用的。

以上应该涵盖了编写 PEG 规则的重要基础知识，如果您需要的话，完整的细节可以在有害程序文档中找到。现在让我们用我们所学的知识来构建一个示例解析器！

## 用 Pest 演示一个简单的解析器

用 Pest 构建一个解析器使您能够定义和解析任何语法，包括 Rust。在我们的示例项目中，我们将构建一个解析器来解析 HTML 文档的简化版本，以给出与标签和属性相关的信息，以及实际的文本。

我们的例子使用 HTML，因为它的语法是众所周知和理解的，不需要大量的解释，但也提供了足够的复杂性，我们可以显示不同的东西，我们将需要考虑的解析。学习完这些基础知识后，您可以使用相同的知识来定义和解析您选择的任何语法。

首先，创建一个新项目并添加`pest`和`pest_derive`作为依赖项。

Pest 强调将规则和我们的应用程序代码分开。因此，我们必须在一个单独的文件中定义规则，并使用 Pest crate 给出的宏在我们的代码中构建和导入解析器。为此，我们将在我们的`src`文件夹中创建一个名为`html.pest`的文件。

首先，我们将首先将`tag`定义为一个规则。标签以一个小于`<`的符号开始，紧接着是零个或一个正斜杠`/`，然后是标签名，以`>`或`/>`结束。因此，我们可以用一个问号`?`来检测可选的`/`并像这样定义规则:

```
tag = ${ "<" ~ "/"? ~ ASCII_ALPHA+ ~ "/"? ~ ">" }

```

这个规则是原子性的，因为我们不希望小于符号`<`和标签名之间有空格。目前，这也允许像`</abc/>`这样的标签——这是无效的 HTML——但是我们稍后会处理这个问题。由于标签名不能为空，我们在匹配字母时使用加号`+`。

下面我们还定义了一个`document`规则，它包含多个标签并允许它们之间有空格:

```
document = { SOI ~ tag+ ~ EOI}

```

我们使用加号`+`符号，因为文档应该至少有一个标签。

最后，我们定义了一个`WHITESPACE`规则来允许标签之间的空白和换行符:

```
WHITESPACE = _{ " " | NEWLINE }

```

为了实际构建并将其导入到我们的项目中，我们将添加以下命令:

```
use pest_derive::Parser;

#[derive(Parser)]
#[grammar = "html.pest"]
pub struct HtmlParser;

```

在上面的代码中，我们从导入派生解析器所需的宏开始。然后，我们定义了名为`HtmlParser`的解析器结构，用`#[derive(Parser)]`标记它，并为它提供文件的名称。我们使用`grammar =`指令来定义我们的规则。

要查看它当前的功能，让我们进行如下设置:

```
const HTML:&str = "<html> </html>";

fn main() {
    // parse the input using the rule 'document'
    let parse = HtmlParser::parse(Rule::document, HTML).unwrap();
    // make an iterator over the pairs in the rule
    for pair in parse.into_iter(){
        // match the rule, as the rule is an enum
        match pair.as_rule(){
            Rule::document =>{
                // for each sub-rule, print the inner contents
                for tag in pair.into_inner(){
                    println!("{:?}",tag);
                }
            }
            // as we have  parsed document, which is a top level rule, there
            // cannot be anything else
            _ => unreachable!()
        }
    }
}

```

以上将产生以下输出:

```
Pair { rule: tag, span: Span { str: "<html>", start: 0, end: 6 }, inner: [] }
Pair { rule: tag, span: Span { str: "</html>", start: 7, end: 14 }, inner: [] }
Pair { rule: EOI, span: Span { str: "", start: 14, end: 14 }, inner: [] }

```

这表明第一个匹配是一个`<html>`标记，然后是一个`</html>`标记，最后是输入的结尾。

让我们尝试添加更多的标签，看看它是否仍然有效:

```
const HTML:&str = "<html> <head> </head> <body></body> </html>";

```

结果应该是这样的:

```
Pair { rule: tag, span: Span { str: "<html>", start: 0, end: 6 }, inner: [] }
Pair { rule: tag, span: Span { str: "<head>", start: 7, end: 13 }, inner: [] }
Pair { rule: tag, span: Span { str: "</head>", start: 14, end: 21 }, inner: [] }
Pair { rule: tag, span: Span { str: "<body>", start: 22, end: 28 }, inner: [] }
Pair { rule: tag, span: Span { str: "</body>", start: 28, end: 35 }, inner: [] }
Pair { rule: tag, span: Span { str: "</html>", start: 36, end: 43 }, inner: [] }
Pair { rule: EOI, span: Span { str: "", start: 43, end: 43 }, inner: [] }

```

它能正确识别所有标签🎉

现在让我们扩展一下，允许标签中包含文本。为了实现这一点，我们需要定义`text`，这可能很棘手。为什么？考虑以下输入:

```
<start> abc < def > </end>

```

综上所述，如果我们将`text`定义为`ANY+`，它将吞噬从`abc`字符串中的`a`字符开始直到`</end>`的所有内容。

如果我们改为将`text`定义为`ASCII_ALPHANUMERIC`，这将解决上面的问题，但是它将在`abc`字符串之后的`<`符号上给我们一个错误，因为解析器将期望它是一个标签的开始。

为了克服这一点，我们这样定义`text`:

```
document = { SOI ~ ( tag ~ text?)* ~ EOI }
...
text = { (ASCII_ALPHANUMERIC | other_symbols | non_tag_start | WHITESPACE )+ }
non_tag_start = ${ "<" ~ WHITESPACE+}
other_symbols = { ">" |"@" | ";" | "," }

```

现在，`text`被定义为重复一次或多次的任何字母数字字符或`other_symbols`或`non_tag_start`或`WHITESPACE`。

`other_symbol`规则处理我们想要包含的任何非字母数字符号。同时，`non_tag_start`是一个原子规则，它匹配严格遵循一个或多个空格的`<`。这样，我们就限制了我们认为是文本的内容。我们还不得不在标签开始符号`<`和标签名称之间不留下任何空格。对于我们的例子来说，这就够了。

现在，考虑以下输入:

```
const HTML:&str = "<html> <head> </head> <body> < def > </body> </html>";

```

我们会得到这些结果:

```
Pair { rule: tag, span: Span { str: "<html>", start: 0, end: 6 }, inner: [] }
Pair { rule: tag, span: Span { str: "<head>", start: 7, end: 13 }, inner: [] }
Pair { rule: tag, span: Span { str: "</head>", start: 14, end: 21 }, inner: [] }
Pair { rule: tag, span: Span { str: "<body>", start: 22, end: 28 }, inner: [] }
Pair { rule: text, span: Span { str: "< def >", start: 29, end: 36 }, inner: [...] }
Pair { rule: tag, span: Span { str: "</body>", start: 37, end: 44 }, inner: [] }
Pair { rule: tag, span: Span { str: "</html>", start: 45, end: 52 }, inner: [] }
Pair { rule: EOI, span: Span { str: "", start: 52, end: 52 }, inner: [] }

```

这是我们所期望的。

最后，我们需要在标签中引入属性，这样我们就可以开始将它解析成文档结构。为了实现这一点，让我们将一个`attr`定义为一个或多个`ASCII_ALPHA`字符，后面跟着`=`，后面跟着引用的文本:

```
attr = { ASCII_ALPHA+ ~ "=" ~ "\"" ~ text ~ "\""}

```

我们还会像这样修改`tag`:

```
tag = ${ "<" ~ "/"? ~ ASCII_ALPHA+ ~ (WHITESPACE+ ~ attr)* ~ "/"? ~ ">" }

```

如果我们在下面的代码上运行它，我们将它转换成一个原始字符串，这样我们就不用转义引号了:

```
const HTML:&str = r#"<html lang=" en"> <head> </head> <body> < def > </body> </html>"#;

```

我们将得到预期的输出:

```
Pair { rule: tag, span: Span { str: "<html lang=\" en\">", start: 0, end: 17 }, inner: [Pair { rule: attr, span: Span { str: "lang=\" en\"", start: 6, end: 16 }, inner: [Pair { rule: text, span: Span { str: " en", start: 12, end: 15 }, inner: [] }] }] }
Pair { rule: tag, span: Span { str: "<head>", start: 18, end: 24 }, inner: [] }
Pair { rule: tag, span: Span { str: "</head>", start: 25, end: 32 }, inner: [] }
Pair { rule: tag, span: Span { str: "<body>", start: 33, end: 39 }, inner: [] }
Pair { rule: text, span: Span { str: "< def >", start: 40, end: 47 }, inner: [...] }
Pair { rule: tag, span: Span { str: "</body>", start: 48, end: 55 }, inner: [] }
Pair { rule: tag, span: Span { str: "</html>", start: 56, end: 63 }, inner: [] }
Pair { rule: EOI, span: Span { str: "", start: 63, end: 63 }, inner: [] }

```

第一对中的`inner`字段包含了`attr`规则，这是意料之中的。

为了帮助我们确定文档的结构、标签的嵌套等等，我们将把`start_tag`、`end_tag`和`self_closing_tag`定义分开，如下所示:

```
tag = ${ start_tag | self_closing_tag | end_tag }
start_tag = { "<" ~ ASCII_ALPHA+ ~ (WHITESPACE+ ~ attr)* ~ ">" }
end_tag = { "</" ~ ASCII_ALPHA+ ~ (WHITESPACE+ ~ attr)* ~ ">" }
self_closing_tag = { "<" ~ ASCII_ALPHA+ ~ (WHITESPACE+ ~ attr)* ~ "/>" }

```

注意，在这里，我们可以改变`tag`定义的顺序，因为它们是互斥的，这意味着任何一个`start_tag`都不能是`self_closing_tag`等等。

为了检查这一点，我们使用以下输入:

```
const HTML:&str = r#"<html lang="en"> <head> </head> <body> <div class="c1" id="id1"> <img src="path"/> </div> </body> </html>"#;

```

预期的输出如下，但请注意，它是手动格式化的，并且为了更好的可读性，开始和结束位置已被截断:

```
Pair { rule: tag, span: Span { str: "<html lang=\"en\">" ... }, 
  inner: [Pair { rule: start_tag, span: Span { str: "<html lang=\"en\">" ...}, 
    inner: [Pair { rule: attr, span: Span { str: "lang=\"en\"" ... }, 
      inner: [Pair { rule: text, span: Span { str: "en"}, inner: [] }] }] }] }

Pair { rule: tag, span: Span { str: "<head>" ... }, 
  inner: [Pair { rule: start_tag, span: Span { str: "<head>" ... }, inner: [] }] }

Pair { rule: tag, span: Span { str: "</head>" ... }, 
  inner: [Pair { rule: end_tag, span: Span { str: "</head>" ... }, inner: [] }] }

Pair { rule: tag, span: Span { str: "<body>" ... }, 
  inner: [Pair { rule: start_tag, span: Span { str: "<body>" ... }, inner: [] }] }

Pair { rule: tag, span: Span { str: "<div class=\"c1\" id=\"id1\">" ... }, 
  inner: [Pair { rule: start_tag, span: Span { str: "<div class=\"c1\" id=\"id1\">" ...}, 
    inner: [Pair { rule: attr, span: Span { str: "class=\"c1\"" ... }, 
      inner: [Pair { rule: text, span: Span { str: "c1" ... }, inner: [] }] }, 
  Pair { rule: attr, span: Span { str: "id=\"id1\"" ... }, 
      inner: [Pair { rule: text, span: Span { str: "id1" ... }, inner: [] }] }] }] }

Pair { rule: tag, span: Span { str: "<img src=\"path\"/>" ... }, 
  inner: [Pair { rule: self_closing_tag, span: Span { str: "<img src=\"path\"/>"...},
    inner: [Pair { rule: attr, span: Span { str: "src=\"path\"" ... }, 
      inner: [Pair { rule: text, span: Span { str: "path" ... }, inner: [] }] }] }] }

Pair { rule: tag, span: Span { str: "</div>" ...}, 
  inner: [Pair { rule: end_tag, span: Span { str: "</div>" ... }, inner: [] }] }

Pair { rule: tag, span: Span { str: "</body>" ...}, 
  inner: [Pair { rule: end_tag, span: Span { str: "</body>" ...}, inner: [] }] }

Pair { rule: tag, span: Span { str: "</html>" ...}, 
  inner: [Pair { rule: end_tag, span: Span { str: "</html>" ... }, inner: [] }] }

Pair { rule: EOI, span: Span { str: "" ... }, inner: [] }

```

正如我们所看到的，每个标签都被正确地识别为`start_tag`或`end_tag`，只有`img`标签除外，它被识别为`self_closing_tag`。

现在，您可以使用这些标记来构建文档树结构。这个项目的 [GitHub repo 实现了将它转换成树形结构的代码。该代码可以解析以下内容:](https://github.com/YJDoc2/LogRocket-Blog-Code/tree/main/parser-with-pest)

```
<html lang="en" l="abc"> 
<head> 
</head> 
<body> 
    <div class="c1" id="id1"> 
        Hello 
        <img src="path"/> 
    </div> 
    <div>
        <p>
            <p>
                abc
    </div>
    <p>
        jkl
    </p>
    <img/>
    </p> 
    </div>
</body> 
</html>

```

然后，它可以打印层次结构，看起来应该类似于下图:

```
- html
  - head
  - body
    - div
      - text(Hello)
      - img
    - div
      - p
        - p
          - text(abc)
    - p
      - text(jkl)
    - img

```

## 为 Rust 或您自己的语言编写解析器

使用我们在前面章节中看到的概念，您可以为现有语言编写一个类似的解析器，或者定义您自己的语法并为您自己的语言编写一个解析器。

例如，让我们考虑为 Rust 的语法子集编写一个解析器。因为为 Rust 编写一个完整的解析器本质上就是为 Rust 的编译器编写一个前端，所以我们不会在这里介绍完整的语法，也不会展示详细的代码示例。

首先，让我们考虑内置类型和关键字。我们可以为这些字符串编写如下解析规则:

```
LET_KW = { "let" }
FOR_KW = { "for" }
...
U8_TYP = { "u8" }
I8_TYP = { "i8" }
...
TYPES = { U8_TYP | I8_TYP | ..}

```

完成后，我们可以为包含`ASCII_ALPHA`字符、`ASCII_DIGIT`字符和下划线`_`符号的字符集合定义一个变量名，如下所示:

```
VAR_NAME = @{ ("_"|ASCII_ALPHA)~(ASCII_ALPHA | ASCII_DIGIT | "_")* }

```

这将允许变量名以下划线或字母字符开头，名称可以包含字母字符、数字或下划线。

在这之后，定义一个没有值的变量的 let 表达式就很简单了

```
VAR_DECL = {LET_KW ~ VAR_NAME ~ (":" ~ TYPES)? ~ ("=" ~ VALUE)?~ ";"}

```

我们从关键字`let`开始，后面是变量名，然后可选地是要赋值的变量类型和可选值，以所需的分号结束。值本身可以定义为数字、字符串或其他变量:

```
VALUE = { NUMBER | STRING | VAR_NAME }

```

通过这种方式，我们可以定义支持的结构并解析任何语言的语法。

在 Rust 的语法之外，我们还可以定义自己的自定义语法:

```
VAR_DECL = { "let there be" VAR_NAME ~( "which stores" ~ TYPES )? ~ ("which is equal to" ~ VALUE)? "." }

```

这将允许我们以下面的格式声明变量:

```
let there be num which stores i8 which is equal to 5.
let there be counter which is equal to 7.
let there be greeting which stores string.

```

这样，我们可以定义自己的语法和文法，使用 Pest 解析它，并构建自己的语言。

## 结论

在本文中，我们看到了为什么我们需要使用解析器生成器来生成解析器，而不是手工编写它们。我们还探索了 Pest 解析器生成器和 PEG 的使用。最后，我们看到了一个示例 Rust parser 项目，它使用 Pest 来解析简化的 HTML 文档的语法。

包括构建文档结构在内的代码可以在 [my GitHub repo](https://github.com/YJDoc2/LogRocket-Blog-Code/tree/main/parser-with-pest) 中找到。感谢阅读！

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。