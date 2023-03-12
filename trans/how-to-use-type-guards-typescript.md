# 如何在 TypeScript - LogRocket 博客中使用类型保护

> 原文：<https://blog.logrocket.com/how-to-use-type-guards-typescript/>

类型保护是一种类型脚本技术，用于获取有关变量类型的信息，通常在条件块中。类型保护是返回布尔值的常规函数，它接受一个类型，并告诉 TypeScript 是否可以缩小到更具体的范围。类型保护具有独特的属性，可以根据返回的布尔值确保测试的值是固定类型的。

TypeScript 使用一些内置的 JavaScript 操作符，如`typeof`、`instanceof`和`in`操作符，用于确定对象是否包含属性。类型保护使您能够指示 TypeScript 编译器在特定上下文中推断变量的特定类型，从而确保参数的类型是您所说的类型。

类型保护通常用于缩小类型，与功能检测非常相似，允许您检测值的正确方法、原型和属性。因此，您可以很容易地找出如何处理该值。

使用类型保护有五种主要方式:

在本文中，我们将探索上面列出的四种方法。我们开始吧！

## `instanceof`型防护罩

[`instanceof`是一个内置的类型保护](https://www.typescriptlang.org/docs/handbook/2/narrowing.html#instanceof-narrowing)，可以用来检查一个值是否是一个给定的构造函数或类的实例。使用这种类型保护，我们可以测试一个对象或值是否是从一个类中派生出来的，这对于确定一个实例类型的类型是很有用的。

`instanceof`型保护的基本语法如下:

```
objectVariable instanceof ClassName;

```

在下面的例子中，我们看到了一个`instanceof`型防护装置的例子:

```
interface Accessory {
    brand: string;
  }
  class Necklace implements Accessory{
    kind: string;
    brand: string;
    constructor(brand: string, kind: string) {    
      this.brand = brand;
      this.kind = kind;
    }
  }
  class bracelet implements Accessory{
    brand: string;
    year: number;
    constructor(brand: string, year: number) {    
      this.brand = brand;
      this.year = year;
    }
  }
  const getRandomAccessory = () =>{
    return Math.random() < 0.5 ?
      new bracelet('cartier', 2021) :
      new Necklace('choker', 'TASAKI');
  }
  let Accessory = getRandomAccessory();
  if (Accessory instanceof bracelet) {
    console.log(Accessory.year);
  }
  if (Accessory instanceof Necklace) {
    console.log(Accessory.brand);    
  }

```

上面的`getRandomAccessory`函数返回一个`Necklace`或`bracelet`对象，因为它们都实现了`Accessory`接口。`Necklace`和`bracelet`的构造函数签名是不同的，并且`instanceof`类型守卫比较这两个构造函数签名来有效地确定类型。

## `typeof`型防护罩

[`typeof`类型保护](https://www.typescriptlang.org/docs/handbook/2/typeof-types.html)用于确定变量的类型。`typeof`型护板据说非常有限而且很浅。它只能确定 JavaScript 识别的以下类型:

*   `boolean`
*   `string`
*   `bigint`
*   `symbol`
*   `undefined`
*   `function`
*   `number`

对于这个列表之外的任何东西，`typeof`类型的守卫简单地返回`object`。

`typeof`型保护可以用以下两种方式编写:

```
typeof v !== "typename"
#or 
typeof v === "typename"

```

`typename`可以是`string`、`number`、`symbol`或`boolean`。

在下面的例子中，`StudentId`有一个`string | number`类型的联合参数条目。我们看到，如果变量是一个`string`，就会打印出`Student`，如果是一个`number`，就会打印出`Id`。`typeof`类型保护帮助我们从`x`中提取类型:

```
function StudentId(x: string | number) {
    if (typeof x == 'string') {
        console.log('Student');
    }
    if (typeof x === 'number') {
        console.log('Id');
    }
}
StudentId(`446`); //prints Student
StudentId(446); //prints Id

```

## `in`型防护罩

[`in`类型守卫](https://www.typescriptlang.org/docs/handbook/2/narrowing.html#the-in-operator-narrowing)检查一个对象是否有特定的属性，以此来区分不同的类型。它通常返回一个布尔值，表明该属性是否存在于该对象中。它用于其缩小功能，以及检查浏览器支持。

`in`型保护的基本语法如下:

```
propertyName in objectName

```

在下面的例子中，`in`类型的守卫检查属性`house`是否存在。如果存在，返回布尔值`true`，如果不存在，返回`false`。

```
"house" in { name: "test", house: { parts: "door" } }; // => true
"house" in { name: "test", house: { parts: "windows" } }; // => true
"house" in { name: "test", house: { parts: "roof" } }; // => true
"house" in { name: "test" }; // => false
"house" in { name: "test", house: undefined }; // => true

```

另一个类似的`in`型防护装置工作原理的例子如下所示:

```
interface Pupil {
    ID: string;
  }
  interface Adult {
    SSN: number;
  }
  interface Person {
    name: string;
    age: number;
  }
  let person: Pupil | Adult | Person = {
    name: 'Britney',
    age: 6
  };
  const getIdentifier = (person: Pupil | Adult | Person) => {
    if ('name' in person) {
      return person.name;
    }
    else if ('ID' in person) {
      return person.ID
    }
    return person.SSN;
  }

```

## 相等缩小型防护

相等缩小检查表达式的值。要使两个变量相等，这两个变量必须属于同一类型。如果变量的类型未知，但它等于另一个具有精确类型的变量，则 Typescript 将使用已知变量提供的信息缩小第一个变量的类型:

```
function getValues(a: number | string, b: string) {
    if(a === b) {
        // this is where the narrowing takes place. narrowed to string
        console.log(typeof a) // string
    } else {
        // if there is no narrowing, type remains unknown
        console.log(typeof a) // number or string
    }
}

```

如果变量`a`等于变量`b`，那么两者必须具有相同的类型。在这种情况下，Typescript 将其缩小到字符串。如果不缩小范围，`a`的类型仍然不清楚，因为它可能是数字，也可能是字符串。

## 带有谓词的自定义类型保护

创建自定义类型保护通常是使用类型保护的一个强大选项。当你通过自己编写来创建一个自定义类型的守卫时，你可以检查的东西是没有限制的。但是，如果自定义类型保护写得不正确，它会带来很多错误。所以，精准是关键。

自定义类型防护的示例如下所示:

```
interface Necklace{
    kind: string;
    brand: string;
}
interface bracelet{
    brand: string;
    year: number;
}
type Accessory = Necklace | bracelet;

const isNecklace = (b: Accessory): b is Necklace => {
    return (b as Necklace).kind !== undefined
}
const Necklace: Accessory = {kind: "Choker", brand: "TASAKI"};
const bracelet: Accessory = {brand: "Cartier", year: 2021};
console.log(isNecklace(bracelet)) //Logs false
console.log(isNecklace(Necklace)) //Logs true

```

在上面的代码中，类型谓词`b is Necklace`将使 TypeScript 将类型简化为`Necklace`，而不是只返回一个布尔值。

## 结论

TypeScript 类型保护有助于确保类型的值，改善整体代码流。在本文中，我们回顾了 TypeScript 中几种最有用的类型保护，并通过几个例子来了解它们的实际应用。

大多数情况下，您的用例可以使用`instanceof`型防护、`typeof`型防护或`in`型防护来解决，但是，您可以在绝对必要时使用定制型防护。

我希望你喜欢这篇文章！如果你有任何问题，一定要留下评论。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.