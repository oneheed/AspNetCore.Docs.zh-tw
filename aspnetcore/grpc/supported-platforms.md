---
title: .NET 支援平臺上的 gRPC
author: jamesnk
description: 瞭解在 .NET 上 gRPC 支援的平臺。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/22/2021
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
ms.openlocfilehash: 6e48a19027f79b75edeebde9c584419871fba533
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530160"
---
# <a name="grpc-on-net-supported-platforms"></a><span data-ttu-id="e1df7-103">.NET 支援平臺上的 gRPC</span><span class="sxs-lookup"><span data-stu-id="e1df7-103">gRPC on .NET supported platforms</span></span>

<span data-ttu-id="e1df7-104">依 [James 牛頓](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="e1df7-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="e1df7-105">本文討論使用 gRPC 搭配 .NET 的需求和支援的平臺。</span><span class="sxs-lookup"><span data-stu-id="e1df7-105">This article discusses the requirements and supported platforms for using gRPC with .NET.</span></span>

<span data-ttu-id="e1df7-106">gRPC 利用 HTTP/2 中提供的 advanced 功能。</span><span class="sxs-lookup"><span data-stu-id="e1df7-106">gRPC takes advantage of advanced features available in  HTTP/2.</span></span> <span data-ttu-id="e1df7-107">在任何位置都不支援 HTTP/2，但使用 HTTP/1.1 的第二個網路格式可供 gRPC：</span><span class="sxs-lookup"><span data-stu-id="e1df7-107">HTTP/2 isn't supported everywhere, but a second wire-format using HTTP/1.1 is available for gRPC:</span></span>

* <span data-ttu-id="e1df7-108">[`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) -gRPC over HTTP/2 是 gRPC 的一般使用方式。</span><span class="sxs-lookup"><span data-stu-id="e1df7-108">[`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) - gRPC over HTTP/2 is how gRPC is typically used.</span></span>
* <span data-ttu-id="e1df7-109">[`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) -gRPC-Web 會修改 gRPC 通訊協定，以與 HTTP/1.1 相容。</span><span class="sxs-lookup"><span data-stu-id="e1df7-109">[`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) - gRPC-Web modifies the gRPC protocol to be compatible with HTTP/1.1.</span></span> <span data-ttu-id="e1df7-110">gRPC Web 可以在更多位置使用，尤其是瀏覽器應用程式可以呼叫它。</span><span class="sxs-lookup"><span data-stu-id="e1df7-110">gRPC-Web can be used in more places, notably it is callable by browser apps.</span></span> <span data-ttu-id="e1df7-111">不再支援兩個 advanced gRPC 功能：用戶端串流和雙向串流。</span><span class="sxs-lookup"><span data-stu-id="e1df7-111">Two advanced gRPC features are no longer supported: client streaming and bidirectional streaming.</span></span>

<span data-ttu-id="e1df7-112">.NET 上的 gRPC 支援兩種網路格式。</span><span class="sxs-lookup"><span data-stu-id="e1df7-112">gRPC on .NET supports both wire-formats.</span></span> <span data-ttu-id="e1df7-113">預設會使用 gRPC over HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="e1df7-113">gRPC over HTTP/2 is used by default.</span></span> <span data-ttu-id="e1df7-114">如需設定 gRPC Web 的詳細資訊，請參閱 <xref:grpc/browser> 。</span><span class="sxs-lookup"><span data-stu-id="e1df7-114">For information on setting up gRPC-Web, see <xref:grpc/browser>.</span></span>

## <a name="device-requirements"></a><span data-ttu-id="e1df7-115">裝置需求</span><span class="sxs-lookup"><span data-stu-id="e1df7-115">Device requirements</span></span>

<span data-ttu-id="e1df7-116">.NET 上的 gRPC 支援 .NET Core 支援的任何裝置。</span><span class="sxs-lookup"><span data-stu-id="e1df7-116">gRPC on .NET supports any device that .NET Core supports.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="e1df7-117">Windows</span><span class="sxs-lookup"><span data-stu-id="e1df7-117">Windows</span></span>
> * <span data-ttu-id="e1df7-118">Linux</span><span class="sxs-lookup"><span data-stu-id="e1df7-118">Linux</span></span>
> * <span data-ttu-id="e1df7-119">macOS&dagger;</span><span class="sxs-lookup"><span data-stu-id="e1df7-119">macOS&dagger;</span></span>
> * <span data-ttu-id="e1df7-120">瀏覽器&Dagger;</span><span class="sxs-lookup"><span data-stu-id="e1df7-120">Browsers&Dagger;</span></span>

<span data-ttu-id="e1df7-121">&dagger;[macOS 不支援使用 HTTPS 裝載 ASP.NET Core 應用程式](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)。</span><span class="sxs-lookup"><span data-stu-id="e1df7-121">&dagger;[macOS doesn't support hosting ASP.NET Core apps with HTTPS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span> <span data-ttu-id="e1df7-122">macOS 上的 gRPC 用戶端可以呼叫使用 HTTPS 的遠端服務。</span><span class="sxs-lookup"><span data-stu-id="e1df7-122">gRPC clients on macOS can call remote services that use HTTPS.</span></span>

<span data-ttu-id="e1df7-123">&Dagger;Blazor WebAssembly 應用程式可以使用 gRPC （Web）來呼叫 gRPC 服務。</span><span class="sxs-lookup"><span data-stu-id="e1df7-123">&Dagger;Blazor WebAssembly apps can call gRPC services with gRPC-Web.</span></span>

## <a name="aspnet-core-server-requirements"></a><span data-ttu-id="e1df7-124">ASP.NET Core 伺服器需求</span><span class="sxs-lookup"><span data-stu-id="e1df7-124">ASP.NET Core server requirements</span></span>

<span data-ttu-id="e1df7-125">gRPC 服務可以裝載在所有內建的 ASP.NET Core 伺服器上。</span><span class="sxs-lookup"><span data-stu-id="e1df7-125">gRPC services can be hosted on all built-in ASP.NET Core servers.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="e1df7-126">Kestrel</span><span class="sxs-lookup"><span data-stu-id="e1df7-126">Kestrel</span></span>
> * <span data-ttu-id="e1df7-127">TestServer</span><span class="sxs-lookup"><span data-stu-id="e1df7-127">TestServer</span></span>
> * <span data-ttu-id="e1df7-128">IIS&dagger;</span><span class="sxs-lookup"><span data-stu-id="e1df7-128">IIS&dagger;</span></span>
> * <span data-ttu-id="e1df7-129">HTTP.sys&Dagger;</span><span class="sxs-lookup"><span data-stu-id="e1df7-129">HTTP.sys&Dagger;</span></span>

<span data-ttu-id="e1df7-130">&dagger;IIS 需要 .NET 5 和 Windows 10 組建20241或更新版本。</span><span class="sxs-lookup"><span data-stu-id="e1df7-130">&dagger;IIS requires .NET 5 and Windows 10 Build 20241 or later.</span></span>

<span data-ttu-id="e1df7-131">&Dagger;HTTP.sys 需要 .NET 5 和 Windows 10 組建19529或更新版本。</span><span class="sxs-lookup"><span data-stu-id="e1df7-131">&Dagger;HTTP.sys requires .NET 5 and Windows 10 Build 19529 or later.</span></span>

<span data-ttu-id="e1df7-132">如需設定 ASP.NET Core 伺服器來執行 gRPC 的相關資訊，請參閱 <xref:grpc/aspnetcore#server-options> 。</span><span class="sxs-lookup"><span data-stu-id="e1df7-132">For information about configuring ASP.NET Core servers to run gRPC, see <xref:grpc/aspnetcore#server-options>.</span></span>

## <a name="net-version-requirements"></a><span data-ttu-id="e1df7-133">.NET 版本需求</span><span class="sxs-lookup"><span data-stu-id="e1df7-133">.NET version requirements</span></span>

<span data-ttu-id="e1df7-134">.NET 上的 gRPC 支援 .NET Core 3 和 .NET 5 或更新版本。</span><span class="sxs-lookup"><span data-stu-id="e1df7-134">gRPC on .NET supports .NET Core 3 and .NET 5 or later.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="e1df7-135">.NET 5 或更新版本</span><span class="sxs-lookup"><span data-stu-id="e1df7-135">.NET 5 or later</span></span>
> * <span data-ttu-id="e1df7-136">.NET Core 3</span><span class="sxs-lookup"><span data-stu-id="e1df7-136">.NET Core 3</span></span>

<span data-ttu-id="e1df7-137">gRPC on .NET 不支援在 .NET Framework 和 Xamarin 上執行。</span><span class="sxs-lookup"><span data-stu-id="e1df7-137">gRPC on .NET doesn't support running on .NET Framework and Xamarin.</span></span> <span data-ttu-id="e1df7-138">[GRPC c # core-程式庫](https://grpc.io/docs/languages/csharp/quickstart/) 是支援 .NET Framework 和 Xamarin 的協力廠商程式庫。</span><span class="sxs-lookup"><span data-stu-id="e1df7-138">[gRPC C# core-library](https://grpc.io/docs/languages/csharp/quickstart/) is a third party library that supports .NET Framework and Xamarin.</span></span> <span data-ttu-id="e1df7-139">Microsoft 不支援 gRPC C-核心。</span><span class="sxs-lookup"><span data-stu-id="e1df7-139">gRPC C-core is not supported by Microsoft.</span></span>

## <a name="azure-services"></a><span data-ttu-id="e1df7-140">Azure 服務</span><span class="sxs-lookup"><span data-stu-id="e1df7-140">Azure services</span></span>

> [!div class="checklist"]
>
> * [<span data-ttu-id="e1df7-141">Azure Kubernetes Service (AKS)</span><span class="sxs-lookup"><span data-stu-id="e1df7-141">Azure Kubernetes Service (AKS)</span></span>](https://azure.microsoft.com/services/kubernetes-service/)
> * <span data-ttu-id="e1df7-142">[Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;</span><span class="sxs-lookup"><span data-stu-id="e1df7-142">[Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;</span></span>

<span data-ttu-id="e1df7-143">&dagger;Azure App Service 不支援透過 HTTP/2 裝載 gRPC。</span><span class="sxs-lookup"><span data-stu-id="e1df7-143">&dagger;Azure App Service doesn't support hosting gRPC over HTTP/2.</span></span> <span data-ttu-id="e1df7-144">gRPC-Web 是相容的替代方案。</span><span class="sxs-lookup"><span data-stu-id="e1df7-144">gRPC-Web is a compatible alternative.</span></span>

<span data-ttu-id="e1df7-145">工作正在進行中，以 Azure App Service 中的 HTTP/2 改善 gRPC 支援。</span><span class="sxs-lookup"><span data-stu-id="e1df7-145">Work is in-progress to improve support for gRPC with HTTP/2 in Azure App Service.</span></span> <span data-ttu-id="e1df7-146">如需詳細資訊，請參閱 [此 GitHub 問題](https://github.com/dotnet/AspNetCore/issues/9020)。</span><span class="sxs-lookup"><span data-stu-id="e1df7-146">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/9020).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e1df7-147">其他資源</span><span class="sxs-lookup"><span data-stu-id="e1df7-147">Additional resources</span></span>

* [<span data-ttu-id="e1df7-148">gRPC c # 核心-程式庫</span><span class="sxs-lookup"><span data-stu-id="e1df7-148">gRPC C# core-library</span></span>](https://grpc.io/docs/languages/csharp/quickstart/)
