# Vue 中的依赖注入:优点和警告

> 原文：<https://blog.logrocket.com/dependency-injection-vue-advantages-caveats/>

## 介绍

在构建大型复杂的应用程序时，依赖注入是一个很好的模式。构建这些应用程序的主要挑战是创建松散耦合的组件，这是依赖性管理最关键的地方。

本文将介绍依赖注入，它的优点和缺点，以及如何在 Vue 项目中处理依赖注入。

## 什么是依赖注入？

依赖注入是一种不允许类创建依赖的设计模式。相反，它们从外部来源请求依赖性。这种设计模式强烈主张一个类不应该静态地配置它的依赖关系。

## 为什么要依赖注入？

当我们可以将数据从父组件传递到子组件时，为什么还要在 Vue 中使用依赖注入？

使用 props 的一些经验会使您接触到术语 prop drilling，这是一个过程，通过遍历不需要数据的其他部分，将 props 从组件树的一个部分传递到另一个部分，而只是帮助在树中传递数据:

```
RexComponent (Anyone needs my wallet address?)
├── TomComponent
   ├── PeterComponent
      ├── DurryComponent (yes I need it)

```

根据上面的片段，让我们考虑一个场景，其中`RexComponent`有一个钱包地址要分发，而`DurryComponent`是唯一需要钱包地址的人。我们必须将钱包地址从`RexComponent`传递到`TomComponent`再传递到`PeterComponent`，最后传递到`DurryComponent`。这导致了`TomComponent`和`PeterComponent`中的冗余代码。

通过依赖注入，`DurryComponent`将从`RexComponent`处接收钱包，而无需经过`TomComponent`和`PeterComponent`。

为了在 Vue 中处理依赖注入，提供和注入选项是现成的。

要注入的依赖项由父组件使用 provide 属性提供，如下所示:

```
//Parent component
<script lang="ts">
    import {Component, Vue} from 'vue-property-decorator';
    import Child from '@/components/Child.vue';
    @Component({
        components: {
            Child
        },
        provide: {
            'name': 'Paul',
        },
    })
    export default class Parent extends Vue {
    }
</script>

```

使用注入的属性将提供的依赖项注入到子组件中:

```
<template>
  <h1> My name is {{name}}</h1>
</template>
<script lang="ts">
    import {Component, Inject, Vue} from 'vue-property-decorator';
    @Component({})
    export default class Child extends Vue {
        @Inject('name')
        name!: string; // non-null assertion operator
    }
</script>

```

`vue-property-decorator`还公开了用于声明提供者的`@Provide`装饰器。
使用`@Provide`装饰器，我们可以使依赖关系在父组件中可用:

```
//Parent component
export default class ParentComponent extends Vue { 
  @Provide("user-details") userDetails: { name: string } = { name: "Paul" }; 
}

```

类似地，依赖关系可以注入到子组件中:

```
//Child component
<script lang="ts">
    import {Component, Inject, Vue} from 'vue-property-decorator';
    @Component({})
    export default class ChildComponent extends Vue {
        @Inject('user-details')
        user!: { name: string };
    }
</script>

```

### 提供商层次结构

提供者层次结构规则规定，如果在组件的依赖关系树中的多个提供者中使用相同的提供者键，那么与子组件最近的父组件的提供者将覆盖层次结构中更高的其他提供者。

为了便于理解，让我们考虑下面的片段:

```
FatherComponent
├── SonComponent
   ├── GrandsonComponent

//Father component
<script lang="ts">
    import {Component, Vue} from 'vue-property-decorator';
    import SonComponent from '@/components/Son.vue';
    @Component({
        components: {
            SonComponent
        },
        provide: {
            'family-name': 'De Ekongs',
        },
    })
    export default class FatherComponent extends Vue {
    }
</script>

```

在上面的代码片段中，`family-name`依赖项是由`FatherComponent`提供的:

```
//Son component
<script lang="ts">
    import {Component, Vue} from 'vue-property-decorator';
    import GrandsonComponent from '@/components/Grandson.vue';
    @Component({
        components: {
            GrandsonComponent
        },
        provide: {
            'family-name': 'De Royals',
        },
    })
    export default class SonComponent extends Vue {
    }
</script>

```

在上面的代码片段中，`SonComponent`覆盖了以前由`FatherComponent`提供的`family-name`依赖关系:

```
//Grand son Component
<template>
  <h1> Our family name is {{familyName}}</h1>
</template>
<script lang="ts">
    import {Component, Inject, Vue} from 'vue-property-decorator';
    @Component({})
    export default class Child extends Vue {
        @Inject('family-name')
        familyName!: string; // non-null assertion operator
    }
</script>

```

正如您所猜测的，`De Royals`将在`GrandsonComponent`的模板中呈现。

在一些复杂的 Vue 项目中，您可能会避免覆盖依赖项来实现代码库的一致性。在这种情况下，覆盖依赖关系被视为一种限制。

幸运的是，JavaScript 为我们提供了 [ES6 符号](https://blog.logrocket.com/practical-use-cases-for-javascript-es6-proxies/)来弥补与多个具有相同密钥的提供者相关的缺陷。

[根据 MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol) 的说法，“符号通常用于向对象添加唯一的属性键，这些键不会与任何其他代码可能添加到该对象的键相冲突，并且对其他代码通常用来访问该对象的任何机制都是隐藏的。”

换句话说，每个符号都有独特的身份:

```
Symbol('foo') === Symbol('foo')  // false

```

我们可以使用 ES6 `Symbol`，而不是像在前面的代码中那样在提供者和注入端使用相同的字符串键。这将确保没有依赖关系被另一个覆盖:

```
export const FAMILY = {
    FAMILY_NAME: Symbol('FAMILYNAME'),
};

```

## 依赖注入的优势

1.  提高代码的可重用性
2.  通过模仿/剔除注入的依赖关系，简化应用程序的单元测试
3.  减少了样板代码，因为依赖项是由它们的注入器组件初始化的
4.  去耦组件逻辑
5.  使扩展应用程序类变得更加容易
6.  增强应用程序的配置

## 依赖注入的注意事项

1.  Vue 中的依赖注入不支持构造函数注入。对于使用基于类的组件的开发人员来说，这是一个主要的缺点，因为构造函数不会初始化组件的类属性
2.  许多编译时错误被推送到运行时
3.  使用 Vue 依赖注入，代码重构可能会非常繁琐
4.  Vue 的依赖注入不是反应式的

## 结论

在本文中，我们建立了对 Vue 中依赖注入的基本理解。我们走过了与具有相同密钥的多个提供者相关联的缺点，同时我们还使用 ES6 符号实现了对该缺点的补救。

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。