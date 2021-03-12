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
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a>在 ASP.NET Core 中使用託管服務的背景工作

[Jeow Li Huan](https://github.com/huan086)

::: moniker range=">= aspnetcore-3.0"

在 ASP.NET Core 中，背景工作可實作為「託管服務」。 託管服務是具有背景工作邏輯的類別，可實作 <xref:Microsoft.Extensions.Hosting.IHostedService> 介面。 本主題提供三個託管服務範例：

* 在計時器上執行的背景工作。
* 啟用 [範圍服務](xref:fundamentals/dependency-injection#service-lifetimes)的託管服務。 已設定範圍的服務可以使用 [ (DI) ](xref:fundamentals/dependency-injection)的相依性插入。
* 以循序方式執行的排入佇列背景工作。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/hosted-services/samples/) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="worker-service-template"></a>背景工作服務範本

ASP.NET Core 背景工作服務範本提供撰寫長期執行服務應用程式的起點。 從背景工作服務範本建立的應用程式會在其專案檔中指定工作者 SDK：

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

使用範本作為裝載服務應用程式的基礎：

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="package"></a>套件

以背景工作角色服務範本為基礎的應用程式會使用 `Microsoft.NET.Sdk.Worker` SDK，而且會有明確的套件參考，可參考至 [裝載](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) 套件。 例如，請參閱範例應用程式的專案檔 (*BackgroundTasksSample .csproj*) 。

針對使用 SDK 的 web 應用程式 `Microsoft.NET.Sdk.Web` ，會以隱含方式從共用架構參考 [裝載](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) 套件。 應用程式專案檔中不需要明確的套件參考。

## <a name="ihostedservice-interface"></a>IHostedService 介面

<xref:Microsoft.Extensions.Hosting.IHostedService>介面會為主機所管理的物件定義兩種方法：

* [StartAsync (CancellationToken) ](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*)： `StartAsync` 包含啟動背景工作的邏輯。 `StartAsync` 會 *在之前* 呼叫：

  * 應用程式的要求處理管線已設定 (`Startup.Configure`) 。
  * 伺服器已啟動且 [IApplicationLifetime。 ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) 已觸發。

  您可以變更預設行為，讓託管服務在 `StartAsync` 應用程式的管線設定完成之後執行，然後 `ApplicationStarted` 呼叫。 若要變更預設行為，請 `VideosWatcher` 在呼叫之後，在下列範例) 中加入託管服務 (`ConfigureWebHostDefaults` ：

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

* [StopAsync (CancellationToken) ](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*)：當主機執行正常關機時觸發。 `StopAsync` 包含用來結束背景工作的邏輯。 實作 <xref:System.IDisposable> 和 [完成項 (解構函式)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) 以處置任何非受控的資源。

  取消權杖有五秒的逾時預設值，以表示關機程序應該不再順利。 在權杖上要求取消時：

  * 應終止應用程式正在執行的任何剩餘背景作業。
  * 在 `StopAsync` 中呼叫的任何方法應立即傳回。

  不過，不會在要求取消後直接放棄工作&mdash;呼叫者會等待所有工作完成。

  如果應用程式意外關閉 (例如，應用程式的處理序失敗)，可能不會呼叫 `StopAsync`。 因此，任何在 `StopAsync` 中所呼叫方法或所執行作業可能不會發生。

  若要延長預設的五秒鐘關機逾時，請設定：

  * <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> (使用泛型主機時)。 如需詳細資訊，請參閱<xref:fundamentals/host/generic-host#shutdown-timeout>。
  * 使用 Web 主機時，關機逾時會裝載組態設定。 如需詳細資訊，請參閱<xref:fundamentals/host/web-host#shutdown-timeout>。

託管服務會在應用程式啟動時隨即啟動，然後在應用程式關閉時正常關閉。 如果在背景工作執行期間擲回錯誤，即使未呼叫 `StopAsync`，也應該呼叫 `Dispose`。

## <a name="backgroundservice-base-class"></a>BackgroundService 基類

<xref:Microsoft.Extensions.Hosting.BackgroundService> 是用來執行長時間執行的基類 <xref:Microsoft.Extensions.Hosting.IHostedService> 。

呼叫[ExecuteAsync (CancellationToken) ](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) ，以執行背景服務。 執行 <xref:System.Threading.Tasks.Task> 會傳回，代表背景服務的整個存留期。 在 [ExecuteAsync 變成非同步](https://github.com/dotnet/extensions/issues/2149)之前，不會啟動任何其他服務，例如藉由呼叫 `await` 。 避免執行長時間的封鎖初始化工作 `ExecuteAsync` 。 StopAsync 中的主機區塊 [ (CancellationToken) ](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) 等候 `ExecuteAsync` 完成。

呼叫 [IHostedService](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) 時，會觸發解除標記。 您應在 `ExecuteAsync` 引發解除標記時立即完成您的執行，以便正常地關閉服務。 否則，服務強制會在關閉超時時關機。 如需詳細資訊，請參閱 [IHostedService 介面](#ihostedservice-interface) 一節。

## <a name="timed-background-tasks"></a>計時背景工作

計時背景工作使用 [System.Threading.Timer](xref:System.Threading.Timer) 類別。 此計時器會觸發工作的 `DoWork` 方法。 計時器已在 `StopAsync` 停用，並會在處置服務容器時於 `Dispose` 上進行處置：

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-17,34,41)]

<xref:System.Threading.Timer>不會等候先前的執行 `DoWork` 完成，因此所顯示的方法可能不適合每種案例。 [連鎖。遞增](xref:System.Threading.Interlocked.Increment*) 是用來將執行計數器遞增為不可部分完成的作業，以確保多個執行緒不會 `executionCount` 同時更新。

服務會在 `IHostBuilder.ConfigureServices` (*Program.cs*) 中註冊 `AddHostedService` 擴充方法：

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a>在背景工作中使用範圍服務

若要在[BackgroundService](#backgroundservice-base-class)中使用[範圍服務](xref:fundamentals/dependency-injection#service-lifetimes)，請建立一個範圍。 根據預設，不會針對託管服務建立任何範圍。

範圍背景工作服務包含背景工作的邏輯。 在下例中︰

* 服務是非同步。 `DoWork` 方法會傳回 `Task`。 基於示範目的，方法中會等待10秒的延遲 `DoWork` 。
* <xref:Microsoft.Extensions.Logging.ILogger>會插入服務。

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

裝載服務會建立範圍來解析已設定範圍的背景工作服務，以呼叫其 `DoWork` 方法。 `DoWork` 傳回 `Task` ，它會在中等待 `ExecuteAsync` ：

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

這些服務會在 `IHostBuilder.ConfigureServices` (*Program.cs*) 中註冊。 託管服務是使用 `AddHostedService` 擴充方法來註冊：

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a>排入佇列背景工作

背景工作佇列是以 .NET 4.x 為基礎 <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ：

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

在下列 `QueueHostedService` 範例中：

* `BackgroundProcessing`方法 `Task` 會傳回在中等候的 `ExecuteAsync` 。
* 佇列中的背景工作會從佇列中清除並執行 `BackgroundProcessing` 。
* 在服務停止之前，會等待工作專案 `StopAsync` 。

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

`MonitorLoop`每當在 `w` 輸入裝置上選取索引鍵時，服務會處理託管服務的佇列工作：

* `IBackgroundTaskQueue`會插入 `MonitorLoop` 服務中。
* `IBackgroundTaskQueue.QueueBackgroundWorkItem` 呼叫以將工作專案排入佇列。
* 工作專案會模擬長時間執行的背景工作：
  * 3 5-秒的延遲是 (`Task.Delay`) 執行。
  * `try-catch` <xref:System.OperationCanceledException> 如果工作已取消，則語句會補漏白。

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

這些服務會在 `IHostBuilder.ConfigureServices` (*Program.cs*) 中註冊。 託管服務是使用 `AddHostedService` 擴充方法來註冊：

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

`MonitorLoop` 啟動時間 `Program.Main` ：

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

在 ASP.NET Core 中，背景工作可實作為「託管服務」。 託管服務是具有背景工作邏輯的類別，可實作 <xref:Microsoft.Extensions.Hosting.IHostedService> 介面。 本主題提供三個託管服務範例：

* 在計時器上執行的背景工作。
* 啟用 [範圍服務](xref:fundamentals/dependency-injection#service-lifetimes)的託管服務。 已設定範圍的服務可以使用相依性 [插入 (DI) ](xref:fundamentals/dependency-injection)
* 以循序方式執行的排入佇列背景工作。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/hosted-services/samples/) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="package"></a>套件

參考 [Microsoft.AspNetCore.App 中繼套件](xref:fundamentals/metapackage-app)，或新增 [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) 套件的套件參考。

## <a name="ihostedservice-interface"></a>IHostedService 介面

託管服務會實作 <xref:Microsoft.Extensions.Hosting.IHostedService> 介面。 此介面針對主機所管理的物件定義兩種方法：

* [StartAsync (CancellationToken) ](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*)： `StartAsync` 包含啟動背景工作的邏輯。 使用 [Web 主機](xref:fundamentals/host/web-host)時， `StartAsync` 會在伺服器啟動後呼叫，且 [IApplicationLifetime](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) 會觸發 ApplicationStarted。 使用 [泛型主](xref:fundamentals/host/generic-host)控制項時， `StartAsync` 會在觸發之前呼叫 `ApplicationStarted` 。

* [StopAsync (CancellationToken) ](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*)：當主機執行正常關機時觸發。 `StopAsync` 包含用來結束背景工作的邏輯。 實作 <xref:System.IDisposable> 和 [完成項 (解構函式)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) 以處置任何非受控的資源。

  取消權杖有五秒的逾時預設值，以表示關機程序應該不再順利。 在權杖上要求取消時：

  * 應終止應用程式正在執行的任何剩餘背景作業。
  * 在 `StopAsync` 中呼叫的任何方法應立即傳回。

  不過，不會在要求取消後直接放棄工作&mdash;呼叫者會等待所有工作完成。

  如果應用程式意外關閉 (例如，應用程式的處理序失敗)，可能不會呼叫 `StopAsync`。 因此，任何在 `StopAsync` 中所呼叫方法或所執行作業可能不會發生。

  若要延長預設的五秒鐘關機逾時，請設定：

  * <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> (使用泛型主機時)。 如需詳細資訊，請參閱<xref:fundamentals/host/generic-host#shutdown-timeout>。
  * 使用 Web 主機時，關機逾時會裝載組態設定。 如需詳細資訊，請參閱<xref:fundamentals/host/web-host#shutdown-timeout>。

託管服務會在應用程式啟動時隨即啟動，然後在應用程式關閉時正常關閉。 如果在背景工作執行期間擲回錯誤，即使未呼叫 `StopAsync`，也應該呼叫 `Dispose`。

## <a name="timed-background-tasks"></a>計時背景工作

計時背景工作使用 [System.Threading.Timer](xref:System.Threading.Timer) 類別。 此計時器會觸發工作的 `DoWork` 方法。 計時器已在 `StopAsync` 停用，並會在處置服務容器時於 `Dispose` 上進行處置：

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<xref:System.Threading.Timer>不會等候先前的執行 `DoWork` 完成，因此所顯示的方法可能不適合每種案例。

服務是在 `Startup.ConfigureServices` 中使用 `AddHostedService` 擴充方法註冊：

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a>在背景工作中使用範圍服務

若要在中使用 [範圍服務](xref:fundamentals/dependency-injection#service-lifetimes) `IHostedService` ，請建立一個範圍。 根據預設，不會針對託管服務建立任何範圍。

範圍背景工作服務包含背景工作的邏輯。 在下列範例中，<xref:Microsoft.Extensions.Logging.ILogger> 會插入至服務：

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

託管服務會建立範圍來解析範圍背景工作服務，以呼叫其 `DoWork` 方法：

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

這些服務會在 `Startup.ConfigureServices` 中註冊。 `IHostedService` 實作是以 `AddHostedService` 擴充方法註冊：

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a>排入佇列背景工作

背景工作佇列是以 .NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([暫時排定為內建 ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)) ：

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

在 `QueueHostedService` 中，佇列中的背景工作會從佇列中清除，並作為 [BackgroundService](#backgroundservice-base-class) 執行，這是實作長時間執行 `IHostedService` 的基底類別：

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

這些服務會在 `Startup.ConfigureServices` 中註冊。 `IHostedService` 實作是以 `AddHostedService` 擴充方法註冊：

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

在索引頁面模型類別中：

* 將 `IBackgroundTaskQueue` 插入至建構函式並指派給 `Queue`。
* 插入 <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> 並指派給 `_serviceScopeFactory`。 處理站會用來建立 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> 的執行個體，可用來在範圍內建立服務。 建立範圍，以便使用應用程式的 `AppDbContext` ([具範圍服務](xref:fundamentals/dependency-injection#service-lifetimes))，在 `IBackgroundTaskQueue` (單一服務) 中寫入資料庫記錄。

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

在索引頁面上選取 [新增工作] 按鈕時，就會執行 `OnPostAddTask` 方法。 `QueueBackgroundWorkItem` 呼叫以將工作專案排入佇列：

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a>其他資源

* [在微服務中使用 IHostedService 和 BackgroundService 類別實作背景工作](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* [在 Azure App Service 中使用 Webjob 執行背景工作](/azure/app-service/webjobs-create)
* <xref:System.Threading.Timer>
