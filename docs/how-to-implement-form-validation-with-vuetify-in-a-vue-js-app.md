# 如何在 Vue.js 应用程序中使用 Vuetify 实现表单验证

> 原文：<https://blog.logrocket.com/how-to-implement-form-validation-with-vuetify-in-a-vue-js-app/>

如今，大多数网站都有要求用户提供信息的注册、登录或联系表单。因为大多数表单在错误处理时不能提供足够的反馈，所以验证这些表单有助于改善用户的整体体验。

例如，当用户填写的表单中有错误时，用户直到提交表单后才知道存在错误，这意味着他或她必须再次完成并重新提交表单。

但是，使用 [Vuetify](https://vuetifyjs.com/en/) ，一个用于 [Vue.js](https://vuejs.org/) 的材料设计组件框架，我们可以实现质量形式验证。在这篇博文中，我们将学习如何构建一个注册表单并使用 Veutify 验证它。

首先，让我们从创建一个 Vue.js 项目开始。

## 创建 Vue.js 应用程序

我们将使用 [Vue CLI](https://cli.vuejs.org/) 创建一个 Vue 应用程序。如果您的本地系统中没有安装 Vue CLI，请运行下面的 npm 命令进行安装:

```
npm install -g @vue/cli

```

命令运行完毕后，我们可以通过运行下面的命令来创建一个 Vue.js 项目。

```
vue create vuetify-form-validation

```

之后，我们会被询问是想要默认预设还是手动设置。在这个项目中，我们将使用默认的预置。

现在让我们将 Vuetify 添加到项目中。

## 将 Vuetify 添加到项目中

Vuetify 为用户提供了构建丰富且引人入胜的 web 和移动应用程序所需的一切。

要添加 Vuetify，导航到项目文件夹**CD vue tify-form-validation**。

然后运行下面的命令来添加 Vuetify:

```
vue add vuetify

```

当 Vuetify 命令开始运行时，它将再次要求选择您想要的预置。选择默认预设。

## 构建注册表单界面

在本文中，我们将构建一个注册表单，它将包含以下字段:

*   西方人名的第一个字
*   姓
*   电子邮件
*   密码
*   请重新输入密码

我们还将学习如何验证每个表单字段。首先，让我们设计表单页面。打开组件文件夹，删除`HelloWorld.vue`文件，创建`RegistrationForm.vue`。

我们将使用 Vuetify grid 组件将页面分成两列。

在 **RegistrationForm.vue** 模板内，编写以下代码:

```
<template>
  <v-container fluid class="fluid">
    <v-row justify="center" align="center" class="row">

      <v-col cols="6" sm="12" md="6" xs="12" class="text-center purple darken-2"
        style="height: 100vh">

        <v-row justify="center" align="center" style="height: 80vh">
          <v-col>
            <h1 class="white--text">LogRockets</h1>
          </v-col>
        </v-row>

      </v-col>

      <v-col cols="6" sm="12" md="6" xs="12" class="text-center" style="height: 100vh">
        <h1>Welcome to LogRockets</h1>
        <p>Please complete this form to create an account</p>
      </v-col>

    </v-row>
  </v-container>
</template>

```

注意，我们用`<v-container>`包装了整个部分。

`<v-container>`是一个 Vuetify 容器，我们可以使用它来居中或水平填充一个网站，并将`fluid`的属性绑定到`<v-container>`，这样它就可以在构建网站时以我们想要的任何屏幕尺寸完全扩展我们的容器。这也使它反应灵敏。

我们还使用了`<v-row>`来包装整个部分。

`<v-row>`允许我们使用 flex 属性，因此它可以按照我们希望的方式控制`<v-cols>`的流程。

我们还添加了`<v-cols>`，传递了一个属性`cols`，并赋予它一个值 6，因为我们只想将页面分成两列。注意，Vuetify `cols`只限于 12 列。
左栏有一个值为`purple`的 class 属性，为我们的 UI 提供紫色作为背景色。它有一个值为`height 100vh`的样式属性。在左栏中，我们有一个带有文本的`<h1>`标签。

在右栏中，有一个值为`text-center`的 class 属性和一个值为`height 100vh`的 style 属性。在右边的`cols`中，我们有一个带有文本的`<h1>`标签和一个`<p>`标签。

我们将值为 100vh 的 style 属性传递给两列，因为我们希望表单占据整个网站页面。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 构建注册表单

现在，让我们建立我们的形式。在右栏中，在`<p>`标签下面，编写下面的代码:

```
<v-form ref="form" class="mx-2" lazy-validation>
</v-form

```

`<v-form>`是一个 Vuetify 表单组件，它让我们很容易验证表单输入。

## 添加表单域

接下来，我们将实现表单输入字段。第一个字段是**名和姓输入**字段。让我们也实现它的验证。

在 v 型表单中，编写以下代码:

```
<v-row>
  <v-col cols="6">
    <v-text-field v-model="firstname" :rules="nameRules" label="First Name">
    </v-text-field>
  </v-col>
  <v-col cols="6">
    <v-text-field v-model="lastname" :rules="nameRules" label="Last Name">
    </v-text-field>
  </v-col>
</v-row>
```

在上面的代码中，我们首先将`<v-input>`包装在一行中，就像我们将网页分成两列时所做的那样。我们希望表单输入在第二列。

我们采取的下一步是添加名字和姓氏输入字段。

`<v-input>`有一个属性`<v-model>`，它是一个 Vue.js 属性，从输入中接收一个值。`<v-input>`有一个属性`:rules`，以函数`nameRules`为值。`nameRules`功能用于设置一个条件，检查传递给输入字段的值是有效还是无效。

`<v-input>`有一个属性`label`，默认情况下它的作用就像一个输入占位符和标签。该值是输入的类型。默认情况下，Vuetify `v-input`有一种文本类型，所以你可以选择忽略或不忽略它。

除了`label`和`v-model`之外，`Last Name`字段也具有与`First Name`字段相同的条件。

## 验证名字和姓氏字段

如果我们现在服务我们的应用程序，并将光标放在两个输入字段上，什么都不会发生。这是因为验证功能没有分配给输入。

现在，我们将向两个输入添加一个必需的验证，说明输入不能为空。接下来，我们将为我们声明的`nameRules`编写验证函数。

转到**模板标签**外的**脚本标签**，并编写以下代码:

```
<v-text-field v-model="firstname" :rules="nameRules" label="First Name" required>
</v-text-field>

firstname: '',
lastname: '',
nameRules: [
  v => !!v || 'Name is required',
  v => (v && v.length <= 10) || 'Name must be less than 10 characters',
],

```

在这段代码中，我们声明了将两个姓名输入字段的值保存为字符串的`firstname`和`lastname`变量。之后，我们声明了作为变量传递给输入标签上的`:rules`属性的`nameRules`变量。

`nameRules`变量有一个条件，检查传递给输入字段的值是否是有效值。如果不是，`nameRules`应该一直显示错误。

如果我们现在运行我们的应用程序，并尝试在名字和姓氏字段中键入内容，然后移开光标，您将会收到一条错误消息，提示需要输入姓名。

## 实现电子邮件字段

我们要看的下一个字段是电子邮件字段及其验证。在“姓氏”字段下，编写以下代码:

```
<v-row>
  <v-col cols="12">
    <v-text-field v-model="email" :rules="emailRules" label="Email" required>
    </v-text-field>
  </v-col>
</v-row>

```

在 email 字段中，我们传递接受电子邮件值的`v-model`、保存验证函数的`:rule`属性以及告诉用户输入类型的标签。

## 验证电子邮件字段

编写以下代码来触发电子邮件验证:

```
email: '',
emailRules: [
  v => !!v || 'E-mail is required',
  v => /^(([^<>()[\]\\.,;:\[email protected]']+(\.[^<>()\\[\]\\.,;:\[email protected]']+)*)|('.+'))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/.test(v) || 'E-mail must be valid',
 ],

```

在其中，我们声明了一个 email 变量，它包含 email 的值和`emailRules`变量，后者在将值发送到服务器之前检查输入的值是有效的还是无效的。

我们的另一个验证是检查输入是否为空，如果为空，则自动向用户提示错误消息。

## 实现密码和复选框字段

最后，让我们实现密码和复选框字段及其验证。写下面的代码。

```
<v-row>
  <v-col cols="6">
    <v-text-field v-model="password" type="password" :rules="passwordRules"
     label="Password" required></v-text-field>
  </v-col>
</v-row>

<v-checkbox v-model="firstcheckbox" :rules="[v => !!v || 'You must agree to continue!']"
    label="I agree with Terms and Conditions" required></v-checkbox>

<v-checkbox v-model="seccheckbox" :rules="[v => !!v || 'You must agree to receive!']"
    label="I want to receive LogRocket Emails" required></v-checkbox>

```

注意，密码字段有一个接受输入字段值的`v-model`属性和一个接受验证函数的`:rule`属性。

`v-checkbox`还有一个`v-model`属性，它接受复选框输入字段的值。`checkbox :rule`属性直接获取验证规则，而不通过任何变量传递。

## 验证密码字段

编写下面的代码来验证密码字段。

```
password: '',
  passwordRules: [
      v => !!v || 'Password is required',
      v => /(?=.*\d)(?=.*[a-z])(?=.*[A-Z]).{6,}/.test(v) || 'Password must contain at least lowercase letter, one number, a special character and one uppercase letter',
  ],
    firstcheckbox: false,

```

`passwordRule`变量与我们已经声明的 email 和 name 规则数组是一样的，但是每个函数都有自己的任务要执行。`password rule`检查密码是否包含大写字母、小写字母、特殊字符和数字。

## 声明按钮验证

使用 Veutify 验证，我们仍然可以通过一个按钮来验证输入字段，而不需要传递`:rule prop`。这里有一个例子:

```
<v-btn class="purple darken-2 white--text mt-5"  @click="submitForm"> Register </v-btn>

methods: {
  submitForm () {
    this.$refs.form.validate();
  },
},

```

上面的代码是一个 Vuetify 按钮组件。按钮组件有一个连接到点击事件的`submitForm`函数。每当触发该事件时，它调用 Vuetify `this.$refs.form.validate()`方法，该方法检查传递给输入字段的值是否有效。如果输入字段为空，它会向用户提示一条错误消息。

这是我们使用 Vuetify 验证表单的另一种方式。

## 结论

在这篇博文中，我们学习了如何在 Vue 应用程序中使用 Vuetify 来验证表单，也看到了验证 Vuetify 表单的两种不同方式。如果您想查看代码或了解更多信息，请访问 [Github](https://github.com/FayVik/vuetify-form-validation) 。

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。