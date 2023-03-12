# 如何使用 CSS 网格构建一个响应式的网页布局

> 原文：<https://blog.logrocket.com/how-to-use-css-grid-to-build-a-responsive-web-layout/>

CSS 网格布局是一个二维网格系统，旨在帮助 web 开发人员将元素分为列和行，以便为 web 应用程序创建一致和无缝的布局。

CSS Grid 背后的逻辑是，如果开发人员获取一个像`div`这样的元素并在其中显示一个网格，他们可以将该元素分成列和行——统称为轨道——可以在网格中获取和放置项目。有了 CSS 网格，这一切都可以在不使用定位属性的额外工作下完成(`top`、`right`、`left`、`bottom`)。

有些情况下会使用 CSS 框架，有些情况下会使用 CSS 网格，但是和 web 开发中的大多数事情一样，[这取决于您的用例](https://blog.logrocket.com/css-grid-or-css-frameworks/)。

在本文中，我们将关注使用行、列和区域的基本设计，以使用 CSS Grid 构建一个简单的响应式 web 应用程序。

## CSS 网格:基本术语

让我们先花点时间来理解 CSS Grid 的一些核心基础知识。

*   网格容器–容纳网格的盒子。这是 CSS 网格的主要构建块
*   网格单元–网格上的一个接一个的单元
*   网格区域–单个单元格或多个单元格，呈正方形或长方形(但不是 L 形)
*   网格轨迹–行和列的集合，使用`grid-template-columns`和`grid-template-rows`属性定义
*   网格间隙——帮助在网格上创建空间。网格间隙上或网格间隙内不能有内容。
*   显式网格–由您使用`grid-template-columns`和`grid-template-rows`定义
*   隐式网格–由浏览器为您定义

## CSS 网格入门

### 显示网格容器和元素

网格容器是在元素上显示网格的起点。要开始使用网格，我们必须首先使用下面的语法在容器上显示网格:

```
display: grid;
```

在 CSS 网格中，网格容器和它的项目(元素)之间的关系分别是父和子的关系。每个网格都有一个包含项目的容器，放置在网格上的每个元素都是一个网格项目。

当您将一个容器作为网格(`display: grid;`)时，它的所有直接子容器都会自动成为网格项目。

```
<div class="container"> <div class ="item item1">1</div> <div class = "item item2">2</div> </div> .container{ display: grid; }
```

但是将项目显示为网格本身并没有多大作用。这是其他概念如`grid-template-columns`和`grid-template-rows`发挥作用的地方，正如你将在下面的演示应用代码片段中看到的。

### 使用`fr`单位和`repeat()`符号

虽然我们在定义网格时总是可以使用固定的度量单位，比如`px`，CSS Grid layout 也引入了一个新的度量单位，称为`fr`单位。

fr 单元表示网格容器中可用空间的一部分，可用于创建灵活的网格轨迹。

这里有一个例子:

```
.container {
display: grid;
grid-template-columns: 2fr 1fr 1fr;
}
```

上面的代码片段将把可用空间分成四个部分:两个部分将被分配给第一个轨道，而剩余的两个部分将根据剩余的可用空间各分配一个轨道。这有助于确保构建网格时的一致性。

`repeat()`符号也有助于确保一致性，可用于重复曲目列表的全部或部分，如下所示:

```
.container {
display: grid;
grid-template-columns: repeat(3, 1fr);
}
```

## 演示:使用 CSS 网格构建 web 应用程序

参见 [CodePen](https://codepen.io) 上查尔斯·弗里伯恩([@弗里伯恩查尔斯](https://codepen.io/freeborncharles) )
的 Pen [响应式网格演示](https://codepen.io/freeborncharles/pen/ZEpVLzR)。

我已经在这里包含了完整的 CodePen，但是在下面，我将使用一些内部注释来分解代码片段，以展示如何使用 CSS Grid 构建 web 应用程序。

### 超文本标记语言

首先，我们将创建一个简单的 HTML 模板，并用一些虚拟文本填充它，以帮助建立一些结构:

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset=utf-8>
  <title>Demo Responsive CSS Grid Site</title>
  <link rel="stylesheet" href="/main.css">
</head>

<!-- Main body of the site where we have the container --> 

<!-- this div contain all elements that will be visible on the screen -->

<div class="container">     
  <nav>
    <ul>
      <li><a href="#">Home</a></li>
      <li><a href="#">About</a></li>
      <li><a href="#">Contact</a></li>
    </ul>  
  </nav>
  <main><h2>A Demo Site showcasing CSS Grid</h2>
    <p>Lorem ipsum dolor sit, amet consectetur adipisicing elit. Itaque pariatur possimus alias quod ratione incidunt dicta assumenda repudiandae optio eveniet, quisquam fuga! Nam eaque fuga similique quia, esse non libero?</p>
    <p>Lorem ipsum dolor sit amet consectetur, adipisicing elit. Illo libero doloremque, eum quis laudantium hic iste ab sed ipsum veniam, quam dolor rem cupiditate corrupti aliquam repudiandae officia soluta impedit!</p>
  </main>
  <div class="sidebar img"><h2>Find Me on Social Media</h2>
    <p><a href="https://twitter.com/charliecodes">Twitter</a></p>
    <p><a href="https://www.linkedin.com/in/charleseteure/">LinkedIn</a></p>
  </div>
  <div class="about"><h2>About </h2>
    <p>Lorem ipsum, dolor sit amet consectetur adipisicing elit. Deserunt voluptatem reprehenderit non. Magni sit alias quia, vel quidem autem quos optio quam at porro aliquid necessitatibus aut et eos nulla.</p>
  </div>
  <div class="contact"><h2>Contact</h2>
    <p>Lorem ipsum dolor sit amet consectetur, adipisicing elit. Animi consequuntur magnam ipsum, commodi quam, non dolorem numquam veritatis, qui nam voluptas asperiores neque magni. Placeat, natus reprehenderit. Fugit, voluptatum commodi.</p>
  </div>
  <footer>Built with <3 by <a href="https://twitter.com/charliecodes">Charles Freeborn</a></footer>
</div>
</html>
```

### 半铸钢ˌ钢性铸铁(Cast Semi-Steel)

这里，我们将样式表(CSS)链接到 HTML 模板，并使用 CSS Grid 实现布局。我们在代码片段中添加了一些注释，以帮助理解。

```
body{
  background: #F1F0EE;
  margin: 0;
}

.container {
   /* first, create a grid container */

  display: grid;        

  /* define the amount and size of each column. Here we define 4 columns with equal fractions  */

  grid-template-columns: repeat(4, 1fr); 

  /* create horizontal tracks as rows and here we create 4 rows with different sizes  */
  grid-template-rows: 0.2fr 1.5fr 1.2fr 0.8fr; 

/* define where each element will be in the grid. We achieve this with grid-template-areas */

  grid-template-areas:
    "nav nav nav nav"
    "main main main main"
    "about about about sidebar"
    "contact contact contact sidebar"
    "footer footer footer footer";

/* we can set gaps - gutters between the rows and columns*/

  gap: 0.5rem;
  height: 100vh;
  font-weight: 800;
  font-size: 12px;
  color: #004d40;
  text-align: center;
}
 /* Styling the Nav starts here */

nav{
  background-color: #3770F6;
  grid-area: nav;
}

nav ul{
  list-style: none;
  font-size: 16px;
  font-weight: bolder;
  float: left;
}

li{
  display: inline-block;
}

li a{
  color: #ffffff;
}
  /* Styling the Nav ends here */

a:hover{
  color: #FF7F50;
}

main {
  grid-area: main;
}

main h2 {
  font-weight: bolder;
}

main p{
text-align: left;
}

.sidebar {
  background: #D3D4D7;
  grid-area: sidebar;
}

.about {
  background: #D7D6D3;
  grid-area: about;
}

.contact {
  background: #BDBCBB;
  grid-area: contact;
}

footer {
  background-color: #3770F6;
  grid-area: footer;
  color: #ffffff;
}

footer a {
  color: #ffffff;
}

a {
  text-align: center;
  display: block;
  font-family: inherit;
  text-decoration: none;
  font-weight: bold;
  margin: 1rem;
}

/* For */

@media only screen and (max-width: 550px) {
  .container {
    grid-template-columns: 1fr;
    grid-template-rows: 0.4fr 0.4fr 2.2fr 1.2fr 1.2fr 1.2fr 1fr;
    grid-template-areas:
      "nav"
      "sidebar"
      "main"
      "about"
      "contact"
      "footer";
  }
}

```

## CSS 网格:关键要点

在 CSS 中有很多方法可以实现类似的目标。使用 CSS Grid[只是将元素放入行和列中的一种方式](https://blog.logrocket.com/flexbox-vs-css-grid/)来设计具有用户友好界面的一致、无缝的 web 应用程序。

关于 CSS 网格的更多内容，我推荐 [W3 CSS 网格布局模块](https://www.w3.org/TR/css-grid-1/)和 [MDN CSS 网格 web 文档。](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Grid_Layout/Basic_Concepts_of_Grid_Layout)

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用情况、内存使用情况等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。