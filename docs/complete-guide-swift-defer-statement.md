# Swift defer 声明完全指南

> 原文：<https://blog.logrocket.com/complete-guide-swift-defer-statement/>

Swift `defer`语句对于我们在退出作用域之前需要做一些事情的情况非常有用——不管是什么。例如，`defer`在退出作用域之前多次执行清理操作时会很方便，比如关闭一个文件或锁定一个锁。简而言之，Swift `defer`声明提供了良好的内务管理。

`defer`关键字早在 2016 年就在 Swift 语言中引入，但很难找到好的例子，因为它在项目中似乎很少使用。在 [Swift 文档](https://docs.swift.org/swift-book/ReferenceManual/Statements.html#ID532)中提供的基本片段也不是很有帮助。

为了更清楚地说明这一主题，本文将研究 Swift 的`defer`声明和语法。我们还将看看几个真实的使用案例:

## 句法

当我们使用`defer`关键字时，我们在`defer`中提供的语句在作用域的末尾执行，就像在方法中一样。它们在每次退出*范围*之前都会被执行，即使抛出了一个错误。请注意，`defer`语句仅在当前作用域退出时执行，这可能与函数返回时不同。

`defer`关键字可以在作用域内定义。在本例中，它定义在一个函数中:

```
// This will always execute before exiting the scope
defer {
    // perform some cleanup operation here
    // statements
}

// rest of the statements

```

在这个例子中，`defer`关键字被定义在一个`do`–`catch`块中:

```
do {

// This will always execute before exiting the scope
    defer {
        // perform some cleanup operation here
        // statements
    }

    // rest of the statements that may throw error
    let result = try await fetchData()
} catch {
    // Handle errors here
}

```

即使在抛出错误或者有很多清理语句的情况下，`defer`语句仍然允许我们在退出作用域之前执行代码。`defer`有助于保持[代码的可读性和可维护性](https://blog.logrocket.com/swift-extensions-an-overview-with-examples/)。

现在，让我们看几个使用`defer`语句的例子。

## 锁

Swift `defer`语句最常见的用例是开锁。`defer`可以确保此状态被更新，即使代码有多条路径。这消除了对忘记解锁的任何担心，忘记解锁可能导致内存泄漏或死锁。

下面的代码锁定锁，将参数中的内容添加到给定的数组中，并在`defer`语句中解锁锁。在这个例子中，在将程序控制转移到另一个方法之前，锁总是解锁的。

```
func append(_ elements: [Element]) {
    lock.lock()

    defer {
        lock.unlock()
    }

    array.append(contentsOf: elements)
}

```

## 建立工作关系网

在执行网络请求时，不得不处理错误、糟糕的服务器响应或丢失数据的情况并不少见。当我们调用完成处理程序时使用一个`defer`块将有助于确保我们不会错过任何这些错误。

```
func fetchQuotes(from url: URL, completion: @escaping (Result<[Quote], Error>) -> ()) {
    var result: Result<[Quote], Error>

    defer {
        completion(result)
    }

    let task = URLSession.shared.dataTask(with: url) { data, response, error in
        if let error = error {
            result = .failure(error)
        }

        guard let response = response else {
            result = .failure(URLError(.badServerResponse))
        }

        guard let data = data else {
            result = .failure(QuoteFetchError.missingData)
        }

        result = .success(quoteResponse(for: data))
    }
    task.resume()
}

```

## 更新布局

使用`setNeedsLayout()`方法，我们可以使用`defer`来更新视图。可能需要多次调用此方法。通过使用`defer`，不用担心忘记执行`setNeedsLayout()`方法。`defer`将确保方法在退出作用域之前总是被执行。

```
func reloadAuthorsData() {
    defer {
        self.setNeedsLayout()
    }

    removeAllViews()

    guard let dataSource = quotingDataSource else { return }

    let itemsCount = dataSource.numberOfItems(in: self)

    for index in itemsCount.indices {
        let view: AuthorView = getViewForIndex(index)

        addSubview(view)

        authorViews.append(view)
    }
}

```

如果我们以编程方式更新约束，我们可以将`layoutIfNeeded()`放在`defer`语句中。这将使我们能够更新约束，而不用担心忘记调用`layoutIfNeeded()`:

```
func updateViewContstraints() {
    defer {
        self.layoutIfNeeded()
    }

    // One conditional statement to check for constraint and can return early

    // Another statement to update another constraint
}

```

## 装载指示器

`defer`语句可与装载指示器一起使用。在这种情况下，`defer`语句将确保即使出现错误，加载指示器也会执行，并且在将来的任何其他情况下，不必重复该语句:

```
func performLogin() {
    shouldShowProgressView = true

    defer {
        shouldShowProgressView = false
    }

    do {
        let _ = try await LoginManager.performLogin()

        DispatchQueue.main.async {
            self.coordinator?.successfulLogin()
        }

    } catch {
        let error = error
        showErrorMessage = true
    }
}

```

## 提交更改

`defer`语句可用于提交使用`CATransaction`做出的所有更改。这确保了即使在提前返回的`defer`语句之后有条件代码，动画事务也将被提交。

假设我们想要更新 UIButton 层的属性，然后添加动画来更新 UIButton 的框架。我们可以通过在`defer`语句中调用`commit()`方法来实现:

```
CATransaction.begin()

defer {
   CATransaction.commit()
}

// Configurations
CATransaction.setAnimationDuration(0.5)
button.layer.opacity = 0.2
button.layer.backgroundColor = UIColor.green.cgColor
button.layer.cornerRadius = 16

// View and layer animation statements

```

一个类似的用例是`AVCaptureSession`。我们在最后调用`commitConfiguration()`来提交配置更改。然而，许多`do`–`catch`语句会导致在抛出错误时提前退出。通过在`defer`语句中调用这个方法，我们确保了配置更改在退出前被提交。

```
func setupCaptureSession() {
    cameraSession.beginConfiguration()

    defer {
        cameraSession.commitConfiguration()
    }

    // Statement to check for device input, and return if there is any error
    do {
        deviceInput = try AVCaptureDeviceInput(device: device)
    } catch let error {
        print(error.localizedDescription)
        return
    }

    // Statements to update the cameraSession
    cameraSession.addInput(deviceInput)
}

```

## 单元测试

异步代码可能很难测试。我们可以使用`defer`语句，这样我们就不会忘记`wait`，直到异步测试遇到`expectation`或者超时。

```
func testQuotesListShouldNotBeEmptyy() {
    var quoteList: [Quote] = []

    let expectation = XCTestExpectation(description: #function)

    defer {
        wait(for: [expectation], timeout: 2.0)
    }

    QuoteKit.fetchQuotes { result in
        switch result {
            case .success(let quotes):
                quoteList = quote
                expectation.fulfill()
            case .failure(let error):
                XCTFail("Expected quotes list, but failed \(error).")
        }
    }
    XCTAssert(quoteList.count > 0, "quotes list is empty")
}

```

类似地，如果在我们检查响应时存在多个 guard 语句，我们可以使用带有`fulfill()`方法的`defer`语句来确保异步测试满足`expectation`:

```
defer {
    expectation.fulfill()
}

// Many guard statements where we call expectation.fulfill() individually.

```

## 结论

Swift `defer`语句对于清理资源和改进代码来说是强大的。`defer`语句将保持您的 iOS 应用程序代码平稳运行，即使团队成员更新了一个方法或添加了一个条件语句。`defer`无论我们如何退出都执行，并且将来证明项目不受可能改变范围流的变更的影响，从而降低出错的可能性。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)