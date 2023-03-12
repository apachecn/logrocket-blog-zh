# 隐式状态共享:React 的上下文 API &在 Vue - LogRocket 博客中提供/注入

> 原文：<https://blog.logrocket.com/implicit-state-sharing-reacts-context-api-provide-inject-in-vue-679062a50f05/>

假设您正在创建一个 accordion 组件，并希望通过 npm 包公开发布。您希望这个 accordion 的用户能够通过将多个组件组合在一起，以非常灵活的方式使用组件。

假设这是您理想的 API:

```
<Accordion>
    <AccordionItem>
        <AccordionHeader>Header content</AccordionHeader>
        <AccordionPanel>Panel content</AccordionPanel>
    </AccordionItem>
</Accordion>
```

`AccordionItem`将包含可展开或折叠的手风琴的每个部分，`AccordionHeader`将是用户可以点击展开或折叠的地方，`AccordionPanel`将包含要显示或隐藏的内容。

每个`AccordionItem`都需要保持某种状态——不管它是不是*展开的*。但是`AccordionHeader`也需要访问这个值，这样它就可以显示适当的切换按钮。而`AccordionPanel`可能也需要访问这个，因为它是被展开和折叠的东西。

一种可能是通过 render props 向用户公开扩展的值，并确保您的文档让他们知道他们需要将该值传递给 header 和 panel 组件。

```
<Accordion>
    <AccordionItem render={({expanded}) => (
        <AccordionHeader expanded={expanded}>
            Header content
        </AccordionHeader>
        <AccordionPanel expanded={expanded}>
            Panel content
        </AccordionPanel>
    )} 
    />
</Accordion>
```

虽然这看起来是一个不错的解决方案，但是让组件的消费者担心组件的内部结构并不理想。事实上,`AccordionHeader`和`AccordionPanel`需要访问扩展状态不应该是我们的用户必须关心的事情。

还应该注意，虽然这是一个简单的例子，但是您的组件可能要复杂得多，具有多层嵌套组件，在这种情况下，正确的钻探可能会变得非常繁琐。

我们真正需要的是一种方式来*含蓄地*传递道具。

## 使用 React 的上下文 API

对于这种情况，有一个更好的解决方案——React 的上下文 API。我们可以使用上下文 API 来创建一些状态，并在幕后需要的地方提供它，从我们面向公众的 API 中消除这种顾虑。(参见:[复合组件模式](https://blog.logrocket.com/guide-to-react-compound-components-9c4b3eb482e9))。

首先，我们将创建一个上下文，并定义该上下文的*形状*。我们将从一个`expanded`值和一个`toggleExpansion`方法开始。我们将此上下文定义为与我们的 accordion 项目特别相关:

```
const AccordionItemContext = React.createContext({
    expanded: false,
    toggleExpansion: () => {}
});
```

现在，在我们的`AccordionItem`组件中，我们将定义`expanded`和`toggleExpansion`值，并将它们作为`Provider`组件的值输入。

```
class AccordionItem extends React.Component {
    constructor (props) {
        super(props)

        this.toggleExpansion = () => {
            this.setState({ expanded: !this.state.expanded })
        }

        this.state = {
            expanded: false,
            toggleExpansion: this.toggleExpansion
        }
    }

    render () {
        return (
            <AccordionItemContext.Provider value={this.state}>
                <div className="accordion-item">
                    {this.props.children}
                </div>
            </AccordionItemContext.Provider>
        )
    }
}
```

`Provider`是上下文等式的一半。另一半是`Consumer`。正如我们将很快看到的那样，`Provider`允许`Consumer`订阅上下文变化。

接下来，我们需要将`AccordionHeader`和`AccordionPanel`设置为这个上下文的*消费者*:

```
const AccordionHeader = (props) => {
    return (
        <AccordionItemContext.Consumer>
            {({ expanded, toggleExpansion }) => (
                <h2 className="accordion-header">
                    <button onClick={toggleExpansion}>
                        { expanded ? '▼ ' : '► ' } 
                        { props.children }
                    </button>
                </h2>
            )}
        </AccordionItemContext.Consumer>
    )
}
```

组件`Consumer`需要一个函数作为其子组件。这个函数将接收上下文值，我们将它析构为`expanded`和`toggleExpansion`。然后，我们的组件能够在其模板中使用这些值。

我们将类似地使用`Consumer`来给予`AccordionPanel`对上下文值的访问:

```
const AccordionPanel = (props) => {
    return (
        <AccordionItemContext.Consumer>
            {({ expanded }) => <div className={"accordion-panel " + (expanded ? 'expanded' : '')}>{props.children}</div>}
        </AccordionItemContext.Consumer>
    )
}
```

现在，我们真的可以为 accordion 组件实现我们理想的 API 了。我们组件的用户不必担心在组件树中上下传递状态。这些组件的内部结构对他们是隐藏的:

```
<Accordion>
    <AccordionItem>
        <AccordionHeader>Header content</AccordionHeader>
        <AccordionPanel>Panel content</AccordionPanel>
    </AccordionItem>
</Accordion>
```

参见 [CodePen](https://codepen.io) 上 Jonathan Harrell([@ Jonathan Harrell](https://codepen.io/jonathanharrell))
使用上下文反应手风琴组件的笔[。](https://codepen.io/jonathanharrell/pen/WgBzyX)

提供/注入 Vue

## Vue 提供了一个类似 React 的上下文 API 的工具，叫做 provide/inject。为了使用它，我们将在我们的`accordion-item` Vue 组件上使用`provide`方法:

我们从`provide()`返回一个对象，该对象包含我们想要提供给其他组件的状态。注意，我们将一个对象传递给`accordionItemState`，而不是简单地传递<代码“>扩展值”。`provide`为了有所反应，必须传递一个对象。

```
Vue.component('accordion-item', {
    data () {
        return {
            sharedState: {
                expanded: false
            }
        }
    },

    provide () {
        return {
            accordionItemState: this.sharedState
        }
    },

    render (createElement) {
        return createElement(
            'div', 
            { class: 'accordion-item' }, 
            this.$slots.default
        )
    }
})
```

注意，我们在这里使用一个 render 函数来创建这个组件，但是这对于使用 provide/inject 来说并不是必需的。

现在，我们将把这个状态注入到我们的子组件中。我们将简单地使用`inject`属性，它接受对应于我们在`provide`中定义的对象属性的字符串数组。

一旦我们在`inject`中包含了属性名，我们就可以访问模板中的那些值。

```
Vue.component('accordion-header', {
    inject: ['accordionItemState'],

    template: `
        <h2 class="accordion-header">
            <button @click="accordionItemState.expanded = !accordionItemState.expanded">
                {{ accordionItemState.expanded ? '▼' : '►' }} 
                <slot></slot>
            </button>
        </h2>
    `
})
```

参见 [CodePen](https://codepen.io) 上 Jonathan Harrell([@ Jonathan Harrell](https://codepen.io/jonathanharrell))
使用提供/注入的 Pen [Vue 手风琴组件。](https://codepen.io/jonathanharrell/pen/PdvRoP)

```
Vue.component('accordion-panel', {
    inject: ['accordionItemState'],

    template: `
        <div class="accordion-panel" :class="{ expanded: accordionItemState.expanded }">
            <slot></slot>
        </div>
    `
})
```

小心使用

值得注意的是，只有在真正有意义的时候，才应该隐式地传递道具。这样做太多会混淆组件的真实行为，并给其他可能参与您的项目的开发人员带来困惑。

## 打包并分发到其他应用程序中使用的组件库是一个完美的用例，因为组件的内部属性实际上不需要向最终用户公开。

React 的上下文 API 和 Vue 的提供/注入特性都使得通过隐式状态共享来实现这一点成为可能。

[LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

## .

LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。

像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

## .

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。

The LogRocket Vuex plugin logs Vuex mutations to the LogRocket console, giving you context around what led to an error, and what state the application was in when an issue occurred.

Modernize how you debug your Vue apps - [Start monitoring for free](https://lp.logrocket.com/blg/vue-signup).