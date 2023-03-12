# TypeScript 3.7 中的新功能

> 原文：<https://blog.logrocket.com/new-in-typescript-3-7/>

# TypeScript 3.7 中的新功能

## 剧透:可选链接和零合并操作符是游戏规则的改变者

2019 年 10 月 15 日 3 min 阅读 859

我认为 TypeScript 3.7 版本是巨大的。几年前，我在一个 C#契约上短暂地体验了一下可选链接，我已经等了很久很久了。

## 它解决什么问题？

在 JavaScript/TypeScript 世界中，我们大多数人最大的痛点之一就是不断地检查变量或表达式中的`null`或`undefined`。下面的例子是 TypeScript 3.7，它说明了这个版本是一个怎样的游戏规则改变者。我们终于可以用这个新特性大幅减少我们必须编写的无意义代码的数量。

```
class Student {
  constructor(public name: string) {}

  discipline?: Discipline;

  printDetails() {
    return `
      name: ${this.name}
      discipline: this.discipline?.name || 'Not set';
    `;
  }
}

class Discipline {
  student?: Student;

  constructor(public name: string) { }
}

class College {
  constructor(public name: string) { }

  disciplines?: Discipline[];
}

class City {
  constructor(public name: string) { }

  college?: College;
}

class University {
  constructor(public name: string) { }

  city?: City;

  get college(): College | undefined {
    return this?.city?.college;
  }

  addDisciplines(...disciplines: Discipline[]) {
    if (!this.city?.college?.disciplines) {
      return;
    }

    this.city.college.disciplines.push(...disciplines)
  }
}

const university = new University("Queen's University Belfast");
const city = new City("Belfast");
const computerScience = new Discipline("Computer Science");
const economics = new Discipline("Economics");
const college = new College("Da College");
const student = new Student("Brian Cant");

university.city = city;
university.city.college;

university.addDisciplines(computerScience, economics);

const firstStudent = university.city?.college?.disciplines?.[0]?.student; // Student | undefined

console.log(firstStudent?.printDetails())

```

[这里有一个操场](http://www.typescriptlang.org/play/?ts=3.7-Beta&ssl=1&ssc=1&pln=70&pc=1#code/MYGwhgzhAEDKAuBXAJgUwHb2gbwLAChppgB7dCeAJ0WHhMoAoAHRAIxAEthp0wBbVAC5oFSh3QBzAJQ4AvgQJFkHCMA5NO6VAH5hAERVqN41AG4FhaIuhMxmPanhgOICAxl5LRSo8SV00AAG1kREvALCACTY8AAWKgB04ajyXqHKquqaQtBxiRlG2dpJ-KjQAD7l0ADkAHIkWBCO1eZpga1EqakEoJAwBpnGWjjWFCgY8LpwSGiYrdak5FQ0dIws7Fw8pcKi4tI40F0WvVDQAMIkICCoEmWeRIuiK-TMbJzcyTtUex6HFkqGLImCBTAaFEwAbQAuq1uvgTjAzhx4ABPEaWR7LWgvdbvLYRETfSS-OEPS7XW5TC5XG5mAhwnrgU4AVXQHAAbqhKBBkWj7sQyE9sWs3ptPoS7PtsH98AteVTefNLLcsKQabd3MJqRSylVEOg0AAzEzIdGhaA+JD+XLxEEJNSo4pqnUdGXWMDIZBgoFaNwJf0FH2oCD6QFDVDQjwhaAcQ3QBgAQjyEHt8vt5NpxUD4YgUbS3l8-ldnX+oWTqdR6fVqAS2eyKZYEFiDH9tbD9ak1gZ+DhmOg+o5XJ5qOgAF4eKgAO7QVmD7m8hgAIgAiohUBhqjBZ5z5yOAEKoECGyDwRdSVp9h1o8daadI1FLg9Hk9ni+C1UkPgseBc2BqDDAGUN5TtA3rhkuFxfogP6UHA-7oIBr49O+0CoIsn5cDAwHTmB2RLgAouhfCYUh8Ioc6tJjhOd4Zhqi56GA5y0agpF9mMsxYNh0zjJgj5iGAARnAJp7nhYA47sOKIVtexCKgQ4lDry0lVi6YlshJSkel67bAgwpBQTBf4cABqAADSoURmGibKZFLNAxrcvACA8Zx-bqYplZXk6zFZjpvrFBCAAMULFOxEymNAAD0kXcRxFRuUaJrHIKlw1iAJASAwDkUM5HHFLY4jwA4TguG4UidjZQA)，上面代码片段的工作示例。

**第 1 行**包含一个`Student`类定义，该类定义包含一个可选的`discipline`属性，该属性可能具有一个`undefined`或`null`值。在第 6**行**上有一个`printDetails`方法，它包含了一个可选属性的访问。

```
printDetails() {
  return `
    name: ${this.name}
    discipline: this.discipline?.name || 'Not set';
  `;
}
```

通过在可选属性值后放置一个`?`问号来指定可选链接，您希望在可选属性值上调用属性、方法甚至下标(稍后将详细介绍)来强制展开其值。如果在属性或方法的解包过程中发现了一个`null`值或一个`undefined`值，则返回`undefined`。无论基础值是`null`还是`undefined`，总是返回`undefined`。

下面是一些可怕的代码，在这个精致的版本发布之前，我们会写得很乏味。

```
printDetails() {
  return `
    name: ${this.name}
    discipline: this.discipline && this.discipline.name ? this.discipline.name : 'Not set';
  `;
}
```

当处理下面代码示例的第**行第 3** 行或第一个代码示例的第**行第 63** 行的深度嵌套对象时，可选链接闪亮登场:

```
university.addDisciplines(computerScience, economics);

const firstStudent = university.city?.college?.disciplines?.[0]?.student; // Student | undefined

console.log(firstStudent?.printDetails())
```

以前我们不得不做一些非常恶心的事情来访问如此深的层次结构，但是现在我们可以选择用 TypeScript 3.7 来链接我们的方式以获得更好的存在。

## 通过可选链接访问下标

您可以使用可选链接来尝试从可选值的下标中检索值，并检查该下标调用是否成功。

```
const firstStudent = university.city?.college?.disciplines?.[0]?.student;
```

`Discipline`类的实例可以有一个学生数组。当你通过可选链接访问可选值的下标时，你要把问号*放在下标的括号*之前，而不是之后。可选的链接问号总是紧跟在表达式的可选部分之后。

我认为这是巨大的，我们将编写更少的代码来检查事物的存在。我无法抑制我对这次释放的兴奋。

## 零合并算子

我之前在 C#中体验过的另一个特性是空合并操作符(`??`)，它简化了对空值或未定义值的检查。

```
const a: number | null = null;
const b = a ?? 42;

console.log(b);  // 42
```

### 那`||`呢？

第一次在 TypeScript 里看到这个，我就喊了一句‘那`||`呢？’下面是同样的例子，同样的结果。

```
const a: number | null = null;
const b = a || 42;

console.log(b);  // 42
```

这个例子的漏洞在于 JavaScript 的真真假假。

```
const a: number | null = 0;
const b = a || 42;

console.log(b);  // 42
```

不幸的是，`0`在 JavaScript 的表达式中使用时返回 false，所以`42`将到达不正确的控制台。

在这种情况下，合并操作符就来帮忙了。

```
const a: number | null = 0;
const b = a ?? 42;

console.log(b);  // 0
```

当 TypeScript 3.7 发布时，我将不再使用 shortcircuit 或`||`操作符，而是使用漂亮的`??`操作符。

## 游戏改变了

我等这些变化已经等了很长时间，我认为官方发布的[TypeScript](https://devblogs.microsoft.com/typescript/announcing-typescript-3-7-beta/)做了它通常的工作，低估了伟大的新功能。

我也讨厌他们使用的代码示例:

```
// Before
if (foo && foo.bar && foo.bar.baz) {
    // ...
}

// After-ish
if (foo?.bar?.baz) {
    // ...
}
```

我认为这说明了现实世界和 TypeScript 开发人员之间的脱节。`foo`、`bar`和`baz`是最糟糕的例子。我想不出这些虚构的财产有什么具体的东西。

我认为这个版本应该有更多的内容。它将使编写打字稿明显不那么烦人。

请在下面的评论中留下反馈。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.