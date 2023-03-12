# 将社交媒体按钮整合到你的网站的 6 种方法

> 原文：<https://blog.logrocket.com/6-ways-to-integrate-social-media-buttons-into-your-website/>

你想让你网站的访问者知道你的社交网络渠道吗？你希望你的文章在社交网络上被分享吗？然后你需要可点击的社交媒体符号(图标)和每个平台对应的 like 和 share 按钮。

在接下来的文章中，我将向你展示几种使你的网站适合社交网络的可能性。

## 将社交媒体图标作为图像嵌入

您可以轻松地将社交网络图标作为图像嵌入到您的网站上，然后将它们链接到您的频道地址。但是你从哪里得到它们呢？互联网上有很多好的网站，你可以免费下载各种形式的符号作为图像文件。

我推荐页面[比如 iconfinder.com](https://d.docs.live.net/183f2cb1fca48b17/Artikel/iconfinder.com)。

在这里，您可以简单地输入所需图标的名称(例如“instagram”)，您将获得一个质量非常好的免费下载图像文件列表。

图标或图标组的其他良好供应商名单可在[buffer.com](https://buffer.com/library/social-media-icons/)找到。

现在我将向你展示如何在你的网站中嵌入图标。我们假设名为“instagram.png”的图标与您的 HTML 文件位于同一个文件夹中。

然后，您可以使用下面的代码片段:

```
<a target="_blank" href="https://www.instagram.com/blaustern_fotografie/">
 <img src="instagram.png">
</a>
```

标签用于使图标成为一个链接。当然，你得把上面显示的地址换成你自己 Instagram 个人资料的地址。通过设置属性`target="_blank"`，您可以确保概要文件在新的浏览器选项卡中打开。

如果图标位于相对于 HTML 文件的子文件夹中(例如“icons”)，则必须相应地调整图像的路径:

```
<img src="icons/instagram.png">
```

当然，你可以根据自己的需要调整图标的大小。我建议你为属于一个公共组的所有图标定义一个 CSS 类，例如`"sm_icon"`:

```
<img class=“sm_icon“ src="icons/instagram.png">
```

在网站的 CSS 部分，你可以自定义图标的大小。但是请确保不要超过图标图像文件的分辨率，否则图像看起来会很模糊。

```
.sm_icon{

  width: 50px;
  /* the height is adjusted automatically*/
}
```

## 图像格式的选择:PNG 还是 SVG？

你会注意到图像文件有不同类型的文件格式，如 JPG，GIF，PNG 或 SVG。文件类型具有不同的特征，因此对于在网站中的使用也有一定的优点和缺点。

我们将它们分为基于光栅的格式(JPG，GIF，PNG)和基于矢量的格式 SVG。基于光栅的格式具有固定的分辨率，以像素为单位具有特定的高度和宽度。

对于这些格式，您必须始终根据屏幕的分辨率和缩放级别以及您想要在网站上显示的图像大小，小心选择合适的分辨率。

如果放大时超出分辨率，图像将会变得模糊或像素化。

例如，如果从 Iconfinder 下载分辨率为 64x64px 的图标，然后在 CSS 设置中定义 200px 的宽度，就会发生这种情况。

另一方面，SVG 格式没有预定义的分辨率。矢量图像总是可以缩放到所需的大小，而不会损失质量。

因此，所有的屏幕尺寸和缩放级别都可以用同一个文件来处理。您可以像对待任何其他图像格式一样对待矢量图像，并将它们作为具有所需高度和宽度的`img-tags`包含在您的 HTML 代码中:

```
<img class=“sm_icon“ src="instagram.svg">
```

如果还是要用基于像素的格式，我推荐 PNG 格式。与 JPG 或 GIF 相比，它支持透明的图像区域，这是图标和标志经常需要的。

## 将社交媒体图标作为字体嵌入

作为图像文件的替代，图标也可以使用字体库集成。这样做的好处是图标可以像普通文本一样格式化。

我将使用库“Font Awesome”作为例子来解释这个过程:

在[https://fontawesome.com/](https://fontawesome.com/)创建一个免费用户账户(现在这是导入免费或付费图标的一个要求)。

*   在您的帐户中，您现在可以找到[“工具包”](https://fontawesome.com/kits)部分。您的新套件有一个 ID，由几个数字和字母组成。单击该工具包后，您将看到需要插入 HTML 页面的 head 标记之间以导入库的脚本代码。

在下面的例子中，我用 x 字符屏蔽了 ID:

```
<script src="https://kit.fontawesome.com/xxxxxxxxxx.js" crossorigin="anonymous"></script>
```

*   然后，您可以搜索图标，例如“instagram”。对于每一个图标，你会找到如何将它包含在 HTML 代码中的说明。我已经决定的图标名称为“instagram-square”，包括如下所示。

```
<a target="_blank" href="https://www.instagram.com/blaustern_fotografie/">
    <i class="fab fa-instagram-square"></i>
</a>
```

*   您可以使用 CSS 来设置图标的样式，例如使用不同的字体大小或颜色:

```
.fa-instagram-square{
    color: green;
    font-size: 18px;
}
```

请注意，到字体 awesome 脚本的链接建立了到外部服务器的连接。这可能涉及到你的网站访问者的个人数据传输到字体牛逼。

根据适用的法律法规(如 gdpr)，您可能需要告知您的用户有关此服务的信息。或者你可以谨慎行事，自己管理图书馆。关于这方面的信息可以在这里找到:[托管字体牛逼自己](https://fontawesome.com/how-to-use/on-the-web/setup/hosting-font-awesome-yourself)

作为字体 Awesome 的替代品，我可以推荐谷歌的[材质图标](https://material.io/resources/icons/?style=baseline)。这里的图标整合非常类似于字体牛逼。

## 为不同平台手动集成社交媒体按钮(如分享)

如果你想包含脸书、推特和其他公司的喜欢、分享和其他互动按钮，你必须使用由各个平台提供的特殊 HTML 代码。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

*   在面向开发者的页面[脸书](https://developers.facebook.com/docs/plugins)上，你可以为不同的脸书按钮生成 HTML 代码。
*   在 [Twitter for Websites](https://developer.twitter.com/en/docs/twitter-for-websites/overview) 页面，你可以找到嵌入推文按钮、关注按钮或嵌入推文的说明。
*   Instagram、Pinterest、LinkedIn 等其他网络也为你提供了在网站中嵌入按钮或小工具的可能性。

请记住，嵌入代码可能会将网站访问者的个人数据传输到相应的平台，即使他们没有在那里注册！根据法律情况，你必须采取一定的措施。

例如，获得访问者的同意或告知他们在隐私政策中使用外部代码。建议您的用户参考各个平台的数据保护政策。

## 在特殊提供商的帮助下集成社交媒体按钮

在一个帖子下面，你经常会发现一系列的社交媒体按钮。为每个单独的按钮手动插入相应平台的给定代码非常耗时。因此，你可以找到为你做这项工作的供应商。你所要做的就是将某个脚本嵌入到你的页面中，并定义社交媒体按钮栏应该出现的位置。

这方面的一个例子是 [sharethis](https://sharethis.com/) 。免费注册后，您可以创建一个多达 43 个社交媒体按钮的按钮栏！

一旦您进行了一些单独的设置(例如，语言、按钮上显示的信息)和视觉调整(例如，颜色、边角、间距)，您将从 sharethis 获得一个自定义脚本，您可以将它嵌入到页面的页眉区域。

这个脚本当然会建立到 sharethis 服务器的连接。您应该在您的隐私政策中告知您网站的访问者这一点。你也可以把他们重定向到[https://sharethis.com/privacy/](https://sharethis.com/privacy/)。

在那里，您可以找到 sharethis 收集哪些用户相关数据的信息。请注意，该页面上有一个“选择退出”按钮，可用于根据 sharethis 收集的数据阻止显示用户相关广告。

重要提示:你还必须告知你的用户关于个人平台的隐私政策，如脸书、推特等。，因为 sharethis 把用户的数据转移到各自的平台上。

另一个提供者是 [addtoany](https://www.addtoany.com/buttons/for/website) 。按钮的选择在这里甚至更大，你甚至可以不用注册就能得到代码。

关于数据保护，同样的建议适用于 sharethis。关于收集和使用用户相关数据的信息在[https://www.addtoany.com/privacy](https://www.addtoany.com/privacy)提供。

当然还有更多提供者，比如 [addthis](https://www.addthis.com/) 。

## WordPress 的社交媒体插件

WordPress 的社交媒体插件领域甚至更多样化。你可以在这里了解不同的插件:

[WP 初学者:2020 年 WordPress 的 10 个最佳社交媒体插件](https://www.wpbeginner.com/plugins/best-social-media-plugins-for-wordpress/))

此外，提供商 sharethis 和 addtoany 也以 WordPress 插件的形式提供服务。

当然，在使用社交媒体插件时，你必须再次注意隐私政策或用户的同意。

## 结论

正如你所看到的，在你的网站中集成交互式社交媒体按钮并不难。您可以手动插入按钮，或者依靠各种插件提供商的支持。最好选择对你来说最容易的选项！如果你也注重隐私，你是安全的。

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。