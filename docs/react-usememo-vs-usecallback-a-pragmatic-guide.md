# React useMemo vs. useCallback:实用指南

> 原文：<https://blog.logrocket.com/react-usememo-vs-usecallback-a-pragmatic-guide/>

随着 HTML 页面大小和复杂性的增长，创建高效的 React 代码变得比以往任何时候都更加重要。重新渲染大型组件的成本很高，而且通过单页应用程序(SPA)为浏览器提供大量工作会增加处理时间，并有可能赶走用户。

本教程研究 React 提供的两种不同的方法，以帮助开发人员规避与代码无关的性能问题:`useMemo`和`useCallback`。这些函数在渲染之间保留对象，有助于提高应用程序性能。

在您最喜欢的 web 服务器中创建一个 React 项目，以便跟进。

## 反应函数的性能优化

React 已经提供了`React.memo()`来避免重新创建 DOM 元素，但是这个方法不支持函数。因此，尽管函数是 JavaScript 中的一等公民，但它可能会在每次使用时被重新创建。

`useMemo`和`useCallback`方法有助于避免在某些情况下重新创建或重新运行函数。尽管[并不总是有用的](https://blog.logrocket.com/rethinking-hooks-memoization/) , `useMemo`或`useCallback`可能会在处理大量数据或许多共享行为的组件时产生显著的差异。例如，当你创建一个股票或数字货币交易平台时，这将是特别有用的一个场景。

## 什么是`useCallback`？

用`React.Memo()`包装组件表明了重用代码的意图。这不会自动扩展到作为参数传递的函数。

用`useCallback`包装时，React 保存对函数的引用。将此引用作为属性传递给新组件，以减少呈现时间。

## 一个实际的回调例子

回调的工作方式与任何其他内联方法没有什么不同。您可以像使用 JavaScript 中的其他函数一样使用包装函数。

考虑以下示例组件:

```
function TransactionList({props}){
   const purchase = useCallback(event =>{
   const id = event.currentTarget.id;
   const price = event.currentTarget.getAttribute("value");
   // perform purchase operation and render elsewhere
   // ...
   console.log("Purchased " + id + " at " + price);
   }, [props]);
   return(<TransactionListItems onItemClick={purchase}/>)
}

export default TransactionList;

```

该对象依赖于`TransactionListItems`子组件:

```
function TransactionListItems({onItemClick}){
    // potentially large list typically from a database
    var prices = [
        {"id": 1, "price": 1000.14},
        {"id": 2, "price": 2000.05},
        {"id": 3, "price": 300.00},
        {"id": 4, "price": 400.00}];
    var priceItems = [];
    // render items
    prices.forEach(priceElement =>{
       priceItems.push(<div style={{"width": "100%", "height": "2em"}} key={priceElement.id} name="child-price-item" id={priceElement.id} value={priceElement.price} onClick={onItemClick}>${priceElement.price}</div>);
    });
    // display
    return (<div name="prices">
        {priceItems}
    </div>)
}
export default React.memo(TransactionListItems);

```

`TransactionList`包含了包装好的`purchase`功能，允许用户以一定的价格购买一件商品。`TransactionListItems`中的每一项都利用了这种方法。在这种情况下，`useCallback`消除了不必要的函数渲染。如果没有它，JavaScript 会在每次点击时渲染`purchase`。

在我们的股票市场例子中，购买可以在一天中快速进行。当软件实时执行交易时,`useCallback`方法可以帮助临时交易者避免错过下一个机会。

## 什么是`useMemo`？

`useMemo`函数服务于类似的目的，但是内部化返回值而不是整个函数。React 不会将句柄传递给同一个函数，而是跳过该函数并返回前一个结果，直到参数发生变化。

这使您可以避免在必要时重复执行潜在的高成本操作。小心使用该方法，因为函数中定义的任何变化的变量都不会影响`useMemo`的行为。例如，如果要添加时间戳，这个方法不关心时间的变化，只关心函数参数的不同。

## 使用反应函数记忆

为了了解`useMemo`是如何工作的，考虑一个实例，在这个实例中，purchases 应该更新一个用户想要为某个价格范围过滤的总数。这种用例对于与股票或数字货币市场合作的大规模交易平台来说是有意义的。交易数量可能很大，尤其是平台零碎出售资产的时候。

对于本教程，创建一个父类来创建一组公共属性:

```
function Purchases({props}){
    const globalPurchaseStats = {
        "purchases": [1, 3, 4, 12, 200, 100],
        "total": 0,
        "greaterThan": 10
    };
    const [globalPurchaseState, setGlobalPurchaseState] = useState(globalPurchaseStats);
    return (<div>
        <PurchasesMade setState={setGlobalPurchaseState}/>
        <PurchaseStatistics purchaseState={globalPurchaseState} />
    </div>);
}
export default Purchases;

```

状态对象允许每个子对象操作同一个对象。它还确保对`purchases`数组的任何更改都会导致我们的内存化函数执行。

接下来，构建`PurchasesMade`，为全局状态变量传入 setter:

```
function PurchasesMade({setState}){
    const update_purchases = () =>{
        const priceArr = [];
        let priceStr = "";
        let total = 0;
        for(var i = 0; i < 10; i++){
            const newPrice = Math.random();
            priceArr.push(newPrice);
            if(priceStr.length > 0){
                priceStr += ", ";
            }
            priceStr += newPrice * 100;
            total += newPrice * 100;
        };
        console.log(priceArr);
        console.log(priceStr)
        setState({"total": total, "purchases": priceArr});
        document.getElementById("current-purchases").innerText = priceStr;
    };
    return <div stye={{"width": "100%", "height": "2em"}} className="purchases" id={"current-purchases"} onClick={update_purchases}>No Purchases yet</div>
}
export default PurchasesMade;

```

最后，使用实际状态对象创建`PurchaseStatistics`组件:

```
function PurchaseStatistics({purchaseState}){
    const filter_purchases = useMemo(() => {
        console.log("Recalculating Purchase Statistics");
        const greaterThan = purchaseState.greaterThan;
        let purchases = purchaseState.purchases;
        let total = 0;
        if(greaterThan > 0) {
            purchases = purchases.filter(price => price > greaterThan);
        }
        purchases.forEach(price =>{
            total += price;
        });
        total += 0.0;
        document.getElementById("filter-total").textContent = "Filtered: $" + total;
    }, [purchaseState.purchases, purchaseState.greaterThan])
    return(<button onClick={filter_purchases}>No Update Required</button>)
}
export default PurchaseStatistics;

```

通过用`useMemo`包装更新，用户可以在必要时强制更新，比如当有变化时，而不用在输入保持不变时运行函数。这有助于交易者在高度焦虑的时候保持快乐。

它还可以通过在网站更新`purchases`数组之前返回保存的结果来减少处理时间。您可以使用类似的代码来构建销售资产的行为。

## 在 React 中使用`useCallback`与`useMemo`

`useCallback`和`useMemo`功能表面上看起来很相似。然而，每一种都有特定的用例。

当出现以下情况时，用`useCallback`包装函数:

*   在`React.memo()`中包装一个功能组件，该组件接受您的方法作为属性
*   将函数作为依赖项传递给其他挂钩

利用`useMemo`:

*   对于输入逐渐变化的函数
*   当数据值不是太大而导致潜在的内存问题时
*   当参数没有小到比较的代价超过包装器的使用时

当每次调用都要重新编译代码时，回调就能很好地工作。当输入随时间逐渐变化时，记忆结果有助于降低重复调用函数的成本。另一方面，在交易的例子中，我们可能不想记住不断变化的订单簿的结果。

## `useCallback`和`useMemo`反模式

你可能会很容易认为可以将`useCallback`或`useMemo`T2 用于每一个函数 …但事实并非如此。包装函数会带来一些开销。每个调用都需要额外的工作来解开函数调用并决定如何进行。

注意`update_purchases`函数不是回调函数，而`purchase`函数是。更新功能不符合我们的标准，因为它只创建一次，从未共享。相比之下，`TransactionListItems`中的每个变更列表项都使用`purchase`功能。

类似地，我们包装了使用`useMemo`进行购买的更新，但绝不会包装处理频繁变化的数据的函数。React 每次都检查输入参数，然后必须调用函数。

## 识别用例

在决定是否利用这些包装器时，首先考虑您现有的性能。使用这些函数的好处相对来说是微不足道的，所以你可能想从[改进你的代码开始。](https://www.keycdn.com/blog/javascript-performance)

借助前端[应用性能监控](https://blog.logrocket.com/rethinking-frontend-apm/)工具识别潜在弱点。然后尝试重构您的 JavaScript。如果做不到这一点，请使用本指南来确定下一步的最佳选择。

## 结论

`useCallback`和`useMemo`功能是微调 React 的工具。知道如何以及何时使用每一个都有可能提高应用程序的性能。尽管如此，没有一个包装器可以替代糟糕的代码库。

在这里，我们提供了一个指南来理解如何使用这些工具，但是请记住，使用它们是有代价的。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)