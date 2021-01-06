---
title: 具有 ASP.NET Core 的 Open Web Interface for .NET (OWIN)
author: ardalis
description: 探索 ASP.NET Core 如何支援 Open Web Interface for .NET (OWIN)，這可讓 Web 應用程式與網頁伺服器分離。
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 12/18/2018
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
uid: fundamentals/owin
ms.openlocfilehash: 6085abc45137223d7a676107cf06dc2cf97a5c19
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060673"
---
# <a name="open-web-interface-for-net-owin-with-aspnet-core"></a><span data-ttu-id="ad5df-103">具有 ASP.NET Core 的 Open Web Interface for .NET (OWIN)</span><span class="sxs-lookup"><span data-stu-id="ad5df-103">Open Web Interface for .NET (OWIN) with ASP.NET Core</span></span>

<span data-ttu-id="ad5df-104">作者：[Steve Smith](https://ardalis.com/) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ad5df-104">By [Steve Smith](https://ardalis.com/) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="ad5df-105">ASP.NET Core 支援Open Web Interface for .NET (OWIN)。</span><span class="sxs-lookup"><span data-stu-id="ad5df-105">ASP.NET Core supports the Open Web Interface for .NET (OWIN).</span></span> <span data-ttu-id="ad5df-106">OWIN 可讓 Web 應用程式獨立於網頁伺服器。</span><span class="sxs-lookup"><span data-stu-id="ad5df-106">OWIN allows web apps to be decoupled from web servers.</span></span> <span data-ttu-id="ad5df-107">它會定義中介軟體要在管線中用來處理要求和相關聯回應的標準方式。</span><span class="sxs-lookup"><span data-stu-id="ad5df-107">It defines a standard way for middleware to be used in a pipeline to handle requests and associated responses.</span></span> <span data-ttu-id="ad5df-108">ASP.NET Core 應用程式和中介軟體可以與以 OWIN 為基礎的應用程式、伺服器及中介軟體進行交互操作。</span><span class="sxs-lookup"><span data-stu-id="ad5df-108">ASP.NET Core applications and middleware can interoperate with OWIN-based applications, servers, and middleware.</span></span>

<span data-ttu-id="ad5df-109">OWIN 提供分離層，可讓兩個利用不同物件模型的架構一起使用。</span><span class="sxs-lookup"><span data-stu-id="ad5df-109">OWIN provides a decoupling layer that allows two frameworks with disparate object models to be used together.</span></span> <span data-ttu-id="ad5df-110">`Microsoft.AspNetCore.Owin` 套件提供兩個配接器實作：</span><span class="sxs-lookup"><span data-stu-id="ad5df-110">The `Microsoft.AspNetCore.Owin` package provides two adapter implementations:</span></span>

* <span data-ttu-id="ad5df-111">ASP.NET Core 至 OWIN</span><span class="sxs-lookup"><span data-stu-id="ad5df-111">ASP.NET Core to OWIN</span></span> 
* <span data-ttu-id="ad5df-112">OWIN 至 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ad5df-112">OWIN to ASP.NET Core</span></span>

<span data-ttu-id="ad5df-113">這可讓 ASP.NET Core 裝載在 OWIN 相容的伺服器/主機之上，或讓其他 OWIN 相容的元件在 ASP.NET Core 之上執行。</span><span class="sxs-lookup"><span data-stu-id="ad5df-113">This allows ASP.NET Core to be hosted on top of an OWIN compatible server/host or for other OWIN compatible components to be run on top of ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="ad5df-114">使用這些配接器將伴隨效能成本增加。</span><span class="sxs-lookup"><span data-stu-id="ad5df-114">Using these adapters comes with a performance cost.</span></span> <span data-ttu-id="ad5df-115">僅使用 ASP.NET Core 元件的應用程式不應使用 `Microsoft.AspNetCore.Owin` 套件或配接器。</span><span class="sxs-lookup"><span data-stu-id="ad5df-115">Apps using only ASP.NET Core components shouldn't use the `Microsoft.AspNetCore.Owin` package or adapters.</span></span>

<span data-ttu-id="ad5df-116">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="ad5df-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="running-owin-middleware-in-the-aspnet-core-pipeline"></a><span data-ttu-id="ad5df-117">在 ASP.NET Core 管線中執行 OWIN 中介軟體</span><span class="sxs-lookup"><span data-stu-id="ad5df-117">Running OWIN middleware in the ASP.NET Core pipeline</span></span>

<span data-ttu-id="ad5df-118">ASP.NET Core 的 OWIN 支援部署為 `Microsoft.AspNetCore.Owin` 套件的一部分。</span><span class="sxs-lookup"><span data-stu-id="ad5df-118">ASP.NET Core's OWIN support is deployed as part of the `Microsoft.AspNetCore.Owin` package.</span></span> <span data-ttu-id="ad5df-119">您可以藉由安裝此套件，將 OWIN 支援匯入您的專案中。</span><span class="sxs-lookup"><span data-stu-id="ad5df-119">You can import OWIN support into your project by installing this package.</span></span>

<span data-ttu-id="ad5df-120">OWIN 中介軟體符合 [OWIN 規格](https://owin.org/spec/spec/owin-1.0.0.html)，它需要設定 `Func<IDictionary<string, object>, Task>` 介面和特定的索引鍵 (例如 `owin.ResponseBody`)。</span><span class="sxs-lookup"><span data-stu-id="ad5df-120">OWIN middleware conforms to the [OWIN specification](https://owin.org/spec/spec/owin-1.0.0.html), which requires a `Func<IDictionary<string, object>, Task>` interface, and specific keys be set (such as `owin.ResponseBody`).</span></span> <span data-ttu-id="ad5df-121">下列簡單的 OWIN 中介軟體會顯示 "Hello World"：</span><span class="sxs-lookup"><span data-stu-id="ad5df-121">The following simple OWIN middleware displays "Hello World":</span></span>

```csharp
public Task OwinHello(IDictionary<string, object> environment)
{
    string responseText = "Hello World via OWIN";
    byte[] responseBytes = Encoding.UTF8.GetBytes(responseText);

    // OWIN Environment Keys: https://owin.org/spec/spec/owin-1.0.0.html
    var responseStream = (Stream)environment["owin.ResponseBody"];
    var responseHeaders = (IDictionary<string, string[]>)environment["owin.ResponseHeaders"];

    responseHeaders["Content-Length"] = new string[] { responseBytes.Length.ToString(CultureInfo.InvariantCulture) };
    responseHeaders["Content-Type"] = new string[] { "text/plain" };

    return responseStream.WriteAsync(responseBytes, 0, responseBytes.Length);
}
```

<span data-ttu-id="ad5df-122">範例簽章會傳回 `Task`，並依照 OWIN 的要求接受 `IDictionary<string, object>`。</span><span class="sxs-lookup"><span data-stu-id="ad5df-122">The sample signature returns a `Task` and accepts an `IDictionary<string, object>` as required by OWIN.</span></span>

<span data-ttu-id="ad5df-123">下列程式碼示範如何使用 `UseOwin` 擴充方法，將 `OwinHello` 中介軟體 (如上所示) 新增至 ASP.NET Core 管線。</span><span class="sxs-lookup"><span data-stu-id="ad5df-123">The following code shows how to add the `OwinHello` middleware (shown above) to the ASP.NET Core pipeline with the `UseOwin` extension method.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}
```

<span data-ttu-id="ad5df-124">您可以設定在 OWIN 管線內進行其他動作。</span><span class="sxs-lookup"><span data-stu-id="ad5df-124">You can configure other actions to take place within the OWIN pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="ad5df-125">只能在第一次寫入回應資料流之前修改回應標頭。</span><span class="sxs-lookup"><span data-stu-id="ad5df-125">Response headers should only be modified prior to the first write to the response stream.</span></span>

> [!NOTE]
> <span data-ttu-id="ad5df-126">基於效能考量，建議您不要多次呼叫 `UseOwin`。</span><span class="sxs-lookup"><span data-stu-id="ad5df-126">Multiple calls to `UseOwin` is discouraged for performance reasons.</span></span> <span data-ttu-id="ad5df-127">OWIN 元件如果群組在一起，其運作效能最佳。</span><span class="sxs-lookup"><span data-stu-id="ad5df-127">OWIN components will operate best if grouped together.</span></span>

```csharp
app.UseOwin(pipeline =>
{
    pipeline(next =>
    {
        return async environment =>
        {
            // Do something before.
            await next(environment);
            // Do something after.
        };
    });
});
```

<a name="hosting-on-owin"></a>

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a><span data-ttu-id="ad5df-128">在以 OWIN 為基礎的伺服器上執行 ASP.NET Core 並使用其 WebSocket 支援</span><span class="sxs-lookup"><span data-stu-id="ad5df-128">Run ASP.NET Core on an OWIN-based server and use its WebSockets support</span></span>

<span data-ttu-id="ad5df-129">ASP.NET Core 如何利用以 OWIN 為基礎之伺服器功能的另一個範例是存取 WebSocket 等功能。</span><span class="sxs-lookup"><span data-stu-id="ad5df-129">Another example of how OWIN-based servers' features can be leveraged by ASP.NET Core is access to features like WebSockets.</span></span> <span data-ttu-id="ad5df-130">在上述範例中使用的 .NET OWIN 網頁伺服器支援內建的 Web 通訊端，供 ASP.NET Core 應用程式加以利用。</span><span class="sxs-lookup"><span data-stu-id="ad5df-130">The .NET OWIN web server used in the previous example has support for Web Sockets built in, which can be leveraged by an ASP.NET Core application.</span></span> <span data-ttu-id="ad5df-131">下列範例顯示的簡單 Web 應用程式支援 Web 通訊端，並透過 Websocket 回應傳送至伺服器的所有項目。</span><span class="sxs-lookup"><span data-stu-id="ad5df-131">The example below shows a simple web app that supports Web Sockets and echoes back everything sent to the server through WebSockets.</span></span>

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app)
    {
        app.Use(async (context, next) =>
        {
            if (context.WebSockets.IsWebSocketRequest)
            {
                WebSocket webSocket = await context.WebSockets.AcceptWebSocketAsync();
                await EchoWebSocket(webSocket);
            }
            else
            {
                await next();
            }
        });

        app.Run(context =>
        {
            return context.Response.WriteAsync("Hello World");
        });
    }

    private async Task EchoWebSocket(WebSocket webSocket)
    {
        byte[] buffer = new byte[1024];
        WebSocketReceiveResult received = await webSocket.ReceiveAsync(
            new ArraySegment<byte>(buffer), CancellationToken.None);

        while (!webSocket.CloseStatus.HasValue)
        {
            // Echo anything we receive
            await webSocket.SendAsync(new ArraySegment<byte>(buffer, 0, received.Count), 
                received.MessageType, received.EndOfMessage, CancellationToken.None);

            received = await webSocket.ReceiveAsync(new ArraySegment<byte>(buffer), 
                CancellationToken.None);
        }

        await webSocket.CloseAsync(webSocket.CloseStatus.Value, 
            webSocket.CloseStatusDescription, CancellationToken.None);
    }
}
```

## <a name="owin-environment"></a><span data-ttu-id="ad5df-132">OWIN 環境</span><span class="sxs-lookup"><span data-stu-id="ad5df-132">OWIN environment</span></span>

<span data-ttu-id="ad5df-133">您可以使用 `HttpContext` 建構 OWIN 環境。</span><span class="sxs-lookup"><span data-stu-id="ad5df-133">You can construct an OWIN environment using the `HttpContext`.</span></span>

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a><span data-ttu-id="ad5df-134">OWIN 索引鍵</span><span class="sxs-lookup"><span data-stu-id="ad5df-134">OWIN keys</span></span>

<span data-ttu-id="ad5df-135">OWIN 仰賴 `IDictionary<string,object>` 物件在 HTTP 要求/回應交換中傳遞資訊。</span><span class="sxs-lookup"><span data-stu-id="ad5df-135">OWIN depends on an `IDictionary<string,object>` object to communicate information throughout an HTTP Request/Response exchange.</span></span> <span data-ttu-id="ad5df-136">ASP.NET Core 會實作下面所列的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="ad5df-136">ASP.NET Core implements the keys listed below.</span></span> <span data-ttu-id="ad5df-137">請參閱[主要規格、模組延伸](https://owin.org/#spec)和 [OWIN Key Guidelines and Common Keys](https://owin.org/spec/spec/CommonKeys.html) (OWIN 索引鍵指導方針和共用索引鍵)。</span><span class="sxs-lookup"><span data-stu-id="ad5df-137">See the [primary specification, extensions](https://owin.org/#spec), and [OWIN Key Guidelines and Common Keys](https://owin.org/spec/spec/CommonKeys.html).</span></span>

### <a name="request-data-owin-v100"></a><span data-ttu-id="ad5df-138">要求資料 (OWIN 1.0.0 版)</span><span class="sxs-lookup"><span data-stu-id="ad5df-138">Request data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="ad5df-139">答案</span><span class="sxs-lookup"><span data-stu-id="ad5df-139">Key</span></span>               | <span data-ttu-id="ad5df-140">值 (類型)</span><span class="sxs-lookup"><span data-stu-id="ad5df-140">Value (type)</span></span> | <span data-ttu-id="ad5df-141">描述</span><span class="sxs-lookup"><span data-stu-id="ad5df-141">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="ad5df-142">owin.RequestScheme</span><span class="sxs-lookup"><span data-stu-id="ad5df-142">owin.RequestScheme</span></span> | `String` |  |
| <span data-ttu-id="ad5df-143">owin.RequestMethod</span><span class="sxs-lookup"><span data-stu-id="ad5df-143">owin.RequestMethod</span></span>  | `String` | |    
| <span data-ttu-id="ad5df-144">owin.RequestPathBase</span><span class="sxs-lookup"><span data-stu-id="ad5df-144">owin.RequestPathBase</span></span>  | `String` | |    
| <span data-ttu-id="ad5df-145">owin.RequestPath</span><span class="sxs-lookup"><span data-stu-id="ad5df-145">owin.RequestPath</span></span> | `String` | |     
| <span data-ttu-id="ad5df-146">owin.RequestQueryString</span><span class="sxs-lookup"><span data-stu-id="ad5df-146">owin.RequestQueryString</span></span>  | `String` | |    
| <span data-ttu-id="ad5df-147">owin.RequestProtocol</span><span class="sxs-lookup"><span data-stu-id="ad5df-147">owin.RequestProtocol</span></span>  | `String` | |    
| <span data-ttu-id="ad5df-148">owin.RequestHeaders</span><span class="sxs-lookup"><span data-stu-id="ad5df-148">owin.RequestHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="ad5df-149">owin.RequestBody</span><span class="sxs-lookup"><span data-stu-id="ad5df-149">owin.RequestBody</span></span> | `Stream`  | |

### <a name="request-data-owin-v110"></a><span data-ttu-id="ad5df-150">要求資料 (OWIN 1.1.0 版)</span><span class="sxs-lookup"><span data-stu-id="ad5df-150">Request data (OWIN v1.1.0)</span></span>

| <span data-ttu-id="ad5df-151">答案</span><span class="sxs-lookup"><span data-stu-id="ad5df-151">Key</span></span>               | <span data-ttu-id="ad5df-152">值 (類型)</span><span class="sxs-lookup"><span data-stu-id="ad5df-152">Value (type)</span></span> | <span data-ttu-id="ad5df-153">描述</span><span class="sxs-lookup"><span data-stu-id="ad5df-153">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="ad5df-154">owin.RequestId</span><span class="sxs-lookup"><span data-stu-id="ad5df-154">owin.RequestId</span></span> | `String` | <span data-ttu-id="ad5df-155">選擇性</span><span class="sxs-lookup"><span data-stu-id="ad5df-155">Optional</span></span> |

### <a name="response-data-owin-v100"></a><span data-ttu-id="ad5df-156">回應資料 (OWIN 1.0.0 版)</span><span class="sxs-lookup"><span data-stu-id="ad5df-156">Response data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="ad5df-157">答案</span><span class="sxs-lookup"><span data-stu-id="ad5df-157">Key</span></span>               | <span data-ttu-id="ad5df-158">值 (類型)</span><span class="sxs-lookup"><span data-stu-id="ad5df-158">Value (type)</span></span> | <span data-ttu-id="ad5df-159">描述</span><span class="sxs-lookup"><span data-stu-id="ad5df-159">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="ad5df-160">owin.ResponseStatusCode</span><span class="sxs-lookup"><span data-stu-id="ad5df-160">owin.ResponseStatusCode</span></span> | `int` | <span data-ttu-id="ad5df-161">選擇性</span><span class="sxs-lookup"><span data-stu-id="ad5df-161">Optional</span></span> |
| <span data-ttu-id="ad5df-162">owin.ResponseReasonPhrase</span><span class="sxs-lookup"><span data-stu-id="ad5df-162">owin.ResponseReasonPhrase</span></span> | `String` | <span data-ttu-id="ad5df-163">選擇性</span><span class="sxs-lookup"><span data-stu-id="ad5df-163">Optional</span></span> |
| <span data-ttu-id="ad5df-164">owin.ResponseHeaders</span><span class="sxs-lookup"><span data-stu-id="ad5df-164">owin.ResponseHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="ad5df-165">owin.ResponseBody</span><span class="sxs-lookup"><span data-stu-id="ad5df-165">owin.ResponseBody</span></span> | `Stream`  | |

### <a name="other-data-owin-v100"></a><span data-ttu-id="ad5df-166">其他資料 (OWIN 1.0.0 版)</span><span class="sxs-lookup"><span data-stu-id="ad5df-166">Other data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="ad5df-167">答案</span><span class="sxs-lookup"><span data-stu-id="ad5df-167">Key</span></span>               | <span data-ttu-id="ad5df-168">值 (類型)</span><span class="sxs-lookup"><span data-stu-id="ad5df-168">Value (type)</span></span> | <span data-ttu-id="ad5df-169">描述</span><span class="sxs-lookup"><span data-stu-id="ad5df-169">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="ad5df-170">owin.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="ad5df-170">owin.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="ad5df-171">owin.Version</span><span class="sxs-lookup"><span data-stu-id="ad5df-171">owin.Version</span></span>  | `String` | |   

### <a name="common-keys"></a><span data-ttu-id="ad5df-172">共同索引鍵</span><span class="sxs-lookup"><span data-stu-id="ad5df-172">Common keys</span></span>

| <span data-ttu-id="ad5df-173">答案</span><span class="sxs-lookup"><span data-stu-id="ad5df-173">Key</span></span>               | <span data-ttu-id="ad5df-174">值 (類型)</span><span class="sxs-lookup"><span data-stu-id="ad5df-174">Value (type)</span></span> | <span data-ttu-id="ad5df-175">描述</span><span class="sxs-lookup"><span data-stu-id="ad5df-175">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="ad5df-176">ssl.ClientCertificate</span><span class="sxs-lookup"><span data-stu-id="ad5df-176">ssl.ClientCertificate</span></span> | `X509Certificate` |  |
| <span data-ttu-id="ad5df-177">ssl.LoadClientCertAsync</span><span class="sxs-lookup"><span data-stu-id="ad5df-177">ssl.LoadClientCertAsync</span></span>  | `Func<Task>` | |    
| <span data-ttu-id="ad5df-178">server.RemoteIpAddress</span><span class="sxs-lookup"><span data-stu-id="ad5df-178">server.RemoteIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="ad5df-179">server.RemotePort</span><span class="sxs-lookup"><span data-stu-id="ad5df-179">server.RemotePort</span></span> | `String` | |     
| <span data-ttu-id="ad5df-180">server.LocalIpAddress</span><span class="sxs-lookup"><span data-stu-id="ad5df-180">server.LocalIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="ad5df-181">server.LocalPort</span><span class="sxs-lookup"><span data-stu-id="ad5df-181">server.LocalPort</span></span>  | `String` | |    
| <span data-ttu-id="ad5df-182">server.IsLocal</span><span class="sxs-lookup"><span data-stu-id="ad5df-182">server.IsLocal</span></span>  | `bool` | |    
| <span data-ttu-id="ad5df-183">server.OnSendingHeaders</span><span class="sxs-lookup"><span data-stu-id="ad5df-183">server.OnSendingHeaders</span></span>  | `Action<Action<object>,object>` | |

### <a name="sendfiles-v030"></a><span data-ttu-id="ad5df-184">SendFiles 0.3.0 版</span><span class="sxs-lookup"><span data-stu-id="ad5df-184">SendFiles v0.3.0</span></span>

| <span data-ttu-id="ad5df-185">答案</span><span class="sxs-lookup"><span data-stu-id="ad5df-185">Key</span></span>               | <span data-ttu-id="ad5df-186">值 (類型)</span><span class="sxs-lookup"><span data-stu-id="ad5df-186">Value (type)</span></span> | <span data-ttu-id="ad5df-187">描述</span><span class="sxs-lookup"><span data-stu-id="ad5df-187">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="ad5df-188">sendfile.SendAsync</span><span class="sxs-lookup"><span data-stu-id="ad5df-188">sendfile.SendAsync</span></span> | <span data-ttu-id="ad5df-189">請參閱[委派簽章](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="ad5df-189">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> | <span data-ttu-id="ad5df-190">每個要求</span><span class="sxs-lookup"><span data-stu-id="ad5df-190">Per Request</span></span> |

### <a name="opaque-v030"></a><span data-ttu-id="ad5df-191">Opaque 0.3.0 版</span><span class="sxs-lookup"><span data-stu-id="ad5df-191">Opaque v0.3.0</span></span>

| <span data-ttu-id="ad5df-192">答案</span><span class="sxs-lookup"><span data-stu-id="ad5df-192">Key</span></span>               | <span data-ttu-id="ad5df-193">值 (類型)</span><span class="sxs-lookup"><span data-stu-id="ad5df-193">Value (type)</span></span> | <span data-ttu-id="ad5df-194">描述</span><span class="sxs-lookup"><span data-stu-id="ad5df-194">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="ad5df-195">opaque.Version</span><span class="sxs-lookup"><span data-stu-id="ad5df-195">opaque.Version</span></span> | `String` |  |
| <span data-ttu-id="ad5df-196">opaque.Upgrade</span><span class="sxs-lookup"><span data-stu-id="ad5df-196">opaque.Upgrade</span></span> | `OpaqueUpgrade` | <span data-ttu-id="ad5df-197">請參閱[委派簽章](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="ad5df-197">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="ad5df-198">opaque.Stream</span><span class="sxs-lookup"><span data-stu-id="ad5df-198">opaque.Stream</span></span> | `Stream` |  |
| <span data-ttu-id="ad5df-199">opaque.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="ad5df-199">opaque.CallCancelled</span></span> | `CancellationToken` |  |

### <a name="websocket-v030"></a><span data-ttu-id="ad5df-200">WebSocket 0.3.0 版</span><span class="sxs-lookup"><span data-stu-id="ad5df-200">WebSocket v0.3.0</span></span>

| <span data-ttu-id="ad5df-201">答案</span><span class="sxs-lookup"><span data-stu-id="ad5df-201">Key</span></span>               | <span data-ttu-id="ad5df-202">值 (類型)</span><span class="sxs-lookup"><span data-stu-id="ad5df-202">Value (type)</span></span> | <span data-ttu-id="ad5df-203">描述</span><span class="sxs-lookup"><span data-stu-id="ad5df-203">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="ad5df-204">websocket.Version</span><span class="sxs-lookup"><span data-stu-id="ad5df-204">websocket.Version</span></span> | `String` |  |
| <span data-ttu-id="ad5df-205">websocket.Accept</span><span class="sxs-lookup"><span data-stu-id="ad5df-205">websocket.Accept</span></span> | `WebSocketAccept` | <span data-ttu-id="ad5df-206">請參閱[委派簽章](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="ad5df-206">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="ad5df-207">websocket.AcceptAlt</span><span class="sxs-lookup"><span data-stu-id="ad5df-207">websocket.AcceptAlt</span></span> |  | <span data-ttu-id="ad5df-208">非規格</span><span class="sxs-lookup"><span data-stu-id="ad5df-208">Non-spec</span></span> |
| <span data-ttu-id="ad5df-209">websocket.SubProtocol</span><span class="sxs-lookup"><span data-stu-id="ad5df-209">websocket.SubProtocol</span></span> | `String` | <span data-ttu-id="ad5df-210">請參閱 [RFC6455 4.2.2 節](https://tools.ietf.org/html/rfc6455#section-4.2.2)的步驟 5.5</span><span class="sxs-lookup"><span data-stu-id="ad5df-210">See [RFC6455 Section 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) Step 5.5</span></span> |
| <span data-ttu-id="ad5df-211">websocket.SendAsync</span><span class="sxs-lookup"><span data-stu-id="ad5df-211">websocket.SendAsync</span></span> | `WebSocketSendAsync` | <span data-ttu-id="ad5df-212">請參閱[委派簽章](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="ad5df-212">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="ad5df-213">websocket.ReceiveAsync</span><span class="sxs-lookup"><span data-stu-id="ad5df-213">websocket.ReceiveAsync</span></span> | `WebSocketReceiveAsync` | <span data-ttu-id="ad5df-214">請參閱[委派簽章](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="ad5df-214">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="ad5df-215">websocket.CloseAsync</span><span class="sxs-lookup"><span data-stu-id="ad5df-215">websocket.CloseAsync</span></span> | `WebSocketCloseAsync` | <span data-ttu-id="ad5df-216">請參閱[委派簽章](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="ad5df-216">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="ad5df-217">websocket.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="ad5df-217">websocket.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="ad5df-218">websocket.ClientCloseStatus</span><span class="sxs-lookup"><span data-stu-id="ad5df-218">websocket.ClientCloseStatus</span></span> | `int` | <span data-ttu-id="ad5df-219">選擇性</span><span class="sxs-lookup"><span data-stu-id="ad5df-219">Optional</span></span> |
| <span data-ttu-id="ad5df-220">websocket.ClientCloseDescription</span><span class="sxs-lookup"><span data-stu-id="ad5df-220">websocket.ClientCloseDescription</span></span> | `String` | <span data-ttu-id="ad5df-221">選擇性</span><span class="sxs-lookup"><span data-stu-id="ad5df-221">Optional</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="ad5df-222">其他資源</span><span class="sxs-lookup"><span data-stu-id="ad5df-222">Additional resources</span></span>

* [<span data-ttu-id="ad5df-223">中介軟體</span><span class="sxs-lookup"><span data-stu-id="ad5df-223">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="ad5df-224">伺服器</span><span class="sxs-lookup"><span data-stu-id="ad5df-224">Servers</span></span>](xref:fundamentals/servers/index)
