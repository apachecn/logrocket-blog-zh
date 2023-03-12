# 从头开始在 Kotlin 中实现可选回调

> 原文：<https://blog.logrocket.com/implementing-optional-callbacks-kotlin-scratch/>

任务结果在程序中非常重要，因为它们决定了下一个任务是否会被执行。最好的程序是那些能够自动和同步地轮询和获取结果的程序。

Android 有一种称为可选回调的机制，允许您的方法和类接收另一个任务的失败和成功结果，而不必一次又一次地调用另一个类来获得结果。

在本教程中，我们将学习可选回调以及如何在 [Kotlin](https://blog.logrocket.com/building-cross-platform-mobile-apps-kotlin-multiplatform/) 中实现它们。此外，我们将探索为您实现这一功能的库。

### 内容

## 什么是可选回调？

Android 回调允许您的方法从另一个方法同步获取结果。回调充当信使；它们是程序通信的中心，并作为参数传递给函数。它们通过提供积极或消极的结果来帮助完成一个功能的任务。可选的回调显式地声明当从另一个方法或函数获取不同的结果时会发生什么。

可选回调还允许依赖回调的函数继续运行，即使回调返回错误，这支持高级别的维护和可靠性。

可选回调很重要，因为它们通过防止应用程序冻结和阻塞，确保用户可以不受干扰地享受你的应用程序。一个冻结的用户界面会导致很多人卸载你的应用。

## 可选回调和标准回调有什么区别？

作为一名 Android 开发人员，我每天都在使用回调，从`onCreate()`方法开始，它在应用程序启动时创建一个活动。这是一个典型的回调示例，您将在 Android 开发过程中使用一百万次。

当您的应用程序在启动时崩溃时，问题可以在这个回调中找到:

```
override fun onCreate(savedInstanceState: Bundle?) {
   super.onCreate(savedInstanceState)
   setContentView(R.layout.activity_main)
}

```

下面是另一个在活动暂停时调用的典型回调示例:

```
override fun onPause() {
        super.onPause()
        print("onPause")
    }

```

上述回调在 Android [活动生命周期](https://blog.logrocket.com/understanding-the-android-activity-lifecycle/)和片段生命周期中做得很好。但是，当在`onPause()`回调中实现的方法不起作用时，问题就出现了。一个可选的回调将声明如果发生错误会发生什么。

## 从头开始在 Kotlin 中实现可选回调

在程序中使用可选回调不应该是可选的。可选的回调使您的活动保持流畅，即使发生异常和任务输出负面结果。

下面是一个可选回调的例子:

```
Result.of<T, Throwable>(doTask())
      .flatMap { normalizedData(it) }
      .map { createRequestFromData(it) }
      .flatMap { database.updateFromRequest(it) }

```

添加失败和成功案例:

```
//if successful
result.success {
}

//if failed
result.failure {
}

```

上面可选的回调有条件:失败和成功切换。你必须陈述如果另一门课的结果是阴性或阳性会发生什么。这样，当回调返回否定结果时，您将能够关闭可能导致应用程序崩溃的进程和函数。

这是另一个不使用库的可选回调的例子。下面显示的可选回调提供了三种情况，当结果被传递时将触发这三种情况。如果动画重复、结束或开始，您有机会做一些事情:

```
animation.setAnimationListener(object : Animation.AnimationListener {
   override fun onAnimationRepeat(animation: Animation?) {

   }

   override fun onAnimationEnd(animation: Animation?) {
       doSomething()
   }

   override fun onAnimationStart(animation: Animation?) {
          }
})

```

可选的回调结构主要由获取结果的代码块组成，随后是失败和成功的情况(或者当接收到某些结果时您希望执行的任何情况)。但是，如果您是从零开始实现它们，而没有库的介入，您可以添加其他案例。

一个库(像[Resul](https://github.com/kittinunf/Result)T2t)通过给你两种情况让它变得简单:失败和成功。您所要做的就是添加代码，这些代码将在成功或失败实例发生时添加。

失败和成功案例使得可选的回访不同于典型的回访:

```
//if successful
result.success {

}
//if failed
result.failure {

}

```

## 使用 Result 在 Kotlin 中实现可选回调

结果是一个 Kotlin 库，它允许你实现可选的回调并帮助你快速处理错误。

首先，将以下依赖项添加到`gradle.build`文件中，并同步 Gradle 以下载第三方库:

```
implementation("com.github.kittinunf.result:result-jvm:5.2.1")
implementation ("com.github.kittinunf.result:result:5.2.1")

```

让我们看看在我们想要打开并读取一个文本文件的情况下 Result 是如何工作的。为了获得打开文本文件任务的结果，我们使用 Kotlin Result 对象，它获取`readText task`的成功和失败值:

```
val operation = { File("/path/to/file/foo.txt").readText() }
Result.of { operation() }  

```

接下来，我们创建一个`normalizedData(foo)`函数，对从操作中收集的数据进行排序和简化。通过调用`normalize()`函数来规范化数据:

```
fun normalizedData(foo): Result<Boolean, NormalizedThrowable> {
    Result.of { foo.normalize() }
}

```

接下来，从我们上面规范化的数据中创建一个请求:

```
fun createRequestFromData(foo): Request {
    return createRequest(foo)
}

```

请求数据后，创建如下所示的数据库函数，该函数将更新结果对象的值。如果更新事务失败，则会引发异常:

```
fun database.updateFromRequest(request): Result<Boolean, DBThrowable> {
    val transaction = request.transaction
    return Result.of { 
        db.openTransaction {
            val success = db.execute(transaction)
            if (!success) {
                throw DBThrowable("Error")
            }
            return success
        }
    }
}

```

使用 Result 对象获取信息后，就该添加成功和失败的案例了。首先声明以下值:

```
val (value, error) = result

```

现在，获得结果值:

```
val value: Int = result.get()

```

现在添加成功和失败案例:

```
result.success {
//Add code that does something when the task is successful here
}

result.failure {
//add code that warns the user that an error has occurred here.
}

```

## 使用 callback-ktx 在 Kotlin 中实现可选回调

[callback-ktx](https://github.com/sagar-viradiya/callback-ktx) 库将基于回调的 API 包装并转换成挂起的扩展函数。目前，callback-ktx 仅支持以下 API:

*   动画
*   位置
*   回收视图
*   传感器
*   视角
*   小部件(文本视图)

使用以下代码下载 callback-ktx 依赖项:

```
implementation("io.github.sagar-viradiya:callback-core-ktx:1.0.0")

```

下载完库之后，您可以通过调用`lifecycleScope`对象来跟踪动画，以获得生命周期的范围。接下来调用`awaitStart()`方法，动画时会触发。

现在，您有机会陈述并特别添加仅在动画开始时执行的代码:

```
viewLifecycleOwner.lifecycleScope.launch {
  animator.awaitStart()
  // insert code that does something when the animation starts here.
 }

```

你可以在这里查看回调-ktx 库[的更多用例。如果你仍然有一个使用 Java 的 Android 代码库，你可以使用](https://github.com/sagar-viradiya/callback-ktx)[秃鹫](https://github.com/shiroyama/vulture)来安全地实现回调。

## 结论

在本文中，我们已经了解了可选回调的好处，可选回调和常规回调的区别，以及如何使用 Result 和 callback-ktx 实现回调。

当回调返回错误和成功结果时，可选的回调肯定会给你更多的能力和选择。

如果你有任何问题或意见，请在下面的评论区留下。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)

## LogRocket :即时重现你的安卓应用中的问题。

[![](img/b5ae4bd0ecde7aa9d5288746416d5e18.png)](https://lp.logrocket.com/blg/kotlin-signup)

[LogRocket](https://lp.logrocket.com/blg/kotlin-signup) 是一款 Android 监控解决方案，可以帮助您即时重现问题，确定 bug 的优先级，并了解您的 Android 应用程序的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 Android 应用程序— [免费试用 LogRocket】。](hhttps://lp.logrocket.com/blg/kotlin-signup)