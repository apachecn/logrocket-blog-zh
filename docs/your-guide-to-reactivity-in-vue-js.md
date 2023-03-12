# Vue.js - LogRocket 博客中的反应性指南

> 原文：<https://blog.logrocket.com/your-guide-to-reactivity-in-vue-js/>

在本文中，我们将研究 Vue.js 中的反应性，并讨论它是如何工作的。

## 介绍

反应性描述了应用程序状态的变化自动呈现在 DOM 中的情况。

Vue 是一个用于构建现代反应式用户界面和单一应用程序的渐进式框架。Vue 通过绑定到数据模型的模板声明支持 HTML 标记的增强呈现。

Vue.js 有一个自适应的反应式架构，专注于数据模型和组件组合的声明性呈现。

Vue 中的反应性，允许 Vue 在应用程序的每一层进行通信，并更新视图库数据修改。

### Vue 3 如何使用代理处理反应

反应性是一种基于数据流和变化传播的编程范式。这允许我们以声明的方式适应数据变化或事件。

在 Vue 中，反应性系统从它最早的版本开始就一直存在。Vue 最强大的功能之一是它的反应系统。

## Vue 2 中的反应状态

在深入研究 Vue 3 中的反应性如何工作之前，让我们简要回顾一下如何在 Vue 2 应用程序中创建反应性数据。如果您希望 Vue 跟踪您对数据所做的更改，您需要在从数据函数返回的对象之外声明该属性。

```
<template>
  <h1>My name is {{ name }}</h1>
</template>
<script>
  export default {
    data() {
      return {
        name: "amycruz"
      };
    }
  };
</script>
```

在 Vue 2 中，Vue 查看所有属性，并使用`Object.defineProperty()`为每条数据创建 getters 和 setters，并确保数据更改被跟踪。

根据 MDN web docs，`[object.defineProperty()](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty)`直接在一个对象上定义一个新的属性。它还可以修改对象的现有属性并返回该对象。使用`object.defineProperty`，我们可以很容易地设置一个对象的 getters 和 setters。

```
const data = {
    count: 10
};

const newData = {

}

Object.defineProperty(newData, 'count', {
  get() { return data.count; },
  set(newValue) { data.count = newValue; },
});

console.log(newData.count); // 10

newData.count = 20;

console.log(newData.count); // 20
```

在这里，我们添加了两个对象:`data`和`newData`(空)。`Data`持有我们原来的对象，而`newData`将作为对象。我们将使用`object.defineProperty()`来设置属性。

要跟踪属性何时被访问或修改，我们可以这样做:

```
const data = {
    count: 10
};

const newData = {

}

function track(){
  console.log("Prop accessed")
}
function trigger(){
  console.log("Prop modified")
}
Object.defineProperty(newData, 'count', {
  get() {track();return data.count; },
  set(newValue) { data.count = newValue;trigger(); },
});

console.log(newData.count); 
// Prop accessed 
// 10

newData.count = 20;
// Prop modified

console.log(newData.count); 
// Prop accessed
// 20
```

有了触发和跟踪功能，我们可以很容易地看到何时发生变化，并执行依赖跟踪和变化通知操作。

作为一名开发人员，重要的是你要真正理解反应性是如何在 Vue 3 中工作的，以避免反应性的限制——特别是在 Vue 2 中。您还需要理解反应性来使用新特性，比如组合 API。

默认情况下，JavaScript 不是自然反应式的:

```
// index.js 
let cost = 10
let qty =  5
let total = cost * qty
console.log(`The total amount is ${total}`) // The total is 50
cost = 20
console.log(`The total amount is ${total}`) // The total is 50\. total doesnt get updated
```

查看代码示例，您会注意到**总计**值没有更新，即使在我们将成本更改为 **20** 之后。

为了给我们的代码增加一点反应性，我们可以把计算变成一个函数:

```
let cost = 10
let qty =  5
let total = 0

//declare a computation
const computeTotal = () => total = cost * qty
computeTotal(); 
console.log(total) // 50
cost = 20
console.log(total) // still outputs 50
computeTotal() //run the computation again
console.log(total) // outputs 100
```

如您所见，现在我们可以运行计算函数来更新总计的计算值。

在上面的代码示例中，我们在 cost 值改变后手动运行计算，以获得更新的总数。这个过程是不可靠的，计算必须手动运行。这可能是一个问题，因为它会对性能产生负面影响。

## Vue 3 中的反应性

在 Vue 3 中，反应式 API 被重写，以解决 Vue 2 的许多缺点。反应式系统被重写以利用 JavaScript 的代理。代理充当拦截操作的对象或函数的包装器，比如获取和设置值。

通过 Vue 3 中新的反应系统，现在可以更好地支持使用代理对象观察数据的变化。

对象使你能够为另一个对象创建一个代理，它可以截取并重新定义那个对象的基本操作。

有了代理，我们可以拦截像`get`和`set`这样的操作，并立即看到数据何时被访问或更改。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

要创建代理，您需要传递两个参数:

`target`:数据对象
`handler`:定义你要截取的操作的对象

让我们看看下面的基本例子:

```
const data = {
  meal: "rice"
}

const handler = {
  get(target, prop, receiver){
    console.log("Data Get: ", target, prop);
    return target[prop];
  },
  set(target, key, value, receiver) {
    console.log("Data Set: ", target, key, value);
    return target[key] = value;
  }
}

const proxy = new Proxy(data, handler);

console.log(proxy) // { meal: "rice" }
console.log(proxy.meal) 
// Data Get: { meal: "rice" } meal
// rice
proxy.meal = "yam"
// Data Set: { meal: "rice" } meal yam
// yam
```

在上面的例子中，我们创建了一个接受数据对象的代理对象。我们还有一个处理程序，用来拦截对象中的`get`和`set`操作。每当我们试图访问`meal`道具时，`Data Get: { meal: "rice" } meal`就会被记录在控制台上。当我们试图更新代理对象中的`meal`属性值时，也会发生同样的事情。

为了维护对象 getters 和 setters 的正常行为，我们需要使用`reflect`对象来反映正常的对象行为。

让我们看看下面的例子:

```
const data = {
  meal: "rice"
}

const handler = {
  get(target, prop, receiver){
    console.log("Data Get: ", target, prop);
    return Reflect.get(target, prop, receiver);
  },
  set(target, key, value, receiver) {
    console.log("Data Set: ", target, key, value);
    return Reflect.set(target, key, value, receiver);
  }
}

const proxy = new Proxy(data, handler);

console.log(proxy) // { meal: "rice" }
console.log(proxy.meal) 
// Data Get: { meal: "rice" } meal
// rice
proxy.meal = "yam"
// Data Set: { meal: "rice" } meal yam
// yam
```

有了`Reflect`，我们不必像以前那样手动处理适当访问或更改。

为了查看数据何时被访问或更改，我们可以添加三个函数:

*   当有人访问数据时，让我们知道
*   当有人设置了物体的道具时通知我们
*   `trigger`:当对象中的任何数据发生变化时通知我们

看看下面的例子:

```
const data = {
  meal: "rice"
}
const track =(target, prop, receiver)=>{
  console.log("Data Get: ", target, prop);
}
const trigger =(target, key, value, receiver)=>{
  console.log("Data Change: ", target, key, prop, value);
}
const watch =(target, key, value, receiver)=>{
  console.log("Data Set: ", target, prop, value);
}
const handler = {
  get(target, prop, receiver){
    track(target, prop, receiver)
    return Reflect.get(target, prop, receiver);
  },
  set(target, key, value, receiver) {
     watch(target, key, value, receiver)
    if(target[key]!=value){
      trigger(target, key, value, receiver)
    }
    return Reflect.set(target, key, value, receiver);
  }
}

const proxy = new Proxy(data, handler);

console.log(proxy) // { meal: "rice" }
console.log(proxy.meal) 
// Data Get: { meal: "rice" } meal
// rice
proxy.meal = "yam"
// Data Set:  { meal: 'rice' } meal yam { meal: 'rice' }
// Data Change:  { meal: 'rice' } meal yam { meal: 'rice' }
// yam
proxy.meal = "beans"
// Data Set:  { meal: 'yam' } meal beans { meal: 'yam' }
// Data Change:  { meal: 'yam' } meal beans { meal: 'yam' }
// beans
proxy.meal = "beans"
// Data Set:  { meal: 'beans' } meal beans { meal: 'beans' }
// beans
```

在这里，我们能够根据我们添加的自定义函数来确定当数据发生变化时应该做什么。

Vue 附带了 reactive 方法，用于在 JavaScript 或 Vue 中创建反应状态。基本上，反应式方法只是一个函数，它创建一个代理，并将其包装在所提供的数据对象周围，最终将它变成一个被代理的对象。

它本质上是将您的数据转换成一个代理对象，这使得 Vue 能够在属性被访问或修改时执行依赖跟踪和更改通知。

结合我们现在所知道的一切，我们应该能够创造我们自己的反应方法:

```
const reactive = (data) =>{

    const track = (target, prop, receiver) =>{
      console.log("Data Get: ", target, prop);
    }
    const trigger = (target, key, value, receiver) =>{
      console.log("Data Change: ", target, key, value, receiver);
    }
    const watch =(target, key, value, receiver)=>{
      console.log("Data Set: ", target, key, value, receiver);
    }
    const handler = {
      get(target, prop, receiver){
        track(target, prop, receiver)
        return Reflect.get(target, prop, receiver);
      },
      set(target, key, value, receiver) {
         watch(target, key, value, receiver)
        if(target[key]!=value){
          trigger(target, key, value, receiver)
        }
        return Reflect.set(target, key, value, receiver);
      }
    }

    const proxy = new Proxy(data, handler);

    return proxy
}

const store = reactive({
  count: 0
})

store.count;
// Data Get: { count: 0 } count
// 0
store.count++;
// Data Set: { count: 0 } count 1 { count: 0 }
// Data Change: { count: 0 } count 1 { count: 0 }
// 1
```

我们刚刚创建了一个`reactive`方法，将我们的数据对象转换成一个代理。这也让我们能够对每个适当变更执行变更通知。

用户看不到这些被代理的对象，但是当属性被访问或修改时，它们让 Vue 执行依赖跟踪和更改通知。Vue 团队在 Vue 3 中发布了一个单独的反应包。

Vue 在内部跟踪所有被激活的对象，所以它总是为相同的对象返回相同的代理。

这样，我们可以说每个组件实例都有一个相应的 watcher 实例，它将组件呈现期间“接触”的任何属性记录为依赖项。如果将来某个依赖项的 setter 被触发，它会通知观察器，这又会导致组件重新呈现。

在第一次挂载或渲染时，组件会跟踪一系列依赖项(或对象中的数据)。这些是它在渲染过程中访问的属性。这些使组件成为这些属性的订阅者。结果，当一个代理截获一个设置操作时，属性将通知所有它订阅的组件重新呈现。

## 结论

恭喜你。您现在已经很好地理解了什么是反应性以及它在 Vue 中是如何工作的。我们还学习了 Vue 2 中的反应状态，并利用这些知识更有效地掌握和理解 Vue 3 中反应的价值。

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。