# 现代组件可重用性:在 Vue 中的 React & scoped 插槽中呈现道具

> 原文：<https://blog.logrocket.com/modern-component-reusability-render-props-in-react-scoped-slots-in-vue-ff3c5b2dc899/>

![](img/71f11232ac9e5b295f19477fc99795e5.png)

所有前端开发人员面临的一个问题是如何使 UI 组件可重用。我们如何以这样一种方式来制作组件，以满足我们现在清楚的狭窄用例，同时还使它们足够可重用以在各种环境中工作？

假设我们正在构建一个自动完成组件:

```
class Autocomplete extends React.Component {
    constructor(props) {
        super(props)

        this.state = {
            results: props.options
        }
    }

    searchList(event) {
        const results = this.props.options
            .filter(option => option.toLowerCase().includes(event.target.value.toLowerCase()))
        this.setState({ results })
    }

    render () {
        return (
            <div className="autocomplete">
                <input
                    type="text"
                    placeholder="Type to search list"
                    onChange={searchList}
                />
                <div className="autocomplete-dropdown">
                    <ul className="autocomplete-search-results">
                        {this.state.results.map(option => (
                            <li class="autocomplete-search-result">{option}</li>
                        ))}
                    </ul>
                </div>
            </div>
        )
    }
}
```

在这个组件中，我们有一些控制核心搜索行为的逻辑，但是我们也指定如何呈现输入和搜索结果。在这个实例中，我们呈现了一个 div，作为一个下拉容器和一个无序列表，其中包含每个结果的列表项。

想想你将如何重用这个组件。当然，如果您想要重现完全相同的行为和视觉效果，您可以使用这个完全相同的组件。但是，如果您想要重用相同的行为，但是组件的可视化略有不同，该怎么办呢？如果您想要重用核心搜索行为，但是为稍微不同的用例添加一些修改，该怎么办？

假设您想要一个类似标签的搜索结果列表，而不是包含搜索结果的下拉列表，该列表总是显示:

![](img/7bdfce28d9cc70bf019adb08a635b118.png)

这两个组件的核心功能非常相似:在输入中输入内容来过滤列表。

对于现代 JavaScript 框架现在提供的一些相对较新的工具来说，这是一个完美的用例。这些是 React 中的*渲染道具*和 Vue 中的*作用域插槽*。它们的工作方式非常相似，并且提供了一种将组件的**行为**与其**表现**分离的方法。

[![](img/94b3e0f84b30cb0d321f60471481ab64.png)](https://logrocket.com/signup/)

### 在 React 中渲染道具

首先，让我们看看如何在 React 中使用 render props 来重构我们的 autocomplete 组件。我们现在将有两个组件——一个用于我们的自动完成组件，另一个用于核心的 SearchSelect 组件。

让我们先来看看 SearchSelect 组件:

```
class SearchSelect extends React.Component {
    constructor(props) {
        super(props)
        this.state = {
            results: props.options
        }
    }

    searchList(event) {
      const results = this.props.filterMethod(this.props.options, event.target.value)
        this.setState({ results })
    }

    render() {
        return this.props.render({
            results: this.state.results, 
            searchList: (event) => this.searchList(event) 
        })
    }
}
```

这是一个*无呈现的*组件(它不呈现自己的任何标记)。相反，它返回一个叫做*渲染道具*的特殊道具的结果。这个渲染属性接受一个对象，您可以将希望父组件能够访问的任何数据传递到该对象中。

我们的 SearchSelect 组件处理最底层的功能——根据查询字符串过滤选项列表。然后它使用特殊的渲染属性来渲染一个元素。

在父组件中，我们将一个函数传递给 SearchSelect 组件的 render prop。该函数返回一个 React 元素，我们可以将它与 SearchSelect 组件本身的状态和行为结合起来。基本上，这意味着我们能够从父组件的子组件中访问数据。

```
import SearchSelect from './search-select'

class Autocomplete extends React.Component {
    constructor(props) {
        super(props)
    }

    filterMethod (options, query) {
        return options.filter(option => option.toLowerCase().includes(query.toLowerCase()))
    }

    render() {
        return (
            <SearchSelect
                options={this.props.options}
                filterMethod={this.filterMethod}
                render={({results, searchList}) => (
                    <div>
                        <input
                            type="text"
                            placeholder="Type to search list"
                            onChange={searchList}
                        />
                        <ul>
                            {results.map(option => (
                                <li>{option}</li>
                            ))}
                        </ul>
                    </div>
                )}
            />
        )
    }
}
```

实现这一点的关键是我们传递给 render prop 函数的参数。看到我们是如何析构单个对象并在我们的标记中使用这些析构的属性了吗？当您在子组件中调用`this.props.render()`时，该对象应该作为参数传递。

所有这一切意味着，我们可以编写任何我们想要的标记，只要我们用 SearchSelect 组件公开的数据和行为适当地混合它。

另外，请注意我们是如何将过滤列表的方法作为一个属性传递进来的。这将允许我们更改选项列表的过滤方式，同时仍然使用 SearchSelect 组件。

让我们看看如何实现类似标签的列表组件。我们使用相同的 SearchSelect 核心组件，只是更改了由 render prop 呈现的标记:

```
import SearchSelect from './search-select'

class TagListSearch extends React.Component {
    constructor(props) {
        super(props)
    }

    filterMethod (options, query) {
        return options.filter(option => option.toLowerCase().includes(query.toLowerCase()))
    }

    render() {
        return (
            <SearchSelect
                options={this.props.options}
                filterMethod={this.filterMethod}
                render={({results, searchList}) => (
                    <div className="tag-list-search">
                        <input
                            type="text"
                            placeholder="Type to search list"
                            onChange={searchList}
                        />
                        <ul className="tag-list">
                            {results.map(result => (
                                <li className="tag" key={result}>{result}</li>
                            ))}
                        </ul>
                    </div>
                )}
            />
        )
    }
}
```

查看工作示例:

见 [CodePen](https://codepen.io) 上乔纳森·哈勒尔([@乔纳森·哈勒尔](https://codepen.io/jonathanharrell) )
的笔 [可重复使用 React 搜索选择带渲染道具](https://codepen.io/jonathanharrell/pen/KeOmVV/)。

Vue 中的作用域插槽

### 现在让我们看看如何使用作用域插槽在 Vue 中实现这一点。首先，这是我们的 SearchSelect 组件(在这个例子中，我使用了全局注册的组件，但是在实际项目中，您可能应该使用单个文件组件):

如您所见，这看起来非常类似于 React 组件中的渲染道具。这里，我们返回一个默认的*作用域槽*，它传递一个我们想要的对象。在这里，我们给它的结果和我们的搜索方法。

```
Vue.component('search-select', {
    props: [
        'options',
        'filterMethod'
    ],

    data () {
        return {
            query: ''
        }
    },

    computed: {
        results () {
            return this.filterMethod(this.options, this.query)
        }
    },

    methods: {
        setQuery (event) {
            this.query = event.target.value
        }
    },

    render () {
        return this.$scopedSlots.default({
            results: this.results,
            searchList: (event) => {
                this.setQuery(event)
            }
        })
    }
})
```

在我们的 Autocomplete 组件中，我们使用`slot-scope`属性来访问子组件中的数据。我们可以析构通过的属性以便于访问，就像 React render prop 参数一样:

查看工作示例:

```
Vue.component('autocomplete', {
    data () {
        return {
            dropdownVisible: false
        }
    },

    methods: {
        filterMethod (options, query) {
            return options.filter(option => option.toLowerCase().includes(query.toLowerCase()))
        },

        showDropdown () {
            this.dropdownVisible = true
        },

        hideDropdown () {
            this.dropdownVisible = false
        }
    },

    template: `
        <search-select
          :options="options"
          :filterMethod="filterMethod"
        >
            <div slot-scope="{ results, searchList }">
                <div class="autocomplete">
                    <input
                        type="text"
                        placeholder="Type to search list"
                        @input="searchList"
                        @focus="showDropdown"
                        @blur="hideDropdown"
                    />
                    <div class="autocomplete-dropdown" v-if="dropdownVisible">
                        <ul class="autocomplete-search-results-list">
                            <li 
                                class="autocomplete-search-result"
                                v-for="result in results"
                                :key="result"
                            >
                                {{ result }}
                            </li>
                        </ul>
                    </div>
                </div>
            </div>
        </search-select>
    `
})
```

参见 [CodePen](https://codepen.io) 上 Jonathan Harrell([@ Jonathan Harrell](https://codepen.io/jonathanharrell))
的 Pen [可重用 Vue Search Select with Scoped slot](https://codepen.io/jonathanharrell/pen/VdobNO/)。

渲染道具和作用域插槽的其他用途

### 创建可重用的接口组件并不是渲染道具和作用域插槽的唯一用途。下面是一些其他的想法，告诉你如何使用它们将可重用的行为封装到一个组件中，然后将该组件暴露给它的父组件。

**数据提供者组件**

您可以使用 render props/scoped slot 来创建一个组件，该组件处理异步获取数据并向其父级公开该数据。这允许您隐藏访问端点、获取结果和处理可能的错误的逻辑，以及在数据获取过程中向用户显示加载状态。

基本组件可能是这样的:

它接受一个 URL 作为道具，并处理实际的获取逻辑。然后，我们在父组件中使用它:

```
class FetchData extends React.Component {
    constructor(props) {
        super(props)
        this.state = {
            loading: false,
            results: [],
            error: false
        }
    }

    componentDidMount() {
        this.fetchData(this.props.url)
    }

    fetchData(url) {
        this.setState({ loading: true })

        fetch(url)
            .then(data => data.json())
            .then(json => {
                this.setState({ loading: false, results: json })
            })
            .catch(error => {
                this.setState({ loading: false, error: true })
            })
    }

    render() {
        return this.props.render({
            loading: this.state.loading,
            results: this.state.results,
            error: this.state.error
        })
    }
}
```

**观察者(调整大小、相交等。)**

```
class App extends React.Component {
    constructor(props) {
        super(props)
    }

    render() {
        return (
            <div className="wrapper">
                <FetchData
                    url="https://jsonplaceholder.typicode.com/todos"
                    render={({loading, results, error}) => (
                        <div>
                            {loading && (
                                <p>Loading...</p>
                            )}
                            {results.length > 0 && (
                                <div className="results">
                                    {results.map(result => (
                                        <p key={result.id}>{result.title}</p>
                                    ))}
                                </div>
                            )}
                            {error && (
                                <p>There was a problem loading.</p>
                            )}
                        </div>
                    )}
                />
            </div>
        )
    }
}
```

您还可以使用渲染道具/作用域插槽来创建一个组件，作为调整大小或相交观察点的包装器。该组件可以简单地向父组件公开元素的当前大小或交叉点。然后，您可以根据父类中的数据执行任何您需要的逻辑，保持关注点的良好分离。

下面是一个观察自身大小并向其父级公开其高度和宽度的基本组件:

我们使用[元素大小调整检测器](https://github.com/wnr/element-resize-detector)库来监听元素大小的变化，并使用 React ref 来获取对实际 DOM 节点的引用。

```
class ObserveDimensions extends React.Component {
    constructor(props) {
        super(props)
        this.state = {
            width: null,
            height: null
        }
        this.elementToObserve = React.createRef()
    }

    componentDidMount(nextProps) {
        const erd = elementResizeDetectorMaker({ strategy: 'scroll' })

        erd.listenTo(this.elementToObserve.current, element => {
            this.setState({
            width: element.offsetWidth,
            height: element.offsetHeight
        })
    });
    }

    render() {
        return (

                {this.props.render({
                    width: this.state.width,
                    height: this.state.height
                })}

        )
    }
}
```

然后，我们可以在我们的应用程序中轻松使用该组件:

结论

```
class App extends React.Component {
    constructor(props) {
        super(props)
    }

    render() {
        return (
            <div className="wrapper">
                <ObserveDimensions
                    render={({width, height}) => (
                        <div>
                            Width: {width}px
                            Height: {height}px
                        </div>
                    )}
                />
            </div>
        )
    }
}
```

### 使用渲染道具和作用域插槽成功创建可重用组件的关键是能够正确地将**行为**与**表示**分开。每当你创建一个新的 UI 组件时，思考“这个组件的核心行为是什么？我能在其他地方使用这个吗？”

拥有一组使用渲染道具或作用域插槽的核心无渲染组件可以帮助您减少应用程序中的代码重复，并更仔细地考虑您的核心接口行为。

[LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

## 调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。

像用户一样体验您的 Vue 应用

## 调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。

Modernize how you debug your Vue apps - [Start monitoring for free](https://lp.logrocket.com/blg/vue-signup).

* * *