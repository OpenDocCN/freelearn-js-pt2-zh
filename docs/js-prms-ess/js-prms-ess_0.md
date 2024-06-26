# 序言

《JavaScript 承诺精要》是一本关于承诺这一新概念的实用指南。它提供了一个单一资源，替代了关于这个主题的所有分散信息。它详细介绍了将增强我们用 JavaScript 进行异步编程的新标准。这本书是对承诺 API 及其特性的简要但简洁的解释，以及如何在 JavaScript 编程中使用它。它涵盖了 JavaScript 承诺的基本要素，触及了在新学习中最重要的细节，并提供了一些在不同方面非常有用的提示。

承诺在大多数情况下是一个编程概念，提供了一个允许开发人员安排在尚不存在数据和值上执行工作的流程，并允许他们在未来不确定的时间点处理这些值（异步）。它还提供了一个抽象，用于处理与异步 API 的通信。目前，可以通过回调、定时器和事件在 JavaScript 中实现异步调用，但所有这些都有局限性。承诺解决了实际开发中的头痛问题，并允许开发人员与传统的做法相比，以更本地的方式处理 JavaScript 的异步操作。此外，承诺在同步功能和异步函数之间提供了一个直接的对应关系，特别是在错误处理层面。一些库已经开始使用承诺，并提供了承诺的健壮实现。你可以在许多库以及在与 Node.js 和 WinRT 交互时找到承诺。学习承诺的实现细节将帮助你避免在异步 JavaScript 世界中出现的大量问题，并构建更好的 JavaScript API。

# 本书内容概览

第一章，*JavaScript 承诺 – 我为什么要关心？*，介绍了 JavaScript 的异步编程世界以及承诺在这个世界中的重要性。

第二章，*承诺 API 及其兼容性*，带你深入了解承诺 API 的更多细节。我们还将学习当前浏览器对承诺标准的支持情况，并查看实现承诺和承诺类似特性的 JavaScript 库。

第三章，*承诺的链式调用*，向你展示了承诺如何允许轻松地链式调用异步操作以及这涵盖了什么。这一章还涵盖了如何排队异步操作。

第四章, *错误处理*, 涵盖了 JavaScript 中的异常和错误处理。本章还将解释承诺如何使错误处理变得更容易和更好。

第五章, *WinJS 中的承诺*, 探讨了 WinJS.Promise 对象及其在 Windows 应用程序开发中的使用。

第六章, *综合运用——承诺的实际应用*, 向你展示了承诺的实际应用以及我们如何在将学到的一切综合运用的场景中使用承诺。

# 本书需要什么准备

为了实现本书中你将学习的内容，你只需要一个 HTML 和 JavaScript 编辑器。你可以从以下选项中选择：

+   Microsoft Visual Studio Express 2013 for Web：这提供了功能齐全的标记和代码编辑器。

+   WebMatrix：这是运行示例代码的另一种选择。它是一个免费、轻量级、云连接的 Web 开发工具，利用最新的 Web 标准和流行的 JavaScript 库。

+   jsFiddle：这是一个在线 Web 编辑器，允许你编写 HTML 和 JavaScript 代码，并直接在浏览器中运行它。

# 本书适合谁

本书面向所有涉及 JavaScript 编程的开发人员，无论是 Web 开发还是如 Node.js 和 WinRT 等技术，这些技术都大量使用异步 API。此外，本书还针对那些想学习 JavaScript 中异步编程以及新标准将如何让这种体验变得更好的开发人员。简而言之，本书适合所有想要学习异步编程的新手，这个新手的名字叫做 JavaScript Promise。

# 约定

在本书中，你会发现有多种文本样式，用于区分不同类型的信息。以下是一些这些样式的示例及其含义。

文本中的代码单词、数据库表名、文件夹名、文件名、文件扩展名、路径名、假 URL、用户输入和 Twitter 处理显示如下："我们可以使用`object.addEventListener()`方法实现这种基于事件的技术。"

代码块如下所示：

```js
var testDiv = document.getElementById("testDiv");
testDiv.addEventListener("click", function(){
  // so the testDiv object has been clicked on, now we can do things
    alert("I'm here!");
});
```

**新术语**和**重要词汇**以粗体显示。例如，在屏幕上的菜单或对话框中看到的词汇，在文本中以这种方式出现：“随意命名应用程序，然后点击**确定**。”

### 注意

警告或重要注释以这样的盒子出现。

### 提示

技巧和小窍门像这样出现。

# 读者反馈

我们的读者反馈总是受欢迎的。请告诉我们你对这本书的看法——你喜欢或可能不喜欢的地方。读者反馈对我们开发您真正能从中获得最大收益的标题很重要。

如果您想给我们发送一般性反馈，只需发送一封电子邮件到`<feedback@packtpub.com>`，并通过邮件主题提及书籍标题。

如果您在某个话题上有专业知识，并且有兴趣撰写或为书籍做出贡献，请查看我们网站上的作者指南[www.packtpub.com/authors](http://www.packtpub.com/authors)。

# 客户支持

现在您已经成为 Packt 书籍的骄傲所有者，我们有很多事情可以帮助您充分利用您的购买。

## 错误

虽然我们已经竭尽全力确保内容的准确性，但错误仍然无法避免。如果您在我们的某本书中发现错误——可能是文本或代码中的错误——如果您能将这些错误告知我们，我们将不胜感激。这样做可以避免其他读者遭受挫折，并帮助我们改进此书的后续版本。如果您发现任何错误，请通过访问[`www.packtpub.com/submit-errata`](http://www.packtpub.com/submit-errata)报告，选择您的书籍，点击**错误报告****提交****表单**链接，并输入您错误的详细信息。一旦您的错误得到验证，您的提交将被接受，并且错误将被上传到我们的网站，或添加到该标题下的现有错误列表中。您可以通过从[`www.packtpub.com/support`](http://www.packtpub.com/support)选择您的标题来查看现有的错误。

## 盗版

互联网上版权材料的盗版是一个持续存在的问题，涵盖所有媒体。 Packt 对我们版权和许可的保护非常重视。如果您在互联网上以任何形式发现我们作品的非法副本，请立即提供给我们地址或网站名称，以便我们可以寻求补救措施。

如果您发现任何疑似盗版的内容，请通过`<copyright@packtpub.com>`联系我们。

我们非常感谢您在保护我们的作者和我们提供有价值内容的能力方面所提供的帮助。

## 问题

如果您在阅读书籍过程中遇到任何问题，可以通过`<questions@packtpub.com>`联系我们，我们会尽力解决。
