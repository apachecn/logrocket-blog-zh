# Iced.rs 教程:如何构建一个简单的 Rust 前端 web 应用程序

> 原文：<https://blog.logrocket.com/iced-rs-tutorial-rust-frontend-web-app/>

之前，我们看了如何使用 Yew web 框架在 Rust 中创建一个基本的基于 W [asm](https://blog.logrocket.com/rust-webassembly-frontend-web-app-yew/) [的前端应用。在本教程中，我们将向您展示如何使用 Iced.rs GUI 库做类似的事情。](https://blog.logrocket.com/rust-webassembly-frontend-web-app-yew/)

为了展示 Iced.rs 的作用，我们将使用 ice 和 Rust 构建一个非常基本的前端应用程序，它使用 [JSONPlaceholder](https://jsonplaceholder.typicode.com/) 来获取数据。我们将获取帖子并将它们显示在一个列表中，每个帖子都有一个详细链接，引导用户找到带有评论的完整帖子。

## 冰镇 rs 对紫杉

[ice . RS](https://iced.rs/)和 [Yew](https://yew.rs/) 最大的区别在于，虽然 Yew 纯粹是为了构建 web apps，但 ice 的重点其实是跨平台应用；web 只是你可以为之构建应用程序的几个平台之一。

就风格而言，虽然任何了解 React 和 JSX 的人都会觉得紫杉很熟悉，但就建筑而言，Iced.rs 的灵感来自神奇的榆树。

另一件需要注意的事情是，Iced.rs 还处于早期的积极开发阶段。虽然完全有可能用它来构建基本的应用程序，但生态系统还不是特别成熟。除了[文档](https://docs.rs/iced/latest/iced/)和[示例](https://github.com/iced-rs/iced/tree/master/examples)，在这个早期阶段，开始有点困难，尤其是如果你试图构建一些复杂的东西。

也就是说，看起来这个项目管理得很好，并且通过它的路线图进展很快。

## 设置 Iced.rs

按照本教程，您所需要的只是一个最新的 Rust 安装版本( [Rust 1.55](https://blog.rust-lang.org/2021/09/09/Rust-1.55.0.html) 是撰写本文时的最新版本)。

首先，创建一个新的 Rust 项目:

```
cargo new rust-frontend-example-iced
cd rust-frontend-example-iced

```

接下来，编辑`Cargo.toml`文件并添加您需要的依赖项:

```
[dependencies]
iced_web = "0.4"
iced = { version = "0.3", features = ["tokio"] }
serde = { version = "1.0", features = ["derive"] }
serde_json = "1.0"
wasm-bindgen = "0.2.69"
reqwest = { version = "0.11", features = ["json"] }

```

在本教程中，我们将使用 Iced.rs 作为我们的前端框架。由于 ice . RS 是一个跨平台的 GUI 库，我们还需要添加`iced_web`，这使我们能够从我们的 ice . RS 应用程序创建一个基于 Wasm 的单页面 web 应用程序。

为了以 JSON 的形式获取数据，我们还将添加`reqwest`和`serde`。我们将固定`wasm-bindgen`版本，这样我们在构建时就不会遇到任何兼容性问题。这很有用，因为 Wasm 生态系统仍在不断变化，为您的项目使用特定的版本可以确保您不会在某一天醒来发现一个坏项目。

## 从`index.html`开始

我们使用[主干](https://github.com/thedodd/trunk)抽象出构建 Wasm 应用程序的本质。Trunk 期望项目根中有一个`index.html`文件，我们将提供:

```
<!DOCTYPE html>
<html>
  <head>
    <meta http-equiv="Content-type" content="text/html; charset=utf-8"/>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Tour - Iced</title>
  </head>
  <body>
    <script type="module">
      import init from "./iced/iced.js";

      init('./iced/iced_bg.wasm');
    </script>
  </body>
</html>

```

在这里，我们简单地创建了一个 HTML 框架，并添加了代码片段来包含我们编译的 Iced.rs 源代码。

这里我们不添加任何 CSS 使用 Iced.rs，我们可以构建自己的定制小部件，并在代码中对它们进行样式化。当然，添加 CSS 是可能的，但是在许多情况下，样式在小部件中被添加到输出 HTML 中的内联样式所覆盖。

随着所有这些设置的完成，我们可以开始编写一些 Rust 代码了。

## 数据存取

我们将从构建数据访问层开始。为此，我们将在`src`文件夹中的`main.rs`旁边创建一个`data.rs`文件，并使用`mod data;`将这个`data`模块添加到我们的`main.rs`中。

因为我们的计划是获取一个帖子列表，然后获取一个帖子的细节及其评论，所以我们需要用于`Post`和`Comment`的结构。

```
use serde::Deserialize;

#[derive(Debug, Clone, Deserialize)]
#[serde(rename_all = "camelCase")]
pub struct Post {
    pub id: i32,
    pub user_id: i32,
    pub title: String,
    pub body: String,
}

#[derive(Debug, Clone, Deserialize)]
#[serde(rename_all = "camelCase")]
pub struct Comment {
    pub post_id: i32,
    pub id: i32,
    pub name: String,
    pub email: String,
    pub body: String,
}

```

接下来，我们将实现一些数据获取例程来从 JSONPlaceholder 获取实际的 JSON 数据。

```
impl Post {
    pub async fn fetch_all() -> Result<Vec<Post>, String> {
        let url = String::from("https://jsonplaceholder.typicode.com/posts/");
        reqwest::get(&url)
            .await
            .map_err(|_| String::new())?
            .json()
            .await
            .map_err(|_| String::new())
    }

    pub async fn fetch(id: i32) -> Result<Post, String> {
        let url = format!("https://jsonplaceholder.typicode.com/posts/{}", id);
        reqwest::get(&url)
            .await
            .map_err(|_| String::new())?
            .json()
            .await
            .map_err(|_| String::new())
    }
}

```

在这个简单的例子中，我们不会处理连接错误，所以我们只在这里返回空字符串。然而，您可以通过返回错误消息或创建自定义错误枚举来处理应用程序中的不同错误情况，从而轻松扩展这一功能。

我们有一个获取所有帖子的函数。为了执行 HTTP 请求，我们使用 [Reqwest](https://github.com/seanmonstar/reqwest) HTTP 客户端，它也支持 Wasm 作为构建目标。

为了获取帖子的详细信息，我们创建了第二个函数，它将帖子的 ID 作为参数，传递给 JSONPlaceholder，并返回类型为`Post`的结果。

现在我们需要为评论做同样的事情。

```
impl Comment {
    pub async fn fetch_for_post(id: i32) -> Result<Vec<Comment>, String> {
        let url = format!(
            "https://jsonplaceholder.typicode.com/posts/{}/comments/",
            id
        );
        reqwest::get(&url)
            .await
            .map_err(|_| String::new())?
            .json()
            .await
            .map_err(|_| String::new())
    }
}

```

这个数据访问函数还获取一个 post ID 并获取给定 post 的所有评论，[将 JSON](https://blog.logrocket.com/rust-serialization-whats-ready-for-production-today/) 反序列化为一个`Comment`结构的向量。

有了我们的`data`模块，我们现在能够获取帖子和评论，并且我们有了包含我们数据的良好结构。

## 用 Iced.rs 构建用户界面

现在是时候开始构建 UI 了。

与 ELM 类似，Iced.rs 应用程序包含四个核心概念:

*   状态
*   信息
*   更新
*   视角

`State`是您的应用程序的状态。例如，在我们的例子中，这是我们从 JSONPlaceholder 获取并显示的数据。

`Messages`用于触发应用程序内部的流程。它们可以是用户交互、定时事件或任何其他可能改变应用程序的事件。

`Update`逻辑用于对这些`Messages`做出反应。例如，在我们的应用程序中，可能有一个`Message`用于导航到详细页面。在此消息的`Update`逻辑中，我们将设置路由并获取数据，因此我们可以将应用程序状态从`List`更新为`Detail`。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

最后，`View`逻辑描述了如何呈现应用程序的某个部分。它显示`State`并可能在用户交互上产生`Messages`。

我们将首先为帖子和评论构建非常简单的小部件，其中将包括这些内容的呈现逻辑，然后使用它们将所有内容与基本的路由和数据访问连接在一起。

## 发布和评论小部件

我们将从`Comment`小部件开始，因为它非常简约和简单。

```
struct Comment {
    comment: data::Comment,
}

impl Comment {
    fn view(&self) -> Element<Message> {
        Column::new()
            .push(Text::new(format!("name: {}", self.comment.name)).size(12))
            .push(Text::new(format!("email: {}", self.comment.email)).size(12))
            .push(Text::new(self.comment.body.to_owned()).size(12))
            .into()
    }
}

```

基本上，我们的`Comment`小部件仅仅有一个`data::Comment`作为它的状态，所以一旦我们从数据层获得评论数据，我们就可以开始创建这些小部件。

在这种情况下，`view`描述了如何呈现一个`Comment`。在这种情况下，我们创建一个`Column`，在 HTML 中，它只是一个`div`。然而，还有一个`Row`和其他预先存在的`Container`小部件，我们可以用它们来以一种反应灵敏、连贯的方式构建我们的 UI。

在本专栏中，我们简单地添加了一些`iced::Text`小部件，它们基本上编译成`p`(文本段落)。我们给它一个字符串，然后手动设置大小。

最后，我们使用`.into()`,因为`view`返回一个`Element<Message>`,其中`Element`只是 ice 的通用小部件，而`Message`是我们的消息抽象，我们稍后将会看到。

现在让我们看看`Post`小部件的实现:

```
struct Post {
    detail_button: button::State,
    post: data::Post,
}

impl Post {
    fn view(&mut self) -> Element<Message> {
        Column::new()
            .push(Text::new(format!("id: {}", self.post.id)).size(12))
            .push(Text::new(format!("user_id: {}", self.post.user_id)).size(12))
            .push(Text::new(format!("title: {}", self.post.title)).size(12))
            .push(Text::new(self.post.body.to_owned()).size(12))
            .into()
    }

    fn view_in_list(&mut self) -> Element<Message> {
        let r = Row::new().padding(5).spacing(5);
        r.push(
            Column::new().spacing(5).push(
                Text::new(self.post.title.to_owned())
                    .size(12)
                    .vertical_alignment(VerticalAlignment::Center),
            ),
        )
        .push(
            Button::new(&mut self.detail_button, Text::new("Detail").size(12))
                .on_press(Message::GoToDetail(self.post.id)),
        )
        .into()
    }
}

```

根本的区别在于，在我们的例子中，一个`Post`也包含一个`detail_button`。这使我们能够为我们的文章列表创建细节按钮。

对于一篇文章，我们也有两个不同的渲染函数:细节视图，它类似于我们在`Comment`中的`view`函数，以及`view_in_list`函数，它使用一些`padding`和`spacing`(网络用语中的填充和边距)创建一个列表元素，使所有内容对齐，重要的是，在行尾添加了`Detail`视图按钮。

如果您查看一些样式选项的[文档](https://docs.rs/iced/latest/iced/widget/button/struct.Style.html),您会从 web 上认出许多选项，因此样式化您的组件非常简单。

要创建一个按钮，我们需要一个`button::State`。我们可以在点击它时添加一个动作，比如按下按钮时添加`Message::GoToDetail(self.post.id)`。

有了这两个基本的小部件，让我们构建我们的`App`小部件，并开始构建一个可运行的应用程序。

## 把所有的放在一起

在我们的`main.rs`中，我们可以从导入和我们的`main`函数开始。

```
use iced::{
    button, executor, Align, Application, Button, Clipboard, Column, Command, Element, Row,
    Settings, Text, VerticalAlignment,
};

mod data;

pub fn main() -> iced::Result {
    App::run(Settings::default())
}

```

运行 Iced.rs 应用程序非常简单。我们需要实现`Application`特征的东西——在我们的例子中是`App`——然后我们可以用默认设置对它调用`run()`。

有了这些设置，我们就可以设置一些基本的应用程序设置，比如默认字体、窗口设置(对于本地应用程序)等等。

接下来，让我们看看我们的`App`结构和其中的应用程序状态。

```
#[derive(Clone, Debug)]
enum Route {
    List,
    Detail(i32),
}

struct App {
    list_button: button::State,
    route: Route,
    posts: Option<Vec<Post>>,
    post: Option<Post>,
    comments: Option<Vec<Comment>>,
}

```

在我们的`App`中，我们有`list_button`的按钮状态，这是返回主页的按钮，我们的根。

然后我们用两条路由`List`和`Detail`保持`route`状态。我找不到任何成熟的`iced_web`路由库，所以我们将构建我们自己的非常简单的路由，不改变浏览器中的 URL、历史或后台处理。

如果您对构建更充实的路由感兴趣，您可以将`web-sys`添加到您的依赖项中:

```
[dependencies.web-sys]
version = "0.3.32"
features = [
    "Document",
    "Window",
]

```

然后，您可以设置 URL，例如使用:

```
let win = web_sys::window().unwrap_throw();
win.location()
  .set_hash(&format!("/detail/{}", id))
  .unwrap_throw();

```

但在本教程中，我们不会走进那个兔子洞。

此外，我们为`posts`、`post`和`comments`保持状态。这些只是分别与我们的部件`Post`和`Comment`以及填充它们的向量相关联的选项。

接下来，让我们定义我们的`Message`结构，它定义了应用程序中的数据流。

```
#[derive(Debug, Clone)]
enum Message {
    PostsFound(Result<Vec<data::Post>, String>),
    PostFound(Result<data::Post, String>),
    CommentsFound(Result<Vec<data::Comment>, String>),
    GoToList,
    GoToDetail(i32),
}

```

我们的应用程序中有五条消息。

最基本的是`GoToList`和`GoToDetail`，本质上就是我们的路由消息。如果有人点击`Home`或`Detail`链接，这些就会被触发。

然后，当数据从我们的数据访问层返回时，`PostsFound`、`PostFound`和`CommentsFound`被触发。我们稍后将研究这些消息的处理。

让我们开始为`App`实现`Application`特征。

```
impl Application for App {
    type Executor = executor::Default;
    type Flags = ();
    type Message = Message;

    fn new(_flags: ()) -> (App, Command<Message>) {
        (
            App {
                list_button: button::State::new(),
                route: Route::List,
                posts: None,
                post: None,
                comments: None,
            },
            Command::perform(data::Post::fetch_all(), Message::PostsFound),
        )
    }

    fn title(&self) -> String {
        String::from("App - Iced")
    }

```

`Executor`实际上是一个异步执行器，可以运行期货，比如 async-io 或 Tokio。我们只是使用默认值。我们也不使用任何标志，并将我们的`Message`结构设置为用于消息。

在`new`函数中，我们只是为所有属性设置默认值，返回`App`，更重要的是，返回一个新的`Command`。这种返回`Command<Message>`的机制就是 Iced.rs 中触发消息的方式。

在这种情况下，在创建`App`时，我们从我们的数据访问层执行`Post::fetch_all` future，并提供一个`Message`，它将被未来的结果调用—在这种情况下是`Message::PostsFound`。

这意味着当应用程序打开时，我们立即获取所有帖子，以便我们可以显示它们。

使用`title()`，我们也可以设置标题，但那不是特别有趣。

让我们看看如何在`update`中管理`Messages`:

```
    fn update(&mut self, message: Message, _c: &mut Clipboard) -> Command<Message> {
        match message {
            Message::GoToList => {
                self.post = None;
                self.comments = None;
                self.route = Route::List;
                Command::perform(data::Post::fetch_all(), Message::PostsFound)
            }
            Message::GoToDetail(id) => {
                self.route = Route::Detail(id);
                self.posts = None;
                Command::batch(vec![
                    Command::perform(data::Post::fetch(id), Message::PostFound),
                    Command::perform(data::Comment::fetch_for_post(id), Message::CommentsFound),
                ])
            }
            Message::PostsFound(posts) => {
                match posts {
                    Err(_) => (),
                    Ok(data) => {
                        self.posts = Some(
                            data.into_iter()
                                .map(|post| Post {
                                    detail_button: button::State::new(),
                                    post,
                                })
                                .collect(),
                        );
                    }
                };
                Command::none()
            }
            Message::PostFound(post) => {
                match post {
                    Err(_) => (),
                    Ok(data) => {
                        self.post = Some(Post {
                            detail_button: button::State::new(),
                            post: data,
                        });
                    }
                }
                Command::none()
            }
            Message::CommentsFound(comments) => {
                match comments {
                    Err(_) => (),
                    Ok(data) => {
                        self.comments = Some(
                            data.into_iter()
                                .map(|comment| Comment { comment })
                                .collect(),
                        );
                    }
                };
                Command::none()
            }
        }
    }

```

这是相当多的代码，但就逻辑而言，这也是我们的应用程序的核心部分，所以让我们一步一步来看。

首先，我们处理`GoToList`。点击`Home`时触发。如果发生这种情况，我们重置`post`和`comment`的保存数据，将`route`设置为`List`，最后，触发获取所有帖子的请求。

在`GoToDetail`中，我们基本上做了同样的事情，但是这一次我们清除了`posts`并发出了一个`batch`命令——即获取给定帖子 ID 的帖子和评论。

现在变得有趣了。处理`Message::PostsFound(posts)`在`fetch_all`成功的任何时候发生。因为我们忽略了错误，所以如果我们得到一个错误，我们不做任何事情，但是如果我们得到数据，我们实际上用返回的数据创建了一个`Post`窗口小部件的向量，并将`self.posts`设置为窗口小部件的列表。

这意味着当数据返回时，我们实际上更新了我们的应用程序状态。在这种情况下，我们返回`Command::none()`，这意味着命令链到此结束。

对于`PostFound`和`CommentsFound`，处理基本上是相同的:我们根据返回的数据用小部件更新应用程序状态。

最后，让我们看看`view`函数，看看我们是如何呈现完整的应用程序的。

```
    fn view(&mut self) -> Element<Message> {
        let col = Column::new()
            .max_width(600)
            .spacing(10)
            .padding(10)
            .align_items(Align::Center)
            .push(
                Button::new(&mut self.list_button, Text::new("Home")).on_press(Message::GoToList),
            );
        match self.route {
            Route::List => {
                let posts: Element<_> = match self.posts {
                    None => Column::new()
                        .push(Text::new("loading...".to_owned()).size(15))
                        .into(),
                    Some(ref mut p) => App::render_posts(p),
                };
                col.push(Text::new("Home".to_owned()).size(20))
                    .push(posts)
                    .into()
            }
            Route::Detail(id) => {
                let post: Element<_> = match self.post {
                    None => Column::new()
                        .push(Text::new("loading...".to_owned()).size(15))
                        .into(),
                    Some(ref mut p) => p.view(),
                };
                let comments: Element<_> = match self.comments {
                    None => Column::new()
                        .push(Text::new("loading...".to_owned()).size(15))
                        .into(),
                    Some(ref mut c) => App::render_comments(c),
                };

                col.push(Text::new(format!("Post: {}", id)).size(20))
                    .push(post)
                    .push(comments)
                    .into()
            }
        }
    }

```

首先，我们创建另一个`Column`，这一次使用最大宽度，将所有内容居中对齐。这是我们最外面的容器。

在这个容器中，我们添加了`Home`按钮，点击它会触发`GoToList`消息，将我们导航回主页。

然后我们在`self.route`上进行匹配，这是我们在应用程序中的路由状态。

如果我们在`List`路线上，我们检查是否已经设置了`self.posts`;记住，当这个被设置时，获取帖子的请求已经被触发了。如果没有，我们显示一个`loading..`消息。

一旦有了数据，我们就调用`App::render_posts`，一个实际呈现文章列表的助手。

```
impl App {
    fn render_posts(posts: &mut Vec<Post>) -> Element<Message> {
        let c = Column::new();
        let posts: Element<_> = posts
            .iter_mut()
            .fold(Column::new().spacing(10), |col, p| {
                col.push(p.view_in_list())
            })
            .into();
        c.push(posts).into()
    }

    fn render_comments(comments: &Vec<Comment>) -> Element<Message> {
        let c = Column::new();
        let comments: Element<_> = comments
            .iter()
            .fold(Column::new().spacing(10), |col, c| col.push(c.view()))
            .into();
        c.push(Text::new(String::from("Comments:")).size(15))
            .push(comments)
            .into()
    }
}

```

还有一个相应的助手来呈现评论列表。在这两种情况下，我们简单地迭代数据并创建一个列，其中包含所有单独的 Post 和 Comment 小部件。

最后，我们将单词`Home`和返回的`posts`小部件列表推到一个列中，将其添加到根列中。

对于`Detail`路径，我们同样检查`loading`的情况，一旦有了数据，就将所有数据组合在一起，并将其返回到一个新列中。

## 测试我们的 Iced.rs 应用程序

现在我们已经完成了简单的列表/细节应用程序，让我们测试一下，看看它是否真的能工作。

让我们运行`trunk serve`，它将在 [http://localhost:8080](http://localhost:8080) 上构建并运行我们的应用程序。

最初，我们看到`Home`页面有一个帖子列表和它们的`Detail`链接:

![Iced.rs Web App Example](img/e337b7dcbc6e0a059028d9b1d74120d8.png)

点击其中的一个链接，我们会被定向到文章的详细页面，显示文章的正文和对这篇文章的所有评论，这些都是从 JSONPlaceholder 并行获取的。

![Iced.rs Web App Example](img/58965e5a5a58cfde645ec886eb9c0f6e.png)

有用！

你可以在 [GitHub](https://github.com/zupzup/rust-frontend-example-iced) 上找到这个例子的完整代码。

## 结论

在过去玩过 Elm 并且熟悉 Rust 之后，用 Iced.rs 构建这个小应用实际上是一个非常简单的体验。

当谈到使用 Iced.rs for web 时，最容易注意到的一点是缺少一些基础知识，比如一个成熟的路由库。一般来说，看起来 Iced 的重点不是在网络上，而是在跨平台的 GUI 应用上。

除此之外，例子和文档也很有帮助。已经有一个活跃的社区在研究和使用 Iced.rs。对于跨平台开发为重点的用例，我可以肯定地看到 Iced.rs 在未来是一个强有力的竞争者。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。