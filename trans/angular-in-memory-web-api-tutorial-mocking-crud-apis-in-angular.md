# Angular 内存 Web API 教程:在 Angular - LogRocket 博客中模仿 CRUD APIs

> 原文：<https://blog.logrocket.com/angular-in-memory-web-api-tutorial-mocking-crud-apis-in-angular/>

## 介绍

设想这样一种情况，您正在应用程序的前端构建一个新特性，但是可用的 HTTP 端点还没有准备好供后端开发团队使用。在各种类型的开发人员组成的大型团队中工作时，这是一个常见问题。

为了在开发过程中克服这个障碍，我们可以使用一个假服务器来模拟 HTTP REST API 的所有 CRUD 操作，同时我们的数据驻留在本地的前端应用程序中。

在本教程中，我们将探索 Angular 内存 Web API，然后动手开发一个假服务器来模拟 Angular 中的一个简单 CRUD 后端，这样您就可以构建一个功能性的前端，而无需依赖后端团队。

## 先决条件

在我们开始之前，请确保您具备以下条件，以便跟上本教程的进度:

*   Node.js V10.x
*   有角度测量的工作经验
*   先前的打字工作知识

## 介绍 Angular 内存 Web API

`angular-in-memory-web-api`是一个库，它拦截 Angular `Http`和`HttpClient`请求，否则这些请求会发送到远程服务器，并将它们重定向到您在前端控制的内存数据存储。有了这个库，您可以无缝地模仿延迟响应，并从本质上做您可能需要后端开发人员做的一切。但是，它的功能有限，并且不适合在生产中使用。

## 设置角度项目

在本教程中，我们将使用 angular CLI 工具来搭建我们的项目。

要检查是否已经安装了 Angular CLI 工具，请在终端中运行以下命令:

```
ng --version
//or
ng v

```

使用以下命令安装 angular CLI 工具:

```
npm install -g @angular/[email protected] 

```

现在，我们可以使用以下命令创建一个新的角度项目:

```
ng new angular-api-backend

```

Angular CLI 将询问您关于您想要创建的应用程序的一些细节，以便它可以支持 Angular 项目。

最初，它会询问您是否想要添加角度路由。对于本教程，对问题回答“否”,然后按 enter 键。

下一个问题是您想使用哪种样式表格式。接受默认选择(CSS)，然后按 enter 键。

根据您的互联网连接，此过程可能需要一些时间。在此期间，Angular CLI 将下载并安装所有必要的依赖项，并为您的 Angular 应用程序创建默认文件。

完成后，它将创建一个名为`angular-api-backend`的文件夹。导航到新创建的文件夹，使用以下命令启动应用程序:

```
ng serve

```

Angular CLI 编译我们新创建的 Angular 项目，并启动一个 web 服务器来监视项目文件中的更改。

## 设置内存角度网络 api

`angular-in-memory-web-api`非常容易设置和配置。

### 第一步

首先，我们需要安装`angular-in-memory-web-api`作为一个开发依赖项，因为我们将只把它用于开发:

```
npm install angular-in-memory-web-api --save-dev

```

在`src/app`目录中，创建`data.services.ts`文件并添加以下内容:

```
//src/app/data.services.ts

import { Injectable } from '@angular/core';
import { InMemoryDbService } from 'angular-in-memory-web-api';
@Injectable({
  providedIn: 'root'
})
export class DataService implements InMemoryDbService {
  constructor() { }
  createDb() {
    return {
      products: [
        {
          id: 1,
          name: 'Seaman Cap',
          description: 'Lorem ipsum . Voluptatem excepturi magnam nostrum dolore recusandae',
          price: '$40'
        },
        {
          id: 2,
          name: 'T-shirt',
          description: 'amet consectetur adipisicing elit.Lorem ipsum dolor sit ',
          price: '$80'
        },
        {
          id: 3,
          name: 'Back Pack',
          description: 'Voluptatem excepturi harum rerum aliquam magnam nostrum dolore recusandae',
          price: '$30'
        }
      ]
    };
  }
}

```

上面的代码片段是一个实际的 Angular 服务，它实现了`InMemoryDbService`接口。然后，Angular 服务实现了`InMemoryDbService`接口的`createDb`方法，这在内存中创建了一个表示我们数据库的对象。对象中的每个键代表我们数据库的一个实体，比如`products`。

### 第二步

将以下内容添加到`src/app/app.module.ts`:

```
//src/app/app.module.ts

import { HttpClientInMemoryWebApiModule } from 'angular-in-memory-web-api';
import { DataService } from './data.service';

@NgModule({
  ...
  imports: [
    ...
    HttpClientInMemoryWebApiModule.forRoot(DataService),
  ],
  ...
})

```

注意我们如何通过调用它的`forRoot`方法导入`HttpClientInMemoryWebApiModule`，将`DataService`作为参数传递。我们这样做是为了避免创建多个`DataService`实例。

我们已经成功地为 Angular 应用程序创建了一个后端 API，而没有设置实际的后端服务器基础设施。

## Angular HTTP 客户端简介

Angular http 客户端是 Angular 框架的内置 http 客户端。这个服务是一个可注入的类，带有执行 HTTP 请求的方法。默认情况下，它是在创建新的 Angular 项目时由 Angular CLI 安装的。

为了使用它，我们需要将以下内容添加到`src/app/app.module.ts`:

```
// src/app/app.module.ts
import { HttpClientModule } from '@angular/common/http';

@NgModule({
  ...
  imports: [
    ...
    HttpClientModule,
  ]
})

```

`HttpClientModule`服务的实例可以访问各种方法来执行常见的请求操作，例如`GET`、`POST`、`PUT`等。

## 以角度处理积垢操作

构建 CRUD 应用程序是学习软件开发中的新工具和新概念的最佳途径之一。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

为了更好地理解如何在 Angular 中模拟 CRUD APIs，我们将构建一个演示 CRUD 应用程序来创建产品、更新和删除它们。

我们将在模块中组织我们的代码，因为我们将创建如下的`products`模块:

```
ng generate module products

```

接下来，我们将向`products`模块注册一个`product-list`组件:

```
ng generate component products/productList --module=products

```

将以下 CSS 样式添加到`products/product-list/productlist.component.css`:

```
//products/product-list/productlist.component.css
#customers {
  font-family: Arial, Helvetica, sans-serif;
  border-collapse: collapse;
  width: 100%;
}
#customers td, #customers th {
  border: 1px solid #ddd;
  padding: 8px;
}
#customers tr:nth-child(even){background-color: #f2f2f2;}
#customers tr:hover {background-color: #ddd;}
#customers th {
  padding-top: 12px;
  padding-bottom: 12px;
  text-align: left;
  background-color: #4CAF50;
  color: white;
}

```

## 产品界面

导航到`products`目录，并使用以下内容创建`product.model.ts`:

```
//products/product.model.ts
export interface Product {
  id: number;
  name: string;
}

```

这里，我们为将要使用的产品数据的结构创建了一个类型接口。

## 与 API 后端的通信

为了与后端进行通信，我们需要将`HttpClient`注入到我们的`ProductService`中，这样我们就可以访问`GET`、`POST`、`PUT`以及向后端发送 HTTP 请求所需的所有其他 HTTP 动词。

在`products`目录中，创建一个`product.service.ts`文件，并添加以下内容:

```
import { Injectable } from '@angular/core';
import { Product } from './product.model';
import { HttpClient, HttpErrorResponse } from '@angular/common/http';
import { Observable, throwError } from 'rxjs';
import { catchError, retry } from 'rxjs/operators';
@Injectable({
  providedIn: 'root'
})
export class ProductService {
  private productsUrl = 'api/products/';
  constructor(private http: HttpClient) { }

  getProducts(): Observable<Product[]> {
    return this.http.get<Product[]>(this.productsUrl).pipe(
      retry(2),
      catchError((error: HttpErrorResponse) => {
        console.error(error);
        return throwError(error);
      })
    );
  }

  createProduct(product: Product): Observable<Product> {
    product.id = null;
    return this.http.post<Product>(this.productsUrl, product).pipe(
      catchError((error: HttpErrorResponse) => {
        console.error(error);
        return throwError(error);
      })
    )
  }

  editProduct(product: Product): Observable<any> {
    return this.http.put(this.productsUrl + product.id, product);
  }

  deleteProduct(id: number): Observable<any> {
    return this.http.delete(this.productsUrl + id);
  }
}

```

在上面的代码片段中，我们从 Angular 内置 HTTP 包中导入了`HttpClient`,并将其注入到`ProductService`类中，如下所示:

```
constructor(private http: HttpClient) { }

```

`getProducts`方法使用`HttpClient`服务从数据库中获取产品列表，并返回它们的`Observable`。`HttpClient`的`GET`方法接受 API 端点的 URL 作为参数。

如果您熟悉 TypeScript，您会注意到我们在`GET`方法中使用了泛型`<Product[]>`来获得从服务器返回的特定数据类型的响应。在我们的例子中，是一个由`Product`对象组成的数组。

`angular-inmemory-web-api`要求 URL 总是以单词`api`开头，后面跟着我们想要访问的实体:

```
private productsUrl = 'api/products/';

```

在我们的例子中，实体是`products`，如前面在`DataService`的`createDb`方法中定义的。

`createProduct`方法使用`HttpClient` post 动词发出 post 请求，在数据库中创建新产品。它返回新创建产品的一个`Observable`。

`editProduct`方法使用`HttpClient`发出一个`PUT`请求来更新数据库中现有的产品。与`createProduct`一样，它返回更新产品的`Observable`。

最后，`deleteProduct`方法使用`HttpClient`发出删除请求，从数据库中删除现有产品。类似地，它返回被删除产品的`Observable`。

现在，我们已经将`HttpClient`配置到我们的`ProductService`中。下一步是让我们的组件订阅可观察流，因为`ProductService`的方法返回可观察流而不是原始数据。

## 订阅组件中可观察到的数据流

用以下内容更新`product-list.component.ts`:

```
//products/product-list/productlist.component.ts
import { Component, OnInit } from '@angular/core';
import { Product } from "../product.model"
import { ProductService } from "../product.service"

@Component({
  selector: 'app-product-list',
  templateUrl: './product-list.component.html',
  styleUrls: ['./product-list.component.css']
})
export class ProductListComponent implements OnInit {
  product = {
    name: '',
    id: null
  }
  edit = true;
  add = false;
  products: Product[];

  constructor(private productService: ProductService) {}

  ngOnInit(): void {
    this.getProducts()
  }

  private getProducts() {
    this.productService.getProducts().subscribe(products => this.products = products);
  }

  addProduct() {
    const data = {
      name: this.product.name,
      id: this.product.id
    };
    this.productService.createProduct(data).subscribe(response => {
      console.log(response)
      this.getProducts();
    });
  }

  setProductEdit(product: Product) {
    this.product.name = product.name;
    this.product.id = product.id;
    this.edit = false;
    this.add = true;
  }

  resetValues() {
    this.product.name = "";
    this.product.id = null;
    this.edit = true;
    this.add = false;
  }

  removeProduct(product: Product) {
    const id = product.id;
    console.log(product)
    this.productService.deleteProduct(id).subscribe(product => console.log(product));
    this.getProducts()
  }

  updateProduct(){
    this.productService.editProduct(this.product).subscribe(response => console.log(response));
    this.getProducts()
    this.resetValues()
  }
}

```

`getProducts`方法订阅`ProductService`的`getProducts`方法，并将结果设置为组件的`products`属性，而`ngOnInit()`生命周期钩子在组件初始化时调用`getProducts`方法。

类似地，`addProduct`、`removeProduct`和`updateProduct`方法分别订阅了`ProductService`的`createProducts`、`editProduct`和`deleteProduct`方法。

现在，让我们将组件逻辑与模板结合起来。将以下内容添加到`products/product-list/productlist.component.html`:

```
//products/product-list/productlist.component.html
<div>
  <input type="text" name="productName" [(ngModel)]="product.name">
  <button [hidden]="add" (click)="addProduct()">add Product</button>
  <button [hidden]="edit" (click)="updateProduct()">Update Product</button>
</div>
<table id="products">
  <tr>
    <th>S/N</th>
    <th>Name</th>
    <th>Update</th>
    <th>Delete</th>
  </tr>
  <tr *ngFor="let product of products">
    <td>{{product.id}}</td>
    <td>{{product.name}}</td>
    <td><button (click)="setProductEdit(product)">Update</button></td>
    <td><button (click)="removeProduct(product)">Delete</button></td>
  </tr>
</table>

```

`*ngFor`指令用于呈现基于数据源的项目列表。在我们的例子中，我们为产品数组中的每个产品对象呈现一个产品数据。

更新`products.module.ts`如下:

```
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { ProductListComponent } from './product-list/product-list.component';
import { FormsModule, ReactiveFormsModule } from '@angular/forms';

@NgModule({
  declarations: [ProductListComponent],
  imports: [
    CommonModule,
    FormsModule,
    ReactiveFormsModule
  ],
  exports: [ProductListComponent]
})
export class ProductsModule { }

```

现在，让我们在浏览器中呈现我们的`product-list`组件。更新`app.component.html`如下:

```
<app-product-list></app-product-list>

```

另外，更新`app.module.ts`如下:

```
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { HttpClientModule } from '@angular/common/http';
import { AppComponent } from './app.component';
import {ProductsModule} from "./products/products.module"
import { HttpClientInMemoryWebApiModule } from 'angular-in-memory-web-api';
import { DataService } from './data.service';
@NgModule({
  declarations: [
    AppComponent,
  ],
  imports: [
    BrowserModule,
    HttpClientModule,
    ProductsModule,
    HttpClientInMemoryWebApiModule.forRoot(DataService)
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }

```

这是一个漫长的构建，所以让我们在浏览器上看看我们的最终项目。如果您的开发服务器关闭，再次运行`ng serve`命令并在`[http://localhost:4200/](http://localhost:4200/)`上打开您的浏览器。

## 结论

在本教程中，我们已经创建了一个基本的 CRUD 应用程序，它利用`angular-in-memory-web-api`包来模拟一个带有内存数据库的 REST API，以便在 HTTP 端点尚未准备好供后端开发团队使用时使用。

如果你有兴趣，你可以在这里找到我们 CRUD 应用程序的 GitHub repo。

## 像用户一样体验 Angular 应用程序

调试 Angular 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪生产中所有用户的角度状态和动作感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/angular-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/2794ac39244976f37c4941d9a910be23.png)](https://lp.logrocket.com/blg/angular-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/angular-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你网站上发生的一切，包括网络请求、JavaScript 错误等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket NgRx 插件将角度状态和动作记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化调试 Angular 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/angular-signup)。