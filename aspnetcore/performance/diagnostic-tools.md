---
title: Performance Diagnostics 工具
author: mjrousos
description: 在 ASP.NET Core 應用程式中診斷效能問題的有用工具。
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
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
uid: performance/diagnostic-tools
ms.openlocfilehash: 71386de232265840f9b825bd33d23bb1d218efc6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060855"
---
# <a name="performance-diagnostic-tools"></a><span data-ttu-id="47594-103">效能診斷工具</span><span class="sxs-lookup"><span data-stu-id="47594-103">Performance Diagnostic Tools</span></span>

<span data-ttu-id="47594-104">由 [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="47594-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="47594-105">本文列出在 ASP.NET Core 中診斷效能問題的工具。</span><span class="sxs-lookup"><span data-stu-id="47594-105">This article lists tools for diagnosing performance issues in ASP.NET Core.</span></span>

## <a name="visual-studio-diagnostic-tools"></a><span data-ttu-id="47594-106">Visual Studio 診斷工具</span><span class="sxs-lookup"><span data-stu-id="47594-106">Visual Studio Diagnostic Tools</span></span>

<span data-ttu-id="47594-107">Visual Studio 內建的程式碼 [剖析和診斷工具](/visualstudio/profiling) ，是開始調查效能問題的好地方。</span><span class="sxs-lookup"><span data-stu-id="47594-107">The [profiling and diagnostic tools](/visualstudio/profiling) built into Visual Studio are a good place to start investigating performance issues.</span></span> <span data-ttu-id="47594-108">這些工具在 Visual Studio 開發環境中，是功能強大且方便的使用。</span><span class="sxs-lookup"><span data-stu-id="47594-108">These tools are powerful and convenient to use from the Visual Studio development environment.</span></span> <span data-ttu-id="47594-109">這些工具可讓您在 ASP.NET Core apps 中分析 CPU 使用量、記憶體使用量和效能事件。</span><span class="sxs-lookup"><span data-stu-id="47594-109">The tooling allows analysis of CPU usage, memory usage, and performance events in ASP.NET Core apps.</span></span> <span data-ttu-id="47594-110">內建可讓您在開發期間輕鬆進行分析。</span><span class="sxs-lookup"><span data-stu-id="47594-110">Being built-in makes profiling easy at development time.</span></span>

<span data-ttu-id="47594-111">[Visual Studio 檔](/visualstudio/profiling/profiling-overview)中提供詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="47594-111">More information is available in [Visual Studio documentation](/visualstudio/profiling/profiling-overview).</span></span>

## <a name="application-insights"></a><span data-ttu-id="47594-112">Application Insights</span><span class="sxs-lookup"><span data-stu-id="47594-112">Application Insights</span></span>

<span data-ttu-id="47594-113">[Application Insights](/azure/application-insights/app-insights-overview) 為您的應用程式提供深入的效能資料。</span><span class="sxs-lookup"><span data-stu-id="47594-113">[Application Insights](/azure/application-insights/app-insights-overview) provides in-depth performance data for your app.</span></span> <span data-ttu-id="47594-114">Application Insights 會自動收集回應率、失敗率、相依性回應時間等資料。</span><span class="sxs-lookup"><span data-stu-id="47594-114">Application Insights automatically collects data on response rates, failure rates, dependency response times, and more.</span></span> <span data-ttu-id="47594-115">Application Insights 支援記錄您應用程式特定的自訂事件和計量。</span><span class="sxs-lookup"><span data-stu-id="47594-115">Application Insights supports logging custom events and metrics specific to your app.</span></span>

<span data-ttu-id="47594-116">Azure 應用程式 Insights 提供多種方式來提供受監視應用程式的深入解析：</span><span class="sxs-lookup"><span data-stu-id="47594-116">Azure Application Insights provides multiple ways to give insights on monitored apps:</span></span>

- <span data-ttu-id="47594-117">[應用程式對應](/azure/application-insights/app-insights-app-map) –有助於找出跨分散式應用程式元件的效能瓶頸或失敗的作用點。</span><span class="sxs-lookup"><span data-stu-id="47594-117">[Application Map](/azure/application-insights/app-insights-app-map) – helps spot performance bottlenecks or failure hot-spots across all components of distributed apps.</span></span>
- <span data-ttu-id="47594-118">[Azure 計量瀏覽器](/azure/azure-monitor/platform/metrics-getting-started) 是 Microsoft Azure 入口網站的元件，可讓您繪製圖表、以視覺化方式關聯趨勢，以及調查計量值的尖峰和下降。</span><span class="sxs-lookup"><span data-stu-id="47594-118">[Azure Metrics Explorer](/azure/azure-monitor/platform/metrics-getting-started) is a component of the Microsoft Azure portal that allows plotting charts, visually correlating trends, and investigating spikes and dips in metrics' values.</span></span>
- <span data-ttu-id="47594-119">[Application Insights 入口網站中的效能](/azure/application-insights/app-insights-tutorial-performance)分頁：</span><span class="sxs-lookup"><span data-stu-id="47594-119">[Performance blade in Application Insights portal](/azure/application-insights/app-insights-tutorial-performance):</span></span>

  - <span data-ttu-id="47594-120">顯示受監視應用程式中不同作業的效能詳細資料。</span><span class="sxs-lookup"><span data-stu-id="47594-120">Shows performance details for different operations in the monitored app.</span></span>
  - <span data-ttu-id="47594-121">允許切入單一作業來檢查參與長期的所有元件/相依性。</span><span class="sxs-lookup"><span data-stu-id="47594-121">Allows drilling into a single operation to check all parts/dependencies that contribute to a long duration.</span></span>
  - <span data-ttu-id="47594-122">您可以從這裡叫用 Profiler，視需要收集效能追蹤。</span><span class="sxs-lookup"><span data-stu-id="47594-122">Profiler can be invoked from here to collect performance traces on-demand.</span></span>

- <span data-ttu-id="47594-123">[Azure 應用程式 Insights Profiler](/azure/azure-monitor/app/profiler) 可讓您定期和依需求對 .net 應用程式進行程式碼剖析。</span><span class="sxs-lookup"><span data-stu-id="47594-123">[Azure Application Insights Profiler](/azure/azure-monitor/app/profiler) allows regular and on-demand profiling of .NET apps.</span></span>  <span data-ttu-id="47594-124">Azure 入口網站會顯示具有呼叫堆疊和熱路徑的已捕獲效能追蹤。</span><span class="sxs-lookup"><span data-stu-id="47594-124">Azure portal shows captured performance traces with call stacks and hot paths.</span></span> <span data-ttu-id="47594-125">您也可以下載追蹤檔案，以使用 PerfView 進行更深入的分析。</span><span class="sxs-lookup"><span data-stu-id="47594-125">The trace files can also be downloaded for deeper analysis using PerfView.</span></span>

<span data-ttu-id="47594-126">Application Insights 可以在各種環境中使用：</span><span class="sxs-lookup"><span data-stu-id="47594-126">Application Insights can be used in a variety environments:</span></span>

- <span data-ttu-id="47594-127">已優化，可在 Azure 中運作。</span><span class="sxs-lookup"><span data-stu-id="47594-127">Optimized to work in Azure.</span></span>
- <span data-ttu-id="47594-128">適用于生產環境、開發和預備環境。</span><span class="sxs-lookup"><span data-stu-id="47594-128">Works in production, development, and staging.</span></span>
- <span data-ttu-id="47594-129">從 [Visual Studio](/azure/application-insights/app-insights-visual-studio) 或其他裝載環境中的本機運作。</span><span class="sxs-lookup"><span data-stu-id="47594-129">Works locally from [Visual Studio](/azure/application-insights/app-insights-visual-studio) or in other hosting environments.</span></span>

<span data-ttu-id="47594-130">如需詳細資訊，請參閱 [ASP.NET Core 的 Application Insights](/azure/application-insights/app-insights-asp-net-core)。</span><span class="sxs-lookup"><span data-stu-id="47594-130">For more information, see [Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="perfview"></a><span data-ttu-id="47594-131">PerfView</span><span class="sxs-lookup"><span data-stu-id="47594-131">PerfView</span></span>

<span data-ttu-id="47594-132">[PerfView](https://github.com/Microsoft/perfview) 是 .net 團隊為了診斷 .net 效能問題而建立的效能分析工具。</span><span class="sxs-lookup"><span data-stu-id="47594-132">[PerfView](https://github.com/Microsoft/perfview) is a performance analysis tool created by the .NET team specifically for diagnosing .NET performance issues.</span></span> <span data-ttu-id="47594-133">PerfView 可讓您分析 CPU 使用量、記憶體和 GC 行為、效能事件和時鐘時間。</span><span class="sxs-lookup"><span data-stu-id="47594-133">PerfView allows analysis of CPU usage, memory and GC behavior, performance events, and wall clock time.</span></span>

<span data-ttu-id="47594-134">您可以深入瞭解 PerfView 以及如何開始使用 [PerfView 影片教學](https://channel9.msdn.com/Series/PerfView-Tutorial) 課程，或閱讀工具或 [GitHub 上](https://github.com/Microsoft/perfview)提供的使用者指南。</span><span class="sxs-lookup"><span data-stu-id="47594-134">You can learn more about PerfView and how to get started with [PerfView video tutorials](https://channel9.msdn.com/Series/PerfView-Tutorial) or by reading the user's guide available in the tool or [on GitHub](https://github.com/Microsoft/perfview).</span></span>

## <a name="windows-performance-toolkit"></a><span data-ttu-id="47594-135">Windows Performance Toolkit</span><span class="sxs-lookup"><span data-stu-id="47594-135">Windows Performance Toolkit</span></span>

<span data-ttu-id="47594-136">[Windows 效能工具](/windows-hardware/test/wpt/) 組 (WPT) 是由兩個元件所組成： WINDOWS PERFORMANCE RECORDER (WPR) 和 WINDOWS PERFORMANCE ANALYZER (WPA) 。</span><span class="sxs-lookup"><span data-stu-id="47594-136">[Windows Performance Toolkit](/windows-hardware/test/wpt/) (WPT) consists of two components: Windows Performance Recorder (WPR) and Windows Performance Analyzer (WPA).</span></span> <span data-ttu-id="47594-137">這些工具會產生 Windows 作業系統和應用程式的深入效能設定檔。</span><span class="sxs-lookup"><span data-stu-id="47594-137">The tools produce in-depth performance profiles of Windows operating systems and apps.</span></span> <span data-ttu-id="47594-138">WPT 具有更豐富的資料視覺化方式，但其資料收集比 PerfView 的強大。</span><span class="sxs-lookup"><span data-stu-id="47594-138">WPT has richer ways of visualizing data, but its data collecting is less powerful than PerfView's.</span></span>

## <a name="perfcollect"></a><span data-ttu-id="47594-139">PerfCollect</span><span class="sxs-lookup"><span data-stu-id="47594-139">PerfCollect</span></span>

<span data-ttu-id="47594-140">雖然 PerfView 是適用于 .NET 案例的實用效能分析工具，但它只會在 Windows 上執行，因此您無法使用它從 Linux 環境中執行的 ASP.NET Core 應用程式收集追蹤。</span><span class="sxs-lookup"><span data-stu-id="47594-140">While PerfView is a useful performance analysis tool for .NET scenarios, it only runs on Windows so you can't use it to collect traces from ASP.NET Core apps running in Linux environments.</span></span>

<span data-ttu-id="47594-141">[PerfCollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) 是一種 bash 腳本，它會使用原生 Linux 分析 [工具 (效能](https://perf.wiki.kernel.org/index.php/Main_Page) 和 [LTTng](https://lttng.org/)) ，在可由 PerfView 分析的 Linux 上收集追蹤。</span><span class="sxs-lookup"><span data-stu-id="47594-141">[PerfCollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) is a bash script that uses native Linux profiling tools ([Perf](https://perf.wiki.kernel.org/index.php/Main_Page) and [LTTng](https://lttng.org/)) to collect traces on Linux that can be analyzed by PerfView.</span></span> <span data-ttu-id="47594-142">當效能問題出現在無法直接使用 PerfView 的 Linux 環境中時，PerfCollect 會很有用。</span><span class="sxs-lookup"><span data-stu-id="47594-142">PerfCollect is useful when performance problems show up in Linux environments where PerfView can't be used directly.</span></span> <span data-ttu-id="47594-143">相反地，PerfCollect 可以從 .NET Core 應用程式收集追蹤，然後使用 PerfView 在 Windows 電腦上進行分析。</span><span class="sxs-lookup"><span data-stu-id="47594-143">Instead, PerfCollect can collect traces from .NET Core apps that are then analyzed on a Windows computer using PerfView.</span></span>

<span data-ttu-id="47594-144">有關如何安裝和開始使用 PerfCollect 的詳細資訊，可 [在 GitHub 上](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md)取得。</span><span class="sxs-lookup"><span data-stu-id="47594-144">More information about how to install and get started with PerfCollect is available [on GitHub](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md).</span></span>

## <a name="other-third-party-performance-tools"></a><span data-ttu-id="47594-145">其他協力廠商效能工具</span><span class="sxs-lookup"><span data-stu-id="47594-145">Other Third-party Performance Tools</span></span>

<span data-ttu-id="47594-146">以下列出一些適用于 .NET Core 應用程式效能調查的協力廠商效能工具。</span><span class="sxs-lookup"><span data-stu-id="47594-146">The following lists some third-party performance tools that are useful in performance investigation of .NET Core applications.</span></span>

- [<span data-ttu-id="47594-147">Miniprofiler 撼動</span><span class="sxs-lookup"><span data-stu-id="47594-147">MiniProfiler</span></span>](https://miniprofiler.com/)
- <span data-ttu-id="47594-148">來自[JetBrains](https://www.jetbrains.com/)的[dotTrace](https://www.jetbrains.com/profiler/)和[dotMemory](https://www.jetbrains.com/dotmemory/)</span><span class="sxs-lookup"><span data-stu-id="47594-148">[dotTrace](https://www.jetbrains.com/profiler/) and [dotMemory](https://www.jetbrains.com/dotmemory/) from [JetBrains](https://www.jetbrains.com/)</span></span>
- <span data-ttu-id="47594-149">來自 Intel 的[VTune](https://software.intel.com/content/www/us/en/develop/tools/vtune-profiler.html)</span><span class="sxs-lookup"><span data-stu-id="47594-149">[VTune](https://software.intel.com/content/www/us/en/develop/tools/vtune-profiler.html) from Intel</span></span>
