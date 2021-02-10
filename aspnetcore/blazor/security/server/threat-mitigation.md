---
title: ASP.NET Core 的威脅風險降低指導方針 Blazor Server
author: guardrex
description: 瞭解如何降低應用程式的安全性威脅 Blazor Server 。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/09/2020
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
uid: blazor/security/server/threat-mitigation
ms.openlocfilehash: 270589b1effc8d82f54d2760b315fa2686699f67
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/10/2021
ms.locfileid: "100106748"
---
# <a name="threat-mitigation-guidance-for-aspnet-core-blazor-server"></a><span data-ttu-id="476a4-103">ASP.NET Core 的威脅風險降低指導方針 Blazor Server</span><span class="sxs-lookup"><span data-stu-id="476a4-103">Threat mitigation guidance for ASP.NET Core Blazor Server</span></span>

<span data-ttu-id="476a4-104">[Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="476a4-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="476a4-105">Blazor Server 應用程式採用具 *狀態* 的資料處理模型，其中伺服器和用戶端會維持長期的關聯性。</span><span class="sxs-lookup"><span data-stu-id="476a4-105">Blazor Server apps adopt a *stateful* data processing model, where the server and client maintain a long-lived relationship.</span></span> <span data-ttu-id="476a4-106">持續性狀態是由 [電路](xref:blazor/state-management)維護，它可以跨越也可能長期存在的連接。</span><span class="sxs-lookup"><span data-stu-id="476a4-106">The persistent state is maintained by a [circuit](xref:blazor/state-management), which can span connections that are also potentially long-lived.</span></span>

<span data-ttu-id="476a4-107">當使用者造訪網站時 Blazor Server ，伺服器會在伺服器的記憶體中建立線路。</span><span class="sxs-lookup"><span data-stu-id="476a4-107">When a user visits a Blazor Server site, the server creates a circuit in the server's memory.</span></span> <span data-ttu-id="476a4-108">線路會向瀏覽器指出要轉譯的內容，並回應事件，例如當使用者在 UI 中選取按鈕時。</span><span class="sxs-lookup"><span data-stu-id="476a4-108">The circuit indicates to the browser what content to render and responds to events, such as when the user selects a button in the UI.</span></span> <span data-ttu-id="476a4-109">若要執行這些動作，線路會在使用者的瀏覽器中叫用 JavaScript 函式，並在伺服器上叫用 .NET 方法。</span><span class="sxs-lookup"><span data-stu-id="476a4-109">To perform these actions, a circuit invokes JavaScript functions in the user's browser and .NET methods on the server.</span></span> <span data-ttu-id="476a4-110">這種以 JavaScript 為基礎的雙向互動稱為 [javascript interop (JS interop) ](xref:blazor/call-javascript-from-dotnet)。</span><span class="sxs-lookup"><span data-stu-id="476a4-110">This two-way JavaScript-based interaction is referred to as [JavaScript interop (JS interop)](xref:blazor/call-javascript-from-dotnet).</span></span>

<span data-ttu-id="476a4-111">由於 JS interop 是在網際網路上進行，而且用戶端使用遠端瀏覽器，因此 Blazor Server 應用程式會共用大部分的 web 應用程式安全性考慮。</span><span class="sxs-lookup"><span data-stu-id="476a4-111">Because JS interop occurs over the Internet and the client uses a remote browser, Blazor Server apps share most web app security concerns.</span></span> <span data-ttu-id="476a4-112">本主題說明對應用程式常見的威脅 Blazor Server ，並針對網際網路面向的應用程式提供威脅防護指引。</span><span class="sxs-lookup"><span data-stu-id="476a4-112">This topic describes common threats to Blazor Server apps and provides threat mitigation guidance focused on Internet-facing apps.</span></span>

<span data-ttu-id="476a4-113">在受限制的環境中（例如公司網路或內部網路），部分風險降低指導方針包括：</span><span class="sxs-lookup"><span data-stu-id="476a4-113">In constrained environments, such as inside corporate networks or intranets, some of the mitigation guidance either:</span></span>

* <span data-ttu-id="476a4-114">不適用於受限的環境。</span><span class="sxs-lookup"><span data-stu-id="476a4-114">Doesn't apply in the constrained environment.</span></span>
* <span data-ttu-id="476a4-115">由於安全性風險在受限的環境中很低，因此不值得實行成本。</span><span class="sxs-lookup"><span data-stu-id="476a4-115">Isn't worth the cost to implement because the security risk is low in a constrained environment.</span></span>

## <a name="blazor-and-shared-state"></a><span data-ttu-id="476a4-116">Blazor 和共用狀態</span><span class="sxs-lookup"><span data-stu-id="476a4-116">Blazor and shared state</span></span>

[!INCLUDE[](~/blazor/includes/security/blazor-shared-state.md)]

## <a name="resource-exhaustion"></a><span data-ttu-id="476a4-117">資源耗盡</span><span class="sxs-lookup"><span data-stu-id="476a4-117">Resource exhaustion</span></span>

<span data-ttu-id="476a4-118">當用戶端與伺服器互動，而導致伺服器耗用過多資源時，就可能會發生資源耗盡。</span><span class="sxs-lookup"><span data-stu-id="476a4-118">Resource exhaustion can occur when a client interacts with the server and causes the server to consume excessive resources.</span></span> <span data-ttu-id="476a4-119">過度耗用資源主要會影響：</span><span class="sxs-lookup"><span data-stu-id="476a4-119">Excessive resource consumption primarily affects:</span></span>

* [<span data-ttu-id="476a4-120">CPU</span><span class="sxs-lookup"><span data-stu-id="476a4-120">CPU</span></span>](#cpu)
* [<span data-ttu-id="476a4-121">記憶體</span><span class="sxs-lookup"><span data-stu-id="476a4-121">Memory</span></span>](#memory)
* [<span data-ttu-id="476a4-122">用戶端連接</span><span class="sxs-lookup"><span data-stu-id="476a4-122">Client connections</span></span>](#client-connections)

<span data-ttu-id="476a4-123">拒絕服務 (DoS) 攻擊通常會試圖耗盡應用程式或伺服器的資源。</span><span class="sxs-lookup"><span data-stu-id="476a4-123">Denial of service (DoS) attacks usually seek to exhaust an app or server's resources.</span></span> <span data-ttu-id="476a4-124">不過，資源耗盡不一定是系統攻擊的結果。</span><span class="sxs-lookup"><span data-stu-id="476a4-124">However, resource exhaustion isn't necessarily the result of an attack on the system.</span></span> <span data-ttu-id="476a4-125">例如，有限的資源可能會因為高使用者需求而用盡。</span><span class="sxs-lookup"><span data-stu-id="476a4-125">For example, finite resources can be exhausted due to high user demand.</span></span> <span data-ttu-id="476a4-126">DoS 將在 [拒絕服務 (dos) 的攻擊](#denial-of-service-dos-attacks) 一節中進一步討論。</span><span class="sxs-lookup"><span data-stu-id="476a4-126">DoS is covered further in the [Denial of service (DoS) attacks](#denial-of-service-dos-attacks) section.</span></span>

<span data-ttu-id="476a4-127">架構外部的資源（ Blazor 例如資料庫和檔案控制代碼） (用來讀取和寫入檔案) ，也可能會遇到資源耗盡。</span><span class="sxs-lookup"><span data-stu-id="476a4-127">Resources external to the Blazor framework, such as databases and file handles (used to read and write files), may also experience resource exhaustion.</span></span> <span data-ttu-id="476a4-128">如需詳細資訊，請參閱<xref:performance/performance-best-practices>。</span><span class="sxs-lookup"><span data-stu-id="476a4-128">For more information, see <xref:performance/performance-best-practices>.</span></span>

### <a name="cpu"></a><span data-ttu-id="476a4-129">CPU</span><span class="sxs-lookup"><span data-stu-id="476a4-129">CPU</span></span>

<span data-ttu-id="476a4-130">當一或多個用戶端強制服務器執行密集的 CPU 工作時，可能會發生 CPU 耗盡。</span><span class="sxs-lookup"><span data-stu-id="476a4-130">CPU exhaustion can occur when one or more clients force the server to perform intensive CPU work.</span></span>

<span data-ttu-id="476a4-131">例如，假設有一個 Blazor Server 應用程式會計算 *Fibonnacci 數位*。</span><span class="sxs-lookup"><span data-stu-id="476a4-131">For example, consider a Blazor Server app that calculates a *Fibonnacci number*.</span></span> <span data-ttu-id="476a4-132">Fibonnacci 數位是從 Fibonnacci 序列產生的，其中序列中的每個數位都是上述兩個數字的總和。</span><span class="sxs-lookup"><span data-stu-id="476a4-132">A Fibonnacci number is produced from a Fibonnacci sequence, where each number in the sequence is the sum of the two preceding numbers.</span></span> <span data-ttu-id="476a4-133">到達答案所需的工作量取決於序列的長度和初始值的大小。</span><span class="sxs-lookup"><span data-stu-id="476a4-133">The amount of work required to reach the answer depends on the length of the sequence and the size of the initial value.</span></span> <span data-ttu-id="476a4-134">如果應用程式未對用戶端的要求進行限制，則需要大量 CPU 的計算可能會讓 CPU 的時間降低，並降低其他工作的效能。</span><span class="sxs-lookup"><span data-stu-id="476a4-134">If the app doesn't place limits on a client's request, the CPU-intensive calculations may dominate the CPU's time and diminish the performance of other tasks.</span></span> <span data-ttu-id="476a4-135">過度耗用資源是安全性考慮，會影響可用性。</span><span class="sxs-lookup"><span data-stu-id="476a4-135">Excessive resource consumption is a security concern impacting availability.</span></span>

<span data-ttu-id="476a4-136">CPU 耗盡是所有公開應用程式的考慮。</span><span class="sxs-lookup"><span data-stu-id="476a4-136">CPU exhaustion is a concern for all public-facing apps.</span></span> <span data-ttu-id="476a4-137">在一般 web 應用程式中，要求和連接會以安全的形式 Blazor Server 提供，但應用程式不會提供相同的保護。</span><span class="sxs-lookup"><span data-stu-id="476a4-137">In regular web apps, requests and connections time out as a safeguard, but Blazor Server apps don't provide the same safeguards.</span></span> <span data-ttu-id="476a4-138">Blazor Server 應用程式必須包含適當的檢查和限制，才能執行可能需要大量 CPU 的工作。</span><span class="sxs-lookup"><span data-stu-id="476a4-138">Blazor Server apps must include appropriate checks and limits before performing potentially CPU-intensive work.</span></span>

### <a name="memory"></a><span data-ttu-id="476a4-139">記憶體</span><span class="sxs-lookup"><span data-stu-id="476a4-139">Memory</span></span>

<span data-ttu-id="476a4-140">當一或多個用戶端強制服務器耗用海量儲存體時，就可能會發生記憶體耗盡。</span><span class="sxs-lookup"><span data-stu-id="476a4-140">Memory exhaustion can occur when one or more clients force the server to consume a large amount of memory.</span></span>

<span data-ttu-id="476a4-141">例如，請考慮 Blazor 具有接受並顯示專案清單之元件的-伺服器端應用程式。</span><span class="sxs-lookup"><span data-stu-id="476a4-141">For example, consider a Blazor-server side app with a component that accepts and displays a list of items.</span></span> <span data-ttu-id="476a4-142">如果 Blazor 應用程式未對允許的專案數目或轉譯回用戶端的專案數目有限制，記憶體密集的處理和轉譯可能會將伺服器的記憶體占到伺服器效能的時間點。</span><span class="sxs-lookup"><span data-stu-id="476a4-142">If the Blazor app doesn't place limits on the number of items allowed or the number of items rendered back to the client, the memory-intensive processing and rendering may dominate the server's memory to the point where performance of the server suffers.</span></span> <span data-ttu-id="476a4-143">伺服器可能會損毀，或速度似乎損毀的點。</span><span class="sxs-lookup"><span data-stu-id="476a4-143">The server may crash or slow to the point that it appears to have crashed.</span></span>

<span data-ttu-id="476a4-144">請考慮下列案例，以維護和顯示與伺服器上可能發生記憶體耗盡案例相關的專案清單：</span><span class="sxs-lookup"><span data-stu-id="476a4-144">Consider the following scenario for maintaining and displaying a list of items that pertain to a potential memory exhaustion scenario on the server:</span></span>

* <span data-ttu-id="476a4-145">屬性或欄位中的專案會 `List<MyItem>` 使用伺服器的記憶體。</span><span class="sxs-lookup"><span data-stu-id="476a4-145">The items in a `List<MyItem>` property or field use the server's memory.</span></span> <span data-ttu-id="476a4-146">如果應用程式允許專案清單成長到未系結，則會有伺服器記憶體不足的風險。</span><span class="sxs-lookup"><span data-stu-id="476a4-146">If the app allows the list of items to grow unbounded, there's a risk of the server running out of memory.</span></span> <span data-ttu-id="476a4-147">記憶體不足會導致目前的會話結束 (損毀) ，而且該伺服器實例中的所有並行會話都會收到記憶體不足的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="476a4-147">Running out of memory causes the current session to end (crash) and all of the concurrent sessions in that server instance receive an out-of-memory exception.</span></span> <span data-ttu-id="476a4-148">若要避免發生此情況，應用程式必須使用在並行使用者上強加專案限制的資料結構。</span><span class="sxs-lookup"><span data-stu-id="476a4-148">To prevent this scenario from occurring, the app must use a data structure that imposes an item limit on concurrent users.</span></span>
* <span data-ttu-id="476a4-149">如果沒有使用分頁配置來進行轉譯，伺服器會針對 UI 中看不到的物件使用額外的記憶體。</span><span class="sxs-lookup"><span data-stu-id="476a4-149">If a paging scheme isn't used for rendering, the server uses additional memory for objects that aren't visible in the UI.</span></span> <span data-ttu-id="476a4-150">如果沒有專案數的限制，記憶體需求可能會耗盡可用的伺服器記憶體。</span><span class="sxs-lookup"><span data-stu-id="476a4-150">Without a limit on the number of items, memory demands may exhaust the available server memory.</span></span> <span data-ttu-id="476a4-151">若要避免這種情況，請使用下列其中一種方法：</span><span class="sxs-lookup"><span data-stu-id="476a4-151">To prevent this scenario, use one of the following approaches:</span></span>
  * <span data-ttu-id="476a4-152">轉譯時使用分頁清單。</span><span class="sxs-lookup"><span data-stu-id="476a4-152">Use paginated lists when rendering.</span></span>
  * <span data-ttu-id="476a4-153">只顯示前100至1000個專案，並且要求使用者輸入搜尋準則，以在顯示的專案之外尋找專案。</span><span class="sxs-lookup"><span data-stu-id="476a4-153">Only display the first 100 to 1,000 items and require the user to enter search criteria to find items beyond the items displayed.</span></span>
  * <span data-ttu-id="476a4-154">如需更先進的轉譯案例，請執行支援 *虛擬化* 的清單或格線。</span><span class="sxs-lookup"><span data-stu-id="476a4-154">For a more advanced rendering scenario, implement lists or grids that support *virtualization*.</span></span> <span data-ttu-id="476a4-155">使用虛擬化時，清單只會呈現使用者目前可見的專案子集。</span><span class="sxs-lookup"><span data-stu-id="476a4-155">Using virtualization, lists only render a subset of items currently visible to the user.</span></span> <span data-ttu-id="476a4-156">當使用者與 UI 中的捲軸互動時，元件只會轉譯顯示所需的專案。</span><span class="sxs-lookup"><span data-stu-id="476a4-156">When the user interacts with the scrollbar in the UI, the component renders only those items required for display.</span></span> <span data-ttu-id="476a4-157">目前不需要顯示的專案可以保留在次要儲存體中，這是理想的方法。</span><span class="sxs-lookup"><span data-stu-id="476a4-157">The items that aren't currently required for display can be held in secondary storage, which is the ideal approach.</span></span> <span data-ttu-id="476a4-158">Undisplayed 專案也可以保留在記憶體中，這比較不理想。</span><span class="sxs-lookup"><span data-stu-id="476a4-158">Undisplayed items can also be held in memory, which is less ideal.</span></span>

<span data-ttu-id="476a4-159">Blazor Server 應用程式會提供類似的程式設計模型給具狀態應用程式的其他 UI 架構，例如 WPF、Windows Forms 或 Blazor WebAssembly 。</span><span class="sxs-lookup"><span data-stu-id="476a4-159">Blazor Server apps offer a similar programming model to other UI frameworks for stateful apps, such as WPF, Windows Forms, or Blazor WebAssembly.</span></span> <span data-ttu-id="476a4-160">主要的差異在於，應用程式所耗用的記憶體屬於用戶端，而且只會影響該個別用戶端的幾個 UI 架構。</span><span class="sxs-lookup"><span data-stu-id="476a4-160">The main difference is that in several of the UI frameworks the memory consumed by the app belongs to the client and only affects that individual client.</span></span> <span data-ttu-id="476a4-161">例如， Blazor WebAssembly 應用程式會完全在用戶端上執行，而且只會使用用戶端記憶體資源。</span><span class="sxs-lookup"><span data-stu-id="476a4-161">For example, a Blazor WebAssembly app runs entirely on the client and only uses client memory resources.</span></span> <span data-ttu-id="476a4-162">在此 Blazor Server 案例中，應用程式所耗用的記憶體屬於伺服器，並在伺服器實例上的用戶端間共用。</span><span class="sxs-lookup"><span data-stu-id="476a4-162">In the Blazor Server scenario, the memory consumed by the app belongs to the server and is shared among clients on the server instance.</span></span>

<span data-ttu-id="476a4-163">伺服器端記憶體需求是所有應用程式的考慮 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="476a4-163">Server-side memory demands are a consideration for all Blazor Server apps.</span></span> <span data-ttu-id="476a4-164">不過，大部分的 web 應用程式都是無狀態的，而且在處理要求時使用的記憶體會在傳迴響應時釋放。</span><span class="sxs-lookup"><span data-stu-id="476a4-164">However, most web apps are stateless, and the memory used while processing a request is released when the response is returned.</span></span> <span data-ttu-id="476a4-165">作為一般建議，請不要讓用戶端配置未系結的記憶體數量，就像在任何其他可保存用戶端連接的伺服器端應用程式中一樣。</span><span class="sxs-lookup"><span data-stu-id="476a4-165">As a general recommendation, don't permit clients to allocate an unbound amount of memory as in any other server-side app that persists client connections.</span></span> <span data-ttu-id="476a4-166">應用程式所耗用的記憶體會 Blazor Server 保存超過單一要求的時間。</span><span class="sxs-lookup"><span data-stu-id="476a4-166">The memory consumed by a Blazor Server app persists for a longer time than a single request.</span></span>

> [!NOTE]
> <span data-ttu-id="476a4-167">在開發期間，您可以流量分析工具或捕獲的追蹤，來評估用戶端的記憶體需求。</span><span class="sxs-lookup"><span data-stu-id="476a4-167">During development, a profiler can be used or a trace captured to assess memory demands of clients.</span></span> <span data-ttu-id="476a4-168">Profiler 或追蹤不會捕捉配置給特定用戶端的記憶體。</span><span class="sxs-lookup"><span data-stu-id="476a4-168">A profiler or trace won't capture the memory allocated to a specific client.</span></span> <span data-ttu-id="476a4-169">若要在開發期間捕捉特定用戶端的記憶體使用量，請捕獲傾印，並檢查所有根于使用者線路之物件的記憶體需求。</span><span class="sxs-lookup"><span data-stu-id="476a4-169">To capture the memory use of a specific client during development, capture a dump and examine the memory demand of all the objects rooted at a user's circuit.</span></span>

### <a name="client-connections"></a><span data-ttu-id="476a4-170">用戶端連接</span><span class="sxs-lookup"><span data-stu-id="476a4-170">Client connections</span></span>

<span data-ttu-id="476a4-171">當一或多個用戶端開啟太多與伺服器的並行連線時，就會發生連接耗盡，而導致其他用戶端無法建立新的連線。</span><span class="sxs-lookup"><span data-stu-id="476a4-171">Connection exhaustion can occur when one or more clients open too many concurrent connections to the server, preventing other clients from establishing new connections.</span></span>

<span data-ttu-id="476a4-172">Blazor 用戶端會針對每個會話建立單一連線，只要開啟瀏覽器視窗，就會保持開啟連接。</span><span class="sxs-lookup"><span data-stu-id="476a4-172">Blazor clients establish a single connection per session and keep the connection open for as long as the browser window is open.</span></span> <span data-ttu-id="476a4-173">維護所有連線之伺服器上的需求並非 Blazor 應用程式特有的。</span><span class="sxs-lookup"><span data-stu-id="476a4-173">The demands on the server of maintaining all of the connections isn't specific to Blazor apps.</span></span> <span data-ttu-id="476a4-174">由於連線的持續性本質和應用程式的具狀態本質 Blazor Server ，連接耗盡對於應用程式的可用性有較大的風險。</span><span class="sxs-lookup"><span data-stu-id="476a4-174">Given the persistent nature of the connections and the stateful nature of Blazor Server apps, connection exhaustion is a greater risk to availability of the app.</span></span>

<span data-ttu-id="476a4-175">依預設，應用程式的每個使用者連線數目沒有限制 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="476a4-175">By default, there's no limit on the number of connections per user for a Blazor Server app.</span></span> <span data-ttu-id="476a4-176">如果應用程式需要連線限制，請採取下列一或多個方法：</span><span class="sxs-lookup"><span data-stu-id="476a4-176">If the app requires a connection limit, take one or more of the following approaches:</span></span>

* <span data-ttu-id="476a4-177">需要驗證，這會自然地限制未授權使用者連線到應用程式的能力。</span><span class="sxs-lookup"><span data-stu-id="476a4-177">Require authentication, which naturally limits the ability of unauthorized users to connect to the app.</span></span> <span data-ttu-id="476a4-178">為了讓此案例生效，使用者必須防止使用者布建新的使用者。</span><span class="sxs-lookup"><span data-stu-id="476a4-178">For this scenario to be effective, users must be prevented from provisioning new users at will.</span></span>
* <span data-ttu-id="476a4-179">限制每個使用者的連接數目。</span><span class="sxs-lookup"><span data-stu-id="476a4-179">Limit the number of connections per user.</span></span> <span data-ttu-id="476a4-180">您可以透過下列方法來限制連接。</span><span class="sxs-lookup"><span data-stu-id="476a4-180">Limiting connections can be accomplished via the following approaches.</span></span> <span data-ttu-id="476a4-181">請小心讓合法的使用者存取應用程式 (例如，根據用戶端的 IP 位址) 建立連線限制。</span><span class="sxs-lookup"><span data-stu-id="476a4-181">Exercise care to allow legitimate users to access the app (for example, when a connection limit is established based on the client's IP address).</span></span>
  * <span data-ttu-id="476a4-182">在應用層級：</span><span class="sxs-lookup"><span data-stu-id="476a4-182">At the app level:</span></span>
    * <span data-ttu-id="476a4-183">端點路由擴充性。</span><span class="sxs-lookup"><span data-stu-id="476a4-183">Endpoint routing extensibility.</span></span>
    * <span data-ttu-id="476a4-184">需要驗證才能連線到應用程式，並追蹤每位使用者的作用中會話。</span><span class="sxs-lookup"><span data-stu-id="476a4-184">Require authentication to connect to the app and keep track of the active sessions per user.</span></span>
    * <span data-ttu-id="476a4-185">在達到限制時拒絕新的會話。</span><span class="sxs-lookup"><span data-stu-id="476a4-185">Reject new sessions upon reaching a limit.</span></span>
    * <span data-ttu-id="476a4-186">Proxy WebSocket 透過使用 proxy 連線到應用程式，例如將分離信號從用戶端連線到應用程式的 [Azure SignalR 服務](/azure/azure-signalr/signalr-overview) 。</span><span class="sxs-lookup"><span data-stu-id="476a4-186">Proxy WebSocket connections to an app through the use of a proxy, such as the [Azure SignalR Service](/azure/azure-signalr/signalr-overview) that multiplexes connections from clients to an app.</span></span> <span data-ttu-id="476a4-187">這會提供比單一用戶端可以建立的連接容量更大的應用程式，以防止用戶端耗盡與伺服器的連線。</span><span class="sxs-lookup"><span data-stu-id="476a4-187">This provides an app with greater connection capacity than a single client can establish, preventing a client from exhausting the connections to the server.</span></span>
  * <span data-ttu-id="476a4-188">在伺服器層級：在應用程式前面使用 proxy/閘道。</span><span class="sxs-lookup"><span data-stu-id="476a4-188">At the server level: Use a proxy/gateway in front of the app.</span></span> <span data-ttu-id="476a4-189">例如， [Azure Front Door](/azure/frontdoor/front-door-overview) 可讓您定義、管理及監視應用程式之 web 流量的全域路由，並在 Blazor Server 應用程式設定為使用長時間輪詢時運作。</span><span class="sxs-lookup"><span data-stu-id="476a4-189">For example, [Azure Front Door](/azure/frontdoor/front-door-overview) enables you to define, manage, and monitor the global routing of web traffic to an app and works when Blazor Server apps are configured to use Long Polling.</span></span>
  
    > [!NOTE]
    > <span data-ttu-id="476a4-190">雖然應用程式支援長時間輪詢 Blazor Server ，但 [websocket 是建議的傳輸通訊協定](xref:blazor/host-and-deploy/server#azure-signalr-service)。</span><span class="sxs-lookup"><span data-stu-id="476a4-190">Although Long Polling is supported for Blazor Server apps, [WebSockets is the recommended transport protocol](xref:blazor/host-and-deploy/server#azure-signalr-service).</span></span> <span data-ttu-id="476a4-191">[Azure Front Door](/azure/frontdoor/front-door-overview) 目前不支援 websocket，但在未來的服務版本中，會針對 websocket 的支援進行考慮。</span><span class="sxs-lookup"><span data-stu-id="476a4-191">[Azure Front Door](/azure/frontdoor/front-door-overview) doesn't support WebSockets at this time, but support for WebSockets is under consideration for a future release of the service.</span></span>

## <a name="denial-of-service-dos-attacks"></a><span data-ttu-id="476a4-192">拒絕服務 (DoS) 的攻擊</span><span class="sxs-lookup"><span data-stu-id="476a4-192">Denial of service (DoS) attacks</span></span>

<span data-ttu-id="476a4-193">阻絕服務 (DoS) 攻擊涉及用戶端，導致伺服器耗盡其一或多個資源，使應用程式無法使用。</span><span class="sxs-lookup"><span data-stu-id="476a4-193">Denial of service (DoS) attacks involve a client causing the server to exhaust one or more of its resources making the app unavailable.</span></span> <span data-ttu-id="476a4-194">Blazor Server 應用程式包含預設限制，並且依賴其他 ASP.NET Core 和 SignalR 限制，以防止在上設定的 DoS 攻擊 <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> ：</span><span class="sxs-lookup"><span data-stu-id="476a4-194">Blazor Server apps include default limits and rely on other ASP.NET Core and SignalR limits to protect against DoS attacks that are set on <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions>:</span></span>

* <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitMaxRetained?displayProperty=nameWithType>
* <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitRetentionPeriod?displayProperty=nameWithType>
* <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout?displayProperty=nameWithType>
* <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches?displayProperty=nameWithType>
* <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions.MaximumReceiveMessageSize?displayProperty=nameWithType>

<span data-ttu-id="476a4-195">如需詳細資訊和設定程式碼撰寫範例，請參閱下列文章：</span><span class="sxs-lookup"><span data-stu-id="476a4-195">For more information and configuration coding examples, see the following articles:</span></span>

* <xref:blazor/fundamentals/signalr>
* <xref:signalr/configuration>

## <a name="interactions-with-the-browser-client"></a><span data-ttu-id="476a4-196">與瀏覽器 (用戶端) 的互動</span><span class="sxs-lookup"><span data-stu-id="476a4-196">Interactions with the browser (client)</span></span>

<span data-ttu-id="476a4-197">用戶端會透過 JS interop 事件分派和轉譯完成來與伺服器互動。</span><span class="sxs-lookup"><span data-stu-id="476a4-197">A client interacts with the server through JS interop event dispatching and render completion.</span></span> <span data-ttu-id="476a4-198">JS interop 通訊會在 JavaScript 和 .NET 之間進行這兩種方式：</span><span class="sxs-lookup"><span data-stu-id="476a4-198">JS interop communication goes both ways between JavaScript and .NET:</span></span>

* <span data-ttu-id="476a4-199">瀏覽器事件是以非同步方式從用戶端分派至伺服器。</span><span class="sxs-lookup"><span data-stu-id="476a4-199">Browser events are dispatched from the client to the server in an asynchronous fashion.</span></span>
* <span data-ttu-id="476a4-200">伺服器會視需要以非同步方式轉譯資料流程 UI。</span><span class="sxs-lookup"><span data-stu-id="476a4-200">The server responds asynchronously rerendering the UI as necessary.</span></span>

### <a name="javascript-functions-invoked-from-net"></a><span data-ttu-id="476a4-201">從 .NET 叫用的 JavaScript 函式</span><span class="sxs-lookup"><span data-stu-id="476a4-201">JavaScript functions invoked from .NET</span></span>

<span data-ttu-id="476a4-202">針對從 .NET 方法到 JavaScript 的呼叫：</span><span class="sxs-lookup"><span data-stu-id="476a4-202">For calls from .NET methods to JavaScript:</span></span>

* <span data-ttu-id="476a4-203">所有調用都有可設定的超時時間，在這之後會將 <xref:System.OperationCanceledException> 傳回給呼叫端。</span><span class="sxs-lookup"><span data-stu-id="476a4-203">All invocations have a configurable timeout after which they fail, returning a <xref:System.OperationCanceledException> to the caller.</span></span>
  * <span data-ttu-id="476a4-204">呼叫 (的預設超時時間 <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout?displayProperty=nameWithType>) 一分鐘。</span><span class="sxs-lookup"><span data-stu-id="476a4-204">There's a default timeout for the calls (<xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout?displayProperty=nameWithType>) of one minute.</span></span> <span data-ttu-id="476a4-205">若要設定此限制，請參閱 <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls> 。</span><span class="sxs-lookup"><span data-stu-id="476a4-205">To configure this limit, see <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls>.</span></span>
  * <span data-ttu-id="476a4-206">您可以提供解除標記來控制每個呼叫的取消。</span><span class="sxs-lookup"><span data-stu-id="476a4-206">A cancellation token can be provided to control the cancellation on a per-call basis.</span></span> <span data-ttu-id="476a4-207">如果有提供取消權杖，就會依賴預設的呼叫超時時間，在可能的情況下對用戶端的任何呼叫進行時間限制。</span><span class="sxs-lookup"><span data-stu-id="476a4-207">Rely on the default call timeout where possible and time-bound any call to the client if a cancellation token is provided.</span></span>
* <span data-ttu-id="476a4-208">無法信任 JavaScript 呼叫的結果。</span><span class="sxs-lookup"><span data-stu-id="476a4-208">The result of a JavaScript call can't be trusted.</span></span> <span data-ttu-id="476a4-209">Blazor在瀏覽器中執行的應用程式用戶端會搜尋要叫用的 JavaScript 函式。</span><span class="sxs-lookup"><span data-stu-id="476a4-209">The Blazor app client running in the browser searches for the JavaScript function to invoke.</span></span> <span data-ttu-id="476a4-210">系統會叫用此函式，並產生結果或錯誤。</span><span class="sxs-lookup"><span data-stu-id="476a4-210">The function is invoked, and either the result or an error is produced.</span></span> <span data-ttu-id="476a4-211">惡意用戶端可以嘗試：</span><span class="sxs-lookup"><span data-stu-id="476a4-211">A malicious client can attempt to:</span></span>
  * <span data-ttu-id="476a4-212">從 JavaScript 函式傳回錯誤，導致應用程式發生問題。</span><span class="sxs-lookup"><span data-stu-id="476a4-212">Cause an issue in the app by returning an error from the JavaScript function.</span></span>
  * <span data-ttu-id="476a4-213">藉由從 JavaScript 函式傳回非預期的結果，在伺服器上引發非預期的行為。</span><span class="sxs-lookup"><span data-stu-id="476a4-213">Induce an unintended behavior on the server by returning an unexpected result from the JavaScript function.</span></span>

<span data-ttu-id="476a4-214">請採取下列預防措施來防範上述案例：</span><span class="sxs-lookup"><span data-stu-id="476a4-214">Take the following precautions to guard against the preceding scenarios:</span></span>

* <span data-ttu-id="476a4-215">將 JS interop 呼叫包裝在 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 語句中，以考慮在調用期間可能發生的錯誤。</span><span class="sxs-lookup"><span data-stu-id="476a4-215">Wrap JS interop calls within [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statements to account for errors that might occur during the invocations.</span></span> <span data-ttu-id="476a4-216">如需詳細資訊，請參閱<xref:blazor/fundamentals/handle-errors#javascript-interop>。</span><span class="sxs-lookup"><span data-stu-id="476a4-216">For more information, see <xref:blazor/fundamentals/handle-errors#javascript-interop>.</span></span>
* <span data-ttu-id="476a4-217">在採取任何動作之前，請先驗證從 JS interop 調用傳回的資料（包括錯誤訊息）。</span><span class="sxs-lookup"><span data-stu-id="476a4-217">Validate data returned from JS interop invocations, including error messages, before taking any action.</span></span>

### <a name="net-methods-invoked-from-the-browser"></a><span data-ttu-id="476a4-218">從瀏覽器叫用的 .NET 方法</span><span class="sxs-lookup"><span data-stu-id="476a4-218">.NET methods invoked from the browser</span></span>

<span data-ttu-id="476a4-219">請勿信任從 JavaScript 到 .NET 方法的呼叫。</span><span class="sxs-lookup"><span data-stu-id="476a4-219">Don't trust calls from JavaScript to .NET methods.</span></span> <span data-ttu-id="476a4-220">當 .NET 方法公開至 JavaScript 時，請考慮如何叫用 .NET 方法：</span><span class="sxs-lookup"><span data-stu-id="476a4-220">When a .NET method is exposed to JavaScript, consider how the .NET method is invoked:</span></span>

* <span data-ttu-id="476a4-221">將公開給 JavaScript 的任何 .NET 方法視為應用程式的公用端點。</span><span class="sxs-lookup"><span data-stu-id="476a4-221">Treat any .NET method exposed to JavaScript as you would a public endpoint to the app.</span></span>
  * <span data-ttu-id="476a4-222">驗證輸入。</span><span class="sxs-lookup"><span data-stu-id="476a4-222">Validate input.</span></span>
    * <span data-ttu-id="476a4-223">請確認值在預期的範圍內。</span><span class="sxs-lookup"><span data-stu-id="476a4-223">Ensure that values are within expected ranges.</span></span>
    * <span data-ttu-id="476a4-224">確定使用者有權執行所要求的動作。</span><span class="sxs-lookup"><span data-stu-id="476a4-224">Ensure that the user has permission to perform the action requested.</span></span>
  * <span data-ttu-id="476a4-225">請勿在 .NET 方法調用中配置過多的資源數量。</span><span class="sxs-lookup"><span data-stu-id="476a4-225">Don't allocate an excessive quantity of resources as part of the .NET method invocation.</span></span> <span data-ttu-id="476a4-226">例如，執行對 CPU 和記憶體使用量的檢查和位置限制。</span><span class="sxs-lookup"><span data-stu-id="476a4-226">For example, perform checks and place limits on CPU and memory use.</span></span>
  * <span data-ttu-id="476a4-227">請考慮靜態和實例方法可以公開給 JavaScript 用戶端。</span><span class="sxs-lookup"><span data-stu-id="476a4-227">Take into account that static and instance methods can be exposed to JavaScript clients.</span></span> <span data-ttu-id="476a4-228">除非設計呼叫具有適當條件約束的共用狀態，否則請避免在會話之間共用狀態。</span><span class="sxs-lookup"><span data-stu-id="476a4-228">Avoid sharing state across sessions unless the design calls for sharing state with appropriate constraints.</span></span>
    * <span data-ttu-id="476a4-229">針對透過相依性插入 (DI) 所公開的物件所公開的實例方法 `DotNetReference` ，物件應該註冊為已設定範圍的物件。</span><span class="sxs-lookup"><span data-stu-id="476a4-229">For instance methods exposed through `DotNetReference` objects that are originally created through dependency injection (DI), the objects should be registered as scoped objects.</span></span> <span data-ttu-id="476a4-230">這適用于應用程式使用的任何 DI 服務 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="476a4-230">This applies to any DI service that the Blazor Server app uses.</span></span>
    * <span data-ttu-id="476a4-231">針對靜態方法，除非應用程式在伺服器實例上的所有使用者之間明確共用狀態，否則請避免建立無法範圍設定為用戶端的狀態。</span><span class="sxs-lookup"><span data-stu-id="476a4-231">For static methods, avoid establishing state that can't be scoped to the client unless the app is explicitly sharing state by-design across all users on a server instance.</span></span>
  * <span data-ttu-id="476a4-232">避免將使用者提供的資料在參數中傳遞給 JavaScript 呼叫。</span><span class="sxs-lookup"><span data-stu-id="476a4-232">Avoid passing user-supplied data in parameters to JavaScript calls.</span></span> <span data-ttu-id="476a4-233">如果在參數中傳遞資料是絕對必要的，請確定 JavaScript 程式碼會處理資料的傳遞，而不會引進 [跨網站腳本 (XSS) ](#cross-site-scripting-xss) 弱點。</span><span class="sxs-lookup"><span data-stu-id="476a4-233">If passing data in parameters is absolutely required, ensure that the JavaScript code handles passing the data without introducing [Cross-site scripting (XSS)](#cross-site-scripting-xss) vulnerabilities.</span></span> <span data-ttu-id="476a4-234">例如，請不要藉由設定專案的屬性，將使用者提供的資料寫入檔物件模型 (DOM) `innerHTML` 。</span><span class="sxs-lookup"><span data-stu-id="476a4-234">For example, don't write user-supplied data to the Document Object Model (DOM) by setting the `innerHTML` property of an element.</span></span> <span data-ttu-id="476a4-235">請考慮使用 [ (CSP) 的內容安全性原則 ](https://developer.mozilla.org/docs/Web/HTTP/CSP) 來停用 `eval` 和其他不安全的 JavaScript 基本專案。</span><span class="sxs-lookup"><span data-stu-id="476a4-235">Consider using [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) to disable `eval` and other unsafe JavaScript primitives.</span></span>
* <span data-ttu-id="476a4-236">避免在架構的分派實行之上，執行 .NET 調用的自訂分派。</span><span class="sxs-lookup"><span data-stu-id="476a4-236">Avoid implementing custom dispatching of .NET invocations on top of the framework's dispatching implementation.</span></span> <span data-ttu-id="476a4-237">將 .NET 方法公開給瀏覽器是一種 advanced 案例，不建議用於一般 Blazor 開發。</span><span class="sxs-lookup"><span data-stu-id="476a4-237">Exposing .NET methods to the browser is an advanced scenario, not recommended for general Blazor development.</span></span>

### <a name="events"></a><span data-ttu-id="476a4-238">事件</span><span class="sxs-lookup"><span data-stu-id="476a4-238">Events</span></span>

<span data-ttu-id="476a4-239">事件會提供應用程式的進入點 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="476a4-239">Events provide an entry point to a Blazor Server app.</span></span> <span data-ttu-id="476a4-240">在 web 應用程式中保護端點的相同規則適用于應用程式中的事件處理 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="476a4-240">The same rules for safeguarding endpoints in web apps apply to event handling in Blazor Server apps.</span></span> <span data-ttu-id="476a4-241">惡意用戶端可以傳送任何想要傳送的資料做為事件的承載。</span><span class="sxs-lookup"><span data-stu-id="476a4-241">A malicious client can send any data it wishes to send as the payload for an event.</span></span>

<span data-ttu-id="476a4-242">例如：</span><span class="sxs-lookup"><span data-stu-id="476a4-242">For example:</span></span>

* <span data-ttu-id="476a4-243">的變更事件 `<select>` 可能會傳送不在應用程式呈現給用戶端之選項中的值。</span><span class="sxs-lookup"><span data-stu-id="476a4-243">A change event for a `<select>` could send a value that isn't within the options that the app presented to the client.</span></span>
* <span data-ttu-id="476a4-244">可 `<input>` 將任何文字資料傳送至伺服器，略過用戶端驗證。</span><span class="sxs-lookup"><span data-stu-id="476a4-244">An `<input>` could send any text data to the server, bypassing client-side validation.</span></span>

<span data-ttu-id="476a4-245">應用程式必須驗證應用程式處理之任何事件的資料。</span><span class="sxs-lookup"><span data-stu-id="476a4-245">The app must validate the data for any event that the app handles.</span></span> <span data-ttu-id="476a4-246">Blazor架構[表單元件](xref:blazor/forms-validation)會執行基本驗證。</span><span class="sxs-lookup"><span data-stu-id="476a4-246">The Blazor framework [forms components](xref:blazor/forms-validation) perform basic validations.</span></span> <span data-ttu-id="476a4-247">如果應用程式使用自訂表單元件，則必須撰寫自訂程式碼來適當地驗證事件資料。</span><span class="sxs-lookup"><span data-stu-id="476a4-247">If the app uses custom forms components, custom code must be written to validate event data as appropriate.</span></span>

<span data-ttu-id="476a4-248">Blazor Server 事件是非同步，因此，您可以在應用程式有時間產生新的轉譯之前，將多個事件分派至伺服器。</span><span class="sxs-lookup"><span data-stu-id="476a4-248">Blazor Server events are asynchronous, so multiple events can be dispatched to the server before the app has time to react by producing a new render.</span></span> <span data-ttu-id="476a4-249">這有一些要考慮的安全性含意。</span><span class="sxs-lookup"><span data-stu-id="476a4-249">This has some security implications to consider.</span></span> <span data-ttu-id="476a4-250">限制應用程式中的用戶端動作必須在事件處理常式內執行，而不需依賴目前呈現的檢視狀態。</span><span class="sxs-lookup"><span data-stu-id="476a4-250">Limiting client actions in the app must be performed inside event handlers and not depend on the current rendered view state.</span></span>

<span data-ttu-id="476a4-251">請考慮應該允許使用者將計數器最多遞增三次的 counter 元件。</span><span class="sxs-lookup"><span data-stu-id="476a4-251">Consider a counter component that should allow a user to increment a counter a maximum of three times.</span></span> <span data-ttu-id="476a4-252">根據下列值，有條件地遞增計數器的按鈕 `count` ：</span><span class="sxs-lookup"><span data-stu-id="476a4-252">The button to increment the counter is conditionally based on the value of `count`:</span></span>

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        count++;
    }
}
```

<span data-ttu-id="476a4-253">用戶端可以分派一或多個增量事件，架構才會產生這個元件的新轉譯。</span><span class="sxs-lookup"><span data-stu-id="476a4-253">A client can dispatch one or more increment events before the framework produces a new render of this component.</span></span> <span data-ttu-id="476a4-254">結果是， `count` 使用者可以增加 *三次* ，因為 UI 很快就不會移除按鈕。</span><span class="sxs-lookup"><span data-stu-id="476a4-254">The result is that the `count` can be incremented *over three times* by the user because the button isn't removed by the UI quickly enough.</span></span> <span data-ttu-id="476a4-255">下列範例顯示可達成三個增量限制的正確方式 `count` ：</span><span class="sxs-lookup"><span data-stu-id="476a4-255">The correct way to achieve the limit of three `count` increments is shown in the following example:</span></span>

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        if (count < 3)
        {
            count++;
        }
    }
}
```

<span data-ttu-id="476a4-256">藉由在 `if (count < 3) { ... }` 處理常式內新增檢查，將 `count` 會根據目前的應用程式狀態來遞增決定。</span><span class="sxs-lookup"><span data-stu-id="476a4-256">By adding the `if (count < 3) { ... }` check inside the handler, the decision to increment `count` is based on the current app state.</span></span> <span data-ttu-id="476a4-257">這項決策並不是以上述範例中的 UI 狀態為基礎，這可能會暫時過時。</span><span class="sxs-lookup"><span data-stu-id="476a4-257">The decision isn't based on the state of the UI as it was in the previous example, which might be temporarily stale.</span></span>

### <a name="guard-against-multiple-dispatches"></a><span data-ttu-id="476a4-258">防止多個分派</span><span class="sxs-lookup"><span data-stu-id="476a4-258">Guard against multiple dispatches</span></span>

<span data-ttu-id="476a4-259">如果事件回呼以非同步方式叫用長時間執行的作業，例如從外部服務或資料庫提取資料，請考慮使用防護。</span><span class="sxs-lookup"><span data-stu-id="476a4-259">If an event callback invokes a long running operation asynchronously, such as fetching data from an external service or database, consider using a guard.</span></span> <span data-ttu-id="476a4-260">當作業正在進行視覺效果的意見反應時，此防護可以防止使用者將多個作業排入佇列。</span><span class="sxs-lookup"><span data-stu-id="476a4-260">The guard can prevent the user from queueing up multiple operations while the operation is in progress with visual feedback.</span></span> <span data-ttu-id="476a4-261">`isLoading` `true` 從伺服器取得資料時，下列元件程式碼會將設定為 `GetForecastAsync` 。</span><span class="sxs-lookup"><span data-stu-id="476a4-261">The following component code sets `isLoading` to `true` while `GetForecastAsync` obtains data from the server.</span></span> <span data-ttu-id="476a4-262">當 `isLoading` 為時 `true` ，UI 中的按鈕會停用：</span><span class="sxs-lookup"><span data-stu-id="476a4-262">While `isLoading` is `true`, the button is disabled in the UI:</span></span>

```razor
@page "/fetchdata"
@using BlazorServerSample.Data
@inject WeatherForecastService ForecastService

<button disabled="@isLoading" @onclick="UpdateForecasts">Update</button>

@code {
    private bool isLoading;
    private WeatherForecast[] forecasts;

    private async Task UpdateForecasts()
    {
        if (!isLoading)
        {
            isLoading = true;
            forecasts = await ForecastService.GetForecastAsync(DateTime.Now);
            isLoading = false;
        }
    }
}
```

<span data-ttu-id="476a4-263">如果使用模式以非同步方式執行背景作業，則在上述範例中所示範的防護模式會正常運作 `async` - `await` 。</span><span class="sxs-lookup"><span data-stu-id="476a4-263">The guard pattern demonstrated in the preceding example works if the background operation is executed asynchronously with the `async`-`await` pattern.</span></span>

### <a name="cancel-early-and-avoid-use-after-dispose"></a><span data-ttu-id="476a4-264">提早取消並避免使用-dispose</span><span class="sxs-lookup"><span data-stu-id="476a4-264">Cancel early and avoid use-after-dispose</span></span>

<span data-ttu-id="476a4-265">除了使用「 [針對多個分派進行防護](#guard-against-multiple-dispatches) 」一節中所述的防護之外，請考慮使用在 <xref:System.Threading.CancellationToken> 元件處置時取消長時間執行的作業。</span><span class="sxs-lookup"><span data-stu-id="476a4-265">In addition to using a guard as described in the [Guard against multiple dispatches](#guard-against-multiple-dispatches) section, consider using a <xref:System.Threading.CancellationToken> to cancel long-running operations when the component is disposed.</span></span> <span data-ttu-id="476a4-266">這種方法有額外的好處，就是避免在元件中 *使用-after 處置* ：</span><span class="sxs-lookup"><span data-stu-id="476a4-266">This approach has the added benefit of avoiding *use-after-dispose* in components:</span></span>

```razor
@implements IDisposable

...

@code {
    private readonly CancellationTokenSource TokenSource = 
        new CancellationTokenSource();

    private async Task UpdateForecasts()
    {
        ...

        forecasts = await ForecastService.GetForecastAsync(DateTime.Now, 
            TokenSource.Token);

        if (TokenSource.Token.IsCancellationRequested)
        {
           return;
        }

        ...
    }

    public void Dispose()
    {
        TokenSource.Cancel();
    }
}
```

### <a name="avoid-events-that-produce-large-amounts-of-data"></a><span data-ttu-id="476a4-267">避免產生大量資料的事件</span><span class="sxs-lookup"><span data-stu-id="476a4-267">Avoid events that produce large amounts of data</span></span>

<span data-ttu-id="476a4-268">某些 DOM 事件（例如 `oninput` 或 `onscroll` ）可能會產生大量的資料。</span><span class="sxs-lookup"><span data-stu-id="476a4-268">Some DOM events, such as `oninput` or `onscroll`, can produce a large amount of data.</span></span> <span data-ttu-id="476a4-269">避免在伺服器應用程式中使用這些事件 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="476a4-269">Avoid using these events in Blazor server apps.</span></span>

## <a name="additional-security-guidance"></a><span data-ttu-id="476a4-270">其他安全性指引</span><span class="sxs-lookup"><span data-stu-id="476a4-270">Additional security guidance</span></span>

<span data-ttu-id="476a4-271">適用于保護 ASP.NET Core apps 的指導方針適用于應用 Blazor Server 程式，並在下列各節中討論：</span><span class="sxs-lookup"><span data-stu-id="476a4-271">The guidance for securing ASP.NET Core apps apply to Blazor Server apps and are covered in the following sections:</span></span>

* [<span data-ttu-id="476a4-272">記錄和敏感性資料</span><span class="sxs-lookup"><span data-stu-id="476a4-272">Logging and sensitive data</span></span>](#logging-and-sensitive-data)
* [<span data-ttu-id="476a4-273">使用 HTTPS 保護傳輸中的資訊</span><span class="sxs-lookup"><span data-stu-id="476a4-273">Protect information in transit with HTTPS</span></span>](#protect-information-in-transit-with-https)
* [<span data-ttu-id="476a4-274">跨網站腳本 (XSS) </span><span class="sxs-lookup"><span data-stu-id="476a4-274">Cross-site scripting (XSS)</span></span>](#cross-site-scripting-xss)
* [<span data-ttu-id="476a4-275">跨原始來源保護</span><span class="sxs-lookup"><span data-stu-id="476a4-275">Cross-origin protection</span></span>](#cross-origin-protection)
* [<span data-ttu-id="476a4-276">按一下-劫持</span><span class="sxs-lookup"><span data-stu-id="476a4-276">Click-jacking</span></span>](#click-jacking)
* [<span data-ttu-id="476a4-277">開啟重新導向</span><span class="sxs-lookup"><span data-stu-id="476a4-277">Open redirects</span></span>](#open-redirects)

### <a name="logging-and-sensitive-data"></a><span data-ttu-id="476a4-278">記錄和敏感性資料</span><span class="sxs-lookup"><span data-stu-id="476a4-278">Logging and sensitive data</span></span>

<span data-ttu-id="476a4-279">用戶端與伺服器之間的 JS interop 互動會記錄在具有實例的伺服器記錄中 <xref:Microsoft.Extensions.Logging.ILogger> 。</span><span class="sxs-lookup"><span data-stu-id="476a4-279">JS interop interactions between the client and server are recorded in the server's logs with <xref:Microsoft.Extensions.Logging.ILogger> instances.</span></span> <span data-ttu-id="476a4-280">Blazor 避免記錄敏感性資訊，例如實際事件或 JS interop 輸入和輸出。</span><span class="sxs-lookup"><span data-stu-id="476a4-280">Blazor avoids logging sensitive information, such as actual events or JS interop inputs and outputs.</span></span>

<span data-ttu-id="476a4-281">當伺服器上發生錯誤時，架構會通知用戶端，並向下眼淚會話。</span><span class="sxs-lookup"><span data-stu-id="476a4-281">When an error occurs on the server, the framework notifies the client and tears down the session.</span></span> <span data-ttu-id="476a4-282">根據預設，用戶端會收到一般錯誤訊息，可以在瀏覽器的開發人員工具中看到。</span><span class="sxs-lookup"><span data-stu-id="476a4-282">By default, the client receives a generic error message that can be seen in the browser's developer tools.</span></span>

<span data-ttu-id="476a4-283">用戶端錯誤不包含呼叫堆疊，也不會提供錯誤原因的詳細資料，但伺服器記錄檔包含這類資訊。</span><span class="sxs-lookup"><span data-stu-id="476a4-283">The client-side error doesn't include the callstack and doesn't provide detail on the cause of the error, but server logs do contain such information.</span></span> <span data-ttu-id="476a4-284">基於開發目的，可透過 [啟用詳細錯誤](xref:blazor/fundamentals/handle-errors#blazor-server-detailed-circuit-errors)，讓用戶端可以使用敏感性錯誤資訊。</span><span class="sxs-lookup"><span data-stu-id="476a4-284">For development purposes, sensitive error information can be made available to the client by [enabling detailed errors](xref:blazor/fundamentals/handle-errors#blazor-server-detailed-circuit-errors).</span></span>

> [!WARNING]
> <span data-ttu-id="476a4-285">將錯誤資訊公開給網際網路上的用戶端，應該一律避免使用安全性風險。</span><span class="sxs-lookup"><span data-stu-id="476a4-285">Exposing error information to clients on the Internet is a security risk that should always be avoided.</span></span>

### <a name="protect-information-in-transit-with-https"></a><span data-ttu-id="476a4-286">使用 HTTPS 保護傳輸中的資訊</span><span class="sxs-lookup"><span data-stu-id="476a4-286">Protect information in transit with HTTPS</span></span>

<span data-ttu-id="476a4-287">Blazor ServerSignalR用於用戶端與伺服器之間的通訊。</span><span class="sxs-lookup"><span data-stu-id="476a4-287">Blazor Server uses SignalR for communication between the client and the server.</span></span> <span data-ttu-id="476a4-288">Blazor Server 通常會使用協調的傳輸 SignalR ，這通常是 websocket。</span><span class="sxs-lookup"><span data-stu-id="476a4-288">Blazor Server normally uses the transport that SignalR negotiates, which is typically WebSockets.</span></span>

<span data-ttu-id="476a4-289">Blazor Server 不確定在伺服器與用戶端之間傳送之資料的完整性和機密性。</span><span class="sxs-lookup"><span data-stu-id="476a4-289">Blazor Server doesn't ensure the integrity and confidentiality of the data sent between the server and the client.</span></span> <span data-ttu-id="476a4-290">一律使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="476a4-290">Always use HTTPS.</span></span>

### <a name="cross-site-scripting-xss"></a><span data-ttu-id="476a4-291">跨網站腳本 (XSS) </span><span class="sxs-lookup"><span data-stu-id="476a4-291">Cross-site scripting (XSS)</span></span>

<span data-ttu-id="476a4-292">跨網站腳本 (XSS) 可讓未經授權的合作物件在瀏覽器的內容中執行任意邏輯。</span><span class="sxs-lookup"><span data-stu-id="476a4-292">Cross-site scripting (XSS) allows an unauthorized party to execute arbitrary logic in the context of the browser.</span></span> <span data-ttu-id="476a4-293">遭入侵的應用程式可能會在用戶端上執行任意程式碼。</span><span class="sxs-lookup"><span data-stu-id="476a4-293">A compromised app could potentially run arbitrary code on the client.</span></span> <span data-ttu-id="476a4-294">此弱點可能會用來對伺服器執行一些惡意動作：</span><span class="sxs-lookup"><span data-stu-id="476a4-294">The vulnerability could be used to potentially perform a number of malicious actions against the server:</span></span>

* <span data-ttu-id="476a4-295">將假/無效事件分派至伺服器。</span><span class="sxs-lookup"><span data-stu-id="476a4-295">Dispatch fake/invalid events to the server.</span></span>
* <span data-ttu-id="476a4-296">分派失敗/不正確轉譯完成。</span><span class="sxs-lookup"><span data-stu-id="476a4-296">Dispatch fail/invalid render completions.</span></span>
* <span data-ttu-id="476a4-297">避免分派轉譯完成。</span><span class="sxs-lookup"><span data-stu-id="476a4-297">Avoid dispatching render completions.</span></span>
* <span data-ttu-id="476a4-298">從 JavaScript 將 interop 呼叫分派至 .NET。</span><span class="sxs-lookup"><span data-stu-id="476a4-298">Dispatch interop calls from JavaScript to .NET.</span></span>
* <span data-ttu-id="476a4-299">修改從 .NET 到 JavaScript 之 interop 呼叫的回應。</span><span class="sxs-lookup"><span data-stu-id="476a4-299">Modify the response of interop calls from .NET to JavaScript.</span></span>
* <span data-ttu-id="476a4-300">避免將 .NET 分派至 JS interop 結果。</span><span class="sxs-lookup"><span data-stu-id="476a4-300">Avoid dispatching .NET to JS interop results.</span></span>

<span data-ttu-id="476a4-301">Blazor Server架構會採取下列步驟來防範先前的威脅：</span><span class="sxs-lookup"><span data-stu-id="476a4-301">The Blazor Server framework takes steps to protect against some of the preceding threats:</span></span>

* <span data-ttu-id="476a4-302">如果用戶端未認可轉譯批次，就會停止產生新的 UI 更新。</span><span class="sxs-lookup"><span data-stu-id="476a4-302">Stops producing new UI updates if the client isn't acknowledging render batches.</span></span> <span data-ttu-id="476a4-303">設定為 <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="476a4-303">Configured with <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches?displayProperty=nameWithType>.</span></span>
* <span data-ttu-id="476a4-304">在一分鐘後，任何 .NET 至 JavaScript 呼叫都不會收到來自用戶端的回應。</span><span class="sxs-lookup"><span data-stu-id="476a4-304">Times out any .NET to JavaScript call after one minute without receiving a response from the client.</span></span> <span data-ttu-id="476a4-305">設定為 <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="476a4-305">Configured with <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout?displayProperty=nameWithType>.</span></span>
* <span data-ttu-id="476a4-306">針對在 JS interop 期間來自瀏覽器的所有輸入執行基本驗證：</span><span class="sxs-lookup"><span data-stu-id="476a4-306">Performs basic validation on all input coming from the browser during JS interop:</span></span>
  * <span data-ttu-id="476a4-307">.NET 參考是有效的，而且是 .NET 方法所需的型別。</span><span class="sxs-lookup"><span data-stu-id="476a4-307">.NET references are valid and of the type expected by the .NET method.</span></span>
  * <span data-ttu-id="476a4-308">資料的格式不正確。</span><span class="sxs-lookup"><span data-stu-id="476a4-308">The data isn't malformed.</span></span>
  * <span data-ttu-id="476a4-309">此方法的正確引數數目存在於裝載中。</span><span class="sxs-lookup"><span data-stu-id="476a4-309">The correct number of arguments for the method are present in the payload.</span></span>
  * <span data-ttu-id="476a4-310">在叫用方法之前，可以正確還原序列化引數或結果。</span><span class="sxs-lookup"><span data-stu-id="476a4-310">The arguments or result can be deserialized correctly before invoking the method.</span></span>
* <span data-ttu-id="476a4-311">針對來自已分派事件之瀏覽器的所有輸入，執行基本驗證：</span><span class="sxs-lookup"><span data-stu-id="476a4-311">Performs basic validation in all input coming from the browser from dispatched events:</span></span>
  * <span data-ttu-id="476a4-312">事件具有有效的型別。</span><span class="sxs-lookup"><span data-stu-id="476a4-312">The event has a valid type.</span></span>
  * <span data-ttu-id="476a4-313">事件的資料可以還原序列化。</span><span class="sxs-lookup"><span data-stu-id="476a4-313">The data for the event can be deserialized.</span></span>
  * <span data-ttu-id="476a4-314">有與事件相關聯的事件處理常式。</span><span class="sxs-lookup"><span data-stu-id="476a4-314">There's an event handler associated with the event.</span></span>

<span data-ttu-id="476a4-315">除了架構所實行的保護，應用程式必須由開發人員撰寫程式碼，以防範威脅並採取適當的動作：</span><span class="sxs-lookup"><span data-stu-id="476a4-315">In addition to the safeguards that the framework implements, the app must be coded by the developer to safeguard against threats and take appropriate actions:</span></span>

* <span data-ttu-id="476a4-316">處理事件時，一律驗證資料。</span><span class="sxs-lookup"><span data-stu-id="476a4-316">Always validate data when handling events.</span></span>
* <span data-ttu-id="476a4-317">在接收無效資料時採取適當的動作：</span><span class="sxs-lookup"><span data-stu-id="476a4-317">Take appropriate action upon receiving invalid data:</span></span>
  * <span data-ttu-id="476a4-318">略過資料並返回。</span><span class="sxs-lookup"><span data-stu-id="476a4-318">Ignore the data and return.</span></span> <span data-ttu-id="476a4-319">這可讓應用程式繼續處理要求。</span><span class="sxs-lookup"><span data-stu-id="476a4-319">This allows the app to continue processing requests.</span></span>
  * <span data-ttu-id="476a4-320">如果應用程式判斷輸入是非法的，且無法由合法用戶端產生，則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="476a4-320">If the app determines that the input is illegitimate and couldn't be produced by legitimate client, throw an exception.</span></span> <span data-ttu-id="476a4-321">擲回例外狀況會向下眼淚，並結束會話。</span><span class="sxs-lookup"><span data-stu-id="476a4-321">Throwing an exception tears down the circuit and ends the session.</span></span>
* <span data-ttu-id="476a4-322">請勿信任記錄中包含的轉譯批次完成所提供的錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="476a4-322">Don't trust the error message provided by render batch completions included in the logs.</span></span> <span data-ttu-id="476a4-323">此錯誤是由用戶端提供，且通常無法信任，因為用戶端可能遭到入侵。</span><span class="sxs-lookup"><span data-stu-id="476a4-323">The error is provided by the client and can't generally be trusted, as the client might be compromised.</span></span>
* <span data-ttu-id="476a4-324">請勿在 JavaScript 與 .NET 方法之間的任何一種方向信任 JS interop 呼叫的輸入。</span><span class="sxs-lookup"><span data-stu-id="476a4-324">Don't trust the input on JS interop calls in either direction between JavaScript and .NET methods.</span></span>
* <span data-ttu-id="476a4-325">應用程式會負責驗證引數和結果的內容是否有效，即使引數或結果已正確還原序列化。</span><span class="sxs-lookup"><span data-stu-id="476a4-325">The app is responsible for validating that the content of arguments and results are valid, even if the arguments or results are correctly deserialized.</span></span>

<span data-ttu-id="476a4-326">若要讓 XSS 弱點存在，應用程式必須將使用者輸入併入轉譯的頁面中。</span><span class="sxs-lookup"><span data-stu-id="476a4-326">For a XSS vulnerability to exist, the app must incorporate user input in the rendered page.</span></span> <span data-ttu-id="476a4-327">Blazor Server 元件會執行編譯時期步驟，其中檔案中的標記 `.razor` 會轉換成程式的 c # 邏輯。</span><span class="sxs-lookup"><span data-stu-id="476a4-327">Blazor Server components execute a compile-time step where the markup in a `.razor` file is transformed into procedural C# logic.</span></span> <span data-ttu-id="476a4-328">在執行時間，c # 邏輯會建立轉譯 *樹狀結構* ，以描述元素、文字和子元件。</span><span class="sxs-lookup"><span data-stu-id="476a4-328">At runtime, the C# logic builds a *render tree* describing the elements, text, and child components.</span></span> <span data-ttu-id="476a4-329">這會透過 JavaScript 指令的序列套用至瀏覽器的 DOM， (，或在進行) 的呈現時，會序列化為 HTML：</span><span class="sxs-lookup"><span data-stu-id="476a4-329">This is applied to the browser's DOM via a sequence of JavaScript instructions (or is serialized to HTML in the case of prerendering):</span></span>

* <span data-ttu-id="476a4-330">透過一般 Razor 語法所呈現的使用者輸入 (例如， `@someStringValue`) 不會公開 XSS 弱點，因為 Razor 語法是透過只能寫入文字的命令新增至 DOM。</span><span class="sxs-lookup"><span data-stu-id="476a4-330">User input rendered via normal Razor syntax (for example, `@someStringValue`) doesn't expose a XSS vulnerability because the Razor syntax is added to the DOM via commands that can only write text.</span></span> <span data-ttu-id="476a4-331">即使值包含 HTML 標籤，值也會顯示為靜態文字。</span><span class="sxs-lookup"><span data-stu-id="476a4-331">Even if the value includes HTML markup, the value is displayed as static text.</span></span> <span data-ttu-id="476a4-332">當預先呈現時，輸出會以 HTML 編碼，也會將內容顯示為靜態文字。</span><span class="sxs-lookup"><span data-stu-id="476a4-332">When prerendering, the output is HTML-encoded, which also displays the content as static text.</span></span>
* <span data-ttu-id="476a4-333">不允許腳本標記，也不應該包含在應用程式的元件呈現樹狀結構中。</span><span class="sxs-lookup"><span data-stu-id="476a4-333">Script tags aren't allowed and shouldn't be included in the app's component render tree.</span></span> <span data-ttu-id="476a4-334">如果腳本標記包含在元件的標記中，則會產生編譯階段錯誤。</span><span class="sxs-lookup"><span data-stu-id="476a4-334">If a script tag is included in a component's markup, a compile-time error is generated.</span></span>
* <span data-ttu-id="476a4-335">元件作者可以在 c # 中撰寫元件，而不需要使用 Razor 。</span><span class="sxs-lookup"><span data-stu-id="476a4-335">Component authors can author components in C# without using Razor.</span></span> <span data-ttu-id="476a4-336">元件作者負責在發出輸出時使用正確的 Api。</span><span class="sxs-lookup"><span data-stu-id="476a4-336">The component author is responsible for using the correct APIs when emitting output.</span></span> <span data-ttu-id="476a4-337">例如，使用 `builder.AddContent(0, someUserSuppliedString)` 而 *非* `builder.AddMarkupContent(0, someUserSuppliedString)` ，因為後者可能會產生 XSS 弱點。</span><span class="sxs-lookup"><span data-stu-id="476a4-337">For example, use `builder.AddContent(0, someUserSuppliedString)` and *not* `builder.AddMarkupContent(0, someUserSuppliedString)`, as the latter could create a XSS vulnerability.</span></span>

<span data-ttu-id="476a4-338">在保護 XSS 攻擊的過程中，請考慮執行 XSS 防護措施，例如 [ (CSP) 的內容安全性原則 ](https://developer.mozilla.org/docs/Web/HTTP/CSP)。</span><span class="sxs-lookup"><span data-stu-id="476a4-338">As part of protecting against XSS attacks, consider implementing XSS mitigations, such as [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP).</span></span>

<span data-ttu-id="476a4-339">如需詳細資訊，請參閱<xref:security/cross-site-scripting>。</span><span class="sxs-lookup"><span data-stu-id="476a4-339">For more information, see <xref:security/cross-site-scripting>.</span></span>

### <a name="cross-origin-protection"></a><span data-ttu-id="476a4-340">跨原始來源保護</span><span class="sxs-lookup"><span data-stu-id="476a4-340">Cross-origin protection</span></span>

<span data-ttu-id="476a4-341">跨原始來源攻擊涉及來自不同來源的用戶端，對伺服器執行動作。</span><span class="sxs-lookup"><span data-stu-id="476a4-341">Cross-origin attacks involve a client from a different origin performing an action against the server.</span></span> <span data-ttu-id="476a4-342">惡意動作通常是 GET 要求或表單 POST (的跨網站要求偽造、CSRF) ，但也可能會開啟惡意 WebSocket。</span><span class="sxs-lookup"><span data-stu-id="476a4-342">The malicious action is typically a GET request or a form POST (Cross-Site Request Forgery, CSRF), but opening a malicious WebSocket is also possible.</span></span> <span data-ttu-id="476a4-343">Blazor Server 應用程式 [會提供與任何其他 SignalR 使用中樞通訊協定的應用程式相同的保證](xref:signalr/security)：</span><span class="sxs-lookup"><span data-stu-id="476a4-343">Blazor Server apps offer [the same guarantees that any other SignalR app using the hub protocol offer](xref:signalr/security):</span></span>

* <span data-ttu-id="476a4-344">Blazor Server 應用程式可以跨原始來源存取，除非採取其他措施來防止此應用程式。</span><span class="sxs-lookup"><span data-stu-id="476a4-344">Blazor Server apps can be accessed cross-origin unless additional measures are taken to prevent it.</span></span> <span data-ttu-id="476a4-345">若要停用跨原始來源存取，請停用端點中的 CORS，方法是將 CORS 中介軟體新增至管線，並將加入 <xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute> 至 Blazor 端點中繼資料，或藉由設定 [ SignalR 跨原始來源資源分享](xref:signalr/security#cross-origin-resource-sharing)來限制允許的來源集合。</span><span class="sxs-lookup"><span data-stu-id="476a4-345">To disable cross-origin access, either disable CORS in the endpoint by adding the CORS middleware to the pipeline and adding the <xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute> to the Blazor endpoint metadata or limit the set of allowed origins by [configuring SignalR for cross-origin resource sharing](xref:signalr/security#cross-origin-resource-sharing).</span></span>
* <span data-ttu-id="476a4-346">如果已啟用 CORS，可能需要額外的步驟，以根據 CORS 設定來保護應用程式。</span><span class="sxs-lookup"><span data-stu-id="476a4-346">If CORS is enabled, extra steps might be required to protect the app depending on the CORS configuration.</span></span> <span data-ttu-id="476a4-347">如果 CORS 是全域啟用的，則在呼叫端點路由產生器之後，您可以將 Blazor Server <xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute> 中繼資料新增至端點中繼資料，以停用中樞的 cors <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> 。</span><span class="sxs-lookup"><span data-stu-id="476a4-347">If CORS is globally enabled, CORS can be disabled for the Blazor Server hub by adding the <xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute> metadata to the endpoint metadata after calling <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> on the endpoint route builder.</span></span>

<span data-ttu-id="476a4-348">如需詳細資訊，請參閱<xref:security/anti-request-forgery>。</span><span class="sxs-lookup"><span data-stu-id="476a4-348">For more information, see <xref:security/anti-request-forgery>.</span></span>

### <a name="click-jacking"></a><span data-ttu-id="476a4-349">按一下-劫持</span><span class="sxs-lookup"><span data-stu-id="476a4-349">Click-jacking</span></span>

<span data-ttu-id="476a4-350">按一下-劫持牽涉 `<iframe>` 到從不同的來源將網站轉譯為網站內的網站，以誘騙使用者在遭受攻擊的網站上執行動作。</span><span class="sxs-lookup"><span data-stu-id="476a4-350">Click-jacking involves rendering a site as an `<iframe>` inside a site from a different origin in order to trick the user into performing actions on the site under attack.</span></span>

<span data-ttu-id="476a4-351">若要防止應用程式在中轉譯 `<iframe>` ，請使用 [內容安全性原則 (CSP) ](https://developer.mozilla.org/docs/Web/HTTP/CSP) 和 `X-Frame-Options` 標頭。</span><span class="sxs-lookup"><span data-stu-id="476a4-351">To protect an app from rendering inside of an `<iframe>`, use [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) and the `X-Frame-Options` header.</span></span> <span data-ttu-id="476a4-352">如需詳細資訊，請參閱 [MDN web 檔： X 框架選項](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options)。</span><span class="sxs-lookup"><span data-stu-id="476a4-352">For more information, see [MDN web docs: X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options).</span></span>

### <a name="open-redirects"></a><span data-ttu-id="476a4-353">開啟重新導向</span><span class="sxs-lookup"><span data-stu-id="476a4-353">Open redirects</span></span>

<span data-ttu-id="476a4-354">當 Blazor Server 應用程式會話啟動時，伺服器會針對啟動會話時所傳送的 url 執行基本驗證。</span><span class="sxs-lookup"><span data-stu-id="476a4-354">When a Blazor Server app session starts, the server performs basic validation of the URLs sent as part of starting the session.</span></span> <span data-ttu-id="476a4-355">架構會在建立線路之前，檢查基底 URL 是否為目前 URL 的父系。</span><span class="sxs-lookup"><span data-stu-id="476a4-355">The framework checks that the base URL is a parent of the current URL before establishing the circuit.</span></span> <span data-ttu-id="476a4-356">架構不會執行其他檢查。</span><span class="sxs-lookup"><span data-stu-id="476a4-356">No additional checks are performed by the framework.</span></span>

<span data-ttu-id="476a4-357">當使用者選取用戶端上的連結時，連結的 URL 會傳送至伺服器，以決定要採取的動作。</span><span class="sxs-lookup"><span data-stu-id="476a4-357">When a user selects a link on the client, the URL for the link is sent to the server, which determines what action to take.</span></span> <span data-ttu-id="476a4-358">例如，應用程式可能會執行用戶端導覽，或向瀏覽器表示移至新位置。</span><span class="sxs-lookup"><span data-stu-id="476a4-358">For example, the app may perform a client-side navigation or indicate to the browser to go to the new location.</span></span>

<span data-ttu-id="476a4-359">元件也可以透過使用，以程式設計方式觸發流覽要求 <xref:Microsoft.AspNetCore.Components.NavigationManager> 。</span><span class="sxs-lookup"><span data-stu-id="476a4-359">Components can also trigger navigation requests programatically through the use of <xref:Microsoft.AspNetCore.Components.NavigationManager>.</span></span> <span data-ttu-id="476a4-360">在這種情況下，應用程式可能會執行用戶端導覽，或向瀏覽器表示移至新的位置。</span><span class="sxs-lookup"><span data-stu-id="476a4-360">In such scenarios, the app might perform a client-side navigation or indicate to the browser to go to the new location.</span></span>

<span data-ttu-id="476a4-361">元件必須：</span><span class="sxs-lookup"><span data-stu-id="476a4-361">Components must:</span></span>

* <span data-ttu-id="476a4-362">避免使用使用者輸入做為導覽呼叫引數的一部分。</span><span class="sxs-lookup"><span data-stu-id="476a4-362">Avoid using user input as part of the navigation call arguments.</span></span>
* <span data-ttu-id="476a4-363">驗證引數，以確保應用程式允許該目標。</span><span class="sxs-lookup"><span data-stu-id="476a4-363">Validate arguments to ensure that the target is allowed by the app.</span></span>

<span data-ttu-id="476a4-364">否則，惡意使用者可以強制瀏覽器移至攻擊者控制的網站。</span><span class="sxs-lookup"><span data-stu-id="476a4-364">Otherwise, a malicious user can force the browser to go to an attacker-controlled site.</span></span> <span data-ttu-id="476a4-365">在此案例中，攻擊者會將應用程式訣竅為使用部分使用者輸入做為方法調用的一部分 <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="476a4-365">In this scenario, the attacker tricks the app into using some user input as part of the invocation of the <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> method.</span></span>

<span data-ttu-id="476a4-366">當轉譯連結作為應用程式的一部分時，也適用這項建議：</span><span class="sxs-lookup"><span data-stu-id="476a4-366">This advice also applies when rendering links as part of the app:</span></span>

* <span data-ttu-id="476a4-367">可能的話，請使用相對連結。</span><span class="sxs-lookup"><span data-stu-id="476a4-367">If possible, use relative links.</span></span>
* <span data-ttu-id="476a4-368">先驗證絕對連結目的地是否有效，再將它們包含在頁面中。</span><span class="sxs-lookup"><span data-stu-id="476a4-368">Validate that absolute link destinations are valid before including them in a page.</span></span>

<span data-ttu-id="476a4-369">如需詳細資訊，請參閱<xref:security/preventing-open-redirects>。</span><span class="sxs-lookup"><span data-stu-id="476a4-369">For more information, see <xref:security/preventing-open-redirects>.</span></span>

## <a name="security-checklist"></a><span data-ttu-id="476a4-370">安全性檢查清單</span><span class="sxs-lookup"><span data-stu-id="476a4-370">Security checklist</span></span>

<span data-ttu-id="476a4-371">下列安全性考慮清單不完整：</span><span class="sxs-lookup"><span data-stu-id="476a4-371">The following list of security considerations isn't comprehensive:</span></span>

* <span data-ttu-id="476a4-372">驗證事件的引數。</span><span class="sxs-lookup"><span data-stu-id="476a4-372">Validate arguments from events.</span></span>
* <span data-ttu-id="476a4-373">從 JS interop 呼叫驗證輸入和結果。</span><span class="sxs-lookup"><span data-stu-id="476a4-373">Validate inputs and results from JS interop calls.</span></span>
* <span data-ttu-id="476a4-374">請避免使用 (，或事先驗證 .NET to JS interop 呼叫的使用者輸入) 。</span><span class="sxs-lookup"><span data-stu-id="476a4-374">Avoid using (or validate beforehand) user input for .NET to JS interop calls.</span></span>
* <span data-ttu-id="476a4-375">防止用戶端配置未系結的記憶體量。</span><span class="sxs-lookup"><span data-stu-id="476a4-375">Prevent the client from allocating an unbound amount of memory.</span></span>
  * <span data-ttu-id="476a4-376">元件中的資料。</span><span class="sxs-lookup"><span data-stu-id="476a4-376">Data within the component.</span></span>
  * <span data-ttu-id="476a4-377">`DotNetObject` 傳回給用戶端的參考。</span><span class="sxs-lookup"><span data-stu-id="476a4-377">`DotNetObject` references returned to the client.</span></span>
* <span data-ttu-id="476a4-378">防止多個分派。</span><span class="sxs-lookup"><span data-stu-id="476a4-378">Guard against multiple dispatches.</span></span>
* <span data-ttu-id="476a4-379">在處置元件時，取消長時間執行的作業。</span><span class="sxs-lookup"><span data-stu-id="476a4-379">Cancel long-running operations when the component is disposed.</span></span>
* <span data-ttu-id="476a4-380">避免產生大量資料的事件。</span><span class="sxs-lookup"><span data-stu-id="476a4-380">Avoid events that produce large amounts of data.</span></span>
* <span data-ttu-id="476a4-381">如果無法避免，請避免在呼叫中使用使用者輸入 <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> ，並針對一組允許的來源驗證使用者輸入。</span><span class="sxs-lookup"><span data-stu-id="476a4-381">Avoid using user input as part of calls to <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> and validate user input for URLs against a set of allowed origins first if unavoidable.</span></span>
* <span data-ttu-id="476a4-382">請勿根據 UI 的狀態，但只能從元件狀態進行授權決策。</span><span class="sxs-lookup"><span data-stu-id="476a4-382">Don't make authorization decisions based on the state of the UI but only from component state.</span></span>
* <span data-ttu-id="476a4-383">請考慮使用 [內容安全性原則 (CSP) ](https://developer.mozilla.org/docs/Web/HTTP/CSP) 來防範 XSS 攻擊。</span><span class="sxs-lookup"><span data-stu-id="476a4-383">Consider using [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) to protect against XSS attacks.</span></span>
* <span data-ttu-id="476a4-384">請考慮使用 CSP 和 [X 框架選項](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) ，以防止按一下劫持。</span><span class="sxs-lookup"><span data-stu-id="476a4-384">Consider using CSP and [X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) to protect against click-jacking.</span></span>
* <span data-ttu-id="476a4-385">啟用 CORS 或明確停用應用程式的 CORS 時，請確定 CORS 設定適當 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="476a4-385">Ensure CORS settings are appropriate when enabling CORS or explicitly disable CORS for Blazor apps.</span></span>
* <span data-ttu-id="476a4-386">測試以確保應用程式的伺服器端限制 Blazor 提供可接受的使用者體驗，而不會有無法接受的風險層級。</span><span class="sxs-lookup"><span data-stu-id="476a4-386">Test to ensure that the server-side limits for the Blazor app provide an acceptable user experience without unacceptable levels of risk.</span></span>
