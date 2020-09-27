---
title: 在 ASP.NET Core 中使用串流 SignalR
author: bradygaster
description: 瞭解如何在用戶端與伺服器之間串流資料。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/streaming
ms.openlocfilehash: 5a172818f8910a637b731dc1b1315965f448b2ba
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393570"
---
# <a name="use-streaming-in-aspnet-core-no-locsignalr"></a><span data-ttu-id="296be-103">在 ASP.NET Core 中使用串流 SignalR</span><span class="sxs-lookup"><span data-stu-id="296be-103">Use streaming in ASP.NET Core SignalR</span></span>

<span data-ttu-id="296be-104">依 [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="296be-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="296be-105">ASP.NET Core SignalR 支援從用戶端到伺服器，以及從伺服器到用戶端的串流。</span><span class="sxs-lookup"><span data-stu-id="296be-105">ASP.NET Core SignalR supports streaming from client to server and from server to client.</span></span> <span data-ttu-id="296be-106">這適用于資料片段隨時間抵達的案例。</span><span class="sxs-lookup"><span data-stu-id="296be-106">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="296be-107">串流處理時，每個片段會在用戶端或伺服器變成可用時立即傳送給用戶端或伺服器，而不是等候所有資料變成可用。</span><span class="sxs-lookup"><span data-stu-id="296be-107">When streaming, each fragment is sent to the client or server as soon as it becomes available, rather than waiting for all of the data to become available.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="296be-108">ASP.NET Core SignalR 支援伺服器方法的資料流程傳回值。</span><span class="sxs-lookup"><span data-stu-id="296be-108">ASP.NET Core SignalR supports streaming return values of server methods.</span></span> <span data-ttu-id="296be-109">這適用于資料片段隨時間抵達的案例。</span><span class="sxs-lookup"><span data-stu-id="296be-109">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="296be-110">當傳回值串流至用戶端時，每個片段都會在它變成可用時立即傳送給用戶端，而不是等候所有資料變成可用。</span><span class="sxs-lookup"><span data-stu-id="296be-110">When a return value is streamed to the client, each fragment is sent to the client as soon as it becomes available, rather than waiting for all the data to become available.</span></span>

::: moniker-end

<span data-ttu-id="296be-111">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="296be-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="set-up-a-hub-for-streaming"></a><span data-ttu-id="296be-112">設定用於串流處理的中樞</span><span class="sxs-lookup"><span data-stu-id="296be-112">Set up a hub for streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="296be-113">當中樞方法傳回 <xref:System.Collections.Generic.IAsyncEnumerable`1> 、、或時，它會自動成為串流中樞方法 <xref:System.Threading.Channels.ChannelReader%601> `Task<IAsyncEnumerable<T>>` `Task<ChannelReader<T>>` 。</span><span class="sxs-lookup"><span data-stu-id="296be-113">A hub method automatically becomes a streaming hub method when it returns <xref:System.Collections.Generic.IAsyncEnumerable`1>, <xref:System.Threading.Channels.ChannelReader%601>, `Task<IAsyncEnumerable<T>>`, or `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="296be-114">中樞方法會在傳回或時自動成為串流中樞方法 <xref:System.Threading.Channels.ChannelReader%601> `Task<ChannelReader<T>>` 。</span><span class="sxs-lookup"><span data-stu-id="296be-114">A hub method automatically becomes a streaming hub method when it returns a <xref:System.Threading.Channels.ChannelReader%601> or a `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

### <a name="server-to-client-streaming"></a><span data-ttu-id="296be-115">伺服器對用戶端串流</span><span class="sxs-lookup"><span data-stu-id="296be-115">Server-to-client streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="296be-116">除了之外，串流中樞方法還可以傳回 `IAsyncEnumerable<T>` `ChannelReader<T>` 。</span><span class="sxs-lookup"><span data-stu-id="296be-116">Streaming hub methods can return `IAsyncEnumerable<T>` in addition to `ChannelReader<T>`.</span></span> <span data-ttu-id="296be-117">最簡單的傳回方式 `IAsyncEnumerable<T>` 是讓中樞方法成為非同步反覆運算器方法，如下列範例所示。</span><span class="sxs-lookup"><span data-stu-id="296be-117">The simplest way to return `IAsyncEnumerable<T>` is by making the hub method an async iterator method as the following sample demonstrates.</span></span> <span data-ttu-id="296be-118">中樞非同步反覆運算器方法可以接受 `CancellationToken` 用戶端從資料流程取消訂閱時所觸發的參數。</span><span class="sxs-lookup"><span data-stu-id="296be-118">Hub async iterator methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="296be-119">非同步反覆運算器方法會避免通道常見的問題，例如，不 `ChannelReader` 會在未完成的情況下傳回足夠的早期或結束方法 <xref:System.Threading.Channels.ChannelWriter`1> 。</span><span class="sxs-lookup"><span data-stu-id="296be-119">Async iterator methods avoid problems common with Channels, such as not returning the `ChannelReader` early enough or exiting the method without completing the <xref:System.Threading.Channels.ChannelWriter`1>.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

<span data-ttu-id="296be-120">下列範例顯示使用通道將資料串流至用戶端的基本概念。</span><span class="sxs-lookup"><span data-stu-id="296be-120">The following sample shows the basics of streaming data to the client using Channels.</span></span> <span data-ttu-id="296be-121">每當物件寫入時 <xref:System.Threading.Channels.ChannelWriter%601> ，物件都會立即傳送至用戶端。</span><span class="sxs-lookup"><span data-stu-id="296be-121">Whenever an object is written to the <xref:System.Threading.Channels.ChannelWriter%601>, the object is immediately sent to the client.</span></span> <span data-ttu-id="296be-122">最後， `ChannelWriter` 會完成，告知用戶端資料流程已關閉。</span><span class="sxs-lookup"><span data-stu-id="296be-122">At the end, the `ChannelWriter` is completed to tell the client the stream is closed.</span></span>

> [!NOTE]
> <span data-ttu-id="296be-123">`ChannelWriter<T>`在背景執行緒上寫入，並 `ChannelReader` 儘快傳回。</span><span class="sxs-lookup"><span data-stu-id="296be-123">Write to the `ChannelWriter<T>` on a background thread and return the `ChannelReader` as soon as possible.</span></span> <span data-ttu-id="296be-124">除非傳回，否則會封鎖其他中樞調用 `ChannelReader` 。</span><span class="sxs-lookup"><span data-stu-id="296be-124">Other hub invocations are blocked until a `ChannelReader` is returned.</span></span>
>
> <span data-ttu-id="296be-125">將邏輯包裝在[ `try ... catch` 語句](/dotnet/csharp/language-reference/keywords/try-catch)中。</span><span class="sxs-lookup"><span data-stu-id="296be-125">Wrap logic in a [`try ... catch` statement](/dotnet/csharp/language-reference/keywords/try-catch).</span></span> <span data-ttu-id="296be-126">`Channel`在[ `finally` 區塊](/dotnet/csharp/language-reference/keywords/try-catch-finally)中完成。</span><span class="sxs-lookup"><span data-stu-id="296be-126">Complete the `Channel` in a [`finally` block](/dotnet/csharp/language-reference/keywords/try-catch-finally).</span></span> <span data-ttu-id="296be-127">如果您想要傳送錯誤，請在區塊內部捕捉， `catch` 然後將它寫入 `finally` 區塊中。</span><span class="sxs-lookup"><span data-stu-id="296be-127">If you want to flow an error, capture it inside the `catch` block and write it in the `finally` block.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Streaming hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[Streaming hub method](streaming/samples/2.2/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[Streaming hub method](streaming/samples/2.1/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="296be-128">伺服器對用戶端串流中樞方法可以接受 `CancellationToken` 用戶端從資料流程取消訂閱時所觸發的參數。</span><span class="sxs-lookup"><span data-stu-id="296be-128">Server-to-client streaming hub methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="296be-129">如果用戶端在串流結束之前中斷連線，請使用此權杖來停止伺服器作業並釋放任何資源。</span><span class="sxs-lookup"><span data-stu-id="296be-129">Use this token to stop the server operation and release any resources if the client disconnects before the end of the stream.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="296be-130">用戶端對伺服器串流</span><span class="sxs-lookup"><span data-stu-id="296be-130">Client-to-server streaming</span></span>

<span data-ttu-id="296be-131">當中樞方法接受一或多個類型或的物件時，它會自動成為用戶端對伺服器的串流中樞方法 <xref:System.Threading.Channels.ChannelReader%601> <xref:System.Collections.Generic.IAsyncEnumerable%601> 。</span><span class="sxs-lookup"><span data-stu-id="296be-131">A hub method automatically becomes a client-to-server streaming hub method when it accepts one or more objects of type <xref:System.Threading.Channels.ChannelReader%601> or <xref:System.Collections.Generic.IAsyncEnumerable%601>.</span></span> <span data-ttu-id="296be-132">下列範例顯示讀取從用戶端傳送之串流資料的基本概念。</span><span class="sxs-lookup"><span data-stu-id="296be-132">The following sample shows the basics of reading streaming data sent from the client.</span></span> <span data-ttu-id="296be-133">每當用戶端寫入時 <xref:System.Threading.Channels.ChannelWriter%601> ，就會將資料寫入至 `ChannelReader` 中樞方法所讀取之伺服器上的。</span><span class="sxs-lookup"><span data-stu-id="296be-133">Whenever the client writes to the <xref:System.Threading.Channels.ChannelWriter%601>, the data is written into the `ChannelReader` on the server from which the hub method is reading.</span></span>

[!code-csharp[Streaming upload hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet2)]

<span data-ttu-id="296be-134"><xref:System.Collections.Generic.IAsyncEnumerable%601>方法的版本如下。</span><span class="sxs-lookup"><span data-stu-id="296be-134">An <xref:System.Collections.Generic.IAsyncEnumerable%601> version of the method follows.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        Console.WriteLine(item);
    }
}
```

::: moniker-end

## <a name="net-client"></a><span data-ttu-id="296be-135">.NET 用戶端</span><span class="sxs-lookup"><span data-stu-id="296be-135">.NET client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="296be-136">伺服器對用戶端串流</span><span class="sxs-lookup"><span data-stu-id="296be-136">Server-to-client streaming</span></span>


::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="296be-137">`StreamAsync`和 `StreamAsChannelAsync` 上的方法 `HubConnection` 是用來叫用伺服器對用戶端資料流程方法。</span><span class="sxs-lookup"><span data-stu-id="296be-137">The `StreamAsync` and `StreamAsChannelAsync` methods on `HubConnection` are used to invoke server-to-client streaming methods.</span></span> <span data-ttu-id="296be-138">將中樞方法中定義的中樞方法名稱和引數傳遞至 `StreamAsync` 或 `StreamAsChannelAsync` 。</span><span class="sxs-lookup"><span data-stu-id="296be-138">Pass the hub method name and arguments defined in the hub method to `StreamAsync` or `StreamAsChannelAsync`.</span></span> <span data-ttu-id="296be-139">上的泛型參數 `StreamAsync<T>` 會 `StreamAsChannelAsync<T>` 指定串流方法所傳回的物件類型。</span><span class="sxs-lookup"><span data-stu-id="296be-139">The generic parameter on `StreamAsync<T>` and `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="296be-140">`IAsyncEnumerable<T>` `ChannelReader<T>` 從資料流程調用傳回類型或的物件，並代表用戶端上的資料流程。</span><span class="sxs-lookup"><span data-stu-id="296be-140">An object of type `IAsyncEnumerable<T>` or `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

<span data-ttu-id="296be-141">傳回 `StreamAsync` 的範例 `IAsyncEnumerable<int>` ：</span><span class="sxs-lookup"><span data-stu-id="296be-141">A `StreamAsync` example that returns `IAsyncEnumerable<int>`:</span></span>

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var stream = await hubConnection.StreamAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

await foreach (var count in stream)
{
    Console.WriteLine($"{count}");
}

Console.WriteLine("Streaming completed");
```

<span data-ttu-id="296be-142">傳回 `StreamAsChannelAsync` 的對應範例 `ChannelReader<int>` ：</span><span class="sxs-lookup"><span data-stu-id="296be-142">A corresponding `StreamAsChannelAsync` example that returns `ChannelReader<int>`:</span></span>

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var channel = await hubConnection.StreamAsChannelAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="296be-143">`StreamAsChannelAsync`上的方法 `HubConnection` 是用來叫用伺服器對用戶端串流方法。</span><span class="sxs-lookup"><span data-stu-id="296be-143">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="296be-144">將中樞方法中定義的中樞方法名稱和引數傳遞至 `StreamAsChannelAsync` 。</span><span class="sxs-lookup"><span data-stu-id="296be-144">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="296be-145">上的泛型參數會 `StreamAsChannelAsync<T>` 指定串流方法所傳回的物件類型。</span><span class="sxs-lookup"><span data-stu-id="296be-145">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="296be-146">`ChannelReader<T>`從資料流程調用傳回，並表示用戶端上的資料流程。</span><span class="sxs-lookup"><span data-stu-id="296be-146">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var channel = await hubConnection.StreamAsChannelAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="296be-147">`StreamAsChannelAsync`上的方法 `HubConnection` 是用來叫用伺服器對用戶端串流方法。</span><span class="sxs-lookup"><span data-stu-id="296be-147">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="296be-148">將中樞方法中定義的中樞方法名稱和引數傳遞至 `StreamAsChannelAsync` 。</span><span class="sxs-lookup"><span data-stu-id="296be-148">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="296be-149">上的泛型參數會 `StreamAsChannelAsync<T>` 指定串流方法所傳回的物件類型。</span><span class="sxs-lookup"><span data-stu-id="296be-149">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="296be-150">`ChannelReader<T>`從資料流程調用傳回，並表示用戶端上的資料流程。</span><span class="sxs-lookup"><span data-stu-id="296be-150">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

```csharp
var channel = await hubConnection
    .StreamAsChannelAsync<int>("Counter", 10, 500, CancellationToken.None);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="296be-151">用戶端對伺服器串流</span><span class="sxs-lookup"><span data-stu-id="296be-151">Client-to-server streaming</span></span>

<span data-ttu-id="296be-152">從 .NET 用戶端叫用用戶端伺服器串流中樞方法的方法有兩種。</span><span class="sxs-lookup"><span data-stu-id="296be-152">There are two ways to invoke a client-to-server streaming hub method from the .NET client.</span></span> <span data-ttu-id="296be-153">您可以 `IAsyncEnumerable<T>` `ChannelReader` `SendAsync` 根據所叫用 `InvokeAsync` 的中樞方法，將或當作引數傳遞至、或 `StreamAsChannelAsync` 。</span><span class="sxs-lookup"><span data-stu-id="296be-153">You can either pass in an `IAsyncEnumerable<T>` or a `ChannelReader` as an argument to `SendAsync`, `InvokeAsync`, or `StreamAsChannelAsync`, depending on the hub method invoked.</span></span>

<span data-ttu-id="296be-154">每次將資料寫入 `IAsyncEnumerable` 或 `ChannelWriter` 物件時，伺服器上的中樞方法都會接收來自用戶端之資料的新專案。</span><span class="sxs-lookup"><span data-stu-id="296be-154">Whenever data is written to the `IAsyncEnumerable` or `ChannelWriter` object, the hub method on the server receives a new item with the data from the client.</span></span>

<span data-ttu-id="296be-155">如果使用 `IAsyncEnumerable` 物件，資料流程會在傳回資料流程專案的方法結束後結束。</span><span class="sxs-lookup"><span data-stu-id="296be-155">If using an `IAsyncEnumerable` object, the stream ends after the method returning stream items exits.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
    //After the for loop has completed and the local function exits the stream completion will be sent.
}

await connection.SendAsync("UploadStream", clientStreamData());
```

<span data-ttu-id="296be-156">或者，如果您使用 `ChannelWriter` ，則會使用下列方式來完成通道 `channel.Writer.Complete()` ：</span><span class="sxs-lookup"><span data-stu-id="296be-156">Or if you're using a `ChannelWriter`, you complete the channel with `channel.Writer.Complete()`:</span></span>

```csharp
var channel = Channel.CreateBounded<string>(10);
await connection.SendAsync("UploadStream", channel.Reader);
await channel.Writer.WriteAsync("some data");
await channel.Writer.WriteAsync("some more data");
channel.Writer.Complete();
```

::: moniker-end

## <a name="javascript-client"></a><span data-ttu-id="296be-157">JavaScript 用戶端</span><span class="sxs-lookup"><span data-stu-id="296be-157">JavaScript client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="296be-158">伺服器對用戶端串流</span><span class="sxs-lookup"><span data-stu-id="296be-158">Server-to-client streaming</span></span>

<span data-ttu-id="296be-159">JavaScript 用戶端會使用來呼叫中樞上的伺服器對用戶端串流方法 `connection.stream` 。</span><span class="sxs-lookup"><span data-stu-id="296be-159">JavaScript clients call server-to-client streaming methods on hubs with `connection.stream`.</span></span> <span data-ttu-id="296be-160">`stream`方法會接受兩個引數：</span><span class="sxs-lookup"><span data-stu-id="296be-160">The `stream` method accepts two arguments:</span></span>

* <span data-ttu-id="296be-161">中樞方法的名稱。</span><span class="sxs-lookup"><span data-stu-id="296be-161">The name of the hub method.</span></span> <span data-ttu-id="296be-162">在下列範例中，中樞方法名稱為 `Counter` 。</span><span class="sxs-lookup"><span data-stu-id="296be-162">In the following example, the hub method name is `Counter`.</span></span>
* <span data-ttu-id="296be-163">在中樞方法中定義的引數。</span><span class="sxs-lookup"><span data-stu-id="296be-163">Arguments defined in the hub method.</span></span> <span data-ttu-id="296be-164">在下列範例中，引數是要接收的資料流程專案數目以及資料流程專案之間的延遲計數。</span><span class="sxs-lookup"><span data-stu-id="296be-164">In the following example, the arguments are a count for the number of stream items to receive and the delay between stream items.</span></span>

<span data-ttu-id="296be-165">`connection.stream` 傳回 `IStreamResult` ，其中包含 `subscribe` 方法。</span><span class="sxs-lookup"><span data-stu-id="296be-165">`connection.stream` returns an `IStreamResult`, which contains a `subscribe` method.</span></span> <span data-ttu-id="296be-166">傳遞 `IStreamSubscriber` 至 `subscribe` ，並設定 `next` 、 `error` 和回呼， `complete` 以接收來自調用的通知 `stream` 。</span><span class="sxs-lookup"><span data-stu-id="296be-166">Pass an `IStreamSubscriber` to `subscribe` and set the `next`, `error`, and `complete` callbacks to receive notifications from the `stream` invocation.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="296be-167">若要從用戶端結束資料流程，請 `dispose` 在方法傳回的上呼叫方法 `ISubscription` `subscribe` 。</span><span class="sxs-lookup"><span data-stu-id="296be-167">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span> <span data-ttu-id="296be-168">`CancellationToken`如果您提供了中樞方法的參數，呼叫這個方法將會取消該方法的參數。</span><span class="sxs-lookup"><span data-stu-id="296be-168">Calling this method causes cancellation of the `CancellationToken` parameter of the Hub method, if you provided one.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="296be-169">若要從用戶端結束資料流程，請 `dispose` 在方法傳回的上呼叫方法 `ISubscription` `subscribe` 。</span><span class="sxs-lookup"><span data-stu-id="296be-169">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="296be-170">用戶端對伺服器串流</span><span class="sxs-lookup"><span data-stu-id="296be-170">Client-to-server streaming</span></span>

<span data-ttu-id="296be-171">JavaScript 用戶端會在中樞上呼叫用戶端對伺服器串流方法，方法是將 `Subject` 做為引數傳遞至 `send` 、 `invoke` 或 `stream` ，視所叫用的中樞方法而定。</span><span class="sxs-lookup"><span data-stu-id="296be-171">JavaScript clients call client-to-server streaming methods on hubs by passing in a `Subject` as an argument to `send`, `invoke`, or `stream`, depending on the hub method invoked.</span></span> <span data-ttu-id="296be-172">`Subject`是看起來像的類別 `Subject` 。</span><span class="sxs-lookup"><span data-stu-id="296be-172">The `Subject` is a class that looks like a `Subject`.</span></span> <span data-ttu-id="296be-173">例如，在 RxJS 中，您可以使用該程式庫中的 [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) 類別。</span><span class="sxs-lookup"><span data-stu-id="296be-173">For example in RxJS, you can use the [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) class from that library.</span></span>

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

<span data-ttu-id="296be-174">使用專案呼叫會將 `subject.next(item)` 專案寫入資料流程，而 hub 方法會接收伺服器上的專案。</span><span class="sxs-lookup"><span data-stu-id="296be-174">Calling `subject.next(item)` with an item writes the item to the stream, and the hub method receives the item on the server.</span></span>

<span data-ttu-id="296be-175">若要結束資料流程，請呼叫 `subject.complete()` 。</span><span class="sxs-lookup"><span data-stu-id="296be-175">To end the stream, call `subject.complete()`.</span></span>

## <a name="java-client"></a><span data-ttu-id="296be-176">Java 用戶端</span><span class="sxs-lookup"><span data-stu-id="296be-176">Java client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="296be-177">伺服器對用戶端串流</span><span class="sxs-lookup"><span data-stu-id="296be-177">Server-to-client streaming</span></span>

<span data-ttu-id="296be-178">SignalRJAVA 用戶端會使用 `stream` 方法來叫用資料流程方法。</span><span class="sxs-lookup"><span data-stu-id="296be-178">The SignalR Java client uses the `stream` method to invoke streaming methods.</span></span> <span data-ttu-id="296be-179">`stream` 接受三個或多個引數：</span><span class="sxs-lookup"><span data-stu-id="296be-179">`stream` accepts three or more arguments:</span></span>

* <span data-ttu-id="296be-180">資料流程專案的預期類型。</span><span class="sxs-lookup"><span data-stu-id="296be-180">The expected type of the stream items.</span></span>
* <span data-ttu-id="296be-181">中樞方法的名稱。</span><span class="sxs-lookup"><span data-stu-id="296be-181">The name of the hub method.</span></span>
* <span data-ttu-id="296be-182">在中樞方法中定義的引數。</span><span class="sxs-lookup"><span data-stu-id="296be-182">Arguments defined in the hub method.</span></span>

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

<span data-ttu-id="296be-183">上的方法會傳回 `stream` `HubConnection` 資料流程專案類型的可觀察專案。</span><span class="sxs-lookup"><span data-stu-id="296be-183">The `stream` method on `HubConnection` returns an Observable of the stream item type.</span></span> <span data-ttu-id="296be-184">可觀察型別的 `subscribe` 方法是在其中 `onNext` `onError` `onCompleted` 定義和處理常式。</span><span class="sxs-lookup"><span data-stu-id="296be-184">The Observable type's `subscribe` method is where `onNext`, `onError` and `onCompleted` handlers are defined.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="296be-185">其他資源</span><span class="sxs-lookup"><span data-stu-id="296be-185">Additional resources</span></span>

* [<span data-ttu-id="296be-186">中樞</span><span class="sxs-lookup"><span data-stu-id="296be-186">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="296be-187">.NET 用戶端</span><span class="sxs-lookup"><span data-stu-id="296be-187">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="296be-188">JavaScript 用戶端</span><span class="sxs-lookup"><span data-stu-id="296be-188">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="296be-189">發佈至 Azure</span><span class="sxs-lookup"><span data-stu-id="296be-189">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
