---
title: GRPC 中的驗證和授權 ASP.NET Core
author: jamesnk
description: 瞭解如何在 gRPC 中針對 ASP.NET Core 使用驗證和授權。
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
ms.openlocfilehash: 833114a12c8c1ac67097b3592cf410d7a69bb628
ms.sourcegitcommit: bce62ceaac7782e22d185814f2e8532c84efa472
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94673974"
---
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a><span data-ttu-id="37a59-103">GRPC 中的驗證和授權 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="37a59-103">Authentication and authorization in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="37a59-104">依 [James 牛頓](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="37a59-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="37a59-105">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [ (如何下載) ](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="37a59-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-calling-a-grpc-service"></a><span data-ttu-id="37a59-106">驗證呼叫 gRPC 服務的使用者</span><span class="sxs-lookup"><span data-stu-id="37a59-106">Authenticate users calling a gRPC service</span></span>

<span data-ttu-id="37a59-107">gRPC 可搭配 [ASP.NET Core authentication](xref:security/authentication/identity) 使用，以將使用者與每個呼叫產生關聯。</span><span class="sxs-lookup"><span data-stu-id="37a59-107">gRPC can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each call.</span></span>

<span data-ttu-id="37a59-108">以下是 `Startup.Configure` 使用 gRPC 和 ASP.NET Core authentication 的範例：</span><span class="sxs-lookup"><span data-stu-id="37a59-108">The following is an example of `Startup.Configure` which uses gRPC and ASP.NET Core authentication:</span></span>

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
> <span data-ttu-id="37a59-109">您註冊 ASP.NET Core authentication 中介軟體重要的順序。</span><span class="sxs-lookup"><span data-stu-id="37a59-109">The order in which you register the ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="37a59-110">一律 `UseAuthentication` 在前後 `UseAuthorization` 呼叫 `UseRouting` `UseEndpoints` 。</span><span class="sxs-lookup"><span data-stu-id="37a59-110">Always call `UseAuthentication` and `UseAuthorization` after `UseRouting` and before `UseEndpoints`.</span></span>

<span data-ttu-id="37a59-111">需要設定您的應用程式在呼叫期間使用的驗證機制。</span><span class="sxs-lookup"><span data-stu-id="37a59-111">The authentication mechanism your app uses during a call needs to be configured.</span></span> <span data-ttu-id="37a59-112">會在中新增驗證設定 `Startup.ConfigureServices` ，並且會根據您的應用程式所使用的驗證機制而有所不同。</span><span class="sxs-lookup"><span data-stu-id="37a59-112">Authentication configuration is added in `Startup.ConfigureServices` and will be different depending upon the authentication mechanism your app uses.</span></span> <span data-ttu-id="37a59-113">如需如何保護 ASP.NET Core 應用程式的範例，請參閱 [驗證範例](xref:security/authentication/samples)。</span><span class="sxs-lookup"><span data-stu-id="37a59-113">For examples of how to secure ASP.NET Core apps, see [Authentication samples](xref:security/authentication/samples).</span></span>

<span data-ttu-id="37a59-114">設定好驗證之後，即可透過 gRPC 服務方法存取使用者 `ServerCallContext` 。</span><span class="sxs-lookup"><span data-stu-id="37a59-114">Once authentication has been setup, the user can be accessed in a gRPC service methods via the `ServerCallContext`.</span></span>

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a><span data-ttu-id="37a59-115">持有人權杖驗證</span><span class="sxs-lookup"><span data-stu-id="37a59-115">Bearer token authentication</span></span>

<span data-ttu-id="37a59-116">用戶端可以提供存取權杖來進行驗證。</span><span class="sxs-lookup"><span data-stu-id="37a59-116">The client can provide an access token for authentication.</span></span> <span data-ttu-id="37a59-117">伺服器會驗證權杖，並使用它來識別使用者。</span><span class="sxs-lookup"><span data-stu-id="37a59-117">The server validates the token and uses it to identify the user.</span></span>

<span data-ttu-id="37a59-118">在伺服器上，會使用 [JWT 持有人中介軟體](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer)來設定持有人權杖驗證。</span><span class="sxs-lookup"><span data-stu-id="37a59-118">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="37a59-119">在 .NET gRPC 用戶端中，您可以使用集合以呼叫傳送權杖 `Metadata` 。</span><span class="sxs-lookup"><span data-stu-id="37a59-119">In the .NET gRPC client, the token can be sent with calls by using the `Metadata` collection.</span></span> <span data-ttu-id="37a59-120">集合中的專案 `Metadata` 會以 gRPC 呼叫作為 HTTP 標頭來傳送：</span><span class="sxs-lookup"><span data-stu-id="37a59-120">Entries in the `Metadata` collection are sent with a gRPC call as HTTP headers:</span></span>

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

<span data-ttu-id="37a59-121">`ChannelCredentials`在通道上設定是使用 gRPC 呼叫將權杖傳送給服務的另一種方式。</span><span class="sxs-lookup"><span data-stu-id="37a59-121">Configuring `ChannelCredentials` on a channel is an alternative way to send the token to the service with gRPC calls.</span></span> <span data-ttu-id="37a59-122">`ChannelCredentials`可以包含 `CallCredentials` ，以提供自動設定的方式 `Metadata` 。</span><span class="sxs-lookup"><span data-stu-id="37a59-122">A `ChannelCredentials` can include `CallCredentials`, which provide a way to automatically set `Metadata`.</span></span>

<span data-ttu-id="37a59-123">`CallCredentials` 會在每次進行 gRPC 呼叫時執行，這樣可避免需要在多個位置撰寫程式碼來自行傳遞權杖。</span><span class="sxs-lookup"><span data-stu-id="37a59-123">`CallCredentials` is run each time a gRPC call is made, which avoids the need to write code in multiple places to pass the token yourself.</span></span> <span data-ttu-id="37a59-124">請注意， `CallCredentials` 只有在使用 TLS 保護通道時，才會套用。</span><span class="sxs-lookup"><span data-stu-id="37a59-124">Note that `CallCredentials` are only applied if the channel is secured with TLS.</span></span> <span data-ttu-id="37a59-125">`CallCredentials` 不會套用到不安全的非 TLS 通道。</span><span class="sxs-lookup"><span data-stu-id="37a59-125">`CallCredentials` aren't applied on unsecured non-TLS channels.</span></span>

<span data-ttu-id="37a59-126">下列範例中的認證會設定通道，以使用每個 gRPC 呼叫來傳送權杖：</span><span class="sxs-lookup"><span data-stu-id="37a59-126">The credential in the following example configures the channel to send the token with every gRPC call:</span></span>

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

### <a name="client-certificate-authentication"></a><span data-ttu-id="37a59-127">用戶端憑證驗證</span><span class="sxs-lookup"><span data-stu-id="37a59-127">Client certificate authentication</span></span>

<span data-ttu-id="37a59-128">用戶端也可以提供用戶端憑證來進行驗證。</span><span class="sxs-lookup"><span data-stu-id="37a59-128">A client could alternatively provide a client certificate for authentication.</span></span> <span data-ttu-id="37a59-129">[憑證驗證](https://tools.ietf.org/html/rfc5246#section-7.4.4) 會在 TLS 層級進行，但在達到 ASP.NET Core 之前也是如此。</span><span class="sxs-lookup"><span data-stu-id="37a59-129">[Certificate authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="37a59-130">當要求進入 ASP.NET Core 時， [用戶端憑證驗證封裝](xref:security/authentication/certauth) 可讓您將憑證解析為 `ClaimsPrincipal` 。</span><span class="sxs-lookup"><span data-stu-id="37a59-130">When the request enters ASP.NET Core, the [client certificate authentication package](xref:security/authentication/certauth) allows you to resolve the certificate to a `ClaimsPrincipal`.</span></span>

> [!NOTE]
> <span data-ttu-id="37a59-131">將伺服器設定為接受用戶端憑證。</span><span class="sxs-lookup"><span data-stu-id="37a59-131">Configure the server to accept client certificates.</span></span> <span data-ttu-id="37a59-132">如需在 Kestrel、IIS 和 Azure 中接受用戶端憑證的相關資訊，請參閱 <xref:security/authentication/certauth#configure-your-server-to-require-certificates> 。</span><span class="sxs-lookup"><span data-stu-id="37a59-132">For information on accepting client certificates in Kestrel, IIS, and Azure, see <xref:security/authentication/certauth#configure-your-server-to-require-certificates>.</span></span>

<span data-ttu-id="37a59-133">在 .NET gRPC 用戶端中，會將用戶端憑證新增至 `HttpClientHandler` 之後用來建立 gRPC 用戶端的憑證：</span><span class="sxs-lookup"><span data-stu-id="37a59-133">In the .NET gRPC client, the client certificate is added to `HttpClientHandler` that is then used to create the gRPC client:</span></span>

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

### <a name="other-authentication-mechanisms"></a><span data-ttu-id="37a59-134">其他驗證機制</span><span class="sxs-lookup"><span data-stu-id="37a59-134">Other authentication mechanisms</span></span>

<span data-ttu-id="37a59-135">許多 ASP.NET Core 支援的驗證機制可與 gRPC 搭配運作：</span><span class="sxs-lookup"><span data-stu-id="37a59-135">Many ASP.NET Core supported authentication mechanisms work with gRPC:</span></span>

* <span data-ttu-id="37a59-136">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="37a59-136">Azure Active Directory</span></span>
* <span data-ttu-id="37a59-137">用戶端憑證</span><span class="sxs-lookup"><span data-stu-id="37a59-137">Client Certificate</span></span>
* <span data-ttu-id="37a59-138">Identity伺服器</span><span class="sxs-lookup"><span data-stu-id="37a59-138">IdentityServer</span></span>
* <span data-ttu-id="37a59-139">JWT 權杖</span><span class="sxs-lookup"><span data-stu-id="37a59-139">JWT Token</span></span>
* <span data-ttu-id="37a59-140">OAuth 2.0</span><span class="sxs-lookup"><span data-stu-id="37a59-140">OAuth 2.0</span></span>
* <span data-ttu-id="37a59-141">OpenID Connect</span><span class="sxs-lookup"><span data-stu-id="37a59-141">OpenID Connect</span></span>
* <span data-ttu-id="37a59-142">WS-同盟</span><span class="sxs-lookup"><span data-stu-id="37a59-142">WS-Federation</span></span>

<span data-ttu-id="37a59-143">如需在伺服器上設定驗證的詳細資訊，請參閱 [ASP.NET Core authentication](xref:security/authentication/identity)。</span><span class="sxs-lookup"><span data-stu-id="37a59-143">For more information on configuring authentication on the server, see [ASP.NET Core authentication](xref:security/authentication/identity).</span></span>

<span data-ttu-id="37a59-144">將 gRPC 用戶端設定為使用驗證將取決於您所使用的驗證機制。</span><span class="sxs-lookup"><span data-stu-id="37a59-144">Configuring the gRPC client to use authentication will depend on the authentication mechanism you are using.</span></span> <span data-ttu-id="37a59-145">先前的持有人權杖和用戶端憑證範例顯示了幾種方法，可讓您使用 gRPC 呼叫來設定 gRPC 用戶端傳送驗證中繼資料：</span><span class="sxs-lookup"><span data-stu-id="37a59-145">The previous bearer token and client certificate examples show a couple of ways the gRPC client can be configured to send authentication metadata with gRPC calls:</span></span>

* <span data-ttu-id="37a59-146">強型別 gRPC 用戶端會 `HttpClient` 在內部使用。</span><span class="sxs-lookup"><span data-stu-id="37a59-146">Strongly typed gRPC clients use `HttpClient` internally.</span></span> <span data-ttu-id="37a59-147">您可以在 [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler)上設定驗證，或將自訂 [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) 實例加入至 `HttpClient` 。</span><span class="sxs-lookup"><span data-stu-id="37a59-147">Authentication can be configured on [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler), or by adding custom [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) instances to the `HttpClient`.</span></span>
* <span data-ttu-id="37a59-148">每個 gRPC 呼叫都有一個選擇性 `CallOptions` 引數。</span><span class="sxs-lookup"><span data-stu-id="37a59-148">Each gRPC call has an optional `CallOptions` argument.</span></span> <span data-ttu-id="37a59-149">您可以使用選項的標頭集合來傳送自訂標頭。</span><span class="sxs-lookup"><span data-stu-id="37a59-149">Custom headers can be sent using the option's headers collection.</span></span>

> [!NOTE]
> <span data-ttu-id="37a59-150">Windows 驗證 (NTLM/Kerberos/Negotiate) 無法與 gRPC 搭配使用。</span><span class="sxs-lookup"><span data-stu-id="37a59-150">Windows Authentication (NTLM/Kerberos/Negotiate) can't be used with gRPC.</span></span> <span data-ttu-id="37a59-151">gRPC 需要 HTTP/2，而 HTTP/2 不支援 Windows 驗證。</span><span class="sxs-lookup"><span data-stu-id="37a59-151">gRPC requires HTTP/2, and HTTP/2 doesn't support Windows Authentication.</span></span>

## <a name="authorize-users-to-access-services-and-service-methods"></a><span data-ttu-id="37a59-152">授權使用者存取服務和服務方法</span><span class="sxs-lookup"><span data-stu-id="37a59-152">Authorize users to access services and service methods</span></span>

<span data-ttu-id="37a59-153">根據預設，服務中的所有方法都可由未經驗證的使用者呼叫。</span><span class="sxs-lookup"><span data-stu-id="37a59-153">By default, all methods in a service can be called by unauthenticated users.</span></span> <span data-ttu-id="37a59-154">若要要求驗證，請將 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 屬性套用至服務：</span><span class="sxs-lookup"><span data-stu-id="37a59-154">To require authentication, apply the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the service:</span></span>

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="37a59-155">您可以使用屬性的函式引數和屬性， `[Authorize]` 將存取限制為僅符合特定 [授權原則](xref:security/authorization/policies)的使用者。</span><span class="sxs-lookup"><span data-stu-id="37a59-155">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="37a59-156">例如，如果您有一個名為的自訂授權原則 `MyAuthorizationPolicy` ，請確定只有符合該原則的使用者可以使用下列程式碼來存取服務：</span><span class="sxs-lookup"><span data-stu-id="37a59-156">For example, if you have a custom authorization policy called `MyAuthorizationPolicy`, ensure that only users matching that policy can access the service using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="37a59-157">個別的服務方法也可以套用 `[Authorize]` 屬性。</span><span class="sxs-lookup"><span data-stu-id="37a59-157">Individual service methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="37a59-158">如果目前使用者不符合 **同時** 套用至方法和類別的原則，則會將錯誤傳回給呼叫者：</span><span class="sxs-lookup"><span data-stu-id="37a59-158">If the current user doesn't match the policies applied to **both** the method and the class, an error is returned to the caller:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="37a59-159">其他資源</span><span class="sxs-lookup"><span data-stu-id="37a59-159">Additional resources</span></span>

* [<span data-ttu-id="37a59-160">ASP.NET Core 中的持有人權杖驗證</span><span class="sxs-lookup"><span data-stu-id="37a59-160">Bearer Token authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="37a59-161">在 ASP.NET Core 中設定用戶端憑證驗證</span><span class="sxs-lookup"><span data-stu-id="37a59-161">Configure Client Certificate authentication in ASP.NET Core</span></span>](xref:security/authentication/certauth)
