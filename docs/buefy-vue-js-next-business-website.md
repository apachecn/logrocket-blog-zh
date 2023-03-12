# 为什么你应该使用 Buefy 和 Vue.js 作为你的下一个商业网站

> 原文：<https://blog.logrocket.com/buefy-vue-js-next-business-website/>

当建立商业网站时，有许多因素需要考虑:

*   速度
*   简洁的用户界面设计
*   航行
*   可用性
*   行动呼吁
*   表演
*   响应性
*   搜索引擎优化
*   浏览器兼容性

…还有更多。

所有这些因素共同决定了用户访问你的网站时的体验。在本文中，我们将使用 Buefy 和 Vue.js 为一家健身房构建一个简单的网站，涵盖上述所有因素。

## view . js-检视. js

Vue.js 是一个 JavaScript 模型-视图-视图模型(MVVM)库，用于构建前端应用程序和单页面应用程序(spa)。它是用于构建前端应用程序的最流行的 JavaScript 库之一。

自 Vue.js 于 2014 年 2 月首次发布以来，Grammarly、GitLab、Behance 和 Louis Vuitton 等公司就是使用 vue . js 构建其产品和应用程序的例子。

### 设置 Vue.js

Vue.js 设置起来非常简单。使用 npm，从您的终端安装:

```
$ npm install vue
```

要安装 CLI:

```
$ npm install -g @vue/cli
```

现在我们有了 vue-cli，我们需要创建一个 vue 项目。运行以下命令:

```
$ vue create gym-website
```

出现提示时，确保选择默认预设。这将安装 Babel、ESLint 和其他附加的依赖项。完成后，切换到您的项目目录:

```
cd gym-website
```

然后在您的本地主机上服务项目:

```
npm run serve
```

您应该会看到一个类似于下图的用户界面:

![Default Vue Project UI](img/b1eca17db639af05285cd23efddd08c1.png)

现在，我们准备开始开发我们的健身房业务网站。

## 布埃菲

Buefy 是一个基于布尔玛的 Vue.js 轻量级 UI 组件库。在撰写本文时，GitHub 上有超过 200，000 名开发人员使用了超过 40，000 颗星，布尔玛是一个以 Flexbox 为核心的 CSS 框架。

要从终端安装 Buefy，请运行:

```
$ npm install buefy
```

### 项目文件夹结构

Vue.js 文件夹结构相当容易理解。在本教程中，我们将使用`src`文件夹。

`src`文件夹包含`assets`文件夹，它将保存我们的项目资产，如图像、视频、音频等。创建一个名为`img`的文件夹来存放我们的图片，然后下载这个[链接](https://www.dropbox.com/sh/lf36m5nku16f9tl/AAAxy6e-DJF9hBlrOr29iJGqa?dl=0)中的内容。

`components`文件夹是我们项目的组件所在的目录。其中包括:

*   导航栏组件
*   英雄成分
*   证明部分
*   功能组件
*   时事通讯组件
*   页脚组件

`public`文件夹包含将要提供的 HTML 文件。`App.vue`是我们应用程序的主要组件，`main.js`是我们应用程序的主要入口。

## 用 Buefy 构建我们的站点

### `Index.html`

`index.html`文件是 HTML 中的入口点，为 Vue.js 提供一个元素来加载，并导入`main.js`来初始化您的应用程序。对于本教程，修改你的`index.html`,如下所示:

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Gym Service</title>
    <link rel="stylesheet" href="styles.css">
     <script defer src="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.11.2/js/all.js"></script>
  </head>
  <body>
    <div id="app"></div>
    <!-- built files will be auto injected -->
  </body>
</html>
```

从上面的代码块中，我们给我们的网页命名为“Web Service ”,并添加了 script 标签以在我们的页面上使用字体 Awesome，我们将使用它在我们的页面上显示图标。

### `Styles.css`

对于本教程，我已经创建了这个项目所需的样式。您可以复制并粘贴以下内容，并将其作为`styles.css`保存在您的`public`文件夹中:

```
.has-text-green {
    color: #1E4132 !important;
}
.has-text-white {
    color: #FFFFFF !important;
}
.has-company-bg {
    background-color: #009999;
}
.has-green-bg {
    background-color: #009999;
}
.has-white-company-gradient {
    background: rgb(255, 255, 255);
    background: -moz-linear-gradient(180deg, rgba(255, 255, 255, 1) 0%, rgba(255, 255, 255, 1) 50%, #006666 50%, #006666 100%);
    background: -webkit-linear-gradient(180deg, rgba(255, 255, 255, 1) 0%, rgba(255, 255, 255, 1) 50%, #006666 50%, #006666 100%);
    background: linear-gradient(180deg, rgba(255, 255, 255, 1) 0%, rgba(255, 255, 255, 1) 50%, #006666 50%, #006666 100%);
    filter: progid:DXImageTransform.Microsoft.gradient(startColorstr="#ffffff", endColorstr="#006666", GradientType=1);
}
@media(max-width: 1024px) {
    .has-white-company-gradient {
        background: rgb(255, 255, 255);
        background: -moz-linear-gradient(180deg, rgba(255, 255, 255, 1) 0%, rgba(255, 255, 255, 1) 25%, #006666 25%, #006666 100%);
        background: -webkit-linear-gradient(180deg, rgba(255, 255, 255, 1) 0%, rgba(255, 255, 255, 1) 25%, #006666 25%, #006666 100%);
        background: linear-gradient(180deg, rgba(255, 255, 255, 1) 0%, rgba(255, 255, 255, 1) 25%, #006666 25%, #006666 100%);
        filter: progid:DXImageTransform.Microsoft.gradient(startColorstr="#ffffff", endColorstr="#006666", GradientType=1);
    }
}
/*
Typography 
*/
@import url('https://fonts.googleapis.com/css?family=Calistoga|Open+Sans&display=swap');
h1,
h2,
h3 {
    font-family: 'Calistoga';
    margin-bottom: 10px;
    color: #FFFFFF;
}
p,
a,
span {
    font-family: 'Open Sans', sans-serif;
    font-weight: 400;
}
@media(max-width: 767px) {
    p {
        max-width: 90%;
    margin: 0 auto;
    }
}

/*
Links
*/
.a-menu {
    letter-spacing: 3px;
    text-transform: uppercase;
    transition: opacity 1s ease;
    opacity: 0.8;
}
.a-menu:hover {
    opacity: 1;
    color: #009999 !important;
    text-decoration: none;
}
/* 
Wrapper
*/
.section .container,
.hero .hero-body,
.hero .hero-head {
    width: 1200px;
    max-width: 100%;
    margin: 0 auto;
}
/*
Some aesthetic improvements
*/
nav {
    padding-top: 20px;
    max-height: 50px;
}
.paragraph {
    width: 600px;
    line-height: 1.5em;
}
/*
Hero
*/
.burger {
    position: absolute;
    top: 0;
    right: 0;
}
@media(min-width: 1024px) {
    .hero-body {
        background-image: url('../src/assets/img/gym.svg');
        background-size: 30%;
        background-position: 80% 50%;
        background-repeat: no-repeat;
    }
    .hero-body p {
        width: 600px;
    }
}
/*
Button
*/
.btn {
    margin-top: 20px;
    border-radius: 50px;
    border: none;
    padding: 10px 30px;
    -webkit-box-shadow: 0px 0px 15px 0px #006666;
    -moz-box-shadow: 0px 0px 15px 0px #006666;
    box-shadow: 0px 0px 15px 0px #006666;
    transition: opacity 1s ease;
    opacity: 1;
    cursor: pointer;
}
.btn:hover {
    opacity: 0.8;
}
/*
Twitter Feed
*/
#twitter-feed .columns {
    margin: 0px;
}
@media(max-width: 1024px) {
    margin: 0px 10px;
}
.box {
    padding-top: 30px;
    padding-bottom: 30px;
}
.box .icon {
    margin-top: -10px;
    color: #009999 !important;
    transition: color 0.5s ease;
}
.box .icon:hover {
    color: #009999 !important;
}
#twitter-feed .container .is-size-7 {
    padding: 20px;
}
/*
Features
*/
.feature-img {
    max-width: 60%;
    margin: 0 auto;
}
.column {
    display: flex;
    justify-content: center;
    flex-flow: column;
    text-align: center;
}
.column h3,
.column p {
    align-self: center;
}
@media(max-width: 1024px) {
    .reverse-row-order {
        flex-direction: column-reverse;
        display: flex;
    }
}
/*
Newsletter
*/
#newsletter {
    text-align: center;
}
#newsletter p {
    width: 600px;
    margin: 0 auto;
}
.social-link {
    padding: 15px;
    color: #FFFFFF !important;
}
.newsletter-input {
    border: none;
    border-bottom: 2px solid #009999;
    border-radius: 0% !important;
    box-shadow: none;
    text-align: center;
}
#newsletter .field {
    width: 400px;
    max-width: 80%;
    padding: 20px;
    margin: 0 auto;
}
/*
Footer
*/
#footer p {
    padding: 20px;
}
#footer a {
    color: #FFFFFF !important;
}
```

在项目文件夹结构中，`styles.css`包含了我们网站不同部分的多个定制样式规则。

### `Main.js`

文件是我们应用程序的主要入口。它将根组件初始化为页面上的一个元素。它还负责设置将在我们的应用程序中使用的插件和第三方组件。

要在我们的应用程序中使用 Buefy，我们需要导入并指定 Vue.js 使用 Buefy。将它复制并粘贴到您的`main.js`文件中:

```
import Vue from 'vue'
import App from './App.vue'
import Buefy from 'buefy'
import 'buefy/dist/buefy.css'
Vue.config.productionTip = false
Vue.use(Buefy)
new Vue({
  render: h => h(App),
}).$mount('#app')
```

`buefy.css`最近被移到了一个新目录。如果您碰巧使用的是 Buefy 的旧版本而不是最新版本，您可能会在`buefy/lib/buefy.css`路径中找到`buefy.css`文件。

### 导航栏组件

在`components`文件夹中创建一个名为`navbar`的文件夹，并将下面的代码粘贴到一个新文件`navbar.vue`中:

```
<template>
    <section id="hero" class="hero is-medium">
        <div class="hero-head">
            <b-navbar>
                <template slot="brand">
                    <b-navbar-item tag="router-link" :to="{ path: '/' }">
                        <h3 class="logo has-text-green is-size-4">Gym Service</h3>
                    </b-navbar-item>
                </template>
                <template slot="end">
                    <b-navbar-item tag="div">
                        <div class="buttons">
                            <a class="a-menu is-size-7 navbar-item">
                                home
                            </a>
                            <a class="a-menu is-size-7 navbar-item">
                                about
                            </a>
                            <a class="a-menu is-size-7 navbar-item">
                                services
                            </a>
                            <a class="a-menu is-size-7 navbar-item">
                                contact
                            </a>
                        </div>

                    </b-navbar-item>
                </template>
            </b-navbar>
        </div>
        <div class="hero-body">
            <div class="container">
                <h1 class="has-text-green is-size-1 is-size-3-mobile">
                    Gym Service
                </h1>
                <p class="has-text-green">
                    Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut 
                    labore et dolore magna aliquyam erat, sed diam voluptua. At vero eos et accusam et justo duo dolores et ea rebum.
                </p>
                <button class="btn has-text-white has-company-bg is-size-7 a-menu">
                    Get started
                </button>
            </div>
        </div> 
    </section>
</template>

<style>
@media(min-width: 1024px) {
    .hero-body {
        background-image: url('../../assets/img/gym.svg');
        background-size: 30%;
        background-position: 80% 50%;
        background-repeat: no-repeat;
    }
    .hero-body p {
        width: 600px;
    }
}
</style>

<script>
export default {
    name: 'Navbar',
}
</script>
```

你可能已经注意到上面的代码使用标签分成了三个部分:`template`、`style`和`scripts`标签。

`template`标签用于呈现客户端内容。我们所有的用户界面代码都将写在这个标签中。`style`标签包含特定于特定组件的 CSS 样式,`script`标签允许我们导入可能要在当前组件中使用的其他组件，定义组件的名称，并保存组件中使用的数据。

在上面的代码块中，我们在`script`标签中定义了组件名，并在`style`标签中添加了样式规则。确保在`assets`文件夹中创建图像文件夹路径`img/`。将所有要使用的图像放在文件夹中，然后可以在样式规则中指定完整的图像路径。

在`template`标签中——不包括`b-navbar`和`b-navbar-item`，它们是 Buefy UI 组件——其他标签只是我们常规的 HTML 标签和 CSS 类。

`b-navbar`组件允许我们定义一个导航栏组件，而`b-navbar-item`允许我们指定一个导航栏项目。在上面的代码块中，我们使用 Buefy 创建了一个导航栏，并使用`b-navbar-item`创建了主页、关于、服务和联系人导航栏项目。

### 奖状部分

在`components`文件夹中创建一个名为`testimonials`的文件夹，并将下面的代码粘贴到一个新文件`testimonials.vue`中:

```
<template>
    <section id="twitter-feed" class="section has-white-company-gradient">
        <div class="container">
            <div class="columns">
                <div class="column">
                    <div class="box">
                        <article class="media">
                            <div class="media-left">
                                <figure class="image is-64x64 ">
                                    <img src="../../assets/img/007.png" alt="Image">
                                </figure>
                            </div>
                            <div class="media-content">
                                <div class="content">
                                    <p>
                                        <strong>James Bond</strong> <small>@007</small> <small>10m</small>
                                        <br>
                                        Gym Service is the best place to be if you're looking for gains and want to pump some muscles. I love it here!
                                    </p>
                                </div>
                                <nav class="level is-mobile">
                                    <div class="level-left">
                                        <a class="level-item" aria-label="reply">
                                                <b-icon
                                                    pack="fas"
                                                    icon="reply"
                                                    size="is-small">
                                                </b-icon>
                                        </a>
                                        <a class="level-item" aria-label="retweet">
                                            <b-icon
                                                pack="fas"
                                                icon="retweet"
                                                size="is-small">
                                            </b-icon>
                                        </a>
                                        <a class="level-item" aria-label="like">
                                            <b-icon
                                                pack="fas"
                                                icon="heart"
                                                size="is-small">
                                            </b-icon>
                                        </a>
                                    </div>
                                </nav>
                            </div>
                        </article>
                    </div>
                </div>
                <div class="column">
                    <div class="box">
                        <article class="media">
                            <div class="media-left">
                                <figure class="image is-64x64 ">
                                    <img src="../../assets/img/alice.png" alt="Image">
                                </figure>
                            </div>
                            <div class="media-content">
                                <div class="content">
                                    <p>
                                        <strong>Alice</strong> <small>@alice</small> <small>15m</small>
                                        <br>
                                        Looking to burn that belly fat? Gym Service diet plans and instructors help you to achieve that effectively in the shortest time possible.
                                    </p>
                                </div>
                                <nav class="level is-mobile">
                                    <div class="level-left">
                                        <a class="level-item" aria-label="reply">
                                                <b-icon
                                                    pack="fas"
                                                    icon="reply"
                                                    size="is-small">
                                                </b-icon>
                                        </a>
                                        <a class="level-item" aria-label="retweet">
                                            <b-icon
                                                pack="fas"
                                                icon="retweet"
                                                size="is-small">
                                            </b-icon>
                                        </a>
                                        <a class="level-item" aria-label="like">
                                            <b-icon
                                                pack="fas"
                                                icon="heart"
                                                size="is-small">
                                            </b-icon>
                                        </a>
                                    </div>
                                </nav>
                            </div>
                        </article>
                    </div>
                </div>
            </div>

        </div>
    </section>
</template>

<script>
export default {
    name: 'Testimonials'
}
</script>
```

见证部分用于展示已经使用我们健身房服务的会员的见证。这让新访客和潜在客户看到我们服务的好处和功效。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

我们已经在`script`标签中定义了组件名。在上面的`template`标签中，是我们的证明书部分的代码。我们用卡片来展示奖状。我们还使用 Buefy `b-icon`组件来定义呈现的图标。

属性用来定义我们正在使用的图标包——在我们的例子中是字体 Awesome，但是请注意 Buefy 支持多个图标包。`icon`属性用于从字体 Awesome 包中选择我们想要的图标，`size`用于指定图标的大小。选项有`is-small`、`is-medium`和`is-large`。

### 功能部分

在`components`文件夹中创建一个名为`features`的文件夹，并将下面的代码粘贴到一个新文件`features.vue`中:

```
<template>
    <section id="features" class="section has-company-bg">
        <div class="container">
            <div class="columns reverse-row-order">
                <div class="column">
                    <h3 class="is-size-3">
                        Personal Instructors
                    </h3>
                    <p class="has-text-white paragraph">
                        Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua. At vero eos et accusam et justo duo dolores et ea rebum.
                    </p>
                </div>
                <div class="column">
                    <img class="feature-img" src="../../assets/img/trainer.svg" alt="feature" />
                </div>
            </div>
        </div>
        <div class="container">
            <div class="columns">
                <div class="column">
                    <img class="feature-img" src="../../assets/img/equip.svg" alt="feature" />
                </div>
                <div class="column">
                    <h3 class="is-size-3">
                        State of the art equiptments</h3>
                    <p class="has-text-white paragraph">
                        Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua. At vero eos et accusam et justo duo dolores et ea rebum.
                    </p>
                </div>
            </div>
        </div>
        <div class="container">
            <div class="columns reverse-row-order">
                <div class="column">
                    <h3 class="is-size-3">
                        Free diet plans</h3>
                    <p class="has-text-white paragraph">
                        Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua. At vero eos et accusam et justo duo dolores et ea rebum.
                    </p>
                </div>
                <div class="column">
                    <img class="feature-img" src="../../assets/img/diet.svg" alt="feature" />
                </div>
            </div>
        </div>
    </section>
</template>
<script>
export default {
    name: 'Features'
}
</script>
```

功能部分用于显示我们向当前和潜在客户提供的所有核心服务。我们已经在`script`标签中定义了组件名。

### 时事通讯部分

在`components`文件夹中创建一个名为`newsletter`的文件夹，并将下面的代码粘贴到一个新文件`newsletter.vue`中:

```
<template>
    <section id="newsletter" class="section">
            <div class="container">
                <h3 class="has-text-green is-size-3">
                    Newsletter</h3>
                <p class="has-text-green">
                    Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua. At vero eos et accusam et justo duo dolores et ea rebum.
                </p>
                <div class="field newsletter-field">
                    <div class="control">
                        <b-input v-model="email" placeholder='[email protected]'></b-input>
                    </div>
                </div>
                <button rounded class="button is-medium" @click="success">
                    Subscribe
                </button>
            </div>
        </section>
</template>
<script>
export default {
    name: 'Newsletter',
    methods: {
        success() {
            this.$buefy.toast.open({
                message: 'We have sent you a confirmation email!',
                type: 'is-success'
            })
        },
        fail() {
            this.$buefy.toast.open({
                duration: 5000,
                message: `Something went wrong`,
                position: 'is-bottom',
                type: 'is-danger'
            })
        }
    }
}
</script>
```

简讯部分允许我们的访问者进一步参与网站，这一次，我们将要求访问者的电子邮件地址与我们保持联系。

我们已经在`script`标签中定义了组件名。在上面的`template`标签中，是我们新闻通讯部分的代码。我们将使用 Buefy `b-input`组件从我们的访问者那里获得输入。

`b-input`有一个名为`v-model`的属性，它允许我们绑定一个类型为`email`的值。我们还将使用`placeholder`属性指定一个占位符，只是为了让用户知道我们正在等待一个电子邮件地址以及该电子邮件地址应该采用的格式。

订阅`button`是一个 Buefy 组件，并被绑定到`script`中的`success()`方法。`success`方法使用 Buefy `toast`组件打开一个 toast 消息窗口，当访问者输入他们的电子邮件时，向他们提供成功或失败的反馈。Buefy `toast`组件允许您指定消息和消息类型。

### 页脚

在`components`文件夹中创建一个名为`footer`的文件夹，并将下面的代码粘贴到一个新文件`footer.vue`中:

```
<template>
    <section id="footer" class="section has-green-bg">
        <div class="container has-text-centered">
            <a href="https://twitter.com" class="is-size-4 social-link">
                <i class="fab fa-twitter"></i>
            </a>
            <a href="https://github.com/" class="is-size-4 social-link">
                <i class="fab fa-github"></i>
            </a>
            <p class="has-text-white is-size-7">
                <i class="fas fa-copyright"></i> <b>{{msg}}, Gym Service</b>
            </p>
        </div>
    </section>
</template>
<script>
export default {
    name: 'Footer',
    props: {
        year: String
    }
}
</script>
```

页脚部分是我们页面的最后一部分。本部分包含外部链接和信息。

我们已经在`script`标签中定义了组件名。在`script`中，我们已经定义了一个道具。属性是组件中使用的自定义属性。当一个值被传递给一个`prop`属性时，它就成为该组件实例的一个属性。

我们已经创建了一个道具类型为`String`的`year`道具，方便我们更改版权年。为了渲染传递给`year`道具的任何值，我们简单地使用`{year}`插入到我们的`templates`中，传递的任何值都将被相应地渲染。

### `App.vue`

`App.vue`是我们应用程序的根。它通常用于定义我们页面的模板。

```
<template>
  <div>
    <Navbar/>
    <Testimonials/>
    <Features/>
    <Newsletter/>
    <Footer year="2020"/>
  </div>
</template>
<script>
import Navbar from './components/navbar/navbar'
import Testimonials from './components/testimonials/testimonials'
import Features from './components/features/features'
import Newsletter from './components/newsletter/newsletter'
import Footer from './components/footer/footer'
export default {
  name: 'App',
  components: {
    Navbar,
    Testimonials,
    Features,
    Newsletter,
    Footer,
  }
}
</script>
<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
```

在上面的代码块中，我们已经从`components`文件夹中导入了所有的组件，并相应地在`templates`中呈现了它们。我们还在我们的`script`标签内注册了所有进口组件。

在页脚组件中，我们将一个值`2020`传递给在`footer.vue`组件中已经定义好的属性。一旦页面被加载，传递给 props 的值将被插入并呈现在我们的页脚组件中。

## 运营我们的网站

现在，我们已经为我们的健身房建立了简单的业务登录页面，我们可以继续下去，看看它现在是什么样子。在您的终端中，键入:

```
npm run serve
```

如果一切顺利，那么在`[http://localhost:8080](http://localhost:8080)`继续在浏览器中加载本地 URL。您应该会看到一个类似下图的网站:

![Our Completed Gym Service Page](img/389206acd9ea439128c96a31b386eee6.png)

## 在生产中部署

现在，我们已经做好了一切工作，我们需要部署我们的网站，并使它活起来:

```
npm run serve 
```

这将创建一个名为`dist`的文件夹。`dist`文件夹将包含准备在生产环境中部署的网站。你可以按照这个[指南](https://www.netlify.com/blog/2019/11/30/how-to-deploy-a-vue-site/)在 Netlify 上部署。

## 结论

正如你所看到的，使用 Buefy 和 Vue.js 来建立一个商业网站是很容易做到的。使用 Buefy 的 UI 组件节省了我们的时间，并帮助我们建立具有干净和现代设计的网站。仍然有很多 Buefy 组件我们没有在本教程中回顾，但是你可以前往 Buefy 组件的官方文档并建立更多令人惊奇的网站。

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。