---
title: ASP.NET Core 中的健康狀態檢查
author: rick-anderson
description: 了解如何為 ASP.NET Core 基礎結構 (例如應用程式和資料庫) 設定健康狀態檢查。
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 06/22/2020
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
ms.openlocfilehash: 272f1f098ca90434f26d6c057859a00b5519602e
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588902"
---
# <a name="health-checks-in-aspnet-core"></a><span data-ttu-id="0c0bc-103">ASP.NET Core 中的健康狀態檢查</span><span class="sxs-lookup"><span data-stu-id="0c0bc-103">Health checks in ASP.NET Core</span></span>

<span data-ttu-id="0c0bc-104">依 [Glenn Condron](https://github.com/glennc)</span><span class="sxs-lookup"><span data-stu-id="0c0bc-104">By [Glenn Condron](https://github.com/glennc)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0c0bc-105">ASP.NET Core 提供健康狀態檢查中介軟體和程式庫，以報告應用程式基礎結構元件的健康情況。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-105">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="0c0bc-106">應用程式會將健康狀態檢查公開為 HTTP 端點。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-106">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="0c0bc-107">您可以針對各種即時監控案例來設定健康狀態檢查端點：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-107">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="0c0bc-108">容器協調器和負載平衡器可以使用健康狀態探查，來檢查應用程式的狀態。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-108">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="0c0bc-109">例如，容器協調器可能會暫停輪流部署或重新啟動容器，來回應失敗的健康狀態檢查。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-109">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="0c0bc-110">負載平衡器可能會將流量從失敗的執行個體路由傳送至狀況良好的執行個體，來回應狀況不良的應用程式。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-110">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="0c0bc-111">您可以監控所使用記憶體、磁碟及其他實體伺服器資源的健康狀態。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-111">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="0c0bc-112">健康狀態檢查可以測試應用程式的相依性 (例如資料庫和外部服務端點)，確認其是否可用且正常運作。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-112">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="0c0bc-113">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/health-checks/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="0c0bc-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0c0bc-114">範例應用程式包含本主題中所述的案例範例。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-114">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="0c0bc-115">若要在指定的案例中執行範例應用程式，請在命令殼層中使用來自專案資料夾的 [dotnet run](/dotnet/core/tools/dotnet-run) 命令。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-115">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="0c0bc-116">如需如何使用範例應用程式的詳細資訊，請參閱範例應用程式的 *README.md* 檔案和本主題中的案例描述。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-116">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0c0bc-117">必要條件</span><span class="sxs-lookup"><span data-stu-id="0c0bc-117">Prerequisites</span></span>

<span data-ttu-id="0c0bc-118">健康狀態檢查通常會搭配使用外部監視服務或容器協調器，來檢查應用程式的狀態。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-118">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="0c0bc-119">將健康狀態檢查新增至應用程式之前，請決定要使用的監控系統。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-119">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="0c0bc-120">監控系統會指定要建立哪些健康狀態檢查類型，以及如何設定其端點。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-120">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="0c0bc-121">ASP.NET Core 應用程式會隱含參考 [AspNetCore HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) 套件。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-121">The [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package is referenced implicitly for ASP.NET Core apps.</span></span> <span data-ttu-id="0c0bc-122">若要使用 Entity Framework Core 執行健康情況檢查，請將套件參考新增至 [HealthChecks. microsoft.entityframeworkcore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) 套件。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-122">To perform health checks using Entity Framework Core, add a package reference to the [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) package.</span></span>

<span data-ttu-id="0c0bc-123">範例應用程式提供啟動程式碼，來示範數個案例的健康狀態檢查。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-123">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="0c0bc-124">[資料庫探查](#database-probe)案例會使用 [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) \(英文\) 來檢查資料庫連線的健康情況。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-124">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="0c0bc-125">[DbContext 探查](#entity-framework-core-dbcontext-probe)案例使用 EF Core `DbContext` 來檢查資料庫。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-125">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="0c0bc-126">為了探索資料庫案例，範例應用程式會：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-126">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="0c0bc-127">建立資料庫，並在檔案中提供其連接字串 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-127">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="0c0bc-128">在其專案檔中具有下列套件參考：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-128">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="0c0bc-129">AspNetCore.HealthChecks.SqlServer</span><span class="sxs-lookup"><span data-stu-id="0c0bc-129">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="0c0bc-130">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="0c0bc-130">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="0c0bc-131">[AspNetCore](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 不會由 Microsoft 維護或支援 HealthChecks。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-131">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="0c0bc-132">另一個健康狀態檢查案例示範如何將健康狀態檢查篩選至管理連接埠。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-132">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="0c0bc-133">範例應用程式會要求您建立 *Properties/launchSettings.json* 檔案，其中包含管理 URL 和管理連接埠。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-133">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="0c0bc-134">如需詳細資訊，請參閱[依連接埠篩選](#filter-by-port)一節。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-134">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="0c0bc-135">基本健康狀態探查</span><span class="sxs-lookup"><span data-stu-id="0c0bc-135">Basic health probe</span></span>

<span data-ttu-id="0c0bc-136">對於許多應用程式，報告應用程式是否可處理要求的基本健康狀態探查組態 (「活躍度」)，便足以探索應用程式的狀態。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-136">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="0c0bc-137">基本設定會註冊健康情況檢查服務並呼叫健康情況檢查中介軟體，以在具有健康回應的 URL 端點回應。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-137">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="0c0bc-138">預設並未登錄特定健康狀態檢查來測試任何特定相依性或子系統。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-138">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="0c0bc-139">如果應用程式能夠在健康狀態端點 URL 做出回應，則視為狀況良好。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-139">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="0c0bc-140">預設回應寫入器會將狀態 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) 以純文字回應形式回寫到用戶端，指出狀態為 [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)[HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) 或 [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-140">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="0c0bc-141">在 `Startup.ConfigureServices` 中，使用 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> 登錄健康狀態檢查服務。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-141">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0c0bc-142">藉由在中呼叫來建立健康情況檢查端點 `MapHealthChecks` `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-142">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="0c0bc-143">在範例應用程式中，健康狀態檢查端點是在 `/health` (*BasicStartup.cs*) 建立：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-143">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

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

<span data-ttu-id="0c0bc-144">若要使用範例應用程式執行基本組態案例，請在命令殼層中執行來自專案資料夾的下列命令：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-144">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="0c0bc-145">Docker 範例</span><span class="sxs-lookup"><span data-stu-id="0c0bc-145">Docker example</span></span>

<span data-ttu-id="0c0bc-146">[Docker](xref:host-and-deploy/docker/index) 提供內建 `HEALTHCHECK` 指示詞，可用來檢查使用基本健康狀態檢查組態的應用程式狀態：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-146">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="0c0bc-147">建立健康狀態檢查</span><span class="sxs-lookup"><span data-stu-id="0c0bc-147">Create health checks</span></span>

<span data-ttu-id="0c0bc-148">健康狀態檢查是藉由實作 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 介面來建立。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-148">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="0c0bc-149"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> 方法會傳回 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>，指出健康狀態為 `Healthy`、`Degraded` 或 `Unhealthy`。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-149">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="0c0bc-150">結果會寫成具有可設定狀態碼的純文字回應 ([健康狀態檢查選項](#health-check-options)一節中將說明如何進行組態)。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-150">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="0c0bc-151"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> 也可以傳回選擇性索引鍵/值組。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-151"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

<span data-ttu-id="0c0bc-152">下列 `ExampleHealthCheck` 類別示範健康情況檢查的版面配置。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-152">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="0c0bc-153">健康情況檢查邏輯會放置在 `CheckHealthAsync` 方法中。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-153">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="0c0bc-154">下列範例會將虛擬變數（）設定 `healthCheckResultHealthy` 為 `true` 。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-154">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="0c0bc-155">如果的值 `healthCheckResultHealthy` 設定為 `false` ，則會傳回 [HealthCheckResult 狀況不良](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) 狀態。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-155">If the value of `healthCheckResultHealthy` is set to `false`, the [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status is returned.</span></span>

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

## <a name="register-health-check-services"></a><span data-ttu-id="0c0bc-156">登錄健康狀態檢查服務</span><span class="sxs-lookup"><span data-stu-id="0c0bc-156">Register health check services</span></span>

<span data-ttu-id="0c0bc-157">此 `ExampleHealthCheck` 類型會新增至具有下列功能的健康情況檢查服務 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-157">The `ExampleHealthCheck` type is added to health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="0c0bc-158">下列範例中所顯示的 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> 多載會設定在健康狀態檢查報告失敗時所要報告的失敗狀態 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>)。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-158">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="0c0bc-159">如果將失敗狀態設定為 `null` (預設)，則會回報 [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-159">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="0c0bc-160">此多載對程式庫作者很有用。若健康狀態檢查實作採用此設定，則當健康狀態檢查失敗時，應用程式就會強制程式庫指出失敗狀態。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-160">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="0c0bc-161">您可以使用「標籤」來篩選健康狀態檢查 ([篩選健康狀態檢查](#filter-health-checks)一節中將進一步說明)。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-161">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="0c0bc-162"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> 也可以執行匿名函式。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-162"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="0c0bc-163">在下列範例中，健康狀態檢查名稱指定為 `Example`，且檢查一律會傳回狀況良好狀態：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-163">In the following example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

<span data-ttu-id="0c0bc-164">呼叫 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*> 以將引數傳遞至健康情況檢查執行。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-164">Call <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*> to pass arguments to a health check implementation.</span></span> <span data-ttu-id="0c0bc-165">在下列範例中， `TestHealthCheckWithArgs` 會接受一個整數和一個在呼叫時使用的字串 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> ：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-165">In the following example, `TestHealthCheckWithArgs` accepts an integer and a string for use when <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> is called:</span></span>

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

<span data-ttu-id="0c0bc-166">`TestHealthCheckWithArgs` 的註冊方式是 `AddTypeActivatedCheck` 以傳遞至實作為的整數和字串來呼叫：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-166">`TestHealthCheckWithArgs` is registered by calling `AddTypeActivatedCheck` with the integer and string passed to the implementation:</span></span>

```csharp
services.AddHealthChecks()
    .AddTypeActivatedCheck<TestHealthCheckWithArgs>(
        "test", 
        failureStatus: HealthStatus.Degraded, 
        tags: new[] { "example" }, 
        args: new object[] { 5, "string" });
```

## <a name="use-health-checks-routing"></a><span data-ttu-id="0c0bc-167">使用健康情況檢查路由</span><span class="sxs-lookup"><span data-stu-id="0c0bc-167">Use Health Checks Routing</span></span>

<span data-ttu-id="0c0bc-168">在中 `Startup.Configure` ， `MapHealthChecks` 使用端點 URL 或相對路徑呼叫端點產生器：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-168">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

### <a name="require-host"></a><span data-ttu-id="0c0bc-169">需要主機</span><span class="sxs-lookup"><span data-stu-id="0c0bc-169">Require host</span></span>

<span data-ttu-id="0c0bc-170">呼叫 `RequireHost` 以指定一或多個允許的主機作為健康情況檢查端點。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-170">Call `RequireHost` to specify one or more permitted hosts for the health check endpoint.</span></span> <span data-ttu-id="0c0bc-171">主機應該是 Unicode，而不是 punycode，而且可能包含埠。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-171">Hosts should be Unicode rather than punycode and may include a port.</span></span> <span data-ttu-id="0c0bc-172">如果未提供集合，則會接受任何主機。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-172">If a collection isn't supplied, any host is accepted.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireHost("www.contoso.com:5001");
});
```

<span data-ttu-id="0c0bc-173">如需詳細資訊，請參閱[依連接埠篩選](#filter-by-port)一節。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-173">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

### <a name="require-authorization"></a><span data-ttu-id="0c0bc-174">需要授權</span><span class="sxs-lookup"><span data-stu-id="0c0bc-174">Require authorization</span></span>

<span data-ttu-id="0c0bc-175">呼叫 `RequireAuthorization` 以在健康情況檢查要求端點上執行授權中介軟體。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-175">Call `RequireAuthorization` to run Authorization Middleware on the health check request endpoint.</span></span> <span data-ttu-id="0c0bc-176">`RequireAuthorization`多載接受一或多個授權原則。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-176">A `RequireAuthorization` overload accepts one or more authorization policies.</span></span> <span data-ttu-id="0c0bc-177">如果未提供原則，則會使用預設授權原則。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-177">If a policy isn't provided, the default authorization policy is used.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireAuthorization();
});
```

### <a name="enable-cross-origin-requests-cors"></a><span data-ttu-id="0c0bc-178">啟用跨原始來源要求 (CORS)</span><span class="sxs-lookup"><span data-stu-id="0c0bc-178">Enable Cross-Origin Requests (CORS)</span></span>

<span data-ttu-id="0c0bc-179">雖然以手動方式從瀏覽器執行健康情況檢查不是常見的使用案例，但您可以藉由呼叫健康情況檢查端點來啟用 CORS 中介軟體 `RequireCors` 。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-179">Although performing health checks manually from a browser isn't a common use scenario, CORS Middleware can be enabled by calling `RequireCors` on health checks endpoints.</span></span> <span data-ttu-id="0c0bc-180">多載會 `RequireCors` 接受 CORS 原則產生器委派 (`CorsPolicyBuilder`) 或原則名稱。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-180">A `RequireCors` overload accepts a CORS policy builder delegate (`CorsPolicyBuilder`) or a policy name.</span></span> <span data-ttu-id="0c0bc-181">如果未提供原則，則會使用預設 CORS 原則。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-181">If a policy isn't provided, the default CORS policy is used.</span></span> <span data-ttu-id="0c0bc-182">如需詳細資訊，請參閱<xref:security/cors>。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-182">For more information, see <xref:security/cors>.</span></span>

## <a name="health-check-options"></a><span data-ttu-id="0c0bc-183">健康狀態檢查選項</span><span class="sxs-lookup"><span data-stu-id="0c0bc-183">Health check options</span></span>

<span data-ttu-id="0c0bc-184"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> 讓您有機會自訂健康狀態檢查行為：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-184"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="0c0bc-185">篩選健康狀態檢查</span><span class="sxs-lookup"><span data-stu-id="0c0bc-185">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="0c0bc-186">自訂 HTTP 狀態碼</span><span class="sxs-lookup"><span data-stu-id="0c0bc-186">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="0c0bc-187">隱藏快取標頭</span><span class="sxs-lookup"><span data-stu-id="0c0bc-187">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="0c0bc-188">自訂輸出</span><span class="sxs-lookup"><span data-stu-id="0c0bc-188">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="0c0bc-189">篩選健康狀態檢查</span><span class="sxs-lookup"><span data-stu-id="0c0bc-189">Filter health checks</span></span>

<span data-ttu-id="0c0bc-190">依預設，健康狀態檢查中介軟體會執行所有已註冊的健康情況檢查。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-190">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="0c0bc-191">若要執行健康狀態檢查子集，請對 <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> 選項提供傳回布林值的函式。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-191">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="0c0bc-192">在下列範例中，會依函式條件陳述式中的標籤 (`bar_tag`) 篩選出 `Bar` 健康狀態檢查。只有在健康狀態檢查的 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> 屬性符合 `foo_tag` 或 `baz_tag` 時，才傳回 `true`：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-192">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

<span data-ttu-id="0c0bc-193">在 `Startup.ConfigureServices` 中：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-193">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Foo", () =>
        HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
    .AddCheck("Bar", () =>
        HealthCheckResult.Unhealthy("Bar is unhealthy!"), tags: new[] { "bar_tag" })
    .AddCheck("Baz", () =>
        HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
```

<span data-ttu-id="0c0bc-194">在中 `Startup.Configure` ，會 `Predicate` 篩選出「Bar」健康情況檢查。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-194">In `Startup.Configure`, the `Predicate` filters out the 'Bar' health check.</span></span> <span data-ttu-id="0c0bc-195">只有 Foo 和 Baz execute.：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-195">Only Foo and Baz execute.:</span></span>

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

### <a name="customize-the-http-status-code"></a><span data-ttu-id="0c0bc-196">自訂 HTTP 狀態碼</span><span class="sxs-lookup"><span data-stu-id="0c0bc-196">Customize the HTTP status code</span></span>

<span data-ttu-id="0c0bc-197">您可以使用 <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> 來自訂健康狀態與 HTTP 狀態碼的對應。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-197">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="0c0bc-198">下列 <xref:Microsoft.AspNetCore.Http.StatusCodes> 指派是中介軟體所使用的預設值。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-198">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="0c0bc-199">請變更狀態碼值以符合您的需求。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-199">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="0c0bc-200">在 `Startup.Configure` 中：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-200">In `Startup.Configure`:</span></span>

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

### <a name="suppress-cache-headers"></a><span data-ttu-id="0c0bc-201">隱藏快取標頭</span><span class="sxs-lookup"><span data-stu-id="0c0bc-201">Suppress cache headers</span></span>

<span data-ttu-id="0c0bc-202"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> 控制健全狀況檢查中介軟體是否會將 HTTP 標頭新增至探查回應，以防止回應快取。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-202"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="0c0bc-203">如果值為 `false` (預設)，則中介軟體會設定或覆寫 `Cache-Control`、`Expires` 和 `Pragma` 標頭，以防止回應快取。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-203">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="0c0bc-204">如果值為 `true`，則中介軟體不會修改回應的快取標頭。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-204">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="0c0bc-205">在 `Startup.Configure` 中：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-205">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        AllowCachingResponses = false
    });
});
```

### <a name="customize-output"></a><span data-ttu-id="0c0bc-206">自訂輸出</span><span class="sxs-lookup"><span data-stu-id="0c0bc-206">Customize output</span></span>

<span data-ttu-id="0c0bc-207">在中 `Startup.Configure` ，將 [HealthCheckOptions. ResponseWriter](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter) 選項設定為用於寫入回應的委派：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-207">In `Startup.Configure`, set the [HealthCheckOptions.ResponseWriter](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter) option to a delegate for writing the response:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
});
```

<span data-ttu-id="0c0bc-208">預設委派會使用 [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status)的字串值撰寫最基本的純文字回應。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-208">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="0c0bc-209">下列自訂委派會輸出自訂 JSON 回應。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-209">The following custom delegates output a custom JSON response.</span></span>

<span data-ttu-id="0c0bc-210">範例應用程式中的第一個範例會示範如何使用 <xref:System.Text.Json?displayProperty=fullName> ：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-210">The first example from the sample app demonstrates how to use <xref:System.Text.Json?displayProperty=fullName>:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_SystemTextJson)]

<span data-ttu-id="0c0bc-211">第二個範例示範如何 [ 在上使用Newtonsoft.Js](https://www.nuget.org/packages/Newtonsoft.Json/)：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-211">The second example demonstrates how to use [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_NewtonSoftJson)]

<span data-ttu-id="0c0bc-212">在範例應用程式中，將 `SYSTEM_TEXT_JSON` *CustomWriterStartup.cs* 中的 [預處理器](xref:index#preprocessor-directives-in-sample-code)指示詞批註為，以啟用的 `Newtonsoft.Json` 版本 `WriteResponse` 。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-212">In the sample app, comment out the `SYSTEM_TEXT_JSON` [preprocessor directive](xref:index#preprocessor-directives-in-sample-code) in *CustomWriterStartup.cs* to enable the `Newtonsoft.Json` version of `WriteResponse`.</span></span>

<span data-ttu-id="0c0bc-213">健康情況檢查 API 不會提供複雜 JSON 傳回格式的內建支援，因為格式是您選擇的監視系統所特有。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-213">The health checks API doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="0c0bc-214">視需要自訂上述範例中的回應。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-214">Customize the response in the preceding examples as needed.</span></span> <span data-ttu-id="0c0bc-215">如需有關 JSON 序列化的詳細資訊 `System.Text.Json` ，請參閱 [如何在 .net 中序列化和還原序列化 json](/dotnet/standard/serialization/system-text-json-how-to)。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-215">For more information on JSON serialization with `System.Text.Json`, see [How to serialize and deserialize JSON in .NET](/dotnet/standard/serialization/system-text-json-how-to).</span></span>

## <a name="database-probe"></a><span data-ttu-id="0c0bc-216">資料庫探查</span><span class="sxs-lookup"><span data-stu-id="0c0bc-216">Database probe</span></span>

<span data-ttu-id="0c0bc-217">健康狀態檢查可指定資料庫查詢以布林測試方式來執行，藉此指出資料庫是否正常回應。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-217">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="0c0bc-218">範例應用程式會使用 [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) \(英文\) (此為適用於 ASP.NET Core 應用程式的健康情況檢查程式庫)，在 SQL Server 資料庫上執行健康情況檢查。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-218">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="0c0bc-219">`AspNetCore.Diagnostics.HealthChecks` 會對資料庫執行 `SELECT 1` 查詢，以確認資料庫連線狀況良好。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-219">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="0c0bc-220">使用查詢檢查資料庫連線時，請選擇快速傳回的查詢。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-220">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="0c0bc-221">查詢方法具有多載資料庫而降低其效能的風險。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-221">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="0c0bc-222">在大部分情況下，不需要執行測試查詢。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-222">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="0c0bc-223">只要成功建立資料庫連線就已足夠。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-223">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="0c0bc-224">如果您發現有必要執行查詢，請選擇簡單的 SELECT 查詢，例如 `SELECT 1`。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-224">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="0c0bc-225">包含 [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/) 的套件參考。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-225">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="0c0bc-226">在範例應用程式的檔案中提供有效的資料庫連接字串 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-226">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="0c0bc-227">應用程式使用名為 `HealthCheckSample` 的 SQL Server 資料庫：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-227">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/3.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="0c0bc-228">在 `Startup.ConfigureServices` 中，使用 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> 登錄健康狀態檢查服務。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-228">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0c0bc-229">範例應用程式會使用資料庫的連接字串 (*DbHealthStartup.cs*) 來呼叫 `AddSqlServer` 方法：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-229">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="0c0bc-230">健康情況檢查端點是在中呼叫來建立的 `MapHealthChecks` `Startup.Configure` ：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-230">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="0c0bc-231">若要使用範例應用程式執行資料庫探查案例，請在命令殼層中執行來自專案資料夾的下列命令：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-231">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="0c0bc-232">[AspNetCore](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 不會由 Microsoft 維護或支援 HealthChecks。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-232">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="0c0bc-233">Entity Framework Core DbContext 探查</span><span class="sxs-lookup"><span data-stu-id="0c0bc-233">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="0c0bc-234">`DbContext` 檢查會確認應用程式是否可與針對 EF Core `DbContext` 設定的資料庫通訊。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-234">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="0c0bc-235">應用程式支援 `DbContext` 檢查：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-235">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="0c0bc-236">使用 [Entity Framework (EF) Core](/ef/core/)。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-236">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="0c0bc-237">包含 [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/) 的套件參考。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-237">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="0c0bc-238">`AddDbContextCheck<TContext>` 會登錄 `DbContext` 的健康狀態檢查。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-238">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="0c0bc-239">`DbContext` 會以 `TContext` 形式提供給方法。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-239">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="0c0bc-240">多載可用來設定失敗狀態、標籤和自訂測試查詢。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-240">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="0c0bc-241">依照預設：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-241">By default:</span></span>

* <span data-ttu-id="0c0bc-242">`DbContextHealthCheck` 會呼叫 EF Core 的 `CanConnectAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-242">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="0c0bc-243">您可以自訂使用 `AddDbContextCheck` 方法多載檢查健康狀態時所要執行的作業。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-243">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="0c0bc-244">健康狀態檢查的名稱是 `TContext` 類型的名稱。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-244">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="0c0bc-245">在範例應用程式中， `AppDbContext` 會 `AddDbContextCheck` 在 `Startup.ConfigureServices` (*DbCoNtextHealthStartup.cs*) 中提供並註冊為服務：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-245">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="0c0bc-246">健康情況檢查端點是在中呼叫來建立的 `MapHealthChecks` `Startup.Configure` ：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-246">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="0c0bc-247">若要使用範例應用程式來執行 `DbContext` 探查案例，請確認 SQL Server 執行個體中沒有連接字串所指定的資料庫。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-247">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="0c0bc-248">如果資料庫存在，請予以刪除。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-248">If the database exists, delete it.</span></span>

<span data-ttu-id="0c0bc-249">在命令殼層中執行來自專案資料夾的下列命令：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-249">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="0c0bc-250">在應用程式執行之後，對瀏覽器中的 `/health` 端點提出要求來檢查健康狀態。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-250">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="0c0bc-251">資料庫和 `AppDbContext` 不存在，因此應用程式會提供下列回應：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-251">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="0c0bc-252">觸發範例應用程式以建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-252">Trigger the sample app to create the database.</span></span> <span data-ttu-id="0c0bc-253">對 `/createdatabase` 提出要求。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-253">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="0c0bc-254">應用程式會回應：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-254">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="0c0bc-255">對 `/health` 端點提出要求。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-255">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="0c0bc-256">資料庫和內容存在，因此應用程式會回應：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-256">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="0c0bc-257">觸發範例應用程式以刪除資料庫。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-257">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="0c0bc-258">對 `/deletedatabase` 提出要求。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-258">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="0c0bc-259">應用程式會回應：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-259">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="0c0bc-260">對 `/health` 端點提出要求。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-260">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="0c0bc-261">應用程式會提供狀況不良回應：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-261">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="0c0bc-262">個別的整備度與活躍度探查</span><span class="sxs-lookup"><span data-stu-id="0c0bc-262">Separate readiness and liveness probes</span></span>

<span data-ttu-id="0c0bc-263">在某些主控案例中，使用一組健康狀態檢查來區分兩個應用程式狀態：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-263">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="0c0bc-264">*準備就緒* 表示應用程式是否正常執行，但尚未準備好接收要求。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-264">*Readiness* indicates if the app is running normally but isn't ready to receive requests.</span></span>
* <span data-ttu-id="0c0bc-265">*活動* 指出應用程式是否已損毀且必須重新開機。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-265">*Liveness* indicates if an app has crashed and must be restarted.</span></span>

<span data-ttu-id="0c0bc-266">請考慮下列範例：應用程式必須先下載大型設定檔，才能開始處理要求。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-266">Consider the following example: An app must download a large configuration file before it's ready to process requests.</span></span> <span data-ttu-id="0c0bc-267">如果初始下載失敗，我們不想要重新開機應用程式，因為應用程式可以重試下載檔案數次。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-267">We don't want the app to be restarted if the initial download fails because the app can retry downloading the file several times.</span></span> <span data-ttu-id="0c0bc-268">我們會使用 *活動探查* 來描述進程的活動，而不會執行其他檢查。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-268">We use a *liveness probe* to describe the liveness of the process, no additional checks are performed.</span></span> <span data-ttu-id="0c0bc-269">我們也想要避免在設定檔下載成功之前將要求傳送至應用程式。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-269">We also want to prevent requests from being sent to the app before the configuration file download has succeeded.</span></span> <span data-ttu-id="0c0bc-270">我們會使用 *就緒探查* 來表示「未就緒」狀態，直到下載成功，而且應用程式已準備好接收要求。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-270">We use a *readiness probe* to indicate a "not ready" state until the download succeeds and the app is ready to receive requests.</span></span>

<span data-ttu-id="0c0bc-271">範例應用程式包含健康狀態檢查，會在[託管服務](xref:fundamentals/host/hosted-services)中長時間執行的啟動工作完成時回報。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-271">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="0c0bc-272">`StartupHostedServiceHealthCheck` 會公開屬性 `StartupTaskCompleted`。託管服務可在其長時間執行的工作完成時，將此屬性設定為 `true` (*StartupHostedServiceHealthCheck.cs*)：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-272">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="0c0bc-273">[託管服務](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*) 會啟動長時間執行的背景工作。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-273">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="0c0bc-274">工作完成時，`StartupHostedServiceHealthCheck.StartupTaskCompleted` 會設定為 `true`：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-274">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="0c0bc-275">健康狀態檢查是 `Startup.ConfigureServices` 中使用 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> 隨託管服務一起登錄。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-275">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="0c0bc-276">由於託管服務必須在健康狀態檢查上設定此屬性，因此也會在服務容器 (*LivenessProbeStartup.cs*) 中登錄健康狀態檢查：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-276">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="0c0bc-277">健康情況檢查端點是藉由在中呼叫來建立 `MapHealthChecks` `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-277">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="0c0bc-278">在範例應用程式中，健康狀態檢查端點是在下列位置建立：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-278">In the sample app, the health check endpoints are created at:</span></span>

* <span data-ttu-id="0c0bc-279">`/health/ready` 適用于準備檢查。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-279">`/health/ready` for the readiness check.</span></span> <span data-ttu-id="0c0bc-280">整備度檢查使用 `ready` 標籤來篩選健康狀態檢查。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-280">The readiness check filters health checks to the health check with the `ready` tag.</span></span>
* <span data-ttu-id="0c0bc-281">`/health/live` 適用于活動檢查。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-281">`/health/live` for the liveness check.</span></span> <span data-ttu-id="0c0bc-282">活動檢查會藉 `StartupHostedServiceHealthCheck` 由 `false` 在 [HealthCheckOptions](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) 中傳回以篩選出， (如需詳細資訊，請參閱 [篩選健康情況檢查](#filter-health-checks)) </span><span class="sxs-lookup"><span data-stu-id="0c0bc-282">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks))</span></span>

<span data-ttu-id="0c0bc-283">在下列範例程式碼中：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-283">In the following example code:</span></span>

* <span data-ttu-id="0c0bc-284">準備就緒檢查會將所有已註冊的檢查都使用「就緒」標記。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-284">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="0c0bc-285">會 `Predicate` 排除所有檢查並傳回 200-確定。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-285">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

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

<span data-ttu-id="0c0bc-286">若要使用範例應用程式執行整備度/活躍度組態案例，請在命令殼層中執行來自專案資料夾的下列命令：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-286">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="0c0bc-287">在瀏覽器中，瀏覽 `/health/ready` 數次，直到經過 15 秒。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-287">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="0c0bc-288">健康狀態檢查在前 15 秒會回報 *Unhealthy*。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-288">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="0c0bc-289">15 秒之後，端點會回報 *Healthy*，這反映託管服務長時間執行的工作已完成。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-289">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="0c0bc-290">此範例也會建立一個以 2 秒延遲執行第一次整備檢查的「健康狀態檢查發行者」(<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 實作)。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-290">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="0c0bc-291">如需詳細資訊，請參閱[健康狀態檢查發行者](#health-check-publisher)一節。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-291">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="0c0bc-292">Kubernetes 範例</span><span class="sxs-lookup"><span data-stu-id="0c0bc-292">Kubernetes example</span></span>

<span data-ttu-id="0c0bc-293">使用個別的整備度與活躍度檢查在 [Kubernetes](https://kubernetes.io/) 之類的環境中很有用。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-293">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="0c0bc-294">在 Kubernetes 中，應用程式可能需要執行耗時的啟動工作才能接受要求 (例如基礎資料庫可用性測試)。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-294">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="0c0bc-295">使用個別的檢查可讓協調器區分應用程式是否為正常運作但尚未準備好，或應用程式是否無法啟動。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-295">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="0c0bc-296">如需 Kubernetes 中整備度與活躍度探查的詳細資訊，請參閱 Kubernetes 文件中的 [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) (設定活躍度與整備度探查)。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-296">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="0c0bc-297">下列範例示範 Kubernetes 整備度探查組態：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-297">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

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

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="0c0bc-298">透過自訂回應寫入器的計量型探查</span><span class="sxs-lookup"><span data-stu-id="0c0bc-298">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="0c0bc-299">範例應用程式示範透過自訂回應寫入器的記憶體健康狀態檢查。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-299">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="0c0bc-300">如果應用程式使用超過指定的記憶體閾值 (在範例應用程式中為 1 GB)，`MemoryHealthCheck` 會報告降級的狀態。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-300">`MemoryHealthCheck` reports a degraded status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="0c0bc-301"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> 包含應用程式的記憶體回收行程 (GC) 資訊 (*MemoryHealthCheck.cs*)：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-301">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="0c0bc-302">在 `Startup.ConfigureServices` 中，使用 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> 登錄健康狀態檢查服務。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-302">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0c0bc-303">`MemoryHealthCheck` 會登錄為服務，而不是將健康狀態檢查傳遞至 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> 以啟用檢查。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-303">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="0c0bc-304">所有 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 登錄的服務都可供健康狀態檢查服務和中介軟體使用。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-304">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="0c0bc-305">建議將健康狀態檢查服務登錄為單一服務。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-305">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="0c0bc-306">在範例應用程式的 *CustomWriterStartup.cs* 中：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-306">In *CustomWriterStartup.cs* of the sample app:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="0c0bc-307">健康情況檢查端點是藉由在中呼叫來建立 `MapHealthChecks` `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-307">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="0c0bc-308">`WriteResponse`當健康狀態檢查執行時，會將委派提供給 <的 AspNetCore. HealthChecks. HealthCheckOptions. ResponseWriter> 屬性以輸出自訂 JSON 回應：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-308">A `WriteResponse` delegate is provided to the <Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> property to output a custom JSON response when the health check executes:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
}
```

<span data-ttu-id="0c0bc-309">委派會將 `WriteResponse` 格式化為 `CompositeHealthCheckResult` json 物件，並產生健康情況檢查回應的 json 輸出。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-309">The `WriteResponse` delegate formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response.</span></span> <span data-ttu-id="0c0bc-310">如需詳細資訊，請參閱 [自訂輸出](#customize-output) 一節。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-310">For more information, see the [Customize output](#customize-output) section.</span></span>

<span data-ttu-id="0c0bc-311">若要使用範例應用程式透過自訂回應寫入器輸出執行計量型探查，請在命令殼層中執行來自專案資料夾的下列命令：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-311">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="0c0bc-312">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) \(英文\) 隨附計量型健康情況檢查案例，包括磁碟儲存體和最大值活躍度檢查。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-312">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="0c0bc-313">[AspNetCore](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 不會由 Microsoft 維護或支援 HealthChecks。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-313">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="0c0bc-314">依連接埠篩選</span><span class="sxs-lookup"><span data-stu-id="0c0bc-314">Filter by port</span></span>

<span data-ttu-id="0c0bc-315">`RequireHost` `MapHealthChecks` 使用指定埠的 URL 模式呼叫，以將健康情況檢查要求限制為指定的埠。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-315">Call `RequireHost` on `MapHealthChecks` with a URL pattern that specifies a port to restrict health check requests to the port specified.</span></span> <span data-ttu-id="0c0bc-316">這通常會用於容器環境，以公開監視服務的連接埠。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-316">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="0c0bc-317">範例應用程式使用[環境變數組態提供者](xref:fundamentals/configuration/index#environment-variables)來設定連接埠。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-317">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables).</span></span> <span data-ttu-id="0c0bc-318">連接埠是在 *launchSettings.json* 檔案中設定，並透過環境變數傳遞至組態提供者。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-318">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="0c0bc-319">您也必須將伺服器設定為在管理連接埠上接聽要求。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-319">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="0c0bc-320">若要使用範例應用程式示範管理連接埠組態，請在 *Properties* 資料夾中建立 *launchSettings.json* 檔案。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-320">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="0c0bc-321">範例應用程式中檔案的下列 *屬性/launchSettings.js* 不包含在範例應用程式的專案檔中，必須手動建立：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-321">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

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

<span data-ttu-id="0c0bc-322">在 `Startup.ConfigureServices` 中，使用 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> 登錄健康狀態檢查服務。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-322">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0c0bc-323">藉由在中呼叫來建立健康情況檢查端點 `MapHealthChecks` `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-323">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="0c0bc-324">在範例應用程式中，對 `RequireHost` 中端點的呼叫會 `Startup.Configure` 從設定中指定管理埠：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-324">In the sample app, a call to `RequireHost` on the endpoint in `Startup.Configure` specifies the management port from configuration:</span></span>

```csharp
endpoints.MapHealthChecks("/health")
    .RequireHost($"*:{Configuration["ManagementPort"]}");
```

<span data-ttu-id="0c0bc-325">端點是在的範例應用程式中建立的 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-325">Endpoints are created in the sample app in `Startup.Configure`.</span></span> <span data-ttu-id="0c0bc-326">在下列範例程式碼中：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-326">In the following example code:</span></span>

* <span data-ttu-id="0c0bc-327">準備就緒檢查會將所有已註冊的檢查都使用「就緒」標記。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-327">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="0c0bc-328">會 `Predicate` 排除所有檢查並傳回 200-確定。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-328">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

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
> <span data-ttu-id="0c0bc-329">您可以在程式碼中明確設定管理埠，以避免在範例應用程式中建立檔案 *launchSettings.js* 。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-329">You can avoid creating the *launchSettings.json* file in the sample app by setting the management port explicitly in code.</span></span> <span data-ttu-id="0c0bc-330">在建立的 *Program.cs* 中 <xref:Microsoft.Extensions.Hosting.HostBuilder> ，新增對的呼叫， <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> 並提供應用程式的管理埠端點。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-330">In *Program.cs* where the <xref:Microsoft.Extensions.Hosting.HostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> and provide the app's management port endpoint.</span></span> <span data-ttu-id="0c0bc-331">在 `Configure` *ManagementPortStartup.cs* 中，使用下列內容指定管理埠 `RequireHost` ：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-331">In `Configure` of *ManagementPortStartup.cs*, specify the management port with `RequireHost`:</span></span>
>
> <span data-ttu-id="0c0bc-332">*Program.cs*：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-332">*Program.cs*:</span></span>
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
> <span data-ttu-id="0c0bc-333">*ManagementPortStartup.cs*：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-333">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseEndpoints(endpoints =>
> {
>     endpoints.MapHealthChecks("/health").RequireHost("*:5001");
> });
> ```

<span data-ttu-id="0c0bc-334">若要使用範例應用程式執行管理連接埠組態案例，請在命令殼層中執行來自專案資料夾的下列命令：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-334">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="0c0bc-335">發佈健康狀態檢查程式庫</span><span class="sxs-lookup"><span data-stu-id="0c0bc-335">Distribute a health check library</span></span>

<span data-ttu-id="0c0bc-336">若要發佈健康狀態檢查作為程式庫：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-336">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="0c0bc-337">寫入健康狀態檢查，將 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 介面當做獨立類別來實作。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-337">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="0c0bc-338">此類別可能依賴[相依性插入 (DI)](xref:fundamentals/dependency-injection)、類型啟用和[具名選項](xref:fundamentals/configuration/options)來存取組態資料。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-338">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="0c0bc-339">在健康狀態檢查邏輯中 `CheckHealthAsync` ：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-339">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="0c0bc-340">`data1` 和 `data2` 在方法中用來執行探查的健康情況檢查邏輯。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-340">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="0c0bc-341">`AccessViolationException` 會進行處理。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-341">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="0c0bc-342">當 <xref:System.AccessViolationException> 發生時， <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> 會傳回， <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> 以允許使用者設定健康情況檢查失敗狀態。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-342">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

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

1. <span data-ttu-id="0c0bc-343">使用取用應用程式在其 `Startup.Configure` 方法中呼叫的參數，來寫入延伸模組。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-343">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="0c0bc-344">在下列範例中，假設健康狀態檢查方法簽章如下：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-344">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="0c0bc-345">上述簽章指出 `ExampleHealthCheck` 需要額外的資料，才能處理健康狀態檢查探查邏輯。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-345">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="0c0bc-346">此資料會提供給委派，以在使用延伸模組登錄健康狀態檢查時，用來建立健康狀態檢查執行個體。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-346">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="0c0bc-347">在下列範例中，呼叫者會選擇性地指定：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-347">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="0c0bc-348">健康狀態檢查名稱 (`name`)。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-348">health check name (`name`).</span></span> <span data-ttu-id="0c0bc-349">如果為 `null`，則會使用 `example_health_check`。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-349">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="0c0bc-350">健康狀態檢查的字串資料點 (`data1`)。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-350">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="0c0bc-351">健康狀態檢查的整數資料點 (`data2`)。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-351">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="0c0bc-352">如果為 `null`，則會使用 `1`。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-352">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="0c0bc-353">失敗狀態 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>)。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-353">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="0c0bc-354">預設為 `null`。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-354">The default is `null`.</span></span> <span data-ttu-id="0c0bc-355">如果為 `null`，就會針對失敗狀態回報 [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-355">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="0c0bc-356">標籤 (`IEnumerable<string>`)。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-356">tags (`IEnumerable<string>`).</span></span>

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

## <a name="health-check-publisher"></a><span data-ttu-id="0c0bc-357">健康狀態檢查發行者</span><span class="sxs-lookup"><span data-stu-id="0c0bc-357">Health Check Publisher</span></span>

<span data-ttu-id="0c0bc-358">當 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 新增至服務容器時，健康狀態檢查系統會定期執行健康狀態檢查，並呼叫 `PublishAsync` 傳回結果。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-358">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="0c0bc-359">這對推送型健康狀態監控系統案例很有用，其預期每個處理序會定期呼叫監控系統來判斷健康狀態。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-359">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="0c0bc-360"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 介面有單一方法：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-360">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="0c0bc-361"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> 可讓您設定：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-361"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="0c0bc-362"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>：在執行實例之前，應用程式啟動後所套用的初始延遲 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-362"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="0c0bc-363">在啟動後就會套用延遲，但不會套用至後續的反覆項目。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-363">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="0c0bc-364">預設值是五秒鐘。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-364">The default value is five seconds.</span></span>
* <span data-ttu-id="0c0bc-365"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>：執行的期間 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-365"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="0c0bc-366">預設值為 30 秒。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-366">The default value is 30 seconds.</span></span>
* <span data-ttu-id="0c0bc-367"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>：如果 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> `null` (預設) ，健全狀況檢查發行者服務會執行所有已註冊的健康情況檢查。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-367"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="0c0bc-368">若要執行一部分的健康狀態檢查，請提供可篩選該組檢查的函式。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-368">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="0c0bc-369">每個期間都會評估該述詞。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-369">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="0c0bc-370"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>：針對所有實例執行健康情況檢查的超時時間 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-370"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="0c0bc-371">若要在沒有逾時的情況下執行，請使用 <xref:System.Threading.Timeout.InfiniteTimeSpan>。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-371">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="0c0bc-372">預設值為 30 秒。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-372">The default value is 30 seconds.</span></span>

<span data-ttu-id="0c0bc-373">在範例應用程式中，`ReadinessPublisher` 是一個 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 實作。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-373">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="0c0bc-374">記錄層級的每個檢查都會記錄健全狀況檢查狀態：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-374">The health check status is logged for each check at a log level of:</span></span>

* <span data-ttu-id="0c0bc-375"><xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>如果健康狀態檢查狀態為， () 資訊 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy> 。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-375">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="0c0bc-376"><xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>當狀態為或時，)  (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> 錯誤 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy> 。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-376">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="0c0bc-377">在範例應用程式的 `LivenessProbeStartup` 範例中，`StartupHostedService` 整備檢查具有 2 秒的啟動延遲，且每 30 秒會執行一次檢查。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-377">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="0c0bc-378">為了啟用 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 實作，此範例會在[相依性插入 (DI)](xref:fundamentals/dependency-injection) 容器中將 `ReadinessPublisher` 註冊為單一服務：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-378">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

> [!NOTE]
> <span data-ttu-id="0c0bc-379">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) \(英文\) 隨附數個系統的發行者，包括 [Application Insights](/azure/application-insights/app-insights-overview)。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-379">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="0c0bc-380">[AspNetCore](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 不會由 Microsoft 維護或支援 HealthChecks。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-380">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="0c0bc-381">利用 MapWhen 限制健康情況檢查</span><span class="sxs-lookup"><span data-stu-id="0c0bc-381">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="0c0bc-382">使用 <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> 來有條件地將健康情況檢查端點的要求管線分支。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-382">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="0c0bc-383">在下列範例中， `MapWhen` 如果收到端點的 GET 要求，則會將要求管線分支以啟用健康情況檢查中介軟體 `api/HealthCheck` ：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-383">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

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

<span data-ttu-id="0c0bc-384">如需詳細資訊，請參閱<xref:fundamentals/middleware/index#branch-the-middleware-pipeline>。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-384">For more information, see <xref:fundamentals/middleware/index#branch-the-middleware-pipeline>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0c0bc-385">ASP.NET Core 提供健康狀態檢查中介軟體和程式庫，以報告應用程式基礎結構元件的健康情況。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-385">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="0c0bc-386">應用程式會將健康狀態檢查公開為 HTTP 端點。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-386">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="0c0bc-387">您可以針對各種即時監控案例來設定健康狀態檢查端點：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-387">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="0c0bc-388">容器協調器和負載平衡器可以使用健康狀態探查，來檢查應用程式的狀態。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-388">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="0c0bc-389">例如，容器協調器可能會暫停輪流部署或重新啟動容器，來回應失敗的健康狀態檢查。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-389">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="0c0bc-390">負載平衡器可能會將流量從失敗的執行個體路由傳送至狀況良好的執行個體，來回應狀況不良的應用程式。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-390">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="0c0bc-391">您可以監控所使用記憶體、磁碟及其他實體伺服器資源的健康狀態。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-391">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="0c0bc-392">健康狀態檢查可以測試應用程式的相依性 (例如資料庫和外部服務端點)，確認其是否可用且正常運作。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-392">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="0c0bc-393">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/health-checks/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="0c0bc-393">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0c0bc-394">範例應用程式包含本主題中所述的案例範例。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-394">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="0c0bc-395">若要在指定的案例中執行範例應用程式，請在命令殼層中使用來自專案資料夾的 [dotnet run](/dotnet/core/tools/dotnet-run) 命令。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-395">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="0c0bc-396">如需如何使用範例應用程式的詳細資訊，請參閱範例應用程式的 *README.md* 檔案和本主題中的案例描述。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-396">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0c0bc-397">必要條件</span><span class="sxs-lookup"><span data-stu-id="0c0bc-397">Prerequisites</span></span>

<span data-ttu-id="0c0bc-398">健康狀態檢查通常會搭配使用外部監視服務或容器協調器，來檢查應用程式的狀態。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-398">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="0c0bc-399">將健康狀態檢查新增至應用程式之前，請決定要使用的監控系統。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-399">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="0c0bc-400">監控系統會指定要建立哪些健康狀態檢查類型，以及如何設定其端點。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-400">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="0c0bc-401">參考 [Microsoft.AspNetCore.App 中繼套件](xref:fundamentals/metapackage-app)，或新增 [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) 套件的套件參考。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-401">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package.</span></span>

<span data-ttu-id="0c0bc-402">範例應用程式提供啟動程式碼，來示範數個案例的健康狀態檢查。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-402">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="0c0bc-403">[資料庫探查](#database-probe)案例會使用 [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) \(英文\) 來檢查資料庫連線的健康情況。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-403">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="0c0bc-404">[DbContext 探查](#entity-framework-core-dbcontext-probe)案例使用 EF Core `DbContext` 來檢查資料庫。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-404">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="0c0bc-405">為了探索資料庫案例，範例應用程式會：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-405">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="0c0bc-406">建立資料庫，並在檔案中提供其連接字串 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-406">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="0c0bc-407">在其專案檔中具有下列套件參考：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-407">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="0c0bc-408">AspNetCore.HealthChecks.SqlServer</span><span class="sxs-lookup"><span data-stu-id="0c0bc-408">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="0c0bc-409">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="0c0bc-409">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="0c0bc-410">[AspNetCore](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 不會由 Microsoft 維護或支援 HealthChecks。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-410">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="0c0bc-411">另一個健康狀態檢查案例示範如何將健康狀態檢查篩選至管理連接埠。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-411">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="0c0bc-412">範例應用程式會要求您建立 *Properties/launchSettings.json* 檔案，其中包含管理 URL 和管理連接埠。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-412">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="0c0bc-413">如需詳細資訊，請參閱[依連接埠篩選](#filter-by-port)一節。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-413">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="0c0bc-414">基本健康狀態探查</span><span class="sxs-lookup"><span data-stu-id="0c0bc-414">Basic health probe</span></span>

<span data-ttu-id="0c0bc-415">對於許多應用程式，報告應用程式是否可處理要求的基本健康狀態探查組態 (「活躍度」)，便足以探索應用程式的狀態。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-415">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="0c0bc-416">基本設定會註冊健康情況檢查服務並呼叫健康情況檢查中介軟體，以在具有健康回應的 URL 端點回應。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-416">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="0c0bc-417">預設並未登錄特定健康狀態檢查來測試任何特定相依性或子系統。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-417">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="0c0bc-418">如果應用程式能夠在健康狀態端點 URL 做出回應，則視為狀況良好。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-418">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="0c0bc-419">預設回應寫入器會將狀態 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) 以純文字回應形式回寫到用戶端，指出狀態為 [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)[HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) 或 [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-419">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="0c0bc-420">在 `Startup.ConfigureServices` 中，使用 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> 登錄健康狀態檢查服務。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-420">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0c0bc-421"><xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>在的要求處理管線中新增健康狀態檢查中介軟體的端點 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-421">Add an endpoint for Health Checks Middleware with <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the request processing pipeline of `Startup.Configure`.</span></span>

<span data-ttu-id="0c0bc-422">在範例應用程式中，健康狀態檢查端點是在 `/health` (*BasicStartup.cs*) 建立：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-422">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

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

<span data-ttu-id="0c0bc-423">若要使用範例應用程式執行基本組態案例，請在命令殼層中執行來自專案資料夾的下列命令：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-423">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="0c0bc-424">Docker 範例</span><span class="sxs-lookup"><span data-stu-id="0c0bc-424">Docker example</span></span>

<span data-ttu-id="0c0bc-425">[Docker](xref:host-and-deploy/docker/index) 提供內建 `HEALTHCHECK` 指示詞，可用來檢查使用基本健康狀態檢查組態的應用程式狀態：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-425">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="0c0bc-426">建立健康狀態檢查</span><span class="sxs-lookup"><span data-stu-id="0c0bc-426">Create health checks</span></span>

<span data-ttu-id="0c0bc-427">健康狀態檢查是藉由實作 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 介面來建立。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-427">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="0c0bc-428"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> 方法會傳回 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>，指出健康狀態為 `Healthy`、`Degraded` 或 `Unhealthy`。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-428">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="0c0bc-429">結果會寫成具有可設定狀態碼的純文字回應 ([健康狀態檢查選項](#health-check-options)一節中將說明如何進行組態)。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-429">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="0c0bc-430"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> 也可以傳回選擇性索引鍵/值組。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-430"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

### <a name="example-health-check"></a><span data-ttu-id="0c0bc-431">範例健康狀態檢查</span><span class="sxs-lookup"><span data-stu-id="0c0bc-431">Example health check</span></span>

<span data-ttu-id="0c0bc-432">下列 `ExampleHealthCheck` 類別示範健康情況檢查的版面配置。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-432">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="0c0bc-433">健康情況檢查邏輯會放置在 `CheckHealthAsync` 方法中。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-433">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="0c0bc-434">下列範例會將虛擬變數（）設定 `healthCheckResultHealthy` 為 `true` 。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-434">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="0c0bc-435">如果的值 `healthCheckResultHealthy` 設定為 `false` ，則會傳回 [HealthCheckResult 狀況不良](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) 狀態。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-435">If the value of `healthCheckResultHealthy` is set to `false`, the [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status is returned.</span></span>

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

### <a name="register-health-check-services"></a><span data-ttu-id="0c0bc-436">登錄健康狀態檢查服務</span><span class="sxs-lookup"><span data-stu-id="0c0bc-436">Register health check services</span></span>

<span data-ttu-id="0c0bc-437">此 `ExampleHealthCheck` 類型會新增至中的健康情況檢查服務 `Startup.ConfigureServices` <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> ：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-437">The `ExampleHealthCheck` type is added to health check services in `Startup.ConfigureServices` with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="0c0bc-438">下列範例中所顯示的 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> 多載會設定在健康狀態檢查報告失敗時所要報告的失敗狀態 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>)。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-438">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="0c0bc-439">如果將失敗狀態設定為 `null` (預設)，則會回報 [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-439">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="0c0bc-440">此多載對程式庫作者很有用。若健康狀態檢查實作採用此設定，則當健康狀態檢查失敗時，應用程式就會強制程式庫指出失敗狀態。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-440">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="0c0bc-441">您可以使用「標籤」來篩選健康狀態檢查 ([篩選健康狀態檢查](#filter-health-checks)一節中將進一步說明)。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-441">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="0c0bc-442"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> 也可以執行匿名函式。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-442"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="0c0bc-443">在下列 `Startup.ConfigureServices` 範例中，健康狀態檢查名稱會指定為 `Example` ，且檢查一律會傳回狀況良好的狀態：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-443">In the following `Startup.ConfigureServices` example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

### <a name="use-health-checks-middleware"></a><span data-ttu-id="0c0bc-444">使用健康狀態檢查中介軟體</span><span class="sxs-lookup"><span data-stu-id="0c0bc-444">Use Health Checks Middleware</span></span>

<span data-ttu-id="0c0bc-445">在 `Startup.Configure` 中，使用端點 URL 或相對路徑呼叫處理管線中的 <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-445">In `Startup.Configure`, call <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the processing pipeline with the endpoint URL or relative path:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="0c0bc-446">如果健康狀態檢查應該接聽特定連接埠，請使用 <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> 的多載來設定連接埠 ([依連接埠篩選](#filter-by-port)一節中將進一步說明)：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-446">If the health checks should listen on a specific port, use an overload of <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> to set the port (described further in the [Filter by port](#filter-by-port) section):</span></span>

```csharp
app.UseHealthChecks("/health", port: 8000);
```

## <a name="health-check-options"></a><span data-ttu-id="0c0bc-447">健康狀態檢查選項</span><span class="sxs-lookup"><span data-stu-id="0c0bc-447">Health check options</span></span>

<span data-ttu-id="0c0bc-448"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> 讓您有機會自訂健康狀態檢查行為：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-448"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="0c0bc-449">篩選健康狀態檢查</span><span class="sxs-lookup"><span data-stu-id="0c0bc-449">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="0c0bc-450">自訂 HTTP 狀態碼</span><span class="sxs-lookup"><span data-stu-id="0c0bc-450">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="0c0bc-451">隱藏快取標頭</span><span class="sxs-lookup"><span data-stu-id="0c0bc-451">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="0c0bc-452">自訂輸出</span><span class="sxs-lookup"><span data-stu-id="0c0bc-452">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="0c0bc-453">篩選健康狀態檢查</span><span class="sxs-lookup"><span data-stu-id="0c0bc-453">Filter health checks</span></span>

<span data-ttu-id="0c0bc-454">依預設，健康狀態檢查中介軟體會執行所有已註冊的健康情況檢查。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-454">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="0c0bc-455">若要執行健康狀態檢查子集，請對 <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> 選項提供傳回布林值的函式。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-455">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="0c0bc-456">在下列範例中，會依函式條件陳述式中的標籤 (`bar_tag`) 篩選出 `Bar` 健康狀態檢查。只有在健康狀態檢查的 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> 屬性符合 `foo_tag` 或 `baz_tag` 時，才傳回 `true`：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-456">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

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

### <a name="customize-the-http-status-code"></a><span data-ttu-id="0c0bc-457">自訂 HTTP 狀態碼</span><span class="sxs-lookup"><span data-stu-id="0c0bc-457">Customize the HTTP status code</span></span>

<span data-ttu-id="0c0bc-458">您可以使用 <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> 來自訂健康狀態與 HTTP 狀態碼的對應。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-458">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="0c0bc-459">下列 <xref:Microsoft.AspNetCore.Http.StatusCodes> 指派是中介軟體所使用的預設值。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-459">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="0c0bc-460">請變更狀態碼值以符合您的需求。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-460">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="0c0bc-461">在 `Startup.Configure` 中：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-461">In `Startup.Configure`:</span></span>

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

### <a name="suppress-cache-headers"></a><span data-ttu-id="0c0bc-462">隱藏快取標頭</span><span class="sxs-lookup"><span data-stu-id="0c0bc-462">Suppress cache headers</span></span>

<span data-ttu-id="0c0bc-463"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> 控制健全狀況檢查中介軟體是否會將 HTTP 標頭新增至探查回應，以防止回應快取。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-463"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="0c0bc-464">如果值為 `false` (預設)，則中介軟體會設定或覆寫 `Cache-Control`、`Expires` 和 `Pragma` 標頭，以防止回應快取。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-464">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="0c0bc-465">如果值為 `true`，則中介軟體不會修改回應的快取標頭。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-465">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="0c0bc-466">在 `Startup.Configure` 中：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-466">In `Startup.Configure`:</span></span>

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    AllowCachingResponses = false
});
```

### <a name="customize-output"></a><span data-ttu-id="0c0bc-467">自訂輸出</span><span class="sxs-lookup"><span data-stu-id="0c0bc-467">Customize output</span></span>

<span data-ttu-id="0c0bc-468"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> 選項會取得或設定用來寫入回應的委派。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-468">The <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> option gets or sets a delegate used to write the response.</span></span> <span data-ttu-id="0c0bc-469">預設委派會使用 [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status)的字串值撰寫最基本的純文字回應。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-469">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span>

<span data-ttu-id="0c0bc-470">在 `Startup.Configure` 中：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-470">In `Startup.Configure`:</span></span>

```csharp
// using Microsoft.AspNetCore.Diagnostics.HealthChecks;
// using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResponseWriter = WriteResponse
});
```

<span data-ttu-id="0c0bc-471">預設委派會使用 [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status)的字串值撰寫最基本的純文字回應。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-471">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="0c0bc-472">下列自訂委派會 `WriteResponse` 輸出自訂 JSON 回應：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-472">The following custom delegate, `WriteResponse`, outputs a custom JSON response:</span></span>

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

<span data-ttu-id="0c0bc-473">健康情況檢查系統不會提供複雜 JSON 傳回格式的內建支援，因為格式是您選擇的監視系統所特有。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-473">The health checks system doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="0c0bc-474">`JObject`您可以視需要自訂上述範例中的，以符合您的需求。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-474">Feel free to customize the `JObject` in the preceding example as necessary to meet your needs.</span></span>

## <a name="database-probe"></a><span data-ttu-id="0c0bc-475">資料庫探查</span><span class="sxs-lookup"><span data-stu-id="0c0bc-475">Database probe</span></span>

<span data-ttu-id="0c0bc-476">健康狀態檢查可指定資料庫查詢以布林測試方式來執行，藉此指出資料庫是否正常回應。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-476">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="0c0bc-477">範例應用程式會使用 [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) \(英文\) (此為適用於 ASP.NET Core 應用程式的健康情況檢查程式庫)，在 SQL Server 資料庫上執行健康情況檢查。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-477">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="0c0bc-478">`AspNetCore.Diagnostics.HealthChecks` 會對資料庫執行 `SELECT 1` 查詢，以確認資料庫連線狀況良好。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-478">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="0c0bc-479">使用查詢檢查資料庫連線時，請選擇快速傳回的查詢。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-479">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="0c0bc-480">查詢方法具有多載資料庫而降低其效能的風險。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-480">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="0c0bc-481">在大部分情況下，不需要執行測試查詢。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-481">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="0c0bc-482">只要成功建立資料庫連線就已足夠。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-482">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="0c0bc-483">如果您發現有必要執行查詢，請選擇簡單的 SELECT 查詢，例如 `SELECT 1`。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-483">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="0c0bc-484">包含 [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/) 的套件參考。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-484">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="0c0bc-485">在範例應用程式的檔案中提供有效的資料庫連接字串 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-485">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="0c0bc-486">應用程式使用名為 `HealthCheckSample` 的 SQL Server 資料庫：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-486">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/2.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="0c0bc-487">在 `Startup.ConfigureServices` 中，使用 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> 登錄健康狀態檢查服務。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-487">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0c0bc-488">範例應用程式會使用資料庫的連接字串 (*DbHealthStartup.cs*) 來呼叫 `AddSqlServer` 方法：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-488">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="0c0bc-489">呼叫健康情況檢查應用程式處理管線中的中介軟體 `Startup.Configure` ：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-489">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="0c0bc-490">若要使用範例應用程式執行資料庫探查案例，請在命令殼層中執行來自專案資料夾的下列命令：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-490">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="0c0bc-491">[AspNetCore](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 不會由 Microsoft 維護或支援 HealthChecks。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-491">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="0c0bc-492">Entity Framework Core DbContext 探查</span><span class="sxs-lookup"><span data-stu-id="0c0bc-492">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="0c0bc-493">`DbContext` 檢查會確認應用程式是否可與針對 EF Core `DbContext` 設定的資料庫通訊。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-493">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="0c0bc-494">應用程式支援 `DbContext` 檢查：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-494">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="0c0bc-495">使用 [Entity Framework (EF) Core](/ef/core/)。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-495">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="0c0bc-496">包含 [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/) 的套件參考。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-496">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="0c0bc-497">`AddDbContextCheck<TContext>` 會登錄 `DbContext` 的健康狀態檢查。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-497">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="0c0bc-498">`DbContext` 會以 `TContext` 形式提供給方法。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-498">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="0c0bc-499">多載可用來設定失敗狀態、標籤和自訂測試查詢。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-499">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="0c0bc-500">依照預設：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-500">By default:</span></span>

* <span data-ttu-id="0c0bc-501">`DbContextHealthCheck` 會呼叫 EF Core 的 `CanConnectAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-501">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="0c0bc-502">您可以自訂使用 `AddDbContextCheck` 方法多載檢查健康狀態時所要執行的作業。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-502">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="0c0bc-503">健康狀態檢查的名稱是 `TContext` 類型的名稱。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-503">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="0c0bc-504">在範例應用程式中， `AppDbContext` 會 `AddDbContextCheck` 在 `Startup.ConfigureServices` (*DbCoNtextHealthStartup.cs*) 中提供並註冊為服務：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-504">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="0c0bc-505">在範例應用程式中， `UseHealthChecks` 新增中的健康情況檢查中介軟體 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-505">In the sample app, `UseHealthChecks` adds the Health Checks Middleware in `Startup.Configure`.</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="0c0bc-506">若要使用範例應用程式來執行 `DbContext` 探查案例，請確認 SQL Server 執行個體中沒有連接字串所指定的資料庫。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-506">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="0c0bc-507">如果資料庫存在，請予以刪除。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-507">If the database exists, delete it.</span></span>

<span data-ttu-id="0c0bc-508">在命令殼層中執行來自專案資料夾的下列命令：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-508">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="0c0bc-509">在應用程式執行之後，對瀏覽器中的 `/health` 端點提出要求來檢查健康狀態。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-509">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="0c0bc-510">資料庫和 `AppDbContext` 不存在，因此應用程式會提供下列回應：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-510">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="0c0bc-511">觸發範例應用程式以建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-511">Trigger the sample app to create the database.</span></span> <span data-ttu-id="0c0bc-512">對 `/createdatabase` 提出要求。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-512">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="0c0bc-513">應用程式會回應：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-513">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="0c0bc-514">對 `/health` 端點提出要求。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-514">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="0c0bc-515">資料庫和內容存在，因此應用程式會回應：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-515">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="0c0bc-516">觸發範例應用程式以刪除資料庫。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-516">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="0c0bc-517">對 `/deletedatabase` 提出要求。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-517">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="0c0bc-518">應用程式會回應：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-518">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="0c0bc-519">對 `/health` 端點提出要求。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-519">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="0c0bc-520">應用程式會提供狀況不良回應：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-520">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="0c0bc-521">個別的整備度與活躍度探查</span><span class="sxs-lookup"><span data-stu-id="0c0bc-521">Separate readiness and liveness probes</span></span>

<span data-ttu-id="0c0bc-522">在某些主控案例中，使用一組健康狀態檢查來區分兩個應用程式狀態：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-522">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="0c0bc-523">*準備就緒* 表示應用程式是否正常執行，但尚未準備好接收要求。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-523">*Readiness* indicates if the app is running normally but isn't ready to receive requests.</span></span>
* <span data-ttu-id="0c0bc-524">*活動* 指出應用程式是否已損毀且必須重新開機。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-524">*Liveness* indicates if an app has crashed and must be restarted.</span></span>

<span data-ttu-id="0c0bc-525">請考慮下列範例：應用程式必須先下載大型設定檔，才能開始處理要求。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-525">Consider the following example: An app must download a large configuration file before it's ready to process requests.</span></span> <span data-ttu-id="0c0bc-526">如果初始下載失敗，我們不想要重新開機應用程式，因為應用程式可以重試下載檔案數次。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-526">We don't want the app to be restarted if the initial download fails because the app can retry downloading the file several times.</span></span> <span data-ttu-id="0c0bc-527">我們會使用 *活動探查* 來描述進程的活動，而不會執行其他檢查。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-527">We use a *liveness probe* to describe the liveness of the process, no additional checks are performed.</span></span> <span data-ttu-id="0c0bc-528">我們也想要避免在設定檔下載成功之前將要求傳送至應用程式。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-528">We also want to prevent requests from being sent to the app before the configuration file download has succeeded.</span></span> <span data-ttu-id="0c0bc-529">我們會使用 *就緒探查* 來表示「未就緒」狀態，直到下載成功，而且應用程式已準備好接收要求。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-529">We use a *readiness probe* to indicate a "not ready" state until the download succeeds and the app is ready to receive requests.</span></span>

<span data-ttu-id="0c0bc-530">範例應用程式包含健康狀態檢查，會在[託管服務](xref:fundamentals/host/hosted-services)中長時間執行的啟動工作完成時回報。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-530">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="0c0bc-531">`StartupHostedServiceHealthCheck` 會公開屬性 `StartupTaskCompleted`。託管服務可在其長時間執行的工作完成時，將此屬性設定為 `true` (*StartupHostedServiceHealthCheck.cs*)：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-531">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="0c0bc-532">[託管服務](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*) 會啟動長時間執行的背景工作。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-532">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="0c0bc-533">工作完成時，`StartupHostedServiceHealthCheck.StartupTaskCompleted` 會設定為 `true`：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-533">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="0c0bc-534">健康狀態檢查是 `Startup.ConfigureServices` 中使用 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> 隨託管服務一起登錄。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-534">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="0c0bc-535">由於託管服務必須在健康狀態檢查上設定此屬性，因此也會在服務容器 (*LivenessProbeStartup.cs*) 中登錄健康狀態檢查：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-535">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="0c0bc-536">在的應用程式處理管線中呼叫健康情況檢查中介軟體 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-536">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="0c0bc-537">在範例應用程式中，健康狀態檢查端點是在 `/health/ready` (針對整備度檢查) 和 `/health/live` (針對活躍度檢查) 建立。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-537">In the sample app, the health check endpoints are created at `/health/ready` for the readiness check and `/health/live` for the liveness check.</span></span> <span data-ttu-id="0c0bc-538">整備度檢查使用 `ready` 標籤來篩選健康狀態檢查。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-538">The readiness check filters health checks to the health check with the `ready` tag.</span></span> <span data-ttu-id="0c0bc-539">活躍度檢查會藉由在 [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) 中傳回 `false` 來篩選掉 `StartupHostedServiceHealthCheck` (如需詳細資訊，請參閱[篩選健康狀態檢查](#filter-health-checks))：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-539">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks)):</span></span>

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

<span data-ttu-id="0c0bc-540">若要使用範例應用程式執行整備度/活躍度組態案例，請在命令殼層中執行來自專案資料夾的下列命令：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-540">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="0c0bc-541">在瀏覽器中，瀏覽 `/health/ready` 數次，直到經過 15 秒。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-541">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="0c0bc-542">健康狀態檢查在前 15 秒會回報 *Unhealthy*。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-542">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="0c0bc-543">15 秒之後，端點會回報 *Healthy*，這反映託管服務長時間執行的工作已完成。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-543">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="0c0bc-544">此範例也會建立一個以 2 秒延遲執行第一次整備檢查的「健康狀態檢查發行者」(<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 實作)。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-544">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="0c0bc-545">如需詳細資訊，請參閱[健康狀態檢查發行者](#health-check-publisher)一節。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-545">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="0c0bc-546">Kubernetes 範例</span><span class="sxs-lookup"><span data-stu-id="0c0bc-546">Kubernetes example</span></span>

<span data-ttu-id="0c0bc-547">使用個別的整備度與活躍度檢查在 [Kubernetes](https://kubernetes.io/) 之類的環境中很有用。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-547">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="0c0bc-548">在 Kubernetes 中，應用程式可能需要執行耗時的啟動工作才能接受要求 (例如基礎資料庫可用性測試)。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-548">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="0c0bc-549">使用個別的檢查可讓協調器區分應用程式是否為正常運作但尚未準備好，或應用程式是否無法啟動。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-549">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="0c0bc-550">如需 Kubernetes 中整備度與活躍度探查的詳細資訊，請參閱 Kubernetes 文件中的 [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) (設定活躍度與整備度探查)。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-550">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="0c0bc-551">下列範例示範 Kubernetes 整備度探查組態：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-551">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

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

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="0c0bc-552">透過自訂回應寫入器的計量型探查</span><span class="sxs-lookup"><span data-stu-id="0c0bc-552">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="0c0bc-553">範例應用程式示範透過自訂回應寫入器的記憶體健康狀態檢查。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-553">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="0c0bc-554">`MemoryHealthCheck` 如果應用程式在範例應用程式) 中使用超過指定的記憶體閾值 (1 GB，則報告狀況不良。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-554">`MemoryHealthCheck` reports an unhealthy status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="0c0bc-555"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> 包含應用程式的記憶體回收行程 (GC) 資訊 (*MemoryHealthCheck.cs*)：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-555">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="0c0bc-556">在 `Startup.ConfigureServices` 中，使用 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> 登錄健康狀態檢查服務。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-556">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0c0bc-557">`MemoryHealthCheck` 會登錄為服務，而不是將健康狀態檢查傳遞至 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> 以啟用檢查。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-557">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="0c0bc-558">所有 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 登錄的服務都可供健康狀態檢查服務和中介軟體使用。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-558">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="0c0bc-559">建議將健康狀態檢查服務登錄為單一服務。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-559">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="0c0bc-560">在範例應用程式中 (*CustomWriterStartup.cs*) ：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-560">In the sample app (*CustomWriterStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="0c0bc-561">在的應用程式處理管線中呼叫健康情況檢查中介軟體 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-561">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="0c0bc-562">當健康狀態檢查執行時，會將 `WriteResponse` 委派提供給 `ResponseWriter` 屬性以輸出自訂 JSON 回應：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-562">A `WriteResponse` delegate is provided to the `ResponseWriter` property to output a custom JSON response when the health check executes:</span></span>

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

<span data-ttu-id="0c0bc-563">`WriteResponse` 方法會將 `CompositeHealthCheckResult` 格式化為 JSON 物件，並產生 JSON 輸出作為健康狀態檢查回應：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-563">The `WriteResponse` method formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

<span data-ttu-id="0c0bc-564">若要使用範例應用程式透過自訂回應寫入器輸出執行計量型探查，請在命令殼層中執行來自專案資料夾的下列命令：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-564">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="0c0bc-565">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) \(英文\) 隨附計量型健康情況檢查案例，包括磁碟儲存體和最大值活躍度檢查。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-565">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="0c0bc-566">[AspNetCore](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 不會由 Microsoft 維護或支援 HealthChecks。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-566">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="0c0bc-567">依連接埠篩選</span><span class="sxs-lookup"><span data-stu-id="0c0bc-567">Filter by port</span></span>

<span data-ttu-id="0c0bc-568">呼叫 <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> 並提供連接埠會限制對指定的連接埠提出健康狀態檢查要求。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-568">Calling <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> with a port restricts health check requests to the port specified.</span></span> <span data-ttu-id="0c0bc-569">這通常會用於容器環境，以公開監視服務的連接埠。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-569">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="0c0bc-570">範例應用程式使用[環境變數組態提供者](xref:fundamentals/configuration/index#environment-variables-configuration-provider)來設定連接埠。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-570">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span> <span data-ttu-id="0c0bc-571">連接埠是在 *launchSettings.json* 檔案中設定，並透過環境變數傳遞至組態提供者。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-571">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="0c0bc-572">您也必須將伺服器設定為在管理連接埠上接聽要求。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-572">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="0c0bc-573">若要使用範例應用程式示範管理連接埠組態，請在 *Properties* 資料夾中建立 *launchSettings.json* 檔案。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-573">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="0c0bc-574">範例應用程式中檔案的下列 *屬性/launchSettings.js* 不包含在範例應用程式的專案檔中，必須手動建立：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-574">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

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

<span data-ttu-id="0c0bc-575">在 `Startup.ConfigureServices` 中，使用 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> 登錄健康狀態檢查服務。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-575">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0c0bc-576">呼叫 <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> 會指定管理連接埠 (*ManagementPortStartup.cs*)：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-576">The call to <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> specifies the management port (*ManagementPortStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ManagementPortStartup.cs?name=snippet1&highlight=17)]

> [!NOTE]
> <span data-ttu-id="0c0bc-577">您可以在程式碼中明確設定 URL 和管理連接埠，避免在範例應用程式中建立 *launchSettings.json* 檔案。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-577">You can avoid creating the *launchSettings.json* file in the sample app by setting the URLs and management port explicitly in code.</span></span> <span data-ttu-id="0c0bc-578">在 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> 建立所在的 *Program.cs* 中，新增 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> 呼叫並提供應用程式的正常回應端點和管理連接埠端點。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-578">In *Program.cs* where the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> and provide the app's normal response endpoint and the management port endpoint.</span></span> <span data-ttu-id="0c0bc-579">在 <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> 呼叫所在的 *ManagementPortStartup.cs* 中，明確指定管理連接埠。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-579">In *ManagementPortStartup.cs* where <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> is called, specify the management port explicitly.</span></span>
>
> <span data-ttu-id="0c0bc-580">*Program.cs*：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-580">*Program.cs*:</span></span>
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
> <span data-ttu-id="0c0bc-581">*ManagementPortStartup.cs*：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-581">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseHealthChecks("/health", port: 5001);
> ```

<span data-ttu-id="0c0bc-582">若要使用範例應用程式執行管理連接埠組態案例，請在命令殼層中執行來自專案資料夾的下列命令：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-582">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="0c0bc-583">發佈健康狀態檢查程式庫</span><span class="sxs-lookup"><span data-stu-id="0c0bc-583">Distribute a health check library</span></span>

<span data-ttu-id="0c0bc-584">若要發佈健康狀態檢查作為程式庫：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-584">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="0c0bc-585">寫入健康狀態檢查，將 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 介面當做獨立類別來實作。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-585">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="0c0bc-586">此類別可能依賴[相依性插入 (DI)](xref:fundamentals/dependency-injection)、類型啟用和[具名選項](xref:fundamentals/configuration/options)來存取組態資料。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-586">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="0c0bc-587">在健康狀態檢查邏輯中 `CheckHealthAsync` ：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-587">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="0c0bc-588">`data1` 和 `data2` 在方法中用來執行探查的健康情況檢查邏輯。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-588">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="0c0bc-589">`AccessViolationException` 會進行處理。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-589">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="0c0bc-590">當 <xref:System.AccessViolationException> 發生時， <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> 會傳回， <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> 以允許使用者設定健康情況檢查失敗狀態。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-590">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

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

1. <span data-ttu-id="0c0bc-591">使用取用應用程式在其 `Startup.Configure` 方法中呼叫的參數，來寫入延伸模組。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-591">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="0c0bc-592">在下列範例中，假設健康狀態檢查方法簽章如下：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-592">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="0c0bc-593">上述簽章指出 `ExampleHealthCheck` 需要額外的資料，才能處理健康狀態檢查探查邏輯。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-593">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="0c0bc-594">此資料會提供給委派，以在使用延伸模組登錄健康狀態檢查時，用來建立健康狀態檢查執行個體。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-594">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="0c0bc-595">在下列範例中，呼叫者會選擇性地指定：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-595">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="0c0bc-596">健康狀態檢查名稱 (`name`)。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-596">health check name (`name`).</span></span> <span data-ttu-id="0c0bc-597">如果為 `null`，則會使用 `example_health_check`。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-597">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="0c0bc-598">健康狀態檢查的字串資料點 (`data1`)。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-598">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="0c0bc-599">健康狀態檢查的整數資料點 (`data2`)。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-599">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="0c0bc-600">如果為 `null`，則會使用 `1`。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-600">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="0c0bc-601">失敗狀態 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>)。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-601">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="0c0bc-602">預設為 `null`。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-602">The default is `null`.</span></span> <span data-ttu-id="0c0bc-603">如果為 `null`，就會針對失敗狀態回報 [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-603">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="0c0bc-604">標籤 (`IEnumerable<string>`)。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-604">tags (`IEnumerable<string>`).</span></span>

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

## <a name="health-check-publisher"></a><span data-ttu-id="0c0bc-605">健康狀態檢查發行者</span><span class="sxs-lookup"><span data-stu-id="0c0bc-605">Health Check Publisher</span></span>

<span data-ttu-id="0c0bc-606">當 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 新增至服務容器時，健康狀態檢查系統會定期執行健康狀態檢查，並呼叫 `PublishAsync` 傳回結果。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-606">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="0c0bc-607">這對推送型健康狀態監控系統案例很有用，其預期每個處理序會定期呼叫監控系統來判斷健康狀態。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-607">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="0c0bc-608"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 介面有單一方法：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-608">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="0c0bc-609"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> 可讓您設定：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-609"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="0c0bc-610"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>：在執行實例之前，應用程式啟動後所套用的初始延遲 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-610"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="0c0bc-611">在啟動後就會套用延遲，但不會套用至後續的反覆項目。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-611">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="0c0bc-612">預設值是五秒鐘。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-612">The default value is five seconds.</span></span>
* <span data-ttu-id="0c0bc-613"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>：執行的期間 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-613"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="0c0bc-614">預設值為 30 秒。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-614">The default value is 30 seconds.</span></span>
* <span data-ttu-id="0c0bc-615"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>：如果 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> `null` (預設) ，健全狀況檢查發行者服務會執行所有已註冊的健康情況檢查。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-615"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="0c0bc-616">若要執行一部分的健康狀態檢查，請提供可篩選該組檢查的函式。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-616">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="0c0bc-617">每個期間都會評估該述詞。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-617">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="0c0bc-618"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>：針對所有實例執行健康情況檢查的超時時間 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-618"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="0c0bc-619">若要在沒有逾時的情況下執行，請使用 <xref:System.Threading.Timeout.InfiniteTimeSpan>。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-619">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="0c0bc-620">預設值為 30 秒。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-620">The default value is 30 seconds.</span></span>

> [!WARNING]
> <span data-ttu-id="0c0bc-621">在 ASP.NET Core 2.2 版中，設定 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>並不會獲得 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 實作遵守；它會設定 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> 的值。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-621">In the ASP.NET Core 2.2 release, setting <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> isn't honored by the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation; it sets the value of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>.</span></span> <span data-ttu-id="0c0bc-622">ASP.NET Core 3.0 已解決此問題。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-622">This issue has been addressed in ASP.NET Core 3.0.</span></span>

<span data-ttu-id="0c0bc-623">在範例應用程式中，`ReadinessPublisher` 是一個 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 實作。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-623">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="0c0bc-624">每個檢查的健康狀態檢查狀態都會記錄為：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-624">The health check status is logged for each check as either:</span></span>

* <span data-ttu-id="0c0bc-625"><xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>如果健康狀態檢查狀態為， () 資訊 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy> 。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-625">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="0c0bc-626"><xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>當狀態為或時，)  (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> 錯誤 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy> 。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-626">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="0c0bc-627">在範例應用程式的 `LivenessProbeStartup` 範例中，`StartupHostedService` 整備檢查具有 2 秒的啟動延遲，且每 30 秒會執行一次檢查。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-627">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="0c0bc-628">為了啟用 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 實作，此範例會在[相依性插入 (DI)](xref:fundamentals/dependency-injection) 容器中將 `ReadinessPublisher` 註冊為單一服務：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-628">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices&highlight=12-17,28)]

> [!NOTE]
> <span data-ttu-id="0c0bc-629">以下因應措施可允許在已將一或多個其他託管服務新增至應用程式的情況下，將 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 執行個體新增至服務容器。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-629">The following workaround permits adding an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instance to the service container when one or more other hosted services have already been added to the app.</span></span> <span data-ttu-id="0c0bc-630">ASP.NET Core 3.0 不需要此因應措施。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-630">This workaround won't isn't required in ASP.NET Core 3.0.</span></span>
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
> <span data-ttu-id="0c0bc-631">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) \(英文\) 隨附數個系統的發行者，包括 [Application Insights](/azure/application-insights/app-insights-overview)。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-631">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="0c0bc-632">[AspNetCore](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 不會由 Microsoft 維護或支援 HealthChecks。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-632">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="0c0bc-633">利用 MapWhen 限制健康情況檢查</span><span class="sxs-lookup"><span data-stu-id="0c0bc-633">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="0c0bc-634">使用 <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> 來有條件地將健康情況檢查端點的要求管線分支。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-634">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="0c0bc-635">在下列範例中， `MapWhen` 如果收到端點的 GET 要求，則會將要求管線分支以啟用健康情況檢查中介軟體 `api/HealthCheck` ：</span><span class="sxs-lookup"><span data-stu-id="0c0bc-635">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseMvc();
```

<span data-ttu-id="0c0bc-636">如需詳細資訊，請參閱<xref:fundamentals/middleware/index#use-run-and-map>。</span><span class="sxs-lookup"><span data-stu-id="0c0bc-636">For more information, see <xref:fundamentals/middleware/index#use-run-and-map>.</span></span>

::: moniker-end
