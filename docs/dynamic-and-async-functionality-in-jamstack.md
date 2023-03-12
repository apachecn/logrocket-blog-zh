# JAMstack - LogRocket 博客中的动态和异步功能

> 原文：<https://blog.logrocket.com/dynamic-and-async-functionality-in-jamstack/>

JAMstack 网站是由通过 CDN 提供的静态文件组成的，但这是否意味着它们给了你一种静态的体验？答案是一个大大的否定。

有一种常见的误解，认为“静态网站”一词描述的是一个不灵活或固定的网站。它真正的意思是，不需要服务器来服务站点。

JAMstack 站点的核心是由预先呈现的文件组成的，这些文件是静态提供的，并通过使用 JavaScript 的 API 逐步增强。不像 WordPress 这样的 HP 项目，我们不需要一个中间步骤来为每个浏览器请求生成 HTML 文件。文件已经被存储，因为它们应该在浏览器上呈现。这些静态文件通常包含调用 API 来完成大部分动态工作的 JavaScript 代码。

但这不就意味着我们在造服务器吗？不完全是。由于我们使用无服务器功能，我们不需要创建真正意义上的服务器。

在本教程中，我们将展示 JAMstack 站点如何处理动态内容和交互。我们将演示 JAMstack 站点需要执行的一些最常见的动态操作，包括:

1.  异步加载数据——我们将创建一个页面，您可以在其中键入神奇宝贝的名称，从而异步获取它的详细信息
2.  读取和写入数据库—我们将使用无服务器功能和数据库即服务(DBaaS)解决方案来动态读取和写入数据库
3.  向上述应用程序添加登录页面(即用户验证)

## 1.**同步加载**ing d**ATA**a

假设您正在从一个 API 请求一些数据。根据具体情况，服务器可能需要一些时间来处理请求，同时阻塞主线程，导致网页无响应。

我们可以使用回调、承诺和 async/await(即异步 JavaScript 特性)来执行长时间的网络请求，而不会阻塞主线程。

JavaScript 包含许多异步库。我们将使用[获取 API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 来加载我们的数据。

现在让我们构建一个页面，使用户能够使用 [PokéAPI](https://pokeapi.co/) 按名称搜索神奇宝贝的详细信息。

1.  创建一个带有输入字段和搜索按钮![JAMstack App Search Form Input Field and Button](img/bfae7faa0579ccb351079f1bc1f138f0.png)

    ```
    <form>    <label for="name">Get Pokemon Details</label>    <input type="text" id="name" name="name" required />    <button type="submit">Search</button> </form> 
    ```

    的表单
2.  Listen for the submit event on the button, call the API with the data in the input field, and cache the result

    ```
    <script type = "module" >
      import displayResult from './displayResult.js';
      const form = document.querySelector('form');
      const handleSubmit = async event => {
      event.preventDefault();
      const name = form.elements['name'].value;
      let character;
      const fromCache = localStorage.getItem(name);
      if (fromCache) {
        character = JSON.parse(fromCache);
      } else {
        character = await fetch(
            `https://pokeapi.co/api/v2/pokemon/${name}`
          )
          .then(response => response.json())
          .catch(error => console.error(error));
        localStorage.setItem(name, JSON.stringify({
          name: character.name,
          moves: character.moves,
          weight: character.weight,
          height: character.height,
          types: character.types,
          sprites: character.sprites,
        }));
      }
      displayResult(character);
    };
    form.addEventListener('submit', handleSubmit);
    </script>
    ```

    注意:我们使用一个名为 displayResult 的辅助函数，它从我们的 API 结果中形成数据的形状，并对 DOM 进行更多的检查。

    ```
    const createResultRow = container => character => {
      if (!'content' in document.createElement('template')) {
        console.error('your browser does not support the template element');
      }
    // load the template for a result row
      const template = document.getElementById('search-result-template');
      const result = document.importNode(template.content, true);
      const types = character.types.map(({
        type
      }) => type.name).join(', ');
      const moves = character.moves.map(({
        move
      }) => move.name).join(', ');
    // update the template content
      result.querySelector('.name').textContent = character.name;
      result.querySelector('.type').textContent = types;
      result.querySelector('.weight').textContent = character.weight;
      result.querySelector('.height').textContent = character.height;
      result.querySelector('.moves').textContent = moves;
    // update the character image and add alt text
      const img = result.querySelector('.image');
      img.src = character.sprites.front_default;
      img.alt = character.name;
    // add the new row to the results container
      container.appendChild(result);
    };
    export default character => {
      const searchResult = document.getElementById('search-result');
    // remove any existing search results
      while (searchResult.firstChild) {
        searchResult.removeChild(searchResult.firstChild);
      }
    // loop through the results and create a new listing for each character
      [character].map(createResultRow(searchResult));
    };

    ```

3.  最后，在 DOM 上显示结果![JAMstack App Search Results](img/cd4933f7895cf457e36accd29a2574dd.png)

这就是我们如何在静态下异步加载数据。查看 [CodeSandbox](https://codesandbox.io/s/naughty-chebyshev-d4gb6) 上的演示。

## 2.**读作** ing 和 w**rit**ing t**o**ad**atabase**

您可能想知道，如果我们没有建立数据库，我们将如何读写数据库呢？简而言之，[数据库即服务(DBaaS)解决方案](https://www.g2.com/categories/database-as-a-service-dbaas)消除了设置或托管数据库的需求。

DBaaS 工具通过简单地上传或创建我们想要存储的数据类型，自动生成 API 来对数据库执行 CRUD 操作。在本教程中，我们将使用动物群创建一个小型电子邮件订阅应用程序(在继续以下步骤之前，您需要创建一个[动物群](https://fauna.com/)帐户)。

1.  创建一个数据库，并将其命名为`Email`
2.  点击左侧导航栏中的`Security`，选择我们刚刚创建的数据库![Creating a Database in Fauna](img/d04fbea8929ac8ece05326f67d7ce83c.png)
3.  保存后，将密钥存放在某个地方，因为它只显示一次
4.  接下来，创建一个扩展名为`.gql`的文件，将其命名为`schema.gql`，并添加以下代码

    ```
    type Email {   id: String! } type Query {   emails: [Email!] ! }
    ```

5.  在 GraphQL 选项卡中上传这个文件，我们就可以执行 CRUD 了

现在，我们有了一个数据库和一个高度可扩展的、安全的 GraphQL API，而且是免费的。

我们可以使用带有密钥的自定义 URL 来执行读写操作，密钥是由客户端的 Fauna 提供的。但是由于我们不能将敏感密钥放在客户端代码中，所以我们将使用无服务器函数将私有服务器密钥传递给 Fauna，以便对数据库进行读写访问。无服务器功能对于保持私有密钥的私有性至关重要，它们可以轻松地从客户端处理昂贵的计算任务。

下图显示了我们的应用程序的最终结构。我们将调用无服务器函数(Lambda)——在我们的例子中，我们将使用来自 JAMstack 应用程序(部署在 CDN 上的 HTML)的 [Netlify 函数](https://docs.netlify.com/functions/overview/)。无服务器函数将负责保存我们的私钥并调用我们的数据库。

![Final Structure of JAMstack App](img/8c592e9f880eff2edbddea73ca60bd04.png)

下面是我们的无服务器函数的代码，它将电子邮件的详细信息存储到数据库中。

```
/* Import faunaDB sdk */
const faunadb = require('faunadb');
const key = 'your faunadb key goes here';
// your faunaDB key
const q = faunadb.query;
const client = new faunadb.Client({
  secret: key
});
// These are required for CORS
const headers = {
  'Access-Control-Allow-Origin': '*',
  'Content-Type': 'application/json',
  'Access-Control-Allow-Methods': '*',
};
exports.handler = (event, context, callback) => {
  return client.query(
    q.Create(
        q.Collection("Email"),
        { 
            data: {
                id: event.queryStringParameters.email
            }
        }
      )
  ).then((response) => {
    console.log("success", response)

    return callback(null, {
      statusCode: 200,
      headers,
      body: JSON.stringify(response)
    })
  }).catch((error) => {
    return callback(null, {
      statusCode: 400,
      headers,
      body: JSON.stringify(error)
    })
  })
}

```

将此功能部署到任何无服务器支持服务。有关在 Netlify 中部署无服务器功能的更多信息，请参见[官方文档](https://www.netlify.com/blog/2018/07/09/building-serverless-crud-apps-with-netlify-functions-faunadb/)。

![Email Form to Test DBaaS App](img/4044a32cf2887f419ceca406ba1f8d4c.png)

HTML 代码将类似于我们创建的第一个应用程序；只有脚本代码会改变。

```
<script>
 const form = document.querySelector('form');
const handleSubmit = async event => {
 event.preventDefault();
 const email = form.elements['email'].value;
 fetch(`http://localhost:9000/.netlify/functions/create?email=${email}`)
  .then(res => res.json())
  .then(data => {
   const successHTML =
    document.querySelector('.email-msg').innerHTML = `
            Your email is sucessfully added ${data.data.id}!
            Thanks for being a part of testing! Cheers :) 
          `;
  });
}
form.addEventListener('submit', handleSubmit); 
</script>

```

在上面的函数中，用 localhost URL 替换服务提供的 URL，并将其添加到 HTML 代码中。

与第一个应用程序的唯一变化是传递的查询字符串，这是我们来自客户端的电子邮件。如果成功，应用程序将在`email-msg`类中显示一条消息。

![Successfully Submitted Email Form to Test DBaaS App](img/8027dedd07763c782c6c04b87dc9e208.png)

查看 [CodeSandbox](https://codesandbox.io/s/pedantic-water-ng1ji) 上的演示。

## 3.用户认证

让我们研究一下在 JAMstack 中用户认证是如何完成的。

1.  使用 OAuth、 [AWS Cognito](https://blog.logrocket.com/aws-services-cheat-sheet/) 或 Netlify Identity 等第三方服务来帮助实现认证系统
2.  使用 JSON Web 令牌(JWT)在服务之间交换和验证用户信息
3.  使用无服务器功能验证令牌并允许用户登录应用程序(我们也可以使用无服务器功能或 Auth0 创建委托令牌，或者使用委托令牌授权从客户端直接访问服务)
4.  编写客户端代码来管理登录和注销

为了简单起见，我们将使用 OAuth 的内置脚本。我们将调用 Google 的授权服务器(假设它是我们的无服务器功能)，它将:

*   处理重定向回您的应用程序
*   验证访问令牌
*   存储授权服务器发送给应用程序并在应用程序随后进行授权 API 调用时检索的访问令牌

让我们为我们创建的第一个神奇宝贝应用程序添加一个登录页面(我们可以简单地重用这个阶段的代码)。

对于 google 身份验证，创建一个名为`google-authentication.js`的文件，并添加以下代码。

```
var GoogleAuth;
const SCOPE = 'https://www.googleapis.com/auth/userinfo.email';
function handleClientLoad() {
  // Load the API's client and auth2 modules.
  // Call the initClient function after the modules load.
  gapi.load('client:auth2', initClient);
}
function initClient() {
  // Initialize the gapi.client object, which app uses to make API requests.
  // Get API key and client ID from API Console.
  // 'scope' field specifies space-delimited list of access scopes.
  gapi.client.init({
      'apiKey': 'your api key',
      'clientId': 'your client id',
      'scope': SCOPE
  }).then(function () {
    GoogleAuth = gapi.auth2.getAuthInstance();
// Listen for sign-in state changes.
    GoogleAuth.isSignedIn.listen(updateSigninStatus);
// Handle initial sign-in state. (Determine if user is already signed in.)
    var user = GoogleAuth.currentUser.get();
    setSigninStatus();
// Call handleAuthClick function when user clicks on
    //      "Sign In/Authorize" button.
    $('#sign-in-or-out-button').click(function() {
      handleAuthClick();
    });
    $('#revoke-access-button').click(function() {
      revokeAccess();
    });
  });
}
function handleAuthClick() {
  if (GoogleAuth.isSignedIn.get()) {
    // User is authorized and has clicked "Sign out" button.
    GoogleAuth.signOut();
  } else {
    // User is not signed in. Start Google auth flow.
    GoogleAuth.signIn();
  }
}
function revokeAccess() {
  GoogleAuth.disconnect();
}
function setSigninStatus(isSignedIn) {
  var user = GoogleAuth.currentUser.get();
  var isAuthorized = user.hasGrantedScopes(SCOPE);
  if (isAuthorized) {
    $('#sign-in-or-out-button').html('Sign out');
    $('#revoke-access-button').css('display', 'inline-block');
    $('#auth-status').html(`Welcome to the app - ${user.Qt.Ad}`);  
    document.getElementById('app').style.display = 'block';
} else {
    $('#sign-in-or-out-button').html('Sign In/Authorize');
    $('#revoke-access-button').css('display', 'none');
    $('#auth-status').html('You need to be authorised to use the app');
    document.getElementById('app').style.display = 'none';
  }
}
function updateSigninStatus(isSignedIn) {
  setSigninStatus();
}var GoogleAuth;
const SCOPE = 'https://www.googleapis.com/auth/userinfo.email';
function handleClientLoad() {
  // Load the API's client and auth2 modules.
  // Call the initClient function after the modules load.
  gapi.load('client:auth2', initClient);
}
function initClient() {
  // Initialize the gapi.client object, which app uses to make API requests.
  // Get API key and client ID from API Console.
  // 'scope' field specifies space-delimited list of access scopes.
  gapi.client.init({
      'apiKey': 'AIzaSyDJM2tpYAm_9za_sZHBajTKCezSoRalNtg',
      'clientId': '1021021275901-8fqh15chm5lgkfpc98m2iuu0n8iiq2j5.apps.googleusercontent.com',
      'scope': SCOPE
  }).then(function () {
    GoogleAuth = gapi.auth2.getAuthInstance();
// Listen for sign-in state changes.
    GoogleAuth.isSignedIn.listen(updateSigninStatus);
// Handle initial sign-in state. (Determine if user is already signed in.)
    var user = GoogleAuth.currentUser.get();
    setSigninStatus();
// Call handleAuthClick function when user clicks on
    //      "Sign In/Authorize" button.
    $('#sign-in-or-out-button').click(function() {
      handleAuthClick();
    });
    $('#revoke-access-button').click(function() {
      revokeAccess();
    });
  });
}
function handleAuthClick() {
  if (GoogleAuth.isSignedIn.get()) {
    // User is authorized and has clicked "Sign out" button.
    GoogleAuth.signOut();
  } else {
    // User is not signed in. Start Google auth flow.
    GoogleAuth.signIn();
  }
}
function revokeAccess() {
  GoogleAuth.disconnect();
}
function setSigninStatus(isSignedIn) {
  var user = GoogleAuth.currentUser.get();
  var isAuthorized = user.hasGrantedScopes(SCOPE);
  if (isAuthorized) {
    $('#sign-in-or-out-button').html('Sign out');
    $('#revoke-access-button').css('display', 'inline-block');
    $('#auth-status').html(`Welcome to the app - ${user.Qt.Ad}`);  
    document.getElementById('app').style.display = 'block';
} else {
    $('#sign-in-or-out-button').html('Sign In/Authorize');
    $('#revoke-access-button').css('display', 'none');
    $('#auth-status').html('You need to be authorised to use the app');
    document.getElementById('app').style.display = 'none';
  }
}
function updateSigninStatus(isSignedIn) {
  setSigninStatus();
}

```

不要担心代码；这只是来自谷歌的修改样本。我们简单地替换了作用域、API 键和`clientId`。

对于客户端代码，我们将编辑`index.html`文件。

1.  在 HTML 代码

    ```
    <script src="./google-authentication.js"></script>
    ```

    中包含上述脚本
2.  在`main`标签上方的正文中添加登录按钮

    ```
    <center style="margin-top: 100px"> <button class="loginBtn loginBtn--google" id='sign-in-or-out-button'> </button> <button id="revoke-access-button" style="display: none; margin-left: 25px">Revoke access</button> </center>
    ```

3.  将应用程序的显示设置为`none`，并使其在登录后出现，这发生在我们添加的

    ```
    <main id='app' style="display: none;">
    ```

    的谷歌认证脚本中

<main id="app">注意:我们可以在使用令牌或用户数据进行认证后进行无服务器函数调用，以获得我们需要的认证数据。上面的过程只是一个小的测试应用程序，用来演示身份验证是如何工作的。

![JAMstack App Authentication Before Login](img/898011ea4fdf7a426e69f33572e2cf56.png)

![JAMstack App Authentication After Login](img/60b799b6720e27a27332a2383eddad5d.png)

这就是全部了！如果你想看完整的演示，请前往 [CodeSandbox](https://codesandbox.io/s/small-pine-qqsxd) 。

## 结论

跳过服务器并使用 JAMstack 来构建和交付网站可以节省时间、金钱，并解决 DevOps 的问题。这些不仅仅是静态网站；有了 JAMstack，你的应用程序可以响应用户交互，存储数据，处理用户认证，以及你想要一个现代网站做的任何事情。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)

</main>