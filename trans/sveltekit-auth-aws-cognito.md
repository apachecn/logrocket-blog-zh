# 带有 AWS Cognito 的 SvelteKit Auth

> 原文：<https://blog.logrocket.com/sveltekit-auth-aws-cognito/>

2022 年最后一个季度，前端世界发布了一些非常酷的公告。其中之一是 [NextAuth.js 成为 Auth.js](https://vercel.com/blog/announcing-sveltekit-auth) ，并将 NextAuth.js 的出色开发人员体验带到了其他流行的 web 框架中。有了 Vercel 和一个了不起的社区的支持，Auth.js 很快将成为所有流行的 web 框架中的身份验证的首选解决方案。

但是如果不使用 Next.js 或者 React 呢？如果用 SvelteKit 呢？好吧，你很幸运，因为斯维尔基特认证是在这里拯救一天。SvelteKit Auth 是一个为 SvelteKit 应用程序提供认证的 SvelteKit 模块。

在本文中，我们将讨论:

## 什么是 SvelteKit Auth？

SvelteKit Auth 是一个为 SvelteKit 应用程序提供认证的 SvelteKit 模块。SvelteKit Auth 建立在 [Auth.js](https://authjs.dev/) 之上，允许你添加认证提供者和定制你的应用的认证流程。

## 构建我们的 SvelteKit 应用程序

让我们创建一个新的 SvelteKit 项目，并向其中添加 AWS Cognito 身份验证。我们将使用自定义凭证添加 AWS Cognito 身份验证，然后在服务器端和客户端获取身份验证令牌和会话数据，直到内部布局。

首先，让我们使用带有打字稿的[官方指南](https://kit.svelte.dev/docs/creating-a-project)来搭建一个新的 SvelteKit 项目:

```
npm create [email protected]st skauth-congito-demo

```

这将为我们提供裸机。

接下来，我们将把最新版本的 SvelteKit Auth 添加到我们的项目中:

```
pnpm install @auth/[email protected] @auth/[email protected]

```

## AWS 认知设置

我们将使用 AWS Cognito 进行身份验证。我们需要建立一个新的 AWS Cognito 用户池和一个应用程序客户端。您可以使用[官方指南](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-pool-as-user-directory.html)设置 AWS Cognito 用户池。一旦你有了`COGNITO_USER_POOL_ID`和`COGNITO_CLIENT_ID`，你就可以继续执行了。

现在我们已经准备好了 AWS Cognito 用户池和应用程序客户端，我们将把自定义凭证 auth 添加到我们的 SvelteKit 项目中。首先，让我们将 AWS Cognito SDK 添加到我们的项目中:

```
pnpm install amazon-cognito-identity-js

```

我们将在项目中添加以下环境变量。我们将使用这些环境变量来获取用户池 ID 和应用程序客户端 ID:

```
# .env
COGNITO_USER_POOL_ID=us-east-1_XXXXXXXXX
COGNITO_CLIENT_ID=XXXXXXXXXXXXXXXXXXXXXXXXXX

```

## AWS 认知实施

使用域驱动架构，我们将把所有与 auth 相关的模块保存在`src/lib/domain/auth`目录中。

创建一个新文件`src/lib/domain/auth/services/Cognito.ts`，并向其中添加以下代码:

```
/**
 * @file Cognito.ts
 * File containing the Cognito service
 */
import { COGNITO_USER_POOL_ID, COGNITO_CLIENT_ID } from '$env/static/private';
import { AuthenticationDetails, CognitoRefreshToken, CognitoUser, CognitoUserPool, CognitoUserSession } from 'amazon-cognito-identity-js';
export type CognitoUserSessionType = CognitoUserSession;
const CONFIGS = {
  UserPoolId: COGNITO_USER_POOL_ID,
  ClientId: COGNITO_CLIENT_ID
};
// Create a new Cognito User Pool
const Pool = new CognitoUserPool(CONFIGS);
// Wrapper function to create a new Cognito User from the User Pool
const User = (Username: string): CognitoUser => new CognitoUser({ Username, Pool });
/**
 * Login to Cognito User Pool using the provided credentials.
 * This will return the session data at the time of login.
 *
 * @param Username - Email address of the user to login
 * @param Password - Password of the user to login
 * @returns - Promise with the result of the login
 */
export const getSession = (Username: string, Password: string): Promise<CognitoUserSession> => {
  return new Promise((resolve, reject) =>
    User(Username).authenticateUser(new AuthenticationDetails({ Username, Password }), {
      onSuccess: resolve,
      onFailure: reject,
    })
  );
};
/**
 * Refresh the access token of the provided user.
 * We will use this method to refresh the access token from our axios interceptor
 * 
 * @param sessionData - Session data of the user with the refresh token
 * @returns - Promise with the new user object with tokens and expiration date
 */
export const refreshAccessToken = async (sessionData: {
  refreshToken: string;
}): Promise<CognitoUserSession> => {
  const cognitoUser = Pool.getCurrentUser();
  // Check if the user is logged in
  if (!cognitoUser) {
    throw new Error('No user found');
  }
  // Refresh the session
  const RefreshToken = new CognitoRefreshToken({
    RefreshToken: sessionData.refreshToken,
  });
  return new Promise<CognitoUserSession>((resolve) => {
    cognitoUser.refreshSession(RefreshToken, (_resp, session: CognitoUserSession) => {
      resolve(session);
    });
  });
}

```

该文件实现了登录 AWS Cognito 用户池和刷新访问令牌所需的所有方法。我们将在我们的 SvelteKit 认证模块中使用这个文件。

## 添加自定义凭据身份验证

SvelteKit Auth 利用 SvelteKit 提供的服务器端`hooks`来实现认证特性。在我们的应用程序中，我们将自定义和创建`src/hooks.server.ts`。

这是它看起来的样子。这看起来可能有点复杂，但我们将详细介绍每个步骤:

```
/**
 * @file src/hooks.service.ts
 * File containing the hooks service
 */
// Import the SvelteKit Auth module
import { SvelteKitAuth } from "@auth/sveltekit"
import Credentials from "@auth/core/providers/credentials"
// Import the Cognito service that we created earlier
import { getSession, refreshAccessToken, type CognitoUserSessionType } from "$lib/domains/auth/services/Cognito"
// Type of the user object returned from the Cognito service
import type AuthUser from "$lib/domains/auth/types/AuthUser";
// Import the secret key from the environment variables
import { AUTH_SECRET } from "$env/static/private";
interface AuthToken {
  accessToken: string;
  accessTokenExpires: number;
  refreshToken: string;
  user: {
    id: string;
    name: string;
    email: string;
  };
}
/**
 * Extract the user object from the session data. This is a helper function that we will use to extract the user object from the session data returned from the Cognito service.
 */
const extractUserFromSession = (session: CognitoUserSessionType): AuthUser => {
  if (!session?.isValid?.()) throw new Error('Invalid session');
  const user = session.getIdToken().payload;
  return {
    id: user.sub,
    name: `${user.name} ${user.family_name}`,
    email: user.email,
    image: user.picture,
    accessToken: session.getAccessToken().getJwtToken(),
    accessTokenExpires: session.getAccessToken().getExpiration(),
    refreshToken: session.getRefreshToken().getToken(),
  }
}
/**
 * Create the token object from the user object. This is a helper function that we will use to create the token object from the user object returned from the Cognito service.
 */
const createTokenFromUser = (user: AuthUser): AuthToken => {
  return {
    accessToken: user.accessToken,
    accessTokenExpires: user.accessTokenExpires,
    refreshToken: user.refreshToken,
    user: {
      id: user.id,
      name: user.name,
      email: user.email,
      image: user.image,
    },
  }
}
export const handle = SvelteKitAuth({
  secret: AUTH_SECRET,
  providers: [
    Credentials({
      type: 'credentials',
      id: 'credentials',
      name: 'Cognito',
      credentials: {
        email: { label: "Email", type: "email", placeholder: "[email protected]" },
        password: { label: "Password", type: "password" },
      },
      async authorize(credentials) {
        if (!credentials) return null
        try {
          const response = await getSession(credentials?.email, credentials?.password)
          return extractUserFromSession(response)
        } catch (error) {
          console.error(error);
          return null
        }
      }
    }) as any,
  ],
  /**
   * Since we are using custom implementation; we have defined URLs for the login and error pages
   */
  pages: {
    signIn: "/auth/login",
    error: "/auth/login",
  },
  callbacks: {
    /**
     * This callback is called whenever a JWT is created or updated. 
     * For the first time login we are creating a token from the user object returned by the authorize callback.
     * For subsequent requests we are refreshing the access token and creating a new token from the user object. If the refresh token has expired
     *
     */
    async jwt({ token, user, account }: any) {
      // Initial sign in; we have plugged tokens and expiry date into the user object in the authorize callback; object
      // returned here will be saved in the JWT and will be available in the session callback as well as this callback
      // on next requests
      if (account && user) {
        return createTokenFromUser(user);
      }
      // Return previous token if the access token has not expired yet
      if (Date.now() < token?.accessTokenExpires) {
        return token;
      }
      try {
        const newUserSession = await refreshAccessToken({
          refreshToken: token?.refreshToken,
        })
        const user = extractUserFromSession(newUserSession);
        return createTokenFromUser(user);
      } catch(error) {
        console.error(error);
        throw new Error('Invalid session');
      }
    },
    /**
     * The session callback is called whenever a session is checked. By default, only a subset of the token is
     * returned for increased security. We are sending properties required for the client side to work.
     * 
     * @param session - Session object
     * @param token - Decrypted JWT that we returned in the jwt callback
     * @returns - Promise with the result of the session
     */
    async session({ session, token }: any) {
      session.user = token.user
      session.accessToken = token.accessToken
      session.error = token.error
      return session;
    },
  },
});

```

在上面的代码中，我们定义了 SvelteKit 将用来处理认证的`handle`函数。我们使用来自 SvelteKit Auth 模块的 SvelteKitAuth 函数来创建`handle`函数。我们还使用来自 SvelteKit Auth 模块的`Credentials`来创建凭证提供者。

`Credentials`函数接受一个对象作为参数。该对象具有以下属性:

*   `type`:提供商类型。在我们的例子中，它是`credentials`
*   `id`:提供者的 ID。在我们的例子中，它是`credentials`
*   `name`:提供者名称。在我们的例子中，它是`Cognito`
*   `credentials`:包含凭证的对象。在我们的例子中，它是`email`和`password`
*   `authorize`:当用户尝试登录时，该函数将被调用。在我们的例子中，我们调用前面创建的`getSession`函数，并从会话数据中返回用户对象

在`pages`属性中，我们定义了登录和错误页面的 URL。如果没有通过认证，SvelteKit Auth 会将用户重定向到登录页面。因为我们为错误和登录保留了相同的 URL，所以我们将在登录页面的查询参数中收到错误消息。

在`callbacks`中，我们已经实现了`jwt`和`session`方法。每当从我们定义的`authorize`方法中创建一个 JWT 时，就会调用`jwt`方法。每当检查一个会话时，就会调用`session`方法。

## 创建根布局

现在我们已经配置了 SevelteKit Auth，我们需要创建一个将被所有页面使用的根布局。

创建一个`src/routes/+layout.svelte`文件并添加以下代码:

```
<script lang="ts">
  import { signOut } from "@auth/sveltekit/client"
  import { page } from "$app/stores"
</script>
<div>
  <header>
    {#if $page.data.session}
      <div>
        <strong>Hello {$page.data.session.user?.name}</strong>
        <button on:click|preventDefault={signOut} class="button">Sign out</button>
      </div>
    {:else}
      <a href="/auth/login" class="buttonPrimary">Sign in</a>
    {/if}
  </header>
  <slot />
</div>

```

一旦用户通过身份验证，SvelteKit Auth 将使用会话对象设置页面数据。我们可以使用`session`数据来检查用户是否被授权。

在我们的示例中，如果用户通过了身份验证，我们将显示用户名和退出按钮。如果用户未经验证，我们将显示一个登录按钮。这里，直接从 SvelteKit Auth 模块使用`signOut`函数。该功能将注销用户，并将用户重定向到登录页面。

## 将用户重定向到登录页面

现在我们有了根布局，我们可以创建登录页面了。我们将创建一个`src/routes/auth/login/+page.svelte`文件，并添加以下代码:

```
<script lang="ts">
  import { signIn } from "@auth/sveltekit/client"
  import { invalidateAll } from '$app/navigation';
  const handleSubmit = async (event: any) => {
    const data = new FormData(event.target);
    try {
      await signIn('credentials', {
        email: data.get('email'),
        password: data.get('password')
      });
    } catch (error) {
      await invalidateAll();
    }
  }
</script>
<h1>Login</h1>
<div>
  <form name="login" method="POST" on:submit|preventDefault={handleSubmit}>
    <input name="email" type="email" placeholder="Email Address" />
    <input name="password" placeholder="Password" type="password" />
    <button>Sign In</button>
  </form>
</div>

```

在上面的代码中，我们使用了来自 SvelteKit Auth 模块的`signIn`函数来登录用户。这将调用我们在`Credentials`中定义的`authorize`。如果用户认证成功，将调用`jwt`回调，用户将被重定向到主页，并带有我们在`hooks.server.ts`从`session`回调返回的会话数据。

## 会话和重定向

正如在根布局中所讨论的，SvelteKit Auth 将在页面存储中设置会话数据。我们可以在子布局中访问这些数据。为了确保我们已认证的路由不被未认证的用户访问，我们将把所有已认证的路由放在`src/lib/routes/(auth)`目录下。在这里，我们利用了 SvelteKit 惊人的[高级布局技术。](https://kit.svelte.dev/docs/advanced-routing#advanced-layouts)

现在让我们在该目录中创建一个`+layout.server.ts`文件:

```
import { redirect } from '@sveltejs/kit';
import type { LayoutServerLoad } from './$types';
import { getAccount } from '$lib/domains/auth/api/getAccount';
export const load: LayoutServerLoad = async ({ locals }) => {
  // Get the session from the locals
  const session = (await locals.getSession()) as any;
  // If the user is not authenticated, redirect to the login page 
  if (!session?.user?.id || !session?.accessToken) {
    throw redirect(307, '/auth/login');
  }
  // Get the account details at the root layout level so that we can use it in the sub layouts
  const account = await getAccount(session?.user?.id, session?.accessToken);
  // If the account is not found, redirect to the login page
  if (!account) {
    throw redirect(307, '/auth/login');
  }
  // On success, we can send the session and account data to the sub layouts
  return {
    session,
    account,
  };
};

```

现在，会话中的所有数据，即所需的令牌和用户帐户详细信息，在服务器端和客户端都可用，我们可以使用它们向后端和/或出于任何其他目的发出经过身份验证的请求。

## 结论

Auth.js 是一个有用的库，用于实现当今最流行的 web 框架的身份验证。它使得实现基于重定向的登录变得非常容易，最重要的是，它提供了很大的灵活性，允许我们根据需要实现自己的身份验证逻辑。

本文我们看到的只是 [Auth.js](https://authjs.dev/) 能做的一小部分。我强烈推荐你查看一下[auth . js](https://authjs.dev/getting-started/introduction)入门指南来了解更多。