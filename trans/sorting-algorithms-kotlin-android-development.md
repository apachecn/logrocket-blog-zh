# 用于 Android 开发的 Kotlin 排序算法

> 原文：<https://blog.logrocket.com/sorting-algorithms-kotlin-android-development/>

算法是程序设计不可或缺的一部分，通常伴随着数据结构。它们共同构成了数据结构和算法(DSA ),这是提高代码效率的关键。在编程中，算法通常指的是你在解决特定问题时选择遵循的模式，而数据结构则是你选择如何组织数据。

一种流行的算法是排序算法。我们可以使用排序算法，通过比较运算符来重新排列数组或元素的集合，比较运算符使用各自的数据结构来确定元素的新顺序。

在本文中，我们将探索 Kotlin 中一些流行的排序算法，包括冒泡排序、合并排序、基数排序和堆排序。然后，我们将深入研究冒泡排序算法，考虑它的一些好处。

要跟随本教程，你需要具备 Kotlin 的基础知识，并安装 Android Studio 或 IntelliJ IDE。我们开始吧！

## 目录

## 排序算法概述

就地排序算法通过修改列表中元素的排列来对列表进行排序，使用常数空间来产生输出。一个很好的例子包括[I](https://github.com/daolq3012/Kotlin-Algorithms#11-insertion-sort)[n 选择](https://github.com/daolq3012/Kotlin-Algorithms#11-insertion-sort)和[选择排序](https://github.com/daolq3012/Kotlin-Algorithms#12-selection-sort)，它们不需要任何额外的空间来排序列表。

排序算法可以以各种方式分类，例如，稳定的或不稳定的，内部的或外部的，这取决于所使用的数据的场景。

## 内部与外部排序算法

当所有数据都放在内存或主存中时，就发生了内部排序。一些例子包括堆、冒泡、选择、快速和插入排序。请注意，在内部排序中，可以容纳的输入数量取决于内存的大小。

当需要排序的所有数据无法一次放入内存时，就会出现外部排序，这非常适合大量数据。您可以通过使用硬盘、闪存驱动器和光盘等外部存储设备来实现外部排序。一些例子包括 merge 及其所有变体、标签和外部基数排序。

## 稳定与不稳定排序算法

当相同的两个数据点以相同的顺序出现时，即使对数据进行了排序，也会出现稳定排序。一些例子包括合并、插入和冒泡排序。不稳定排序与稳定排序相反。当相同的两个数据点在数据排序后以不同的顺序出现，导致位置发生变化时，就会出现这种情况。一些例子包括堆和快速排序。

## 冒泡排序

最简单的排序算法之一，[冒泡排序](https://github.com/daolq3012/Kotlin-Algorithms#13-bubble-sort)比较相邻值。为了实现这种排序，它会在需要时交换这些相邻的值。这种排序需要将较大的值放在顶部，即冒泡。但由于时间复杂度较高，不适用于海量数据集。

## 合并排序

[合并排序算法](https://github.com/daolq3012/Kotlin-Algorithms#15-merge-sort)遵循分而治之的范式。它需要将两个数组分成相等的两半，然后通过排序组合在一起。这种组合导致合并，而合并又会与另一个数组组合在一起。只有当数组为空或者只剩下一个元素而没有其他元素要合并时，该操作才会结束。合并操作通常需要将两个较小的数组组成一个较大的数组。

## 基数排序

[基数排序算法](https://en.wikipedia.org/wiki/Radix_sort)使用逐位排序，从最低有效位到最高有效位。它是一种非比较算法，通过基于基数创建元素并将其分布到桶中来实现这一点。因此，也称为桶或数字排序。

## 堆排序

[堆排序](https://github.com/daolq3012/Kotlin-Algorithms#17-heap-sort)是一种基于比较的排序技术，它基于二进制堆数据结构。这是一个既就地又不稳定的算法，但它可以变得稳定。没有像递归和最小内存使用这样的高级计算机科学概念，理解起来非常简单。主要用于混合算法，像`IntroSort`。

## 快速排序

[快速排序算法](https://github.com/daolq3012/Kotlin-Algorithms#16-quick-sort)类似于合并排序，因为它们都涉及分治的概念。但是，快速排序算法也是一种就地算法。它的工作方式是从数组中选取一个主元素，并根据其他元素是大于还是小于主元素将它们划分为两个子数组。它也被称为分区交换排序。

## 冒泡排序算法:Android 开发的好处

冒泡排序算法编写简单，易于理解，并且只需要几行代码。这种实现上的直接性有助于 Android 开发人员降低应用程序设计中的错误率，并提高应用程序的效率。它可以检测数组中的微小错误，可以使用线性复杂度`2n`来修复。

### 实现冒泡排序算法

实现冒泡排序算法基本上包括比较两个值并在需要时交换它们，如下面的代码片段所示:

```
class BubbleSortingAlgorithm {

    static void bubbleSorting(int arrayNumber[])
    {
        int n = arrayNumber.length; 
        int temp=0;
        for (int i = 0; i < n; i++){
            for (int j = 1; j < (n - i); j++){
                if (arrayNumber[j-1] > arrayNumber[j]) {

                    // the element will be swapped using the swapping method below
                    temp = arrayNumber[j-1];
                    arrayNumber[j-1] = arrayNumber[j];
                    arrayNumber[j] = temp;

      }
    }
 }
}        

    // This is the main method to test run the bubbleSorting implementation logic

    public static void main(String args[])
    {
        int arrayVariable[] = {10, 50, 110, 90, 1, 9, 200, 4, 2000};
        System.out.println("This is the Array values before Sorting")
        for(int i=0; i < arrayVariable.length; i++){
          System.out.print(arrayVariable[i] + " ")
    }
       System.out.print();    

        // Sorting the elements using Bubble Sorting Algorithm
        bubbleSorting(arrayVariable);
        System.out.println("This is the value of the Array after Sorting");
        for(int i=0; i < arrayVariable.length; i++){
        System.out.print(arrayVariable[i] + " ")
       }
    }
}

```

上面代码的输出如下:

```
This is the Array values before Sorting
10, 50, 110, 90, 1, 9, 200, 4, 2000
This is the value of the Array after Sorting
1, 4, 9, 10, 50, 90, 110, 200, 2000

```

### 冒泡排序算法:挑战

使用冒泡排序算法的主要挑战是时间复杂度。对于大型数据集来说，这不是一种有效的方法，因为它需要更长的时间来完成排序，导致运行时间为`O(n2)`。因此，冒泡排序将需要更长的时间来完成所有需要的交换。然而，有一个改进版本的冒泡排序，称为改进的冒泡排序，它更有效，可以用于这样的用例。

### 比较冒泡排序、插入和选择排序算法

下图从时间和空间复杂性方面比较了冒泡、插入和选择排序算法:

| 最坏情况空间复杂度 | 平均案例空间复杂度 | 最佳情况时间复杂度 | 冒泡排序算法 |
| --- | --- | --- | --- |
| O(n^2)和 O(1) | O(n^2) | O(n) | 选择排序算法 |
| O(n^2)和 O(1) | O(n^2) | O(n^2) | 插入排序算法 |
| O(n^2)和 O(1) | O(n^2) | O(n) | 从上面的比较中，我们观察到冒泡和插入排序算法之间似乎有联系。但是，插入排序算法比冒泡排序算法需要更少的交换来完成其操作。 |

冒泡排序算法:性能

### 冒泡排序算法的性能清楚地表明，它只适用于小数据集；它的最坏情况时间复杂度为`O(n2)`，空间复杂度为`O(n)`。

冒泡排序中发生的交换数量等于给定数组中要反转的对的数量。因此，交换次数越多，冒泡排序算法的时间就越长。

结论

## 在本教程中，我们从整体的角度回顾了 Kotlin 中的一些排序算法。我们重点讨论了冒泡排序算法，它的优点、实现、挑战和性能。冒泡算法是理解排序算法类型的最简单和最容易的方法，但是，我们认为它不适合大型和复杂的数据集。

我希望你喜欢这篇文章。如果你在开发你的 Android 应用时采用了这种算法，一定要留下评论。编码快乐！

LogRocket :即时重现你的安卓应用中的问题。

## [LogRocket](https://lp.logrocket.com/blg/kotlin-signup) 是一款 Android 监控解决方案，可以帮助您即时重现问题，确定 bug 的优先级，并了解您的 Android 应用程序的性能。

[![](img/b5ae4bd0ecde7aa9d5288746416d5e18.png)](https://lp.logrocket.com/blg/kotlin-signup)

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 Android 应用程序— [免费试用 LogRocket】。](hhttps://lp.logrocket.com/blg/kotlin-signup)

Start proactively monitoring your Android apps — [try LogRocket for free](hhttps://lp.logrocket.com/blg/kotlin-signup).