# 高级 Vue.js 概念:Mixins、自定义指令和更多- LogRocket 博客

> 原文：<https://blog.logrocket.com/advanced-vue-js-concepts-mixins-custom-directives-filters-transitions-and-state-management-ca6955905156/>

### 设置我们的环境

首先，为了跳过为从 ES6 编译到 ES5 而配置 webpack 的过程，我们将使用 Vue CLI。如果您没有安装 Vue CLI，请安装它！

`sudo npm install -g vue-cli`

### 混合视图

混合是我们可以重用的代码块。就当是坚持干原则的一种方式吧。DRY 是首字母缩写，代表“不要重复自己”。

Mixins 是为 Vue 组件分发可重用功能的一种灵活方式。mixin 对象可以包含任何组件选项。当一个组件使用 mixin 时，mixin 中的所有选项都将“混合”到组件自己的选项中。

理解 Vue mixins 如何工作的最简单方法是实际使用它们。考虑两个组件:“A”和“B”。两者都根据用户生命周期中的创建日期来筛选用户。基于用户的创建日期过滤用户的功能将出现在两个不同的组件中，导致您重复自己。

正如我们所讨论的，这是不好的。但是有一种更简单的方法可以根据创建日期过滤用户，避免重复。有什么猜测吗？

Vue mixins。猜得好。

请记住，你越是重复，就越容易出错。让我们写一些代码。

首先，让我们通过运行以下命令来创建一个新项目:

```
#create a new project vue init webpack mixins #change into the new project cd mixings #install npm packages npm install
```

一旦我们完成了上面的代码，让我们继续编辑我们的`src/components/hello.vue`到:

让我们快速看一下上面的代码块。在我们的模板部分，我们有一个内容为非活动用户的 H2 标签。接下来，我们有一个带有循环 li 标签的 ul 标签，它打印出每个不活动用户的姓名和年龄。

现在看一下脚本部分。注意，我们有一个普通的 Vue 组件实例。在我们的数据部分，我们有两个属性，一个是 status，另一个是名为`users.`的数组

在我们的方法部分，我们有两个方法:`get_active_or_inactive`和`filter_by_date`，前者根据我们的状态获取所有活动用户。这将根据`created_at`属性以升序返回一个用户数组。

最后，我们有计算部分，它包含一个名为`inactiveUsers`的方法，该方法调用了`get_active_or_inactive`和`filter_by_date`函数。

我们知道 hello 组件现在做什么，但是我们可能仍然想知道目标是什么。目标是有两个组件，一个显示活动用户，另一个显示非活动用户。这两个代码既有`get_active_or_inactive`方法，也有`filter_by_date`方法(这是重复的)。

所以，我们用 Vue mixins 来统一那些重复的功能。

接下来，我们将创建一个名为`Active.vue`的新组件，并将以下内容放入其中:

如果我们看一下上面的活动组件，与“你好”组件相比的主要变化是:

*   数据中的状态属性从 0 更改为 1
*   计算属性从 inactiveUsers 更改为 activeUsers

让我们把重复的代码统一成一个 mixin。为此，在我们的`src`文件夹中创建一个名为 mixins 的新目录。接下来，在我们的`src/mixins`文件夹中创建一个名为`firstMixin.js`的文件，并粘贴到:

注意，在上面的代码片段中，它与一个新的 Vue 组件相同。事实上，我们只是将两个组件的方法部分移到了这个新的类似组件的结构中。

然后我们需要改变我们的组件来使用这个 mixin，如下所示:

看上面的两个组件，与原始组件的区别是:

*   我们的组件中不再有我们的方法
*   我们在脚本中添加了 mixin 的导入
*   我们在组件的 mixins 部分声明了我们的 mixin

让我们把所有这些结合在一起，看看是否行得通。为此，将我们的`app.vue`文件替换为:

上述`app.vue`文件与原始`app.vue`文件的唯一区别在于:

*   我们已经导入了活动组件，并在组件部分中声明了它
*   我们还向浏览器呈现了我们的活动组件

如果我们在终端上运行 npm run dev 命令，并浏览到 [http://localhost:8080/](http://localhost:8080/) ，我们应该会看到以下内容:

### 自定义指令

每个人都听说过指令。如果不使用`v-if`、`v-for`、`v-show`等指令，你很可能无法用 Vue 构建一个功能丰富的应用。

在本节中，我们将学习如何构建一个名为`v-test`的自定义指令。这个函数用 base64 表示的文本替换给它的文本。

指令是可以附加到 DOM 元素上的微小命令。它们以 v-为前缀，让库知道您正在使用一种特殊的标记，并保持语法一致。如果您需要对 HTML 元素进行低级访问来控制行为，它们通常很有用。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

首先，通过运行以下命令创建一个新项目:

```
#create a new project vue init webpack customdirectives #change into the new project cd customdirectives #install npm packages npm install
```

接下来，打开我们的`src/main.js`文件，并在声明我们的应用程序之前添加以下代码:

在上面的指令中，我们已经监听了指令所附加的元素的`insert`钩子。我们修改元素的颜色和背景样式属性。接下来，我们定义了一个将字符串解码成`base64`编码的函数，然后我们将元素的 HTML 转换成函数的结果。

由于我们使用了`inserted`钩子，这看起来有些混乱，但是插入的钩子到底是从哪里来的呢？

根据 [Vue 文档](https://vuejs.org/v2/guide/custom-directive.html#Hook-Functions)，有所谓的“挂钩功能”，解释如下:

*   bind:仅在指令第一次绑定到元素时调用一次。这是您可以进行一次性设置工作的地方
*   inserted:当绑定元素已经插入其父节点时调用(这仅保证父节点存在，不一定在文档中)
*   update:在包含组件的 VNode 更新后调用，**，但可能在其子组件更新之前调用**。该指令的值可能已经更改，也可能没有更改，但是您可以通过比较绑定的当前值和旧值来跳过不必要的更新(参见下面的钩子参数)
*   componentUpdated:在包含组件的 VNode **及其子组件的 VNode**更新后调用
*   unbind:仅在指令与元素解除绑定时调用一次

基本上，我们所说的挂钩很大程度上取决于我们希望我们的行动发生的时间和方式。在这个指令中，我们希望它发生在元素被插入到它的父节点之后。

我们的`src/main.js`应该这样看:

新指令有效吗？让我们找出答案。

进入`src/components/Hello.vue`并在元素上使用`v-test`指令，这样我们的组件现在看起来像这样:

在上面的代码中，我们对模板中的第一个 H1 和 H2 元素应用了 v-test 指令。

如果我们运行当前的代码库，我们应该会看到下面的屏幕:

### 过滤

Vue 过滤器本质上是一个函数，它获取一个值，对其进行处理，然后返回处理后的值。

过滤器在两个地方有用:**胡子插值和** `**v-bind**` **表情。**过滤器应该附加到 JavaScript 表达式的末尾，用管道符号(|)表示。

我们想写一个过滤器，将我们输入的任何字符串的第一个单词都大写。例如，如果我们希望字符串`hi there`以大写字母 H 和 t 开头。

首先，通过运行以下命令创建一个新项目:

```
#create a new project vue init webpack customfilters #change into the new project cd customfilters #install npm packages npm install
```

接下来，让我们打开我们的`src/main.js` 文件，并在声明我们的应用程序之前添加以下代码:

```
Vue.filter('camel',function(str){
    return str.toLowerCase().replace(/^\w|\s\w/g, function (letter) {
    return letter.toUpperCase();
    })
})
```

在我们上面定义的 Vue 过滤器中，我们首先将整个字符串转换为小写，然后检查整个字符串的第一个字母，检查之前有空格字符的第一个字母，并应用`.` `toUpperCase()`方法替换它。

我们的`src/main.js` main 应该是这样的:

接下来，我们需要测试我们的过滤器是否正常工作。为此，打开`src/components/hello.vue`文件并替换为:

什么变了？

我们数据段中`msg`的值。此外，当显示模板中第一个`h1`的`msg`值时，我们应用了`camel`过滤器。

如果我们现在运行代码，我们应该会看到:

注意，我们的`msg`变量中每个单词的第一个字母都是大写的。

### 转换(进入/离开)

过渡是在 DOM 中插入、更新和删除元素时可以应用的效果。

在页面上实现过渡效果最简单的方法是通过 Vue 的`<transition>`组件。组件`<transition>`被 Vue 设为全局可接受，这意味着它可以在任何组件中使用和访问。

Vue 通过在转换过程中添加和删除元素上的类来工作。

处理转换时有六种不同的类前缀，它们是:

*   -enter:包含元素何时开始出现在场景中的样式
*   -leave:包含元素开始离开场景时的样式
*   -enter-active:过渡到位时的样式，例如过渡秒
*   -保持活动:当过渡不合适时的样式，例如过渡秒
*   -leave-to:它取代了-leave
*   -enter-to:这是 enter 的结束类。当-enter 被删除时，它被应用

为了很好地使用过渡，我们需要做的就是使用任何前缀给我们的过渡名称添加一个样式。例如，如果我们有一个名为`test`的转换，我们可以这样使用它们:

```
.test-enter-active {
    transition: transform 3s;
    text-shadow:0px 5px 10px #fdff00;
}
.test-leave-active {
    transition: transform 3s;
    text-shadow:0px 5px 10px #a1a194;
}
.test-enter, .test-leave-to {
    transform: translateX(90%);
}
.test-enter-to, .test-leave {
    transform: translateX(-15%);
}
```

首先，通过运行以下命令创建一个新项目:

```
#create a new project vue init webpack transitions #change into the new project cd transitions #install npm packages npm install
```

接下来，将我们的`src/components/hello.vue`替换为:

在上面的代码块中，我们用一个名为“test”的转换包装了第一个`h1`元素。我们还创建了一个新的输入元素，这是一个切换状态变量的按钮，强制我们的元素进入“离开”和“进入”状态。

在我们的脚本部分，我们添加了一个名为`status`的新变量，默认为 false。接下来，在我们的 mounted 调用中，我们将其设置为“true”。

如果我们运行我们的代码库，我们应该观察到它的工作方式如下图所示:

### 状态管理

当您的 Vue 应用程序变得越来越大，包含多个组件时，您可能会遇到这样的问题:如何在这些组件之间共享数据，并确保使用相同数据的组件在数据发生变化时总是得到更新。

由于分散在许多组件中的多种状态以及它们之间的交互，大型应用程序的复杂性通常会增加。为了解决这个问题，Vue 提供了 [**vuex**](https://github.com/vuejs/vuex)

**vuex 是什么？**

根据 Vuex 的官方文档，它是 VueJS 应用程序的状态管理模式和库。它充当应用程序中所有组件的集中存储，规则确保状态只能以可预测的方式发生变化。

Vuex 通过实施以下核心概念来使用集中式状态管理:

*   ***状态*** :包含数据的对象
*   ***Getters*** :用于从商店的状态树中访问数据
*   ***突变*** :对状态树中的数据进行修改的处理函数
*   ***动作*** :提交变异的函数。与*突变*的主要区别在于*动作*可以包含异步操作

让我们把这些理论付诸实践。

首先，让我们通过运行以下命令来创建一个新项目:

```
#create a new project vue init webpack statemanagement #change into the new project cd statemanagement #install npm packages npm install
```

接下来，我们将安装 Vuex 库:

```
npm install Vuex
```

一旦安装了 Vuex，让我们研究一下我们的示例应用程序的`state`、`actions`、`mutations`和`getters`。

当我们解释状态是什么时，我们说它是一个包含数据的对象。如果我们看上面的代码，我们可以看到它是一个包含用户数组的对象。

看看我们的行动:

```
actions: {
  ADD_USER: function({ commit }, new_user) {
    commit("ADD_USER_MUTATION", new_user);
  },
  DELETE_USER: function({ commit }, user_id) {
    commit("DELETE_USER_MUTATION", user_id);
  }
}
```

在我们的操作部分，我们定义了两个函数:

*   ADD_USER:这个函数进一步提交一个名为`ADD_USER_MUTATION`的变异，它将接收到的新用户对象作为一个参数，这将向列表中添加一个新用户。
*   DELETE_USER:这个函数进一步提交一个名为`DELETE_USER_MUTATION`的变异，它将收到的用户 id 作为参数，从列表中删除一个用户。

我们现在进入我们的突变:

```
mutations: {
  ADD_USER_MUTATION: function(state, new_user) {
    state.users.push(new_user);
  },
  DELETE_USER_MUTATION: function(state, user_id) {
    state.users.splice(user_id,1);
  }
}
```

在上面的代码中，我们有我们的突变，它直接改变了我们的存储状态。检查代码时，我们注意到两个函数:

*   ADD_USER_MUTATION:这种突变将新用户推送到我们的用户列表中
*   DELETE_USER_MUTATION:这种突变基于传递的 id 从用户数组中删除一个对象，id 是该对象的索引。

现在让我们看看我们的 getters:

```
getters: {
  users: state => {
    return state.users;
  }
}
```

在我们的`getters`块中，我们定义了一个函数:

*   users:这个 getter 返回我们商店中的所有用户

接下来，让我们将这些概念合并在一起，创建我们的 Vuex 商店。

在我们的`src`文件夹中创建一个名为`store.js`的新文件，放在:

这就是建立 Vuex 商店的简单之处。现在开始在我们的应用程序中使用这个商店。

为了通知 Vue 我们的中央商店，我们需要在创建时将商店对象传递给 Vue 实例。为此，将我们的`src/main.js`的内容替换为:

如果您查看上面的代码，您会注意到我们的 store 组件的导入，并将其传递给我们的 Vue 组件。

让我们更改`Hello`组件，这样我们就可以与我们的商店进行交互。

现在，将`src/components/hello`的内容替换为:

在上面的代码块中，您会注意到我们已经声明了一组 HTML 元素，在其中我们可以找到:

*   保存新用户名的文本输入。
*   保存新用户年龄的文本输入。
*   保存新用户状态的文本输入。
*   保存新用户创建日期的文本输入。
*   一个按钮元素，我们为它附加了一个函数，用来添加一个新用户。
*   标签，再加上 LI 标签，在删除按钮旁边显示用户。

我们现在已经完成了组件的 HTML 部分。

回顾我们的`methods`和`computed`属性很重要，这样我们可以看到我们如何与 Vuex 交互。

在我们的方法中，我们会注意到两个函数:

*   `add_user`:该方法将当前的`user`对象分派给名为`ADD_USER`的存储动作
*   `delete_user`:该方法接收一个参数，然后将该参数分派给名为`DELETE_USER`的存储动作

转到我们的计算属性，我们只有一个名为`users`的函数。该函数返回我们商店中的所有用户。

如果我们运行我们的代码库，它应该如下所示:

### 服务器端渲染

Vue 作为 JavaScript 框架的一个缺点是，在浏览器执行了应用程序的 JavaScript 包之前，页面是不可见的。

这导致应用程序呈现空白页面，在一些应用程序中，长时间显示预加载程序。Vue 团队试图解决这个问题，并提出了名为 SSR 的服务器端渲染。幸运的是，这里有关于如何在 Vue 中实现服务器端渲染的详细信息。

### 结论

在本教程中，我们介绍了许多使用 Vue 的高级技术。我们还学习了如何创建和使用 mixins 来停止代码重复。最后，我们学习了自定义指令、过滤器、转换、状态管理和服务器端呈现。

我希望无论你在这里学到了什么，都将有助于编写更高效的 Vue 应用程序。

本教程的代码库可在这个[公共 GitHub repo](https://github.com/samuelayo/advanced_vue_concepts) 中获得。请下载用于教育目的。

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。