# 使用 JavaScript 框架提交 HTML 表单

> 原文：<https://blog.logrocket.com/submitting-html-forms-using-javascript-frameworks-vue-react-hyperapp-44664dba1218/>

**在表单中实现 Vue**

在上一节中，我们已经解释了在 HTML 结构和`@click`指令中看到像`v-model`这样的属性的原因。这里，我们展示了处理其余部分的 Vue 部分是什么样子的。

在 HTML 文档中打开一个脚本文件并粘贴到:

```
<script>
var app = new Vue({
    el: '#app',
    data: {
    form: {
    name: '',
    mob: '',
    email: '',
    mess: ''
    }
},
methods: {
  submitForm: function(){
      axios.post('https://httpbin.org/anything', this.form)
      .then(function (response) {
        console.log(response.data);
      })
      .catch(function (error) {
        console.log(error);
      });
  }
}
})
</script>
```

在上面的代码块中，我们定义了一个名为 form 的对象，它包含了我们的数据。接下来，我们定义了一个名为`submitForm`的方法，它向`[https://httpbin.org/anything](https://httpbin.org/anything)`发出 Ajax 请求。我们使用 httpbin，因为他们的服务允许我们执行免费的 HTTP 方法。`/anything`路由将返回我们发送给它的准确数据。

看到使用 JavaScript 提交表单有多简单了吗？您所需要做的就是将 URL 更改为您的服务器的 URL。

**为什么我的表格没有提交？**我们经常注意到，在编写了看起来正确的代码之后，表单没有提交。我们如何解决这个问题？让我强调一下您的 Vue 表单可能无法提交的常见原因。

*   通过`el`键传入 Vue 对象的 id 为`app`的挂载元素不存在，app 没有绑定到 Vue
*   提交按钮上的点击处理程序不存在/未附加
*   没有引用 axios 库
*   未引用 Vue 库

### 使用 React 提交表单

React 是一个由脸书开发和维护的用于构建用户界面的 JavaScript 库。React 使得创建交互式 ui 变得不那么痛苦。为应用程序中的每个状态设计简单的视图，当数据发生变化时，React 将有效地更新和呈现正确的组件。

首先，让我们定义我们的 HTML 结构。创建一个名为`react.html`的文件，并添加:

```
<link href="//maxcdn.bootstrapcdn.com/bootstrap/3.3.0/css/bootstrap.min.css" rel="stylesheet" id="bootstrap-css">
<script crossorigin src="https://unpkg.com/[email protected]/umd/react.development.js"></script>
<script crossorigin src="https://unpkg.com/[email protected]/umd/react-dom.development.js"></script>
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
<div class="container" id="app">
</div>
```

上面的代码片段是一个基本的 HTML 声明，其中我们:

*   需要引导 CSS 库
*   需要 React JavaScript 库
*   需要 React-Dom JavaScript 库
*   需要 Axios JavaScript 库，这个库将发出`POST`请求
*   声明了一个 id 为`app`的 div，这将是我们的根组件

**将 React 应用到混音中**

我们有了一个基本的设置，其中包含所需的可用库和一个 react 将附加到的根元素。让我们继续 react 实现。打开脚本标签并输入:

```
class Root extends React.Component {
      constructor(props) {
        super(props);
        this.state = {
          form: {
            name: "",
            mob: "",
            email: "",
            mess: ""
          }
        };
        this._onInputChange = this._onInputChange.bind(this);
        this._onSubmit = this._onSubmit.bind(this);
      }
      _onInputChange(name, e) {
        var form = this.state.form;
        form[name] = e.target.value;
        this.setState(form);
      }
      _onSubmit() {
        axios
          .post("https://httpbin.org/anything", this.state.form)
          .then(function(response) {
            console.log(response.data);
          })
          .catch(function(error) {
            console.log(error);
          });
      }
      render() {
        return (
          <div className="row">
            <div className="col-md-4">
              <div className="panel">
                <h4 className="heading">
                  <strong>Quick </strong> Contact <span />
                </h4>
                <div className="form">
                  <input
                    type="text"
                    required=""
                    placeholder="Please input your Name"
                    className="form-control"
                    onChange={e => this._onInputChange("name", e)}
                  />
                  <input
                    type="text"
                    required=""
                    placeholder="Please input your mobile No"
                    className="form-control"
                    onChange={e => this._onInputChange("mob", e)}
                  />
                  <input
                    type="text"
                    required=""
                    placeholder="Please input your Email"
                    onChange={e => this._onInputChange("email", e)}
                    className="form-control"
                  />

                  <textarea
                    placeholder="Your Message"
                    className="form-control"
                    onChange={e => this._onInputChange("mess", e)}
                  />
                  <input
                    type="submit"
                    value="submit"
                    name="submit"
                    className="btn btn-primary"
                    onClick={this._onSubmit}
                  />
                </div>
              </div>
            </div>
          </div>
        );
      }
    }
    ReactDOM.render(<Root />, document.getElementById("app"));
```

让我们回顾一下上面的内容。这里，在我们的构造函数中，我们声明了一个包含表单对象的初始状态，然后我们绑定了两个函数，我们将在输入改变时设置状态并提交表单。

在`_onInputChange`函数中，我们接收两个参数，它们是:

*   名称:元素的名称
*   事件:发生的变更事件

我们使用这两个参数来设置被改变的确切输入的状态。

在`_onSubmit`函数中，我们向`[https://httpbin.org/anything](https://httpbin.org/anything)`端点发出一个 post 请求，后者返回发送的确切参数。在这里，这是我们用来作为我们的服务器。

让我们仔细看看`render`函数，在这里元素被渲染。

这里，我们定义了 5 个元素，包括 3 个输入、一个文本区域(其更改事件绑定到`_onInputChange`函数)和一个按钮元素(其点击事件绑定到`_onSubmit`方法)。

最后，我们将应用程序附加到 HTML 标记的一个元素上。

为什么我的表单不显示？我敢打赌，你已经得到了一个空白的屏幕，不知道错误来自哪里。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

快速浏览一下 render 函数，您会注意到我们在其中使用了`jsx`语法。现在，这就是问题所在。除非你使用 babel 来编译你的应用程序，否则`jsx`很可能会失败。这是因为`jsx`不是常规的 javascript 语法，在这里，我们使用 React 的浏览器构建。

那么我们如何解决这个问题呢？这是一个简单的修复。

任何 JSX 块都可以转换成对具有三个参数的`React.createElement`的调用:

*   要创建的元素，例如`div`、`span`、`ul`等
*   一个属性对象，它指定要在该元素上设置的任何属性值，如`class`、`style`、`required`等
*   要放入其中的任何子元素。这可能是一个字符串或对`React.createElement`的其他调用，以获取更多元素。

将渲染函数替换为:

```
render() {
        return (
            React.createElement("div", { className: 'row' }, [
                React.createElement("div", { className: 'col-md-4' }, [
                    React.createElement("div", { className: 'panel' }, [
                        React.createElement("h4", {}, 'Quick Contact'),
                        React.createElement("div", { className: 'form' }, [
                            React.createElement("input", {
                                type: 'text',
                                placeholder: "Please input your Name",
                                className: "form-control",
                                name: 'name',
                                onChange: (e) => this._onInputChange('name', e)
                            }),
                            React.createElement("input", {
                                type: 'text',
                                placeholder: "Please input your Mobile number",
                                className: "form-control",
                                name: 'mob',
                                onChange: (e) => this._onInputChange('mob', e)
                            }),
                            React.createElement("input", {
                                type: 'text',
                                placeholder: "Please input your Email",
                                className: "form-control",
                                name: 'email',
                                onChange: (e) => this._onInputChange('email', e)
                            }),
                            React.createElement("textarea", {
                                placeholder: "Please your message",
                                className: "form-control",
                                name: 'mess',
                                onChange: (e) => this._onInputChange('mess', e)
                            }),
                            React.createElement("button", {
                                type: 'button',
                                className: "btn btn-primary",
                                onClick: () => this._onSubmit()
                            }, "submit"),

                        ])
                    ])
                ]),

            ])
        );
    }
```

另外，将`ReactDom.render`调用更新为:

```
ReactDOM.render(
    React.createElement(Root, null),
    document.getElementById('app')
);
```

**为什么我的表格没有提交？**即使执行了我们认为必要的每个步骤并交叉检查了我们的代码，您的表单仍有可能无法提交，我们该如何解决这个问题？

*   确保您的控制台没有抛出错误
*   确保单击和更改事件被正确绑定
*   交叉检查`axios`库或您用于 post 请求的库是否被引用

### 使用 HyperApp 提交表单

HyperApp 是一个用于构建 web 应用程序的 JavaScript 微框架。这个框架极大地减少了您需要理解的概念，以便在保持与其他框架同等水平的同时保持高效。

HyperApp 在管理状态时坚持使用函数式编程，但采取了一种务实的方法来考虑副作用、异步操作和 DOM 操作。

首先，让我们定义我们的 HTML 结构。创建一个名为`hyper.html`的文件，并添加:

```
<link href="//maxcdn.bootstrapcdn.com/bootstrap/3.3.0/css/bootstrap.min.css" rel="stylesheet" id="bootstrap-css">
<script src="https://unpkg.com/hyperapp"></script>
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
<div class="container" id="app">
</div>
```

上面的代码片段是一个基本的 HTML 声明，其中我们:

*   需要引导 CSS 库
*   需要 Hyperapp JavaScript 库
*   需要 Axios JavaScript 库，这个库将发出`POST`请求
*   声明了一个 id 为`app`的 div，这将是我们的根组件

**向应用程序介绍 Hyperapp**我们有了一个基本设置，其中包含所需的可用库和 Hyperapp 将附加到的根元素。让我们继续 react 实现。打开脚本标签并输入:

```
const h = hyperapp.h;
    const app = hyperapp.app;
    const state = {
      form: {
              name: '',
              mob: '',
              email: '',
              mess: '',
            }
    }

    const actions = {
      onInputChange: (event) => state => {
        state.form[event.target.name] = event.target.value;
        return state;
      },
      submitForm: () => {
        console.log(state.form)
    axios.post('https://httpbin.org/anything', state.form)
          .then(function (response) {
          console.log(response.data);
        })
          .catch(function (error) {
          console.log(error);
        });
      }
    }

    const view = (state, actions) => (
      h("div", {class: 'row'}, [
        h("div", {class: 'col-md-4'}, [
          h("div", {class: 'panel'}, [
            h("h4", {}, 'Quick Contact'),
            h("div", {class: 'form'}, [
              h("input", {type: 'text', placeholder: "Please input your Name", class:"form-control", 
                          name: 'name',
                         oninput: (e)=>actions.onInputChange(e)}),
              h("input", {type: 'text', placeholder: "Please input your Mobile number", class:"form-control", 
                          name: 'mob',
                         oninput: (e)=>actions.onInputChange(e)}),
              h("input", {type: 'text', placeholder: "Please input your Email", class:"form-control", 
                          name: 'email',
                         oninput: (e)=>actions.onInputChange(e)}),
               h("textarea", {placeholder: "Please your message", class:"form-control",
                              name: 'mess',
                         oninput: (e)=>actions.onInputChange( e)}),
              h("button", {type: 'button', class:"btn btn-primary", 
                         onclick: ()=>actions.submitForm()}, "submit"),

            ])
          ])
        ]),
      ])
    )
    app(state, actions, view, document.getElementById('app'))
```

让我们回顾一下上面的内容。在这里，我们声明了一个包含表单对象的初始状态，然后继续声明两个动作，我们将在输入改变时设置状态并提交表单。

在`onInputChange`函数中，我们收到一个参数，它是:

*   事件:发生的变更事件

我们使用这两个参数来设置被改变的确切输入的状态。

在`_onSubmit`函数中，我们向`[https://httpbin.org/anything](https://httpbin.org/anything)`端点发出一个 post 请求，后者返回发送的确切参数。在这里，这是我们用来作为我们的服务器。

在这里，我们一定看到了 React 和 Hyperapp 的相似之处。出于我们的目的，我将 Hyperapp 描述为 React 的一个轻量级替代方案。

在上面代码的 render 函数中，我们会注意到 React 的确切相似之处。事实上，你会注意到的唯一区别是用`class`代替了 React 的`className`和`onInput`代替了`onChange`。

> *出于同样的原因，我们没有在 React 表单中使用* `*jsx*` *，同样的原因，我们也没有在这里使用* `*jsx*` *。如果您使用* `*npm*` *包并且更喜欢使用* `*jsx*` *，请随意。*

### 结论

在本教程中，我们看到了使用 3 种不同的 JavaScript 框架提交表单是多么容易。我们还看到了当我们的表单没有按预期显示或提交时如何解决常见问题。你对本教程有什么看法或观点想分享吗？请在评论中告诉我们。

## [LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。