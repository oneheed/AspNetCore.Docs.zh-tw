---
title: ASP.NET Core SignalR JavaScript 用戶端
author: bradygaster
description: ASP.NET Core SignalR JavaScript 用戶端的總覽。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
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
uid: signalr/javascript-client
ms.openlocfilehash: 359aa2b9e6b7f826d75f10645b7f2b565ab48b7a
ms.sourcegitcommit: 62cc131969b2379f7a45c286a751e22d961dfbdb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90847685"
---
# <a name="aspnet-core-no-locsignalr-javascript-client"></a><span data-ttu-id="00851-103">ASP.NET Core SignalR JavaScript 用戶端</span><span class="sxs-lookup"><span data-stu-id="00851-103">ASP.NET Core SignalR JavaScript client</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="00851-104">作者：[Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="00851-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="00851-105">ASP.NET Core SignalR JavaScript 用戶端程式庫可讓開發人員呼叫伺服器端中樞程式碼。</span><span class="sxs-lookup"><span data-stu-id="00851-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="00851-106">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="00851-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-client-package"></a><span data-ttu-id="00851-107">安裝 SignalR 用戶端套件</span><span class="sxs-lookup"><span data-stu-id="00851-107">Install the SignalR client package</span></span>

<span data-ttu-id="00851-108">SignalRJavaScript 用戶端程式庫會以[npm](https://www.npmjs.com/)套件的形式傳遞。</span><span class="sxs-lookup"><span data-stu-id="00851-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="00851-109">下列各節概述安裝用戶端程式庫的不同方式。</span><span class="sxs-lookup"><span data-stu-id="00851-109">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="00851-110">使用 npm 安裝</span><span class="sxs-lookup"><span data-stu-id="00851-110">Install with npm</span></span>

<span data-ttu-id="00851-111">針對 Visual Studio，請在根資料夾中從 **封裝管理員主控台** 執行下列命令。</span><span class="sxs-lookup"><span data-stu-id="00851-111">For Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="00851-112">針對 Visual Studio Code，請從 **整合式終端**機執行下列命令。</span><span class="sxs-lookup"><span data-stu-id="00851-112">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="00851-113">npm 會將套件內容安裝\*node_modules \\ @microsoft\signalr\dist\browser \*資料夾。</span><span class="sxs-lookup"><span data-stu-id="00851-113">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="00851-114">在*wwwroot \\ lib*資料夾下建立名為*signalr*的新資料夾。</span><span class="sxs-lookup"><span data-stu-id="00851-114">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="00851-115">將 *signalr.js* 檔案複製到 *wwwroot\lib\signalr* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="00851-115">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

<span data-ttu-id="00851-116">參考 SignalR 元素中的 JavaScript 用戶端 `<script>` 。</span><span class="sxs-lookup"><span data-stu-id="00851-116">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="00851-117">例如：</span><span class="sxs-lookup"><span data-stu-id="00851-117">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="00851-118">使用 (CDN) 的內容傳遞網路</span><span class="sxs-lookup"><span data-stu-id="00851-118">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="00851-119">若要在沒有 npm 必要條件的情況下使用用戶端程式庫，請參考用戶端程式庫的 CDN 託管複本。</span><span class="sxs-lookup"><span data-stu-id="00851-119">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="00851-120">例如：</span><span class="sxs-lookup"><span data-stu-id="00851-120">For example:</span></span>

[!code-html[](javascript-client/samples/3.x/SignalRChat/Pages/Index.cshtml?name=snippet_CDN)]

<span data-ttu-id="00851-121">用戶端程式庫可在下列 Cdn 上取得：</span><span class="sxs-lookup"><span data-stu-id="00851-121">The client library is available on the following CDNs:</span></span>

* [<span data-ttu-id="00851-122">cdnjs</span><span class="sxs-lookup"><span data-stu-id="00851-122">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="00851-123">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="00851-123">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="00851-124">unpkg</span><span class="sxs-lookup"><span data-stu-id="00851-124">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a><span data-ttu-id="00851-125">使用 LibMan 安裝</span><span class="sxs-lookup"><span data-stu-id="00851-125">Install with LibMan</span></span>

<span data-ttu-id="00851-126">[LibMan](xref:client-side/libman/index) 可以用來從 CDN 裝載的用戶端程式庫安裝特定的用戶端程式庫檔案。</span><span class="sxs-lookup"><span data-stu-id="00851-126">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="00851-127">例如，只將縮減 JavaScript 檔案新增至專案。</span><span class="sxs-lookup"><span data-stu-id="00851-127">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="00851-128">如需該方法的詳細資訊，請參閱 [新增 SignalR 用戶端程式庫](xref:tutorials/signalr#add-the-signalr-client-library)。</span><span class="sxs-lookup"><span data-stu-id="00851-128">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="00851-129">連接至中樞</span><span class="sxs-lookup"><span data-stu-id="00851-129">Connect to a hub</span></span>

<span data-ttu-id="00851-130">下列程式碼會建立並啟動連接。</span><span class="sxs-lookup"><span data-stu-id="00851-130">The following code creates and starts a connection.</span></span> <span data-ttu-id="00851-131">中樞的名稱不區分大小寫：</span><span class="sxs-lookup"><span data-stu-id="00851-131">The hub's name is case insensitive:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?range=3-6,29-43)]

### <a name="cross-origin-connections"></a><span data-ttu-id="00851-132">跨原始連接</span><span class="sxs-lookup"><span data-stu-id="00851-132">Cross-origin connections</span></span>

<span data-ttu-id="00851-133">一般而言，瀏覽器會從與要求的頁面相同的網域載入連接。</span><span class="sxs-lookup"><span data-stu-id="00851-133">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="00851-134">不過，在某些情況下需要連接到另一個網域。</span><span class="sxs-lookup"><span data-stu-id="00851-134">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="00851-135">為了防止惡意網站從另一個網站讀取敏感性資料，預設會停用 [跨原始來源的連接](xref:security/cors) 。</span><span class="sxs-lookup"><span data-stu-id="00851-135">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="00851-136">若要允許跨原始來源的要求，請在類別中啟用它 `Startup` ：</span><span class="sxs-lookup"><span data-stu-id="00851-136">To allow a cross-origin request, enable it in the `Startup` class:</span></span>

[!code-csharp[](javascript-client/samples/3.x/SignalRChat/Startup.cs?highlight=16-23,40)]

## <a name="call-hub-methods-from-the-client"></a><span data-ttu-id="00851-137">從用戶端呼叫中樞方法</span><span class="sxs-lookup"><span data-stu-id="00851-137">Call hub methods from the client</span></span>

<span data-ttu-id="00851-138">JavaScript 用戶端會透過[HubConnection](/javascript/api/%40microsoft/signalr/hubconnection)的[invoke](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---)方法，呼叫中樞上的公用方法。</span><span class="sxs-lookup"><span data-stu-id="00851-138">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---) method of the [HubConnection](/javascript/api/%40microsoft/signalr/hubconnection).</span></span> <span data-ttu-id="00851-139">此 `invoke` 方法會接受：</span><span class="sxs-lookup"><span data-stu-id="00851-139">The `invoke` method accepts:</span></span>

* <span data-ttu-id="00851-140">中樞方法的名稱。</span><span class="sxs-lookup"><span data-stu-id="00851-140">The name of the hub method.</span></span>
* <span data-ttu-id="00851-141">中樞方法中定義的任何引數。</span><span class="sxs-lookup"><span data-stu-id="00851-141">Any arguments defined in the hub method.</span></span>

<span data-ttu-id="00851-142">在下列範例中，中樞上的方法名稱為 `SendMessage` 。</span><span class="sxs-lookup"><span data-stu-id="00851-142">In the following example, the method name on the hub is `SendMessage`.</span></span> <span data-ttu-id="00851-143">傳遞給的第二個和第三個引數會 `invoke` 對應至中樞方法的 `user` 和 `message` 引數：</span><span class="sxs-lookup"><span data-stu-id="00851-143">The second and third arguments passed to `invoke` map to the hub method's `user` and `message` arguments:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Invoke&highlight=2)]

> [!NOTE]
> <span data-ttu-id="00851-144">只有 SignalR 在 *預設* 模式下使用 Azure 服務時，才支援從用戶端呼叫中樞方法。</span><span class="sxs-lookup"><span data-stu-id="00851-144">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="00851-145">如需詳細資訊，請參閱 [ (azure Signalr GitHub 存放庫) ](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose)的常見問題。</span><span class="sxs-lookup"><span data-stu-id="00851-145">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

<span data-ttu-id="00851-146">方法會傳回 `invoke` JavaScript [承諾](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)。</span><span class="sxs-lookup"><span data-stu-id="00851-146">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="00851-147">`Promise`當伺服器上的方法傳回時，會使用傳回值來解析 (是否有任何) 。</span><span class="sxs-lookup"><span data-stu-id="00851-147">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="00851-148">如果伺服器上的方法擲回錯誤，則 `Promise` 會被拒絕，並顯示錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="00851-148">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="00851-149">使用 `async` 和 `await` 或的 `Promise` `then` 和 `catch` 方法來處理這些情況。</span><span class="sxs-lookup"><span data-stu-id="00851-149">Use `async` and `await` or the `Promise`'s `then` and `catch` methods to handle these cases.</span></span>

<span data-ttu-id="00851-150">JavaScript 用戶端也可以透過的 [傳送](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) 方法，呼叫中樞上的公用方法 `HubConnection` 。</span><span class="sxs-lookup"><span data-stu-id="00851-150">JavaScript clients can also call public methods on hubs via the the [send](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) method of the `HubConnection`.</span></span> <span data-ttu-id="00851-151">與方法不同的是 `invoke` ， `send` 方法不會等候伺服器的回應。</span><span class="sxs-lookup"><span data-stu-id="00851-151">Unlike the `invoke` method, the `send` method doesn't wait for a response from the server.</span></span> <span data-ttu-id="00851-152">方法會傳回 `send` JavaScript `Promise` 。</span><span class="sxs-lookup"><span data-stu-id="00851-152">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="00851-153">`Promise`當訊息傳送到伺服器時，會解析。</span><span class="sxs-lookup"><span data-stu-id="00851-153">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="00851-154">如果傳送訊息時發生錯誤， `Promise` 就會拒絕，並顯示錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="00851-154">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="00851-155">使用 `async` 和 `await` 或的 `Promise` `then` 和 `catch` 方法來處理這些情況。</span><span class="sxs-lookup"><span data-stu-id="00851-155">Use `async` and `await` or the `Promise`'s `then` and `catch` methods to handle these cases.</span></span>

> [!NOTE]
> <span data-ttu-id="00851-156">使用 `send` 並不會等到伺服器收到訊息為止。</span><span class="sxs-lookup"><span data-stu-id="00851-156">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="00851-157">因此，不可能從伺服器傳回資料或錯誤。</span><span class="sxs-lookup"><span data-stu-id="00851-157">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-the-hub"></a><span data-ttu-id="00851-158">從中樞呼叫用戶端方法</span><span class="sxs-lookup"><span data-stu-id="00851-158">Call client methods from the hub</span></span>

<span data-ttu-id="00851-159">若要從中樞接收訊息，請使用的 [on](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) 方法定義方法 `HubConnection` 。</span><span class="sxs-lookup"><span data-stu-id="00851-159">To receive messages from the hub, define a method using the [on](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) method of the `HubConnection`.</span></span>

* <span data-ttu-id="00851-160">JavaScript 用戶端方法的名稱。</span><span class="sxs-lookup"><span data-stu-id="00851-160">The name of the JavaScript client method.</span></span>
* <span data-ttu-id="00851-161">中樞傳遞給方法的引數。</span><span class="sxs-lookup"><span data-stu-id="00851-161">Arguments the hub passes to the method.</span></span>

<span data-ttu-id="00851-162">在下列範例中，方法名稱為 `ReceiveMessage` 。</span><span class="sxs-lookup"><span data-stu-id="00851-162">In the following example, the method name is `ReceiveMessage`.</span></span> <span data-ttu-id="00851-163">引數名稱為 `user` 和 `message` ：</span><span class="sxs-lookup"><span data-stu-id="00851-163">The argument names are `user` and `message`:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_ReceiveMessage)]

<span data-ttu-id="00851-164">中的上述程式碼 `connection.on` 會在伺服器端程式碼使用方法呼叫它時執行 <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> ：</span><span class="sxs-lookup"><span data-stu-id="00851-164">The preceding code in `connection.on` runs when server-side code calls it using the <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> method:</span></span>

[!code-csharp[Call client-side](javascript-client/samples/3.x/SignalRChat/Hubs/ChatHub.cs?name=snippet_SendMessage)]

<span data-ttu-id="00851-165">SignalR 藉由比對和中定義的方法名稱和引數，判斷要呼叫的用戶端方法 `SendAsync` `connection.on` 。</span><span class="sxs-lookup"><span data-stu-id="00851-165">SignalR determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="00851-166">最佳做法是在之後呼叫 [start](/javascript/api/%40aspnet/signalr/hubconnection#start) 方法 `HubConnection` `on` 。</span><span class="sxs-lookup"><span data-stu-id="00851-166">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="00851-167">這麼做可確保您的處理常式會在收到任何訊息之前註冊。</span><span class="sxs-lookup"><span data-stu-id="00851-167">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="00851-168">錯誤處理和記錄</span><span class="sxs-lookup"><span data-stu-id="00851-168">Error handling and logging</span></span>

<span data-ttu-id="00851-169">使用 `try` 和 `catch` 搭配 `async` 和 `await` 或的 `Promise` `catch` 方法來處理用戶端錯誤。</span><span class="sxs-lookup"><span data-stu-id="00851-169">Use `try` and `catch` with `async` and `await` or the `Promise`'s `catch` method to handle client-side errors.</span></span> <span data-ttu-id="00851-170">使用 `console.error` 將錯誤輸出至瀏覽器的主控台：</span><span class="sxs-lookup"><span data-stu-id="00851-170">Use `console.error` to output errors to the browser's console:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Invoke&highlight=1,3-5)]

<span data-ttu-id="00851-171">設定用戶端記錄檔追蹤，方法是在建立連接時，傳遞記錄器和事件種類來記錄。</span><span class="sxs-lookup"><span data-stu-id="00851-171">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="00851-172">訊息會以指定的記錄層級和更新版本記錄。</span><span class="sxs-lookup"><span data-stu-id="00851-172">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="00851-173">可用的記錄層級如下所示：</span><span class="sxs-lookup"><span data-stu-id="00851-173">Available log levels are as follows:</span></span>

* <span data-ttu-id="00851-174">`signalR.LogLevel.Error`：錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="00851-174">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="00851-175">`Error`只記錄訊息。</span><span class="sxs-lookup"><span data-stu-id="00851-175">Logs `Error` messages only.</span></span>
* <span data-ttu-id="00851-176">`signalR.LogLevel.Warning`：可能發生錯誤的警告訊息。</span><span class="sxs-lookup"><span data-stu-id="00851-176">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="00851-177">記錄檔 `Warning` 和 `Error` 訊息。</span><span class="sxs-lookup"><span data-stu-id="00851-177">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="00851-178">`signalR.LogLevel.Information`：沒有錯誤的狀態訊息。</span><span class="sxs-lookup"><span data-stu-id="00851-178">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="00851-179">記錄 `Information` 、 `Warning` 和 `Error` 訊息。</span><span class="sxs-lookup"><span data-stu-id="00851-179">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="00851-180">`signalR.LogLevel.Trace`：追蹤訊息。</span><span class="sxs-lookup"><span data-stu-id="00851-180">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="00851-181">記錄所有內容，包括中樞與用戶端之間傳輸的資料。</span><span class="sxs-lookup"><span data-stu-id="00851-181">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="00851-182">在[HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)上使用[configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging)方法來設定記錄層級。</span><span class="sxs-lookup"><span data-stu-id="00851-182">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="00851-183">訊息會記錄到瀏覽器主控台：</span><span class="sxs-lookup"><span data-stu-id="00851-183">Messages are logged to the browser console:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Connection&highlight=3)]

## <a name="reconnect-clients"></a><span data-ttu-id="00851-184">重新連線用戶端</span><span class="sxs-lookup"><span data-stu-id="00851-184">Reconnect clients</span></span>

### <a name="automatically-reconnect"></a><span data-ttu-id="00851-185">自動重新連線</span><span class="sxs-lookup"><span data-stu-id="00851-185">Automatically reconnect</span></span>

<span data-ttu-id="00851-186">的 JavaScript 用戶端 SignalR 可以設定為使用 `withAutomaticReconnect` [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)上的方法自動重新連接。</span><span class="sxs-lookup"><span data-stu-id="00851-186">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="00851-187">它預設不會自動重新連線。</span><span class="sxs-lookup"><span data-stu-id="00851-187">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="00851-188">如果沒有任何參數， `withAutomaticReconnect()` 則會先將用戶端設定為等候0、2、10和30秒，然後再嘗試每次嘗試重新連線，並在嘗試四次失敗之後停止。</span><span class="sxs-lookup"><span data-stu-id="00851-188">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="00851-189">在開始進行任何重新連接嘗試之前， `HubConnection` 會轉換成 `HubConnectionState.Reconnecting` 狀態並引發 `onreconnecting` 回呼，而不是轉換成 `Disconnected` 狀態並觸發其 `onclose` 回呼，例如 `HubConnection` 未設定自動重新連線。</span><span class="sxs-lookup"><span data-stu-id="00851-189">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="00851-190">這可讓使用者警告使用者連接已遺失，並停用 UI 元素。</span><span class="sxs-lookup"><span data-stu-id="00851-190">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="00851-191">如果用戶端在前四次嘗試中成功重新連接，則 `HubConnection` 會轉換回 `Connected` 狀態並引發 `onreconnected` 回呼。</span><span class="sxs-lookup"><span data-stu-id="00851-191">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="00851-192">這會讓您有機會通知使用者已重新建立連接。</span><span class="sxs-lookup"><span data-stu-id="00851-192">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="00851-193">由於連接完全是伺服器的新，因此 `connectionId` 會提供新的 `onreconnected` 回呼。</span><span class="sxs-lookup"><span data-stu-id="00851-193">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="00851-194">`onreconnected` `connectionId` 如果 `HubConnection` 設定為[略過協商](xref:signalr/configuration#configure-client-options)，回呼的參數將會是未定義的。</span><span class="sxs-lookup"><span data-stu-id="00851-194">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="00851-195">`withAutomaticReconnect()` 將不會設定 `HubConnection` 重試初始啟動失敗，因此必須手動處理啟動失敗：</span><span class="sxs-lookup"><span data-stu-id="00851-195">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

```javascript
async function start() {
    try {
        await connection.start();
        console.assert(connection.state === signalR.HubConnectionState.Connected);
        console.log("SignalR Connected.");
    } catch (err) {
        console.assert(connection.state === signalR.HubConnectionState.Disconnected);
        console.log(err);
        setTimeout(() => start(), 5000);
    }
};
```

<span data-ttu-id="00851-196">如果用戶端在前四次嘗試中未成功重新連接，則 `HubConnection` 會轉換為 `Disconnected` 狀態並引發其 [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) 回呼。</span><span class="sxs-lookup"><span data-stu-id="00851-196">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="00851-197">這讓您有機會通知使用者連線已永久遺失，並建議重新整理頁面：</span><span class="sxs-lookup"><span data-stu-id="00851-197">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="00851-198">若要在中斷連線或變更重新連線時間之前設定自訂的重新連接嘗試次數，請 `withAutomaticReconnect` 接受代表延遲的數位陣列，以毫秒為單位來開始每次重新連線嘗試。</span><span class="sxs-lookup"><span data-stu-id="00851-198">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="00851-199">上述範例會將設定 `HubConnection` 為在連接遺失之後立即開始嘗試重新連接。</span><span class="sxs-lookup"><span data-stu-id="00851-199">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="00851-200">這也適用于預設設定。</span><span class="sxs-lookup"><span data-stu-id="00851-200">This is also true for the default configuration.</span></span>

<span data-ttu-id="00851-201">如果第一次重新連線嘗試失敗，第二次重新連線嘗試也會立即開始，而不是等候2秒，就像在預設設定中一樣。</span><span class="sxs-lookup"><span data-stu-id="00851-201">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="00851-202">如果第二次重新連線嘗試失敗，第三次重新連線嘗試將會在10秒內啟動，這同樣就像是預設設定。</span><span class="sxs-lookup"><span data-stu-id="00851-202">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="00851-203">然後，自訂行為會在第三次重新連線嘗試失敗之後停止，而不是在另一個30秒（如同在預設設定中）嘗試多次重新連線嘗試，再次從預設行為分歧。</span><span class="sxs-lookup"><span data-stu-id="00851-203">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="00851-204">如果您想要更充分掌控自動重新連線嘗試的時間和數目，請 `withAutomaticReconnect` 接受執行介面的物件 `IRetryPolicy` ，該介面具有名為的單一方法 `nextRetryDelayInMilliseconds` 。</span><span class="sxs-lookup"><span data-stu-id="00851-204">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="00851-205">`nextRetryDelayInMilliseconds` 採用具有類型的單一引數 `RetryContext` 。</span><span class="sxs-lookup"><span data-stu-id="00851-205">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="00851-206">`RetryContext`有三個屬性： `previousRetryCount` 、 `elapsedMilliseconds` 和 `retryReason` `number` 分別為、 `number` 和 `Error` 。</span><span class="sxs-lookup"><span data-stu-id="00851-206">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="00851-207">在第一次重新連線嘗試之前， `previousRetryCount` 和都 `elapsedMilliseconds` 是零，而且 `retryReason` 將會是導致中斷連接的錯誤。</span><span class="sxs-lookup"><span data-stu-id="00851-207">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="00851-208">每次重試失敗後，將會 `previousRetryCount` 遞增一次， `elapsedMilliseconds` 將會更新以反映到目前為止所花的時間（以毫秒為單位），而且 `retryReason` 會是造成上次重新連線嘗試失敗的錯誤。</span><span class="sxs-lookup"><span data-stu-id="00851-208">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="00851-209">`nextRetryDelayInMilliseconds` 必須傳回一個數位，代表下一次重新連接嘗試之前要等候的毫秒數，或 `null` 是否 `HubConnection` 應該停止重新連接。</span><span class="sxs-lookup"><span data-stu-id="00851-209">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect({
        nextRetryDelayInMilliseconds: retryContext => {
            if (retryContext.elapsedMilliseconds < 60000) {
                // If we've been reconnecting for less than 60 seconds so far,
                // wait between 0 and 10 seconds before the next reconnect attempt.
                return Math.random() * 10000;
            } else {
                // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
                return null;
            }
        }
    })
    .build();
```

<span data-ttu-id="00851-210">或者，您也可以撰寫程式碼，以手動方式重新連接用戶端，如 [手動重新](#manually-reconnect)連線中所示範。</span><span class="sxs-lookup"><span data-stu-id="00851-210">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

### <a name="manually-reconnect"></a><span data-ttu-id="00851-211">手動重新連線</span><span class="sxs-lookup"><span data-stu-id="00851-211">Manually reconnect</span></span>

<span data-ttu-id="00851-212">下列程式碼示範一般手動重新連接方法：</span><span class="sxs-lookup"><span data-stu-id="00851-212">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="00851-213">函式 (在此案例中， `start` 會建立函式) 以開始連接。</span><span class="sxs-lookup"><span data-stu-id="00851-213">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="00851-214">`start`在連接的事件處理常式中呼叫函式 `onclose` 。</span><span class="sxs-lookup"><span data-stu-id="00851-214">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?range=30-40)]

<span data-ttu-id="00851-215">實際執行會使用指數輪詢，或在放棄之前重試指定的次數。</span><span class="sxs-lookup"><span data-stu-id="00851-215">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="00851-216">其他資源</span><span class="sxs-lookup"><span data-stu-id="00851-216">Additional resources</span></span>

* [<span data-ttu-id="00851-217">JavaScript API 參考</span><span class="sxs-lookup"><span data-stu-id="00851-217">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest&preserve-view=true )
* [<span data-ttu-id="00851-218">JavaScript 教學課程</span><span class="sxs-lookup"><span data-stu-id="00851-218">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="00851-219">WebPack 和 TypeScript 教學課程</span><span class="sxs-lookup"><span data-stu-id="00851-219">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="00851-220">中樞</span><span class="sxs-lookup"><span data-stu-id="00851-220">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="00851-221">.NET 用戶端</span><span class="sxs-lookup"><span data-stu-id="00851-221">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="00851-222">發佈至 Azure</span><span class="sxs-lookup"><span data-stu-id="00851-222">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="00851-223"> (CORS) 的跨原始來源要求 </span><span class="sxs-lookup"><span data-stu-id="00851-223">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="00851-224">Azure SignalR 服務無伺服器檔</span><span class="sxs-lookup"><span data-stu-id="00851-224">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="00851-225">作者：[Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="00851-225">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="00851-226">ASP.NET Core SignalR JavaScript 用戶端程式庫可讓開發人員呼叫伺服器端中樞程式碼。</span><span class="sxs-lookup"><span data-stu-id="00851-226">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="00851-227">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="00851-227">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-client-package"></a><span data-ttu-id="00851-228">安裝 SignalR 用戶端套件</span><span class="sxs-lookup"><span data-stu-id="00851-228">Install the SignalR client package</span></span>

<span data-ttu-id="00851-229">SignalRJavaScript 用戶端程式庫會以[npm](https://www.npmjs.com/)套件的形式傳遞。</span><span class="sxs-lookup"><span data-stu-id="00851-229">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="00851-230">下列各節概述安裝用戶端程式庫的不同方式。</span><span class="sxs-lookup"><span data-stu-id="00851-230">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="00851-231">使用 npm 安裝</span><span class="sxs-lookup"><span data-stu-id="00851-231">Install with npm</span></span>

<span data-ttu-id="00851-232">如果使用 Visual Studio，請在根資料夾中從 **封裝管理員主控台** 執行下列命令。</span><span class="sxs-lookup"><span data-stu-id="00851-232">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="00851-233">針對 Visual Studio Code，請從 **整合式終端**機執行下列命令。</span><span class="sxs-lookup"><span data-stu-id="00851-233">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="00851-234">npm 會將套件內容安裝\*node_modules \\ @aspnet\signalr\dist\browser \*資料夾。</span><span class="sxs-lookup"><span data-stu-id="00851-234">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="00851-235">在*wwwroot \\ lib*資料夾下建立名為*signalr*的新資料夾。</span><span class="sxs-lookup"><span data-stu-id="00851-235">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="00851-236">將 *signalr.js* 檔案複製到 *wwwroot\lib\signalr* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="00851-236">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

<span data-ttu-id="00851-237">參考 SignalR 元素中的 JavaScript 用戶端 `<script>` 。</span><span class="sxs-lookup"><span data-stu-id="00851-237">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="00851-238">例如：</span><span class="sxs-lookup"><span data-stu-id="00851-238">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="00851-239">使用 (CDN) 的內容傳遞網路</span><span class="sxs-lookup"><span data-stu-id="00851-239">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="00851-240">若要在沒有 npm 必要條件的情況下使用用戶端程式庫，請參考用戶端程式庫的 CDN 託管複本。</span><span class="sxs-lookup"><span data-stu-id="00851-240">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="00851-241">例如：</span><span class="sxs-lookup"><span data-stu-id="00851-241">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="00851-242">用戶端程式庫可在下列 Cdn 上取得：</span><span class="sxs-lookup"><span data-stu-id="00851-242">The client library is available on the following CDNs:</span></span>

* [<span data-ttu-id="00851-243">cdnjs</span><span class="sxs-lookup"><span data-stu-id="00851-243">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="00851-244">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="00851-244">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="00851-245">unpkg</span><span class="sxs-lookup"><span data-stu-id="00851-245">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a><span data-ttu-id="00851-246">使用 LibMan 安裝</span><span class="sxs-lookup"><span data-stu-id="00851-246">Install with LibMan</span></span>

<span data-ttu-id="00851-247">[LibMan](xref:client-side/libman/index) 可以用來從 CDN 裝載的用戶端程式庫安裝特定的用戶端程式庫檔案。</span><span class="sxs-lookup"><span data-stu-id="00851-247">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="00851-248">例如，只將縮減 JavaScript 檔案新增至專案。</span><span class="sxs-lookup"><span data-stu-id="00851-248">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="00851-249">如需該方法的詳細資訊，請參閱 [新增 SignalR 用戶端程式庫](xref:tutorials/signalr#add-the-signalr-client-library)。</span><span class="sxs-lookup"><span data-stu-id="00851-249">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="00851-250">連接至中樞</span><span class="sxs-lookup"><span data-stu-id="00851-250">Connect to a hub</span></span>

<span data-ttu-id="00851-251">下列程式碼會建立並啟動連接。</span><span class="sxs-lookup"><span data-stu-id="00851-251">The following code creates and starts a connection.</span></span> <span data-ttu-id="00851-252">中樞的名稱不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="00851-252">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=9-13,28-51)]

### <a name="cross-origin-connections"></a><span data-ttu-id="00851-253">跨原始連接</span><span class="sxs-lookup"><span data-stu-id="00851-253">Cross-origin connections</span></span>

<span data-ttu-id="00851-254">一般而言，瀏覽器會從與要求的頁面相同的網域載入連接。</span><span class="sxs-lookup"><span data-stu-id="00851-254">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="00851-255">不過，在某些情況下需要連接到另一個網域。</span><span class="sxs-lookup"><span data-stu-id="00851-255">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="00851-256">為了防止惡意網站從另一個網站讀取敏感性資料，預設會停用 [跨原始來源的連接](xref:security/cors) 。</span><span class="sxs-lookup"><span data-stu-id="00851-256">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="00851-257">若要允許跨原始來源的要求，請在類別中加以啟用 `Startup` 。</span><span class="sxs-lookup"><span data-stu-id="00851-257">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/samples/2.x/SignalRChat/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="00851-258">從用戶端呼叫中樞方法</span><span class="sxs-lookup"><span data-stu-id="00851-258">Call hub methods from client</span></span>

<span data-ttu-id="00851-259">JavaScript 用戶端會透過[HubConnection](/javascript/api/%40aspnet/signalr/hubconnection)的[invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke)方法，呼叫中樞上的公用方法。</span><span class="sxs-lookup"><span data-stu-id="00851-259">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="00851-260">`invoke`方法會接受兩個引數：</span><span class="sxs-lookup"><span data-stu-id="00851-260">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="00851-261">中樞方法的名稱。</span><span class="sxs-lookup"><span data-stu-id="00851-261">The name of the hub method.</span></span> <span data-ttu-id="00851-262">在下列範例中，中樞上的方法名稱為 `SendMessage` 。</span><span class="sxs-lookup"><span data-stu-id="00851-262">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="00851-263">中樞方法中定義的任何引數。</span><span class="sxs-lookup"><span data-stu-id="00851-263">Any arguments defined in the hub method.</span></span> <span data-ttu-id="00851-264">在下列範例中，引數名稱為 `message` 。</span><span class="sxs-lookup"><span data-stu-id="00851-264">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="00851-265">範例程式碼會使用箭號函式語法，此語法在所有主要瀏覽器的目前版本中受到支援，但 Internet Explorer 除外。</span><span class="sxs-lookup"><span data-stu-id="00851-265">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="00851-266">只有 SignalR 在 *預設* 模式下使用 Azure 服務時，才支援從用戶端呼叫中樞方法。</span><span class="sxs-lookup"><span data-stu-id="00851-266">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="00851-267">如需詳細資訊，請參閱 [ (azure Signalr GitHub 存放庫) ](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose)的常見問題。</span><span class="sxs-lookup"><span data-stu-id="00851-267">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

<span data-ttu-id="00851-268">方法會傳回 `invoke` JavaScript [承諾](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)。</span><span class="sxs-lookup"><span data-stu-id="00851-268">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="00851-269">`Promise`當伺服器上的方法傳回時，會使用傳回值來解析 (是否有任何) 。</span><span class="sxs-lookup"><span data-stu-id="00851-269">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="00851-270">如果伺服器上的方法擲回錯誤，則 `Promise` 會被拒絕，並顯示錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="00851-270">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="00851-271">您 `then` 可以使用本身的和 `catch` 方法 `Promise` ， (或語法) 來處理這些情況 `await` 。</span><span class="sxs-lookup"><span data-stu-id="00851-271">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="00851-272">方法會傳回 `send` JavaScript `Promise` 。</span><span class="sxs-lookup"><span data-stu-id="00851-272">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="00851-273">`Promise`當訊息傳送到伺服器時，會解析。</span><span class="sxs-lookup"><span data-stu-id="00851-273">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="00851-274">如果傳送訊息時發生錯誤， `Promise` 就會拒絕，並顯示錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="00851-274">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="00851-275">您 `then` 可以使用本身的和 `catch` 方法 `Promise` ， (或語法) 來處理這些情況 `await` 。</span><span class="sxs-lookup"><span data-stu-id="00851-275">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="00851-276">使用 `send` 並不會等到伺服器收到訊息為止。</span><span class="sxs-lookup"><span data-stu-id="00851-276">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="00851-277">因此，不可能從伺服器傳回資料或錯誤。</span><span class="sxs-lookup"><span data-stu-id="00851-277">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="00851-278">從中樞呼叫用戶端方法</span><span class="sxs-lookup"><span data-stu-id="00851-278">Call client methods from hub</span></span>

<span data-ttu-id="00851-279">若要從中樞接收訊息，請使用的 [on](/javascript/api/%40aspnet/signalr/hubconnection#on) 方法定義方法 `HubConnection` 。</span><span class="sxs-lookup"><span data-stu-id="00851-279">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="00851-280">JavaScript 用戶端方法的名稱。</span><span class="sxs-lookup"><span data-stu-id="00851-280">The name of the JavaScript client method.</span></span> <span data-ttu-id="00851-281">在下列範例中，方法名稱為 `ReceiveMessage` 。</span><span class="sxs-lookup"><span data-stu-id="00851-281">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="00851-282">中樞傳遞給方法的引數。</span><span class="sxs-lookup"><span data-stu-id="00851-282">Arguments the hub passes to the method.</span></span> <span data-ttu-id="00851-283">在下列範例中，引數值為 `message` 。</span><span class="sxs-lookup"><span data-stu-id="00851-283">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="00851-284">中的上述程式碼 `connection.on` 會在伺服器端程式碼使用方法呼叫它時執行 <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> 。</span><span class="sxs-lookup"><span data-stu-id="00851-284">The preceding code in `connection.on` runs when server-side code calls it using the <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> method.</span></span>

[!code-csharp[Call client-side](javascript-client/samples/2.x/SignalRChat/hubs/chathub.cs?range=8-11)]

<span data-ttu-id="00851-285">SignalR 藉由比對和中定義的方法名稱和引數，判斷要呼叫的用戶端方法 `SendAsync` `connection.on` 。</span><span class="sxs-lookup"><span data-stu-id="00851-285">SignalR determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="00851-286">最佳做法是在之後呼叫 [start](/javascript/api/%40aspnet/signalr/hubconnection#start) 方法 `HubConnection` `on` 。</span><span class="sxs-lookup"><span data-stu-id="00851-286">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="00851-287">這麼做可確保您的處理常式會在收到任何訊息之前註冊。</span><span class="sxs-lookup"><span data-stu-id="00851-287">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="00851-288">錯誤處理和記錄</span><span class="sxs-lookup"><span data-stu-id="00851-288">Error handling and logging</span></span>

<span data-ttu-id="00851-289">將 `catch` 方法連結至方法的結尾 `start` ，以處理用戶端錯誤。</span><span class="sxs-lookup"><span data-stu-id="00851-289">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="00851-290">用 `console.error` 來將錯誤輸出至瀏覽器的主控台。</span><span class="sxs-lookup"><span data-stu-id="00851-290">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=50)]

<span data-ttu-id="00851-291">設定用戶端記錄檔追蹤，方法是在建立連接時，傳遞記錄器和事件種類來記錄。</span><span class="sxs-lookup"><span data-stu-id="00851-291">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="00851-292">訊息會以指定的記錄層級和更新版本記錄。</span><span class="sxs-lookup"><span data-stu-id="00851-292">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="00851-293">可用的記錄層級如下所示：</span><span class="sxs-lookup"><span data-stu-id="00851-293">Available log levels are as follows:</span></span>

* <span data-ttu-id="00851-294">`signalR.LogLevel.Error`：錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="00851-294">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="00851-295">`Error`只記錄訊息。</span><span class="sxs-lookup"><span data-stu-id="00851-295">Logs `Error` messages only.</span></span>
* <span data-ttu-id="00851-296">`signalR.LogLevel.Warning`：可能發生錯誤的警告訊息。</span><span class="sxs-lookup"><span data-stu-id="00851-296">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="00851-297">記錄檔 `Warning` 和 `Error` 訊息。</span><span class="sxs-lookup"><span data-stu-id="00851-297">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="00851-298">`signalR.LogLevel.Information`：沒有錯誤的狀態訊息。</span><span class="sxs-lookup"><span data-stu-id="00851-298">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="00851-299">記錄 `Information` 、 `Warning` 和 `Error` 訊息。</span><span class="sxs-lookup"><span data-stu-id="00851-299">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="00851-300">`signalR.LogLevel.Trace`：追蹤訊息。</span><span class="sxs-lookup"><span data-stu-id="00851-300">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="00851-301">記錄所有內容，包括中樞與用戶端之間傳輸的資料。</span><span class="sxs-lookup"><span data-stu-id="00851-301">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="00851-302">在[HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)上使用[configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging)方法來設定記錄層級。</span><span class="sxs-lookup"><span data-stu-id="00851-302">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="00851-303">訊息會記錄到瀏覽器主控台。</span><span class="sxs-lookup"><span data-stu-id="00851-303">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="00851-304">重新連線用戶端</span><span class="sxs-lookup"><span data-stu-id="00851-304">Reconnect clients</span></span>

### <a name="manually-reconnect"></a><span data-ttu-id="00851-305">手動重新連線</span><span class="sxs-lookup"><span data-stu-id="00851-305">Manually reconnect</span></span>

> [!WARNING]
> <span data-ttu-id="00851-306">在3.0 之前，的 JavaScript 用戶端 SignalR 不會自動重新連線。</span><span class="sxs-lookup"><span data-stu-id="00851-306">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="00851-307">您必須撰寫會以手動方式重新連接用戶端的程式碼。</span><span class="sxs-lookup"><span data-stu-id="00851-307">You must write code that will reconnect your client manually.</span></span>

<span data-ttu-id="00851-308">下列程式碼示範一般手動重新連接方法：</span><span class="sxs-lookup"><span data-stu-id="00851-308">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="00851-309">函式 (在此案例中， `start` 會建立函式) 以開始連接。</span><span class="sxs-lookup"><span data-stu-id="00851-309">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="00851-310">`start`在連接的事件處理常式中呼叫函式 `onclose` 。</span><span class="sxs-lookup"><span data-stu-id="00851-310">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="00851-311">實際執行會使用指數輪詢，或在放棄之前重試指定的次數。</span><span class="sxs-lookup"><span data-stu-id="00851-311">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="00851-312">其他資源</span><span class="sxs-lookup"><span data-stu-id="00851-312">Additional resources</span></span>

* [<span data-ttu-id="00851-313">JavaScript API 參考</span><span class="sxs-lookup"><span data-stu-id="00851-313">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="00851-314">JavaScript 教學課程</span><span class="sxs-lookup"><span data-stu-id="00851-314">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="00851-315">WebPack 和 TypeScript 教學課程</span><span class="sxs-lookup"><span data-stu-id="00851-315">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="00851-316">中樞</span><span class="sxs-lookup"><span data-stu-id="00851-316">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="00851-317">.NET 用戶端</span><span class="sxs-lookup"><span data-stu-id="00851-317">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="00851-318">發佈至 Azure</span><span class="sxs-lookup"><span data-stu-id="00851-318">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="00851-319"> (CORS) 的跨原始來源要求 </span><span class="sxs-lookup"><span data-stu-id="00851-319">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="00851-320">Azure SignalR 服務無伺服器檔</span><span class="sxs-lookup"><span data-stu-id="00851-320">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)

::: moniker-end
