# 在 Unity - LogRocket 博客中使用协程

> 原文：<https://blog.logrocket.com/using-coroutines-unity/>

随着您的 Unity 项目的增长，将会需要创建更复杂的逻辑。您可能需要加载不同的资源或准备时间敏感的场景。最终，我们希望玩家有最无缝的体验，而不破坏他们的沉浸感。

使用协程，您可以使用各种方法来实现这一点。在本教程中，我们将讨论协程的不同用法以及何时使用它们。

## 什么是协程？

Unity 使用帧来表示时间。正常的方法将在一帧中执行，并进入下一条指令。

协程可用于跨多个框架执行一段代码。它们也可以用来持续执行一段代码，直到你让它停止。一个协程包含一个产出指令，它将等待你告诉它的一定时间。

## 何时使用协程

假设你想使用一个普通的调用方法和一个`for`循环来填充 1-100 HP 的生命值。玩家会在你的方法结束时看到一个完整的生命值条。这是因为该方法计数到 100 的时间比 Unity 上的一帧快，也比你的玩家的眼睛快。

这是一个何时使用协程的好例子。你会告诉协程充满 1 马力，等待几分之一秒，并再次这样做，直到我们达到 100 马力。

这样，随着时间的推移，你的玩家将会看到血量条逐渐变满。

```
​​using System.Collections;
using UnityEngine;

public class HealthBar : MonoBehaviour
{
   int maxHP=100;
   int currentHP=0;

   void Start()
   {
       Debug.Log("Start Increasing Health");
       StartCoroutine(HealthIncrease());
       Debug.Log("Finish start event");
   }

   IEnumerator HealthIncrease(){
       Debug.Log("Start Coroutine");
       for(int x=1; x<=maxHP;x++){
           currentHP=x;
           //Increase or decrease the parameter of WaitForSeconds
           //to test different speeds.
           yield return new WaitForSeconds(0.2f);
           Debug.Log("HP: "+currentHP+" / "+maxHP);
       }
       Debug.Log("Current health is "+maxHP);
       Debug.Log("End Coroutine");
   }
}

```

另一个潜在的使用案例可能是，如果一个玩家被一个物体击中，你想增加几秒钟，当没有其他东西可以击中他们。这将创造暂时无敌，以避免不公正的游戏体验。

在伪代码中，我们会以这样的方式结束:当一个物体击中播放器时，停用播放器点击框两秒钟，然后再次激活它。

## 协程的一个例子

`coroutine`是来自`MonoBehavior`的一个方法，它有一个`IEnumerator`返回类型。到

调用并开始使用它，您应该使用`StartCoroutine`方法并在需要时传递您的`coroutine`方法和参数。

在一个协同程序中，当你的代码在下一帧暂停并在之后继续时，产生返回。如果您不需要您的协程等待，您可以使用`WaitForSeconds`(或任何其他 yield 指令)，以及`null`。

```
using System.Collections;
using UnityEngine;

public class Coroutines : MonoBehaviour
{
   void Start(){
       Debug.Log("Begin Start event");
       StartCoroutine(WaitCoroutine(2f));
       Debug.Log("End Start event");
   }

   private IEnumerator WaitCoroutine(float time){
       Debug.Log("Inside coroutine");
       yield return new WaitForSeconds(time);
       Debug.Log("Finish coroutine after "+time+" seconds");
   }
}

```

上面的示例将打印:

```
Begin Start event
Inside coroutine
End Start event

```

它将等待两秒钟，并将打印:

```
Finish coroutine after 2 seconds

```

## 实现永不停止的协程

您还可以声明一个`IEnumerator`变量，并用您的协程引用它来调用它。

在下面的例子中，由于`while`循环总是为`true`，协程将在等待两秒后继续执行。

```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class InfiniteLoopCoroutine : MonoBehaviour
{
   IEnumerator myCouroutine;

   void Start(){
       myCouroutine= InfiniteLoop(2f);
       StartCoroutine(myCouroutine);
   }

   IEnumerator InfiniteLoop(float seconds){
       while(true){
           yield return new WaitForSeconds(seconds);
           Debug.Log("Inside infiniteLoop");
       }
   }
}

```

请注意，`WaitForSeconds`使用了除以`Time.timeScale`的浮点参数。我们这样做是为了在游戏设备上模拟真实的一秒。

## 如何停止协程

现在我们有了一个有无限循环的协程。但是我们如何让它停止？

您可以使用`StopCoroutine`方法停止正在运行的协程，该方法将接受以下任一参数:

*   具有协程名称的字符串
*   你的协程的参考

因此，在前面的示例中，我们可以:

```
StopCoroutine("InfiniteLoop");

```

或者…

```
StopCoroutine(myCoroutine);

```

如果它所连接的对象被`SetActive(false)` 禁用或被`Destroy()`销毁，协程也会停止。

下面的例子将在玩家按下`Space`键时停止协程。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
using System.Collections;
using UnityEngine;

public class InfiniteLoopCoroutine : MonoBehaviour
{
   IEnumerator myCouroutine;

   void Start(){
       myCouroutine= InfiniteLoop(2f);
       StartCoroutine(myCouroutine);
   }

   IEnumerator InfiniteLoop(float seconds){
       while(true){
           yield return new WaitForSeconds(seconds);
           Debug.Log("Inside infiniteLoop");
       }
   }

   void Update(){
       if (Input.GetKeyDown(KeyCode.Space))
       {
           StopCoroutine("InfiniteLoop");
           Debug.Log("Coroutine stopped");
       }
   }
}

```

## 用协程程序暂停游戏

在 Unity 中暂停游戏的一种流行方式是将`Time.timeScale`设置为等于`0`。这设置了 Unity 用来根据运行游戏的设备来测量时间的属性。

请记住，这都是在游戏/unity 项目环境中，所以它不等同于现实世界中的时间。

当`Time.timeScale`设置为`0`时，游戏暂停，`Update`方法不再在每一帧执行。每个正在执行的动画都会停止，每个使用时间刻度的移动对象也会原地停止。

对于协程来说，这有一点不同。

如果你正在一个循环中执行一个协程，并且当`Time.timeScale`被设置为`0`时它仍然在运行，那么协程将继续运行，但是`WaitForSeconds`不会。

`WaitForSeconds`用`Time.timeScale`知道什么是真正的一秒。在这种情况下，它将正常运行，但是协程中的其余代码将继续执行。

```
using System.Collections;
using UnityEngine;

public class TimeStop : MonoBehaviour
{
   // Start is called before the first frame update
   void Start()
   {
       StartCoroutine(PrintEverySecond());
   }

   // Update is called once per frame
   void Update()
   {
       if(Input.GetKeyDown(KeyCode.Space)){
           if(Time.timeScale==1.0){
               Debug.Log("Time Stop");
               Time.timeScale=0.0f;
               StartCoroutine(PrintOnStop()); //We start a new coroutine after stopping time.
           }else{
               Debug.Log("Time Continue");
               Time.timeScale=1.0f;
           }
       }
   }

   IEnumerator PrintEverySecond(){
       while(true){
           yield return new WaitForSeconds(1.0f);
           Debug.Log("Inside Coroutine");
       }
   }

   IEnumerator PrintOnStop(){
       for(int x=0;x<=10;x++){
           Debug.Log("Time is stopped but I'm still running "+x);
       }
       yield return null;
   }
}

```

请记住，您可以在暂停游戏之前用`StopCoroutine`停止协程，以防止意外行为。

你也可以同时停止所有的协程！我们将在下一节讨论这一点。

## 使用多个协程并一次停止它们

如您所知，您可以同时执行多个行为不同的协程。可以通过`StopAllCoroutines()`方法立即停止它们。

```
using System.Collections;
using UnityEngine;

public class MultipleCoroutines : MonoBehaviour
{
   void Start()
   {
       //You can use multiple coroutines at the same time,
       //even on the same definition.
       //Each one will behave individually.
       StartCoroutine(MyCoroutine(1f));
       StartCoroutine(MyCoroutine(3f));
       StartCoroutine(MyCoroutine(5f));
   }

   void Update(){
       //If you hit space at runtime, all the coroutines will stop
       if(Input.GetKeyDown(KeyCode.Space)){
           StopAllCoroutines();
           Debug.Log("All coroutines stopped");
       }
   }

   IEnumerator MyCoroutine(float sec){
       while(true){
           yield return new WaitForSeconds(sec);
           Debug.Log("I'm on a loop that prints every "+sec+" seconds");
       }
   }
}

```

`StopAllCoroutines()`不接受同时停止游戏中所有协程的参数。

## 协程与线程

请记住，协程不是线程。在协程中执行的任何同步代码都将在主线程上执行。

例如，如果您想使用线程来处理 HTTP 请求，您可以使用异步方法。

下面是一个使用协程处理 HTTP 请求的例子:

```
using System.Collections;
using UnityEngine.Networking;
using UnityEngine;

public class HTTP_Request_Example : MonoBehaviour
{
   void Start()
   {
       //Consume an HTTP request with a coroutine
       //It happens on the main thread, which might cause the UI
       //to be unresponsive for a bit
       StartCoroutine(MyRequest("www.google.com"));
   }

   IEnumerator MyRequest(string url){
       using (UnityWebRequest request = UnityWebRequest.Get(url))
       {
           //Fetches a page and displays the number of characters of the response.
           yield return request.SendWebRequest();
           Debug.Log("Page length: "+request.downloadHandler.text.Length);
       }
   }
}

```

虽然有很多这种方法的例子，但是当您不能完全控制这些外部 API 的时间响应时，这种方法并不理想。

下面是使用异步方法的相同示例:

```
using System.Collections;
using UnityEngine.Networking;
using UnityEngine;
using System.Threading.Tasks;
using System.Runtime.CompilerServices;

//By default, UnityWebRequest is not async, so you will need this extension
//Credit goes to: https://gist.github.com/mattyellen/d63f1f557d08f7254345bff77bfdc8b3
public static class ExtensionMethods
{
   public static TaskAwaiter GetAwaiter(this AsyncOperation asyncOp)
   {
       var tcs = new TaskCompletionSource<object>();
       asyncOp.completed += obj => { tcs.SetResult(null); };
       return ((Task)tcs.Task).GetAwaiter();
   }
}

public class HTTP_Request_Async_Example : MonoBehaviour
{
   void Start()
   {
       //Invoke a new thread when the web request will happen without blocking the UI
       ConsumeWebRequest();
   }

   async void ConsumeWebRequest(){
       Debug.Log(await MyRequest("www.google.com"));
   }

   async Task<string> MyRequest(string url){
       using (UnityWebRequest request = UnityWebRequest.Get(url))
       {
           //Fetches a page and displays the number of characters of the response.
           await request.SendWebRequest();
           string response = "Page length: "+request.downloadHandler.text.Length;
           return response;
       }
   }
}

```

开箱即用，Unity 使用自己的网络包`UnityEngine.Networking`。

默认情况下，此包不是异步的。如果在协程内部使用它，它可能会锁定应用程序的主线程，导致一些意外的行为。

第二个例子将使用自己的线程，并将主线程留给玩家。游戏会感觉运行一如既往的流畅！

## 其他想法

协程非常节省资源，所以不要害怕尝试它们。如果您最终处于同时拥有太多协程的情况，您可能需要仔细检查您的设计以寻找解决方案。真正需要的同一个对象中的两三个协程就足够了。

在 WebRequest 中使用协程的例子有很多，但是，一般来说，如果您必须消耗项目无法控制的外部资源(比如服务器响应您的请求所需的时间)，那么使用异步方法代替协程是一个好主意。这是因为协程执行同步代码，可能会使用主线程，从而给玩家带来不好的体验。

我不是说`async` / `await`在 Unity 上比协程更好，它们都是很棒的工具。这实际上取决于您的设计以及何时何地使用它们的选择。一个通用的经验法则是，对于内部流程，使用协程，对于外部流程，使用异步方法。

## 结论

协程是一个很好的工具。他们可以帮助你为你的玩家在他们眼前设置关卡，在事件之间平滑过渡，或者为你创造新的功能来打动你的玩家。

我希望你觉得这篇文章很有用，并且喜欢读它，就像我喜欢写它一样！

gamedev 快乐！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)