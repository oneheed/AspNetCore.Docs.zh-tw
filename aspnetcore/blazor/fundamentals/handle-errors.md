---
title: 處理 ASP.NET Core 應用程式中的錯誤 Blazor
author: guardrex
description: 探索 ASP.NET Core 如何 Blazor Blazor 管理未處理的例外狀況，以及如何開發偵測和處理錯誤的應用程式。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
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
uid: blazor/fundamentals/handle-errors
ms.openlocfilehash: f7cac477e2c5bca54e24ae3faeadff9b51bdcd0f
ms.sourcegitcommit: 422e8444b9f5cedc373be5efe8032822db54fcaf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/18/2021
ms.locfileid: "101101057"
---
# <a name="handle-errors-in-aspnet-core-blazor-apps"></a><span data-ttu-id="20f1a-103">處理 ASP.NET Core 應用程式中的錯誤 Blazor</span><span class="sxs-lookup"><span data-stu-id="20f1a-103">Handle errors in ASP.NET Core Blazor apps</span></span>

<span data-ttu-id="20f1a-104">本文說明如何 Blazor 管理未處理的例外狀況，以及如何開發偵測和處理錯誤的應用程式。</span><span class="sxs-lookup"><span data-stu-id="20f1a-104">This article describes how Blazor manages unhandled exceptions and how to develop apps that detect and handle errors.</span></span>

## <a name="detailed-errors-during-development"></a><span data-ttu-id="20f1a-105">開發期間的詳細錯誤</span><span class="sxs-lookup"><span data-stu-id="20f1a-105">Detailed errors during development</span></span>

<span data-ttu-id="20f1a-106">當 Blazor 應用程式在開發期間無法正常運作時，從應用程式接收詳細的錯誤資訊有助於疑難排解和修正問題。</span><span class="sxs-lookup"><span data-stu-id="20f1a-106">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="20f1a-107">發生錯誤時， Blazor 應用程式會在畫面底部顯示金色橫條：</span><span class="sxs-lookup"><span data-stu-id="20f1a-107">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="20f1a-108">在開發期間，金線會將您導向瀏覽器主控台，您可以在其中查看例外狀況。</span><span class="sxs-lookup"><span data-stu-id="20f1a-108">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="20f1a-109">在生產環境中，金級橫條會通知使用者發生錯誤，建議您重新整理瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="20f1a-109">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="20f1a-110">此錯誤處理體驗的 UI 是專案範本的一部分 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="20f1a-110">The UI for this error handling experience is part of the Blazor project templates.</span></span>

<span data-ttu-id="20f1a-111">在 Blazor WebAssembly 應用程式中，自訂檔案的體驗 `wwwroot/index.html` ：</span><span class="sxs-lookup"><span data-stu-id="20f1a-111">In a Blazor WebAssembly app, customize the experience in the `wwwroot/index.html` file:</span></span>

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="20f1a-112">在 Blazor Server 應用程式中，自訂檔案的體驗 `Pages/_Host.cshtml` ：</span><span class="sxs-lookup"><span data-stu-id="20f1a-112">In a Blazor Server app, customize the experience in the `Pages/_Host.cshtml` file:</span></span>

```cshtml
<div id="blazor-error-ui">
    <environment include="Staging,Production">
        An error has occurred. This application may no longer respond until reloaded.
    </environment>
    <environment include="Development">
        An unhandled exception has occurred. See browser dev tools for details.
    </environment>
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="20f1a-113">專案 `blazor-error-ui` 會由範本中所包含的樣式隱藏 Blazor (`wwwroot/css/app.css` 或 `wwwroot/css/site.css`) ，然後在錯誤發生時顯示：</span><span class="sxs-lookup"><span data-stu-id="20f1a-113">The `blazor-error-ui` element is hidden by the styles included in the Blazor templates (`wwwroot/css/app.css` or `wwwroot/css/site.css`) and then shown when an error occurs:</span></span>

```css
#blazor-error-ui {
    background: lightyellow;
    bottom: 0;
    box-shadow: 0 -1px 2px rgba(0, 0, 0, 0.2);
    display: none;
    left: 0;
    padding: 0.6rem 1.25rem 0.7rem 1.25rem;
    position: fixed;
    width: 100%;
    z-index: 1000;
}

#blazor-error-ui .dismiss {
    cursor: pointer;
    position: absolute;
    right: 0.75rem;
    top: 0.5rem;
}
```

## <a name="blazor-server-detailed-circuit-errors"></a><span data-ttu-id="20f1a-114">Blazor Server 詳細線路錯誤</span><span class="sxs-lookup"><span data-stu-id="20f1a-114">Blazor Server detailed circuit errors</span></span>

<span data-ttu-id="20f1a-115">用戶端錯誤不包含呼叫堆疊，也不會提供錯誤原因的詳細資料，但伺服器記錄檔包含這類資訊。</span><span class="sxs-lookup"><span data-stu-id="20f1a-115">Client-side errors don't include the callstack and don't provide detail on the cause of the error, but server logs do contain such information.</span></span> <span data-ttu-id="20f1a-116">基於開發目的，可透過啟用詳細錯誤，讓用戶端可以使用敏感性電路錯誤資訊。</span><span class="sxs-lookup"><span data-stu-id="20f1a-116">For development purposes, sensitive circuit error information can be made available to the client by enabling detailed errors.</span></span>

<span data-ttu-id="20f1a-117">Blazor Server使用下列方法啟用詳細錯誤：</span><span class="sxs-lookup"><span data-stu-id="20f1a-117">Enable Blazor Server detailed errors using the following approaches:</span></span>

* <span data-ttu-id="20f1a-118"><xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="20f1a-118"><xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType>.</span></span>
* <span data-ttu-id="20f1a-119">`DetailedErrors`設定金鑰設定為 `true` ，可以在應用程式的開發設定檔案 () 中設定 `appsettings.Development.json` 。</span><span class="sxs-lookup"><span data-stu-id="20f1a-119">The `DetailedErrors` configuration key set to `true`, which can be set in the app's Development settings file (`appsettings.Development.json`).</span></span> <span data-ttu-id="20f1a-120">您也可以使用 `ASPNETCORE_DETAILEDERRORS` 具有值的環境變數來設定索引鍵 `true` 。</span><span class="sxs-lookup"><span data-stu-id="20f1a-120">The key can also be set using the `ASPNETCORE_DETAILEDERRORS` environment variable with a value of `true`.</span></span>
* <span data-ttu-id="20f1a-121">[ SignalR 伺服器端記錄](xref:signalr/diagnostics#server-side-logging) (`Microsoft.AspNetCore.SignalR`) 可以設定為[Debug](xref:Microsoft.Extensions.Logging.LogLevel)或[Trace](xref:Microsoft.Extensions.Logging.LogLevel)以進行詳細 SignalR 記錄。</span><span class="sxs-lookup"><span data-stu-id="20f1a-121">[SignalR server-side logging](xref:signalr/diagnostics#server-side-logging) (`Microsoft.AspNetCore.SignalR`) can be set to [Debug](xref:Microsoft.Extensions.Logging.LogLevel) or [Trace](xref:Microsoft.Extensions.Logging.LogLevel) for detailed SignalR logging.</span></span>

<span data-ttu-id="20f1a-122">`appsettings.Development.json`:</span><span class="sxs-lookup"><span data-stu-id="20f1a-122">`appsettings.Development.json`:</span></span>

```json
{
  "DetailedErrors": true,
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information",
      "Microsoft.AspNetCore.SignalR": "Debug"
    }
  }
}
```

> [!WARNING]
> <span data-ttu-id="20f1a-123">將錯誤資訊公開給網際網路上的用戶端，應該一律避免使用安全性風險。</span><span class="sxs-lookup"><span data-stu-id="20f1a-123">Exposing error information to clients on the Internet is a security risk that should always be avoided.</span></span>

## <a name="how-a-blazor-server-app-reacts-to-unhandled-exceptions"></a><span data-ttu-id="20f1a-124">Blazor Server應用程式如何回應未處理的例外狀況</span><span class="sxs-lookup"><span data-stu-id="20f1a-124">How a Blazor Server app reacts to unhandled exceptions</span></span>

<span data-ttu-id="20f1a-125">Blazor Server 是具狀態的架構。</span><span class="sxs-lookup"><span data-stu-id="20f1a-125">Blazor Server is a stateful framework.</span></span> <span data-ttu-id="20f1a-126">當使用者與應用程式互動時，它們會維持與伺服器的連線，稱為 *線路*。</span><span class="sxs-lookup"><span data-stu-id="20f1a-126">While users interact with an app, they maintain a connection to the server known as a *circuit*.</span></span> <span data-ttu-id="20f1a-127">線路會保存作用中的元件實例，再加上許多其他狀態的層面，例如：</span><span class="sxs-lookup"><span data-stu-id="20f1a-127">The circuit holds active component instances, plus many other aspects of state, such as:</span></span>

* <span data-ttu-id="20f1a-128">元件的最新轉譯輸出。</span><span class="sxs-lookup"><span data-stu-id="20f1a-128">The most recent rendered output of components.</span></span>
* <span data-ttu-id="20f1a-129">可由用戶端事件觸發的目前事件處理委派集。</span><span class="sxs-lookup"><span data-stu-id="20f1a-129">The current set of event-handling delegates that could be triggered by client-side events.</span></span>

<span data-ttu-id="20f1a-130">如果使用者在多個瀏覽器索引標籤中開啟應用程式，則會有多個獨立線路。</span><span class="sxs-lookup"><span data-stu-id="20f1a-130">If a user opens the app in multiple browser tabs, they have multiple independent circuits.</span></span>

<span data-ttu-id="20f1a-131">Blazor 將大部分未處理的例外狀況視為嚴重的重大例外狀況。</span><span class="sxs-lookup"><span data-stu-id="20f1a-131">Blazor treats most unhandled exceptions as fatal to the circuit where they occur.</span></span> <span data-ttu-id="20f1a-132">如果線路因為未處理的例外狀況而終止，則使用者只能透過重載頁面來建立新的線路，以繼續與應用程式互動。</span><span class="sxs-lookup"><span data-stu-id="20f1a-132">If a circuit is terminated due to an unhandled exception, the user can only continue to interact with the app by reloading the page to create a new circuit.</span></span> <span data-ttu-id="20f1a-133">終止的線路以外的線路（其他使用者或其他瀏覽器索引標籤的線路）不受影響。</span><span class="sxs-lookup"><span data-stu-id="20f1a-133">Circuits outside of the one that's terminated, which are circuits for other users or other browser tabs, aren't affected.</span></span> <span data-ttu-id="20f1a-134">此案例類似于損毀的桌面應用程式。</span><span class="sxs-lookup"><span data-stu-id="20f1a-134">This scenario is similar to a desktop app that crashes.</span></span> <span data-ttu-id="20f1a-135">損毀的應用程式必須重新開機，但不會影響其他應用程式。</span><span class="sxs-lookup"><span data-stu-id="20f1a-135">The crashed app must be restarted, but other apps aren't affected.</span></span>

<span data-ttu-id="20f1a-136">當發生未處理的例外狀況時，會終止電路，原因如下：</span><span class="sxs-lookup"><span data-stu-id="20f1a-136">A circuit is terminated when an unhandled exception occurs for the following reasons:</span></span>

* <span data-ttu-id="20f1a-137">未處理的例外狀況通常會讓電路保持未定義狀態。</span><span class="sxs-lookup"><span data-stu-id="20f1a-137">An unhandled exception often leaves the circuit in an undefined state.</span></span>
* <span data-ttu-id="20f1a-138">無法在未處理的例外狀況之後保證應用程式的正常操作。</span><span class="sxs-lookup"><span data-stu-id="20f1a-138">The app's normal operation can't be guaranteed after an unhandled exception.</span></span>
* <span data-ttu-id="20f1a-139">如果線路持續發生，則應用程式中可能會出現安全性弱點。</span><span class="sxs-lookup"><span data-stu-id="20f1a-139">Security vulnerabilities may appear in the app if the circuit continues.</span></span>

## <a name="manage-unhandled-exceptions-in-developer-code"></a><span data-ttu-id="20f1a-140">管理開發人員程式碼中未處理的例外狀況</span><span class="sxs-lookup"><span data-stu-id="20f1a-140">Manage unhandled exceptions in developer code</span></span>

<span data-ttu-id="20f1a-141">若要讓應用程式在發生錯誤之後繼續，應用程式必須有錯誤處理邏輯。</span><span class="sxs-lookup"><span data-stu-id="20f1a-141">For an app to continue after an error, the app must have error handling logic.</span></span> <span data-ttu-id="20f1a-142">本文稍後的章節將說明可能的未處理例外狀況來源。</span><span class="sxs-lookup"><span data-stu-id="20f1a-142">Later sections of this article describe potential sources of unhandled exceptions.</span></span>

<span data-ttu-id="20f1a-143">在生產環境中，請勿在 UI 中呈現架構例外狀況訊息或堆疊追蹤。</span><span class="sxs-lookup"><span data-stu-id="20f1a-143">In production, don't render framework exception messages or stack traces in the UI.</span></span> <span data-ttu-id="20f1a-144">轉譯例外狀況訊息或堆疊追蹤可能：</span><span class="sxs-lookup"><span data-stu-id="20f1a-144">Rendering exception messages or stack traces could:</span></span>

* <span data-ttu-id="20f1a-145">將機密資訊洩漏給終端使用者。</span><span class="sxs-lookup"><span data-stu-id="20f1a-145">Disclose sensitive information to end users.</span></span>
* <span data-ttu-id="20f1a-146">協助惡意使用者探索應用程式中可能會危害應用程式、伺服器或網路安全性的弱點。</span><span class="sxs-lookup"><span data-stu-id="20f1a-146">Help a malicious user discover weaknesses in an app that can compromise the security of the app, server, or network.</span></span>

## <a name="log-errors-with-a-persistent-provider"></a><span data-ttu-id="20f1a-147">使用持續性提供者記錄錯誤</span><span class="sxs-lookup"><span data-stu-id="20f1a-147">Log errors with a persistent provider</span></span>

<span data-ttu-id="20f1a-148">如果發生未處理的例外狀況，則會將例外狀況記錄到 <xref:Microsoft.Extensions.Logging.ILogger> 服務容器中設定的實例。</span><span class="sxs-lookup"><span data-stu-id="20f1a-148">If an unhandled exception occurs, the exception is logged to <xref:Microsoft.Extensions.Logging.ILogger> instances configured in the service container.</span></span> <span data-ttu-id="20f1a-149">根據預設， Blazor 應用程式會使用主控台記錄提供者來記錄主控台輸出。</span><span class="sxs-lookup"><span data-stu-id="20f1a-149">By default, Blazor apps log to console output with the Console Logging Provider.</span></span> <span data-ttu-id="20f1a-150">請考慮使用記錄管理檔大小和記錄檔迴圈的提供者，記錄至更永久的位置。</span><span class="sxs-lookup"><span data-stu-id="20f1a-150">Consider logging to a more permanent location with a provider that manages log size and log rotation.</span></span> <span data-ttu-id="20f1a-151">如需詳細資訊，請參閱<xref:fundamentals/logging/index>。</span><span class="sxs-lookup"><span data-stu-id="20f1a-151">For more information, see <xref:fundamentals/logging/index>.</span></span>

<span data-ttu-id="20f1a-152">在開發期間， Blazor 通常會將例外狀況的完整詳細資料傳送至瀏覽器的主控台，以協助進行偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="20f1a-152">During development, Blazor usually sends the full details of exceptions to the browser's console to aid in debugging.</span></span> <span data-ttu-id="20f1a-153">在生產環境中，預設會停用瀏覽器主控台中的詳細錯誤，這表示不會將錯誤傳送到用戶端，但例外狀況的完整詳細資料仍會記錄在伺服器端。</span><span class="sxs-lookup"><span data-stu-id="20f1a-153">In production, detailed errors in the browser's console are disabled by default, which means that errors aren't sent to clients but the exception's full details are still logged server-side.</span></span> <span data-ttu-id="20f1a-154">如需詳細資訊，請參閱<xref:fundamentals/error-handling>。</span><span class="sxs-lookup"><span data-stu-id="20f1a-154">For more information, see <xref:fundamentals/error-handling>.</span></span>

<span data-ttu-id="20f1a-155">您必須決定要記錄哪些事件，以及記錄事件的嚴重性層級。</span><span class="sxs-lookup"><span data-stu-id="20f1a-155">You must decide which incidents to log and the level of severity of logged incidents.</span></span> <span data-ttu-id="20f1a-156">惡意使用者可能可以刻意觸發錯誤。</span><span class="sxs-lookup"><span data-stu-id="20f1a-156">Hostile users might be able to trigger errors deliberately.</span></span> <span data-ttu-id="20f1a-157">例如，請勿從 `ProductId` 顯示產品詳細資料的元件 URL 中提供未知的錯誤記錄事件。</span><span class="sxs-lookup"><span data-stu-id="20f1a-157">For example, don't log an incident from an error where an unknown `ProductId` is supplied in the URL of a component that displays product details.</span></span> <span data-ttu-id="20f1a-158">並非所有錯誤都應該視為記錄的高嚴重性事件。</span><span class="sxs-lookup"><span data-stu-id="20f1a-158">Not all errors should be treated as high-severity incidents for logging.</span></span>

<span data-ttu-id="20f1a-159">如需詳細資訊，請參閱<xref:blazor/fundamentals/logging>。</span><span class="sxs-lookup"><span data-stu-id="20f1a-159">For more information, see <xref:blazor/fundamentals/logging>.</span></span>

## <a name="places-where-errors-may-occur"></a><span data-ttu-id="20f1a-160">可能發生錯誤的位置</span><span class="sxs-lookup"><span data-stu-id="20f1a-160">Places where errors may occur</span></span>

<span data-ttu-id="20f1a-161">架構和應用程式程式碼可能會在下列任何位置觸發未處理的例外狀況：</span><span class="sxs-lookup"><span data-stu-id="20f1a-161">Framework and app code may trigger unhandled exceptions in any of the following locations:</span></span>

* [<span data-ttu-id="20f1a-162">元件具現化</span><span class="sxs-lookup"><span data-stu-id="20f1a-162">Component instantiation</span></span>](#component-instantiation)
* [<span data-ttu-id="20f1a-163">生命週期方法</span><span class="sxs-lookup"><span data-stu-id="20f1a-163">Lifecycle methods</span></span>](#lifecycle-methods)
* [<span data-ttu-id="20f1a-164">轉譯邏輯</span><span class="sxs-lookup"><span data-stu-id="20f1a-164">Rendering logic</span></span>](#rendering-logic)
* [<span data-ttu-id="20f1a-165">事件處理常式</span><span class="sxs-lookup"><span data-stu-id="20f1a-165">Event handlers</span></span>](#event-handlers)
* [<span data-ttu-id="20f1a-166">元件處置</span><span class="sxs-lookup"><span data-stu-id="20f1a-166">Component disposal</span></span>](#component-disposal)
* [<span data-ttu-id="20f1a-167">JavaScript Interop</span><span class="sxs-lookup"><span data-stu-id="20f1a-167">JavaScript interop</span></span>](#javascript-interop)
* [<span data-ttu-id="20f1a-168">Blazor Server 轉譯資料流程</span><span class="sxs-lookup"><span data-stu-id="20f1a-168">Blazor Server rerendering</span></span>](#blazor-server-prerendering)

<span data-ttu-id="20f1a-169">前述未處理的例外狀況將在本文的下列各節中說明。</span><span class="sxs-lookup"><span data-stu-id="20f1a-169">The preceding unhandled exceptions are described in the following sections of this article.</span></span>

### <a name="component-instantiation"></a><span data-ttu-id="20f1a-170">元件具現化</span><span class="sxs-lookup"><span data-stu-id="20f1a-170">Component instantiation</span></span>

<span data-ttu-id="20f1a-171">當 Blazor 建立元件的實例時：</span><span class="sxs-lookup"><span data-stu-id="20f1a-171">When Blazor creates an instance of a component:</span></span>

* <span data-ttu-id="20f1a-172">會叫用元件的函式。</span><span class="sxs-lookup"><span data-stu-id="20f1a-172">The component's constructor is invoked.</span></span>
* <span data-ttu-id="20f1a-173">系統會叫用透過指示詞 [`@inject`](xref:mvc/views/razor#inject) 或[ `[Inject]` 屬性](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component)提供給元件之函式之任何非 singleton DI 服務的函式。</span><span class="sxs-lookup"><span data-stu-id="20f1a-173">The constructors of any non-singleton DI services supplied to the component's constructor via the [`@inject`](xref:mvc/views/razor#inject) directive or the [`[Inject]` attribute](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component) are invoked.</span></span>

<span data-ttu-id="20f1a-174">Blazor Server當任何已執行的函式或任何屬性的 setter 擲回 `[Inject]` 未處理的例外狀況時，電路會失敗。</span><span class="sxs-lookup"><span data-stu-id="20f1a-174">A Blazor Server circuit fails when any executed constructor or a setter for any `[Inject]` property throws an unhandled exception.</span></span> <span data-ttu-id="20f1a-175">例外狀況是嚴重的，因為架構無法將元件具現化。</span><span class="sxs-lookup"><span data-stu-id="20f1a-175">The exception is fatal because the framework can't instantiate the component.</span></span> <span data-ttu-id="20f1a-176">如果函式邏輯可能會擲回例外狀況，則應用程式應該使用 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 具有錯誤處理和記錄的語句來攔截例外狀況。</span><span class="sxs-lookup"><span data-stu-id="20f1a-176">If constructor logic may throw exceptions, the app should trap the exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

### <a name="lifecycle-methods"></a><span data-ttu-id="20f1a-177">生命週期方法</span><span class="sxs-lookup"><span data-stu-id="20f1a-177">Lifecycle methods</span></span>

<span data-ttu-id="20f1a-178">在元件的存留期內，會叫用 Blazor 下列 [生命週期方法](xref:blazor/components/lifecycle)：</span><span class="sxs-lookup"><span data-stu-id="20f1a-178">During the lifetime of a component, Blazor invokes the following [lifecycle methods](xref:blazor/components/lifecycle):</span></span>

* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>

<span data-ttu-id="20f1a-179">如果任何生命週期方法會以同步或非同步方式擲回例外狀況，則例外狀況對電路而言是嚴重的 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="20f1a-179">If any lifecycle method throws an exception, synchronously or asynchronously, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="20f1a-180">若要讓元件處理生命週期方法中的錯誤，請新增錯誤處理邏輯。</span><span class="sxs-lookup"><span data-stu-id="20f1a-180">For components to deal with errors in lifecycle methods, add error handling logic.</span></span>

<span data-ttu-id="20f1a-181">在下列範例中，會 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> 呼叫方法來取得產品：</span><span class="sxs-lookup"><span data-stu-id="20f1a-181">In the following example where <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> calls a method to obtain a product:</span></span>

* <span data-ttu-id="20f1a-182">方法中擲回的例外狀況 `ProductRepository.GetProductByIdAsync` 是由 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 語句處理。</span><span class="sxs-lookup"><span data-stu-id="20f1a-182">An exception thrown in the `ProductRepository.GetProductByIdAsync` method is handled by a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement.</span></span>
* <span data-ttu-id="20f1a-183">`catch`執行區塊時：</span><span class="sxs-lookup"><span data-stu-id="20f1a-183">When the `catch` block is executed:</span></span>
  * <span data-ttu-id="20f1a-184">`loadFailed` 設定為 `true` ，用來向使用者顯示錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="20f1a-184">`loadFailed` is set to `true`, which is used to display an error message to the user.</span></span>
  * <span data-ttu-id="20f1a-185">會記錄錯誤。</span><span class="sxs-lookup"><span data-stu-id="20f1a-185">The error is logged.</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/handle-errors/ProductDetails.razor?name=snippet&highlight=11,27-39)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/handle-errors/ProductDetails.razor?name=snippet&highlight=11,27-39)]

::: moniker-end

### <a name="rendering-logic"></a><span data-ttu-id="20f1a-186">轉譯邏輯</span><span class="sxs-lookup"><span data-stu-id="20f1a-186">Rendering logic</span></span>

<span data-ttu-id="20f1a-187">元件檔中的宣告式標記 `.razor` 會編譯成名為的 c # 方法 <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> 。</span><span class="sxs-lookup"><span data-stu-id="20f1a-187">The declarative markup in a `.razor` component file is compiled into a C# method called <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A>.</span></span> <span data-ttu-id="20f1a-188">當元件呈現時，會 <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> 執行並建立描述所轉譯元件之元素、文字和子元件的資料結構。</span><span class="sxs-lookup"><span data-stu-id="20f1a-188">When a component renders, <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> executes and builds up a data structure describing the elements, text, and child components of the rendered component.</span></span>

<span data-ttu-id="20f1a-189">轉譯邏輯可能會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="20f1a-189">Rendering logic can throw an exception.</span></span> <span data-ttu-id="20f1a-190">當評估但為時，就會發生這種情況的範例 `@someObject.PropertyName` `@someObject` `null` 。</span><span class="sxs-lookup"><span data-stu-id="20f1a-190">An example of this scenario occurs when `@someObject.PropertyName` is evaluated but `@someObject` is `null`.</span></span> <span data-ttu-id="20f1a-191">轉譯邏輯擲回的未處理例外狀況對電路而言是嚴重的 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="20f1a-191">An unhandled exception thrown by rendering logic is fatal to a Blazor Server circuit.</span></span>

<span data-ttu-id="20f1a-192">若要避免轉譯邏輯中有 null 參考例外狀況，請 `null` 先檢查物件，再存取其成員。</span><span class="sxs-lookup"><span data-stu-id="20f1a-192">To prevent a null reference exception in rendering logic, check for a `null` object before accessing its members.</span></span> <span data-ttu-id="20f1a-193">在下列範例中， `person.Address` 如果是，則不會存取屬性 `person.Address` `null` ：</span><span class="sxs-lookup"><span data-stu-id="20f1a-193">In the following example, `person.Address` properties aren't accessed if `person.Address` is `null`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/handle-errors/PersonExample.razor?name=snippet&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/handle-errors/PersonExample.razor?name=snippet&highlight=1)]

::: moniker-end

<span data-ttu-id="20f1a-194">上述程式碼假設 `person` 不是 `null` 。</span><span class="sxs-lookup"><span data-stu-id="20f1a-194">The preceding code assumes that `person` isn't `null`.</span></span> <span data-ttu-id="20f1a-195">程式碼的結構通常會保證物件存在於轉譯元件時。</span><span class="sxs-lookup"><span data-stu-id="20f1a-195">Often, the structure of the code guarantees that an object exists at the time the component is rendered.</span></span> <span data-ttu-id="20f1a-196">在這些情況下，不需要檢查轉譯 `null` 邏輯。</span><span class="sxs-lookup"><span data-stu-id="20f1a-196">In those cases, it isn't necessary to check for `null` in rendering logic.</span></span> <span data-ttu-id="20f1a-197">在先前的範例中， `person` 可能保證存在，因為 `person` 會在元件具現化時建立。</span><span class="sxs-lookup"><span data-stu-id="20f1a-197">In the prior example, `person` might be guaranteed to exist because `person` is created when the component is instantiated.</span></span>

### <a name="event-handlers"></a><span data-ttu-id="20f1a-198">事件處理常式</span><span class="sxs-lookup"><span data-stu-id="20f1a-198">Event handlers</span></span>

<span data-ttu-id="20f1a-199">使用下列專案建立事件處理常式時，用戶端程式代碼會觸發 c # 程式碼的調用：</span><span class="sxs-lookup"><span data-stu-id="20f1a-199">Client-side code triggers invocations of C# code when event handlers are created using:</span></span>

* `@onclick`
* `@onchange`
* <span data-ttu-id="20f1a-200">其他 `@on...` 屬性</span><span class="sxs-lookup"><span data-stu-id="20f1a-200">Other `@on...` attributes</span></span>
* `@bind`

<span data-ttu-id="20f1a-201">在這些案例中，事件處理常式程式碼可能會擲回未處理的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="20f1a-201">Event handler code might throw an unhandled exception in these scenarios.</span></span>

<span data-ttu-id="20f1a-202">如果事件處理常式擲回未處理的例外狀況 (例如，資料庫查詢) 失敗，則例外狀況對電路而言是嚴重的 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="20f1a-202">If an event handler throws an unhandled exception (for example, a database query fails), the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="20f1a-203">如果應用程式呼叫可能因為外部原因而失敗的程式碼，請使用 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 具有錯誤處理和記錄的語句來攔截例外狀況。</span><span class="sxs-lookup"><span data-stu-id="20f1a-203">If the app calls code that could fail for external reasons, trap exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="20f1a-204">如果使用者程式碼未攔截並處理例外狀況，則架構會記錄例外狀況並終止電路。</span><span class="sxs-lookup"><span data-stu-id="20f1a-204">If user code doesn't trap and handle the exception, the framework logs the exception and terminates the circuit.</span></span>

### <a name="component-disposal"></a><span data-ttu-id="20f1a-205">元件處置</span><span class="sxs-lookup"><span data-stu-id="20f1a-205">Component disposal</span></span>

<span data-ttu-id="20f1a-206">您可以從 UI 中移除元件，例如，因為使用者已流覽至另一個頁面。</span><span class="sxs-lookup"><span data-stu-id="20f1a-206">A component may be removed from the UI, for example, because the user has navigated to another page.</span></span> <span data-ttu-id="20f1a-207">從 UI 中移除實作為的元件時 <xref:System.IDisposable?displayProperty=fullName> ，架構會呼叫元件的 <xref:System.IDisposable.Dispose%2A> 方法。</span><span class="sxs-lookup"><span data-stu-id="20f1a-207">When a component that implements <xref:System.IDisposable?displayProperty=fullName> is removed from the UI, the framework calls the component's <xref:System.IDisposable.Dispose%2A> method.</span></span>

<span data-ttu-id="20f1a-208">如果元件的方法擲回 `Dispose` 未處理的例外狀況，則例外狀況對線路而言是嚴重的 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="20f1a-208">If the component's `Dispose` method throws an unhandled exception, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="20f1a-209">如果處置邏輯可能會擲回例外狀況，則應用程式應該使用 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 具有錯誤處理和記錄的語句來攔截例外狀況。</span><span class="sxs-lookup"><span data-stu-id="20f1a-209">If disposal logic may throw exceptions, the app should trap the exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="20f1a-210">如需元件處置的詳細資訊，請參閱 <xref:blazor/components/lifecycle#component-disposal-with-idisposable> 。</span><span class="sxs-lookup"><span data-stu-id="20f1a-210">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

### <a name="javascript-interop"></a><span data-ttu-id="20f1a-211">JavaScript Interop</span><span class="sxs-lookup"><span data-stu-id="20f1a-211">JavaScript interop</span></span>

<span data-ttu-id="20f1a-212"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> 允許 .NET 程式碼在使用者的瀏覽器中對 JavaScript 執行時間進行非同步呼叫。</span><span class="sxs-lookup"><span data-stu-id="20f1a-212"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> allows .NET code to make asynchronous calls to the JavaScript runtime in the user's browser.</span></span>

<span data-ttu-id="20f1a-213">下列條件適用于的錯誤處理 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> ：</span><span class="sxs-lookup"><span data-stu-id="20f1a-213">The following conditions apply to error handling with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>:</span></span>

* <span data-ttu-id="20f1a-214">如果同步的呼叫 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 失敗，就會發生 .net 例外狀況。</span><span class="sxs-lookup"><span data-stu-id="20f1a-214">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails synchronously, a .NET exception occurs.</span></span> <span data-ttu-id="20f1a-215"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>例如，的呼叫可能會失敗，因為無法序列化提供的引數。</span><span class="sxs-lookup"><span data-stu-id="20f1a-215">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the supplied arguments can't be serialized.</span></span> <span data-ttu-id="20f1a-216">開發人員程式碼必須攔截例外狀況。</span><span class="sxs-lookup"><span data-stu-id="20f1a-216">Developer code must catch the exception.</span></span> <span data-ttu-id="20f1a-217">如果事件處理常式或元件生命週期方法中的應用程式程式碼未處理例外狀況，則產生的例外狀況對線路而言是嚴重的 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="20f1a-217">If app code in an event handler or component lifecycle method doesn't handle an exception, the resulting exception is fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="20f1a-218">如果 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 非同步呼叫失敗，則 .net <xref:System.Threading.Tasks.Task> 會失敗。</span><span class="sxs-lookup"><span data-stu-id="20f1a-218">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails asynchronously, the .NET <xref:System.Threading.Tasks.Task> fails.</span></span> <span data-ttu-id="20f1a-219"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>例如，的呼叫可能會失敗，因為 JavaScript 端程式碼會擲回例外狀況，或傳回 `Promise` 做為完成的 `rejected` 。</span><span class="sxs-lookup"><span data-stu-id="20f1a-219">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the JavaScript-side code throws an exception or returns a `Promise` that completed as `rejected`.</span></span> <span data-ttu-id="20f1a-220">開發人員程式碼必須攔截例外狀況。</span><span class="sxs-lookup"><span data-stu-id="20f1a-220">Developer code must catch the exception.</span></span> <span data-ttu-id="20f1a-221">如果使用 [`await`](/dotnet/csharp/language-reference/keywords/await) 運算子，請考慮使用錯誤處理和記錄，將方法呼叫包裝在 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 語句中。</span><span class="sxs-lookup"><span data-stu-id="20f1a-221">If using the [`await`](/dotnet/csharp/language-reference/keywords/await) operator, consider wrapping the method call in a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span> <span data-ttu-id="20f1a-222">否則，失敗的程式碼會導致未處理的例外狀況對線路而言是嚴重的 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="20f1a-222">Otherwise, the failing code results in an unhandled exception that's fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="20f1a-223">依預設，對的呼叫 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 必須在特定期間內完成，否則會呼叫超時。預設的超時時間是一分鐘。</span><span class="sxs-lookup"><span data-stu-id="20f1a-223">By default, calls to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> must complete within a certain period or else the call times out. The default timeout period is one minute.</span></span> <span data-ttu-id="20f1a-224">此超時會防止程式碼遺失網路連線或永遠不會傳回完成訊息的 JavaScript 程式碼。</span><span class="sxs-lookup"><span data-stu-id="20f1a-224">The timeout protects the code against a loss in network connectivity or JavaScript code that never sends back a completion message.</span></span> <span data-ttu-id="20f1a-225">如果呼叫超時，則產生的 <xref:System.Threading.Tasks> 會失敗，並出現 <xref:System.OperationCanceledException> 。</span><span class="sxs-lookup"><span data-stu-id="20f1a-225">If the call times out, the resulting <xref:System.Threading.Tasks> fails with an <xref:System.OperationCanceledException>.</span></span> <span data-ttu-id="20f1a-226">使用記錄來攔截和處理例外狀況。</span><span class="sxs-lookup"><span data-stu-id="20f1a-226">Trap and process the exception with logging.</span></span>

<span data-ttu-id="20f1a-227">同樣地，JavaScript 程式碼可能會起始對[ `[JSInvokable]` 屬性](xref:blazor/call-dotnet-from-javascript)所表示之 .net 方法的呼叫。</span><span class="sxs-lookup"><span data-stu-id="20f1a-227">Similarly, JavaScript code may initiate calls to .NET methods indicated by the [`[JSInvokable]` attribute](xref:blazor/call-dotnet-from-javascript).</span></span> <span data-ttu-id="20f1a-228">如果這些 .NET 方法擲回未處理的例外狀況：</span><span class="sxs-lookup"><span data-stu-id="20f1a-228">If these .NET methods throw an unhandled exception:</span></span>

* <span data-ttu-id="20f1a-229">例外狀況不會被視為線路的嚴重例外狀況 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="20f1a-229">The exception isn't treated as fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="20f1a-230">JavaScript 端 `Promise` 遭到拒絕。</span><span class="sxs-lookup"><span data-stu-id="20f1a-230">The JavaScript-side `Promise` is rejected.</span></span>

<span data-ttu-id="20f1a-231">您可以選擇在 .NET 端或方法呼叫的 JavaScript 端使用錯誤處理常式代碼。</span><span class="sxs-lookup"><span data-stu-id="20f1a-231">You have the option of using error handling code on either the .NET side or the JavaScript side of the method call.</span></span>

<span data-ttu-id="20f1a-232">如需詳細資訊，請參閱下列文章：</span><span class="sxs-lookup"><span data-stu-id="20f1a-232">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

### <a name="blazor-server-prerendering"></a><span data-ttu-id="20f1a-233">Blazor Server 預</span><span class="sxs-lookup"><span data-stu-id="20f1a-233">Blazor Server prerendering</span></span>

<span data-ttu-id="20f1a-234">Blazor 您可以使用 [元件標記](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) 協助程式來資源清單元件，以便在使用者的初始 HTTP 要求中傳回其轉譯的 HTML 標籤。</span><span class="sxs-lookup"><span data-stu-id="20f1a-234">Blazor components can be prerendered using the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) so that their rendered HTML markup is returned as part of the user's initial HTTP request.</span></span> <span data-ttu-id="20f1a-235">其運作方式如下：</span><span class="sxs-lookup"><span data-stu-id="20f1a-235">This works by:</span></span>

* <span data-ttu-id="20f1a-236">針對屬於相同頁面的所有資源清單元件建立新的線路。</span><span class="sxs-lookup"><span data-stu-id="20f1a-236">Creating a new circuit for all of the prerendered components that are part of the same page.</span></span>
* <span data-ttu-id="20f1a-237">產生初始 HTML。</span><span class="sxs-lookup"><span data-stu-id="20f1a-237">Generating the initial HTML.</span></span>
* <span data-ttu-id="20f1a-238">將線路視為 `disconnected` 使用者的瀏覽器會將 SignalR 連接重新建立回相同的伺服器。</span><span class="sxs-lookup"><span data-stu-id="20f1a-238">Treating the circuit as `disconnected` until the user's browser establishes a SignalR connection back to the same server.</span></span> <span data-ttu-id="20f1a-239">建立連線時，會繼續執行線路的互動，並且更新元件的 HTML 標籤。</span><span class="sxs-lookup"><span data-stu-id="20f1a-239">When the connection is established, interactivity on the circuit is resumed and the components' HTML markup is updated.</span></span>

<span data-ttu-id="20f1a-240">如果任何元件在轉譯期間擲回未處理的例外狀況，例如在生命週期方法或轉譯邏輯中：</span><span class="sxs-lookup"><span data-stu-id="20f1a-240">If any component throws an unhandled exception during prerendering, for example, during a lifecycle method or in rendering logic:</span></span>

* <span data-ttu-id="20f1a-241">線路的例外狀況是嚴重的。</span><span class="sxs-lookup"><span data-stu-id="20f1a-241">The exception is fatal to the circuit.</span></span>
* <span data-ttu-id="20f1a-242">例外狀況是從標記協助程式的呼叫堆疊中擲回 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> 。</span><span class="sxs-lookup"><span data-stu-id="20f1a-242">The exception is thrown up the call stack from the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper.</span></span> <span data-ttu-id="20f1a-243">因此，整個 HTTP 要求都會失敗，除非開發人員程式碼明確攔截到例外狀況。</span><span class="sxs-lookup"><span data-stu-id="20f1a-243">Therefore, the entire HTTP request fails unless the exception is explicitly caught by developer code.</span></span>

<span data-ttu-id="20f1a-244">在正常情況下，預先轉譯失敗時，繼續建立和轉譯元件並不合理，因為無法轉譯工作元件。</span><span class="sxs-lookup"><span data-stu-id="20f1a-244">Under normal circumstances when prerendering fails, continuing to build and render the component doesn't make sense because a working component can't be rendered.</span></span>

<span data-ttu-id="20f1a-245">若要容忍在預進行期間可能發生的錯誤，必須將錯誤處理邏輯放在可能會擲回例外狀況的元件內部。</span><span class="sxs-lookup"><span data-stu-id="20f1a-245">To tolerate errors that may occur during prerendering, error handling logic must be placed inside a component that may throw exceptions.</span></span> <span data-ttu-id="20f1a-246">使用 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 語句搭配錯誤處理和記錄。</span><span class="sxs-lookup"><span data-stu-id="20f1a-246">Use [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span> <span data-ttu-id="20f1a-247">您 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 可以將錯誤處理邏輯放在標記協助程式所呈現的元件中，而不是在語句中包裝標記協助程式 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> 。</span><span class="sxs-lookup"><span data-stu-id="20f1a-247">Instead of wrapping the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper in a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement, place error handling logic in the component rendered by the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper.</span></span>

## <a name="advanced-scenarios"></a><span data-ttu-id="20f1a-248">進階案例</span><span class="sxs-lookup"><span data-stu-id="20f1a-248">Advanced scenarios</span></span>

### <a name="recursive-rendering"></a><span data-ttu-id="20f1a-249">遞迴轉譯</span><span class="sxs-lookup"><span data-stu-id="20f1a-249">Recursive rendering</span></span>

<span data-ttu-id="20f1a-250">元件可以用遞迴方式進行嵌套。</span><span class="sxs-lookup"><span data-stu-id="20f1a-250">Components can be nested recursively.</span></span> <span data-ttu-id="20f1a-251">這適用于表示遞迴資料結構。</span><span class="sxs-lookup"><span data-stu-id="20f1a-251">This is useful for representing recursive data structures.</span></span> <span data-ttu-id="20f1a-252">例如， `TreeNode` 元件可以 `TreeNode` 針對每個節點的子系呈現更多元件。</span><span class="sxs-lookup"><span data-stu-id="20f1a-252">For example, a `TreeNode` component can render more `TreeNode` components for each of the node's children.</span></span>

<span data-ttu-id="20f1a-253">以遞迴方式呈現時，請避免產生無限遞迴的程式碼模式：</span><span class="sxs-lookup"><span data-stu-id="20f1a-253">When rendering recursively, avoid coding patterns that result in infinite recursion:</span></span>

* <span data-ttu-id="20f1a-254">不要以遞迴方式呈現包含迴圈的資料結構。</span><span class="sxs-lookup"><span data-stu-id="20f1a-254">Don't recursively render a data structure that contains a cycle.</span></span> <span data-ttu-id="20f1a-255">例如，不要轉譯其子系本身包含的樹狀節點。</span><span class="sxs-lookup"><span data-stu-id="20f1a-255">For example, don't render a tree node whose children includes itself.</span></span>
* <span data-ttu-id="20f1a-256">請勿建立包含迴圈的版面配置鏈。</span><span class="sxs-lookup"><span data-stu-id="20f1a-256">Don't create a chain of layouts that contain a cycle.</span></span> <span data-ttu-id="20f1a-257">例如，請勿建立版面配置本身的配置。</span><span class="sxs-lookup"><span data-stu-id="20f1a-257">For example, don't create a layout whose layout is itself.</span></span>
* <span data-ttu-id="20f1a-258">不允許終端使用者違反遞迴不變數 (規則) 透過惡意資料輸入或 JavaScript interop 呼叫。</span><span class="sxs-lookup"><span data-stu-id="20f1a-258">Don't allow an end user to violate recursion invariants (rules) through malicious data entry or JavaScript interop calls.</span></span>

<span data-ttu-id="20f1a-259">轉譯期間的無限迴圈：</span><span class="sxs-lookup"><span data-stu-id="20f1a-259">Infinite loops during rendering:</span></span>

* <span data-ttu-id="20f1a-260">導致轉譯程式永遠繼續。</span><span class="sxs-lookup"><span data-stu-id="20f1a-260">Causes the rendering process to continue forever.</span></span>
* <span data-ttu-id="20f1a-261">相當於建立未結束的迴圈。</span><span class="sxs-lookup"><span data-stu-id="20f1a-261">Is equivalent to creating an unterminated loop.</span></span>

<span data-ttu-id="20f1a-262">在這些情況下，受影響的 Blazor Server 線路會失敗，而且執行緒通常會嘗試：</span><span class="sxs-lookup"><span data-stu-id="20f1a-262">In these scenarios, an affected Blazor Server circuit fails, and the thread usually attempts to:</span></span>

* <span data-ttu-id="20f1a-263">無限期耗用作業系統所允許的 CPU 時間。</span><span class="sxs-lookup"><span data-stu-id="20f1a-263">Consume as much CPU time as permitted by the operating system, indefinitely.</span></span>
* <span data-ttu-id="20f1a-264">耗用無限數量的伺服器記憶體。</span><span class="sxs-lookup"><span data-stu-id="20f1a-264">Consume an unlimited amount of server memory.</span></span> <span data-ttu-id="20f1a-265">使用無限制的記憶體相當於未結束的迴圈在每次反復專案中將專案新增至集合的案例。</span><span class="sxs-lookup"><span data-stu-id="20f1a-265">Consuming unlimited memory is equivalent to the scenario where an unterminated loop adds entries to a collection on every iteration.</span></span>

<span data-ttu-id="20f1a-266">若要避免無限遞迴模式，請確定遞迴轉譯程式碼包含適當的停止條件。</span><span class="sxs-lookup"><span data-stu-id="20f1a-266">To avoid infinite recursion patterns, ensure that recursive rendering code contains suitable stopping conditions.</span></span>

### <a name="custom-render-tree-logic"></a><span data-ttu-id="20f1a-267">自訂呈現樹狀結構邏輯</span><span class="sxs-lookup"><span data-stu-id="20f1a-267">Custom render tree logic</span></span>

<span data-ttu-id="20f1a-268">大部分 Blazor 的元件會實作為檔案 `.razor` ，並進行編譯以產生可在上操作的邏輯， <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 以轉譯其輸出。</span><span class="sxs-lookup"><span data-stu-id="20f1a-268">Most Blazor components are implemented as `.razor` files and are compiled to produce logic that operates on a <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to render their output.</span></span> <span data-ttu-id="20f1a-269">開發人員可以使用程式 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> c # 程式碼手動執行邏輯。</span><span class="sxs-lookup"><span data-stu-id="20f1a-269">A developer may manually implement <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic using procedural C# code.</span></span> <span data-ttu-id="20f1a-270">如需詳細資訊，請參閱<xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>。</span><span class="sxs-lookup"><span data-stu-id="20f1a-270">For more information, see <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span></span>

> [!WARNING]
> <span data-ttu-id="20f1a-271">手動轉譯樹狀結構產生器邏輯的使用會被視為 advanced 和 unsafe 案例，不建議用於一般元件開發。</span><span class="sxs-lookup"><span data-stu-id="20f1a-271">Use of manual render tree builder logic is considered an advanced and unsafe scenario, not recommended for general component development.</span></span>

<span data-ttu-id="20f1a-272"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>撰寫程式碼時，開發人員必須保證程式碼的正確性。</span><span class="sxs-lookup"><span data-stu-id="20f1a-272">If <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> code is written, the developer must guarantee the correctness of the code.</span></span> <span data-ttu-id="20f1a-273">例如，開發人員必須確保：</span><span class="sxs-lookup"><span data-stu-id="20f1a-273">For example, the developer must ensure that:</span></span>

* <span data-ttu-id="20f1a-274">對 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> 和 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> 的呼叫會正確平衡。</span><span class="sxs-lookup"><span data-stu-id="20f1a-274">Calls to <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> and <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> are correctly balanced.</span></span>
* <span data-ttu-id="20f1a-275">只會將屬性新增至正確的位置。</span><span class="sxs-lookup"><span data-stu-id="20f1a-275">Attributes are only added in the correct places.</span></span>

<span data-ttu-id="20f1a-276">不正確的手動轉譯樹狀結構產生器邏輯可能會造成任何未定義的行為，包括損毀、伺服器當機和安全性弱點。</span><span class="sxs-lookup"><span data-stu-id="20f1a-276">Incorrect manual render tree builder logic can cause arbitrary undefined behavior, including crashes, server hangs, and security vulnerabilities.</span></span>

<span data-ttu-id="20f1a-277">請考慮以相同的複雜度層級手動呈現樹系產生器邏輯，並以手動方式撰寫元件程式碼或 MSIL 指令的相同層級 *風險* 。</span><span class="sxs-lookup"><span data-stu-id="20f1a-277">Consider manual render tree builder logic on the same level of complexity and with the same level of *danger* as writing assembly code or MSIL instructions by hand.</span></span>
