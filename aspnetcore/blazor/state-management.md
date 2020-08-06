---
title: ASP.NET Core Blazor 狀態管理
author: guardrex
description: 瞭解如何在應用程式中保存狀態 Blazor Server 。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/state-management
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: e4ec360e1f9fb0bc5784b3120d7842faf24cfa5b
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87818764"
---
# <a name="aspnet-core-no-locblazor-state-management"></a><span data-ttu-id="ed97f-103">ASP.NET Core Blazor 狀態管理</span><span class="sxs-lookup"><span data-stu-id="ed97f-103">ASP.NET Core Blazor state management</span></span>

<span data-ttu-id="ed97f-104">作者： [Steve Sanderson](https://github.com/SteveSandersonMS)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ed97f-104">By [Steve Sanderson](https://github.com/SteveSandersonMS) and [Luke Latham](https://github.com/guardrex)</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="ed97f-105">在應用程式中建立的使用者狀態 Blazor WebAssembly 會保留在瀏覽器的記憶體中。</span><span class="sxs-lookup"><span data-stu-id="ed97f-105">User state created in a Blazor WebAssembly app is held in the browser's memory.</span></span>

<span data-ttu-id="ed97f-106">瀏覽器記憶體中所保存的使用者狀態範例包括：</span><span class="sxs-lookup"><span data-stu-id="ed97f-106">Examples of user state held in browser memory include:</span></span>

* <span data-ttu-id="ed97f-107">呈現的 UI 中元件實例及其最新轉譯輸出的階層。</span><span class="sxs-lookup"><span data-stu-id="ed97f-107">The hierarchy of component instances and their most recent render output in the rendered UI.</span></span>
* <span data-ttu-id="ed97f-108">元件實例中的欄位和屬性值。</span><span class="sxs-lookup"><span data-stu-id="ed97f-108">The values of fields and properties in component instances.</span></span>
* <span data-ttu-id="ed97f-109">保存在相依性[插入 (DI) ](xref:fundamentals/dependency-injection)服務實例中的資料。</span><span class="sxs-lookup"><span data-stu-id="ed97f-109">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances.</span></span>
* <span data-ttu-id="ed97f-110">透過[JavaScript interop](xref:blazor/call-javascript-from-dotnet)呼叫所設定的值。</span><span class="sxs-lookup"><span data-stu-id="ed97f-110">Values set through [JavaScript interop](xref:blazor/call-javascript-from-dotnet) calls.</span></span>

<span data-ttu-id="ed97f-111">當使用者關閉並重新開啟其瀏覽器或重載頁面時，會遺失瀏覽器記憶體中所保存的使用者狀態。</span><span class="sxs-lookup"><span data-stu-id="ed97f-111">When a user closes and re-opens their browser or reloads the page, user state held in the browser's memory is lost.</span></span>

## <a name="persist-state-across-browser-sessions"></a><span data-ttu-id="ed97f-112">跨瀏覽器會話保存狀態</span><span class="sxs-lookup"><span data-stu-id="ed97f-112">Persist state across browser sessions</span></span>

<span data-ttu-id="ed97f-113">一般來說，會在使用者主動建立資料的瀏覽器會話之間維護狀態，而不只是讀取已經存在的資料。</span><span class="sxs-lookup"><span data-stu-id="ed97f-113">Generally, maintain state across browser sessions where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="ed97f-114">若要在瀏覽器會話之間保留狀態，應用程式必須將資料保存在瀏覽器記憶體以外的其他儲存位置。</span><span class="sxs-lookup"><span data-stu-id="ed97f-114">To preserve state across browser sessions, the app must persist the data to some other storage location than the browser's memory.</span></span> <span data-ttu-id="ed97f-115">狀態持續性不是自動的。</span><span class="sxs-lookup"><span data-stu-id="ed97f-115">State persistence isn't automatic.</span></span> <span data-ttu-id="ed97f-116">開發應用程式時，您必須採取步驟來執行具狀態的資料持續性。</span><span class="sxs-lookup"><span data-stu-id="ed97f-116">You must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="ed97f-117">資料持續性通常只有在使用者花費在建立的高價值狀態時才需要。</span><span class="sxs-lookup"><span data-stu-id="ed97f-117">Data persistence is typically only required for high-value state that users expended effort to create.</span></span> <span data-ttu-id="ed97f-118">在下列範例中，保存狀態可以節省商務工作的時間或輔助：</span><span class="sxs-lookup"><span data-stu-id="ed97f-118">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="ed97f-119">多步驟 web 表單：使用者在其狀態遺失時，針對多步驟 web 表單的幾個完成步驟重新輸入資料相當耗時。</span><span class="sxs-lookup"><span data-stu-id="ed97f-119">Multi-step web forms: It's time-consuming for a user to re-enter data for several completed steps of a multi-step web form if their state is lost.</span></span> <span data-ttu-id="ed97f-120">如果使用者離開表單並于稍後返回，則會在此案例中失去狀態。</span><span class="sxs-lookup"><span data-stu-id="ed97f-120">A user loses state in this scenario if they navigate away from the form and return later.</span></span>
* <span data-ttu-id="ed97f-121">購物車：可以維護應用程式中代表潛在收益的任何商業重要元件。</span><span class="sxs-lookup"><span data-stu-id="ed97f-121">Shopping carts: Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="ed97f-122">失去其狀態的使用者，因此其購物車，可能會在日後返回網站時購買較少的產品或服務。</span><span class="sxs-lookup"><span data-stu-id="ed97f-122">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="ed97f-123">應用程式只能保存*應用程式狀態*。</span><span class="sxs-lookup"><span data-stu-id="ed97f-123">An app can only persist *app state*.</span></span> <span data-ttu-id="ed97f-124">Ui 無法保存，例如元件實例和其呈現樹狀結構。</span><span class="sxs-lookup"><span data-stu-id="ed97f-124">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="ed97f-125">元件和轉譯樹狀結構通常不是可序列化的。</span><span class="sxs-lookup"><span data-stu-id="ed97f-125">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="ed97f-126">若要保存 UI 狀態（例如樹狀檢視控制項的展開節點），應用程式必須使用自訂程式碼，將 UI 狀態的行為模型化為可序列化的應用程式狀態。</span><span class="sxs-lookup"><span data-stu-id="ed97f-126">To persist UI state, such as the expanded nodes of a tree view control, the app must use custom code to model the behavior of the UI state as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="ed97f-127">保存狀態的位置</span><span class="sxs-lookup"><span data-stu-id="ed97f-127">Where to persist state</span></span>

<span data-ttu-id="ed97f-128">保存狀態有三個常見的位置：</span><span class="sxs-lookup"><span data-stu-id="ed97f-128">Three common locations exist for persisting state:</span></span>

* [<span data-ttu-id="ed97f-129">伺服器端儲存體</span><span class="sxs-lookup"><span data-stu-id="ed97f-129">Server-side storage</span></span>](#server-side-storage)
* [<span data-ttu-id="ed97f-130">URL</span><span class="sxs-lookup"><span data-stu-id="ed97f-130">URL</span></span>](#url)
* [<span data-ttu-id="ed97f-131">瀏覽器儲存</span><span class="sxs-lookup"><span data-stu-id="ed97f-131">Browser storage</span></span>](#browser-storage)

### <a name="server-side-storage"></a><span data-ttu-id="ed97f-132">伺服器端儲存體</span><span class="sxs-lookup"><span data-stu-id="ed97f-132">Server-side storage</span></span>

<span data-ttu-id="ed97f-133">對於跨越多個使用者和裝置的永久資料持續性，應用程式可以使用透過 Web API 存取的獨立伺服器端儲存體。</span><span class="sxs-lookup"><span data-stu-id="ed97f-133">For permanent data persistence that spans multiple users and devices, the app can use independent server-side storage accessed via a web API.</span></span> <span data-ttu-id="ed97f-134">選項包括：</span><span class="sxs-lookup"><span data-stu-id="ed97f-134">Options include:</span></span>

* <span data-ttu-id="ed97f-135">Blob 儲存體</span><span class="sxs-lookup"><span data-stu-id="ed97f-135">Blob storage</span></span>
* <span data-ttu-id="ed97f-136">索引鍵/值儲存</span><span class="sxs-lookup"><span data-stu-id="ed97f-136">Key-value storage</span></span>
* <span data-ttu-id="ed97f-137">關聯式資料庫</span><span class="sxs-lookup"><span data-stu-id="ed97f-137">Relational database</span></span>
* <span data-ttu-id="ed97f-138">表格儲存體</span><span class="sxs-lookup"><span data-stu-id="ed97f-138">Table storage</span></span>

<span data-ttu-id="ed97f-139">儲存資料之後，使用者的狀態會保留，並可在任何新的瀏覽器會話中使用。</span><span class="sxs-lookup"><span data-stu-id="ed97f-139">After data is saved, the user's state is retained and available in any new browser session.</span></span>

<span data-ttu-id="ed97f-140">因為 Blazor WebAssembly 應用程式完全在使用者的瀏覽器中執行，所以它們需要額外的措施來存取安全的外部系統，例如儲存體服務和資料庫。</span><span class="sxs-lookup"><span data-stu-id="ed97f-140">Because Blazor WebAssembly apps run entirely in the user's browser, they require additional measures to access secure external systems, such as storage services and databases.</span></span> <span data-ttu-id="ed97f-141">Blazor WebAssembly應用程式的保護方式與單一頁面應用程式 (Spa) 相同。</span><span class="sxs-lookup"><span data-stu-id="ed97f-141">Blazor WebAssembly apps are secured in the same manner as Single Page Applications (SPAs).</span></span> <span data-ttu-id="ed97f-142">一般而言，應用程式會透過[OAuth](https://oauth.net) / [OpenID connect (OIDC) ](https://openid.net/connect/)來驗證使用者，然後藉由 Web API 對伺服器端應用程式的呼叫，與儲存體服務和資料庫互動。</span><span class="sxs-lookup"><span data-stu-id="ed97f-142">Typically, an app authenticates a user via [OAuth](https://oauth.net)/[OpenID Connect (OIDC)](https://openid.net/connect/) and then interacts with storage services and databases through web API calls to a server-side app.</span></span> <span data-ttu-id="ed97f-143">伺服器端應用程式會調節 Blazor WebAssembly 應用程式與儲存體服務或資料庫之間的資料傳輸。</span><span class="sxs-lookup"><span data-stu-id="ed97f-143">The server-side app mediates the transfer of data between the Blazor WebAssembly app and the storage service or database.</span></span> <span data-ttu-id="ed97f-144">Blazor WebAssembly應用程式會維護與伺服器端應用程式的暫時連線，而伺服器端應用程式會持續連線至儲存體。</span><span class="sxs-lookup"><span data-stu-id="ed97f-144">The Blazor WebAssembly app maintains an ephemeral connection to the server-side app, while the server-side app has a persistent connection to storage.</span></span>

<span data-ttu-id="ed97f-145">如需詳細資訊，請參閱下列資源：</span><span class="sxs-lookup"><span data-stu-id="ed97f-145">For more information, see the following resources:</span></span>

* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>
* <span data-ttu-id="ed97f-146">Blazor\*安全性與 Identity \*關於</span><span class="sxs-lookup"><span data-stu-id="ed97f-146">Blazor *Security and Identity* articles</span></span>

<span data-ttu-id="ed97f-147">如需 Azure 資料儲存體選項的詳細資訊，請參閱下列各項：</span><span class="sxs-lookup"><span data-stu-id="ed97f-147">For more information on Azure data storage options, see the following:</span></span>

* [<span data-ttu-id="ed97f-148">Azure 資料庫</span><span class="sxs-lookup"><span data-stu-id="ed97f-148">Azure Databases</span></span>](https://azure.microsoft.com/product-categories/databases/)
* [<span data-ttu-id="ed97f-149">Azure 儲存體檔</span><span class="sxs-lookup"><span data-stu-id="ed97f-149">Azure Storage Documentation</span></span>](/azure/storage/)

### <a name="url"></a><span data-ttu-id="ed97f-150">URL</span><span class="sxs-lookup"><span data-stu-id="ed97f-150">URL</span></span>

<span data-ttu-id="ed97f-151">對於代表導覽狀態的暫時性資料，請將資料模型為 URL 的一部分。</span><span class="sxs-lookup"><span data-stu-id="ed97f-151">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="ed97f-152">在 URL 中模型化使用者狀態的範例包括：</span><span class="sxs-lookup"><span data-stu-id="ed97f-152">Examples of user state modeled in the URL include:</span></span>

* <span data-ttu-id="ed97f-153">已查看之實體的識別碼。</span><span class="sxs-lookup"><span data-stu-id="ed97f-153">The ID of a viewed entity.</span></span>
* <span data-ttu-id="ed97f-154">分頁方格中的目前頁碼。</span><span class="sxs-lookup"><span data-stu-id="ed97f-154">The current page number in a paged grid.</span></span>

<span data-ttu-id="ed97f-155">如果使用者手動重載頁面，則會保留瀏覽器網址列的內容。</span><span class="sxs-lookup"><span data-stu-id="ed97f-155">The contents of the browser's address bar are retained if the user manually reloads the page.</span></span>

<span data-ttu-id="ed97f-156">如需使用指示詞定義 URL 模式的詳細資訊 [`@page`](xref:mvc/views/razor#page) ，請參閱 <xref:blazor/fundamentals/routing> 。</span><span class="sxs-lookup"><span data-stu-id="ed97f-156">For information on defining URL patterns with the [`@page`](xref:mvc/views/razor#page) directive, see <xref:blazor/fundamentals/routing>.</span></span>

### <a name="browser-storage"></a><span data-ttu-id="ed97f-157">瀏覽器儲存</span><span class="sxs-lookup"><span data-stu-id="ed97f-157">Browser storage</span></span>

<span data-ttu-id="ed97f-158">對於使用者主動建立的暫時性資料，常用的儲存位置是瀏覽器的 [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) 和 [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) 集合：</span><span class="sxs-lookup"><span data-stu-id="ed97f-158">For transient data that the user is actively creating, a commonly used storage location is the browser's [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) collections:</span></span>

* <span data-ttu-id="ed97f-159">`localStorage`的範圍是瀏覽器的視窗。</span><span class="sxs-lookup"><span data-stu-id="ed97f-159">`localStorage` is scoped to the browser's window.</span></span> <span data-ttu-id="ed97f-160">如果使用者重載頁面，或關閉並重新開啟瀏覽器，則狀態會保持不變。</span><span class="sxs-lookup"><span data-stu-id="ed97f-160">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="ed97f-161">如果使用者開啟多個瀏覽器索引標籤，則狀態會在索引標籤之間共用。</span><span class="sxs-lookup"><span data-stu-id="ed97f-161">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="ed97f-162">資料會保存在中， `localStorage` 直到明確清除為止。</span><span class="sxs-lookup"><span data-stu-id="ed97f-162">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="ed97f-163">`sessionStorage`的範圍設定為 [瀏覽器] 索引標籤。如果使用者重載索引標籤，則狀態會保持不變。</span><span class="sxs-lookup"><span data-stu-id="ed97f-163">`sessionStorage` is scoped to the browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="ed97f-164">如果使用者關閉索引標籤或瀏覽器，則狀態會遺失。</span><span class="sxs-lookup"><span data-stu-id="ed97f-164">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="ed97f-165">如果使用者開啟多個瀏覽器索引標籤，每個索引標籤都有自己獨立的資料版本。</span><span class="sxs-lookup"><span data-stu-id="ed97f-165">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

> [!NOTE]
> <span data-ttu-id="ed97f-166">`localStorage`和 `sessionStorage` 可以在應用程式中使用， Blazor WebAssembly 但只能透過撰寫自訂程式碼或使用協力廠商套件。</span><span class="sxs-lookup"><span data-stu-id="ed97f-166">`localStorage` and `sessionStorage` can be used in Blazor WebAssembly apps but only by writing custom code or using a third-party package.</span></span>

<span data-ttu-id="ed97f-167">一般而言， `sessionStorage` 使用較安全。</span><span class="sxs-lookup"><span data-stu-id="ed97f-167">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="ed97f-168">`sessionStorage`避免使用者開啟多個索引標籤並遇到下列問題的風險：</span><span class="sxs-lookup"><span data-stu-id="ed97f-168">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="ed97f-169">索引標籤上狀態儲存體中的 bug。</span><span class="sxs-lookup"><span data-stu-id="ed97f-169">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="ed97f-170">當索引標籤覆寫其他索引標籤的狀態時，會造成混淆的行為。</span><span class="sxs-lookup"><span data-stu-id="ed97f-170">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="ed97f-171">`localStorage`如果應用程式必須在關閉並重新開啟瀏覽器時保存狀態，這是較好的選擇。</span><span class="sxs-lookup"><span data-stu-id="ed97f-171">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

> [!WARNING]
> <span data-ttu-id="ed97f-172">使用者可能會看到或篡改儲存在和中的資料 `localStorage` `sessionStorage` 。</span><span class="sxs-lookup"><span data-stu-id="ed97f-172">Users may view or tamper with the data stored in `localStorage` and `sessionStorage`.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ed97f-173">其他資源</span><span class="sxs-lookup"><span data-stu-id="ed97f-173">Additional resources</span></span>

* [<span data-ttu-id="ed97f-174">在驗證操作之前儲存應用程式狀態</span><span class="sxs-lookup"><span data-stu-id="ed97f-174">Save app state before an authentication operation</span></span>](xref:blazor/security/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation)
* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>

::: zone-end

::: zone pivot="server"

<span data-ttu-id="ed97f-175">Blazor Server是可設定狀態的應用程式架構。</span><span class="sxs-lookup"><span data-stu-id="ed97f-175">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="ed97f-176">在大部分的情況下，應用程式會維護與伺服器的連接。</span><span class="sxs-lookup"><span data-stu-id="ed97f-176">Most of the time, the app maintains a connection to the server.</span></span> <span data-ttu-id="ed97f-177">使用者的狀態會保留在*伺服器的記憶體中。*</span><span class="sxs-lookup"><span data-stu-id="ed97f-177">The user's state is held in the server's memory in a *circuit*.</span></span> 

<span data-ttu-id="ed97f-178">線路中保留的使用者狀態範例包括：</span><span class="sxs-lookup"><span data-stu-id="ed97f-178">Examples of user state held in a circuit include:</span></span>

* <span data-ttu-id="ed97f-179">呈現的 UI 中元件實例及其最新轉譯輸出的階層。</span><span class="sxs-lookup"><span data-stu-id="ed97f-179">The hierarchy of component instances and their most recent render output in the rendered UI.</span></span>
* <span data-ttu-id="ed97f-180">元件實例中的欄位和屬性值。</span><span class="sxs-lookup"><span data-stu-id="ed97f-180">The values of fields and properties in component instances.</span></span>
* <span data-ttu-id="ed97f-181">在相依性插入中保存的資料[ (DI) ](xref:fundamentals/dependency-injection)範圍設定為線路的服務實例。</span><span class="sxs-lookup"><span data-stu-id="ed97f-181">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span>

<span data-ttu-id="ed97f-182">您也可以透過[javascript interop](xref:blazor/call-javascript-from-dotnet)呼叫，在瀏覽器記憶體集的 javascript 變數中找到使用者狀態。</span><span class="sxs-lookup"><span data-stu-id="ed97f-182">User state might also be found in JavaScript variables in the browser's memory set via [JavaScript interop](xref:blazor/call-javascript-from-dotnet) calls.</span></span>

<span data-ttu-id="ed97f-183">如果使用者遇到暫時性的網路連線遺失， Blazor 會嘗試將使用者重新連接到其原始狀態的原始線路。</span><span class="sxs-lookup"><span data-stu-id="ed97f-183">If a user experiences a temporary network connection loss, Blazor attempts to reconnect the user to their original circuit with their original state.</span></span> <span data-ttu-id="ed97f-184">不過，不一定能夠將使用者重新連接到伺服器記憶體中的原始線路：</span><span class="sxs-lookup"><span data-stu-id="ed97f-184">However, reconnecting a user to their original circuit in the server's memory isn't always possible:</span></span>

* <span data-ttu-id="ed97f-185">伺服器無法永久保留中斷連線的線路。</span><span class="sxs-lookup"><span data-stu-id="ed97f-185">The server can't retain a disconnected circuit forever.</span></span> <span data-ttu-id="ed97f-186">伺服器必須在超時之後或伺服器處於記憶體不足的壓力時，釋放中斷連線的線路。</span><span class="sxs-lookup"><span data-stu-id="ed97f-186">The server must release a disconnected circuit after a timeout or when the server is under memory pressure.</span></span>
* <span data-ttu-id="ed97f-187">在多伺服器、負載平衡的部署環境中，當不再需要處理要求的整體量時，個別伺服器可能會失敗或自動移除。</span><span class="sxs-lookup"><span data-stu-id="ed97f-187">In multi-server, load-balanced deployment environments, individual servers may fail or be automatically removed when no longer required to handle the overall volume of requests.</span></span> <span data-ttu-id="ed97f-188">使用者嘗試重新連線時，對使用者的源伺服器處理要求可能會變成無法使用。</span><span class="sxs-lookup"><span data-stu-id="ed97f-188">The original server processing requests for a user may become unavailable when the user attempts to reconnect.</span></span>
* <span data-ttu-id="ed97f-189">使用者可能會關閉並重新開啟其瀏覽器或重載頁面，這會移除瀏覽器記憶體中保留的任何狀態。</span><span class="sxs-lookup"><span data-stu-id="ed97f-189">The user might close and re-open their browser or reload the page, which removes any state held in the browser's memory.</span></span> <span data-ttu-id="ed97f-190">例如，透過 JavaScript interop 呼叫所設定的 JavaScript 變數值會遺失。</span><span class="sxs-lookup"><span data-stu-id="ed97f-190">For example, JavaScript variable values set through JavaScript interop calls are lost.</span></span>

<span data-ttu-id="ed97f-191">當使用者無法重新連線到其原始線路時，使用者會收到空白狀態的新線路。</span><span class="sxs-lookup"><span data-stu-id="ed97f-191">When a user can't be reconnected to their original circuit, the user receives a new circuit with an empty state.</span></span> <span data-ttu-id="ed97f-192">這相當於關閉和重新開啟桌面應用程式。</span><span class="sxs-lookup"><span data-stu-id="ed97f-192">This is equivalent to closing and re-opening a desktop app.</span></span>

## <a name="persist-state-across-circuits"></a><span data-ttu-id="ed97f-193">跨線路保存狀態</span><span class="sxs-lookup"><span data-stu-id="ed97f-193">Persist state across circuits</span></span>

<span data-ttu-id="ed97f-194">一般來說，會在使用者主動建立資料的線路上維護狀態，而不只是讀取已經存在的資料。</span><span class="sxs-lookup"><span data-stu-id="ed97f-194">Generally, maintain state across circuits where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="ed97f-195">若要保留跨線路的狀態，應用程式必須將資料保存在伺服器記憶體以外的其他儲存位置。</span><span class="sxs-lookup"><span data-stu-id="ed97f-195">To preserve state across circuits, the app must persist the data to some other storage location than the server's memory.</span></span> <span data-ttu-id="ed97f-196">狀態持續性不是自動的。</span><span class="sxs-lookup"><span data-stu-id="ed97f-196">State persistence isn't automatic.</span></span> <span data-ttu-id="ed97f-197">開發應用程式時，您必須採取步驟來執行具狀態的資料持續性。</span><span class="sxs-lookup"><span data-stu-id="ed97f-197">You must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="ed97f-198">資料持續性通常只有在使用者花費在建立的高價值狀態時才需要。</span><span class="sxs-lookup"><span data-stu-id="ed97f-198">Data persistence is typically only required for high-value state that users expended effort to create.</span></span> <span data-ttu-id="ed97f-199">在下列範例中，保存狀態可以節省商務工作的時間或輔助：</span><span class="sxs-lookup"><span data-stu-id="ed97f-199">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="ed97f-200">多步驟 web 表單：使用者在其狀態遺失時，針對多步驟 web 表單的幾個完成步驟重新輸入資料相當耗時。</span><span class="sxs-lookup"><span data-stu-id="ed97f-200">Multi-step web forms: It's time-consuming for a user to re-enter data for several completed steps of a multi-step web form if their state is lost.</span></span> <span data-ttu-id="ed97f-201">如果使用者離開表單並于稍後返回，則會在此案例中失去狀態。</span><span class="sxs-lookup"><span data-stu-id="ed97f-201">A user loses state in this scenario if they navigate away from the form and return later.</span></span>
* <span data-ttu-id="ed97f-202">購物車：可以維護應用程式中代表潛在收益的任何商業重要元件。</span><span class="sxs-lookup"><span data-stu-id="ed97f-202">Shopping carts: Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="ed97f-203">失去其狀態的使用者，因此其購物車，可能會在日後返回網站時購買較少的產品或服務。</span><span class="sxs-lookup"><span data-stu-id="ed97f-203">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="ed97f-204">應用程式只能保存*應用程式狀態*。</span><span class="sxs-lookup"><span data-stu-id="ed97f-204">An app can only persist *app state*.</span></span> <span data-ttu-id="ed97f-205">Ui 無法保存，例如元件實例和其呈現樹狀結構。</span><span class="sxs-lookup"><span data-stu-id="ed97f-205">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="ed97f-206">元件和轉譯樹狀結構通常不是可序列化的。</span><span class="sxs-lookup"><span data-stu-id="ed97f-206">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="ed97f-207">若要保存 UI 狀態（例如樹狀檢視控制項的展開節點），應用程式必須使用自訂程式碼，將 UI 狀態的行為模型化為可序列化的應用程式狀態。</span><span class="sxs-lookup"><span data-stu-id="ed97f-207">To persist UI state, such as the expanded nodes of a tree view control, the app must use custom code to model the behavior of the UI state as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="ed97f-208">保存狀態的位置</span><span class="sxs-lookup"><span data-stu-id="ed97f-208">Where to persist state</span></span>

<span data-ttu-id="ed97f-209">保存狀態有三個常見的位置：</span><span class="sxs-lookup"><span data-stu-id="ed97f-209">Three common locations exist for persisting state:</span></span>

* [<span data-ttu-id="ed97f-210">伺服器端儲存體</span><span class="sxs-lookup"><span data-stu-id="ed97f-210">Server-side storage</span></span>](#server-side-storage)
* [<span data-ttu-id="ed97f-211">URL</span><span class="sxs-lookup"><span data-stu-id="ed97f-211">URL</span></span>](#url)
* [<span data-ttu-id="ed97f-212">瀏覽器儲存</span><span class="sxs-lookup"><span data-stu-id="ed97f-212">Browser storage</span></span>](#browser-storage)

### <a name="server-side-storage"></a><span data-ttu-id="ed97f-213">伺服器端儲存體</span><span class="sxs-lookup"><span data-stu-id="ed97f-213">Server-side storage</span></span>

<span data-ttu-id="ed97f-214">對於跨越多個使用者和裝置的永久資料持續性，應用程式可以使用伺服器端儲存體。</span><span class="sxs-lookup"><span data-stu-id="ed97f-214">For permanent data persistence that spans multiple users and devices, the app can use server-side storage.</span></span> <span data-ttu-id="ed97f-215">選項包括：</span><span class="sxs-lookup"><span data-stu-id="ed97f-215">Options include:</span></span>

* <span data-ttu-id="ed97f-216">Blob 儲存體</span><span class="sxs-lookup"><span data-stu-id="ed97f-216">Blob storage</span></span>
* <span data-ttu-id="ed97f-217">索引鍵/值儲存</span><span class="sxs-lookup"><span data-stu-id="ed97f-217">Key-value storage</span></span>
* <span data-ttu-id="ed97f-218">關聯式資料庫</span><span class="sxs-lookup"><span data-stu-id="ed97f-218">Relational database</span></span>
* <span data-ttu-id="ed97f-219">表格儲存體</span><span class="sxs-lookup"><span data-stu-id="ed97f-219">Table storage</span></span>

<span data-ttu-id="ed97f-220">儲存資料之後，使用者的狀態會保留，並可在任何新的線路中使用。</span><span class="sxs-lookup"><span data-stu-id="ed97f-220">After data is saved, the user's state is retained and available in any new circuit.</span></span>

<span data-ttu-id="ed97f-221">如需 Azure 資料儲存體選項的詳細資訊，請參閱下列各項：</span><span class="sxs-lookup"><span data-stu-id="ed97f-221">For more information on Azure data storage options, see the following:</span></span>

* [<span data-ttu-id="ed97f-222">Azure 資料庫</span><span class="sxs-lookup"><span data-stu-id="ed97f-222">Azure Databases</span></span>](https://azure.microsoft.com/product-categories/databases/)
* [<span data-ttu-id="ed97f-223">Azure 儲存體檔</span><span class="sxs-lookup"><span data-stu-id="ed97f-223">Azure Storage Documentation</span></span>](/azure/storage/)

### <a name="url"></a><span data-ttu-id="ed97f-224">URL</span><span class="sxs-lookup"><span data-stu-id="ed97f-224">URL</span></span>

<span data-ttu-id="ed97f-225">對於代表導覽狀態的暫時性資料，請將資料模型為 URL 的一部分。</span><span class="sxs-lookup"><span data-stu-id="ed97f-225">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="ed97f-226">在 URL 中模型化使用者狀態的範例包括：</span><span class="sxs-lookup"><span data-stu-id="ed97f-226">Examples of user state modeled in the URL include:</span></span>

* <span data-ttu-id="ed97f-227">已查看之實體的識別碼。</span><span class="sxs-lookup"><span data-stu-id="ed97f-227">The ID of a viewed entity.</span></span>
* <span data-ttu-id="ed97f-228">分頁方格中的目前頁碼。</span><span class="sxs-lookup"><span data-stu-id="ed97f-228">The current page number in a paged grid.</span></span>

<span data-ttu-id="ed97f-229">瀏覽器的網址列內容會保留：</span><span class="sxs-lookup"><span data-stu-id="ed97f-229">The contents of the browser's address bar are retained:</span></span>

* <span data-ttu-id="ed97f-230">如果使用者手動重載頁面。</span><span class="sxs-lookup"><span data-stu-id="ed97f-230">If the user manually reloads the page.</span></span>
* <span data-ttu-id="ed97f-231">如果網頁伺服器變得無法使用，而且會強制使用者重載頁面，以便連接到不同的伺服器。</span><span class="sxs-lookup"><span data-stu-id="ed97f-231">If the web server becomes unavailable, and the user is forced to reload the page in order to connect to a different server.</span></span>

<span data-ttu-id="ed97f-232">如需使用指示詞定義 URL 模式的詳細資訊 [`@page`](xref:mvc/views/razor#page) ，請參閱 <xref:blazor/fundamentals/routing> 。</span><span class="sxs-lookup"><span data-stu-id="ed97f-232">For information on defining URL patterns with the [`@page`](xref:mvc/views/razor#page) directive, see <xref:blazor/fundamentals/routing>.</span></span>

### <a name="browser-storage"></a><span data-ttu-id="ed97f-233">瀏覽器儲存</span><span class="sxs-lookup"><span data-stu-id="ed97f-233">Browser storage</span></span>

<span data-ttu-id="ed97f-234">對於使用者主動建立的暫時性資料，常用的儲存位置是瀏覽器的 [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) 和 [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) 集合：</span><span class="sxs-lookup"><span data-stu-id="ed97f-234">For transient data that the user is actively creating, a commonly used storage location is the browser's [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) collections:</span></span>

* <span data-ttu-id="ed97f-235">`localStorage`的範圍是瀏覽器的視窗。</span><span class="sxs-lookup"><span data-stu-id="ed97f-235">`localStorage` is scoped to the browser's window.</span></span> <span data-ttu-id="ed97f-236">如果使用者重載頁面，或關閉並重新開啟瀏覽器，則狀態會保持不變。</span><span class="sxs-lookup"><span data-stu-id="ed97f-236">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="ed97f-237">如果使用者開啟多個瀏覽器索引標籤，則狀態會在索引標籤之間共用。</span><span class="sxs-lookup"><span data-stu-id="ed97f-237">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="ed97f-238">資料會保存在中， `localStorage` 直到明確清除為止。</span><span class="sxs-lookup"><span data-stu-id="ed97f-238">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="ed97f-239">`sessionStorage`的範圍設定為 [瀏覽器] 索引標籤。如果使用者重載索引標籤，則狀態會保持不變。</span><span class="sxs-lookup"><span data-stu-id="ed97f-239">`sessionStorage` is scoped to the browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="ed97f-240">如果使用者關閉索引標籤或瀏覽器，則狀態會遺失。</span><span class="sxs-lookup"><span data-stu-id="ed97f-240">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="ed97f-241">如果使用者開啟多個瀏覽器索引標籤，每個索引標籤都有自己獨立的資料版本。</span><span class="sxs-lookup"><span data-stu-id="ed97f-241">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

<span data-ttu-id="ed97f-242">一般而言， `sessionStorage` 使用較安全。</span><span class="sxs-lookup"><span data-stu-id="ed97f-242">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="ed97f-243">`sessionStorage`避免使用者開啟多個索引標籤並遇到下列問題的風險：</span><span class="sxs-lookup"><span data-stu-id="ed97f-243">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="ed97f-244">索引標籤上狀態儲存體中的 bug。</span><span class="sxs-lookup"><span data-stu-id="ed97f-244">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="ed97f-245">當索引標籤覆寫其他索引標籤的狀態時，會造成混淆的行為。</span><span class="sxs-lookup"><span data-stu-id="ed97f-245">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="ed97f-246">`localStorage`如果應用程式必須在關閉並重新開啟瀏覽器時保存狀態，這是較好的選擇。</span><span class="sxs-lookup"><span data-stu-id="ed97f-246">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

<span data-ttu-id="ed97f-247">使用瀏覽器儲存的注意事項：</span><span class="sxs-lookup"><span data-stu-id="ed97f-247">Caveats for using browser storage:</span></span>

* <span data-ttu-id="ed97f-248">類似于使用伺服器端資料庫，載入和儲存資料是非同步。</span><span class="sxs-lookup"><span data-stu-id="ed97f-248">Similar to the use of a server-side database, loading and saving data are asynchronous.</span></span>
* <span data-ttu-id="ed97f-249">與伺服器端資料庫不同的是，預先呈現期間無法使用儲存體，因為在預先呈現階段期間，瀏覽器中不存在要求的頁面。</span><span class="sxs-lookup"><span data-stu-id="ed97f-249">Unlike a server-side database, storage isn't available during prerendering because the requested page doesn't exist in the browser during the prerendering stage.</span></span>
* <span data-ttu-id="ed97f-250">儲存幾 kb 的資料可合理保存 Blazor Server 應用程式。</span><span class="sxs-lookup"><span data-stu-id="ed97f-250">Storage of a few kilobytes of data is reasonable to persist for Blazor Server apps.</span></span> <span data-ttu-id="ed97f-251">除了幾 kb 以外，您還必須考慮效能上的影響，因為資料是透過網路載入和儲存。</span><span class="sxs-lookup"><span data-stu-id="ed97f-251">Beyond a few kilobytes, you must consider the performance implications because the data is loaded and saved across the network.</span></span>
* <span data-ttu-id="ed97f-252">使用者可能會看到或篡改資料。</span><span class="sxs-lookup"><span data-stu-id="ed97f-252">Users may view or tamper with the data.</span></span> <span data-ttu-id="ed97f-253">[ASP.NET Core 資料保護](xref:security/data-protection/introduction)可以降低風險。</span><span class="sxs-lookup"><span data-stu-id="ed97f-253">[ASP.NET Core Data Protection](xref:security/data-protection/introduction) can mitigate the risk.</span></span> <span data-ttu-id="ed97f-254">例如， [ASP.NET Core 受保護的瀏覽器存放裝置](#aspnet-core-protected-browser-storage)使用 ASP.NET Core 的資料保護。</span><span class="sxs-lookup"><span data-stu-id="ed97f-254">For example, [ASP.NET Core Protected Browser Storage](#aspnet-core-protected-browser-storage) uses ASP.NET Core Data Protection.</span></span>

<span data-ttu-id="ed97f-255">協力廠商 NuGet 套件提供使用和的 Api `localStorage` `sessionStorage` 。</span><span class="sxs-lookup"><span data-stu-id="ed97f-255">Third-party NuGet packages provide APIs for working with `localStorage` and `sessionStorage`.</span></span> <span data-ttu-id="ed97f-256">值得考慮選擇以透明方式使用[ASP.NET Core 資料保護](xref:security/data-protection/introduction)的套件。</span><span class="sxs-lookup"><span data-stu-id="ed97f-256">It's worth considering choosing a package that transparently uses [ASP.NET Core Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="ed97f-257">資料保護會將儲存的資料加密，並降低篡改已儲存資料的潛在風險。</span><span class="sxs-lookup"><span data-stu-id="ed97f-257">Data Protection encrypts stored data and reduces the potential risk of tampering with stored data.</span></span> <span data-ttu-id="ed97f-258">如果 JSON 序列化資料是以純文字儲存，則使用者可以使用瀏覽器開發人員工具來查看資料，同時也會修改儲存的資料。</span><span class="sxs-lookup"><span data-stu-id="ed97f-258">If JSON-serialized data is stored in plain text, users can see the data using browser developer tools and also modify the stored data.</span></span> <span data-ttu-id="ed97f-259">保護資料不一定會有問題，因為資料在本質上可能是很簡單的。</span><span class="sxs-lookup"><span data-stu-id="ed97f-259">Securing data isn't always a problem because the data might be trivial in nature.</span></span> <span data-ttu-id="ed97f-260">例如，讀取或修改 UI 專案的預存色彩對使用者或組織而言並不會有嚴重的安全性風險。</span><span class="sxs-lookup"><span data-stu-id="ed97f-260">For example, reading or modifying the stored color of a UI element isn't a significant security risk to the user or the organization.</span></span> <span data-ttu-id="ed97f-261">避免允許使用者檢查或篡改*敏感性資料*。</span><span class="sxs-lookup"><span data-stu-id="ed97f-261">Avoid allowing users to inspect or tamper with *sensitive data*.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="aspnet-core-protected-browser-storage"></a><span data-ttu-id="ed97f-262">ASP.NET Core 受保護的瀏覽器存放裝置</span><span class="sxs-lookup"><span data-stu-id="ed97f-262">ASP.NET Core Protected Browser Storage</span></span>

<span data-ttu-id="ed97f-263">ASP.NET Core 受保護的瀏覽器存放裝置會利用與[ASP.NET Core 的資料保護](xref:security/data-protection/introduction) [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) 。</span><span class="sxs-lookup"><span data-stu-id="ed97f-263">ASP.NET Core Protected Browser Storage leverages [ASP.NET Core Data Protection](xref:security/data-protection/introduction) for [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).</span></span>

> [!NOTE]
> <span data-ttu-id="ed97f-264">受保護的瀏覽器存放裝置依賴 ASP.NET Core 資料保護，且僅支援 Blazor Server 應用程式。</span><span class="sxs-lookup"><span data-stu-id="ed97f-264">Protected Browser Storage relies on ASP.NET Core Data Protection and is only supported for Blazor Server apps.</span></span>

### <a name="configuration"></a><span data-ttu-id="ed97f-265">組態</span><span class="sxs-lookup"><span data-stu-id="ed97f-265">Configuration</span></span>

1. <span data-ttu-id="ed97f-266">將套件參考新增至 [`Microsoft.AspNetCore.Components.Web.Extensions`](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions) 。</span><span class="sxs-lookup"><span data-stu-id="ed97f-266">Add a package reference to [`Microsoft.AspNetCore.Components.Web.Extensions`](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions).</span></span>
1. <span data-ttu-id="ed97f-267">在中 `Startup.ConfigureServices` ，呼叫 `AddProtectedBrowserStorage` 以將 `localStorage` 和服務新增 `sessionStorage` 至服務集合：</span><span class="sxs-lookup"><span data-stu-id="ed97f-267">In `Startup.ConfigureServices`, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="ed97f-268">儲存和載入元件中的資料</span><span class="sxs-lookup"><span data-stu-id="ed97f-268">Save and load data within a component</span></span>

<span data-ttu-id="ed97f-269">在需要將資料載入或儲存至瀏覽器儲存體的任何元件中，使用指示詞 [`@inject`](xref:mvc/views/razor#inject) 插入下列任一項的實例：</span><span class="sxs-lookup"><span data-stu-id="ed97f-269">In any component that requires loading or saving data to browser storage, use the [`@inject`](xref:mvc/views/razor#inject) directive to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="ed97f-270">選擇取決於您想要使用的瀏覽器儲存位置。</span><span class="sxs-lookup"><span data-stu-id="ed97f-270">The choice depends on which browser storage location you wish to use.</span></span> <span data-ttu-id="ed97f-271">在下列範例中， `sessionStorage` 會使用：</span><span class="sxs-lookup"><span data-stu-id="ed97f-271">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="ed97f-272">指示詞 `@using` 可以放在應用程式的檔案中， `_Imports.razor` 而不是在元件中。</span><span class="sxs-lookup"><span data-stu-id="ed97f-272">The `@using` directive can be placed in the app's `_Imports.razor` file instead of in the component.</span></span> <span data-ttu-id="ed97f-273">使用檔案 `_Imports.razor` 會讓應用程式或整個應用程式的較大區段可以使用命名空間。</span><span class="sxs-lookup"><span data-stu-id="ed97f-273">Use of the `_Imports.razor` file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="ed97f-274">若要根據 `currentCount` 專案範本，將值保存在 `Counter` 應用程式的元件中 Blazor Server ，請修改 `IncrementCount` 要使用的方法 `ProtectedSessionStore.SetAsync` ：</span><span class="sxs-lookup"><span data-stu-id="ed97f-274">To persist the `currentCount` value in the `Counter` component of an app based on the Blazor Server project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="ed97f-275">在更大、更實際的應用程式中，個別欄位的儲存是不太可能發生的情況。</span><span class="sxs-lookup"><span data-stu-id="ed97f-275">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="ed97f-276">應用程式較可能儲存包含複雜狀態的整個模型物件。</span><span class="sxs-lookup"><span data-stu-id="ed97f-276">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="ed97f-277">`ProtectedSessionStore`自動序列化和還原序列化 JSON 資料，以儲存複雜的狀態物件。</span><span class="sxs-lookup"><span data-stu-id="ed97f-277">`ProtectedSessionStore` automatically serializes and deserializes JSON data to store complex state objects.</span></span>

<span data-ttu-id="ed97f-278">在上述程式碼範例中， `currentCount` 資料會 `sessionStorage['count']` 在使用者的瀏覽器中儲存為。</span><span class="sxs-lookup"><span data-stu-id="ed97f-278">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="ed97f-279">資料不會以純文字儲存，而是使用 ASP.NET Core 資料保護來保護。</span><span class="sxs-lookup"><span data-stu-id="ed97f-279">The data isn't stored in plain text but rather is protected using ASP.NET Core Data Protection.</span></span> <span data-ttu-id="ed97f-280">如果 `sessionStorage['count']` 是在瀏覽器的開發人員主控台中評估，則可以檢查加密的資料。</span><span class="sxs-lookup"><span data-stu-id="ed97f-280">The encrypted data can be inspected if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="ed97f-281">若要 `currentCount` 在使用者稍後回到元件時復原資料 `Counter` ，包括使用者是否在新的線路上，請使用 `ProtectedSessionStore.GetAsync` ：</span><span class="sxs-lookup"><span data-stu-id="ed97f-281">To recover the `currentCount` data if the user returns to the `Counter` component later, including if the user is on a new circuit, use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    var result = await ProtectedSessionStore.GetAsync<int>("count");
    currentCount = result.Success ? result.Value : 0;
}
```

<span data-ttu-id="ed97f-282">如果元件的參數包含導覽狀態，請呼叫， `ProtectedSessionStore.GetAsync` 並 `null` 在中指派非結果 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> ，而不是 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 。</span><span class="sxs-lookup"><span data-stu-id="ed97f-282">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign a non-`null` result in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, not <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span> <span data-ttu-id="ed97f-283"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>只有在第一次具現化元件時，才會呼叫。</span><span class="sxs-lookup"><span data-stu-id="ed97f-283"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is only called once when the component is first instantiated.</span></span> <span data-ttu-id="ed97f-284"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>如果使用者流覽至不同的 URL，但仍在相同頁面上，則不會再呼叫一次。</span><span class="sxs-lookup"><span data-stu-id="ed97f-284"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="ed97f-285">如需詳細資訊，請參閱<xref:blazor/components/lifecycle>。</span><span class="sxs-lookup"><span data-stu-id="ed97f-285">For more information, see <xref:blazor/components/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="ed97f-286">此章節中的範例僅適用于伺服器未啟用預先安裝的情況。</span><span class="sxs-lookup"><span data-stu-id="ed97f-286">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="ed97f-287">啟用自動處理時，會產生錯誤，說明無法發出 JavaScript interop 呼叫，因為元件正在資源清單中。</span><span class="sxs-lookup"><span data-stu-id="ed97f-287">With prerendering enabled, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>
>
> <span data-ttu-id="ed97f-288">請停用已選擇的，或加入額外的程式碼以使用可處理的。</span><span class="sxs-lookup"><span data-stu-id="ed97f-288">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="ed97f-289">若要深入瞭解如何撰寫可搭配已預呈現運作的程式碼，請參閱[處理預呈現](#handle-prerendering)一節。</span><span class="sxs-lookup"><span data-stu-id="ed97f-289">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="ed97f-290">處理載入狀態</span><span class="sxs-lookup"><span data-stu-id="ed97f-290">Handle the loading state</span></span>

<span data-ttu-id="ed97f-291">由於瀏覽器存放裝置是透過網路連接以非同步方式存取，因此在載入資料並提供給元件之前，一律會有一段時間。</span><span class="sxs-lookup"><span data-stu-id="ed97f-291">Since browser storage is accessed asynchronously over a network connection, there's always a period of time before the data is loaded and available to a component.</span></span> <span data-ttu-id="ed97f-292">若要獲得最佳結果，請在載入進行時轉譯載入狀態訊息，而不是顯示空白或預設的資料。</span><span class="sxs-lookup"><span data-stu-id="ed97f-292">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="ed97f-293">其中一種方法是追蹤資料是否為 `null` ，這表示資料仍在載入中。</span><span class="sxs-lookup"><span data-stu-id="ed97f-293">One approach is to track whether the data is `null`, which means that the data is still loading.</span></span> <span data-ttu-id="ed97f-294">在預設 `Counter` 元件中，計數會保留在中 `int` 。</span><span class="sxs-lookup"><span data-stu-id="ed97f-294">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="ed97f-295">將問號 () 加入 () 的類型，[使其成為 `currentCount` 可為 null](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) `?` `int` ：</span><span class="sxs-lookup"><span data-stu-id="ed97f-295">[Make `currentCount` nullable](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="ed97f-296">若不是無條件顯示計數和 **`Increment`** 按鈕，只會在透過檢查來載入資料時，才顯示這些元素 <xref:System.Nullable%601.HasValue%2A> ：</span><span class="sxs-lookup"><span data-stu-id="ed97f-296">Instead of unconditionally displaying the count and **`Increment`** button, display these elements only if the data is loaded by checking <xref:System.Nullable%601.HasValue%2A>:</span></span>

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

### <a name="handle-prerendering"></a><span data-ttu-id="ed97f-297">處理已預呈現</span><span class="sxs-lookup"><span data-stu-id="ed97f-297">Handle prerendering</span></span>

<span data-ttu-id="ed97f-298">在預做期間：</span><span class="sxs-lookup"><span data-stu-id="ed97f-298">During prerendering:</span></span>

* <span data-ttu-id="ed97f-299">與使用者的瀏覽器之間的互動連接不存在。</span><span class="sxs-lookup"><span data-stu-id="ed97f-299">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="ed97f-300">瀏覽器還沒有可執行 JavaScript 程式碼的頁面。</span><span class="sxs-lookup"><span data-stu-id="ed97f-300">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="ed97f-301">`localStorage`或 `sessionStorage` 在預做期間無法使用。</span><span class="sxs-lookup"><span data-stu-id="ed97f-301">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="ed97f-302">如果元件嘗試與儲存體互動，則會產生錯誤，說明無法發出 JavaScript interop 呼叫，因為元件正在資源清單中。</span><span class="sxs-lookup"><span data-stu-id="ed97f-302">If the component attempts to interact with storage, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>

<span data-ttu-id="ed97f-303">解決錯誤的其中一種方法是停用已處理的。</span><span class="sxs-lookup"><span data-stu-id="ed97f-303">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="ed97f-304">如果應用程式大量使用以瀏覽器為基礎的存放裝置，這通常是最佳的選擇。</span><span class="sxs-lookup"><span data-stu-id="ed97f-304">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="ed97f-305">因為應用程式無法提供任何有用的內容，直到 `localStorage` 或可供使用，因此會增加複雜性並不會讓應用程式受益 `sessionStorage` 。</span><span class="sxs-lookup"><span data-stu-id="ed97f-305">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="ed97f-306">若要停用預呈現，請開啟檔案， `Pages/_Host.cshtml` 然後將 `render-mode` [元件標記](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)協助程式的屬性變更為 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> ：</span><span class="sxs-lookup"><span data-stu-id="ed97f-306">To disable prerendering, open the `Pages/_Host.cshtml` file and change the `render-mode` attribute of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>:</span></span>

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

<span data-ttu-id="ed97f-307">如果是其他未使用或的頁面，則會進行預呈現 `localStorage` `sessionStorage` 。</span><span class="sxs-lookup"><span data-stu-id="ed97f-307">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="ed97f-308">若要保留已呈現，請延遲載入作業，直到瀏覽器連線到線路為止。</span><span class="sxs-lookup"><span data-stu-id="ed97f-308">To retain prerendering, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="ed97f-309">以下是儲存計數器值的範例：</span><span class="sxs-lookup"><span data-stu-id="ed97f-309">The following is an example for storing a counter value:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions
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

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="ed97f-310">將狀態保留分解為一般位置</span><span class="sxs-lookup"><span data-stu-id="ed97f-310">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="ed97f-311">如果許多元件都依賴以瀏覽器為基礎的儲存體，則重新執行狀態提供者程式碼很多次會建立程式碼重複。</span><span class="sxs-lookup"><span data-stu-id="ed97f-311">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="ed97f-312">避免程式碼重複的其中一個選項是建立一個可封裝狀態提供者邏輯的*狀態供應器父元件*。</span><span class="sxs-lookup"><span data-stu-id="ed97f-312">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="ed97f-313">子元件可以使用持續性資料，而不考慮狀態持續性機制。</span><span class="sxs-lookup"><span data-stu-id="ed97f-313">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="ed97f-314">在下列元件範例中 `CounterStateProvider` ，計數器資料會保存到 `sessionStorage` ：</span><span class="sxs-lookup"><span data-stu-id="ed97f-314">In the following example of a `CounterStateProvider` component, counter data is persisted to `sessionStorage`:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions
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

<span data-ttu-id="ed97f-315">`CounterStateProvider`元件在載入完成之前，不會呈現其子內容來處理載入階段。</span><span class="sxs-lookup"><span data-stu-id="ed97f-315">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="ed97f-316">若要使用 `CounterStateProvider` 元件，請將元件的實例包裝在需要存取計數器狀態的任何其他元件周圍。</span><span class="sxs-lookup"><span data-stu-id="ed97f-316">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="ed97f-317">若要讓應用程式中的所有元件都能存取該狀態，請將 `CounterStateProvider` 元件包裝 <xref:Microsoft.AspNetCore.Components.Routing.Router> 在 `App` 元件 (`App.razor`) 中：</span><span class="sxs-lookup"><span data-stu-id="ed97f-317">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the <xref:Microsoft.AspNetCore.Components.Routing.Router> in the `App` component (`App.razor`):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

<span data-ttu-id="ed97f-318">包裝的元件會接收並可修改保存的計數器狀態。</span><span class="sxs-lookup"><span data-stu-id="ed97f-318">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="ed97f-319">下列 `Counter` 元件會執行模式：</span><span class="sxs-lookup"><span data-stu-id="ed97f-319">The following `Counter` component implements the pattern:</span></span>

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

<span data-ttu-id="ed97f-320">先前的元件不需要與互動 `ProtectedBrowserStorage` ，也不會處理「載入」階段。</span><span class="sxs-lookup"><span data-stu-id="ed97f-320">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="ed97f-321">如先前所述，若要處理已進行的已 `CounterStateProvider` 修訂，可以修改，讓取用計數器資料的所有元件都能自動以可處理方式使用。</span><span class="sxs-lookup"><span data-stu-id="ed97f-321">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="ed97f-322">如需詳細資訊，請參閱[處理預呈現](#handle-prerendering)一節。</span><span class="sxs-lookup"><span data-stu-id="ed97f-322">For more information, see the [Handle prerendering](#handle-prerendering) section.</span></span>

<span data-ttu-id="ed97f-323">一般情況下，建議使用*狀態供應器的父元件*模式：</span><span class="sxs-lookup"><span data-stu-id="ed97f-323">In general, the *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="ed97f-324">跨許多元件取用狀態。</span><span class="sxs-lookup"><span data-stu-id="ed97f-324">To consume state across many components.</span></span>
* <span data-ttu-id="ed97f-325">如果只有一個最上層狀態物件要保存，則為。</span><span class="sxs-lookup"><span data-stu-id="ed97f-325">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="ed97f-326">若要保存許多不同的狀態物件，並在不同位置使用不同的物件子集，最好避免全域保存狀態。</span><span class="sxs-lookup"><span data-stu-id="ed97f-326">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid persisting state globally.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="protected-browser-storage-experimental-nuget-package"></a><span data-ttu-id="ed97f-327">受保護的瀏覽器儲存體實驗性 NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="ed97f-327">Protected Browser Storage experimental NuGet package</span></span>

<span data-ttu-id="ed97f-328">ASP.NET Core 受保護的瀏覽器存放裝置會利用與[ASP.NET Core 的資料保護](xref:security/data-protection/introduction) [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) 。</span><span class="sxs-lookup"><span data-stu-id="ed97f-328">ASP.NET Core Protected Browser Storage leverages [ASP.NET Core Data Protection](xref:security/data-protection/introduction) for [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).</span></span>

> [!WARNING]
> <span data-ttu-id="ed97f-329">`Microsoft.AspNetCore.ProtectedBrowserStorage`是不支援的實驗性封裝，不適用於生產用途。</span><span class="sxs-lookup"><span data-stu-id="ed97f-329">`Microsoft.AspNetCore.ProtectedBrowserStorage` is an unsupported, experimental package unsuitable for production use.</span></span>
>
> <span data-ttu-id="ed97f-330">套件僅適用于 ASP.NET Core 3.1 Blazor Server 應用程式。</span><span class="sxs-lookup"><span data-stu-id="ed97f-330">The package is only available for use in ASP.NET Core 3.1 Blazor Server apps.</span></span>

### <a name="configuration"></a><span data-ttu-id="ed97f-331">組態</span><span class="sxs-lookup"><span data-stu-id="ed97f-331">Configuration</span></span>

1. <span data-ttu-id="ed97f-332">將套件參考新增至 [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage) 。</span><span class="sxs-lookup"><span data-stu-id="ed97f-332">Add a package reference to [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>
1. <span data-ttu-id="ed97f-333">在檔案中 `Pages/_Host.cshtml` ，于結束記號內新增下列腳本 `</body>` ：</span><span class="sxs-lookup"><span data-stu-id="ed97f-333">In the `Pages/_Host.cshtml` file, add the following script inside the closing `</body>` tag:</span></span>

   ```cshtml
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. <span data-ttu-id="ed97f-334">在中 `Startup.ConfigureServices` ，呼叫 `AddProtectedBrowserStorage` 以將 `localStorage` 和服務新增 `sessionStorage` 至服務集合：</span><span class="sxs-lookup"><span data-stu-id="ed97f-334">In `Startup.ConfigureServices`, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="ed97f-335">儲存和載入元件中的資料</span><span class="sxs-lookup"><span data-stu-id="ed97f-335">Save and load data within a component</span></span>

<span data-ttu-id="ed97f-336">在需要將資料載入或儲存至瀏覽器儲存體的任何元件中，使用指示詞 [`@inject`](xref:mvc/views/razor#inject) 插入下列任一項的實例：</span><span class="sxs-lookup"><span data-stu-id="ed97f-336">In any component that requires loading or saving data to browser storage, use the [`@inject`](xref:mvc/views/razor#inject) directive to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="ed97f-337">選擇取決於您想要使用的瀏覽器儲存位置。</span><span class="sxs-lookup"><span data-stu-id="ed97f-337">The choice depends on which browser storage location you wish to use.</span></span> <span data-ttu-id="ed97f-338">在下列範例中， `sessionStorage` 會使用：</span><span class="sxs-lookup"><span data-stu-id="ed97f-338">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="ed97f-339">`@using`語句可以放在檔案中， `_Imports.razor` 而不是放置在元件中。</span><span class="sxs-lookup"><span data-stu-id="ed97f-339">The `@using` statement can be placed into an `_Imports.razor` file instead of in the component.</span></span> <span data-ttu-id="ed97f-340">使用檔案 `_Imports.razor` 會讓應用程式或整個應用程式的較大區段可以使用命名空間。</span><span class="sxs-lookup"><span data-stu-id="ed97f-340">Use of the `_Imports.razor` file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="ed97f-341">若要根據 `currentCount` 專案範本，將值保存在 `Counter` 應用程式的元件中 Blazor Server ，請修改 `IncrementCount` 要使用的方法 `ProtectedSessionStore.SetAsync` ：</span><span class="sxs-lookup"><span data-stu-id="ed97f-341">To persist the `currentCount` value in the `Counter` component of an app based on the Blazor Server project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="ed97f-342">在更大、更實際的應用程式中，個別欄位的儲存是不太可能發生的情況。</span><span class="sxs-lookup"><span data-stu-id="ed97f-342">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="ed97f-343">應用程式較可能儲存包含複雜狀態的整個模型物件。</span><span class="sxs-lookup"><span data-stu-id="ed97f-343">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="ed97f-344">`ProtectedSessionStore`自動序列化和還原序列化 JSON 資料。</span><span class="sxs-lookup"><span data-stu-id="ed97f-344">`ProtectedSessionStore` automatically serializes and deserializes JSON data.</span></span>

<span data-ttu-id="ed97f-345">在上述程式碼範例中， `currentCount` 資料會 `sessionStorage['count']` 在使用者的瀏覽器中儲存為。</span><span class="sxs-lookup"><span data-stu-id="ed97f-345">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="ed97f-346">資料不會以純文字儲存，而是使用 ASP.NET Core 資料保護來保護。</span><span class="sxs-lookup"><span data-stu-id="ed97f-346">The data isn't stored in plain text but rather is protected using ASP.NET Core Data Protection.</span></span> <span data-ttu-id="ed97f-347">如果 `sessionStorage['count']` 是在瀏覽器的開發人員主控台中評估，則可以檢查加密的資料。</span><span class="sxs-lookup"><span data-stu-id="ed97f-347">The encrypted data can be inspected if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="ed97f-348">若要在 `currentCount` 使用者稍後回到元件時復原資料 `Counter` ，包括如果它們是在全新的線路上，請使用 `ProtectedSessionStore.GetAsync` ：</span><span class="sxs-lookup"><span data-stu-id="ed97f-348">To recover the `currentCount` data if the user returns to the `Counter` component later, including if they're on an entirely new circuit, use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

<span data-ttu-id="ed97f-349">如果元件的參數包含導覽狀態，請呼叫 `ProtectedSessionStore.GetAsync` 並在中指派結果 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> ，而不是 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 。</span><span class="sxs-lookup"><span data-stu-id="ed97f-349">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign the result in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, not <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span> <span data-ttu-id="ed97f-350"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>只有在第一次具現化元件時，才會呼叫。</span><span class="sxs-lookup"><span data-stu-id="ed97f-350"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is only called once when the component is first instantiated.</span></span> <span data-ttu-id="ed97f-351"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>如果使用者流覽至不同的 URL，但仍在相同頁面上，則不會再呼叫一次。</span><span class="sxs-lookup"><span data-stu-id="ed97f-351"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="ed97f-352">如需詳細資訊，請參閱<xref:blazor/components/lifecycle>。</span><span class="sxs-lookup"><span data-stu-id="ed97f-352">For more information, see <xref:blazor/components/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="ed97f-353">此章節中的範例僅適用于伺服器未啟用預先安裝的情況。</span><span class="sxs-lookup"><span data-stu-id="ed97f-353">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="ed97f-354">啟用自動處理時，會產生錯誤，說明無法發出 JavaScript interop 呼叫，因為元件正在資源清單中。</span><span class="sxs-lookup"><span data-stu-id="ed97f-354">With prerendering enabled, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>
>
> <span data-ttu-id="ed97f-355">請停用已選擇的，或加入額外的程式碼以使用可處理的。</span><span class="sxs-lookup"><span data-stu-id="ed97f-355">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="ed97f-356">若要深入瞭解如何撰寫可搭配已預呈現運作的程式碼，請參閱[處理預呈現](#handle-prerendering)一節。</span><span class="sxs-lookup"><span data-stu-id="ed97f-356">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="ed97f-357">處理載入狀態</span><span class="sxs-lookup"><span data-stu-id="ed97f-357">Handle the loading state</span></span>

<span data-ttu-id="ed97f-358">由於瀏覽器存放裝置是透過網路連接以非同步方式存取，因此在載入資料並提供給元件之前，一律會有一段時間。</span><span class="sxs-lookup"><span data-stu-id="ed97f-358">Since browser storage is accessed asynchronously over a network connection, there's always a period of time before the data is loaded and available to a component.</span></span> <span data-ttu-id="ed97f-359">若要獲得最佳結果，請在載入進行時轉譯載入狀態訊息，而不是顯示空白或預設的資料。</span><span class="sxs-lookup"><span data-stu-id="ed97f-359">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="ed97f-360">其中一種方法是追蹤資料是否為 `null` ，這表示資料仍在載入中。</span><span class="sxs-lookup"><span data-stu-id="ed97f-360">One approach is to track whether the data is `null`, which means that the data is still loading.</span></span> <span data-ttu-id="ed97f-361">在預設 `Counter` 元件中，計數會保留在中 `int` 。</span><span class="sxs-lookup"><span data-stu-id="ed97f-361">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="ed97f-362">將問號 () 加入 () 的類型，[使其成為 `currentCount` 可為 null](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) `?` `int` ：</span><span class="sxs-lookup"><span data-stu-id="ed97f-362">[Make `currentCount` nullable](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="ed97f-363">不是無條件顯示計數和 **`Increment`** 按鈕，而是只在載入資料時，選擇顯示這些元素：</span><span class="sxs-lookup"><span data-stu-id="ed97f-363">Instead of unconditionally displaying the count and **`Increment`** button, choose to display these elements only if the data is loaded:</span></span>

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

### <a name="handle-prerendering"></a><span data-ttu-id="ed97f-364">處理已預呈現</span><span class="sxs-lookup"><span data-stu-id="ed97f-364">Handle prerendering</span></span>

<span data-ttu-id="ed97f-365">在預做期間：</span><span class="sxs-lookup"><span data-stu-id="ed97f-365">During prerendering:</span></span>

* <span data-ttu-id="ed97f-366">與使用者的瀏覽器之間的互動連接不存在。</span><span class="sxs-lookup"><span data-stu-id="ed97f-366">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="ed97f-367">瀏覽器還沒有可執行 JavaScript 程式碼的頁面。</span><span class="sxs-lookup"><span data-stu-id="ed97f-367">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="ed97f-368">`localStorage`或 `sessionStorage` 在預做期間無法使用。</span><span class="sxs-lookup"><span data-stu-id="ed97f-368">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="ed97f-369">如果元件嘗試與儲存體互動，則會產生錯誤，說明無法發出 JavaScript interop 呼叫，因為元件正在資源清單中。</span><span class="sxs-lookup"><span data-stu-id="ed97f-369">If the component attempts to interact with storage, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>

<span data-ttu-id="ed97f-370">解決錯誤的其中一種方法是停用已處理的。</span><span class="sxs-lookup"><span data-stu-id="ed97f-370">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="ed97f-371">如果應用程式大量使用以瀏覽器為基礎的存放裝置，這通常是最佳的選擇。</span><span class="sxs-lookup"><span data-stu-id="ed97f-371">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="ed97f-372">因為應用程式無法提供任何有用的內容，直到 `localStorage` 或可供使用，因此會增加複雜性並不會讓應用程式受益 `sessionStorage` 。</span><span class="sxs-lookup"><span data-stu-id="ed97f-372">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="ed97f-373">若要停用預呈現，請開啟檔案， `Pages/_Host.cshtml` 然後將 `render-mode` [元件標記](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)協助程式的屬性變更為 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> ：</span><span class="sxs-lookup"><span data-stu-id="ed97f-373">To disable prerendering, open the `Pages/_Host.cshtml` file and change the `render-mode` attribute of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>:</span></span>

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

<span data-ttu-id="ed97f-374">如果是其他未使用或的頁面，則會進行預呈現 `localStorage` `sessionStorage` 。</span><span class="sxs-lookup"><span data-stu-id="ed97f-374">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="ed97f-375">若要保留已呈現，請延遲載入作業，直到瀏覽器連線到線路為止。</span><span class="sxs-lookup"><span data-stu-id="ed97f-375">To retain prerendering, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="ed97f-376">以下是儲存計數器值的範例：</span><span class="sxs-lookup"><span data-stu-id="ed97f-376">The following is an example for storing a counter value:</span></span>

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

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="ed97f-377">將狀態保留分解為一般位置</span><span class="sxs-lookup"><span data-stu-id="ed97f-377">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="ed97f-378">如果許多元件都依賴以瀏覽器為基礎的儲存體，則重新執行狀態提供者程式碼很多次會建立程式碼重複。</span><span class="sxs-lookup"><span data-stu-id="ed97f-378">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="ed97f-379">避免程式碼重複的其中一個選項是建立一個可封裝狀態提供者邏輯的*狀態供應器父元件*。</span><span class="sxs-lookup"><span data-stu-id="ed97f-379">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="ed97f-380">子元件可以使用持續性資料，而不考慮狀態持續性機制。</span><span class="sxs-lookup"><span data-stu-id="ed97f-380">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="ed97f-381">在下列元件範例中 `CounterStateProvider` ，計數器資料會保存到 `sessionStorage` ：</span><span class="sxs-lookup"><span data-stu-id="ed97f-381">In the following example of a `CounterStateProvider` component, counter data is persisted to `sessionStorage`:</span></span>

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

<span data-ttu-id="ed97f-382">`CounterStateProvider`元件在載入完成之前，不會呈現其子內容來處理載入階段。</span><span class="sxs-lookup"><span data-stu-id="ed97f-382">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="ed97f-383">若要使用 `CounterStateProvider` 元件，請將元件的實例包裝在需要存取計數器狀態的任何其他元件周圍。</span><span class="sxs-lookup"><span data-stu-id="ed97f-383">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="ed97f-384">若要讓應用程式中的所有元件都能存取該狀態，請將 `CounterStateProvider` 元件包裝 <xref:Microsoft.AspNetCore.Components.Routing.Router> 在 `App` 元件 (`App.razor`) 中：</span><span class="sxs-lookup"><span data-stu-id="ed97f-384">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the <xref:Microsoft.AspNetCore.Components.Routing.Router> in the `App` component (`App.razor`):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

<span data-ttu-id="ed97f-385">包裝的元件會接收並可修改保存的計數器狀態。</span><span class="sxs-lookup"><span data-stu-id="ed97f-385">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="ed97f-386">下列 `Counter` 元件會執行模式：</span><span class="sxs-lookup"><span data-stu-id="ed97f-386">The following `Counter` component implements the pattern:</span></span>

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

<span data-ttu-id="ed97f-387">先前的元件不需要與互動 `ProtectedBrowserStorage` ，也不會處理「載入」階段。</span><span class="sxs-lookup"><span data-stu-id="ed97f-387">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="ed97f-388">如先前所述，若要處理已進行的已 `CounterStateProvider` 修訂，可以修改，讓取用計數器資料的所有元件都能自動以可處理方式使用。</span><span class="sxs-lookup"><span data-stu-id="ed97f-388">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="ed97f-389">如需詳細資訊，請參閱[處理預呈現](#handle-prerendering)一節。</span><span class="sxs-lookup"><span data-stu-id="ed97f-389">For more information, see the [Handle prerendering](#handle-prerendering) section.</span></span>

<span data-ttu-id="ed97f-390">一般情況下，建議使用*狀態供應器父元件*模式：</span><span class="sxs-lookup"><span data-stu-id="ed97f-390">In general, *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="ed97f-391">跨許多元件取用狀態。</span><span class="sxs-lookup"><span data-stu-id="ed97f-391">To consume state across many components.</span></span>
* <span data-ttu-id="ed97f-392">如果只有一個最上層狀態物件要保存，則為。</span><span class="sxs-lookup"><span data-stu-id="ed97f-392">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="ed97f-393">若要保存許多不同的狀態物件，並在不同位置使用不同的物件子集，最好避免全域保存狀態。</span><span class="sxs-lookup"><span data-stu-id="ed97f-393">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid persisting state globally.</span></span>

::: moniker-end

::: zone-end
