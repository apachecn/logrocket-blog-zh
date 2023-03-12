# 使用 FormBuilder 进行角度反应式表单验证

> 原文：<https://blog.logrocket.com/angular-formbuilder-reactive-form-validation/>

***编者按:**本帖更新于 2020 年 12 月 23 日。*

Angular 中的反应式表单使你不用使用太多的指令就能构建干净的表单。这一点至关重要，因为:

*   JavaScript 框架通常反对使用集群模板
*   表单逻辑现在位于组件类中

本质上，[角度反应形式](https://angular.io/guide/reactive-forms)给了开发者更多的控制，因为每一个与输入和控制相关的决定都必须是有意的和明确的。

为了确保数据的质量，最好的做法是[验证反应式表单输入](https://angular.io/guide/form-validation)的准确性和完整性。在本教程中，我们将向您展示如何使用`FormBuilder`在 Angular 中验证反应式表单。

我们将详细介绍以下内容:

为了跟进，请确保您安装了最新版本的 [Node.js (15.5.0)](https://nodejs.org/en/) 和 [Angular (11.0.5)](https://github.com/angular/angular) ，以及 [Angular CLI (11.0.5)](https://github.com/angular/angular-cli/releases) 。你可以在 [GitHub](https://github.com/viclotana/ng-group) 上下载启动项目。

要快速查看角度形式验证，请查看下面的视频教程:

[https://www.youtube.com/embed/d8XONHXTv_4](https://www.youtube.com/embed/d8XONHXTv_4)

视频

## 角度中的表单控件和表单组

表单控件是可以保存任何表单元素的数据值和验证信息的类，这意味着您在反应式表单中的每个表单输入都应该由表单控件绑定。这些是组成反应形式的基本单位。

是 Angular 中的一个类，用于跟踪单个表单控件的值和验证状态。Angular 表单中的三个基本构件之一——与`FormGroup`和`FormArray` — `FormControl`一起——扩展了`AbstractControl`类，使其能够访问值、验证状态、用户交互和事件。

表单组是基本上包装表单控件集合的结构。正如控件使您可以访问元素的状态一样，组也提供了相同的访问权限，但访问的是包装控件的状态。初始化时，表单组中的每个表单控件都由名称标识。

`FormGroup`与`FormControl`一起使用，用于跟踪值并验证表单控制的状态。实际上，`FormGroup`将每个子控件`FormControl`的值聚合到一个对象中，使用每个控件名作为键。它通过减少其子级的状态值来计算其状态，这样，如果一个组中的一个控件无效，则整个组都无效。

为了更深入的了解，请查看我们关于[表单组和 Angular](https://blog.logrocket.com/reactive-form-controls-form-groups-angular/) 表单控件的综合教程。

## Angular 中的表单验证是什么？

Angular 中的表单验证使您能够验证输入是否准确和完整。您可以从 UI 验证用户输入，并在模板驱动和反应式表单中显示有用的验证消息。

在 Angular 中验证反应式表单时，验证器函数被直接添加到组件类中的表单控件模型中。每当控件的值改变时，Angular 调用这些函数。

验证器函数可以是同步的，也可以是异步的:

*   同步验证器接受一个控件实例，并返回一组错误或 null。当您创建一个`FormControl`时，您可以将 sync 函数作为第二个参数传入。
*   异步验证器接受一个控件实例，并返回一个承诺或一个可观察对象，随后发出一组错误或 null。当实例化一个`FormControl`时，可以将异步函数作为第三个参数传入。

根据您项目的独特需求和目标，您可以选择[编写定制的验证器函数](https://angular.io/guide/form-validation#validating-input-in-reactive-forms)或者使用 Angular 的任何[内置验证器](https://angular.io/api/forms/Validators)。

事不宜迟，让我们开始构建反应式表单。

## 什么是`FormBuilder`？

设置表单控件，尤其是对于非常长的表单，很快就会变得既单调又紧张。Angular 帮助您简化构建复杂表单的过程，同时避免重复。

简而言之，`FormBuilder`提供了语法上的好处，减轻了创建`FormControl`、`FormGroup`或`FormArray`实例的负担，并减少了构建复杂表单所需的样板文件的数量。

## 如何使用`FormBuilder`

以下示例显示了如何使用`FormBuilder`在 Angular 中构建反应式表单。

你应该已经下载并打开了 VS 代码中的[启动项目](https://github.com/viclotana/ng-group)。如果你打开`employee.component.ts`，文件应该是这样的:

```
import { Component, OnInit } from '@angular/core';
import { FormControl, FormGroup } from '@angular/forms'
@Component({
  selector: 'app-employee',
  templateUrl: './employee.component.html',
  styleUrls: ['./employee.component.css']
})
export class EmployeeComponent implements OnInit {
  bioSection = new FormGroup({
    firstName: new FormControl(''),
    lastName: new FormControl(''),
    age: new FormControl(''),
    stackDetails: new FormGroup({
      stack: new FormControl(''),
      experience: new FormControl('')
    }),
    address: new FormGroup({
        country: new FormControl(''),
        city: new FormControl('')
    })
  });
constructor() { }
ngOnInit() {
  }
  callingFunction() {
    console.log(this.bioSection.value);
   }
}
```

您可以看到每一个表单控件——甚至是划分它的表单组——都是拼写出来的，所以随着时间的推移，您最终会重复自己的操作。`FormBuilder`有助于解决这个效率问题。

### 注册`FormBuilder`

要使用`FormBuilder`，必须先注册。要在组件中注册`FormBuilder`，从角度表单中导入它:

```
import { FormBuilder } from ‘@angular/forms’;
```

下一步是注入 form builder 服务，这是一个可注入的提供者，它与反应式表单模块一起提供。注入表单生成器后，您就可以使用它了。导航到`employee.component.ts`文件并复制到下面的代码块中。

```
import { Component, OnInit } from '@angular/core';
import { FormBuilder } from '@angular/forms'
@Component({
  selector: 'app-employee',
  templateUrl: './employee.component.html',
  styleUrls: ['./employee.component.css']
})
export class EmployeeComponent implements OnInit {
  bioSection = this.fb.group({
    firstName: [''],
    lastName: [''],
    age: [''],
    stackDetails: this.fb.group({
      stack: [''],
      experience: ['']
    }),
    address: this.fb.group({
        country: [''],
        city: ['']
    })
  });
constructor(private fb: FormBuilder) { }
ngOnInit() {
  }
  callingFunction() {
    console.log(this.bioSection.value);
   }
}
```

这与您在开始时看到的前一个代码块做了完全相同的事情，但是您可以看到代码更少，结构更多——因此，资源得到了最佳利用。表单生成器不仅有助于提高反应式表单的代码效率，而且对表单验证也很重要。

## 如何在 Angular 中验证表单

使用 Angular 中的反应式表单，您可以在表单生成器中验证表单。

使用以下命令运行开发中的应用程序:

```
ng serve
```

您会发现，即使您没有在文本框中输入值，表单也会提交。这可以很容易地用反应式表单中的表单验证器来检查。第一件要做的事，就像反应形式的所有元素一样，是从角度形式中导入它。

```
import { Validators } from '@angular/forms';
```

现在，您可以通过指定表单控件来试验验证器，为了激活提交按钮，必须填写表单控件。将下面的代码块复制到`employee.component.ts`文件中:

```
import { Component, OnInit } from '@angular/core';
import { FormBuilder } from '@angular/forms';
import { Validators } from '@angular/forms';
@Component({
  selector: 'app-employee',
  templateUrl: './employee.component.html',
  styleUrls: ['./employee.component.css']
})
export class EmployeeComponent implements OnInit {
  bioSection = this.fb.group({
    firstName: ['', Validators.required],
    lastName: [''],
    age: [''],
    stackDetails: this.fb.group({
      stack: [''],
      experience: ['']
    }),
    address: this.fb.group({
        country: [''],
        city: ['']
    })
  });
constructor(private fb: FormBuilder) { }
ngOnInit() {
  }
  callingFunction() {
    console.log(this.bioSection.value);
   }
}

```

最后要做的是确保提交按钮的活动设置得到相应的设置。导航到`employee.component.html`文件并确保提交语句如下所示:

```
<button type=”submit” [disabled]=”!bioSection.valid”>Submit Application</button>
```

如果您现在运行您的应用程序，您会看到如果您不为名字设置输入，您就不能提交表单——这不是很酷吗？

### 显示输入值和状态

假设您想要使用 value 和 status 属性来实时显示您的反应式表单的输入值，以及它是否可以被提交。

反应式表单 API 允许您在模板部分的表单组或表单控件上使用值和状态属性。打开您的`employee.component.html`文件，复制下面的代码块:

```
<form [formGroup]="bioSection" (ngSubmit)="callingFunction()">
    <h3>Bio Details
</h3>

  <label>
    First Name:
    <input type="text" formControlName="firstName">
  </label> <br>
<label>
    Last Name:
    <input type="text" formControlName="lastName">
  </label> <br>
<label>
    Age:
    <input type="text" formControlName="age">
  </label>
<div formGroupName="stackDetails">
    <h3>Stack Details</h3>

    <label>
      Stack:
      <input type="text" formControlName="stack">
    </label> <br>

    <label>
      Experience:
      <input type="text" formControlName="experience">
    </label>
  </div>
<div formGroupName="address">
    <h3>Address</h3>

    <label>
      Country:
      <input type="text" formControlName="country">
    </label> <br>

    <label>
      City:
      <input type="text" formControlName="city">
    </label>
  </div>
<button type="submit" [disabled]="!bioSection.valid">Submit Application</button>
  <p>
    Real-time data: {{ bioSection.value | json }}
  </p>
  <p>
    Your form status is : {{ bioSection.status }}
  </p>
</form>
```

这将在您使用表单时，在界面中为您显示提交的值和状态。本教程的完整代码可以在 GitHub 上找到。

## 不要只是验证你的表单，用 [LogRocket](https://www2.logrocket.com/angular-performance-monitoring) 验证你的 Angular 应用的用户体验

调试 Angular 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪生产中所有用户的角度状态和动作感兴趣，[请尝试 LogRocket](https://www2.logrocket.com/angular-performance-monitoring) 。

[![LogRocket Dashboard Free Trial Banner](img/2794ac39244976f37c4941d9a910be23.png)](https://www2.logrocket.com/angular-performance-monitoring)

LogRocket 就像是网络应用程序的 DVR，记录你网站上发生的一切，包括网络请求、JavaScript 错误等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket NgRx 插件将角度状态和动作记录到 LogRocket 控制台，为您提供导致错误的原因以及出现问题时应用程序所处的状态。

现代化调试 Angular 应用的方式— [开始免费监控](https://www2.logrocket.com/angular-performance-monitoring)。

## 结论

本文概述了表单生成器，以及它是如何提高表单控件和表单组的效率的。它还展示了用反应式表单轻松处理表单验证是多么重要。黑客快乐！