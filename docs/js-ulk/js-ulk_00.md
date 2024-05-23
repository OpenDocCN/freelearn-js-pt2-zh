# 序言

JavaScript 是在最不恰当的时候——浏览器大战时期——作为脚本语言诞生的。它被忽视和误解了十年，经历了六个版本。现在看看它！JavaScript 已经成为一种主流编程语言。它在各个方面都有先进的使用：在大型客户端开发、服务器脚本、桌面应用、原生移动编程、游戏开发、数据库查询、硬件控制和操作系统自动化中。JavaScript 获得了许多子集，如 Objective-J、CoffeeScript、TypeScript 等。JavaScript 非常简洁，是一种表达性语言。它具有基于原型的面向对象编程、对象组合和继承、可变参函数、事件驱动编程和非阻塞 I/O 等特点。然而，为了发挥 JavaScript 的真正威力，我们需要对其语言特性有深入的理解。此外，在 JavaScript 开发过程中，我们会注意到它众多的陷阱，我们需要一些技巧来避免它们。以前被称为 EcmaScript Harmony 的项目，最近在名为 EcmaScript 2015 的规范中最终确定，通常被称为 ES6。这不仅将语言提升到下一个层次，还引入了许多需要关注的新技术。

本书旨在引导读者了解 JavaScript 即将推出和现有的特性。它充满了针对常见编程任务的代码食谱。这些任务提供了针对经典 JavaScript（ES5）以及下一代语言（ES6-7）的解决方案。本书关注的不仅仅是浏览器中的语言，还提供了编写高效 JavaScript 的基本知识，用于桌面应用、服务器端软件和原生模块应用。作者的最终目标是不仅描述语言，还要帮助读者改进他们的代码，以提高可维护性、可读性和性能。

# 本书内容涵盖

第一章，*深入 JavaScript 核心*，讨论了提高代码表达性的技术，掌握多行字符串和模板化，以及操作数组和类数组对象的方法。这一章解释了如何利用 JavaScript 原型而不损害代码的可读性。此外，这一章介绍了 JavaScript 的“魔法方法”，并给出了它们的实际使用示例。

第二章，*使用 JavaScript 的模块化编程*，描述了 JavaScript 中的模块化：模块是什么，为什么它们很重要，异步和同步加载模块的标准方法，以及 ES6 模块是什么。这一章展示了如何在服务器端 JavaScript 中使用 CommonJS 模块，以及如何为浏览器预编译它们。它详细介绍了如何将异步和同步方法结合起来，以实现更好的应用程序性能。它还解释了如何使用 Babel.js 为生产环境填充 ES6 模块。

第三章，*DOM 脚本编程与 AJAX*，介绍了文档对象模型（DOM），展示了最小化浏览器重绘的最佳实践，并在操作 DOM 时提高应用程序性能。这一章还比较了两种客户端服务器通信模型：XHR 和 Fetch API。

第四章，*HTML5 APIs*，考虑了浏览器持久化 API，如 Web 存储、IndexDB 和文件系统。它介绍了 Web 组件，并概述了创建自定义组件的过程。这一章描述了服务器到浏览器通信 API，如 SSE 和 WebSockets。

第五章，*异步 JavaScript*，解释了 JavaScript 的非阻塞性质，阐述了事件循环和调用栈。这一章考虑了异步调用链的流行风格以及错误处理。它介绍了 ES7 的 async/await 技术，并给出了使用 Promise API 和 Async.js 库并行和顺序运行任务的例子。它描述了节流和防抖的概念。

第六章，*大型 JavaScript 应用程序架构*，重点是代码可维护性和架构。这一章介绍了 MVC 范式及其变体，MVP 和 MVVM。它还通过 Backbone.js、AngularJS 和 ReactJS 等流行框架的示例，展示了如何实现关注分离。

第七章，*JavaScript 浏览器之外的应用*，解释了如何在 JavaScript 中编写命令行程序以及如何使用 Node.js 构建 Web 服务器。它还涵盖了使用 NW.js 创建桌面 HTML5 应用程序和指导使用 Phongap 开发原生移动应用程序的内容。

第八章，*调试和剖析*，深入探讨了 bug 的检测和隔离。它检查了 DevTools 的容量和 JavaScript 控制台 API 的一些不太知名的功能。

# 您需要什么

只要你有一个现代浏览器和一个文本编辑器，就可以运行书中的示例。然而，使用类似 Firefox Scratchpad 的浏览器工具可能会有所帮助，以直接在浏览器中编辑示例代码。（[`developer.mozilla.org/en-US/docs/Tools/Scratchpad`](https://developer.mozilla.org/en-US/docs/Tools/Scratchpad)）书中还包含了一些依赖于浏览器尚未支持的 ES6/ES7 特性的代码示例。你可以在[`babeljs.io/repl/`](https://babeljs.io/repl/)上使用 Babel.js 的在线沙盒运行这些示例。

你将在涉及 Node.js、NW.js、PhoneGap、JavaScript 框架和 NPM 包的章节中找到详细的设置开发环境和安装所需工具和依赖项的说明。

# 本书适合谁

这本书适合那些已经熟悉 JavaScript 并且想要提高技能以充分利用这门语言的开发者。本书以实践为导向，对于那些习惯于“边做边学”方法的人来说会有帮助，因为主题通过真实示例和教程进行了彻底的讲解。

# 约定

在这本书中，你会发现有许多文本样式用来区分不同类型的信息。以下是一些这些样式的示例及其含义的解释。

文本中的代码词、数据库表名、文件夹名、文件名、文件扩展名、路径名、假 URL、用户输入和 Twitter 处理方式如下所示："我们可以通过使用`include`指令来包含其他上下文。"

代码块如下所示设置：

```js
var res = [ 1, 2, 3, 4 ].filter(function( v ){
 return v > 2;
})
console.log( res ); // [3,4]
```

当我们希望吸引您对代码块的特定部分注意时，相关行或项目以粗体显示：

```js
/**
* @param {Function} [cb] - callback
*/
function fn( cb ) {
 cb && cb();
};
```

任何命令行输入或输出如下所示：

```js
npm install fs-walk cli-color

```

**新术语**和**重要词汇**以粗体显示。例如，在菜单或对话框中出现的屏幕上的词，在文本中如下所示："一旦按下*Enter*，控制台输出**I'm running**。"

### 注意

警告或重要说明如下所示的盒子：

### 技巧

技巧和窍门就像这样出现。

# 读者反馈

读者反馈对我们来说总是受欢迎的。让我们知道你对这本书的看法——你喜欢或不喜欢什么。读者反馈对我们很重要，因为它帮助我们开发出你能真正从中受益的标题。

要给我们发送一般性反馈，只需发送电子邮件`<feedback@packtpub.com>`，并在消息主题中提到书名。

如果您在某个主题上有专业知识，并且有兴趣撰写或贡献一本书，请查看我们的作者指南[www.packtpub.com/authors](http://www.packtpub.com/authors)。

# 客户支持

您现在拥有了一本 Packt 图书，我们有很多方法可以帮助您充分利用您的购买。

## 下载示例代码

您可以从您在[`www.packtpub.com`](http://www.packtpub.com)的账户上下载您购买的所有 Packt Publishing 图书的示例代码文件。如果您在其他地方购买了这本书，您可以访问[`www.packtpub.com/support`](http://www.packtpub.com/support)并注册，以便将文件直接通过电子邮件发送给您。

## 勘误

尽管我们已经尽一切努力确保我们的内容的准确性，但错误仍然会发生。如果您在我们的书中发现了一个错误——可能是文本或代码中的错误——我们将非常感谢您能向我们报告。这样做可以节省其他读者的挫折感，并帮助我们改进本书的后续版本。如果您发现任何勘误，请通过访问[`www.packtpub.com/submit-errata`](http://www.packtpub.com/submit-errata)，选择您的书籍，点击**勘误提交表单**链接，并输入您的勘误详情。一旦您的勘误得到验证，您的提交将被接受，勘误将被上传到我们的网站，或添加到该标题下的现有勘误列表中。

要查看以前提交的勘误，请前往[`www.packtpub.com/books/content/support`](https://www.packtpub.com/books/content/support)并在搜索字段中输入书籍的名称。所需信息将在**勘误**部分下出现。

## 盗版

互联网上版权材料的盗版是一个持续存在的问题，涵盖所有媒体。在 Packt，我们非常重视我们版权和许可证的保护。如果您在互联网上以任何形式发现我们作品的非法副本，请立即提供给我们位置地址或网站名称，以便我们可以寻求解决方案。

如有怀疑的侵权材料，请联系我们`<copyright@packtpub.com>`。

我们感谢您在保护我们的作者和我们提供有价值内容的能力方面所提供的帮助。

## 问题

如果您在阅读本书时遇到任何问题，可以通过`<questions@packtpub.com>`联系我们，我们会尽力解决问题。