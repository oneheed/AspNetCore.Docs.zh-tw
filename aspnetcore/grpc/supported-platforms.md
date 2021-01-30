---
title: 適用于 .NET 的 gRPC 支援平臺
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
ms.openlocfilehash: 92ca38875c6618c8630a66af16548d32bc469a62
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057710"
---
# <a name="grpc-for-net-supported-platforms"></a><span data-ttu-id="f6f75-103">適用于 .NET 的 gRPC 支援平臺</span><span class="sxs-lookup"><span data-stu-id="f6f75-103">gRPC for .NET supported platforms</span></span>

<span data-ttu-id="f6f75-104">依 [James 牛頓](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="f6f75-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="f6f75-105">本文討論使用 gRPC 搭配 .NET 的需求和支援的平臺。</span><span class="sxs-lookup"><span data-stu-id="f6f75-105">This article discusses the requirements and supported platforms for using gRPC with .NET.</span></span>

<span data-ttu-id="f6f75-106">gRPC 的設計目的是要針對一些更先進的功能使用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="f6f75-106">gRPC is designed to use HTTP/2 for some of its more advanced features.</span></span> <span data-ttu-id="f6f75-107">在所有可能防止使用 gRPC 的地方，都不支援 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="f6f75-107">HTTP/2 isn't supported everywhere which can prevent using gRPC.</span></span> <span data-ttu-id="f6f75-108">因為這種情況下，第二種連線格式與 HTTP/1.1 相容，可在用戶端與伺服器之間傳送 gRPC 呼叫：</span><span class="sxs-lookup"><span data-stu-id="f6f75-108">Because of this there is second wire-format that is compatible with HTTP/1.1 for sending gRPC calls between clients and servers:</span></span>

* <span data-ttu-id="f6f75-109">[`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) -gRPC over HTTP/2 是 gRPC 的一般使用方式。</span><span class="sxs-lookup"><span data-stu-id="f6f75-109">[`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) - gRPC over HTTP/2 is how gRPC is typically used.</span></span>
* <span data-ttu-id="f6f75-110">[`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) -gRPC-Web 會修改 gRPC 通訊協定，以與 HTTP/1.1 相容。</span><span class="sxs-lookup"><span data-stu-id="f6f75-110">[`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) - gRPC-Web modifies the gRPC protocol to be compatible with HTTP/1.1.</span></span> <span data-ttu-id="f6f75-111">gRPC Web 可以在更多位置使用，尤其是瀏覽器應用程式可以呼叫它。</span><span class="sxs-lookup"><span data-stu-id="f6f75-111">gRPC-Web can be used in more places, notably it is callable by browser apps.</span></span> <span data-ttu-id="f6f75-112">不再支援兩個 advanced gRPC 功能：用戶端串流和雙向串流。</span><span class="sxs-lookup"><span data-stu-id="f6f75-112">Two advanced gRPC features are no longer supported: client streaming and bidirectional streaming.</span></span>

<span data-ttu-id="f6f75-113">適用于 .NET 的 gRPC 支援兩種網路格式。</span><span class="sxs-lookup"><span data-stu-id="f6f75-113">gRPC for .NET supports both wire-formats.</span></span> <span data-ttu-id="f6f75-114">如需設定 gRPC Web 的詳細資訊，請參閱 <xref:grpc/browser> 。</span><span class="sxs-lookup"><span data-stu-id="f6f75-114">For information on setting up gRPC-Web, see <xref:grpc/browser>.</span></span>

## <a name="device-requirements"></a><span data-ttu-id="f6f75-115">裝置需求</span><span class="sxs-lookup"><span data-stu-id="f6f75-115">Device requirements</span></span>

<span data-ttu-id="f6f75-116">gRPC for .NET 支援 .NET Core 支援的任何裝置。</span><span class="sxs-lookup"><span data-stu-id="f6f75-116">gRPC for .NET supports any device that .NET Core supports.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="f6f75-117">Windows</span><span class="sxs-lookup"><span data-stu-id="f6f75-117">Windows</span></span>
> * <span data-ttu-id="f6f75-118">Linux</span><span class="sxs-lookup"><span data-stu-id="f6f75-118">Linux</span></span>
> * <span data-ttu-id="f6f75-119">macOS&dagger;</span><span class="sxs-lookup"><span data-stu-id="f6f75-119">macOS&dagger;</span></span>
> * <span data-ttu-id="f6f75-120">瀏覽器&Dagger;</span><span class="sxs-lookup"><span data-stu-id="f6f75-120">Browsers&Dagger;</span></span>

<span data-ttu-id="f6f75-121">&dagger;MacOS 上託管的應用程式不支援 HTTPS。 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f6f75-121">&dagger;ASP.NET Core apps hosted on macOS don't support HTTPS.</span></span> <span data-ttu-id="f6f75-122">呼叫遠端服務時，macOS 上的 gRPC 用戶端仍然可以使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="f6f75-122">gRPC clients on macOS can still use HTTPS when calling remote services.</span></span>

<span data-ttu-id="f6f75-123">&Dagger;Blazor WebAssembly 應用程式可以使用 gRPC （Web）來呼叫 gRPC 服務。</span><span class="sxs-lookup"><span data-stu-id="f6f75-123">&Dagger;Blazor WebAssembly apps can call gRPC services with gRPC-Web.</span></span>

## <a name="aspnet-core-server-requirements"></a><span data-ttu-id="f6f75-124">ASP.NET Core 伺服器需求</span><span class="sxs-lookup"><span data-stu-id="f6f75-124">ASP.NET Core server requirements</span></span>

<span data-ttu-id="f6f75-125">gRPC 服務可以裝載在所有內建的 ASP.NET Core 伺服器上。</span><span class="sxs-lookup"><span data-stu-id="f6f75-125">gRPC services can be hosted on all built-in ASP.NET Core servers.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="f6f75-126">Kestrel</span><span class="sxs-lookup"><span data-stu-id="f6f75-126">Kestrel</span></span>
> * <span data-ttu-id="f6f75-127">TestServer</span><span class="sxs-lookup"><span data-stu-id="f6f75-127">TestServer</span></span>
> * <span data-ttu-id="f6f75-128">IIS&dagger;</span><span class="sxs-lookup"><span data-stu-id="f6f75-128">IIS&dagger;</span></span>
> * <span data-ttu-id="f6f75-129">HTTP.sys&dagger;</span><span class="sxs-lookup"><span data-stu-id="f6f75-129">HTTP.sys&dagger;</span></span>

<span data-ttu-id="f6f75-130">&dagger;IIS 和 HTTP.sys 需要 .NET 5 和 Windows 10 組建20241或更新版本。</span><span class="sxs-lookup"><span data-stu-id="f6f75-130">&dagger;IIS and HTTP.sys require .NET 5 and Windows 10 Build 20241 or later.</span></span>

<span data-ttu-id="f6f75-131">如需詳細資訊，請參閱<xref:grpc/aspnetcore>。</span><span class="sxs-lookup"><span data-stu-id="f6f75-131">For more information, see <xref:grpc/aspnetcore>.</span></span>

## <a name="net-version-requirements"></a><span data-ttu-id="f6f75-132">.NET 版本需求</span><span class="sxs-lookup"><span data-stu-id="f6f75-132">.NET version requirements</span></span>

<span data-ttu-id="f6f75-133">適用于 .NET 的 gRPC 支援 .NET Core 3 和 .NET 5 或更新版本。</span><span class="sxs-lookup"><span data-stu-id="f6f75-133">gRPC for .NET supports .NET Core 3 and .NET 5 or later.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="f6f75-134">.NET 5 或更新版本</span><span class="sxs-lookup"><span data-stu-id="f6f75-134">.NET 5 or later</span></span>
> * <span data-ttu-id="f6f75-135">.NET Core 3</span><span class="sxs-lookup"><span data-stu-id="f6f75-135">.NET Core 3</span></span>

<span data-ttu-id="f6f75-136">gRPC for .NET 不支援在 .NET Framework 和 Xamarin 上執行。</span><span class="sxs-lookup"><span data-stu-id="f6f75-136">gRPC for .NET doesn't support running on .NET Framework and Xamarin.</span></span> <span data-ttu-id="f6f75-137">[GRPC c # core-程式庫](https://grpc.io/docs/languages/csharp/quickstart/) 是支援 .NET Framework 和 Xamarin 的協力廠商程式庫。</span><span class="sxs-lookup"><span data-stu-id="f6f75-137">[gRPC C# core-library](https://grpc.io/docs/languages/csharp/quickstart/) is a third party library that supports .NET Framework and Xamarin.</span></span> <span data-ttu-id="f6f75-138">Microsoft 不支援 gRPC C-核心。</span><span class="sxs-lookup"><span data-stu-id="f6f75-138">gRPC C-core is not supported by Microsoft.</span></span>

## <a name="azure-services"></a><span data-ttu-id="f6f75-139">Azure 服務</span><span class="sxs-lookup"><span data-stu-id="f6f75-139">Azure services</span></span>

> [!div class="checklist"]
>
> * [<span data-ttu-id="f6f75-140">Azure Kubernetes Service (AKS)</span><span class="sxs-lookup"><span data-stu-id="f6f75-140">Azure Kubernetes Service (AKS)</span></span>](https://azure.microsoft.com/services/kubernetes-service/)
> * <span data-ttu-id="f6f75-141">[Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;</span><span class="sxs-lookup"><span data-stu-id="f6f75-141">[Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;</span></span>

<span data-ttu-id="f6f75-142">&dagger;Azure App Service 不支援透過 HTTP/2 裝載 gRPC。</span><span class="sxs-lookup"><span data-stu-id="f6f75-142">&dagger;Azure App Service doesn't support hosting gRPC over HTTP/2.</span></span> <span data-ttu-id="f6f75-143">gRPC-Web 是相容的替代方案。</span><span class="sxs-lookup"><span data-stu-id="f6f75-143">gRPC-Web is a compatible alternative.</span></span>

<span data-ttu-id="f6f75-144">工作正在進行中，以 Azure App Service 中的 HTTP/2 改善 gRPC 支援。</span><span class="sxs-lookup"><span data-stu-id="f6f75-144">Work is in-progress to improve support for gRPC with HTTP/2 in Azure App Service.</span></span> <span data-ttu-id="f6f75-145">如需詳細資訊，請參閱 [此 GitHub 問題](https://github.com/dotnet/AspNetCore/issues/9020)。</span><span class="sxs-lookup"><span data-stu-id="f6f75-145">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/9020).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f6f75-146">其他資源</span><span class="sxs-lookup"><span data-stu-id="f6f75-146">Additional resources</span></span>

* [<span data-ttu-id="f6f75-147">gRPC c # 核心-程式庫</span><span class="sxs-lookup"><span data-stu-id="f6f75-147">gRPC C# core-library</span></span>](https://grpc.io/docs/languages/csharp/quickstart/)
