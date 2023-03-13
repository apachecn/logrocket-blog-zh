# 在 Figma 中使用组件属性

> 原文：<https://blog.logrocket.com/ux-design/using-component-properties-figma/>

当 Figma 在 Config 2022 期间宣布元件特性时，设计界理所当然地感到兴奋。

组件属性被承诺为创建一组可应用于任何对象的可定制属性的强大方法，被吹捧为提高生产率、解决围绕变体复制的问题以及使团队之间的交流更简单的方法。怎么会？通过使设计系统更加简化，减少创建多个变体以覆盖各种场景的需要，并在组件属性侧栏中显示选项，帮助团队更好地了解他们可以(和不可以)对任何给定的组件进行哪些更改。

从我个人的经验来看，在团队中，围绕组件变更的一般模糊性和可忽略的问题数量已经大大减少了。

简而言之，组件属性允许您设置颜色和字体等属性。某些组件的布局仍然由变体来处理，变体可以演示一个组件应该如何寻找不同的上下文，例如，移动与桌面。

*注意，作为提醒，Figma 中的变体并不新鲜。因为它们被认为是 Figma 中组件属性特性集的一部分，所以在本文中我将把它们当作组件属性特性集来处理。*

从根本上说，组件属性使调整对象的外观和感觉变得容易，而不必进行重大的设计更改。总而言之，它们是给你和你的团队更多控制权的一个超级强大的方法，特别是如果你碰巧正在维护或创建一个设计系统。

![Variants Only vs Component Properties](img/3b5f0f426ae743838c2a704b724ebb09.png)

In this example, we can see the benefits of using variants only versus using variants with component properties. This is a simple example, but we can see a reduction of 50 percent of components needed.

从个人经验来看，我发现组件属性对于工程团队的日常工作来说非常棒。能够使用具有组件属性的组件快速传达想法和构建原型，为我们的工作流创造了奇迹。它们还为设计团队腾出了时间，让他们能够专注于发现和为今年即将推出的功能提供更多概念性想法。

我很高兴与您分享组件属性。准备好了吗？首先，介绍创建一个组件需要做些什么，最后，让组件属性启动并运行。

## 主要成分

可以从层、组和框架创建组件。

要创建组件，您必须选择这些层、组或帧，然后从工具栏中选择**创建组件**，或者按 windows:**ctrl+alt+k**/MAC:**⌥选项+ ⌘命令+ K** 。您也可以右键单击您的层、组或框架，并从上下文菜单中选择**创建组件**。

![Create Component Dropdown](img/e7db8172fb80a1fee4fc82882db5e836.png)

There are usually multiple ways of doing things in Figma, and creating a component is no exception. By right-clicking on an element, we can create a component via the context menu.

将主要部分视为所有未来实例的真相来源。它是实例存在之前必须存在的基础。简单来说就是复制粘贴。如果没有要复制的内容，就不能粘贴。实例只是主组件的副本。

一个实例可以有一个或多个变体，但是主组件总是首先创建，其他的都是它的一个实例(副本)。如果主组件以任何方式更改，所有实例都会立即更新以反映主组件的更改。

![Three Properties Combined](img/84539243663a62a1281ef8535b58e8ae.png)

An example of three variants reduced to just one by using the Boolean, text, and instance swap properties. The end result is a robust, flexible component.

但是，如果编辑主构件的实例，则只有该实例会受到影响，因为每个实例都被认为是唯一的。

![Main Component and Instance](img/2e0512f4841583786f822d4cd50b4e61.png)

A look at both the main component and an instance created from that main component, in the layers panel.

创建了主组件后，让我们更深入地研究每个组件的属性，并探索它们如何使设计者受益。

### 不同的

变体是不同大小、颜色、样式和状态(即交互)的理想选择。

我们知道变体不是新的，但是在组件属性的上下文中讨论它们是值得的——特别是 Figma 根据他们自己的文档认为变体是组件属性[的一部分。当一个变体被创建时，它存在于所谓的组件集中。组件集是一种通过将相关组件组合在一起来识别它们的方法。简单地说，按钮、表格甚至表格单元格可以存在于它们自己的组件集中。将这些作为相关的实体组合在一起是合乎逻辑的，尤其是对于 Figma 项目来说是新的。](https://help.figma.com/hc/en-us/articles/8883756012823-Create-and-manage-component-properties)

变体的主要好处是，它们表示和携带由变体属性定义的特定属性，如大小、布局变化或状态变化。需要设计一个有悬停状态的按钮？让我们看看下面的一个例子。

![Three Button Variant](img/f6d42ff58f660a2f69fc3c114f117c3b.png)

Three-button variants are contained within a component set. Notice the purple dotted border to denote these components belong to the same group.

假设您想设计一个具有不同状态的按钮，如启用、悬停和禁用。变体向您的直接团队或利益相关者展示了所有不同的排列看起来会是什么样子。每一种可能性都可以优雅地设计出来，并打包成一个内部设计系统。或者，您可能正在创建一系列信息卡，并希望展示它们在移动设备、平板电脑和桌面上的外观？变体已经覆盖了你。此外，如果你想给你的 Figma 设计增加一定程度的互动性，那么变体是必须的。

交互性对于向利益相关者展示你的工作是极好的。再次使用一个简单按钮的例子，我们可以演示一旦开发团队实现，悬停和默认状态之间的切换将会是什么样子。虽然这是一个简单的例子，但是您可以想象这对其他组件的影响。复选框、下拉框、底部表单……不胜枚举！

![Interactive Details Menu](img/7daaf1896cbda4599656b74e5379505d.png)

Want to add some interactivity to your designs? Then you’ll need to use variants to do that.

你可以在图中查看这个例子。

以下是如何使用变体。首先，您需要选择您的主元素，并将其转换成一个**组件**。我们之前提到过。完成后，选择您想要从中创建变体的**组件**。在下面的示例中，我选择了一张信息卡。

当我们从工具栏中选择 **Add variant** 时，主组件将被复制——本质上是创建一个实例。值得注意的是，每个实例都将被创建并存在于一个预定义的组件集中。

从下面的例子可以看出，左边的卡片是移动设计的主要组成部分，右边的卡片是它的变体。我创建的那个变体已经被我调整过了，我已经创建了一个现在更适合桌面的卡。

![Mobile and Desktop Instances](img/0238632201c711bddf7cabcba76fb4c7.png)

By using the variants, we can create multiple instances to reflect different platforms like mobile or desktop.

> 注意，即使组件属性的目的是减少创建的变体的数量，在特定情况下仍然需要变体。如果你有一个主要的设计变化，如尺寸或颜色，那么变体仍然占主导地位。交流交互，比如悬停状态或表单交互，只能使用变体来完成

请记住，变体非常适合创建具有多种状态的元素。例如，能够创建一个主按钮或信息卡，针对不同的上下文有多种变化，这对于独立设计师和大型设计团队来说是一个巨大的时间节省。

### 布尔代数学体系的

布尔值是能够在项目之间切换可见性的理想选择。

布尔是一个术语，意思是某个东西不是开着就是关着。你家的灯开关是布尔控制的一个很好的例子。灯是亮着还是关着，取决于开关的位置。

虽然 Boolean 在实践中是一个简单的概念，但我认为这个属性是所有组件属性中最有用的一个。通过使用 Figma 中的布尔属性，设计人员可以显示或隐藏组件中的某些元素，并且该选项可以方便地显示在设计面板中。

让我们看一个你可能很熟悉的例子。想象一下创建一个包含图标的按钮。如果您希望图标出现在按钮内部，应该将 Boolean 属性切换到 on。如果不想显示图标，应该关闭布尔属性。

![Instance Swap](img/c91d8013a45a98344bab83f4504e629d.png)

The Boolean property has already been applied to the above, meaning we can toggle the icon on or off.

简单吧？让我们更详细地看看如何正确地实现布尔属性。

选择您的**组件**。在本例中，我选择了一个包含图标的按钮。在侧边栏的图层面板中选择**组件属性**按钮。让我们将属性命名为“has icon”，并将值设置为 **True** 。本例中的 True 表示打开，意味着图标默认可见。

![Create Component Property](img/acd88429b7fab35536a926fdeef0926e.png)

Creating the Boolean component property for one of our buttons.

选择**创建属性**。恭喜您，“has icon”现在将作为切换开关显示在 Figma 右侧的按钮面板中。现在可以切换此元素的可见性。

![Toggle Switch](img/a0fd1453da45992c68482437fec912e2.png)

A toggle switch has now been created, allowing users to toggle the icon on or off.

现在，让我们创建一个属性来隐藏和显示文本！

为此，再次选择**组件属性**按钮——但是这次选择**创建属性**。我们将其命名为“有文本”，并将值设置为 **True** 。这意味着默认值将显示文本。如果我们现在回到属性面板，我们可以在一个地方找到所有的控件属性。

控制画布上某些元素的可见性是 Figma 中一个非常强大的特性，也是我合作过的所有团队中使用最多的组件属性之一。

### 实例交换

实例交换是快速定制实例的理想方式。

![Instance Swap Local Components](img/b9bbce49e96b5cc0fade218e502f73d3.png)

An example of a component that’s been set up with an instance swap — notice on the right-hand side that we have four different icons we can now change without digging into the layers panel.

实例交换属性是一个组件属性，它允许我们直接从属性面板的下拉列表中交换组件。通过这样做，您不需要选择组件本身中的层来交换它。这是一个很好的生活质量更新，使 Figma 中的 UI 设计更加简化。

当在按钮、提醒或导航条等组件内部交换图标时，我通常使用实例交换。在这些环境中拥有一组定义好的实例交换对于大型团队来说也是非常好的。随着多个设计者在设计中跳来跳去，已经定义的实例交换与团队交流，“这些图标已经被签署，可以在这个上下文中使用。”

选择希望实例交换发生的组件后，选择**应用实例交换属性**图标。给实例 swap 一个名称，并选择**选择实例**作为值。

此时，您将能够创建所谓的“首选值列表”这是一种设置默认图标选择的方法，您或您的团队已经决定了这些图标的优先级。实例交换菜单现在会有一个叫做**图标**的紫色药丸。现在，创建主组件的一个实例。回到侧边栏，您会注意到我们现在可以使用组件属性控件通过下拉菜单选择不同的图标。

![Selecting Component to Swap](img/c0b2c36c3b9c200ea26f2217bb8923fe.png)

Figma 中实例交换属性的典型用例是交换设计库中的元素。

例如，假设您有一个不同样式的按钮库，但您想快速更改其中一个按钮的样式。使用“实例交换”属性，您可以快速将按钮与库中的另一个按钮进行交换，而不必手动删除和替换现有按钮。这可以节省时间，并有助于确保整个设计的一致性。

### 快速更改文本内容

![Instance Swap Local Components](img/b9bbce49e96b5cc0fade218e502f73d3.png)

The above shows the button selected in the layers list, the outcome of creating a text property, and on the right what that looks like in the property panel.

文本属性是所有组件属性中最简单的。启用后，您现在可以选择直接在属性面板中更新文本层，而不必单击多个层来最终找到您想要更新的文本。改变多个文本实例现在也是可能的。

![Text Layers](img/d1aca7bbabb36fa048b55af9d4f3c2c6.png)

After creating an initial main component, and creating a text property, we can now create instances from that main component. If we select multiple instances, the text property shows in the design panel.

另一个好处是创建一个命名的值，它反映了工程团队可能更熟悉的东西。例如，将按钮命名为“Label”，并赋予其值“Click me”

尽管文本组件属性可能很简单，但它有很多用例。示例包括向仪表板添加标题或描述、标记应用程序的各个部分或为地图提供说明。只要有文本，就有机会使用文本组件属性。

然而，团队应该自行决定是否使用文本组件——在设计面板中拥有大量可编辑的文本字段可能会变得难以使用，并且会对团队的生活变得更加轻松产生相反的效果！

## 结论

如果您是 Figma 组件属性的新手，或者是经验丰富的专业人士，您可能会同意了解这些属性的最佳方式是亲自尝试。值得庆幸的是，设计社区已经创建了一些惊人的资源来帮助您开始并看到组件属性的作用。

我个人最喜欢的是 Figma 的[组件属性游乐场。](https://www.figma.com/community/file/1100581138025393004)[Vic](https://www.figma.com/community/file/1113141767963760931)的组件属性(案例研究)也是 Figma 中的一个极好的资源，解释了在微软的一个大型设计团队中使用组件属性的复杂性。

我喜欢 Figma 的是实验。打开一个新的画布来探索想法，创建新的按钮和元素，然后使用组件属性来增加一定程度的灵活性，这在此次更新之前是不可能做到的。这很有趣，令人兴奋，最重要的是，这是一个成为设计师的绝佳时机！

## [LogRocket](https://lp.logrocket.com/blg/signup) :无需采访即可获得 UX 洞察的分析

[![](img/1af2ef21ae5da387d71d92a7a09c08e8.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 让您可以回放用户的产品体验，以可视化竞争，了解影响采用的问题，并结合定性和定量数据，以便您可以创建令人惊叹的数字体验。

查看设计选择、交互和问题如何影响您的用户— [立即尝试 LogRocket】。](hhttps://lp.logrocket.com/blg/signup)