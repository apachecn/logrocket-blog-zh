# 何时以及如何选择 HTML 进行表单验证

> 原文：<https://blog.logrocket.com/when-how-to-choose-html-form-validation/>

## 介绍

JavaScript 在 web 应用程序中最常见的用途之一是表单验证。客户端表单验证为用户提供了关于其输入数据是否有效的近乎即时的反馈。开发人员倾向于从众多优秀的表单验证库中选择一个，特别是当他们在 React 或 Angular 这样的框架环境中工作时，那里有很多这样的解决方案。

这些库为您的应用程序增加了价值，但是即使您可能只需要库的一小部分功能，它们也是有代价的，尤其是对您的包大小而言。

如果它符合您的用例，可能会有更简单的方法。在本文中，我们将深入探讨 HTML 表单验证的所有内容，包括:

## HTML5 表单验证功能

HTML5 和现代的 DOM APIs 已经为表单验证做好了准备。HTML5 于 2008 年问世，带来了许多改进，包括`input`标签，它围绕表单验证进行了几次更新。

### 验证属性

HTML5 增加了新的属性，以声明方式为给定的输入字段设置验证规则。这些新属性包括:

*   `required`:表示该字段不能为空
*   `min` / `max`:指定数值输入的允许值范围
*   `minlength` / `maxlength`:指定文本输入允许的长度范围
*   `step`:对于`n`的步长，数值输入值只有是`n`的倍数才有效
*   `pattern`:指定文本输入必须匹配的正则表达式

### CSS 选择器

HTML5 还引入了一些新的 CSS 选择器，包括两个新的伪类— `:valid`和`:invalid`。它们分别匹配其值通过或未通过验证的任何输入。

例如，我们可以用红色边框自动标记无效字段:

```
input:invalid {
  border: 2px solid red;
}

input:valid {
  border: 2px solid green;
}
```

在此必填字段中输入值之前，该值会自动标记为无效:

![Invalid Entry For Required Field](img/bda5cbfa681cf546872ed65c29fa36fc.png)

当输入一个值时，`:invalid`选择器不再匹配，取而代之的是`:valid`选择器:

![Valid Entry](img/e3253ee4664b680b6dd03695b8f70177.png)

### 内置验证消息

如果您试图提交一个包含无效值输入的表单，表单提交将被取消，并显示浏览器提供的错误消息(如下所示，如 Chrome 中所示):

![Inbuilt Validation](img/b3b0e3351849cca71e78207ee92cef98.png)

对于您的应用程序来说，这可能并不理想。消息的外观和感觉因浏览器而异，可能与 UI 的其他部分不一致。

因此，这些验证消息是可选的。您可以通过在包含输入的表单上设置`novalidate`属性来退出它们:

```
<form name="loginForm" novalidate>
  Username:
  <input type="text" name="username" required>
</form>
```

这只会禁用验证行为—规则仍然有效。如果表单包含无效输入，它仍然会被提交。如果设置了`novalidate`属性，表单将不再在提交时自动验证其字段。在这种情况下，您必须通过调用表单上的`checkValidity`手动完成此操作，如果表单无效，则停止提交表单。

这样做的好处是您可以完全控制验证过程及其行为。验证规则和当前验证状态仍然通过约束验证 API 提供给您。

### 约束验证 API

这个 DOM API 与我们上面讨论的 HTML5 验证属性密切相关。它提供了一些对象、方法和事件，允许您构建验证行为，而不必担心检查验证规则本身。

## 用`ValidityState`检查有效性

表单中的输入标签有一个`validity`属性，它是一个`ValidityState`对象，反映输入的当前验证状态。该值实时更新；它总是及时反映该时刻的验证状态。

`ValidityState`有一系列布尔标志。主要的是简单的`valid`——如果元素通过了所有适用的状态检查，这就是`true`。一旦有效输入由于其值的变化而变为无效，该标志立即变为`false`。

如果一个元素的`validity.valid`属性是`false`，那么可以在`ValidityState`中检查附加的标志，以确定违反了哪个验证规则。这些标志的不同组适用于不同的验证规则类型。

### `ValidityState`标志

| **验证规则** | **关联的有效性标志** |
| `required` | **`valueMissing`** :输入为空值

*   `min, max`

 |
| **`rangeOverflow`** :该值小于允许的最小值 | **`rangeOverflow`** :超过最大允许值

*   `minlength, maxlength`

 |
| **`tooShort`** :输入值的长度小于最小值 | **`tooShort`** :输入值长度超过最大值

*   `step`
*   **`stepMismatch`** :输入值不在允许的步长范围内

 |
| `pattern` | **`patternMismatch`** :输入值与给定模式不匹配

*   `type="email", type="url"`

 |
|  | **`typeMismatch`** :输入值与指定类型不匹配。例如，`type`为`"email"`的输入值不是有效的电子邮件地址

*   使用`checkValidity`按需验证

 |
| 当用户试图提交表单时，系统会检查表单的有效性，但是您可能希望表单能够更快地标记无效字段(可能是在模糊时，甚至是在更改时)。 | 这可以通过在特定的输入上或者在封闭的表单元素上调用`checkValidity`方法来完成。

*   当在表单上调用`checkValidity`时，如果所有字段都有效，它将返回`true`。如果任何字段无效，表单将返回`false`，每个无效字段将单独触发一个`invalid`事件。当在单个输入上调用时，它的行为类似，只是它只为该输入检查并可能触发事件。

 |

## 参见 [CodePen](https://codepen.io) 上 Joe Attardi([@ thinksInCode](https://codepen.io/thinksInCode))
的笔 [自定义验证码](https://codepen.io/thinksInCode/pen/zYREBeV)。

A form is checked for validity when a user attempts to submit it, but you may want your form to flag invalid fields sooner (perhaps on blur, or even on change).

在上面的 CodePen 中，每当字段改变时，我们手动触发验证。如果提交的表单带有空字段，它会显示验证错误，但只要您键入一个字符，错误消息就会消失。如果删除该字段的内容，验证错误将立即重新出现。

自定义验证逻辑

您可能会遇到这样的情况，没有一个内置的验证检查适用于您的验证规则。例如，您的注册表单中可能有密码和密码确认字段。如果这些密码不匹配，这些字段应该被标记为无效。

为了支持这个用例，所有的输入都有一个`setCustomValidity`方法。这将把字段标记为无效，并将指定的消息设置为验证错误。

请注意，这只会将验证状态设置为无效。它不对值执行任何类型的检查；您将需要实现自定义逻辑。这里有一个简单的例子:

## 当在上面的`confirmPasswordField`输入上调用`setCustomValidity`时:

输入的`validity.valid`标志变为`false`

输入的`validity.customError`标志变为`true`

输入的`validationMessage`属性变为“密码不匹配”

```
function validatePasswords() {
  if (passwordField.value !== confirmPasswordField.value) {
    confirmPasswordField.setCustomValidity('Password does not match');
  }
}
```

异步自定义验证

*   让我们考虑一个更复杂的注册表单的例子。给新用户一个关于他们选择的用户名是否已经被使用的反馈是很好的。这可以异步完成，并用`setCustomValidity`设置结果。
*   更多来自 LogRocket 的精彩文章:
*   自定义验证消息和`checkValidity`

## 在上面的例子中，如果密码不匹配，调用表单上的`checkValidity`仍然会返回`true`，并且不会触发无效事件。您需要手动检查字段并设置自定义有效性。

然而，一旦输入设置了自定义错误，任何对`checkValidity`的进一步调用都将看到自定义错误，验证将失败，受影响的输入将触发`invalid`事件。

* * *

### 这可以包装在表单的提交处理程序中:

* * *

```
async function checkUsername() {
  // We only should make the async check if the username field has a
  // value, otherwise we will skip this and the blank username field
  // will be handled when the built-in validation runs (assuming the
  // username field has the `required` attribute set.
  if (usernameField.value) {
    // Use the Fetch API to call our endpoint to validate the username.
    // The response will contain a `valid` property.
    const response = 
      await fetch(`/api/checkUsername/${usernameField.value}`);
    const { valid } = await response.json();

    if (!valid) {
      usernameField.setCustomValidity('This username is already taken.');
    }
  }
}
```

### 集成异步验证

约束验证 API 是同步的。当在表单或输入上调用`checkValidity`时，它会立即设置验证结果。如果您的表单包含需要异步验证逻辑的输入，比如上面的用户名检查示例，您将需要推迟`checkValidity`调用，直到您的异步操作完成并且`setCustomValidity`调用已经发出。

扩展上面的提交处理程序以包括用户名检查可能如下所示:

约束验证 API 的限制

```
form.addEventListener('submit', event => {
  // first check any custom validation logic
  if (passwordInput.value !== confirmPasswordInput.value) {
    confirmPasswordInput.setCustomValidity('Passwords do not match');
  }

  // now run the built-in validation, which will detect 
  // any custom validation errors from above
  // if validation fails, stop the submission
  if (!form.checkValidity()) {
    event.preventDefault();
  }
});
```

### 虽然 API 简化了许多核心验证逻辑，但在大多数情况下，您需要做一些额外的工作来填补功能完整的验证解决方案的空白。

建立你自己的流程

这个 API 的主要限制是需要明确一些验证操作。如果设置了`novalidate`属性——通常是为了禁用浏览器内消息——验证流必须手动编排。

```
form.addEventListener('submit', async event => {
  // first check any custom validation logic

  // as shown above, if the username field is blank the async validation
  // will be skipped, and the required validation rule will be handled
  // below when we call `form.checkValidity`.
  await checkUsername();

  if (passwordInput.value !== confirmPasswordInput.value) {
    confirmPasswordInput.setCustomValidity('Passwords do not match');
  }

  // now run the built-in validation, which will detect 
  // any custom validation errors from above
  // if validation fails, stop the submission
  if (!form.checkValidity()) {
    event.preventDefault();
  }
});
```

## 幸运的是，没有太多额外的代码要写。如果您只想在提交时验证，上面的代码片段就可以了。如果您想更快地进行验证，例如当输入失去焦点时，您将需要侦听模糊事件，并在事件处理程序中执行任何自定义验证，然后对输入调用 checkValidity。

显示错误消息

### 如果通过`novalidate`属性禁用浏览器内验证消息，字段将在其`ValidityState`中被标记为无效，但不会显示任何消息。浏览器仍然用`input`元素的`validationMessage`属性提供合适的消息，但是您需要添加代码来显示消息(或者提供您自己的代码)。

一个`input`可以监听`invalid`事件，并显示一个适当的错误消息作为响应。如果有自定义的有效性消息，可以使用它，否则消息将依赖于输入的`ValidityState`中的值。

自定义或高级验证逻辑

### 这有好的一面也有不好的一面。一个核心 API 不能预见所有可能的验证场景，所以如果你有独特的验证需求，通常会有一个缺口。但是，一旦执行了自定义验证检查，就可以依靠 API 将这些结果转换成表单有效性状态的其余部分。

例如，自定义验证检查在“更改密码”页面上可能很有帮助，该页面上有两个允许输入密码的字段。自定义验证检查可以指示新密码和确认密码输入是否匹配。请参见下面的代码:

const form = document . query selector(' form ')；const error = document . query selector('。错误’)；const input = document . query selector(' input ')；const success = document . query selector(# success))；const inputs = { password:document . query selector(' # password ')，confirm password:document . query selector(' # confirm password ')} const error messages = { password:document . query selector(' # password-error ')，confirm password:document . query selector(' # confirm password-error ')}；form.addEventListener('submit '，event = > { success . class list . add(' hidden ')；//停止表单提交，这样我们就不会离开 CodePen！

### 结论

约束验证 API 只是近年来增加的许多方便的浏览器 API 之一。约束验证 API 与 HTML5 验证属性相结合，可以为您节省大量样板验证代码。对于简单的应用程序，它可能是你所需要的。

它还拥有强大的浏览器支持，最早可以追溯到 Internet Explorer 10！旧的浏览器可能不支持这个 API 的一些新特性，但是核心支持是有的。

> 对于更高级的验证或更大规模的应用，这可能还不够。

幸运的是，API 公开了可靠的底层构建块，可以用更少的代码构建更健壮的验证解决方案。您需要担心自定义验证、错误消息以及何时进行验证，但是 API 会负责检查和验证字段。

## 如果你有兴趣看更多的例子，可以看看我整理的 CodePen 集合。

使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)

## Cut through the noise of traditional error reporting with [LogRocket](https://lp.logrocket.com/blg/signup)

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) is a digital experience analytics solution that shields you from the hundreds of false-positive errors alerts to just a few truly important items. LogRocket tells you the most impactful bugs and UX issues actually impacting users in your applications.

Then, use session replay with deep technical telemetry to see exactly what the user saw and what caused the problem, as if you were looking over their shoulder.

LogRocket automatically aggregates client side errors, JS exceptions, frontend performance metrics, and user interactions. Then LogRocket uses machine learning to tell you which problems are affecting the most users and provides the context you need to fix it.

Focus on the bugs that matter — [try LogRocket today](https://lp.logrocket.com/blg/signup-issue-free).