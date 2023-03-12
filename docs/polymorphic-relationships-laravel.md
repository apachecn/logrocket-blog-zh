# Laravel 中的多态关系及其用例

> 原文：<https://blog.logrocket.com/polymorphic-relationships-laravel/>

***编者按:*** *这篇文章于 2021 年 9 月更新，以确保准确性，并包括 Laravel 中多种类型的多态关系的信息，包括一对一、多对一和多对多关系。*

在软件开发中，模型可以属于多个实体的情况并不少见。这种类型的模型保持相同的结构，无论它连接到哪个模型都不会改变。

这种情况的一个常见例子是注释。例如，在博客中，评论可以出现在帖子或页面上，但无论是帖子还是页面，都保持相同的结构。这种行为被称为多态性。

在本文中，我们将回顾 Laravel 中的多态关系，它们是如何工作的，以及它们最适用的各种用例。

## Laravel 中有哪些多态关系？

考虑到上面提到的例子，我们有两个实体:`Post`和`Page`。为了允许对每一项进行评论，我们可以决定这样设置我们的数据库:

```
posts:
  id
  title
  content

posts_comments:
  id
  post_id
  comment
  date

pages:
  id
  body

pages_comments:
  id
  page_id
  comment
  date
```

上面的方法让我们创建多个注释表——`posts_comments`和`pages_comments`——它们做同样的事情，除了它们指向不同的实体。

有了多态关系，对于同样的情况，我们可以遵循一种更干净、更简单的方法。

```
posts:
  id
  title
  content

pages:
  id
  body

comments:
  id
  commentable_id
  commentable_type
  date
  body
```

根据定义，多态性是以几种不同的形式出现的情况，这是我们上面试图遵循的方法。我们有两个重要的新列需要注意:`commentable_id`和`commentable_type`。

在上面的例子中，我们将`page_comments`和`post_comments`合并在一起，用`commentable_id`和`commentable_type`替换每个表中的`post_id`和`page_id`，得到`comments`表。

`commentable_id`列将包含文章或页面的 ID，而`commentable_type`将包含拥有该记录的模型的类名。`commentable_type`将存储类似于`App\Post`的东西，这是 ORM 在试图访问该值时确定它属于哪个模型并返回的方式。

这里，我们有三个实体:`Post`、`Page`和`Comments`。

`Post`可以有`Comments`。
`Page`可以有`Comments`。
和`Comments`可以属于`Post`或`Page`。

让我们创建我们的迁移:

```
Schema::create('posts', function (Blueprint $table) {
    $table->increments('id');
    $table->string('title');
    $table->text('content');
});

Schema::create('pages', function (Blueprint $table) {
    $table->increments('id');
    $table->text('body');
});

Schema::create('comments', function (Blueprint $table) {
    $table->increments('id');
    $table->morphs(‘commentable’);
    $table->text('body');
    $table->date('date');
});
```

`$table→morphs('commentable')`将使用传递给它的文本自动为`id`和`type`创建两列，因此它将产生`commentable_id`和`commentable_type`。

接下来，我们为我们的实体创建模型:

```
//file: app/Post.php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Post extends Model
{
    /**
     * Get all of the post's comments.
     */
    public function comments()
    {
        return $this->morphMany('App\Comment', 'commentable');
    }
}

//file: app/Page.php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Page extends Model
{
    /**
     * Get all of the page's comments.
     */
    public function comments()
    {
        return $this->morphMany('App\Comment', 'commentable');
    }
}

//file: app/Comment.php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Comment extends Model
{
    /**
     * Get all of the models that own comments.
     */
    public function commentable()
    {
        return $this->morphTo();
    }
}
```

在上面的代码中，我们声明了我们的模型，并且还使用了两个方法，`morphMany()`和`morphTo`，帮助我们定义一对多多态关系。

`Page`和`Post`模型都有一个`comments()`函数，将一个`morphMany()`返回给`Comment`模型。这预示着两人都有望与众多评论发生关系。

`Comment`模型有一个`commentable()`函数，它返回一个`morphTo()`函数，表明这个类与其他模型相关。

一旦建立了这些，通过我们的模型访问数据和处理这种关系就变得容易了。

以下是一些例子:

要访问页面的所有注释，我们可以使用模型中声明的`comments`动态属性。

```
// getting comments for a sample page...
  $page = Page::find(3);

  foreach($page->comment as $comment)
  {
    // working with comment here...
  }
```

要检索帖子上的评论:

```
// getting comments for a sample post...
  $post = Post::find(13);

  foreach($post->comment as $comment)
  {
    // working with comment here...
  }
```

您也可以逆转这种检索。如果您有一个注释 ID，并且想要在`Comment`模型上使用`commentable`方法找出它属于哪个实体，您可以使用:

```
$comment = Comment::find(23);

  // getting the model...
  var_dump($comment->commentable);
```

有了这个设置，你应该知道使用`Comment`关系的模型的数量不限于两个。您可以在不进行任何重大更改或破坏代码的情况下，尽可能地添加更多内容。例如，让我们创建一个新的`Product`模型添加到您的站点中，它也可以有评论。

首先，我们为新模型创建迁移:

```
Schema::create('products', function (Blueprint $table) {
    $table->increments('id');
    $table->string('name');
});
```

然后我们创建模型类:

```
//file: app/Product.php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Product extends Model
{
    /**
     * Get all of the product's comments.
     */
    public function comments()
    {
        return $this->morphMany('App\Comment', 'commentable');
    }
}
```

仅此而已。您的评论系统现在支持您的产品，并且可以像其他实体一样进行检索。

```
// getting comments for a sample product...
  $product = Product::find(3);

  foreach($product->comment as $comment)
  {
    // working with comment here...
  }
```

## Laravel 中多态关系的类型

上面的例子已经覆盖了一对多的多态关系，其中一个模型，例如一个页面，可以有许多与之相关的模型，例如评论。类似于关系数据库中关系的工作方式，还有其他类型的多态关系。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

### 一对一的多态关系

一对一多态关系是指一个模型可以属于多种类型的模型，但只能属于一个关联。一个典型的例子是帖子上的特色图片和用户的头像。然而，唯一改变的是我们如何通过使用`morphOne`来获得相关的模型。

```
class Post extends Model
{
    /**
     * Get the post's only image.
     */
    public function image()
    {
        return $this->morphOne(Image::class, 'imageable');
    }
}

class User extends Model
{
    /**
     * Get the user's only image.
     */
    public function image()
    {
        return $this->morphOne(Image::class, 'imageable');
    }
}

```

### 一对多多态关系

一对多关系是指一个模型可以与多个模型有多个关联，但是您一次只想检索一个。这利用了 Laravel 的`ofMany`辅助方法和`morphOne`来检索所需的单个关联。

```
public function latestImage()
{
    return $this->morphOne(Image::class, 'imageable')->latestOfMany();
}

```

查看 [Laravel 的文档](https://laravel.com/docs/8.x/eloquent-relationships#advanced-has-one-of-many-relationships)中可以用来构建这种关系的各种`ofMany`方法。

### 多对多多态关系

多对多多态关系处理的情况比一对多和一对一关系稍微复杂一些。一个简单的例子是当我们有文章、页面和标签时。多个帖子可以有多个标签。多个标签可以属于多个页面。

在我们的 post 模型中，我们可以使用`morphToMany`检索相关的标签。

```
class Post extends Model
{
    /**
     * Get all of the tags for the post.
     */
    public function tags()
    {
        return $this->morphToMany(Tag::class, 'taggable');
    }
}
And from the Tag model, return all connected associations from the desired model

class Tag extends Model
{
    /**
     * Get all of the posts that are assigned this tag.
     */
    public function posts()
    {
        return $this->morphedByMany(Post::class, 'taggable');
    }
}

```

## 多态关系的其他用例

### 多种用户类型

多态关系也发挥作用的一个常见用例是当需要多种用户类型时。这些用户类型通常有一些相似的字段，然后还有一些他们特有的字段。这可能是一种`User`和`Admin`类型，在拼车应用程序的情况下可能是一种`Driver`或`Rider`类型，甚至是有许多种用户或专业人士的应用程序。

每个用户可能有一个名字、电子邮件、虚拟电话等。，然后再添加其他细节。下面是一个招聘平台的示例模式，它允许您招聘不同种类的工匠:

```
user:
   id
   name
   email
   avatar
   address
   phone
   experience
   userable_id
   userable_type

drivers:
  id
  region
  car_type //manual or automatic
  long_distance_drive

cleaners:
  id
  use_chemicals //uses chemicals in cleaning
  preferred_size //size of cleaning

  ...
```

在这个场景中，我们可以获得用户的基本数据，而不用担心他们是否是清洁工，同时，我们可以在需要时从`userable_type`中获得他们的类型，并从那个表的`userable_id`列中获得 ID。这将是一对一的多态关系。

### 对附件和媒体使用多态关系

在类似上面提供的评论示例的场景中，帖子和页面——甚至消息——可能需要附件或任何其他形式的媒体。这比为每种附件创建一个表要好得多。

```
messages:
  id
  user_id
  recipient_id
  content

attachment:
  id
  url
  attachable_id
  attachable_type
```

在上面的例子中，`attachable_type`可以作为消息、帖子或页面的模型。

使用多态关系背后的一般概念围绕着识别两个或更多模型之间可能需要的相似性，并在此基础上进行构建，而不是复制和创建大量的表和代码。

## 结论

我们已经讨论了多态关系的基本应用及其可能的用例。我们还应该注意到，多态关系并不是解决所有问题的完整方案，应该只在方便或感觉合适的时候使用。在您的应用程序中有令人兴奋的多态关系用例吗？下面分享！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)