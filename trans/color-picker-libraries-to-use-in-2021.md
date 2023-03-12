# 将在 2021 年使用的颜色选择器库

> 原文：<https://blog.logrocket.com/color-picker-libraries-to-use-in-2021/>

不可否认颜色在网络上的重要性。那里的品牌是借助颜色和偏好的配色方案来识别的。幸运的是，由于 JavaScript 有无数流行的颜色库，开发者可以有多种选择。众所周知，JavaScript 中的颜色选择器库提供了对开发人员可以在他们的项目中使用的各种颜色选项或颜色代码的访问。

借助 JavaScript 中给定的颜色选择器库，您可以简单地调整 RGB(红、绿、蓝)阴影的不同频率值，以获得所需的频率或颜色方案。

在本文中，我们将浏览 JavaScript 中的几个 JavaScript 颜色选择器库的列表，以了解它们的功能，同时展示一个示例用法。最后，我们将根据库的大小以及它们对页面加载时间的相对影响来比较所有库的性能。

Bootstrap Colorpicker 是用于 Bootstrap 的领先模块化颜色选择器插件。给定的插件允许你从许多颜色中选择。您可以在最终用户可以选择颜色的任何编辑器功能或产品变体场景中轻松使用它。

如果您希望获得其最新版本，您可以通过多种方式确保获得相同的版本，包括:

在 [v2.x 文档](https://itsjavi.com/bootstrap-colorpicker/v2/)和 [v3.x 文档](https://itsjavi.com/bootstrap-colorpicker/)中也有不同版本的拾色器可用于引导。让我们看看[行动](https://tpf8l.csb.app/bootstrap_colorpicker.html)中的一个例子:

```
<!DOCTYPE html>
<html>
<head>
    <title>Bootstrap Color Picker</title>
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
    <script src="https://code.jquery.com/jquery-3.2.1.slim.min.js" integrity="sha384-KJ3o2DKtIkvYIK3UENzmM7KCkRr/rE9/Qpg6aAZGJwFDMVNA/GpGFF93hXpG5KkN" crossorigin="anonymous"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
    <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/bootstrap-colorpicker/3.2.0/css/bootstrap-colorpicker.css" rel="stylesheet">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/bootstrap-colorpicker/3.2.0/js/bootstrap-colorpicker.js"></script>
    <style type="text/css">
        body {
        width: 80%;
        height:80%;
        margin:auto;
        }
        #main {
        height:400px;
        }
        #color {
        margin-left: 10%;
        width:50%;
        }
  </style>
</head>
<body>
    <div id="main" class="container">
        <h1>Bootstrap Color Picker</h1>
        <input id="color" type="text" value="#269faf" class="form-control" />
    </div>
    <script type="text/javascript">
      $(function () {
        $('#color')
        .colorpicker({})
        .on('colorpickerChange', function (e) { //change the bacground color of the main when the color changes  
            new_color = e.color.toString()
            $('#main').css('background-color', new_color)
        })     
    });
</script>
</body>
</html>
```

React Color 是 Photoshop、Chrome、Sketch、Github、Material Design、Twitter 等工具的颜色选择器集合。多达 [13 个采色器](https://casesandberg.github.io/react-color/#examples)可供选择。使用 JavaScript 中给定的颜色选择器，web 设计人员还可以通过利用现有的构建块组件来创建自己的颜色范围。

在 NPM 的帮助下安装 [react-color 即可完成安装。同时，通过从组件顶部的 react-color 导入一些颜色选择器，然后在 render 函数中使用相同的颜色选择器，可以将颜色组件包含在给定的库中。React Color 的一些组件 API 包括:](https://github.com/casesandberg/react-color#installation--usage)

*   `color`:用于控制拾色器上保持激活的颜色。您可以利用给定的组件用特定的颜色初始化颜色选择器，或者使其与父组件的状态保持同步
*   `onChange`:每次颜色变化时，您都需要传递一个调用函数。然后，它可以用于存储父组件状态中的颜色或进行其他重要的转换

根据您选择导入的颜色选择器组件，有[个其他道具可供使用:](https://casesandberg.github.io/react-color/#api-individual)

```
import logo from './logo.svg';
import React from 'react';
import './App.css';
import { ChromePicker } from 'react-color'

class App extends React.Component {
  state = {
    background: 'rgb(0,0,0,1)',
    color:""
  };
  changeHandler = (colors) => {
    let col = 'rgb('+colors.rgb.r+','+colors.rgb.g+','+colors.rgb.b+','+colors.rgb.a+')'
    this.setState({ background: col, color:colors.rgb });
  };

  render() {
    return (
      <div id="main" style={{backgroundColor: this.state.background}}>
      <h1>React-Color Library</h1>
      <ChromePicker
        className="picker"
        color={ this.state.color }
        onChange={ this.changeHandler }
      />
      </div>
    );
  }
}
export default App;
```

Pickr 是一个简单、扁平、响应迅速、可破解的多主题 JavaScript 颜色选择器。在这个颜色选择器的帮助下，不需要使用任何依赖项或 jQuery。此外，给定的颜色选择器也与所有可用的 CSS 框架高度兼容。

众所周知，Pickr 可以帮助您为您的网站或应用程序创建一个高度优雅、可触摸、可定制的颜色选择器。给定的颜色选择器能够支持 RGB、HEX、HSV、HSL 和 CMYK 颜色代码。同时，Pickr 颜色选择器还提供了一个特定的功能，能够将默认颜色代码或值(HSVa)转换为相应的 RGBa、HSLa、CMYK 和 HEX 值。已知给定的颜色选择器为 node.js 和浏览器提供支持。

如果您希望在 JavaScript 库中使用相同的颜色选择器来添加颜色，请遵循以下步骤:

*   使用 [NPM](https://www.npmjs.com/package/@simonwep/pickr) 安装拾取器
*   将 Pickr 的 JavaScript 添加到页面中
*   向给定页面添加特定的主题 CSS
*   创建一个定义明确的容器元素来放置拾色器
*   初始化颜色选择器以生成默认颜色选择器
*   自定义触发时颜色选择器的给定位置

考虑下面给出的[示例](https://tpf8l.csb.app/pickr.html):

```
<!DOCTYPE html>
<html>
  <head>
    <title>Pickr library</title>
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.4.1/jquery.min.js"></script>
    <link
      rel="stylesheet"
      href="https://cdn.jsdelivr.net/npm/@simonwep/pickr/dist/themes/classic.min.css"
    />
    <!-- 'classic' theme -->
    <link
      rel="stylesheet"
      href="https://cdn.jsdelivr.net/npm/@simonwep/pickr/dist/themes/monolith.min.css"
    />
    <!-- 'monolith' theme -->
    <link
      rel="stylesheet"
      href="https://cdn.jsdelivr.net/npm/@simonwep/pickr/dist/themes/nano.min.css"
    />
    <!-- 'nano' theme -->
    <script src="https://cdn.jsdelivr.net/npm/@simonwep/[email protected]/dist/pickr.min.js"></script>
    <style type="text/css">
      body {
        width: 80%;
        height: 80%;
        margin: auto;
      }
      #main {
        height: 400px;
      }
      .pickr {
        margin-top: -3%;
        margin-left: 30%;
      }
    </style>
  </head>
  <body>
    <div id="main">
      <h1>Pickr library</h1>
      <p>Select Button</p>
      <div id="color_input"></div>
    </div>
    <script type="text/javascript">
      $(document).ready(function () {
        const pickr = Pickr.create({
          el: "#color_input",
          theme: "monolith",
          components: {
            preview: true,
            opacity: true,
            hue: true,
            // Input / output Options
            interaction: {
              hex: true,
              rgba: true,
              hsla: true,
              hsva: true,
              cmyk: true,
              input: true,
              clear: true,
              save: true
            }
          }
        });
        //change the color of the main div when color changes
        pickr.on("change", function (e) {
          $("#main").css("backgroundColor", e.toRGBA());
        });
      });
    </script>
  </body>
</html>
```

除了这些流行的颜色选择器，开发人员还可以选择:

colorPicker 是一个高度直观、轻量级和兼容的 JavaScript 框架，可用作独立的颜色拾取工具。它具有多种功能，涵盖颜色转换以及色差、图层混合和对比度等计算。colorPicker 工具甚至能够完全支持不良的色彩空间范围。无论您的要求是什么——RGB、HSV、HSL、CMY、CMYK、HEX、XYZ 等等，该工具都能为您提供所需的结果。让我们看一个例子:

```
<!DOCTYPE html>
<html>
<head>
    <title>colorPicker library</title>
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.4.1/jquery.min.js"></script>
    <style type="text/css">
    body {
    width: 80%;
    height:80%;
    margin:auto;
    }
    #main {
    height:400px;
    }
    #color {
      margin-left: 30%;
    }
  </style>
</head>
<body>
    <div id="main">
    <h1>colorPicker library</h1>
    <input  id = "color" type="text">
    </div>

    <script type="text/javascript" src="../colors.js"></script>
    <script type="text/javascript" src="../colorPicker.data.js"></script>
    <script type="text/javascript" src="../colorPicker.js"></script>
    <script type="text/javascript" src="jsColor.js"></script>

    <script  type="text/javascript">
     $(document).ready(function () {

        //find the page loading time
        var loadTime = window.performance.timing.domContentLoadedEventEnd- window.performance.timing.navigationStart
        console.log("Load Time:",loadTime)
        var colors = jsColorPicker('#color', {
            size: 2,
            readOnly: false,
            init: function(elm, colors) {},
            renderCallback: function(colors, mode){ //change the background of the main div when the color is selected
                document.getElementById("main").style.backgroundColor = "#"+colors.HEX
                document.getElementById("color").value = "#"+colors.HEX

            }

        });
    });

</script>
</body>
</html>
```

evol-colorpicker 是一个适应性很强的 JavaScript 库，具有颜色选择器功能，可以内嵌使用。众所周知，evol-colorpicker 在确保透明颜色支持、颜色历史记录跟踪和调色板选择时会按下正确的按钮。

由于给定的颜色选择器是一个完全成熟的 UI(用户界面)小部件，它还可以根据您的喜好轻松调整配置和主题。看看下面的例子:

```
<!DOCTYPE html>
<html>
  <head>
    <title>Evol Color Picker library</title>
    <script
      src="https://ajax.googleapis.com/ajax/libs/jquery/3.5.1/jquery.min.js"
      type="text/javascript"
      charset="utf-8"
    ></script>
    <script
      src="https://ajax.googleapis.com/ajax/libs/jqueryui/1.12.1/jquery-ui.min.js"
      type="text/javascript"
      charset="utf-8"
    ></script>
    <script src="https://cdn.jsdelivr.net/npm/[email protected]/js/evol-colorpicker.js"></script>
    <link
      rel="stylesheet"
      type="text/css"
      href="http://ajax.googleapis.com/ajax/libs/jqueryui/1.12.1/themes/ui-lightness/jquery-ui.css"
    />
    <link
      rel="stylesheet"
      href="https://cdn.jsdelivr.net/npm/[email protected]/css/evol-colorpicker.css"
    />
    <style type="text/css">
      body {
        width: 80%;
        height: 80%;
        margin: auto;
      }
      #main {
        height: 400px;
      }
      .evo-cp-wrap {
        margin-left: 30%;
      }
      div.evo-pointer.evo-colorind {
        border-width: 2px;
      }
    </style>
  </head>
  <body>
    <div id="main">
      <h1>Evol Color Picker library</h1>
      <input id="color" type="text" />
    </div>
    <script type="text/javascript">
      $(document).ready(function () {
        $("#color").colorpicker({
          color: "#ffffff", //initial color
          defaultPalette: "theme", //theme or web
          transparentColor: true //user can select transparent colors
        });
        //change the main background of the div when the color is selected
        $("#color").on("change.color", function (event, color) {
          $("#main").css("background-color", color);
        });
      });
    </script>
  </body>
</html>
```

JSColor 是领先的基于 web 的颜色选择器之一，旨在为设计人员和开发人员在给定组件的安装和最终使用过程中提供最佳体验。总体易用性及其简单性使得给定的颜色选择器在用户中非常受欢迎。众所周知，JSColor 支持所有浏览器，包括 Chrome、Safari、Internet Explorer 7 及以上版本、Mozilla、Opera 等。让我们看一个实际例子:

```
<!DOCTYPE html>
<html>
  <head>
    <title>Jscolor library</title>
    <script
      src="https://ajax.googleapis.com/ajax/libs/jquery/3.5.1/jquery.min.js"
      type="text/javascript"
      charset="utf-8"
    ></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jscolor/2.4.0/jscolor.js"></script>
    <style type="text/css">
      body {
        width: 80%;
        height: 80%;
        margin: auto;
      }
      #main {
        height: 400px;
      }
      p {
        margin-left: 30%;
      }
    </style>
  </head>
  <body>
    <div id="main">
      <h1>Jscolor library</h1>
      <p>
        Color: <input id="color" value="rgba(255,160,0,0.5)" data-jscolor="" />
      </p>
    </div>
    <script type="text/javascript">
      $(document).ready(function () {
        //change the main background of the div when the color is changed
        $("#color").on("change", function (e) {
          color = $(this).val();
          $("#main").css("backgroundColor", color);
        });
      });
    </script>
  </body>
</html>
```

Farbtastic 为您提供了一个专门的颜色拾取插件，用于在 web 项目中添加一个甚至多个颜色拾取器小部件。这是借助 JavaScript 实现的。因为您会将每个小部件链接到文本字段等现有元素，所以元素值会在选择某种颜色后自动更新。让我们来看看它的实际应用:

```
<!DOCTYPE html>
<html>
  <head>
    <title>Farbtastic Color Picker library</title>
    <script
      src="https://ajax.googleapis.com/ajax/libs/jquery/3.5.1/jquery.min.js"
      type="text/javascript"
      charset="utf-8"
    ></script>
    <script src="https://cdn.jsdelivr.net/npm/[email protected]/farbtastic.js"></script>
    <link
      rel="stylesheet"
      href="https://cdn.jsdelivr.net/npm/[email protected]/farbtastic.css"
    />
    <style type="text/css">
      body {
        width: 80%;
        height: 80%;
        margin: auto;
      }
      #color {
        margin-left: 30%;
      }
      #colorpicker {
        margin-left: 30%;
      }
    </style>
  </head>
  <body>
    <div id="main">
      <h1>Farbtastic Color Picker library</h1>
      <input id="color" type="text" />
      <div id="colorpicker"></div>
    </div>
    <script type="text/javascript">
      $(document).ready(function () {
        var obj = $.farbtastic("#colorpicker", "#color"); //placeholder, callback
        obj.linkTo(function (event) {
          //linking a callback function that changes the background color of the main div
          $("#main").css("backgroundColor", event);
          $("#color").val(event);
        });
      });
    </script>
  </body>
</html>
```

colorjoe 是一个高度可扩展的颜色选择器。它为您提供了即时颜色选择的整体便利性。使用此工具，您可以在选择并单击可用的颜色选择区域时获得 RGB 以及其他颜色代码或值。colorjoe 提供的可伸缩性非常实用。这是因为它不依赖于永恒的图像，而且众所周知它是基于 CSS 的。因此，您可以通过利用 CSS 来修改 colorjoe 的大小，以满足个人需求:

```
<!DOCTYPE html>
<html>
  <head>
    <title>ColorJoe library</title>
    <script
      src="https://ajax.googleapis.com/ajax/libs/jquery/3.5.1/jquery.min.js"
      type="text/javascript"
      charset="utf-8"
    ></script>
    <link
      rel="stylesheet"
      href="https://cdn.jsdelivr.net/npm/[email protected]/css/colorjoe.css"
    />
    <script src="https://cdn.jsdelivr.net/npm/[email protected]/dist/colorjoe.js"></script>
    <style type="text/css">
      body {
        width: 80%;
        height: 80%;
        margin: auto;
      }
      #main {
        height: 600px;
      }
      #color {
        margin-left: 30%;
      }
    </style>
  </head>
  <body>
    <div id="main">
      <h1>ColorJoe library</h1>
      <div id="colorpicker"></div>
    </div>
    <script type="text/javascript">
      $(document).ready(function () {
        const joe = colorjoe.rgb("colorpicker", "red", [
          "hex",
          ["fields", { space: "RGB", limit: 255, fix: 0 }],
          "currentColor"
        ]);
        //change the background color of the main div when color changes
        joe
          .on("change", function (c) {
            $("#main").css("backgroundColor", c.css());
          })
          .update();
      });
    </script>
  </body>
</html>
```

## 特性试验

下表给出了上述颜色选择器库的页面加载时间。它显示平均 10 次测试。

| **图书馆** | **以毫秒为单位的页面加载时间** | **下载大小** |
| --- | --- | --- |
| **引导选色器** | 170.4 | 1.92 兆字节 |
| **反应颜色** | 444.1 | 1.35 兆字节 |
| **Pickr** | 120.3 | 367.97 KB |
| **颜色选择器** | 189.5 | – |
| **evol-colorpicker** | 181.2 | 4.33 兆字节 |
| **JSColor** | 131.9 | 1.16KB |
| **色彩修饰** | 129.1 | 32.60 KB |
| **colorjoe** | 145.9 岁 | 238.12 KB |

## 来源

[https://github.com/casesandberg/react-color](https://github.com/casesandberg/react-color)

[https://github.com/Simonwep/pickr](https://github.com/Simonwep/pickr)

[https://github.com/itsjavi/bootstrap-colorpicker](https://github.com/itsjavi/bootstrap-colorpicker)

[https://github.com/PitPik/colorPicker](https://github.com/PitPik/colorPicker)

[https://github . com/evolved/color picker](https://github.com/evoluteur/colorpicker)

[https://github.com/mattfarina/farbtastic](https://github.com/mattfarina/farbtastic)

[https://github.com/bebraw/colorjoe](https://github.com/bebraw/colorjoe)

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)