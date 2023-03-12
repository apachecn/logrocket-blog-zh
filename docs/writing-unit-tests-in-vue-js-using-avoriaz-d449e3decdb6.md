# 使用 Avoriaz - LogRocket 博客编写健壮的 Vue.js 单元测试

> 原文：<https://blog.logrocket.com/writing-unit-tests-in-vue-js-using-avoriaz-d449e3decdb6/>

### 设置我们的环境

为了开始我们的项目，我们将使用`Vue-cli`。这将帮助我们跳过配置 webpack 的过程和所有其他复杂的过程。

如果没有安装`Vue-cli`，我们可以通过运行以下命令来安装:

```
npm install -g vue-cli
```

一旦我们安装了`vie-cli`，让我们继续创建应用程序。为此，我们运行:

```
vue init webpack unittest
```

在这个阶段，会提示您几个问题。您可以接受大多数的默认选项，唯一的要求是您回答“是”来包含 vue-router，并回答“是”来设置 Karma 和 Mocha 的单元测试。

### 了解默认测试

接下来，让我们来看看我们的`test\unit\specs\Hello.spec.js`。我们会注意到这段代码:

在上面的代码块中，我们会注意到:

*   Vue 库的导入
*   Hello 组件的导入
*   名为 Hello.vue 的测试描述
*   一个单元测试说“应该呈现正确的内容”。在这个测试中，我们安装我们的组件，然后声明我们的`expect`函数，它检查输出是否等于我们所期望的。

注意，在上面的测试中，我们期望`hello`类下的`h1`标签的文本内容等于`welcome to Your Vue.js App`

这解释了编写 Vue 测试是多么简单。虽然这个方法很简单，但是有一个更简单的方法可以在 Vue 中编写测试。

### 使用 Avorias 编写 Vue 测试

记得我提到过在 Vue 中编写单元测试有一个更简单的方法。我们到了。这个简单的方法是使用一个名为`avorias`的包。

首先，我们需要安装软件包。为此，让我们运行以下命令。

```
// change directory to our folder
cd unittest
// install the avorias package
npm install avoriaz
```

现在让我们使用`avoriaz`重写默认的`hello`测试。为此，让我们删除`test\unit\specs\Hello.spec.js`文件，然后创建一个名为`test\unit\specs\Hello-avorias.spec.js`的新文件。

创建该文件后，让我们将它的内容替换为:

查看上面的代码块，我们注意到了与默认测试规范的以下不同之处

*   我们在默认测试中导入 mount，而不是 Vue。这是因为 avorias 附带了一个 mount helper 函数，而不是扩展 Vue，然后挂载它。
*   我们使用了`text()`函数，而不是使用`textContent` 属性。这是因为 avorias 给了我们一个叫做`text()`的帮助函数

### 创建我们的可测试组件

让我们在`src\components`文件夹中创建一个名为`Happy.vue`的新文件，并粘贴以下内容:

让我们快速解释一下上面代码块中的内容。

*   我们的模板部分包含一个 div(根元素),它包含一个循环的`h1`标签。
*   我们声明我们的数据只包含一个名为`them` 的变量，它在模板部分循环。
*   在我们的挂载调用中，我们调用了名为`get_them`的函数
*   `get_them`函数使用 axios 向[https://restcountries.eu/rest/v2/all](https://restcountries.eu/rest/v2/all%27%29.then%28%28response%29)发送一个 GET 请求。但是，我们不使用响应。我们只使用它来触发和异步事件。
*   最后，我们有两个计算属性。将一个新项目添加到`them`数组并返回它。第二个计算属性返回一个字符串`happy`

接下来，我们将看看如何测试我们的新组件。这意味着我们将测试我们的`get_them`函数，以及我们计算的属性。

### 更新我们的路线

接下来，我们需要更新我们的路线，这样我们就可以查看到目前为止我们已经构建的组件。为此，让我们打开我们的`src\router\index.js`,并将其替换为:

在上面的代码块中，我们已经导入了我们的 happy 组件，并将其添加到我们的 routes 中。

如果我们在终端中运行`npm run dev`并浏览到`[http://localhost:8080/#/happy](http://localhost:8080/#/happy)`，我们应该会看到下图:

### 测试我们的组件方法

现在，让我们测试一下。如果我们检查我们名为`get_them`的方法，我们注意到我们有一个使用`axios`的异步调用。因此，如果我们试图为我们的组件编写测试，我们会遇到各种各样的问题，例如:

*   承诺没有定义
*   超过 2000 毫秒的超时。

为了在测试过程中解决这些问题，我们将安装两个库，它们是:

*   承诺:解决“找不到可变承诺/承诺未定义”
*   Moxios:这个库将存根请求并返回假数据到其中。以便伪造异步调用的提前完成。

为了安装这些库，我们运行:

```
npm install promise moxios
```

现在我们已经安装了这些库，让我们在`test\unit\specs\`文件夹中创建一个名为`Happy-avorias.spec.js`的新文件。让我们将以下代码添加到文件中:

让我们了解一下我们在上面的代码块中做了什么:

*   正如我们之前解释的那样，我们导入了挂载函数
*   接下来，我们为存根请求导入了`Moxios`。
*   然后，我们将名为 Promise 的全局变量设置为 Promise 库的实例。这是为了让我们不会面临诸如承诺没有在 Axios 中定义的问题。
*   然后我们描述了我们的测试
*   我们调用了两个钩子`beforeEach`和`afterEach`来处理`Moxios`的安装和卸载。这意味着`Moxios`将在该文件中定义的所有测试之前安装，并将在每次测试之后卸载。
*   我们定义了两个测试标准
*   在第一个测试标准中，我们使用 Moxios 来捕捉发往[https://restcountries.eu/rest/v2/all](https://restcountries.eu/rest/v2/all%27)的请求，然后返回一个测试数据。
*   我们使用`moxios.wait`来确保在声明 expect 关键字之前承诺被解析
*   注意，我们使用`vm.data()`来访问组件的数据属性。这是`avorias`提供的一个帮助函数
*   我们检查我们的数据是否将`happy one`作为其数组内容的一部分，该数组是在请求后分配给它的。
*   在第二个测试标准中，我们还使用 Moxios 来存根请求。然而，这里我们发现数组的长度等于 4。这是我们期望的数组长度。

### 测试我们的计算数据

就在上面，我们已经看到了如何测试数据和方法。这里我们进入测试计算属性。用`avorias`测试计算属性可能有点棘手，因为`this`没有绑定到计算属性。请参考下面关于该问题的链接。

然而，不要害怕。我们将讨论如何在对计算属性运行我们自己的测试时解决这个问题。

让我们回到我们的`Happy-avorias.spec.js`，用以下内容替换它:

在上面的代码块中，我们会注意到添加了两个新的测试标准。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

*   第一个标准是它必须返回计算的数据。它检查我们的`computed_first()`属性的响应，该属性对`this`关键字没有任何作用。
*   请注意 avorias 的帮助函数`computed()`用于检查计算的属性
*   第二个标准说它的长度应该是 5。然而，这里引用的计算属性`all_of_them`使用了`this`关键字，avorias 并没有绑定到计算属性
*   注意，我们期望`app.vm.all_of_them`包含`computed_one`，而不是期望`app.computed().all_of_them()`

上面解释的是我们在运行计算数据时需要考虑的唯一差异。另外，请注意，同样的问题也适用于道具数据。

我们可以通过运行以下命令来运行所有的测试:

```
npm run unit
```

我们应该得到以下结果: