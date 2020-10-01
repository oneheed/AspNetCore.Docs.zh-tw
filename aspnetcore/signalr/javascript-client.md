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
ms.openlocfilehash: 6fc586d144547585ef75d653bf54193def5c8b7f
ms.sourcegitcommit: d1a897ebd89daa05170ac448e4831d327f6b21a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2020
ms.locfileid: "91606687"
---
# <a name="aspnet-core-no-locsignalr-javascript-client"></a><span data-ttu-id="3bc00-103">ASP.NET Core SignalR JavaScript 用戶端</span><span class="sxs-lookup"><span data-stu-id="3bc00-103">ASP.NET Core SignalR JavaScript client</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3bc00-104">作者：[Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="3bc00-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="3bc00-105">ASP.NET Core SignalR JavaScript 用戶端程式庫可讓開發人員呼叫伺服器端中樞程式碼。</span><span class="sxs-lookup"><span data-stu-id="3bc00-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="3bc00-106">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="3bc00-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-client-package"></a><span data-ttu-id="3bc00-107">安裝 SignalR 用戶端套件</span><span class="sxs-lookup"><span data-stu-id="3bc00-107">Install the SignalR client package</span></span>

<span data-ttu-id="3bc00-108">SignalRJavaScript 用戶端程式庫會以[npm](https://www.npmjs.com/)套件的形式傳遞。</span><span class="sxs-lookup"><span data-stu-id="3bc00-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="3bc00-109">下列各節概述安裝用戶端程式庫的不同方式。</span><span class="sxs-lookup"><span data-stu-id="3bc00-109">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="3bc00-110">使用 npm 安裝</span><span class="sxs-lookup"><span data-stu-id="3bc00-110">Install with npm</span></span>

<span data-ttu-id="3bc00-111">針對 Visual Studio，請在根資料夾中從 **封裝管理員主控台** 執行下列命令。</span><span class="sxs-lookup"><span data-stu-id="3bc00-111">For Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="3bc00-112">針對 Visual Studio Code，請從 **整合式終端**機執行下列命令。</span><span class="sxs-lookup"><span data-stu-id="3bc00-112">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="3bc00-113">npm 會將套件內容安裝\*node_modules \\ @microsoft\signalr\dist\browser \*資料夾。</span><span class="sxs-lookup"><span data-stu-id="3bc00-113">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="3bc00-114">在*wwwroot \\ lib*資料夾下建立名為*signalr*的新資料夾。</span><span class="sxs-lookup"><span data-stu-id="3bc00-114">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="3bc00-115">將 *signalr.js* 檔案複製到 *wwwroot\lib\signalr* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="3bc00-115">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

<span data-ttu-id="3bc00-116">參考 SignalR 元素中的 JavaScript 用戶端 `<script>` 。</span><span class="sxs-lookup"><span data-stu-id="3bc00-116">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="3bc00-117">例如：</span><span class="sxs-lookup"><span data-stu-id="3bc00-117">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="3bc00-118">使用 (CDN) 的內容傳遞網路</span><span class="sxs-lookup"><span data-stu-id="3bc00-118">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="3bc00-119">若要在沒有 npm 必要條件的情況下使用用戶端程式庫，請參考用戶端程式庫的 CDN 託管複本。</span><span class="sxs-lookup"><span data-stu-id="3bc00-119">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="3bc00-120">例如：</span><span class="sxs-lookup"><span data-stu-id="3bc00-120">For example:</span></span>

[!code-html[](javascript-client/samples/3.x/SignalRChat/Pages/Index.cshtml?name=snippet_CDN)]

<span data-ttu-id="3bc00-121">用戶端程式庫可在下列 Cdn 上取得：</span><span class="sxs-lookup"><span data-stu-id="3bc00-121">The client library is available on the following CDNs:</span></span>

* [<span data-ttu-id="3bc00-122">cdnjs</span><span class="sxs-lookup"><span data-stu-id="3bc00-122">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="3bc00-123">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="3bc00-123">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="3bc00-124">unpkg</span><span class="sxs-lookup"><span data-stu-id="3bc00-124">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a><span data-ttu-id="3bc00-125">使用 LibMan 安裝</span><span class="sxs-lookup"><span data-stu-id="3bc00-125">Install with LibMan</span></span>

<span data-ttu-id="3bc00-126">[LibMan](xref:client-side/libman/index) 可以用來從 CDN 裝載的用戶端程式庫安裝特定的用戶端程式庫檔案。</span><span class="sxs-lookup"><span data-stu-id="3bc00-126">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="3bc00-127">例如，只將縮減 JavaScript 檔案新增至專案。</span><span class="sxs-lookup"><span data-stu-id="3bc00-127">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="3bc00-128">如需該方法的詳細資訊，請參閱 [新增 SignalR 用戶端程式庫](xref:tutorials/signalr#add-the-signalr-client-library)。</span><span class="sxs-lookup"><span data-stu-id="3bc00-128">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="3bc00-129">連接至中樞</span><span class="sxs-lookup"><span data-stu-id="3bc00-129">Connect to a hub</span></span>

<span data-ttu-id="3bc00-130">下列程式碼會建立並啟動連接。</span><span class="sxs-lookup"><span data-stu-id="3bc00-130">The following code creates and starts a connection.</span></span> <span data-ttu-id="3bc00-131">中樞的名稱不區分大小寫：</span><span class="sxs-lookup"><span data-stu-id="3bc00-131">The hub's name is case insensitive:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?range=3-6,29-43)]

### <a name="cross-origin-connections"></a><span data-ttu-id="3bc00-132">跨原始連接</span><span class="sxs-lookup"><span data-stu-id="3bc00-132">Cross-origin connections</span></span>

<span data-ttu-id="3bc00-133">一般而言，瀏覽器會從與要求的頁面相同的網域載入連接。</span><span class="sxs-lookup"><span data-stu-id="3bc00-133">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="3bc00-134">不過，在某些情況下需要連接到另一個網域。</span><span class="sxs-lookup"><span data-stu-id="3bc00-134">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="3bc00-135">為了防止惡意網站從另一個網站讀取敏感性資料，預設會停用 [跨原始來源的連接](xref:security/cors) 。</span><span class="sxs-lookup"><span data-stu-id="3bc00-135">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="3bc00-136">若要允許跨原始來源的要求，請在類別中啟用它 `Startup` ：</span><span class="sxs-lookup"><span data-stu-id="3bc00-136">To allow a cross-origin request, enable it in the `Startup` class:</span></span>

[!code-csharp[](javascript-client/samples/3.x/SignalRChat/Startup.cs?highlight=16-23,40)]

## <a name="call-hub-methods-from-the-client"></a><span data-ttu-id="3bc00-137">從用戶端呼叫中樞方法</span><span class="sxs-lookup"><span data-stu-id="3bc00-137">Call hub methods from the client</span></span>

<span data-ttu-id="3bc00-138">JavaScript 用戶端會透過[HubConnection](/javascript/api/%40microsoft/signalr/hubconnection)的[invoke](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---)方法，呼叫中樞上的公用方法。</span><span class="sxs-lookup"><span data-stu-id="3bc00-138">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---) method of the [HubConnection](/javascript/api/%40microsoft/signalr/hubconnection).</span></span> <span data-ttu-id="3bc00-139">此 `invoke` 方法會接受：</span><span class="sxs-lookup"><span data-stu-id="3bc00-139">The `invoke` method accepts:</span></span>

* <span data-ttu-id="3bc00-140">中樞方法的名稱。</span><span class="sxs-lookup"><span data-stu-id="3bc00-140">The name of the hub method.</span></span>
* <span data-ttu-id="3bc00-141">中樞方法中定義的任何引數。</span><span class="sxs-lookup"><span data-stu-id="3bc00-141">Any arguments defined in the hub method.</span></span>

<span data-ttu-id="3bc00-142">在下列範例中，中樞上的方法名稱為 `SendMessage` 。</span><span class="sxs-lookup"><span data-stu-id="3bc00-142">In the following example, the method name on the hub is `SendMessage`.</span></span> <span data-ttu-id="3bc00-143">傳遞給的第二個和第三個引數會 `invoke` 對應至中樞方法的 `user` 和 `message` 引數：</span><span class="sxs-lookup"><span data-stu-id="3bc00-143">The second and third arguments passed to `invoke` map to the hub method's `user` and `message` arguments:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Invoke&highlight=2)]

> [!NOTE]
> <span data-ttu-id="3bc00-144">只有 SignalR 在 *預設* 模式下使用 Azure 服務時，才支援從用戶端呼叫中樞方法。</span><span class="sxs-lookup"><span data-stu-id="3bc00-144">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="3bc00-145">如需詳細資訊，請參閱 [ (azure Signalr GitHub 存放庫) ](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose)的常見問題。</span><span class="sxs-lookup"><span data-stu-id="3bc00-145">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

<span data-ttu-id="3bc00-146">方法會傳回 `invoke` JavaScript [承諾](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)。</span><span class="sxs-lookup"><span data-stu-id="3bc00-146">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="3bc00-147">`Promise`當伺服器上的方法傳回時，會使用傳回值來解析 (是否有任何) 。</span><span class="sxs-lookup"><span data-stu-id="3bc00-147">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="3bc00-148">如果伺服器上的方法擲回錯誤，則 `Promise` 會被拒絕，並顯示錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="3bc00-148">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="3bc00-149">使用 `async` 和 `await` 或的 `Promise` `then` 和 `catch` 方法來處理這些情況。</span><span class="sxs-lookup"><span data-stu-id="3bc00-149">Use `async` and `await` or the `Promise`'s `then` and `catch` methods to handle these cases.</span></span>

<span data-ttu-id="3bc00-150">JavaScript 用戶端也可以透過的 [傳送](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) 方法，呼叫中樞上的公用方法 `HubConnection` 。</span><span class="sxs-lookup"><span data-stu-id="3bc00-150">JavaScript clients can also call public methods on hubs via the the [send](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) method of the `HubConnection`.</span></span> <span data-ttu-id="3bc00-151">與方法不同的是 `invoke` ， `send` 方法不會等候伺服器的回應。</span><span class="sxs-lookup"><span data-stu-id="3bc00-151">Unlike the `invoke` method, the `send` method doesn't wait for a response from the server.</span></span> <span data-ttu-id="3bc00-152">方法會傳回 `send` JavaScript `Promise` 。</span><span class="sxs-lookup"><span data-stu-id="3bc00-152">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="3bc00-153">`Promise`當訊息傳送到伺服器時，會解析。</span><span class="sxs-lookup"><span data-stu-id="3bc00-153">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="3bc00-154">如果傳送訊息時發生錯誤， `Promise` 就會拒絕，並顯示錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="3bc00-154">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="3bc00-155">使用 `async` 和 `await` 或的 `Promise` `then` 和 `catch` 方法來處理這些情況。</span><span class="sxs-lookup"><span data-stu-id="3bc00-155">Use `async` and `await` or the `Promise`'s `then` and `catch` methods to handle these cases.</span></span>

> [!NOTE]
> <span data-ttu-id="3bc00-156">使用 `send` 並不會等到伺服器收到訊息為止。</span><span class="sxs-lookup"><span data-stu-id="3bc00-156">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="3bc00-157">因此，不可能從伺服器傳回資料或錯誤。</span><span class="sxs-lookup"><span data-stu-id="3bc00-157">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-the-hub"></a><span data-ttu-id="3bc00-158">從中樞呼叫用戶端方法</span><span class="sxs-lookup"><span data-stu-id="3bc00-158">Call client methods from the hub</span></span>

<span data-ttu-id="3bc00-159">若要從中樞接收訊息，請使用的 [on](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) 方法定義方法 `HubConnection` 。</span><span class="sxs-lookup"><span data-stu-id="3bc00-159">To receive messages from the hub, define a method using the [on](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) method of the `HubConnection`.</span></span>

* <span data-ttu-id="3bc00-160">JavaScript 用戶端方法的名稱。</span><span class="sxs-lookup"><span data-stu-id="3bc00-160">The name of the JavaScript client method.</span></span>
* <span data-ttu-id="3bc00-161">中樞傳遞給方法的引數。</span><span class="sxs-lookup"><span data-stu-id="3bc00-161">Arguments the hub passes to the method.</span></span>

<span data-ttu-id="3bc00-162">在下列範例中，方法名稱為 `ReceiveMessage` 。</span><span class="sxs-lookup"><span data-stu-id="3bc00-162">In the following example, the method name is `ReceiveMessage`.</span></span> <span data-ttu-id="3bc00-163">引數名稱為 `user` 和 `message` ：</span><span class="sxs-lookup"><span data-stu-id="3bc00-163">The argument names are `user` and `message`:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_ReceiveMessage)]

<span data-ttu-id="3bc00-164">中的上述程式碼 `connection.on` 會在伺服器端程式碼使用方法呼叫它時執行 <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> ：</span><span class="sxs-lookup"><span data-stu-id="3bc00-164">The preceding code in `connection.on` runs when server-side code calls it using the <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> method:</span></span>

[!code-csharp[Call client-side](javascript-client/samples/3.x/SignalRChat/Hubs/ChatHub.cs?name=snippet_SendMessage)]

<span data-ttu-id="3bc00-165">SignalR 藉由比對和中定義的方法名稱和引數，判斷要呼叫的用戶端方法 `SendAsync` `connection.on` 。</span><span class="sxs-lookup"><span data-stu-id="3bc00-165">SignalR determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="3bc00-166">最佳做法是在之後呼叫 [start](/javascript/api/%40aspnet/signalr/hubconnection#start) 方法 `HubConnection` `on` 。</span><span class="sxs-lookup"><span data-stu-id="3bc00-166">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="3bc00-167">這麼做可確保您的處理常式會在收到任何訊息之前註冊。</span><span class="sxs-lookup"><span data-stu-id="3bc00-167">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="3bc00-168">錯誤處理和記錄</span><span class="sxs-lookup"><span data-stu-id="3bc00-168">Error handling and logging</span></span>

<span data-ttu-id="3bc00-169">使用 `try` 和 `catch` 搭配 `async` 和 `await` 或的 `Promise` `catch` 方法來處理用戶端錯誤。</span><span class="sxs-lookup"><span data-stu-id="3bc00-169">Use `try` and `catch` with `async` and `await` or the `Promise`'s `catch` method to handle client-side errors.</span></span> <span data-ttu-id="3bc00-170">使用 `console.error` 將錯誤輸出至瀏覽器的主控台：</span><span class="sxs-lookup"><span data-stu-id="3bc00-170">Use `console.error` to output errors to the browser's console:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Invoke&highlight=1,3-5)]

<span data-ttu-id="3bc00-171">設定用戶端記錄檔追蹤，方法是在建立連接時，傳遞記錄器和事件種類來記錄。</span><span class="sxs-lookup"><span data-stu-id="3bc00-171">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="3bc00-172">訊息會以指定的記錄層級和更新版本記錄。</span><span class="sxs-lookup"><span data-stu-id="3bc00-172">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="3bc00-173">可用的記錄層級如下所示：</span><span class="sxs-lookup"><span data-stu-id="3bc00-173">Available log levels are as follows:</span></span>

* <span data-ttu-id="3bc00-174">`signalR.LogLevel.Error`：錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="3bc00-174">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="3bc00-175">`Error`只記錄訊息。</span><span class="sxs-lookup"><span data-stu-id="3bc00-175">Logs `Error` messages only.</span></span>
* <span data-ttu-id="3bc00-176">`signalR.LogLevel.Warning`：可能發生錯誤的警告訊息。</span><span class="sxs-lookup"><span data-stu-id="3bc00-176">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="3bc00-177">記錄檔 `Warning` 和 `Error` 訊息。</span><span class="sxs-lookup"><span data-stu-id="3bc00-177">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="3bc00-178">`signalR.LogLevel.Information`：沒有錯誤的狀態訊息。</span><span class="sxs-lookup"><span data-stu-id="3bc00-178">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="3bc00-179">記錄 `Information` 、 `Warning` 和 `Error` 訊息。</span><span class="sxs-lookup"><span data-stu-id="3bc00-179">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="3bc00-180">`signalR.LogLevel.Trace`：追蹤訊息。</span><span class="sxs-lookup"><span data-stu-id="3bc00-180">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="3bc00-181">記錄所有內容，包括中樞與用戶端之間傳輸的資料。</span><span class="sxs-lookup"><span data-stu-id="3bc00-181">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="3bc00-182">在[HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)上使用[configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging)方法來設定記錄層級。</span><span class="sxs-lookup"><span data-stu-id="3bc00-182">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="3bc00-183">訊息會記錄到瀏覽器主控台：</span><span class="sxs-lookup"><span data-stu-id="3bc00-183">Messages are logged to the browser console:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Connection&highlight=3)]

## <a name="reconnect-clients"></a><span data-ttu-id="3bc00-184">重新連線用戶端</span><span class="sxs-lookup"><span data-stu-id="3bc00-184">Reconnect clients</span></span>

### <a name="automatically-reconnect"></a><span data-ttu-id="3bc00-185">自動重新連線</span><span class="sxs-lookup"><span data-stu-id="3bc00-185">Automatically reconnect</span></span>

<span data-ttu-id="3bc00-186">的 JavaScript 用戶端 SignalR 可以設定為使用 `withAutomaticReconnect` [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)上的方法自動重新連接。</span><span class="sxs-lookup"><span data-stu-id="3bc00-186">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="3bc00-187">它預設不會自動重新連線。</span><span class="sxs-lookup"><span data-stu-id="3bc00-187">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="3bc00-188">如果沒有任何參數， `withAutomaticReconnect()` 則會先將用戶端設定為等候0、2、10和30秒，然後再嘗試每次嘗試重新連線，並在嘗試四次失敗之後停止。</span><span class="sxs-lookup"><span data-stu-id="3bc00-188">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="3bc00-189">在開始進行任何重新連接嘗試之前， `HubConnection` 會轉換成 `HubConnectionState.Reconnecting` 狀態並引發 `onreconnecting` 回呼，而不是轉換成 `Disconnected` 狀態並觸發其 `onclose` 回呼，例如 `HubConnection` 未設定自動重新連線。</span><span class="sxs-lookup"><span data-stu-id="3bc00-189">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="3bc00-190">這可讓使用者警告使用者連接已遺失，並停用 UI 元素。</span><span class="sxs-lookup"><span data-stu-id="3bc00-190">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="3bc00-191">如果用戶端在前四次嘗試中成功重新連接，則 `HubConnection` 會轉換回 `Connected` 狀態並引發 `onreconnected` 回呼。</span><span class="sxs-lookup"><span data-stu-id="3bc00-191">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="3bc00-192">這會讓您有機會通知使用者已重新建立連接。</span><span class="sxs-lookup"><span data-stu-id="3bc00-192">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="3bc00-193">由於連接完全是伺服器的新，因此 `connectionId` 會提供新的 `onreconnected` 回呼。</span><span class="sxs-lookup"><span data-stu-id="3bc00-193">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="3bc00-194">`onreconnected` `connectionId` 如果 `HubConnection` 設定為[略過協商](xref:signalr/configuration#configure-client-options)，回呼的參數將會是未定義的。</span><span class="sxs-lookup"><span data-stu-id="3bc00-194">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="3bc00-195">`withAutomaticReconnect()` 將不會設定 `HubConnection` 重試初始啟動失敗，因此必須手動處理啟動失敗：</span><span class="sxs-lookup"><span data-stu-id="3bc00-195">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

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

<span data-ttu-id="3bc00-196">如果用戶端在前四次嘗試中未成功重新連接，則 `HubConnection` 會轉換為 `Disconnected` 狀態並引發其 [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) 回呼。</span><span class="sxs-lookup"><span data-stu-id="3bc00-196">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="3bc00-197">這讓您有機會通知使用者連線已永久遺失，並建議重新整理頁面：</span><span class="sxs-lookup"><span data-stu-id="3bc00-197">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="3bc00-198">若要在中斷連線或變更重新連線時間之前設定自訂的重新連接嘗試次數，請 `withAutomaticReconnect` 接受代表延遲的數位陣列，以毫秒為單位來開始每次重新連線嘗試。</span><span class="sxs-lookup"><span data-stu-id="3bc00-198">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="3bc00-199">上述範例會將設定 `HubConnection` 為在連接遺失之後立即開始嘗試重新連接。</span><span class="sxs-lookup"><span data-stu-id="3bc00-199">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="3bc00-200">這也適用于預設設定。</span><span class="sxs-lookup"><span data-stu-id="3bc00-200">This is also true for the default configuration.</span></span>

<span data-ttu-id="3bc00-201">如果第一次重新連線嘗試失敗，第二次重新連線嘗試也會立即開始，而不是等候2秒，就像在預設設定中一樣。</span><span class="sxs-lookup"><span data-stu-id="3bc00-201">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="3bc00-202">如果第二次重新連線嘗試失敗，第三次重新連線嘗試將會在10秒內啟動，這同樣就像是預設設定。</span><span class="sxs-lookup"><span data-stu-id="3bc00-202">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="3bc00-203">然後，自訂行為會在第三次重新連線嘗試失敗之後停止，而不是在另一個30秒（如同在預設設定中）嘗試多次重新連線嘗試，再次從預設行為分歧。</span><span class="sxs-lookup"><span data-stu-id="3bc00-203">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="3bc00-204">如果您想要更充分掌控自動重新連線嘗試的時間和數目，請 `withAutomaticReconnect` 接受執行介面的物件 `IRetryPolicy` ，該介面具有名為的單一方法 `nextRetryDelayInMilliseconds` 。</span><span class="sxs-lookup"><span data-stu-id="3bc00-204">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="3bc00-205">`nextRetryDelayInMilliseconds` 採用具有類型的單一引數 `RetryContext` 。</span><span class="sxs-lookup"><span data-stu-id="3bc00-205">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="3bc00-206">`RetryContext`有三個屬性： `previousRetryCount` 、 `elapsedMilliseconds` 和 `retryReason` `number` 分別為、 `number` 和 `Error` 。</span><span class="sxs-lookup"><span data-stu-id="3bc00-206">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="3bc00-207">在第一次重新連線嘗試之前， `previousRetryCount` 和都 `elapsedMilliseconds` 是零，而且 `retryReason` 將會是導致中斷連接的錯誤。</span><span class="sxs-lookup"><span data-stu-id="3bc00-207">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="3bc00-208">每次重試失敗後，將會 `previousRetryCount` 遞增一次， `elapsedMilliseconds` 將會更新以反映到目前為止所花的時間（以毫秒為單位），而且 `retryReason` 會是造成上次重新連線嘗試失敗的錯誤。</span><span class="sxs-lookup"><span data-stu-id="3bc00-208">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="3bc00-209">`nextRetryDelayInMilliseconds` 必須傳回一個數位，代表下一次重新連接嘗試之前要等候的毫秒數，或 `null` 是否 `HubConnection` 應該停止重新連接。</span><span class="sxs-lookup"><span data-stu-id="3bc00-209">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

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

<span data-ttu-id="3bc00-210">或者，您也可以撰寫程式碼，以手動方式重新連接用戶端，如 [手動重新](#manually-reconnect)連線中所示範。</span><span class="sxs-lookup"><span data-stu-id="3bc00-210">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

### <a name="manually-reconnect"></a><span data-ttu-id="3bc00-211">手動重新連線</span><span class="sxs-lookup"><span data-stu-id="3bc00-211">Manually reconnect</span></span>

<span data-ttu-id="3bc00-212">下列程式碼示範一般手動重新連接方法：</span><span class="sxs-lookup"><span data-stu-id="3bc00-212">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="3bc00-213">函式 (在此案例中， `start` 會建立函式) 以開始連接。</span><span class="sxs-lookup"><span data-stu-id="3bc00-213">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="3bc00-214">`start`在連接的事件處理常式中呼叫函式 `onclose` 。</span><span class="sxs-lookup"><span data-stu-id="3bc00-214">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?range=30-40)]

<span data-ttu-id="3bc00-215">實際執行會使用指數輪詢，或在放棄之前重試指定的次數。</span><span class="sxs-lookup"><span data-stu-id="3bc00-215">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="troubleshoot-websocket-handshake-errors"></a><span data-ttu-id="3bc00-216">針對 WebSocket 交握錯誤進行疑難排解</span><span class="sxs-lookup"><span data-stu-id="3bc00-216">Troubleshoot WebSocket handshake errors</span></span>

<span data-ttu-id="3bc00-217">本節提供在嘗試建立與 ASP.NET Core hub 的連線時，發生「 *WebSocket 交握期間發生錯誤* 」例外狀況的說明 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="3bc00-217">This section provides help on the *"Error during WebSocket handshake"* exception that occurs when trying to establish connection to ASP.NET Core SignalR hub.</span></span>

### <a name="response-code-400-or-503"></a><span data-ttu-id="3bc00-218">回應碼400或503</span><span class="sxs-lookup"><span data-stu-id="3bc00-218">Response code 400 or 503</span></span>

<span data-ttu-id="3bc00-219">針對下列錯誤：</span><span class="sxs-lookup"><span data-stu-id="3bc00-219">For the following error:</span></span>

```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 400

Error: Failed to start the connection: Error: There was an error with the transport.
```

<span data-ttu-id="3bc00-220">此錯誤通常是因為用戶端只使用 Websocket 傳輸，但在伺服器上未啟用 Websocket 通訊協定。</span><span class="sxs-lookup"><span data-stu-id="3bc00-220">The error is usually caused by client only use WebSockets transport but WebSockets protocol is not enabled on server.</span></span>

### <a name="response-code-307"></a><span data-ttu-id="3bc00-221">回應碼307</span><span class="sxs-lookup"><span data-stu-id="3bc00-221">Response code 307</span></span>

```log
WebSocket connection to 'ws://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 307
```

<span data-ttu-id="3bc00-222">當中樞伺服器發生下列情況時，通常會發生這種情況 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="3bc00-222">Frequently this occurs when the SignalR hub server:</span></span>

* <span data-ttu-id="3bc00-223">接聽 HTTP 和 HTTPS，並對其進行回應。</span><span class="sxs-lookup"><span data-stu-id="3bc00-223">Listens to and responds over both HTTP and HTTPS.</span></span>
* <span data-ttu-id="3bc00-224">設定為在中呼叫以強制使用 HTTPS `UseHttpsRedirection` `Startup` ，或透過 URL 重寫規則強制使用 HTTPs。</span><span class="sxs-lookup"><span data-stu-id="3bc00-224">Is configured to enforce HTTPS by calling `UseHttpsRedirection` in `Startup`, or enforces HTTPS via URL rewrite rule.</span></span>

<span data-ttu-id="3bc00-225">此錯誤的原因可能是在用戶端上使用指定 HTTP URL `.withUrl("http://xxx/HubName")` 。</span><span class="sxs-lookup"><span data-stu-id="3bc00-225">This error can be caused by specifying the HTTP URL on client side using `.withUrl("http://xxx/HubName")`.</span></span> <span data-ttu-id="3bc00-226">此案例的修正是將程式碼修改為使用 HTTPS URL。</span><span class="sxs-lookup"><span data-stu-id="3bc00-226">The fix for this case is modifying the code to use an HTTPS URL.</span></span>

### <a name="response-code-404"></a><span data-ttu-id="3bc00-227">回應碼404</span><span class="sxs-lookup"><span data-stu-id="3bc00-227">Response code 404</span></span>

```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 404
```

<span data-ttu-id="3bc00-228">如果應用程式在 localhost 上運作，但在發佈至 IIS 伺服器之後傳回此錯誤：</span><span class="sxs-lookup"><span data-stu-id="3bc00-228">If the app works on localhost, but returns this error after publishing to IIS server:</span></span>

* <span data-ttu-id="3bc00-229">確認 ASP.NET Core 的 SignalR 應用程式是以 IIS 子應用程式的形式裝載。</span><span class="sxs-lookup"><span data-stu-id="3bc00-229">Verify the ASP.NET Core SignalR app is hosted as an IIS sub-application.</span></span>
* <span data-ttu-id="3bc00-230">請勿在 JavaScript 用戶端上使用子應用程式的 pathbase 來設定 URL SignalR `.withUrl("/SubAppName/HubName")` 。</span><span class="sxs-lookup"><span data-stu-id="3bc00-230">Don't set URL with the sub-app's pathbase on SignalR JavaScript client side `.withUrl("/SubAppName/HubName")`.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3bc00-231">其他資源</span><span class="sxs-lookup"><span data-stu-id="3bc00-231">Additional resources</span></span>

* [<span data-ttu-id="3bc00-232">JavaScript API 參考</span><span class="sxs-lookup"><span data-stu-id="3bc00-232">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest&preserve-view=true )
* [<span data-ttu-id="3bc00-233">JavaScript 教學課程</span><span class="sxs-lookup"><span data-stu-id="3bc00-233">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="3bc00-234">WebPack 和 TypeScript 教學課程</span><span class="sxs-lookup"><span data-stu-id="3bc00-234">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="3bc00-235">中樞</span><span class="sxs-lookup"><span data-stu-id="3bc00-235">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="3bc00-236">.NET 用戶端</span><span class="sxs-lookup"><span data-stu-id="3bc00-236">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="3bc00-237">發佈至 Azure</span><span class="sxs-lookup"><span data-stu-id="3bc00-237">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="3bc00-238"> (CORS) 的跨原始來源要求 </span><span class="sxs-lookup"><span data-stu-id="3bc00-238">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="3bc00-239">Azure SignalR 服務無伺服器檔</span><span class="sxs-lookup"><span data-stu-id="3bc00-239">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3bc00-240">作者：[Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="3bc00-240">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="3bc00-241">ASP.NET Core SignalR JavaScript 用戶端程式庫可讓開發人員呼叫伺服器端中樞程式碼。</span><span class="sxs-lookup"><span data-stu-id="3bc00-241">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="3bc00-242">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="3bc00-242">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-client-package"></a><span data-ttu-id="3bc00-243">安裝 SignalR 用戶端套件</span><span class="sxs-lookup"><span data-stu-id="3bc00-243">Install the SignalR client package</span></span>

<span data-ttu-id="3bc00-244">SignalRJavaScript 用戶端程式庫會以[npm](https://www.npmjs.com/)套件的形式傳遞。</span><span class="sxs-lookup"><span data-stu-id="3bc00-244">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="3bc00-245">下列各節概述安裝用戶端程式庫的不同方式。</span><span class="sxs-lookup"><span data-stu-id="3bc00-245">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="3bc00-246">使用 npm 安裝</span><span class="sxs-lookup"><span data-stu-id="3bc00-246">Install with npm</span></span>

<span data-ttu-id="3bc00-247">如果使用 Visual Studio，請在根資料夾中從 **封裝管理員主控台** 執行下列命令。</span><span class="sxs-lookup"><span data-stu-id="3bc00-247">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="3bc00-248">針對 Visual Studio Code，請從 **整合式終端**機執行下列命令。</span><span class="sxs-lookup"><span data-stu-id="3bc00-248">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="3bc00-249">npm 會將套件內容安裝\*node_modules \\ @aspnet\signalr\dist\browser \*資料夾。</span><span class="sxs-lookup"><span data-stu-id="3bc00-249">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="3bc00-250">在*wwwroot \\ lib*資料夾下建立名為*signalr*的新資料夾。</span><span class="sxs-lookup"><span data-stu-id="3bc00-250">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="3bc00-251">將 *signalr.js* 檔案複製到 *wwwroot\lib\signalr* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="3bc00-251">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

<span data-ttu-id="3bc00-252">參考 SignalR 元素中的 JavaScript 用戶端 `<script>` 。</span><span class="sxs-lookup"><span data-stu-id="3bc00-252">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="3bc00-253">例如：</span><span class="sxs-lookup"><span data-stu-id="3bc00-253">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="3bc00-254">使用 (CDN) 的內容傳遞網路</span><span class="sxs-lookup"><span data-stu-id="3bc00-254">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="3bc00-255">若要在沒有 npm 必要條件的情況下使用用戶端程式庫，請參考用戶端程式庫的 CDN 託管複本。</span><span class="sxs-lookup"><span data-stu-id="3bc00-255">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="3bc00-256">例如：</span><span class="sxs-lookup"><span data-stu-id="3bc00-256">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="3bc00-257">用戶端程式庫可在下列 Cdn 上取得：</span><span class="sxs-lookup"><span data-stu-id="3bc00-257">The client library is available on the following CDNs:</span></span>

* [<span data-ttu-id="3bc00-258">cdnjs</span><span class="sxs-lookup"><span data-stu-id="3bc00-258">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="3bc00-259">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="3bc00-259">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="3bc00-260">unpkg</span><span class="sxs-lookup"><span data-stu-id="3bc00-260">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a><span data-ttu-id="3bc00-261">使用 LibMan 安裝</span><span class="sxs-lookup"><span data-stu-id="3bc00-261">Install with LibMan</span></span>

<span data-ttu-id="3bc00-262">[LibMan](xref:client-side/libman/index) 可以用來從 CDN 裝載的用戶端程式庫安裝特定的用戶端程式庫檔案。</span><span class="sxs-lookup"><span data-stu-id="3bc00-262">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="3bc00-263">例如，只將縮減 JavaScript 檔案新增至專案。</span><span class="sxs-lookup"><span data-stu-id="3bc00-263">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="3bc00-264">如需該方法的詳細資訊，請參閱 [新增 SignalR 用戶端程式庫](xref:tutorials/signalr#add-the-signalr-client-library)。</span><span class="sxs-lookup"><span data-stu-id="3bc00-264">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="3bc00-265">連接至中樞</span><span class="sxs-lookup"><span data-stu-id="3bc00-265">Connect to a hub</span></span>

<span data-ttu-id="3bc00-266">下列程式碼會建立並啟動連接。</span><span class="sxs-lookup"><span data-stu-id="3bc00-266">The following code creates and starts a connection.</span></span> <span data-ttu-id="3bc00-267">中樞的名稱不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="3bc00-267">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=9-13,28-51)]

### <a name="cross-origin-connections"></a><span data-ttu-id="3bc00-268">跨原始連接</span><span class="sxs-lookup"><span data-stu-id="3bc00-268">Cross-origin connections</span></span>

<span data-ttu-id="3bc00-269">一般而言，瀏覽器會從與要求的頁面相同的網域載入連接。</span><span class="sxs-lookup"><span data-stu-id="3bc00-269">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="3bc00-270">不過，在某些情況下需要連接到另一個網域。</span><span class="sxs-lookup"><span data-stu-id="3bc00-270">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="3bc00-271">為了防止惡意網站從另一個網站讀取敏感性資料，預設會停用 [跨原始來源的連接](xref:security/cors) 。</span><span class="sxs-lookup"><span data-stu-id="3bc00-271">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="3bc00-272">若要允許跨原始來源的要求，請在類別中加以啟用 `Startup` 。</span><span class="sxs-lookup"><span data-stu-id="3bc00-272">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/samples/2.x/SignalRChat/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="3bc00-273">從用戶端呼叫中樞方法</span><span class="sxs-lookup"><span data-stu-id="3bc00-273">Call hub methods from client</span></span>

<span data-ttu-id="3bc00-274">JavaScript 用戶端會透過[HubConnection](/javascript/api/%40aspnet/signalr/hubconnection)的[invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke)方法，呼叫中樞上的公用方法。</span><span class="sxs-lookup"><span data-stu-id="3bc00-274">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="3bc00-275">`invoke`方法會接受兩個引數：</span><span class="sxs-lookup"><span data-stu-id="3bc00-275">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="3bc00-276">中樞方法的名稱。</span><span class="sxs-lookup"><span data-stu-id="3bc00-276">The name of the hub method.</span></span> <span data-ttu-id="3bc00-277">在下列範例中，中樞上的方法名稱為 `SendMessage` 。</span><span class="sxs-lookup"><span data-stu-id="3bc00-277">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="3bc00-278">中樞方法中定義的任何引數。</span><span class="sxs-lookup"><span data-stu-id="3bc00-278">Any arguments defined in the hub method.</span></span> <span data-ttu-id="3bc00-279">在下列範例中，引數名稱為 `message` 。</span><span class="sxs-lookup"><span data-stu-id="3bc00-279">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="3bc00-280">範例程式碼會使用箭號函式語法，此語法在所有主要瀏覽器的目前版本中受到支援，但 Internet Explorer 除外。</span><span class="sxs-lookup"><span data-stu-id="3bc00-280">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="3bc00-281">只有 SignalR 在 *預設* 模式下使用 Azure 服務時，才支援從用戶端呼叫中樞方法。</span><span class="sxs-lookup"><span data-stu-id="3bc00-281">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="3bc00-282">如需詳細資訊，請參閱 [ (azure Signalr GitHub 存放庫) ](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose)的常見問題。</span><span class="sxs-lookup"><span data-stu-id="3bc00-282">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

<span data-ttu-id="3bc00-283">方法會傳回 `invoke` JavaScript [承諾](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)。</span><span class="sxs-lookup"><span data-stu-id="3bc00-283">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="3bc00-284">`Promise`當伺服器上的方法傳回時，會使用傳回值來解析 (是否有任何) 。</span><span class="sxs-lookup"><span data-stu-id="3bc00-284">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="3bc00-285">如果伺服器上的方法擲回錯誤，則 `Promise` 會被拒絕，並顯示錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="3bc00-285">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="3bc00-286">您 `then` 可以使用本身的和 `catch` 方法 `Promise` ， (或語法) 來處理這些情況 `await` 。</span><span class="sxs-lookup"><span data-stu-id="3bc00-286">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="3bc00-287">方法會傳回 `send` JavaScript `Promise` 。</span><span class="sxs-lookup"><span data-stu-id="3bc00-287">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="3bc00-288">`Promise`當訊息傳送到伺服器時，會解析。</span><span class="sxs-lookup"><span data-stu-id="3bc00-288">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="3bc00-289">如果傳送訊息時發生錯誤， `Promise` 就會拒絕，並顯示錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="3bc00-289">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="3bc00-290">您 `then` 可以使用本身的和 `catch` 方法 `Promise` ， (或語法) 來處理這些情況 `await` 。</span><span class="sxs-lookup"><span data-stu-id="3bc00-290">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="3bc00-291">使用 `send` 並不會等到伺服器收到訊息為止。</span><span class="sxs-lookup"><span data-stu-id="3bc00-291">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="3bc00-292">因此，不可能從伺服器傳回資料或錯誤。</span><span class="sxs-lookup"><span data-stu-id="3bc00-292">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="3bc00-293">從中樞呼叫用戶端方法</span><span class="sxs-lookup"><span data-stu-id="3bc00-293">Call client methods from hub</span></span>

<span data-ttu-id="3bc00-294">若要從中樞接收訊息，請使用的 [on](/javascript/api/%40aspnet/signalr/hubconnection#on) 方法定義方法 `HubConnection` 。</span><span class="sxs-lookup"><span data-stu-id="3bc00-294">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="3bc00-295">JavaScript 用戶端方法的名稱。</span><span class="sxs-lookup"><span data-stu-id="3bc00-295">The name of the JavaScript client method.</span></span> <span data-ttu-id="3bc00-296">在下列範例中，方法名稱為 `ReceiveMessage` 。</span><span class="sxs-lookup"><span data-stu-id="3bc00-296">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="3bc00-297">中樞傳遞給方法的引數。</span><span class="sxs-lookup"><span data-stu-id="3bc00-297">Arguments the hub passes to the method.</span></span> <span data-ttu-id="3bc00-298">在下列範例中，引數值為 `message` 。</span><span class="sxs-lookup"><span data-stu-id="3bc00-298">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="3bc00-299">中的上述程式碼 `connection.on` 會在伺服器端程式碼使用方法呼叫它時執行 <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> 。</span><span class="sxs-lookup"><span data-stu-id="3bc00-299">The preceding code in `connection.on` runs when server-side code calls it using the <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> method.</span></span>

[!code-csharp[Call client-side](javascript-client/samples/2.x/SignalRChat/hubs/chathub.cs?range=8-11)]

<span data-ttu-id="3bc00-300">SignalR 藉由比對和中定義的方法名稱和引數，判斷要呼叫的用戶端方法 `SendAsync` `connection.on` 。</span><span class="sxs-lookup"><span data-stu-id="3bc00-300">SignalR determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="3bc00-301">最佳做法是在之後呼叫 [start](/javascript/api/%40aspnet/signalr/hubconnection#start) 方法 `HubConnection` `on` 。</span><span class="sxs-lookup"><span data-stu-id="3bc00-301">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="3bc00-302">這麼做可確保您的處理常式會在收到任何訊息之前註冊。</span><span class="sxs-lookup"><span data-stu-id="3bc00-302">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="3bc00-303">錯誤處理和記錄</span><span class="sxs-lookup"><span data-stu-id="3bc00-303">Error handling and logging</span></span>

<span data-ttu-id="3bc00-304">將 `catch` 方法連結至方法的結尾 `start` ，以處理用戶端錯誤。</span><span class="sxs-lookup"><span data-stu-id="3bc00-304">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="3bc00-305">用 `console.error` 來將錯誤輸出至瀏覽器的主控台。</span><span class="sxs-lookup"><span data-stu-id="3bc00-305">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=50)]

<span data-ttu-id="3bc00-306">設定用戶端記錄檔追蹤，方法是在建立連接時，傳遞記錄器和事件種類來記錄。</span><span class="sxs-lookup"><span data-stu-id="3bc00-306">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="3bc00-307">訊息會以指定的記錄層級和更新版本記錄。</span><span class="sxs-lookup"><span data-stu-id="3bc00-307">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="3bc00-308">可用的記錄層級如下所示：</span><span class="sxs-lookup"><span data-stu-id="3bc00-308">Available log levels are as follows:</span></span>

* <span data-ttu-id="3bc00-309">`signalR.LogLevel.Error`：錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="3bc00-309">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="3bc00-310">`Error`只記錄訊息。</span><span class="sxs-lookup"><span data-stu-id="3bc00-310">Logs `Error` messages only.</span></span>
* <span data-ttu-id="3bc00-311">`signalR.LogLevel.Warning`：可能發生錯誤的警告訊息。</span><span class="sxs-lookup"><span data-stu-id="3bc00-311">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="3bc00-312">記錄檔 `Warning` 和 `Error` 訊息。</span><span class="sxs-lookup"><span data-stu-id="3bc00-312">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="3bc00-313">`signalR.LogLevel.Information`：沒有錯誤的狀態訊息。</span><span class="sxs-lookup"><span data-stu-id="3bc00-313">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="3bc00-314">記錄 `Information` 、 `Warning` 和 `Error` 訊息。</span><span class="sxs-lookup"><span data-stu-id="3bc00-314">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="3bc00-315">`signalR.LogLevel.Trace`：追蹤訊息。</span><span class="sxs-lookup"><span data-stu-id="3bc00-315">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="3bc00-316">記錄所有內容，包括中樞與用戶端之間傳輸的資料。</span><span class="sxs-lookup"><span data-stu-id="3bc00-316">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="3bc00-317">在[HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)上使用[configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging)方法來設定記錄層級。</span><span class="sxs-lookup"><span data-stu-id="3bc00-317">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="3bc00-318">訊息會記錄到瀏覽器主控台。</span><span class="sxs-lookup"><span data-stu-id="3bc00-318">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="3bc00-319">重新連線用戶端</span><span class="sxs-lookup"><span data-stu-id="3bc00-319">Reconnect clients</span></span>

### <a name="manually-reconnect"></a><span data-ttu-id="3bc00-320">手動重新連線</span><span class="sxs-lookup"><span data-stu-id="3bc00-320">Manually reconnect</span></span>

> [!WARNING]
> <span data-ttu-id="3bc00-321">在3.0 之前，的 JavaScript 用戶端 SignalR 不會自動重新連線。</span><span class="sxs-lookup"><span data-stu-id="3bc00-321">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="3bc00-322">您必須撰寫會以手動方式重新連接用戶端的程式碼。</span><span class="sxs-lookup"><span data-stu-id="3bc00-322">You must write code that will reconnect your client manually.</span></span>

<span data-ttu-id="3bc00-323">下列程式碼示範一般手動重新連接方法：</span><span class="sxs-lookup"><span data-stu-id="3bc00-323">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="3bc00-324">函式 (在此案例中， `start` 會建立函式) 以開始連接。</span><span class="sxs-lookup"><span data-stu-id="3bc00-324">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="3bc00-325">`start`在連接的事件處理常式中呼叫函式 `onclose` 。</span><span class="sxs-lookup"><span data-stu-id="3bc00-325">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="3bc00-326">實際執行會使用指數輪詢，或在放棄之前重試指定的次數。</span><span class="sxs-lookup"><span data-stu-id="3bc00-326">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3bc00-327">其他資源</span><span class="sxs-lookup"><span data-stu-id="3bc00-327">Additional resources</span></span>

* [<span data-ttu-id="3bc00-328">JavaScript API 參考</span><span class="sxs-lookup"><span data-stu-id="3bc00-328">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="3bc00-329">JavaScript 教學課程</span><span class="sxs-lookup"><span data-stu-id="3bc00-329">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="3bc00-330">WebPack 和 TypeScript 教學課程</span><span class="sxs-lookup"><span data-stu-id="3bc00-330">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="3bc00-331">中樞</span><span class="sxs-lookup"><span data-stu-id="3bc00-331">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="3bc00-332">.NET 用戶端</span><span class="sxs-lookup"><span data-stu-id="3bc00-332">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="3bc00-333">發佈至 Azure</span><span class="sxs-lookup"><span data-stu-id="3bc00-333">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="3bc00-334"> (CORS) 的跨原始來源要求 </span><span class="sxs-lookup"><span data-stu-id="3bc00-334">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="3bc00-335">Azure SignalR 服務無伺服器檔</span><span class="sxs-lookup"><span data-stu-id="3bc00-335">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)

::: moniker-end
