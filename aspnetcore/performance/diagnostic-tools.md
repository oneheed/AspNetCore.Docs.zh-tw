---
title: Performance Diagnostics 工具
author: mjrousos
description: 在 ASP.NET Core 應用程式中診斷效能問題的有用工具。
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
no-loc:
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
uid: performance/diagnostic-tools
ms.openlocfilehash: 5f3daaf132b903898e756160a459d4df5f421c11
ms.sourcegitcommit: dd0e87abf2bb50ee992d9185bb256ed79d48f545
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2020
ms.locfileid: "88746542"
---
# <a name="performance-diagnostic-tools"></a>效能診斷工具

由 [Mike Rousos](https://github.com/mjrousos)

本文列出在 ASP.NET Core 中診斷效能問題的工具。

## <a name="visual-studio-diagnostic-tools"></a>Visual Studio 診斷工具

Visual Studio 內建的程式碼 [剖析和診斷工具](/visualstudio/profiling) ，是開始調查效能問題的好地方。 這些工具在 Visual Studio 開發環境中，是功能強大且方便的使用。 這些工具可讓您在 ASP.NET Core apps 中分析 CPU 使用量、記憶體使用量和效能事件。 內建可讓您在開發期間輕鬆進行分析。

[Visual Studio 檔](/visualstudio/profiling/profiling-overview)中提供詳細資訊。

## <a name="application-insights"></a>Application Insights

[Application Insights](/azure/application-insights/app-insights-overview) 為您的應用程式提供深入的效能資料。 Application Insights 會自動收集回應率、失敗率、相依性回應時間等資料。 Application Insights 支援記錄您應用程式特定的自訂事件和計量。

Azure 應用程式 Insights 提供多種方式來提供受監視應用程式的深入解析：

- [應用程式對應](/azure/application-insights/app-insights-app-map) –有助於找出跨分散式應用程式元件的效能瓶頸或失敗的作用點。
- [Azure 計量瀏覽器](/azure/azure-monitor/platform/metrics-getting-started) 是 Microsoft Azure 入口網站的元件，可讓您繪製圖表、以視覺化方式關聯趨勢，以及調查計量值的尖峰和下降。
- [Application Insights 入口網站中的效能](/azure/application-insights/app-insights-tutorial-performance)分頁：

  - 顯示受監視應用程式中不同作業的效能詳細資料。
  - 允許切入單一作業來檢查參與長期的所有元件/相依性。
  - 您可以從這裡叫用 Profiler，視需要收集效能追蹤。

- [Azure 應用程式 Insights Profiler](/azure/azure-monitor/app/profiler) 可讓您定期和依需求對 .net 應用程式進行程式碼剖析。  Azure 入口網站會顯示具有呼叫堆疊和熱路徑的已捕獲效能追蹤。 您也可以下載追蹤檔案，以使用 PerfView 進行更深入的分析。

Application Insights 可以在各種環境中使用：

- 已優化，可在 Azure 中運作。
- 適用于生產環境、開發和預備環境。
- 從 [Visual Studio](/azure/application-insights/app-insights-visual-studio) 或其他裝載環境中的本機運作。

如需詳細資訊，請參閱 [ASP.NET Core 的 Application Insights](/azure/application-insights/app-insights-asp-net-core)。

## <a name="perfview"></a>PerfView

[PerfView](https://github.com/Microsoft/perfview) 是 .net 團隊為了診斷 .net 效能問題而建立的效能分析工具。 PerfView 可讓您分析 CPU 使用量、記憶體和 GC 行為、效能事件和時鐘時間。

您可以深入瞭解 PerfView 以及如何開始使用 [PerfView 影片教學](https://channel9.msdn.com/Series/PerfView-Tutorial) 課程，或閱讀工具或 [GitHub 上](https://github.com/Microsoft/perfview)提供的使用者指南。

## <a name="windows-performance-toolkit"></a>Windows Performance Toolkit

[Windows 效能工具](/windows-hardware/test/wpt/) 組 (WPT) 是由兩個元件所組成： WINDOWS PERFORMANCE RECORDER (WPR) 和 WINDOWS PERFORMANCE ANALYZER (WPA) 。 這些工具會產生 Windows 作業系統和應用程式的深入效能設定檔。 WPT 具有更豐富的資料視覺化方式，但其資料收集比 PerfView 的強大。

## <a name="perfcollect"></a>PerfCollect

雖然 PerfView 是適用于 .NET 案例的實用效能分析工具，但它只會在 Windows 上執行，因此您無法使用它從 Linux 環境中執行的 ASP.NET Core 應用程式收集追蹤。

[PerfCollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) 是一種 bash 腳本，它會使用原生 Linux 分析 [工具 (效能](https://perf.wiki.kernel.org/index.php/Main_Page) 和 [LTTng](https://lttng.org/)) ，在可由 PerfView 分析的 Linux 上收集追蹤。 當效能問題出現在無法直接使用 PerfView 的 Linux 環境中時，PerfCollect 會很有用。 相反地，PerfCollect 可以從 .NET Core 應用程式收集追蹤，然後使用 PerfView 在 Windows 電腦上進行分析。

有關如何安裝和開始使用 PerfCollect 的詳細資訊，可 [在 GitHub 上](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md)取得。

## <a name="other-third-party-performance-tools"></a>其他協力廠商效能工具

以下列出一些適用于 .NET Core 應用程式效能調查的協力廠商效能工具。

- [Miniprofiler 撼動](https://miniprofiler.com/)
- 來自[JetBrains](https://www.jetbrains.com/)的[dotTrace](https://www.jetbrains.com/profiler/)和[dotMemory](https://www.jetbrains.com/dotmemory/)
- 來自 Intel 的[VTune](https://software.intel.com/content/www/us/en/develop/tools/vtune-profiler.html)
