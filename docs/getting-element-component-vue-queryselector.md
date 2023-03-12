# 使用 Vue querySelector 获取组件中的元素

> 原文：<https://blog.logrocket.com/getting-element-component-vue-queryselector/>

Vue 是一个用于构建动态用户界面的渐进式 JavaScript 框架。Vue 的设计逐渐灵活，侧重于声明性渲染和组件组合。核心 Vue 库只关注 MVC 模式的视图层，然而，它有一个庞大的支持库的生态系统，使得创建响应迅速的 web 体验变得简单。

使用 Vue 中的 [`querySelector`](https://developer.mozilla.org/en-US/docs/Web/API/Document/querySelector) ，我们可以访问和改变第一个选中元素的属性，在这个过程中创建动态的交互式网站。我们可以出于各种原因使用`querySelector` ,包括但不限于下列原因:

*   添加或移除选定元素的 CSS 样式
*   检索或设置选定输入元素的值
*   获取或设置选定元素的属性

`querySelector`在 Vue 代码内部实现简单，因此熟悉 Vue 的所有级别的开发人员都可以使用。在本教程中，我们将演示如何在 Vue 中使用`querySelector`来获取组件中的元素，按照一步一步的说明来访问或更改这个元素属性。

### 先决条件

要跟随本教程，您需要以下内容:

*   已安装 Node.js ≥v10
*   npm v≥6.7 已安装
*   熟悉 Vue

注意，您可以通过在终端或命令提示符下运行`node -v`来验证 Node.js 版本。我们开始吧！

## 安装 Vue

有几种方法可以安装 Vue，但是，安装 Vue CLI 是最简单的。一般来说，处理单个文件组件和 Vue API 更容易。您可以通过运行以下命令来全局安装 Vue CLI:

```
npm install -g @vue/cli  

```

安装完成后，您可以使用以下命令创建一个新项目:

```
vue create my-project

```

### Vue 组件

组件允许我们创建自定义元素，我们可以在应用程序中任意多次重用这些元素。我们可以在另一个组件中使用一个组件，使我们的应用程序是动态的。

## `querySelector`在望

我们可以使用`querySelector`来选择一个 HTML 元素，该元素返回文档中匹配指定选择器的第一个元素。它可用于设置、获取、修改或删除 Vue 中所选元素的属性。

在 Vue 中查询一个元素的对象模型和 JavaScript 的`document.querySelector()`很像。虽然我们也可以在 Vue 中使用`document.querySelector()`，但是 Vue 组件被设计成可重用和动态的。因此，我们不能保证带有自定义`className`的元素是唯一的。

`querySelector()`函数接受一个用于获取指定元素的参数。该参数可以是 HTML 元素的标记名、类名或 ID。`querySelector()`函数的语法如下:

```
this.$el.querySelector("input").value = "Enter name" 

```

注意`this.`只能在 Vue 组件内部使用。

## `this.$el.querySelector`在望

Vue 中的`$el`选项为 Vue 提供了一个现有的 HTML 元素来挂载使用 new 关键字生成的 Vue 实例。`this.$el.querySelector`用于访问 HTML 元素并修改元素的属性。

`this.$el.querySelector`在 Vue 组件中遵循以下语法:

```
this.$el.querySelector(parameter).value;

```

与`this.$refs`不同的是，`this.$el.querySelector()`不需要你添加`ref`属性到你的 HTML 标签元素来访问元素。您可以使用元素的标记名、类名或 ID 直接访问元素。

我们将在组件内部使用`this.$el.querySelector`来访问 HTML 元素，并更改元素的一些属性。

### 通过标签获取元素

让我们选择一个组件中的 HTML 元素标记，并将元素的文本更改为“您单击了按钮”。我们将通过使用标记名来访问元素，将其文本颜色属性设置为粉红色:

```
<div id="app" >
    <span  >{{ message }}</span>
    <button @click="myFunctionClick()">Click Here</button>
</div>

</body>

<script type="text/javascript">
 new Vue({
    el: '#app',
    data: { 
          message:"You clicked me"
      },
      methods:{
        myFunctionClick: function () {        
            this.$el.querySelector("span").innerText = ' you clicked the button';
            this.$el.querySelector("span").style.color = 'pink';
        }
    }
});
</script>

```

当您单击按钮时，span 元素的文本颜色应该变成粉红色。

### 通过 ID 获取元素

通过将 ID 名称传递给`querySelector`方法，可以通过 ID 选择元素。为了演示，让我们创建一个隐藏文本页面，带有一个名为**的按钮来显示隐藏文本。**点击按钮时，将显示文本，并将按钮设置为`hidden`。我们将使用下面的代码:

```
<div id="app" >
    <span hidden id="hide" ></span>
    <button id="bn" @click="myFunctionClick()">show hidden text</button>
</div>

</body>

<script type="text/javascript">
 new Vue({
    el: '#app',
    data: { 
          message:"You clicked me"
      },
      methods:{
        myFunctionClick: function () {        
            this.$el.querySelector("#bn").style.display = 'none';
            var hide = this.$el.querySelector("#hide");
             hide.innerText = 'this is the hidden text show time';
             hide.style.display = 'block';
             hide.style.color = 'pink';
        }
    }
});

</script>

```

当您想要在所选元素上应用多个属性时，您可以将`this.$el.querySelector()`存储在一个变量中。

### 按类获取元素

我们还可以使用`querySelector`通过类名获得元素，就像我们对标签和 id 所做的那样。让我们通过创建一个允许用户从 input 元素插入 URL 的简单应用程序来进行演示。图像将显示在一个`img` HTML 元素中:

```
<body>

<div id="app" >
    <img src="" class="image">
    <input type="url" class="urlinput">
    <button id="bn" @click="myFunctionClick()">Load image</button>
</div>

</body>

<script type="text/javascript">
 new Vue({
    el: '#app',
    data: { 
          Message:"load image"
      },
      methods:{
        myFunctionClick: function () {        
            this.$el.querySelector(".image").src = this.$el.querySelector(".urlinput").value;

        }
    }
});
</script>

```

在上面的代码中，`this.$el.querySelector(".image").src`属性用于更改图像 src (URL)。从指定输入元素获得的值被分配给所选的`img`元素。

## 包扎

当使用`querySelector()`功能选择一个元素时，可以获得或改变所选元素的属性。有许多属性`querySelector()`支持，比如输入元素的自动聚焦，使用`innerHTML`访问元素的文本，元素样式等等。

例如，我们将使用前面代码中的以下代码:

```
this.$el.querySelector(".image").src ="logo.jpg"

```

让我们将所选图像类名的图像 src 改为`logo.jpg`:

```
this.$el.querySelector(".image").src

```

上面的代码用于获取所选图像 ID 的属性(URL)值，我们可以将它存储在一个变量中。让我们使用`querySelector`在`input`元素上设置自动对焦:

```
this.$el.querySelector("input").focus

```

如果有两个或更多的元素具有相同的标记名、类名或 ID，并且使用`querySelector`来访问元素，那么只有第一个元素是可访问的。让我们使用以下示例来演示这一点:

```
<body>

<div id="app" >
    <img src="" class="image">
    <span>first element</span><br>  
    <span>second element</span> <br>  
    <button id="bn" @click="myFunctionClick()">change background color</button>
</div>

&lt;/body>

<script type="text/javascript">
 new Vue({
    el: '#app',
    data: { 
          message:"You clicked me"
      },
      methods:{
        myFunctionClick: function () {        
            this.$el.querySelector("span").style.backgroundColor ="red";

        }
    }
});
</script>

```

我们可以从上面的代码中看到，当单击按钮时，只有第一个 span 元素的背景色变为红色。

## 结论

在本教程中，我们学习了如何使用`querySelector`访问 Vue 应用程序中组件内部的元素。我们还可以使用标记名、类名或 ID 来访问每个元素。现在，你应该能够让你的网页更加动态和互动。我希望你喜欢这个教程。

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。