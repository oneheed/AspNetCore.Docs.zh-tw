---
title: ASP.NET Core 的 gRPC 中的安全性考慮
author: jamesnk
description: 瞭解 gRPC for ASP.NET Core 的安全性考慮。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/07/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/security
ms.openlocfilehash: 4c805ab1e01b4c70644a12c277441d1d242ce720
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015994"
---
# <a name="security-considerations-in-grpc-for-aspnet-core"></a>ASP.NET Core 的 gRPC 中的安全性考慮

依[James 牛頓-王](https://twitter.com/jamesnk)

本文提供使用 .NET Core 保護 gRPC 的相關資訊。

## <a name="transport-security"></a>傳輸安全性

gRPC 訊息會使用 HTTP/2 進行傳送和接收。 我們建議：

* [傳輸層安全性 (TLS) ](https://tools.ietf.org/html/rfc5246)用來保護生產 gRPC 應用程式中的訊息。
* gRPC 服務應該只接聽並回應安全的埠。

TLS 是在 Kestrel 中設定。 如需設定 Kestrel 端點的詳細資訊，請參閱[Kestrel 端點](xref:fundamentals/servers/kestrel#endpoint-configuration)設定。

## <a name="exceptions"></a>例外狀況

例外狀況訊息通常會被視為不應該向用戶端顯示的敏感性資料。 根據預設，gRPC 不會將 gRPC 服務擲回之例外狀況的詳細資料傳送給用戶端。 相反地，用戶端會收到一般訊息，指出發生錯誤。 可以覆寫用戶端的例外狀況訊息傳遞 (例如，在使用[EnableDetailedErrors](xref:grpc/configuration#configure-services-options)的開發或測試) 中。 例外狀況訊息不應在生產環境應用程式中公開給用戶端。

## <a name="message-size-limits"></a>訊息大小限制

傳入訊息至 gRPC 用戶端和服務會載入記憶體中。 訊息大小限制是一種機制，可協助防止 gRPC 耗用過多的資源。

gRPC 會使用每個訊息的大小限制來管理傳入和傳出訊息。 根據預設，gRPC 會將傳入訊息限制為 4 MB。 外寄訊息沒有任何限制。

在伺服器上，可以使用下列方式為應用程式中的所有服務設定 gRPC 訊息限制 `AddGrpc` ：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 1 * 1024 * 1024; // 1 MB
        options.MaxSendMessageSize = 1 * 1024 * 1024; // 1 MB
    });
}
```

您也可以使用來設定個別服務的限制 `AddServiceOptions<TService>` 。 如需設定訊息大小限制的詳細資訊，請參閱[gRPC configuration](xref:grpc/configuration)。

## <a name="client-certificate-validation"></a>用戶端憑證驗證

建立連接時，一開始會驗證[用戶端憑證](https://tools.ietf.org/html/rfc5246#section-7.4.4)。 根據預設，Kestrel 不會對連接的用戶端憑證執行額外的驗證。

我們建議以用戶端憑證保護的 gRPC 服務使用[AspNetCore 憑證](xref:security/authentication/certauth)封裝。 ASP.NET Core 認證驗證將在用戶端憑證上執行額外的驗證，包括：

* 憑證具有有效的擴充金鑰使用 (EKU) 
* 在其有效期間內
* 檢查憑證撤銷
