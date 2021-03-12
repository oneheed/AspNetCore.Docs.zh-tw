---
title: 使用 ASP.NET Core 中的中樞 SignalR
author: bradygaster
description: 瞭解如何使用 ASP.NET Core 中的中樞 SignalR 。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/hubs
ms.openlocfilehash: 872b88cc3c87137365de8c50a37bf5dd5fd9fe10
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587862"
---
# <a name="use-hubs-in-signalr-for-aspnet-core"></a><span data-ttu-id="66d3c-103">使用中 SignalR 的中樞進行 ASP.NET 核心</span><span class="sxs-lookup"><span data-stu-id="66d3c-103">Use hubs in SignalR for ASP.NET Core</span></span>

<span data-ttu-id="66d3c-104">[Rachel Appel](https://twitter.com/rachelappel)和[古柯 Griffin](https://twitter.com/1kevgriff)</span><span class="sxs-lookup"><span data-stu-id="66d3c-104">By [Rachel Appel](https://twitter.com/rachelappel) and [Kevin Griffin](https://twitter.com/1kevgriff)</span></span>

<span data-ttu-id="66d3c-105">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/signalr/hubs/sample/ ) [ (如何下載) ](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="66d3c-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/signalr/hubs/sample/ ) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="what-is-a-signalr-hub"></a><span data-ttu-id="66d3c-106">什麼是 SignalR 中樞</span><span class="sxs-lookup"><span data-stu-id="66d3c-106">What is a SignalR hub</span></span>

<span data-ttu-id="66d3c-107">SignalR中樞 API 可讓您從伺服器呼叫連接用戶端上的方法。</span><span class="sxs-lookup"><span data-stu-id="66d3c-107">The SignalR Hubs API enables you to call methods on connected clients from the server.</span></span> <span data-ttu-id="66d3c-108">在伺服器程式碼中，您可以定義用戶端所呼叫的方法。</span><span class="sxs-lookup"><span data-stu-id="66d3c-108">In the server code, you define methods that are called by client.</span></span> <span data-ttu-id="66d3c-109">在用戶端程式代碼中，您可以定義從伺服器呼叫的方法。</span><span class="sxs-lookup"><span data-stu-id="66d3c-109">In the client code, you define methods that are called from the server.</span></span> <span data-ttu-id="66d3c-110">SignalR 會處理幕後的所有專案，讓用戶端對伺服器和伺服器對用戶端的通訊能夠正常運作。</span><span class="sxs-lookup"><span data-stu-id="66d3c-110">SignalR takes care of everything behind the scenes that makes real-time client-to-server and server-to-client communications possible.</span></span>

## <a name="configure-signalr-hubs"></a><span data-ttu-id="66d3c-111">設定 SignalR 中樞</span><span class="sxs-lookup"><span data-stu-id="66d3c-111">Configure SignalR hubs</span></span>

<span data-ttu-id="66d3c-112">SignalR中介軟體需要一些透過呼叫所設定的服務 `services.AddSignalR` 。</span><span class="sxs-lookup"><span data-stu-id="66d3c-112">The SignalR middleware requires some services, which are configured by calling `services.AddSignalR`.</span></span>

[!code-csharp[Configure service](hubs/sample/startup.cs?range=38)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="66d3c-113">將 SignalR 功能新增至 ASP.NET Core 應用程式時，會 SignalR `endpoint.MapHub` 在 `Startup.Configure` 方法的回呼中呼叫來設定路由 `app.UseEndpoints` 。</span><span class="sxs-lookup"><span data-stu-id="66d3c-113">When adding SignalR functionality to an ASP.NET Core app, setup SignalR routes by calling `endpoint.MapHub` in the `Startup.Configure` method's `app.UseEndpoints` callback.</span></span>

```csharp
app.UseRouting();
app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/chathub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="66d3c-114">將 SignalR 功能新增至 ASP.NET Core 應用程式時，會 SignalR `app.UseSignalR` 在方法中呼叫來設定路由 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="66d3c-114">When adding SignalR functionality to an ASP.NET Core app, setup SignalR routes by calling `app.UseSignalR` in the `Startup.Configure` method.</span></span>

[!code-csharp[Configure routes to hubs](hubs/sample/startup.cs?range=57-60)]

::: moniker-end

## <a name="create-and-use-hubs"></a><span data-ttu-id="66d3c-115">建立和使用中樞</span><span class="sxs-lookup"><span data-stu-id="66d3c-115">Create and use hubs</span></span>

<span data-ttu-id="66d3c-116">宣告繼承自的類別 `Hub` ，並在其中新增公用方法，以建立中樞。</span><span class="sxs-lookup"><span data-stu-id="66d3c-116">Create a hub by declaring a class that inherits from `Hub`, and add public methods to it.</span></span> <span data-ttu-id="66d3c-117">用戶端可以呼叫定義為的方法 `public` 。</span><span class="sxs-lookup"><span data-stu-id="66d3c-117">Clients can call methods that are defined as `public`.</span></span>

```csharp
public class ChatHub : Hub
{
    public Task SendMessage(string user, string message)
    {
        return Clients.All.SendAsync("ReceiveMessage", user, message);
    }
}
```

<span data-ttu-id="66d3c-118">您可以指定傳回型別和參數，包括複雜類型和陣列，就像在任何 c # 方法中一樣。</span><span class="sxs-lookup"><span data-stu-id="66d3c-118">You can specify a return type and parameters, including complex types and arrays, as you would in any C# method.</span></span> <span data-ttu-id="66d3c-119">SignalR 在您的參數和傳回值中處理複雜物件和陣列的序列化和還原序列化。</span><span class="sxs-lookup"><span data-stu-id="66d3c-119">SignalR handles the serialization and deserialization of complex objects and arrays in your parameters and return values.</span></span>

> [!NOTE]
> <span data-ttu-id="66d3c-120">中樞是暫時性的：</span><span class="sxs-lookup"><span data-stu-id="66d3c-120">Hubs are transient:</span></span>
>
> * <span data-ttu-id="66d3c-121">請勿在中樞類別的屬性中儲存狀態。</span><span class="sxs-lookup"><span data-stu-id="66d3c-121">Don't store state in a property on the hub class.</span></span> <span data-ttu-id="66d3c-122">每個中樞方法呼叫都會在新的中樞實例上執行。</span><span class="sxs-lookup"><span data-stu-id="66d3c-122">Every hub method call is executed on a new hub instance.</span></span>
> * <span data-ttu-id="66d3c-123">`await`在呼叫相依于中樞保持運作的非同步方法時使用。</span><span class="sxs-lookup"><span data-stu-id="66d3c-123">Use `await` when calling asynchronous methods that depend on the hub staying alive.</span></span> <span data-ttu-id="66d3c-124">例如， `Clients.All.SendAsync(...)` 如果在未呼叫的情況下呼叫， `await` 且中樞方法在完成之前完成，則像這樣的方法可能會失敗 `SendAsync` 。</span><span class="sxs-lookup"><span data-stu-id="66d3c-124">For example, a method such as `Clients.All.SendAsync(...)` can fail if it's called without `await` and the hub method completes before `SendAsync` finishes.</span></span>

## <a name="the-context-object"></a><span data-ttu-id="66d3c-125">CoNtext 物件</span><span class="sxs-lookup"><span data-stu-id="66d3c-125">The Context object</span></span>

<span data-ttu-id="66d3c-126">`Hub`類別具有 `Context` 屬性，其中包含下列具有連接相關資訊的屬性：</span><span class="sxs-lookup"><span data-stu-id="66d3c-126">The `Hub` class has a `Context` property that contains the following properties with information about the connection:</span></span>

| <span data-ttu-id="66d3c-127">屬性</span><span class="sxs-lookup"><span data-stu-id="66d3c-127">Property</span></span> | <span data-ttu-id="66d3c-128">描述</span><span class="sxs-lookup"><span data-stu-id="66d3c-128">Description</span></span> |
| ------ | ----------- |
| `ConnectionId` | <span data-ttu-id="66d3c-129">取得所指派之連接的唯一識別碼 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="66d3c-129">Gets the unique ID for the connection, assigned by SignalR.</span></span> <span data-ttu-id="66d3c-130">每個連接都有一個連接識別碼。</span><span class="sxs-lookup"><span data-stu-id="66d3c-130">There is one connection ID for each connection.</span></span>|
| `UserIdentifier` | <span data-ttu-id="66d3c-131">取得 [使用者識別碼](xref:signalr/groups)。</span><span class="sxs-lookup"><span data-stu-id="66d3c-131">Gets the [user identifier](xref:signalr/groups).</span></span> <span data-ttu-id="66d3c-132">根據預設，會 SignalR 使用 `ClaimTypes.NameIdentifier` `ClaimsPrincipal` 與連接相關聯的，做為使用者識別碼。</span><span class="sxs-lookup"><span data-stu-id="66d3c-132">By default, SignalR uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> |
| `User` | <span data-ttu-id="66d3c-133">取得 `ClaimsPrincipal` 與目前使用者相關聯的。</span><span class="sxs-lookup"><span data-stu-id="66d3c-133">Gets the `ClaimsPrincipal` associated with the current user.</span></span> |
| `Items` | <span data-ttu-id="66d3c-134">取得索引鍵/值集合，可用來在此連接的範圍內共用資料。</span><span class="sxs-lookup"><span data-stu-id="66d3c-134">Gets a key/value collection that can be used to share data within the scope of this connection.</span></span> <span data-ttu-id="66d3c-135">資料可以儲存在此集合中，而且會針對跨不同中樞方法調用的連接保存。</span><span class="sxs-lookup"><span data-stu-id="66d3c-135">Data can be stored in this collection and it will persist for the connection across different hub method invocations.</span></span> |
| `Features` | <span data-ttu-id="66d3c-136">取得連接上可用之功能的集合。</span><span class="sxs-lookup"><span data-stu-id="66d3c-136">Gets the collection of features available on the connection.</span></span> <span data-ttu-id="66d3c-137">目前，在大部分情況下並不需要此集合，因此尚未詳細記載。</span><span class="sxs-lookup"><span data-stu-id="66d3c-137">For now, this collection isn't needed in most scenarios, so it isn't documented in detail yet.</span></span> |
| `ConnectionAborted` | <span data-ttu-id="66d3c-138">取得，以在 `CancellationToken` 中斷連接時通知。</span><span class="sxs-lookup"><span data-stu-id="66d3c-138">Gets a `CancellationToken` that notifies when the connection is aborted.</span></span> |

<span data-ttu-id="66d3c-139">`Hub.Context` 也包含下列方法：</span><span class="sxs-lookup"><span data-stu-id="66d3c-139">`Hub.Context` also contains the following methods:</span></span>

| <span data-ttu-id="66d3c-140">方法</span><span class="sxs-lookup"><span data-stu-id="66d3c-140">Method</span></span> | <span data-ttu-id="66d3c-141">描述</span><span class="sxs-lookup"><span data-stu-id="66d3c-141">Description</span></span> |
| ------ | ----------- |
| `GetHttpContext` | <span data-ttu-id="66d3c-142">傳回 `HttpContext` 連接的， `null` 如果連接與 HTTP 要求沒有關聯，則為。</span><span class="sxs-lookup"><span data-stu-id="66d3c-142">Returns the `HttpContext` for the connection, or `null` if the connection is not associated with an HTTP request.</span></span> <span data-ttu-id="66d3c-143">若為 HTTP 連接，您可以使用這個方法來取得 HTTP 標頭和查詢字串等資訊。</span><span class="sxs-lookup"><span data-stu-id="66d3c-143">For HTTP connections, you can use this method to get information such as HTTP headers and query strings.</span></span> |
| `Abort` | <span data-ttu-id="66d3c-144">中止連線。</span><span class="sxs-lookup"><span data-stu-id="66d3c-144">Aborts the connection.</span></span> |

## <a name="the-clients-object"></a><span data-ttu-id="66d3c-145">用戶端物件</span><span class="sxs-lookup"><span data-stu-id="66d3c-145">The Clients object</span></span>

<span data-ttu-id="66d3c-146">`Hub`類別具有 `Clients` 屬性，其中包含伺服器與用戶端之間通訊的下列屬性：</span><span class="sxs-lookup"><span data-stu-id="66d3c-146">The `Hub` class has a `Clients` property that contains the following properties for communication between server and client:</span></span>

| <span data-ttu-id="66d3c-147">屬性</span><span class="sxs-lookup"><span data-stu-id="66d3c-147">Property</span></span> | <span data-ttu-id="66d3c-148">描述</span><span class="sxs-lookup"><span data-stu-id="66d3c-148">Description</span></span> |
| ------ | ----------- |
| `All` | <span data-ttu-id="66d3c-149">在所有連線的用戶端上呼叫方法</span><span class="sxs-lookup"><span data-stu-id="66d3c-149">Calls a method on all connected clients</span></span> |
| `Caller` | <span data-ttu-id="66d3c-150">在叫用中樞方法的用戶端上呼叫方法</span><span class="sxs-lookup"><span data-stu-id="66d3c-150">Calls a method on the client that invoked the hub method</span></span> |
| `Others` | <span data-ttu-id="66d3c-151">除了叫用方法的用戶端之外，在所有連線的用戶端上呼叫方法</span><span class="sxs-lookup"><span data-stu-id="66d3c-151">Calls a method on all connected clients except the client that invoked the method</span></span> |

<span data-ttu-id="66d3c-152">`Hub.Clients` 也包含下列方法：</span><span class="sxs-lookup"><span data-stu-id="66d3c-152">`Hub.Clients` also contains the following methods:</span></span>

| <span data-ttu-id="66d3c-153">方法</span><span class="sxs-lookup"><span data-stu-id="66d3c-153">Method</span></span> | <span data-ttu-id="66d3c-154">描述</span><span class="sxs-lookup"><span data-stu-id="66d3c-154">Description</span></span> |
| ------ | ----------- |
| `AllExcept` | <span data-ttu-id="66d3c-155">在所有連線的用戶端上呼叫方法（指定的連接除外）</span><span class="sxs-lookup"><span data-stu-id="66d3c-155">Calls a method on all connected clients except for the specified connections</span></span> |
| `Client` | <span data-ttu-id="66d3c-156">在特定連線用戶端上呼叫方法</span><span class="sxs-lookup"><span data-stu-id="66d3c-156">Calls a method on a specific connected client</span></span> |
| `Clients` | <span data-ttu-id="66d3c-157">在特定連線用戶端上呼叫方法</span><span class="sxs-lookup"><span data-stu-id="66d3c-157">Calls a method on specific connected clients</span></span> |
| `Group` | <span data-ttu-id="66d3c-158">在指定群組中的所有連接上呼叫方法</span><span class="sxs-lookup"><span data-stu-id="66d3c-158">Calls a method on all connections in the specified group</span></span>  |
| `GroupExcept` | <span data-ttu-id="66d3c-159">在指定的群組中的所有連接上呼叫方法（指定的連接除外）</span><span class="sxs-lookup"><span data-stu-id="66d3c-159">Calls a method on all connections in the specified group, except the specified connections</span></span> |
| `Groups` | <span data-ttu-id="66d3c-160">在多個連接群組上呼叫方法</span><span class="sxs-lookup"><span data-stu-id="66d3c-160">Calls a method on multiple groups of connections</span></span>  |
| `OthersInGroup` | <span data-ttu-id="66d3c-161">在一組連接上呼叫方法，但不包括叫用中樞方法的用戶端。</span><span class="sxs-lookup"><span data-stu-id="66d3c-161">Calls a method on a group of connections, excluding the client that invoked the hub method</span></span>  |
| `User` | <span data-ttu-id="66d3c-162">在所有與特定使用者相關聯的連接上呼叫方法</span><span class="sxs-lookup"><span data-stu-id="66d3c-162">Calls a method on all connections associated with a specific user</span></span> |
| `Users` | <span data-ttu-id="66d3c-163">在與指定使用者相關聯的所有連接上呼叫方法</span><span class="sxs-lookup"><span data-stu-id="66d3c-163">Calls a method on all connections associated with the specified users</span></span> |

<span data-ttu-id="66d3c-164">上述資料表中的每個屬性或方法都會傳回具有方法的物件 `SendAsync` 。</span><span class="sxs-lookup"><span data-stu-id="66d3c-164">Each property or method in the preceding tables returns an object with a `SendAsync` method.</span></span> <span data-ttu-id="66d3c-165">`SendAsync`方法可讓您提供要呼叫的用戶端方法名稱和參數。</span><span class="sxs-lookup"><span data-stu-id="66d3c-165">The `SendAsync` method allows you to supply the name and parameters of the client method to call.</span></span>

## <a name="send-messages-to-clients"></a><span data-ttu-id="66d3c-166">將訊息傳送給用戶端</span><span class="sxs-lookup"><span data-stu-id="66d3c-166">Send messages to clients</span></span>

<span data-ttu-id="66d3c-167">若要對特定用戶端進行呼叫，請使用物件的屬性 `Clients` 。</span><span class="sxs-lookup"><span data-stu-id="66d3c-167">To make calls to specific clients, use the properties of the `Clients` object.</span></span> <span data-ttu-id="66d3c-168">在下列範例中，有三個中樞方法：</span><span class="sxs-lookup"><span data-stu-id="66d3c-168">In the following example, there are three Hub methods:</span></span>

* <span data-ttu-id="66d3c-169">`SendMessage` 使用將訊息傳送至所有連線的用戶端 `Clients.All` 。</span><span class="sxs-lookup"><span data-stu-id="66d3c-169">`SendMessage` sends a message to all connected clients, using `Clients.All`.</span></span>
* <span data-ttu-id="66d3c-170">`SendMessageToCaller` 使用將訊息傳回給呼叫端 `Clients.Caller` 。</span><span class="sxs-lookup"><span data-stu-id="66d3c-170">`SendMessageToCaller` sends a message back to the caller, using `Clients.Caller`.</span></span>
* <span data-ttu-id="66d3c-171">`SendMessageToGroups` 將訊息傳送給群組中的所有用戶端 `SignalR Users` 。</span><span class="sxs-lookup"><span data-stu-id="66d3c-171">`SendMessageToGroups` sends a message to all clients in the `SignalR Users` group.</span></span>

[!code-csharp[Send messages](hubs/sample/hubs/chathub.cs?name=HubMethods)]

## <a name="strongly-typed-hubs"></a><span data-ttu-id="66d3c-172">強型別中樞</span><span class="sxs-lookup"><span data-stu-id="66d3c-172">Strongly typed hubs</span></span>

<span data-ttu-id="66d3c-173">使用的缺點 `SendAsync` 是它依賴魔術字串來指定要呼叫的用戶端方法。</span><span class="sxs-lookup"><span data-stu-id="66d3c-173">A drawback of using `SendAsync` is that it relies on a magic string to specify the client method to be called.</span></span> <span data-ttu-id="66d3c-174">如果用戶端的方法名稱拼錯或遺失，則會將程式碼開放給執行階段錯誤。</span><span class="sxs-lookup"><span data-stu-id="66d3c-174">This leaves code open to runtime errors if the method name is misspelled or missing from the client.</span></span>

<span data-ttu-id="66d3c-175">使用的替代方法 `SendAsync` 是使用強型別 `Hub` <xref:Microsoft.AspNetCore.SignalR.Hub%601> 。</span><span class="sxs-lookup"><span data-stu-id="66d3c-175">An alternative to using `SendAsync` is to strongly type the `Hub` with <xref:Microsoft.AspNetCore.SignalR.Hub%601>.</span></span> <span data-ttu-id="66d3c-176">在下列範例中， `ChatHub` 已將用戶端方法解壓縮至名為的介面 `IChatClient` 。</span><span class="sxs-lookup"><span data-stu-id="66d3c-176">In the following example, the `ChatHub` client methods have been extracted out into an interface called `IChatClient`.</span></span>

[!code-csharp[Interface for IChatClient](hubs/sample/hubs/ichatclient.cs?name=snippet_IChatClient)]

<span data-ttu-id="66d3c-177">這個介面可以用來重構上述 `ChatHub` 範例。</span><span class="sxs-lookup"><span data-stu-id="66d3c-177">This interface can be used to refactor the preceding `ChatHub` example.</span></span>

[!code-csharp[Strongly typed ChatHub](hubs/sample/hubs/StronglyTypedChatHub.cs?range=8-18,36)]

<span data-ttu-id="66d3c-178">使用 `Hub<IChatClient>` 可啟用用戶端方法的編譯時間檢查。</span><span class="sxs-lookup"><span data-stu-id="66d3c-178">Using `Hub<IChatClient>` enables compile-time checking of the client methods.</span></span> <span data-ttu-id="66d3c-179">這可防止使用魔術字串所造成的問題，因為只能 `Hub<T>` 提供介面中所定義方法的存取權。</span><span class="sxs-lookup"><span data-stu-id="66d3c-179">This prevents issues caused by using magic strings, since `Hub<T>` can only provide access to the methods defined in the interface.</span></span>

<span data-ttu-id="66d3c-180">使用強型別會 `Hub<T>` 停用使用的能力 `SendAsync` 。</span><span class="sxs-lookup"><span data-stu-id="66d3c-180">Using a strongly typed `Hub<T>` disables the ability to use `SendAsync`.</span></span> <span data-ttu-id="66d3c-181">在介面上定義的任何方法仍可以定義為非同步。</span><span class="sxs-lookup"><span data-stu-id="66d3c-181">Any methods defined on the interface can still be defined as asynchronous.</span></span> <span data-ttu-id="66d3c-182">事實上，這些方法都應該傳回 `Task` 。</span><span class="sxs-lookup"><span data-stu-id="66d3c-182">In fact, each of these methods should return a `Task`.</span></span> <span data-ttu-id="66d3c-183">由於它是介面，因此請勿使用 `async` 關鍵字。</span><span class="sxs-lookup"><span data-stu-id="66d3c-183">Since it's an interface, don't use the `async` keyword.</span></span> <span data-ttu-id="66d3c-184">例如：</span><span class="sxs-lookup"><span data-stu-id="66d3c-184">For example:</span></span>

```csharp
public interface IClient
{
    Task ClientMethod();
}
```

> [!NOTE]
> <span data-ttu-id="66d3c-185">`Async`不會從方法名稱中移除尾碼。</span><span class="sxs-lookup"><span data-stu-id="66d3c-185">The `Async` suffix isn't stripped from the method name.</span></span> <span data-ttu-id="66d3c-186">除非您的用戶端方法是使用來定義 `.on('MyMethodAsync')` ，否則您不應該使用 `MyMethodAsync` 做為名稱。</span><span class="sxs-lookup"><span data-stu-id="66d3c-186">Unless your client method is defined with `.on('MyMethodAsync')`, you shouldn't use `MyMethodAsync` as a name.</span></span>

## <a name="change-the-name-of-a-hub-method"></a><span data-ttu-id="66d3c-187">變更中樞方法的名稱</span><span class="sxs-lookup"><span data-stu-id="66d3c-187">Change the name of a hub method</span></span>

<span data-ttu-id="66d3c-188">根據預設，伺服器中樞方法名稱是 .NET 方法的名稱。</span><span class="sxs-lookup"><span data-stu-id="66d3c-188">By default, a server hub method name is the name of the .NET method.</span></span> <span data-ttu-id="66d3c-189">不過，您可以使用 [HubMethodName](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) 屬性來變更這個預設值，並手動指定方法的名稱。</span><span class="sxs-lookup"><span data-stu-id="66d3c-189">However, you can use the [HubMethodName](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) attribute to change this default and manually specify a name for the method.</span></span> <span data-ttu-id="66d3c-190">叫用方法時，用戶端應該使用這個名稱，而不是 .NET 方法名稱。</span><span class="sxs-lookup"><span data-stu-id="66d3c-190">The client should use this name, instead of the .NET method name, when invoking the method.</span></span>

[!code-csharp[HubMethodName attribute](hubs/sample/hubs/chathub.cs?name=HubMethodName&highlight=1)]

## <a name="handle-events-for-a-connection"></a><span data-ttu-id="66d3c-191">處理連接的事件</span><span class="sxs-lookup"><span data-stu-id="66d3c-191">Handle events for a connection</span></span>

<span data-ttu-id="66d3c-192">SignalR中樞 API 提供 `OnConnectedAsync` 和 `OnDisconnectedAsync` 虛擬方法來管理和追蹤連接。</span><span class="sxs-lookup"><span data-stu-id="66d3c-192">The SignalR Hubs API provides the `OnConnectedAsync` and `OnDisconnectedAsync` virtual methods to manage and track connections.</span></span> <span data-ttu-id="66d3c-193">覆寫 `OnConnectedAsync` 虛擬方法，以在用戶端連線至中樞時執行動作，例如將它新增至群組。</span><span class="sxs-lookup"><span data-stu-id="66d3c-193">Override the `OnConnectedAsync` virtual method to perform actions when a client connects to the Hub, such as adding it to a group.</span></span>

[!code-csharp[Handle connection](hubs/sample/hubs/chathub.cs?name=OnConnectedAsync)]

<span data-ttu-id="66d3c-194">覆寫 `OnDisconnectedAsync` 虛擬方法，以在用戶端中斷連線時執行動作。</span><span class="sxs-lookup"><span data-stu-id="66d3c-194">Override the `OnDisconnectedAsync` virtual method to perform actions when a client disconnects.</span></span> <span data-ttu-id="66d3c-195">如果用戶端藉由呼叫來刻意中斷 (`connection.stop()` ，例如) ， `exception` 參數將會是 `null` 。</span><span class="sxs-lookup"><span data-stu-id="66d3c-195">If the client disconnects intentionally (by calling `connection.stop()`, for example), the `exception` parameter will be `null`.</span></span> <span data-ttu-id="66d3c-196">但是，如果用戶端因為錯誤而中斷連線 (例如網路失敗) ， `exception` 參數將會包含描述失敗的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="66d3c-196">However, if the client is disconnected due to an error (such as a network failure), the `exception` parameter will contain an exception describing the failure.</span></span>

[!code-csharp[Handle disconnection](hubs/sample/hubs/chathub.cs?name=OnDisconnectedAsync)]

[!INCLUDE[](~/includes/connectionid-signalr.md)]

## <a name="handle-errors"></a><span data-ttu-id="66d3c-197">處理錯誤</span><span class="sxs-lookup"><span data-stu-id="66d3c-197">Handle errors</span></span>

<span data-ttu-id="66d3c-198">在您的中樞方法中擲回的例外狀況會傳送至叫用方法的用戶端。</span><span class="sxs-lookup"><span data-stu-id="66d3c-198">Exceptions thrown in your hub methods are sent to the client that invoked the method.</span></span> <span data-ttu-id="66d3c-199">在 JavaScript 用戶端上，此方法會傳回 `invoke` [javascript 承諾](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises)。</span><span class="sxs-lookup"><span data-stu-id="66d3c-199">On the JavaScript client, the `invoke` method returns a [JavaScript Promise](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises).</span></span> <span data-ttu-id="66d3c-200">當用戶端收到使用附加至承諾的處理常式時發生錯誤時 `catch` ，就會叫用該處理常式，並將其傳遞為 JavaScript `Error` 物件。</span><span class="sxs-lookup"><span data-stu-id="66d3c-200">When the client receives an error with a handler attached to the promise using `catch`, it's invoked and passed as a JavaScript `Error` object.</span></span>

[!code-javascript[Error](hubs/sample/wwwroot/js/chat.js?range=23)]

<span data-ttu-id="66d3c-201">如果您的中樞擲回例外狀況，則不會關閉連接。</span><span class="sxs-lookup"><span data-stu-id="66d3c-201">If your Hub throws an exception, connections aren't closed.</span></span> <span data-ttu-id="66d3c-202">依預設，會將 SignalR 一般錯誤訊息傳回給用戶端。</span><span class="sxs-lookup"><span data-stu-id="66d3c-202">By default, SignalR returns a generic error message to the client.</span></span> <span data-ttu-id="66d3c-203">例如：</span><span class="sxs-lookup"><span data-stu-id="66d3c-203">For example:</span></span>

```
Microsoft.AspNetCore.SignalR.HubException: An unexpected error occurred invoking 'MethodName' on the server.
```

<span data-ttu-id="66d3c-204">非預期的例外狀況通常包含敏感性資訊，例如資料庫連接失敗時所觸發之例外狀況中的資料庫伺服器名稱。</span><span class="sxs-lookup"><span data-stu-id="66d3c-204">Unexpected exceptions often contain sensitive information, such as the name of a database server in an exception triggered when the database connection fails.</span></span> <span data-ttu-id="66d3c-205">SignalR 預設不會公開這些詳細的錯誤訊息做為安全性措施。</span><span class="sxs-lookup"><span data-stu-id="66d3c-205">SignalR doesn't expose these detailed error messages by default as a security measure.</span></span> <span data-ttu-id="66d3c-206">如需隱藏例外狀況詳細資料的詳細資訊，請參閱 [安全性考慮文章](xref:signalr/security#exceptions) 。</span><span class="sxs-lookup"><span data-stu-id="66d3c-206">See the [Security considerations article](xref:signalr/security#exceptions) for more information on why exception details are suppressed.</span></span>

<span data-ttu-id="66d3c-207">如果 *您有想要* 傳播至用戶端的例外狀況，您可以使用 `HubException` 類別。</span><span class="sxs-lookup"><span data-stu-id="66d3c-207">If you have an exceptional condition you *do* want to propagate to the client, you can use the `HubException` class.</span></span> <span data-ttu-id="66d3c-208">如果您 `HubException` 從中樞方法擲回，則 SignalR **會將** 整個訊息傳送給用戶端（未經修改）。</span><span class="sxs-lookup"><span data-stu-id="66d3c-208">If you throw a `HubException` from your hub method, SignalR **will** send the entire message to the client, unmodified.</span></span>

[!code-csharp[ThrowHubException](hubs/sample/hubs/chathub.cs?name=ThrowHubException&highlight=3)]

> [!NOTE]
> <span data-ttu-id="66d3c-209">SignalR 只會將 `Message` 例外狀況的屬性傳送至用戶端。</span><span class="sxs-lookup"><span data-stu-id="66d3c-209">SignalR only sends the `Message` property of the exception to the client.</span></span> <span data-ttu-id="66d3c-210">例外狀況的堆疊追蹤和其他屬性無法供用戶端使用。</span><span class="sxs-lookup"><span data-stu-id="66d3c-210">The stack trace and other properties on the exception aren't available to the client.</span></span>

## <a name="related-resources"></a><span data-ttu-id="66d3c-211">相關資源</span><span class="sxs-lookup"><span data-stu-id="66d3c-211">Related resources</span></span>

* [<span data-ttu-id="66d3c-212">ASP.NET Core 簡介 SignalR</span><span class="sxs-lookup"><span data-stu-id="66d3c-212">Intro to ASP.NET Core SignalR</span></span>](xref:signalr/introduction)
* [<span data-ttu-id="66d3c-213">JavaScript 用戶端</span><span class="sxs-lookup"><span data-stu-id="66d3c-213">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="66d3c-214">發佈至 Azure</span><span class="sxs-lookup"><span data-stu-id="66d3c-214">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
