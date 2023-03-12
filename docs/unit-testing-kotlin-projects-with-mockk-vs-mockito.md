# 在 Kotlin 项目中使用 Mockk vs. Mockito 进行单元测试

> 原文：<https://blog.logrocket.com/unit-testing-kotlin-projects-with-mockk-vs-mockito/>

## 介绍

单元测试是软件工程实践中由来已久的组成部分。大多数成功的软件产品使用正确编写的单元测试；基本上，单元测试验证一段代码的正确性。

编写代码来测试代码可能看起来违反直觉，但它本身就是一门艺术，可以让开发人员对他们的代码充满信心。

下面是我们从编写单元测试中得到的重要好处:

1.  在生产代码的早期捕捉 bug
2.  通过让一个代码单元做特定的事情来降低代码的复杂性
3.  作为开发人员了解项目的良好文档来源
4.  节省时间(和金钱)
5.  鼓励用更少的耦合编写干净和可维护的代码

### Kotlin 项目中的单元测试

Kotlin 编程语言基本上是在 JVM 环境中执行的。其简洁而别致的语言特性使其在社区中广受欢迎，并被频繁用于 Android 应用程序、Kotlin 多平台移动(KMM)应用程序和 spring boot 应用程序等项目中。

目前有两个流行的框架用来帮助有效的单元测试: [Mockito](https://site.mockito.org/) 和[mock](https://mockk.io/)。在本文中，我们将通过以下几个部分来讨论它们:

## 莫克对莫克托

Mockk 和 Mockito 是帮助编写针对 JVM 平台的单元测试的库。自 Android 开发早期以来，Mockito 就一直存在，并最终成为编写单元测试的事实上的嘲讽库。

Mockito 和 Mockk 分别用 Java 和 Kotlin 编写，由于 Kotlin 和 Java 可以互操作，所以它们可以存在于同一个项目中。从本质上讲，这两个库可以在这些项目中互换使用，但是 Kotlin 的优势使得大多数 Kotlin 项目都倾向于使用 Mockk。

以下几点总结了为什么在 Kotlin 项目中，Mockk 比 Mockito 更受青睐:

*   对 Kotlin 功能的一流支持
*   一个纯粹的模仿 Kotlin 的 DSL，用于编写干净、地道的 Kotlin 代码
*   模仿对最终类和方法的支持
*   [协程](https://blog.logrocket.com/understanding-kotlin-coroutines/)默认支持

## 如何为 Kotlin 项目编写单元测试

出于本文的目的，我们将实现一个简单的用户存储库类来演示在 Kotlin 项目中编写单元测试。在我们继续之前，有两件事需要注意:

1.  开发环境将在 Android Studio 中
2.  我们将比较两个模拟库在不同测试用例中的语法差异

说够了，让我们把手弄脏吧！

### 创建用户存储库

首先，像这样定义存储库的接口:

```
interface UserRepository {

    suspend fun saveUser(user: User)

    suspend fun getUser(id: String): User

    suspend fun deleteUser(id: String)
}

```

这基本上是一个将由`concrete`类实现的契约。请参见下面的代码块。

```
class UserRepositoryImpl constructor(
    private val dataSource: DataSource
) : UserRepository {
    override suspend fun saveUser(user: User) {
        dataSource.save(user)
    }

    override suspend fun getUser(id: String): User {
        return dataSource.get(id) ?: throw IllegalArgumentException("User with id $id not found")
    }

    override suspend fun deleteUser(id: String) {
        dataSource.clear(id)
    }
}

```

`UserRepositoryImpl`依赖`DataSource`，通过`DataSource`完成`UserRepository`的合同。

`DataSource`是一个简单的 Kotlin 类。它的目的是将用户数据存储在内存中，以便以后检索。有关详细信息，请参见下面的代码块:

```
class DataSource {

    private val db = mutableMapOf<String, User>()

    fun save(user: User) = db.let { it[user.email] = user }

    fun get(key: String): User? = db[key]

    fun clear(key: String) = db.remove(key)

    fun clearAll() = db.clear()
}

```

为了简单起见，我使用了一个`mutableMap`对象将一个`User`保存到内存中。[映射](https://blog.logrocket.com/guide-to-map-interface-in-kotlin/)是保存成对对象(键和值)的集合，因此让用户电子邮件作为保存和检索`User`的唯一键是有意义的。

将库依赖项添加到 Gradle

将以下依赖项添加到您的应用程序级 Gradle 文件中，如下所示:

```
//Mockk
testImplementation "io.mockk:mockk:1.12.4"

//Mockito
testImplementation "org.mockito:mockito-core:4.0.0"
testImplementation "org.mockito:mockito-inline:4.0.0"
testImplementation "org.mockito.kotlin:mockito-kotlin:4.0.0"

```

在 Kotlin 项目中使用 Mockito 需要一些额外的依赖项，原因如下:

1.  默认情况下，Kotlin 类是 final 类，不能被 Mockito 模仿，因此需要`:mockito-inline:`。你可能认为一个替代方法是将 open 修饰符添加到相关的类中，但是不推荐这样做，因为它会弄乱你的代码库并迫使你将类定义为`open`
2.  `:mockito-kotlin`是一个库，为在 Kotlin 项目中使用 Mockito 提供了有用的函数

## 使用 Mockk 为被测系统(SUT)编写测试

```
import io.mockk.coEvery
import io.mockk.coVerify
import io.mockk.mockk
import io.mockk.slot
import java.util.*
import kotlinx.coroutines.ExperimentalCoroutinesApi
import kotlinx.coroutines.test.runTest
import org.junit.Assert
import org.junit.Test

@OptIn(ExperimentalCoroutinesApi::class)
class UserRepositoryImplTest {

    private val dataSource = mockk<DataSource>(relaxed = true)
    private val sut = UserRepositoryImpl(dataSource)

    @Test
    fun `verify correct user params are used`() = runTest {
        val user = buildUser()

        sut.saveUser(user)

        val captor = slot<User>()

        coVerify { dataSource.save(capture(captor))}

        Assert.assertEquals(user.email, captor.captured.email)
    }

    @Test
    fun `verify correct user is retrieved`() = runTest {
        val email = "[email protected]"

        coEvery { dataSource.get(any()) } returns buildUser()

        val user = sut.getUser(email)

        Assert.assertEquals(email, user.email)
    }

    @Test
    fun `verify user is deleted`() = runTest {
        val email = "[email protected]"
        sut.deleteUser(email)

        coVerify { dataSource.clear(any()) }
    }

    companion object {
        fun buildUser() = User(
            id = UUID.randomUUID().toString(),
            email = "[email protected]",
            fullName = "Emmanuel Enya",
            verificationStatus = User.VerificationStatus.Verified,
            memberShipStatus = User.MemberShipStatus.Free
        )
    }
}

```

上面的代码块是一个相当简单的测试类，包含最少的测试用例。在类体的顶层，我模拟了数据源并创建了一个被测系统的实例。

请注意，`DataSource`被嘲讽，而`relax`被设置为`true`，就像这样:

```
mockk<DataSource>(relaxed = true)

```

这种模拟为所有函数返回一个简单的值，允许您跳过为每种情况指定行为。查看 mock 文档，了解关于放松的 mock 的更多[细节。](https://mockk.io/#relaxed-mock)

代码块的其他部分将与测试类的 Mockito 变体一起检查。

## 使用 Mockito 为被测系统(SUT)编写测试

```
import java.util.*
import kotlinx.coroutines.ExperimentalCoroutinesApi
import kotlinx.coroutines.test.runTest
import org.junit.Assert
import org.junit.Test
import org.mockito.Mockito.*
import org.mockito.kotlin.any
import org.mockito.kotlin.argumentCaptor

@OptIn(ExperimentalCoroutinesApi::class)
class UserRepositoryImplTestMockito {

    private val dataSource = mock(DataSource::class.java)
    private val sut = UserRepositoryImpl(dataSource)

    @Test
    fun `verify correct user params are used`() = runTest {
        val user = buildUser()

        sut.saveUser(user)

        val captor = argumentCaptor<User>()

        verify(dataSource).save(captor.capture())

        Assert.assertEquals(user.email, captor.firstValue.email)
    }

    @Test
    fun `verify correct user is retrieved`() = runTest {
        val email = "[email protected]"

        `when`(dataSource.get(any())).then { buildUser() }

        val user = sut.getUser(email)

        Assert.assertEquals(email, user.email)
    }

    @Test
    fun `verify user is deleted`() = runTest {
        val email = "[email protected]"
        sut.deleteUser(email)

        verify(dataSource).clear(any())
    }

    companion object {

        fun buildUser() = User(
            id = UUID.randomUUID().toString(),
            email = "[email protected]",
            fullName = "Emmanuel Enya",
            verificationStatus = User.VerificationStatus.Verified,
            memberShipStatus = User.MemberShipStatus.Free
        )
    }
}

```

上面的代码块是用 Mockito 编写的测试类。在类体的顶层，我们有模仿的依赖项和 SUT，它们的设置方式与我们对 Mockk 的设置方式类似。

然而，要注意的一个要点是没有`relaxed`模拟论证。这样做的原因是因为当行为没有被存根化时，Mockito 为行为提供了默认的答案。

### 结论

我觉得 mock 在这里大放异彩，因为默认情况下 mock 是不宽松的，这鼓励您完全控制 SUT 发出的函数调用。

## 《摩克》和《摩克托》中的论元捕捉

`argumentCaptor`是来自`mockito-kotlin`扩展库的一个函数。这有助于从被模仿的对象中捕获单个参数，通常在验证块中完成。

Mockk 变体是一个`slot`。

## 存根

通常，在编写单元测试时，我们会在模拟对象上指定函数调用的答案。这在单元测试中被称为存根。

使用 Mockito，是这样声明的:

```
`when`(dataSource.get(any())).then { buildUser() }

```

Mockito 没有对 Kotlin 协程的内置支持，这意味着[测试协程](https://blog.logrocket.com/better-kotlin-coroutine-unit-testing/)将需要使用`runTest`来编译代码。

在 Mockk 中，有用于存根协程的`coEvery { }`和用于常规函数的`every` `{ }`。这种区别增加了被测试代码的清晰度。

## 确认

单元测试的一个重要部分是验证产品代码中模拟对象的方法交互。
`suspend`功能只能被其他暂停功能调用。有了`coVerify{ }`,开发人员就可以支持存根`suspend`函数，这在一般情况下用`verify{ }`块是不可能的。

Mockk 提供了两个函数来测试挂起函数和常规函数:

1.  `coVerify` `{ }`，用于暂停功能
2.  `verify` `{ }`，用于常规功能

Mockito 使用`verify()`进行验证，并且仍然需要使用`runTest`来支持挂起功能。这仍然有效，但不如莫克的方法清晰。

## 结论

我们已经探讨了 Kotlin 项目中的单元测试，以及如何用 Mockk 和 Mockito 有效地编写测试。

您可以随意使用任何您认为有吸引力的库，但是我会推荐 Mockk，因为它在使用 Kotlin 语言时非常灵活。

## LogRocket :即时重现你的安卓应用中的问题。

[![](img/b5ae4bd0ecde7aa9d5288746416d5e18.png)](https://lp.logrocket.com/blg/kotlin-signup)

[LogRocket](https://lp.logrocket.com/blg/kotlin-signup) 是一款 Android 监控解决方案，可以帮助您即时重现问题，确定 bug 的优先级，并了解您的 Android 应用程序的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 Android 应用程序— [免费试用 LogRocket】。](hhttps://lp.logrocket.com/blg/kotlin-signup)