# 第四章：构建网络应用程序

我们终于来了！我们走过了漫长的道路，才到达这里。这里的乐趣才刚刚开始。我们已经经历了三个阶段：了解 Deno 是什么，探索它提供的工具链，以及通过其运行时了解其详细信息和功能。

几乎本章之前的所有内容都将证明是有用的。希望，前面的章节让你有足够的信心开始应用我们所学的知识。我们将利用这些章节，结合你现有的 TypeScript 和 JavaScript 知识，来构建一个完整的网络应用程序。

我们将编写一个包含业务逻辑、处理身份验证、授权和日志记录等内容的 API。我们将涵盖足够的基本内容，让你在最后觉得舒适地选择 Deno 来构建你下一个伟大的应用程序。

在本章中，我们将不仅仅讨论 Deno，还会回顾一些关于软件工程和应用程序架构的基本思想。我们认为，在从头开始构建应用程序时，保持一些事情在心中至关重要。我们将查看一些基本原则，这些原则将证明是有用的，并帮助我们结构化代码，使其易于更改，从而使它能够适应未来。

后来，我们将开始参考一些第三方模块，查看它们的方法，并决定从这里开始使用哪些来帮助我们处理路由和 HTTP 相关挑战。我们还将确保我们的应用程序结构以一种第三方代码被隔离并且作为我们想要构建的功能的启用器而不是功能本身来工作的方式进行组织。

本章我们将涵盖以下主题：

+   结构化网络应用程序

+   探索 Deno HTTP 框架

+   让我们开始吧！

## 技术要求

本章使用的代码文件可在以下链接找到：[`github.com/PacktPublishing/Deno-Web-Development/tree/master/Chapter04/museums-api`](https://github.com/PacktPublishing/Deno-Web-Development/tree/master/Chapter04/museums-api)。

# 结构化网络应用程序

开始一个应用程序时，花时间思考其结构和架构是很重要的。这一节将从这个角度开始：通过查看应用程序架构的核心。我们将看看它带来了什么优势，并使自己与一套原则保持一致，这些原则将帮助我们在应用程序增长时扩展它。

然后，我们将开发将成为应用程序第一个端点的代码。然而，首先，我们将从业务逻辑开始。持久性层将紧随其后，最后我们将查看一个 HTTP 端点，它将作为应用程序的入口点。

## Deno 作为一款无偏见的工具

当我们使用将许多决策委托给开发者的低级工具，如 Node.js 和 Deno 时，结构化应用程序就成为了一个主要挑战。

这与具有明确观点的 Web 框架，如 PHP Symfony、Java SpringBoot 或 Ruby on Rails，有很大的不同，因为这些框架为我们做了许多决策。

这些决策大多数与结构有关；也就是说，代码和文件夹结构。那些框架通常为我们提供处理依赖关系、导入的方法，甚至为不同的应用层提供一些指导。由于我们使用的是*原始*语言和几个包，所以我们将在本书中自己负责结构。

前述的框架不能直接与 Deno 比较，因为它们是建立在诸如 PHP、Java 和 Ruby 等语言之上的框架。但当我们观察 JS 世界，也就是 Node.js 时，我们可以发现最流行的创建 HTTP 服务器的工具是 Express.js 和 Kao.js。这些通常比前述的框架轻量许多，尽管也有一些像 Nest.js 或 hapi.js 这样坚实的完整替代品，但 Node.js 社区更倾向于采用*库*方法，而不是*框架*方法。

尽管这些非常流行的库提供了大量功能，但许多决策仍然委托给开发者。这不是库的错，更多的是一个社区偏好。

一方面，直接访问这些原生对象让我们能构建非常适合我们用例的应用程序。另一方面，灵活性是有代价的。拥有极大的灵活性随之而来的是做出无数决策的责任。而在做出许多决策时，有很多机会做出糟糕的决策。难点在于，这些通常是对代码库扩展方式产生巨大影响的决策，这就是它们如此重要的原因。

在当前状态下，Deno 及其社区在框架与库这一问题上遵循与 Node.js 非常相似的方法。社区主要押注于开发者为满足他们特定需求而创建的轻量级、小型软件。我们稍后将在本章中评估一些这些软件。

从现在开始，贯穿整本书，我们将使用一种我们相信对当前用例有很大好处的应用程序结构。然而，不要期望这种结构和架构是灵丹妙药，因为我们深信软件世界中不存在这样的东西；每种架构都将随着成长而不断进化。

我们不仅仅是要按照食谱机械地操作，而是要熟悉一种思维方式——一种逻辑。这应该能让我们在后续的道路上做出正确的决策，目标明确：*编写易于更改的代码*。

通过编写易于更改的代码，我们总是准备好在不需要太多努力的情况下改进我们的应用程序。

## 应用程序最重要的部分

应用程序是为了适应一个目的而创建的。无论这个目的是支持一个企业还是一个简单的宠物项目都不重要。到最后，我们希望它能做些什么。那个*做些什么*就是让应用程序变得有用的原因。

这可能看起来很显然，但对于我们这些开发者来说，有时候很容易因为对某项技术充满热情而忘记，它只是一个达成目标的手段。

正如罗伯特叔叔在他的*架构——失去的岁月*演讲中说的([`www.youtube.com/watch?v=hALFGQNeEnU`](https://www.youtube.com/watch?v=hALFGQNeEnU)),人们很容易忘记应用程序的目的，而更多地关注技术本身。我们在应用程序开发的各个阶段都记住这一点非常重要，但在设置其初始结构时尤为关键。接下来，我们将发现本书剩余部分我们将要构建的应用程序的需求。

## 我们的应用程序是关于什么的？

尽管我们真心相信业务逻辑是任何应用程序最重要的事情，但在本书中，情况有点不同。我们将创建一个示例应用程序，但它只是一个达到主要目标的手段：学习 Deno。然而，为了使过程尽可能真实，我们希望在心中有一个清晰的目标。

我们将构建一个应用程序，让人们可以创建和与博物馆列表进行互动。我们可以通过将其功能作为用户故事来使其更清晰，如下所示：

+   用户可以注册和登录。

+   用户可以创建一个带有标题、描述和位置的博物馆。

+   用户可以查看博物馆列表。

在这个旅程中，我们将开发 API 和支持这些功能的逻辑。

现在我们已经了解了最终目标，我们可以开始思考如何组织应用程序。

## 理解文件夹结构和应用程序架构

关于文件夹结构，我们需要注意的第一个问题是，尤其是当我们从零开始一个没有框架的项目时，它会随着项目的进行而不断演变。一个对于只有几个端点的项目来说不错的文件夹结构，对于有数百个端点的项目来说可能就不那么好了。这取决于很多因素，从团队规模，到制定的标准，最终到个人偏好。

在定义文件夹结构时，重要的是我们要达到一个可以促进未来关于如何定位一段代码的决策的地方。文件夹结构应该为如何做出良好的架构决策提供清晰的提示。

同时，我们当然不希望创建一个过度工程化的应用程序。我们将创建足够的抽象，以便模块非常紧凑，并且不知道它们域外的知识，但不会超过这个程度。牢记这一点也迫使我们构建灵活的代码和清晰的接口。

最终，最重要的是架构能够使代码库如下所示：

+   可测试

+   易于扩展

+   与特定技术或库松耦合

+   易于导航和推理

在创建文件夹、文件和模块时，我们必须记住，我们不想牺牲前面列出的任何主题。

这些原则与软件设计中的 SOLID 原则非常一致，这些原则由 Uncle Bob，Robert C. Martin（[`en.wikipedia.org/wiki/SOLID`](https://en.wikipedia.org/wiki/SOLID)）在另一个值得一看的演讲中提出（[`youtu.be/zHiWqnTWsn4`](https://youtu.be/zHiWqnTWsn4)）。

本书我们将要使用的文件夹结构，如果你有 Node.js 背景，可能会觉得熟悉。

正如发生在 Node.js 上一样，我们创建一个完整的 API 的单个文件没有任何障碍。然而，我们不会这样做，因为我们相信一些初步的关注点分离将大大提高我们的灵活性，而不会牺牲开发者的生产力。

在接下来的部分，我们将探讨不同层的责任以及它们如何在开发应用程序功能时相互配合。

遵循这种思路，我们努力保证模块之间的解耦程度。例如，我们希望能够确保在更改 Web 框架时，不必触摸业务逻辑对象。

所有这些建议，以及我们在这本书中将会提出的建议，将有助于确保我们应用程序的核心部分是业务逻辑，其他所有内容只是插件。JSON API 只是向用户发送我们数据的方式，而数据库只是持久化数据的方式；这两者都不应成为应用程序的核心部分。

确保我们这样做的一种方法是在编写代码时进行以下心理练习：

"当你编写业务逻辑时，想象这些对象将在不同的上下文中使用。例如，使用不同的交付机制（例如 CLI）或不同的持久化引擎（内存数据库而非 NoSQL 数据库）。"

在接下来的几页中，我们将引导您创建不同的层，并解释所有设计决策以及它们所启用的功能。

让我们来实践并开始构建我们项目的基础。

### 定义文件夹结构

在我们项目的文件夹中做的第一件事是创建一个`src`文件夹。

这是我们代码将存放的地方。我们不希望有任何代码位于项目的根级别，因为可能会添加配置文件、READMEs、文档文件夹等。这将使得代码难以区分。

在接下来的章节中，我们大部分时间将在`src`文件夹内度过。由于我们的应用程序是关于博物馆的，我们将在`src`文件夹内创建一个名为`museums`的文件夹。这个文件夹将是本章将编写大部分逻辑的地方。后来，我们将创建类型、控制器和服务器的文件。然后，我们将创建`src/web`文件夹。

控制器的文件是承载我们业务逻辑的地方。仓库将处理与数据访问相关的逻辑，而网络层将处理所有*与网络相关*的事情。

你可以通过查看本书的 GitHub 仓库来了解最终结构会是什么样子：[`github.com/PacktPublishing/Deno-Web-Development/tree/master/Chapter04/museums-api`](https://github.com/PacktPublishing/Deno-Web-Development/tree/master/Chapter04/museums-api)。

本章的初始要求是有一个路由，我们可以在此执行 GET 请求并收到以 JSON 格式表示的博物馆列表。

我们将在控制器文件（`src/museums/controller.ts`）中编写所需的业务逻辑。

文件夹结构应该如下所示：

```js
└── src
    ├── museums
    │   ├── controller.ts
    │   ├── repository.ts
    │   └── types.ts
    └── web
```

这是我们出发的起点。所有与博物馆相关的内容都将位于`museums`文件夹内，我们将之称作一个模块。`controller`文件将承载业务逻辑，`repository`文件将承载数据获取功能，而`types`文件将是我们放置类型的位置。

现在，让我们开始编码！

## 开发业务逻辑

我们之前说过，我们的业务逻辑是应用程序最重要的部分。尽管现在我们的会非常简单，但这是我们首先开发的。

由于我们将使用 TypeScript 来编写我们的应用程序，让我们来创建一个定义我们的`Museum`对象的接口。按照以下步骤操作：

1.  进入`src/museums/types.ts`并创建一个定义`Museum`的类型：

    ```js
    export type Museum = {
      id: string,
      name: string,
      description: string,
      location: {
        lat: string,
        lng: string
      }
    }
    ```

    确保它被导出，因为我们将跨其他文件使用这个。

    既然我们知道了类型，我们必须创建一些业务逻辑来获取博物馆列表。

1.  在`src/museums/types.ts`内，创建一个定义`MuseumController`的接口。它应该包含一个列出所有博物馆的方法：

    ```js
    export interface MuseumController {
      getAll: () => Promise<Museum[]>;
    }
    ```

1.  在`src/museums/controller.ts`内，创建一个类作为控制器。它应该包含一个名为`getAll`的函数。将来，这里将是业务逻辑的所在，但目前，我们只需返回一个空数组：

    ```js
    import type { MuseumController } from "./types.ts";
    export class Controller implements MuseumController {
      async getAll() {
        return [];
      }
    } 
    ```

    我们本可以直接访问数据库并获取某些记录。然而，由于我们希望能够使我们的业务逻辑得到隔离，并不与应用程序的其他部分耦合，所以我们不会这样做。

    此外，我们还希望业务逻辑能够独立于数据库或服务器的连接进行测试。为了实现这一点，我们不能直接从我们的控制器访问数据源。稍后，我们将创建一个抽象，它将负责从数据库获取这些记录。

    现在，我们知道我们将需要调用一个外部模块，它将为我们获取所有的博物馆，并将它们交给我们的控制器——它从哪里来无关紧要。

    请记住以下软件设计最佳实践：*"面向接口编程，而不是面向实现。"*

    简单来说，这个引言的意思是我们应该定义模块的签名，然后才开始思考它的实现。这对设计清晰的接口非常有帮助。

    回到我们的控制器，我们知道控制器的`getAll`方法最终必须调用一个模块以从数据源获取数据。

1.  在`src/museums/types.ts`中，定义`MuseumRepository`，这个模块将负责从数据源获取博物馆：

    ```js
    export interface MuseumRepository {
      getAll: () => Promise<Museum[]>
    }
    ```

1.  在`src/museums/controller.ts`中，向构造函数添加一个注入的类`museumRepository`：

    ```js
    museumRepository that implements the MuseumRepository interface. By creating this and *lifting the dependencies*, we no longer need to return an empty array from our controller.Before we write any more logic, let's make sure our code runs and check if it is working. We're just missing one thing.
    ```

1.  创建一个名为`src/index.ts`的文件，导入`MuseumController`，实例化它，并调用`getAll`方法，记录其输出。现在，你可以注入一个伪仓库，它只是返回一个空数组：

    ```js
    import { Controller as MuseumController } from
      "./museums/controller.ts";
    const museumController = new MuseumController({
      museumRepository: {
        getAll: async () => []
      }
    })
    console.log(await museumController.getAll())
    ```

1.  运行它以检查是否正常工作：

    ```js
    $ deno run src/index.ts 
    []
    ```

    就这样！我们刚刚从伪仓库函数中得到了一个空数组！

有了我们创建的抽象，我们的控制器现在与数据源解耦。它的依赖关系通过构造函数注入，允许我们稍后不改变控制器而更改仓库。

我们刚刚所做的称为**依赖倒置**——SOLID 原则中的**D**——它包括将部分依赖性提升到函数调用者。这使得独立测试内部函数变得非常容易，正如我们将在*第八章*，*测试——单元和集成*中看到的，我们将涵盖测试。

要将我们刚刚编写的代码转换为完全功能的应用程序，我们需要有一个数据库或类似的东西。我们需要一个可以存储和检索博物馆列表的东西。现在让我们创建这个东西。

## 开发数据访问逻辑

在开发控制器时，我们注意到我们需要一个能够获取数据的东西，即仓库。这个模块将抽象所有对数据源的调用，在这个案例中，数据源存储博物馆。它将有一组非常明确的方法，任何想要访问数据的人都应该通过这个模块进行访问。

我们已经在`src/museums/types.ts`中定义了其接口的一部分，所以让我们写一个实现它的类。现在，我们不会将它连接到真实的数据库。我们将使用 ES6 Map 作为内存中的数据库。

让我们打开我们的仓库文件，并按照以下步骤开始编写我们的数据访问逻辑：

1.  打开`src/museums/repository.ts`文件，创建一个`Repository`类。

    它应该有一个名为`storage`的属性，这将是一个 JavaScript `Map`。`Map`的键应该是字符串，值应该是`Museum`类型的对象：

    ```js
    import type { Museum, MuseumRepository } from
      "./types.ts";
    export class Repository implements MuseumRepository {
      storage = new Map<string, Museum>();
    }
    ```

    我们使用 TypeScript 泛型来设置我们的`Map`的类型。请注意，我们已经从博物馆控制器中导入了`Museum`接口，以及由我们的类实现的`MuseumRepository`。

    既然“数据库”已经“准备就绪”，我们必须暴露某些方法，以便人们可以与之交互。上一部分的要求是我们可以从数据库中获取所有记录。让我们接下来实现这一点。

1.  在仓库类内部，创建一个名为`getAll`的方法。它应该负责返回我们`storage` `Map`中的所有记录：

    ```js
    export class Repository implements MuseumRepository {
      storage = new Map<string, Museum>();
    src should only be accessible from the outside through a single file. This means that whoever wants to import stuff from src/museums should only do so from a single src/museums/index.ts file.
    ```

1.  创建一个名为`src/museums/index.ts`的文件，导出博物馆的控制器和仓库：

    ```js
    export { Controller } from "./controller.ts";
    export { Repository } from "./repository.ts";
    export type { Museum, MuseumController,
      MuseumRepository } from "./types.ts"; 
    ```

    为了保持一致性，我们需要去所有之前从不是`src/museums/index.ts`的文件中导入的导入，并更改它们，使它们只从这个文件中导入东西。

1.  更新`controller.ts`和`repository.ts`的导入，使它们从`index`文件中导入：

    ```js
    import type { MuseumController, MuseumRepository }
      from "./index.ts";
    ```

    你可能已经猜到我们接下来必须做什么了……你还记得上一部分末尾吗，我们在博物馆控制器中注入了一个虚拟函数，它返回一个空数组？让我们回到这一点并使用我们刚刚编写的逻辑。

1.  回到`src/index.ts`，导入我们刚刚开发的`Repository`类，并将其注入到`MuseumController`构造函数中：

    ```js
    import {
      Controller as MuseumController,
      Repository as MuseumRepository,
    } from "./museums/index.ts";
    const museumRepository = new MuseumRepository();
    const museumController = new MuseumController({
      museumRepository })
    console.log(await museumController.getAll())
    ```

    现在，让我们向我们的“数据库”添加一个测试数据，这样我们就可以检查它是否实际上正在打印一些内容。

1.  访问`museumRepository`中的`storage`属性，并向其添加一个测试数据。

    这目前是一个反模式，因为我们直接访问了模块的数据库，但我们将创建一个方法，以便我们以后可以正确地添加测试数据：

    ```js
    const museumRepository = new MuseumRepository();
    …
    museumRepository.storage.set
      ("1fbdd2a9-1b97-46e0-b450-62819e5772ff", {
      id: "1fbdd2a9-1b97-46e0-b450-62819e5772ff",
      name: "The Louvre",
    description: "The world's largest art museum 
        and a historic monument in Paris, France.",
      location: {
        lat: "48.860294",
        lng: "2.33862",
      },
    });
    console.log(await museumController.getAll())
    ```

1.  现在，让我们再次运行我们的代码：

    ```js
    $ deno run src/index.ts
    [
      {
        id: "1fbdd2a9-1b97-46e0-b450-62819e5772ff",
        name: "The Louvre",
        description: "The world's largest art
          museum and a historic monument in Paris,
            France.",
        location: { lat: "48.860294", lng: "2.33862" }
      }
    ]
    ```

    有了这个，我们数据库的连接就可以工作了，正如我们通过打印测试数据所看到的那样。

我们在上一部分创建的抽象使我们能够在不更改控制器的情况下更改数据源。这是我们正在使用的架构的一个优点。

现在，如果我们回顾我们的初始需求，我们可以确认我们已经完成了一半。我们已经创建了满足用例的业务逻辑——我们只是缺少 HTTP 部分。

## 创建 Web 服务器

现在我们已经有了我们的功能，我们需要通过 Web 服务器暴露它。让我们使用我们从标准库学到的知识来创建它，并按照以下步骤进行：

1.  在`src/web`文件夹中创建一个名为`index.ts`的文件，并在那里添加创建服务器的逻辑。我们可以从上一章的练习中复制并粘贴它：

    ```js
    import { serve } from
      "https://deno.land/std@0.83.0/http/server.ts";
    const PORT = 8080;
    const server = serve({ port: PORT });
    console.log(`Server running at
      https://localhost:${PORT}`);
    for await (let req of server) {
      req.respond({ body: 'museums api', status: 200 })
    }
    ```

    由于我们希望应用程序易于配置，我们不希望`port`在这里是硬编码的，而是可以从外部配置的。我们需要将这个服务器创建逻辑作为一个函数导出。

1.  将服务器逻辑创建封装在一个接收配置和`port`作为参数的函数中：

    ```js
    import { serve } from
      "https://deno.land/std@0.83.0/http/server.ts";
    port defining its type. 
    ```

1.  将这个函数的参数设置为`接口`。这将有助于我们的文档工作，并且还会增加类型安全和静态检查：

    ```js
    interface CreateServerDependencies {
      configuration: {
        port: number
      }
    }
    export async function createServer({
      configuration: {
        port
      }
    }: CreateServerDependencies) {
    …
    ```

    现在我们已经配置了网络服务器，我们可以考虑为其使用场景使用它。

1.  回到`src/index.ts`，导入`createServer`，并使用它创建一个在端口`8080`上运行的服务器：

    ```js
    import { createServer } from "./web/index.ts";
    …
    createServer({
      configuration: {
        port: 8080
      }
    })
    …
    ```

1.  运行它并看看它是否起作用：

    ```js
    $ deno run --allow-net src/index.ts
    Server running at http://localhost:8080
    [
      {
        id: "1fbdd2a9-1b97-46e0-b450-62819e5772ff",
        name: "The Louvre",
        description: "The world's largest art museum and a
          historic monument in Paris, France.",
        location: { lat: "48.860294", lng: "2.33862" }
      }
    ]
    ```

在这里，我们可以看到有一个日志记录服务器正在运行，以及来自上一节的日志结果。

现在，我们可以使用`curl`测试网络服务器以确保它正在工作：

```js
$ curl http://localhost:8080
museums api
```

正如我们所看到的，它起作用了——我们有一些相当基础的逻辑，但这仍然不能满足我们的要求，却能启动一个网络服务器。我们接下来要做的就是将这个网络服务器与之前编写的逻辑连接起来。

## 将网络服务器与业务逻辑连接起来

我们离完成本章开始时计划做的事情已经相当接近了。我们目前有一个网络服务器和一些业务逻辑；缺少的是连接。

将两者连接起来的一个快速方法是将控制器直接导入`src/web/index.ts`并在那里使用它。在这里，应用程序将具有所需的行为，目前，这并没有带来任何问题。

然而，由于我们考虑的是一个可以无需太多问题就能扩展的应用程序架构，我们不会这样做。这是因为这将使测试我们的网络逻辑变得非常困难，从而违背了我们的一条原则。

如果我们直接从网络服务器导入控制器，那么每次在测试环境中调用`createServer`函数时，它都会自动导入并调用`MuseumController`中的方法，这不是我们想要的结果。

我们再次使用依赖倒置将控制器的函数发送到网络服务器。如果这仍然看起来过于抽象，不要担心——我们马上就会看到代码。

为了确保我们没有忘记我们的初始目标，我们想要的是，当用户对`/api/museums`进行`GET`请求时，我们的网络服务器返回一个博物馆列表。

由于我们正在进行这个练习，我们暂时不会使用路由库。

我们只想添加一个基本检查，以确保请求的 URL 和方式是我们希望回答的。如果是，我们想返回博物馆列表。

让我们回到`createServer`函数并添加我们的路由处理程序：

```js
export async function createServer({
  configuration: {
    port
  }
}: CreateServerDependencies) {
  const server = serve({ port });
  console.log(`Server running at
    http://localhost:${port}`);
  for await (let req of server) {
    if (req.url === "/api/museums" && req.method === "GET")     
     {
req.respond({ 
body: JSON.stringify({ 
museums: [] 
}), 
status: 200 
      })
      continue
    }
    req.respond({ body: "museums api", status: 200 })
  }
}
```

我们添加了对请求 URL 和方法的基本检查，以及它们符合初始要求时的不同响应。让我们运行代码看看它的行为如何：

```js
$ deno run --allow-net src/index.ts 
Server running at http://localhost:8080
```

再次，用`curl`测试它：

```js
$ curl http://localhost:8080/api/museums
{"museums":[]}
```

它起作用了——酷！

现在是我们定义需要什么来满足这个请求作为接口的部分。

我们最终需要一个返回博物馆列表的函数，以注入到我们的服务器中。让我们按照以下步骤在`CreateServerDependencies`接口中添加该功能：

1.  回到`src/web/index.ts`中，将`MuseumController`作为`createServer`函数的一个依赖项添加：

    ```js
    MuseumController type we defined in the museum's module. We're also adding a museum object alongside the configuration object.
    ```

1.  从博物馆控制器中调用`getAll`函数以获取所有博物馆的列表并响应请求：

    ```js
    export async function createServer({
      configuration: {
        port
      },
      createServer function, but we're not sending it when we call createServer. Let's fix that.
    ```

1.  回到`src/index.ts`，这是我们调用`createServer`函数的地方，并发送来自`MuseumController`的`getAll`函数。你也可以删除上一节直接调用控制器方法的代码，因为此刻它没有任何用处：

    ```js
    import { createServer } from "./web/index.ts";
    import {
      Controller as MuseumController,
      Repository as MuseumRepository,
    } from "./museums/index.ts";
    const museumRepository = new MuseumRepository();
    const museumController = new MuseumController({
      museumRepository })
    museumRepository.storage.set
     ("1fbdd2a9-1b97-46e0-b450-62819e5772ff", {
      id: "1fbdd2a9-1b97-46e0-b450-62819e5772ff",
      name: "The Louvre",
      description: "The world's largest art museum 
        and a historic monument in Paris, France.",
      location: {
        lat: "48.860294",
        lng: "2.33862",
      },
    });
    createServer({
      configuration: { port: 8080 },
      museum: museumController
    })
    ```

1.  再次运行应用程序：

    ```js
    $ deno run --allow-net src/index.ts
    Server running at http://localhost:8080
    ```

1.  向 http://localhost:8080/api/museums 发送请求；你会得到一个博物馆列表：

    ```js
    $ curl localhost:8080/api/museums
    {"museums":[{"id":"1fbdd2a9-1b97-46e0-b450-62819e5772ff","name":"The Louvre","description":"The world's largest art museum and a historic monument in Paris, France.","location":{"lat":"48.860294","lng":"2.33862"}}]}
    ```

就这样——我们得到了博物馆的列表！

我们已经完成了本节的 goals；也就是说，将我们的业务逻辑连接到 web 服务器。

注意我们是如何使控制器方法可以被注入，而不是 web 服务器直接导入它。这之所以可能，是因为我们使用了依赖倒置。这是我们在这本书中会持续做的事情，无论何时我们想要解耦并增加模块和函数的可测试性。

在我们进行代码耦合的思维锻炼时，当我们想要将当前的业务逻辑与不同的交付机制（如 CLI）结合使用时，没有任何阻碍。我们仍然可以重用相同的控制器和存储库。这意味着我们很好地使用了抽象来将业务逻辑与应用程序逻辑解耦。

现在我们已经有了应用程序架构和文件结构的基线，并且也理解了背后的*原因*，我们可以开始查看可能帮助我们构建它的工具。

在下一节中，我们将看看 Deno 社区中现有的 HTTP 框架。我们不会花太多时间在这方面，但我们需要了解每个框架的优缺点，并最终选择一个来帮助我们完成余下的旅程。

# 探索 Deno HTTP 框架

当你构建一个比简单教程更复杂的应用程序时，如果你不想采取纯粹主义方法，你很可能会使用第三方软件。

显然，这不仅仅是 Deno 特有的。尽管有些社区比其他社区更愿意使用第三方模块，但所有社区都在使用第三方软件。

我们可以讨论人们为什么这样做或不做，但更常见的原因总是与可靠性和时间管理有关。这可能是因为你想使用经过实战测试的软件，而不是自己构建。有时，这只是时间管理问题，即不想重写已经创建的东西。

有一点我们必须说的是，我们必须极其谨慎地考虑我们构建的应用程序中有多少与第三方软件耦合。我们并不是说你应该试图达到完全解耦的乌托邦，尤其是因为这会引入其他问题和大量的间接性。我们要说的是，我们应该非常清楚地将一个依赖项引入我们的代码以及它引入的权衡。

在本章的第一部分，我们构建了一个 web 应用程序的基础，我们将在本书的其余部分向其添加功能。在其当前状态，它仍然非常小，因此除了标准库之外，没有其他依赖。

在那篇应用中，我们做了一些我们认为不会很好扩展的事情，比如使用普通的`if`语句根据 URL 和 HTTP 方法定义路由。

随着应用程序的增长，我们可能会需要更高级的功能。这些需求可能从处理不同格式的 HTTP 请求体，到拥有更复杂的路由系统，处理头部和 cookies，或连接数据库。

因为我们不相信在开发应用程序时重新发明轮子，所以我们将分析 Deno 社区目前存在的几个库和框架，它们专注于创建 web 应用程序。

我们将对现有的解决方案进行一般性的观察，并探索它们的功能和方法。

最后，我们将选择我们认为最适合我们用例的一个。

## 存在哪些替代方案？

在撰写本文时，有几个第三方包提供了大量功能来创建 web 应用程序和 API。其中一些深受非常流行的 Node.js 包（如 Express.JS、Koa 或 hapi.js）的启发，而其他则受到 JavaScript 之外的框架（如 Laravel、Flask 等）的启发。

我们将探索其中的四个，这些在撰写本文时非常流行且维护得很好。请注意，由于 Deno 和提到的包正在快速发展，这可能会随时间而变化。

重要提示

Craig Morten 撰写了一篇非常彻底的分析文章，探讨了可用的库。如果你想要了解更多，我强烈推荐这篇文章（[`dev.to/craigmorten/what-is-the-best-deno-web-framework-2k69`](https://dev.to/craigmorten/what-is-the-best-deno-web-framework-2k69)）。

我们将尝试在我们将要探索的包中保持多样性。有些包提供了比其他包更多的抽象和结构，而有些包提供的功能并不多于纯粹的实用函数和可组合的功能。

我们将探索的包如下：

+   Drash

+   Servest

+   Oak

+   Alosaur

让我们逐一看看。

### Drash

Drash ([`github.com/drashland/deno-drash`](https://github.com/drashland/deno-drash))旨在与现有的 Deno 和 Node.js 框架不同。这一动机在其维护者 Edward Bebbington 的一篇博客文章中明确提到，他比较了 Drash 与其他替代方案，并解释了其创建的动机 ([`dev.to/drash_land/what-makes-drash-different-idd`](https://dev.to/drash_land/what-makes-drash-different-idd)).

这些动机很好，而且像 Laravel、Flask 和 Tonic 这样非常流行的软件工具的启发证明了许多这些决定是合理的。你一查看 Drash 的代码，就能发现一些相似之处。

与 Express.js 或 Koa 等库相比，它确实提供了一种不同的方法，正如文档所述：

“Deno 与 Node.js 的不同之处在于，Drash 旨在与 Express 或 Koa 不同，利用资源和一个完整的基于类的系统。”

主要区别在于，Drash 不想提供应用程序对象，让开发者可以在其中注册他们的端点，就像一些流行的 Node.js 框架一样。它将端点视为在类内部定义的资源，类似于以下内容：

```js
import { Drash } from
  "https://deno.land/x/drash@v1.2.2/mod.ts";
class HomeResource extends Drash.Http.Resource {
  static paths = ["/"];
  public GET() {
    this.response.body = "Hello World!";
    return this.response;
  }
}
```

这些资源随后被插入到 Drash 的应用程序中：

```js
const server = new Drash.Http.Server({
  response_output: "text/html",
  resources: [HomeResource]
});
server.run({
  hostname: "localhost",
  port: 1447
});
```

在这里，我们可以直接声明，它实际上与我们在前面提到的其他框架不同。这些差异是有意的，旨在满足喜欢这种方法及其为其他框架解决问题的开发者。这些用例在 Drash 的文档中有很好的解释。

Drash 基于资源的的方法确实值得关注。它从非常成熟的软件（如 Flask 和 Tonic）中汲取灵感，确实为桌面上带来了一些东西，并提出了一种解决方案，有助于解决一些无观点工具的常见问题。文档完整且易于理解，这使得在选择构建应用程序的工具时，它成为了一个宝贵的资产。

### Servest

Servest ([`servestjs.org/`](https://servestjs.org/))自称是一个*"适用于 Deno 的渐进式 HTTP 服务器。"*

它被创建的一个原因是因为其作者想要使标准库的 HTTP 模块中的一些 API 更容易使用，并实验新功能。后者是在需要稳定性的标准库上真正难以实现的。

Servest 直接关注与标准库的 HTTP 模块的比较。其主要目标之一是在项目的主页上直接声明，即使其易于从标准库的 HTTP 模块迁移到 Servest。这很好地总结了 Servest 的愿景。

从 API 的角度来看，Servest 与我们从 Express.js 和 Koa 熟悉的东西非常相似。它提供了一个应用程序对象，可以在其中注册路由。你也可以看到以下代码片段中明显受到了标准库模块提供的内容的启发：

```js
import { createApp } from
  "https://servestjs.org/@v1.1.4/mod.ts";
const app = createApp();
app.handle("/", async (req) => {
  await req.respond({
    status: 200,
    headers: new Headers({
      "content-type": "text/plain",
    }),
    body: "Hello, Servest!",
  });
});
app.listen({ port: 8899 });
```

我们可以识别出来自知名 Node.js 库的应用程序对象和来自标准库的请求对象等功能。

在这些功能之上，Servest 还提供了诸如直接渲染 JSX 页面、提供静态文件和身份验证等常见功能。文档也非常清晰，充满了示例。

Servest 试图利用 Node.js 用户的知识和熟悉度，同时利用 Deno 提供的优势，这是一个很有前途的混合。它逐渐发展的特性为开发人员提供了非常好的功能，承诺会使开发人员比使用标准库 HTTP 包时更加高效。

### Oak

Oak ([`oakserver.github.io/oak/`](https://oakserver.github.io/oak/)) 目前是创建网络应用程序最受欢迎的 Deno 库。它的名字来源于 Koa 的词语游戏，Koa 是一个非常流行的 Node.js 中间件框架，也是 Oak 的主要灵感来源。

由于其深受启发，其 API 与 Koa 相似，使用异步函数和上下文对象也就不足为奇了。Oak 还包括一个路由器，也是受 `@koa/router` 启发的。

如果你知道 Koa，下面的代码对你来说可能非常熟悉：

```js
import { Application } from
  "https://deno.land/x/oak/mod.ts";
const app = new Application();
app.use((ctx) => {
  ctx.response.body = "Hello world!";
});
await app.listen("127.0.0.1:8000");
```

对于不熟悉 Koa 的你们，我们将简要解释一下，因为理解它将帮助你理解 Oak。

Koa 通过使用现代 JavaScript 特性提供了一个最小化和不带偏见的的方法。Koa 最初被创建（由创建 Express.js 的同一团队）的原因之一是，其创建者想要创建一个利用现代 JavaScript 特性的框架，而不是像 Express 那样，在 Node.js 的早期就被创建。

团队希望使用诸如 promises 和 async/await 等新特性，然后解决开发者在使用 Express.JS 时面临的挑战。这些挑战大多数与错误处理、处理回调和某些 API 的缺乏清晰性有关。

Oak 的流行并非空穴来风，它在 GitHub 上的星级与其他替代方案的距离反映了这一点。单凭 GitHub 的星级并不能说明什么，但是结合打开和关闭的问题、发布的版本等等，我们可以看出人们为什么信任它。当然，这种熟悉在很大程度上影响了这个包的流行度。

在当前状态下，Oak 是一个坚实的（就 Deno 社区标准而言）构建网络应用程序的方式，因为它提供了一组非常清晰和直接的功能。

### Alosaur

Alosaur（[`github.com/alosaur/alosaur`](https://github.com/alosaur/alosaur)）是一个基于装饰器和类的 Deno 网络应用程序框架。它在某种程度上与 Drash 相似，尽管最终的方法有很大不同。

作为其主要特性之一，Aloaur 提供诸如模板渲染、依赖注入和 OpenAPI 支持等功能。这些功能是在所有我们在这里介绍的替代方案的标准之上添加的，例如中间件支持和路由。

这个框架的方法是使用类定义控制器，并使用装饰器定义其行为，如下面的代码所示：

```js
import { Controller, Get, Area, App } from
  'https://deno.land/x/alosaur@v0.21.1/mod.ts';
@Controller() // or specific path @Controller("/home")
export class HomeController {
    @Get() // or specific path @Get("/hello")
    text() {
        return 'Hello world';
    }
}
// Declare module
@Area({
    controllers: [HomeController],
})
export class HomeArea {}
// Create alosaur application
const app = new App({
    areas: [HomeArea],
});
app.listen();
```

在这里，我们可以看到应用程序的实例化与 Drash 有相似之处。它还使用 TypeScript 装饰器来声明框架的行为。

Alosaur 与前面提到的大多数库相比采取了不同的方法，主要是因为它不试图简约。相反，它提供了一组在构建某些类型的应用程序时证明有用的特性。

我们决定看看它，不仅是因为它能做到它应该做的事情，而且还因为它在 Node.js 和 Deno 世界中有一些不常见的特性。这包括像依赖注入和 OpenAPI 支持这样的东西，这是其他展示的解决方案所没有提供的。同时，它保持了如模板渲染之类的特性，这可能是您从 Express.JS 熟悉的东西，但在更现代的框架中可能不那么熟悉。

最终的解决方案在提供的功能方面非常有前途且完整。这绝对是值得关注的东西，这样你可以看到它是如何发展的。

## 最终判决

在查看了所有展示的解决方案并认识到它们都有自己的优点之后，我们决定在本书的剩余部分使用 Oak。

这并不意味着本书将重点介绍 Oak。不会的，因为它只会处理 HTTP 和路由。Oak 的简约方法将与我们接下来要做的很多事情非常契合，帮助我们逐步创建功能而不会妨碍我们。它也是 Deno 社区中最稳定、维护得最好、最受欢迎的选项之一，这对我们的决定有明显的影响。

请注意，这个决定并不意味着我们将在接下来的几章中学到的内容不能在任何替代方案中完成。事实上，由于我们将如何组织和架构我们的代码，我们相信很容易使用不同的框架来完成我们将要做的绝大多数事情。

在本书的剩余部分，我们将使用其他第三方模块来帮助我们构建我们提出的功能。我们决定深入研究处理 HTTP 的库的原因是因为这是我们即将开发的应用程序的基本交付机制。

# 总结

在本章中，我们终于开始构建一个利用我们对 Deno 知识的应用程序。我们首先考虑了构建应用程序时我们将拥有的主要目标以及定义其架构。这些目标将为我们本书中关于架构和结构的绝大多数对话定下基调，因为我们将会不断回顾它们，确保我们的工作与它们保持一致。

我们首先创建了我们的文件夹结构，并试图实现我们第一个应用程序目标：拥有一个列出博物馆的 HTTP 端点。我们首先构建了简单的业务逻辑，并在需要关注分离和隔离以及职责时逐步推进。这些需求引导了我们的架构，证明了我们所创建的层次和抽象的有用性，并展示了它们所提供的价值。

通过明确职责和模块接口，我们了解到我们可以暂时使用内存数据库来构建我们的应用程序，我们也确实这样做了。借助这种分离层次结构的方法，我们可以稍后回来将其更改为合适的持久性层而不会遇到任何问题。在定义业务和数据访问逻辑之后，我们使用标准库创建了一个 web 服务器作为交付机制。在创建了一个非常简单的路由系统之后，我们插入了之前构建的业务逻辑，满足了本章的主要要求：拥有一个返回博物馆列表的应用程序。

所有这些工作都没有在业务逻辑、数据获取和交付层之间创建直接耦合。我们认为这在开始增加复杂性、扩展我们的应用程序并为其添加测试时会非常有用。

本章通过查看 Deno 社区目前存在的 HTTP 框架和库以及它们之间的差异和做法来结束。其中一些使用对 Node.js 用户熟悉的做法，而其他则深入使用 TypeScript 及其特性来创建更具结构的 web 应用程序。通过查看目前可用的四个解决方案，我们了解了社区正在开发的内容以及他们可能采取的方向。

我们最终选择了 Oak，这是一个非常简洁且相对成熟解决方案，以帮助解决本书后续内容中我们将遇到的路由和 HTTP 挑战。

在下一章中，我们将开始将 Oak 添加到我们的代码库中，并使用诸如身份验证和授权等有用功能，使用诸如中间件等概念，并使我们的应用程序增长，以实现我们为自己设定的目标。

让我们开始吧！
