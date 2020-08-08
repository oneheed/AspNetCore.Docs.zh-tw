---
title: ASP.NET Core Blazor 支援的平臺
author: guardrex
description: 瞭解 ASP.NET Core 支援的平臺 Blazor 。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
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
ms.openlocfilehash: 67896bcdd5d99ff2c9cf22e3902262f9513eb4f6
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/08/2020
ms.locfileid: "88013524"
---
# <a name="aspnet-core-no-locblazor-supported-platforms"></a><span data-ttu-id="505a0-103">ASP.NET Core Blazor 支援的平臺</span><span class="sxs-lookup"><span data-stu-id="505a0-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="505a0-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="505a0-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="browser-requirements"></a><span data-ttu-id="505a0-105">瀏覽器需求</span><span class="sxs-lookup"><span data-stu-id="505a0-105">Browser requirements</span></span>

### Blazor WebAssembly

| <span data-ttu-id="505a0-106">瀏覽器</span><span class="sxs-lookup"><span data-stu-id="505a0-106">Browser</span></span>                          | <span data-ttu-id="505a0-107">版本</span><span class="sxs-lookup"><span data-stu-id="505a0-107">Version</span></span>               |
| -------------------------------- | :-------------------: |
| <span data-ttu-id="505a0-108">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="505a0-108">Microsoft Edge</span></span>                   | <span data-ttu-id="505a0-109">目前</span><span class="sxs-lookup"><span data-stu-id="505a0-109">Current</span></span>               |
| <span data-ttu-id="505a0-110">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="505a0-110">Mozilla Firefox</span></span>                  | <span data-ttu-id="505a0-111">目前</span><span class="sxs-lookup"><span data-stu-id="505a0-111">Current</span></span>               |
| <span data-ttu-id="505a0-112">Google Chrome，包括 Android</span><span class="sxs-lookup"><span data-stu-id="505a0-112">Google Chrome, including Android</span></span> | <span data-ttu-id="505a0-113">目前</span><span class="sxs-lookup"><span data-stu-id="505a0-113">Current</span></span>               |
| <span data-ttu-id="505a0-114">Safari，包括 iOS</span><span class="sxs-lookup"><span data-stu-id="505a0-114">Safari, including iOS</span></span>            | <span data-ttu-id="505a0-115">目前</span><span class="sxs-lookup"><span data-stu-id="505a0-115">Current</span></span>               |
| <span data-ttu-id="505a0-116">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="505a0-116">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="505a0-117">不受支援&dagger;</span><span class="sxs-lookup"><span data-stu-id="505a0-117">Not Supported&dagger;</span></span> |

<span data-ttu-id="505a0-118">&dagger;Microsoft Internet Explorer 不支援[WebAssembly](https://webassembly.org)。</span><span class="sxs-lookup"><span data-stu-id="505a0-118">&dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

### Blazor Server

| <span data-ttu-id="505a0-119">瀏覽器</span><span class="sxs-lookup"><span data-stu-id="505a0-119">Browser</span></span>                          | <span data-ttu-id="505a0-120">版本</span><span class="sxs-lookup"><span data-stu-id="505a0-120">Version</span></span>    |
| -------------------------------- | :--------: |
| <span data-ttu-id="505a0-121">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="505a0-121">Microsoft Edge</span></span>                   | <span data-ttu-id="505a0-122">目前</span><span class="sxs-lookup"><span data-stu-id="505a0-122">Current</span></span>    |
| <span data-ttu-id="505a0-123">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="505a0-123">Mozilla Firefox</span></span>                  | <span data-ttu-id="505a0-124">目前</span><span class="sxs-lookup"><span data-stu-id="505a0-124">Current</span></span>    |
| <span data-ttu-id="505a0-125">Google Chrome，包括 Android</span><span class="sxs-lookup"><span data-stu-id="505a0-125">Google Chrome, including Android</span></span> | <span data-ttu-id="505a0-126">目前</span><span class="sxs-lookup"><span data-stu-id="505a0-126">Current</span></span>    |
| <span data-ttu-id="505a0-127">Safari，包括 iOS</span><span class="sxs-lookup"><span data-stu-id="505a0-127">Safari, including iOS</span></span>            | <span data-ttu-id="505a0-128">目前</span><span class="sxs-lookup"><span data-stu-id="505a0-128">Current</span></span>    |
| <span data-ttu-id="505a0-129">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="505a0-129">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="505a0-130">英寸&dagger;</span><span class="sxs-lookup"><span data-stu-id="505a0-130">11&dagger;</span></span> |

<span data-ttu-id="505a0-131">&dagger; (需要額外的 polyfills，例如，可透過配套) 新增承諾 [`Polyfill.io`](https://polyfill.io/v3/) 。</span><span class="sxs-lookup"><span data-stu-id="505a0-131">&dagger;Additional polyfills are required (for example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="505a0-132">其他資源</span><span class="sxs-lookup"><span data-stu-id="505a0-132">Additional resources</span></span>

* <xref:blazor/hosting-models>
