---
title: 針對 .NET Core 上的 gRPC 進行疑難排解
author: jamesnk
description: 針對在 .NET Core 上使用 gRPC 時發生的錯誤進行疑難排解。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/09/2020
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
uid: grpc/troubleshoot
ms.openlocfilehash: 1fd89059183300993c7fa78aa8dab1bda247a530
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586978"
---
# <a name="troubleshoot-grpc-on-net-core"></a>針對 .NET Core 上的 gRPC 進行疑難排解

依 [James 牛頓](https://twitter.com/jamesnk)

本檔討論在 .NET 上開發 gRPC 應用程式時常見的問題。

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a>用戶端與服務 SSL/TLS 設定不符

GRPC 範本和範例預設會使用 [傳輸層安全性 (TLS) ](https://tools.ietf.org/html/rfc5246) ，以保護 gRPC 服務的安全。 gRPC 用戶端必須使用安全連線，才能成功呼叫安全的 gRPC 服務。

您可以在應用程式啟動時，確認 ASP.NET Core gRPC 服務是否正在使用 TLS。 服務將接聽 HTTPS 端點：

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

.NET Core 用戶端必須 `https` 在伺服器位址中使用，才能使用安全的連接進行呼叫：

```csharp
static async Task Main(string[] args)
{
    // The port number(5001) must match the port of the gRPC server.
    var channel = GrpcChannel.ForAddress("https://localhost:5001");
    var client = new Greet.GreeterClient(channel);
}
```

所有 gRPC 用戶端都支援 TLS。 從其他語言 gRPC 用戶端通常需要使用設定的通道 `SslCredentials` 。 `SslCredentials` 指定用戶端將使用的憑證，而且必須使用它，而不是不安全的認證。 如需將不同的 gRPC 用戶端執行設定為使用 TLS 的範例，請參閱 [GRPC Authentication](https://www.grpc.io/docs/guides/auth/)。

## <a name="call-a-grpc-service-with-an-untrustedinvalid-certificate"></a>使用未受信任/不正確憑證呼叫 gRPC 服務

.NET gRPC 用戶端要求服務必須有受信任的憑證。 呼叫沒有受信任憑證的 gRPC 服務時，會傳回下列錯誤訊息：

> 未處理的例外狀況。 System.net.HTTP.HTTPrequestexception：無法建立 SSL 連線，請參閱內部例外狀況。
> ---> System.Security.Authentication.AuthenticationException: 根據驗證程序，遠端憑證是無效的。

如果您要在本機測試您的應用程式，且 ASP.NET Core HTTPS 開發憑證不受信任，您可能會看到此錯誤。 如需修正此問題的指示，請參閱[信任 Windows 和 macOS上的 ASP.NET Core HTTPS 開發憑證](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)。

如果您在另一部電腦上呼叫 gRPC 服務，但無法信任該憑證，則可以設定 gRPC 用戶端忽略不正確憑證。 下列程式碼會使用 [HttpClientHandler ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) 來允許沒有受信任憑證的呼叫：

```csharp
var httpHandler = new HttpClientHandler();
// Return `true` to allow certificates that are untrusted/invalid
httpHandler.ServerCertificateCustomValidationCallback = 
    HttpClientHandler.DangerousAcceptAnyServerCertificateValidator;

var channel = GrpcChannel.ForAddress("https://localhost:5001",
    new GrpcChannelOptions { HttpHandler = httpHandler });
var client = new Greet.GreeterClient(channel);
```

> [!WARNING]
> 未受信任的憑證應該只在應用程式開發期間使用。 生產環境應用程式應該一律使用有效的憑證。

## <a name="call-insecure-grpc-services-with-net-core-client"></a>使用 .NET Core 用戶端呼叫不安全的 gRPC 服務

.NET gRPC 用戶端可以藉由 `http` 在伺服器位址中 specifing 來呼叫不安全的 gRPC 服務。 例如： `GrpcChannel.ForAddress("http://localhost:5000")` 。

根據應用程式所使用的 .NET 版本，呼叫不安全的 gRPC 服務有一些額外的需求：

* .NET 5 或更新版本需要 [Grpc .net. Client](https://www.nuget.org/packages/Grpc.Net.Client) version 2.32.0 或更新版本。
* .NET Core 3.x 需要額外的設定。 應用程式必須將 `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` 參數設定為 `true` ：
    
    ```csharp
    // This switch must be set before creating the GrpcChannel/HttpClient.
    AppContext.SetSwitch(
        "System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);
    
    // The port number(5000) must match the port of the gRPC server.
    var channel = GrpcChannel.ForAddress("http://localhost:5000");
    var client = new Greet.GreeterClient(channel);
    ```

`System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport`只有 .Net Core 3.x 才需要此參數。 它不會在 .NET 5 中執行任何動作，而且也不需要。

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a>無法在 macOS 上啟動 ASP.NET Core gRPC 應用程式

Kestrel 在 macOS 和舊版 Windows （例如 Windows 7）上不支援具有 TLS 的 HTTP/2。 ASP.NET Core gRPC 範本和範例預設會使用 TLS。 當您嘗試啟動 gRPC 伺服器時，將會看到下列錯誤訊息：

> 無法 https://localhost:5001 在 IPv4 回送介面上系結至： macOS 因為缺少 ALPN 支援，所以不支援透過 TLS 的 HTTP/2。

若要解決此問題，請將 Kestrel 和 gRPC 用戶端設定為使用 *沒有* TLS 的 HTTP/2。 您應該只在開發期間執行此動作。 不使用 TLS 會導致傳送 gRPC 的訊息，而不加密。

Kestrel 必須在 *Program.cs* 中設定不含 TLS 的 HTTP/2 端點：

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                // Setup a HTTP/2 endpoint without TLS.
                options.ListenLocalhost(5000, o => o.Protocols = 
                    HttpProtocols.Http2);
            });
            webBuilder.UseStartup<Startup>();
        });
```

::: moniker range=">= aspnetcore-5.0"
當 HTTP/2 端點設定為沒有 TLS 時，端點的 [>listenoptions](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) 必須設定為 `HttpProtocols.Http2` 。 `HttpProtocols.Http1AndHttp2` 因為必須有 TLS 才能協商 HTTP/2，所以無法使用。 如果沒有 TLS，端點的所有連接都會預設為 HTTP/1.1，而 gRPC 呼叫會失敗。
::: moniker-end

::: moniker range="< aspnetcore-5.0"
當 HTTP/2 端點設定為沒有 TLS 時，端點的 [>listenoptions](xref:fundamentals/servers/kestrel#listenoptionsprotocols) 必須設定為 `HttpProtocols.Http2` 。 `HttpProtocols.Http1AndHttp2` 因為必須有 TLS 才能協商 HTTP/2，所以無法使用。 如果沒有 TLS，端點的所有連接都會預設為 HTTP/1.1，而 gRPC 呼叫會失敗。
::: moniker-end

GRPC 用戶端也必須設定為不使用 TLS。 如需詳細資訊，請參閱 [使用 .Net Core 用戶端呼叫不安全的 gRPC 服務](#call-insecure-grpc-services-with-net-core-client)。

> [!WARNING]
> 只有在應用程式開發期間，才應該使用沒有 TLS 的 HTTP/2。 生產環境應用程式應該一律使用傳輸安全性。 如需詳細資訊，請參閱 [gRPC for ASP.NET Core 中的安全性考慮](xref:grpc/security#transport-security)。

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a>gRPC c # 資產不是從 proto 檔案產生的程式碼

實體用戶端和服務基類的 gRPC 程式碼產生需要從專案參考 protobuf 檔案和工具。 您必須包含：

* 您要在專案群組中使用的 *proto* 檔。 `<Protobuf>` 專案必須參考匯 [入的 *. proto*](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions)檔案。
* GRPC 工具套件 [gRPC](https://www.nuget.org/packages/Grpc.Tools/)的套件參考。

如需產生 gRPC c # 資產的詳細資訊，請參閱 <xref:grpc/basics> 。

裝載 gRPC 服務的 ASP.NET Core web 應用程式只需要產生的服務基類：

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

發出 gRPC 呼叫的 gRPC 用戶端應用程式只需要產生具體用戶端：

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```

## <a name="wpf-projects-unable-to-generate-grpc-c-assets-from-proto-files"></a>WPF 專案無法從 proto 檔案產生 gRPC c # 資產

WPF 專案有 [已知的問題](https://github.com/dotnet/wpf/issues/810) ，導致 gRPC 程式碼產生無法正常運作。 藉由參考和 proto 檔案，在 WPF 專案中產生的任何 gRPC 類型 `Grpc.Tools` 都會在使用時建立編譯錯誤： 

> 錯誤 CS0246：找不到類型或命名空間名稱 ' MyGrpcServices ' (您是否遺漏 using 指示詞或元件參考？ ) 

您可以透過下列方式解決此問題：

1. 建立新的 .NET Core 類別庫專案。
2. 在新的專案中，新增參考以 [從 *\* proto* 檔案啟用 c # 程式碼產生](xref:grpc/basics#generated-c-assets)：
    * 將套件參考新增至 [Grpc 工具](https://www.nuget.org/packages/Grpc.Tools/) 套件。
    * 將 *\* proto* 檔新增至 `<Protobuf>` 專案群組。
3. 在 WPF 應用程式中，加入新專案的參考。

WPF 應用程式可以從新的類別庫專案使用 gRPC 產生的類型。

[!INCLUDE[](~/includes/gRPCazure.md)]
