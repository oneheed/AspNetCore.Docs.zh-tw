---
title: ASP.NET Core SignalR 設定
author: bradygaster
description: 瞭解如何設定 ASP.NET Core SignalR 應用程式。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
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
uid: signalr/configuration
ms.openlocfilehash: 8851246dbaa076af1fdbc4e5e4f1ada0e4e3988a
ms.sourcegitcommit: b5ebaf42422205d212e3dade93fcefcf7f16db39
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92326586"
---
# <a name="aspnet-core-no-locsignalr-configuration"></a>ASP.NET Core SignalR 設定

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a>JSON/MessagePack 序列化選項

ASP.NET Core SignalR 支援兩種用來編碼訊息的通訊協定： [JSON](https://www.json.org/) 和 [MessagePack](https://msgpack.org/index.html)。 每個通訊協定都有序列化設定選項。

您可以使用 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 擴充方法，在伺服器上設定 JSON 序列化。 `AddJsonProtocol`可以在[加入 SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)之後加入 `Startup.ConfigureServices` 。 `AddJsonProtocol`方法會使用接收物件的委派 `options` 。 該物件的 [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) 屬性是一個 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 物件，可以用來設定引數和傳回值的序列化。 如需詳細資訊，請參閱 [ 檔上的System.Text.Js](/dotnet/api/system.text.json)。

舉例來說，若要將序列化程式設定為不變更屬性名稱的大小寫，而不是預設的 "camelCase" 名稱，請在中使用下列程式碼 `Startup.ConfigureServices` ：

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

在 .NET 用戶端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的擴充方法。 `Microsoft.Extensions.DependencyInjection`必須匯入命名空間來解析擴充方法：

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> 目前無法在 JavaScript 用戶端中設定 JSON 序列化。

### <a name="switch-to-newtonsoftjson"></a>切換至 Newtonsoft.Js開啟

如果您需要 `Newtonsoft.Json` 中不支援的功能 `System.Text.Json` ，請參閱 [切換至 Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson)。

### <a name="messagepack-serialization-options"></a>MessagePack 序列化選項

您可以藉由提供委派給 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 呼叫來設定 MessagePack 序列化。 如需詳細資訊，請參閱[中 SignalR 的 MessagePack](xref:signalr/messagepackhubprotocol) 。

> [!NOTE]
> 目前無法在 JavaScript 用戶端中設定 MessagePack 序列化。

## <a name="configure-server-options"></a>設定伺服器選項

下表說明設定中樞的選項 SignalR ：

| 選項 | 預設值 | 描述 |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 秒 | 如果用戶端未收到訊息 (（包括在此間隔內保持運作) ），伺服器會將用戶端視為已中斷連線。 這可能需要比此逾時間隔更長的時間，讓用戶端實際標示為已中斷連線，因為這是如何實行的。 建議的值是值的兩倍 `KeepAliveInterval` 。|
| `HandshakeTimeout` | 15 秒 | 如果用戶端未在此時間間隔內傳送初始交握訊息，則連接會關閉。 這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。 如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。 |
| `KeepAliveInterval` | 15 秒 | 如果伺服器未在此間隔內傳送訊息，則會自動傳送 ping 訊息以保持連接開啟。 變更時 `KeepAliveInterval` ，請變更 `ServerTimeout` / `serverTimeoutInMilliseconds` 用戶端上的設定。 建議的 `ServerTimeout` / `serverTimeoutInMilliseconds` 值是值的兩倍 `KeepAliveInterval` 。  |
| `SupportedProtocols` | 所有已安裝的通訊協定 | 此中樞所支援的通訊協定。 預設會允許在伺服器上註冊的所有通訊協定，但是可以從此清單中移除通訊協定，以停用個別中樞的特定通訊協定。 |
| `EnableDetailedErrors` | `false` | 如果為 `true` ，則在中樞方法中擲回例外狀況時，會將詳細的例外狀況訊息傳回給用戶端。 預設值為 `false` ，因為這些例外狀況訊息可能包含機密資訊。 |
| `StreamBufferCapacity` | `10` | 可以針對用戶端上傳資料流程進行緩衝處理的最大專案數。 如果達到此限制，就會封鎖調用的處理，直到伺服器處理資料流程專案為止。|
| `MaximumReceiveMessageSize` | 32 KB | 單一傳入中樞訊息的大小上限。 |
| `MaximumParallelInvocationsPerClient` | 1 | 每個用戶端在進行佇列之前可以平行呼叫的中樞方法數目上限。 |

您可以為所有中樞設定選項，方法是提供選項委派給 `AddSignalR` 中的呼叫 `Startup.ConfigureServices` 。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

單一中樞的選項會覆寫中提供的全域選項 `AddSignalR` ，可以使用下列方式進行設定 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ：

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Advanced HTTP 設定選項

用 `HttpConnectionDispatcherOptions` 來設定與傳輸和記憶體緩衝區管理相關的 advanced 設定。 這些選項的設定方式是將委派傳遞至中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) `Startup.Configure` 。

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

下表說明設定 ASP.NET Core SignalR ADVANCED HTTP 選項的選項：

| 選項 | 預設值 | 描述 |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | 在套用背壓之前，從用戶端接收的最大位元組數目。 提高此值可讓伺服器在不套用背壓的情況下，更快接收更大的訊息，但可能會增加記憶體耗用量。 |
| `AuthorizationData` | 自動從套用 `Authorize` 至中樞類別的屬性收集的資料。 | 用來判斷是否授權用戶端連線至中樞之 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 物件的清單。 |
| `TransportMaxBufferSize` | 32 KB | 在觀察到背壓之前，伺服器所緩衝的應用程式所傳送的位元組數目上限。 提高此值可讓伺服器更快速緩衝較大的訊息，而不需要等待背壓，但可能會增加記憶體耗用量。 |
| `Transports` | 所有傳輸都會啟用。 | 值的位旗標列舉 `HttpTransportType` ，可限制用戶端可以用來連接的傳輸。 |
| `LongPolling` | 請參閱下文。 | 長輪詢傳輸特定的其他選項。 |
| `WebSockets` | 請參閱下文。 | Websocket 傳輸特定的其他選項。 |
| `MinimumProtocolVersion` | 0 | 指定 negotiate 通訊協定的最小版本。 這是用來將用戶端限制在較新的版本。 |

長時間輪詢傳輸具有可使用屬性設定的其他選項 `LongPolling` ：

| 選項 | 預設值 | 描述 |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90秒 | 在終止單一輪詢要求之前，伺服器等候訊息傳送至用戶端的最大時間量。 減少此值會導致用戶端更頻繁地發出新的投票要求。 |

WebSocket 傳輸具有可使用屬性設定的其他選項 `WebSockets` ：

| 選項 | 預設值 | 描述 |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 秒 | 伺服器關閉之後，如果用戶端在此時間間隔內無法關閉，則連接會終止。 |
| `SubProtocolSelector` | `null` | 可以用來將 `Sec-WebSocket-Protocol` 標頭設定為自訂值的委派。 委派會接收用戶端要求的值做為輸入，而且預期會傳回所需的值。 |

## <a name="configure-client-options"></a>設定用戶端選項

您可以在 `HubConnectionBuilder` .net 和 JavaScript 用戶端) 中提供的類型 (設定用戶端選項。 它也可以在 JAVA 用戶端中使用，但子類別也是包含產生器設定選項以及本身的子 `HttpHubConnectionBuilder` 類別 `HubConnection` 。

### <a name="configure-logging"></a>設定記錄

您可以使用方法，在 .NET 用戶端中設定記錄 `ConfigureLogging` 。 記錄提供者和篩選器的註冊方式與在伺服器上的方式相同。 如需詳細資訊，請參閱 [ASP.NET Core 檔中的記錄](xref:fundamentals/logging/index) 。

> [!NOTE]
> 為了註冊記錄提供者，您必須安裝必要的套件。 如需完整清單，請參閱檔的 [內建記錄提供者](xref:fundamentals/logging/index#built-in-logging-providers) 一節。

例如，若要啟用主控台記錄，請安裝 `Microsoft.Extensions.Logging.Console` NuGet 套件。 呼叫 `AddConsole` 擴充方法：

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

在 JavaScript 用戶端中，有類似的 `configureLogging` 方法。 提供一個 `LogLevel` 值，指出要產生的記錄檔訊息的最小層級。 記錄檔會寫入至瀏覽器主控台視窗。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

`LogLevel`您也可以提供 `string` 代表記錄層級名稱的值，而不是值。 SignalR在您沒有常數存取權的環境中設定記錄時，這會很有用 `LogLevel` 。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

下表列出可用的記錄層級。 您所提供的值， `configureLogging` 用來設定將記錄的 **最小** 記錄層級。 記錄在此層級的訊息， **或是在資料表中所列的層級**，將會被記錄下來。

| String                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info`**或**`information` | `LogLevel.Information` |
| `warn`**或**`warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> 若要完全停用記錄，請 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。

如需有關記錄的詳細資訊，請參閱[ SignalR 診斷檔](xref:signalr/diagnostics)。

SignalRJAVA 用戶端會使用[SLF4J](https://www.slf4j.org/)程式庫來進行記錄。 它是一種高階記錄 API，可讓程式庫的使用者藉由帶入特定的記錄相依性，選擇自己的特定記錄執行。 下列程式碼片段說明如何搭配使用 `java.util.logging` SignalR JAVA 用戶端。

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

如果您未設定相依性中的記錄，SLF4J 會載入預設的無作業記錄器，並顯示下列警告訊息：

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

這可以安全地忽略。

### <a name="configure-allowed-transports"></a>設定允許的傳輸

使用的傳輸 SignalR 可在 `WithUrl` JavaScript) 的呼叫 (中設定 `withUrl` 。 值的位 OR `HttpTransportType` 可以用來限制用戶端只能使用指定的傳輸。 預設會啟用所有傳輸。

例如，若要停用 Server-Sent 事件傳輸，但允許 Websocket 和長時間輪詢連接：

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

在 JavaScript 用戶端中，您可以設定提供的 `transport` 選項物件上的欄位來設定傳輸 `withUrl` ：

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

這一版的 JAVA 用戶端 websocket 是唯一可用的傳輸。

在 JAVA 用戶端中，會使用上的方法來選取傳輸 `withTransport` `HttpHubConnectionBuilder` 。 JAVA 用戶端預設為使用 Websocket 傳輸。

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> SignalRJAVA 用戶端尚不支援傳輸回復。

### <a name="configure-bearer-authentication"></a>設定持有人驗證

若要提供驗證資料和 SignalR 要求，請使用 `AccessTokenProvider` JavaScript) 的選項 (， `accessTokenFactory` 以指定傳回所需存取權杖的函式。 在 .NET 用戶端中，此存取權杖會以 HTTP 「持有人驗證」權杖的形式傳入， (使用 `Authorization`) 類型的標頭 `Bearer` 。 在 JavaScript 用戶端中，存取權杖會用來作為持有人權杖， **但** 在某些情況下，瀏覽器 api 會限制在) 中 Server-Sent 事件和 websocket 要求中套用標頭 (的能力。 在這些情況下，存取權杖會以查詢字串值的形式提供 `access_token` 。

在 .NET 用戶端中，您 `AccessTokenProvider` 可以使用中的 options 委派來指定選項 `WithUrl` ：

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

在 JavaScript 用戶端中，您可以設定 `accessTokenFactory` 中選項物件上的欄位來設定存取權杖 `withUrl` ：

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

在 SignalR JAVA 用戶端中，您可以提供存取權杖 factory 給 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)，以設定用於驗證的持有人權杖。 使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)來提供[RxJAVA](https://github.com/ReactiveX/RxJava) [單一 \<String> ](https://reactivex.io/documentation/single.html)。 只要呼叫 [Single. 延遲](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，您就可以撰寫邏輯來產生用戶端的存取權杖。

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>設定 timeout 和 keep-alive 選項

設定 timeout 和 keep-alive 行為的其他選項可在 `HubConnection` 物件本身取得：

# <a name="net"></a>[.NET](#tab/dotnet)

| 選項 | 預設值 | 描述 |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30秒 (30000 毫秒)  | 伺服器活動的超時時間。 如果伺服器未在此間隔中傳送訊息，用戶端會將伺服器視為已中斷連線，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。 這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。 建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。 |
| `HandshakeTimeout` | 15 秒 | 初始伺服器交握的超時時間。 如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。 這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。 如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。 |
| `KeepAliveInterval` | 15 秒 | 決定用戶端傳送 ping 訊息的間隔。 從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。 如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。 |

在 .NET 用戶端中，超時值會指定為 `TimeSpan` 值。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| 選項 | 預設值 | 描述 |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30秒 (30000 毫秒)  | 伺服器活動的超時時間。 如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onclose` 事件。 這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。 建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。 |
| `keepAliveIntervalInMilliseconds` | 15秒 (15000 毫秒)  | 決定用戶端傳送 ping 訊息的間隔。 從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。 如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。 |

# <a name="java"></a>[Java](#tab/java)

| 選項 | 預設值 | 描述 |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30秒 (30000 毫秒)  | 伺服器活動的超時時間。 如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onClose` 事件。 這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。 建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。 |
| `withHandshakeResponseTimeout` | 15 秒 | 初始伺服器交握的超時時間。 如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並觸發 `onClose` 事件。 這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。 如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。 |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15秒 (15000 毫秒)  | 決定用戶端傳送 ping 訊息的間隔。 從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。 如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。 |

---

### <a name="configure-additional-options"></a>設定其他選項

您可以在 `WithUrl` `withUrl` JavaScript) 方法的 (中， `HubConnectionBuilder` 或在 JAVA 用戶端的不同設定 api 上設定其他選項 `HttpHubConnectionBuilder` ：

# <a name="net"></a>[.NET](#tab/dotnet)

| .NET 選項 |  預設值 | 描述 |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | 傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。 |
| `SkipNegotiation` | `false` | 將此設定為， `true` 以略過協商步驟。 **只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。 使用 Azure 服務時，無法啟用此設定 SignalR 。 |
| `ClientCertificates` | Empty | 要傳送以驗證要求的 TLS 憑證集合。 |
| `Cookies` | Empty | cookie要與每個 HTTP 要求一起傳送的 HTTP 集合。 |
| `Credentials` | Empty | 每個 HTTP 要求所要傳送的認證。 |
| `CloseTimeout` | 5 秒 | 僅限 Websocket。 用戶端在關閉以確認關閉要求後等待的最大時間量。 如果伺服器未在這段時間內確認關閉，用戶端就會中斷連線。 |
| `Headers` | Empty | 要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。 |
| `HttpMessageHandlerFactory` | `null` | 可以用來設定或取代 `HttpMessageHandler` 用來傳送 HTTP 要求的委派。 不用於 WebSocket 連接。 此委派必須傳回非 null 值，且會接收預設值做為參數。 請修改該預設值的設定並傳回，或傳回新的 `HttpMessageHandler` 實例。 **取代處理常式時，請務必從提供的處理常式複製您想要保留的設定，否則設定的選項 (例如 Cookie s 和標頭) 不會套用至新的處理常式。** |
| `Proxy` | `null` | 傳送 HTTP 要求時要使用的 HTTP proxy。 |
| `UseDefaultCredentials` | `false` | 設定此布林值，以傳送 HTTP 和 Websocket 要求的預設認證。 這樣就可以使用 Windows 驗證。 |
| `WebSocketConfiguration` | `null` | 可以用來設定其他 WebSocket 選項的委派。 接收可以用來設定選項的 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 實例。 |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| JavaScript 選項 | 預設值 | 描述 |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | 傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。 |
| `transport` | `null` | <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>值，指定要用於連接的傳輸。 |
| `headers` | `null` | 每個 HTTP 要求傳送的標頭字典。 在瀏覽器中傳送標頭不適用於 Websocket 或 <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> 串流。 |
| `logMessageContent` | `null` | 設定為， `true` 以記錄用戶端所傳送和接收之訊息的位元組/字元。 |
| `skipNegotiation` | `false` | 將此設定為， `true` 以略過協商步驟。 **只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。 使用 Azure 服務時，無法啟用此設定 SignalR 。 |
| `withCredentials` | `true` | 指定是否將使用 CORS 要求傳送認證。 Azure App Service 會使用 cookie 來進行粘滯話，且必須啟用此選項才能正確運作。 如需 CORS 與的詳細資訊 SignalR ，請參閱 <xref:signalr/security#cross-origin-resource-sharing> 。 |

# <a name="java"></a>[Java](#tab/java)

| JAVA 選項 | 預設值 | 描述 |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | 傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。 |
| `shouldSkipNegotiate` | `false` | 將此設定為， `true` 以略過協商步驟。 **只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。 使用 Azure 服務時，無法啟用此設定 SignalR 。 |
| `withHeader` `withHeaders` | Empty | 要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。 |

---

在 .NET 用戶端中，這些選項可以由提供給的 options 委派來修改 `WithUrl` ：

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.SkipNegotiation = true;
        options.Transports = HttpTransportType.WebSockets;
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

在 JavaScript 用戶端中，您可以在提供下列功能的 JavaScript 物件中提供這些選項 `withUrl` ：

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        // "Foo: Bar" will not be sent with WebSockets or Server-Sent Events requests
        headers: { "Foo": "Bar" },
        transport: signalR.HttpTransportType.LongPolling 
    })
    .build();
```

在 JAVA 用戶端中，您可以在傳回的上使用方法來設定這些選項。 `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>其他資源

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a>JSON/MessagePack 序列化選項

ASP.NET Core SignalR 支援兩種用來編碼訊息的通訊協定： [JSON](https://www.json.org/) 和 [MessagePack](https://msgpack.org/index.html)。 每個通訊協定都有序列化設定選項。

您可以使用 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 擴充方法，在伺服器上設定 JSON 序列化。 `AddJsonProtocol`可以在[加入 SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)之後加入 `Startup.ConfigureServices` 。 `AddJsonProtocol`方法會使用接收物件的委派 `options` 。 該物件的 [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) 屬性是一個 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 物件，可以用來設定引數和傳回值的序列化。 如需詳細資訊，請參閱 [ 檔上的System.Text.Js](/dotnet/api/system.text.json)。

舉例來說，若要將序列化程式設定為不變更屬性名稱的大小寫，而不是預設的 "camelCase" 名稱，請在中使用下列程式碼 `Startup.ConfigureServices` ：

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

在 .NET 用戶端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的擴充方法。 `Microsoft.Extensions.DependencyInjection`必須匯入命名空間來解析擴充方法：

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> 目前無法在 JavaScript 用戶端中設定 JSON 序列化。

### <a name="switch-to-newtonsoftjson"></a>切換至 Newtonsoft.Js開啟

如果您需要 `Newtonsoft.Json` 中不支援的功能 `System.Text.Json` ，請參閱 [切換至 Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson)。

### <a name="messagepack-serialization-options"></a>MessagePack 序列化選項

您可以藉由提供委派給 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 呼叫來設定 MessagePack 序列化。 如需詳細資訊，請參閱[中 SignalR 的 MessagePack](xref:signalr/messagepackhubprotocol) 。

> [!NOTE]
> 目前無法在 JavaScript 用戶端中設定 MessagePack 序列化。

## <a name="configure-server-options"></a>設定伺服器選項

下表說明設定中樞的選項 SignalR ：

| 選項 | 預設值 | 描述 |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 秒 | 如果用戶端未收到訊息 (（包括在此間隔內保持運作) ），伺服器會將用戶端視為已中斷連線。 這可能需要比此逾時間隔更長的時間，讓用戶端實際標示為已中斷連線，因為這是如何實行的。 建議的值是值的兩倍 `KeepAliveInterval` 。|
| `HandshakeTimeout` | 15 秒 | 如果用戶端未在此時間間隔內傳送初始交握訊息，則連接會關閉。 這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。 如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。 |
| `KeepAliveInterval` | 15 秒 | 如果伺服器未在此間隔內傳送訊息，則會自動傳送 ping 訊息以保持連接開啟。 變更時 `KeepAliveInterval` ，請變更 `ServerTimeout` / `serverTimeoutInMilliseconds` 用戶端上的設定。 建議的 `ServerTimeout` / `serverTimeoutInMilliseconds` 值是值的兩倍 `KeepAliveInterval` 。  |
| `SupportedProtocols` | 所有已安裝的通訊協定 | 此中樞所支援的通訊協定。 預設會允許在伺服器上註冊的所有通訊協定，但是可以從此清單中移除通訊協定，以停用個別中樞的特定通訊協定。 |
| `EnableDetailedErrors` | `false` | 如果為 `true` ，則在中樞方法中擲回例外狀況時，會將詳細的例外狀況訊息傳回給用戶端。 預設值為 `false` ，因為這些例外狀況訊息可能包含機密資訊。 |
| `StreamBufferCapacity` | `10` | 可以針對用戶端上傳資料流程進行緩衝處理的最大專案數。 如果達到此限制，就會封鎖調用的處理，直到伺服器處理資料流程專案為止。|
| `MaximumReceiveMessageSize` | 32 KB | 單一傳入中樞訊息的大小上限。 |

您可以為所有中樞設定選項，方法是提供選項委派給 `AddSignalR` 中的呼叫 `Startup.ConfigureServices` 。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

單一中樞的選項會覆寫中提供的全域選項 `AddSignalR` ，可以使用下列方式進行設定 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ：

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Advanced HTTP 設定選項

用 `HttpConnectionDispatcherOptions` 來設定與傳輸和記憶體緩衝區管理相關的 advanced 設定。 這些選項的設定方式是將委派傳遞至中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) `Startup.Configure` 。

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

下表說明設定 ASP.NET Core SignalR ADVANCED HTTP 選項的選項：

| 選項 | 預設值 | 描述 |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | 在套用背壓之前，從用戶端接收的最大位元組數目。 提高此值可讓伺服器在不套用背壓的情況下，更快接收更大的訊息，但可能會增加記憶體耗用量。 |
| `AuthorizationData` | 自動從套用 `Authorize` 至中樞類別的屬性收集的資料。 | 用來判斷是否授權用戶端連線至中樞之 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 物件的清單。 |
| `TransportMaxBufferSize` | 32 KB | 在觀察到背壓之前，伺服器所緩衝的應用程式所傳送的位元組數目上限。 提高此值可讓伺服器更快速緩衝較大的訊息，而不需要等待背壓，但可能會增加記憶體耗用量。 |
| `Transports` | 所有傳輸都會啟用。 | 值的位旗標列舉 `HttpTransportType` ，可限制用戶端可以用來連接的傳輸。 |
| `LongPolling` | 請參閱下文。 | 長輪詢傳輸特定的其他選項。 |
| `WebSockets` | 請參閱下文。 | Websocket 傳輸特定的其他選項。 |
| `MinimumProtocolVersion` | 0 | 指定 negotiate 通訊協定的最小版本。 這是用來將用戶端限制在較新的版本。 |

長時間輪詢傳輸具有可使用屬性設定的其他選項 `LongPolling` ：

| 選項 | 預設值 | 描述 |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90秒 | 在終止單一輪詢要求之前，伺服器等候訊息傳送至用戶端的最大時間量。 減少此值會導致用戶端更頻繁地發出新的投票要求。 |

WebSocket 傳輸具有可使用屬性設定的其他選項 `WebSockets` ：

| 選項 | 預設值 | 描述 |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 秒 | 伺服器關閉之後，如果用戶端在此時間間隔內無法關閉，則連接會終止。 |
| `SubProtocolSelector` | `null` | 可以用來將 `Sec-WebSocket-Protocol` 標頭設定為自訂值的委派。 委派會接收用戶端要求的值做為輸入，而且預期會傳回所需的值。 |

## <a name="configure-client-options"></a>設定用戶端選項

您可以在 `HubConnectionBuilder` .net 和 JavaScript 用戶端) 中提供的類型 (設定用戶端選項。 它也可以在 JAVA 用戶端中使用，但子類別也是包含產生器設定選項以及本身的子 `HttpHubConnectionBuilder` 類別 `HubConnection` 。

### <a name="configure-logging"></a>設定記錄

您可以使用方法，在 .NET 用戶端中設定記錄 `ConfigureLogging` 。 記錄提供者和篩選器的註冊方式與在伺服器上的方式相同。 如需詳細資訊，請參閱 [ASP.NET Core 檔中的記錄](xref:fundamentals/logging/index) 。

> [!NOTE]
> 為了註冊記錄提供者，您必須安裝必要的套件。 如需完整清單，請參閱檔的 [內建記錄提供者](xref:fundamentals/logging/index#built-in-logging-providers) 一節。

例如，若要啟用主控台記錄，請安裝 `Microsoft.Extensions.Logging.Console` NuGet 套件。 呼叫 `AddConsole` 擴充方法：

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

在 JavaScript 用戶端中，有類似的 `configureLogging` 方法。 提供一個 `LogLevel` 值，指出要產生的記錄檔訊息的最小層級。 記錄檔會寫入至瀏覽器主控台視窗。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

`LogLevel`您也可以提供 `string` 代表記錄層級名稱的值，而不是值。 SignalR在您沒有常數存取權的環境中設定記錄時，這會很有用 `LogLevel` 。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

下表列出可用的記錄層級。 您所提供的值， `configureLogging` 用來設定將記錄的 **最小** 記錄層級。 記錄在此層級的訊息， **或是在資料表中所列的層級**，將會被記錄下來。

| String                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info`**或**`information` | `LogLevel.Information` |
| `warn`**或**`warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> 若要完全停用記錄，請 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。

如需有關記錄的詳細資訊，請參閱[ SignalR 診斷檔](xref:signalr/diagnostics)。

SignalRJAVA 用戶端會使用[SLF4J](https://www.slf4j.org/)程式庫來進行記錄。 它是一種高階記錄 API，可讓程式庫的使用者藉由帶入特定的記錄相依性，選擇自己的特定記錄執行。 下列程式碼片段說明如何搭配使用 `java.util.logging` SignalR JAVA 用戶端。

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

如果您未設定相依性中的記錄，SLF4J 會載入預設的無作業記錄器，並顯示下列警告訊息：

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

這可以安全地忽略。

### <a name="configure-allowed-transports"></a>設定允許的傳輸

使用的傳輸 SignalR 可在 `WithUrl` JavaScript) 的呼叫 (中設定 `withUrl` 。 值的位 OR `HttpTransportType` 可以用來限制用戶端只能使用指定的傳輸。 預設會啟用所有傳輸。

例如，若要停用 Server-Sent 事件傳輸，但允許 Websocket 和長時間輪詢連接：

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

在 JavaScript 用戶端中，您可以設定提供的 `transport` 選項物件上的欄位來設定傳輸 `withUrl` ：

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

這一版的 JAVA 用戶端 websocket 是唯一可用的傳輸。

在 JAVA 用戶端中，會使用上的方法來選取傳輸 `withTransport` `HttpHubConnectionBuilder` 。 JAVA 用戶端預設為使用 Websocket 傳輸。

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> SignalRJAVA 用戶端尚不支援傳輸回復。

### <a name="configure-bearer-authentication"></a>設定持有人驗證

若要提供驗證資料和 SignalR 要求，請使用 `AccessTokenProvider` JavaScript) 的選項 (， `accessTokenFactory` 以指定傳回所需存取權杖的函式。 在 .NET 用戶端中，此存取權杖會以 HTTP 「持有人驗證」權杖的形式傳入， (使用 `Authorization`) 類型的標頭 `Bearer` 。 在 JavaScript 用戶端中，存取權杖會用來作為持有人權杖， **但** 在某些情況下，瀏覽器 api 會限制在) 中 Server-Sent 事件和 websocket 要求中套用標頭 (的能力。 在這些情況下，存取權杖會以查詢字串值的形式提供 `access_token` 。

在 .NET 用戶端中，您 `AccessTokenProvider` 可以使用中的 options 委派來指定選項 `WithUrl` ：

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

在 JavaScript 用戶端中，您可以設定 `accessTokenFactory` 中選項物件上的欄位來設定存取權杖 `withUrl` ：

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

在 SignalR JAVA 用戶端中，您可以提供存取權杖 factory 給 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)，以設定用於驗證的持有人權杖。 使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)來提供[RxJAVA](https://github.com/ReactiveX/RxJava) [單一 \<String> ](https://reactivex.io/documentation/single.html)。 只要呼叫 [Single. 延遲](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，您就可以撰寫邏輯來產生用戶端的存取權杖。

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>設定 timeout 和 keep-alive 選項

設定 timeout 和 keep-alive 行為的其他選項可在 `HubConnection` 物件本身取得：

# <a name="net"></a>[.NET](#tab/dotnet)

| 選項 | 預設值 | 描述 |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30秒 (30000 毫秒)  | 伺服器活動的超時時間。 如果伺服器未在此間隔中傳送訊息，用戶端會將伺服器視為已中斷連線，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。 這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。 建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。 |
| `HandshakeTimeout` | 15 秒 | 初始伺服器交握的超時時間。 如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。 這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。 如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。 |
| `KeepAliveInterval` | 15 秒 | 決定用戶端傳送 ping 訊息的間隔。 從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。 如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。 |

在 .NET 用戶端中，超時值會指定為 `TimeSpan` 值。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| 選項 | 預設值 | 描述 |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30秒 (30000 毫秒)  | 伺服器活動的超時時間。 如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onclose` 事件。 這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。 建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。 |
| `keepAliveIntervalInMilliseconds` | 15秒 (15000 毫秒)  | 決定用戶端傳送 ping 訊息的間隔。 從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。 如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。 |

# <a name="java"></a>[Java](#tab/java)

| 選項 | 預設值 | 描述 |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30秒 (30000 毫秒)  | 伺服器活動的超時時間。 如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onClose` 事件。 這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。 建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。 |
| `withHandshakeResponseTimeout` | 15 秒 | 初始伺服器交握的超時時間。 如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並觸發 `onClose` 事件。 這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。 如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。 |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15秒 (15000 毫秒)  | 決定用戶端傳送 ping 訊息的間隔。 從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。 如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。 |

---

### <a name="configure-additional-options"></a>設定其他選項

您可以在 `WithUrl` `withUrl` JavaScript) 方法的 (中， `HubConnectionBuilder` 或在 JAVA 用戶端的不同設定 api 上設定其他選項 `HttpHubConnectionBuilder` ：

# <a name="net"></a>[.NET](#tab/dotnet)

| .NET 選項 |  預設值 | 描述 |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | 傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。 |
| `SkipNegotiation` | `false` | 將此設定為， `true` 以略過協商步驟。 **只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。 使用 Azure 服務時，無法啟用此設定 SignalR 。 |
| `ClientCertificates` | Empty | 要傳送以驗證要求的 TLS 憑證集合。 |
| `Cookies` | Empty | cookie要與每個 HTTP 要求一起傳送的 HTTP 集合。 |
| `Credentials` | Empty | 每個 HTTP 要求所要傳送的認證。 |
| `CloseTimeout` | 5 秒 | 僅限 Websocket。 用戶端在關閉以確認關閉要求後等待的最大時間量。 如果伺服器未在這段時間內確認關閉，用戶端就會中斷連線。 |
| `Headers` | Empty | 要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。 |
| `HttpMessageHandlerFactory` | `null` | 可以用來設定或取代 `HttpMessageHandler` 用來傳送 HTTP 要求的委派。 不用於 WebSocket 連接。 此委派必須傳回非 null 值，且會接收預設值做為參數。 請修改該預設值的設定並傳回，或傳回新的 `HttpMessageHandler` 實例。 **取代處理常式時，請務必從提供的處理常式複製您想要保留的設定，否則設定的選項 (例如 Cookie s 和標頭) 不會套用至新的處理常式。** |
| `Proxy` | `null` | 傳送 HTTP 要求時要使用的 HTTP proxy。 |
| `UseDefaultCredentials` | `false` | 設定此布林值，以傳送 HTTP 和 Websocket 要求的預設認證。 這樣就可以使用 Windows 驗證。 |
| `WebSocketConfiguration` | `null` | 可以用來設定其他 WebSocket 選項的委派。 接收可以用來設定選項的 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 實例。 |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| JavaScript 選項 | 預設值 | 描述 |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | 傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。 |
| `transport` | `null` | <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>值，指定要用於連接的傳輸。 |
| `logMessageContent` | `null` | 設定為， `true` 以記錄用戶端所傳送和接收之訊息的位元組/字元。 |
| `skipNegotiation` | `false` | 將此設定為， `true` 以略過協商步驟。 **只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。 使用 Azure 服務時，無法啟用此設定 SignalR 。 |

# <a name="java"></a>[Java](#tab/java)

| JAVA 選項 | 預設值 | 描述 |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | 傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。 |
| `shouldSkipNegotiate` | `false` | 將此設定為， `true` 以略過協商步驟。 **只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。 使用 Azure 服務時，無法啟用此設定 SignalR 。 |
| `withHeader` `withHeaders` | Empty | 要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。 |

---

在 .NET 用戶端中，這些選項可以由提供給的 options 委派來修改 `WithUrl` ：

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

在 JavaScript 用戶端中，您可以在提供下列功能的 JavaScript 物件中提供這些選項 `withUrl` ：

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

在 JAVA 用戶端中，您可以在傳回的上使用方法來設定這些選項。 `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>其他資源

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a>JSON/MessagePack 序列化選項

ASP.NET Core SignalR 支援兩種用來編碼訊息的通訊協定： [JSON](https://www.json.org/) 和 [MessagePack](https://msgpack.org/index.html)。 每個通訊協定都有序列化設定選項。

您可以使用 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 擴充方法，在伺服器上設定 JSON 序列化。 `AddJsonProtocol`可以在[加入 SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)之後加入 `Startup.ConfigureServices` 。 `AddJsonProtocol`方法會使用接收物件的委派 `options` 。 該物件的 [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) 屬性是一個 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 物件，可以用來設定引數和傳回值的序列化。 如需詳細資訊，請參閱 [ 檔上的System.Text.Js](/dotnet/api/system.text.json)。

舉例來說，若要將序列化程式設定為不變更屬性名稱的大小寫，而不是預設的 "camelCase" 名稱，請在中使用下列程式碼 `Startup.ConfigureServices` ：

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

在 .NET 用戶端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的擴充方法。 `Microsoft.Extensions.DependencyInjection`必須匯入命名空間來解析擴充方法：

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> 目前無法在 JavaScript 用戶端中設定 JSON 序列化。

### <a name="switch-to-newtonsoftjson"></a>切換至 Newtonsoft.Js開啟

如果您需要 `Newtonsoft.Json` 中不支援的功能 `System.Text.Json` ，請參閱 [切換至 Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson)。

### <a name="messagepack-serialization-options"></a>MessagePack 序列化選項

您可以藉由提供委派給 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 呼叫來設定 MessagePack 序列化。 如需詳細資訊，請參閱[中 SignalR 的 MessagePack](xref:signalr/messagepackhubprotocol) 。

> [!NOTE]
> 目前無法在 JavaScript 用戶端中設定 MessagePack 序列化。

## <a name="configure-server-options"></a>設定伺服器選項

下表說明設定中樞的選項 SignalR ：

| 選項 | 預設值 | 描述 |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 秒 | 如果用戶端未收到訊息 (（包括在此間隔內保持運作) ），伺服器會將用戶端視為已中斷連線。 這可能需要比此逾時間隔更長的時間，讓用戶端實際標示為已中斷連線，因為這是如何實行的。 建議的值是值的兩倍 `KeepAliveInterval` 。|
| `HandshakeTimeout` | 15 秒 | 如果用戶端未在此時間間隔內傳送初始交握訊息，則連接會關閉。 這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。 如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。 |
| `KeepAliveInterval` | 15 秒 | 如果伺服器未在此間隔內傳送訊息，則會自動傳送 ping 訊息以保持連接開啟。 變更時 `KeepAliveInterval` ，請變更 `ServerTimeout` / `serverTimeoutInMilliseconds` 用戶端上的設定。 建議的 `ServerTimeout` / `serverTimeoutInMilliseconds` 值是值的兩倍 `KeepAliveInterval` 。  |
| `SupportedProtocols` | 所有已安裝的通訊協定 | 此中樞所支援的通訊協定。 預設會允許在伺服器上註冊的所有通訊協定，但是可以從此清單中移除通訊協定，以停用個別中樞的特定通訊協定。 |
| `EnableDetailedErrors` | `false` | 如果為 `true` ，則在中樞方法中擲回例外狀況時，會將詳細的例外狀況訊息傳回給用戶端。 預設值為 `false` ，因為這些例外狀況訊息可能包含機密資訊。 |
| `StreamBufferCapacity` | `10` | 可以針對用戶端上傳資料流程進行緩衝處理的最大專案數。 如果達到此限制，就會封鎖調用的處理，直到伺服器處理資料流程專案為止。|
| `MaximumReceiveMessageSize` | 32 KB | 單一傳入中樞訊息的大小上限。 |

您可以為所有中樞設定選項，方法是提供選項委派給 `AddSignalR` 中的呼叫 `Startup.ConfigureServices` 。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

單一中樞的選項會覆寫中提供的全域選項 `AddSignalR` ，可以使用下列方式進行設定 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ：

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Advanced HTTP 設定選項

用 `HttpConnectionDispatcherOptions` 來設定與傳輸和記憶體緩衝區管理相關的 advanced 設定。 這些選項的設定方式是將委派傳遞至中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) `Startup.Configure` 。

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

下表說明設定 ASP.NET Core SignalR ADVANCED HTTP 選項的選項：

| 選項 | 預設值 | 描述 |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | 在套用背壓之前，從用戶端接收的最大位元組數目。 提高此值可讓伺服器在不套用背壓的情況下，更快接收更大的訊息，但可能會增加記憶體耗用量。 |
| `AuthorizationData` | 自動從套用 `Authorize` 至中樞類別的屬性收集的資料。 | 用來判斷是否授權用戶端連線至中樞之 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 物件的清單。 |
| `TransportMaxBufferSize` | 32 KB | 在觀察到背壓之前，伺服器所緩衝的應用程式所傳送的位元組數目上限。 提高此值可讓伺服器更快速緩衝較大的訊息，而不需要等待背壓，但可能會增加記憶體耗用量。 |
| `Transports` | 所有傳輸都會啟用。 | 值的位旗標列舉 `HttpTransportType` ，可限制用戶端可以用來連接的傳輸。 |
| `LongPolling` | 請參閱下文。 | 長輪詢傳輸特定的其他選項。 |
| `WebSockets` | 請參閱下文。 | Websocket 傳輸特定的其他選項。 |

長時間輪詢傳輸具有可使用屬性設定的其他選項 `LongPolling` ：

| 選項 | 預設值 | 描述 |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90秒 | 在終止單一輪詢要求之前，伺服器等候訊息傳送至用戶端的最大時間量。 減少此值會導致用戶端更頻繁地發出新的投票要求。 |

WebSocket 傳輸具有可使用屬性設定的其他選項 `WebSockets` ：

| 選項 | 預設值 | 描述 |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 秒 | 伺服器關閉之後，如果用戶端在此時間間隔內無法關閉，則連接會終止。 |
| `SubProtocolSelector` | `null` | 可以用來將 `Sec-WebSocket-Protocol` 標頭設定為自訂值的委派。 委派會接收用戶端要求的值做為輸入，而且預期會傳回所需的值。 |

## <a name="configure-client-options"></a>設定用戶端選項

您可以在 `HubConnectionBuilder` .net 和 JavaScript 用戶端) 中提供的類型 (設定用戶端選項。 它也可以在 JAVA 用戶端中使用，但子類別也是包含產生器設定選項以及本身的子 `HttpHubConnectionBuilder` 類別 `HubConnection` 。

### <a name="configure-logging"></a>設定記錄

您可以使用方法，在 .NET 用戶端中設定記錄 `ConfigureLogging` 。 記錄提供者和篩選器的註冊方式與在伺服器上的方式相同。 如需詳細資訊，請參閱 [ASP.NET Core 檔中的記錄](xref:fundamentals/logging/index) 。

> [!NOTE]
> 為了註冊記錄提供者，您必須安裝必要的套件。 如需完整清單，請參閱檔的 [內建記錄提供者](xref:fundamentals/logging/index#built-in-logging-providers) 一節。

例如，若要啟用主控台記錄，請安裝 `Microsoft.Extensions.Logging.Console` NuGet 套件。 呼叫 `AddConsole` 擴充方法：

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

在 JavaScript 用戶端中，有類似的 `configureLogging` 方法。 提供一個 `LogLevel` 值，指出要產生的記錄檔訊息的最小層級。 記錄檔會寫入至瀏覽器主控台視窗。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

`LogLevel`您也可以提供 `string` 代表記錄層級名稱的值，而不是值。 SignalR在您沒有常數存取權的環境中設定記錄時，這會很有用 `LogLevel` 。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

下表列出可用的記錄層級。 您所提供的值， `configureLogging` 用來設定將記錄的 **最小** 記錄層級。 記錄在此層級的訊息， **或是在資料表中所列的層級**，將會被記錄下來。

| String                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info`**或**`information` | `LogLevel.Information` |
| `warn`**或**`warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> 若要完全停用記錄，請 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。

如需有關記錄的詳細資訊，請參閱[ SignalR 診斷檔](xref:signalr/diagnostics)。

SignalRJAVA 用戶端會使用[SLF4J](https://www.slf4j.org/)程式庫來進行記錄。 它是一種高階記錄 API，可讓程式庫的使用者藉由帶入特定的記錄相依性，選擇自己的特定記錄執行。 下列程式碼片段說明如何搭配使用 `java.util.logging` SignalR JAVA 用戶端。

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

如果您未設定相依性中的記錄，SLF4J 會載入預設的無作業記錄器，並顯示下列警告訊息：

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

這可以安全地忽略。

### <a name="configure-allowed-transports"></a>設定允許的傳輸

使用的傳輸 SignalR 可在 `WithUrl` JavaScript) 的呼叫 (中設定 `withUrl` 。 值的位 OR `HttpTransportType` 可以用來限制用戶端只能使用指定的傳輸。 預設會啟用所有傳輸。

例如，若要停用 Server-Sent 事件傳輸，但允許 Websocket 和長時間輪詢連接：

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

在 JavaScript 用戶端中，您可以設定提供的 `transport` 選項物件上的欄位來設定傳輸 `withUrl` ：

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

這一版的 JAVA 用戶端 websocket 是唯一可用的傳輸。

在 JAVA 用戶端中，會使用上的方法來選取傳輸 `withTransport` `HttpHubConnectionBuilder` 。 JAVA 用戶端預設為使用 Websocket 傳輸。

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> SignalRJAVA 用戶端尚不支援傳輸回復。

### <a name="configure-bearer-authentication"></a>設定持有人驗證

若要提供驗證資料和 SignalR 要求，請使用 `AccessTokenProvider` JavaScript) 的選項 (， `accessTokenFactory` 以指定傳回所需存取權杖的函式。 在 .NET 用戶端中，此存取權杖會以 HTTP 「持有人驗證」權杖的形式傳入， (使用 `Authorization`) 類型的標頭 `Bearer` 。 在 JavaScript 用戶端中，存取權杖會用來作為持有人權杖， **但** 在某些情況下，瀏覽器 api 會限制在) 中 Server-Sent 事件和 websocket 要求中套用標頭 (的能力。 在這些情況下，存取權杖會以查詢字串值的形式提供 `access_token` 。

在 .NET 用戶端中，您 `AccessTokenProvider` 可以使用中的 options 委派來指定選項 `WithUrl` ：

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

在 JavaScript 用戶端中，您可以設定 `accessTokenFactory` 中選項物件上的欄位來設定存取權杖 `withUrl` ：

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

在 SignalR JAVA 用戶端中，您可以提供存取權杖 factory 給 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)，以設定用於驗證的持有人權杖。 使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)來提供[RxJAVA](https://github.com/ReactiveX/RxJava) [單一 \<String> ](https://reactivex.io/documentation/single.html)。 只要呼叫 [Single. 延遲](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，您就可以撰寫邏輯來產生用戶端的存取權杖。

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>設定 timeout 和 keep-alive 選項

設定 timeout 和 keep-alive 行為的其他選項可在 `HubConnection` 物件本身取得：

# <a name="net"></a>[.NET](#tab/dotnet)

| 選項 | 預設值 | 描述 |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30秒 (30000 毫秒)  | 伺服器活動的超時時間。 如果伺服器未在此間隔中傳送訊息，用戶端會將伺服器視為已中斷連線，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。 這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。 建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。 |
| `HandshakeTimeout` | 15 秒 | 初始伺服器交握的超時時間。 如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。 這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。 如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。 |
| `KeepAliveInterval` | 15 秒 | 決定用戶端傳送 ping 訊息的間隔。 從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。 如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。 |

在 .NET 用戶端中，超時值會指定為 `TimeSpan` 值。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| 選項 | 預設值 | 描述 |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30秒 (30000 毫秒)  | 伺服器活動的超時時間。 如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onclose` 事件。 這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。 建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。 |
| `keepAliveIntervalInMilliseconds` | 15秒 (15000 毫秒)  | 決定用戶端傳送 ping 訊息的間隔。 從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。 如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。 |

# <a name="java"></a>[Java](#tab/java)

| 選項 | 預設值 | 描述 |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30秒 (30000 毫秒)  | 伺服器活動的超時時間。 如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onClose` 事件。 這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。 建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。 |
| `withHandshakeResponseTimeout` | 15 秒 | 初始伺服器交握的超時時間。 如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並觸發 `onClose` 事件。 這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。 如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。 |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15秒 (15000 毫秒)  | 決定用戶端傳送 ping 訊息的間隔。 從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。 如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。 |

---

### <a name="configure-additional-options"></a>設定其他選項

您可以在 `WithUrl` `withUrl` JavaScript) 方法的 (中， `HubConnectionBuilder` 或在 JAVA 用戶端的不同設定 api 上設定其他選項 `HttpHubConnectionBuilder` ：

# <a name="net"></a>[.NET](#tab/dotnet)

| .NET 選項 |  預設值 | 描述 |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | 傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。 |
| `SkipNegotiation` | `false` | 將此設定為， `true` 以略過協商步驟。 **只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。 使用 Azure 服務時，無法啟用此設定 SignalR 。 |
| `ClientCertificates` | Empty | 要傳送以驗證要求的 TLS 憑證集合。 |
| `Cookies` | Empty | cookie要與每個 HTTP 要求一起傳送的 HTTP 集合。 |
| `Credentials` | Empty | 每個 HTTP 要求所要傳送的認證。 |
| `CloseTimeout` | 5 秒 | 僅限 Websocket。 用戶端在關閉以確認關閉要求後等待的最大時間量。 如果伺服器未在這段時間內確認關閉，用戶端就會中斷連線。 |
| `Headers` | Empty | 要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。 |
| `HttpMessageHandlerFactory` | `null` | 可以用來設定或取代 `HttpMessageHandler` 用來傳送 HTTP 要求的委派。 不用於 WebSocket 連接。 此委派必須傳回非 null 值，且會接收預設值做為參數。 請修改該預設值的設定並傳回，或傳回新的 `HttpMessageHandler` 實例。 **取代處理常式時，請務必從提供的處理常式複製您想要保留的設定，否則設定的選項 (例如 Cookie s 和標頭) 不會套用至新的處理常式。** |
| `Proxy` | `null` | 傳送 HTTP 要求時要使用的 HTTP proxy。 |
| `UseDefaultCredentials` | `false` | 設定此布林值，以傳送 HTTP 和 Websocket 要求的預設認證。 這樣就可以使用 Windows 驗證。 |
| `WebSocketConfiguration` | `null` | 可以用來設定其他 WebSocket 選項的委派。 接收可以用來設定選項的 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 實例。 |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| JavaScript 選項 | 預設值 | 描述 |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | 傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。 |
| `transport` | `null` | <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>值，指定要用於連接的傳輸。 |
| `logMessageContent` | `null` | 設定為， `true` 以記錄用戶端所傳送和接收之訊息的位元組/字元。 |
| `skipNegotiation` | `false` | 將此設定為， `true` 以略過協商步驟。 **只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。 使用 Azure 服務時，無法啟用此設定 SignalR 。 |

# <a name="java"></a>[Java](#tab/java)

| JAVA 選項 | 預設值 | 描述 |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | 傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。 |
| `shouldSkipNegotiate` | `false` | 將此設定為， `true` 以略過協商步驟。 **只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。 使用 Azure 服務時，無法啟用此設定 SignalR 。 |
| `withHeader` `withHeaders` | Empty | 要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。 |

---

在 .NET 用戶端中，這些選項可以由提供給的 options 委派來修改 `WithUrl` ：

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

在 JavaScript 用戶端中，您可以在提供下列功能的 JavaScript 物件中提供這些選項 `withUrl` ：

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

在 JAVA 用戶端中，您可以在傳回的上使用方法來設定這些選項。 `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>其他資源

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a>JSON/MessagePack 序列化選項

ASP.NET Core SignalR 支援兩種用來編碼訊息的通訊協定： [JSON](https://www.json.org/) 和 [MessagePack](https://msgpack.org/index.html)。 每個通訊協定都有序列化設定選項。

您可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)擴充方法在伺服器上設定 JSON 序列化，這可以在您的方法[中 SignalR 加入之後加入](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) `Startup.ConfigureServices` 。 `AddJsonProtocol`方法會使用接收物件的委派 `options` 。 該物件的 [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) 屬性是 JSON.NET `JsonSerializerSettings` 物件，可用來設定引數和傳回值的序列化。 如需詳細資訊，請參閱 [JSON.NET 檔](https://www.newtonsoft.com/json/help/html/Introduction.htm)。
 
舉例來說，若要將序列化程式設定為使用 "PascalCase" 屬性名稱，而不是預設的 "camelCase" 名稱，請在中使用下列程式碼 `Startup.ConfigureServices` ：
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

在 .NET 用戶端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的擴充方法。 `Microsoft.Extensions.DependencyInjection`必須匯入命名空間來解析擴充方法：

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> 目前無法在 JavaScript 用戶端中設定 JSON 序列化。

### <a name="messagepack-serialization-options"></a>MessagePack 序列化選項

您可以藉由提供委派給 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 呼叫來設定 MessagePack 序列化。 如需詳細資訊，請參閱[中 SignalR 的 MessagePack](xref:signalr/messagepackhubprotocol) 。

> [!NOTE]
> 目前無法在 JavaScript 用戶端中設定 MessagePack 序列化。

## <a name="configure-server-options"></a>設定伺服器選項

下表說明設定中樞的選項 SignalR ：

| 選項 | 預設值 | 描述 |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 秒 | 如果用戶端未收到訊息 (（包括在此間隔內保持運作) ），伺服器會將用戶端視為已中斷連線。 這可能需要比此逾時間隔更長的時間，讓用戶端實際標示為已中斷連線，因為這是如何實行的。 建議的值是值的兩倍 `KeepAliveInterval` 。|
| `HandshakeTimeout` | 15 秒 | 如果用戶端未在此時間間隔內傳送初始交握訊息，則連接會關閉。 這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。 如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。 |
| `KeepAliveInterval` | 15 秒 | 如果伺服器未在此間隔內傳送訊息，則會自動傳送 ping 訊息以保持連接開啟。 變更時 `KeepAliveInterval` ，請變更 `ServerTimeout` / `serverTimeoutInMilliseconds` 用戶端上的設定。 建議的 `ServerTimeout` / `serverTimeoutInMilliseconds` 值是值的兩倍 `KeepAliveInterval` 。  |
| `SupportedProtocols` | 所有已安裝的通訊協定 | 此中樞所支援的通訊協定。 預設會允許在伺服器上註冊的所有通訊協定，但是可以從此清單中移除通訊協定，以停用個別中樞的特定通訊協定。 |
| `EnableDetailedErrors` | `false` | 如果為 `true` ，則在中樞方法中擲回例外狀況時，會將詳細的例外狀況訊息傳回給用戶端。 預設值為 `false` ，因為這些例外狀況訊息可能包含機密資訊。 |

您可以為所有中樞設定選項，方法是提供選項委派給 `AddSignalR` 中的呼叫 `Startup.ConfigureServices` 。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

單一中樞的選項會覆寫中提供的全域選項 `AddSignalR` ，可以使用下列方式進行設定 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ：

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Advanced HTTP 設定選項

用 `HttpConnectionDispatcherOptions` 來設定與傳輸和記憶體緩衝區管理相關的 advanced 設定。 這些選項的設定方式是將委派傳遞至中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) `Startup.Configure` 。

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

下表說明設定 ASP.NET Core SignalR ADVANCED HTTP 選項的選項：

| 選項 | 預設值 | 描述 |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | 從伺服器緩衝的用戶端接收的最大位元組數目。 提高此值可讓伺服器接收較大的訊息，但可能會對記憶體耗用量造成負面影響。 |
| `AuthorizationData` | 自動從套用 `Authorize` 至中樞類別的屬性收集的資料。 | 用來判斷是否授權用戶端連線至中樞之 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 物件的清單。 |
| `TransportMaxBufferSize` | 32 KB | 由伺服器緩衝的應用程式所傳送的位元組數目上限。 提高此值可讓伺服器傳送較大的訊息，但可能會對記憶體耗用量造成負面影響。 |
| `Transports` | 所有傳輸都會啟用。 | 值的位旗標列舉 `HttpTransportType` ，可限制用戶端可以用來連接的傳輸。 |
| `LongPolling` | 請參閱下文。 | 長輪詢傳輸特定的其他選項。 |
| `WebSockets` | 請參閱下文。 | Websocket 傳輸特定的其他選項。 |

長時間輪詢傳輸具有可使用屬性設定的其他選項 `LongPolling` ：

| 選項 | 預設值 | 描述 |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90秒 | 在終止單一輪詢要求之前，伺服器等候訊息傳送至用戶端的最大時間量。 減少此值會導致用戶端更頻繁地發出新的投票要求。 |

WebSocket 傳輸具有可使用屬性設定的其他選項 `WebSockets` ：

| 選項 | 預設值 | 描述 |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 秒 | 伺服器關閉之後，如果用戶端在此時間間隔內無法關閉，則連接會終止。 |
| `SubProtocolSelector` | `null` | 可以用來將 `Sec-WebSocket-Protocol` 標頭設定為自訂值的委派。 委派會接收用戶端要求的值做為輸入，而且預期會傳回所需的值。 |

## <a name="configure-client-options"></a>設定用戶端選項

您可以在 `HubConnectionBuilder` .net 和 JavaScript 用戶端) 中提供的類型 (設定用戶端選項。 它也可以在 JAVA 用戶端中使用，但子類別也是包含產生器設定選項以及本身的子 `HttpHubConnectionBuilder` 類別 `HubConnection` 。

### <a name="configure-logging"></a>設定記錄

您可以使用方法，在 .NET 用戶端中設定記錄 `ConfigureLogging` 。 記錄提供者和篩選器的註冊方式與在伺服器上的方式相同。 如需詳細資訊，請參閱 [ASP.NET Core 檔中的記錄](xref:fundamentals/logging/index) 。

> [!NOTE]
> 為了註冊記錄提供者，您必須安裝必要的套件。 如需完整清單，請參閱檔的 [內建記錄提供者](xref:fundamentals/logging/index#built-in-logging-providers) 一節。

例如，若要啟用主控台記錄，請安裝 `Microsoft.Extensions.Logging.Console` NuGet 套件。 呼叫 `AddConsole` 擴充方法：

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

在 JavaScript 用戶端中，有類似的 `configureLogging` 方法。 提供一個 `LogLevel` 值，指出要產生的記錄檔訊息的最小層級。 記錄檔會寫入至瀏覽器主控台視窗。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> 若要完全停用記錄，請 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。

如需有關記錄的詳細資訊，請參閱[ SignalR 診斷檔](xref:signalr/diagnostics)。

SignalRJAVA 用戶端會使用[SLF4J](https://www.slf4j.org/)程式庫來進行記錄。 它是一種高階記錄 API，可讓程式庫的使用者藉由帶入特定的記錄相依性，選擇自己的特定記錄執行。 下列程式碼片段說明如何搭配使用 `java.util.logging` SignalR JAVA 用戶端。

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

如果您未設定相依性中的記錄，SLF4J 會載入預設的無作業記錄器，並顯示下列警告訊息：

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

這可以安全地忽略。

### <a name="configure-allowed-transports"></a>設定允許的傳輸

使用的傳輸 SignalR 可在 `WithUrl` JavaScript) 的呼叫 (中設定 `withUrl` 。 值的位 OR `HttpTransportType` 可以用來限制用戶端只能使用指定的傳輸。 預設會啟用所有傳輸。

例如，若要停用 Server-Sent 事件傳輸，但允許 Websocket 和長時間輪詢連接：

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

在 JavaScript 用戶端中，您可以設定提供的 `transport` 選項物件上的欄位來設定傳輸 `withUrl` ：

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

這一版的 JAVA 用戶端 websocket 是唯一可用的傳輸。

### <a name="configure-bearer-authentication"></a>設定持有人驗證

若要提供驗證資料和 SignalR 要求，請使用 `AccessTokenProvider` JavaScript) 的選項 (， `accessTokenFactory` 以指定傳回所需存取權杖的函式。 在 .NET 用戶端中，此存取權杖會以 HTTP 「持有人驗證」權杖的形式傳入， (使用 `Authorization`) 類型的標頭 `Bearer` 。 在 JavaScript 用戶端中，存取權杖會用來作為持有人權杖， **但** 在某些情況下，瀏覽器 api 會限制在) 中 Server-Sent 事件和 websocket 要求中套用標頭 (的能力。 在這些情況下，存取權杖會以查詢字串值的形式提供 `access_token` 。

在 .NET 用戶端中，您 `AccessTokenProvider` 可以使用中的 options 委派來指定選項 `WithUrl` ：

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

在 JavaScript 用戶端中，您可以設定 `accessTokenFactory` 中選項物件上的欄位來設定存取權杖 `withUrl` ：

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

在 SignalR JAVA 用戶端中，您可以提供存取權杖 factory 給 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)，以設定用於驗證的持有人權杖。 使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)來提供[RxJAVA](https://github.com/ReactiveX/RxJava) [單一 \<String> ](https://reactivex.io/documentation/single.html)。 只要呼叫 [Single. 延遲](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，您就可以撰寫邏輯來產生用戶端的存取權杖。

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>設定 timeout 和 keep-alive 選項

設定 timeout 和 keep-alive 行為的其他選項可在 `HubConnection` 物件本身取得：

# <a name="net"></a>[.NET](#tab/dotnet)

| 選項 | 預設值 | 描述 |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30秒 (30000 毫秒)  | 伺服器活動的超時時間。 如果伺服器未在此間隔中傳送訊息，用戶端會將伺服器視為已中斷連線，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。 這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。 建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。 |
| `HandshakeTimeout` | 15 秒 | 初始伺服器交握的超時時間。 如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。 這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。 如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。 |
| `KeepAliveInterval` | 15 秒 | 決定用戶端傳送 ping 訊息的間隔。 從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。 如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。 |

在 .NET 用戶端中，超時值會指定為 `TimeSpan` 值。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| 選項 | 預設值 | 描述 |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30秒 (30000 毫秒)  | 伺服器活動的超時時間。 如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onclose` 事件。 這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。 建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。 |
| `keepAliveIntervalInMilliseconds` | 15秒 (15000 毫秒)  | 決定用戶端傳送 ping 訊息的間隔。 從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。 如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。 |

# <a name="java"></a>[Java](#tab/java)

| 選項 | 預設值 | 描述 |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30秒 (30000 毫秒)  | 伺服器活動的超時時間。 如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onClose` 事件。 這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。 建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。 |
| `withHandshakeResponseTimeout` | 15 秒 | 初始伺服器交握的超時時間。 如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並觸發 `onClose` 事件。 這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。 如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。 |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15秒 (15000 毫秒)  | 決定用戶端傳送 ping 訊息的間隔。 從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。 如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。 |

---

### <a name="configure-additional-options"></a>設定其他選項

您可以在 `WithUrl` `withUrl` JavaScript) 方法的 (中， `HubConnectionBuilder` 或在 JAVA 用戶端的不同設定 api 上設定其他選項 `HttpHubConnectionBuilder` ：

# <a name="net"></a>[.NET](#tab/dotnet)

| .NET 選項 |  預設值 | 描述 |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | 傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。 |
| `SkipNegotiation` | `false` | 將此設定為， `true` 以略過協商步驟。 **只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。 使用 Azure 服務時，無法啟用此設定 SignalR 。 |
| `ClientCertificates` | Empty | 要傳送以驗證要求的 TLS 憑證集合。 |
| `Cookies` | Empty | cookie要與每個 HTTP 要求一起傳送的 HTTP 集合。 |
| `Credentials` | Empty | 每個 HTTP 要求所要傳送的認證。 |
| `CloseTimeout` | 5 秒 | 僅限 Websocket。 用戶端在關閉以確認關閉要求後等待的最大時間量。 如果伺服器未在這段時間內確認關閉，用戶端就會中斷連線。 |
| `Headers` | Empty | 要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。 |
| `HttpMessageHandlerFactory` | `null` | 可以用來設定或取代 `HttpMessageHandler` 用來傳送 HTTP 要求的委派。 不用於 WebSocket 連接。 此委派必須傳回非 null 值，且會接收預設值做為參數。 請修改該預設值的設定並傳回，或傳回新的 `HttpMessageHandler` 實例。 **取代處理常式時，請務必從提供的處理常式複製您想要保留的設定，否則設定的選項 (例如 Cookie s 和標頭) 不會套用至新的處理常式。** |
| `Proxy` | `null` | 傳送 HTTP 要求時要使用的 HTTP proxy。 |
| `UseDefaultCredentials` | `false` | 設定此布林值，以傳送 HTTP 和 Websocket 要求的預設認證。 這樣就可以使用 Windows 驗證。 |
| `WebSocketConfiguration` | `null` | 可以用來設定其他 WebSocket 選項的委派。 接收可以用來設定選項的 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 實例。 |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| JavaScript 選項 | 預設值 | 描述 |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | 傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。 |
| `transport` | `null` | <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>值，指定要用於連接的傳輸。 |
| `logMessageContent` | `null` | 設定為， `true` 以記錄用戶端所傳送和接收之訊息的位元組/字元。 |
| `skipNegotiation` | `false` | 將此設定為， `true` 以略過協商步驟。 **只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。 使用 Azure 服務時，無法啟用此設定 SignalR 。 |

# <a name="java"></a>[Java](#tab/java)

| JAVA 選項 | 預設值 | 描述 |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | 傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。 |
| `shouldSkipNegotiate` | `false` | 將此設定為， `true` 以略過協商步驟。 **只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。 使用 Azure 服務時，無法啟用此設定 SignalR 。 |
| `withHeader` `withHeaders` | Empty | 要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。 |

---

在 .NET 用戶端中，這些選項可以由提供給的 options 委派來修改 `WithUrl` ：

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

在 JavaScript 用戶端中，您可以在提供下列功能的 JavaScript 物件中提供這些選項 `withUrl` ：

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

在 JAVA 用戶端中，您可以在傳回的上使用方法來設定這些選項。 `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>其他資源

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a>JSON/MessagePack 序列化選項

ASP.NET Core SignalR 支援兩種用來編碼訊息的通訊協定： [JSON](https://www.json.org/) 和 [MessagePack](https://msgpack.org/index.html)。 每個通訊協定都有序列化設定選項。

您可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)擴充方法在伺服器上設定 JSON 序列化，這可以在您的方法[中 SignalR 加入之後加入](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) `Startup.ConfigureServices` 。 `AddJsonProtocol`方法會使用接收物件的委派 `options` 。 該物件的 [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) 屬性是 JSON.NET `JsonSerializerSettings` 物件，可用來設定引數和傳回值的序列化。 如需詳細資訊，請參閱 [JSON.NET 檔](https://www.newtonsoft.com/json/help/html/Introduction.htm)。
 
舉例來說，若要將序列化程式設定為使用 "PascalCase" 屬性名稱，而不是預設的 "camelCase" 名稱，請在中使用下列程式碼 `Startup.ConfigureServices` ：
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

在 .NET 用戶端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的擴充方法。 `Microsoft.Extensions.DependencyInjection`必須匯入命名空間來解析擴充方法：

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> 目前無法在 JavaScript 用戶端中設定 JSON 序列化。

### <a name="messagepack-serialization-options"></a>MessagePack 序列化選項

您可以藉由提供委派給 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 呼叫來設定 MessagePack 序列化。 如需詳細資訊，請參閱[中 SignalR 的 MessagePack](xref:signalr/messagepackhubprotocol) 。

> [!NOTE]
> 目前無法在 JavaScript 用戶端中設定 MessagePack 序列化。

## <a name="configure-server-options"></a>設定伺服器選項

下表說明設定中樞的選項 SignalR ：

| 選項 | 預設值 | 描述 |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | 15 秒 | 如果用戶端未在此時間間隔內傳送初始交握訊息，則連接會關閉。 這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。 如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。 |
| `KeepAliveInterval` | 15 秒 | 如果伺服器未在此間隔內傳送訊息，則會自動傳送 ping 訊息以保持連接開啟。 變更時 `KeepAliveInterval` ，請變更 `ServerTimeout` / `serverTimeoutInMilliseconds` 用戶端上的設定。 建議的 `ServerTimeout` / `serverTimeoutInMilliseconds` 值是值的兩倍 `KeepAliveInterval` 。  |
| `SupportedProtocols` | 所有已安裝的通訊協定 | 此中樞所支援的通訊協定。 預設會允許在伺服器上註冊的所有通訊協定，但是可以從此清單中移除通訊協定，以停用個別中樞的特定通訊協定。 |
| `EnableDetailedErrors` | `false` | 如果為 `true` ，則在中樞方法中擲回例外狀況時，會將詳細的例外狀況訊息傳回給用戶端。 預設值為 `false` ，因為這些例外狀況訊息可能包含機密資訊。 |

您可以為所有中樞設定選項，方法是提供選項委派給 `AddSignalR` 中的呼叫 `Startup.ConfigureServices` 。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

單一中樞的選項會覆寫中提供的全域選項 `AddSignalR` ，可以使用下列方式進行設定 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ：

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Advanced HTTP 設定選項

用 `HttpConnectionDispatcherOptions` 來設定與傳輸和記憶體緩衝區管理相關的 advanced 設定。 這些選項的設定方式是將委派傳遞至中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) `Startup.Configure` 。

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

下表說明設定 ASP.NET Core SignalR ADVANCED HTTP 選項的選項：

| 選項 | 預設值 | 描述 |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | 從伺服器緩衝的用戶端接收的最大位元組數目。 提高此值可讓伺服器接收較大的訊息，但可能會對記憶體耗用量造成負面影響。 |
| `AuthorizationData` | 自動從套用 `Authorize` 至中樞類別的屬性收集的資料。 | 用來判斷是否授權用戶端連線至中樞之 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 物件的清單。 |
| `TransportMaxBufferSize` | 32 KB | 由伺服器緩衝的應用程式所傳送的位元組數目上限。 提高此值可讓伺服器傳送較大的訊息，但可能會對記憶體耗用量造成負面影響。 |
| `Transports` | 所有傳輸都會啟用。 | 值的位旗標列舉 `HttpTransportType` ，可限制用戶端可以用來連接的傳輸。 |
| `LongPolling` | 請參閱下文。 | 長輪詢傳輸特定的其他選項。 |
| `WebSockets` | 請參閱下文。 | Websocket 傳輸特定的其他選項。 |

長時間輪詢傳輸具有可使用屬性設定的其他選項 `LongPolling` ：

| 選項 | 預設值 | 描述 |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90秒 | 在終止單一輪詢要求之前，伺服器等候訊息傳送至用戶端的最大時間量。 減少此值會導致用戶端更頻繁地發出新的投票要求。 |

WebSocket 傳輸具有可使用屬性設定的其他選項 `WebSockets` ：

| 選項 | 預設值 | 描述 |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 秒 | 伺服器關閉之後，如果用戶端在此時間間隔內無法關閉，則連接會終止。 |
| `SubProtocolSelector` | `null` | 可以用來將 `Sec-WebSocket-Protocol` 標頭設定為自訂值的委派。 委派會接收用戶端要求的值做為輸入，而且預期會傳回所需的值。 |

## <a name="configure-client-options"></a>設定用戶端選項

您可以在 `HubConnectionBuilder` .net 和 JavaScript 用戶端) 中提供的類型 (設定用戶端選項。 它也可以在 JAVA 用戶端中使用，但子類別也是包含產生器設定選項以及本身的子 `HttpHubConnectionBuilder` 類別 `HubConnection` 。

### <a name="configure-logging"></a>設定記錄

您可以使用方法，在 .NET 用戶端中設定記錄 `ConfigureLogging` 。 記錄提供者和篩選器的註冊方式與在伺服器上的方式相同。 如需詳細資訊，請參閱 [ASP.NET Core 檔中的記錄](xref:fundamentals/logging/index) 。

> [!NOTE]
> 為了註冊記錄提供者，您必須安裝必要的套件。 如需完整清單，請參閱檔的 [內建記錄提供者](xref:fundamentals/logging/index#built-in-logging-providers) 一節。

例如，若要啟用主控台記錄，請安裝 `Microsoft.Extensions.Logging.Console` NuGet 套件。 呼叫 `AddConsole` 擴充方法：

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

在 JavaScript 用戶端中，有類似的 `configureLogging` 方法。 提供一個 `LogLevel` 值，指出要產生的記錄檔訊息的最小層級。 記錄檔會寫入至瀏覽器主控台視窗。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> 若要完全停用記錄，請 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。

如需有關記錄的詳細資訊，請參閱[ SignalR 診斷檔](xref:signalr/diagnostics)。

SignalRJAVA 用戶端會使用[SLF4J](https://www.slf4j.org/)程式庫來進行記錄。 它是一種高階記錄 API，可讓程式庫的使用者藉由帶入特定的記錄相依性，選擇自己的特定記錄執行。 下列程式碼片段說明如何搭配使用 `java.util.logging` SignalR JAVA 用戶端。

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

如果您未設定相依性中的記錄，SLF4J 會載入預設的無作業記錄器，並顯示下列警告訊息：

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

這可以安全地忽略。

### <a name="configure-allowed-transports"></a>設定允許的傳輸

使用的傳輸 SignalR 可在 `WithUrl` JavaScript) 的呼叫 (中設定 `withUrl` 。 值的位 OR `HttpTransportType` 可以用來限制用戶端只能使用指定的傳輸。 預設會啟用所有傳輸。

例如，若要停用 Server-Sent 事件傳輸，但允許 Websocket 和長時間輪詢連接：

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

在 JavaScript 用戶端中，您可以設定提供的 `transport` 選項物件上的欄位來設定傳輸 `withUrl` ：

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a>設定持有人驗證

若要提供驗證資料和 SignalR 要求，請使用 `AccessTokenProvider` JavaScript) 的選項 (， `accessTokenFactory` 以指定傳回所需存取權杖的函式。 在 .NET 用戶端中，此存取權杖會以 HTTP 「持有人驗證」權杖的形式傳入， (使用 `Authorization`) 類型的標頭 `Bearer` 。 在 JavaScript 用戶端中，存取權杖會用來作為持有人權杖， **但** 在某些情況下，瀏覽器 api 會限制在) 中 Server-Sent 事件和 websocket 要求中套用標頭 (的能力。 在這些情況下，存取權杖會以查詢字串值的形式提供 `access_token` 。

在 .NET 用戶端中，您 `AccessTokenProvider` 可以使用中的 options 委派來指定選項 `WithUrl` ：

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

在 JavaScript 用戶端中，您可以設定 `accessTokenFactory` 中選項物件上的欄位來設定存取權杖 `withUrl` ：

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

在 SignalR JAVA 用戶端中，您可以提供存取權杖 factory 給 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)，以設定用於驗證的持有人權杖。 使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)來提供[RxJAVA](https://github.com/ReactiveX/RxJava) [單一 \<String> ](https://reactivex.io/documentation/single.html)。 只要呼叫 [Single. 延遲](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，您就可以撰寫邏輯來產生用戶端的存取權杖。

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>設定 timeout 和 keep-alive 選項

設定 timeout 和 keep-alive 行為的其他選項可在 `HubConnection` 物件本身取得：

# <a name="net"></a>[.NET](#tab/dotnet)

| 選項 | 預設值 | 描述 |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30秒 (30000 毫秒)  | 伺服器活動的超時時間。 如果伺服器未在此間隔中傳送訊息，用戶端會將伺服器視為已中斷連線，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。 這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。 建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。 |
| `HandshakeTimeout` | 15 秒 | 初始伺服器交握的超時時間。 如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。 這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。 如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。 |

在 .NET 用戶端中，超時值會指定為 `TimeSpan` 值。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| 選項 | 預設值 | 描述 |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30秒 (30000 毫秒)  | 伺服器活動的超時時間。 如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onclose` 事件。 這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。 建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。 |

# <a name="java"></a>[Java](#tab/java)

| 選項 | 預設值 | 描述 |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30秒 (30000 毫秒)  | 伺服器活動的超時時間。 如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onClose` 事件。 這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。 建議的值是至少兩個伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。 |
| `withHandshakeResponseTimeout` | 15 秒 | 初始伺服器交握的超時時間。 如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並觸發 `onClose` 事件。 這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。 如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。 |

---

### <a name="configure-additional-options"></a>設定其他選項

您可以在 `WithUrl` `withUrl` JavaScript) 方法的 (中， `HubConnectionBuilder` 或在 JAVA 用戶端的不同設定 api 上設定其他選項 `HttpHubConnectionBuilder` ：

# <a name="net"></a>[.NET](#tab/dotnet)

| .NET 選項 |  預設值 | 描述 |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | 傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。 |
| `SkipNegotiation` | `false` | 將此設定為， `true` 以略過協商步驟。 **只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。 使用 Azure 服務時，無法啟用此設定 SignalR 。 |
| `ClientCertificates` | Empty | 要傳送以驗證要求的 TLS 憑證集合。 |
| `Cookies` | Empty | cookie要與每個 HTTP 要求一起傳送的 HTTP 集合。 |
| `Credentials` | Empty | 每個 HTTP 要求所要傳送的認證。 |
| `CloseTimeout` | 5 秒 | 僅限 Websocket。 用戶端在關閉以確認關閉要求後等待的最大時間量。 如果伺服器未在這段時間內確認關閉，用戶端就會中斷連線。 |
| `Headers` | Empty | 要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。 |
| `HttpMessageHandlerFactory` | `null` | 可以用來設定或取代 `HttpMessageHandler` 用來傳送 HTTP 要求的委派。 不用於 WebSocket 連接。 此委派必須傳回非 null 值，且會接收預設值做為參數。 請修改該預設值的設定並傳回，或傳回新的 `HttpMessageHandler` 實例。 **取代處理常式時，請務必從提供的處理常式複製您想要保留的設定，否則設定的選項 (例如 Cookie s 和標頭) 不會套用至新的處理常式。** |
| `Proxy` | `null` | 傳送 HTTP 要求時要使用的 HTTP proxy。 |
| `UseDefaultCredentials` | `false` | 設定此布林值，以傳送 HTTP 和 Websocket 要求的預設認證。 這樣就可以使用 Windows 驗證。 |
| `WebSocketConfiguration` | `null` | 可以用來設定其他 WebSocket 選項的委派。 接收可以用來設定選項的 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 實例。 |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| JavaScript 選項 | 預設值 | 描述 |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | 傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。 |
| `transport` | `null` | <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>值，指定要用於連接的傳輸。 |
| `logMessageContent` | `null` | 設定為， `true` 以記錄用戶端所傳送和接收之訊息的位元組/字元。 |
| `skipNegotiation` | `false` | 將此設定為， `true` 以略過協商步驟。 **只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。 使用 Azure 服務時，無法啟用此設定 SignalR 。 |

# <a name="java"></a>[Java](#tab/java)

| JAVA 選項 | 預設值 | 描述 |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | 傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。 |
| `shouldSkipNegotiate` | `false` | 將此設定為， `true` 以略過協商步驟。 **只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。 使用 Azure 服務時，無法啟用此設定 SignalR 。 |
| `withHeader` `withHeaders` | Empty | 要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。 |

---

在 .NET 用戶端中，這些選項可以由提供給的 options 委派來修改 `WithUrl` ：

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

在 JavaScript 用戶端中，您可以在提供下列功能的 JavaScript 物件中提供這些選項 `withUrl` ：

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

在 JAVA 用戶端中，您可以在傳回的上使用方法來設定這些選項。 `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>其他資源

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
