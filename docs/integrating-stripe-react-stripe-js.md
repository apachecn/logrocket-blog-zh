# 将 Stripe 集成到 React 应用中

> 原文：<https://blog.logrocket.com/integrating-stripe-react-stripe-js/>

如今，通过网站或移动应用程序支付以换取某种产品已经变得无处不在。因此，有多种支付网关解决方案可以让这些网站和移动应用程序轻松接受支付。

Stripe 是领先的支付网关之一，它允许企业和个人通过使用其强大的 API 和工具来接受支付。在本文中，我们将讨论如何使用 Stripe 的官方库 React Stripe.js 将 Stripe 集成到我们的 React 应用程序中。

## 条纹元素

React Stripe.js 只是 Stripe 元素的一个轻量级包装器，Stripe 元素是一组预构建的 UI 组件，允许开发人员快速轻松地在他们的应用程序中实现安全支付功能。一个这样的元素是 Card 元素，它是一个单行表单，收集使用借记卡/信用卡进行在线支付所需的所有信息。

开发人员还可以使用表示卡元素各个部分的元素构建自己的自定义支付表单:卡号元素、卡到期元素等等。你可以在这里了解更多关于条纹元素[。](https://stripe.com/docs/stripe-js)

## React Stripe.js

现在我们已经熟悉了 Stripe 元素，我们将看看 React Stripe.js 如何允许我们在应用程序中使用元素。

在 React Stripe.js 中，元素以 React 组件的形式提供，所以对于每个元素，我们都有一个对应的 React 组件。除了元素组件之外，React Stripe.js 还包含一些钩子和其他组件，比如`Elements` provider 和`ElementsConsumer`。

在我们继续构建一个允许我们用 Stripe 和 React Stripe.js 接受支付的支付表单之前，我想简要讨论一下其中的一些钩子和组件，因为它们是我们在本文后面要做的事情的基础。

### `Elements`供应商

`Elements` provider 是一个允许嵌套在其中的任何组件使用 Element 组件的组件，React Stripe.js 提供的钩子`Elements`有两个道具:`stripe`和`options`。通常，`stripe`接受一个 Stripe 对象或一个解析为 Stripe 对象的承诺。要初始化条带对象，可以使用`stripe-js`模块中的`loadStripe`函数。

```
import React from "react";
import { loadStripe } from "@stripe/stripe-js";
import { Elements } from "@stripe/react-stripe-js";

const stripe = loadStripe(
  "{PUBLIC-KEY}"
);
const App = () => {
  return (
    <Elements stripe={stripe}>
      ...
    </Elements>
  );
};
```

### `useStripe`和`useElements`挂钩

`useStripe`和`useElements`钩子是 React Stripe.js 提供的两个 [React 钩子](https://reactjs.org/docs/hooks-intro.html)。`useStripe`返回我们最初传递给`Elements`提供者的 Stripe 对象实例的引用。有了这个 Stripe 对象，我们就可以和 Stripe 交互了。`useElements`返回一个 Elements 对象，我们可以用它来与我们的元素进行交互，并为支付收集敏感信息。

### `ElementsConsumer`

因为钩子只能在功能组件中使用，所以每当我们想要访问基于类的组件中的 Stripe 和 Element 实例时，就会使用`ElementsConsumer`组件。它接受一个函数，从它的参数中我们可以得到我们的 Stripe 和 Element 实例，就像一个孩子。

```
import React from "react";
import { ElementsConsumer, Elements } from "@stripe/react-stripe-js";
import { loadStripe } from "@stripe/stripe-js";

const App = () => {
  return (
    <Elements stripe={stripe}>
       <CheckoutForm />
    </Elements>
  );
};

const CheckoutForm = () => {
  return (
    <ElementsConsumer>
      {({elements, stripe}) => (
        ...
      )}
    </ElementsConsumer>
  );
};
```

现在我们已经熟悉了 React Stripe.js 提供的工具箱，让我们继续构建一个简单的 React 组件，它可以通过借记卡/信用卡接受支付。

## 使用 React Stripe.js 和 Stripe 接受付款

### 先决条件

1.  Stripe 帐户-这是将 Stripe 集成到您的应用程序或网站中所必需的。你可以在[Stripe.com](https://dashboard.stripe.com/register)创建一个
2.  React 的基础知识

> **注意**:要建立一个实际的支付流程，您需要一个服务器，在那里可以生成用于支付的客户秘密。这超出了本文的范围，不在讨论之列。

现在我们已经讨论过了，让我们开始写一些代码吧！

### 设置我们的 React 项目

首先，我们必须使用任何首选方法创建一个新的 React 项目。我推荐使用 create-react-app。

一旦我们的 React 项目被创建，我们必须安装一些依赖项。使用 npm，我们可以安装 React Stripe.js 和`stripe-js`模块:

```
npm install --save @stripe/react-stripe-js @stripe/stripe-js
```

### 接受付款

在我们的`App`组件中，我们将使用`loadStripe`函数初始化我们的 Stripe 对象。然后我们将把这个实例传递给`Elements`提供者的`stripe`道具。

为此，我们需要一个可发布的密钥，您可以在创建帐户后在您的 Stripe 仪表盘上找到它。请注意，您应该使用您的可发布密钥的测试版本。

一旦我们的`Elements`提供者准备好了，我们将为我们的结帐表单创建一个新的组件，从中我们将接受用户的卡细节。

我们将使用`CardElement`组件安全地收集用户的卡详细信息，并将其传递给 Stripe 进行处理。让我们为表单编写一些标记和 CSS。

#### `App.js`

```
import React, { useState } from "react";
import {
  Elements,
  CardElement,
} from "@stripe/react-stripe-js";
import { loadStripe } from "@stripe/stripe-js";
import "./App.css";
const App = () => {
  const stripe = loadStripe(
    "pk_test_*****"
  );
  return (
    <Elements stripe={stripe}>
      <CheckoutForm />
    </Elements>
  );
};
function CheckoutForm() {
  const [isPaymentLoading, setPaymentLoading] = useState(false);
  return (
    <div
      style={{
        padding: "3rem",
      }}
    >
      <div
        style={{
          maxWidth: "500px",
          margin: "0 auto",
        }}
      >
        <form
          style={{
            display: "block",
            width: "100%",
          }}
        >
          <div
            style={{
              display: "flex",
              flexDirection: "column",
              alignItems: "center",
            }}
          >
            <CardElement
              className="card"
              options={{
                style: {
                  base: {
                    backgroundColor: "white"
                  } 
                },
              }}
            />
            <button
              className="pay-button"
              disabled={isPaymentLoading}
            >
              {isPaymentLoading ? "Loading..." : "Pay"}
            </button>
          </div>
        </form>
      </div>
    </div>
  );
}
```

#### `App.css`

```
.card {
  background-color: white;
  padding: 10px 20px 11px;
  border-radius: 5px;
  width: 100%;
  border: 1px solid #afafaf;
  box-shadow: 0px 4px 5.5px 0px rgba(0, 0, 0, 0.07);
}
.pay-button{
  padding: 0.7rem 2rem;
  width: 100%;
  margin: 1rem 0;
  color: white;
  font-weight: bold;
  font-size: medium;
  background-color: #556cd6;
  border: 0;
  border-radius: 5px;
  box-shadow: 0px 4px 5.5px 0px rgba(0, 0, 0, 0.07);
  transition: box-shadow 500ms;
  cursor: pointer;
}
.pay-button:disabled{
  background-color: #afafaf;
  box-shadow: none;
  cursor: default;
}
.pay-button:disabled:hover{
  box-shadow: none;
}
.pay-button:hover{
  box-shadow: 2px 5px 15px 2px rgba(0, 0, 0, 0.2);
}
```

现在我们需要做的就是创建一个函数，我们称之为`payMoney`，用于收集用户的详细信息并将其发送给 Stripe 进行支付。要做到这一点，我们必须向服务器上的一个端点发出一个请求(同样，我们不会在本教程中讨论它)来获取客户端秘密，我们将用它来进行支付。

因为我们没有服务器来发出请求，所以我们将只使用一个函数来表示流程。

```
const clientSecret = getClientSecret();
```

我们现在将使用`useStripe`和`useElement`钩子来获取 stripe 和 element 实例，我们将使用它们通过`confirmCardPayment`方法进行支付。这个方法接受我们的客户端秘密和一个对象，该对象指定关于用户付款的附加细节。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
const stripe = useStripe();
const elements = useElements();

const paymentResult = await stripe.confirmCardPayment(clientSecret, {
      payment_method: {
        card: elements.getElement(CardElement),
        billing_details: {
          name: "Faruq Yusuff",
        },
      },
});
```

从上面的代码块中可以看到，我们使用了`elements`实例及其`getElement`方法，该方法将`CardElement`组件作为参数。我们还可以使用`billing_details`属性指定关于这次支付的其他细节。

如果我们的结果包含错误，我们将使用错误消息触发警报，如果结果成功，我们将使用成功消息触发警报。至此，我们的`payMoney`功能就完成了。

```
const payMoney = async (e) => {
    e.preventDefault();
    const clientSecret = getClientSecret();
    const paymentResult = await stripe.confirmCardPayment(clientSecret, {
      payment_method: {
        card: elements.getElement(CardElement),
        billing_details: {
          name: "Yusuff Faruq",
        },
      },
    });
    setPaymentLoading(false);
    if (paymentResult.error) {
      alert(paymentResult.error.message);
      console.log(paymentResult.error.message);
    } else {
      if (paymentResult.paymentIntent.status === "succeeded") {
        alert("Success!");
        console.log(paymentResult);
      }
    }
  };
```

现在这应该是我们完成的应用程序。

```
import React, { useState } from "react";
import {
  Elements,
  CardElement,
  useStripe,
  useElements,
} from "@stripe/react-stripe-js";
import { loadStripe } from "@stripe/stripe-js";
import "./App.css";
const App = () => {
  const stripe = loadStripe(
    "pk_test_***************"
  );
  return (
    <Elements stripe={stripe}>
      <CheckoutForm />
    </Elements>
  );
};
function CheckoutForm() {
  const [isPaymentLoading, setPaymentLoading] = useState(false);
  const stripe = useStripe();
  const elements = useElements();
  const payMoney = async (e) => {
    e.preventDefault();
    if (!stripe || !elements) {
      return;
    }
    setPaymentLoading(true);
    const clientSecret = getClientSecret();
    const paymentResult = await stripe.confirmCardPayment(clientSecret, {
      payment_method: {
        card: elements.getElement(CardElement),
        billing_details: {
          name: "Faruq Yusuff",
        },
      },
    });
    setPaymentLoading(false);
    if (paymentResult.error) {
      alert(paymentResult.error.message);
    } else {
      if (paymentResult.paymentIntent.status === "succeeded") {
        alert("Success!");
      }
    }
  };

  return (
    <div
      style={{
        padding: "3rem",
      }}
    >
      <div
        style={{
          maxWidth: "500px",
          margin: "0 auto",
        }}
      >
        <form
          style={{
            display: "block",
            width: "100%",
          }}
          onSubmit = {payMoney}
        >
          <div
            style={{
              display: "flex",
              flexDirection: "column",
              alignItems: "center",
            }}
          >
            <CardElement
              className="card"
              options={{
                style: {
                  base: {
                    backgroundColor: "white"
                  } 
                },
              }}
            />
            <button
              className="pay-button"
              disabled={isPaymentLoading}
            >
              {isPaymentLoading ? "Loading..." : "Pay"}
            </button>
          </div>
        </form>
      </div>
    </div>
  );
}
```

我们刚刚建立了一个简单的应用程序，可以接受使用条纹支付！

### 关于定制和样式的说明

我们可以在每个元素组件上使用`options`属性，或者对元素组件应用一个类名，并用 CSS 对组件进行样式化，来定制元素组件的样式。

## 结论

React Stripe.js 为我们在 React 应用中实现支付功能提供了一种快速安全的方式。你可以在这里了解更多关于 React Stripe.js 和 Stripe [的信息。](https://stripe.com/docs)

## [LogRocket](https://lp.logrocket.com/blg/ecommerce-signup) :看看用户为什么不完成你的电子商务流程中的一个步骤的技术和 UX 原因。

[![](img/d60d88871d85e76e0dcca90f4bbaf78c.png)](https://lp.logrocket.com/blg/ecommerce-signup)

LogRocket 就像是一个网络和移动应用程序和网站的 DVR，记录你的电子商务应用程序上发生的一切。LogRocket 没有猜测用户不转化的原因，而是主动揭示了阻止你转化的根本原因，比如 JavaScript 错误或死点击。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

开始主动监控您的电子商务应用程序— [免费试用](https://lp.logrocket.com/blg/ecommerce-signup)。