---
title: '使用中的 MessagePack Hub 通訊協定 :::no-loc(SignalR)::: 進行 ASP.NET Core'
author: bradygaster
description: '將 MessagePack 中樞通訊協定新增至 ASP.NET Core :::no-loc(SignalR)::: 。'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 09/24/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: e7d19a42e48048d2be4b87d6b0ac1ba6b2596ff1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058164"
---
# <a name="use-messagepack-hub-protocol-in-no-locsignalr-for-aspnet-core"></a><span data-ttu-id="780cb-103">使用中的 MessagePack Hub 通訊協定 :::no-loc(SignalR)::: 進行 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="780cb-103">Use MessagePack Hub Protocol in :::no-loc(SignalR)::: for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="780cb-104">本文假設讀者已熟悉 [開始](xref:tutorials/signalr)所涵蓋的主題。</span><span class="sxs-lookup"><span data-stu-id="780cb-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="780cb-105">什麼是 MessagePack？</span><span class="sxs-lookup"><span data-stu-id="780cb-105">What is MessagePack?</span></span>

<span data-ttu-id="780cb-106">[MessagePack](https://msgpack.org/index.html) 是一種快速且精簡的二進位序列化格式。</span><span class="sxs-lookup"><span data-stu-id="780cb-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="780cb-107">當效能和頻寬有問題時，這會很有用，因為它會建立比 [JSON](https://www.json.org/)更小的訊息。</span><span class="sxs-lookup"><span data-stu-id="780cb-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="780cb-108">在查看網路追蹤和記錄檔時，除非透過 MessagePack 剖析器傳遞位元組，否則二進位訊息無法讀取。</span><span class="sxs-lookup"><span data-stu-id="780cb-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="780cb-109">:::no-loc(SignalR)::: 內建支援 MessagePack 格式，並提供 Api 供用戶端和伺服器使用。</span><span class="sxs-lookup"><span data-stu-id="780cb-109">:::no-loc(SignalR)::: has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="780cb-110">在伺服器上設定 MessagePack</span><span class="sxs-lookup"><span data-stu-id="780cb-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="780cb-111">若要在伺服器上啟用 MessagePack Hub 通訊協定，請 `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` 在您的應用程式中安裝套件。</span><span class="sxs-lookup"><span data-stu-id="780cb-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="780cb-112">在 `Startup.ConfigureServices` 方法中，新增 `AddMessagePackProtocol` 至在 `Add:::no-loc(SignalR):::` 伺服器上啟用 MessagePack 支援的呼叫。</span><span class="sxs-lookup"><span data-stu-id="780cb-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `Add:::no-loc(SignalR):::` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="780cb-113">預設會啟用 JSON。</span><span class="sxs-lookup"><span data-stu-id="780cb-113">JSON is enabled by default.</span></span> <span data-ttu-id="780cb-114">新增 MessagePack 可支援 JSON 和 MessagePack 用戶端。</span><span class="sxs-lookup"><span data-stu-id="780cb-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddMessagePackProtocol();
```

<span data-ttu-id="780cb-115">若要自訂 MessagePack 將如何格式化您的資料，請 `AddMessagePackProtocol` 使用委派來設定選項。</span><span class="sxs-lookup"><span data-stu-id="780cb-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="780cb-116">在該委派中， `SerializerOptions` 屬性可以用來設定 MessagePack 序列化選項。</span><span class="sxs-lookup"><span data-stu-id="780cb-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="780cb-117">如需解析程式如何運作的詳細資訊，請造訪 MessagePack library，網址為 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)。</span><span class="sxs-lookup"><span data-stu-id="780cb-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="780cb-118">屬性可用於您要序列化的物件，以定義應該如何處理它們。</span><span class="sxs-lookup"><span data-stu-id="780cb-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddMessagePackProtocol(options =>
    {
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(new CustomResolver())
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

> [!WARNING]
> <span data-ttu-id="780cb-119">強烈建議您檢查 [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) 並套用建議的修補程式。</span><span class="sxs-lookup"><span data-stu-id="780cb-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="780cb-120">例如， `.WithSecurity(MessagePackSecurity.UntrustedData)` 在取代時呼叫 `SerializerOptions` 。</span><span class="sxs-lookup"><span data-stu-id="780cb-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="780cb-121">在用戶端上設定 MessagePack</span><span class="sxs-lookup"><span data-stu-id="780cb-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="780cb-122">針對支援的用戶端，預設會啟用 JSON。</span><span class="sxs-lookup"><span data-stu-id="780cb-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="780cb-123">用戶端只能支援單一通訊協定。</span><span class="sxs-lookup"><span data-stu-id="780cb-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="780cb-124">新增 MessagePack 支援將會取代任何先前設定的通訊協定。</span><span class="sxs-lookup"><span data-stu-id="780cb-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="780cb-125">.NET 用戶端</span><span class="sxs-lookup"><span data-stu-id="780cb-125">.NET client</span></span>

<span data-ttu-id="780cb-126">若要在 .NET 用戶端中啟用 MessagePack，請安裝 `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` 封裝並 `AddMessagePackProtocol` 在上呼叫 `HubConnectionBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="780cb-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.:::no-loc(SignalR):::.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="780cb-127">此 `AddMessagePackProtocol` 呼叫會採用委派來設定選項，就像伺服器一樣。</span><span class="sxs-lookup"><span data-stu-id="780cb-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="780cb-128">JavaScript 用戶端</span><span class="sxs-lookup"><span data-stu-id="780cb-128">JavaScript client</span></span>

<span data-ttu-id="780cb-129">JavaScript 用戶端的 MessagePack 支援是由 [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm 封裝提供。</span><span class="sxs-lookup"><span data-stu-id="780cb-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="780cb-130">在命令 shell 中執行下列命令，以安裝套件：</span><span class="sxs-lookup"><span data-stu-id="780cb-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="780cb-131">安裝 npm 套件之後，您可以透過 JavaScript 模組載入器直接使用模組，或藉由參考下列檔案將其匯入至瀏覽器：</span><span class="sxs-lookup"><span data-stu-id="780cb-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="780cb-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="780cb-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="780cb-133">在瀏覽器中， `msgpack5` 也必須參考程式庫。</span><span class="sxs-lookup"><span data-stu-id="780cb-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="780cb-134">使用 `<script>` 標記來建立參考。</span><span class="sxs-lookup"><span data-stu-id="780cb-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="780cb-135">您可以在 *node_modules\msgpack5\dist\msgpack5.js* 找到該程式庫。</span><span class="sxs-lookup"><span data-stu-id="780cb-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js* .</span></span>

> [!NOTE]
> <span data-ttu-id="780cb-136">使用專案時 `<script>` ，順序很重要。</span><span class="sxs-lookup"><span data-stu-id="780cb-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="780cb-137">如果在 *msgpack5.js* 之前參考 *signalr-protocol-msgpack.js* ，則嘗試使用 MessagePack 連接時，就會發生錯誤。</span><span class="sxs-lookup"><span data-stu-id="780cb-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js* , an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="780cb-138">*signalr-protocol-msgpack.js* 之前也需要 *signalr.js* 。</span><span class="sxs-lookup"><span data-stu-id="780cb-138">*signalr.js* is also required before *signalr-protocol-msgpack.js* .</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="780cb-139">新增 `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` 至 `HubConnectionBuilder` 會將用戶端設定為在連接到伺服器時使用 MessagePack 通訊協定。</span><span class="sxs-lookup"><span data-stu-id="780cb-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="780cb-140">目前，JavaScript 用戶端上沒有 MessagePack 通訊協定的設定選項。</span><span class="sxs-lookup"><span data-stu-id="780cb-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="780cb-141">MessagePack 的不相容</span><span class="sxs-lookup"><span data-stu-id="780cb-141">MessagePack quirks</span></span>

<span data-ttu-id="780cb-142">使用 MessagePack 中樞通訊協定時，有幾個要注意的問題。</span><span class="sxs-lookup"><span data-stu-id="780cb-142">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="780cb-143">MessagePack 區分大小寫</span><span class="sxs-lookup"><span data-stu-id="780cb-143">MessagePack is case-sensitive</span></span>

<span data-ttu-id="780cb-144">MessagePack 通訊協定會區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="780cb-144">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="780cb-145">例如，請考慮下列 c # 類別：</span><span class="sxs-lookup"><span data-stu-id="780cb-145">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="780cb-146">從 JavaScript 用戶端傳送時，您必須使用 `PascalCased` 屬性名稱，因為大小寫必須完全符合 c # 類別。</span><span class="sxs-lookup"><span data-stu-id="780cb-146">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="780cb-147">例如：</span><span class="sxs-lookup"><span data-stu-id="780cb-147">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="780cb-148">使用 `camelCased` 名稱並不會正確地系結至 c # 類別。</span><span class="sxs-lookup"><span data-stu-id="780cb-148">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="780cb-149">您可以使用 `Key` 屬性來為 MessagePack 屬性指定不同的名稱，藉此解決這個問題。</span><span class="sxs-lookup"><span data-stu-id="780cb-149">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="780cb-150">如需詳細資訊，請參閱 [MessagePack-CSharp 檔](https://github.com/neuecc/MessagePack-CSharp#object-serialization)。</span><span class="sxs-lookup"><span data-stu-id="780cb-150">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="780cb-151">序列化/還原序列化時，不會保留 DateTime. 種類</span><span class="sxs-lookup"><span data-stu-id="780cb-151">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="780cb-152">MessagePack 通訊協定不提供將的值編碼的方式 `Kind` `DateTime` 。</span><span class="sxs-lookup"><span data-stu-id="780cb-152">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="780cb-153">如此一來，當您將日期還原序列化時，如果是，MessagePack Hub 通訊協定將會轉換成 UTC 格式， `DateTime.Kind` `DateTimeKind.Local` 否則它將不會觸及時間並依原樣傳遞。</span><span class="sxs-lookup"><span data-stu-id="780cb-153">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="780cb-154">如果您正在使用 `DateTime` 值，建議您在傳送之前轉換為 UTC。</span><span class="sxs-lookup"><span data-stu-id="780cb-154">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="780cb-155">當您收到時，請將它們從 UTC 轉換為當地時間。</span><span class="sxs-lookup"><span data-stu-id="780cb-155">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="780cb-156">JavaScript 中的 MessagePack 不支援 MinValue</span><span class="sxs-lookup"><span data-stu-id="780cb-156">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="780cb-157">JavaScript 用戶端所使用的 [msgpack5](https://github.com/mcollina/msgpack5) 程式庫 :::no-loc(SignalR)::: 不支援 `timestamp96` MessagePack 中的類型。</span><span class="sxs-lookup"><span data-stu-id="780cb-157">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the :::no-loc(SignalR)::: JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="780cb-158">這種類型是用來編碼非常大的日期值， (在過去或未來的) 中非常早。</span><span class="sxs-lookup"><span data-stu-id="780cb-158">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="780cb-159">的值 `DateTime.MinValue` 為 `January 1, 0001` ，必須以值進行編碼 `timestamp96` 。</span><span class="sxs-lookup"><span data-stu-id="780cb-159">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="780cb-160">基於這個原因， `DateTime.MinValue` 不支援傳送至 JavaScript 用戶端。</span><span class="sxs-lookup"><span data-stu-id="780cb-160">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="780cb-161">當 `DateTime.MinValue` JavaScript 用戶端收到時，會擲回下列錯誤：</span><span class="sxs-lookup"><span data-stu-id="780cb-161">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="780cb-162">通常 `DateTime.MinValue` 是用來編碼「遺失」或 `null` 值。</span><span class="sxs-lookup"><span data-stu-id="780cb-162">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="780cb-163">如果您需要在 MessagePack 中編碼該值，請使用可為 null 的 `DateTime` 值 (`DateTime?`) 或編碼個別 `bool` 值，以指出日期是否存在。</span><span class="sxs-lookup"><span data-stu-id="780cb-163">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="780cb-164">如需這項限制的詳細資訊，請參閱 GitHub 問題 [aspnet/ :::no-loc(SignalR)::: #2228](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2228)。</span><span class="sxs-lookup"><span data-stu-id="780cb-164">For more information on this limitation, see GitHub issue [aspnet/:::no-loc(SignalR):::#2228](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="780cb-165">在「事先」編譯環境中 MessagePack 支援</span><span class="sxs-lookup"><span data-stu-id="780cb-165">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="780cb-166">.NET 用戶端和伺服器所使用的 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) 程式庫會使用程式碼產生，將序列化優化。</span><span class="sxs-lookup"><span data-stu-id="780cb-166">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="780cb-167">因此，在使用「預先」編譯 (（例如 Xamarin iOS 或 Unity) ）的環境上，預設不會支援此方法。</span><span class="sxs-lookup"><span data-stu-id="780cb-167">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="780cb-168">您可以藉由「預先產生」序列化程式/還原序列化程式碼，在這些環境中使用 MessagePack。</span><span class="sxs-lookup"><span data-stu-id="780cb-168">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="780cb-169">如需詳細資訊，請參閱 [MessagePack-CSharp 檔](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin)。</span><span class="sxs-lookup"><span data-stu-id="780cb-169">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="780cb-170">當您預先產生序列化程式之後，您可以使用傳遞至的設定委派來註冊它們 `AddMessagePackProtocol` ：</span><span class="sxs-lookup"><span data-stu-id="780cb-170">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="780cb-171">MessagePack 中的類型檢查較嚴格</span><span class="sxs-lookup"><span data-stu-id="780cb-171">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="780cb-172">JSON Hub 通訊協定會在還原序列化期間執行類型轉換。</span><span class="sxs-lookup"><span data-stu-id="780cb-172">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="780cb-173">例如，如果傳入物件的屬性值是數位 (`{ foo: 42 }`) 但 .net 類別上的屬性是類型 `string` ，則值將會轉換。</span><span class="sxs-lookup"><span data-stu-id="780cb-173">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="780cb-174">但是，MessagePack 不會執行這項轉換，而且會擲回例外狀況，在伺服器端記錄檔 (和主控台) 中會出現此例外狀況：</span><span class="sxs-lookup"><span data-stu-id="780cb-174">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="780cb-175">如需這項限制的詳細資訊，請參閱 GitHub 問題 [aspnet/ :::no-loc(SignalR)::: #2937](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2937)。</span><span class="sxs-lookup"><span data-stu-id="780cb-175">For more information on this limitation, see GitHub issue [aspnet/:::no-loc(SignalR):::#2937](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="780cb-176">相關資源</span><span class="sxs-lookup"><span data-stu-id="780cb-176">Related resources</span></span>

* [<span data-ttu-id="780cb-177">開始使用</span><span class="sxs-lookup"><span data-stu-id="780cb-177">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="780cb-178">.NET 用戶端</span><span class="sxs-lookup"><span data-stu-id="780cb-178">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="780cb-179">JavaScript 用戶端</span><span class="sxs-lookup"><span data-stu-id="780cb-179">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="780cb-180">本文假設讀者已熟悉 [開始](xref:tutorials/signalr)所涵蓋的主題。</span><span class="sxs-lookup"><span data-stu-id="780cb-180">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="780cb-181">什麼是 MessagePack？</span><span class="sxs-lookup"><span data-stu-id="780cb-181">What is MessagePack?</span></span>

<span data-ttu-id="780cb-182">[MessagePack](https://msgpack.org/index.html) 是一種快速且精簡的二進位序列化格式。</span><span class="sxs-lookup"><span data-stu-id="780cb-182">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="780cb-183">當效能和頻寬有問題時，這會很有用，因為它會建立比 [JSON](https://www.json.org/)更小的訊息。</span><span class="sxs-lookup"><span data-stu-id="780cb-183">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="780cb-184">在查看網路追蹤和記錄檔時，除非透過 MessagePack 剖析器傳遞位元組，否則二進位訊息無法讀取。</span><span class="sxs-lookup"><span data-stu-id="780cb-184">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="780cb-185">:::no-loc(SignalR)::: 內建支援 MessagePack 格式，並提供 Api 供用戶端和伺服器使用。</span><span class="sxs-lookup"><span data-stu-id="780cb-185">:::no-loc(SignalR)::: has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="780cb-186">在伺服器上設定 MessagePack</span><span class="sxs-lookup"><span data-stu-id="780cb-186">Configure MessagePack on the server</span></span>

<span data-ttu-id="780cb-187">若要在伺服器上啟用 MessagePack Hub 通訊協定，請 `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` 在您的應用程式中安裝套件。</span><span class="sxs-lookup"><span data-stu-id="780cb-187">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="780cb-188">在 `Startup.ConfigureServices` 方法中，新增 `AddMessagePackProtocol` 至在 `Add:::no-loc(SignalR):::` 伺服器上啟用 MessagePack 支援的呼叫。</span><span class="sxs-lookup"><span data-stu-id="780cb-188">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `Add:::no-loc(SignalR):::` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="780cb-189">預設會啟用 JSON。</span><span class="sxs-lookup"><span data-stu-id="780cb-189">JSON is enabled by default.</span></span> <span data-ttu-id="780cb-190">新增 MessagePack 可支援 JSON 和 MessagePack 用戶端。</span><span class="sxs-lookup"><span data-stu-id="780cb-190">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddMessagePackProtocol();
```

<span data-ttu-id="780cb-191">若要自訂 MessagePack 將如何格式化您的資料，請 `AddMessagePackProtocol` 使用委派來設定選項。</span><span class="sxs-lookup"><span data-stu-id="780cb-191">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="780cb-192">在該委派中， `FormatterResolvers` 屬性可以用來設定 MessagePack 序列化選項。</span><span class="sxs-lookup"><span data-stu-id="780cb-192">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="780cb-193">如需解析程式如何運作的詳細資訊，請造訪 MessagePack library，網址為 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)。</span><span class="sxs-lookup"><span data-stu-id="780cb-193">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="780cb-194">屬性可用於您要序列化的物件，以定義應該如何處理它們。</span><span class="sxs-lookup"><span data-stu-id="780cb-194">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> <span data-ttu-id="780cb-195">強烈建議您檢查 [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) 並套用建議的修補程式。</span><span class="sxs-lookup"><span data-stu-id="780cb-195">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="780cb-196">例如，將 `MessagePackSecurity.Active` 靜態屬性設定為 `MessagePackSecurity.UntrustedData` 。</span><span class="sxs-lookup"><span data-stu-id="780cb-196">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="780cb-197">設定 `MessagePackSecurity.Active` 需要手動安裝 MessagePack 的 [1.9. x 版](https://www.nuget.org/packages/MessagePack/1.9.3)。</span><span class="sxs-lookup"><span data-stu-id="780cb-197">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="780cb-198">安裝 `MessagePack` 1.9. x 版升級時 :::no-loc(SignalR)::: 使用。</span><span class="sxs-lookup"><span data-stu-id="780cb-198">Installing `MessagePack` 1.9.x upgrades the version :::no-loc(SignalR)::: uses.</span></span> <span data-ttu-id="780cb-199">`MessagePack` 2.x 版引進了重大變更，與 :::no-loc(SignalR)::: 3.1 版及更早版本不相容。</span><span class="sxs-lookup"><span data-stu-id="780cb-199">`MessagePack` version 2.x introduced breaking changes and is incompatible with :::no-loc(SignalR)::: versions 3.1 and earlier.</span></span> <span data-ttu-id="780cb-200">當 `MessagePackSecurity.Active` 未設定為時 `MessagePackSecurity.UntrustedData` ，惡意用戶端可能會造成拒絕服務。</span><span class="sxs-lookup"><span data-stu-id="780cb-200">When `MessagePackSecurity.Active` isn't set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="780cb-201">`MessagePackSecurity.Active`在中設定 `Program.Main` ，如下列程式碼所示：</span><span class="sxs-lookup"><span data-stu-id="780cb-201">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="780cb-202">在用戶端上設定 MessagePack</span><span class="sxs-lookup"><span data-stu-id="780cb-202">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="780cb-203">針對支援的用戶端，預設會啟用 JSON。</span><span class="sxs-lookup"><span data-stu-id="780cb-203">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="780cb-204">用戶端只能支援單一通訊協定。</span><span class="sxs-lookup"><span data-stu-id="780cb-204">Clients can only support a single protocol.</span></span> <span data-ttu-id="780cb-205">新增 MessagePack 支援將會取代任何先前設定的通訊協定。</span><span class="sxs-lookup"><span data-stu-id="780cb-205">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="780cb-206">.NET 用戶端</span><span class="sxs-lookup"><span data-stu-id="780cb-206">.NET client</span></span>

<span data-ttu-id="780cb-207">若要在 .NET 用戶端中啟用 MessagePack，請安裝 `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` 封裝並 `AddMessagePackProtocol` 在上呼叫 `HubConnectionBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="780cb-207">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.:::no-loc(SignalR):::.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="780cb-208">此 `AddMessagePackProtocol` 呼叫會採用委派來設定選項，就像伺服器一樣。</span><span class="sxs-lookup"><span data-stu-id="780cb-208">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="780cb-209">JavaScript 用戶端</span><span class="sxs-lookup"><span data-stu-id="780cb-209">JavaScript client</span></span>

<span data-ttu-id="780cb-210">JavaScript 用戶端的 MessagePack 支援是由 [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm 封裝提供。</span><span class="sxs-lookup"><span data-stu-id="780cb-210">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="780cb-211">在命令 shell 中執行下列命令，以安裝套件：</span><span class="sxs-lookup"><span data-stu-id="780cb-211">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="780cb-212">安裝 npm 套件之後，您可以透過 JavaScript 模組載入器直接使用模組，或藉由參考下列檔案將其匯入至瀏覽器：</span><span class="sxs-lookup"><span data-stu-id="780cb-212">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="780cb-213">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="780cb-213">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="780cb-214">在瀏覽器中， `msgpack5` 也必須參考程式庫。</span><span class="sxs-lookup"><span data-stu-id="780cb-214">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="780cb-215">使用 `<script>` 標記來建立參考。</span><span class="sxs-lookup"><span data-stu-id="780cb-215">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="780cb-216">您可以在 *node_modules\msgpack5\dist\msgpack5.js* 找到該程式庫。</span><span class="sxs-lookup"><span data-stu-id="780cb-216">The library can be found at *node_modules\msgpack5\dist\msgpack5.js* .</span></span>

> [!NOTE]
> <span data-ttu-id="780cb-217">使用專案時 `<script>` ，順序很重要。</span><span class="sxs-lookup"><span data-stu-id="780cb-217">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="780cb-218">如果在 *msgpack5.js* 之前參考 *signalr-protocol-msgpack.js* ，則嘗試使用 MessagePack 連接時，就會發生錯誤。</span><span class="sxs-lookup"><span data-stu-id="780cb-218">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js* , an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="780cb-219">*signalr-protocol-msgpack.js* 之前也需要 *signalr.js* 。</span><span class="sxs-lookup"><span data-stu-id="780cb-219">*signalr.js* is also required before *signalr-protocol-msgpack.js* .</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="780cb-220">新增 `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` 至 `HubConnectionBuilder` 會將用戶端設定為在連接到伺服器時使用 MessagePack 通訊協定。</span><span class="sxs-lookup"><span data-stu-id="780cb-220">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="780cb-221">目前，JavaScript 用戶端上沒有 MessagePack 通訊協定的設定選項。</span><span class="sxs-lookup"><span data-stu-id="780cb-221">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="780cb-222">MessagePack 的不相容</span><span class="sxs-lookup"><span data-stu-id="780cb-222">MessagePack quirks</span></span>

<span data-ttu-id="780cb-223">使用 MessagePack 中樞通訊協定時，有幾個要注意的問題。</span><span class="sxs-lookup"><span data-stu-id="780cb-223">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="780cb-224">MessagePack 區分大小寫</span><span class="sxs-lookup"><span data-stu-id="780cb-224">MessagePack is case-sensitive</span></span>

<span data-ttu-id="780cb-225">MessagePack 通訊協定會區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="780cb-225">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="780cb-226">例如，請考慮下列 c # 類別：</span><span class="sxs-lookup"><span data-stu-id="780cb-226">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="780cb-227">從 JavaScript 用戶端傳送時，您必須使用 `PascalCased` 屬性名稱，因為大小寫必須完全符合 c # 類別。</span><span class="sxs-lookup"><span data-stu-id="780cb-227">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="780cb-228">例如：</span><span class="sxs-lookup"><span data-stu-id="780cb-228">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="780cb-229">使用 `camelCased` 名稱並不會正確地系結至 c # 類別。</span><span class="sxs-lookup"><span data-stu-id="780cb-229">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="780cb-230">您可以使用 `Key` 屬性來為 MessagePack 屬性指定不同的名稱，藉此解決這個問題。</span><span class="sxs-lookup"><span data-stu-id="780cb-230">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="780cb-231">如需詳細資訊，請參閱 [MessagePack-CSharp 檔](https://github.com/neuecc/MessagePack-CSharp#object-serialization)。</span><span class="sxs-lookup"><span data-stu-id="780cb-231">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="780cb-232">序列化/還原序列化時，不會保留 DateTime. 種類</span><span class="sxs-lookup"><span data-stu-id="780cb-232">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="780cb-233">MessagePack 通訊協定不提供將的值編碼的方式 `Kind` `DateTime` 。</span><span class="sxs-lookup"><span data-stu-id="780cb-233">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="780cb-234">如此一來，在還原序列化日期時，MessagePack Hub 通訊協定會假設內送日期採用 UTC 格式。</span><span class="sxs-lookup"><span data-stu-id="780cb-234">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="780cb-235">如果您是 `DateTime` 以當地時間處理值，建議您在傳送之前轉換成 UTC。</span><span class="sxs-lookup"><span data-stu-id="780cb-235">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="780cb-236">當您收到時，請將它們從 UTC 轉換為當地時間。</span><span class="sxs-lookup"><span data-stu-id="780cb-236">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="780cb-237">如需這項限制的詳細資訊，請參閱 GitHub 問題 [aspnet/ :::no-loc(SignalR)::: #2632](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2632)。</span><span class="sxs-lookup"><span data-stu-id="780cb-237">For more information on this limitation, see GitHub issue [aspnet/:::no-loc(SignalR):::#2632](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="780cb-238">JavaScript 中的 MessagePack 不支援 MinValue</span><span class="sxs-lookup"><span data-stu-id="780cb-238">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="780cb-239">JavaScript 用戶端所使用的 [msgpack5](https://github.com/mcollina/msgpack5) 程式庫 :::no-loc(SignalR)::: 不支援 `timestamp96` MessagePack 中的類型。</span><span class="sxs-lookup"><span data-stu-id="780cb-239">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the :::no-loc(SignalR)::: JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="780cb-240">這種類型是用來編碼非常大的日期值， (在過去或未來的) 中非常早。</span><span class="sxs-lookup"><span data-stu-id="780cb-240">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="780cb-241">的值 `DateTime.MinValue` 為 `January 1, 0001` ，必須以值進行編碼 `timestamp96` 。</span><span class="sxs-lookup"><span data-stu-id="780cb-241">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="780cb-242">基於這個原因， `DateTime.MinValue` 不支援傳送至 JavaScript 用戶端。</span><span class="sxs-lookup"><span data-stu-id="780cb-242">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="780cb-243">當 `DateTime.MinValue` JavaScript 用戶端收到時，會擲回下列錯誤：</span><span class="sxs-lookup"><span data-stu-id="780cb-243">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="780cb-244">通常 `DateTime.MinValue` 是用來編碼「遺失」或 `null` 值。</span><span class="sxs-lookup"><span data-stu-id="780cb-244">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="780cb-245">如果您需要在 MessagePack 中編碼該值，請使用可為 null 的 `DateTime` 值 (`DateTime?`) 或編碼個別 `bool` 值，以指出日期是否存在。</span><span class="sxs-lookup"><span data-stu-id="780cb-245">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="780cb-246">如需這項限制的詳細資訊，請參閱 GitHub 問題 [aspnet/ :::no-loc(SignalR)::: #2228](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2228)。</span><span class="sxs-lookup"><span data-stu-id="780cb-246">For more information on this limitation, see GitHub issue [aspnet/:::no-loc(SignalR):::#2228](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="780cb-247">在「事先」編譯環境中 MessagePack 支援</span><span class="sxs-lookup"><span data-stu-id="780cb-247">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="780cb-248">.NET 用戶端和伺服器所使用的 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) 程式庫會使用程式碼產生，將序列化優化。</span><span class="sxs-lookup"><span data-stu-id="780cb-248">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="780cb-249">因此，在使用「預先」編譯 (（例如 Xamarin iOS 或 Unity) ）的環境上，預設不會支援此方法。</span><span class="sxs-lookup"><span data-stu-id="780cb-249">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="780cb-250">您可以藉由「預先產生」序列化程式/還原序列化程式碼，在這些環境中使用 MessagePack。</span><span class="sxs-lookup"><span data-stu-id="780cb-250">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="780cb-251">如需詳細資訊，請參閱 [MessagePack-CSharp 檔](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports)。</span><span class="sxs-lookup"><span data-stu-id="780cb-251">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="780cb-252">當您預先產生序列化程式之後，您可以使用傳遞至的設定委派來註冊它們 `AddMessagePackProtocol` ：</span><span class="sxs-lookup"><span data-stu-id="780cb-252">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="780cb-253">MessagePack 中的類型檢查較嚴格</span><span class="sxs-lookup"><span data-stu-id="780cb-253">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="780cb-254">JSON Hub 通訊協定會在還原序列化期間執行類型轉換。</span><span class="sxs-lookup"><span data-stu-id="780cb-254">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="780cb-255">例如，如果傳入物件的屬性值是數位 (`{ foo: 42 }`) 但 .net 類別上的屬性是類型 `string` ，則值將會轉換。</span><span class="sxs-lookup"><span data-stu-id="780cb-255">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="780cb-256">但是，MessagePack 不會執行這項轉換，而且會擲回例外狀況，在伺服器端記錄檔 (和主控台) 中會出現此例外狀況：</span><span class="sxs-lookup"><span data-stu-id="780cb-256">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="780cb-257">如需這項限制的詳細資訊，請參閱 GitHub 問題 [aspnet/ :::no-loc(SignalR)::: #2937](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2937)。</span><span class="sxs-lookup"><span data-stu-id="780cb-257">For more information on this limitation, see GitHub issue [aspnet/:::no-loc(SignalR):::#2937](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="780cb-258">相關資源</span><span class="sxs-lookup"><span data-stu-id="780cb-258">Related resources</span></span>

* [<span data-ttu-id="780cb-259">開始使用</span><span class="sxs-lookup"><span data-stu-id="780cb-259">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="780cb-260">.NET 用戶端</span><span class="sxs-lookup"><span data-stu-id="780cb-260">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="780cb-261">JavaScript 用戶端</span><span class="sxs-lookup"><span data-stu-id="780cb-261">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="780cb-262">本文假設讀者已熟悉 [開始](xref:tutorials/signalr)所涵蓋的主題。</span><span class="sxs-lookup"><span data-stu-id="780cb-262">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="780cb-263">什麼是 MessagePack？</span><span class="sxs-lookup"><span data-stu-id="780cb-263">What is MessagePack?</span></span>

<span data-ttu-id="780cb-264">[MessagePack](https://msgpack.org/index.html) 是一種快速且精簡的二進位序列化格式。</span><span class="sxs-lookup"><span data-stu-id="780cb-264">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="780cb-265">當效能和頻寬有問題時，這會很有用，因為它會建立比 [JSON](https://www.json.org/)更小的訊息。</span><span class="sxs-lookup"><span data-stu-id="780cb-265">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="780cb-266">在查看網路追蹤和記錄檔時，除非透過 MessagePack 剖析器傳遞位元組，否則二進位訊息無法讀取。</span><span class="sxs-lookup"><span data-stu-id="780cb-266">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="780cb-267">:::no-loc(SignalR)::: 內建支援 MessagePack 格式，並提供 Api 供用戶端和伺服器使用。</span><span class="sxs-lookup"><span data-stu-id="780cb-267">:::no-loc(SignalR)::: has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="780cb-268">在伺服器上設定 MessagePack</span><span class="sxs-lookup"><span data-stu-id="780cb-268">Configure MessagePack on the server</span></span>

<span data-ttu-id="780cb-269">若要在伺服器上啟用 MessagePack Hub 通訊協定，請 `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` 在您的應用程式中安裝套件。</span><span class="sxs-lookup"><span data-stu-id="780cb-269">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="780cb-270">在 `Startup.ConfigureServices` 方法中，新增 `AddMessagePackProtocol` 至在 `Add:::no-loc(SignalR):::` 伺服器上啟用 MessagePack 支援的呼叫。</span><span class="sxs-lookup"><span data-stu-id="780cb-270">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `Add:::no-loc(SignalR):::` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="780cb-271">預設會啟用 JSON。</span><span class="sxs-lookup"><span data-stu-id="780cb-271">JSON is enabled by default.</span></span> <span data-ttu-id="780cb-272">新增 MessagePack 可支援 JSON 和 MessagePack 用戶端。</span><span class="sxs-lookup"><span data-stu-id="780cb-272">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddMessagePackProtocol();
```

<span data-ttu-id="780cb-273">若要自訂 MessagePack 將如何格式化您的資料，請 `AddMessagePackProtocol` 使用委派來設定選項。</span><span class="sxs-lookup"><span data-stu-id="780cb-273">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="780cb-274">在該委派中， `FormatterResolvers` 屬性可以用來設定 MessagePack 序列化選項。</span><span class="sxs-lookup"><span data-stu-id="780cb-274">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="780cb-275">如需解析程式如何運作的詳細資訊，請造訪 MessagePack library，網址為 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)。</span><span class="sxs-lookup"><span data-stu-id="780cb-275">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="780cb-276">屬性可用於您要序列化的物件，以定義應該如何處理它們。</span><span class="sxs-lookup"><span data-stu-id="780cb-276">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> <span data-ttu-id="780cb-277">強烈建議您檢查 [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) 並套用建議的修補程式。</span><span class="sxs-lookup"><span data-stu-id="780cb-277">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="780cb-278">例如，將 `MessagePackSecurity.Active` 靜態屬性設定為 `MessagePackSecurity.UntrustedData` 。</span><span class="sxs-lookup"><span data-stu-id="780cb-278">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="780cb-279">設定 `MessagePackSecurity.Active` 需要手動安裝 MessagePack 的 [1.9. x 版](https://www.nuget.org/packages/MessagePack/1.9.3)。</span><span class="sxs-lookup"><span data-stu-id="780cb-279">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="780cb-280">安裝 `MessagePack` 1.9. x 版升級時 :::no-loc(SignalR)::: 使用。</span><span class="sxs-lookup"><span data-stu-id="780cb-280">Installing `MessagePack` 1.9.x upgrades the version :::no-loc(SignalR)::: uses.</span></span> <span data-ttu-id="780cb-281">當未 `MessagePackSecurity.Active` 設定為時 `MessagePackSecurity.UntrustedData` ，惡意用戶端可能會造成阻絕服務。</span><span class="sxs-lookup"><span data-stu-id="780cb-281">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="780cb-282">`MessagePackSecurity.Active`在中設定 `Program.Main` ，如下列程式碼所示：</span><span class="sxs-lookup"><span data-stu-id="780cb-282">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="780cb-283">在用戶端上設定 MessagePack</span><span class="sxs-lookup"><span data-stu-id="780cb-283">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="780cb-284">針對支援的用戶端，預設會啟用 JSON。</span><span class="sxs-lookup"><span data-stu-id="780cb-284">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="780cb-285">用戶端只能支援單一通訊協定。</span><span class="sxs-lookup"><span data-stu-id="780cb-285">Clients can only support a single protocol.</span></span> <span data-ttu-id="780cb-286">新增 MessagePack 支援將會取代任何先前設定的通訊協定。</span><span class="sxs-lookup"><span data-stu-id="780cb-286">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="780cb-287">.NET 用戶端</span><span class="sxs-lookup"><span data-stu-id="780cb-287">.NET client</span></span>

<span data-ttu-id="780cb-288">若要在 .NET 用戶端中啟用 MessagePack，請安裝 `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` 封裝並 `AddMessagePackProtocol` 在上呼叫 `HubConnectionBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="780cb-288">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.:::no-loc(SignalR):::.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="780cb-289">此 `AddMessagePackProtocol` 呼叫會採用委派來設定選項，就像伺服器一樣。</span><span class="sxs-lookup"><span data-stu-id="780cb-289">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="780cb-290">JavaScript 用戶端</span><span class="sxs-lookup"><span data-stu-id="780cb-290">JavaScript client</span></span>

<span data-ttu-id="780cb-291">JavaScript 用戶端的 MessagePack 支援是由 [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm 封裝提供。</span><span class="sxs-lookup"><span data-stu-id="780cb-291">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="780cb-292">在命令 shell 中執行下列命令，以安裝套件：</span><span class="sxs-lookup"><span data-stu-id="780cb-292">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="780cb-293">安裝 npm 套件之後，您可以透過 JavaScript 模組載入器直接使用模組，或藉由參考下列檔案將其匯入至瀏覽器：</span><span class="sxs-lookup"><span data-stu-id="780cb-293">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="780cb-294">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="780cb-294">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="780cb-295">在瀏覽器中， `msgpack5` 也必須參考程式庫。</span><span class="sxs-lookup"><span data-stu-id="780cb-295">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="780cb-296">使用 `<script>` 標記來建立參考。</span><span class="sxs-lookup"><span data-stu-id="780cb-296">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="780cb-297">您可以在 *node_modules\msgpack5\dist\msgpack5.js* 找到該程式庫。</span><span class="sxs-lookup"><span data-stu-id="780cb-297">The library can be found at *node_modules\msgpack5\dist\msgpack5.js* .</span></span>

> [!NOTE]
> <span data-ttu-id="780cb-298">使用專案時 `<script>` ，順序很重要。</span><span class="sxs-lookup"><span data-stu-id="780cb-298">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="780cb-299">如果在 *msgpack5.js* 之前參考 *signalr-protocol-msgpack.js* ，則嘗試使用 MessagePack 連接時，就會發生錯誤。</span><span class="sxs-lookup"><span data-stu-id="780cb-299">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js* , an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="780cb-300">*signalr-protocol-msgpack.js* 之前也需要 *signalr.js* 。</span><span class="sxs-lookup"><span data-stu-id="780cb-300">*signalr.js* is also required before *signalr-protocol-msgpack.js* .</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="780cb-301">新增 `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` 至 `HubConnectionBuilder` 會將用戶端設定為在連接到伺服器時使用 MessagePack 通訊協定。</span><span class="sxs-lookup"><span data-stu-id="780cb-301">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="780cb-302">目前，JavaScript 用戶端上沒有 MessagePack 通訊協定的設定選項。</span><span class="sxs-lookup"><span data-stu-id="780cb-302">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="780cb-303">MessagePack 的不相容</span><span class="sxs-lookup"><span data-stu-id="780cb-303">MessagePack quirks</span></span>

<span data-ttu-id="780cb-304">使用 MessagePack 中樞通訊協定時，有幾個要注意的問題。</span><span class="sxs-lookup"><span data-stu-id="780cb-304">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="780cb-305">MessagePack 區分大小寫</span><span class="sxs-lookup"><span data-stu-id="780cb-305">MessagePack is case-sensitive</span></span>

<span data-ttu-id="780cb-306">MessagePack 通訊協定會區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="780cb-306">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="780cb-307">例如，請考慮下列 c # 類別：</span><span class="sxs-lookup"><span data-stu-id="780cb-307">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="780cb-308">從 JavaScript 用戶端傳送時，您必須使用 `PascalCased` 屬性名稱，因為大小寫必須完全符合 c # 類別。</span><span class="sxs-lookup"><span data-stu-id="780cb-308">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="780cb-309">例如：</span><span class="sxs-lookup"><span data-stu-id="780cb-309">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="780cb-310">使用 `camelCased` 名稱並不會正確地系結至 c # 類別。</span><span class="sxs-lookup"><span data-stu-id="780cb-310">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="780cb-311">您可以使用 `Key` 屬性來為 MessagePack 屬性指定不同的名稱，藉此解決這個問題。</span><span class="sxs-lookup"><span data-stu-id="780cb-311">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="780cb-312">如需詳細資訊，請參閱 [MessagePack-CSharp 檔](https://github.com/neuecc/MessagePack-CSharp#object-serialization)。</span><span class="sxs-lookup"><span data-stu-id="780cb-312">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="780cb-313">序列化/還原序列化時，不會保留 DateTime. 種類</span><span class="sxs-lookup"><span data-stu-id="780cb-313">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="780cb-314">MessagePack 通訊協定不提供將的值編碼的方式 `Kind` `DateTime` 。</span><span class="sxs-lookup"><span data-stu-id="780cb-314">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="780cb-315">如此一來，在還原序列化日期時，MessagePack Hub 通訊協定會假設內送日期採用 UTC 格式。</span><span class="sxs-lookup"><span data-stu-id="780cb-315">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="780cb-316">如果您是 `DateTime` 以當地時間處理值，建議您在傳送之前轉換成 UTC。</span><span class="sxs-lookup"><span data-stu-id="780cb-316">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="780cb-317">當您收到時，請將它們從 UTC 轉換為當地時間。</span><span class="sxs-lookup"><span data-stu-id="780cb-317">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="780cb-318">如需這項限制的詳細資訊，請參閱 GitHub 問題 [aspnet/ :::no-loc(SignalR)::: #2632](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2632)。</span><span class="sxs-lookup"><span data-stu-id="780cb-318">For more information on this limitation, see GitHub issue [aspnet/:::no-loc(SignalR):::#2632](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="780cb-319">JavaScript 中的 MessagePack 不支援 MinValue</span><span class="sxs-lookup"><span data-stu-id="780cb-319">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="780cb-320">JavaScript 用戶端所使用的 [msgpack5](https://github.com/mcollina/msgpack5) 程式庫 :::no-loc(SignalR)::: 不支援 `timestamp96` MessagePack 中的類型。</span><span class="sxs-lookup"><span data-stu-id="780cb-320">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the :::no-loc(SignalR)::: JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="780cb-321">這種類型是用來編碼非常大的日期值， (在過去或未來的) 中非常早。</span><span class="sxs-lookup"><span data-stu-id="780cb-321">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="780cb-322">的值 `DateTime.MinValue` `January 1, 0001` 必須以值進行編碼 `timestamp96` 。</span><span class="sxs-lookup"><span data-stu-id="780cb-322">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="780cb-323">基於這個原因， `DateTime.MinValue` 不支援傳送至 JavaScript 用戶端。</span><span class="sxs-lookup"><span data-stu-id="780cb-323">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="780cb-324">當 `DateTime.MinValue` JavaScript 用戶端收到時，會擲回下列錯誤：</span><span class="sxs-lookup"><span data-stu-id="780cb-324">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="780cb-325">通常 `DateTime.MinValue` 是用來編碼「遺失」或 `null` 值。</span><span class="sxs-lookup"><span data-stu-id="780cb-325">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="780cb-326">如果您需要在 MessagePack 中編碼該值，請使用可為 null 的 `DateTime` 值 (`DateTime?`) 或編碼個別 `bool` 值，以指出日期是否存在。</span><span class="sxs-lookup"><span data-stu-id="780cb-326">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="780cb-327">如需這項限制的詳細資訊，請參閱 GitHub 問題 [aspnet/ :::no-loc(SignalR)::: #2228](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2228)。</span><span class="sxs-lookup"><span data-stu-id="780cb-327">For more information on this limitation, see GitHub issue [aspnet/:::no-loc(SignalR):::#2228](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="780cb-328">在「事先」編譯環境中 MessagePack 支援</span><span class="sxs-lookup"><span data-stu-id="780cb-328">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="780cb-329">.NET 用戶端和伺服器所使用的 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) 程式庫會使用程式碼產生，將序列化優化。</span><span class="sxs-lookup"><span data-stu-id="780cb-329">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="780cb-330">因此，在使用「預先」編譯 (（例如 Xamarin iOS 或 Unity) ）的環境上，預設不會支援此方法。</span><span class="sxs-lookup"><span data-stu-id="780cb-330">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="780cb-331">您可以藉由「預先產生」序列化程式/還原序列化程式碼，在這些環境中使用 MessagePack。</span><span class="sxs-lookup"><span data-stu-id="780cb-331">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="780cb-332">如需詳細資訊，請參閱 [MessagePack-CSharp 檔](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports)。</span><span class="sxs-lookup"><span data-stu-id="780cb-332">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="780cb-333">當您預先產生序列化程式之後，您可以使用傳遞至的設定委派來註冊它們 `AddMessagePackProtocol` ：</span><span class="sxs-lookup"><span data-stu-id="780cb-333">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="780cb-334">MessagePack 中的類型檢查較嚴格</span><span class="sxs-lookup"><span data-stu-id="780cb-334">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="780cb-335">JSON Hub 通訊協定會在還原序列化期間執行類型轉換。</span><span class="sxs-lookup"><span data-stu-id="780cb-335">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="780cb-336">例如，如果傳入物件的屬性值是數位 (`{ foo: 42 }`) 但 .net 類別上的屬性是類型 `string` ，則值將會轉換。</span><span class="sxs-lookup"><span data-stu-id="780cb-336">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="780cb-337">但是，MessagePack 不會執行這項轉換，而且會擲回例外狀況，在伺服器端記錄檔 (和主控台) 中會出現此例外狀況：</span><span class="sxs-lookup"><span data-stu-id="780cb-337">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="780cb-338">如需這項限制的詳細資訊，請參閱 GitHub 問題 [aspnet/ :::no-loc(SignalR)::: #2937](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2937)。</span><span class="sxs-lookup"><span data-stu-id="780cb-338">For more information on this limitation, see GitHub issue [aspnet/:::no-loc(SignalR):::#2937](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="780cb-339">相關資源</span><span class="sxs-lookup"><span data-stu-id="780cb-339">Related resources</span></span>

* [<span data-ttu-id="780cb-340">開始使用</span><span class="sxs-lookup"><span data-stu-id="780cb-340">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="780cb-341">.NET 用戶端</span><span class="sxs-lookup"><span data-stu-id="780cb-341">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="780cb-342">JavaScript 用戶端</span><span class="sxs-lookup"><span data-stu-id="780cb-342">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
