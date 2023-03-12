# 验证 Laravel 中的数组和嵌套值

> 原文：<https://blog.logrocket.com/validating-arrays-nested-values-laravel/>

随着前端架构的复杂性增加，开发人员能够向后端提交更复杂的数据变得比以往任何时候都更加重要。

因为我们正在构建更复杂的表单，所以我们需要更有效的方法来验证和处理来自这些表单的数据。幸运的是，Laravel 提供了许多方法，可以轻松地验证来自前端的数据。

## Laravel 中的验证基础

在我们讨论验证数组和嵌套数组之前，让我们先概述一下 Laravel 验证的基础知识。

通常，进入 Laravel 的 HTTP 请求主要是在控制器中处理的(也有其他地方处理请求，比如中间件，但那是另一篇文章中的讨论)。因此，许多开发人员也选择在这里存放他们的验证方法。

假设我们正在构建一个非常简单的库存软件。在这个软件中，我们将把商品存储在数据库的“产品”表中。我们访问和管理表的模型是`Product`，控制器将被命名为`ProductController`

在我们的表单中，有项目名称、SKU 和价格字段。当发出 POST 请求时，我们需要验证这些项目。

```
public function store(Request $request)
{
    $validated = $request->validate([
        'item_name' => 'required|string|max:255',
        'sku' => 'required|string|regex:​​/^[a-zA-Z0-9]+$/',
        'price' => 'required|numeric'
    ]);

    Product::create($validated);
}

```

上面的代码是我们在控制器中验证请求的最简单的方法。请注意，在每个键(属性)旁边都有一个字符串，管道将我们想要验证属性的所有规则分隔开来。

令人惊奇的是，通过一些规则，你可以提供更多的上下文来说明你想要什么。例如，在上面的代码块中，您会看到`max:255`，这意味着`item_name`不能超过 255 个字符；`regex:​​/^[a-zA-Z0-9]+$/`表示我们只需要字母数字字符。这些仅仅是 Laravel 内置的众多规则中的一小部分。

当上面的项目通过验证后，会产生一个带有相关错误消息的 HTTP 重定向。但是，如果发出一个 XHR 请求(类似于来自 API 的请求)，就不会进行重定向，而是用 JSON 和 422 HTTP 状态代码进行响应。

一些 Laravel 开发人员选择使用更复杂的验证方法对此进行扩展。一种方法是使用`Validator`对象。

```
 public function store(Request $request)
{
    $validator = Validator::make($request->all(), [
        'item_name' => 'required|string|max:255',
        'sku' => 'required|string|regex:​​/^[a-zA-Z0-9]+$/',
        'price' => 'required|numeric'
    ]);

    If ($validator->fails()){
        // Do something
    }
    Product::create($validated);
}

```

Laravel devs 扩展验证的另一种方式是通过使用表单请求将验证从控制器中分离出来。这是我个人最喜欢的扩展验证的方式，因为当我制定自定义规则、[使用 After Validation Hooks](https://laravel.com/docs/8.x/validation#adding-after-hooks-to-form-requests) 或扩展规则等时，我能够整齐地组织一切。

```
 <?php

namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;

class ProductRequest extends FormRequest
{
    public function authorize()
    {
        return true;
    }

    public function rules()
    {
        return [
            'item_name' => 'required|string|max:255',
            'sku' => 'required|string|regex:​​/^[a-zA-Z0-9]+$/',
            'price' => 'required|numeric'
        ];
    }
}

```

## 验证简单嵌套属性

比方说，我想让我的库存软件变得复杂一点，有一个包含两个嵌套字段的`item`字段:`name`和`description`。

在前端，它看起来像这样:

```
<form method="POST">
    <input type="text" name="item['name']" />
    <input type="text" name="item['description']" />
    <input type="text" name="sku" />
    <input type="text" name="price" />
    <button type="submit">Submit</button>
</form>

```

假设我们现在使用表单请求来验证传入的数据，因为我们希望更好地组织数据。规则方法将如下所示:

```
public function rules()
{
    return [
        'item.name' => 'required|string|max:255',
        'item.description' => 'nullable|string|min:60',
        'sku' => 'required|string|regex:​​/^[a-zA-Z0-9]+$/',
        'price' => 'required|numeric'
    ];
}

```

然后，我们可以在控制器中使用经过验证的数据，如下所示:

```
public function store(ProductRequest $request)
{
    // Do something or just save straight to the db like below
   Product::create($request->validated());
}

```

如您所见，我们使用点符号来表示嵌套属性。当我们想要定制错误消息时，点符号也很重要。例如，如果我们想定制当有人在价格字段中输入字母字符时的错误消息，我们可以这样做:

```
public function messages()
{
    return [
        'price.required' => 'You must have a price.',
        'price.numeric' => 'You have invalid characters in the price field'
    ];
}

```

请注意，当我们制作自定义消息时，我们使用“字段[点]规则”这一语法。

## 验证数组和嵌套属性

假设我们通过使用包含可重复部分的表单使数据收集变得更加复杂。例如，我们希望存储不同种类的商品，比如颜色不同、价格不同的商品。

```
<form method="POST">
    <label>Item 1</label>
    <input type="text" name="item[0][name]" />
    <input type="text" name="item[0][description]" />
    <input type="text" name="sku[0]" />
    <input type="text" name="price[0]" />
    <button type="submit">Submit</button>

    <label>Item 2</label>
    <input type="text" name="item[1][name]" />
    <input type="text" name="item[1][description]" />
    <input type="text" name="sku[1]" />
    <input type="text" name="price[1]" />
    <button type="submit">Submit</button>

    <label>Item 3</label>
    <input type="text" name="item[2][name]" />
    <input type="text" name="item[2][description]" />
    <input type="text" name="sku[2]" />
    <input type="text" name="price[2]" />
    <button type="submit">Submit</button>
</form>

```

我们有三个迭代的项目需要验证。HTML 没有限制表单中可以提交的数组元素的数量，所以如果我们必须单独验证每个元素，这将是一个令人头疼的问题。

幸运的是，Laravel 提供了一种简单的方法，通过使用点符号和`*`字符来验证数组和嵌套数组输入。

```
public function rules()
{
    return [
        'item.*.name' => 'required|string|max:255',
        'item.*.description' => 'sometimes|nullable|string|min:60',
        'sku' => 'required|array',
        'sku.*' => 'sometimes|required|string|regex:​​/^[a-zA-Z0-9]+$/',
        'sku' => 'required|array',
        'price.*' => 'sometimes|required|numeric'
    ];
}

```

`*`字符替换数组中元素的迭代次数。当我们进行更复杂的嵌套时，这也非常有用。

假设我们有一个`months_available`字段，每个字段都是您可以选择的月份列表。无需为我们的深层嵌套属性命名，我们可以验证每个月数组和嵌套数组中的每个月，如下所示:

```
public function rules()
{
    return [
        'item.*.name' => 'required|string|max:255',
        'item.*.description' => 'sometimes|nullable|string|min:60',
        'sku' => 'required|array',
        'sku.*' => 'sometimes|required|string|regex:​​/^[a-zA-Z0-9]+$/',
        'sku' => 'required|array',
        'price.*' => 'sometimes|required|numeric',
        'months_available' => 'required|array',
        'months_available.*' => 'sometimes|required|array',
        'months_available.*.*' => 'sometimes|required|string',
    ];
}

```

如果我们要为每个属性编写自定义消息，我们将得到如下内容:

```
public function messages()
{
    return [
        'item.*.name.required' => 'You must have an item name.',
        'item.*.name.max' => 'The item name must not surpass 255 characters.',
        'item.*.description.min' => 'The description must have a minimum of 60 characters.',
        'sku.*.regex' => 'The SKU must only have alphanumeric characters.',
        'price.*.numeric' => 'You have invalid characters in the price field.'
    ];
}

```

## 数组验证的重要规则

既然您正在使用数组，那么有些规则尤其重要。我们将讨论其中的一些，并为每一个提供示例，以帮助您更好地理解它们。

### `array`

这确保了输入的值是一个数组。可以提供一个列表作为这个规则的上下文，告诉 Laravel 确保键出现在输入中。

```
public function rules()
    {
        return [
            'item' => 'array:name', // name must be present in input
        ];
    }
```

### `distinct`

这确保了数组中没有重复的元素。当您需要唯一的值(如 id)时，这很有用。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
public function rules()
    {
        return [
            'item.*.id' => 'distinct', 
        ];
    }
```

### `exclude_if, exclude_unless, exclude_without`

每个规则将当前字段与另一个字段进行比较，并根据条件将其从返回的数据中排除。`exclude_if`如果另一个字段等于某个值，则排除当前字段，`exclude_unless`除非另一个字段等于某个值，否则排除当前字段，`exclude_without` 如果另一个字段不存在，则排除当前字段。

```
public function rules()
    {
        return [
            'tag' => 'exclude_if:product_type,"digital"|required|array', // 
            'item_code' => 'exclude_unless:sku,null|required|array',
            'discount' => 'exclude_without:price|sometimes|array'
        ];
    }

```

### `required`

该规则确保当前字段存在并有数据，因此不能为空。

### `sometimes`

这将验证当前字段是否存在。当您验证数组中的嵌套值时，您会经常用到这一点，因为有时迭代的某个属性会丢失；即使存在其他属性。

这不是`required`的反义词，因为你可以一起使用它们。例如，因为数据可能有`item.5.name`，验证器可能期望有一个`item.5.description`。有了`sometimes`，它知道当它不存在时，它不必担心，也不会抛出令人讨厌的异常。

```
public function rules()
{
    return [
         ‘item.*.name’ => ‘required|string|max:255’,
                 ‘item.*.description’ => ‘sometimes|nullable|string|min:60’,
    ];
}

```

## 结论

尽管我们已经介绍了很多，但是在 Laravel 中验证嵌套数据还有更多的事情要做。它不仅提供了验证数据的方法，还为您提供了定制数据验证规则的方法。

库存系统并不是唯一将复杂的嵌套数据提供给后端进行验证的例子，因为一些具有多页表单的网站属于这一类别，还有允许用户使用可重复的模块和块来构建网页和其他数字项目的软件。

关于 Laravel 验证可以做的更多令人惊奇的事情，请参见 Laravel 文档。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)