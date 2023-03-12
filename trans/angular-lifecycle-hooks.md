# 角度生命周期钩子解释

> 原文：<https://blog.logrocket.com/angular-lifecycle-hooks/>

生命周期挂钩是 Angular 中的一个特殊功能，它允许我们在组件或指令的特定生命周期事件中“挂钩”并运行代码。

Angular 在创建、更新或销毁组件时为我们管理组件和指令。有了生命周期挂钩，我们可以更好地控制我们的应用程序。

为此，我们在组件或指令中添加了一些前缀为`ng`的特定钩子方法。这些挂钩分为两种类型:组件或指令挂钩和子组件挂钩。

## 我们将学到什么

本文将涵盖以下内容:

*   角度元件和指令的不同生命周期挂钩
*   如何挂钩到组件生命周期来运行我们的代码
*   什么触发了生命周期挂钩以及它们被调用的顺序

这些是组件或指令的挂钩，按调用顺序排列:

1.  `constructor()`
2.  `OnChanges`
3.  `OnInit`
4.  `DoCheck`
5.  `OnDestroy`

这些是组件的子组件的挂钩:

1.  `AfterContentInit`
2.  `AfterContentChecked`
3.  `AfterViewInit`
4.  `AfterViewChecked`

下表总结了角度生命周期挂钩:

| `OnChanges` | 当输入属性改变时调用 |
| `OnInit` | 初始化时调用 |
| `DoCheck` | 开发人员的自定义变更检测 |
| `OnDestroy` | 在组件被销毁之前调用 |
| `AfterContentInit` | 当组件的内容`ngContent`初始化时调用 |
| `AfterContentChecked` | 当组件内容更新或检查更新时调用 |
| `AfterViewInit` | 当组件的投影视图已初始化时调用 |
| `AfterViewChecked` | 检查投影视图后调用 |

## 组件或指令的挂钩

在 Angular 中，组件是组件树的最小构建单元。这使我们能够非常容易地开发和维护我们的代码库。组件是带有`@Component()`装饰符的类，就像这样:

```
@Component({
  selector: "app-comp",
  template: `<div>AppComponent</div>`,
  styles: ``
})
class AppComponent implements OnInit {
  //...
}
```

指令在技术上是组件，但是它们在 DOM 的非 UI 部分操作。他们的工作是给 DOM 添加额外的功能。指令也是类，但是用`@Directive()`类装饰器来装饰。

内置指令的例子有`*ngFor`、`*ngIf`等。我们可以这样创建自己的指令:

```
@Directive({
  selector: '[highlight-text]'
})
export class HighlightDirective {
  //...
}
```

以下是组件或指令的生命周期挂钩，并解释了它们如何工作以及如何使用。

### `Constructor()`

这是我们在 JS 类中熟悉的构造函数。包含它是因为我们的组件/指令是带有`@Component`或`@Directive`装饰器的 JavaScript 类。因此，当 Angular 使用`new`关键字创建我们的组件/指令实例时，就会调用这个构造函数。

### `OnChanges`

`OnChanges`是一个生命周期挂钩，当指令的任何数据绑定属性发生变化时，就会调用它。必须定义一个`ngOnChanges()`方法来处理这些变化。

假设我们的`BComponent`有输入属性`books`:

```
@Component({
 selector: 'bcomp',
 template: `
 <div *ngFor="let book of books">
 {{book}}
 </div>
 `
})
export class BComponent implements OnChanges {
 @Input() book
 ngOnChanges() {
 console.log("The book property changed")
 }
}
@Component({
 // ...
 template: `
 <bcomp [books]="books"></bcomp>
 `
})
export class App {
 books = ["Michael Crichton: Prey"]
}

```

`App`将其`books`属性绑定到`BComponent`的`books`。注意我们是如何实现了`OnChanges`接口并添加了`ngOnChanges`方法的。当 Angular 从`App`组件接收的`books`属性改变时，Angular 将调用`ngOnChanges`。

如果我们改变`App`组件中的`books`属性，Angular 将不会选择它，并且`BComponent`中的`ngOnChanges`将不会运行:

```
@Component({
 // ...
 template: `
 <bcomp [books]="books"></bcomp>
 `
})
export class App {
 books = ["Michael Crichton: Prey"]
 constructor() {
 setInterval(()=> {
 this.books.push("New Book: " + Date.now())
 }, 1000)
 }
}

```

`push`方法是一个变异方法，使用上面的代码，`BComponent`不会得到任何改变，`ngOnChanges`也不会运行。

由于`BComponent`不是一个`OnPush` CD 策略，它的视图将被更新，但是添加到`books`的新书将不会显示，因为 Angular 在更新它的 DOM 之前更新了一个组件/指令输入绑定。

我们应该意识到不变性，并总是试图返回一个新的状态/引用。我们将使用一种叫做`Array#concat`的非变异方法:

```
@Component({
 // ...
 template: `
 <bcomp [books]="books"></bcomp>
 `
})
export class App {
 books = ["Michael Crichton: Prey"]
 constructor() {
 setInterval(()=> {
 this.books = this.books.concat(["New Book: " + Date.now()])
 }, 1000)
 }
}
```

现在，`BComponent`上的`ngOnChanges`将被运行。

很简单，`ngOnChanges`在组件/指令的输入绑定改变时运行。

### `OnInit`

`OnInit`是一个生命周期钩子，在 Angular 初始化了一个指令的所有数据绑定属性后被调用。定义一个`ngOnInit()`方法来处理任何额外的初始化任务。

当第一次在组件上运行变更检测时，这个钩子被调用。第一次运行后，它会被关闭，永远不会再次运行。在这里添加我们的初始化逻辑是一个最佳实践。

为此，我们将实现`OnInit`接口，并在组件/指令中添加`ngOnInit`方法:

```
@Component({
 //...
})
export class BComponent implements OnInit {
 ngOnInit() {
 console.log("ngOnInit called")
 }
}

```

#### `constructor`和`OnInit`有什么区别？

`Constructor`和`OnInit`看似相同，但在不同的阶段调用。Angular 有两个阶段:引导阶段和变化检测阶段。

构造函数在引导阶段被调用。这个阶段是 Angular 在我们的模块中创建服务、管道、组件和指令的实例的时候。Angular 初始化组件并解析其依赖关系，然后将其传递给构造函数。

当 Angular 最终调用`tick`方法`ApplicationRef#tick`时，变化检测阶段发生在引导阶段之后。

什么是变化检测？这是一种机制，Angular 通过这种机制检测组件/指令的状态变化，并更新 DOM 以反映这种变化。Angular 使用 [Zone.js](https://github.com/angular/zone.js) 检测到这一点，这是一个在浏览器中对以下异步 API 进行猴子式修补的库:

*   `setTimeout`、`clearTimeout`、`setInterval`等。
*   AJAX `XMLHttpRequest`对象

在变更检测阶段，这些钩子被依次调用:

证明:

```
// https://github.com/angular/packages/core/src/view/provider.ts
export function checkAndUpdateDirectiveDynamic(
 view: ViewData, def: NodeDef, values: any[]): boolean {
 const providerData = asProviderData(view, def.nodeIndex);
 const directive = providerData.instance;
 let changed = false;
 let changes: SimpleChanges = undefined !;
 for (let i = 0; i < values.length; i++) {
 if (checkBinding(view, def, i, values[i])) {
 changed = true;
 changes = updateProp(view, providerData, def, i, values[i], changes);
 }
 }
 if (changes) {
 directive.ngOnChanges(changes);
 }
 if ((def.flags & NodeFlags.OnInit) &&
 shouldCallLifecycleInitHook(view, ViewState.InitState_CallingOnInit, def.nodeIndex)) {
 directive.ngOnInit();
 }
 if (def.flags & NodeFlags.DoCheck) {
 directive.ngDoCheck();
 }
 return changed;
}

```

因此，在构建了组件树之后，调用了`OnInit`,并且解析了依赖项并将其传递给组件/指令的实例。在被调用之后，它被关闭。

我们所说的关闭是什么意思？Angular 中的每个组件/指令都有视图状态:

```
export const enum NodeFlags {
 OnInit = 1 << 16,
 OnDestroy = 1 << 17,
 DoCheck = 1 << 18,
 OnChanges = 1 << 19,
 AfterContentInit = 1 << 20,
 AfterContentChecked = 1 << 21,
 AfterViewInit = 1 << 22,
 AfterViewChecked = 1 << 23,
 // ...
}

```

上面是一个视图状态的位掩码。在内部，组件/指令被称为视图。每个组件的视图都是根据它实现的生命周期钩子用视图状态创建的。视图状态可以是上述任意位掩码的组合。注意，每个钩子都有一个位掩码。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

当我们的组件`BComponent`实现了`OnInit`时，它的视图状态将是`OnInit = 1 << 16`。
在组件/指令上调用`OnInit`钩子时，Angular 检查是否是第一次。如果是，它将状态掩码更新为真，并调用`ngOnInit`方法:

```
if ((def.flags & NodeFlags.OnInit) &&
 shouldCallLifecycleInitHook(view, ViewState.InitState_CallingOnInit, def.nodeIndex)) {
 directive.ngOnInit();
 }

```

因此，在下一次 CD 运行时，上述内容将始终为假，并且将跳过`ngOnInit`调用。

### `DoCheck`

`DoCheck`是一个执行变更检测的回调方法，在默认的变更检测器运行后调用。

这个钩子在`OnInit`钩子之后。`DoCheck`不会在类似于`OnInit`和`OnChanges`的事件上运行，当输入属性发生变化或组件/指令初始化时会调用这些事件。相反，添加这个钩子是为了让开发人员可以添加他或她的定制代码来执行定制 CD。

`DoCheck`除了默认变更检测器执行的检查之外，为指令调用自定义变更检测功能:

```
if (def.flags & NodeFlags.DoCheck) {
 directive.ngDoCheck();
 }

```

正如我们在上面看到的，无论组件/指令是一个`OnPush`还是`Default`组件，这个钩子总是在每个 CD 周期运行。你的`ngDoCheck`钩子被调用并不意味着你的组件/指令被检查，只是钩子被调用了:

```
@Component({
 selector: 'bcomp',
 template: `
 <div *ngFor="let book of books">
 {{book}}
 </div>
 `,
 changeDetection: ChangeDetectionStrategy.OnPush
})
export class BComponent implements OnChanges {
 @Input() book
 ngOnChanges() {
 console.log("The book property changed")
 }
}
@Component({
 // ...
 template: `
 <bcomp [books]="books"></bcomp>
 `
})
export class App {
 books = ["Michael Crichton: Prey"]
}

```

`BComponent`是一个`OnPush`组件，这意味着它将首先检查其输入绑定的变化，然后在后续的 CD 运行中，该组件及其子组件将被跳过检查。`OnPush` CD 策略是一种优化技术，它只在必要时或者当组件的输入绑定发生变化时才呈现组件。

如果`BComponent`实现了`DoCheck`并添加了`ngDoCheck`方法，那么即使跳过了在其上运行的 CD，它也会一直运行:

```
@Component({
 selector: 'bcomp',
 template: `
 <div *ngFor="let book of books">
 {{book}}
 </div>
 `,
 changeDetection: ChangeDetectionStrategy.OnPush
})
export class BComponent implements OnChanges, DoCheck {
 @Input() book
 ngOnChanges() {
 console.log("The book property changed")
 }
 ngDoCheck() {
 console.log("ngDoCheck on BComponent called")
 }
}
@Component({
 // ...
 template: `
 <bcomp [books]="books"></bcomp>
 `
})
export class App {
 books = ["Michael Crichton: Prey"]
 constructor() {
 setInterval(()=> {
 this.books.push("Book Name: "+Date.now())
 }, 1000)
 }
}

```

在第一次 CD 运行时，`BComponent`将被重新渲染，我们将在控制台上看到以下记录:

```
The book property changed
ngDoCheck on BComponent called

```

当`App`组件开始更新 books 属性时，它本身(`App`)将被更新，但是在`BComponent`上运行的 CD 将被跳过，因为 Angular 在`BComponent` `books`数组中看不到任何变化。

然而，我们会看到`ngDoCheck`和`BComponent`在控制台上被调用。这是因为角度是如何构成的。

在组件上运行 CD 时，它会经历五个阶段:

1.  它更新其子组件的输入绑定
2.  如果输入属性发生变化，则运行`OnChanges`
3.  在子组件上运行`OnInit`,如果它还没有运行的话
4.  在子组件上运行`DoCheck`
5.  运行它的`updateRenderer`函数来更新 DOM 插值

`DoCheck`在运行前没有条件，所以只要组件/指令实现了`DoCheck`接口，它就必须运行。

如果`App`使用`Array#concat`方法推送新书，我们总会看到如下，因为`concat`返回一个新的对象引用:

```
The book property changed
ngDoCheck on BComponent called

```

### `DoCheck`有什么用途？

由于它不能在任何条件下运行，我们可以使用`DoCheck`来添加我们自己的定制 CD 机制。

默认的更改检测算法通过跨更改检测运行按引用比较绑定属性值来寻找差异。您可以使用这个钩子通过其他方式来检查和响应更改。

当默认的变更检测器检测到变更时，不管您是否执行额外的变更检测，它都会调用`ngOnChanges`(如果提供的话)。通常，你不应该同时使用`DoCheck`和`OnChanges`来响应相同输入的变化。

理想情况下，角度支持并宣扬我们对象的不变性，但是有了`ngDoCheck`，我们可以跟踪对象的变化。

以我们的例子为例:

```
@Component({
 selector: 'bcomp',
 template: `
 <div *ngFor="let book of books">
 {{book}}
 </div>
 `,
 changeDetection: ChangeDetectionStrategy.OnPush
})
export class BComponent {
 @Input() book
}
@Component({
 // ...
 template: `
 <bcomp [books]="books"></bcomp>
 `
})
export class App {
 books = ["Michael Crichton: Prey"]
 constructor() {
 setInterval(()=> {
 this.books.push("Book Name: "+Date.now())
 }, 1000)
 }
}

```

由于`App`组件使用`Array#push`方法推一个新值，Angular 不会接受这个变化，因为`Array#push`改变了`books`数组。因此，`BComponent`不会拾起零钱。这里`ngDoCheck`派上了用场，因为我们可以用它来检查对象突变。

`BComponent`将实现`DoCheck`接口并添加`ngDoCheck`方法。接下来，我们将注入`ChangeDetectorRef`，这样当`BComponent`的定制 CD 检测到变化时，它可以触发 CD:

```
@Component({
 selector: 'bcomp',
 template: `
 <div *ngFor="let book of books">
 {{book}}
 </div>
 `,
 changeDetection: ChangeDetectionStrategy.OnPush
})
export class BComponent implements OnChanges, DoCheck {
 @Input() books
 lastBooksArray
 constructor(private changeDetectorRef: ChnageDetectorRef) {}
 ngOnChanges() {
 console.log("The book property changed")
 this.lastBooksArray = this.books
 }
 ngDoCheck() {
 console.log("ngDoCheck on BComponent called")
 if(this.lastBooksArray.length != this.books.length){
 this.changeDetectorRef.markForChange()
 }
 }
}
@Component({
 // ...
 template: `
 <bcomp [books]="books"></bcomp>
 `
})
export class App {
 books = ["Michael Crichton: Prey"]
 constructor() {
 setInterval(()=> {
 this.books.push("Book Name: "+Date.now())
 }, 1000)
 }
}

```

我们将`ChangeDetectorRef`注入到`BComponent`的 CD 中 hook，实现了`DoCheck`和`OnChanges`，增加了`ngDoCheck`和`ngOnChanges`方法。

在`ngOnChanges`方法中，我们保存了最后一次`books`数组变化，因此当数组在`App`组件中发生变化时，我们可以检查`ngDoCheck`中的变化，并在下一个 CD 周期为 CD 标记`BComponent`。

### `OnDestroy`

`OnDestroy`是一个生命周期挂钩，当指令、管道或服务被销毁时调用。当实例被销毁时，使用该选项进行任何需要的自定义清理。

这个钩子主要用于取消订阅我们的可观察流，并分离事件处理程序以避免内存泄漏。

## 儿童组件挂钩

Angular 的结构像一棵树，所以组件可以有父组件和子组件。换句话说，什么在渲染组件，组件在渲染什么。

我们在这里将要学习的钩子是在组件的子组件的特定生命周期事件中被调用的钩子。挂钩是在父组件中声明的，因此开发人员可以添加他们的自定义代码来增强他们组件的功能。

当子组件的内容已经初始化，当子组件的 UI 或视图已经初始化并呈现，或者当子组件的更改检测已经运行时，父组件中的钩子被调用。

### `AfterContentInit`

当组件/指令的内容已经初始化时，调用`AfterContentInit`。在这种情况下，“内容”是投射在`<ng-content></ng-content>`标签之间的组件/指令:

```
@Component({
 selector: 'bcomp',
 template: `
 <div>This is a BComponent</div>
 `
})
export class BComponent {}
@Component({
 selector: 'acomp',
 template: `
 <ng-content></ng-content>
 `
})
export class AComponent implements AfterContentInit {
 ngAfterContentInit() {
 // ...
 }
}
@Component({
 template: `
 <acomp>
 <bcomp></bcomp>
 </acomp>
 `
})
export class App {}

```

`AComponent`将把它的标签`<acomp></acomp>`之间的任何元素投射到`ng-content`标签中。现在，在`App`组件中，`BComponent`被投射到`acomp`标签中。当`BComponent`被初始化时，`ngAfterContentInit`钩子将在`AComponent`中被调用。

### `AfterContentChecked`

在通过`ng-content`标签投射到组件中的组件/指令的默认变化检测器完成检查后，调用这个钩子:

```
@Component({
 selector: 'acomp',
 template: `
 <ng-content></ng-content>
 `
})
export class AComponent implements AfterContentInit {
 ngAfterContentInit() {
 // ...
 }
}
@Component({
 template: `
 <acomp>
 {{data}}
 </acomp>
 `
})
export class App implements AfterContentChecked {
 data: any
 ngAfterCOntentChecked() {
 //...
 }
}

```

`App`组件有一个位于`AComponent`内部的`data`属性。当`data`属性改变时，将调用`ngAfterContentChecked`方法。

### `AfterViewInit`

这个钩子在一个组件的视图及其子视图被创建并完全初始化后被调用。

当我们想使用`ViewChild` / `ViewChildren`引用组件中的组件/指令实例时，这个钩子很方便。

在任何其他方法中使用它可能会导致错误，因为组件/指令在使用时可能尚未初始化:

```
@Component({
 selector: 'acomp',
 template: `
 `
})
export class AComponent {
 aCompMethod() {
 //...
 }
}
@Component({
 template: `
 <acomp #acomp></acomp>
 `
})
export class App {
 @ViewChild('acomp') acomp: AComponent
 constructor() {
 setInterval(()=>{
 this.acomp.aCompMethod()
 }, 1000);
 }
}

```

我们通过把`#`放到`App`组件模板中，得到了`acomp`的类`AComponent`的引用。

然后，我们用`ViewChild`告诉 Angular 将`AComponent`的实例设置为`acomp`变量。如果在我们请求`AComponent`的实例时`AComponent`还没有初始化，我们可能会出错。

如果我们以一秒的间隔调用`AComponent`的`aCompMethod`，它可能会抛出一个引用错误，因为`AComponent`在这段时间内还没有初始化。使用更大的间隔，`aCompMethod`将被正确调用，因为到那时，`AComponent`将被创建。

为了防止这种引用丢失，我们将实现`AfterViewInit`接口并添加`ngAfterViewInit`方法。然后，呼叫`ngAfterViewInit`中的`aCompMethod`:

```
@Component({
 selector: 'acomp',
 // ...
})
export class AComponent {
 aCompMethod() {
 }
}
@Component({
 template: `
 <acomp #acomp></acomp>
 `
})
export class App implements AfterViewInit {
 @ViewChild('acomp') acomp: AComponent
 ngAfterViewInit() {
 //...
 setInterval(()=>{
 this.acomp.aCompMethod()
 }, 1000);
 }
}

```

现在，我们在调用`aCompMethod`时不会有引用丢失，因为它将在类`AComponent`被创建和初始化时被调用。

### `AfterViewChecked`

在运行组件/指令的子组件的更改检测器进行检查后，调用此挂钩。

注意不要在这里设置任何绑定到模板的变量。如果这样做，您将收到`"Expression has changed after it was checked"`错误。

```
@Component({
 selector: 'acomp',
 // ...
})
export class AComponent {
 @Input() data
 aCompMethod() {
 return this.data * 9
 }
}
@Component({
 template: `
 <div>
 {{aCompData}}
 <acomp [data]="data" #acomp></acomp>
 <button (click)="changeData()"></button>
 </div>
 `
})
export class App implements AfterViewChecked {
 @ViewChild('acomp') acomp: AComponent
 data: any
 acompData:any
 changeData() {
 this.data = Date.now()
 }
 ngAfterViewChecked() {
 //...
 this.acompData = this.acomp.aCompMethod()
 }
}

```

`AComponent`有一个输入绑定`data`，当我们点击`changeData`按钮时，我们改变了数据绑定，因为我们想在`aCompData`中显示`AComponent`的`aCompMethod`结果。

如果我们在任何其他地方调用该方法，我们可能会遇到一个引用错误，因为 Angular 可能不会用 CD run 在`AComponent`上完成。所以我们实现了`AfterViewChecked`接口并添加了`ngAfterViewInit`方法，因为该方法将在`AComponent`完成 CD 运行后运行。

在这种情况下，当我们调用`aCompMethod`时，我们不会得到不正确的数据值，因为数据将具有当前值。

## 结论

这是带有示例的角度生命周期事件的综合列表。这篇文章深入解释了每个生命周期事件何时发生，它们的时间表，以及它们触发的原因。

下一次，如果您碰巧忘记了任何生命周期事件，您可以随时回到本文。

如果你对此有任何问题或我应该补充的任何东西，请随时留下评论！

## 像用户一样体验 Angular 应用程序

调试 Angular 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪生产中所有用户的角度状态和动作感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/angular-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/2794ac39244976f37c4941d9a910be23.png)](https://lp.logrocket.com/blg/angular-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/angular-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你网站上发生的一切，包括网络请求、JavaScript 错误等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket NgRx 插件将角度状态和动作记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化调试 Angular 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/angular-signup)。