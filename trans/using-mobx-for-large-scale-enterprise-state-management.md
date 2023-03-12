# 使用 MobX 进行大规模企业状态管理

> 原文：<https://blog.logrocket.com/using-mobx-for-large-scale-enterprise-state-management/>

当涉及到构建更大的应用程序时，应用程序状态结构良好且定义清晰是至关重要的。在本帖中，我们将讨论如何将 [MobX](https://mobx.js.org) 用于大规模应用。我们将重点关注如何构建应用程序状态、定义数据之间的关系、进行网络调用以及在商店中加载数据。

因为本文关注的是状态管理，所以我们不会在创建 UI/样式组件上花太多时间。

## 先决条件

准备好了吗？让我们开始吧。

## 在 React 和 MobX 中设置您的企业应用

首先，用 [create-react-app](https://reactjs.org/docs/create-a-new-react-app.html) 创建一个 TypeScript React app。

```
$ npx create-react-app react-mobx-app --template=typescript

```

现在，转到应用程序文件夹并安装以下内容:

```
$ yarn add react-router-dom mobx mobx-react axios

```

其中一些需要类型，所以让我们也安装它们。

```
$ yarn add -D @types/react-router-dom 

```

让我们删除我们不需要的文件。我们可以删除 *App.tsx* 、 *App.test.tsx* 、 *App.css* 、 *logo.svg* 。我们稍后将再次创建必要的文件。

在我们开始构建应用程序之前，让我们看看我们将要构建的内容和商店结构。我们将构建一个简单的博客应用程序。该应用程序将有三个实体，即用户，帖子和评论。这是它们之间的关系。

*   用户(有许多帖子)
    *   帖子(有很多评论，属于一个用户)
        *   评论(属于一个帖子)

## 创建实体类型

现在我们知道了结构，让我们为它创建类型。我们将把类型放在 src 下一个名为 *types 的文件夹中。*

让我们从在*类型*下创建*用户类型*开始。这是它看起来的样子。

```
export default interface IUser {
  id: number;
  name: string;
  username: string;
  email: string;
}

```

然后，*类型/帖子*:

```
export default interface IPost {
  id: number;
  userId: number;
  title: string;
  body: string;
}

```

最后，*类型/注释*:

```
export default interface IComment {
  id: number;
  postId: number;
  name: string;
  email: string;
  body: string;
}

```

## 应用商店

现在我们需要创建模型。这些模型将实现上述类型，并定义其他实体之间的关系。为了定义关系，这些模型需要访问 app store。但是我们还没有创建应用商店。让我们现在就做吧。

在*商店*文件夹下 *src* 下创建一个名为*商店*的文件夹。现在，创建一个名为 *app.ts* 的文件。这个文件将包含应用程序的所有商店。现在，让我们把它变成一个空类，就像这样:

```
export default class AppStore {}

```

## 创建模型

现在我们有了应用商店，让我们在 *src* 下创建一个名为 *models* 的文件夹，并开始实现我们的模型。

首先是用户模型( *models/user.ts* ):

```
import AppStore from "../stores/app";
import IUser from "../types/user";

export default class User implements IUser {

  id: number;
  name: string;
  username: string;
  email: string;

  constructor(private store: AppStore, user: IUser) {
    this.id = user.id;
    this.name = user.name;
    this.username = user.username;
    this.email = user.email;
  }
}

```

我们的用户模型实现了用户类型，构造函数接受两个参数。首先，`AppStore`用于定义关系，其次，用户类型用于实例化成员变量。

同样，让我们创建一个帖子模型和一个评论模型。

下面是我们的 post 模型的代码( *models/post.ts* ):

```
import AppStore from "../stores/app";
import IPost from "../types/post";

export default class Post implements IPost {

  id: number;
  userId: number;
  title: string;
  body: string;

  constructor(private store: AppStore, post: IPost) {
    this.id = post.id;
    this.userId = post.userId;
    this.title = post.title;
    this.body = post.body;
  }
}

```

下面是我们的评论模型( *models/comment.ts* ):

```
import AppStore from "../stores/app";
import IComment from "../types/comment";

export default class Comment implements IComment {

  id: number;
  postId: number;
  name: string;
  email: string;
  body: string;

  constructor(private store: AppStore, comment: IComment) {
    this.id = comment.id;
    this.postId = comment.postId;
    this.name = comment.name;
    this.email = comment.email;
    this.body = comment.body;
  }
}

```

但是我们的模型遗漏了一些东西——关系。我们还没有定义它们之间的任何关系，但是我们将在创建商店之后这样做。

## 在 MobX 中创建商店

让我们创建商店，从用户商店开始。在*商店*文件夹中创建一个名为 *user.ts* 的文件。粘贴以下代码:

```
import User from "../models/user";
import IUser from "../types/user";
import AppStore from "./app";

export default class UserStore {

  byId = new Map<number, User>();

  constructor(private store: AppStore) {}

  load(users: IUser[]) {
    users.forEach((it) => this.byId.set(it.id, new User(this.store, it)));
  }

  get all() {
    return Array.from(this.byId.values());
  }
}

```

让我解释一下这里发生了什么。

首先，我们有一张名为`byId`的地图。它将存储由`id`输入的所有用户记录。为什么是地图？因为获取、更新和删除记录更容易。

构造函数再次接受`AppStore`，这样它就可以将它传递给模型实例。

接下来，我们有一个`load`方法，它接收一个`IUser`类型的数组，并通过实例化用户模型将其加载到`byId`地图中。

最后，我们有一个名为`all`的 getter 属性。它返回存储中所有可用的用户记录。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

注意，我们的商店是普通的。我们没有把它变成 MobX 商店。

为此，我们将对我们的商店进行以下更改:

1.  将 MobX 中的`byId`成员变量类型从`Map`更改为`observable.map`。通过创建属性`observable`，我们告诉 MobX 观察变化，并在必要时重新呈现组件。在我们的例子中，我们使用的是`observable.map`，这意味着当从`map`添加、更新或删除记录时，我们将接收到更新。
2.  `load`方法将数据加载到存储中。我们必须告诉 MobX，我们正在通过用动作修饰方法来更新可观察对象。
3.  `all` getter 属性实际上来源于`byId` observable。我们必须用`computed`来修饰它，让 MobX 知道这是一个计算属性。
4.  最后，我们必须调用`makeObservable`函数，在构造函数中传递这个实例，以使一切正常工作。

做了上面的改动之后，这就是我们店的样子。

```
import {
  action,
  computed,
  makeObservable,
  observable,
  ObservableMap,
} from "mobx";
import User from "../models/user";
import IUser from "../types/user";
import AppStore from "./app";

export default class UserStore {

  byId = observable.map<number, User>();

  constructor(private store: AppStore) {
    makeObservable(this);
  }

  @action load(users: IUser[]) {
    users.forEach((it) => this.byId.set(it.id, new User(this.store, it)));
  }

  @computed get all() {
    return Array.from(this.byId.values());
  }
}

```

以同样的方式，让我们创建`PostStore`和`CommentStore`。

对于*商店/邮局*:

```
import {
  action,
  computed,
  makeObservable,
  observable,
  ObservableMap,
} from "mobx";
import Post from "../models/post";
import IPost from "../types/post";
import AppStore from "./app";

export default class PostStore {

  byId = new observable.map<number, Post>();

  constructor(private store: AppStore) {
    makeObservable(this);
  }

  @action load(posts: IPost[]) {
    posts.forEach((it) => this.byId.set(it.id, new Post(this.store, it)));
  }

  @computed get all() {
    return Array.from(this.byId.values());
  }
}

```

现在，对于*商店/评论. ts* :

```
import {
  action,
  computed,
  makeObservable,
  observable,
  ObservableMap,
} from "mobx";

import IComment from "../types/comment";
import Comment from "../models/comment";
import AppStore from "./app";

export default class CommentStore {

  byId = new observable.map<number, Comment>();

  constructor(private store: AppStore) {
    makeObservable(this);
  }

  @action load(comments: IComment[]) {
    comments.forEach((it) => this.byId.set(it.id, new Comment(this.store, it)));
  }

  @computed get all() {
    return Array.from(this.byId.values());
  }
}

```

随着商店的创建，让我们在`AppStore`中实例化它们，如下所示:

```
import CommentStore from "./comment";
import PostStore from "./post";
import UserStore from "./user";

export default class AppStore {
  user = new UserStore(this);
  post = new PostStore(this);
  comment = new CommentStore(this);
} 

```

现在，让我们回到定义模型之间的关系。

**用户模型**:正如我们之前讨论的，用户会有很多帖子。让我们为它编码一个关系。

```
import AppStore from "../stores/app";
import IUser from "../types/user";

export default class User implements IUser {

  id: number;
  name: string;
  username: string;
  email: string;

  constructor(private store: AppStore, user: IUser) {
    this.id = user.id;
    this.name = user.name;
    this.username = user.username;
    this.email = user.email;
  }

  get posts() {
    return this.store.post.all.filter((it) => it.userId === this.id);
  }
}

```

如果您查看我们的 posts 关系，您会注意到它是从`post.all` computed property 派生的一个 computed property。我们必须用`computed` decorator 来修饰它，以便 MobX 计算它，并在构造函数中调用`makeObservable`来告诉 MobX 这个类有`observable`属性。

以下是最终版本:

```
import { computed, makeObservable } from "mobx";
import AppStore from "../stores/app";
import IUser from "../types/user";

export default class User implements IUser {
  id: number;
  name: string;
  username: string;
  email: string;

  constructor(private store: AppStore, user: IUser) {
    this.id = user.id;
    this.name = user.name;
    this.username = user.username;
    this.email = user.email;

    makeObservable(this);
  }

  @computed get posts() {
    return this.store.post.all.filter((it) => it.userId === this.id);
  }
}

```

现在让我们也对其他两个模型进行编码。

**岗位模型**:

```
import { computed, makeObservable } from "mobx";
import AppStore from "../stores/app";
import IPost from "../types/post";

export default class Post implements IPost {
  id: number;
  userId: number;
  title: string;
  body: string;

  constructor(private store: AppStore, post: IPost) {
    this.id = post.id;
    this.userId = post.userId;
    this.title = post.title;
    this.body = post.body;

    makeObservable(this);
  }

  @computed get user() {
    return this.store.user.byId.get(this.userId);
  }

  @computed get comments() {
    return this.store.comment.all.filter((it) => it.postId === this.id);
  }
}

```

**评论模式**:

```
import { computed, makeObservable } from "mobx";
import AppStore from "../stores/app";
import IComment from "../types/comment";

export default class Comment implements IComment {
  id: number;
  postId: number;
  name: string;
  email: string;
  body: string;

  constructor(private store: AppStore, comment: IComment) {
    this.id = comment.id;
    this.postId = comment.postId;
    this.name = comment.name;
    this.email = comment.email;
    this.body = comment.body;

    makeObservable(this);
  }

  @computed get post() {
    return this.store.post.byId.get(this.postId);
  }
}

```

这样，我们就成功地为我们的应用程序创建了整个商店！

## 网络层编码

到目前为止，我们已经为我们的应用程序创建了商店，但网络层正在等待。现在让我们编写我们的应用程序网络层，它将负责进行网络调用和加载商店中的数据。

我们将把网络层和商店完全分开。存储不知道数据是从哪里加载的。

让我们首先在 *src/apis* 文件夹下的 *app.ts* 文件中创建一个主`AppApi`类。它将包含其他资源的网络调用。

这里是 src/API/app . ts 的代码。

```
import axios from "axios";
import AppStore from "../stores/app";

export default class AppApi {

  client = axios.create({ baseURL: "https://jsonplaceholder.typicode.com" });

  constructor(store: AppStore) {}
}

```

我们还创建了一个`axios`客户端成员变量，它将用于进行网络调用。

我们将使用 [JSONPlaceholder](https://jsonplaceholder.typicode.com/) 假 REST API 来获取数据，并为我们的`axios`客户端将基本 URL 设置为[https://jsonplaceholder.typicode.com](https://jsonplaceholder.typicode.com)。

`AppStore`在构造函数中传递给我们。从 API 获取数据后，我们将使用`AppStore`将数据加载到商店中。

现在，我们有了我们的`AppApi`。让我们为您的个人资源编写网络调用。我们从用户开始。首先，在 *src/apis* 文件夹下创建一个名为 *user.ts* 的文件，并粘贴以下代码:

```
import AppStore from "../stores/app";
import AppApi from "./app";

export default class UserApi {

  constructor(private api: AppApi, private store: AppStore) {}

  async getAll() {
    const res = await this.api.client.get(`/users`);
    this.store.user.load(res.data);
  }

  async getById(id: number) {
    const res = await this.api.client.get(`/users/${id}`);
    this.store.user.load([res.data]);
  }
}

```

让我们了解一下这里发生了什么。

首先，我们创建一个`UserApi`类，将`AppApi`和`AppStore`作为构造函数的参数。然后，我们将使用`AppApi`实例来获取`axios`客户端并进行网络调用。获得数据后，我们使用`AppStore`将数据加载到 store 中。

让我们分别来看看这些方法:

`getAll`–向`/users`发送获取请求。响应作为用户对象的数组返回，这些对象使用存储区的 load 方法加载到存储区中。

`getById`–向`/users/$id`发送 get 请求以获取单个用户记录。响应作为单个用户对象返回，因此我们在将用户对象传递给存储的 load 方法之前，通过放入方括号将它包装在一个数组中。

类似地，我们将为另外两个资源 post 和 comment 创建另外两个 API 客户机。

下面是我们的 post*API/post . ts*的代码:

```
import AppStore from "../stores/app";
import AppApi from "./app";

export default class PostApi {

  constructor(private api: AppApi, private store: AppStore) {}

  async getAll() {
    const res = await this.api.client.get(`/posts`);
    this.store.post.load(res.data);
  }

  async getById(id: number) {
    const res = await this.api.client.get(`/posts/${id}`);
    this.store.post.load([res.data]);
  }

  async getByUserId(userId: number) {
    const res = await this.api.client.get(`/posts?userId=${userId}`);
    this.store.post.load(res.data);
  }
}

```

对于注释*API/comment . ts*:

```
import AppStore from "../stores/app";
import AppApi from "./app";

export default class CommentApi {

  constructor(private api: AppApi, private store: AppStore) {}

  async getByPostId(postId: number) {
    const res = await this.api.client.get(`/posts/${postId}/comments`);
    this.store.comment.load(res.data);
  }
}

```

注意，我只为必需的 API 调用编写方法。您可以根据需要添加或删除电话。

现在，我们已经写完了 API 客户端，让我们在`AppApi`类中注册它们。

```
import axios from "axios";
import AppStore from "../stores/app";
import CommentApi from "./comment";
import PostApi from "./post";
import UserApi from "./user";

export default class AppApi {

  client = axios.create({ baseURL: "https://jsonplaceholder.typicode.com" });

  user: UserApi;
  post: PostApi;
  comment: CommentApi;

  constructor(store: AppStore) {
    this.user = new UserApi(this, store);
    this.post = new PostApi(this, store);
    this.comment = new CommentApi(this, store);
  }
}

```

我们已经为整个应用程序创建了网络层。

## 使用商店和 API 的应用程序上下文

我们为我们的博客应用程序创建了商店和网络层，但必须有一种方法让我们在 React 组件中使用它们和 API。为此，我们将使用 React 上下文为组件提供存储和 API。

```
import React, { useContext } from "react";
import AppApi from "./apis/app";
import AppStore from "./stores/app";

interface AppContextType {
  store: AppStore;
  api: AppApi;
}

const AppContext = React.createContext<null | AppContextType>(null);

export const useAppContext = () => {
  const context = useContext(AppContext);
  return context as AppContextType;
};

export default AppContext;

```

这很简单。我们为`context`创建一个类型，它有两个属性:`store`和`api`。

然后我们创建一个名为`AppContext`的 React 上下文，为我们的应用程序提供`store`和`api`。最后，我们有一个`useAppContext`定制的 React 钩子来利用组件中的`store`和`api`。

让我们把上面的代码放到 *src 文件夹*下的 *app-context.ts 文件*中。

## 通过 MobX 使用组件

因为我们不关注 UI，所以组件很简单。我们将使用基本的 React 概念，如组件挂载回调的 useEffect，从 URL 获取参数的 useParams，我们将把模型实例传递给组件作为显示数据的道具。

让我们从创建注释组件开始。我们将组件文件放在 *src/components* 下，文件名为 *comment.tsx* 。

```
import CommentModel from "../models/comment";

const Comment: React.FC<{ comment: CommentModel }> = ({ comment }) => {
  return (
    <div>
      <strong>
        {comment.name} • {comment.email}
      </strong>
      <p>{comment.body}</p>
      <br />
    </div>
  );
};

export default Comment;

```

我们的组件缺少了非常重要的东西。我们必须让 MobX 知道，我们的组件将会观察到来自商店的可观察到的变化。

为此，我们将用来自`mobx-react`包的`observer`包装我们的 React 组件。下面是更新后的组件的外观:

```
import { observer } from "mobx-react";
import CommentModel from "../models/comment";

const Comment: React.FC<{ comment: CommentModel }> = observer(({ comment }) => {
  return (
    <div>
      <strong>
        {comment.name} • {comment.email}
      </strong>
      <p>{comment.body}</p>
      <br />
    </div>
  );
});

export default Comment;

```

以及 post 组件( *components/post.tsx* ):

```
import { observer } from "mobx-react";
import React from "react";
import { Link } from "react-router-dom";
import PostModel from "../models/post";

const Post: React.FC<{ post: PostModel; ellipsisBody?: boolean }> = observer(
  ({ post, ellipsisBody = true }) => {
    return (
      <div>
        <h2>{post.title}</h2>
        <p>
          {ellipsisBody ? post.body.substr(0, 100) : post.body}
          {ellipsisBody && (
            <span>
              ...<Link to={`/post/${post.id}`}>read more</Link>
            </span>
          )}
        </p>
        <p>
          Written by <Link to={`/user/${post.userId}`}>{post.user?.name}</Link>
        </p>
      </div>
    );
  }
);

export default Post;

```

## 在我们的 React 应用中构建页面

我们的 React 应用程序将有三个页面。

1.  主页–显示帖子列表
2.  帖子页面–显示收到的帖子内容和评论
3.  用户页面–显示用户信息和用户撰写的帖子列表

### 主页

将主页文件放在 pages/home.tsx 下。

```
import { observer } from "mobx-react";
import { useEffect, useState } from "react";
import { useAppContext } from "../app-context";
import Post from "../components/post";

const HomePage = observer(() => {
  const { api, store } = useAppContext();
  const [loading, setLoading] = useState(false);

  const load = async () => {
    try {
      setLoading(true);
      await api.post.getAll();
      await api.user.getAll();
    } finally {
      setLoading(false);
    }
  };

  useEffect(() => {
    load();
  }, []);

  if (loading) {
    return <div>loading...</div>;
  }

  return (
    <div>
      <h1>Posts</h1>
      {store.post.all.map((post) => (
        <Post key={post.id} post={post} />
      ))}
    </div>
  );
});

export default HomePage;

```

这里，我们使用`useAppContext`钩子来获取`store`和`api`。注意，我们有一个 useState 钩子来存储 API 调用的加载状态。之后我们有一个加载功能，设置加载到`true`，所有的帖子和用户，设置加载`false`。

最后，使用带有空数组的 Effect 作为`deps`来创建组件挂载效果，并在其中调用`load`函数。

让我们编码其他剩余的页面:

**发帖页面** *(pages/post.tsx)*

```
import { observer } from "mobx-react";
import { useEffect, useState } from "react";
import { useParams } from "react-router";
import { useAppContext } from "../app-context";
import Post from "../components/post";

const PostPage = observer(() => {

  const { api, store } = useAppContext();

  const [loading, setLoading] = useState(false);

  const params = useParams<{ postId: string }>();

  const postId = Number(params.postId);

  const load = async () => {
    try {
      setLoading(true);
      await api.post.getById(postId);
      await api.comment.getByPostId(postId);
    } finally {
      setLoading(false);
    }
  };

  useEffect(() => {
    load();
  }, []);

  if (loading) {
    return <div>loading...</div>;
  }

  const post = store.post.byId.get(Number(params.postId));

  if (!post) {
    return <div>Post not found</div>;
  }

  return (
    <div>
      <Post ellipsisBody={false} post={post} />
      <h2>Comments </h2>
      {post.comments.map((comment) => (
        <Comment key={comment.id} comment={comment} />
      ))}
    </div>
  );
});

export default PostPage;

```

我们唯一的区别是:

1.  我们使用`react-router-dom`的`useParams`钩子得到了`postId`
2.  我们正在加载帖子和帖子的评论
3.  在渲染中，我们使用在模型中创建的`post.comments`关系来获取和渲染 post 的所有评论

最后，让我们编写用户页面代码。

```
import { observer } from "mobx-react";
import { useEffect, useState } from "react";
import { useParams } from "react-router";
import { useAppContext } from "../app-context";
import Post from "../components/post";

const UserPage = observer(() => {
  const { api, store } = useAppContext();
  const [loading, setLoading] = useState(false);
  const params = useParams<{ userId: string }>();
  const userId = Number(params.userId);

  const load = async () => {
    try {
      setLoading(true);
      await api.user.getById(userId);
      await api.post.getByUserId(userId);
    } finally {
      setLoading(false);
    }
  };

  useEffect(() => {
    load();
  }, []);

  if (loading) {
    return <div>loading...</div>;
  }

  const user = store.user.byId.get(userId);

  if (!user) {
    return <div>User not found</div>;
  }

  return (
    <div>
      <h3>
        {user.name} • {user.username}
      </h3>
      <p>{user.email}</p>
      <h2>Posts</h2>
      {user.posts.map((post) => (
        <Post key={post.id} post={post} />
      ))}
    </div>
  );
});

export default UserPage;

```

这样，我们就成功地完成了整个应用程序！

现在只剩下一项:根应用程序组件。记住，我们在启动时删除了 *App.tsx* 文件。让我们将它添加回 *src 文件夹*下。

*app.tsx*

```
import { BrowserRouter, Route, Switch } from "react-router-dom";
import AppContext from "./app-context";
import AppStore from "./stores/app";
import AppApi from "./apis/app";
import HomePage from "./pages/home";
import PostPage from "./pages/post";
import UserPage from "./pages/user";

const store = new AppStore();
const api = new AppApi(store);

function App() {
  return (
    <AppContext.Provider value={{ store, api }}>
      <BrowserRouter>
        <Switch>
          <Route path="/user/:userId" component={UserPage} />
          <Route path="/post/:postId" component={PostPage} />
          <Route path="/" component={HomePage} />
        </Switch>
      </BrowserRouter>
    </AppContext.Provider>
  );
}

export default App;

```

我们正在实例化商店和 API，并通过 AppContext.Provider 将其提供给我们的应用程序。

使用来自`react-router-dom`的`BrowserRouter`，我们将呈现我们的页面。就是这样。如果您在浏览器中运行并打开该应用程序，您应该会看到该应用程序正在运行。

## 结论

在本教程中，我们学习了如何使用 MobX 管理大规模的 React 状态。感谢您的阅读，如果您有任何问题，请联系我！

你可以在这里或者[获得整个项目的源代码](https://varunpvp.github.io/react-mobx-app/)。

> 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个标签页或窗口中注销。重新加载以刷新您的会话。重新加载以刷新您的会话。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)