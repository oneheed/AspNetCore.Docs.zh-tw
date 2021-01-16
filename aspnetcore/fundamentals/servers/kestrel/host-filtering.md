---
title: 使用 ASP.NET Core Kestrel web 伺服器進行主機篩選
author: rick-anderson
description: 瞭解如何使用 Kestrel （適用于 ASP.NET Core 的跨平臺 web 伺服器）來使用主機篩選。
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
uid: fundamentals/servers/kestrel/host-filtering
ms.openlocfilehash: d55c211f05a77f6acabedef2ff62a621d9a1844e
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253934"
---
# <a name="host-filtering-with-aspnet-core-kestrel-web-server"></a>使用 ASP.NET Core Kestrel web 伺服器進行主機篩選

雖然 Kestrel 根據前置詞來支援組態，例如 `http://example.com:5000`，Kestrel 大多會忽略主機名稱。 主機 `localhost` 是特殊情況，用來繫結到回送位址。 任何非明確 IP 位址的主機，會繫結至所有公用 IP 位址。 `Host` 標頭未驗證。

因應措施是使用主機篩選中介軟體。 主機篩選中介軟體是由 [AspNetCore HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) 套件所提供，該套件是針對 ASP.NET Core 應用程式隱含提供的。 中介軟體是由 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> 所新增，它會呼叫 <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering%2A>：

[!code-csharp[](samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

預設停用主機篩選中介軟體。 若要啟用中介軟體，請 `AllowedHosts` 在 *appsettings.json* / *appsettings 中定義 \<EnvironmentName> 金鑰。json*。 此值是以分號分隔的主機名稱清單，不含連接埠號碼：

*appsettings.json*:

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> [轉送的標頭中介軟體](xref:host-and-deploy/proxy-load-balancer)也有 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> 選項。 在不同的案例中，轉送標頭中介軟體和主機篩選中介軟體有類似的功能。 當不保留 `Host` 標頭，卻使用反向 Proxy 伺服器或負載平衡器轉送要求時，可使用轉送標頭中介軟體設定 `AllowedHosts`。 當使用 Kestrel 作為公眾對應 Edge Server，或直接轉送 `Host` 標頭時，可使用主機篩選中介軟體設定 `AllowedHosts`。
>
> 如需轉送標頭中介軟體的詳細資訊，請參閱<xref:host-and-deploy/proxy-load-balancer>。
