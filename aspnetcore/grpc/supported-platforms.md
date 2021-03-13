---
title: .NET 支援平臺上的 gRPC
author: jamesnk
description: 瞭解在 .NET 上 gRPC 支援的平臺。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 3/11/2021
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
uid: grpc/supported-platforms
ms.openlocfilehash: c2bd808d16f11077e39aada829d79e8aedf2755b
ms.sourcegitcommit: 07e7ee573fe4e12be93249a385db745d714ff6ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2021
ms.locfileid: "103413414"
---
# <a name="grpc-on-net-supported-platforms"></a><span data-ttu-id="ad609-103">.NET 支援平臺上的 gRPC</span><span class="sxs-lookup"><span data-stu-id="ad609-103">gRPC on .NET supported platforms</span></span>

<span data-ttu-id="ad609-104">依 [James 牛頓](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="ad609-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="ad609-105">本文討論使用 gRPC 搭配 .NET 的需求和支援的平臺。</span><span class="sxs-lookup"><span data-stu-id="ad609-105">This article discusses the requirements and supported platforms for using gRPC with .NET.</span></span> <span data-ttu-id="ad609-106">這兩個主要 gRPC 工作負載有不同的需求：</span><span class="sxs-lookup"><span data-stu-id="ad609-106">There are different requirements for the two major gRPC workloads:</span></span>

* [<span data-ttu-id="ad609-107">在 ASP.NET Core 中裝載 gRPC 服務</span><span class="sxs-lookup"><span data-stu-id="ad609-107">Hosting gRPC services in ASP.NET Core</span></span>](#aspnet-core-grpc-server-requirements)
* [<span data-ttu-id="ad609-108">從 .NET 用戶端應用程式呼叫 gRPC</span><span class="sxs-lookup"><span data-stu-id="ad609-108">Calling gRPC from .NET client apps</span></span>](#net-grpc-client-requirements)

## <a name="wire-formats"></a><span data-ttu-id="ad609-109">有線格式</span><span class="sxs-lookup"><span data-stu-id="ad609-109">Wire-formats</span></span>

<span data-ttu-id="ad609-110">gRPC 利用 HTTP/2 中提供的 advanced 功能。</span><span class="sxs-lookup"><span data-stu-id="ad609-110">gRPC takes advantage of advanced features available in HTTP/2.</span></span> <span data-ttu-id="ad609-111">在任何位置都不支援 HTTP/2，但使用 HTTP/1.1 的第二個網路格式可供 gRPC：</span><span class="sxs-lookup"><span data-stu-id="ad609-111">HTTP/2 isn't supported everywhere, but a second wire-format using HTTP/1.1 is available for gRPC:</span></span>

* <span data-ttu-id="ad609-112">[`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) -gRPC over HTTP/2 是 gRPC 的一般使用方式。</span><span class="sxs-lookup"><span data-stu-id="ad609-112">[`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) - gRPC over HTTP/2 is how gRPC is typically used.</span></span>
* <span data-ttu-id="ad609-113">[`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) -gRPC-Web 會修改 gRPC 通訊協定，以與 HTTP/1.1 相容。</span><span class="sxs-lookup"><span data-stu-id="ad609-113">[`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) - gRPC-Web modifies the gRPC protocol to be compatible with HTTP/1.1.</span></span> <span data-ttu-id="ad609-114">gRPC-Web 可以在更多位置使用。</span><span class="sxs-lookup"><span data-stu-id="ad609-114">gRPC-Web can be used in more places.</span></span> <span data-ttu-id="ad609-115">gRPC Web 可供瀏覽器應用程式和網路使用，而不需要對 HTTP/2 的完整支援。</span><span class="sxs-lookup"><span data-stu-id="ad609-115">gRPC-Web can be used by browser apps and in networks without complete support for HTTP/2.</span></span> <span data-ttu-id="ad609-116">不再支援兩個 advanced gRPC 功能：用戶端串流和雙向串流。</span><span class="sxs-lookup"><span data-stu-id="ad609-116">Two advanced gRPC features are no longer supported: client streaming and bidirectional streaming.</span></span>

<span data-ttu-id="ad609-117">.NET 上的 gRPC 支援兩種網路格式。</span><span class="sxs-lookup"><span data-stu-id="ad609-117">gRPC on .NET supports both wire-formats.</span></span> <span data-ttu-id="ad609-118">`application/grpc` 預設使用。</span><span class="sxs-lookup"><span data-stu-id="ad609-118">`application/grpc` is used by default.</span></span> <span data-ttu-id="ad609-119">必須在用戶端和伺服器上設定 gRPC-Web，才能成功 gRPC Web 呼叫。</span><span class="sxs-lookup"><span data-stu-id="ad609-119">gRPC-Web must be configured on the client and the server for successful gRPC-Web calls.</span></span> <span data-ttu-id="ad609-120">如需設定 gRPC Web 的詳細資訊，請參閱 <xref:grpc/browser> 。</span><span class="sxs-lookup"><span data-stu-id="ad609-120">For information on setting up gRPC-Web, see <xref:grpc/browser>.</span></span>

## <a name="aspnet-core-grpc-server-requirements"></a><span data-ttu-id="ad609-121">ASP.NET Core gRPC 伺服器需求</span><span class="sxs-lookup"><span data-stu-id="ad609-121">ASP.NET Core gRPC server requirements</span></span>

<span data-ttu-id="ad609-122">使用 ASP.NET Core 裝載 gRPC 服務需要 .NET Core 3.x 或更新版本。</span><span class="sxs-lookup"><span data-stu-id="ad609-122">Hosting gRPC services with ASP.NET Core requires .NET Core 3.x or later.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="ad609-123">.NET 5 或更新版本</span><span class="sxs-lookup"><span data-stu-id="ad609-123">.NET 5 or later</span></span>
> * <span data-ttu-id="ad609-124">.NET Core 3</span><span class="sxs-lookup"><span data-stu-id="ad609-124">.NET Core 3</span></span>

<span data-ttu-id="ad609-125">ASP.NET Core gRPC 服務可以裝載于 .NET Core 支援的所有作業系統上。</span><span class="sxs-lookup"><span data-stu-id="ad609-125">ASP.NET Core gRPC services can be hosted on all operating system that .NET Core supports.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="ad609-126">Windows</span><span class="sxs-lookup"><span data-stu-id="ad609-126">Windows</span></span>
> * <span data-ttu-id="ad609-127">Linux</span><span class="sxs-lookup"><span data-stu-id="ad609-127">Linux</span></span>
> * <span data-ttu-id="ad609-128">macOS&dagger;</span><span class="sxs-lookup"><span data-stu-id="ad609-128">macOS&dagger;</span></span>

<span data-ttu-id="ad609-129">&dagger;[macOS 不支援使用 HTTPS 裝載 ASP.NET Core 應用程式](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)。</span><span class="sxs-lookup"><span data-stu-id="ad609-129">&dagger;[macOS doesn't support hosting ASP.NET Core apps with HTTPS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

### <a name="supported-aspnet-core-servers"></a><span data-ttu-id="ad609-130">支援的 ASP.NET 核心伺服器</span><span class="sxs-lookup"><span data-stu-id="ad609-130">Supported ASP.NET Core servers</span></span>

<span data-ttu-id="ad609-131">支援所有內建的 ASP.NET 核心伺服器。</span><span class="sxs-lookup"><span data-stu-id="ad609-131">All built-in ASP.NET Core servers are supported.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="ad609-132">Kestrel</span><span class="sxs-lookup"><span data-stu-id="ad609-132">Kestrel</span></span>
> * <span data-ttu-id="ad609-133">TestServer</span><span class="sxs-lookup"><span data-stu-id="ad609-133">TestServer</span></span>
> * <span data-ttu-id="ad609-134">IIS&dagger;</span><span class="sxs-lookup"><span data-stu-id="ad609-134">IIS&dagger;</span></span>
> * <span data-ttu-id="ad609-135">HTTP.sys&Dagger;</span><span class="sxs-lookup"><span data-stu-id="ad609-135">HTTP.sys&Dagger;</span></span>

<span data-ttu-id="ad609-136">&dagger;IIS 需要 .NET 5 和 Windows 10 組建20241或更新版本。</span><span class="sxs-lookup"><span data-stu-id="ad609-136">&dagger;IIS requires .NET 5 and Windows 10 Build 20241 or later.</span></span>

<span data-ttu-id="ad609-137">&Dagger;HTTP.sys 需要 .NET 5 和 Windows 10 組建19529或更新版本。</span><span class="sxs-lookup"><span data-stu-id="ad609-137">&Dagger;HTTP.sys requires .NET 5 and Windows 10 Build 19529 or later.</span></span>

<span data-ttu-id="ad609-138">如需設定 ASP.NET 核心伺服器以執行 gRPC 的詳細資訊，請參閱 <xref:grpc/aspnetcore#server-options> 。</span><span class="sxs-lookup"><span data-stu-id="ad609-138">For information about configuring ASP.NET Core servers to run gRPC, see <xref:grpc/aspnetcore#server-options>.</span></span>

### <a name="azure-services"></a><span data-ttu-id="ad609-139">Azure 服務</span><span class="sxs-lookup"><span data-stu-id="ad609-139">Azure services</span></span>

> [!div class="checklist"]
>
> * [<span data-ttu-id="ad609-140">Azure Kubernetes Service (AKS)</span><span class="sxs-lookup"><span data-stu-id="ad609-140">Azure Kubernetes Service (AKS)</span></span>](https://azure.microsoft.com/services/kubernetes-service/)
> * <span data-ttu-id="ad609-141">[Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;</span><span class="sxs-lookup"><span data-stu-id="ad609-141">[Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;</span></span>

<span data-ttu-id="ad609-142">&dagger;Azure App Service 不支援透過 HTTP/2 裝載 gRPC。</span><span class="sxs-lookup"><span data-stu-id="ad609-142">&dagger;Azure App Service doesn't support hosting gRPC over HTTP/2.</span></span> <span data-ttu-id="ad609-143">gRPC-Web 是相容的替代方案。</span><span class="sxs-lookup"><span data-stu-id="ad609-143">gRPC-Web is a compatible alternative.</span></span>

<span data-ttu-id="ad609-144">正在進行工作，以改善在 Azure App Service 中使用 HTTP/2 的 gRPC 支援。</span><span class="sxs-lookup"><span data-stu-id="ad609-144">Work is in-progress to improve support for gRPC with HTTP/2 in Azure App Service.</span></span> <span data-ttu-id="ad609-145">如需詳細資訊，請參閱 [此 GitHub 問題](https://github.com/dotnet/AspNetCore/issues/9020)。</span><span class="sxs-lookup"><span data-stu-id="ad609-145">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/9020).</span></span>

## <a name="net-grpc-client-requirements"></a><span data-ttu-id="ad609-146">.NET gRPC 用戶端需求</span><span class="sxs-lookup"><span data-stu-id="ad609-146">.NET gRPC client requirements</span></span>

<span data-ttu-id="ad609-147">[Grpc .net. 用戶端](https://www.nuget.org/packages/Grpc.Net.Client/)套件支援在 .net Core 3 和 .net 5 或更新版本上透過 HTTP/2 的 Grpc 呼叫。</span><span class="sxs-lookup"><span data-stu-id="ad609-147">The [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client/) package supports gRPC calls over HTTP/2 on .NET Core 3 and .NET 5 or later.</span></span>

<span data-ttu-id="ad609-148">有限的支援可透過 .NET Framework 上的 HTTP/2 進行 gRPC。</span><span class="sxs-lookup"><span data-stu-id="ad609-148">Limited support is available for gRPC over HTTP/2 on .NET Framework.</span></span> <span data-ttu-id="ad609-149">其他 .NET 版本（例如 UWP、Xamarin 和 Unity）都不需要 HTTP/2 支援，而且必須改用 gRPC Web。</span><span class="sxs-lookup"><span data-stu-id="ad609-149">Other .NET versions such as UWP, Xamarin and Unity don't have required HTTP/2 support, and must use gRPC-Web instead.</span></span>

<span data-ttu-id="ad609-150">下表列出 .NET 的執行和其 gRPC 用戶端支援：</span><span class="sxs-lookup"><span data-stu-id="ad609-150">The following table lists .NET implementations and their gRPC client support:</span></span>

| <span data-ttu-id="ad609-151">.NET 實作</span><span class="sxs-lookup"><span data-stu-id="ad609-151">.NET implementation</span></span>                          | <span data-ttu-id="ad609-152">透過 HTTP/2 的 gRPC</span><span class="sxs-lookup"><span data-stu-id="ad609-152">gRPC over HTTP/2</span></span>   | <span data-ttu-id="ad609-153">gRPC-Web</span><span class="sxs-lookup"><span data-stu-id="ad609-153">gRPC-Web</span></span>   |
|----------------------------------------------|--------------------|------------|
| <span data-ttu-id="ad609-154">.NET 5 或更新版本</span><span class="sxs-lookup"><span data-stu-id="ad609-154">.NET 5 or later</span></span>                              | <span data-ttu-id="ad609-155">✔️</span><span class="sxs-lookup"><span data-stu-id="ad609-155">✔️</span></span>                | <span data-ttu-id="ad609-156">✔️</span><span class="sxs-lookup"><span data-stu-id="ad609-156">✔️</span></span>         |
| <span data-ttu-id="ad609-157">.NET Core 3</span><span class="sxs-lookup"><span data-stu-id="ad609-157">.NET Core 3</span></span>                                  | <span data-ttu-id="ad609-158">✔️</span><span class="sxs-lookup"><span data-stu-id="ad609-158">✔️</span></span>                | <span data-ttu-id="ad609-159">✔️</span><span class="sxs-lookup"><span data-stu-id="ad609-159">✔️</span></span>         |
| <span data-ttu-id="ad609-160">.NET Core 2.1</span><span class="sxs-lookup"><span data-stu-id="ad609-160">.NET Core 2.1</span></span>                                | ❌                | <span data-ttu-id="ad609-161">✔️</span><span class="sxs-lookup"><span data-stu-id="ad609-161">✔️</span></span>         |
| <span data-ttu-id="ad609-162">.NET Framework 4.6.1</span><span class="sxs-lookup"><span data-stu-id="ad609-162">.NET Framework 4.6.1</span></span>                         | ⚠️&dagger;        | <span data-ttu-id="ad609-163">✔️</span><span class="sxs-lookup"><span data-stu-id="ad609-163">✔️</span></span>         |
| Blazor WebAssembly                           | ❌                | <span data-ttu-id="ad609-164">✔️</span><span class="sxs-lookup"><span data-stu-id="ad609-164">✔️</span></span>         |
| <span data-ttu-id="ad609-165">Mono 5.4</span><span class="sxs-lookup"><span data-stu-id="ad609-165">Mono 5.4</span></span>                                     | ❌                | <span data-ttu-id="ad609-166">✔️</span><span class="sxs-lookup"><span data-stu-id="ad609-166">✔️</span></span>         |
| <span data-ttu-id="ad609-167">Xamarin.iOS 10.14</span><span class="sxs-lookup"><span data-stu-id="ad609-167">Xamarin.iOS 10.14</span></span>                            | ❌                | <span data-ttu-id="ad609-168">✔️</span><span class="sxs-lookup"><span data-stu-id="ad609-168">✔️</span></span>         |
| <span data-ttu-id="ad609-169">Xamarin.Android 8.0</span><span class="sxs-lookup"><span data-stu-id="ad609-169">Xamarin.Android 8.0</span></span>                          | ❌                | <span data-ttu-id="ad609-170">✔️</span><span class="sxs-lookup"><span data-stu-id="ad609-170">✔️</span></span>         |
| <span data-ttu-id="ad609-171">通用 Windows 平台 10.0.16299</span><span class="sxs-lookup"><span data-stu-id="ad609-171">Universal Windows Platform 10.0.16299</span></span>        | ❌                | <span data-ttu-id="ad609-172">✔️</span><span class="sxs-lookup"><span data-stu-id="ad609-172">✔️</span></span>         |
| <span data-ttu-id="ad609-173">Unity 2018。1</span><span class="sxs-lookup"><span data-stu-id="ad609-173">Unity 2018.1</span></span>                                 | ❌                | <span data-ttu-id="ad609-174">✔️</span><span class="sxs-lookup"><span data-stu-id="ad609-174">✔️</span></span>         |

<span data-ttu-id="ad609-175">&dagger;需要設定 .NET Framework <xref:System.Net.Http.WinHttpHandler> 和 Windows 10 組建19622或更新版本。</span><span class="sxs-lookup"><span data-stu-id="ad609-175">&dagger;.NET Framework requires <xref:System.Net.Http.WinHttpHandler> to be configured and Windows 10 Build 19622 or later.</span></span>

<span data-ttu-id="ad609-176">`Grpc.Net.Client`在 .Net Framework 或 GRPC Web 上使用需要額外的設定。</span><span class="sxs-lookup"><span data-stu-id="ad609-176">Using `Grpc.Net.Client` on .NET Framework or with gRPC-Web requires additional configuration.</span></span> <span data-ttu-id="ad609-177">如需詳細資訊，請參閱<xref:grpc/netstandard>。</span><span class="sxs-lookup"><span data-stu-id="ad609-177">For more information, see <xref:grpc/netstandard>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ad609-178">其他資源</span><span class="sxs-lookup"><span data-stu-id="ad609-178">Additional resources</span></span>

* <xref:grpc/netstandard>
* [<span data-ttu-id="ad609-179">gRPC c # 核心-程式庫</span><span class="sxs-lookup"><span data-stu-id="ad609-179">gRPC C# core-library</span></span>](https://grpc.io/docs/languages/csharp/quickstart/)
