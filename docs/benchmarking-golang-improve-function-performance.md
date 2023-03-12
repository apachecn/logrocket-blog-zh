# Golang 中的基准测试:提高功能性能

> 原文：<https://blog.logrocket.com/benchmarking-golang-improve-function-performance/>

基准是一种函数，它多次执行一个代码段，并将每个输出与标准进行比较，评估代码的整体性能水平。Golang 在`testing`包和`go`工具中包含了用于编写基准的内置工具，因此您可以在不安装任何依赖项的情况下编写有用的基准。

在本教程中，我们将介绍一些在 Go 中运行一致和准确的基准测试的最佳实践，包括编写基准测试函数和解释结果的基础知识。

按照这个教程，你需要一个关于 Go 语法的基础知识和一个在你的电脑上的 Go 安装。我们开始吧！

## 为基准设定合适的条件

要使基准测试有用，每次执行的结果必须一致且相似，否则，将很难衡量被测试代码的真实性能。

运行基准测试的机器的状态会极大地影响基准测试的结果。电源管理、后台进程和热量管理的影响会影响测试结果，使其不准确和不稳定。

因此，我们需要尽可能减少对环境的影响。如果可能的话，您应该使用一台物理机器或者一台远程服务器来执行您的基准测试，在那里没有其他东西在运行。

但是，如果您无法访问保留的机器，那么您应该在运行基准测试之前关闭尽可能多的程序，最大限度地减少其他进程对基准测试结果的影响。

此外，为了确保更稳定的结果，您应该在记录测量值之前运行几次基准测试，确保系统充分预热。

最后，将被基准测试的代码与程序的其余部分隔离开来是至关重要的，例如，通过模仿网络请求。

## 用 Golang 写标杆

让我们通过编写一个简单的基准来演示 Go 中基准测试的基本原理。我们将确定以下函数的性能，该函数计算 1 到整数之间的所有质数:

```
// main.go
func primeNumbers(max int) []int {
    var primes []int

    for i := 2; i < max; i++ {
        isPrime := true

        for j := 2; j <= int(math.Sqrt(float64(i))); j++ {
            if i%j == 0 {
                isPrime = false
                break
            }
        }

        if isPrime {
            primes = append(primes, i)
        }
    }

    return primes
}

```

上面的函数通过检查一个数是否能被 2 和它的平方根之间的数整除来确定这个数是否是质数。让我们在`main_test.go`中为这个函数编写一个基准:

```
package main

import (
    "testing"
)

var num = 1000

func BenchmarkPrimeNumbers(b *testing.B) {
    for i := 0; i < b.N; i++ {
        primeNumbers(num)
    }
}

```

像 Go 中的[单元测试一样，基准函数被放在一个`_test.go`文件](https://blog.logrocket.com/a-deep-dive-into-unit-testing-in-go/)中，每个基准函数都应该有一个`func BenchmarkXxx(*testing.B)`作为签名，由`testing.B`类型管理基准函数的计时。

`b.N`指定迭代次数；该值不是固定的，而是动态分配的，确保基准在默认情况下至少运行一秒钟。

在上面的`BenchmarkPrimeNumbers()`函数中，`primeNumbers()`函数将被执行`b.N`次，直到开发者对基准的稳定性感到满意。

## 在 Go 中运行基准测试

为了在 Go 中运行基准测试，我们将把`-bench`标志附加到`go test`命令上。`-bench`的参数是一个正则表达式，它指定应该运行哪些基准，这在您想要运行基准函数的子集时很有帮助。

要运行所有基准，请使用`-bench=.`，如下所示:

```
$ go test -bench=.
goos: linux
goarch: amd64
pkg: github.com/ayoisaiah/random
cpu: Intel(R) Core(TM) i7-7560U CPU @ 2.40GHz
BenchmarkPrimeNumbers-4            14588             82798 ns/op
PASS
ok      github.com/ayoisaiah/random     2.091s

```

`goos`、`goarch`、`pkg`和`cpu`分别描述了操作系统、架构、封装和 CPU 规格。`BenchmarkPrimeNumbers-4`表示运行的基准函数的名称。`-4`后缀表示用于运行基准测试的 CPU 数量，由`GOMAXPROCS`指定。

在函数名的右边，有两个值，`14588`和`82798 ns/op`。前者表示循环执行的总次数，而后者是每次迭代完成所用的平均时间，以纳秒/操作表示。

在我的笔记本电脑上，`primeNumbers(1000)`函数运行了 14588 次，每次调用平均需要 82798 纳秒才能完成。为了验证基准测试产生了一致的结果，您可以通过向`-count`标志传递一个数字来多次运行它:

```
$ go test -bench=. -count 5
goos: linux
goarch: amd64
pkg: github.com/ayoisaiah/random
cpu: Intel(R) Core(TM) i7-7560U CPU @ 2.40GHz
BenchmarkPrimeNumbers-4            14485             82484 ns/op
BenchmarkPrimeNumbers-4            14557             82456 ns/op
BenchmarkPrimeNumbers-4            14520             82702 ns/op
BenchmarkPrimeNumbers-4            14407             87850 ns/op
BenchmarkPrimeNumbers-4            14446             82525 ns/op
PASS
ok      github.com/ayoisaiah/random     10.259s

```

## 跳过单元测试

如果测试文件中存在任何单元测试函数，那么当您运行基准测试时，这些函数也会被执行，从而导致整个过程花费更长时间或者基准测试失败。

为了避免在测试文件中执行任何测试函数，向`-run`标志传递一个正则表达式:

```
$ go test -bench=. -count 5 -run=^#

```

`-run`标志用于指定应该执行哪些单元测试。通过使用`^#`作为`-run`的参数，我们有效地过滤掉了所有的单元测试功能。

## 各种输入的基准测试

在对您的代码进行基准测试时，测试一个函数在有各种输入时的行为是非常重要的。我们将利用通常用于在 Go 中编写单元测试的表驱动测试模式来指定各种输入。接下来，我们将使用 [`b.Run() method`](https://pkg.go.dev/testing#B.Run) 为每个输入创建一个子基准:

```
var table = []struct {
    input int
}{
    {input: 100},
    {input: 1000},
    {input: 74382},
    {input: 382399},
}

func BenchmarkPrimeNumbers(b *testing.B) {
    for _, v := range table {
        b.Run(fmt.Sprintf("input_size_%d", v.input), func(b *testing.B) {
            for i := 0; i < b.N; i++ {
                primeNumbers(v.input)
            }
        })
    }
}

```

当您运行基准测试时，结果将以如下所示的格式显示。请注意每个子基准的名称是如何附加到主基准函数名称上的；[认为给每个子基准](https://go.dev/blog/subtests)一个不同的名称来反映被测试的输入是最佳实践:

```
$ go test -bench=.
BenchmarkPrimeNumbers/input_size_100-4            288234              4071 ns/op
BenchmarkPrimeNumbers/input_size_1000-4            14337             82603 ns/op
BenchmarkPrimeNumbers/input_size_74382-4              43          27331405 ns/op
BenchmarkPrimeNumbers/input_size_382399-4              5         242932020 ns/op

```

对于较大的输入值，函数需要更多的时间来计算结果，并且完成的迭代次数较少。

## 调整最小时间

之前的基准测试只运行了五次，样本量太小，不可信。为了获得更准确的结果，我们可以使用`-benchtime`标志增加基准测试运行的最短时间:

```
$ go test -bench=. -benchtime=10s
BenchmarkPrimeNumbers/input_size_100-4           3010218              4073 ns/op
BenchmarkPrimeNumbers/input_size_1000-4           143540             86319 ns/op
BenchmarkPrimeNumbers/input_size_74382-4             451          26289573 ns/op
BenchmarkPrimeNumbers/input_size_382399-4             43         240926221 ns/op
PASS
ok      github.com/ayoisaiah/random     54.723s

```

`-benchtime`的参数设置基准函数运行的最短时间。在这种情况下，我们将其设置为 10 秒。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

控制基准运行时间的另一种方法是为每个基准指定期望的迭代次数。为此，我们将以`Nx`到`-benchtime`的形式传递一个输入，用`N`作为所需的数字:

```
$ go test -bench=. -benchtime=100x
BenchmarkPrimeNumbers/input_size_100-4               100              4905 ns/op
BenchmarkPrimeNumbers/input_size_1000-4              100             87004 ns/op
BenchmarkPrimeNumbers/input_size_74382-4             100          24832746 ns/op
BenchmarkPrimeNumbers/input_size_382399-4            100         241834688 ns/op
PASS
ok      github.com/ayoisaiah/random     26.953s

```

## 显示内存分配统计信息

Go 运行时还跟踪被测试代码的内存分配，帮助您确定代码的一部分是否可以更有效地使用内存。

要在基准输出中包含内存分配统计信息，请在运行基准时添加`-benchmem`标志:

```
$ go test -bench=. -benchtime=10s -benchmem
BenchmarkPrimeNumbers/input_size_100-4           3034203              4170 ns/op             504 B/op          6 allocs/op
BenchmarkPrimeNumbers/input_size_1000-4           138378             83258 ns/op            4088 B/op          9 allocs/op
BenchmarkPrimeNumbers/input_size_74382-4             422          26562731 ns/op          287992 B/op         19 allocs/op
BenchmarkPrimeNumbers/input_size_382399-4             46         255095050 ns/op         1418496 B/op         25 allocs/op
PASS
ok      github.com/ayoisaiah/random     55.121s

```

在上面的输出中，第四列和第五列分别表示每个操作分配的平均字节数和每个操作的分配数。

## 让您的代码更快

如果您已经确定正在进行基准测试的功能没有达到可接受的性能阈值，那么下一步就是找到一种方法来加快操作速度。

根据所讨论的操作，有几种不同的方法可以做到这一点。首先，您可以尝试使用更有效的算法来获得想要的结果。或者，您可以同时执行计算的不同部分。

在我们的示例中，`primeNumbers()`函数的性能对于小数字来说是可以接受的，但是，随着输入的增长，它会表现出指数行为。为了提高其性能，我们可以将实现改为更快的算法，如厄拉多塞的[筛子:](https://brilliant.org/wiki/sieve-of-eratosthenes/)

```
// main.go
func sieveOfEratosthenes(max int) []int {
    b := make([]bool, max)

    var primes []int

    for i := 2; i < max; i++ {
        if b[i] {
            continue
        }

        primes = append(primes, i)

        for k := i * i; k < max; k += i {
            b[k] = true
        }
    }

    return primes
}

```

新函数的基准与`BenchmarkPrimeNumbers`函数相同，但是，调用了`sieveOfEratosthenes()`函数:

```
// main_test.go
func BenchmarkSieveOfErastosthenes(b *testing.B) {
    for _, v := range table {
        b.Run(fmt.Sprintf("input_size_%d", v.input), func(b *testing.B) {
            for i := 0; i < b.N; i++ {
                sieveOfEratosthenes(v.input)
            }
        })
    }
}

```

运行基准测试后，我们会收到以下结果:

```
$ go test -bench=Sieve
BenchmarkSieveOfErastosthenes/input_size_100-4           1538118               764.0 ns/op
BenchmarkSieveOfErastosthenes/input_size_1000-4           204426              5378 ns/op
BenchmarkSieveOfErastosthenes/input_size_74382-4            2492            421640 ns/op
BenchmarkSieveOfErastosthenes/input_size_382399-4            506           2305954 ns/op
PASS
ok      github.com/ayoisaiah/random     5.646s

```

乍一看，我们可以看到厄拉多塞算法的筛子比以前的算法性能好得多。然而，我们可以使用像`[benchstat](https://pkg.go.dev/golang.org/x/perf/cmd/benchstat)`这样的工具来帮助我们计算和比较基准统计数据，而不是目测结果来比较运行之间的性能。

## 比较基准测试结果

为了将我们的基准测试的两个实现的输出与`benchstat`进行比较，让我们从将每个实现存储在一个文件中开始。首先，运行旧的`primeNumbers()`函数实现的基准，并将其输出保存到一个名为`old.txt`的文件中:

```
$ go test -bench=Prime -count 5 | tee old.txt

```

`tee`命令将命令的输出发送到指定的文件，并打印到标准输出。现在，我们可以用`benchstat`查看基准测试的结果。首先，让我们确保它已经安装:

```
$ go install golang.org/x/perf/cmd/[email protected]

```

然后，运行下面的命令:

```
$ benchstat old.txt
name                              time/op
PrimeNumbers/input_size_100-4     3.87µs ± 1%
PrimeNumbers/input_size_1000-4    79.1µs ± 1%
PrimeNumbers/input_size_74382-4   24.6ms ± 1%
PrimeNumbers/input_size_382399-4   233ms ± 2%

```

`benchstat`显示样本的平均时间差以及百分比变化。在我的例子中，`±`的变化在 1%到 2%之间，这是理想的。

任何大于百分之五的数据都表明一些样本不可靠。在这种情况下，您应该重新运行基准测试，尽可能保持您的环境稳定以增加可靠性。

接下来，将对`BenchmarkPrimeNumbers()`中的`primeNumbers()`的调用改为`sieveOfEratosthenes()`，并再次运行基准测试命令，这一次将输出通过管道传输到一个`new.txt`文件:

```
$ go test -bench=Prime -count 5 | tee new.txt

```

基准运行结束后，使用`benchstat`比较结果:

```
$ benchstat old.txt new.txt
name                              old time/op  new time/op  delta
PrimeNumbers/input*size*100-4     3.90µs ± 1%  0.76µs ± 2%  -80.48%  (p=0.008 n=5+5)
PrimeNumbers/input*size*1000-4    79.4µs ± 1%   5.5µs ± 1%  -93.11%  (p=0.008 n=5+5)
PrimeNumbers/input*size*74382-4   25.0ms ± 1%   0.4ms ± 1%  -98.47%  (p=0.008 n=5+5)
PrimeNumbers/input*size*382399-4   236ms ± 1%     2ms ± 0%  -99.13%  (p=0.008 n=5+5)
```

`delta`列报告性能的百分比变化、P 值以及被认为有效的样本数`n`。如果您看到一个`n`值低于采集的样本数，这可能意味着在收集样本时您的环境不够稳定。查看 [benchstat 文档](https://pkg.go.dev/golang.org/x/perf/cmd/benchstat)以了解您可用的其他选项。

## 结论

基准测试是测量代码不同部分性能的有用工具。它允许我们在对系统进行更改后，识别潜在的优化、性能改进或回归的机会。

Go 提供的用于基准测试的工具简单易用且可靠。在本文中，我们仅仅触及了这些包的皮毛。感谢您的阅读，祝您编码愉快！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)