---
title: 'ASP.NET Core :::no-loc(SignalR)::: JavaScript 用戶端'
author: bradygaster
description: 'ASP.NET Core :::no-loc(SignalR)::: JavaScript 用戶端的總覽。'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc, devx-track-js
ms.date: 04/08/2020
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
uid: signalr/javascript-client
ms.openlocfilehash: b4b1bc6131a6676710adbf2503efe3f304d89a58
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050845"
---
# <a name="aspnet-core-no-locsignalr-javascript-client"></a><span data-ttu-id="a65a2-103">ASP.NET Core :::no-loc(SignalR)::: JavaScript 用戶端</span><span class="sxs-lookup"><span data-stu-id="a65a2-103">ASP.NET Core :::no-loc(SignalR)::: JavaScript client</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a65a2-104">作者：[Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="a65a2-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="a65a2-105">ASP.NET Core :::no-loc(SignalR)::: JavaScript 用戶端程式庫可讓開發人員呼叫伺服器端中樞程式碼。</span><span class="sxs-lookup"><span data-stu-id="a65a2-105">The ASP.NET Core :::no-loc(SignalR)::: JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="a65a2-106">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="a65a2-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-client-package"></a><span data-ttu-id="a65a2-107">安裝 :::no-loc(SignalR)::: 用戶端套件</span><span class="sxs-lookup"><span data-stu-id="a65a2-107">Install the :::no-loc(SignalR)::: client package</span></span>

<span data-ttu-id="a65a2-108">:::no-loc(SignalR):::JavaScript 用戶端程式庫會以[npm](https://www.npmjs.com/)套件的形式傳遞。</span><span class="sxs-lookup"><span data-stu-id="a65a2-108">The :::no-loc(SignalR)::: JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="a65a2-109">下列各節概述安裝用戶端程式庫的不同方式。</span><span class="sxs-lookup"><span data-stu-id="a65a2-109">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="a65a2-110">使用 npm 安裝</span><span class="sxs-lookup"><span data-stu-id="a65a2-110">Install with npm</span></span>

<span data-ttu-id="a65a2-111">針對 Visual Studio，請在根資料夾中從 **封裝管理員主控台** 執行下列命令。</span><span class="sxs-lookup"><span data-stu-id="a65a2-111">For Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="a65a2-112">針對 Visual Studio Code，請從 **整合式終端** 機執行下列命令。</span><span class="sxs-lookup"><span data-stu-id="a65a2-112">For Visual Studio Code, run the following commands from the **Integrated Terminal** .</span></span>

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="a65a2-113">npm 會將套件內容安裝 *node_modules \\ @microsoft\signalr\dist\browser* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="a65a2-113">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="a65a2-114">在 *wwwroot \\ lib* 資料夾下建立名為 *signalr* 的新資料夾。</span><span class="sxs-lookup"><span data-stu-id="a65a2-114">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="a65a2-115">將 *signalr.js* 檔案複製到 *wwwroot\lib\signalr* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="a65a2-115">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

<span data-ttu-id="a65a2-116">參考 :::no-loc(SignalR)::: 元素中的 JavaScript 用戶端 `<script>` 。</span><span class="sxs-lookup"><span data-stu-id="a65a2-116">Reference the :::no-loc(SignalR)::: JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="a65a2-117">例如：</span><span class="sxs-lookup"><span data-stu-id="a65a2-117">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="a65a2-118">使用 (CDN) 的內容傳遞網路</span><span class="sxs-lookup"><span data-stu-id="a65a2-118">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="a65a2-119">若要在沒有 npm 必要條件的情況下使用用戶端程式庫，請參考用戶端程式庫的 CDN 託管複本。</span><span class="sxs-lookup"><span data-stu-id="a65a2-119">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="a65a2-120">例如：</span><span class="sxs-lookup"><span data-stu-id="a65a2-120">For example:</span></span>

[!code-html[](javascript-client/samples/3.x/:::no-loc(SignalR):::Chat/Pages/Index.cshtml?name=snippet_CDN)]

<span data-ttu-id="a65a2-121">用戶端程式庫可在下列 Cdn 上取得：</span><span class="sxs-lookup"><span data-stu-id="a65a2-121">The client library is available on the following CDNs:</span></span>

* [<span data-ttu-id="a65a2-122">cdnjs</span><span class="sxs-lookup"><span data-stu-id="a65a2-122">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="a65a2-123">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="a65a2-123">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="a65a2-124">unpkg</span><span class="sxs-lookup"><span data-stu-id="a65a2-124">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a><span data-ttu-id="a65a2-125">使用 LibMan 安裝</span><span class="sxs-lookup"><span data-stu-id="a65a2-125">Install with LibMan</span></span>

<span data-ttu-id="a65a2-126">[LibMan](xref:client-side/libman/index) 可以用來從 CDN 裝載的用戶端程式庫安裝特定的用戶端程式庫檔案。</span><span class="sxs-lookup"><span data-stu-id="a65a2-126">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="a65a2-127">例如，只將縮減 JavaScript 檔案新增至專案。</span><span class="sxs-lookup"><span data-stu-id="a65a2-127">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="a65a2-128">如需該方法的詳細資訊，請參閱 [新增 :::no-loc(SignalR)::: 用戶端程式庫](xref:tutorials/signalr#add-the-signalr-client-library)。</span><span class="sxs-lookup"><span data-stu-id="a65a2-128">For details on that approach, see [Add the :::no-loc(SignalR)::: client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="a65a2-129">連接至中樞</span><span class="sxs-lookup"><span data-stu-id="a65a2-129">Connect to a hub</span></span>

<span data-ttu-id="a65a2-130">下列程式碼會建立並啟動連接。</span><span class="sxs-lookup"><span data-stu-id="a65a2-130">The following code creates and starts a connection.</span></span> <span data-ttu-id="a65a2-131">中樞的名稱不區分大小寫：</span><span class="sxs-lookup"><span data-stu-id="a65a2-131">The hub's name is case insensitive:</span></span>

[!code-javascript[](javascript-client/samples/3.x/:::no-loc(SignalR):::Chat/wwwroot/chat.js?range=3-6,29-43)]

### <a name="cross-origin-connections"></a><span data-ttu-id="a65a2-132">跨原始連接</span><span class="sxs-lookup"><span data-stu-id="a65a2-132">Cross-origin connections</span></span>

<span data-ttu-id="a65a2-133">一般而言，瀏覽器會從與要求的頁面相同的網域載入連接。</span><span class="sxs-lookup"><span data-stu-id="a65a2-133">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="a65a2-134">不過，在某些情況下需要連接到另一個網域。</span><span class="sxs-lookup"><span data-stu-id="a65a2-134">However, there are occasions when a connection to another domain is required.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a65a2-135">用戶端程式代碼必須使用絕對 URL，而不是相對 URL。</span><span class="sxs-lookup"><span data-stu-id="a65a2-135">The client code must use an absolute URL instead of a relative URL.</span></span> <span data-ttu-id="a65a2-136">將 `.withUrl("/chathub")` 變更為 `.withUrl("https://myappurl/chathub")`。</span><span class="sxs-lookup"><span data-stu-id="a65a2-136">Change `.withUrl("/chathub")` to `.withUrl("https://myappurl/chathub")`.</span></span>

<span data-ttu-id="a65a2-137">為了防止惡意網站從另一個網站讀取敏感性資料，預設會停用 [跨原始來源的連接](xref:security/cors) 。</span><span class="sxs-lookup"><span data-stu-id="a65a2-137">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="a65a2-138">若要允許跨原始來源的要求，請在類別中啟用它 `Startup` ：</span><span class="sxs-lookup"><span data-stu-id="a65a2-138">To allow a cross-origin request, enable it in the `Startup` class:</span></span>

[!code-csharp[](javascript-client/samples/3.x/:::no-loc(SignalR):::Chat/Startup.cs?highlight=16-23,40)]

## <a name="call-hub-methods-from-the-client"></a><span data-ttu-id="a65a2-139">從用戶端呼叫中樞方法</span><span class="sxs-lookup"><span data-stu-id="a65a2-139">Call hub methods from the client</span></span>

<span data-ttu-id="a65a2-140">JavaScript 用戶端會透過[HubConnection](/javascript/api/%40microsoft/signalr/hubconnection)的[invoke](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---)方法，呼叫中樞上的公用方法。</span><span class="sxs-lookup"><span data-stu-id="a65a2-140">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---) method of the [HubConnection](/javascript/api/%40microsoft/signalr/hubconnection).</span></span> <span data-ttu-id="a65a2-141">此 `invoke` 方法會接受：</span><span class="sxs-lookup"><span data-stu-id="a65a2-141">The `invoke` method accepts:</span></span>

* <span data-ttu-id="a65a2-142">中樞方法的名稱。</span><span class="sxs-lookup"><span data-stu-id="a65a2-142">The name of the hub method.</span></span>
* <span data-ttu-id="a65a2-143">中樞方法中定義的任何引數。</span><span class="sxs-lookup"><span data-stu-id="a65a2-143">Any arguments defined in the hub method.</span></span>

<span data-ttu-id="a65a2-144">在下列範例中，中樞上的方法名稱為 `SendMessage` 。</span><span class="sxs-lookup"><span data-stu-id="a65a2-144">In the following example, the method name on the hub is `SendMessage`.</span></span> <span data-ttu-id="a65a2-145">傳遞給的第二個和第三個引數會 `invoke` 對應至中樞方法的 `user` 和 `message` 引數：</span><span class="sxs-lookup"><span data-stu-id="a65a2-145">The second and third arguments passed to `invoke` map to the hub method's `user` and `message` arguments:</span></span>

[!code-javascript[](javascript-client/samples/3.x/:::no-loc(SignalR):::Chat/wwwroot/chat.js?name=snippet_Invoke&highlight=2)]

> [!NOTE]
> <span data-ttu-id="a65a2-146">只有 :::no-loc(SignalR)::: 在 *預設* 模式下使用 Azure 服務時，才支援從用戶端呼叫中樞方法。</span><span class="sxs-lookup"><span data-stu-id="a65a2-146">Calling hub methods from a client is only supported when using the Azure :::no-loc(SignalR)::: Service in *Default* mode.</span></span> <span data-ttu-id="a65a2-147">如需詳細資訊，請參閱 [ (azure Signalr GitHub 存放庫) ](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose)的常見問題。</span><span class="sxs-lookup"><span data-stu-id="a65a2-147">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

<span data-ttu-id="a65a2-148">方法會傳回 `invoke` JavaScript [承諾](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)。</span><span class="sxs-lookup"><span data-stu-id="a65a2-148">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="a65a2-149">`Promise`當伺服器上的方法傳回時，會使用傳回值來解析 (是否有任何) 。</span><span class="sxs-lookup"><span data-stu-id="a65a2-149">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="a65a2-150">如果伺服器上的方法擲回錯誤，則 `Promise` 會被拒絕，並顯示錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="a65a2-150">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="a65a2-151">使用 `async` 和 `await` 或的 `Promise` `then` 和 `catch` 方法來處理這些情況。</span><span class="sxs-lookup"><span data-stu-id="a65a2-151">Use `async` and `await` or the `Promise`'s `then` and `catch` methods to handle these cases.</span></span>

<span data-ttu-id="a65a2-152">JavaScript 用戶端也可以透過的 [傳送](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) 方法，呼叫中樞上的公用方法 `HubConnection` 。</span><span class="sxs-lookup"><span data-stu-id="a65a2-152">JavaScript clients can also call public methods on hubs via the the [send](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) method of the `HubConnection`.</span></span> <span data-ttu-id="a65a2-153">與方法不同的是 `invoke` ， `send` 方法不會等候伺服器的回應。</span><span class="sxs-lookup"><span data-stu-id="a65a2-153">Unlike the `invoke` method, the `send` method doesn't wait for a response from the server.</span></span> <span data-ttu-id="a65a2-154">方法會傳回 `send` JavaScript `Promise` 。</span><span class="sxs-lookup"><span data-stu-id="a65a2-154">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="a65a2-155">`Promise`當訊息傳送到伺服器時，會解析。</span><span class="sxs-lookup"><span data-stu-id="a65a2-155">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="a65a2-156">如果傳送訊息時發生錯誤， `Promise` 就會拒絕，並顯示錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="a65a2-156">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="a65a2-157">使用 `async` 和 `await` 或的 `Promise` `then` 和 `catch` 方法來處理這些情況。</span><span class="sxs-lookup"><span data-stu-id="a65a2-157">Use `async` and `await` or the `Promise`'s `then` and `catch` methods to handle these cases.</span></span>

> [!NOTE]
> <span data-ttu-id="a65a2-158">使用 `send` 並不會等到伺服器收到訊息為止。</span><span class="sxs-lookup"><span data-stu-id="a65a2-158">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="a65a2-159">因此，不可能從伺服器傳回資料或錯誤。</span><span class="sxs-lookup"><span data-stu-id="a65a2-159">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-the-hub"></a><span data-ttu-id="a65a2-160">從中樞呼叫用戶端方法</span><span class="sxs-lookup"><span data-stu-id="a65a2-160">Call client methods from the hub</span></span>

<span data-ttu-id="a65a2-161">若要從中樞接收訊息，請使用的 [on](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) 方法定義方法 `HubConnection` 。</span><span class="sxs-lookup"><span data-stu-id="a65a2-161">To receive messages from the hub, define a method using the [on](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) method of the `HubConnection`.</span></span>

* <span data-ttu-id="a65a2-162">JavaScript 用戶端方法的名稱。</span><span class="sxs-lookup"><span data-stu-id="a65a2-162">The name of the JavaScript client method.</span></span>
* <span data-ttu-id="a65a2-163">中樞傳遞給方法的引數。</span><span class="sxs-lookup"><span data-stu-id="a65a2-163">Arguments the hub passes to the method.</span></span>

<span data-ttu-id="a65a2-164">在下列範例中，方法名稱為 `ReceiveMessage` 。</span><span class="sxs-lookup"><span data-stu-id="a65a2-164">In the following example, the method name is `ReceiveMessage`.</span></span> <span data-ttu-id="a65a2-165">引數名稱為 `user` 和 `message` ：</span><span class="sxs-lookup"><span data-stu-id="a65a2-165">The argument names are `user` and `message`:</span></span>

[!code-javascript[](javascript-client/samples/3.x/:::no-loc(SignalR):::Chat/wwwroot/chat.js?name=snippet_ReceiveMessage)]

<span data-ttu-id="a65a2-166">中的上述程式碼 `connection.on` 會在伺服器端程式碼使用方法呼叫它時執行 <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.ClientProxyExtensions.SendAsync%2A> ：</span><span class="sxs-lookup"><span data-stu-id="a65a2-166">The preceding code in `connection.on` runs when server-side code calls it using the <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.ClientProxyExtensions.SendAsync%2A> method:</span></span>

[!code-csharp[Call client-side](javascript-client/samples/3.x/:::no-loc(SignalR):::Chat/Hubs/ChatHub.cs?name=snippet_SendMessage)]

<span data-ttu-id="a65a2-167">:::no-loc(SignalR)::: 藉由比對和中定義的方法名稱和引數，判斷要呼叫的用戶端方法 `SendAsync` `connection.on` 。</span><span class="sxs-lookup"><span data-stu-id="a65a2-167">:::no-loc(SignalR)::: determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="a65a2-168">最佳做法是在之後呼叫 [start](/javascript/api/%40aspnet/signalr/hubconnection#start) 方法 `HubConnection` `on` 。</span><span class="sxs-lookup"><span data-stu-id="a65a2-168">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="a65a2-169">這麼做可確保您的處理常式會在收到任何訊息之前註冊。</span><span class="sxs-lookup"><span data-stu-id="a65a2-169">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="a65a2-170">錯誤處理和記錄</span><span class="sxs-lookup"><span data-stu-id="a65a2-170">Error handling and logging</span></span>

<span data-ttu-id="a65a2-171">使用 `try` 和 `catch` 搭配 `async` 和 `await` 或的 `Promise` `catch` 方法來處理用戶端錯誤。</span><span class="sxs-lookup"><span data-stu-id="a65a2-171">Use `try` and `catch` with `async` and `await` or the `Promise`'s `catch` method to handle client-side errors.</span></span> <span data-ttu-id="a65a2-172">使用 `console.error` 將錯誤輸出至瀏覽器的主控台：</span><span class="sxs-lookup"><span data-stu-id="a65a2-172">Use `console.error` to output errors to the browser's console:</span></span>

[!code-javascript[](javascript-client/samples/3.x/:::no-loc(SignalR):::Chat/wwwroot/chat.js?name=snippet_Invoke&highlight=1,3-5)]

<span data-ttu-id="a65a2-173">設定用戶端記錄檔追蹤，方法是在建立連接時，傳遞記錄器和事件種類來記錄。</span><span class="sxs-lookup"><span data-stu-id="a65a2-173">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="a65a2-174">訊息會以指定的記錄層級和更新版本記錄。</span><span class="sxs-lookup"><span data-stu-id="a65a2-174">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="a65a2-175">可用的記錄層級如下所示：</span><span class="sxs-lookup"><span data-stu-id="a65a2-175">Available log levels are as follows:</span></span>

* <span data-ttu-id="a65a2-176">`signalR.LogLevel.Error`：錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="a65a2-176">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="a65a2-177">`Error`只記錄訊息。</span><span class="sxs-lookup"><span data-stu-id="a65a2-177">Logs `Error` messages only.</span></span>
* <span data-ttu-id="a65a2-178">`signalR.LogLevel.Warning`：可能發生錯誤的警告訊息。</span><span class="sxs-lookup"><span data-stu-id="a65a2-178">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="a65a2-179">記錄檔 `Warning` 和 `Error` 訊息。</span><span class="sxs-lookup"><span data-stu-id="a65a2-179">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="a65a2-180">`signalR.LogLevel.Information`：沒有錯誤的狀態訊息。</span><span class="sxs-lookup"><span data-stu-id="a65a2-180">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="a65a2-181">記錄 `Information` 、 `Warning` 和 `Error` 訊息。</span><span class="sxs-lookup"><span data-stu-id="a65a2-181">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="a65a2-182">`signalR.LogLevel.Trace`：追蹤訊息。</span><span class="sxs-lookup"><span data-stu-id="a65a2-182">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="a65a2-183">記錄所有內容，包括中樞與用戶端之間傳輸的資料。</span><span class="sxs-lookup"><span data-stu-id="a65a2-183">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="a65a2-184">在[HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)上使用[configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging)方法來設定記錄層級。</span><span class="sxs-lookup"><span data-stu-id="a65a2-184">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="a65a2-185">訊息會記錄到瀏覽器主控台：</span><span class="sxs-lookup"><span data-stu-id="a65a2-185">Messages are logged to the browser console:</span></span>

[!code-javascript[](javascript-client/samples/3.x/:::no-loc(SignalR):::Chat/wwwroot/chat.js?name=snippet_Connection&highlight=3)]

## <a name="reconnect-clients"></a><span data-ttu-id="a65a2-186">重新連線用戶端</span><span class="sxs-lookup"><span data-stu-id="a65a2-186">Reconnect clients</span></span>

### <a name="automatically-reconnect"></a><span data-ttu-id="a65a2-187">自動重新連線</span><span class="sxs-lookup"><span data-stu-id="a65a2-187">Automatically reconnect</span></span>

<span data-ttu-id="a65a2-188">的 JavaScript 用戶端 :::no-loc(SignalR)::: 可以設定為使用 `withAutomaticReconnect` [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)上的方法自動重新連接。</span><span class="sxs-lookup"><span data-stu-id="a65a2-188">The JavaScript client for :::no-loc(SignalR)::: can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="a65a2-189">它預設不會自動重新連線。</span><span class="sxs-lookup"><span data-stu-id="a65a2-189">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="a65a2-190">如果沒有任何參數， `withAutomaticReconnect()` 則會先將用戶端設定為等候0、2、10和30秒，然後再嘗試每次嘗試重新連線，並在嘗試四次失敗之後停止。</span><span class="sxs-lookup"><span data-stu-id="a65a2-190">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="a65a2-191">在開始進行任何重新連接嘗試之前， `HubConnection` 會轉換成 `HubConnectionState.Reconnecting` 狀態並引發 `onreconnecting` 回呼，而不是轉換成 `Disconnected` 狀態並觸發其 `onclose` 回呼，例如 `HubConnection` 未設定自動重新連線。</span><span class="sxs-lookup"><span data-stu-id="a65a2-191">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="a65a2-192">這可讓使用者警告使用者連接已遺失，並停用 UI 元素。</span><span class="sxs-lookup"><span data-stu-id="a65a2-192">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="a65a2-193">如果用戶端在前四次嘗試中成功重新連接，則 `HubConnection` 會轉換回 `Connected` 狀態並引發 `onreconnected` 回呼。</span><span class="sxs-lookup"><span data-stu-id="a65a2-193">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="a65a2-194">這會讓您有機會通知使用者已重新建立連接。</span><span class="sxs-lookup"><span data-stu-id="a65a2-194">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="a65a2-195">由於連接完全是伺服器的新，因此 `connectionId` 會提供新的 `onreconnected` 回呼。</span><span class="sxs-lookup"><span data-stu-id="a65a2-195">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="a65a2-196">`onreconnected` `connectionId` 如果 `HubConnection` 設定為[略過協商](xref:signalr/configuration#configure-client-options)，回呼的參數將會是未定義的。</span><span class="sxs-lookup"><span data-stu-id="a65a2-196">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="a65a2-197">`withAutomaticReconnect()` 將不會設定 `HubConnection` 重試初始啟動失敗，因此必須手動處理啟動失敗：</span><span class="sxs-lookup"><span data-stu-id="a65a2-197">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

```javascript
async function start() {
    try {
        await connection.start();
        console.assert(connection.state === signalR.HubConnectionState.Connected);
        console.log(":::no-loc(SignalR)::: Connected.");
    } catch (err) {
        console.assert(connection.state === signalR.HubConnectionState.Disconnected);
        console.log(err);
        setTimeout(() => start(), 5000);
    }
};
```

<span data-ttu-id="a65a2-198">如果用戶端在前四次嘗試中未成功重新連接，則 `HubConnection` 會轉換為 `Disconnected` 狀態並引發其 [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) 回呼。</span><span class="sxs-lookup"><span data-stu-id="a65a2-198">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="a65a2-199">這讓您有機會通知使用者連線已永久遺失，並建議重新整理頁面：</span><span class="sxs-lookup"><span data-stu-id="a65a2-199">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="a65a2-200">若要在中斷連線或變更重新連線時間之前設定自訂的重新連接嘗試次數，請 `withAutomaticReconnect` 接受代表延遲的數位陣列，以毫秒為單位來開始每次重新連線嘗試。</span><span class="sxs-lookup"><span data-stu-id="a65a2-200">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="a65a2-201">上述範例會將設定 `HubConnection` 為在連接遺失之後立即開始嘗試重新連接。</span><span class="sxs-lookup"><span data-stu-id="a65a2-201">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="a65a2-202">這也適用于預設設定。</span><span class="sxs-lookup"><span data-stu-id="a65a2-202">This is also true for the default configuration.</span></span>

<span data-ttu-id="a65a2-203">如果第一次重新連線嘗試失敗，第二次重新連線嘗試也會立即開始，而不是等候2秒，就像在預設設定中一樣。</span><span class="sxs-lookup"><span data-stu-id="a65a2-203">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="a65a2-204">如果第二次重新連線嘗試失敗，第三次重新連線嘗試將會在10秒內啟動，這同樣就像是預設設定。</span><span class="sxs-lookup"><span data-stu-id="a65a2-204">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="a65a2-205">然後，自訂行為會在第三次重新連線嘗試失敗之後停止，而不是在另一個30秒（如同在預設設定中）嘗試多次重新連線嘗試，再次從預設行為分歧。</span><span class="sxs-lookup"><span data-stu-id="a65a2-205">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="a65a2-206">如果您想要更充分掌控自動重新連線嘗試的時間和數目，請 `withAutomaticReconnect` 接受執行介面的物件 `IRetryPolicy` ，該介面具有名為的單一方法 `nextRetryDelayInMilliseconds` 。</span><span class="sxs-lookup"><span data-stu-id="a65a2-206">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="a65a2-207">`nextRetryDelayInMilliseconds` 採用具有類型的單一引數 `RetryContext` 。</span><span class="sxs-lookup"><span data-stu-id="a65a2-207">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="a65a2-208">`RetryContext`有三個屬性： `previousRetryCount` 、 `elapsedMilliseconds` 和 `retryReason` `number` 分別為、 `number` 和 `Error` 。</span><span class="sxs-lookup"><span data-stu-id="a65a2-208">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="a65a2-209">在第一次重新連線嘗試之前， `previousRetryCount` 和都 `elapsedMilliseconds` 是零，而且 `retryReason` 將會是導致中斷連接的錯誤。</span><span class="sxs-lookup"><span data-stu-id="a65a2-209">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="a65a2-210">每次重試失敗後，將會 `previousRetryCount` 遞增一次， `elapsedMilliseconds` 將會更新以反映到目前為止所花的時間（以毫秒為單位），而且 `retryReason` 會是造成上次重新連線嘗試失敗的錯誤。</span><span class="sxs-lookup"><span data-stu-id="a65a2-210">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="a65a2-211">`nextRetryDelayInMilliseconds` 必須傳回一個數位，代表下一次重新連接嘗試之前要等候的毫秒數，或 `null` 是否 `HubConnection` 應該停止重新連接。</span><span class="sxs-lookup"><span data-stu-id="a65a2-211">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

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

<span data-ttu-id="a65a2-212">或者，您也可以撰寫程式碼，以手動方式重新連接用戶端，如 [手動重新](#manually-reconnect)連線中所示範。</span><span class="sxs-lookup"><span data-stu-id="a65a2-212">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

### <a name="manually-reconnect"></a><span data-ttu-id="a65a2-213">手動重新連線</span><span class="sxs-lookup"><span data-stu-id="a65a2-213">Manually reconnect</span></span>

<span data-ttu-id="a65a2-214">下列程式碼示範一般手動重新連接方法：</span><span class="sxs-lookup"><span data-stu-id="a65a2-214">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="a65a2-215">函式 (在此案例中， `start` 會建立函式) 以開始連接。</span><span class="sxs-lookup"><span data-stu-id="a65a2-215">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="a65a2-216">`start`在連接的事件處理常式中呼叫函式 `onclose` 。</span><span class="sxs-lookup"><span data-stu-id="a65a2-216">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[](javascript-client/samples/3.x/:::no-loc(SignalR):::Chat/wwwroot/chat.js?range=30-40)]

<span data-ttu-id="a65a2-217">實際執行會使用指數輪詢，或在放棄之前重試指定的次數。</span><span class="sxs-lookup"><span data-stu-id="a65a2-217">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a65a2-218">其他資源</span><span class="sxs-lookup"><span data-stu-id="a65a2-218">Additional resources</span></span>

* [<span data-ttu-id="a65a2-219">JavaScript API 參考</span><span class="sxs-lookup"><span data-stu-id="a65a2-219">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest&preserve-view=true )
* [<span data-ttu-id="a65a2-220">JavaScript 教學課程</span><span class="sxs-lookup"><span data-stu-id="a65a2-220">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="a65a2-221">WebPack 和 TypeScript 教學課程</span><span class="sxs-lookup"><span data-stu-id="a65a2-221">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="a65a2-222">中樞</span><span class="sxs-lookup"><span data-stu-id="a65a2-222">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="a65a2-223">.NET 用戶端</span><span class="sxs-lookup"><span data-stu-id="a65a2-223">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="a65a2-224">發佈至 Azure</span><span class="sxs-lookup"><span data-stu-id="a65a2-224">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="a65a2-225"> (CORS) 的跨原始來源要求 </span><span class="sxs-lookup"><span data-stu-id="a65a2-225">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="a65a2-226">Azure :::no-loc(SignalR)::: 服務無伺服器檔</span><span class="sxs-lookup"><span data-stu-id="a65a2-226">Azure :::no-loc(SignalR)::: Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)
* [<span data-ttu-id="a65a2-227">針對連線問題進行疑難排解</span><span class="sxs-lookup"><span data-stu-id="a65a2-227">Troubleshoot connection errors</span></span>](xref:signalr/troubleshoot)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a65a2-228">作者：[Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="a65a2-228">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="a65a2-229">ASP.NET Core :::no-loc(SignalR)::: JavaScript 用戶端程式庫可讓開發人員呼叫伺服器端中樞程式碼。</span><span class="sxs-lookup"><span data-stu-id="a65a2-229">The ASP.NET Core :::no-loc(SignalR)::: JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="a65a2-230">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="a65a2-230">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-client-package"></a><span data-ttu-id="a65a2-231">安裝 :::no-loc(SignalR)::: 用戶端套件</span><span class="sxs-lookup"><span data-stu-id="a65a2-231">Install the :::no-loc(SignalR)::: client package</span></span>

<span data-ttu-id="a65a2-232">:::no-loc(SignalR):::JavaScript 用戶端程式庫會以[npm](https://www.npmjs.com/)套件的形式傳遞。</span><span class="sxs-lookup"><span data-stu-id="a65a2-232">The :::no-loc(SignalR)::: JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="a65a2-233">下列各節概述安裝用戶端程式庫的不同方式。</span><span class="sxs-lookup"><span data-stu-id="a65a2-233">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="a65a2-234">使用 npm 安裝</span><span class="sxs-lookup"><span data-stu-id="a65a2-234">Install with npm</span></span>

<span data-ttu-id="a65a2-235">如果使用 Visual Studio，請在根資料夾中從 **封裝管理員主控台** 執行下列命令。</span><span class="sxs-lookup"><span data-stu-id="a65a2-235">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="a65a2-236">針對 Visual Studio Code，請從 **整合式終端** 機執行下列命令。</span><span class="sxs-lookup"><span data-stu-id="a65a2-236">For Visual Studio Code, run the following commands from the **Integrated Terminal** .</span></span>

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="a65a2-237">npm 會將套件內容安裝 *node_modules \\ @aspnet\signalr\dist\browser* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="a65a2-237">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="a65a2-238">在 *wwwroot \\ lib* 資料夾下建立名為 *signalr* 的新資料夾。</span><span class="sxs-lookup"><span data-stu-id="a65a2-238">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="a65a2-239">將 *signalr.js* 檔案複製到 *wwwroot\lib\signalr* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="a65a2-239">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

<span data-ttu-id="a65a2-240">參考 :::no-loc(SignalR)::: 元素中的 JavaScript 用戶端 `<script>` 。</span><span class="sxs-lookup"><span data-stu-id="a65a2-240">Reference the :::no-loc(SignalR)::: JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="a65a2-241">例如：</span><span class="sxs-lookup"><span data-stu-id="a65a2-241">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="a65a2-242">使用 (CDN) 的內容傳遞網路</span><span class="sxs-lookup"><span data-stu-id="a65a2-242">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="a65a2-243">若要在沒有 npm 必要條件的情況下使用用戶端程式庫，請參考用戶端程式庫的 CDN 託管複本。</span><span class="sxs-lookup"><span data-stu-id="a65a2-243">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="a65a2-244">例如：</span><span class="sxs-lookup"><span data-stu-id="a65a2-244">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="a65a2-245">用戶端程式庫可在下列 Cdn 上取得：</span><span class="sxs-lookup"><span data-stu-id="a65a2-245">The client library is available on the following CDNs:</span></span>

* [<span data-ttu-id="a65a2-246">cdnjs</span><span class="sxs-lookup"><span data-stu-id="a65a2-246">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="a65a2-247">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="a65a2-247">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="a65a2-248">unpkg</span><span class="sxs-lookup"><span data-stu-id="a65a2-248">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a><span data-ttu-id="a65a2-249">使用 LibMan 安裝</span><span class="sxs-lookup"><span data-stu-id="a65a2-249">Install with LibMan</span></span>

<span data-ttu-id="a65a2-250">[LibMan](xref:client-side/libman/index) 可以用來從 CDN 裝載的用戶端程式庫安裝特定的用戶端程式庫檔案。</span><span class="sxs-lookup"><span data-stu-id="a65a2-250">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="a65a2-251">例如，只將縮減 JavaScript 檔案新增至專案。</span><span class="sxs-lookup"><span data-stu-id="a65a2-251">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="a65a2-252">如需該方法的詳細資訊，請參閱 [新增 :::no-loc(SignalR)::: 用戶端程式庫](xref:tutorials/signalr#add-the-signalr-client-library)。</span><span class="sxs-lookup"><span data-stu-id="a65a2-252">For details on that approach, see [Add the :::no-loc(SignalR)::: client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="a65a2-253">連接至中樞</span><span class="sxs-lookup"><span data-stu-id="a65a2-253">Connect to a hub</span></span>

<span data-ttu-id="a65a2-254">下列程式碼會建立並啟動連接。</span><span class="sxs-lookup"><span data-stu-id="a65a2-254">The following code creates and starts a connection.</span></span> <span data-ttu-id="a65a2-255">中樞的名稱不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="a65a2-255">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/samples/2.x/:::no-loc(SignalR):::Chat/wwwroot/js/chat.js?range=9-13,28-51)]

### <a name="cross-origin-connections"></a><span data-ttu-id="a65a2-256">跨原始連接</span><span class="sxs-lookup"><span data-stu-id="a65a2-256">Cross-origin connections</span></span>

<span data-ttu-id="a65a2-257">一般而言，瀏覽器會從與要求的頁面相同的網域載入連接。</span><span class="sxs-lookup"><span data-stu-id="a65a2-257">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="a65a2-258">不過，在某些情況下需要連接到另一個網域。</span><span class="sxs-lookup"><span data-stu-id="a65a2-258">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="a65a2-259">為了防止惡意網站從另一個網站讀取敏感性資料，預設會停用 [跨原始來源的連接](xref:security/cors) 。</span><span class="sxs-lookup"><span data-stu-id="a65a2-259">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="a65a2-260">若要允許跨原始來源的要求，請在類別中加以啟用 `Startup` 。</span><span class="sxs-lookup"><span data-stu-id="a65a2-260">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/samples/2.x/:::no-loc(SignalR):::Chat/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="a65a2-261">從用戶端呼叫中樞方法</span><span class="sxs-lookup"><span data-stu-id="a65a2-261">Call hub methods from client</span></span>

<span data-ttu-id="a65a2-262">JavaScript 用戶端會透過[HubConnection](/javascript/api/%40aspnet/signalr/hubconnection)的[invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke)方法，呼叫中樞上的公用方法。</span><span class="sxs-lookup"><span data-stu-id="a65a2-262">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="a65a2-263">`invoke`方法會接受兩個引數：</span><span class="sxs-lookup"><span data-stu-id="a65a2-263">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="a65a2-264">中樞方法的名稱。</span><span class="sxs-lookup"><span data-stu-id="a65a2-264">The name of the hub method.</span></span> <span data-ttu-id="a65a2-265">在下列範例中，中樞上的方法名稱為 `SendMessage` 。</span><span class="sxs-lookup"><span data-stu-id="a65a2-265">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="a65a2-266">中樞方法中定義的任何引數。</span><span class="sxs-lookup"><span data-stu-id="a65a2-266">Any arguments defined in the hub method.</span></span> <span data-ttu-id="a65a2-267">在下列範例中，引數名稱為 `message` 。</span><span class="sxs-lookup"><span data-stu-id="a65a2-267">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="a65a2-268">範例程式碼會使用箭號函式語法，此語法在所有主要瀏覽器的目前版本中受到支援，但 Internet Explorer 除外。</span><span class="sxs-lookup"><span data-stu-id="a65a2-268">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/samples/2.x/:::no-loc(SignalR):::Chat/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="a65a2-269">只有 :::no-loc(SignalR)::: 在 *預設* 模式下使用 Azure 服務時，才支援從用戶端呼叫中樞方法。</span><span class="sxs-lookup"><span data-stu-id="a65a2-269">Calling hub methods from a client is only supported when using the Azure :::no-loc(SignalR)::: Service in *Default* mode.</span></span> <span data-ttu-id="a65a2-270">如需詳細資訊，請參閱 [ (azure Signalr GitHub 存放庫) ](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose)的常見問題。</span><span class="sxs-lookup"><span data-stu-id="a65a2-270">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

<span data-ttu-id="a65a2-271">方法會傳回 `invoke` JavaScript [承諾](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)。</span><span class="sxs-lookup"><span data-stu-id="a65a2-271">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="a65a2-272">`Promise`當伺服器上的方法傳回時，會使用傳回值來解析 (是否有任何) 。</span><span class="sxs-lookup"><span data-stu-id="a65a2-272">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="a65a2-273">如果伺服器上的方法擲回錯誤，則 `Promise` 會被拒絕，並顯示錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="a65a2-273">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="a65a2-274">您 `then` 可以使用本身的和 `catch` 方法 `Promise` ， (或語法) 來處理這些情況 `await` 。</span><span class="sxs-lookup"><span data-stu-id="a65a2-274">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="a65a2-275">方法會傳回 `send` JavaScript `Promise` 。</span><span class="sxs-lookup"><span data-stu-id="a65a2-275">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="a65a2-276">`Promise`當訊息傳送到伺服器時，會解析。</span><span class="sxs-lookup"><span data-stu-id="a65a2-276">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="a65a2-277">如果傳送訊息時發生錯誤， `Promise` 就會拒絕，並顯示錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="a65a2-277">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="a65a2-278">您 `then` 可以使用本身的和 `catch` 方法 `Promise` ， (或語法) 來處理這些情況 `await` 。</span><span class="sxs-lookup"><span data-stu-id="a65a2-278">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="a65a2-279">使用 `send` 並不會等到伺服器收到訊息為止。</span><span class="sxs-lookup"><span data-stu-id="a65a2-279">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="a65a2-280">因此，不可能從伺服器傳回資料或錯誤。</span><span class="sxs-lookup"><span data-stu-id="a65a2-280">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="a65a2-281">從中樞呼叫用戶端方法</span><span class="sxs-lookup"><span data-stu-id="a65a2-281">Call client methods from hub</span></span>

<span data-ttu-id="a65a2-282">若要從中樞接收訊息，請使用的 [on](/javascript/api/%40aspnet/signalr/hubconnection#on) 方法定義方法 `HubConnection` 。</span><span class="sxs-lookup"><span data-stu-id="a65a2-282">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="a65a2-283">JavaScript 用戶端方法的名稱。</span><span class="sxs-lookup"><span data-stu-id="a65a2-283">The name of the JavaScript client method.</span></span> <span data-ttu-id="a65a2-284">在下列範例中，方法名稱為 `ReceiveMessage` 。</span><span class="sxs-lookup"><span data-stu-id="a65a2-284">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="a65a2-285">中樞傳遞給方法的引數。</span><span class="sxs-lookup"><span data-stu-id="a65a2-285">Arguments the hub passes to the method.</span></span> <span data-ttu-id="a65a2-286">在下列範例中，引數值為 `message` 。</span><span class="sxs-lookup"><span data-stu-id="a65a2-286">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/samples/2.x/:::no-loc(SignalR):::Chat/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="a65a2-287">中的上述程式碼 `connection.on` 會在伺服器端程式碼使用方法呼叫它時執行 <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.ClientProxyExtensions.SendAsync%2A> 。</span><span class="sxs-lookup"><span data-stu-id="a65a2-287">The preceding code in `connection.on` runs when server-side code calls it using the <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.ClientProxyExtensions.SendAsync%2A> method.</span></span>

[!code-csharp[Call client-side](javascript-client/samples/2.x/:::no-loc(SignalR):::Chat/hubs/chathub.cs?range=8-11)]

<span data-ttu-id="a65a2-288">:::no-loc(SignalR)::: 藉由比對和中定義的方法名稱和引數，判斷要呼叫的用戶端方法 `SendAsync` `connection.on` 。</span><span class="sxs-lookup"><span data-stu-id="a65a2-288">:::no-loc(SignalR)::: determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="a65a2-289">最佳做法是在之後呼叫 [start](/javascript/api/%40aspnet/signalr/hubconnection#start) 方法 `HubConnection` `on` 。</span><span class="sxs-lookup"><span data-stu-id="a65a2-289">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="a65a2-290">這麼做可確保您的處理常式會在收到任何訊息之前註冊。</span><span class="sxs-lookup"><span data-stu-id="a65a2-290">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="a65a2-291">錯誤處理和記錄</span><span class="sxs-lookup"><span data-stu-id="a65a2-291">Error handling and logging</span></span>

<span data-ttu-id="a65a2-292">將 `catch` 方法連結至方法的結尾 `start` ，以處理用戶端錯誤。</span><span class="sxs-lookup"><span data-stu-id="a65a2-292">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="a65a2-293">用 `console.error` 來將錯誤輸出至瀏覽器的主控台。</span><span class="sxs-lookup"><span data-stu-id="a65a2-293">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/samples/2.x/:::no-loc(SignalR):::Chat/wwwroot/js/chat.js?range=50)]

<span data-ttu-id="a65a2-294">設定用戶端記錄檔追蹤，方法是在建立連接時，傳遞記錄器和事件種類來記錄。</span><span class="sxs-lookup"><span data-stu-id="a65a2-294">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="a65a2-295">訊息會以指定的記錄層級和更新版本記錄。</span><span class="sxs-lookup"><span data-stu-id="a65a2-295">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="a65a2-296">可用的記錄層級如下所示：</span><span class="sxs-lookup"><span data-stu-id="a65a2-296">Available log levels are as follows:</span></span>

* <span data-ttu-id="a65a2-297">`signalR.LogLevel.Error`：錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="a65a2-297">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="a65a2-298">`Error`只記錄訊息。</span><span class="sxs-lookup"><span data-stu-id="a65a2-298">Logs `Error` messages only.</span></span>
* <span data-ttu-id="a65a2-299">`signalR.LogLevel.Warning`：可能發生錯誤的警告訊息。</span><span class="sxs-lookup"><span data-stu-id="a65a2-299">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="a65a2-300">記錄檔 `Warning` 和 `Error` 訊息。</span><span class="sxs-lookup"><span data-stu-id="a65a2-300">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="a65a2-301">`signalR.LogLevel.Information`：沒有錯誤的狀態訊息。</span><span class="sxs-lookup"><span data-stu-id="a65a2-301">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="a65a2-302">記錄 `Information` 、 `Warning` 和 `Error` 訊息。</span><span class="sxs-lookup"><span data-stu-id="a65a2-302">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="a65a2-303">`signalR.LogLevel.Trace`：追蹤訊息。</span><span class="sxs-lookup"><span data-stu-id="a65a2-303">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="a65a2-304">記錄所有內容，包括中樞與用戶端之間傳輸的資料。</span><span class="sxs-lookup"><span data-stu-id="a65a2-304">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="a65a2-305">在[HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)上使用[configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging)方法來設定記錄層級。</span><span class="sxs-lookup"><span data-stu-id="a65a2-305">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="a65a2-306">訊息會記錄到瀏覽器主控台。</span><span class="sxs-lookup"><span data-stu-id="a65a2-306">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/samples/2.x/:::no-loc(SignalR):::Chat/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="a65a2-307">重新連線用戶端</span><span class="sxs-lookup"><span data-stu-id="a65a2-307">Reconnect clients</span></span>

### <a name="manually-reconnect"></a><span data-ttu-id="a65a2-308">手動重新連線</span><span class="sxs-lookup"><span data-stu-id="a65a2-308">Manually reconnect</span></span>

> [!WARNING]
> <span data-ttu-id="a65a2-309">在3.0 之前，的 JavaScript 用戶端 :::no-loc(SignalR)::: 不會自動重新連線。</span><span class="sxs-lookup"><span data-stu-id="a65a2-309">Prior to 3.0, the JavaScript client for :::no-loc(SignalR)::: doesn't automatically reconnect.</span></span> <span data-ttu-id="a65a2-310">您必須撰寫會以手動方式重新連接用戶端的程式碼。</span><span class="sxs-lookup"><span data-stu-id="a65a2-310">You must write code that will reconnect your client manually.</span></span>

<span data-ttu-id="a65a2-311">下列程式碼示範一般手動重新連接方法：</span><span class="sxs-lookup"><span data-stu-id="a65a2-311">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="a65a2-312">函式 (在此案例中， `start` 會建立函式) 以開始連接。</span><span class="sxs-lookup"><span data-stu-id="a65a2-312">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="a65a2-313">`start`在連接的事件處理常式中呼叫函式 `onclose` 。</span><span class="sxs-lookup"><span data-stu-id="a65a2-313">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/samples/2.x/:::no-loc(SignalR):::Chat/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="a65a2-314">實際執行會使用指數輪詢，或在放棄之前重試指定的次數。</span><span class="sxs-lookup"><span data-stu-id="a65a2-314">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a65a2-315">其他資源</span><span class="sxs-lookup"><span data-stu-id="a65a2-315">Additional resources</span></span>

* [<span data-ttu-id="a65a2-316">JavaScript API 參考</span><span class="sxs-lookup"><span data-stu-id="a65a2-316">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="a65a2-317">JavaScript 教學課程</span><span class="sxs-lookup"><span data-stu-id="a65a2-317">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="a65a2-318">WebPack 和 TypeScript 教學課程</span><span class="sxs-lookup"><span data-stu-id="a65a2-318">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="a65a2-319">中樞</span><span class="sxs-lookup"><span data-stu-id="a65a2-319">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="a65a2-320">.NET 用戶端</span><span class="sxs-lookup"><span data-stu-id="a65a2-320">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="a65a2-321">發佈至 Azure</span><span class="sxs-lookup"><span data-stu-id="a65a2-321">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="a65a2-322"> (CORS) 的跨原始來源要求 </span><span class="sxs-lookup"><span data-stu-id="a65a2-322">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="a65a2-323">Azure :::no-loc(SignalR)::: 服務無伺服器檔</span><span class="sxs-lookup"><span data-stu-id="a65a2-323">Azure :::no-loc(SignalR)::: Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)

::: moniker-end
