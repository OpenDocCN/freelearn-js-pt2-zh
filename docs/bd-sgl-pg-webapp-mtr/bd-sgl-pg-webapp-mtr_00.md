# 序言

感谢您购买这本书。您为前端和 JavaScript 技术的新一步做出了明智的选择。Meteor 框架不仅仅是为了简化事情而出现的另一个库。它为 Web 服务器、客户端逻辑和模板提供了一个完整的解决方案。此外，它还包含了一个完整的构建过程，这将使通过块状方式为 Web 工作变得更快。多亏了 Meteor，链接您的脚本和样式已经成为过去，因为自动构建过程会为您处理所有事情。这确实是一个很大的改变，但您很快就会喜欢上它，因为它使扩展应用程序的速度与创建新文件一样快。

Meteor 旨在创建单页应用程序，其中实时是默认值。它负责数据同步和 DOM 的更新。如果数据发生变化，您的屏幕将进行更新。这两个基本概念构成了我们作为网页开发者所做的很多工作，而 Meteor 则无需编写任何额外的代码即可实现。

在我看来，Meteor 在现代网页开发中是一个完整的游戏改变者。它将以下模式作为默认值引入：

+   胖客户端：所有的逻辑都存在于客户端。HTML 仅在初始页面加载时发送

+   在客户端和服务器上使用相同的 JavaScript 和 API

+   实时：数据自动同步到所有客户端

+   一种“无处不在的数据库”方法，允许在客户端进行数据库查询

+   作为 Web 服务器通信默认的发布/订阅模式

一旦你使用了我所介绍的所有这些新概念，你很难回到过去那种只花费时间准备应用程序结构，而链接文件或将它们封装为 Require.js 模块，编写端点以及编写请求和发送数据上下的代码的老方法。

在阅读这本书的过程中，您将逐步介绍这些概念以及它们是如何相互连接的。我们将建立一个带有后端编辑帖子的博客。博客是一个很好的例子，因为它使用了帖子列表、每个帖子的不同路由以及一个管理界面来添加新帖子，为我们提供了全面理解 Meteor 所需的所有内容。

# 本书涵盖内容

第一章，*Meteor 入门*，描述了安装和运行 Meteor 所需的步骤，同时还详细介绍了 Meteor 项目的文件结构，特别是我们将要构建的 Meteor 项目。

第二章，*构建 HTML 模板*，展示了如何使用 handlebar 这样的语法构建反应式模板，以及如何在其中显示数据是多么简单。

第三章，*存储数据和处理集合*，涵盖了服务器和客户端的数据库使用。

第四章, *数据流控制*, 介绍了 Meteor 的发布/订阅模式，该模式用于在服务器和客户端之间同步数据。

第五章, *使用路由使我们的应用具有多样性*, 教我们如何设置路由，以及如何让我们的应用表现得像一个真正的网站。

第六章, *使用会话保持状态*, 讨论了响应式会话对象及其使用方法。

第七章, *用户和权限*, 描述了用户的创建以及登录过程是如何工作的。此时，我们将为我们的博客创建后端部分。

第八章, *使用 Allow 和 Deny 规则进行安全控制*, 介绍了如何限制数据流仅对某些用户开放，以防止所有人对我们的数据库进行更改。

第九章, *高级响应性*, 展示了如何构建我们自己的自定义响应式对象，该对象可以根据时间间隔重新运行一个函数。

第十章, *部署我们的应用*, 介绍了如何使用 Meteor 自己的部署服务以及在自己的基础设施上部署应用。

第十一章, *构建我们自己的包*, 描述了如何编写一个包并将其发布到 Atmosphere，供所有人使用。

第十二章, *Meteor 中的测试*, 展示了如何使用 Meteor 自带的 tinytest 包进行包测试，以及如何使用第三方工具测试 Meteor 应用程序本身。

附录, 包含 Meteor 命令列表以及 iron:router 钩子及其描述。

# 本书需要的软件

为了跟随章节中的示例，你需要一个文本编辑器来编写代码。我强烈推荐 Sublime Text 作为你的集成开发环境，因为它有几乎涵盖每个任务的可扩展插件。

你还需要一个现代浏览器来查看你的结果。由于许多示例使用浏览器控制台来更改数据库以及查看代码片段的结果，我推荐使用 Google Chrome。其开发者工具网络检查器拥有一个 web 开发者需要的所有工具，以便轻松地工作和服务器调试网站。

此外，你可以使用 Git 和 GitHub 来存储你每一步的成功，以及为了回到代码的先前版本。

每个章节的代码示例也将发布在 GitHub 上，地址为[`github.com/frozeman/book-building-single-page-web-apps-with-meteor`](https://github.com/frozeman/book-building-single-page-web-apps-with-meteor)，该仓库中的每个提交都与书中的一个章节相对应，为你提供了一种直观的方式来查看在每个步骤中添加和移除了哪些内容。

# 本书适合对象

这本书适合希望进入单页、实时应用新范式的 Web 开发者。你不需要成为 JavaScript 专业人士就能跟随书中的内容，但扎实的基本知识会让你发现这本书是个宝贵的伴侣。

如果你听说过 Meteor 但还没有使用过，这本书绝对适合你。它会教你所有你需要理解并成功使用 Meteor 的知识。如果你之前使用过 Meteor 但想要更深入的了解，那么最后一章将帮助你提高对自定义反应式对象和编写包的理解。目前 Meteor 社区中涉及最少的主题可能是测试，因此通过阅读最后一章，你将很容易理解如何使用自动化测试使你的应用更加健壮。

# 约定

在这本书中，你会发现多种用于区分不同信息类型的文本样式。以下是这些样式的几个示例及其含义解释。

文本中的代码词汇、数据库表名、文件夹名、文件名、文件扩展名、路径名、假 URL、用户输入和 Twitter 处理显示如下："With Meteor, we never have to link files with the `<script>` tags in HTML."

一段代码如下所示：

```js
<head>
  <title>My Meteor Blog</title>
</head>
<body>
  Hello World
</body>
```

当我们希望引起你对代码块中特定部分的关注时，相关行或项目以粗体显示：

```js
<div class="footer">
  <time datetime="{{formatTime timeCreated "iso"}}">Posted {{formatTime timeCreated "fromNow"}} by {{author}}</time>
</div>
```

任何命令行输入或输出如下所示：

```js
$ cd my/developer/folder
$ meteor create my-meteor-blog

```

**新术语**和**重要词汇**以粗体显示。例如，你在屏幕上看到的、在菜单或对话框中出现的词汇，在文本中显示为这样："However, now when we go to our browser, we will still see **Hello World**."

### 注意

警告或重要说明以这样的盒子形式出现。

### 提示

技巧和建议以这样的形式出现。

# 读者反馈

我们的读者的反馈总是受欢迎的。告诉我们你对这本书的看法——你喜欢或可能不喜欢的地方。读者反馈对我们开发您真正能从中获得最大收益的书很重要。

发送一般反馈，只需发送电子邮件至`<feedback@packtpub.com>`，并在消息主题中提到书名。

如果你需要我们出版某本书，并希望看到它，请在[www.packtpub.com](http://www.packtpub.com)上的**建议书名**表单中给我们留言，或者发送电子邮件至`<suggest@packtpub.com>`。

如果您在某个主题上有专业知识，并且您有兴趣撰写或为书籍做出贡献，请查看我们在[www.packtpub.com/authors](http://www.packtpub.com/authors)上的作者指南。

# 客户支持

既然您已经成为 Packt 书籍的自豪拥有者，我们有很多东西可以帮助您充分利用您的购买。

## 下载示例代码

您可以通过您在[`www.packtpub.com`](http://www.packtpub.com)的账户下载您购买的所有 Packt 书籍的示例代码文件。如果您在其他地方购买了这本书，您可以访问[`www.packtpub.com/support`](http://www.packtpub.com/support)并注册，以便我们将文件直接通过电子邮件发送给您。

## 错误更正

尽管我们已经尽一切努力确保我们的内容的准确性，但错误确实会发生。如果您在我们的书中发现一个错误——也许是在文本或代码中——我们将非常感谢您能向我们报告。这样做可以节省其他读者的挫折感，并帮助我们改进本书的后续版本。如果您发现任何错误，请通过访问[`www.packtpub.com/submit-errata`](http://www.packtpub.com/submit-errata)，选择您的书籍，点击**错误提交表单**链接，并输入您错误的详细信息。一旦您的错误得到验证，您的提交将被接受，错误将被上传到我们的网站，或添加到该标题的错误部分现有的错误列表中。

要查看之前提交的错误更正，请前往[`www.packtpub.com/books/content/support`](https://www.packtpub.com/books/content/support)并在搜索字段中输入书籍的名称。所需信息将在**错误更正**部分下出现。

## 盗版

互联网上的版权材料盗版是一个持续存在的问题，所有媒体都受到影响。在 Packt，我们非常重视我们版权和许可证的保护。如果您在互联网上以任何形式发现我们作品的非法副本，请立即提供给我们地址或网站名称，以便我们可以寻求解决方案。

请通过`<copyright@packtpub.com>`联系我们，并提供疑似被盗材料的链接。

我们感谢您在保护我们的作者和我们提供有价值内容的能力方面所提供的帮助。

## 问题

如果您在这本书的任何一个方面遇到问题，可以通过`<questions@packtpub.com>`联系我们，我们会尽力解决问题。