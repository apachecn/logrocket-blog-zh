# 信用卡表单 web 组件教程

> 原文：<https://blog.logrocket.com/credit-card-form-web-component-tutorial/>

作为开发人员，我们经常需要构建接受信用卡信息的表单，在每个网站上不断重复这些代码，修改它们以适应这里的 PHP 站点，那里的 React 站点，以及一秒钟也不能停止的奇怪的定制内容管理系统。

有一种方法可以将所有独立的信用卡字段打包到一个字段中，加上自己的错误更正，然后在投资组合中的每个网站上无缝使用，不需要进行任何修改。我们可以使用 web 组件来构建它。

Web 组件本质上是定制的 HTML 元素，可以在您必须支持的所有不同平台上工作。在本文中，我们将学习如何使用 web 组件来构建信用卡表单。

请注意，我们只关注前端问题，因此请确保加密和其他安全措施到位，以妥善保管用户委托给您的数据。

## 创建信用卡表单

首先，在将信用卡表单制作成 web 组件之前，让我们先把它放在一起。

我们首先需要四个主要字段:卡上的姓名、卡号、有效期和 CVV 代码。我们可以通过添加帐单地址和其他相关信息对此进行扩展，但出于本文的目的，我们将坚持使用这四个字段:

```
<form>
  <label>
    <span class="nmoncd">Name on Card</span>
    <input name="nameoncard" type="text" placeholder="Lucious B. Byinshtuff">
  </label>
  <label> 
    <span class="cdnum">Card Number <span>(Dashes not required)</span></span>
    <input name="cardnumber" type="text" placeholder="DONT-USEA-REAL-NMBR">
  </label>
  <label class="exp">
    <span class="expdt">Exp. Date</span>
    <input name="experationdate" type="text" placeholder="MM/YYYY">
  </label>
  <label class="cvc">
    <span class="cvcnum">CVC</span>
    <input name="cvcnumber" type="text" placeholder="XXX">
  </label>
  <button type="submit">
    Buy Now
  </button>
</form>
```

参见 [CodePen](https://codepen.io) 上的 [@undeadinstitute](https://codepen.io/undeadinstitute) )
笔[信用卡表单——香草](https://codepen.io/undeadinstitute/pen/MWvyeKY)。

## 为信用卡字段创建 web 组件

好了，现在让我们使用一个 web 组件将我们的四个信用卡字段放入一个单独的自定义 HTML 元素中。

我们称这个元素为`<credit-card>`(自治的 web 组件名称，也就是我们自己命名的名称，其中必须有一个连字符)。

任何 web 组件都有三个主要部分:HTML、CSS 和 JavaScript。HTML 是我们刚刚决定的元素名称，如果我们有任何想要包含的标准 HTML(我们确实这样做了)，我们必须将它添加到一个`<template>`元素中。

每当我们创建一个新的 web 组件实例时，我们还可以为我们想要更改的任何内容添加`<slot>`元素。然而，对于我们的特定目的，我们不需要任何插槽，因为用户将放入他们自己的信息，我们不需要区分实例之间的任何东西。

因此，模板包含了上面的四个字段，并带有一个包装器`div`，以确保布局在不同实例中保持一致。然后模板元素包装所有这些:

```
<template id="cc-template">
  <div class="wrapper">
    <label>
        <span class="nmoncd">Name on Card</span>
        <input name="nameoncard" type="text" placeholder="Lucious B. Byinshtuff">
    </label>
    <label> 
        <span class="cdnum">Card Number <span>(Dashes not required)</span></span>
        <input name="cardnumber" type="text" placeholder="DONT-USEA-REAL-NMBR">
     </label>
    <label class="exp">
        <span class="expdt">Exp. Date</span>
        <input name="experationdate" type="text" placeholder="MM/YYYY">
    </label>
    <label class="cvc">
        <span class="cvcnum">CVC</span>
        <input name="cvcnumber" type="text" placeholder="XXX">
    </label>
  </div>
</template>
```

### 创建自定义 JavaScript 元素

下一步是在 JavaScript 中设置新的定制元素。我们将扩展`HTMLElement`类，并赋予新元素内置元素的所有属性。然后，我们可以添加并扩展自定义元素的功能。

让我们首先获取我们设置的模板，并将其插入到名为 shadow DOM 的元素中，这是该元素的内部 DOM。我们将在构造函数中这样做(当我们创建元素并设置好一切时运行的函数):

```
customElements.define(
  "credit-card", //the name of the element we chose above
  class extends HTMLElement {
    constructor() {
      super();

      let cctemplate = document.getElementById("cc-template"); //grabs the template element

      const shadowRoot = this.attachShadow({ mode: "open" }).appendChild(
        cctemplate.content.cloneNode(true) //clones the template's HTML and attaches it to our new custom element
      );
    }
  }
);
```

参见 [CodePen](https://codepen.io) 上的 [@undeadinstitute](https://codepen.io/undeadinstitute) )
笔[信用卡表单 Web 组件](https://codepen.io/undeadinstitute/pen/zYdNKrv)。

因为`shadowDom`与完整的 DOM 有着千丝万缕的联系，它就像一个黑盒，不让 CSS 样式侵入它的领地。但是，这也意味着它不会让这些风格出来。

这样，您的 CSS 可以封装在元素中，并与 web 组件一起保存在您放置它的任何站点或上下文中。

但是，我们如何设置这些样式呢？我们在模板中使用了一个样式元素。只需将 CSS 放在模板的样式元素中，这将影响 web 组件的内容，但不会影响其他内容:

```
<template id="cc-template">
  <style>
    * {
      box-sizing: border-box;
    }
    input {
      padding: 0.6em 0.25em 0;
      font-size: 1.5em;
      height: 2.1em;
      margin-bottom: 0.25em;
      border: none;
      border-bottom: 1px solid peachpuff;
      font-family: Raleway, Helvetica, sans-serif;
      font-weight: 100;
      border-radius: 0.25em;
    }
    label span {
      font-size: 0.75em;
    }
    label {
      position: relative;
    }
    label>span {
      position: absolute;
      top: 0.25em;
      left: 0.25em;
    }
    .cdnum span {
      color: #555;
      font-size: 0.8em;
    }
    .cvc {
      width: 25%;
    }
    label,
    input {
      width: 100%;
    }
    .exp {
      width: calc(75% - 0.75em);
    }
    ::placeholder {
      color: #ccc;
      opacity: 1;
    }
    ::-ms-input-placeholder {
      color: #ccc;
    }
    input:focus {
      outline-color: #eb0062;
    }
    .wrapper {
      width: 100%;
      display: flex;
      flex-wrap: wrap;
      justify-content: space-between;
      align-items: center;
      background-color: #fff0f0;
    }
  </style>
  <!-- **The HTML code we already had in the template** -->
</template>
```

现在我们已经有了 web 组件，要使用它，我们必须做的就是在我们的代码中放置以下内容，然后我们的表单域就会出现:

```
<credit-card></credit-card>
```

## 表单关联的 web 组件

通过使用 web 组件使我们的字段成为一个元素，它们仍然没有以任何方式与表单挂钩。如果我们提交表单，它不会为我们的`<credit-card>`子字段提交任何内容。为了挂钩到表单的内置流程，我们必须使它成为一个与表单相关联的 web 组件。

到目前为止，所有浏览器都支持我们所做的一切，但不幸的是，Safari 还不支持与表单相关的 web 组件，在 Firefox 中的可用性也有限。幸运的是，Edge 和 Chrome 完全支持它们。([如果你需要的话，还有一个 polyfill。)](https://www.npmjs.com/package/element-internals-polyfill)

要使一个 web 组件与表单相关联，将一个`formAssociated`变量设置为`true`，并将一个`ElementInternals`对象附加到 web 组件，为我们提供实现表单参与所必需的实用程序。我们可以通过`attachInternals()`功能来实现:

```
customElements.define(
  "credit-card",
  class extends HTMLElement {
    static formAssociated = true; //shows our web component to be form-associated 
    constructor() {
      super();
      try {
        this.guts_ = this.attachInternals(); //attaches the ElementInternals object to the web component
      } catch {} //kept in a try/catch block so Safari doesn't choke
      let cctemplate = document.getElementById("cc-template");

      const shadowRoot = this.attachShadow({ mode: "open" }).appendChild(
        cctemplate.content.cloneNode(true)
      );
   }
});
```

既然`<credit-card>`是与表单相关联的，并且我们已经附加了`ElementInternals`，那么一旦表单被提交，我们需要一种方法来发送来自`<credit-card>`的四个子字段的值。

为此，我们必须首先使用`querySelector`函数从`shadowRoot`中选择每个输入，并使用每个输入的 name 属性来选择 CSS 元素。一旦我们有了每个输入，我们就可以创建一个事件侦听器来更新表单提交时将发送的内容。

我们将创建一个更新函数，更新每个子字段的值，这样当表单被提交时，最新的信息将被发送；我们就叫它`updateValue()`。

唯一的问题是，因为我们将在事件侦听器中的元素内，所以我们必须返回到自定义元素来运行更新函数。我们可以使用`getRootNode()`函数和主机属性来实现这一点:

```
constructor() {
    /**The rest of the constructor code removed for simplicity**/
      this.shadowRoot
        .querySelector('[name="nameoncard"]')
        .addEventListener("input", function (evt) {
          this.getRootNode().host.updateValue(); //call <credit-card>'s updateValue() function
        });
      this.shadowRoot.querySelector('[name="cardnumber"]')
        .addEventListener("input", function (evt) {
          this.getRootNode().host.updateValue(); //call <credit-card>'s updateValue() function
        });
      this.shadowRoot.querySelector(
        '[name="experationdate"]'
      ).addEventListener("input", function (evt) {
          this.getRootNode().host.updateValue(); //call <credit-card>'s updateValue() function
        });
      this.cvvnumberEl_ = this.shadowRoot.querySelector(
        '[name="cvvnumber"]'
      ).addEventListener("input", function (evt) {
          this.getRootNode().host.updateValue(); //call <credit-card>'s updateValue() function
        });

      this.updateValue(); //Run updateValue initially so that the fields will be sent in name/value pairs even if empty.
    }
    updateValue() {
      const ccinfo = new FormData(); //Since we're sending more than one field we need to use a FormData object.

    //Grab the current value of all fields
      this.nameoncard_ = this.shadowRoot.querySelector(
        '[name="nameoncard"]'
      ).value; 
      this.cardnumber_ = this.shadowRoot.querySelector(
        '[name="cardnumber"]'
      ).value;
      this.experationdate_ = this.shadowRoot.querySelector(
        '[name="experationdate"]'
      ).value;
      this.cvvnumber_ = this.shadowRoot.querySelector(
        '[name="cvvnumber"]'
      ).value;

    //Append the value of each field to the FormData object as a name/value pair. The first parameter is the name, the second is the value
      ccinfo.append("nameoncard", this.nameoncard_);
      ccinfo.append("cardnumber", this.cardnumber_);
      ccinfo.append("experationdate", this.experationdate_);
      ccinfo.append("cvvnumber", this.cvvnumber_);

      this.guts_.setFormValue(ccinfo); //Using one of the utilities we got from ElementInternals, setFormValue(), we'll set the <credit-card> field's value using the FormData object.
    }
  }
);
```

参见 [CodePen](https://codepen.io) 上 [@undeadinstitute](https://codepen.io/undeadinstitute) )
笔[信用卡表单-表单关联](https://codepen.io/undeadinstitute/pen/vYJgpgW)。

## Web 组件内容验证

我们可以添加到 web 组件中的另一件事是一些内容验证。我们必须确保用户可以轻松地向我们付款，因此纠正前端的一些常见错误可以使我们避免卡被拒和类似的容易修复的错误。

我们将使用`ElementInternals`中的`setValidity`函数，它允许我们标记不正确的内容，并在输入时显示错误消息。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

如果内容通过了我们的验证测试，我们将通过以下步骤解除警报:

```
this.guts_.setValidity({});
```

如果它没有通过我们的测试，我们将错误类型设置为`true`，给它一个错误消息，并且，因为我们有多个字段，所以将错误所在的字段传递给它:

```
this.guts_.setValidity(
          { typeMismatch: true }, //error type
          "Credit Card Number Invalid", //error message to show
          this.shadowRoot.querySelector('[name="cardnumber"]') //element on which to show error
        );
```

虽然你可能认为至少有一些简单的规则可以用来验证名字，比如至少包含一个空格或只使用字母字符，但名字并不真正符合标准，除非你在这个过程中有进一步的约束，否则我建议关闭纠错(可能除了[小鲍比表](https://xkcd.com/327/))。

信用卡号码不仅仅是随机数字，也不是所有的 16 位数字都是信用卡号码(美国运通使用 15 位数字，但我们暂时忽略它们)。

对于信用卡，前六位数字代表发卡行(如万事达、Visa、Discover)；接下来的九个是他们客户的账号；最后一个数字是校验和，它确保前 15 个数字是准确的，也就是说，没有数字被调换，也没有随机数被替换。

该校验和使用 Luhn 算法。有一些转置和替换通过了 Luhn 算法，但它捕捉到了绝大多数简单的错误:

```
if ( checkLuhn(this.cardnumber_) == this.cardnumber_[this.cardnumber_.length - 1] ) {
    //Checks if the last digit is equal to the results of the Luhn algorithm
        this.guts_.setValidity({});
      } else {
        this.guts_.setValidity(
          { typeMismatch: true },
          "Credit Card Number Invalid",
          this.shadowRoot.querySelector('[name="cardnumber"]')
        );
      }
```

在运行算法之前，`checkLuhn`函数还会删除任何非数字字符，比如连字符，所以我们不需要担心用户是否将它们放在字段中。

到期日期必须是有效的未来日期，这允许我们在此提供一些错误更正。

为了简单起见，我使用了`date.parse`，但是因为该函数严重依赖于实现，这意味着它对日期的解释可能依赖于浏览器和/或操作系统，所以我建议使用[日期库，以确保在不同的系统和浏览器上获得一致的答案](https://blog.logrocket.com/javascript-date-libraries/):

```
let expdate = this.experationdate_.split("/"), //separate the inputted month and year
        today = new Date(Date.now()); //grab today's date
      if (Date.parse(expdate[1] + "-" + expdate[0])) { //check if the inputted date is parsable as YYYY-MM (a format Date.parse() understands)
        if (today.getFullYear() < parseInt(expdate[1])) { //if the year is in the future, it's valid
          this.guts_.setValidity({});
        } else if (
          today.getFullYear() == parseInt(expdate[1]) && 
          today.getMonth() + 1 <= parseInt(expdate[0]) //if it's the current year and the month is current or in the future, it's valid
        ) {
          this.guts_.setValidity({});
        } else { //otherwise the month and year are in the past and the card is expired
          this.guts_.setValidity(
            { rangeUnderflow: true },
            "Card is Expired",
            this.shadowRoot.querySelector('[name="experationdate"]')
          );
        }
      } else { //the date wasn't parsable so we throw an error
        this.guts_.setValidity(
          { typeMismatch: true },
          "Invalid Expiration Date",
          this.shadowRoot.querySelector('[name="experationdate"]')
        );
      }
```

最后，CVV 代码必须至少是三位数(美国运通使用四位数，但我们将继续忽略这种边缘情况)。

[我们可以用 JavaScript](https://blog.logrocket.com/javascript-testing-best-practices/) 来测试，但是 HTML 也有内置的方法来使用`maxlength`属性和`pattern`属性来处理测试。记住，我们仍然必须进入`setValidity`来检查表单的输入是否符合模式:

对于 HTML 测试，请使用以下代码:

```
<input maxlength="3" pattern="[0-9][0-9][0-9]" name="cvvnumber" type="text" placeholder="XXX">
```

对于 JavaScript，使用以下代码:

```
 let pattern = this.shadowRoot
        .querySelector('[name="cvvnumber"]')
        .getAttribute("pattern"); //grab the contents of the pattern attribute
      let rgx = new RegExp(pattern); //create a Regular Expression object
      if (rgx.test(this.cvvnumber_)) { //Test whether the inputted value satisfies the pattern, if so it's valid
        this.guts_.setValidity({});
      } else {
        this.guts_.setValidity(
          { patternMismatch: true },
          "CVV code should be three numbers",
          this.shadowRoot.querySelector('[name="cvvnumber"]')
        );
      }
```

现在，上面的每个验证脚本都可以很好地独立工作，但是每当我们设置`this.guts_.setValidity({});`时，它就会清除所有的标志。

如果 CVV 代码验证是列表中的最后一个，并且是有效的，但到期日期或信用卡号不是，那么这些错误将被跳过，表单将通过。

我们可以单独重置错误标志，但是由于我们有多个`typeMismatch`错误，固定的到期日期将会消除信用卡号上的错误。

因此，我们将错误保存在一个数组中，并在最后一次性处理它们。如果我们没有收集到任何错误，内容是有效的，可以通过以下方式发送:

```
let errors = []; //create an array to hold errors

      this.cardnumber_ = this.shadowRoot.querySelector(
        '[name="cardnumber"]'
      ).value;

      if (
        checkLuhn(this.cardnumber_) !=
        this.cardnumber_[this.cardnumber_.length - 1]
      ) { 
        //Add the error to the array
        errors.push([
          { typeMismatch: true },
          "Credit Card Number Invalid",
          this.shadowRoot.querySelector('[name="cardnumber"]')
        ]);
      }

      this.experationdate_ = this.shadowRoot.querySelector(
        '[name="experationdate"]'
      ).value;
      let expdate = this.experationdate_.split("/"),
        today = new Date(Date.now());
      if (Date.parse(expdate[1] + "-" + expdate[0])) {
        if (today.getFullYear() < parseInt(expdate[1])) {
          //do nothing, field is valid
        } else if (
          today.getFullYear() == parseInt(expdate[1]) &&
          today.getMonth() + 1 <= parseInt(expdate[0])
        ) {
          //do nothing, field is valid
        } else {
          errors.push([
            { rangeUnderflow: true },
            "Card is Expired",
            this.shadowRoot.querySelector('[name="experationdate"]')
          ]);
        }
      } else {
        errors.push([
          { typeMismatch: true },
          "Invalid Expiration Date",
          this.shadowRoot.querySelector('[name="experationdate"]')
        ]);
      }

      this.cvvnumber_ = this.shadowRoot.querySelector(
        '[name="cvvnumber"]'
      ).value;

      let pattern = this.shadowRoot
        .querySelector('[name="cvvnumber"]')
        .getAttribute("pattern");
      let rgx = new RegExp(pattern);
      if (!this.cvvnumber_ || !rgx.test(this.cvvnumber_)) {
        errors.push([
          { patternMismatch: true },
          "CVV code should be Three Numbers",
          this.shadowRoot.querySelector('[name="cvvnumber"]')
        ]);
      }

    //If we have any errors, process them. If not, the input is valid and okay to go through.
      if (errors.length > 0) {
        for (let i = 0; i < errors.length; i++) {
          this.guts_.setValidity(errors[i][0], errors[i][1], errors[i][2]);
        }
      } else {
        this.guts_.setValidity({});
      }
```

参见 [CodePen](https://codepen.io) 上的 [笔信用卡表单——亡灵学院(](https://codepen.io/undeadinstitute/pen/porpqWy) [@undeadinstitute](https://codepen.io/undeadinstitute) )
验证。

结论

## 现在，您已经构建了一个完整的 web 组件，您可以按原样使用它，也可以对它进行修改。事实上，您可以做的验证工作比这里介绍的要多得多。快乐的 web 组件化！

使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

## [LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)

Focus on the bugs that matter — [try LogRocket today](https://lp.logrocket.com/blg/signup-issue-free).