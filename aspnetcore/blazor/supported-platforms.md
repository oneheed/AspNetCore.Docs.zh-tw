---
title: ASP.NET Core Blazor 支援的平臺
author: guardrex
description: 瞭解 ASP.NET Core 支援的平臺 Blazor 。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/01/2020
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
uid: blazor/supported-platforms
ms.openlocfilehash: 1ffe98636ed200adbf00e89c2c3499eb69792d3f
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91754537"
---
# <a name="aspnet-core-no-locblazor-supported-platforms"></a><span data-ttu-id="fc592-103">ASP.NET Core Blazor 支援的平臺</span><span class="sxs-lookup"><span data-stu-id="fc592-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="fc592-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="fc592-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="fc592-105">Blazor WebAssemblyBlazor Server下表所示的瀏覽器支援和。</span><span class="sxs-lookup"><span data-stu-id="fc592-105">Blazor WebAssembly and Blazor Server are supported in the browsers shown in the following table.</span></span>

| <span data-ttu-id="fc592-106">瀏覽器</span><span class="sxs-lookup"><span data-stu-id="fc592-106">Browser</span></span>                          | <span data-ttu-id="fc592-107">版本</span><span class="sxs-lookup"><span data-stu-id="fc592-107">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="fc592-108">Apple Safari，包括 iOS</span><span class="sxs-lookup"><span data-stu-id="fc592-108">Apple Safari, including iOS</span></span>      | <span data-ttu-id="fc592-109">當前&dagger;</span><span class="sxs-lookup"><span data-stu-id="fc592-109">Current&dagger;</span></span> |
| <span data-ttu-id="fc592-110">Google Chrome （包括 Android）</span><span class="sxs-lookup"><span data-stu-id="fc592-110">Google Chrome, including Android</span></span> | <span data-ttu-id="fc592-111">當前&dagger;</span><span class="sxs-lookup"><span data-stu-id="fc592-111">Current&dagger;</span></span> |
| <span data-ttu-id="fc592-112">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="fc592-112">Microsoft Edge</span></span>                   | <span data-ttu-id="fc592-113">當前&dagger;</span><span class="sxs-lookup"><span data-stu-id="fc592-113">Current&dagger;</span></span> |
| <span data-ttu-id="fc592-114">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="fc592-114">Mozilla Firefox</span></span>                  | <span data-ttu-id="fc592-115">當前&dagger;</span><span class="sxs-lookup"><span data-stu-id="fc592-115">Current&dagger;</span></span> |  

<span data-ttu-id="fc592-116">&dagger;*Current* 指的是最新版的瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="fc592-116">&dagger;*Current* refers to the latest version of the browser.</span></span>  

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## Blazor WebAssembly

| <span data-ttu-id="fc592-117">瀏覽器</span><span class="sxs-lookup"><span data-stu-id="fc592-117">Browser</span></span>                          | <span data-ttu-id="fc592-118">版本</span><span class="sxs-lookup"><span data-stu-id="fc592-118">Version</span></span>               |
| -------------------------------- | --------------------- |
| <span data-ttu-id="fc592-119">Apple Safari，包括 iOS</span><span class="sxs-lookup"><span data-stu-id="fc592-119">Apple Safari, including iOS</span></span>      | <span data-ttu-id="fc592-120">當前&dagger;</span><span class="sxs-lookup"><span data-stu-id="fc592-120">Current&dagger;</span></span>       |
| <span data-ttu-id="fc592-121">Google Chrome （包括 Android）</span><span class="sxs-lookup"><span data-stu-id="fc592-121">Google Chrome, including Android</span></span> | <span data-ttu-id="fc592-122">當前&dagger;</span><span class="sxs-lookup"><span data-stu-id="fc592-122">Current&dagger;</span></span>       |
| <span data-ttu-id="fc592-123">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="fc592-123">Microsoft Edge</span></span>                   | <span data-ttu-id="fc592-124">當前&dagger;</span><span class="sxs-lookup"><span data-stu-id="fc592-124">Current&dagger;</span></span>       |
| <span data-ttu-id="fc592-125">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="fc592-125">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="fc592-126">不支援&Dagger;</span><span class="sxs-lookup"><span data-stu-id="fc592-126">Not Supported&Dagger;</span></span> |
| <span data-ttu-id="fc592-127">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="fc592-127">Mozilla Firefox</span></span>                  | <span data-ttu-id="fc592-128">當前&dagger;</span><span class="sxs-lookup"><span data-stu-id="fc592-128">Current&dagger;</span></span>       |  

<span data-ttu-id="fc592-129">&dagger;*Current* 指的是最新版的瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="fc592-129">&dagger;*Current* refers to the latest version of the browser.</span></span>  
<span data-ttu-id="fc592-130">&Dagger;Microsoft Internet Explorer 不支援 [WebAssembly](https://webassembly.org)。</span><span class="sxs-lookup"><span data-stu-id="fc592-130">&Dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

## Blazor Server

| <span data-ttu-id="fc592-131">瀏覽器</span><span class="sxs-lookup"><span data-stu-id="fc592-131">Browser</span></span>                          | <span data-ttu-id="fc592-132">版本</span><span class="sxs-lookup"><span data-stu-id="fc592-132">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="fc592-133">Apple Safari，包括 iOS</span><span class="sxs-lookup"><span data-stu-id="fc592-133">Apple Safari, including iOS</span></span>      | <span data-ttu-id="fc592-134">當前&dagger;</span><span class="sxs-lookup"><span data-stu-id="fc592-134">Current&dagger;</span></span> |
| <span data-ttu-id="fc592-135">Google Chrome （包括 Android）</span><span class="sxs-lookup"><span data-stu-id="fc592-135">Google Chrome, including Android</span></span> | <span data-ttu-id="fc592-136">當前&dagger;</span><span class="sxs-lookup"><span data-stu-id="fc592-136">Current&dagger;</span></span> |
| <span data-ttu-id="fc592-137">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="fc592-137">Microsoft Edge</span></span>                   | <span data-ttu-id="fc592-138">當前&dagger;</span><span class="sxs-lookup"><span data-stu-id="fc592-138">Current&dagger;</span></span> |
| <span data-ttu-id="fc592-139">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="fc592-139">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="fc592-140">rj-11&Dagger;</span><span class="sxs-lookup"><span data-stu-id="fc592-140">11&Dagger;</span></span>      |
| <span data-ttu-id="fc592-141">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="fc592-141">Mozilla Firefox</span></span>                  | <span data-ttu-id="fc592-142">當前&dagger;</span><span class="sxs-lookup"><span data-stu-id="fc592-142">Current&dagger;</span></span> |

<span data-ttu-id="fc592-143">&dagger;*Current* 指的是最新版的瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="fc592-143">&dagger;*Current* refers to the latest version of the browser.</span></span>  
<span data-ttu-id="fc592-144">&Dagger;需要其他 polyfill。</span><span class="sxs-lookup"><span data-stu-id="fc592-144">&Dagger;Additional polyfills are required.</span></span> <span data-ttu-id="fc592-145">例如，您可以透過組合來加入承諾 [`Polyfill.io`](https://polyfill.io/v3/) 。</span><span class="sxs-lookup"><span data-stu-id="fc592-145">For example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="fc592-146">其他資源</span><span class="sxs-lookup"><span data-stu-id="fc592-146">Additional resources</span></span>

* <xref:blazor/hosting-models>
* <xref:signalr/supported-platforms>
