# 使用 Vest 进行优雅的数据验证

> 原文：<https://blog.logrocket.com/elegant-data-validation-vest/>

还有什么比用测试代码的方式测试表单的有效性更优雅的呢？

Vest 是一个强大的 JavaScript 数据验证库，它的语法来自现代 JS 框架，如 Mocha 或 Jest。它是框架不可知的，所以你可以立刻把它放到你的代码中。

单元测试应用于数据验证？没错。

如果用摩卡，我们会这样做:

```
import { assert } from 'chai'
let hello = () => 'Hello!'
assert.equal(hello, 'Hello!')

```

同样，使用 Vest，我们可以做到这一点:

```
import { enforce } from 'vest'
let hello = document.querySelector('#hello').value
enforce(hello).equals('Hello!')

```

## “吸烟”！"

首先，我们需要导出一个验证函数——也称为 suit——用于对作为对象传递的输入数据执行测试。

```
import vest, { test, enforce } from 'vest'

const suitName = 'signUpSuit'

const validate = vest.create(suitName, formData => {

   test('username', 'Should not be empty', () => {
       enforce(formData.username).isNotEmpty()
   })

   test('password', 'Should be longer, () => {
       enforce(formData.password).longerThanOrEquals(8)
   })

   test('password_repeat', 'Should match password', () => {
       enforce(formData.password_repeat).equals(formData.password)
   })

})

export default validate

```

现在，我们可以简单地在逻辑中发生验证时调用它:

```
import validate from './signup/validate.js'

let response = validate(formData)

```

Vest 是一个通用的数据验证器，所以它不包含任何表单序列化方法——但是，收集数据非常容易！要么你写的是普通的 JS:

```
let formData = inputs.reduce((data, input) => {
   return {
       ...data,
       ...{ [input.name]: input.value }
   }
, {})

```

或者使用 React 这样的框架:

```
const [formData, setFormData] = useState({})

const onInputChange = e => {
   setFormData(data => {
       ...data,
       ...{ [e.target.name]: e.target.value }
   })
}

```

现在我们知道了如何收集和传递数据，我们需要处理验证套装的结果，以便为用户定制适当的响应。

## “我们到了吗？”

验证总是返回包含有用信息和方法池的对象:

```
let response = validate(formData)

```

无论如何，在处理动态分配的变量时，代码会很快变得混乱。Vest 提供了一个`.done()`方法，可以多次链接到验证函数。在这里，我们可以传递一个回调函数来优雅地处理响应:

```
validate(formData).done(callback)

```

在回调函数之前指定一个可选键，我们可以显式地等待单个字段的验证。无论回调在链中的顺序如何，只有满足条件时才会执行回调。

```
validate(formData)
   .done('username', usernameCallback)
   .done(formCallback)

```

## “我们需要一个反应小组！”

为了检查是否有测试失败，我们需要调用`.hasErrors()`响应方法。

```
if (response.hasErrors()) let errors = response.getErrors()

```

如果为真，可以从`.getErrors()`方法中以键值对象的形式访问错误的完整列表。对于每个没有通过测试的字段，将有一个包含错误消息列表的数组。

可以为单个字段提供多个测试，并且出现的顺序将反映在数组中。

```
test('username', 'Cannot be empty', () => {
   enforce(formData.username).isNotEmpty()
})

test('username', 'Must be longer than 3 chars', () => {
   enforce(formData.username).isLongerOrEquals(4)
})

```

如果将名称传递给上述方法，将只返回该特定字段的错误:

```
if (response.hasErrors('password')) {
   let passwordErrorMessage = response.getErrors('password')[0]
   // ...

```

当测试调用`vest.warn()`时，它的消息将可以从`.hasWarnings()`和`.getWarnings()`方法中访问。

这允许我们将响应消息分为两个不同的重要级别。

```
test('username', 'Should not be a number', () => {
   vest.warn()
   enforce(formData.username).isNotNumeric()
})

```

## “请下一组。”

可以对测试进行分组，以便在我们的逻辑的某一点上过滤应该测试哪些输入。这非常有用，例如，验证按顺序步骤发送数据的多页表单。

```
const validate = vest.create(suitName, formData => {
   group('formPage1', () => {
       // test(...)
   })
})

```

从`.hasErrorsByGroup()`和`.getErrorsByGroup()`可访问分组验证结果:

```
if (response.hasErrorsByGroup('formPage1')) {
   // ...

```

也可以使用`vest.skip()`过滤掉测试。该方法接受字段名或组名，并可用于在某些条件下避免测试。

```
const validate = vest.create('purchaseSuit', formData => {
   if (!formData.coupon) vest.skip('coupon')
   // ...

```

## “我做实时的。”

通过调用`vest.only()`，我们可以要求 Vest 只为特定的字段或组执行测试。

```
const validate = vest.create('userSuit',
   (data = {}, inputName = '') => {
       if (inputName) vest.only(inputName)
       // ...

```

这在用户输入时实时验证数据时特别有用:

```
const onInputChange = e => {
   validate({ [e.target.name]: e.target.value }, e.target.name)
       .done(e.target.name, callback)
}

```

默认情况下，Vest 是有状态的，所以即使我们为单个字段传递数据——就像上面的例子一样——响应也会返回所有已经测试过的字段的最新验证结果，从而使更新我们的应用程序状态变得更加容易。

此外，最新的套装状态始终可从`vest.get(suitName)`访问，并可通过`vest.reset(suitName)`重置。

如果你想运行无状态，直接从 Vest 导入一个`validate()`函数，而不是创建一个新的。这更适合服务器端验证，在这种情况下，响应应该是等幂的。因此，使用:

```
const response = validate('stateless', data => {
   // ...
})

```

而不是:

```
const validate = vest.create('stateful', data => {
   // ...
})

```

## “为此，我们需要服务器！”

Vest 允许异步验证。这在我们需要对照数据库中的条目检查数据时特别有用，比如检查用户名是否已经被使用。

```
test('username', 'Should not already exist', async () => {
   return await checkIfUserExists(formData.username)
})

```

请记住，异步响应可能会在其他同步测试已经运行完之后出现。一般来说，建议在所有同步请求运行之后，在 suit 逻辑的底端执行异步测试。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

在这种情况下，我们可以使用`vest.draft()` 方法来保存服务器资源。这样，我们可以针对当前正在运行的套装的状态执行条件操作，并避免不必要的请求:

```
test('username', 'Cannot be empty', () => {
   enforce(formData.username).isNotEmpty()
})
if (!vest.draft().hasErrors('username')) {
  test('username', 'Should not already exist', async () => {
       return await doesUserExist(formData.username)
   })
}

```

## “我们有一个规则。”

规则可以链接在一起，如果缺省值不能满足您的需要，您可以简单地创建返回布尔值的定制逻辑，或者为了更好的可重用性，扩展缺省方法池:

```
import { enforce } from 'vest'

const customRules = {
   isTruthyStr: value =>
       ['true','on'].includes(String(value).toLowerCase()),
   isFalsyStr: value =>
       ['false','off'].includes(String(value).toLowerCase()),
}

enforce.extend(customRules)

```

用 Vest 的`any()`，我们可以表达规则之间的一个逻辑 OR。如果满足任何强制规则，则整个测试视为通过:

```
test('comment', 'Must be at least 140 chars, if provided.',
   any(
       () => enforce(data.comment).isEmpty(),
       () => enforce(data.comment).longerThanOrEquals(140)
   )
)

```

## “上课，上课，上课。”

一套量身定做的西装没有档次就什么都不是了吧？Vest 提供了一个`classNames()`函数，这是一个基于验证状态将 CSS 类关联到 DOM 元素的强大方法:

```
import classNames from 'vest/classNames'
const getFieldClass = classNames(response, {
  untested: 'is-untested',
  tested: 'is-tested',
  valid: 'is-valid',
  invalid: 'has-errors',
  warning: 'has-warnings',
})

```

这允许实时更新我们组件的外观:

```
function InputText(props) {
   return <input
       type='text'
       name={props.name}
       value={props.value}
       className={getFieldClass(props.name)}
       onChange={e => onInputChange(e)}
    />
}

```

通过这种方式，可以实时表达基于输入状态的复杂 UI 逻辑，而不会出现意大利面条！

## 您是否添加了新的 JS 库来提高性能或构建新特性？如果他们反其道而行之呢？

毫无疑问，前端变得越来越复杂。当您向应用程序添加新的 JavaScript 库和其他依赖项时，您将需要更多的可见性，以确保您的用户不会遇到未知的问题。

LogRocket 是一个前端应用程序监控解决方案，可以让您回放 JavaScript 错误，就像它们发生在您自己的浏览器中一样，这样您就可以更有效地对错误做出反应。

[![LogRocket Dashboard Free Trial Banner](img/e8a0ab42befa3b3b1ae08c1439527dc6.png)](https://lp.logrocket.com/blg/javascript-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/javascript-signup)

[LogRocket](https://lp.logrocket.com/blg/javascript-signup) 可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

自信地构建— [开始免费监控](https://lp.logrocket.com/blg/javascript-signup)。