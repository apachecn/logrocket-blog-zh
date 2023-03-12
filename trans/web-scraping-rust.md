# 使用 Rust - LogRocket 博客进行网络抓取

> 原文：<https://blog.logrocket.com/web-scraping-rust/>

Web 抓取是一些应用程序中棘手但必要的部分。在这篇文章中，我们将探讨一些在编写 web scraper 时要记住的原则。我们还将看看 Rust 有哪些工具可以让编写 web scraper 变得更容易。

我们将涵盖的内容:

## 什么是网页抓取？

网页抓取是指以自动方式从网页中收集数据。如果您可以在 web 浏览器中加载一个页面，那么您可以将它加载到一个脚本中，并从中解析出您需要的部分！

然而，网页抓取可能相当棘手。HTML 不是一种非常结构化的格式，所以你通常需要挖掘一下才能找到相关的部分。

如果您想要的数据可以通过另一种方式获得——通过某种 API 调用，或者以 JSON、XML 或 CSV 等结构化格式获得——那么通过这种方式获得数据几乎肯定会更容易。网页抓取可能是最后的手段，因为它可能会很麻烦和脆弱。

网页抓取的细节很大程度上取决于你获取数据的页面。下面我们来看一个例子。

## 网页抓取原则

让我们来看看一些很好遵循的网络抓取的一般原则。

### 写 web scraper 的时候做个好公民

写 web scraper 的时候，很容易不小心快速发出一堆 web 请求。这被认为是粗鲁的，因为它可能会淹没较小的 web 服务器，使它们很难响应来自其他客户端的请求。

此外，它可能被认为是拒绝服务(DoS)攻击，您的 IP 地址可能会被手动或自动阻止！

避免这种情况的最好方法是在两次请求之间放置一个小的延迟。我们将在本文后面看到的例子中，请求之间有 500 毫秒的延迟，这应该足够让 web 服务器不至于不堪重负。

### 致力于强大的 web 刮刀解决方案

正如我们将在示例中看到的，很多 HTML 并不是为人类阅读而设计的，所以弄清楚如何定位要提取的数据可能有点棘手。

一种选择是在文档中找到第七个`p`元素。但这是非常脆弱的；如果 HTML 文档页面有一点小小的变化，第七个`p`元素很可能会有所不同。

最好试着找一些看起来不会改变的更健壮的东西。

在下面的例子中，为了找到主数据表，我们找到了拥有最多行的`table`元素，即使页面发生显著变化，它也应该是稳定的。

### 验证，验证，验证！

防止意外页面更改的另一种方法是尽可能多地进行验证。确切地说，您验证的内容对于您正在抓取的页面和您正在使用的应用程序来说是非常具体的。

在下面的示例中，我们验证的一些内容包括:

*   如果一行有我们正在寻找的任何一个标题，那么它就有我们期望的所有三个标题
*   这些值都在 0 到 100，000 之间
*   这些值正在减少(我们知道这是意料之中的，因为我们正在查看的数据很具体)
*   解析页面后，我们至少获得了 50 行数据

包含合理的错误消息也是有帮助的，这样当问题发生时，就可以更容易地跟踪违反了什么不变量。

现在，我们来看一个带锈的网页抓取的例子！

## 用铁锈制造刮刀

在本例中，我们将从社会保障管理局(SSA)收集预期寿命数据。这些数据可以在 SSA 网站的各个页面上的“生命表”中找到。

我们正在使用的页面列出了 1900 年出生的人活到不同年龄的几率。SSA 对这些生命表提供了更加[全面的解释，但是我们不需要通读这篇文章的整个研究。](https://www.ssa.gov/oact/NOTES/as120/LifeTables_Body.html)

桌子被分成两部分，男性和女性。表格的每一行代表不同的年龄(即“x”列)。其他各栏显示了该年龄段存活率的不同统计数据。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

就我们的目的而言，我们关心“lx”列，它从 100，000 个出生婴儿(0 岁)开始，并显示在给定年龄有多少婴儿仍然活着。这是我们想要捕获并保存到 JSON 文件中的数据。

SSA 为 1900-2100 年每 10 年出生的婴儿提供这个数据(我假设 2100 年的数据只是一个投影，除非他们那边有时光机！).我们想捕捉所有的一切。

有一点需要注意:在 1900 年，14%的婴儿没能活到一岁！2020 年，这个数字更像是 0.5%。现代医学万岁！

HTML 表格本身有点奇怪；因为它被分为男性和女性，所以在一个`table`元素中基本上有两个表，一堆标题行，以及每五年插入一次的空白行，以便于人们阅读。我们将不得不处理所有这些，同时建立我们的锈网刮刀。

示例代码在[这个 GitHub repo](https://github.com/gregstoll/rust-scraping) 中。请随意跟随我们查看铲运机的不同部分！

### 获取有铁锈的页面

首先，我们需要获取网页。在这一步，我们将使用 `[reqwest](https://crates.io/crates/reqwest)` [板条箱](https://crates.io/crates/reqwest)[。如果您同时在做大量的工作，这个机箱有强大的方法以异步方式获取页面，但是对于我们的目的，使用阻塞 API 更简单。](https://crates.io/crates/reqwest)

请注意，要使用阻塞 API，您需要将“阻塞”特性添加到您的`Cargo.toml`文件中的`reqwest`依赖项中；[参见 Github repo](https://github.com/gregstoll/rust-scraping) 文件第九行的例子。

获取页面是在`scraper_utils.rs`中的`[do_throttled_request()](https://github.com/gregstoll/rust-scraping/blob/main/src/scraper_utils.rs#L19)`T2 方法中完成的。下面是该代码的简化版本:

```
// Do a request for the given URL, with a minimum time between requests
// to avoid overloading the server.
pub fn do_throttled_request(url: &str) -> Result<String, Error> {
    // See the real code for the throttling - it's omitted here for clarity
    let response = reqwest::blocking::get(url)?;
    response.text()
}

```

其核心是，这个方法非常简单:发出请求并以`String`的形式返回主体。我们使用`?`操作符对我们遇到的任何错误进行早期返回——例如，如果我们的网络连接中断。

有趣的是，`text()`方法也可能失败，我们也只是返回它。请记住，由于最后一行末尾没有分号，这与执行以下操作是一样的，但对于 Rust 来说更习惯一些:

```
return response.text();

```

### 用 Rust `scraper`机箱解析 HTML

现在到了最难的部分！我们将[使用恰当命名的](https://crates.io/crates/scraper) `[scraper](https://crates.io/crates/scraper)` [机箱](https://crates.io/crates/scraper)，它是基于[与 Firefox 共享代码的伺服项目](https://servo.org/)。换句话说，它是一个工业级的解析器！

解析是使用您的`main.rs`文件中的`[parse_page()](https://github.com/gregstoll/rust-scraping/blob/main/src/main.rs#L44)` [方法](https://github.com/gregstoll/rust-scraping/blob/main/src/main.rs#L44)完成的。让我们把它分成几个步骤。

首先，我们解析文档。注意，下面的`parse_document()`调用没有返回错误，因此不会失败，这是有意义的，因为这是来自真实 web 浏览器的代码。不管 HTML 的格式有多糟糕，浏览器总要呈现一些东西！

```
let document = Html::parse_document(&body);
// Find the table with the most rows
let main_table = document.select(&TABLE).max_by_key(|table| {
    table.select(&TR).count()
}).expect("No tables found in document?");

```

接下来，我们希望找到文档中的所有表格。`select()`调用允许我们传入一个 CSS 选择器，并返回所有匹配该选择器的节点。

CSS 选择器是一种非常强大的方式来指定你想要的节点。出于我们的目的，我们只想选择所有的表节点，这很容易用一个简单的`Type`选择器来完成:

```
static ref TABLE: Selector = make_selector("table");

```

一旦我们有了所有的表节点，我们希望找到拥有最多行的节点。我们将[使用](https://doc.rust-lang.org/stable/std/cmp/fn.max_by_key.html) `[max_by_key()](https://doc.rust-lang.org/stable/std/cmp/fn.max_by_key.html)` [方法](https://doc.rust-lang.org/stable/std/cmp/fn.max_by_key.html)，对于键我们得到表中的行数。

节点也有一个`select()`方法，所以我们可以使用另一个简单的选择器来获取所有行的后代，并对它们进行计数:

```
static ref TR: Selector = make_selector("tr");

```

现在是时候找出哪些列有“100，000”文本了。下面是这段代码，为了清楚起见，省略了一些部分:

```
let mut column_indices: Option<ColumnIndices> = None;
for row in main_table.select(&TR) {
    // Need to collect this into a Vec<> because we're going to be iterating over it
    // multiple times.
    let entries = row.select(&TD).collect::<Vec<_>>();
    if column_indices.is_none() {
        let mut row_number_index: Option<usize> = None;
        let mut male_index: Option<usize> = None;
        let mut female_index: Option<usize> = None;
        // look for values of "0" (for the row number) and "100000"
        for (column_index, cell) in entries.iter().enumerate() {
            let text: String = get_numeric_text(cell);
            if text == "0" {
                // Only want the first column that has a value of "0"
                row_number_index = row_number_index.or(Some(column_index));
            } else if text == "100000" {
                // male columns are first
                if male_index.is_none() {
                    male_index = Some(column_index);
                }
                else if female_index.is_none() {
                    female_index = Some(column_index);
                }
                else {
                    panic!("Found too many columns with text \"100000\"!");
                }
            }
        }
        assert_eq!(male_index.is_some(), female_index.is_some(), "Found male column but not female?");
        if let Some(male_index) = male_index {
            assert!(row_number_index.is_some(), "Found male column but not row number?");
            column_indices = Some(ColumnIndices {
                row_number: row_number_index.unwrap(),
                male: male_index,
                female: female_index.unwrap()
            });
        }
    }

```

对于每一行，如果我们没有找到我们需要的列索引，我们将寻找年龄的值`0`和男性和女性列的值`100000`。

注意，`get_numeric_text()`函数负责删除文本中的任何逗号。还要注意这里断言和恐慌的数量，以防止页面格式改变太多——我们宁愿让脚本错误消失，也不愿得到不正确的数据！

最后，这里是收集所有数据的代码:

```
if let Some(column_indices) = column_indices {
    if entries.len() < column_indices.max_index() {
        // Too few columns, this isn't a real row
        continue
    }
    let row_number_text = get_numeric_text(&entries[column_indices.row_number]);
    if row_number_text.parse::<u32>().map(|x| x == next_row_number) == Ok(true) {
        next_row_number += 1;
        let male_value = get_numeric_text(&entries[column_indices.male]).parse::<u32>();
        let male_value = male_value.expect("Couldn't parse value in male cell");
        // The page normalizes all values by assuming 100,000 babies were born in the
        // given year, so scale this down to a range of 0-1.
        let male_value = male_value as f32 / 100000_f32;
        assert!(male_value <= 1.0, "male value is out of range");
        if let Some(last_value) = male_still_alive_values.last() {
            assert!(*last_value >= male_value, "male values are not decreasing");
        }
        male_still_alive_values.push(male_value);
        // Similar code for female values omitted
    }
}

```

这段代码只是确保行号(即年龄)是下一个预期值，然后从列中获取值，解析数字，并按比例缩小。同样，我们做一些断言来确保这些值看起来合理。

### 将数据写出到 JSON

对于这个应用程序，我们希望数据以 JSON 格式写出到一个文件中。在这一步，我们将使用 `[json](https://crates.io/crates/json)` [板条箱](https://crates.io/crates/json)[。现在我们有了所有的数据，这一部分非常简单:](https://crates.io/crates/json)

```
fn write_data(data: HashMap<u32, SurvivorsAtAgeTable>) -> std::io::Result<()> {
    let mut json_data = json::object! {};
    let mut keys = data.keys().collect::<Vec<_>>();
    keys.sort();
    for &key in keys {
        let value = data.get(&key).unwrap();
        let json_value = json::object! {
            "female": value.female.clone(),
            "male": value.male.clone()
        };
        json_data[key.to_string()] = json_value;
    }
    let mut file = File::create("fileTables.json")?;
    write!(&mut file, "{}", json::stringify_pretty(json_data, 4))?;
    Ok(())
}

```

对键进行排序并不是绝对必要的，但它确实使输出更容易阅读。我们使用方便的`[json::object!](https://docs.rs/json/0.12.4/json/macro.object.html)` [宏](https://docs.rs/json/0.12.4/json/macro.object.html)轻松地创建 JSON 数据，并用`write!`将其写出到文件中。我们完事了。

## 结论

希望这篇文章能给你一个在 Rust 中做网页抓取的好起点。

有了这些工具，大量的工作可以简化为制作 CSS 选择器来获得您感兴趣的节点，并计算出在页面发生变化的情况下，您可以使用哪些不变量来断言您获得了正确的不变量！

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。