# 覆盖角度注射器层次结构中的依赖性

> 原文：<https://blog.logrocket.com/overriding-dependencies-angular-injector-hierarchy/>

## 介绍

Angular 框架可以直接确定应用程序中的方向依赖流，从而使调试变得非常容易。

Angular 通过将父组件的注入器提供的依赖项注入到子组件的构造函数中，从而允许在子组件之间共享这些依赖项。

为了更好地理解这一点，让我们考虑一种实用的父-子依赖注入方法。

要学习本教程，您应该具备:

*   Node.js v10.x
*   角度的知识
*   打字知识

## 入门指南

运行以下命令创建新的角度应用程序:

```
ng new phone-book

```

CD 放入电话簿目录，在终端中运行下面的命令，为我们的应用程序创建一个合适的结构模块:

```
ng generate module contacts

```

创建`app/contacts/contact.model.ts`:

```
//app/contacts/contact.model.ts
export interface Contact {
  id: number;
  name: string;
  phone_no: string;
}

```

上面的代码片段使用了 TypeScript 接口。它创建一个模型来验证将从产品服务返回的数据。

运行以下命令创建联系服务:

```
ng generate service contacts/contact

```

更新`contact.service.ts`:

```
//app/contacts/contact.service.ts
import { Injectable } from '@angular/core';
import { Contact } from './contact.model';
@Injectable({
  providedIn: 'root'
})
export class ContactService {
  constructor() { }
  getContacts(): Contact[] {
    return [
      { id: 1, name: 'Peter', phone_no: '09033940948' },
      { id: 2, name: 'Sam', phone_no: '07033945948'},
      { id: 3, name: 'Bryce', phone_no: '08033740948' },
      { id: 4, name: 'Lee', phone_no: '090339409321' },
      { id: 5, name: 'Albert', phone_no: '09066894948'  }
    ];
  }
}

```

属性为服务创建一个提供者。在这种情况下，`providedIn: 'root'`指定 Angular 应该在根注入器中提供服务(即，使其在整个应用程序中可用)。

现在，`ContactService`可以在我们应用程序的任何地方注入。

更新`contact-list.component.ts`:

```
//app/contacts/contact-list.component.ts
import { Component, OnInit } from '@angular/core';
import { Contact } from '../contact.model';
import { ContactService } from '../contact.service';
@Component({
  selector: 'app-contact-list',
  templateUrl: './contact-list.component.html',
  styleUrls: ['./contact-list.component.css'],
  providers: [ContactService]
})
export class ContactListComponent implements OnInit {
  contacts: Contact[];
  constructor(private contactService: ContactService) { }
  ngOnInit(): void {
    this.contacts = this.contactService.getContacts();
  }
}

```

上面的代码片段在组件的构造函数中使用 new 关键字实例化了`ContactService`私有属性，在`ngOnInit`方法中调用了`contactService`的`getContacts`方法，并将返回值赋给了`contacts`属性。

更新`contact-list.component.html`:

```
//app/contacts/contact-list.component.html
<h3>My Contact List</h3>
<ul>
  <li *ngFor="let contact of contacts">
    {{contact.name}}
  </li>
</ul>
<app-recent-contact></app-recent-contact>

```

上面的代码片段使用了`ngFor`指令来显示联系人列表，并接受`RecentContact`作为它的直接子组件。

更新`app/contacts/recent-contact.component.ts`:

```
//app/contacts/recent-contact.component.ts
import { Component, OnInit } from '@angular/core';
import { ContactService } from '../contact.service';
import { Contact } from '../contact.model';
@Component({
  selector: 'app-recent-contact',
  templateUrl: './recent-contact.component.html',
  styleUrls: ['./recent-contact.component.css']
})
export class RecentContactComponent implements OnInit {
  contacts: Contact[];
  constructor(private contactService: ContactService) { }
  ngOnInit(): void {
    this.contacts = this.contactService.getContacts();
  }
}

```

作为`ContactListComponent`的直接子组件，`RecentContactComponent`可以访问`ContactListComponent`提供的依赖项，即使没有通过`@component`装饰器的`providers`属性提供它。

更新`app/contacts/recent-contact.component.html`:

```
//app/contacts/recent-contact.component.html
<h3>My Recent Contacts</h3>
<ul>
  <li *ngFor="let contact of contacts | slice:0:3">
    {{contact.name}}
  </li>
</ul>

```

这里，我们将`SlicePipe`应用于`ngFor`语句，只显示最后三个联系人。

使用`ng serve`命令运行应用程序，我们应该在浏览器中呈现所有联系人和最近联系人的列表。

## 覆盖角度中的从属关系

在 Angular 中覆盖从属关系需要两个关键属性:

*   `provide` —指向您希望覆盖的依赖关系
*   `useClass` —这指向新的依赖关系，它将覆盖`providers`属性中的现有依赖关系

它遵循一种简单的格式:

```
@Component({
 providers: [
  { provide: Service, useClass : FakeService }
 ]
})

```

### 何时覆盖依赖关系

有些情况下，您希望覆盖构造的默认分辨率。

以下是一些典型案例:

*   编写单元测试时重写提供程序
*   向依赖项添加独特的功能，而不对其进行更改

后者是通过使用`extend`关键字在现有的依赖项之外创建一个新的依赖项来实现的。

让我们考虑后一种情况的实际方法，通过创建一个名为`RecentContactService`的新服务来扩展`ContactService`，并使用`slice`数组方法而不是使用模板中的管道来过滤数据。

更新`recent-contact.service.ts`:

```
//app/contacts/recent-contact.service.ts
import { Injectable } from '@angular/core';
import { ContactService } from './contact.service';
import { Contact } from './contact.model';
@Injectable({
  providedIn: 'root'
})
export class RecentContactService extends ContactService {
  constructor() {
    super();
  }
  getContacts(): Contact[] {
    return super.getContacts().slice(Math.max(super.getContacts().length - 3, 0))
  }
}

```

这里，`getContacts`方法从来自`ContactService`的结果数组中返回最后三项。

现在，我们可以将这个新服务(依赖项)添加到`RecentContactComponent`的`providers`属性中

更新`recent-contact.component.ts`:

```
//app/contact/recent-contact.component.ts
...
import { RecentContactService } from '../recent-contact.service';
@Component({
  selector: 'app-recent-contact',
  templateUrl: './recent-contact.component.html',
  styleUrls: ['./recent-contact.component.css'],
  providers: [{
    provide: ContactService,
    useClass: RecentContactService
  }]
})
export class RecentContactComponent implements OnInit {
  contacts: Contact[];
  constructor(private recentContactService: RecentContactService) {}
  ngOnInit(): void {
    this.contacts = this.recentContactService.getContacts();
  }
}

```

这里，`useClass`属性允许 Angular 用我们的新依赖项(`RecentContactService`)覆盖我们以前的依赖项(`ContactService`)。

### 用字符串值覆盖依赖关系

在我们想要覆盖的依赖项是一个字符串的情况下，`useValue`语法会很方便。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

假设我们的应用程序有一个`DBconfig`文件:

```
//app/db.config.ts
export interface DBConfig {
  name: string;
  version: number;
}
export const databaseSettings: DBConfig = {
  name: 'MongoDB',
  version: 2.0
};

```

为了使数据库配置在我们的应用程序中可访问，我们需要提供一个`InjectionToken`对象:

```
//app/db.config.ts
import { InjectionToken } from '@angular/core';
export const DB_CONFIG = new InjectionToken<DBConfig>('db.config');
...

```

现在，我们的组件可以使用`@Inject`装饰器访问数据库配置:

```
//app/app.component.ts
import { Component, Inject } from '@angular/core';
import { DB_CONFIG, databaseSettings, DBConfig } from './db.config';
@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css'],
  providers: [{
    provide: DB_CONFIG,
    useValue: databaseSettings
  }]
})
export class AppComponent {
  name: string;
  version: number;
  constructor(@Inject(DB_CONFIG) config: DBConfig) {
    this.name = config.name;
    this.version = config.version;
  }
}

//app/app.component.html
<h4>This app uses {{name}} Database v. {{version}}</h4>
<app-contact-list></app-contact-list>

```

### 运行时覆盖依赖关系

关键字`useFactory`允许 Angular 决定在运行时将什么依赖注入到一个构造中:

```
import { RecentContactService } from './recent-contact.service';
import { ContactService } from './contact.service';
export function contactFactory(isRecent: boolean) {
  return () => {
    if (isRecent) {
      return new RecentContactService();
    }
    return new ContactService();
  };
}

```

上面的代码片段是一个工厂，它根据布尔条件返回`RecentContactService`或`ContactService`。

现在，我们可以修改`RecentContactComponent`的`providers`属性如下:

```
...
 providers: [{
    provide: ContactService,
    useClass: contactFactory(true)
  }]
...

```

有了它，我们可以尽可能多地覆盖任何依赖。这甚至更容易，因为我们只需将新的依赖项添加到`contactFactory`中，调整条件，最后调整新依赖项的相应组件的`useClass`属性。

#### 运行时覆盖依赖关系的限制

如果这些依赖关系中的任何一个将其他依赖关系注入到它们的带有先前的`contactFactory`实现的构造函数中，Angular 将抛出一个错误。

假设`RecentContactService`和`ContactService`都依赖于`AuthService`的依赖性，我们将不得不对`contactFactory`进行如下调整:

```
export function contactFactory(isRecent: boolean) {
  return (auth: AuthService) => {
    if (isRecent) {
      return new RecentContactService();
    }
    return new ContactService();
  };
}

```

然后，我们必须将`AuthService`依赖项添加到`RecentContactComponent`中`providers`
对象的`deps`属性中，如下所示:

```
...
 providers: [{
    ...
    deps: [AuthService]
  }]
...

```

## 结论

开发大规模的角度应用程序需要很好地理解[依赖注入是如何工作的](https://blog.logrocket.com/how-dependency-injection-works-in-angular/)。在处理大规模 Angular 应用程序时，了解如何以及何时覆盖 Angular 中的依赖关系是非常重要的，因为这有助于避免应用程序中不必要的代码重复。

本文的源代码可以在 [GitHub](https://github.com/emmanuelhashy/angular-dependency-injection) 上找到。

## 像用户一样体验 Angular 应用程序

调试 Angular 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪生产中所有用户的角度状态和动作感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/angular-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/2794ac39244976f37c4941d9a910be23.png)](https://lp.logrocket.com/blg/angular-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/angular-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你网站上发生的一切，包括网络请求、JavaScript 错误等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket NgRx 插件将角度状态和动作记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化调试 Angular 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/angular-signup)。