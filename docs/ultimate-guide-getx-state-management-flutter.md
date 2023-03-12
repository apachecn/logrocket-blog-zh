# Flutter 中的 GetX 状态管理终极指南

> 原文：<https://blog.logrocket.com/ultimate-guide-getx-state-management-flutter/>

当你按下家里电话总机的按钮时会发生什么？

您基本上是在将按钮的状态从关闭更改为打开，反之亦然。这触发了电流，要么打开灯泡，要么关闭灯泡。

![Lightbulb](img/2107fcd4d41511b1c0e93fd1559540e7.png)

来源:[动画 images.org](https://www.animatedimages.org/img-animated-light-bulb-image-0025-98965.htm)

从软件开发人员的角度考虑。当灯泡打开时，灯泡的 UI 从不发光状态变为发光状态。虽然物理上我们看不到灯泡被重新创建或重建，但如果在具有反应式状态管理的移动软件上出现这种情况，UI 将从头开始构建。

如果您来自 Android 或 iOS 框架，您需要从一个全新的角度开始考虑移动应用程序开发。Android 和 iOS 是命令式框架。另一方面，Flutter 是一个声明性框架。这意味着可以从头开始构建 UI，而不是修改它的一小部分，因为当小部件改变其状态时，Flutter 在重建 UI 方面很有效。

UI 编程的声明式风格有它自己的好处；你写一遍任何 UI 的代码，描述它在任何状态下应该是什么样子，就这样！然而，随着您深入钻研 Flutter 编程，有时您需要在整个应用程序的屏幕之间共享数据和状态。这时，您将需要一个优秀的状态管理库来帮助您快速高效地构建应用程序。

在 Flutter 中，状态管理是一个复杂的讨论话题。然而，许多状态管理库，如 [Provider](https://blog.logrocket.com/quick-guide-provider-flutter-state-management/) 是可用的，这是大多数开发人员推荐的。

但是…

今天，我们将讨论一个用于 Flutter 应用程序开发的简化状态管理解决方案，它的大多数特性都不需要上下文，称为 GetX。

## GetX 是什么？

GetX 不仅是一个状态管理库，相反，它是一个结合了路由管理和依赖注入的微框架。它旨在为 Flutter 提供一个超级轻量级但功能强大的解决方案，提供顶级的开发体验。GetX 基于三个基本原则:

1.  性能:关注最低的内存和资源消耗
2.  生产力:直观高效的工具与简单明了的语法相结合，最终节省了开发时间
3.  组织:将业务逻辑从视图和表示逻辑中分离出来是再好不过的了。您不需要上下文来导航路由，也不需要有状态的小部件

## GetX 的三大支柱

1.  状态管理:GetX 有两个状态管理器。一个是与`GetBuilder`功能一起使用的简单状态管理器，另一个是与`Getx`或`Obx`一起使用的反应式状态管理器。我们将在下面详细讨论它
2.  路线管理:无论是在屏幕之间导航，显示`SnackBars`，弹出对话框，还是在不使用`context`的情况下添加底部表单，GetX 都可以满足您。我不会写路由管理的细节，因为这超出了本文的范围，但确实有几个例子可以让你了解 GetX 语法的简单性是如何工作的
3.  依赖关系管理:GetX 为使用控制器的依赖关系管理提供了一个简单而强大的解决方案。只需一行代码，就可以从视图中访问它，而无需使用继承的小部件或上下文。通常，您会在一个类中实例化一个类，但是使用 GetX，您是用`Get`实例来实例化的，它将在整个应用程序中可用

## GetX 的增值功能

GetX 有一些现成的优秀特性，使得在 Flutter 中开发移动应用程序变得更加容易，而不需要任何样板代码:

1.  国际化:带有键值映射的翻译，各种语言支持，使用带有单数、复数和参数的翻译。在整个应用程序中只使用`Get`单词来更改应用程序的区域设置
2.  验证:电子邮件和密码验证也包含在 GetX 中。现在，您不需要安装单独的验证包
3.  存储:GetX 还为完全用 Dart 编写的数据提供了快速和额外的轻量级同步键值内存备份，可以轻松地与 GetX 核心包集成
4.  主题:使用 GetX，明暗主题之间的切换变得很简单
5.  响应式视图(Responsive view):如果你正在构建一个不同屏幕尺寸的应用，你只需要用`GetView`进行扩展，你就可以快速开发你的 UI，它将对桌面、平板、手机、手表都有响应

## 让我们开始 GetX 状态管理

我会一步一步地做这件事，这是我一直喜欢做的，我会尽量描述性地尽可能详细地解释这个过程。

### 步骤 1:创建新的应用程序

在您喜欢的 IDE 中创建一个全新的应用程序。首先，通过选择**编辑**菜单中的查找和替换选项，删除所有的起始注释，并键入以下内容:`\/\/.*`。这将在起始代码中选择 Flutter 的注释，你可以点击删除按钮。

### 步骤 2:添加所需的依赖项

将这些依赖项添加到您的`pubspec.yaml`文件中:

```
get: ^4.6.1           //YAML
get_storage: ^2.0.3  //YAML
```

运行以下命令:

```
flutter pub get  //YAML
```

在进行第 3 步之前，让我解释一下我们在这里做什么。我创建了一个演示 GetX 核心功能的小应用程序。该应用程序是一个商店，用户可以:

1.  更改商店的名称
2.  添加关注者姓名
3.  添加追随者计数
4.  将商店的状态从打开更改为关闭，反之亦然
5.  向商店添加评论
6.  将商店的主题由亮变暗

以上都将解释[状态管理](https://blog.logrocket.com/flutter-state-management-methods/)，依赖关系管理，路由管理，存储，以及主题。

这里我们更关注状态和依赖性管理。路线、存储和主题只是为了应用程序的美观。

您可以通过这个链接阅读并[测试应用程序。](https://getx-store.web.app/#/)

添加完依赖项后，您需要做的第一件事是将`main.dart`文件中的`MaterialApp`小部件更改为`GetMaterialApp`。这使得可以访问应用程序中的所有 GetX 属性。

### 步骤 4:添加 GetX 控制器

我们已经确定 GetX 将 UI 从业务逻辑中分离出来。这就是 GetX 控制器发挥作用的地方。

您总是可以在应用程序中创建多个控制器。当您用它的`Observer`包装一个单独的小部件时，GetX 控制器类控制 UI 的状态，以便它只在特定小部件的状态发生变化时才重建。

我们正在添加一个新的 Dart 文件来创建我们的控制器类`StoreController`，它扩展了`GetxController`:

```
class StoreController extends GetxController {}
```

接下来，我们添加一些变量并用默认值初始化它们。

通常我们会像下面这样添加这些变量:

```
final storeName = 'Thick Shake';
```

但是，在使用 GetX 时，我们必须通过在值的末尾添加 **`obs`** 来使变量可观测。然后，当变量发生变化时，依赖于它的应用程序的其他部分会得到通知。所以现在，我们的初始化值看起来像这样:

```
final storeName = 'Thick Shake'.obs;
```

其余变量如下所示:

```
// String for changing the Store Name
final storeName = 'Thick Shake'.obs;
// int for increasing the Follower count
final followerCount = 0.obs;
// bool for showing the status of the Store open or close
final storeStatus = true.obs;
// List for names of Store Followers
final followerList = [].obs;
// Map for Names and their Reviews for the Store
final reviews = <StoreReviews>[].obs;
// text editing controllers
final storeNameEditingController  = TextEditingController();
final reviewEditingController = TextEditingController();
final followerController = TextEditingController();
final reviewNameController = TextEditingController();
```

接下来，我们创建三种方法来更改名称、增加关注者数量和更改商店状态:

```
updateStoreName(String name) {
 storeName(name);
}

updateFollowerCount() {
 followerCount(followerCount.value + 1);
}

void storeStatusOpen(bool isOpen) {
 storeStatus(isOpen);
}
```

### 步骤 5:依赖注入

通俗地说，就是把刚刚创建的`controller`类加入到我们的`view`类中。有三种方法可以实例化。

1.  用`GetView`扩展整个`view`类，并用它注入我们的`StoreController`:

    ```
    class Home extends GetView<StoreController>{}
    ```

2.  实例化`storeController`如下:

    ```
    final storeController = Get.put(StoreContoller())
    ```

3.  对于选项三，首先创建一个新的`StoreBinding`类并实现`Bindings`。在它的默认依赖项中，您需要通过使用`Get.lazyPut()`来`lazyPut`这个`StoreController`。其次，你需要在`GetMaterialWidget`中的`initialBinding`属性内添加绑定类。

最后，代替上面提到的`Get.Put`,现在你可以使用`Get.find`,当你在你的任何类中实例化时，GetX 会为你找到你的控制器:

```
class StoreBinding implements Bindings {
// default dependency
 @override
 void dependencies() {
   Get.lazyPut(() => StoreController();
 }
}
```

```
@override
Widget build(BuildContext context) {
 return GetMaterialApp(
   debugShowCheckedModeBanner: false,
   title: 'GetX Store',
   initialBinding: StoreBinding(),
}
```

```
class UpdateStoreName extends StatelessWidget {
 UpdateStoreName({Key? key}) : super(key: key);
//Getx will find your controller.
 final storeController = Get.find<StoreController>();
```

项目中有很多代码和 Dart 文件。我只写我上面提到的三种方法。其余的代码将在 Git 上提供。链接将在本文末尾提供。其次，你也可以[通过一个网页链接](https://getx-store.web.app/#/)来尝试应用。

### 步骤 6:实例化控制器

因为我们已经用`GetView`扩展了我们的`Home`视图，并且创建了一个绑定类来`lazyPut`我们在其中的控制器，我们现在将使用`Get.find`在我们的类中实例化我们的控制器。

首先，我们添加一个新的无状态小部件`UpdateStoreName`。像这样实例化我们的`controller`类:

```
final storeController = Get.find<StoreController>();
```

```
RoundedInput(
 hintText: "Store Name",
 controller: storeController.storeNameEditingController,
),
const SizedBox(height: 20),
ElevatedButton(
 onPressed: () {
   storeController.updateStoreName(
       storeController.storeNameEditingController.text);
   Get.snackbar(
       'Updated',
       'Store name has been updated ton '
           '${storeController.storeNameEditingController.text}',
       snackPosition: SnackPosition.BOTTOM);
 },
 child: const Padding(
   padding: EdgeInsets.all(10.0),
   child: Text(
     'Update',
     style: TextStyle(fontSize: 20.0),
   ),
 ),
),
```

我来解释一下上面的代码:`RoundedInput`只是一个自定义的`TextField`，我们正在使用我们的`storeController`为`TextField`添加一个`TextEditingController`。我们也在`ElevatedButton`的`onPressed`中以同样的方式调用`updateStoreName()`方法。然后我们显示一个`SnackBar`，确认商店名称已经更新。

下面是`AddFollowerCount`和`StoreStatus`的代码。同样，两者都是无状态小部件，实现`storeController`和调用我们的控制器的方法是相似的:

```
class AddFollowerCount extends StatelessWidget {
 AddFollowerCount({Key? key}) : super(key: key);
 final storeController = Get.find<StoreController>();

 @override
 Widget build(BuildContext context) {
   return Scaffold(
     appBar: AppBar(title: const Text("Add Follower Count")),
     floatingActionButton: FloatingActionButton(
       onPressed: () {storeController.updateFollowerCount();
       },
       child: const Icon(Icons.add),
     ),
     body: Container(
       padding: const EdgeInsets.all(24),
       child: Center(
         child: Column(
           mainAxisAlignment: MainAxisAlignment.center,
           children: [
             const Text(
               'You have add these many followers to your store',
               textAlign: TextAlign.center,
               style: TextStyle(fontSize: 28),
             ),
             const SizedBox(
               height: 40.0,
             ),
             Obx(
               () => Text(
                 storeController.followerCount.value.toString(),
                 style: const TextStyle(fontSize: 48),
               ),
             )
           ],
         ),
       ),
     ),
   );
 }
}
```

```
class StoreStatus extends StatelessWidget {
 StoreStatus({Key? key}) : super(key: key);
 //final storeController = Get.put(StoreController());
 final storeController = Get.find<StoreController>();

 @override
 Widget build(BuildContext context) {
   return Scaffold(
     appBar: AppBar(title: const Text("Test Status Toggle")),
     body: Container(
       padding: const EdgeInsets.all(24),
       child: Center(
         child: Column(
           mainAxisAlignment: MainAxisAlignment.center,
           children: [
             const Text(
               "Is the Store open?",
               style: TextStyle(fontSize: 22),
             ),
             const SizedBox(height: 16),
             Obx(
               () => Switch(
                 onChanged: (value) => storeController.storeStatus(value),
                 activeColor: Colors.green,
                 value: storeController.storeStatus.value,
               ),
             )
           ],
         ),
       ),
     ),
   );
 }
}
```

现在，让我们进入使用我们的`storeController`显示我们商店名称的输入值、增加的追随者计数和商店状态的部分。

我们的`Home`视图是用`GetView<StoreController>`扩展的，所以我们不需要在这里实例化我们的`storeController`。相反，我们可以只使用 GetX 的默认控制器。请查看下面给出的代码，以获得清晰的图片，并了解第 6 步和第 7 步的区别。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

您一定已经注意到了，`Flexible`小部件中的`Text`小部件被一个`Obx`小部件包裹着，我们也称之为`controller`。还记得我们如何将`(.obs)`添加到变量中吗？现在，当我们想要看到可观察变量的变化时，我们必须用`Obx`包装小部件，也称为`Observer`，类似于您在上面的代码中已经注意到的。

当状态改变时，用`Obx`包装小部件只会重新构建特定的小部件，而不是整个类。这是多么简单:

```
class Home extends GetView<StoreController> {
 Home({Key? key}) : super(key: key);

 @override
 Widget build(BuildContext context) {
   return Scaffold(
     backgroundColor: AppColors.spaceCadet,
     appBar: AppBar(
       title: const Text("GetX Store"),),
     drawer: const SideDrawer(),
     body: Container(
       padding: const EdgeInsets.all(10),
       child: SingleChildScrollView(
         child: Column(
           children: [
             MainCard(
               title: "Store Info",
               body: Column(
                 crossAxisAlignment: CrossAxisAlignment.stretch,
                 children: [
                   Row(
                     mainAxisAlignment: MainAxisAlignment.spaceBetween,
                     children: [
                       const Flexible(
                         child: Text('Store Name:',
                           style: TextStyle(fontSize: 20),),
                         fit: FlexFit.tight,),
                       const SizedBox(width: 20.0),
                   // Wrapped with Obx to observe changes to the storeName
                   // variable when called using the StoreController.
                       Obx(
                         () => Flexible(
                           child: Text(
                             controller.storeName.value.toString(),
                             style: const TextStyle(
                             fontSize: 22, fontWeight: FontWeight.bold) ),
                           fit: FlexFit.tight,
                         ),),
                     ],),
                   const SizedBox(height: 20.0),
                   Row(
                     mainAxisAlignment: MainAxisAlignment.spaceBetween,
                     children: [
                       const Flexible(
                         child: Text('Store Followers:',
                           style: TextStyle(fontSize: 20),),
                         fit: FlexFit.tight, ),
                       const SizedBox(width: 20.0),
               // Wrapped with Obx to observe changes to the followerCount
               // variable when called using the StoreController.
                       Obx(
                         () => Flexible(
                           child: Text(
                             controller.followerCount.value.toString(),
                             textAlign: TextAlign.start,
                             style: const TextStyle(
                             fontSize: 22, fontWeight: FontWeight.bold),
                           ),
                           fit: FlexFit.tight,),), ],
                   ),
                   const SizedBox(height: 20.0),
                   Row(
                     mainAxisAlignment: MainAxisAlignment.spaceBetween,
                     children: [
                       const Flexible(
                         child: Text('Status:',
                           style: TextStyle(fontSize: 20),),
                         fit: FlexFit.tight,),
                       const SizedBox(width: 20.0),
                 // Wrapped with Obx to observe changes to the storeStatus
                 // variable when called using the StoreController.
                       Obx(
                         () => Flexible(
                           child: Text(
                        controller.storeStatus.value ? 'Open' : 'Closed',
                             textAlign: TextAlign.start,
                             style: TextStyle(
                                 color: controller.storeStatus.value
                                     ? Colors.green.shade700
                                     : Colors.red,
                                 fontSize: 22,
                                 fontWeight: FontWeight.bold),),
                           fit: FlexFit.tight,
                         ),  ),  ], ), ], ), ),
```

我特意强调了`controllers`和`Obx`，以理解 Flutter 提供的默认有状态小部件和使用 GetX 管理视图或整个应用程序的状态之间的区别。

如果我们使用有状态的小部件，那么每当我们想要看到变化时，我们就必须使用`setState()`方法。我们还必须手动处理`controllers`。因此，我们避免所有的样板代码，只用`Obx`包装我们的小部件，剩下的就交给我们了。

如果我们必须总结以上所有内容，只需两步:

1.  将`obs`添加到变量中
2.  用`Obx`包装您的 widget

#### 另一种方法

这并不是唯一的方法。例如，如果你让你的变量是可观察的，你也可以直接用`GetX<StoreController>`而不是`Obx`来包装小部件。但是，功能保持不变。这样，在调用`storeController`之前，您不需要实例化它。请看下面的代码:

```
// Wrapped with GetX<StoreController> to observe changes to the
//storeStatus variable when called using the StoreController.
GetX<StoreController>(
 builder: (sController) => Flexible(
   child: Text(
     sController.storeStatus.value ? 'Open' : 'Closed',
     textAlign: TextAlign.start,
     style: TextStyle(
         color: sController.storeStatus.value
             ? Colors.green.shade700
             : Colors.red,
         fontSize: 22,
         fontWeight: FontWeight.bold), ),
   fit: FlexFit.tight, ),),
```

*注意，我已经将`storeStatus`从`Obx`更改为`GetX<StoreController>`，它正在使用`building`功能中的`sController`。*

用`Obx`或`GetX`包装小部件被称为反应式状态管理。

#### 简单的状态管理

让我们看一个简单状态管理的例子。首先，使用简单状态管理的好处是您不需要将您的`MaterialWidget`更改为`GetMaterialWidget`。其次，您可以将其他状态管理库与简单的状态管理结合起来。

*注意，如果您不将`MaterialWidget`* *更改为`GetMaterialWidget`，您将无法使用其他 GetX 功能，如路线管理。*

对于简单的状态管理:

1.  你需要使用`GetBuilder`功能
2.  你不需要`observable`变量
3.  您必须在您的方法中调用`update()`函数

我在我们的`StoreController`中创建了一个新变量。但是这一次，我没有在变量的末尾加上`(obs)`。这意味着现在它是不可见的。

但是当商店数量增加时，我仍然需要更新我的视图，所以我必须在我新创建的方法中调用`update()`函数。检查下面的代码:

```
// variable is not observable
int storeFollowerCount = 0;

void incrementStoreFollowers() {
 storeFollowerCount++;
//update function needs to be called
 update();
}
```

现在，在我们的**主页**视图中，我已经将`Obx`更改为`GetBuilder`到`Text`小部件，它显示了关注者数量:

```
GetBuilder<StoreController>(
 builder: (newController) => Flexible(
   child: Text(
     newController.storeFollowerCount.toString(),
     textAlign: TextAlign.start,
     style: const TextStyle(
         fontSize: 22, fontWeight: FontWeight.bold),
   ),
   fit: FlexFit.tight, ),),
```

由于我们在**主页**视图中用`GetBuilder`包装了我们的追随者计数，我们还必须对`AddFollowerCount` Dart 文件进行修改。

1.  在`Fab`按钮的`onPressed`功能中添加这个:

    ```
    storeController.incrementStoreFollowers();
    ```

    1.  用`GetBuilder`包装`Text`小部件，以便它显示关注者计数:

        ```
        GetBuilder<StoreController>(  builder: (newController) => Text(    'With GetBuilder: ${newController.storeFollowerCount.toString()}',    textAlign: TextAlign.start,    style: const TextStyle(        fontSize: 22, fontWeight: FontWeight.bold), ),),
        ```

使用`Obx`或`GetX`和使用`GetBuilder`还有一个区别。当使用`Obx`或`GetX`时，您需要在使用`StoreController`调用您的方法后添加值。但是在使用`GetBuilder`的时候，不需要给它添加一个 value 参数。请看下面的区别:

```
// value parameter to be added with Obx or GetX
controller.storeName.value.toString(),

// value parameter is not needed with GetBuilder
newController.storeFollowerCount.toString(),
```

这就是 GetX 提供的不同状态管理。此外，正如我所承诺的，我正在写一些关于 GetX 包的路由管理和其他特性的内容。因此，需要一篇全新的文章来详细描述这一切。

## 其他 GetX 特性

### 路线管理

传统上，当用户想通过点击按钮从一个屏幕切换到另一个屏幕时，代码应该是这样的:

```
Navigator.push(context, 
    MaterialPageRoute(builder: (context)=> Home()));
```

但是，对于 GetX，实际上只有两个词:

```
Get.to(Home());
```

当您想要导航回上一个屏幕时:

```
Navigator.pop(context);
```

使用 GetX 时，绝对不需要上下文:

```
Get.back();
```

如果您打开了一个对话框或一个抽屉，并且想要在关闭抽屉或对话框时导航到另一个屏幕，有两种方法可以通过默认的颤动导航来实现:

1.  关闭抽屉或对话框，然后像这样导航:

    ```
    Navigator.pop(context); Navigator.push(context,      MaterialPageRoute(builder: (context)=> SecondScreen()));
    ```

2.  如果已经生成了命名路线:

    ```
    Navigator.popAndPushNamed(context, '/second');
    ```

使用 GetX，在关闭任何打开的对话框或抽屉时，生成命名路线和在屏幕间导航变得简单多了:

```
// for named routes
Get.toNamed('/second'),
// to close, then navigate to named route
Get.offAndToNamed('/second'),
```

### 增值功能

1.  小吃吧

    ```
    Get.snackbar(    'title',    'message',    snackPosition: SnackPosition.BOTTOM, colorText: Colors.white, backgroundColor: Colors.black, borderColor: Colors.white);
    ```

2.  对话框

    ```
    Get.defaultDialog(    radius: 10.0,    contentPadding: const EdgeInsets.all(20.0),    title: 'title',    middleText: 'content',    textConfirm: 'Okay',    confirm: OutlinedButton.icon(      onPressed: () => Get.back(),      icon: const Icon(        Icons.check,        color: Colors.blue,     ),      label: const Text('Okay',        style: TextStyle(color: Colors.blue),      ),   ),  cancel: OutlinedButton.icon(      onPressed: (){},      icon: Icon(),      label: Text(),),);
    ```

3.  底部板材

    ```
    Get.bottomSheet(    Container(  height: 150,  color: AppColors.spaceBlue,  child: Center(      child: Text(    'Count has reached ${obxCount.value.toString()}',    style: const TextStyle(fontSize: 28.0, color: Colors.white),  )), ));
    ```

查看上面的代码，您可以很容易地理解显示和定制 snackbars、对话框和底部表单是多么简单。

这只是冰山一角。使用 GetX 库可以做更多的事情。在我结束我的文章之前，最后一个例子是在光明和黑暗主题之间切换。

### 从亮主题切换到暗主题，反之亦然

首先，我创建了一个类似于我们的`StoreController`的`ThemeController`。在我的控制器中，我使用`GetStorage`功能来保存切换后的主题:

```
class ThemeController extends GetxController {
  final _box = GetStorage();
  final _key = 'isDarkMode';

  ThemeMode get theme => _loadTheme() ? ThemeMode.dark : ThemeMode.light;
  bool _loadTheme() => _box.read(_key) ?? false;

  void saveTheme(bool isDarkMode) => _box.write(_key, isDarkMode);
  void changeTheme(ThemeData theme) => Get.changeTheme(theme);
  void changeThemeMode(ThemeMode themeMode) => Get.changeThemeMode(themeMode);
}
```

在`GetMaterialApp`小部件中，我已经为`theme`和`darkTheme`添加了属性，并且初始化了`themeController` 并将其添加到了`themeMode`属性中:

```
class MyApp extends StatelessWidget {
  MyApp({Key? key}) : super(key: key);
  final themeController = Get.put(ThemeController());

  @override
  Widget build(BuildContext context) {
    return GetMaterialApp(
      debugShowCheckedModeBanner: false,
      title: 'GetX Store',
      initialBinding: StoreBinding(),
      theme: Themes.lightTheme,
      darkTheme: Themes.darkTheme,
      themeMode: themeController.theme,
}
}
```

接下来，在我们的**主页`appBar`的**屏幕上，我添加了一个图标，可以在明暗之间切换主题。看看下面的代码就知道了:

```
class Home extends GetView<StoreController> {
 Home({Key? key}) : super(key: key);
 final themeController = Get.find<ThemeController>();

 @override
 Widget build(BuildContext context) {
   return Scaffold(backgroundColor: AppColors.spaceCadet,
     appBar: AppBar(title: const Text("GetX Store"),
       actions: [IconButton(
           onPressed: () {
             if (Get.isDarkMode) {
               themeController.changeTheme(Themes.lightTheme);
               themeController.saveTheme(false);
             } else {
               themeController.changeTheme(Themes.darkTheme);
               themeController.saveTheme(true); }},
           icon: Get.isDarkMode
               ? const Icon(Icons.light_mode_outlined)
               : const Icon(Icons.dark_mode_outlined),),], ),
```

仅此而已。现在，您可以轻松地在亮暗主题之间切换。

## 结论

看完整篇文章，你是否想知道这个库的创建者为什么给它起名叫 GetX？依我拙见，人们经常会下命令说:“把它做完！”或者“让我们完成它！”

然而，x 是一个未知变量，但在这种情况下，它真的是任何东西。

使用 GetX，您可以完成所有工作。

就这些了，谢谢你的阅读。如果您有任何建议、更正或反馈，请在下面留下评论。

我在下面留下了我在本文中解释过的应用程序源代码的链接，以及一个额外的基础计数器应用程序。您可以随意克隆 Git 存储库并自己试验代码。也有到 PWAs 的链接，可以在没有任何安装的情况下试用这个应用程序。

### GitHub 上的源代码链接

GetX 商店链接:[https://github.com/timelessfusionapps/getx_store](https://github.com/timelessfusionapps/getx_store)

GetX 专柜链接:[https://github.com/timelessfusionapps/getx_counter](https://github.com/timelessfusionapps/getx_counter)

### web 应用程序的链接

GetX 商店链接:[https://getx-store.web.app/#/](https://getx-store.web.app/#/)

GetX 专柜 app:[https://getx-counter.web.app/#/](https://getx-counter.web.app/#/)

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)