# 在 React 和 Angular - LogRocket 博客中创建复合组件

> 原文：<https://blog.logrocket.com/creating-compound-components-in-react-and-angular/>

毫无疑问，组件是创作前端用户界面的强大抽象(事实证明，[基于文本的 ui](https://github.com/vadimdemedes/ink)、[应用路由器](https://github.com/ReactTraining/react-router)和许多其他类型的界面)。

但是有许多不同的方法来创作组件。在本帖中，我们将讨论复合组件模式，以及它何时可能是组件作者的合适选择。

## 什么是复合组件？

[复合组件](https://blog.logrocket.com/guide-to-react-compound-components-9c4b3eb482e9/)是一组单独的组件，它们组合在一起产生一种有凝聚力的复杂行为。它们一起工作，在幕后共享状态，为使用这些组件的程序员提供一个直观而富有表现力的语法。复合组件的一个最常见的例子来自普通的 HTML:

```
<select>
  <option value="apples">Apples</option>
  <option value="oranges">Oranges</option>
  <option value="pears">Pears</option>
</select>

```

在这个例子中，`<select>`和`<option>`元素组合在一起形成一个复杂的输入控件，许多用户都很熟悉。`<select>`保持其自身的内部状态，即用户当前选择的`<option>`，并提供高级行为，如键盘输入，以改变选择的`<option>`。

## 如何实现复合组件

首先，确保复合组件是适当的 API 设计。像任何编程范例一样，复合组件也有其合理的权衡。一般来说，任何存在隐含共享状态的模式——或者“神奇的”或不容易被发现的行为——如果没有经过精心设计，都可能导致令人头疼的问题。

在设计复合组件之前，您需要问自己一些问题:

*   使用组件的程序员真的需要将两个(或更多)组件组合在一起才能达到预期的结果吗？或者用更简单的单组件设计和适当的输入或道具能达到同样的结果吗？
*   组件之间的交互和角色对使用它们的程序员来说是否清晰直观？

仔细考虑新组件的各种 API，并咨询同事他们更喜欢使用哪种 API。

下面是一些非常适合复合组件模式的常见 UI 范例:

*   一种高级表格组件，允许组件用户不仅提供表格数据，还提供自定义排序或过滤行为
*   一个可搜索的下拉组件，该组件的用户将提供选项

## React 中的复合组件架构

为了在 React 中实现我们的示例复合组件，我们将利用许多 React APIs，因此每个 API 的基础知识将会有所帮助:

*   [挂钩](https://reactjs.org/docs/hooks-intro.html)和功能部件
*   用于在组件之间共享状态的上下文 API

### 构建复合组件

我们将构建一个复合组件的草图，允许用户从列表中选择多个选项，如果需要，可以使用文本字段来过滤列表。

![Selecting Items From a List](img/ad7a6f94b190c266a782a6d6df5b6857.png)

### 实现子组件和一个`App`组件

为了创建我们的复合组件，我们将实现两个子组件:

*   `EnhancedMultiSelect`:这将是外包装部件，并将具有以下作用:
    *   封装子选项的状态
    *   呈现过滤选项的文本字段
*   `EnhancedMultiSelectOption`:该组件将表示单个可选选项，并将具有以下作用:
    *   基于用户交互读取和写入选择状态
    *   读取过滤器值，并在适当时将其自身从渲染中排除

最后，我们还将实现一个使用我们的复合组件的`App`组件，来开发和测试它的 API。

`EnhancedMultiSelect`

首先，我们将创建一个[上下文](https://reactjs.org/docs/context.html)，用于在父组件和子组件之间共享状态。

```
export const EnhancedMultiSelectContext = createContext();

```

接下来，我们将为组件实现签名，这将需要三个道具:

*   `children`:渲染树中的子节点，将包括可选选项和 UI 所需的任何其他标记。我们不关心选项嵌套有多深，也不关心树中是否有其他组件，允许使用我们组件的工程师灵活使用
*   `value`:表示所选选项的字符串的`Set`
*   `onChange`:每当选择改变时，我们将用一个新的`Set`调用的函数

```
export default function EnhancedMultiSelect({ children, value, onChange }) {}

```

现在我们将实现组件的主体。首先，我们将使用一个`useState`钩子来跟踪用户在过滤器文本输入中输入的查询。

```
const [filter, setFilter] = useState('');

```

接下来，我们将返回 React 将为我们呈现的组件。我们将首先为我们之前设置的上下文设置一个提供者，并使用它来提供稍后将被选项使用的一些值:

*   一个`isSelected`函数，接受一个字符串键并返回给定的键是否出现在选择中
*   一个`setSelected`函数，它获取一个键，并根据指示从选择中添加或删除该键
*   过滤器文本输入的当前值

我们还将在上下文提供程序中呈现我们的过滤器文本输入和组件子组件。以下是`EnhancedMultiSelect`的完整源代码:

```
import { createContext, useState } from 'react';

export const EnhancedMultiSelectContext = createContext();

export default function EnhancedMultiSelect({ children, value, onChange }) {
  const [filter, setFilter] = useState('');
  return (
    <EnhancedMultiSelectContext.Provider
      value={{
        isSelected: key => value.has(key),
        setSelected: (key, selected) => {
          const newValue = new Set([...value]);
          if (selected) {
            newValue.add(key);
          } else {
            newValue.delete(key);
          }
          onChange(newValue);
        },
        filter,
      }}
    >
      <input
        type="text"
        placeholder="Filter options..."
        value={filter}
        onChange={evt => setFilter(evt.target.value)}
      />
      {children}
    </EnhancedMultiSelectContext.Provider>
  );
}

```

`EnhancedMultiSelectOption`

现在，我们将实现复合组件的另一半，它需要两个道具:

*   用于显示我们组件的用户想要在可选选项中呈现的任何内容
*   `value`:表示该选项的字符串；如果选择该选项，该值将包含在父`EnhancedMultiSelect`组件公开的`Set`中

```
export default function EnhancedMultiSelectOption({ children, value }) {}
```

我们在组件主体中要做的第一件事是使用父组件`EnhancedMultiSelect`提供的上下文，使用[析构赋值](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)来分离上下文以便于使用。

```
const { isSelected, setSelected, filter } = useContext(
  EnhancedMultiSelectContext,
);

```

现在我们已经从上下文中获得了用户的过滤器查询，如果它与选项的值不匹配，我们将返回`null`以不呈现任何内容:

```
if (!value.includes(filter)) {
  return null;
}

```

最后，我们将呈现复选框，并将其`checked`状态插入复合组件的选择状态，以及组件消费者想要呈现的任何子组件。以下是`EnhancedMultiSelectOption`的完整源代码:

```
import { useContext } from 'react';
import { EnhancedMultiSelectContext } from './EnhancedMultiSelect';

export default function EnhancedMultiSelectOption({ children, value }) {
  const { isSelected, setSelected, filter } = useContext(
    EnhancedMultiSelectContext,
  );
  if (!value.includes(filter)) {
    return null;
  }
  return (
    <label style={{ display: 'block' }}>
      <input
        type="checkbox"
        checked={isSelected(value)}
        onChange={evt => setSelected(value, evt.target.checked)}
      />
      {children}
    </label>
  );
}

```

`App`

为了了解它们是如何协同工作的，我们将使用我们的复合组件，并将其呈现在入口点`App`组件中:

```
import { useState } from 'react';
import EnhancedMultiSelect from './EnhancedMultiSelect';
import EnhancedMultiSelectOption from './EnhancedMultiSelectOption';

export default function App() {
  const [selection, setSelection] = useState(new Set());
  return (
    <section>
      <EnhancedMultiSelect value={selection} onChange={v => setSelection(v)}>
        <EnhancedMultiSelectOption value="apples">
          Apples
        </EnhancedMultiSelectOption>
        <EnhancedMultiSelectOption value="oranges">
          Oranges
        </EnhancedMultiSelectOption>
        <EnhancedMultiSelectOption value="peaches">
          Peaches
        </EnhancedMultiSelectOption>
        <EnhancedMultiSelectOption value="grapes">
          Grapes
        </EnhancedMultiSelectOption>
        <EnhancedMultiSelectOption value="plums">
          Plums
        </EnhancedMultiSelectOption>
      </EnhancedMultiSelect>
      <pre>
        <code>{JSON.stringify([...selection], null, 2)}</code>
      </pre>
    </section>
  );
}
```

## 角度的复合组件架构

让我们使用 [Angular](https://angular.io/) 框架构建同一个简单的复合组件。

`enhanced-multi-select.component.ts`

对于外部组件，我们将设置一个简单的模板，其中包含一个双向绑定到`filter`属性的文本输入。与 React 示例一样，我们将为选择状态创建一个输入，并在选择状态改变时创建一个输出。以下是完整的源代码:

```
import { Component, Input, Output, EventEmitter } from '@angular/core';

@Component({
  selector: 'app-enhanced-multi-select',
  template: `
    <input type="text" [(ngModel)]="filter" />
    <ng-content></ng-content>
  `,
})
export class EnhancedMultiSelectComponent {
  @Input()
  value!: Set<string>;

  @Output()
  valueChange = new EventEmitter<Set<string>>();

  filter = '';
}

```

`enhanced-multi-select-option.component.ts`

对于选项项，我们将呈现一个包装复选框和组件内容的标签，就像 React 示例中一样。我们将利用 Angular 的[依赖注入](https://angular.io/guide/dependency-injection)系统来获取对通过`constructor`传递的父`EnhancedMultiSelectComponent`实例的引用。

有了这个引用，我们可以直接评估和操作状态，并根据用户提供的过滤器字符串的值检查选项是否应该可见。下面是源代码:

```
import { Component, Input } from '@angular/core';
import { EnhancedMultiSelectComponent } from './enhanced-multi-select.component';

@Component({
  selector: 'app-enhanced-multi-select-option',
  template: `
    <label *ngIf="visible()" style="display: block">
      <input
        type="checkbox"
        [ngModel]="selected()"
        (ngModelChange)="setSelected($event)"
      />
      <ng-content></ng-content>
    </label>
  `,
})
export class EnhancedMultiSelectOptionComponent {
  constructor(private readonly select: EnhancedMultiSelectComponent) {}

  visible() {
    return this.value.includes(this.select.filter);
  }

  selected() {
    return this.select.value.has(this.value);
  }

  setSelected(selected: boolean) {
    if (selected) {
      this.select.value.add(this.value);
    } else {
      this.select.value.delete(this.value);
    }
  }

  @Input()
  value!: string;
}

```

`app.component.ts`

最后，我们将利用我们的复合组件并显示格式化的 JSON 选择数据，用于演示目的:

```
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  template: `
    <app-enhanced-multi-select [(value)]="selection">
      <app-enhanced-multi-select-option value="apples">
        Apples
      </app-enhanced-multi-select-option>
      <app-enhanced-multi-select-option value="oranges">
        Oranges
      </app-enhanced-multi-select-option>
      <app-enhanced-multi-select-option value="peaches">
        Peaches
      </app-enhanced-multi-select-option>
      <app-enhanced-multi-select-option value="grapes">
        Grapes
      </app-enhanced-multi-select-option>
      <app-enhanced-multi-select-option value="plums">
        Plums
      </app-enhanced-multi-select-option>
    </app-enhanced-multi-select>
    <pre><code>{{ selectionArray() | json }}</code></pre>
  `,
})
export class AppComponent {
  selection = new Set<string>();

  selectionArray() {
    return [...this.selection];
  }
}
```

# 结论

在这篇文章中，我们在 React 中使用[上下文 API](https://reactjs.org/docs/context.html) ，在 Angular 中使用[依赖注入](https://angular.io/guide/dependency-injection)，实现了一个可过滤的多选复合组件。

复合组件是创建一个简单的 API 来组合对于单个组件来说过于复杂的行为的一种选择。有许多可供选择的模式，例如 React 中的[“render props”](https://reactjs.org/docs/render-props.html)，对于特定的用例，应该仔细考虑每种模式的权衡。

在开发环境中运行上述示例的完整源代码可以在 [GitHub](https://github.com/mjswensen/compound-components) 上找到。

## [LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。