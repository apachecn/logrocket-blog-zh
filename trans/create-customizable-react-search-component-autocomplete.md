# 用自动完成创建一个可定制的 React 搜索组件

> 原文：<https://blog.logrocket.com/create-customizable-react-search-component-autocomplete/>

如今，大多数前端应用程序都需要某种搜索框，有时这是用户在页面上交互的第一个组件——比如 Airbnb、优步或谷歌地图。创建一个搜索组件，它不仅可以工作，而且功能足够强大，可以指导用户完成他们想要的任务，这对应用程序的用户体验至关重要。

Turnstone 是一个新的库，让 React 开发人员能够做到这一点。这个轻量级库(12.2kB Gzip)附带了自动完成、自动缓存、WAI-ARIA 可访问性和其他特性，这些特性允许您构建一个功能性的、可访问的搜索组件。

使用 CSS 模块或 [Tailwind CSS](https://blog.logrocket.com/theming-react-components-tailwind-css/) 等各种 CSS 方法可以轻松定制 Turnstone 元素。在本文中，我们将使用 Turnstone 和 Tailwind 创建一个应用程序，该应用程序查询[漫威漫画 API](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=&cad=rja&uact=8&ved=2ahUKEwjL2I669NX3AhVJ8rsIHV1kCaEQFnoECAcQAQ&url=https%3A%2F%2Fdeveloper.marvel.com%2F&usg=AOvVaw0w2s1zbJuHkEOopq1GGaGy) 来搜索属于漫威电影宇宙的角色。

![Working Search Bar](img/3d4821b747fe1747c6d1f83d226adf13.png)

在 GitHub 上查看 [live 项目](https://mcu-character-search.netlify.app)和[源代码](https://github.com/Chinwike1/turnstone-autocomplete)。

## 先决条件

要跟随本教程，您需要:

*   对 React 框架的基本理解
*   来自漫威漫画 API 的 API 密钥

顺风 CSS 知识是赞赏，但不是必需的。

## 获取您的 API 密钥并添加推荐站点

前往[漫威开发者](https://developer.marvel.com/)网站注册一个新账户。然后导航到**我的开发者账户**，在那里你会找到你的公共和私有 API 密匙。复制公钥以备后用。

在您的应用程序可以向此 API 发出请求之前，其域名必须包含在您的推荐站点列表中。在同一页面上，向下滚动到推荐站点部分，并添加您的应用程序的域名，即`localhost`。您还可以使用星号`*`来接受来自所有域的请求，尽管这并不完全安全。

现在您已经准备好开始开发了！

## Turnstone 特征

`Turnstone`组件接受各种用于控制搜索框不同部分的属性。组件的样式、搜索框查询的数据源、错误消息等等都可以用适当的属性进行配置。

让我们回顾一下我们将在构建这个应用程序中使用的一些重要的方法。

`typeahead`
类型:`boolean`
`typeahead`——又名自动完成——是一种应用程序预测用户正在键入的单词的剩余部分的功能。默认设置为`true`。

`maxItems`
类型:`number`
该属性控制`listbox`中显示的搜索结果的最大数量。

`listbox`
类型:`object`、`array`或`function`
`listbox`指定如何呈现结果以响应用户的查询。该属性控制数据源和搜索类型——可以是`startsWith`或`contains`。

作为一个对象，`listbox`像这样查询单个数据源:

```
const listbox = {
    displayField: 'characters',
    data: (query) =>
      fetch(`/api/characters?q=${query}`)
        .then(response => response.json()),
    searchType: 'startsWith',
}

return (
  <Turnstone listbox={listbox} />
)

```

上面是一个函数，它的返回值应该是一个解析为一个项目数组的承诺。这个函数将当前的`query`字符串作为参数，并在每次字符串改变时重新运行——这就是为什么我们需要另一个名为`debounceWait`的道具，稍后会详细介绍。

如果用作数组，`listbox`可以从多个来源收集数据:

```
const listbox = [
  {
    id: 'cities',
    name: 'Cities',
    ratio: 8,
    displayField: 'name',
    data: (query) =>
      fetch(`/api/cities?q=${encodeURIComponent(query)}`)
        .then(response => response.json()),
    searchType: 'startswith'
  },
  {
    id: 'airports',
    name: 'Airports',
    ratio: 2,
    displayField: 'name',
    data: (query) =>
      fetch(`/api/airports?q=${encodeURIComponent(query)}`)
        .then(response => response.json()),
    searchType: 'contains'
  }
]

return (
  <Turnstone listbox={listbox} />
)

```

在这个场景中，`ratio`属性可以用来指定相对于`maxItems`占据`listbox`的结果的数量。这意味着，例如，如果`maxItems`被设置为 10，那么来自每个数据源的`ratio`数加起来应该是 10。

`styles`
类型:`object`
一个对象，它的键表示由 Turnstone 渲染的元素。每个对应的值都是一个字符串，代表元素的`class`属性

```
const styles = {
input: 'w-full h-12 border border-slate-300 py-2 pl-10',
  listbox: 'w-full bg-white sm:border sm:border-blue-300 sm:rounded text-left sm:mt-2 p-2 sm:drop-shadow-xl',
  groupHeading: 'cursor-default mt-2 mb-0.5 px-1.5 uppercase text-sm text-rose-300',
} 

return (
  <Turnstone styles={styles} />
)

```

我们可以看到 Tailwind 是多么容易适应，使造型过程更容易。查看文档中可用 Turnstone 元素的[列表。](https://github.com/tomsouthall/turnstone#styles)

`debounceWait`
类型:`number`
该属性指定用户完成输入后，在他们的查询被发送到`fetch`函数之前的等待时间，以毫秒为单位。

`defaultListbox`
该属性与`listbox`相同，但是当搜索框处于焦点时显示，没有查询字符串。它通常用于为最近的搜索创建一个`listbox`:

```
const defaultListBox = {
  displayField: 'Recent Searches',
  data: () => Promise.resolve(JSON.parse(localStorage.getItem('recentSearches')) || [])
}

return (
  <Turnstone defaultListBox={defaultListBox} />
)

```

## 创建应用程序

打开您的终端，使用以下命令创建一个新的 React 应用程序:

```
npx create-react-app turnstone-demo

```

安装完成后，导航到项目目录:

```
cd turnstone-demo

```

并安装 Turnstone 和 Tailwind CSS——以及它的对等依赖项， [PostCSS](https://www.npmjs.com/package/postcss) 和 [Autoprefixer](https://www.npmjs.com/package/autoprefixer) :

```
npm install -D turnstone tailwindcss postcss autoprefixer

```

让我们首先为 API 键创建一个环境变量。在项目的根目录下，创建一个`.env`文件并存储 API 密钥

```
// .env
REACT_APP_MARVEL_APIKEY = 'your_apikey_here'

```

Create React App 提供了对环境变量的支持，这些变量是用必需的`REACT_APP_`前缀创建的。然后可以在应用程序中以`process.env.REACT_APP_MARVEL_APIKEY`的形式访问这个变量。

***注意:**，记得把* `.env` *添加到你的* `.gitignore` *文件中，这样你就不会在公共存储库中暴露你的密钥了。*

## 图像背景

项目演示中看到的底层图像背景是用下面的 CSS 类创建的:

```
// App.css
.image-backdrop {
  background-image: linear-gradient(rgba(0, 0, 0, 0.7), rgba(0, 0, 0, 0.7)), url('../public/comic-backdrop.jpg');
  height: 100vh;
  width: 100%;
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
}

```

将这个类附加到`public/index.html`中的`body`标签上，你应该有一个搜索框的图像背景放置在:

```
// public/index.html
<!DOCTYPE html>
<html lang="en">
  <head> 
    <!-- markup -->
  </head>
  <body class="image-backdrop"> 
    <!-- more markup -->
    <div id="root"></div>
  </body>
</html>

```

## 初始化顺风

要初始化 Tailwind CSS，请运行以下命令:

```
npx tailwindcss init -p

```

这会生成`tailwind.config.js`和`postcss.config.js`文件，用于定制和扩展 Tailwind 的特性。

我们现在只需要配置模板路径。用下面的代码更新`tailwind.config.js`:

```
// tailwind.config.js
module.exports = {
  content: ['./src/**/*.{js,jsx,ts,tsx}'],
  theme: {
    extend: {},
  },
  plugins: [],
}

```

接下来，使用`@tailwind`指令将顺风层添加到`index.css`:

```
// index.css
@tailwind base;
@tailwind components;
@tailwind utilities;

```

现在，您可以开始使用 Tailwind 的实用程序类来设计 React 应用程序了。让我们从将`SearchBox`组件放置在屏幕顶部中心开始。

在`src`中，创建一个`components`文件夹，存储`SearchBox.js`文件。接下来，将这个组件导入到`App.js`中，并将下面的顺风类应用到父容器中:

```
// App.js
import SearchBox from './components/SearchBox'
import './App.css'

function App() {
  return (
    <div className='m-auto relative top-28 w-11/12 sm:w-6/12'>
      <SearchBox />
    </div>
  )
}
export default App

```

这会将搜索框定位在页面的顶部中央。

## 使用`Turnstone`组件

在我们开始配置搜索框的动态部分之前，向`Turnstone`组件添加以下属性:

```
// SearchBox.js
import Turnstone from 'turnstone'

const SearchBox = () => {
  return (
    <Turnstone
      id='search'
      name='search'
      autoFocus={true}
      typeahead={true}
      clearButton={true}
      debounceWait={250}
      listboxIsImmutable={true}
      maxItems={6}
      noItemsMessage="We couldn't find any character that matches your search"
      placeholder='Search for any character in the MCU'
    />
  )
}
export default SearchBox

```

每当用户在搜索框中输入一个字符时，呈现一个清除按钮。

`autoFocus`设置为`true`使搜索框在页面加载时自动接收焦点。

`maxItems`将`listbox`中显示的搜索结果的最大数量设置为 6。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

`listboxIsImmutable`设置为`true`确保`listbox`的内容不会在查询之间改变；即，相同的查询不能返回不同的结果。

现在让我们继续讨论`listbox`属性。

## `listbox`

在 listbox 的`data`属性中，我们向 Comics API 发出一个请求，在这个过程中附加当前的查询字符串和您的 API 键:

```
// SearchBox.js
import Turnstone from 'turnstone'

const SearchBox = () => {
  const listbox = {
    displayField: 'characters',
    data: async (query) => {
      const res = await fetch(
        `https://gateway.marvel.com:443/v1/public/characters?nameStartsWith=${query}&apikey=${process.env.REACT_APP_MARVEL_APIKEY}`
      )
      const data = await res.json()
      return data.data.results
    },
    searchType: 'startsWith',
  }

  return (
    <Turnstone
      id='search'
      name='search'
      autoFocus={true}
      typeahead={true}
      clearButton={true}
      debounceWait={250}
      listboxIsImmutable={true}
      maxItems={6}
      noItemsMessage="We couldn't find any character that matches your search"
      placeholder='Search for any character in the MCU'
      listbox={listbox}
    />
  )
}
export default SearchBox

```

漫威 API 有一个[交互式文档](https://developer.marvel.com/docs#!/public/getCreatorCollection_get_0)页面，列出了所有可用的终点。在我们的例子中，我们已经向字符端点提出了一个请求:`/v1/public/characters`。

可以添加额外的参数，如`stories`、`events`或`nameStartsWith`，以获得不同的结果。我们还使用了`nameStartsWith`参数，将其值设置为`query`字符串。

这个函数的结果应该是一个包含所有漫威字符的`results`数组的对象，这些字符的名称以`query`字符串开始:

```
// JSON result from API call. query="Doctor Strange"
{
  "code": 200,
  "status": "Ok",
  "copyright": "© 2022 MARVEL",
  "attributionText": "Data provided by Marvel. © 2022 MARVEL",
  "attributionHTML": "<a href=\"http://marvel.com\">Data provided by Marvel. © 2022 MARVEL</a>",
  "etag": "07a3a76164eec745484f34562db7ca7166c196cc",
  "data": {
    "offset": 0,
    "limit": 20,
    "total": 2,
    "count": 2,
    "results": [
      {
        "id": 1009282,
        "name": "Doctor Strange",
        "description": "",
        // ...

```

相关数据位于`data.results`中，是函数的返回值。

此时，应用程序运行正常。现在我们可以用 Tailwind 和`styles`属性来设计 Turnstone 的元素了。

## 带顺风的造型元素

如前所述，`styles`对象中的键代表搜索组件的某个元素。我们可以设计元素的样式，比如`listbox`、`listbox`中突出显示的项目，甚至是自动完成文本的颜色，来创建一个更好看的搜索框:

```
// SearchBox.js
import Turnstone from 'turnstone'
import recentSearchesPlugin from 'turnstone-recent-searches'

const listbox = {
  // ...
}

const styles = {
  input: 'w-full border py-2 px-4 text-lg outline-none rounded-md',
  listbox: 'bg-neutral-900 w-full text-slate-50 rounded-md',
  highlightedItem: 'bg-neutral-800',
  query: 'text-oldsilver-800 placeholder:text-slate-600',
  typeahead: 'text-slate-500',
  clearButton:
    'absolute inset-y-0 text-lg right-0 w-10 inline-flex items-center justify-center bg-netural-700 hover:text-red-500',
  noItems: 'cursor-default text-center my-20',
  match: 'font-semibold',
  groupHeading: 'px-5 py-3 text-pink-500',
}

const SearchBox = () => {
  return (
    <Turnstone
      id='search'
      name='search'
      autoFocus={true}
      typeahead={true}
      clearButton={true}
      debounceWait={250}
      listboxIsImmutable={true}
      maxItems={6}
      noItemsMessage="We couldn't find any character that matches your search"
      placeholder='Search for any character in the MCU'
      listbox={listbox}
      styles={styles}
    />
  )
}

export default SearchBox

```

![Black Panther in Search](img/ebb3c616fa7409555b30601751ce3bd3.png)

## `Item`组件道具

虽然我们可以通过引用`styles`中的`item`属性来设置`listbox`中的项目的样式，但是 Turnstone 提供了组件属性，本质上允许对 Turnstone 元素进行额外的定制和格式化。

下面是我们如何使用它在搜索结果中的角色名字旁边包含一个头像:

```
// SearchBox.js
import Turnstone from 'turnstone'

const listbox = {
    // ...
}

const styles = {
    // ...
}

const Item = ({ item }) => {
  /* thubmnails from the API are stored as partials therefore 
    we have to concatenate the image path with its extension
  */
  const avatar = `${item.thumbnail.path}.${item.thumbnail.extension}`
  return (
    <div className='flex items-center cursor-pointer px-5 py-4'>
      <img
        width={35}
        height={35}
        src={avatar}
        alt={item.name}
        className='rounded-full object-cover mr-3'
      />
      <p>{item.name}</p>
    </div>
  )
}

const SearchBox = () => {
  return (
    <Turnstone
      id='search'
      name='search'
      autoFocus={true}
      typeahead={true}
      clearButton={true}
      debounceWait={250}
      listboxIsImmutable={true}
      maxItems={6}
      noItemsMessage="We couldn't find any character that matches your search"
      placeholder='Search for any character in the MCU'
      listbox={listbox}
      styles={styles}
      Item={Item}
    />
  )
}

export default SearchBox

```

![Thanos in Search](img/f8fa18d861139b8fb4547b6af05f4871.png)

## 最近搜索插件

额外支付(1.7kB gzip)，可以在 Turnstone 的插件 prop 中添加另一个名为`turnstone-recent-searches`的包，自动记录用户最近的搜索。

使用以下命令安装此软件包:

```
npm install turnstone-recent-searches

```

并将其包含在`plugins`道具中:

```
// SearchBox.js
import Turnstone from 'turnstone'
import recentSearchesPlugin from 'turnstone-recent-searches'

const styles = {
  input: 'w-full border py-2 px-4 text-lg outline-none rounded-md',
  listbox: 'bg-neutral-900 w-full text-slate-50 rounded-md',
  highlightedItem: 'bg-neutral-800',
  query: 'text-oldsilver-800 placeholder:text-slate-600',
  typeahead: 'text-slate-500',
  clearButton:
    'absolute inset-y-0 text-lg right-0 w-10 inline-flex items-center justify-center bg-netural-700 hover:text-red-500',
  noItems: 'cursor-default text-center my-20',
  match: 'font-semibold',
  groupHeading: 'px-5 py-3 text-pink-500',
}

const listbox = {
  displayField: 'characters',
  data: async (query) => {
    const res = await fetch(
      `https://gateway.marvel.com:443/v1/public/characters?nameStartsWith=${query}&apikey=${process.env.REACT_APP_MARVEL_APIKEY}`
    )
    const data = await res.json()
    return data.data.results
  },
  searchType: 'startsWith',
}

const Item = ({ item }) => {
  const avatar = `${item.thumbnail.path}.${item.thumbnail.extension}`
  return (
    <div className='flex items-center cursor-pointer px-5 py-4'>
      <img
        width={35}
        height={35}
        src={avatar}
        alt={item.name}
        className='rounded-full object-cover mr-3'
      />
      <p>{item.name}</p>
    </div>
  )
}

const SearchBox = () => {
  return (
    <Turnstone
      id='search'
      name='search'
      autoFocus={true}
      typeahead={true}
      clearButton={true}
      debounceWait={250}
      listboxIsImmutable={true}
      maxItems={6}
      noItemsMessage="We couldn't find any character that matches your search"
      placeholder='Search for any character in the MCU'
      listbox={listbox}
      styles={styles}
      Item={Item}
      plugins={[recentSearchesPlugin]}
    />
  )
}

export default SearchBox

```

这个特性同样重要，因为它为您的用户创造了更好的体验。

![Recent Searches](img/07939c2a294ba7170c4f8b63b7fe3650.png)

## 结论

自动完成搜索框在现代 UI 设计中很普遍，拥有一个帮助我们轻松实现它们的 React 库非常棒。

Turnstone 的文档在解释其 API 设计方面做得很好，给了它一个渐进的学习曲线——当我尝试其他 React 自动完成库时，情况并非如此。要查看更多 Turnstone 的例子，请查看 Turnstone 网站上的例子。

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