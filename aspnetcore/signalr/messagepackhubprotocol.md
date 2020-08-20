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
# <a name="use-messagepack-hub-protocol-in-no-locsignalr-for-aspnet-core"></a><span data-ttu-id="0c0e4-103">使用中的 MessagePack Hub 通訊協定 SignalR 進行 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0c0e4-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="0c0e4-104">本文假設讀者已熟悉 [開始](xref:tutorials/signalr)所涵蓋的主題。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="0c0e4-105">什麼是 MessagePack？</span><span class="sxs-lookup"><span data-stu-id="0c0e4-105">What is MessagePack?</span></span>

<span data-ttu-id="0c0e4-106">[MessagePack](https://msgpack.org/index.html) 是一種快速且精簡的二進位序列化格式。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="0c0e4-107">當效能和頻寬有問題時，這會很有用，因為它會建立比 [JSON](https://www.json.org/)更小的訊息。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="0c0e4-108">在查看網路追蹤和記錄檔時，除非透過 MessagePack 剖析器傳遞位元組，否則二進位訊息無法讀取。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="0c0e4-109">SignalR 內建支援 MessagePack 格式，並提供 Api 供用戶端和伺服器使用。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-109">SignalR has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="0c0e4-110">在伺服器上設定 MessagePack</span><span class="sxs-lookup"><span data-stu-id="0c0e4-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="0c0e4-111">若要在伺服器上啟用 MessagePack Hub 通訊協定，請 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 在您的應用程式中安裝套件。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="0c0e4-112">在 `Startup.ConfigureServices` 方法中，新增 `AddMessagePackProtocol` 至在 `AddSignalR` 伺服器上啟用 MessagePack 支援的呼叫。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="0c0e4-113">預設會啟用 JSON。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-113">JSON is enabled by default.</span></span> <span data-ttu-id="0c0e4-114">新增 MessagePack 可支援 JSON 和 MessagePack 用戶端。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="0c0e4-115">若要自訂 MessagePack 將如何格式化您的資料，請 `AddMessagePackProtocol` 使用委派來設定選項。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="0c0e4-116">在該委派中， `SerializerOptions` 屬性可以用來設定 MessagePack 序列化選項。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="0c0e4-117">如需解析程式如何運作的詳細資訊，請造訪 MessagePack library，網址為 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="0c0e4-118">屬性可用於您要序列化的物件，以定義應該如何處理它們。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="0c0e4-119">強烈建議您檢查 [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) 並套用建議的修補程式。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="0c0e4-120">例如， `.WithSecurity(MessagePackSecurity.UntrustedData)` 在取代時呼叫 `SerializerOptions` 。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="0c0e4-121">在用戶端上設定 MessagePack</span><span class="sxs-lookup"><span data-stu-id="0c0e4-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="0c0e4-122">針對支援的用戶端，預設會啟用 JSON。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="0c0e4-123">用戶端只能支援單一通訊協定。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="0c0e4-124">新增 MessagePack 支援將會取代任何先前設定的通訊協定。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="0c0e4-125">.NET 用戶端</span><span class="sxs-lookup"><span data-stu-id="0c0e4-125">.NET client</span></span>

<span data-ttu-id="0c0e4-126">若要在 .NET 用戶端中啟用 MessagePack，請安裝 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 封裝並 `AddMessagePackProtocol` 在上呼叫 `HubConnectionBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="0c0e4-127">此 `AddMessagePackProtocol` 呼叫會採用委派來設定選項，就像伺服器一樣。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="0c0e4-128">JavaScript 用戶端</span><span class="sxs-lookup"><span data-stu-id="0c0e4-128">JavaScript client</span></span>

<span data-ttu-id="0c0e4-129">JavaScript 用戶端的 MessagePack 支援是由 [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm 封裝提供。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="0c0e4-130">在命令 shell 中執行下列命令，以安裝套件：</span><span class="sxs-lookup"><span data-stu-id="0c0e4-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="0c0e4-131">安裝 npm 套件之後，您可以透過 JavaScript 模組載入器直接使用模組，或藉由參考下列檔案將其匯入至瀏覽器：</span><span class="sxs-lookup"><span data-stu-id="0c0e4-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="0c0e4-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="0c0e4-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="0c0e4-133">在瀏覽器中， `msgpack5` 也必須參考程式庫。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="0c0e4-134">使用 `<script>` 標記來建立參考。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="0c0e4-135">您可以在 *node_modules\msgpack5\dist\msgpack5.js*找到該程式庫。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="0c0e4-136">使用專案時 `<script>` ，順序很重要。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="0c0e4-137">如果在*msgpack5.js*之前參考*signalr-protocol-msgpack.js* ，則嘗試使用 MessagePack 連接時，就會發生錯誤。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="0c0e4-138">*signalr-protocol-msgpack.js*之前也需要*signalr.js* 。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-138">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="0c0e4-139">新增 `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` 至 `HubConnectionBuilder` 會將用戶端設定為在連接到伺服器時使用 MessagePack 通訊協定。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="0c0e4-140">目前，JavaScript 用戶端上沒有 MessagePack 通訊協定的設定選項。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="0c0e4-141">MessagePack 的不相容</span><span class="sxs-lookup"><span data-stu-id="0c0e4-141">MessagePack quirks</span></span>

<span data-ttu-id="0c0e4-142">使用 MessagePack 中樞通訊協定時，有幾個要注意的問題。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-142">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="0c0e4-143">MessagePack 區分大小寫</span><span class="sxs-lookup"><span data-stu-id="0c0e4-143">MessagePack is case-sensitive</span></span>

<span data-ttu-id="0c0e4-144">MessagePack 通訊協定會區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-144">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="0c0e4-145">例如，請考慮下列 c # 類別：</span><span class="sxs-lookup"><span data-stu-id="0c0e4-145">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="0c0e4-146">從 JavaScript 用戶端傳送時，您必須使用 `PascalCased` 屬性名稱，因為大小寫必須完全符合 c # 類別。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-146">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="0c0e4-147">例如：</span><span class="sxs-lookup"><span data-stu-id="0c0e4-147">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="0c0e4-148">使用 `camelCased` 名稱並不會正確地系結至 c # 類別。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-148">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="0c0e4-149">您可以使用 `Key` 屬性來為 MessagePack 屬性指定不同的名稱，藉此解決這個問題。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-149">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="0c0e4-150">如需詳細資訊，請參閱 [MessagePack-CSharp 檔](https://github.com/neuecc/MessagePack-CSharp#object-serialization)。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-150">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="0c0e4-151">序列化/還原序列化時，不會保留 DateTime. 種類</span><span class="sxs-lookup"><span data-stu-id="0c0e4-151">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="0c0e4-152">MessagePack 通訊協定不提供將的值編碼的方式 `Kind` `DateTime` 。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-152">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="0c0e4-153">如此一來，當您將日期還原序列化時，如果是，MessagePack Hub 通訊協定將會轉換成 UTC 格式， `DateTime.Kind` `DateTimeKind.Local` 否則它將不會觸及時間並依原樣傳遞。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-153">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="0c0e4-154">如果您正在使用 `DateTime` 值，建議您在傳送之前轉換為 UTC。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-154">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="0c0e4-155">當您收到時，請將它們從 UTC 轉換為當地時間。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-155">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="0c0e4-156">JavaScript 中的 MessagePack 不支援 MinValue</span><span class="sxs-lookup"><span data-stu-id="0c0e4-156">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="0c0e4-157">JavaScript 用戶端所使用的 [msgpack5](https://github.com/mcollina/msgpack5) 程式庫 SignalR 不支援 `timestamp96` MessagePack 中的類型。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-157">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="0c0e4-158">這種類型是用來編碼非常大的日期值， (在過去或未來的) 中非常早。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-158">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="0c0e4-159">的值 `DateTime.MinValue` 為 `January 1, 0001` ，必須以值進行編碼 `timestamp96` 。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-159">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="0c0e4-160">基於這個原因， `DateTime.MinValue` 不支援傳送至 JavaScript 用戶端。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-160">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="0c0e4-161">當 `DateTime.MinValue` JavaScript 用戶端收到時，會擲回下列錯誤：</span><span class="sxs-lookup"><span data-stu-id="0c0e4-161">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="0c0e4-162">通常 `DateTime.MinValue` 是用來編碼「遺失」或 `null` 值。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-162">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="0c0e4-163">如果您需要在 MessagePack 中編碼該值，請使用可為 null 的 `DateTime` 值 (`DateTime?`) 或編碼個別 `bool` 值，以指出日期是否存在。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-163">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="0c0e4-164">如需這項限制的詳細資訊，請參閱 GitHub 問題 [aspnet/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228)。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-164">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="0c0e4-165">在「事先」編譯環境中 MessagePack 支援</span><span class="sxs-lookup"><span data-stu-id="0c0e4-165">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="0c0e4-166">.NET 用戶端和伺服器所使用的 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) 程式庫會使用程式碼產生，將序列化優化。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-166">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="0c0e4-167">因此，在使用「預先」編譯 (（例如 Xamarin iOS 或 Unity) ）的環境上，預設不會支援此方法。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-167">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="0c0e4-168">您可以藉由「預先產生」序列化程式/還原序列化程式碼，在這些環境中使用 MessagePack。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-168">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="0c0e4-169">如需詳細資訊，請參閱 [MessagePack-CSharp 檔](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin)。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-169">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="0c0e4-170">當您預先產生序列化程式之後，您可以使用傳遞至的設定委派來註冊它們 `AddMessagePackProtocol` ：</span><span class="sxs-lookup"><span data-stu-id="0c0e4-170">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="0c0e4-171">MessagePack 中的類型檢查較嚴格</span><span class="sxs-lookup"><span data-stu-id="0c0e4-171">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="0c0e4-172">JSON Hub 通訊協定會在還原序列化期間執行類型轉換。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-172">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="0c0e4-173">例如，如果傳入物件的屬性值是數位 (`{ foo: 42 }`) 但 .net 類別上的屬性是類型 `string` ，則值將會轉換。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-173">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="0c0e4-174">但是，MessagePack 不會執行這項轉換，而且會擲回例外狀況，在伺服器端記錄檔 (和主控台) 中會出現此例外狀況：</span><span class="sxs-lookup"><span data-stu-id="0c0e4-174">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="0c0e4-175">如需這項限制的詳細資訊，請參閱 GitHub 問題 [aspnet/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937)。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-175">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="0c0e4-176">相關資源</span><span class="sxs-lookup"><span data-stu-id="0c0e4-176">Related resources</span></span>

* [<span data-ttu-id="0c0e4-177">開始使用</span><span class="sxs-lookup"><span data-stu-id="0c0e4-177">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="0c0e4-178">.NET 用戶端</span><span class="sxs-lookup"><span data-stu-id="0c0e4-178">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="0c0e4-179">JavaScript 用戶端</span><span class="sxs-lookup"><span data-stu-id="0c0e4-179">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="0c0e4-180">本文假設讀者已熟悉 [開始](xref:tutorials/signalr)所涵蓋的主題。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-180">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="0c0e4-181">什麼是 MessagePack？</span><span class="sxs-lookup"><span data-stu-id="0c0e4-181">What is MessagePack?</span></span>

<span data-ttu-id="0c0e4-182">[MessagePack](https://msgpack.org/index.html) 是一種快速且精簡的二進位序列化格式。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-182">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="0c0e4-183">當效能和頻寬有問題時，這會很有用，因為它會建立比 [JSON](https://www.json.org/)更小的訊息。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-183">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="0c0e4-184">在查看網路追蹤和記錄檔時，除非透過 MessagePack 剖析器傳遞位元組，否則二進位訊息無法讀取。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-184">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="0c0e4-185">SignalR 內建支援 MessagePack 格式，並提供 Api 供用戶端和伺服器使用。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-185">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="0c0e4-186">在伺服器上設定 MessagePack</span><span class="sxs-lookup"><span data-stu-id="0c0e4-186">Configure MessagePack on the server</span></span>

<span data-ttu-id="0c0e4-187">若要在伺服器上啟用 MessagePack Hub 通訊協定，請 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 在您的應用程式中安裝套件。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-187">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="0c0e4-188">在 `Startup.ConfigureServices` 方法中，新增 `AddMessagePackProtocol` 至在 `AddSignalR` 伺服器上啟用 MessagePack 支援的呼叫。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-188">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="0c0e4-189">預設會啟用 JSON。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-189">JSON is enabled by default.</span></span> <span data-ttu-id="0c0e4-190">新增 MessagePack 可支援 JSON 和 MessagePack 用戶端。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-190">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="0c0e4-191">若要自訂 MessagePack 將如何格式化您的資料，請 `AddMessagePackProtocol` 使用委派來設定選項。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-191">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="0c0e4-192">在該委派中， `FormatterResolvers` 屬性可以用來設定 MessagePack 序列化選項。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-192">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="0c0e4-193">如需解析程式如何運作的詳細資訊，請造訪 MessagePack library，網址為 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-193">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="0c0e4-194">屬性可用於您要序列化的物件，以定義應該如何處理它們。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-194">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="0c0e4-195">強烈建議您檢查 [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) 並套用建議的修補程式。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-195">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="0c0e4-196">例如，將 `MessagePackSecurity.Active` 靜態屬性設定為 `MessagePackSecurity.UntrustedData` 。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-196">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="0c0e4-197">設定 `MessagePackSecurity.Active` 需要手動安裝 MessagePack 的 [1.9. x 版](https://www.nuget.org/packages/MessagePack/1.9.3)。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-197">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="0c0e4-198">安裝 `MessagePack` 1.9. x 版升級時 SignalR 使用。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-198">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="0c0e4-199">當未 `MessagePackSecurity.Active` 設定為時 `MessagePackSecurity.UntrustedData` ，惡意用戶端可能會造成阻絕服務。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-199">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="0c0e4-200">`MessagePackSecurity.Active`在中設定 `Program.Main` ，如下列程式碼所示：</span><span class="sxs-lookup"><span data-stu-id="0c0e4-200">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="0c0e4-201">在用戶端上設定 MessagePack</span><span class="sxs-lookup"><span data-stu-id="0c0e4-201">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="0c0e4-202">針對支援的用戶端，預設會啟用 JSON。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-202">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="0c0e4-203">用戶端只能支援單一通訊協定。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-203">Clients can only support a single protocol.</span></span> <span data-ttu-id="0c0e4-204">新增 MessagePack 支援將會取代任何先前設定的通訊協定。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-204">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="0c0e4-205">.NET 用戶端</span><span class="sxs-lookup"><span data-stu-id="0c0e4-205">.NET client</span></span>

<span data-ttu-id="0c0e4-206">若要在 .NET 用戶端中啟用 MessagePack，請安裝 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 封裝並 `AddMessagePackProtocol` 在上呼叫 `HubConnectionBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-206">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="0c0e4-207">此 `AddMessagePackProtocol` 呼叫會採用委派來設定選項，就像伺服器一樣。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-207">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="0c0e4-208">JavaScript 用戶端</span><span class="sxs-lookup"><span data-stu-id="0c0e4-208">JavaScript client</span></span>

<span data-ttu-id="0c0e4-209">JavaScript 用戶端的 MessagePack 支援是由 [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm 封裝提供。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-209">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="0c0e4-210">在命令 shell 中執行下列命令，以安裝套件：</span><span class="sxs-lookup"><span data-stu-id="0c0e4-210">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="0c0e4-211">安裝 npm 套件之後，您可以透過 JavaScript 模組載入器直接使用模組，或藉由參考下列檔案將其匯入至瀏覽器：</span><span class="sxs-lookup"><span data-stu-id="0c0e4-211">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="0c0e4-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="0c0e4-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="0c0e4-213">在瀏覽器中， `msgpack5` 也必須參考程式庫。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-213">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="0c0e4-214">使用 `<script>` 標記來建立參考。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-214">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="0c0e4-215">您可以在 *node_modules\msgpack5\dist\msgpack5.js*找到該程式庫。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-215">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="0c0e4-216">使用專案時 `<script>` ，順序很重要。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-216">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="0c0e4-217">如果在*msgpack5.js*之前參考*signalr-protocol-msgpack.js* ，則嘗試使用 MessagePack 連接時，就會發生錯誤。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-217">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="0c0e4-218">*signalr-protocol-msgpack.js*之前也需要*signalr.js* 。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-218">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="0c0e4-219">新增 `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` 至 `HubConnectionBuilder` 會將用戶端設定為在連接到伺服器時使用 MessagePack 通訊協定。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-219">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="0c0e4-220">目前，JavaScript 用戶端上沒有 MessagePack 通訊協定的設定選項。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-220">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="0c0e4-221">MessagePack 的不相容</span><span class="sxs-lookup"><span data-stu-id="0c0e4-221">MessagePack quirks</span></span>

<span data-ttu-id="0c0e4-222">使用 MessagePack 中樞通訊協定時，有幾個要注意的問題。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-222">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="0c0e4-223">MessagePack 區分大小寫</span><span class="sxs-lookup"><span data-stu-id="0c0e4-223">MessagePack is case-sensitive</span></span>

<span data-ttu-id="0c0e4-224">MessagePack 通訊協定會區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-224">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="0c0e4-225">例如，請考慮下列 c # 類別：</span><span class="sxs-lookup"><span data-stu-id="0c0e4-225">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="0c0e4-226">從 JavaScript 用戶端傳送時，您必須使用 `PascalCased` 屬性名稱，因為大小寫必須完全符合 c # 類別。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-226">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="0c0e4-227">例如：</span><span class="sxs-lookup"><span data-stu-id="0c0e4-227">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="0c0e4-228">使用 `camelCased` 名稱並不會正確地系結至 c # 類別。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-228">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="0c0e4-229">您可以使用 `Key` 屬性來為 MessagePack 屬性指定不同的名稱，藉此解決這個問題。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-229">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="0c0e4-230">如需詳細資訊，請參閱 [MessagePack-CSharp 檔](https://github.com/neuecc/MessagePack-CSharp#object-serialization)。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-230">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="0c0e4-231">序列化/還原序列化時，不會保留 DateTime. 種類</span><span class="sxs-lookup"><span data-stu-id="0c0e4-231">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="0c0e4-232">MessagePack 通訊協定不提供將的值編碼的方式 `Kind` `DateTime` 。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-232">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="0c0e4-233">如此一來，在還原序列化日期時，MessagePack Hub 通訊協定會假設內送日期採用 UTC 格式。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-233">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="0c0e4-234">如果您是 `DateTime` 以當地時間處理值，建議您在傳送之前轉換成 UTC。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-234">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="0c0e4-235">當您收到時，請將它們從 UTC 轉換為當地時間。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-235">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="0c0e4-236">如需這項限制的詳細資訊，請參閱 GitHub 問題 [aspnet/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632)。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-236">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="0c0e4-237">JavaScript 中的 MessagePack 不支援 MinValue</span><span class="sxs-lookup"><span data-stu-id="0c0e4-237">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="0c0e4-238">JavaScript 用戶端所使用的 [msgpack5](https://github.com/mcollina/msgpack5) 程式庫 SignalR 不支援 `timestamp96` MessagePack 中的類型。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-238">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="0c0e4-239">這種類型是用來編碼非常大的日期值， (在過去或未來的) 中非常早。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-239">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="0c0e4-240">的值 `DateTime.MinValue` 為 `January 1, 0001` ，必須以值進行編碼 `timestamp96` 。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-240">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="0c0e4-241">基於這個原因， `DateTime.MinValue` 不支援傳送至 JavaScript 用戶端。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-241">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="0c0e4-242">當 `DateTime.MinValue` JavaScript 用戶端收到時，會擲回下列錯誤：</span><span class="sxs-lookup"><span data-stu-id="0c0e4-242">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="0c0e4-243">通常 `DateTime.MinValue` 是用來編碼「遺失」或 `null` 值。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-243">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="0c0e4-244">如果您需要在 MessagePack 中編碼該值，請使用可為 null 的 `DateTime` 值 (`DateTime?`) 或編碼個別 `bool` 值，以指出日期是否存在。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-244">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="0c0e4-245">如需這項限制的詳細資訊，請參閱 GitHub 問題 [aspnet/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228)。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-245">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="0c0e4-246">在「事先」編譯環境中 MessagePack 支援</span><span class="sxs-lookup"><span data-stu-id="0c0e4-246">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="0c0e4-247">.NET 用戶端和伺服器所使用的 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) 程式庫會使用程式碼產生，將序列化優化。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-247">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="0c0e4-248">因此，在使用「預先」編譯 (（例如 Xamarin iOS 或 Unity) ）的環境上，預設不會支援此方法。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-248">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="0c0e4-249">您可以藉由「預先產生」序列化程式/還原序列化程式碼，在這些環境中使用 MessagePack。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-249">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="0c0e4-250">如需詳細資訊，請參閱 [MessagePack-CSharp 檔](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports)。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-250">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="0c0e4-251">當您預先產生序列化程式之後，您可以使用傳遞至的設定委派來註冊它們 `AddMessagePackProtocol` ：</span><span class="sxs-lookup"><span data-stu-id="0c0e4-251">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="0c0e4-252">MessagePack 中的類型檢查較嚴格</span><span class="sxs-lookup"><span data-stu-id="0c0e4-252">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="0c0e4-253">JSON Hub 通訊協定會在還原序列化期間執行類型轉換。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-253">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="0c0e4-254">例如，如果傳入物件的屬性值是數位 (`{ foo: 42 }`) 但 .net 類別上的屬性是類型 `string` ，則值將會轉換。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-254">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="0c0e4-255">但是，MessagePack 不會執行這項轉換，而且會擲回例外狀況，在伺服器端記錄檔 (和主控台) 中會出現此例外狀況：</span><span class="sxs-lookup"><span data-stu-id="0c0e4-255">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="0c0e4-256">如需這項限制的詳細資訊，請參閱 GitHub 問題 [aspnet/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937)。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-256">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="0c0e4-257">相關資源</span><span class="sxs-lookup"><span data-stu-id="0c0e4-257">Related resources</span></span>

* [<span data-ttu-id="0c0e4-258">開始使用</span><span class="sxs-lookup"><span data-stu-id="0c0e4-258">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="0c0e4-259">.NET 用戶端</span><span class="sxs-lookup"><span data-stu-id="0c0e4-259">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="0c0e4-260">JavaScript 用戶端</span><span class="sxs-lookup"><span data-stu-id="0c0e4-260">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0c0e4-261">本文假設讀者已熟悉 [開始](xref:tutorials/signalr)所涵蓋的主題。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-261">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="0c0e4-262">什麼是 MessagePack？</span><span class="sxs-lookup"><span data-stu-id="0c0e4-262">What is MessagePack?</span></span>

<span data-ttu-id="0c0e4-263">[MessagePack](https://msgpack.org/index.html) 是一種快速且精簡的二進位序列化格式。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-263">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="0c0e4-264">當效能和頻寬有問題時，這會很有用，因為它會建立比 [JSON](https://www.json.org/)更小的訊息。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-264">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="0c0e4-265">在查看網路追蹤和記錄檔時，除非透過 MessagePack 剖析器傳遞位元組，否則二進位訊息無法讀取。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-265">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="0c0e4-266">SignalR 內建支援 MessagePack 格式，並提供 Api 供用戶端和伺服器使用。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-266">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="0c0e4-267">在伺服器上設定 MessagePack</span><span class="sxs-lookup"><span data-stu-id="0c0e4-267">Configure MessagePack on the server</span></span>

<span data-ttu-id="0c0e4-268">若要在伺服器上啟用 MessagePack Hub 通訊協定，請 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 在您的應用程式中安裝套件。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-268">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="0c0e4-269">在 `Startup.ConfigureServices` 方法中，新增 `AddMessagePackProtocol` 至在 `AddSignalR` 伺服器上啟用 MessagePack 支援的呼叫。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-269">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="0c0e4-270">預設會啟用 JSON。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-270">JSON is enabled by default.</span></span> <span data-ttu-id="0c0e4-271">新增 MessagePack 可支援 JSON 和 MessagePack 用戶端。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-271">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="0c0e4-272">若要自訂 MessagePack 將如何格式化您的資料，請 `AddMessagePackProtocol` 使用委派來設定選項。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-272">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="0c0e4-273">在該委派中， `FormatterResolvers` 屬性可以用來設定 MessagePack 序列化選項。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-273">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="0c0e4-274">如需解析程式如何運作的詳細資訊，請造訪 MessagePack library，網址為 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-274">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="0c0e4-275">屬性可用於您要序列化的物件，以定義應該如何處理它們。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-275">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="0c0e4-276">強烈建議您檢查 [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) 並套用建議的修補程式。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-276">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="0c0e4-277">例如，將 `MessagePackSecurity.Active` 靜態屬性設定為 `MessagePackSecurity.UntrustedData` 。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-277">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="0c0e4-278">設定 `MessagePackSecurity.Active` 需要手動安裝 MessagePack 的 [1.9. x 版](https://www.nuget.org/packages/MessagePack/1.9.3)。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-278">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="0c0e4-279">安裝 `MessagePack` 1.9. x 版升級時 SignalR 使用。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-279">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="0c0e4-280">當未 `MessagePackSecurity.Active` 設定為時 `MessagePackSecurity.UntrustedData` ，惡意用戶端可能會造成阻絕服務。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-280">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="0c0e4-281">`MessagePackSecurity.Active`在中設定 `Program.Main` ，如下列程式碼所示：</span><span class="sxs-lookup"><span data-stu-id="0c0e4-281">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="0c0e4-282">在用戶端上設定 MessagePack</span><span class="sxs-lookup"><span data-stu-id="0c0e4-282">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="0c0e4-283">針對支援的用戶端，預設會啟用 JSON。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-283">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="0c0e4-284">用戶端只能支援單一通訊協定。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-284">Clients can only support a single protocol.</span></span> <span data-ttu-id="0c0e4-285">新增 MessagePack 支援將會取代任何先前設定的通訊協定。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-285">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="0c0e4-286">.NET 用戶端</span><span class="sxs-lookup"><span data-stu-id="0c0e4-286">.NET client</span></span>

<span data-ttu-id="0c0e4-287">若要在 .NET 用戶端中啟用 MessagePack，請安裝 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 封裝並 `AddMessagePackProtocol` 在上呼叫 `HubConnectionBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-287">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="0c0e4-288">此 `AddMessagePackProtocol` 呼叫會採用委派來設定選項，就像伺服器一樣。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-288">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="0c0e4-289">JavaScript 用戶端</span><span class="sxs-lookup"><span data-stu-id="0c0e4-289">JavaScript client</span></span>

<span data-ttu-id="0c0e4-290">JavaScript 用戶端的 MessagePack 支援是由 [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm 封裝提供。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-290">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="0c0e4-291">在命令 shell 中執行下列命令，以安裝套件：</span><span class="sxs-lookup"><span data-stu-id="0c0e4-291">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="0c0e4-292">安裝 npm 套件之後，您可以透過 JavaScript 模組載入器直接使用模組，或藉由參考下列檔案將其匯入至瀏覽器：</span><span class="sxs-lookup"><span data-stu-id="0c0e4-292">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="0c0e4-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="0c0e4-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="0c0e4-294">在瀏覽器中， `msgpack5` 也必須參考程式庫。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-294">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="0c0e4-295">使用 `<script>` 標記來建立參考。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-295">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="0c0e4-296">您可以在 *node_modules\msgpack5\dist\msgpack5.js*找到該程式庫。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-296">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="0c0e4-297">使用專案時 `<script>` ，順序很重要。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-297">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="0c0e4-298">如果在*msgpack5.js*之前參考*signalr-protocol-msgpack.js* ，則嘗試使用 MessagePack 連接時，就會發生錯誤。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-298">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="0c0e4-299">*signalr-protocol-msgpack.js*之前也需要*signalr.js* 。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-299">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="0c0e4-300">新增 `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` 至 `HubConnectionBuilder` 會將用戶端設定為在連接到伺服器時使用 MessagePack 通訊協定。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-300">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="0c0e4-301">目前，JavaScript 用戶端上沒有 MessagePack 通訊協定的設定選項。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-301">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="0c0e4-302">MessagePack 的不相容</span><span class="sxs-lookup"><span data-stu-id="0c0e4-302">MessagePack quirks</span></span>

<span data-ttu-id="0c0e4-303">使用 MessagePack 中樞通訊協定時，有幾個要注意的問題。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-303">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="0c0e4-304">MessagePack 區分大小寫</span><span class="sxs-lookup"><span data-stu-id="0c0e4-304">MessagePack is case-sensitive</span></span>

<span data-ttu-id="0c0e4-305">MessagePack 通訊協定會區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-305">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="0c0e4-306">例如，請考慮下列 c # 類別：</span><span class="sxs-lookup"><span data-stu-id="0c0e4-306">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="0c0e4-307">從 JavaScript 用戶端傳送時，您必須使用 `PascalCased` 屬性名稱，因為大小寫必須完全符合 c # 類別。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-307">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="0c0e4-308">例如：</span><span class="sxs-lookup"><span data-stu-id="0c0e4-308">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="0c0e4-309">使用 `camelCased` 名稱並不會正確地系結至 c # 類別。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-309">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="0c0e4-310">您可以使用 `Key` 屬性來為 MessagePack 屬性指定不同的名稱，藉此解決這個問題。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-310">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="0c0e4-311">如需詳細資訊，請參閱 [MessagePack-CSharp 檔](https://github.com/neuecc/MessagePack-CSharp#object-serialization)。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-311">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="0c0e4-312">序列化/還原序列化時，不會保留 DateTime. 種類</span><span class="sxs-lookup"><span data-stu-id="0c0e4-312">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="0c0e4-313">MessagePack 通訊協定不提供將的值編碼的方式 `Kind` `DateTime` 。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-313">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="0c0e4-314">如此一來，在還原序列化日期時，MessagePack Hub 通訊協定會假設內送日期採用 UTC 格式。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-314">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="0c0e4-315">如果您是 `DateTime` 以當地時間處理值，建議您在傳送之前轉換成 UTC。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-315">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="0c0e4-316">當您收到時，請將它們從 UTC 轉換為當地時間。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-316">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="0c0e4-317">如需這項限制的詳細資訊，請參閱 GitHub 問題 [aspnet/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632)。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-317">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="0c0e4-318">JavaScript 中的 MessagePack 不支援 MinValue</span><span class="sxs-lookup"><span data-stu-id="0c0e4-318">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="0c0e4-319">JavaScript 用戶端所使用的 [msgpack5](https://github.com/mcollina/msgpack5) 程式庫 SignalR 不支援 `timestamp96` MessagePack 中的類型。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-319">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="0c0e4-320">這種類型是用來編碼非常大的日期值， (在過去或未來的) 中非常早。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-320">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="0c0e4-321">的值 `DateTime.MinValue` `January 1, 0001` 必須以值進行編碼 `timestamp96` 。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-321">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="0c0e4-322">基於這個原因， `DateTime.MinValue` 不支援傳送至 JavaScript 用戶端。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-322">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="0c0e4-323">當 `DateTime.MinValue` JavaScript 用戶端收到時，會擲回下列錯誤：</span><span class="sxs-lookup"><span data-stu-id="0c0e4-323">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="0c0e4-324">通常 `DateTime.MinValue` 是用來編碼「遺失」或 `null` 值。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-324">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="0c0e4-325">如果您需要在 MessagePack 中編碼該值，請使用可為 null 的 `DateTime` 值 (`DateTime?`) 或編碼個別 `bool` 值，以指出日期是否存在。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-325">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="0c0e4-326">如需這項限制的詳細資訊，請參閱 GitHub 問題 [aspnet/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228)。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-326">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="0c0e4-327">在「事先」編譯環境中 MessagePack 支援</span><span class="sxs-lookup"><span data-stu-id="0c0e4-327">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="0c0e4-328">.NET 用戶端和伺服器所使用的 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) 程式庫會使用程式碼產生，將序列化優化。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-328">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="0c0e4-329">因此，在使用「預先」編譯 (（例如 Xamarin iOS 或 Unity) ）的環境上，預設不會支援此方法。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-329">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="0c0e4-330">您可以藉由「預先產生」序列化程式/還原序列化程式碼，在這些環境中使用 MessagePack。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-330">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="0c0e4-331">如需詳細資訊，請參閱 [MessagePack-CSharp 檔](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports)。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-331">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="0c0e4-332">當您預先產生序列化程式之後，您可以使用傳遞至的設定委派來註冊它們 `AddMessagePackProtocol` ：</span><span class="sxs-lookup"><span data-stu-id="0c0e4-332">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="0c0e4-333">MessagePack 中的類型檢查較嚴格</span><span class="sxs-lookup"><span data-stu-id="0c0e4-333">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="0c0e4-334">JSON Hub 通訊協定會在還原序列化期間執行類型轉換。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-334">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="0c0e4-335">例如，如果傳入物件的屬性值是數位 (`{ foo: 42 }`) 但 .net 類別上的屬性是類型 `string` ，則值將會轉換。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-335">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="0c0e4-336">但是，MessagePack 不會執行這項轉換，而且會擲回例外狀況，在伺服器端記錄檔 (和主控台) 中會出現此例外狀況：</span><span class="sxs-lookup"><span data-stu-id="0c0e4-336">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="0c0e4-337">如需這項限制的詳細資訊，請參閱 GitHub 問題 [aspnet/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937)。</span><span class="sxs-lookup"><span data-stu-id="0c0e4-337">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="0c0e4-338">相關資源</span><span class="sxs-lookup"><span data-stu-id="0c0e4-338">Related resources</span></span>

* [<span data-ttu-id="0c0e4-339">開始使用</span><span class="sxs-lookup"><span data-stu-id="0c0e4-339">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="0c0e4-340">.NET 用戶端</span><span class="sxs-lookup"><span data-stu-id="0c0e4-340">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="0c0e4-341">JavaScript 用戶端</span><span class="sxs-lookup"><span data-stu-id="0c0e4-341">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
