# 创建第一个转换 PHP 代码的 Rector 规则的技巧

> 原文：<https://blog.logrocket.com/creating-first-rector-rule-transform-php-code/>

[Rector](https://github.com/rectorphp/rector) 是一个 PHP 的重构工具。它将源代码和转换规则作为输入，并根据规则修改代码作为输出。

即使我们没有想到，我们也经常使用工具来转换 PHP 代码。例如， [PHP 代码嗅探器](https://github.com/squizlabs/PHP_CodeSniffer)可以验证代码是否符合`PSR`标准，如果不符合，执行`phpcbf`命令可以自动修复。或者 [PHP-Scoper](https://github.com/humbug/php-scoper) 将确定项目中的依赖关系以避免潜在的冲突。

Rector 与这些工具的不同之处在于它是一个元应用程序。它没有一个预先确定的目标，例如固定样式或项目范围。相反，它将[按照规则转换代码](https://blog.logrocket.com/transpiling-php-code-from-8-0-to-7-x-via-rector/),不管给出的是什么规则。然后，Rector 可以完美地重现 PHP CodeSniffer，PHP-Scoper，或者其他任何代码转换工具。

在本文中，我将分享一些在 Rector 中创建规则的技巧。

## 教区长的柱子

雷克脱站在两个巨人的肩膀上:

*   PHP 解析器:一个解析 PHP 代码的库，支持静态代码分析和操作
*   PHPStan :一个静态分析工具

多亏了 PHP 解析器，Rector 可以使用 AST(抽象语法树的缩写)中的节点来操作代码。由于 PHPStan，Rector 可以理解代码，因此它能够映射、浏览和验证代码中实体之间的关系，比如获取一个类或其所有实现的接口的祖先。

在开始使用 Rector 之前，最好对这两个库有一个基本的了解，并在我们使用 Rector 时不断学习它们的文档。事实上，Rector 规则越复杂，掌握这两个库就变得越重要。

## 什么是教区长规则？

规则是一个继承自`[AbstractRector](https://github.com/rectorphp/rector/blob/master/src/Rector/AbstractRector.php)`的 PHP 类，它执行来自 AST(对应于解析的 PHP 文件)的节点上的转换。

它由三个主要方法组成，我们必须满足:

*   `getRuleDefinition`:用于记录规则
*   `getNodeTypes`:规则将应用于什么类型的节点
*   `refactor`:产生新 AST 节点的逻辑

例如，规则`DowngradeNullCoalescingOperatorRector`用 PHP 7.3 中的等价操作符替换了 PHP 7.4 中引入的`??=`操作符。它有[这个实现](https://github.com/rectorphp/rector/blob/a7705ed0a59c7c0ff8619c07ec48f489fb90787f/rules/downgrade-php74/src/Rector/Coalesce/DowngradeNullCoalescingOperatorRector.php):

```
use PhpParser\Node;
use PhpParser\Node\Expr\Assign;
use PhpParser\Node\Expr\AssignOp\Coalesce as AssignCoalesce;
use PhpParser\Node\Expr\BinaryOp\Coalesce;
use Rector\Core\Rector\AbstractRector;
use Symplify\RuleDocGenerator\ValueObject\CodeSample\CodeSample;
use Symplify\RuleDocGenerator\ValueObject\RuleDefinition;

final class DowngradeNullCoalescingOperatorRector extends AbstractRector
{
  public function getRuleDefinition(): RuleDefinition
  {
    return new RuleDefinition('Remove null coalescing operator ??=', [
      new CodeSample(
        <<<'CODE_SAMPLE'
$array = [];
$array['user_id'] ??= 'value';
CODE_SAMPLE
        ,
        <<<'CODE_SAMPLE'
$array = [];
$array['user_id'] = $array['user_id'] ?? 'value';
CODE_SAMPLE
      ),
    ]);
  }

  /**
   * @return string[]
   */
  public function getNodeTypes(): array
  {
    return [AssignCoalesce::class];
  }

  /**
   * @param AssignCoalesce $node
   */
  public function refactor(Node $node): ?Node
  {
    return new Assign($node->var, new Coalesce($node->var, $node->expr));
  }
}
```

让我们看看它是如何工作的。

### `getRuleDefinition`

我们必须提供转换前后的代码示例。然后，Rector 使用这两个状态来记录更改，使用 diff 格式，就像这里的[所做的那样](https://github.com/rectorphp/rector/blob/master/docs/rector_rules_overview.md#downgradenullcoalescingoperatorrector):

```
 $array = [];
-$array['user_id'] ??= 'value';
+$array['user_id'] = $array['user_id'] ?? 'value';

```

### `getNodeTypes`

在这个函数中，我们指出将在 AST 的哪个节点上应用转换。这些节点[直接来自 PHP 解析器](https://github.com/nikic/PHP-Parser/tree/master/lib/PhpParser/Node)。

在上面的例子中，规则只应用于类型为`[Coalesce](https://github.com/nikic/PHP-Parser/blob/46221a091439122f97334e34c03f667fa54ad326/lib/PhpParser/Node/Expr/AssignOp/Coalesce.php)`(别名为`AssignCoalesce`)的节点，这是代表`??=`的节点。

其他节点的一些示例如下:

*   `FuncCall`:每当调用一个函数，比如`var_dump("hello")`
*   `MethodCall`:每当从类中调用方法时，比如`$foo->bar()`
*   `Assign`:通过`=`赋值时
*   `Equal`、`NotEqual`、`Identical`、`NotIdentical`:分别使用二元运算符`==`、`!=`、`===`或`!==`时

### `refactor`

如果需要，这个函数执行转换。它有返回类型`?Node`，意思是:

*   要么返回一个新节点，它将替换旧节点；或者
*   返回`null`，表示无变化

请注意，返回`null`表示“不修改节点”；这并不意味着“删除节点”

上面的规则旨在用等价的`$foo = $foo ?? $bar`替换`$foo ??= $bar`。函数`refactor`然后返回这个新节点:

```
return new Assign(
  $node->var,
  new Coalesce(
    $node->var,
    $node->expr
  )
);
```

新节点的类型是`[Assign](https://github.com/nikic/PHP-Parser/blob/master/lib/PhpParser/Node/Expr/Assign.php)`，也就是`$foo = $foo ?? $bar`中的`=`。这种类型需要两个元素:

*   从原始节点检索的变量`$foo`，作为`$node->var`
*   表情`$foo ?? $bar`

为了创建表达式，我们在上面嵌套了一个新的节点，类型为`[Coalesce]([https://github.com/nikic/PHP-Parser/blob/master/lib/PhpParser/Node/Expr/BinaryOp/Coalesce.php](https://github.com/nikic/PHP-Parser/blob/master/lib/PhpParser/Node/Expr/BinaryOp/Coalesce.php))`，也就是`$foo ?? $bar`中的`??`。coalesce 运算符需要两个元素:

*   左边的表达式`$foo`，作为`$node->var`从原始节点检索
*   右边的表达式`$bar`，作为`$node->expr`从原始节点检索

此示例显示了创建规则所涉及的基本概念:

*   找到满足目标代码的新节点
*   确定它需要的数据
*   将数据(变量、表达式)从旧节点移植到新节点

## 重用现有规则中的代码

在撰写本文时，Rector repo 提供了[近 700 条规则](https://github.com/rectorphp/rector/blob/master/docs/rector_rules_overview.md)，涉及多种转换。这些现有的规则是帮助我们实现我们自己的定制规则的极好资源。

所以这是我的建议:无论何时你需要创建一个定制规则，首先检查一个相似的逻辑是否已经在任何现有的规则中被编码。很有可能会有。

例如，我实现了规则`[DowngradeStripTagsCallWithArrayRector](https://github.com/rectorphp/rector/blob/d465ec83e2c2886c51d86beb9bf9e0bcfc84eef7/rules/downgrade-php74/src/Rector/FuncCall/DowngradeStripTagsCallWithArrayRector.php)`，它将传递给`strip_tags`的数组参数——受 PHP 7.3 支持——转换成一个字符串参数，可用于 PHP 7.3 及更低版本:

```
-strip_tags($string, ['a', 'p']);
+strip_tags($string, '<' . implode('><', ['a', 'p']) . '>');

```

现在，我们可能不知道静态分析期间参数的类型。例如，此函数返回字符串或数组:

```
function getStringOrArray() {
  if (rand(0, 1)) {
    return ['a', 'p'];
  }
  return 'ap';
}

```

然后，我们的代码需要在运行时检查参数的类型:

```
-strip_tags($string, getStringOrArray());
+strip_tags($string, is_array(getStringOrArray()) ? ( '<' . implode('><', getStringOrArray()) . '>' ) : getStringOrArray());

```

但是现在我们有一个问题:函数`getStringOrArray()`被执行两次，这可能是昂贵的，或者更糟，它可能产生意想不到的副作用(例如，如果它增加一个全局计数器，它将做两次)。

因此，解决方案是首先将`getStringOrArray()`中的值赋给一个变量:

```
-strip_tags($string, getStringOrArray());
+$var = getStringOrArray();
+strip_tags($string, is_array($var) ? ( '<' . implode('><', $var) . '>' ) : $var);

```

但是，我不能随机选择变量的名称为`$var`(或其他名称),因为它可能已经存在，并且我将覆盖它的值:

```
 $var = "blah blah blah";
-strip_tags($string, getStringOrArray());
+$var = getStringOrArray();
+strip_tags($string, is_array($var) ? ( '<' . implode('><', $var) . '>' ) : $var);
 var_dump($var);
 // It expects "blah blah blah". It got "ap"

```

我不知道该怎么处理这件事。因此，我浏览了 repo 中所有规则的[列表，检查它们中是否有任何一个可以处理这个问题，即使用一个未使用的名称创建一个新变量。](https://github.com/rectorphp/rector/blob/master/docs/rector_rules_overview.md)

我找到了。规则`[ForRepeatedCountToOwnVariableRector](https://github.com/rectorphp/rector/blob/master/docs/rector_rules_overview.md#forrepeatedcounttoownvariablerector)`进行这种转换:

```
 class SomeClass
 {
   public function run($items)
   {
-    for ($i = 5; $i <= count($items); $i++) {
+    $itemsCount = count($items);
+    for ($i = 5; $i <= $itemsCount; $i++) {
       echo $items[$i];
     }
   }
 }

```

变量`$itemsCount`不知从哪里被创造出来。检查它是如何完成的，我发现了`VariableNaming`服务，它可以识别变量`$itemsCount`是否已经存在。如果是的话，它会再次尝试`$itemsCount2`，依此类推，直到找到一个没有添加的变量名。

然后我复制/粘贴了使用服务的逻辑，从这里开始:

```
$variableName = $this->variableNaming->resolveFromFuncCallFirstArgumentWithSuffix(
  $node,
  'Count',
  'itemsCount',
  $forScope
);

```

总的来说，我觉得 Rector repo 中的源代码非常优雅。我特别喜欢它大量使用了 Symfony 组件，包括依赖注入、CLI 命令、文件和目录查找器。在浏览它的时候，我学到了很多关于编程最佳实践的知识，所以我建议你也这样做。

## 测试提示

这里有一些测试与 [PHPUnit](https://phpunit.de/) 相关的规则的有用提示。

当执行`phpunit`来测试一个规则时，如果该规则有许多测试，并且只有一个失败，我们可以通过传递`--filter=test#X`来只执行那一个，其中`X`是夹具测试的顺序号。

例如，当执行以下命令时:

```
vendor/bin/phpunit rules/solid/tests/Rector/Class_/FinalizeClassesWithoutChildrenRector/FinalizeClassesWithoutChildrenRectorTest.php
```

我会得到这个错误:

```
There was 1 failure:

1) Rector\DowngradePhp73\Tests\Rector\List_\DowngradeListReferenceAssignmentRector\DowngradeListReferenceAssignmentRectorTest::test with data set #4 (Symplify\SmartFileSystem\SmartFileInfo Object (...))
rules/downgrade-php73/tests/Rector/List_/DowngradeListReferenceAssignmentRector/Fixture/nested_list.php.inc
Failed asserting that string matches format description.
```

从这个错误中，我们可以知道测试`nested_list.php.inc`是`#4`，所以我只能像这样执行这个测试:

```
vendor/bin/phpunit rules/solid/tests/Rector/Class_/FinalizeClassesWithoutChildrenRector/FinalizeClassesWithoutChildrenRectorTest.php --filter=test#4
```

这对于调试很有用，可以快速简单地将输出转储到屏幕上，从而直观地看到问题可能出在哪里。

如果我们需要转储节点的内容，我们可以在规则类中这样做，如下所示:

```
dump($this->print($node));
die;

```

我们必须使用来自 [Symfony 的 VarDumper 组件](https://symfony.com/doc/current/components/var_dumper.html)的`dump`，而不是`var_dump`，因为:

*   它格式化输出，使其更容易理解
*   节点可能包含循环引用，`dump`识别并停止它们，但是`var_dump`没有，所以屏幕上的输出会一直持续下去

## 结论

Rector 是一个转换 PHP 代码的好工具。我用它来把我的应用从 PHP 7.4 移植到 7.1，这样我就可以用现代的 PHP 特性对它进行编码，并把它部署到我的客户支持的环境中。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)