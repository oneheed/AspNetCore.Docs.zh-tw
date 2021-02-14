---
title: ASP.NET Core Blazor 支援的平臺
author: guardrex
description: 瞭解 ASP.NET Core 支援的平臺 Blazor 。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/01/2020
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
uid: blazor/supported-platforms
ms.openlocfilehash: 948c3e3f66da4727731b37491ae5c5470cfb36fe
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280715"
---
# <a name="aspnet-core-blazor-supported-platforms"></a><span data-ttu-id="6e519-103">ASP.NET Core Blazor 支援的平臺</span><span class="sxs-lookup"><span data-stu-id="6e519-103">ASP.NET Core Blazor supported platforms</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="6e519-104">Blazor WebAssemblyBlazor Server下表所示的瀏覽器支援和。</span><span class="sxs-lookup"><span data-stu-id="6e519-104">Blazor WebAssembly and Blazor Server are supported in the browsers shown in the following table.</span></span>

| <span data-ttu-id="6e519-105">瀏覽器</span><span class="sxs-lookup"><span data-stu-id="6e519-105">Browser</span></span>                          | <span data-ttu-id="6e519-106">版本</span><span class="sxs-lookup"><span data-stu-id="6e519-106">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="6e519-107">Apple Safari，包括 iOS</span><span class="sxs-lookup"><span data-stu-id="6e519-107">Apple Safari, including iOS</span></span>      | <span data-ttu-id="6e519-108">當前&dagger;</span><span class="sxs-lookup"><span data-stu-id="6e519-108">Current&dagger;</span></span> |
| <span data-ttu-id="6e519-109">Google Chrome （包括 Android）</span><span class="sxs-lookup"><span data-stu-id="6e519-109">Google Chrome, including Android</span></span> | <span data-ttu-id="6e519-110">當前&dagger;</span><span class="sxs-lookup"><span data-stu-id="6e519-110">Current&dagger;</span></span> |
| <span data-ttu-id="6e519-111">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="6e519-111">Microsoft Edge</span></span>                   | <span data-ttu-id="6e519-112">當前&dagger;</span><span class="sxs-lookup"><span data-stu-id="6e519-112">Current&dagger;</span></span> |
| <span data-ttu-id="6e519-113">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="6e519-113">Mozilla Firefox</span></span>                  | <span data-ttu-id="6e519-114">當前&dagger;</span><span class="sxs-lookup"><span data-stu-id="6e519-114">Current&dagger;</span></span> |  

<span data-ttu-id="6e519-115">&dagger;*Current* 指的是最新版的瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="6e519-115">&dagger;*Current* refers to the latest version of the browser.</span></span>  

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## Blazor WebAssembly

| <span data-ttu-id="6e519-116">瀏覽器</span><span class="sxs-lookup"><span data-stu-id="6e519-116">Browser</span></span>                          | <span data-ttu-id="6e519-117">版本</span><span class="sxs-lookup"><span data-stu-id="6e519-117">Version</span></span>               |
| -------------------------------- | --------------------- |
| <span data-ttu-id="6e519-118">Apple Safari，包括 iOS</span><span class="sxs-lookup"><span data-stu-id="6e519-118">Apple Safari, including iOS</span></span>      | <span data-ttu-id="6e519-119">當前&dagger;</span><span class="sxs-lookup"><span data-stu-id="6e519-119">Current&dagger;</span></span>       |
| <span data-ttu-id="6e519-120">Google Chrome （包括 Android）</span><span class="sxs-lookup"><span data-stu-id="6e519-120">Google Chrome, including Android</span></span> | <span data-ttu-id="6e519-121">當前&dagger;</span><span class="sxs-lookup"><span data-stu-id="6e519-121">Current&dagger;</span></span>       |
| <span data-ttu-id="6e519-122">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="6e519-122">Microsoft Edge</span></span>                   | <span data-ttu-id="6e519-123">當前&dagger;</span><span class="sxs-lookup"><span data-stu-id="6e519-123">Current&dagger;</span></span>       |
| <span data-ttu-id="6e519-124">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="6e519-124">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="6e519-125">不支援&Dagger;</span><span class="sxs-lookup"><span data-stu-id="6e519-125">Not Supported&Dagger;</span></span> |
| <span data-ttu-id="6e519-126">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="6e519-126">Mozilla Firefox</span></span>                  | <span data-ttu-id="6e519-127">當前&dagger;</span><span class="sxs-lookup"><span data-stu-id="6e519-127">Current&dagger;</span></span>       |  

<span data-ttu-id="6e519-128">&dagger;*Current* 指的是最新版的瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="6e519-128">&dagger;*Current* refers to the latest version of the browser.</span></span>  
<span data-ttu-id="6e519-129">&Dagger;Microsoft Internet Explorer 不支援 [WebAssembly](https://webassembly.org)。</span><span class="sxs-lookup"><span data-stu-id="6e519-129">&Dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

## Blazor Server

| <span data-ttu-id="6e519-130">瀏覽器</span><span class="sxs-lookup"><span data-stu-id="6e519-130">Browser</span></span>                          | <span data-ttu-id="6e519-131">版本</span><span class="sxs-lookup"><span data-stu-id="6e519-131">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="6e519-132">Apple Safari，包括 iOS</span><span class="sxs-lookup"><span data-stu-id="6e519-132">Apple Safari, including iOS</span></span>      | <span data-ttu-id="6e519-133">當前&dagger;</span><span class="sxs-lookup"><span data-stu-id="6e519-133">Current&dagger;</span></span> |
| <span data-ttu-id="6e519-134">Google Chrome （包括 Android）</span><span class="sxs-lookup"><span data-stu-id="6e519-134">Google Chrome, including Android</span></span> | <span data-ttu-id="6e519-135">當前&dagger;</span><span class="sxs-lookup"><span data-stu-id="6e519-135">Current&dagger;</span></span> |
| <span data-ttu-id="6e519-136">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="6e519-136">Microsoft Edge</span></span>                   | <span data-ttu-id="6e519-137">當前&dagger;</span><span class="sxs-lookup"><span data-stu-id="6e519-137">Current&dagger;</span></span> |
| <span data-ttu-id="6e519-138">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="6e519-138">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="6e519-139">rj-11&Dagger;</span><span class="sxs-lookup"><span data-stu-id="6e519-139">11&Dagger;</span></span>      |
| <span data-ttu-id="6e519-140">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="6e519-140">Mozilla Firefox</span></span>                  | <span data-ttu-id="6e519-141">當前&dagger;</span><span class="sxs-lookup"><span data-stu-id="6e519-141">Current&dagger;</span></span> |

<span data-ttu-id="6e519-142">&dagger;*Current* 指的是最新版的瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="6e519-142">&dagger;*Current* refers to the latest version of the browser.</span></span>  
<span data-ttu-id="6e519-143">&Dagger;需要其他 polyfill。</span><span class="sxs-lookup"><span data-stu-id="6e519-143">&Dagger;Additional polyfills are required.</span></span> <span data-ttu-id="6e519-144">例如，您可以透過組合來加入承諾 [`Polyfill.io`](https://polyfill.io/v3/) 。</span><span class="sxs-lookup"><span data-stu-id="6e519-144">For example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="6e519-145">其他資源</span><span class="sxs-lookup"><span data-stu-id="6e519-145">Additional resources</span></span>

* <xref:blazor/hosting-models>
* <xref:signalr/supported-platforms>
