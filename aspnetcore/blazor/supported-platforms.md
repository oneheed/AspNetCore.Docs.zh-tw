---
title: ASP.NET Core Blazor 支援的平臺
author: guardrex
description: 瞭解 ASP.NET Core 支援的平臺 Blazor 。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
ms.openlocfilehash: 692ab63bb48dbfa29021d59cdf035e9549d3039c
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625943"
---
# <a name="aspnet-core-no-locblazor-supported-platforms"></a><span data-ttu-id="48ce9-103">ASP.NET Core Blazor 支援的平臺</span><span class="sxs-lookup"><span data-stu-id="48ce9-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="48ce9-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="48ce9-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="browser-requirements"></a><span data-ttu-id="48ce9-105">瀏覽器需求</span><span class="sxs-lookup"><span data-stu-id="48ce9-105">Browser requirements</span></span>

### Blazor WebAssembly

| <span data-ttu-id="48ce9-106">瀏覽器</span><span class="sxs-lookup"><span data-stu-id="48ce9-106">Browser</span></span>                          | <span data-ttu-id="48ce9-107">版本</span><span class="sxs-lookup"><span data-stu-id="48ce9-107">Version</span></span>               |
| -------------------------------- | :-------------------: |
| <span data-ttu-id="48ce9-108">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="48ce9-108">Microsoft Edge</span></span>                   | <span data-ttu-id="48ce9-109">目前</span><span class="sxs-lookup"><span data-stu-id="48ce9-109">Current</span></span>               |
| <span data-ttu-id="48ce9-110">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="48ce9-110">Mozilla Firefox</span></span>                  | <span data-ttu-id="48ce9-111">目前</span><span class="sxs-lookup"><span data-stu-id="48ce9-111">Current</span></span>               |
| <span data-ttu-id="48ce9-112">Google Chrome （包括 Android）</span><span class="sxs-lookup"><span data-stu-id="48ce9-112">Google Chrome, including Android</span></span> | <span data-ttu-id="48ce9-113">目前</span><span class="sxs-lookup"><span data-stu-id="48ce9-113">Current</span></span>               |
| <span data-ttu-id="48ce9-114">Safari，包括 iOS</span><span class="sxs-lookup"><span data-stu-id="48ce9-114">Safari, including iOS</span></span>            | <span data-ttu-id="48ce9-115">目前</span><span class="sxs-lookup"><span data-stu-id="48ce9-115">Current</span></span>               |
| <span data-ttu-id="48ce9-116">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="48ce9-116">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="48ce9-117">不支援&dagger;</span><span class="sxs-lookup"><span data-stu-id="48ce9-117">Not Supported&dagger;</span></span> |

<span data-ttu-id="48ce9-118">&dagger;Microsoft Internet Explorer 不支援 [WebAssembly](https://webassembly.org)。</span><span class="sxs-lookup"><span data-stu-id="48ce9-118">&dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

### Blazor Server

| <span data-ttu-id="48ce9-119">瀏覽器</span><span class="sxs-lookup"><span data-stu-id="48ce9-119">Browser</span></span>                          | <span data-ttu-id="48ce9-120">版本</span><span class="sxs-lookup"><span data-stu-id="48ce9-120">Version</span></span>    |
| -------------------------------- | :--------: |
| <span data-ttu-id="48ce9-121">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="48ce9-121">Microsoft Edge</span></span>                   | <span data-ttu-id="48ce9-122">目前</span><span class="sxs-lookup"><span data-stu-id="48ce9-122">Current</span></span>    |
| <span data-ttu-id="48ce9-123">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="48ce9-123">Mozilla Firefox</span></span>                  | <span data-ttu-id="48ce9-124">目前</span><span class="sxs-lookup"><span data-stu-id="48ce9-124">Current</span></span>    |
| <span data-ttu-id="48ce9-125">Google Chrome （包括 Android）</span><span class="sxs-lookup"><span data-stu-id="48ce9-125">Google Chrome, including Android</span></span> | <span data-ttu-id="48ce9-126">目前</span><span class="sxs-lookup"><span data-stu-id="48ce9-126">Current</span></span>    |
| <span data-ttu-id="48ce9-127">Safari，包括 iOS</span><span class="sxs-lookup"><span data-stu-id="48ce9-127">Safari, including iOS</span></span>            | <span data-ttu-id="48ce9-128">目前</span><span class="sxs-lookup"><span data-stu-id="48ce9-128">Current</span></span>    |
| <span data-ttu-id="48ce9-129">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="48ce9-129">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="48ce9-130">rj-11&dagger;</span><span class="sxs-lookup"><span data-stu-id="48ce9-130">11&dagger;</span></span> |

<span data-ttu-id="48ce9-131">&dagger; (需要額外的 polyfill，例如，您可以透過組合) 加入承諾 [`Polyfill.io`](https://polyfill.io/v3/) 。</span><span class="sxs-lookup"><span data-stu-id="48ce9-131">&dagger;Additional polyfills are required (for example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="48ce9-132">其他資源</span><span class="sxs-lookup"><span data-stu-id="48ce9-132">Additional resources</span></span>

* <xref:blazor/hosting-models>
