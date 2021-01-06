---
title: 具有期限和取消的可靠 gRPC 服務
author: jamesnk
description: 瞭解如何在 .NET 中使用期限和取消來建立可靠的 gRPC 服務。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/07/2020
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
uid: grpc/deadlines-cancellation
ms.openlocfilehash: a735ed4d2ca8db1c9b7998acd14f9be761fe7ec6
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059919"
---
# <a name="reliable-grpc-services-with-deadlines-and-cancellation"></a><span data-ttu-id="4d8f8-103">具有期限和取消的可靠 gRPC 服務</span><span class="sxs-lookup"><span data-stu-id="4d8f8-103">Reliable gRPC services with deadlines and cancellation</span></span>

<span data-ttu-id="4d8f8-104">依 [James 牛頓](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="4d8f8-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="4d8f8-105">期限和取消是 gRPC 用戶端用來中止進行中呼叫的功能。</span><span class="sxs-lookup"><span data-stu-id="4d8f8-105">Deadlines and cancellation are features used by gRPC clients to abort in-progress calls.</span></span> <span data-ttu-id="4d8f8-106">本文討論為何期限和取消很重要，以及如何在 .NET gRPC 應用程式中使用它們。</span><span class="sxs-lookup"><span data-stu-id="4d8f8-106">This article discusses why deadlines and cancellation are important, and how to use them in .NET gRPC apps.</span></span>

## <a name="deadlines"></a><span data-ttu-id="4d8f8-107">最後 期限</span><span class="sxs-lookup"><span data-stu-id="4d8f8-107">Deadlines</span></span>

<span data-ttu-id="4d8f8-108">期限可讓 gRPC 用戶端指定等候完成呼叫所需的時間。</span><span class="sxs-lookup"><span data-stu-id="4d8f8-108">A deadline allows a gRPC client to specify how long it will wait for a call to complete.</span></span> <span data-ttu-id="4d8f8-109">當超過期限時，即會取消通話。</span><span class="sxs-lookup"><span data-stu-id="4d8f8-109">When a deadline is exceeded, the call is canceled.</span></span> <span data-ttu-id="4d8f8-110">設定期限很重要，因為它會提供呼叫可執行檔時間上限。</span><span class="sxs-lookup"><span data-stu-id="4d8f8-110">Setting a deadline is important because it provides an upper limit on how long a call can run for.</span></span> <span data-ttu-id="4d8f8-111">它會停止不正常的服務，使其無法執行永久和耗盡的伺服器資源。</span><span class="sxs-lookup"><span data-stu-id="4d8f8-111">It stops misbehaving services from running forever and exhausting server resources.</span></span> <span data-ttu-id="4d8f8-112">期限是一個實用的工具，可用於建立可靠的應用程式，而且應該進行設定。</span><span class="sxs-lookup"><span data-stu-id="4d8f8-112">Deadlines are a useful tool for building reliable apps and should be configured.</span></span>

<span data-ttu-id="4d8f8-113">期限設定：</span><span class="sxs-lookup"><span data-stu-id="4d8f8-113">Deadline configuration:</span></span>

* <span data-ttu-id="4d8f8-114">當進行呼叫時，會使用設定期限 `CallOptions.Deadline` 。</span><span class="sxs-lookup"><span data-stu-id="4d8f8-114">A deadline is configured using `CallOptions.Deadline` when a call is made.</span></span>
* <span data-ttu-id="4d8f8-115">沒有預設期限值。</span><span class="sxs-lookup"><span data-stu-id="4d8f8-115">There is no default deadline value.</span></span> <span data-ttu-id="4d8f8-116">除非指定期限，否則 gRPC 呼叫不會有時間限制。</span><span class="sxs-lookup"><span data-stu-id="4d8f8-116">gRPC calls aren't time limited unless a deadline is specified.</span></span>
* <span data-ttu-id="4d8f8-117">期限是超過期限的 UTC 時間。</span><span class="sxs-lookup"><span data-stu-id="4d8f8-117">A deadline is the UTC time of when the deadline is exceeded.</span></span> <span data-ttu-id="4d8f8-118">例如，的 `DateTime.UtcNow.AddSeconds(5)` 期限是從現在開始的5秒。</span><span class="sxs-lookup"><span data-stu-id="4d8f8-118">For example, `DateTime.UtcNow.AddSeconds(5)` is a deadline of 5 seconds from now.</span></span>
* <span data-ttu-id="4d8f8-119">如果使用過去或目前的時間，則呼叫會立即超過期限。</span><span class="sxs-lookup"><span data-stu-id="4d8f8-119">If a past or current time is used then the call immediately exceeds the deadline.</span></span>
* <span data-ttu-id="4d8f8-120">期限會透過 gRPC 呼叫傳送至服務，而且用戶端與服務會獨立追蹤此期限。</span><span class="sxs-lookup"><span data-stu-id="4d8f8-120">The deadline is sent with the gRPC call to the service and is independently tracked by both the client and the service.</span></span> <span data-ttu-id="4d8f8-121">GRPC 呼叫可能會在一部電腦上完成，但回應傳回用戶端的時間已超過期限。</span><span class="sxs-lookup"><span data-stu-id="4d8f8-121">It is possible that a gRPC call completes on one machine, but by the time the response has returned to the client the deadline has been exceeded.</span></span>

<span data-ttu-id="4d8f8-122">如果超過期限，用戶端和服務會有不同的行為：</span><span class="sxs-lookup"><span data-stu-id="4d8f8-122">If a deadline is exceeded, the client and service have different behavior:</span></span>

* <span data-ttu-id="4d8f8-123">用戶端會立即中止基礎 HTTP 要求，並擲回 `DeadlineExceeded` 錯誤。</span><span class="sxs-lookup"><span data-stu-id="4d8f8-123">The client immediately aborts the underlying HTTP request and throws a `DeadlineExceeded` error.</span></span> <span data-ttu-id="4d8f8-124">用戶端應用程式可以選擇攔截錯誤，並向使用者顯示超時訊息。</span><span class="sxs-lookup"><span data-stu-id="4d8f8-124">The client app can choose to catch the error and display a timeout message to the user.</span></span>
* <span data-ttu-id="4d8f8-125">在伺服器上，執行中的 HTTP 要求會中止，而且會引發 [ServerCallCoNtext。 CancellationToken](xref:System.Threading.CancellationToken) 。</span><span class="sxs-lookup"><span data-stu-id="4d8f8-125">On the server, the executing HTTP request is aborted and [ServerCallContext.CancellationToken](xref:System.Threading.CancellationToken) is raised.</span></span> <span data-ttu-id="4d8f8-126">雖然 HTTP 要求已中止，gRPC 呼叫仍會繼續在伺服器上執行，直到方法完成為止。</span><span class="sxs-lookup"><span data-stu-id="4d8f8-126">Although the HTTP request is aborted, the gRPC call continues to run on the server until the method completes.</span></span> <span data-ttu-id="4d8f8-127">解除標記必須傳遞給非同步方法，如此一來，它們就會與呼叫一起取消。</span><span class="sxs-lookup"><span data-stu-id="4d8f8-127">It's important that the cancellation token is passed to async methods so they are cancelled along with the call.</span></span> <span data-ttu-id="4d8f8-128">例如，將解除標記傳遞給非同步資料庫查詢和 HTTP 要求。</span><span class="sxs-lookup"><span data-stu-id="4d8f8-128">For example, passing a cancellation token to async database queries and HTTP requests.</span></span> <span data-ttu-id="4d8f8-129">傳遞取消權杖可讓取消的呼叫在伺服器上快速完成，並釋出資源供其他呼叫使用。</span><span class="sxs-lookup"><span data-stu-id="4d8f8-129">Passing a cancellation token allows the canceled call to complete quickly on the server and free up resources for other calls.</span></span>

<span data-ttu-id="4d8f8-130">`CallOptions.Deadline`設定以設定 gRPC 呼叫的期限：</span><span class="sxs-lookup"><span data-stu-id="4d8f8-130">Configure `CallOptions.Deadline` to set a deadline for a gRPC call:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-client.cs?highlight=7,12)]

<span data-ttu-id="4d8f8-131">`ServerCallContext.CancellationToken`在 gRPC 服務中使用：</span><span class="sxs-lookup"><span data-stu-id="4d8f8-131">Using `ServerCallContext.CancellationToken` in a gRPC service:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-server.cs?highlight=5)]

### <a name="propagating-deadlines"></a><span data-ttu-id="4d8f8-132">傳播期限</span><span class="sxs-lookup"><span data-stu-id="4d8f8-132">Propagating deadlines</span></span>

<span data-ttu-id="4d8f8-133">從執行中的 gRPC 服務進行 gRPC 呼叫時，應會傳播期限。</span><span class="sxs-lookup"><span data-stu-id="4d8f8-133">When a gRPC call is made from an executing gRPC service, the deadline should be propagated.</span></span> <span data-ttu-id="4d8f8-134">例如：</span><span class="sxs-lookup"><span data-stu-id="4d8f8-134">For example:</span></span>

1. <span data-ttu-id="4d8f8-135">具有期限的用戶端應用程式呼叫 `FrontendService.GetUser` 。</span><span class="sxs-lookup"><span data-stu-id="4d8f8-135">Client app calls `FrontendService.GetUser` with a deadline.</span></span>
2. <span data-ttu-id="4d8f8-136">`FrontendService` 會呼叫 `UserService.GetUser`。</span><span class="sxs-lookup"><span data-stu-id="4d8f8-136">`FrontendService` calls `UserService.GetUser`.</span></span> <span data-ttu-id="4d8f8-137">用戶端指定的期限應該使用新的 gRPC 呼叫來指定。</span><span class="sxs-lookup"><span data-stu-id="4d8f8-137">The deadline specified by the client should be specified with the new gRPC call.</span></span>
3. <span data-ttu-id="4d8f8-138">`UserService.GetUser` 接收期限。</span><span class="sxs-lookup"><span data-stu-id="4d8f8-138">`UserService.GetUser` receives the deadline.</span></span> <span data-ttu-id="4d8f8-139">如果超過用戶端應用程式的期限，則會正確地發生。</span><span class="sxs-lookup"><span data-stu-id="4d8f8-139">It correctly times-out if the client app's deadline is exceeded.</span></span>

<span data-ttu-id="4d8f8-140">呼叫內容提供期限 `ServerCallContext.Deadline` ：</span><span class="sxs-lookup"><span data-stu-id="4d8f8-140">The call context provides the deadline with `ServerCallContext.Deadline`:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-propagate.cs?highlight=7)]

<span data-ttu-id="4d8f8-141">手動傳播期限可能很麻煩。</span><span class="sxs-lookup"><span data-stu-id="4d8f8-141">Manually propagating deadlines can be cumbersome.</span></span> <span data-ttu-id="4d8f8-142">期限必須傳遞至每次呼叫，且很容易不小心遺漏。</span><span class="sxs-lookup"><span data-stu-id="4d8f8-142">The deadline needs to be passed to every call, and it's easy to accidentally miss.</span></span> <span data-ttu-id="4d8f8-143">GRPC client factory 提供自動解決方案。</span><span class="sxs-lookup"><span data-stu-id="4d8f8-143">An automatic solution is available with gRPC client factory.</span></span> <span data-ttu-id="4d8f8-144">指定 `EnableCallContextPropagation` ：</span><span class="sxs-lookup"><span data-stu-id="4d8f8-144">Specifying `EnableCallContextPropagation`:</span></span>

* <span data-ttu-id="4d8f8-145">自動將期限和取消權杖傳播至子呼叫。</span><span class="sxs-lookup"><span data-stu-id="4d8f8-145">Automatically propagates the deadline and cancellation token to child calls.</span></span>
* <span data-ttu-id="4d8f8-146">是確保複雜的嵌套 gRPC 案例一律會傳播期限和取消的絕佳方法。</span><span class="sxs-lookup"><span data-stu-id="4d8f8-146">Is an excellent way of ensuring that complex, nested gRPC scenarios always propagate the deadline and cancellation.</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/clientfactory-propagate.cs?highlight=6)]

<span data-ttu-id="4d8f8-147">如需詳細資訊，請參閱 <xref:grpc/clientfactory#deadline-and-cancellation-propagation> 。</span><span class="sxs-lookup"><span data-stu-id="4d8f8-147">For more information, see <xref:grpc/clientfactory#deadline-and-cancellation-propagation>.</span></span>

## <a name="cancellation"></a><span data-ttu-id="4d8f8-148">取消</span><span class="sxs-lookup"><span data-stu-id="4d8f8-148">Cancellation</span></span>

<span data-ttu-id="4d8f8-149">取消可讓 gRPC 用戶端取消不再需要的長時間執行呼叫。</span><span class="sxs-lookup"><span data-stu-id="4d8f8-149">Cancellation allows a gRPC client to cancel long running calls that are no longer needed.</span></span> <span data-ttu-id="4d8f8-150">例如，當使用者造訪網站上的頁面時，會啟動串流即時更新的 gRPC 呼叫。</span><span class="sxs-lookup"><span data-stu-id="4d8f8-150">For example, a gRPC call that streams realtime updates is started when the user visits a page on a website.</span></span> <span data-ttu-id="4d8f8-151">當使用者流覽離開頁面時，應取消資料流程。</span><span class="sxs-lookup"><span data-stu-id="4d8f8-151">The stream should be canceled when the user navigates away from the page.</span></span>

<span data-ttu-id="4d8f8-152">GRPC 呼叫可以在用戶端中取消，方法是傳遞具有 [CallOptions](xref:System.Threading.CancellationToken) 的取消權杖，或 `Dispose` 在呼叫上呼叫。</span><span class="sxs-lookup"><span data-stu-id="4d8f8-152">A gRPC call can be canceled in the client by passing a cancellation token with [CallOptions.CancellationToken](xref:System.Threading.CancellationToken) or calling `Dispose` on the call.</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/cancellation-client.cs?highlight=19)]

<span data-ttu-id="4d8f8-153">gRPC 可取消的服務應：</span><span class="sxs-lookup"><span data-stu-id="4d8f8-153">gRPC services that can be cancelled should:</span></span>
* <span data-ttu-id="4d8f8-154">傳遞 `ServerCallContext.CancellationToken` 至非同步方法。</span><span class="sxs-lookup"><span data-stu-id="4d8f8-154">Pass `ServerCallContext.CancellationToken` to async methods.</span></span> <span data-ttu-id="4d8f8-155">取消非同步方法可讓伺服器上的呼叫快速完成。</span><span class="sxs-lookup"><span data-stu-id="4d8f8-155">Canceling async methods allows the call on the server to complete quickly.</span></span>
* <span data-ttu-id="4d8f8-156">將取消權杖傳播至子呼叫。</span><span class="sxs-lookup"><span data-stu-id="4d8f8-156">Propagate the cancellation token to child calls.</span></span> <span data-ttu-id="4d8f8-157">傳播取消權杖可確保子呼叫會以其父系取消。</span><span class="sxs-lookup"><span data-stu-id="4d8f8-157">Propagating the cancellation token ensures that child calls are canceled with their parent.</span></span> <span data-ttu-id="4d8f8-158">[gRPC 用戶端 factory](xref:grpc/clientfactory) ，並 `EnableCallContextPropagation()` 自動傳播取消權杖。</span><span class="sxs-lookup"><span data-stu-id="4d8f8-158">[gRPC client factory](xref:grpc/clientfactory) and `EnableCallContextPropagation()` automatically propagates the cancellation token.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4d8f8-159">其他資源</span><span class="sxs-lookup"><span data-stu-id="4d8f8-159">Additional resources</span></span>

* <xref:grpc/client>
* <xref:grpc/clientfactory>
