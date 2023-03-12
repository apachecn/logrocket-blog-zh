# Rust serialization:今天准备生产什么？- LogRocket 博客

> 原文：<https://blog.logrocket.com/rust-serialization-whats-ready-for-production-today/>

序列化一直是铁锈的强项。特别是， [Serde](https://serde.rs) 在 Rust 1.0.0 发布之前就已经可以使用了(尽管衍生宏直到 1.15.0 才变得不稳定)。这背后的想法是使用 traits 来解耦对象，并从序列化格式中(反)序列化——这是一个非常强大的想法。格式编写者只需要实现 Serde 的(反)序列化特性，用户就可以`#[derive(Serialize, Deserialize)]`获得他们对象的序列化，而不管格式如何。

当然也有格式特定的板条箱，比如协议缓冲、`bincode`、FlatBuffers 等。这些协议可以提供良好的编译时和运行时性能，但是它们将数据锁定在各自的协议中，通常使用其他语言实现。对于许多用途，尤其是在多语言环境中，这是一个可以接受的折衷。

在本指南中，我们将着眼于这两种框架，考虑 API 的可用性和性能。虽然我肯定你会发现检查这种并列有很多价值，但不要搞错:我们是在比较苹果和橘子。

对于我们的基准测试，我们将使用这个相对简单的数据结构(请不要在生产中使用它):

```
pub enum StoredVariants {
    YesNo(bool),
    Small(u8),
    Signy(i64),
    Stringy(String),
}

pub struct StoredData {
    pub variant: StoredVariants,
    pub opt_bool: Option<bool>,
    pub vec_strs: Vec<String>,
    pub range: std::ops::Range<usize>,
} 
```

然后，基准将对这些`StoredData`的一部分进行序列化和反序列化。我们将测量编译基准所需的时间，以及字节和字节之间的时间。

## 塞尔德

Serde 是现有的序列化/反序列化库，它优雅、灵活、运行速度快、编译速度慢。

API 有三个方面。`Serialize`和`Deserialize`特征将格式连接到用户可能想要序列化和反序列化的数据结构。您很少需要手动实现这些特征，因为 Serde 附带了强大的 derive 宏，允许对输出格式进行许多调整。例如，您可以重命名字段，定义反序列化时缺失值的默认值，等等。一旦您已经派生或实现了那些特征，您就可以使用格式箱，比如 `serde_json` 或 `bincode` ，来序列化(反序列化)您的数据。

第三个方面是 `Serializer` 和 `Deserializer` 特征，格式箱需要实现这些特征来序列化(反序列化)任意数据结构。这意味着将 N 个数据结构序列化为 M 种格式的问题从 M × N 减少到 M+N。

尽管 Serde 具有著名的灵活性，但它严重依赖于单态化来提高性能，因此编译时间从一开始就是一个问题。为了应对这种情况，出现了多种板条箱，从 [miniserde](https://crates.io/crates/miniserde) ，到 [tinyserde](https://crates.io/crates/makepad-tinyserde) ，到 [nanoserde](https://crates.io/crates/nanoserde) 。这些工具背后的思想是使用运行时调度来减少由于单态化导致的代码膨胀。

我们的基准将序列化，然后将我们的数据结构反序列化到一个具有足够容量的`Vec<u8>`,以确保输出的分配不会干扰基准。

## JSON

crate 允许与 JSON 进行序列化和反序列化，JSON 是纯文本，因此(某种程度上)可读，代价是解析和格式化过程中的一些开销。

序列化可以用`to_string`、`to_vec`或`to_writer`和`_pretty`来完成——变体写出格式良好的 JSON，而不是缩小的 JSON。对于反序列化，`serde_json`有`from_reader`、`from_string`和`from_vec`。`serde_json`也有自己的`Value`型，带有`to_value`和`from_value`功能。

当然，这不是最快的方法。在我的机器上序列化`StoredData`需要大约四分之一微秒。反序列化耗时不到半微秒。缩小的数据占用 100 字节。它变成了 153 字节。开销将根据序列化的类型和值而变化。例如，一个 boolean 可能理想地占用 1 位，但是它至少需要 4 个字节来序列化为 JSON ( `true`，不包括键)。

## 西姆·JSON

这是一个板条箱，试图充分利用我们的 CPU 的向量单位，使 JSON 序列化真的很快。这并不奇怪。更令人惊讶的(至少对我来说)是反序列化相对滞后。也许这对于向量化来说不够简单，或者我的基准做错了什么。

## 亚姆

YAML 的名字是一个递归的缩写:YAML 不是标记语言。这是另一种像 JSON 一样具有多种语言绑定的文本格式，但是它非常特殊。序列化大约需要 3 微秒，反序列化大约需要 7 微秒，因此对于这个特定的工作负载，它比 JSON 慢 10 倍以上。大小为 99 字节，与 JSON 相当。

只有当你的数据已经在 YAML 时，使用 YAML 才有意义。

## 留宿（remain overnight 的缩写）

Rusty 对象符号是一种新的源自 Rust 的文本格式。它比 91 字节的 JSON 要简洁一点，但是处理起来要慢一些。序列化花了半微秒多一点的时间；反序列化大约需要两微秒。这比 JSON 慢，但也不是特别慢。

## `bincode`

像`serde_json`，`bincode`也和`serde`一起工作来序列化或反序列化任何实现各自特征的类型。由于`bincode`使用二进制格式，显然没有与`String`相关的方法。`serialize`创建一个`Vec<u8>`并且`serialize_into`接受一个`&mut Writer`以及要序列化的值。

因为`Writer`是为很多类型(特别是`&mut Vec<u8>`、`File`和`TcpStream`)实现的，这个简单的 API 给了我们很多好处。反序列化与`deserialize`的工作方式类似，它接受一个`&[u8]`，而`deserialize_from`接受任何实现`Read`的类型。

所有这些泛型的一个小缺点是，当参数出错时，类型错误可能会令人困惑。典型的例子:当我编写基准时，我忘记了一个`&mut`,在找到解决方案之前，我必须查找`Write`特征的实现者。

在性能方面，可以预见，它比`serde_json`更快。序列化大约需要 35 纳秒，反序列化不到八分之一微秒。序列化数据最终占用了 58 个字节。

## **消息包**

MessagePack 是另一种具有多语言绑定的二进制格式。它引以为豪的是在网络上非常简洁，我们的基准案例验证了这一点:数据序列化为 24 字节。

MessagePack 协议的 Rust 实现称为`rmp`，它也与`serde`一起工作。界面(除了命名上的小差异)和上面的一样。与`bincode`相比，它在空间方面的节俭带来了很小的性能开销。序列化需要十分之一微秒，而反序列化大约需要三分之一微秒。

## **jsonway**

jsonway 提供了一个接口来构造一个`serde_json` `JsonValue`。但是，没有`derive`宏，所以我们必须手工实现序列化。

```
struct DataSerializer<'a> {
        data: &'a StoredData,
}

impl Serializer for DataSerializer<'_> {
        fn root(&self) -> Option<&str> { None }
        fn build(&self, json: &mut jsonway::ObjectBuilder) {
                let data = &self.data;
                use StoredVariant::*;
                json.object("variant", |json| match data.variant {
                        YesNo(b) => { json.set("YesNo", b) },
                        Small(u) => { json.set("Small", u) },
                        Signy(i) => { json.set("Signy", i) },
                        Stringy(ref s) => { json.set("Stringy", s) },
                });
                match data.opt_bool {
                         Some(true) => json.set("opt_bool", true),
                         Some(false) => json.set("opt_bool", false),
                         None => {},
                };
                json.array("vec_strs", |json| json.map(data.vec_strs.iter(), |s| s[..].into()));
                json.object("range", |json| {
                        json.set("start", data.range.start);
                        json.set("end", data.range.end);
                });
        }
} 
```

注意，这将只构造一个`serde_json::Value`，它非常快(只有几纳秒)，但不完全是一个序列化的对象。序列化这个花费了我们大约 5 毫秒，这比直接使用`serde_json`要慢得多。

## CBOR

简明二进制对象表示(CBOR)是另一种二进制格式，在我们的测试中，它占了 72 个字节。序列化大约 140 纳秒就足够快了，但是反序列化出乎意料地慢了将近半毫秒。

## 明信片

明信片箱是为嵌入式系统设计的。对于 41 字节，这是大小和速度之间的一个很好的折衷，因为序列化需要 60 纳秒，反序列化需要 180 纳秒，它比 bincode 大约慢 1.5 倍，大约是消息大小的 70%。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

相对快速的序列化和简洁的格式非常适合嵌入式系统。MessagePack 可能会使嵌入式 CPU 负担过重，而我们通常有更强大的机器来反序列化数据。

## 弹性缓冲器

FlexBuffers 是一种源自 FlatBuffers 的多语言、二进制、无模式格式。在这个基准测试中，它的序列化性能比 RON 更差，反序列化性能比 JSON 更差。也就是说，这种格式就像 41 字节的明信片一样紧凑。

只有当我必须使用已经使用它的服务时，我才会使用它。对于自由选择的多语言格式，JSON 和 msgpack 在各个方面都是最好的。

还有其他基于 Serde 的格式，但这些主要是为了与现有系统接口，例如 Python 的 Pickle 格式、Apache Hadoop 的 Avro 或 DBus 的 binary wire 格式。

## 平板缓冲器

Google 提供了一种多语言序列化格式，可以绑定到 C、C++、Java、C#、Go、Lua 和 Rust 等语言。为了将它们结合在一起，FlatBuffers 有自己的接口定义语言，你必须学习它(我在写这篇文章的时候必须学习它)。

它有`struct` s、`enum` s(它们是 C 风格的单值`enum`)、`union` s，出于某种原因还有`table` s，有趣的是，后者是 FlatBuffers 定义类型的主要方式。他们在所有领域都像铁锈一样工作。除此之外，`struct`都是一样的，只有非可选字段。这样做是为了促进接口的发展。

除此之外，基本类型大部分都在那里，只是名称不同:

| **生锈** | **平板缓冲器** |
| u8，u16，u32，u64 | uint8, uint16, uint32, uint64 |
| i8，i16，i32，i64 | int8, int16, int32, int64 |
| bool | bool |
| String | string |
| 【T；N]，如[String；5] | [T:N]，例如[字符串:5] |
| Vec | [T] |

FlatBuffer 的`union` s 类似于 Rust enums，但是它们总是有一个`NONE`变体，并且所有的变体必须是`table` s，大概是为了允许以后的变化。

下面是上面的`StoredData`作为一个 FlatBuffers 模式，它进入一个`storeddate.fbs`文件。`fbs`扩展代表“FlatBuffer 模式”

```
file_identifier "SDFB";

table Bool {
    value: bool;
}

table Uint8 {
    value: uint8;
}

table Int64 {
    value: int64;
}

table String {
    value: string;
}

union StoredVariants {
    Bool,
    Uint8,
    Int64,
    String
}

struct Range {
    start: uint64;
    end: uint64;
}

table StoredData {
    variant_content: StoredVariants;
    opt_bool: bool;
    vec_strs: [String];
    range: Range;
}

root_type StoredData; 
```

FlatBuffers 似乎缺少指针长度整数的直接表示(例如，`usize`和`Range` s)，所以在这个例子中，我只选择了`uint64`和一个长度为`2`的数组来表示它们。这在 32 位机器上不太理想。文档还告诉我们，FlatBuffers 将把整数值存储为小端，所以在大端机器上，这将会影响性能。但这是网络广泛应用的代价。

编译这个来信任代码需要`flatc`，它是一个 Windows 二进制文件。它也可能在您的 Linux 发行版的存储库中。否则，它可以从源代码构建。要构建它，你需要谷歌开发的构建工具`bazel`。

安装之后，您可以克隆 FlatBuffers repo(我使用的是 1.12.0 版本)并用`bazel build flatc`构建它。

一旦构建完成，可执行文件将位于`bazel-out/k8-fastbuild/bin/flatc`。把它放在`$PATH`上允许下面的命令行。

```
$ flatc --rust stored_data.fbs 
```

现在我们有了一个可以在代码中`include!`的`storeddata_generated.rs`文件。显然，这不是我们最初的数据结构，但是为了便于比较，我们将通过这种稍加修改的类型对序列化和反序列化进行基准测试。反序列化基本上是一个 memcpy (或者 甚至没有 memcpy，但是你只能做一次，这将使基准测试变得更加困难)，所以它几乎是免费的。然而，如果你不想把对你的类型的控制权交给 flatbuffer，你需要代码在反序列化的时候把 flatbuffer 的类型转换成我们自己的类型。这是我基准测试的。

## 原始船长

在我们发表这篇文章后，人们问我为什么遗漏了 Cap'n Proto，所以我将它添加到了基准测试中。它的工作原理类似于平板缓冲器，但是接口有点难以理解，所以我不能保证结果。高运行时间表明我做错了什么。*

***更新，2020 年 9 月 25 日**:Cap ' n Proto 板条箱的一个维护者给我发了一个 PR，显示我确实做错了:我使用了一个嵌套结构来表示一个`Option<bool>`，而使用一个`OptionBool`枚举(就像我可选板条箱中的那个)会更合适。更新后的基准测试结果显示了一些改进(产生的字节流有所减少)，但仍然比类似的解决方案慢。*

2020 年 11 月 24 日更新:Cap'nProto 团队又进行了一次更新，再次提高了性能，尽管他们向我解释说，他们无法与其他解决方案的性能相比，因为他们对数据进行了更多的检查。从安全角度来说，这可能是个好主意。

## 憎恨

这个箱子有点用词不当，因为它真的令人厌恶。使用它肯定是不安全的，也可能是不健康的。除了基准测试之外，将它用于测量序列化和反序列化的最大理论性能是完全不可取的。你已经被警告了。

Abomonation 不使用`serde`，而是使用自己的`Abomonation`特性来序列化和反序列化任何数据。它真正做的基本上是一个 memcpy，加上固定偶尔的指针，所以它可以处理像`Vec`和`String`这样的事情。然而，目前它缺乏对`BTreeMap`、`HashMap`、`VecDeque`和其他标准数据结构的处理，包括我们在`StoredData`中使用的`Range`。我克隆了存储库并设置了一个 pull 请求来实现`Range`的`Abomonation`特征。在合并之前，我将在基准测试中使用我自己的分支。

对于反序列化，只要我们想使用解码后的值，我们就需要保持数据有效，因为`Abomonatio`不会复制内存——它会选择就地重用它。这也意味着我们必须在每次基准测试运行时复制数据。否则，我们会破坏数据。

虽然结果数据占用 116 字节，但确实非常快。序列化需要 15ns 多一点，反序列化需要 10ns 多一点，即使有额外的副本。

同样，在生产中使用它之前请注意。对于很多类型来说，其实是不健全的，每用一次，编码大神们就杀了一只甜甜的小螃蟹。或者什么的。

## 结果

下表显示了所有格式的序列化大小和序列化/反序列化的舍入时间(在我的机器上测量—您的里程会有所不同)。

| **格式** | **板条箱版本** | **字节** | **连载时间** | **反序列化的时间** |
| `json` | 1.0.57 | 100 | 284.85 ns | 443.51 ns |
| `simd-json` | 0.3.22 | 100 | 77.819 ns | 456.60 秒 |
| `yaml` | 0.8.13 | 99 | 3.2847 美元 | 8.0961 美国 |
| `ron` | 0.6.0 | 91 | 642.23 ns | 1.1271 us |
| `bincode` | 1.3.1 | 58 | 43.451 ns | 127.10 ns |
| `msgpack` | 0.14.4 | 24 | 93，329 ns | 197.28 ns |
| `cbor` | 0.11.1 | 72 | 194.83 秒 | 534.99 ns |
| `postcard` | 0.5.1 | 41 | 55.363 ns | 210.12 ns |
| `flexbuffers` | 0.1.1 | 41 | 1.4346 us | 815.03 ns |
| `flatbuffers` | 0.6.1 | 104 | 167 点 30 分 | 114.11 ns |
| `capnproto` | 0.13.6 | 96 | 143.10 ns | 357.23 ns |
| `packed` | 0.13.6 | 39 | 231.22 秒 | 485.49 ns |
| `abomonation` | 主人* | 116 | 15.157 纳秒 | 12.711 纳秒 |

*进行了额外的更改，以允许序列化已合并但未发布的`Range`

## 结论

序列化确实是铁锈的一个强项。这些库既成熟又快速。

我觉得我应该在这里唱出对赛德的赞美。这是一项伟大的工程，我强烈推荐它。

让我们快速总结一下我们对其他选择的了解:

*   对于您选择的格式，如果您需要快速的序列化和反序列化，`bincode`是最好的选择
*   对于最小可能的序列化大小，MessagePack 是最好的格式，尽管您在反序列化上花费了更多的运行时间
*   明信片在大小和速度之间提供了一个很好的折衷，允许嵌入式使用
*   平板缓冲器很复杂，给人的感觉明显不生锈，而且占据了过多的空间。除非在多种语言中使用模式定义，否则确实没有理由使用它。即便如此，JSON 还是更快
*   JSON 是三种可读格式中速度最快的，这是有道理的，因为它已经被业界广泛使用，并受益于 SIMD 优化，尤其是 和`simd-json`机箱

关于成熟度，只有`bincode`和 JSON 标有`1.` *主版本号。尽管如此，在 Rust 世界中，当涉及到 1.0 时，有一种非常小心的趋势，甚至可能过于小心，所以这并不能说明板条箱的实际成熟度。

我发现所有基于 Serde 的板条箱都很容易使用，尽管板条箱之间的接口更加一致不会有什么坏处。基准用`to_writer`、`serialize_into`、`serialize(Serializer::new(_))`、`to_slice`或`to_bytes`序列化，用`from_slice`、`from_bytes`、`from_read_ref`或`deserialize`反序列化。

我的基准代码可以在 [GitHub](https://github.com/llogiq/serdebench) 上找到。如果您发现问题或改进，请随时给我发送问题或 PR。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。