---
title: 在 ASP.NET Core 中使用 ObjectPool 的物件重複使用
author: rick-anderson
description: 使用 ObjectPool 在 ASP.NET Core 應用程式中提高效能的秘訣。
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
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
uid: performance/ObjectPool
ms.openlocfilehash: 6997dbfdd5c654e4a8b15a026fd3ec61d024f02d
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632365"
---
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a><span data-ttu-id="77a8b-103">在 ASP.NET Core 中使用 ObjectPool 的物件重複使用</span><span class="sxs-lookup"><span data-stu-id="77a8b-103">Object reuse with ObjectPool in ASP.NET Core</span></span>

<span data-ttu-id="77a8b-104">由 [Steve Gordon](https://twitter.com/stevejgordon)、 [Ryan Nowak](https://github.com/rynowak)和 [Günther Foidl](https://github.com/gfoidl)</span><span class="sxs-lookup"><span data-stu-id="77a8b-104">By [Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak), and [Günther Foidl](https://github.com/gfoidl)</span></span>

<span data-ttu-id="77a8b-105"><xref:Microsoft.Extensions.ObjectPool> 是 ASP.NET Core 基礎結構的一部分，可支援將一組物件保留在記憶體中以供重複使用，而不是允許物件進行垃圾收集。</span><span class="sxs-lookup"><span data-stu-id="77a8b-105"><xref:Microsoft.Extensions.ObjectPool> is part of the ASP.NET Core infrastructure that supports keeping a group of objects in memory for reuse rather than allowing the objects to be garbage collected.</span></span>

<span data-ttu-id="77a8b-106">如果正在管理的物件為，您可能會想要使用物件集區：</span><span class="sxs-lookup"><span data-stu-id="77a8b-106">You might want to use the object pool if the objects that are being managed are:</span></span>

- <span data-ttu-id="77a8b-107">配置/初始化耗費資源。</span><span class="sxs-lookup"><span data-stu-id="77a8b-107">Expensive to allocate/initialize.</span></span>
- <span data-ttu-id="77a8b-108">代表某些有限的資源。</span><span class="sxs-lookup"><span data-stu-id="77a8b-108">Represent some limited resource.</span></span>
- <span data-ttu-id="77a8b-109">使用可預測且經常使用。</span><span class="sxs-lookup"><span data-stu-id="77a8b-109">Used predictably and frequently.</span></span>

<span data-ttu-id="77a8b-110">例如，ASP.NET Core framework 會在某些地方使用物件集區來重複使用 <xref:System.Text.StringBuilder> 實例。</span><span class="sxs-lookup"><span data-stu-id="77a8b-110">For example, the ASP.NET Core framework uses the object pool in some places to reuse <xref:System.Text.StringBuilder> instances.</span></span> <span data-ttu-id="77a8b-111">`StringBuilder` 配置並管理它自己的緩衝區以保存字元資料。</span><span class="sxs-lookup"><span data-stu-id="77a8b-111">`StringBuilder` allocates and manages its own buffers to hold character data.</span></span> <span data-ttu-id="77a8b-112">ASP.NET Core 定期使用 `StringBuilder` 來執行功能，並重複使用它們可提供效能優勢。</span><span class="sxs-lookup"><span data-stu-id="77a8b-112">ASP.NET Core regularly uses `StringBuilder` to implement features, and reusing them provides a performance benefit.</span></span>

<span data-ttu-id="77a8b-113">物件共用不一定能改善效能：</span><span class="sxs-lookup"><span data-stu-id="77a8b-113">Object pooling doesn't always improve performance:</span></span>

- <span data-ttu-id="77a8b-114">除非物件的初始化成本很高，否則從集區取得物件通常會較慢。</span><span class="sxs-lookup"><span data-stu-id="77a8b-114">Unless the initialization cost of an object is high, it's usually slower to get the object from the pool.</span></span>
- <span data-ttu-id="77a8b-115">在解除配置集區之前，不會解除配置集區所管理的物件。</span><span class="sxs-lookup"><span data-stu-id="77a8b-115">Objects managed by the pool aren't de-allocated until the pool is de-allocated.</span></span>

<span data-ttu-id="77a8b-116">在使用應用程式或程式庫的真實案例收集效能資料之後，才使用物件共用。</span><span class="sxs-lookup"><span data-stu-id="77a8b-116">Use object pooling only after collecting performance data using realistic scenarios for your app or library.</span></span>

::: moniker range="< aspnetcore-3.0"
<span data-ttu-id="77a8b-117">**警告： `ObjectPool` 不會執行 `IDisposable` 。我們不建議您將它與需要處置的類型搭配使用。**</span><span class="sxs-lookup"><span data-stu-id="77a8b-117">**WARNING: The `ObjectPool` doesn't implement `IDisposable`. We don't recommend using it with types that need disposal.**</span></span> <span data-ttu-id="77a8b-118">`ObjectPool` 在 ASP.NET Core 3.0 和更新版本中支援 `IDisposable` 。</span><span class="sxs-lookup"><span data-stu-id="77a8b-118">`ObjectPool` in ASP.NET Core 3.0 and later supports `IDisposable`.</span></span>
::: moniker-end

<span data-ttu-id="77a8b-119">**注意： ObjectPool 不會限制要配置的物件數目，而是會限制它將保留的物件數目。**</span><span class="sxs-lookup"><span data-stu-id="77a8b-119">**NOTE: The ObjectPool doesn't place a limit on the number of objects that it will allocate, it places a limit on the number of objects it will retain.**</span></span>

## <a name="concepts"></a><span data-ttu-id="77a8b-120">概念</span><span class="sxs-lookup"><span data-stu-id="77a8b-120">Concepts</span></span>

<span data-ttu-id="77a8b-121"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> -基本物件集區抽象概念。</span><span class="sxs-lookup"><span data-stu-id="77a8b-121"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> - the basic object pool abstraction.</span></span> <span data-ttu-id="77a8b-122">用來取得和傳回物件。</span><span class="sxs-lookup"><span data-stu-id="77a8b-122">Used to get and return objects.</span></span>

<span data-ttu-id="77a8b-123"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> -將此值用於自訂物件的建立方式，以及傳回給集區時的 *重設* 方式。</span><span class="sxs-lookup"><span data-stu-id="77a8b-123"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> - implement this to customize how an object is created and how it is *reset* when returned to the pool.</span></span> <span data-ttu-id="77a8b-124">這可以傳遞至您直接建立的物件集區 .。。或</span><span class="sxs-lookup"><span data-stu-id="77a8b-124">This can be passed into an object pool that you construct directly.... OR</span></span>

<span data-ttu-id="77a8b-125"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> 作為建立物件集區的 factory。</span><span class="sxs-lookup"><span data-stu-id="77a8b-125"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> acts as a factory for creating object pools.</span></span>
<!-- REview, there is no ObjectPoolProvider<T> -->

<span data-ttu-id="77a8b-126">ObjectPool 可以透過多種方式在應用程式中使用：</span><span class="sxs-lookup"><span data-stu-id="77a8b-126">The ObjectPool can be used in an app in multiple ways:</span></span>

* <span data-ttu-id="77a8b-127">將集區具現化。</span><span class="sxs-lookup"><span data-stu-id="77a8b-127">Instantiating a pool.</span></span>
* <span data-ttu-id="77a8b-128">在相依性 [插入](xref:fundamentals/dependency-injection) (DI) 作為實例來註冊集區。</span><span class="sxs-lookup"><span data-stu-id="77a8b-128">Registering a pool in [Dependency injection](xref:fundamentals/dependency-injection) (DI) as an instance.</span></span>
* <span data-ttu-id="77a8b-129">`ObjectPoolProvider<>`在 DI 中註冊，並使用它作為 factory。</span><span class="sxs-lookup"><span data-stu-id="77a8b-129">Registering the `ObjectPoolProvider<>` in DI and using it as a factory.</span></span>

## <a name="how-to-use-objectpool"></a><span data-ttu-id="77a8b-130">如何使用 ObjectPool</span><span class="sxs-lookup"><span data-stu-id="77a8b-130">How to use ObjectPool</span></span>

<span data-ttu-id="77a8b-131">呼叫 <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Get*> 以取得物件，並傳回 <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> 物件。</span><span class="sxs-lookup"><span data-stu-id="77a8b-131">Call <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Get*> to get an object and <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> to return the object.</span></span>  <span data-ttu-id="77a8b-132">您不需要傳回每個物件。</span><span class="sxs-lookup"><span data-stu-id="77a8b-132">There's no requirement that you return every object.</span></span> <span data-ttu-id="77a8b-133">如果您沒有傳回物件，則會進行垃圾收集。</span><span class="sxs-lookup"><span data-stu-id="77a8b-133">If you don't return an object, it will be garbage collected.</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="77a8b-134">當 <xref:Microsoft.Extensions.ObjectPool.DefaultObjectPoolProvider> 使用和 `T` 實行時 `IDisposable` ：</span><span class="sxs-lookup"><span data-stu-id="77a8b-134">When <xref:Microsoft.Extensions.ObjectPool.DefaultObjectPoolProvider> is used and `T` implements `IDisposable`:</span></span>

* <span data-ttu-id="77a8b-135">系統將會處置 ***未*** 傳回集區的專案。</span><span class="sxs-lookup"><span data-stu-id="77a8b-135">Items that are ***not*** returned to the pool will be disposed.</span></span>
* <span data-ttu-id="77a8b-136">當集區由 DI 處置時，集區中的所有專案都會被處置。</span><span class="sxs-lookup"><span data-stu-id="77a8b-136">When the pool gets disposed by DI, all items in the pool are disposed.</span></span>

<span data-ttu-id="77a8b-137">注意：處置集區之後：</span><span class="sxs-lookup"><span data-stu-id="77a8b-137">NOTE: After the pool is disposed:</span></span>

* <span data-ttu-id="77a8b-138">呼叫會擲回 `Get` `ObjectDisposedException` 。</span><span class="sxs-lookup"><span data-stu-id="77a8b-138">Calling `Get` throws a `ObjectDisposedException`.</span></span>
* <span data-ttu-id="77a8b-139">`return` 處置指定的專案。</span><span class="sxs-lookup"><span data-stu-id="77a8b-139">`return` disposes the given item.</span></span>

::: moniker-end

## <a name="objectpool-sample"></a><span data-ttu-id="77a8b-140">ObjectPool 範例</span><span class="sxs-lookup"><span data-stu-id="77a8b-140">ObjectPool sample</span></span>

<span data-ttu-id="77a8b-141">下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="77a8b-141">The following code:</span></span>

* <span data-ttu-id="77a8b-142">將相依性 `ObjectPoolProvider` [插入](xref:fundamentals/dependency-injection) (DI) 容器中。</span><span class="sxs-lookup"><span data-stu-id="77a8b-142">Adds `ObjectPoolProvider` to the [Dependency injection](xref:fundamentals/dependency-injection) (DI) container.</span></span>
* <span data-ttu-id="77a8b-143">將和設定 `ObjectPool<StringBuilder>` 為 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="77a8b-143">Adds and configures `ObjectPool<StringBuilder>` to the DI container.</span></span>
* <span data-ttu-id="77a8b-144">加入 `BirthdayMiddleware` 。</span><span class="sxs-lookup"><span data-stu-id="77a8b-144">Adds the `BirthdayMiddleware`.</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

<span data-ttu-id="77a8b-145">下列程式碼會執行 `BirthdayMiddleware`</span><span class="sxs-lookup"><span data-stu-id="77a8b-145">The following code implements `BirthdayMiddleware`</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]
