---
title: 'ASP.NET Core :::no-loc(Blazor)::: 狀態管理'
author: guardrex
description: '瞭解如何在應用程式中保存狀態 :::no-loc(Blazor Server)::: 。'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
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
uid: blazor/state-management
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 1769ddbb95c9ffe373e916c885e411adc3d4c65b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054992"
---
# <a name="aspnet-core-no-locblazor-state-management"></a><span data-ttu-id="26d9c-103">ASP.NET Core :::no-loc(Blazor)::: 狀態管理</span><span class="sxs-lookup"><span data-stu-id="26d9c-103">ASP.NET Core :::no-loc(Blazor)::: state management</span></span>

<span data-ttu-id="26d9c-104">由 [Steve Sanderson](https://github.com/SteveSandersonMS) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="26d9c-104">By [Steve Sanderson](https://github.com/SteveSandersonMS) and [Luke Latham](https://github.com/guardrex)</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="26d9c-105">在應用程式中建立的使用者狀態 :::no-loc(Blazor WebAssembly)::: 會保留在瀏覽器的記憶體中。</span><span class="sxs-lookup"><span data-stu-id="26d9c-105">User state created in a :::no-loc(Blazor WebAssembly)::: app is held in the browser's memory.</span></span>

<span data-ttu-id="26d9c-106">在瀏覽器記憶體中保留的使用者狀態範例包括：</span><span class="sxs-lookup"><span data-stu-id="26d9c-106">Examples of user state held in browser memory include:</span></span>

* <span data-ttu-id="26d9c-107">轉譯的 UI 中元件實例及其最新轉譯輸出的階層。</span><span class="sxs-lookup"><span data-stu-id="26d9c-107">The hierarchy of component instances and their most recent render output in the rendered UI.</span></span>
* <span data-ttu-id="26d9c-108">元件實例中欄位和屬性的值。</span><span class="sxs-lookup"><span data-stu-id="26d9c-108">The values of fields and properties in component instances.</span></span>
* <span data-ttu-id="26d9c-109">保存在相依性 [插入 (DI) ](xref:fundamentals/dependency-injection) 服務實例中的資料。</span><span class="sxs-lookup"><span data-stu-id="26d9c-109">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances.</span></span>
* <span data-ttu-id="26d9c-110">透過 [JavaScript interop](xref:blazor/call-javascript-from-dotnet) 呼叫設定的值。</span><span class="sxs-lookup"><span data-stu-id="26d9c-110">Values set through [JavaScript interop](xref:blazor/call-javascript-from-dotnet) calls.</span></span>

<span data-ttu-id="26d9c-111">當使用者關閉並重新開啟其瀏覽器或重載網頁時，瀏覽器記憶體中保留的使用者狀態將會遺失。</span><span class="sxs-lookup"><span data-stu-id="26d9c-111">When a user closes and re-opens their browser or reloads the page, user state held in the browser's memory is lost.</span></span>

## <a name="persist-state-across-browser-sessions"></a><span data-ttu-id="26d9c-112">跨瀏覽器會話保存狀態</span><span class="sxs-lookup"><span data-stu-id="26d9c-112">Persist state across browser sessions</span></span>

<span data-ttu-id="26d9c-113">一般而言，在使用者主動建立資料的瀏覽器會話中維護狀態，而不只是讀取已經存在的資料。</span><span class="sxs-lookup"><span data-stu-id="26d9c-113">Generally, maintain state across browser sessions where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="26d9c-114">若要在瀏覽器會話之間保持狀態，應用程式必須將資料保存到其他儲存位置，而不是瀏覽器的記憶體。</span><span class="sxs-lookup"><span data-stu-id="26d9c-114">To preserve state across browser sessions, the app must persist the data to some other storage location than the browser's memory.</span></span> <span data-ttu-id="26d9c-115">狀態持續性不是自動的。</span><span class="sxs-lookup"><span data-stu-id="26d9c-115">State persistence isn't automatic.</span></span> <span data-ttu-id="26d9c-116">您必須在開發應用程式時採取步驟，以執行可設定狀態的資料持續性。</span><span class="sxs-lookup"><span data-stu-id="26d9c-116">You must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="26d9c-117">通常只有在使用者需要花費更多時間來建立的高價值狀態下，才需要資料持續性。</span><span class="sxs-lookup"><span data-stu-id="26d9c-117">Data persistence is typically only required for high-value state that users expended effort to create.</span></span> <span data-ttu-id="26d9c-118">在下列範例中，保存狀態可以節省商務工作的時間或輔助：</span><span class="sxs-lookup"><span data-stu-id="26d9c-118">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="26d9c-119">多重步驟的 web 表單：當使用者的狀態遺失時，使用者重新輸入多步驟 web 表單的幾個已完成步驟的資料相當耗時。</span><span class="sxs-lookup"><span data-stu-id="26d9c-119">Multi-step web forms: It's time-consuming for a user to re-enter data for several completed steps of a multi-step web form if their state is lost.</span></span> <span data-ttu-id="26d9c-120">如果使用者離開表單並在稍後返回，則會在此案例中失去狀態。</span><span class="sxs-lookup"><span data-stu-id="26d9c-120">A user loses state in this scenario if they navigate away from the form and return later.</span></span>
* <span data-ttu-id="26d9c-121">購物車：應用程式中任何代表潛在收益的商業重要元件都可以維護。</span><span class="sxs-lookup"><span data-stu-id="26d9c-121">Shopping carts: Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="26d9c-122">如果使用者遺失其狀態，而使用者的購物車可能會在稍後返回網站時購買較少的產品或服務。</span><span class="sxs-lookup"><span data-stu-id="26d9c-122">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="26d9c-123">應用程式只能保存 *應用程式狀態* 。</span><span class="sxs-lookup"><span data-stu-id="26d9c-123">An app can only persist *app state* .</span></span> <span data-ttu-id="26d9c-124">Ui 無法保存，例如元件實例和其轉譯樹狀結構。</span><span class="sxs-lookup"><span data-stu-id="26d9c-124">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="26d9c-125">元件和轉譯樹狀結構通常不是可序列化的。</span><span class="sxs-lookup"><span data-stu-id="26d9c-125">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="26d9c-126">若要保存 UI 狀態，例如樹狀檢視控制項的展開節點，應用程式必須使用自訂程式碼，將 UI 狀態的行為模型化為可序列化的應用程式狀態。</span><span class="sxs-lookup"><span data-stu-id="26d9c-126">To persist UI state, such as the expanded nodes of a tree view control, the app must use custom code to model the behavior of the UI state as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="26d9c-127">保存狀態的位置</span><span class="sxs-lookup"><span data-stu-id="26d9c-127">Where to persist state</span></span>

<span data-ttu-id="26d9c-128">存在於保存狀態的常見位置：</span><span class="sxs-lookup"><span data-stu-id="26d9c-128">Common locations exist for persisting state:</span></span>

* [<span data-ttu-id="26d9c-129">伺服器端儲存空間</span><span class="sxs-lookup"><span data-stu-id="26d9c-129">Server-side storage</span></span>](#server-side-storage)
* [<span data-ttu-id="26d9c-130">URL</span><span class="sxs-lookup"><span data-stu-id="26d9c-130">URL</span></span>](#url)
* [<span data-ttu-id="26d9c-131">瀏覽器儲存體</span><span class="sxs-lookup"><span data-stu-id="26d9c-131">Browser storage</span></span>](#browser-storage)
* [<span data-ttu-id="26d9c-132">記憶體內部狀態容器服務</span><span class="sxs-lookup"><span data-stu-id="26d9c-132">In-memory state container service</span></span>](#in-memory-state-container-service)

### <a name="server-side-storage"></a><span data-ttu-id="26d9c-133">伺服器端儲存空間</span><span class="sxs-lookup"><span data-stu-id="26d9c-133">Server-side storage</span></span>

<span data-ttu-id="26d9c-134">針對跨越多個使用者和裝置的永久資料持續性，應用程式可以使用透過 web API 存取的獨立伺服器端儲存體。</span><span class="sxs-lookup"><span data-stu-id="26d9c-134">For permanent data persistence that spans multiple users and devices, the app can use independent server-side storage accessed via a web API.</span></span> <span data-ttu-id="26d9c-135">這些選項包括：</span><span class="sxs-lookup"><span data-stu-id="26d9c-135">Options include:</span></span>

* <span data-ttu-id="26d9c-136">Blob 儲存體</span><span class="sxs-lookup"><span data-stu-id="26d9c-136">Blob storage</span></span>
* <span data-ttu-id="26d9c-137">機碼值儲存體</span><span class="sxs-lookup"><span data-stu-id="26d9c-137">Key-value storage</span></span>
* <span data-ttu-id="26d9c-138">關聯式資料庫</span><span class="sxs-lookup"><span data-stu-id="26d9c-138">Relational database</span></span>
* <span data-ttu-id="26d9c-139">表格儲存體</span><span class="sxs-lookup"><span data-stu-id="26d9c-139">Table storage</span></span>

<span data-ttu-id="26d9c-140">儲存資料之後，會保留使用者的狀態，並可在任何新的瀏覽器會話中使用。</span><span class="sxs-lookup"><span data-stu-id="26d9c-140">After data is saved, the user's state is retained and available in any new browser session.</span></span>

<span data-ttu-id="26d9c-141">由於 :::no-loc(Blazor WebAssembly)::: 應用程式完全是在使用者的瀏覽器中執行，因此需要額外的措施來存取安全的外部系統，例如儲存體服務和資料庫。</span><span class="sxs-lookup"><span data-stu-id="26d9c-141">Because :::no-loc(Blazor WebAssembly)::: apps run entirely in the user's browser, they require additional measures to access secure external systems, such as storage services and databases.</span></span> <span data-ttu-id="26d9c-142">:::no-loc(Blazor WebAssembly)::: 應用程式的保護方式與單一頁面應用程式 (Spa) 相同。</span><span class="sxs-lookup"><span data-stu-id="26d9c-142">:::no-loc(Blazor WebAssembly)::: apps are secured in the same manner as Single Page Applications (SPAs).</span></span> <span data-ttu-id="26d9c-143">一般而言，應用程式會透過[OAuth](https://oauth.net) / [OpenID Connect (OIDC) ](https://openid.net/connect/)來驗證使用者，然後透過 web API 呼叫，與伺服器端應用程式的服務和資料庫互動。</span><span class="sxs-lookup"><span data-stu-id="26d9c-143">Typically, an app authenticates a user via [OAuth](https://oauth.net)/[OpenID Connect (OIDC)](https://openid.net/connect/) and then interacts with storage services and databases through web API calls to a server-side app.</span></span> <span data-ttu-id="26d9c-144">伺服器端應用程式會協調 :::no-loc(Blazor WebAssembly)::: 應用程式與儲存體服務或資料庫之間的資料傳輸。</span><span class="sxs-lookup"><span data-stu-id="26d9c-144">The server-side app mediates the transfer of data between the :::no-loc(Blazor WebAssembly)::: app and the storage service or database.</span></span> <span data-ttu-id="26d9c-145">:::no-loc(Blazor WebAssembly):::應用程式會維護與伺服器端應用程式的暫時連線，而伺服器端應用程式會持續連線至儲存體。</span><span class="sxs-lookup"><span data-stu-id="26d9c-145">The :::no-loc(Blazor WebAssembly)::: app maintains an ephemeral connection to the server-side app, while the server-side app has a persistent connection to storage.</span></span>

<span data-ttu-id="26d9c-146">如需詳細資訊，請參閱下列資源：</span><span class="sxs-lookup"><span data-stu-id="26d9c-146">For more information, see the following resources:</span></span>

* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>
* <span data-ttu-id="26d9c-147">:::no-loc(Blazor):::*安全性和 :::no-loc(Identity):::* 文章</span><span class="sxs-lookup"><span data-stu-id="26d9c-147">:::no-loc(Blazor)::: *Security and :::no-loc(Identity):::* articles</span></span>

<span data-ttu-id="26d9c-148">如需 Azure 資料儲存體選項的詳細資訊，請參閱下列各項：</span><span class="sxs-lookup"><span data-stu-id="26d9c-148">For more information on Azure data storage options, see the following:</span></span>

* [<span data-ttu-id="26d9c-149">Azure 資料庫</span><span class="sxs-lookup"><span data-stu-id="26d9c-149">Azure Databases</span></span>](https://azure.microsoft.com/product-categories/databases/)
* [<span data-ttu-id="26d9c-150">Azure 儲存體檔</span><span class="sxs-lookup"><span data-stu-id="26d9c-150">Azure Storage Documentation</span></span>](/azure/storage/)

### <a name="url"></a><span data-ttu-id="26d9c-151">URL</span><span class="sxs-lookup"><span data-stu-id="26d9c-151">URL</span></span>

<span data-ttu-id="26d9c-152">若為代表導覽狀態的暫時性資料，請將資料模型為 URL 的一部分。</span><span class="sxs-lookup"><span data-stu-id="26d9c-152">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="26d9c-153">在 URL 中模型化使用者狀態的範例包括：</span><span class="sxs-lookup"><span data-stu-id="26d9c-153">Examples of user state modeled in the URL include:</span></span>

* <span data-ttu-id="26d9c-154">已查看之實體的識別碼。</span><span class="sxs-lookup"><span data-stu-id="26d9c-154">The ID of a viewed entity.</span></span>
* <span data-ttu-id="26d9c-155">分頁方格中的目前頁碼。</span><span class="sxs-lookup"><span data-stu-id="26d9c-155">The current page number in a paged grid.</span></span>

<span data-ttu-id="26d9c-156">如果使用者以手動方式重載網頁，則會保留瀏覽器網址列的內容。</span><span class="sxs-lookup"><span data-stu-id="26d9c-156">The contents of the browser's address bar are retained if the user manually reloads the page.</span></span>

<span data-ttu-id="26d9c-157">如需使用指示詞定義 URL 模式的詳細資訊 [`@page`](xref:mvc/views/razor#page) ，請參閱 <xref:blazor/fundamentals/routing> 。</span><span class="sxs-lookup"><span data-stu-id="26d9c-157">For information on defining URL patterns with the [`@page`](xref:mvc/views/razor#page) directive, see <xref:blazor/fundamentals/routing>.</span></span>

### <a name="browser-storage"></a><span data-ttu-id="26d9c-158">瀏覽器儲存體</span><span class="sxs-lookup"><span data-stu-id="26d9c-158">Browser storage</span></span>

<span data-ttu-id="26d9c-159">對於使用者主動建立的暫時性資料，常用的儲存位置是瀏覽器 [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) 和 [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) 集合：</span><span class="sxs-lookup"><span data-stu-id="26d9c-159">For transient data that the user is actively creating, a commonly used storage location is the browser's [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) collections:</span></span>

* <span data-ttu-id="26d9c-160">`localStorage` 範圍設定為瀏覽器的視窗。</span><span class="sxs-lookup"><span data-stu-id="26d9c-160">`localStorage` is scoped to the browser's window.</span></span> <span data-ttu-id="26d9c-161">如果使用者重載頁面或關閉並重新開啟瀏覽器，則狀態會持續存在。</span><span class="sxs-lookup"><span data-stu-id="26d9c-161">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="26d9c-162">如果使用者開啟多個瀏覽器索引標籤，則會在索引標籤之間共用狀態。</span><span class="sxs-lookup"><span data-stu-id="26d9c-162">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="26d9c-163">資料會持續存在， `localStorage` 直到明確清除為止。</span><span class="sxs-lookup"><span data-stu-id="26d9c-163">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="26d9c-164">`sessionStorage` 的範圍設定為 [瀏覽器] 索引標籤。如果使用者重載此索引標籤，則狀態會持續存在。</span><span class="sxs-lookup"><span data-stu-id="26d9c-164">`sessionStorage` is scoped to the browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="26d9c-165">如果使用者關閉索引標籤或瀏覽器，狀態就會遺失。</span><span class="sxs-lookup"><span data-stu-id="26d9c-165">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="26d9c-166">如果使用者開啟多個瀏覽器索引標籤，每個索引標籤都有自己獨立的資料版本。</span><span class="sxs-lookup"><span data-stu-id="26d9c-166">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

> [!NOTE]
> <span data-ttu-id="26d9c-167">`localStorage` 和 `sessionStorage` 可以在應用程式中使用， :::no-loc(Blazor WebAssembly)::: 但只能透過撰寫自訂程式碼或使用協力廠商套件。</span><span class="sxs-lookup"><span data-stu-id="26d9c-167">`localStorage` and `sessionStorage` can be used in :::no-loc(Blazor WebAssembly)::: apps but only by writing custom code or using a third-party package.</span></span>

<span data-ttu-id="26d9c-168">一般而言， `sessionStorage` 使用會比較安全。</span><span class="sxs-lookup"><span data-stu-id="26d9c-168">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="26d9c-169">`sessionStorage` 避免使用者開啟多個索引標籤，並遇到下列情況的風險：</span><span class="sxs-lookup"><span data-stu-id="26d9c-169">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="26d9c-170">所有索引標籤的狀態儲存區中的 bug。</span><span class="sxs-lookup"><span data-stu-id="26d9c-170">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="26d9c-171">當索引標籤覆寫其他索引標籤的狀態時，有混淆的行為。</span><span class="sxs-lookup"><span data-stu-id="26d9c-171">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="26d9c-172">`localStorage` 如果應用程式必須在關閉並重新開啟瀏覽器時保存狀態，則這是較佳的選擇。</span><span class="sxs-lookup"><span data-stu-id="26d9c-172">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

> [!WARNING]
> <span data-ttu-id="26d9c-173">使用者可能會看到或篡改儲存在和中的資料 `localStorage` `sessionStorage` 。</span><span class="sxs-lookup"><span data-stu-id="26d9c-173">Users may view or tamper with the data stored in `localStorage` and `sessionStorage`.</span></span>

## <a name="in-memory-state-container-service"></a><span data-ttu-id="26d9c-174">記憶體內部狀態容器服務</span><span class="sxs-lookup"><span data-stu-id="26d9c-174">In-memory state container service</span></span>

[!INCLUDE[](~/includes/blazor-state-management/state-container.md)]

## <a name="additional-resources"></a><span data-ttu-id="26d9c-175">其他資源</span><span class="sxs-lookup"><span data-stu-id="26d9c-175">Additional resources</span></span>

* [<span data-ttu-id="26d9c-176">在驗證操作之前儲存應用程式狀態</span><span class="sxs-lookup"><span data-stu-id="26d9c-176">Save app state before an authentication operation</span></span>](xref:blazor/security/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation)
* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>

::: zone-end

::: zone pivot="server"

<span data-ttu-id="26d9c-177">:::no-loc(Blazor Server)::: 是具狀態的應用程式架構。</span><span class="sxs-lookup"><span data-stu-id="26d9c-177">:::no-loc(Blazor Server)::: is a stateful app framework.</span></span> <span data-ttu-id="26d9c-178">大部分的情況下，應用程式會維護與伺服器的連線。</span><span class="sxs-lookup"><span data-stu-id="26d9c-178">Most of the time, the app maintains a connection to the server.</span></span> <span data-ttu-id="26d9c-179">使用者的狀態會保留在 *伺服器的記憶體中。*</span><span class="sxs-lookup"><span data-stu-id="26d9c-179">The user's state is held in the server's memory in a *circuit* .</span></span> 

<span data-ttu-id="26d9c-180">線路中保留的使用者狀態範例包括：</span><span class="sxs-lookup"><span data-stu-id="26d9c-180">Examples of user state held in a circuit include:</span></span>

* <span data-ttu-id="26d9c-181">轉譯的 UI 中元件實例及其最新轉譯輸出的階層。</span><span class="sxs-lookup"><span data-stu-id="26d9c-181">The hierarchy of component instances and their most recent render output in the rendered UI.</span></span>
* <span data-ttu-id="26d9c-182">元件實例中欄位和屬性的值。</span><span class="sxs-lookup"><span data-stu-id="26d9c-182">The values of fields and properties in component instances.</span></span>
* <span data-ttu-id="26d9c-183">保存在相依性插入中的資料 [ (DI) ](xref:fundamentals/dependency-injection) 範圍設定為線路的服務實例。</span><span class="sxs-lookup"><span data-stu-id="26d9c-183">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span>

<span data-ttu-id="26d9c-184">您也可以透過 [javascript interop](xref:blazor/call-javascript-from-dotnet) 呼叫，在瀏覽器記憶體集中的 javascript 變數中找到使用者狀態。</span><span class="sxs-lookup"><span data-stu-id="26d9c-184">User state might also be found in JavaScript variables in the browser's memory set via [JavaScript interop](xref:blazor/call-javascript-from-dotnet) calls.</span></span>

<span data-ttu-id="26d9c-185">如果使用者遇到暫時性的網路連接遺失， :::no-loc(Blazor)::: 會嘗試將使用者重新連接到其原始狀態的原始線路。</span><span class="sxs-lookup"><span data-stu-id="26d9c-185">If a user experiences a temporary network connection loss, :::no-loc(Blazor)::: attempts to reconnect the user to their original circuit with their original state.</span></span> <span data-ttu-id="26d9c-186">不過，不一定能夠將使用者重新連接到其在伺服器記憶體中的原始線路：</span><span class="sxs-lookup"><span data-stu-id="26d9c-186">However, reconnecting a user to their original circuit in the server's memory isn't always possible:</span></span>

* <span data-ttu-id="26d9c-187">伺服器無法永遠保留中斷連線的電路。</span><span class="sxs-lookup"><span data-stu-id="26d9c-187">The server can't retain a disconnected circuit forever.</span></span> <span data-ttu-id="26d9c-188">伺服器必須在超時之後或伺服器處於記憶體不足的壓力之後釋放中斷連接的線路。</span><span class="sxs-lookup"><span data-stu-id="26d9c-188">The server must release a disconnected circuit after a timeout or when the server is under memory pressure.</span></span>
* <span data-ttu-id="26d9c-189">在多伺服器的負載平衡部署環境中，個別的伺服器可能會失敗，或在不再需要處理要求的整體數量時自動移除。</span><span class="sxs-lookup"><span data-stu-id="26d9c-189">In multi-server, load-balanced deployment environments, individual servers may fail or be automatically removed when no longer required to handle the overall volume of requests.</span></span> <span data-ttu-id="26d9c-190">當使用者嘗試重新連接時，使用者的原始伺服器處理要求可能會變成無法使用。</span><span class="sxs-lookup"><span data-stu-id="26d9c-190">The original server processing requests for a user may become unavailable when the user attempts to reconnect.</span></span>
* <span data-ttu-id="26d9c-191">使用者可能會關閉並重新開啟其瀏覽器或重載頁面，以移除瀏覽器記憶體中保留的任何狀態。</span><span class="sxs-lookup"><span data-stu-id="26d9c-191">The user might close and re-open their browser or reload the page, which removes any state held in the browser's memory.</span></span> <span data-ttu-id="26d9c-192">例如，透過 JavaScript interop 呼叫設定的 JavaScript 變數值會遺失。</span><span class="sxs-lookup"><span data-stu-id="26d9c-192">For example, JavaScript variable values set through JavaScript interop calls are lost.</span></span>

<span data-ttu-id="26d9c-193">當使用者無法重新連接到其原始線路時，使用者會收到具有空白狀態的新電路。</span><span class="sxs-lookup"><span data-stu-id="26d9c-193">When a user can't be reconnected to their original circuit, the user receives a new circuit with an empty state.</span></span> <span data-ttu-id="26d9c-194">這相當於關閉並重新開啟桌面應用程式。</span><span class="sxs-lookup"><span data-stu-id="26d9c-194">This is equivalent to closing and re-opening a desktop app.</span></span>

## <a name="persist-state-across-circuits"></a><span data-ttu-id="26d9c-195">跨線路保存狀態</span><span class="sxs-lookup"><span data-stu-id="26d9c-195">Persist state across circuits</span></span>

<span data-ttu-id="26d9c-196">一般來說，在使用者主動建立資料的線路上維護狀態，而不只是讀取已經存在的資料。</span><span class="sxs-lookup"><span data-stu-id="26d9c-196">Generally, maintain state across circuits where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="26d9c-197">若要保留跨電路的狀態，應用程式必須將資料保存到與伺服器記憶體不同的儲存位置。</span><span class="sxs-lookup"><span data-stu-id="26d9c-197">To preserve state across circuits, the app must persist the data to some other storage location than the server's memory.</span></span> <span data-ttu-id="26d9c-198">狀態持續性不是自動的。</span><span class="sxs-lookup"><span data-stu-id="26d9c-198">State persistence isn't automatic.</span></span> <span data-ttu-id="26d9c-199">您必須在開發應用程式時採取步驟，以執行可設定狀態的資料持續性。</span><span class="sxs-lookup"><span data-stu-id="26d9c-199">You must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="26d9c-200">通常只有在使用者需要花費更多時間來建立的高價值狀態下，才需要資料持續性。</span><span class="sxs-lookup"><span data-stu-id="26d9c-200">Data persistence is typically only required for high-value state that users expended effort to create.</span></span> <span data-ttu-id="26d9c-201">在下列範例中，保存狀態可以節省商務工作的時間或輔助：</span><span class="sxs-lookup"><span data-stu-id="26d9c-201">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="26d9c-202">多重步驟的 web 表單：當使用者的狀態遺失時，使用者重新輸入多步驟 web 表單的幾個已完成步驟的資料相當耗時。</span><span class="sxs-lookup"><span data-stu-id="26d9c-202">Multi-step web forms: It's time-consuming for a user to re-enter data for several completed steps of a multi-step web form if their state is lost.</span></span> <span data-ttu-id="26d9c-203">如果使用者離開表單並在稍後返回，則會在此案例中失去狀態。</span><span class="sxs-lookup"><span data-stu-id="26d9c-203">A user loses state in this scenario if they navigate away from the form and return later.</span></span>
* <span data-ttu-id="26d9c-204">購物車：應用程式中任何代表潛在收益的商業重要元件都可以維護。</span><span class="sxs-lookup"><span data-stu-id="26d9c-204">Shopping carts: Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="26d9c-205">如果使用者遺失其狀態，而使用者的購物車可能會在稍後返回網站時購買較少的產品或服務。</span><span class="sxs-lookup"><span data-stu-id="26d9c-205">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="26d9c-206">應用程式只能保存 *應用程式狀態* 。</span><span class="sxs-lookup"><span data-stu-id="26d9c-206">An app can only persist *app state* .</span></span> <span data-ttu-id="26d9c-207">Ui 無法保存，例如元件實例和其轉譯樹狀結構。</span><span class="sxs-lookup"><span data-stu-id="26d9c-207">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="26d9c-208">元件和轉譯樹狀結構通常不是可序列化的。</span><span class="sxs-lookup"><span data-stu-id="26d9c-208">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="26d9c-209">若要保存 UI 狀態，例如樹狀檢視控制項的展開節點，應用程式必須使用自訂程式碼，將 UI 狀態的行為模型化為可序列化的應用程式狀態。</span><span class="sxs-lookup"><span data-stu-id="26d9c-209">To persist UI state, such as the expanded nodes of a tree view control, the app must use custom code to model the behavior of the UI state as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="26d9c-210">保存狀態的位置</span><span class="sxs-lookup"><span data-stu-id="26d9c-210">Where to persist state</span></span>

<span data-ttu-id="26d9c-211">存在於保存狀態的常見位置：</span><span class="sxs-lookup"><span data-stu-id="26d9c-211">Common locations exist for persisting state:</span></span>

* [<span data-ttu-id="26d9c-212">伺服器端儲存空間</span><span class="sxs-lookup"><span data-stu-id="26d9c-212">Server-side storage</span></span>](#server-side-storage)
* [<span data-ttu-id="26d9c-213">URL</span><span class="sxs-lookup"><span data-stu-id="26d9c-213">URL</span></span>](#url)
* [<span data-ttu-id="26d9c-214">瀏覽器儲存體</span><span class="sxs-lookup"><span data-stu-id="26d9c-214">Browser storage</span></span>](#browser-storage)
* [<span data-ttu-id="26d9c-215">記憶體內部狀態容器服務</span><span class="sxs-lookup"><span data-stu-id="26d9c-215">In-memory state container service</span></span>](#in-memory-state-container-service)

### <a name="server-side-storage"></a><span data-ttu-id="26d9c-216">伺服器端儲存空間</span><span class="sxs-lookup"><span data-stu-id="26d9c-216">Server-side storage</span></span>

<span data-ttu-id="26d9c-217">針對跨越多個使用者和裝置的永久資料持續性，應用程式可以使用伺服器端儲存體。</span><span class="sxs-lookup"><span data-stu-id="26d9c-217">For permanent data persistence that spans multiple users and devices, the app can use server-side storage.</span></span> <span data-ttu-id="26d9c-218">這些選項包括：</span><span class="sxs-lookup"><span data-stu-id="26d9c-218">Options include:</span></span>

* <span data-ttu-id="26d9c-219">Blob 儲存體</span><span class="sxs-lookup"><span data-stu-id="26d9c-219">Blob storage</span></span>
* <span data-ttu-id="26d9c-220">機碼值儲存體</span><span class="sxs-lookup"><span data-stu-id="26d9c-220">Key-value storage</span></span>
* <span data-ttu-id="26d9c-221">關聯式資料庫</span><span class="sxs-lookup"><span data-stu-id="26d9c-221">Relational database</span></span>
* <span data-ttu-id="26d9c-222">表格儲存體</span><span class="sxs-lookup"><span data-stu-id="26d9c-222">Table storage</span></span>

<span data-ttu-id="26d9c-223">儲存資料之後，會保留使用者的狀態，並可在任何新的線路中使用。</span><span class="sxs-lookup"><span data-stu-id="26d9c-223">After data is saved, the user's state is retained and available in any new circuit.</span></span>

<span data-ttu-id="26d9c-224">如需 Azure 資料儲存體選項的詳細資訊，請參閱下列各項：</span><span class="sxs-lookup"><span data-stu-id="26d9c-224">For more information on Azure data storage options, see the following:</span></span>

* [<span data-ttu-id="26d9c-225">Azure 資料庫</span><span class="sxs-lookup"><span data-stu-id="26d9c-225">Azure Databases</span></span>](https://azure.microsoft.com/product-categories/databases/)
* [<span data-ttu-id="26d9c-226">Azure 儲存體檔</span><span class="sxs-lookup"><span data-stu-id="26d9c-226">Azure Storage Documentation</span></span>](/azure/storage/)

### <a name="url"></a><span data-ttu-id="26d9c-227">URL</span><span class="sxs-lookup"><span data-stu-id="26d9c-227">URL</span></span>

<span data-ttu-id="26d9c-228">若為代表導覽狀態的暫時性資料，請將資料模型為 URL 的一部分。</span><span class="sxs-lookup"><span data-stu-id="26d9c-228">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="26d9c-229">在 URL 中模型化使用者狀態的範例包括：</span><span class="sxs-lookup"><span data-stu-id="26d9c-229">Examples of user state modeled in the URL include:</span></span>

* <span data-ttu-id="26d9c-230">已查看之實體的識別碼。</span><span class="sxs-lookup"><span data-stu-id="26d9c-230">The ID of a viewed entity.</span></span>
* <span data-ttu-id="26d9c-231">分頁方格中的目前頁碼。</span><span class="sxs-lookup"><span data-stu-id="26d9c-231">The current page number in a paged grid.</span></span>

<span data-ttu-id="26d9c-232">瀏覽器網址列的內容會保留：</span><span class="sxs-lookup"><span data-stu-id="26d9c-232">The contents of the browser's address bar are retained:</span></span>

* <span data-ttu-id="26d9c-233">如果使用者手動重載頁面。</span><span class="sxs-lookup"><span data-stu-id="26d9c-233">If the user manually reloads the page.</span></span>
* <span data-ttu-id="26d9c-234">如果網頁伺服器變得無法使用，則會強制使用者重載頁面，以便連接到不同的伺服器。</span><span class="sxs-lookup"><span data-stu-id="26d9c-234">If the web server becomes unavailable, and the user is forced to reload the page in order to connect to a different server.</span></span>

<span data-ttu-id="26d9c-235">如需使用指示詞定義 URL 模式的詳細資訊 [`@page`](xref:mvc/views/razor#page) ，請參閱 <xref:blazor/fundamentals/routing> 。</span><span class="sxs-lookup"><span data-stu-id="26d9c-235">For information on defining URL patterns with the [`@page`](xref:mvc/views/razor#page) directive, see <xref:blazor/fundamentals/routing>.</span></span>

### <a name="browser-storage"></a><span data-ttu-id="26d9c-236">瀏覽器儲存體</span><span class="sxs-lookup"><span data-stu-id="26d9c-236">Browser storage</span></span>

<span data-ttu-id="26d9c-237">對於使用者主動建立的暫時性資料，常用的儲存位置是瀏覽器 [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) 和 [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) 集合：</span><span class="sxs-lookup"><span data-stu-id="26d9c-237">For transient data that the user is actively creating, a commonly used storage location is the browser's [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) collections:</span></span>

* <span data-ttu-id="26d9c-238">`localStorage` 範圍設定為瀏覽器的視窗。</span><span class="sxs-lookup"><span data-stu-id="26d9c-238">`localStorage` is scoped to the browser's window.</span></span> <span data-ttu-id="26d9c-239">如果使用者重載頁面或關閉並重新開啟瀏覽器，則狀態會持續存在。</span><span class="sxs-lookup"><span data-stu-id="26d9c-239">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="26d9c-240">如果使用者開啟多個瀏覽器索引標籤，則會在索引標籤之間共用狀態。</span><span class="sxs-lookup"><span data-stu-id="26d9c-240">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="26d9c-241">資料會持續存在， `localStorage` 直到明確清除為止。</span><span class="sxs-lookup"><span data-stu-id="26d9c-241">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="26d9c-242">`sessionStorage` 的範圍設定為 [瀏覽器] 索引標籤。如果使用者重載此索引標籤，則狀態會持續存在。</span><span class="sxs-lookup"><span data-stu-id="26d9c-242">`sessionStorage` is scoped to the browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="26d9c-243">如果使用者關閉索引標籤或瀏覽器，狀態就會遺失。</span><span class="sxs-lookup"><span data-stu-id="26d9c-243">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="26d9c-244">如果使用者開啟多個瀏覽器索引標籤，每個索引標籤都有自己獨立的資料版本。</span><span class="sxs-lookup"><span data-stu-id="26d9c-244">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

<span data-ttu-id="26d9c-245">一般而言， `sessionStorage` 使用會比較安全。</span><span class="sxs-lookup"><span data-stu-id="26d9c-245">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="26d9c-246">`sessionStorage` 避免使用者開啟多個索引標籤，並遇到下列情況的風險：</span><span class="sxs-lookup"><span data-stu-id="26d9c-246">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="26d9c-247">所有索引標籤的狀態儲存區中的 bug。</span><span class="sxs-lookup"><span data-stu-id="26d9c-247">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="26d9c-248">當索引標籤覆寫其他索引標籤的狀態時，有混淆的行為。</span><span class="sxs-lookup"><span data-stu-id="26d9c-248">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="26d9c-249">`localStorage` 如果應用程式必須在關閉並重新開啟瀏覽器時保存狀態，則這是較佳的選擇。</span><span class="sxs-lookup"><span data-stu-id="26d9c-249">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

<span data-ttu-id="26d9c-250">使用瀏覽器存放裝置的注意事項：</span><span class="sxs-lookup"><span data-stu-id="26d9c-250">Caveats for using browser storage:</span></span>

* <span data-ttu-id="26d9c-251">與使用伺服器端資料庫類似，載入和儲存資料是非同步。</span><span class="sxs-lookup"><span data-stu-id="26d9c-251">Similar to the use of a server-side database, loading and saving data are asynchronous.</span></span>
* <span data-ttu-id="26d9c-252">與伺服器端資料庫不同的是，在預先呈現期間，儲存體無法使用，因為在預先設定的階段中，要求的頁面不存在於瀏覽器中。</span><span class="sxs-lookup"><span data-stu-id="26d9c-252">Unlike a server-side database, storage isn't available during prerendering because the requested page doesn't exist in the browser during the prerendering stage.</span></span>
* <span data-ttu-id="26d9c-253">儲存幾 kb 的資料可合理保存 :::no-loc(Blazor Server)::: 應用程式。</span><span class="sxs-lookup"><span data-stu-id="26d9c-253">Storage of a few kilobytes of data is reasonable to persist for :::no-loc(Blazor Server)::: apps.</span></span> <span data-ttu-id="26d9c-254">除了幾 kb 以外，您還必須考慮效能影響，因為資料是在網路上載入和儲存。</span><span class="sxs-lookup"><span data-stu-id="26d9c-254">Beyond a few kilobytes, you must consider the performance implications because the data is loaded and saved across the network.</span></span>
* <span data-ttu-id="26d9c-255">使用者可能會看到或篡改資料。</span><span class="sxs-lookup"><span data-stu-id="26d9c-255">Users may view or tamper with the data.</span></span> <span data-ttu-id="26d9c-256">[ASP.NET Core 資料保護](xref:security/data-protection/introduction) 可減輕風險。</span><span class="sxs-lookup"><span data-stu-id="26d9c-256">[ASP.NET Core Data Protection](xref:security/data-protection/introduction) can mitigate the risk.</span></span> <span data-ttu-id="26d9c-257">例如， [ASP.NET Core 受保護的瀏覽器儲存體](#aspnet-core-protected-browser-storage) 使用 ASP.NET Core 資料保護。</span><span class="sxs-lookup"><span data-stu-id="26d9c-257">For example, [ASP.NET Core Protected Browser Storage](#aspnet-core-protected-browser-storage) uses ASP.NET Core Data Protection.</span></span>

<span data-ttu-id="26d9c-258">協力廠商 NuGet 套件提供使用和的 Api `localStorage` `sessionStorage` 。</span><span class="sxs-lookup"><span data-stu-id="26d9c-258">Third-party NuGet packages provide APIs for working with `localStorage` and `sessionStorage`.</span></span> <span data-ttu-id="26d9c-259">值得考慮選擇明確使用 [ASP.NET Core 資料保護](xref:security/data-protection/introduction)的封裝。</span><span class="sxs-lookup"><span data-stu-id="26d9c-259">It's worth considering choosing a package that transparently uses [ASP.NET Core Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="26d9c-260">資料保護會加密儲存的資料，並減少篡改儲存資料的潛在風險。</span><span class="sxs-lookup"><span data-stu-id="26d9c-260">Data Protection encrypts stored data and reduces the potential risk of tampering with stored data.</span></span> <span data-ttu-id="26d9c-261">如果以純文字儲存 JSON 序列化資料，使用者就可以使用瀏覽器開發人員工具來查看資料，也可以修改儲存的資料。</span><span class="sxs-lookup"><span data-stu-id="26d9c-261">If JSON-serialized data is stored in plain text, users can see the data using browser developer tools and also modify the stored data.</span></span> <span data-ttu-id="26d9c-262">保護資料並不一定會造成問題，因為資料在本質上可能很簡單。</span><span class="sxs-lookup"><span data-stu-id="26d9c-262">Securing data isn't always a problem because the data might be trivial in nature.</span></span> <span data-ttu-id="26d9c-263">例如，讀取或修改 UI 元素的預存色彩，對於使用者或組織而言並不是重大的安全性風險。</span><span class="sxs-lookup"><span data-stu-id="26d9c-263">For example, reading or modifying the stored color of a UI element isn't a significant security risk to the user or the organization.</span></span> <span data-ttu-id="26d9c-264">避免讓使用者檢查或篡改 *機密資料* 。</span><span class="sxs-lookup"><span data-stu-id="26d9c-264">Avoid allowing users to inspect or tamper with *sensitive data* .</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="aspnet-core-protected-browser-storage"></a><span data-ttu-id="26d9c-265">ASP.NET Core 受保護的瀏覽器儲存體</span><span class="sxs-lookup"><span data-stu-id="26d9c-265">ASP.NET Core Protected Browser Storage</span></span>

<span data-ttu-id="26d9c-266">ASP.NET Core 受保護的瀏覽器儲存體會利用和的 [ASP.NET Core 資料保護](xref:security/data-protection/introduction) [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) 。</span><span class="sxs-lookup"><span data-stu-id="26d9c-266">ASP.NET Core Protected Browser Storage leverages [ASP.NET Core Data Protection](xref:security/data-protection/introduction) for [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).</span></span>

> [!NOTE]
> <span data-ttu-id="26d9c-267">受保護的瀏覽器存放裝置依賴 ASP.NET Core 資料保護，且僅支援 :::no-loc(Blazor Server)::: 應用程式。</span><span class="sxs-lookup"><span data-stu-id="26d9c-267">Protected Browser Storage relies on ASP.NET Core Data Protection and is only supported for :::no-loc(Blazor Server)::: apps.</span></span>

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="26d9c-268">儲存並載入元件中的資料</span><span class="sxs-lookup"><span data-stu-id="26d9c-268">Save and load data within a component</span></span>

<span data-ttu-id="26d9c-269">在需要將資料載入或儲存至瀏覽器儲存體的任何元件中，請使用指示詞 [`@inject`](xref:mvc/views/razor#inject) 插入下列任一項的實例：</span><span class="sxs-lookup"><span data-stu-id="26d9c-269">In any component that requires loading or saving data to browser storage, use the [`@inject`](xref:mvc/views/razor#inject) directive to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="26d9c-270">選擇取決於您想要使用的瀏覽器儲存位置。</span><span class="sxs-lookup"><span data-stu-id="26d9c-270">The choice depends on which browser storage location you wish to use.</span></span> <span data-ttu-id="26d9c-271">在下列範例中， `sessionStorage` 會使用：</span><span class="sxs-lookup"><span data-stu-id="26d9c-271">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="26d9c-272">指示詞 `@using` 可以放在應用程式的檔案中，而不是放在 `_Imports.razor` 元件中。</span><span class="sxs-lookup"><span data-stu-id="26d9c-272">The `@using` directive can be placed in the app's `_Imports.razor` file instead of in the component.</span></span> <span data-ttu-id="26d9c-273">使用檔案可 `_Imports.razor` 讓應用程式的較大區段或整個應用程式使用命名空間。</span><span class="sxs-lookup"><span data-stu-id="26d9c-273">Use of the `_Imports.razor` file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="26d9c-274">若要在以 `currentCount` `Counter` 專案範本為基礎的應用程式元件中保存值 :::no-loc(Blazor Server)::: ，請修改 `IncrementCount` 要使用的方法 `ProtectedSessionStore.SetAsync` ：</span><span class="sxs-lookup"><span data-stu-id="26d9c-274">To persist the `currentCount` value in the `Counter` component of an app based on the :::no-loc(Blazor Server)::: project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="26d9c-275">在更大、更實際的應用程式中，個別欄位的儲存是不太可能的情況。</span><span class="sxs-lookup"><span data-stu-id="26d9c-275">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="26d9c-276">應用程式更有可能儲存包含複雜狀態的整個模型物件。</span><span class="sxs-lookup"><span data-stu-id="26d9c-276">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="26d9c-277">`ProtectedSessionStore` 自動序列化和還原序列化 JSON 資料，以儲存複雜的狀態物件。</span><span class="sxs-lookup"><span data-stu-id="26d9c-277">`ProtectedSessionStore` automatically serializes and deserializes JSON data to store complex state objects.</span></span>

<span data-ttu-id="26d9c-278">在上述程式碼範例中， `currentCount` 資料會儲存 `sessionStorage['count']` 在使用者的瀏覽器中。</span><span class="sxs-lookup"><span data-stu-id="26d9c-278">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="26d9c-279">資料不會以純文字儲存，而是使用 ASP.NET Core 資料保護來保護。</span><span class="sxs-lookup"><span data-stu-id="26d9c-279">The data isn't stored in plain text but rather is protected using ASP.NET Core Data Protection.</span></span> <span data-ttu-id="26d9c-280">如果 `sessionStorage['count']` 在瀏覽器的開發人員主控台中評估，則可以檢查加密的資料。</span><span class="sxs-lookup"><span data-stu-id="26d9c-280">The encrypted data can be inspected if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="26d9c-281">若要在 `currentCount` 使用者稍後返回元件時復原資料 `Counter` ，包括使用者是否在新線路上，請使用 `ProtectedSessionStore.GetAsync` ：</span><span class="sxs-lookup"><span data-stu-id="26d9c-281">To recover the `currentCount` data if the user returns to the `Counter` component later, including if the user is on a new circuit, use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    var result = await ProtectedSessionStore.GetAsync<int>("count");
    currentCount = result.Success ? result.Value : 0;
}
```

<span data-ttu-id="26d9c-282">如果元件的參數包含流覽狀態，請呼叫 `ProtectedSessionStore.GetAsync` 並指派非 `null` 結果中的 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> ，而不是 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 。</span><span class="sxs-lookup"><span data-stu-id="26d9c-282">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign a non-`null` result in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, not <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span> <span data-ttu-id="26d9c-283"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 只有在第一次具現化元件時，才會呼叫一次。</span><span class="sxs-lookup"><span data-stu-id="26d9c-283"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is only called once when the component is first instantiated.</span></span> <span data-ttu-id="26d9c-284"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 如果使用者流覽至不同的 URL，並在相同頁面上剩餘，則不會再次呼叫。</span><span class="sxs-lookup"><span data-stu-id="26d9c-284"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="26d9c-285">如需詳細資訊，請參閱<xref:blazor/components/lifecycle>。</span><span class="sxs-lookup"><span data-stu-id="26d9c-285">For more information, see <xref:blazor/components/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="26d9c-286">此區段中的範例只適用于伺服器未啟用可呈現的情況。</span><span class="sxs-lookup"><span data-stu-id="26d9c-286">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="26d9c-287">啟用自動呈現之後，會產生錯誤，說明因為正在資源清單元件，所以無法發出 JavaScript interop 呼叫。</span><span class="sxs-lookup"><span data-stu-id="26d9c-287">With prerendering enabled, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>
>
> <span data-ttu-id="26d9c-288">停用已呈現或新增額外的程式碼，以使用可呈現的。</span><span class="sxs-lookup"><span data-stu-id="26d9c-288">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="26d9c-289">若要深入瞭解如何撰寫可搭配可進行處理的程式碼，請參閱處理常式性的 [ [處理](#handle-prerendering) 程式] 區段</span><span class="sxs-lookup"><span data-stu-id="26d9c-289">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="26d9c-290">處理載入狀態</span><span class="sxs-lookup"><span data-stu-id="26d9c-290">Handle the loading state</span></span>

<span data-ttu-id="26d9c-291">由於瀏覽器儲存體會透過網路連線以非同步方式存取，因此在載入資料並可供元件使用之前，一律會有一段時間。</span><span class="sxs-lookup"><span data-stu-id="26d9c-291">Since browser storage is accessed asynchronously over a network connection, there's always a period of time before the data is loaded and available to a component.</span></span> <span data-ttu-id="26d9c-292">為了獲得最佳結果，在載入過程中轉譯載入狀態訊息，而不是顯示空白或預設的資料。</span><span class="sxs-lookup"><span data-stu-id="26d9c-292">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="26d9c-293">其中一個方法是追蹤資料是否為 `null` ，這表示資料仍在載入中。</span><span class="sxs-lookup"><span data-stu-id="26d9c-293">One approach is to track whether the data is `null`, which means that the data is still loading.</span></span> <span data-ttu-id="26d9c-294">在預設 `Counter` 元件中，計數會保留在中 `int` 。</span><span class="sxs-lookup"><span data-stu-id="26d9c-294">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="26d9c-295">藉由將問號 () 新增至類型 () 來[建立 `currentCount` 可為 null](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) `?` `int` ：</span><span class="sxs-lookup"><span data-stu-id="26d9c-295">[Make `currentCount` nullable](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="26d9c-296">**`Increment`** 只有在透過檢查來載入資料時，才會顯示這些元素，而不是無條件地顯示計數和按鈕 <xref:System.Nullable%601.HasValue%2A> ：</span><span class="sxs-lookup"><span data-stu-id="26d9c-296">Instead of unconditionally displaying the count and **`Increment`** button, display these elements only if the data is loaded by checking <xref:System.Nullable%601.HasValue%2A>:</span></span>

```razor
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a><span data-ttu-id="26d9c-297">處理已呈現</span><span class="sxs-lookup"><span data-stu-id="26d9c-297">Handle prerendering</span></span>

<span data-ttu-id="26d9c-298">在呈現期間：</span><span class="sxs-lookup"><span data-stu-id="26d9c-298">During prerendering:</span></span>

* <span data-ttu-id="26d9c-299">與使用者瀏覽器的互動式連接不存在。</span><span class="sxs-lookup"><span data-stu-id="26d9c-299">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="26d9c-300">瀏覽器還沒有可執行 JavaScript 程式碼的頁面。</span><span class="sxs-lookup"><span data-stu-id="26d9c-300">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="26d9c-301">`localStorage` 或 `sessionStorage` 無法在預進行期間使用。</span><span class="sxs-lookup"><span data-stu-id="26d9c-301">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="26d9c-302">如果元件嘗試與儲存體互動，就會產生錯誤，說明因為正在資源清單元件，所以無法發出 JavaScript interop 呼叫。</span><span class="sxs-lookup"><span data-stu-id="26d9c-302">If the component attempts to interact with storage, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>

<span data-ttu-id="26d9c-303">解決此錯誤的其中一種方式是停用已呈現的。</span><span class="sxs-lookup"><span data-stu-id="26d9c-303">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="26d9c-304">如果應用程式大量使用以瀏覽器為基礎的儲存體，這通常是最佳選擇。</span><span class="sxs-lookup"><span data-stu-id="26d9c-304">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="26d9c-305">由於應用程式無法取得任何有用的內容，直到 `localStorage` 或可供使用，因此會導致應用程式不會讓應用程式受益 `sessionStorage` 。</span><span class="sxs-lookup"><span data-stu-id="26d9c-305">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="26d9c-306">若要停用已呈現，請開啟檔案， `Pages/_Host.cshtml` 然後將 `render-mode` [元件標記](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) 協助程式的屬性變更為 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> ：</span><span class="sxs-lookup"><span data-stu-id="26d9c-306">To disable prerendering, open the `Pages/_Host.cshtml` file and change the `render-mode` attribute of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>:</span></span>

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

<span data-ttu-id="26d9c-307">針對不使用或的其他頁面，可進行的自動呈現可能很有用 `localStorage` `sessionStorage` 。</span><span class="sxs-lookup"><span data-stu-id="26d9c-307">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="26d9c-308">若要保留已呈現，請延遲載入作業，直到瀏覽器連線至線路為止。</span><span class="sxs-lookup"><span data-stu-id="26d9c-308">To retain prerendering, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="26d9c-309">以下是儲存計數器值的範例：</span><span class="sxs-lookup"><span data-stu-id="26d9c-309">The following is an example for storing a counter value:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

@if (isConnected)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}

@code {
    private int currentCount;
    private bool isConnected;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        var result = await ProtectedLocalStore.GetAsync<int>("count");
        currentCount = result.Success ? result.Value : 0;
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedLocalStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="26d9c-310">將狀態保留的狀態考慮到一般位置</span><span class="sxs-lookup"><span data-stu-id="26d9c-310">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="26d9c-311">如果有許多元件依賴以瀏覽器為基礎的儲存體，則重新執行狀態提供者程式碼多次會建立程式碼重複。</span><span class="sxs-lookup"><span data-stu-id="26d9c-311">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="26d9c-312">避免程式碼重複的其中一個選項，就是建立可封裝狀態提供者邏輯的 *狀態提供者父元件* 。</span><span class="sxs-lookup"><span data-stu-id="26d9c-312">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="26d9c-313">子元件可以使用保存的資料，而不需要考慮狀態持續性機制。</span><span class="sxs-lookup"><span data-stu-id="26d9c-313">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="26d9c-314">在下列的元件範例中 `CounterStateProvider` ，計數器資料會保存到 `sessionStorage` ：</span><span class="sxs-lookup"><span data-stu-id="26d9c-314">In the following example of a `CounterStateProvider` component, counter data is persisted to `sessionStorage`:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (isLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool isLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        var result = await ProtectedSessionStore.GetAsync<int>("count");
        currentCount = result.Success ? result.Value : 0;
        isLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

<span data-ttu-id="26d9c-315">元件在載入 `CounterStateProvider` 完成之前，不會轉譯其子內容來處理載入階段。</span><span class="sxs-lookup"><span data-stu-id="26d9c-315">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="26d9c-316">若要使用 `CounterStateProvider` 元件，請將元件的實例包裝在需要存取計數器狀態的任何其他元件周圍。</span><span class="sxs-lookup"><span data-stu-id="26d9c-316">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="26d9c-317">若要讓應用程式中的所有元件都能存取該狀態，請將 `CounterStateProvider` 元件包裝 <xref:Microsoft.AspNetCore.Components.Routing.Router> 在 `App` 元件 (`App.razor`) 中：</span><span class="sxs-lookup"><span data-stu-id="26d9c-317">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the <xref:Microsoft.AspNetCore.Components.Routing.Router> in the `App` component (`App.razor`):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

<span data-ttu-id="26d9c-318">包裝的元件會接收和修改持續性的計數器狀態。</span><span class="sxs-lookup"><span data-stu-id="26d9c-318">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="26d9c-319">下列元件會實 `Counter` 作為模式：</span><span class="sxs-lookup"><span data-stu-id="26d9c-319">The following `Counter` component implements the pattern:</span></span>

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>
<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

<span data-ttu-id="26d9c-320">上述元件不需要與互動 `ProtectedBrowserStorage` ，也不會處理「載入」階段。</span><span class="sxs-lookup"><span data-stu-id="26d9c-320">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="26d9c-321">如先前所述，若要處理如先前所述的自動完成， `CounterStateProvider` 可以修改所有使用計數器資料的元件，以便自動進行可呈現。</span><span class="sxs-lookup"><span data-stu-id="26d9c-321">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="26d9c-322">如需詳細資訊，請參閱處理常式的已進行 [處理](#handle-prerendering) 一節。</span><span class="sxs-lookup"><span data-stu-id="26d9c-322">For more information, see the [Handle prerendering](#handle-prerendering) section.</span></span>

<span data-ttu-id="26d9c-323">一般情況下，建議使用「 *狀態供應器」父元件* 模式：</span><span class="sxs-lookup"><span data-stu-id="26d9c-323">In general, the *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="26d9c-324">跨多個元件使用狀態。</span><span class="sxs-lookup"><span data-stu-id="26d9c-324">To consume state across many components.</span></span>
* <span data-ttu-id="26d9c-325">如果只保留一個最上層狀態物件。</span><span class="sxs-lookup"><span data-stu-id="26d9c-325">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="26d9c-326">若要保存許多不同的狀態物件，並在不同位置取用不同的物件子集，最好避免全域保存狀態。</span><span class="sxs-lookup"><span data-stu-id="26d9c-326">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid persisting state globally.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="protected-browser-storage-experimental-nuget-package"></a><span data-ttu-id="26d9c-327">受保護的瀏覽器儲存體實驗性 NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="26d9c-327">Protected Browser Storage experimental NuGet package</span></span>

<span data-ttu-id="26d9c-328">ASP.NET Core 受保護的瀏覽器儲存體會利用和的 [ASP.NET Core 資料保護](xref:security/data-protection/introduction) [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) 。</span><span class="sxs-lookup"><span data-stu-id="26d9c-328">ASP.NET Core Protected Browser Storage leverages [ASP.NET Core Data Protection](xref:security/data-protection/introduction) for [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).</span></span>

> [!WARNING]
> <span data-ttu-id="26d9c-329">`Microsoft.AspNetCore.ProtectedBrowserStorage` 是不受支援的實驗性套件，不適用於生產用途。</span><span class="sxs-lookup"><span data-stu-id="26d9c-329">`Microsoft.AspNetCore.ProtectedBrowserStorage` is an unsupported, experimental package unsuitable for production use.</span></span>
>
> <span data-ttu-id="26d9c-330">封裝僅適用于 ASP.NET Core 3.1 :::no-loc(Blazor Server)::: 應用程式。</span><span class="sxs-lookup"><span data-stu-id="26d9c-330">The package is only available for use in ASP.NET Core 3.1 :::no-loc(Blazor Server)::: apps.</span></span>

### <a name="configuration"></a><span data-ttu-id="26d9c-331">組態</span><span class="sxs-lookup"><span data-stu-id="26d9c-331">Configuration</span></span>

1. <span data-ttu-id="26d9c-332">將封裝參考加入至 [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage) 。</span><span class="sxs-lookup"><span data-stu-id="26d9c-332">Add a package reference to [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>
1. <span data-ttu-id="26d9c-333">在檔案中 `Pages/_Host.cshtml` ，將下列腳本新增至結尾 `</body>` 標記內：</span><span class="sxs-lookup"><span data-stu-id="26d9c-333">In the `Pages/_Host.cshtml` file, add the following script inside the closing `</body>` tag:</span></span>

   ```cshtml
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. <span data-ttu-id="26d9c-334">在中 `Startup.ConfigureServices` ，呼叫 `AddProtectedBrowserStorage` 以將 `localStorage` 和服務加入 `sessionStorage` 至服務集合：</span><span class="sxs-lookup"><span data-stu-id="26d9c-334">In `Startup.ConfigureServices`, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="26d9c-335">儲存並載入元件中的資料</span><span class="sxs-lookup"><span data-stu-id="26d9c-335">Save and load data within a component</span></span>

<span data-ttu-id="26d9c-336">在需要將資料載入或儲存至瀏覽器儲存體的任何元件中，請使用指示詞 [`@inject`](xref:mvc/views/razor#inject) 插入下列任一項的實例：</span><span class="sxs-lookup"><span data-stu-id="26d9c-336">In any component that requires loading or saving data to browser storage, use the [`@inject`](xref:mvc/views/razor#inject) directive to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="26d9c-337">選擇取決於您想要使用的瀏覽器儲存位置。</span><span class="sxs-lookup"><span data-stu-id="26d9c-337">The choice depends on which browser storage location you wish to use.</span></span> <span data-ttu-id="26d9c-338">在下列範例中， `sessionStorage` 會使用：</span><span class="sxs-lookup"><span data-stu-id="26d9c-338">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="26d9c-339">`@using`語句可以放入檔案中， `_Imports.razor` 而不是放在元件中。</span><span class="sxs-lookup"><span data-stu-id="26d9c-339">The `@using` statement can be placed into an `_Imports.razor` file instead of in the component.</span></span> <span data-ttu-id="26d9c-340">使用檔案可 `_Imports.razor` 讓應用程式的較大區段或整個應用程式使用命名空間。</span><span class="sxs-lookup"><span data-stu-id="26d9c-340">Use of the `_Imports.razor` file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="26d9c-341">若要在以 `currentCount` `Counter` 專案範本為基礎的應用程式元件中保存值 :::no-loc(Blazor Server)::: ，請修改 `IncrementCount` 要使用的方法 `ProtectedSessionStore.SetAsync` ：</span><span class="sxs-lookup"><span data-stu-id="26d9c-341">To persist the `currentCount` value in the `Counter` component of an app based on the :::no-loc(Blazor Server)::: project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="26d9c-342">在更大、更實際的應用程式中，個別欄位的儲存是不太可能的情況。</span><span class="sxs-lookup"><span data-stu-id="26d9c-342">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="26d9c-343">應用程式更有可能儲存包含複雜狀態的整個模型物件。</span><span class="sxs-lookup"><span data-stu-id="26d9c-343">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="26d9c-344">`ProtectedSessionStore` 自動序列化和還原序列化 JSON 資料。</span><span class="sxs-lookup"><span data-stu-id="26d9c-344">`ProtectedSessionStore` automatically serializes and deserializes JSON data.</span></span>

<span data-ttu-id="26d9c-345">在上述程式碼範例中， `currentCount` 資料會儲存 `sessionStorage['count']` 在使用者的瀏覽器中。</span><span class="sxs-lookup"><span data-stu-id="26d9c-345">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="26d9c-346">資料不會以純文字儲存，而是使用 ASP.NET Core 資料保護來保護。</span><span class="sxs-lookup"><span data-stu-id="26d9c-346">The data isn't stored in plain text but rather is protected using ASP.NET Core Data Protection.</span></span> <span data-ttu-id="26d9c-347">如果 `sessionStorage['count']` 在瀏覽器的開發人員主控台中評估，則可以檢查加密的資料。</span><span class="sxs-lookup"><span data-stu-id="26d9c-347">The encrypted data can be inspected if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="26d9c-348">若要在 `currentCount` 使用者稍後返回元件時復原資料 `Counter` ，包括是否在全新的線路上，請使用 `ProtectedSessionStore.GetAsync` ：</span><span class="sxs-lookup"><span data-stu-id="26d9c-348">To recover the `currentCount` data if the user returns to the `Counter` component later, including if they're on an entirely new circuit, use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

<span data-ttu-id="26d9c-349">如果元件的參數包含導覽狀態，請呼叫 `ProtectedSessionStore.GetAsync` 並指派中的結果 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> ，而不是 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 。</span><span class="sxs-lookup"><span data-stu-id="26d9c-349">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign the result in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, not <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span> <span data-ttu-id="26d9c-350"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 只有在第一次具現化元件時，才會呼叫一次。</span><span class="sxs-lookup"><span data-stu-id="26d9c-350"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is only called once when the component is first instantiated.</span></span> <span data-ttu-id="26d9c-351"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 如果使用者流覽至不同的 URL，並在相同頁面上剩餘，則不會再次呼叫。</span><span class="sxs-lookup"><span data-stu-id="26d9c-351"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="26d9c-352">如需詳細資訊，請參閱<xref:blazor/components/lifecycle>。</span><span class="sxs-lookup"><span data-stu-id="26d9c-352">For more information, see <xref:blazor/components/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="26d9c-353">此區段中的範例只適用于伺服器未啟用可呈現的情況。</span><span class="sxs-lookup"><span data-stu-id="26d9c-353">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="26d9c-354">啟用自動呈現之後，會產生錯誤，說明因為正在資源清單元件，所以無法發出 JavaScript interop 呼叫。</span><span class="sxs-lookup"><span data-stu-id="26d9c-354">With prerendering enabled, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>
>
> <span data-ttu-id="26d9c-355">停用已呈現或新增額外的程式碼，以使用可呈現的。</span><span class="sxs-lookup"><span data-stu-id="26d9c-355">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="26d9c-356">若要深入瞭解如何撰寫可搭配可進行處理的程式碼，請參閱處理常式性的 [ [處理](#handle-prerendering) 程式] 區段</span><span class="sxs-lookup"><span data-stu-id="26d9c-356">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="26d9c-357">處理載入狀態</span><span class="sxs-lookup"><span data-stu-id="26d9c-357">Handle the loading state</span></span>

<span data-ttu-id="26d9c-358">由於瀏覽器儲存體會透過網路連線以非同步方式存取，因此在載入資料並可供元件使用之前，一律會有一段時間。</span><span class="sxs-lookup"><span data-stu-id="26d9c-358">Since browser storage is accessed asynchronously over a network connection, there's always a period of time before the data is loaded and available to a component.</span></span> <span data-ttu-id="26d9c-359">為了獲得最佳結果，在載入過程中轉譯載入狀態訊息，而不是顯示空白或預設的資料。</span><span class="sxs-lookup"><span data-stu-id="26d9c-359">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="26d9c-360">其中一個方法是追蹤資料是否為 `null` ，這表示資料仍在載入中。</span><span class="sxs-lookup"><span data-stu-id="26d9c-360">One approach is to track whether the data is `null`, which means that the data is still loading.</span></span> <span data-ttu-id="26d9c-361">在預設 `Counter` 元件中，計數會保留在中 `int` 。</span><span class="sxs-lookup"><span data-stu-id="26d9c-361">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="26d9c-362">藉由將問號 () 新增至類型 () 來[建立 `currentCount` 可為 null](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) `?` `int` ：</span><span class="sxs-lookup"><span data-stu-id="26d9c-362">[Make `currentCount` nullable](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="26d9c-363">**`Increment`** 只有在載入資料時，才選擇顯示這些元素，而不是無條件地顯示計數和按鈕：</span><span class="sxs-lookup"><span data-stu-id="26d9c-363">Instead of unconditionally displaying the count and **`Increment`** button, choose to display these elements only if the data is loaded:</span></span>

```razor
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a><span data-ttu-id="26d9c-364">處理已呈現</span><span class="sxs-lookup"><span data-stu-id="26d9c-364">Handle prerendering</span></span>

<span data-ttu-id="26d9c-365">在呈現期間：</span><span class="sxs-lookup"><span data-stu-id="26d9c-365">During prerendering:</span></span>

* <span data-ttu-id="26d9c-366">與使用者瀏覽器的互動式連接不存在。</span><span class="sxs-lookup"><span data-stu-id="26d9c-366">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="26d9c-367">瀏覽器還沒有可執行 JavaScript 程式碼的頁面。</span><span class="sxs-lookup"><span data-stu-id="26d9c-367">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="26d9c-368">`localStorage` 或 `sessionStorage` 無法在預進行期間使用。</span><span class="sxs-lookup"><span data-stu-id="26d9c-368">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="26d9c-369">如果元件嘗試與儲存體互動，就會產生錯誤，說明因為正在資源清單元件，所以無法發出 JavaScript interop 呼叫。</span><span class="sxs-lookup"><span data-stu-id="26d9c-369">If the component attempts to interact with storage, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>

<span data-ttu-id="26d9c-370">解決此錯誤的其中一種方式是停用已呈現的。</span><span class="sxs-lookup"><span data-stu-id="26d9c-370">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="26d9c-371">如果應用程式大量使用以瀏覽器為基礎的儲存體，這通常是最佳選擇。</span><span class="sxs-lookup"><span data-stu-id="26d9c-371">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="26d9c-372">由於應用程式無法取得任何有用的內容，直到 `localStorage` 或可供使用，因此會導致應用程式不會讓應用程式受益 `sessionStorage` 。</span><span class="sxs-lookup"><span data-stu-id="26d9c-372">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="26d9c-373">若要停用已呈現，請開啟檔案， `Pages/_Host.cshtml` 然後將 `render-mode` [元件標記](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) 協助程式的屬性變更為 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> ：</span><span class="sxs-lookup"><span data-stu-id="26d9c-373">To disable prerendering, open the `Pages/_Host.cshtml` file and change the `render-mode` attribute of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>:</span></span>

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

<span data-ttu-id="26d9c-374">針對不使用或的其他頁面，可進行的自動呈現可能很有用 `localStorage` `sessionStorage` 。</span><span class="sxs-lookup"><span data-stu-id="26d9c-374">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="26d9c-375">若要保留已呈現，請延遲載入作業，直到瀏覽器連線至線路為止。</span><span class="sxs-lookup"><span data-stu-id="26d9c-375">To retain prerendering, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="26d9c-376">以下是儲存計數器值的範例：</span><span class="sxs-lookup"><span data-stu-id="26d9c-376">The following is an example for storing a counter value:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

@if (isConnected)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}

@code {
    private int? currentCount;
    private bool isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        currentCount = await ProtectedLocalStore.GetAsync<int>("count");
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedLocalStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="26d9c-377">將狀態保留的狀態考慮到一般位置</span><span class="sxs-lookup"><span data-stu-id="26d9c-377">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="26d9c-378">如果有許多元件依賴以瀏覽器為基礎的儲存體，則重新執行狀態提供者程式碼多次會建立程式碼重複。</span><span class="sxs-lookup"><span data-stu-id="26d9c-378">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="26d9c-379">避免程式碼重複的其中一個選項，就是建立可封裝狀態提供者邏輯的 *狀態提供者父元件* 。</span><span class="sxs-lookup"><span data-stu-id="26d9c-379">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="26d9c-380">子元件可以使用保存的資料，而不需要考慮狀態持續性機制。</span><span class="sxs-lookup"><span data-stu-id="26d9c-380">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="26d9c-381">在下列的元件範例中 `CounterStateProvider` ，計數器資料會保存到 `sessionStorage` ：</span><span class="sxs-lookup"><span data-stu-id="26d9c-381">In the following example of a `CounterStateProvider` component, counter data is persisted to `sessionStorage`:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (isLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool isLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        isLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

<span data-ttu-id="26d9c-382">元件在載入 `CounterStateProvider` 完成之前，不會轉譯其子內容來處理載入階段。</span><span class="sxs-lookup"><span data-stu-id="26d9c-382">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="26d9c-383">若要使用 `CounterStateProvider` 元件，請將元件的實例包裝在需要存取計數器狀態的任何其他元件周圍。</span><span class="sxs-lookup"><span data-stu-id="26d9c-383">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="26d9c-384">若要讓應用程式中的所有元件都能存取該狀態，請將 `CounterStateProvider` 元件包裝 <xref:Microsoft.AspNetCore.Components.Routing.Router> 在 `App` 元件 (`App.razor`) 中：</span><span class="sxs-lookup"><span data-stu-id="26d9c-384">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the <xref:Microsoft.AspNetCore.Components.Routing.Router> in the `App` component (`App.razor`):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

<span data-ttu-id="26d9c-385">包裝的元件會接收和修改持續性的計數器狀態。</span><span class="sxs-lookup"><span data-stu-id="26d9c-385">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="26d9c-386">下列元件會實 `Counter` 作為模式：</span><span class="sxs-lookup"><span data-stu-id="26d9c-386">The following `Counter` component implements the pattern:</span></span>

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>
<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

<span data-ttu-id="26d9c-387">上述元件不需要與互動 `ProtectedBrowserStorage` ，也不會處理「載入」階段。</span><span class="sxs-lookup"><span data-stu-id="26d9c-387">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="26d9c-388">如先前所述，若要處理如先前所述的自動完成， `CounterStateProvider` 可以修改所有使用計數器資料的元件，以便自動進行可呈現。</span><span class="sxs-lookup"><span data-stu-id="26d9c-388">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="26d9c-389">如需詳細資訊，請參閱處理常式的已進行 [處理](#handle-prerendering) 一節。</span><span class="sxs-lookup"><span data-stu-id="26d9c-389">For more information, see the [Handle prerendering](#handle-prerendering) section.</span></span>

<span data-ttu-id="26d9c-390">一般情況下，建議使用「 *狀態供應器」父元件* 模式：</span><span class="sxs-lookup"><span data-stu-id="26d9c-390">In general, *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="26d9c-391">跨多個元件使用狀態。</span><span class="sxs-lookup"><span data-stu-id="26d9c-391">To consume state across many components.</span></span>
* <span data-ttu-id="26d9c-392">如果只保留一個最上層狀態物件。</span><span class="sxs-lookup"><span data-stu-id="26d9c-392">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="26d9c-393">若要保存許多不同的狀態物件，並在不同位置取用不同的物件子集，最好避免全域保存狀態。</span><span class="sxs-lookup"><span data-stu-id="26d9c-393">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid persisting state globally.</span></span>

::: moniker-end

## <a name="in-memory-state-container-service"></a><span data-ttu-id="26d9c-394">記憶體內部狀態容器服務</span><span class="sxs-lookup"><span data-stu-id="26d9c-394">In-memory state container service</span></span>

[!INCLUDE[](~/includes/blazor-state-management/state-container.md)]

::: zone-end
