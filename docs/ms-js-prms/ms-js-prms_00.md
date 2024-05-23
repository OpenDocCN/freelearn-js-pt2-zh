# 序言

在这本书中，我们将探讨 JavaScript 中承诺的概念和实现。这本书有一个不断发展的上下文，将引导你从初学者水平达到承诺的专家水平。本书的每一章都会为你提供一个实现特定目标的概要，帮助你实现并量化你在每一章中吸收的知识量。

所有章节的整个堆栈都是设计成这样，以至于当你阅读它时，书会随着你的阅读而发展。本书的每一章都分为两部分：一部分是概念构建部分，另一部分是实验部分，你将能够尝试概念的片段，有时是代码，有时是最佳实践，有时是图片。

前四章基本上是理论知识，为你提供了 JavaScript 和承诺的坚实基础。所以，如果你是一个新手，对 JavaScript 或承诺一无所知，这些章节你会学到很多。本书的其他章节更侧重于技术，你将了解承诺在 WinRT，Angular.js，jQuery 和 Node.js 中的实现。所以，如果你是一个专业人士，已经有了关于承诺的一些想法，你可以直接跳到第五章，*WinRT 中的承诺*，但我建议你阅读所有章节，以便更好地理解本书。

我们将首先介绍 JavaScript 以及它从 90 年代末到 21 世纪初的起伏。我们将关注异步编程是什么以及 JavaScript 是如何使用它的。接下来，我将介绍承诺及其影响以及它是如何实现的。为了使本书有趣并为您提供更多知识，我将向您展示承诺如何在 Java 这种最成熟的面向对象编程语言中占据一席之地。这个附加内容将作为一个旁路，并以更有效的方式阐明概念。

随后，本书的流程将引导你了解一些最常用的 JavaScript 库中承诺的实现。我们将看到一个示例代码，了解这些库的工作机制。最后，我们将在最后一章中总结本书，向你展示 JavaScript 接下来会发生什么，为什么在过去几年中它获得了如此多的关注，以及 JavaScript 可能的未来。

# 本书内容涵盖

第一章，*Promises.js*，涵盖了 JavaScript 的历史以及它是如何成为现代应用程序开发中领先的技术的。我们将讨论为什么在 90 年代初需要 JavaScript 以及这种语言在其存在期间是如何经历起伏的。

第二章，*JavaScript 异步模型*，解释了编程模型是什么以及它们如何在不同的语言中实现，从简单的编程模型到同步模型到异步模型。我们还将看到任务是如何在内存中组织的，以及它们将如何根据它们的轮次和优先级进行服务，以及编程模型是如何决定要服务哪个任务的。

第三章，*Promise 范式*，涵盖了 promise 的范式及其背后的概念。我们将学习 promise 的概念知识、deferred、常见的 promise 序列以及 promise 如何在解耦业务逻辑和应用程序逻辑中提供帮助。我们还将学习 promise 与事件发射器之间的关系以及它们之间的关系的概念。

第四章，*实现 Promise*，讨论了为什么要实现 promise 以及为什么选择 Java 作为本章的核心主题。Java 比任何其他编程语言都有更丰富的功能，并且它还有更好的异步行为机制。本章是我们开始掌握 promise 的旅程的起点。

第五章，*WinRT 中的 Promise*，解释了如何在 WinRT 中实现 promise。我们将看到 promise 在 Windows 平台上的演变以及它如何为不同的 Windows-based 设备做出贡献。

第六章，*Node.js 中的 Promise*，介绍了 Node.js 是什么，这个最令人惊叹的库是如何演变而来的，是谁创建的，以及它如何帮助我们创建实时 web 应用。我们将看到 Q，这是向 Node.js 提供 promise 的最佳方式。我们将了解如何使用 Q，然后我们将看到使用 Q 与 Node.js 结合的不同方法。

第七章，*Angular.js 中的 Promise*，解释了 promise 将在 Angular.js 中如何实现，它是如何演变的，以及 promise 将如何帮助实现为实时 web 应用程序组成的应用程序。我们还将看到 Q 库的功能以及使用代码实现的 Angular.js 中的 promise，并学习如何在下一个应用程序中使用它们。

第八章，*jQuery 中的 Promise*，讨论了 jQuery 是如何开始成形的，以及它是如何成为现代 web 开发的基本元素的。我们将学习如何构建基本的 jQuery 文档以及如何调用嵌入到 HTML 文件中的函数。我们将了解为什么我们开始在 jQuery 中使用 deferred 和 promise，以及它们如何帮助我们创建基于 web 平台和便携设备的尖端应用程序。

第九章，*JavaScript – 未来已来*，介绍了 JavaScript 是如何改变游戏规则的，以及它有着光明的前途。我们还将探讨为什么 JavaScript 具有很大的倾向性和可采用性，这将引导它在计算机科学的几乎每个领域达到下一个使用水平。

# 本书所需材料

如果您是希望了解更多关于 JavaScript 的有趣事实以使您的生活更轻松的软件工程师，这本书适合您。简单而吸引人的语言，配以叙述和代码示例，使这本书易于理解和应用其实践。本书从 JavaScript 承诺的介绍开始，以及它是如何随时间演变的。然后，您将学习 JavaScript 异步模型以及 JavaScript 如何处理异步编程。接下来，您将了解承诺范式及其优势。最后，本书将向您展示如何在 WinRT、jQuery 和 Node.js 等平台上实现承诺，这些平台在项目开发中使用。

为了更好地掌握本书内容，您应该了解基本的编程概念、JavaScript 的基本语法以及良好的 HTML 理解。

# 本书适合对象

本书适用于所有希望在其下一个项目中应用承诺范式并从中获得最佳结果的软件/网页工程师。这本书包含了 JavaScript 中承诺的所有基本和高级概念。这本书也可以作为已经在项目中使用承诺并希望改进当前知识的工程师的参考。

这本书是前端工程师的宝贵资源，同时也为希望确保代码在项目中无缝协作的后端工程师提供了学习指南。

# 约定

在这本书中，你会发现有许多文本样式，用以区分不同类型的信息。以下是这些样式的一些示例及其含义解释。

文本中的代码词汇、数据库表名、文件夹名、文件名、文件扩展名、路径名、假网址、用户输入和 Twitter 账号等，将按如下格式展示：“`click`函数将调用（或执行）我们传递给它的回调函数。”

一段代码如下所示：

```js
Q.fcall(imException)
.then(
    // first handler-fulfill
    function() { },

);
```

任何命令行输入或输出都将按如下格式书写：

```js
D:\> node –v
D:\> NPM  –v

```

**新术语**和**重要词汇**以粗体显示。您在屏幕上看到的词汇，例如在菜单或对话框中，将以如下格式出现在文本中：“它应该变成绿色并显示**成功**消息。”

### 注意

警告或重要说明以如下框格式出现。

### 提示

技巧和建议将如此展示。

# 读者反馈

读者反馈对我们来说总是受欢迎的。让我们知道您对这本书的看法——您喜欢或不喜欢的地方。读者反馈对我们很重要，因为它帮助我们开发出您会真正从中获益的标题。

要给我们一般性反馈，只需电子邮件`<feedback@packtpub.com>`，并在消息主题中提及书籍的标题。

如果您在某个话题上有专业知识，并且有兴趣撰写或贡献一本书，请查看我们的作者指南：[www.packtpub.com/authors](http://www.packtpub.com/authors)。

# 客户支持

既然您已经成为 Packt 书籍的骄傲所有者，我们有很多事情可以帮助您充分利用您的购买。

## 下载示例代码

您可以从您在[`www.packtpub.com`](http://www.packtpub.com)的账户上下载所有您购买的 Packt Publishing 书籍的示例代码文件。如果您在其他地方购买了这本书，您可以访问[`www.packtpub.com/support`](http://www.packtpub.com/support)并注册，以便将文件直接通过电子邮件发送给您。

## 错误

虽然我们已经尽一切努力确保我们内容的准确性，但错误确实会发生。如果您在我们的某本书中发现错误——可能是文本或代码中的错误——我们将非常感谢您能向我们报告。这样做，您可以节省其他读者的挫折感，并帮助我们提高本书后续版本的质量。如果您发现任何错误，请通过访问[`www.packtpub.com/submit-errata`](http://www.packtpub.com/submit-errata)，选择您的书籍，点击**错误提交****表单**链接，并输入您错误的详细信息。一旦您的错误得到验证，您的提交将被接受，错误将被上传到我们的网站，或添加到该标题的错误部分现有的错误列表中。

要查看之前提交的错误，请前往[`www.packtpub.com/books/content/support`](https://www.packtpub.com/books/content/support)，在搜索框中输入书籍名称。所需信息将在**错误**部分下出现。

## 盗版

互联网上版权材料的盗版是一个持续存在的问题，涵盖所有媒体。在 Packt，我们非常重视我们版权和许可的保护。如果您在互联网上以任何形式发现我们作品的非法副本，请立即提供给我们位置地址或网站名称，以便我们可以寻求补救措施。

请通过`<copyright@packtpub.com>`联系我们，附上疑似盗版材料的链接。

我们感谢您在保护我们的作者和我们提供有价值内容的能力方面所提供的帮助。

## 问题

如果您在本书的任何方面遇到问题，您可以联系`<questions@packtpub.com>`，我们会尽力解决问题。