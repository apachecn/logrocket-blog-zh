# 加速对 GraphQL 模式的修改

> 原文：<https://blog.logrocket.com/speeding-up-changes-to-the-graphql-schema/>

本文是正在进行的关于概念化、设计和实现 GraphQL 服务器的系列文章的一部分。本系列之前的文章有:

1.  *[设计 GraphQL 服务器以获得最佳性能](https://blog.logrocket.com/designing-graphql-server-optimal-performance/)*
2.  *[简化 GraphQL 数据模型](https://blog.logrocket.com/simplifying-the-graphql-data-model/)*
3.  *[graph QL 中模式优先与代码优先的开发](https://blog.logrocket.com/code-first-vs-schema-first-development-graphql/)*

* * *

Principled GraphQL 是一组创建、维护和操作数据图的最佳实践。由 [Apollo](https://apollographql.com/) 团队响应来自成千上万开发者的反馈而创建，它提出了创建 GraphQL 服务的最佳实践标准。从项目一开始就遵循这些原则会非常有帮助，尤其是如果您刚刚开始使用 GraphQL 的话。

在构成 Principled GraphQL 的 10 个最佳实践中，我们将重点关注 Integrity Principles 一节中的三个，它定义了应该如何创建、管理和展示一个图。

> 1.  **一张图**:你的公司应该有一个统一的图，而不是每个团队创建多个图
> 2.  **联合实现**:虽然只有一个图，但是该图的实现应该跨多个团队联合
> 3.  **在注册表中跟踪模式**:应该有一个真实的来源来注册和跟踪图形

前两个原则确立了图表是不同团队的人共同努力的结果，因为创建一个 GraphQL 服务不仅需要做出技术上的决定，还需要做出逻辑上的决定，比如如何建立一个全公司范围的流程，使每个人都能为同一个图表做出贡献。

假设销售团队拥有、管理和开发`Product`类型，该类型目前有下面的[模式](https://blog.logrocket.com/defining-types-for-your-graphql-api/)。

```
type Product {
  id: Int
  name: String!
  price: Int!
}

```

现在，另一个团队——比如说，教程团队——决定以折扣价推出教程。该团队希望在`Product`类型上添加一个名为`discountedPrice`的字段(因为在本例中，教程是一个产品)。团队可以通过几种方法实现这一点:

1.  **自主**——由于每个团队拥有自己的服务，指导团队可以创建一个从`Product`类型扩展而来的`TutorialProduct`类型，并在该类型下添加字段
2.  **委托** —辅导团队可以要求销售团队将该字段添加到`Product`类型中
3.  **跨境** —辅导团队可以直接将字段添加到`Product`类型中

Principled GraphQL 在第二个原则下对此进行了权衡，[联邦实现](https://principledgraphql.com/integrity#2-federated-implementation)，它声明“每个团队都应该负责维护暴露其数据和服务的那部分模式。”

根据这个方向，您可以应用上面三个选项中的任何一个，因为并不总是清楚一个服务在哪里结束，另一个服务在哪里开始。例如，如果`Product`类型应该由销售团队单独拥有，那么前两个选项将适用。如果这种类型足够通用，可以由销售团队和辅导团队的一组成员拥有，那么第三个选项也适用。

这些选择没有一个是完美的。让我们检查一下权衡，以帮助决定哪一个最适合我们的情况。

### 自治的

一个`discountedPrice`字段足够通用，将它添加到`Product`类型中是有意义的。例如，假设车间团队也需要为其车间(另一种类型的产品)访问`discountedPrice`字段，并且它必须创建一个新的`WorkshopProduct`，扩展`Product`来添加它。`discountedPrice`会存在几种类型，导致代码重复，打破了 DRY 原则，引入了 bug 的可能。

### 委托

这种选择很官僚，可能会造成瓶颈。例如，如果销售团队中没有人可以完成教程团队要求的工作，那么模式可能无法及时更新。此外，跨团队所需的沟通会产生开销(例如，召开会议解释工作、发送电子邮件等)。

### 跨界

谁拥有一个特定的服务还没有完全定义。它需要更好的文档来说明哪个人实现了给定的字段。

我更喜欢跨边界选项，因为与第二个选项不同，它使您能够通过避免团队间依赖的瓶颈快速迭代升级模式，同时保持模式干燥和精简，这与第一个选项不同。第三种选择可能最初看起来是最不适合联邦原则的选择。然而，没有什么是我们不能用一些聪明的架构来解决的。

现在让我们来探索一个 GraphQL 服务器的架构设计，它 a)使不同团队的人能够对同一个模式做出贡献，而不会覆盖彼此的工作或对贡献设置官僚障碍，b)让每个成员对他们的模式部分拥有所有权。

## 分散模式创建

当创建 GraphQL 模式时，团队必须拥有其实现的所有权。但是，模式中需要所有权的部分没有预先定义；它可以包含一组类型、一个特定的类型(`Product`)，甚至是一个类型中的一个字段(`discountedPrice`)。

此外，不同的团队对同一个领域可能有不同的需求。以属于`Product`类型的`discountedPrice`字段为例。虽然辅导团队提供 10%的折扣，但研讨会团队可能提供 20%的折扣，因此字段`discountedPrice`的分辨率必须是动态的，取决于上下文。

对于这两种情况，我们不想将`discountedPrice`字段分别重命名为`discountedPriceForTutorials`和`discountedPriceForWorkshops`。这样做会使模式过于冗长，而且没有必要在字段本身的签名中区分折扣。毕竟，折扣的概念对于所有产品都是一样的，所以它们都应该被命名为`discountedPrice`。产品作为参数传递给解析器；这将是运行时的区别因素。

对于我们的第一次迭代，我们将创建一个解析器函数，它对不同类型的产品进行不同的解析。

```
const resolvers = {
  Product: {
    discountedPrice: function(product, args) {
      if (product.type == "tutorial") {
        return getTutorialDiscountedPrice(product, args);
      }
      if (product.type == "workshop") {
        return getWorkshopDiscountedPrice(product, args);
      }
      return getDefaultDiscountedPrice(product, args);
    }
  }
}

```

在这个场景中，教程团队拥有功能`getTutorialDiscountedPrice`，车间团队拥有`getWorkshopDiscountedPrice`，销售团队拥有`getDefaultDiscountedPrice`。教程团队也应该拥有产品线`if (product.type == "tutorial") {`，但它目前属于销售团队。让我们解决这个问题。

对于我们的下一次迭代，我们将创建一个`combineResolvers`函数，它模仿 Redux 的 [`combineReducers`](https://redux.js.org/api/combinereducers/) 函数，组合来自不同团队的解析器。然后，每个团队为其产品类型提供自己的解析器实现，如下所示:

```
// Provided by the tutorials team
const tutorialResolvers = {
  Product: {
    discountedPrice: getTutorialDiscountedPrice,
  }
}

// Provided by the workshop team
const workshopResolvers = {
  Product: {
    discountedPrice: getWorkshopDiscountedPrice,
  }
}

// Provided by the sales team
const defaultResolvers = {
  Product: {
    discountedPrice: getDefaultDiscountedPrice,
  }
}

```

这些都结合成一个:

```
// Provided by the sales team
const combinedResolvers = combineResolvers(
  {
    tutorial: tutorialResolvers,
    workshop: workshopResolvers,
    default: defaultResolvers,
  }
)
const resolvers = {
  Product: {
    discountedPrice: function(product, args) {
      const productResolvers = combinedResolvers[product.type] || combinedResolvers["default"];
      return productResolvers.Product.discountedPrice(product, args);
    }
  }
}

```

第二次迭代看起来比第一次更好，但它仍然有相同的基本问题:解析器委托者，它是组合解析器并为每种产品类型找到合适的解析器的对象，必须知道教程和研讨会的实现存在。由于这段代码是由销售团队维护的，它需要一定程度的官僚作风和团队间的依赖，这是我们宁愿放弃的。

我们还有一点工作要做。

## 订阅解析程序

第三次也是最后一次迭代涉及两种设计模式的组合:

1.  [发布-订阅设计模式](https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern)，它将解析器委托者(销售团队拥有的“发布者”)与实际的解析器(教程和研讨会团队拥有的“订阅者”)分离开来
2.  [责任链设计模式](https://en.wikipedia.org/wiki/Chain-of-responsibility_pattern)，它使得分解器委托者在分解器链中一个接一个地询问每个产品分解器是否能够处理产品，直到找到合适的一个

订阅链时，每个解析程序都必须提供一个优先级编号。这决定了他们在供应链中的位置——优先级越高，他们越快被询问是否能处理该产品。然后，`default`解析器必须被置于最低优先级，并且它必须表明它处理所有类型的产品。

我已经用 PHP 为我自己的 [GraphQL 服务器实现了这个解决方案，所以从这里开始我们将切换到 PHP 来演示代码示例。](https://graphql-by-pop.com/)

唯一必须填写的字段是`id`字段。否则，类型最初是空的，没有任何字段。这些字段都是通过解析器提供的，解析器将它们自己附加到它们的预期类型。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

对于我们的例子，我们将把`Product`类型定义为一个`TypeResolver`类，只实现类型的名称(以及其他一些信息，在下面的代码中省略了)和它如何解析它的 ID。

```
class ProductTypeResolver extends AbstractTypeResolver
{
  public function getTypeName(): string
  {
    return 'Product';
  }

  public function getID(Product $product)
  {
    return $product->ID;
  }
}

```

然后我们将实现`FieldResolver`类的实例，它们将字段附加到特定的类型。销售团队提供了一个初始解析器，它实现了所有的基本字段，比如`name`和`price`，以及`discountedPrice`的默认实现，提供了 5%的折扣。

```
namespace MyCompany\Sales;

class ProductFieldResolver extends AbstractDBDataFieldResolver
{
  /**
   * Attach the fields to the Product type
   */
  public static function getClassesToAttachTo(): array
  {
    return [ProductTypeResolver::class];
  }

  /**
   * Fields to implement
   */
  public static function getFieldNamesToResolve(): array
  {
    return [
      'name',
      'price',
      'discountedPrice',
    ];
  }

  /**
   * Priority with which it is attached to the chain.
   * Priority 0 => added last
   */
  public static function getPriorityToAttachClasses(): ?int
  {
    return 0;
  }

  /**
   * Always process everything
   */
  public function resolveCanProcess(Product $product, string $fieldName, array $fieldArgs): bool
  {
    return true;
  }

  /**
   * Implementation of the fields
   */
  public function resolveValue(Product $product, string $fieldName, array $fieldArgs)
  {
    switch ($fieldName) {
      case 'name':
        return $product->name;
      case 'price':
        return $product->price;
      case 'discountedPrice':
        // By default, provide a discount of 5%
        return $product->price * 0.95;
    }

    return null;
  }
}

```

现在，教程团队(同样，工作室团队)可以实现自己的解析器，只有当产品是类型`tutorial`时才使用。

```
namespace MyCompany\Tutorials;

class ProductFieldResolver extends AbstractDBDataFieldResolver
{
  /**
   * Attach the fields to the Product type
   */
  public static function getClassesToAttachTo(): array
  {
    return [ProductTypeResolver::class];
  }

  /**
   * Fields to implement
   */
  public static function getFieldNamesToResolve(): array
  {
    return [
      'discountedPrice',
    ];
  }

  /**
   * Priority with which it is attached to the chain.
   * Priority 10 => it is placed in the chain before the default resolver
   */
  public static function getPriorityToAttachClasses(): ?int
  {
    return 10;
  }

  /**
   * Process products of type "tutorial"
   */
  public function resolveCanProcess(Product $product, string $fieldName, array $fieldArgs): bool
  {
    return $product->type == "tutorial";
  }

  /**
   * Implementation of the fields
   */
  public function resolveValue(Product $product, string $fieldName, array $fieldArgs)
  {
    switch ($fieldName) {
      case 'discountedPrice':
        // Provide a discount of 10%
        return $product->price * 0.90;
    }

    return null;
  }
}

```

## 快速迭代

这种策略的美妙之处在于模式可以是动态的，根据上下文改变其形状和属性。你所需要做的就是订阅一个额外的解析器来处理一个特殊的情况，并在不再需要的时候把它取出来。这允许快速迭代和 bug 修复(比如实现一个特殊的解析器来处理来自受 bug 影响的客户端的请求)，而不用担心代码中其他地方的副作用。

按照我们之前的示例，教程团队可以覆盖`discountedPrice`字段的实现，以便为本周末的 flash 交易提供更大的折扣。这样，他们可以避免在周六晚上打扰销售团队的同事。

```
namespace MyCompany\Tutorials;

class FlashDealProductFieldResolver extends ProductFieldResolver
{
  /**
   * Priority with which it is attached to the chain.
   * Priority 20 => it is placed at the very beginning!
   */
  public static function getPriorityToAttachClasses(): ?int
  {
    return 20;
  }

  /**
   * Process tutorial products just for this weekend
   */
  public function resolveCanProcess(Product $product, string $fieldName, array $fieldArgs): bool
  {
    $now = new DateTime("now");
    $dealStart = new DateTime("2020-03-28");
    $dealEnd = new DateTime("2020-03-30");
    return $now >= $dealStart && $now <= $dealEnd && parent::resolveCanProcess($product, $fieldName, $fieldArgs);
  }

  /**
   * Implementation of the fields
   */
  public function resolveValue(Product $product, string $fieldName, array $fieldArgs)
  {
    switch ($fieldName) {
      case 'discountedPrice':
        // Provide a discount of 30%
        return $product->price * 0.70;
    }

    return null;
  }
}

```

## 结论

前端开发人员喜欢使用 GraphQL，因为这使他们能够自主地编写查询，从一个 GraphQL 端点获取数据来驱动他们的组件——而不像 REST 那样依赖任何人来提供所需的端点。也就是说，只要满足查询所需的所有解析器都已经实现。否则，这可能是不正确的，因为仍然必须有人实现这些解析器，而且这个人甚至可能属于不同的团队，从而产生一些瓶颈和官僚主义。

当创建 GraphQL 模式本身时，也会发生类似的事情:团队必须能够在一个共享的、公司范围的模式上协作，并且在不依赖其他团队的情况下自主地这样做，以避免官僚主义和瓶颈，并提供快速迭代。

现在，您应该对实现类型解析器的体系结构策略有了基本的了解，这可以最大限度地减少团队之间的交互，同时仍然允许每个团队拥有自己的模式部分。由此产生的架构提供了快速的迭代，并允许团队在特定的基础上从模式中提取解析器，使模式动态化。

## 监控生产中失败和缓慢的 GraphQL 请求

虽然 GraphQL 有一些调试请求和响应的特性，但确保 GraphQL 可靠地为您的生产应用程序提供资源是一件比较困难的事情。如果您对确保对后端或第三方服务的网络请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/graphql-signup)

.

[![](img/432a3823c85b3fb72a206e6236a29f48.png)![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/graphql-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/graphql-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你网站上发生的每一件事。您可以汇总并报告有问题的 GraphQL 请求，以快速了解根本原因，而不是猜测问题发生的原因。此外，您可以跟踪 Apollo 客户机状态并检查 GraphQL 查询的键值对。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/graphql-signup)

.