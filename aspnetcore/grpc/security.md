---
title: ASP.NET Core gRPC 中的安全性考慮
author: jamesnk
description: 瞭解 ASP.NET Core 的 gRPC 安全性考慮。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/07/2019
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
uid: grpc/security
ms.openlocfilehash: a7a595a71f988377bf25c500f04da2add3d85aef
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058827"
---
# <a name="security-considerations-in-grpc-for-aspnet-core"></a>ASP.NET Core gRPC 中的安全性考慮

依 [James 牛頓](https://twitter.com/jamesnk)

本文提供使用 .NET Core 保護 gRPC 的相關資訊。

## <a name="transport-security"></a>傳輸安全性

gRPC 訊息是使用 HTTP/2 來傳送和接收。 建議您：

* [傳輸層安全性 (TLS) ](https://tools.ietf.org/html/rfc5246) 用來保護生產 gRPC 應用程式中的訊息。
* gRPC 服務應該只接聽並回應安全的埠。

TLS 是在 Kestrel 中設定。 如需有關設定 Kestrel 端點的詳細資訊，請參閱 [Kestrel 端點](xref:fundamentals/servers/kestrel#endpoint-configuration)設定。

## <a name="exceptions"></a>例外狀況

例外狀況訊息通常會視為不應該向用戶端顯示的機密資料。 根據預設，gRPC 不會將 gRPC 服務擲回之例外狀況的詳細資料傳送給用戶端。 相反地，用戶端會收到一般訊息，指出發生錯誤。 您可以使用 [EnableDetailedErrors](xref:grpc/configuration#configure-services-options)來覆寫用戶端的例外狀況訊息傳遞 (例如開發或測試) 。 例外狀況訊息不應該在生產應用程式中公開給用戶端。

## <a name="message-size-limits"></a>訊息大小限制

GRPC 用戶端和服務的傳入訊息會載入到記憶體中。 訊息大小限制是一種機制，可協助防止 gRPC 耗用過度的資源。

gRPC 會使用每個訊息的大小限制來管理傳入和傳出訊息。 根據預設，gRPC 會將傳入訊息限制為 4 MB。 外寄訊息沒有限制。

在伺服器上，您可以使用下列方式，針對應用程式中的所有服務設定 gRPC 訊息限制 `AddGrpc` ：

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

您也可以使用來設定個別服務的限制 `AddServiceOptions<TService>` 。 如需有關設定訊息大小限制的詳細資訊，請參閱 [gRPC configuration](xref:grpc/configuration)。

## <a name="client-certificate-validation"></a>用戶端憑證驗證

[用戶端憑證](https://tools.ietf.org/html/rfc5246#section-7.4.4) 最初會在建立連線時進行驗證。 根據預設，Kestrel 不會執行額外的連線用戶端憑證驗證。

我們建議 gRPC 使用用戶端憑證保護的服務使用 [AspNetCore。憑證](xref:security/authentication/certauth) 套件。 ASP.NET Core 憑證驗證會對用戶端憑證執行額外的驗證，包括：

* 憑證具有有效的擴充金鑰使用 (EKU) 
* 在其有效期間內
* 檢查憑證撤銷
