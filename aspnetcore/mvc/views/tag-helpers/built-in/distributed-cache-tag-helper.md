---
title: ASP.NET Core 中的分散式快取標籤協助程式
author: pkellner
description: 了解如何使用分散式快取標籤協助程式。
ms.author: riande
ms.custom: mvc
ms.date: 01/24/2020
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
uid: mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper
ms.openlocfilehash: a7cc45e1bcc0d0d2bdd09c4ba1f0ec891e4accef
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018672"
---
# <a name="distributed-cache-tag-helper-in-aspnet-core"></a><span data-ttu-id="5c9c1-103">ASP.NET Core 中的分散式快取標籤協助程式</span><span class="sxs-lookup"><span data-stu-id="5c9c1-103">Distributed Cache Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="5c9c1-104">由 [Peter Kellner](https://peterkellner.net) 提供</span><span class="sxs-lookup"><span data-stu-id="5c9c1-104">By [Peter Kellner](https://peterkellner.net)</span></span>

<span data-ttu-id="5c9c1-105">分散式快取標籤協助程式可將 ASP.NET Core 應用程式內容快取至分散式快取來源，因此大幅提升應用程式的效能。</span><span class="sxs-lookup"><span data-stu-id="5c9c1-105">The Distributed Cache Tag Helper provides the ability to dramatically improve the performance of your ASP.NET Core app by caching its content to a distributed cache source.</span></span>

<span data-ttu-id="5c9c1-106">如需標籤協助程式的概觀，請參閱 <xref:mvc/views/tag-helpers/intro>。</span><span class="sxs-lookup"><span data-stu-id="5c9c1-106">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="5c9c1-107">分散式快取標籤協助程式繼承自與快取標籤協助程式相同的基底類別。</span><span class="sxs-lookup"><span data-stu-id="5c9c1-107">The Distributed Cache Tag Helper inherits from the same base class as the Cache Tag Helper.</span></span> <span data-ttu-id="5c9c1-108">分散式標籤協助程式可以使用所有[快取標籤協助程式](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)屬性。</span><span class="sxs-lookup"><span data-stu-id="5c9c1-108">All of the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper) attributes are available to the Distributed Tag Helper.</span></span>

<span data-ttu-id="5c9c1-109">分散式快取標籤協助程式會使用[建構函式插入](xref:fundamentals/dependency-injection#constructor-injection-behavior)。</span><span class="sxs-lookup"><span data-stu-id="5c9c1-109">The Distributed Cache Tag Helper uses [constructor injection](xref:fundamentals/dependency-injection#constructor-injection-behavior).</span></span> <span data-ttu-id="5c9c1-110"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 介面會被傳入分散式快取標籤協助程式的建構函式。</span><span class="sxs-lookup"><span data-stu-id="5c9c1-110">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface is passed into the Distributed Cache Tag Helper's constructor.</span></span> <span data-ttu-id="5c9c1-111">如果 `Startup.ConfigureServices` 中未建立 `IDistributedCache` 的具體實作 (*Startup.cs*)，[分散式快取標籤協助程式](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)將會使用與快取標籤協助程式相同的記憶體內部提供者來儲存快取資料。</span><span class="sxs-lookup"><span data-stu-id="5c9c1-111">If no concrete implementation of `IDistributedCache` is created in `Startup.ConfigureServices` (*Startup.cs*), the Distributed Cache Tag Helper uses the same in-memory provider for storing cached data as the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span></span>

## <a name="distributed-cache-tag-helper-attributes"></a><span data-ttu-id="5c9c1-112">分散式快取標籤協助程式屬性</span><span class="sxs-lookup"><span data-stu-id="5c9c1-112">Distributed Cache Tag Helper Attributes</span></span>

### <a name="attributes-shared-with-the-cache-tag-helper"></a><span data-ttu-id="5c9c1-113">與快取標籤協助程式共用的屬性</span><span class="sxs-lookup"><span data-stu-id="5c9c1-113">Attributes shared with the Cache Tag Helper</span></span>

* `enabled`
* `expires-on`
* `expires-after`
* `expires-sliding`
* `vary-by-header`
* `vary-by-query`
* `vary-by-route`
* `vary-by-cookie`
* `vary-by-user`
* `vary-by priority`

<span data-ttu-id="5c9c1-114">分散式快取標籤協助程式繼承自與快取標籤協助程式相同的類別。</span><span class="sxs-lookup"><span data-stu-id="5c9c1-114">The Distributed Cache Tag Helper inherits from the same class as Cache Tag Helper.</span></span> <span data-ttu-id="5c9c1-115">如需這些屬性的描述，請參閱[快取標籤協助程式](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="5c9c1-115">For descriptions of these attributes, see the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span></span>

### <a name="name"></a><span data-ttu-id="5c9c1-116">NAME</span><span class="sxs-lookup"><span data-stu-id="5c9c1-116">name</span></span>

| <span data-ttu-id="5c9c1-117">屬性類型</span><span class="sxs-lookup"><span data-stu-id="5c9c1-117">Attribute Type</span></span> | <span data-ttu-id="5c9c1-118">範例</span><span class="sxs-lookup"><span data-stu-id="5c9c1-118">Example</span></span>                               |
| -------------- | ------------------------------------- |
| <span data-ttu-id="5c9c1-119">String</span><span class="sxs-lookup"><span data-stu-id="5c9c1-119">String</span></span>         | `my-distributed-cache-unique-key-101` |

<span data-ttu-id="5c9c1-120">`name` 是必要的。</span><span class="sxs-lookup"><span data-stu-id="5c9c1-120">`name` is required.</span></span> <span data-ttu-id="5c9c1-121">`name` 屬性會作為每個預存快取執行個體的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="5c9c1-121">The `name` attribute is used as a key for each stored cache instance.</span></span> <span data-ttu-id="5c9c1-122">不同于快取標籤協助程式會根據頁面 Razor 名稱和分頁中的位置，將快取索引鍵指派給每個實例，分散式快取標籤協助程式只會以屬性的索引鍵為基礎 Razor `name` 。</span><span class="sxs-lookup"><span data-stu-id="5c9c1-122">Unlike the Cache Tag Helper that assigns a cache key to each instance based on the Razor page name and location in the Razor page, the Distributed Cache Tag Helper only bases its key on the attribute `name`.</span></span>

<span data-ttu-id="5c9c1-123">範例：</span><span class="sxs-lookup"><span data-stu-id="5c9c1-123">Example:</span></span>

```cshtml
<distributed-cache name="my-distributed-cache-unique-key-101">
    Time Inside Cache Tag Helper: @DateTime.Now
</distributed-cache>
```

## <a name="distributed-cache-tag-helper-idistributedcache-implementations"></a><span data-ttu-id="5c9c1-124">分散式快取標記協助程式 IDistributedCache 實作</span><span class="sxs-lookup"><span data-stu-id="5c9c1-124">Distributed Cache Tag Helper IDistributedCache implementations</span></span>

<span data-ttu-id="5c9c1-125">ASP.NET Core 內建兩項 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 實作。</span><span class="sxs-lookup"><span data-stu-id="5c9c1-125">There are two implementations of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> built in to ASP.NET Core.</span></span> <span data-ttu-id="5c9c1-126">其中一個是以 SQL Server 為基礎，另一項是以 Redis 為基礎。</span><span class="sxs-lookup"><span data-stu-id="5c9c1-126">One is based on SQL Server, and the other is based on Redis.</span></span> <span data-ttu-id="5c9c1-127">協力廠商的執行也可供使用，例如[NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html)。</span><span class="sxs-lookup"><span data-stu-id="5c9c1-127">Third-party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html).</span></span> <span data-ttu-id="5c9c1-128">如需這些實作的詳細資料，請參閱<xref:performance/caching/distributed>。</span><span class="sxs-lookup"><span data-stu-id="5c9c1-128">Details of these implementations can be found at <xref:performance/caching/distributed>.</span></span> <span data-ttu-id="5c9c1-129">這兩個實作都涉及在 `Startup` 的 `IDistributedCache` 中設定執行個體。</span><span class="sxs-lookup"><span data-stu-id="5c9c1-129">Both implementations involve setting an instance of `IDistributedCache` in `Startup`.</span></span>

<span data-ttu-id="5c9c1-130">沒有特別與使用 `IDistributedCache` 的任何特定實作建立關聯的標籤屬性。</span><span class="sxs-lookup"><span data-stu-id="5c9c1-130">There are no tag attributes specifically associated with using any specific implementation of `IDistributedCache`.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5c9c1-131">其他資源</span><span class="sxs-lookup"><span data-stu-id="5c9c1-131">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:fundamentals/dependency-injection>
* <xref:performance/caching/distributed>
* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
