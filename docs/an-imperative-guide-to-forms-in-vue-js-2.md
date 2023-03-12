# Vue.js 中表单的完整指南

> 原文：<https://blog.logrocket.com/an-imperative-guide-to-forms-in-vue-js-2/>

***编者按:*** *这本 Vue.js 中的表格指南更新于 2021 年 1 月 19 日。*

学习在我们最喜欢的框架中正确使用表单是有价值的，它可以在开发过程中节省我们一些时间和精力。在本教程中，我将带您浏览在 [Vue.js](https://vuejs.org/) v2.x 应用程序中创建、验证和利用表单输入的过程。

要跟随本教程，你需要一些 HTML 和 Vue.js 的知识。你可以在 [CodePen](https://codepen.io/olayinkaos/pen/GMmpPm) 上体验整个演示。

> 没有描述

设置我们的 Vue.js 应用程序

## 我们将首先创建一个简单的 Vue.js 应用程序，其中包含一些基本的 HTML 标记。我们还将引入[布尔玛](http://bulma.io/)来利用一些预制的风格:

将输入值与`v-model`绑定

```
<!DOCTYPE html>
<html>
<head>
  <title>Fun with Forms in Vue.js</title>
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/bulma/0.4.4/css/bulma.min.css">
</head>

<body>
  <div class="columns" id="app">
    <div class="column is-two-thirds">
      <section class="section">
        <h1 class="title">Fun with Forms in Vue 2.0</h1>
        <p class="subtitle">
          Learn how to work with forms, including <strong>validation</strong>!
        </p>
        <hr>      

        <!-- form starts here -->
        <section class="form">

</section>
      </section>
    </div>
  </div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/vue/2.3.4/vue.min.js"></script>

<script>
  new Vue({
    el: '#app'
  })
</script>

</body>
</html>
```

## 我们可以使用`v-model`指令将表单输入和`textarea`元素值绑定到 Vue 实例数据。根据 [Vue 文档](https://vuejs.org/v2/guide/forms.html#:~:text=You%20can%20use%20the%20v,based%20on%20the%20input%20type.),`v-model`指令使您能够在表单输入、`textarea`和选择元素上创建双向数据绑定。它会根据输入类型自动选择正确的方式来更新元素。

文本输入示例

### 让我们从创建一个简单的文本输入来获取用户的全名开始:

在上面的代码中，我们在 Vue 实例中定义了 data 选项，并定义了一个 form 对象，它将保存我们的表单所需的所有属性。我们定义的第一个属性是`name`，它绑定到我们也创建的文本输入。

```
...
<section class="form">
  <div class="field">
    <label class="label">Name</label>
    <div class="control">
      <input v-model="form.name" class="input" type="text" placeholder="Text input">
    </div>
  </div>
</section>
...

<script>
new Vue({
  el: '#app',
  data: {
    form : {
      name: ''
    }
  }
})
</script>
```

既然双向绑定已经存在，我们可以在应用程序的任何地方使用`form.name`的值，因为它将是文本输入的更新值。我们可以添加一个部分来查看表单对象的所有属性:

如果你按照正确的方式进行，你应该会得到和下面的小提琴一样的结果。尝试在输入框中键入:

```
...
<div class="columns" id="app">

<div class="column">
    <section class="section" id="results">
      <div class="box">
        <ul>          

          <li v-for="(item, k) in form">
            <strong>{{ k }}:</strong> {{ item }}
          </li>
        </ul>
      </div>
    </section>
  </div>
</div>
...
```

If you’re following along properly, you should have the same result as the fiddle below. Try typing in the input box:

注意`v-model`将忽略表单输入的值、检查或选择的属性，并将 Vue 实例数据视为事实的来源。这意味着您还可以在 Vue 实例中为`form.name`指定一个默认值。这就是表单输入的初始值。

`Textarea`举例

### 这些功能与常规输入框的工作方式相同:

以及表单模型中的相应值:

```
...
<div class="field">
  <label class="label">Message</label>
  <div class="control">
    <textarea class="textarea" placeholder="Message" v-model="form.message"></textarea>
  </div>
</div>
...
```

需要注意的是，`textarea` — `<textarea>{{ form.message}}</textarea>` —中的插值不适用于双向绑定。请改用`v-model`指令。

```
data: {
  form : {
    name: '',  
    message: '' 
  }
}
```

对选择框使用`v-model`指令

### 对于选择框，也可以很容易地插入`v-model`指令。定义的模型将与所选选项的值同步:

在上面的代码中，我们选择使用`v-for`指令动态加载选项。这要求我们也定义 Vue 实例中的可用选项:

```
...
<div class="field">
  <label class="label">Inquiry Type</label>
  <div class="control">
    <div class="select">
      <select v-model="form.inquiry_type">
        <option disabled value="">Nothing selected</option>
        <option v-for="option in options.inquiry" v-bind:value="option.value">
          {{ option.text }}
        </option>
      </select>
    </div>
  </div>
</div>
...
```

多选框的过程类似。不同之处在于，多选框的选定值存储在一个数组中。

```
data: {
  form : {
    name: '',
    message: '',
    inquiry_type: '' 
  },
  options: {
    inquiry: [
      { value: 'feature', text: "Feature Request"},
      { value: 'bug', text: "Bug Report"},
      { value: 'support', text: "Support"}
    ]
  }
}
```

例如:

并定义相应的模型属性:

```
...
<div class="field">
  <label class="label">LogRocket Usecases</label>
  <div class="control">
    <div class="select is-multiple">
      <select multiple v-model="form.logrocket_usecases">
        <option>Debugging</option>
        <option>Fixing Errors</option>
        <option>User support</option>
      </select>
    </div>
  </div>
</div>
...
```

在上面的例子中，选择的值将被添加到`logrocket_usecases`数组中。

复选框示例

```
data: {
  form : {
    name: '',
    message: '',
    inquiry_type: '',
    logrocket_usecases: [], 
  }, 

}
```

需要布尔值(真/假)的单个复选框可以这样实现:

### 定义相应的模型属性:

上例中`form.terms`的值将根据复选框是否被选中而为真或假。该属性的默认值为 false，因此复选框的初始状态将被取消选中。

```
...
<div class="field">
  <div class="control">
    <label class="checkbox">
      <input type="checkbox" v-model="form.terms">
      I agree to the <a href="#">terms and conditions</a>
    </label>
  </div>
</div>
...
```

对于多个复选框，它们可以简单地绑定到同一个数组:

```
>data: {
  form : {
    name: '',
    message: '',
    inquiry_type: '',
    logrocket_usecases: [],
    terms: false, 
  }, 

}
```

单选按钮示例

对于单选按钮，`model`属性取选中的`radio`按钮的值。

```
...
<div class="field"> 
  <label>
    <strong>What dev concepts are you interested in?</strong>
  </label>
  <div class="control">
    <label class="checkbox">
      <input type="checkbox" v-model="form.concepts"
        value="promises">
      Promises
    </label> 
    <label class="checkbox">
      <input type="checkbox" v-model="form.concepts" 
        value="testing">
      Testing
    </label>
  </div>
</div>
...

data: {
  form : {
    name: '',
    message: '', 
    inquiry_type: '', 
    logrocket_usecases: [],
    terms: false,
    concepts: [], 
  }, 

}
```

### 这里有一个例子:

使用 vee-validate 验证用户输入

虽然编写自定义验证逻辑是可能的，但是已经有一个很棒的插件可以帮助轻松验证输入并显示相应的错误。这个插件是 [vee-validate](https://github.com/baianat/vee-validate) 。

```
...
<div class="field">
  <label><strong>Is JavaScript awesome?</strong></label>

<div class="control">
    <label class="radio">
      <input v-model="form.js_awesome" type="radio" value="Yes">
      Yes
    </label>
    <label class="radio">
      <input v-model="form.js_awesome" type="radio" value="Yeap!">
      Yeap!
    </label>
  </div>
</div>
...

data: {
  form : {
    name: '',
    message: '',
    inquiry_type: '',
    logrocket_usecases: [],
    terms: false,
    concepts: [],
    js_awesome: '' 
  }, 

}
```

## 这个用于 Vue.js 的表单验证库允许您验证输入，并以声明的方式或使用复合函数构建表单 ui。

首先，我们需要在我们的应用程序中包含插件。这可以通过 yarn 或 npm 来实现，但在我们的情况下，通过 CDN 来实现也可以:

为了设置插件，我们将把它放在 Vue 实例的正上方:

现在，我们可以对输入使用`v-validate`指令，并将规则作为字符串值传入。每条规则都可以用竖线隔开。

```
<script src="https://unpkg.com/[[email protected]](/cdn-cgi/l/email-protection)/dist/vee-validate.js"></script>
```

举一个简单的例子，让我们验证我们之前定义的`name`输入:

```
Vue.use(VeeValidate);
```

在上面的例子中，我们定义了两条规则:第一条是 name 字段是必需的(`required`)，第二条是在 name 字段中输入的任何值的最小长度应该是三(`min:3`)。

**提示:**规则可以被定义为对象以获得更大的灵活性。例如:`v-validate=”{required: true, min: 3}”`

```
...
<div class="field">
  <label class="label">Name</label>
  <div class="control">
    <input name="name" 
      v-model="form.name" 
      v-validate="'required|min:3'" 
      class="input" type="text" placeholder="Full name">
  </div> 
</div>
...
```

要在这些规则没有通过时访问错误，我们可以使用插件创建的错误助手对象。例如，要在输入下方显示错误，我们可以添加以下内容:

> 在上面的代码中，我们利用了来自`vee-validate`插件的几个助手方法来显示错误:

`.has()`帮助我们检查作为参数传入的输入字段是否有错误。它返回一个布尔值(真/假)。

```
<p class="help is-danger" v-show="errors.has('name')">
  {{ errors.first('name') }}
</p>
```

`.first()`返回与作为参数传入的字段相关联的第一个错误消息。

*   其他有用的方法包括`.collect()`、`.all()`和`.any()`。你可以在这里阅读更多关于他们的[。](http://vee-validate.logaretm.com/#render-errors)
*   注意，name 属性需要在我们的输入字段上定义，因为这是`vee-validate`用来标识字段的。

最后，我们可以向输入字段添加一个`is-danger`类(由布尔玛提供),以指示字段何时出现验证错误。这对用户体验非常好，因为当输入字段没有被正确填充时，用户会立即看到。

整个字段标记现在将如下所示:

你可以在下面的结果标签中看到目前正在进行的工作:

使用 JSFiddle 代码编辑器在线测试您的 JavaScript、CSS、HTML 或 CoffeeScript。

```
...
<div class="field">
  <label class="label">Name</label>
  <div class="control">
    <input name="name" 
      v-model="form.name" 
      v-validate="'required|min:3'" 
      v-bind:class="{'is-danger': errors.has('name')}"
      class="input" type="text" placeholder="Full name">
  </div>
  <p class="help is-danger" v-show="errors.has('name')">
    {{ errors.first('name') }}
  </p> 
</div>
...
```

You can see the work in progress so far in the results tab in the fiddle below:

> `vee-validate`有许多满足一般用例的预定义规则。可用规则的完整列表可以在[这里](http://vee-validate.logaretm.com/#available-rules)找到。如果您的表单有通用规则没有涵盖的任何需求，也可以定义自定义验证规则。

使用`Validator.extend()`创建自定义验证规则

我们可以使用`Validator.extend()`方法创建定制规则。您的自定义规则必须符合合同或特定的结构。

### 让我们添加一个验证方法，强制用户在发送消息时保持礼貌:

我们的验证器由两个属性组成:

`getMessage(field, params)`:返回一个字符串——验证失败时的错误信息。

```
VeeValidate.Validator.extend('polite', { 
  getMessage: field => `You need to be polite in the ${field} field`,
  validate: value => value.toLowerCase().indexOf('please') !== -1
});
```

`validate(value, params)`:返回一个布尔值、对象或承诺。如果没有返回布尔值，那么`valid(boolean)`属性需要出现在对象或承诺中。

*   也考虑到了本地化。您可以在完整的`vue-validate` [自定义规则文档](https://vee-validate.logaretm.com/v2/guide/custom-rules.html)中查看翻译和本地化说明。
*   使用事件处理程序提交表单

为了处理表单提交，我们可以使用 Vue 的提交[事件处理程序](https://v3.vuejs.org/guide/events.html)。对于方法事件处理程序，我们将使用`v-on`方法。我们还可以插入`.prevent`修饰符来阻止默认动作，在本例中，默认动作是提交表单时页面刷新:

## 在上面的例子中，我们只是在表单提交时将整个表单模型记录到控制台。

我们可以用`vee-validate`添加最后一点，以确保用户不被允许提交无效的表单。我们可以使用`errors.any()`来实现这一点:

```
...
<form v-on:submit.prevent="console.log(form)"> 
  ...
  <div class="field is-grouped">
    <div class="control">
      <button class="button is-primary">Submit</button>
    </div>
  </div>
</form>
...
```

在上面的代码中，一旦表单中存在任何错误，我们就禁用提交按钮。

太好了！现在我们已经从头构建了一个表单，添加了验证，并且可以使用表单中的输入值。你可以在 CodePen 上找到我们在[上完成的全部代码。](https://codepen.io/olayinkaos/pen/GMmpPm?editors=1010)

```
<button 
  v-bind:disabled="errors.any()"
  class="button is-primary">
  Submit
</button>
```

最终说明

其他一些需要注意的关键事项:

## 表单输入上的`v-model`有一些修饰符，包括`.lazy`、`.number`和`.trim`。你可以在这里读到关于他们[的一切。](https://vuejs.org/v2/guide/forms.html#Modifiers)

可以使用`v-bind:value`将动态属性绑定到输入值。查看关于[值绑定](https://vuejs.org/v2/guide/forms.html#Value-Bindings)的文档了解更多信息。

*   在本指南中，我们学习了如何在 Vue.js 应用程序中创建表单、验证表单以及使用其字段值。我们提出了一种在 Vue.js 中创建表单的方法，但是还有其他选择。本教程向你展示了如何用 Vue Formulate 创建表单。
*   你对 Vue.js 中的表单有什么问题或意见吗，或者它们与其他框架中的表单相比如何？欢迎发表评论，让我们开始讨论吧！

像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

## .

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。

The LogRocket Vuex plugin logs Vuex mutations to the LogRocket console, giving you context around what led to an error, and what state the application was in when an issue occurred.

Modernize how you debug your Vue apps - [Start monitoring for free](https://lp.logrocket.com/blg/vue-signup).