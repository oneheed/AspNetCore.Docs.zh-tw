---
title: ':::no-loc(SignalR)::: HubCoNtext'
author: bradygaster
description: '瞭解如何使用 ASP.NET Core :::no-loc(SignalR)::: HubCoNtext 服務，從中樞外部將通知傳送至用戶端。'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: signalr/hubcontext
ms.openlocfilehash: 91d02ea9e15a2c3910c3b10159bf5b1523c8e271
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058177"
---
# <a name="send-messages-from-outside-a-hub"></a><span data-ttu-id="2fd43-103">從中樞外部傳送訊息</span><span class="sxs-lookup"><span data-stu-id="2fd43-103">Send messages from outside a hub</span></span>

<span data-ttu-id="2fd43-104">依 [Mikael Mengistu](https://twitter.com/MikaelM_12)</span><span class="sxs-lookup"><span data-stu-id="2fd43-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="2fd43-105">:::no-loc(SignalR):::中樞是將訊息傳送給連接到伺服器之用戶端的核心抽象概念 :::no-loc(SignalR)::: 。</span><span class="sxs-lookup"><span data-stu-id="2fd43-105">The :::no-loc(SignalR)::: hub is the core abstraction for sending messages to clients connected to the :::no-loc(SignalR)::: server.</span></span> <span data-ttu-id="2fd43-106">也可以使用服務從應用程式中的其他位置傳送訊息 `IHubContext` 。</span><span class="sxs-lookup"><span data-stu-id="2fd43-106">It's also possible to send messages from other places in your app using the `IHubContext` service.</span></span> <span data-ttu-id="2fd43-107">本文說明如何存取 :::no-loc(SignalR)::: `IHubContext` ，以將通知從中樞外部傳送至用戶端。</span><span class="sxs-lookup"><span data-stu-id="2fd43-107">This article explains how to access a :::no-loc(SignalR)::: `IHubContext` to send notifications to clients from outside a hub.</span></span>

<span data-ttu-id="2fd43-108">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [ (如何下載) ](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="2fd43-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="get-an-instance-of-ihubcontext"></a><span data-ttu-id="2fd43-109">取得 IHubCoNtext 的實例</span><span class="sxs-lookup"><span data-stu-id="2fd43-109">Get an instance of IHubContext</span></span>

<span data-ttu-id="2fd43-110">在 ASP.NET Core 中 :::no-loc(SignalR)::: ，您可以透過相依性插入來存取的實例 `IHubContext` 。</span><span class="sxs-lookup"><span data-stu-id="2fd43-110">In ASP.NET Core :::no-loc(SignalR):::, you can access an instance of `IHubContext` via dependency injection.</span></span> <span data-ttu-id="2fd43-111">您可以將的實例插入 `IHubContext` 控制器、中介軟體或其他 DI 服務。</span><span class="sxs-lookup"><span data-stu-id="2fd43-111">You can inject an instance of `IHubContext` into a controller, middleware, or other DI service.</span></span> <span data-ttu-id="2fd43-112">使用實例將訊息傳送至用戶端。</span><span class="sxs-lookup"><span data-stu-id="2fd43-112">Use the instance to send messages to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="2fd43-113">這與 ASP.NET 4.x 不同， :::no-loc(SignalR)::: 後者使用 GlobalHost 來提供的存取權 `IHubContext` 。</span><span class="sxs-lookup"><span data-stu-id="2fd43-113">This differs from ASP.NET 4.x :::no-loc(SignalR)::: which used GlobalHost to provide access to the `IHubContext`.</span></span> <span data-ttu-id="2fd43-114">ASP.NET Core 具有相依性插入架構，以免除此全域 singleton 的需求。</span><span class="sxs-lookup"><span data-stu-id="2fd43-114">ASP.NET Core has a dependency injection framework that removes the need for this global singleton.</span></span>

### <a name="inject-an-instance-of-ihubcontext-in-a-controller"></a><span data-ttu-id="2fd43-115">在控制器中插入 IHubCoNtext 的實例</span><span class="sxs-lookup"><span data-stu-id="2fd43-115">Inject an instance of IHubContext in a controller</span></span>

<span data-ttu-id="2fd43-116">您可以藉由將實例加入至您的函式，將實例插入 `IHubContext` 至控制器：</span><span class="sxs-lookup"><span data-stu-id="2fd43-116">You can inject an instance of `IHubContext` into a controller by adding it to your constructor:</span></span>

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=12-19,57)]

<span data-ttu-id="2fd43-117">現在，有了實例的存取權 `IHubContext` ，您就可以像在中樞本身一樣呼叫中樞方法。</span><span class="sxs-lookup"><span data-stu-id="2fd43-117">Now, with access to an instance of `IHubContext`, you can call hub methods as if you were in the hub itself.</span></span>

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=21-25)]

### <a name="get-an-instance-of-ihubcontext-in-middleware"></a><span data-ttu-id="2fd43-118">在中介軟體中取得 IHubCoNtext 的實例</span><span class="sxs-lookup"><span data-stu-id="2fd43-118">Get an instance of IHubContext in middleware</span></span>

<span data-ttu-id="2fd43-119">在 `IHubContext` 中介軟體管線記憶體取，如下所示：</span><span class="sxs-lookup"><span data-stu-id="2fd43-119">Access the `IHubContext` within the middleware pipeline like so:</span></span>

```csharp
app.Use(async (context, next) =>
{
    var hubContext = context.RequestServices
                            .GetRequiredService<IHubContext<ChatHub>>();
    //...
    
    if (next != null)
    {
        await next.Invoke();
    }
});
```

> [!NOTE]
> <span data-ttu-id="2fd43-120">從類別外部呼叫中樞方法時 `Hub` ，沒有與調用相關聯的呼叫端。</span><span class="sxs-lookup"><span data-stu-id="2fd43-120">When hub methods are called from outside of the `Hub` class, there's no caller associated with the invocation.</span></span> <span data-ttu-id="2fd43-121">因此，沒有 `ConnectionId` 、和屬性的存取權 `Caller` `Others` 。</span><span class="sxs-lookup"><span data-stu-id="2fd43-121">Therefore, there's no access to the `ConnectionId`, `Caller`, and `Others` properties.</span></span>

### <a name="get-an-instance-of-ihubcontext-from-ihost"></a><span data-ttu-id="2fd43-122">從 IHost 取得 IHubCoNtext 的實例</span><span class="sxs-lookup"><span data-stu-id="2fd43-122">Get an instance of IHubContext from IHost</span></span>

<span data-ttu-id="2fd43-123">`IHubContext`從 web 主機存取可用於整合 ASP.NET Core 以外的區域，例如使用協力廠商相依性插入架構：</span><span class="sxs-lookup"><span data-stu-id="2fd43-123">Accessing an `IHubContext` from the web host is useful for integrating with areas outside of ASP.NET Core, for example, using third-party dependency injection frameworks:</span></span>

```csharp
    public class Program
    {
        public static void Main(string[] args)
        {
            var host = CreateHostBuilder(args).Build();
            var hubContext = host.Services.GetService(typeof(IHubContext<ChatHub>));
            host.Run();
        }

        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureWebHostDefaults(webBuilder => {
                    webBuilder.UseStartup<Startup>();
                });
    }
```

### <a name="inject-a-strongly-typed-hubcontext"></a><span data-ttu-id="2fd43-124">插入強型別 HubCoNtext</span><span class="sxs-lookup"><span data-stu-id="2fd43-124">Inject a strongly-typed HubContext</span></span>

<span data-ttu-id="2fd43-125">若要插入強型別 HubCoNtext，請確定您的中樞繼承自 `Hub<T>` 。</span><span class="sxs-lookup"><span data-stu-id="2fd43-125">To inject a strongly-typed HubContext, ensure your Hub inherits from `Hub<T>`.</span></span> <span data-ttu-id="2fd43-126">使用 `IHubContext<THub, T>` 介面（而非）插入它 `IHubContext<THub>` 。</span><span class="sxs-lookup"><span data-stu-id="2fd43-126">Inject it using the `IHubContext<THub, T>` interface rather than `IHubContext<THub>`.</span></span>

```csharp
public class ChatController : Controller
{
    public IHubContext<ChatHub, IChatClient> _strongChatHubContext { get; }

    public ChatController(IHubContext<ChatHub, IChatClient> chatHubContext)
    {
        _strongChatHubContext = chatHubContext;
    }

    public async Task SendMessage(string user, string message)
    {
        await _strongChatHubContext.Clients.All.ReceiveMessage(user, message);
    }
}
```

<span data-ttu-id="2fd43-127">如需詳細資訊，請參閱 [強型別中樞](xref:signalr/hubs#strongly-typed-hubs) 。</span><span class="sxs-lookup"><span data-stu-id="2fd43-127">See [Strongly typed hubs](xref:signalr/hubs#strongly-typed-hubs) for more information.</span></span>

## <a name="related-resources"></a><span data-ttu-id="2fd43-128">相關資源</span><span class="sxs-lookup"><span data-stu-id="2fd43-128">Related resources</span></span>

* [<span data-ttu-id="2fd43-129">開始使用</span><span class="sxs-lookup"><span data-stu-id="2fd43-129">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="2fd43-130">中樞</span><span class="sxs-lookup"><span data-stu-id="2fd43-130">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="2fd43-131">發佈至 Azure</span><span class="sxs-lookup"><span data-stu-id="2fd43-131">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
