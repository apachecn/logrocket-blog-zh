# 使用 CSS 设计 Vue.js 应用程序的样式

> 原文：<https://blog.logrocket.com/styling-a-vue-js-application-using-css/>

使用 CSS 设计 Vue 组件的样式可以帮助开发人员将设计美学添加到他们的应用程序中，包括背景颜色、字体大小、填充、定位、动画和不同屏幕大小的响应显示。

使用 Vue 指令，您可以管理模板中的类和样式绑定。您还可以在组件中使用内联样式，或者使用外部 CSS 文件来组织我们的应用程序。

在本文中，我们将通过构建一个简单的 web 页面来探索用 CSS 样式化 Vue 组件的不同方法。

### 先决条件

在阅读本教程之前，有几件事你应该检查。首先，你需要一个代码编辑器，最好是 [Visual Studio 代码](https://code.visualstudio.com/)。然后，通过在终端中运行以下命令，检查是否安装了 Node.js 版本 10.x 或更高版本:

```
:node -v
```

你还需要 Vue 最新的命令行界面。要获取最新版本，请先卸载旧版本的 CLI:

```
npm uninstall -g @vue/cli
 id="or">#or
yarn global remove @vue/cli
```

然后，安装最新版本:

```
npm install -g @vue/cli
# OR
yarn global add @vue/cli

```

或者，您可以像这样更新版本:

```
npm update -g @vue/cli

# OR
yarn global upgrade --latest @vue/cli
```

此演示的回购也可以在[这里](https://github.com/amycruz97/managing-styes-in-vue)找到。我还建议查看一下最近的[顶级 Vue 组件库列表](https://blog.logrocket.com/top-10-vue-component-libraries-for-2020/)，以便进一步阅读。

## 设置您的 Vue 项目

要创建新项目，请运行:

```
vue create <projectname>

```

然后会要求您选择一个预设。您可以选择:

1.  一个基本的 Babel + ESLint 设置附带的默认预设
2.  Vue 3 预览版，或者
3.  选择“手动选择功能”以选择您需要的功能

接下来，我们将更改目录:

```
cd <projectname>

```

我们将自己设置为在本地主机中查看:

```
npm run serve

or

yarn serve

```

## 使用`scoped`属性在 Vue 中设置样式

附在下面的`style`标签上的`scoped`属性意味着这里定义的任何 CSS 样式只应用于这个模板，而不会应用于组件/模板之外。

首先，创建一个`navbar`组件，命名为“Navbar”:

```
// @/components/Navbar.vue

<template>
    <div class="navbar">

        <div class="navLink">
            <a href="#">About</a>
            <a href="#">Services</a>
            <a href="#">Contact</a>
        </div>
    </div>
</template>
<script>
export default {
    name: 'Navbar'
}
</script>
<style scoped>
.navbar{
    background: #f44336;
    padding: 1rem;
    font-size: 1.5rem;
    border-bottom: 1px solid white;

}
.navLink{
text-align: center;
}
a{
    text-decoration: none;
    padding: 1rem;
    color:#fff;
    text-align: center;
}
@media only screen and (max-width: 600px) {
    .navLink{
        display: flex;
        flex-direction: column;
    }
}
</style>
```

在上面的例子中，我们创建了一个`navbar`组件。在里面，我们使用了一个`scoped`属性来设计导航条的样式。这意味着这里定义的所有 CSS 样式将只应用于`navbar`组件。

## 与外部 CSS 文件链接

随着我们的应用程序随着大量 CSS 变得越来越大，我建议将 CSS 样式分离到一个外部 CSS 文件中，并将其链接到组件。这只是众多清理代码的方法之一。

这里有一个例子:

```
// @/components/Body.vue

<template>
    <div class="container"> 
        <div class="startPage">
        <h2>Cruz Page</h2>
        <button class="btn">Get started here </button>
        </div>
    </div>
</template>
<script>
export default {
    name: 'Body'
}
</script>
<style scoped src="../assets/css/startpage.css">
/* @import '../assets/css/startpage.css'; */
</style>

```

上面的组件将链接到下面的外部 CSS 文件:

```
// @/assets/css/startpage.css';

.startPage {
    height: 600px;
    background-color: #f44336;
    text-align: center;
}
h2{
    padding-top: 10rem;
    font-size: 4rem;
}
.btn {
    background: black;
    color: #fff;
    border-radius: 5px;
    -webkit-border-radius: 5px;
    -moz-border-radius: 5px;
    -ms-border-radius: 5px;
    -o-border-radius: 5px;
    outline: none;
    padding: 0.7rem 2rem;
    border: none;
    margin-top: 2rem;
}

```

当使用外部文件时，您可以通过源文件本身来链接它，或者在`style`标签中导入它。对于本例，我们链接了在 Vue 应用程序的 assets 文件夹中创建的外部 CSS 文件。

## 在 Vue.js 中使用全局样式

我们希望在应用程序的组件中应用一些样式。为了有效地做到这一点，我们将使用全局样式，而不是在一个作用域或外部文件中对它们进行样式化(尽管这样也可以)。如果你想做像`fonts`、`colors`、`background-color`、`border-radius`、`margin`这样的通用造型，全局造型是你最好的选择。

在下面的例子中，我们将在 App.vue `style`标签中添加全局样式。

```
// @/src/App.vue

<template>
  <div>
    <Navbar/>
    <Body/>
  </div>
</template>
<script>
import Navbar from './components/Navbar.vue'
import Body from './components/Body.vue'

export default {
  name: 'App',
  components: {
    Navbar,
    Body
  }
}
</script>
<style>
* {
  box-sizing: border-box;
  padding: 0;
  margin: 0;
}
h1,h2,h3,h4,h5,h6{
  color: #fff;
}
</style>

```

您会注意到，我们使用 CSS 通配符选择器(发音为星号)来选择应用程序中的所有元素。在这种情况下，所有元件上的`margin`和`padding`已经被设置为`0`，并且`box-sizing`已经被设置为`border-box`。

## 使用内嵌样式

内联 CSS 使用 style 属性将独特的样式应用于特定的 HTML 元素。

这里有一个简单的例子:

```
<h1 style="color: red; text-align: center;">I am a footer</h1>

```

在 Vue.js 中，我们可以通过在 HTML 标签中绑定 style 属性来为元素添加内联样式。作为参考，`:style`是`v-bind:style`的简写。

内联样式可以通过两种方式实现:使用对象语法或数组语法。我们将在下面讨论这两个问题。

### 对象语法

对象语法允许我们使用内联样式，将 CSS 属性名作为对象的键名，将值作为每个 CSS 属性的值。使用对象语法时，使用 camelCase 或“kebab-case”，如下例所示。

```
// @/components/Footer.vue

<template>
    <footer 
      :style="{backgroundColor: bgColor, color: textColor, 
              height: `${height}px`, textAlign: align, 
              padding: `${padding}rem`
              }">
          <p> &copy; 2020</p>
    </footer>
</template>
<script>
export default {
    data(){
        return{
            bgColor: 'black',
            textColor: 'white',
            height: 200,
            align: 'center',
            padding: 5
        }
    }
}
</script>
<style>

</style>

```

在这个实例中，我们创建了一个`footer`组件，然后使用 object 语法来样式化 footer 元素。

使用对象语法方法，直接绑定到样式对象是一个很好的实践，这样当我们的应用程序变大时，我们的模板会看起来更干净。请看下面的例子:

```
<template>
        <footer :style="footerStyles">
          <p> &copy; 2020</p>
        </footer>
</template>
<script>
export default {
    data(){
        return{
            footerStyles:{
                backgroundColor: 'black',
                color: 'white',
                height: '200px',
                textAlign: 'center',
                padding: '5rem'
            }

        }
    }
}
</script>
<style>

</style>

```

### 数组语法

内联样式的另一种方法是使用数组语法添加多个样式对象。在下面的示例中，我们将在数组语法中添加一个额外的对象— `textColor` —它将文本颜色更改为红色:

```
<template>
        <footer :style="[footerStyles1, textColor]">
        <p> &copy; 2020</p>
        </footer>
</template>
<script>
export default {
    data(){
        return{
            footerStyles1:{
                backgroundColor: 'black',
                height: '200px',
                textAlign: 'center',
                padding: '5rem'
            },
            textColor:{
                color: 'red',
            }

        }
    }
}
</script>
<style>

</style>

```

当你要像上面的例子那样添加多个样式对象时，最好使用数组语法；要动态地样式化类，对象语法是内联样式化的最佳方法。

## 结论

在本教程中，我们了解了可以对 Vue.js 应用程序进行样式化的不同方法，包括作用域样式化、链接到外部 CSS 文件、全局样式化以及使用对象和数组语法进行内联样式化。我们还制作了一个简单的网站来说明如何应用不同的样式方法。

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。