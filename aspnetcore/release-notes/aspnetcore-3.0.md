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
# <a name="whats-new-in-aspnet-core-30"></a>ASP.NET Core 3.0 的新功能

本文強調 ASP.NET Core 3.0 中最重要的變更，以及相關檔的連結。

## Blazor

Blazor 是 ASP.NET Core 中的新架構，可使用 .NET 建立互動式用戶端 web UI：

* 使用 C# 而不是 JavaScript 來建立豐富的互動式 UI。
* 共用以 .NET 撰寫的伺服器端與用戶端應用程式邏輯。
* 將 UI 轉譯為 HTML 和 CSS 以支援寬瀏覽器，包括行動裝置瀏覽器。

Blazor 架構支援的案例：

* 可重複使用的 UI 元件 (Razor 元件) 
* 用戶端路由
* 元件版面配置
* 支援相依性插入
* 表單和驗證
* 使用類別庫建立元件程式庫 Razor
* JavaScript Interop

如需詳細資訊，請參閱<xref:blazor/index>。

### Blazor Server

Blazor 將元件轉譯邏輯與應用程式 UI 更新的套用方式分離出來。 Blazor Server 提供在 Razor ASP.NET Core 應用程式的伺服器上裝載元件的支援。 UI 更新會透過連接來處理 SignalR 。 Blazor Server ASP.NET Core 3.0 支援。

### <a name="blazor-webassembly-preview"></a>Blazor WebAssembly (預覽)

Blazor 您也可以使用以 WebAssembly 為基礎的 .NET 執行時間，直接在瀏覽器中執行應用程式。 Blazor WebAssembly 處於預覽狀態，且在 ASP.NET Core 3.0 中 *不* 受支援。 Blazor WebAssembly 未來的 ASP.NET Core 版本將會支援。

### <a name="razor-components"></a>Razor 元件

Blazor 應用程式是從元件建立的。 元件是使用者介面 (UI) 的獨立區塊，例如頁面、對話方塊或表單。 元件是標準的 .NET 類別，可定義 UI 轉譯邏輯和用戶端事件處理常式。 您可以建立豐富的互動式 web 應用程式，而不需要 JavaScript。

中的元件 Blazor 通常是使用 Razor HTML 和 c # 的自然混合語法來撰寫。 Razor 元件類似于 Razor 頁面和 MVC 視圖，它們都使用它們 Razor 。 不同于根據要求-回應模型的頁面和流覽，元件是專門用來處理 UI 組合。

## <a name="grpc"></a>gRPC

[gRPC](https://grpc.io/)：

* 是受歡迎的高效能 RPC (遠端程序呼叫) framework。
* 提供固定合約優先的 API 開發方法。
* 使用新式技術，例如：

  * 傳輸的 HTTP/2。
  * 通訊協定緩衝區作為介面描述語言。
  * 二進位序列化格式。
* 提供下列功能：

  * 驗證
  * 雙向串流和流量控制。
  * 取消和超時。

ASP.NET Core 3.0 中的 gRPC 功能包括：

* [Grpc. AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore)：用於裝載 Grpc 服務的 ASP.NET 核心架構。 ASP.NET Core 上的 gRPC 與標準 ASP.NET 核心功能整合，例如記錄、相依性插入 (DI) 、驗證和授權。
* [Grpc .net. 用戶端](https://www.nuget.org/packages/Grpc.Net.Client)：適用于 .net Core 的 Grpc 用戶端，以熟悉的為基礎 `HttpClient` 。
* [Grpc .net. ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory)： Grpc 用戶端與的整合 `HttpClientFactory` 。

如需詳細資訊，請參閱<xref:grpc/index>。

## SignalR

請參閱 [更新程式 SignalR 代碼](xref:migration/22-to-30#signalr) 以取得遷移指示。 SignalR 現在會使用 `System.Text.Json` 來序列化/還原序列化 JSON 訊息。 如需還原序列化程式的指示，請參閱 [切換至 Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson) `Newtonsoft.Json` 。

在的 JavaScript 和 .NET 用戶端中 SignalR ，已針對自動重新連接新增了支援。 根據預設，用戶端會立即嘗試重新連線，並在2、10和30秒後重試（如有需要）。 如果用戶端成功重新連接，則會收到新的連接識別碼。 自動重新連線是加入宣告：

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

您可以藉由傳遞以毫秒為基礎的持續時間陣列來指定重新連接間隔：

```javascript
.withAutomaticReconnect([0, 3000, 5000, 10000, 15000, 30000])
//.withAutomaticReconnect([0, 2000, 10000, 30000]) The default intervals.
```

您可以傳入自訂的實作為重新連接間隔的完整控制。

如果在上次重新連接間隔後重新連線失敗：

* 用戶端會將連接視為離線。
* 用戶端停止嘗試重新連接。

在重新連線嘗試期間，請更新應用程式 UI，以通知使用者正在嘗試重新連接。

為了在連接中斷時提供 UI 回饋， SignalR 用戶端 API 已擴充為包含下列事件處理常式：

* `onreconnecting`：讓開發人員有機會停用 UI，或讓使用者知道應用程式已離線。
* `onreconnected`：讓開發人員有機會在連接重新建立之後更新 UI。

下列程式碼會在 `onreconnecting` 嘗試連接時使用來更新 UI：

```javascript
connection.onreconnecting((error) => {
    const status = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messageInput").disabled = true;
    document.getElementById("sendButton").disabled = true;
    document.getElementById("connectionStatus").innerText = status;
});
```

下列程式碼會使用 `onreconnected` 來更新連接上的 UI：

```javascript
connection.onreconnected((connectionId) => {
    const status = `Connection reestablished. Connected.`;
    document.getElementById("messageInput").disabled = false;
    document.getElementById("sendButton").disabled = false;
    document.getElementById("connectionStatus").innerText = status;
});
```

SignalR 當中樞方法需要授權時，3.0 和更新版本會提供自訂資源給授權處理常式。 資源是的實例 `HubInvocationContext` 。 `HubInvocationContext`包括：

* `HubCallerContext`
* 正在叫用之中樞方法的名稱。
* 中樞方法的引數。

請考慮下列聊天室應用程式範例，讓多個組織能夠透過 Azure Active Directory 登入。 具有 Microsoft 帳戶的任何人都可以登入聊天，但只有擁有組織的成員可以禁止使用者或查看使用者的聊天記錄。 應用程式可能會限制特定使用者的特定功能。

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

在上述程式碼中，是 `DomainRestrictedRequirement` 做為自訂的 `IAuthorizationRequirement` 。 由於 `HubInvocationContext` 資源參數會傳入，內部邏輯可以：

* 檢查正在呼叫中樞的內容。
* 決定是否要讓使用者執行個別的中樞方法。

個別的中樞方法可以用程式碼在執行時間檢查的原則名稱來標記。 當用戶端嘗試呼叫個別的中樞方法時， `DomainRestrictedRequirement` 處理常式會執行並控制對方法的存取。 根據控制項的存取方式 `DomainRestrictedRequirement` ：

* 所有登入的使用者都可以呼叫 `SendMessage` 方法。
* 只有登入 `@jabbr.net` 電子郵件地址的使用者可以查看使用者的歷程記錄。
* 只 `bob42@jabbr.net` 可禁止來自聊天室的使用者。

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

建立 `DomainRestricted` 原則可能包括：

* 在 *Startup.cs* 中，加入新的原則。
* 將自訂 `DomainRestrictedRequirement` 需求提供為參數。
* `DomainRestricted`向授權中介軟體註冊。

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

SignalR 中樞使用 [端點路由](xref:fundamentals/routing)。 SignalR 中樞連接先前已明確完成：

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});
```

在先前的版本中，開發人員需要 Razor 在不同的地方連接控制器、頁面和中樞。 明確連接會產生一系列幾乎相同的路由區段：

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

SignalR 3.0 中樞可以透過端點路由來路由傳送。 使用端點路由，通常可以在中設定所有路由 `UseRouting` ：

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapHub<ChatHub>("hubs/chat");
});
```

已新增 ASP.NET Core 3.0 SignalR ：

用戶端對伺服器串流處理。 使用用戶端對伺服器串流時，伺服器端方法可以取得或的實例 `IAsyncEnumerable<T>` `ChannelReader<T>` 。 在下列 c # 範例中， `UploadStream` 中樞上的方法會接收來自用戶端的字串資料流程：

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        // process content
    }
}
```

.NET 用戶端應用程式可以傳遞 `IAsyncEnumerable<T>` 或 `ChannelReader<T>` 實例作為 `stream` `UploadStream` 上述中樞方法的引數。

在 `for` 迴圈完成且區域函式結束之後，會傳送資料流程完成：

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

JavaScript 用戶端應用程式會 SignalR `Subject` 針對上述中樞方法的引數使用 (或 [RxJS 主體](https://rxjs.dev/api/index/class/Subject)) `stream` `UploadStream` 。

```javascript
let subject = new signalR.Subject();
await connection.send("StartStream", "MyAsciiArtStream", subject);
```

JavaScript 程式碼可使用 `subject.next` 方法來處理已捕捉的字串，並準備好傳送至伺服器。

```javascript
subject.next("example");
subject.complete();
```

您可以使用類似上述兩個程式碼片段的程式碼，建立即時串流體驗。

## <a name="new-json-serialization"></a>新的 JSON 序列化

ASP.NET Core 3.0 現在預設會使用 <xref:System.Text.Json> JSON 序列化：

* 以非同步方式讀取和寫入 JSON。
* 已針對 UTF-8 文字進行優化。
* 通常比更高 `Newtonsoft.Json` 的效能。

若要將 Json.NET 新增至 ASP.NET Core 3.0，請參閱 [新增 Newtonsoft.Js的 Json 格式支援](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support)。

## <a name="new-razor-directives"></a>新的指示詞 Razor

下列清單包含新的指示詞 Razor ：

* [`@attribute`](xref:mvc/views/razor#attribute)：指示詞會將 `@attribute` 指定的屬性套用至所產生頁面或視圖的類別。 例如： `@attribute [Authorize]` 。
* [`@implements`](xref:mvc/views/razor#implements)：指示詞會 `@implements` 針對產生的類別實作為介面。 例如： `@implements IDisposable` 。

## <a name="identityserver4-supports-authentication-and-authorization-for-web-apis-and-spas"></a>IdentityServer4 支援 web Api 和 Spa 的驗證和授權

ASP.NET Core 3.0 在單一頁面應用程式中提供驗證 (Spa) 使用 web API 授權的支援。 ASP.NET Core Identity針對驗證和儲存使用者，會結合[ Identity Server4](https://identityserver.io/)來執行 OpenID connect。

IdentityServer4 是適用于 ASP.NET Core 3.0 的 OpenID Connect 和 OAuth 2.0 架構。 它可啟用下列安全性功能：

* 驗證即服務 (AaaS) 
* 單一登入/關閉 (SSO) 超過多個應用程式類型
* Api 的存取控制
* 同盟閘道

如需詳細資訊，請參閱 Spa [的 Identity Server4 檔](http://docs.identityserver.io/en/latest/index.html) 或 [驗證和授權](xref:security/authentication/identity/spa)。

## <a name="certificate-and-kerberos-authentication"></a>憑證和 Kerberos 驗證

憑證驗證需要：

* 設定伺服器接受憑證。
* 在中加入驗證中介軟體 `Startup.Configure` 。
* 在中新增憑證驗證服務 `Startup.ConfigureServices` 。

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

憑證驗證的選項包括下列功能：

* 接受自我簽署的憑證。
* 檢查憑證撤銷。
* 檢查推出憑證是否有正確的使用方式旗標。

預設使用者主體是從憑證屬性所構成。 使用者主體包含可補充或取代主體的事件。 如需詳細資訊，請參閱<xref:security/authentication/certauth>。

[Windows 驗證](/windows-server/security/windows-authentication/windows-authentication-overview) 已延伸至 Linux 和 macOS。 在先前的版本中，Windows 驗證僅限於 [IIS](xref:host-and-deploy/iis/index) 和 [HTTP.sys](xref:fundamentals/servers/httpsys)。 在 ASP.NET Core 3.0 中， [Kestrel](xref:fundamentals/servers/kestrel) 能夠在 windows、Linux 和 macOS 上針對已加入網域的 windows 主機使用 Negotiate、 [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview)和 [NTLM](/windows-server/security/kerberos/ntlm-overview)。 這些驗證配置的 Kestrel 支援是由 AspNetCore 提供。 [Negotiate NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) 套件。 如同其他驗證服務，請設定驗證應用程式範圍，然後設定服務：

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

主機需求：

* Windows 主機必須有 [服務主體名稱](/windows/win32/ad/service-principal-names) (spn) 新增至裝載該應用程式的使用者帳戶。
* Linux 和 macOS 電腦必須加入網域。
  * 必須為 web 進程建立 Spn。
  * 您必須在主機電腦上產生並設定[Keytab](/archive/blogs/pie/all-you-need-to-know-about-keytab-files)檔案。

如需詳細資訊，請參閱<xref:security/authentication/windowsauth>。

## <a name="template-changes"></a>範本變更

Web UI 範本 (Razor 頁面、具有控制器和 views 的 MVC) 移除下列各項：

* cookie已不再包含同意 UI。 若要 cookie 在 ASP.NET Core 3.0 範本產生的應用程式中啟用同意功能，請參閱 <xref:security/gdpr> 。
* 腳本和相關靜態資產現在會以本機檔案的形式來參考，而不是使用 Cdn。 如需詳細資訊，請參閱 [腳本和相關靜態資產現在會以本機檔案的形式來參考，而不是根據目前的環境 (dotnet/AspNetCore.Docs #14350) 使用 cdn ](https://github.com/dotnet/AspNetCore.Docs/issues/14350)。

角度範本已更新為使用角度8。

Razor依預設，類別庫 (RCL) 範本預設為 Razor 元件開發。 Visual Studio 中的新範本選項提供頁面和視圖的範本支援。 從命令介面中的範本建立 RCL 時，請將選項傳遞 `--support-pages-and-views` (`dotnet new razorclasslib --support-pages-and-views`) 。

## <a name="generic-host"></a>一般主機

ASP.NET Core 3.0 範本使用 <xref:fundamentals/host/generic-host> 。 使用舊版 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> 。 使用 .NET Core 泛型主機 (<xref:Microsoft.Extensions.Hosting.HostBuilder>) 可讓 ASP.NET Core 應用程式與其他不是 web 特定的伺服器案例更緊密地整合。 如需詳細資訊，請參閱 [HostBuilder 取代 >webhostbuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder)。

### <a name="host-configuration"></a>主機組態

在 ASP.NET Core 3.0 發行之前，會針對 Web 主機的主機設定載入前面加上的環境變數 `ASPNETCORE_` 。 在3.0 中， `AddEnvironmentVariables` 是用來載入前面加上的環境變數，以 `DOTNET_` 供主機設定使用 `CreateDefaultBuilder` 。

### <a name="changes-to-startup-constructor-injection"></a>啟動函式插入的變更

泛型主機僅支援下列類型的函式 `Startup` 插入：

* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

所有服務仍然可以直接插入至方法的引數 `Startup.Configure` 。 如需詳細資訊，請參閱 [泛型主機限制啟動的函式插入 (aspnet/公告 #353) ](https://github.com/aspnet/Announcements/issues/353)。

## <a name="kestrel"></a>Kestrel

* Kestrel 設定已更新，可遷移至一般主機。 在3.0 中，Kestrel 是在提供的 web 主機建立器上進行設定 `ConfigureWebHostDefaults` 。
* 連接介面卡已從 Kestrel 中移除，並以連線中介軟體取代，這類似于 ASP.NET 核心管線中的 HTTP 中介軟體，但適用于較低層級的連接。
* Kestrel 傳輸層已公開為中的公用介面 `Connections.Abstractions` 。
* 將尾端標頭移至新的集合，可解決標頭和結尾之間的不明確的問題。
* 同步 i/o Api （例如 `HttpRequest.Body.Read` ）是造成應用程式當機的執行緒耗盡的常見來源。 在3.0 中， `AllowSynchronousIO` 預設為停用。

如需詳細資訊，請參閱<xref:migration/22-to-30#kestrel>。

## <a name="http2-enabled-by-default"></a>預設啟用的 HTTP/2

HTTPS 端點的 Kestrel 預設會啟用 HTTP/2。 當作業系統支援時，會啟用 IIS 或 HTTP.sys 的 HTTP/2 支援。

## <a name="eventcounters-on-request"></a>依要求 EventCounters

主控 EventSource，會 `Microsoft.AspNetCore.Hosting` 發出下列與連 <xref:System.Diagnostics.Tracing.EventCounter> 入要求相關的新類型：

* `requests-per-second`
* `total-requests`
* `current-requests`
* `failed-requests`

## <a name="endpoint-routing"></a>端點路由

端點路由可讓架構 (例如，MVC) 可與中介軟體妥善搭配運作，以增強：

* 中介軟體和端點的順序可在的要求處理管線中進行設定 `Startup.Configure` 。
* 端點和中介軟體適用于其他 ASP.NET 核心技術，例如健康情況檢查。
* 端點可以在中介軟體和 MVC 中執行原則，例如 CORS 或授權。
* 篩選和屬性可以放在控制器中的方法上。

如需詳細資訊，請參閱<xref:fundamentals/routing#routing-basics>。

## <a name="health-checks"></a>健康情況檢查

健康情況檢查會使用端點路由搭配一般主機。 在中 `Startup.Configure` ， `MapHealthChecks` 使用端點 URL 或相對路徑呼叫端點產生器：

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

健康情況檢查端點可以：

* 指定一或多個允許的主機/埠。
* 需要授權。
* 需要 CORS。

如需詳細資訊，請參閱下列文章：

* <xref:migration/22-to-30#health-checks>
* <xref:host-and-deploy/health-checks>

## <a name="pipes-on-httpcontext"></a>HttpCoNtext 上的管道

現在，您可以使用 API 來讀取要求本文，並撰寫回應主體 <xref:System.IO.Pipelines> 。 必須提供 <!-- <xref:Microsoft.AspNetCore.Http.HttpRequest.BodyReader> --> `HttpRequest.BodyReader` 屬性提供 <xref:System.IO.Pipelines.PipeReader> 可用於讀取要求主體的。 必須提供 <!-- <xref:Microsoft.AspNetCore.Http.> --> `HttpResponse.BodyWriter` 屬性會提供 <xref:System.IO.Pipelines.PipeWriter> ，可用來寫入回應主體。 `HttpRequest.BodyReader` 是資料流程的類比 `HttpRequest.Body` 。 `HttpResponse.BodyWriter` 是資料流程的類比 `HttpResponse.Body` 。

<!-- indirectly related, https://github.com/dotnet/docs/pull/14414 won't be published by 9/23  -->

## <a name="improved-error-reporting-in-iis"></a>改進 IIS 中的錯誤報表

在 IIS 中裝載 ASP.NET Core 應用程式時啟動錯誤，現在會產生更豐富的診斷資料。 這些錯誤會在適用的情況下，向 Windows 事件記錄檔報告堆疊追蹤。 此外，所有警告、錯誤和未處理的例外狀況都會記錄到 Windows 事件記錄檔中。

## <a name="worker-service-and-worker-sdk"></a>背景工作服務和工作者 SDK

.NET Core 3.0 引進了新的背景工作服務應用程式範本。 此範本提供在 .NET Core 中撰寫長時間執行之服務的起點。

如需詳細資訊，請參閱

* [以 .NET Core 背景工作角色作為 Windows 服務](https://devblogs.microsoft.com/aspnet/net-core-workers-as-windows-services/)
* <xref:fundamentals/host/hosted-services>
* <xref:host-and-deploy/windows-service>

## <a name="forwarded-headers-middleware-improvements"></a>轉送的標頭中介軟體改進

在舊版的 ASP.NET Core 中， <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>  <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> 當部署至 Azure LINUX 或 IIS 以外的任何反向 proxy 之後，呼叫和會有問題。 先前版本的修正記錄在 [轉寄 Linux 和非 IIS 反向](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies)proxy 的配置中。

此案例已在 ASP.NET Core 3.0 中修正。 當環境變數設定為時，主機會啟用 [轉送的標頭中介軟體](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) `ASPNETCORE_FORWARDEDHEADERS_ENABLED` `true` 。 `ASPNETCORE_FORWARDEDHEADERS_ENABLED``true`在容器映射中設定為。

## <a name="performance-improvements"></a>效能改善

ASP.NET Core 3.0 包含許多改進，可減少記憶體使用量並提升輸送量：

* 使用內建相依性插入容器進行限域服務時，減少記憶體使用量。
* 減少跨架構的配置，包括中介軟體案例和路由。
* 減少 WebSocket 連接的記憶體使用量。
* HTTPS 連線的記憶體減少和輸送量改進。
* 新的優化和完全非同步 JSON 序列化程式。
* 減少表單剖析中的記憶體使用量和輸送量改進。

## <a name="aspnet-core-30-only-runs-on-net-core-30"></a>ASP.NET Core 3.0 僅可在 .NET Core 3.0 上執行

從 ASP.NET Core 3.0，.NET Framework 不再是支援的目標架構。 以 .NET Framework 為目標的專案可以使用 [.Net Core 2.1 LTS 版本](https://dotnet.microsoft.com/download/dotnet-core/2.1)，以完全支援的方式繼續進行。 大部分 ASP.NET Core 2.1. x 相關套件將會無限期地支援，超過三年的 .NET Core 2.1 LTS 期限。

如需遷移資訊，請參閱將 [您的程式碼從 .Net Framework 移植到 .Net Core](/dotnet/core/porting/)。

## <a name="use-the-aspnet-core-shared-framework"></a>使用 ASP.NET Core 共用架構

ASP.NET Core 3.0 共用架構（包含在 [AspNetCore 中繼套件](xref:fundamentals/metapackage-app)中）不再需要專案檔中的明確 `<PackageReference />` 元素。 在專案檔中使用 SDK 時，會自動參考共用架構 `Microsoft.NET.Sdk.Web` ：

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

## <a name="assemblies-removed-from-the-aspnet-core-shared-framework"></a>從 ASP.NET Core 共用架構移除的元件

從 ASP.NET Core 3.0 共用架構中移除最顯著的元件如下：

* [Newtonsoft.Js](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET) 。 若要將 Json.NET 新增至 ASP.NET Core 3.0，請參閱 [新增 Newtonsoft.Js的 Json 格式支援](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support)。 ASP.NET Core 3.0 引進 `System.Text.Json` 讀取和寫入 JSON。 如需詳細資訊，請參閱本檔中的 [新 JSON 序列化](#new-json-serialization) 。
* [Entity Framework Core](/ef/core/)

如需從共用架構移除的完整元件清單，請參閱 [從 AspNetCore 中移除的元件。應用程式 3.0](https://github.com/dotnet/AspNetCore/issues/3755)。 如需這項變更動機的詳細資訊，請參閱 [3.0 中的 AspNetCore 應用程式的重大變更](https://github.com/aspnet/Announcements/issues/325) ，以及 [ASP.NET Core 3.0 中的變更首次查看](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/)。

<!-- 
## Additional information
For the complete list of changes, see the [ASP.NET Core 3.0 Release Notes](WHERE IS THIS????).
-->
