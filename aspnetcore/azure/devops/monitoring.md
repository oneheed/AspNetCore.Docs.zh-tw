---
title: 使用 ASP.NET Core 和 Azure 進行監視和調試 DevOps
author: CamSoper
description: 使用 ASP.NET Core 和 Azure 以 DevOps 解決方案的一部分監視和偵錯工具代碼
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18
ms.date: 07/10/2019
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
uid: azure/devops/monitor
ms.openlocfilehash: 74e789828bf5d54e3457f235657f8ed7086df80d
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "93056747"
---
# <a name="monitor-and-debug"></a><span data-ttu-id="88173-103">監視和調試</span><span class="sxs-lookup"><span data-stu-id="88173-103">Monitor and debug</span></span>

<span data-ttu-id="88173-104">部署應用程式並建立 DevOps 管線之後，請務必瞭解如何監視應用程式並對其進行疑難排解。</span><span class="sxs-lookup"><span data-stu-id="88173-104">Having deployed the app and built a DevOps pipeline, it's important to understand how to monitor and troubleshoot the app.</span></span>

<span data-ttu-id="88173-105">在本節中，您將完成下列工作：</span><span class="sxs-lookup"><span data-stu-id="88173-105">In this section, you'll complete the following tasks:</span></span>

* <span data-ttu-id="88173-106">在 Azure 入口網站中尋找基本的監視和疑難排解資料</span><span class="sxs-lookup"><span data-stu-id="88173-106">Find basic monitoring and troubleshooting data in the Azure portal</span></span>
* <span data-ttu-id="88173-107">瞭解 Azure 監視器如何讓您更深入瞭解所有 Azure 服務之間的計量</span><span class="sxs-lookup"><span data-stu-id="88173-107">Learn how Azure Monitor provides a deeper look at metrics across all Azure services</span></span>
* <span data-ttu-id="88173-108">將 web 應用程式與 Application Insights 連線以進行應用程式分析</span><span class="sxs-lookup"><span data-stu-id="88173-108">Connect the web app with Application Insights for app profiling</span></span>
* <span data-ttu-id="88173-109">開啟記錄並瞭解下載記錄的位置</span><span class="sxs-lookup"><span data-stu-id="88173-109">Turn on logging and learn where to download logs</span></span>
* <span data-ttu-id="88173-110">即時串流記錄</span><span class="sxs-lookup"><span data-stu-id="88173-110">Stream logs in real time</span></span>
* <span data-ttu-id="88173-111">瞭解設定警示的位置</span><span class="sxs-lookup"><span data-stu-id="88173-111">Learn where to set up alerts</span></span>
* <span data-ttu-id="88173-112">瞭解如何 Azure App Service web 應用程式進行遠端偵錯程式。</span><span class="sxs-lookup"><span data-stu-id="88173-112">Learn about remote debugging Azure App Service web apps.</span></span>

## <a name="basic-monitoring-and-troubleshooting"></a><span data-ttu-id="88173-113">基本監視與疑難排解</span><span class="sxs-lookup"><span data-stu-id="88173-113">Basic monitoring and troubleshooting</span></span>

<span data-ttu-id="88173-114">App Service 的 web 應用程式可即時輕鬆監視。</span><span class="sxs-lookup"><span data-stu-id="88173-114">App Service web apps are easily monitored in real time.</span></span> <span data-ttu-id="88173-115">Azure 入口網站會以易於瞭解的圖表和圖形呈現度量。</span><span class="sxs-lookup"><span data-stu-id="88173-115">The Azure portal renders metrics in easy-to-understand charts and graphs.</span></span>

1. <span data-ttu-id="88173-116">開啟 [Azure 入口網站](https://portal.azure.com)，然後流覽至 [ *mywebapp \<unique_number\>* ] App Service。</span><span class="sxs-lookup"><span data-stu-id="88173-116">Open the [Azure portal](https://portal.azure.com), and then navigate to the *mywebapp\<unique_number\>* App Service.</span></span>

1. <span data-ttu-id="88173-117">[ **總覽** ] 索引標籤會顯示有用的「一覽」資訊，包括顯示最近計量的圖表。</span><span class="sxs-lookup"><span data-stu-id="88173-117">The **Overview** tab displays useful "at-a-glance" information, including graphs displaying recent metrics.</span></span>

    ![顯示總覽面板的螢幕擷取畫面](./media/monitoring/overview.png)

    * <span data-ttu-id="88173-119">**Http 5xx**：伺服器端錯誤的計數，通常是 ASP.NET Core 程式碼中的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="88173-119">**Http 5xx**: Count of server-side errors, usually exceptions in ASP.NET Core code.</span></span>
    * <span data-ttu-id="88173-120">**資料** 輸入：進入您 web 應用程式的資料輸入。</span><span class="sxs-lookup"><span data-stu-id="88173-120">**Data In**: Data ingress coming into your web app.</span></span>
    * <span data-ttu-id="88173-121">**資料輸出**：從 web 應用程式到用戶端的資料輸出。</span><span class="sxs-lookup"><span data-stu-id="88173-121">**Data Out**: Data egress from your web app to clients.</span></span>
    * <span data-ttu-id="88173-122">**要求**： HTTP 要求的計數。</span><span class="sxs-lookup"><span data-stu-id="88173-122">**Requests**: Count of HTTP requests.</span></span>
    * <span data-ttu-id="88173-123">**平均回應時間**： web 應用程式回應 HTTP 要求的平均時間。</span><span class="sxs-lookup"><span data-stu-id="88173-123">**Average Response Time**: Average time for the web app to respond to HTTP requests.</span></span>

    <span data-ttu-id="88173-124">您也可以在此頁面上找到數個用於疑難排解和優化的自助工具。</span><span class="sxs-lookup"><span data-stu-id="88173-124">Several self-service tools for troubleshooting and optimization are also found on this page.</span></span>

    ![顯示自助工具的螢幕擷取畫面](./media/monitoring/wizards.png)

    * <span data-ttu-id="88173-126">**診斷和解決問題** 是自助服務疑難排解員。</span><span class="sxs-lookup"><span data-stu-id="88173-126">**Diagnose and solve problems** is a self-service troubleshooter.</span></span>
    * <span data-ttu-id="88173-127">**Application Insights** 適用于分析效能和應用程式行為，並將在本節稍後討論。</span><span class="sxs-lookup"><span data-stu-id="88173-127">**Application Insights** is for profiling performance and app behavior, and is discussed later in this section.</span></span>
    * <span data-ttu-id="88173-128">**App Service Advisor** 提出建議來調整您的應用程式體驗。</span><span class="sxs-lookup"><span data-stu-id="88173-128">**App Service Advisor** makes recommendations to tune your app experience.</span></span>

## <a name="advanced-monitoring"></a><span data-ttu-id="88173-129">進階監視</span><span class="sxs-lookup"><span data-stu-id="88173-129">Advanced monitoring</span></span>

<span data-ttu-id="88173-130">[Azure 監視器](/azure/monitoring-and-diagnostics/) 是集中式服務，可監視所有計量，並在 Azure 服務中設定警示。</span><span class="sxs-lookup"><span data-stu-id="88173-130">[Azure Monitor](/azure/monitoring-and-diagnostics/) is the centralized service for monitoring all metrics and setting alerts across Azure services.</span></span> <span data-ttu-id="88173-131">在 Azure 監視器中，系統管理員可以更精細地追蹤效能並找出趨勢。</span><span class="sxs-lookup"><span data-stu-id="88173-131">Within Azure Monitor, administrators can granularly track performance and identify trends.</span></span> <span data-ttu-id="88173-132">每個 Azure 服務都提供自己 [的一組計量](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) 來 Azure 監視器。</span><span class="sxs-lookup"><span data-stu-id="88173-132">Each Azure service offers its own [set of metrics](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) to Azure Monitor.</span></span>

## <a name="profile-with-application-insights"></a><span data-ttu-id="88173-133">使用 Application Insights 設定檔</span><span class="sxs-lookup"><span data-stu-id="88173-133">Profile with Application Insights</span></span>

<span data-ttu-id="88173-134">[Application Insights](/azure/application-insights/app-insights-overview) 是一項 Azure 服務，用來分析 web 應用程式的效能和穩定性，以及使用者使用這些應用程式的方式。</span><span class="sxs-lookup"><span data-stu-id="88173-134">[Application Insights](/azure/application-insights/app-insights-overview) is an Azure service for analyzing the performance and stability of web apps and how users use them.</span></span> <span data-ttu-id="88173-135">Application Insights 的資料比 Azure 監視器更廣泛且更深入。</span><span class="sxs-lookup"><span data-stu-id="88173-135">The data from Application Insights is broader and deeper than that of Azure Monitor.</span></span> <span data-ttu-id="88173-136">資料可以為開發人員和系統管理員提供重要資訊，以改善應用程式。</span><span class="sxs-lookup"><span data-stu-id="88173-136">The data can provide developers and administrators with key information for improving apps.</span></span> <span data-ttu-id="88173-137">Application Insights 可以新增至 Azure App Service 的資源，而不需要變更程式碼。</span><span class="sxs-lookup"><span data-stu-id="88173-137">Application Insights can be added to an Azure App Service resource without code changes.</span></span>

1. <span data-ttu-id="88173-138">開啟 [Azure 入口網站](https://portal.azure.com)，然後流覽至 [ *mywebapp \<unique_number\>* ] App Service。</span><span class="sxs-lookup"><span data-stu-id="88173-138">Open the [Azure portal](https://portal.azure.com), and then navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="88173-139">在 [ **總覽** ] 索引標籤中，按一下 [ **Application Insights** ] 磚。</span><span class="sxs-lookup"><span data-stu-id="88173-139">From the **Overview** tab, click the **Application Insights** tile.</span></span>

    ![Application Insights 圖格](./media/monitoring/app-insights.png)

1. <span data-ttu-id="88173-141">選取 [ **建立新資源** ] 選項按鈕。</span><span class="sxs-lookup"><span data-stu-id="88173-141">Select the **Create new resource** radio button.</span></span> <span data-ttu-id="88173-142">使用預設的資源名稱，然後選取 Application Insights 資源的位置。</span><span class="sxs-lookup"><span data-stu-id="88173-142">Use the default resource name, and select the location for the Application Insights resource.</span></span> <span data-ttu-id="88173-143">此位置不需要與您的 web 應用程式相符。</span><span class="sxs-lookup"><span data-stu-id="88173-143">The location doesn't need to match that of your web app.</span></span>

    ![Application Insights 設定](./media/monitoring/new-app-insights.png)

1. <span data-ttu-id="88173-145">針對 **執行時間/架構**，請選取 [ **ASP.NET Core**]。</span><span class="sxs-lookup"><span data-stu-id="88173-145">For **Runtime/Framework**, select **ASP.NET Core**.</span></span> <span data-ttu-id="88173-146">接受預設設定。</span><span class="sxs-lookup"><span data-stu-id="88173-146">Accept the default settings.</span></span>
1. <span data-ttu-id="88173-147">選取 [確定]。</span><span class="sxs-lookup"><span data-stu-id="88173-147">Select **OK**.</span></span> <span data-ttu-id="88173-148">如果系統提示您確認，請選取 [ **繼續**]。</span><span class="sxs-lookup"><span data-stu-id="88173-148">If prompted to confirm, select **Continue**.</span></span>
1. <span data-ttu-id="88173-149">建立資源之後，請按一下 Application Insights 資源的名稱，直接流覽至 Application Insights 頁面。</span><span class="sxs-lookup"><span data-stu-id="88173-149">After the resource has been created, click the name of Application Insights resource to navigate directly to the Application Insights page.</span></span>

    ![已備妥新的 Application Insights 資源](./media/monitoring/new-app-insights-done.png)

<span data-ttu-id="88173-151">使用應用程式時，資料會累積。</span><span class="sxs-lookup"><span data-stu-id="88173-151">As the app is used, data accumulates.</span></span> <span data-ttu-id="88173-152">選取 **[** 重新整理]，以新資料重載該分頁。</span><span class="sxs-lookup"><span data-stu-id="88173-152">Select **Refresh** to reload the blade with new data.</span></span>

![Application Insights 總覽] 索引標籤](./media/monitoring/app-insights-overview.png)

<span data-ttu-id="88173-154">Application Insights 提供有用的伺服器端資訊，不需額外設定。</span><span class="sxs-lookup"><span data-stu-id="88173-154">Application Insights provides useful server-side information with no additional configuration.</span></span> <span data-ttu-id="88173-155">若要充分利用 Application Insights 的最大價值，請 [使用 APPLICATION INSIGHTS SDK 來檢測您的應用程式](/azure/application-insights/app-insights-asp-net-core)。</span><span class="sxs-lookup"><span data-stu-id="88173-155">To get the most value from Application Insights, [instrument your app with the Application Insights SDK](/azure/application-insights/app-insights-asp-net-core).</span></span> <span data-ttu-id="88173-156">如果設定正確，服務會在 web 伺服器和瀏覽器之間提供端對端監視，包括用戶端效能。</span><span class="sxs-lookup"><span data-stu-id="88173-156">When properly configured, the service provides end-to-end monitoring across the web server and browser, including client-side performance.</span></span> <span data-ttu-id="88173-157">如需詳細資訊，請參閱 [Application Insights 檔](/azure/application-insights/app-insights-overview)。</span><span class="sxs-lookup"><span data-stu-id="88173-157">For more information, see the [Application Insights documentation](/azure/application-insights/app-insights-overview).</span></span>

## <a name="logging"></a><span data-ttu-id="88173-158">記錄</span><span class="sxs-lookup"><span data-stu-id="88173-158">Logging</span></span>

<span data-ttu-id="88173-159">在 Azure App Service 中，預設會停用 Web 服務器和應用程式記錄檔。</span><span class="sxs-lookup"><span data-stu-id="88173-159">Web server and app logs are disabled by default in Azure App Service.</span></span> <span data-ttu-id="88173-160">使用下列步驟啟用記錄：</span><span class="sxs-lookup"><span data-stu-id="88173-160">Enable the logs with the following steps:</span></span>

1. <span data-ttu-id="88173-161">開啟 [Azure 入口網站](https://portal.azure.com)，然後流覽至 *mywebapp \<unique_number\>* App Service。</span><span class="sxs-lookup"><span data-stu-id="88173-161">Open the [Azure portal](https://portal.azure.com), and navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="88173-162">在左側功能表中，向下滾動至 [ **監視** ] 區段。</span><span class="sxs-lookup"><span data-stu-id="88173-162">In the menu to the left, scroll down to the **Monitoring** section.</span></span> <span data-ttu-id="88173-163">選取 [ **診斷記錄**]。</span><span class="sxs-lookup"><span data-stu-id="88173-163">Select **Diagnostics logs**.</span></span>

    ![診斷記錄連結](./media/monitoring/logging.png)

1. <span data-ttu-id="88173-165">開啟 **應用程式記錄 (檔案系統)**。</span><span class="sxs-lookup"><span data-stu-id="88173-165">Turn on **Application Logging (Filesystem)**.</span></span> <span data-ttu-id="88173-166">如果出現提示，請按一下方塊以安裝延伸模組，以在 web 應用程式中啟用應用程式記錄。</span><span class="sxs-lookup"><span data-stu-id="88173-166">If prompted, click the box to install the extensions to enable app logging in the web app.</span></span>
1. <span data-ttu-id="88173-167">將 **網頁伺服器記錄** 設定為 **檔案系統**。</span><span class="sxs-lookup"><span data-stu-id="88173-167">Set **Web server logging** to **File System**.</span></span>
1. <span data-ttu-id="88173-168">輸入 **保留期限** （以天為單位）。</span><span class="sxs-lookup"><span data-stu-id="88173-168">Enter the **Retention Period** in days.</span></span> <span data-ttu-id="88173-169">例如30。</span><span class="sxs-lookup"><span data-stu-id="88173-169">For example, 30.</span></span>
1. <span data-ttu-id="88173-170">按一下 [儲存]。</span><span class="sxs-lookup"><span data-stu-id="88173-170">Click **Save**.</span></span>

<span data-ttu-id="88173-171">Web 應用程式會產生 ASP.NET Core 和 web 伺服器 (App Service) 記錄。</span><span class="sxs-lookup"><span data-stu-id="88173-171">ASP.NET Core and web server (App Service) logs are generated for the web app.</span></span> <span data-ttu-id="88173-172">您可以使用顯示的 FTP/FTPS 資訊來下載它們。</span><span class="sxs-lookup"><span data-stu-id="88173-172">They can be downloaded using the FTP/FTPS information displayed.</span></span> <span data-ttu-id="88173-173">密碼與本指南稍早所建立的部署認證相同。</span><span class="sxs-lookup"><span data-stu-id="88173-173">The password is the same as the deployment credentials created earlier in this guide.</span></span> <span data-ttu-id="88173-174">[您可以使用 PowerShell 或 Azure CLI](/azure/app-service/web-sites-enable-diagnostic-log#download)，將記錄檔直接串流至您的本機電腦。</span><span class="sxs-lookup"><span data-stu-id="88173-174">The logs can be [streamed directly to your local machine with PowerShell or Azure CLI](/azure/app-service/web-sites-enable-diagnostic-log#download).</span></span> <span data-ttu-id="88173-175">您也可以 [在 Application Insights 中查看](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights)記錄。</span><span class="sxs-lookup"><span data-stu-id="88173-175">Logs can also be [viewed in Application Insights](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).</span></span>

## <a name="log-streaming"></a><span data-ttu-id="88173-176">記錄串流</span><span class="sxs-lookup"><span data-stu-id="88173-176">Log streaming</span></span>

<span data-ttu-id="88173-177">您可以透過入口網站即時串流應用程式和 web 伺服器記錄。</span><span class="sxs-lookup"><span data-stu-id="88173-177">App and web server logs can be streamed in real time through the portal.</span></span>

1. <span data-ttu-id="88173-178">開啟 [Azure 入口網站](https://portal.azure.com)，然後流覽至 *mywebapp \<unique_number\>* App Service。</span><span class="sxs-lookup"><span data-stu-id="88173-178">Open the [Azure portal](https://portal.azure.com), and navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="88173-179">在左側功能表中，向下移至 [ **監視** ] 區段，然後選取 [ **記錄資料流程**]。</span><span class="sxs-lookup"><span data-stu-id="88173-179">In the menu to the left, scroll down to the **Monitoring** section and select **Log stream**.</span></span>

    ![顯示記錄資料流程連結的螢幕擷取畫面](./media/monitoring/log-stream.png)

<span data-ttu-id="88173-181">您也可以透過 [Azure CLI 或 Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs)來串流記錄，包括透過 Cloud Shell。</span><span class="sxs-lookup"><span data-stu-id="88173-181">Logs can also be [streamed via Azure CLI or Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), including through the Cloud Shell.</span></span>

## <a name="alerts"></a><span data-ttu-id="88173-182">警示</span><span class="sxs-lookup"><span data-stu-id="88173-182">Alerts</span></span>

<span data-ttu-id="88173-183">Azure 監視器也會根據計量、系統管理事件和其他準則來提供 [即時警示](/azure/monitoring-and-diagnostics/insights-alerts-portal) 。</span><span class="sxs-lookup"><span data-stu-id="88173-183">Azure Monitor also provides [real time alerts](/azure/monitoring-and-diagnostics/insights-alerts-portal) based on metrics, administrative events, and other criteria.</span></span>

> <span data-ttu-id="88173-184">*注意： web 應用程式計量目前的警示僅適用于 (傳統) 服務的警示。*</span><span class="sxs-lookup"><span data-stu-id="88173-184">*Note: Currently alerting on web app metrics is only available in the Alerts (classic) service.*</span></span>

<span data-ttu-id="88173-185">您可以在 Azure 監視器或在 App Service 設定的 [**監視**] 區段下，找到 [ (傳統) 服務的警示](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal)。</span><span class="sxs-lookup"><span data-stu-id="88173-185">The [Alerts (classic) service](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) can be found in Azure Monitor or under the **Monitoring** section of the App Service settings.</span></span>

![ (傳統) 連結的警示](./media/monitoring/alerts.png)

## <a name="live-debugging"></a><span data-ttu-id="88173-187">即時調試</span><span class="sxs-lookup"><span data-stu-id="88173-187">Live debugging</span></span>

<span data-ttu-id="88173-188">當記錄檔未提供足夠的資訊時，Azure App Service 可以 [使用 Visual Studio 來進行遠端偵錯](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) 。</span><span class="sxs-lookup"><span data-stu-id="88173-188">Azure App Service can be [debugged remotely with Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) when logs don't provide enough information.</span></span> <span data-ttu-id="88173-189">不過，遠端偵錯程式需要使用 debug 符號來編譯應用程式。</span><span class="sxs-lookup"><span data-stu-id="88173-189">However, remote debugging requires the app to be compiled with debug symbols.</span></span> <span data-ttu-id="88173-190">除了最後的手段以外，不應在生產環境中完成調試。</span><span class="sxs-lookup"><span data-stu-id="88173-190">Debugging shouldn't be done in production, except as a last resort.</span></span>

## <a name="conclusion"></a><span data-ttu-id="88173-191">結論</span><span class="sxs-lookup"><span data-stu-id="88173-191">Conclusion</span></span>

<span data-ttu-id="88173-192">在本節中，您已完成下列工作：</span><span class="sxs-lookup"><span data-stu-id="88173-192">In this section, you completed the following tasks:</span></span>

* <span data-ttu-id="88173-193">在 Azure 入口網站中尋找基本的監視和疑難排解資料</span><span class="sxs-lookup"><span data-stu-id="88173-193">Find basic monitoring and troubleshooting data in the Azure portal</span></span>
* <span data-ttu-id="88173-194">瞭解 Azure 監視器如何讓您更深入瞭解所有 Azure 服務之間的計量</span><span class="sxs-lookup"><span data-stu-id="88173-194">Learn how Azure Monitor provides a deeper look at metrics across all Azure services</span></span>
* <span data-ttu-id="88173-195">將 web 應用程式與 Application Insights 連線以進行應用程式分析</span><span class="sxs-lookup"><span data-stu-id="88173-195">Connect the web app with Application Insights for app profiling</span></span>
* <span data-ttu-id="88173-196">開啟記錄並瞭解下載記錄的位置</span><span class="sxs-lookup"><span data-stu-id="88173-196">Turn on logging and learn where to download logs</span></span>
* <span data-ttu-id="88173-197">即時串流記錄</span><span class="sxs-lookup"><span data-stu-id="88173-197">Stream logs in real time</span></span>
* <span data-ttu-id="88173-198">瞭解設定警示的位置</span><span class="sxs-lookup"><span data-stu-id="88173-198">Learn where to set up alerts</span></span>
* <span data-ttu-id="88173-199">瞭解如何 Azure App Service web 應用程式進行遠端偵錯程式。</span><span class="sxs-lookup"><span data-stu-id="88173-199">Learn about remote debugging Azure App Service web apps.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="88173-200">延伸閱讀</span><span class="sxs-lookup"><span data-stu-id="88173-200">Additional reading</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="88173-201">使用 Application Insights 監視 Azure Web 應用程式效能</span><span class="sxs-lookup"><span data-stu-id="88173-201">Monitor Azure web app performance with Application Insights</span></span>](/azure/application-insights/app-insights-azure-web-apps)
* [<span data-ttu-id="88173-202">在 Azure App Service 中針對 Web 應用程式啟用診斷記錄功能。</span><span class="sxs-lookup"><span data-stu-id="88173-202">Enable diagnostics logging for web apps in Azure App Service</span></span>](/azure/app-service/web-sites-enable-diagnostic-log)
* [<span data-ttu-id="88173-203">使用 Visual Studio 疑難排解 Azure App Service 中的 Web 應用程式</span><span class="sxs-lookup"><span data-stu-id="88173-203">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="88173-204">在 Azure 監視器中為 Azure 服務建立傳統計量警示 - Azure 入口網站</span><span class="sxs-lookup"><span data-stu-id="88173-204">Create classic metric alerts in Azure Monitor for Azure services - Azure portal</span></span>](/azure/monitoring-and-diagnostics/insights-alerts-portal)
