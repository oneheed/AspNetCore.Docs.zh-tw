---
title: ASP.NET Core 中的健康狀態檢查
author: rick-anderson
description: 了解如何為 ASP.NET Core 基礎結構 (例如應用程式和資料庫) 設定健康狀態檢查。
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2021
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
uid: host-and-deploy/health-checks
ms.openlocfilehash: 5282ca2e96fbe5526a020aea0d845dd6e4dd4fb9
ms.sourcegitcommit: 7b6781051d341a1daaf46c6a4368fa8a5701db81
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2021
ms.locfileid: "105638665"
---
# <a name="health-checks-in-aspnet-core"></a><span data-ttu-id="d4b88-103">ASP.NET Core 中的健康狀態檢查</span><span class="sxs-lookup"><span data-stu-id="d4b88-103">Health checks in ASP.NET Core</span></span>

<span data-ttu-id="d4b88-104">依 [Glenn Condron](https://github.com/glennc)</span><span class="sxs-lookup"><span data-stu-id="d4b88-104">By [Glenn Condron](https://github.com/glennc)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="d4b88-105">ASP.NET Core 提供健康狀態檢查中介軟體和程式庫，以報告應用程式基礎結構元件的健康情況。</span><span class="sxs-lookup"><span data-stu-id="d4b88-105">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="d4b88-106">應用程式會將健康狀態檢查公開為 HTTP 端點。</span><span class="sxs-lookup"><span data-stu-id="d4b88-106">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="d4b88-107">您可以針對各種即時監控案例來設定健康狀態檢查端點：</span><span class="sxs-lookup"><span data-stu-id="d4b88-107">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="d4b88-108">容器協調器和負載平衡器可以使用健康狀態探查，來檢查應用程式的狀態。</span><span class="sxs-lookup"><span data-stu-id="d4b88-108">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="d4b88-109">例如，容器協調器可能會暫停輪流部署或重新啟動容器，來回應失敗的健康狀態檢查。</span><span class="sxs-lookup"><span data-stu-id="d4b88-109">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="d4b88-110">負載平衡器可能會將流量從失敗的執行個體路由傳送至狀況良好的執行個體，來回應狀況不良的應用程式。</span><span class="sxs-lookup"><span data-stu-id="d4b88-110">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="d4b88-111">您可以監控所使用記憶體、磁碟及其他實體伺服器資源的健康狀態。</span><span class="sxs-lookup"><span data-stu-id="d4b88-111">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="d4b88-112">健康狀態檢查可以測試應用程式的相依性 (例如資料庫和外部服務端點)，確認其是否可用且正常運作。</span><span class="sxs-lookup"><span data-stu-id="d4b88-112">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="d4b88-113">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/health-checks/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="d4b88-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d4b88-114">範例應用程式包含本主題中所述的案例範例。</span><span class="sxs-lookup"><span data-stu-id="d4b88-114">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="d4b88-115">若要在指定的案例中執行範例應用程式，請在命令殼層中使用來自專案資料夾的 [dotnet run](/dotnet/core/tools/dotnet-run) 命令。</span><span class="sxs-lookup"><span data-stu-id="d4b88-115">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="d4b88-116">`README.md`如需如何使用範例應用程式的詳細資訊，請參閱本主題中的範例應用程式檔案和案例描述。</span><span class="sxs-lookup"><span data-stu-id="d4b88-116">See the sample app's `README.md` file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d4b88-117">必要條件</span><span class="sxs-lookup"><span data-stu-id="d4b88-117">Prerequisites</span></span>

<span data-ttu-id="d4b88-118">健康狀態檢查通常會搭配使用外部監視服務或容器協調器，來檢查應用程式的狀態。</span><span class="sxs-lookup"><span data-stu-id="d4b88-118">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="d4b88-119">將健康狀態檢查新增至應用程式之前，請決定要使用的監控系統。</span><span class="sxs-lookup"><span data-stu-id="d4b88-119">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="d4b88-120">監控系統會指定要建立哪些健康狀態檢查類型，以及如何設定其端點。</span><span class="sxs-lookup"><span data-stu-id="d4b88-120">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="d4b88-121">[`Microsoft.AspNetCore.Diagnostics.HealthChecks`](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks)ASP.NET Core 的應用程式會隱含參考此套件。</span><span class="sxs-lookup"><span data-stu-id="d4b88-121">The [`Microsoft.AspNetCore.Diagnostics.HealthChecks`](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package is referenced implicitly for ASP.NET Core apps.</span></span> <span data-ttu-id="d4b88-122">若要使用 Entity Framework Core 執行健康情況檢查，請新增封裝的參考 [`Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore`](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-122">To perform health checks using Entity Framework Core, add a reference to the [`Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore`](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) package.</span></span>

<span data-ttu-id="d4b88-123">範例應用程式提供啟動程式碼，來示範數個案例的健康狀態檢查。</span><span class="sxs-lookup"><span data-stu-id="d4b88-123">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="d4b88-124">[資料庫探查](#database-probe)案例會使用來檢查資料庫連接的健全狀況 [`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-124">The [database probe](#database-probe) scenario checks the health of a database connection using [`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="d4b88-125">[DbContext 探查](#entity-framework-core-dbcontext-probe)案例使用 EF Core `DbContext` 來檢查資料庫。</span><span class="sxs-lookup"><span data-stu-id="d4b88-125">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="d4b88-126">為了探索資料庫案例，範例應用程式會：</span><span class="sxs-lookup"><span data-stu-id="d4b88-126">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="d4b88-127">建立資料庫，並在檔案中提供其連接字串 `appsettings.json` 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-127">Creates a database and provides its connection string in the `appsettings.json` file.</span></span>
* <span data-ttu-id="d4b88-128">在其專案檔中具有下列套件參考：</span><span class="sxs-lookup"><span data-stu-id="d4b88-128">Has the following package references in its project file:</span></span>
  * [`AspNetCore.HealthChecks.SqlServer`](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer)
  * [`Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore`](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore)

> [!NOTE]
> <span data-ttu-id="d4b88-129">[`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) Microsoft 不會維護或支援。</span><span class="sxs-lookup"><span data-stu-id="d4b88-129">[`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="d4b88-130">另一個健康狀態檢查案例示範如何將健康狀態檢查篩選至管理連接埠。</span><span class="sxs-lookup"><span data-stu-id="d4b88-130">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="d4b88-131">範例應用程式會要求您建立 `Properties/launchSettings.json` 包含管理 URL 和管理埠的檔案。</span><span class="sxs-lookup"><span data-stu-id="d4b88-131">The sample app requires you to create a `Properties/launchSettings.json` file that includes the management URL and management port.</span></span> <span data-ttu-id="d4b88-132">如需詳細資訊，請參閱[依連接埠篩選](#filter-by-port)一節。</span><span class="sxs-lookup"><span data-stu-id="d4b88-132">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="d4b88-133">基本健康狀態探查</span><span class="sxs-lookup"><span data-stu-id="d4b88-133">Basic health probe</span></span>

<span data-ttu-id="d4b88-134">對於許多應用程式，報告應用程式是否可處理要求的基本健康狀態探查組態 (「活躍度」)，便足以探索應用程式的狀態。</span><span class="sxs-lookup"><span data-stu-id="d4b88-134">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="d4b88-135">基本設定會註冊健康情況檢查服務並呼叫健康情況檢查中介軟體，以在具有健康回應的 URL 端點回應。</span><span class="sxs-lookup"><span data-stu-id="d4b88-135">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="d4b88-136">預設並未登錄特定健康狀態檢查來測試任何特定相依性或子系統。</span><span class="sxs-lookup"><span data-stu-id="d4b88-136">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="d4b88-137">如果應用程式能夠在健康狀態端點 URL 做出回應，則視為狀況良好。</span><span class="sxs-lookup"><span data-stu-id="d4b88-137">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="d4b88-138">預設回應寫入器會將狀態 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) ，以純文字回應的形式寫入用戶端，表示 [`HealthStatus.Healthy`](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) [`HealthStatus.Degraded`](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) 或 [`HealthStatus.Unhealthy`](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) 狀態。</span><span class="sxs-lookup"><span data-stu-id="d4b88-138">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [`HealthStatus.Healthy`](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [`HealthStatus.Degraded`](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [`HealthStatus.Unhealthy`](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="d4b88-139">在 `Startup.ConfigureServices` 中，使用 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks%2A> 登錄健康狀態檢查服務。</span><span class="sxs-lookup"><span data-stu-id="d4b88-139">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks%2A> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d4b88-140">藉由在中呼叫來建立健康情況檢查端點 `MapHealthChecks` `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-140">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="d4b88-141">在範例應用程式中，健康狀態檢查端點是在 `/health` (`BasicStartup.cs`) 建立的：</span><span class="sxs-lookup"><span data-stu-id="d4b88-141">In the sample app, the health check endpoint is created at `/health` (`BasicStartup.cs`):</span></span>

```csharp
public class BasicStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddHealthChecks();
    }

    public void Configure(IApplicationBuilder app)
    {
        app.UseRouting();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHealthChecks("/health");
        });
    }
}
```

<span data-ttu-id="d4b88-142">若要使用範例應用程式執行基本組態案例，請在命令殼層中執行來自專案資料夾的下列命令：</span><span class="sxs-lookup"><span data-stu-id="d4b88-142">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="d4b88-143">Docker 範例</span><span class="sxs-lookup"><span data-stu-id="d4b88-143">Docker example</span></span>

<span data-ttu-id="d4b88-144">[Docker](xref:host-and-deploy/docker/index) 提供內建 `HEALTHCHECK` 指示詞，可用來檢查使用基本健康狀態檢查組態的應用程式狀態：</span><span class="sxs-lookup"><span data-stu-id="d4b88-144">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="d4b88-145">建立健康狀態檢查</span><span class="sxs-lookup"><span data-stu-id="d4b88-145">Create health checks</span></span>

<span data-ttu-id="d4b88-146">健康狀態檢查是藉由實作 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 介面來建立。</span><span class="sxs-lookup"><span data-stu-id="d4b88-146">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="d4b88-147"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync%2A> 方法會傳回 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>，指出健康狀態為 `Healthy`、`Degraded` 或 `Unhealthy`。</span><span class="sxs-lookup"><span data-stu-id="d4b88-147">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync%2A> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="d4b88-148">結果會寫成具有可設定狀態碼的純文字回應 ([健康狀態檢查選項](#health-check-options)一節中將說明如何進行組態)。</span><span class="sxs-lookup"><span data-stu-id="d4b88-148">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="d4b88-149"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> 也可以傳回選擇性索引鍵/值組。</span><span class="sxs-lookup"><span data-stu-id="d4b88-149"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

<span data-ttu-id="d4b88-150">下列 `ExampleHealthCheck` 類別示範健康情況檢查的版面配置。</span><span class="sxs-lookup"><span data-stu-id="d4b88-150">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="d4b88-151">健康情況檢查邏輯會放置在 `CheckHealthAsync` 方法中。</span><span class="sxs-lookup"><span data-stu-id="d4b88-151">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="d4b88-152">下列範例會將虛擬變數（）設定 `healthCheckResultHealthy` 為 `true` 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-152">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="d4b88-153">如果的值 `healthCheckResultHealthy` 設定為 `false` ，則 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus?displayProperty=nameWithType> 會傳回狀態。</span><span class="sxs-lookup"><span data-stu-id="d4b88-153">If the value of `healthCheckResultHealthy` is set to `false`, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus?displayProperty=nameWithType> status is returned.</span></span>

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        var healthCheckResultHealthy = true;

        if (healthCheckResultHealthy)
        {
            return Task.FromResult(
                HealthCheckResult.Healthy("A healthy result."));
        }

        return Task.FromResult(
            new HealthCheckResult(context.Registration.FailureStatus, 
            "An unhealthy result."));
    }
}
```

<span data-ttu-id="d4b88-154">如果 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync%2A> 在檢查期間擲回例外狀況，則 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReportEntry> 會傳回新的，其 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReportEntry.Status?displayProperty=nameWithType> 設定為 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> ，由 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck%2A> (查看 [ [註冊健康情況檢查服務](#register-health-check-services) ] 區段) 並包含一開始導致檢查失敗的 [內部例外](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReportEntry.Exception) 狀況。</span><span class="sxs-lookup"><span data-stu-id="d4b88-154">If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync%2A> throws an exception during the check, a new <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReportEntry> is returned with its <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReportEntry.Status?displayProperty=nameWithType> set to the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus>, which is defined by <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck%2A> (see the [Register health check services](#register-health-check-services) section) and includes the [inner exception](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReportEntry.Exception) that initially caused the check failure.</span></span> <span data-ttu-id="d4b88-155"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReportEntry.Description>會設定為例外狀況的訊息。</span><span class="sxs-lookup"><span data-stu-id="d4b88-155">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReportEntry.Description> is set to the exception's message.</span></span>

## <a name="register-health-check-services"></a><span data-ttu-id="d4b88-156">登錄健康狀態檢查服務</span><span class="sxs-lookup"><span data-stu-id="d4b88-156">Register health check services</span></span>

<span data-ttu-id="d4b88-157">此 `ExampleHealthCheck` 類型會新增至具有下列功能的健康情況檢查服務 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck%2A> `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="d4b88-157">The `ExampleHealthCheck` type is added to health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck%2A> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="d4b88-158">下列範例中所顯示的 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck%2A> 多載會設定在健康狀態檢查報告失敗時所要報告的失敗狀態 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>)。</span><span class="sxs-lookup"><span data-stu-id="d4b88-158">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck%2A> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="d4b88-159">如果失敗狀態設定為 `null` (預設) ， [`HealthStatus.Unhealthy`](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) 則會回報。</span><span class="sxs-lookup"><span data-stu-id="d4b88-159">If the failure status is set to `null` (default), [`HealthStatus.Unhealthy`](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="d4b88-160">此多載對程式庫作者很有用。若健康狀態檢查實作採用此設定，則當健康狀態檢查失敗時，應用程式就會強制程式庫指出失敗狀態。</span><span class="sxs-lookup"><span data-stu-id="d4b88-160">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="d4b88-161">您可以使用「標籤」來篩選健康狀態檢查 ([篩選健康狀態檢查](#filter-health-checks)一節中將進一步說明)。</span><span class="sxs-lookup"><span data-stu-id="d4b88-161">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="d4b88-162"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck%2A> 也可以執行匿名函式。</span><span class="sxs-lookup"><span data-stu-id="d4b88-162"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck%2A> can also execute a lambda function.</span></span> <span data-ttu-id="d4b88-163">在下列範例中，健康狀態檢查名稱指定為 `Example`，且檢查一律會傳回狀況良好狀態：</span><span class="sxs-lookup"><span data-stu-id="d4b88-163">In the following example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

<span data-ttu-id="d4b88-164">呼叫 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck%2A> 以將引數傳遞至健康情況檢查執行。</span><span class="sxs-lookup"><span data-stu-id="d4b88-164">Call <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck%2A> to pass arguments to a health check implementation.</span></span> <span data-ttu-id="d4b88-165">在下列範例中， `TestHealthCheckWithArgs` 會接受一個整數和一個在呼叫時使用的字串 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync%2A> ：</span><span class="sxs-lookup"><span data-stu-id="d4b88-165">In the following example, `TestHealthCheckWithArgs` accepts an integer and a string for use when <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync%2A> is called:</span></span>

```csharp
private class TestHealthCheckWithArgs : IHealthCheck
{
    public TestHealthCheckWithArgs(int i, string s)
    {
        I = i;
        S = s;
    }

    public int I { get; set; }

    public string S { get; set; }

    public Task<HealthCheckResult> CheckHealthAsync(HealthCheckContext context, 
        CancellationToken cancellationToken = default)
    {
        ...
    }
}
```

<span data-ttu-id="d4b88-166">`TestHealthCheckWithArgs` 的註冊方式是 `AddTypeActivatedCheck` 以傳遞至實作為的整數和字串來呼叫：</span><span class="sxs-lookup"><span data-stu-id="d4b88-166">`TestHealthCheckWithArgs` is registered by calling `AddTypeActivatedCheck` with the integer and string passed to the implementation:</span></span>

```csharp
services.AddHealthChecks()
    .AddTypeActivatedCheck<TestHealthCheckWithArgs>(
        "test", 
        failureStatus: HealthStatus.Degraded, 
        tags: new[] { "example" }, 
        args: new object[] { 5, "string" });
```

## <a name="use-health-checks-routing"></a><span data-ttu-id="d4b88-167">使用健康情況檢查路由</span><span class="sxs-lookup"><span data-stu-id="d4b88-167">Use Health Checks Routing</span></span>

<span data-ttu-id="d4b88-168">在中 `Startup.Configure` ， `MapHealthChecks` 使用端點 URL 或相對路徑呼叫端點產生器：</span><span class="sxs-lookup"><span data-stu-id="d4b88-168">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

### <a name="require-host"></a><span data-ttu-id="d4b88-169">需要主機</span><span class="sxs-lookup"><span data-stu-id="d4b88-169">Require host</span></span>

<span data-ttu-id="d4b88-170">呼叫 `RequireHost` 以指定一或多個允許的主機作為健康情況檢查端點。</span><span class="sxs-lookup"><span data-stu-id="d4b88-170">Call `RequireHost` to specify one or more permitted hosts for the health check endpoint.</span></span> <span data-ttu-id="d4b88-171">主機應該是 Unicode，而不是 punycode，而且可能包含埠。</span><span class="sxs-lookup"><span data-stu-id="d4b88-171">Hosts should be Unicode rather than punycode and may include a port.</span></span> <span data-ttu-id="d4b88-172">如果未提供集合，則會接受任何主機。</span><span class="sxs-lookup"><span data-stu-id="d4b88-172">If a collection isn't supplied, any host is accepted.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireHost("www.contoso.com:5001");
});
```

<span data-ttu-id="d4b88-173">如需詳細資訊，請參閱[依連接埠篩選](#filter-by-port)一節。</span><span class="sxs-lookup"><span data-stu-id="d4b88-173">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

### <a name="require-authorization"></a><span data-ttu-id="d4b88-174">需要授權</span><span class="sxs-lookup"><span data-stu-id="d4b88-174">Require authorization</span></span>

<span data-ttu-id="d4b88-175">呼叫 `RequireAuthorization` 以在健康情況檢查要求端點上執行授權中介軟體。</span><span class="sxs-lookup"><span data-stu-id="d4b88-175">Call `RequireAuthorization` to run Authorization Middleware on the health check request endpoint.</span></span> <span data-ttu-id="d4b88-176">`RequireAuthorization`多載接受一或多個授權原則。</span><span class="sxs-lookup"><span data-stu-id="d4b88-176">A `RequireAuthorization` overload accepts one or more authorization policies.</span></span> <span data-ttu-id="d4b88-177">如果未提供原則，則會使用預設授權原則。</span><span class="sxs-lookup"><span data-stu-id="d4b88-177">If a policy isn't provided, the default authorization policy is used.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireAuthorization();
});
```

### <a name="enable-cross-origin-requests-cors"></a><span data-ttu-id="d4b88-178">啟用跨原始來源要求 (CORS)</span><span class="sxs-lookup"><span data-stu-id="d4b88-178">Enable Cross-Origin Requests (CORS)</span></span>

<span data-ttu-id="d4b88-179">雖然以手動方式從瀏覽器執行健康情況檢查不是常見的使用案例，但您可以藉由呼叫健康情況檢查端點來啟用 CORS 中介軟體 `RequireCors` 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-179">Although performing health checks manually from a browser isn't a common use scenario, CORS Middleware can be enabled by calling `RequireCors` on health checks endpoints.</span></span> <span data-ttu-id="d4b88-180">多載會 `RequireCors` 接受 CORS 原則產生器委派 (`CorsPolicyBuilder`) 或原則名稱。</span><span class="sxs-lookup"><span data-stu-id="d4b88-180">A `RequireCors` overload accepts a CORS policy builder delegate (`CorsPolicyBuilder`) or a policy name.</span></span> <span data-ttu-id="d4b88-181">如果未提供原則，則會使用預設 CORS 原則。</span><span class="sxs-lookup"><span data-stu-id="d4b88-181">If a policy isn't provided, the default CORS policy is used.</span></span> <span data-ttu-id="d4b88-182">如需詳細資訊，請參閱<xref:security/cors>。</span><span class="sxs-lookup"><span data-stu-id="d4b88-182">For more information, see <xref:security/cors>.</span></span>

## <a name="health-check-options"></a><span data-ttu-id="d4b88-183">健康狀態檢查選項</span><span class="sxs-lookup"><span data-stu-id="d4b88-183">Health check options</span></span>

<span data-ttu-id="d4b88-184"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> 讓您有機會自訂健康狀態檢查行為：</span><span class="sxs-lookup"><span data-stu-id="d4b88-184"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="d4b88-185">篩選健康狀態檢查</span><span class="sxs-lookup"><span data-stu-id="d4b88-185">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="d4b88-186">自訂 HTTP 狀態碼</span><span class="sxs-lookup"><span data-stu-id="d4b88-186">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="d4b88-187">隱藏快取標頭</span><span class="sxs-lookup"><span data-stu-id="d4b88-187">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="d4b88-188">自訂輸出</span><span class="sxs-lookup"><span data-stu-id="d4b88-188">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="d4b88-189">篩選健康狀態檢查</span><span class="sxs-lookup"><span data-stu-id="d4b88-189">Filter health checks</span></span>

<span data-ttu-id="d4b88-190">依預設，健康狀態檢查中介軟體會執行所有已註冊的健康情況檢查。</span><span class="sxs-lookup"><span data-stu-id="d4b88-190">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="d4b88-191">若要執行健康狀態檢查子集，請對 <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> 選項提供傳回布林值的函式。</span><span class="sxs-lookup"><span data-stu-id="d4b88-191">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="d4b88-192">在下列範例中，會依函式條件陳述式中的標籤 (`bar_tag`) 篩選出 `Bar` 健康狀態檢查。只有在健康狀態檢查的 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> 屬性符合 `foo_tag` 或 `baz_tag` 時，才傳回 `true`：</span><span class="sxs-lookup"><span data-stu-id="d4b88-192">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

<span data-ttu-id="d4b88-193">在 `Startup.ConfigureServices` 中：</span><span class="sxs-lookup"><span data-stu-id="d4b88-193">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Foo", () =>
        HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
    .AddCheck("Bar", () =>
        HealthCheckResult.Unhealthy("Bar is unhealthy!"), tags: new[] { "bar_tag" })
    .AddCheck("Baz", () =>
        HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
```

<span data-ttu-id="d4b88-194">在中 `Startup.Configure` ，會 `Predicate` 篩選出「Bar」健康情況檢查。</span><span class="sxs-lookup"><span data-stu-id="d4b88-194">In `Startup.Configure`, the `Predicate` filters out the 'Bar' health check.</span></span> <span data-ttu-id="d4b88-195">只有 Foo 和 Baz execute.：</span><span class="sxs-lookup"><span data-stu-id="d4b88-195">Only Foo and Baz execute.:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
});
```

### <a name="customize-the-http-status-code"></a><span data-ttu-id="d4b88-196">自訂 HTTP 狀態碼</span><span class="sxs-lookup"><span data-stu-id="d4b88-196">Customize the HTTP status code</span></span>

<span data-ttu-id="d4b88-197">您可以使用 <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> 來自訂健康狀態與 HTTP 狀態碼的對應。</span><span class="sxs-lookup"><span data-stu-id="d4b88-197">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="d4b88-198">下列 <xref:Microsoft.AspNetCore.Http.StatusCodes> 指派是中介軟體所使用的預設值。</span><span class="sxs-lookup"><span data-stu-id="d4b88-198">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="d4b88-199">請變更狀態碼值以符合您的需求。</span><span class="sxs-lookup"><span data-stu-id="d4b88-199">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="d4b88-200">在 `Startup.Configure` 中：</span><span class="sxs-lookup"><span data-stu-id="d4b88-200">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResultStatusCodes =
        {
            [HealthStatus.Healthy] = StatusCodes.Status200OK,
            [HealthStatus.Degraded] = StatusCodes.Status200OK,
            [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
        }
    });
});
```

### <a name="suppress-cache-headers"></a><span data-ttu-id="d4b88-201">隱藏快取標頭</span><span class="sxs-lookup"><span data-stu-id="d4b88-201">Suppress cache headers</span></span>

<span data-ttu-id="d4b88-202"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> 控制健全狀況檢查中介軟體是否會將 HTTP 標頭新增至探查回應，以防止回應快取。</span><span class="sxs-lookup"><span data-stu-id="d4b88-202"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="d4b88-203">如果值為 `false` (預設)，則中介軟體會設定或覆寫 `Cache-Control`、`Expires` 和 `Pragma` 標頭，以防止回應快取。</span><span class="sxs-lookup"><span data-stu-id="d4b88-203">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="d4b88-204">如果值為 `true`，則中介軟體不會修改回應的快取標頭。</span><span class="sxs-lookup"><span data-stu-id="d4b88-204">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="d4b88-205">在 `Startup.Configure` 中：</span><span class="sxs-lookup"><span data-stu-id="d4b88-205">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        AllowCachingResponses = false
    });
});
```

### <a name="customize-output"></a><span data-ttu-id="d4b88-206">自訂輸出</span><span class="sxs-lookup"><span data-stu-id="d4b88-206">Customize output</span></span>

<span data-ttu-id="d4b88-207">在中 `Startup.Configure` ，將 [`HealthCheckOptions.ResponseWriter`](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter) 選項設定為用於寫入回應的委派：</span><span class="sxs-lookup"><span data-stu-id="d4b88-207">In `Startup.Configure`, set the [`HealthCheckOptions.ResponseWriter`](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter) option to a delegate for writing the response:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
});
```

<span data-ttu-id="d4b88-208">預設委派會以的字串值寫入最短的純文字回應 [`HealthReport.Status`](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status) 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-208">The default delegate writes a minimal plaintext response with the string value of [`HealthReport.Status`](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="d4b88-209">下列自訂委派會輸出自訂 JSON 回應。</span><span class="sxs-lookup"><span data-stu-id="d4b88-209">The following custom delegates output a custom JSON response.</span></span>

<span data-ttu-id="d4b88-210">範例應用程式中的第一個範例會示範如何使用 <xref:System.Text.Json?displayProperty=fullName> ：</span><span class="sxs-lookup"><span data-stu-id="d4b88-210">The first example from the sample app demonstrates how to use <xref:System.Text.Json?displayProperty=fullName>:</span></span>

[!code-csharp[](health-checks/samples/5.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_SystemTextJson)]

<span data-ttu-id="d4b88-211">第二個範例示範如何使用 [`Newtonsoft.Json`](https://www.nuget.org/packages/Newtonsoft.Json) ：</span><span class="sxs-lookup"><span data-stu-id="d4b88-211">The second example demonstrates how to use [`Newtonsoft.Json`](https://www.nuget.org/packages/Newtonsoft.Json):</span></span>

[!code-csharp[](health-checks/samples/5.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_NewtonSoftJson)]

<span data-ttu-id="d4b88-212">在範例應用程式中，將中的 `SYSTEM_TEXT_JSON` [預處理器](xref:index#preprocessor-directives-in-sample-code) 指示詞批註 `CustomWriterStartup.cs` 為，以啟用的 `Newtonsoft.Json` 版本 `WriteResponse` 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-212">In the sample app, comment out the `SYSTEM_TEXT_JSON` [preprocessor directive](xref:index#preprocessor-directives-in-sample-code) in `CustomWriterStartup.cs` to enable the `Newtonsoft.Json` version of `WriteResponse`.</span></span>

<span data-ttu-id="d4b88-213">健康情況檢查 API 不會提供複雜 JSON 傳回格式的內建支援，因為格式是您選擇的監視系統所特有。</span><span class="sxs-lookup"><span data-stu-id="d4b88-213">The health checks API doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="d4b88-214">視需要自訂上述範例中的回應。</span><span class="sxs-lookup"><span data-stu-id="d4b88-214">Customize the response in the preceding examples as needed.</span></span> <span data-ttu-id="d4b88-215">如需有關 JSON 序列化的詳細資訊 `System.Text.Json` ，請參閱 [如何在 .net 中序列化和還原序列化 json](/dotnet/standard/serialization/system-text-json-how-to)。</span><span class="sxs-lookup"><span data-stu-id="d4b88-215">For more information on JSON serialization with `System.Text.Json`, see [How to serialize and deserialize JSON in .NET](/dotnet/standard/serialization/system-text-json-how-to).</span></span>

## <a name="database-probe"></a><span data-ttu-id="d4b88-216">資料庫探查</span><span class="sxs-lookup"><span data-stu-id="d4b88-216">Database probe</span></span>

<span data-ttu-id="d4b88-217">健康狀態檢查可指定資料庫查詢以布林測試方式來執行，藉此指出資料庫是否正常回應。</span><span class="sxs-lookup"><span data-stu-id="d4b88-217">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="d4b88-218">範例應用程式會使用 [`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) ASP.NET Core 應用程式的健康情況檢查程式庫，對 SQL Server 資料庫執行健康情況檢查。</span><span class="sxs-lookup"><span data-stu-id="d4b88-218">The sample app uses [`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="d4b88-219">`AspNetCore.Diagnostics.HealthChecks` 會對資料庫執行 `SELECT 1` 查詢，以確認資料庫連線狀況良好。</span><span class="sxs-lookup"><span data-stu-id="d4b88-219">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="d4b88-220">使用查詢檢查資料庫連線時，請選擇快速傳回的查詢。</span><span class="sxs-lookup"><span data-stu-id="d4b88-220">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="d4b88-221">查詢方法具有多載資料庫而降低其效能的風險。</span><span class="sxs-lookup"><span data-stu-id="d4b88-221">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="d4b88-222">在大部分情況下，不需要執行測試查詢。</span><span class="sxs-lookup"><span data-stu-id="d4b88-222">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="d4b88-223">只要成功建立資料庫連線就已足夠。</span><span class="sxs-lookup"><span data-stu-id="d4b88-223">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="d4b88-224">如果您發現有必要執行查詢，請選擇簡單的 SELECT 查詢，例如 `SELECT 1`。</span><span class="sxs-lookup"><span data-stu-id="d4b88-224">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="d4b88-225">包含的封裝參考 [`AspNetCore.HealthChecks.SqlServer`](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer) 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-225">Include a package reference to [`AspNetCore.HealthChecks.SqlServer`](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer).</span></span>

<span data-ttu-id="d4b88-226">在範例應用程式的檔案中提供有效的資料庫連接字串 `appsettings.json` 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-226">Supply a valid database connection string in the `appsettings.json` file of the sample app.</span></span> <span data-ttu-id="d4b88-227">應用程式使用名為 `HealthCheckSample` 的 SQL Server 資料庫：</span><span class="sxs-lookup"><span data-stu-id="d4b88-227">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/5.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="d4b88-228">在 `Startup.ConfigureServices` 中，使用 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks%2A> 登錄健康狀態檢查服務。</span><span class="sxs-lookup"><span data-stu-id="d4b88-228">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks%2A> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d4b88-229">範例應用程式會 `AddSqlServer` 使用資料庫的連接字串呼叫方法， (`DbHealthStartup.cs`) ：</span><span class="sxs-lookup"><span data-stu-id="d4b88-229">The sample app calls the `AddSqlServer` method with the database's connection string (`DbHealthStartup.cs`):</span></span>

[!code-csharp[](health-checks/samples/5.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="d4b88-230">健康情況檢查端點是在中呼叫來建立的 `MapHealthChecks` `Startup.Configure` ：</span><span class="sxs-lookup"><span data-stu-id="d4b88-230">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="d4b88-231">若要使用範例應用程式執行資料庫探查案例，請在命令殼層中執行來自專案資料夾的下列命令：</span><span class="sxs-lookup"><span data-stu-id="d4b88-231">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="d4b88-232">[`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) Microsoft 不會維護或支援。</span><span class="sxs-lookup"><span data-stu-id="d4b88-232">[`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="d4b88-233">Entity Framework Core DbContext 探查</span><span class="sxs-lookup"><span data-stu-id="d4b88-233">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="d4b88-234">`DbContext` 檢查會確認應用程式是否可與針對 EF Core `DbContext` 設定的資料庫通訊。</span><span class="sxs-lookup"><span data-stu-id="d4b88-234">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="d4b88-235">應用程式支援 `DbContext` 檢查：</span><span class="sxs-lookup"><span data-stu-id="d4b88-235">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="d4b88-236">使用 [Entity Framework (EF) Core](/ef/core/)。</span><span class="sxs-lookup"><span data-stu-id="d4b88-236">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="d4b88-237">包含的封裝參考 [`Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore`](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-237">Include a package reference to [`Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore`](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore).</span></span>

<span data-ttu-id="d4b88-238">`AddDbContextCheck<TContext>` 會登錄 `DbContext` 的健康狀態檢查。</span><span class="sxs-lookup"><span data-stu-id="d4b88-238">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="d4b88-239">`DbContext` 會以 `TContext` 形式提供給方法。</span><span class="sxs-lookup"><span data-stu-id="d4b88-239">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="d4b88-240">多載可用來設定失敗狀態、標籤和自訂測試查詢。</span><span class="sxs-lookup"><span data-stu-id="d4b88-240">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="d4b88-241">依照預設：</span><span class="sxs-lookup"><span data-stu-id="d4b88-241">By default:</span></span>

* <span data-ttu-id="d4b88-242">`DbContextHealthCheck` 會呼叫 EF Core 的 `CanConnectAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="d4b88-242">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="d4b88-243">您可以自訂使用 `AddDbContextCheck` 方法多載檢查健康狀態時所要執行的作業。</span><span class="sxs-lookup"><span data-stu-id="d4b88-243">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="d4b88-244">健康狀態檢查的名稱是 `TContext` 類型的名稱。</span><span class="sxs-lookup"><span data-stu-id="d4b88-244">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="d4b88-245">在範例應用程式中， `AppDbContext` 會提供給 `AddDbContextCheck` () 中的服務，並將其註冊為服務 `Startup.ConfigureServices` `DbContextHealthStartup.cs` ：</span><span class="sxs-lookup"><span data-stu-id="d4b88-245">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (`DbContextHealthStartup.cs`):</span></span>

[!code-csharp[](health-checks/samples/5.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="d4b88-246">健康情況檢查端點是在中呼叫來建立的 `MapHealthChecks` `Startup.Configure` ：</span><span class="sxs-lookup"><span data-stu-id="d4b88-246">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="d4b88-247">若要使用範例應用程式來執行 `DbContext` 探查案例，請確認 SQL Server 執行個體中沒有連接字串所指定的資料庫。</span><span class="sxs-lookup"><span data-stu-id="d4b88-247">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="d4b88-248">如果資料庫存在，請予以刪除。</span><span class="sxs-lookup"><span data-stu-id="d4b88-248">If the database exists, delete it.</span></span>

<span data-ttu-id="d4b88-249">在命令殼層中執行來自專案資料夾的下列命令：</span><span class="sxs-lookup"><span data-stu-id="d4b88-249">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="d4b88-250">在應用程式執行之後，對瀏覽器中的 `/health` 端點提出要求來檢查健康狀態。</span><span class="sxs-lookup"><span data-stu-id="d4b88-250">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="d4b88-251">資料庫和 `AppDbContext` 不存在，因此應用程式會提供下列回應：</span><span class="sxs-lookup"><span data-stu-id="d4b88-251">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="d4b88-252">觸發範例應用程式以建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="d4b88-252">Trigger the sample app to create the database.</span></span> <span data-ttu-id="d4b88-253">對 `/createdatabase` 提出要求。</span><span class="sxs-lookup"><span data-stu-id="d4b88-253">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="d4b88-254">應用程式會回應：</span><span class="sxs-lookup"><span data-stu-id="d4b88-254">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="d4b88-255">對 `/health` 端點提出要求。</span><span class="sxs-lookup"><span data-stu-id="d4b88-255">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="d4b88-256">資料庫和內容存在，因此應用程式會回應：</span><span class="sxs-lookup"><span data-stu-id="d4b88-256">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="d4b88-257">觸發範例應用程式以刪除資料庫。</span><span class="sxs-lookup"><span data-stu-id="d4b88-257">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="d4b88-258">對 `/deletedatabase` 提出要求。</span><span class="sxs-lookup"><span data-stu-id="d4b88-258">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="d4b88-259">應用程式會回應：</span><span class="sxs-lookup"><span data-stu-id="d4b88-259">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="d4b88-260">對 `/health` 端點提出要求。</span><span class="sxs-lookup"><span data-stu-id="d4b88-260">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="d4b88-261">應用程式會提供狀況不良回應：</span><span class="sxs-lookup"><span data-stu-id="d4b88-261">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="d4b88-262">個別的整備度與活躍度探查</span><span class="sxs-lookup"><span data-stu-id="d4b88-262">Separate readiness and liveness probes</span></span>

<span data-ttu-id="d4b88-263">在某些主控案例中，使用一組健康狀態檢查來區分兩個應用程式狀態：</span><span class="sxs-lookup"><span data-stu-id="d4b88-263">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="d4b88-264">*準備就緒* 表示應用程式是否正常執行，但尚未準備好接收要求。</span><span class="sxs-lookup"><span data-stu-id="d4b88-264">*Readiness* indicates if the app is running normally but isn't ready to receive requests.</span></span>
* <span data-ttu-id="d4b88-265">*活動* 指出應用程式是否已損毀且必須重新開機。</span><span class="sxs-lookup"><span data-stu-id="d4b88-265">*Liveness* indicates if an app has crashed and must be restarted.</span></span>

<span data-ttu-id="d4b88-266">請考慮下列範例：應用程式必須先下載大型設定檔，才能開始處理要求。</span><span class="sxs-lookup"><span data-stu-id="d4b88-266">Consider the following example: An app must download a large configuration file before it's ready to process requests.</span></span> <span data-ttu-id="d4b88-267">如果初始下載失敗，我們不想要重新開機應用程式，因為應用程式可以重試下載檔案數次。</span><span class="sxs-lookup"><span data-stu-id="d4b88-267">We don't want the app to be restarted if the initial download fails because the app can retry downloading the file several times.</span></span> <span data-ttu-id="d4b88-268">我們會使用 *活動探查* 來描述進程的活動，而不會執行其他檢查。</span><span class="sxs-lookup"><span data-stu-id="d4b88-268">We use a *liveness probe* to describe the liveness of the process, no additional checks are performed.</span></span> <span data-ttu-id="d4b88-269">我們也想要避免在設定檔下載成功之前將要求傳送至應用程式。</span><span class="sxs-lookup"><span data-stu-id="d4b88-269">We also want to prevent requests from being sent to the app before the configuration file download has succeeded.</span></span> <span data-ttu-id="d4b88-270">我們會使用 *就緒探查* 來表示「未就緒」狀態，直到下載成功，而且應用程式已準備好接收要求。</span><span class="sxs-lookup"><span data-stu-id="d4b88-270">We use a *readiness probe* to indicate a "not ready" state until the download succeeds and the app is ready to receive requests.</span></span>

<span data-ttu-id="d4b88-271">範例應用程式包含健康狀態檢查，會在[託管服務](xref:fundamentals/host/hosted-services)中長時間執行的啟動工作完成時回報。</span><span class="sxs-lookup"><span data-stu-id="d4b88-271">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="d4b88-272">會 `StartupHostedServiceHealthCheck` 公開一個屬性，也就是裝載的服務在長時間執行的工作 `StartupTaskCompleted` `true` 完成 () 時可以設定的屬性 `StartupHostedServiceHealthCheck.cs` ：</span><span class="sxs-lookup"><span data-stu-id="d4b88-272">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (`StartupHostedServiceHealthCheck.cs`):</span></span>

[!code-csharp[](health-checks/samples/5.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="d4b88-273">長時間執行的背景工作是由 [託管服務](xref:fundamentals/host/hosted-services) () 所啟動 `Services/StartupHostedService` 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-273">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (`Services/StartupHostedService`).</span></span> <span data-ttu-id="d4b88-274">工作完成時，`StartupHostedServiceHealthCheck.StartupTaskCompleted` 會設定為 `true`：</span><span class="sxs-lookup"><span data-stu-id="d4b88-274">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/5.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="d4b88-275">健康狀態檢查是 `Startup.ConfigureServices` 中使用 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck%2A> 隨託管服務一起登錄。</span><span class="sxs-lookup"><span data-stu-id="d4b88-275">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck%2A> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="d4b88-276">因為託管服務必須設定健康情況檢查的屬性，所以健康情況檢查也會在服務容器中註冊 (`LivenessProbeStartup.cs`) ：</span><span class="sxs-lookup"><span data-stu-id="d4b88-276">Because the hosted service must set the property on the health check, the health check is also registered in the service container (`LivenessProbeStartup.cs`):</span></span>

[!code-csharp[](health-checks/samples/5.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="d4b88-277">健康情況檢查端點是藉由在中呼叫來建立 `MapHealthChecks` `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-277">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="d4b88-278">在範例應用程式中，健康狀態檢查端點是在下列位置建立：</span><span class="sxs-lookup"><span data-stu-id="d4b88-278">In the sample app, the health check endpoints are created at:</span></span>

* <span data-ttu-id="d4b88-279">`/health/ready` 適用于準備檢查。</span><span class="sxs-lookup"><span data-stu-id="d4b88-279">`/health/ready` for the readiness check.</span></span> <span data-ttu-id="d4b88-280">整備度檢查使用 `ready` 標籤來篩選健康狀態檢查。</span><span class="sxs-lookup"><span data-stu-id="d4b88-280">The readiness check filters health checks to the health check with the `ready` tag.</span></span>
* <span data-ttu-id="d4b88-281">`/health/live` 適用于活動檢查。</span><span class="sxs-lookup"><span data-stu-id="d4b88-281">`/health/live` for the liveness check.</span></span> <span data-ttu-id="d4b88-282">活動檢查會 `StartupHostedServiceHealthCheck` `false` 在 (中傳回以篩選出 [`HealthCheckOptions.Predicate`](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) 詳細資訊，請參閱 [篩選健康情況檢查](#filter-health-checks)) </span><span class="sxs-lookup"><span data-stu-id="d4b88-282">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [`HealthCheckOptions.Predicate`](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks))</span></span>

<span data-ttu-id="d4b88-283">在下列範例程式碼中：</span><span class="sxs-lookup"><span data-stu-id="d4b88-283">In the following example code:</span></span>

* <span data-ttu-id="d4b88-284">準備就緒檢查會將所有已註冊的檢查都使用「就緒」標記。</span><span class="sxs-lookup"><span data-stu-id="d4b88-284">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="d4b88-285">會 `Predicate` 排除所有檢查並傳回 200-確定。</span><span class="sxs-lookup"><span data-stu-id="d4b88-285">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health/ready", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("ready"),
    });

    endpoints.MapHealthChecks("/health/live", new HealthCheckOptions()
    {
        Predicate = (_) => false
    });
}
```

<span data-ttu-id="d4b88-286">若要使用範例應用程式執行整備度/活躍度組態案例，請在命令殼層中執行來自專案資料夾的下列命令：</span><span class="sxs-lookup"><span data-stu-id="d4b88-286">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="d4b88-287">在瀏覽器中，瀏覽 `/health/ready` 數次，直到經過 15 秒。</span><span class="sxs-lookup"><span data-stu-id="d4b88-287">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="d4b88-288">健康狀態檢查報告前 15 秒為 `Unhealthy`。</span><span class="sxs-lookup"><span data-stu-id="d4b88-288">The health check reports `Unhealthy` for the first 15 seconds.</span></span> <span data-ttu-id="d4b88-289">15 秒之後，端點會報告 `Healthy`，以反映託管服務長時間執行的工作已完成。</span><span class="sxs-lookup"><span data-stu-id="d4b88-289">After 15 seconds, the endpoint reports `Healthy`, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="d4b88-290">此範例也會建立一個以 2 秒延遲執行第一次整備檢查的「健康狀態檢查發行者」(<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 實作)。</span><span class="sxs-lookup"><span data-stu-id="d4b88-290">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="d4b88-291">如需詳細資訊，請參閱[健康狀態檢查發行者](#health-check-publisher)一節。</span><span class="sxs-lookup"><span data-stu-id="d4b88-291">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="d4b88-292">Kubernetes 範例</span><span class="sxs-lookup"><span data-stu-id="d4b88-292">Kubernetes example</span></span>

<span data-ttu-id="d4b88-293">使用個別的整備度與活躍度檢查在 [Kubernetes](https://kubernetes.io/) 之類的環境中很有用。</span><span class="sxs-lookup"><span data-stu-id="d4b88-293">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="d4b88-294">在 Kubernetes 中，應用程式可能需要執行耗時的啟動工作才能接受要求 (例如基礎資料庫可用性測試)。</span><span class="sxs-lookup"><span data-stu-id="d4b88-294">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="d4b88-295">使用個別的檢查可讓協調器區分應用程式是否為正常運作但尚未準備好，或應用程式是否無法啟動。</span><span class="sxs-lookup"><span data-stu-id="d4b88-295">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="d4b88-296">如需 Kubernetes 中整備度與活躍度探查的詳細資訊，請參閱 Kubernetes 文件中的 [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) (設定活躍度與整備度探查)。</span><span class="sxs-lookup"><span data-stu-id="d4b88-296">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="d4b88-297">下列範例示範 Kubernetes 整備度探查組態：</span><span class="sxs-lookup"><span data-stu-id="d4b88-297">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

```yml
spec:
  template:
  spec:
    readinessProbe:
      # an http probe
      httpGet:
        path: /health/ready
        port: 80
      # length of time to wait for a pod to initialize
      # after pod startup, before applying health checking
      initialDelaySeconds: 30
      timeoutSeconds: 1
    ports:
      - containerPort: 80
```

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="d4b88-298">透過自訂回應寫入器的計量型探查</span><span class="sxs-lookup"><span data-stu-id="d4b88-298">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="d4b88-299">範例應用程式示範透過自訂回應寫入器的記憶體健康狀態檢查。</span><span class="sxs-lookup"><span data-stu-id="d4b88-299">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="d4b88-300">如果應用程式使用超過指定的記憶體閾值 (在範例應用程式中為 1 GB)，`MemoryHealthCheck` 會報告降級的狀態。</span><span class="sxs-lookup"><span data-stu-id="d4b88-300">`MemoryHealthCheck` reports a degraded status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="d4b88-301"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>包含應用程式 () 的垃圾收集行程 (GC) 資訊 `MemoryHealthCheck.cs` ：</span><span class="sxs-lookup"><span data-stu-id="d4b88-301">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (`MemoryHealthCheck.cs`):</span></span>

[!code-csharp[](health-checks/samples/5.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="d4b88-302">在 `Startup.ConfigureServices` 中，使用 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks%2A> 登錄健康狀態檢查服務。</span><span class="sxs-lookup"><span data-stu-id="d4b88-302">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks%2A> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d4b88-303">`MemoryHealthCheck` 會登錄為服務，而不是將健康狀態檢查傳遞至 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck%2A> 以啟用檢查。</span><span class="sxs-lookup"><span data-stu-id="d4b88-303">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck%2A>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="d4b88-304">所有 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 登錄的服務都可供健康狀態檢查服務和中介軟體使用。</span><span class="sxs-lookup"><span data-stu-id="d4b88-304">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="d4b88-305">建議將健康狀態檢查服務登錄為單一服務。</span><span class="sxs-lookup"><span data-stu-id="d4b88-305">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="d4b88-306">在 `CustomWriterStartup.cs` 範例應用程式中：</span><span class="sxs-lookup"><span data-stu-id="d4b88-306">In `CustomWriterStartup.cs` of the sample app:</span></span>

[!code-csharp[](health-checks/samples/5.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="d4b88-307">健康情況檢查端點是藉由在中呼叫來建立 `MapHealthChecks` `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-307">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="d4b88-308">`WriteResponse`當健康狀態檢查執行時，會將委派提供給 <的 AspNetCore. HealthChecks. HealthCheckOptions. ResponseWriter> 屬性以輸出自訂 JSON 回應：</span><span class="sxs-lookup"><span data-stu-id="d4b88-308">A `WriteResponse` delegate is provided to the <Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> property to output a custom JSON response when the health check executes:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
}
```

<span data-ttu-id="d4b88-309">委派會將 `WriteResponse` 格式化為 `CompositeHealthCheckResult` json 物件，並產生健康情況檢查回應的 json 輸出。</span><span class="sxs-lookup"><span data-stu-id="d4b88-309">The `WriteResponse` delegate formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response.</span></span> <span data-ttu-id="d4b88-310">如需詳細資訊，請參閱 [自訂輸出](#customize-output) 一節。</span><span class="sxs-lookup"><span data-stu-id="d4b88-310">For more information, see the [Customize output](#customize-output) section.</span></span>

<span data-ttu-id="d4b88-311">若要使用範例應用程式透過自訂回應寫入器輸出執行計量型探查，請在命令殼層中執行來自專案資料夾的下列命令：</span><span class="sxs-lookup"><span data-stu-id="d4b88-311">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="d4b88-312">[`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 包含以計量為基礎的健康情況檢查案例，包括磁片儲存體和最大值活動檢查。</span><span class="sxs-lookup"><span data-stu-id="d4b88-312">[`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="d4b88-313">[`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) Microsoft 不會維護或支援。</span><span class="sxs-lookup"><span data-stu-id="d4b88-313">[`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="d4b88-314">依連接埠篩選</span><span class="sxs-lookup"><span data-stu-id="d4b88-314">Filter by port</span></span>

<span data-ttu-id="d4b88-315">`RequireHost` `MapHealthChecks` 使用指定埠的 URL 模式呼叫，以將健康情況檢查要求限制為指定的埠。</span><span class="sxs-lookup"><span data-stu-id="d4b88-315">Call `RequireHost` on `MapHealthChecks` with a URL pattern that specifies a port to restrict health check requests to the port specified.</span></span> <span data-ttu-id="d4b88-316">這通常會用於容器環境，以公開監視服務的連接埠。</span><span class="sxs-lookup"><span data-stu-id="d4b88-316">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="d4b88-317">範例應用程式使用[環境變數組態提供者](xref:fundamentals/configuration/index#environment-variables)來設定連接埠。</span><span class="sxs-lookup"><span data-stu-id="d4b88-317">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables).</span></span> <span data-ttu-id="d4b88-318">埠是在檔案中設定 `launchSettings.json` ，並透過環境變數傳遞至設定提供者。</span><span class="sxs-lookup"><span data-stu-id="d4b88-318">The port is set in the `launchSettings.json` file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="d4b88-319">您也必須將伺服器設定為在管理連接埠上接聽要求。</span><span class="sxs-lookup"><span data-stu-id="d4b88-319">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="d4b88-320">若要使用範例應用程式來示範管理埠設定，請 `launchSettings.json` 在資料夾中建立檔案 `Properties` 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-320">To use the sample app to demonstrate management port configuration, create the `launchSettings.json` file in a `Properties` folder.</span></span>

<span data-ttu-id="d4b88-321">範例應用 `Properties/launchSettings.json` 程式中的下列檔案未包含在範例應用程式的專案檔中，必須以手動方式建立：</span><span class="sxs-lookup"><span data-stu-id="d4b88-321">The following `Properties/launchSettings.json` file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

```json
{
  "profiles": {
    "SampleApp": {
      "commandName": "Project",
      "commandLineArgs": "",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_URLS": "http://localhost:5000/;http://localhost:5001/",
        "ASPNETCORE_MANAGEMENTPORT": "5001"
      },
      "applicationUrl": "http://localhost:5000/"
    }
  }
}
```

<span data-ttu-id="d4b88-322">在 `Startup.ConfigureServices` 中，使用 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks%2A> 登錄健康狀態檢查服務。</span><span class="sxs-lookup"><span data-stu-id="d4b88-322">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks%2A> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d4b88-323">藉由在中呼叫來建立健康情況檢查端點 `MapHealthChecks` `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-323">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="d4b88-324">在範例應用程式中，對 `RequireHost` 中端點的呼叫會 `Startup.Configure` 從設定中指定管理埠：</span><span class="sxs-lookup"><span data-stu-id="d4b88-324">In the sample app, a call to `RequireHost` on the endpoint in `Startup.Configure` specifies the management port from configuration:</span></span>

```csharp
endpoints.MapHealthChecks("/health")
    .RequireHost($"*:{Configuration["ManagementPort"]}");
```

<span data-ttu-id="d4b88-325">端點是在的範例應用程式中建立的 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-325">Endpoints are created in the sample app in `Startup.Configure`.</span></span> <span data-ttu-id="d4b88-326">在下列範例程式碼中：</span><span class="sxs-lookup"><span data-stu-id="d4b88-326">In the following example code:</span></span>

* <span data-ttu-id="d4b88-327">準備就緒檢查會將所有已註冊的檢查都使用「就緒」標記。</span><span class="sxs-lookup"><span data-stu-id="d4b88-327">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="d4b88-328">會 `Predicate` 排除所有檢查並傳回 200-確定。</span><span class="sxs-lookup"><span data-stu-id="d4b88-328">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health/ready", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("ready"),
    });

    endpoints.MapHealthChecks("/health/live", new HealthCheckOptions()
    {
        Predicate = (_) => false
    });
}
```

> [!NOTE]
> <span data-ttu-id="d4b88-329">您可以在 `launchSettings.json` 程式碼中明確設定管理埠，以避免在範例應用程式中建立檔案。</span><span class="sxs-lookup"><span data-stu-id="d4b88-329">You can avoid creating the `launchSettings.json` file in the sample app by setting the management port explicitly in code.</span></span> <span data-ttu-id="d4b88-330">在 `Program.cs` 建立的位置中 <xref:Microsoft.Extensions.Hosting.HostBuilder> ，新增對的呼叫， <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP%2A> 並提供應用程式的管理埠端點。</span><span class="sxs-lookup"><span data-stu-id="d4b88-330">In `Program.cs` where the <xref:Microsoft.Extensions.Hosting.HostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP%2A> and provide the app's management port endpoint.</span></span> <span data-ttu-id="d4b88-331">在 `Configure` 中 `ManagementPortStartup.cs` ，使用下列內容指定管理埠 `RequireHost` ：</span><span class="sxs-lookup"><span data-stu-id="d4b88-331">In `Configure` of `ManagementPortStartup.cs`, specify the management port with `RequireHost`:</span></span>
>
> <span data-ttu-id="d4b88-332">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="d4b88-332">`Program.cs`:</span></span>
>
> ```csharp
> return new HostBuilder()
>     .ConfigureWebHostDefaults(webBuilder =>
>     {
>         webBuilder.UseKestrel()
>             .ConfigureKestrel(serverOptions =>
>             {
>                 serverOptions.ListenAnyIP(5001);
>             })
>             .UseStartup(startupType);
>     })
>     .Build();
> ```
>
> <span data-ttu-id="d4b88-333">`ManagementPortStartup.cs`:</span><span class="sxs-lookup"><span data-stu-id="d4b88-333">`ManagementPortStartup.cs`:</span></span>
>
> ```csharp
> app.UseEndpoints(endpoints =>
> {
>     endpoints.MapHealthChecks("/health").RequireHost("*:5001");
> });
> ```

<span data-ttu-id="d4b88-334">若要使用範例應用程式執行管理連接埠組態案例，請在命令殼層中執行來自專案資料夾的下列命令：</span><span class="sxs-lookup"><span data-stu-id="d4b88-334">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="d4b88-335">發佈健康狀態檢查程式庫</span><span class="sxs-lookup"><span data-stu-id="d4b88-335">Distribute a health check library</span></span>

<span data-ttu-id="d4b88-336">若要發佈健康狀態檢查作為程式庫：</span><span class="sxs-lookup"><span data-stu-id="d4b88-336">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="d4b88-337">寫入健康狀態檢查，將 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 介面當做獨立類別來實作。</span><span class="sxs-lookup"><span data-stu-id="d4b88-337">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="d4b88-338">此類別可能依賴[相依性插入 (DI)](xref:fundamentals/dependency-injection)、類型啟用和[具名選項](xref:fundamentals/configuration/options)來存取組態資料。</span><span class="sxs-lookup"><span data-stu-id="d4b88-338">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="d4b88-339">在健康狀態檢查邏輯中 `CheckHealthAsync` ：</span><span class="sxs-lookup"><span data-stu-id="d4b88-339">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="d4b88-340">`data1` 和 `data2` 在方法中用來執行探查的健康情況檢查邏輯。</span><span class="sxs-lookup"><span data-stu-id="d4b88-340">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="d4b88-341">`AccessViolationException` 會進行處理。</span><span class="sxs-lookup"><span data-stu-id="d4b88-341">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="d4b88-342">當 <xref:System.AccessViolationException> 發生時， <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> 會傳回， <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> 以允許使用者設定健康情況檢查失敗狀態。</span><span class="sxs-lookup"><span data-stu-id="d4b88-342">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

   ```csharp
   using System;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   namespace SampleApp
   {
       public class ExampleHealthCheck : IHealthCheck
       {
           private readonly string _data1;
           private readonly int? _data2;

           public ExampleHealthCheck(string data1, int? data2)
           {
               _data1 = data1 ?? throw new ArgumentNullException(nameof(data1));
               _data2 = data2 ?? throw new ArgumentNullException(nameof(data2));
           }

           public async Task<HealthCheckResult> CheckHealthAsync(
               HealthCheckContext context, CancellationToken cancellationToken)
           {
               try
               {
                   return HealthCheckResult.Healthy();
               }
               catch (AccessViolationException ex)
               {
                   return new HealthCheckResult(
                       context.Registration.FailureStatus,
                       description: "An access violation occurred during the check.",
                       exception: ex,
                       data: null);
               }
           }
       }
   }
   ```

1. <span data-ttu-id="d4b88-343">使用取用應用程式在其 `Startup.Configure` 方法中呼叫的參數，來寫入延伸模組。</span><span class="sxs-lookup"><span data-stu-id="d4b88-343">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="d4b88-344">在下列範例中，假設健康狀態檢查方法簽章如下：</span><span class="sxs-lookup"><span data-stu-id="d4b88-344">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="d4b88-345">上述簽章指出 `ExampleHealthCheck` 需要額外的資料，才能處理健康狀態檢查探查邏輯。</span><span class="sxs-lookup"><span data-stu-id="d4b88-345">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="d4b88-346">此資料會提供給委派，以在使用延伸模組登錄健康狀態檢查時，用來建立健康狀態檢查執行個體。</span><span class="sxs-lookup"><span data-stu-id="d4b88-346">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="d4b88-347">在下列範例中，呼叫者會選擇性地指定：</span><span class="sxs-lookup"><span data-stu-id="d4b88-347">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="d4b88-348">健康狀態檢查名稱 (`name`)。</span><span class="sxs-lookup"><span data-stu-id="d4b88-348">health check name (`name`).</span></span> <span data-ttu-id="d4b88-349">如果為 `null`，則會使用 `example_health_check`。</span><span class="sxs-lookup"><span data-stu-id="d4b88-349">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="d4b88-350">健康狀態檢查的字串資料點 (`data1`)。</span><span class="sxs-lookup"><span data-stu-id="d4b88-350">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="d4b88-351">健康狀態檢查的整數資料點 (`data2`)。</span><span class="sxs-lookup"><span data-stu-id="d4b88-351">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="d4b88-352">如果為 `null`，則會使用 `1`。</span><span class="sxs-lookup"><span data-stu-id="d4b88-352">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="d4b88-353">失敗狀態 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>)。</span><span class="sxs-lookup"><span data-stu-id="d4b88-353">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="d4b88-354">預設為 `null`。</span><span class="sxs-lookup"><span data-stu-id="d4b88-354">The default is `null`.</span></span> <span data-ttu-id="d4b88-355">如果為 `null` ， [`HealthStatus.Unhealthy`](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) 則回報失敗狀態。</span><span class="sxs-lookup"><span data-stu-id="d4b88-355">If `null`, [`HealthStatus.Unhealthy`](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="d4b88-356">標籤 (`IEnumerable<string>`)。</span><span class="sxs-lookup"><span data-stu-id="d4b88-356">tags (`IEnumerable<string>`).</span></span>

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string DefaultName = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder,
           string name = default,
           string data1,
           int data2 = 1,
           HealthStatus? failureStatus = default,
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? DefaultName,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a><span data-ttu-id="d4b88-357">健康狀態檢查發行者</span><span class="sxs-lookup"><span data-stu-id="d4b88-357">Health Check Publisher</span></span>

<span data-ttu-id="d4b88-358">當 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 新增至服務容器時，健康狀態檢查系統會定期執行健康狀態檢查，並呼叫 `PublishAsync` 傳回結果。</span><span class="sxs-lookup"><span data-stu-id="d4b88-358">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="d4b88-359">這對推送型健康狀態監控系統案例很有用，其預期每個處理序會定期呼叫監控系統來判斷健康狀態。</span><span class="sxs-lookup"><span data-stu-id="d4b88-359">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="d4b88-360"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 介面有單一方法：</span><span class="sxs-lookup"><span data-stu-id="d4b88-360">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="d4b88-361"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> 可讓您設定：</span><span class="sxs-lookup"><span data-stu-id="d4b88-361"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="d4b88-362"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>：在執行實例之前，應用程式啟動後所套用的初始延遲 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-362"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="d4b88-363">在啟動後就會套用延遲，但不會套用至後續的反覆項目。</span><span class="sxs-lookup"><span data-stu-id="d4b88-363">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="d4b88-364">預設值是五秒鐘。</span><span class="sxs-lookup"><span data-stu-id="d4b88-364">The default value is five seconds.</span></span>
* <span data-ttu-id="d4b88-365"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>：執行的期間 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-365"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="d4b88-366">預設值為 30 秒。</span><span class="sxs-lookup"><span data-stu-id="d4b88-366">The default value is 30 seconds.</span></span>
* <span data-ttu-id="d4b88-367"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>：如果 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> `null` (預設) ，健全狀況檢查發行者服務會執行所有已註冊的健康情況檢查。</span><span class="sxs-lookup"><span data-stu-id="d4b88-367"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="d4b88-368">若要執行一部分的健康狀態檢查，請提供可篩選該組檢查的函式。</span><span class="sxs-lookup"><span data-stu-id="d4b88-368">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="d4b88-369">每個期間都會評估該述詞。</span><span class="sxs-lookup"><span data-stu-id="d4b88-369">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="d4b88-370"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>：針對所有實例執行健康情況檢查的超時時間 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-370"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="d4b88-371">若要在沒有逾時的情況下執行，請使用 <xref:System.Threading.Timeout.InfiniteTimeSpan>。</span><span class="sxs-lookup"><span data-stu-id="d4b88-371">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="d4b88-372">預設值為 30 秒。</span><span class="sxs-lookup"><span data-stu-id="d4b88-372">The default value is 30 seconds.</span></span>

<span data-ttu-id="d4b88-373">在範例應用程式中，`ReadinessPublisher` 是一個 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 實作。</span><span class="sxs-lookup"><span data-stu-id="d4b88-373">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="d4b88-374">記錄層級的每個檢查都會記錄健全狀況檢查狀態：</span><span class="sxs-lookup"><span data-stu-id="d4b88-374">The health check status is logged for each check at a log level of:</span></span>

* <span data-ttu-id="d4b88-375"><xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation%2A>如果健康狀態檢查狀態為， () 資訊 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy> 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-375">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation%2A>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="d4b88-376"><xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>當狀態為或時，)  (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> 錯誤 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy> 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-376">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/5.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="d4b88-377">在範例應用程式的 `LivenessProbeStartup` 範例中，`StartupHostedService` 整備檢查具有 2 秒的啟動延遲，且每 30 秒會執行一次檢查。</span><span class="sxs-lookup"><span data-stu-id="d4b88-377">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="d4b88-378">為了啟用 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 實作，此範例會在[相依性插入 (DI)](xref:fundamentals/dependency-injection) 容器中將 `ReadinessPublisher` 註冊為單一服務：</span><span class="sxs-lookup"><span data-stu-id="d4b88-378">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/5.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

> [!NOTE]
> <span data-ttu-id="d4b88-379">[`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 包含數個系統的發行者，包括 [Application Insights](/azure/application-insights/app-insights-overview)。</span><span class="sxs-lookup"><span data-stu-id="d4b88-379">[`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="d4b88-380">[`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) Microsoft 不會維護或支援。</span><span class="sxs-lookup"><span data-stu-id="d4b88-380">[`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="d4b88-381">利用 MapWhen 限制健康情況檢查</span><span class="sxs-lookup"><span data-stu-id="d4b88-381">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="d4b88-382">使用 <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> 來有條件地將健康情況檢查端點的要求管線分支。</span><span class="sxs-lookup"><span data-stu-id="d4b88-382">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="d4b88-383">在下列範例中， `MapWhen` 如果收到端點的 GET 要求，則會將要求管線分支以啟用健康情況檢查中介軟體 `api/HealthCheck` ：</span><span class="sxs-lookup"><span data-stu-id="d4b88-383">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseEndpoints(endpoints =>
{
    endpoints.MapRazorPages();
});
```

<span data-ttu-id="d4b88-384">如需詳細資訊，請參閱<xref:fundamentals/middleware/index#branch-the-middleware-pipeline>。</span><span class="sxs-lookup"><span data-stu-id="d4b88-384">For more information, see <xref:fundamentals/middleware/index#branch-the-middleware-pipeline>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="d4b88-385">ASP.NET Core 提供健康狀態檢查中介軟體和程式庫，以報告應用程式基礎結構元件的健康情況。</span><span class="sxs-lookup"><span data-stu-id="d4b88-385">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="d4b88-386">應用程式會將健康狀態檢查公開為 HTTP 端點。</span><span class="sxs-lookup"><span data-stu-id="d4b88-386">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="d4b88-387">您可以針對各種即時監控案例來設定健康狀態檢查端點：</span><span class="sxs-lookup"><span data-stu-id="d4b88-387">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="d4b88-388">容器協調器和負載平衡器可以使用健康狀態探查，來檢查應用程式的狀態。</span><span class="sxs-lookup"><span data-stu-id="d4b88-388">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="d4b88-389">例如，容器協調器可能會暫停輪流部署或重新啟動容器，來回應失敗的健康狀態檢查。</span><span class="sxs-lookup"><span data-stu-id="d4b88-389">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="d4b88-390">負載平衡器可能會將流量從失敗的執行個體路由傳送至狀況良好的執行個體，來回應狀況不良的應用程式。</span><span class="sxs-lookup"><span data-stu-id="d4b88-390">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="d4b88-391">您可以監控所使用記憶體、磁碟及其他實體伺服器資源的健康狀態。</span><span class="sxs-lookup"><span data-stu-id="d4b88-391">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="d4b88-392">健康狀態檢查可以測試應用程式的相依性 (例如資料庫和外部服務端點)，確認其是否可用且正常運作。</span><span class="sxs-lookup"><span data-stu-id="d4b88-392">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="d4b88-393">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/health-checks/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="d4b88-393">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d4b88-394">範例應用程式包含本主題中所述的案例範例。</span><span class="sxs-lookup"><span data-stu-id="d4b88-394">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="d4b88-395">若要在指定的案例中執行範例應用程式，請在命令殼層中使用來自專案資料夾的 [dotnet run](/dotnet/core/tools/dotnet-run) 命令。</span><span class="sxs-lookup"><span data-stu-id="d4b88-395">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="d4b88-396">`README.md`如需如何使用範例應用程式的詳細資訊，請參閱本主題中的範例應用程式檔案和案例描述。</span><span class="sxs-lookup"><span data-stu-id="d4b88-396">See the sample app's `README.md` file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d4b88-397">必要條件</span><span class="sxs-lookup"><span data-stu-id="d4b88-397">Prerequisites</span></span>

<span data-ttu-id="d4b88-398">健康狀態檢查通常會搭配使用外部監視服務或容器協調器，來檢查應用程式的狀態。</span><span class="sxs-lookup"><span data-stu-id="d4b88-398">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="d4b88-399">將健康狀態檢查新增至應用程式之前，請決定要使用的監控系統。</span><span class="sxs-lookup"><span data-stu-id="d4b88-399">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="d4b88-400">監控系統會指定要建立哪些健康狀態檢查類型，以及如何設定其端點。</span><span class="sxs-lookup"><span data-stu-id="d4b88-400">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="d4b88-401">[`Microsoft.AspNetCore.Diagnostics.HealthChecks`](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks)ASP.NET Core 的應用程式會隱含參考此套件。</span><span class="sxs-lookup"><span data-stu-id="d4b88-401">The [`Microsoft.AspNetCore.Diagnostics.HealthChecks`](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package is referenced implicitly for ASP.NET Core apps.</span></span> <span data-ttu-id="d4b88-402">若要使用 Entity Framework Core 執行健康情況檢查，請將封裝參考新增至 [`Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore`](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) 套件。</span><span class="sxs-lookup"><span data-stu-id="d4b88-402">To perform health checks using Entity Framework Core, add a package reference to the [`Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore`](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) package.</span></span>

<span data-ttu-id="d4b88-403">範例應用程式提供啟動程式碼，來示範數個案例的健康狀態檢查。</span><span class="sxs-lookup"><span data-stu-id="d4b88-403">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="d4b88-404">[資料庫探查](#database-probe)案例會使用來檢查資料庫連接的健全狀況 [`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-404">The [database probe](#database-probe) scenario checks the health of a database connection using [`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="d4b88-405">[DbContext 探查](#entity-framework-core-dbcontext-probe)案例使用 EF Core `DbContext` 來檢查資料庫。</span><span class="sxs-lookup"><span data-stu-id="d4b88-405">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="d4b88-406">為了探索資料庫案例，範例應用程式會：</span><span class="sxs-lookup"><span data-stu-id="d4b88-406">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="d4b88-407">建立資料庫，並在檔案中提供其連接字串 `appsettings.json` 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-407">Creates a database and provides its connection string in the `appsettings.json` file.</span></span>
* <span data-ttu-id="d4b88-408">在其專案檔中具有下列套件參考：</span><span class="sxs-lookup"><span data-stu-id="d4b88-408">Has the following package references in its project file:</span></span>
  * [`AspNetCore.HealthChecks.SqlServer`](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer)
  * [`Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore`](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore)

> [!NOTE]
> <span data-ttu-id="d4b88-409">[`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) Microsoft 不會維護或支援。</span><span class="sxs-lookup"><span data-stu-id="d4b88-409">[`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="d4b88-410">另一個健康狀態檢查案例示範如何將健康狀態檢查篩選至管理連接埠。</span><span class="sxs-lookup"><span data-stu-id="d4b88-410">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="d4b88-411">範例應用程式會要求您建立 `Properties/launchSettings.json` 包含管理 URL 和管理埠的檔案。</span><span class="sxs-lookup"><span data-stu-id="d4b88-411">The sample app requires you to create a `Properties/launchSettings.json` file that includes the management URL and management port.</span></span> <span data-ttu-id="d4b88-412">如需詳細資訊，請參閱[依連接埠篩選](#filter-by-port)一節。</span><span class="sxs-lookup"><span data-stu-id="d4b88-412">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="d4b88-413">基本健康狀態探查</span><span class="sxs-lookup"><span data-stu-id="d4b88-413">Basic health probe</span></span>

<span data-ttu-id="d4b88-414">對於許多應用程式，報告應用程式是否可處理要求的基本健康狀態探查組態 (「活躍度」)，便足以探索應用程式的狀態。</span><span class="sxs-lookup"><span data-stu-id="d4b88-414">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="d4b88-415">基本設定會註冊健康情況檢查服務並呼叫健康情況檢查中介軟體，以在具有健康回應的 URL 端點回應。</span><span class="sxs-lookup"><span data-stu-id="d4b88-415">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="d4b88-416">預設並未登錄特定健康狀態檢查來測試任何特定相依性或子系統。</span><span class="sxs-lookup"><span data-stu-id="d4b88-416">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="d4b88-417">如果應用程式能夠在健康狀態端點 URL 做出回應，則視為狀況良好。</span><span class="sxs-lookup"><span data-stu-id="d4b88-417">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="d4b88-418">預設回應寫入器會將狀態 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) 作為純文字回應寫入用戶端，表示 [`HealthStatus.Healthy`](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) 、 [`HealthStatus.Degraded`](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) 或 [`HealthStatus.Unhealthy`](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) 狀態。</span><span class="sxs-lookup"><span data-stu-id="d4b88-418">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [`HealthStatus.Healthy`](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [`HealthStatus.Degraded`](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), or [`HealthStatus.Unhealthy`](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="d4b88-419">在 `Startup.ConfigureServices` 中，使用 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks%2A> 登錄健康狀態檢查服務。</span><span class="sxs-lookup"><span data-stu-id="d4b88-419">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks%2A> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d4b88-420">藉由在中呼叫來建立健康情況檢查端點 `MapHealthChecks` `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-420">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="d4b88-421">在範例應用程式中，健康狀態檢查端點是在 `/health` (`BasicStartup.cs`) 建立的：</span><span class="sxs-lookup"><span data-stu-id="d4b88-421">In the sample app, the health check endpoint is created at `/health` (`BasicStartup.cs`):</span></span>

```csharp
public class BasicStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddHealthChecks();
    }

    public void Configure(IApplicationBuilder app)
    {
        app.UseRouting();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHealthChecks("/health");
        });
    }
}
```

<span data-ttu-id="d4b88-422">若要使用範例應用程式執行基本組態案例，請在命令殼層中執行來自專案資料夾的下列命令：</span><span class="sxs-lookup"><span data-stu-id="d4b88-422">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="d4b88-423">Docker 範例</span><span class="sxs-lookup"><span data-stu-id="d4b88-423">Docker example</span></span>

<span data-ttu-id="d4b88-424">[Docker](xref:host-and-deploy/docker/index) 提供內建 `HEALTHCHECK` 指示詞，可用來檢查使用基本健康狀態檢查組態的應用程式狀態：</span><span class="sxs-lookup"><span data-stu-id="d4b88-424">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="d4b88-425">建立健康狀態檢查</span><span class="sxs-lookup"><span data-stu-id="d4b88-425">Create health checks</span></span>

<span data-ttu-id="d4b88-426">健康狀態檢查是藉由實作 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 介面來建立。</span><span class="sxs-lookup"><span data-stu-id="d4b88-426">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="d4b88-427"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync%2A> 方法會傳回 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>，指出健康狀態為 `Healthy`、`Degraded` 或 `Unhealthy`。</span><span class="sxs-lookup"><span data-stu-id="d4b88-427">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync%2A> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="d4b88-428">結果會寫成具有可設定狀態碼的純文字回應 ([健康狀態檢查選項](#health-check-options)一節中將說明如何進行組態)。</span><span class="sxs-lookup"><span data-stu-id="d4b88-428">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="d4b88-429"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> 也可以傳回選擇性索引鍵/值組。</span><span class="sxs-lookup"><span data-stu-id="d4b88-429"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

<span data-ttu-id="d4b88-430">下列 `ExampleHealthCheck` 類別示範健康情況檢查的版面配置。</span><span class="sxs-lookup"><span data-stu-id="d4b88-430">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="d4b88-431">健康情況檢查邏輯會放置在 `CheckHealthAsync` 方法中。</span><span class="sxs-lookup"><span data-stu-id="d4b88-431">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="d4b88-432">下列範例會將虛擬變數（）設定 `healthCheckResultHealthy` 為 `true` 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-432">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="d4b88-433">如果的值 `healthCheckResultHealthy` 設定為 `false` ，則 [`HealthCheckResult.Unhealthy`](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy%2A) 會傳回狀態。</span><span class="sxs-lookup"><span data-stu-id="d4b88-433">If the value of `healthCheckResultHealthy` is set to `false`, the [`HealthCheckResult.Unhealthy`](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy%2A) status is returned.</span></span>

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        var healthCheckResultHealthy = true;

        if (healthCheckResultHealthy)
        {
            return Task.FromResult(
                HealthCheckResult.Healthy("A healthy result."));
        }

        return Task.FromResult(
            HealthCheckResult.Unhealthy("An unhealthy result."));
    }
}
```

## <a name="register-health-check-services"></a><span data-ttu-id="d4b88-434">登錄健康狀態檢查服務</span><span class="sxs-lookup"><span data-stu-id="d4b88-434">Register health check services</span></span>

<span data-ttu-id="d4b88-435">此 `ExampleHealthCheck` 類型會新增至具有下列功能的健康情況檢查服務 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck%2A> `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="d4b88-435">The `ExampleHealthCheck` type is added to health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck%2A> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="d4b88-436">下列範例中所顯示的 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck%2A> 多載會設定在健康狀態檢查報告失敗時所要報告的失敗狀態 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>)。</span><span class="sxs-lookup"><span data-stu-id="d4b88-436">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck%2A> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="d4b88-437">如果失敗狀態設定為 `null` (預設) ， [`HealthStatus.Unhealthy`](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) 則會回報。</span><span class="sxs-lookup"><span data-stu-id="d4b88-437">If the failure status is set to `null` (default), [`HealthStatus.Unhealthy`](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="d4b88-438">此多載對程式庫作者很有用。若健康狀態檢查實作採用此設定，則當健康狀態檢查失敗時，應用程式就會強制程式庫指出失敗狀態。</span><span class="sxs-lookup"><span data-stu-id="d4b88-438">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="d4b88-439">您可以使用「標籤」來篩選健康狀態檢查 ([篩選健康狀態檢查](#filter-health-checks)一節中將進一步說明)。</span><span class="sxs-lookup"><span data-stu-id="d4b88-439">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="d4b88-440"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck%2A> 也可以執行匿名函式。</span><span class="sxs-lookup"><span data-stu-id="d4b88-440"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck%2A> can also execute a lambda function.</span></span> <span data-ttu-id="d4b88-441">在下列範例中，健康狀態檢查名稱指定為 `Example`，且檢查一律會傳回狀況良好狀態：</span><span class="sxs-lookup"><span data-stu-id="d4b88-441">In the following example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

<span data-ttu-id="d4b88-442">呼叫 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck%2A> 以將引數傳遞至健康情況檢查執行。</span><span class="sxs-lookup"><span data-stu-id="d4b88-442">Call <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck%2A> to pass arguments to a health check implementation.</span></span> <span data-ttu-id="d4b88-443">在下列範例中， `TestHealthCheckWithArgs` 會接受一個整數和一個在呼叫時使用的字串 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync%2A> ：</span><span class="sxs-lookup"><span data-stu-id="d4b88-443">In the following example, `TestHealthCheckWithArgs` accepts an integer and a string for use when <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync%2A> is called:</span></span>

```csharp
private class TestHealthCheckWithArgs : IHealthCheck
{
    public TestHealthCheckWithArgs(int i, string s)
    {
        I = i;
        S = s;
    }

    public int I { get; set; }

    public string S { get; set; }

    public Task<HealthCheckResult> CheckHealthAsync(HealthCheckContext context, 
        CancellationToken cancellationToken = default)
    {
        ...
    }
}
```

<span data-ttu-id="d4b88-444">`TestHealthCheckWithArgs` 的註冊方式是 `AddTypeActivatedCheck` 以傳遞至實作為的整數和字串來呼叫：</span><span class="sxs-lookup"><span data-stu-id="d4b88-444">`TestHealthCheckWithArgs` is registered by calling `AddTypeActivatedCheck` with the integer and string passed to the implementation:</span></span>

```csharp
services.AddHealthChecks()
    .AddTypeActivatedCheck<TestHealthCheckWithArgs>(
        "test", 
        failureStatus: HealthStatus.Degraded, 
        tags: new[] { "example" }, 
        args: new object[] { 5, "string" });
```

## <a name="use-health-checks-routing"></a><span data-ttu-id="d4b88-445">使用健康情況檢查路由</span><span class="sxs-lookup"><span data-stu-id="d4b88-445">Use Health Checks Routing</span></span>

<span data-ttu-id="d4b88-446">在中 `Startup.Configure` ， `MapHealthChecks` 使用端點 URL 或相對路徑呼叫端點產生器：</span><span class="sxs-lookup"><span data-stu-id="d4b88-446">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

### <a name="require-host"></a><span data-ttu-id="d4b88-447">需要主機</span><span class="sxs-lookup"><span data-stu-id="d4b88-447">Require host</span></span>

<span data-ttu-id="d4b88-448">呼叫 `RequireHost` 以指定一或多個允許的主機作為健康情況檢查端點。</span><span class="sxs-lookup"><span data-stu-id="d4b88-448">Call `RequireHost` to specify one or more permitted hosts for the health check endpoint.</span></span> <span data-ttu-id="d4b88-449">主機應該是 Unicode，而不是 punycode，而且可能包含埠。</span><span class="sxs-lookup"><span data-stu-id="d4b88-449">Hosts should be Unicode rather than punycode and may include a port.</span></span> <span data-ttu-id="d4b88-450">如果未提供集合，則會接受任何主機。</span><span class="sxs-lookup"><span data-stu-id="d4b88-450">If a collection isn't supplied, any host is accepted.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireHost("www.contoso.com:5001");
});
```

<span data-ttu-id="d4b88-451">如需詳細資訊，請參閱[依連接埠篩選](#filter-by-port)一節。</span><span class="sxs-lookup"><span data-stu-id="d4b88-451">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

### <a name="require-authorization"></a><span data-ttu-id="d4b88-452">需要授權</span><span class="sxs-lookup"><span data-stu-id="d4b88-452">Require authorization</span></span>

<span data-ttu-id="d4b88-453">呼叫 `RequireAuthorization` 以在健康情況檢查要求端點上執行授權中介軟體。</span><span class="sxs-lookup"><span data-stu-id="d4b88-453">Call `RequireAuthorization` to run Authorization Middleware on the health check request endpoint.</span></span> <span data-ttu-id="d4b88-454">`RequireAuthorization`多載接受一或多個授權原則。</span><span class="sxs-lookup"><span data-stu-id="d4b88-454">A `RequireAuthorization` overload accepts one or more authorization policies.</span></span> <span data-ttu-id="d4b88-455">如果未提供原則，則會使用預設授權原則。</span><span class="sxs-lookup"><span data-stu-id="d4b88-455">If a policy isn't provided, the default authorization policy is used.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireAuthorization();
});
```

### <a name="enable-cross-origin-requests-cors"></a><span data-ttu-id="d4b88-456">啟用跨原始來源要求 (CORS)</span><span class="sxs-lookup"><span data-stu-id="d4b88-456">Enable Cross-Origin Requests (CORS)</span></span>

<span data-ttu-id="d4b88-457">雖然以手動方式從瀏覽器執行健康情況檢查不是常見的使用案例，但您可以藉由呼叫健康情況檢查端點來啟用 CORS 中介軟體 `RequireCors` 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-457">Although performing health checks manually from a browser isn't a common use scenario, CORS Middleware can be enabled by calling `RequireCors` on health checks endpoints.</span></span> <span data-ttu-id="d4b88-458">多載會 `RequireCors` 接受 CORS 原則產生器委派 (`CorsPolicyBuilder`) 或原則名稱。</span><span class="sxs-lookup"><span data-stu-id="d4b88-458">A `RequireCors` overload accepts a CORS policy builder delegate (`CorsPolicyBuilder`) or a policy name.</span></span> <span data-ttu-id="d4b88-459">如果未提供原則，則會使用預設 CORS 原則。</span><span class="sxs-lookup"><span data-stu-id="d4b88-459">If a policy isn't provided, the default CORS policy is used.</span></span> <span data-ttu-id="d4b88-460">如需詳細資訊，請參閱<xref:security/cors>。</span><span class="sxs-lookup"><span data-stu-id="d4b88-460">For more information, see <xref:security/cors>.</span></span>

## <a name="health-check-options"></a><span data-ttu-id="d4b88-461">健康狀態檢查選項</span><span class="sxs-lookup"><span data-stu-id="d4b88-461">Health check options</span></span>

<span data-ttu-id="d4b88-462"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> 讓您有機會自訂健康狀態檢查行為：</span><span class="sxs-lookup"><span data-stu-id="d4b88-462"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="d4b88-463">篩選健康狀態檢查</span><span class="sxs-lookup"><span data-stu-id="d4b88-463">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="d4b88-464">自訂 HTTP 狀態碼</span><span class="sxs-lookup"><span data-stu-id="d4b88-464">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="d4b88-465">隱藏快取標頭</span><span class="sxs-lookup"><span data-stu-id="d4b88-465">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="d4b88-466">自訂輸出</span><span class="sxs-lookup"><span data-stu-id="d4b88-466">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="d4b88-467">篩選健康狀態檢查</span><span class="sxs-lookup"><span data-stu-id="d4b88-467">Filter health checks</span></span>

<span data-ttu-id="d4b88-468">依預設，健康狀態檢查中介軟體會執行所有已註冊的健康情況檢查。</span><span class="sxs-lookup"><span data-stu-id="d4b88-468">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="d4b88-469">若要執行健康狀態檢查子集，請對 <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> 選項提供傳回布林值的函式。</span><span class="sxs-lookup"><span data-stu-id="d4b88-469">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="d4b88-470">在下列範例中，會依函式條件陳述式中的標籤 (`bar_tag`) 篩選出 `Bar` 健康狀態檢查。只有在健康狀態檢查的 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> 屬性符合 `foo_tag` 或 `baz_tag` 時，才傳回 `true`：</span><span class="sxs-lookup"><span data-stu-id="d4b88-470">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

<span data-ttu-id="d4b88-471">在 `Startup.ConfigureServices` 中：</span><span class="sxs-lookup"><span data-stu-id="d4b88-471">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Foo", () =>
        HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
    .AddCheck("Bar", () =>
        HealthCheckResult.Unhealthy("Bar is unhealthy!"), tags: new[] { "bar_tag" })
    .AddCheck("Baz", () =>
        HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
```

<span data-ttu-id="d4b88-472">在中 `Startup.Configure` ，會 `Predicate` 篩選出「Bar」健康情況檢查。</span><span class="sxs-lookup"><span data-stu-id="d4b88-472">In `Startup.Configure`, the `Predicate` filters out the 'Bar' health check.</span></span> <span data-ttu-id="d4b88-473">只有 Foo 和 Baz execute.：</span><span class="sxs-lookup"><span data-stu-id="d4b88-473">Only Foo and Baz execute.:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
});
```

### <a name="customize-the-http-status-code"></a><span data-ttu-id="d4b88-474">自訂 HTTP 狀態碼</span><span class="sxs-lookup"><span data-stu-id="d4b88-474">Customize the HTTP status code</span></span>

<span data-ttu-id="d4b88-475">您可以使用 <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> 來自訂健康狀態與 HTTP 狀態碼的對應。</span><span class="sxs-lookup"><span data-stu-id="d4b88-475">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="d4b88-476">下列 <xref:Microsoft.AspNetCore.Http.StatusCodes> 指派是中介軟體所使用的預設值。</span><span class="sxs-lookup"><span data-stu-id="d4b88-476">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="d4b88-477">請變更狀態碼值以符合您的需求。</span><span class="sxs-lookup"><span data-stu-id="d4b88-477">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="d4b88-478">在 `Startup.Configure` 中：</span><span class="sxs-lookup"><span data-stu-id="d4b88-478">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResultStatusCodes =
        {
            [HealthStatus.Healthy] = StatusCodes.Status200OK,
            [HealthStatus.Degraded] = StatusCodes.Status200OK,
            [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
        }
    });
});
```

### <a name="suppress-cache-headers"></a><span data-ttu-id="d4b88-479">隱藏快取標頭</span><span class="sxs-lookup"><span data-stu-id="d4b88-479">Suppress cache headers</span></span>

<span data-ttu-id="d4b88-480"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> 控制健全狀況檢查中介軟體是否會將 HTTP 標頭新增至探查回應，以防止回應快取。</span><span class="sxs-lookup"><span data-stu-id="d4b88-480"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="d4b88-481">如果值為 `false` (預設)，則中介軟體會設定或覆寫 `Cache-Control`、`Expires` 和 `Pragma` 標頭，以防止回應快取。</span><span class="sxs-lookup"><span data-stu-id="d4b88-481">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="d4b88-482">如果值為 `true`，則中介軟體不會修改回應的快取標頭。</span><span class="sxs-lookup"><span data-stu-id="d4b88-482">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="d4b88-483">在 `Startup.Configure` 中：</span><span class="sxs-lookup"><span data-stu-id="d4b88-483">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        AllowCachingResponses = false
    });
});
```

### <a name="customize-output"></a><span data-ttu-id="d4b88-484">自訂輸出</span><span class="sxs-lookup"><span data-stu-id="d4b88-484">Customize output</span></span>

<span data-ttu-id="d4b88-485">在中 `Startup.Configure` ，將 [`HealthCheckOptions.ResponseWriter`](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter) 選項設定為用於寫入回應的委派：</span><span class="sxs-lookup"><span data-stu-id="d4b88-485">In `Startup.Configure`, set the [`HealthCheckOptions.ResponseWriter`](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter) option to a delegate for writing the response:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
});
```

<span data-ttu-id="d4b88-486">預設委派會以的字串值寫入最短的純文字回應 [`HealthReport.Status`](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status) 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-486">The default delegate writes a minimal plaintext response with the string value of [`HealthReport.Status`](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="d4b88-487">下列自訂委派會輸出自訂 JSON 回應。</span><span class="sxs-lookup"><span data-stu-id="d4b88-487">The following custom delegates output a custom JSON response.</span></span>

<span data-ttu-id="d4b88-488">範例應用程式中的第一個範例會示範如何使用 <xref:System.Text.Json?displayProperty=fullName> ：</span><span class="sxs-lookup"><span data-stu-id="d4b88-488">The first example from the sample app demonstrates how to use <xref:System.Text.Json?displayProperty=fullName>:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_SystemTextJson)]

<span data-ttu-id="d4b88-489">第二個範例示範如何 [ 在上使用Newtonsoft.Js](https://www.nuget.org/packages/Newtonsoft.Json)：</span><span class="sxs-lookup"><span data-stu-id="d4b88-489">The second example demonstrates how to use [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_NewtonSoftJson)]

<span data-ttu-id="d4b88-490">在範例應用程式中，將中的 `SYSTEM_TEXT_JSON` [預處理器](xref:index#preprocessor-directives-in-sample-code) 指示詞批註 `CustomWriterStartup.cs` 為，以啟用的 `Newtonsoft.Json` 版本 `WriteResponse` 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-490">In the sample app, comment out the `SYSTEM_TEXT_JSON` [preprocessor directive](xref:index#preprocessor-directives-in-sample-code) in `CustomWriterStartup.cs` to enable the `Newtonsoft.Json` version of `WriteResponse`.</span></span>

<span data-ttu-id="d4b88-491">健康情況檢查 API 不會提供複雜 JSON 傳回格式的內建支援，因為格式是您選擇的監視系統所特有。</span><span class="sxs-lookup"><span data-stu-id="d4b88-491">The health checks API doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="d4b88-492">視需要自訂上述範例中的回應。</span><span class="sxs-lookup"><span data-stu-id="d4b88-492">Customize the response in the preceding examples as needed.</span></span> <span data-ttu-id="d4b88-493">如需有關 JSON 序列化的詳細資訊 `System.Text.Json` ，請參閱 [如何在 .net 中序列化和還原序列化 json](/dotnet/standard/serialization/system-text-json-how-to)。</span><span class="sxs-lookup"><span data-stu-id="d4b88-493">For more information on JSON serialization with `System.Text.Json`, see [How to serialize and deserialize JSON in .NET](/dotnet/standard/serialization/system-text-json-how-to).</span></span>

## <a name="database-probe"></a><span data-ttu-id="d4b88-494">資料庫探查</span><span class="sxs-lookup"><span data-stu-id="d4b88-494">Database probe</span></span>

<span data-ttu-id="d4b88-495">健康狀態檢查可指定資料庫查詢以布林測試方式來執行，藉此指出資料庫是否正常回應。</span><span class="sxs-lookup"><span data-stu-id="d4b88-495">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="d4b88-496">範例應用程式會使用 [`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) ASP.NET Core 應用程式的健康情況檢查程式庫，對 SQL Server 資料庫執行健康情況檢查。</span><span class="sxs-lookup"><span data-stu-id="d4b88-496">The sample app uses [`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="d4b88-497">`AspNetCore.Diagnostics.HealthChecks` 會對資料庫執行 `SELECT 1` 查詢，以確認資料庫連線狀況良好。</span><span class="sxs-lookup"><span data-stu-id="d4b88-497">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="d4b88-498">使用查詢檢查資料庫連線時，請選擇快速傳回的查詢。</span><span class="sxs-lookup"><span data-stu-id="d4b88-498">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="d4b88-499">查詢方法具有多載資料庫而降低其效能的風險。</span><span class="sxs-lookup"><span data-stu-id="d4b88-499">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="d4b88-500">在大部分情況下，不需要執行測試查詢。</span><span class="sxs-lookup"><span data-stu-id="d4b88-500">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="d4b88-501">只要成功建立資料庫連線就已足夠。</span><span class="sxs-lookup"><span data-stu-id="d4b88-501">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="d4b88-502">如果您發現有必要執行查詢，請選擇簡單的 SELECT 查詢，例如 `SELECT 1`。</span><span class="sxs-lookup"><span data-stu-id="d4b88-502">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="d4b88-503">包含的封裝參考 [`AspNetCore.HealthChecks.SqlServer`](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer) 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-503">Include a package reference to [`AspNetCore.HealthChecks.SqlServer`](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer).</span></span>

<span data-ttu-id="d4b88-504">在範例應用程式的檔案中提供有效的資料庫連接字串 `appsettings.json` 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-504">Supply a valid database connection string in the `appsettings.json` file of the sample app.</span></span> <span data-ttu-id="d4b88-505">應用程式使用名為 `HealthCheckSample` 的 SQL Server 資料庫：</span><span class="sxs-lookup"><span data-stu-id="d4b88-505">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/3.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="d4b88-506">在 `Startup.ConfigureServices` 中，使用 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks%2A> 登錄健康狀態檢查服務。</span><span class="sxs-lookup"><span data-stu-id="d4b88-506">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks%2A> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d4b88-507">範例應用程式會 `AddSqlServer` 使用資料庫的連接字串呼叫方法， (`DbHealthStartup.cs`) ：</span><span class="sxs-lookup"><span data-stu-id="d4b88-507">The sample app calls the `AddSqlServer` method with the database's connection string (`DbHealthStartup.cs`):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="d4b88-508">健康情況檢查端點是在中呼叫來建立的 `MapHealthChecks` `Startup.Configure` ：</span><span class="sxs-lookup"><span data-stu-id="d4b88-508">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="d4b88-509">若要使用範例應用程式執行資料庫探查案例，請在命令殼層中執行來自專案資料夾的下列命令：</span><span class="sxs-lookup"><span data-stu-id="d4b88-509">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="d4b88-510">[`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) Microsoft 不會維護或支援。</span><span class="sxs-lookup"><span data-stu-id="d4b88-510">[`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="d4b88-511">Entity Framework Core DbContext 探查</span><span class="sxs-lookup"><span data-stu-id="d4b88-511">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="d4b88-512">`DbContext` 檢查會確認應用程式是否可與針對 EF Core `DbContext` 設定的資料庫通訊。</span><span class="sxs-lookup"><span data-stu-id="d4b88-512">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="d4b88-513">應用程式支援 `DbContext` 檢查：</span><span class="sxs-lookup"><span data-stu-id="d4b88-513">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="d4b88-514">使用 [Entity Framework (EF) Core](/ef/core/)。</span><span class="sxs-lookup"><span data-stu-id="d4b88-514">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="d4b88-515">包含的封裝參考 [`Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore`](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-515">Include a package reference to [`Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore`](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore).</span></span>

<span data-ttu-id="d4b88-516">`AddDbContextCheck<TContext>` 會登錄 `DbContext` 的健康狀態檢查。</span><span class="sxs-lookup"><span data-stu-id="d4b88-516">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="d4b88-517">`DbContext` 會以 `TContext` 形式提供給方法。</span><span class="sxs-lookup"><span data-stu-id="d4b88-517">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="d4b88-518">多載可用來設定失敗狀態、標籤和自訂測試查詢。</span><span class="sxs-lookup"><span data-stu-id="d4b88-518">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="d4b88-519">依照預設：</span><span class="sxs-lookup"><span data-stu-id="d4b88-519">By default:</span></span>

* <span data-ttu-id="d4b88-520">`DbContextHealthCheck` 會呼叫 EF Core 的 `CanConnectAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="d4b88-520">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="d4b88-521">您可以自訂使用 `AddDbContextCheck` 方法多載檢查健康狀態時所要執行的作業。</span><span class="sxs-lookup"><span data-stu-id="d4b88-521">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="d4b88-522">健康狀態檢查的名稱是 `TContext` 類型的名稱。</span><span class="sxs-lookup"><span data-stu-id="d4b88-522">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="d4b88-523">在範例應用程式中， `AppDbContext` 會提供給 `AddDbContextCheck` () 中的服務，並將其註冊為服務 `Startup.ConfigureServices` `DbContextHealthStartup.cs` ：</span><span class="sxs-lookup"><span data-stu-id="d4b88-523">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (`DbContextHealthStartup.cs`):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="d4b88-524">健康情況檢查端點是在中呼叫來建立的 `MapHealthChecks` `Startup.Configure` ：</span><span class="sxs-lookup"><span data-stu-id="d4b88-524">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="d4b88-525">若要使用範例應用程式來執行 `DbContext` 探查案例，請確認 SQL Server 執行個體中沒有連接字串所指定的資料庫。</span><span class="sxs-lookup"><span data-stu-id="d4b88-525">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="d4b88-526">如果資料庫存在，請予以刪除。</span><span class="sxs-lookup"><span data-stu-id="d4b88-526">If the database exists, delete it.</span></span>

<span data-ttu-id="d4b88-527">在命令殼層中執行來自專案資料夾的下列命令：</span><span class="sxs-lookup"><span data-stu-id="d4b88-527">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="d4b88-528">在應用程式執行之後，對瀏覽器中的 `/health` 端點提出要求來檢查健康狀態。</span><span class="sxs-lookup"><span data-stu-id="d4b88-528">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="d4b88-529">資料庫和 `AppDbContext` 不存在，因此應用程式會提供下列回應：</span><span class="sxs-lookup"><span data-stu-id="d4b88-529">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="d4b88-530">觸發範例應用程式以建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="d4b88-530">Trigger the sample app to create the database.</span></span> <span data-ttu-id="d4b88-531">對 `/createdatabase` 提出要求。</span><span class="sxs-lookup"><span data-stu-id="d4b88-531">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="d4b88-532">應用程式會回應：</span><span class="sxs-lookup"><span data-stu-id="d4b88-532">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="d4b88-533">對 `/health` 端點提出要求。</span><span class="sxs-lookup"><span data-stu-id="d4b88-533">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="d4b88-534">資料庫和內容存在，因此應用程式會回應：</span><span class="sxs-lookup"><span data-stu-id="d4b88-534">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="d4b88-535">觸發範例應用程式以刪除資料庫。</span><span class="sxs-lookup"><span data-stu-id="d4b88-535">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="d4b88-536">對 `/deletedatabase` 提出要求。</span><span class="sxs-lookup"><span data-stu-id="d4b88-536">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="d4b88-537">應用程式會回應：</span><span class="sxs-lookup"><span data-stu-id="d4b88-537">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="d4b88-538">對 `/health` 端點提出要求。</span><span class="sxs-lookup"><span data-stu-id="d4b88-538">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="d4b88-539">應用程式會提供狀況不良回應：</span><span class="sxs-lookup"><span data-stu-id="d4b88-539">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="d4b88-540">個別的整備度與活躍度探查</span><span class="sxs-lookup"><span data-stu-id="d4b88-540">Separate readiness and liveness probes</span></span>

<span data-ttu-id="d4b88-541">在某些主控案例中，使用一組健康狀態檢查來區分兩個應用程式狀態：</span><span class="sxs-lookup"><span data-stu-id="d4b88-541">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="d4b88-542">*準備就緒* 表示應用程式是否正常執行，但尚未準備好接收要求。</span><span class="sxs-lookup"><span data-stu-id="d4b88-542">*Readiness* indicates if the app is running normally but isn't ready to receive requests.</span></span>
* <span data-ttu-id="d4b88-543">*活動* 指出應用程式是否已損毀且必須重新開機。</span><span class="sxs-lookup"><span data-stu-id="d4b88-543">*Liveness* indicates if an app has crashed and must be restarted.</span></span>

<span data-ttu-id="d4b88-544">請考慮下列範例：應用程式必須先下載大型設定檔，才能開始處理要求。</span><span class="sxs-lookup"><span data-stu-id="d4b88-544">Consider the following example: An app must download a large configuration file before it's ready to process requests.</span></span> <span data-ttu-id="d4b88-545">如果初始下載失敗，我們不想要重新開機應用程式，因為應用程式可以重試下載檔案數次。</span><span class="sxs-lookup"><span data-stu-id="d4b88-545">We don't want the app to be restarted if the initial download fails because the app can retry downloading the file several times.</span></span> <span data-ttu-id="d4b88-546">我們會使用 *活動探查* 來描述進程的活動，而不會執行其他檢查。</span><span class="sxs-lookup"><span data-stu-id="d4b88-546">We use a *liveness probe* to describe the liveness of the process, no additional checks are performed.</span></span> <span data-ttu-id="d4b88-547">我們也想要避免在設定檔下載成功之前將要求傳送至應用程式。</span><span class="sxs-lookup"><span data-stu-id="d4b88-547">We also want to prevent requests from being sent to the app before the configuration file download has succeeded.</span></span> <span data-ttu-id="d4b88-548">我們會使用 *就緒探查* 來表示「未就緒」狀態，直到下載成功，而且應用程式已準備好接收要求。</span><span class="sxs-lookup"><span data-stu-id="d4b88-548">We use a *readiness probe* to indicate a "not ready" state until the download succeeds and the app is ready to receive requests.</span></span>

<span data-ttu-id="d4b88-549">範例應用程式包含健康狀態檢查，會在[託管服務](xref:fundamentals/host/hosted-services)中長時間執行的啟動工作完成時回報。</span><span class="sxs-lookup"><span data-stu-id="d4b88-549">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="d4b88-550">會 `StartupHostedServiceHealthCheck` 公開一個屬性，也就是裝載的服務在長時間執行的工作 `StartupTaskCompleted` `true` 完成 () 時可以設定的屬性 `StartupHostedServiceHealthCheck.cs` ：</span><span class="sxs-lookup"><span data-stu-id="d4b88-550">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (`StartupHostedServiceHealthCheck.cs`):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="d4b88-551">長時間執行的背景工作是由 [託管服務](xref:fundamentals/host/hosted-services) () 所啟動 `Services/StartupHostedService` 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-551">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (`Services/StartupHostedService`).</span></span> <span data-ttu-id="d4b88-552">工作完成時，`StartupHostedServiceHealthCheck.StartupTaskCompleted` 會設定為 `true`：</span><span class="sxs-lookup"><span data-stu-id="d4b88-552">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="d4b88-553">健康狀態檢查是 `Startup.ConfigureServices` 中使用 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck%2A> 隨託管服務一起登錄。</span><span class="sxs-lookup"><span data-stu-id="d4b88-553">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck%2A> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="d4b88-554">因為託管服務必須設定健康情況檢查的屬性，所以健康情況檢查也會在服務容器中註冊 (`LivenessProbeStartup.cs`) ：</span><span class="sxs-lookup"><span data-stu-id="d4b88-554">Because the hosted service must set the property on the health check, the health check is also registered in the service container (`LivenessProbeStartup.cs`):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="d4b88-555">健康情況檢查端點是藉由在中呼叫來建立 `MapHealthChecks` `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-555">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="d4b88-556">在範例應用程式中，健康狀態檢查端點是在下列位置建立：</span><span class="sxs-lookup"><span data-stu-id="d4b88-556">In the sample app, the health check endpoints are created at:</span></span>

* <span data-ttu-id="d4b88-557">`/health/ready` 適用于準備檢查。</span><span class="sxs-lookup"><span data-stu-id="d4b88-557">`/health/ready` for the readiness check.</span></span> <span data-ttu-id="d4b88-558">整備度檢查使用 `ready` 標籤來篩選健康狀態檢查。</span><span class="sxs-lookup"><span data-stu-id="d4b88-558">The readiness check filters health checks to the health check with the `ready` tag.</span></span>
* <span data-ttu-id="d4b88-559">`/health/live` 適用于活動檢查。</span><span class="sxs-lookup"><span data-stu-id="d4b88-559">`/health/live` for the liveness check.</span></span> <span data-ttu-id="d4b88-560">活動檢查會 `StartupHostedServiceHealthCheck` `false` 在 (中傳回以篩選出 [`HealthCheckOptions.Predicate`](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) 詳細資訊，請參閱 [篩選健康情況檢查](#filter-health-checks)) </span><span class="sxs-lookup"><span data-stu-id="d4b88-560">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [`HealthCheckOptions.Predicate`](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks))</span></span>

<span data-ttu-id="d4b88-561">在下列範例程式碼中：</span><span class="sxs-lookup"><span data-stu-id="d4b88-561">In the following example code:</span></span>

* <span data-ttu-id="d4b88-562">準備就緒檢查會將所有已註冊的檢查都使用「就緒」標記。</span><span class="sxs-lookup"><span data-stu-id="d4b88-562">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="d4b88-563">會 `Predicate` 排除所有檢查並傳回 200-確定。</span><span class="sxs-lookup"><span data-stu-id="d4b88-563">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health/ready", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("ready"),
    });

    endpoints.MapHealthChecks("/health/live", new HealthCheckOptions()
    {
        Predicate = (_) => false
    });
}
```

<span data-ttu-id="d4b88-564">若要使用範例應用程式執行整備度/活躍度組態案例，請在命令殼層中執行來自專案資料夾的下列命令：</span><span class="sxs-lookup"><span data-stu-id="d4b88-564">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="d4b88-565">在瀏覽器中，瀏覽 `/health/ready` 數次，直到經過 15 秒。</span><span class="sxs-lookup"><span data-stu-id="d4b88-565">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="d4b88-566">健康狀態檢查報告前 15 秒為 `Unhealthy`。</span><span class="sxs-lookup"><span data-stu-id="d4b88-566">The health check reports `Unhealthy` for the first 15 seconds.</span></span> <span data-ttu-id="d4b88-567">15 秒之後，端點會報告 `Healthy`，以反映託管服務長時間執行的工作已完成。</span><span class="sxs-lookup"><span data-stu-id="d4b88-567">After 15 seconds, the endpoint reports `Healthy`, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="d4b88-568">此範例也會建立一個以 2 秒延遲執行第一次整備檢查的「健康狀態檢查發行者」(<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 實作)。</span><span class="sxs-lookup"><span data-stu-id="d4b88-568">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="d4b88-569">如需詳細資訊，請參閱[健康狀態檢查發行者](#health-check-publisher)一節。</span><span class="sxs-lookup"><span data-stu-id="d4b88-569">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="d4b88-570">Kubernetes 範例</span><span class="sxs-lookup"><span data-stu-id="d4b88-570">Kubernetes example</span></span>

<span data-ttu-id="d4b88-571">使用個別的整備度與活躍度檢查在 [Kubernetes](https://kubernetes.io/) 之類的環境中很有用。</span><span class="sxs-lookup"><span data-stu-id="d4b88-571">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="d4b88-572">在 Kubernetes 中，應用程式可能需要執行耗時的啟動工作才能接受要求 (例如基礎資料庫可用性測試)。</span><span class="sxs-lookup"><span data-stu-id="d4b88-572">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="d4b88-573">使用個別的檢查可讓協調器區分應用程式是否為正常運作但尚未準備好，或應用程式是否無法啟動。</span><span class="sxs-lookup"><span data-stu-id="d4b88-573">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="d4b88-574">如需 Kubernetes 中整備度與活躍度探查的詳細資訊，請參閱 Kubernetes 文件中的 [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) (設定活躍度與整備度探查)。</span><span class="sxs-lookup"><span data-stu-id="d4b88-574">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="d4b88-575">下列範例示範 Kubernetes 整備度探查組態：</span><span class="sxs-lookup"><span data-stu-id="d4b88-575">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

```yml
spec:
  template:
  spec:
    readinessProbe:
      # an http probe
      httpGet:
        path: /health/ready
        port: 80
      # length of time to wait for a pod to initialize
      # after pod startup, before applying health checking
      initialDelaySeconds: 30
      timeoutSeconds: 1
    ports:
      - containerPort: 80
```

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="d4b88-576">透過自訂回應寫入器的計量型探查</span><span class="sxs-lookup"><span data-stu-id="d4b88-576">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="d4b88-577">範例應用程式示範透過自訂回應寫入器的記憶體健康狀態檢查。</span><span class="sxs-lookup"><span data-stu-id="d4b88-577">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="d4b88-578">如果應用程式使用超過指定的記憶體閾值 (在範例應用程式中為 1 GB)，`MemoryHealthCheck` 會報告降級的狀態。</span><span class="sxs-lookup"><span data-stu-id="d4b88-578">`MemoryHealthCheck` reports a degraded status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="d4b88-579"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>包含應用程式 () 的垃圾收集行程 (GC) 資訊 `MemoryHealthCheck.cs` ：</span><span class="sxs-lookup"><span data-stu-id="d4b88-579">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (`MemoryHealthCheck.cs`):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="d4b88-580">在 `Startup.ConfigureServices` 中，使用 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks%2A> 登錄健康狀態檢查服務。</span><span class="sxs-lookup"><span data-stu-id="d4b88-580">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks%2A> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d4b88-581">`MemoryHealthCheck` 會登錄為服務，而不是將健康狀態檢查傳遞至 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck%2A> 以啟用檢查。</span><span class="sxs-lookup"><span data-stu-id="d4b88-581">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck%2A>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="d4b88-582">所有 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 登錄的服務都可供健康狀態檢查服務和中介軟體使用。</span><span class="sxs-lookup"><span data-stu-id="d4b88-582">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="d4b88-583">建議將健康狀態檢查服務登錄為單一服務。</span><span class="sxs-lookup"><span data-stu-id="d4b88-583">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="d4b88-584">在 `CustomWriterStartup.cs` 範例應用程式中：</span><span class="sxs-lookup"><span data-stu-id="d4b88-584">In `CustomWriterStartup.cs` of the sample app:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="d4b88-585">健康情況檢查端點是藉由在中呼叫來建立 `MapHealthChecks` `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-585">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="d4b88-586">`WriteResponse`當健康狀態檢查執行時，會將委派提供給 <的 AspNetCore. HealthChecks. HealthCheckOptions. ResponseWriter> 屬性以輸出自訂 JSON 回應：</span><span class="sxs-lookup"><span data-stu-id="d4b88-586">A `WriteResponse` delegate is provided to the <Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> property to output a custom JSON response when the health check executes:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
}
```

<span data-ttu-id="d4b88-587">委派會將 `WriteResponse` 格式化為 `CompositeHealthCheckResult` json 物件，並產生健康情況檢查回應的 json 輸出。</span><span class="sxs-lookup"><span data-stu-id="d4b88-587">The `WriteResponse` delegate formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response.</span></span> <span data-ttu-id="d4b88-588">如需詳細資訊，請參閱 [自訂輸出](#customize-output) 一節。</span><span class="sxs-lookup"><span data-stu-id="d4b88-588">For more information, see the [Customize output](#customize-output) section.</span></span>

<span data-ttu-id="d4b88-589">若要使用範例應用程式透過自訂回應寫入器輸出執行計量型探查，請在命令殼層中執行來自專案資料夾的下列命令：</span><span class="sxs-lookup"><span data-stu-id="d4b88-589">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="d4b88-590">[`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 包含以計量為基礎的健康情況檢查案例，包括磁片儲存體和最大值活動檢查。</span><span class="sxs-lookup"><span data-stu-id="d4b88-590">[`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="d4b88-591">[`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) Microsoft 不會維護或支援。</span><span class="sxs-lookup"><span data-stu-id="d4b88-591">[`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="d4b88-592">依連接埠篩選</span><span class="sxs-lookup"><span data-stu-id="d4b88-592">Filter by port</span></span>

<span data-ttu-id="d4b88-593">`RequireHost` `MapHealthChecks` 使用指定埠的 URL 模式呼叫，以將健康情況檢查要求限制為指定的埠。</span><span class="sxs-lookup"><span data-stu-id="d4b88-593">Call `RequireHost` on `MapHealthChecks` with a URL pattern that specifies a port to restrict health check requests to the port specified.</span></span> <span data-ttu-id="d4b88-594">這通常會用於容器環境，以公開監視服務的連接埠。</span><span class="sxs-lookup"><span data-stu-id="d4b88-594">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="d4b88-595">範例應用程式使用[環境變數組態提供者](xref:fundamentals/configuration/index#environment-variables)來設定連接埠。</span><span class="sxs-lookup"><span data-stu-id="d4b88-595">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables).</span></span> <span data-ttu-id="d4b88-596">埠是在檔案中設定 `launchSettings.json` ，並透過環境變數傳遞至設定提供者。</span><span class="sxs-lookup"><span data-stu-id="d4b88-596">The port is set in the `launchSettings.json` file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="d4b88-597">您也必須將伺服器設定為在管理連接埠上接聽要求。</span><span class="sxs-lookup"><span data-stu-id="d4b88-597">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="d4b88-598">若要使用範例應用程式來示範管理埠設定，請 `launchSettings.json` 在資料夾中建立檔案 `Properties` 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-598">To use the sample app to demonstrate management port configuration, create the `launchSettings.json` file in a `Properties` folder.</span></span>

<span data-ttu-id="d4b88-599">範例應用 `Properties/launchSettings.json` 程式中的下列檔案未包含在範例應用程式的專案檔中，必須以手動方式建立：</span><span class="sxs-lookup"><span data-stu-id="d4b88-599">The following `Properties/launchSettings.json` file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

```json
{
  "profiles": {
    "SampleApp": {
      "commandName": "Project",
      "commandLineArgs": "",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_URLS": "http://localhost:5000/;http://localhost:5001/",
        "ASPNETCORE_MANAGEMENTPORT": "5001"
      },
      "applicationUrl": "http://localhost:5000/"
    }
  }
}
```

<span data-ttu-id="d4b88-600">在 `Startup.ConfigureServices` 中，使用 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks%2A> 登錄健康狀態檢查服務。</span><span class="sxs-lookup"><span data-stu-id="d4b88-600">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks%2A> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d4b88-601">藉由在中呼叫來建立健康情況檢查端點 `MapHealthChecks` `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-601">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="d4b88-602">在範例應用程式中，對 `RequireHost` 中端點的呼叫會 `Startup.Configure` 從設定中指定管理埠：</span><span class="sxs-lookup"><span data-stu-id="d4b88-602">In the sample app, a call to `RequireHost` on the endpoint in `Startup.Configure` specifies the management port from configuration:</span></span>

```csharp
endpoints.MapHealthChecks("/health")
    .RequireHost($"*:{Configuration["ManagementPort"]}");
```

<span data-ttu-id="d4b88-603">端點是在的範例應用程式中建立的 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-603">Endpoints are created in the sample app in `Startup.Configure`.</span></span> <span data-ttu-id="d4b88-604">在下列範例程式碼中：</span><span class="sxs-lookup"><span data-stu-id="d4b88-604">In the following example code:</span></span>

* <span data-ttu-id="d4b88-605">準備就緒檢查會將所有已註冊的檢查都使用「就緒」標記。</span><span class="sxs-lookup"><span data-stu-id="d4b88-605">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="d4b88-606">會 `Predicate` 排除所有檢查並傳回 200-確定。</span><span class="sxs-lookup"><span data-stu-id="d4b88-606">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health/ready", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("ready"),
    });

    endpoints.MapHealthChecks("/health/live", new HealthCheckOptions()
    {
        Predicate = (_) => false
    });
}
```

> [!NOTE]
> <span data-ttu-id="d4b88-607">您可以在 `launchSettings.json` 程式碼中明確設定管理埠，以避免在範例應用程式中建立檔案。</span><span class="sxs-lookup"><span data-stu-id="d4b88-607">You can avoid creating the `launchSettings.json` file in the sample app by setting the management port explicitly in code.</span></span> <span data-ttu-id="d4b88-608">在 `Program.cs` 建立的位置中 <xref:Microsoft.Extensions.Hosting.HostBuilder> ，新增對的呼叫， <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP%2A> 並提供應用程式的管理埠端點。</span><span class="sxs-lookup"><span data-stu-id="d4b88-608">In `Program.cs` where the <xref:Microsoft.Extensions.Hosting.HostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP%2A> and provide the app's management port endpoint.</span></span> <span data-ttu-id="d4b88-609">在 `Configure` 中 `ManagementPortStartup.cs` ，使用下列內容指定管理埠 `RequireHost` ：</span><span class="sxs-lookup"><span data-stu-id="d4b88-609">In `Configure` of `ManagementPortStartup.cs`, specify the management port with `RequireHost`:</span></span>
>
> <span data-ttu-id="d4b88-610">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="d4b88-610">`Program.cs`:</span></span>
>
> ```csharp
> return new HostBuilder()
>     .ConfigureWebHostDefaults(webBuilder =>
>     {
>         webBuilder.UseKestrel()
>             .ConfigureKestrel(serverOptions =>
>             {
>                 serverOptions.ListenAnyIP(5001);
>             })
>             .UseStartup(startupType);
>     })
>     .Build();
> ```
>
> <span data-ttu-id="d4b88-611">`ManagementPortStartup.cs`:</span><span class="sxs-lookup"><span data-stu-id="d4b88-611">`ManagementPortStartup.cs`:</span></span>
>
> ```csharp
> app.UseEndpoints(endpoints =>
> {
>     endpoints.MapHealthChecks("/health").RequireHost("*:5001");
> });
> ```

<span data-ttu-id="d4b88-612">若要使用範例應用程式執行管理連接埠組態案例，請在命令殼層中執行來自專案資料夾的下列命令：</span><span class="sxs-lookup"><span data-stu-id="d4b88-612">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="d4b88-613">發佈健康狀態檢查程式庫</span><span class="sxs-lookup"><span data-stu-id="d4b88-613">Distribute a health check library</span></span>

<span data-ttu-id="d4b88-614">若要發佈健康狀態檢查作為程式庫：</span><span class="sxs-lookup"><span data-stu-id="d4b88-614">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="d4b88-615">寫入健康狀態檢查，將 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 介面當做獨立類別來實作。</span><span class="sxs-lookup"><span data-stu-id="d4b88-615">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="d4b88-616">此類別可能依賴[相依性插入 (DI)](xref:fundamentals/dependency-injection)、類型啟用和[具名選項](xref:fundamentals/configuration/options)來存取組態資料。</span><span class="sxs-lookup"><span data-stu-id="d4b88-616">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="d4b88-617">在健康狀態檢查邏輯中 `CheckHealthAsync` ：</span><span class="sxs-lookup"><span data-stu-id="d4b88-617">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="d4b88-618">`data1` 和 `data2` 在方法中用來執行探查的健康情況檢查邏輯。</span><span class="sxs-lookup"><span data-stu-id="d4b88-618">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="d4b88-619">`AccessViolationException` 會進行處理。</span><span class="sxs-lookup"><span data-stu-id="d4b88-619">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="d4b88-620">當 <xref:System.AccessViolationException> 發生時， <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> 會傳回， <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> 以允許使用者設定健康情況檢查失敗狀態。</span><span class="sxs-lookup"><span data-stu-id="d4b88-620">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

   ```csharp
   using System;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   namespace SampleApp
   {
       public class ExampleHealthCheck : IHealthCheck
       {
           private readonly string _data1;
           private readonly int? _data2;

           public ExampleHealthCheck(string data1, int? data2)
           {
               _data1 = data1 ?? throw new ArgumentNullException(nameof(data1));
               _data2 = data2 ?? throw new ArgumentNullException(nameof(data2));
           }

           public async Task<HealthCheckResult> CheckHealthAsync(
               HealthCheckContext context, CancellationToken cancellationToken)
           {
               try
               {
                   return HealthCheckResult.Healthy();
               }
               catch (AccessViolationException ex)
               {
                   return new HealthCheckResult(
                       context.Registration.FailureStatus,
                       description: "An access violation occurred during the check.",
                       exception: ex,
                       data: null);
               }
           }
       }
   }
   ```

1. <span data-ttu-id="d4b88-621">使用取用應用程式在其 `Startup.Configure` 方法中呼叫的參數，來寫入延伸模組。</span><span class="sxs-lookup"><span data-stu-id="d4b88-621">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="d4b88-622">在下列範例中，假設健康狀態檢查方法簽章如下：</span><span class="sxs-lookup"><span data-stu-id="d4b88-622">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="d4b88-623">上述簽章指出 `ExampleHealthCheck` 需要額外的資料，才能處理健康狀態檢查探查邏輯。</span><span class="sxs-lookup"><span data-stu-id="d4b88-623">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="d4b88-624">此資料會提供給委派，以在使用延伸模組登錄健康狀態檢查時，用來建立健康狀態檢查執行個體。</span><span class="sxs-lookup"><span data-stu-id="d4b88-624">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="d4b88-625">在下列範例中，呼叫者會選擇性地指定：</span><span class="sxs-lookup"><span data-stu-id="d4b88-625">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="d4b88-626">健康狀態檢查名稱 (`name`)。</span><span class="sxs-lookup"><span data-stu-id="d4b88-626">health check name (`name`).</span></span> <span data-ttu-id="d4b88-627">如果為 `null`，則會使用 `example_health_check`。</span><span class="sxs-lookup"><span data-stu-id="d4b88-627">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="d4b88-628">健康狀態檢查的字串資料點 (`data1`)。</span><span class="sxs-lookup"><span data-stu-id="d4b88-628">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="d4b88-629">健康狀態檢查的整數資料點 (`data2`)。</span><span class="sxs-lookup"><span data-stu-id="d4b88-629">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="d4b88-630">如果為 `null`，則會使用 `1`。</span><span class="sxs-lookup"><span data-stu-id="d4b88-630">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="d4b88-631">失敗狀態 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>)。</span><span class="sxs-lookup"><span data-stu-id="d4b88-631">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="d4b88-632">預設為 `null`。</span><span class="sxs-lookup"><span data-stu-id="d4b88-632">The default is `null`.</span></span> <span data-ttu-id="d4b88-633">如果為 `null` ， [`HealthStatus.Unhealthy`](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) 則回報失敗狀態。</span><span class="sxs-lookup"><span data-stu-id="d4b88-633">If `null`, [`HealthStatus.Unhealthy`](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="d4b88-634">標籤 (`IEnumerable<string>`)。</span><span class="sxs-lookup"><span data-stu-id="d4b88-634">tags (`IEnumerable<string>`).</span></span>

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string DefaultName = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder,
           string name = default,
           string data1,
           int data2 = 1,
           HealthStatus? failureStatus = default,
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? DefaultName,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a><span data-ttu-id="d4b88-635">健康狀態檢查發行者</span><span class="sxs-lookup"><span data-stu-id="d4b88-635">Health Check Publisher</span></span>

<span data-ttu-id="d4b88-636">當 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 新增至服務容器時，健康狀態檢查系統會定期執行健康狀態檢查，並呼叫 `PublishAsync` 傳回結果。</span><span class="sxs-lookup"><span data-stu-id="d4b88-636">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="d4b88-637">這對推送型健康狀態監控系統案例很有用，其預期每個處理序會定期呼叫監控系統來判斷健康狀態。</span><span class="sxs-lookup"><span data-stu-id="d4b88-637">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="d4b88-638"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 介面有單一方法：</span><span class="sxs-lookup"><span data-stu-id="d4b88-638">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="d4b88-639"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> 可讓您設定：</span><span class="sxs-lookup"><span data-stu-id="d4b88-639"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="d4b88-640"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>：在執行實例之前，應用程式啟動後所套用的初始延遲 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-640"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="d4b88-641">在啟動後就會套用延遲，但不會套用至後續的反覆項目。</span><span class="sxs-lookup"><span data-stu-id="d4b88-641">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="d4b88-642">預設值是五秒鐘。</span><span class="sxs-lookup"><span data-stu-id="d4b88-642">The default value is five seconds.</span></span>
* <span data-ttu-id="d4b88-643"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>：執行的期間 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-643"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="d4b88-644">預設值為 30 秒。</span><span class="sxs-lookup"><span data-stu-id="d4b88-644">The default value is 30 seconds.</span></span>
* <span data-ttu-id="d4b88-645"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>：如果 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> `null` (預設) ，健全狀況檢查發行者服務會執行所有已註冊的健康情況檢查。</span><span class="sxs-lookup"><span data-stu-id="d4b88-645"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="d4b88-646">若要執行一部分的健康狀態檢查，請提供可篩選該組檢查的函式。</span><span class="sxs-lookup"><span data-stu-id="d4b88-646">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="d4b88-647">每個期間都會評估該述詞。</span><span class="sxs-lookup"><span data-stu-id="d4b88-647">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="d4b88-648"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>：針對所有實例執行健康情況檢查的超時時間 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-648"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="d4b88-649">若要在沒有逾時的情況下執行，請使用 <xref:System.Threading.Timeout.InfiniteTimeSpan>。</span><span class="sxs-lookup"><span data-stu-id="d4b88-649">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="d4b88-650">預設值為 30 秒。</span><span class="sxs-lookup"><span data-stu-id="d4b88-650">The default value is 30 seconds.</span></span>

<span data-ttu-id="d4b88-651">在範例應用程式中，`ReadinessPublisher` 是一個 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 實作。</span><span class="sxs-lookup"><span data-stu-id="d4b88-651">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="d4b88-652">記錄層級的每個檢查都會記錄健全狀況檢查狀態：</span><span class="sxs-lookup"><span data-stu-id="d4b88-652">The health check status is logged for each check at a log level of:</span></span>

* <span data-ttu-id="d4b88-653"><xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation%2A>如果健康狀態檢查狀態為， () 資訊 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy> 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-653">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation%2A>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="d4b88-654"><xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>當狀態為或時，)  (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> 錯誤 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy> 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-654">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="d4b88-655">在範例應用程式的 `LivenessProbeStartup` 範例中，`StartupHostedService` 整備檢查具有 2 秒的啟動延遲，且每 30 秒會執行一次檢查。</span><span class="sxs-lookup"><span data-stu-id="d4b88-655">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="d4b88-656">為了啟用 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 實作，此範例會在[相依性插入 (DI)](xref:fundamentals/dependency-injection) 容器中將 `ReadinessPublisher` 註冊為單一服務：</span><span class="sxs-lookup"><span data-stu-id="d4b88-656">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

> [!NOTE]
> <span data-ttu-id="d4b88-657">[`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 包含數個系統的發行者，包括 [Application Insights](/azure/application-insights/app-insights-overview)。</span><span class="sxs-lookup"><span data-stu-id="d4b88-657">[`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="d4b88-658">[`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) Microsoft 不會維護或支援。</span><span class="sxs-lookup"><span data-stu-id="d4b88-658">[`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="d4b88-659">利用 MapWhen 限制健康情況檢查</span><span class="sxs-lookup"><span data-stu-id="d4b88-659">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="d4b88-660">使用 <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> 來有條件地將健康情況檢查端點的要求管線分支。</span><span class="sxs-lookup"><span data-stu-id="d4b88-660">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="d4b88-661">在下列範例中， `MapWhen` 如果收到端點的 GET 要求，則會將要求管線分支以啟用健康情況檢查中介軟體 `api/HealthCheck` ：</span><span class="sxs-lookup"><span data-stu-id="d4b88-661">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseEndpoints(endpoints =>
{
    endpoints.MapRazorPages();
});
```

<span data-ttu-id="d4b88-662">如需詳細資訊，請參閱<xref:fundamentals/middleware/index#branch-the-middleware-pipeline>。</span><span class="sxs-lookup"><span data-stu-id="d4b88-662">For more information, see <xref:fundamentals/middleware/index#branch-the-middleware-pipeline>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d4b88-663">ASP.NET Core 提供健康狀態檢查中介軟體和程式庫，以報告應用程式基礎結構元件的健康情況。</span><span class="sxs-lookup"><span data-stu-id="d4b88-663">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="d4b88-664">應用程式會將健康狀態檢查公開為 HTTP 端點。</span><span class="sxs-lookup"><span data-stu-id="d4b88-664">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="d4b88-665">您可以針對各種即時監控案例來設定健康狀態檢查端點：</span><span class="sxs-lookup"><span data-stu-id="d4b88-665">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="d4b88-666">容器協調器和負載平衡器可以使用健康狀態探查，來檢查應用程式的狀態。</span><span class="sxs-lookup"><span data-stu-id="d4b88-666">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="d4b88-667">例如，容器協調器可能會暫停輪流部署或重新啟動容器，來回應失敗的健康狀態檢查。</span><span class="sxs-lookup"><span data-stu-id="d4b88-667">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="d4b88-668">負載平衡器可能會將流量從失敗的執行個體路由傳送至狀況良好的執行個體，來回應狀況不良的應用程式。</span><span class="sxs-lookup"><span data-stu-id="d4b88-668">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="d4b88-669">您可以監控所使用記憶體、磁碟及其他實體伺服器資源的健康狀態。</span><span class="sxs-lookup"><span data-stu-id="d4b88-669">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="d4b88-670">健康狀態檢查可以測試應用程式的相依性 (例如資料庫和外部服務端點)，確認其是否可用且正常運作。</span><span class="sxs-lookup"><span data-stu-id="d4b88-670">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="d4b88-671">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/health-checks/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="d4b88-671">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d4b88-672">範例應用程式包含本主題中所述的案例範例。</span><span class="sxs-lookup"><span data-stu-id="d4b88-672">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="d4b88-673">若要在指定的案例中執行範例應用程式，請在命令殼層中使用來自專案資料夾的 [dotnet run](/dotnet/core/tools/dotnet-run) 命令。</span><span class="sxs-lookup"><span data-stu-id="d4b88-673">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="d4b88-674">如需如何使用範例應用程式的詳細資訊，請參閱範例應用程式的 *README.md* 檔案和本主題中的案例描述。</span><span class="sxs-lookup"><span data-stu-id="d4b88-674">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d4b88-675">必要條件</span><span class="sxs-lookup"><span data-stu-id="d4b88-675">Prerequisites</span></span>

<span data-ttu-id="d4b88-676">健康狀態檢查通常會搭配使用外部監視服務或容器協調器，來檢查應用程式的狀態。</span><span class="sxs-lookup"><span data-stu-id="d4b88-676">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="d4b88-677">將健康狀態檢查新增至應用程式之前，請決定要使用的監控系統。</span><span class="sxs-lookup"><span data-stu-id="d4b88-677">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="d4b88-678">監控系統會指定要建立哪些健康狀態檢查類型，以及如何設定其端點。</span><span class="sxs-lookup"><span data-stu-id="d4b88-678">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="d4b88-679">參考[ `Microsoft.AspNetCore.App` 中繼套件](xref:fundamentals/metapackage-app)或將封裝參考加入 [`Microsoft.AspNetCore.Diagnostics.HealthChecks`](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) 封裝。</span><span class="sxs-lookup"><span data-stu-id="d4b88-679">Reference the [`Microsoft.AspNetCore.App` metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [`Microsoft.AspNetCore.Diagnostics.HealthChecks`](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package.</span></span>

<span data-ttu-id="d4b88-680">範例應用程式提供啟動程式碼，來示範數個案例的健康狀態檢查。</span><span class="sxs-lookup"><span data-stu-id="d4b88-680">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="d4b88-681">[資料庫探查](#database-probe)案例會使用來檢查資料庫連接的健全狀況 [`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-681">The [database probe](#database-probe) scenario checks the health of a database connection using [`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="d4b88-682">[DbContext 探查](#entity-framework-core-dbcontext-probe)案例使用 EF Core `DbContext` 來檢查資料庫。</span><span class="sxs-lookup"><span data-stu-id="d4b88-682">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="d4b88-683">為了探索資料庫案例，範例應用程式會：</span><span class="sxs-lookup"><span data-stu-id="d4b88-683">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="d4b88-684">建立資料庫，並在檔案中提供其連接字串 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-684">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="d4b88-685">在其專案檔中具有下列套件參考：</span><span class="sxs-lookup"><span data-stu-id="d4b88-685">Has the following package references in its project file:</span></span>
  * [`AspNetCore.HealthChecks.SqlServer`](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer)
  * [`Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore`](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore)

> [!NOTE]
> <span data-ttu-id="d4b88-686">[`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) Microsoft 不會維護或支援。</span><span class="sxs-lookup"><span data-stu-id="d4b88-686">[`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="d4b88-687">另一個健康狀態檢查案例示範如何將健康狀態檢查篩選至管理連接埠。</span><span class="sxs-lookup"><span data-stu-id="d4b88-687">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="d4b88-688">範例應用程式會要求您建立 *Properties/launchSettings.json* 檔案，其中包含管理 URL 和管理連接埠。</span><span class="sxs-lookup"><span data-stu-id="d4b88-688">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="d4b88-689">如需詳細資訊，請參閱[依連接埠篩選](#filter-by-port)一節。</span><span class="sxs-lookup"><span data-stu-id="d4b88-689">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="d4b88-690">基本健康狀態探查</span><span class="sxs-lookup"><span data-stu-id="d4b88-690">Basic health probe</span></span>

<span data-ttu-id="d4b88-691">對於許多應用程式，報告應用程式是否可處理要求的基本健康狀態探查組態 (「活躍度」)，便足以探索應用程式的狀態。</span><span class="sxs-lookup"><span data-stu-id="d4b88-691">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="d4b88-692">基本設定會註冊健康情況檢查服務並呼叫健康情況檢查中介軟體，以在具有健康回應的 URL 端點回應。</span><span class="sxs-lookup"><span data-stu-id="d4b88-692">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="d4b88-693">預設並未登錄特定健康狀態檢查來測試任何特定相依性或子系統。</span><span class="sxs-lookup"><span data-stu-id="d4b88-693">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="d4b88-694">如果應用程式能夠在健康狀態端點 URL 做出回應，則視為狀況良好。</span><span class="sxs-lookup"><span data-stu-id="d4b88-694">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="d4b88-695">預設回應寫入器會將狀態 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) 作為純文字回應寫入用戶端，表示 [`HealthStatus.Healthy`](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) 、 [`HealthStatus.Degraded`](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) 或 [`HealthStatus.Unhealthy`](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) 狀態。</span><span class="sxs-lookup"><span data-stu-id="d4b88-695">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [`HealthStatus.Healthy`](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [`HealthStatus.Degraded`](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), or [`HealthStatus.Unhealthy`](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="d4b88-696">在 `Startup.ConfigureServices` 中，使用 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks%2A> 登錄健康狀態檢查服務。</span><span class="sxs-lookup"><span data-stu-id="d4b88-696">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks%2A> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d4b88-697"><xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks%2A>在的要求處理管線中新增健康狀態檢查中介軟體的端點 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-697">Add an endpoint for Health Checks Middleware with <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks%2A> in the request processing pipeline of `Startup.Configure`.</span></span>

<span data-ttu-id="d4b88-698">在範例應用程式中，健康狀態檢查端點是在 `/health` (*BasicStartup.cs*) 建立：</span><span class="sxs-lookup"><span data-stu-id="d4b88-698">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

```csharp
public class BasicStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddHealthChecks();
    }

    public void Configure(IApplicationBuilder app)
    {
        app.UseHealthChecks("/health");
    }
}
```

<span data-ttu-id="d4b88-699">若要使用範例應用程式執行基本組態案例，請在命令殼層中執行來自專案資料夾的下列命令：</span><span class="sxs-lookup"><span data-stu-id="d4b88-699">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="d4b88-700">Docker 範例</span><span class="sxs-lookup"><span data-stu-id="d4b88-700">Docker example</span></span>

<span data-ttu-id="d4b88-701">[Docker](xref:host-and-deploy/docker/index) 提供內建 `HEALTHCHECK` 指示詞，可用來檢查使用基本健康狀態檢查組態的應用程式狀態：</span><span class="sxs-lookup"><span data-stu-id="d4b88-701">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="d4b88-702">建立健康狀態檢查</span><span class="sxs-lookup"><span data-stu-id="d4b88-702">Create health checks</span></span>

<span data-ttu-id="d4b88-703">健康狀態檢查是藉由實作 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 介面來建立。</span><span class="sxs-lookup"><span data-stu-id="d4b88-703">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="d4b88-704"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync%2A> 方法會傳回 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>，指出健康狀態為 `Healthy`、`Degraded` 或 `Unhealthy`。</span><span class="sxs-lookup"><span data-stu-id="d4b88-704">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync%2A> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="d4b88-705">結果會寫成具有可設定狀態碼的純文字回應 ([健康狀態檢查選項](#health-check-options)一節中將說明如何進行組態)。</span><span class="sxs-lookup"><span data-stu-id="d4b88-705">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="d4b88-706"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> 也可以傳回選擇性索引鍵/值組。</span><span class="sxs-lookup"><span data-stu-id="d4b88-706"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

### <a name="example-health-check"></a><span data-ttu-id="d4b88-707">範例健康狀態檢查</span><span class="sxs-lookup"><span data-stu-id="d4b88-707">Example health check</span></span>

<span data-ttu-id="d4b88-708">下列 `ExampleHealthCheck` 類別示範健康情況檢查的版面配置。</span><span class="sxs-lookup"><span data-stu-id="d4b88-708">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="d4b88-709">健康情況檢查邏輯會放置在 `CheckHealthAsync` 方法中。</span><span class="sxs-lookup"><span data-stu-id="d4b88-709">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="d4b88-710">下列範例會將虛擬變數（）設定 `healthCheckResultHealthy` 為 `true` 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-710">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="d4b88-711">如果的值 `healthCheckResultHealthy` 設定為 `false` ，則 [`HealthCheckResult.Unhealthy`](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy%2A) 會傳回狀態。</span><span class="sxs-lookup"><span data-stu-id="d4b88-711">If the value of `healthCheckResultHealthy` is set to `false`, the [`HealthCheckResult.Unhealthy`](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy%2A) status is returned.</span></span>

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        var healthCheckResultHealthy = true;

        if (healthCheckResultHealthy)
        {
            return Task.FromResult(
                HealthCheckResult.Healthy("The check indicates a healthy result."));
        }

        return Task.FromResult(
            HealthCheckResult.Unhealthy("The check indicates an unhealthy result."));
    }
}
```

### <a name="register-health-check-services"></a><span data-ttu-id="d4b88-712">登錄健康狀態檢查服務</span><span class="sxs-lookup"><span data-stu-id="d4b88-712">Register health check services</span></span>

<span data-ttu-id="d4b88-713">此 `ExampleHealthCheck` 類型會新增至中的健康情況檢查服務 `Startup.ConfigureServices` <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck%2A> ：</span><span class="sxs-lookup"><span data-stu-id="d4b88-713">The `ExampleHealthCheck` type is added to health check services in `Startup.ConfigureServices` with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck%2A>:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="d4b88-714">下列範例中所顯示的 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck%2A> 多載會設定在健康狀態檢查報告失敗時所要報告的失敗狀態 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>)。</span><span class="sxs-lookup"><span data-stu-id="d4b88-714">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck%2A> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="d4b88-715">如果失敗狀態設定為 `null` (預設) ， [`HealthStatus.Unhealthy`](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) 則會回報。</span><span class="sxs-lookup"><span data-stu-id="d4b88-715">If the failure status is set to `null` (default), [`HealthStatus.Unhealthy`](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="d4b88-716">此多載對程式庫作者很有用。若健康狀態檢查實作採用此設定，則當健康狀態檢查失敗時，應用程式就會強制程式庫指出失敗狀態。</span><span class="sxs-lookup"><span data-stu-id="d4b88-716">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="d4b88-717">您可以使用「標籤」來篩選健康狀態檢查 ([篩選健康狀態檢查](#filter-health-checks)一節中將進一步說明)。</span><span class="sxs-lookup"><span data-stu-id="d4b88-717">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="d4b88-718"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck%2A> 也可以執行匿名函式。</span><span class="sxs-lookup"><span data-stu-id="d4b88-718"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck%2A> can also execute a lambda function.</span></span> <span data-ttu-id="d4b88-719">在下列 `Startup.ConfigureServices` 範例中，健康狀態檢查名稱會指定為 `Example` ，且檢查一律會傳回狀況良好的狀態：</span><span class="sxs-lookup"><span data-stu-id="d4b88-719">In the following `Startup.ConfigureServices` example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

### <a name="use-health-checks-middleware"></a><span data-ttu-id="d4b88-720">使用健康狀態檢查中介軟體</span><span class="sxs-lookup"><span data-stu-id="d4b88-720">Use Health Checks Middleware</span></span>

<span data-ttu-id="d4b88-721">在 `Startup.Configure` 中，使用端點 URL 或相對路徑呼叫處理管線中的 <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks%2A>：</span><span class="sxs-lookup"><span data-stu-id="d4b88-721">In `Startup.Configure`, call <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks%2A> in the processing pipeline with the endpoint URL or relative path:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="d4b88-722">如果健康狀態檢查應該接聽特定連接埠，請使用 <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks%2A> 的多載來設定連接埠 ([依連接埠篩選](#filter-by-port)一節中將進一步說明)：</span><span class="sxs-lookup"><span data-stu-id="d4b88-722">If the health checks should listen on a specific port, use an overload of <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks%2A> to set the port (described further in the [Filter by port](#filter-by-port) section):</span></span>

```csharp
app.UseHealthChecks("/health", port: 8000);
```

## <a name="health-check-options"></a><span data-ttu-id="d4b88-723">健康狀態檢查選項</span><span class="sxs-lookup"><span data-stu-id="d4b88-723">Health check options</span></span>

<span data-ttu-id="d4b88-724"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> 讓您有機會自訂健康狀態檢查行為：</span><span class="sxs-lookup"><span data-stu-id="d4b88-724"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="d4b88-725">篩選健康狀態檢查</span><span class="sxs-lookup"><span data-stu-id="d4b88-725">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="d4b88-726">自訂 HTTP 狀態碼</span><span class="sxs-lookup"><span data-stu-id="d4b88-726">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="d4b88-727">隱藏快取標頭</span><span class="sxs-lookup"><span data-stu-id="d4b88-727">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="d4b88-728">自訂輸出</span><span class="sxs-lookup"><span data-stu-id="d4b88-728">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="d4b88-729">篩選健康狀態檢查</span><span class="sxs-lookup"><span data-stu-id="d4b88-729">Filter health checks</span></span>

<span data-ttu-id="d4b88-730">依預設，健康狀態檢查中介軟體會執行所有已註冊的健康情況檢查。</span><span class="sxs-lookup"><span data-stu-id="d4b88-730">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="d4b88-731">若要執行健康狀態檢查子集，請對 <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> 選項提供傳回布林值的函式。</span><span class="sxs-lookup"><span data-stu-id="d4b88-731">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="d4b88-732">在下列範例中，會依函式條件陳述式中的標籤 (`bar_tag`) 篩選出 `Bar` 健康狀態檢查。只有在健康狀態檢查的 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> 屬性符合 `foo_tag` 或 `baz_tag` 時，才傳回 `true`：</span><span class="sxs-lookup"><span data-stu-id="d4b88-732">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

```csharp
using System.Threading.Tasks;
using Microsoft.AspNetCore.Diagnostics.HealthChecks;
using Microsoft.Extensions.Diagnostics.HealthChecks;

public void ConfigureServices(IServiceCollection services)
{
    services.AddHealthChecks()
        .AddCheck("Foo", () =>
            HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
        .AddCheck("Bar", () =>
            HealthCheckResult.Unhealthy("Bar is unhealthy!"), 
                tags: new[] { "bar_tag" })
        .AddCheck("Baz", () =>
            HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
}

public void Configure(IApplicationBuilder app)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
}
```

### <a name="customize-the-http-status-code"></a><span data-ttu-id="d4b88-733">自訂 HTTP 狀態碼</span><span class="sxs-lookup"><span data-stu-id="d4b88-733">Customize the HTTP status code</span></span>

<span data-ttu-id="d4b88-734">您可以使用 <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> 來自訂健康狀態與 HTTP 狀態碼的對應。</span><span class="sxs-lookup"><span data-stu-id="d4b88-734">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="d4b88-735">下列 <xref:Microsoft.AspNetCore.Http.StatusCodes> 指派是中介軟體所使用的預設值。</span><span class="sxs-lookup"><span data-stu-id="d4b88-735">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="d4b88-736">請變更狀態碼值以符合您的需求。</span><span class="sxs-lookup"><span data-stu-id="d4b88-736">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="d4b88-737">在 `Startup.Configure` 中：</span><span class="sxs-lookup"><span data-stu-id="d4b88-737">In `Startup.Configure`:</span></span>

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResultStatusCodes =
    {
        [HealthStatus.Healthy] = StatusCodes.Status200OK,
        [HealthStatus.Degraded] = StatusCodes.Status200OK,
        [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
    }
});
```

### <a name="suppress-cache-headers"></a><span data-ttu-id="d4b88-738">隱藏快取標頭</span><span class="sxs-lookup"><span data-stu-id="d4b88-738">Suppress cache headers</span></span>

<span data-ttu-id="d4b88-739"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> 控制健全狀況檢查中介軟體是否會將 HTTP 標頭新增至探查回應，以防止回應快取。</span><span class="sxs-lookup"><span data-stu-id="d4b88-739"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="d4b88-740">如果值為 `false` (預設)，則中介軟體會設定或覆寫 `Cache-Control`、`Expires` 和 `Pragma` 標頭，以防止回應快取。</span><span class="sxs-lookup"><span data-stu-id="d4b88-740">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="d4b88-741">如果值為 `true`，則中介軟體不會修改回應的快取標頭。</span><span class="sxs-lookup"><span data-stu-id="d4b88-741">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="d4b88-742">在 `Startup.Configure` 中：</span><span class="sxs-lookup"><span data-stu-id="d4b88-742">In `Startup.Configure`:</span></span>

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    AllowCachingResponses = false
});
```

### <a name="customize-output"></a><span data-ttu-id="d4b88-743">自訂輸出</span><span class="sxs-lookup"><span data-stu-id="d4b88-743">Customize output</span></span>

<span data-ttu-id="d4b88-744"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> 選項會取得或設定用來寫入回應的委派。</span><span class="sxs-lookup"><span data-stu-id="d4b88-744">The <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> option gets or sets a delegate used to write the response.</span></span> <span data-ttu-id="d4b88-745">預設委派會以的字串值寫入最短的純文字回應 [`HealthReport.Status`](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status) 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-745">The default delegate writes a minimal plaintext response with the string value of [`HealthReport.Status`](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span>

<span data-ttu-id="d4b88-746">在 `Startup.Configure` 中：</span><span class="sxs-lookup"><span data-stu-id="d4b88-746">In `Startup.Configure`:</span></span>

```csharp
// using Microsoft.AspNetCore.Diagnostics.HealthChecks;
// using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResponseWriter = WriteResponse
});
```

<span data-ttu-id="d4b88-747">預設委派會以的字串值寫入最短的純文字回應 [`HealthReport.Status`](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status) 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-747">The default delegate writes a minimal plaintext response with the string value of [`HealthReport.Status`](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="d4b88-748">下列自訂委派會 `WriteResponse` 輸出自訂 JSON 回應：</span><span class="sxs-lookup"><span data-stu-id="d4b88-748">The following custom delegate, `WriteResponse`, outputs a custom JSON response:</span></span>

```csharp
private static Task WriteResponse(HttpContext httpContext, HealthReport result)
{
    httpContext.Response.ContentType = "application/json";

    var json = new JObject(
        new JProperty("status", result.Status.ToString()),
        new JProperty("results", new JObject(result.Entries.Select(pair =>
            new JProperty(pair.Key, new JObject(
                new JProperty("status", pair.Value.Status.ToString()),
                new JProperty("description", pair.Value.Description),
                new JProperty("data", new JObject(pair.Value.Data.Select(
                    p => new JProperty(p.Key, p.Value))))))))));
    return httpContext.Response.WriteAsync(
        json.ToString(Formatting.Indented));
}
```

<span data-ttu-id="d4b88-749">健康情況檢查系統不會提供複雜 JSON 傳回格式的內建支援，因為格式是您選擇的監視系統所特有。</span><span class="sxs-lookup"><span data-stu-id="d4b88-749">The health checks system doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="d4b88-750">`JObject`您可以視需要自訂上述範例中的，以符合您的需求。</span><span class="sxs-lookup"><span data-stu-id="d4b88-750">Feel free to customize the `JObject` in the preceding example as necessary to meet your needs.</span></span>

## <a name="database-probe"></a><span data-ttu-id="d4b88-751">資料庫探查</span><span class="sxs-lookup"><span data-stu-id="d4b88-751">Database probe</span></span>

<span data-ttu-id="d4b88-752">健康狀態檢查可指定資料庫查詢以布林測試方式來執行，藉此指出資料庫是否正常回應。</span><span class="sxs-lookup"><span data-stu-id="d4b88-752">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="d4b88-753">範例應用程式會使用 [`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) ASP.NET Core 應用程式的健康情況檢查程式庫，對 SQL Server 資料庫執行健康情況檢查。</span><span class="sxs-lookup"><span data-stu-id="d4b88-753">The sample app uses [`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="d4b88-754">`AspNetCore.Diagnostics.HealthChecks` 會對資料庫執行 `SELECT 1` 查詢，以確認資料庫連線狀況良好。</span><span class="sxs-lookup"><span data-stu-id="d4b88-754">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="d4b88-755">使用查詢檢查資料庫連線時，請選擇快速傳回的查詢。</span><span class="sxs-lookup"><span data-stu-id="d4b88-755">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="d4b88-756">查詢方法具有多載資料庫而降低其效能的風險。</span><span class="sxs-lookup"><span data-stu-id="d4b88-756">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="d4b88-757">在大部分情況下，不需要執行測試查詢。</span><span class="sxs-lookup"><span data-stu-id="d4b88-757">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="d4b88-758">只要成功建立資料庫連線就已足夠。</span><span class="sxs-lookup"><span data-stu-id="d4b88-758">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="d4b88-759">如果您發現有必要執行查詢，請選擇簡單的 SELECT 查詢，例如 `SELECT 1`。</span><span class="sxs-lookup"><span data-stu-id="d4b88-759">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="d4b88-760">包含的封裝參考 [`AspNetCore.HealthChecks.SqlServer`](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer) 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-760">Include a package reference to [`AspNetCore.HealthChecks.SqlServer`](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer).</span></span>

<span data-ttu-id="d4b88-761">在範例應用程式的檔案中提供有效的資料庫連接字串 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-761">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="d4b88-762">應用程式使用名為 `HealthCheckSample` 的 SQL Server 資料庫：</span><span class="sxs-lookup"><span data-stu-id="d4b88-762">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/2.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="d4b88-763">在 `Startup.ConfigureServices` 中，使用 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks%2A> 登錄健康狀態檢查服務。</span><span class="sxs-lookup"><span data-stu-id="d4b88-763">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks%2A> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d4b88-764">範例應用程式會使用資料庫的連接字串 (*DbHealthStartup.cs*) 來呼叫 `AddSqlServer` 方法：</span><span class="sxs-lookup"><span data-stu-id="d4b88-764">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="d4b88-765">呼叫健康情況檢查應用程式處理管線中的中介軟體 `Startup.Configure` ：</span><span class="sxs-lookup"><span data-stu-id="d4b88-765">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="d4b88-766">若要使用範例應用程式執行資料庫探查案例，請在命令殼層中執行來自專案資料夾的下列命令：</span><span class="sxs-lookup"><span data-stu-id="d4b88-766">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="d4b88-767">[`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) Microsoft 不會維護或支援。</span><span class="sxs-lookup"><span data-stu-id="d4b88-767">[`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="d4b88-768">Entity Framework Core DbContext 探查</span><span class="sxs-lookup"><span data-stu-id="d4b88-768">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="d4b88-769">`DbContext` 檢查會確認應用程式是否可與針對 EF Core `DbContext` 設定的資料庫通訊。</span><span class="sxs-lookup"><span data-stu-id="d4b88-769">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="d4b88-770">應用程式支援 `DbContext` 檢查：</span><span class="sxs-lookup"><span data-stu-id="d4b88-770">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="d4b88-771">使用 [Entity Framework (EF) Core](/ef/core/)。</span><span class="sxs-lookup"><span data-stu-id="d4b88-771">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="d4b88-772">包含的封裝參考 [`Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore`](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-772">Include a package reference to [`Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore`](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore).</span></span>

<span data-ttu-id="d4b88-773">`AddDbContextCheck<TContext>` 會登錄 `DbContext` 的健康狀態檢查。</span><span class="sxs-lookup"><span data-stu-id="d4b88-773">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="d4b88-774">`DbContext` 會以 `TContext` 形式提供給方法。</span><span class="sxs-lookup"><span data-stu-id="d4b88-774">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="d4b88-775">多載可用來設定失敗狀態、標籤和自訂測試查詢。</span><span class="sxs-lookup"><span data-stu-id="d4b88-775">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="d4b88-776">依照預設：</span><span class="sxs-lookup"><span data-stu-id="d4b88-776">By default:</span></span>

* <span data-ttu-id="d4b88-777">`DbContextHealthCheck` 會呼叫 EF Core 的 `CanConnectAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="d4b88-777">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="d4b88-778">您可以自訂使用 `AddDbContextCheck` 方法多載檢查健康狀態時所要執行的作業。</span><span class="sxs-lookup"><span data-stu-id="d4b88-778">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="d4b88-779">健康狀態檢查的名稱是 `TContext` 類型的名稱。</span><span class="sxs-lookup"><span data-stu-id="d4b88-779">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="d4b88-780">在範例應用程式中， `AppDbContext` 會在 `AddDbContextCheck` `Startup.ConfigureServices` (*DbCoNtextHealthStartup*) 中提供並註冊為服務：</span><span class="sxs-lookup"><span data-stu-id="d4b88-780">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="d4b88-781">在範例應用程式中， `UseHealthChecks` 新增中的健康情況檢查中介軟體 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-781">In the sample app, `UseHealthChecks` adds the Health Checks Middleware in `Startup.Configure`.</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="d4b88-782">若要使用範例應用程式來執行 `DbContext` 探查案例，請確認 SQL Server 執行個體中沒有連接字串所指定的資料庫。</span><span class="sxs-lookup"><span data-stu-id="d4b88-782">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="d4b88-783">如果資料庫存在，請予以刪除。</span><span class="sxs-lookup"><span data-stu-id="d4b88-783">If the database exists, delete it.</span></span>

<span data-ttu-id="d4b88-784">在命令殼層中執行來自專案資料夾的下列命令：</span><span class="sxs-lookup"><span data-stu-id="d4b88-784">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="d4b88-785">在應用程式執行之後，對瀏覽器中的 `/health` 端點提出要求來檢查健康狀態。</span><span class="sxs-lookup"><span data-stu-id="d4b88-785">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="d4b88-786">資料庫和 `AppDbContext` 不存在，因此應用程式會提供下列回應：</span><span class="sxs-lookup"><span data-stu-id="d4b88-786">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="d4b88-787">觸發範例應用程式以建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="d4b88-787">Trigger the sample app to create the database.</span></span> <span data-ttu-id="d4b88-788">對 `/createdatabase` 提出要求。</span><span class="sxs-lookup"><span data-stu-id="d4b88-788">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="d4b88-789">應用程式會回應：</span><span class="sxs-lookup"><span data-stu-id="d4b88-789">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="d4b88-790">對 `/health` 端點提出要求。</span><span class="sxs-lookup"><span data-stu-id="d4b88-790">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="d4b88-791">資料庫和內容存在，因此應用程式會回應：</span><span class="sxs-lookup"><span data-stu-id="d4b88-791">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="d4b88-792">觸發範例應用程式以刪除資料庫。</span><span class="sxs-lookup"><span data-stu-id="d4b88-792">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="d4b88-793">對 `/deletedatabase` 提出要求。</span><span class="sxs-lookup"><span data-stu-id="d4b88-793">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="d4b88-794">應用程式會回應：</span><span class="sxs-lookup"><span data-stu-id="d4b88-794">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="d4b88-795">對 `/health` 端點提出要求。</span><span class="sxs-lookup"><span data-stu-id="d4b88-795">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="d4b88-796">應用程式會提供狀況不良回應：</span><span class="sxs-lookup"><span data-stu-id="d4b88-796">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="d4b88-797">個別的整備度與活躍度探查</span><span class="sxs-lookup"><span data-stu-id="d4b88-797">Separate readiness and liveness probes</span></span>

<span data-ttu-id="d4b88-798">在某些主控案例中，使用一組健康狀態檢查來區分兩個應用程式狀態：</span><span class="sxs-lookup"><span data-stu-id="d4b88-798">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="d4b88-799">*準備就緒* 表示應用程式是否正常執行，但尚未準備好接收要求。</span><span class="sxs-lookup"><span data-stu-id="d4b88-799">*Readiness* indicates if the app is running normally but isn't ready to receive requests.</span></span>
* <span data-ttu-id="d4b88-800">*活動* 指出應用程式是否已損毀且必須重新開機。</span><span class="sxs-lookup"><span data-stu-id="d4b88-800">*Liveness* indicates if an app has crashed and must be restarted.</span></span>

<span data-ttu-id="d4b88-801">請考慮下列範例：應用程式必須先下載大型設定檔，才能開始處理要求。</span><span class="sxs-lookup"><span data-stu-id="d4b88-801">Consider the following example: An app must download a large configuration file before it's ready to process requests.</span></span> <span data-ttu-id="d4b88-802">如果初始下載失敗，我們不想要重新開機應用程式，因為應用程式可以重試下載檔案數次。</span><span class="sxs-lookup"><span data-stu-id="d4b88-802">We don't want the app to be restarted if the initial download fails because the app can retry downloading the file several times.</span></span> <span data-ttu-id="d4b88-803">我們會使用 *活動探查* 來描述進程的活動，而不會執行其他檢查。</span><span class="sxs-lookup"><span data-stu-id="d4b88-803">We use a *liveness probe* to describe the liveness of the process, no additional checks are performed.</span></span> <span data-ttu-id="d4b88-804">我們也想要避免在設定檔下載成功之前將要求傳送至應用程式。</span><span class="sxs-lookup"><span data-stu-id="d4b88-804">We also want to prevent requests from being sent to the app before the configuration file download has succeeded.</span></span> <span data-ttu-id="d4b88-805">我們會使用 *就緒探查* 來表示「未就緒」狀態，直到下載成功，而且應用程式已準備好接收要求。</span><span class="sxs-lookup"><span data-stu-id="d4b88-805">We use a *readiness probe* to indicate a "not ready" state until the download succeeds and the app is ready to receive requests.</span></span>

<span data-ttu-id="d4b88-806">範例應用程式包含健康狀態檢查，會在[託管服務](xref:fundamentals/host/hosted-services)中長時間執行的啟動工作完成時回報。</span><span class="sxs-lookup"><span data-stu-id="d4b88-806">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="d4b88-807">`StartupHostedServiceHealthCheck` 會公開屬性 `StartupTaskCompleted`。託管服務可在其長時間執行的工作完成時，將此屬性設定為 `true` (*StartupHostedServiceHealthCheck.cs*)：</span><span class="sxs-lookup"><span data-stu-id="d4b88-807">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="d4b88-808">[託管服務](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*) 會啟動長時間執行的背景工作。</span><span class="sxs-lookup"><span data-stu-id="d4b88-808">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="d4b88-809">工作完成時，`StartupHostedServiceHealthCheck.StartupTaskCompleted` 會設定為 `true`：</span><span class="sxs-lookup"><span data-stu-id="d4b88-809">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="d4b88-810">健康狀態檢查是 `Startup.ConfigureServices` 中使用 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck%2A> 隨託管服務一起登錄。</span><span class="sxs-lookup"><span data-stu-id="d4b88-810">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck%2A> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="d4b88-811">由於託管服務必須在健康狀態檢查上設定此屬性，因此也會在服務容器 (*LivenessProbeStartup.cs*) 中登錄健康狀態檢查：</span><span class="sxs-lookup"><span data-stu-id="d4b88-811">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="d4b88-812">在的應用程式處理管線中呼叫健康情況檢查中介軟體 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-812">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="d4b88-813">在範例應用程式中，健康狀態檢查端點是在 `/health/ready` (針對整備度檢查) 和 `/health/live` (針對活躍度檢查) 建立。</span><span class="sxs-lookup"><span data-stu-id="d4b88-813">In the sample app, the health check endpoints are created at `/health/ready` for the readiness check and `/health/live` for the liveness check.</span></span> <span data-ttu-id="d4b88-814">整備度檢查使用 `ready` 標籤來篩選健康狀態檢查。</span><span class="sxs-lookup"><span data-stu-id="d4b88-814">The readiness check filters health checks to the health check with the `ready` tag.</span></span> <span data-ttu-id="d4b88-815">活動檢查會 `StartupHostedServiceHealthCheck` `false` 在 (中傳回以篩選出 [`HealthCheckOptions.Predicate`](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) 詳細資訊，請參閱 [篩選健康情況檢查](#filter-health-checks)) ：</span><span class="sxs-lookup"><span data-stu-id="d4b88-815">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [`HealthCheckOptions.Predicate`](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks)):</span></span>

```csharp
app.UseHealthChecks("/health/ready", new HealthCheckOptions()
{
    Predicate = (check) => check.Tags.Contains("ready"), 
});

app.UseHealthChecks("/health/live", new HealthCheckOptions()
{
    Predicate = (_) => false
});
```

<span data-ttu-id="d4b88-816">若要使用範例應用程式執行整備度/活躍度組態案例，請在命令殼層中執行來自專案資料夾的下列命令：</span><span class="sxs-lookup"><span data-stu-id="d4b88-816">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="d4b88-817">在瀏覽器中，瀏覽 `/health/ready` 數次，直到經過 15 秒。</span><span class="sxs-lookup"><span data-stu-id="d4b88-817">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="d4b88-818">健康狀態檢查在前 15 秒會回報 *Unhealthy*。</span><span class="sxs-lookup"><span data-stu-id="d4b88-818">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="d4b88-819">15 秒之後，端點會回報 *Healthy*，這反映託管服務長時間執行的工作已完成。</span><span class="sxs-lookup"><span data-stu-id="d4b88-819">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="d4b88-820">此範例也會建立一個以 2 秒延遲執行第一次整備檢查的「健康狀態檢查發行者」(<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 實作)。</span><span class="sxs-lookup"><span data-stu-id="d4b88-820">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="d4b88-821">如需詳細資訊，請參閱[健康狀態檢查發行者](#health-check-publisher)一節。</span><span class="sxs-lookup"><span data-stu-id="d4b88-821">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="d4b88-822">Kubernetes 範例</span><span class="sxs-lookup"><span data-stu-id="d4b88-822">Kubernetes example</span></span>

<span data-ttu-id="d4b88-823">使用個別的整備度與活躍度檢查在 [Kubernetes](https://kubernetes.io/) 之類的環境中很有用。</span><span class="sxs-lookup"><span data-stu-id="d4b88-823">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="d4b88-824">在 Kubernetes 中，應用程式可能需要執行耗時的啟動工作才能接受要求 (例如基礎資料庫可用性測試)。</span><span class="sxs-lookup"><span data-stu-id="d4b88-824">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="d4b88-825">使用個別的檢查可讓協調器區分應用程式是否為正常運作但尚未準備好，或應用程式是否無法啟動。</span><span class="sxs-lookup"><span data-stu-id="d4b88-825">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="d4b88-826">如需 Kubernetes 中整備度與活躍度探查的詳細資訊，請參閱 Kubernetes 文件中的 [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) (設定活躍度與整備度探查)。</span><span class="sxs-lookup"><span data-stu-id="d4b88-826">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="d4b88-827">下列範例示範 Kubernetes 整備度探查組態：</span><span class="sxs-lookup"><span data-stu-id="d4b88-827">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

```
spec:
  template:
  spec:
    readinessProbe:
      # an http probe
      httpGet:
        path: /health/ready
        port: 80
      # length of time to wait for a pod to initialize
      # after pod startup, before applying health checking
      initialDelaySeconds: 30
      timeoutSeconds: 1
    ports:
      - containerPort: 80
```

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="d4b88-828">透過自訂回應寫入器的計量型探查</span><span class="sxs-lookup"><span data-stu-id="d4b88-828">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="d4b88-829">範例應用程式示範透過自訂回應寫入器的記憶體健康狀態檢查。</span><span class="sxs-lookup"><span data-stu-id="d4b88-829">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="d4b88-830">`MemoryHealthCheck` 如果應用程式在範例應用程式) 中使用超過指定的記憶體閾值 (1 GB，則報告狀況不良。</span><span class="sxs-lookup"><span data-stu-id="d4b88-830">`MemoryHealthCheck` reports an unhealthy status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="d4b88-831"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> 包含應用程式的記憶體回收行程 (GC) 資訊 (*MemoryHealthCheck.cs*)：</span><span class="sxs-lookup"><span data-stu-id="d4b88-831">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="d4b88-832">在 `Startup.ConfigureServices` 中，使用 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks%2A> 登錄健康狀態檢查服務。</span><span class="sxs-lookup"><span data-stu-id="d4b88-832">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks%2A> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d4b88-833">`MemoryHealthCheck` 會登錄為服務，而不是將健康狀態檢查傳遞至 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck%2A> 以啟用檢查。</span><span class="sxs-lookup"><span data-stu-id="d4b88-833">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck%2A>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="d4b88-834">所有 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 登錄的服務都可供健康狀態檢查服務和中介軟體使用。</span><span class="sxs-lookup"><span data-stu-id="d4b88-834">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="d4b88-835">建議將健康狀態檢查服務登錄為單一服務。</span><span class="sxs-lookup"><span data-stu-id="d4b88-835">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="d4b88-836">在範例應用程式中 (*CustomWriterStartup .cs*) ：</span><span class="sxs-lookup"><span data-stu-id="d4b88-836">In the sample app (*CustomWriterStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="d4b88-837">在的應用程式處理管線中呼叫健康情況檢查中介軟體 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-837">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="d4b88-838">當健康狀態檢查執行時，會將 `WriteResponse` 委派提供給 `ResponseWriter` 屬性以輸出自訂 JSON 回應：</span><span class="sxs-lookup"><span data-stu-id="d4b88-838">A `WriteResponse` delegate is provided to the `ResponseWriter` property to output a custom JSON response when the health check executes:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        // This custom writer formats the detailed status as JSON.
        ResponseWriter = WriteResponse
    });
}
```

<span data-ttu-id="d4b88-839">`WriteResponse` 方法會將 `CompositeHealthCheckResult` 格式化為 JSON 物件，並產生 JSON 輸出作為健康狀態檢查回應：</span><span class="sxs-lookup"><span data-stu-id="d4b88-839">The `WriteResponse` method formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

<span data-ttu-id="d4b88-840">若要使用範例應用程式透過自訂回應寫入器輸出執行計量型探查，請在命令殼層中執行來自專案資料夾的下列命令：</span><span class="sxs-lookup"><span data-stu-id="d4b88-840">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="d4b88-841">[`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 包含以計量為基礎的健康情況檢查案例，包括磁片儲存體和最大值活動檢查。</span><span class="sxs-lookup"><span data-stu-id="d4b88-841">[`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="d4b88-842">[`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) Microsoft 不會維護或支援。</span><span class="sxs-lookup"><span data-stu-id="d4b88-842">[`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="d4b88-843">依連接埠篩選</span><span class="sxs-lookup"><span data-stu-id="d4b88-843">Filter by port</span></span>

<span data-ttu-id="d4b88-844">呼叫 <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks%2A> 並提供連接埠會限制對指定的連接埠提出健康狀態檢查要求。</span><span class="sxs-lookup"><span data-stu-id="d4b88-844">Calling <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks%2A> with a port restricts health check requests to the port specified.</span></span> <span data-ttu-id="d4b88-845">這通常會用於容器環境，以公開監視服務的連接埠。</span><span class="sxs-lookup"><span data-stu-id="d4b88-845">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="d4b88-846">範例應用程式使用[環境變數組態提供者](xref:fundamentals/configuration/index#environment-variables-configuration-provider)來設定連接埠。</span><span class="sxs-lookup"><span data-stu-id="d4b88-846">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span> <span data-ttu-id="d4b88-847">連接埠是在 *launchSettings.json* 檔案中設定，並透過環境變數傳遞至組態提供者。</span><span class="sxs-lookup"><span data-stu-id="d4b88-847">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="d4b88-848">您也必須將伺服器設定為在管理連接埠上接聽要求。</span><span class="sxs-lookup"><span data-stu-id="d4b88-848">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="d4b88-849">若要使用範例應用程式示範管理連接埠組態，請在 *Properties* 資料夾中建立 *launchSettings.json* 檔案。</span><span class="sxs-lookup"><span data-stu-id="d4b88-849">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="d4b88-850">範例應用程式中檔案的下列 *屬性/launchSettings.js* 不包含在範例應用程式的專案檔中，必須手動建立：</span><span class="sxs-lookup"><span data-stu-id="d4b88-850">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

```json
{
  "profiles": {
    "SampleApp": {
      "commandName": "Project",
      "commandLineArgs": "",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_URLS": "http://localhost:5000/;http://localhost:5001/",
        "ASPNETCORE_MANAGEMENTPORT": "5001"
      },
      "applicationUrl": "http://localhost:5000/"
    }
  }
}
```

<span data-ttu-id="d4b88-851">在 `Startup.ConfigureServices` 中，使用 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks%2A> 登錄健康狀態檢查服務。</span><span class="sxs-lookup"><span data-stu-id="d4b88-851">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks%2A> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d4b88-852">呼叫 <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks%2A> 會指定管理連接埠 (*ManagementPortStartup.cs*)：</span><span class="sxs-lookup"><span data-stu-id="d4b88-852">The call to <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks%2A> specifies the management port (*ManagementPortStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ManagementPortStartup.cs?name=snippet1&highlight=17)]

> [!NOTE]
> <span data-ttu-id="d4b88-853">您可以在程式碼中明確設定 URL 和管理連接埠，避免在範例應用程式中建立 *launchSettings.json* 檔案。</span><span class="sxs-lookup"><span data-stu-id="d4b88-853">You can avoid creating the *launchSettings.json* file in the sample app by setting the URLs and management port explicitly in code.</span></span> <span data-ttu-id="d4b88-854">在 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> 建立所在的 *Program.cs* 中，新增 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls%2A> 呼叫並提供應用程式的正常回應端點和管理連接埠端點。</span><span class="sxs-lookup"><span data-stu-id="d4b88-854">In *Program.cs* where the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls%2A> and provide the app's normal response endpoint and the management port endpoint.</span></span> <span data-ttu-id="d4b88-855">在 <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks%2A> 呼叫所在的 *ManagementPortStartup.cs* 中，明確指定管理連接埠。</span><span class="sxs-lookup"><span data-stu-id="d4b88-855">In *ManagementPortStartup.cs* where <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks%2A> is called, specify the management port explicitly.</span></span>
>
> <span data-ttu-id="d4b88-856">*Program .cs*：</span><span class="sxs-lookup"><span data-stu-id="d4b88-856">*Program.cs*:</span></span>
>
> ```csharp
> return new WebHostBuilder()
>     .UseConfiguration(config)
>     .UseUrls("http://localhost:5000/;http://localhost:5001/")
>     .ConfigureLogging(builder =>
>     {
>         builder.SetMinimumLevel(LogLevel.Trace);
>         builder.AddConfiguration(config);
>         builder.AddConsole();
>     })
>     .UseKestrel()
>     .UseStartup(startupType)
>     .Build();
> ```
>
> <span data-ttu-id="d4b88-857">*ManagementPortStartup.cs*：</span><span class="sxs-lookup"><span data-stu-id="d4b88-857">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseHealthChecks("/health", port: 5001);
> ```

<span data-ttu-id="d4b88-858">若要使用範例應用程式執行管理連接埠組態案例，請在命令殼層中執行來自專案資料夾的下列命令：</span><span class="sxs-lookup"><span data-stu-id="d4b88-858">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="d4b88-859">發佈健康狀態檢查程式庫</span><span class="sxs-lookup"><span data-stu-id="d4b88-859">Distribute a health check library</span></span>

<span data-ttu-id="d4b88-860">若要發佈健康狀態檢查作為程式庫：</span><span class="sxs-lookup"><span data-stu-id="d4b88-860">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="d4b88-861">寫入健康狀態檢查，將 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 介面當做獨立類別來實作。</span><span class="sxs-lookup"><span data-stu-id="d4b88-861">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="d4b88-862">此類別可能依賴[相依性插入 (DI)](xref:fundamentals/dependency-injection)、類型啟用和[具名選項](xref:fundamentals/configuration/options)來存取組態資料。</span><span class="sxs-lookup"><span data-stu-id="d4b88-862">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="d4b88-863">在健康狀態檢查邏輯中 `CheckHealthAsync` ：</span><span class="sxs-lookup"><span data-stu-id="d4b88-863">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="d4b88-864">`data1` 和 `data2` 在方法中用來執行探查的健康情況檢查邏輯。</span><span class="sxs-lookup"><span data-stu-id="d4b88-864">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="d4b88-865">`AccessViolationException` 會進行處理。</span><span class="sxs-lookup"><span data-stu-id="d4b88-865">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="d4b88-866">當 <xref:System.AccessViolationException> 發生時， <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> 會傳回， <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> 以允許使用者設定健康情況檢查失敗狀態。</span><span class="sxs-lookup"><span data-stu-id="d4b88-866">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

   ```csharp
   using System;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public class ExampleHealthCheck : IHealthCheck
   {
       private readonly string _data1;
       private readonly int? _data2;

       public ExampleHealthCheck(string data1, int? data2)
       {
           _data1 = data1 ?? throw new ArgumentNullException(nameof(data1));
           _data2 = data2 ?? throw new ArgumentNullException(nameof(data2));
       }

       public async Task<HealthCheckResult> CheckHealthAsync(
           HealthCheckContext context, CancellationToken cancellationToken)
       {
           try
           {
               return HealthCheckResult.Healthy();
           }
           catch (AccessViolationException ex)
           {
               return new HealthCheckResult(
                   context.Registration.FailureStatus,
                   description: "An access violation occurred during the check.",
                   exception: ex,
                   data: null);
           }
       }
   }
   ```

1. <span data-ttu-id="d4b88-867">使用取用應用程式在其 `Startup.Configure` 方法中呼叫的參數，來寫入延伸模組。</span><span class="sxs-lookup"><span data-stu-id="d4b88-867">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="d4b88-868">在下列範例中，假設健康狀態檢查方法簽章如下：</span><span class="sxs-lookup"><span data-stu-id="d4b88-868">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="d4b88-869">上述簽章指出 `ExampleHealthCheck` 需要額外的資料，才能處理健康狀態檢查探查邏輯。</span><span class="sxs-lookup"><span data-stu-id="d4b88-869">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="d4b88-870">此資料會提供給委派，以在使用延伸模組登錄健康狀態檢查時，用來建立健康狀態檢查執行個體。</span><span class="sxs-lookup"><span data-stu-id="d4b88-870">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="d4b88-871">在下列範例中，呼叫者會選擇性地指定：</span><span class="sxs-lookup"><span data-stu-id="d4b88-871">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="d4b88-872">健康狀態檢查名稱 (`name`)。</span><span class="sxs-lookup"><span data-stu-id="d4b88-872">health check name (`name`).</span></span> <span data-ttu-id="d4b88-873">如果為 `null`，則會使用 `example_health_check`。</span><span class="sxs-lookup"><span data-stu-id="d4b88-873">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="d4b88-874">健康狀態檢查的字串資料點 (`data1`)。</span><span class="sxs-lookup"><span data-stu-id="d4b88-874">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="d4b88-875">健康狀態檢查的整數資料點 (`data2`)。</span><span class="sxs-lookup"><span data-stu-id="d4b88-875">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="d4b88-876">如果為 `null`，則會使用 `1`。</span><span class="sxs-lookup"><span data-stu-id="d4b88-876">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="d4b88-877">失敗狀態 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>)。</span><span class="sxs-lookup"><span data-stu-id="d4b88-877">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="d4b88-878">預設為 `null`。</span><span class="sxs-lookup"><span data-stu-id="d4b88-878">The default is `null`.</span></span> <span data-ttu-id="d4b88-879">如果為 `null` ， [`HealthStatus.Unhealthy`](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) 則回報失敗狀態。</span><span class="sxs-lookup"><span data-stu-id="d4b88-879">If `null`, [`HealthStatus.Unhealthy`](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="d4b88-880">標籤 (`IEnumerable<string>`)。</span><span class="sxs-lookup"><span data-stu-id="d4b88-880">tags (`IEnumerable<string>`).</span></span>

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string DefaultName = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder,
           string name = default,
           string data1,
           int data2 = 1,
           HealthStatus? failureStatus = default,
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? DefaultName,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a><span data-ttu-id="d4b88-881">健康狀態檢查發行者</span><span class="sxs-lookup"><span data-stu-id="d4b88-881">Health Check Publisher</span></span>

<span data-ttu-id="d4b88-882">當 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 新增至服務容器時，健康狀態檢查系統會定期執行健康狀態檢查，並呼叫 `PublishAsync` 傳回結果。</span><span class="sxs-lookup"><span data-stu-id="d4b88-882">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="d4b88-883">這對推送型健康狀態監控系統案例很有用，其預期每個處理序會定期呼叫監控系統來判斷健康狀態。</span><span class="sxs-lookup"><span data-stu-id="d4b88-883">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="d4b88-884"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 介面有單一方法：</span><span class="sxs-lookup"><span data-stu-id="d4b88-884">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="d4b88-885"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> 可讓您設定：</span><span class="sxs-lookup"><span data-stu-id="d4b88-885"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="d4b88-886"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>：在執行實例之前，應用程式啟動後所套用的初始延遲 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-886"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="d4b88-887">在啟動後就會套用延遲，但不會套用至後續的反覆項目。</span><span class="sxs-lookup"><span data-stu-id="d4b88-887">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="d4b88-888">預設值是五秒鐘。</span><span class="sxs-lookup"><span data-stu-id="d4b88-888">The default value is five seconds.</span></span>
* <span data-ttu-id="d4b88-889"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>：執行的期間 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-889"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="d4b88-890">預設值為 30 秒。</span><span class="sxs-lookup"><span data-stu-id="d4b88-890">The default value is 30 seconds.</span></span>
* <span data-ttu-id="d4b88-891"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>：如果 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> `null` (預設) ，健全狀況檢查發行者服務會執行所有已註冊的健康情況檢查。</span><span class="sxs-lookup"><span data-stu-id="d4b88-891"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="d4b88-892">若要執行一部分的健康狀態檢查，請提供可篩選該組檢查的函式。</span><span class="sxs-lookup"><span data-stu-id="d4b88-892">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="d4b88-893">每個期間都會評估該述詞。</span><span class="sxs-lookup"><span data-stu-id="d4b88-893">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="d4b88-894"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>：針對所有實例執行健康情況檢查的超時時間 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-894"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="d4b88-895">若要在沒有逾時的情況下執行，請使用 <xref:System.Threading.Timeout.InfiniteTimeSpan>。</span><span class="sxs-lookup"><span data-stu-id="d4b88-895">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="d4b88-896">預設值為 30 秒。</span><span class="sxs-lookup"><span data-stu-id="d4b88-896">The default value is 30 seconds.</span></span>

> [!WARNING]
> <span data-ttu-id="d4b88-897">在 ASP.NET Core 2.2 版中，設定 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>並不會獲得 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 實作遵守；它會設定 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> 的值。</span><span class="sxs-lookup"><span data-stu-id="d4b88-897">In the ASP.NET Core 2.2 release, setting <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> isn't honored by the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation; it sets the value of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>.</span></span> <span data-ttu-id="d4b88-898">ASP.NET Core 3.0 已解決此問題。</span><span class="sxs-lookup"><span data-stu-id="d4b88-898">This issue has been addressed in ASP.NET Core 3.0.</span></span>

<span data-ttu-id="d4b88-899">在範例應用程式中，`ReadinessPublisher` 是一個 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 實作。</span><span class="sxs-lookup"><span data-stu-id="d4b88-899">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="d4b88-900">每個檢查的健康狀態檢查狀態都會記錄為：</span><span class="sxs-lookup"><span data-stu-id="d4b88-900">The health check status is logged for each check as either:</span></span>

* <span data-ttu-id="d4b88-901"><xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation%2A>如果健康狀態檢查狀態為， () 資訊 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy> 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-901">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation%2A>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="d4b88-902"><xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>當狀態為或時，)  (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> 錯誤 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy> 。</span><span class="sxs-lookup"><span data-stu-id="d4b88-902">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="d4b88-903">在範例應用程式的 `LivenessProbeStartup` 範例中，`StartupHostedService` 整備檢查具有 2 秒的啟動延遲，且每 30 秒會執行一次檢查。</span><span class="sxs-lookup"><span data-stu-id="d4b88-903">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="d4b88-904">為了啟用 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 實作，此範例會在[相依性插入 (DI)](xref:fundamentals/dependency-injection) 容器中將 `ReadinessPublisher` 註冊為單一服務：</span><span class="sxs-lookup"><span data-stu-id="d4b88-904">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices&highlight=12-17,28)]

> [!NOTE]
> <span data-ttu-id="d4b88-905">以下因應措施可允許在已將一或多個其他託管服務新增至應用程式的情況下，將 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 執行個體新增至服務容器。</span><span class="sxs-lookup"><span data-stu-id="d4b88-905">The following workaround permits adding an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instance to the service container when one or more other hosted services have already been added to the app.</span></span> <span data-ttu-id="d4b88-906">ASP.NET Core 3.0 不需要此因應措施。</span><span class="sxs-lookup"><span data-stu-id="d4b88-906">This workaround won't isn't required in ASP.NET Core 3.0.</span></span>
>
> ```csharp
> private const string HealthCheckServiceAssembly =
>     "Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherHostedService";
>
> services.TryAddEnumerable(
>     ServiceDescriptor.Singleton(typeof(IHostedService),
>         typeof(HealthCheckPublisherOptions).Assembly
>             .GetType(HealthCheckServiceAssembly)));
> ```

> [!NOTE]
> <span data-ttu-id="d4b88-907">[`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 包含數個系統的發行者，包括 [Application Insights](/azure/application-insights/app-insights-overview)。</span><span class="sxs-lookup"><span data-stu-id="d4b88-907">[`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="d4b88-908">[`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) Microsoft 不會維護或支援。</span><span class="sxs-lookup"><span data-stu-id="d4b88-908">[`AspNetCore.Diagnostics.HealthChecks`](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="d4b88-909">利用 MapWhen 限制健康情況檢查</span><span class="sxs-lookup"><span data-stu-id="d4b88-909">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="d4b88-910">使用 <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> 來有條件地將健康情況檢查端點的要求管線分支。</span><span class="sxs-lookup"><span data-stu-id="d4b88-910">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="d4b88-911">在下列範例中， `MapWhen` 如果收到端點的 GET 要求，則會將要求管線分支以啟用健康情況檢查中介軟體 `api/HealthCheck` ：</span><span class="sxs-lookup"><span data-stu-id="d4b88-911">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseMvc();
```

<span data-ttu-id="d4b88-912">如需詳細資訊，請參閱<xref:fundamentals/middleware/index#use-run-and-map>。</span><span class="sxs-lookup"><span data-stu-id="d4b88-912">For more information, see <xref:fundamentals/middleware/index#use-run-and-map>.</span></span>

::: moniker-end
