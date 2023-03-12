# 为 Angular - LogRocket 博客发布 LogRocket

> 原文：<https://blog.logrocket.com/announcing-support-for-angular-and-ngrx-6ec55224454/>

今天，我们很高兴地宣布官方支持 Angular 和 ngrx。使用新的 logrocket-ngrx npm 包，开发人员可以在生产中记录操作/状态，帮助更快地修复错误和用户问题。

### 什么是 LogRocket？

![](img/7f9cc7da85477f88dd33827526df86b4.png)

LogRocket 是第一个 webapps 的生产日志工具。它有一个高度优化的 JavaScript SDK 和插件，用于 Redux、@ngrx/store 和 Vuex。

除了记录操作/状态之外，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有标题+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

### 为什么它有帮助

Vuex 日志通常是解决错误和用户报告问题的关键。

![](img/4e576b33ee7ca491ed497d49c9f4d83c.png)

LogRocket 集成了错误报告工具，如 Sentry 和 Bugsnag，让您看到每个 bug 的 Vuex 日志和视频。这不仅有助于修复错误，也有助于理解错误的影响，因为有些 JavaScript 错误是完全无害的。通过观看视频和查看错误发生时的日志，您可以很容易地判断它是否确实影响了用户，或者可以忽略。

### @ngrx/store 插件

ngrx 插件记录应用程序中的动作/状态。设置只需要几行代码:

```
import createNgrxMiddleware from 'logrocket-ngrx'; const logrocketMiddleware = createNgrxMiddleware(LogRocket, opts); const productionReducer = compose(
  stateSetter, 
  logrocketMiddleware, 
  combineReducers)
(reducers);
```

默认情况下，所有的突变都会被记录，但是该插件允许您指定一个杀毒功能来阻止某些突变被记录，或者隐藏突变负载中的敏感信息。

### `ngrx action search`

![](img/a5980d574c1fc3bf9adc80b14de4d5bb.png)

LogRocket 有一个强大的搜索界面，可以让您跨会话搜索特定的 Vuex 突变。您可以组合搜索过滤器来查找给定用户或应用程序中某个页面的突变。

### 入门指南

要开始使用 LogRocket，请在[https://logrocket.com 创建一个免费账户。](https://logrocket.com.)