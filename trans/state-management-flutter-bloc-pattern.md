# 使用 BLoC 设计模式的颤振状态管理

> 原文：<https://blog.logrocket.com/state-management-flutter-bloc-pattern/>

在开发 Flutter 应用程序时，您可能需要将一个大的 UI 组件拆分成几个小的组件，以提高代码的可读性。对于多个组件，实现它们之间的有效通信至关重要。所有的 UI 组件都应该知道应用程序的状态。这就是所谓的状态管理。

在 Flutter 中，你只需使用`setState`就可以管理你的应用程序的状态。虽然`setState`可以成为你最好的朋友，但完全依赖它并不是一个好主意。在开发 Flutter 应用程序时，还有许多其他因素也应该考虑，如架构、可伸缩性、可读性、复杂性等。保持领先需要有效的状态管理技术。

有无数的[状态管理解决方案](https://flutter.dev/docs/development/data-and-backend/state-mgmt/options)可供 Flutter 使用，包括 Provider、InheritedWidget 和 InheritedModel、Redux、BLoC、GetIt、MobX、Riverpod 等。在本教程中，我们将重点关注使用 [BLoC 设计模式](https://flutter.dev/docs/development/data-and-backend/state-mgmt/options#bloc--rx)进行颤振状态管理。我们将解释 BLoC 的含义，并演示如何在 BLoC 中实现任何功能。

## 什么是阻塞？

业务逻辑组件(BLoC)允许您将业务逻辑与 UI 分离开来。以块的形式编写代码使得编写和重用测试更加容易。

简单地说，BLoC 接受事件流，基于事件处理数据，并以状态的形式产生输出。以下面的简单例子为例:

![Process Events and Produces States](img/78f0b71298e6f8ae9f691c5c8e717453.png)

当**旋转 90 度**按钮被点击后，`RotateEvent`被分派到 BLoC 并发出代表旋转的状态，即`RotatedState`。三角形小部件在收到来自集团的`RotatedState`时会自动旋转。类似地，当点击**将颜色变为红色**按钮时，圆形小部件改变其颜色。

由于 BLoC 处理旋转和改变颜色操作，这两种操作都可以在任何小部件上执行。这促进了代码的可重用性。

## 重要集团概念

在我们开始之前，让我们回顾一下一些基本的集团概念和术语，这样我们就在同一页上了。

### 事件

事件告诉 BLoC 做一些事情。事件可以从任何地方触发，比如从 UI 小部件。外部事件，如网络连接的变化、传感器读数的变化等。，看起来像这样:

```
class RotateEvent {
  final double angle;

  const RotateEvent(this.angle);

  @override
  List<Object> get props => [angle];
}

```

### 集团

布洛克是中间的人。所有的业务逻辑都位于 BLoC 文件中。它只是接受事件，执行逻辑，并输出状态。它看起来是这样的:

```
class TransformationBloc
    extends Bloc<TransformationEvent, TransformationState> {
  TransformationBloc() : super(RotatedState(angle: 0);

  @override
  Stream<TransformationState> mapEventToState(
      TransformationEvent event) async* {
    if (event is RotateEvent) {
      yield RotatedState(angle: event.angle);
    }
  }
}

```

### 州

状态表示任何小部件要处理的信息。小部件会根据状态改变自身。

```
class RotatedState {
  final double angle;

  const RotatedState({@required this.angle});

  @override
  List<Object> get props => [angle];
}

```

### 肘

[Cubit](https://pub.dev/documentation/flutter_cubit/latest/) 是 BLoC 模式的一个简单版本。它消除了编写事件的需要。

Cubit 公开直接函数，这可以产生适当的状态。编写 Cubit 而不是 BLoC 还可以减少样板代码，使代码更容易阅读。

这里有一个简单的例子:

```
class TransformCubit extends Cubit<TransformState> {
  TransformCubit() : super(RotatedState(angle: 0));

  void rotate(double angle) {
    emit(RotatedState(angle: angle));
  }

}

```

## 用`setState`管理颤振状态(无阻塞)

在我们强调用 BLoC 在 Flutter 中管理状态的好处之前，让我们来看一下使用`setState`进行状态管理的过程。

我们的示例 Flutter 应用程序将显示可用产品的列表。通过单击产品名称旁边的图标，可以在购物车中添加或删除产品。购物车中的商品数量会相应更新:

![Cart Updating](img/5c346a6aa841d383d79a167d1b567268.png)

使用`setState`，整个 UI 被分成三个类:

1.  `home.dart`是支撑脚手架和 [AppBar](https://blog.logrocket.com/flutter-appbar-tutorial/) 的主文件。AppBar 包含购物车图标小部件
2.  `product_list.dart`显示产品列表
3.  `product_tile.dart`显示单个产品项目。

它看起来是这样的:

![UI Broken Into Three Classes](img/1e47d78871493b8ba7f93781a2defd8a.png)

购物车中的商品列表从`Home`(顶部)一直传递到`ProductTile`(底部)小部件，以检查给定的商品是否存在于购物车中。如果是，购物车图标将高亮显示。

![Cart Icon Highlighted](img/cc95560e453e3f13307cdab52926a196.png)

单击产品名称旁边的购物车图标，将商品添加到购物车中。从`ProductTile`(底部)到`Home`(顶部)进行回调以刷新 AppBar 上的购物车图标。

![Callback to Refresh Cart](img/4965d2172e274725bde15bd642f9c27d.png)

### `setState`的问题

在 Flutter 中，状态管理的`setState`方法适用于只有几个组件的简单应用程序。但是对于更复杂的、具有深度部件树的真实应用程序，使用`setState`会导致以下问题:

*   代码重复——数据必须从所有部件传递到底层部件，这使得代码难以阅读
*   提升一个`setState`到一个具有深层次的父窗口小部件会导致不必要的重绘，从而导致性能下降

## 如何用阻塞管理颤振中的状态

现在让我们用 BLoC 实现同样的特性。

首先，添加 [BLoC 库](https://bloclibrary.dev/):

```
dependencies:
  flutter:
    sdk: flutter
  cupertino_icons: ^1.0.2
  flutter_bloc: ^7.0.0

```

接下来，创建并添加一个 BLoC observer。这有助于您确定已经发生的事件和状态的顺序，这对调试应用程序非常有用。

```
void main() {
  Bloc.observer = SimpleBlocObserver();
  runApp(MyApp());
}
import 'package:flutter_bloc/flutter_bloc.dart';

/// Custom [BlocObserver] which observes all bloc and cubit instances.
class SimpleBlocObserver extends BlocObserver {
  @override
  void onEvent(Bloc bloc, Object event) {
    super.onEvent(bloc, event);
    print(event);
  }

  @override
  void onTransition(Bloc bloc, Transition transition) {
    super.onTransition(bloc, transition);
    print(transition);
  }

  @override
  void onError(BlocBase bloc, Object error, StackTrace stackTrace) {
    print(error);
    super.onError(bloc, error, stackTrace);
  }
}

```

创建事件以在购物车商品列表中添加和删除产品:

```
import 'package:equatable/equatable.dart';

abstract class CartEvent extends Equatable {
  const CartEvent();

  @override
  List<Object> get props => [];
}

class AddProduct extends CartEvent {
  final int productIndex;
  const AddProduct(this.productIndex);
  @override
  List<Object> get props => [productIndex];
  @override
  String toString() => 'AddProduct { index: $productIndex }';
}

```

现在，创建状态来表示正在添加和删除的产品:

```
import 'package:flutter/material.dart';

abstract class CartState {
  final List<int> cartItem;
  const CartState({@required this.cartItem});

  @override
  List<Object> get props => [];
}

class ProductAdded extends CartState {
  final List<int> cartItem;
  const ProductAdded({@required this.cartItem}) : super(cartItem: cartItem);

  @override
  List<Object> get props => [cartItem];
  @override
  String toString() => 'ProductAdded { todos: $cartItem }';
}

```

编写业务逻辑，在`cartItems`中添加和删除产品，并发出各自的状态。购物车中的实际商品列表在块级别维护。

```
class CartBloc extends Bloc<CartEvent, CartState> {
  CartBloc() : super(ProductAdded(cartItem: []));

  final List<int> _cartItems = [];
  List<int> get items => _cartItems;

  @override
  Stream<CartState> mapEventToState(CartEvent event) async* {
    if (event is AddProduct) {
      _cartItems.add(event.productIndex);
      yield ProductAdded(cartItem: _cartItems);
    } else if (event is RemoveProduct) {
      _cartItems.remove(event.productIndex);
      yield ProductRemoved(cartItem: _cartItems);
    }
  }
}

```

接下来，将 scaffold 小部件包装在`[BlocProvider](https://pub.dev/packages/flutter_bloc#blocprovider)`中。

`BlocProvider`是一个 Flutter 小部件，它使得任何块对它下面的整个小部件树都可用。在我们的例子中，在`Home`(顶部)和`ProductTile`(底部)之间的任何小部件都可以访问购物车，所以不需要将购物车数据从小部件树的顶部传递到底部。

```
BlocProvider(
    create: (_) => CartBloc(),
    child: Scaffold(
      appBar: CartCounter(),
      body: ProductList(),
    ));

```

将购物车图标和产品列表包装在`BlocBuilder`中。`BlocBuilder`从 BLoC 接收新状态后，简单地在其中重建小部件。

```
// Cart icon
BlocBuilder<CartBloc, CartState>(builder: (_, cartState) {
  List<int> cartItem = cartState.cartItem;
  return Positioned(
    left: 30,
    child: Container(
      padding: EdgeInsets.all(5),
      decoration: BoxDecoration(
          borderRadius: BorderRadius.circular(10),
          color: Colors.red),
      child: Text(
        '${cartItem.length}',
        style: TextStyle(fontWeight: FontWeight.bold),
      ),
    ),
  );
}),
//Product list
 BlocBuilder<CartBloc, CartState>(builder: (_, cartState) {
  List<int> cart = cartState.cartItem;
  return LayoutBuilder(builder: (context, constraints) {
    return GridView.builder(
      itemCount: 100,
      itemBuilder: (context, index) => ProductTile(
        itemNo: index,
        cart: cart,
      ),
      gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
        crossAxisCount: constraints.maxWidth > 700 ? 4 : 1,
        childAspectRatio: 5,
      ),
    );
  });
});

```

注意:`CartBloc`的`BlocBuilder`只在两个地方添加，因为我们只希望这两个小部件在`CartBloc`发生事情时重建。这种只刷新所需窗口小部件的方法大大减少了不必要的重绘次数。

下一步是向`CartBloc`发送事件，以添加和删除购物车中的商品。`BlocProvider.of<CartBloc>(context)`在窗口小部件树中找到最近的`CartBloc`实例，并将事件添加到其中:

```
IconButton(
  key: Key('icon_$itemNo'),
  icon: cart.contains(itemNo)
      ? Icon(Icons.shopping_cart)
      : Icon(Icons.shopping_cart_outlined),
  onPressed: () {
    !cart.contains(itemNo)
        ? BlocProvider.of<CartBloc>(context).add(AddProduct(itemNo))
        : BlocProvider.of<CartBloc>(context).add(RemoveProduct(itemNo));
  },
)

```

现在用`BlocConsumer`代替`BlocBuilder`。`BlocConsumer`允许我们重新构建小部件并对状态做出反应。只有当您想要重新构建小部件并执行某些操作时，才应该使用它。

在我们的示例中，我们希望每当在购物车中添加或删除产品时，都刷新列表并显示一个 snackbar:

```
BlocConsumer<CartBloc, CartState>(
listener: (context, state) { 
  Scaffold.of(context).showSnackBar(
    SnackBar(
      content: Text(
          state is ProductAdded ? 'Added to cart.' : 'Removed from cart.'),
      duration: Duration(seconds: 1),
    ),
  );
}, 
builder: (_, cartState) {
  List<int> cart = cartState.cartItem;
  return LayoutBuilder(builder: (context, constraints) {
    return GridView.builder();
  });
});

```

或者，如果您想减少一些样板代码，并且状态的顺序对您来说并不重要，那么尝试 Cubit。下面是`CartCubit`的样子:

```
class CartCubit extends Cubit<CartState> {
  CartCubit() : super(ProductAdded(cartItem: []));

  final List<int> _cartItems = [];
  List<int> get items => _cartItems;

  void add(int productIndex) {
    _cartItems.add(productIndex);
    emit (ProductAdded(cartItem: _cartItems));
  }

  void remove(int productIndex) {
    _cartItems.remove(productIndex);
    emit (ProductRemoved(cartItem: _cartItems));
  }
}

```

注意:在整个代码中将`CartBloc`替换为`CartCubit`,并触发如下所示的事件:

```
onPressed: () {
  !cart.contains(itemNo)
      ? BlocProvider.of<CartCubit>(context).add(itemNo)
      : BlocProvider.of<CartCubit>(context).remove(itemNo);
},

```

输出是相同的，但是状态管理得到了改进:

![Cart Updating](img/5c346a6aa841d383d79a167d1b567268.png)

## 结论

拥有一个稳固的块架构可以很好地分离关注点。尽管使用 BLoC 模式比使用`setState`需要更多的代码，但它使代码更具可读性、可伸缩性和可测试性。

在本教程中，我们介绍了在 Flutter 中使用 BLoC 模式的基础知识，并通过一个实际的例子强调了与`setState`方法相比，在 Flutter 中使用 BLoC 进行状态管理的好处。

你可以在 [GitHub](https://github.com/pinkeshdarji/flutter_bloc_demo) 上找到这个例子的完整源代码。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)