---
title: 在 ASP.NET Core 中使用託管服務的背景工作
author: rick-anderson
description: 了解如何在 ASP.NET Core 中使用託管服務實作背景工作。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2020
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
uid: fundamentals/host/hosted-services
ms.openlocfilehash: c0492c0c5b660e1387b0d0a4f6be405ded49ee92
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587472"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a><span data-ttu-id="6ece0-103">在 ASP.NET Core 中使用託管服務的背景工作</span><span class="sxs-lookup"><span data-stu-id="6ece0-103">Background tasks with hosted services in ASP.NET Core</span></span>

<span data-ttu-id="6ece0-104">[Jeow Li Huan](https://github.com/huan086)</span><span class="sxs-lookup"><span data-stu-id="6ece0-104">By [Jeow Li Huan](https://github.com/huan086)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6ece0-105">在 ASP.NET Core 中，背景工作可實作為「託管服務」。</span><span class="sxs-lookup"><span data-stu-id="6ece0-105">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="6ece0-106">託管服務是具有背景工作邏輯的類別，可實作 <xref:Microsoft.Extensions.Hosting.IHostedService> 介面。</span><span class="sxs-lookup"><span data-stu-id="6ece0-106">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="6ece0-107">本主題提供三個託管服務範例：</span><span class="sxs-lookup"><span data-stu-id="6ece0-107">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="6ece0-108">在計時器上執行的背景工作。</span><span class="sxs-lookup"><span data-stu-id="6ece0-108">Background task that runs on a timer.</span></span>
* <span data-ttu-id="6ece0-109">啟用 [範圍服務](xref:fundamentals/dependency-injection#service-lifetimes)的託管服務。</span><span class="sxs-lookup"><span data-stu-id="6ece0-109">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="6ece0-110">已設定範圍的服務可以使用 [ (DI) ](xref:fundamentals/dependency-injection)的相依性插入。</span><span class="sxs-lookup"><span data-stu-id="6ece0-110">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="6ece0-111">以循序方式執行的排入佇列背景工作。</span><span class="sxs-lookup"><span data-stu-id="6ece0-111">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="6ece0-112">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/hosted-services/samples/) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="6ece0-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="worker-service-template"></a><span data-ttu-id="6ece0-113">背景工作服務範本</span><span class="sxs-lookup"><span data-stu-id="6ece0-113">Worker Service template</span></span>

<span data-ttu-id="6ece0-114">ASP.NET Core 背景工作服務範本提供撰寫長期執行服務應用程式的起點。</span><span class="sxs-lookup"><span data-stu-id="6ece0-114">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="6ece0-115">從背景工作服務範本建立的應用程式會在其專案檔中指定工作者 SDK：</span><span class="sxs-lookup"><span data-stu-id="6ece0-115">An app created from the Worker Service template specifies the Worker SDK in its project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

<span data-ttu-id="6ece0-116">使用範本作為裝載服務應用程式的基礎：</span><span class="sxs-lookup"><span data-stu-id="6ece0-116">To use the template as a basis for a hosted services app:</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="package"></a><span data-ttu-id="6ece0-117">套件</span><span class="sxs-lookup"><span data-stu-id="6ece0-117">Package</span></span>

<span data-ttu-id="6ece0-118">以背景工作角色服務範本為基礎的應用程式會使用 `Microsoft.NET.Sdk.Worker` SDK，而且會有明確的套件參考，可參考至 [裝載](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) 套件。</span><span class="sxs-lookup"><span data-stu-id="6ece0-118">An app based on the Worker Service template uses the `Microsoft.NET.Sdk.Worker` SDK and has an explicit package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span> <span data-ttu-id="6ece0-119">例如，請參閱範例應用程式的專案檔 (*BackgroundTasksSample .csproj*) 。</span><span class="sxs-lookup"><span data-stu-id="6ece0-119">For example, see the sample app's project file (*BackgroundTasksSample.csproj*).</span></span>

<span data-ttu-id="6ece0-120">針對使用 SDK 的 web 應用程式 `Microsoft.NET.Sdk.Web` ，會以隱含方式從共用架構參考 [裝載](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) 套件。</span><span class="sxs-lookup"><span data-stu-id="6ece0-120">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package is referenced implicitly from the shared framework.</span></span> <span data-ttu-id="6ece0-121">應用程式專案檔中不需要明確的套件參考。</span><span class="sxs-lookup"><span data-stu-id="6ece0-121">An explicit package reference in the app's project file isn't required.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="6ece0-122">IHostedService 介面</span><span class="sxs-lookup"><span data-stu-id="6ece0-122">IHostedService interface</span></span>

<span data-ttu-id="6ece0-123"><xref:Microsoft.Extensions.Hosting.IHostedService>介面會為主機所管理的物件定義兩種方法：</span><span class="sxs-lookup"><span data-stu-id="6ece0-123">The <xref:Microsoft.Extensions.Hosting.IHostedService> interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="6ece0-124">[StartAsync (CancellationToken) ](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*)： `StartAsync` 包含啟動背景工作的邏輯。</span><span class="sxs-lookup"><span data-stu-id="6ece0-124">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="6ece0-125">`StartAsync` 會 *在之前* 呼叫：</span><span class="sxs-lookup"><span data-stu-id="6ece0-125">`StartAsync` is called *before*:</span></span>

  * <span data-ttu-id="6ece0-126">應用程式的要求處理管線已設定 (`Startup.Configure`) 。</span><span class="sxs-lookup"><span data-stu-id="6ece0-126">The app's request processing pipeline is configured (`Startup.Configure`).</span></span>
  * <span data-ttu-id="6ece0-127">伺服器已啟動且 [IApplicationLifetime。 ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) 已觸發。</span><span class="sxs-lookup"><span data-stu-id="6ece0-127">The server is started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span>

  <span data-ttu-id="6ece0-128">您可以變更預設行為，讓託管服務在 `StartAsync` 應用程式的管線設定完成之後執行，然後 `ApplicationStarted` 呼叫。</span><span class="sxs-lookup"><span data-stu-id="6ece0-128">The default behavior can be changed so that the hosted service's `StartAsync` runs after the app's pipeline has been configured and `ApplicationStarted` is called.</span></span> <span data-ttu-id="6ece0-129">若要變更預設行為，請 `VideosWatcher` 在呼叫之後，在下列範例) 中加入託管服務 (`ConfigureWebHostDefaults` ：</span><span class="sxs-lookup"><span data-stu-id="6ece0-129">To change the default behavior, add the hosted service (`VideosWatcher` in the following example) after calling `ConfigureWebHostDefaults`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Hosting;
  using Microsoft.Extensions.DependencyInjection;
  using Microsoft.Extensions.Hosting;

  public class Program
  {
      public static void Main(string[] args)
      {
          CreateHostBuilder(args).Build().Run();
      }

      public static IHostBuilder CreateHostBuilder(string[] args) =>
          Host.CreateDefaultBuilder(args)
              .ConfigureWebHostDefaults(webBuilder =>
              {
                  webBuilder.UseStartup<Startup>();
              })
              .ConfigureServices(services =>
              {
                  services.AddHostedService<VideosWatcher>();
              });
  }
  ```

* <span data-ttu-id="6ece0-130">[StopAsync (CancellationToken) ](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*)：當主機執行正常關機時觸發。</span><span class="sxs-lookup"><span data-stu-id="6ece0-130">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="6ece0-131">`StopAsync` 包含用來結束背景工作的邏輯。</span><span class="sxs-lookup"><span data-stu-id="6ece0-131">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="6ece0-132">實作 <xref:System.IDisposable> 和 [完成項 (解構函式)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) 以處置任何非受控的資源。</span><span class="sxs-lookup"><span data-stu-id="6ece0-132">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="6ece0-133">取消權杖有五秒的逾時預設值，以表示關機程序應該不再順利。</span><span class="sxs-lookup"><span data-stu-id="6ece0-133">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="6ece0-134">在權杖上要求取消時：</span><span class="sxs-lookup"><span data-stu-id="6ece0-134">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="6ece0-135">應終止應用程式正在執行的任何剩餘背景作業。</span><span class="sxs-lookup"><span data-stu-id="6ece0-135">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="6ece0-136">在 `StopAsync` 中呼叫的任何方法應立即傳回。</span><span class="sxs-lookup"><span data-stu-id="6ece0-136">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="6ece0-137">不過，不會在要求取消後直接放棄工作&mdash;呼叫者會等待所有工作完成。</span><span class="sxs-lookup"><span data-stu-id="6ece0-137">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="6ece0-138">如果應用程式意外關閉 (例如，應用程式的處理序失敗)，可能不會呼叫 `StopAsync`。</span><span class="sxs-lookup"><span data-stu-id="6ece0-138">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="6ece0-139">因此，任何在 `StopAsync` 中所呼叫方法或所執行作業可能不會發生。</span><span class="sxs-lookup"><span data-stu-id="6ece0-139">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="6ece0-140">若要延長預設的五秒鐘關機逾時，請設定：</span><span class="sxs-lookup"><span data-stu-id="6ece0-140">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="6ece0-141"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> (使用泛型主機時)。</span><span class="sxs-lookup"><span data-stu-id="6ece0-141"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="6ece0-142">如需詳細資訊，請參閱<xref:fundamentals/host/generic-host#shutdown-timeout>。</span><span class="sxs-lookup"><span data-stu-id="6ece0-142">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="6ece0-143">使用 Web 主機時，關機逾時會裝載組態設定。</span><span class="sxs-lookup"><span data-stu-id="6ece0-143">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="6ece0-144">如需詳細資訊，請參閱<xref:fundamentals/host/web-host#shutdown-timeout>。</span><span class="sxs-lookup"><span data-stu-id="6ece0-144">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="6ece0-145">託管服務會在應用程式啟動時隨即啟動，然後在應用程式關閉時正常關閉。</span><span class="sxs-lookup"><span data-stu-id="6ece0-145">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="6ece0-146">如果在背景工作執行期間擲回錯誤，即使未呼叫 `StopAsync`，也應該呼叫 `Dispose`。</span><span class="sxs-lookup"><span data-stu-id="6ece0-146">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="backgroundservice-base-class"></a><span data-ttu-id="6ece0-147">BackgroundService 基類</span><span class="sxs-lookup"><span data-stu-id="6ece0-147">BackgroundService base class</span></span>

<span data-ttu-id="6ece0-148"><xref:Microsoft.Extensions.Hosting.BackgroundService> 是用來執行長時間執行的基類 <xref:Microsoft.Extensions.Hosting.IHostedService> 。</span><span class="sxs-lookup"><span data-stu-id="6ece0-148"><xref:Microsoft.Extensions.Hosting.BackgroundService> is a base class for implementing a long running <xref:Microsoft.Extensions.Hosting.IHostedService>.</span></span>

<span data-ttu-id="6ece0-149">呼叫[ExecuteAsync (CancellationToken) ](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) ，以執行背景服務。</span><span class="sxs-lookup"><span data-stu-id="6ece0-149">[ExecuteAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) is called to run the background service.</span></span> <span data-ttu-id="6ece0-150">執行 <xref:System.Threading.Tasks.Task> 會傳回，代表背景服務的整個存留期。</span><span class="sxs-lookup"><span data-stu-id="6ece0-150">The implementation returns a <xref:System.Threading.Tasks.Task> that represents the entire lifetime of the background service.</span></span> <span data-ttu-id="6ece0-151">在 [ExecuteAsync 變成非同步](https://github.com/dotnet/extensions/issues/2149)之前，不會啟動任何其他服務，例如藉由呼叫 `await` 。</span><span class="sxs-lookup"><span data-stu-id="6ece0-151">No further services are started until [ExecuteAsync becomes asynchronous](https://github.com/dotnet/extensions/issues/2149), such as by calling `await`.</span></span> <span data-ttu-id="6ece0-152">避免執行長時間的封鎖初始化工作 `ExecuteAsync` 。</span><span class="sxs-lookup"><span data-stu-id="6ece0-152">Avoid performing long, blocking initialization work in `ExecuteAsync`.</span></span> <span data-ttu-id="6ece0-153">StopAsync 中的主機區塊 [ (CancellationToken) ](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) 等候 `ExecuteAsync` 完成。</span><span class="sxs-lookup"><span data-stu-id="6ece0-153">The host blocks in [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) waiting for `ExecuteAsync` to complete.</span></span>

<span data-ttu-id="6ece0-154">呼叫 [IHostedService](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) 時，會觸發解除標記。</span><span class="sxs-lookup"><span data-stu-id="6ece0-154">The cancellation token is triggered when [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) is called.</span></span> <span data-ttu-id="6ece0-155">您應在 `ExecuteAsync` 引發解除標記時立即完成您的執行，以便正常地關閉服務。</span><span class="sxs-lookup"><span data-stu-id="6ece0-155">Your implementation of `ExecuteAsync` should finish promptly when the cancellation token is fired in order to gracefully shut down the service.</span></span> <span data-ttu-id="6ece0-156">否則，服務強制會在關閉超時時關機。</span><span class="sxs-lookup"><span data-stu-id="6ece0-156">Otherwise, the service ungracefully shuts down at the shutdown timeout.</span></span> <span data-ttu-id="6ece0-157">如需詳細資訊，請參閱 [IHostedService 介面](#ihostedservice-interface) 一節。</span><span class="sxs-lookup"><span data-stu-id="6ece0-157">For more information, see the [IHostedService interface](#ihostedservice-interface) section.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="6ece0-158">計時背景工作</span><span class="sxs-lookup"><span data-stu-id="6ece0-158">Timed background tasks</span></span>

<span data-ttu-id="6ece0-159">計時背景工作使用 [System.Threading.Timer](xref:System.Threading.Timer) 類別。</span><span class="sxs-lookup"><span data-stu-id="6ece0-159">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="6ece0-160">此計時器會觸發工作的 `DoWork` 方法。</span><span class="sxs-lookup"><span data-stu-id="6ece0-160">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="6ece0-161">計時器已在 `StopAsync` 停用，並會在處置服務容器時於 `Dispose` 上進行處置：</span><span class="sxs-lookup"><span data-stu-id="6ece0-161">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-17,34,41)]

<span data-ttu-id="6ece0-162"><xref:System.Threading.Timer>不會等候先前的執行 `DoWork` 完成，因此所顯示的方法可能不適合每種案例。</span><span class="sxs-lookup"><span data-stu-id="6ece0-162">The <xref:System.Threading.Timer> doesn't wait for previous executions of `DoWork` to finish, so the approach shown might not be suitable for every scenario.</span></span> <span data-ttu-id="6ece0-163">[連鎖。遞增](xref:System.Threading.Interlocked.Increment*) 是用來將執行計數器遞增為不可部分完成的作業，以確保多個執行緒不會 `executionCount` 同時更新。</span><span class="sxs-lookup"><span data-stu-id="6ece0-163">[Interlocked.Increment](xref:System.Threading.Interlocked.Increment*) is used to increment the execution counter as an atomic operation, which ensures that multiple threads don't update `executionCount` concurrently.</span></span>

<span data-ttu-id="6ece0-164">服務會在 `IHostBuilder.ConfigureServices` (*Program.cs*) 中註冊 `AddHostedService` 擴充方法：</span><span class="sxs-lookup"><span data-stu-id="6ece0-164">The service is registered in `IHostBuilder.ConfigureServices` (*Program.cs*) with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="6ece0-165">在背景工作中使用範圍服務</span><span class="sxs-lookup"><span data-stu-id="6ece0-165">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="6ece0-166">若要在[BackgroundService](#backgroundservice-base-class)中使用[範圍服務](xref:fundamentals/dependency-injection#service-lifetimes)，請建立一個範圍。</span><span class="sxs-lookup"><span data-stu-id="6ece0-166">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within a [BackgroundService](#backgroundservice-base-class), create a scope.</span></span> <span data-ttu-id="6ece0-167">根據預設，不會針對託管服務建立任何範圍。</span><span class="sxs-lookup"><span data-stu-id="6ece0-167">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="6ece0-168">範圍背景工作服務包含背景工作的邏輯。</span><span class="sxs-lookup"><span data-stu-id="6ece0-168">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="6ece0-169">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="6ece0-169">In the following example:</span></span>

* <span data-ttu-id="6ece0-170">服務是非同步。</span><span class="sxs-lookup"><span data-stu-id="6ece0-170">The service is asynchronous.</span></span> <span data-ttu-id="6ece0-171">`DoWork` 方法會傳回 `Task`。</span><span class="sxs-lookup"><span data-stu-id="6ece0-171">The `DoWork` method returns a `Task`.</span></span> <span data-ttu-id="6ece0-172">基於示範目的，方法中會等待10秒的延遲 `DoWork` 。</span><span class="sxs-lookup"><span data-stu-id="6ece0-172">For demonstration purposes, a delay of ten seconds is awaited in the `DoWork` method.</span></span>
* <span data-ttu-id="6ece0-173"><xref:Microsoft.Extensions.Logging.ILogger>會插入服務。</span><span class="sxs-lookup"><span data-stu-id="6ece0-173">An <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="6ece0-174">裝載服務會建立範圍來解析已設定範圍的背景工作服務，以呼叫其 `DoWork` 方法。</span><span class="sxs-lookup"><span data-stu-id="6ece0-174">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method.</span></span> <span data-ttu-id="6ece0-175">`DoWork` 傳回 `Task` ，它會在中等待 `ExecuteAsync` ：</span><span class="sxs-lookup"><span data-stu-id="6ece0-175">`DoWork` returns a `Task`, which is awaited in `ExecuteAsync`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

<span data-ttu-id="6ece0-176">這些服務會在 `IHostBuilder.ConfigureServices` (*Program.cs*) 中註冊。</span><span class="sxs-lookup"><span data-stu-id="6ece0-176">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="6ece0-177">託管服務是使用 `AddHostedService` 擴充方法來註冊：</span><span class="sxs-lookup"><span data-stu-id="6ece0-177">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="6ece0-178">排入佇列背景工作</span><span class="sxs-lookup"><span data-stu-id="6ece0-178">Queued background tasks</span></span>

<span data-ttu-id="6ece0-179">背景工作佇列是以 .NET 4.x 為基礎 <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ：</span><span class="sxs-lookup"><span data-stu-id="6ece0-179">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*>:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="6ece0-180">在下列 `QueueHostedService` 範例中：</span><span class="sxs-lookup"><span data-stu-id="6ece0-180">In the following `QueueHostedService` example:</span></span>

* <span data-ttu-id="6ece0-181">`BackgroundProcessing`方法 `Task` 會傳回在中等候的 `ExecuteAsync` 。</span><span class="sxs-lookup"><span data-stu-id="6ece0-181">The `BackgroundProcessing` method returns a `Task`, which is awaited in `ExecuteAsync`.</span></span>
* <span data-ttu-id="6ece0-182">佇列中的背景工作會從佇列中清除並執行 `BackgroundProcessing` 。</span><span class="sxs-lookup"><span data-stu-id="6ece0-182">Background tasks in the queue are dequeued and executed in `BackgroundProcessing`.</span></span>
* <span data-ttu-id="6ece0-183">在服務停止之前，會等待工作專案 `StopAsync` 。</span><span class="sxs-lookup"><span data-stu-id="6ece0-183">Work items are awaited before the service stops in `StopAsync`.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

<span data-ttu-id="6ece0-184">`MonitorLoop`每當在 `w` 輸入裝置上選取索引鍵時，服務會處理託管服務的佇列工作：</span><span class="sxs-lookup"><span data-stu-id="6ece0-184">A `MonitorLoop` service handles enqueuing tasks for the hosted service whenever the `w` key is selected on an input device:</span></span>

* <span data-ttu-id="6ece0-185">`IBackgroundTaskQueue`會插入 `MonitorLoop` 服務中。</span><span class="sxs-lookup"><span data-stu-id="6ece0-185">The `IBackgroundTaskQueue` is injected into the `MonitorLoop` service.</span></span>
* <span data-ttu-id="6ece0-186">`IBackgroundTaskQueue.QueueBackgroundWorkItem` 呼叫以將工作專案排入佇列。</span><span class="sxs-lookup"><span data-stu-id="6ece0-186">`IBackgroundTaskQueue.QueueBackgroundWorkItem` is called to enqueue a work item.</span></span>
* <span data-ttu-id="6ece0-187">工作專案會模擬長時間執行的背景工作：</span><span class="sxs-lookup"><span data-stu-id="6ece0-187">The work item simulates a long-running background task:</span></span>
  * <span data-ttu-id="6ece0-188">3 5-秒的延遲是 (`Task.Delay`) 執行。</span><span class="sxs-lookup"><span data-stu-id="6ece0-188">Three 5-second delays are executed (`Task.Delay`).</span></span>
  * <span data-ttu-id="6ece0-189">`try-catch` <xref:System.OperationCanceledException> 如果工作已取消，則語句會補漏白。</span><span class="sxs-lookup"><span data-stu-id="6ece0-189">A `try-catch` statement traps <xref:System.OperationCanceledException> if the task is cancelled.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

<span data-ttu-id="6ece0-190">這些服務會在 `IHostBuilder.ConfigureServices` (*Program.cs*) 中註冊。</span><span class="sxs-lookup"><span data-stu-id="6ece0-190">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="6ece0-191">託管服務是使用 `AddHostedService` 擴充方法來註冊：</span><span class="sxs-lookup"><span data-stu-id="6ece0-191">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

<span data-ttu-id="6ece0-192">`MonitorLoop` 啟動時間 `Program.Main` ：</span><span class="sxs-lookup"><span data-stu-id="6ece0-192">`MonitorLoop` is started in `Program.Main`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6ece0-193">在 ASP.NET Core 中，背景工作可實作為「託管服務」。</span><span class="sxs-lookup"><span data-stu-id="6ece0-193">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="6ece0-194">託管服務是具有背景工作邏輯的類別，可實作 <xref:Microsoft.Extensions.Hosting.IHostedService> 介面。</span><span class="sxs-lookup"><span data-stu-id="6ece0-194">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="6ece0-195">本主題提供三個託管服務範例：</span><span class="sxs-lookup"><span data-stu-id="6ece0-195">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="6ece0-196">在計時器上執行的背景工作。</span><span class="sxs-lookup"><span data-stu-id="6ece0-196">Background task that runs on a timer.</span></span>
* <span data-ttu-id="6ece0-197">啟用 [範圍服務](xref:fundamentals/dependency-injection#service-lifetimes)的託管服務。</span><span class="sxs-lookup"><span data-stu-id="6ece0-197">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="6ece0-198">已設定範圍的服務可以使用相依性 [插入 (DI) ](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="6ece0-198">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection)</span></span>
* <span data-ttu-id="6ece0-199">以循序方式執行的排入佇列背景工作。</span><span class="sxs-lookup"><span data-stu-id="6ece0-199">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="6ece0-200">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/hosted-services/samples/) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="6ece0-200">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="package"></a><span data-ttu-id="6ece0-201">套件</span><span class="sxs-lookup"><span data-stu-id="6ece0-201">Package</span></span>

<span data-ttu-id="6ece0-202">參考 [Microsoft.AspNetCore.App 中繼套件](xref:fundamentals/metapackage-app)，或新增 [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) 套件的套件參考。</span><span class="sxs-lookup"><span data-stu-id="6ece0-202">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="6ece0-203">IHostedService 介面</span><span class="sxs-lookup"><span data-stu-id="6ece0-203">IHostedService interface</span></span>

<span data-ttu-id="6ece0-204">託管服務會實作 <xref:Microsoft.Extensions.Hosting.IHostedService> 介面。</span><span class="sxs-lookup"><span data-stu-id="6ece0-204">Hosted services implement the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="6ece0-205">此介面針對主機所管理的物件定義兩種方法：</span><span class="sxs-lookup"><span data-stu-id="6ece0-205">The interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="6ece0-206">[StartAsync (CancellationToken) ](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*)： `StartAsync` 包含啟動背景工作的邏輯。</span><span class="sxs-lookup"><span data-stu-id="6ece0-206">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="6ece0-207">使用 [Web 主機](xref:fundamentals/host/web-host)時， `StartAsync` 會在伺服器啟動後呼叫，且 [IApplicationLifetime](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) 會觸發 ApplicationStarted。</span><span class="sxs-lookup"><span data-stu-id="6ece0-207">When using the [Web Host](xref:fundamentals/host/web-host), `StartAsync` is called after the server has started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span> <span data-ttu-id="6ece0-208">使用 [泛型主](xref:fundamentals/host/generic-host)控制項時， `StartAsync` 會在觸發之前呼叫 `ApplicationStarted` 。</span><span class="sxs-lookup"><span data-stu-id="6ece0-208">When using the [Generic Host](xref:fundamentals/host/generic-host), `StartAsync` is called before `ApplicationStarted` is triggered.</span></span>

* <span data-ttu-id="6ece0-209">[StopAsync (CancellationToken) ](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*)：當主機執行正常關機時觸發。</span><span class="sxs-lookup"><span data-stu-id="6ece0-209">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="6ece0-210">`StopAsync` 包含用來結束背景工作的邏輯。</span><span class="sxs-lookup"><span data-stu-id="6ece0-210">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="6ece0-211">實作 <xref:System.IDisposable> 和 [完成項 (解構函式)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) 以處置任何非受控的資源。</span><span class="sxs-lookup"><span data-stu-id="6ece0-211">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="6ece0-212">取消權杖有五秒的逾時預設值，以表示關機程序應該不再順利。</span><span class="sxs-lookup"><span data-stu-id="6ece0-212">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="6ece0-213">在權杖上要求取消時：</span><span class="sxs-lookup"><span data-stu-id="6ece0-213">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="6ece0-214">應終止應用程式正在執行的任何剩餘背景作業。</span><span class="sxs-lookup"><span data-stu-id="6ece0-214">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="6ece0-215">在 `StopAsync` 中呼叫的任何方法應立即傳回。</span><span class="sxs-lookup"><span data-stu-id="6ece0-215">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="6ece0-216">不過，不會在要求取消後直接放棄工作&mdash;呼叫者會等待所有工作完成。</span><span class="sxs-lookup"><span data-stu-id="6ece0-216">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="6ece0-217">如果應用程式意外關閉 (例如，應用程式的處理序失敗)，可能不會呼叫 `StopAsync`。</span><span class="sxs-lookup"><span data-stu-id="6ece0-217">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="6ece0-218">因此，任何在 `StopAsync` 中所呼叫方法或所執行作業可能不會發生。</span><span class="sxs-lookup"><span data-stu-id="6ece0-218">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="6ece0-219">若要延長預設的五秒鐘關機逾時，請設定：</span><span class="sxs-lookup"><span data-stu-id="6ece0-219">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="6ece0-220"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> (使用泛型主機時)。</span><span class="sxs-lookup"><span data-stu-id="6ece0-220"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="6ece0-221">如需詳細資訊，請參閱<xref:fundamentals/host/generic-host#shutdown-timeout>。</span><span class="sxs-lookup"><span data-stu-id="6ece0-221">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="6ece0-222">使用 Web 主機時，關機逾時會裝載組態設定。</span><span class="sxs-lookup"><span data-stu-id="6ece0-222">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="6ece0-223">如需詳細資訊，請參閱<xref:fundamentals/host/web-host#shutdown-timeout>。</span><span class="sxs-lookup"><span data-stu-id="6ece0-223">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="6ece0-224">託管服務會在應用程式啟動時隨即啟動，然後在應用程式關閉時正常關閉。</span><span class="sxs-lookup"><span data-stu-id="6ece0-224">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="6ece0-225">如果在背景工作執行期間擲回錯誤，即使未呼叫 `StopAsync`，也應該呼叫 `Dispose`。</span><span class="sxs-lookup"><span data-stu-id="6ece0-225">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="6ece0-226">計時背景工作</span><span class="sxs-lookup"><span data-stu-id="6ece0-226">Timed background tasks</span></span>

<span data-ttu-id="6ece0-227">計時背景工作使用 [System.Threading.Timer](xref:System.Threading.Timer) 類別。</span><span class="sxs-lookup"><span data-stu-id="6ece0-227">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="6ece0-228">此計時器會觸發工作的 `DoWork` 方法。</span><span class="sxs-lookup"><span data-stu-id="6ece0-228">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="6ece0-229">計時器已在 `StopAsync` 停用，並會在處置服務容器時於 `Dispose` 上進行處置：</span><span class="sxs-lookup"><span data-stu-id="6ece0-229">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<span data-ttu-id="6ece0-230"><xref:System.Threading.Timer>不會等候先前的執行 `DoWork` 完成，因此所顯示的方法可能不適合每種案例。</span><span class="sxs-lookup"><span data-stu-id="6ece0-230">The <xref:System.Threading.Timer> doesn't wait for previous executions of `DoWork` to finish, so the approach shown might not be suitable for every scenario.</span></span>

<span data-ttu-id="6ece0-231">服務是在 `Startup.ConfigureServices` 中使用 `AddHostedService` 擴充方法註冊：</span><span class="sxs-lookup"><span data-stu-id="6ece0-231">The service is registered in `Startup.ConfigureServices` with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="6ece0-232">在背景工作中使用範圍服務</span><span class="sxs-lookup"><span data-stu-id="6ece0-232">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="6ece0-233">若要在中使用 [範圍服務](xref:fundamentals/dependency-injection#service-lifetimes) `IHostedService` ，請建立一個範圍。</span><span class="sxs-lookup"><span data-stu-id="6ece0-233">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within an `IHostedService`, create a scope.</span></span> <span data-ttu-id="6ece0-234">根據預設，不會針對託管服務建立任何範圍。</span><span class="sxs-lookup"><span data-stu-id="6ece0-234">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="6ece0-235">範圍背景工作服務包含背景工作的邏輯。</span><span class="sxs-lookup"><span data-stu-id="6ece0-235">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="6ece0-236">在下列範例中，<xref:Microsoft.Extensions.Logging.ILogger> 會插入至服務：</span><span class="sxs-lookup"><span data-stu-id="6ece0-236">In the following example, an <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="6ece0-237">託管服務會建立範圍來解析範圍背景工作服務，以呼叫其 `DoWork` 方法：</span><span class="sxs-lookup"><span data-stu-id="6ece0-237">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

<span data-ttu-id="6ece0-238">這些服務會在 `Startup.ConfigureServices` 中註冊。</span><span class="sxs-lookup"><span data-stu-id="6ece0-238">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6ece0-239">`IHostedService` 實作是以 `AddHostedService` 擴充方法註冊：</span><span class="sxs-lookup"><span data-stu-id="6ece0-239">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="6ece0-240">排入佇列背景工作</span><span class="sxs-lookup"><span data-stu-id="6ece0-240">Queued background tasks</span></span>

<span data-ttu-id="6ece0-241">背景工作佇列是以 .NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([暫時排定為內建 ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)) ：</span><span class="sxs-lookup"><span data-stu-id="6ece0-241">A background task queue is based on the .NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="6ece0-242">在 `QueueHostedService` 中，佇列中的背景工作會從佇列中清除，並作為 [BackgroundService](#backgroundservice-base-class) 執行，這是實作長時間執行 `IHostedService` 的基底類別：</span><span class="sxs-lookup"><span data-stu-id="6ece0-242">In `QueueHostedService`, background tasks in the queue are dequeued and executed as a [BackgroundService](#backgroundservice-base-class), which is a base class for implementing a long running `IHostedService`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

<span data-ttu-id="6ece0-243">這些服務會在 `Startup.ConfigureServices` 中註冊。</span><span class="sxs-lookup"><span data-stu-id="6ece0-243">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6ece0-244">`IHostedService` 實作是以 `AddHostedService` 擴充方法註冊：</span><span class="sxs-lookup"><span data-stu-id="6ece0-244">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

<span data-ttu-id="6ece0-245">在索引頁面模型類別中：</span><span class="sxs-lookup"><span data-stu-id="6ece0-245">In the Index page model class:</span></span>

* <span data-ttu-id="6ece0-246">將 `IBackgroundTaskQueue` 插入至建構函式並指派給 `Queue`。</span><span class="sxs-lookup"><span data-stu-id="6ece0-246">The `IBackgroundTaskQueue` is injected into the constructor and assigned to `Queue`.</span></span>
* <span data-ttu-id="6ece0-247">插入 <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> 並指派給 `_serviceScopeFactory`。</span><span class="sxs-lookup"><span data-stu-id="6ece0-247">An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> is injected and assigned to `_serviceScopeFactory`.</span></span> <span data-ttu-id="6ece0-248">處理站會用來建立 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> 的執行個體，可用來在範圍內建立服務。</span><span class="sxs-lookup"><span data-stu-id="6ece0-248">The factory is used to create instances of <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, which is used to create services within a scope.</span></span> <span data-ttu-id="6ece0-249">建立範圍，以便使用應用程式的 `AppDbContext` ([具範圍服務](xref:fundamentals/dependency-injection#service-lifetimes))，在 `IBackgroundTaskQueue` (單一服務) 中寫入資料庫記錄。</span><span class="sxs-lookup"><span data-stu-id="6ece0-249">A scope is created in order to use the app's `AppDbContext` (a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes)) to write database records in the `IBackgroundTaskQueue` (a singleton service).</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="6ece0-250">在索引頁面上選取 [新增工作] 按鈕時，就會執行 `OnPostAddTask` 方法。</span><span class="sxs-lookup"><span data-stu-id="6ece0-250">When the **Add Task** button is selected on the Index page, the `OnPostAddTask` method is executed.</span></span> <span data-ttu-id="6ece0-251">`QueueBackgroundWorkItem` 呼叫以將工作專案排入佇列：</span><span class="sxs-lookup"><span data-stu-id="6ece0-251">`QueueBackgroundWorkItem` is called to enqueue a work item:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="6ece0-252">其他資源</span><span class="sxs-lookup"><span data-stu-id="6ece0-252">Additional resources</span></span>

* [<span data-ttu-id="6ece0-253">在微服務中使用 IHostedService 和 BackgroundService 類別實作背景工作</span><span class="sxs-lookup"><span data-stu-id="6ece0-253">Implement background tasks in microservices with IHostedService and the BackgroundService class</span></span>](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* [<span data-ttu-id="6ece0-254">在 Azure App Service 中使用 Webjob 執行背景工作</span><span class="sxs-lookup"><span data-stu-id="6ece0-254">Run background tasks with WebJobs in Azure App Service</span></span>](/azure/app-service/webjobs-create)
* <xref:System.Threading.Timer>
