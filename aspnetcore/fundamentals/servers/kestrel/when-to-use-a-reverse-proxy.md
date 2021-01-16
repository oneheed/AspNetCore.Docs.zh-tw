---
title: 搭配 ASP.NET Core Kestrel web 伺服器使用反向 proxy 的時機
author: rick-anderson
description: 瞭解如何在 Kestrel 前面使用反向 proxy，ASP.NET Core 的跨平臺網頁伺服器。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/14/2021
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
uid: fundamentals/servers/kestrel/when-to-use-a-reverse-proxy
ms.openlocfilehash: fc89a9f841403bbccedff0a9c0720a08c11abdd6
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253923"
---
# <a name="when-to-use-kestrel-with-a-reverse-proxy"></a>何時搭配使用 Kestrel 與反向 Proxy

您可以單獨使用 Kestrel，或與 [Internet Information Services (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/) 等「反向 Proxy 伺服器」搭配使用。 反向 Proxy 伺服器會從網路接收 HTTP 要求，然後轉送到 Kestrel。

Kestrel 用作邊緣 (網際網路對應) 網頁伺服器：

![Kestrel 不使用反向 Proxy 伺服器直接與網際網路通訊](_static/kestrel-to-internet2.png)

Kestrel 用於反向 Proxy 組態中：

![Kestrel 透過 IIS、Nginx 或 Apache 等反向 Proxy 伺服器間接與網際網路通訊](_static/kestrel-to-internet.png)

不論是否有反向 proxy 伺服器，設定都是支援的裝載設定。

使用 Kestrel 做為沒有反向 proxy 伺服器的 edge server 時，不支援在多個進程之間共用相同的 IP 位址和埠。 當 Kestrel 設定為接聽埠時，Kestrel 會處理該埠的所有流量，而不論要求的 `Host` 標頭為何。 可以共用埠的反向 proxy 可以將要求轉送至唯一 IP 和埠上的 Kestrel。

即使不需要反向 Proxy 伺服器，使用反向 Proxy 伺服器也是不錯的選擇。

反向 Proxy：

* 可以限制它所主控之應用程式的公開介面區。
* 提供額外的組態和防禦層。
* 能夠與現有基礎結構更好地整合。
* 簡化負載平衡和安全通訊 (HTTPS) 組態。 只有反向 proxy 伺服器需要 x.509 憑證，而且該伺服器可以使用一般 HTTP 與內部網路上的應用程式伺服器進行通訊。

> [!WARNING]
> 裝載於反向 Proxy 組態需要[主機篩選](xref:fundamentals/servers/kestrel/host-filtering)。

## <a name="additional-resources"></a>其他資源

<xref:host-and-deploy/proxy-load-balancer>

