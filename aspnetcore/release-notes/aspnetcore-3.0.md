---
title: ASP.NET Core 3.0 的新功能
author: rick-anderson
description: 瞭解 ASP.NET Core 3.0 中的新功能。
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: aspnetcore-3.0
ms.openlocfilehash: fe68c54ff16751058a3eeee3880a11657344c40a
ms.sourcegitcommit: acfe51c35497a204f75c2a61125c9408c04493e6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102605668"
---
# <a name="whats-new-in-aspnet-core-30"></a><span data-ttu-id="a59a1-103">ASP.NET Core 3.0 的新功能</span><span class="sxs-lookup"><span data-stu-id="a59a1-103">What's new in ASP.NET Core 3.0</span></span>

<span data-ttu-id="a59a1-104">本文強調 ASP.NET Core 3.0 中最重要的變更，以及相關檔的連結。</span><span class="sxs-lookup"><span data-stu-id="a59a1-104">This article highlights the most significant changes in ASP.NET Core 3.0 with links to relevant documentation.</span></span>

## Blazor

<span data-ttu-id="a59a1-105">Blazor 是 ASP.NET Core 中的新架構，可使用 .NET 建立互動式用戶端 web UI：</span><span class="sxs-lookup"><span data-stu-id="a59a1-105">Blazor is a new framework in ASP.NET Core for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="a59a1-106">使用 C# 而不是 JavaScript 來建立豐富的互動式 UI。</span><span class="sxs-lookup"><span data-stu-id="a59a1-106">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="a59a1-107">共用以 .NET 撰寫的伺服器端與用戶端應用程式邏輯。</span><span class="sxs-lookup"><span data-stu-id="a59a1-107">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="a59a1-108">將 UI 轉譯為 HTML 和 CSS 以支援寬瀏覽器，包括行動裝置瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="a59a1-108">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>

<span data-ttu-id="a59a1-109">Blazor 架構支援的案例：</span><span class="sxs-lookup"><span data-stu-id="a59a1-109">Blazor framework supported scenarios:</span></span>

* <span data-ttu-id="a59a1-110">可重複使用的 UI 元件 (Razor 元件) </span><span class="sxs-lookup"><span data-stu-id="a59a1-110">Reusable UI components (Razor components)</span></span>
* <span data-ttu-id="a59a1-111">用戶端路由</span><span class="sxs-lookup"><span data-stu-id="a59a1-111">Client-side routing</span></span>
* <span data-ttu-id="a59a1-112">元件版面配置</span><span class="sxs-lookup"><span data-stu-id="a59a1-112">Component layouts</span></span>
* <span data-ttu-id="a59a1-113">支援相依性插入</span><span class="sxs-lookup"><span data-stu-id="a59a1-113">Support for dependency injection</span></span>
* <span data-ttu-id="a59a1-114">表單和驗證</span><span class="sxs-lookup"><span data-stu-id="a59a1-114">Forms and validation</span></span>
* <span data-ttu-id="a59a1-115">使用類別庫建立元件程式庫 Razor</span><span class="sxs-lookup"><span data-stu-id="a59a1-115">Build component libraries with Razor class libraries</span></span>
* <span data-ttu-id="a59a1-116">JavaScript Interop</span><span class="sxs-lookup"><span data-stu-id="a59a1-116">JavaScript interop</span></span>

<span data-ttu-id="a59a1-117">如需詳細資訊，請參閱<xref:blazor/index>。</span><span class="sxs-lookup"><span data-stu-id="a59a1-117">For more information, see <xref:blazor/index>.</span></span>

### Blazor Server

<span data-ttu-id="a59a1-118">Blazor 將元件轉譯邏輯與應用程式 UI 更新的套用方式分離出來。</span><span class="sxs-lookup"><span data-stu-id="a59a1-118">Blazor decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="a59a1-119">Blazor Server 提供在 Razor ASP.NET Core 應用程式的伺服器上裝載元件的支援。</span><span class="sxs-lookup"><span data-stu-id="a59a1-119">Blazor Server provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="a59a1-120">UI 更新會透過連接來處理 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="a59a1-120">UI updates are handled over a SignalR connection.</span></span> <span data-ttu-id="a59a1-121">Blazor Server ASP.NET Core 3.0 支援。</span><span class="sxs-lookup"><span data-stu-id="a59a1-121">Blazor Server is supported in ASP.NET Core 3.0.</span></span>

### <a name="blazor-webassembly-preview"></a><span data-ttu-id="a59a1-122">Blazor WebAssembly (預覽)</span><span class="sxs-lookup"><span data-stu-id="a59a1-122">Blazor WebAssembly (Preview)</span></span>

<span data-ttu-id="a59a1-123">Blazor 您也可以使用以 WebAssembly 為基礎的 .NET 執行時間，直接在瀏覽器中執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="a59a1-123">Blazor apps can also be run directly in the browser using a WebAssembly-based .NET runtime.</span></span> <span data-ttu-id="a59a1-124">Blazor WebAssembly 處於預覽狀態，且在 ASP.NET Core 3.0 中 *不* 受支援。</span><span class="sxs-lookup"><span data-stu-id="a59a1-124">Blazor WebAssembly is in preview and *not* supported in ASP.NET Core 3.0.</span></span> <span data-ttu-id="a59a1-125">Blazor WebAssembly 未來的 ASP.NET Core 版本將會支援。</span><span class="sxs-lookup"><span data-stu-id="a59a1-125">Blazor WebAssembly will be supported in a future release of ASP.NET Core.</span></span>

### <a name="razor-components"></a><span data-ttu-id="a59a1-126">Razor 元件</span><span class="sxs-lookup"><span data-stu-id="a59a1-126">Razor components</span></span>

<span data-ttu-id="a59a1-127">Blazor 應用程式是從元件建立的。</span><span class="sxs-lookup"><span data-stu-id="a59a1-127">Blazor apps are built from components.</span></span> <span data-ttu-id="a59a1-128">元件是使用者介面 (UI) 的獨立區塊，例如頁面、對話方塊或表單。</span><span class="sxs-lookup"><span data-stu-id="a59a1-128">Components are self-contained chunks of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="a59a1-129">元件是標準的 .NET 類別，可定義 UI 轉譯邏輯和用戶端事件處理常式。</span><span class="sxs-lookup"><span data-stu-id="a59a1-129">Components are normal .NET classes that define UI rendering logic and client-side event handlers.</span></span> <span data-ttu-id="a59a1-130">您可以建立豐富的互動式 web 應用程式，而不需要 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="a59a1-130">You can create rich interactive web apps without JavaScript.</span></span>

<span data-ttu-id="a59a1-131">中的元件 Blazor 通常是使用 Razor HTML 和 c # 的自然混合語法來撰寫。</span><span class="sxs-lookup"><span data-stu-id="a59a1-131">Components in Blazor are typically authored using Razor syntax, a natural blend of HTML and C#.</span></span> <span data-ttu-id="a59a1-132">Razor 元件類似于 Razor 頁面和 MVC 視圖，它們都使用它們 Razor 。</span><span class="sxs-lookup"><span data-stu-id="a59a1-132">Razor components are similar to Razor Pages and MVC views in that they both use Razor.</span></span> <span data-ttu-id="a59a1-133">不同于根據要求-回應模型的頁面和流覽，元件是專門用來處理 UI 組合。</span><span class="sxs-lookup"><span data-stu-id="a59a1-133">Unlike pages and views, which are based on a request-response model, components are used specifically for handling UI composition.</span></span>

## <a name="grpc"></a><span data-ttu-id="a59a1-134">gRPC</span><span class="sxs-lookup"><span data-stu-id="a59a1-134">gRPC</span></span>

<span data-ttu-id="a59a1-135">[gRPC](https://grpc.io/)：</span><span class="sxs-lookup"><span data-stu-id="a59a1-135">[gRPC](https://grpc.io/):</span></span>

* <span data-ttu-id="a59a1-136">是受歡迎的高效能 RPC (遠端程序呼叫) framework。</span><span class="sxs-lookup"><span data-stu-id="a59a1-136">Is a popular, high-performance RPC (remote procedure call) framework.</span></span>
* <span data-ttu-id="a59a1-137">提供固定合約優先的 API 開發方法。</span><span class="sxs-lookup"><span data-stu-id="a59a1-137">Offers an opinionated contract-first approach to API development.</span></span>
* <span data-ttu-id="a59a1-138">使用新式技術，例如：</span><span class="sxs-lookup"><span data-stu-id="a59a1-138">Uses modern technologies such as:</span></span>

  * <span data-ttu-id="a59a1-139">傳輸的 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="a59a1-139">HTTP/2 for transport.</span></span>
  * <span data-ttu-id="a59a1-140">通訊協定緩衝區作為介面描述語言。</span><span class="sxs-lookup"><span data-stu-id="a59a1-140">Protocol Buffers as the interface description language.</span></span>
  * <span data-ttu-id="a59a1-141">二進位序列化格式。</span><span class="sxs-lookup"><span data-stu-id="a59a1-141">Binary serialization format.</span></span>
* <span data-ttu-id="a59a1-142">提供下列功能：</span><span class="sxs-lookup"><span data-stu-id="a59a1-142">Provides features such as:</span></span>

  * <span data-ttu-id="a59a1-143">驗證</span><span class="sxs-lookup"><span data-stu-id="a59a1-143">Authentication</span></span>
  * <span data-ttu-id="a59a1-144">雙向串流和流量控制。</span><span class="sxs-lookup"><span data-stu-id="a59a1-144">Bidirectional streaming and flow control.</span></span>
  * <span data-ttu-id="a59a1-145">取消和超時。</span><span class="sxs-lookup"><span data-stu-id="a59a1-145">Cancellation and timeouts.</span></span>

<span data-ttu-id="a59a1-146">ASP.NET Core 3.0 中的 gRPC 功能包括：</span><span class="sxs-lookup"><span data-stu-id="a59a1-146">gRPC functionality in ASP.NET Core 3.0 includes:</span></span>

* <span data-ttu-id="a59a1-147">[Grpc. AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore)：用於裝載 Grpc 服務的 ASP.NET 核心架構。</span><span class="sxs-lookup"><span data-stu-id="a59a1-147">[Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore): An ASP.NET Core framework for hosting gRPC services.</span></span> <span data-ttu-id="a59a1-148">ASP.NET Core 上的 gRPC 與標準 ASP.NET 核心功能整合，例如記錄、相依性插入 (DI) 、驗證和授權。</span><span class="sxs-lookup"><span data-stu-id="a59a1-148">gRPC on ASP.NET Core integrates with standard ASP.NET Core features like logging, dependency injection (DI), authentication, and authorization.</span></span>
* <span data-ttu-id="a59a1-149">[Grpc .net. 用戶端](https://www.nuget.org/packages/Grpc.Net.Client)：適用于 .net Core 的 Grpc 用戶端，以熟悉的為基礎 `HttpClient` 。</span><span class="sxs-lookup"><span data-stu-id="a59a1-149">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client): A gRPC client for .NET Core that builds upon the familiar `HttpClient`.</span></span>
* <span data-ttu-id="a59a1-150">[Grpc .net. ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory)： Grpc 用戶端與的整合 `HttpClientFactory` 。</span><span class="sxs-lookup"><span data-stu-id="a59a1-150">[Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory): gRPC client integration with `HttpClientFactory`.</span></span>

<span data-ttu-id="a59a1-151">如需詳細資訊，請參閱<xref:grpc/index>。</span><span class="sxs-lookup"><span data-stu-id="a59a1-151">For more information, see <xref:grpc/index>.</span></span>

## SignalR

<span data-ttu-id="a59a1-152">請參閱 [更新程式 SignalR 代碼](xref:migration/22-to-30#signalr) 以取得遷移指示。</span><span class="sxs-lookup"><span data-stu-id="a59a1-152">See [Update SignalR code](xref:migration/22-to-30#signalr) for migration instructions.</span></span> <span data-ttu-id="a59a1-153">SignalR 現在會使用 `System.Text.Json` 來序列化/還原序列化 JSON 訊息。</span><span class="sxs-lookup"><span data-stu-id="a59a1-153">SignalR now uses `System.Text.Json` to serialize/deserialize JSON messages.</span></span> <span data-ttu-id="a59a1-154">如需還原序列化程式的指示，請參閱 [切換至 Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson) `Newtonsoft.Json` 。</span><span class="sxs-lookup"><span data-stu-id="a59a1-154">See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson) for instructions to restore the `Newtonsoft.Json`-based serializer.</span></span>

<span data-ttu-id="a59a1-155">在的 JavaScript 和 .NET 用戶端中 SignalR ，已針對自動重新連接新增了支援。</span><span class="sxs-lookup"><span data-stu-id="a59a1-155">In the JavaScript and .NET Clients for SignalR, support was added for automatic reconnection.</span></span> <span data-ttu-id="a59a1-156">根據預設，用戶端會立即嘗試重新連線，並在2、10和30秒後重試（如有需要）。</span><span class="sxs-lookup"><span data-stu-id="a59a1-156">By default, the client tries to reconnect immediately and retry after 2, 10, and 30 seconds if necessary.</span></span> <span data-ttu-id="a59a1-157">如果用戶端成功重新連接，則會收到新的連接識別碼。</span><span class="sxs-lookup"><span data-stu-id="a59a1-157">If the client successfully reconnects, it receives a new connection ID.</span></span> <span data-ttu-id="a59a1-158">自動重新連線是加入宣告：</span><span class="sxs-lookup"><span data-stu-id="a59a1-158">Automatic reconnect is opt-in:</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="a59a1-159">您可以藉由傳遞以毫秒為基礎的持續時間陣列來指定重新連接間隔：</span><span class="sxs-lookup"><span data-stu-id="a59a1-159">The reconnection intervals can be specified by passing an array of millisecond-based durations:</span></span>

```javascript
.withAutomaticReconnect([0, 3000, 5000, 10000, 15000, 30000])
//.withAutomaticReconnect([0, 2000, 10000, 30000]) The default intervals.
```

<span data-ttu-id="a59a1-160">您可以傳入自訂的實作為重新連接間隔的完整控制。</span><span class="sxs-lookup"><span data-stu-id="a59a1-160">A custom implementation can be passed in for full control of the reconnection intervals.</span></span>

<span data-ttu-id="a59a1-161">如果在上次重新連接間隔後重新連線失敗：</span><span class="sxs-lookup"><span data-stu-id="a59a1-161">If the reconnection fails after the last reconnect interval:</span></span>

* <span data-ttu-id="a59a1-162">用戶端會將連接視為離線。</span><span class="sxs-lookup"><span data-stu-id="a59a1-162">The client considers the connection is offline.</span></span>
* <span data-ttu-id="a59a1-163">用戶端停止嘗試重新連接。</span><span class="sxs-lookup"><span data-stu-id="a59a1-163">The client stops trying to reconnect.</span></span>

<span data-ttu-id="a59a1-164">在重新連線嘗試期間，請更新應用程式 UI，以通知使用者正在嘗試重新連接。</span><span class="sxs-lookup"><span data-stu-id="a59a1-164">During reconnection attempts, update the app UI to notify the user that the reconnection is being attempted.</span></span>

<span data-ttu-id="a59a1-165">為了在連接中斷時提供 UI 回饋， SignalR 用戶端 API 已擴充為包含下列事件處理常式：</span><span class="sxs-lookup"><span data-stu-id="a59a1-165">To provide UI feedback when the connection is interrupted, the SignalR client API has been expanded to include the following event handlers:</span></span>

* <span data-ttu-id="a59a1-166">`onreconnecting`：讓開發人員有機會停用 UI，或讓使用者知道應用程式已離線。</span><span class="sxs-lookup"><span data-stu-id="a59a1-166">`onreconnecting`:  Gives developers an opportunity to disable UI or to let users know the app is offline.</span></span>
* <span data-ttu-id="a59a1-167">`onreconnected`：讓開發人員有機會在連接重新建立之後更新 UI。</span><span class="sxs-lookup"><span data-stu-id="a59a1-167">`onreconnected`: Gives developers an opportunity to update the UI once the connection is reestablished.</span></span>

<span data-ttu-id="a59a1-168">下列程式碼會在 `onreconnecting` 嘗試連接時使用來更新 UI：</span><span class="sxs-lookup"><span data-stu-id="a59a1-168">The following code uses `onreconnecting` to update the UI while trying to connect:</span></span>

```javascript
connection.onreconnecting((error) => {
    const status = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messageInput").disabled = true;
    document.getElementById("sendButton").disabled = true;
    document.getElementById("connectionStatus").innerText = status;
});
```

<span data-ttu-id="a59a1-169">下列程式碼會使用 `onreconnected` 來更新連接上的 UI：</span><span class="sxs-lookup"><span data-stu-id="a59a1-169">The following code uses `onreconnected` to update the UI on connection:</span></span>

```javascript
connection.onreconnected((connectionId) => {
    const status = `Connection reestablished. Connected.`;
    document.getElementById("messageInput").disabled = false;
    document.getElementById("sendButton").disabled = false;
    document.getElementById("connectionStatus").innerText = status;
});
```

<span data-ttu-id="a59a1-170">SignalR 當中樞方法需要授權時，3.0 和更新版本會提供自訂資源給授權處理常式。</span><span class="sxs-lookup"><span data-stu-id="a59a1-170">SignalR 3.0 and later provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="a59a1-171">資源是的實例 `HubInvocationContext` 。</span><span class="sxs-lookup"><span data-stu-id="a59a1-171">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="a59a1-172">`HubInvocationContext`包括：</span><span class="sxs-lookup"><span data-stu-id="a59a1-172">The `HubInvocationContext` includes the:</span></span>

* `HubCallerContext`
* <span data-ttu-id="a59a1-173">正在叫用之中樞方法的名稱。</span><span class="sxs-lookup"><span data-stu-id="a59a1-173">Name of the hub method being invoked.</span></span>
* <span data-ttu-id="a59a1-174">中樞方法的引數。</span><span class="sxs-lookup"><span data-stu-id="a59a1-174">Arguments to the hub method.</span></span>

<span data-ttu-id="a59a1-175">請考慮下列聊天室應用程式範例，讓多個組織能夠透過 Azure Active Directory 登入。</span><span class="sxs-lookup"><span data-stu-id="a59a1-175">Consider the following example of a chat room app allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="a59a1-176">具有 Microsoft 帳戶的任何人都可以登入聊天，但只有擁有組織的成員可以禁止使用者或查看使用者的聊天記錄。</span><span class="sxs-lookup"><span data-stu-id="a59a1-176">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization can ban users or view users' chat histories.</span></span> <span data-ttu-id="a59a1-177">應用程式可能會限制特定使用者的特定功能。</span><span class="sxs-lookup"><span data-stu-id="a59a1-177">The app could restrict certain functionality from specific users.</span></span>

```csharp
public class DomainRestrictedRequirement :
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>,
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement,
        HubInvocationContext resource)
    {
        if (context.User?.Identity?.Name == null)
        {
            return Task.CompletedTask;
        }

        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name))
        {
            context.Succeed(requirement);
        }

        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName, string currentUsername)
    {
        if (hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase))
        {
            return currentUsername.Equals("bob42@jabbr.net", StringComparison.OrdinalIgnoreCase);
        }

        return currentUsername.EndsWith("@jabbr.net", StringComparison.OrdinalIgnoreCase));
    }
}
```

<span data-ttu-id="a59a1-178">在上述程式碼中，是 `DomainRestrictedRequirement` 做為自訂的 `IAuthorizationRequirement` 。</span><span class="sxs-lookup"><span data-stu-id="a59a1-178">In the preceding code, `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="a59a1-179">由於 `HubInvocationContext` 資源參數會傳入，內部邏輯可以：</span><span class="sxs-lookup"><span data-stu-id="a59a1-179">Because the `HubInvocationContext` resource parameter is being passed in, the internal logic can:</span></span>

* <span data-ttu-id="a59a1-180">檢查正在呼叫中樞的內容。</span><span class="sxs-lookup"><span data-stu-id="a59a1-180">Inspect the context in which the Hub is being called.</span></span>
* <span data-ttu-id="a59a1-181">決定是否要讓使用者執行個別的中樞方法。</span><span class="sxs-lookup"><span data-stu-id="a59a1-181">Make decisions on allowing the user to execute individual Hub methods.</span></span>

<span data-ttu-id="a59a1-182">個別的中樞方法可以用程式碼在執行時間檢查的原則名稱來標記。</span><span class="sxs-lookup"><span data-stu-id="a59a1-182">Individual Hub methods can be marked with the name of the policy the code checks at run-time.</span></span> <span data-ttu-id="a59a1-183">當用戶端嘗試呼叫個別的中樞方法時， `DomainRestrictedRequirement` 處理常式會執行並控制對方法的存取。</span><span class="sxs-lookup"><span data-stu-id="a59a1-183">As clients attempt to call individual Hub methods, the `DomainRestrictedRequirement` handler runs and controls access to the methods.</span></span> <span data-ttu-id="a59a1-184">根據控制項的存取方式 `DomainRestrictedRequirement` ：</span><span class="sxs-lookup"><span data-stu-id="a59a1-184">Based on the way the `DomainRestrictedRequirement` controls access:</span></span>

* <span data-ttu-id="a59a1-185">所有登入的使用者都可以呼叫 `SendMessage` 方法。</span><span class="sxs-lookup"><span data-stu-id="a59a1-185">All logged-in users can call the `SendMessage` method.</span></span>
* <span data-ttu-id="a59a1-186">只有登入 `@jabbr.net` 電子郵件地址的使用者可以查看使用者的歷程記錄。</span><span class="sxs-lookup"><span data-stu-id="a59a1-186">Only users who have logged in with a `@jabbr.net` email address can view users' histories.</span></span>
* <span data-ttu-id="a59a1-187">只 `bob42@jabbr.net` 可禁止來自聊天室的使用者。</span><span class="sxs-lookup"><span data-stu-id="a59a1-187">Only `bob42@jabbr.net` can ban users from the chat room.</span></span>

```csharp
[Authorize]
public class ChatHub : Hub
{
    public void SendMessage(string message)
    {
    }

    [Authorize("DomainRestricted")]
    public void BanUser(string username)
    {
    }

    [Authorize("DomainRestricted")]
    public void ViewUserHistory(string username)
    {
    }
}
```

<span data-ttu-id="a59a1-188">建立 `DomainRestricted` 原則可能包括：</span><span class="sxs-lookup"><span data-stu-id="a59a1-188">Creating the `DomainRestricted` policy might involve:</span></span>

* <span data-ttu-id="a59a1-189">在 *Startup.cs* 中，加入新的原則。</span><span class="sxs-lookup"><span data-stu-id="a59a1-189">In *Startup.cs*, adding the new policy.</span></span>
* <span data-ttu-id="a59a1-190">將自訂 `DomainRestrictedRequirement` 需求提供為參數。</span><span class="sxs-lookup"><span data-stu-id="a59a1-190">Provide the custom `DomainRestrictedRequirement` requirement as a parameter.</span></span>
* <span data-ttu-id="a59a1-191">`DomainRestricted`向授權中介軟體註冊。</span><span class="sxs-lookup"><span data-stu-id="a59a1-191">Registering `DomainRestricted` with the authorization middleware.</span></span>

```csharp
services
    .AddAuthorization(options =>
    {
        options.AddPolicy("DomainRestricted", policy =>
        {
            policy.Requirements.Add(new DomainRestrictedRequirement());
        });
    });
```

<span data-ttu-id="a59a1-192">SignalR 中樞使用 [端點路由](xref:fundamentals/routing)。</span><span class="sxs-lookup"><span data-stu-id="a59a1-192">SignalR hubs use [Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="a59a1-193">SignalR 中樞連接先前已明確完成：</span><span class="sxs-lookup"><span data-stu-id="a59a1-193">SignalR hub connection was previously done explicitly:</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="a59a1-194">在先前的版本中，開發人員需要 Razor 在不同的地方連接控制器、頁面和中樞。</span><span class="sxs-lookup"><span data-stu-id="a59a1-194">In the previous version, developers needed to wire up controllers, Razor pages, and hubs in a variety of places.</span></span> <span data-ttu-id="a59a1-195">明確連接會產生一系列幾乎相同的路由區段：</span><span class="sxs-lookup"><span data-stu-id="a59a1-195">Explicit connection results in a series of nearly-identical routing segments:</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});

app.UseRouting(routes =>
{
    routes.MapRazorPages();
});
```

<span data-ttu-id="a59a1-196">SignalR 3.0 中樞可以透過端點路由來路由傳送。</span><span class="sxs-lookup"><span data-stu-id="a59a1-196">SignalR 3.0 hubs can be routed via endpoint routing.</span></span> <span data-ttu-id="a59a1-197">使用端點路由，通常可以在中設定所有路由 `UseRouting` ：</span><span class="sxs-lookup"><span data-stu-id="a59a1-197">With endpoint routing, typically all routing can be configured in `UseRouting`:</span></span>

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="a59a1-198">已新增 ASP.NET Core 3.0 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="a59a1-198">ASP.NET Core 3.0 SignalR added:</span></span>

<span data-ttu-id="a59a1-199">用戶端對伺服器串流處理。</span><span class="sxs-lookup"><span data-stu-id="a59a1-199">Client-to-server streaming.</span></span> <span data-ttu-id="a59a1-200">使用用戶端對伺服器串流時，伺服器端方法可以取得或的實例 `IAsyncEnumerable<T>` `ChannelReader<T>` 。</span><span class="sxs-lookup"><span data-stu-id="a59a1-200">With client-to-server streaming, server-side methods can take instances of either an `IAsyncEnumerable<T>` or `ChannelReader<T>`.</span></span> <span data-ttu-id="a59a1-201">在下列 c # 範例中， `UploadStream` 中樞上的方法會接收來自用戶端的字串資料流程：</span><span class="sxs-lookup"><span data-stu-id="a59a1-201">In the following C# sample, the `UploadStream` method on the Hub will receive a stream of strings from the client:</span></span>

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        // process content
    }
}
```

<span data-ttu-id="a59a1-202">.NET 用戶端應用程式可以傳遞 `IAsyncEnumerable<T>` 或 `ChannelReader<T>` 實例作為 `stream` `UploadStream` 上述中樞方法的引數。</span><span class="sxs-lookup"><span data-stu-id="a59a1-202">.NET client apps can pass either an `IAsyncEnumerable<T>` or `ChannelReader<T>` instance as the `stream` argument of the `UploadStream` Hub method above.</span></span>

<span data-ttu-id="a59a1-203">在 `for` 迴圈完成且區域函式結束之後，會傳送資料流程完成：</span><span class="sxs-lookup"><span data-stu-id="a59a1-203">After the `for` loop has completed and the local function exits, the stream completion is sent:</span></span>

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
}

await connection.SendAsync("UploadStream", clientStreamData());
```

<span data-ttu-id="a59a1-204">JavaScript 用戶端應用程式會 SignalR `Subject` 針對上述中樞方法的引數使用 (或 [RxJS 主體](https://rxjs.dev/api/index/class/Subject)) `stream` `UploadStream` 。</span><span class="sxs-lookup"><span data-stu-id="a59a1-204">JavaScript client apps use the SignalR `Subject` (or an [RxJS Subject](https://rxjs.dev/api/index/class/Subject)) for the `stream` argument of the `UploadStream` Hub method above.</span></span>

```javascript
let subject = new signalR.Subject();
await connection.send("StartStream", "MyAsciiArtStream", subject);
```

<span data-ttu-id="a59a1-205">JavaScript 程式碼可使用 `subject.next` 方法來處理已捕捉的字串，並準備好傳送至伺服器。</span><span class="sxs-lookup"><span data-stu-id="a59a1-205">The JavaScript code could use the `subject.next` method to handle strings as they are captured and ready to be sent to the server.</span></span>

```javascript
subject.next("example");
subject.complete();
```

<span data-ttu-id="a59a1-206">您可以使用類似上述兩個程式碼片段的程式碼，建立即時串流體驗。</span><span class="sxs-lookup"><span data-stu-id="a59a1-206">Using code like the two preceding snippets, real-time streaming experiences can be created.</span></span>

## <a name="new-json-serialization"></a><span data-ttu-id="a59a1-207">新的 JSON 序列化</span><span class="sxs-lookup"><span data-stu-id="a59a1-207">New JSON serialization</span></span>

<span data-ttu-id="a59a1-208">ASP.NET Core 3.0 現在預設會使用 <xref:System.Text.Json> JSON 序列化：</span><span class="sxs-lookup"><span data-stu-id="a59a1-208">ASP.NET Core 3.0 now uses <xref:System.Text.Json> by default for JSON serialization:</span></span>

* <span data-ttu-id="a59a1-209">以非同步方式讀取和寫入 JSON。</span><span class="sxs-lookup"><span data-stu-id="a59a1-209">Reads and writes JSON asynchronously.</span></span>
* <span data-ttu-id="a59a1-210">已針對 UTF-8 文字進行優化。</span><span class="sxs-lookup"><span data-stu-id="a59a1-210">Is optimized for UTF-8 text.</span></span>
* <span data-ttu-id="a59a1-211">通常比更高 `Newtonsoft.Json` 的效能。</span><span class="sxs-lookup"><span data-stu-id="a59a1-211">Typically higher performance than `Newtonsoft.Json`.</span></span>

<span data-ttu-id="a59a1-212">若要將 Json.NET 新增至 ASP.NET Core 3.0，請參閱 [新增 Newtonsoft.Js的 Json 格式支援](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support)。</span><span class="sxs-lookup"><span data-stu-id="a59a1-212">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span>

## <a name="new-razor-directives"></a><span data-ttu-id="a59a1-213">新的指示詞 Razor</span><span class="sxs-lookup"><span data-stu-id="a59a1-213">New Razor directives</span></span>

<span data-ttu-id="a59a1-214">下列清單包含新的指示詞 Razor ：</span><span class="sxs-lookup"><span data-stu-id="a59a1-214">The following list contains new Razor directives:</span></span>

* <span data-ttu-id="a59a1-215">[`@attribute`](xref:mvc/views/razor#attribute)：指示詞會將 `@attribute` 指定的屬性套用至所產生頁面或視圖的類別。</span><span class="sxs-lookup"><span data-stu-id="a59a1-215">[`@attribute`](xref:mvc/views/razor#attribute): The `@attribute` directive applies the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="a59a1-216">例如： `@attribute [Authorize]` 。</span><span class="sxs-lookup"><span data-stu-id="a59a1-216">For example, `@attribute [Authorize]`.</span></span>
* <span data-ttu-id="a59a1-217">[`@implements`](xref:mvc/views/razor#implements)：指示詞會 `@implements` 針對產生的類別實作為介面。</span><span class="sxs-lookup"><span data-stu-id="a59a1-217">[`@implements`](xref:mvc/views/razor#implements): The `@implements` directive implements an interface for the generated class.</span></span> <span data-ttu-id="a59a1-218">例如： `@implements IDisposable` 。</span><span class="sxs-lookup"><span data-stu-id="a59a1-218">For example, `@implements IDisposable`.</span></span>

## <a name="identityserver4-supports-authentication-and-authorization-for-web-apis-and-spas"></a><span data-ttu-id="a59a1-219">IdentityServer4 支援 web Api 和 Spa 的驗證和授權</span><span class="sxs-lookup"><span data-stu-id="a59a1-219">IdentityServer4 supports authentication and authorization for web APIs and SPAs</span></span>

<span data-ttu-id="a59a1-220">ASP.NET Core 3.0 在單一頁面應用程式中提供驗證 (Spa) 使用 web API 授權的支援。</span><span class="sxs-lookup"><span data-stu-id="a59a1-220">ASP.NET Core 3.0 offers authentication in Single Page Apps (SPAs) using the support for web API authorization.</span></span> <span data-ttu-id="a59a1-221">ASP.NET Core Identity針對驗證和儲存使用者，會結合[ Identity Server4](https://identityserver.io/)來執行 OpenID connect。</span><span class="sxs-lookup"><span data-stu-id="a59a1-221">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer4](https://identityserver.io/) for implementing OpenID Connect.</span></span>

<span data-ttu-id="a59a1-222">IdentityServer4 是適用于 ASP.NET Core 3.0 的 OpenID Connect 和 OAuth 2.0 架構。</span><span class="sxs-lookup"><span data-stu-id="a59a1-222">IdentityServer4 is an OpenID Connect and OAuth 2.0 framework for ASP.NET Core 3.0.</span></span> <span data-ttu-id="a59a1-223">它可啟用下列安全性功能：</span><span class="sxs-lookup"><span data-stu-id="a59a1-223">It enables the following security features:</span></span>

* <span data-ttu-id="a59a1-224">驗證即服務 (AaaS) </span><span class="sxs-lookup"><span data-stu-id="a59a1-224">Authentication as a Service (AaaS)</span></span>
* <span data-ttu-id="a59a1-225">單一登入/關閉 (SSO) 超過多個應用程式類型</span><span class="sxs-lookup"><span data-stu-id="a59a1-225">Single sign-on/off (SSO) over multiple application types</span></span>
* <span data-ttu-id="a59a1-226">Api 的存取控制</span><span class="sxs-lookup"><span data-stu-id="a59a1-226">Access control for APIs</span></span>
* <span data-ttu-id="a59a1-227">同盟閘道</span><span class="sxs-lookup"><span data-stu-id="a59a1-227">Federation Gateway</span></span>

<span data-ttu-id="a59a1-228">如需詳細資訊，請參閱 Spa [的 Identity Server4 檔](http://docs.identityserver.io/en/latest/index.html) 或 [驗證和授權](xref:security/authentication/identity/spa)。</span><span class="sxs-lookup"><span data-stu-id="a59a1-228">For more information, see [the IdentityServer4 documentation](http://docs.identityserver.io/en/latest/index.html) or [Authentication and authorization for SPAs](xref:security/authentication/identity/spa).</span></span>

## <a name="certificate-and-kerberos-authentication"></a><span data-ttu-id="a59a1-229">憑證和 Kerberos 驗證</span><span class="sxs-lookup"><span data-stu-id="a59a1-229">Certificate and Kerberos authentication</span></span>

<span data-ttu-id="a59a1-230">憑證驗證需要：</span><span class="sxs-lookup"><span data-stu-id="a59a1-230">Certificate authentication requires:</span></span>

* <span data-ttu-id="a59a1-231">設定伺服器接受憑證。</span><span class="sxs-lookup"><span data-stu-id="a59a1-231">Configuring the server to accept certificates.</span></span>
* <span data-ttu-id="a59a1-232">在中加入驗證中介軟體 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="a59a1-232">Adding the authentication middleware in `Startup.Configure`.</span></span>
* <span data-ttu-id="a59a1-233">在中新增憑證驗證服務 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="a59a1-233">Adding the certificate authentication service in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

<span data-ttu-id="a59a1-234">憑證驗證的選項包括下列功能：</span><span class="sxs-lookup"><span data-stu-id="a59a1-234">Options for certificate authentication include the ability to:</span></span>

* <span data-ttu-id="a59a1-235">接受自我簽署的憑證。</span><span class="sxs-lookup"><span data-stu-id="a59a1-235">Accept self-signed certificates.</span></span>
* <span data-ttu-id="a59a1-236">檢查憑證撤銷。</span><span class="sxs-lookup"><span data-stu-id="a59a1-236">Check for certificate revocation.</span></span>
* <span data-ttu-id="a59a1-237">檢查推出憑證是否有正確的使用方式旗標。</span><span class="sxs-lookup"><span data-stu-id="a59a1-237">Check that the proffered certificate has the right usage flags in it.</span></span>

<span data-ttu-id="a59a1-238">預設使用者主體是從憑證屬性所構成。</span><span class="sxs-lookup"><span data-stu-id="a59a1-238">A default user principal is constructed from the certificate properties.</span></span> <span data-ttu-id="a59a1-239">使用者主體包含可補充或取代主體的事件。</span><span class="sxs-lookup"><span data-stu-id="a59a1-239">The user principal contains an event that enables supplementing or replacing the principal.</span></span> <span data-ttu-id="a59a1-240">如需詳細資訊，請參閱<xref:security/authentication/certauth>。</span><span class="sxs-lookup"><span data-stu-id="a59a1-240">For more information, see <xref:security/authentication/certauth>.</span></span>

<span data-ttu-id="a59a1-241">[Windows 驗證](/windows-server/security/windows-authentication/windows-authentication-overview) 已延伸至 Linux 和 macOS。</span><span class="sxs-lookup"><span data-stu-id="a59a1-241">[Windows Authentication](/windows-server/security/windows-authentication/windows-authentication-overview) has been extended onto Linux and macOS.</span></span> <span data-ttu-id="a59a1-242">在先前的版本中，Windows 驗證僅限於 [IIS](xref:host-and-deploy/iis/index) 和 [HTTP.sys](xref:fundamentals/servers/httpsys)。</span><span class="sxs-lookup"><span data-stu-id="a59a1-242">In previous versions, Windows Authentication was limited to [IIS](xref:host-and-deploy/iis/index) and [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span> <span data-ttu-id="a59a1-243">在 ASP.NET Core 3.0 中， [Kestrel](xref:fundamentals/servers/kestrel) 能夠在 windows、Linux 和 macOS 上針對已加入網域的 windows 主機使用 Negotiate、 [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview)和 [NTLM](/windows-server/security/kerberos/ntlm-overview)。</span><span class="sxs-lookup"><span data-stu-id="a59a1-243">In ASP.NET Core 3.0, [Kestrel](xref:fundamentals/servers/kestrel) has the ability to use Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview), and [NTLM on Windows](/windows-server/security/kerberos/ntlm-overview), Linux, and macOS for Windows domain-joined hosts.</span></span> <span data-ttu-id="a59a1-244">這些驗證配置的 Kestrel 支援是由 AspNetCore 提供。 [Negotiate NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) 套件。</span><span class="sxs-lookup"><span data-stu-id="a59a1-244">Kestrel support of these authentication schemes is provided by the [Microsoft.AspNetCore.Authentication.Negotiate NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) package.</span></span> <span data-ttu-id="a59a1-245">如同其他驗證服務，請設定驗證應用程式範圍，然後設定服務：</span><span class="sxs-lookup"><span data-stu-id="a59a1-245">As with the other authentication services, configure authentication app wide, then configure the service:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
        .AddNegotiate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

<span data-ttu-id="a59a1-246">主機需求：</span><span class="sxs-lookup"><span data-stu-id="a59a1-246">Host requirements:</span></span>

* <span data-ttu-id="a59a1-247">Windows 主機必須有 [服務主體名稱](/windows/win32/ad/service-principal-names) (spn) 新增至裝載該應用程式的使用者帳戶。</span><span class="sxs-lookup"><span data-stu-id="a59a1-247">Windows hosts must have [Service Principal Names](/windows/win32/ad/service-principal-names) (SPNs) added to the user account hosting the app.</span></span>
* <span data-ttu-id="a59a1-248">Linux 和 macOS 電腦必須加入網域。</span><span class="sxs-lookup"><span data-stu-id="a59a1-248">Linux and macOS machines must be joined to the domain.</span></span>
  * <span data-ttu-id="a59a1-249">必須為 web 進程建立 Spn。</span><span class="sxs-lookup"><span data-stu-id="a59a1-249">SPNs must be created for the web process.</span></span>
  * <span data-ttu-id="a59a1-250">您必須在主機電腦上產生並設定[Keytab](/archive/blogs/pie/all-you-need-to-know-about-keytab-files)檔案。</span><span class="sxs-lookup"><span data-stu-id="a59a1-250">[Keytab files](/archive/blogs/pie/all-you-need-to-know-about-keytab-files) must be generated and configured on the host machine.</span></span>

<span data-ttu-id="a59a1-251">如需詳細資訊，請參閱<xref:security/authentication/windowsauth>。</span><span class="sxs-lookup"><span data-stu-id="a59a1-251">For more information, see <xref:security/authentication/windowsauth>.</span></span>

## <a name="template-changes"></a><span data-ttu-id="a59a1-252">範本變更</span><span class="sxs-lookup"><span data-stu-id="a59a1-252">Template changes</span></span>

<span data-ttu-id="a59a1-253">Web UI 範本 (Razor 頁面、具有控制器和 views 的 MVC) 移除下列各項：</span><span class="sxs-lookup"><span data-stu-id="a59a1-253">The web UI templates (Razor Pages, MVC with controller and views) have the following removed:</span></span>

* <span data-ttu-id="a59a1-254">cookie已不再包含同意 UI。</span><span class="sxs-lookup"><span data-stu-id="a59a1-254">The cookie consent UI is no longer included.</span></span> <span data-ttu-id="a59a1-255">若要 cookie 在 ASP.NET Core 3.0 範本產生的應用程式中啟用同意功能，請參閱 <xref:security/gdpr> 。</span><span class="sxs-lookup"><span data-stu-id="a59a1-255">To enable the cookie consent feature in an ASP.NET Core 3.0 template-generated app, see <xref:security/gdpr>.</span></span>
* <span data-ttu-id="a59a1-256">腳本和相關靜態資產現在會以本機檔案的形式來參考，而不是使用 Cdn。</span><span class="sxs-lookup"><span data-stu-id="a59a1-256">Scripts and related static assets are now referenced as local files instead of using CDNs.</span></span> <span data-ttu-id="a59a1-257">如需詳細資訊，請參閱 [腳本和相關靜態資產現在會以本機檔案的形式來參考，而不是根據目前的環境 (dotnet/AspNetCore.Docs #14350) 使用 cdn ](https://github.com/dotnet/AspNetCore.Docs/issues/14350)。</span><span class="sxs-lookup"><span data-stu-id="a59a1-257">For more information, see [Scripts and related static assets are now referenced as local files instead of using CDNs based on the current environment (dotnet/AspNetCore.Docs #14350)](https://github.com/dotnet/AspNetCore.Docs/issues/14350).</span></span>

<span data-ttu-id="a59a1-258">角度範本已更新為使用角度8。</span><span class="sxs-lookup"><span data-stu-id="a59a1-258">The Angular template updated to use Angular 8.</span></span>

<span data-ttu-id="a59a1-259">Razor依預設，類別庫 (RCL) 範本預設為 Razor 元件開發。</span><span class="sxs-lookup"><span data-stu-id="a59a1-259">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="a59a1-260">Visual Studio 中的新範本選項提供頁面和視圖的範本支援。</span><span class="sxs-lookup"><span data-stu-id="a59a1-260">A new template option in Visual Studio provides template support for pages and views.</span></span> <span data-ttu-id="a59a1-261">從命令介面中的範本建立 RCL 時，請將選項傳遞 `--support-pages-and-views` (`dotnet new razorclasslib --support-pages-and-views`) 。</span><span class="sxs-lookup"><span data-stu-id="a59a1-261">When creating an RCL from the template in a command shell, pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`).</span></span>

## <a name="generic-host"></a><span data-ttu-id="a59a1-262">一般主機</span><span class="sxs-lookup"><span data-stu-id="a59a1-262">Generic Host</span></span>

<span data-ttu-id="a59a1-263">ASP.NET Core 3.0 範本使用 <xref:fundamentals/host/generic-host> 。</span><span class="sxs-lookup"><span data-stu-id="a59a1-263">The ASP.NET Core 3.0 templates use <xref:fundamentals/host/generic-host>.</span></span> <span data-ttu-id="a59a1-264">使用舊版 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> 。</span><span class="sxs-lookup"><span data-stu-id="a59a1-264">Previous versions used <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>.</span></span> <span data-ttu-id="a59a1-265">使用 .NET Core 泛型主機 (<xref:Microsoft.Extensions.Hosting.HostBuilder>) 可讓 ASP.NET Core 應用程式與其他不是 web 特定的伺服器案例更緊密地整合。</span><span class="sxs-lookup"><span data-stu-id="a59a1-265">Using the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) provides better integration of ASP.NET Core apps with other server scenarios that aren't web-specific.</span></span> <span data-ttu-id="a59a1-266">如需詳細資訊，請參閱 [HostBuilder 取代 >webhostbuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder)。</span><span class="sxs-lookup"><span data-stu-id="a59a1-266">For more information, see [HostBuilder replaces WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).</span></span>

### <a name="host-configuration"></a><span data-ttu-id="a59a1-267">主機組態</span><span class="sxs-lookup"><span data-stu-id="a59a1-267">Host configuration</span></span>

<span data-ttu-id="a59a1-268">在 ASP.NET Core 3.0 發行之前，會針對 Web 主機的主機設定載入前面加上的環境變數 `ASPNETCORE_` 。</span><span class="sxs-lookup"><span data-stu-id="a59a1-268">Prior to the release of ASP.NET Core 3.0, environment variables prefixed with `ASPNETCORE_` were loaded for host configuration of the Web Host.</span></span> <span data-ttu-id="a59a1-269">在3.0 中， `AddEnvironmentVariables` 是用來載入前面加上的環境變數，以 `DOTNET_` 供主機設定使用 `CreateDefaultBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="a59a1-269">In 3.0, `AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for host configuration with `CreateDefaultBuilder`.</span></span>

### <a name="changes-to-startup-constructor-injection"></a><span data-ttu-id="a59a1-270">啟動函式插入的變更</span><span class="sxs-lookup"><span data-stu-id="a59a1-270">Changes to Startup constructor injection</span></span>

<span data-ttu-id="a59a1-271">泛型主機僅支援下列類型的函式 `Startup` 插入：</span><span class="sxs-lookup"><span data-stu-id="a59a1-271">The Generic Host only supports the following types for `Startup` constructor injection:</span></span>

* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="a59a1-272">所有服務仍然可以直接插入至方法的引數 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="a59a1-272">All services can still be injected directly as arguments to the `Startup.Configure` method.</span></span> <span data-ttu-id="a59a1-273">如需詳細資訊，請參閱 [泛型主機限制啟動的函式插入 (aspnet/公告 #353) ](https://github.com/aspnet/Announcements/issues/353)。</span><span class="sxs-lookup"><span data-stu-id="a59a1-273">For more information, see [Generic Host restricts Startup constructor injection (aspnet/Announcements #353)](https://github.com/aspnet/Announcements/issues/353).</span></span>

## <a name="kestrel"></a><span data-ttu-id="a59a1-274">Kestrel</span><span class="sxs-lookup"><span data-stu-id="a59a1-274">Kestrel</span></span>

* <span data-ttu-id="a59a1-275">Kestrel 設定已更新，可遷移至一般主機。</span><span class="sxs-lookup"><span data-stu-id="a59a1-275">Kestrel configuration has been updated for the migration to the Generic Host.</span></span> <span data-ttu-id="a59a1-276">在3.0 中，Kestrel 是在提供的 web 主機建立器上進行設定 `ConfigureWebHostDefaults` 。</span><span class="sxs-lookup"><span data-stu-id="a59a1-276">In 3.0, Kestrel is configured on the web host builder provided by `ConfigureWebHostDefaults`.</span></span>
* <span data-ttu-id="a59a1-277">連接介面卡已從 Kestrel 中移除，並以連線中介軟體取代，這類似于 ASP.NET 核心管線中的 HTTP 中介軟體，但適用于較低層級的連接。</span><span class="sxs-lookup"><span data-stu-id="a59a1-277">Connection Adapters have been removed from Kestrel and replaced with Connection Middleware, which is similar to HTTP Middleware in the ASP.NET Core pipeline but for lower-level connections.</span></span>
* <span data-ttu-id="a59a1-278">Kestrel 傳輸層已公開為中的公用介面 `Connections.Abstractions` 。</span><span class="sxs-lookup"><span data-stu-id="a59a1-278">The Kestrel transport layer has been exposed as a public interface in `Connections.Abstractions`.</span></span>
* <span data-ttu-id="a59a1-279">將尾端標頭移至新的集合，可解決標頭和結尾之間的不明確的問題。</span><span class="sxs-lookup"><span data-stu-id="a59a1-279">Ambiguity between headers and trailers has been resolved by moving trailing headers to a new collection.</span></span>
* <span data-ttu-id="a59a1-280">同步 i/o Api （例如 `HttpRequest.Body.Read` ）是造成應用程式當機的執行緒耗盡的常見來源。</span><span class="sxs-lookup"><span data-stu-id="a59a1-280">Synchronous I/O APIs, such as `HttpRequest.Body.Read`, are a common source of thread starvation leading to app crashes.</span></span> <span data-ttu-id="a59a1-281">在3.0 中， `AllowSynchronousIO` 預設為停用。</span><span class="sxs-lookup"><span data-stu-id="a59a1-281">In 3.0, `AllowSynchronousIO` is disabled by default.</span></span>

<span data-ttu-id="a59a1-282">如需詳細資訊，請參閱<xref:migration/22-to-30#kestrel>。</span><span class="sxs-lookup"><span data-stu-id="a59a1-282">For more information, see <xref:migration/22-to-30#kestrel>.</span></span>

## <a name="http2-enabled-by-default"></a><span data-ttu-id="a59a1-283">預設啟用的 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="a59a1-283">HTTP/2 enabled by default</span></span>

<span data-ttu-id="a59a1-284">HTTPS 端點的 Kestrel 預設會啟用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="a59a1-284">HTTP/2 is enabled by default in Kestrel for HTTPS endpoints.</span></span> <span data-ttu-id="a59a1-285">當作業系統支援時，會啟用 IIS 或 HTTP.sys 的 HTTP/2 支援。</span><span class="sxs-lookup"><span data-stu-id="a59a1-285">HTTP/2 support for IIS or HTTP.sys is enabled when supported by the operating system.</span></span>

## <a name="eventcounters-on-request"></a><span data-ttu-id="a59a1-286">依要求 EventCounters</span><span class="sxs-lookup"><span data-stu-id="a59a1-286">EventCounters on request</span></span>

<span data-ttu-id="a59a1-287">主控 EventSource，會 `Microsoft.AspNetCore.Hosting` 發出下列與連 <xref:System.Diagnostics.Tracing.EventCounter> 入要求相關的新類型：</span><span class="sxs-lookup"><span data-stu-id="a59a1-287">The Hosting EventSource, `Microsoft.AspNetCore.Hosting`, emits the following new <xref:System.Diagnostics.Tracing.EventCounter> types related to incoming requests:</span></span>

* `requests-per-second`
* `total-requests`
* `current-requests`
* `failed-requests`

## <a name="endpoint-routing"></a><span data-ttu-id="a59a1-288">端點路由</span><span class="sxs-lookup"><span data-stu-id="a59a1-288">Endpoint routing</span></span>

<span data-ttu-id="a59a1-289">端點路由可讓架構 (例如，MVC) 可與中介軟體妥善搭配運作，以增強：</span><span class="sxs-lookup"><span data-stu-id="a59a1-289">Endpoint Routing, which allows frameworks (for example, MVC) to work well with middleware, is enhanced:</span></span>

* <span data-ttu-id="a59a1-290">中介軟體和端點的順序可在的要求處理管線中進行設定 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="a59a1-290">The order of middleware and endpoints is configurable in the request processing pipeline of `Startup.Configure`.</span></span>
* <span data-ttu-id="a59a1-291">端點和中介軟體適用于其他 ASP.NET 核心技術，例如健康情況檢查。</span><span class="sxs-lookup"><span data-stu-id="a59a1-291">Endpoints and middleware compose well with other ASP.NET Core-based technologies, such as Health Checks.</span></span>
* <span data-ttu-id="a59a1-292">端點可以在中介軟體和 MVC 中執行原則，例如 CORS 或授權。</span><span class="sxs-lookup"><span data-stu-id="a59a1-292">Endpoints can implement a policy, such as CORS or authorization, in both middleware and MVC.</span></span>
* <span data-ttu-id="a59a1-293">篩選和屬性可以放在控制器中的方法上。</span><span class="sxs-lookup"><span data-stu-id="a59a1-293">Filters and attributes can be placed on methods in controllers.</span></span>

<span data-ttu-id="a59a1-294">如需詳細資訊，請參閱<xref:fundamentals/routing#routing-basics>。</span><span class="sxs-lookup"><span data-stu-id="a59a1-294">For more information, see <xref:fundamentals/routing#routing-basics>.</span></span>

## <a name="health-checks"></a><span data-ttu-id="a59a1-295">健康情況檢查</span><span class="sxs-lookup"><span data-stu-id="a59a1-295">Health Checks</span></span>

<span data-ttu-id="a59a1-296">健康情況檢查會使用端點路由搭配一般主機。</span><span class="sxs-lookup"><span data-stu-id="a59a1-296">Health Checks use endpoint routing with the Generic Host.</span></span> <span data-ttu-id="a59a1-297">在中 `Startup.Configure` ， `MapHealthChecks` 使用端點 URL 或相對路徑呼叫端點產生器：</span><span class="sxs-lookup"><span data-stu-id="a59a1-297">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

<span data-ttu-id="a59a1-298">健康情況檢查端點可以：</span><span class="sxs-lookup"><span data-stu-id="a59a1-298">Health Checks endpoints can:</span></span>

* <span data-ttu-id="a59a1-299">指定一或多個允許的主機/埠。</span><span class="sxs-lookup"><span data-stu-id="a59a1-299">Specify one or more permitted hosts/ports.</span></span>
* <span data-ttu-id="a59a1-300">需要授權。</span><span class="sxs-lookup"><span data-stu-id="a59a1-300">Require authorization.</span></span>
* <span data-ttu-id="a59a1-301">需要 CORS。</span><span class="sxs-lookup"><span data-stu-id="a59a1-301">Require CORS.</span></span>

<span data-ttu-id="a59a1-302">如需詳細資訊，請參閱下列文章：</span><span class="sxs-lookup"><span data-stu-id="a59a1-302">For more information, see the following articles:</span></span>

* <xref:migration/22-to-30#health-checks>
* <xref:host-and-deploy/health-checks>

## <a name="pipes-on-httpcontext"></a><span data-ttu-id="a59a1-303">HttpCoNtext 上的管道</span><span class="sxs-lookup"><span data-stu-id="a59a1-303">Pipes on HttpContext</span></span>

<span data-ttu-id="a59a1-304">現在，您可以使用 API 來讀取要求本文，並撰寫回應主體 <xref:System.IO.Pipelines> 。</span><span class="sxs-lookup"><span data-stu-id="a59a1-304">It's now possible to read the request body and write the response body using the <xref:System.IO.Pipelines> API.</span></span> <span data-ttu-id="a59a1-305">必須提供</span><span class="sxs-lookup"><span data-stu-id="a59a1-305">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.HttpRequest.BodyReader> --> <span data-ttu-id="a59a1-306">`HttpRequest.BodyReader` 屬性提供 <xref:System.IO.Pipelines.PipeReader> 可用於讀取要求主體的。</span><span class="sxs-lookup"><span data-stu-id="a59a1-306">`HttpRequest.BodyReader` property provides a <xref:System.IO.Pipelines.PipeReader> that can be used to read the request body.</span></span> <span data-ttu-id="a59a1-307">必須提供</span><span class="sxs-lookup"><span data-stu-id="a59a1-307">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.> --> <span data-ttu-id="a59a1-308">`HttpResponse.BodyWriter` 屬性會提供 <xref:System.IO.Pipelines.PipeWriter> ，可用來寫入回應主體。</span><span class="sxs-lookup"><span data-stu-id="a59a1-308">`HttpResponse.BodyWriter` property provides a <xref:System.IO.Pipelines.PipeWriter> that can be used to write the response body.</span></span> <span data-ttu-id="a59a1-309">`HttpRequest.BodyReader` 是資料流程的類比 `HttpRequest.Body` 。</span><span class="sxs-lookup"><span data-stu-id="a59a1-309">`HttpRequest.BodyReader` is an analogue of the `HttpRequest.Body` stream.</span></span> <span data-ttu-id="a59a1-310">`HttpResponse.BodyWriter` 是資料流程的類比 `HttpResponse.Body` 。</span><span class="sxs-lookup"><span data-stu-id="a59a1-310">`HttpResponse.BodyWriter` is an analogue of the `HttpResponse.Body` stream.</span></span>

<!-- indirectly related, https://github.com/dotnet/docs/pull/14414 won't be published by 9/23  -->

## <a name="improved-error-reporting-in-iis"></a><span data-ttu-id="a59a1-311">改進 IIS 中的錯誤報表</span><span class="sxs-lookup"><span data-stu-id="a59a1-311">Improved error reporting in IIS</span></span>

<span data-ttu-id="a59a1-312">在 IIS 中裝載 ASP.NET Core 應用程式時啟動錯誤，現在會產生更豐富的診斷資料。</span><span class="sxs-lookup"><span data-stu-id="a59a1-312">Startup errors when hosting ASP.NET Core apps in IIS now produce richer diagnostic data.</span></span> <span data-ttu-id="a59a1-313">這些錯誤會在適用的情況下，向 Windows 事件記錄檔報告堆疊追蹤。</span><span class="sxs-lookup"><span data-stu-id="a59a1-313">These errors are reported to the Windows Event Log with stack traces wherever applicable.</span></span> <span data-ttu-id="a59a1-314">此外，所有警告、錯誤和未處理的例外狀況都會記錄到 Windows 事件記錄檔中。</span><span class="sxs-lookup"><span data-stu-id="a59a1-314">In addition, all warnings, errors, and unhandled exceptions are logged to the Windows Event Log.</span></span>

## <a name="worker-service-and-worker-sdk"></a><span data-ttu-id="a59a1-315">背景工作服務和工作者 SDK</span><span class="sxs-lookup"><span data-stu-id="a59a1-315">Worker Service and Worker SDK</span></span>

<span data-ttu-id="a59a1-316">.NET Core 3.0 引進了新的背景工作服務應用程式範本。</span><span class="sxs-lookup"><span data-stu-id="a59a1-316">.NET Core 3.0 introduces the new Worker Service app template.</span></span> <span data-ttu-id="a59a1-317">此範本提供在 .NET Core 中撰寫長時間執行之服務的起點。</span><span class="sxs-lookup"><span data-stu-id="a59a1-317">This template provides a starting point for writing long running services in .NET Core.</span></span>

<span data-ttu-id="a59a1-318">如需詳細資訊，請參閱</span><span class="sxs-lookup"><span data-stu-id="a59a1-318">For more information, see:</span></span>

* [<span data-ttu-id="a59a1-319">以 .NET Core 背景工作角色作為 Windows 服務</span><span class="sxs-lookup"><span data-stu-id="a59a1-319">.NET Core Workers as Windows Services</span></span>](https://devblogs.microsoft.com/aspnet/net-core-workers-as-windows-services/)
* <xref:fundamentals/host/hosted-services>
* <xref:host-and-deploy/windows-service>

## <a name="forwarded-headers-middleware-improvements"></a><span data-ttu-id="a59a1-320">轉送的標頭中介軟體改進</span><span class="sxs-lookup"><span data-stu-id="a59a1-320">Forwarded Headers Middleware improvements</span></span>

<span data-ttu-id="a59a1-321">在舊版的 ASP.NET Core 中， <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>  <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> 當部署至 Azure LINUX 或 IIS 以外的任何反向 proxy 之後，呼叫和會有問題。</span><span class="sxs-lookup"><span data-stu-id="a59a1-321">In previous versions of ASP.NET Core, calling <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> and  <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> were problematic when deployed to an Azure Linux or behind any reverse proxy other than IIS.</span></span> <span data-ttu-id="a59a1-322">先前版本的修正記錄在 [轉寄 Linux 和非 IIS 反向](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies)proxy 的配置中。</span><span class="sxs-lookup"><span data-stu-id="a59a1-322">The fix for previous versions is documented in [Forward the scheme for Linux and non-IIS reverse proxies](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).</span></span>

<span data-ttu-id="a59a1-323">此案例已在 ASP.NET Core 3.0 中修正。</span><span class="sxs-lookup"><span data-stu-id="a59a1-323">This scenario is fixed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="a59a1-324">當環境變數設定為時，主機會啟用 [轉送的標頭中介軟體](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) `ASPNETCORE_FORWARDEDHEADERS_ENABLED` `true` 。</span><span class="sxs-lookup"><span data-stu-id="a59a1-324">The host enables the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) when the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span> <span data-ttu-id="a59a1-325">`ASPNETCORE_FORWARDEDHEADERS_ENABLED``true`在容器映射中設定為。</span><span class="sxs-lookup"><span data-stu-id="a59a1-325">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` is set to `true` in our container images.</span></span>

## <a name="performance-improvements"></a><span data-ttu-id="a59a1-326">效能改善</span><span class="sxs-lookup"><span data-stu-id="a59a1-326">Performance improvements</span></span>

<span data-ttu-id="a59a1-327">ASP.NET Core 3.0 包含許多改進，可減少記憶體使用量並提升輸送量：</span><span class="sxs-lookup"><span data-stu-id="a59a1-327">ASP.NET Core 3.0 includes many improvements that reduce memory usage and improve throughput:</span></span>

* <span data-ttu-id="a59a1-328">使用內建相依性插入容器進行限域服務時，減少記憶體使用量。</span><span class="sxs-lookup"><span data-stu-id="a59a1-328">Reduction in memory usage when using the built-in dependency injection container for scoped services.</span></span>
* <span data-ttu-id="a59a1-329">減少跨架構的配置，包括中介軟體案例和路由。</span><span class="sxs-lookup"><span data-stu-id="a59a1-329">Reduction in allocations across the framework, including middleware scenarios and routing.</span></span>
* <span data-ttu-id="a59a1-330">減少 WebSocket 連接的記憶體使用量。</span><span class="sxs-lookup"><span data-stu-id="a59a1-330">Reduction in memory usage for WebSocket connections.</span></span>
* <span data-ttu-id="a59a1-331">HTTPS 連線的記憶體減少和輸送量改進。</span><span class="sxs-lookup"><span data-stu-id="a59a1-331">Memory reduction and throughput improvements for HTTPS connections.</span></span>
* <span data-ttu-id="a59a1-332">新的優化和完全非同步 JSON 序列化程式。</span><span class="sxs-lookup"><span data-stu-id="a59a1-332">New optimized and fully asynchronous JSON serializer.</span></span>
* <span data-ttu-id="a59a1-333">減少表單剖析中的記憶體使用量和輸送量改進。</span><span class="sxs-lookup"><span data-stu-id="a59a1-333">Reduction in memory usage and throughput improvements in form parsing.</span></span>

## <a name="aspnet-core-30-only-runs-on-net-core-30"></a><span data-ttu-id="a59a1-334">ASP.NET Core 3.0 僅可在 .NET Core 3.0 上執行</span><span class="sxs-lookup"><span data-stu-id="a59a1-334">ASP.NET Core 3.0 only runs on .NET Core 3.0</span></span>

<span data-ttu-id="a59a1-335">從 ASP.NET Core 3.0，.NET Framework 不再是支援的目標架構。</span><span class="sxs-lookup"><span data-stu-id="a59a1-335">As of ASP.NET Core 3.0, .NET Framework is no longer a supported target framework.</span></span> <span data-ttu-id="a59a1-336">以 .NET Framework 為目標的專案可以使用 [.Net Core 2.1 LTS 版本](https://dotnet.microsoft.com/download/dotnet-core/2.1)，以完全支援的方式繼續進行。</span><span class="sxs-lookup"><span data-stu-id="a59a1-336">Projects targeting .NET Framework can continue in a fully supported fashion using the [.NET Core 2.1 LTS release](https://dotnet.microsoft.com/download/dotnet-core/2.1).</span></span> <span data-ttu-id="a59a1-337">大部分 ASP.NET Core 2.1. x 相關套件將會無限期地支援，超過三年的 .NET Core 2.1 LTS 期限。</span><span class="sxs-lookup"><span data-stu-id="a59a1-337">Most ASP.NET Core 2.1.x related packages will be supported indefinitely, beyond the three-year LTS period for .NET Core 2.1.</span></span>

<span data-ttu-id="a59a1-338">如需遷移資訊，請參閱將 [您的程式碼從 .Net Framework 移植到 .Net Core](/dotnet/core/porting/)。</span><span class="sxs-lookup"><span data-stu-id="a59a1-338">For migration information, see [Port your code from .NET Framework to .NET Core](/dotnet/core/porting/).</span></span>

## <a name="use-the-aspnet-core-shared-framework"></a><span data-ttu-id="a59a1-339">使用 ASP.NET Core 共用架構</span><span class="sxs-lookup"><span data-stu-id="a59a1-339">Use the ASP.NET Core shared framework</span></span>

<span data-ttu-id="a59a1-340">ASP.NET Core 3.0 共用架構（包含在 [AspNetCore 中繼套件](xref:fundamentals/metapackage-app)中）不再需要專案檔中的明確 `<PackageReference />` 元素。</span><span class="sxs-lookup"><span data-stu-id="a59a1-340">The ASP.NET Core 3.0 shared framework, contained in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), no longer requires an explicit `<PackageReference />` element in the project file.</span></span> <span data-ttu-id="a59a1-341">在專案檔中使用 SDK 時，會自動參考共用架構 `Microsoft.NET.Sdk.Web` ：</span><span class="sxs-lookup"><span data-stu-id="a59a1-341">The shared framework is automatically referenced when using the `Microsoft.NET.Sdk.Web` SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

## <a name="assemblies-removed-from-the-aspnet-core-shared-framework"></a><span data-ttu-id="a59a1-342">從 ASP.NET Core 共用架構移除的元件</span><span class="sxs-lookup"><span data-stu-id="a59a1-342">Assemblies removed from the ASP.NET Core shared framework</span></span>

<span data-ttu-id="a59a1-343">從 ASP.NET Core 3.0 共用架構中移除最顯著的元件如下：</span><span class="sxs-lookup"><span data-stu-id="a59a1-343">The most notable assemblies removed from the ASP.NET Core 3.0 shared framework are:</span></span>

* <span data-ttu-id="a59a1-344">[Newtonsoft.Js](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET) 。</span><span class="sxs-lookup"><span data-stu-id="a59a1-344">[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET).</span></span> <span data-ttu-id="a59a1-345">若要將 Json.NET 新增至 ASP.NET Core 3.0，請參閱 [新增 Newtonsoft.Js的 Json 格式支援](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support)。</span><span class="sxs-lookup"><span data-stu-id="a59a1-345">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span> <span data-ttu-id="a59a1-346">ASP.NET Core 3.0 引進 `System.Text.Json` 讀取和寫入 JSON。</span><span class="sxs-lookup"><span data-stu-id="a59a1-346">ASP.NET Core 3.0 introduces `System.Text.Json` for reading and writing JSON.</span></span> <span data-ttu-id="a59a1-347">如需詳細資訊，請參閱本檔中的 [新 JSON 序列化](#new-json-serialization) 。</span><span class="sxs-lookup"><span data-stu-id="a59a1-347">For more information, see [New JSON serialization](#new-json-serialization) in this document.</span></span>
* [<span data-ttu-id="a59a1-348">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="a59a1-348">Entity Framework Core</span></span>](/ef/core/)

<span data-ttu-id="a59a1-349">如需從共用架構移除的完整元件清單，請參閱 [從 AspNetCore 中移除的元件。應用程式 3.0](https://github.com/dotnet/AspNetCore/issues/3755)。</span><span class="sxs-lookup"><span data-stu-id="a59a1-349">For a complete list of assemblies removed from the shared framework, see [Assemblies being removed from Microsoft.AspNetCore.App 3.0](https://github.com/dotnet/AspNetCore/issues/3755).</span></span> <span data-ttu-id="a59a1-350">如需這項變更動機的詳細資訊，請參閱 [3.0 中的 AspNetCore 應用程式的重大變更](https://github.com/aspnet/Announcements/issues/325) ，以及 [ASP.NET Core 3.0 中的變更首次查看](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/)。</span><span class="sxs-lookup"><span data-stu-id="a59a1-350">For more information on the motivation for this change, see [Breaking changes to Microsoft.AspNetCore.App in 3.0](https://github.com/aspnet/Announcements/issues/325) and [A first look at changes coming in ASP.NET Core 3.0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<!-- 
## Additional information
For the complete list of changes, see the [ASP.NET Core 3.0 Release Notes](WHERE IS THIS????).
-->
