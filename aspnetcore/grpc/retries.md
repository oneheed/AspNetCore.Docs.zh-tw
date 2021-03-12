---
title: 使用 gRPC 重試進行暫時性錯誤處理
author: jamesnk
description: 瞭解如何在 .NET 中使用重試來建立復原性、容錯的 gRPC 呼叫。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 02/25/2021
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
uid: grpc/retries
ms.openlocfilehash: 613386d1fedd8b1b04081e3240b8a3aaf7b37012
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589799"
---
# <a name="transient-fault-handling-with-grpc-retries"></a><span data-ttu-id="f74d7-103">使用 gRPC 重試進行暫時性錯誤處理</span><span class="sxs-lookup"><span data-stu-id="f74d7-103">Transient fault handling with gRPC retries</span></span>

<span data-ttu-id="f74d7-104">依 [James 牛頓](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="f74d7-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="f74d7-105">gRPC 重試是一項功能，可讓 gRPC 用戶端自動重試失敗的呼叫。</span><span class="sxs-lookup"><span data-stu-id="f74d7-105">gRPC retries is a feature that allows gRPC clients to automatically retry failed calls.</span></span> <span data-ttu-id="f74d7-106">本文討論如何設定重試原則，以在 .NET 中建立具有復原能力的容錯 gRPC 應用程式。</span><span class="sxs-lookup"><span data-stu-id="f74d7-106">This article discusses how to configure a retry policy to make resilient, fault tolerant gRPC apps in .NET.</span></span>

<span data-ttu-id="f74d7-107">gRPC 重試需要 [gRPC .net。用戶端](https://www.nuget.org/packages/Grpc.Net.Client) 版本 2.36.0-pre1 或更新版本。</span><span class="sxs-lookup"><span data-stu-id="f74d7-107">gRPC retries requires [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) version 2.36.0-pre1 or later.</span></span>

## <a name="transient-fault-handling"></a><span data-ttu-id="f74d7-108">暫時性錯誤處理</span><span class="sxs-lookup"><span data-stu-id="f74d7-108">Transient fault handling</span></span>

<span data-ttu-id="f74d7-109">暫時性錯誤可能會中斷 gRPC 呼叫。</span><span class="sxs-lookup"><span data-stu-id="f74d7-109">gRPC calls can be interrupted by transient faults.</span></span> <span data-ttu-id="f74d7-110">暫時性錯誤包括：</span><span class="sxs-lookup"><span data-stu-id="f74d7-110">Transient faults include:</span></span>

* <span data-ttu-id="f74d7-111">暫時遺失網路連線能力。</span><span class="sxs-lookup"><span data-stu-id="f74d7-111">Momentary loss of network connectivity.</span></span>
* <span data-ttu-id="f74d7-112">暫時無法使用服務。</span><span class="sxs-lookup"><span data-stu-id="f74d7-112">Temporary unavailability of a service.</span></span>
* <span data-ttu-id="f74d7-113">由於伺服器負載而造成的超時。</span><span class="sxs-lookup"><span data-stu-id="f74d7-113">Timeouts due to server load.</span></span>

<span data-ttu-id="f74d7-114">當 gRPC 呼叫中斷時，用戶端會擲回， `RpcException` 並提供錯誤的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="f74d7-114">When a gRPC call is interrupted, the client throws an `RpcException` with details about the error.</span></span> <span data-ttu-id="f74d7-115">用戶端應用程式必須攔截例外狀況，並選擇處理錯誤的方式。</span><span class="sxs-lookup"><span data-stu-id="f74d7-115">The client app must catch the exception and choose how to handle the error.</span></span>

```csharp
var client = new Greeter.GreeterClient(channel);
try
{
    var response = await client.SayHelloAsync(
        new HelloRequest { Name = ".NET" });

    Console.WriteLine("From server: " + response.Message);
}
catch (RpcException ex)
{
    // Write logic to inspect the error and retry
    // if the error is from a transient fault.
}
```

<span data-ttu-id="f74d7-116">複製整個應用程式的重試邏輯很詳細，而且容易出錯。</span><span class="sxs-lookup"><span data-stu-id="f74d7-116">Duplicating retry logic throughout an app is verbose and error prone.</span></span> <span data-ttu-id="f74d7-117">幸運的是，.NET gRPC 用戶端具有自動重試的內建支援。</span><span class="sxs-lookup"><span data-stu-id="f74d7-117">Fortunately the .NET gRPC client has a built-in support for automatic retries.</span></span>

## <a name="configure-a-grpc-retry-policy"></a><span data-ttu-id="f74d7-118">設定 gRPC 重試原則</span><span class="sxs-lookup"><span data-stu-id="f74d7-118">Configure a gRPC retry policy</span></span>

<span data-ttu-id="f74d7-119">建立 gRPC 通道時，會設定一次重試原則：</span><span class="sxs-lookup"><span data-stu-id="f74d7-119">A retry policy is configured once when a gRPC channel is created:</span></span>

```csharp
var defaultMethodConfig = new MethodConfig
{
    Names = { MethodName.Default },
    RetryPolicy = new RetryPolicy
    {
        MaxAttempts = 5,
        InitialBackoff = TimeSpan.FromSeconds(1),
        MaxBackoff = TimeSpan.FromSeconds(5),
        BackoffMultiplier = 1.5,
        RetryableStatusCodes = { StatusCode.Unavailable }
    }
};

var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
{
    ServiceConfig = new ServiceConfig { MethodConfigs = { defaultMethodConfig } }
});
```

<span data-ttu-id="f74d7-120">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="f74d7-120">The preceding code:</span></span>

* <span data-ttu-id="f74d7-121">建立 `MethodConfig`。</span><span class="sxs-lookup"><span data-stu-id="f74d7-121">Creates a `MethodConfig`.</span></span> <span data-ttu-id="f74d7-122">您可以針對每個方法設定重試原則，而且方法會使用屬性來進行比對 `Names` 。</span><span class="sxs-lookup"><span data-stu-id="f74d7-122">Retry policies can be configured per-method and methods are matched using the `Names` property.</span></span> <span data-ttu-id="f74d7-123">這個方法是使用設定的 `MethodName.Default` ，因此會套用至此通道所呼叫的所有 gRPC 方法。</span><span class="sxs-lookup"><span data-stu-id="f74d7-123">This method is configured with `MethodName.Default`, so it's applied to all gRPC methods called by this channel.</span></span>
* <span data-ttu-id="f74d7-124">設定重試原則。</span><span class="sxs-lookup"><span data-stu-id="f74d7-124">Configures a retry policy.</span></span> <span data-ttu-id="f74d7-125">此原則會指示用戶端自動重試因狀態碼而失敗的 gRPC 呼叫 `Unavailable` 。</span><span class="sxs-lookup"><span data-stu-id="f74d7-125">This policy instructs clients to automatically retry gRPC calls that fail with the status code `Unavailable`.</span></span>
* <span data-ttu-id="f74d7-126">藉由設定，將建立的通道設定為使用重試原則 `GrpcChannelOptions.ServiceConfig` 。</span><span class="sxs-lookup"><span data-stu-id="f74d7-126">Configures the created channel to use the retry policy by setting `GrpcChannelOptions.ServiceConfig`.</span></span>

<span data-ttu-id="f74d7-127">使用通道建立的 gRPC 用戶端會自動重試失敗的呼叫：</span><span class="sxs-lookup"><span data-stu-id="f74d7-127">gRPC clients created with the channel will automatically retry failed calls:</span></span>

```csharp
var client = new Greeter.GreeterClient(channel);
var response = await client.SayHelloAsync(
    new HelloRequest { Name = ".NET" });

Console.WriteLine("From server: " + response.Message);
```

### <a name="grpc-retry-options"></a><span data-ttu-id="f74d7-128">gRPC 重試選項</span><span class="sxs-lookup"><span data-stu-id="f74d7-128">gRPC retry options</span></span>

<span data-ttu-id="f74d7-129">下表說明設定 gRPC 重試原則的選項：</span><span class="sxs-lookup"><span data-stu-id="f74d7-129">The following table describes options for configuring gRPC retry policies:</span></span>

| <span data-ttu-id="f74d7-130">選項</span><span class="sxs-lookup"><span data-stu-id="f74d7-130">Option</span></span> | <span data-ttu-id="f74d7-131">Description</span><span class="sxs-lookup"><span data-stu-id="f74d7-131">Description</span></span> |
| ------ | ----------- |
| `MaxAttempts` | <span data-ttu-id="f74d7-132">呼叫嘗試次數的上限，包括原始嘗試。</span><span class="sxs-lookup"><span data-stu-id="f74d7-132">The maximum number of call attempts, including the original attempt.</span></span> <span data-ttu-id="f74d7-133">此值受限於 `GrpcChannelOptions.MaxRetryAttempts` 預設值為5。</span><span class="sxs-lookup"><span data-stu-id="f74d7-133">This value is limited by `GrpcChannelOptions.MaxRetryAttempts` which defaults to 5.</span></span> <span data-ttu-id="f74d7-134">值是必要的，且必須大於1。</span><span class="sxs-lookup"><span data-stu-id="f74d7-134">A value is required and must be greater than 1.</span></span> |
| `InitialBackoff` | <span data-ttu-id="f74d7-135">重試嘗試之間的初始輪詢延遲。</span><span class="sxs-lookup"><span data-stu-id="f74d7-135">The initial backoff delay between retry attempts.</span></span> <span data-ttu-id="f74d7-136">0和目前輪詢之間的隨機延遲，會決定下次重試嘗試的時間。</span><span class="sxs-lookup"><span data-stu-id="f74d7-136">A randomized delay between 0 and the current backoff determines when the next retry attempt is made.</span></span> <span data-ttu-id="f74d7-137">每次嘗試之後，目前的輪詢會乘以 `BackoffMultiplier` 。</span><span class="sxs-lookup"><span data-stu-id="f74d7-137">After each attempt, the current backoff is multiplied by `BackoffMultiplier`.</span></span> <span data-ttu-id="f74d7-138">值是必要的，而且必須大於零。</span><span class="sxs-lookup"><span data-stu-id="f74d7-138">A value is required and must be greater than zero.</span></span> |
| `MaxBackoff` | <span data-ttu-id="f74d7-139">最大輪詢會將指數輪詢成長的上限放在最大值。</span><span class="sxs-lookup"><span data-stu-id="f74d7-139">The maximum backoff places an upper limit on exponential backoff growth.</span></span> <span data-ttu-id="f74d7-140">值是必要的，而且必須大於零。</span><span class="sxs-lookup"><span data-stu-id="f74d7-140">A value is required and must be greater than zero.</span></span> |
| `BackoffMultiplier` | <span data-ttu-id="f74d7-141">在每次重試嘗試之後，輪詢將乘以此值，並會在乘數大於1時以指數方式增加。</span><span class="sxs-lookup"><span data-stu-id="f74d7-141">The backoff will be multiplied by this value after each retry attempt and will increase exponentially when the multiplier is greater than 1.</span></span> <span data-ttu-id="f74d7-142">值是必要的，而且必須大於零。</span><span class="sxs-lookup"><span data-stu-id="f74d7-142">A value is required and must be greater than zero.</span></span> |
| `RetryableStatusCodes` | <span data-ttu-id="f74d7-143">狀態碼的集合。</span><span class="sxs-lookup"><span data-stu-id="f74d7-143">A collection of status codes.</span></span> <span data-ttu-id="f74d7-144">因符合狀態而失敗的 gRPC 呼叫會自動重試。</span><span class="sxs-lookup"><span data-stu-id="f74d7-144">A gRPC call that fails with a matching status will be automatically retried.</span></span> <span data-ttu-id="f74d7-145">如需狀態碼的詳細資訊，請參閱 [狀態碼及其在 gRPC 中的用法](https://grpc.github.io/grpc/core/md_doc_statuscodes.html)。</span><span class="sxs-lookup"><span data-stu-id="f74d7-145">For more information about status codes, see [Status codes and their use in gRPC](https://grpc.github.io/grpc/core/md_doc_statuscodes.html).</span></span> <span data-ttu-id="f74d7-146">至少需要一個可重試的狀態碼。</span><span class="sxs-lookup"><span data-stu-id="f74d7-146">At least one retryable status code is required.</span></span> |

## <a name="hedging"></a><span data-ttu-id="f74d7-147">對沖</span><span class="sxs-lookup"><span data-stu-id="f74d7-147">Hedging</span></span>

<span data-ttu-id="f74d7-148">避險是替代的重試策略。</span><span class="sxs-lookup"><span data-stu-id="f74d7-148">Hedging is an alternative retry strategy.</span></span> <span data-ttu-id="f74d7-149">避險可主動傳送單一 gRPC 呼叫的多個複本，而不需要等待回應。</span><span class="sxs-lookup"><span data-stu-id="f74d7-149">Hedging enables aggressively sending multiple copies of a single gRPC call without waiting for a response.</span></span> <span data-ttu-id="f74d7-150">Hedged gRPC 呼叫可能會在伺服器上執行多次，並使用第一個成功的結果。</span><span class="sxs-lookup"><span data-stu-id="f74d7-150">Hedged gRPC calls may be executed multiple times on the server and the first successful result is used.</span></span> <span data-ttu-id="f74d7-151">請務必只針對可安全執行多次且不會造成負面影響的方法啟用避險。</span><span class="sxs-lookup"><span data-stu-id="f74d7-151">It's important that hedging is only enabled for methods that are safe to execute multiple times without adverse effect.</span></span>

<span data-ttu-id="f74d7-152">相較于重試，避險具有優缺點：</span><span class="sxs-lookup"><span data-stu-id="f74d7-152">Hedging has pros and cons when compared to retries:</span></span> 

* <span data-ttu-id="f74d7-153">避險的優點是，它可能會更快地傳回成功的結果。</span><span class="sxs-lookup"><span data-stu-id="f74d7-153">An advantage to hedging is it might return a successful result faster.</span></span> <span data-ttu-id="f74d7-154">它允許多個同時 gRPC 的呼叫，並且會在第一個成功的結果可用時完成。</span><span class="sxs-lookup"><span data-stu-id="f74d7-154">It allows for multiple simultaneously gRPC calls and will complete when the first successful result is available.</span></span> 
* <span data-ttu-id="f74d7-155">避險的缺點是可能會很浪費。</span><span class="sxs-lookup"><span data-stu-id="f74d7-155">A disadvantage to hedging is it can be wasteful.</span></span> <span data-ttu-id="f74d7-156">可能會進行多個呼叫，且全部都成功。</span><span class="sxs-lookup"><span data-stu-id="f74d7-156">Multiple calls could be made and all succeed.</span></span> <span data-ttu-id="f74d7-157">只會使用第一個結果，並捨棄其餘部分。</span><span class="sxs-lookup"><span data-stu-id="f74d7-157">Only the first result is used and the rest are discarded.</span></span>

## <a name="configure-a-grpc-hedging-policy"></a><span data-ttu-id="f74d7-158">設定 gRPC 避險原則</span><span class="sxs-lookup"><span data-stu-id="f74d7-158">Configure a gRPC hedging policy</span></span>

<span data-ttu-id="f74d7-159">避險原則的設定就像是重試原則。</span><span class="sxs-lookup"><span data-stu-id="f74d7-159">A hedging policy is configured like a retry policy.</span></span> <span data-ttu-id="f74d7-160">請注意，避險原則無法與重試原則合併。</span><span class="sxs-lookup"><span data-stu-id="f74d7-160">Note that a hedging policy can't be combined with a retry policy.</span></span>

```csharp
var defaultMethodConfig = new MethodConfig
{
    Names = { MethodName.Default },
    HedgingPolicy = new HedgingPolicy
    {
        MaxAttempts = 5,
        NonFatalStatusCodes = { StatusCode.Unavailable }
    }
};

var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
{
    ServiceConfig = new ServiceConfig { MethodConfigs = { defaultMethodConfig } }
});
```

### <a name="grpc-hedging-options"></a><span data-ttu-id="f74d7-161">gRPC 避險選項</span><span class="sxs-lookup"><span data-stu-id="f74d7-161">gRPC hedging options</span></span>

<span data-ttu-id="f74d7-162">下表說明設定 gRPC 避險原則的選項：</span><span class="sxs-lookup"><span data-stu-id="f74d7-162">The following table describes options for configuring gRPC hedging policies:</span></span>

| <span data-ttu-id="f74d7-163">選項</span><span class="sxs-lookup"><span data-stu-id="f74d7-163">Option</span></span> | <span data-ttu-id="f74d7-164">Description</span><span class="sxs-lookup"><span data-stu-id="f74d7-164">Description</span></span> |
| ------ | ----------- |
| `MaxAttempts` | <span data-ttu-id="f74d7-165">避險原則會傳送到此次數的呼叫。</span><span class="sxs-lookup"><span data-stu-id="f74d7-165">The hedging policy will send up to this number of calls.</span></span> <span data-ttu-id="f74d7-166">`MaxAttempts` 表示所有嘗試的總次數，包括原始嘗試。</span><span class="sxs-lookup"><span data-stu-id="f74d7-166">`MaxAttempts` represents the total number of all attempts, including the original attempt.</span></span> <span data-ttu-id="f74d7-167">此值受限於 `GrpcChannelOptions.MaxRetryAttempts` 預設值為5。</span><span class="sxs-lookup"><span data-stu-id="f74d7-167">This value is limited by `GrpcChannelOptions.MaxRetryAttempts` which defaults to 5.</span></span> <span data-ttu-id="f74d7-168">值是必要的，而且必須是2或以上。</span><span class="sxs-lookup"><span data-stu-id="f74d7-168">A value is required and must be 2 or greater.</span></span> |
| `HedgingDelay` | <span data-ttu-id="f74d7-169">第一個呼叫會立即傳送，但後續的避險呼叫將會被此值延遲。</span><span class="sxs-lookup"><span data-stu-id="f74d7-169">The first call will be sent immediately, but the subsequent hedging calls will be delayed by this value.</span></span> <span data-ttu-id="f74d7-170">當延遲設定為零或時 `null` ，會立即傳送所有 hedged 呼叫。</span><span class="sxs-lookup"><span data-stu-id="f74d7-170">When the delay is set to zero or `null`, all hedged calls are sent immediately.</span></span> <span data-ttu-id="f74d7-171">預設值為零。</span><span class="sxs-lookup"><span data-stu-id="f74d7-171">Default value is zero.</span></span> |
| `NonFatalStatusCodes` | <span data-ttu-id="f74d7-172">表示其他籬呼叫仍可能成功的狀態碼集合。</span><span class="sxs-lookup"><span data-stu-id="f74d7-172">A collection of status codes which indicate other hedge calls may still succeed.</span></span> <span data-ttu-id="f74d7-173">如果伺服器傳回非嚴重的狀態碼，hedged 呼叫將會繼續。</span><span class="sxs-lookup"><span data-stu-id="f74d7-173">If a non-fatal status code is returned by the server, hedged calls will continue.</span></span> <span data-ttu-id="f74d7-174">否則，將會取消未完成的要求，並將錯誤傳回給應用程式。</span><span class="sxs-lookup"><span data-stu-id="f74d7-174">Otherwise, outstanding requests will be canceled and the error returned to the app.</span></span> <span data-ttu-id="f74d7-175">如需狀態碼的詳細資訊，請參閱 [狀態碼及其在 gRPC 中的用法](https://grpc.github.io/grpc/core/md_doc_statuscodes.html)。</span><span class="sxs-lookup"><span data-stu-id="f74d7-175">For more information about status codes, see [Status codes and their use in gRPC](https://grpc.github.io/grpc/core/md_doc_statuscodes.html).</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="f74d7-176">其他資源</span><span class="sxs-lookup"><span data-stu-id="f74d7-176">Additional resources</span></span>

* <xref:grpc/client>
* [<span data-ttu-id="f74d7-177">重試一般指引-雲端應用程式的最佳作法</span><span class="sxs-lookup"><span data-stu-id="f74d7-177">Retry general guidance - Best practices for cloud applications</span></span>](/azure/architecture/best-practices/transient-faults)
