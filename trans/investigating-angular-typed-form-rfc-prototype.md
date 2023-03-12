# 研究角型 RFC 原型

> 原文：<https://blog.logrocket.com/investigating-angular-typed-form-rfc-prototype/>

有史以来角度回购最受欢迎的问题是什么？

你可能已经知道了第 13721 号问题。五年后，期待已久的功能需求随着一个[原型演示](https://stackblitz.com/edit/angular-typed-forms-ex-cvwtas?file=src%2Fapp%2Fprofile%2Fprofile.component.ts)到达 RFC 阶段。

在本文中，我将回顾这个问题的历史，并研究 RFC 及其原型。

## 为什么这是最受欢迎的特性请求？

Angular 被广泛认为是企业应用的最佳框架，因为它的[表单包](https://angular.io/api/forms)，这是它最重要的特性。

Angular 提供了两个表单包:模板驱动表单和反应式表单。反应式表单更强大、可测试，适合构建更复杂的表单，比如有大量字段、许多验证规则和一些嵌套子表单(如地址和家属)的注册表单。反应式表单可以比模板驱动的表单更好地处理这种复杂性。

但是一旦你开始处理一个角度反应形式，你会很快注意到一个问题:它不是强类型的。整个反应式表单 API 不支持强类型化——来自`FormControl`、`FormGroup`和`FormArray`的属性/方法都返回`any`。这种类型安全性的缺乏会导致许多潜在的问题，并使维护复杂或嵌套的窗体变得更加困难。

因此，自从反应式表单 API 发布以来，Angular 社区一直要求强类型的反应式表单。该请求在第 [#13721](https://github.com/angular/angular/issues/13721) 期仅用两行概述:

> 反应式表单应该在复杂的表单中使用，但是控件的值变化是可以观察到的<any>，这完全违背了复杂代码的良好实践。</any>

应该有一种创建强类型表单控件的方法。

问题#13721 不是唯一一个请求强类型表单的问题——有许多类似的功能请求，如问题 [#17000](https://github.com/angular/angular/issues/17000) 、 [#16999](https://github.com/angular/angular/issues/16999) 和[# 16933](https://github.com/angular/angular/issues/16933)——但没有一个像问题#13721 那样直截了当。它简明扼要。

我们很多人都在等待 Angular 团队的官方解决方案，但这是一个漫长的等待。一些开发人员试图通过使用类型定义文件或创建自定义包装来自行解决缺乏类型安全的问题。有些人还创建了自己的第三方库。

所有这些尝试都各有利弊，没有一个是公认的最佳方法。所以，我们继续等待。

## 为什么花了这么长时间？

当主要稳定角版本为 2.x 时，该问题首次公开；Angular 13 于 2021 年 11 月发布[。现在我们终于通过了 RFC 阶段，我们可以说只用了五年零一个月就达成了一个解决方案！](https://blog.angular.io/angular-v13-is-now-available-cce66f7bc296)

为了理解为什么花了这么长时间，我将把主要事件总结成下面的时间表。

*   **2016 年 12 月 16 日** : [@asfernandes](https://github.com/asfernandes) 开[第 13721 期](https://github.com/angular/angular/issues/13721#issue-198158565)
*   **2017–2020**:许多社区成员和贡献者参与了讨论，但其他人试图通过各种拉取请求和一些第三方库来实现他们自己的解决方案:
*   2020 年 6 月 3 日 : [@kara](https://github.com/kara) 分享了来自 Angular 团队的[更新:“我们听说这是一个很大的痛点。我们将很快开始开发更强类型的表单”](https://github.com/angular/angular/issues/13721#issuecomment-637698836)
*   【2020 年 6 月至 10 月:在 Angular 团队确认他们正在解决这个问题后，一些社区成员和 Angular 贡献者就不同的实现方法展开了更深入的讨论
*   【2020 年 11 月 18 日 : [@pauldraper](https://medium.com/u/301b362ad9bc) 在#13721 帖子上评论说，这个问题现在已经标记在[角度路线图](https://angular.io/guide/roadmap#better-developer-ergonomics-with-strict-typing-for-angularforms)上，以供未来发展
*   【2021 年 2 月 2 日:[@ ristomati](https://medium.com/u/455ce958d2f3)在#13721 线程评论说，Angular 团队研究了@ng-stack/forms 并制定了一份[的 PR 草案](https://github.com/angular/angular/pull/38406/files)
*   【2021 年 9 月 22 日 : [@dylhunn](https://medium.com/u/db1c88da04ac) 提供了项目状态的更新以及他自己的发展[路线图](https://github.com/angular/angular/issues/13721#issuecomment-924284753)，重新点燃了对这个问题和即将到来的原型的兴奋
*   【2021 年 12 月 17 日 : @dylhunn 宣布“严格类型化反应形式”[征求意见稿](https://github.com/angular/angular/discussions/44513#discussion-3748538)现已发布
*   【2022 年 1 月 25 日 : @dylhunn 宣布[打字表格 RFC 关闭](https://github.com/angular/angular/discussions/44513#discussioncomment-2038985)

正如你所看到的，Angular 团队在它开放三年半后才正式承认这个问题。我不想猜测为什么 Angular 团队花了这么长时间才做出响应，但 Angular 社区在追求这一功能请求时表现出了极大的坚持和热情。

## 强类型反应形式的技术要点

Angular 类型表单原型是通过向`AbstractControl`类和`FormBuilder`类添加泛型来实现的。这使得窗体对于控件和值都是类型安全和空安全的。

最初，@dylhunn 试图使用“值类型”方法，这意味着类型参数是数据模型/接口。

```
interface Cat {
  name: string;
  lives: number;
}

const cat = new FormGroup<Cat>({
  name: new FormControl('spot, ...),,
  lives: new FormControl(9, ...),
});
```

但是他很快发现这种方法是不够的:值类型不足以[处理深度嵌套的表单组](https://blog.logrocket.com/reactive-form-controls-form-groups-angular/)。添加强类型不仅需要`FormGroup.value`，还需要`FormGroup.controls`。

最终的设计使用了“控制类型”的方法。简而言之，`FormGroup`上的类型参数是一个包含控件的对象。下面显示了一个示例:

```
const cat = new FormGroup<{
    name: FormControl<string>,
    lives: FormControl<number>,
}>(...);
```

### 重置和可空性

原型在现有表单 API 的基础上应用了强类型，这些变化是复杂而棘手的。如何处理控件`reset`就是一个很好的例子。现有控件`reset()`会将控件的值设置为`null`。这种现有行为会导致所有推断出的类型都变得可为空。

```
const dog = new FormControl('spot'); // dog has type FormControl<string|null>
dog.reset();
const whichDog = dog.value; // null
```

为了克服这种不希望的行为，并仍然保持现有代码不被破坏，引入了`initialValueIsDefault`标志，以允许控件被`reset()`为它们的默认值，而不是默认为`null`。这种方法为用户提供了避免不必要的可空类型的选项，同时保持现有代码正常工作。

```
const dog = new FormControl('spot', {initialValueIsDefault: true}); // dog has type FormControl<string>
dog.reset();
const whichDog = dog.value; // spot
```

### 如何实现`Get`方法

原型中另一个真正酷的实现是使用 TypeScript 模板字符串文字和递归类型在`AbstractControl.get()`中执行类型推断。以下是从 PR 中提取的代码:

```
// extract from @dylhunn PR #43834 https://github.com/angular/angular/pull/43834
/**
 * Tokenize splits a string literal S by a delimeter D.
 */
type Tokenize<S extends string, D extends string> =                                      /*\n*/
    string extends S ? string[] : /* S must be a literal */                              /*\n*/
    S extends `${infer T}${D}${infer U}` ? [T, ...Tokenize<U, D>] : /* Recursive case */ /*\n*/
    [S] /* Base case */                                                                  /*\n*/
    ;

/**
 * Navigate takes a type T and an array K, and returns the type of T[K[0]][K[1]][K[2]]...
 */
type Navigate<T, K extends(string|number)[]> =                                 /*\n*/
    T extends object ? /* T must be indexable (object or array) */             /*\n*/
    (K extends [infer K0, ...infer K1] ? /* Split K into head and tail */      /*\n*/
         (K0 extends keyof T ? /* head(K) must index T */                      /*\n*/
              (K1 extends(string|number)[] ? /* tail(K) must be an array */    /*\n*/
                   (Navigate<T[K0], K1>) /* explore T[head(K)] by tail(K) */ : /*\n*/
                   any) /* tail(K) was not an array, give up */ :              /*\n*/
              any) /* head(K) does not index T, give up */ :                   /*\n*/
         T) /* K is empty; just return T */ :                                  /*\n*/
    T /* T is a primitive, return it */                                        /*\n*/
    ;

/**
 * Get takes a type T and some property names or indices K.
 * If K is a dot-separated string, it is tokenized into an array before proceeding.
 * Then, the type of the nested property at K is computed: T[K[0]][K[1]][K[2]]...
 * This works with both objects, which are indexed by property name, and arrays, which are indexed
 * numerically.
 *
 * TODO: Array indices work in the format ['foo', 0, 'bar'], but not in the format 'foo.0.bar'. This
 * is not currently possible to support with the TypeScript type system.
 *
 * @publicApi
 */
export type Get<T, K> =                                                            /*\n*/
    K extends string ? Get<T, Tokenize<K, '.'>>: /* dot-separated path */          /*\n*/
    K extends Array<string|number>? Navigate<T, K>: /* array of path components */ /*\n*/
    never;

```

`Get`将类型`T`和属性名作为点分隔的字符串或数组，并返回子属性类型的类型。示例用法如下所示:

```
// Example usage: let's go to a party!

type Party = {
  venue: {
    address: string,
    dates: Array<{
      month: string,
      day: number,
    }>
  },
}

// This evaluates to `string`.
type whereIsTheParty = Get<Party, 'venue.address'>;
// This evaluates to `number`.
type whatDayIsTheParty = Get<Party, ['venue', 'dates', 0, 'day']>;
```

这个代码块是对 [TypeScript 的模板文字类型特性](https://www.typescriptlang.org/docs/handbook/2/template-literal-types.html)的完美使用，该特性在 TypeScript 版本 4.1 中可用。

## 了解 RFC #44513

在开源世界中，大型框架的重大变更通常伴随着变更请求(RFC)。RFC 是实施变更的正式请求或建议，包含批准变更所需的所有信息。

RFC #44513 简明扼要，提供了适当的细节。它包括以下内容:

*   RFC 的目标
*   新 API 介绍
*   API 的局限性
*   就几个重要的设计问题征求反馈

RFC 非常清楚地表明，主要目标之一是维护向后兼容性，引用它的话来说就是“关注于向现有系统增量地添加类型。”这类似于 TypeScript 处理 JavaScript 兼容性的原则:向后兼容，关注类型，允许混合使用。

在 RFC 讨论期间，许多人担心他们现有的代码是否可以顺利迁移，一些人建议为将来的角度更新增加额外的选项。@dylhunn 解释了当前策略背后的基本原理，并承诺在发布新功能时附带一个迁移示意图，以保持依赖于先前行为的代码正常工作。

这种设计的充分讨论的向后兼容性将有望使开发人员更容易在现有的代码库中采用这种变化。

## RFC 期间的反馈要点

不用说，Angular 社区对 RFC 感到非常兴奋。在为期四周的 RFC 期间，进行了非常活跃的讨论，参与者提出了许多建设性的建议——您可以从对话中感受到兴奋。

讨论中有一些亮点没有包含在最终的 RFC 中，但我认为仍然值得注意:

*   **`FormControls`**的可空性:提出了关于添加选项以处理可空表单控件的问题和建议。许多讨论集中在新的`initialValueIsDefault`标志上，它允许`reset()`将表单控件设置为预定义的默认值
*   **`FormRecord`或`FormTuple`** 的用例:用户 [@johncrim](https://github.com/johncrim) ， [@bbarry](https://github.com/bbarry) ， [@Harpush](https://github.com/Harpush) 根据他们的项目经验确认`FormRecord`是一个期望的特性
*   **当前的限制(验证器和`ControlValueAccessor`)是否阻碍了特性**的发布:普遍的共识是它们是好的，但是不应该阻碍这个非常需要的特性

许多参与者已经试用了原型，并发现了几个问题:

@dylhunn 在他的结束语中对讨论的未来做了如下总结:

*   “我们不会在模板类型检查改进、验证器或 CVAs 上阻止这个特性。我们可能会在这些方面进行改进，可能会在后续版本中进行改进”
*   “我们已经确定了一些`FormRecord`(而不是`FormTuple`)可能有用的用例，我们将考虑将其作为后续添加”
*   “我们处理可空性的方法有一些折衷，但似乎是一个可接受的不间断解决方案”

他还感谢参与者的帮助，并承诺[解决他们发现的一些实施问题](https://github.com/angular/angular/discussions/44513#discussioncomment-2038985)。

## 角的类型化反应形式的未来

在 [RFC 关闭评论](https://github.com/angular/angular/discussions/44513#discussioncomment-2038985)中，@dylhunn 写道:“基于反馈和最初的原型，我们计划推进提议的设计。随着实施的进展，我们将提供更多更新，并采纳您的反馈，敬请关注。”

根据他之前的暗示，下一个里程碑将是作为 Angular v14 的一部分的正式功能发布，它将带有一个迁移示意图。

当前原型(以及即将发布的第一个版本)的局限性是缺少对验证器和`ControlValueAccessor`的模板类型检查。这意味着当前的模板类型检查将无法捕捉到`FormControl`和模板 DOM 控件之间的类型不匹配。

因此，下一步是对模板类型检查进行改进。

## 摘要

反应式表单是 [Angular](https://blog.logrocket.com/tag/angular) 最受欢迎的特性之一，它缺乏强类型一直是一个关键问题。当 Angular v14 在未来几个月发布这一功能时，它将把反应式表单带到一个新的水平，并可能使 Angular 成为需要复杂表单的应用程序更有吸引力的选择。

感谢社区、@dylhunn 和其他贡献者的坚持和努力，这个请求现在变成了现实！亡羊捕牢，未为晚也。我期待着即将到来的正式发布。

## 像用户一样体验 Angular 应用程序

调试 Angular 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪生产中所有用户的角度状态和动作感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/angular-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/2794ac39244976f37c4941d9a910be23.png)](https://lp.logrocket.com/blg/angular-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/angular-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你网站上发生的一切，包括网络请求、JavaScript 错误等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket NgRx 插件将角度状态和动作记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化调试 Angular 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/angular-signup)。