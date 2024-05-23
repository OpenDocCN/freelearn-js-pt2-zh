# 第三章：第*5 章*：添加用户和迁移到 Oak

至此，我们已经为具有结构的 Web 应用程序奠定了基础，这将使我们能够在继续进行时添加更多功能。正如您可能已经从本章的名称猜测到的，我们将从向当前 Web 应用程序，Oak，添加我们选择的中间件框架开始本章。

与 Oak 一起，由于我们的应用程序开始有了更多的第三方依赖，我们将运用前面章节中学到的知识创建一个锁文件，并在安装依赖时执行完整性检查。这样，我们可以确保我们的应用程序在无依赖问题的情况下运行顺畅。

随着本章的深入，我们将开始了解如何使用 Oak 的功能简化我们的代码。我们将使我们的路由逻辑更具扩展性，同时也更具可伸缩性。我们最初的解决方案是使用`if`语句和标准库一起创建一个 DIY 路由解决方案，我们将在这里重构它。

完成这一点后，我们将得到更干净的代码，并能够使用 Oak 的功能，如自动内容类型定义、处理不允许的方法和路由前缀。

然后，我们将添加一个在几乎每个应用程序中都非常重要的功能：用户。我们将创建一个与博物馆并列的模块来处理所有与用户相关的事情。在这个新模块中，我们将开发创建用户的业务逻辑，以及使用常见的实践（如散列和盐）在数据库中创建新用户的代码。

在实现这些功能的过程中，我们将了解到 Deno 提供的其他模块，比如标准库的散列功能或包含在运行时中的加密 API。

向应用程序中添加这个新模块，并让它与其他应用程序部分进行交互，将是一种很好的测试应用程序架构的方法。通过这样做，我们将了解它在保持与上下文相关的所有内容在一个地方的同时如何扩展。

本章将涵盖以下主题：

+   管理依赖项和锁文件

+   使用 Oak 编写 Web 服务器

+   向应用程序添加用户

+   让我们开始吧！

## 技术要求

本章将在我们上一章开发的代码基础上进行构建。本章的所有代码文件都可以在这本书的 GitHub 仓库中找到，网址为[`github.com/PacktPublishing/Deno-Web-Development/tree/master/Chapter05/sections`](https://github.com/PacktPublishing/Deno-Web-Development/tree/master/Chapter05/sections)。

# 管理依赖项和锁文件

在第二章《*工具链*》中，我们学习了 Deno 如何让我们进行依赖管理。在本节中，我们将更实际地使用它。我们首先将代码中所有分散的 URL 导入移到一个集中的依赖文件中；之后，我们将创建一个锁文件，确保我们的尚处于初级阶段的应用程序在任何地方都能顺利运行。最后，我们将学习如何根据锁文件安装项目的依赖项。

## 使用一个集中的依赖文件

在上一章中，你可能会注意到我们直接在代码中使用 URL 作为依赖。尽管这可行，但这是我们几章前 discouraged 的做法。在我们第一个阶段，这对我们有效，但随着应用程序开始增长，我们必须适当地管理我们的依赖项。我们想要避免因依赖版本冲突、URL 中的拼写错误以及依赖项分散在各个文件中而产生的困扰。为了解决这个问题，我们必须做以下几点：

1.  在`src`文件夹的根目录下创建一个`deps.ts`文件。

    这个文件可以有任何你喜欢的名字。我们目前称之为`deps.ts`，因为这是 Deno 文档中提到的，也是许多模块使用的命名约定。

1.  将所有外部依赖从我们的代码移动到`deps.ts`。

    目前，我们唯一的依赖项就是标准库中的 HTTP 模块，可以在`src/web/index.ts`文件中找到。

1.  将导入移动到`deps.ts`文件中，并将`import`更改为`export`：

    ```js
    export { serve } from
      "https://deno.land/std@0.83.0/http/server.ts"
    ```

1.  注意固定版本位于 URL 上：

    ```js
    export { serve } from
      "https://deno.land/std@0.83.0/http/server.ts"
    ```

    正如我们在第二章《*工具链*》中学习的，这是 Deno 中的版本控制工作方式。

    我们现在需要更改依赖文件，使其直接从`deps.ts`文件导入，而不是直接从 URL 导入。

1.  在`src/web/index.ts`中，从`deps.ts`导入`serve`方法：

    ```js
    import { serve } from "../deps.ts";
    ```

通过拥有一个集中的依赖文件，我们还有了一种轻松确保我们所有依赖项都本地下载的方式，而无需运行任何代码。有了这个，我们现在有一个文件，在其中我们可以运行`deno cache`命令（在第二章《*工具链*》中提到）。

## 创建一个锁文件

集中了我们的依赖项之后，我们需要确保安装项目的任何人都能得到与我们相同的依赖项版本。这是我们确保代码以相同方式运行的唯一方法。我们将通过使用一个锁文件来实现这一点。我们在第二章《*工具链*》中学习了如何做到这一点；在这里，我们将将其应用到我们的应用程序中。

让我们使用`lock`和`lock-write`标志以及锁文件和集中依赖文件`deps.ts`的路径来运行`cache`命令：

```js
$ deno cache --lock=lock.json --lock-write src/deps.ts
```

在当前文件夹中应生成一个`lock.json`文件。如果您打开它，它应该包含 URL 的键值对，以及用于执行完整性检查的哈希。

这个锁文件应该随后添加到版本控制中。后来，如果一个同事想要安装这个相同的项目，他们只需要运行相同的命令，而不需要`--lock-write`标志：

```js
$ deno cache --lock=lock.json src/deps.ts
```

这样，`src/deps.ts`中的所有依赖项（这应该全部）将被安装，并根据`lock.json`文件进行完整性检查。

现在，每当我们在项目中安装一个新的依赖项时，我们必须运行`deno` `cache`命令，并带上`lock`和`lock-write`标志，以确保锁文件被更新。

这一节就差不多结束了！

在这一节中，我们学习了一个确保应用程序运行顺畅的简单但非常重要的步骤。这有助于我们避免未来的复杂问题，如依赖冲突和版本间行为不匹配。我们还保证了资源完整性，这对于 Deno 来说尤为重要，因为其依赖项是存储在 URL 中，而不是注册表中。

在下一节中，我们将从标准库 HTTP 服务器开始*重构*我们的应用程序，改为使用 Oak，这将导致我们的网络代码得到简化。

# 使用 Oak 编写 Web 服务器

在上一章的末尾，我们审视了不同的网络库。经过短暂的分析后，我们最终选择了 Oak。在本节中，我们将重写我们网络应用程序的一部分，以便我们可以使用它，而不是标准库的 HTTP 模块。

让我们打开`src/web/index.ts`并逐步开始处理它。

遵循 Oak 的文档([`deno.land/x/oak@v6.3.1`](https://deno.land/x/oak@v6.3.1))，我们唯一需要做的就是实例化`Application`对象，定义一个中间件，并调用`listen`方法。让我们这样做：

1.  在`deps.ts`文件中添加 Oak 的导入：

    ```js
    export { Application } from
      "https://deno.land/x/oak@v6.3.1/mod.ts"
    ```

    如果您正在使用 VSCode，那么您可能已经注意到有一个警告，它说它无法在本地找到这个版本的依赖。

1.  让我们运行上一节中的命令来下载它并添加到锁文件中。

    不要忘记每次添加依赖时这样做，以便我们有更好的自动完成功能，并且我们的锁文件始终是更新的：

    ```js
    $ deno cache --lock=lock.json --reload --lock-write src/deps.ts
    Download https://deno.land/std@0.83.0/http/server.ts
    Download https://deno.land/x/oak@v6.3.1/mod.ts
    Download https://deno.land/std@0.83.0/encoding/utf8.ts
    …
    ```

    在所有必要的依赖项下载完成后，让我们开始在我们的代码中使用它们。

1.  删除`src/web/index.ts`中`createServer`函数的所有代码。

1.  在`src/web/index.ts`文件中，导入`Application`类并实例化它。创建一个极其简单的中间件（如文档中所述）并调用`listen`方法：

    ```js
    import { Application } from "../deps.ts";
    …
    export async function createServer({
      configuration: {
        port
      },
      museum
    }: CreateServerDependencies) {
      const app = new Application ();
      app.use((ctx) => {
        ctx.response.body = "Hello World!";
      });
      await app.listen({ port });
    }
    ```

请记住，在删除旧代码的同时，我们也移除了`console.log`，因此它暂时不会打印任何内容。让我们运行它并验证是否有任何问题：

```js
$ deno run --allow-net src/index.ts  
```

现在，如果我们访问`http://localhost:8080`，我们将在那里看到“Hello World!”响应。

现在，你可能会想知道 Oak 应用程序的`use`方法是什么。嗯，我们将使用这个方法来定义中间件。现在，我们只是想它修改响应并在其主体中添加一条消息。在下一章，我们将深入学习中间件函数。

记得我们移除了`console.log`，如果应用程序正在运行，我们就得不到任何反馈吗？在了解如何向 Oak 应用程序添加事件监听器的同时，我们将学习如何做到这一点。

## 向 Oak 应用程序添加事件监听器

到目前为止，我们已经设法使应用程序运行，但目前，我们没有任何消息来确认这一点。我们将使用这个机会来了解 Oak 中的事件监听器。

Oak 应用程序分发两种不同类型的事件。其中一个是`listen`，而另一个是`the listen event`，我们将使用它来在应用程序运行时向控制台日志。另一个，`error`，是我们将在出现错误时向控制台日志使用的。

首先，让我们在`app.listen`语句之前添加`listen`事件的监听器：

```js
app.addEventListener("listen", e => {
  console.log(`Application running at 
    http://${e.hostname || 'localhost'}:${port}`)
})
…
await app.listen({ port });
```

请注意，我们不仅向控制台打印消息，还打印事件中的`hostname`并发送其默认值，以防它未定义。

为了安全和确保我们能捕获任何意外错误，我们还需要添加一个错误事件监听器。这个错误事件将在应用程序中发生未处理的错误时触发：

```js
app.addEventListener("error", e => {
  console.log('An error occurred', e.message);
})
```

这些处理程序，尤其是`error`处理程序，将帮助我们开发过程中快速反馈正在发生的情况。后来，当接近生产阶段时，我们将添加适当的日志中间件。

现在，你可能会认为我们仍然缺少我们在本章开始时拥有的功能，你是对的：我们从应用程序中移除了列出所有博物馆的端点。

让我们再次添加它，并学习如何在 Oak 应用程序中创建路由。

## 在 Oak 应用程序中处理路由

Oak 还提供了另一个对象，与`Application`类并列，允许我们定义路由——`Router`类。我们将使用这个来重新实现我们之前的路由，该路由列出了应用程序中的所有博物馆。

我们可以通过向构造函数发送前缀属性来创建它。这样做意味着那里定义的所有路由都将用这个路径进行前缀：

```js
import { Application, Router } from "../deps.ts";
…
const apiRouter = new Router ({ prefix: "/api" })
```

现在，让我们恢复我们的功能，通过`GET`请求`/api/museums`返回博物馆列表：

```js
apiRouter.get("/museums", async (ctx) => {
  ctx.response.body = {
    museums: await museum.getAll()
  }
});
```

这里发生了一些事情。

在这里，我们使用 Oak 的路由 API 定义路由，通过发送 URL 和一个处理函数。然后我们的处理程序会带有一个上下文（`ctx`）对象调用。所有这些都在 Oak 的文档中有详细解释([`doc.deno.land/https/deno.land/x/oak@v6.3.1/mod.ts#Router`](https://doc.deno.land/https/deno.land/x/oak@v6.3.1/mod.ts#Router)),但我留给你们一个简短的总结。

在 Oak 中，一个处理程序可以做的所有事情都是通过上下文对象完成的。发出的请求在`ctx.request`属性中可用，而当前请求的响应在`ctx.response`中可用。头部、cookies、参数、正文等都可以在这些对象中找到。一些属性，如`ctx.response.body`，是可写的。

提示

您可以通过查看 Deno 的文档网站来更好地了解 Oak 的功能：[`doc.deno.land/https/deno.land/x/oak@v6.3.1/mod.ts`](https://doc.deno.land/https/deno.land/x/oak@v6.3.0/mod.ts)。

在这种情况下，我们使用响应体属性来设置其内容。当 Oak 能够推断出响应的类型（这里是的 JSON），它自动为响应添加正确的`Content-Type`头。

我们将在本书中继续学习 Oak 及其功能。下一步是连接我们刚刚创建的路由器。

## 将路由器连接到应用程序

现在我们的路由器已经定义好了，我们需要在应用程序上注册它，这样它就可以开始处理请求了。

为此，我们将使用我们之前使用过的应用程序实例的方法——`use`方法。

在 Oak 中，一旦定义了一个`Router`（并注册了它），它提供了两个返回中间件函数的方法。这些函数可以用来在应用程序上注册路由。它们如下：

+   `routes`：在应用程序中注册已注册的路由处理程序。

+   `allowedMethods`：为在路由器中未定义的 API 方法的调用注册自动处理程序，返回一个`405 – Not allowed`响应。

我们将使用它们来在主应用程序中注册我们的路由，如下所示：

```js
const apiRouter = new Router({ prefix: "/api" })
apiRouter.get("/museums", async (ctx) => {
  ctx.response.body = {
    museums: await museum.getAll()
  }
});
app.use(apiRouter.routes());
app.use(apiRouter.allowedMethods());
app.use((ctx) => {
  ctx.response.body = "Hello World!";
});
```

这样一来，我们的路由器就在应用程序中注册了它的处理程序，它们准备好开始处理请求。

记住，我们必须在之前注册我们之前定义的 Hello World 中间件。如果我们不这样做，Hello World 处理程序会在它们到达我们的路由器之前响应所有请求，因此它将无法工作。

现在，我们可以通过运行以下命令来运行我们的应用程序：

```js
$ deno run --allow-net src/index.ts
Application running at http://localhost:8080
```

然后，我们可以执行一个`curl`到 URL：

```js
$ curl http://localhost:8080/api/museums
{"museums":[{"id":"1fbdd2a9-1b97-46e0-b450-62819e5772ff","name":"The Louvre","description":"The world's largest art museum and a historic monument in Paris, France.","location":{"lat":"48.860294","lng":"2.33862"}}]}
```

正如我们所看到的，一切都在按预期进行！我们成功地将我们的应用程序迁移到了 Oak。

通过这样做，我们大大提高了我们代码的可读性。我们还使用 Oak 处理我们不想处理的事情，并成功地专注于我们的应用程序。

在下一节中，我们将向应用程序中添加用户概念。将会创建更多的路由，以及一个全新的模块和一些业务逻辑来处理用户。

提示

本章的代码可以在[`github.com/PacktPublishing/Deno-Web-Development/tree/master/Chapter05/sections`](https://github.com/PacktPublishing/Deno-Web-Development/tree/master/Chapter05/sections)找到，按部分分开。

现在，让我们向应用程序中添加一些用户！

# 向应用程序添加用户

目前我们已经有了一个运行中的第一个端点，列出了应用程序中的所有博物馆，但我们仍然离最终要求相去甚远。

我们希望添加用户，以便可以注册、登录并在应用程序中与身份互动。

我们将首先创建一个定义用户的对象，然后进入业务逻辑以创建和存储它。之后，我们将创建允许我们通过 HTTP 与应用程序交互的端点，从而允许用户注册。

## 创建用户模块

目前我们可以说在应用程序中有一个单一的“模块”：`museums`模块。与博物馆有关的所有内容都在这里，从控制器到仓库、对象定义等。这个模块只有一个接口，即它的`index.ts`文件。

我们这样做是为了在保持模块外部 API 稳定的同时，在模块内部拥有工作自由，从而实现模块之间的良好解耦。为了确保模块内部的部分合理解耦，我们还必须通过构造函数注入它们的依赖项，允许我们轻松地交换部分并独立测试它们（如您将在第八章中看到的，*测试 - 单元和集成*）。

遵循这些指导原则，我们将继续使用这种“模块”系统，并按照以下步骤为我们的用户创建一个：

1.  在`src/users`目录下创建一个名为`index.ts`的文件。

1.  创建`src/users/types.ts`文件。这是我们定义`User`类型的地方：

    ```js
    export type User = {
      username: string,
      hash: string,
      salt: string,
      createdAt: Date
    } 
    ```

    我们的用户对象将非常简单：它将有一个`username`，一个`createdAt`日期，然后有两个属性：`hash`和`salt`。我们将使用这些来保护存储用户密码时的用户密码。

1.  在`src/users/controller.ts`中创建一个名为`register`的用户控制器方法。它应该接收一个用户名和一个密码，然后在数据库中创建一个用户：

    ```js
    type RegisterPayload = 
      { username: string, password: string };
    export class Controller {
      public async register(payload: RegisterPayload) {
        // Logic to register users
      }
    }
    ```

1.  在`src/users/types.ts`中定义`RegisterPayload`并将其从`src/users/controller.ts`中移除，在`src/users/index.ts`中导出它。

    在`src/users/types.ts`中添加以下内容：

    ```js
    // src/users/types
    export type RegisterPayload = 
      { username: string; password: string };
    ```

    在`src/users/index.ts`中添加以下内容：

    ```js
    export type {
      RegisterPayload,
    } from "./types.ts";
    ```

    让我们在这里停下来，思考一下寄存器逻辑。

    要创建用户，我们必须检查该用户是否存在于数据库中。如果不存在，我们将使用输入的用户名和密码创建他们，然后返回一个不包含敏感数据的对象。

    在上一章中，我们每次想要与数据源交互时都使用了仓库模式。仓库保留了所有*数据访问*逻辑（`src/museums/repository.ts`）。

    在这里，我们将做同样的事情。我们已经注意到，我们的控制器需要调用`UserRepository`中的两个方法：一个是为了检查用户是否存在，另一个是创建用户。这是我们接下来要定义的接口。

1.  前往`src/users/types.ts`并定义`UserRepository`接口：

    ```js
    export type CreateUser = 
      Pick<User, "username" | "hash" | "salt">;
    …
    export interface UserRepository {
      create: (user: CreateUser) => Promise<User>
      exists: (username: string) => Promise<boolean>
    }
    ```

    注意我们是如何创建一个`CreateUser`类型，它包含`User`的所有属性，除了`createdAt`，这应该由仓库添加。

    定义了`UserRepository`接口后，我们可以现在移动到用户的控制器中，并确保它在构造函数中接收接口的一个实例。

1.  在`src/users/controller.ts`中，创建一个`constructor`，它接收用户仓库作为注入参数，并用相同名称设置类属性：

    ```js
    userRepository, we can start writing the logic for the register method.
    ```

1.  编写`register`方法的逻辑，检查用户是否存在，如果不存在则创建他们：

    ```js
    async register(payload: RegisterPayload) {
    create method of userRepository to make sure it follows the CreateUser type we defined previously. These will have to be automatically generated, but don't worry about that for now.And with this, we've pretty much finished looking at what will happen whenever someone tries to register with our application. We're still missing one thing, though. As you may have noticed, we're returning the `User` object directly from the repository, which might contain sensitive information, namely the `hash` and `salt` properties.
    ```

1.  在`src/users/types.ts`中创建一个名为`UserDto`的类型，定义不包含敏感数据的`User`对象的格式：

    ```js
    export type User = {
      username: string,
      hash: string,
      salt: string,
      createdAt: Date
    }
    Pick to choose two properties from the User object; that is, createdAt and username.With `UserDto` ([`en.wikipedia.org/wiki/Data_transfer_object`](https://en.wikipedia.org/wiki/Data_transfer_object)) defined, we can now make sure our register is returning it. 
    ```

1.  创建一个名为`src/users/adapter.ts`的文件，里面有一个名为`userToUserDto`的函数，该函数将用户转换为`UserDto`：

    ```js
    import type { User, UserDto } from "./types.ts";
    export const userToUserDto = (user: User): UserDto => {
      return {
        username: user.username,
        createdAt: user.createdAt
      }
    }
    ```

1.  在注册方法中使用最近创建的函数，以确保我们返回一个`UserDto`：

    ```js
    import { userToUserDto } from "./adapter.ts";
    …
    public async register(payload: RegisterPayload) {
      …
      const createdUser = await
        this.userRepository.create(
        payload.username,
        payload.password
      );
      return userToUserDto(createdUser);
    }
    ```

有了这个，`register`方法就完成了！

我们目前将散列和盐值作为两个没有意义的明文字符串发送。

你可能会想知道为什么我们不直接发送密码。这是因为我们想确保我们不在任何数据库中以明文形式存储密码。

为了确保我们遵循最佳实践，我们将使用散列和盐值在数据库中存储用户的密码。同时，我们还想了解一些 Deno API。这就是我们将在下一节中做的事情。

## 在数据库中存储用户

尽管我们使用的是内存数据库，但我们决定我们不会以明文形式存储密码。相反，我们将使用一种常见的存储密码的方法，称为散列和盐值。如果你不熟悉它，auth0 有一篇非常好的文章，我强烈推荐你阅读（[`auth0.com/blog/adding-salt-to-hashing-a-better-way-to-store-passwords/`](https://auth0.com/blog/adding-salt-to-hashing-a-better-way-to-store-passwords/)）。

这个模式本身并不复杂，你只需要按照代码学习就能掌握它。

所以，我们将以散列的形式存储密码。我们不会存储用户输入的确切散列密码，而是存储密码加上一个随机生成的字符串，称为盐值。这个盐值将与密码一起存储，以便稍后使用。在此之后，我们永远不需要再次解码密码。

有了盐值，无论何时我们想要检查密码是否正确，我们只需将盐值添加到用户输入的任何一个密码中，进行散列，并验证输出是否与数据库中存储的内容匹配。

如果你觉得这仍然很奇怪，我敢保证当你查看代码时，它会变得简单得多。让我们按照这些步骤实现这些函数：

1.  创建一个名为`src/users/util.ts`的 utils 文件，里面有一个名为`hashWithSalt`的函数，该函数使用提供的盐值散列一个字符串：

    ```js
    import { createHash } from
      "https://deno.land/std@0.83.0/hash/mod.ts";
    export const hashWithSalt = 
      (password: string, salt: string) => {
        const hash = createHash("sha512")
          .update(`${password}${salt}`)
            .toString();
      return hash;
    };
    ```

    现在应该很清楚，这个函数将返回一个字符串，它是提供的字符串的`hash`值，加上一个`salt`。

    正如前面文章中提到的，使用不同盐值对不同密码也被认为是最佳实践。通过为每个密码生成不同的`salt`，即使一个密码的盐值被泄露，我们也能确保所有密码都是安全的。

    让我们通过创建一个生成`salt`的函数来继续进行：

1.  使用`crypto` API（[`doc.deno.land/builtin/stable#crypto`](https://doc.deno.land/builtin/stable#crypto)）创建一个`generateSalt`函数，以获取随机值并从那里生成一个 salt 字符串：

    ```js
    import { encodeToString } from
      "https://deno.land/std@0.83.0/encoding/hex.ts"
    …
    export const generateSalt = () => {
      const arr = new Uint8Array(64);
      crypto.getRandomValues(arr)
      return encodeToString(arr);
    }
    ```

    这就是我们为应用程序生成散列密码所需的所有内容。

    现在，我们可以开始在我们控制器中使用刚刚创建的实用函数。让我们创建一个方法，以便在那里散列我们的密码。

1.  在`UserController`中创建一个私有方法，名为`getHashedUser`，它接收一个用户名和密码，并返回一个用户，以及他们的散列值和盐值：

    ```js
    import { generateSalt, hashWithSalt } from
      "./util.ts";
    …
    export class Controller implements UserController {
    … 
      private async getHashedUser
        (username: string, password: string) {
        const salt = generateSalt();
        const user = {
          username,
          hash: hashWithSalt(password, salt),
          salt
        }
        return user;
      }
    …
    ```

1.  在`register`方法中使用刚刚创建的`getHashedUser`方法：

    ```js
    public async register(payload: RegisterPayload) {
      if (await
        this.userRepository.exists(payload.username)) {
        return Promise.reject("Username already exists");
      }
      const createdUser = await
        this.userRepository.create(
        await this.getHashedUser
          (payload.username, payload.password)
      );
      return userToDto(createdUser);
    }
    ```

大功告成！这样一来，我们就确保我们不会存储任何明文密码。在路径中，我们了解了 Deno 中可用的`crypto` API。

我们在实现所有这些功能时都在使用之前定义的`UserRepository`接口。然而，目前我们还没有一个实现该接口的类，所以让我们创建一个。

## 创建用户仓库

在前一节中，我们创建了定义`UserRepository`接口，所以接下来，我们要创建一个实现它的类。让我们开始吧：

1.  创建一个名为`src/users/repository.ts`的文件，其中有一个导出的`Repository`类：

    ```js
    import type { CreateUser, User, UserRepository } from
      "./types.ts";
    export class Repository implements UserRepository {
      async create(user: CreateUser) {
      }
      async exists(username: string) {
      }
    }
    ```

    接口保证这两个公共方法必须存在。

    现在，我们需要一种将用户存储在数据库中的方法。为了本章的目的，我们将继续使用内存数据库，这与我们对博物馆所做的工作非常相似。

1.  在`src/users/repository.ts`类中创建一个名为`storage`的属性。它应该是一个 JavaScript Map，将作为用户数据库使用：

    ```js
    import { User, UserRepository } from "./types.ts";
    export class Repository implements UserRepository {
      private storage = new Map<User["username"], User>();
    …
    ```

    有了数据库，我们现在可以实现这两个方法的逻辑。

1.  在`exists`方法中从数据库获取用户，如果存在则返回`true`，如果不存在则返回`false`：

    ```js
    async exists(username: string) {
      return Boolean(this.storage.get(username));
    }
    ```

    `Map#get`函数如果在找不到记录时返回 undefined，所以我们将其转换为 Boolean，以确保它总是返回 true 或 false。

    `exists`方法非常简单；它只需要检查用户是否存在于数据库中，然后相应地返回一个`boolean`。

    要创建用户，我们需要执行比那多一个或两个步骤。不仅仅是创建，我们还将确保它还向调用此函数的任何人发送了一个带有`createdAt`日期的用户。

    现在，让我们回到主要任务：在数据库中创建用户。

1.  打开`src/users/repository.ts`文件，实现`create`方法，以正确的格式创建一个`user`对象。

    记得在发送给函数的`user`对象中添加`createdDate`：

    ```js
    async create(user: CreateUser) {
      const userWithCreatedAt = 
        { ...user, createdAt: new Date() }
      this.storage.set
       (user.username, { ...userWithCreatedAt });
      return userWithCreatedAt;
    } 
    ```

    这样一来，我们的仓库就完整了！

    它完全实现了我们之前在`UserRepository`接口中定义的内容，并准备使用。

    下一步是把这些碎片连接在一起。我们已经创建了`User`控制器 和 `User`仓库，但它们目前还没有在任何地方使用。

    在我们继续之前，我们需要将从用户模块中暴露出这些对象到外部世界。我们将遵循我们之前定义的规则；也就是说，模块的接口将总是位于其根目录下的`index.ts`文件。

1.  打开`src/users/index.ts`，并从模块中导出`Controller`、`Repository`类及其相应的类型：

    ```js
    export { Repository } from './repository.ts';
    export { Controller } from './controller.ts';

    export type {
      CreateUser,
      RegisterPayload,
      User,
      UserController,
      UserRepository,
    } from "./types.ts"; 
    ```

    我们现在可以确保用户模块中的每个文件都是直接从这个文件（`src/users/index.ts`）导入类型，而不是直接导入其他文件。

现在，任何想要从用户模块导入内容的模块都必须通过`index.ts`文件进行。现在，我们可以开始考虑用户将如何与刚刚编写的业务逻辑互动。由于我们正在构建一个 API，下一节我们将学习如何通过 HTTP 暴露它。

## 创建注册端点

有了业务逻辑和数据访问逻辑，唯一缺少的是用户可以调用以注册自己的端点。

对于注册请求，我们将实现一个`POST /api/users/register`，预期一个包含名为`user`的属性，其中包含`username`和`password`两个属性的 JSON 对象。

我们首先必须做的是声明`src/web/index.ts`中的`createServer`函数将依赖于`UserController`接口的注入。让我们开始：

1.  在`src/users/types.ts`中创建`UserController`接口。确保它也导出在`src/users/index.ts`中：

    ```js
    RegisterPayload from src/users/controller.ts previously.
    ```

1.  现在，为了保持整洁，去`src/users/controller.ts`并确保类实现了`UserController`：

    ```js
    import { RegisterPayload, UserController,
      UserRepository } from "./types.ts";
    export class Controller implements UserController
    ```

1.  回到`src/web/index.ts`，将`UserController`添加到`createServer`依赖项中：

    ```js
    import { UserController } from "../users/index.ts";
    interface CreateServerDependencies {
      configuration: {
        port: number
      },
      museum: MuseumController,
      user: UserController
    }
    export async function createServer({
      configuration: {
        port
      },
      museum,
      user
    }: CreateServerDependencies) {
    …
    ```

    现在，我们准备创建我们的注册处理程序。

1.  创建一个处理`POST`请求的处理器，在`/api/users/register`下创建用户，使用注入的控制器的`register`方法：

    ```js
    apiRouter.post method to define a route that accepts a POST request. Then, we're using the body method from the request (https://doc.deno.land/https/deno.land/x/oak@v6.3.1/mod.ts#ServerRequest) to get its output in JSON. We then do a simple validation to check if the username and password are present in the request body, and at the bottom, we use the injected register method from the controller. We're wrapping it in a try catch so that we can return HTTP status code 400 if an error happens.
    ```

这应该足以使 web 层能够完美地回答我们的请求。现在，我们只需要把一切连接在一起。

## 用户控制器与 web 层的连接

我们已经创建了应用程序的基本部分。有业务逻辑，有数据访问逻辑，还有 web 服务器来处理请求。唯一缺少的是将它们连接在一起的东西。在本节中，我们将实例化我们定义的接口的实际实现，并将它们注入到期望它们的内容中。

返回`src/index.ts`。让我们对`museums`模块做类似的事情。在这里，我们将导入用户仓库和控制器，实例化它们，并将控制器发送到`createServer`函数。

按照以下步骤进行操作：

1.  在`src/index.ts`中，从用户模块导入`Controller`和`Repository`，并在实例化它们的同时发送必要的依赖项：

    ```js
    import {
      Controller as UserController,
      Repository as UserRepository,
       } from './users/index.ts';
    …
    const userRepository = new UserRepository();
    const userController = new UserController({
      userRepository });
    ```

1.  将用户控制器发送到`createServer`函数：

    ```js
    createServer({
      configuration: { port: 8080 },
      museum: museumController,
      user: userController
    })
    ```

就这样，我们完成了！为了完成这一切，让我们运行以下命令来运行我们的应用程序：

```js
$ deno run --allow-net src/index.ts
Application running at http://localhost:8080
```

现在，让我们用`curl`向`/api/users/register`发送请求，来测试注册的端点：

```js
$ curl -X POST -d '{"username": "alexandrempsantos", "password": "testpw" }' -H 'Content-Type: application/json' http://localhost:8080/api/users/register
{"user":{"username":"alexandrempsantos","createdAt":"2020-10-06T21:56:54.718Z"}}
```

正如我们所看到的，它正在运行并返回`UserDto`的内容。我们本章的主要目标已经实现：我们创建了用户模块并向其中添加了一个注册用户的端点！

# 总结

在本章中，我们的应用程序经历了巨大的变化！

我们首先将应用程序从标准库 HTTP 模块迁移到 Oak。我们不仅迁移了服务应用程序的逻辑，还开始使用 Oak 的路由器定义一些路由。我们注意到，随着 Oak 封装了之前手动完成的某些工作，应用程序逻辑开始变得简单。我们成功地将标准库中的所有 HTTP 代码迁移过来，而没有改变业务逻辑，这是一个非常好的迹象，表明我们在应用程序架构方面做得很好。

我们一直在前进，并学习了如何在 Oak 应用程序中监听和处理事件。随着我们编写更多的代码，我们也对 Oak 变得更加熟悉，理解其功能，探索其文档，并对其进行实验。

用户是任何应用程序的重要组成部分，因此在本章中，我们花了很多时间来关注用户。我们不仅在应用程序中添加了用户，还将用户作为一个独立的、自包含的模块添加进来，与博物馆并列。

一旦我们在应用程序中开发了注册用户的业务逻辑，为它提供一个持久化层就变得迫切需要了。这意味着我们必须开发一个用户仓库，负责在数据库中创建用户。在这里，我们深入实现了一个哈希和盐机制，以安全地在数据库中存储用户的密码，并在过程中学习了几个 Deno API。

用户业务逻辑完成后，我们转向了缺失的部分：HTTP 端点。我们在 HTTP 路由器中添加了注册路线，并在 Oak 的帮助下设置了一切。

为了结束这一切，我们再次使用依赖注入将所有内容连接起来。由于我们所有模块的依赖都是基于接口的，我们很容易注入所需的依赖，并使我们的代码工作。

本章是我们使应用程序更具可扩展性和可读性的旅程。我们首先移除了自制的路由器代码并将其移到 Oak 中，最后我们添加了一个重要的大型业务实体——用户。后者也作为我们架构的测试，并展示了它如何适应不同的业务领域。

在下一章中，我们将通过添加一些有趣的功能来不断迭代应用程序。这样做，我们将完成在这里创建的功能，例如用户登录、授权以及在真实数据库中的持久化。我们还将处理包括基本日志记录和错误处理在内的常见 API 实践。

兴奋吗？我们也是——开始吧！
