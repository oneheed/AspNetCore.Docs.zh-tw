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
# <a name="host-filtering-with-aspnet-core-kestrel-web-server"></a><span data-ttu-id="3f731-103">使用 ASP.NET Core Kestrel web 伺服器進行主機篩選</span><span class="sxs-lookup"><span data-stu-id="3f731-103">Host filtering with ASP.NET Core Kestrel web server</span></span>

<span data-ttu-id="3f731-104">雖然 Kestrel 根據前置詞來支援組態，例如 `http://example.com:5000`，Kestrel 大多會忽略主機名稱。</span><span class="sxs-lookup"><span data-stu-id="3f731-104">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="3f731-105">主機 `localhost` 是特殊情況，用來繫結到回送位址。</span><span class="sxs-lookup"><span data-stu-id="3f731-105">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="3f731-106">任何非明確 IP 位址的主機，會繫結至所有公用 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="3f731-106">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="3f731-107">`Host` 標頭未驗證。</span><span class="sxs-lookup"><span data-stu-id="3f731-107">`Host` headers aren't validated.</span></span>

<span data-ttu-id="3f731-108">因應措施是使用主機篩選中介軟體。</span><span class="sxs-lookup"><span data-stu-id="3f731-108">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="3f731-109">主機篩選中介軟體是由 [AspNetCore HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) 套件所提供，該套件是針對 ASP.NET Core 應用程式隱含提供的。</span><span class="sxs-lookup"><span data-stu-id="3f731-109">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="3f731-110">中介軟體是由 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> 所新增，它會呼叫 <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering%2A>：</span><span class="sxs-lookup"><span data-stu-id="3f731-110">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering%2A>:</span></span>

[!code-csharp[](samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="3f731-111">預設停用主機篩選中介軟體。</span><span class="sxs-lookup"><span data-stu-id="3f731-111">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="3f731-112">若要啟用中介軟體，請 `AllowedHosts` 在 *appsettings.json* / *appsettings 中定義 \<EnvironmentName> 金鑰。json*。</span><span class="sxs-lookup"><span data-stu-id="3f731-112">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="3f731-113">此值是以分號分隔的主機名稱清單，不含連接埠號碼：</span><span class="sxs-lookup"><span data-stu-id="3f731-113">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="3f731-114">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="3f731-114">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="3f731-115">[轉送的標頭中介軟體](xref:host-and-deploy/proxy-load-balancer)也有 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> 選項。</span><span class="sxs-lookup"><span data-stu-id="3f731-115">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="3f731-116">在不同的案例中，轉送標頭中介軟體和主機篩選中介軟體有類似的功能。</span><span class="sxs-lookup"><span data-stu-id="3f731-116">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="3f731-117">當不保留 `Host` 標頭，卻使用反向 Proxy 伺服器或負載平衡器轉送要求時，可使用轉送標頭中介軟體設定 `AllowedHosts`。</span><span class="sxs-lookup"><span data-stu-id="3f731-117">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="3f731-118">當使用 Kestrel 作為公眾對應 Edge Server，或直接轉送 `Host` 標頭時，可使用主機篩選中介軟體設定 `AllowedHosts`。</span><span class="sxs-lookup"><span data-stu-id="3f731-118">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="3f731-119">如需轉送標頭中介軟體的詳細資訊，請參閱<xref:host-and-deploy/proxy-load-balancer>。</span><span class="sxs-lookup"><span data-stu-id="3f731-119">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>
