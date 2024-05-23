# 第八章：测试 - 单元和集成

代码在相应测试编写之前是不会创建的。既然您在读这一章，我将假设我们可以同意这个观点。然而，您可能想知道，为什么我们一个测试都没有写呢？合情合理。

我们选择不这样做，因为我们相信这会使得内容更难吸收。由于我们想在构建应用程序的同时让您专注于学习 Deno，所以我们决定不这样做。第二个原因是，我们真的想要一个完整的章节专注于测试，即这一章。

测试是软件生命周期中非常重要的一部分。它可以用来节省时间，明确需求，或者只是因为你想在以后重新编写和重构时感到自信。无论动机如何，有一点是确定的：您将编写测试。我也坚信测试在软件设计中扮演着重要角色。易于测试的代码很可能易于维护。

由于我们非常重视测试的重要性，所以在不学习测试的情况下，我们无法认为这是一本关于 Deno 的完整指南。

在本章中，我们将编写不同类型的测试。我们将从单元测试开始，这对于开发者和维护周期来说是非常有价值的测试。然后，我们将进行集成测试，在其中我们运行应用程序并对其执行几个请求。最后，我们将使用在前一章中编写的客户端。我们将在这个过程中，逐步向应用程序中添加测试，确保我们之前编写的代码正常工作。

本章还将展示我们在这本书一开始所做的某些架构决策将得到回报。这将是使用 Deno 及其工具链编写简单模拟和清晰、专注测试的介绍。

在本章中，我们将涵盖以下主题：

+   在 Deno 中编写您的第一个测试

+   编写集成测试

+   测试网络服务器

+   为应用程序创建集成测试

+   同时测试 API 和客户端

+   对应用程序的部分进行基准测试

让我们开始吧！

## 技术要求

本章中将使用的代码可以在 [`github.com/PacktPublishing/Deno-Web-Development/tree/master/Chapter08/sections`](https://github.com/PacktPublishing/Deno-Web-Development/tree/master/Chapter08/sections) 找到。

# 在 Deno 中编写您的第一个测试

在我们开始编写测试之前，记住一些事情是很重要的。其中最重要的原因是，我们为什么要进行测试？

这个问题可能会有多个答案，但大多数都会指向保证代码在运行。您也可能说，您使用它们以便在重构时具有灵活性，或者您重视在实施时拥有短暂的反馈周期——我们可以同意这两点。由于我们在实现这些功能之前没有编写测试，所以后者对我们来说并不适用。

我们将把这些目标牢记在整个章节中。在本节中，我们将编写我们的第一个测试。我们将使用在前几章中编写的应用程序并向其添加测试。我们将编写两种类型的测试：集成测试和单元测试。

集成测试将测试应用程序不同组件之间的交互。单元测试测试隔离的层次。如果我们把它看作是一个光谱，那么单元测试更接近代码，而集成测试更接近用户。在用户端的尽头，还有端到端测试。这些测试通过模拟用户行为来测试应用程序，我们将在本章不涉及这些内容。

在实际应用程序开发中使用的模式，如依赖注入和控制反转，在测试时非常有用。由于我们通过注入所有其依赖项来开发代码，现在，在测试中只需模拟这些依赖项即可。记住：易于测试的代码通常也易于维护。

我们首先要做的是为业务逻辑编写测试。目前，由于我们的 API 相当简单，所以它没有太多的业务逻辑。大部分都生活在`UserController`中，因为`MuseumController`非常简单。我们从后者开始。

要在 Deno 中编写测试，我们需要使用以下内容：

+   Deno 测试运行器（在第二章，《工具链》中介绍）

+   Deno 命名空间中的`test`方法（[`doc.deno.land/builtin/stable#Deno.test`](https://doc.deno.land/builtin/stable#Deno.test)）

+   Deno 标准库中的断言方法（`doc.deno.land/https/deno.land/std@0.83.0/testing/asserts.ts`）

这些都是 Deno 的组成部分，由核心团队分发和维护。社区中还有许多其他可以在测试中使用的库。我们将使用 Deno 提供的默认设置，因为它工作得很好，并且允许我们编写清晰易读的测试。

让我们来学习我们如何定义一个测试！

## 定义测试

Deno 提供了一个 API 来定义测试。这个 API，`Deno.test`（[`doc.deno.land/builtin/stable#Deno.test`](https://doc.deno.land/builtin/stable#Deno.test)），提供了两种不同的方法来定义一个测试。

其中一个是我们在第二章，《工具链》中展示的，由两部分组成，即调用它需要两个参数；也就是说，测试名称和测试函数。这可以在以下示例中看到：

```js
Deno.test("my first test", () => {})
```

我们还可以通过调用相同的 API 来实现，这次将对象作为参数发送。 你可以发送函数和测试名称，还可以发送其他一些选项到这个对象，如你所见在以下示例中：

```js
Deno.test({
  name: "my-second-test",
  fn: () => {},
  only: false,
  sanitizeOps: true,
  sanitizeResources: true,
});
```

这些标志行为在文档中解释得非常清楚([`doc.deno.land/builtin/stable#Deno.test`](https://doc.deno.land/builtin/stable#Deno.test)),但这里有一个总结供您参考：

+   `only`：只运行设置为`true`的测试，使测试套件失败，因此这应仅作为临时措施使用。

+   `sanitizeOps`：如果 Deno 核心启动的所有操作都不成功，则使测试失败。此标志默认为`true`。

+   `sanitizeResources`：如果测试完成后仍有资源运行（这可能表明内存泄漏），则使测试失败。这个标志确保测试必须有一个清理阶段，在此阶段停止资源，默认值为`true`。

现在我们已经了解了 API，让我们去编写我们的第一个测试——针对`MuseumController`函数的单元测试。

## 针对`MuseumController`的单元测试

在本节中，我们将编写一个非常简单的测试，它将涵盖我们在`MuseumController`中编写的所有功能，不多也不少。

它列出了应用程序中的所有博物馆，尽管目前它还没有做太多工作，只是作为`MuseumRepository`的代理运行。我们可以通过以下步骤创建这个简单功能的测试文件和逻辑：

1.  创建`src/museums/controller.test.ts`文件。

    测试运行程序将自动将文件名中包含`.test`的文件视为测试文件，如第二章 *工具链*中所解释的其他约定。

1.  使用`Deno.test`([`doc.deno.land/builtin/stable#Deno.test`](https://doc.deno.land/builtin/stable#Deno.test))声明第一个测试：

    ```js
    Deno.test("it lists all the museums", async () => {});
    ```

1.  现在，将标准库中的断言方法导出到一个名为`t`的命名空间中，这样我们就可以在测试文件中使用它们，通过在`src/deps.ts`中添加以下内容：

    ```js
    export * as t from
      "https://deno.land/std@0.83.0/testing/asserts.ts";
    ```

    如果您想了解标准库中可用的断言方法，请查看`doc.deno.land/https/deno.land/std@0.83.0/testing/asserts.ts`。

1.  您现在可以使用标准库中的断言方法来编写一个实例化`MuseumController`并调用`getAll`方法的测试：

    ```js
    import { t } from "../deps.ts";
    import { Controller } from "./controller.ts";
    Deno.test("it lists all the museums", async () => {
      const controller = new Controller({
    MuseumController and sending in a mocked version of museumRepository, which returns a static array. This is how we're sure we're testing only the logic inside MuseumController, and nothing more. Closer to the end of the snippet, we're making sure the getAll method's result is returning the museum being returned by the mocked repository. We are doing this by using the assertion methods we exported from the dependencies file.
    ```

1.  让我们运行测试并验证它是否正常工作：

    ```js
    $ deno test --unstable --allow-plugin --allow-env --allow-read –-allow-write --allow-net src/museums
    running 1 tests
    test it lists all the museums ... ok (1ms)
    test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out (1ms)
    ```

我们的第一个测试成功了！

注意测试输出如何列出测试的名称、状态和运行时间，以及测试运行的摘要。

`MuseumController`内部的逻辑相当简单，因此这个测试也非常简单。然而，它隔离了控制器的行为，使我们能够编写非常专注的测试。如果您对为应用程序的其他部分创建单元测试感兴趣，它们可以在本书的存储库中找到([`github.com/PacktPublishing/Deno-Web-Development/tree/master/Chapter08/sections/7-final-tested-version/museums-api`](https://github.com/PacktPublishing/Deno-Web-Development/tree/master/Chapter08/sections/7-final-tested-version/museums-api)).

在接下来的几节中，我们将编写更有趣的测试。这些测试将教会我们如何检查应用程序不同模块之间的集成。

# 编写集成测试

我们之前创建的第一个单元测试依赖于存储库的模拟实例来确保我们的控制器正常工作。这个测试在检测`MuseumController`中的错误方面增加了很大价值，但它在理解控制器是否与存储库良好配合方面并不值太多。

集成测试的目的是测试多个组件如何相互集成。

在本节中，我们将编写一个集成测试，用于测试`MuseumController`和`MuseumRepository`。这些测试将 closely mimic 应用程序运行时发生的事情，并帮助我们 later in terms of detecting any problems between these two classes.

让我们开始吧：

1.  在`src/museums`中为此模块的集成测试创建一个文件，称为`museums.test.ts`，并在那里添加第一个测试用例。

    它应该测试是否可以使用存储库的实例而不是模拟实例获取所有博物馆：

    ```js
    Deno.test("it is able to get all the museums from
      storage", async () => {});
    ```

1.  我们将从实例化存储库并在此添加几个测试用例开始：

    ```js
    import { t } from "../deps.ts";
    import { Controller, Repository } from "./index.ts";
    Deno.test("it is able to get all the museums from
      storage", async () => {
      const repository = new Repository();
      repository.storage.set("0", {
        description: "museum with id 0",
        name: "my-museum",
        id: "0",
        location: { lat: "123", lng: "321" },
      });
      repository.storage.set("1", {
        description: "museum with id 1",
        name: "my-museum",
        id: "1",
        location: { lat: "123", lng: "321" },
      });
    …
    ```

1.  现在我们有了存储库，我们可以用它来实例化控制器：

    ```js
    const controller = new Controller({ museumRepository:
      repository });
    ```

1.  现在我们可以编写我们的断言来确保一切都在正常工作：

    ```js
    const allMuseums = await controller.getAll();
    t.assertEquals(allMuseums.length, 2);
    t.assertEquals(allMuseums[0].name, "my-museum", "has
      name");
    t.assertEquals(
      allMuseums[0].description,
      "museum with id 0",
      "has description",
    );
    t.assertEquals(allMuseums[0].id, "0", "has id");
    t.assertEquals(allMuseums[0].location.lat, "123", "has
      latitude");
    t.assertEquals(allMuseums[0].location.lng, "321", assertEquals, allowing us to get a proper message when this assertion fails. This is something that all assertion methods support.
    ```

1.  让我们运行测试并检查结果：

    ```js
    $ deno test --unstable --allow-plugin --allow-env --allow-read –-allow-write --allow-net src/museums
    running 2 tests
    test it lists all the museums ... ok (1ms)
    test it is able to get all the museums from storage ... ok (1ms)
    test result: ok. 2 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out (2ms)
    ```

它通过了！这就是我们需要的存储库和控制器集成测试！这个测试在我们要更改`MuseumController`或`MuseumRepository`中的代码时非常有用，因为它确保它们一起工作得很好。

同样，如果您对应用程序其他部分的集成测试如何工作感到好奇，我们在这本书的仓库中提供了它们 ([`github.com/PacktPublishing/Deno-Web-Development/tree/master/Chapter08/sections/7-final-tested-version/museums-api`](https://github.com/PacktPublishing/Deno-Web-Development/tree/master/Chapter08/sections/7-final-tested-version/museums-api)).

在第一部分，我们创建了一个单元测试，在这里，我们创建了一个集成测试，但我们还没有为应用程序的界面 - 网络部分编写任何测试，该部分使用 HTTP。这就是我们下一部分要做的。我们将学习如何可以独立地测试网络层中的逻辑，不使用任何其他模块。

# 测试网络服务器

到目前为止，我们已经学习了如何测试应用程序的不同部分。我们始于业务逻辑，它测试如何与与持久性交互的模块（存储库）集成，但网络层仍然没有测试。

确实，那些测试非常重要，但我们一致认为如果网络层失败，用户将无法访问到任何逻辑。

这就是我们这一节要做的。我们将启动我们的网络服务器，模拟它的依赖关系，并向它发送几个请求，以确保网络*单元*正在工作。

让我们先通过以下步骤创建网络模块的单元测试：

1.  前往`src/web`并创建一个名为`web.test.ts`的文件。

1.  现在，为了测试网络服务器，我们需要回到`src/web/index.ts`中的`createServer`函数，并导出它创建的`Application`对象：

    ```js
    const app = new Application();
    …
    return { app };
    ```

1.  我们还希望能够在任何时候停止应用程序。我们还没有实现这个功能。

    如果我们查看 oak 的文档，我们会看到它非常完善([`github.com/oakserver/oak#closing-the-server`](https://github.com/oakserver/oak#closing-the-server))。

    为了取消由`listen`方法启动的应用程序，我们还需要返回`AbortController`。所以，让我们在`createServer`函数的最后这样做。

    如果你不知道`AbortController`是什么，我留下一个来自 Mozilla 开发者网络的链接([`developer.mozilla.org/en-US/docs/Web/API/AbortController`](https://developer.mozilla.org/en-US/docs/Web/API/AbortController)),它解释得非常清楚。简而言之，它允许我们取消一个进行中的承诺：

    ```js
    const app = new Application();
    …
    const controller = new AbortController();
    const { signal } = controller;
    …
    return { app, controller };
    ```

    注意我们是如何实例化`AbortController`的，这与文档中的示例类似，并在最后返回它，还有`app`变量。

1.  回到我们的测试中，让我们创建一个测试，检查服务器是否对`hello world`做出响应：

    ```js
    Deno.test("it responds to hello world", async () => {})
    ```

1.  让我们使用我们之前创建的函数来获取服务器的实例运行；也就是说，`createServer`。记住，要调用这个函数，我们必须发送它的依赖关系。在这里，我们将不得不模拟它们：

    ```js
    import { Controller as UserController } from
      "../users/index.ts";
    import { Controller as MuseumController } from
      "../museums/index.ts";
    import { createServer } from "./index.ts";
    …
    const server = await createServer({
      configuration: {
        allowedOrigins: [],
        authorization: {
          algorithm: "HS256",
          key: "abcd",
        },
        certFile: "",
        keyFile: "",
        port: 9001,
        secure: false,
      },
    9001 and with HTTPS disabled, along with some random algorithm and key.Note how we're using TypeScript's `as` keyword to pass mocked types into the `createServer` function without TypeScript warning us about the type.
    ```

1.  我们现在可以创建一个测试，通过响应 hello world 请求来检查网络服务器是否正常工作：

    ```js
    import { t } from "../deps.ts";
    …
    const response = await fetch(
      "http://localhost:9001/",
      {
        method: "GET",
      },
    ).then((r) => r.text());
    t.assertEquals(
      response,
      "Hello World!",
      "responds with hello world",
    );
    ```

1.  我们需要做的最后一件事是在测试运行后关闭服务器。Deno 默认会让测试失败如果我们不这样做（因为`sanitizeResources`默认是`true`），这可能会导致内存泄漏：

    ```js
      server.controller.abort();
    ```

这完成了我们针对网络层的第一个测试！这是一个单元测试，它测试了启动服务器的逻辑，并确保了 Hello World 正在工作。接下来，我们将为端点创建更完整的测试，包括业务逻辑。

在下一节中，我们将开始为登录和注册功能编写集成测试。这些测试比我们为博物馆模块编写的测试要复杂一些，因为它们将测试整个应用程序，包括其业务逻辑、持久性和网络逻辑。

# 为应用程序创建集成测试

我们迄今为止编写的三个测试单元测试了一个单一模块，以及两个不同模块之间的集成测试。然而，为了确信我们的代码正在工作，如果我们能够测试整个应用程序那就太好了。那就是我们接下来要做的。我们将为应用程序设置一个测试配置，并对它运行一些测试。

我们将首先调用与初始化 Web 服务器相同的函数，然后创建其所有依赖项（控制器、存储库等）的实例。我们将确保我们使用内存持久性等事物来做到这一点。这将确保我们的测试是可复制的，并且不需要复杂的清理阶段或真实数据库的连接，因为这会减慢测试速度。

我们首先创建一个测试文件，暂时包括应用程序的集成测试。随着应用程序的发展，可能在每个模块中创建一个测试文件夹是有意义的，但现在，这个解决方案完全可行。

我们将以非常接近实际生产中运行的设置实例化应用程序，并对它进行一些请求和断言：

1.  创建一个与`src/index.ts`文件并列的`src/index.test.ts`文件。在其中创建一个测试声明，测试用户是否可以登录：

    ```js
    Deno.test("it returns user and token when user logs
      in", async () => {})
    ```

1.  在开始编写这个测试之前，我们将创建一个助手函数，该函数将为测试设置 Web 服务器。它将包含所有实例化控制器和存储库的逻辑，以及将配置发送到应用程序的逻辑。它看起来像这样：

    ```js
    import { CreateServerDependencies } from
      "./web/index.ts";
    …
    function createTestServer(options?: CreateServerDependencies) {
      const museumRepository = new MuseumRepository();
      const museumController = new MuseumController({
        museumRepository });
      const authConfiguration = {
        algorithm: "HS256" as Algorithm,
        key: "abcd",
        tokenExpirationInSeconds: 120,
      };
      const userRepository = new UserRepository();
      const userController = new UserController(
        {
          userRepository,
          authRepository: new AuthRepository({
            configuration: authConfiguration,
          }),
        },
      );
      return createServer({
        configuration: {
          allowedOrigins: [],
          authorization: {
            algorithm: "HS256",
            key: "abcd",
          },
          certFile: "abcd",
          keyFile: "abcd",
          port: 9001,
          secure: false,
        },
        museum: museumController,
        user: userController,
        ...options,
      });
    }
    ```

    我们在这里做的工作与`src/index.ts`中的布线逻辑非常相似。唯一的区别是我们将显式导入内存存储库，而不是 MongoDB 存储库，如下面的代码块所示：

    ```js
    import {
      Controller as MuseumController,
      InMemoryRepository as MuseumRepository,
    } from "./museums/index.ts";
    import {
      Controller as UserController,
      InMemoryRepository as UserRepository,
    } from "./users/index.ts";
    ```

    为了让我们能够访问`Museums`和`Users`模块的内存存储库，我们需要进入这些模块并将它们导出。

    `src/users/index.ts`文件应该看起来像这样：

    ```js
    export { Repository } from "./repository/mongoDb.ts";
    Repository but also exporting InMemoryRepository at the same time.Now that we have a way to create a test server instance, we can go back to writing our tests.
    ```

1.  使用我们刚刚创建的助手函数`createTestServer`创建一个服务器实例，并使用`fetch`对 API 发起注册请求：

    ```js
    Deno.test("it returns user and token when user logs
      in", async () => {
      const jsonHeaders = new Headers();
      jsonHeaders.set("content-type", "application/json");
      const server = await createTestServer();
      // Registering a user
      const { user: registeredUser } = await fetch(
        "http://localhost:9001/api/users/register",
        {
          method: "POST",
          headers: jsonHeaders,
          body: JSON.stringify({
            username: "asantos00",
            password: "abcd",
          }),
        },
      ).then((r) => r.json())
    …
    ```

1.  由于我们可以访问注册用户，因此我们可以尝试使用同一个用户登录：

    ```js
      // Login in with the createdUser
      const response = await
        fetch("http://localhost:9001/api/login", {
          method: "POST",
          headers: jsonHeaders,
          body: JSON.stringify({
          username: registeredUser.username,
          password: "abcd",
        }),
      }).then((r) => r.json())
    ```

1.  现在我们准备编写几个断言来检查我们的登录响应是否如我们所期望的那样：

    ```js
      t.assertEquals(response.user.username, "asantos00",
        "returns username");
      t.assert(!!response.user.createdAt, "has createdAt
        date");
      t.assert(!!response.token, "has token");
    ```

1.  最后，我们需要在我们的服务器上调用`abort`函数：

    ```js
    server.controller.abort();
    ```

这是我们第一次进行应用集成测试！我们让应用运行起来，对其进行了注册和登录请求，并断言一切按预期工作。在这里，我们逐步构建了测试，但如果您想查看完整的测试，它可以在本书的 GitHub 仓库中找到([`github.com/PacktPublishing/Deno-Web-Development/blob/master/Chapter08/sections/7-final-tested-version/museums-api/src/index.test.ts`](https://github.com/PacktPublishing/Deno-Web-Development/blob/master/Chapter08/sections/7-final-tested-version/museums-api/src/index.test.ts)).

为了结束这个话题，我们将编写另一个测试。还记得在上一个章节中，我们创建了一些授权逻辑，只允许已登录的用户访问博物馆列表吗？让我们用另一个测试来检查这是否有效：

1.  在`src/index.test.ts`中创建另一个测试，以测试用户是否可以使用有效的令牌访问博物馆列表：

    ```js
    Deno.test("it should let users with a valid token
      access the museums list", async () => {})
    ```

1.  由于我们想要再次登录和注册，我们将这些功能提取到一个我们可以在其多个测试中使用的工具函数中：

    ```js
    function register(username: string, password: string) {
      const jsonHeaders = new Headers();
      jsonHeaders.set("content-type", "application/json");
      return
       fetch("http://localhost:9001/api/users/register", {
         method: "POST",
         headers: jsonHeaders,
         body: JSON.stringify({
          username,
          password,
        }),
      }).then((r) => r.json());
    }
    function login(username: string, password: string) {
      const jsonHeaders = new Headers();
      jsonHeaders.set("content-type", "application/json");
      return fetch("http://localhost:9001/api/login", {
        method: "POST",
        headers: jsonHeaders,
        body: JSON.stringify({
          username,
          password,
        }),
      }).then((r) => r.json());
    }
    ```

1.  有了这些功能，我们现在可以重构之前的测试，使其看起来更干净一些，以下代码段展示了这一点：

    ```js
    Deno.test("it returns user and token when user logs
      in", async () => {
      const jsonHeaders = new Headers();
      jsonHeaders.set("content-type", "application/json");
      const server = await createTestServer();
      // Registering a user
      await register("test-user", "test-password");
      const response = await login("test-user", "test-
      password");
      // Login with the created user
      t.assertEquals(response.user.username, "test-user",
        "returns username");
      t.assert(!!response.user.createdAt, "has createdAt
        date");
      t.assert(!!response.token, "has token");
      server.controller.abort();
    });
    ```

1.  让我们回到我们正在编写的测试——检查认证用户是否可以访问博物馆的测试，并使用`register`和`login`函数来注册和认证一个用户：

    ```js
    Deno.test("it should let users with a valid token
      access the museums list", async () => {
      const jsonHeaders = new Headers();
      jsonHeaders.set("content-type", "application/json");
      const server = await createTestServer();
      // Registering a user
      await register("test-user", "test-password");
      const { token } = await login("test-user", "test-
        password");
    ```

1.  现在，我们可以使用从`login`函数返回的令牌，在`Authorization`头中进行认证请求：

    ```js
      const authenticatedHeaders = new Headers();
      authenticatedHeaders.set("content-type",
        "application/json");
      login function and sending it with the Authorization header in the request to the museums route. Then, we're checking if the API responds correctly to the request with the 200 OK status code. In this case, since our application doesn't have any museums, it is returning an empty array, which we're also asserting.Since we're testing this authorization feature, we can also test that a user with no token or an invalid token can't access this same route. Let's do it.
    ```

1.  创建一个测试，检查用户是否可以在没有有效令牌的情况下访问`museums`路由来。它应该与之前的测试非常相似，唯一的不同是我们现在发送一个无效的令牌：

    ```js
    Deno.test("it should respond with a 401 to a user with
      an invalid token", async () => {
      const server = await createTestServer();
      const authenticatedHeaders = new Headers();
      authenticatedHeaders.set("content-type",
        "application/json");
    authenticatedHeaders.set("authorization", 
       `Bearer invalid-token`);
      const response = await
        fetch("http://localhost:9001/api/museums", {
          headers: authenticatedHeaders,
          body: JSON.stringify({
          username: "test-user",
          password: "test-password",
        }),
      });
      t.assertEquals(response.status, 401);
      t.assertEquals(await response.text(),
       "Authentication failed");
      server.controller.abort();
    });
    ```

1.  现在，我们可以运行所有测试并确认它们都是绿色的：

    ```js
    $ deno test --unstable --allow-plugin --allow-env --allow-read –-allow-write --allow-net src/index.test.ts      
    running 3 tests
    test it returns user and token when user logs in ... Application running at http://localhost:9001
    POST http://localhost:9001/api/users/register - 3ms
    POST http://localhost:9001/api/login - 3ms
    ok (24ms)
    test it should let users with a valid token access the museums list ... Application running at http://localhost:9001
    POST http://localhost:9001/api/users/register - 0ms
    POST http://localhost:9001/api/login - 1ms
    GET http://localhost:9001/api/museums - 8ms
    ok (15ms)
    test it should respond with a 400 to a user with an invalid token ... Application running at http://localhost:9001
    An error occurred Authentication failed
    ok (5ms)
    test result: ok. 3 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out (45ms)
    ```

本书我们要编写的应用集成测试就这些！如果您想了解更多，请不要担心——本书中编写的一切代码都可以在本书的 GitHub 仓库中找到([`github.com/PacktPublishing/Deno-Web-Development/tree/master/Chapter08/sections/7-final-tested-version/museums-api`](https://github.com/PacktPublishing/Deno-Web-Development/tree/master/Chapter08/sections/7-final-tested-version/museums-api)).

我们现在对代码工作的信心更强了。我们创造了机会来重构、扩展和后期维护代码，担忧更少。我们在架构决策上越来越受益于在隔离测试代码方面的经验。

在前一章中，当我们创建我们的 JavaScript 客户端时，我们提到了将其保存在 API 代码库中的一个优点是，我们可以轻松地为客户端和 API 编写测试，以确保它们一起正常工作。在下一部分，我们将展示如何做到这一点。这些测试将与我们在这里所做的非常相似，唯一的区别是，我们将使用我们创建的 API 客户端，而不是使用`fetch`并进行原始请求。

# 测试应用程序与 API 客户端一起工作

当你向用户提供 API 客户端时，你有责任确保它与你的应用程序完美无缺地工作。确保这一点的一种方法是拥有一个完整的测试套件，不仅要测试客户端本身，还要测试其与 API 的集成。我们将在这里处理后者。

我们将使用 API 客户端的一个功能，并创建一个测试，确保它正在工作。再次，你会注意到这些测试与我们在上一部分末尾编写的测试有一些相似之处。我们将复制先前测试的逻辑，但这次我们将使用客户端。让我们开始吧：

1.  在同一个`src/index.test.ts`文件中，为登录功能创建一个新的测试：

    ```js
    Deno.test("it returns user and token when user logs in
      with the client", async () => {})
    ```

    对于这次测试，我们知道我们需要访问 API 客户端。我们需要从`client`模块中导入它。

1.  从`src/client/index.ts`导入`getClient`函数：

    ```js
    import { getClient } from "./client/index.ts"
    ```

1.  让我们回到`src/index.test.ts`测试，并导入`client`，从而创建一个它的实例。记住，它应该使用测试网络服务器创建的相同地址：

    ```js
    Deno.test("it returns user and token when user logs in
      with the client", async () => {
      const server = await createTestServer();
      const client = getClient({
    createTestServer function and this test, but for simplicity, we won't do this here.
    ```

1.  现在，只需编写使用`client`调用`register`和`login`方法的逻辑。最终测试将看起来像这样：

    ```js
    Deno.test("it returns user and token when user logs in
      with the client", async () => {
    …
      // Register a user
      await client.register(
        { username: "test-user", password: "test-password"
           },
      );
      // Login with the createdUser
      const response = await client.login({
        username: "test-user",
        password: "test-password",
      });
      t.assertEquals(response.user.username, "test-user",
        "returns username");
      t.assert(!!response.user.createdAt, "has createdAt
        date");
      t.assert(!!response.token, "has token");
    …
    });
    ```

    注意我们如何使用客户端的方法进行登录和注册，同时保留来自先前测试的断言。

遵循相同的指导原则，我们可以为客户端的所有功能编写测试，确保它与 API 一起正常工作，从而使我们能够自信地维护它。

为了简洁起见，并且因为这些测试与我们之前编写的测试相似，我们这里不会提供有关为客户端的所有功能编写测试的逐步指南。然而，如果你感兴趣，你可以在本书的 GitHub 仓库中找到它们（[`github.com/PacktPublishing/Deno-Web-Development/blob/master/Chapter08/sections/7-final-tested-version/museums-api/src/index.test.ts`](https://github.com/PacktPublishing/Deno-Web-Development/blob/master/Chapter08/sections/7-final-tested-version/museums-api/src/index.test.ts)）。

在下一部分，我们将预览一个可能位于应用程序路径下方的功能。总有一天，你会发现应用程序的某些部分似乎变得很慢，你希望追踪它们的性能，这时性能测试就派上用场了。因此，我们将引入基准测试。

# 基准测试应用程序的部分

当涉及到用 JavaScript 编写基准测试时，该语言本身提供了一些函数，所有这些函数都包含在高级分辨率时间 API 中。

由于 Deno 完全兼容 ES6，这些相同的功能都可以使用。如果你有机会查看 Deno 的标准库或官方网站，你会发现人们对基准测试给予了大量的关注，并且在 Deno 各个版本中跟踪它们([`deno.land/benchmarks`](https://deno.land/benchmarks))。在检查 Deno 的源代码时，你会发现有关如何编写它们的非常不错的示例集。

对于我们的应用程序，我们本可以轻松地使用浏览器上可用的 API，但 Deno 本身在其标准库中提供了帮助编写和运行基准测试的功能，因此我们将在这里使用它。

首先，我们需要了解 Deno 的标准库基准工具，以便我们知道可以做什么（[`github.com/denoland/deno/blob/ae86cbb551f7b88f83d73a447411f753485e49e2/std/testing/README.md#benching`](https://github.com/denoland/deno/blob/ae86cbb551f7b88f83d73a447411f753485e49e2/std/testing/README.md#benching)）。在本节中，我们将使用两个可用的函数编写一个非常简单的基准测试；即，`bench`和`runBenchmarks`。第一个将定义一个基准测试，而第二个将运行它并将结果打印到控制台。

记得我们在第五章中编写的函数吗？*添加用户和迁移到 Oak*，用于生成散列值和盐值，这使我们能够将用户凭据安全地存储在数据库上？我们将通过以下步骤为此编写基准测试：

1.  首先，在`src/users/util.ts`旁边创建一个名为`utilBenchmarks.ts`的文件。

1.  从`util`模块中导入我们想要测试的两个函数，即`generateSalt`和`hashWithSalt`：

    ```js
    import { generateSalt, hashWithSalt } from "./util.ts"
    ```

1.  是时候将基准工具添加到我们的`src/deps.ts`文件中，并运行`deno cache`命令（我们在第二章中了解到）*工具链*）并在此处导入它。我们将把它作为`benchmark`导出到`src/deps.ts`中，以避免命名冲突：

    ```js
    export * as benchmark from
      "https://deno.land/std@0.83.0/testing/bench.ts";
    ```

1.  将基准工具导入我们的基准测试文件中，并为`generateSalt`函数编写第一个基准测试。我们希望它运行 1000 次：

    ```js
    import { benchmarks } from "../deps.ts";
    benchmarks.bench({
      name: "runsSaltFunction1000Times",
      runs: 1000,
      func: (b) => {
        bench function (as stated in the documentation). Inside this object, we're defining the number of runs, the name of the benchmark, and the test function. That function is what will run every time, since an argument is an object of the BenchmarkTimer type with two methods; that is, start and stop. These methods are used to start and stop the timings of the benchmarks, respectively.
    ```

1.  我们所缺少的只是定义了基准测试后调用`runBenchmarks`：

    ```js
    benchmarks.bench({
      name: "runsSaltFunction1000Times",
      …
    });
    benchmarks.runBenchmarks();
    ```

1.  是时候运行这个文件并查看结果了。

    记住，由于我们希望我们的基准测试具有高精度，所以我们正在处理高分辨率时间。为了让这段代码能够访问这个系统特性，我们需要以`--allow-hrtime`权限运行这个脚本（如第二章中所解释的，*工具链*）：

    ```js
    $ deno run --unstable --allow-plugin --allow-env --allow-read --allow-write --allow-hrtime src/users/utilBenchmarks.ts
    running 1 benchmarks ...
    benchmark runsSaltFunction1000Times ...
        1000 runs avg: 0.036691561000000206ms
    benchmark result: DONE. 1 measured; 0 filtered
    ```

1.  让我们为第二个函数编写基准测试，即`hashWithSalt`：

    ```js
    benchmarks.bench({
      name: "runsHashFunction1000Times",
      runs: 1000,
      func: (b) => {
        b.start();
        hashWithSalt("password", "salt");
        b.stop();
      },
    });
    benchmarks.runBenchmarks();
    ```

1.  现在，让我们运行它，以便得到最终结果：

    ```js
    $ deno run --allow-hrtime --unstable --allow-plugin --allow-env –-allow-write --allow-read src/users/utilBenchmarks.ts     
    running 2 benchmarks ...
    benchmark runsSaltFunction100Times ...
        1000 runs avg: 0.036691561000000206ms
    benchmark runsHashFunction100Times ...
        1000 runs avg: 0.02896806399999923ms
    benchmark result: DONE. 2 measured; 0 filtered
    ```

就这样！现在你可以随时使用我们刚刚编写的代码来分析这些函数的性能。你可能想这样做，是因为你已经修改了这段代码，或者只是因为你想要对其进行严格跟踪。你可以将其集成到诸如持续集成服务器之类的系统中，在那里你可以定期检查这些值并保持其正常运行。

本书关于基准测试的部分就到此为止。我们决定给予它一个简短的介绍，并展示从 Deno 获取的 API，以方便进行基准测试。我们相信，这里介绍的概念和例子可以帮助你跟踪应用程序的运行情况。

# 总结

通过这一章节，我们已经完成了我们一直在构建的应用程序的开发周期。我们从编写几个简单的类开始，带有我们的业务逻辑，编写 web 服务器，最后将其与持久化集成。我们通过学习如何测试我们编写的功能来结束这一部分，这就是我们在这章所做的事情。我们决定选择几种不同类型的测试，而不是深入每个模块编写所有测试，因为我们认为这样做会带来更多的价值。

我们从业务逻辑的一个非常简单的单元测试开始，然后转向带有多个类的集成测试，最后为 web 服务器编写了一个测试。这些测试只能通过利用我们创建的架构、遵循依赖注入原则并尽量使代码解耦来编写。

随着章节的进行，我们转向了集成测试，这些测试紧密地模仿了将在生产环境中运行的队列应用程序，使我们能够提高对我们刚刚编写的代码的信心。我们创建了测试，使用测试设置实例化了应用程序，能够启动带有所有应用程序层（业务逻辑、持久化和网络）的 web 服务器，并对它进行断言。在这些测试中，我们可以非常有信心地断言登录和注册行为是否正常，因为我们向 API 发送了真实的请求。

为了结束本章，我们将它与前一章连接起来，我们在那里为 API 编写了一个 JavaScript 客户端。我们利用了客户端与 API 位于同一代码库中的一个巨大优势，并一起测试了客户端及其应用程序。这是确保一切按预期工作，并在发布 API 和客户端更改时保持信心的好方法。

本章试图展示如何在 Deno 中使用测试来提高我们对所编写代码的信心，以及当它们用于关注简单结果时所提供的价值。这类测试在应用更改时将非常有用，因为我们可以使用它们来添加更多功能或改进现有功能。在这里，我们了解到 Deno 提供的测试套件足以编写清晰、可读的测试，而无需任何第三方包。

下一章将重点关注应用开发过程中最重要的阶段之一，那就是部署。我们将配置一个非常简单的持续集成环境，在该环境中我们可以将应用部署到云端。这一章节非常重要，因为我们将体验到 Deno 在部署方面的某些优势。

迫不及待地想让你的应用供用户使用吗？我们也是——让我们开始吧！
