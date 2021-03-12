---
title: ASP.NET Core SignalR .Net 用戶端
author: bradygaster
description: ASP.NET Core SignalR .Net 用戶端的相關資訊
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/14/2020
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
uid: signalr/dotnet-client
ms.openlocfilehash: db2bfa6d3aa440615c5a9c17ae843dbe22755c97
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589240"
---
# <a name="aspnet-core-signalr-net-client"></a>ASP.NET Core SignalR .Net 用戶端

ASP.NET Core SignalR .net 用戶端程式庫可讓您 SignalR 從 .net 應用程式與中樞進行通訊。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/signalr/dotnet-client/sample) ([如何下載](xref:index#how-to-download-a-sample)) 

本文中的程式碼範例是使用 ASP.NET Core .Net 用戶端的 WPF 應用程式 SignalR 。

## <a name="install-the-signalr-net-client-package"></a>安裝 SignalR .net 用戶端套件

[AspNetCore。 SignalR需要用戶端](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client)套件才能讓 .net 用戶端連線到 SignalR 中樞。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

若要安裝用戶端程式庫，請在 [ **套件管理員主控台** ] 視窗中執行下列命令：

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

若要安裝用戶端程式庫，請在命令 shell 中執行下列命令：

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="connect-to-a-hub"></a>連接至中樞

若要建立連接，請建立 `HubConnectionBuilder` 和呼叫 `Build` 。 建立連接時，可以設定中樞 URL、通訊協定、傳輸類型、記錄層級、標頭和其他選項。 藉由將任何方法插入，來設定任何必要的選項 `HubConnectionBuilder` `Build` 。 開始與的連接 `StartAsync` 。

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a>處理遺失的連接

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>自動重新連線

<xref:Microsoft.AspNetCore.SignalR.Client.HubConnection>可以設定為使用上的方法自動重新連接 `WithAutomaticReconnect` <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> 。 它預設不會自動重新連線。

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect()
    .Build();
```

如果沒有任何參數， `WithAutomaticReconnect()` 則會先將用戶端設定為等候0、2、10和30秒，然後再嘗試每次嘗試重新連線，並在嘗試四次失敗之後停止。

在開始進行任何重新連接嘗試之前， `HubConnection` 會轉換成 `HubConnectionState.Reconnecting` 狀態並引發 `Reconnecting` 事件。  這可讓使用者警告使用者連接已遺失，並停用 UI 元素。 非互動式應用程式可以開始將訊息排入佇列或卸載。

```csharp
connection.Reconnecting += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Reconnecting);

    // Notify users the connection was lost and the client is reconnecting.
    // Start queuing or dropping messages.

    return Task.CompletedTask;
};
```

如果用戶端在前四次嘗試中成功重新連接，則 `HubConnection` 會轉換回 `Connected` 狀態並引發 `Reconnected` 事件。 這讓您有機會通知使用者已重新建立連線，並將任何佇列的訊息清除佇列。

由於連接完全是伺服器的新專案，因此 `ConnectionId` 會提供新的 `Reconnected` 事件處理常式。

> [!WARNING]
> `Reconnected` `connectionId` 如果 `HubConnection` 設定為[略過協商](xref:signalr/configuration#configure-client-options)，事件處理常式的參數將會是 null。

```csharp
connection.Reconnected += connectionId =>
{
    Debug.Assert(connection.State == HubConnectionState.Connected);

    // Notify users the connection was reestablished.
    // Start dequeuing messages queued while reconnecting if any.

    return Task.CompletedTask;
};
```

`WithAutomaticReconnect()` 將不會設定 `HubConnection` 重試初始啟動失敗，因此必須手動處理啟動失敗：

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

如果用戶端在前四次嘗試中未成功重新連接，則 `HubConnection` 會轉換為 `Disconnected` 狀態並引發 <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> 事件。 這會讓您有機會嘗試手動重新開機連線，或通知使用者連接已永久遺失。

```csharp
connection.Closed += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Disconnected);

    // Notify users the connection has been closed or manually try to restart the connection.

    return Task.CompletedTask;
};
```

若要在中斷連線或變更重新連線時間之前設定自訂的重新連接嘗試次數，請 `WithAutomaticReconnect` 接受代表延遲的數位陣列，以毫秒為單位來開始每次重新連線嘗試。

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.Zero, TimeSpan.FromSeconds(10) })
    .Build();

    // .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.FromSeconds(2), TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(30) }) yields the default behavior.
```

上述範例會將設定 `HubConnection` 為在連接遺失之後立即開始嘗試重新連接。 這也適用于預設設定。

如果第一次重新連線嘗試失敗，第二次重新連線嘗試也會立即開始，而不是等候2秒，就像在預設設定中一樣。

如果第二次重新連線嘗試失敗，第三次重新連線嘗試將會在10秒內啟動，這同樣就像是預設設定。

然後，自訂行為會在第三次重新連線嘗試失敗之後停止，然後從預設行為再次分歧。 在預設設定中，會在另一個30秒內重新連線嘗試。

如果您想要更充分掌控自動重新連線嘗試的時間和數目，請 `WithAutomaticReconnect` 接受執行介面的物件 `IRetryPolicy` ，該介面具有名為的單一方法 `NextRetryDelay` 。

`NextRetryDelay` 採用具有類型的單一引數 `RetryContext` 。 `RetryContext`有三個屬性： `PreviousRetryCount` 、 `ElapsedTime` 和 `RetryReason` ， `long` 分別為、和 `TimeSpan` `Exception` 。 第一次重新連線嘗試之前， `PreviousRetryCount` 和都 `ElapsedTime` 是零，而且 `RetryReason` 會是導致連接遺失的例外狀況。 每次重試嘗試失敗之後，將會 `PreviousRetryCount` 遞增一次， `ElapsedTime` 將會更新以反映到目前為止所花的時間，而且 `RetryReason` 將會是造成上次重新連線嘗試失敗的例外狀況。

`NextRetryDelay` 必須傳回代表下次重新連線嘗試之前等候時間的 TimeSpan，或 `null` 是否 `HubConnection` 應該停止重新連接。

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

或者，您也可以撰寫程式碼，以手動方式重新連接用戶端，如 [手動重新](#manually-reconnect)連線中所示範。

::: moniker-end

### <a name="manually-reconnect"></a>手動重新連線

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> 在3.0 之前，的 .NET 用戶端 SignalR 不會自動重新連線。 您必須撰寫會以手動方式重新連接用戶端的程式碼。

::: moniker-end

使用 <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> 事件來回應遺失的連接。 例如，您可能會想要自動重新連接。

此 `Closed` 事件需要一個傳回的委派 `Task` ，讓非同步程式碼在不使用的情況下執行 `async void` 。 若要在以同步方式執行的事件處理常式中滿足委派簽章 `Closed` ，請傳回 `Task.CompletedTask` ：

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

非同步支援的主要原因是，您可以重新開機連接。 啟動連接是非同步動作。

在 `Closed` 重新開機連接的處理常式中，請考慮等待一些隨機延遲，以避免伺服器超載，如下列範例所示：

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a>從用戶端呼叫中樞方法

`InvokeAsync` 呼叫中樞上的方法。 將中樞方法中定義的中樞方法名稱和任何引數傳遞至 `InvokeAsync` 。 SignalR 是非同步，因此 `async` 請 `await` 在進行呼叫時使用和。

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

方法會傳回， `InvokeAsync` `Task` 當伺服器方法傳回時，它就會完成。 傳回值（如果有的話）會做為的結果提供 `Task` 。 伺服器上的方法所擲回的任何例外狀況都會產生錯誤 `Task` 。 使用 `await` 語法來等候伺服器方法完成，並使用 `try...catch` 語法來處理錯誤。

方法會傳回， `SendAsync` `Task` 當訊息傳送到伺服器時，它就會完成。 未提供傳回值，因為這 `Task` 不會等到伺服器方法完成為止。 傳送訊息時，在用戶端上擲回的任何例外狀況都會產生錯誤 `Task` 。 使用 `await` 和 `try...catch` 語法來處理傳送錯誤。

> [!NOTE]
> 只有 SignalR 在 *預設* 模式下使用 Azure 服務時，才支援從用戶端呼叫中樞方法。 如需詳細資訊，請參閱 [ (azure Signalr GitHub 存放庫) ](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose)的常見問題。

## <a name="call-client-methods-from-hub"></a>從中樞呼叫用戶端方法

定義中樞在 `connection.On` 建立之後，但在開始連接之前所呼叫的方法。

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

中的上述程式碼 `connection.On` 會在伺服器端程式碼使用方法呼叫它時執行 `SendAsync` 。

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a>錯誤處理和記錄

使用 try-catch 語句來處理錯誤。 檢查 `Exception` 物件，以判斷發生錯誤之後要採取的適當動作。

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a>其他資源

* [中樞](xref:signalr/hubs)
* [JavaScript 用戶端](xref:signalr/javascript-client)
* [發佈至 Azure](xref:signalr/publish-to-azure-web-app)
* [Azure SignalR 服務無伺服器檔](/azure/azure-signalr/signalr-concept-serverless-development-config)
