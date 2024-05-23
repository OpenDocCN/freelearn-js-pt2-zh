# 第一章：*第三章*：运行时和标准库

现在我们已经足够了解 Deno，我们可以用它来写一些真正的应用程序。在本章中，我们将不使用任何库，因为其主要目的是介绍运行时 API 和标准库。

我们将编写小的 CLI 工具、Web 服务器等，始终利用官方 Deno 团队创建的力量，没有外部依赖。

我们的起点将是 Deno 命名空间，因为我们认为首先探索运行时包含的内容是有意义的。遵循这个想法，我们还将查看 Deno 与浏览器共享的 Web API。我们将使用`setTimeout`到`addEventListener`、`fetch`等。

仍然在 Deno 命名空间中，我们将了解程序的生命周期，与文件系统交互，并构建小的命令行程序。后来，我们将了解缓冲区，并了解如何使用它们异步地读写。

然后我们将快速转向标准库，并浏览一些有用的模块。本章旨在取代标准库的文档；它将为您提供一些其功能和使用案例的介绍，而我们在编写小程序的过程中将了解它。

在标准库的旅程中，我们将使用与文件系统、ID 生成、文本格式化和 HTTP 通信相关的模块。其中一部分将是介绍我们将在后续章节中深入探讨的内容。您将通过编写第一个 JSON API 并连接到它来完成本章。

以下是我们将在本章中涵盖的主题：

+   Deno 运行时

+   探索 Deno 命名空间

+   使用标准库

+   使用 HTTP 模块构建 Web 服务器

## 技术要求

本章的所有代码文件都可以在以下 GitHub 链接中找到：[`github.com/PacktPublishing/Deno-Web-Development/tree/master/Chapter03`](https://github.com/PacktPublishing/Deno-Web-Development/tree/master/Chapter03)。

# Deno 运行时

Deno 提供了一组函数，这些函数作为全局变量包含在`Deno`命名空间中。运行时 API 在[`doc.deno.land/`](https://doc.deno.land/)上进行文档化，可以用来做最基本、最底层的事情。

Deno 上有两种函数无需任何导入即可使用：Web API 和`Deno`命名空间。每当 Deno 中存在与浏览器中相同的行为时，Deno 会模仿浏览器 API——这些是 Web API。由于您来自 JavaScript 世界，您可能熟悉其中的大部分内容。我们谈论的是诸如`fetch`、`addEventListener`、`setTimeout`等函数，以及`window`、`Event`、`console`等对象等。

使用 Web API 编写的代码可以无需转换即可捆绑并在浏览器中运行。

运行时暴露的 API 的另一个大部分位于一个名为`Deno`的全局命名空间中。你可以使用 REPL 和文档，这两样我们在第二章《工具链》中探索过，来探索它并快速了解它包括哪些函数。在本章后面，我们还将尝试一些最常用的函数。

如果你想访问 Deno 中包含的所有符号的文档，你可以运行带有`--builtin`标志的`doc`命令。

## 稳定性

从版本 1.0.0 开始，Deno 命名空间内的函数被认为是稳定的。这意味着 Deno 团队将努力在更新的版本中支持它们，并尽最大努力使它们与未来的变化保持兼容。

仍然没有被认为是在生产中稳定的特性，正如你想象的那样，因为我们已经在之前的例子中使用过它们，都位于`--unstable`标志下。

不稳定模块的文档可以通过使用`doc`命令的`--unstable`标志访问，或者通过访问[`doc.deno.land/builtin/unstable`](https://doc.deno.land/builtin/unstable)来访问。

标准库尚未被 Deno 团队认为是稳定的，因此它们的版本与 CLI（在撰写本文时，版本为 0.83.0）不同。

与`Deno`命名空间函数相比，标准库通常不需要`--unstable`标志来运行，除非标准库中的任何模块正在使用来自`Deno`命名空间的不可稳定函数。

## 程序生命周期

Deno 支持与浏览器兼容的`load`和`unload`事件，可以用来运行设置和清理代码。

处理程序可以以两种不同的方式编写：使用`addEventListener`和通过重写`window.onload`和`window.onunload`函数。`load`事件可以是异步的，但`unload`事件不是真的，因为它们不能被取消。

使用`addEventListener`可以让你注册无限数量的处理程序；例如：

```js
addEventListener("load", () => {
  console.log("loaded 1");
});
addEventListener("unload", () => {
  console.log("unloaded 1");
});
addEventListener("load", () => {
  console.log("loaded 2");
});
addEventListener("unload", () => {
  console.log("unloaded 2");
});
console.log("Exiting...");
```

如果我们运行前面的代码，我们会得到以下输出：

```js
$ deno run program-lifecycle/add-event-listener.js
Exiting...
loaded 1
loaded 2
unloaded 1
unloaded 2
```

另一种在设置和清理阶段安排代码运行的方法是重写`window`对象上的`onload`和`onunload`函数。这些函数的特点是只有最后分配的一个会运行。这是因为它们会相互覆盖；例如以下代码：

```js
window.onload = () => {
  console.log("onload 1");
};
window.onunload = () => {
  console.log("onunload 1");
};
window.onload = () => {
  console.log("onload 2");
};
window.onunload = () => {
  console.log("onunload 2");
};
console.log("Exiting");
```

运行前面的程序后，我们得到了以下输出：

```js
$ deno run program-lifecycle/window-on-load.js
Exiting
onload 2
onunload 2
```

然后如果我们看看我们最初编写的代码，我们可以理解前两个声明被跟在它们后面的两个声明覆盖了。当我们覆盖`onunload`和`onload`时，就是这样发生的。

## 网络 API

为了证明我们可以像在浏览器上一样使用 Web API，我们将编写一个简单的程序，获取 Deno 网站的标志，将其转换为 base64，并在控制台打印一个包含图像 base64 的 HTML 页面。让我们按照以下步骤进行：

1.  从请求`https://deno.land/logo.svg`开始：

    ```js
    fetch("https://deno.land/logo.svg")
    ```

1.  将其转换为`blob`：

    ```js
    fetch("https://deno.land/logo.svg")
      .then(r =>r.blob())
    ```

1.  从`blob`对象中获取文本并将其转换为`base64`：

    ```js
    fetch("https://deno.land/logo.svg ")
      .then(r =>r.blob())
      .then(async (img) => {
        const base64 = btoa(
          await img.text()
        )
    });
    ```

1.  将 base64 图像打印到控制台的 HTML 页面中，并使用图像标签：

    ```js
    fetch("https://deno.land/logo.svg ")
      .then(r =>r.blob())
      .then(async (img) => {
    const base64 = btoa(
          await img.text()
        )
        console.log(`<html>
    <img src="img/svg+xml;base64,${base64}" />
    </html>
        `
        )
      })
    ```

    当我们运行这个时，我们得到了预期的输出：

    ```js
    $ deno run --allow-net web-apis/fetch-deno-logo.js
    <html>
      <img src="data:image/svg+xml;base64,PHN2ZyBoZWlnaHQ9Ijgx My4xODQiIHdpZHRoPSI4MTMuMTUiIHhtbG5zPSJodHRwOi8vd3d3Lncz Lm9yZy8yMDAwL3N2ZyI+PGcgZmlsbD0iIzIyMiI+PHBhdGggZD0ibTM3 NC41NzUuMjA5Yy0xLjkuMi04IC45LTEzLjUgMS40LTc4LjIgOC4yLTE1 NS4yIDQxLjMtMjE4IDkzLjktMTEuNiA5LjYtMzggMzYtNDcuNiA0Ny42 LTUyIDYyLjEtODIuNCAxMzEuOC05My42IDIxNC4zLTIuNSAxOC4z
    …
    ```

现在，借助*nix 输出重定向功能，我们可以创建一个带有我们脚本输出的 HTML 文件：

```js
$ deno run --allow-net web-apis/fetch-deno-logo.js > web-apis/deno-logo.html
```

现在，您可以查看文件，或者直接在浏览器中打开它以测试它是否可以工作。

您还可以使用前一章的知识，直接运行 Deno 标准库中的脚本来服务当前文件夹：

```js
$ deno run --allow-net --allow-read https://deno.land/std@0.83.0/http/file_server.ts web-apis
Check https://deno.land/std@0.65.0/http/file_server.ts
HTTP server listening on http://0.0.0.0:4507
```

然后，通过导航到`http://localhost:4507/deno-logo.html`，我们可以检查图像是否在那里并且可以正常工作：

![图 3.1 – 使用 Deno.land 标志作为 base64 图像访问网页](img/Figure_3.1_B16380.jpg)

图 3.1 – 使用 Deno.land 标志作为 base64 图像访问网页

这些只是 Deno 支持的一些 Web API 的例子。在本书的这个特定例子中，我们使用了`fetch`和`btoa`，但本章还将使用更多。

请随意尝试这些已经熟悉的 API，无论是通过编写简单脚本还是使用 REPL。在本书的其余部分，我们将使用来自 Web APIs 的已知函数。在下一节中，我们将了解 Deno 命名空间，那些只在内置 Deno 中工作的函数，并且通常提供更低级别的行为。

# 探索 Deno 命名空间

不受 Web API 覆盖的所有功能都位于 Deno 命名空间下。这是专属于 Deno 的功能，并且不能，例如，被捆绑以在 Node 或浏览器中运行。

在本节中，我们将探索一些这些功能。我们将构建一些小工具，模仿您每天使用的程序。

如果您想在动手之前探索可用的功能，它们可以在[`doc.deno.land/builtin/stable`](https://doc.deno.land/builtin/stable)找到。

## 构建一个简单的 ls 命令

如果您曾经使用过*nix 系统的终端或 Windows PowerShell，您可能熟悉`ls`命令。简单来说，它列出了一个目录中的文件和文件夹。我们要做的是创建一个模仿`ls`一些功能的 Deno 工具，即列出目录中的文件，并显示它们的某些详细信息。

原始命令有无数的标志，出于简洁原因，我们在这里不会实现。

我们决定显示的信息是文件的名字、大小和最后修改日期。让我们开始动手：

1.  创建一个名为`list-file-names.js`的文件，并使用`Deno.readDir`获取当前目录中所有文件和文件夹的列表：

    ```js
    for await (const dir of Deno.readDir(".")) {
      console.log(dir.name)
    }
    ```

    这将把当前目录中的文件打印在不同的行上：

    ```js
    readDir (https://doc.deno.land/builtin/stable#Deno.readDir) from the Deno namespace.As is mentioned in the documentation, it returns `AsyncInterable`, which we're looping through and printing the name of the file. As the runtime is written in TypeScript, we have very useful type completion and we know exactly what properties are present in every `dir` entry.Now, we want to get the current directory as a command-line argument.
    ```

1.  使用`Deno.args`（[`doc.deno.land/builtin/stable#Deno.args`](https://doc.deno.land/builtin/stable)）来获取命令行参数。如果没有发送任何参数，请使用当前目录作为默认值：

    ```js
    const [path = "."] = Deno.args;
    for await (const dir of Deno.readDir(path)) {
      console.log(dir.name)
    }
    ```

    我们利用数组解构来获取`Deno.args`的第一个值，同时使用默认属性来设置`path`变量的默认值。

1.  导航到`demo-files`文件夹（[`github.com/PacktPublishing/Deno-Web-Development/tree/master/Chapter03/ls/demo-files`](https://github.com/PacktPublishing/Deno-Web-Development/tree/master/Chapter03/ls/demo-files)）并运行以下命令：

    ```js
    $ deno run --allow-read ../list-file-names.ts            
    file-with-no-content.txt
    .hidden-file
    lorem-ipsum.txt
    ```

    看起来它正在工作。它正在获取当前文件夹中的文件并列出它们。

    现在我们需要获取文件信息，以便我们可以显示它。

1.  使用`Deno.stat`（https://doc.deno.land/builtin/stable#Deno.stat）获取有关文件的信息：

    ```js
    padEnd so that the output is aligned. By running the program we just wrote, while in the Chapter03/Is folder (https://github.com/PacktPublishing/Deno-Web-Development/tree/master/Chapter03/ls/demo-files), we get the following output:

    ```

    $ deno run --allow-read index.ts ./demo-files

    12   7/4  .hidden

    96   7/4  folder

    96   7/4  second-folder

    5    7/4  my-best-file

    20   7/4  .file1

    0    7/4  .hidden-file

    ```js

    ```

我们获取了作为参数传递的`deno-files`目录中的文件和文件夹列表，以及以字节为单位的大小和创建的月份和日期。

在这里，我们使用了已经知的必需的`--allow-read`标志，以赋予 Deno 访问文件系统的权限。然而，在上一章中，我们提到了 Deno 程序请求权限的不同方式，我们称之为“动态权限”。接下来我们将学习这方面的内容。

## 使用动态权限

当我们自己编写 Deno 程序时，我们通常事先知道所需的权限。然而，当编写可能需要或不需要的权限的代码，或者编写交互式 CLI 工具时，一次性请求所有权限可能没有意义。这就是动态权限的作用。

动态权限允许程序在需要时请求权限，使得执行代码的人可以交互式地给予或拒绝特定的权限。

这是一个仍然不稳定的功能，因此其 API 可能会发生变化，但由于它所启用的潜在功能量，我认为它仍然值得提及。

您可以查看 Deno 的权限 API，位于[`doc.deno.land/builtin/unstable#Deno.permissions`](https://doc.deno.land/builtin/unstable#Deno.permissions)。

接下来我们要做的是确保我们的`ls`程序请求文件系统的读取权限。让我们按照以下步骤进行：

1.  使用`Deno.permissions.request`在执行程序之前请求读取权限：

    ```js
    …
    const [path = "."] = Deno.args;
    await Deno.permissions.request({
      name: "read",
      path,
    });
    for await (const dir of Deno.readDir(path)) {
    …
    ```

    此操作请求对程序将要运行的目录的权限。

1.  运行程序并在当前目录中授予权限：

    ```js
    g to the permission request command, we're granting it access to the current directory (.).We can now try to run the same program but denying the permissions this time.
    ```

1.  运行程序并在当前目录中拒绝读取权限：

    ```js
    $ deno run --unstable list-file-names-interactive-permissions.ts .
    Deno requests read access to ".". Grant? [g/d (g = grant, d = deny)] d
    error: Uncaught (in promise) PermissionDenied: read access to ".", run again with the --allow-read flag
        at processResponse (deno:core/core.js:223:11)
        at Object.jsonOpAsync (deno:core/core.js:240:12)
        at async Object.[Symbol.asyncIterator] (deno:cli/rt/30_fs.js:125:16)
        at async list-file-names-interactive-permissions.ts:10:18
    ```

    这就是动态权限的工作方式！

在这里，我们使用它们来控制文件系统的读取权限，但它们也可以用来请求运行时所有可用的权限（在第二章中提到，*工具链*）。在编写 CLI 应用程序时，它们非常有用，允许您交互式地调整正在运行的程序可以访问的权限。

## 使用文件系统 API

编写程序时，访问文件系统是我们最基本的需求之一。正如您在文档中可能已经看到的那样，Deno 提供了执行这些常见任务的 API。

决定与 Rust 核心标准化通信后，所有这些 API 都返回`Uint8Array`，其解码和编码应由其消费者完成。这与 Node.js 有所不同，在 Node.js 中，一些函数返回转换格式，而其他函数返回 blob、缓冲区等。

让我们探索这些文件系统 API 并阅读一个文件的内容。

我们将要读取位于[`github.com/PacktPublishing/Deno-Web-Development/tree/master/Chapter03/file-system/sentence.txt`](https://github.com/PacktPublishing/Deno-Web-Development/tree/master/Chapter03/file-system/sentence.txt)的示例文件，使用`TextDecoder`和`Deno.readFile` API，如下脚本所示：

```js
const decoder = new TextDecoder()
const content = await Deno.readFile('./sentence.txt');
console.log(decoder.decode(content))
```

您可能会注意到我们使用了`TextDecoder`类，这是另一个在浏览器中存在的 API。

运行脚本时，请不要忘记使用`--allow-read`权限，以便它可以从文件系统中读取。

如果我们想将这个文件的内容写入另一个文件，我们可以使用`writeFile`：

```js
const content = await Deno.readFile("./sentence.txt");
await Deno.writeFile("./copied-sentence.txt", content)
```

请注意，由于我们正在使用从`readFile`得到的`Uint8Array`直接发送到`writeFile`方法，所以我们不再需要`TextEncoder`。记得在运行时使用`--allow-write`标志，因为它现在正在向文件系统写入。

正如您可能猜到的或阅读文档的那样，Deno 正好提供了这样的 API，`copyFile`：

```js
await Deno.copyFile("./copied-sentence.txt", 
  "./using-copy-command.txt");
```

现在，您可能注意到我们总是在 Deno 命名空间函数的调用前使用`await`。

所有 Deno 上的异步操作都返回一个承诺，这是我们这样做的主要原因。我们本可以在那里使用等效的`then`语法处理结果，但我们认为这样更易读。

您可以在文档中找到 Deno 命名空间中还包括的其他 API，用于删除、重命名、更改权限等。

重要提示

Deno 中的许多异步 API 都有相应的*同步*API，可以用于特定用例，在这些用例中，您希望阻塞进程并获取结果（例如，`readFileSync`、`writeFileSync`等）。

## 使用缓冲区

缓冲区代表用于存储临时二进制数据的内存区域。它们通常用于处理 I/O 和网络操作。由于异步操作是 Deno 的优势之一，所以我们将在本节中探索缓冲区。

Deno 缓冲区与 Node 缓冲区不同。这是因为当 Node 被创建时，直到版本 4，JavaScript 中都没有对`ArrayBuffers`的支持。由于 Node 针对异步操作进行了优化（缓冲区真正闪耀的地方），其背后的团队不得不创建一个 Node 缓冲区来模拟本地缓冲区的行为。后来，`ArrayBuffers`被添加到语言中，Node 团队将现有的缓冲区迁移到利用它。目前它只是一个`ArrayBuffers`的子类。这个相同的缓冲区在 Node v10 中被弃用。由于 Deno 是最近创建的，它的缓冲区深度利用了`ArrayBuffer`。

## 从 Deno.Buffer 读写

Deno 提供了一个动态长度的缓冲区，它是基于`ArrayBuffer`的固定内存分配实现的。缓冲区提供了类似于队列的功能，其中数据可以被不同的消费者写入和读取。正如我们最初提到的，它们在网络和 I/O 等任务中得到了广泛应用，因为它们允许异步读写。

举个例子，假设你有一个正在写一些日志的应用程序，你想处理这些日志。你可以同步地处理它们，也可以让这个应用程序将日志写入缓冲区，然后有一个消费者异步地处理它们。

让我们为那种情况写一个小的程序。我们将写两个简短程序。第一个程序将模拟一个产生日志的应用程序；第二个程序将通过使用缓冲区来消费这些日志。

我们首先编写模拟一个产生日志的应用程序的代码。在[`github.com/PacktPublishing/Deno-Web-Development/blob/master/Chapter03/buffers/logs/example-log.txt`](https://github.com/PacktPublishing/Deno-Web-Development/blob/master/Chapter03/buffers/logs/example-log.txt)，有一个文件，里面有一些示例日志我们将使用：

```js
const encoder = new TextEncoder();
const fileContents = await Deno.readFile("./example-log.txt ");
const decoder = new TextDecoder();
const logLines = decoder.decode(fileContents).split("\n");
export default function start(buffer: Deno.Buffer) {
  setInterval(() => {
     const randomLine = Math.floor(Math.min(Math.random() *        1000, logLines.length));
     buffer.write(encoder.encode(logLines[randomLine]));
  },   100)
}
```

这段代码从示例文件中读取内容并将其分割成行。然后，它获取一个随机的行号，每 100 毫秒将那一行写入缓冲区。这个文件然后导出一个函数，我们可以调用它来“生成随机日志”。我们将在下一个脚本中使用这个来模仿一个产生日志的应用程序。

现在来到了有趣的部分：我们将按照这些步骤编写基本的*日志处理器*：

1.  创建一个缓冲区并将其发送给我们刚刚编写的日志生产者的`start`函数：

    ```js
    import start from "./logCreator.ts";
    const buffer = new Deno.Buffer();
    start(buffer);
    ```

1.  调用`processLogs`函数来开始处理缓冲区中存在的日志条目：

    ```js
    …
    start(buffer);
    processLogs();
    async function processLogs() {}
    ```

    正如你所看到的，`processLogs`函数会被调用，但是什么也不会发生，因为我们还没有实现一个程序来执行它。

1.  在`processLogs`函数内部创建一个`Uint8Array`对象类型，并读取那里的缓冲区内容：

    ```js
    …
    async function processLogs() {
      const destination = new Uint8Array(100);
      const readBytes = await buffer.read(destination);
      if (readBytes) {
        // Something was read from the buffer
      }
    }
    ```

    文档（[`doc.deno.land/builtin/stable#Deno.Buffer`](https://doc.deno.land/builtin/stable#Deno.Buffer)）指出，当有东西要读取时，`Deno.Buffer`的`read`函数返回读取的字节数。当没有东西可读时，缓冲区为空，它返回 null。

1.  现在，在`if`内部，我们可以简单地解码已经读取的内容，因为我们知道它以`Uint8Array`格式存在：

    ```js
    const decoder = new TextDecoder();
    …  
    if (readBytes) {
      const read = decoder.decode(destination);
    }
    ```

1.  要将在控制台中解码的值打印出来，我们可以使用已知的`console.log`。我们也可以用不同的方式来做，通过使用`Deno.stdout`([`doc.deno.land/builtin/stable#Deno.stdout`](https://doc.deno.land/builtin/stable#Deno.stdout))来写入标准输出。

    `Deno.stdout` 是 Deno 中的一个`writer`对象([`doc.deno.land/builtin/stable#Deno.Writer`](https://doc.deno.land/builtin/stable#Deno.Writer))。我们可以使用它的`write`方法将文本发送到那里：

    ```js
    const decoder = new TextDecoder();
    const encoder = new TextEncoder();
    …  
    if (readBytes) {
      const read = decoder.decode(destination);
      await Deno.stdout.write(encoder.encode(`${read}\n`));
    }
    ```

    这样，我们就在`Deno.stdout`中写入刚刚读取的值。我们还添加了一个换行符（`\n`）在末尾，使其在控制台上的可读性稍好。

    如果我们保持这种方式，这个`processLogs`函数将只运行一次。因为我们希望这个函数再次运行并检查`buffer`中是否还有更多日志，我们需要将其安排在稍后再次运行。

1.  使用`setTimeout`在 100 毫秒后调用相同的`processLogs`函数：

    ```js
    async function processLogs() {
      const destination = new Uint8Array(100);
      const readBytes = await buffer.read(destination);
      if (readBytes) {
        …
      }
      setTimeout(processLogs, 10);
    }
    ```

例如，如果我们打开`example-log.txt`文件，我们可以看到包含以下格式的日期行：`Thu Aug 20 22:14:31 WEST 2020`。

假设我们只想打印出包含`Tue`的日志。让我们编写实现该功能的逻辑：

```js
async function processLogs() {
  const destination = new Uint8Array(100);
  const readBytes = await buffer.read(destination);
  if (readBytes) {
    const read = decoder.decode(destination);
    if (read.includes("Tue")) {
      await Deno.stdout.write(encoder.encode(`${read}\n`));
    }
  }
  setTimeout(processLogs, 10);
}  
```

然后，我们在包含`example-logs.txt`文件的文件夹内执行程序：

```js
$ deno run --allow-read index.ts
Tue Aug 20 17:12:05 WEST 2019
Tue Sep 17 02:19:56 WEST 2019
Tue Dec  3 14:02:01 CET 2019
Tue Jul 21 10:37:26 WEST 2020
```

带有日期的日志行如实地从缓冲区读取并符合我们的条件出现。

这是使用缓冲区可以做到的简要演示。我们能够异步地从缓冲区写入和读取。这种方法允许，例如，消费者在应用程序读取其他部分的同时处理文件的一部分。

`Deno`命名空间提供了比我们在这里尝试的更多功能。在本节中，我们决定选择几个部分并给您展示它启用多少功能。

我们将使用这些函数，以及第三方模块和标准库，当我们从第*4*章，*构建网络应用程序*开始编写我们的网络服务器。

# 使用标准库

在本节中，我们将探讨由 Deno 的标准库提供的行为。目前， runtime 认为它还不稳定，因此模块是单独版本化的。在我们撰写本文时，标准库处于 *version 0.83.0* 版本。

正如我们之前提到的，Deno 在添加到标准库的内容上非常细致。核心团队希望它提供足够的行为，这样人们就不需要依赖数百万个外部包来完成某些事情，但同时也不想增加太多的 API 表面。这是一个难以达到的微妙平衡。

受到 golang 的启发，Deno 标准库的大部分函数模仿了谷歌创建的语言。这是因为 Deno 团队真正相信*golang*如何发展其标准库，这个标准库以其精心打磨而广为人知。作为一个有趣的注记，Ryan Dahl（Deno 和 Node 的创建者）在他的某次演讲中提到，当拉取请求向标准库添加新的 API 时，会要求提供相应的*golang*实现。

我们不会逐一介绍整个库，原因与我们没有逐一介绍 Deno 命名空间相同。我们将做的是在学习它所提供的内容的同时，用它来构建几个有用的程序。我们将从生成 ID 等操作，到日志记录，到 HTTP 通信等已知用例进行介绍。

## 为我们的简单`ls`添加颜色

几页之前，我们在*nix 系统中建立了一个非常粗糙和简单的`ls`命令的“克隆”。当时我们列出了文件，以及它们的大小和修改日期。

为了开始探索标准库，我们将向该程序的终端输出添加一些颜色。让我们把文件夹名称打印成红色，以便我们可以轻松地区分它们。

我们将创建一个名为`list-file-names-color.ts`的文件。这次我们将使用 TypeScript，因为我们将得到更好的补全功能，因为标准库和 Deno 命名空间函数都是为此编写的。

让我们探索允许我们为文本着色的标准库函数（https://deno.land/std@0.83.0/fmt/colors.ts）。

如果我们想要查看一个模块的文档，我们可以直接查看代码，但我们也可以使用`doc`命令或文档网站。我们将使用后者。

导航到 https://doc.deno.land/https/deno.land/std@0.83.0/fmt/colors.ts。屏幕上列出了所有可用的方法：

1.  从标准库的格式化库中导入打印红色文本的方法：

    ```js
    import { red } from "https://deno.land/std@0.83.0/fmt/colors.ts";
    ```

1.  在我们的`async`迭代器遍历当前目录中的文件时使用它：

    ```js
    const [path = "."] = Deno.args;
    for await (const item of Deno.readDir(path)) {
      if (item.isDirectory) {
        console.log(red(item.name));
      } else {
        console.log(item.name);
      }
    }
    ```

1.  通过在`demo-files`文件夹内运行它([`github.com/PacktPublishing/Deno-Web-Development/tree/master/Chapter03/ls`](https://github.com/PacktPublishing/Deno-Web-Development/tree/master/Chapter03/ls)),我们得到用红色打印的文件夹（这在打印书中无法显示，但您可以本地运行它）：

    ```js
    $ deno run –allow-read list-file-names-color.ts
    file-with-no-content.txt
    demo-folder
    .hidden-file
    lorem-ipsum.txt
    ```

我们现在有一个更好的`ls`命令，它让我们能够区分文件夹和文件，这是通过使用标准库中的颜色函数实现的。标准库还提供了许多其他模块，我们将在书的进程中逐一了解它们。其中一些将在我们开始编写自己的应用程序时使用。

我们将特别关注的一个模块是 HTTP 模块，从下一节开始我们将大量使用它。

# 使用 HTTP 模块构建 Web 服务器

本书的主要焦点不仅是介绍 Deno 以及如何使用它，还包括学习如何使用它来构建 Web 应用程序。在这里，我们将创建一个简单的 JSON API，以便您了解 HTTP 模块。

我们将构建一个 API，用于保存和列出便签。我们把这些便签称为笔记。想象一下，这个 API 将为您提供便签板的数据。

我们将借助 Web APIs 和 Deno 标准库 HTTP 模块的功能创建一个非常简单的路由系统。请记住，我们这样做是为了探索 API 本身，因此这并不是生产就绪的代码。

让我们先创建一个名为`post-it-api`的文件夹和一个名为`index.ts`的文件。再次使用 TypeScript，因为我们认为自动完成和类型检查功能极大地提高了我们的体验，并减少了可能的错误数量。

本节的最终代码可在[`github.com/PacktPublishing/Deno-Web-Development/blob/master/Chapter03/post-it-api/steps/7.ts`](https://github.com/PacktPublishing/Deno-Web-Development/blob/master/Chapter03/post-it-api/steps/7.ts)找到：

1.  首先将标准库 HTTP 模块导入我们的文件中：

    ```js
    import { serve } from
      "https://deno.land/std@0.83.0/http/server.ts";
    ```

1.  使用`AsyncIterator`处理请求，就像我们之前的示例一样：

    ```js
    console.log("Server running at port 8080");
    for await (const req of serve({ port: 8080 })) {
      req.respond({ body: "post-it api", status: 200 });
    }
    ```

    如果我们现在运行它，这就是我们会得到的。请记住，为了使其具有网络访问权限，我们需要使用在权限部分提到的`--allow-net`标志：

    ```js
    deno run --allow-net index.ts
    Server running at port 8080
    ```

1.  为了清晰起见，我们可以将端口和服务器实例提取到单独的变量中：

    ```js
    const PORT = 8080;
    const server = serve({ port: PORT });
    console.log("Server running at port", PORT);
    for await (const req of serve({ port: PORT })) {
    …
    ```

我们的服务器像以前一样运行良好，唯一的区别是现在代码（有争议地）看起来更易读，因为配置变量位于文件的顶部。我们稍后学习如何从代码中提取这些变量。

### 返回便签列表

我们的第一个要求是我们有一个返回便签列表的 API。这些便签将包括名称、标题和创建日期。在我们到达那里之前，为了使我们能够有多个路由，我们需要一个路由系统。

为了进行这个练习，我们将自己构建一个。这是我们了解 Deno 内置的一些 API 的方式。我们稍后会同意，在编写生产应用程序时，有时最好重用经过测试和广泛使用的软件，而不是一直重新发明轮子。然而，为了学习目的，完全可以*重新发明轮子*。

为了创建我们的基本路由系统，我们将使用一些您可能从浏览器中知道的 API。例如`URL`、`UrlSearchParams`等对象。

我们的目标是能够通过其 URL 和路径定义一个路由。像`GET /api/post-its`这样的东西会很好。让我们来做吧！

1.  首先创建一个`URL`对象（[`developer.mozilla.org/en-US/docs/Web/API/URL`](https://developer.mozilla.org/en-US/docs/Web/API/URL)）来帮助我们解析 URL 和其参数。我们将提取`HOST`和`PROTOCOL`到另一个变量中，这样我们就不用重复了：

    ```js
    const PORT = 8080;
    const HOST = "localhost";
    const PROTOCOL = "http";
    const server = serve({ port: PORT, hostname: HOST });
    console.log(`Server running at ${HOST}:${PORT}`);
    for await (const req of server) {
      const url = new
        URL(`${PROTOCOL}://${HOST}${req.url}`);
      req.respond({ body: "post-it api", status: 200 });
    }
    ```

1.  使用创建的`URL`对象进行一些路由。我们将使用`switch case`来实现。当没有匹配的路由时，应向客户端发送`404`：

    ```js
      const pathWithMethod = `${req.method} ${url.pathname}`;
      switch (pathWithMethod) {
        case "GET /api/post-its":
          req.respond({ body: "list of all the post-its",
            status: 200 });
          continue;
        default:
          req.respond({ status: 404 });
      } 
    ```

    提示

    你可以运行脚本时同时使用`--unstable`和`--watch`标志来重新启动文件更改后的脚本，如下所示：`deno run --allow-net --watch --unstable index.ts`。

1.  访问`http://localhost:8080/api/post-its`并确认我们得到了正确的响应。其他任何路由都会得到 404 响应。

    请注意，我们使用`continue`关键字让 Deno 在响应请求后跳出当前迭代（记住我们正在一个`for`循环内）。

    你可能会注意到，目前我们只是按路径路由，而不是按方法。这意味着对`/api/post-its`的任何请求，无论是`POST`还是`GET`，都会得到相同的响应。让我们通过继续前进来解决这个问题。

1.  创建一个包含请求方法和路径名的变量：

    ```js
      const pathWithMethod = `${req.method} ${url.pathname}`
      switch (pathWithMethod) {
    ```

    现在我们可以按照自己的意愿定义路由，比如`GET /api/post-its`。现在我们已经有了路由系统的基本知识，我们将编写返回便签的逻辑。

1.  创建 TypeScript 接口，以帮助我们保持便签的结构：

    ```js
    interface PostIt {
      title: string,
      id: string,
      body: string,
      createdAt: Date
    }
    ```

1.  创建一个将作为我们这次练习的*内存数据库*的变量。

    我们将使用一个 JavaScript 对象，其中键是 ID，值是刚刚定义的`PostIt`类型的对象：

    ```js
    let postIts: Record<PostIt["id"], PostIt> = {}
    ```

1.  向我们的数据库添加几个测试数据：

    ```js
    let postIts: Record<PostIt["id"], PostIt> = {
      '3209ebc7-b3b4-4555-88b1-b64b33d507ab': { title: 'Read more', body: 'PacktPub books', id: 3209ebc7-b3b4-4555-88b1-b64b33d507ab ', createdAt: new Date() },
      'a1afee4a-b078-4eff-8ca6-06b3722eee2c': { title: 'Finish book', body: 'Deno Web Development', id: '3209ebc7-b3b4-4555-88b1-b64b33d507ab ', createdAt: new Date() }
    }
    ```

    请注意，我们目前是*手动生成* *IDs*。稍后，我们将使用标准库中的另一个模块来完成。让我们回到我们的 API，并更改处理路由的`case`。

1.  更改`case`，以便返回所有便签而不是硬编码的消息。

    由于我们的数据库是一个键/值存储，我们需要使用`reduce`来构建包含所有便签的数组（删除下面代码块中突出显示的行）：

    ```js
    case GET "/api/post-its":
      req.respond({ body: "list of all the post-its", status:     200 });
      const allPostIts = Object.keys(postIts).
        reduce((allPostIts: PostIt[], postItId) => {
            return allPostIts.concat(postIts[postItId]);
          }, []);
      req.respond({ body: JSON.stringify({ postIts:     allPostIts }) });
      continue;
    ```

1.  运行代码并访问`/api/post-its`。我们应该会在那里看到我们的便签列表！

    你可能已经注意到，这仍然不是 100%正确的，因为我们的 API 返回的是 JSON，而且其头部与载荷不匹配。

1.  我们将通过使用来自浏览器的我们知道的 API，即`Headers`对象，来添加`content-type`（[`developer.mozilla.org/en-US/docs/Web/API/Headers`](https://developer.mozilla.org/en-US/docs/Web/API/Headers)）。删除下面代码块中突出显示的行：

    ```js
    const headers = new Headers();
    headers.set("content-type", "application/json");
    const pathWithMethod = `${req.method} ${url.pathname}`
    switch (pathWithMethod) {
      case "GET /api/post-its":
    …
        req.respond({ body: JSON.stringify({ postIts: 
          allPostIts }) });
        req.respond({ headers, body: JSON.stringify({ 
          postIts: allPostIts }) });
        continue;
    ```

我们上面创建了`Headers`对象的实例，然后我们在响应中使用它，在`req.respond`上。这样，我们的 API 现在更加一致、易消化，并遵循标准。

### 向数据库添加便签

既然我们已经有了读取便签的方法，我们还需要一种添加新便签的方法，因为如果 API 完全是静态内容，那就没有意义了。那就是我们接下来要做的。

我们将使用我们创建的*路由基础设施*来添加一个允许我们*插入*记录到我们数据库的路由。由于我们遵循 REST 指南，那个路由将位于列出`post-its`的相同路径上，但方法不同：

1.  定义一个总是返回`201`状态码的路由：

    ```js
        case "POST /api/post-its":
          req.respond({ status: 201 });
          continue
    ```

1.  使用`curl`的帮助，我们可以看到它返回了正确的状态码：

    ```js
    curl but feel free to use your favorite HTTP requests tool, you can even use a graphical client such as Postman (https://www.postman.com/).Let's make the new route do what it is supposed to. It should get a JSON payload and use that to create a new post-it.We know, by looking at the documentation of the standard library's HTTP module (`doc.deno.land/https/deno.land/std@0.83.0/http/server.ts#ServerRequest`) that the body of the request is a *Reader* object. The documentation includes an example on how to read from it.
    ```

1.  遵循建议，读取值并打印出来以更好地理解它：

    ```js
    case "POST /api/post-its":
          const body = await Deno.readAll(req.body);
          console.log(body) 
    ```

1.  用`curl`发出带有`body`的请求：

    ```js
    201 status code. If we look at our running server though, something like this is printed to the console:

    ```

    Uint8Array(25) [

    123, 34, 116, 105, 116, 108, 101,

    34, 58, 32, 34, 84, 32, 101, 115,

    116, 32, 112, 111, 115, 116, 45,

    105, 116, 34, 125

    ]

    ```js

    We previously learned that Deno uses `Uint8Array` to do all its communications with the Rust backend, and this is not an exception. However, `Uint8Array` is not what we currently want, we want the actual text of the request body. 
    ```

1.  使用`TextDecoder`将请求体作为可读值获取。完成此操作后，我们再次记录输出，然后我们将发出一个新的请求：

    ```js
    $ deno -X POST -d "{\"title\": \"Buy milk\"}" 
    http://localhost:8080/api/post-its
    ```

    这次服务器在控制台打印的就是这个：

    ```js
    {"title": "Buy milk "}
    ```

    我们正在取得进展！

1.  由于正文是一个字符串，我们需要将其解析为 JavaScript 对象。我们将使用我们的一位老朋友，`JSON.parse`：

    ```js
    const decoded = JSON.parse(new 
      TextDecoder().decode(body));
    ```

    现在我们有了一种请求体格式，我们可以对其采取行动，这基本上是我们创建新数据库记录所需要的一切。让我们按照以下步骤创建一个：

1.  使用标准库中的`uuid`模块（[`deno.land/std@0.83.0/uuid`](https://deno.land/std@0.67.0/uuid)）为我们的记录生成一个随机的 UUID：

    ```js
    import { v4 } from 
      "https://deno.land/std/uuid/mod.ts";
    ```

1.  在我们的路由的 switch case 中，我们将使用`generate`方法创建一个`id`并将其插入到*数据库*中，在用户在请求负载中发送的内容顶部添加`createdAt`日期。为了这个例子，我们省略了验证：

    ```js
    case "POST /api/post-its":
    …
        const decoded = JSON.parse(new 
          TextDecoder().decode(body));
        const id = v4.generate();
        postIts[id] = {
          ...decoded,
          id,
          createdAt: new Date()
        }
        req.respond({ status: 201, body:
          JSON.stringify(postIts[id]), headers });
    ```

    注意，我们使用了之前在`GET`路由来定义的相同的`headers`对象，以便我们的 API 返回`Content-Type: application/json`。

    再次，遵循*REST*指南，我们返回`201` `Created`代码和创建的记录。

1.  保存代码，重启服务器，再次运行它：

    ```js
    GET request to the route that lists all the post-its to check if the record was actually inserted into the database:

    ```

    `curl http://localhost:8080/api/post-its`

    {"postIts":[{"title":"Read more","body":"PacktPub books","id":"3209ebc7-b3b4-4555-88b1-b64b33d507ab","createdAt":"2021-01-10T16:28:52.210Z"},{"title":"Finish book","body":"Deno Web Development","id":"a1afee4a-b078-4eff-8ca6-06b3722eee2c","createdAt":"2021-01-10T16:28:52.210Z"},{"title":"Buy groceries","body":"1 x Milk","id":"b35b0a62-4519-4491-9ba9-b5809b4810d5","createdAt":"2021-01-10T16:29:05.519Z"}]}

    ```js

    ```

而且它奏效了！现在我们有一个 API，它可以返回便签列表并添加便签。

这基本上结束了我们在这个章节中使用 HTTP 模块关于 API 的一切。像我们写的这个 API 一样，大多数 API 都是为了被前端应用程序消费，我们接下来会做的那样来结束这个章节。

### 提供前端服务

由于这超出了本书的范围，我们不会编写与这个 API 交互的前端代码。然而，如果你想用它来获取 post-its 并显示在一个单页应用程序上，我在书中的文件中包含了一个（[`github.com/PacktPublishing/Deno-Web-Development/blob/master/Chapter03/post-it-api/index.html`](https://github.com/PacktPublishing/Deno-Web-Development/blob/master/Chapter03/post-it-api/index.html)）。

我们将学习如何使用我们刚刚建立的网络服务器来提供一份 HTML 文件：

1.  首先，我们需要在服务器的根目录下创建一个路由。然后，我们需要设置正确的`Content-Type`，并使用已知的文件系统 API 返回文件内容。

    为了获取当前文件中 HTML 文件的路径，我们将使用 URL 对象与 JavaScript 中的`import.meta`声明一起使用（[`developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import.meta`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import.meta)），其中包含当前文件的路径：

    ```js
    resolve, and fromFileUrl methods from Deno's standard-library to get a URL that is relative to the current file.Note that we now need to run this with the `--allow-read` flag since our code is reading from the filesystem. 
    ```

1.  为了让我们更安全，我们将指定程序可以读取的确切文件夹，通过将其发送到`--allow-read`标志：

    ```js
    $ deno run --allow-net --allow-read=. index.ts
    Server running at http://0.0.0.0:8080 
    ```

    这将防止任何可能允许恶意人士读取我们文件系统的错误。

1.  打开浏览器，输入网址，你应该会来到一个页面，我们可以看到添加的`post-its`日程安排。要添加一个新的，你也可以点击**添加新日程**文本并填写表单：

![图 3.2 – 前端消费 post-it API](img/Figure_3.2_B16380.jpg)

](img/Figure_3.2_B16380.jpg)

图 3.2 – 前端消费 post-it API

重要说明

请记住，在许多生产环境中，不建议 API 提供前端代码。在这里，我们这样做是为了学习目的，以便我们能够理解标准库 HTTP 模块的一些可能性。

在本节中，我们学习了如何使用标准库提供的模块造福。我们创建了`ls`命令的简单版本，并使用标准库的输出格式化功能为其添加了一些颜色。为了结束这一节，我们创建了一个具有几个端点的 HTTP API，用于列出和持久化记录。我们经历了不同的需求，并学习了 Deno 如何完成它们。

# 总结

随着我们对这本书的阅读，我们对 Deno 的了解变得更加实用，我们开始用它来处理更接近现实世界的用例。这一章就是关于这个。

我们首先学习了运行时的基本特性，即程序生命周期，以及 Deno 如何看待模块稳定性和版本控制。我们迅速转向了 Deno 提供的 Web API，通过编写一个简单的程序，从网站上获取 Deno 标志，将其转换为 base64，并将其放入 HTML 页面。

然后，我们进入了`Deno`命名空间，探索了一些其底层功能。我们使用文件系统 API 构建了一些示例，并最终用它构建了一个`ls`命令的简化版。

缓冲区是在 Node.js 世界中广泛使用的东西，它们有能力执行异步读写行为。正如我们所知，Deno 与 Node.js 有很多相似的使用场景，这使得在这一章中不可避免地要讨论缓冲区。我们首先解释了 Deno 缓冲区与 Node.js 的区别，然后通过构建一个小型应用程序来结束这一节，该程序可以异步地从它们中读写。

为了结束这一章，我们更接近了这本书的主要目标之一，即使用 Deno 进行 Web 开发。我们使用 Deno 创建了第一个 JSON API。在这个过程中，我们了解到了多个 Deno API，甚至建立了我们基本的路由系统。然后，我们继续创建几个路由，列出并在我们的*数据存储*中创建记录。在章节的最后，我们学习了如何处理 API 中的头部，并将它们添加到我们的端点。

我们通过直接从我们的 Web 服务器提供单页应用程序来结束这一章；那个相同的单页应用程序消费并与我们的 API 进行了交互。

这一章我们覆盖了很多内容。我们开始构建 API，这些 API 现在离现实更近了，比我们之前做的要接近多了。我们还更清楚地了解了如何使用 Deno 进行开发，包括使用权限和文档。

当前章节结束了我们的入门之旅，希望它让你对接下来的内容感到好奇。

在接下来的四章中，我们将构建一个 Web 应用程序，并探索在过程中所做的所有决策。你到目前为止学到的大部分知识将会在后面用到，但还有大量新的、令人兴奋的内容即将出现。在下一章，我们将开始创建一个 API，随着章节的进行，我们将继续添加功能。

我希望你能加入我们！
