# 使用 Vue Formulate 更简单、更强大的 Vue 表单

> 原文：<https://blog.logrocket.com/easier-more-robust-vue-forms-with-vue-formulate/>

## 造型是乏味的

举手表决:当你在构建一个 web 应用程序时，你最喜欢这个过程的哪一部分？你是说“实现表单”吗？从来没有人知道。

形式是必要的，但也是痛苦的。用户体验期望很高，但是由于我们总是从原始的 HTML 元素开始(即使在使用 React 或 Vue 时)，构建高质量的表单需要很多努力。不一致的输入标签、糟糕的前端验证、可访问性需求和大量乏味的标记损害了开发人员的体验，这只是列举了一些令人沮丧的地方。

如果我们将所有输入的 API 标准化，使得每种输入类型都是可预测的，甚至是可互换的，会怎么样？如果我们能够使用一种表达性的语法来执行复杂的前端验证，会怎么样？如果我们对`v-model`充电，使它可以应用*一次*到整个`form`元素，从表单数据生成单个对象，会怎么样？

如果我们向 Vue 学习——构建一个不限制高级用户的易于理解的 API 并把精力集中在重塑表单创作体验上会怎么样？开发人员会非常高兴，我们都会节省大量的时间。这些生活质量的改善，以及许多其他的改善，都可以通过 [Vue Formulate](https://www.vueformulate.com) 实现。让我们来看看。

## 简单的例子

让我们从一个简单的例子开始，用我们熟悉的传统 Vue 方式做事。下面是一个(假)会议的模拟用户注册表单。在本例中，我们需要用户名、电子邮件、简短简历、密码和密码确认。

请参见 [CodePen](https://codepen.io) 上 Andrew Boyd ( [@boyd](https://codepen.io/boyd) )
的 Pen [1–Vue 配方介绍–香草登记表](https://codepen.io/boyd/pen/pojxJGE)。

这是一个简单的表单，但是在创建它的时候，我们已经遇到了很多令人头疼的问题:

*   用于输入的 API 不是 100%一致的，并且`textarea`是它自己的独立标签，不同于其他任何东西
*   每个输入都需要一个可访问性标签，有些输入需要帮助文本来促进良好的 UX。这导致了大量冗长的标记
*   我们能够对必填字段、用户名格式、电子邮件地址格式和密码长度进行基本的(也是丑陋的)验证，但是如果我们想要检查密码确认(我们确实这样做了)，我们将需要滚动我们自己的验证
*   如果我们想在用户提交表单之前为用户提供友好的检查，看看他们选择的用户名是否可用，我们只能靠自己了

让我们添加密码确认验证，并从上面的列表中剔除用户名可用性检查。毕竟，我们的用户会期待好的 UX 行为，没有它们我们不会发布这个表单——对吗？

我们将再次使用我们已经熟悉的普通 Vue 方法。再做一点工作后，我们得到了类似这样的结果:

请参见 [CodePen](https://codepen.io) 上的 Pen [2–Vue formula 简介–Vanilla 注册表单，带有 Andrew Boyd (](https://codepen.io/boyd/pen/oNjajER) [@boyd](https://codepen.io/boyd) )
的自定义验证。

表单上的用户体验更好，但是我们的简单例子开始看起来…复杂了。注意添加了几个`ref`属性来帮助我们跟踪组件中的输入值，以及用于触发我们自己的定制验证方法的`keyup`和`blur`事件处理程序。这些方法工作得很好，但是在用户名检查的情况下，由于可用性检查的异步性质，可能会很快提交带有无效用户名的表单。

我们的组件现在包含了所有这些复杂性——在公认的不完美状态下——而且这是针对只有五个输入的表单！最重要的是，我们在组件中编写的所有逻辑都绑定到这种特定的形式，不容易重用。重用将需要我们做额外的工作，将我们的验证功能抽象到项目中某个地方的实用程序库中，或者更糟的是，只要我们创作另一个需要类似行为的表单，就复制/粘贴并修改它。

我们应该做得更好。我们可以做得更好。让我们看看用 Vue Formulate 编写的相同表单。

见笔 [3–Vue 公式化介绍–用 Vue 公式化](https://codepen.io/boyd/pen/PoPyZmJ)重写登记表由安德鲁博伊德([@博伊德](https://codepen.io/boyd) )
在 [CodePen](https://codepen.io) 。

啊！好多了。我们手工开发的所有相同的功能(以及更多)，模板更干净(我们组件的模板代码被削减了一半)，我们的组件包含更少的一次性逻辑。在上面的演示中，有一些关键事项需要解决:

*   我们只需要担心两个不同的组件:`FormulateForm`和`FormulateInput`。就是这样！
*   输入的 API 都是内部一致的
*   对于用户名、电子邮件和密码字段，我们能够使用表达性的[内联验证规则](https://vueformulate.com/guide/validation/#available-rules)来执行复杂的验证逻辑，而无需向组件添加任何一次性方法或计算属性
*   我们应用的验证规则输出了友好的内联错误消息，无需我们进行任何手动配置
*   通过在验证规则上使用前缀`^`,我们可以告诉 Vue Formulate 在特定规则失败时“放弃”验证，限制一次显示的错误数量
*   对于用户名检查，我们能够声明我们自己的[自定义验证规则](https://vueformulate.com/guide/validation/#custom-validation-rules)，它对已知的用户名执行异步检查(在本例中用本地数据道具模拟)。我们还能够提供一个定制的验证消息，如果规则失败，将会显示该消息

不仅代码简洁易懂，Vue Formulate 还免费为我们提供了一些很棒的 UX 功能。验证规则产生格式良好的错误消息，表单本身不会提交，直到所有验证规则(包括异步规则)都通过，标签和帮助文本作为道具存在于`FormulateInput`上，允许用户期望的相同的改进 UX，而不需要在模板中编写臃肿的标记。

所有这些，我们只是触及了 Vue Formulate 功能的表面。

## 让我们做一些复杂的事情

我们将为一个(假)会议(FormulateConf 2020)构建一个多参与者购买表单，并利用它来展示 Vue Formulate 的一些更强大的功能。

首先，我们将为单个与会者搭建表单的功能，输入姓名、电子邮件、机票等级和首选支付方式。我们将添加 Vue 公式验证、标签和帮助文本，因为我们现在对它们已经很熟悉了。

参见 [CodePen](https://codepen.io) 上 Andrew Boyd ( [@boyd](https://codepen.io/boyd) )
的 Pen [4–Vue formula 简介–单会员预订表](https://codepen.io/boyd/pen/wvKYqLE)。

这太棒了！如果我们一次只需要一个用户的表单，我们就可以开始了。相反，我们希望允许一个用户(例如，一个办公室经理)为多个与会者预订门票，并作为一次付款提交表单。这是 Vue Formulate 的可重复分组字段的完美用例。

让我们做最少量的重构来利用可重复组。

请参见 [CodePen](https://codepen.io) 上 Andrew Boyd ( [@boyd](https://codepen.io/boyd) )
的 Pen [5–Vue formula 简介–多会员预订表格](https://codepen.io/boyd/pen/dyYgVEq)。

万岁。通过将用户详细信息字段包装在类型为`group`的`FormulateInput`中，并将`repeatable`属性设置为`true`，我们能够实现开箱即用的可重复字段。那太容易了！我们还为组添加了一个`v-model`属性，并修改了我们的`total` computed 属性，以汇总表单数据中表示的一个或多个票证。很整洁，是吧？

在 Vue Formulate 中，`v-model`是强大的，并且完全按照你希望的方式工作。您不仅可以为单个输入或分组字段建模。让我们在根`FormulateForm`元素本身上加一个`v-model`。我们将把它的值输出到页面上，这样我们就可以在与表单交互时看到 Vue Formulate 提供的数据结构。

参见 [CodePen](https://codepen.io) 上 Andrew Boyd ( [@boyd](https://codepen.io/boyd) )
)的 Pen [6–Vue Formulate 简介–v-model 输出](https://codepen.io/boyd/pen/oNjaoLQ)的多会员预订表格。

就这样！虽然我们创建的功能很复杂，但留给我们的是一个易于阅读的组件，并且只需要最少的自定义逻辑来增强其体验。

## 哦，你要做的事情！

Vue Formulate 极大地降低了使用 Vue 编写表单的复杂性。我们已经介绍了很多内容，但是还有很多内容需要探索。使用 Vue Formulate，您还可以:

*   轻松处理和填充从后端 API 收到的表单错误
*   从单个对象重新填充表单，这对于设置可编辑用户配置文件等内容的初始状态非常有用
*   用完整的作用域插槽支持覆盖任何默认的 Vue 公式组件模板
*   由于单个`FormulateInput`元素上的标准化 API，从 JSON 对象生成表单

## 结论

Vue Formulate 是——也将永远是——免费和开源的。我们的[全面的开发者文档](https://www.vueformulate.com/guide)将帮助你将 Vue Formulate 集成到你的下一个项目中。额外收获:因为它是< 15KB 的压缩文件，你甚至不需要为此感到内疚。将 Vue Formulate 添加到您的项目中，去构建一些令人敬畏的东西吧！

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。