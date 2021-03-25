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
ms.openlocfilehash: 4fda4968102740af8d1a7f37dcc588abd24ab70e
ms.sourcegitcommit: b81327f1a62e9857d9e51fb34775f752261a88ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2021
ms.locfileid: "105050993"
---
# <a name="transient-fault-handling-with-grpc-retries"></a><span data-ttu-id="8209a-103">使用 gRPC 重試進行暫時性錯誤處理</span><span class="sxs-lookup"><span data-stu-id="8209a-103">Transient fault handling with gRPC retries</span></span>

<span data-ttu-id="8209a-104">依 [James 牛頓](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="8209a-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="8209a-105">gRPC 重試是一項功能，可讓 gRPC 用戶端自動重試失敗的呼叫。</span><span class="sxs-lookup"><span data-stu-id="8209a-105">gRPC retries is a feature that allows gRPC clients to automatically retry failed calls.</span></span> <span data-ttu-id="8209a-106">本文討論如何設定重試原則，以在 .NET 中建立具有復原能力的容錯 gRPC 應用程式。</span><span class="sxs-lookup"><span data-stu-id="8209a-106">This article discusses how to configure a retry policy to make resilient, fault tolerant gRPC apps in .NET.</span></span>

<span data-ttu-id="8209a-107">gRPC 重試需要 [gRPC .net。用戶端](https://www.nuget.org/packages/Grpc.Net.Client) 版本2.36.0 或更新版本。</span><span class="sxs-lookup"><span data-stu-id="8209a-107">gRPC retries requires [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) version 2.36.0 or later.</span></span>

## <a name="transient-fault-handling"></a><span data-ttu-id="8209a-108">暫時性錯誤處理</span><span class="sxs-lookup"><span data-stu-id="8209a-108">Transient fault handling</span></span>

<span data-ttu-id="8209a-109">暫時性錯誤可能會中斷 gRPC 呼叫。</span><span class="sxs-lookup"><span data-stu-id="8209a-109">gRPC calls can be interrupted by transient faults.</span></span> <span data-ttu-id="8209a-110">暫時性錯誤包括：</span><span class="sxs-lookup"><span data-stu-id="8209a-110">Transient faults include:</span></span>

* <span data-ttu-id="8209a-111">暫時遺失網路連線能力。</span><span class="sxs-lookup"><span data-stu-id="8209a-111">Momentary loss of network connectivity.</span></span>
* <span data-ttu-id="8209a-112">暫時無法使用服務。</span><span class="sxs-lookup"><span data-stu-id="8209a-112">Temporary unavailability of a service.</span></span>
* <span data-ttu-id="8209a-113">由於伺服器負載而造成的超時。</span><span class="sxs-lookup"><span data-stu-id="8209a-113">Timeouts due to server load.</span></span>

<span data-ttu-id="8209a-114">當 gRPC 呼叫中斷時，用戶端會擲回， `RpcException` 並提供錯誤的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="8209a-114">When a gRPC call is interrupted, the client throws an `RpcException` with details about the error.</span></span> <span data-ttu-id="8209a-115">用戶端應用程式必須攔截例外狀況，並選擇處理錯誤的方式。</span><span class="sxs-lookup"><span data-stu-id="8209a-115">The client app must catch the exception and choose how to handle the error.</span></span>

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

<span data-ttu-id="8209a-116">複製整個應用程式的重試邏輯很詳細，而且容易出錯。</span><span class="sxs-lookup"><span data-stu-id="8209a-116">Duplicating retry logic throughout an app is verbose and error prone.</span></span> <span data-ttu-id="8209a-117">幸運的是，.NET gRPC 用戶端具有自動重試的內建支援。</span><span class="sxs-lookup"><span data-stu-id="8209a-117">Fortunately the .NET gRPC client has a built-in support for automatic retries.</span></span>

## <a name="configure-a-grpc-retry-policy"></a><span data-ttu-id="8209a-118">設定 gRPC 重試原則</span><span class="sxs-lookup"><span data-stu-id="8209a-118">Configure a gRPC retry policy</span></span>

<span data-ttu-id="8209a-119">建立 gRPC 通道時，會設定一次重試原則：</span><span class="sxs-lookup"><span data-stu-id="8209a-119">A retry policy is configured once when a gRPC channel is created:</span></span>

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

<span data-ttu-id="8209a-120">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="8209a-120">The preceding code:</span></span>

* <span data-ttu-id="8209a-121">建立 `MethodConfig`。</span><span class="sxs-lookup"><span data-stu-id="8209a-121">Creates a `MethodConfig`.</span></span> <span data-ttu-id="8209a-122">您可以針對每個方法設定重試原則，而且方法會使用屬性來進行比對 `Names` 。</span><span class="sxs-lookup"><span data-stu-id="8209a-122">Retry policies can be configured per-method and methods are matched using the `Names` property.</span></span> <span data-ttu-id="8209a-123">這個方法是使用設定的 `MethodName.Default` ，因此會套用至此通道所呼叫的所有 gRPC 方法。</span><span class="sxs-lookup"><span data-stu-id="8209a-123">This method is configured with `MethodName.Default`, so it's applied to all gRPC methods called by this channel.</span></span>
* <span data-ttu-id="8209a-124">設定重試原則。</span><span class="sxs-lookup"><span data-stu-id="8209a-124">Configures a retry policy.</span></span> <span data-ttu-id="8209a-125">此原則會指示用戶端自動重試因狀態碼而失敗的 gRPC 呼叫 `Unavailable` 。</span><span class="sxs-lookup"><span data-stu-id="8209a-125">This policy instructs clients to automatically retry gRPC calls that fail with the status code `Unavailable`.</span></span>
* <span data-ttu-id="8209a-126">藉由設定，將建立的通道設定為使用重試原則 `GrpcChannelOptions.ServiceConfig` 。</span><span class="sxs-lookup"><span data-stu-id="8209a-126">Configures the created channel to use the retry policy by setting `GrpcChannelOptions.ServiceConfig`.</span></span>

<span data-ttu-id="8209a-127">使用通道建立的 gRPC 用戶端會自動重試失敗的呼叫：</span><span class="sxs-lookup"><span data-stu-id="8209a-127">gRPC clients created with the channel will automatically retry failed calls:</span></span>

```csharp
var client = new Greeter.GreeterClient(channel);
var response = await client.SayHelloAsync(
    new HelloRequest { Name = ".NET" });

Console.WriteLine("From server: " + response.Message);
```

### <a name="when-retries-are-valid"></a><span data-ttu-id="8209a-128">當重試有效時</span><span class="sxs-lookup"><span data-stu-id="8209a-128">When retries are valid</span></span>

<span data-ttu-id="8209a-129">如果失敗狀態碼符合已設定的狀態碼，且先前的嘗試次數小於最大嘗試次數，則會重試呼叫。</span><span class="sxs-lookup"><span data-stu-id="8209a-129">Calls are retried if the failing status code matches a configured status code and the previous number of attempts is less than the maximum attempts.</span></span> <span data-ttu-id="8209a-130">在某些情況下，重試 gRPC 呼叫是不正確。</span><span class="sxs-lookup"><span data-stu-id="8209a-130">In certain cases it is not valid to retry a gRPC call.</span></span> <span data-ttu-id="8209a-131">這些案例會在已認可呼叫時發生。</span><span class="sxs-lookup"><span data-stu-id="8209a-131">These cases occur when the call has been committed.</span></span>

<span data-ttu-id="8209a-132">GRPC 呼叫會在兩個案例中認可：</span><span class="sxs-lookup"><span data-stu-id="8209a-132">A gRPC call becomes committed in two scenarios:</span></span>

* <span data-ttu-id="8209a-133">用戶端會接收回應標頭。</span><span class="sxs-lookup"><span data-stu-id="8209a-133">The client receives response headers.</span></span> <span data-ttu-id="8209a-134">當 `ServerCallContext.WriteResponseHeadersAsync` 呼叫時，或當第一個訊息寫入伺服器回應資料流程時，伺服器會傳送回應標頭。</span><span class="sxs-lookup"><span data-stu-id="8209a-134">Response headers are sent by the server when `ServerCallContext.WriteResponseHeadersAsync` is called, or when the first message is written to the server response stream.</span></span>
* <span data-ttu-id="8209a-135">如果串流) 超過用戶端的緩衝區大小上限，用戶端的外寄訊息 (或訊息。</span><span class="sxs-lookup"><span data-stu-id="8209a-135">The client's outgoing message (or messages if streaming) has exceeded the client's maximum buffer size.</span></span> <span data-ttu-id="8209a-136">`MaxRetryBufferSize` 和 `MaxRetryBufferPerCallSize` 會 [在通道上進行設定](xref:grpc/configuration#configure-client-options)。</span><span class="sxs-lookup"><span data-stu-id="8209a-136">`MaxRetryBufferSize` and `MaxRetryBufferPerCallSize` are [configured on the channel](xref:grpc/configuration#configure-client-options).</span></span>

<span data-ttu-id="8209a-137">無論狀態碼或先前的嘗試次數為何，已認可的呼叫都不會重試。</span><span class="sxs-lookup"><span data-stu-id="8209a-137">Committed calls won't retry, regardless of the status code or the previous number of attempts.</span></span>

### <a name="streaming-calls"></a><span data-ttu-id="8209a-138">串流呼叫</span><span class="sxs-lookup"><span data-stu-id="8209a-138">Streaming calls</span></span>

<span data-ttu-id="8209a-139">串流呼叫可以與 gRPC 重試搭配使用，但同時使用時，有一些重要的考慮：</span><span class="sxs-lookup"><span data-stu-id="8209a-139">Streaming calls can be used with gRPC retries, but there are important considerations when they are used together:</span></span>

* <span data-ttu-id="8209a-140">**伺服器串流**（ **雙向串流**：從伺服器傳回多個訊息的串流 rpc）將不會在收到第一則訊息之後重試。</span><span class="sxs-lookup"><span data-stu-id="8209a-140">**Server streaming**, **bidirectional streaming**: Streaming RPCs that return multiple messages from the server won't retry after the first message has been received.</span></span>
* <span data-ttu-id="8209a-141">**用戶端串流**（ **雙向串流**）：如果外寄訊息超過用戶端的緩衝區大小上限，則傳送多個訊息至伺服器的串流 rpc 將不會重試。</span><span class="sxs-lookup"><span data-stu-id="8209a-141">**Client streaming**, **bidirectional streaming**: Streaming RPCs that send multiple messages to the server won't retry if the outgoing messages have exceeded the client's maximum buffer size.</span></span>

<span data-ttu-id="8209a-142">如需詳細資訊，請參閱 [何時有效重試](#when-retries-are-valid)。</span><span class="sxs-lookup"><span data-stu-id="8209a-142">For more information, see [When retries are valid](#when-retries-are-valid).</span></span>

### <a name="grpc-retry-options"></a><span data-ttu-id="8209a-143">gRPC 重試選項</span><span class="sxs-lookup"><span data-stu-id="8209a-143">gRPC retry options</span></span>

<span data-ttu-id="8209a-144">下表說明設定 gRPC 重試原則的選項：</span><span class="sxs-lookup"><span data-stu-id="8209a-144">The following table describes options for configuring gRPC retry policies:</span></span>

| <span data-ttu-id="8209a-145">選項</span><span class="sxs-lookup"><span data-stu-id="8209a-145">Option</span></span> | <span data-ttu-id="8209a-146">Description</span><span class="sxs-lookup"><span data-stu-id="8209a-146">Description</span></span> |
| ------ | ----------- |
| `MaxAttempts` | <span data-ttu-id="8209a-147">呼叫嘗試次數的上限，包括原始嘗試。</span><span class="sxs-lookup"><span data-stu-id="8209a-147">The maximum number of call attempts, including the original attempt.</span></span> <span data-ttu-id="8209a-148">此值受限於 `GrpcChannelOptions.MaxRetryAttempts` 預設值為5。</span><span class="sxs-lookup"><span data-stu-id="8209a-148">This value is limited by `GrpcChannelOptions.MaxRetryAttempts` which defaults to 5.</span></span> <span data-ttu-id="8209a-149">值是必要的，且必須大於1。</span><span class="sxs-lookup"><span data-stu-id="8209a-149">A value is required and must be greater than 1.</span></span> |
| `InitialBackoff` | <span data-ttu-id="8209a-150">重試嘗試之間的初始輪詢延遲。</span><span class="sxs-lookup"><span data-stu-id="8209a-150">The initial backoff delay between retry attempts.</span></span> <span data-ttu-id="8209a-151">0和目前輪詢之間的隨機延遲，會決定下次重試嘗試的時間。</span><span class="sxs-lookup"><span data-stu-id="8209a-151">A randomized delay between 0 and the current backoff determines when the next retry attempt is made.</span></span> <span data-ttu-id="8209a-152">每次嘗試之後，目前的輪詢會乘以 `BackoffMultiplier` 。</span><span class="sxs-lookup"><span data-stu-id="8209a-152">After each attempt, the current backoff is multiplied by `BackoffMultiplier`.</span></span> <span data-ttu-id="8209a-153">值是必要的，而且必須大於零。</span><span class="sxs-lookup"><span data-stu-id="8209a-153">A value is required and must be greater than zero.</span></span> |
| `MaxBackoff` | <span data-ttu-id="8209a-154">最大輪詢會將指數輪詢成長的上限放在最大值。</span><span class="sxs-lookup"><span data-stu-id="8209a-154">The maximum backoff places an upper limit on exponential backoff growth.</span></span> <span data-ttu-id="8209a-155">值是必要的，而且必須大於零。</span><span class="sxs-lookup"><span data-stu-id="8209a-155">A value is required and must be greater than zero.</span></span> |
| `BackoffMultiplier` | <span data-ttu-id="8209a-156">在每次重試嘗試之後，輪詢將乘以此值，並會在乘數大於1時以指數方式增加。</span><span class="sxs-lookup"><span data-stu-id="8209a-156">The backoff will be multiplied by this value after each retry attempt and will increase exponentially when the multiplier is greater than 1.</span></span> <span data-ttu-id="8209a-157">值是必要的，而且必須大於零。</span><span class="sxs-lookup"><span data-stu-id="8209a-157">A value is required and must be greater than zero.</span></span> |
| `RetryableStatusCodes` | <span data-ttu-id="8209a-158">狀態碼的集合。</span><span class="sxs-lookup"><span data-stu-id="8209a-158">A collection of status codes.</span></span> <span data-ttu-id="8209a-159">因符合狀態而失敗的 gRPC 呼叫會自動重試。</span><span class="sxs-lookup"><span data-stu-id="8209a-159">A gRPC call that fails with a matching status will be automatically retried.</span></span> <span data-ttu-id="8209a-160">如需狀態碼的詳細資訊，請參閱 [狀態碼及其在 gRPC 中的用法](https://grpc.github.io/grpc/core/md_doc_statuscodes.html)。</span><span class="sxs-lookup"><span data-stu-id="8209a-160">For more information about status codes, see [Status codes and their use in gRPC](https://grpc.github.io/grpc/core/md_doc_statuscodes.html).</span></span> <span data-ttu-id="8209a-161">至少需要一個可重試的狀態碼。</span><span class="sxs-lookup"><span data-stu-id="8209a-161">At least one retryable status code is required.</span></span> |

## <a name="hedging"></a><span data-ttu-id="8209a-162">對沖</span><span class="sxs-lookup"><span data-stu-id="8209a-162">Hedging</span></span>

<span data-ttu-id="8209a-163">避險是替代的重試策略。</span><span class="sxs-lookup"><span data-stu-id="8209a-163">Hedging is an alternative retry strategy.</span></span> <span data-ttu-id="8209a-164">避險可主動傳送單一 gRPC 呼叫的多個複本，而不需要等待回應。</span><span class="sxs-lookup"><span data-stu-id="8209a-164">Hedging enables aggressively sending multiple copies of a single gRPC call without waiting for a response.</span></span> <span data-ttu-id="8209a-165">Hedged gRPC 呼叫可能會在伺服器上執行多次，並使用第一個成功的結果。</span><span class="sxs-lookup"><span data-stu-id="8209a-165">Hedged gRPC calls may be executed multiple times on the server and the first successful result is used.</span></span> <span data-ttu-id="8209a-166">請務必只針對可安全執行多次且不會造成負面影響的方法啟用避險。</span><span class="sxs-lookup"><span data-stu-id="8209a-166">It's important that hedging is only enabled for methods that are safe to execute multiple times without adverse effect.</span></span>

<span data-ttu-id="8209a-167">相較于重試，避險具有優缺點：</span><span class="sxs-lookup"><span data-stu-id="8209a-167">Hedging has pros and cons when compared to retries:</span></span> 

* <span data-ttu-id="8209a-168">避險的優點是，它可能會更快地傳回成功的結果。</span><span class="sxs-lookup"><span data-stu-id="8209a-168">An advantage to hedging is it might return a successful result faster.</span></span> <span data-ttu-id="8209a-169">它允許多個同時 gRPC 的呼叫，並且會在第一個成功的結果可用時完成。</span><span class="sxs-lookup"><span data-stu-id="8209a-169">It allows for multiple simultaneously gRPC calls and will complete when the first successful result is available.</span></span> 
* <span data-ttu-id="8209a-170">避險的缺點是可能會很浪費。</span><span class="sxs-lookup"><span data-stu-id="8209a-170">A disadvantage to hedging is it can be wasteful.</span></span> <span data-ttu-id="8209a-171">可能會進行多個呼叫，且全部都成功。</span><span class="sxs-lookup"><span data-stu-id="8209a-171">Multiple calls could be made and all succeed.</span></span> <span data-ttu-id="8209a-172">只會使用第一個結果，並捨棄其餘部分。</span><span class="sxs-lookup"><span data-stu-id="8209a-172">Only the first result is used and the rest are discarded.</span></span>

## <a name="configure-a-grpc-hedging-policy"></a><span data-ttu-id="8209a-173">設定 gRPC 避險原則</span><span class="sxs-lookup"><span data-stu-id="8209a-173">Configure a gRPC hedging policy</span></span>

<span data-ttu-id="8209a-174">避險原則的設定就像是重試原則。</span><span class="sxs-lookup"><span data-stu-id="8209a-174">A hedging policy is configured like a retry policy.</span></span> <span data-ttu-id="8209a-175">請注意，避險原則無法與重試原則合併。</span><span class="sxs-lookup"><span data-stu-id="8209a-175">Note that a hedging policy can't be combined with a retry policy.</span></span>

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

### <a name="grpc-hedging-options"></a><span data-ttu-id="8209a-176">gRPC 避險選項</span><span class="sxs-lookup"><span data-stu-id="8209a-176">gRPC hedging options</span></span>

<span data-ttu-id="8209a-177">下表說明設定 gRPC 避險原則的選項：</span><span class="sxs-lookup"><span data-stu-id="8209a-177">The following table describes options for configuring gRPC hedging policies:</span></span>

| <span data-ttu-id="8209a-178">選項</span><span class="sxs-lookup"><span data-stu-id="8209a-178">Option</span></span> | <span data-ttu-id="8209a-179">Description</span><span class="sxs-lookup"><span data-stu-id="8209a-179">Description</span></span> |
| ------ | ----------- |
| `MaxAttempts` | <span data-ttu-id="8209a-180">避險原則會傳送到此次數的呼叫。</span><span class="sxs-lookup"><span data-stu-id="8209a-180">The hedging policy will send up to this number of calls.</span></span> <span data-ttu-id="8209a-181">`MaxAttempts` 表示所有嘗試的總次數，包括原始嘗試。</span><span class="sxs-lookup"><span data-stu-id="8209a-181">`MaxAttempts` represents the total number of all attempts, including the original attempt.</span></span> <span data-ttu-id="8209a-182">此值受限於 `GrpcChannelOptions.MaxRetryAttempts` 預設值為5。</span><span class="sxs-lookup"><span data-stu-id="8209a-182">This value is limited by `GrpcChannelOptions.MaxRetryAttempts` which defaults to 5.</span></span> <span data-ttu-id="8209a-183">值是必要的，而且必須是2或以上。</span><span class="sxs-lookup"><span data-stu-id="8209a-183">A value is required and must be 2 or greater.</span></span> |
| `HedgingDelay` | <span data-ttu-id="8209a-184">第一個呼叫會立即傳送，但後續的避險呼叫將會被此值延遲。</span><span class="sxs-lookup"><span data-stu-id="8209a-184">The first call will be sent immediately, but the subsequent hedging calls will be delayed by this value.</span></span> <span data-ttu-id="8209a-185">當延遲設定為零或時 `null` ，會立即傳送所有 hedged 呼叫。</span><span class="sxs-lookup"><span data-stu-id="8209a-185">When the delay is set to zero or `null`, all hedged calls are sent immediately.</span></span> <span data-ttu-id="8209a-186">預設值為零。</span><span class="sxs-lookup"><span data-stu-id="8209a-186">Default value is zero.</span></span> |
| `NonFatalStatusCodes` | <span data-ttu-id="8209a-187">表示其他籬呼叫仍可能成功的狀態碼集合。</span><span class="sxs-lookup"><span data-stu-id="8209a-187">A collection of status codes which indicate other hedge calls may still succeed.</span></span> <span data-ttu-id="8209a-188">如果伺服器傳回非嚴重的狀態碼，hedged 呼叫將會繼續。</span><span class="sxs-lookup"><span data-stu-id="8209a-188">If a non-fatal status code is returned by the server, hedged calls will continue.</span></span> <span data-ttu-id="8209a-189">否則，將會取消未完成的要求，並將錯誤傳回給應用程式。</span><span class="sxs-lookup"><span data-stu-id="8209a-189">Otherwise, outstanding requests will be canceled and the error returned to the app.</span></span> <span data-ttu-id="8209a-190">如需狀態碼的詳細資訊，請參閱 [狀態碼及其在 gRPC 中的用法](https://grpc.github.io/grpc/core/md_doc_statuscodes.html)。</span><span class="sxs-lookup"><span data-stu-id="8209a-190">For more information about status codes, see [Status codes and their use in gRPC](https://grpc.github.io/grpc/core/md_doc_statuscodes.html).</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="8209a-191">其他資源</span><span class="sxs-lookup"><span data-stu-id="8209a-191">Additional resources</span></span>

* <xref:grpc/client>
* [<span data-ttu-id="8209a-192">重試一般指引-雲端應用程式的最佳作法</span><span class="sxs-lookup"><span data-stu-id="8209a-192">Retry general guidance - Best practices for cloud applications</span></span>](/azure/architecture/best-practices/transient-faults)
