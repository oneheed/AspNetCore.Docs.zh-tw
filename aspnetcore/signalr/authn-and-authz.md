---
title: ASP.NET Core 中的驗證和授權 SignalR
author: bradygaster
description: 瞭解如何在 ASP.NET Core 中使用驗證和授權 SignalR 。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
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
uid: signalr/authn-and-authz
ms.openlocfilehash: 9a3102e4451bbc5cd9ff15e88bebd4e4f2c115f4
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588096"
---
# <a name="authentication-and-authorization-in-aspnet-core-signalr"></a><span data-ttu-id="f86b0-103">ASP.NET Core 中的驗證和授權 SignalR</span><span class="sxs-lookup"><span data-stu-id="f86b0-103">Authentication and authorization in ASP.NET Core SignalR</span></span>

<span data-ttu-id="f86b0-104">[Andrew Stanton-護士](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="f86b0-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="f86b0-105">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/signalr/authn-and-authz/sample/) [ (如何下載) ](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="f86b0-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/signalr/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-connecting-to-a-signalr-hub"></a><span data-ttu-id="f86b0-106">驗證連接至中樞的使用者 SignalR</span><span class="sxs-lookup"><span data-stu-id="f86b0-106">Authenticate users connecting to a SignalR hub</span></span>

<span data-ttu-id="f86b0-107">SignalR 可以與 [ASP.NET Core 驗證](xref:security/authentication/identity) 搭配使用，以將使用者與每個連接產生關聯。</span><span class="sxs-lookup"><span data-stu-id="f86b0-107">SignalR can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each connection.</span></span> <span data-ttu-id="f86b0-108">在中樞中，可以從 HubConnectionCoNtext 存取驗證資料。 [使用者](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) 屬性。</span><span class="sxs-lookup"><span data-stu-id="f86b0-108">In a hub, authentication data can be accessed from the [HubConnectionContext.User](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) property.</span></span> <span data-ttu-id="f86b0-109">驗證可讓中樞在與使用者相關聯的所有連接上呼叫方法。</span><span class="sxs-lookup"><span data-stu-id="f86b0-109">Authentication allows the hub to call methods on all connections associated with a user.</span></span> <span data-ttu-id="f86b0-110">如需詳細資訊，請參閱[中 SignalR 的管理使用者和群組](xref:signalr/groups)。</span><span class="sxs-lookup"><span data-stu-id="f86b0-110">For more information, see [Manage users and groups in SignalR](xref:signalr/groups).</span></span> <span data-ttu-id="f86b0-111">多個連接可能與單一使用者相關聯。</span><span class="sxs-lookup"><span data-stu-id="f86b0-111">Multiple connections may be associated with a single user.</span></span>

<span data-ttu-id="f86b0-112">以下是 `Startup.Configure` 使用 SignalR 和 ASP.NET 核心驗證的範例：</span><span class="sxs-lookup"><span data-stu-id="f86b0-112">The following is an example of `Startup.Configure` which uses SignalR and ASP.NET Core authentication:</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseRouting();

    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chat");
        endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseAuthentication();

    app.UseSignalR(hubs =>
    {
        hubs.MapHub<ChatHub>("/chat");
    });

    app.UseMvc(routes =>
    {
        routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

> [!NOTE]
> <span data-ttu-id="f86b0-113">您註冊 SignalR 和 ASP.NET 核心驗證中介軟體重要的順序。</span><span class="sxs-lookup"><span data-stu-id="f86b0-113">The order in which you register the SignalR and ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="f86b0-114">一律在 `UseAuthentication` 之前呼叫 `UseSignalR` ，在 SignalR 上有使用者 `HttpContext` 。</span><span class="sxs-lookup"><span data-stu-id="f86b0-114">Always call `UseAuthentication` before `UseSignalR` so that SignalR has a user on the `HttpContext`.</span></span>

::: moniker-end

### <a name="cookie-authentication"></a><span data-ttu-id="f86b0-115">Cookie 驗證</span><span class="sxs-lookup"><span data-stu-id="f86b0-115">Cookie authentication</span></span>

<span data-ttu-id="f86b0-116">在以瀏覽器為基礎的應用程式中， cookie 驗證可讓您現有的使用者認證自動流向連線 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="f86b0-116">In a browser-based app, cookie authentication allows your existing user credentials to automatically flow to SignalR connections.</span></span> <span data-ttu-id="f86b0-117">使用瀏覽器用戶端時，不需要進行其他設定。</span><span class="sxs-lookup"><span data-stu-id="f86b0-117">When using the browser client, no additional configuration is needed.</span></span> <span data-ttu-id="f86b0-118">如果使用者已登入您的應用程式，則 SignalR 連接會自動繼承此驗證。</span><span class="sxs-lookup"><span data-stu-id="f86b0-118">If the user is logged in to your app, the SignalR connection automatically inherits this authentication.</span></span>

<span data-ttu-id="f86b0-119">Cookie是以瀏覽器特定的方式傳送存取權杖，但是非瀏覽器用戶端可以傳送存取權杖。</span><span class="sxs-lookup"><span data-stu-id="f86b0-119">Cookies are a browser-specific way to send access tokens, but non-browser clients can send them.</span></span> <span data-ttu-id="f86b0-120">使用 [.Net 用戶端](xref:signalr/dotnet-client)時， `Cookies` 可以在呼叫中設定屬性， `.WithUrl` 以提供 cookie 。</span><span class="sxs-lookup"><span data-stu-id="f86b0-120">When using the [.NET Client](xref:signalr/dotnet-client), the `Cookies` property can be configured in the `.WithUrl` call to provide a cookie.</span></span> <span data-ttu-id="f86b0-121">不過， cookie 若要使用來自 .net 用戶端的驗證，應用程式會要求應用程式提供 API，以交換的驗證資料 cookie 。</span><span class="sxs-lookup"><span data-stu-id="f86b0-121">However, using cookie authentication from the .NET client requires the app to provide an API to exchange authentication data for a cookie.</span></span>

### <a name="bearer-token-authentication"></a><span data-ttu-id="f86b0-122">持有人權杖驗證</span><span class="sxs-lookup"><span data-stu-id="f86b0-122">Bearer token authentication</span></span>

<span data-ttu-id="f86b0-123">用戶端可以提供存取權杖，而不是使用 cookie 。</span><span class="sxs-lookup"><span data-stu-id="f86b0-123">The client can provide an access token instead of using a cookie.</span></span> <span data-ttu-id="f86b0-124">伺服器會驗證權杖，並使用它來識別使用者。</span><span class="sxs-lookup"><span data-stu-id="f86b0-124">The server validates the token and uses it to identify the user.</span></span> <span data-ttu-id="f86b0-125">只有在建立連接時，才會進行這項驗證。</span><span class="sxs-lookup"><span data-stu-id="f86b0-125">This validation is done only when the connection is established.</span></span> <span data-ttu-id="f86b0-126">在連線的存留期內，伺服器不會自動重新驗證以檢查權杖撤銷。</span><span class="sxs-lookup"><span data-stu-id="f86b0-126">During the life of the connection, the server doesn't automatically revalidate to check for token revocation.</span></span>

<span data-ttu-id="f86b0-127">在 JavaScript 用戶端中，您可以使用 [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) 選項來提供權杖。</span><span class="sxs-lookup"><span data-stu-id="f86b0-127">In the JavaScript client, the token can be provided using the [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) option.</span></span>

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=52-55)]

<span data-ttu-id="f86b0-128">在 .NET 用戶端中，有一個類似的 [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) 屬性可用來設定權杖：</span><span class="sxs-lookup"><span data-stu-id="f86b0-128">In the .NET client, there's a similar [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) property that can be used to configure the token:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="f86b0-129">您提供的存取權杖函式會在 **每個** 發出的 HTTP 要求之前呼叫 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="f86b0-129">The access token function you provide is called before **every** HTTP request made by SignalR.</span></span> <span data-ttu-id="f86b0-130">如果您需要更新權杖以便讓連接保持使用中 (，因為它可能會在連線) 期間過期，請從這個函式中執行，並傳回更新的權杖。</span><span class="sxs-lookup"><span data-stu-id="f86b0-130">If you need to renew the token in order to keep the connection active (because it may expire during the connection), do so from within this function and return the updated token.</span></span>

<span data-ttu-id="f86b0-131">在標準 web Api 中，會在 HTTP 標頭中傳送持有人權杖。</span><span class="sxs-lookup"><span data-stu-id="f86b0-131">In standard web APIs, bearer tokens are sent in an HTTP header.</span></span> <span data-ttu-id="f86b0-132">不過， SignalR 使用某些傳輸時，無法在瀏覽器中設定這些標頭。</span><span class="sxs-lookup"><span data-stu-id="f86b0-132">However, SignalR is unable to set these headers in browsers when using some transports.</span></span> <span data-ttu-id="f86b0-133">使用 Websocket 和 Server-Sent 事件時，會以查詢字串參數的形式傳送權杖。</span><span class="sxs-lookup"><span data-stu-id="f86b0-133">When using WebSockets and Server-Sent Events, the token is transmitted as a query string parameter.</span></span> 

#### <a name="built-in-jwt-authentication"></a><span data-ttu-id="f86b0-134">內建 JWT 驗證</span><span class="sxs-lookup"><span data-stu-id="f86b0-134">Built-in JWT authentication</span></span>

<span data-ttu-id="f86b0-135">在伺服器上，持有人權杖驗證是使用 [JWT 持有人中介軟體](xref:Microsoft.Extensions.DependencyInjection.JwtBearerExtensions.AddJwtBearer%2A)進行設定：</span><span class="sxs-lookup"><span data-stu-id="f86b0-135">On the server, bearer token authentication is configured using the [JWT Bearer middleware](xref:Microsoft.Extensions.DependencyInjection.JwtBearerExtensions.AddJwtBearer%2A):</span></span>

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

> [!NOTE]
> <span data-ttu-id="f86b0-136">因為瀏覽器 API 的限制，使用 Websocket 和 Server-Sent 事件時，會在瀏覽器上使用查詢字串。</span><span class="sxs-lookup"><span data-stu-id="f86b0-136">The query string is used on browsers when connecting with WebSockets and Server-Sent Events due to browser API limitations.</span></span> <span data-ttu-id="f86b0-137">使用 HTTPS 時，查詢字串值是由 TLS 連接所保護。</span><span class="sxs-lookup"><span data-stu-id="f86b0-137">When using HTTPS, query string values are secured by the TLS connection.</span></span> <span data-ttu-id="f86b0-138">但是，許多伺服器會記錄查詢字串值。</span><span class="sxs-lookup"><span data-stu-id="f86b0-138">However, many servers log query string values.</span></span> <span data-ttu-id="f86b0-139">如需詳細資訊，請參閱[ASP.NET Core SignalR 中的安全性考慮](xref:signalr/security)。</span><span class="sxs-lookup"><span data-stu-id="f86b0-139">For more information, see [Security considerations in ASP.NET Core SignalR](xref:signalr/security).</span></span> <span data-ttu-id="f86b0-140">SignalR 使用標頭在支援這些權杖的環境中傳輸權杖 (例如 .NET 和 JAVA 用戶端) 。</span><span class="sxs-lookup"><span data-stu-id="f86b0-140">SignalR uses headers to transmit tokens in environments which support them (such as the .NET and Java clients).</span></span>

#### <a name="identity-server-jwt-authentication"></a><span data-ttu-id="f86b0-141">Identity 伺服器 JWT 驗證</span><span class="sxs-lookup"><span data-stu-id="f86b0-141">Identity Server JWT authentication</span></span>

<span data-ttu-id="f86b0-142">使用 Identity 伺服器時，請將 <xref:Microsoft.Extensions.Options.PostConfigureOptions%601> 服務新增至專案：</span><span class="sxs-lookup"><span data-stu-id="f86b0-142">When using Identity Server, add a <xref:Microsoft.Extensions.Options.PostConfigureOptions%601> service to the project:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.Extensions.Options;
public class ConfigureJwtBearerOptions : IPostConfigureOptions<JwtBearerOptions>
{
    public void PostConfigure(string name, JwtBearerOptions options)
    {
        var originalOnMessageReceived = options.Events.OnMessageReceived;
        options.Events.OnMessageReceived = async context =>
        {
            await originalOnMessageReceived(context);
                
            if (string.IsNullOrEmpty(context.Token))
            {
                var accessToken = context.Request.Query["access_token"];
                var path = context.HttpContext.Request.Path;
                
                if (!string.IsNullOrEmpty(accessToken) && 
                    path.StartsWithSegments("/hubs"))
                {
                    context.Token = accessToken;
                }
            }
        };
    }
}
```

<span data-ttu-id="f86b0-143">在 `Startup.ConfigureServices` 新增驗證 (<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A>) 和 Identity 伺服器 () 的驗證處理常式之後，在中註冊服務 <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> ：</span><span class="sxs-lookup"><span data-stu-id="f86b0-143">Register the service in `Startup.ConfigureServices` after adding services for authentication (<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A>) and the authentication handler for Identity Server (<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>):</span></span>

```csharp
services.AddAuthentication()
    .AddIdentityServerJwt();
services.TryAddEnumerable(
    ServiceDescriptor.Singleton<IPostConfigureOptions<JwtBearerOptions>, 
        ConfigureJwtBearerOptions>());
```

### <a name="cookies-vs-bearer-tokens"></a><span data-ttu-id="f86b0-144">Cookie和持有人權杖</span><span class="sxs-lookup"><span data-stu-id="f86b0-144">Cookies vs. bearer tokens</span></span> 

<span data-ttu-id="f86b0-145">Cookie是瀏覽器專用的。</span><span class="sxs-lookup"><span data-stu-id="f86b0-145">Cookies are specific to browsers.</span></span> <span data-ttu-id="f86b0-146">相較于傳送持有人權杖，從其他類型的用戶端傳送這些用戶端會增加複雜性。</span><span class="sxs-lookup"><span data-stu-id="f86b0-146">Sending them from other kinds of clients adds complexity compared to sending bearer tokens.</span></span> <span data-ttu-id="f86b0-147">因此， cookie 除非應用程式只需要從瀏覽器用戶端驗證使用者，否則不建議進行驗證。</span><span class="sxs-lookup"><span data-stu-id="f86b0-147">Consequently, cookie authentication isn't recommended unless the app only needs to authenticate users from the browser client.</span></span> <span data-ttu-id="f86b0-148">使用瀏覽器用戶端以外的用戶端時，建議使用持有人權杖驗證。</span><span class="sxs-lookup"><span data-stu-id="f86b0-148">Bearer token authentication is the recommended approach when using clients other than the browser client.</span></span>

### <a name="windows-authentication"></a><span data-ttu-id="f86b0-149">Windows 驗證</span><span class="sxs-lookup"><span data-stu-id="f86b0-149">Windows authentication</span></span>

<span data-ttu-id="f86b0-150">如果您的應用程式中已設定 [Windows 驗證](xref:security/authentication/windowsauth) ，則 SignalR 可以使用該身分識別來保護中樞。</span><span class="sxs-lookup"><span data-stu-id="f86b0-150">If [Windows authentication](xref:security/authentication/windowsauth) is configured in your app, SignalR can use that identity to secure hubs.</span></span> <span data-ttu-id="f86b0-151">不過，若要將訊息傳送給個別使用者，您需要新增自訂使用者識別碼提供者。</span><span class="sxs-lookup"><span data-stu-id="f86b0-151">However, to send messages to individual users, you need to add a custom User ID provider.</span></span> <span data-ttu-id="f86b0-152">Windows 驗證系統未提供「名稱識別碼」宣告。</span><span class="sxs-lookup"><span data-stu-id="f86b0-152">The Windows authentication system doesn't provide the "Name Identifier" claim.</span></span> <span data-ttu-id="f86b0-153">SignalR 使用宣告來判斷使用者名稱。</span><span class="sxs-lookup"><span data-stu-id="f86b0-153">SignalR uses the claim to determine the user name.</span></span>

<span data-ttu-id="f86b0-154">加入新的類別，此類別會實 `IUserIdProvider` 作為識別碼，並從使用者取得其中一個宣告。</span><span class="sxs-lookup"><span data-stu-id="f86b0-154">Add a new class that implements `IUserIdProvider` and retrieve one of the claims from the user to use as the identifier.</span></span> <span data-ttu-id="f86b0-155">例如，若要使用 "Name" 宣告 (是) 表單中的 Windows 使用者名稱 `[Domain]\[Username]` ，請建立下列類別：</span><span class="sxs-lookup"><span data-stu-id="f86b0-155">For example, to use the "Name" claim (which is the Windows username in the form `[Domain]\[Username]`), create the following class:</span></span>

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

<span data-ttu-id="f86b0-156">`ClaimTypes.Name`您可以使用 (中的任何值， `User` 例如 Windows SID 識別碼等等) 。</span><span class="sxs-lookup"><span data-stu-id="f86b0-156">Rather than `ClaimTypes.Name`, you can use any value from the `User` (such as the Windows SID identifier, and so on).</span></span>

> [!NOTE]
> <span data-ttu-id="f86b0-157">您所選擇的值在系統中的所有使用者之間必須是唯一的。</span><span class="sxs-lookup"><span data-stu-id="f86b0-157">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="f86b0-158">否則，適用于某位使用者的訊息最後可能會前往不同的使用者。</span><span class="sxs-lookup"><span data-stu-id="f86b0-158">Otherwise, a message intended for one user could end up going to a different user.</span></span>

<span data-ttu-id="f86b0-159">在您的方法中註冊此元件 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="f86b0-159">Register this component in your `Startup.ConfigureServices` method.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

<span data-ttu-id="f86b0-160">在 .NET 用戶端中，您必須藉由設定 [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) 屬性來啟用 Windows 驗證：</span><span class="sxs-lookup"><span data-stu-id="f86b0-160">In the .NET Client, Windows Authentication must be enabled by setting the [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) property:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

<span data-ttu-id="f86b0-161">Internet Explorer 和 Microsoft Edge 都支援 Windows 驗證，但並非所有瀏覽器都支援。</span><span class="sxs-lookup"><span data-stu-id="f86b0-161">Windows authentication is supported in Internet Explorer and Microsoft Edge, but not in all browsers.</span></span> <span data-ttu-id="f86b0-162">例如，在 Chrome 和 Safari 中，嘗試使用 Windows 驗證和 Websocket 會失敗。</span><span class="sxs-lookup"><span data-stu-id="f86b0-162">For example, in Chrome and Safari, attempting to use Windows authentication and WebSockets fails.</span></span> <span data-ttu-id="f86b0-163">當 Windows 驗證失敗時，用戶端會嘗試切換回可能運作的其他傳輸。</span><span class="sxs-lookup"><span data-stu-id="f86b0-163">When Windows authentication fails, the client attempts to fall back to other transports which might work.</span></span>

### <a name="use-claims-to-customize-identity-handling"></a><span data-ttu-id="f86b0-164">使用宣告來自訂身分識別處理</span><span class="sxs-lookup"><span data-stu-id="f86b0-164">Use claims to customize identity handling</span></span>

<span data-ttu-id="f86b0-165">驗證使用者的應用程式可以 SignalR 從使用者宣告衍生使用者識別碼。</span><span class="sxs-lookup"><span data-stu-id="f86b0-165">An app that authenticates users can derive SignalR user IDs from user claims.</span></span> <span data-ttu-id="f86b0-166">若要指定如何 SignalR 建立使用者識別碼，請執行 `IUserIdProvider` 並註冊執行。</span><span class="sxs-lookup"><span data-stu-id="f86b0-166">To specify how SignalR creates user IDs, implement `IUserIdProvider` and register the implementation.</span></span>

<span data-ttu-id="f86b0-167">範例程式碼會示範如何使用宣告來選取使用者的電子郵件地址做為識別屬性。</span><span class="sxs-lookup"><span data-stu-id="f86b0-167">The sample code demonstrates how you would use claims to select the user's email address as the identifying property.</span></span> 

> [!NOTE]
> <span data-ttu-id="f86b0-168">您所選擇的值在系統中的所有使用者之間必須是唯一的。</span><span class="sxs-lookup"><span data-stu-id="f86b0-168">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="f86b0-169">否則，適用于某位使用者的訊息最後可能會前往不同的使用者。</span><span class="sxs-lookup"><span data-stu-id="f86b0-169">Otherwise, a message intended for one user could end up going to a different user.</span></span>

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

<span data-ttu-id="f86b0-170">帳戶註冊會將具有類型的宣告新增 `ClaimsTypes.Email` 至 ASP.NET identity 資料庫。</span><span class="sxs-lookup"><span data-stu-id="f86b0-170">The account registration adds a claim with type `ClaimsTypes.Email` to the ASP.NET identity database.</span></span>

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

<span data-ttu-id="f86b0-171">在中註冊此元件 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="f86b0-171">Register this component in your `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a><span data-ttu-id="f86b0-172">授權使用者存取中樞和中樞方法</span><span class="sxs-lookup"><span data-stu-id="f86b0-172">Authorize users to access hubs and hub methods</span></span>

<span data-ttu-id="f86b0-173">根據預設，中樞內的所有方法都可由未經驗證的使用者呼叫。</span><span class="sxs-lookup"><span data-stu-id="f86b0-173">By default, all methods in a hub can be called by an unauthenticated user.</span></span> <span data-ttu-id="f86b0-174">若要要求驗證，請將 [授權](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) 屬性套用至中樞：</span><span class="sxs-lookup"><span data-stu-id="f86b0-174">To require authentication, apply the [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) attribute to the hub:</span></span>

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

<span data-ttu-id="f86b0-175">您可以使用屬性的函式引數和屬性， `[Authorize]` 將存取限制為僅符合特定 [授權原則](xref:security/authorization/policies)的使用者。</span><span class="sxs-lookup"><span data-stu-id="f86b0-175">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="f86b0-176">例如，如果您有一個名為的自訂授權原則， `MyAuthorizationPolicy` 您可以確保只有符合該原則的使用者可以使用下列程式碼來存取中樞：</span><span class="sxs-lookup"><span data-stu-id="f86b0-176">For example, if you have a custom authorization policy called `MyAuthorizationPolicy` you can ensure that only users matching that policy can access the hub using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub : Hub
{
}
```

<span data-ttu-id="f86b0-177">個別的中樞方法也可以套用 `[Authorize]` 屬性。</span><span class="sxs-lookup"><span data-stu-id="f86b0-177">Individual hub methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="f86b0-178">如果目前使用者不符合套用至方法的原則，則會將錯誤傳回給呼叫者：</span><span class="sxs-lookup"><span data-stu-id="f86b0-178">If the current user doesn't match the policy applied to the method, an error is returned to the caller:</span></span>

```csharp
[Authorize]
public class ChatHub : Hub
{
    public async Task Send(string message)
    {
        // ... send a message to all users ...
    }

    [Authorize("Administrators")]
    public void BanUser(string userName)
    {
        // ... ban a user from the chat room (something only Administrators can do) ...
    }
}
```

::: moniker range=">= aspnetcore-3.0"

### <a name="use-authorization-handlers-to-customize-hub-method-authorization"></a><span data-ttu-id="f86b0-179">使用授權處理常式自訂中樞方法授權</span><span class="sxs-lookup"><span data-stu-id="f86b0-179">Use authorization handlers to customize hub method authorization</span></span>

<span data-ttu-id="f86b0-180">SignalR 當中樞方法需要授權時，提供自訂資源給授權處理常式。</span><span class="sxs-lookup"><span data-stu-id="f86b0-180">SignalR provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="f86b0-181">資源是的實例 `HubInvocationContext` 。</span><span class="sxs-lookup"><span data-stu-id="f86b0-181">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="f86b0-182">包括、所叫用 `HubInvocationContext` `HubCallerContext` 中樞方法的名稱，以及中樞方法的引數。</span><span class="sxs-lookup"><span data-stu-id="f86b0-182">The `HubInvocationContext` includes the `HubCallerContext`, the name of the hub method being invoked, and the arguments to the hub method.</span></span>

<span data-ttu-id="f86b0-183">請考慮允許透過 Azure Active Directory 登入多個組織的聊天室範例。</span><span class="sxs-lookup"><span data-stu-id="f86b0-183">Consider the example of a chat room allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="f86b0-184">具有 Microsoft 帳戶的任何人都可以登入聊天，但只有擁有組織的成員才能禁止使用者或查看使用者的聊天記錄。</span><span class="sxs-lookup"><span data-stu-id="f86b0-184">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization should be able to ban users or view users' chat histories.</span></span> <span data-ttu-id="f86b0-185">此外，我們可能會想要限制特定使用者的特定功能。</span><span class="sxs-lookup"><span data-stu-id="f86b0-185">Furthermore, we might want to restrict certain functionality from certain users.</span></span> <span data-ttu-id="f86b0-186">使用 ASP.NET Core 3.0 中更新的功能，這是完全可行的。</span><span class="sxs-lookup"><span data-stu-id="f86b0-186">Using the updated features in ASP.NET Core 3.0, this is entirely possible.</span></span> <span data-ttu-id="f86b0-187">請注意如何 `DomainRestrictedRequirement` 作為自訂 `IAuthorizationRequirement` 。</span><span class="sxs-lookup"><span data-stu-id="f86b0-187">Note how the `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="f86b0-188">現在 `HubInvocationContext` 已傳入資源參數，內部邏輯可以檢查正在呼叫中樞的內容，並決定是否要讓使用者執行個別的中樞方法。</span><span class="sxs-lookup"><span data-stu-id="f86b0-188">Now that the `HubInvocationContext` resource parameter is being passed in, the internal logic can inspect the context in which the Hub is being called and make decisions on allowing the user to execute individual Hub methods.</span></span>

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

public class DomainRestrictedRequirement : 
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>, 
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement, 
        HubInvocationContext resource)
    {
        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name) && 
            context.User.Identity.Name.EndsWith("@microsoft.com"))
        {
            context.Succeed(requirement);
        }
        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName,
        string currentUsername)
    {
        return !(currentUsername.Equals("asdf42@microsoft.com") && 
            hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase));
    }
}
```

<span data-ttu-id="f86b0-189">在中 `Startup.ConfigureServices` ，新增新的原則，並提供自訂 `DomainRestrictedRequirement` 需求作為用來建立 `DomainRestricted` 原則的參數。</span><span class="sxs-lookup"><span data-stu-id="f86b0-189">In `Startup.ConfigureServices`, add the new policy, providing the custom `DomainRestrictedRequirement` requirement as a parameter to create the `DomainRestricted` policy.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services
        .AddAuthorization(options =>
        {
            options.AddPolicy("DomainRestricted", policy =>
            {
                policy.Requirements.Add(new DomainRestrictedRequirement());
            });
        });
}
```

<span data-ttu-id="f86b0-190">在上述範例中， `DomainRestrictedRequirement` 類別同時是 `IAuthorizationRequirement` 和它本身 `AuthorizationHandler` 的需求。</span><span class="sxs-lookup"><span data-stu-id="f86b0-190">In the preceding example, the `DomainRestrictedRequirement` class is both an `IAuthorizationRequirement` and its own `AuthorizationHandler` for that requirement.</span></span> <span data-ttu-id="f86b0-191">您可以將這兩個元件分割成不同的類別，以分開考慮。</span><span class="sxs-lookup"><span data-stu-id="f86b0-191">It's acceptable to split these two components into separate classes to separate concerns.</span></span> <span data-ttu-id="f86b0-192">範例方法的優點是不需要 `AuthorizationHandler` 在啟動期間插入，因為需求和處理常式是相同的。</span><span class="sxs-lookup"><span data-stu-id="f86b0-192">A benefit of the example's approach is there's no need to inject the `AuthorizationHandler` during startup, as the requirement and the handler are the same thing.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="f86b0-193">其他資源</span><span class="sxs-lookup"><span data-stu-id="f86b0-193">Additional resources</span></span>

* [<span data-ttu-id="f86b0-194">ASP.NET Core 中的持有人權杖驗證</span><span class="sxs-lookup"><span data-stu-id="f86b0-194">Bearer Token Authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="f86b0-195">以資源為基礎的授權</span><span class="sxs-lookup"><span data-stu-id="f86b0-195">Resource-based Authorization</span></span>](xref:security/authorization/resourcebased)
