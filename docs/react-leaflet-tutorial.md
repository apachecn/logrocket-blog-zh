# React 传单教程- LogRocket 博客

> 原文：<https://blog.logrocket.com/react-leaflet-tutorial/>

***编者按*** : *本帖最后更新于**2021 年 10 月 22 日* *为了改进代码和更新任何过时的信息。*

大多数开发者都熟悉谷歌地图和 MapBox，但它们都需要账户才能使用，并且可能需要付费订阅某些功能。如果你想要一个开源和免费的替代品呢？这就是传单上垒的地方！

传单是一个轻量级的开源地图库，利用 OpenStreetMap，一个免费的可编辑地理数据库。

在本文中，我们将了解如何使用 React Leaflet 在 React 应用程序中呈现传单地图。我们将显示带有自定义图标的标记，并在单击时在地图上显示弹出窗口。稍后，我们将看到使用 SWR 加载远程和本地数据需要做哪些更改。

[https://www.youtube.com/embed/290VgjkLong](https://www.youtube.com/embed/290VgjkLong)

视频

## 入门指南

让我们从创建一个 React 应用程序开始，然后进入它的目录。之后，使用以下命令安装`react-leaflet`和`leaflet`的稳定版本:

```
npx create-react-app react-leaflet-demo
cd react-leaflet-demo
# install react-leaflet and leaflet
npm install [email protected] [email protected]

```

在渲染应用程序之前，将`package.json`文件中的`"browserslist"`值替换为:

```
"browserslist": [
  ">0.2%",
      "not dead",
      "not op_mini all"
    ]

```

这样做可以防止出现下面的错误:

```
Module parse failed: Unexpected token (10:41)
File was processed with these loaders:
    ./node_modules/babel-loader/lib/index.js
    You may need an additional loader to handle the result of these loaders.
> | useEffect(function updatePathOptions() {
> | if (props.pathOptions !== optionsRef.current) {
>  const options = props.pathOptions ?? {};
> | element.instance.setStyle(options);
> | optionsRef.current = options;

```

运行`npm start`应该会在你的浏览器上显示一个肯定的消息。

## 如何安装 React 传单

将`react-leaflet`添加到我们的`package.json`文件后，我们必须正确显示我们的地图。React 传单[需要一些 CSS 来渲染](https://blog.logrocket.com/styling-in-react-4-ways-style-react-app/)，我们可以在`head`中包含 CSS `link`标签，也可以从下面的文件中直接复制并粘贴 CSS 到项目中:

```
<link
  rel="stylesheet"
  href="https://unpkg.com/[email protected]/dist/leaflet.css"
  integrity="sha512-xwE/Az9zrjBIphAcBb3F6JVqxf46+CDLwfLMHloNu6KEQCAWi6HcDUbeOfBIptF7tcCzusKFjFw2yuvEpDL9wQ=="
  crossorigin=""
/>

```

我们还需要设置地图本身呈现的`.leaflet-container`的宽度和高度，否则它将不可见，因为 div 的高度为`0px`:

```
.leaflet-container {
  width: 100%;
  height: 100vh;
}

```

一旦完成，我们就可以开始了！下面的代码显示了在 React 应用程序中获得传单地图渲染所需的最小量。

我们必须从`react-leaflet`导入`Map`(以及我们稍后将使用的其他一些包)，我们将从我们的`App`组件返回它:

```
import React, {useState} from "react";
import { MapContainer, TileLayer, Marker, Popup } from 'react-leaflet';
import './App.css';
import { Icon } from "leaflet";
import * as parkData from "./data/skateboard-parks.json";

function App() {
  return (
    <MapContainer center={[45.4, -75.7]} zoom={12}scrollWheelZoom={false}>
      <TileLayer
    url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png"
    attribution='&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors'
      />
    </MapContainer>
  );
}

export default App;

```

`MapContainer`组件要求我们设置一个`center`位置，这是一个包含纬度和经度以及地图默认缩放级别的数组。

您还会注意到嵌套在`MapContainer`中的`TileLayer`组件。我们需要向 OpenStreetMap 提供属性，否则，您在屏幕上只会看到一个灰色方块:

## 如何在传单上添加标记？

为了在地图上显示标记，我们需要一些数据。我们的数据来自渥太华市，包含该地区滑板公园的位置。让我们从一个 JSON 文件本地加载这些数据，但是，为了了解它的样子，下面是两个 skateparks 的例子:

```
{
  "features": [
    {
      "type": "Feature",
      "properties": {
        "PARK_ID": 960,
        "NAME": "Bearbrook Skateboard Park",
        "DESCRIPTIO": "Flat asphalt surface, 5 components"
      },
      "geometry": {
        "type": "Point",
        "coordinates": [-75.3372987731628, 45.383321536272049]
      }
    },
    {
      "type": "Feature",
      "properties": {
        "PARK_ID": 1219,
        "NAME": "Bob MacQuarrie Skateboard Park (SK8 Extreme Park)",
        "DESCRIPTIO": "Flat asphalt surface, 10 components, City run learn to skateboard programs, City run skateboard camps in summer"
      },
      "geometry": {
        "type": "Point",
        "coordinates": [-75.546518086577947, 45.467134581917357]
      }
    }
  ]
}

```

有了我们的数据，我们可以在`MapContainer`组件中映射它，为每个公园位置返回一个`Marker`组件。一个`Marker`需要一个`position`道具，告诉它在地图上的哪里渲染。

这是一个`[latitude, longitude]`的数组，很像`MapContainer`的`center`道具。

除此之外，我们必须建立一些国家。在`onClick`属性中，当用户点击标记时，我们设置`activePark`。我们稍后将使用它在地图弹出窗口中向用户显示特定滑板公园的一些信息:

```
 //App.js
export default function App() {
  const [activePark, setActivePark] = useState(null);

  return (
    <MapContainer center={[45.4, -75.7]} zoom={12} scrollWheelZoom={false}>
  {parkData.features.map(park => (
        <Marker
          key={park.properties.PARK_ID}
          position={[
            park.geometry.coordinates[1],
            park.geometry.coordinates[0]
          ]}
          onClick={() => {
            setActivePark(park);
          }}
          icon={icon}
        />
      ))}
    </MapContainer>
  );
}

```

## 用 React 传单显示地图弹出窗口

因为我们正在跟踪用户点击了哪个 skatepark，如果我们的状态中有一个`activePark`，我们可以显示一个弹出窗口。

`Popup`组件显示了一个可以关闭的白色小气泡，很像`Marker`，我们需要给它一个位置，这样它就知道在地图上的哪里渲染了。在`Popup`内部，我们能够传递 HTML。

这也可以使用 CSS 来设置样式，所以可以随意改变外观，让它看起来完全像你想要的。

在`Popup`上有一个`onClose` prop 事件，允许我们跟踪用户何时关闭弹出气泡，以便我们可以相应地更新状态:

```
//App.js
<MapContainer center={[45.4, -75.7]} zoom={12} scrollWheelZoom={false}>
  {activePark && (
    <Popup
      position={[
        activePark.geometry.coordinates[1],
        activePark.geometry.coordinates[0]
      ]}
      onClose={() => {
        setActivePark(null);
      }}
    >
      <div>
        <h2>{activePark.properties.NAME}</h2>
        <p>{activePark.properties.DESCRIPTIO}</p>
      </div>
    </Popup>
  )}
</MapContainer>

```

## 添加 React 传单自定义标记图标

使用从`leaflet`本身导入的`Icon`，很容易在传单中自定义标记图标。这样，我们可以创建一个新的`Icon`实例，设置图像的 URL 位置及其大小:

```
import { Icon } from "leaflet";

const skater = new Icon({
  iconUrl: "/skateboarding.svg",
  iconSize: [25, 25]
});

```

组件`Marker`有一个`icon`属性，可以设置为我们创建的`skater`变量。

## 用 React 传单显示远程数据

使用 [SWR 进行远程数据获取](https://blog.logrocket.com/whats-new-swr-v1/)，我们可以从 API 端点远程加载数据。一旦我们有了数据，我们如何在地图上显示它与显示本地数据没有什么不同。为了增加这个概念，我们将展示一些由英国警方提供的[犯罪数据。](https://data.police.uk/api/crimes-street/all-crime?lat=52.629729&lng=-1.131592&date=2019-10)

我对数据进行了切片，只渲染数组中的前 100 个犯罪，因为渲染超过 1000 个标记会降低地图的速度:

```
// existing imports + new import for SWR
import useSwr from "swr";

const fetcher = (...args) => fetch(...args).then(response => response.json());

function App() {
  const url =
    "https://data.police.uk/api/crimes-street/all-crime?lat=52.629729&lng=-1.131592&date=2019-10";
  const { data, error } = useSwr(url, { fetcher });
  const crimes = data && !error ? data.slice(0, 100) : [];

  return (
    <MapContainer center={[52.6376, -1.135171]} zoom={12} scrollWheelZoom={false}>
      <TileLayer
        url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png"
        attribution='&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors'
      />

      {crimes.map(crime => (
        <Marker
          key={crime.id}
          position={[crime.location.latitude, crime.location.longitude]}
        />
      ))}
    </MapContainer>
  );
}

export default App;

```

如果你确实需要数千个标记，你可能想直接使用传单(看看它是否能给你一些额外的性能)，或者看看谷歌地图或 [MapBox 是否更适合你的需要](https://blog.logrocket.com/how-to-use-mapbox-gl/)。

## 结论

传单和它的反应副本，反应传单，是一个神奇的开源和免费的地图替代谷歌地图和地图框，没有 API 的关键要求！这是一个易于使用的软件包，值得一试。

Leaflet 是一个非常轻量级的库，其容量[略低于 40kb 的 JavaScript](https://bundlephobia.com/result?p=leaflet@1.6.0) ，它被行业巨头使用，如 [GitHub、Pinterest 和 Etsy](https://leafletjs.com/) 。

本文展示的源代码是[，可在此处](https://github.com/ugwutotheeshoes/react-leaflet-app-demo)获得。

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