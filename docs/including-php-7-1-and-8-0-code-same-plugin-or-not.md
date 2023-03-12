# 在同一个插件中包含 PHP 7.1 和 8.0 代码…或者不包含？

> 原文：<https://blog.logrocket.com/including-php-7-1-and-8-0-code-same-plugin-or-not/>

我最近写了很多关于 PHP 代码转换的文章(这里是，[这里是](https://blog.logrocket.com/coding-in-php-7-4-and-deploying-to-7-1-via-rector-and-github-actions/)，这里是[这里是](https://blog.logrocket.com/tips-transpiling-code-from-php-8-0-to-7-1/))，描述了我们如何使用最新的 PHP 代码进行开发，但是发布我们的包/插件/应用程序的遗留版本，转换我们的代码从 PHP 8.0 到 7.1。

我已经把我的 WordPress 插件从 PHP 8.0 升级到了 7.1。我对结果非常满意，因为我的代码库已经提高了质量:我现在可以使用类型化属性和联合类型，否则我无法负担一个公共 WordPress 插件的费用。

然而，我仍然不是 100%满意。在解决最初的挑战(为 WordPress 编码时能够使用 PHP 8.0)的同时，transpiling 代码也带来了一些新的问题。

## 传输代码的问题

通过在 PHP 8.0 中编写我的插件，然后在 PHP 7.1 中发布它，我遇到了以下三个问题:

### 1.扩展需要用 PHP 7.1 编写方法签名，即使它们需要 PHP 8.0

我的插件，一个 WordPress 的 GraphQL 服务器，允许开发者通过创建一个实现`TypeResolverInterface`的对象，用他们自己的类型扩展 GraphQL 模式。其中，该接口具有功能`getID`，具有[该签名](https://github.com/leoloso/PoP/blob/ce8744ffadcf9af0dabe8dc62a4c2b138348bda8/layers/Engine/packages/component-model/src/TypeResolvers/TypeResolverInterface.php#L12):

```
interface TypeResolverInterface
{
  public function getID(object $resultItem): string|int;
}

```

正如我们所看到的，这个函数使用 PHP 8.0 中的联合类型来指定返回类型，使用 PHP 7.2 中的`object`参数类型。

当转换到 PHP 7.1 时，这个方法签名被降级为[这个代码](https://github.com/GraphQLAPI/graphql-api-for-wp-dist/blob/master/vendor/getpop/component-model/src/TypeResolvers/TypeResolverInterface.php#L13):

```
interface TypeResolverInterface
{
  /**
   * @param $resultItem object
   * @return string|int
   */
  public function getID($resultItem);
}

```

这个方法签名是在插件中发布的。

那么，当开发人员想为我的插件创建一个扩展并将其部署到运行在 PHP 8.0 上的应用程序时，会发生什么呢？嗯，他们仍然需要使用 PHP 7.1 代码进行方法签名，即去掉`object` param 类型和`string|int`返回类型；否则，PHP 将抛出一个错误。

幸运的是，这种情况仅限于方法签名。例如，扩展仍然可以使用联合类型来声明它们的类的属性:

```
class IcecreamTypeResolver implements IcecreamTypeResolverInterface
{
  // PHP 8.0 code here is allowed
  private string|int $id = 'vanilla';

  /**
   * PHP 7.1 code in method signature...
   *
   * @param $resultItem object
   * @return string|int
   */
  public function getID($resultItem)
  {
    return $this->id;
  }
}

```

然而，当我们的应用程序需要 PHP 8.0 时，却不得不使用 PHP 7.1 代码，这仍然令人恼火。作为一个插件提供者，强迫我的用户进入这种情况感觉有点悲哀。

(要明确的是，我不是在创造情境；当覆盖任何支持 PHP 7.1 的 WordPress 插件的方法签名时，也会发生同样的情况。但是在这种情况下感觉不同，只是因为我从 PHP 8.0 开始，目标是为我的用户提供更好的选择。)

### 2.必须使用 PHP 7.1 提供文档

因为插件是在 PHP 7.1 上发布的，所以扩展它的文档也必须使用 PHP 7.1 作为方法签名，即使原始源代码是在 PHP 8.0 上。

此外，文档不能指向带有 PHP 8.0 源代码的回购协议，否则我们将冒着访问者复制/粘贴一段代码的风险，这将产生 PHP 错误。

最后，我们开发人员通常以使用最新版本的 PHP 为荣。但是插件的文档不能反映这一点，因为它仍然基于 PHP 7.1。

我们可以通过向我们的访问者解释编译过程来解决这些问题，鼓励他们也用 PHP 8.0 编写他们的扩展，然后将其转换到 PHP 7.1。但是这样做会增加认知的复杂性，降低他们使用我们软件的机会。

### 3.调试信息使用转换后的代码，而不是源代码

假设插件抛出一个异常，在某个`debug.log`文件上打印出这个信息，我们使用堆栈跟踪来定位源代码上的问题。

那么，堆栈跟踪中显示的发生错误的行将指向转换后的代码，并且行号很可能与源代码中的不同。因此，为了从 transpiled 转换回原始代码，还需要做一些额外的工作。

## 第一个建议的解决方案:生产两个版本的插件

要考虑的最简单的解决方案是生成两个版本，而不是一个:

*   其中一个带有完整的 PHP 7.1 代码
*   一个带有 PHP 8.0 原始代码的

这很容易实现，因为 PHP 8.0 的新版本将只包含原始源代码，没有任何修改。

有了使用 PHP 8.0 代码的第二个插件，任何在 PHP 8.0 上运行站点的开发者都可以使用这个插件。

### 产生两个版本插件的问题

这种方法有几个问题，我认为，使它不切实际。

#### WordPress 每个插件只接受一个版本

对于像我这样的 WordPress 插件，我们不能把两个版本都上传到 WordPress 目录。因此，我们必须在它们之间做出选择，这意味着我们将最终拥有使用 PHP 7.1 的“官方”插件和使用 PHP 8.0 的“非官方”插件。

这使事情变得非常复杂，因为虽然官方插件可以上传到(和从)插件目录下载，但非官方插件不能——除非它作为不同的插件发布，这将是一个可怕的想法。因此，必须从其网站或回购网站下载。

此外，建议只从 wordpress.org/plugins 下载官方插件，以免与[指南](https://developer.wordpress.org/plugins/wordpress-org/detailed-plugin-guidelines/#3-a-stable-version-of-a-plugin-must-be-available-from-its-wordpress-plugin-directory-page)相混淆:

> 一个插件的稳定版本必须可以从它的 WordPress 插件目录页面获得。
> 
> WordPress.org 发布的插件的唯一版本是目录中的版本。尽管人们可能在其他地方开发他们的代码，但是用户将从目录中下载，而不是从开发环境中下载。
> 
> 通过替代方法分发代码，而不保持这里的代码是最新的，可能会导致插件被删除。

这实际上意味着我们的用户需要知道插件有两个不同的版本——一个官方版本和一个非官方版本——并且它们可以在两个不同的地方获得。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

这种情况可能会让不知情的用户感到困惑，这是我希望避免的。

#### 它没有解决文档问题

因为文档必须说明官方插件，它将包含 PHP 7.1 代码，然后发出“2。必须使用 PHP 7.1 提供文档”的情况仍然会发生。

#### 没有什么可以阻止插件被安装两次

必须在我们的持续集成过程中完成插件的传输。由于我的代码托管在 GitHub 上，所以每当标记代码时，插件都通过 GitHub Actions 生成，并作为发布资产上传[。](https://github.com/leoloso/PoP/releases/tag/0.7.13)

不能有两个同名的发布资产。目前，插件名称为`graphql-api.zip`。如果我也要用 PHP 8.0 代码生成并上传插件，我必须将其命名为`graphql-api-php80.zip`。

这可能会导致一个潜在的问题:任何人都可以在 WordPress 中下载并安装这两个版本的插件，由于它们有不同的名字，WordPress 会有效地将它们并排安装在文件夹`graphql-api`和`graphql-api-php80`下。

如果发生这种情况，我相信第二个插件的安装会失败，因为在不同的 PHP 版本中有相同的方法签名会产生 PHP 错误，使 WordPress 停止安装。但即使那样，我也不想冒这个险。

## 第二个建议的解决方案:在同一个插件中包含 PHP 7.1 和 8.0 代码

既然上面这个简单的解决方案并不是无懈可击的，那就该迭代了。

除了只使用 transpiled PHP 7.1 代码发布插件，还包括源 PHP 8.0 代码，并根据环境决定运行时是使用对应于一个 PHP 版本还是另一个 PHP 版本的代码。

让我们看看这将如何工作。我的插件目前将 PHP 代码放在两个文件夹中，`src`和`vendor`，这两个文件夹都转换为 PHP 7.1。采用新方法后，它将包含四个文件夹:

*   `src-php71`:代码转换到 PHP 7.1
*   `vendor-php71`:代码转换到 PHP 7.1
*   `src`:PHP 8.0 中的原始代码
*   `vendor`:PHP 8.0 中的原始代码

这些文件夹必须被命名为`src`和`vendor`而不是`src-php80`和`vendor-php80`，这样，如果我们在这些路径中的任何一个路径下硬编码了对某个文件的引用，它仍然可以工作，不需要任何修改。

加载`vendor`或`vendor-php71`文件夹的方式如下:

```
if (PHP_VERSION_ID < 80000) {
  require_once __DIR__ . '/vendor-php71/autoload.php';
} else {
  require_once __DIR__ . '/vendor/autoload.php';
}

```

加载`src`或`src-php71`文件夹是通过相应的`autoload_psr4.php`文件完成的。PHP 8.0 的版本保持不变:

```
<?php

// autoload_psr4.php @generated by Composer

$vendorDir = dirname(dirname(__FILE__));
$baseDir = dirname($vendorDir);

return array(
  'GraphQLAPI\\GraphQLAPI\\' => array($baseDir . '/src'),
);

```

但是转换到 PHP 7.1 的那个，在`vendor-php71/composer/autoload_psr4.php`下，必须把路径改成`src-php71`:

```
return array(
  'GraphQLAPI\\GraphQLAPI\\' => array($baseDir . '/src-php71'),
);

```

差不多就是这样。现在，插件可以在两个不同的 PHP 版本中发布它的代码，运行 PHP 8.0 的服务器可以使用 PHP 8.0 代码。

让我们看看这种方法是如何解决这三个问题的。

### 1.扩展可以使用 PHP 7.1 的方法签名

现在该插件仍然支持 PHP 7.1，但除此之外，它还支持在 web 服务器中运行 PHP 8.0 时使用原生 PHP 8.0 代码。因此，这两个 PHP 版本都是一等公民。

这样，运行 PHP 8.0 的 web 服务器将从相应的 PHP 8.0 版本加载方法签名:

```
interface TypeResolverInterface
{
  public function getID(object $resultItem): string|int;
}

```

为自己的网站扩展 GraphQL 模式的开发人员可以使用 PHP 8.0 方法签名来编写他们的扩展。

### 2.可以使用 PHP 8.0 提供文档

因为 PHP 8.0 成为了一等公民，所以文档将演示使用 PHP 8.0 的代码。

也可以从原始的 repo 中复制/粘贴源代码到文档中。为了演示 PHP 7.1 版本，我们可以简单地在 transpiled repo 中添加一个到相应代码段的链接。

### 3.调试信息尽可能使用原始代码

如果 web 服务器运行 PHP 8.0，调试中的堆栈跟踪将正确地打印原始源代码中的行号。

如果不运行 PHP 8.0，这个问题仍然会发生，但至少我们已经对它进行了改进。

## 为什么只有两个 PHP 版本？瞄准整个范围现在是可能的。

如果实现这个解决方案，将插件从仅使用 PHP 8.0 和 7.1 升级到使用介于两者之间的所有 PHP 版本是非常容易的。

我们为什么要这么做？改进解决方案项目“1。扩展可以使用上面提到的 PHP 7.1 的方法签名”，但是允许开发人员使用他们已经用于扩展的任何 PHP 版本。

例如，如果运行 PHP 7.3，前面提到的`getID`的方法签名不能使用联合类型，但是它可以使用`object`参数类型。所以扩展可以使用这个代码:

```
interface TypeResolverInterface
{
  /**
   * @return string|int
   */
  public function getID(object $resultItem);
}

```

实施此升级意味着存储版本中的所有中间降级阶段，如下所示:

*   `src-php71`:代码转换到 PHP 7.1
*   `vendor-php71`:代码转换到 PHP 7.1
*   `src-php72`:代码转换到 PHP 7.2
*   `vendor-php72`:代码转换到 PHP 7.2
*   `src-php73`:代码转换到 PHP 7.3
*   `vendor-php73`:代码转换到 PHP 7.3
*   `src-php74`:代码转换到 PHP 7.4
*   `vendor-php74`:代码转换到 PHP 7.4
*   `src`:PHP 8.0 中的原始代码
*   `vendor`:PHP 8.0 中的原始代码

然后，加载一个或另一个版本是这样完成的:

```
if (PHP_VERSION_ID < 72000) {
  require_once __DIR__ . '/vendor-php71/autoload.php';
} elseif (PHP_VERSION_ID < 73000) {
  require_once __DIR__ . '/vendor-php72/autoload.php';
} elseif (PHP_VERSION_ID < 74000) {
  require_once __DIR__ . '/vendor-php73/autoload.php';
} elseif (PHP_VERSION_ID < 80000) {
  require_once __DIR__ . '/vendor-php74/autoload.php';
} else {
  require_once __DIR__ . '/vendor/autoload.php';
}

```

## 在同一个插件中包含 PHP 7.1 和 8.0 代码的问题

这种方法最明显的问题是我们将复制插件的文件大小。

然而，在大多数情况下，这不是一个关键问题，因为这些插件运行在服务器端，对应用程序的性能没有任何影响(例如复制 JS 或 CSS 文件的大小)。最多，下载这个文件需要一点时间，在 WordPress 中安装也需要一点时间。

此外，只有 PHP 代码必然会被复制，但资产(如 CSS/JS 文件或图像)只能保留在`vendor`和`src`下，并在`vendor-php71`和`src-php71`下删除，因此插件的文件大小可能不到两倍大小。

所以没什么大不了的。

第二个问题更严重:公共扩展也需要用两个 PHP 版本编码。根据包/插件/应用程序的性质，这个问题可能是一个阻碍。

不幸的是，我的插件就是这种情况，我将在下面解释。

### 公共扩展也需要包含 PHP 8.0 和 7.1 代码

那些对所有人公开的扩展会发生什么？他们应该使用什么版本的 PHP？

例如，GraphQL API 插件允许用户扩展 GraphQL 模式以从任何其他 WordPress 插件获取数据。因此，第三方插件能够提供自己的扩展(想想“WooCommerce for GraphQL API”或“Yoast for GraphQL API”)。这些扩展也可以上传到 WordPress.org 插件库中，任何人都可以下载并安装到他们的网站上。

现在，这些扩展不会预先知道用户将使用哪个 PHP 版本。他们不能只使用一个版本(PHP 7.1 或 8.0)的代码，因为当使用另一个版本时，肯定会产生 PHP 错误。因此，这些扩展也需要在 PHP 7.1 和 8.0 中包含它们的代码。

从技术角度来看，这当然是可行的。但在其他方面，这是一个可怕的想法。尽管我喜欢编译我的代码，但我不能强迫别人也这么做。当强加如此高的要求时，我怎么能指望一个生态系统围绕着我的插件繁荣呢？

因此，我认为，对于 GraphQL API，遵循这种方法是不值得的。

## 那么，解决办法是什么？

让我们回顾一下目前的状态:

将代码从 PHP 8.0 移植到 7.1 有一些问题:

1.  扩展需要用 PHP 7.1 编写方法签名，即使它们需要 PHP 8.0
2.  必须使用 PHP 7.1 提供文档
3.  调试信息使用转换后的代码，而不是源代码

第一个提出的解决方案，生产两个版本的插件，效果不好，因为:

1.  WordPress 只接受每个插件的发布
2.  它没有解决文档问题
3.  没有什么可以阻止插件被安装两次

第二个建议的解决方案，在同一个插件中同时包含 PHP 7.1 和 8.0 代码，可能有效也可能无效:

*   如果插件可以被第三方扩展，这些扩展也需要被传输。这可能会增加进入的障碍，使它不值得
*   否则，它应该工作正常

在我的例子中，GraphQL API 受到第二个建议的解决方案的影响。然后，这是一个完整的循环，我又回到了我开始的地方——忍受着我试图找到解决方案的三个问题。

尽管有这个挫折，我并没有改变我对运输的积极看法。事实上，如果我不传输我的源代码，它将不得不使用 PHP 7.1(或者可能是 PHP 5.6)，所以我不会好到哪里去。(只有关于调试信息不指向源代码的问题会得到解决。)

## 包扎

我在这篇文章的开头描述了我将 WordPress 插件从 PHP 8.0 移植到 7.1 时遇到的三个问题。然后我提出了两个解决方案，第一个效果不会很好。

第二种解决方案会很好，除了可以由第三方扩展的包/插件/应用程序。我的插件就是这种情况，所以我又回到了起点，没有解决这三个问题的方法。

所以我还是对运输不太满意。只有 93%。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)