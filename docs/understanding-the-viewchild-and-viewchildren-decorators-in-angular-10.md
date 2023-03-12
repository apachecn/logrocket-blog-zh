# 了解 Angular 10 - LogRocket 博客中的 ViewChild 和 ViewChildren 装饰器

> 原文：<https://blog.logrocket.com/understanding-the-viewchild-and-viewchildren-decorators-in-angular-10/>

Angular 中的`@ViewChild`和`@ViewChildren`装饰器通过设置视图查询来提供对视图 DOM 中子元素的访问。视图查询是对包含元素元数据的[组件视图](https://angular.io/guide/glossary#view)内的子元素的请求引用。这些装饰器的范围仅限于组件视图及其嵌入的子视图。在无法以传统方式访问和修改视图中的元素的情况下，这些装饰器尤其有用。

例如，如果一个库附带了一个组件或指令，并且带有您想要更改的公共非输入或非输出属性，那么这些 decorators 将允许您访问和修改它们。这些装饰器还有助于暴露在子组件中配置的提供者，以注入依赖关系(如服务、配置值等。)主组件可能无法访问。

在本文中，我们将介绍如何使用`@ViewChild`和`@ViewChildren`装饰器，它们的属性做什么，以及如何指定它们的属性。

## 视图查询和 AfterViewInit 生命周期挂钩

当组件视图及其子视图完全初始化时，调用`AfterViewInit`生命周期钩子。因此，对于即时修改或赋值，访问视图查询的最佳位置是在`ngAfterViewInit`回调中，因为视图查询已经被解析和设置。试图在`ngAfterViewInit`响应之前访问它们可能会产生未定义的值。然而，`@ViewChild`装饰器提供了一个`static`属性，可以在变更检测运行之前设置该属性来解析视图查询。我们将在下面介绍如何使用这个属性。

## ViewChild 装饰器

这个装饰器接受三个属性，一个`selector`、一个`read`和一个`static`属性。`read`和`static`属性是可选的。这些属性是这样指定的:

```
@ViewChild(selector {read: readValue, static: staticValue}) property;
```

## 支持的视图子选择器

`selector`属性指定要查询组件视图中的哪个子元素。根据`[@ViewChild](https://angular.io/api/core/ViewChild)`文档，支持五种选择器。这些是:

1)具有`@Component`或`@Directive`装饰器的类

在第一个例子中，`MenuItemComponent`是从`MenuComponent`视图中查询的:

```
@Component({
  selector: 'menu-item',
  template: `<p>{{menuText}}</p>`
})

export class MenuItemComponent {
  @Input() menuText: string;
}

@Component({
  selector: 'menu',
  template: `<menu-item [menuText]="'Contact Us'"></menu-item>`
})

export class MenuComponent{
  @ViewChild(MenuItemComponent) menu: MenuItem;
}
```

下面是一个带有指令的示例:

```
@Directive({
  selector: '[textHighlight]'
})

export class TextHighlightDirective{}

@Component({
  selector: 'profile',
  template: '<p textHighlight>Some text to highlight</p>'
})

export class ProfileComponent{
  @ViewChild(TextHighlightDirective) highlightedText: TextHighlightDirective;
}
```

2)字符串形式的模板引用变量。模板引用变量通常在模板中使用，但在本例中，它用于配置视图查询:

```
@Component({
  selector: 'menu-item',
  template: `<p>{{menuText}}</p>`
})

export class MenuItemComponent {
  @Input() menuText: string;
}

@Component({
  selector: 'menu',
  template: `
    <menu-item #aboutUs [menuText]="'About Us'"></menu-item>
    <menu-item  #contactUs [menuText]="'Contact Us'"></menu-item>
  `
})

export class MenuComponent{
  @ViewChild('aboutUs') aboutItem: MenuItem;
  @ViewChild('contactUs') contactItem: MenuItem;
}
```

3)在当前组件的子组件树中定义的提供者。在这个例子中，`SampleService`被指定为`FirstChildComponentClass`的提供者令牌。由于`<first-child>`是`ParentComponent`中的一个元素，我们可以使用`SampleService`类作为令牌从它那里访问`SampleService`:

```
export class SampleService {}

@Component({
  selector: 'first-child',
  providers: [SampleService]
})

export class FirstChildComponent{}

@Component({
  selector: 'parent',
  template: '<first-child></first-child>'
})

export class ParentComponent{
  @ViewChild(SampleService) sampleService: SampleService;
}
```

4)通过字符串令牌定义的提供者。虽然[在文档](https://angular.io/api/core/ViewChild#description)中有说明，但是通过这个方法获取提供者会返回未定义的值。这是在 [Ivy](https://angular.io/guide/ivy) 中的一个回归，在 Angular 9 中默认启用。针对此的[修复已经完成，但是在本文发布时，它还没有包含在任何版本中。为了让它工作，您需要在`tsconfig.json`文件中禁用 Ivy，而使用`ViewEngine`:](https://github.com/angular/angular/pull/38321)

```
{
  "angularCompilerOptions": {
    "enableIvy": false,
  }
}
```

下面是如何使用通过字符串标记定义的提供者作为选择器:

```
@Component({
  selector: 'first-child',
  providers: [{ provide: 'TokenA', useValue: 'ValueA' }]
})

export class FirstChildComponent{}

@Component({
  selector: 'parent',
  template: '<first-child></first-child>'
})

export class ParentComponent{
  @ViewChild('TokenA') providerA: string;
}
```

但是，如果您想在 Ivy 中使用这种类型的选择器，您可以使用`read`属性来获取一个视图查询:

```
export class ParentComponent{
  @ViewChild(FirstChildComponent, { read: 'TokenA' }) providerA: string;
}
```

5)一个`TemplateRef`。可以使用`@ViewChild`装饰器访问嵌入式模板，然后使用`ViewContainerRef`实例化嵌入式视图:

```
@Component({
  selector: `container`,
  template: `<ng-template><h1>This container is empty</h1></ng-template>`
})

export class ContainerComponent{
  @ViewChild(TemplateRef) contTemplate: TemplateRef;
}
```

为了更好地理解如何使用这些配置好的视图查询，请查看每种选择器的这些[实例](https://stackblitz.com/edit/view-child-examples)。它们说明了如何使用视图查询来访问和修改嵌入视图。

## 使用读取属性

`read`属性允许您从查询的元素中选择各种标记。这些标记可以是用于依赖注入的提供者标记，或者在某些情况下，可以是视图查询的类型。这是一个可选属性。

在下面的例子中，`FirstChildComponent`有一个提供者配置，其中有各种依赖标记，比如类、字符串标记和注入标记。这些令牌与`read`属性一起可以向嵌入了`FirstChildComponent`的父组件公开这些依赖关系。使用`ViewChild`装饰器和指定每个对应令牌的`read`属性在`ParentComponent`中访问了所有这些依赖关系。

还可以使用`read`属性指定视图查询的类型。在同一个例子中，`fcElementRef`和`fcComponent`属性都是`FirstChildComponent`的查询，但是基于`read`属性被指定为什么，它们具有不同的类型:

```
export class SampleService {}

export const ExampleServiceToken = new InjectionToken<string>('ExampleService');

@Component({
  selector: 'first-child',
  providers: [
    SampleService,
    { provide: 'TokenA', useValue: 'valueA' },
    { provide: 'TokenB', useValue: 123 },
    { provide: ExampleServiceToken, useExisting: SampleService },
    { provide: 'TokenC', useValue: true }
  ]
})

export class FirstChildComponent{}

@Component({
  selector: 'parent',
  template: `<first-child></first-child>`
})

export class ParentComponent{
  @ViewChild(FirstChildComponent, { read: 'TokenA' }) dependencyA: string;
  @ViewChild(FirstChildComponent, { read: 'TokenB' }) dependencyB: number;
  @ViewChild(FirstChildComponent, { read: 'TokenC' }) dependencyC: boolean;
  @ViewChild(FirstChildComponent, { read: SampleService }) sampleService: SampleService;
  @ViewChild(FirstChildComponent, { read: ElementRef }) fcElementRef: ElementRef;
  @ViewChild(FirstChildComponent, { read: FirstChildComponent }) fcComponent: FirstChildComponent;
  @ViewChild(FirstChildComponent, { read: ExampleServiceToken }) exampleService: SampleService;
}
```

## 使用静态属性

`static`属性接受一个布尔值，并且是可选的。默认情况下，它是假的。如果为真，则在完全初始化完整的主管视图和数据绑定属性之前，视图查询被解析。如果设置为 false，则在组件视图和数据绑定属性完全初始化后解析视图查询。

在这个例子中，使用`true`和`false static`属性以及在`ngOnInit`和`ngAfterViewInit`回调中为每个属性记录的值来查询段落元素:

```
@Component({
  selector: 'display-name',
  template: '<p #displayName>{{name}}</p>'
})

export class DisplayNameComponent implements OnInit, AfterViewInit{
  @ViewChild('displayName', {static: true}) staticName: ElementRef;
  @ViewChild('displayName', {static: false}) nonStaticName: ElementRef;

  name: string = "Jane";

  ngOnInit(){
   logValues('OnInit');
  }

  ngAfterViewInit(){
   logValues('AfterViewInit');
  }

  logValues(eventType: string){
    console.log(`[${eventType}]\n staticName: ${this.staticName}, name value: "${this.staticName.nativeElement.innerHTML}"\n nonStaticName: ${this.nonStaticName}, name value: "${this.nonStaticName.nativeElement.innerHTML}"\n`);
  }
}
```

以下是将被记录的内容:

```
[OnInit]
staticName: [object Object], name value: "" // static: true
nonStaticName: undefined, name value: "" // static: false

[AfterViewInit]
staticName: [object Object], name value: "Jane" // static: true
nonStaticName: [object Object], name value: "Jane"  // static: false
```

在`ngOnInit`回调中，没有内插值被初始化，但是通过`{ static: true}`,`staticName`视图查询已经被解析，但是`nonStaticName`未被定义。然而，在`AfterViewInit`事件之后，所有的视图查询都被解决了。

您可以查看这些[活生生的例子](https://stackblitz.com/edit/view-child-examples)，它们更好地说明了如何通过`@ViewChild`装饰器使用`read`和`static`属性。

## 视图儿童

`@ViewChildren`装饰器的工作方式类似于`@ViewChild`装饰器，但是它不是配置单个视图查询，而是获取一个查询列表。从组件视图 DOM 中，它检索子元素的`[QueryList](https://angular.io/api/core/QueryList)`。当对子元素进行任何更改时，该列表会更新。`@ViewChildren`装饰器获取两个属性，一个`selector`和一个`read`属性。这些属性的工作方式与`@ViewChild`装饰器相同。任何匹配的子元素都将成为列表的一部分。这里有一个例子:

```
@Component({
  selector: 'item-label',
  template: `<h6>{{labelText}}</h6>`
})

export class ItemLabelComponent{
  @Input() labelText: string;
}

@Component({
  selector: 'item',
  template: `<item-label *ngFor="let label of labels" [labelText]="label"></item-label>`
})

export class ItemComponent{
  @ViewChildren(ItemLabelComponent) allLabels: ItemLabelComponent;

  labels = ['recent', 'popular', 'new'];
}
```

由于所有的`<item-label>`都将被选择，因此`allLabels`的长度将为三。`QueryList`有许多[方法](https://angular.io/api/core/QueryList)，您可以使用它们来操作视图查询。

对于如何使用`@ViewChildren`装饰器和指定`read`属性的更广泛的说明，请查看[这个例子](https://stackblitz.com/edit/view-child-examples)。

## 结论

`@ViewChild`和`@ViewChildren`装饰器是查询视图中子元素的优秀工具。了解如何使用它们可以为您提供更多自定义组件视图的选项。如果你想了解更多关于如何使用它们，请查看[这些例子](https://stackblitz.com/edit/view-child-examples?file=src/app/app.component.ts)。

## 像用户一样体验 Angular 应用程序

调试 Angular 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪生产中所有用户的角度状态和动作感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/angular-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/2794ac39244976f37c4941d9a910be23.png)](https://lp.logrocket.com/blg/angular-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/angular-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你网站上发生的一切，包括网络请求、JavaScript 错误等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket NgRx 插件将角度状态和动作记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化调试 Angular 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/angular-signup)。