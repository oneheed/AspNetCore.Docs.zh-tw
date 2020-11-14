---
title: 將 HTTP 處理常式和模組遷移至 ASP.NET Core 中介軟體
author: rick-anderson
description: ''
ms.author: riande
ms.date: 12/07/2016
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: migration/http-modules
ms.openlocfilehash: 4abba1d4304bf537bd96623527c851d9d15774a4
ms.sourcegitcommit: 1be547564381873fe9e84812df8d2088514c622a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94508158"
---
# <a name="migrate-http-handlers-and-modules-to-aspnet-core-middleware"></a><span data-ttu-id="82066-102">將 HTTP 處理常式和模組遷移至 ASP.NET Core 中介軟體</span><span class="sxs-lookup"><span data-stu-id="82066-102">Migrate HTTP handlers and modules to ASP.NET Core middleware</span></span>

<span data-ttu-id="82066-103">本文說明如何將現有 [的 ASP.NET HTTP 模組和處理常式從 system.webserver](/iis/configuration/system.webserver/) 遷移至 ASP.NET Core [中介軟體](xref:fundamentals/middleware/index)。</span><span class="sxs-lookup"><span data-stu-id="82066-103">This article shows how to migrate existing ASP.NET [HTTP modules and handlers from system.webserver](/iis/configuration/system.webserver/) to ASP.NET Core [middleware](xref:fundamentals/middleware/index).</span></span>

## <a name="modules-and-handlers-revisited"></a><span data-ttu-id="82066-104">進行的模組和處理常式</span><span class="sxs-lookup"><span data-stu-id="82066-104">Modules and handlers revisited</span></span>

<span data-ttu-id="82066-105">在繼續 ASP.NET Core 中介軟體之前，讓我們先回顧一下 HTTP 模組和處理常式的運作方式：</span><span class="sxs-lookup"><span data-stu-id="82066-105">Before proceeding to ASP.NET Core middleware, let's first recap how HTTP modules and handlers work:</span></span>

![模組處理常式](http-modules/_static/moduleshandlers.png)

<span data-ttu-id="82066-107">**處理常式如下：**</span><span class="sxs-lookup"><span data-stu-id="82066-107">**Handlers are:**</span></span>

* <span data-ttu-id="82066-108">執行[IHttpHandler](/dotnet/api/system.web.ihttphandler)的類別</span><span class="sxs-lookup"><span data-stu-id="82066-108">Classes that implement [IHttpHandler](/dotnet/api/system.web.ihttphandler)</span></span>

* <span data-ttu-id="82066-109">用來處理具有指定之檔案名或副檔名的要求，例如 *. report*</span><span class="sxs-lookup"><span data-stu-id="82066-109">Used to handle requests with a given file name or extension, such as *.report*</span></span>

* <span data-ttu-id="82066-110">在 *Web.config* 中 [設定](/iis/configuration/system.webserver/handlers/)</span><span class="sxs-lookup"><span data-stu-id="82066-110">[Configured](/iis/configuration/system.webserver/handlers/) in *Web.config*</span></span>

<span data-ttu-id="82066-111">**模組包括：**</span><span class="sxs-lookup"><span data-stu-id="82066-111">**Modules are:**</span></span>

* <span data-ttu-id="82066-112">執行[IHttpModule](/dotnet/api/system.web.ihttpmodule)的類別</span><span class="sxs-lookup"><span data-stu-id="82066-112">Classes that implement [IHttpModule](/dotnet/api/system.web.ihttpmodule)</span></span>

* <span data-ttu-id="82066-113">針對每個要求叫用</span><span class="sxs-lookup"><span data-stu-id="82066-113">Invoked for every request</span></span>

* <span data-ttu-id="82066-114">能夠進行短暫的 (停止進一步處理要求) </span><span class="sxs-lookup"><span data-stu-id="82066-114">Able to short-circuit (stop further processing of a request)</span></span>

* <span data-ttu-id="82066-115">可以新增至 HTTP 回應，或建立自己的</span><span class="sxs-lookup"><span data-stu-id="82066-115">Able to add to the HTTP response, or create their own</span></span>

* <span data-ttu-id="82066-116">在 *Web.config* 中 [設定](/iis/configuration/system.webserver/modules/)</span><span class="sxs-lookup"><span data-stu-id="82066-116">[Configured](/iis/configuration/system.webserver/modules/) in *Web.config*</span></span>

<span data-ttu-id="82066-117">**模組處理傳入要求的順序取決於：**</span><span class="sxs-lookup"><span data-stu-id="82066-117">**The order in which modules process incoming requests is determined by:**</span></span>

1. <span data-ttu-id="82066-118"><https://docs.microsoft.com/previous-versions/ms227673(v=vs.140)>，這是 ASP.NET： [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest)、 [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest)等所引發的一連串事件。每個模組都可以建立一個或多個事件的處理常式。</span><span class="sxs-lookup"><span data-stu-id="82066-118">The <https://docs.microsoft.com/previous-versions/ms227673(v=vs.140)>, which is a series events fired by ASP.NET: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest), etc. Each module can create a handler for one or more events.</span></span>

2. <span data-ttu-id="82066-119">針對相同事件， *Web.config* 中設定它們的順序。</span><span class="sxs-lookup"><span data-stu-id="82066-119">For the same event, the order in which they're configured in *Web.config*.</span></span>

<span data-ttu-id="82066-120">除了模組之外，您還可以將生命週期事件的處理常式加入至 *Global.asax.cs* 檔案。</span><span class="sxs-lookup"><span data-stu-id="82066-120">In addition to modules, you can add handlers for the life cycle events to your *Global.asax.cs* file.</span></span> <span data-ttu-id="82066-121">這些處理常式會在設定的模組中的處理常式之後執行。</span><span class="sxs-lookup"><span data-stu-id="82066-121">These handlers run after the handlers in the configured modules.</span></span>

## <a name="from-handlers-and-modules-to-middleware"></a><span data-ttu-id="82066-122">從處理常式和模組到中介軟體</span><span class="sxs-lookup"><span data-stu-id="82066-122">From handlers and modules to middleware</span></span>

<span data-ttu-id="82066-123">**中介軟體比 HTTP 模組和處理常式簡單：**</span><span class="sxs-lookup"><span data-stu-id="82066-123">**Middleware are simpler than HTTP modules and handlers:**</span></span>

* <span data-ttu-id="82066-124">模組、處理常式、 *Global.asax.cs* 、 *Web.config* (除了 IIS 設定) 和應用程式生命週期消失之外</span><span class="sxs-lookup"><span data-stu-id="82066-124">Modules, handlers, *Global.asax.cs* , *Web.config* (except for IIS configuration) and the application life cycle are gone</span></span>

* <span data-ttu-id="82066-125">模組和處理常式的角色已由中介軟體接管</span><span class="sxs-lookup"><span data-stu-id="82066-125">The roles of both modules and handlers have been taken over by middleware</span></span>

* <span data-ttu-id="82066-126">中介軟體是使用程式碼來設定，而不是在 *Web.config*</span><span class="sxs-lookup"><span data-stu-id="82066-126">Middleware are configured using code rather than in *Web.config*</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="82066-127">[管線分支](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) 可讓您根據 URL，以及要求標頭、查詢字串等，將要求傳送至特定中介軟體。</span><span class="sxs-lookup"><span data-stu-id="82066-127">[Pipeline branching](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) lets you send requests to specific middleware, based on not only the URL but also on request headers, query strings, etc.</span></span>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="82066-128">[管線分支](xref:fundamentals/middleware/index#use-run-and-map) 可讓您根據 URL，以及要求標頭、查詢字串等，將要求傳送至特定中介軟體。</span><span class="sxs-lookup"><span data-stu-id="82066-128">[Pipeline branching](xref:fundamentals/middleware/index#use-run-and-map) lets you send requests to specific middleware, based on not only the URL but also on request headers, query strings, etc.</span></span>

::: moniker-end

<span data-ttu-id="82066-129">**中介軟體與模組非常類似：**</span><span class="sxs-lookup"><span data-stu-id="82066-129">**Middleware are very similar to modules:**</span></span>

* <span data-ttu-id="82066-130">針對每個要求以準則叫用</span><span class="sxs-lookup"><span data-stu-id="82066-130">Invoked in principle for every request</span></span>

* <span data-ttu-id="82066-131">藉由[不將要求傳遞至下一個中介軟體](#http-modules-shortcircuiting-middleware)，而能夠將要求短路</span><span class="sxs-lookup"><span data-stu-id="82066-131">Able to short-circuit a request, by [not passing the request to the next middleware](#http-modules-shortcircuiting-middleware)</span></span>

* <span data-ttu-id="82066-132">能夠建立自己的 HTTP 回應</span><span class="sxs-lookup"><span data-stu-id="82066-132">Able to create their own HTTP response</span></span>

<span data-ttu-id="82066-133">**中介軟體和模組會以不同的連續處理：**</span><span class="sxs-lookup"><span data-stu-id="82066-133">**Middleware and modules are processed in a different order:**</span></span>

* <span data-ttu-id="82066-134">中介軟體的順序是根據它們插入要求管線的順序，而模組的順序主要是以事件為基礎 <https://docs.microsoft.com/previous-versions/ms227673(v=vs.140)></span><span class="sxs-lookup"><span data-stu-id="82066-134">Order of middleware is based on the order in which they're inserted into the request pipeline, while order of modules is mainly based on <https://docs.microsoft.com/previous-versions/ms227673(v=vs.140)> events</span></span>

* <span data-ttu-id="82066-135">回應的中介軟體順序與要求的順序相反，因為要求和回應的模組順序相同</span><span class="sxs-lookup"><span data-stu-id="82066-135">Order of middleware for responses is the reverse from that for requests, while order of modules is the same for requests and responses</span></span>

* <span data-ttu-id="82066-136">請參閱 [使用 IApplicationBuilder 建立中介軟體管線](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)</span><span class="sxs-lookup"><span data-stu-id="82066-136">See [Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)</span></span>

![中介軟體](http-modules/_static/middleware.png)

<span data-ttu-id="82066-138">請注意，在上圖中，驗證中介軟體會簡短縮短要求。</span><span class="sxs-lookup"><span data-stu-id="82066-138">Note how in the image above, the authentication middleware short-circuited the request.</span></span>

## <a name="migrating-module-code-to-middleware"></a><span data-ttu-id="82066-139">將模組程式碼遷移至中介軟體</span><span class="sxs-lookup"><span data-stu-id="82066-139">Migrating module code to middleware</span></span>

<span data-ttu-id="82066-140">現有的 HTTP 模組看起來會像這樣：</span><span class="sxs-lookup"><span data-stu-id="82066-140">An existing HTTP module will look similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]

<span data-ttu-id="82066-141">如 [中介軟體](xref:fundamentals/middleware/index) 頁面所示，ASP.NET Core 中介軟體是一種類別，可公開 `Invoke` 接受 `HttpContext` 並傳回的方法 `Task` 。</span><span class="sxs-lookup"><span data-stu-id="82066-141">As shown in the [Middleware](xref:fundamentals/middleware/index) page, an ASP.NET Core middleware is a class that exposes an `Invoke` method taking an `HttpContext` and returning a `Task`.</span></span> <span data-ttu-id="82066-142">新的中介軟體看起來像這樣：</span><span class="sxs-lookup"><span data-stu-id="82066-142">Your new middleware will look like this:</span></span>

<a name="http-modules-usemiddleware"></a>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]

<span data-ttu-id="82066-143">上述中介軟體範本取自 [撰寫中介軟體](xref:fundamentals/middleware/write)的章節。</span><span class="sxs-lookup"><span data-stu-id="82066-143">The preceding middleware template was taken from the section on [writing middleware](xref:fundamentals/middleware/write).</span></span>

<span data-ttu-id="82066-144">*MyMiddlewareExtensions* helper 類別可讓您更輕鬆地在類別中設定中介軟體 `Startup` 。</span><span class="sxs-lookup"><span data-stu-id="82066-144">The *MyMiddlewareExtensions* helper class makes it easier to configure your middleware in your `Startup` class.</span></span> <span data-ttu-id="82066-145">`UseMyMiddleware`方法會將中介軟體類別新增至要求管線。</span><span class="sxs-lookup"><span data-stu-id="82066-145">The `UseMyMiddleware` method adds your middleware class to the request pipeline.</span></span> <span data-ttu-id="82066-146">中介軟體所需的服務會插入中介軟體的函式中。</span><span class="sxs-lookup"><span data-stu-id="82066-146">Services required by the middleware get injected in the middleware's constructor.</span></span>

<a name="http-modules-shortcircuiting-middleware"></a>

<span data-ttu-id="82066-147">您的模組可能會終止要求，例如，如果使用者未獲授權：</span><span class="sxs-lookup"><span data-stu-id="82066-147">Your module might terminate a request, for example if the user isn't authorized:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]

<span data-ttu-id="82066-148">中介軟體不會 `Invoke` 在管線中的下一個中介軟體上呼叫，藉此處理此情況。</span><span class="sxs-lookup"><span data-stu-id="82066-148">A middleware handles this by not calling `Invoke` on the next middleware in the pipeline.</span></span> <span data-ttu-id="82066-149">請記住，這不會完全終止要求，因為當回應透過管線回傳時，仍會叫用先前的中介軟體。</span><span class="sxs-lookup"><span data-stu-id="82066-149">Keep in mind that this doesn't fully terminate the request, because previous middlewares will still be invoked when the response makes its way back through the pipeline.</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]

<span data-ttu-id="82066-150">當您將模組的功能遷移至新的中介軟體時，可能會發現您的程式碼未進行編譯，因為 `HttpContext` ASP.NET Core 中的類別已大幅變更。</span><span class="sxs-lookup"><span data-stu-id="82066-150">When you migrate your module's functionality to your new middleware, you may find that your code doesn't compile because the `HttpContext` class has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="82066-151">[稍後](#migrating-to-the-new-httpcontext)，您將瞭解如何遷移至新的 ASP.NET Core HttpCoNtext。</span><span class="sxs-lookup"><span data-stu-id="82066-151">[Later on](#migrating-to-the-new-httpcontext), you'll see how to migrate to the new ASP.NET Core HttpContext.</span></span>

## <a name="migrating-module-insertion-into-the-request-pipeline"></a><span data-ttu-id="82066-152">將模組插入遷移至要求管線</span><span class="sxs-lookup"><span data-stu-id="82066-152">Migrating module insertion into the request pipeline</span></span>

<span data-ttu-id="82066-153">HTTP 模組通常會使用 *Web.config* 新增至要求管線：</span><span class="sxs-lookup"><span data-stu-id="82066-153">HTTP modules are typically added to the request pipeline using *Web.config* :</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]

<span data-ttu-id="82066-154">將 [新的中介軟體新增](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) 至類別中的要求管線，以進行轉換 `Startup` ：</span><span class="sxs-lookup"><span data-stu-id="82066-154">Convert this by [adding your new middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) to the request pipeline in your `Startup` class:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]

<span data-ttu-id="82066-155">在管線中，您插入新中介軟體的確切位置，取決於它以模組形式處理的事件 (`BeginRequest` 、等等 `EndRequest` ) 及其在 *Web.config* 的模組清單中的順序。</span><span class="sxs-lookup"><span data-stu-id="82066-155">The exact spot in the pipeline where you insert your new middleware depends on the event that it handled as a module (`BeginRequest`, `EndRequest`, etc.) and its order in your list of modules in *Web.config*.</span></span>

<span data-ttu-id="82066-156">如先前所述，ASP.NET Core 中沒有應用程式生命週期，中介軟體的處理回應順序與模組所使用的順序不同。</span><span class="sxs-lookup"><span data-stu-id="82066-156">As previously stated, there's no application life cycle in ASP.NET Core and the order in which responses are processed by middleware differs from the order used by modules.</span></span> <span data-ttu-id="82066-157">這可能會讓您的訂購決策更具挑戰性。</span><span class="sxs-lookup"><span data-stu-id="82066-157">This could make your ordering decision more challenging.</span></span>

<span data-ttu-id="82066-158">如果排序變成問題，您可以將模組分割成多個中介軟體元件，以便個別進行排序。</span><span class="sxs-lookup"><span data-stu-id="82066-158">If ordering becomes a problem, you could split your module into multiple middleware components that can be ordered independently.</span></span>

## <a name="migrating-handler-code-to-middleware"></a><span data-ttu-id="82066-159">將處理常式程式碼遷移至中介軟體</span><span class="sxs-lookup"><span data-stu-id="82066-159">Migrating handler code to middleware</span></span>

<span data-ttu-id="82066-160">HTTP 處理常式看起來像這樣：</span><span class="sxs-lookup"><span data-stu-id="82066-160">An HTTP handler looks something like this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]

<span data-ttu-id="82066-161">在您的 ASP.NET Core 專案中，您會將其轉譯為類似如下的中介軟體：</span><span class="sxs-lookup"><span data-stu-id="82066-161">In your ASP.NET Core project, you would translate this to a middleware similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]

<span data-ttu-id="82066-162">此中介軟體非常類似于模組的對應中介軟體。</span><span class="sxs-lookup"><span data-stu-id="82066-162">This middleware is very similar to the middleware corresponding to modules.</span></span> <span data-ttu-id="82066-163">唯一的差別在於這裡沒有任何呼叫 `_next.Invoke(context)` 。</span><span class="sxs-lookup"><span data-stu-id="82066-163">The only real difference is that here there's no call to `_next.Invoke(context)`.</span></span> <span data-ttu-id="82066-164">這是合理的，因為處理常式是在要求管線的結尾，所以不會有下一個中介軟體可叫用。</span><span class="sxs-lookup"><span data-stu-id="82066-164">That makes sense, because the handler is at the end of the request pipeline, so there will be no next middleware to invoke.</span></span>

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a><span data-ttu-id="82066-165">將處理常式插入遷移至要求管線</span><span class="sxs-lookup"><span data-stu-id="82066-165">Migrating handler insertion into the request pipeline</span></span>

<span data-ttu-id="82066-166">設定 HTTP 處理常式是在 *Web.config* 中完成，並看起來像這樣：</span><span class="sxs-lookup"><span data-stu-id="82066-166">Configuring an HTTP handler is done in *Web.config* and looks something like this:</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]

<span data-ttu-id="82066-167">您可以藉由將新的處理常式中介軟體新增至類別中的要求管線來轉換 `Startup` ，類似于從模組轉換的中介軟體。</span><span class="sxs-lookup"><span data-stu-id="82066-167">You could convert this by adding your new handler middleware to the request pipeline in your `Startup` class, similar to middleware converted from modules.</span></span> <span data-ttu-id="82066-168">這種方法的問題在於它會將所有要求傳送至新的處理常式中介軟體。</span><span class="sxs-lookup"><span data-stu-id="82066-168">The problem with that approach is that it would send all requests to your new handler middleware.</span></span> <span data-ttu-id="82066-169">不過，您只需要具有指定延伸模組的要求才能到達您的中介軟體。</span><span class="sxs-lookup"><span data-stu-id="82066-169">However, you only want requests with a given extension to reach your middleware.</span></span> <span data-ttu-id="82066-170">這會提供您與 HTTP 處理常式相同的功能。</span><span class="sxs-lookup"><span data-stu-id="82066-170">That would give you the same functionality you had with your HTTP handler.</span></span>

<span data-ttu-id="82066-171">其中一個解決方法是使用擴充方法，將管線分支給具有指定擴充的要求 `MapWhen` 。</span><span class="sxs-lookup"><span data-stu-id="82066-171">One solution is to branch the pipeline for requests with a given extension, using the `MapWhen` extension method.</span></span> <span data-ttu-id="82066-172">您可以在 `Configure` 新增其他中介軟體的相同方法中執行此動作：</span><span class="sxs-lookup"><span data-stu-id="82066-172">You do this in the same `Configure` method where you add the other middleware:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]

<span data-ttu-id="82066-173">`MapWhen` 採用下列參數：</span><span class="sxs-lookup"><span data-stu-id="82066-173">`MapWhen` takes these parameters:</span></span>

1. <span data-ttu-id="82066-174">使用的 lambda， `HttpContext` `true` 如果要求應該在分支下移，則會傳回。</span><span class="sxs-lookup"><span data-stu-id="82066-174">A lambda that takes the `HttpContext` and returns `true` if the request should go down the branch.</span></span> <span data-ttu-id="82066-175">這表示您可以不只根據其擴充功能來分支要求，也可以在要求標頭、查詢字串參數等上進行。</span><span class="sxs-lookup"><span data-stu-id="82066-175">This means you can branch requests not just based on their extension, but also on request headers, query string parameters, etc.</span></span>

2. <span data-ttu-id="82066-176">接受 `IApplicationBuilder` 並加入分支所有中介軟體的 lambda。</span><span class="sxs-lookup"><span data-stu-id="82066-176">A lambda that takes an `IApplicationBuilder` and adds all the middleware for the branch.</span></span> <span data-ttu-id="82066-177">這表示您可以在處理常式中介軟體之前，將其他中介軟體新增至分支。</span><span class="sxs-lookup"><span data-stu-id="82066-177">This means you can add additional middleware to the branch in front of your handler middleware.</span></span>

<span data-ttu-id="82066-178">將在所有要求上叫用分支之前新增至管線的中介軟體;分支不會有任何影響。</span><span class="sxs-lookup"><span data-stu-id="82066-178">Middleware added to the pipeline before the branch will be invoked on all requests; the branch will have no impact on them.</span></span>

## <a name="loading-middleware-options-using-the-options-pattern"></a><span data-ttu-id="82066-179">使用選項模式載入中介軟體選項</span><span class="sxs-lookup"><span data-stu-id="82066-179">Loading middleware options using the options pattern</span></span>

<span data-ttu-id="82066-180">某些模組和處理常式具有儲存在 *Web.config* 中的設定選項。不過，在 ASP.NET Core 新的設定模型是用來取代 *Web.config* 。</span><span class="sxs-lookup"><span data-stu-id="82066-180">Some modules and handlers have configuration options that are stored in *Web.config*. However, in ASP.NET Core a new configuration model is used in place of *Web.config*.</span></span>

<span data-ttu-id="82066-181">新的設定 [系統](xref:fundamentals/configuration/index) 提供下列選項來解決此問題：</span><span class="sxs-lookup"><span data-stu-id="82066-181">The new [configuration system](xref:fundamentals/configuration/index) gives you these options to solve this:</span></span>

* <span data-ttu-id="82066-182">將選項直接插入中介軟體中，如下一 [節](#loading-middleware-options-through-direct-injection)所示。</span><span class="sxs-lookup"><span data-stu-id="82066-182">Directly inject the options into the middleware, as shown in the [next section](#loading-middleware-options-through-direct-injection).</span></span>

* <span data-ttu-id="82066-183">使用 [選項模式](xref:fundamentals/configuration/options)：</span><span class="sxs-lookup"><span data-stu-id="82066-183">Use the [options pattern](xref:fundamentals/configuration/options):</span></span>

1. <span data-ttu-id="82066-184">建立類別來保存中介軟體選項，例如：</span><span class="sxs-lookup"><span data-stu-id="82066-184">Create a class to hold your middleware options, for example:</span></span>

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]

2. <span data-ttu-id="82066-185">儲存選項值</span><span class="sxs-lookup"><span data-stu-id="82066-185">Store the option values</span></span>

   <span data-ttu-id="82066-186">設定系統可讓您將選項值儲存在任何您想要的位置。</span><span class="sxs-lookup"><span data-stu-id="82066-186">The configuration system allows you to store option values anywhere you want.</span></span> <span data-ttu-id="82066-187">不過，大部分的網站 *appsettings.json* 會使用，因此我們將採用該方法：</span><span class="sxs-lookup"><span data-stu-id="82066-187">However, most sites use *appsettings.json* , so we'll take that approach:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]

   <span data-ttu-id="82066-188">這裡的 *MyMiddlewareOptionsSection* 是區段名稱。</span><span class="sxs-lookup"><span data-stu-id="82066-188">*MyMiddlewareOptionsSection* here is a section name.</span></span> <span data-ttu-id="82066-189">它不一定要與選項類別的名稱相同。</span><span class="sxs-lookup"><span data-stu-id="82066-189">It doesn't have to be the same as the name of your options class.</span></span>

3. <span data-ttu-id="82066-190">建立選項值與 options 類別的關聯</span><span class="sxs-lookup"><span data-stu-id="82066-190">Associate the option values with the options class</span></span>

    <span data-ttu-id="82066-191">選項模式使用 ASP.NET Core 的相依性插入架構，將選項類型 (例如 `MyMiddlewareOptions`) 與 `MyMiddlewareOptions` 具有實際選項的物件產生關聯。</span><span class="sxs-lookup"><span data-stu-id="82066-191">The options pattern uses ASP.NET Core's dependency injection framework to associate the options type (such as `MyMiddlewareOptions`) with a `MyMiddlewareOptions` object that has the actual options.</span></span>

    <span data-ttu-id="82066-192">更新您的 `Startup` 類別：</span><span class="sxs-lookup"><span data-stu-id="82066-192">Update your `Startup` class:</span></span>

   1. <span data-ttu-id="82066-193">如果您正在使用 *appsettings.json* ，請將它新增至函式中的設定產生器 `Startup` ：</span><span class="sxs-lookup"><span data-stu-id="82066-193">If you're using *appsettings.json* , add it to the configuration builder in the `Startup` constructor:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]

   2. <span data-ttu-id="82066-194">設定選項服務：</span><span class="sxs-lookup"><span data-stu-id="82066-194">Configure the options service:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

   3. <span data-ttu-id="82066-195">將您的選項與您的 options 類別產生關聯：</span><span class="sxs-lookup"><span data-stu-id="82066-195">Associate your options with your options class:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]

4. <span data-ttu-id="82066-196">將選項插入中介軟體函式中。</span><span class="sxs-lookup"><span data-stu-id="82066-196">Inject the options into your middleware constructor.</span></span> <span data-ttu-id="82066-197">這類似于在控制器中插入選項。</span><span class="sxs-lookup"><span data-stu-id="82066-197">This is similar to injecting options into a controller.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]

   <span data-ttu-id="82066-198">將中介軟體新增至的 [UseMiddleware](#http-modules-usemiddleware) 擴充方法 `IApplicationBuilder` 會負責相依性插入。</span><span class="sxs-lookup"><span data-stu-id="82066-198">The [UseMiddleware](#http-modules-usemiddleware) extension method that adds your middleware to the `IApplicationBuilder` takes care of dependency injection.</span></span>

   <span data-ttu-id="82066-199">這並不限於 `IOptions` 物件。</span><span class="sxs-lookup"><span data-stu-id="82066-199">This isn't limited to `IOptions` objects.</span></span> <span data-ttu-id="82066-200">中介軟體所需的任何其他物件都可以透過這種方式插入。</span><span class="sxs-lookup"><span data-stu-id="82066-200">Any other object that your middleware requires can be injected this way.</span></span>

## <a name="loading-middleware-options-through-direct-injection"></a><span data-ttu-id="82066-201">透過直接插入載入中介軟體選項</span><span class="sxs-lookup"><span data-stu-id="82066-201">Loading middleware options through direct injection</span></span>

<span data-ttu-id="82066-202">選項模式的優點是它會在選項值和取用者之間建立鬆散結合。</span><span class="sxs-lookup"><span data-stu-id="82066-202">The options pattern has the advantage that it creates loose coupling between options values and their consumers.</span></span> <span data-ttu-id="82066-203">當您將 options 類別與實際的選項值建立關聯之後，任何其他類別都可以透過相依性插入架構取得選項的存取權。</span><span class="sxs-lookup"><span data-stu-id="82066-203">Once you've associated an options class with the actual options values, any other class can get access to the options through the dependency injection framework.</span></span> <span data-ttu-id="82066-204">不需要傳遞選項值。</span><span class="sxs-lookup"><span data-stu-id="82066-204">There's no need to pass around options values.</span></span>

<span data-ttu-id="82066-205">但是，如果您想要使用相同的中介軟體兩次，並使用不同的選項，這會中斷。</span><span class="sxs-lookup"><span data-stu-id="82066-205">This breaks down though if you want to use the same middleware twice, with different options.</span></span> <span data-ttu-id="82066-206">例如，在不同分支中使用的授權中介軟體允許不同的角色。</span><span class="sxs-lookup"><span data-stu-id="82066-206">For example an authorization middleware used in different branches allowing different roles.</span></span> <span data-ttu-id="82066-207">您無法將兩個不同的選項物件與一個 options 類別建立關聯。</span><span class="sxs-lookup"><span data-stu-id="82066-207">You can't associate two different options objects with the one options class.</span></span>

<span data-ttu-id="82066-208">解決方法是在類別中取得具有實際選項值的選項物件 `Startup` ，並將它們直接傳遞至中介軟體的每個實例。</span><span class="sxs-lookup"><span data-stu-id="82066-208">The solution is to get the options objects with the actual options values in your `Startup` class and pass those directly to each instance of your middleware.</span></span>

1. <span data-ttu-id="82066-209">將第二個索引鍵新增至 *appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="82066-209">Add a second key to *appsettings.json*</span></span>

   <span data-ttu-id="82066-210">若要在檔案中新增第二組選項 *appsettings.json* ，請使用新的金鑰來唯一識別它：</span><span class="sxs-lookup"><span data-stu-id="82066-210">To add a second set of options to the *appsettings.json* file, use a new key to uniquely identify it:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]

2. <span data-ttu-id="82066-211">取出選項值，並將其傳遞至中介軟體。</span><span class="sxs-lookup"><span data-stu-id="82066-211">Retrieve options values and pass them to middleware.</span></span> <span data-ttu-id="82066-212">`Use...`擴充方法 (會將中介軟體新增至管線) 是要傳入選項值的邏輯位置：</span><span class="sxs-lookup"><span data-stu-id="82066-212">The `Use...` extension method (which adds your middleware to the pipeline) is a logical place to pass in the option values:</span></span> 

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]

3. <span data-ttu-id="82066-213">啟用中介軟體以採用 options 參數。</span><span class="sxs-lookup"><span data-stu-id="82066-213">Enable middleware to take an options parameter.</span></span> <span data-ttu-id="82066-214">提供擴充方法的多載 `Use...` ， (會採用 options 參數，並將其傳遞給 `UseMiddleware`) 。</span><span class="sxs-lookup"><span data-stu-id="82066-214">Provide an overload of the `Use...` extension method (that takes the options parameter and passes it to `UseMiddleware`).</span></span> <span data-ttu-id="82066-215">`UseMiddleware`使用參數呼叫時，會在將中介軟體物件具現化時，將參數傳遞至中介軟體函式。</span><span class="sxs-lookup"><span data-stu-id="82066-215">When `UseMiddleware` is called with parameters, it passes the parameters to your middleware constructor when it instantiates the middleware object.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]

   <span data-ttu-id="82066-216">請注意，這會如何包裝物件中的選項物件 `OptionsWrapper` 。</span><span class="sxs-lookup"><span data-stu-id="82066-216">Note how this wraps the options object in an `OptionsWrapper` object.</span></span> <span data-ttu-id="82066-217">這會依照 `IOptions` 中介軟體函式的預期執行。</span><span class="sxs-lookup"><span data-stu-id="82066-217">This implements `IOptions`, as expected by the middleware constructor.</span></span>

## <a name="migrating-to-the-new-httpcontext"></a><span data-ttu-id="82066-218">遷移至新的 HttpCoNtext</span><span class="sxs-lookup"><span data-stu-id="82066-218">Migrating to the new HttpContext</span></span>

<span data-ttu-id="82066-219">您稍早看到 `Invoke` 中介軟體中的方法接受下列類型的參數 `HttpContext` ：</span><span class="sxs-lookup"><span data-stu-id="82066-219">You saw earlier that the `Invoke` method in your middleware takes a parameter of type `HttpContext`:</span></span>

```csharp
public async Task Invoke(HttpContext context)
```

<span data-ttu-id="82066-220">`HttpContext` 在 ASP.NET Core 中已大幅變更。</span><span class="sxs-lookup"><span data-stu-id="82066-220">`HttpContext` has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="82066-221">本節說明如何將 system.servicemodel 最常 [使用的屬性轉譯為新](/dotnet/api/system.web.httpcontext) 的 `Microsoft.AspNetCore.Http.HttpContext` 。</span><span class="sxs-lookup"><span data-stu-id="82066-221">This section shows how to translate the most commonly used properties of [System.Web.HttpContext](/dotnet/api/system.web.httpcontext) to the new `Microsoft.AspNetCore.Http.HttpContext`.</span></span>

### <a name="httpcontext"></a><span data-ttu-id="82066-222">HttpcoNtext.current</span><span class="sxs-lookup"><span data-stu-id="82066-222">HttpContext</span></span>

<span data-ttu-id="82066-223">**HttpCoNtext** 會轉譯為：</span><span class="sxs-lookup"><span data-stu-id="82066-223">**HttpContext.Items** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]

<span data-ttu-id="82066-224">**唯一的要求識別碼 (沒有 system.string 對應)**</span><span class="sxs-lookup"><span data-stu-id="82066-224">**Unique request ID (no System.Web.HttpContext counterpart)**</span></span>

<span data-ttu-id="82066-225">為您提供每個要求的唯一識別碼。</span><span class="sxs-lookup"><span data-stu-id="82066-225">Gives you a unique id for each request.</span></span> <span data-ttu-id="82066-226">包含在您的記錄中非常有用。</span><span class="sxs-lookup"><span data-stu-id="82066-226">Very useful to include in your logs.</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]

### <a name="httpcontextrequest"></a><span data-ttu-id="82066-227">HttpCoNtext. 要求</span><span class="sxs-lookup"><span data-stu-id="82066-227">HttpContext.Request</span></span>

<span data-ttu-id="82066-228">**HttpMethod** 會將轉換成：</span><span class="sxs-lookup"><span data-stu-id="82066-228">**HttpContext.Request.HttpMethod** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]

<span data-ttu-id="82066-229">**HttpcoNtext.current** 會轉譯為：</span><span class="sxs-lookup"><span data-stu-id="82066-229">**HttpContext.Request.QueryString** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]

<span data-ttu-id="82066-230">**HttpcoNtext. Url** 和 **HttpCoNtext. RawUrl** 會轉譯為：</span><span class="sxs-lookup"><span data-stu-id="82066-230">**HttpContext.Request.Url** and **HttpContext.Request.RawUrl** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]

<span data-ttu-id="82066-231">**IsSecureConnection** 會將轉換成：</span><span class="sxs-lookup"><span data-stu-id="82066-231">**HttpContext.Request.IsSecureConnection** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]

<span data-ttu-id="82066-232">**UserHostAddress** 會將轉換成：</span><span class="sxs-lookup"><span data-stu-id="82066-232">**HttpContext.Request.UserHostAddress** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]

<span data-ttu-id="82066-233">**HttpCoNtext. 要求。 Cookie可轉換為** ：</span><span class="sxs-lookup"><span data-stu-id="82066-233">**HttpContext.Request.Cookies** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]

<span data-ttu-id="82066-234">**RequestCoNtext. RouteData** 可轉換為：</span><span class="sxs-lookup"><span data-stu-id="82066-234">**HttpContext.Request.RequestContext.RouteData** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]

<span data-ttu-id="82066-235">**HttpCoNtext. 標頭** 會轉譯為：</span><span class="sxs-lookup"><span data-stu-id="82066-235">**HttpContext.Request.Headers** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]

<span data-ttu-id="82066-236">**UserAgent** 會將轉換成：</span><span class="sxs-lookup"><span data-stu-id="82066-236">**HttpContext.Request.UserAgent** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]

<span data-ttu-id="82066-237">**UrlReferrer** 會將轉換成：</span><span class="sxs-lookup"><span data-stu-id="82066-237">**HttpContext.Request.UrlReferrer** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]

<span data-ttu-id="82066-238">**HttpCoNtext** 會轉譯為：</span><span class="sxs-lookup"><span data-stu-id="82066-238">**HttpContext.Request.ContentType** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]

<span data-ttu-id="82066-239">**HttpCoNtext. Form** 會轉譯為：</span><span class="sxs-lookup"><span data-stu-id="82066-239">**HttpContext.Request.Form** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]

> [!WARNING]
> <span data-ttu-id="82066-240">只有在內容子類型為 *x-www 表單 urlencoded* 或 *表單資料* 時，才會讀取表單值。</span><span class="sxs-lookup"><span data-stu-id="82066-240">Read form values only if the content sub type is *x-www-form-urlencoded* or *form-data*.</span></span>

<span data-ttu-id="82066-241">**InputStream** 會將轉換成：</span><span class="sxs-lookup"><span data-stu-id="82066-241">**HttpContext.Request.InputStream** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]

> [!WARNING]
> <span data-ttu-id="82066-242">此程式碼只能在管線結尾的處理常式類型中介軟體中使用。</span><span class="sxs-lookup"><span data-stu-id="82066-242">Use this code only in a handler type middleware, at the end of a pipeline.</span></span>
>
><span data-ttu-id="82066-243">您可以針對每個要求唯讀取一次以上所示的原始主體。</span><span class="sxs-lookup"><span data-stu-id="82066-243">You can read the raw body as shown above only once per request.</span></span> <span data-ttu-id="82066-244">在第一次讀取之後嘗試讀取主體的中介軟體將會讀取空白主體。</span><span class="sxs-lookup"><span data-stu-id="82066-244">Middleware trying to read the body after the first read will read an empty body.</span></span>
>
><span data-ttu-id="82066-245">這並不適用于讀取如先前所示的表單，因為這是從緩衝區完成的。</span><span class="sxs-lookup"><span data-stu-id="82066-245">This doesn't apply to reading a form as shown earlier, because that's done from a buffer.</span></span>

### <a name="httpcontextresponse"></a><span data-ttu-id="82066-246">HttpCoNtext. 回應</span><span class="sxs-lookup"><span data-stu-id="82066-246">HttpContext.Response</span></span>

<span data-ttu-id="82066-247">**HttpcoNtext. response** . **StatusDescription** 可轉換為：</span><span class="sxs-lookup"><span data-stu-id="82066-247">**HttpContext.Response.Status** and **HttpContext.Response.StatusDescription** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]

<span data-ttu-id="82066-248">**ContentEncoding** 和 **HTTPcoNtext** 會轉譯為：</span><span class="sxs-lookup"><span data-stu-id="82066-248">**HttpContext.Response.ContentEncoding** and **HttpContext.Response.ContentType** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]

<span data-ttu-id="82066-249">**HttpCoNtext** 本身的 ContentType 也會轉譯為：</span><span class="sxs-lookup"><span data-stu-id="82066-249">**HttpContext.Response.ContentType** on its own also translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]

<span data-ttu-id="82066-250">**HttpCoNtext。輸出** 會轉譯為：</span><span class="sxs-lookup"><span data-stu-id="82066-250">**HttpContext.Response.Output** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]

<span data-ttu-id="82066-251">**HttpCoNtext. TransmitFile**</span><span class="sxs-lookup"><span data-stu-id="82066-251">**HttpContext.Response.TransmitFile**</span></span>

<span data-ttu-id="82066-252">提供檔案的相關討論 <xref:fundamentals/request-features> 。</span><span class="sxs-lookup"><span data-stu-id="82066-252">Serving up a file is discussed in <xref:fundamentals/request-features>.</span></span>

<span data-ttu-id="82066-253">**HttpCoNtext. 標頭**</span><span class="sxs-lookup"><span data-stu-id="82066-253">**HttpContext.Response.Headers**</span></span>

<span data-ttu-id="82066-254">傳送回應標頭很複雜，因為如果您在將任何專案寫入回應本文之後設定這些標頭，就不會傳送這些標頭。</span><span class="sxs-lookup"><span data-stu-id="82066-254">Sending response headers is complicated by the fact that if you set them after anything has been written to the response body, they will not be sent.</span></span>

<span data-ttu-id="82066-255">解決方法是設定回呼方法，這個方法會在寫入回應開始之前呼叫。</span><span class="sxs-lookup"><span data-stu-id="82066-255">The solution is to set a callback method that will be called right before writing to the response starts.</span></span> <span data-ttu-id="82066-256">在中介軟體的方法開始時，最好這麼做 `Invoke` 。</span><span class="sxs-lookup"><span data-stu-id="82066-256">This is best done at the start of the `Invoke` method in your middleware.</span></span> <span data-ttu-id="82066-257">這是設定回應標頭的回呼方法。</span><span class="sxs-lookup"><span data-stu-id="82066-257">It's this callback method that sets your response headers.</span></span>

<span data-ttu-id="82066-258">下列程式碼會設定名為的回呼方法 `SetHeaders` ：</span><span class="sxs-lookup"><span data-stu-id="82066-258">The following code sets a callback method called `SetHeaders`:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="82066-259">`SetHeaders`回呼方法看起來像這樣：</span><span class="sxs-lookup"><span data-stu-id="82066-259">The `SetHeaders` callback method would look like this:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]

<span data-ttu-id="82066-260">**HttpCoNtext. 回應。 Cookie！**</span><span class="sxs-lookup"><span data-stu-id="82066-260">**HttpContext.Response.Cookies**</span></span>

<span data-ttu-id="82066-261">Cookie在 *設定 Cookie* 回應標頭中移動至瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="82066-261">Cookies travel to the browser in a *Set-Cookie* response header.</span></span> <span data-ttu-id="82066-262">如此一來，傳送 cookie 必須與用來傳送回應標頭的回呼相同：</span><span class="sxs-lookup"><span data-stu-id="82066-262">As a result, sending cookies requires the same callback as used for sending response headers:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetCookies, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="82066-263">`SetCookies`回呼方法如下所示：</span><span class="sxs-lookup"><span data-stu-id="82066-263">The `SetCookies` callback method would look like the following:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]

## <a name="additional-resources"></a><span data-ttu-id="82066-264">其他資源</span><span class="sxs-lookup"><span data-stu-id="82066-264">Additional resources</span></span>

* [<span data-ttu-id="82066-265">HTTP 處理常式和 HTTP 模組總覽</span><span class="sxs-lookup"><span data-stu-id="82066-265">HTTP Handlers and HTTP Modules Overview</span></span>](/iis/configuration/system.webserver/)
* [<span data-ttu-id="82066-266">設定</span><span class="sxs-lookup"><span data-stu-id="82066-266">Configuration</span></span>](xref:fundamentals/configuration/index)
* [<span data-ttu-id="82066-267">應用程式啟動</span><span class="sxs-lookup"><span data-stu-id="82066-267">Application Startup</span></span>](xref:fundamentals/startup)
* [<span data-ttu-id="82066-268">中介軟體</span><span class="sxs-lookup"><span data-stu-id="82066-268">Middleware</span></span>](xref:fundamentals/middleware/index)
