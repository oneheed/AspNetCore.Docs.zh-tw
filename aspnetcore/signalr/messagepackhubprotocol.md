---
title: 使用中的 MessagePack Hub 通訊協定 SignalR 進行 ASP.NET Core
author: bradygaster
description: 將 MessagePack 中樞通訊協定新增至 ASP.NET Core SignalR 。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/13/2020
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
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: ab9bd11e37182f5b24db5595d5d050f4cc0e32da
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626645"
---
# <a name="use-messagepack-hub-protocol-in-no-locsignalr-for-aspnet-core"></a>使用中的 MessagePack Hub 通訊協定 SignalR 進行 ASP.NET Core

::: moniker range=">= aspnetcore-5.0"

本文假設讀者已熟悉 [開始](xref:tutorials/signalr)所涵蓋的主題。

## <a name="what-is-messagepack"></a>什麼是 MessagePack？

[MessagePack](https://msgpack.org/index.html) 是一種快速且精簡的二進位序列化格式。 當效能和頻寬有問題時，這會很有用，因為它會建立比 [JSON](https://www.json.org/)更小的訊息。 在查看網路追蹤和記錄檔時，除非透過 MessagePack 剖析器傳遞位元組，否則二進位訊息無法讀取。 SignalR 內建支援 MessagePack 格式，並提供 Api 供用戶端和伺服器使用。

## <a name="configure-messagepack-on-the-server"></a>在伺服器上設定 MessagePack

若要在伺服器上啟用 MessagePack Hub 通訊協定，請 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 在您的應用程式中安裝套件。 在 `Startup.ConfigureServices` 方法中，新增 `AddMessagePackProtocol` 至在 `AddSignalR` 伺服器上啟用 MessagePack 支援的呼叫。

> [!NOTE]
> 預設會啟用 JSON。 新增 MessagePack 可支援 JSON 和 MessagePack 用戶端。

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

若要自訂 MessagePack 將如何格式化您的資料，請 `AddMessagePackProtocol` 使用委派來設定選項。 在該委派中， `SerializerOptions` 屬性可以用來設定 MessagePack 序列化選項。 如需解析程式如何運作的詳細資訊，請造訪 MessagePack library，網址為 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)。 屬性可用於您要序列化的物件，以定義應該如何處理它們。

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(new CustomResolver())
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

> [!WARNING]
> 強烈建議您檢查 [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) 並套用建議的修補程式。 例如， `.WithSecurity(MessagePackSecurity.UntrustedData)` 在取代時呼叫 `SerializerOptions` 。

## <a name="configure-messagepack-on-the-client"></a>在用戶端上設定 MessagePack

> [!NOTE]
> 針對支援的用戶端，預設會啟用 JSON。 用戶端只能支援單一通訊協定。 新增 MessagePack 支援將會取代任何先前設定的通訊協定。

### <a name="net-client"></a>.NET 用戶端

若要在 .NET 用戶端中啟用 MessagePack，請安裝 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 封裝並 `AddMessagePackProtocol` 在上呼叫 `HubConnectionBuilder` 。

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> 此 `AddMessagePackProtocol` 呼叫會採用委派來設定選項，就像伺服器一樣。

### <a name="javascript-client"></a>JavaScript 用戶端

JavaScript 用戶端的 MessagePack 支援是由 [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm 封裝提供。 在命令 shell 中執行下列命令，以安裝套件：

```bash
npm install @microsoft/signalr-protocol-msgpack
```

安裝 npm 套件之後，您可以透過 JavaScript 模組載入器直接使用模組，或藉由參考下列檔案將其匯入至瀏覽器：

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

在瀏覽器中， `msgpack5` 也必須參考程式庫。 使用 `<script>` 標記來建立參考。 您可以在 *node_modules\msgpack5\dist\msgpack5.js*找到該程式庫。

> [!NOTE]
> 使用專案時 `<script>` ，順序很重要。 如果在*msgpack5.js*之前參考*signalr-protocol-msgpack.js* ，則嘗試使用 MessagePack 連接時，就會發生錯誤。 *signalr-protocol-msgpack.js*之前也需要*signalr.js* 。

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

新增 `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` 至 `HubConnectionBuilder` 會將用戶端設定為在連接到伺服器時使用 MessagePack 通訊協定。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> 目前，JavaScript 用戶端上沒有 MessagePack 通訊協定的設定選項。

## <a name="messagepack-quirks"></a>MessagePack 的不相容

使用 MessagePack 中樞通訊協定時，有幾個要注意的問題。

### <a name="messagepack-is-case-sensitive"></a>MessagePack 區分大小寫

MessagePack 通訊協定會區分大小寫。 例如，請考慮下列 c # 類別：

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

從 JavaScript 用戶端傳送時，您必須使用 `PascalCased` 屬性名稱，因為大小寫必須完全符合 c # 類別。 例如：

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

使用 `camelCased` 名稱並不會正確地系結至 c # 類別。 您可以使用 `Key` 屬性來為 MessagePack 屬性指定不同的名稱，藉此解決這個問題。 如需詳細資訊，請參閱 [MessagePack-CSharp 檔](https://github.com/neuecc/MessagePack-CSharp#object-serialization)。

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>序列化/還原序列化時，不會保留 DateTime. 種類

MessagePack 通訊協定不提供將的值編碼的方式 `Kind` `DateTime` 。 如此一來，當您將日期還原序列化時，如果是，MessagePack Hub 通訊協定將會轉換成 UTC 格式， `DateTime.Kind` `DateTimeKind.Local` 否則它將不會觸及時間並依原樣傳遞。 如果您正在使用 `DateTime` 值，建議您在傳送之前轉換為 UTC。 當您收到時，請將它們從 UTC 轉換為當地時間。

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>JavaScript 中的 MessagePack 不支援 MinValue

JavaScript 用戶端所使用的 [msgpack5](https://github.com/mcollina/msgpack5) 程式庫 SignalR 不支援 `timestamp96` MessagePack 中的類型。 這種類型是用來編碼非常大的日期值， (在過去或未來的) 中非常早。 的值 `DateTime.MinValue` 為 `January 1, 0001` ，必須以值進行編碼 `timestamp96` 。 基於這個原因， `DateTime.MinValue` 不支援傳送至 JavaScript 用戶端。 當 `DateTime.MinValue` JavaScript 用戶端收到時，會擲回下列錯誤：

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

通常 `DateTime.MinValue` 是用來編碼「遺失」或 `null` 值。 如果您需要在 MessagePack 中編碼該值，請使用可為 null 的 `DateTime` 值 (`DateTime?`) 或編碼個別 `bool` 值，以指出日期是否存在。

如需這項限制的詳細資訊，請參閱 GitHub 問題 [aspnet/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228)。

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>在「事先」編譯環境中 MessagePack 支援

.NET 用戶端和伺服器所使用的 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) 程式庫會使用程式碼產生，將序列化優化。 因此，在使用「預先」編譯 (（例如 Xamarin iOS 或 Unity) ）的環境上，預設不會支援此方法。 您可以藉由「預先產生」序列化程式/還原序列化程式碼，在這些環境中使用 MessagePack。 如需詳細資訊，請參閱 [MessagePack-CSharp 檔](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin)。 當您預先產生序列化程式之後，您可以使用傳遞至的設定委派來註冊它們 `AddMessagePackProtocol` ：

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        StaticCompositeResolver.Instance.Register(
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        );
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(StaticCompositeResolver.Instance)
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a>MessagePack 中的類型檢查較嚴格

JSON Hub 通訊協定會在還原序列化期間執行類型轉換。 例如，如果傳入物件的屬性值是數位 (`{ foo: 42 }`) 但 .net 類別上的屬性是類型 `string` ，則值將會轉換。 但是，MessagePack 不會執行這項轉換，而且會擲回例外狀況，在伺服器端記錄檔 (和主控台) 中會出現此例外狀況：

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

如需這項限制的詳細資訊，請參閱 GitHub 問題 [aspnet/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937)。

## <a name="related-resources"></a>相關資源

* [開始使用](xref:tutorials/signalr)
* [.NET 用戶端](xref:signalr/dotnet-client)
* [JavaScript 用戶端](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

本文假設讀者已熟悉 [開始](xref:tutorials/signalr)所涵蓋的主題。

## <a name="what-is-messagepack"></a>什麼是 MessagePack？

[MessagePack](https://msgpack.org/index.html) 是一種快速且精簡的二進位序列化格式。 當效能和頻寬有問題時，這會很有用，因為它會建立比 [JSON](https://www.json.org/)更小的訊息。 在查看網路追蹤和記錄檔時，除非透過 MessagePack 剖析器傳遞位元組，否則二進位訊息無法讀取。 SignalR 內建支援 MessagePack 格式，並提供 Api 供用戶端和伺服器使用。

## <a name="configure-messagepack-on-the-server"></a>在伺服器上設定 MessagePack

若要在伺服器上啟用 MessagePack Hub 通訊協定，請 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 在您的應用程式中安裝套件。 在 `Startup.ConfigureServices` 方法中，新增 `AddMessagePackProtocol` 至在 `AddSignalR` 伺服器上啟用 MessagePack 支援的呼叫。

> [!NOTE]
> 預設會啟用 JSON。 新增 MessagePack 可支援 JSON 和 MessagePack 用戶端。

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

若要自訂 MessagePack 將如何格式化您的資料，請 `AddMessagePackProtocol` 使用委派來設定選項。 在該委派中， `FormatterResolvers` 屬性可以用來設定 MessagePack 序列化選項。 如需解析程式如何運作的詳細資訊，請造訪 MessagePack library，網址為 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)。 屬性可用於您要序列化的物件，以定義應該如何處理它們。

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> 強烈建議您檢查 [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) 並套用建議的修補程式。 例如，將 `MessagePackSecurity.Active` 靜態屬性設定為 `MessagePackSecurity.UntrustedData` 。 設定 `MessagePackSecurity.Active` 需要手動安裝 MessagePack 的 [1.9. x 版](https://www.nuget.org/packages/MessagePack/1.9.3)。 安裝 `MessagePack` 1.9. x 版升級時 SignalR 使用。 當未 `MessagePackSecurity.Active` 設定為時 `MessagePackSecurity.UntrustedData` ，惡意用戶端可能會造成阻絕服務。 `MessagePackSecurity.Active`在中設定 `Program.Main` ，如下列程式碼所示：

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a>在用戶端上設定 MessagePack

> [!NOTE]
> 針對支援的用戶端，預設會啟用 JSON。 用戶端只能支援單一通訊協定。 新增 MessagePack 支援將會取代任何先前設定的通訊協定。

### <a name="net-client"></a>.NET 用戶端

若要在 .NET 用戶端中啟用 MessagePack，請安裝 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 封裝並 `AddMessagePackProtocol` 在上呼叫 `HubConnectionBuilder` 。

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> 此 `AddMessagePackProtocol` 呼叫會採用委派來設定選項，就像伺服器一樣。

### <a name="javascript-client"></a>JavaScript 用戶端

JavaScript 用戶端的 MessagePack 支援是由 [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm 封裝提供。 在命令 shell 中執行下列命令，以安裝套件：

```bash
npm install @microsoft/signalr-protocol-msgpack
```

安裝 npm 套件之後，您可以透過 JavaScript 模組載入器直接使用模組，或藉由參考下列檔案將其匯入至瀏覽器：

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

在瀏覽器中， `msgpack5` 也必須參考程式庫。 使用 `<script>` 標記來建立參考。 您可以在 *node_modules\msgpack5\dist\msgpack5.js*找到該程式庫。

> [!NOTE]
> 使用專案時 `<script>` ，順序很重要。 如果在*msgpack5.js*之前參考*signalr-protocol-msgpack.js* ，則嘗試使用 MessagePack 連接時，就會發生錯誤。 *signalr-protocol-msgpack.js*之前也需要*signalr.js* 。

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

新增 `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` 至 `HubConnectionBuilder` 會將用戶端設定為在連接到伺服器時使用 MessagePack 通訊協定。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> 目前，JavaScript 用戶端上沒有 MessagePack 通訊協定的設定選項。

## <a name="messagepack-quirks"></a>MessagePack 的不相容

使用 MessagePack 中樞通訊協定時，有幾個要注意的問題。

### <a name="messagepack-is-case-sensitive"></a>MessagePack 區分大小寫

MessagePack 通訊協定會區分大小寫。 例如，請考慮下列 c # 類別：

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

從 JavaScript 用戶端傳送時，您必須使用 `PascalCased` 屬性名稱，因為大小寫必須完全符合 c # 類別。 例如：

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

使用 `camelCased` 名稱並不會正確地系結至 c # 類別。 您可以使用 `Key` 屬性來為 MessagePack 屬性指定不同的名稱，藉此解決這個問題。 如需詳細資訊，請參閱 [MessagePack-CSharp 檔](https://github.com/neuecc/MessagePack-CSharp#object-serialization)。

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>序列化/還原序列化時，不會保留 DateTime. 種類

MessagePack 通訊協定不提供將的值編碼的方式 `Kind` `DateTime` 。 如此一來，在還原序列化日期時，MessagePack Hub 通訊協定會假設內送日期採用 UTC 格式。 如果您是 `DateTime` 以當地時間處理值，建議您在傳送之前轉換成 UTC。 當您收到時，請將它們從 UTC 轉換為當地時間。

如需這項限制的詳細資訊，請參閱 GitHub 問題 [aspnet/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632)。

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>JavaScript 中的 MessagePack 不支援 MinValue

JavaScript 用戶端所使用的 [msgpack5](https://github.com/mcollina/msgpack5) 程式庫 SignalR 不支援 `timestamp96` MessagePack 中的類型。 這種類型是用來編碼非常大的日期值， (在過去或未來的) 中非常早。 的值 `DateTime.MinValue` 為 `January 1, 0001` ，必須以值進行編碼 `timestamp96` 。 基於這個原因， `DateTime.MinValue` 不支援傳送至 JavaScript 用戶端。 當 `DateTime.MinValue` JavaScript 用戶端收到時，會擲回下列錯誤：

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

通常 `DateTime.MinValue` 是用來編碼「遺失」或 `null` 值。 如果您需要在 MessagePack 中編碼該值，請使用可為 null 的 `DateTime` 值 (`DateTime?`) 或編碼個別 `bool` 值，以指出日期是否存在。

如需這項限制的詳細資訊，請參閱 GitHub 問題 [aspnet/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228)。

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>在「事先」編譯環境中 MessagePack 支援

.NET 用戶端和伺服器所使用的 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) 程式庫會使用程式碼產生，將序列化優化。 因此，在使用「預先」編譯 (（例如 Xamarin iOS 或 Unity) ）的環境上，預設不會支援此方法。 您可以藉由「預先產生」序列化程式/還原序列化程式碼，在這些環境中使用 MessagePack。 如需詳細資訊，請參閱 [MessagePack-CSharp 檔](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports)。 當您預先產生序列化程式之後，您可以使用傳遞至的設定委派來註冊它們 `AddMessagePackProtocol` ：

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a>MessagePack 中的類型檢查較嚴格

JSON Hub 通訊協定會在還原序列化期間執行類型轉換。 例如，如果傳入物件的屬性值是數位 (`{ foo: 42 }`) 但 .net 類別上的屬性是類型 `string` ，則值將會轉換。 但是，MessagePack 不會執行這項轉換，而且會擲回例外狀況，在伺服器端記錄檔 (和主控台) 中會出現此例外狀況：

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

如需這項限制的詳細資訊，請參閱 GitHub 問題 [aspnet/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937)。

## <a name="related-resources"></a>相關資源

* [開始使用](xref:tutorials/signalr)
* [.NET 用戶端](xref:signalr/dotnet-client)
* [JavaScript 用戶端](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

本文假設讀者已熟悉 [開始](xref:tutorials/signalr)所涵蓋的主題。

## <a name="what-is-messagepack"></a>什麼是 MessagePack？

[MessagePack](https://msgpack.org/index.html) 是一種快速且精簡的二進位序列化格式。 當效能和頻寬有問題時，這會很有用，因為它會建立比 [JSON](https://www.json.org/)更小的訊息。 在查看網路追蹤和記錄檔時，除非透過 MessagePack 剖析器傳遞位元組，否則二進位訊息無法讀取。 SignalR 內建支援 MessagePack 格式，並提供 Api 供用戶端和伺服器使用。

## <a name="configure-messagepack-on-the-server"></a>在伺服器上設定 MessagePack

若要在伺服器上啟用 MessagePack Hub 通訊協定，請 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 在您的應用程式中安裝套件。 在 `Startup.ConfigureServices` 方法中，新增 `AddMessagePackProtocol` 至在 `AddSignalR` 伺服器上啟用 MessagePack 支援的呼叫。

> [!NOTE]
> 預設會啟用 JSON。 新增 MessagePack 可支援 JSON 和 MessagePack 用戶端。

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

若要自訂 MessagePack 將如何格式化您的資料，請 `AddMessagePackProtocol` 使用委派來設定選項。 在該委派中， `FormatterResolvers` 屬性可以用來設定 MessagePack 序列化選項。 如需解析程式如何運作的詳細資訊，請造訪 MessagePack library，網址為 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)。 屬性可用於您要序列化的物件，以定義應該如何處理它們。

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> 強烈建議您檢查 [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) 並套用建議的修補程式。 例如，將 `MessagePackSecurity.Active` 靜態屬性設定為 `MessagePackSecurity.UntrustedData` 。 設定 `MessagePackSecurity.Active` 需要手動安裝 MessagePack 的 [1.9. x 版](https://www.nuget.org/packages/MessagePack/1.9.3)。 安裝 `MessagePack` 1.9. x 版升級時 SignalR 使用。 當未 `MessagePackSecurity.Active` 設定為時 `MessagePackSecurity.UntrustedData` ，惡意用戶端可能會造成阻絕服務。 `MessagePackSecurity.Active`在中設定 `Program.Main` ，如下列程式碼所示：

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a>在用戶端上設定 MessagePack

> [!NOTE]
> 針對支援的用戶端，預設會啟用 JSON。 用戶端只能支援單一通訊協定。 新增 MessagePack 支援將會取代任何先前設定的通訊協定。

### <a name="net-client"></a>.NET 用戶端

若要在 .NET 用戶端中啟用 MessagePack，請安裝 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 封裝並 `AddMessagePackProtocol` 在上呼叫 `HubConnectionBuilder` 。

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> 此 `AddMessagePackProtocol` 呼叫會採用委派來設定選項，就像伺服器一樣。

### <a name="javascript-client"></a>JavaScript 用戶端

JavaScript 用戶端的 MessagePack 支援是由 [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm 封裝提供。 在命令 shell 中執行下列命令，以安裝套件：

```bash
npm install @aspnet/signalr-protocol-msgpack
```

安裝 npm 套件之後，您可以透過 JavaScript 模組載入器直接使用模組，或藉由參考下列檔案將其匯入至瀏覽器：

*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*

在瀏覽器中， `msgpack5` 也必須參考程式庫。 使用 `<script>` 標記來建立參考。 您可以在 *node_modules\msgpack5\dist\msgpack5.js*找到該程式庫。

> [!NOTE]
> 使用專案時 `<script>` ，順序很重要。 如果在*msgpack5.js*之前參考*signalr-protocol-msgpack.js* ，則嘗試使用 MessagePack 連接時，就會發生錯誤。 *signalr-protocol-msgpack.js*之前也需要*signalr.js* 。

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

新增 `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` 至 `HubConnectionBuilder` 會將用戶端設定為在連接到伺服器時使用 MessagePack 通訊協定。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> 目前，JavaScript 用戶端上沒有 MessagePack 通訊協定的設定選項。

## <a name="messagepack-quirks"></a>MessagePack 的不相容

使用 MessagePack 中樞通訊協定時，有幾個要注意的問題。

### <a name="messagepack-is-case-sensitive"></a>MessagePack 區分大小寫

MessagePack 通訊協定會區分大小寫。 例如，請考慮下列 c # 類別：

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

從 JavaScript 用戶端傳送時，您必須使用 `PascalCased` 屬性名稱，因為大小寫必須完全符合 c # 類別。 例如：

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

使用 `camelCased` 名稱並不會正確地系結至 c # 類別。 您可以使用 `Key` 屬性來為 MessagePack 屬性指定不同的名稱，藉此解決這個問題。 如需詳細資訊，請參閱 [MessagePack-CSharp 檔](https://github.com/neuecc/MessagePack-CSharp#object-serialization)。

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>序列化/還原序列化時，不會保留 DateTime. 種類

MessagePack 通訊協定不提供將的值編碼的方式 `Kind` `DateTime` 。 如此一來，在還原序列化日期時，MessagePack Hub 通訊協定會假設內送日期採用 UTC 格式。 如果您是 `DateTime` 以當地時間處理值，建議您在傳送之前轉換成 UTC。 當您收到時，請將它們從 UTC 轉換為當地時間。

如需這項限制的詳細資訊，請參閱 GitHub 問題 [aspnet/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632)。

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>JavaScript 中的 MessagePack 不支援 MinValue

JavaScript 用戶端所使用的 [msgpack5](https://github.com/mcollina/msgpack5) 程式庫 SignalR 不支援 `timestamp96` MessagePack 中的類型。 這種類型是用來編碼非常大的日期值， (在過去或未來的) 中非常早。 的值 `DateTime.MinValue` `January 1, 0001` 必須以值進行編碼 `timestamp96` 。 基於這個原因， `DateTime.MinValue` 不支援傳送至 JavaScript 用戶端。 當 `DateTime.MinValue` JavaScript 用戶端收到時，會擲回下列錯誤：

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

通常 `DateTime.MinValue` 是用來編碼「遺失」或 `null` 值。 如果您需要在 MessagePack 中編碼該值，請使用可為 null 的 `DateTime` 值 (`DateTime?`) 或編碼個別 `bool` 值，以指出日期是否存在。

如需這項限制的詳細資訊，請參閱 GitHub 問題 [aspnet/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228)。

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>在「事先」編譯環境中 MessagePack 支援

.NET 用戶端和伺服器所使用的 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) 程式庫會使用程式碼產生，將序列化優化。 因此，在使用「預先」編譯 (（例如 Xamarin iOS 或 Unity) ）的環境上，預設不會支援此方法。 您可以藉由「預先產生」序列化程式/還原序列化程式碼，在這些環境中使用 MessagePack。 如需詳細資訊，請參閱 [MessagePack-CSharp 檔](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports)。 當您預先產生序列化程式之後，您可以使用傳遞至的設定委派來註冊它們 `AddMessagePackProtocol` ：

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a>MessagePack 中的類型檢查較嚴格

JSON Hub 通訊協定會在還原序列化期間執行類型轉換。 例如，如果傳入物件的屬性值是數位 (`{ foo: 42 }`) 但 .net 類別上的屬性是類型 `string` ，則值將會轉換。 但是，MessagePack 不會執行這項轉換，而且會擲回例外狀況，在伺服器端記錄檔 (和主控台) 中會出現此例外狀況：

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

如需這項限制的詳細資訊，請參閱 GitHub 問題 [aspnet/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937)。

## <a name="related-resources"></a>相關資源

* [開始使用](xref:tutorials/signalr)
* [.NET 用戶端](xref:signalr/dotnet-client)
* [JavaScript 用戶端](xref:signalr/javascript-client)

::: moniker-end
