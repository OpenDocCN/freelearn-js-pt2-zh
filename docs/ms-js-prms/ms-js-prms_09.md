# 第九章：JavaScript - 未来已来

在之前的章节中，我们重点关注了如何精通地将承诺的概念应用于不同的 JavaScript 库，以及如何在未来的项目中取得最大的优势。然而，这不仅仅是关于 JavaScript。

尽管承诺很大，它们的实现可以带来许多好处，但这不是 JavaScript 的终点。实际上，JavaScript 在未来几年里能给我们带来比我们能想到的更多的东西。它是现代时代的进步语言，并且其受欢迎程度日益增加。JavaScript 还能给我们提供什么？我们将在本章中尝试找出答案。

让我们从 ECMAScript 6 开始。

# ECMAScript 6（ECMA 262）

ECMAScript 语言规范已进入第六版。自从其第一个版本于 1997 年发布以来，ECMAScript 已成为世界上广泛采用的一般目的编程语言之一。它以其能够嵌入自身到网络浏览器中以及其能够使用服务器端和嵌入式应用程序的能力而闻名。

许多人认为第六版是自 1997 年 ECMAScript 成立以来最详细、最广泛涵盖的更新。

我们将讨论 ECMA 262 的第六版；这是一个草案版本，旨在更好地支持大型应用、库的创建，以及将 ECMAScript 作为其他语言的编译目标。

# harmony:generators

`harmony:generators`是一等公民面包丁，将作为对象表示，这将封装悬挂的执行上下文（即，函数激活）。到目前为止，这些仍在审查中，可能会发生变化，所以我们只是考虑这些以获得关于它们的知识。

几个高级示例将有助于更好地理解一旦获得批准后和谐形状会是什么样子。

由于这些是未经批准的草案，我们将使用来自 ECMAScript 母网站的示例。

本节中将使用的参考代码可以在[`wiki.ecmascript.org/doku.php?id=harmony:generators`](http://wiki.ecmascript.org/doku.php?id=harmony:generators)找到。

## 斐波那契数列

斐波那契数的“无限”序列是：

```js
Function* Fibonacci () {
    let [prev, curr] = [0, 1];
    For (;;) {
        [prev, curr] = [curr, prev + curr];
        yield curr;
    }
}
```

生成器可以在循环中迭代：

```js
for (n of fibonacci()) {
    // truncate the sequence at 1000
    if (n > 1000)
        break;
    print(n);
}
```

生成器如以下代码所示是迭代器：

```js
let seq = fibonacci();
print(seq.next()); // 1
print(seq.next()); // 2
print(seq.next()); // 3
print(seq.next()); // 5
print(seq.next()); // 8
```

前面的片段是非常高级的语法，它们很可能会被修改。生成器将是和谐的关键元素和显著添加，但完全实现它们需要时间。

# MEAN 栈

尽管 MEAN 堆栈不是一个新概念，但它为我们提供了 JavaScript 的一切基础。它提供了基于 Node.js 的 JavaScript 基础网络服务器，基于 MongoDB 的数据库，其中也使用 JavaScript 作为核心语言，Express.js 作为 Node.js 网络应用程序框架，以及 Angular.js 作为可以让你以更先进、更现代的方式扩展 HTML 的前端元素。

这些概念已经存在一段时间，但它们有超越想象的潜力。想象一个全面规模的金融应用程序，或者一个基于 MEAN 堆栈的整个银行系统，或者控制工业。硬件将利用这个堆栈的服务，但这种情况将在不久的将来发生，现在还不晚，但仍需要时间来完全实施这个堆栈。

我之所以这样说，是因为企业界仍然不愿意采用 MEAN 标准或向其过渡，原因是这些开源产品的成熟度和财务支持水平。此外，他们还需要升级现有的基础设施。无论出于什么原因，当今的网络应用程序都大量使用这个堆栈来编写轻量级和可扩展的应用程序。让我们把 MEAN 堆栈作为 JavaScript 未来的第一个项目。

# 实时通信在 JavaScript 中

另一个被认为将是 JavaScript 未来的强大功能是两个套接字之间的实时通信。在 JavaScript 之前，套接字编程已经存在很长时间，以至于每种主要编程语言都有其使用套接字读写数据的版本，但与 JavaScript 相比，这还是一个需要在这个阶段做大量工作的新概念。有几种方法可以在 JavaScript 中实现实时套接字编程，但目前最成熟的方法是使用 Socket.IO。

它基本上实现了一种双向基于事件的实时通信，从而使两个实体之间的通信成为可能。它支持各种平台，包括网络浏览器、便携式设备、移动设备以及具有通信功能的其他任何设备。它的实施相当容易且可靠，具有高质量和高速度。

我们能用这个实现什么？嗯，可能性很多，这取决于你如何尝试基于 Socket.IO 提供的支持。在这一点上，你可以为企业智能或市场预测或趋势识别编写实时分析，或者你可以使用它的二进制流功能，从地球的一部分实时流媒体传输到另一部分，或者你可以用它来远程监控你的场所。所有这些实施方法现在已经可用，通过聪明地使用这些功能，这些想法可以变为现实。

结论是 Socket.IO 是您可以依赖的最健壮的实时通信库之一。从当前趋势来看，我们可以有把握地说，设备之间的实时通信可能是 JavaScript 在未来最大的优势之一。这并不一定必须通过 Socket.IO 实现；任何有潜力的库都将占据主导地位。这是关于 JavaScript 在未来几年将如何给我们留下深刻印象的概念。

# 物联网

不久前，硬件与设备和机器的接口还只限于某些成熟和发达的编程语言，没有人会考虑 JavaScript 能否与这些成熟语言站在同一条线上。这种现状局限于 C++或 Java 或其他高级语言，但这种情况已经不再存在了。

随着对 JavaScript 的关注，开发者和工程师们现在正试图在硬件接口中使用 JavaScript 的力量。他们通过编写智能代码和使用已经使用某种程度通信的库来克服 JavaScript 的问题。

这样一个努力叫做树莓派。我们来谈谈树莓派及其目的，然后我们再看看 JavaScript 是如何使用它的。

树莓派是一种设计简单的信用卡式计算机，用于以非常简单和有效的方式学习编程。它带有一个你可以称之为没有外设连接的计算机的主板。你必须连接鼠标、键盘和屏幕才能使其运行。它有一个安装在 SD 卡上的操作系统，可供实验使用。这是一种便携式设备，你可以连接任何设备，或者使用它编程其他设备。它拥有计算机必须具备的所有基本元素，但以非常简单、便携和易于处理的方式实现。

现在，JavaScript 与树莓派有什么关系呢？嗯，JavaScript 现在无处不在，所以它的实现也已经开始了，使用 Pijs.io 为树莓派提供支持。

就像你可以用其他任何语言为树莓派编写代码一样，你也可以使用 JavaScript 为你的手持计算机编写应用程序。这个 JavaScript 库将允许你使用 JavaScript 与硬件交互并为你的需求编程设备。你可以查看该库在[`pijs.io/`](http://pijs.io/)。

如前所述，硬件接口不仅限于树莓派；其他任何实施方法都必须做同样的事情。这些线路的核心是展示 JavaScript 变得多么强大以及它被广泛接受的程度。现在，人们正在考虑用它来编程他们的设备，无论这些设备属于他们的日常使用还是商业使用。JavaScript 在计算机硬件接口方面的未来非常光明，并且正在快速增长。

# 计算机动画和 3D 图形

1996 年，在一部革命性的电影《玩具总动员》中，引入了一个全新的概念**计算机生成的图像**（**CGI**）。这部电影在动画和计算机图形方面树立了新的标准。这部电影的成功不仅仅是因为它的剧本，还得益于用来建造它的技术。

在当前时代，计算机动画领域从许多方面得到了发展，并且仍在以快速的速度增长。那么 JavaScript 与所有这些进展有什么关系呢？嗯，JavaScript 前所未有的准备好通过 Web 在计算机动画和 3D 图形中发挥作用。

WebGL 是一个开源的 JavaScript API，用于渲染 2D 和 3D 图像和对象。WebGL 的力量在于它通过采用浏览器及其引擎的标准，扩展到几乎每一个浏览器。它非常适应性强，可以用于任何现代网络浏览器渲染所需的图像。

凭借 WebGL，现在可以编写无需额外插件即可运行的互动性和尖端游戏。它还将在未来帮助我们在浏览器中看到动画计算机建模，而不是使用沉重、昂贵且庞大的软件。它还将帮助我们在移动中可视化信息。所以，你可以看到股价上涨和下跌对其他你投资过的股票的影响。

到目前为止，WebGL 已经得到了包括苹果的 Safari、微软的 IE 11 及其后续版本 Edge 浏览器、谷歌的 Chrome 浏览器以及 Mozilla 的 Firefox 在内的所有行业关键角色的支持。另外，请注意，WebGL 是 Mozilla 的 Vladimir Vukićević的创意，他在 2011 年发布了其初始版本。

我们可以得出结论，JavaScript 已经在动画和 3D 图形领域播下了种子，在不久的将来，这不仅有助于 JavaScript 获得信任，还将使许多开发者和工程师在面对当前语言包的限制时，能够更容易地学习新的语言。有了统一的语言，输出的应用程序将更有趣。

# NoSQL 数据库

曾经有一段时间，了解关系数据库管理系统（RDBMS）是所有开发者的必备知识，特别是那些从事数据库驱动应用程序开发的人。人们期望你了解主键是什么，连接是什么，如何规范化数据库，以及实体-关系图是什么。然而，这种情况正在逐渐消失，在今天的世界中，一个新的概念 NoSQL 正在兴起，其中大量数据驱动的应用程序仍在使用。

在前进之前，让我们谈谈为什么工程师们正在关注非关系型数据库（RDBMS）技术。原因很简单。数据驱动的应用程序以惊人的方式增长，它们在每天的每个小时都在全球产生数以 terabytes 的数据。处理这些数据并获得所需的结果并非易事。**数据库管理员**（**DBAs**）编写一个查询并执行它，从分布式的数据库存储库中获取数据，他们必须等待几小时才能知道结果是否显示在他们的屏幕上，或者由于操作符放置不当而使所有努力付之东流。这是由于 RDBMS 的设计方式，但在当今的现代世界中，这种延迟和计算时间会让你付出巨大的代价和声誉。

那么还有其他选择吗？非关系型数据库！在本章的前一部分，我们已经看到 MongoDB 在 MEAN 堆栈中发挥了关键作用。然而，在这里再给 MongoDB 多写几行是值得的，因为它是我们在 JavaScript 未来增长方面的候选人。

那么 MongoDB 是什么呢？它是一个面向文档的非关系型数据库，具有跨平台的适应性，支持类似于 JSON 的文档。截至 2015 年 2 月，它是世界上第四受欢迎的数据库管理系统，被认为是世界上最受欢迎的数据存储。

我们为什么把 MongoDB 列入我们未来 JavaScript 增长的候选人名单中呢？仅仅因为它基于 JavaScript，你可以在其控制台中用纯 JavaScript 编写脚本。这使得它成为一种基于 JavaScript 的高度可适应的数据库技术。它的发展方式不仅会取代当前的 RDBMS 场景，而且与 MEAN 堆栈的其他部分或硬件接口或网络或与 Socket.IO 结合时，也会产生奇妙的效果。

无论以何种形式，MongoDB 都将帮助其余的应用程序在未来增长，并将现有的 RDBMS 转变为更易于访问和快速响应的引擎。

# 总结

在本章中，我们了解到 JavaScript 是一个改变游戏规则的语言，它有着光明的未来。JavaScript 具有巨大的潜力和适应性，这将成为计算机科学几乎所有领域下一个级别的使用。可能性是无限的，天空是 JavaScript 的极限。在不久的将来，由于其适应性、接受度以及成千上万的开发者和坚定承诺的大型软件公司的贡献，JavaScript 将主导其他编程语言。

至此，我们结束了这本书。

让我们回顾一下在这本书中学到的内容。在开始时，我们深入探讨了 JavaScript 是什么以及它的起源，JavaScript 的结构以及不同浏览器是如何使用它的。我们还看到了不同的编程模型以及 JavaScript 正在使用的模型。

然后，我们的旅程转向了本书的核心——Promises.js。我们学到了很多关于承诺基本知识，这使我们走向了这个概念的高级用法。我们接着从不同的技术角度了解了它，并展示了代码以消除任何模糊之处。

所以，总的来说，这本书不仅关于 JavaScript 中的承诺，而且涵盖了 JavaScript 和承诺的历史、实现和用法。有了这本书，你不仅可以成为承诺的大师，还可以保持独特的理解水平，从而以更多、更亮的方式实现这个概念。

学习愉快！
