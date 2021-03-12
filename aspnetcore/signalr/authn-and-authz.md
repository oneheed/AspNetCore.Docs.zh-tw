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
# <a name="authentication-and-authorization-in-aspnet-core-signalr"></a>ASP.NET Core 中的驗證和授權 SignalR

[Andrew Stanton-護士](https://twitter.com/anurse)

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/signalr/authn-and-authz/sample/) [ (如何下載) ](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-connecting-to-a-signalr-hub"></a>驗證連接至中樞的使用者 SignalR

SignalR 可以與 [ASP.NET Core 驗證](xref:security/authentication/identity) 搭配使用，以將使用者與每個連接產生關聯。 在中樞中，可以從 HubConnectionCoNtext 存取驗證資料。 [使用者](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) 屬性。 驗證可讓中樞在與使用者相關聯的所有連接上呼叫方法。 如需詳細資訊，請參閱[中 SignalR 的管理使用者和群組](xref:signalr/groups)。 多個連接可能與單一使用者相關聯。

以下是 `Startup.Configure` 使用 SignalR 和 ASP.NET 核心驗證的範例：

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
> 您註冊 SignalR 和 ASP.NET 核心驗證中介軟體重要的順序。 一律在 `UseAuthentication` 之前呼叫 `UseSignalR` ，在 SignalR 上有使用者 `HttpContext` 。

::: moniker-end

### <a name="cookie-authentication"></a>Cookie 驗證

在以瀏覽器為基礎的應用程式中， cookie 驗證可讓您現有的使用者認證自動流向連線 SignalR 。 使用瀏覽器用戶端時，不需要進行其他設定。 如果使用者已登入您的應用程式，則 SignalR 連接會自動繼承此驗證。

Cookie是以瀏覽器特定的方式傳送存取權杖，但是非瀏覽器用戶端可以傳送存取權杖。 使用 [.Net 用戶端](xref:signalr/dotnet-client)時， `Cookies` 可以在呼叫中設定屬性， `.WithUrl` 以提供 cookie 。 不過， cookie 若要使用來自 .net 用戶端的驗證，應用程式會要求應用程式提供 API，以交換的驗證資料 cookie 。

### <a name="bearer-token-authentication"></a>持有人權杖驗證

用戶端可以提供存取權杖，而不是使用 cookie 。 伺服器會驗證權杖，並使用它來識別使用者。 只有在建立連接時，才會進行這項驗證。 在連線的存留期內，伺服器不會自動重新驗證以檢查權杖撤銷。

在 JavaScript 用戶端中，您可以使用 [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) 選項來提供權杖。

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=52-55)]

在 .NET 用戶端中，有一個類似的 [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) 屬性可用來設定權杖：

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> 您提供的存取權杖函式會在 **每個** 發出的 HTTP 要求之前呼叫 SignalR 。 如果您需要更新權杖以便讓連接保持使用中 (，因為它可能會在連線) 期間過期，請從這個函式中執行，並傳回更新的權杖。

在標準 web Api 中，會在 HTTP 標頭中傳送持有人權杖。 不過， SignalR 使用某些傳輸時，無法在瀏覽器中設定這些標頭。 使用 Websocket 和 Server-Sent 事件時，會以查詢字串參數的形式傳送權杖。 

#### <a name="built-in-jwt-authentication"></a>內建 JWT 驗證

在伺服器上，持有人權杖驗證是使用 [JWT 持有人中介軟體](xref:Microsoft.Extensions.DependencyInjection.JwtBearerExtensions.AddJwtBearer%2A)進行設定：

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

> [!NOTE]
> 因為瀏覽器 API 的限制，使用 Websocket 和 Server-Sent 事件時，會在瀏覽器上使用查詢字串。 使用 HTTPS 時，查詢字串值是由 TLS 連接所保護。 但是，許多伺服器會記錄查詢字串值。 如需詳細資訊，請參閱[ASP.NET Core SignalR 中的安全性考慮](xref:signalr/security)。 SignalR 使用標頭在支援這些權杖的環境中傳輸權杖 (例如 .NET 和 JAVA 用戶端) 。

#### <a name="identity-server-jwt-authentication"></a>Identity 伺服器 JWT 驗證

使用 Identity 伺服器時，請將 <xref:Microsoft.Extensions.Options.PostConfigureOptions%601> 服務新增至專案：

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

在 `Startup.ConfigureServices` 新增驗證 (<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A>) 和 Identity 伺服器 () 的驗證處理常式之後，在中註冊服務 <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> ：

```csharp
services.AddAuthentication()
    .AddIdentityServerJwt();
services.TryAddEnumerable(
    ServiceDescriptor.Singleton<IPostConfigureOptions<JwtBearerOptions>, 
        ConfigureJwtBearerOptions>());
```

### <a name="cookies-vs-bearer-tokens"></a>Cookie和持有人權杖 

Cookie是瀏覽器專用的。 相較于傳送持有人權杖，從其他類型的用戶端傳送這些用戶端會增加複雜性。 因此， cookie 除非應用程式只需要從瀏覽器用戶端驗證使用者，否則不建議進行驗證。 使用瀏覽器用戶端以外的用戶端時，建議使用持有人權杖驗證。

### <a name="windows-authentication"></a>Windows 驗證

如果您的應用程式中已設定 [Windows 驗證](xref:security/authentication/windowsauth) ，則 SignalR 可以使用該身分識別來保護中樞。 不過，若要將訊息傳送給個別使用者，您需要新增自訂使用者識別碼提供者。 Windows 驗證系統未提供「名稱識別碼」宣告。 SignalR 使用宣告來判斷使用者名稱。

加入新的類別，此類別會實 `IUserIdProvider` 作為識別碼，並從使用者取得其中一個宣告。 例如，若要使用 "Name" 宣告 (是) 表單中的 Windows 使用者名稱 `[Domain]\[Username]` ，請建立下列類別：

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

`ClaimTypes.Name`您可以使用 (中的任何值， `User` 例如 Windows SID 識別碼等等) 。

> [!NOTE]
> 您所選擇的值在系統中的所有使用者之間必須是唯一的。 否則，適用于某位使用者的訊息最後可能會前往不同的使用者。

在您的方法中註冊此元件 `Startup.ConfigureServices` 。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

在 .NET 用戶端中，您必須藉由設定 [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) 屬性來啟用 Windows 驗證：

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

Internet Explorer 和 Microsoft Edge 都支援 Windows 驗證，但並非所有瀏覽器都支援。 例如，在 Chrome 和 Safari 中，嘗試使用 Windows 驗證和 Websocket 會失敗。 當 Windows 驗證失敗時，用戶端會嘗試切換回可能運作的其他傳輸。

### <a name="use-claims-to-customize-identity-handling"></a>使用宣告來自訂身分識別處理

驗證使用者的應用程式可以 SignalR 從使用者宣告衍生使用者識別碼。 若要指定如何 SignalR 建立使用者識別碼，請執行 `IUserIdProvider` 並註冊執行。

範例程式碼會示範如何使用宣告來選取使用者的電子郵件地址做為識別屬性。 

> [!NOTE]
> 您所選擇的值在系統中的所有使用者之間必須是唯一的。 否則，適用于某位使用者的訊息最後可能會前往不同的使用者。

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

帳戶註冊會將具有類型的宣告新增 `ClaimsTypes.Email` 至 ASP.NET identity 資料庫。

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

在中註冊此元件 `Startup.ConfigureServices` 。

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a>授權使用者存取中樞和中樞方法

根據預設，中樞內的所有方法都可由未經驗證的使用者呼叫。 若要要求驗證，請將 [授權](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) 屬性套用至中樞：

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

您可以使用屬性的函式引數和屬性， `[Authorize]` 將存取限制為僅符合特定 [授權原則](xref:security/authorization/policies)的使用者。 例如，如果您有一個名為的自訂授權原則， `MyAuthorizationPolicy` 您可以確保只有符合該原則的使用者可以使用下列程式碼來存取中樞：

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub : Hub
{
}
```

個別的中樞方法也可以套用 `[Authorize]` 屬性。 如果目前使用者不符合套用至方法的原則，則會將錯誤傳回給呼叫者：

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

### <a name="use-authorization-handlers-to-customize-hub-method-authorization"></a>使用授權處理常式自訂中樞方法授權

SignalR 當中樞方法需要授權時，提供自訂資源給授權處理常式。 資源是的實例 `HubInvocationContext` 。 包括、所叫用 `HubInvocationContext` `HubCallerContext` 中樞方法的名稱，以及中樞方法的引數。

請考慮允許透過 Azure Active Directory 登入多個組織的聊天室範例。 具有 Microsoft 帳戶的任何人都可以登入聊天，但只有擁有組織的成員才能禁止使用者或查看使用者的聊天記錄。 此外，我們可能會想要限制特定使用者的特定功能。 使用 ASP.NET Core 3.0 中更新的功能，這是完全可行的。 請注意如何 `DomainRestrictedRequirement` 作為自訂 `IAuthorizationRequirement` 。 現在 `HubInvocationContext` 已傳入資源參數，內部邏輯可以檢查正在呼叫中樞的內容，並決定是否要讓使用者執行個別的中樞方法。

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

在中 `Startup.ConfigureServices` ，新增新的原則，並提供自訂 `DomainRestrictedRequirement` 需求作為用來建立 `DomainRestricted` 原則的參數。

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

在上述範例中， `DomainRestrictedRequirement` 類別同時是 `IAuthorizationRequirement` 和它本身 `AuthorizationHandler` 的需求。 您可以將這兩個元件分割成不同的類別，以分開考慮。 範例方法的優點是不需要 `AuthorizationHandler` 在啟動期間插入，因為需求和處理常式是相同的。

::: moniker-end

## <a name="additional-resources"></a>其他資源

* [ASP.NET Core 中的持有人權杖驗證](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [以資源為基礎的授權](xref:security/authorization/resourcebased)
