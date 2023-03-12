# 探索通用 GraphQL 错误代码的未来潜力 Blog 火箭博客

> 原文：<https://blog.logrocket.com/exploring-future-potential-generic-graphql-error-codes/>

GraphQL 规范为 GraphQL 服务器在解析查询时执行的验证定义了一组[。如果某些验证失败，必须在 GraphQL 响应](https://spec.graphql.org/October2021/#sec-Validation)的 [`errors`条目下返回相应的错误消息。要返回的实际消息没有在规范中定义，因此它是由每个服务器定制的。更重要的是，该规范没有定义唯一识别每个错误的代码。](https://spec.graphql.org/October2021/#sec-Errors.Error-result-format)

换句话说，在 GraphQL 规范中没有“错误代码”的概念——只有要执行的验证。事实上，响应中没有针对错误的`code`条目，只有`message`(还有`locations`，以指示查询中错误产生的位置)。为了返回一个错误代码，GraphQL 服务器必须将它添加到错误的 [`extensions`子条目](https://spec.graphql.org/October2021/#sel-GAPHRPZBABDg0F)下，GraphQL 服务器可以将它用于它们支持的任何自定义特性，并使用它自己构思的错误代码。

对于要返回的实际错误代码，服务器当前可能会使用以下替代方法之一:

1.  GraphQL 规范中定义相应验证的部分
2.  使用自定义错误代码，即未在规范级别定义的代码

例如，每当 GraphQL 查询中的给定字段缺少强制参数时，GraphQL 服务器可能会在第一种情况下返回错误代码 [`5.4.2.1`，或者在第二种情况下返回`"gql0001"`(或其他内容)。](https://spec.graphql.org/October2021/#sec-Required-Arguments)

其他一些语言/框架确实使用了错误代码，如 [Rust](https://doc.rust-lang.org/error-index.html) 、[reactor](https://github.com/facebook/react/blob/main/scripts/error-codes/codes.json)和 [TypeScript](https://github.com/microsoft/TypeScript/blob/main/src/compiler/diagnosticMessages.json) 。受这些其他语言和框架经验的启发，两年前提出了几个问题，要求在 GraphQL 规范中添加通用错误代码:

1.  [# 698–讨论:规范中的一般错误代码](https://github.com/graphql/graphql-spec/issues/698)
2.  [# 708-【RFC】标准错误代码](https://github.com/graphql/graphql-spec/issues/708)

这两个问题都立即得到了机构群体的认可，但是它们都没有发布任何公共关系，而且它们在去年都没有任何活动。

作为我自己的 GraphQL 服务器的维护者，我经常浏览 GraphQL 规范的建议列表，试图确定我要实现的下一个任务。我研究了关于一般错误代码的问题，发现它们很有说服力，所以我决定在 PR 之前支持它们，而不是等待它们合并到规范中(这也可能永远不会发生)。

在本文中，我将分享我在我的 GraphQL 服务器上实现这个特性的一些见解，包括:

最后，我将给出我的观点，在 GraphQL 规范中支持通用错误代码是否值得追求。

## GraphQL 中通用错误代码的潜在好处

目前，所有的 GraphQL 服务器都必须实现自己的测试套件，以确保验证得到支持并按预期工作。由于每个服务器都会为失败的验证返回一个定制的错误消息——例如，一些服务器可能返回`Mandatory argument '{0}' in field '{1}' is missing`，而另一个可能返回`You must provide a value for argument '{0}'`——它们的测试不能跨服务器重用:

```
{
  "errors": [
    {
      "message": "Mandatory argument 'id' in field 'user' is missing",
      "locations": [ { "line": 1, "column": 12 } ]
    }
  ]
}

```

但是在错误信息旁边返回一个通用错误代码可以解决这个问题。测试套件可以针对错误代码执行，所有服务器都会知道错误代码，因为它是在规范级别定义的。

在这种情况下，无论何时出现错误，服务器都会返回一个`code`条目，以及`message`和`locations`条目:

```
{
  "errors": [
    {
      "message": "Mandatory argument 'id' in field 'user' is missing",
      "locations": [ { "line": 1, "column": 12 } ],
      "code": "1"
    }
  ]
}

```

这确实是第一个问题所陈述的目标，它提出了支持一个适用于所有 GraphQL 服务器的[通用测试套件](https://github.com/graphql/graphql-spec/issues/698#issue-585687487)的想法:

> 我正在研究并计划构建一个通用的测试套件来检查各种 GraphQL 库的符合性[…]。GraphQL 有多种实现，它们都以不同的方式抛出错误。
> 
> 因为所有错误都以纯文本(字符串)的形式抛出。没有办法测试这些实现是否有这样的错误。
> 
> 假设我们给了一个 GraphQL 服务器一个不正确的查询(带有循环片段),我们预计会有一个错误。如果错误带有特定的错误代码，那么测试库并提供和保证错误的实际原因，从而改进 DX 将是非常好的。

使用通用错误代码还可以简化应用程序中需要理解错误的逻辑，并使交换 GraphQL 服务器提供程序变得更容易。例如，如果应用程序需要根据错误的严重程度显示不同的布局，这个逻辑可以根据错误代码做出反应，如果代码是通用的，在迁移到不同的 GraphQL 服务器后，相同的逻辑将会工作。

通过支持通用错误代码，我们可以将应用程序中所有错误消息和代码的定义集中起来，甚至将它们都定义在一个文件中，这可以帮助开发人员理解所有必须注意的可能发生的情况。React 就是这种情况，它在一个单独的 JSON 文件中定义了所有的错误代码。

最后，同一个解决方案还提供了返回更多信息的机会。特别是，`specifiedBy`条目可以指向解释错误发生原因和解决方法的在线文档:

```
{
  "errors": [
    {
      "message": "Mandatory argument 'id' in field 'user' is missing",
      "locations": [ { "line": 1, "column": 12 } ],
      "code": "1",
      "specifiedBy": "https://spec.graphql.org/October2021/#sec-Required-Arguments"
    }
  ]
}

```

Rust 就是这种情况，它的[在线文档](https://doc.rust-lang.org/error-index.html)为开发者排除故障和理解语言提供了详细的指导。

## 设计和实施解决方案

我最近[在我的 GraphQL 服务器中实现了上述特性](https://github.com/leoloso/PoP/blob/master/layers%2FGraphQLAPIForWP%2Fplugins%2Fgraphql-api-for-wp%2Fdocs%2Fen%2Frelease-notes%2F0.9.md#link-to-the-online-documentation-of-the-graphql-errors)，这样它也可以为每个错误返回`code`和`specifiedBy`条目。

正如我之前提到的，这些仍然是自定义特性，不受 GraphQL 规范的支持，它们必须出现在`extensions`子条目下。只有当提议被批准并合并到规范中时，这些新条目才会出现在错误条目的根中。

目前，错误代码不是通用的，因为这取决于定义它们的 GraphQL 规范。相反，我决定将验证部分编号用于我的特定错误代码。

现在，当执行下面的错误查询时(因为变量`$limit`没有在操作中定义):

```
{
  posts(pagination: { limit: $limit }) {
    id
    title
  }
}

```

…除了预期的`message`和`locations`条目之外，服务器还将`code`和`specifiedBy`元素附加到错误中:

```
{
  "errors": [
    {
      "message": "...",
      "locations": [...],
      "extensions": {
        "code": "gql-5.8.3",
        "specifiedBy": "https://spec.graphal.org/draft/#sec-All-Variable-Uses-Defined"
      }
    }
  ]
}

```

![The GraphQL response contains error codes](img/ef662248c393e250d631dcc8df95a419.png)

下面，我将详细介绍我所做的一些设计和实现决策，以及我为什么要做这些决策，以及它们如何影响 GraphQL 中通用错误代码的有用性。

### 处理不同类型的错误

当我们创建 GraphQL API 时，有[种不同的错误，API 可能会返回](https://github.com/graphql/graphql-spec/issues/698#issuecomment-795038505):

1.  GraphQL 规范错误:那些与解析、验证和执行 GraphQL 文档相关的错误，分别包含在规范的[语言](https://spec.graphql.org/draft/#sec-Language)、[验证](https://spec.graphql.org/draft/#sec-Validation)和[执行](https://spec.graphql.org/draft/#sec-Execution)部分中
2.  客户端错误:属于应用程序领域的错误，比如验证输入是否有一定的长度或格式
3.  服务器错误:运行时操作失败时产生的错误，例如解析从外部数据源获取数据的字段时连接失败

客户机和服务器错误不仅仅是“所有其他”错误:它们在本质上是完全不同的。基于为应用程序定义的规则，客户端错误是预先已知的，并且它们是公共的，因此必须将相应的错误消息添加到响应中，以通知用户问题。

相比之下，服务器错误大多是由意外事件引起的，比如服务器或数据库关闭。它们是私有的，因为它们可能暴露安全敏感的数据(比如 API 密钥或数据库连接凭证)，所以用户必须收到模糊的`There was an unexpected error`消息，而详细的错误消息必须只提供给应用程序的管理员。

当我们讨论 GraphQL 的一般错误代码时，我们最初只处理第一类错误，GraphQL 规范错误。但是情况并不一定如此 GraphQL 服务器有机会针对所有三种类型的错误推广相同的概念及其相关逻辑。

这种概括可以产生几个额外的好处:

*   开发人员只需学习一种为 API 定义错误的方法
*   用户将总是收到相同格式的错误
*   错误代码可以以模块化的方式管理，我将在下面解释

### 管理错误代码的模块化方法

在一个文件中定义完整 API 的所有错误代码(正如我前面提到的)在模块化方法中不再可能，因为不同种类的错误必须在不同的层实现:

*   GraphQL 规范和服务器错误将在 GraphQL 规范层实现
*   服务器和客户端错误将在应用层实现

最多，只有 GraphQL 规范错误可以在单个文件中定义(正如这里的[和](https://github.com/leoloso/PoP/blob/a0a63d83546384e65ea44d4752c9a8aaeb944fba/layers/Engine/packages/graphql-parser/src/FeedbackItemProviders/GraphQLSpecErrorFeedbackItemProvider.php)所做的那样)，服务器和客户端错误可以为 API 一起定义，或者可能使用模块化方法在不同的文件中定义(正如我已经演示的[这里的](https://github.com/leoloso/PoP/blob/0475714e3504868285060f01649785e9982170ed/layers/Schema/packages/schema-commons/src/FeedbackItemProviders/InputValueCoercionErrorFeedbackItemProvider.php)和[这里的](https://github.com/leoloso/PoP/blob/98b32b5673c82797f3bd403e4fc913e920ed2dd4/layers/Engine/packages/access-control/src/FeedbackItemProviders/FeedbackItemProvider.php))，并且在为 API 安装的每个模块中定义，这可能会返回错误。

如果我们需要了解 API 中的所有错误，GraphQL 服务器可以支持通过自省来检索它们，而不是在一个地方查找和存储它们。

### 选择错误代码和适当的在线文档

由于错误的类型不同，GraphQL 服务器可以使用不同的策略来选择要返回的错误代码以及要指向的在线文档:

1.  对于 GraphQL 规范错误:
    1.  使用规范中定义的通用错误代码(一旦提供)
    2.  `specifiedBy`可以指向[spec.graphql.org/draft](https://spec.graphql.org/draft/)中的相应部分
2.  对于服务器错误:
    1.  使用 GraphQL 服务器定义的自定义错误代码
    2.  `specifiedBy`可以参考 GraphQL 服务器供应商网站上的文档
3.  对于客户端错误:
    1.  使用 API 开发人员定义的自定义错误代码
    2.  如果需要的话，可以指向公共 API 的技术文档(或者，可能是公共模式文档旁边的一个部分),例如，如果错误消息的描述性不够，那么输入验证应该已经足够了

### 错误代码的格式

在这一点上可能显而易见的是，不同类型的错误将返回不同的错误代码，并且它们可以使用不同的格式。

对于 GraphQL 规范错误，格式可以直接使用规范部分代码，例如`"gql-{section-code}"`(生成代码`"gql-5.4.2.1"`)，这允许我们快速了解 GraphQL 规范中的哪个错误被引用。为了在不同的 GraphQL 服务器上建立相同的错误代码，spec 部分代码是自然的选择，直到在 spec 中定义了相应的通用错误代码。

然而，[这个解决方案并不理想](https://github.com/graphql/graphql-spec/issues/698#issuecomment-602221146),因为一个部分可能需要不止一个验证来执行；因此，它不会与错误代码/消息一一对应。例如，第 [6.1 节执行请求](https://spec.graphql.org/draft/#sec-Executing-Requests)可能会返回[四个不同错误消息](https://github.com/leoloso/PoP/blob/a0a63d83546384e65ea44d4752c9a8aaeb944fba/layers/Engine/packages/graphql-parser/src/FeedbackItemProviders/GraphQLSpecErrorFeedbackItemProvider.php#L142-L145)中的一个，在我的服务器上，它们的代码中还添加了一个额外的后缀`a-d`:

*   `gql-6.1.a` : `Operation with name '%s' does not exist`
*   `gql-6.1.b` : `When the document contains more than one operation, the operation name to execute must be provided`
*   `gql-6.1.c` : `The query has not been provided`
*   `gql-6.1.d` : `No operations defined in the document`

对于客户端和服务器错误，它们是自定义错误)，我们可以使用格式`"{namespace}\{feedbackType}{counter}"`，其中:

*   对于安装在 API 上的每个模块来说，`{namespace}` ( `"PoP\ComponentModel"`)是唯一的(稍后将详细介绍)
*   `{feedbackType}` ( `"e"`表示“错误”)表示“反馈”的类型，其中“错误”只是几个选项中的一个(稍后将详细介绍)
*   `{counter}` ( `"24"`)只是一个递增的数字，用于在每个模块内为每条消息创建唯一的代码

![Our custom GraphQL error code appears in the response](img/c4b8db8f39d6de126a9124a933b6fb79.png)

遵循这种格式会在我的 GraphQL 服务器中产生代码`"PoP\ComponentModel\e24"`。

### 命名空间错误代码

错误代码是一个任意的字符串，只有一个要求:它必须是唯一的，标识一条错误消息。否则，如果相同的错误代码被分配给两个不同的错误消息，那么它就不会非常有用。

如前所述，应用程序中的不同模块可能会返回各自的错误。由于不同的错误发生在服务器和应用程序的不同层，并且不同的模块可能由不同的提供者提供(例如使用由一个第三方提供的某个模块，由 API 开发团队创建的另一个模块，等等)，创建一个为每个错误分配唯一代码的系统很快变得困难。

因此，在 GraphQL 响应中打印“名称空间”错误代码是有意义的。这样，两个不同的模块都可以在内部使用错误代码`"1"`，这些将被应用程序视为`"module1\1"`和`"module2\1"`。只要每个模块的每个名称空间是唯一的，那么所有产生的错误代码也将是唯一的。

### 提供其他类型的反馈消息

如果我们注意 TypeScript 中的[错误消息，我们会注意到，除了`code`之外，它们还有一个`category`。错误将有一个值为`"Error"`的类别，但也有其他值的条目，即`"Message"`和`"Suggestion"`:](https://github.com/microsoft/TypeScript/blob/main/src/compiler/diagnosticMessages.json)

```
{
  "'use strict' directive used here.": {
    "category": "Error",
    "code": 1349
  },
  "Print the final configuration instead of building.": {
    "category": "Message",
    "code": 1350
  },
  "File is a CommonJS module; it may be converted to an ES module.": {
    "category": "Suggestion",
    "code": 80001
  }
}

```

在 TypeScript 中，“错误”只是应用程序可以提供给用户的一种反馈。GraphQL 没有理由不能实现同样的想法。

实现错误代码为 GraphQL 服务器提供了支持这一功能的机会。由于 GraphQL 规范中没有记录这种行为，这些额外的反馈条目将需要在响应中的根`extensions`条目下返回。

为了区分这些不同的反馈类型，我在前面建议我们将`{feedbackType}`添加到条目代码中，用值`"e"`表示“错误”，`"w"`表示“警告”，而`"s"`表示“建议”。

GraphQL 规范目前涵盖了两种类型的反馈:“错误”和“反对”。GraphQL 服务器可以在内部将这两个消息视为“反馈”，因此相同的逻辑可以处理这两个消息(或任何其他类型的反馈消息)。然后，`specifiedBy`条目也可以方便地分配给弃用——进一步解释为什么模式中的一些字段被弃用，描述 API 未来预期的相关弃用，等等。

对于我的 GraphQL 服务器，除了错误和反对之外，我还决定支持这些类别:

*   警告
*   通知；注意
*   建议
*   原木

来自这些类别的消息提供了额外的细微差别，因为并非一切都是错误的。例如，使用`@export`将两个不同的值赋给同一个变量的查询不一定是一个暂停问题(在相应的字段中返回`null`),我宁愿返回一个警告:

![GraphQL response containing a warning](img/af1b831bf95cf2d5262b1ca844522e5b.png)

支持不同类型的反馈消息不会使 API 开发者的任务复杂化，因为定义[错误](https://github.com/leoloso/PoP/blob/10e3485f9ba5bfa32458efc89744fab7604b601f/layers/Engine/packages/graphql-parser/src/FeedbackItemProviders/GraphQLParserErrorFeedbackItemProvider.php)、[警告](https://github.com/leoloso/PoP/blob/5c56de5452a87992a524cbff807643112c0f9a89/layers/Engine/packages/component-model/src/FeedbackItemProviders/WarningFeedbackItemProvider.php)或[建议](https://github.com/leoloso/PoP/blob/1bf635022f26bcb82f85ff83691f68a29c9ee7fe/layers/Engine/packages/graphql-parser/src/FeedbackItemProviders/SuggestionFeedbackItemProvider.php)是基于相同的底层代码。

## 从实施中获得的见解

强制 GraphQL 服务器返回消息之外的错误代码，并通过在 GraphQL 规范中定义这些代码来使它们通用化，会产生什么好处和坏处？

以下是我的印象，基于我在我的服务器上添加对这个特性的支持的经验。

### 仍然没有通用的错误代码，但这很容易纠正

因为还没有通用的错误代码，所以我使用规范中相应的部分作为错误代码。但是这很容易纠正:如果这个问题被批准并合并到规范中，我必须只更新 PHP 常量中的[代码，这些 PHP 常量定义在一个文件](https://github.com/leoloso/PoP/blob/a0a63d83546384e65ea44d4752c9a8aaeb944fba/layers/Engine/packages/graphql-parser/src/FeedbackItemProviders/GraphQLSpecErrorFeedbackItemProvider.php)中，并且在服务器代码中对它们的所有引用中，我可以很容易地使用编辑器(在我的例子中是 VSCode)找到它们。

然后，GraphQL 服务器可以决定实现这个特性，并在未来以可以忽略的成本对其进行升级。

### 关于 GraphQL 规范没有重大变化

返回错误代码并不是一个突破性的改变，因为新特性在响应中添加了一个新条目`code`，而没有修改任何其他内容。

这个条目暂时必须放在`extensions`下。如果它被合并到规范中，那么`code`可以向上移动一级，这是一个最小的变化。

### 关于服务器代码的重大更改

目前，由于 GraphQL 响应必须只包含错误消息，GraphQL 服务器只需要传递一个字符串来表示错误，如[T2 的`graphql-js`代码所示:](https://github.com/graphql/graphql-js/blob/47bd8c8897c72d3efc17ecb1599a95cee6bac5e8/src/utilities/getOperationRootType.ts#L23-L26)

```
throw new GraphQLError(
  'Schema does not define the required query root type.',
  operation,
);

```

如果要返回错误代码，GraphQL 规范错误应该是强制性的(否则它们不会提供任何值)，而服务器/客户端错误可以是可选的，允许开发人员决定是否支持他们自己的 API 的错误代码(因为这些错误代码是应用程序定制的，它们不能满足跨不同服务器标准化响应的目的，使它们变得不那么有用)。

因此，GraphQL 服务器的代码库中产生错误的所有实例都必须进行调整，以提供错误代码。基于它们的 API 也可以被修改，但是它们不一定必须被修改。

此外，将每个模块中的所有错误消息转移到一个文件中是有意义的，这样可以确保为每个错误消息分配一个唯一的错误代码。然而，这项任务可能需要对 GraphQL 服务器代码进行相当大的重构，可能还需要对实现 API 进行重构(也就是说，如果底层架构发生变化，API 也可能被迫调整它们的代码)。

总之，GraphQL 服务器支持错误代码是有意义的，但可能只有在它们不需要彻底改变其当前架构的情况下才有意义，因为不需要实现也要适应的 API(否则会有失去用户的风险)。

另一方面，GraphQL 服务器已经实现的所有测试都可以基于错误代码进行调整，但这很可能是一项可选任务:如果基于错误消息的测试现在可以工作，那么在响应中引入额外的错误代码后，针对它们的测试应该仍然可以工作。

### 单元测试可以有更好的基础

在测试 GraphQL 服务器时，我们可能希望根据错误代码进行验证，错误代码简洁地传达了问题所在，而不是错误消息，错误消息也给出了不需要的信息，并且可能会偶尔更新(而代码将保持不变)。

此外，如果将所有错误放在每个模块的一个地方，我们也可以通过调用某个函数来获得错误消息(该函数必须接收错误代码，可能还需要一些参数来定制消息)。这样，如果错误信息被更新，单元测试仍然可以工作，而不需要更新其中的错误信息。

例如，验证 [5.8.3 为我的服务器定义的所有变量使用](https://spec.graphql.org/draft/#sec-All-Variable-Uses-Defined)是[这样做的](https://github.com/leoloso/PoP/blob/783b7715833791eafc245a9df3138d822ce1c932/layers/Engine/packages/graphql-parser/tests/Spec/Parser/DocumentTest.php#L216-L229):

```
public function testVariableMissing(): void
{
  $this->expectException(InvalidRequestException::class);
  $this->expectExceptionMessage(
    (
      new FeedbackItemResolution(
        GraphQLSpecErrorFeedbackItemProvider::class, GraphQLSpecErrorFeedbackItemProvider::E_5_8_3,
        ['missingVar']
      )
    )->getMessage()
  );
  $this->getParser()->parse('
    query SomeOperation {
      foo(bar: $missingVar) {
        id
      }
    }
  ')->validate();
}

```

### 跟踪未实施的验证

使用一个中心位置来管理所有的错误使得跟踪那些还没有实现的验证变得更加容易。

例如，我的服务器在一个文件中定义了所有的 GraphQL 规范验证，但是那些不满足的验证已经被禁用，并被赋予了[描述](https://github.com/leoloso/PoP/blob/a0a63d83546384e65ea44d4752c9a8aaeb944fba/layers/Engine/packages/graphql-parser/src/FeedbackItemProviders/GraphQLSpecErrorFeedbackItemProvider.php#L113) `"TODO: satisfy"`，这使我很容易识别它们。

### 更好地全面了解所有错误，但是更难可视化实际的验证代码

将所有错误代码移动到一个中心位置，可以更容易地全面理解应用程序，这是一件好事，但同时也使理解执行验证的实际代码变得更加困难，至少在错误代码不容易解释的情况下是如此。

例如，对于客户端和服务器错误，我的服务器使用一个简单的错误代码计数器，即`"1"`、`"2"`等，而不是更有意义的代码，如`"field_arg_missing"`、`"field_arg_cant_be_negative"`等。那是因为我很懒，也因为命名错误代码很难。

在某种程度上，GraphQL 规范错误也会出现同样的情况。错误代码`5.2.1.1`、`5.8.3`等不像错误代码`"recursive_fragments"`、`"missing_variable"`等那样具有描述性。

因此，当我将执行验证并在验证失败时抛出错误的代码可视化时，就不太容易理解错误是什么了。代码看起来像这样:

```
public function getValue(): mixed
{
  if ($this->variable === null) {
    throw new InvalidRequestException(
      new FeedbackItemResolution(
        GraphQLSpecErrorFeedbackItemProvider::class,
        GraphQLSpecErrorFeedbackItemProvider::E_5_8_3,
        [
          $this->name,
        ]
      )
    );
  }

  return $this->variable->getValue();
}

```

在迁移上述代码以使用错误代码之前，逻辑更容易理解:

```
public function getValue(): mixed
{
  if ($this->variable === null) {
    throw new InvalidRequestException(
      \sprintf(
        'Variable \'%s\' has not been defined in the operation',
        $this->name
      )
    );
  }

  return $this->variable->getValue();
}

```

### 通用测试套件？对他们不太确定

最初的版本提出了通用错误代码，作为支持通用测试套件的一种方式，它可以在不同的 GraphQL 服务器上工作。

我不确定这真的行得通。至少，它不会为我的服务器。

提议的测试套件需要与平台/技术/语言无关，以便适用于 GraphQL 服务器的所有不同实现。因此，[将被实现为验收测试](https://github.com/graphql-java/graphql-java/issues/1826#issuecomment-601540032)，针对 GraphQL 服务器运行实例的单个端点执行请求。

我的 GraphQL 服务器是 WordPress 的一个实现。如果我要执行验收测试套件，我需要启动一个完整的 WordPress 环境，包括一个 MySQL 数据库的实例。这反过来会给我的基于 GitHub Actions 的 CI 增加很多复杂性。

相反，我依赖于完全模仿 WordPress 功能的单元测试，所以我不仅不需要实际的 MySQL 实例，而且运行测试也不需要 WordPress，这使得测试更加简单、快速和便宜。

如果验收测试可用，我实际上很难利用它们。因为相同的验证已经通过我已经创建的单元测试进行了测试，所以我不会真的费心使用验收测试。

之前一个将通用测试套件引入 GraphQL 的尝试，叫做 [Cats](https://github.com/graphql-cats/graphql-cats) ，类似的[遇到了几个问题](https://github.com/graphql-java/graphql-java/issues/1826#issuecomment-601488238%20target=)，使得这个提议不切实际。

虽然通用测试套件的想法很吸引人，但对我来说，完成它所需的努力似乎不值得。

## 结论

将通用错误代码添加到 GraphQL 规范中值得吗？我的印象是肯定的，但不是因为通用测试套件的可用性(这是最初问题中陈述的原因)。

我从支持错误代码中获得的好处是:

*   它允许应用程序处理代码(代码不会改变)，这是一个比消息更可靠的单元，消息可以更新
*   它鼓励使用集中位置来管理所有错误代码，这可以让开发人员更好地理解要执行的所有验证
*   它允许我跟踪哪些验证还没有实现
*   它允许以最小的成本提供额外的`specifiedBy`信息，指向错误的在线文档
*   它允许使用相同的逻辑向用户提供其他类型的消息，例如“警告”、“建议”和“通知”

我经历过的一个缺点是，查看执行验证的逻辑变得比以前更难理解，因为一个无意义的错误代码不能解释错误是关于什么的。

我遭受的另一个缺点是转换我的服务器代码库所需的时间和精力。整个重构花了大约一个月的时间，而我感觉自己在原地打转，花费精力只是为了到达我已经到达的地方，不断地感觉我应该把时间投入到为服务器添加新功能上。

但是作为迁移的结果，我现在对处理更健壮的代码库感到满意:单元测试得到了改进，引入了新类型的反馈消息(其他类型的消息可以在任何时候以最小的努力添加)，文档被返回给访问 API 的用户。总的来说，我觉得 API 的质量提高了。

由于利大于弊，我确信将通用错误代码添加到 GraphQL 规范中是值得追求的。

## 监控生产中失败和缓慢的 GraphQL 请求

虽然 GraphQL 有一些调试请求和响应的特性，但确保 GraphQL 可靠地为您的生产应用程序提供资源是一件比较困难的事情。如果您对确保对后端或第三方服务的网络请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/graphql-signup)

.

[![](img/432a3823c85b3fb72a206e6236a29f48.png)![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/graphql-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/graphql-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你网站上发生的每一件事。您可以汇总并报告有问题的 GraphQL 请求，以快速了解根本原因，而不是猜测问题发生的原因。此外，您可以跟踪 Apollo 客户机状态并检查 GraphQL 查询的键值对。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/graphql-signup)

.