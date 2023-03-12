# 用 Python - LogRocket 博客构建一个机器学习的垃圾邮件检测器

> 原文：<https://blog.logrocket.com/email-spam-detector-python-machine-learning/>

我们都曾经收到过垃圾邮件。垃圾邮件是一种一次发送给大量用户的电子邮件，通常包含神秘消息、诈骗或最危险的网络钓鱼内容。

虽然垃圾邮件有时是由人工发送的，但大多数情况下，它们是使用机器人发送的。大多数流行的电子邮件平台，如 Gmail 和 Microsoft Outlook，通过筛选可识别的短语和模式来自动过滤垃圾邮件。一些常见的垃圾邮件包括虚假广告、连锁邮件和假冒邮件。虽然这些内置的垃圾邮件检测器通常非常有效，但有时，一封伪装得特别好的垃圾邮件可能会漏网，进入您的收件箱而不是垃圾邮件文件夹。

点击垃圾邮件是很危险的，会将你的电脑和个人信息暴露给不同类型的恶意软件。因此，实施额外的安全措施来保护您的设备非常重要，尤其是当它处理用户数据等敏感信息时。

在本教程中，我们将使用 Python 来构建一个垃圾邮件检测器。然后，我们将使用[机器学习来训练我们的垃圾邮件检测器](https://blog.logrocket.com/best-javascript-machine-learning-libraries-in-2021/)来识别电子邮件并将其分类为垃圾邮件和非垃圾邮件。我们开始吧！

### 先决条件

首先，我们将导入必要的依赖项。Pandas 是一个主要由数据科学家用于数据清理和分析的库。

Scikit-learn ，也称为 Sklearn，是一个健壮的 Python 机器学习库。它为机器学习和统计建模提供了一系列有效的工具，包括通过一致的界面进行分类、回归、聚类和降维。

运行以下命令导入必要的依赖项:

```
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.feature_extraction.text import CountVectorizer
from sklearn import svm 

```

## 入门指南

首先，运行下面的代码:

```
spam = pd.read_csv('spam.csv')

```

在上面的代码中，我们创建了一个`spam.csv`文件，我们将把它转换成一个数据框并保存到我们的文件夹 spam 中。数据框是一种以表格形式在行和列中对齐数据的结构，如下图所示。

继续从 GitHub 下载样本文件[`.csv`](https://github.com/SmallLion/Python-Projects/blob/main/Spam-detection/spam.csv)。它模仿了典型的电子邮件收件箱的布局，包括 5000 多个示例，我们将使用这些示例来训练我们的模型。它应该如下图所示:

![Email Inbox Example CSV file](img/6aeeb0f7c63f6e28bb9669f3d121c4f5.png)

## Python `train_test_split()`

我们将使用训练-测试分离方法来训练我们的垃圾邮件检测器识别和分类垃圾邮件。训练测试分割是一种用于评估机器学习算法性能的技术。我们可以将它用于任何监督学习算法的分类或回归。

该过程包括获取一个数据集并将其分成两个独立的数据集。第一个数据集用于拟合模型，称为训练数据集。对于第二个数据集，即测试数据集，我们向模型提供输入元素。最后，我们进行预测，将它们与实际输出进行比较。

*   训练数据集:用于拟合机器学习模型
*   测试数据集:用于评估机器学习模型的拟合度

在实践中，我们会根据已知输入和输出的可用数据来拟合模型。然后，我们会根据没有预期输出或目标值的新示例进行预测。我们将从样本`.csv`文件中获取数据，该文件包含预先分类为垃圾邮件和非垃圾邮件的示例，分别使用标签`spam`和`ham`。

为了将数据分成两个数据集，我们将使用 scikit-learn 的`train_test_split()`方法。

假设加载的数据集中有 100 条记录。如果我们指定测试数据集为 30 %,我们将拆分 70 条记录用于训练，并将剩余的 30 条记录用于测试。

运行下面的命令:

```
z = spam['EmailText']
y = spam["Label"]
z_train, z_test,y_train, y_test = train_test_split(z,y,test_size = 0.2)

```

`z = spam['EmailText']`将列`EmailText`从垃圾邮件分配给`z`。它包含我们将在模型中运行的数据。`y = spam["Label"]`将列`Label`从垃圾邮件分配到`y`，告诉模型更正答案。你可以在下面看到原始数据集的截图。

函数`z_train, z_test,y_train, y_test = train_test_split(z,y,test_size = 0.2)`将`z`和`y`列分为用于训练输入的`z_train`、用于训练标签的`y_train`、用于测试输入的`z_test`和用于测试标签的`y_test`。

`test_size=0.2`将测试设置为`z`和`y`的 20%。你可以在下面的截图中看到这样的例子，其中`ham`标签表示非垃圾邮件，`spam`代表已知的垃圾邮件:

![Spam Non Spam Emails CSV file](img/63d7fb560f3cc8c13640fcae194f6457.png)

## 提取特征

接下来，我们将运行下面的代码:

```
cv = CountVectorizer()
features = cv.fit_transform(z_train)

```

在`cv= CountVectorizer()`中，`CountVectorizer()`在一个称为标记化的过程中随机地给每个单词分配一个数字。然后，它统计单词出现的次数并保存到`cv`。至此，我们只给`cv`分配了一个方法。

`features = cv.fit_transform(z_train)`为每个单词随机分配一个数字。它统计每个单词出现的次数，然后保存到下图中的`cv.`，`0`代表邮件的索引。中间一列中的数字序列表示我们的函数识别的单词，右边的数字表示该单词被计数的次数:

![Spam Word Occurrences Example](img/89adf59047b31011a6b86b0dbb8a4176.png)

比如上图中，`1841`对应的词在邮件号`0`中用了两次。

现在，我们的机器学习模型将能够根据垃圾邮件中常见的某些单词的出现次数来预测垃圾邮件。

## 构建模型

支持向量机算法 SVM 是用于分类和回归的线性模型。SVM 的想法很简单，算法创建一条线，或一个超平面，将数据分成几类。SVM 可以解决线性和非线性问题:

![Support Vector Machine Example](img/4a2b104899b92e19d8af79429a67475e.png)

让我们用下面的代码创建一个 SVM 模型:

```
model = svm.SVC()
model.fit(features,y_train)

```

`model = svm.SVC()`将`svm.SVC()`分配给模型。在`model.fit(features,y_train)`功能中，`model.fit`用`features`和`y_train`训练模型。然后，它对照`y_train`标签检查预测，并调整其参数，直到达到可能的最高精度。

## 测试我们的垃圾邮件检测器

现在，为了确保准确性，让我们测试我们的应用程序。运行下面的代码:

```
features_test = cv.transform(z_test)
print("Accuracy: {}".format(model.score(features_test,y_test)))

```

`features_test = cv.transform(z_test)`函数根据`z_test`进行预测，这些预测将经过计数矢量化。它将结果保存到`features_test`文件中。

在`print(model.score(features_test,y_test))`函数中，`mode.score()`根据`y_test`中的实际标签对`features_test`的预测进行评分。

![Email Spam Detector Test Example](img/9c237298f12e1b18c68617cce1b7cdbc.png)

在上图中，你会看到我们能够以 97%的准确率对垃圾邮件进行分类。

该项目的完整脚本如下:

```
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.feature_extraction.text import CountVectorizer
from sklearn import svm

spam = pd.read_csv('C:\\Users\\nethm\\Downloads\\spam.csv')
z = spam['EmailText']
y = spam["Label"]
z_train, z_test,y_train, y_test = train_test_split(z,y,test_size = 0.2)

cv = CountVectorizer()
features = cv.fit_transform(z_train)

model = svm.SVC()
model.fit(features,y_train)

features_test = cv.transform(z_test)
print(model.score(features_test,y_test))

```

## 摘要

在本教程中，我们学习了如何构建和运行我们的模型，将我们的预测与实际输出进行比较。最后，我们使用计数矢量化来测试我们的模型。

对于我们的垃圾邮件检测器，我们只是触及了机器学习的皮毛。我们还可以添加修改，如自动化 CSV 文件或提供语音助手。

希望这篇文章能让你对 Python 中一些流行的机器学习算法有更深入的了解。编码快乐！