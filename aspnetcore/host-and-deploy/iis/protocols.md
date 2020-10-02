---
title: 在 IIS 上搭配 HTTP/2 使用 ASP.NET Core
author: rick-anderson
description: 瞭解如何搭配 IIS 使用 HTTP/2 功能。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
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
uid: host-and-deploy/iis/protocols
ms.openlocfilehash: 1c3748eeead1b8cccdb6112150cf90fb5597b689
ms.sourcegitcommit: e519d95d17443abafba8f712ac168347b15c8b57
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2020
ms.locfileid: "91654368"
---
# <a name="use-aspnet-core-with-http2-on-iis"></a>在 IIS 上搭配 HTTP/2 使用 ASP.NET Core

作者：[Justin Kotalik](https://github.com/jkotalik)

在下列 IIS 部署案例中，ASP.NET Core 支援 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：

* Windows Server 2016 或更新版本/Windows 10 或更新版本
* IIS 10 或更新版本
* TLS 1.2 或更新版本連線
* [裝載跨進程](xref:host-and-deploy/iis/index#out-of-process-hosting-model)時：公開的 edge server 連線使用 HTTP/2，但[Kestrel 伺服器](xref:fundamentals/servers/kestrel)的反向 proxy 連線使用 HTTP/1.1。

若為建立 HTTP/2 連接時的同進程部署，則為 [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 報告 `HTTP/2` 。 若為建立 HTTP/2 連接時的跨進程部署，則為 [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 報告 `HTTP/1.1` 。

如需有關同處理序和跨處理序主控模型的詳細資訊，請參閱 <xref:host-and-deploy/aspnet-core-module>。

HTTPS/TLS 連接預設會啟用 HTTP/2。 如果 HTTP/2 連線尚未建立，連線會退為 HTTP/1.1。 如需使用 IIS 部署之 HTTP/2 設定的詳細資訊，請參閱 [IIS 上的 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)。

## <a name="advanced-http2-features-to-support-grpc"></a>支援 gRPC 的 Advanced HTTP/2 功能

IIS 中的額外 HTTP/2 功能支援 gRPC，包括對回應結尾和傳送重設框架的支援。

在 IIS 上執行 gRPC 的需求：

* 同進程裝載。
* Windows 10、OS 組建20300.1000 或更新版本。 可能需要使用 Windows 測試人員組建。
* TLS 1.2 或更新版本連線

### <a name="trailers"></a>預告片

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a>重設

[!INCLUDE[](~/includes/reset.md)]
