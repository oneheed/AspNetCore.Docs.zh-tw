---
title: ASP.NET 核心 Blazor 裝載模型
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
ms.openlocfilehash: 8dd11251358bbeea444661970fadf19cb1390fd3
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2021
ms.locfileid: "102394924"
---
# <a name="aspnet-core-blazor-hosting-models"></a>ASP.NET 核心 Blazor 裝載模型

Blazor 是一種 web 架構，設計用來在瀏覽器中以 [WebAssembly](https://webassembly.org/)為基礎的 .net 執行時間執行用戶端 (*Blazor WebAssembly*) 或 ASP.NET Core () 中的伺服器端 *Blazor Server* 。 無論裝載模型為何，應用程式和元件模型 *都相同*。

## Blazor WebAssembly

主要 Blazor 裝載模型正在 WebAssembly 的瀏覽器中執行用戶端。 Blazor應用程式、其相依性和 .net 執行時間會下載至瀏覽器。 應用程式會直接在瀏覽器 UI 執行緒上執行。 UI 更新和事件處理會在相同的進程中進行。 應用程式的資產會以靜態檔案的形式部署至 web 伺服器或服務，以提供靜態內容給用戶端。

![：：：非 loc (Blazor WebAssembly) ：：：：：：：非 loc (Blazor) ：：：應用程式會在瀏覽器內的 UI 執行緒上執行。](hosting-models/_static/blazor-webassembly.png)

Blazor WebAssembly建立應用程式以進行部署，而不使用後端 ASP.NET 核心應用程式來提供其檔案，則應用程式稱為 *獨立* Blazor WebAssembly 應用程式。 建立應用程式以供使用後端應用程式進行部署以提供其檔案時，應用程式稱為 *託管* Blazor WebAssembly 應用程式。 託管 Blazor WebAssembly **`Client`** 應用程式通常會 **`Server`** 使用 web API 呼叫或 () ，透過網路與後端應用程式互動 [SignalR](xref:signalr/introduction) <xref:tutorials/signalr-blazor> 。

`blazor.webassembly.js`腳本是由架構所提供，並處理：

* 下載 .NET 執行時間、應用程式和應用程式的相依性。
* 要執行應用程式的執行時間初始化。

Blazor WebAssembly裝載模型提供幾項優點：

* 沒有 .NET 伺服器端相依性。 應用程式在下載至用戶端之後可完全正常運作。
* 用戶端資源和功能已完全運用。
* 工作會從伺服器卸載至用戶端。
* 裝載應用程式不需要 ASP.NET 核心網頁伺服器。 無伺服器部署案例是可行的，例如，從內容傳遞網路提供應用程式 (CDN) 。

Blazor WebAssembly裝載模型有下列限制：

* 應用程式僅限於瀏覽器的功能。
* 支援的用戶端硬體和軟體 (例如，需要 WebAssembly 支援) 。
* 下載大小更大，而應用程式需要較長的時間才能載入。
* .NET 執行時間和工具支援較不成熟。 例如，限制存在於 [.Net Standard](/dotnet/standard/net-standard) 支援和偵錯工具中。

若要建立 Blazor WebAssembly 應用程式，請參閱 <xref:blazor/tooling> 。

託管 Blazor 應用程式模型支援 [Docker 容器](/dotnet/standard/microservices-architecture/container-docker-introduction/index)。 如需 Visual Studio 中的 Docker 支援，請以滑鼠右鍵按一下 **`Server`** 託管解決方案的專案， Blazor WebAssembly 然後選取 [**新增**  >  **Docker 支援**]。

## Blazor Server

使用 Blazor Server 裝載模型時，會在伺服器上從 ASP.NET Core 應用程式中執行應用程式。 UI 更新、事件處理及 JavaScript 呼叫會透過連接來處理 [SignalR](xref:signalr/introduction) 。

![瀏覽器會與應用程式進行互動 (裝載在伺服器上的 ASP.NET Core 應用程式) 中，而不是透過：：：無 loc (SignalR) ：：： connection。](hosting-models/_static/blazor-server.png)

ASP.NET Core 應用程式會參考要新增的應用程式 `Startup` 類別：

* 伺服器端服務。
* 要求處理管線的應用程式。

在用戶端上， `blazor.server.js` 腳本會建立 SignalR 與伺服器之間的連接。 腳本是從 ASP.NET Core 共用架構中的內嵌資源提供給用戶端應用程式。 用戶端應用程式會視需要負責保存和還原應用程式狀態。 

Blazor Server裝載模型提供幾項優點：

* 下載大小遠小於 Blazor WebAssembly 應用程式，且應用程式的載入速度更快。
* 應用程式會充分利用伺服器功能，包括使用任何 .NET Core 相容的 Api。
* 伺服器上的 .NET Core 可用來執行應用程式，因此現有的 .NET 工具（例如偵錯工具）會如預期般運作。
* 支援瘦用戶端。 例如， Blazor Server 應用程式會使用不支援 WebAssembly 和資源受限裝置的瀏覽器。
* 應用程式的 .NET/c # 程式碼基底（包括應用程式的元件程式碼）不會提供給用戶端。

> [!IMPORTANT]
> Blazor Server應用程式會 prerenders 以回應第一個用戶端要求，這會在伺服器上建立 UI 狀態。 當用戶端嘗試建立連接時 SignalR ， **用戶端必須重新連接到相同的伺服器**。 Blazor Server 使用多部後端伺服器的應用程式應該執行連線的「 *粘滯話」會話* SignalR 。 如需詳細資訊，請參閱 [伺服器的連接](#connection-to-the-server) 一節。

Blazor Server裝載模型有下列限制：

* 通常會有較高的延遲。 每個使用者互動都牽涉到網路躍點。
* 沒有離線支援。 如果用戶端連接失敗，應用程式就會停止運作。
* 針對具有許多使用者的應用程式，擴充性相當困難。 伺服器必須管理多個用戶端連接並處理用戶端狀態。
* 需要 ASP.NET 核心伺服器才能提供應用程式。 無法進行無伺服器部署案例，例如，從內容傳遞網路提供應用程式 (CDN) 。

若要建立 Blazor Server 應用程式，請參閱 <xref:blazor/tooling> 。

Blazor Server應用程式模型支援[Docker 容器](/dotnet/standard/microservices-architecture/container-docker-introduction/index)。 如需 visual studio 中的 Docker 支援，請在 visual studio 中的專案上按一下滑鼠右鍵，然後選取 [**新增**  >  **Docker 支援**]。

### <a name="comparison-to-server-rendered-ui"></a>與伺服器呈現的 UI 比較

瞭解應用程式的其中一種方式 Blazor Server ，就是了解它與傳統模型之間的差異，以便在 ASP.NET Core 應用程式中使用 Razor 視圖或頁面呈現 UI Razor 。 這兩種模型都使用 [ Razor 語言](xref:mvc/views/razor)來描述要轉譯的 HTML 內容，但在轉譯標記的 *方式* 上會有很大的差異。

轉譯 Razor 頁面或視圖時，每一行程式碼都會 Razor 以文字形式發出 HTML。 轉譯之後，伺服器會處置頁面或視圖實例，包括任何已產生的狀態。 當頁面出現另一個要求時，例如伺服器驗證失敗並顯示驗證摘要時：

* 整個頁面會再次保存至 HTML 文字。
* 頁面會傳送至用戶端。

Blazor應用程式是由 UI 的可重複使用元素所組成，稱為「*元件*」。 元件包含 c # 程式碼、標記和其他元件。 轉譯元件時，會 Blazor 產生類似于 HTML 或 XML 檔物件模型的包含元件圖形 (DOM) 。 此圖表包含屬性和欄位中保留的元件狀態。 Blazor 評估元件圖形，以產生標記的二進位標記法。 二進位格式可以是：

* 在倒轉) 期間轉換成 HTML 文字 (&dagger; 。
* 用來在一般轉譯期間有效率地更新標記。

&dagger;預先 *轉譯：在* 伺服器上將所要求的 Razor 元件編譯為靜態 HTML，並傳送至用戶端，並將其呈現給使用者。 在用戶端與伺服器之間建立連線之後，元件的靜態資源清單元素會以互動式元素取代。 進行可進行進行的工作，讓應用程式更能回應使用者。

中的 UI 更新 Blazor 會由下列觸發：

* 使用者互動，例如選取按鈕。
* 應用程式觸發程式，例如計時器。

元件圖形是保存，而且會計算 (差異) 的 UI *差異* 。 這項差異是在用戶端上更新 UI 所需的最小 DOM 編輯集合。 差異會以二進位格式傳送至用戶端，並由瀏覽器套用。

在使用者導覽用戶端上的元件之後，就會處置該元件。 當使用者與元件互動時，元件的狀態 (服務，) 的資源必須保存在伺服器的記憶體中。 由於許多元件的狀態可能會同時由伺服器維護，因此記憶體耗盡是必須解決的問題。 如需如何撰寫 Blazor Server 應用程式以確保充分利用伺服器記憶體的指引，請參閱 <xref:blazor/security/server/threat-mitigation> 。

### <a name="circuits"></a>電路

Blazor Server應用程式是以[ASP.NET Core SignalR ](xref:signalr/introduction)為基礎。 每個用戶端會透過一或多個 SignalR 稱為 *線路* 的連線來與伺服器通訊。 線路是透過 Blazor SignalR 連接的抽象概念，可容忍暫時性的網路中斷。 當 Blazor 用戶端看到 SignalR 連接已中斷連線時，就會嘗試使用新的連接重新連接到伺服器 SignalR 。

連線到應用程式的每個瀏覽器畫面 (瀏覽器] 索引標籤或 iframe) 都會 Blazor Server 使用 SignalR 連接。 相較于一般的伺服器轉譯應用程式，這也是另一項重要的區別。 在伺服器呈現的應用程式中，在多個瀏覽器畫面中開啟相同的應用程式通常不會轉譯為伺服器上的其他資源需求。 在 Blazor Server 應用程式中，每個瀏覽器畫面都需要個別的線路，而個別的元件狀態實例則由伺服器管理。

Blazor 考慮關閉瀏覽器索引標籤，或流覽至外部 URL 的 *正常* 終止。 當正常終止時，會立即釋出電路和相關聯的資源。 用戶端也可能會因為網路中斷而無法正常地中斷連線。 Blazor Server 儲存已中斷連線的線路以進行可設定的間隔，以允許用戶端重新連線。

Blazor Server 允許程式碼定義迴圈 *處理常式*，以允許在使用者的線路狀態變更時執行程式碼。 如需詳細資訊，請參閱<xref:blazor/advanced-scenarios#blazor-server-circuit-handler>。

### <a name="ui-latency"></a>UI 延遲

UI 延遲是從起始的動作到 UI 更新時間所花費的時間。 較小的 UI 延遲值是應用程式對使用者有回應的必要值。 在 Blazor Server 應用程式中，會將每個動作傳送到伺服器、處理，然後將 UI 差異傳送回來。 因此，UI 延遲是網路延遲的總和，以及處理動作的伺服器延遲。

對於僅限私用公司網路的商務應用程式，因為網路延遲的緣故，影響使用者對延遲的影響通常是 imperceptible。 針對透過網際網路部署的應用程式，使用者可能會對使用者造成延遲，特別是在地理位置分散的使用者。

記憶體使用量也有助於應用程式延遲。 記憶體使用量增加會導致頻繁的垃圾收集或將記憶體分頁到磁片，而這兩者都會降低應用程式效能，進而增加 UI 延遲。

Blazor Server 應用程式應該經過優化，藉由減少網路延遲和記憶體使用量來將 UI 延遲降至最低。 如需測量網路延遲的方法，請參閱 <xref:blazor/host-and-deploy/server#measure-network-latency> 。 如需和的詳細資訊 SignalR Blazor ，請參閱：

* <xref:blazor/host-and-deploy/server>
* <xref:blazor/security/server/threat-mitigation>

### <a name="connection-to-the-server"></a>連接到伺服器

Blazor Server 應用程式需要使用中 SignalR 的伺服器連接。 如果連線中斷，應用程式會嘗試重新連線到伺服器。 只要用戶端的狀態保留在伺服器的記憶體中，用戶端會話就會繼續，而不會遺失狀態。

Blazor Server應用程式會 prerenders 以回應第一個用戶端要求，這會在伺服器上建立 UI 狀態。 當用戶端嘗試建立連接時 SignalR ，用戶端必須重新連接到相同的伺服器。 Blazor Server 使用多部後端伺服器的應用程式應該執行連線的「 *粘滯話」會話* SignalR 。

我們建議使用 [Azure SignalR Service](/azure/azure-signalr) for Blazor Server apps。 服務可讓您將 Blazor Server 應用程式相應增加為大量的並行 SignalR 連接。 您 SignalR 可以將服務的選項或設定值設定為，以啟用 Azure 服務的「粘滯話」 `ServerStickyMode` `Required` 。 如需詳細資訊，請參閱<xref:blazor/host-and-deploy/server#signalr-configuration>。

使用 IIS 時，會使用 *應用程式要求路由* 來啟用「粘滯話」。 如需詳細資訊，請參閱 [使用應用程式要求路由的 HTTP 負載平衡](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)。

## <a name="additional-resources"></a>其他資源

* <xref:blazor/tooling>
* <xref:blazor/project-structure>
* <xref:signalr/introduction>
* <xref:blazor/fundamentals/signalr>
* <xref:tutorials/signalr-blazor>
