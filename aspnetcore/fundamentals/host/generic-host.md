---
title: .NET 泛型主機
author: rick-anderson
description: 了解 .NET Core 的泛型主機，其負責啟動應用程式及管理存留期。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
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
uid: fundamentals/host/generic-host
ms.openlocfilehash: 59cfae9ff619f8de894686c4b773d66e5cbe10ad
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91754732"
---
# <a name="net-generic-host"></a><span data-ttu-id="a4b91-103">.NET 泛型主機</span><span class="sxs-lookup"><span data-stu-id="a4b91-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="a4b91-104">ASP.NET Core 範本會建立 .NET Core 泛型主機 (<xref:Microsoft.Extensions.Hosting.HostBuilder>) 。</span><span class="sxs-lookup"><span data-stu-id="a4b91-104">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

## <a name="host-definition"></a><span data-ttu-id="a4b91-105">主機定義</span><span class="sxs-lookup"><span data-stu-id="a4b91-105">Host definition</span></span>

<span data-ttu-id="a4b91-106">「主機」\*\* 是封裝所有應用程式資源的物件，例如：</span><span class="sxs-lookup"><span data-stu-id="a4b91-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="a4b91-107">相依性插入 (DI)</span><span class="sxs-lookup"><span data-stu-id="a4b91-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="a4b91-108">記錄</span><span class="sxs-lookup"><span data-stu-id="a4b91-108">Logging</span></span>
* <span data-ttu-id="a4b91-109">組態</span><span class="sxs-lookup"><span data-stu-id="a4b91-109">Configuration</span></span>
* <span data-ttu-id="a4b91-110">`IHostedService` 實作</span><span class="sxs-lookup"><span data-stu-id="a4b91-110">`IHostedService` implementations</span></span>

<span data-ttu-id="a4b91-111">當主機啟動時，它會呼叫 <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> <xref:Microsoft.Extensions.Hosting.IHostedService> 服務容器的託管服務集合中註冊的每個執行。</span><span class="sxs-lookup"><span data-stu-id="a4b91-111">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="a4b91-112">在 Web 應用程式中，其中一個 `IHostedService` 實作是一種 Web 服務，負責啟動 [HTTP 伺服器實作](xref:fundamentals/index#servers)。</span><span class="sxs-lookup"><span data-stu-id="a4b91-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="a4b91-113">在單一物件中包含所有應用程式相互依存資源的主要理由便是生命週期管理：控制應用程式的啟動及順利關機。</span><span class="sxs-lookup"><span data-stu-id="a4b91-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="a4b91-114">設定主機</span><span class="sxs-lookup"><span data-stu-id="a4b91-114">Set up a host</span></span>

<span data-ttu-id="a4b91-115">主機通常由 `Program` 類別的程式碼來設定、建置並執行。</span><span class="sxs-lookup"><span data-stu-id="a4b91-115">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="a4b91-116">`Main` 方法：</span><span class="sxs-lookup"><span data-stu-id="a4b91-116">The `Main` method:</span></span>

* <span data-ttu-id="a4b91-117">呼叫 `CreateHostBuilder` 方法來建立及設定建立器物件。</span><span class="sxs-lookup"><span data-stu-id="a4b91-117">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="a4b91-118">在建立器物件上呼叫 `Build` 和 `Run` 方法。</span><span class="sxs-lookup"><span data-stu-id="a4b91-118">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="a4b91-119">ASP.NET Core web 範本會產生下列程式碼來建立主機：</span><span class="sxs-lookup"><span data-stu-id="a4b91-119">The ASP.NET Core web templates generate the following code to create a host:</span></span>

```csharp
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
            });
}
```

<span data-ttu-id="a4b91-120">下列程式碼會建立非 HTTP 工作負載，並將其 `IHostedService` 執行新增至 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="a4b91-120">The following code creates a non-HTTP workload with an `IHostedService` implementation added to the DI container.</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

<span data-ttu-id="a4b91-121">針對 HTTP 工作負載，`Main` 方法相同，但 `CreateHostBuilder` 會呼叫 `ConfigureWebHostDefaults`：</span><span class="sxs-lookup"><span data-stu-id="a4b91-121">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="a4b91-122">如果應用程式使用 Entity Framework Core，請勿變更 `CreateHostBuilder` 方法的名稱或簽章。</span><span class="sxs-lookup"><span data-stu-id="a4b91-122">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="a4b91-123">[Entity Framework Core 工具](/ef/core/miscellaneous/cli/)預期找到 `CreateHostBuilder` 方法，其在不執行應用程式的情況下設定主機。</span><span class="sxs-lookup"><span data-stu-id="a4b91-123">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="a4b91-124">如需詳細資訊，請參閱[設計階段 DbContext 建立](/ef/core/miscellaneous/cli/dbcontext-creation)。</span><span class="sxs-lookup"><span data-stu-id="a4b91-124">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="a4b91-125">預設建立器設定</span><span class="sxs-lookup"><span data-stu-id="a4b91-125">Default builder settings</span></span>

<span data-ttu-id="a4b91-126"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> 方法：</span><span class="sxs-lookup"><span data-stu-id="a4b91-126">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="a4b91-127">將 [內容根目錄](xref:fundamentals/index#content-root) 設定為所傳回的路徑 <xref:System.IO.Directory.GetCurrentDirectory*> 。</span><span class="sxs-lookup"><span data-stu-id="a4b91-127">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="a4b91-128">從下列項目載入主機組態：</span><span class="sxs-lookup"><span data-stu-id="a4b91-128">Loads host configuration from:</span></span>
  * <span data-ttu-id="a4b91-129">前面加上的環境變數 `DOTNET_` 。</span><span class="sxs-lookup"><span data-stu-id="a4b91-129">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="a4b91-130">命令列引數。</span><span class="sxs-lookup"><span data-stu-id="a4b91-130">Command-line arguments.</span></span>
* <span data-ttu-id="a4b91-131">從下列項目載入應用程式組態：</span><span class="sxs-lookup"><span data-stu-id="a4b91-131">Loads app configuration from:</span></span>
  * <span data-ttu-id="a4b91-132">*appsettings.js開啟*。</span><span class="sxs-lookup"><span data-stu-id="a4b91-132">*appsettings.json*.</span></span>
  * <span data-ttu-id="a4b91-133">*appsettings.{Environment}.json*</span><span class="sxs-lookup"><span data-stu-id="a4b91-133">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="a4b91-134">應用程式在 `Development` 環境中執行時的[祕密管理員](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="a4b91-134">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="a4b91-135">環境變數。</span><span class="sxs-lookup"><span data-stu-id="a4b91-135">Environment variables.</span></span>
  * <span data-ttu-id="a4b91-136">命令列引數。</span><span class="sxs-lookup"><span data-stu-id="a4b91-136">Command-line arguments.</span></span>
* <span data-ttu-id="a4b91-137">新增下列[記錄](xref:fundamentals/logging/index)提供者：</span><span class="sxs-lookup"><span data-stu-id="a4b91-137">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="a4b91-138">主控台</span><span class="sxs-lookup"><span data-stu-id="a4b91-138">Console</span></span>
  * <span data-ttu-id="a4b91-139">偵錯</span><span class="sxs-lookup"><span data-stu-id="a4b91-139">Debug</span></span>
  * <span data-ttu-id="a4b91-140">EventSource</span><span class="sxs-lookup"><span data-stu-id="a4b91-140">EventSource</span></span>
  * <span data-ttu-id="a4b91-141">EventLog (僅當在 Windows 上執行時)</span><span class="sxs-lookup"><span data-stu-id="a4b91-141">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="a4b91-142">環境為開發時，會啟用[範圍驗證](xref:fundamentals/dependency-injection#scope-validation)和[相依性驗證](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild)。</span><span class="sxs-lookup"><span data-stu-id="a4b91-142">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="a4b91-143">`ConfigureWebHostDefaults` 方法：</span><span class="sxs-lookup"><span data-stu-id="a4b91-143">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="a4b91-144">從前面加上的環境變數載入主機設定 `ASPNETCORE_` 。</span><span class="sxs-lookup"><span data-stu-id="a4b91-144">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="a4b91-145">會將 [Kestrel](xref:fundamentals/servers/kestrel) 伺服器設為網頁伺服器，並使用應用程式的主機組態提供者進行設定。</span><span class="sxs-lookup"><span data-stu-id="a4b91-145">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="a4b91-146">如需 Kestrel 伺服器的預設選項，請參閱 <xref:fundamentals/servers/kestrel#kestrel-options>。</span><span class="sxs-lookup"><span data-stu-id="a4b91-146">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="a4b91-147">新增[主機篩選中介軟體](xref:fundamentals/servers/kestrel#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="a4b91-147">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="a4b91-148">如果等於，則新增 [轉送的標頭中介軟體](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) `ASPNETCORE_FORWARDEDHEADERS_ENABLED` `true` 。</span><span class="sxs-lookup"><span data-stu-id="a4b91-148">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="a4b91-149">啟用 IIS 整合。</span><span class="sxs-lookup"><span data-stu-id="a4b91-149">Enables IIS integration.</span></span> <span data-ttu-id="a4b91-150">如需 IIS 預設選項，請參閱 <xref:host-and-deploy/iis/index#iis-options>。</span><span class="sxs-lookup"><span data-stu-id="a4b91-150">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="a4b91-151">本文稍後的[＜設定所有應用程式類型＞](#settings-for-all-app-types)和[＜Web 應用程式設定＞](#settings-for-web-apps)章節，將說明如何覆寫預設的建立器設定。</span><span class="sxs-lookup"><span data-stu-id="a4b91-151">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="a4b91-152">架構提供的服務</span><span class="sxs-lookup"><span data-stu-id="a4b91-152">Framework-provided services</span></span>

<span data-ttu-id="a4b91-153">下列服務會自動註冊：</span><span class="sxs-lookup"><span data-stu-id="a4b91-153">The following services are registered automatically:</span></span>

* [<span data-ttu-id="a4b91-154">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="a4b91-154">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="a4b91-155">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="a4b91-155">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="a4b91-156">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="a4b91-156">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="a4b91-157">如需架構所提供之服務的詳細資訊，請參閱 <xref:fundamentals/dependency-injection#framework-provided-services> 。</span><span class="sxs-lookup"><span data-stu-id="a4b91-157">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="a4b91-158">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="a4b91-158">IHostApplicationLifetime</span></span>

<span data-ttu-id="a4b91-159">將 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (先前稱為 `IApplicationLifetime`) 服務插入任何類別來處理啟動後和順利關機工作。</span><span class="sxs-lookup"><span data-stu-id="a4b91-159">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="a4b91-160">介面上的三個屬性是用於註冊應用程式啟動和應用程式關閉事件處理程序方法的取消語彙基元。</span><span class="sxs-lookup"><span data-stu-id="a4b91-160">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="a4b91-161">介面也包括 `StopApplication` 方法。</span><span class="sxs-lookup"><span data-stu-id="a4b91-161">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="a4b91-162">下列範例是 `IHostedService` 註冊事件的實作為 `IHostApplicationLifetime` ：</span><span class="sxs-lookup"><span data-stu-id="a4b91-162">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="a4b91-163">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="a4b91-163">IHostLifetime</span></span>

<span data-ttu-id="a4b91-164"><xref:Microsoft.Extensions.Hosting.IHostLifetime> 實作會控制主機啟動及停止的時機。</span><span class="sxs-lookup"><span data-stu-id="a4b91-164">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="a4b91-165">會使用最後一個註冊的實作。</span><span class="sxs-lookup"><span data-stu-id="a4b91-165">The last implementation registered is used.</span></span>

<span data-ttu-id="a4b91-166">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` 是預設的 `IHostLifetime` 實作。</span><span class="sxs-lookup"><span data-stu-id="a4b91-166">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="a4b91-167">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="a4b91-167">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="a4b91-168">接聽<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT 或 SIGTERM，並呼叫 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> 以啟動關機程式。</span><span class="sxs-lookup"><span data-stu-id="a4b91-168">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="a4b91-169">會解除封鎖 [RunAsync](#runasync) 和 [WaitForShutdownAsync](#waitforshutdownasync) 等延伸模組。</span><span class="sxs-lookup"><span data-stu-id="a4b91-169">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="a4b91-170">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="a4b91-170">IHostEnvironment</span></span>

<span data-ttu-id="a4b91-171">將服務插入至 <xref:Microsoft.Extensions.Hosting.IHostEnvironment> 類別，以取得下列設定的相關資訊：</span><span class="sxs-lookup"><span data-stu-id="a4b91-171">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="a4b91-172">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="a4b91-172">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="a4b91-173">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="a4b91-173">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="a4b91-174">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="a4b91-174">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="a4b91-175">Web apps 會執行 `IWebHostEnvironment` 介面，此介面會繼承 `IHostEnvironment` 並新增 [WebRootPath](#webroot)。</span><span class="sxs-lookup"><span data-stu-id="a4b91-175">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="a4b91-176">主機組態</span><span class="sxs-lookup"><span data-stu-id="a4b91-176">Host configuration</span></span>

<span data-ttu-id="a4b91-177">主機組態用於 <xref:Microsoft.Extensions.Hosting.IHostEnvironment> 實作的屬性。</span><span class="sxs-lookup"><span data-stu-id="a4b91-177">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="a4b91-178">主機組態位於 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 內的 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration)。</span><span class="sxs-lookup"><span data-stu-id="a4b91-178">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="a4b91-179">在 `ConfigureAppConfiguration` 之後，應用程式組態會取代 `HostBuilderContext.Configuration`。</span><span class="sxs-lookup"><span data-stu-id="a4b91-179">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="a4b91-180">若要新增主機組態，請呼叫 `IHostBuilder` 上的 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>。</span><span class="sxs-lookup"><span data-stu-id="a4b91-180">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="a4b91-181">`ConfigureHostConfiguration` 可以多次呼叫，其結果是累加的。</span><span class="sxs-lookup"><span data-stu-id="a4b91-181">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="a4b91-182">主機會使用指定索引鍵上最後設定值的任何選項。</span><span class="sxs-lookup"><span data-stu-id="a4b91-182">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="a4b91-183">包含前置詞 `DOTNET_` 和命令列引數的環境變數提供者 `CreateDefaultBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="a4b91-183">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="a4b91-184">針對 Web 應用程式，會新增具有前置詞 `ASPNETCORE_` 的環境變數提供者。</span><span class="sxs-lookup"><span data-stu-id="a4b91-184">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="a4b91-185">讀取環境變數時，就會移除前置詞。</span><span class="sxs-lookup"><span data-stu-id="a4b91-185">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="a4b91-186">例如，`ASPNETCORE_ENVIRONMENT` 的環境變數值會變成 `environment` 索引鍵的主機組態值。</span><span class="sxs-lookup"><span data-stu-id="a4b91-186">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="a4b91-187">下列範例會建立主機組態：</span><span class="sxs-lookup"><span data-stu-id="a4b91-187">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="a4b91-188">應用程式設定</span><span class="sxs-lookup"><span data-stu-id="a4b91-188">App configuration</span></span>

<span data-ttu-id="a4b91-189">應用程式組態的建立方式是在 `IHostBuilder` 上呼叫 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>。</span><span class="sxs-lookup"><span data-stu-id="a4b91-189">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="a4b91-190">`ConfigureAppConfiguration` 可以多次呼叫，其結果是累加的。</span><span class="sxs-lookup"><span data-stu-id="a4b91-190">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="a4b91-191">應用程式會使用指定索引鍵上最後設定值的任何選項。</span><span class="sxs-lookup"><span data-stu-id="a4b91-191">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="a4b91-192">由 `ConfigureAppConfiguration` 建立的組態位於 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*)，可用於後續作業並用作 DI 中的服務。</span><span class="sxs-lookup"><span data-stu-id="a4b91-192">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="a4b91-193">主機組態也會新增至應用程式組態。</span><span class="sxs-lookup"><span data-stu-id="a4b91-193">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="a4b91-194">如需詳細資訊，請參閱 [ASP.NET Core 中的組態](xref:fundamentals/configuration/index#configureappconfiguration)。</span><span class="sxs-lookup"><span data-stu-id="a4b91-194">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="a4b91-195">所有應用程式類型的設定</span><span class="sxs-lookup"><span data-stu-id="a4b91-195">Settings for all app types</span></span>

<span data-ttu-id="a4b91-196">本節列出適用於 HTTP 和非 HTTP 工作負載的主機設定。</span><span class="sxs-lookup"><span data-stu-id="a4b91-196">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="a4b91-197">根據預設，用來設定這些設定的環境變數可以具有 `DOTNET_` 或 `ASPNETCORE_` 前置詞。</span><span class="sxs-lookup"><span data-stu-id="a4b91-197">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="a4b91-198">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="a4b91-198">ApplicationName</span></span>

<span data-ttu-id="a4b91-199">[IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) 屬性是在主機建構期間從主機組態當中設定。</span><span class="sxs-lookup"><span data-stu-id="a4b91-199">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="a4b91-200">機**碼**：`applicationName`</span><span class="sxs-lookup"><span data-stu-id="a4b91-200">**Key**: `applicationName`</span></span>  
<span data-ttu-id="a4b91-201">**輸入**： `string`</span><span class="sxs-lookup"><span data-stu-id="a4b91-201">**Type**: `string`</span></span>  
<span data-ttu-id="a4b91-202">**預設值**：包含應用程式進入點的元件名稱。</span><span class="sxs-lookup"><span data-stu-id="a4b91-202">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="a4b91-203">**環境變數**： `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="a4b91-203">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="a4b91-204">若要設定此值，請使用環境變數。</span><span class="sxs-lookup"><span data-stu-id="a4b91-204">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="a4b91-205">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="a4b91-205">ContentRoot</span></span>

<span data-ttu-id="a4b91-206">[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) 屬性會決定主機從哪裡開始搜尋內容檔案。</span><span class="sxs-lookup"><span data-stu-id="a4b91-206">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="a4b91-207">如果路徑不存在，就無法啟動主機。</span><span class="sxs-lookup"><span data-stu-id="a4b91-207">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="a4b91-208">機**碼**：`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="a4b91-208">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="a4b91-209">**輸入**： `string`</span><span class="sxs-lookup"><span data-stu-id="a4b91-209">**Type**: `string`</span></span>  
<span data-ttu-id="a4b91-210">**預設值**：應用程式元件所在的資料夾。</span><span class="sxs-lookup"><span data-stu-id="a4b91-210">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="a4b91-211">**環境變數**： `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="a4b91-211">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="a4b91-212">若要設定此值，請使用環境變數或呼叫 `IHostBuilder` 上的 `UseContentRoot`：</span><span class="sxs-lookup"><span data-stu-id="a4b91-212">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="a4b91-213">如需詳細資訊，請參閱</span><span class="sxs-lookup"><span data-stu-id="a4b91-213">For more information, see:</span></span>

* [<span data-ttu-id="a4b91-214">基本概念：內容根目錄</span><span class="sxs-lookup"><span data-stu-id="a4b91-214">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="a4b91-215">WebRoot</span><span class="sxs-lookup"><span data-stu-id="a4b91-215">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="a4b91-216">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="a4b91-216">EnvironmentName</span></span>

<span data-ttu-id="a4b91-217">[IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) 屬性可以設為任何值。</span><span class="sxs-lookup"><span data-stu-id="a4b91-217">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="a4b91-218">架構定義的值包括 `Development`、`Staging` 和 `Production`。</span><span class="sxs-lookup"><span data-stu-id="a4b91-218">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="a4b91-219">值不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="a4b91-219">Values aren't case-sensitive.</span></span>

<span data-ttu-id="a4b91-220">機**碼**：`environment`</span><span class="sxs-lookup"><span data-stu-id="a4b91-220">**Key**: `environment`</span></span>  
<span data-ttu-id="a4b91-221">**輸入**： `string`</span><span class="sxs-lookup"><span data-stu-id="a4b91-221">**Type**: `string`</span></span>  
<span data-ttu-id="a4b91-222">**預設值**： `Production`</span><span class="sxs-lookup"><span data-stu-id="a4b91-222">**Default**: `Production`</span></span>  
<span data-ttu-id="a4b91-223">**環境變數**： `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="a4b91-223">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="a4b91-224">若要設定此值，請使用環境變數或呼叫 `IHostBuilder` 上的 `UseEnvironment`：</span><span class="sxs-lookup"><span data-stu-id="a4b91-224">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="a4b91-225">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="a4b91-225">ShutdownTimeout</span></span>

<span data-ttu-id="a4b91-226">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) 會設定 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 的逾時。</span><span class="sxs-lookup"><span data-stu-id="a4b91-226">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="a4b91-227">預設值是五秒鐘。</span><span class="sxs-lookup"><span data-stu-id="a4b91-227">The default value is five seconds.</span></span>  <span data-ttu-id="a4b91-228">在逾時期間，主機會：</span><span class="sxs-lookup"><span data-stu-id="a4b91-228">During the timeout period, the host:</span></span>

* <span data-ttu-id="a4b91-229">觸發 [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping)。</span><span class="sxs-lookup"><span data-stu-id="a4b91-229">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="a4b91-230">嘗試停止託管的服務，並記錄無法停止之服務的錯誤。</span><span class="sxs-lookup"><span data-stu-id="a4b91-230">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="a4b91-231">如果在所有的託管服務停止之前逾時期限已到期，則應用程式關閉時，會停止任何剩餘的作用中服務。</span><span class="sxs-lookup"><span data-stu-id="a4b91-231">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="a4b91-232">即使服務尚未完成處理也會停止。</span><span class="sxs-lookup"><span data-stu-id="a4b91-232">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="a4b91-233">如果服務需要更多時間才能停止，請增加逾時。</span><span class="sxs-lookup"><span data-stu-id="a4b91-233">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="a4b91-234">機**碼**：`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="a4b91-234">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="a4b91-235">**輸入**： `int`</span><span class="sxs-lookup"><span data-stu-id="a4b91-235">**Type**: `int`</span></span>  
<span data-ttu-id="a4b91-236">**預設值**：5秒</span><span class="sxs-lookup"><span data-stu-id="a4b91-236">**Default**: 5 seconds</span></span>  
<span data-ttu-id="a4b91-237">**環境變數**： `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="a4b91-237">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="a4b91-238">若要設定此值，請使用環境變數或設定 `HostOptions`。</span><span class="sxs-lookup"><span data-stu-id="a4b91-238">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="a4b91-239">下列範例將逾時設為 20 秒：</span><span class="sxs-lookup"><span data-stu-id="a4b91-239">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="a4b91-240">變更時停用應用程式設定重載</span><span class="sxs-lookup"><span data-stu-id="a4b91-240">Disable app configuration reload on change</span></span>

<span data-ttu-id="a4b91-241">依 [預設](xref:fundamentals/configuration/index#default)， *appsettings.json* 和 *appsettings. {環境}。* 當檔案變更時，會重載 json。</span><span class="sxs-lookup"><span data-stu-id="a4b91-241">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="a4b91-242">若要在 ASP.NET Core 5.0 或更新版本中停用這個重載行為，請將索引 `hostBuilder:reloadConfigOnChange` 鍵設定為 `false` 。</span><span class="sxs-lookup"><span data-stu-id="a4b91-242">To disable this reload behavior in ASP.NET Core 5.0 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="a4b91-243">機**碼**：`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="a4b91-243">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="a4b91-244">**類型**： `bool` (`true` 或 `1`) </span><span class="sxs-lookup"><span data-stu-id="a4b91-244">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="a4b91-245">**預設值**： `true`</span><span class="sxs-lookup"><span data-stu-id="a4b91-245">**Default**: `true`</span></span>  
<span data-ttu-id="a4b91-246">**命令列引數**： `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="a4b91-246">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="a4b91-247">**環境變數**： `<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="a4b91-247">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="a4b91-248">冒號 (`:`) 分隔符號不適用於所有平臺上的環境變數階層式索引鍵。</span><span class="sxs-lookup"><span data-stu-id="a4b91-248">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="a4b91-249">如需詳細資訊，請參閱 [環境變數](xref:fundamentals/configuration/index#environment-variables)。</span><span class="sxs-lookup"><span data-stu-id="a4b91-249">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="a4b91-250">Web 應用程式的設定</span><span class="sxs-lookup"><span data-stu-id="a4b91-250">Settings for web apps</span></span>

<span data-ttu-id="a4b91-251">某些主機設定僅適用於 HTTP 工作負載。</span><span class="sxs-lookup"><span data-stu-id="a4b91-251">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="a4b91-252">根據預設，用來設定這些設定的環境變數可以具有 `DOTNET_` 或 `ASPNETCORE_` 前置詞。</span><span class="sxs-lookup"><span data-stu-id="a4b91-252">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="a4b91-253">`IWebHostBuilder` 上的擴充方法適用於這些設定。</span><span class="sxs-lookup"><span data-stu-id="a4b91-253">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="a4b91-254">示範如何呼叫擴充方法的程式碼範例假設 `webBuilder` 是 `IWebHostBuilder` 的執行個體，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="a4b91-254">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="a4b91-255">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="a4b91-255">CaptureStartupErrors</span></span>

<span data-ttu-id="a4b91-256">當它為 `false` 時，啟動期間發生的錯誤會導致主機結束。</span><span class="sxs-lookup"><span data-stu-id="a4b91-256">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="a4b91-257">當它為 `true` 時，主機會擷取啟動期間的例外狀況，並嘗試啟動伺服器。</span><span class="sxs-lookup"><span data-stu-id="a4b91-257">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="a4b91-258">機**碼**：`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="a4b91-258">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="a4b91-259">**類型**： `bool` (`true` 或 `1`) </span><span class="sxs-lookup"><span data-stu-id="a4b91-259">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="a4b91-260">**預設值**：預設為 `false`，除非應用程式執行時在 IIS 背後有 Kestrel，此時預設值即為 `true`。</span><span class="sxs-lookup"><span data-stu-id="a4b91-260">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="a4b91-261">**環境變數**： `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="a4b91-261">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="a4b91-262">若要設定此值，請使用組態或呼叫 `CaptureStartupErrors`：</span><span class="sxs-lookup"><span data-stu-id="a4b91-262">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="a4b91-263">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="a4b91-263">DetailedErrors</span></span>

<span data-ttu-id="a4b91-264">啟用時 (或當環境為 `Development` 時)，應用程式會擷取詳細錯誤。</span><span class="sxs-lookup"><span data-stu-id="a4b91-264">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="a4b91-265">機**碼**：`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="a4b91-265">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="a4b91-266">**類型**： `bool` (`true` 或 `1`) </span><span class="sxs-lookup"><span data-stu-id="a4b91-266">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="a4b91-267">**預設值**： `false`</span><span class="sxs-lookup"><span data-stu-id="a4b91-267">**Default**: `false`</span></span>  
<span data-ttu-id="a4b91-268">**環境變數**： `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="a4b91-268">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="a4b91-269">若要設定此值，請使用組態或呼叫 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="a4b91-269">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="a4b91-270">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="a4b91-270">HostingStartupAssemblies</span></span>

<span data-ttu-id="a4b91-271">在啟動時載入的裝載啟動組件字串，以分號分隔。</span><span class="sxs-lookup"><span data-stu-id="a4b91-271">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="a4b91-272">雖然設定值會預設為空字串，但裝載啟動組件一律會包含應用程式的組件。</span><span class="sxs-lookup"><span data-stu-id="a4b91-272">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="a4b91-273">提供裝載啟動組件時，它們會新增至應用程式的組件，以便在應用程式在啟動時建置其通用服務時載入。</span><span class="sxs-lookup"><span data-stu-id="a4b91-273">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="a4b91-274">機**碼**：`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="a4b91-274">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="a4b91-275">**輸入**： `string`</span><span class="sxs-lookup"><span data-stu-id="a4b91-275">**Type**: `string`</span></span>  
<span data-ttu-id="a4b91-276">**預設值**：空字串</span><span class="sxs-lookup"><span data-stu-id="a4b91-276">**Default**: Empty string</span></span>  
<span data-ttu-id="a4b91-277">**環境變數**： `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="a4b91-277">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="a4b91-278">若要設定此值，請使用組態或呼叫 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="a4b91-278">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="a4b91-279">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="a4b91-279">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="a4b91-280">在啟動時排除以分號分隔的裝載啟動組件字串。</span><span class="sxs-lookup"><span data-stu-id="a4b91-280">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="a4b91-281">機**碼**：`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="a4b91-281">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="a4b91-282">**輸入**： `string`</span><span class="sxs-lookup"><span data-stu-id="a4b91-282">**Type**: `string`</span></span>  
<span data-ttu-id="a4b91-283">**預設值**：空字串</span><span class="sxs-lookup"><span data-stu-id="a4b91-283">**Default**: Empty string</span></span>  
<span data-ttu-id="a4b91-284">**環境變數**： `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="a4b91-284">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="a4b91-285">若要設定此值，請使用組態或呼叫 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="a4b91-285">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="a4b91-286">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="a4b91-286">HTTPS_Port</span></span>

<span data-ttu-id="a4b91-287">HTTPS 重新導向連接埠。</span><span class="sxs-lookup"><span data-stu-id="a4b91-287">The HTTPS redirect port.</span></span> <span data-ttu-id="a4b91-288">用於[強制 HTTPS](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="a4b91-288">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="a4b91-289">機**碼**：`https_port`</span><span class="sxs-lookup"><span data-stu-id="a4b91-289">**Key**: `https_port`</span></span>  
<span data-ttu-id="a4b91-290">**輸入**： `string`</span><span class="sxs-lookup"><span data-stu-id="a4b91-290">**Type**: `string`</span></span>  
<span data-ttu-id="a4b91-291">**預設**值：未設定預設值。</span><span class="sxs-lookup"><span data-stu-id="a4b91-291">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="a4b91-292">**環境變數**： `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="a4b91-292">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="a4b91-293">若要設定此值，請使用組態或呼叫 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="a4b91-293">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="a4b91-294">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="a4b91-294">PreferHostingUrls</span></span>

<span data-ttu-id="a4b91-295">指出主機是否應接聽使用設定的 Url， `IWebHostBuilder` 而不是使用執行時所設定的 url `IServer` 。</span><span class="sxs-lookup"><span data-stu-id="a4b91-295">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="a4b91-296">機**碼**：`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="a4b91-296">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="a4b91-297">**類型**： `bool` (`true` 或 `1`) </span><span class="sxs-lookup"><span data-stu-id="a4b91-297">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="a4b91-298">**預設值**： `true`</span><span class="sxs-lookup"><span data-stu-id="a4b91-298">**Default**: `true`</span></span>  
<span data-ttu-id="a4b91-299">**環境變數**： `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="a4b91-299">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="a4b91-300">若要設定此值，請使用環境變數或呼叫 `PreferHostingUrls`：</span><span class="sxs-lookup"><span data-stu-id="a4b91-300">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="a4b91-301">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="a4b91-301">PreventHostingStartup</span></span>

<span data-ttu-id="a4b91-302">可防止自動載入裝載啟動組件，包括應用程式組件所設定的裝載啟動組件。</span><span class="sxs-lookup"><span data-stu-id="a4b91-302">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="a4b91-303">如需詳細資訊，請參閱<xref:fundamentals/configuration/platform-specific-configuration>。</span><span class="sxs-lookup"><span data-stu-id="a4b91-303">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="a4b91-304">機**碼**：`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="a4b91-304">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="a4b91-305">**類型**： `bool` (`true` 或 `1`) </span><span class="sxs-lookup"><span data-stu-id="a4b91-305">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="a4b91-306">**預設值**： `false`</span><span class="sxs-lookup"><span data-stu-id="a4b91-306">**Default**: `false`</span></span>  
<span data-ttu-id="a4b91-307">**環境變數**： `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="a4b91-307">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="a4b91-308">若要設定此值，請使用環境變數或呼叫 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="a4b91-308">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="a4b91-309">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="a4b91-309">StartupAssembly</span></span>

<span data-ttu-id="a4b91-310">要搜尋 `Startup` 類別的組件。</span><span class="sxs-lookup"><span data-stu-id="a4b91-310">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="a4b91-311">機**碼**：`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="a4b91-311">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="a4b91-312">**輸入**： `string`</span><span class="sxs-lookup"><span data-stu-id="a4b91-312">**Type**: `string`</span></span>  
<span data-ttu-id="a4b91-313">**預設值**：應用程式的組件</span><span class="sxs-lookup"><span data-stu-id="a4b91-313">**Default**: The app's assembly</span></span>  
<span data-ttu-id="a4b91-314">**環境變數**： `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="a4b91-314">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="a4b91-315">若要設定此值，請使用環境變數或呼叫 `UseStartup`。</span><span class="sxs-lookup"><span data-stu-id="a4b91-315">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="a4b91-316">`UseStartup` 可以採用組件名稱 (`string`) 或類型 (`TStartup`)。</span><span class="sxs-lookup"><span data-stu-id="a4b91-316">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="a4b91-317">如果呼叫多個 `UseStartup` 方法，最後一個將會優先。</span><span class="sxs-lookup"><span data-stu-id="a4b91-317">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="a4b91-318">URL</span><span class="sxs-lookup"><span data-stu-id="a4b91-318">URLs</span></span>

<span data-ttu-id="a4b91-319">以分號分隔的 IP 位址或主機位址，包含伺服器應接聽要求的連接埠和通訊協定。</span><span class="sxs-lookup"><span data-stu-id="a4b91-319">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="a4b91-320">例如： `http://localhost:123` 。</span><span class="sxs-lookup"><span data-stu-id="a4b91-320">For example, `http://localhost:123`.</span></span> <span data-ttu-id="a4b91-321">使用 "\*"，表示伺服器應接聽任何 IP 位址或主機名稱上的要求，並使用指定的連接埠和通訊協定 (例如，`http://*:5000`)。</span><span class="sxs-lookup"><span data-stu-id="a4b91-321">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="a4b91-322">通訊協定 (`http://` 或 `https://`) 必須包含在每個 URL 中。</span><span class="sxs-lookup"><span data-stu-id="a4b91-322">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="a4b91-323">支援的格式會依伺服器而有所不同。</span><span class="sxs-lookup"><span data-stu-id="a4b91-323">Supported formats vary among servers.</span></span>

<span data-ttu-id="a4b91-324">機**碼**：`urls`</span><span class="sxs-lookup"><span data-stu-id="a4b91-324">**Key**: `urls`</span></span>  
<span data-ttu-id="a4b91-325">**輸入**： `string`</span><span class="sxs-lookup"><span data-stu-id="a4b91-325">**Type**: `string`</span></span>  
<span data-ttu-id="a4b91-326">**預設值**： `http://localhost:5000` 和 `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="a4b91-326">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="a4b91-327">**環境變數**： `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="a4b91-327">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="a4b91-328">若要設定此值，請使用環境變數或呼叫 `UseUrls`：</span><span class="sxs-lookup"><span data-stu-id="a4b91-328">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="a4b91-329">Kestrel 有它自己的端點設定 API。</span><span class="sxs-lookup"><span data-stu-id="a4b91-329">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="a4b91-330">如需詳細資訊，請參閱<xref:fundamentals/servers/kestrel#endpoint-configuration>。</span><span class="sxs-lookup"><span data-stu-id="a4b91-330">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="a4b91-331">WebRoot</span><span class="sxs-lookup"><span data-stu-id="a4b91-331">WebRoot</span></span>

<span data-ttu-id="a4b91-332">[IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath)屬性會決定應用程式靜態資產的相對路徑。</span><span class="sxs-lookup"><span data-stu-id="a4b91-332">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="a4b91-333">如果路徑不存在，則會使用無作業檔案提供者。</span><span class="sxs-lookup"><span data-stu-id="a4b91-333">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="a4b91-334">機**碼**：`webroot`</span><span class="sxs-lookup"><span data-stu-id="a4b91-334">**Key**: `webroot`</span></span>  
<span data-ttu-id="a4b91-335">**輸入**： `string`</span><span class="sxs-lookup"><span data-stu-id="a4b91-335">**Type**: `string`</span></span>  
<span data-ttu-id="a4b91-336">**預設**值：預設值為 `wwwroot` 。</span><span class="sxs-lookup"><span data-stu-id="a4b91-336">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="a4b91-337">*{Content root}/wwwroot*的路徑必須存在。</span><span class="sxs-lookup"><span data-stu-id="a4b91-337">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="a4b91-338">**環境變數**： `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="a4b91-338">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="a4b91-339">若要設定此值，請使用環境變數或呼叫 `IWebHostBuilder` 上的 `UseWebRoot`：</span><span class="sxs-lookup"><span data-stu-id="a4b91-339">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="a4b91-340">如需詳細資訊，請參閱</span><span class="sxs-lookup"><span data-stu-id="a4b91-340">For more information, see:</span></span>

* [<span data-ttu-id="a4b91-341">基本概念： Web 根目錄</span><span class="sxs-lookup"><span data-stu-id="a4b91-341">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="a4b91-342">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="a4b91-342">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="a4b91-343">管理主機存留期</span><span class="sxs-lookup"><span data-stu-id="a4b91-343">Manage the host lifetime</span></span>

<span data-ttu-id="a4b91-344">在建置的 <xref:Microsoft.Extensions.Hosting.IHost> 實作上呼叫方法來啟動和停止應用程式。</span><span class="sxs-lookup"><span data-stu-id="a4b91-344">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="a4b91-345">這些方法會影響所有在服務容器中註冊的 <xref:Microsoft.Extensions.Hosting.IHostedService> 實作。</span><span class="sxs-lookup"><span data-stu-id="a4b91-345">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="a4b91-346">執行</span><span class="sxs-lookup"><span data-stu-id="a4b91-346">Run</span></span>

<span data-ttu-id="a4b91-347"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> 會執行應用程式並封鎖呼叫執行緒，直到主機關閉為止。</span><span class="sxs-lookup"><span data-stu-id="a4b91-347"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="a4b91-348">RunAsync</span><span class="sxs-lookup"><span data-stu-id="a4b91-348">RunAsync</span></span>

<span data-ttu-id="a4b91-349"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> 會執行應用程式，並傳回觸發取消語彙基元或關機時所完成的 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="a4b91-349"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="a4b91-350">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="a4b91-350">RunConsoleAsync</span></span>

<span data-ttu-id="a4b91-351"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>啟用主控台支援、建立並啟動主機，以及等候<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT 或 SIGTERM 關閉。</span><span class="sxs-lookup"><span data-stu-id="a4b91-351"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="a4b91-352">開始</span><span class="sxs-lookup"><span data-stu-id="a4b91-352">Start</span></span>

<span data-ttu-id="a4b91-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> 會同步啟動主機。</span><span class="sxs-lookup"><span data-stu-id="a4b91-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="a4b91-354">StartAsync</span><span class="sxs-lookup"><span data-stu-id="a4b91-354">StartAsync</span></span>

<span data-ttu-id="a4b91-355"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> 會啟動主機，並傳回觸發取消語彙基元或關機時所完成的 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="a4b91-355"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="a4b91-356"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> 在 `StartAsync` 開始時呼叫，並等到完成後再繼續進行。</span><span class="sxs-lookup"><span data-stu-id="a4b91-356"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="a4b91-357">這可用來將啟動延遲到外部事件發出訊號為止。</span><span class="sxs-lookup"><span data-stu-id="a4b91-357">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="a4b91-358">StopAsync</span><span class="sxs-lookup"><span data-stu-id="a4b91-358">StopAsync</span></span>

<span data-ttu-id="a4b91-359"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> 會嘗試在提供的逾時內停止主機。</span><span class="sxs-lookup"><span data-stu-id="a4b91-359"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="a4b91-360">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="a4b91-360">WaitForShutdown</span></span>

<span data-ttu-id="a4b91-361"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>封鎖呼叫的執行緒，直到 IHostLifetime 觸發關機（例如 via <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT 或 SIGTERM）。</span><span class="sxs-lookup"><span data-stu-id="a4b91-361"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="a4b91-362">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="a4b91-362">WaitForShutdownAsync</span></span>

<span data-ttu-id="a4b91-363"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> 會傳回透過指定語彙基元觸發關機時所完成的 <xref:System.Threading.Tasks.Task>，並呼叫 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>。</span><span class="sxs-lookup"><span data-stu-id="a4b91-363"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="a4b91-364">外部控制</span><span class="sxs-lookup"><span data-stu-id="a4b91-364">External control</span></span>

<span data-ttu-id="a4b91-365">主機存留期直接控制可以使用可從外部呼叫的方法來達成：</span><span class="sxs-lookup"><span data-stu-id="a4b91-365">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="a4b91-366">ASP.NET Core 範本會建立 .NET Core 泛型主機 (<xref:Microsoft.Extensions.Hosting.HostBuilder>) 。</span><span class="sxs-lookup"><span data-stu-id="a4b91-366">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

## <a name="host-definition"></a><span data-ttu-id="a4b91-367">主機定義</span><span class="sxs-lookup"><span data-stu-id="a4b91-367">Host definition</span></span>

<span data-ttu-id="a4b91-368">「主機」\*\* 是封裝所有應用程式資源的物件，例如：</span><span class="sxs-lookup"><span data-stu-id="a4b91-368">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="a4b91-369">相依性插入 (DI)</span><span class="sxs-lookup"><span data-stu-id="a4b91-369">Dependency injection (DI)</span></span>
* <span data-ttu-id="a4b91-370">記錄</span><span class="sxs-lookup"><span data-stu-id="a4b91-370">Logging</span></span>
* <span data-ttu-id="a4b91-371">組態</span><span class="sxs-lookup"><span data-stu-id="a4b91-371">Configuration</span></span>
* <span data-ttu-id="a4b91-372">`IHostedService` 實作</span><span class="sxs-lookup"><span data-stu-id="a4b91-372">`IHostedService` implementations</span></span>

<span data-ttu-id="a4b91-373">當主機啟動時，它會呼叫 <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> <xref:Microsoft.Extensions.Hosting.IHostedService> 服務容器的託管服務集合中註冊的每個執行。</span><span class="sxs-lookup"><span data-stu-id="a4b91-373">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="a4b91-374">在 Web 應用程式中，其中一個 `IHostedService` 實作是一種 Web 服務，負責啟動 [HTTP 伺服器實作](xref:fundamentals/index#servers)。</span><span class="sxs-lookup"><span data-stu-id="a4b91-374">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="a4b91-375">在單一物件中包含所有應用程式相互依存資源的主要理由便是生命週期管理：控制應用程式的啟動及順利關機。</span><span class="sxs-lookup"><span data-stu-id="a4b91-375">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="a4b91-376">設定主機</span><span class="sxs-lookup"><span data-stu-id="a4b91-376">Set up a host</span></span>

<span data-ttu-id="a4b91-377">主機通常由 `Program` 類別的程式碼來設定、建置並執行。</span><span class="sxs-lookup"><span data-stu-id="a4b91-377">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="a4b91-378">`Main` 方法：</span><span class="sxs-lookup"><span data-stu-id="a4b91-378">The `Main` method:</span></span>

* <span data-ttu-id="a4b91-379">呼叫 `CreateHostBuilder` 方法來建立及設定建立器物件。</span><span class="sxs-lookup"><span data-stu-id="a4b91-379">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="a4b91-380">在建立器物件上呼叫 `Build` 和 `Run` 方法。</span><span class="sxs-lookup"><span data-stu-id="a4b91-380">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="a4b91-381">ASP.NET Core web 範本會產生下列程式碼來建立泛型主機：</span><span class="sxs-lookup"><span data-stu-id="a4b91-381">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

```csharp
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
            });
}
```

<span data-ttu-id="a4b91-382">下列程式碼會使用非 HTTP 工作負載來建立泛型主機。</span><span class="sxs-lookup"><span data-stu-id="a4b91-382">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="a4b91-383">`IHostedService`執行會新增至 DI 容器：</span><span class="sxs-lookup"><span data-stu-id="a4b91-383">The `IHostedService` implementation is added to the DI container:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

<span data-ttu-id="a4b91-384">針對 HTTP 工作負載，`Main` 方法相同，但 `CreateHostBuilder` 會呼叫 `ConfigureWebHostDefaults`：</span><span class="sxs-lookup"><span data-stu-id="a4b91-384">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="a4b91-385">上述程式碼是由 ASP.NET Core 範本所產生。</span><span class="sxs-lookup"><span data-stu-id="a4b91-385">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="a4b91-386">如果應用程式使用 Entity Framework Core，請勿變更 `CreateHostBuilder` 方法的名稱或簽章。</span><span class="sxs-lookup"><span data-stu-id="a4b91-386">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="a4b91-387">[Entity Framework Core 工具](/ef/core/miscellaneous/cli/)預期找到 `CreateHostBuilder` 方法，其在不執行應用程式的情況下設定主機。</span><span class="sxs-lookup"><span data-stu-id="a4b91-387">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="a4b91-388">如需詳細資訊，請參閱[設計階段 DbContext 建立](/ef/core/miscellaneous/cli/dbcontext-creation)。</span><span class="sxs-lookup"><span data-stu-id="a4b91-388">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="a4b91-389">預設建立器設定</span><span class="sxs-lookup"><span data-stu-id="a4b91-389">Default builder settings</span></span>

<span data-ttu-id="a4b91-390"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> 方法：</span><span class="sxs-lookup"><span data-stu-id="a4b91-390">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> method:</span></span>

* <span data-ttu-id="a4b91-391">將 [內容根目錄](xref:fundamentals/index#content-root) 設定為所傳回的路徑 <xref:System.IO.Directory.GetCurrentDirectory%2A> 。</span><span class="sxs-lookup"><span data-stu-id="a4b91-391">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory%2A>.</span></span>
* <span data-ttu-id="a4b91-392">從下列項目載入主機組態：</span><span class="sxs-lookup"><span data-stu-id="a4b91-392">Loads host configuration from:</span></span>
  * <span data-ttu-id="a4b91-393">前面加上的環境變數 `DOTNET_` 。</span><span class="sxs-lookup"><span data-stu-id="a4b91-393">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="a4b91-394">命令列引數。</span><span class="sxs-lookup"><span data-stu-id="a4b91-394">Command-line arguments.</span></span>
* <span data-ttu-id="a4b91-395">從下列項目載入應用程式組態：</span><span class="sxs-lookup"><span data-stu-id="a4b91-395">Loads app configuration from:</span></span>
  * <span data-ttu-id="a4b91-396">*appsettings.js開啟*。</span><span class="sxs-lookup"><span data-stu-id="a4b91-396">*appsettings.json*.</span></span>
  * <span data-ttu-id="a4b91-397">*appsettings.{Environment}.json*</span><span class="sxs-lookup"><span data-stu-id="a4b91-397">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="a4b91-398">應用程式在 `Development` 環境中執行時的[祕密管理員](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="a4b91-398">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="a4b91-399">環境變數。</span><span class="sxs-lookup"><span data-stu-id="a4b91-399">Environment variables.</span></span>
  * <span data-ttu-id="a4b91-400">命令列引數。</span><span class="sxs-lookup"><span data-stu-id="a4b91-400">Command-line arguments.</span></span>
* <span data-ttu-id="a4b91-401">新增下列[記錄](xref:fundamentals/logging/index)提供者：</span><span class="sxs-lookup"><span data-stu-id="a4b91-401">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="a4b91-402">主控台</span><span class="sxs-lookup"><span data-stu-id="a4b91-402">Console</span></span>
  * <span data-ttu-id="a4b91-403">偵錯</span><span class="sxs-lookup"><span data-stu-id="a4b91-403">Debug</span></span>
  * <span data-ttu-id="a4b91-404">EventSource</span><span class="sxs-lookup"><span data-stu-id="a4b91-404">EventSource</span></span>
  * <span data-ttu-id="a4b91-405">EventLog (僅當在 Windows 上執行時)</span><span class="sxs-lookup"><span data-stu-id="a4b91-405">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="a4b91-406">環境為開發時，會啟用[範圍驗證](xref:fundamentals/dependency-injection#scope-validation)和[相依性驗證](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild)。</span><span class="sxs-lookup"><span data-stu-id="a4b91-406">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="a4b91-407">`ConfigureWebHostDefaults` 方法：</span><span class="sxs-lookup"><span data-stu-id="a4b91-407">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="a4b91-408">從前面加上的環境變數載入主機設定 `ASPNETCORE_` 。</span><span class="sxs-lookup"><span data-stu-id="a4b91-408">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="a4b91-409">會將 [Kestrel](xref:fundamentals/servers/kestrel) 伺服器設為網頁伺服器，並使用應用程式的主機組態提供者進行設定。</span><span class="sxs-lookup"><span data-stu-id="a4b91-409">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="a4b91-410">如需 Kestrel 伺服器的預設選項，請參閱 <xref:fundamentals/servers/kestrel#kestrel-options>。</span><span class="sxs-lookup"><span data-stu-id="a4b91-410">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="a4b91-411">新增[主機篩選中介軟體](xref:fundamentals/servers/kestrel#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="a4b91-411">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="a4b91-412">如果等於，則新增 [轉送的標頭中介軟體](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) `ASPNETCORE_FORWARDEDHEADERS_ENABLED` `true` 。</span><span class="sxs-lookup"><span data-stu-id="a4b91-412">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="a4b91-413">啟用 IIS 整合。</span><span class="sxs-lookup"><span data-stu-id="a4b91-413">Enables IIS integration.</span></span> <span data-ttu-id="a4b91-414">如需 IIS 預設選項，請參閱 <xref:host-and-deploy/iis/index#iis-options>。</span><span class="sxs-lookup"><span data-stu-id="a4b91-414">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="a4b91-415">本文稍後的[＜設定所有應用程式類型＞](#settings-for-all-app-types)和[＜Web 應用程式設定＞](#settings-for-web-apps)章節，將說明如何覆寫預設的建立器設定。</span><span class="sxs-lookup"><span data-stu-id="a4b91-415">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="a4b91-416">架構提供的服務</span><span class="sxs-lookup"><span data-stu-id="a4b91-416">Framework-provided services</span></span>

<span data-ttu-id="a4b91-417">下列服務會自動註冊：</span><span class="sxs-lookup"><span data-stu-id="a4b91-417">The following services are registered automatically:</span></span>

* [<span data-ttu-id="a4b91-418">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="a4b91-418">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="a4b91-419">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="a4b91-419">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="a4b91-420">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="a4b91-420">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="a4b91-421">如需架構所提供之服務的詳細資訊，請參閱 <xref:fundamentals/dependency-injection#framework-provided-services> 。</span><span class="sxs-lookup"><span data-stu-id="a4b91-421">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="a4b91-422">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="a4b91-422">IHostApplicationLifetime</span></span>

<span data-ttu-id="a4b91-423">將 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (先前稱為 `IApplicationLifetime`) 服務插入任何類別來處理啟動後和順利關機工作。</span><span class="sxs-lookup"><span data-stu-id="a4b91-423">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="a4b91-424">介面上的三個屬性是用於註冊應用程式啟動和應用程式關閉事件處理程序方法的取消語彙基元。</span><span class="sxs-lookup"><span data-stu-id="a4b91-424">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="a4b91-425">介面也包括 `StopApplication` 方法。</span><span class="sxs-lookup"><span data-stu-id="a4b91-425">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="a4b91-426">下列範例是 `IHostedService` 註冊事件的實作為 `IHostApplicationLifetime` ：</span><span class="sxs-lookup"><span data-stu-id="a4b91-426">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="a4b91-427">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="a4b91-427">IHostLifetime</span></span>

<span data-ttu-id="a4b91-428"><xref:Microsoft.Extensions.Hosting.IHostLifetime> 實作會控制主機啟動及停止的時機。</span><span class="sxs-lookup"><span data-stu-id="a4b91-428">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="a4b91-429">會使用最後一個註冊的實作。</span><span class="sxs-lookup"><span data-stu-id="a4b91-429">The last implementation registered is used.</span></span>

<span data-ttu-id="a4b91-430">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` 是預設的 `IHostLifetime` 實作。</span><span class="sxs-lookup"><span data-stu-id="a4b91-430">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="a4b91-431">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="a4b91-431">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="a4b91-432">接聽<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT 或 SIGTERM，並呼叫 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> 以啟動關機程式。</span><span class="sxs-lookup"><span data-stu-id="a4b91-432">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> to start the shutdown process.</span></span>
* <span data-ttu-id="a4b91-433">會解除封鎖 [RunAsync](#runasync) 和 [WaitForShutdownAsync](#waitforshutdownasync) 等延伸模組。</span><span class="sxs-lookup"><span data-stu-id="a4b91-433">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="a4b91-434">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="a4b91-434">IHostEnvironment</span></span>

<span data-ttu-id="a4b91-435">將服務插入至 <xref:Microsoft.Extensions.Hosting.IHostEnvironment> 類別，以取得下列設定的相關資訊：</span><span class="sxs-lookup"><span data-stu-id="a4b91-435">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="a4b91-436">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="a4b91-436">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="a4b91-437">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="a4b91-437">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="a4b91-438">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="a4b91-438">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="a4b91-439">Web apps 會執行 `IWebHostEnvironment` 介面，此介面會繼承 `IHostEnvironment` 並新增 [WebRootPath](#webroot)。</span><span class="sxs-lookup"><span data-stu-id="a4b91-439">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="a4b91-440">主機組態</span><span class="sxs-lookup"><span data-stu-id="a4b91-440">Host configuration</span></span>

<span data-ttu-id="a4b91-441">主機組態用於 <xref:Microsoft.Extensions.Hosting.IHostEnvironment> 實作的屬性。</span><span class="sxs-lookup"><span data-stu-id="a4b91-441">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="a4b91-442">主機組態位於 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> 內的 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration)。</span><span class="sxs-lookup"><span data-stu-id="a4b91-442">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span></span> <span data-ttu-id="a4b91-443">在 `ConfigureAppConfiguration` 之後，應用程式組態會取代 `HostBuilderContext.Configuration`。</span><span class="sxs-lookup"><span data-stu-id="a4b91-443">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="a4b91-444">若要新增主機組態，請呼叫 `IHostBuilder` 上的 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A>。</span><span class="sxs-lookup"><span data-stu-id="a4b91-444">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> on `IHostBuilder`.</span></span> <span data-ttu-id="a4b91-445">`ConfigureHostConfiguration` 可以多次呼叫，其結果是累加的。</span><span class="sxs-lookup"><span data-stu-id="a4b91-445">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="a4b91-446">主機會使用指定索引鍵上最後設定值的任何選項。</span><span class="sxs-lookup"><span data-stu-id="a4b91-446">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="a4b91-447">包含前置詞 `DOTNET_` 和命令列引數的環境變數提供者 `CreateDefaultBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="a4b91-447">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="a4b91-448">針對 Web 應用程式，會新增具有前置詞 `ASPNETCORE_` 的環境變數提供者。</span><span class="sxs-lookup"><span data-stu-id="a4b91-448">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="a4b91-449">讀取環境變數時，就會移除前置詞。</span><span class="sxs-lookup"><span data-stu-id="a4b91-449">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="a4b91-450">例如，`ASPNETCORE_ENVIRONMENT` 的環境變數值會變成 `environment` 索引鍵的主機組態值。</span><span class="sxs-lookup"><span data-stu-id="a4b91-450">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="a4b91-451">下列範例會建立主機組態：</span><span class="sxs-lookup"><span data-stu-id="a4b91-451">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="a4b91-452">應用程式設定</span><span class="sxs-lookup"><span data-stu-id="a4b91-452">App configuration</span></span>

<span data-ttu-id="a4b91-453">應用程式組態的建立方式是在 `IHostBuilder` 上呼叫 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>。</span><span class="sxs-lookup"><span data-stu-id="a4b91-453">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="a4b91-454">`ConfigureAppConfiguration` 可以多次呼叫，其結果是累加的。</span><span class="sxs-lookup"><span data-stu-id="a4b91-454">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="a4b91-455">應用程式會使用指定索引鍵上最後設定值的任何選項。</span><span class="sxs-lookup"><span data-stu-id="a4b91-455">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="a4b91-456">由 `ConfigureAppConfiguration` 建立的組態位於 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*)，可用於後續作業並用作 DI 中的服務。</span><span class="sxs-lookup"><span data-stu-id="a4b91-456">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="a4b91-457">主機組態也會新增至應用程式組態。</span><span class="sxs-lookup"><span data-stu-id="a4b91-457">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="a4b91-458">如需詳細資訊，請參閱 [ASP.NET Core 中的組態](xref:fundamentals/configuration/index#configureappconfiguration)。</span><span class="sxs-lookup"><span data-stu-id="a4b91-458">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="a4b91-459">所有應用程式類型的設定</span><span class="sxs-lookup"><span data-stu-id="a4b91-459">Settings for all app types</span></span>

<span data-ttu-id="a4b91-460">本節列出適用於 HTTP 和非 HTTP 工作負載的主機設定。</span><span class="sxs-lookup"><span data-stu-id="a4b91-460">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="a4b91-461">根據預設，用來設定這些設定的環境變數可以具有 `DOTNET_` 或 `ASPNETCORE_` 前置詞。</span><span class="sxs-lookup"><span data-stu-id="a4b91-461">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="a4b91-462">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="a4b91-462">ApplicationName</span></span>

<span data-ttu-id="a4b91-463">[IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) 屬性是在主機建構期間從主機組態當中設定。</span><span class="sxs-lookup"><span data-stu-id="a4b91-463">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="a4b91-464">機**碼**：`applicationName`</span><span class="sxs-lookup"><span data-stu-id="a4b91-464">**Key**: `applicationName`</span></span>  
<span data-ttu-id="a4b91-465">**輸入**： `string`</span><span class="sxs-lookup"><span data-stu-id="a4b91-465">**Type**: `string`</span></span>  
<span data-ttu-id="a4b91-466">**預設值**：包含應用程式進入點的元件名稱。</span><span class="sxs-lookup"><span data-stu-id="a4b91-466">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="a4b91-467">**環境變數**： `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="a4b91-467">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="a4b91-468">若要設定此值，請使用環境變數。</span><span class="sxs-lookup"><span data-stu-id="a4b91-468">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="a4b91-469">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="a4b91-469">ContentRoot</span></span>

<span data-ttu-id="a4b91-470">[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) 屬性會決定主機從哪裡開始搜尋內容檔案。</span><span class="sxs-lookup"><span data-stu-id="a4b91-470">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="a4b91-471">如果路徑不存在，就無法啟動主機。</span><span class="sxs-lookup"><span data-stu-id="a4b91-471">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="a4b91-472">機**碼**：`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="a4b91-472">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="a4b91-473">**輸入**： `string`</span><span class="sxs-lookup"><span data-stu-id="a4b91-473">**Type**: `string`</span></span>  
<span data-ttu-id="a4b91-474">**預設值**：應用程式元件所在的資料夾。</span><span class="sxs-lookup"><span data-stu-id="a4b91-474">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="a4b91-475">**環境變數**： `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="a4b91-475">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="a4b91-476">若要設定此值，請使用環境變數或呼叫 `IHostBuilder` 上的 `UseContentRoot`：</span><span class="sxs-lookup"><span data-stu-id="a4b91-476">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="a4b91-477">如需詳細資訊，請參閱</span><span class="sxs-lookup"><span data-stu-id="a4b91-477">For more information, see:</span></span>

* [<span data-ttu-id="a4b91-478">基本概念：內容根目錄</span><span class="sxs-lookup"><span data-stu-id="a4b91-478">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="a4b91-479">WebRoot</span><span class="sxs-lookup"><span data-stu-id="a4b91-479">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="a4b91-480">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="a4b91-480">EnvironmentName</span></span>

<span data-ttu-id="a4b91-481">[IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) 屬性可以設為任何值。</span><span class="sxs-lookup"><span data-stu-id="a4b91-481">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="a4b91-482">架構定義的值包括 `Development`、`Staging` 和 `Production`。</span><span class="sxs-lookup"><span data-stu-id="a4b91-482">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="a4b91-483">值不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="a4b91-483">Values aren't case-sensitive.</span></span>

<span data-ttu-id="a4b91-484">機**碼**：`environment`</span><span class="sxs-lookup"><span data-stu-id="a4b91-484">**Key**: `environment`</span></span>  
<span data-ttu-id="a4b91-485">**輸入**： `string`</span><span class="sxs-lookup"><span data-stu-id="a4b91-485">**Type**: `string`</span></span>  
<span data-ttu-id="a4b91-486">**預設值**： `Production`</span><span class="sxs-lookup"><span data-stu-id="a4b91-486">**Default**: `Production`</span></span>  
<span data-ttu-id="a4b91-487">**環境變數**： `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="a4b91-487">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="a4b91-488">若要設定此值，請使用環境變數或呼叫 `IHostBuilder` 上的 `UseEnvironment`：</span><span class="sxs-lookup"><span data-stu-id="a4b91-488">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="a4b91-489">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="a4b91-489">ShutdownTimeout</span></span>

<span data-ttu-id="a4b91-490">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) 會設定 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 的逾時。</span><span class="sxs-lookup"><span data-stu-id="a4b91-490">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="a4b91-491">預設值是五秒鐘。</span><span class="sxs-lookup"><span data-stu-id="a4b91-491">The default value is five seconds.</span></span>  <span data-ttu-id="a4b91-492">在逾時期間，主機會：</span><span class="sxs-lookup"><span data-stu-id="a4b91-492">During the timeout period, the host:</span></span>

* <span data-ttu-id="a4b91-493">觸發 [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping)。</span><span class="sxs-lookup"><span data-stu-id="a4b91-493">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="a4b91-494">嘗試停止託管的服務，並記錄無法停止之服務的錯誤。</span><span class="sxs-lookup"><span data-stu-id="a4b91-494">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="a4b91-495">如果在所有的託管服務停止之前逾時期限已到期，則應用程式關閉時，會停止任何剩餘的作用中服務。</span><span class="sxs-lookup"><span data-stu-id="a4b91-495">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="a4b91-496">即使服務尚未完成處理也會停止。</span><span class="sxs-lookup"><span data-stu-id="a4b91-496">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="a4b91-497">如果服務需要更多時間才能停止，請增加逾時。</span><span class="sxs-lookup"><span data-stu-id="a4b91-497">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="a4b91-498">機**碼**：`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="a4b91-498">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="a4b91-499">**輸入**： `int`</span><span class="sxs-lookup"><span data-stu-id="a4b91-499">**Type**: `int`</span></span>  
<span data-ttu-id="a4b91-500">**預設值**：5秒</span><span class="sxs-lookup"><span data-stu-id="a4b91-500">**Default**: 5 seconds</span></span>  
<span data-ttu-id="a4b91-501">**環境變數**： `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="a4b91-501">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="a4b91-502">若要設定此值，請使用環境變數或設定 `HostOptions`。</span><span class="sxs-lookup"><span data-stu-id="a4b91-502">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="a4b91-503">下列範例將逾時設為 20 秒：</span><span class="sxs-lookup"><span data-stu-id="a4b91-503">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="a4b91-504">Web 應用程式的設定</span><span class="sxs-lookup"><span data-stu-id="a4b91-504">Settings for web apps</span></span>

<span data-ttu-id="a4b91-505">某些主機設定僅適用於 HTTP 工作負載。</span><span class="sxs-lookup"><span data-stu-id="a4b91-505">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="a4b91-506">根據預設，用來設定這些設定的環境變數可以具有 `DOTNET_` 或 `ASPNETCORE_` 前置詞。</span><span class="sxs-lookup"><span data-stu-id="a4b91-506">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="a4b91-507">`IWebHostBuilder` 上的擴充方法適用於這些設定。</span><span class="sxs-lookup"><span data-stu-id="a4b91-507">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="a4b91-508">示範如何呼叫擴充方法的程式碼範例假設 `webBuilder` 是 `IWebHostBuilder` 的執行個體，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="a4b91-508">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="a4b91-509">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="a4b91-509">CaptureStartupErrors</span></span>

<span data-ttu-id="a4b91-510">當它為 `false` 時，啟動期間發生的錯誤會導致主機結束。</span><span class="sxs-lookup"><span data-stu-id="a4b91-510">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="a4b91-511">當它為 `true` 時，主機會擷取啟動期間的例外狀況，並嘗試啟動伺服器。</span><span class="sxs-lookup"><span data-stu-id="a4b91-511">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="a4b91-512">機**碼**：`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="a4b91-512">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="a4b91-513">**類型**： `bool` (`true` 或 `1`) </span><span class="sxs-lookup"><span data-stu-id="a4b91-513">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="a4b91-514">**預設值**：預設為 `false`，除非應用程式執行時在 IIS 背後有 Kestrel，此時預設值即為 `true`。</span><span class="sxs-lookup"><span data-stu-id="a4b91-514">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="a4b91-515">**環境變數**： `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="a4b91-515">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="a4b91-516">若要設定此值，請使用組態或呼叫 `CaptureStartupErrors`：</span><span class="sxs-lookup"><span data-stu-id="a4b91-516">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="a4b91-517">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="a4b91-517">DetailedErrors</span></span>

<span data-ttu-id="a4b91-518">啟用時 (或當環境為 `Development` 時)，應用程式會擷取詳細錯誤。</span><span class="sxs-lookup"><span data-stu-id="a4b91-518">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="a4b91-519">機**碼**：`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="a4b91-519">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="a4b91-520">**類型**： `bool` (`true` 或 `1`) </span><span class="sxs-lookup"><span data-stu-id="a4b91-520">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="a4b91-521">**預設值**： `false`</span><span class="sxs-lookup"><span data-stu-id="a4b91-521">**Default**: `false`</span></span>  
<span data-ttu-id="a4b91-522">**環境變數**： `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="a4b91-522">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="a4b91-523">若要設定此值，請使用組態或呼叫 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="a4b91-523">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="a4b91-524">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="a4b91-524">HostingStartupAssemblies</span></span>

<span data-ttu-id="a4b91-525">在啟動時載入的裝載啟動組件字串，以分號分隔。</span><span class="sxs-lookup"><span data-stu-id="a4b91-525">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="a4b91-526">雖然設定值會預設為空字串，但裝載啟動組件一律會包含應用程式的組件。</span><span class="sxs-lookup"><span data-stu-id="a4b91-526">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="a4b91-527">提供裝載啟動組件時，它們會新增至應用程式的組件，以便在應用程式在啟動時建置其通用服務時載入。</span><span class="sxs-lookup"><span data-stu-id="a4b91-527">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="a4b91-528">機**碼**：`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="a4b91-528">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="a4b91-529">**輸入**： `string`</span><span class="sxs-lookup"><span data-stu-id="a4b91-529">**Type**: `string`</span></span>  
<span data-ttu-id="a4b91-530">**預設值**：空字串</span><span class="sxs-lookup"><span data-stu-id="a4b91-530">**Default**: Empty string</span></span>  
<span data-ttu-id="a4b91-531">**環境變數**： `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="a4b91-531">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="a4b91-532">若要設定此值，請使用組態或呼叫 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="a4b91-532">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="a4b91-533">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="a4b91-533">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="a4b91-534">在啟動時排除以分號分隔的裝載啟動組件字串。</span><span class="sxs-lookup"><span data-stu-id="a4b91-534">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="a4b91-535">機**碼**：`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="a4b91-535">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="a4b91-536">**輸入**： `string`</span><span class="sxs-lookup"><span data-stu-id="a4b91-536">**Type**: `string`</span></span>  
<span data-ttu-id="a4b91-537">**預設值**：空字串</span><span class="sxs-lookup"><span data-stu-id="a4b91-537">**Default**: Empty string</span></span>  
<span data-ttu-id="a4b91-538">**環境變數**： `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="a4b91-538">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="a4b91-539">若要設定此值，請使用組態或呼叫 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="a4b91-539">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="a4b91-540">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="a4b91-540">HTTPS_Port</span></span>

<span data-ttu-id="a4b91-541">HTTPS 重新導向連接埠。</span><span class="sxs-lookup"><span data-stu-id="a4b91-541">The HTTPS redirect port.</span></span> <span data-ttu-id="a4b91-542">用於[強制 HTTPS](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="a4b91-542">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="a4b91-543">機**碼**：`https_port`</span><span class="sxs-lookup"><span data-stu-id="a4b91-543">**Key**: `https_port`</span></span>  
<span data-ttu-id="a4b91-544">**輸入**： `string`</span><span class="sxs-lookup"><span data-stu-id="a4b91-544">**Type**: `string`</span></span>  
<span data-ttu-id="a4b91-545">**預設**值：未設定預設值。</span><span class="sxs-lookup"><span data-stu-id="a4b91-545">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="a4b91-546">**環境變數**： `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="a4b91-546">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="a4b91-547">若要設定此值，請使用組態或呼叫 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="a4b91-547">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="a4b91-548">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="a4b91-548">PreferHostingUrls</span></span>

<span data-ttu-id="a4b91-549">指出主機是否應接聽使用設定的 Url， `IWebHostBuilder` 而不是使用執行時所設定的 url `IServer` 。</span><span class="sxs-lookup"><span data-stu-id="a4b91-549">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="a4b91-550">機**碼**：`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="a4b91-550">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="a4b91-551">**類型**： `bool` (`true` 或 `1`) </span><span class="sxs-lookup"><span data-stu-id="a4b91-551">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="a4b91-552">**預設值**： `true`</span><span class="sxs-lookup"><span data-stu-id="a4b91-552">**Default**: `true`</span></span>  
<span data-ttu-id="a4b91-553">**環境變數**： `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="a4b91-553">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="a4b91-554">若要設定此值，請使用環境變數或呼叫 `PreferHostingUrls`：</span><span class="sxs-lookup"><span data-stu-id="a4b91-554">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="a4b91-555">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="a4b91-555">PreventHostingStartup</span></span>

<span data-ttu-id="a4b91-556">可防止自動載入裝載啟動組件，包括應用程式組件所設定的裝載啟動組件。</span><span class="sxs-lookup"><span data-stu-id="a4b91-556">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="a4b91-557">如需詳細資訊，請參閱<xref:fundamentals/configuration/platform-specific-configuration>。</span><span class="sxs-lookup"><span data-stu-id="a4b91-557">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="a4b91-558">機**碼**：`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="a4b91-558">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="a4b91-559">**類型**： `bool` (`true` 或 `1`) </span><span class="sxs-lookup"><span data-stu-id="a4b91-559">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="a4b91-560">**預設值**： `false`</span><span class="sxs-lookup"><span data-stu-id="a4b91-560">**Default**: `false`</span></span>  
<span data-ttu-id="a4b91-561">**環境變數**： `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="a4b91-561">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="a4b91-562">若要設定此值，請使用環境變數或呼叫 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="a4b91-562">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="a4b91-563">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="a4b91-563">StartupAssembly</span></span>

<span data-ttu-id="a4b91-564">要搜尋 `Startup` 類別的組件。</span><span class="sxs-lookup"><span data-stu-id="a4b91-564">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="a4b91-565">機**碼**：`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="a4b91-565">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="a4b91-566">**輸入**： `string`</span><span class="sxs-lookup"><span data-stu-id="a4b91-566">**Type**: `string`</span></span>  
<span data-ttu-id="a4b91-567">**預設值**：應用程式的組件</span><span class="sxs-lookup"><span data-stu-id="a4b91-567">**Default**: The app's assembly</span></span>  
<span data-ttu-id="a4b91-568">**環境變數**： `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="a4b91-568">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="a4b91-569">若要設定此值，請使用環境變數或呼叫 `UseStartup`。</span><span class="sxs-lookup"><span data-stu-id="a4b91-569">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="a4b91-570">`UseStartup` 可以採用組件名稱 (`string`) 或類型 (`TStartup`)。</span><span class="sxs-lookup"><span data-stu-id="a4b91-570">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="a4b91-571">如果呼叫多個 `UseStartup` 方法，最後一個將會優先。</span><span class="sxs-lookup"><span data-stu-id="a4b91-571">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="a4b91-572">URL</span><span class="sxs-lookup"><span data-stu-id="a4b91-572">URLs</span></span>

<span data-ttu-id="a4b91-573">以分號分隔的 IP 位址或主機位址，包含伺服器應接聽要求的連接埠和通訊協定。</span><span class="sxs-lookup"><span data-stu-id="a4b91-573">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="a4b91-574">例如： `http://localhost:123` 。</span><span class="sxs-lookup"><span data-stu-id="a4b91-574">For example, `http://localhost:123`.</span></span> <span data-ttu-id="a4b91-575">使用 "\*"，表示伺服器應接聽任何 IP 位址或主機名稱上的要求，並使用指定的連接埠和通訊協定 (例如，`http://*:5000`)。</span><span class="sxs-lookup"><span data-stu-id="a4b91-575">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="a4b91-576">通訊協定 (`http://` 或 `https://`) 必須包含在每個 URL 中。</span><span class="sxs-lookup"><span data-stu-id="a4b91-576">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="a4b91-577">支援的格式會依伺服器而有所不同。</span><span class="sxs-lookup"><span data-stu-id="a4b91-577">Supported formats vary among servers.</span></span>

<span data-ttu-id="a4b91-578">機**碼**：`urls`</span><span class="sxs-lookup"><span data-stu-id="a4b91-578">**Key**: `urls`</span></span>  
<span data-ttu-id="a4b91-579">**輸入**： `string`</span><span class="sxs-lookup"><span data-stu-id="a4b91-579">**Type**: `string`</span></span>  
<span data-ttu-id="a4b91-580">**預設值**： `http://localhost:5000` 和 `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="a4b91-580">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="a4b91-581">**環境變數**： `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="a4b91-581">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="a4b91-582">若要設定此值，請使用環境變數或呼叫 `UseUrls`：</span><span class="sxs-lookup"><span data-stu-id="a4b91-582">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="a4b91-583">Kestrel 有它自己的端點設定 API。</span><span class="sxs-lookup"><span data-stu-id="a4b91-583">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="a4b91-584">如需詳細資訊，請參閱<xref:fundamentals/servers/kestrel#endpoint-configuration>。</span><span class="sxs-lookup"><span data-stu-id="a4b91-584">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="a4b91-585">WebRoot</span><span class="sxs-lookup"><span data-stu-id="a4b91-585">WebRoot</span></span>

<span data-ttu-id="a4b91-586">[IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath)屬性會決定應用程式靜態資產的相對路徑。</span><span class="sxs-lookup"><span data-stu-id="a4b91-586">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="a4b91-587">如果路徑不存在，則會使用無作業檔案提供者。</span><span class="sxs-lookup"><span data-stu-id="a4b91-587">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="a4b91-588">機**碼**：`webroot`</span><span class="sxs-lookup"><span data-stu-id="a4b91-588">**Key**: `webroot`</span></span>  
<span data-ttu-id="a4b91-589">**輸入**： `string`</span><span class="sxs-lookup"><span data-stu-id="a4b91-589">**Type**: `string`</span></span>  
<span data-ttu-id="a4b91-590">**預設**值：預設值為 `wwwroot` 。</span><span class="sxs-lookup"><span data-stu-id="a4b91-590">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="a4b91-591">*{Content root}/wwwroot*的路徑必須存在。</span><span class="sxs-lookup"><span data-stu-id="a4b91-591">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="a4b91-592">**環境變數**： `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="a4b91-592">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="a4b91-593">若要設定此值，請使用環境變數或呼叫 `IWebHostBuilder` 上的 `UseWebRoot`：</span><span class="sxs-lookup"><span data-stu-id="a4b91-593">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="a4b91-594">如需詳細資訊，請參閱</span><span class="sxs-lookup"><span data-stu-id="a4b91-594">For more information, see:</span></span>

* [<span data-ttu-id="a4b91-595">基本概念： Web 根目錄</span><span class="sxs-lookup"><span data-stu-id="a4b91-595">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="a4b91-596">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="a4b91-596">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="a4b91-597">管理主機存留期</span><span class="sxs-lookup"><span data-stu-id="a4b91-597">Manage the host lifetime</span></span>

<span data-ttu-id="a4b91-598">在建置的 <xref:Microsoft.Extensions.Hosting.IHost> 實作上呼叫方法來啟動和停止應用程式。</span><span class="sxs-lookup"><span data-stu-id="a4b91-598">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="a4b91-599">這些方法會影響所有在服務容器中註冊的 <xref:Microsoft.Extensions.Hosting.IHostedService> 實作。</span><span class="sxs-lookup"><span data-stu-id="a4b91-599">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="a4b91-600">執行</span><span class="sxs-lookup"><span data-stu-id="a4b91-600">Run</span></span>

<span data-ttu-id="a4b91-601"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> 會執行應用程式並封鎖呼叫執行緒，直到主機關閉為止。</span><span class="sxs-lookup"><span data-stu-id="a4b91-601"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="a4b91-602">RunAsync</span><span class="sxs-lookup"><span data-stu-id="a4b91-602">RunAsync</span></span>

<span data-ttu-id="a4b91-603"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> 會執行應用程式，並傳回觸發取消語彙基元或關機時所完成的 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="a4b91-603"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="a4b91-604">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="a4b91-604">RunConsoleAsync</span></span>

<span data-ttu-id="a4b91-605"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>啟用主控台支援、建立並啟動主機，以及等候<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT 或 SIGTERM 關閉。</span><span class="sxs-lookup"><span data-stu-id="a4b91-605"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="a4b91-606">開始</span><span class="sxs-lookup"><span data-stu-id="a4b91-606">Start</span></span>

<span data-ttu-id="a4b91-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> 會同步啟動主機。</span><span class="sxs-lookup"><span data-stu-id="a4b91-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="a4b91-608">StartAsync</span><span class="sxs-lookup"><span data-stu-id="a4b91-608">StartAsync</span></span>

<span data-ttu-id="a4b91-609"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> 會啟動主機，並傳回觸發取消語彙基元或關機時所完成的 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="a4b91-609"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="a4b91-610"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> 在 `StartAsync` 開始時呼叫，並等到完成後再繼續進行。</span><span class="sxs-lookup"><span data-stu-id="a4b91-610"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="a4b91-611">這可用來將啟動延遲到外部事件發出訊號為止。</span><span class="sxs-lookup"><span data-stu-id="a4b91-611">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="a4b91-612">StopAsync</span><span class="sxs-lookup"><span data-stu-id="a4b91-612">StopAsync</span></span>

<span data-ttu-id="a4b91-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> 會嘗試在提供的逾時內停止主機。</span><span class="sxs-lookup"><span data-stu-id="a4b91-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="a4b91-614">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="a4b91-614">WaitForShutdown</span></span>

<span data-ttu-id="a4b91-615"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>封鎖呼叫的執行緒，直到 IHostLifetime 觸發關機（例如 via <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT 或 SIGTERM）。</span><span class="sxs-lookup"><span data-stu-id="a4b91-615"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="a4b91-616">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="a4b91-616">WaitForShutdownAsync</span></span>

<span data-ttu-id="a4b91-617"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> 會傳回透過指定語彙基元觸發關機時所完成的 <xref:System.Threading.Tasks.Task>，並呼叫 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>。</span><span class="sxs-lookup"><span data-stu-id="a4b91-617"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="a4b91-618">外部控制</span><span class="sxs-lookup"><span data-stu-id="a4b91-618">External control</span></span>

<span data-ttu-id="a4b91-619">主機存留期直接控制可以使用可從外部呼叫的方法來達成：</span><span class="sxs-lookup"><span data-stu-id="a4b91-619">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a4b91-620">ASP.NET Core 應用程式會設定並啟動主機。</span><span class="sxs-lookup"><span data-stu-id="a4b91-620">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="a4b91-621">主機負責應用程式啟動和存留期管理。</span><span class="sxs-lookup"><span data-stu-id="a4b91-621">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="a4b91-622">本文所涵蓋的 ASP.NET Core 泛型主機 (<xref:Microsoft.Extensions.Hosting.HostBuilder>)，可用來不會處理 HTTP 要求的應用程式。</span><span class="sxs-lookup"><span data-stu-id="a4b91-622">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="a4b91-623">泛型主機的用途是將 HTTP 管線從 Web 主機 API 分離，以允許更廣泛的主機陣列案例。</span><span class="sxs-lookup"><span data-stu-id="a4b91-623">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="a4b91-624">傳訊、背景工作及其他以泛型主機為基礎的非 HTTP 工作負載都受益於跨領域的功能，例如設定、相依性插入 (DI) 和記錄。</span><span class="sxs-lookup"><span data-stu-id="a4b91-624">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="a4b91-625">泛型主機是 ASP.NET Core 2.1 的新功能，不適用於虛擬主機案例。</span><span class="sxs-lookup"><span data-stu-id="a4b91-625">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="a4b91-626">針對虛擬主機案例，請使用 [Web 主機](xref:fundamentals/host/web-host)。</span><span class="sxs-lookup"><span data-stu-id="a4b91-626">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="a4b91-627">泛型主機將在未來版本中取代 Web 主機，並成為 HTTP 和非 HTTP 案例中的主要主機 API。</span><span class="sxs-lookup"><span data-stu-id="a4b91-627">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="a4b91-628">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="a4b91-628">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="a4b91-629">在 [Visual Studio Code](https://code.visualstudio.com/) 中執行範例應用程式時，請使用「外部或整合式終端機」\*\*。</span><span class="sxs-lookup"><span data-stu-id="a4b91-629">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="a4b91-630">請勿在 `internalConsole` 中執行範例。</span><span class="sxs-lookup"><span data-stu-id="a4b91-630">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="a4b91-631">若要在 Visual Studio Code 中設定主控台：</span><span class="sxs-lookup"><span data-stu-id="a4b91-631">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="a4b91-632">開啟 *.vscode/launch.json* 檔案。</span><span class="sxs-lookup"><span data-stu-id="a4b91-632">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="a4b91-633">在 [.NET Core Launch (console)] \(.NET Core 啟動 (主控台)\)\*\*\*\* 設定中，尋找 **console** 項目。</span><span class="sxs-lookup"><span data-stu-id="a4b91-633">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="a4b91-634">將此值設定為 `externalTerminal` 或 `integratedTerminal`。</span><span class="sxs-lookup"><span data-stu-id="a4b91-634">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="a4b91-635">簡介</span><span class="sxs-lookup"><span data-stu-id="a4b91-635">Introduction</span></span>

<span data-ttu-id="a4b91-636">可使用位於 <xref:Microsoft.Extensions.Hosting> 命名空間的泛型主機程式庫，且該程式庫由 [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) 套件提供。</span><span class="sxs-lookup"><span data-stu-id="a4b91-636">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="a4b91-637">[Microsoft.AspNetCore.App 中繼套件](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 或更新版本) 包含 `Microsoft.Extensions.Hosting` 套件。</span><span class="sxs-lookup"><span data-stu-id="a4b91-637">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="a4b91-638"><xref:Microsoft.Extensions.Hosting.IHostedService> 是程式碼執行的進入點。</span><span class="sxs-lookup"><span data-stu-id="a4b91-638"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="a4b91-639">每個 <xref:Microsoft.Extensions.Hosting.IHostedService> 實作會依 [ConfigureServices 中的服務註冊](#configureservices)順序執行。</span><span class="sxs-lookup"><span data-stu-id="a4b91-639">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="a4b91-640">當主機啟動時，會在每個 <xref:Microsoft.Extensions.Hosting.IHostedService> 上呼叫 <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*>；當主機順利關閉時，則會依反向註冊順序呼叫 <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*>。</span><span class="sxs-lookup"><span data-stu-id="a4b91-640"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="a4b91-641">設定主機</span><span class="sxs-lookup"><span data-stu-id="a4b91-641">Set up a host</span></span>

<span data-ttu-id="a4b91-642"><xref:Microsoft.Extensions.Hosting.IHostBuilder> 是程式庫和應用程式用來初始化、建置及執行主機的主要元件：</span><span class="sxs-lookup"><span data-stu-id="a4b91-642"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="a4b91-643">選項</span><span class="sxs-lookup"><span data-stu-id="a4b91-643">Options</span></span>

<span data-ttu-id="a4b91-644"><xref:Microsoft.Extensions.Hosting.IHost> 的 <xref:Microsoft.Extensions.Hosting.HostOptions> 設定選項。</span><span class="sxs-lookup"><span data-stu-id="a4b91-644"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="a4b91-645">關機逾時</span><span class="sxs-lookup"><span data-stu-id="a4b91-645">Shutdown timeout</span></span>

<span data-ttu-id="a4b91-646"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> 會為 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 設定逾時。</span><span class="sxs-lookup"><span data-stu-id="a4b91-646"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="a4b91-647">預設值是五秒鐘。</span><span class="sxs-lookup"><span data-stu-id="a4b91-647">The default value is five seconds.</span></span>

<span data-ttu-id="a4b91-648">中的下列選項 `Program.Main` 設定會將預設的五秒關機超時時間增加為20秒：</span><span class="sxs-lookup"><span data-stu-id="a4b91-648">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

```csharp
var host = new HostBuilder()
    .ConfigureServices((hostContext, services) =>
    {
        services.Configure<HostOptions>(option =>
        {
            option.ShutdownTimeout = System.TimeSpan.FromSeconds(20);
        });
    })
    .Build();
```

## <a name="default-services"></a><span data-ttu-id="a4b91-649">預設服務</span><span class="sxs-lookup"><span data-stu-id="a4b91-649">Default services</span></span>

<span data-ttu-id="a4b91-650">下列服務會在主機初始化期間註冊：</span><span class="sxs-lookup"><span data-stu-id="a4b91-650">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="a4b91-651">[環境](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>) </span><span class="sxs-lookup"><span data-stu-id="a4b91-651">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="a4b91-652">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>) </span><span class="sxs-lookup"><span data-stu-id="a4b91-652">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="a4b91-653"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="a4b91-653"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="a4b91-654"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="a4b91-654"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="a4b91-655">[選項](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>) </span><span class="sxs-lookup"><span data-stu-id="a4b91-655">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="a4b91-656">[記錄](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>) </span><span class="sxs-lookup"><span data-stu-id="a4b91-656">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="a4b91-657">主機組態</span><span class="sxs-lookup"><span data-stu-id="a4b91-657">Host configuration</span></span>

<span data-ttu-id="a4b91-658">主機組態的建立方式：</span><span class="sxs-lookup"><span data-stu-id="a4b91-658">Host configuration is created by:</span></span>

* <span data-ttu-id="a4b91-659">呼叫 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 上的擴充方法，來設定[內容根目錄](#content-root)及[環境](#environment)。</span><span class="sxs-lookup"><span data-stu-id="a4b91-659">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="a4b91-660">在 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> 中從組態提供者讀取組態。</span><span class="sxs-lookup"><span data-stu-id="a4b91-660">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="a4b91-661">擴充方法</span><span class="sxs-lookup"><span data-stu-id="a4b91-661">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="a4b91-662">應用程式索引鍵 (名稱)</span><span class="sxs-lookup"><span data-stu-id="a4b91-662">Application key (name)</span></span>

<span data-ttu-id="a4b91-663">[IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) 屬性是在主機建構期間從主機設定當中設定。</span><span class="sxs-lookup"><span data-stu-id="a4b91-663">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="a4b91-664">若要明確設定該值，請使用 [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey)：</span><span class="sxs-lookup"><span data-stu-id="a4b91-664">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="a4b91-665">機**碼**：`applicationName`</span><span class="sxs-lookup"><span data-stu-id="a4b91-665">**Key**: `applicationName`</span></span>  
<span data-ttu-id="a4b91-666">**輸入**： `string`</span><span class="sxs-lookup"><span data-stu-id="a4b91-666">**Type**: `string`</span></span>  
<span data-ttu-id="a4b91-667">**預設**：包含應用程式進入點的組件名稱。</span><span class="sxs-lookup"><span data-stu-id="a4b91-667">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="a4b91-668">**設定使用**： `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="a4b91-668">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="a4b91-669">**環境變數**： `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` 是 [選擇性的，而且是使用者定義的](#configurehostconfiguration)) </span><span class="sxs-lookup"><span data-stu-id="a4b91-669">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="a4b91-670">內容根目錄</span><span class="sxs-lookup"><span data-stu-id="a4b91-670">Content root</span></span>

<span data-ttu-id="a4b91-671">此設定可決定主機開始搜尋內容檔案的位置。</span><span class="sxs-lookup"><span data-stu-id="a4b91-671">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="a4b91-672">機**碼**：`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="a4b91-672">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="a4b91-673">**輸入**： `string`</span><span class="sxs-lookup"><span data-stu-id="a4b91-673">**Type**: `string`</span></span>  
<span data-ttu-id="a4b91-674">**預設值**：預設為應用程式組件所在的資料夾。</span><span class="sxs-lookup"><span data-stu-id="a4b91-674">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="a4b91-675">**設定使用**： `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="a4b91-675">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="a4b91-676">**環境變數**： `<PREFIX_>CONTENTROOT` (`<PREFIX_>` 是 [選擇性的，而且是使用者定義的](#configurehostconfiguration)) </span><span class="sxs-lookup"><span data-stu-id="a4b91-676">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="a4b91-677">如果路徑不存在，就無法啟動主機。</span><span class="sxs-lookup"><span data-stu-id="a4b91-677">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="a4b91-678">如需詳細資訊，請參閱 [基本概念：內容根目錄](xref:fundamentals/index#content-root)。</span><span class="sxs-lookup"><span data-stu-id="a4b91-678">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="a4b91-679">環境</span><span class="sxs-lookup"><span data-stu-id="a4b91-679">Environment</span></span>

<span data-ttu-id="a4b91-680">設定應用程式的 [環境](xref:fundamentals/environments)。</span><span class="sxs-lookup"><span data-stu-id="a4b91-680">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="a4b91-681">機**碼**：`environment`</span><span class="sxs-lookup"><span data-stu-id="a4b91-681">**Key**: `environment`</span></span>  
<span data-ttu-id="a4b91-682">**輸入**： `string`</span><span class="sxs-lookup"><span data-stu-id="a4b91-682">**Type**: `string`</span></span>  
<span data-ttu-id="a4b91-683">**預設值**： `Production`</span><span class="sxs-lookup"><span data-stu-id="a4b91-683">**Default**: `Production`</span></span>  
<span data-ttu-id="a4b91-684">**設定使用**： `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="a4b91-684">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="a4b91-685">**環境變數**： `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` 是 [選擇性的，而且是使用者定義的](#configurehostconfiguration)) </span><span class="sxs-lookup"><span data-stu-id="a4b91-685">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="a4b91-686">環境可以設定為任何值。</span><span class="sxs-lookup"><span data-stu-id="a4b91-686">The environment can be set to any value.</span></span> <span data-ttu-id="a4b91-687">架構定義的值包括 `Development`、`Staging` 和 `Production`。</span><span class="sxs-lookup"><span data-stu-id="a4b91-687">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="a4b91-688">值不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="a4b91-688">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="a4b91-689">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="a4b91-689">ConfigureHostConfiguration</span></span>

<span data-ttu-id="a4b91-690"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> 會使用 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> 來建立主機的 <xref:Microsoft.Extensions.Configuration.IConfiguration>。</span><span class="sxs-lookup"><span data-stu-id="a4b91-690"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="a4b91-691">主機組態會用來將 <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> 初始化，使其在應用程式建置流程中可供使用。</span><span class="sxs-lookup"><span data-stu-id="a4b91-691">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="a4b91-692"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> 可以多次呼叫，其結果是累加的。</span><span class="sxs-lookup"><span data-stu-id="a4b91-692"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="a4b91-693">主機會使用指定索引鍵上最後設定值的任何選項。</span><span class="sxs-lookup"><span data-stu-id="a4b91-693">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="a4b91-694">預設不會包含任何提供者。</span><span class="sxs-lookup"><span data-stu-id="a4b91-694">No providers are included by default.</span></span> <span data-ttu-id="a4b91-695">您必須明確地指定應用程式在 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> 之中需要哪個組態提供者，包括：</span><span class="sxs-lookup"><span data-stu-id="a4b91-695">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="a4b91-696">檔案組態 (例如，來自 *hostsettings.json* 的檔案)。</span><span class="sxs-lookup"><span data-stu-id="a4b91-696">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="a4b91-697">環境變數組態。</span><span class="sxs-lookup"><span data-stu-id="a4b91-697">Environment variable configuration.</span></span>
* <span data-ttu-id="a4b91-698">命令列引數組態。</span><span class="sxs-lookup"><span data-stu-id="a4b91-698">Command-line argument configuration.</span></span>
* <span data-ttu-id="a4b91-699">任何其他必要的組態提供者。</span><span class="sxs-lookup"><span data-stu-id="a4b91-699">Any other required configuration providers.</span></span>

<span data-ttu-id="a4b91-700">使用之後呼叫[檔案組態提供者](xref:fundamentals/configuration/index#file-configuration-provider)的 `SetBasePath`，並透過指定應用程式基底路徑，就可使用主機的檔案設定。</span><span class="sxs-lookup"><span data-stu-id="a4b91-700">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="a4b91-701">範例應用程式會使用 JSON 檔案，*hostsettings.json*，並呼叫 <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 取用檔案的主機組態設定。</span><span class="sxs-lookup"><span data-stu-id="a4b91-701">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="a4b91-702">若要新增主機的[環境變數組態](xref:fundamentals/configuration/index#environment-variables-configuration-provider)，請在主機建立器上呼叫 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>。</span><span class="sxs-lookup"><span data-stu-id="a4b91-702">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="a4b91-703">`AddEnvironmentVariables` 可接受選擇性的使用者定義前置詞。</span><span class="sxs-lookup"><span data-stu-id="a4b91-703">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="a4b91-704">範例應用程式會使用前置詞 `PREFIX_`。</span><span class="sxs-lookup"><span data-stu-id="a4b91-704">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="a4b91-705">讀取環境變數時，就會移除前置詞。</span><span class="sxs-lookup"><span data-stu-id="a4b91-705">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="a4b91-706">在設定範例應用程式的主機時，`PREFIX_ENVIRONMENT` 的環境變數值會變成 `environment` 索引鍵的主機組態值。</span><span class="sxs-lookup"><span data-stu-id="a4b91-706">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="a4b91-707">在開發期間使用 [Visual Studio](https://visualstudio.microsoft.com) 或以 `dotnet run` 執行應用程式時，可能會在 *Properties/launchSettings.json* 檔案中設定環境變數。</span><span class="sxs-lookup"><span data-stu-id="a4b91-707">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="a4b91-708">在 [Visual Studio Code](https://code.visualstudio.com/) 中，可以在開發期間於 *.vscode/launch.json* 檔案中設定環境變數。</span><span class="sxs-lookup"><span data-stu-id="a4b91-708">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="a4b91-709">如需詳細資訊，請參閱<xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="a4b91-709">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="a4b91-710">[命令列組態](xref:fundamentals/configuration/index#command-line-configuration-provider)可透過呼叫 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 新增。</span><span class="sxs-lookup"><span data-stu-id="a4b91-710">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="a4b91-711">命令列組態會在最後新增，以便命令列引數覆寫由先前組態提供者提供的組態。</span><span class="sxs-lookup"><span data-stu-id="a4b91-711">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="a4b91-712">*hostsettings.json*：</span><span class="sxs-lookup"><span data-stu-id="a4b91-712">*hostsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="a4b91-713">您可以使用 [applicationName](#application-key-name) 和 [contentRoot](#content-root) 索引鍵來提供其他組態。</span><span class="sxs-lookup"><span data-stu-id="a4b91-713">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="a4b91-714">使用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> 的 `HostBuilder` 組態範例：</span><span class="sxs-lookup"><span data-stu-id="a4b91-714">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="a4b91-715">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="a4b91-715">ConfigureAppConfiguration</span></span>

<span data-ttu-id="a4b91-716">應用程式組態的建立方式是在 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 實作上呼叫 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>。</span><span class="sxs-lookup"><span data-stu-id="a4b91-716">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="a4b91-717"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 會使用 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> 來建立應用程式的 <xref:Microsoft.Extensions.Configuration.IConfiguration>。</span><span class="sxs-lookup"><span data-stu-id="a4b91-717"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="a4b91-718"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 可以多次呼叫，其結果是累加的。</span><span class="sxs-lookup"><span data-stu-id="a4b91-718"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="a4b91-719">應用程式會使用指定索引鍵上最後設定值的任何選項。</span><span class="sxs-lookup"><span data-stu-id="a4b91-719">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="a4b91-720">您可以從後續作業的 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) 及 <xref:Microsoft.Extensions.Hosting.IHost.Services*> 中存取 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 所建立的組態。</span><span class="sxs-lookup"><span data-stu-id="a4b91-720">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="a4b91-721">應用程式組態會自動接收由 [ConfigureHostConfiguration](#configurehostconfiguration) 提供的主機組態。</span><span class="sxs-lookup"><span data-stu-id="a4b91-721">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="a4b91-722">使用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 的應用程式組態範例：</span><span class="sxs-lookup"><span data-stu-id="a4b91-722">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="a4b91-723">*appsettings.js*：</span><span class="sxs-lookup"><span data-stu-id="a4b91-723">*appsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="a4b91-724">*appsettings.Development.json*：</span><span class="sxs-lookup"><span data-stu-id="a4b91-724">*appsettings.Development.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="a4b91-725">*appsettings.Production.json*：</span><span class="sxs-lookup"><span data-stu-id="a4b91-725">*appsettings.Production.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="a4b91-726">若要將設定檔移至輸出目錄，請在專案檔中將設定檔指定為 [MSBuild 專案項目](/visualstudio/msbuild/common-msbuild-project-items)。</span><span class="sxs-lookup"><span data-stu-id="a4b91-726">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="a4b91-727">範例應用程式使用下列 `<Content>` 項目，移動其 JSON 應用程式設定檔和 *hostsettings.json*：</span><span class="sxs-lookup"><span data-stu-id="a4b91-727">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="a4b91-728">組態擴充方法 (例如 <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 和 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>) 需要其他的 NuGet 套件，例如 [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) \(英文\) 和[Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="a4b91-728">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="a4b91-729">除非應用程式使用 [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app)，否則，除了核心 [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) \(英文\) 套件，還必須將這些套件新增至專案。</span><span class="sxs-lookup"><span data-stu-id="a4b91-729">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="a4b91-730">如需詳細資訊，請參閱<xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="a4b91-730">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="a4b91-731">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="a4b91-731">ConfigureServices</span></span>

<span data-ttu-id="a4b91-732"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> 會將服務新增至應用程式的[相依性插入](xref:fundamentals/dependency-injection)容器。</span><span class="sxs-lookup"><span data-stu-id="a4b91-732"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="a4b91-733"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> 可以多次呼叫，其結果是累加的。</span><span class="sxs-lookup"><span data-stu-id="a4b91-733"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="a4b91-734">託管服務是具有背景工作邏輯的類別，可實作 <xref:Microsoft.Extensions.Hosting.IHostedService> 介面。</span><span class="sxs-lookup"><span data-stu-id="a4b91-734">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="a4b91-735">如需詳細資訊，請參閱<xref:fundamentals/host/hosted-services>。</span><span class="sxs-lookup"><span data-stu-id="a4b91-735">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="a4b91-736">[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/)使用 `AddHostedService` 擴充方法，將存留期事件 `LifetimeEventsHostedService` 和計時背景工作 `TimedHostedService` 等服務新增至應用程式：</span><span class="sxs-lookup"><span data-stu-id="a4b91-736">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="a4b91-737">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="a4b91-737">ConfigureLogging</span></span>

<span data-ttu-id="a4b91-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> 會新增委派以設定提供的 <xref:Microsoft.Extensions.Logging.ILoggingBuilder>。</span><span class="sxs-lookup"><span data-stu-id="a4b91-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="a4b91-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> 可以多次呼叫，其結果是累加的。</span><span class="sxs-lookup"><span data-stu-id="a4b91-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="a4b91-740">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="a4b91-740">UseConsoleLifetime</span></span>

<span data-ttu-id="a4b91-741"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>接聽<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT 或 SIGTERM，並呼叫 <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> 以啟動關機程式。</span><span class="sxs-lookup"><span data-stu-id="a4b91-741"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="a4b91-742"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> 解除封鎖 [RunAsync](#runasync) 和 [>waitforshutdownasync](#waitforshutdownasync)等擴充功能。</span><span class="sxs-lookup"><span data-stu-id="a4b91-742"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="a4b91-743">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` 會預先註冊為預設存留期實作。</span><span class="sxs-lookup"><span data-stu-id="a4b91-743">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="a4b91-744">系統會使用最後一個註冊的存留期。</span><span class="sxs-lookup"><span data-stu-id="a4b91-744">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="a4b91-745">容器設定</span><span class="sxs-lookup"><span data-stu-id="a4b91-745">Container configuration</span></span>

<span data-ttu-id="a4b91-746">為支援插入其他容器，主機可以接受 <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>。</span><span class="sxs-lookup"><span data-stu-id="a4b91-746">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="a4b91-747">提供處理站不是 DI 容器註冊的一部分，而是用來建立實體 DI 容器的主機內建功能。</span><span class="sxs-lookup"><span data-stu-id="a4b91-747">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="a4b91-748">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) 會覆寫用來建立應用程式服務提供者的預設處理站。</span><span class="sxs-lookup"><span data-stu-id="a4b91-748">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="a4b91-749">自訂容器組態由 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> 方法管理。</span><span class="sxs-lookup"><span data-stu-id="a4b91-749">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="a4b91-750"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> 提供在基礎主機 API 上設定容器的強型別體驗。</span><span class="sxs-lookup"><span data-stu-id="a4b91-750"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="a4b91-751"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> 可以多次呼叫，其結果是累加的。</span><span class="sxs-lookup"><span data-stu-id="a4b91-751"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="a4b91-752">建立應用程式的服務容器：</span><span class="sxs-lookup"><span data-stu-id="a4b91-752">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="a4b91-753">提供服務容器處理站：</span><span class="sxs-lookup"><span data-stu-id="a4b91-753">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="a4b91-754">使用處理站，並設定應用程式的自訂服務容器：</span><span class="sxs-lookup"><span data-stu-id="a4b91-754">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="a4b91-755">擴充性</span><span class="sxs-lookup"><span data-stu-id="a4b91-755">Extensibility</span></span>

<span data-ttu-id="a4b91-756">主機擴充性是透過 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 上的擴充方法執行。</span><span class="sxs-lookup"><span data-stu-id="a4b91-756">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="a4b91-757">下列範例使用 <xref:fundamentals/host/hosted-services> 中示範的 [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) 範例顯示擴充方法如何擴充 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 實作。</span><span class="sxs-lookup"><span data-stu-id="a4b91-757">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="a4b91-758">應用程式會建立 `UseHostedService` 擴充方法以註冊在 `T` 中傳遞的裝載服務：</span><span class="sxs-lookup"><span data-stu-id="a4b91-758">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

```csharp
using System;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

public static class Extensions
{
    public static IHostBuilder UseHostedService<T>(this IHostBuilder hostBuilder)
        where T : class, IHostedService, IDisposable
    {
        return hostBuilder.ConfigureServices(services =>
            services.AddHostedService<T>());
    }
}
```

## <a name="manage-the-host"></a><span data-ttu-id="a4b91-759">管理主機</span><span class="sxs-lookup"><span data-stu-id="a4b91-759">Manage the host</span></span>

<span data-ttu-id="a4b91-760"><xref:Microsoft.Extensions.Hosting.IHost> 實作負責啟動及停止服務容器中已註冊的 <xref:Microsoft.Extensions.Hosting.IHostedService> 實作。</span><span class="sxs-lookup"><span data-stu-id="a4b91-760">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="a4b91-761">執行</span><span class="sxs-lookup"><span data-stu-id="a4b91-761">Run</span></span>

<span data-ttu-id="a4b91-762"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> 會執行應用程式並封鎖呼叫執行緒，直到主機關閉為止：</span><span class="sxs-lookup"><span data-stu-id="a4b91-762"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        host.Run();
    }
}
```

### <a name="runasync"></a><span data-ttu-id="a4b91-763">RunAsync</span><span class="sxs-lookup"><span data-stu-id="a4b91-763">RunAsync</span></span>

<span data-ttu-id="a4b91-764"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> 會執行應用程式，並傳回觸發取消語彙基元或關機時所完成的 <xref:System.Threading.Tasks.Task>：</span><span class="sxs-lookup"><span data-stu-id="a4b91-764"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="runconsoleasync"></a><span data-ttu-id="a4b91-765">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="a4b91-765">RunConsoleAsync</span></span>

<span data-ttu-id="a4b91-766"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>啟用主控台支援、建立並啟動主機，以及等候<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT 或 SIGTERM 關閉。</span><span class="sxs-lookup"><span data-stu-id="a4b91-766"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var hostBuilder = new HostBuilder();

        await hostBuilder.RunConsoleAsync();
    }
}
```

### <a name="start-and-stopasync"></a><span data-ttu-id="a4b91-767">Start 和 StopAsync</span><span class="sxs-lookup"><span data-stu-id="a4b91-767">Start and StopAsync</span></span>

<span data-ttu-id="a4b91-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> 會同步啟動主機。</span><span class="sxs-lookup"><span data-stu-id="a4b91-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="a4b91-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> 會嘗試在提供的逾時內停止主機。</span><span class="sxs-lookup"><span data-stu-id="a4b91-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            await host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

### <a name="startasync-and-stopasync"></a><span data-ttu-id="a4b91-770">StartAsync 和 StopAsync</span><span class="sxs-lookup"><span data-stu-id="a4b91-770">StartAsync and StopAsync</span></span>

<span data-ttu-id="a4b91-771"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> 會啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="a4b91-771"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="a4b91-772"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 會停止應用程式。</span><span class="sxs-lookup"><span data-stu-id="a4b91-772"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.StopAsync();
        }
    }
}
```

### <a name="waitforshutdown"></a><span data-ttu-id="a4b91-773">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="a4b91-773">WaitForShutdown</span></span>

<span data-ttu-id="a4b91-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>會透過來觸發 <xref:Microsoft.Extensions.Hosting.IHostLifetime> ，例如 `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (接聽<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT 或 SIGTERM) 。</span><span class="sxs-lookup"><span data-stu-id="a4b91-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="a4b91-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> 會呼叫 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>。</span><span class="sxs-lookup"><span data-stu-id="a4b91-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            host.WaitForShutdown();
        }
    }
}
```

### <a name="waitforshutdownasync"></a><span data-ttu-id="a4b91-776">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="a4b91-776">WaitForShutdownAsync</span></span>

<span data-ttu-id="a4b91-777"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> 會傳回透過指定語彙基元觸發關機時所完成的 <xref:System.Threading.Tasks.Task>，並呼叫 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>。</span><span class="sxs-lookup"><span data-stu-id="a4b91-777"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.WaitForShutdownAsync();
        }

    }
}
```

### <a name="external-control"></a><span data-ttu-id="a4b91-778">外部控制</span><span class="sxs-lookup"><span data-stu-id="a4b91-778">External control</span></span>

<span data-ttu-id="a4b91-779">主機的外部控制可以使用可從外部呼叫的方法來達成：</span><span class="sxs-lookup"><span data-stu-id="a4b91-779">External control of the host can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

<span data-ttu-id="a4b91-780"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> 在 <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> 開始時呼叫，並等到完成後再繼續進行。</span><span class="sxs-lookup"><span data-stu-id="a4b91-780"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="a4b91-781">這可用來將啟動延遲到外部事件發出訊號為止。</span><span class="sxs-lookup"><span data-stu-id="a4b91-781">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="a4b91-782">IHostingEnvironment 介面</span><span class="sxs-lookup"><span data-stu-id="a4b91-782">IHostingEnvironment interface</span></span>

<span data-ttu-id="a4b91-783"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> 提供應用程式主控環境的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="a4b91-783"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="a4b91-784">使用[建構函式插入](xref:fundamentals/dependency-injection)以取得 <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>，才能使用其屬性和擴充方法：</span><span class="sxs-lookup"><span data-stu-id="a4b91-784">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

```csharp
public class MyClass
{
    private readonly IHostingEnvironment _env;

    public MyClass(IHostingEnvironment env)
    {
        _env = env;
    }

    public void DoSomething()
    {
        var environmentName = _env.EnvironmentName;
    }
}
```

<span data-ttu-id="a4b91-785">如需詳細資訊，請參閱<xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="a4b91-785">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="a4b91-786">IApplicationLifetime 介面</span><span class="sxs-lookup"><span data-stu-id="a4b91-786">IApplicationLifetime interface</span></span>

<span data-ttu-id="a4b91-787"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> 允許啟動後和關機活動，包括順利關機要求。</span><span class="sxs-lookup"><span data-stu-id="a4b91-787"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="a4b91-788">在介面上的三個屬性是用來註冊定義啟動和關閉事件之 <xref:System.Action> 方法的取消語彙基元。</span><span class="sxs-lookup"><span data-stu-id="a4b91-788">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="a4b91-789">取消權杖</span><span class="sxs-lookup"><span data-stu-id="a4b91-789">Cancellation Token</span></span> | <span data-ttu-id="a4b91-790">觸發時機&#8230;</span><span class="sxs-lookup"><span data-stu-id="a4b91-790">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="a4b91-791">已完全啟動主機。</span><span class="sxs-lookup"><span data-stu-id="a4b91-791">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="a4b91-792">主機正在完成正常關機程序。</span><span class="sxs-lookup"><span data-stu-id="a4b91-792">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="a4b91-793">應該處理所有要求。</span><span class="sxs-lookup"><span data-stu-id="a4b91-793">All requests should be processed.</span></span> <span data-ttu-id="a4b91-794">關機封鎖，直到完成此事件。</span><span class="sxs-lookup"><span data-stu-id="a4b91-794">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="a4b91-795">主機正在執行正常關機程序。</span><span class="sxs-lookup"><span data-stu-id="a4b91-795">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="a4b91-796">可能仍在處理要求。</span><span class="sxs-lookup"><span data-stu-id="a4b91-796">Requests may still be processing.</span></span> <span data-ttu-id="a4b91-797">關機封鎖，直到完成此事件。</span><span class="sxs-lookup"><span data-stu-id="a4b91-797">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="a4b91-798">建構函式將 <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> 服務插入任何類別。</span><span class="sxs-lookup"><span data-stu-id="a4b91-798">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="a4b91-799">[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/)使用建構函式插入 `LifetimeEventsHostedService` 類別 (<xref:Microsoft.Extensions.Hosting.IHostedService> 實作) 來註冊事件。</span><span class="sxs-lookup"><span data-stu-id="a4b91-799">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="a4b91-800">*LifetimeEventsHostedService.cs*：</span><span class="sxs-lookup"><span data-stu-id="a4b91-800">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="a4b91-801"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> 會要求應用程式終止。</span><span class="sxs-lookup"><span data-stu-id="a4b91-801"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="a4b91-802">當呼叫類別的 `Shutdown` 方法時，下列類別使用 <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> 來順利關閉應用程式：</span><span class="sxs-lookup"><span data-stu-id="a4b91-802">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

```csharp
public class MyClass
{
    private readonly IApplicationLifetime _appLifetime;

    public MyClass(IApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="a4b91-803">其他資源</span><span class="sxs-lookup"><span data-stu-id="a4b91-803">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
