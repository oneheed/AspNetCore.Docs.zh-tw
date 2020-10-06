---
title: ASP.NET Core 中的 .NET 泛型主機
author: rick-anderson
description: 在 ASP.NET Core apps 中使用 .NET Core 泛型主機。  一般主機負責應用程式啟動和存留期管理。
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
ms.openlocfilehash: d3de81ce7248372279b423da865513ee5db73c79
ms.sourcegitcommit: d7991068bc6b04063f4bd836fc5b9591d614d448
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762317"
---
# <a name="net-generic-host-in-aspnet-core"></a><span data-ttu-id="225bd-104">ASP.NET Core 中的 .NET 泛型主機</span><span class="sxs-lookup"><span data-stu-id="225bd-104">.NET Generic Host in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="225bd-105">ASP.NET Core 範本會建立 .NET Core 泛型主機 (<xref:Microsoft.Extensions.Hosting.HostBuilder>) 。</span><span class="sxs-lookup"><span data-stu-id="225bd-105">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="225bd-106">本主題提供在 ASP.NET Core 中使用 .NET 泛型主機的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="225bd-106">This topic provides information on using .NET Generic Host in ASP.NET Core.</span></span> <span data-ttu-id="225bd-107">如需在主控台應用程式中使用 .NET 泛型主機的詳細資訊，請參閱 [.Net 泛型主機](/dotnet/core/extensions/generic-host)。</span><span class="sxs-lookup"><span data-stu-id="225bd-107">For information on using .NET Generic Host in console apps, see [.NET Generic Host](/dotnet/core/extensions/generic-host).</span></span>

## <a name="host-definition"></a><span data-ttu-id="225bd-108">主機定義</span><span class="sxs-lookup"><span data-stu-id="225bd-108">Host definition</span></span>

<span data-ttu-id="225bd-109">「主機」\*\* 是封裝所有應用程式資源的物件，例如：</span><span class="sxs-lookup"><span data-stu-id="225bd-109">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="225bd-110">相依性插入 (DI)</span><span class="sxs-lookup"><span data-stu-id="225bd-110">Dependency injection (DI)</span></span>
* <span data-ttu-id="225bd-111">記錄</span><span class="sxs-lookup"><span data-stu-id="225bd-111">Logging</span></span>
* <span data-ttu-id="225bd-112">組態</span><span class="sxs-lookup"><span data-stu-id="225bd-112">Configuration</span></span>
* <span data-ttu-id="225bd-113">`IHostedService` 實作</span><span class="sxs-lookup"><span data-stu-id="225bd-113">`IHostedService` implementations</span></span>

<span data-ttu-id="225bd-114">當主機啟動時，它會呼叫 <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> <xref:Microsoft.Extensions.Hosting.IHostedService> 服務容器的託管服務集合中註冊的每個執行。</span><span class="sxs-lookup"><span data-stu-id="225bd-114">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="225bd-115">在 Web 應用程式中，其中一個 `IHostedService` 實作是一種 Web 服務，負責啟動 [HTTP 伺服器實作](xref:fundamentals/index#servers)。</span><span class="sxs-lookup"><span data-stu-id="225bd-115">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="225bd-116">在單一物件中包含所有應用程式相互依存資源的主要理由便是生命週期管理：控制應用程式的啟動及順利關機。</span><span class="sxs-lookup"><span data-stu-id="225bd-116">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="225bd-117">設定主機</span><span class="sxs-lookup"><span data-stu-id="225bd-117">Set up a host</span></span>

<span data-ttu-id="225bd-118">主機通常由 `Program` 類別的程式碼來設定、建置並執行。</span><span class="sxs-lookup"><span data-stu-id="225bd-118">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="225bd-119">`Main` 方法：</span><span class="sxs-lookup"><span data-stu-id="225bd-119">The `Main` method:</span></span>

* <span data-ttu-id="225bd-120">呼叫 `CreateHostBuilder` 方法來建立及設定建立器物件。</span><span class="sxs-lookup"><span data-stu-id="225bd-120">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="225bd-121">在建立器物件上呼叫 `Build` 和 `Run` 方法。</span><span class="sxs-lookup"><span data-stu-id="225bd-121">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="225bd-122">ASP.NET Core web 範本會產生下列程式碼來建立主機：</span><span class="sxs-lookup"><span data-stu-id="225bd-122">The ASP.NET Core web templates generate the following code to create a host:</span></span>

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

<span data-ttu-id="225bd-123">下列程式碼會建立非 HTTP 工作負載，並將其 `IHostedService` 執行新增至 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="225bd-123">The following code creates a non-HTTP workload with an `IHostedService` implementation added to the DI container.</span></span>

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

<span data-ttu-id="225bd-124">針對 HTTP 工作負載，`Main` 方法相同，但 `CreateHostBuilder` 會呼叫 `ConfigureWebHostDefaults`：</span><span class="sxs-lookup"><span data-stu-id="225bd-124">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="225bd-125">如果應用程式使用 Entity Framework Core，請勿變更 `CreateHostBuilder` 方法的名稱或簽章。</span><span class="sxs-lookup"><span data-stu-id="225bd-125">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="225bd-126">[Entity Framework Core 工具](/ef/core/miscellaneous/cli/)預期找到 `CreateHostBuilder` 方法，其在不執行應用程式的情況下設定主機。</span><span class="sxs-lookup"><span data-stu-id="225bd-126">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="225bd-127">如需詳細資訊，請參閱[設計階段 DbContext 建立](/ef/core/miscellaneous/cli/dbcontext-creation)。</span><span class="sxs-lookup"><span data-stu-id="225bd-127">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="225bd-128">預設建立器設定</span><span class="sxs-lookup"><span data-stu-id="225bd-128">Default builder settings</span></span>

<span data-ttu-id="225bd-129"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> 方法：</span><span class="sxs-lookup"><span data-stu-id="225bd-129">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="225bd-130">將 [內容根目錄](xref:fundamentals/index#content-root) 設定為所傳回的路徑 <xref:System.IO.Directory.GetCurrentDirectory*> 。</span><span class="sxs-lookup"><span data-stu-id="225bd-130">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="225bd-131">從下列項目載入主機組態：</span><span class="sxs-lookup"><span data-stu-id="225bd-131">Loads host configuration from:</span></span>
  * <span data-ttu-id="225bd-132">前面加上的環境變數 `DOTNET_` 。</span><span class="sxs-lookup"><span data-stu-id="225bd-132">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="225bd-133">命令列引數。</span><span class="sxs-lookup"><span data-stu-id="225bd-133">Command-line arguments.</span></span>
* <span data-ttu-id="225bd-134">從下列項目載入應用程式組態：</span><span class="sxs-lookup"><span data-stu-id="225bd-134">Loads app configuration from:</span></span>
  * <span data-ttu-id="225bd-135">*appsettings.js開啟*。</span><span class="sxs-lookup"><span data-stu-id="225bd-135">*appsettings.json*.</span></span>
  * <span data-ttu-id="225bd-136">*appsettings.{Environment}.json*</span><span class="sxs-lookup"><span data-stu-id="225bd-136">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="225bd-137">應用程式在 `Development` 環境中執行時的[祕密管理員](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="225bd-137">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="225bd-138">環境變數。</span><span class="sxs-lookup"><span data-stu-id="225bd-138">Environment variables.</span></span>
  * <span data-ttu-id="225bd-139">命令列引數。</span><span class="sxs-lookup"><span data-stu-id="225bd-139">Command-line arguments.</span></span>
* <span data-ttu-id="225bd-140">新增下列[記錄](xref:fundamentals/logging/index)提供者：</span><span class="sxs-lookup"><span data-stu-id="225bd-140">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="225bd-141">主控台</span><span class="sxs-lookup"><span data-stu-id="225bd-141">Console</span></span>
  * <span data-ttu-id="225bd-142">偵錯</span><span class="sxs-lookup"><span data-stu-id="225bd-142">Debug</span></span>
  * <span data-ttu-id="225bd-143">EventSource</span><span class="sxs-lookup"><span data-stu-id="225bd-143">EventSource</span></span>
  * <span data-ttu-id="225bd-144">EventLog (僅當在 Windows 上執行時)</span><span class="sxs-lookup"><span data-stu-id="225bd-144">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="225bd-145">環境為開發時，會啟用[範圍驗證](xref:fundamentals/dependency-injection#scope-validation)和[相依性驗證](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild)。</span><span class="sxs-lookup"><span data-stu-id="225bd-145">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="225bd-146">`ConfigureWebHostDefaults` 方法：</span><span class="sxs-lookup"><span data-stu-id="225bd-146">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="225bd-147">從前面加上的環境變數載入主機設定 `ASPNETCORE_` 。</span><span class="sxs-lookup"><span data-stu-id="225bd-147">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="225bd-148">會將 [Kestrel](xref:fundamentals/servers/kestrel) 伺服器設為網頁伺服器，並使用應用程式的主機組態提供者進行設定。</span><span class="sxs-lookup"><span data-stu-id="225bd-148">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="225bd-149">如需 Kestrel 伺服器的預設選項，請參閱 <xref:fundamentals/servers/kestrel#kestrel-options>。</span><span class="sxs-lookup"><span data-stu-id="225bd-149">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="225bd-150">新增[主機篩選中介軟體](xref:fundamentals/servers/kestrel#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="225bd-150">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="225bd-151">如果等於，則新增 [轉送的標頭中介軟體](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) `ASPNETCORE_FORWARDEDHEADERS_ENABLED` `true` 。</span><span class="sxs-lookup"><span data-stu-id="225bd-151">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="225bd-152">啟用 IIS 整合。</span><span class="sxs-lookup"><span data-stu-id="225bd-152">Enables IIS integration.</span></span> <span data-ttu-id="225bd-153">如需 IIS 預設選項，請參閱 <xref:host-and-deploy/iis/index#iis-options>。</span><span class="sxs-lookup"><span data-stu-id="225bd-153">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="225bd-154">本文稍後的[＜設定所有應用程式類型＞](#settings-for-all-app-types)和[＜Web 應用程式設定＞](#settings-for-web-apps)章節，將說明如何覆寫預設的建立器設定。</span><span class="sxs-lookup"><span data-stu-id="225bd-154">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="225bd-155">架構提供的服務</span><span class="sxs-lookup"><span data-stu-id="225bd-155">Framework-provided services</span></span>

<span data-ttu-id="225bd-156">下列服務會自動註冊：</span><span class="sxs-lookup"><span data-stu-id="225bd-156">The following services are registered automatically:</span></span>

* [<span data-ttu-id="225bd-157">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="225bd-157">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="225bd-158">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="225bd-158">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="225bd-159">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="225bd-159">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="225bd-160">如需架構所提供之服務的詳細資訊，請參閱 <xref:fundamentals/dependency-injection#framework-provided-services> 。</span><span class="sxs-lookup"><span data-stu-id="225bd-160">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="225bd-161">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="225bd-161">IHostApplicationLifetime</span></span>

<span data-ttu-id="225bd-162">將 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (先前稱為 `IApplicationLifetime`) 服務插入任何類別來處理啟動後和順利關機工作。</span><span class="sxs-lookup"><span data-stu-id="225bd-162">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="225bd-163">介面上的三個屬性是用於註冊應用程式啟動和應用程式關閉事件處理程序方法的取消語彙基元。</span><span class="sxs-lookup"><span data-stu-id="225bd-163">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="225bd-164">介面也包括 `StopApplication` 方法。</span><span class="sxs-lookup"><span data-stu-id="225bd-164">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="225bd-165">下列範例是 `IHostedService` 註冊事件的實作為 `IHostApplicationLifetime` ：</span><span class="sxs-lookup"><span data-stu-id="225bd-165">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="225bd-166">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="225bd-166">IHostLifetime</span></span>

<span data-ttu-id="225bd-167"><xref:Microsoft.Extensions.Hosting.IHostLifetime> 實作會控制主機啟動及停止的時機。</span><span class="sxs-lookup"><span data-stu-id="225bd-167">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="225bd-168">會使用最後一個註冊的實作。</span><span class="sxs-lookup"><span data-stu-id="225bd-168">The last implementation registered is used.</span></span>

<span data-ttu-id="225bd-169">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` 是預設的 `IHostLifetime` 實作。</span><span class="sxs-lookup"><span data-stu-id="225bd-169">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="225bd-170">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="225bd-170">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="225bd-171">接聽<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT 或 SIGTERM，並呼叫 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> 以啟動關機程式。</span><span class="sxs-lookup"><span data-stu-id="225bd-171">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="225bd-172">會解除封鎖 [RunAsync](#runasync) 和 [WaitForShutdownAsync](#waitforshutdownasync) 等延伸模組。</span><span class="sxs-lookup"><span data-stu-id="225bd-172">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="225bd-173">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="225bd-173">IHostEnvironment</span></span>

<span data-ttu-id="225bd-174">將服務插入至 <xref:Microsoft.Extensions.Hosting.IHostEnvironment> 類別，以取得下列設定的相關資訊：</span><span class="sxs-lookup"><span data-stu-id="225bd-174">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="225bd-175">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="225bd-175">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="225bd-176">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="225bd-176">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="225bd-177">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="225bd-177">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="225bd-178">Web apps 會執行 `IWebHostEnvironment` 介面，此介面會繼承 `IHostEnvironment` 並新增 [WebRootPath](#webroot)。</span><span class="sxs-lookup"><span data-stu-id="225bd-178">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="225bd-179">主機組態</span><span class="sxs-lookup"><span data-stu-id="225bd-179">Host configuration</span></span>

<span data-ttu-id="225bd-180">主機組態用於 <xref:Microsoft.Extensions.Hosting.IHostEnvironment> 實作的屬性。</span><span class="sxs-lookup"><span data-stu-id="225bd-180">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="225bd-181">主機組態位於 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 內的 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration)。</span><span class="sxs-lookup"><span data-stu-id="225bd-181">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="225bd-182">在 `ConfigureAppConfiguration` 之後，應用程式組態會取代 `HostBuilderContext.Configuration`。</span><span class="sxs-lookup"><span data-stu-id="225bd-182">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="225bd-183">若要新增主機組態，請呼叫 `IHostBuilder` 上的 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>。</span><span class="sxs-lookup"><span data-stu-id="225bd-183">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="225bd-184">`ConfigureHostConfiguration` 可以多次呼叫，其結果是累加的。</span><span class="sxs-lookup"><span data-stu-id="225bd-184">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="225bd-185">主機會使用指定索引鍵上最後設定值的任何選項。</span><span class="sxs-lookup"><span data-stu-id="225bd-185">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="225bd-186">包含前置詞 `DOTNET_` 和命令列引數的環境變數提供者 `CreateDefaultBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="225bd-186">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="225bd-187">針對 Web 應用程式，會新增具有前置詞 `ASPNETCORE_` 的環境變數提供者。</span><span class="sxs-lookup"><span data-stu-id="225bd-187">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="225bd-188">讀取環境變數時，就會移除前置詞。</span><span class="sxs-lookup"><span data-stu-id="225bd-188">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="225bd-189">例如，`ASPNETCORE_ENVIRONMENT` 的環境變數值會變成 `environment` 索引鍵的主機組態值。</span><span class="sxs-lookup"><span data-stu-id="225bd-189">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="225bd-190">下列範例會建立主機組態：</span><span class="sxs-lookup"><span data-stu-id="225bd-190">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="225bd-191">應用程式設定</span><span class="sxs-lookup"><span data-stu-id="225bd-191">App configuration</span></span>

<span data-ttu-id="225bd-192">應用程式組態的建立方式是在 `IHostBuilder` 上呼叫 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>。</span><span class="sxs-lookup"><span data-stu-id="225bd-192">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="225bd-193">`ConfigureAppConfiguration` 可以多次呼叫，其結果是累加的。</span><span class="sxs-lookup"><span data-stu-id="225bd-193">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="225bd-194">應用程式會使用指定索引鍵上最後設定值的任何選項。</span><span class="sxs-lookup"><span data-stu-id="225bd-194">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="225bd-195">由 `ConfigureAppConfiguration` 建立的組態位於 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*)，可用於後續作業並用作 DI 中的服務。</span><span class="sxs-lookup"><span data-stu-id="225bd-195">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="225bd-196">主機組態也會新增至應用程式組態。</span><span class="sxs-lookup"><span data-stu-id="225bd-196">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="225bd-197">如需詳細資訊，請參閱 [ASP.NET Core 中的組態](xref:fundamentals/configuration/index#configureappconfiguration)。</span><span class="sxs-lookup"><span data-stu-id="225bd-197">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="225bd-198">所有應用程式類型的設定</span><span class="sxs-lookup"><span data-stu-id="225bd-198">Settings for all app types</span></span>

<span data-ttu-id="225bd-199">本節列出適用於 HTTP 和非 HTTP 工作負載的主機設定。</span><span class="sxs-lookup"><span data-stu-id="225bd-199">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="225bd-200">根據預設，用來設定這些設定的環境變數可以具有 `DOTNET_` 或 `ASPNETCORE_` 前置詞。</span><span class="sxs-lookup"><span data-stu-id="225bd-200">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="225bd-201">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="225bd-201">ApplicationName</span></span>

<span data-ttu-id="225bd-202">[IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) 屬性是在主機建構期間從主機組態當中設定。</span><span class="sxs-lookup"><span data-stu-id="225bd-202">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="225bd-203">機**碼**：`applicationName`</span><span class="sxs-lookup"><span data-stu-id="225bd-203">**Key**: `applicationName`</span></span>  
<span data-ttu-id="225bd-204">**輸入**： `string`</span><span class="sxs-lookup"><span data-stu-id="225bd-204">**Type**: `string`</span></span>  
<span data-ttu-id="225bd-205">**預設值**：包含應用程式進入點的元件名稱。</span><span class="sxs-lookup"><span data-stu-id="225bd-205">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="225bd-206">**環境變數**： `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="225bd-206">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="225bd-207">若要設定此值，請使用環境變數。</span><span class="sxs-lookup"><span data-stu-id="225bd-207">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="225bd-208">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="225bd-208">ContentRoot</span></span>

<span data-ttu-id="225bd-209">[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) 屬性會決定主機從哪裡開始搜尋內容檔案。</span><span class="sxs-lookup"><span data-stu-id="225bd-209">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="225bd-210">如果路徑不存在，就無法啟動主機。</span><span class="sxs-lookup"><span data-stu-id="225bd-210">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="225bd-211">機**碼**：`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="225bd-211">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="225bd-212">**輸入**： `string`</span><span class="sxs-lookup"><span data-stu-id="225bd-212">**Type**: `string`</span></span>  
<span data-ttu-id="225bd-213">**預設值**：應用程式元件所在的資料夾。</span><span class="sxs-lookup"><span data-stu-id="225bd-213">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="225bd-214">**環境變數**： `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="225bd-214">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="225bd-215">若要設定此值，請使用環境變數或呼叫 `IHostBuilder` 上的 `UseContentRoot`：</span><span class="sxs-lookup"><span data-stu-id="225bd-215">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="225bd-216">如需詳細資訊，請參閱</span><span class="sxs-lookup"><span data-stu-id="225bd-216">For more information, see:</span></span>

* [<span data-ttu-id="225bd-217">基本概念：內容根目錄</span><span class="sxs-lookup"><span data-stu-id="225bd-217">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="225bd-218">WebRoot</span><span class="sxs-lookup"><span data-stu-id="225bd-218">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="225bd-219">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="225bd-219">EnvironmentName</span></span>

<span data-ttu-id="225bd-220">[IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) 屬性可以設為任何值。</span><span class="sxs-lookup"><span data-stu-id="225bd-220">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="225bd-221">架構定義的值包括 `Development`、`Staging` 和 `Production`。</span><span class="sxs-lookup"><span data-stu-id="225bd-221">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="225bd-222">值不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="225bd-222">Values aren't case-sensitive.</span></span>

<span data-ttu-id="225bd-223">機**碼**：`environment`</span><span class="sxs-lookup"><span data-stu-id="225bd-223">**Key**: `environment`</span></span>  
<span data-ttu-id="225bd-224">**輸入**： `string`</span><span class="sxs-lookup"><span data-stu-id="225bd-224">**Type**: `string`</span></span>  
<span data-ttu-id="225bd-225">**預設值**： `Production`</span><span class="sxs-lookup"><span data-stu-id="225bd-225">**Default**: `Production`</span></span>  
<span data-ttu-id="225bd-226">**環境變數**： `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="225bd-226">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="225bd-227">若要設定此值，請使用環境變數或呼叫 `IHostBuilder` 上的 `UseEnvironment`：</span><span class="sxs-lookup"><span data-stu-id="225bd-227">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="225bd-228">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="225bd-228">ShutdownTimeout</span></span>

<span data-ttu-id="225bd-229">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) 會設定 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 的逾時。</span><span class="sxs-lookup"><span data-stu-id="225bd-229">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="225bd-230">預設值是五秒鐘。</span><span class="sxs-lookup"><span data-stu-id="225bd-230">The default value is five seconds.</span></span>  <span data-ttu-id="225bd-231">在逾時期間，主機會：</span><span class="sxs-lookup"><span data-stu-id="225bd-231">During the timeout period, the host:</span></span>

* <span data-ttu-id="225bd-232">觸發 [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping)。</span><span class="sxs-lookup"><span data-stu-id="225bd-232">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="225bd-233">嘗試停止託管的服務，並記錄無法停止之服務的錯誤。</span><span class="sxs-lookup"><span data-stu-id="225bd-233">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="225bd-234">如果在所有的託管服務停止之前逾時期限已到期，則應用程式關閉時，會停止任何剩餘的作用中服務。</span><span class="sxs-lookup"><span data-stu-id="225bd-234">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="225bd-235">即使服務尚未完成處理也會停止。</span><span class="sxs-lookup"><span data-stu-id="225bd-235">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="225bd-236">如果服務需要更多時間才能停止，請增加逾時。</span><span class="sxs-lookup"><span data-stu-id="225bd-236">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="225bd-237">機**碼**：`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="225bd-237">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="225bd-238">**輸入**： `int`</span><span class="sxs-lookup"><span data-stu-id="225bd-238">**Type**: `int`</span></span>  
<span data-ttu-id="225bd-239">**預設值**：5秒</span><span class="sxs-lookup"><span data-stu-id="225bd-239">**Default**: 5 seconds</span></span>  
<span data-ttu-id="225bd-240">**環境變數**： `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="225bd-240">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="225bd-241">若要設定此值，請使用環境變數或設定 `HostOptions`。</span><span class="sxs-lookup"><span data-stu-id="225bd-241">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="225bd-242">下列範例將逾時設為 20 秒：</span><span class="sxs-lookup"><span data-stu-id="225bd-242">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="225bd-243">變更時停用應用程式設定重載</span><span class="sxs-lookup"><span data-stu-id="225bd-243">Disable app configuration reload on change</span></span>

<span data-ttu-id="225bd-244">依 [預設](xref:fundamentals/configuration/index#default)， *appsettings.json* 和 *appsettings. {環境}。* 當檔案變更時，會重載 json。</span><span class="sxs-lookup"><span data-stu-id="225bd-244">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="225bd-245">若要在 ASP.NET Core 5.0 或更新版本中停用這個重載行為，請將索引 `hostBuilder:reloadConfigOnChange` 鍵設定為 `false` 。</span><span class="sxs-lookup"><span data-stu-id="225bd-245">To disable this reload behavior in ASP.NET Core 5.0 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="225bd-246">機**碼**：`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="225bd-246">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="225bd-247">**類型**： `bool` (`true` 或 `1`) </span><span class="sxs-lookup"><span data-stu-id="225bd-247">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="225bd-248">**預設值**： `true`</span><span class="sxs-lookup"><span data-stu-id="225bd-248">**Default**: `true`</span></span>  
<span data-ttu-id="225bd-249">**命令列引數**： `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="225bd-249">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="225bd-250">**環境變數**： `<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="225bd-250">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="225bd-251">冒號 (`:`) 分隔符號不適用於所有平臺上的環境變數階層式索引鍵。</span><span class="sxs-lookup"><span data-stu-id="225bd-251">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="225bd-252">如需詳細資訊，請參閱 [環境變數](xref:fundamentals/configuration/index#environment-variables)。</span><span class="sxs-lookup"><span data-stu-id="225bd-252">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="225bd-253">Web 應用程式的設定</span><span class="sxs-lookup"><span data-stu-id="225bd-253">Settings for web apps</span></span>

<span data-ttu-id="225bd-254">某些主機設定僅適用於 HTTP 工作負載。</span><span class="sxs-lookup"><span data-stu-id="225bd-254">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="225bd-255">根據預設，用來設定這些設定的環境變數可以具有 `DOTNET_` 或 `ASPNETCORE_` 前置詞。</span><span class="sxs-lookup"><span data-stu-id="225bd-255">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="225bd-256">`IWebHostBuilder` 上的擴充方法適用於這些設定。</span><span class="sxs-lookup"><span data-stu-id="225bd-256">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="225bd-257">示範如何呼叫擴充方法的程式碼範例假設 `webBuilder` 是 `IWebHostBuilder` 的執行個體，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="225bd-257">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="225bd-258">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="225bd-258">CaptureStartupErrors</span></span>

<span data-ttu-id="225bd-259">當它為 `false` 時，啟動期間發生的錯誤會導致主機結束。</span><span class="sxs-lookup"><span data-stu-id="225bd-259">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="225bd-260">當它為 `true` 時，主機會擷取啟動期間的例外狀況，並嘗試啟動伺服器。</span><span class="sxs-lookup"><span data-stu-id="225bd-260">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="225bd-261">機**碼**：`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="225bd-261">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="225bd-262">**類型**： `bool` (`true` 或 `1`) </span><span class="sxs-lookup"><span data-stu-id="225bd-262">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="225bd-263">**預設值**：預設為 `false`，除非應用程式執行時在 IIS 背後有 Kestrel，此時預設值即為 `true`。</span><span class="sxs-lookup"><span data-stu-id="225bd-263">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="225bd-264">**環境變數**： `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="225bd-264">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="225bd-265">若要設定此值，請使用組態或呼叫 `CaptureStartupErrors`：</span><span class="sxs-lookup"><span data-stu-id="225bd-265">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="225bd-266">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="225bd-266">DetailedErrors</span></span>

<span data-ttu-id="225bd-267">啟用時 (或當環境為 `Development` 時)，應用程式會擷取詳細錯誤。</span><span class="sxs-lookup"><span data-stu-id="225bd-267">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="225bd-268">機**碼**：`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="225bd-268">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="225bd-269">**類型**： `bool` (`true` 或 `1`) </span><span class="sxs-lookup"><span data-stu-id="225bd-269">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="225bd-270">**預設值**： `false`</span><span class="sxs-lookup"><span data-stu-id="225bd-270">**Default**: `false`</span></span>  
<span data-ttu-id="225bd-271">**環境變數**： `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="225bd-271">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="225bd-272">若要設定此值，請使用組態或呼叫 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="225bd-272">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="225bd-273">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="225bd-273">HostingStartupAssemblies</span></span>

<span data-ttu-id="225bd-274">在啟動時載入的裝載啟動組件字串，以分號分隔。</span><span class="sxs-lookup"><span data-stu-id="225bd-274">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="225bd-275">雖然設定值會預設為空字串，但裝載啟動組件一律會包含應用程式的組件。</span><span class="sxs-lookup"><span data-stu-id="225bd-275">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="225bd-276">提供裝載啟動組件時，它們會新增至應用程式的組件，以便在應用程式在啟動時建置其通用服務時載入。</span><span class="sxs-lookup"><span data-stu-id="225bd-276">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="225bd-277">機**碼**：`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="225bd-277">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="225bd-278">**輸入**： `string`</span><span class="sxs-lookup"><span data-stu-id="225bd-278">**Type**: `string`</span></span>  
<span data-ttu-id="225bd-279">**預設值**：空字串</span><span class="sxs-lookup"><span data-stu-id="225bd-279">**Default**: Empty string</span></span>  
<span data-ttu-id="225bd-280">**環境變數**： `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="225bd-280">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="225bd-281">若要設定此值，請使用組態或呼叫 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="225bd-281">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="225bd-282">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="225bd-282">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="225bd-283">在啟動時排除以分號分隔的裝載啟動組件字串。</span><span class="sxs-lookup"><span data-stu-id="225bd-283">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="225bd-284">機**碼**：`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="225bd-284">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="225bd-285">**輸入**： `string`</span><span class="sxs-lookup"><span data-stu-id="225bd-285">**Type**: `string`</span></span>  
<span data-ttu-id="225bd-286">**預設值**：空字串</span><span class="sxs-lookup"><span data-stu-id="225bd-286">**Default**: Empty string</span></span>  
<span data-ttu-id="225bd-287">**環境變數**： `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="225bd-287">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="225bd-288">若要設定此值，請使用組態或呼叫 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="225bd-288">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="225bd-289">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="225bd-289">HTTPS_Port</span></span>

<span data-ttu-id="225bd-290">HTTPS 重新導向連接埠。</span><span class="sxs-lookup"><span data-stu-id="225bd-290">The HTTPS redirect port.</span></span> <span data-ttu-id="225bd-291">用於[強制 HTTPS](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="225bd-291">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="225bd-292">機**碼**：`https_port`</span><span class="sxs-lookup"><span data-stu-id="225bd-292">**Key**: `https_port`</span></span>  
<span data-ttu-id="225bd-293">**輸入**： `string`</span><span class="sxs-lookup"><span data-stu-id="225bd-293">**Type**: `string`</span></span>  
<span data-ttu-id="225bd-294">**預設**值：未設定預設值。</span><span class="sxs-lookup"><span data-stu-id="225bd-294">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="225bd-295">**環境變數**： `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="225bd-295">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="225bd-296">若要設定此值，請使用組態或呼叫 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="225bd-296">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="225bd-297">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="225bd-297">PreferHostingUrls</span></span>

<span data-ttu-id="225bd-298">指出主機是否應接聽使用設定的 Url， `IWebHostBuilder` 而不是使用執行時所設定的 url `IServer` 。</span><span class="sxs-lookup"><span data-stu-id="225bd-298">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="225bd-299">機**碼**：`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="225bd-299">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="225bd-300">**類型**： `bool` (`true` 或 `1`) </span><span class="sxs-lookup"><span data-stu-id="225bd-300">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="225bd-301">**預設值**： `true`</span><span class="sxs-lookup"><span data-stu-id="225bd-301">**Default**: `true`</span></span>  
<span data-ttu-id="225bd-302">**環境變數**： `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="225bd-302">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="225bd-303">若要設定此值，請使用環境變數或呼叫 `PreferHostingUrls`：</span><span class="sxs-lookup"><span data-stu-id="225bd-303">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="225bd-304">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="225bd-304">PreventHostingStartup</span></span>

<span data-ttu-id="225bd-305">可防止自動載入裝載啟動組件，包括應用程式組件所設定的裝載啟動組件。</span><span class="sxs-lookup"><span data-stu-id="225bd-305">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="225bd-306">如需詳細資訊，請參閱<xref:fundamentals/configuration/platform-specific-configuration>。</span><span class="sxs-lookup"><span data-stu-id="225bd-306">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="225bd-307">機**碼**：`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="225bd-307">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="225bd-308">**類型**： `bool` (`true` 或 `1`) </span><span class="sxs-lookup"><span data-stu-id="225bd-308">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="225bd-309">**預設值**： `false`</span><span class="sxs-lookup"><span data-stu-id="225bd-309">**Default**: `false`</span></span>  
<span data-ttu-id="225bd-310">**環境變數**： `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="225bd-310">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="225bd-311">若要設定此值，請使用環境變數或呼叫 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="225bd-311">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="225bd-312">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="225bd-312">StartupAssembly</span></span>

<span data-ttu-id="225bd-313">要搜尋 `Startup` 類別的組件。</span><span class="sxs-lookup"><span data-stu-id="225bd-313">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="225bd-314">機**碼**：`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="225bd-314">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="225bd-315">**輸入**： `string`</span><span class="sxs-lookup"><span data-stu-id="225bd-315">**Type**: `string`</span></span>  
<span data-ttu-id="225bd-316">**預設值**：應用程式的組件</span><span class="sxs-lookup"><span data-stu-id="225bd-316">**Default**: The app's assembly</span></span>  
<span data-ttu-id="225bd-317">**環境變數**： `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="225bd-317">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="225bd-318">若要設定此值，請使用環境變數或呼叫 `UseStartup`。</span><span class="sxs-lookup"><span data-stu-id="225bd-318">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="225bd-319">`UseStartup` 可以採用組件名稱 (`string`) 或類型 (`TStartup`)。</span><span class="sxs-lookup"><span data-stu-id="225bd-319">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="225bd-320">如果呼叫多個 `UseStartup` 方法，最後一個將會優先。</span><span class="sxs-lookup"><span data-stu-id="225bd-320">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="225bd-321">URL</span><span class="sxs-lookup"><span data-stu-id="225bd-321">URLs</span></span>

<span data-ttu-id="225bd-322">以分號分隔的 IP 位址或主機位址，包含伺服器應接聽要求的連接埠和通訊協定。</span><span class="sxs-lookup"><span data-stu-id="225bd-322">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="225bd-323">例如： `http://localhost:123` 。</span><span class="sxs-lookup"><span data-stu-id="225bd-323">For example, `http://localhost:123`.</span></span> <span data-ttu-id="225bd-324">使用 "\*"，表示伺服器應接聽任何 IP 位址或主機名稱上的要求，並使用指定的連接埠和通訊協定 (例如，`http://*:5000`)。</span><span class="sxs-lookup"><span data-stu-id="225bd-324">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="225bd-325">通訊協定 (`http://` 或 `https://`) 必須包含在每個 URL 中。</span><span class="sxs-lookup"><span data-stu-id="225bd-325">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="225bd-326">支援的格式會依伺服器而有所不同。</span><span class="sxs-lookup"><span data-stu-id="225bd-326">Supported formats vary among servers.</span></span>

<span data-ttu-id="225bd-327">機**碼**：`urls`</span><span class="sxs-lookup"><span data-stu-id="225bd-327">**Key**: `urls`</span></span>  
<span data-ttu-id="225bd-328">**輸入**： `string`</span><span class="sxs-lookup"><span data-stu-id="225bd-328">**Type**: `string`</span></span>  
<span data-ttu-id="225bd-329">**預設值**： `http://localhost:5000` 和 `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="225bd-329">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="225bd-330">**環境變數**： `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="225bd-330">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="225bd-331">若要設定此值，請使用環境變數或呼叫 `UseUrls`：</span><span class="sxs-lookup"><span data-stu-id="225bd-331">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="225bd-332">Kestrel 有它自己的端點設定 API。</span><span class="sxs-lookup"><span data-stu-id="225bd-332">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="225bd-333">如需詳細資訊，請參閱<xref:fundamentals/servers/kestrel#endpoint-configuration>。</span><span class="sxs-lookup"><span data-stu-id="225bd-333">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="225bd-334">WebRoot</span><span class="sxs-lookup"><span data-stu-id="225bd-334">WebRoot</span></span>

<span data-ttu-id="225bd-335">[IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath)屬性會決定應用程式靜態資產的相對路徑。</span><span class="sxs-lookup"><span data-stu-id="225bd-335">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="225bd-336">如果路徑不存在，則會使用無作業檔案提供者。</span><span class="sxs-lookup"><span data-stu-id="225bd-336">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="225bd-337">機**碼**：`webroot`</span><span class="sxs-lookup"><span data-stu-id="225bd-337">**Key**: `webroot`</span></span>  
<span data-ttu-id="225bd-338">**輸入**： `string`</span><span class="sxs-lookup"><span data-stu-id="225bd-338">**Type**: `string`</span></span>  
<span data-ttu-id="225bd-339">**預設**值：預設值為 `wwwroot` 。</span><span class="sxs-lookup"><span data-stu-id="225bd-339">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="225bd-340">*{Content root}/wwwroot*的路徑必須存在。</span><span class="sxs-lookup"><span data-stu-id="225bd-340">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="225bd-341">**環境變數**： `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="225bd-341">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="225bd-342">若要設定此值，請使用環境變數或呼叫 `IWebHostBuilder` 上的 `UseWebRoot`：</span><span class="sxs-lookup"><span data-stu-id="225bd-342">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="225bd-343">如需詳細資訊，請參閱</span><span class="sxs-lookup"><span data-stu-id="225bd-343">For more information, see:</span></span>

* [<span data-ttu-id="225bd-344">基本概念： Web 根目錄</span><span class="sxs-lookup"><span data-stu-id="225bd-344">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="225bd-345">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="225bd-345">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="225bd-346">管理主機存留期</span><span class="sxs-lookup"><span data-stu-id="225bd-346">Manage the host lifetime</span></span>

<span data-ttu-id="225bd-347">在建置的 <xref:Microsoft.Extensions.Hosting.IHost> 實作上呼叫方法來啟動和停止應用程式。</span><span class="sxs-lookup"><span data-stu-id="225bd-347">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="225bd-348">這些方法會影響所有在服務容器中註冊的 <xref:Microsoft.Extensions.Hosting.IHostedService> 實作。</span><span class="sxs-lookup"><span data-stu-id="225bd-348">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="225bd-349">執行</span><span class="sxs-lookup"><span data-stu-id="225bd-349">Run</span></span>

<span data-ttu-id="225bd-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> 會執行應用程式並封鎖呼叫執行緒，直到主機關閉為止。</span><span class="sxs-lookup"><span data-stu-id="225bd-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="225bd-351">RunAsync</span><span class="sxs-lookup"><span data-stu-id="225bd-351">RunAsync</span></span>

<span data-ttu-id="225bd-352"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> 會執行應用程式，並傳回觸發取消語彙基元或關機時所完成的 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="225bd-352"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="225bd-353">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="225bd-353">RunConsoleAsync</span></span>

<span data-ttu-id="225bd-354"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>啟用主控台支援、建立並啟動主機，以及等候<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT 或 SIGTERM 關閉。</span><span class="sxs-lookup"><span data-stu-id="225bd-354"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="225bd-355">開始</span><span class="sxs-lookup"><span data-stu-id="225bd-355">Start</span></span>

<span data-ttu-id="225bd-356"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> 會同步啟動主機。</span><span class="sxs-lookup"><span data-stu-id="225bd-356"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="225bd-357">StartAsync</span><span class="sxs-lookup"><span data-stu-id="225bd-357">StartAsync</span></span>

<span data-ttu-id="225bd-358"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> 會啟動主機，並傳回觸發取消語彙基元或關機時所完成的 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="225bd-358"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="225bd-359"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> 在 `StartAsync` 開始時呼叫，並等到完成後再繼續進行。</span><span class="sxs-lookup"><span data-stu-id="225bd-359"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="225bd-360">這可用來將啟動延遲到外部事件發出訊號為止。</span><span class="sxs-lookup"><span data-stu-id="225bd-360">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="225bd-361">StopAsync</span><span class="sxs-lookup"><span data-stu-id="225bd-361">StopAsync</span></span>

<span data-ttu-id="225bd-362"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> 會嘗試在提供的逾時內停止主機。</span><span class="sxs-lookup"><span data-stu-id="225bd-362"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="225bd-363">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="225bd-363">WaitForShutdown</span></span>

<span data-ttu-id="225bd-364"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>封鎖呼叫的執行緒，直到 IHostLifetime 觸發關機（例如 via <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT 或 SIGTERM）。</span><span class="sxs-lookup"><span data-stu-id="225bd-364"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="225bd-365">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="225bd-365">WaitForShutdownAsync</span></span>

<span data-ttu-id="225bd-366"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> 會傳回透過指定語彙基元觸發關機時所完成的 <xref:System.Threading.Tasks.Task>，並呼叫 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>。</span><span class="sxs-lookup"><span data-stu-id="225bd-366"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="225bd-367">外部控制</span><span class="sxs-lookup"><span data-stu-id="225bd-367">External control</span></span>

<span data-ttu-id="225bd-368">主機存留期直接控制可以使用可從外部呼叫的方法來達成：</span><span class="sxs-lookup"><span data-stu-id="225bd-368">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="225bd-369">ASP.NET Core 範本會建立 .NET Core 泛型主機 (<xref:Microsoft.Extensions.Hosting.HostBuilder>) 。</span><span class="sxs-lookup"><span data-stu-id="225bd-369">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="225bd-370">本主題提供在 ASP.NET Core 中使用 .NET 泛型主機的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="225bd-370">This topic provides information on using .NET Generic Host in ASP.NET Core.</span></span> <span data-ttu-id="225bd-371">如需在主控台應用程式中使用 .NET 泛型主機的詳細資訊，請參閱 [.Net 泛型主機](/dotnet/core/extensions/generic-host)。</span><span class="sxs-lookup"><span data-stu-id="225bd-371">For information on using .NET Generic Host in console apps, see [.NET Generic Host](/dotnet/core/extensions/generic-host).</span></span>

## <a name="host-definition"></a><span data-ttu-id="225bd-372">主機定義</span><span class="sxs-lookup"><span data-stu-id="225bd-372">Host definition</span></span>

<span data-ttu-id="225bd-373">「主機」\*\* 是封裝所有應用程式資源的物件，例如：</span><span class="sxs-lookup"><span data-stu-id="225bd-373">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="225bd-374">相依性插入 (DI)</span><span class="sxs-lookup"><span data-stu-id="225bd-374">Dependency injection (DI)</span></span>
* <span data-ttu-id="225bd-375">記錄</span><span class="sxs-lookup"><span data-stu-id="225bd-375">Logging</span></span>
* <span data-ttu-id="225bd-376">組態</span><span class="sxs-lookup"><span data-stu-id="225bd-376">Configuration</span></span>
* <span data-ttu-id="225bd-377">`IHostedService` 實作</span><span class="sxs-lookup"><span data-stu-id="225bd-377">`IHostedService` implementations</span></span>

<span data-ttu-id="225bd-378">當主機啟動時，它會呼叫 <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> <xref:Microsoft.Extensions.Hosting.IHostedService> 服務容器的託管服務集合中註冊的每個執行。</span><span class="sxs-lookup"><span data-stu-id="225bd-378">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="225bd-379">在 Web 應用程式中，其中一個 `IHostedService` 實作是一種 Web 服務，負責啟動 [HTTP 伺服器實作](xref:fundamentals/index#servers)。</span><span class="sxs-lookup"><span data-stu-id="225bd-379">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="225bd-380">在單一物件中包含所有應用程式相互依存資源的主要理由便是生命週期管理：控制應用程式的啟動及順利關機。</span><span class="sxs-lookup"><span data-stu-id="225bd-380">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="225bd-381">設定主機</span><span class="sxs-lookup"><span data-stu-id="225bd-381">Set up a host</span></span>

<span data-ttu-id="225bd-382">主機通常由 `Program` 類別的程式碼來設定、建置並執行。</span><span class="sxs-lookup"><span data-stu-id="225bd-382">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="225bd-383">`Main` 方法：</span><span class="sxs-lookup"><span data-stu-id="225bd-383">The `Main` method:</span></span>

* <span data-ttu-id="225bd-384">呼叫 `CreateHostBuilder` 方法來建立及設定建立器物件。</span><span class="sxs-lookup"><span data-stu-id="225bd-384">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="225bd-385">在建立器物件上呼叫 `Build` 和 `Run` 方法。</span><span class="sxs-lookup"><span data-stu-id="225bd-385">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="225bd-386">ASP.NET Core web 範本會產生下列程式碼來建立泛型主機：</span><span class="sxs-lookup"><span data-stu-id="225bd-386">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

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

<span data-ttu-id="225bd-387">下列程式碼會使用非 HTTP 工作負載來建立泛型主機。</span><span class="sxs-lookup"><span data-stu-id="225bd-387">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="225bd-388">`IHostedService`執行會新增至 DI 容器：</span><span class="sxs-lookup"><span data-stu-id="225bd-388">The `IHostedService` implementation is added to the DI container:</span></span>

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

<span data-ttu-id="225bd-389">針對 HTTP 工作負載，`Main` 方法相同，但 `CreateHostBuilder` 會呼叫 `ConfigureWebHostDefaults`：</span><span class="sxs-lookup"><span data-stu-id="225bd-389">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="225bd-390">上述程式碼是由 ASP.NET Core 範本所產生。</span><span class="sxs-lookup"><span data-stu-id="225bd-390">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="225bd-391">如果應用程式使用 Entity Framework Core，請勿變更 `CreateHostBuilder` 方法的名稱或簽章。</span><span class="sxs-lookup"><span data-stu-id="225bd-391">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="225bd-392">[Entity Framework Core 工具](/ef/core/miscellaneous/cli/)預期找到 `CreateHostBuilder` 方法，其在不執行應用程式的情況下設定主機。</span><span class="sxs-lookup"><span data-stu-id="225bd-392">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="225bd-393">如需詳細資訊，請參閱[設計階段 DbContext 建立](/ef/core/miscellaneous/cli/dbcontext-creation)。</span><span class="sxs-lookup"><span data-stu-id="225bd-393">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="225bd-394">預設建立器設定</span><span class="sxs-lookup"><span data-stu-id="225bd-394">Default builder settings</span></span>

<span data-ttu-id="225bd-395"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> 方法：</span><span class="sxs-lookup"><span data-stu-id="225bd-395">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> method:</span></span>

* <span data-ttu-id="225bd-396">將 [內容根目錄](xref:fundamentals/index#content-root) 設定為所傳回的路徑 <xref:System.IO.Directory.GetCurrentDirectory%2A> 。</span><span class="sxs-lookup"><span data-stu-id="225bd-396">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory%2A>.</span></span>
* <span data-ttu-id="225bd-397">從下列項目載入主機組態：</span><span class="sxs-lookup"><span data-stu-id="225bd-397">Loads host configuration from:</span></span>
  * <span data-ttu-id="225bd-398">前面加上的環境變數 `DOTNET_` 。</span><span class="sxs-lookup"><span data-stu-id="225bd-398">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="225bd-399">命令列引數。</span><span class="sxs-lookup"><span data-stu-id="225bd-399">Command-line arguments.</span></span>
* <span data-ttu-id="225bd-400">從下列項目載入應用程式組態：</span><span class="sxs-lookup"><span data-stu-id="225bd-400">Loads app configuration from:</span></span>
  * <span data-ttu-id="225bd-401">*appsettings.js開啟*。</span><span class="sxs-lookup"><span data-stu-id="225bd-401">*appsettings.json*.</span></span>
  * <span data-ttu-id="225bd-402">*appsettings.{Environment}.json*</span><span class="sxs-lookup"><span data-stu-id="225bd-402">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="225bd-403">應用程式在 `Development` 環境中執行時的[祕密管理員](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="225bd-403">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="225bd-404">環境變數。</span><span class="sxs-lookup"><span data-stu-id="225bd-404">Environment variables.</span></span>
  * <span data-ttu-id="225bd-405">命令列引數。</span><span class="sxs-lookup"><span data-stu-id="225bd-405">Command-line arguments.</span></span>
* <span data-ttu-id="225bd-406">新增下列[記錄](xref:fundamentals/logging/index)提供者：</span><span class="sxs-lookup"><span data-stu-id="225bd-406">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="225bd-407">主控台</span><span class="sxs-lookup"><span data-stu-id="225bd-407">Console</span></span>
  * <span data-ttu-id="225bd-408">偵錯</span><span class="sxs-lookup"><span data-stu-id="225bd-408">Debug</span></span>
  * <span data-ttu-id="225bd-409">EventSource</span><span class="sxs-lookup"><span data-stu-id="225bd-409">EventSource</span></span>
  * <span data-ttu-id="225bd-410">EventLog (僅當在 Windows 上執行時)</span><span class="sxs-lookup"><span data-stu-id="225bd-410">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="225bd-411">環境為開發時，會啟用[範圍驗證](xref:fundamentals/dependency-injection#scope-validation)和[相依性驗證](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild)。</span><span class="sxs-lookup"><span data-stu-id="225bd-411">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="225bd-412">`ConfigureWebHostDefaults` 方法：</span><span class="sxs-lookup"><span data-stu-id="225bd-412">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="225bd-413">從前面加上的環境變數載入主機設定 `ASPNETCORE_` 。</span><span class="sxs-lookup"><span data-stu-id="225bd-413">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="225bd-414">會將 [Kestrel](xref:fundamentals/servers/kestrel) 伺服器設為網頁伺服器，並使用應用程式的主機組態提供者進行設定。</span><span class="sxs-lookup"><span data-stu-id="225bd-414">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="225bd-415">如需 Kestrel 伺服器的預設選項，請參閱 <xref:fundamentals/servers/kestrel#kestrel-options>。</span><span class="sxs-lookup"><span data-stu-id="225bd-415">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="225bd-416">新增[主機篩選中介軟體](xref:fundamentals/servers/kestrel#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="225bd-416">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="225bd-417">如果等於，則新增 [轉送的標頭中介軟體](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) `ASPNETCORE_FORWARDEDHEADERS_ENABLED` `true` 。</span><span class="sxs-lookup"><span data-stu-id="225bd-417">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="225bd-418">啟用 IIS 整合。</span><span class="sxs-lookup"><span data-stu-id="225bd-418">Enables IIS integration.</span></span> <span data-ttu-id="225bd-419">如需 IIS 預設選項，請參閱 <xref:host-and-deploy/iis/index#iis-options>。</span><span class="sxs-lookup"><span data-stu-id="225bd-419">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="225bd-420">本文稍後的[＜設定所有應用程式類型＞](#settings-for-all-app-types)和[＜Web 應用程式設定＞](#settings-for-web-apps)章節，將說明如何覆寫預設的建立器設定。</span><span class="sxs-lookup"><span data-stu-id="225bd-420">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="225bd-421">架構提供的服務</span><span class="sxs-lookup"><span data-stu-id="225bd-421">Framework-provided services</span></span>

<span data-ttu-id="225bd-422">下列服務會自動註冊：</span><span class="sxs-lookup"><span data-stu-id="225bd-422">The following services are registered automatically:</span></span>

* [<span data-ttu-id="225bd-423">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="225bd-423">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="225bd-424">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="225bd-424">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="225bd-425">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="225bd-425">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="225bd-426">如需架構所提供之服務的詳細資訊，請參閱 <xref:fundamentals/dependency-injection#framework-provided-services> 。</span><span class="sxs-lookup"><span data-stu-id="225bd-426">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="225bd-427">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="225bd-427">IHostApplicationLifetime</span></span>

<span data-ttu-id="225bd-428">將 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (先前稱為 `IApplicationLifetime`) 服務插入任何類別來處理啟動後和順利關機工作。</span><span class="sxs-lookup"><span data-stu-id="225bd-428">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="225bd-429">介面上的三個屬性是用於註冊應用程式啟動和應用程式關閉事件處理程序方法的取消語彙基元。</span><span class="sxs-lookup"><span data-stu-id="225bd-429">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="225bd-430">介面也包括 `StopApplication` 方法。</span><span class="sxs-lookup"><span data-stu-id="225bd-430">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="225bd-431">下列範例是 `IHostedService` 註冊事件的實作為 `IHostApplicationLifetime` ：</span><span class="sxs-lookup"><span data-stu-id="225bd-431">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="225bd-432">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="225bd-432">IHostLifetime</span></span>

<span data-ttu-id="225bd-433"><xref:Microsoft.Extensions.Hosting.IHostLifetime> 實作會控制主機啟動及停止的時機。</span><span class="sxs-lookup"><span data-stu-id="225bd-433">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="225bd-434">會使用最後一個註冊的實作。</span><span class="sxs-lookup"><span data-stu-id="225bd-434">The last implementation registered is used.</span></span>

<span data-ttu-id="225bd-435">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` 是預設的 `IHostLifetime` 實作。</span><span class="sxs-lookup"><span data-stu-id="225bd-435">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="225bd-436">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="225bd-436">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="225bd-437">接聽<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT 或 SIGTERM，並呼叫 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> 以啟動關機程式。</span><span class="sxs-lookup"><span data-stu-id="225bd-437">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> to start the shutdown process.</span></span>
* <span data-ttu-id="225bd-438">會解除封鎖 [RunAsync](#runasync) 和 [WaitForShutdownAsync](#waitforshutdownasync) 等延伸模組。</span><span class="sxs-lookup"><span data-stu-id="225bd-438">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="225bd-439">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="225bd-439">IHostEnvironment</span></span>

<span data-ttu-id="225bd-440">將服務插入至 <xref:Microsoft.Extensions.Hosting.IHostEnvironment> 類別，以取得下列設定的相關資訊：</span><span class="sxs-lookup"><span data-stu-id="225bd-440">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="225bd-441">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="225bd-441">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="225bd-442">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="225bd-442">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="225bd-443">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="225bd-443">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="225bd-444">Web apps 會執行 `IWebHostEnvironment` 介面，此介面會繼承 `IHostEnvironment` 並新增 [WebRootPath](#webroot)。</span><span class="sxs-lookup"><span data-stu-id="225bd-444">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="225bd-445">主機組態</span><span class="sxs-lookup"><span data-stu-id="225bd-445">Host configuration</span></span>

<span data-ttu-id="225bd-446">主機組態用於 <xref:Microsoft.Extensions.Hosting.IHostEnvironment> 實作的屬性。</span><span class="sxs-lookup"><span data-stu-id="225bd-446">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="225bd-447">主機組態位於 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> 內的 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration)。</span><span class="sxs-lookup"><span data-stu-id="225bd-447">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span></span> <span data-ttu-id="225bd-448">在 `ConfigureAppConfiguration` 之後，應用程式組態會取代 `HostBuilderContext.Configuration`。</span><span class="sxs-lookup"><span data-stu-id="225bd-448">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="225bd-449">若要新增主機組態，請呼叫 `IHostBuilder` 上的 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A>。</span><span class="sxs-lookup"><span data-stu-id="225bd-449">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> on `IHostBuilder`.</span></span> <span data-ttu-id="225bd-450">`ConfigureHostConfiguration` 可以多次呼叫，其結果是累加的。</span><span class="sxs-lookup"><span data-stu-id="225bd-450">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="225bd-451">主機會使用指定索引鍵上最後設定值的任何選項。</span><span class="sxs-lookup"><span data-stu-id="225bd-451">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="225bd-452">包含前置詞 `DOTNET_` 和命令列引數的環境變數提供者 `CreateDefaultBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="225bd-452">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="225bd-453">針對 Web 應用程式，會新增具有前置詞 `ASPNETCORE_` 的環境變數提供者。</span><span class="sxs-lookup"><span data-stu-id="225bd-453">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="225bd-454">讀取環境變數時，就會移除前置詞。</span><span class="sxs-lookup"><span data-stu-id="225bd-454">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="225bd-455">例如，`ASPNETCORE_ENVIRONMENT` 的環境變數值會變成 `environment` 索引鍵的主機組態值。</span><span class="sxs-lookup"><span data-stu-id="225bd-455">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="225bd-456">下列範例會建立主機組態：</span><span class="sxs-lookup"><span data-stu-id="225bd-456">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="225bd-457">應用程式設定</span><span class="sxs-lookup"><span data-stu-id="225bd-457">App configuration</span></span>

<span data-ttu-id="225bd-458">應用程式組態的建立方式是在 `IHostBuilder` 上呼叫 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>。</span><span class="sxs-lookup"><span data-stu-id="225bd-458">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="225bd-459">`ConfigureAppConfiguration` 可以多次呼叫，其結果是累加的。</span><span class="sxs-lookup"><span data-stu-id="225bd-459">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="225bd-460">應用程式會使用指定索引鍵上最後設定值的任何選項。</span><span class="sxs-lookup"><span data-stu-id="225bd-460">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="225bd-461">由 `ConfigureAppConfiguration` 建立的組態位於 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*)，可用於後續作業並用作 DI 中的服務。</span><span class="sxs-lookup"><span data-stu-id="225bd-461">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="225bd-462">主機組態也會新增至應用程式組態。</span><span class="sxs-lookup"><span data-stu-id="225bd-462">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="225bd-463">如需詳細資訊，請參閱 [ASP.NET Core 中的組態](xref:fundamentals/configuration/index#configureappconfiguration)。</span><span class="sxs-lookup"><span data-stu-id="225bd-463">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="225bd-464">所有應用程式類型的設定</span><span class="sxs-lookup"><span data-stu-id="225bd-464">Settings for all app types</span></span>

<span data-ttu-id="225bd-465">本節列出適用於 HTTP 和非 HTTP 工作負載的主機設定。</span><span class="sxs-lookup"><span data-stu-id="225bd-465">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="225bd-466">根據預設，用來設定這些設定的環境變數可以具有 `DOTNET_` 或 `ASPNETCORE_` 前置詞。</span><span class="sxs-lookup"><span data-stu-id="225bd-466">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="225bd-467">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="225bd-467">ApplicationName</span></span>

<span data-ttu-id="225bd-468">[IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) 屬性是在主機建構期間從主機組態當中設定。</span><span class="sxs-lookup"><span data-stu-id="225bd-468">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="225bd-469">機**碼**：`applicationName`</span><span class="sxs-lookup"><span data-stu-id="225bd-469">**Key**: `applicationName`</span></span>  
<span data-ttu-id="225bd-470">**輸入**： `string`</span><span class="sxs-lookup"><span data-stu-id="225bd-470">**Type**: `string`</span></span>  
<span data-ttu-id="225bd-471">**預設值**：包含應用程式進入點的元件名稱。</span><span class="sxs-lookup"><span data-stu-id="225bd-471">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="225bd-472">**環境變數**： `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="225bd-472">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="225bd-473">若要設定此值，請使用環境變數。</span><span class="sxs-lookup"><span data-stu-id="225bd-473">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="225bd-474">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="225bd-474">ContentRoot</span></span>

<span data-ttu-id="225bd-475">[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) 屬性會決定主機從哪裡開始搜尋內容檔案。</span><span class="sxs-lookup"><span data-stu-id="225bd-475">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="225bd-476">如果路徑不存在，就無法啟動主機。</span><span class="sxs-lookup"><span data-stu-id="225bd-476">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="225bd-477">機**碼**：`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="225bd-477">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="225bd-478">**輸入**： `string`</span><span class="sxs-lookup"><span data-stu-id="225bd-478">**Type**: `string`</span></span>  
<span data-ttu-id="225bd-479">**預設值**：應用程式元件所在的資料夾。</span><span class="sxs-lookup"><span data-stu-id="225bd-479">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="225bd-480">**環境變數**： `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="225bd-480">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="225bd-481">若要設定此值，請使用環境變數或呼叫 `IHostBuilder` 上的 `UseContentRoot`：</span><span class="sxs-lookup"><span data-stu-id="225bd-481">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="225bd-482">如需詳細資訊，請參閱</span><span class="sxs-lookup"><span data-stu-id="225bd-482">For more information, see:</span></span>

* [<span data-ttu-id="225bd-483">基本概念：內容根目錄</span><span class="sxs-lookup"><span data-stu-id="225bd-483">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="225bd-484">WebRoot</span><span class="sxs-lookup"><span data-stu-id="225bd-484">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="225bd-485">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="225bd-485">EnvironmentName</span></span>

<span data-ttu-id="225bd-486">[IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) 屬性可以設為任何值。</span><span class="sxs-lookup"><span data-stu-id="225bd-486">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="225bd-487">架構定義的值包括 `Development`、`Staging` 和 `Production`。</span><span class="sxs-lookup"><span data-stu-id="225bd-487">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="225bd-488">值不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="225bd-488">Values aren't case-sensitive.</span></span>

<span data-ttu-id="225bd-489">機**碼**：`environment`</span><span class="sxs-lookup"><span data-stu-id="225bd-489">**Key**: `environment`</span></span>  
<span data-ttu-id="225bd-490">**輸入**： `string`</span><span class="sxs-lookup"><span data-stu-id="225bd-490">**Type**: `string`</span></span>  
<span data-ttu-id="225bd-491">**預設值**： `Production`</span><span class="sxs-lookup"><span data-stu-id="225bd-491">**Default**: `Production`</span></span>  
<span data-ttu-id="225bd-492">**環境變數**： `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="225bd-492">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="225bd-493">若要設定此值，請使用環境變數或呼叫 `IHostBuilder` 上的 `UseEnvironment`：</span><span class="sxs-lookup"><span data-stu-id="225bd-493">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="225bd-494">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="225bd-494">ShutdownTimeout</span></span>

<span data-ttu-id="225bd-495">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) 會設定 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 的逾時。</span><span class="sxs-lookup"><span data-stu-id="225bd-495">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="225bd-496">預設值是五秒鐘。</span><span class="sxs-lookup"><span data-stu-id="225bd-496">The default value is five seconds.</span></span>  <span data-ttu-id="225bd-497">在逾時期間，主機會：</span><span class="sxs-lookup"><span data-stu-id="225bd-497">During the timeout period, the host:</span></span>

* <span data-ttu-id="225bd-498">觸發 [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping)。</span><span class="sxs-lookup"><span data-stu-id="225bd-498">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="225bd-499">嘗試停止託管的服務，並記錄無法停止之服務的錯誤。</span><span class="sxs-lookup"><span data-stu-id="225bd-499">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="225bd-500">如果在所有的託管服務停止之前逾時期限已到期，則應用程式關閉時，會停止任何剩餘的作用中服務。</span><span class="sxs-lookup"><span data-stu-id="225bd-500">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="225bd-501">即使服務尚未完成處理也會停止。</span><span class="sxs-lookup"><span data-stu-id="225bd-501">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="225bd-502">如果服務需要更多時間才能停止，請增加逾時。</span><span class="sxs-lookup"><span data-stu-id="225bd-502">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="225bd-503">機**碼**：`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="225bd-503">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="225bd-504">**輸入**： `int`</span><span class="sxs-lookup"><span data-stu-id="225bd-504">**Type**: `int`</span></span>  
<span data-ttu-id="225bd-505">**預設值**：5秒</span><span class="sxs-lookup"><span data-stu-id="225bd-505">**Default**: 5 seconds</span></span>  
<span data-ttu-id="225bd-506">**環境變數**： `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="225bd-506">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="225bd-507">若要設定此值，請使用環境變數或設定 `HostOptions`。</span><span class="sxs-lookup"><span data-stu-id="225bd-507">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="225bd-508">下列範例將逾時設為 20 秒：</span><span class="sxs-lookup"><span data-stu-id="225bd-508">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="225bd-509">Web 應用程式的設定</span><span class="sxs-lookup"><span data-stu-id="225bd-509">Settings for web apps</span></span>

<span data-ttu-id="225bd-510">某些主機設定僅適用於 HTTP 工作負載。</span><span class="sxs-lookup"><span data-stu-id="225bd-510">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="225bd-511">根據預設，用來設定這些設定的環境變數可以具有 `DOTNET_` 或 `ASPNETCORE_` 前置詞。</span><span class="sxs-lookup"><span data-stu-id="225bd-511">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="225bd-512">`IWebHostBuilder` 上的擴充方法適用於這些設定。</span><span class="sxs-lookup"><span data-stu-id="225bd-512">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="225bd-513">示範如何呼叫擴充方法的程式碼範例假設 `webBuilder` 是 `IWebHostBuilder` 的執行個體，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="225bd-513">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="225bd-514">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="225bd-514">CaptureStartupErrors</span></span>

<span data-ttu-id="225bd-515">當它為 `false` 時，啟動期間發生的錯誤會導致主機結束。</span><span class="sxs-lookup"><span data-stu-id="225bd-515">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="225bd-516">當它為 `true` 時，主機會擷取啟動期間的例外狀況，並嘗試啟動伺服器。</span><span class="sxs-lookup"><span data-stu-id="225bd-516">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="225bd-517">機**碼**：`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="225bd-517">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="225bd-518">**類型**： `bool` (`true` 或 `1`) </span><span class="sxs-lookup"><span data-stu-id="225bd-518">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="225bd-519">**預設值**：預設為 `false`，除非應用程式執行時在 IIS 背後有 Kestrel，此時預設值即為 `true`。</span><span class="sxs-lookup"><span data-stu-id="225bd-519">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="225bd-520">**環境變數**： `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="225bd-520">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="225bd-521">若要設定此值，請使用組態或呼叫 `CaptureStartupErrors`：</span><span class="sxs-lookup"><span data-stu-id="225bd-521">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="225bd-522">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="225bd-522">DetailedErrors</span></span>

<span data-ttu-id="225bd-523">啟用時 (或當環境為 `Development` 時)，應用程式會擷取詳細錯誤。</span><span class="sxs-lookup"><span data-stu-id="225bd-523">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="225bd-524">機**碼**：`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="225bd-524">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="225bd-525">**類型**： `bool` (`true` 或 `1`) </span><span class="sxs-lookup"><span data-stu-id="225bd-525">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="225bd-526">**預設值**： `false`</span><span class="sxs-lookup"><span data-stu-id="225bd-526">**Default**: `false`</span></span>  
<span data-ttu-id="225bd-527">**環境變數**： `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="225bd-527">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="225bd-528">若要設定此值，請使用組態或呼叫 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="225bd-528">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="225bd-529">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="225bd-529">HostingStartupAssemblies</span></span>

<span data-ttu-id="225bd-530">在啟動時載入的裝載啟動組件字串，以分號分隔。</span><span class="sxs-lookup"><span data-stu-id="225bd-530">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="225bd-531">雖然設定值會預設為空字串，但裝載啟動組件一律會包含應用程式的組件。</span><span class="sxs-lookup"><span data-stu-id="225bd-531">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="225bd-532">提供裝載啟動組件時，它們會新增至應用程式的組件，以便在應用程式在啟動時建置其通用服務時載入。</span><span class="sxs-lookup"><span data-stu-id="225bd-532">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="225bd-533">機**碼**：`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="225bd-533">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="225bd-534">**輸入**： `string`</span><span class="sxs-lookup"><span data-stu-id="225bd-534">**Type**: `string`</span></span>  
<span data-ttu-id="225bd-535">**預設值**：空字串</span><span class="sxs-lookup"><span data-stu-id="225bd-535">**Default**: Empty string</span></span>  
<span data-ttu-id="225bd-536">**環境變數**： `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="225bd-536">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="225bd-537">若要設定此值，請使用組態或呼叫 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="225bd-537">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="225bd-538">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="225bd-538">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="225bd-539">在啟動時排除以分號分隔的裝載啟動組件字串。</span><span class="sxs-lookup"><span data-stu-id="225bd-539">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="225bd-540">機**碼**：`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="225bd-540">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="225bd-541">**輸入**： `string`</span><span class="sxs-lookup"><span data-stu-id="225bd-541">**Type**: `string`</span></span>  
<span data-ttu-id="225bd-542">**預設值**：空字串</span><span class="sxs-lookup"><span data-stu-id="225bd-542">**Default**: Empty string</span></span>  
<span data-ttu-id="225bd-543">**環境變數**： `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="225bd-543">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="225bd-544">若要設定此值，請使用組態或呼叫 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="225bd-544">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="225bd-545">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="225bd-545">HTTPS_Port</span></span>

<span data-ttu-id="225bd-546">HTTPS 重新導向連接埠。</span><span class="sxs-lookup"><span data-stu-id="225bd-546">The HTTPS redirect port.</span></span> <span data-ttu-id="225bd-547">用於[強制 HTTPS](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="225bd-547">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="225bd-548">機**碼**：`https_port`</span><span class="sxs-lookup"><span data-stu-id="225bd-548">**Key**: `https_port`</span></span>  
<span data-ttu-id="225bd-549">**輸入**： `string`</span><span class="sxs-lookup"><span data-stu-id="225bd-549">**Type**: `string`</span></span>  
<span data-ttu-id="225bd-550">**預設**值：未設定預設值。</span><span class="sxs-lookup"><span data-stu-id="225bd-550">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="225bd-551">**環境變數**： `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="225bd-551">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="225bd-552">若要設定此值，請使用組態或呼叫 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="225bd-552">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="225bd-553">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="225bd-553">PreferHostingUrls</span></span>

<span data-ttu-id="225bd-554">指出主機是否應接聽使用設定的 Url， `IWebHostBuilder` 而不是使用執行時所設定的 url `IServer` 。</span><span class="sxs-lookup"><span data-stu-id="225bd-554">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="225bd-555">機**碼**：`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="225bd-555">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="225bd-556">**類型**： `bool` (`true` 或 `1`) </span><span class="sxs-lookup"><span data-stu-id="225bd-556">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="225bd-557">**預設值**： `true`</span><span class="sxs-lookup"><span data-stu-id="225bd-557">**Default**: `true`</span></span>  
<span data-ttu-id="225bd-558">**環境變數**： `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="225bd-558">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="225bd-559">若要設定此值，請使用環境變數或呼叫 `PreferHostingUrls`：</span><span class="sxs-lookup"><span data-stu-id="225bd-559">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="225bd-560">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="225bd-560">PreventHostingStartup</span></span>

<span data-ttu-id="225bd-561">可防止自動載入裝載啟動組件，包括應用程式組件所設定的裝載啟動組件。</span><span class="sxs-lookup"><span data-stu-id="225bd-561">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="225bd-562">如需詳細資訊，請參閱<xref:fundamentals/configuration/platform-specific-configuration>。</span><span class="sxs-lookup"><span data-stu-id="225bd-562">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="225bd-563">機**碼**：`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="225bd-563">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="225bd-564">**類型**： `bool` (`true` 或 `1`) </span><span class="sxs-lookup"><span data-stu-id="225bd-564">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="225bd-565">**預設值**： `false`</span><span class="sxs-lookup"><span data-stu-id="225bd-565">**Default**: `false`</span></span>  
<span data-ttu-id="225bd-566">**環境變數**： `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="225bd-566">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="225bd-567">若要設定此值，請使用環境變數或呼叫 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="225bd-567">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="225bd-568">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="225bd-568">StartupAssembly</span></span>

<span data-ttu-id="225bd-569">要搜尋 `Startup` 類別的組件。</span><span class="sxs-lookup"><span data-stu-id="225bd-569">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="225bd-570">機**碼**：`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="225bd-570">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="225bd-571">**輸入**： `string`</span><span class="sxs-lookup"><span data-stu-id="225bd-571">**Type**: `string`</span></span>  
<span data-ttu-id="225bd-572">**預設值**：應用程式的組件</span><span class="sxs-lookup"><span data-stu-id="225bd-572">**Default**: The app's assembly</span></span>  
<span data-ttu-id="225bd-573">**環境變數**： `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="225bd-573">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="225bd-574">若要設定此值，請使用環境變數或呼叫 `UseStartup`。</span><span class="sxs-lookup"><span data-stu-id="225bd-574">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="225bd-575">`UseStartup` 可以採用組件名稱 (`string`) 或類型 (`TStartup`)。</span><span class="sxs-lookup"><span data-stu-id="225bd-575">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="225bd-576">如果呼叫多個 `UseStartup` 方法，最後一個將會優先。</span><span class="sxs-lookup"><span data-stu-id="225bd-576">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="225bd-577">URL</span><span class="sxs-lookup"><span data-stu-id="225bd-577">URLs</span></span>

<span data-ttu-id="225bd-578">以分號分隔的 IP 位址或主機位址，包含伺服器應接聽要求的連接埠和通訊協定。</span><span class="sxs-lookup"><span data-stu-id="225bd-578">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="225bd-579">例如： `http://localhost:123` 。</span><span class="sxs-lookup"><span data-stu-id="225bd-579">For example, `http://localhost:123`.</span></span> <span data-ttu-id="225bd-580">使用 "\*"，表示伺服器應接聽任何 IP 位址或主機名稱上的要求，並使用指定的連接埠和通訊協定 (例如，`http://*:5000`)。</span><span class="sxs-lookup"><span data-stu-id="225bd-580">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="225bd-581">通訊協定 (`http://` 或 `https://`) 必須包含在每個 URL 中。</span><span class="sxs-lookup"><span data-stu-id="225bd-581">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="225bd-582">支援的格式會依伺服器而有所不同。</span><span class="sxs-lookup"><span data-stu-id="225bd-582">Supported formats vary among servers.</span></span>

<span data-ttu-id="225bd-583">機**碼**：`urls`</span><span class="sxs-lookup"><span data-stu-id="225bd-583">**Key**: `urls`</span></span>  
<span data-ttu-id="225bd-584">**輸入**： `string`</span><span class="sxs-lookup"><span data-stu-id="225bd-584">**Type**: `string`</span></span>  
<span data-ttu-id="225bd-585">**預設值**： `http://localhost:5000` 和 `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="225bd-585">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="225bd-586">**環境變數**： `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="225bd-586">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="225bd-587">若要設定此值，請使用環境變數或呼叫 `UseUrls`：</span><span class="sxs-lookup"><span data-stu-id="225bd-587">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="225bd-588">Kestrel 有它自己的端點設定 API。</span><span class="sxs-lookup"><span data-stu-id="225bd-588">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="225bd-589">如需詳細資訊，請參閱<xref:fundamentals/servers/kestrel#endpoint-configuration>。</span><span class="sxs-lookup"><span data-stu-id="225bd-589">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="225bd-590">WebRoot</span><span class="sxs-lookup"><span data-stu-id="225bd-590">WebRoot</span></span>

<span data-ttu-id="225bd-591">[IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath)屬性會決定應用程式靜態資產的相對路徑。</span><span class="sxs-lookup"><span data-stu-id="225bd-591">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="225bd-592">如果路徑不存在，則會使用無作業檔案提供者。</span><span class="sxs-lookup"><span data-stu-id="225bd-592">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="225bd-593">機**碼**：`webroot`</span><span class="sxs-lookup"><span data-stu-id="225bd-593">**Key**: `webroot`</span></span>  
<span data-ttu-id="225bd-594">**輸入**： `string`</span><span class="sxs-lookup"><span data-stu-id="225bd-594">**Type**: `string`</span></span>  
<span data-ttu-id="225bd-595">**預設**值：預設值為 `wwwroot` 。</span><span class="sxs-lookup"><span data-stu-id="225bd-595">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="225bd-596">*{Content root}/wwwroot*的路徑必須存在。</span><span class="sxs-lookup"><span data-stu-id="225bd-596">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="225bd-597">**環境變數**： `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="225bd-597">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="225bd-598">若要設定此值，請使用環境變數或呼叫 `IWebHostBuilder` 上的 `UseWebRoot`：</span><span class="sxs-lookup"><span data-stu-id="225bd-598">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="225bd-599">如需詳細資訊，請參閱</span><span class="sxs-lookup"><span data-stu-id="225bd-599">For more information, see:</span></span>

* [<span data-ttu-id="225bd-600">基本概念： Web 根目錄</span><span class="sxs-lookup"><span data-stu-id="225bd-600">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="225bd-601">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="225bd-601">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="225bd-602">管理主機存留期</span><span class="sxs-lookup"><span data-stu-id="225bd-602">Manage the host lifetime</span></span>

<span data-ttu-id="225bd-603">在建置的 <xref:Microsoft.Extensions.Hosting.IHost> 實作上呼叫方法來啟動和停止應用程式。</span><span class="sxs-lookup"><span data-stu-id="225bd-603">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="225bd-604">這些方法會影響所有在服務容器中註冊的 <xref:Microsoft.Extensions.Hosting.IHostedService> 實作。</span><span class="sxs-lookup"><span data-stu-id="225bd-604">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="225bd-605">執行</span><span class="sxs-lookup"><span data-stu-id="225bd-605">Run</span></span>

<span data-ttu-id="225bd-606"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> 會執行應用程式並封鎖呼叫執行緒，直到主機關閉為止。</span><span class="sxs-lookup"><span data-stu-id="225bd-606"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="225bd-607">RunAsync</span><span class="sxs-lookup"><span data-stu-id="225bd-607">RunAsync</span></span>

<span data-ttu-id="225bd-608"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> 會執行應用程式，並傳回觸發取消語彙基元或關機時所完成的 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="225bd-608"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="225bd-609">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="225bd-609">RunConsoleAsync</span></span>

<span data-ttu-id="225bd-610"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>啟用主控台支援、建立並啟動主機，以及等候<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT 或 SIGTERM 關閉。</span><span class="sxs-lookup"><span data-stu-id="225bd-610"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="225bd-611">開始</span><span class="sxs-lookup"><span data-stu-id="225bd-611">Start</span></span>

<span data-ttu-id="225bd-612"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> 會同步啟動主機。</span><span class="sxs-lookup"><span data-stu-id="225bd-612"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="225bd-613">StartAsync</span><span class="sxs-lookup"><span data-stu-id="225bd-613">StartAsync</span></span>

<span data-ttu-id="225bd-614"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> 會啟動主機，並傳回觸發取消語彙基元或關機時所完成的 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="225bd-614"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="225bd-615"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> 在 `StartAsync` 開始時呼叫，並等到完成後再繼續進行。</span><span class="sxs-lookup"><span data-stu-id="225bd-615"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="225bd-616">這可用來將啟動延遲到外部事件發出訊號為止。</span><span class="sxs-lookup"><span data-stu-id="225bd-616">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="225bd-617">StopAsync</span><span class="sxs-lookup"><span data-stu-id="225bd-617">StopAsync</span></span>

<span data-ttu-id="225bd-618"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> 會嘗試在提供的逾時內停止主機。</span><span class="sxs-lookup"><span data-stu-id="225bd-618"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="225bd-619">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="225bd-619">WaitForShutdown</span></span>

<span data-ttu-id="225bd-620"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>封鎖呼叫的執行緒，直到 IHostLifetime 觸發關機（例如 via <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT 或 SIGTERM）。</span><span class="sxs-lookup"><span data-stu-id="225bd-620"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="225bd-621">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="225bd-621">WaitForShutdownAsync</span></span>

<span data-ttu-id="225bd-622"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> 會傳回透過指定語彙基元觸發關機時所完成的 <xref:System.Threading.Tasks.Task>，並呼叫 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>。</span><span class="sxs-lookup"><span data-stu-id="225bd-622"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="225bd-623">外部控制</span><span class="sxs-lookup"><span data-stu-id="225bd-623">External control</span></span>

<span data-ttu-id="225bd-624">主機存留期直接控制可以使用可從外部呼叫的方法來達成：</span><span class="sxs-lookup"><span data-stu-id="225bd-624">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="225bd-625">ASP.NET Core 應用程式會設定並啟動主機。</span><span class="sxs-lookup"><span data-stu-id="225bd-625">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="225bd-626">主機負責應用程式啟動和存留期管理。</span><span class="sxs-lookup"><span data-stu-id="225bd-626">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="225bd-627">本文所涵蓋的 ASP.NET Core 泛型主機 (<xref:Microsoft.Extensions.Hosting.HostBuilder>)，可用來不會處理 HTTP 要求的應用程式。</span><span class="sxs-lookup"><span data-stu-id="225bd-627">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="225bd-628">泛型主機的用途是將 HTTP 管線從 Web 主機 API 分離，以允許更廣泛的主機陣列案例。</span><span class="sxs-lookup"><span data-stu-id="225bd-628">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="225bd-629">傳訊、背景工作及其他以泛型主機為基礎的非 HTTP 工作負載都受益於跨領域的功能，例如設定、相依性插入 (DI) 和記錄。</span><span class="sxs-lookup"><span data-stu-id="225bd-629">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="225bd-630">泛型主機是 ASP.NET Core 2.1 的新功能，不適用於虛擬主機案例。</span><span class="sxs-lookup"><span data-stu-id="225bd-630">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="225bd-631">針對虛擬主機案例，請使用 [Web 主機](xref:fundamentals/host/web-host)。</span><span class="sxs-lookup"><span data-stu-id="225bd-631">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="225bd-632">泛型主機將在未來版本中取代 Web 主機，並成為 HTTP 和非 HTTP 案例中的主要主機 API。</span><span class="sxs-lookup"><span data-stu-id="225bd-632">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="225bd-633">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="225bd-633">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="225bd-634">在 [Visual Studio Code](https://code.visualstudio.com/) 中執行範例應用程式時，請使用「外部或整合式終端機」\*\*。</span><span class="sxs-lookup"><span data-stu-id="225bd-634">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="225bd-635">請勿在 `internalConsole` 中執行範例。</span><span class="sxs-lookup"><span data-stu-id="225bd-635">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="225bd-636">若要在 Visual Studio Code 中設定主控台：</span><span class="sxs-lookup"><span data-stu-id="225bd-636">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="225bd-637">開啟 *.vscode/launch.json* 檔案。</span><span class="sxs-lookup"><span data-stu-id="225bd-637">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="225bd-638">在 [.NET Core Launch (console)] \(.NET Core 啟動 (主控台)\)\*\*\*\* 設定中，尋找 **console** 項目。</span><span class="sxs-lookup"><span data-stu-id="225bd-638">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="225bd-639">將此值設定為 `externalTerminal` 或 `integratedTerminal`。</span><span class="sxs-lookup"><span data-stu-id="225bd-639">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="225bd-640">簡介</span><span class="sxs-lookup"><span data-stu-id="225bd-640">Introduction</span></span>

<span data-ttu-id="225bd-641">可使用位於 <xref:Microsoft.Extensions.Hosting> 命名空間的泛型主機程式庫，且該程式庫由 [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) 套件提供。</span><span class="sxs-lookup"><span data-stu-id="225bd-641">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="225bd-642">[Microsoft.AspNetCore.App 中繼套件](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 或更新版本) 包含 `Microsoft.Extensions.Hosting` 套件。</span><span class="sxs-lookup"><span data-stu-id="225bd-642">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="225bd-643"><xref:Microsoft.Extensions.Hosting.IHostedService> 是程式碼執行的進入點。</span><span class="sxs-lookup"><span data-stu-id="225bd-643"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="225bd-644">每個 <xref:Microsoft.Extensions.Hosting.IHostedService> 實作會依 [ConfigureServices 中的服務註冊](#configureservices)順序執行。</span><span class="sxs-lookup"><span data-stu-id="225bd-644">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="225bd-645">當主機啟動時，會在每個 <xref:Microsoft.Extensions.Hosting.IHostedService> 上呼叫 <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*>；當主機順利關閉時，則會依反向註冊順序呼叫 <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*>。</span><span class="sxs-lookup"><span data-stu-id="225bd-645"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="225bd-646">設定主機</span><span class="sxs-lookup"><span data-stu-id="225bd-646">Set up a host</span></span>

<span data-ttu-id="225bd-647"><xref:Microsoft.Extensions.Hosting.IHostBuilder> 是程式庫和應用程式用來初始化、建置及執行主機的主要元件：</span><span class="sxs-lookup"><span data-stu-id="225bd-647"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="225bd-648">選項。</span><span class="sxs-lookup"><span data-stu-id="225bd-648">Options</span></span>

<span data-ttu-id="225bd-649"><xref:Microsoft.Extensions.Hosting.IHost> 的 <xref:Microsoft.Extensions.Hosting.HostOptions> 設定選項。</span><span class="sxs-lookup"><span data-stu-id="225bd-649"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="225bd-650">關機逾時</span><span class="sxs-lookup"><span data-stu-id="225bd-650">Shutdown timeout</span></span>

<span data-ttu-id="225bd-651"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> 會為 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 設定逾時。</span><span class="sxs-lookup"><span data-stu-id="225bd-651"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="225bd-652">預設值是五秒鐘。</span><span class="sxs-lookup"><span data-stu-id="225bd-652">The default value is five seconds.</span></span>

<span data-ttu-id="225bd-653">中的下列選項 `Program.Main` 設定會將預設的五秒關機超時時間增加為20秒：</span><span class="sxs-lookup"><span data-stu-id="225bd-653">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="225bd-654">預設服務</span><span class="sxs-lookup"><span data-stu-id="225bd-654">Default services</span></span>

<span data-ttu-id="225bd-655">下列服務會在主機初始化期間註冊：</span><span class="sxs-lookup"><span data-stu-id="225bd-655">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="225bd-656">[環境](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>) </span><span class="sxs-lookup"><span data-stu-id="225bd-656">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="225bd-657">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>) </span><span class="sxs-lookup"><span data-stu-id="225bd-657">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="225bd-658"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="225bd-658"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="225bd-659"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="225bd-659"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="225bd-660">[選項](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>) </span><span class="sxs-lookup"><span data-stu-id="225bd-660">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="225bd-661">[記錄](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>) </span><span class="sxs-lookup"><span data-stu-id="225bd-661">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="225bd-662">主機組態</span><span class="sxs-lookup"><span data-stu-id="225bd-662">Host configuration</span></span>

<span data-ttu-id="225bd-663">主機組態的建立方式：</span><span class="sxs-lookup"><span data-stu-id="225bd-663">Host configuration is created by:</span></span>

* <span data-ttu-id="225bd-664">呼叫 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 上的擴充方法，來設定[內容根目錄](#content-root)及[環境](#environment)。</span><span class="sxs-lookup"><span data-stu-id="225bd-664">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="225bd-665">在 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> 中從組態提供者讀取組態。</span><span class="sxs-lookup"><span data-stu-id="225bd-665">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="225bd-666">擴充方法</span><span class="sxs-lookup"><span data-stu-id="225bd-666">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="225bd-667">應用程式索引鍵 (名稱)</span><span class="sxs-lookup"><span data-stu-id="225bd-667">Application key (name)</span></span>

<span data-ttu-id="225bd-668">[IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) 屬性是在主機建構期間從主機設定當中設定。</span><span class="sxs-lookup"><span data-stu-id="225bd-668">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="225bd-669">若要明確設定該值，請使用 [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey)：</span><span class="sxs-lookup"><span data-stu-id="225bd-669">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="225bd-670">機**碼**：`applicationName`</span><span class="sxs-lookup"><span data-stu-id="225bd-670">**Key**: `applicationName`</span></span>  
<span data-ttu-id="225bd-671">**輸入**： `string`</span><span class="sxs-lookup"><span data-stu-id="225bd-671">**Type**: `string`</span></span>  
<span data-ttu-id="225bd-672">**預設**：包含應用程式進入點的組件名稱。</span><span class="sxs-lookup"><span data-stu-id="225bd-672">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="225bd-673">**設定使用**： `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="225bd-673">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="225bd-674">**環境變數**： `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` 是 [選擇性的，而且是使用者定義的](#configurehostconfiguration)) </span><span class="sxs-lookup"><span data-stu-id="225bd-674">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="225bd-675">內容根目錄</span><span class="sxs-lookup"><span data-stu-id="225bd-675">Content root</span></span>

<span data-ttu-id="225bd-676">此設定可決定主機開始搜尋內容檔案的位置。</span><span class="sxs-lookup"><span data-stu-id="225bd-676">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="225bd-677">機**碼**：`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="225bd-677">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="225bd-678">**輸入**： `string`</span><span class="sxs-lookup"><span data-stu-id="225bd-678">**Type**: `string`</span></span>  
<span data-ttu-id="225bd-679">**預設值**：預設為應用程式組件所在的資料夾。</span><span class="sxs-lookup"><span data-stu-id="225bd-679">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="225bd-680">**設定使用**： `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="225bd-680">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="225bd-681">**環境變數**： `<PREFIX_>CONTENTROOT` (`<PREFIX_>` 是 [選擇性的，而且是使用者定義的](#configurehostconfiguration)) </span><span class="sxs-lookup"><span data-stu-id="225bd-681">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="225bd-682">如果路徑不存在，就無法啟動主機。</span><span class="sxs-lookup"><span data-stu-id="225bd-682">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="225bd-683">如需詳細資訊，請參閱 [基本概念：內容根目錄](xref:fundamentals/index#content-root)。</span><span class="sxs-lookup"><span data-stu-id="225bd-683">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="225bd-684">環境</span><span class="sxs-lookup"><span data-stu-id="225bd-684">Environment</span></span>

<span data-ttu-id="225bd-685">設定應用程式的 [環境](xref:fundamentals/environments)。</span><span class="sxs-lookup"><span data-stu-id="225bd-685">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="225bd-686">機**碼**：`environment`</span><span class="sxs-lookup"><span data-stu-id="225bd-686">**Key**: `environment`</span></span>  
<span data-ttu-id="225bd-687">**輸入**： `string`</span><span class="sxs-lookup"><span data-stu-id="225bd-687">**Type**: `string`</span></span>  
<span data-ttu-id="225bd-688">**預設值**： `Production`</span><span class="sxs-lookup"><span data-stu-id="225bd-688">**Default**: `Production`</span></span>  
<span data-ttu-id="225bd-689">**設定使用**： `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="225bd-689">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="225bd-690">**環境變數**： `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` 是 [選擇性的，而且是使用者定義的](#configurehostconfiguration)) </span><span class="sxs-lookup"><span data-stu-id="225bd-690">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="225bd-691">環境可以設定為任何值。</span><span class="sxs-lookup"><span data-stu-id="225bd-691">The environment can be set to any value.</span></span> <span data-ttu-id="225bd-692">架構定義的值包括 `Development`、`Staging` 和 `Production`。</span><span class="sxs-lookup"><span data-stu-id="225bd-692">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="225bd-693">值不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="225bd-693">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="225bd-694">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="225bd-694">ConfigureHostConfiguration</span></span>

<span data-ttu-id="225bd-695"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> 會使用 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> 來建立主機的 <xref:Microsoft.Extensions.Configuration.IConfiguration>。</span><span class="sxs-lookup"><span data-stu-id="225bd-695"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="225bd-696">主機組態會用來將 <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> 初始化，使其在應用程式建置流程中可供使用。</span><span class="sxs-lookup"><span data-stu-id="225bd-696">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="225bd-697"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> 可以多次呼叫，其結果是累加的。</span><span class="sxs-lookup"><span data-stu-id="225bd-697"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="225bd-698">主機會使用指定索引鍵上最後設定值的任何選項。</span><span class="sxs-lookup"><span data-stu-id="225bd-698">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="225bd-699">預設不會包含任何提供者。</span><span class="sxs-lookup"><span data-stu-id="225bd-699">No providers are included by default.</span></span> <span data-ttu-id="225bd-700">您必須明確地指定應用程式在 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> 之中需要哪個組態提供者，包括：</span><span class="sxs-lookup"><span data-stu-id="225bd-700">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="225bd-701">檔案組態 (例如，來自 *hostsettings.json* 的檔案)。</span><span class="sxs-lookup"><span data-stu-id="225bd-701">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="225bd-702">環境變數組態。</span><span class="sxs-lookup"><span data-stu-id="225bd-702">Environment variable configuration.</span></span>
* <span data-ttu-id="225bd-703">命令列引數組態。</span><span class="sxs-lookup"><span data-stu-id="225bd-703">Command-line argument configuration.</span></span>
* <span data-ttu-id="225bd-704">任何其他必要的組態提供者。</span><span class="sxs-lookup"><span data-stu-id="225bd-704">Any other required configuration providers.</span></span>

<span data-ttu-id="225bd-705">使用之後呼叫[檔案組態提供者](xref:fundamentals/configuration/index#file-configuration-provider)的 `SetBasePath`，並透過指定應用程式基底路徑，就可使用主機的檔案設定。</span><span class="sxs-lookup"><span data-stu-id="225bd-705">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="225bd-706">範例應用程式會使用 JSON 檔案，*hostsettings.json*，並呼叫 <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 取用檔案的主機組態設定。</span><span class="sxs-lookup"><span data-stu-id="225bd-706">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="225bd-707">若要新增主機的[環境變數組態](xref:fundamentals/configuration/index#environment-variables-configuration-provider)，請在主機建立器上呼叫 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>。</span><span class="sxs-lookup"><span data-stu-id="225bd-707">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="225bd-708">`AddEnvironmentVariables` 可接受選擇性的使用者定義前置詞。</span><span class="sxs-lookup"><span data-stu-id="225bd-708">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="225bd-709">範例應用程式會使用前置詞 `PREFIX_`。</span><span class="sxs-lookup"><span data-stu-id="225bd-709">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="225bd-710">讀取環境變數時，就會移除前置詞。</span><span class="sxs-lookup"><span data-stu-id="225bd-710">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="225bd-711">在設定範例應用程式的主機時，`PREFIX_ENVIRONMENT` 的環境變數值會變成 `environment` 索引鍵的主機組態值。</span><span class="sxs-lookup"><span data-stu-id="225bd-711">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="225bd-712">在開發期間使用 [Visual Studio](https://visualstudio.microsoft.com) 或以 `dotnet run` 執行應用程式時，可能會在 *Properties/launchSettings.json* 檔案中設定環境變數。</span><span class="sxs-lookup"><span data-stu-id="225bd-712">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="225bd-713">在 [Visual Studio Code](https://code.visualstudio.com/) 中，可以在開發期間於 *.vscode/launch.json* 檔案中設定環境變數。</span><span class="sxs-lookup"><span data-stu-id="225bd-713">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="225bd-714">如需詳細資訊，請參閱<xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="225bd-714">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="225bd-715">[命令列組態](xref:fundamentals/configuration/index#command-line-configuration-provider)可透過呼叫 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 新增。</span><span class="sxs-lookup"><span data-stu-id="225bd-715">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="225bd-716">命令列組態會在最後新增，以便命令列引數覆寫由先前組態提供者提供的組態。</span><span class="sxs-lookup"><span data-stu-id="225bd-716">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="225bd-717">*hostsettings.json*：</span><span class="sxs-lookup"><span data-stu-id="225bd-717">*hostsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="225bd-718">您可以使用 [applicationName](#application-key-name) 和 [contentRoot](#content-root) 索引鍵來提供其他組態。</span><span class="sxs-lookup"><span data-stu-id="225bd-718">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="225bd-719">使用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> 的 `HostBuilder` 組態範例：</span><span class="sxs-lookup"><span data-stu-id="225bd-719">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="225bd-720">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="225bd-720">ConfigureAppConfiguration</span></span>

<span data-ttu-id="225bd-721">應用程式組態的建立方式是在 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 實作上呼叫 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>。</span><span class="sxs-lookup"><span data-stu-id="225bd-721">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="225bd-722"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 會使用 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> 來建立應用程式的 <xref:Microsoft.Extensions.Configuration.IConfiguration>。</span><span class="sxs-lookup"><span data-stu-id="225bd-722"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="225bd-723"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 可以多次呼叫，其結果是累加的。</span><span class="sxs-lookup"><span data-stu-id="225bd-723"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="225bd-724">應用程式會使用指定索引鍵上最後設定值的任何選項。</span><span class="sxs-lookup"><span data-stu-id="225bd-724">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="225bd-725">您可以從後續作業的 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) 及 <xref:Microsoft.Extensions.Hosting.IHost.Services*> 中存取 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 所建立的組態。</span><span class="sxs-lookup"><span data-stu-id="225bd-725">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="225bd-726">應用程式組態會自動接收由 [ConfigureHostConfiguration](#configurehostconfiguration) 提供的主機組態。</span><span class="sxs-lookup"><span data-stu-id="225bd-726">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="225bd-727">使用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 的應用程式組態範例：</span><span class="sxs-lookup"><span data-stu-id="225bd-727">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="225bd-728">*appsettings.js*：</span><span class="sxs-lookup"><span data-stu-id="225bd-728">*appsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="225bd-729">*appsettings.Development.json*：</span><span class="sxs-lookup"><span data-stu-id="225bd-729">*appsettings.Development.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="225bd-730">*appsettings.Production.json*：</span><span class="sxs-lookup"><span data-stu-id="225bd-730">*appsettings.Production.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="225bd-731">若要將設定檔移至輸出目錄，請在專案檔中將設定檔指定為 [MSBuild 專案項目](/visualstudio/msbuild/common-msbuild-project-items)。</span><span class="sxs-lookup"><span data-stu-id="225bd-731">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="225bd-732">範例應用程式使用下列 `<Content>` 項目，移動其 JSON 應用程式設定檔和 *hostsettings.json*：</span><span class="sxs-lookup"><span data-stu-id="225bd-732">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="225bd-733">組態擴充方法 (例如 <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 和 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>) 需要其他的 NuGet 套件，例如 [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) \(英文\) 和[Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="225bd-733">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="225bd-734">除非應用程式使用 [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app)，否則，除了核心 [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) \(英文\) 套件，還必須將這些套件新增至專案。</span><span class="sxs-lookup"><span data-stu-id="225bd-734">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="225bd-735">如需詳細資訊，請參閱<xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="225bd-735">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="225bd-736">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="225bd-736">ConfigureServices</span></span>

<span data-ttu-id="225bd-737"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> 會將服務新增至應用程式的[相依性插入](xref:fundamentals/dependency-injection)容器。</span><span class="sxs-lookup"><span data-stu-id="225bd-737"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="225bd-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> 可以多次呼叫，其結果是累加的。</span><span class="sxs-lookup"><span data-stu-id="225bd-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="225bd-739">託管服務是具有背景工作邏輯的類別，可實作 <xref:Microsoft.Extensions.Hosting.IHostedService> 介面。</span><span class="sxs-lookup"><span data-stu-id="225bd-739">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="225bd-740">如需詳細資訊，請參閱<xref:fundamentals/host/hosted-services>。</span><span class="sxs-lookup"><span data-stu-id="225bd-740">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="225bd-741">[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/)使用 `AddHostedService` 擴充方法，將存留期事件 `LifetimeEventsHostedService` 和計時背景工作 `TimedHostedService` 等服務新增至應用程式：</span><span class="sxs-lookup"><span data-stu-id="225bd-741">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="225bd-742">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="225bd-742">ConfigureLogging</span></span>

<span data-ttu-id="225bd-743"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> 會新增委派以設定提供的 <xref:Microsoft.Extensions.Logging.ILoggingBuilder>。</span><span class="sxs-lookup"><span data-stu-id="225bd-743"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="225bd-744"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> 可以多次呼叫，其結果是累加的。</span><span class="sxs-lookup"><span data-stu-id="225bd-744"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="225bd-745">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="225bd-745">UseConsoleLifetime</span></span>

<span data-ttu-id="225bd-746"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>接聽<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT 或 SIGTERM，並呼叫 <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> 以啟動關機程式。</span><span class="sxs-lookup"><span data-stu-id="225bd-746"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="225bd-747"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> 解除封鎖 [RunAsync](#runasync) 和 [>waitforshutdownasync](#waitforshutdownasync)等擴充功能。</span><span class="sxs-lookup"><span data-stu-id="225bd-747"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="225bd-748">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` 會預先註冊為預設存留期實作。</span><span class="sxs-lookup"><span data-stu-id="225bd-748">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="225bd-749">系統會使用最後一個註冊的存留期。</span><span class="sxs-lookup"><span data-stu-id="225bd-749">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="225bd-750">容器設定</span><span class="sxs-lookup"><span data-stu-id="225bd-750">Container configuration</span></span>

<span data-ttu-id="225bd-751">為支援插入其他容器，主機可以接受 <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>。</span><span class="sxs-lookup"><span data-stu-id="225bd-751">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="225bd-752">提供處理站不是 DI 容器註冊的一部分，而是用來建立實體 DI 容器的主機內建功能。</span><span class="sxs-lookup"><span data-stu-id="225bd-752">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="225bd-753">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) 會覆寫用來建立應用程式服務提供者的預設處理站。</span><span class="sxs-lookup"><span data-stu-id="225bd-753">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="225bd-754">自訂容器組態由 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> 方法管理。</span><span class="sxs-lookup"><span data-stu-id="225bd-754">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="225bd-755"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> 提供在基礎主機 API 上設定容器的強型別體驗。</span><span class="sxs-lookup"><span data-stu-id="225bd-755"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="225bd-756"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> 可以多次呼叫，其結果是累加的。</span><span class="sxs-lookup"><span data-stu-id="225bd-756"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="225bd-757">建立應用程式的服務容器：</span><span class="sxs-lookup"><span data-stu-id="225bd-757">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="225bd-758">提供服務容器處理站：</span><span class="sxs-lookup"><span data-stu-id="225bd-758">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="225bd-759">使用處理站，並設定應用程式的自訂服務容器：</span><span class="sxs-lookup"><span data-stu-id="225bd-759">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="225bd-760">擴充性</span><span class="sxs-lookup"><span data-stu-id="225bd-760">Extensibility</span></span>

<span data-ttu-id="225bd-761">主機擴充性是透過 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 上的擴充方法執行。</span><span class="sxs-lookup"><span data-stu-id="225bd-761">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="225bd-762">下列範例使用 <xref:fundamentals/host/hosted-services> 中示範的 [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) 範例顯示擴充方法如何擴充 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 實作。</span><span class="sxs-lookup"><span data-stu-id="225bd-762">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="225bd-763">應用程式會建立 `UseHostedService` 擴充方法以註冊在 `T` 中傳遞的裝載服務：</span><span class="sxs-lookup"><span data-stu-id="225bd-763">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="225bd-764">管理主機</span><span class="sxs-lookup"><span data-stu-id="225bd-764">Manage the host</span></span>

<span data-ttu-id="225bd-765"><xref:Microsoft.Extensions.Hosting.IHost> 實作負責啟動及停止服務容器中已註冊的 <xref:Microsoft.Extensions.Hosting.IHostedService> 實作。</span><span class="sxs-lookup"><span data-stu-id="225bd-765">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="225bd-766">執行</span><span class="sxs-lookup"><span data-stu-id="225bd-766">Run</span></span>

<span data-ttu-id="225bd-767"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> 會執行應用程式並封鎖呼叫執行緒，直到主機關閉為止：</span><span class="sxs-lookup"><span data-stu-id="225bd-767"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="225bd-768">RunAsync</span><span class="sxs-lookup"><span data-stu-id="225bd-768">RunAsync</span></span>

<span data-ttu-id="225bd-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> 會執行應用程式，並傳回觸發取消語彙基元或關機時所完成的 <xref:System.Threading.Tasks.Task>：</span><span class="sxs-lookup"><span data-stu-id="225bd-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="225bd-770">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="225bd-770">RunConsoleAsync</span></span>

<span data-ttu-id="225bd-771"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>啟用主控台支援、建立並啟動主機，以及等候<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT 或 SIGTERM 關閉。</span><span class="sxs-lookup"><span data-stu-id="225bd-771"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="225bd-772">Start 和 StopAsync</span><span class="sxs-lookup"><span data-stu-id="225bd-772">Start and StopAsync</span></span>

<span data-ttu-id="225bd-773"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> 會同步啟動主機。</span><span class="sxs-lookup"><span data-stu-id="225bd-773"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="225bd-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> 會嘗試在提供的逾時內停止主機。</span><span class="sxs-lookup"><span data-stu-id="225bd-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="225bd-775">StartAsync 和 StopAsync</span><span class="sxs-lookup"><span data-stu-id="225bd-775">StartAsync and StopAsync</span></span>

<span data-ttu-id="225bd-776"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> 會啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="225bd-776"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="225bd-777"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 會停止應用程式。</span><span class="sxs-lookup"><span data-stu-id="225bd-777"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="225bd-778">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="225bd-778">WaitForShutdown</span></span>

<span data-ttu-id="225bd-779"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>會透過來觸發 <xref:Microsoft.Extensions.Hosting.IHostLifetime> ，例如 `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (接聽<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT 或 SIGTERM) 。</span><span class="sxs-lookup"><span data-stu-id="225bd-779"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="225bd-780"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> 會呼叫 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>。</span><span class="sxs-lookup"><span data-stu-id="225bd-780"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="225bd-781">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="225bd-781">WaitForShutdownAsync</span></span>

<span data-ttu-id="225bd-782"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> 會傳回透過指定語彙基元觸發關機時所完成的 <xref:System.Threading.Tasks.Task>，並呼叫 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>。</span><span class="sxs-lookup"><span data-stu-id="225bd-782"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="225bd-783">外部控制</span><span class="sxs-lookup"><span data-stu-id="225bd-783">External control</span></span>

<span data-ttu-id="225bd-784">主機的外部控制可以使用可從外部呼叫的方法來達成：</span><span class="sxs-lookup"><span data-stu-id="225bd-784">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="225bd-785"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> 在 <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> 開始時呼叫，並等到完成後再繼續進行。</span><span class="sxs-lookup"><span data-stu-id="225bd-785"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="225bd-786">這可用來將啟動延遲到外部事件發出訊號為止。</span><span class="sxs-lookup"><span data-stu-id="225bd-786">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="225bd-787">IHostingEnvironment 介面</span><span class="sxs-lookup"><span data-stu-id="225bd-787">IHostingEnvironment interface</span></span>

<span data-ttu-id="225bd-788"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> 提供應用程式主控環境的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="225bd-788"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="225bd-789">使用[建構函式插入](xref:fundamentals/dependency-injection)以取得 <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>，才能使用其屬性和擴充方法：</span><span class="sxs-lookup"><span data-stu-id="225bd-789">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="225bd-790">如需詳細資訊，請參閱<xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="225bd-790">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="225bd-791">IApplicationLifetime 介面</span><span class="sxs-lookup"><span data-stu-id="225bd-791">IApplicationLifetime interface</span></span>

<span data-ttu-id="225bd-792"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> 允許啟動後和關機活動，包括順利關機要求。</span><span class="sxs-lookup"><span data-stu-id="225bd-792"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="225bd-793">在介面上的三個屬性是用來註冊定義啟動和關閉事件之 <xref:System.Action> 方法的取消語彙基元。</span><span class="sxs-lookup"><span data-stu-id="225bd-793">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="225bd-794">取消權杖</span><span class="sxs-lookup"><span data-stu-id="225bd-794">Cancellation Token</span></span> | <span data-ttu-id="225bd-795">觸發時機&#8230;</span><span class="sxs-lookup"><span data-stu-id="225bd-795">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="225bd-796">已完全啟動主機。</span><span class="sxs-lookup"><span data-stu-id="225bd-796">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="225bd-797">主機正在完成正常關機程序。</span><span class="sxs-lookup"><span data-stu-id="225bd-797">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="225bd-798">應該處理所有要求。</span><span class="sxs-lookup"><span data-stu-id="225bd-798">All requests should be processed.</span></span> <span data-ttu-id="225bd-799">關機封鎖，直到完成此事件。</span><span class="sxs-lookup"><span data-stu-id="225bd-799">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="225bd-800">主機正在執行正常關機程序。</span><span class="sxs-lookup"><span data-stu-id="225bd-800">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="225bd-801">可能仍在處理要求。</span><span class="sxs-lookup"><span data-stu-id="225bd-801">Requests may still be processing.</span></span> <span data-ttu-id="225bd-802">關機封鎖，直到完成此事件。</span><span class="sxs-lookup"><span data-stu-id="225bd-802">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="225bd-803">建構函式將 <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> 服務插入任何類別。</span><span class="sxs-lookup"><span data-stu-id="225bd-803">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="225bd-804">[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/)使用建構函式插入 `LifetimeEventsHostedService` 類別 (<xref:Microsoft.Extensions.Hosting.IHostedService> 實作) 來註冊事件。</span><span class="sxs-lookup"><span data-stu-id="225bd-804">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="225bd-805">*LifetimeEventsHostedService.cs*：</span><span class="sxs-lookup"><span data-stu-id="225bd-805">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="225bd-806"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> 會要求應用程式終止。</span><span class="sxs-lookup"><span data-stu-id="225bd-806"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="225bd-807">當呼叫類別的 `Shutdown` 方法時，下列類別使用 <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> 來順利關閉應用程式：</span><span class="sxs-lookup"><span data-stu-id="225bd-807">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="225bd-808">其他資源</span><span class="sxs-lookup"><span data-stu-id="225bd-808">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
