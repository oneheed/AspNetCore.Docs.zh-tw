---
title: ASP.NET Core SignalR .Net 用戶端
author: bradygaster
description: ASP.NET Core SignalR .Net 用戶端的相關資訊
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/14/2020
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
uid: signalr/dotnet-client
ms.openlocfilehash: f56beb634689b42aec3fe193c389e52de746c92a
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634003"
---
# <a name="aspnet-core-no-locsignalr-net-client"></a><span data-ttu-id="0b2c7-103">ASP.NET Core SignalR .Net 用戶端</span><span class="sxs-lookup"><span data-stu-id="0b2c7-103">ASP.NET Core SignalR .NET Client</span></span>

<span data-ttu-id="0b2c7-104">ASP.NET Core SignalR .net 用戶端程式庫可讓您 SignalR 從 .net 應用程式與中樞進行通訊。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-104">The ASP.NET Core SignalR .NET client library lets you communicate with SignalR hubs from .NET apps.</span></span>

<span data-ttu-id="0b2c7-105">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="0b2c7-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0b2c7-106">本文中的程式碼範例是使用 ASP.NET Core .Net 用戶端的 WPF 應用程式 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-106">The code sample in this article is a WPF app that uses the ASP.NET Core SignalR .NET client.</span></span>

## <a name="install-the-no-locsignalr-net-client-package"></a><span data-ttu-id="0b2c7-107">安裝 SignalR .net 用戶端套件</span><span class="sxs-lookup"><span data-stu-id="0b2c7-107">Install the SignalR .NET client package</span></span>

<span data-ttu-id="0b2c7-108">[AspNetCore。 SignalR需要用戶端](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client)套件才能讓 .net 用戶端連線到 SignalR 中樞。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-108">The [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package is required for .NET clients to connect to SignalR hubs.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0b2c7-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0b2c7-109">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0b2c7-110">若要安裝用戶端程式庫，請在 **封裝管理員主控台** 視窗中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="0b2c7-110">To install the client library, run the following command in the **Package Manager Console** window:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

# <a name="net-core-cli"></a>[<span data-ttu-id="0b2c7-111">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="0b2c7-111">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="0b2c7-112">若要安裝用戶端程式庫，請在命令 shell 中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="0b2c7-112">To install the client library, run the following command in a command shell:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="connect-to-a-hub"></a><span data-ttu-id="0b2c7-113">連接至中樞</span><span class="sxs-lookup"><span data-stu-id="0b2c7-113">Connect to a hub</span></span>

<span data-ttu-id="0b2c7-114">若要建立連接，請建立 `HubConnectionBuilder` 和呼叫 `Build` 。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-114">To establish a connection, create a `HubConnectionBuilder` and call `Build`.</span></span> <span data-ttu-id="0b2c7-115">建立連接時，可以設定中樞 URL、通訊協定、傳輸類型、記錄層級、標頭和其他選項。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-115">The hub URL, protocol, transport type, log level, headers, and other options can be configured while building a connection.</span></span> <span data-ttu-id="0b2c7-116">藉由將任何方法插入，來設定任何必要的選項 `HubConnectionBuilder` `Build` 。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-116">Configure any required options by inserting any of the `HubConnectionBuilder` methods into `Build`.</span></span> <span data-ttu-id="0b2c7-117">開始與的連接 `StartAsync` 。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-117">Start the connection with `StartAsync`.</span></span>

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a><span data-ttu-id="0b2c7-118">處理遺失的連接</span><span class="sxs-lookup"><span data-stu-id="0b2c7-118">Handle lost connection</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="0b2c7-119">自動重新連線</span><span class="sxs-lookup"><span data-stu-id="0b2c7-119">Automatically reconnect</span></span>

<span data-ttu-id="0b2c7-120"><xref:Microsoft.AspNetCore.SignalR.Client.HubConnection>可以設定為使用上的方法自動重新連接 `WithAutomaticReconnect` <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> 。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-120">The <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection> can be configured to automatically reconnect using the `WithAutomaticReconnect` method on the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="0b2c7-121">它預設不會自動重新連線。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-121">It won't automatically reconnect by default.</span></span>

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect()
    .Build();
```

<span data-ttu-id="0b2c7-122">如果沒有任何參數， `WithAutomaticReconnect()` 則會先將用戶端設定為等候0、2、10和30秒，然後再嘗試每次嘗試重新連線，並在嘗試四次失敗之後停止。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-122">Without any parameters, `WithAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="0b2c7-123">在開始進行任何重新連接嘗試之前， `HubConnection` 會轉換成 `HubConnectionState.Reconnecting` 狀態並引發 `Reconnecting` 事件。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-123">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire the `Reconnecting` event.</span></span>  <span data-ttu-id="0b2c7-124">這可讓使用者警告使用者連接已遺失，並停用 UI 元素。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-124">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span> <span data-ttu-id="0b2c7-125">非互動式應用程式可以開始將訊息排入佇列或卸載。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-125">Non-interactive apps can start queuing or dropping messages.</span></span>

```csharp
connection.Reconnecting += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Reconnecting);

    // Notify users the connection was lost and the client is reconnecting.
    // Start queuing or dropping messages.

    return Task.CompletedTask;
};
```

<span data-ttu-id="0b2c7-126">如果用戶端在前四次嘗試中成功重新連接，則 `HubConnection` 會轉換回 `Connected` 狀態並引發 `Reconnected` 事件。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-126">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire the `Reconnected` event.</span></span> <span data-ttu-id="0b2c7-127">這讓您有機會通知使用者已重新建立連線，並將任何佇列的訊息清除佇列。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-127">This provides an opportunity to inform users the connection has been reestablished and dequeue any queued messages.</span></span>

<span data-ttu-id="0b2c7-128">由於連接完全是伺服器的新專案，因此 `ConnectionId` 會提供新的 `Reconnected` 事件處理常式。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-128">Since the connection looks entirely new to the server, a new `ConnectionId` will be provided to the `Reconnected` event handlers.</span></span>

> [!WARNING]
> <span data-ttu-id="0b2c7-129">`Reconnected` `connectionId` 如果 `HubConnection` 設定為[略過協商](xref:signalr/configuration#configure-client-options)，事件處理常式的參數將會是 null。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-129">The `Reconnected` event handler's `connectionId` parameter will be null if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```csharp
connection.Reconnected += connectionId =>
{
    Debug.Assert(connection.State == HubConnectionState.Connected);

    // Notify users the connection was reestablished.
    // Start dequeuing messages queued while reconnecting if any.

    return Task.CompletedTask;
};
```

<span data-ttu-id="0b2c7-130">`WithAutomaticReconnect()` 將不會設定 `HubConnection` 重試初始啟動失敗，因此必須手動處理啟動失敗：</span><span class="sxs-lookup"><span data-stu-id="0b2c7-130">`WithAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

```csharp
public static async Task<bool> ConnectWithRetryAsync(HubConnection connection, CancellationToken token)
{
    // Keep trying to until we can start or the token is canceled.
    while (true)
    {
        try
        {
            await connection.StartAsync(token);
            Debug.Assert(connection.State == HubConnectionState.Connected);
            return true;
        }
        catch when (token.IsCancellationRequested)
        {
            return false;
        }
        catch
        {
            // Failed to connect, trying again in 5000 ms.
            Debug.Assert(connection.State == HubConnectionState.Disconnected);
            await Task.Delay(5000);
        }
    }
}
```

<span data-ttu-id="0b2c7-131">如果用戶端在前四次嘗試中未成功重新連接，則 `HubConnection` 會轉換為 `Disconnected` 狀態並引發 <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> 事件。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-131">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event.</span></span> <span data-ttu-id="0b2c7-132">這會讓您有機會嘗試手動重新開機連線，或通知使用者連接已永久遺失。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-132">This provides an opportunity to attempt to restart the connection manually or inform users the connection has been permanently lost.</span></span>

```csharp
connection.Closed += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Disconnected);

    // Notify users the connection has been closed or manually try to restart the connection.

    return Task.CompletedTask;
};
```

<span data-ttu-id="0b2c7-133">若要在中斷連線或變更重新連線時間之前設定自訂的重新連接嘗試次數，請 `WithAutomaticReconnect` 接受代表延遲的數位陣列，以毫秒為單位來開始每次重新連線嘗試。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-133">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `WithAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.Zero, TimeSpan.FromSeconds(10) })
    .Build();

    // .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.FromSeconds(2), TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(30) }) yields the default behavior.
```

<span data-ttu-id="0b2c7-134">上述範例會將設定 `HubConnection` 為在連接遺失之後立即開始嘗試重新連接。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-134">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="0b2c7-135">這也適用于預設設定。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-135">This is also true for the default configuration.</span></span>

<span data-ttu-id="0b2c7-136">如果第一次重新連線嘗試失敗，第二次重新連線嘗試也會立即開始，而不是等候2秒，就像在預設設定中一樣。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-136">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="0b2c7-137">如果第二次重新連線嘗試失敗，第三次重新連線嘗試將會在10秒內啟動，這同樣就像是預設設定。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-137">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="0b2c7-138">然後，自訂行為會在第三次重新連線嘗試失敗之後停止，然後從預設行為再次分歧。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-138">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure.</span></span> <span data-ttu-id="0b2c7-139">在預設設定中，會在另一個30秒內重新連線嘗試。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-139">In the default configuration there would be one more reconnect attempt in another 30 seconds.</span></span>

<span data-ttu-id="0b2c7-140">如果您想要更充分掌控自動重新連線嘗試的時間和數目，請 `WithAutomaticReconnect` 接受執行介面的物件 `IRetryPolicy` ，該介面具有名為的單一方法 `NextRetryDelay` 。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-140">If you want even more control over the timing and number of automatic reconnect attempts, `WithAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `NextRetryDelay`.</span></span>

<span data-ttu-id="0b2c7-141">`NextRetryDelay` 採用具有類型的單一引數 `RetryContext` 。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-141">`NextRetryDelay` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="0b2c7-142">`RetryContext`有三個屬性： `PreviousRetryCount` 、 `ElapsedTime` 和 `RetryReason` ， `long` 分別為、和 `TimeSpan` `Exception` 。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-142">The `RetryContext` has three properties: `PreviousRetryCount`, `ElapsedTime` and `RetryReason`, which are a `long`, a `TimeSpan` and an `Exception` respectively.</span></span> <span data-ttu-id="0b2c7-143">第一次重新連線嘗試之前， `PreviousRetryCount` 和都 `ElapsedTime` 是零，而且 `RetryReason` 會是導致連接遺失的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-143">Before the first reconnect attempt, both `PreviousRetryCount` and `ElapsedTime` will be zero, and the `RetryReason` will be the Exception that caused the connection to be lost.</span></span> <span data-ttu-id="0b2c7-144">每次重試嘗試失敗之後，將會 `PreviousRetryCount` 遞增一次， `ElapsedTime` 將會更新以反映到目前為止所花的時間，而且 `RetryReason` 將會是造成上次重新連線嘗試失敗的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-144">After each failed retry attempt, `PreviousRetryCount` will be incremented by one, `ElapsedTime` will be updated to reflect the amount of time spent reconnecting so far, and the `RetryReason` will be the Exception that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="0b2c7-145">`NextRetryDelay` 必須傳回代表下次重新連線嘗試之前等候時間的 TimeSpan，或 `null` 是否 `HubConnection` 應該停止重新連接。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-145">`NextRetryDelay` must return either a TimeSpan representing the time to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

```csharp
public class RandomRetryPolicy : IRetryPolicy
{
    private readonly Random _random = new Random();

    public TimeSpan? NextRetryDelay(RetryContext retryContext)
    {
        // If we've been reconnecting for less than 60 seconds so far,
        // wait between 0 and 10 seconds before the next reconnect attempt.
        if (retryContext.ElapsedTime < TimeSpan.FromSeconds(60))
        {
            return TimeSpan.FromSeconds(_random.NextDouble() * 10);
        }
        else
        {
            // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
            return null;
        }
    }
}
```

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect(new RandomRetryPolicy())
    .Build();
```

<span data-ttu-id="0b2c7-146">或者，您也可以撰寫程式碼，以手動方式重新連接用戶端，如 [手動重新](#manually-reconnect)連線中所示範。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-146">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="0b2c7-147">手動重新連線</span><span class="sxs-lookup"><span data-stu-id="0b2c7-147">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="0b2c7-148">在3.0 之前，的 .NET 用戶端 SignalR 不會自動重新連線。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-148">Prior to 3.0, the .NET client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="0b2c7-149">您必須撰寫會以手動方式重新連接用戶端的程式碼。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-149">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="0b2c7-150">使用 <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> 事件來回應遺失的連接。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-150">Use the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event to respond to a lost connection.</span></span> <span data-ttu-id="0b2c7-151">例如，您可能會想要自動重新連接。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-151">For example, you might want to automate reconnection.</span></span>

<span data-ttu-id="0b2c7-152">此 `Closed` 事件需要一個傳回的委派 `Task` ，讓非同步程式碼在不使用的情況下執行 `async void` 。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-152">The `Closed` event requires a delegate that returns a `Task`, which allows async code to run without using `async void`.</span></span> <span data-ttu-id="0b2c7-153">若要在以同步方式執行的事件處理常式中滿足委派簽章 `Closed` ，請傳回 `Task.CompletedTask` ：</span><span class="sxs-lookup"><span data-stu-id="0b2c7-153">To satisfy the delegate signature in a `Closed` event handler that runs synchronously, return `Task.CompletedTask`:</span></span>

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

<span data-ttu-id="0b2c7-154">非同步支援的主要原因是，您可以重新開機連接。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-154">The main reason for the async support is so you can restart the connection.</span></span> <span data-ttu-id="0b2c7-155">啟動連接是非同步動作。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-155">Starting a connection is an async action.</span></span>

<span data-ttu-id="0b2c7-156">在 `Closed` 重新開機連接的處理常式中，請考慮等待一些隨機延遲，以避免伺服器超載，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="0b2c7-156">In a `Closed` handler that restarts the connection, consider waiting for some random delay to prevent overloading the server, as shown in the following example:</span></span>

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="0b2c7-157">從用戶端呼叫中樞方法</span><span class="sxs-lookup"><span data-stu-id="0b2c7-157">Call hub methods from client</span></span>

<span data-ttu-id="0b2c7-158">`InvokeAsync` 呼叫中樞上的方法。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-158">`InvokeAsync` calls methods on the hub.</span></span> <span data-ttu-id="0b2c7-159">將中樞方法中定義的中樞方法名稱和任何引數傳遞至 `InvokeAsync` 。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-159">Pass the hub method name and any arguments defined in the hub method to `InvokeAsync`.</span></span> <span data-ttu-id="0b2c7-160">SignalR 是非同步，因此 `async` 請 `await` 在進行呼叫時使用和。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-160">SignalR is asynchronous, so use `async` and `await` when making the calls.</span></span>

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

<span data-ttu-id="0b2c7-161">方法會傳回， `InvokeAsync` `Task` 當伺服器方法傳回時，它就會完成。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-161">The `InvokeAsync` method returns a `Task` which completes when the server method returns.</span></span> <span data-ttu-id="0b2c7-162">傳回值（如果有的話）會做為的結果提供 `Task` 。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-162">The return value, if any, is provided as the result of the `Task`.</span></span> <span data-ttu-id="0b2c7-163">伺服器上的方法所擲回的任何例外狀況都會產生錯誤 `Task` 。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-163">Any exceptions thrown by the method on the server produce a faulted `Task`.</span></span> <span data-ttu-id="0b2c7-164">使用 `await` 語法來等候伺服器方法完成，並使用 `try...catch` 語法來處理錯誤。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-164">Use `await` syntax to wait for the server method to complete and `try...catch` syntax to handle errors.</span></span>

<span data-ttu-id="0b2c7-165">方法會傳回， `SendAsync` `Task` 當訊息傳送到伺服器時，它就會完成。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-165">The `SendAsync` method returns a `Task` which completes when the message has been sent to the server.</span></span> <span data-ttu-id="0b2c7-166">未提供傳回值，因為這 `Task` 不會等到伺服器方法完成為止。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-166">No return value is provided since this `Task` doesn't wait until the server method completes.</span></span> <span data-ttu-id="0b2c7-167">傳送訊息時，在用戶端上擲回的任何例外狀況都會產生錯誤 `Task` 。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-167">Any exceptions thrown on the client while sending the message produce a faulted `Task`.</span></span> <span data-ttu-id="0b2c7-168">使用 `await` 和 `try...catch` 語法來處理傳送錯誤。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-168">Use `await` and `try...catch` syntax to handle send errors.</span></span>

> [!NOTE]
> <span data-ttu-id="0b2c7-169">只有 SignalR 在 *預設* 模式下使用 Azure 服務時，才支援從用戶端呼叫中樞方法。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-169">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="0b2c7-170">如需詳細資訊，請參閱 [ (azure Signalr GitHub 存放庫) ](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose)的常見問題。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-170">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="0b2c7-171">從中樞呼叫用戶端方法</span><span class="sxs-lookup"><span data-stu-id="0b2c7-171">Call client methods from hub</span></span>

<span data-ttu-id="0b2c7-172">定義中樞在 `connection.On` 建立之後，但在開始連接之前所呼叫的方法。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-172">Define methods the hub calls using `connection.On` after building, but before starting the connection.</span></span>

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

<span data-ttu-id="0b2c7-173">中的上述程式碼 `connection.On` 會在伺服器端程式碼使用方法呼叫它時執行 `SendAsync` 。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-173">The preceding code in `connection.On` runs when server-side code calls it using the `SendAsync` method.</span></span>

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a><span data-ttu-id="0b2c7-174">錯誤處理和記錄</span><span class="sxs-lookup"><span data-stu-id="0b2c7-174">Error handling and logging</span></span>

<span data-ttu-id="0b2c7-175">使用 try-catch 語句來處理錯誤。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-175">Handle errors with a try-catch statement.</span></span> <span data-ttu-id="0b2c7-176">檢查 `Exception` 物件，以判斷發生錯誤之後要採取的適當動作。</span><span class="sxs-lookup"><span data-stu-id="0b2c7-176">Inspect the `Exception` object to determine the proper action to take after an error occurs.</span></span>

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a><span data-ttu-id="0b2c7-177">其他資源</span><span class="sxs-lookup"><span data-stu-id="0b2c7-177">Additional resources</span></span>

* [<span data-ttu-id="0b2c7-178">集線器</span><span class="sxs-lookup"><span data-stu-id="0b2c7-178">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="0b2c7-179">JavaScript 用戶端</span><span class="sxs-lookup"><span data-stu-id="0b2c7-179">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="0b2c7-180">發佈至 Azure</span><span class="sxs-lookup"><span data-stu-id="0b2c7-180">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="0b2c7-181">Azure SignalR 服務無伺服器檔</span><span class="sxs-lookup"><span data-stu-id="0b2c7-181">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)
