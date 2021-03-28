---
title: 使用 gRPC 重試進行暫時性錯誤處理
author: jamesnk
description: 瞭解如何在 .NET 中使用重試來建立復原性、容錯的 gRPC 呼叫。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 03/18/2021
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
ms.openlocfilehash: 116c201d728c3631f2be107b95e4fa38db35f074
ms.sourcegitcommit: 7b6781051d341a1daaf46c6a4368fa8a5701db81
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2021
ms.locfileid: "105638831"
---
# <a name="transient-fault-handling-with-grpc-retries"></a><span data-ttu-id="7a518-103">使用 gRPC 重試進行暫時性錯誤處理</span><span class="sxs-lookup"><span data-stu-id="7a518-103">Transient fault handling with gRPC retries</span></span>

<span data-ttu-id="7a518-104">依 [James 牛頓](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="7a518-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="7a518-105">gRPC 重試是一項功能，可讓 gRPC 用戶端自動重試失敗的呼叫。</span><span class="sxs-lookup"><span data-stu-id="7a518-105">gRPC retries is a feature that allows gRPC clients to automatically retry failed calls.</span></span> <span data-ttu-id="7a518-106">本文討論如何設定重試原則，以在 .NET 中建立具有復原能力的容錯 gRPC 應用程式。</span><span class="sxs-lookup"><span data-stu-id="7a518-106">This article discusses how to configure a retry policy to make resilient, fault tolerant gRPC apps in .NET.</span></span>

<span data-ttu-id="7a518-107">gRPC 重試需要 [gRPC .net。用戶端](https://www.nuget.org/packages/Grpc.Net.Client) 版本2.36.0 或更新版本。</span><span class="sxs-lookup"><span data-stu-id="7a518-107">gRPC retries requires [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) version 2.36.0 or later.</span></span>

## <a name="transient-fault-handling"></a><span data-ttu-id="7a518-108">暫時性錯誤處理</span><span class="sxs-lookup"><span data-stu-id="7a518-108">Transient fault handling</span></span>

<span data-ttu-id="7a518-109">暫時性錯誤可能會中斷 gRPC 呼叫。</span><span class="sxs-lookup"><span data-stu-id="7a518-109">gRPC calls can be interrupted by transient faults.</span></span> <span data-ttu-id="7a518-110">暫時性錯誤包括：</span><span class="sxs-lookup"><span data-stu-id="7a518-110">Transient faults include:</span></span>

* <span data-ttu-id="7a518-111">暫時遺失網路連線能力。</span><span class="sxs-lookup"><span data-stu-id="7a518-111">Momentary loss of network connectivity.</span></span>
* <span data-ttu-id="7a518-112">暫時無法使用服務。</span><span class="sxs-lookup"><span data-stu-id="7a518-112">Temporary unavailability of a service.</span></span>
* <span data-ttu-id="7a518-113">由於伺服器負載而造成的超時。</span><span class="sxs-lookup"><span data-stu-id="7a518-113">Timeouts due to server load.</span></span>

<span data-ttu-id="7a518-114">當 gRPC 呼叫中斷時，用戶端會擲回， `RpcException` 並提供錯誤的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="7a518-114">When a gRPC call is interrupted, the client throws an `RpcException` with details about the error.</span></span> <span data-ttu-id="7a518-115">用戶端應用程式必須攔截例外狀況，並選擇處理錯誤的方式。</span><span class="sxs-lookup"><span data-stu-id="7a518-115">The client app must catch the exception and choose how to handle the error.</span></span>

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

<span data-ttu-id="7a518-116">複製整個應用程式的重試邏輯很詳細，而且容易出錯。</span><span class="sxs-lookup"><span data-stu-id="7a518-116">Duplicating retry logic throughout an app is verbose and error prone.</span></span> <span data-ttu-id="7a518-117">幸運的是，.NET gRPC 用戶端具有自動重試的內建支援。</span><span class="sxs-lookup"><span data-stu-id="7a518-117">Fortunately the .NET gRPC client has a built-in support for automatic retries.</span></span>

## <a name="configure-a-grpc-retry-policy"></a><span data-ttu-id="7a518-118">設定 gRPC 重試原則</span><span class="sxs-lookup"><span data-stu-id="7a518-118">Configure a gRPC retry policy</span></span>

<span data-ttu-id="7a518-119">建立 gRPC 通道時，會設定一次重試原則：</span><span class="sxs-lookup"><span data-stu-id="7a518-119">A retry policy is configured once when a gRPC channel is created:</span></span>

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

<span data-ttu-id="7a518-120">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="7a518-120">The preceding code:</span></span>

* <span data-ttu-id="7a518-121">建立 `MethodConfig`。</span><span class="sxs-lookup"><span data-stu-id="7a518-121">Creates a `MethodConfig`.</span></span> <span data-ttu-id="7a518-122">您可以針對每個方法設定重試原則，而且方法會使用屬性來進行比對 `Names` 。</span><span class="sxs-lookup"><span data-stu-id="7a518-122">Retry policies can be configured per-method and methods are matched using the `Names` property.</span></span> <span data-ttu-id="7a518-123">這個方法是使用設定的 `MethodName.Default` ，因此會套用至此通道所呼叫的所有 gRPC 方法。</span><span class="sxs-lookup"><span data-stu-id="7a518-123">This method is configured with `MethodName.Default`, so it's applied to all gRPC methods called by this channel.</span></span>
* <span data-ttu-id="7a518-124">設定重試原則。</span><span class="sxs-lookup"><span data-stu-id="7a518-124">Configures a retry policy.</span></span> <span data-ttu-id="7a518-125">此原則會指示用戶端自動重試因狀態碼而失敗的 gRPC 呼叫 `Unavailable` 。</span><span class="sxs-lookup"><span data-stu-id="7a518-125">This policy instructs clients to automatically retry gRPC calls that fail with the status code `Unavailable`.</span></span>
* <span data-ttu-id="7a518-126">藉由設定，將建立的通道設定為使用重試原則 `GrpcChannelOptions.ServiceConfig` 。</span><span class="sxs-lookup"><span data-stu-id="7a518-126">Configures the created channel to use the retry policy by setting `GrpcChannelOptions.ServiceConfig`.</span></span>

<span data-ttu-id="7a518-127">使用通道建立的 gRPC 用戶端會自動重試失敗的呼叫：</span><span class="sxs-lookup"><span data-stu-id="7a518-127">gRPC clients created with the channel will automatically retry failed calls:</span></span>

```csharp
var client = new Greeter.GreeterClient(channel);
var response = await client.SayHelloAsync(
    new HelloRequest { Name = ".NET" });

Console.WriteLine("From server: " + response.Message);
```

### <a name="when-retries-are-valid"></a><span data-ttu-id="7a518-128">當重試有效時</span><span class="sxs-lookup"><span data-stu-id="7a518-128">When retries are valid</span></span>

<span data-ttu-id="7a518-129">呼叫會在下列情況重試：</span><span class="sxs-lookup"><span data-stu-id="7a518-129">Calls are retried when:</span></span>

* <span data-ttu-id="7a518-130">失敗狀態碼符合中的值 `RetryableStatusCodes` 。</span><span class="sxs-lookup"><span data-stu-id="7a518-130">The failing status code matches a value in `RetryableStatusCodes`.</span></span>
* <span data-ttu-id="7a518-131">先前的嘗試次數小於 `MaxAttempts` 。</span><span class="sxs-lookup"><span data-stu-id="7a518-131">The previous number of attempts is less than `MaxAttempts`.</span></span>
* <span data-ttu-id="7a518-132">呼叫尚未經過認可。</span><span class="sxs-lookup"><span data-stu-id="7a518-132">The call hasn't been commited.</span></span>

<span data-ttu-id="7a518-133">GRPC 呼叫會在兩個案例中認可：</span><span class="sxs-lookup"><span data-stu-id="7a518-133">A gRPC call becomes committed in two scenarios:</span></span>

* <span data-ttu-id="7a518-134">用戶端會接收回應標頭。</span><span class="sxs-lookup"><span data-stu-id="7a518-134">The client receives response headers.</span></span> <span data-ttu-id="7a518-135">當 `ServerCallContext.WriteResponseHeadersAsync` 呼叫時，或當第一個訊息寫入伺服器回應資料流程時，伺服器會傳送回應標頭。</span><span class="sxs-lookup"><span data-stu-id="7a518-135">Response headers are sent by the server when `ServerCallContext.WriteResponseHeadersAsync` is called, or when the first message is written to the server response stream.</span></span>
* <span data-ttu-id="7a518-136">如果串流) 超過用戶端的緩衝區大小上限，用戶端的外寄訊息 (或訊息。</span><span class="sxs-lookup"><span data-stu-id="7a518-136">The client's outgoing message (or messages if streaming) has exceeded the client's maximum buffer size.</span></span> <span data-ttu-id="7a518-137">`MaxRetryBufferSize` 和 `MaxRetryBufferPerCallSize` 會 [在通道上進行設定](xref:grpc/configuration#configure-client-options)。</span><span class="sxs-lookup"><span data-stu-id="7a518-137">`MaxRetryBufferSize` and `MaxRetryBufferPerCallSize` are [configured on the channel](xref:grpc/configuration#configure-client-options).</span></span>

<span data-ttu-id="7a518-138">無論狀態碼或先前的嘗試次數為何，已認可的呼叫都不會重試。</span><span class="sxs-lookup"><span data-stu-id="7a518-138">Committed calls won't retry, regardless of the status code or the previous number of attempts.</span></span>

### <a name="streaming-calls"></a><span data-ttu-id="7a518-139">串流呼叫</span><span class="sxs-lookup"><span data-stu-id="7a518-139">Streaming calls</span></span>

<span data-ttu-id="7a518-140">串流呼叫可以與 gRPC 重試搭配使用，但同時使用時，有一些重要的考慮：</span><span class="sxs-lookup"><span data-stu-id="7a518-140">Streaming calls can be used with gRPC retries, but there are important considerations when they are used together:</span></span>

* <span data-ttu-id="7a518-141">**伺服器串流**（ **雙向串流**：從伺服器傳回多個訊息的串流 rpc）將不會在收到第一則訊息之後重試。</span><span class="sxs-lookup"><span data-stu-id="7a518-141">**Server streaming**, **bidirectional streaming**: Streaming RPCs that return multiple messages from the server won't retry after the first message has been received.</span></span> <span data-ttu-id="7a518-142">應用程式必須新增額外的邏輯，以手動重新建立伺服器和雙向串流呼叫。</span><span class="sxs-lookup"><span data-stu-id="7a518-142">Apps must add additional logic to manually re-establish server and bidirectional streaming calls.</span></span>
* <span data-ttu-id="7a518-143">**用戶端串流**（ **雙向串流**）：如果外寄訊息超過用戶端的緩衝區大小上限，則傳送多個訊息至伺服器的串流 rpc 將不會重試。</span><span class="sxs-lookup"><span data-stu-id="7a518-143">**Client streaming**, **bidirectional streaming**: Streaming RPCs that send multiple messages to the server won't retry if the outgoing messages have exceeded the client's maximum buffer size.</span></span> <span data-ttu-id="7a518-144">您可以使用設定來增加緩衝區大小上限。</span><span class="sxs-lookup"><span data-stu-id="7a518-144">The maximum buffer size can be increased with configuration.</span></span>

<span data-ttu-id="7a518-145">如需詳細資訊，請參閱 [何時有效重試](#when-retries-are-valid)。</span><span class="sxs-lookup"><span data-stu-id="7a518-145">For more information, see [When retries are valid](#when-retries-are-valid).</span></span>

### <a name="grpc-retry-options"></a><span data-ttu-id="7a518-146">gRPC 重試選項</span><span class="sxs-lookup"><span data-stu-id="7a518-146">gRPC retry options</span></span>

<span data-ttu-id="7a518-147">下表說明設定 gRPC 重試原則的選項：</span><span class="sxs-lookup"><span data-stu-id="7a518-147">The following table describes options for configuring gRPC retry policies:</span></span>

| <span data-ttu-id="7a518-148">選項</span><span class="sxs-lookup"><span data-stu-id="7a518-148">Option</span></span> | <span data-ttu-id="7a518-149">Description</span><span class="sxs-lookup"><span data-stu-id="7a518-149">Description</span></span> |
| ------ | ----------- |
| `MaxAttempts` | <span data-ttu-id="7a518-150">呼叫嘗試次數的上限，包括原始嘗試。</span><span class="sxs-lookup"><span data-stu-id="7a518-150">The maximum number of call attempts, including the original attempt.</span></span> <span data-ttu-id="7a518-151">此值受限於 `GrpcChannelOptions.MaxRetryAttempts` 預設值為5。</span><span class="sxs-lookup"><span data-stu-id="7a518-151">This value is limited by `GrpcChannelOptions.MaxRetryAttempts` which defaults to 5.</span></span> <span data-ttu-id="7a518-152">值是必要的，且必須大於1。</span><span class="sxs-lookup"><span data-stu-id="7a518-152">A value is required and must be greater than 1.</span></span> |
| `InitialBackoff` | <span data-ttu-id="7a518-153">重試嘗試之間的初始輪詢延遲。</span><span class="sxs-lookup"><span data-stu-id="7a518-153">The initial backoff delay between retry attempts.</span></span> <span data-ttu-id="7a518-154">0和目前輪詢之間的隨機延遲，會決定下次重試嘗試的時間。</span><span class="sxs-lookup"><span data-stu-id="7a518-154">A randomized delay between 0 and the current backoff determines when the next retry attempt is made.</span></span> <span data-ttu-id="7a518-155">每次嘗試之後，目前的輪詢會乘以 `BackoffMultiplier` 。</span><span class="sxs-lookup"><span data-stu-id="7a518-155">After each attempt, the current backoff is multiplied by `BackoffMultiplier`.</span></span> <span data-ttu-id="7a518-156">值是必要的，而且必須大於零。</span><span class="sxs-lookup"><span data-stu-id="7a518-156">A value is required and must be greater than zero.</span></span> |
| `MaxBackoff` | <span data-ttu-id="7a518-157">最大輪詢會將指數輪詢成長的上限放在最大值。</span><span class="sxs-lookup"><span data-stu-id="7a518-157">The maximum backoff places an upper limit on exponential backoff growth.</span></span> <span data-ttu-id="7a518-158">值是必要的，而且必須大於零。</span><span class="sxs-lookup"><span data-stu-id="7a518-158">A value is required and must be greater than zero.</span></span> |
| `BackoffMultiplier` | <span data-ttu-id="7a518-159">在每次重試嘗試之後，輪詢將乘以此值，並會在乘數大於1時以指數方式增加。</span><span class="sxs-lookup"><span data-stu-id="7a518-159">The backoff will be multiplied by this value after each retry attempt and will increase exponentially when the multiplier is greater than 1.</span></span> <span data-ttu-id="7a518-160">值是必要的，而且必須大於零。</span><span class="sxs-lookup"><span data-stu-id="7a518-160">A value is required and must be greater than zero.</span></span> |
| `RetryableStatusCodes` | <span data-ttu-id="7a518-161">狀態碼的集合。</span><span class="sxs-lookup"><span data-stu-id="7a518-161">A collection of status codes.</span></span> <span data-ttu-id="7a518-162">因符合狀態而失敗的 gRPC 呼叫會自動重試。</span><span class="sxs-lookup"><span data-stu-id="7a518-162">A gRPC call that fails with a matching status will be automatically retried.</span></span> <span data-ttu-id="7a518-163">如需狀態碼的詳細資訊，請參閱 [狀態碼及其在 gRPC 中的用法](https://grpc.github.io/grpc/core/md_doc_statuscodes.html)。</span><span class="sxs-lookup"><span data-stu-id="7a518-163">For more information about status codes, see [Status codes and their use in gRPC](https://grpc.github.io/grpc/core/md_doc_statuscodes.html).</span></span> <span data-ttu-id="7a518-164">至少需要一個可重試的狀態碼。</span><span class="sxs-lookup"><span data-stu-id="7a518-164">At least one retryable status code is required.</span></span> |

## <a name="hedging"></a><span data-ttu-id="7a518-165">對沖</span><span class="sxs-lookup"><span data-stu-id="7a518-165">Hedging</span></span>

<span data-ttu-id="7a518-166">避險是替代的重試策略。</span><span class="sxs-lookup"><span data-stu-id="7a518-166">Hedging is an alternative retry strategy.</span></span> <span data-ttu-id="7a518-167">避險可主動傳送單一 gRPC 呼叫的多個複本，而不需要等待回應。</span><span class="sxs-lookup"><span data-stu-id="7a518-167">Hedging enables aggressively sending multiple copies of a single gRPC call without waiting for a response.</span></span> <span data-ttu-id="7a518-168">Hedged gRPC 呼叫可能會在伺服器上執行多次，並使用第一個成功的結果。</span><span class="sxs-lookup"><span data-stu-id="7a518-168">Hedged gRPC calls may be executed multiple times on the server and the first successful result is used.</span></span> <span data-ttu-id="7a518-169">請務必只針對可安全執行多次且不會造成負面影響的方法啟用避險。</span><span class="sxs-lookup"><span data-stu-id="7a518-169">It's important that hedging is only enabled for methods that are safe to execute multiple times without adverse effect.</span></span>

<span data-ttu-id="7a518-170">相較于重試，避險具有優缺點：</span><span class="sxs-lookup"><span data-stu-id="7a518-170">Hedging has pros and cons when compared to retries:</span></span> 

* <span data-ttu-id="7a518-171">避險的優點是，它可能會更快地傳回成功的結果。</span><span class="sxs-lookup"><span data-stu-id="7a518-171">An advantage to hedging is it might return a successful result faster.</span></span> <span data-ttu-id="7a518-172">它允許多個同時 gRPC 的呼叫，並且會在第一個成功的結果可用時完成。</span><span class="sxs-lookup"><span data-stu-id="7a518-172">It allows for multiple simultaneously gRPC calls and will complete when the first successful result is available.</span></span> 
* <span data-ttu-id="7a518-173">避險的缺點是可能會很浪費。</span><span class="sxs-lookup"><span data-stu-id="7a518-173">A disadvantage to hedging is it can be wasteful.</span></span> <span data-ttu-id="7a518-174">可能會進行多個呼叫，且全部都成功。</span><span class="sxs-lookup"><span data-stu-id="7a518-174">Multiple calls could be made and all succeed.</span></span> <span data-ttu-id="7a518-175">只會使用第一個結果，並捨棄其餘部分。</span><span class="sxs-lookup"><span data-stu-id="7a518-175">Only the first result is used and the rest are discarded.</span></span>

## <a name="configure-a-grpc-hedging-policy"></a><span data-ttu-id="7a518-176">設定 gRPC 避險原則</span><span class="sxs-lookup"><span data-stu-id="7a518-176">Configure a gRPC hedging policy</span></span>

<span data-ttu-id="7a518-177">避險原則的設定就像是重試原則。</span><span class="sxs-lookup"><span data-stu-id="7a518-177">A hedging policy is configured like a retry policy.</span></span> <span data-ttu-id="7a518-178">請注意，避險原則無法與重試原則合併。</span><span class="sxs-lookup"><span data-stu-id="7a518-178">Note that a hedging policy can't be combined with a retry policy.</span></span>

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

### <a name="grpc-hedging-options"></a><span data-ttu-id="7a518-179">gRPC 避險選項</span><span class="sxs-lookup"><span data-stu-id="7a518-179">gRPC hedging options</span></span>

<span data-ttu-id="7a518-180">下表說明設定 gRPC 避險原則的選項：</span><span class="sxs-lookup"><span data-stu-id="7a518-180">The following table describes options for configuring gRPC hedging policies:</span></span>

| <span data-ttu-id="7a518-181">選項</span><span class="sxs-lookup"><span data-stu-id="7a518-181">Option</span></span> | <span data-ttu-id="7a518-182">Description</span><span class="sxs-lookup"><span data-stu-id="7a518-182">Description</span></span> |
| ------ | ----------- |
| `MaxAttempts` | <span data-ttu-id="7a518-183">避險原則會傳送到此次數的呼叫。</span><span class="sxs-lookup"><span data-stu-id="7a518-183">The hedging policy will send up to this number of calls.</span></span> <span data-ttu-id="7a518-184">`MaxAttempts` 表示所有嘗試的總次數，包括原始嘗試。</span><span class="sxs-lookup"><span data-stu-id="7a518-184">`MaxAttempts` represents the total number of all attempts, including the original attempt.</span></span> <span data-ttu-id="7a518-185">此值受限於 `GrpcChannelOptions.MaxRetryAttempts` 預設值為5。</span><span class="sxs-lookup"><span data-stu-id="7a518-185">This value is limited by `GrpcChannelOptions.MaxRetryAttempts` which defaults to 5.</span></span> <span data-ttu-id="7a518-186">值是必要的，而且必須是2或以上。</span><span class="sxs-lookup"><span data-stu-id="7a518-186">A value is required and must be 2 or greater.</span></span> |
| `HedgingDelay` | <span data-ttu-id="7a518-187">第一個呼叫會立即傳送，但後續的避險呼叫將會被此值延遲。</span><span class="sxs-lookup"><span data-stu-id="7a518-187">The first call will be sent immediately, but the subsequent hedging calls will be delayed by this value.</span></span> <span data-ttu-id="7a518-188">當延遲設定為零或時 `null` ，會立即傳送所有 hedged 呼叫。</span><span class="sxs-lookup"><span data-stu-id="7a518-188">When the delay is set to zero or `null`, all hedged calls are sent immediately.</span></span> <span data-ttu-id="7a518-189">預設值為零。</span><span class="sxs-lookup"><span data-stu-id="7a518-189">Default value is zero.</span></span> |
| `NonFatalStatusCodes` | <span data-ttu-id="7a518-190">表示其他籬呼叫仍可能成功的狀態碼集合。</span><span class="sxs-lookup"><span data-stu-id="7a518-190">A collection of status codes which indicate other hedge calls may still succeed.</span></span> <span data-ttu-id="7a518-191">如果伺服器傳回非嚴重的狀態碼，hedged 呼叫將會繼續。</span><span class="sxs-lookup"><span data-stu-id="7a518-191">If a non-fatal status code is returned by the server, hedged calls will continue.</span></span> <span data-ttu-id="7a518-192">否則，將會取消未完成的要求，並將錯誤傳回給應用程式。</span><span class="sxs-lookup"><span data-stu-id="7a518-192">Otherwise, outstanding requests will be canceled and the error returned to the app.</span></span> <span data-ttu-id="7a518-193">如需狀態碼的詳細資訊，請參閱 [狀態碼及其在 gRPC 中的用法](https://grpc.github.io/grpc/core/md_doc_statuscodes.html)。</span><span class="sxs-lookup"><span data-stu-id="7a518-193">For more information about status codes, see [Status codes and their use in gRPC](https://grpc.github.io/grpc/core/md_doc_statuscodes.html).</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="7a518-194">其他資源</span><span class="sxs-lookup"><span data-stu-id="7a518-194">Additional resources</span></span>

* <xref:grpc/client>
* [<span data-ttu-id="7a518-195">重試一般指引-雲端應用程式的最佳作法</span><span class="sxs-lookup"><span data-stu-id="7a518-195">Retry general guidance - Best practices for cloud applications</span></span>](/azure/architecture/best-practices/transient-faults)
