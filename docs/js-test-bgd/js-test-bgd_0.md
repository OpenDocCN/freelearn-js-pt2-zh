# 序言

在今天 Web 2.0 的世界中，JavaScript 是网络开发的重要部分。尽管市场上有很多 JavaScript 框架，但学会在没有框架帮助的情况下编写、测试和调试 JavaScript，会使你成为一个更好的 JavaScript 开发者。然而，测试和调试可能既耗时又繁琐，令人痛苦。这本书将通过提供各种测试策略、建议和工具指南，让你的测试变得顺畅和简单，从而减轻你的烦恼。

本书采用易于跟随的、逐步的教学风格，以便最大化你的学习。你将首先学习到你作为 JavaScript 开发者最常遇到的不同的错误类型。你还将通过我们易于跟随的示例，学习到 JavaScript 的最重要特性。

随着学习，你将通过验证学会如何编写更好的 JavaScript 代码；仅仅学习如何编写验证过的代码就会极大地提高你作为 JavaScript 开发者的能力，最重要的是，帮助你编写运行得更好、更快、且错误更少的 JavaScript 代码。

随着我们的 JavaScript 程序变得更大，我们需要更好的方法来测试我们的 JavaScript 代码。你将学习到各种测试概念以及如何在你的测试计划中使用它们。之后，你将学习如何为你的代码实施测试计划。为了适应更复杂的 JavaScript 代码，你将了解更多关于 JavaScript 的内置特性，以便识别和捕捉不同类型的 JavaScript 错误；这些信息有助于找到问题的根源，从而采取行动。

最后，你将学习如何利用内置浏览器工具和其他外部工具来自动化你的测试过程。

# 本书内容涵盖

第一章，*什么是 JavaScript 测试？*，涵盖了 JavaScript 在网络开发中的作用以及 HTML 和 CSS 等基本构建模块。它还涵盖了您最常见到的错误类型。

第二章，*JavaScript 中的即兴测试和调试*，介绍了我们为何要为我们的 JavaScript 程序进行即兴测试，并通过编写一个简单的程序，介绍 JavaScript 最常用的特性。这个程序将作为一个例子，用于执行即兴测试。

第三章，*语法验证*，介绍了如何编写验证过的 JavaScript。完成这一章后，您将提高作为 JavaScript 开发者的技能，同时理解更多关于验证在测试 JavaScript 代码中的作用。

第四章，*规划测试*，介绍了制定测试计划的重要性，以及我们在执行测试时可以使用的策略和概念。本章还涵盖了各种测试策略和概念，我们将通过一个简单的测试计划来看看制定测试计划意味着什么。

第五章，*将测试计划付诸行动*，紧接着第四章，我们应用我们已经制定的简单测试计划。最重要的是，我们将通过揭示错误、记录并应用我们在第四章中学到的理论来修复错误。

第六章，*测试更复杂的代码*，介绍了测试我们代码的高级方法。测试代码的一种方式是使用 JavaScript 提供的内置错误对象。本章还介绍了如何使用控制台日志，如何编写自己的消息，以及如何捕获错误。

第七章，*调试工具*，讨论了我们的代码变得太大而复杂，无法使用手动方法进行测试的情况。我们现在可以利用市场上流行浏览器提供的调试工具，包括 Internet Explorer 8、FireFox 3.6、Chrome 5.0、Safari 4.0 和 Opera 10.

第八章，*测试工具*，介绍了你可以使用免费、跨浏览器和跨平台的测试工具来自动化你的测试。它还涵盖了如何测试你的界面，自动化测试，以及进行断言和基准测试。

# 本书所需准备

像 Notepad++这样的基本文本编辑器。

浏览器，如 Internet Explorer 8、Google Chrome 4.0、Safari 4.0 和更新版本、FireFox 3.6。

JavaScript 版本 1.7 或更高版本。

其他涉及到的软件还包括 Sahi、JSLitmus 和 QUnit。

# 本书面向人群

本书适合初学者 JavaScript 程序员，或者可能只有少量使用 JavaScript 经验的初学者程序员，以及希望学习 HTML 和 CSS 的人。

# 约定

在本书中，你会发现几个频繁出现的标题。

为了清楚地说明如何完成一个程序或任务，我们使用：

# 动手时间—标题

1.  行动 1

1.  行动 2

1.  行动 3

说明往往需要一些额外的解释，以便它们有意义，所以它们后面跟着：

## 刚才发生了什么？

这个标题解释了你刚刚完成的工作或指令的工作原理。

你还会发现书中有一些其他的学习辅助工具，包括：

## 互动测验—标题

这些是旨在帮助你测试自己理解程度的简短多项选择题。

## 试一试英雄—标题

这些部分设定了实际挑战，并给你提供了一些实验你学到的内容的点子。

你还会发现有一些文本样式，用于区分不同类型的信息。以下是一些这些样式的示例及其含义解释。

文本中的代码词汇如下所示展示："我们可以通过使用`include`指令包含其他上下文。"

一段代码如下所示：

```js
<input type="submit" value="Submit"
onclick="amountOfMoneySaved(moneyForm.money.value)" />
</form>
</body>
</html>

```

当我们要引您注意代码块中的某个特定部分时，相关的行或项目会被加粗：

```js
function changeElementUsingName(a){
var n = document.getElementsByName(a); 
for(var i = 0; i< n.length; i++){
n[i].setAttribute("style","color:#ffffff");
}
}

```

**新术语**和**重要词汇**以粗体显示。例如，您在屏幕上、菜单或对话框中看到的词汇，在文本中会这样显示："点击**下一页**按钮将您带到下一页"。

### 注意

警告或重要说明会以这样的盒子形式出现。

### 注意

技巧和窍门会这样展示。

# 读者反馈

我们总是欢迎读者的反馈。告诉我们您对这本书的看法——您喜欢或可能不喜欢的地方。读者的反馈对我们来说非常重要，以便我们开发出您真正能从中获得最大收益的标题。

要给我们发送一般性反馈，只需发送一封电子邮件到`<feedback@packtpub.com>`，并在您消息的主题中提到书名。

如果您需要一本书，并希望我们出版，请通过[www.packtpub.com](http://www.packtpub.com)上的**建议一个标题**表单给我们发个消息，或者发送电子邮件到`<suggest@packtpub.com>`。

如果您在某个主题上有专业知识，并且您有兴趣撰写或为书籍做出贡献，请查看我们作者指南中的[www.packtpub.com/authors](http://www.packtpub.com/authors)。

# 客户支持

现在您已经成为 Packt 书籍的骄傲拥有者，我们有很多事情可以帮助您充分利用您的购买。

### 注意

**下载本书的示例代码**

您可以从您在[`www.PacktPub.com`](http://www.PacktPub.com)账户上下载您购买的所有 Packt 书籍的示例代码文件。如果您在其他地方购买了这本书，您可以访问[`www.PacktPub.com/support`](http://www.PacktPub.com/support)并注册，以便将文件直接通过电子邮件发送给您。

## 错误

虽然我们已经尽一切努力确保我们内容的准确性，但是错误仍然会发生。如果您在我们的某本书中发现了一个错误——可能是文本或代码中的错误——如果您能向我们报告，我们将非常感激。这样做，您可以节省其他读者不必要的挫折，并帮助我们改进本书的后续版本。如果您发现任何错误，请通过访问[`www.packtpub.com/support`](http://www.packtpub.com/support)，选择您的书，点击**让我们知道**链接，并输入您错误的详情。一旦您的错误得到验证，您的提交将被接受，错误将被上传到我们的网站，或添加到该标题的错误部分现有的错误列表中。您可以通过从[`www.packtpub.com/support`](http://www.packtpub.com/support)选择您的标题来查看现有的错误。

## 盗版

互联网上侵犯版权材料的问题持续存在，遍布所有媒体。在 Packt，我们对保护我们的版权和许可非常重视。如果您在互联网上以任何形式发现我们作品的非法副本，请立即提供给我们位置地址或网站名称，这样我们可以采取补救措施。

请通过`<copyright@packtpub.com>`联系我们，并提供疑似侵权材料的链接。

我们感谢您在保护我们的作者，以及我们为您提供有价值内容的能力方面所提供的帮助。

## 问题

如果您在阅读本书的任何方面遇到问题，可以通过`<questions@packtpub.com>`联系我们，我们会尽力解决。
