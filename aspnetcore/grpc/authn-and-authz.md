---
title: 適用于 ASP.NET Core 的 gRPC 中的驗證和授權
author: jamesnk
description: 瞭解如何在 gRPC for ASP.NET Core 中使用驗證和授權。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 05/26/2020
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
uid: grpc/authn-and-authz
ms.openlocfilehash: 5120964459a81ef2d668877bb08ecde512c2389d
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587992"
---
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a>適用于 ASP.NET Core 的 gRPC 中的驗證和授權

依 [James 牛頓](https://twitter.com/jamesnk)

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/grpc/authn-and-authz/sample/) [ (如何下載) ](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-calling-a-grpc-service"></a>驗證呼叫 gRPC 服務的使用者

gRPC 可與 [ASP.NET Core 驗證](xref:security/authentication/identity) 搭配使用，以將使用者與每個呼叫產生關聯。

以下是 `Startup.Configure` 使用 gRPC 和 ASP.NET Core 驗證的範例：

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseRouting();
    
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapGrpcService<GreeterService>();
    });
}
```

> [!NOTE]
> 您註冊 ASP.NET Core 驗證中介軟體重要的順序。 一律 `UseAuthentication` 在前後 `UseAuthorization` 呼叫 `UseRouting` `UseEndpoints` 。

需要設定您的應用程式在呼叫期間使用的驗證機制。 會在中新增驗證設定 `Startup.ConfigureServices` ，並且會根據您的應用程式所使用的驗證機制而有所不同。 如需如何保護 ASP.NET Core 應用程式的範例，請參閱 [驗證範例](xref:security/authentication/samples)。

設定好驗證之後，即可透過 gRPC 服務方法存取使用者 `ServerCallContext` 。

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a>持有人權杖驗證

用戶端可以提供存取權杖來進行驗證。 伺服器會驗證權杖，並使用它來識別使用者。

在伺服器上，會使用 [JWT 持有人中介軟體](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer)來設定持有人權杖驗證。

在 .NET gRPC 用戶端中，您可以使用集合以呼叫傳送權杖 `Metadata` 。 集合中的專案 `Metadata` 會以 gRPC 呼叫作為 HTTP 標頭來傳送：

```csharp
public bool DoAuthenticatedCall(
    Ticketer.TicketerClient client, string token)
{
    var headers = new Metadata();
    headers.Add("Authorization", $"Bearer {token}");

    var request = new BuyTicketsRequest { Count = 1 };
    var response = await client.BuyTicketsAsync(request, headers);

    return response.Success;
}
```

`ChannelCredentials`在通道上設定是使用 gRPC 呼叫將權杖傳送給服務的另一種方式。 `ChannelCredentials`可以包含 `CallCredentials` ，以提供自動設定的方式 `Metadata` 。

`CallCredentials` 會在每次進行 gRPC 呼叫時執行，這樣可避免需要在多個位置撰寫程式碼來自行傳遞權杖。 請注意， `CallCredentials` 只有在使用 TLS 保護通道時，才會套用。 `CallCredentials` 不會套用到不安全的非 TLS 通道。

下列範例中的認證會設定通道，以使用每個 gRPC 呼叫來傳送權杖：

```csharp
private static GrpcChannel CreateAuthenticatedChannel(string address)
{
    var credentials = CallCredentials.FromInterceptor((context, metadata) =>
    {
        if (!string.IsNullOrEmpty(_token))
        {
            metadata.Add("Authorization", $"Bearer {_token}");
        }
        return Task.CompletedTask;
    });

    // SslCredentials is used here because this channel is using TLS.
    // CallCredentials can't be used with ChannelCredentials.Insecure on non-TLS channels.
    var channel = GrpcChannel.ForAddress(address, new GrpcChannelOptions
    {
        Credentials = ChannelCredentials.Create(new SslCredentials(), credentials)
    });
    return channel;
}
```

### <a name="client-certificate-authentication"></a>用戶端憑證驗證

用戶端也可以提供用戶端憑證來進行驗證。 [憑證驗證](https://tools.ietf.org/html/rfc5246#section-7.4.4) 會在 TLS 層級進行，但在到達 ASP.NET Core 之前也是如此。 當要求進入 ASP.NET Core 時， [用戶端憑證驗證封裝](xref:security/authentication/certauth) 可讓您將憑證解析為 `ClaimsPrincipal` 。

> [!NOTE]
> 將伺服器設定為接受用戶端憑證。 如需在 Kestrel、IIS 和 Azure 中接受用戶端憑證的相關資訊，請參閱 <xref:security/authentication/certauth#configure-your-server-to-require-certificates> 。

在 .NET gRPC 用戶端中，會將用戶端憑證新增至 `HttpClientHandler` 之後用來建立 gRPC 用戶端的憑證：

```csharp
public Ticketer.TicketerClient CreateClientWithCert(
    string baseAddress,
    X509Certificate2 certificate)
{
    // Add client cert to the handler
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(certificate);

    // Create the gRPC channel
    var channel = GrpcChannel.ForAddress(baseAddress, new GrpcChannelOptions
    {
        HttpHandler = handler
    });

    return new Ticketer.TicketerClient(channel);
}
```

### <a name="other-authentication-mechanisms"></a>其他驗證機制

許多 ASP.NET 核心支援的驗證機制可與 gRPC 搭配運作：

* Azure Active Directory
* 用戶端憑證
* Identity伺服器
* JWT 權杖
* OAuth 2.0
* OpenID Connect
* WS-同盟

如需在伺服器上設定驗證的詳細資訊，請參閱 [ASP.NET Core authentication](xref:security/authentication/identity)。

將 gRPC 用戶端設定為使用驗證將取決於您所使用的驗證機制。 先前的持有人權杖和用戶端憑證範例顯示了幾種方法，可讓您使用 gRPC 呼叫來設定 gRPC 用戶端傳送驗證中繼資料：

* 強型別 gRPC 用戶端會 `HttpClient` 在內部使用。 您可以在 [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler)上設定驗證，或將自訂 [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) 實例加入至 `HttpClient` 。
* 每個 gRPC 呼叫都有一個選擇性 `CallOptions` 引數。 您可以使用選項的標頭集合來傳送自訂標頭。

> [!NOTE]
> Windows 驗證 (NTLM/Kerberos/Negotiate) 無法與 gRPC 搭配使用。 gRPC 需要 HTTP/2，而 HTTP/2 不支援 Windows 驗證。

## <a name="authorize-users-to-access-services-and-service-methods"></a>授權使用者存取服務和服務方法

根據預設，服務中的所有方法都可由未經驗證的使用者呼叫。 若要要求驗證，請將 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 屬性套用至服務：

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

您可以使用屬性的函式引數和屬性， `[Authorize]` 將存取限制為僅符合特定 [授權原則](xref:security/authorization/policies)的使用者。 例如，如果您有一個名為的自訂授權原則 `MyAuthorizationPolicy` ，請確定只有符合該原則的使用者可以使用下列程式碼來存取服務：

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

個別的服務方法也可以套用 `[Authorize]` 屬性。 如果目前使用者不符合 **同時** 套用至方法和類別的原則，則會將錯誤傳回給呼叫者：

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
    public override Task<AvailableTicketsResponse> GetAvailableTickets(
        Empty request, ServerCallContext context)
    {
        // ... buy tickets for the current user ...
    }

    [Authorize("Administrators")]
    public override Task<BuyTicketsResponse> RefundTickets(
        BuyTicketsRequest request, ServerCallContext context)
    {
        // ... refund tickets (something only Administrators can do) ..
    }
}
```

## <a name="additional-resources"></a>其他資源

* [ASP.NET Core 中的持有人權杖驗證](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [在 ASP.NET Core 中設定用戶端憑證驗證](xref:security/authentication/certauth)
