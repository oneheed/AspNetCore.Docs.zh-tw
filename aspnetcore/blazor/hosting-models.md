---
title: ASP.NET Core Blazor 裝載模型
author: guardrex
description: 瞭解 Blazor WebAssembly 和 Blazor Server 裝載模型。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/07/2020
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
uid: blazor/hosting-models
ms.openlocfilehash: c60191cc8822dd875db613fb70516beb08a659d8
ms.sourcegitcommit: 20a41c8e40a2e69e99291e2fe18caa04c02e7109
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2021
ms.locfileid: "99578278"
---
# <a name="aspnet-core-blazor-hosting-models"></a><span data-ttu-id="3d928-103">ASP.NET Core Blazor 裝載模型</span><span class="sxs-lookup"><span data-stu-id="3d928-103">ASP.NET Core Blazor hosting models</span></span>

<span data-ttu-id="3d928-104">依 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="3d928-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="3d928-105">Blazor 是設計用來在瀏覽器中以 [WebAssembly](https://webassembly.org/)為基礎的 .net 執行時間執行用戶端的 web 架構， (*Blazor WebAssembly*) 或 ASP.NET Core () 中的伺服器端 *Blazor Server* 。</span><span class="sxs-lookup"><span data-stu-id="3d928-105">Blazor is a web framework designed to run client-side in the browser on a [WebAssembly](https://webassembly.org/)-based .NET runtime (*Blazor WebAssembly*) or server-side in ASP.NET Core (*Blazor Server*).</span></span> <span data-ttu-id="3d928-106">無論裝載模型為何，應用程式和元件模型 *都相同*。</span><span class="sxs-lookup"><span data-stu-id="3d928-106">Regardless of the hosting model, the app and component models *are the same*.</span></span>

## Blazor WebAssembly

<span data-ttu-id="3d928-107">主要 Blazor 裝載模型正在 WebAssembly 的瀏覽器中執行用戶端。</span><span class="sxs-lookup"><span data-stu-id="3d928-107">The primary Blazor hosting model is running client-side in the browser on WebAssembly.</span></span> <span data-ttu-id="3d928-108">Blazor應用程式、其相依性和 .net 執行時間會下載至瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="3d928-108">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span> <span data-ttu-id="3d928-109">應用程式會直接在瀏覽器 UI 執行緒上執行。</span><span class="sxs-lookup"><span data-stu-id="3d928-109">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="3d928-110">UI 更新和事件處理會在相同的進程中進行。</span><span class="sxs-lookup"><span data-stu-id="3d928-110">UI updates and event handling occur within the same process.</span></span> <span data-ttu-id="3d928-111">應用程式的資產會以靜態檔案的形式部署至 web 伺服器或服務，以提供靜態內容給用戶端。</span><span class="sxs-lookup"><span data-stu-id="3d928-111">The app's assets are deployed as static files to a web server or service capable of serving static content to clients.</span></span>

![：：：非 loc (Blazor WebAssembly) ：：：：：：：非 loc (Blazor) ：：：應用程式會在瀏覽器內的 UI 執行緒上執行。](hosting-models/_static/blazor-webassembly.png)

<span data-ttu-id="3d928-113">Blazor WebAssembly建立應用程式以進行部署，而不使用後端 ASP.NET Core 應用程式來提供其檔案時，應用程式稱為 *獨立* Blazor WebAssembly 應用程式。</span><span class="sxs-lookup"><span data-stu-id="3d928-113">When the Blazor WebAssembly app is created for deployment without a backend ASP.NET Core app to serve its files, the app is called a *standalone* Blazor WebAssembly app.</span></span> <span data-ttu-id="3d928-114">建立應用程式以供使用後端應用程式進行部署以提供其檔案時，應用程式稱為 *託管* Blazor WebAssembly 應用程式。</span><span class="sxs-lookup"><span data-stu-id="3d928-114">When the app is created for deployment with a backend app to serve its files, the app is called a *hosted* Blazor WebAssembly app.</span></span> <span data-ttu-id="3d928-115">託管 Blazor WebAssembly 應用程式通常會使用 WEB API 呼叫或 () ，透過網路與伺服器互動 [SignalR](xref:signalr/introduction) <xref:tutorials/signalr-blazor> 。</span><span class="sxs-lookup"><span data-stu-id="3d928-115">A hosted Blazor WebAssembly app typically interacts with the server over the network using web API calls or [SignalR](xref:signalr/introduction) (<xref:tutorials/signalr-blazor>).</span></span>

<span data-ttu-id="3d928-116">`blazor.webassembly.js`腳本是由架構所提供，並處理：</span><span class="sxs-lookup"><span data-stu-id="3d928-116">The `blazor.webassembly.js` script is provided by the framework and handles:</span></span>

* <span data-ttu-id="3d928-117">下載 .NET 執行時間、應用程式和應用程式的相依性。</span><span class="sxs-lookup"><span data-stu-id="3d928-117">Downloading the .NET runtime, the app, and the app's dependencies.</span></span>
* <span data-ttu-id="3d928-118">要執行應用程式的執行時間初始化。</span><span class="sxs-lookup"><span data-stu-id="3d928-118">Initialization of the runtime to run the app.</span></span>

<span data-ttu-id="3d928-119">Blazor WebAssembly裝載模型提供幾項優點：</span><span class="sxs-lookup"><span data-stu-id="3d928-119">The Blazor WebAssembly hosting model offers several benefits:</span></span>

* <span data-ttu-id="3d928-120">沒有 .NET 伺服器端相依性。</span><span class="sxs-lookup"><span data-stu-id="3d928-120">There's no .NET server-side dependency.</span></span> <span data-ttu-id="3d928-121">應用程式在下載至用戶端之後可完全正常運作。</span><span class="sxs-lookup"><span data-stu-id="3d928-121">The app is fully functioning after it's downloaded to the client.</span></span>
* <span data-ttu-id="3d928-122">用戶端資源和功能已完全運用。</span><span class="sxs-lookup"><span data-stu-id="3d928-122">Client resources and capabilities are fully leveraged.</span></span>
* <span data-ttu-id="3d928-123">工作會從伺服器卸載至用戶端。</span><span class="sxs-lookup"><span data-stu-id="3d928-123">Work is offloaded from the server to the client.</span></span>
* <span data-ttu-id="3d928-124">不需要 ASP.NET Core web 伺服器來裝載應用程式。</span><span class="sxs-lookup"><span data-stu-id="3d928-124">An ASP.NET Core web server isn't required to host the app.</span></span> <span data-ttu-id="3d928-125">無伺服器部署案例是可行的，例如，從內容傳遞網路提供應用程式 (CDN) 。</span><span class="sxs-lookup"><span data-stu-id="3d928-125">Serverless deployment scenarios are possible, such as serving the app from a Content Delivery Network (CDN).</span></span>

<span data-ttu-id="3d928-126">Blazor WebAssembly裝載模型有下列限制：</span><span class="sxs-lookup"><span data-stu-id="3d928-126">The Blazor WebAssembly hosting model has the following limitations:</span></span>

* <span data-ttu-id="3d928-127">應用程式僅限於瀏覽器的功能。</span><span class="sxs-lookup"><span data-stu-id="3d928-127">The app is restricted to the capabilities of the browser.</span></span>
* <span data-ttu-id="3d928-128">支援的用戶端硬體和軟體 (例如，需要 WebAssembly 支援) 。</span><span class="sxs-lookup"><span data-stu-id="3d928-128">Capable client hardware and software (for example, WebAssembly support) is required.</span></span>
* <span data-ttu-id="3d928-129">下載大小更大，而應用程式需要較長的時間才能載入。</span><span class="sxs-lookup"><span data-stu-id="3d928-129">Download size is larger, and apps take longer to load.</span></span>
* <span data-ttu-id="3d928-130">.NET 執行時間和工具支援較不成熟。</span><span class="sxs-lookup"><span data-stu-id="3d928-130">.NET runtime and tooling support is less mature.</span></span> <span data-ttu-id="3d928-131">例如， [.NET Standard](/dotnet/standard/net-standard) 支援和偵錯工具中有限制。</span><span class="sxs-lookup"><span data-stu-id="3d928-131">For example, limitations exist in [.NET Standard](/dotnet/standard/net-standard) support and debugging.</span></span>

<span data-ttu-id="3d928-132">若要建立 Blazor WebAssembly 應用程式，請參閱 <xref:blazor/tooling> 。</span><span class="sxs-lookup"><span data-stu-id="3d928-132">To create a Blazor WebAssembly app, see <xref:blazor/tooling>.</span></span>

<span data-ttu-id="3d928-133">託管 Blazor 應用程式模型支援 [Docker 容器](/dotnet/standard/microservices-architecture/container-docker-introduction/index)。</span><span class="sxs-lookup"><span data-stu-id="3d928-133">The hosted Blazor app model supports [Docker containers](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span></span> <span data-ttu-id="3d928-134">如需 Visual Studio 中的 Docker 支援，請以滑鼠右鍵按一下 `Server` 託管解決方案的專案， Blazor WebAssembly 然後選取 [**新增**  >  **Docker 支援**]。</span><span class="sxs-lookup"><span data-stu-id="3d928-134">For Docker support in Visual Studio, right-click on the `Server` project of a hosted Blazor WebAssembly solution and select **Add** > **Docker Support**.</span></span>

## Blazor Server

<span data-ttu-id="3d928-135">使用 Blazor Server 裝載模型時，會在伺服器上從 ASP.NET Core 應用程式中執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="3d928-135">With the Blazor Server hosting model, the app is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="3d928-136">UI 更新、事件處理及 JavaScript 呼叫會透過連接來處理 [SignalR](xref:signalr/introduction) 。</span><span class="sxs-lookup"><span data-stu-id="3d928-136">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

![瀏覽器會與應用程式 (裝載在伺服器上 ASP.NET Core 應用程式) 的應用程式，而不是透過：：： no loc (SignalR) ：：： connection。](hosting-models/_static/blazor-server.png)

<span data-ttu-id="3d928-138">ASP.NET Core 應用程式會參考要新增的應用程式 `Startup` 類別：</span><span class="sxs-lookup"><span data-stu-id="3d928-138">The ASP.NET Core app references the app's `Startup` class to add:</span></span>

* <span data-ttu-id="3d928-139">伺服器端服務。</span><span class="sxs-lookup"><span data-stu-id="3d928-139">Server-side services.</span></span>
* <span data-ttu-id="3d928-140">要求處理管線的應用程式。</span><span class="sxs-lookup"><span data-stu-id="3d928-140">The app to the request handling pipeline.</span></span>

<span data-ttu-id="3d928-141">在用戶端上， `blazor.server.js` 腳本會建立 SignalR 與伺服器之間的連接。</span><span class="sxs-lookup"><span data-stu-id="3d928-141">On the client, the `blazor.server.js` script establishes the SignalR connection with the server.</span></span> <span data-ttu-id="3d928-142">從 ASP.NET Core 共用架構中的內嵌資源，將腳本提供給用戶端應用程式。</span><span class="sxs-lookup"><span data-stu-id="3d928-142">The script is served to the client-side app from an embedded resource in the ASP.NET Core shared framework.</span></span> <span data-ttu-id="3d928-143">用戶端應用程式會視需要負責保存和還原應用程式狀態。</span><span class="sxs-lookup"><span data-stu-id="3d928-143">The client-side app is responsible for persisting and restoring app state as required.</span></span> 

<span data-ttu-id="3d928-144">Blazor Server裝載模型提供幾項優點：</span><span class="sxs-lookup"><span data-stu-id="3d928-144">The Blazor Server hosting model offers several benefits:</span></span>

* <span data-ttu-id="3d928-145">下載大小遠小於 Blazor WebAssembly 應用程式，且應用程式的載入速度更快。</span><span class="sxs-lookup"><span data-stu-id="3d928-145">Download size is significantly smaller than a Blazor WebAssembly app, and the app loads much faster.</span></span>
* <span data-ttu-id="3d928-146">應用程式會充分利用伺服器功能，包括使用任何 .NET Core 相容的 Api。</span><span class="sxs-lookup"><span data-stu-id="3d928-146">The app takes full advantage of server capabilities, including use of any .NET Core compatible APIs.</span></span>
* <span data-ttu-id="3d928-147">伺服器上的 .NET Core 可用來執行應用程式，因此現有的 .NET 工具（例如偵錯工具）會如預期般運作。</span><span class="sxs-lookup"><span data-stu-id="3d928-147">.NET Core on the server is used to run the app, so existing .NET tooling, such as debugging, works as expected.</span></span>
* <span data-ttu-id="3d928-148">支援瘦用戶端。</span><span class="sxs-lookup"><span data-stu-id="3d928-148">Thin clients are supported.</span></span> <span data-ttu-id="3d928-149">例如， Blazor Server 應用程式會使用不支援 WebAssembly 和資源受限裝置的瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="3d928-149">For example, Blazor Server apps work with browsers that don't support WebAssembly and on resource-constrained devices.</span></span>
* <span data-ttu-id="3d928-150">應用程式的 .NET/c # 程式碼基底（包括應用程式的元件程式碼）不會提供給用戶端。</span><span class="sxs-lookup"><span data-stu-id="3d928-150">The app's .NET/C# code base, including the app's component code, isn't served to clients.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="3d928-151">Blazor Server應用程式會 prerenders 以回應第一個用戶端要求，這會在伺服器上建立 UI 狀態。</span><span class="sxs-lookup"><span data-stu-id="3d928-151">A Blazor Server app prerenders in response to the first client request, which creates the UI state on the server.</span></span> <span data-ttu-id="3d928-152">當用戶端嘗試建立連接時 SignalR ， **用戶端必須重新連接到相同的伺服器**。</span><span class="sxs-lookup"><span data-stu-id="3d928-152">When the client attempts to create a SignalR connection, **the client must reconnect to the same server**.</span></span> <span data-ttu-id="3d928-153">Blazor Server 使用多部後端伺服器的應用程式應該執行連線的「 *粘滯話」會話* SignalR 。</span><span class="sxs-lookup"><span data-stu-id="3d928-153">Blazor Server apps that use more than one backend server should implement *sticky sessions* for SignalR connections.</span></span> <span data-ttu-id="3d928-154">如需詳細資訊，請參閱 [伺服器的連接](#connection-to-the-server) 一節。</span><span class="sxs-lookup"><span data-stu-id="3d928-154">For more information, see the [Connection to the server](#connection-to-the-server) section.</span></span>

<span data-ttu-id="3d928-155">Blazor Server裝載模型有下列限制：</span><span class="sxs-lookup"><span data-stu-id="3d928-155">The Blazor Server hosting model has the following limitations:</span></span>

* <span data-ttu-id="3d928-156">通常會有較高的延遲。</span><span class="sxs-lookup"><span data-stu-id="3d928-156">Higher latency usually exists.</span></span> <span data-ttu-id="3d928-157">每個使用者互動都牽涉到網路躍點。</span><span class="sxs-lookup"><span data-stu-id="3d928-157">Every user interaction involves a network hop.</span></span>
* <span data-ttu-id="3d928-158">沒有離線支援。</span><span class="sxs-lookup"><span data-stu-id="3d928-158">There's no offline support.</span></span> <span data-ttu-id="3d928-159">如果用戶端連接失敗，應用程式就會停止運作。</span><span class="sxs-lookup"><span data-stu-id="3d928-159">If the client connection fails, the app stops working.</span></span>
* <span data-ttu-id="3d928-160">針對具有許多使用者的應用程式，擴充性相當困難。</span><span class="sxs-lookup"><span data-stu-id="3d928-160">Scalability is challenging for apps with many users.</span></span> <span data-ttu-id="3d928-161">伺服器必須管理多個用戶端連接並處理用戶端狀態。</span><span class="sxs-lookup"><span data-stu-id="3d928-161">The server must manage multiple client connections and handle client state.</span></span>
* <span data-ttu-id="3d928-162">需要 ASP.NET Core 伺服器才能提供應用程式。</span><span class="sxs-lookup"><span data-stu-id="3d928-162">An ASP.NET Core server is required to serve the app.</span></span> <span data-ttu-id="3d928-163">無法進行無伺服器部署案例，例如，從內容傳遞網路提供應用程式 (CDN) 。</span><span class="sxs-lookup"><span data-stu-id="3d928-163">Serverless deployment scenarios aren't possible, such as serving the app from a Content Delivery Network (CDN).</span></span>

<span data-ttu-id="3d928-164">若要建立 Blazor Server 應用程式，請參閱 <xref:blazor/tooling> 。</span><span class="sxs-lookup"><span data-stu-id="3d928-164">To create a Blazor Server app, see <xref:blazor/tooling>.</span></span>

<span data-ttu-id="3d928-165">Blazor Server應用程式模型支援[Docker 容器](/dotnet/standard/microservices-architecture/container-docker-introduction/index)。</span><span class="sxs-lookup"><span data-stu-id="3d928-165">The Blazor Server app model supports [Docker containers](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span></span> <span data-ttu-id="3d928-166">如需 Visual Studio 中的 Docker 支援，請在 Visual Studio 中以滑鼠右鍵按一下專案，然後選取 [**新增**  >  **Docker 支援**]。</span><span class="sxs-lookup"><span data-stu-id="3d928-166">For Docker support in Visual Studio, right-click on the project in Visual Studio and select **Add** > **Docker Support**.</span></span>

### <a name="comparison-to-server-rendered-ui"></a><span data-ttu-id="3d928-167">與伺服器呈現的 UI 比較</span><span class="sxs-lookup"><span data-stu-id="3d928-167">Comparison to server-rendered UI</span></span>

<span data-ttu-id="3d928-168">瞭解應用程式的其中一種方式 Blazor Server ，就是了解它與傳統模型之間的差異，以使用 Razor 視圖或頁面在 ASP.NET Core 應用程式中呈現 UI Razor 。</span><span class="sxs-lookup"><span data-stu-id="3d928-168">One way to understand Blazor Server apps is to understand how it differs from traditional models for rendering UI in ASP.NET Core apps using Razor views or Razor Pages.</span></span> <span data-ttu-id="3d928-169">這兩種模型都使用 [ Razor 語言](xref:mvc/views/razor)來描述要轉譯的 HTML 內容，但在轉譯標記的 *方式* 上會有很大的差異。</span><span class="sxs-lookup"><span data-stu-id="3d928-169">Both models use the [Razor language](xref:mvc/views/razor) to describe HTML content for rendering, but they significantly differ in *how* markup is rendered.</span></span>

<span data-ttu-id="3d928-170">轉譯 Razor 頁面或視圖時，每一行程式碼都會 Razor 以文字形式發出 HTML。</span><span class="sxs-lookup"><span data-stu-id="3d928-170">When a Razor Page or view is rendered, every line of Razor code emits HTML in text form.</span></span> <span data-ttu-id="3d928-171">轉譯之後，伺服器會處置頁面或視圖實例，包括任何已產生的狀態。</span><span class="sxs-lookup"><span data-stu-id="3d928-171">After rendering, the server disposes of the page or view instance, including any state that was produced.</span></span> <span data-ttu-id="3d928-172">當頁面出現另一個要求時，例如伺服器驗證失敗並顯示驗證摘要時：</span><span class="sxs-lookup"><span data-stu-id="3d928-172">When another request for the page occurs, for instance when server validation fails and the validation summary is displayed:</span></span>

* <span data-ttu-id="3d928-173">整個頁面會再次保存至 HTML 文字。</span><span class="sxs-lookup"><span data-stu-id="3d928-173">The entire page is rerendered to HTML text again.</span></span>
* <span data-ttu-id="3d928-174">頁面會傳送至用戶端。</span><span class="sxs-lookup"><span data-stu-id="3d928-174">The page is sent to the client.</span></span>

<span data-ttu-id="3d928-175">Blazor應用程式是由 UI 的可重複使用元素所組成，稱為「*元件*」。</span><span class="sxs-lookup"><span data-stu-id="3d928-175">A Blazor app is composed of reusable elements of UI called *components*.</span></span> <span data-ttu-id="3d928-176">元件包含 c # 程式碼、標記和其他元件。</span><span class="sxs-lookup"><span data-stu-id="3d928-176">A component contains C# code, markup, and other components.</span></span> <span data-ttu-id="3d928-177">轉譯元件時，會 Blazor 產生類似于 HTML 或 XML 檔物件模型 (DOM) 的包含元件圖形。</span><span class="sxs-lookup"><span data-stu-id="3d928-177">When a component is rendered, Blazor produces a graph of the included components similar to an HTML or XML Document Object Model (DOM).</span></span> <span data-ttu-id="3d928-178">此圖表包含屬性和欄位中保留的元件狀態。</span><span class="sxs-lookup"><span data-stu-id="3d928-178">This graph includes component state held in properties and fields.</span></span> <span data-ttu-id="3d928-179">Blazor 評估元件圖形，以產生標記的二進位標記法。</span><span class="sxs-lookup"><span data-stu-id="3d928-179">Blazor evaluates the component graph to produce a binary representation of the markup.</span></span> <span data-ttu-id="3d928-180">二進位格式可以是：</span><span class="sxs-lookup"><span data-stu-id="3d928-180">The binary format can be:</span></span>

* <span data-ttu-id="3d928-181">在倒轉) 期間轉換成 HTML 文字 (&dagger; 。</span><span class="sxs-lookup"><span data-stu-id="3d928-181">Turned into HTML text (during prerendering&dagger;).</span></span>
* <span data-ttu-id="3d928-182">用來在一般轉譯期間有效率地更新標記。</span><span class="sxs-lookup"><span data-stu-id="3d928-182">Used to efficiently update the markup during regular rendering.</span></span>

<span data-ttu-id="3d928-183">&dagger;預先 *轉譯：在* 伺服器上將所要求的 Razor 元件編譯為靜態 HTML，並傳送至用戶端，並將其呈現給使用者。</span><span class="sxs-lookup"><span data-stu-id="3d928-183">&dagger;*Prerendering*: The requested Razor component is compiled on the server into static HTML and sent to the client, where it's rendered to the user.</span></span> <span data-ttu-id="3d928-184">在用戶端與伺服器之間建立連線之後，元件的靜態資源清單元素會以互動式元素取代。</span><span class="sxs-lookup"><span data-stu-id="3d928-184">After the connection is made between the client and the server, the component's static prerendered elements are replaced with interactive elements.</span></span> <span data-ttu-id="3d928-185">進行可進行進行的工作，讓應用程式更能回應使用者。</span><span class="sxs-lookup"><span data-stu-id="3d928-185">Prerendering makes the app feel more responsive to the user.</span></span>

<span data-ttu-id="3d928-186">中的 UI 更新 Blazor 會由下列觸發：</span><span class="sxs-lookup"><span data-stu-id="3d928-186">A UI update in Blazor is triggered by:</span></span>

* <span data-ttu-id="3d928-187">使用者互動，例如選取按鈕。</span><span class="sxs-lookup"><span data-stu-id="3d928-187">User interaction, such as selecting a button.</span></span>
* <span data-ttu-id="3d928-188">應用程式觸發程式，例如計時器。</span><span class="sxs-lookup"><span data-stu-id="3d928-188">App triggers, such as a timer.</span></span>

<span data-ttu-id="3d928-189">元件圖形是保存，而且會計算 (差異) 的 UI *差異* 。</span><span class="sxs-lookup"><span data-stu-id="3d928-189">The component graph is rerendered, and a UI *diff* (difference) is calculated.</span></span> <span data-ttu-id="3d928-190">這項差異是在用戶端上更新 UI 所需的最小 DOM 編輯集合。</span><span class="sxs-lookup"><span data-stu-id="3d928-190">This diff is the smallest set of DOM edits required to update the UI on the client.</span></span> <span data-ttu-id="3d928-191">差異會以二進位格式傳送至用戶端，並由瀏覽器套用。</span><span class="sxs-lookup"><span data-stu-id="3d928-191">The diff is sent to the client in a binary format and applied by the browser.</span></span>

<span data-ttu-id="3d928-192">在使用者導覽用戶端上的元件之後，就會處置該元件。</span><span class="sxs-lookup"><span data-stu-id="3d928-192">A component is disposed after the user navigates away from it on the client.</span></span> <span data-ttu-id="3d928-193">當使用者與元件互動時，元件的狀態 (服務，) 的資源必須保存在伺服器的記憶體中。</span><span class="sxs-lookup"><span data-stu-id="3d928-193">While a user is interacting with a component, the component's state (services, resources) must be held in the server's memory.</span></span> <span data-ttu-id="3d928-194">由於許多元件的狀態可能會同時由伺服器維護，因此記憶體耗盡是必須解決的問題。</span><span class="sxs-lookup"><span data-stu-id="3d928-194">Because the state of many components might be maintained by the server concurrently, memory exhaustion is a concern that must be addressed.</span></span> <span data-ttu-id="3d928-195">如需如何撰寫 Blazor Server 應用程式以確保充分利用伺服器記憶體的指引，請參閱 <xref:blazor/security/server/threat-mitigation> 。</span><span class="sxs-lookup"><span data-stu-id="3d928-195">For guidance on how to author a Blazor Server app to ensure the best use of server memory, see <xref:blazor/security/server/threat-mitigation>.</span></span>

### <a name="circuits"></a><span data-ttu-id="3d928-196">電路</span><span class="sxs-lookup"><span data-stu-id="3d928-196">Circuits</span></span>

<span data-ttu-id="3d928-197">Blazor Server應用程式是以[ASP.NET Core SignalR ](xref:signalr/introduction)為基礎。</span><span class="sxs-lookup"><span data-stu-id="3d928-197">A Blazor Server app is built on top of [ASP.NET Core SignalR](xref:signalr/introduction).</span></span> <span data-ttu-id="3d928-198">每個用戶端會透過一或多個 SignalR 稱為 *線路* 的連線來與伺服器通訊。</span><span class="sxs-lookup"><span data-stu-id="3d928-198">Each client communicates to the server over one or more SignalR connections called a *circuit*.</span></span> <span data-ttu-id="3d928-199">線路是透過 Blazor SignalR 連接的抽象概念，可容忍暫時性的網路中斷。</span><span class="sxs-lookup"><span data-stu-id="3d928-199">A circuit is Blazor's abstraction over SignalR connections that can tolerate temporary network interruptions.</span></span> <span data-ttu-id="3d928-200">當 Blazor 用戶端看到 SignalR 連接已中斷連線時，就會嘗試使用新的連接重新連接到伺服器 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="3d928-200">When a Blazor client sees that the SignalR connection is disconnected, it attempts to reconnect to the server using a new SignalR connection.</span></span>

<span data-ttu-id="3d928-201">連線到應用程式的每個瀏覽器畫面 (瀏覽器] 索引標籤或 iframe) 都會 Blazor Server 使用 SignalR 連接。</span><span class="sxs-lookup"><span data-stu-id="3d928-201">Each browser screen (browser tab or iframe) that is connected to a Blazor Server app uses a SignalR connection.</span></span> <span data-ttu-id="3d928-202">相較于一般的伺服器轉譯應用程式，這也是另一項重要的區別。</span><span class="sxs-lookup"><span data-stu-id="3d928-202">This is yet another important distinction compared to typical server-rendered apps.</span></span> <span data-ttu-id="3d928-203">在伺服器呈現的應用程式中，在多個瀏覽器畫面中開啟相同的應用程式通常不會轉譯為伺服器上的其他資源需求。</span><span class="sxs-lookup"><span data-stu-id="3d928-203">In a server-rendered app, opening the same app in multiple browser screens typically doesn't translate into additional resource demands on the server.</span></span> <span data-ttu-id="3d928-204">在 Blazor Server 應用程式中，每個瀏覽器畫面都需要個別的線路，而個別的元件狀態實例則由伺服器管理。</span><span class="sxs-lookup"><span data-stu-id="3d928-204">In a Blazor Server app, each browser screen requires a separate circuit and separate instances of component state to be managed by the server.</span></span>

<span data-ttu-id="3d928-205">Blazor 考慮關閉瀏覽器索引標籤，或流覽至外部 URL 的 *正常* 終止。</span><span class="sxs-lookup"><span data-stu-id="3d928-205">Blazor considers closing a browser tab or navigating to an external URL a *graceful* termination.</span></span> <span data-ttu-id="3d928-206">當正常終止時，會立即釋出電路和相關聯的資源。</span><span class="sxs-lookup"><span data-stu-id="3d928-206">In the event of a graceful termination, the circuit and associated resources are immediately released.</span></span> <span data-ttu-id="3d928-207">用戶端也可能會因為網路中斷而無法正常地中斷連線。</span><span class="sxs-lookup"><span data-stu-id="3d928-207">A client may also disconnect non-gracefully, for instance due to a network interruption.</span></span> <span data-ttu-id="3d928-208">Blazor Server 儲存已中斷連線的線路以進行可設定的間隔，以允許用戶端重新連線。</span><span class="sxs-lookup"><span data-stu-id="3d928-208">Blazor Server stores disconnected circuits for a configurable interval to allow the client to reconnect.</span></span>

<span data-ttu-id="3d928-209">Blazor Server 允許程式碼定義迴圈 *處理常式*，以允許在使用者的線路狀態變更時執行程式碼。</span><span class="sxs-lookup"><span data-stu-id="3d928-209">Blazor Server allows code to define a *circuit handler*, which allows running code on changes to the state of a user's circuit.</span></span> <span data-ttu-id="3d928-210">如需詳細資訊，請參閱<xref:blazor/advanced-scenarios#blazor-server-circuit-handler>。</span><span class="sxs-lookup"><span data-stu-id="3d928-210">For more information, see <xref:blazor/advanced-scenarios#blazor-server-circuit-handler>.</span></span>

### <a name="ui-latency"></a><span data-ttu-id="3d928-211">UI 延遲</span><span class="sxs-lookup"><span data-stu-id="3d928-211">UI Latency</span></span>

<span data-ttu-id="3d928-212">UI 延遲是從起始的動作到 UI 更新時間所花費的時間。</span><span class="sxs-lookup"><span data-stu-id="3d928-212">UI latency is the time it takes from an initiated action to the time the UI is updated.</span></span> <span data-ttu-id="3d928-213">較小的 UI 延遲值是應用程式對使用者有回應的必要值。</span><span class="sxs-lookup"><span data-stu-id="3d928-213">Smaller values for UI latency are imperative for an app to feel responsive to a user.</span></span> <span data-ttu-id="3d928-214">在 Blazor Server 應用程式中，會將每個動作傳送到伺服器、處理，然後將 UI 差異傳送回來。</span><span class="sxs-lookup"><span data-stu-id="3d928-214">In a Blazor Server app, each action is sent to the server, processed, and a UI diff is sent back.</span></span> <span data-ttu-id="3d928-215">因此，UI 延遲是網路延遲的總和，以及處理動作的伺服器延遲。</span><span class="sxs-lookup"><span data-stu-id="3d928-215">Consequently, UI latency is the sum of network latency and the server latency in processing the action.</span></span>

<span data-ttu-id="3d928-216">對於僅限私用公司網路的商務應用程式，因為網路延遲的緣故，影響使用者對延遲的影響通常是 imperceptible。</span><span class="sxs-lookup"><span data-stu-id="3d928-216">For a business app that's limited to a private corporate network, the effect on user perceptions of latency due to network latency are usually imperceptible.</span></span> <span data-ttu-id="3d928-217">針對透過網際網路部署的應用程式，使用者可能會對使用者造成延遲，特別是在地理位置分散的使用者。</span><span class="sxs-lookup"><span data-stu-id="3d928-217">For an app deployed over the Internet, latency may become noticeable to users, particularly if users are widely distributed geographically.</span></span>

<span data-ttu-id="3d928-218">記憶體使用量也有助於應用程式延遲。</span><span class="sxs-lookup"><span data-stu-id="3d928-218">Memory usage can also contribute to app latency.</span></span> <span data-ttu-id="3d928-219">記憶體使用量增加會導致頻繁的垃圾收集或將記憶體分頁到磁片，而這兩者都會降低應用程式效能，進而增加 UI 延遲。</span><span class="sxs-lookup"><span data-stu-id="3d928-219">Increased memory usage results in frequent garbage collection or paging memory to disk, both of which degrade app performance and consequently increase UI latency.</span></span>

<span data-ttu-id="3d928-220">Blazor Server 應用程式應該經過優化，藉由減少網路延遲和記憶體使用量來將 UI 延遲降至最低。</span><span class="sxs-lookup"><span data-stu-id="3d928-220">Blazor Server apps should be optimized to minimize UI latency by reducing network latency and memory usage.</span></span> <span data-ttu-id="3d928-221">如需測量網路延遲的方法，請參閱 <xref:blazor/host-and-deploy/server#measure-network-latency> 。</span><span class="sxs-lookup"><span data-stu-id="3d928-221">For an approach to measuring network latency, see <xref:blazor/host-and-deploy/server#measure-network-latency>.</span></span> <span data-ttu-id="3d928-222">如需和的詳細資訊 SignalR Blazor ，請參閱：</span><span class="sxs-lookup"><span data-stu-id="3d928-222">For more information on SignalR and Blazor, see:</span></span>

* <xref:blazor/host-and-deploy/server>
* <xref:blazor/security/server/threat-mitigation>

### <a name="connection-to-the-server"></a><span data-ttu-id="3d928-223">連接到伺服器</span><span class="sxs-lookup"><span data-stu-id="3d928-223">Connection to the server</span></span>

<span data-ttu-id="3d928-224">Blazor Server 應用程式需要使用中 SignalR 的伺服器連接。</span><span class="sxs-lookup"><span data-stu-id="3d928-224">Blazor Server apps require an active SignalR connection to the server.</span></span> <span data-ttu-id="3d928-225">如果連線中斷，應用程式會嘗試重新連線到伺服器。</span><span class="sxs-lookup"><span data-stu-id="3d928-225">If the connection is lost, the app attempts to reconnect to the server.</span></span> <span data-ttu-id="3d928-226">只要用戶端的狀態保留在伺服器的記憶體中，用戶端會話就會繼續，而不會遺失狀態。</span><span class="sxs-lookup"><span data-stu-id="3d928-226">As long as the client's state remains in the server's memory, the client session resumes without losing state.</span></span>

<span data-ttu-id="3d928-227">Blazor Server應用程式會 prerenders 以回應第一個用戶端要求，這會在伺服器上建立 UI 狀態。</span><span class="sxs-lookup"><span data-stu-id="3d928-227">A Blazor Server app prerenders in response to the first client request, which creates the UI state on the server.</span></span> <span data-ttu-id="3d928-228">當用戶端嘗試建立連接時 SignalR ，用戶端必須重新連接到相同的伺服器。</span><span class="sxs-lookup"><span data-stu-id="3d928-228">When the client attempts to create a SignalR connection, the client must reconnect to the same server.</span></span> <span data-ttu-id="3d928-229">Blazor Server 使用多部後端伺服器的應用程式應該執行連線的「 *粘滯話」會話* SignalR 。</span><span class="sxs-lookup"><span data-stu-id="3d928-229">Blazor Server apps that use more than one backend server should implement *sticky sessions* for SignalR connections.</span></span>

<span data-ttu-id="3d928-230">我們建議使用 [Azure SignalR Service](/azure/azure-signalr) for Blazor Server apps。</span><span class="sxs-lookup"><span data-stu-id="3d928-230">We recommend using the [Azure SignalR Service](/azure/azure-signalr) for Blazor Server apps.</span></span> <span data-ttu-id="3d928-231">服務可讓您將 Blazor Server 應用程式相應增加為大量的並行 SignalR 連接。</span><span class="sxs-lookup"><span data-stu-id="3d928-231">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="3d928-232">您 SignalR 可以將服務的選項或設定值設定為，以啟用 Azure 服務的「粘滯話」 `ServerStickyMode` `Required` 。</span><span class="sxs-lookup"><span data-stu-id="3d928-232">Sticky sessions are enabled for the Azure SignalR Service by setting the service's `ServerStickyMode` option or configuration value to `Required`.</span></span> <span data-ttu-id="3d928-233">如需詳細資訊，請參閱<xref:blazor/host-and-deploy/server#signalr-configuration>。</span><span class="sxs-lookup"><span data-stu-id="3d928-233">For more information, see <xref:blazor/host-and-deploy/server#signalr-configuration>.</span></span>

<span data-ttu-id="3d928-234">使用 IIS 時，會使用 *應用程式要求路由* 來啟用「粘滯話」。</span><span class="sxs-lookup"><span data-stu-id="3d928-234">When using IIS, sticky sessions are enabled with *Application Request Routing*.</span></span> <span data-ttu-id="3d928-235">如需詳細資訊，請參閱 [使用應用程式要求路由的 HTTP 負載平衡](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)。</span><span class="sxs-lookup"><span data-stu-id="3d928-235">For more information, see [HTTP Load Balancing using Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3d928-236">其他資源</span><span class="sxs-lookup"><span data-stu-id="3d928-236">Additional resources</span></span>

* <xref:blazor/tooling>
* <xref:signalr/introduction>
* <xref:blazor/fundamentals/additional-scenarios>
* <xref:tutorials/signalr-blazor>
