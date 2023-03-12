# 使用 Next.js 和 Netlify 进行表单验证

> 原文：<https://blog.logrocket.com/form-validation-next-js-netlify/>

构建表单是许多软件应用程序的关键。表单通常用于从用户那里收集数据。

随着 JAMStack 技术和无服务器方法的现代增强，过去需要后端的大多数事情现在可以完全在前端或通过 API 处理。但有时这还不够，这就是无服务器功能发挥作用的地方。

在本文中，我们将构建完全无服务器的表单。我们将使用 [Netlify Lambda 函数](https://www.netlify.com/products/functions/)，它允许我们在几乎没有额外配置的情况下运行服务器端代码。 [Netlify 与 Next.js](https://blog.logrocket.com/choosing-best-nextjs-hosting-platform/) 很好地结合在一起。

***注意，您也可以通过简单的 [Netlify 表单](https://www.netlify.com/products/forms/)提交简单的表单，而不需要 Netlify 功能。***

 **## 什么是无服务器功能？

无服务器功能是一段可以按需执行的代码。

无服务器功能将扩展我们的应用程序，因为它们不需要 24/7 的运行时间。本质上，我们通过仅利用必要的计算过程来使用按需方法。

我们将展示如何使用 Netlify Lambda 函数来整合构建无服务器表单的能力。让我们投入进去吧！

## 项目设置

在这个项目中，我们将使用相当多的库来帮助我们向服务器提交所需的数据。我们将使用下面的命令创建一个空白的 Next.js starter 项目:

```
npx [email protected] next-netlify-forms --typescript

```

我们用名为`next-netlify-forms`的 TypeScript 创建了一个空白的 Next 项目。我们将添加几个依赖项，开始构建被称为无服务器 Netlify 函数的经过验证的表单。

下面是我们将要安装的软件包的代码:

```
npm i -D react-hook-form yup  @hookform/resolvers 
tailwindcss postcss autoprefixer 
npm i  @types/node ts-node  --save-dev

```

我们使用 [React Hook Form](https://react-hook-form.com/) 进行客户端表单验证， [Yup](https://github.com/jquense/yup) 作为我们的模式验证器，并且我们将使用 [TailwindCSS](https://tailwindui.com/) 为我们的 UI 增添风格。

让我们从定义数据结构的类型开始:

```
type formData = {
 fullName: string;
 companyEmail: string;
 phoneNumber: string;
 companyWebsite: string;
 companySize: string;
 acceptTerms: boolean;
};

```

我们的表单中会有几个字段，最终提交给 MongoDB Atlas 中的 [MongoDB 数据库实例。MongoDB 有一个慷慨的免费层可供试验。](https://blog.logrocket.com/mern-stack-tutorial/)

## 用“是”验证

现在我们将使用 Yup 为我们的数据创建一个模式验证器。这将有助于我们摆脱不想要的数据保存在我们的数据库。通过预期我们需要的来自用户的数据类型，Yup 就派上了用场。

```
 const validateSchema = Yup.object().shape({
   fullName: Yup.string().required('Full name is required'),
   companyEmail: Yup.string()
     .email('Invalid email')
     .required('Email is required'),
   phoneNumber: Yup.string()
     .required('Phone number is required')
     .min(7, 'Phone must be at least 7 numbers')
     .max(12, 'UserPhonename must not exceed 12 characters'),
   companyWebsite: Yup.string().url('Invalid website URL'),
   companySize: Yup.string().required('Company size is required'),
   acceptTerms: Yup.boolean().oneOf(
     [true],
     'You must accept the terms and conditions'
   ),
 });

```

太好了，我们已经定义了表单验证！让我们回顾一下我们要求的字段及其含义:

*   fullName:必需的字符串
*   companyEmail:必须是电子邮件地址的字符串
*   phoneNumber:最少 7 个字符、最多 12 个字符的字符串
*   companyWebsite:应该是 URL 的可选字符串
*   companySize:必需的字符串，应为数字
*   acceptTerms:必选的布尔值

现在是我们加入`react-hook-form`的好时机。它将为我们提供一个方便的函数来注册，调用一个`onSubmit`处理程序，设置表单字段的值，并跟踪错误验证。

```
import { useForm } from 'react-hook-form';
import { yupResolver } from '@hookform/resolvers/yup';
import * as Yup from 'yup';

 const {
   register,
   handleSubmit,
   setValue,
   formState: { errors },
 } = useForm<formData>({
   mode: 'onChange',
   resolver: yupResolver(validateSchema),
 });

```

`register()`方法允许我们注册一个元素并应用适当的验证规则。如果验证成功，`handleSubmit()`函数将接收表单数据。

在这种情况下，我们使用`formState`来更容易地返回表单错误，并使用`setValues`来跟踪用户在表单字段中写入的值。

## 创建必要的表单域

让我们用各自的表单域创建标记。我们还需要跟踪用户输入该表单的数据，并在以后调用我们的无服务器函数。

```
<form
       action="#"
       method="POST"
       onSubmit={handleSubmit(onSubmit)}
     >
       <input type="hidden" name="remember" defaultValue="true" />
       {/* name field */}
       <div>
         <label
           htmlFor="fullName"
         >
           Full name
         </label>
         <input
           type="text"
           {...register('fullName')}
           id="fullName"
           aria-describedby="nameHelp"
         />
         {errors.fullName && (
           <small
             id="emailHelp"
           >
             Name is a required field
           </small>
         )}
       </div>

       {/* company email field */}
       <div className="form-group mb-4">
         <label
           htmlFor="companyEmail"
         >
           Company email
         </label>
         <input
           type="email"
           {...register('companyEmail')}
           id="companyEmail"
           aria-describedby="emailHelp"
         />
         {errors.companyEmail && (
           <small
             id="emailHelp"
           >
             Email is a required field
           </small>
         )}
       </div>

       {/* phone number field */}
       <div className="form-group mb-4">
         <label
           htmlFor="phoneNumber"
         >
           Phone number
         </label>
         <input
           type="number"
           {...register('phoneNumber')}
           id="phoneNumber"
           aria-describedby="numberHelp"
         />
         {errors.phoneNumber && (
           <small
             id="emailHelp"
           >
             Phone number is required field
           </small>
         )}
       </div>

       {/* company website optional field */}

       <div>
         <label
           htmlFor="companyWebsite"
         >
           Website
         </label>
         <input
           type="text"
           {...register('companyWebsite')}
           id="companyWebsite"
           aria-describedby="websiteHelp"
         />
         {errors.companyWebsite && (
           <small
             id="websiteHelp"
           >
             Your website is incorrect
           </small>
         )}
       </div>

       {/* company size field */}
       <div className="form-group">
         <label
           htmlFor="companySize"
         >
           Company size
         </label>
         <select
           aria-label="Select an option"
           {...register('companySize')}
           onChange={(e) =>
             setValue('companySize', e.target.value, {
               shouldValidate: true,
             })
           }
         >
           <option value={''}>Select an option</option>
           <option value="0-9">Small, 0-9 employees</option>
           <option value="10-49">Medium, 10-49 employees</option>
           <option value="50+">Large, 50+ employees</option>
         </select>
         {errors.companySize && (
           <small
             id="sizeHelp"
           >
             Select company size
           </small>
         )}
       </div>

       {/* checkbox field */}
       <div>
         <div >
           <input
             id="remember-me"
             type="checkbox"
             {...register('acceptTerms')}
           />
           <label
             htmlFor="remember-me"
           >
             I hereby confirm all the information provided is true and
             accurate.
           </label>
         </div>
         {errors.acceptTerms && (
           <small
           >
             Accept our terms and conditions
           </small>
         )}
       </div>

         <button
           type="submit"
         >
           Get in touch
         </button>
     </form>

```

在我们制作好表单字段的框架后，我们现在可以创建一个`onSubmit`处理程序，它将调用我们创建的无服务器函数。这个函数接受数据作为参数，它是我们之前定义的对象类型`formData`。它使用 Fetch API 调用 API，并将表单数据作为响应体查询数据发送。

```
 const onSubmit = async (data: formData) => {
   try {
     const response = await fetch(
       'http://localhost:8888/.netlify/functions/formSubmit',
       {
         method: 'POST',
         body: JSON.stringify({
           query: data,
         }),
       }
     );
     console.log(response, 'Form submitted successfully');
   } catch (err) {
     console.log(err);
   }
   finally{
     setValue('fullName', '');
     setValue('companyEmail', '');
     setValue('phoneNumber', '');
     setValue('companyWebsite', '');
     setValue('companySize', '');
     setValue('acceptTerms', false);
   }
 };

```

请注意，我们当前正在使用尚未创建的 URL `[http://localhost:8888/.netlify/functions/formSubmit](http://localhost:8888/.netlify/functions/formSubmit)`调用位于`localhost`的端点。这是我们将在应用程序内部创建的无服务器端点。

为了进入网络函数，我们需要在本地测试一下。这是因为我们不需要在我们的网络服务器上部署和测试应用程序。

```
npm i @netlify/functions

```

安装了这个之后，我们就可以开始编写我们的无服务器函数了，这个函数将在我们的 Netlify 服务器上执行。默认情况下， [Netlify 会在你的项目根目录下的`netlify/functions`文件夹中寻找你的无服务器函数](https://blog.logrocket.com/firebase-vs-netlify-which-one-is-right-for-you/)。

为此，我们需要创建两个文件。第一个是`formSubmit.ts`，在一个函数目录中。另一个是用于网络配置的`netlify.toml`文件。我们将在下面创建文件夹和文件结构。

```
.
├── functions
 ├── formSubmit.ts
├── src
├── public
├── ...
└── package.json
└── netlify.toml

```

`netlify.toml`文件包含我们的无服务器函数执行所需的函数`congif`。它在`functions`文件夹中，以后可以在`build`文件夹中访问。

```
[build]
 functions = 'functions'
 public = 'build'

```

## 连接到数据库

让我们编写一个函数来帮助我们连接 MongoDB 数据库，`netlify-forms`。我们现在可以将它导入到我们创建的 Netlify 函数中。

```
const MongoClient = require('mongodb').MongoClient;
const client = new MongoClient(process.env.MONGODB_URI, {
 useNewUrlParser: true,
 useUnifiedTopology: true,
});

async function connect() {
 if (!client.connect()) await client.connect();
 const db = client.db("netlify-forms");
 return { db, client };
}

export { connect };

```

抓紧了，我们快到了！我们现在将创建我们的无服务器函数，它将接受事件作为参数。这是我们可以在对象内部接受查询和其他数据的地方。

## 创建网络功能

是时候创建我们的 Netlify 函数来处理我们传入的表单数据了。这些函数对公众来说是隐藏的，但是它们的交互方式不同于其他 API 服务！

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

这些功能是同步的，最大超时时间为 10 秒。如果我们需要添加更多的时间，我们可以通过在其名称中添加一个`-background`扩展名来更改这个普通的函数。

```
import { Handler } from "@netlify/functions";
import { connect } from "../utils/database";

const handler: Handler = async (event: any) => {
 const { query } = JSON.parse(event.body);
 const { fullName, companyEmail, phoneNumber, companyWebsite, companySize } = query;
 const { db } = await connect();
 await db.collection("contact").insertOne({
   contacts: {
     Name: fullName,
     Email: companyEmail,
     Phone: phoneNumber,
     Website: companyWebsite,
     Size: companySize,
   },
   createdAt: new Date(),
 });
 return {
   statusCode: 200,
   body: JSON.stringify({ message: `ok` })
 }
};

export { handler };

```

我们析构了解析的查询，并访问了作为参数事件传递给函数的所有数据。

当异步函数返回一个承诺时，我们最终返回一个状态代码 200，并调用我们的 Mongo 数据库，从我们创建的`connect`函数访问它。这是我们存储所有表单数据的地方！

您可以查看 Netlify 文档，获得可用事件触发器的[列表。](https://docs.netlify.com/functions/trigger-on-events/#available-triggers)

有了所有这些，我们现在可以发送所需的和经过验证的数据来调用无服务器函数了！这将把数据保存到我们的数据库中。

## 结论

通过可自由访问的 web APIs，我们可以利用这种无服务器功能为我们提供的强大功能。事实上，我们可以用网络函数实现另一个层次的可能性！

你可以在这里的链接[中找到代码库的参考。](https://github.com/ishandotjs/next-netlify-forms/tree/main)

编码快乐！

## [LogRocket](https://lp.logrocket.com/blg/nextjs-signup) :全面了解生产 Next.js 应用

调试下一个应用程序可能会很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪状态、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/nextjs-signup)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png)](https://lp.logrocket.com/blg/nextjs-signup)[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/nextjs-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你的 Next.js 应用上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用程序的性能，报告客户端 CPU 负载、客户端内存使用等指标。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

让您调试 Next.js 应用的方式现代化— [开始免费监控](https://lp.logrocket.com/blg/nextjs-signup)。**