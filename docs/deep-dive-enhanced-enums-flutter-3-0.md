# 深入探究 Flutter 3.0 中增强的枚举

> 原文：<https://blog.logrocket.com/deep-dive-enhanced-enums-flutter-3-0/>

Flutter 3.0 引入了一些期待已久的变化，其中最主要的是我们可以在 Flutter 中使用枚举的方式，这是我们许多人都在急切期待的！对于那些在其他语言中使用了更健壮的枚举的人来说尤其如此，他们发现自己在从事 Flutter 项目时错过了它们。

我们现在可以使用新的内置 Flutter enum 特性在一个地方实现所有功能，而不是使用静态方法、方法扩展以及帮助器或实用程序类。

这是一个相当新的概念，因为我们大多数人都习惯于使用旧的方法来处理这种情况，所以在这方面肯定有很多项目需要重构。在这篇文章中，我将深入探讨你需要了解的关于 Flutter 3.0 中枚举的内容。

*向前跳转:*

## 什么是枚举？

Enum 代表枚举类型，一种只存在一组预定义值的数据类型。在 Dart 中，`Enum`只是一种特殊的类，用于表示固定数量的常数值。

下面是一个`enum`类的例子:

```
enum OperatingSystem { macOS, windows, linux }
```

在我们继续之前，有必要记住 Dart 文档中的一个重要注释，我在这里引用它，因为我认为它很好地总结了 enums:

> 所有枚举都自动扩展枚举类。它们也是密封的，这意味着它们不能被子类化、实现、混合或显式实例化。
> 
> 抽象类和 mixin 可以显式实现或扩展 enum，但除非它们由 Enum 声明实现或混合到 Enum 声明中，否则没有对象可以实际实现该类或 mixin 的类型。

## enums 在 Flutter 3.0 之前是如何工作的

让我们坚持上面的例子，想象这些是可以使用 Flutter 的平台。我们希望有一个属性来说明一个特定的平台类型是否可以为 iOS 设备构建一个应用。

如何将属性或方法添加到枚举中？现在让我们来看看最流行的解决方案。

第一个解决方案是创建一个扩展方法，如下所示:

```
extension OperatingSystemExtension on OperatingSystem {
        bool switch (this) {
            case OperatingSystem.macOS: 
        return true;
      case OperatingSystem.windows: 
      case OperatingSystem.linux: 
        return false; 
    }
  }
}

```

或者，您可以从一个枚举转移到一个具有内部构造函数和作为“枚举”值的静态常量的类:

```
class OperatingSystem {
  final _value;
  const OperatingSystem._internal(this._value);
  toString() => 'OperatingSystem.$_value';
  static const macOS = const OperatingSystem._internal('macOS');
  static const windows = const OperatingSystem._internal('windows');
  static const linux = const OperatingSystem._internal('linux');
}

```

最后，您可以在静态方法中使用 util 或 helper 类，但在我看来，这似乎是比其他两个选项更糟糕的代码实践:

```
class OperatingSystemHelper{
  static bool canBuildForIos(OperatingSystem os){
    switch(os){
      case OperatingSystem.macOS: 
        return true;
      case OperatingSystem.windows: 
      case OperatingSystem.linux: 
        return false; 
    }
  }

```

很丑吧？看起来有很多多余的代码与 enum 本身是分开的，并且比它应该的更难维护。

此外，如果你想应用一个 mixin 或一个接口来提供开箱即用的定制排序，那么你就不走运了——你不能在旧版本的`Enum`中做到这一点，因为它也必须单独存在。

显然，您可以将所有内容存储在一个文件中，这没什么大不了的——事实上，我们已经接受它有一段时间了，但是如果某些东西可以编码得更好，那么为什么不这样做呢？

## 增强的枚举在 Flutter 3.0 中如何工作

那么，有哪些期待已久的`Enum`改进呢？

### 枚举可以有属性

```
enum OperatingSystem {
  macOS(true, true),
  windows(false, true),
  linux(false, true);
  const OperatingSystem(this.canBuildForIos, this.canBuildForAndroid);
  final bool canBuildForIos;
  final bool canBuildForAndroid;
}

```

好多了，不是吗？一个构造函数可以像任何其他类一样有命名参数或位置参数，只要它仍然是一个`const`构造函数。

(注:如果不是`const`，就不会编译，会给你一个警告，不用担心)

### 枚举可以像任何类一样有方法

```
enum OperatingSystem {
  macOS(true, true),
  windows(false, true),
  linux(false, true);
  const OperatingSystem(this.canBuildForIos, this.canBuildForAndroid);
  final bool canBuildForIos;
  final bool canBuildForAndroid;
        bool get isBestOperatingSystemForFlutterDevelopment => canBuildForIos && canBuildForAndroid;
}

```

然而，一个生成构造函数必须是`const`，所以实际上这些可能只是额外的 getters。

如果一个构造函数必须是`const`，那么所有的实例变量也必须是 final。工厂构造函数只能返回一个固定的枚举实例。

显然，颤振支持的平台更多，而且不存在颤振开发客观上更优越的操作系统；这只是展示该特性的一个简化示例。

增强的`enum`看起来更像一个普通的类，所以我们现在也可以实现接口和混合。

```
mixin Foo {
  bool get canBuildForIos;
}

enum OperatingSystem with Foo implements Comparable<OperatingSystem>{
  macOS(true, true),
  windows(false, true),
  linux(false, true);

  const OperatingSystem(this.canBuildForIos, this.canBuildForAndroid);
  final bool canBuildForAndroid;

        bool get isBestOperatingSystemForFlutterDevelopment => canBuildForIos && canBuildForAndroid;

        @override
  final bool canBuildForIos;

        @override
        int compareTo(OperatingSystem other) => name.length - other.name.length;
}

```

## 无商标消费品

增强的枚举也允许我们对枚举使用泛型，就像这样:

```
enum OperatingSystem<T> {
  macOS<String>('Yes I can'),
  windows<int>(0),
  linux(false);

  const OperatingSystem(this.canBuildForIos);
  final T canBuildForIos;
}

print(OperatingSystem.values.map((e) => e.canBuildForIos));

//reuslts in
//(Yes I can, 0, false)

```

您可能已经注意到，在 Linux 中，类型推理是有效的。

## 限制

应该注意增强的枚举带来的一些约束。

增强型枚举不能扩展其他类，因为它会自动扩展`Enum`——这是意料之中的。您也不能覆盖`index`、`hashCode`或等号运算符，但这是意料之中的；我们通常习惯于在任何情况下默认这些 enum 实现。

为了确保它是可读的，所有的实例都必须在开始时声明，并且必须至少有一个。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

总而言之，我们现在可以为枚举和 getters 声明变量的最终实例，并且可以实现接口。如果你仔细想想，随之而来的其他约束是相当明显的！

## 为什么增强型枚举是一种改进？

为什么增强型枚举比静态类的限制少？

```
class OperatingSystem {
  final _value;
  final bool canBuildForIos;
  const OperatingSystem._internal(this._value, this.canBuildForIos);

  toString() => 'OperatingSystem.$_value';

  static const macOS = const OperatingSystem._internal('macOS', true);
  static const windows = const OperatingSystem._internal('windows', false);
  static const linux = const OperatingSystem._internal('linux', false);
}

enum OperatingSystem {
  macOS(true),
  windows(false),
  linux(false);

  const OperatingSystem(this.canBuildForIos);
  final bool canBuildForIos;
}

```

将这两者结合在一起，看起来可读性更好，代码更少，并且用于预期目的。

可能导致一些问题的一个副作用是，如果您希望针对枚举实例使用 switch 语句，并且使用自定义类，那么如果您没有覆盖所有值，您将不会得到警告。然而，对于 enums，你会得到一个警告，我相信你已经见过几次了。所以，是赞成 enums 的两点！

## 如何重构我现有的枚举？

如果您使用扩展，那么只要将您拥有的任何东西移动到 enum 类，只要它是一个`getter`或`final`属性。由于本地化通常依赖于`BuildContext`，根据您的 l10n 解决方案，这些可能仍然必须存在于方法扩展中。

如果你使用带有内部构造函数的类，情况是一样的；只需将代码更改为新的枚举，除非它不符合您的需要。

## 我可以用它来定义单例吗？

这是一个有趣的小怪癖，你可以使用一个增强的 enum 来创建一个 singleton，只需要比正常情况下更少的代码，但是构造函数必须是`const`并且所有的实例变量必须是 final——所以在大多数情况下，这样的 singleton 没有真正的好处或用途。

因此，任何函数都不会有任何副作用，因此它们和顶级静态函数一样有用。当我们看代码风格时，Dart 更喜欢顶层函数，而不是只有静态成员的类。

```
//preferred
String get usecase => "Am I am useless? true";

//enum way 

enum OperatingSystem {
  uselessSingleton(true);

  const OperatingSystem(this.canBuildForIos);
  final bool canBuildForIos;

        String get usecase => "Am I am useless? $canBuildForIos";
}

//same result
print(usecase);
print(OperatingSystem.uselessSingleton.usecase);

```

## 结论

增强的枚举是 Flutter dev 社区长期以来一直要求的，我喜欢他们与开发人员交流他们的发布，并提供一个简单直观的解决方案来解决我们现有的大部分棘手问题。

这当然不是一个复杂的概念，但我认为我们应该更加关注它，因为我总是发现自己忘记了它们！此外，许多关于增强型枚举的教程已经过时了，尽管它们有许多用途，所以让我们跟上这些变化总是有帮助的。

我建议至少在现有项目中开始使用增强的枚举，并在可行的情况下重构枚举。所有约束都会产生编译时错误，并且会立即被您选择的 IDE 突出显示，因此您不必担心任何回归。

如果你是个超级书呆子，并且想知道关于增强型枚举的所有事情，以及它们在幕后是如何工作的，这里的是它们的一个很好的说明。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)