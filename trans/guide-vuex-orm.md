# a vuex 形式指南

> 原文：<https://blog.logrocket.com/guide-vuex-orm/>

在 Vue 中，我们使用像 Vuex、Redux 和 Pinia 这样的库来管理状态，这些库充当组件状态的中央存储。然而，这些库是单树状态管理系统，难以用于关系数据结构。令人欣慰的是，有了 [Vuex ORM](https://vuex-orm.org) ，开发人员可以将状态作为数据库来处理，同时仍然保持规范化的形式。

例如，在一个组件中，我们可以有一个名为`user`的状态，其中包含用户的全名、年龄、性别和电子邮件地址。因为这些数据结构良好，所以我们可以轻松地执行基本的 CRUD、创建、读取、更新和删除操作。

在本教程中，我们将探索 Vuex ORM 的特性，演示如何使用它们在我们的 Vue 应用程序状态上执行基本的 CRUD 操作。我们还将学习如何使用 Vuex ORM 生命周期挂钩。我们开始吧！

*向前跳转:*

## 什么是 Vuex 形式？

Vuex ORM 是一个用于 Vue 应用程序的 Vuex 状态管理模式和库。Vuex ORM 支持 Vue 应用程序中的对象关系映射(ORM ),允许开发人员更有效地处理模型和管理它们之间的关系。

ORM 提供了一种简单的方法，将 Vue 应用程序的数据作为对象而不是原始数据进行交互。因此，您可以编写更易于理解和维护的代码。

## 先决条件

要跟随本教程，您需要以下内容:

1.  [节点](https://nodejs.org/en/) [。js](https://nodejs.org/en/) 安装在您的机器上
2.  对如何创建 Vue 应用的基本了解
3.  Vuex 的基础知识

## Vuex ORM 怎么用？

应用程序的性能和维护非常重要，尤其是在处理大型项目时。让我们探索一下使用 Vuex ORM 相对于普通状态存储的一些好处。

Vuex ORM 使得使用 Vuex 存储中的数据变得更加容易。Vuex ORM 提供了一个 ORM 层，允许开发人员定义数据模型并将应用程序的数据作为对象使用，从而减少了对样板文件和重复代码的需求。

Vuex ORM 通过引入数据查询特性来过滤、更新和删除数据库中的数据，使数据操作变得更加容易。所以 Vuex ORM 适合大数据应用。

Vuex ORM 是管理应用程序状态的有用工具。它通过提供一种高效且有组织的方式来处理 Vuex 商店数据，有助于提高应用程序的性能和可伸缩性。

## 创建 Vue 项目

在开始之前，让我们用以下命令创建一个简单的 Vue 项目:

```
npm create [email protected] vue-app --template vue
cd vue-app
npm install

```

## 安装 Vuex 形状

使用以下命令在您的 Vue 应用程序中安装 Vuex ORM:

```
# using npm
npm install vue vuex @vuex-orm/core --save

```

## 创建一个简单的 Vuex ORM 模型

我们可以使用 Vuex ORM 的模型来定义我们的数据库实体的字段和关系。为了更好地理解 Vuex ORM 的模型，我们将创建一个简单的图书模型。

从应用程序根目录，导航到`src`文件夹并创建一个`store/models`文件夹。在`models`文件夹中，创建一个`book.js`文件并添加以下代码:

```
// src/store/models/book.js
import { Model } from "@vuex-orm/core";

export default class Book extends Model {
 static entity = "books";

 static fields() {
   return {
    bookTitle: this.string(""),
     bookCover: this.string("https://github.com/popoolatopzy/upload/raw/main/cover1.jpeg"),
     bookPrice: this.string("1000"),
   };
 }
}

```

## 数据库注册

要访问模型，我们必须首先创建一个 Vuex ORM 数据库，然后使用 Vuex ORM `install`方法将其注册到 Vuex。从项目的根目录中，打开`main.js`,并用以下代码替换它:

```
import { createApp } from "vue";
import Vuex from "vuex";
import App from "./App.vue";
import VuexORM from "@vuex-orm/core";
import Book from "./store/models/Book";

const database = new VuexORM.Database();
database.register(Book);

const store = new Vuex.Store({
 plugins: [VuexORM.install(database)],
});

createApp(App).use(store).mount("#app");

```

## 创建 Vue 组件

让我们创建一个名为`favourite.vue`的组件，使用户能够输入他们最喜欢的书的详细信息，并将它们存储在 Vuex ORM 数据库中。在`src/components`文件夹中，创建`favourite.vue`文件并添加以下代码:

```
<script>
import Book from "../store/models/book";

export default {
 data() {
   return {
     form: {
       title: "",
       url: "",
       price: "",
     },
   };
 },
 methods: {

 },
};
</script>
<template>
 <div>
   <link
     href="https://cdn.jsdelivr.net/npm/[email protected]/dist/css/bootstrap.min.css"
     rel="stylesheet"
   />
   <center>
     <div
       style="width: 70%; height: 60px  margin-bottom: 20px;  margin-top: 100px;"
     >
       <label for="">Book Title</label>
       <input v-model="form.title" />
       <label for="">Book Cover URL</label>
       <input v-model="form.url" />
       <label for="">Book Price</label>
       <input v-model="form.price" /><br /><br />
       <button @click="addItem" class="btn-primary" style="border-radius: 12%">
         Add favorite book
       </button>
     </div>
   </center>
 </div>
</template>

```

将上述组件添加到`app.vue`并运行应用程序。我们应该会看到页面上显示的组件，如下所示:

![Book Component Added App Vue](img/5fe484fa7f8a4dd518ca006e985ab131.png)

## CRUD 操作

既然我们已经创建了 book 模型并将其注册为 Vuex ORM 数据库，那么让我们来探索如何在数据库上执行基本的 CRUD 操作。

### 插入数据

我们可以使用 Vuex ORM `insert()`方法向 Vuex 存储添加新记录。我们将把想要添加到 Vuex 存储中的数据作为一个对象和`INSERT`方法一起传递。下面的代码会将用户最喜欢的书添加到数据库中:

```
 // src/components/favourite.vue
// . . .
methods: {
   addItem() {
     const favourite = [
       {
         bookTitle: this.form.title,
         bookCover: this.form.url,
         bookPrice: this.form.price,
       },
     ];
     Book.insert({ data: favourite });
   },
 },
// . . .

```

## 检索数据

从 Vuex 存储中检索数据有多种方法；让我们回顾一下从 Vuex 商店过滤数据的一些方法。

### 检索所有数据

方法从数据库中检索所有记录。我们可以在 Vue 方法中使用它，如下所示:

```
// get all books
const books = Book.all();

```

### 使用 ID 检索数据

我们可以使用 [Vuex ORM `find()`](https://vuex-orm.org/guide/data/retrieving.html#get-single-data) 方法从数据库中获取单个记录的值。我们可以使用下面的代码从数据库中获取 ID 为`3`的记录的值:

```
// get Book with the id of 3
const book = Book.find(3);

```

现在我们知道了如何从 Vuex ORM 数据库中检索数据，我们可以通过向`favourite.vue`脚本添加以下代码来从数据库中获取所有书籍:

```
<script>
import Book from "../store/models/book";

export default {
 computed: {
   books: () => Book.all(),
 },
 data() {
   return {
     form: {
       title: "",
       url: "",
       price: "",
     },
   };
 },

// . . .
};
</script>

```

接下来，通过向`favourite.vue`模板添加以下代码，让我们向用户显示数据库中的所有书籍:

[代码]

刷新应用程序，您应该会看到以下输出:

![Display Books Favorite Vue File](img/24943497ebebc0a0166184bad4a79e85.png)

删除数据

为了从 Vuex ORM 数据库中删除记录，我们将使用 [`delete()`方法](https://vuex-orm.org/guide/data/deleting.html#deleting-models-with-composite-primary-key)。目标记录的主 ID 作为一个扩充传递到方法中。我们可以通过用下面的代码更新`favourite.vue`来添加删除功能:

一旦我们保存了上面的代码并刷新了我们的浏览器，我们应该会得到下面的结果:

![Vue Delete Book Favorite Component Final](img/dcaa7ed6c62b9fa621bbf357347a3d7f.png)

## Vuex 形状生命周期挂钩

我们可以使用 Vuex ORM 生命周期挂钩，在保存、更改或从 Vuex ORM 数据库中检索特定记录时执行自动操作。Vuex ORM 生命周期分为两类，选择生命周期挂钩和突变生命周期挂钩。

```
 // . . .
methods: {
   addItem() {
     const posts = [
       {
         bookTitle: this.form.title,
         bookCover: this.form.url,
         bookPrice: this.form.price,
       },
     ];

     Book.insert({ data: posts });
     // console.log(this.form.title);
   },
   deleteItem(itemID) {
     Book.delete(itemID);
     console.log(itemID);
   },
 },
};
</script>

```

选择生命周期挂钩

[选择生命周期挂钩](https://vuex-orm.org/guide/digging-deeper/lifecycle-hooks.html#mutation-lifecycle-hook)在我们从 Vuex 商店检索数据时被触发。选择挂钩的几个例子包括`beforeSelect`、`afterWhere`、`afterOrderBy`和`afterLimit`。在我们的图书模型中，我们可以使用`beforeSelect`过滤记录，返回价格高于 130:

## 突变生命周期挂钩

当我们改变 Vuex 存储中的数据时，突变生命周期挂钩被触发。突变生命周期挂钩包括`beforeCreate`、`afterCreate`、`beforeUpdate`、`afterUpdate`、`beforeDelete`、`afterDelete`:

### 结论

Vuex ORM 是一个强大的 Vuex 插件,允许开发者轻松管理和操作 Vue 应用中的数据。Vuex ORM 提供了一个简单的界面，用于与作为数据库的 Vuex 商店进行交互，使得在 Vue 应用程序中存储、检索和更新数据变得容易。

```
// src/models/book.js
import { Model } from "@vuex-orm/core";

export default class Book extends Model {
 static entity = "books";

 static fields() {
   return {
     bookTitle: this.string(""),
     bookCover: this.string("https://github.com/popoolatopzy/upload/raw/main/cover1.jpeg"),
     bookPrice: this.string("1000"),
   };
 }
 static beforeSelect(books) {
   return books.filter((book) => book.bookPrice >= "130");
 }
}

```

### 在本教程中，我们学习了如何在 Vue 应用程序中配置和使用 Vuex ORM 数据库。欲了解更多信息，请务必查看官方文件。我希望你喜欢这个教程，如果你有任何问题，一定要留下评论。编码快乐！

像用户一样体验您的 Vue 应用

```
// src/models/book.js
    import { Model } from "@vuex-orm/core";

export default class Book extends Model {
 static entity = "books";

 static fields() {
   // . . .
 }
 static beforeSelect(books) {
   return books.filter((book) => book.bookPrice >= "130");
 }
 static beforeCreate(model) {
   model.published = true;
 }
}

```

## 调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

.

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

## LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

[LogRocket](https://lp.logrocket.com/blg/vue-signup) is like a DVR for web and mobile apps, recording literally everything that happens in your Vue apps including network requests, JavaScript errors, performance problems, and much more. Instead of guessing why problems happen, you can aggregate and report on what state your application was in when an issue occurred.

The LogRocket Vuex plugin logs Vuex mutations to the LogRocket console, giving you context around what led to an error, and what state the application was in when an issue occurred.

Modernize how you debug your Vue apps - [Start monitoring for free](https://lp.logrocket.com/blg/vue-signup).