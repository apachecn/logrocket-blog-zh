# Next.js 和 Firebase DB 全栈 app 教程| LogRocket 博客

> 原文：<https://blog.logrocket.com/nextjs-cloud-firestore-full-stack-app-tutorial/>

***编者按:**本教程于 2022 年 3 月 8 日更新，包含了 Firebase 的云 Firestore 的最新信息。*

建立数据库从未如此简单。过去需要几个小时的事情现在可以在几分钟内完成。特别是，有了 Firebase 最新的 NoSQL 数据库，开发人员只需点击几下鼠标就可以建立并运行一个数据库。像 Firestore 的实时数据库一样，Cloud Firestore 是免费的，允许开发人员在全球范围内为移动和网络应用程序存储、编辑和同步数据。结合 web 或移动客户端，开发人员可以快速轻松地创建完整的 web 解决方案。

在本教程中，我们将演示如何使用 Next.js 和 Cloud Firestore 创建全栈应用。我们将回顾如何建立一个 Firestore 数据库。然后，我们将构建一个 Next.js 应用程序，我们将创建页面和 API 路由来创建、编辑、获取和删除新数据库中的条目。

## 在 Firebase 的实时数据库和云 Firestore 之间进行选择

Firebase 现在提供两种数据库解决方案:云 Firestore 和实时数据库。理论上，他们看起来很相似。这两个都是 NoSQL 数据库，带有客户端 SDK、实时更新和免费层。你可能想知道这两者之间是否有很大的区别。

选择哪一个在很大程度上取决于你的需求。如果你需要一个轻量级的数据库，有简单的查询，并且知道你的数据会经常被访问，那么就用实时数据库。

另一方面，如果您需要更高级的查询功能以及更多的数据结构，那么 Cloud Firestore 可能是更好的选择。

## 设置云风暴

我们将从建立 Firebase 项目开始。前往 [Firebase 站点](https://console.firebase.google.com/)点击**创建项目**。Firebase 提供了各种各样的服务，但是对于本文，我们将重点关注云 Firestore。

在侧边栏的构建选项卡下，点击 **Firestore 数据库**，然后点击**创建数据库**。出现提示时，在生产模式选项中选择**开始**，并选择离您最近的位置。这对于减少数据延迟非常重要。

现在，数据库已经建立。下一步是创建凭证，我们将用它在 Next.js 应用程序中初始化和访问数据库。

点按边栏顶部“项目概述”旁边的齿轮图标，然后导航到“项目设置”。接下来，转到 Service accounts 选项卡并单击 **Generate new private key** 以生成 JSON 文件，我们将在同一页面上显示的示例代码中使用该文件。

## 设置 Next.js 应用程序

现在，我们将创建 Next.js 应用程序并配置数据库连接。开始使用 Next.js 最简单的方法是使用以下命令在指定的文件夹中创建一个 Next.js 项目:

```
npx create-next-app demo-app

```

转到控制台上的`demo-app`目录，运行`npm run dev`来启动开发服务器。当你在浏览器中访问`localhost:3000`时，你应该会看到一个欢迎页面。

## 连接到数据库

接下来，让我们创建一个与数据库通信的实用函数。首先，我们将安装`firebase-admin`作为一个依赖项。我们还将使用`axios`和`dashify`库，所以让我们也安装它们。

```
npm i firebase-admin axios dashify

```

现在，创建相对于项目根目录的`utils/db/index.js`。

接下来，我们将把在上一步中下载的 JSON 文件复制并粘贴到`utils/db`文件夹中，并将其重命名为`serviceAccountKey.json`。

*提示:*确保也将这个文件路径添加到您的`.gitignore`中。您的`serviceAccountKey.json`文件包含不应该意外提交给 Github 的密钥。

将以下代码块复制并粘贴到`utils/db/index.js`:

```
import admin from 'firebase-admin';
import serviceAccount from './serviceAccountKey.json';

if (!admin.apps.length) {
  try {
    admin.initializeApp({
      credential: admin.credential.cert(serviceAccount)
    });
  } catch (error) {
    console.log('Firebase admin initialization error', error.stack);
  }
}
export default admin.firestore();

```

## 创建端点

Next.js 有一个非常方便的 API routes 解决方案，它有助于在`/pages/api`文件夹下创建 API 端点。让我们创建`/pages/api/entry/[id].js`和`/pages/api/entry/index.js`。第一个端点捕获请求中发送的`id`,并使其在函数中可用。我们稍后会谈到这一点。我们将有四种不同的请求方法:`POST`、`GET`、`PUT`和`DELETE`。

*   `POST`用于添加新的数据库条目
*   `PUT`用于更新现有的数据库条目
*   `GET`用于获取现有的数据库条目
*   `DELETE`用于删除现有的数据库条目

让我们从`POST`方法开始，我们将向`entries`集合添加一个新条目。以发送到该端点的以下请求为例:

```
axios.post('/api/entry', { title: Foo Bar, slug: foo-bar, body: lorem ipsum });

```

这是`/pages/api/entry/index.js`的内容:

```
import db from '../../../utils/db';

export default async (req, res) => {
  try {
    const { slug } = req.body;
    const entries = await db.collection('entries').get();
    const entriesData = entries.docs.map(entry => entry.data());

    if (entriesData.some(entry => entry.slug === slug)) {
      res.status(400).end();
    } else {
      const { id } = await db.collection('entries').add({
        ...req.body,
        created: new Date().toISOString(),
      });
      res.status(200).json({ id });
    }
  } catch (e) {
    res.status(400).end();
  }
}

```

在这段代码中，我们从`/utils/db`导入了`db`对象，以便使用它来建立与数据库的通信。Firestore 有集合，每个集合有多个文档。

文档再次包含字段或集合，就像 JSON 结构一样。在上面的函数中，我们将从`/api/entry/index.js`导出，我们将使用`slug`键的值来检查是否已经有另一个条目具有相同的`slug`值。如果是这样，我们将用一个`400`状态码结束请求。您可以指定另一个状态代码，或者传递一个消息显示在 UI 上。为了简单起见，我们只结束请求，不发送任何数据。

接下来，我们将指定集合的名称和要添加的数据。如果该集合不存在，它将自动以指定的名称创建，并以随机的`id`添加一个文档。这个文档包含我们传递的数据。

在验证了`slug`是惟一的之后，我们将向`db`发出另一个请求来添加请求体，这是一个包含`title`、`slug`和`body`的对象:

```
// Example data
{
  title: 'Foo bar',
  slug: 'foo-bar', // This is auto generated on the client side
  body: 'Lorem ipsum',
}

```

我们将把这个对象扩展到另一个对象中，并添加带有当前时间戳的键作为值。现在是时候创建`PUT`、`GET`和`DELETE`端点了。这些将在`/api/entry/[id].js`处理。

以下是这些请求的示例:

```
await axios.get(`/api/entry/${id}`);
await axios.delete(`/api/entry/${id}`);
await axios.put(`/api/entry/${id}`, {
  slug: 'foo-bar',
  title: 'Foo Bar',
  body: 'Lorem ipsum'
});

```

您会注意到`id`变量是如何在端点路径中使用的。因为我们在文件名`id`中使用了方括号，所以它在`req.query`中可用。

我们将检查`req`对象的`method`键来确定请求类型。如果是一个`PUT`方法，我们将通过`id`在`entries`集合中找到文档，然后用一个对象更新它。

我们还将添加另一个名为`updated`的带有当前时间戳的键。这样，我们可以显示条目何时更新。当然，这只是一个可选特性的演示。如果是一个`GET`方法，我们将通过`id`找到文档，然后返回`doc.data()`作为响应。如果是`DELETE`方法，我们会通过`id`找到文件，然后删除文件。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

这是`/pages/api/entry/[id].js`的内容:

```
import db from '../../../utils/db';

export default async (req, res) => {
  const { id } = req.query;

  try {
    if (req.method === 'PUT') {
      await db.collection('entries').doc(id).update({
        ...req.body,
        updated: new Date().toISOString(),
      });
    } else if (req.method === 'GET') {
      const doc = await db.collection('entries').doc(id).get();
      if (!doc.exists) {
        res.status(404).end();
      } else {
        res.status(200).json(doc.data());
      }
    } else if (req.method === 'DELETE') {
      await db.collection('entries').doc(id).delete();
    }
    res.status(200).end();
  } catch (e) {
    res.status(400).end();
  }
}

```

到目前为止，所有这些端点都用于控制单个条目。然而，我们还需要一个端点来获取条目和列出条目。为此，我们将再创建一个端点:`/api/entries.js`。

首先，我们将获取`entries`集合中的所有文档，同时用`created`键对它们进行排序，这是我们在为`POST`方法实现端点时添加的。然后，我们将对返回值进行映射，并返回一个新的对象数组，我们将在该数组中分布条目数据，并添加文档`id`。

我们将需要这个`id`，以便与`GET`、`DELETE`和`PUT`方法一起使用。这是`/pages/api/entries.js`的内容:

```
import db from '../../utils/db';

export default async (req, res) => {
  try {
    const entries = await db.collection('entries').orderBy('created').get();
    const entriesData = entries.docs.map(entry => ({
      id: entry.id,
      ...entry.data()
    }));
    res.status(200).json({ entriesData });
  } catch (e) {
    res.status(400).end();
  }
}

```

我们将按如下方式使用该端点:

```
await axios.get('/api/entries');

```

既然我们已经完成了端点的创建，我们将继续实现页面。

## 在 Next.js 中创建路径

在最简单的形式中，我们需要一条`/admin`和一条`/posts`路线。当然，这些都是随意起的名字。在`/admin`路线中，我们需要能够发布一个条目并列出所有条目，以便我们可以选择和编辑一个条目。在`/posts`路线中，我们需要能够列出所有条目，还可以导航到特定条目。

下面是`/pages`文件夹的最终结构:

```
|-- api
|-- admin
    |-- post.js
    |-- edit
        |-- index.js
        |-- [id].js
|-- posts
    |-- index.js
    |-- [slug].js

```

同样，我们将在`/admin/edit`和`/posts`路线下分别用方括号将`id`和`slug`括起来。这些动态路由让我们可以使用相同的页面模板来创建不同内容的不同路由。

### `/admin`路线

先说`/admin`路线。注意，为了安全起见，我们没有将`/admin`路由放在认证之后。在实际应用中，这显然是必需的。

#### `/admin/post.js`

这里，我们有一个状态变量，它是一个保存了`title`和`body`属性的对象。在设置了`title`和`body`对象后，我们可以将这些值作为一个条目发送。

`onSubmit`函数将对`/api/entry`进行`POST`调用，同时调用一个包含`title`、`body`和`slug`的对象。`slug`由`title`变量通过`dashify`包生成。

```
import { useState } from 'react';
import dashify from 'dashify';
import axios from 'axios';

const Post = () => {
  const [content, setContent] = useState({
    title: undefined,
    body: undefined,
  })
  const onChange = (e) => {
    const { value, name } = e.target;
    setContent(prevState => ({ ...prevState, [name]: value }));
  }
  const onSubmit = async () => {
    const { title, body } = content;
    await axios.post('/api/entry', { title, slug: dashify(title), body });
  }
  return (
    <div>
      <label htmlFor="title">Title</label>
      <input
        type="text"
        name="title"
        value={content.title}
        onChange={onChange}
      />
      <label htmlFor="body">Body</label>
      <textarea
        name="body"
        value={content.body}
        onChange={onChange}
      />
      <button onClick={onSubmit}>POST</button>
    </div>
  );
};

export default Post;

```

#### `/admin/edit/index.js`

在编辑页面上，我们将通过从`/api/entries`端点获取条目来列出所有条目。
然后，我们将使用`id`键链接到该特定条目，该条目将由`admin/edit/[id].js`进行匹配。

```
import { useEffect, useState } from 'react';
import Link from 'next/link'
import axios from 'axios';

const List = () => {
  const [entries, setEntries] = useState([]);
  useEffect(async () => {
    const res = await axios.get('/api/entries');
    setEntries(res.data.entriesData);
  }, []);

  return (
    <div>
      <h1>Entries</h1>
      {entries.map(entry => (
        <div key={entry.id}>
          <Link href={`/admin/edit/${entry.id}`}>
            <a>{entry.title}</a>
          </Link>
          <br/>
        </div>
      ))}
    </div>
  );
};

export default List;

```

#### `/admin/edit/[id].js`

这里，我们将使用带有`GET`方法的`/api/entry/[id].js`端点，以便通过`id`获取特定条目的数据。

然后，我们将使用从数据库获取的数据填充页面的`title`和`body`字段。
我们将使用`onSubmit`和`onDelete`方法向`/api/entry/[id].js`发送请求，并分别使用`PUT`和`DELETE`方法更新和删除条目。

```
import { useEffect, useState } from 'react';
import { useRouter } from 'next/router'
import dashify from 'dashify';
import axios from 'axios';

const EditEntry = () => {
  const router = useRouter()
  const [content, setContent] = useState({
    title: undefined,
    body: undefined,
  })

  useEffect(async () => {
    const { id } = router.query;
    if (id) {
      const res = await axios.get(`/api/entry/${id}`);
      const { title, body } = res.data;
      setContent({
        title,
        body
      })
    }
  }, [router])

  const onChange = (e) => {
    const { value, name } = e.target;
    setContent(prevState => ({ ...prevState, [name]: value }));
  }

  const onSubmit = async (e) => {
    const { id } = router.query
    const { title, body } = content;
    console.log(id, title, body);
    await axios.put(`/api/entry/${id}`, {
      slug: dashify(title),
      title,
      body,
    });
  }

  const onDelete = async () => {
    const { id } = router.query;
    await axios.delete(`/api/entry/${id}`);
    router.back();
  }

  return (
    <div>
      <label htmlFor="title">Title</label>
      <input
        type="text"
        name="title"
        value={content.title}
        onChange={onChange}
      />
      <label htmlFor="body">Body</label>
      <textarea
        name="body"
        value={content.body}
        onChange={onChange}
      />
      <button
        type="button"
        onClick={onSubmit}
      >
        Submit
      </button>
      <button
        type="button"
        onClick={onDelete}
      >
        Delete
      </button>
    </div>
  );
};

export default EditEntry;

```

这些`/admin`路由都不是服务器端呈现的，因为它们不应该是公共的，因此可以在客户端呈现。

### `/posts`路线

`/posts`路线是公共的，因此它们可以在服务器端呈现以提高性能和获得更好的 SEO 结果。

#### `/posts/index.js`

这一页是我们列出所有条目的地方。我们将导出`getStaticProps`，这是一个 Next.js 特定的函数。通过导出它，我们告诉 Next.js 在构建时在服务器端呈现该页面。`getStaticProps`函数中的一切都是服务器端呈现的，不会向客户端公开。同样，如果在`getStaticProps`中使用，那么发送给客户端的代码中不包含该函数之外的所有导入和定义。

当在服务器上生成这个页面时，我们首先需要获取条目，以便能够列出它们。因此，我们将导入我们的`db`对象，然后向数据库发出请求。

获得条目后，我们需要使它们在组件中可用。从`getStaticProps`返回的对象成为组件中可用的`props`。它应该总是返回一个带有`props`键的对象，我们将在这里传递获取的数据:

```
return {
  props: {
    entriesData
  }
}

```

这种方法适用于真正静态的页面。然而，在我们的例子中，当我们发布一个新条目时，我们需要触发一个构建，以便用来自新条目的数据重新生成页面。或者，我们可以在`request-time`服务器端呈现页面，但这种方法成本高且速度慢。Next.js 对此有解决方案:[增量静态再生](https://nextjs.org/docs/basic-features/data-fetching#incremental-static-regeneration)。

通过添加以秒为单位的超时值的`revalidate`键，我们将告诉 Next.js 在请求时重新生成页面。因此，第一个访问该页面的人将获得现有页面，这是在部署应用程序时构建的。第一个请求触发一个新的构建，因此下一个访问该页面的人将得到新生成的页面。

持续时间决定了服务器应该等待多长时间，以根据请求触发另一次重新生成。例如，如果 duration 值设置为 600，相当于 10 分钟，那么对该页面的第一个请求将触发重新生成。10 分钟内即将到来的请求不会触发另一次重新生成，它们将在第一次请求后得到最后生成的页面。一旦 10 分钟的持续时间到期，第一个请求就会触发另一个重新生成，依此类推。

这里，我们还将使用由 Next.js 提供的用于客户端路由转换的`<Link/>`组件。我们将把`/posts/${entry.slug}`传递给`href`道具，以便`/posts/[slug].js`可以匹配它。

```
import Link from 'next/link'
import db from '../../utils/db';

const Posts = (props) => {
  const { entriesData } = props;

  return (
    <div>
      <h1>Posts</h1>
      {entriesData.map(entry => (
        <div key={entry.id}>
          <Link href={`/posts/${entry.slug}`}>
            <a>{entry.title}</a>
          </Link>
          <br />
        </div>
      ))}
    </div>
  );
};

export const getStaticProps = async () => {
  const entries = await db.collection('entries').orderBy('created', 'desc').get();
  const entriesData = entries.docs.map(entry => ({
    id: entry.id,
    ...entry.data()
  }));
  return {
    props: { entriesData },
    revalidate: 10
  }
}

export default Posts;

```

#### `/posts/[slug].js`

在`getStaticProps`中，我们将从`*context*.params`中捕获`slug`，并使用它来过滤`entries`集合，以找到与特定的`slug`值匹配的文档。然后，我们将返回该条目的数据作为道具。如果输入的 slug 不存在，那么我们将返回一个空的`props`对象，因为无论如何我们都必须返回一个带有`props`键的对象。

这里的新东西是使用了`getStaticPaths`。当一个页面有动态路由和`getStaticProps`时，那么它也需要使用`getStaticPaths`，在这里我们定义了一个将在构建时生成的路径列表。

在`getStaticPaths`中，我们获取所有的条目。通过使用每个条目的`slug`值，我们创建了一个具有以下结构的对象数组:

```
params: {
  slug: ...
}

```

因为我们在带方括号的文件名中使用了`slug`，所以我们也需要在`params`对象中使用`slug`的键名。

我们将生成的数组被传递给由`getStaticPaths`返回的对象的`paths`键。
除了`paths`键，我们还有`fallback:true`键-值对。

必须设置该`fallback`键。如果它被设置为`false`，那么 Next.js 将为所有在构建时没有生成的路径返回一个 404 页面。如果我们添加一个新条目，那么我们必须再次运行构建来生成这些路径。然而，将`fallback`设置为`true`会在后台触发静态生成。

为了检测`fallback`设置并显示相应的加载页面，可以使用路由器对象。`router.isFallback`在生成过程中变成`true`，一旦路径生成，就变成`false`。

下面，看看如何使用`router.isFallback`返回一个`loading…`文本。如果访问的路径与数据库中的`slug`不匹配，那么`getStaticProps`返回一个空的`props`对象，这导致`not found`文本被显示。

```
import { useRouter } from 'next/router'
import db from '../../utils/db';

const Post = (props) => {
  const { entry } = props;
  const router = useRouter()
  if (router.isFallback) {
    return (
      <div>loading</div>
    )
  } else {
    if (entry) {
      return (
        <div>
          <h1>{entry.title}</h1>
          <h4>{entry.created}</h4>
          <p>{entry.body}</p>
        </div>
      );
    } else {
      return (
        <div>not found</div>
      )
    }
  }
};

export const getStaticPaths = async () => {
  const entries = await db.collection("entries").get()
  const paths = entries.docs.map(entry => ({
    params: {
      slug: entry.data().slug
    }
  }));
  return {
    paths,
    fallback: true
  }
}

export const getStaticProps = async (context) => {
  const { slug } = context.params;
  const res = await db.collection("entries").where("slug", "==", slug).get()
  const entry = res.docs.map(entry => entry.data());
  if (entry.length) {
    return {
      props: {
        entry: entry[0]
      }
    }
  } else {
    return {
      props: {}
    }
  }
}

export default Post;

```

## 结论

在本文中，我们展示了如何使用 Next.js 和 Cloud Firestore 构建一个全栈应用。我们介绍了如何设置 Firebase 的云 Firestore，以及如何通过 Next.js 应用程序与这个数据库通信。

我们讨论了如何创建 API 端点并在各种组件中使用这些端点。我们还演示了用于静态页面生成的动态路由和服务器端呈现，并解释了如何动态重新生成现有或不存在的静态页面。

有关 Firebase 的云 Firestore 的更多信息，请参见[官方文档](https://firebase.google.com/docs/firestore)。

## [LogRocket](https://lp.logrocket.com/blg/nextjs-signup) :全面了解生产 Next.js 应用

调试下一个应用程序可能会很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪状态、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/nextjs-signup)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png)](https://lp.logrocket.com/blg/nextjs-signup)[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/nextjs-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你的 Next.js 应用上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用程序的性能，报告客户端 CPU 负载、客户端内存使用等指标。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

让您调试 Next.js 应用的方式现代化— [开始免费监控](https://lp.logrocket.com/blg/nextjs-signup)。