# 用 Formik 和 React Query 构建多步向导

> 原文：<https://blog.logrocket.com/building-multi-step-wizards-with-formik-and-react-query/>

## 介绍

表单可能是当今网络上最普遍的元素。如果没有一个或多个表格，你很难找到关键的应用程序。

它们在金融应用中尤其重要，在这些应用中，转换成功率可能是公司意外收获和惨淡数字之间的差异。根据转换权威 [SaleCycle](https://salecycle.com) , [26%的人在结账时因为漫长或复杂的过程而放弃购买](https://blog.salecycle.com/strategies/infographic-people-abandon-shopping-carts/)。那伤害了任何公司的底线！

为了从一个表单中获得最大的影响，最好保持它的简单。我们可以通过使用亚当·席尔瓦推广的“每页一件事模式”理论来实现这一点，并且我们可以通过使用多步向导来轻松实现这一点。

## 什么是多步向导？

多步骤向导是一种解决方案，允许将复杂的挑战分解为实现目标所需的一系列步骤。有时，分支条件逻辑用于缩小用户选择的路径。

多步骤向导允许我们通过一个重要的因素消除认知超载，并提高用户在填写表单时对其要求的理解。

接下来，我们将看看 Formik，看看它为什么如此适合 React 中的表单管理。

## 什么是福米克？

Formik 是一个小型的库，帮助简化表单管理。

[根据库的创建者贾里德·帕尔默](https://formik.org/docs/overview)的说法，Formik 的创建是为了通过表单标准化数据流，同时保持对输入组件的外观和感觉的最小影响。

> 我对 Formik 的目标是用最少的 API 创建一个可伸缩的、高性能的表单助手，它可以做一些非常烦人的事情，剩下的就交给你了。–[贾里德·帕尔默](https://twitter.com/jaredpalmer)

Formik 的优势在于:

*   允许声明式表单管理，同时抽象出与 React 中的表单相关的平凡且重复的目标
*   保持表单的熟悉和直观。如果您知道一点 React 并了解表单，那么您可以使用 Formik
*   对您当前的状态管理解决方案不做任何假设。你可以使用 Redux 或者 MobX，这两个软件都可以很好的运行

## 为什么要对多步向导使用 Formik？

Formik 非常适合构建多步向导，因为我们可以保持表单状态本地化并由 Formik 管理。

Formik 允许我们维护单一的表单管理解决方案，具有以下优势:

*   Formik 有很好的文档记录，因此很容易被引用
*   在构建应用程序时，维护单一上下文变得更加容易
*   利用构建 HTML 表单时已经采用的许多实践，使 Formik 表单感觉自然，不那么做作

现在，让我们看一个在前端管理服务器状态的解决方案。

## React 查询简介

[React Query](https://react-query.tanstack.com/) 是一个 React 库，它使获取、缓存、同步和更新 React 应用程序中的服务器状态变得轻而易举。

React Query 作为一种解决方案而存在，因为在客户机上管理服务器端状态是一个众所周知的难题。通常，您必须提供:

*   易于缓存失效的适当缓存解决方案([众所周知的困难](https://quotesondesign.com/phil-karlton/)，根据 Phil Karlton 的说法)
*   重复数据删除机制，用于识别重复数据并将它们合并到一个请求中，以节省多个请求的开销
*   将数据记忆和规范化为一种状态，以便以一致的方式更容易访问

## 为什么使用 React Query？

React Query 是管理服务器端状态的最佳库之一，它有很多好处。它可以帮助:

*   减少发出琐碎请求所需的代码行数
*   通过节省带宽和内存，让您的应用程序感觉性能更高
*   充当远程调用的解耦层，因为您可以发出 REST/GraphQL 请求——React Query 将毫不费力地处理这两种用例

React Query 对我特别有帮助，就像过去一样，因为我必须进行 HTTP 调用，并使用 Paul Armstrong 的便利包`normalizr`和 Redux 选择器作为缓存解决方案。对我来说，这通常涉及大量的样板文件和复杂性。

为了更好地说明这些好处，我们将使用 Formik 和 React Query 构建一个小型的概念验证应用程序。

## 创建概念加密组合管理向导

我们将创建一个小的演示 React 应用程序，允许用户添加加密货币并指定第三方加密市场(如罗宾汉和比特币基地)，他们希望在那里管理他们的投资组合。

然后，用户可以为各自选择的市场提供 API 令牌。

## 先决条件

*   您已经安装了节点(版本 6 及更高版本)和 NPM
*   您对 React 有中级到高级的理解

## 设置 React 项目

首先，创建一个新的 React 项目并启动开发服务器:

```
npx create-react-app crypto-portfolio
cd crypto-portfolio
npm start

```

我们需要为路由、样式模块、表单管理和 HTTP 请求安装依赖项。

```
npm install react-router-dom node-sass formik axios react-query classnames --save

```

我个人的偏好是使用绝对进口的 T2，因为我觉得它们更干净，也更容易解释。您可以通过在根目录下创建一个包含以下内容的`jsconfig.json`文件来添加绝对导入:

```
{
    "compilerOptions": {
      "baseUrl": "src"
    },
    "include": ["src"]
}

```

为了适当的可访问性，我们需要让每个向导屏幕包含在它自己的路径中。我们将采用嵌套路由系统，允许我们通过 URL 反映语义关系。

我们将更新`src/index.js`文件来注册我们的路由器:

```
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import AppRouter from 'components/AppRouter';

ReactDOM.render(
  <React.StrictMode>
    <AppRouter />
  </React.StrictMode>,
  document.getElementById('root')
);

```

对于我们的顶级路线，让我们定义一个`AppRouter`组件，它将帮助我们建立一个用于投资组合创建的顶级路线。在`src/components`创建一个`AppRouter.js`文件:

```
import React, { Suspense, lazy } from "react"
import { BrowserRouter, Route, Switch } from "react-router-dom";
import NotFound from 'screens/NotFound'

const Portfolio = lazy(() => import("screens/Portfolio"))

function AppRouter() {
  return (
    <BrowserRouter>
      <Suspense fallback={<NotFound />}>
        <Switch>
          <Route component={Portfolio} path="/portfolio" />
          <Route component={NotFound} />
        </Switch>
      </Suspense>
    </BrowserRouter>
  );
}

export default AppRouter;

```

这里，我们利用`Suspense`来延迟加载我们的路由组件，以获得更好的性能。我们还引用了`NotFound`和`Portfolio`组件，我们将很快创建它们。`NotFound`组件是一个显示简单错误消息的普通组件。

创建`src/screens/NotFound.js`并填写:

```
function NotFound() {
    return (
        <div>
            <h2>Page not found</h2>
        </div>
    )
}

export default NotFound

```

我们的`Portfolio`组件提供了一个挂载点来引入`Formik`提供者。我们设置 Formik 提供程序，为表单提供默认值，并为每个向导屏幕组装路线。我们还定义了一些重定向来处理来自用户的可能不完整的 URL。

我们将从我们的`Portfolio.module.scss` CSS 模块导入一些样式。我们还打算创建一个路由数组列表，它允许我们定义关于向导中的步骤的信息。

我们计划使用一个定制的`StepsRouter`组件来呈现路线和一个`Steps`指示器，这样用户就可以看到他们的进度。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

让我们在`src/screens/Portfolio`创建一个`Portfolio.js`文件并添加一些代码。

```
import React, { Suspense } from "react"
import { BrowserRouter, Redirect, Route, Switch } from "react-router-dom";
import { Formik } from "formik";
import styles from './Portfolio.module.scss'
import stepsComposer from './steps'
import StepsRouter from 'components/StepsRouter'
import NotFound from 'screens/NotFound'

const initialFormValues = {
    portfolioName: '',
    marketplaces: {}
}

function Portfolio({ match: { url } }) {
    return (
        <div className={styles.Portfolio__Container}>
            <Formik initialValues={initialFormValues}>
                <BrowserRouter>
                    <Suspense fallback={<NotFound />}>
                        <Switch>
                            <StepsRouter steps={stepsComposer({ url })} />
                            <Route exact path={`${url}/create`} render={() => <Redirect to={`${url}/create/add-name`} />} />
                            <Route path="/" render={() => <Redirect to={`${url}/create`} />} />
                            <Route component={NotFound} />
                        </Switch>
                    </Suspense>
                </BrowserRouter>
            </Formik>
        </div>
    )
}
export default Portfolio

```

## 设置增强型定制路由器

让我们在`src/screens/Portfolio`的`steps.js`文件中定义向导的步骤。我们将提供关于路线的信息——主要是我们打算呈现的组件、它的路径以及它在步骤指示器上的标签。

```
import { lazy } from "react"

const AddName = lazy(() => import("./screens/AddName"))
const SelectCrypto = lazy(() => import("./screens/SelectCrypto"))
const AddKeys = lazy(() => import("./screens/AddKeys"))

const stepsComposer = ({ url }) => [
    {
        path: `${url}/create/add-name`,
        Component: AddName,
        label: "Add Name"
    },
    {
        path: `${url}/create/select-crypto`,
        Component: SelectCrypto,
        label: "Select Crypto"
    },
    {
        path: `${url}/create/add-keys`,
        Component: AddKeys,
        label: "Add Keys"
    }
]

export default stepsComposer

```

有了我们的步骤列表，我们需要创建我们的`StepsRouter`组件。我们的`StepsRouter`组件将获取我们之前列出的步骤列表，并呈现每一个步骤。

让我们也定义一个`EnhancedRoute`组件，它将接收一个额外的道具，这对我们以后的工作很有用。

我们将在`src/components/StepsRouter`创建`StepsRouter.js`文件。

```
import { Route } from "react-router-dom";

import EnhancedRoute from "./EnhancedRoute";

const StepsRouter = ({ steps }) => {
    return (
        <>
            {steps.map((step, index) => (
                <Route
                    path={step.path}
                    key={index}
                    render={props => <EnhancedRoute {...props} step={step} steps={steps} />}
                />
            ))}
        </>
    );
}

export default StepsRouter;

```

现在我们需要定义负责增强传递给向导屏幕路由的`props`的`EnhancedRoute`组件。我们将在前一个目录下创建`EnhancedRouter.js`。

```
import { generateStepsList } from "utils";

function EnhancedRoute ({ step, steps, ...props }) {
    const { match, location: { pathname } } = props;
    const { Component } = step
    const stepsList = steps.map(generateStepsList({
      route: { pathname, ...match },
    }));
    return (
        <Component {...props} stepsList={stepsList} />
    )
}

export default EnhancedRoute

```

注意我们定义了一个`generateStepsList`实用函数，所以我们需要在`src`目录下创建`utils.js`文件。该实用程序将返回路由的完全解析路径。

```
/**
 * Generates a list of steps for display by the steps component.
 *
 * This function returns a function that accepts a step object and returns
 * the object with extra properties.
 *
 * @param {Object} route
 * @returns {Function}
 */
export function generateStepsList({ route }) {
    const routeParams = route.params
    const routeParamKeys = Object.keys(routeParams)
    // Swap out any dynamic routes with their param values so "/portfolio/:portfolioId" becomes "/portfolio/1"
    const replaceParams = (path, param) => path.replace(`:${param}`, routeParams[param]);
    const createStepEntry = ({ path, label }) => {
        let routePath = routeParamKeys.length ? routeParamKeys.reduce(replaceParams, path) : path
        let active = route.pathname === path
        return {
            path: routePath,
            active,
            label
        };
    }
    return createStepEntry;
  }

```

现在我们需要在`src/components/Steps`创建`Steps.js`组件。该组件将显示并为我们提供可视化的进度更新。

我们将使用`classnames`包来帮助我们管理用于样式化指示器的条件语句，并且我们将存储关于`active step`、`last step`和`completed steps`的信息。

```
import React from "react";
import { Link } from "react-router-dom";
import styles from "./Steps.module.scss";
import classNames from "classnames/bind";

let cx = classNames.bind(styles);

function Steps({ steps }) {
  const activeIdx = steps.reduce((acc, curr, idx) => curr.active ? idx: acc, 0)

  return (
    <div className={styles.Steps__Wrapper}>
      <ol className={styles.Steps}>
        {steps.map(({ label, path, active }, idx) => {
          const isLastStep = idx === steps.length - 1;
          const completed = idx < activeIdx
          const stepWidth = 100 / (steps.length);
          let stepClasses = cx({
            Step: true,
            Step__Last: isLastStep,
            Step__Current: active,
            Step__Completed: completed
          });
          const checkmarkClass = completed ? 'checkmark-primary': 'checkmark-default'
          return (
            <li className={stepClasses} style={{ left: `${stepWidth/2}%` }} key={idx}>
              <Link to={path} className={styles.Step__Milestone}>
                <i className={`checkmark ${checkmarkClass}`} />
              </Link>
              {active ? (
                <span className={styles.Step__Label}>{label}</span>
              ) : (
                <Link to={path} className={styles.Step__Label}>
                  {label}
                </Link>
              )}
            </li>
          );
        })}
      </ol>
    </div>
  );
}

Steps.defaultProps = {
  steps: [],
};

export default Steps;

```

我们还需要为`Steps`组件定义样式。让我们创建`src/components/Steps/Steps.module.scss`并添加以下内容:

```
$secondary: #0056D8;
$light-gray: #F0F1F3;
$gray: #CCC;
$pale-blue: #D3E0F2;

.Steps__Wrapper {
    display: block;
  }

  .Steps {
    position: relative;
    display: flex;
    padding: 0;
    list-style: none;
    width: inherit;
  }

  .Step {
    position: relative;
    font-size: 12px;
    flex: 1;
  }

  .Step__Label {
    color: $gray;
  }

  .Step {
    &::before {
      content: "";
      width: 100%;
      height: 8px;
      background-color: $gray;
      position: absolute;
    }

    &__Milestone {
      width: 28px;
      height: 28px;
      border-radius: 100%;
      display: flex;
      align-items: center;
      justify-content: center;
      background: $light-gray;
      border: 2px solid $gray;
      position: relative;
      bottom: 10px;
      position: relative;
    }
  }

  .Step__Completed {
    &::before {
      background: $secondary;
    }
    .Step__Milestone {
      background: $pale-blue;
      color: $secondary;
      border-color: $secondary;
    }
  }

  .Step__Completed .Step__Label,
  .Step__Current .Step__Label {
    color: $secondary;
  }

  .Step__Current {
    .Step__Milestone {
      background: $secondary;
      border-color: $secondary;
    }
    .Step__Label {
      font-weight: bolder;
    }
  }

  .Step__Last::before {
    content: "";
    display: none;
  }

```

我们还将在`src/index.css`定义一些全局样式。你可以[看看这里](https://github.com/creatrixity/crypto-portfolio/blob/master/src/index.css)的款式。

此时，您应该有一个如下所示的可视指示器:

![Visual Steps Indicator](img/71bbc9e1c418e7058e780a035aaf87e5.png)

## 创建`AddName`屏幕

我们现在可以创建我们的第一个屏幕:`AddName`屏幕。在`src/screens/Portfolio/screens`创建`AddName.js`。

```
import { Field } from "formik";
import { Link } from "react-router-dom";
import Steps from "components/Steps";
import styles from 'screens/Portfolio/Portfolio.module.scss'

function AddName({ stepsList, ...props }) {
    return (
        <div>
            <section className={styles.Section__Heading}>
                <h2 className={styles.Section__Heading__Title}>Give your portfolio a name.</h2>
                <p>A simple name to identify your portfolio by.</p>
            </section>
            <div className={styles.Steps__Container}>
                <Steps steps={stepsList} />
            </div>
            <section className={styles.Form__Card}>
                <label className={styles.Form__Label}>Portfolio Name</label>
                <Field
                  type="text"
                  name="portfolioName"
                  className={styles.Form__Field}
                />
                <div className={styles.Form__Button__Container}>
                    <Link to={`/portfolio`} className="btn">
                        <span className="chevron left" />
                        Back
                    </Link>
                    <Link to={`/portfolio/create/select-crypto`} className="btn btn-primary">
                        Select Crypto
                        <span className="chevron right" />
                  </Link>
                </div>
            </section>
        </div>
    )
}

export default AddName

```

我们的`AddName`屏幕应该是这样的:

![Add Portfolio Name Screen](img/a8c3dfc4fba06b6312ac496362ad2c1b.png)

## 设置模拟 JSON API

对于我们的下一个屏幕，我们需要运行一个模拟 JSON 服务器，我们将使用它来获取与它们相关的加密和市场列表。

首先，运行`npm install -g json-server`来安装我们将用于模仿的`json-server`包。

接下来，在一个新目录下创建一个`crypto.json`文件，我们将在根目录下命名为`mocks`。我们将向文件中添加一些种子数据。

```
{
    "crypto": [
        {
            "id": 1,
            "label": "Bitcoin",
            "name": "bitcoin",
            "marketplaces": [
                {"id":1, "label": "Robinhood", "brand_url": "http://localhost:3000/brands/robinhood.svg", "name":"robinhood"},
                {"id":2, "label": "Coinbase", "brand_url": "http://localhost:3000/brands/coinbase.svg", "name":"coinbase"}
            ]
        },
        {
            "id": 2,
            "label": "Ethereum",
            "name": "ethereum",
            "marketplaces": [
                {"id":1, "label": "Robinhood", "brand_url": "http://localhost:3000/brands/robinhood.svg", "name":"robinhood"},
                {"id":2, "label": "Coinbase", "brand_url": "http://localhost:3000/brands/coinbase.svg", "name":"coinbase"}
            ]
        }
    ],

    "marketplaces": [
        {"id":1, "label": "Robinhood", "brand_url": "http://localhost:3000/brands/robinhood.svg", "name":"robinhood"},
        {"id":2, "label": "Coinbase", "brand_url": "http://localhost:3000/brands/coinbase.svg", "name":"coinbase"}
    ]
}

```

我们将通过访问 shell 并运行下面的命令来启动 JSON 服务器，这使得来自 mock 的数据在端口`3001`可用。

```
json-server --watch mocks/data.json --port 3001

```

## 构建选择加密屏幕

我们需要为加密选择屏幕添加几个专门的表单控件。为了节省时间，我预先构建了一个`CheckboxCard`和一个`CheckboxRound`组件。如果你愿意，你可以阅读`[CheckboxCard](https://github.com/creatrixity/crypto-portfolio/blob/master/src/components/CheckboxCard/CheckboxCard.js)`和`[CheckboxRound](https://github.com/creatrixity/crypto-portfolio/blob/master/src/components/CheckboxRound/CheckboxRound.js)`的源代码。

我们现在可以创建`SelectCrypto`屏幕。我们将在这个屏幕中做很多事情，因为我们需要定义一个新的`CryptoField`组件来显示加密市场信息。当组件安装时，我们将预先选择所有的加密市场，这样用户可以更容易地消除选项。

对于这个屏幕，我们将使用`Formik`上下文挂钩，它允许我们获取和设置表单的值。我们也用`React-Query`来取。

我定义了实用函数来帮助我们的 HTTP 请求管理，并处理错误/成功响应。更新`src/utils`并添加以下代码:

```
import axiosInstance from 'axios'

const REACT_APP_API_BASE_URL = "http://localhost:3001"

export const axios = axiosInstance.create({
    baseURL: REACT_APP_API_BASE_URL,
});

export function handleResponse(response) {
    if (response.data) {
      return Promise.resolve(response.data);
    }

    return Promise.resolve(response);
}

export function handleError(error) {
    if (error.data) {
        return Promise.reject(error.data);
    }

    return Promise.reject(error);
}

export const fetchAll = (resource) => axios.get(resource).then(handleResponse).catch(handleError);

```

作为开发过程的一部分，我们将遍历获取的加密货币列表，并为每个条目呈现一个`CryptoField`组件。

在`src/screens/Portfolio/screens`处创建`SelectCrypto.js`，并添加以下内容:

```
import { useEffect } from 'react'
import { useQuery } from "react-query";
import { Link } from "react-router-dom";
import { useFormikContext } from "formik";

import Steps from "components/Steps";
import CryptoField from "components/CryptoField";
import styles from 'screens/Portfolio/Portfolio.module.scss'
import { fetchAll } from 'utils'

function SelectCrypto({ stepsList }) {
    const { values, setFieldValue } = useFormikContext();
    const { isLoading, error, data: cryptocurrencies } = useQuery(
        "cryptocurrencies",
        () => fetchAll("/crypto")
    );

    useEffect(() => {
        // Prefill all marketplaces by reducing the cryptocurrencies list
        if (!Object.keys(values.marketplaces).length) {
          let marketplaces = cryptocurrencies.reduce((acc, curr) => {
            return {
              ...acc,
              [curr.id]: curr.marketplaces.map(({ id }) => id),
            };
          }, {});

          // Use the context hook to replace the marketplaces object
          setFieldValue("marketplaces", marketplaces);
        }
    }, [cryptocurrencies, setFieldValue, values.marketplaces]);

    if (isLoading) return <p>Loading...</p>
    if (error) return <p>Error</p>

    return (
        <div>
            <section className={styles.Section__Heading}>
                <h2 className={styles.Section__Heading__Title}>Select the crypto you wish to manage</h2>
                <p>Pick cryptocurrencies and choose exchanges to trade them on.</p>
            </section>
            <div className={styles.Steps__Container}>
                <Steps steps={stepsList} />
            </div>
            <section className={styles.Form__Card}>
                {cryptocurrencies.map((crypto, idx) => (
                    <div className="mb-2" key={idx}>
                        <CryptoField
                            crypto={crypto}
                            selectedMarketplaces={values.marketplaces[crypto.id] || []}
                        />
                    </div>
                ))}
                <div className={styles.Form__Button__Container}>
                    <Link to={`/portfolio/create`} className="btn">
                        <span className="chevron left" />
                        Back
                    </Link>
                    <Link to={`/portfolio/create/add-keys`} className="btn btn-primary">
                        Add Keys
                        <span className="chevron right" />
                  </Link>
                </div>
            </section>
        </div>
    )
}
export default SelectCrypto

```

### 创建`CryptoField`组件

`CryptoField`组件显示一种加密货币和与之相关的市场。

它接收以下内容作为`props`:

*   一个包含用户要选择的市场列表的`crypto`对象
*   一个包含每个所选市场的`id`的`selectedMarketplaces`数组

让我们在`src/components/CryptoField`创建`CryptoField.js`文件并添加一些代码。目前，我们正在呈现一个复选框组件并显示一个描述。我们使用来自`Formik`的`FieldArray`组件来处理市场数组和`FieldArray`的渲染道具[可以接收助手作为参数](https://formik.org/docs/api/fieldarray#fieldarray-helpers)。

我们的策略是，如果加密货币至少选择了一个市场，则将其标记为“选中”。

```
import React from "react";
import { FieldArray } from "formik";

import CheckboxRound from "components/CheckboxRound";

const descriptions = {
    bitcoin: "Bitcoin was the first cryptocurrency to successfully record transactions on a secure, decentralized blockchain-based network.",
    ethereum: "Ethereum is a decentralized computing platform which runs smart contracts and uses the Ether cryptocurrency built on top of the open source Ethereum blockchain"
}

const CryptoField = ({ crypto, selectedMarketplaces }) => {
    return (
        <FieldArray
            name={`marketplaces.${crypto.id}`}
            render={(arrayHelpers) => (
                <section className="">
                    <section className="d-flex align-items-center">
                        <CheckboxRound
                            checked={selectedMarketplaces.length}
                            onChange={() => {
                                if (selectedMarketplaces.length) {
                                    selectedMarketplaces.map(() => arrayHelpers.pop());
                                } else {
                                    crypto.marketplaces.map(({ id }) => arrayHelpers.push(id));
                                }
                            }}
                            id={crypto.name}
                        />
                        <div className="ml-4 d-flex">
                            <img src={crypto.brand_url} alt={`${crypto.label} logo`} className="mr-6" />
                            <b className="h6">{crypto.label}</b>
                        </div>
                    </section>
                    <div className="w-100 mb-4 ml-3">
                        {descriptions.hasOwnProperty(crypto.name) ? (
                            <p className="text-muted small">{descriptions[crypto.name]}</p>
                        ) : null}
                    </div>
                </section>
            )}
        />
    )
}

export default CryptoField

```

我们的屏幕应该是这样的:

![Select Crypto Screen](img/a0961afa9c31eea08a9228aa543beb96.png)

我们需要显示市场，所以让我们更新一下`CryptoField`组件。我们将遍历市场列表，并为每个市场显示一个`CheckboxCard`。

```
  <div className="d-flex mb-3">
  {crypto.marketplaces.map((marketplace, idx) => {
      const marketplaceSelected = selectedMarketplaces.includes(marketplace.id);
      return (
          <section className="mr-4 flex-1" key={idx}>
              <CheckboxCard
                  checked={marketplaceSelected}
                  onChange={(e) => {
                      if (marketplaceSelected) {
                          arrayHelpers.remove(
                              selectedMarketplaces.indexOf(marketplace.id)
                          );
                      } else {
                          arrayHelpers.push(marketplace.id);
                      }
                  }
              }
              label={marketplace.label}
              >
              <img src={marketplace.brand_url} alt="Marketplace logo" />
              </CheckboxCard>
          </section>
      );
  })}
  </div>

```

现在我们有了一个完整的界面。

![Select Crypto Screen with Coins and Marketplaces](img/faf0027abceabb3150d8cfdc9617b8a5.png)

## 完成`AddKey`屏幕和多步向导

`AddKey`屏幕与`SelectCrypto`屏幕非常相似。我们将允许用户只在他们选择的市场上更新密钥。为此，我们需要获取市场的 id，将它们传递给一个`Set`(以消除重复)，然后使用它们创建一个列表进行迭代。

对`src/screens/Portfolio/AddKeys.js`进行更新。

```
import { useState, useEffect } from "react";
import { useQuery } from "react-query";
import { Link } from "react-router-dom";
import { fetchAll } from 'utils'
import { useFormikContext } from "formik";

import Steps from "components/Steps";
import styles from 'screens/Portfolio/Portfolio.module.scss'
import MarketplaceKeyField from "components/MarketplaceKeyField";

function AddKeys({ stepsList }) {
    const [selectedMarketplaces, setSelectedMarketplaces] = useState([])
    const { isLoading, error, data: marketplacesList = [] } = useQuery(
        "marketplaces",
        () => fetchAll("/marketplaces")
    );

    const marketplaces = marketplacesList.reduce((acc, curr) => ({
        ...acc,
        [curr.id]: curr
    }), {})

    const { values } = useFormikContext();

    useEffect(() => {
        const selectedMarketplaces = [
            ...new Set(
                Object.values(values.marketplaces).reduce(
                    (vendorsList, vendor) => [...vendorsList, ...vendor],
                    []
                )
            )
        ]
        setSelectedMarketplaces(selectedMarketplaces)
    }, [values.marketplaces])

    if (isLoading) return <p>Loading...</p>
    if (error) return <p>Error</p>

    return (
        <div>
            <section className={styles.Section__Heading}>
                <h2 className={styles.Section__Heading__Title}>Take control with your keys.</h2>
                <p>Adding your keys allows you more control over integrations.</p>
            </section>

            <div className={styles.Steps__Container}>
                <Steps steps={stepsList} />
            </div>

            <section className={styles.Form__Card}>
                {selectedMarketplaces.map((marketplaceId, idx) => (
                    <div className="mb-4" key={idx}>
                        <MarketplaceKeyField marketplace={marketplaces[marketplaceId]} />
                    </div>
                ))}

                <div className={styles.Form__Button__Container}>
                    <Link to={`/portfolio/create/select-crypto`} className="btn">
                        <span className="chevron left" />
                        Back
                    </Link>
                    <button
                        type="submit"
                        className="btn btn-sm btn-primary font-weight-bold"
                    >
                        Submit
                        <span className="chevron right" />
                    </button>
                </div>
            </section>
        </div>
    )
}
export default AddKeys

```

### 创建`MarketplaceKeyField`组件

我们需要定义`MarketplaceKeyField`组件，它将显示市场的输入字段。我们将使用`useField` Formik 钩子来获取和设置单个字段值。

在`src/components/Marketplace/MarketplaceKeyField`创建`MarketplaceKeyField.js`并更新。

```
import { FieldArray, useField } from "formik";

const MarketplaceKeyField = ({ marketplace }) => {
    const identifier = `secrets.${marketplace.id}`;
    const [field, , helpers] = useField(identifier);

    return (
        <FieldArray
            name={`marketplaces.${marketplace.id}`}
            render={() => {
            return (
                <div className="d-flex">
                <label htmlFor={identifier} className="mr-4" style={{ width: "50%" }}>
                    <img
                    src={marketplace.brand_url}
                    alt={marketplace.label}
                    className="img img-responsive"
                    />
                </label>
                    <input
                    className="d-inline-block w-100"
                    id={identifier}
                    name={identifier}
                    value={field.value || ""}
                    autoSave={false}
                    onChange={(e) => helpers.setValue(e.target.value)}
                    type="password"
                    />
                </div>
            );
            }}
      />
    )
}

export default MarketplaceKeyField

```

![Add Keys Screen](img/9a8485d7963b90caec9e91b2a96df8ab.png)

## 用`Formik`提交处理单

设置好向导屏幕和控件后，我们需要一种方法将收集到的信息发送到服务器，以便保存和进一步处理。

让我们更新`Portfolio`组件，并将路线包装在`<form/>`元素中。我们将利用一个呈现函数来访问`Formik`提交处理程序，然后将该处理程序传递给表单。

```
    return (
        <div className={styles.Portfolio__Container}>
            <Formik initialValues={initialFormValues} onSubmit={handleFormSubmit}>
            {({ handleSubmit }) => (
                <form onSubmit={handleSubmit}>
                  {/* ...Rest of the Router goes here */}
                </form>
            </Formik>
        </div>

```

我们将很快定义提交处理程序。使用`react-query`时，通过使用`mutations`将信息保存到后端。我们将创建我们的第一个变异和一个记录我们有效负载的变异请求处理器。如果您有一个功能服务器，您可以发起一个实际的`HTTP`或`graphql`请求。

```
import { useMutation } from "react-query";

const portfolioCreationRequest = async (payload) => {
    console.log(JSON.stringify(payload))
    setTimeout(() => {
        return Promise.resolve("success");
    }, 3000)
  };

function Portfolio({ match: { url, path }, history }) {
  const mutation = useMutation(portfolioCreationRequest, {
      onSuccess: () => {
        history.push(`${path}/success`);
      },
    });

  const handleFormSubmit = (values) => {
      const { portfolioName, marketplaces, secrets } = values;
      const payload = {
          portfolioName,
          marketplaces,
          secrets
      }

      mutation.mutate(payload)
  }
// ... rest of the component
}

```

测试它，我们看到有效载荷被成功记录。

![Payload Recorded Testing with Formik](img/40788f79dce4237f48625394eff85488.png)

## 结论

`Formik`和`React-Query`对于 React 工程师来说是强大的工具，你可以使用它们来为重复出现的问题创建一致的解决方案。

我鼓励对 Formik 和 T2 React Query 文档进行更深入的研究，因为我们只利用了它们 API 表面的一小部分。

你可以在 [GitHub](https://github.com/creatrixity/crypto-portfolio) 找到完整的源代码。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)