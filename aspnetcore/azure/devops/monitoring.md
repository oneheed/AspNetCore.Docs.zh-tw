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
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056747"
---
# <a name="monitor-and-debug"></a>監視和調試

部署應用程式並建立 DevOps 管線之後，請務必瞭解如何監視應用程式並對其進行疑難排解。

在本節中，您將完成下列工作：

* 在 Azure 入口網站中尋找基本的監視和疑難排解資料
* 瞭解 Azure 監視器如何讓您更深入瞭解所有 Azure 服務之間的計量
* 將 web 應用程式與 Application Insights 連線以進行應用程式分析
* 開啟記錄並瞭解下載記錄的位置
* 即時串流記錄
* 瞭解設定警示的位置
* 瞭解如何 Azure App Service web 應用程式進行遠端偵錯程式。

## <a name="basic-monitoring-and-troubleshooting"></a>基本監視與疑難排解

App Service 的 web 應用程式可即時輕鬆監視。 Azure 入口網站會以易於瞭解的圖表和圖形呈現度量。

1. 開啟 [Azure 入口網站](https://portal.azure.com)，然後流覽至 [ *mywebapp \<unique_number\>* ] App Service。

1. [ **總覽** ] 索引標籤會顯示有用的「一覽」資訊，包括顯示最近計量的圖表。

    ![顯示總覽面板的螢幕擷取畫面](./media/monitoring/overview.png)

    * **Http 5xx** ：伺服器端錯誤的計數，通常是 ASP.NET Core 程式碼中的例外狀況。
    * **資料** 輸入：進入您 web 應用程式的資料輸入。
    * **資料輸出** ：從 web 應用程式到用戶端的資料輸出。
    * **要求** ： HTTP 要求的計數。
    * **平均回應時間** ： web 應用程式回應 HTTP 要求的平均時間。

    您也可以在此頁面上找到數個用於疑難排解和優化的自助工具。

    ![顯示自助工具的螢幕擷取畫面](./media/monitoring/wizards.png)

    * **診斷和解決問題** 是自助服務疑難排解員。
    * **Application Insights** 適用于分析效能和應用程式行為，並將在本節稍後討論。
    * **App Service Advisor** 提出建議來調整您的應用程式體驗。

## <a name="advanced-monitoring"></a>進階監視

[Azure 監視器](/azure/monitoring-and-diagnostics/) 是集中式服務，可監視所有計量，並在 Azure 服務中設定警示。 在 Azure 監視器中，系統管理員可以更精細地追蹤效能並找出趨勢。 每個 Azure 服務都提供自己 [的一組計量](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) 來 Azure 監視器。

## <a name="profile-with-application-insights"></a>使用 Application Insights 設定檔

[Application Insights](/azure/application-insights/app-insights-overview) 是一項 Azure 服務，用來分析 web 應用程式的效能和穩定性，以及使用者使用這些應用程式的方式。 Application Insights 的資料比 Azure 監視器更廣泛且更深入。 資料可以為開發人員和系統管理員提供重要資訊，以改善應用程式。 Application Insights 可以新增至 Azure App Service 的資源，而不需要變更程式碼。

1. 開啟 [Azure 入口網站](https://portal.azure.com)，然後流覽至 [ *mywebapp \<unique_number\>* ] App Service。
1. 在 [ **總覽** ] 索引標籤中，按一下 [ **Application Insights** ] 磚。

    ![Application Insights 圖格](./media/monitoring/app-insights.png)

1. 選取 [ **建立新資源** ] 選項按鈕。 使用預設的資源名稱，然後選取 Application Insights 資源的位置。 此位置不需要與您的 web 應用程式相符。

    ![Application Insights 設定](./media/monitoring/new-app-insights.png)

1. 針對 **執行時間/架構** ，請選取 [ **ASP.NET Core** ]。 接受預設設定。
1. 選取 [確定]  。 如果系統提示您確認，請選取 [ **繼續** ]。
1. 建立資源之後，請按一下 Application Insights 資源的名稱，直接流覽至 Application Insights 頁面。

    ![已備妥新的 Application Insights 資源](./media/monitoring/new-app-insights-done.png)

使用應用程式時，資料會累積。 選取 **[** 重新整理]，以新資料重載該分頁。

![Application Insights 總覽] 索引標籤](./media/monitoring/app-insights-overview.png)

Application Insights 提供有用的伺服器端資訊，不需額外設定。 若要充分利用 Application Insights 的最大價值，請 [使用 APPLICATION INSIGHTS SDK 來檢測您的應用程式](/azure/application-insights/app-insights-asp-net-core)。 如果設定正確，服務會在 web 伺服器和瀏覽器之間提供端對端監視，包括用戶端效能。 如需詳細資訊，請參閱 [Application Insights 檔](/azure/application-insights/app-insights-overview)。

## <a name="logging"></a>記錄

在 Azure App Service 中，預設會停用 Web 服務器和應用程式記錄檔。 使用下列步驟啟用記錄：

1. 開啟 [Azure 入口網站](https://portal.azure.com)，然後流覽至 *mywebapp \<unique_number\>* App Service。
1. 在左側功能表中，向下滾動至 [ **監視** ] 區段。 選取 [ **診斷記錄** ]。

    ![診斷記錄連結](./media/monitoring/logging.png)

1. 開啟 **應用程式記錄 (檔案系統)** 。 如果出現提示，請按一下方塊以安裝延伸模組，以在 web 應用程式中啟用應用程式記錄。
1. 將 **網頁伺服器記錄** 設定為 **檔案系統** 。
1. 輸入 **保留期限** （以天為單位）。 例如30。
1. 按一下 [儲存]。

Web 應用程式會產生 ASP.NET Core 和 web 伺服器 (App Service) 記錄。 您可以使用顯示的 FTP/FTPS 資訊來下載它們。 密碼與本指南稍早所建立的部署認證相同。 [您可以使用 PowerShell 或 Azure CLI](/azure/app-service/web-sites-enable-diagnostic-log#download)，將記錄檔直接串流至您的本機電腦。 您也可以 [在 Application Insights 中查看](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights)記錄。

## <a name="log-streaming"></a>記錄串流

您可以透過入口網站即時串流應用程式和 web 伺服器記錄。

1. 開啟 [Azure 入口網站](https://portal.azure.com)，然後流覽至 *mywebapp \<unique_number\>* App Service。
1. 在左側功能表中，向下移至 [ **監視** ] 區段，然後選取 [ **記錄資料流程** ]。

    ![顯示記錄資料流程連結的螢幕擷取畫面](./media/monitoring/log-stream.png)

您也可以透過 [Azure CLI 或 Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs)來串流記錄，包括透過 Cloud Shell。

## <a name="alerts"></a>警示

Azure 監視器也會根據計量、系統管理事件和其他準則來提供 [即時警示](/azure/monitoring-and-diagnostics/insights-alerts-portal) 。

> *注意： web 應用程式計量目前的警示僅適用于 (傳統) 服務的警示。*

您可以在 Azure 監視器或在 App Service 設定的 [ **監視** ] 區段下，找到 [ (傳統) 服務的警示](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal)。

![ (傳統) 連結的警示](./media/monitoring/alerts.png)

## <a name="live-debugging"></a>即時調試

當記錄檔未提供足夠的資訊時，Azure App Service 可以 [使用 Visual Studio 來進行遠端偵錯](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) 。 不過，遠端偵錯程式需要使用 debug 符號來編譯應用程式。 除了最後的手段以外，不應在生產環境中完成調試。

## <a name="conclusion"></a>結論

在本節中，您已完成下列工作：

* 在 Azure 入口網站中尋找基本的監視和疑難排解資料
* 瞭解 Azure 監視器如何讓您更深入瞭解所有 Azure 服務之間的計量
* 將 web 應用程式與 Application Insights 連線以進行應用程式分析
* 開啟記錄並瞭解下載記錄的位置
* 即時串流記錄
* 瞭解設定警示的位置
* 瞭解如何 Azure App Service web 應用程式進行遠端偵錯程式。

## <a name="additional-reading"></a>延伸閱讀

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [使用 Application Insights 監視 Azure Web 應用程式效能](/azure/application-insights/app-insights-azure-web-apps)
* [在 Azure App Service 中針對 Web 應用程式啟用診斷記錄功能。](/azure/app-service/web-sites-enable-diagnostic-log)
* [使用 Visual Studio 疑難排解 Azure App Service 中的 Web 應用程式](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [在 Azure 監視器中為 Azure 服務建立傳統計量警示 - Azure 入口網站](/azure/monitoring-and-diagnostics/insights-alerts-portal)
