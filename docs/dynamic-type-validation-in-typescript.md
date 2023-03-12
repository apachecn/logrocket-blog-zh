# TypeScript 中的动态类型验证

> 原文：<https://blog.logrocket.com/dynamic-type-validation-in-typescript/>

毫无疑问，TypeScript 已经在 JavaScript 社区中被广泛采用，它提供的一个巨大好处是对我们代码中的所有变量进行类型检查。它将检查在给定变量类型的情况下，是否可以对变量执行任何操作。

大多数人认为，通过使用 TypeScript 作为他们的应用程序语言，他们可以“避免”任何空性错误，就像经典的“undefined 不是函数”或我最喜欢的“无法读取 undefined 的属性 X”这个假设是错误的，最好的证明方法是用代码！

我在柏林会议的打字稿上做了一个关于这个话题的演讲。这篇文章和 [the talk](https://youtu.be/cn6i5Av-B8Q?t=1884) 涵盖了相同的内容，所以你可以使用其中任何一个来了解这个话题！

## 为什么打字稿不会总是覆盖你🕵

下面的示例不会出现任何 TypeScript 错误。

```
// Typescript definition
type ExampleType = {
  name: string,
  age?: number,
  pets: {
    name: string,
    legs: number,
  }[],
};

// communicates with external API
const fetchData = (): Promise<ExampleType> => {};

const getBiped = async () => {
  const data = await fetchData();
  console.log(data);
  // { name: 'John' }
  return data.pets.find(pet => pet.legs === 2); // Boom!
};

```

该代码片段包含:

*   `ExampleType`–具有两个必需属性`name`和`pets`以及一个可选属性`age`的类型定义。属性`pets`是一个带有`name`和`legs`的对象数组，两者都是必需的
*   `fetchData`–从外部端点检索数据的功能
*   `getBiped`–另一个函数将调用`fetchData`，迭代`pets`，并且只返回带有两个`legs`的`pets`

那么，为什么我的脚本在执行时会失败呢？原因是因为外部 API 正在返回一个内部不包含`pets`的对象，然后当你试图执行`data.pets.find()`时，就会收到错误`Uncaught ReferenceError: Cannot read property 'find' of undefined`。

在[官方 React 文档](https://reactjs.org/docs/static-type-checking.html)中，你可以找到一个关于 TypeScript 的非常好的定义:

> TypeScript 是微软开发的一种编程语言。它是 JavaScript 的类型化超集，包括它的编译器。作为一种类型化语言，TypeScript 可以在构建时捕捉错误和 bug，远在你的应用程序上线之前。

有了这个定义，就有可能提出一个新的假设:

TypeScript 执行静态类型验证。开发人员应该利用动态验证。

## 你需要验证一切吗？🤔

简单来说，不是！🎉

从开发和性能的角度来看，检查应用程序中的所有变量都非常耗时。一个很好的经验法则是:

> 验证应用程序的所有外部来源。

外部来源是外部的或无法访问您的应用程序的任何东西。一些例子:

*   API 响应
*   文件内的内容
*   来自用户的输入
*   无类型库

一个应用程序将总是呈现至少一个外部源，否则，它很可能是无用的。因此，让我们看看如何在 TypeScript 中为对象编写验证。

为了简单起见，最初的代码片段将被认为是*的基础*，在上面，我将展示如何实现每个验证方法。

## 手动验证

最基本的验证是一组检查结构是否是预期结构的条件。

```
const validate = (data: ExampleType) => {
  if (!data.pets) return false;
  // perform more checks

  return true;
};

const getBiped = async () => {
  const data = await fetchData();
  console.log(data);
  // { name: 'John' }

  if (!validate(data))
    throw Error('Validation error: data is not complete ...');

  return data.pets.find(pet => pet.legs === 2);
};

```

如您所见，已经定义了一个新函数，名为`validate`，它接收一个`ExampleType`对象作为参数，用它来检查属性`pets`是否已定义。如果不是，它将返回`false`，这将最终抛出一个带有描述消息的错误。否则，它将继续执行，现在，当评估`data.pets.find`时，它不会抛出一个错误。

请注意，`validate`函数的实现非常简单，并且还有进行更多检查的空间，例如:

*   `name`应该存在
*   `name`应该是一个`string`
*   如果`age`存在，应该是一个`number`
*   `pets`应该是`objects`的一个`array`
*   每个`pet`物体都应该有道具`name`和`legs`

您添加的检查越多，您的应用程序就越健壮——但是您也需要投入更多的时间。

这种方法的优点是:

*   **不需要外部库**:只需要纯打字稿
*   **以业务为中心的**:您可以在这些验证器中添加任何业务逻辑。例如，如果`propertyB`存在，您可以检查`propertyA`不应该存在

它也存在一些缺点:

*   **手工工作**:每个验证都必须手工编码，这可能非常耗时
*   **代码重复**:在本例中，`ExampleType`已经定义了有一个`pets`属性，并且是必需的。但是同样，在验证代码中，您仍然应该检查它是否为真
*   **漏洞空间**:在之前的代码中，有很多“漏洞”或需要改进的地方

## 使用验证库

为什么要重新发明轮子，对吗？这种方法包括使用任何验证库来断言对象的结构。列举一些最常用的库:

本文使用的验证库是`ajv`；然而，所有的结论也适用于其他图书馆。

```
const Ajv = require('ajv');
const ajv = new Ajv();

const validate = ajv.compile({
  properties: {
    name: {
      type: 'string',
      minLength: 3,
    },
    age: { type: 'number' },
    pets: {
      type: 'array',
      items: {
        name: {
          type: 'string',
          minLength: 3,
        },
        legs: { type: 'number' },
      },
    },
  },
  required: ['name', 'pets'],
  type: 'object',
});

const getBiped = async () => {
  const data = await fetchData();
  console.log(data);
  // { name: 'John' }
  if (!validate(data)) {
    throw Error('Validation failed: ' + ajv.errorsText(validate.errors));
    // Error: Validation failed: data should have required property 'pets'
  }

  return data.pets.find(pet => pet.legs === 2);
};

```

许多验证库强迫您定义一个`schema`，您可以在其中描述要评估的结构。给定该模式，您可以创建将在您的代码中使用的验证函数。

您的架构声明将始终取决于您正在使用的库；因此，我总是建议查看官方文件。在`ajv`的情况下，它强迫你在一个对象样式中声明，其中每个属性必须提供它的`type`。也可以为这些值设置自定义检查器，比如为任何`array`或`string`设置`minLength`。

该方法提供:

*   **创建验证器和检查**的标准化方法:`schema`背后的想法是只有一种方法来检查应用程序内部的特定条件——特别是在 JavaScript 中，有许多方法可以完成相同的任务，例如检查数组的`length`。这种品质对于改善团队内部的沟通和协作非常重要
*   **错误报告的改进**:如果某个属性不匹配，库会以一种人性化的方式通知你是哪一个，而不仅仅是打印堆栈跟踪

这种创建验证的新方法存在以下缺点:

*   **引入新语法**:当一个团队决定添加一个新的库时，理解整个代码库变得更加困难。每个贡献者都必须了解验证器模式，才能理解如何对它进行修改
*   **验证器和类型不同步**:对`schema`和`ExampleType`的定义是断开的，这意味着每次你在`ExampleType`内部做了更改，你都要手动地在`schema`内部反映出来。根据您拥有的验证器的数量，这个任务可能会非常乏味

关于保持验证器和类型同步的一个小评论:一些开源项目解决了这个问题，例如[JSON-schema-to-typescript](https://www.npmjs.com/package/json-schema-to-typescript)，它可以从现有的模式生成类型定义。那这个就不算问题了。

## 动态类型验证器

这就是我想谈论的方法，它代表了关于如何创建验证器和保持类型同步的范式的变化。

在另外两个方法中，验证器和类型可以被视为不同的实体:验证器将接受传入的对象并检查其属性，而类型静态地属于该对象。结合这两个实体，结果是一个经过验证的类型对象。

动态类型验证允许类型从其定义中生成验证器。现在它们是相关的——验证器完全依赖于类型，防止了结构之间的任何不匹配。

### 验证器的生成

为了生成这些验证器，我找到了一个叫做`[typescript-json-validator](https://github.com/ForbesLindesay/typescript-json-validator)`的惊人的开源项目，由 [@ForbesLindesay](https://twitter.com/ForbesLindesay) 制作。repo 描述声明其目标是“使用 JSON 模式和 AJV 为任何 TypeScript 类型自动生成验证器”

对于测试，让我们重用`ExampleType`定义，它现在已经被移动到`types`文件夹中的一个单独的文件中。

```
// src/types/ExampleType.ts

type ExampleType = {
  name: string;
  age?: number;
  pets: {
    name: string;
    legs: number;
  }[];
};

```

这个库公开了一个方便的 CLI，可以从任何地方调用，给定一个文件路径和类型名称，它将在与文件相同的位置生成一个带有验证器代码的新文件。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
> npx typescript-json-validator src/types/ExampleType.ts ExampleType
# ExampleType.validator.ts created!

```

得到的验证器可能是一个很长的文件，所以让我们一点一点地看一下。

### 1.创建`ajv`实例

它还为`ajv`设置了一些默认配置。

```
/* tslint:disable */
// generated by typescript-json-validator
import { inspect } from 'util';
import Ajv = require('ajv');
import ExampleType from './ExampleType';

export const ajv = new Ajv({
  allErrors: true,
  coerceTypes: false,
  format: 'fast',
  nullable: true,
  unicode: true,
  uniqueItems: true,
  useDefaults: true,
});

ajv.addMetaSchema(require('ajv/lib/refs/json-schema-draft-06.json'));

export { ExampleType };

```

### 2.`schema`由下式定义

这是这种方法的关键。

```
// Definition of Schema
export const ExampleTypeSchema = {
  $schema: 'http://json-schema.org/draft-07/schema#',
  defaultProperties: [],
  properties: {
    age: {
      type: 'number',
    },
    name: {
      type: 'string',
    },
    pets: {
      items: {
        defaultProperties: [],
        properties: {
          legs: {
            type: 'number',
          },
          name: {
            type: 'string',
          },
        },
        required: ['legs', 'name'],
        type: 'object',
      },
      type: 'array',
    },
  },
  required: ['name', 'pets'],
  type: 'object',
};

```

### 3.使用生成的`schema`导出验证函数

它还负责在出现错误时抛出异常。

```
export type ValidateFunction<T> = ((data: unknown) => data is T) &
  Pick<Ajv.ValidateFunction, 'errors'>;
export const isExampleType = ajv.compile(ExampleTypeSchema) as ValidateFunction<
  ExampleType
>;

export default function validate(value: unknown): ExampleType {
  if (isExampleType(value)) {
    return value;
  } else {
    throw new Error(
      ajv.errorsText(
        isExampleType.errors!.filter((e: any) => e.keyword !== 'if'),
        { dataVar: 'ExampleType' },
      ) +
        '\n\n' +
        inspect(value),
    );
  }
}

```

要使用验证器，您只需要从各自的路径导入并调用它。请注意，该函数已经在检查对象内部是否有任何错误；因此，没有必要在这里添加一个`if`语句，使代码更加简洁。

```
import validate from 'src/types/ExampleType.validator';

const getBiped = async () => {
  const data = validate(await fetchData());

  return data.pets.find(pet => pet.legs === 2);
};

```

### 打字稿❤️ `ajv`

这个库使用`ajv`来创建验证器函数，这意味着您可以利用它提供的所有优秀特性，比如类型的自定义验证。

让我们为`ExampleType`创建一个新的定义类型。

```
interface ExampleType {
  /**
   * @format email
   */
  email?: string;
  /**
   * @minimum 0
   * @maximum 100
   */
  answer: number;
}

```

在每个属性的上方，你会发现一些注释在注释括号内。当库生成最终模式时，这些将被翻译成`ajv`规则。这是结果:

```
export const ExampleTypeSchema = {
  $schema: 'http://json-schema.org/draft-07/schema#',
  defaultProperties: [],
  properties: {
    answer: {
      maximum: 100,
      minimum: 0,
      type: 'number',
    },
    email: {
      format: 'email',
      type: 'string',
    },
  },
  required: ['answer'],
  type: 'object',
};

```

属性`answer`现在提供了另外两个属性来检查`number`是否在 0 和 100 之间。在`email`的情况下，它将检查`string`值是否属于有效的电子邮件地址。

因为这些注释被包装在注释中，所以它们不会与 TypeScript 编译器发生任何冲突。

## 让它成为你工作流程的一部分

这种方法基于这样的想法，即开发人员将运行 CLI 命令并生成验证器；否则，有可能该模式是用该类型的旧版本生成的，这可能会导致不匹配。

修复这个问题非常简单:您只需添加一个脚本，该脚本将在您的代码运行之前执行。你可以称它为`prebuild`或`prestart`，这就是你的`package.json`可能的样子:

```
{
  "scripts": {
    "prebuild": "typescript-json-validator src/types/ExampleType.ts ExampleType",
    "start": "yarn prebuild && ts-node start.ts",
    "build": "yarn prebuild && tsc"
  }
}

```

最后一个建议:忽略项目中的任何`validator.ts`文件。将这些文件提交到您的存储库中是没有意义的，因为它们将在您每次启动项目时生成。

## 我使用这种方法的经验🙋‍♂️

大约两个月前，我开源了我的一个副业项目`[gatsby-starter-linkedin-resume](https://github.com/EmaSuriano/gatsby-starter-linkedin-resume)`。

总之，这是一个 [Gatsby starter](https://www.gatsbyjs.org/docs/starters/) ，它可以使用 [LinkedIn 爬虫](https://github.com/linkedtales/scrapedin)从 LinkedIn 检索你的信息，并使用 [JSON Resume](https://jsonresume.org/) 从中生成 HTML 和 PDF 简历。

该项目呈现两个主要流程:

1.  创建简历信息:您将被要求输入您的链接凭据，然后一个爬虫将打开一个新的浏览器，读取您的配置文件值，最后将所有这些信息保存在您目录下的一个 JSON 文件中。之后，项目将把从爬虫中提取的数据转换成 Json Resume 的结构。
2.  构建项目:一旦简历信息得到处理，Gatsby 就可以用它生成 HTML 和 PDF。

在本文的开始，我提到验证您的外部资源是明智的。对于这个项目，它们是:

1.  来自 LinkedIn 爬虫的数据:当与爬虫打交道时，你应该总是非常小心他们的结果，因为它与他们获取数据的网站高度相关。在网站发生变化的情况下，来自爬虫的输出可以被改变。
2.  **包含简历信息的本地文件**:该项目允许您手动更改简历内容，以防您想跳过简历信息的创建而自己创建。如果简历数据的结构错误，JSON Resume 将无法正确生成简历。

以下是每种情况的类型定义:

```
interface LinkedInSchema {
  contact: ContactItem[];
  profile: ProfileData;
  positions: LinkedInPosition[];
  educations: LinkedInEducation[];
  skills: Skill[];
  courses: Course[];
  languages: LinkedInLanguage[];
  projects: LinkedInProject[];
}

interface JsonResumeSchema {
  basics: JsonResumeBasics;
  work: JsonResumeWork[];
  volunteer?: JsonResumeVolunteer[];
  education: JsonResumeEducation[];
  awards?: JsonResumeAward[];
  publications?: JsonResumePublication[];
  skills?: JsonResumeSkill[];
  languages?: JsonResumeLanguage[];
  interests?: JsonResumeInterest[];
  references?: JsonResumeReference[];
  projects?: JsonResumeProject[];
}

```

这两种类型在变量名方面有相似之处，但它们的内部结构不同。这就是为什么有必要在第一个流中从一个结构转换到另一个结构。

在我设置我的项目从这些类型生成验证器之后，检查传入对象的结构是一件非常容易的任务。

### 爬虫结果的验证

```
// src/index.ts
import { RESUME_PATH, LINKED_IN_PATH } from './utils/path';
import validateLinkedInSchema from './types/LinkedInSchema.validator';
import { saveJson, readJson } from './utils/file';
import { inquireLoginData, getLinkedInData } from './utils/linkedin';

// ❗️❗️ IMPORT OF THE VALIDATOR  ❗️❗️
import mapLinkedInToJSONResume from './utils/mapLinkedInToJSONResume';

export const main = async ({ renew }) => {
  if (renew || !readJson(LINKED_IN_PATH)) {
    const credentials = await inquireLoginData();
    const linkedInData = await getLinkedInData(credentials);

    saveJson(LINKED_IN_PATH, linkedInData);
  }

  // ❗️❗️ VALIDATION IN ACTION ❗️❗️
  const linkedInParsed = validateLinkedInSchema(readJson(LINKED_IN_PATH));

  const jsonResumeData = mapLinkedInToJSONResume(linkedInParsed);
  saveJson(RESUME_PATH, jsonResumeData);
};

```

### 简历信息的验证

```
// gatsby-config.js
const { existsSync } = require('fs');

// ❗️❗️ IMPORT OF THE VALIDATOR  ❗️❗️
const {
  default: validateJsonResume,
} = require('./lib/types/JsonResumeSchema.validator');

if (!existsSync('./resume.json')) {
  throw new Error(
    'Please run "yarn generate-resume" to generate your resume information.',
  );
}

// ❗️❗️ VALIDATION IN ACTION ❗️❗️
const resumeJson = validateJsonResume(require('./resume.json'));

module.exports = {
  plugins: [
    {
      resolve: 'gatsby-theme-jsonresume',
      options: {
        resumeJson,
      },
    },
    'gatsby-plugin-meta-redirect',
  ],
};

```

## 结束语🗣

总而言之，我创建了这个表来比较这三种方法。动态类型方法吸取了其他方法的优点，是验证对象的推荐方法。

| **接近** | **没有附加语法** | **验证器和类型同步** | **标准化** |
| 指南 | ✅ | ❌ | ❌ |
| 图书馆 | ❌ | ❓ | ✅ |
| Dynamic types | ✅ | ✅ | ✅ |

如果您正在使用 TypeScript 代码库，我建议您尝试一下这种验证对象的新方法。它很容易设置，如果你发现它没有用，从代码库中删除它就像从你的文件中删除一个`import`一样简单。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.