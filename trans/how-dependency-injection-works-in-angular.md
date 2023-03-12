# 依赖注入在 Angular - LogRocket 博客中是如何工作的

> 原文：<https://blog.logrocket.com/how-dependency-injection-works-in-angular/>

在软件开发中，有几种处理依赖注入的模式。Angular 强制执行构造函数注入模式，该模式使用构造函数将类的依赖项作为构造函数的参数传入。

Angular 有自己内置的依赖注入(DI)框架，在实例化时提供对类的依赖。这是在 Angular 中构建可伸缩 web 应用程序的一个重要特性。

在本教程中，我们将通过一些实际例子向您展示 Angular 中的依赖注入是如何工作的。我们还将回顾一些最佳实践，并回顾在 Angular 应用程序中处理依赖注入的几种不同方法。

要跟进，您应该具备以下条件:

*   Node.js V10.x
*   有角度测量的工作经验
*   先前的打字工作知识

## Angular 中的依赖注入是什么？

根据 [Angular 的官方文档](https://angular.io/guide/dependency-injection)，依赖注入是“一种设计模式，其中一个类从外部源请求依赖，而不是创建它们。”

简而言之，角度依赖注入旨在将服务的实现从组件中分离出来。这简化了服务的测试、覆盖和更改，而不会影响依赖这些服务的组件。

大多数时候，您会遇到一些 angular 教程或代码库以这种方式处理依赖注入:

```
ng generate service products/product
```

上面的命令创建了一个新的 Angular 服务以及其他附带的文件。

```
//app/products/product.model.ts
export interface Product {
  id: number;
  name: string;
}

```

上面的代码片段使用 TypeScript 接口创建一个模型来验证从产品服务返回的数据。

```
//app/products/product.service.ts
import { Injectable } from '@angular/core';
import { Product } from './product.model';
@Injectable({
  providedIn: 'root'
})
export class ProductService {
  constructor() { }
  getProducts(): Product[] {
    return [
      { id: 1, name: 'Nike' },
      { id: 2, name: 'Balenciaga' },
      { id: 3, name: 'Gucci' },
      { id: 4, name: 'Addidas' },
    ];
  }
}

```

属性为服务创建一个提供者。在这种情况下，`providedIn: 'root'`指定 Angular 应该在根注入器中提供服务(即，使其在整个应用程序中可用)。

现在，`ProductService`可以在我们应用程序的任何地方注入:

```
//app/products/product-list/product-list.component.ts
import { Component, OnInit } from '@angular/core';
import { Product } from '../product.model';
import { ProductService } from '../product.service';
@Component({
  selector: 'app-product-list',
  templateUrl: './product-list.component.html',
  styleUrls: ['./product-list.component.css'],
  providers: [ProductService]
})
export class ProductListComponent implements OnInit {
  products: Product[];
  private productService: ProductService;
  constructor() { 
    this.productService = new ProductService();
  }
  ngOnInit(): void {
    this.products = this.productService.getProducts();
  }
}

```

上面的代码片段在组件的构造函数中使用 new 关键字实例化了`productService`私有属性，在`ngOnInit`方法中调用了`productService`的`getProducts`方法，并将返回值赋给了`products`属性。

```
//app/products/product-list/product-list.component.html
<h3>Our products</h3>
<ul>
  <li *ngFor="let product of products">
    <p>(S/N: {{product.id}}) {{product.name}} </p>
  </li>
</ul>

```

上面的代码片段使用了`ngFor`指令来显示产品列表。

如果您使用`ng serve`命令运行应用程序，一切都应该运行良好。

尽管我们通过角度服务成功地将组件从产品逻辑中分离出来，这也是 DI 的主要目标，但是这种方法仍然有两个主要的缺点。第一个是每次呈现`ProductListComponent`时都会创建一个新服务。在需要[单例服务](https://blog.logrocket.com/youre-wrong-about-singletons/)的情况下，这可能会对应用程序的性能产生负面影响。

其次，如果我们改变`ProductService`的构造函数来适应另一个依赖，我们也需要改变`ProductListComponent`构造函数的实现。这意味着组件仍然与服务的实现紧密耦合，这使得测试服务非常困难。

在 Angular 中处理依赖注入的最佳实践如下。

更新`product-list.component.ts`如下图所示:

```
//app/products/product-list/product-list.component.ts
...
export class ProductListComponent implements OnInit {
  products: Product[];
  constructor(private productService: ProductService) { }
  ngOnInit(): void {
    this.products = this.productService.getProducts();
}

```

这样，组件不需要知道如何实例化服务。相反，它接收依赖项并通过其构造函数注入它。这种方法使得测试服务更加容易。

## 如何在 Angular 中处理依赖注入

在 Angular 应用程序中处理依赖注入时，您可以采用基于应用程序或基于组件的方法。让我们放大差异。

### 基于应用的依赖注入

[Angular DI 框架](https://angular.io/guide/dependency-injection)通过提供一个注入器来保存应用程序需要的所有依赖项的列表，使得依赖项在整个应用程序中都可用。当一个组件或服务想要使用一个依赖项时，注入器首先检查它是否已经创建了该依赖项的一个实例。如果没有，它会创建一个新的依赖项，将其返回给组件，并保留一个副本以备将来使用，以便下次请求相同的依赖项时，它会返回保留的依赖项，而不是创建新的依赖项。

在角度应用程序中，存在与注射器相关联的层次。每当 Angular 组件在其构造函数中定义一个标记时，注入器都会在注册提供程序池中搜索与该标记匹配的类型。如果没有找到匹配，它将通过组件注入器树向上搜索父组件的提供者。如果它找到了依赖项，它就停止并向请求它的组件返回它的一个实例。

如果提供者查找结束时没有匹配，它将返回到请求提供者的组件的注入器，并沿模块注入器层次结构向上搜索所有父模块的注入器，直到到达根注入器。如果没有找到匹配，Angular 抛出一个异常。否则，它返回组件依赖关系的实例。

我们已经浏览了这种方法的一些实用代码片段。如果你想复习，请随意参考前面的部分。

### 基于组件的依赖注入

众所周知，这种方法使用`@Component` decorator 的`providers`属性将依赖项直接注入到组件树中，从而向组件注入器注册服务。这种方法通常用于角度应用。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

当在子组件之间共享依赖关系时，依赖关系在提供依赖关系的组件的所有子组件之间共享。它们很容易被注入到子组件的构造函数中，使得每个子组件重用父组件中服务的同一个实例。

假设我们想要显示最近添加的产品列表。显然，显示最近添加的产品列表的模型与显示所有产品的模型是相同的。因此，我们可以在`ProductListComponent`和`RecentProductComponent`组件之间共享`products`依赖(服务)。

使用以下命令在`products`模块中创建一个名为`recent-products`的新组件:

```
ng generate component products/recentProducts --module=products

```

更新`recent-products.component.ts`如下:

```
import { Component, OnInit } from '@angular/core';
import { ProductService } from '../product.service';
import { Product } from '../product.model';
@Component({
  selector: 'app-recent-products',
  templateUrl: './recent-products.component.html',
  styleUrls: ['./recent-products.component.css']
})
export class RecentProductsComponent implements OnInit {
  products: Product[];
  constructor(private productService: ProductService) { }
  ngOnInit(): void {
    this.products = this.productService.getHeroes();
  }
}

```

这里，我们将`ProductService`注入到`RecentProductsComponent`的构造函数中，而不是像我们对`ProductListComponent`所做的那样，通过`@component`装饰器的`providers`属性来提供它。

我们如何解释`@component`装饰器丢失的`providers`属性？如果没有这个，`RecentProductsComponent`将不知道如何创建`ProductService`的实例。

如下更新`ProductListComponent`模板:

```
//app/products/product-list/product-list.component.html
<h3>Our products</h3>
<ul>
  <li *ngFor="let product of products">
    <p>(S/N: {{product.id}}) {{product.name}} </p>
  </li>
</ul>
<app-recent-products></app-recent-products>

```

我们将通过使`RecentProductComponent`成为`ProductListComponent`的直接子节点来回答前面的问题，让`RecentProductComponent`访问由`ProductListComponent`提供的所有依赖项。

更新`recent-products.component.html`如下:

```
//app/products/recent-products/recent-products.component.html
<h3>Recent Products</h3>
<ul>
  <li *ngFor="let product of products | slice:0:5">
    {{product.name}}
  </li>
</ul>

```

这里，我们将切片管道应用于`ngFor`语句，只显示前五个产品。

当您使用`ng serve`命令运行应用程序时，您应该看到浏览器中呈现的所有产品和最近产品的列表。

## 结论

在本教程中，我们建立了对角度依赖注入的基本理解。我们通过几个实际的例子演示了依赖关系是如何在子组件以及整个应用程序之间共享的。我们还回顾了在下一个 Angular 应用程序中实现依赖注入的一些最佳实践。

## 像用户一样体验 Angular 应用程序

调试 Angular 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪生产中所有用户的角度状态和动作感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/angular-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/2794ac39244976f37c4941d9a910be23.png)](https://lp.logrocket.com/blg/angular-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/angular-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你网站上发生的一切，包括网络请求、JavaScript 错误等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket NgRx 插件将角度状态和动作记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化调试 Angular 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/angular-signup)。