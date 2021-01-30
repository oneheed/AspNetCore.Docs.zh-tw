---
title: 搭配 ASP.NET Core 的 gRPC 服務
author: juntaoluo
description: 瞭解使用 ASP.NET Core 撰寫 gRPC 服務時的基本概念。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 01/29/2021
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
uid: grpc/aspnetcore
ms.openlocfilehash: 57edfa31079cb3fca6e9e8d0fa55bcbb8bbfefca
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057472"
---
# <a name="grpc-services-with-aspnet-core"></a>搭配 ASP.NET Core 的 gRPC 服務

本檔說明如何使用 ASP.NET Core 開始使用 gRPC 服務。

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="prerequisites"></a>必要條件

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a>開始在 ASP.NET Core 中使用 gRPC 服務

[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([如何下載](xref:index#how-to-download-a-sample))。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

如需如何建立 gRPC 專案的詳細指示，請參閱 [開始使用 gRPC services](xref:tutorials/grpc/grpc-start) 。

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

從命令列執行 `dotnet new grpc -o GrpcGreeter`。

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a>將 gRPC 服務新增至 ASP.NET Core 應用程式

gRPC 需要 [gRPC. AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) 套件。

### <a name="configure-grpc"></a>設定 gRPC

在 *Startup.cs* 中：

* 使用方法可啟用 gRPC `AddGrpc` 。
* 每個 gRPC 服務都會透過方法新增至路由管線 `MapGrpcService` 。

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

ASP.NET Core 中介軟體和功能共用路由管線，因此可以將應用程式設定為可提供額外的要求處理常式。 其他的要求處理常式（例如 MVC 控制器）會與已設定的 gRPC 服務平行運作。

## <a name="server-options"></a>伺服器選項

gRPC 服務可以由所有內建的 ASP.NET Core 伺服器主控。

> [!div class="checklist"]
>
> * Kestrel
> * TestServer
> * IIS&dagger;
> * HTTP.sys&dagger;

&dagger;IIS 和 HTTP.sys 需要 .NET 5 和 Windows 10 組建20241或更新版本。

如需為 ASP.NET Core 應用程式選擇正確伺服器的詳細資訊，請參閱 <xref:fundamentals/servers/index> 。

::: moniker range=">= aspnetcore-5.0"

## <a name="kestrel"></a>Kestrel

[Kestrel](xref:fundamentals/servers/kestrel) 是 ASP.NET Core 的跨平臺 web 伺服器。 Kestrel 可提供最佳的效能和記憶體使用率，但它沒有一些 HTTP.sys 的 advanced 功能，例如埠共用。

Kestrel gRPC 端點：

* 需要 HTTP/2。
* 應透過 [傳輸層安全性 (TLS) ](https://tools.ietf.org/html/rfc5246)來保護。

### <a name="http2"></a>HTTP/2

gRPC 需要 HTTP/2。 ASP.NET Core 的 gRPC 會驗證[HttpRequest。](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) `HTTP/2`

Kestrel 支援大部分新式作業系統上的 [HTTP/2](xref:fundamentals/servers/kestrel/http2) 。 預設會將 Kestrel 端點設定為支援 HTTP/1.1 和 HTTP/2 連接。

### <a name="tls"></a>TLS

用於 gRPC 的 Kestrel 端點應使用 TLS 來保護。 在開發期間，會在 `https://localhost:5001` ASP.NET Core 開發憑證存在時自動建立以 TLS 保護的端點。 不需要組態。 `https`前置詞會驗證 Kestrel 端點是否使用 TLS。

在生產環境中，必須明確設定 TLS。 下列 *appsettings.json* 範例會提供以 TLS 保護的 HTTP/2 端點：

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

或者，您也可以在 *Program.cs* 中設定 Kestrel 端點：

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

### <a name="protocol-negotiation"></a>通訊協定交涉

TLS 是用來保護通訊安全。 當端點支援多個通訊協定時，會使用 TLS [應用層通訊協定協商 (ALPN) ](https://tools.ietf.org/html/rfc7301#section-3) 交握來協調用戶端與伺服器之間的連接通訊協定。 此協商會判斷連接使用的是 HTTP/1.1 或 HTTP/2。

如果 HTTP/2 端點設定為沒有 TLS，則端點的 [>listenoptions](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) 必須設定為 `HttpProtocols.Http2` 。 具有多個通訊協定的端點 (例如， `HttpProtocols.Http1AndHttp2`) 無法在沒有 TLS 的情況下使用，因為沒有任何協調。 所有不安全端點的連接都會預設為 HTTP/1.1，且 gRPC 呼叫會失敗。

如需有關使用 Kestrel 啟用 HTTP/2 和 TLS 的詳細資訊，請參閱 [Kestrel 端點](xref:fundamentals/servers/kestrel/endpoints)設定。

> [!NOTE]
> macOS 不支援具有 TLS 的 ASP.NET Core gRPC。 您需要額外的組態才能在 macOS 上成功執行 gRPC 服務。 如需詳細資訊，請參閱[無法在 macOS 上啟動 ASP.NET Core gRPC 應用程式](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)。

## <a name="iis"></a>IIS

[Internet Information Services (IIS) ](xref:host-and-deploy/iis/index) 是可裝載 web 應用程式（包括 ASP.NET Core）的彈性、安全且可管理的 web 伺服器。 需要 .NET 5 和 Windows 10 組建20241或更新版本，才能使用 IIS 裝載 gRPC services。

IIS 必須設定為使用 TLS 和 HTTP/2。 如需詳細資訊，請參閱<xref:host-and-deploy/iis/protocols>。

## <a name="httpsys"></a>HTTP.sys

[HTTP.sys](xref:fundamentals/servers/httpsys) 是只在 Windows 上執行的 ASP.NET Core 網頁伺服器。 需要 .NET 5 和 Windows 10 組建20241或更新版本，才能裝載具有 HTTP.sys 的 gRPC 服務。

HTTP.sys 必須設定為使用 TLS 和 HTTP/2。 如需詳細資訊，請參閱  [HTTP.sys 網頁伺服器 HTTP/2 支援](xref:fundamentals/servers/httpsys#http2-support)。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="kestrel"></a>Kestrel

[Kestrel](xref:fundamentals/servers/kestrel) 是 ASP.NET Core 的跨平臺 web 伺服器。 Kestrel 可提供最佳的效能和記憶體使用率，但它沒有一些 HTTP.sys 的 advanced 功能，例如埠共用。

Kestrel gRPC 端點：

* 需要 HTTP/2。
* 應透過 [傳輸層安全性 (TLS) ](https://tools.ietf.org/html/rfc5246)來保護。

### <a name="http2"></a>HTTP/2

gRPC 需要 HTTP/2。 ASP.NET Core 的 gRPC 會驗證[HttpRequest。](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) `HTTP/2`

Kestrel 支援大部分新式作業系統上的 [HTTP/2](xref:fundamentals/servers/kestrel#http2-support) 。 預設會將 Kestrel 端點設定為支援 HTTP/1.1 和 HTTP/2 連接。

### <a name="tls"></a>TLS

用於 gRPC 的 Kestrel 端點應使用 TLS 來保護。 在開發期間，會在 `https://localhost:5001` ASP.NET Core 開發憑證存在時自動建立以 TLS 保護的端點。 不需要組態。 `https`前置詞會驗證 Kestrel 端點是否使用 TLS。

在生產環境中，必須明確設定 TLS。 下列 *appsettings.json* 範例會提供以 TLS 保護的 HTTP/2 端點：

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

或者，您也可以在 *Program.cs* 中設定 Kestrel 端點：

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

### <a name="protocol-negotiation"></a>通訊協定交涉

TLS 是用來保護通訊安全。 當端點支援多個通訊協定時，會使用 TLS [應用層通訊協定協商 (ALPN) ](https://tools.ietf.org/html/rfc7301#section-3) 交握來協調用戶端與伺服器之間的連接通訊協定。 此協商會判斷連接使用的是 HTTP/1.1 或 HTTP/2。

如果 HTTP/2 端點設定為沒有 TLS，則端點的 [>listenoptions](xref:fundamentals/servers/kestrel#listenoptionsprotocols) 必須設定為 `HttpProtocols.Http2` 。 具有多個通訊協定的端點 (例如， `HttpProtocols.Http1AndHttp2`) 無法在沒有 TLS 的情況下使用，因為沒有任何協調。 所有不安全端點的連接都會預設為 HTTP/1.1，且 gRPC 呼叫會失敗。

如需有關使用 Kestrel 啟用 HTTP/2 和 TLS 的詳細資訊，請參閱 [Kestrel 端點](xref:fundamentals/servers/kestrel#endpoint-configuration)設定。

> [!NOTE]
> macOS 不支援具有 TLS 的 ASP.NET Core gRPC。 您需要額外的組態才能在 macOS 上成功執行 gRPC 服務。 如需詳細資訊，請參閱[無法在 macOS 上啟動 ASP.NET Core gRPC 應用程式](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)。

::: moniker-end

## <a name="integration-with-aspnet-core-apis"></a>與 ASP.NET Core Api 整合

gRPC services 具有 ASP.NET Core 功能的完整存取權，例如相依性 [插入](xref:fundamentals/dependency-injection) (DI) 和 [記錄](xref:fundamentals/logging/index)。 例如，服務執行可透過此函式從 DI 容器解析記錄器服務：

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

根據預設，gRPC 服務執行可以使用任何存留期來解析其他 DI 服務 (單一、範圍或暫時性) 。

### <a name="resolve-httpcontext-in-grpc-methods"></a>解析 gRPC 方法中的 HttpCoNtext

GRPC API 可讓您存取某些 HTTP/2 訊息資料，例如方法、主機、標頭和結尾。 存取是透過 `ServerCallContext` 傳遞給每個 gRPC 方法的引數：

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

`ServerCallContext` 未提供 `HttpContext` 所有 ASP.NET api 的完整存取權。 `GetHttpContext`擴充方法會提供完整的存取權，以 `HttpContext` 代表 ASP.NET api 中的基礎 HTTP/2 訊息：

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]


## <a name="additional-resources"></a>其他資源

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
