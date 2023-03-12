# 比较 React 本地状态管理库

> 原文：<https://blog.logrocket.com/comparing-react-native-state-management-libraries/>

如果您必须开发一个应用程序，其中两个以上具有不同祖先的组件必须共享相同的状态，那么您会理解将属性传递给所有这些组件会很快变得混乱。状态管理是在我们的应用程序中管理这些数据的一种方式，从文本字段的值到表格中的行。

进入状态管理库，像 [Redux](https://blog.logrocket.com/redux-isnt-dead/) 。这些库旨在解决这个问题，但它们仍然不完美。事实是，完美的状态管理库并不存在。在选择一个时，有太多不同的因素需要考虑，比如你的应用程序的大小，你想要实现的目标，以及有多少状态被共享。

在本文中，我们将了解一些状态管理选项，以帮助您决定在 React 本机应用程序中使用哪些选项。我将把状态管理的开发者体验与 React Context API、Hookstate 和 Easy-Peasy 进行比较。

已经有太多关于像 Redux 这样受欢迎的州经理的文章了，所以我将讨论这些小文章来帮助你做出明智的决定。

## 先决条件

为了阅读本文，您应该具备以下条件:

在本文中，我将使用 Yarn，但是如果您更喜欢 npm，请确保用 npm 等价物替换命令。

## 设置演示应用程序

由于本文的性质，我们不会从头开始构建一个新的应用程序。因为我将只讨论如何比较这些库，所以我设置了一个演示应用程序，您可以跟随我演示它们的优缺点。

### 克隆回购

你可以在这个 Github repo 找到[我的演示应用。如果您在本地克隆它并安装必要的依赖项，您将会看到已经为我们将要讨论的每个库的示例创建了分支:](https://github.com/edmund1645-demos/comparing-rn-state-lib)

```
git clone https://github.com/edmund1645-demos/comparing-rn-state-lib

```

### 安装依赖项

将 repo 克隆到本地计算机后，使用您喜欢的软件包管理器安装依赖项:

```
npm install 
#or
yarn install

```

### 运行应用程序

你可以看看`main`分支，尤其是`App.js`文件，在我们实现状态管理之前，了解一下应用程序是如何构建的。

使用以下命令运行应用程序:

```
yarn ios #or npm 
#or
yarn android

```

## 使用 React 上下文 API 管理状态

我们将首先看一下[上下文](https://reactjs.org/docs/context.html) API。现在，我知道你在想什么:上下文 API 不是一个“独立”的库。虽然这是事实，但它仍然是一个值得考虑的选择。

克隆存储库并安装依赖项后，检查`context-example`分支:

```
git checkout context-example

```

现在看一下`contexts/CartContext.js`文件:

```
import React, { createContext } from 'react';
export const initialState = {
  size: 0,
  products: {},
};
export const CartContext = createContext(initialState);

```

[我们使用 React](https://blog.logrocket.com/use-hooks-and-context-not-react-and-redux/#:~:text=requires%20minimal%20code.-,What%20is%20the%20React%20Context%20API%3F,-The%20new%20Context) 的`createContext`方法创建一个上下文对象并导出它。我们还传入一个默认值。

在`App.js`中，我们首先导入`CartContext`对象和默认值`initialState`。

导入后，我们需要设置一个`useReducer`钩子来根据动作类型修改状态:

```
//  import context
import { CartContext, initialState } from './contexts/CartContext.js';

// reducer function
const reducer = (state, action) => {
  switch (action.type) {
    case 'ADD_TO_CART':
      if (!state.products[`item-${action.payload.id}`]) {
        return { size: (state.size += 1), products: { ...state.products, [`item-${action.payload.id}`]: { ...action.payload, quantity: 1 } } };
      } else {
        let productsCopy = { ...state.products };
        productsCopy[`item-${action.payload.id}`].quantity += 1;
        return { size: (state.size += 1), products: productsCopy };
      }
  }
};

export default function App() {
  // set up reducer hook
  const [state, dispatch] = useReducer(reducer, initialState);

  // create a Provider and use the return values from the reducer hook as the Provider's value
  return (
    <CartContext.Provider value={[state, dispatch]}>
      {/* other components */}
    </CartContext.Provider>
  )
}

```

当设置需要修改值的上下文时，就像我们的例子一样，我们需要使用一个 reducer 钩子。您会注意到，在上面的代码中，我们使用了提供程序中这个 reducer 钩子的值。这是因为`reducer`函数更新了状态，所以我们想让状态(以及修改它的函数)在我们所有的组件中都可用。

稍后，您将看到为什么子组件可以访问提供者的值，而不是创建上下文时使用的默认值。

接下来，看一下`components/ProductCard.jsx`文件:

```
import React, {useContext} from 'react'
import {CartContext} from '../contexts/CartContext'

const ProductCard = ({ product }) => {
  const [state, dispatch] = useContext(CartContext)

  function addToCart() {
    dispatch({type: 'ADD_TO_CART', payload: product})
  }
  return (
      {/* children */}
   )
}

```

为了访问创建购物车上下文时使用的值，我们需要导入它并将其传递给`useContext`钩子。

请注意，返回值是我们之前传递给提供者的数组，而不是创建上下文时使用的默认值。这是因为它使用树中匹配提供程序的值；如果树上没有`CartContext.Provider`，返回值将是`initialState`。

当点击 cart 按钮时，`addToCart`被调用，一个动作被分派给我们的 reducer 函数来更新状态。如果您再次查看`reducer`函数，您会注意到一个对象正在被返回；这个对象是新的状态。

每次我们调度一个动作，都会返回一个新的状态来更新这个大对象的一个属性。

让我们看看购物车屏幕(`screens/Cart.jsx`):

```
import React, {useContext} from 'react'
import { CartContext } from '../contexts/CartContext'

const Cart = () => {
  const [state, dispatch] = useContext(CartContext)
  return (
    {/* children */
  )
}

```

这里我们使用与`ProductCard.jsx`相同的模式，只是这次我们只使用`state`来呈现购物车中的商品。

## 使用上下文 API 和`useReducer`的优点

*   小型项目的理想选择
*   不影响包的大小

## 与`useReducer`一起使用上下文 API 的缺点

*   更新大型对象会很快变得混乱
*   可能不适合大型项目，因为如果需要的话，您需要在树上堆叠多个提供者

# 用 Hookstate 管理状态

Hookstate 提供了一种不同的状态管理方法。它对于小型应用程序足够简单，对于相对较大的应用程序足够灵活。

结账`hookstate-example`分支:

```
git checkout hookstate-example

```

对于 Hookstate，我们在`state/Cart.js`中使用了[全局状态](https://hookstate.js.org/docs/global-state)的概念。这个库导出了两个函数:`createState`通过在默认状态周围包装一些属性和方法来创建一个新的状态，并返回它，`useState`使用从`createState`或另一个`useState`返回的状态。

```
import { createState, useState } from '@hookstate/core';

const cartState = createState({
  size: 0,
  products: {},
});

export const useGlobalState = () => {
  const cart = useState(cartState);
  return {
    get: () => cart.value,
    addToCart: (product) => {
      if (cart.products[`item-${product.id}`].value) {
        cart.products[`item-${product.id}`].merge({ quantity: cart.products[`item-${product.id}`].quantity.value + 1 });
        cart.size.set(cart.size.value + 1);
      } else {
        cart.products.merge({ [`item-${product.id}`]: { ...product, quantity: 1 } });
        cart.size.set(cart.size.value + 1);
      }
    },
  };
};

```

利用 Hookstate 的结构化方式，我们还可以导出一个助手函数，用于与状态内部的组件进行交互。

我们需要做的就是导入`useGlobalState`，在一个功能组件中调用它，并从返回的对象中析构任何方法(取决于我们想要实现什么)。

这里有一个我们如何在`components/ProductCard.jsx`中使用`addToCart`方法的例子:

```
import { useGlobalState } from '../state/Cart';
const ProductCard = ({ product }) => {
  // invoke the function to return the object
  const state = useGlobalState()

  function addToCart() {
  // pass the product and let the helper function deal with the rest
    state.addToCart(product)
  }

  return (
    {/* products */}
  )
}

```

并且在`/screens/Cart.js`的`Cart`页面上:

```
import { useGlobalState } from '../state/Cart';
const Cart = () => {
  const {products} = useGlobalState().get()
  return (
    {/* render every item from the cart here */}
  )
}

```

Hookstate 最好的部分是，全局状态中的每个属性或方法(嵌套的和顶层的)都是一种状态，并且有各种[方法](https://hookstate.js.org/docs/typedoc-hookstate-core#interfacesstatemethodsmd)来直接修改自身。它的反应能力足以更新应用程序中所有组件的状态。

## 使用 Hookstate 的优点

*   轻松完成工作的 API
*   表演
*   大量的[扩展](https://hookstate.js.org/docs/extensions-overview)来创建更多功能丰富的应用程序
*   全类型系统

## 使用 Hookstate 的缺点

我知道我说过没有任何“完美”的替代方案，但看起来 Hookstate 试图否定我的理论。然而，有一个可以忽略的因素需要考虑:Hookstate 并不太知名——它在 [npm](https://www.npmjs.com/package/@hookstate/core) 上的每周下载量约为 3000 次，因此它周围的社区可能很小。

# 轻松管理国家

Easy-Peasy 是 Redux 的一个抽象，旨在公开一个简单的 API，它极大地改善了开发人员的体验，同时保留了 Redux 提供的所有好处。

我注意到使用 Easy-Peasy 就像使用上面两个例子的组合，因为您必须将整个应用程序包装在一个提供者周围(不要担心，您只需要这样做一次，除非您想要模块化状态)。

要轻松导入，请将以下内容复制到`App.js`:

```
import { StoreProvider } from 'easy-peasy';
import cartStore from './state/cart';

export default function App() {

  return (
     <>
      <StoreProvider store={cartStore}>
        {/* children */}
      </StoreProvider>
    </>
  );

```

您可以从库中导入钩子来挑选出您在组件中需要的全局状态的特定部分。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

我们来看看`/state/Cart.js`:

```
import { createStore, action } from 'easy-peasy';
export default createStore({
  size: 0,
  products: {},
  addProductToCart: action((state, payload) => {
    if (state.products[`item-${payload.id}`]) {
      state.products[`item-${payload.id}`].quantity += 1;
      state.size += 1;
    } else {
      state.products[`item-${payload.id}`] = { ...payload, quantity: 1 };
      state.size += 1;
    }
  }),
});

```

我们用`createStore`来建立一个全球商店。传递的对象称为“模型”。在定义模型时，我们还可以包括像[动作](https://easy-peasy.vercel.app/docs/api/action.html)这样的属性。动作允许我们更新商店中的状态。

在`components/ProductCard.jsx`中，我们想要使用`addProductToCart`动作，所以我们使用 Easy-Peasy 的`useStoreActions`钩子:

```
import React from 'react';
import { useStoreActions } from 'easy-peasy';

const ProductCard = ({ product }) => {
  const addProductToCart = useStoreActions((actions)=> actions.addProductToCart)

  function addToCart() {
    addProductToCart(product)
  }
  return (
    {/* children */}
  )
}

```

如果我们想在组件中使用状态，我们使用`useStoreState`钩子，如`screens/Cart.jsx`所示:

```
import React from 'react';
import { useStoreState } from 'easy-peasy';

const Cart = () => {
  const products = useStoreState((state)=> state.products)
  return (
    {/* children */}
  )
}

```

## 使用 Easy-Peasy 的优点

*   完全反应
*   基于 Redux 构建，因此支持 Redux 开发工具等等
*   简易 API

## 使用 Easy-peay 的缺点

*   增加束尺寸。如果这对你来说很重要，Easy-Peasy 可能不是你理想的图书馆

# 结论

在本文中，我们比较了带有 hooks、Hookstate 和 Easy-Peasy 的上下文 API。

总的来说，在演示项目中使用带钩子的上下文 API 是理想的，但是当应用程序的规模开始增长时，维护起来就变得很困难了。这就是 Hookstate 和 Easy-peas 大放异彩之处。

Hookstate 和 Easy-Peasy 都公开了管理状态的简单 API，并且以独特的方式执行。Easy-Peasy 构建于 Redux 之上，因此您可以获得这些额外的好处，而且 Hookstate 有一套用于在您的应用程序中实现特性的扩展，比如状态的本地存储持久性。

由于篇幅原因，本文中没有提到许多备选方案，因此这里有一些值得一提的内容:

如果你想检查每个例子的代码，你可以在这里找到这个项目的库[。](https://github.com/edmund1645-demos/comparing-rn-state-lib)

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)