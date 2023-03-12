# 使用 Symfony DI 构建可扩展的 PHP 应用程序

> 原文：<https://blog.logrocket.com/building-extensible-php-apps-with-symfony-di/>

当构建复杂的 PHP 应用程序时，我们可以依靠依赖注入和服务容器来管理应用程序中对象或“服务”的实例化。

有几个依赖注入库满足 [PSR-11](https://www.php-fig.org/psr/psr-11/) ，这是描述“容器接口”契约的 PHP 标准建议:

GitHub 上有 3.4K 的星星，Symfony 的 DependencyInjection 比同类库更上一层楼。它非常强大，但使用简单。因为所有服务必须如何初始化的逻辑可以作为 PHP 文件生成和转储，所以在生产中运行很快。它可以被配置为同时服务于 PHP 和 YAML。它很容易理解，因为它有广泛的文档支持。

使用服务容器已经有助于管理复杂的应用程序。同样重要的是，服务容器减少了外部开发人员为我们的应用程序编写代码的需求。

例如，我们的 PHP 应用程序可以通过模块进行扩展，第三方开发者可以编写他们自己的扩展。通过使用服务容器，我们使他们更容易将他们的服务注入到我们的应用程序中，即使他们对我们的应用程序如何工作没有深刻的理解。这是因为我们可以编写规则来定义服务容器如何初始化服务，并自动化这个过程。

这种自动化转化为开发人员不必再做的工作。因此，他们不需要理解服务如何初始化的内部细节；这由服务容器负责。

虽然开发人员仍然需要理解依赖注入和容器服务背后的概念，但是通过使用 dependency injection 库，我们可以简单地将他们引向主题为的 [Symfony 文档。减少我们需要维护的文档数量会让我们更开心，并释放时间和资源来处理我们的代码。](https://symfony.com/doc/current/service_container.html)

在本文中，我们将看一些如何使用 DependencyInjection 库使 PHP 应用程序更具可扩展性的例子。

## 使用编译器阶段

[编译器通道](https://symfony.com/doc/current/service_container/compiler_passes.html)是库的机制，用于在编译[服务容器之前修改容器中服务的初始化和调用方式](https://symfony.com/doc/current/components/dependency_injection/compilation.html)。

一个编译器传递的对象必须实现 [`CompilerPassInterface`](https://symfony.com/doc/current/components/dependency_injection/compilation.html#creating-separate-compiler-passes) :

```
use Symfony\Component\DependencyInjection\Compiler\CompilerPassInterface;
use Symfony\Component\DependencyInjection\ContainerBuilder;

class OurCustomPass implements CompilerPassInterface
{
  public function process(ContainerBuilder $container)
  {
    // ... do something during the compilation
  }
}

```

要在我们的应用程序中注册，我们需要执行以下操作:

```
use Symfony\Component\DependencyInjection\ContainerBuilder;

$containerBuilder = new ContainerBuilder();
$containerBuilder->addCompilerPass(new OurCustomPass());

```

我们可以根据需要注入[多个编译器通道](https://github.com/leoloso/PoP/blob/52204de3e8e3183a450cde5b5dd51a295d2bc7d1/layers/Engine/packages/root/src/Container/ContainerBuilderFactoryTrait.php):

```
// Inject all the compiler passes
foreach ($compilerPasses as $compilerPass) {
  $containerBuilder->addCompilerPass($compilerPass);
}
// Compile the container
$containerBuilder->compile();

```

## 自动初始化服务

通过一次编译，我们可以自动初始化某种类型的服务——例如，从某个类扩展的任何类，实现某些接口，将某个[服务标签](https://symfony.com/doc/current/service_container/tags.html)分配给它的定义，或者一些其他自定义行为。

让我们看一个例子。我们将让我们的 PHP 应用程序通过调用其`initialize`方法来自动初始化任何实现 [`AutomaticallyInstantiatedServiceInterface`](https://github.com/leoloso/PoP/blob/572e9a316656415f42ee7e1b2e62080c467b2513/layers/Engine/packages/root/src/Services/AutomaticallyInstantiatedServiceInterface.php) 的对象:

```
interface AutomaticallyInstantiatedServiceInterface
{
  public function initialize(): void;
}

```

然后我们可以[创建一个编译器通道](https://github.com/leoloso/PoP/blob/e6afe0f53537e17fa255a54b4cd9429d6f7ba86c/layers/Engine/packages/root/src/Container/CompilerPasses/AbstractInstantiateServiceCompilerPass.php)，它将迭代容器中定义的所有服务的列表，并识别那些实现`AutomaticallyInstantiatedServiceInterface`的服务:

```
class AutomaticallyInstantiateServiceCustomPass implements CompilerPassInterface
{
  public function process(ContainerBuilder $container)
  {
    $definitions = $container->getDefinitions();
    foreach ($definitions as $definitionID => $definition) {
      $definitionClass = $definition->getClass();
      if ($definitionClass === null || !is_a($definitionClass, AutomaticallyInstantiatedServiceInterface::class, true)) {
        continue;
      }

      // $definition is a AutomaticallyInstantiatedServiceInterface
      // Do something with it
      // ...
    }
  }
}

```

接下来，我们将创建一个名为`ServiceInstantiatorInterface`的服务，它将由[负责初始化被识别的服务](https://github.com/leoloso/PoP/blob/d7a40f1c17ca81adacf0a521114104713c5ff95d/layers/Engine/packages/root/src/Container/ServiceInstantiatorInterface.php)。使用`addService`方法，它将收集所有要初始化的服务，并且它的方法`initializeServices`将最终被 PHP 应用程序调用:

```
interface ServiceInstantiatorInterface
{
  public function addService(AutomaticallyInstantiatedServiceInterface $service): void;
  public function initializeServices(): void;
}

```

该服务的实现可在 [GitHub](https://github.com/leoloso/PoP/blob/d7a40f1c17ca81adacf0a521114104713c5ff95d/layers/Engine/packages/root/src/Container/ServiceInstantiator.php) 上获得:

```
class ServiceInstantiator implements ServiceInstantiatorInterface
{
  /**
   * @var AutomaticallyInstantiatedServiceInterface[]
   */
  protected array $services = [];

  public function addService(AutomaticallyInstantiatedServiceInterface $service): void
  {
    $this->services[] = $service;
  }

  public function initializeServices(): void
  {
    foreach ($this->services as $service) {
      $service->initialize();
    }
  }
}

```

现在，我们可以通过将所有已识别的服务注入到`ServiceInstantiatorInterface`服务中来完成上面的编译器代码:

```
class AutomaticallyInstantiateServiceCustomPass implements CompilerPassInterface
{
  public function process(ContainerBuilder $container)
  {
    $serviceInstantiatorDefinition = $container->getDefinition(ServiceInstantiatorInterface::class);
    $definitions = $container->getDefinitions();
    foreach ($definitions as $definitionID => $definition) {
      $definitionClass = $definition->getClass();
      if ($definitionClass === null) {
        continue;
      }
      if (!is_a($definitionClass, AutomaticallyInstantiatedServiceInterface::class, true)) {
        continue;
      }

      // $definition is a AutomaticallyInstantiatedServiceInterface
      // Do something with it
      $serviceInstantiatorDefinition->addMethodCall(
        'addService',
        [new Reference($definitionID)]
      );
    }
  }
}

```

作为一个服务本身，`ServiceInstantiatorInterface`的定义也可以在服务容器中找到。因此，要获得对该服务的引用，我们必须:

```
$serviceInstantiatorDefinition = $container->getDefinition(ServiceInstantiatorInterface::class);

```

我们不使用实例化的对象/服务，因为我们还没有它们。相反，我们正在处理容器上服务的定义。这也是为什么，要将一个服务注入到另一个服务中，我们不能这样做:

```
$serviceInstantiator->addService(new $definitionClass());

```

而是必须这样做:

```
$serviceInstantiatorDefinition->addMethodCall(
  'addService',
  [new Reference($definitionID)]
);

```

PHP 应用程序在启动时必须[触发服务](https://github.com/leoloso/PoP/blob/d7a40f1c17ca81adacf0a521114104713c5ff95d/layers/Engine/packages/root/src/Component.php#L77)的初始化:

```
$serviceInstantiator->initializeServices();

```

最后，我们让那些需要自动初始化的服务实现`AutomaticallyInstantiatedServiceInterface`。

在这个例子中，我们的应用程序使用了`SchemaConfiguratorExecuter`服务。它们的祖先类`[AbstractSchemaConfiguratorExecuter](https://github.com/leoloso/PoP/blob/ad33f7e1bcb83309718272008b11079582b3a718/layers/GraphQLAPIForWP/plugins/graphql-api-for-wp/src/Services/SchemaConfiguratorExecuters/AbstractSchemaConfiguratorExecuter.php)`已经满足了初始化逻辑，如下所示:

```
abstract class AbstractSchemaConfiguratorExecuter implements AutomaticallyInstantiatedServiceInterface
{
  public function initialize(): void
  {
    if ($customPostID = $this->getCustomPostID()) {
      $schemaConfigurator = $this->getSchemaConfigurator();
      $schemaConfigurator->executeSchemaConfiguration($customPostID);
    }
  }

  /**
   * Provide the ID of the custom post containing the Schema Configuration block
   */
  abstract protected function getCustomPostID(): ?int;

  /**
   * Initialize the configuration of services before the execution of the GraphQL query
   */
  abstract protected function getSchemaConfigurator(): SchemaConfiguratorInterface;
}

```

现在，任何第三方开发者想要创建自己的`SchemaConfiguratorExecuter`服务，只需要创建一个继承自`AbstractSchemaConfiguratorExecuter`的类，满足抽象方法，并在自己的服务容器配置中定义该类即可。

然后，服务容器将按照应用程序生命周期的要求，负责实例化和初始化该类。

## 注册但不初始化服务

在某些情况下，我们可能希望禁用某项服务。在我们的示例 PHP 应用程序中，WordPress 的 [GraphQL 服务器允许用户从 GraphQL 模式中删除类型。如果网站上的博客文章没有显示评论，那么我们可以跳过将`Comment`类型添加到模式中。](https://graphql-api.com)

`[CommentTypeResolver](https://github.com/leoloso/PoP/blob/master/layers/GraphQLAPIForWP/plugins/graphql-api-for-wp/vendor/pop-schema/comments/src/TypeResolvers/CommentTypeResolver.php)`是将`Comment`类型添加到模式中的服务。要跳过向模式中添加该类型，我们所要做的就是不要在容器中注册该服务。

但是这样做，我们会遇到一个问题:如果任何其他服务已经将`CommentTypeResolver`注入其中(比如[这个](https://github.com/leoloso/PoP/blob/a947061cf095b804e1819c2513e4cbbcf414c1ec/layers/GraphQLAPIForWP/plugins/graphql-api-for-wp/src/ModuleResolvers/SchemaTypeModuleResolver.php#L90)，那么实例化将会失败，因为 DependencyInjection 不知道如何解析那个服务，并且会抛出一个错误:

```
Fatal error: Uncaught Symfony\Component\DependencyInjection\Exception\RuntimeException: Cannot autowire service "GraphQLAPI\GraphQLAPI\ModuleResolvers\SchemaTypeModuleResolver": argument "$commentTypeResolver" of method "__construct()" references class "PoPSchema\Comments\TypeResolvers\CommentTypeResolver" but no such service exists. in /app/wordpress/wp-content/plugins/graphql-api/vendor/symfony/dependency-injection/Compiler/DefinitionErrorExceptionPass.php:54

```

这意味着`CommentTypeResolver`和所有其他服务必须总是在容器服务中注册——也就是说，除非我们绝对确定它不会被其他服务引用。如下所述，我们的示例应用程序中的一些服务只在管理员端可用，所以我们可以跳过面向用户端的注册。

从模式中删除`Comment`类型的解决方案必须是实例化服务，这应该没有副作用，但不要初始化它，否则会产生副作用。

为了实现这一点，我们可以在注册服务时使用 [`autoconfigure`属性](https://symfony.com/doc/current/service_container.html#the-autoconfigure-option)来指示服务必须被初始化:

```
services:
  PoPSchema\Comments\TypeResolvers\CommentTypeResolver:
    class: ~
    autoconfigure: true

```

我们可以[更新编译器通道](https://github.com/leoloso/PoP/blob/e6afe0f53537e17fa255a54b4cd9429d6f7ba86c/layers/Engine/packages/component-model/src/Container/CompilerPasses/AbstractAttachExtensionCompilerPass.php#L37)，只将那些带有`autoconfigure: true`的服务注入`ServiceInstantiatorInterface`:

```
class AutomaticallyInstantiateServiceCustomPass implements CompilerPassInterface
{
  public function process(ContainerBuilder $container)
  {
    // ...
    foreach ($definitions as $definitionID => $definition) {
      // ...

      if ($definition->isAutoconfigured()) {
        // $definition is a AutomaticallyInstantiatedServiceInterface
        // Do something with it
        $serviceInstantiatorDefinition->addMethodCall(
          'addService',
          [new Reference($definitionID)]
        );
      }
    }
  }
}

```

## 指示有条件的服务初始化

上面的解决方案是可行的，但是它有一个大问题:定义服务是否必须初始化必须在服务定义文件上设置，该文件在容器编译时被访问——也就是说，在我们可以开始在应用程序中使用服务之前。在某些情况下，我们可能还希望基于运行时值禁用服务，例如当管理员用户通过应用程序设置禁用`Comment`类型时，应用程序设置保存在数据库中。

为了解决这个问题，我们可以让服务本身指示它是否必须被初始化。为此，我们向其接口添加了`isServiceEnabled`方法:

```
interface AutomaticallyInstantiatedServiceInterface
{
  // ...
  public function isServiceEnabled(): bool;
}

```

例如，我们的示例 PHP 应用程序中的一个服务实现了这个方法[，就像这个](https://github.com/leoloso/PoP/blob/57a7a21a378f718e1ecfd378fe34ea99fa62c168/layers/GraphQLAPIForWP/plugins/graphql-api-for-wp/src/Services/Scripts/AbstractScript.php#L42):

```
abstract class AbstractScript implements AutomaticallyInstantiatedServiceInterface
{
  /**
   * Only enable the service, if the corresponding module is also enabled
   */
  public function isServiceEnabled(): bool
  {
    $enablingModule = $this->getEnablingModule();
    return $this->moduleRegistry->isModuleEnabled($enablingModule);
  }
}

```

最后，`ServiceInstantiatorInterface`服务可以识别那些必须初始化的服务:

```
class ServiceInstantiator implements ServiceInstantiatorInterface
{
  // ...

  public function initializeServices(): void
  {
    $enabledServices = array_filter(
      $this->services,
      fn ($service) => $service->isServiceEnabled()
    );
    foreach ($enabledServices as $service) {
      $service->initialize();
    }
  }
}

```

这样，我们不仅可以在配置服务容器时跳过服务初始化，还可以在运行应用程序时动态地跳过服务初始化。

## 为不同的行为注册不同的容器服务

PHP 应用程序不仅限于一个服务容器。例如，应用程序可以根据给定的条件表现不同，比如在管理员端或面向用户端。这意味着，根据上下文，应用程序将需要注册不同的服务集。

为了实现这一点，我们可以将`services.yaml`配置文件分成几个子文件，并在需要时注册它们。

[应该总是加载`services.yaml`](https://github.com/leoloso/PoP/blob/902c230e2a78b7dd737caf83e5dfd580b429f236/layers/GraphQLAPIForWP/plugins/graphql-api-for-wp/config/services.yaml) 的这个定义，因为它将注册在`Services/`下找到的所有服务:

```
services:
  _defaults:
    public: true
    autowire: true

  GraphQLAPI\GraphQLAPI\Services\:
    resource: 'src/Services/*'

```

这个[对`Conditional/Admin/services.yaml`](https://github.com/leoloso/PoP/blob/8ee284c2eb55d8abf559ea6521b3a8ea2167acb7/layers/GraphQLAPIForWP/plugins/graphql-api-for-wp/config/ConditionalOnEnvironment/Admin/services.yaml) 的其他定义是有条件的，仅当在管理端注册在`Conditional/Admin/Services/`下找到的所有服务时加载:

```
services:
  _defaults:
    public: true
    autowire: true

  GraphQLAPI\GraphQLAPI\Conditional\Admin\Services\:
    resource: 'src/Conditional/Admin/Services/*'

```

后面的代码总是注册第一个文件，但在管理端只注册第二个文件:

```
self::initServices('services.yaml');
if (is_admin()) {
  self::initServices('Conditional/Admin/services.yaml');
}

```

现在我们必须记住，对于生产，DependencyInjection 会将编译好的服务容器转储到一个 PHP 文件中。我们还需要生成两个不同的转储，并为每个上下文加载相应的转储:

```
public function getCachedContainerFileName(): string
{
  $fileName = 'container_cache';
  if (is_admin()) {
    $fileName .= '_admin';
  }
  return $fileName . '.php';
}

```

## 建立对配置的约定

约定优于配置是为项目建立规范的艺术，以应用不仅有效，而且减少开发人员所需的配置数量的标准行为。

这种策略的实现可能需要我们将某些文件放在某些文件夹中。例如，为了实例化某个框架的`EventListener`对象，我们可能需要将所有相应的文件放在一个`EventListeners`文件夹下，或者给它分配一个`app\EventListeners`名称空间。

请注意编译器传递是如何消除这种需求的。为了识别服务并以特殊方式对待它，服务必须扩展某个类、实现某个接口、被分配某个服务标签或显示某个其他自定义行为——无论它位于何处。

感谢编译器的传递，我们的 PHP 应用程序可以自然地为开发人员创建扩展提供配置约定，同时减少不便。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 通过文件夹结构公开有关服务的信息

即使我们不需要将文件放在任何特定的文件夹中，如果应用程序除了初始化服务之外还有其他用途，我们仍然可以为它设计一个逻辑结构。

在我们的示例 PHP 应用程序中，让文件夹结构传达哪些服务是可用的，它们是否必须在容器中隐式定义，以及在什么上下文中它们将被添加到容器中。

为此，我使用下面的结构:

*   访问特定服务的所有门面都归入`Facades/`
*   所有总是被初始化的服务都归入`Services/`
*   所有条件服务，根据上下文可能初始化也可能不初始化，都在`Conditional/{ConditionName}/Services`下
*   由一些包提供的覆盖默认实现的服务的所有实现都归入`Overrides/Services`
*   所有通过契约而不是直接作为实现来访问的服务，比如服务`ServiceInstantiatorInterface`，可以放在任何地方，因为它们在容器[中的定义必须是显式的](https://github.com/leoloso/PoP/blob/d7a40f1c17ca81adacf0a521114104713c5ff95d/layers/Engine/packages/root/config/hybrid-services.yaml):

```
services:
  _defaults:
    public: true
    autowire: true
  PoP\Root\Container\ServiceInstantiatorInterface:
    class: \PoP\Root\Container\ServiceInstantiator

```

我们使用什么样的结构完全取决于我们自己，取决于我们应用程序的需求。

## 结论

为 PHP 应用程序创建一个健壮的架构，即使只是为我们自己的开发团队，也已经是一个挑战了。对于这些情况，使用依赖注入和容器服务可以大大简化任务。

最重要的是，如果我们还需要允许第三方——他们可能不完全了解应用程序如何工作——提供扩展，挑战就变得更大了。当使用 DependencyInjection 组件时，我们可以创建编译器通道来自动配置和初始化应用程序，消除了开发人员的这一需求。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)