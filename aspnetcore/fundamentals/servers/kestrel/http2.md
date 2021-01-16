---
title: 搭配 ASP.NET Core Kestrel web 伺服器使用 HTTP/2
author: rick-anderson
description: 瞭解如何搭配使用 HTTP/2 與 Kestrel，這是適用于 ASP.NET Core 的跨平臺 web 伺服器。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel/http2
ms.openlocfilehash: 431459bb6ece1d054146558ef865e44845a22686
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253931"
---
# <a name="use-http2-with-the-aspnet-core-kestrel-web-server"></a>搭配 ASP.NET Core Kestrel web 伺服器使用 HTTP/2

如果符合下列基本需求，則可以針對 ASP.NET Core 應用程式使用 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：

* 作業系統&dagger;
  * Windows Server 2016/Windows 10 或更新版本&Dagger;
  * Linux 含 OpenSSL 1.0.2 或更新版本 (例如 Ubuntu 16.04 或更新版本)
* 目標 Framework：.NET Core 2.2 或更新版本
* [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 連線
* TLS 1.2 或更新版本連線

&dagger;未來版本的 macOS 上將會支援 HTTP/2。
&Dagger;Kestrel 在 Windows Server 2012 R2 與 Windows 8.1 對 HTTP/2 的支援有限。 支援有限的原因是這些作業系統上的支援 TLS 密碼編譯套件清單有限。 可能需要使用橢圓曲線數位簽章演算法 (ECDSA) 產生的憑證來保護 TLS 連線。

如果已建立 HTTP/2 連線，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) 會報告 `HTTP/2`。

從 .NET Core 3.0 開始，預設會啟用 HTTP/2。 如需有關設定的詳細資訊，請參閱 [KESTREL HTTP/2 限制](xref:fundamentals/servers/kestrel/options#http2-limits) 和 [>listenoptions](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) 一節。

## <a name="advanced-http2-features"></a>Advanced HTTP/2 功能

Kestrel 支援 gRPC 中的額外 HTTP/2 功能，包括對回應結尾和傳送重設框架的支援。

### <a name="trailers"></a>預告片

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a>重設

[!INCLUDE[](~/includes/reset.md)]
