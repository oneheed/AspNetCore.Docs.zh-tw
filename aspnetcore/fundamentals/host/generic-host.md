---
title: ASP.NET Core 中的 .NET 泛型主機
author: rick-anderson
description: 在 ASP.NET Core 應用程式中使用 .NET Core 泛型主機。  一般主機負責應用程式啟動和存留期管理。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
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
uid: fundamentals/host/generic-host
ms.openlocfilehash: ceb766cae1f8a1735f5b578a44f0c481927e47a5
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586744"
---
# <a name="net-generic-host-in-aspnet-core"></a><span data-ttu-id="883ca-104">ASP.NET Core 中的 .NET 泛型主機</span><span class="sxs-lookup"><span data-stu-id="883ca-104">.NET Generic Host in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="883ca-105">ASP.NET 核心範本會建立 .NET Core 泛型主機 (<xref:Microsoft.Extensions.Hosting.HostBuilder>) 。</span><span class="sxs-lookup"><span data-stu-id="883ca-105">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="883ca-106">本主題提供在 ASP.NET Core 中使用 .NET 泛型主機的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="883ca-106">This topic provides information on using .NET Generic Host in ASP.NET Core.</span></span> <span data-ttu-id="883ca-107">如需在主控台應用程式中使用 .NET 泛型主機的詳細資訊，請參閱 [.Net 泛型主機](/dotnet/core/extensions/generic-host)。</span><span class="sxs-lookup"><span data-stu-id="883ca-107">For information on using .NET Generic Host in console apps, see [.NET Generic Host](/dotnet/core/extensions/generic-host).</span></span>

## <a name="host-definition"></a><span data-ttu-id="883ca-108">主機定義</span><span class="sxs-lookup"><span data-stu-id="883ca-108">Host definition</span></span>

<span data-ttu-id="883ca-109">「主機」是封裝所有應用程式資源的物件，例如：</span><span class="sxs-lookup"><span data-stu-id="883ca-109">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="883ca-110">相依性插入 (DI)</span><span class="sxs-lookup"><span data-stu-id="883ca-110">Dependency injection (DI)</span></span>
* <span data-ttu-id="883ca-111">記錄</span><span class="sxs-lookup"><span data-stu-id="883ca-111">Logging</span></span>
* <span data-ttu-id="883ca-112">組態</span><span class="sxs-lookup"><span data-stu-id="883ca-112">Configuration</span></span>
* <span data-ttu-id="883ca-113">`IHostedService` 實作</span><span class="sxs-lookup"><span data-stu-id="883ca-113">`IHostedService` implementations</span></span>

<span data-ttu-id="883ca-114">當主機啟動時，它會呼叫 <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> <xref:Microsoft.Extensions.Hosting.IHostedService> 服務容器的託管服務集合中註冊的每個執行。</span><span class="sxs-lookup"><span data-stu-id="883ca-114">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="883ca-115">在 Web 應用程式中，其中一個 `IHostedService` 實作是一種 Web 服務，負責啟動 [HTTP 伺服器實作](xref:fundamentals/index#servers)。</span><span class="sxs-lookup"><span data-stu-id="883ca-115">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="883ca-116">在單一物件中包含所有應用程式相互依存資源的主要理由便是生命週期管理：控制應用程式的啟動及順利關機。</span><span class="sxs-lookup"><span data-stu-id="883ca-116">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="883ca-117">設定主機</span><span class="sxs-lookup"><span data-stu-id="883ca-117">Set up a host</span></span>

<span data-ttu-id="883ca-118">主機通常由 `Program` 類別的程式碼來設定、建置並執行。</span><span class="sxs-lookup"><span data-stu-id="883ca-118">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="883ca-119">`Main` 方法：</span><span class="sxs-lookup"><span data-stu-id="883ca-119">The `Main` method:</span></span>

* <span data-ttu-id="883ca-120">呼叫 `CreateHostBuilder` 方法來建立及設定建立器物件。</span><span class="sxs-lookup"><span data-stu-id="883ca-120">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="883ca-121">在建立器物件上呼叫 `Build` 和 `Run` 方法。</span><span class="sxs-lookup"><span data-stu-id="883ca-121">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="883ca-122">ASP.NET Core web 範本會產生下列程式碼來建立主機：</span><span class="sxs-lookup"><span data-stu-id="883ca-122">The ASP.NET Core web templates generate the following code to create a host:</span></span>

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

<span data-ttu-id="883ca-123">下列程式碼會建立非 HTTP 工作負載，並將其 `IHostedService` 執行新增至 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="883ca-123">The following code creates a non-HTTP workload with an `IHostedService` implementation added to the DI container.</span></span>

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

<span data-ttu-id="883ca-124">針對 HTTP 工作負載，`Main` 方法相同，但 `CreateHostBuilder` 會呼叫 `ConfigureWebHostDefaults`：</span><span class="sxs-lookup"><span data-stu-id="883ca-124">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="883ca-125">如果應用程式使用 Entity Framework Core，請勿變更 `CreateHostBuilder` 方法的名稱或簽章。</span><span class="sxs-lookup"><span data-stu-id="883ca-125">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="883ca-126">[Entity Framework Core 工具](/ef/core/miscellaneous/cli/)預期找到 `CreateHostBuilder` 方法，其在不執行應用程式的情況下設定主機。</span><span class="sxs-lookup"><span data-stu-id="883ca-126">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="883ca-127">如需詳細資訊，請參閱[設計階段 DbContext 建立](/ef/core/miscellaneous/cli/dbcontext-creation)。</span><span class="sxs-lookup"><span data-stu-id="883ca-127">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="883ca-128">預設建立器設定</span><span class="sxs-lookup"><span data-stu-id="883ca-128">Default builder settings</span></span>

<span data-ttu-id="883ca-129"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> 方法：</span><span class="sxs-lookup"><span data-stu-id="883ca-129">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="883ca-130">將 [內容根目錄](xref:fundamentals/index#content-root) 設定為所傳回的路徑 <xref:System.IO.Directory.GetCurrentDirectory*> 。</span><span class="sxs-lookup"><span data-stu-id="883ca-130">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="883ca-131">從下列項目載入主機組態：</span><span class="sxs-lookup"><span data-stu-id="883ca-131">Loads host configuration from:</span></span>
  * <span data-ttu-id="883ca-132">前面加上的環境變數 `DOTNET_` 。</span><span class="sxs-lookup"><span data-stu-id="883ca-132">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="883ca-133">命令列引數。</span><span class="sxs-lookup"><span data-stu-id="883ca-133">Command-line arguments.</span></span>
* <span data-ttu-id="883ca-134">從下列項目載入應用程式組態：</span><span class="sxs-lookup"><span data-stu-id="883ca-134">Loads app configuration from:</span></span>
  * <span data-ttu-id="883ca-135">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="883ca-135">*appsettings.json*.</span></span>
  * <span data-ttu-id="883ca-136">*appsettings.{Environment}.json*</span><span class="sxs-lookup"><span data-stu-id="883ca-136">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="883ca-137">應用程式在 `Development` 環境中執行時的[使用者密碼](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="883ca-137">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="883ca-138">環境變數。</span><span class="sxs-lookup"><span data-stu-id="883ca-138">Environment variables.</span></span>
  * <span data-ttu-id="883ca-139">命令列引數。</span><span class="sxs-lookup"><span data-stu-id="883ca-139">Command-line arguments.</span></span>
* <span data-ttu-id="883ca-140">新增下列[記錄](xref:fundamentals/logging/index)提供者：</span><span class="sxs-lookup"><span data-stu-id="883ca-140">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="883ca-141">主控台</span><span class="sxs-lookup"><span data-stu-id="883ca-141">Console</span></span>
  * <span data-ttu-id="883ca-142">偵錯</span><span class="sxs-lookup"><span data-stu-id="883ca-142">Debug</span></span>
  * <span data-ttu-id="883ca-143">EventSource</span><span class="sxs-lookup"><span data-stu-id="883ca-143">EventSource</span></span>
  * <span data-ttu-id="883ca-144">EventLog (僅當在 Windows 上執行時)</span><span class="sxs-lookup"><span data-stu-id="883ca-144">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="883ca-145">環境為開發時，會啟用[範圍驗證](xref:fundamentals/dependency-injection#scope-validation)和[相依性驗證](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild)。</span><span class="sxs-lookup"><span data-stu-id="883ca-145">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="883ca-146"><xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> 方法：</span><span class="sxs-lookup"><span data-stu-id="883ca-146">The <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method:</span></span>

* <span data-ttu-id="883ca-147">從前面加上的環境變數載入主機設定 `ASPNETCORE_` 。</span><span class="sxs-lookup"><span data-stu-id="883ca-147">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="883ca-148">會將 [Kestrel](xref:fundamentals/servers/kestrel) 伺服器設為網頁伺服器，並使用應用程式的主機組態提供者進行設定。</span><span class="sxs-lookup"><span data-stu-id="883ca-148">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="883ca-149">如需 Kestrel 伺服器的預設選項，請參閱 <xref:fundamentals/servers/kestrel/options>。</span><span class="sxs-lookup"><span data-stu-id="883ca-149">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel/options>.</span></span>
* <span data-ttu-id="883ca-150">新增[主機篩選中介軟體](xref:fundamentals/servers/kestrel/host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="883ca-150">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel/host-filtering).</span></span>
* <span data-ttu-id="883ca-151">如果等於，則新增 [轉送的標頭中介軟體](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) `ASPNETCORE_FORWARDEDHEADERS_ENABLED` `true` 。</span><span class="sxs-lookup"><span data-stu-id="883ca-151">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="883ca-152">啟用 IIS 整合。</span><span class="sxs-lookup"><span data-stu-id="883ca-152">Enables IIS integration.</span></span> <span data-ttu-id="883ca-153">如需 IIS 預設選項，請參閱 <xref:host-and-deploy/iis/index#iis-options>。</span><span class="sxs-lookup"><span data-stu-id="883ca-153">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="883ca-154">本文稍後的[＜設定所有應用程式類型＞](#settings-for-all-app-types)和[＜Web 應用程式設定＞](#settings-for-web-apps)章節，將說明如何覆寫預設的建立器設定。</span><span class="sxs-lookup"><span data-stu-id="883ca-154">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="883ca-155">架構提供的服務</span><span class="sxs-lookup"><span data-stu-id="883ca-155">Framework-provided services</span></span>

<span data-ttu-id="883ca-156">下列服務會自動註冊：</span><span class="sxs-lookup"><span data-stu-id="883ca-156">The following services are registered automatically:</span></span>

* [<span data-ttu-id="883ca-157">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="883ca-157">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="883ca-158">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="883ca-158">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="883ca-159">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="883ca-159">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="883ca-160">如需架構所提供之服務的詳細資訊，請參閱 <xref:fundamentals/dependency-injection#framework-provided-services> 。</span><span class="sxs-lookup"><span data-stu-id="883ca-160">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="883ca-161">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="883ca-161">IHostApplicationLifetime</span></span>

<span data-ttu-id="883ca-162">將 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (先前稱為 `IApplicationLifetime`) 服務插入任何類別來處理啟動後和順利關機工作。</span><span class="sxs-lookup"><span data-stu-id="883ca-162">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="883ca-163">介面上的三個屬性是用於註冊應用程式啟動和應用程式關閉事件處理程序方法的取消語彙基元。</span><span class="sxs-lookup"><span data-stu-id="883ca-163">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="883ca-164">介面也包括 `StopApplication` 方法。</span><span class="sxs-lookup"><span data-stu-id="883ca-164">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="883ca-165">下列範例是 `IHostedService` 註冊事件的實作為 `IHostApplicationLifetime` ：</span><span class="sxs-lookup"><span data-stu-id="883ca-165">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="883ca-166">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="883ca-166">IHostLifetime</span></span>

<span data-ttu-id="883ca-167"><xref:Microsoft.Extensions.Hosting.IHostLifetime> 實作會控制主機啟動及停止的時機。</span><span class="sxs-lookup"><span data-stu-id="883ca-167">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="883ca-168">會使用最後一個註冊的實作。</span><span class="sxs-lookup"><span data-stu-id="883ca-168">The last implementation registered is used.</span></span>

<span data-ttu-id="883ca-169">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` 是預設的 `IHostLifetime` 實作。</span><span class="sxs-lookup"><span data-stu-id="883ca-169">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="883ca-170">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="883ca-170">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="883ca-171">接聽<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT 或 SIGTERM，並呼叫 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> 以啟動關機程式。</span><span class="sxs-lookup"><span data-stu-id="883ca-171">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="883ca-172">會解除封鎖 [RunAsync](#runasync) 和 [WaitForShutdownAsync](#waitforshutdownasync) 等延伸模組。</span><span class="sxs-lookup"><span data-stu-id="883ca-172">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="883ca-173">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="883ca-173">IHostEnvironment</span></span>

<span data-ttu-id="883ca-174">將服務插入至 <xref:Microsoft.Extensions.Hosting.IHostEnvironment> 類別，以取得下列設定的相關資訊：</span><span class="sxs-lookup"><span data-stu-id="883ca-174">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="883ca-175">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="883ca-175">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="883ca-176">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="883ca-176">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="883ca-177">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="883ca-177">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="883ca-178">Web apps 會執行 `IWebHostEnvironment` 介面，此介面會繼承 `IHostEnvironment` 並新增 [WebRootPath](#webroot)。</span><span class="sxs-lookup"><span data-stu-id="883ca-178">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="883ca-179">主機組態</span><span class="sxs-lookup"><span data-stu-id="883ca-179">Host configuration</span></span>

<span data-ttu-id="883ca-180">主機組態用於 <xref:Microsoft.Extensions.Hosting.IHostEnvironment> 實作的屬性。</span><span class="sxs-lookup"><span data-stu-id="883ca-180">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="883ca-181">主機組態位於 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 內的 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration)。</span><span class="sxs-lookup"><span data-stu-id="883ca-181">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="883ca-182">在 `ConfigureAppConfiguration` 之後，應用程式組態會取代 `HostBuilderContext.Configuration`。</span><span class="sxs-lookup"><span data-stu-id="883ca-182">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="883ca-183">若要新增主機組態，請呼叫 `IHostBuilder` 上的 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>。</span><span class="sxs-lookup"><span data-stu-id="883ca-183">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="883ca-184">`ConfigureHostConfiguration` 可以多次呼叫，其結果是累加的。</span><span class="sxs-lookup"><span data-stu-id="883ca-184">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="883ca-185">主機會使用指定索引鍵上最後設定值的任何選項。</span><span class="sxs-lookup"><span data-stu-id="883ca-185">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="883ca-186">包含前置詞 `DOTNET_` 和命令列引數的環境變數提供者 `CreateDefaultBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="883ca-186">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="883ca-187">針對 Web 應用程式，會新增具有前置詞 `ASPNETCORE_` 的環境變數提供者。</span><span class="sxs-lookup"><span data-stu-id="883ca-187">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="883ca-188">讀取環境變數時，就會移除前置詞。</span><span class="sxs-lookup"><span data-stu-id="883ca-188">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="883ca-189">例如，`ASPNETCORE_ENVIRONMENT` 的環境變數值會變成 `environment` 索引鍵的主機組態值。</span><span class="sxs-lookup"><span data-stu-id="883ca-189">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="883ca-190">下列範例會建立主機組態：</span><span class="sxs-lookup"><span data-stu-id="883ca-190">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="883ca-191">應用程式組態</span><span class="sxs-lookup"><span data-stu-id="883ca-191">App configuration</span></span>

<span data-ttu-id="883ca-192">應用程式組態的建立方式是在 `IHostBuilder` 上呼叫 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>。</span><span class="sxs-lookup"><span data-stu-id="883ca-192">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="883ca-193">`ConfigureAppConfiguration` 可以多次呼叫，其結果是累加的。</span><span class="sxs-lookup"><span data-stu-id="883ca-193">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="883ca-194">應用程式會使用指定索引鍵上最後設定值的任何選項。</span><span class="sxs-lookup"><span data-stu-id="883ca-194">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="883ca-195">由 `ConfigureAppConfiguration` 建立的組態位於 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*)，可用於後續作業並用作 DI 中的服務。</span><span class="sxs-lookup"><span data-stu-id="883ca-195">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="883ca-196">主機組態也會新增至應用程式組態。</span><span class="sxs-lookup"><span data-stu-id="883ca-196">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="883ca-197">如需詳細資訊，請參閱 [ASP.NET Core 中的組態](xref:fundamentals/configuration/index#configureappconfiguration)。</span><span class="sxs-lookup"><span data-stu-id="883ca-197">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="883ca-198">所有應用程式類型的設定</span><span class="sxs-lookup"><span data-stu-id="883ca-198">Settings for all app types</span></span>

<span data-ttu-id="883ca-199">本節列出適用於 HTTP 和非 HTTP 工作負載的主機設定。</span><span class="sxs-lookup"><span data-stu-id="883ca-199">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="883ca-200">根據預設，用來設定這些設定的環境變數可以具有 `DOTNET_` 或 `ASPNETCORE_` 前置詞。</span><span class="sxs-lookup"><span data-stu-id="883ca-200">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span> <span data-ttu-id="883ca-201">如需詳細資訊，請參閱預設建立器 [設定](#default-builder-settings) 一節。</span><span class="sxs-lookup"><span data-stu-id="883ca-201">For more information, see the [Default builder settings](#default-builder-settings) section.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="883ca-202">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="883ca-202">ApplicationName</span></span>

<span data-ttu-id="883ca-203">[IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) 屬性是在主機建構期間從主機組態當中設定。</span><span class="sxs-lookup"><span data-stu-id="883ca-203">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="883ca-204">**機碼**：`applicationName`</span><span class="sxs-lookup"><span data-stu-id="883ca-204">**Key**: `applicationName`</span></span>  
<span data-ttu-id="883ca-205">**類型**：`string`</span><span class="sxs-lookup"><span data-stu-id="883ca-205">**Type**: `string`</span></span>  
<span data-ttu-id="883ca-206">**預設值**：包含應用程式進入點的元件名稱。</span><span class="sxs-lookup"><span data-stu-id="883ca-206">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="883ca-207">**環境變數**： `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="883ca-207">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="883ca-208">若要設定此值，請使用環境變數。</span><span class="sxs-lookup"><span data-stu-id="883ca-208">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="883ca-209">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="883ca-209">ContentRoot</span></span>

<span data-ttu-id="883ca-210">[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) 屬性會決定主機從哪裡開始搜尋內容檔案。</span><span class="sxs-lookup"><span data-stu-id="883ca-210">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="883ca-211">如果路徑不存在，就無法啟動主機。</span><span class="sxs-lookup"><span data-stu-id="883ca-211">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="883ca-212">**機碼**：`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="883ca-212">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="883ca-213">**類型**：`string`</span><span class="sxs-lookup"><span data-stu-id="883ca-213">**Type**: `string`</span></span>  
<span data-ttu-id="883ca-214">**預設值**：應用程式元件所在的資料夾。</span><span class="sxs-lookup"><span data-stu-id="883ca-214">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="883ca-215">**環境變數**： `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="883ca-215">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="883ca-216">若要設定此值，請使用環境變數或呼叫 `IHostBuilder` 上的 `UseContentRoot`：</span><span class="sxs-lookup"><span data-stu-id="883ca-216">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="883ca-217">如需詳細資訊，請參閱</span><span class="sxs-lookup"><span data-stu-id="883ca-217">For more information, see:</span></span>

* [<span data-ttu-id="883ca-218">基本概念：內容根目錄</span><span class="sxs-lookup"><span data-stu-id="883ca-218">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="883ca-219">WebRoot</span><span class="sxs-lookup"><span data-stu-id="883ca-219">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="883ca-220">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="883ca-220">EnvironmentName</span></span>

<span data-ttu-id="883ca-221">[IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) 屬性可以設為任何值。</span><span class="sxs-lookup"><span data-stu-id="883ca-221">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="883ca-222">架構定義的值包括 `Development`、`Staging` 和 `Production`。</span><span class="sxs-lookup"><span data-stu-id="883ca-222">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="883ca-223">值不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="883ca-223">Values aren't case-sensitive.</span></span>

<span data-ttu-id="883ca-224">**機碼**：`environment`</span><span class="sxs-lookup"><span data-stu-id="883ca-224">**Key**: `environment`</span></span>  
<span data-ttu-id="883ca-225">**類型**：`string`</span><span class="sxs-lookup"><span data-stu-id="883ca-225">**Type**: `string`</span></span>  
<span data-ttu-id="883ca-226">**預設值**： `Production`</span><span class="sxs-lookup"><span data-stu-id="883ca-226">**Default**: `Production`</span></span>  
<span data-ttu-id="883ca-227">**環境變數**： `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="883ca-227">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="883ca-228">若要設定此值，請使用環境變數或呼叫 `IHostBuilder` 上的 `UseEnvironment`：</span><span class="sxs-lookup"><span data-stu-id="883ca-228">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="883ca-229">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="883ca-229">ShutdownTimeout</span></span>

<span data-ttu-id="883ca-230">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) 會設定 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 的逾時。</span><span class="sxs-lookup"><span data-stu-id="883ca-230">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="883ca-231">預設值是五秒鐘。</span><span class="sxs-lookup"><span data-stu-id="883ca-231">The default value is five seconds.</span></span>  <span data-ttu-id="883ca-232">在逾時期間，主機會：</span><span class="sxs-lookup"><span data-stu-id="883ca-232">During the timeout period, the host:</span></span>

* <span data-ttu-id="883ca-233">觸發 [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping)。</span><span class="sxs-lookup"><span data-stu-id="883ca-233">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="883ca-234">嘗試停止託管的服務，並記錄無法停止之服務的錯誤。</span><span class="sxs-lookup"><span data-stu-id="883ca-234">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="883ca-235">如果在所有的託管服務停止之前逾時期限已到期，則應用程式關閉時，會停止任何剩餘的作用中服務。</span><span class="sxs-lookup"><span data-stu-id="883ca-235">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="883ca-236">即使服務尚未完成處理也會停止。</span><span class="sxs-lookup"><span data-stu-id="883ca-236">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="883ca-237">如果服務需要更多時間才能停止，請增加逾時。</span><span class="sxs-lookup"><span data-stu-id="883ca-237">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="883ca-238">**機碼**：`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="883ca-238">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="883ca-239">**類型**：`int`</span><span class="sxs-lookup"><span data-stu-id="883ca-239">**Type**: `int`</span></span>  
<span data-ttu-id="883ca-240">**預設值**：5秒</span><span class="sxs-lookup"><span data-stu-id="883ca-240">**Default**: 5 seconds</span></span>  
<span data-ttu-id="883ca-241">**環境變數**： `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="883ca-241">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="883ca-242">若要設定此值，請使用環境變數或設定 `HostOptions`。</span><span class="sxs-lookup"><span data-stu-id="883ca-242">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="883ca-243">下列範例將逾時設為 20 秒：</span><span class="sxs-lookup"><span data-stu-id="883ca-243">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="883ca-244">變更時停用應用程式設定重載</span><span class="sxs-lookup"><span data-stu-id="883ca-244">Disable app configuration reload on change</span></span>

<span data-ttu-id="883ca-245">依 [預設](xref:fundamentals/configuration/index#default)， *appsettings.json* 和 *appsettings。環境}。* 當檔案變更時，會重載 json。</span><span class="sxs-lookup"><span data-stu-id="883ca-245">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="883ca-246">若要停用 ASP.NET Core 5.0 或更新版本中的重載行為，請將機 `hostBuilder:reloadConfigOnChange` 碼設定為 `false` 。</span><span class="sxs-lookup"><span data-stu-id="883ca-246">To disable this reload behavior in ASP.NET Core 5.0 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="883ca-247">**機碼**：`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="883ca-247">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="883ca-248">**類型**： `bool` (`true` 或 `1`) </span><span class="sxs-lookup"><span data-stu-id="883ca-248">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="883ca-249">**預設值**： `true`</span><span class="sxs-lookup"><span data-stu-id="883ca-249">**Default**: `true`</span></span>  
<span data-ttu-id="883ca-250">**命令列引數**： `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="883ca-250">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="883ca-251">**環境變數**： `<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="883ca-251">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="883ca-252">冒號 (`:`) 分隔符號不適用於所有平臺上的環境變數階層式索引鍵。</span><span class="sxs-lookup"><span data-stu-id="883ca-252">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="883ca-253">如需詳細資訊，請參閱 [環境變數](xref:fundamentals/configuration/index#environment-variables)。</span><span class="sxs-lookup"><span data-stu-id="883ca-253">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="883ca-254">Web 應用程式的設定</span><span class="sxs-lookup"><span data-stu-id="883ca-254">Settings for web apps</span></span>

<span data-ttu-id="883ca-255">某些主機設定僅適用於 HTTP 工作負載。</span><span class="sxs-lookup"><span data-stu-id="883ca-255">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="883ca-256">根據預設，用來設定這些設定的環境變數可以具有 `DOTNET_` 或 `ASPNETCORE_` 前置詞。</span><span class="sxs-lookup"><span data-stu-id="883ca-256">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="883ca-257">`IWebHostBuilder` 上的擴充方法適用於這些設定。</span><span class="sxs-lookup"><span data-stu-id="883ca-257">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="883ca-258">示範如何呼叫擴充方法的程式碼範例假設 `webBuilder` 是 `IWebHostBuilder` 的執行個體，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="883ca-258">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="883ca-259">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="883ca-259">CaptureStartupErrors</span></span>

<span data-ttu-id="883ca-260">當它為 `false` 時，啟動期間發生的錯誤會導致主機結束。</span><span class="sxs-lookup"><span data-stu-id="883ca-260">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="883ca-261">當它為 `true` 時，主機會擷取啟動期間的例外狀況，並嘗試啟動伺服器。</span><span class="sxs-lookup"><span data-stu-id="883ca-261">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="883ca-262">**機碼**：`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="883ca-262">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="883ca-263">**類型**： `bool` (`true` 或 `1`) </span><span class="sxs-lookup"><span data-stu-id="883ca-263">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="883ca-264">**預設值**：預設為 `false`，除非應用程式執行時在 IIS 背後有 Kestrel，此時預設值即為 `true`。</span><span class="sxs-lookup"><span data-stu-id="883ca-264">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="883ca-265">**環境變數**： `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="883ca-265">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="883ca-266">若要設定此值，請使用組態或呼叫 `CaptureStartupErrors`：</span><span class="sxs-lookup"><span data-stu-id="883ca-266">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="883ca-267">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="883ca-267">DetailedErrors</span></span>

<span data-ttu-id="883ca-268">啟用時 (或當環境為 `Development` 時)，應用程式會擷取詳細錯誤。</span><span class="sxs-lookup"><span data-stu-id="883ca-268">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="883ca-269">**機碼**：`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="883ca-269">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="883ca-270">**類型**： `bool` (`true` 或 `1`) </span><span class="sxs-lookup"><span data-stu-id="883ca-270">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="883ca-271">**預設值**： `false`</span><span class="sxs-lookup"><span data-stu-id="883ca-271">**Default**: `false`</span></span>  
<span data-ttu-id="883ca-272">**環境變數**： `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="883ca-272">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="883ca-273">若要設定此值，請使用組態或呼叫 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="883ca-273">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="883ca-274">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="883ca-274">HostingStartupAssemblies</span></span>

<span data-ttu-id="883ca-275">在啟動時載入的裝載啟動組件字串，以分號分隔。</span><span class="sxs-lookup"><span data-stu-id="883ca-275">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="883ca-276">雖然設定值會預設為空字串，但裝載啟動組件一律會包含應用程式的組件。</span><span class="sxs-lookup"><span data-stu-id="883ca-276">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="883ca-277">提供裝載啟動組件時，它們會新增至應用程式的組件，以便在應用程式在啟動時建置其通用服務時載入。</span><span class="sxs-lookup"><span data-stu-id="883ca-277">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="883ca-278">**機碼**：`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="883ca-278">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="883ca-279">**類型**：`string`</span><span class="sxs-lookup"><span data-stu-id="883ca-279">**Type**: `string`</span></span>  
<span data-ttu-id="883ca-280">**預設值**：空字串</span><span class="sxs-lookup"><span data-stu-id="883ca-280">**Default**: Empty string</span></span>  
<span data-ttu-id="883ca-281">**環境變數**： `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="883ca-281">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="883ca-282">若要設定此值，請使用組態或呼叫 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="883ca-282">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="883ca-283">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="883ca-283">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="883ca-284">在啟動時排除以分號分隔的裝載啟動組件字串。</span><span class="sxs-lookup"><span data-stu-id="883ca-284">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="883ca-285">**機碼**：`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="883ca-285">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="883ca-286">**類型**：`string`</span><span class="sxs-lookup"><span data-stu-id="883ca-286">**Type**: `string`</span></span>  
<span data-ttu-id="883ca-287">**預設值**：空字串</span><span class="sxs-lookup"><span data-stu-id="883ca-287">**Default**: Empty string</span></span>  
<span data-ttu-id="883ca-288">**環境變數**： `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="883ca-288">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="883ca-289">若要設定此值，請使用組態或呼叫 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="883ca-289">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="883ca-290">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="883ca-290">HTTPS_Port</span></span>

<span data-ttu-id="883ca-291">HTTPS 重新導向連接埠。</span><span class="sxs-lookup"><span data-stu-id="883ca-291">The HTTPS redirect port.</span></span> <span data-ttu-id="883ca-292">用於[強制 HTTPS](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="883ca-292">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="883ca-293">**機碼**：`https_port`</span><span class="sxs-lookup"><span data-stu-id="883ca-293">**Key**: `https_port`</span></span>  
<span data-ttu-id="883ca-294">**類型**：`string`</span><span class="sxs-lookup"><span data-stu-id="883ca-294">**Type**: `string`</span></span>  
<span data-ttu-id="883ca-295">**預設** 值：未設定預設值。</span><span class="sxs-lookup"><span data-stu-id="883ca-295">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="883ca-296">**環境變數**： `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="883ca-296">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="883ca-297">若要設定此值，請使用組態或呼叫 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="883ca-297">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="883ca-298">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="883ca-298">PreferHostingUrls</span></span>

<span data-ttu-id="883ca-299">指出主機是否應接聽使用設定的 Url， `IWebHostBuilder` 而不是使用執行時所設定的 url `IServer` 。</span><span class="sxs-lookup"><span data-stu-id="883ca-299">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="883ca-300">**機碼**：`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="883ca-300">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="883ca-301">**類型**： `bool` (`true` 或 `1`) </span><span class="sxs-lookup"><span data-stu-id="883ca-301">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="883ca-302">**預設值**： `true`</span><span class="sxs-lookup"><span data-stu-id="883ca-302">**Default**: `true`</span></span>  
<span data-ttu-id="883ca-303">**環境變數**： `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="883ca-303">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="883ca-304">若要設定此值，請使用環境變數或呼叫 `PreferHostingUrls`：</span><span class="sxs-lookup"><span data-stu-id="883ca-304">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="883ca-305">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="883ca-305">PreventHostingStartup</span></span>

<span data-ttu-id="883ca-306">可防止自動載入裝載啟動組件，包括應用程式組件所設定的裝載啟動組件。</span><span class="sxs-lookup"><span data-stu-id="883ca-306">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="883ca-307">如需詳細資訊，請參閱<xref:fundamentals/configuration/platform-specific-configuration>。</span><span class="sxs-lookup"><span data-stu-id="883ca-307">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="883ca-308">**機碼**：`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="883ca-308">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="883ca-309">**類型**： `bool` (`true` 或 `1`) </span><span class="sxs-lookup"><span data-stu-id="883ca-309">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="883ca-310">**預設值**： `false`</span><span class="sxs-lookup"><span data-stu-id="883ca-310">**Default**: `false`</span></span>  
<span data-ttu-id="883ca-311">**環境變數**： `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="883ca-311">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="883ca-312">若要設定此值，請使用環境變數或呼叫 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="883ca-312">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="883ca-313">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="883ca-313">StartupAssembly</span></span>

<span data-ttu-id="883ca-314">要搜尋 `Startup` 類別的組件。</span><span class="sxs-lookup"><span data-stu-id="883ca-314">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="883ca-315">**機碼**：`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="883ca-315">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="883ca-316">**類型**：`string`</span><span class="sxs-lookup"><span data-stu-id="883ca-316">**Type**: `string`</span></span>  
<span data-ttu-id="883ca-317">**預設值**：應用程式的組件</span><span class="sxs-lookup"><span data-stu-id="883ca-317">**Default**: The app's assembly</span></span>  
<span data-ttu-id="883ca-318">**環境變數**： `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="883ca-318">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="883ca-319">若要設定此值，請使用環境變數或呼叫 `UseStartup`。</span><span class="sxs-lookup"><span data-stu-id="883ca-319">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="883ca-320">`UseStartup` 可以採用組件名稱 (`string`) 或類型 (`TStartup`)。</span><span class="sxs-lookup"><span data-stu-id="883ca-320">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="883ca-321">如果呼叫多個 `UseStartup` 方法，最後一個將會優先。</span><span class="sxs-lookup"><span data-stu-id="883ca-321">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="883ca-322">URL</span><span class="sxs-lookup"><span data-stu-id="883ca-322">URLs</span></span>

<span data-ttu-id="883ca-323">以分號分隔的 IP 位址或主機位址，包含伺服器應接聽要求的連接埠和通訊協定。</span><span class="sxs-lookup"><span data-stu-id="883ca-323">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="883ca-324">例如： `http://localhost:123` 。</span><span class="sxs-lookup"><span data-stu-id="883ca-324">For example, `http://localhost:123`.</span></span> <span data-ttu-id="883ca-325">使用 "\*"，表示伺服器應接聽任何 IP 位址或主機名稱上的要求，並使用指定的連接埠和通訊協定 (例如，`http://*:5000`)。</span><span class="sxs-lookup"><span data-stu-id="883ca-325">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="883ca-326">通訊協定 (`http://` 或 `https://`) 必須包含在每個 URL 中。</span><span class="sxs-lookup"><span data-stu-id="883ca-326">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="883ca-327">支援的格式會依伺服器而有所不同。</span><span class="sxs-lookup"><span data-stu-id="883ca-327">Supported formats vary among servers.</span></span>

<span data-ttu-id="883ca-328">**機碼**：`urls`</span><span class="sxs-lookup"><span data-stu-id="883ca-328">**Key**: `urls`</span></span>  
<span data-ttu-id="883ca-329">**類型**：`string`</span><span class="sxs-lookup"><span data-stu-id="883ca-329">**Type**: `string`</span></span>  
<span data-ttu-id="883ca-330">**預設值**： `http://localhost:5000` 和 `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="883ca-330">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="883ca-331">**環境變數**： `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="883ca-331">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="883ca-332">若要設定此值，請使用環境變數或呼叫 `UseUrls`：</span><span class="sxs-lookup"><span data-stu-id="883ca-332">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="883ca-333">Kestrel 有它自己的端點設定 API。</span><span class="sxs-lookup"><span data-stu-id="883ca-333">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="883ca-334">如需詳細資訊，請參閱<xref:fundamentals/servers/kestrel/endpoints>。</span><span class="sxs-lookup"><span data-stu-id="883ca-334">For more information, see <xref:fundamentals/servers/kestrel/endpoints>.</span></span>

### <a name="webroot"></a><span data-ttu-id="883ca-335">WebRoot</span><span class="sxs-lookup"><span data-stu-id="883ca-335">WebRoot</span></span>

<span data-ttu-id="883ca-336">[IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath)屬性會決定應用程式靜態資產的相對路徑。</span><span class="sxs-lookup"><span data-stu-id="883ca-336">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="883ca-337">如果路徑不存在，則會使用無作業檔案提供者。</span><span class="sxs-lookup"><span data-stu-id="883ca-337">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="883ca-338">**機碼**：`webroot`</span><span class="sxs-lookup"><span data-stu-id="883ca-338">**Key**: `webroot`</span></span>  
<span data-ttu-id="883ca-339">**類型**：`string`</span><span class="sxs-lookup"><span data-stu-id="883ca-339">**Type**: `string`</span></span>  
<span data-ttu-id="883ca-340">**預設** 值：預設值為 `wwwroot` 。</span><span class="sxs-lookup"><span data-stu-id="883ca-340">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="883ca-341">*{Content root}/wwwroot* 的路徑必須存在。</span><span class="sxs-lookup"><span data-stu-id="883ca-341">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="883ca-342">**環境變數**： `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="883ca-342">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="883ca-343">若要設定此值，請使用環境變數或呼叫 `IWebHostBuilder` 上的 `UseWebRoot`：</span><span class="sxs-lookup"><span data-stu-id="883ca-343">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="883ca-344">如需詳細資訊，請參閱</span><span class="sxs-lookup"><span data-stu-id="883ca-344">For more information, see:</span></span>

* [<span data-ttu-id="883ca-345">基本概念： Web 根目錄</span><span class="sxs-lookup"><span data-stu-id="883ca-345">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="883ca-346">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="883ca-346">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="883ca-347">管理主機存留期</span><span class="sxs-lookup"><span data-stu-id="883ca-347">Manage the host lifetime</span></span>

<span data-ttu-id="883ca-348">在建置的 <xref:Microsoft.Extensions.Hosting.IHost> 實作上呼叫方法來啟動和停止應用程式。</span><span class="sxs-lookup"><span data-stu-id="883ca-348">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="883ca-349">這些方法會影響所有在服務容器中註冊的 <xref:Microsoft.Extensions.Hosting.IHostedService> 實作。</span><span class="sxs-lookup"><span data-stu-id="883ca-349">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="883ca-350">執行</span><span class="sxs-lookup"><span data-stu-id="883ca-350">Run</span></span>

<span data-ttu-id="883ca-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> 會執行應用程式並封鎖呼叫執行緒，直到主機關閉為止。</span><span class="sxs-lookup"><span data-stu-id="883ca-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="883ca-352">RunAsync</span><span class="sxs-lookup"><span data-stu-id="883ca-352">RunAsync</span></span>

<span data-ttu-id="883ca-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> 會執行應用程式，並傳回觸發取消語彙基元或關機時所完成的 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="883ca-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="883ca-354">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="883ca-354">RunConsoleAsync</span></span>

<span data-ttu-id="883ca-355"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>啟用主控台支援、建立並啟動主機，以及等候<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT 或 SIGTERM 關閉。</span><span class="sxs-lookup"><span data-stu-id="883ca-355"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="883ca-356">開始</span><span class="sxs-lookup"><span data-stu-id="883ca-356">Start</span></span>

<span data-ttu-id="883ca-357"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> 會同步啟動主機。</span><span class="sxs-lookup"><span data-stu-id="883ca-357"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="883ca-358">StartAsync</span><span class="sxs-lookup"><span data-stu-id="883ca-358">StartAsync</span></span>

<span data-ttu-id="883ca-359"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> 會啟動主機，並傳回觸發取消語彙基元或關機時所完成的 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="883ca-359"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="883ca-360"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> 在 `StartAsync` 開始時呼叫，並等到完成後再繼續進行。</span><span class="sxs-lookup"><span data-stu-id="883ca-360"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="883ca-361">這可用來將啟動延遲到外部事件發出訊號為止。</span><span class="sxs-lookup"><span data-stu-id="883ca-361">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="883ca-362">StopAsync</span><span class="sxs-lookup"><span data-stu-id="883ca-362">StopAsync</span></span>

<span data-ttu-id="883ca-363"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> 會嘗試在提供的逾時內停止主機。</span><span class="sxs-lookup"><span data-stu-id="883ca-363"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="883ca-364">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="883ca-364">WaitForShutdown</span></span>

<span data-ttu-id="883ca-365"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>封鎖呼叫的執行緒，直到 IHostLifetime 觸發關機（例如 via <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT 或 SIGTERM）。</span><span class="sxs-lookup"><span data-stu-id="883ca-365"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="883ca-366">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="883ca-366">WaitForShutdownAsync</span></span>

<span data-ttu-id="883ca-367"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> 會傳回透過指定語彙基元觸發關機時所完成的 <xref:System.Threading.Tasks.Task>，並呼叫 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>。</span><span class="sxs-lookup"><span data-stu-id="883ca-367"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="883ca-368">外部控制</span><span class="sxs-lookup"><span data-stu-id="883ca-368">External control</span></span>

<span data-ttu-id="883ca-369">主機存留期直接控制可以使用可從外部呼叫的方法來達成：</span><span class="sxs-lookup"><span data-stu-id="883ca-369">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="883ca-370">ASP.NET 核心範本會建立 .NET Core 泛型主機 (<xref:Microsoft.Extensions.Hosting.HostBuilder>) 。</span><span class="sxs-lookup"><span data-stu-id="883ca-370">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="883ca-371">本主題提供在 ASP.NET Core 中使用 .NET 泛型主機的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="883ca-371">This topic provides information on using .NET Generic Host in ASP.NET Core.</span></span> <span data-ttu-id="883ca-372">如需在主控台應用程式中使用 .NET 泛型主機的詳細資訊，請參閱 [.Net 泛型主機](/dotnet/core/extensions/generic-host)。</span><span class="sxs-lookup"><span data-stu-id="883ca-372">For information on using .NET Generic Host in console apps, see [.NET Generic Host](/dotnet/core/extensions/generic-host).</span></span>

## <a name="host-definition"></a><span data-ttu-id="883ca-373">主機定義</span><span class="sxs-lookup"><span data-stu-id="883ca-373">Host definition</span></span>

<span data-ttu-id="883ca-374">「主機」是封裝所有應用程式資源的物件，例如：</span><span class="sxs-lookup"><span data-stu-id="883ca-374">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="883ca-375">相依性插入 (DI)</span><span class="sxs-lookup"><span data-stu-id="883ca-375">Dependency injection (DI)</span></span>
* <span data-ttu-id="883ca-376">記錄</span><span class="sxs-lookup"><span data-stu-id="883ca-376">Logging</span></span>
* <span data-ttu-id="883ca-377">組態</span><span class="sxs-lookup"><span data-stu-id="883ca-377">Configuration</span></span>
* <span data-ttu-id="883ca-378">`IHostedService` 實作</span><span class="sxs-lookup"><span data-stu-id="883ca-378">`IHostedService` implementations</span></span>

<span data-ttu-id="883ca-379">當主機啟動時，它會呼叫 <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> <xref:Microsoft.Extensions.Hosting.IHostedService> 服務容器的託管服務集合中註冊的每個執行。</span><span class="sxs-lookup"><span data-stu-id="883ca-379">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="883ca-380">在 Web 應用程式中，其中一個 `IHostedService` 實作是一種 Web 服務，負責啟動 [HTTP 伺服器實作](xref:fundamentals/index#servers)。</span><span class="sxs-lookup"><span data-stu-id="883ca-380">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="883ca-381">在單一物件中包含所有應用程式相互依存資源的主要理由便是生命週期管理：控制應用程式的啟動及順利關機。</span><span class="sxs-lookup"><span data-stu-id="883ca-381">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="883ca-382">設定主機</span><span class="sxs-lookup"><span data-stu-id="883ca-382">Set up a host</span></span>

<span data-ttu-id="883ca-383">主機通常由 `Program` 類別的程式碼來設定、建置並執行。</span><span class="sxs-lookup"><span data-stu-id="883ca-383">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="883ca-384">`Main` 方法：</span><span class="sxs-lookup"><span data-stu-id="883ca-384">The `Main` method:</span></span>

* <span data-ttu-id="883ca-385">呼叫 `CreateHostBuilder` 方法來建立及設定建立器物件。</span><span class="sxs-lookup"><span data-stu-id="883ca-385">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="883ca-386">在建立器物件上呼叫 `Build` 和 `Run` 方法。</span><span class="sxs-lookup"><span data-stu-id="883ca-386">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="883ca-387">ASP.NET Core web 範本會產生下列程式碼來建立泛型主機：</span><span class="sxs-lookup"><span data-stu-id="883ca-387">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

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

<span data-ttu-id="883ca-388">下列程式碼會使用非 HTTP 工作負載來建立泛型主機。</span><span class="sxs-lookup"><span data-stu-id="883ca-388">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="883ca-389">`IHostedService`執行會新增至 DI 容器：</span><span class="sxs-lookup"><span data-stu-id="883ca-389">The `IHostedService` implementation is added to the DI container:</span></span>

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

<span data-ttu-id="883ca-390">針對 HTTP 工作負載，`Main` 方法相同，但 `CreateHostBuilder` 會呼叫 `ConfigureWebHostDefaults`：</span><span class="sxs-lookup"><span data-stu-id="883ca-390">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="883ca-391">上述程式碼是由 ASP.NET Core 範本所產生。</span><span class="sxs-lookup"><span data-stu-id="883ca-391">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="883ca-392">如果應用程式使用 Entity Framework Core，請勿變更 `CreateHostBuilder` 方法的名稱或簽章。</span><span class="sxs-lookup"><span data-stu-id="883ca-392">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="883ca-393">[Entity Framework Core 工具](/ef/core/miscellaneous/cli/)預期找到 `CreateHostBuilder` 方法，其在不執行應用程式的情況下設定主機。</span><span class="sxs-lookup"><span data-stu-id="883ca-393">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="883ca-394">如需詳細資訊，請參閱[設計階段 DbContext 建立](/ef/core/miscellaneous/cli/dbcontext-creation)。</span><span class="sxs-lookup"><span data-stu-id="883ca-394">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="883ca-395">預設建立器設定</span><span class="sxs-lookup"><span data-stu-id="883ca-395">Default builder settings</span></span>

<span data-ttu-id="883ca-396"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> 方法：</span><span class="sxs-lookup"><span data-stu-id="883ca-396">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> method:</span></span>

* <span data-ttu-id="883ca-397">將 [內容根目錄](xref:fundamentals/index#content-root) 設定為所傳回的路徑 <xref:System.IO.Directory.GetCurrentDirectory%2A> 。</span><span class="sxs-lookup"><span data-stu-id="883ca-397">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory%2A>.</span></span>
* <span data-ttu-id="883ca-398">從下列項目載入主機組態：</span><span class="sxs-lookup"><span data-stu-id="883ca-398">Loads host configuration from:</span></span>
  * <span data-ttu-id="883ca-399">前面加上的環境變數 `DOTNET_` 。</span><span class="sxs-lookup"><span data-stu-id="883ca-399">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="883ca-400">命令列引數。</span><span class="sxs-lookup"><span data-stu-id="883ca-400">Command-line arguments.</span></span>
* <span data-ttu-id="883ca-401">從下列項目載入應用程式組態：</span><span class="sxs-lookup"><span data-stu-id="883ca-401">Loads app configuration from:</span></span>
  * <span data-ttu-id="883ca-402">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="883ca-402">*appsettings.json*.</span></span>
  * <span data-ttu-id="883ca-403">*appsettings.{Environment}.json*</span><span class="sxs-lookup"><span data-stu-id="883ca-403">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="883ca-404">應用程式在 `Development` 環境中執行時的[使用者密碼](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="883ca-404">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="883ca-405">環境變數。</span><span class="sxs-lookup"><span data-stu-id="883ca-405">Environment variables.</span></span>
  * <span data-ttu-id="883ca-406">命令列引數。</span><span class="sxs-lookup"><span data-stu-id="883ca-406">Command-line arguments.</span></span>
* <span data-ttu-id="883ca-407">新增下列[記錄](xref:fundamentals/logging/index)提供者：</span><span class="sxs-lookup"><span data-stu-id="883ca-407">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="883ca-408">主控台</span><span class="sxs-lookup"><span data-stu-id="883ca-408">Console</span></span>
  * <span data-ttu-id="883ca-409">偵錯</span><span class="sxs-lookup"><span data-stu-id="883ca-409">Debug</span></span>
  * <span data-ttu-id="883ca-410">EventSource</span><span class="sxs-lookup"><span data-stu-id="883ca-410">EventSource</span></span>
  * <span data-ttu-id="883ca-411">EventLog (僅當在 Windows 上執行時)</span><span class="sxs-lookup"><span data-stu-id="883ca-411">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="883ca-412">環境為開發時，會啟用[範圍驗證](xref:fundamentals/dependency-injection#scope-validation)和[相依性驗證](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild)。</span><span class="sxs-lookup"><span data-stu-id="883ca-412">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="883ca-413">`ConfigureWebHostDefaults` 方法：</span><span class="sxs-lookup"><span data-stu-id="883ca-413">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="883ca-414">從前面加上的環境變數載入主機設定 `ASPNETCORE_` 。</span><span class="sxs-lookup"><span data-stu-id="883ca-414">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="883ca-415">會將 [Kestrel](xref:fundamentals/servers/kestrel) 伺服器設為網頁伺服器，並使用應用程式的主機組態提供者進行設定。</span><span class="sxs-lookup"><span data-stu-id="883ca-415">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="883ca-416">如需 Kestrel 伺服器的預設選項，請參閱 <xref:fundamentals/servers/kestrel#kestrel-options>。</span><span class="sxs-lookup"><span data-stu-id="883ca-416">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="883ca-417">新增[主機篩選中介軟體](xref:fundamentals/servers/kestrel#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="883ca-417">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="883ca-418">如果等於，則新增 [轉送的標頭中介軟體](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) `ASPNETCORE_FORWARDEDHEADERS_ENABLED` `true` 。</span><span class="sxs-lookup"><span data-stu-id="883ca-418">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="883ca-419">啟用 IIS 整合。</span><span class="sxs-lookup"><span data-stu-id="883ca-419">Enables IIS integration.</span></span> <span data-ttu-id="883ca-420">如需 IIS 預設選項，請參閱 <xref:host-and-deploy/iis/index#iis-options>。</span><span class="sxs-lookup"><span data-stu-id="883ca-420">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="883ca-421">本文稍後的[＜設定所有應用程式類型＞](#settings-for-all-app-types)和[＜Web 應用程式設定＞](#settings-for-web-apps)章節，將說明如何覆寫預設的建立器設定。</span><span class="sxs-lookup"><span data-stu-id="883ca-421">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="883ca-422">架構提供的服務</span><span class="sxs-lookup"><span data-stu-id="883ca-422">Framework-provided services</span></span>

<span data-ttu-id="883ca-423">下列服務會自動註冊：</span><span class="sxs-lookup"><span data-stu-id="883ca-423">The following services are registered automatically:</span></span>

* [<span data-ttu-id="883ca-424">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="883ca-424">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="883ca-425">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="883ca-425">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="883ca-426">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="883ca-426">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="883ca-427">如需架構所提供之服務的詳細資訊，請參閱 <xref:fundamentals/dependency-injection#framework-provided-services> 。</span><span class="sxs-lookup"><span data-stu-id="883ca-427">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="883ca-428">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="883ca-428">IHostApplicationLifetime</span></span>

<span data-ttu-id="883ca-429">將 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (先前稱為 `IApplicationLifetime`) 服務插入任何類別來處理啟動後和順利關機工作。</span><span class="sxs-lookup"><span data-stu-id="883ca-429">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="883ca-430">介面上的三個屬性是用於註冊應用程式啟動和應用程式關閉事件處理程序方法的取消語彙基元。</span><span class="sxs-lookup"><span data-stu-id="883ca-430">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="883ca-431">介面也包括 `StopApplication` 方法。</span><span class="sxs-lookup"><span data-stu-id="883ca-431">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="883ca-432">下列範例是 `IHostedService` 註冊事件的實作為 `IHostApplicationLifetime` ：</span><span class="sxs-lookup"><span data-stu-id="883ca-432">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="883ca-433">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="883ca-433">IHostLifetime</span></span>

<span data-ttu-id="883ca-434"><xref:Microsoft.Extensions.Hosting.IHostLifetime> 實作會控制主機啟動及停止的時機。</span><span class="sxs-lookup"><span data-stu-id="883ca-434">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="883ca-435">會使用最後一個註冊的實作。</span><span class="sxs-lookup"><span data-stu-id="883ca-435">The last implementation registered is used.</span></span>

<span data-ttu-id="883ca-436">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` 是預設的 `IHostLifetime` 實作。</span><span class="sxs-lookup"><span data-stu-id="883ca-436">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="883ca-437">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="883ca-437">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="883ca-438">接聽<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT 或 SIGTERM，並呼叫 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> 以啟動關機程式。</span><span class="sxs-lookup"><span data-stu-id="883ca-438">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> to start the shutdown process.</span></span>
* <span data-ttu-id="883ca-439">會解除封鎖 [RunAsync](#runasync) 和 [WaitForShutdownAsync](#waitforshutdownasync) 等延伸模組。</span><span class="sxs-lookup"><span data-stu-id="883ca-439">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="883ca-440">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="883ca-440">IHostEnvironment</span></span>

<span data-ttu-id="883ca-441">將服務插入至 <xref:Microsoft.Extensions.Hosting.IHostEnvironment> 類別，以取得下列設定的相關資訊：</span><span class="sxs-lookup"><span data-stu-id="883ca-441">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="883ca-442">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="883ca-442">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="883ca-443">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="883ca-443">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="883ca-444">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="883ca-444">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="883ca-445">Web apps 會執行 `IWebHostEnvironment` 介面，此介面會繼承 `IHostEnvironment` 並新增 [WebRootPath](#webroot)。</span><span class="sxs-lookup"><span data-stu-id="883ca-445">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="883ca-446">主機組態</span><span class="sxs-lookup"><span data-stu-id="883ca-446">Host configuration</span></span>

<span data-ttu-id="883ca-447">主機組態用於 <xref:Microsoft.Extensions.Hosting.IHostEnvironment> 實作的屬性。</span><span class="sxs-lookup"><span data-stu-id="883ca-447">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="883ca-448">主機組態位於 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> 內的 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration)。</span><span class="sxs-lookup"><span data-stu-id="883ca-448">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span></span> <span data-ttu-id="883ca-449">在 `ConfigureAppConfiguration` 之後，應用程式組態會取代 `HostBuilderContext.Configuration`。</span><span class="sxs-lookup"><span data-stu-id="883ca-449">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="883ca-450">若要新增主機組態，請呼叫 `IHostBuilder` 上的 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A>。</span><span class="sxs-lookup"><span data-stu-id="883ca-450">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> on `IHostBuilder`.</span></span> <span data-ttu-id="883ca-451">`ConfigureHostConfiguration` 可以多次呼叫，其結果是累加的。</span><span class="sxs-lookup"><span data-stu-id="883ca-451">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="883ca-452">主機會使用指定索引鍵上最後設定值的任何選項。</span><span class="sxs-lookup"><span data-stu-id="883ca-452">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="883ca-453">包含前置詞 `DOTNET_` 和命令列引數的環境變數提供者 `CreateDefaultBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="883ca-453">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="883ca-454">針對 Web 應用程式，會新增具有前置詞 `ASPNETCORE_` 的環境變數提供者。</span><span class="sxs-lookup"><span data-stu-id="883ca-454">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="883ca-455">讀取環境變數時，就會移除前置詞。</span><span class="sxs-lookup"><span data-stu-id="883ca-455">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="883ca-456">例如，`ASPNETCORE_ENVIRONMENT` 的環境變數值會變成 `environment` 索引鍵的主機組態值。</span><span class="sxs-lookup"><span data-stu-id="883ca-456">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="883ca-457">下列範例會建立主機組態：</span><span class="sxs-lookup"><span data-stu-id="883ca-457">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="883ca-458">應用程式組態</span><span class="sxs-lookup"><span data-stu-id="883ca-458">App configuration</span></span>

<span data-ttu-id="883ca-459">應用程式組態的建立方式是在 `IHostBuilder` 上呼叫 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>。</span><span class="sxs-lookup"><span data-stu-id="883ca-459">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="883ca-460">`ConfigureAppConfiguration` 可以多次呼叫，其結果是累加的。</span><span class="sxs-lookup"><span data-stu-id="883ca-460">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="883ca-461">應用程式會使用指定索引鍵上最後設定值的任何選項。</span><span class="sxs-lookup"><span data-stu-id="883ca-461">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="883ca-462">由 `ConfigureAppConfiguration` 建立的組態位於 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*)，可用於後續作業並用作 DI 中的服務。</span><span class="sxs-lookup"><span data-stu-id="883ca-462">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="883ca-463">主機組態也會新增至應用程式組態。</span><span class="sxs-lookup"><span data-stu-id="883ca-463">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="883ca-464">如需詳細資訊，請參閱 [ASP.NET Core 中的組態](xref:fundamentals/configuration/index#configureappconfiguration)。</span><span class="sxs-lookup"><span data-stu-id="883ca-464">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="883ca-465">所有應用程式類型的設定</span><span class="sxs-lookup"><span data-stu-id="883ca-465">Settings for all app types</span></span>

<span data-ttu-id="883ca-466">本節列出適用於 HTTP 和非 HTTP 工作負載的主機設定。</span><span class="sxs-lookup"><span data-stu-id="883ca-466">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="883ca-467">根據預設，用來設定這些設定的環境變數可以具有 `DOTNET_` 或 `ASPNETCORE_` 前置詞。</span><span class="sxs-lookup"><span data-stu-id="883ca-467">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="883ca-468">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="883ca-468">ApplicationName</span></span>

<span data-ttu-id="883ca-469">[IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) 屬性是在主機建構期間從主機組態當中設定。</span><span class="sxs-lookup"><span data-stu-id="883ca-469">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="883ca-470">**機碼**：`applicationName`</span><span class="sxs-lookup"><span data-stu-id="883ca-470">**Key**: `applicationName`</span></span>  
<span data-ttu-id="883ca-471">**類型**：`string`</span><span class="sxs-lookup"><span data-stu-id="883ca-471">**Type**: `string`</span></span>  
<span data-ttu-id="883ca-472">**預設值**：包含應用程式進入點的元件名稱。</span><span class="sxs-lookup"><span data-stu-id="883ca-472">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="883ca-473">**環境變數**： `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="883ca-473">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="883ca-474">若要設定此值，請使用環境變數。</span><span class="sxs-lookup"><span data-stu-id="883ca-474">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="883ca-475">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="883ca-475">ContentRoot</span></span>

<span data-ttu-id="883ca-476">[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) 屬性會決定主機從哪裡開始搜尋內容檔案。</span><span class="sxs-lookup"><span data-stu-id="883ca-476">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="883ca-477">如果路徑不存在，就無法啟動主機。</span><span class="sxs-lookup"><span data-stu-id="883ca-477">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="883ca-478">**機碼**：`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="883ca-478">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="883ca-479">**類型**：`string`</span><span class="sxs-lookup"><span data-stu-id="883ca-479">**Type**: `string`</span></span>  
<span data-ttu-id="883ca-480">**預設值**：應用程式元件所在的資料夾。</span><span class="sxs-lookup"><span data-stu-id="883ca-480">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="883ca-481">**環境變數**： `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="883ca-481">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="883ca-482">若要設定此值，請使用環境變數或呼叫 `IHostBuilder` 上的 `UseContentRoot`：</span><span class="sxs-lookup"><span data-stu-id="883ca-482">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="883ca-483">如需詳細資訊，請參閱</span><span class="sxs-lookup"><span data-stu-id="883ca-483">For more information, see:</span></span>

* [<span data-ttu-id="883ca-484">基本概念：內容根目錄</span><span class="sxs-lookup"><span data-stu-id="883ca-484">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="883ca-485">WebRoot</span><span class="sxs-lookup"><span data-stu-id="883ca-485">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="883ca-486">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="883ca-486">EnvironmentName</span></span>

<span data-ttu-id="883ca-487">[IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) 屬性可以設為任何值。</span><span class="sxs-lookup"><span data-stu-id="883ca-487">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="883ca-488">架構定義的值包括 `Development`、`Staging` 和 `Production`。</span><span class="sxs-lookup"><span data-stu-id="883ca-488">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="883ca-489">值不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="883ca-489">Values aren't case-sensitive.</span></span>

<span data-ttu-id="883ca-490">**機碼**：`environment`</span><span class="sxs-lookup"><span data-stu-id="883ca-490">**Key**: `environment`</span></span>  
<span data-ttu-id="883ca-491">**類型**：`string`</span><span class="sxs-lookup"><span data-stu-id="883ca-491">**Type**: `string`</span></span>  
<span data-ttu-id="883ca-492">**預設值**： `Production`</span><span class="sxs-lookup"><span data-stu-id="883ca-492">**Default**: `Production`</span></span>  
<span data-ttu-id="883ca-493">**環境變數**： `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="883ca-493">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="883ca-494">若要設定此值，請使用環境變數或呼叫 `IHostBuilder` 上的 `UseEnvironment`：</span><span class="sxs-lookup"><span data-stu-id="883ca-494">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="883ca-495">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="883ca-495">ShutdownTimeout</span></span>

<span data-ttu-id="883ca-496">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) 會設定 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 的逾時。</span><span class="sxs-lookup"><span data-stu-id="883ca-496">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="883ca-497">預設值是五秒鐘。</span><span class="sxs-lookup"><span data-stu-id="883ca-497">The default value is five seconds.</span></span>  <span data-ttu-id="883ca-498">在逾時期間，主機會：</span><span class="sxs-lookup"><span data-stu-id="883ca-498">During the timeout period, the host:</span></span>

* <span data-ttu-id="883ca-499">觸發 [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping)。</span><span class="sxs-lookup"><span data-stu-id="883ca-499">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="883ca-500">嘗試停止託管的服務，並記錄無法停止之服務的錯誤。</span><span class="sxs-lookup"><span data-stu-id="883ca-500">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="883ca-501">如果在所有的託管服務停止之前逾時期限已到期，則應用程式關閉時，會停止任何剩餘的作用中服務。</span><span class="sxs-lookup"><span data-stu-id="883ca-501">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="883ca-502">即使服務尚未完成處理也會停止。</span><span class="sxs-lookup"><span data-stu-id="883ca-502">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="883ca-503">如果服務需要更多時間才能停止，請增加逾時。</span><span class="sxs-lookup"><span data-stu-id="883ca-503">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="883ca-504">**機碼**：`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="883ca-504">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="883ca-505">**類型**：`int`</span><span class="sxs-lookup"><span data-stu-id="883ca-505">**Type**: `int`</span></span>  
<span data-ttu-id="883ca-506">**預設值**：5秒</span><span class="sxs-lookup"><span data-stu-id="883ca-506">**Default**: 5 seconds</span></span>  
<span data-ttu-id="883ca-507">**環境變數**： `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="883ca-507">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="883ca-508">若要設定此值，請使用環境變數或設定 `HostOptions`。</span><span class="sxs-lookup"><span data-stu-id="883ca-508">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="883ca-509">下列範例將逾時設為 20 秒：</span><span class="sxs-lookup"><span data-stu-id="883ca-509">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="883ca-510">Web 應用程式的設定</span><span class="sxs-lookup"><span data-stu-id="883ca-510">Settings for web apps</span></span>

<span data-ttu-id="883ca-511">某些主機設定僅適用於 HTTP 工作負載。</span><span class="sxs-lookup"><span data-stu-id="883ca-511">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="883ca-512">根據預設，用來設定這些設定的環境變數可以具有 `DOTNET_` 或 `ASPNETCORE_` 前置詞。</span><span class="sxs-lookup"><span data-stu-id="883ca-512">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="883ca-513">`IWebHostBuilder` 上的擴充方法適用於這些設定。</span><span class="sxs-lookup"><span data-stu-id="883ca-513">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="883ca-514">示範如何呼叫擴充方法的程式碼範例假設 `webBuilder` 是 `IWebHostBuilder` 的執行個體，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="883ca-514">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="883ca-515">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="883ca-515">CaptureStartupErrors</span></span>

<span data-ttu-id="883ca-516">當它為 `false` 時，啟動期間發生的錯誤會導致主機結束。</span><span class="sxs-lookup"><span data-stu-id="883ca-516">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="883ca-517">當它為 `true` 時，主機會擷取啟動期間的例外狀況，並嘗試啟動伺服器。</span><span class="sxs-lookup"><span data-stu-id="883ca-517">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="883ca-518">**機碼**：`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="883ca-518">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="883ca-519">**類型**： `bool` (`true` 或 `1`) </span><span class="sxs-lookup"><span data-stu-id="883ca-519">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="883ca-520">**預設值**：預設為 `false`，除非應用程式執行時在 IIS 背後有 Kestrel，此時預設值即為 `true`。</span><span class="sxs-lookup"><span data-stu-id="883ca-520">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="883ca-521">**環境變數**： `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="883ca-521">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="883ca-522">若要設定此值，請使用組態或呼叫 `CaptureStartupErrors`：</span><span class="sxs-lookup"><span data-stu-id="883ca-522">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="883ca-523">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="883ca-523">DetailedErrors</span></span>

<span data-ttu-id="883ca-524">啟用時 (或當環境為 `Development` 時)，應用程式會擷取詳細錯誤。</span><span class="sxs-lookup"><span data-stu-id="883ca-524">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="883ca-525">**機碼**：`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="883ca-525">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="883ca-526">**類型**： `bool` (`true` 或 `1`) </span><span class="sxs-lookup"><span data-stu-id="883ca-526">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="883ca-527">**預設值**： `false`</span><span class="sxs-lookup"><span data-stu-id="883ca-527">**Default**: `false`</span></span>  
<span data-ttu-id="883ca-528">**環境變數**： `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="883ca-528">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="883ca-529">若要設定此值，請使用組態或呼叫 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="883ca-529">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="883ca-530">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="883ca-530">HostingStartupAssemblies</span></span>

<span data-ttu-id="883ca-531">在啟動時載入的裝載啟動組件字串，以分號分隔。</span><span class="sxs-lookup"><span data-stu-id="883ca-531">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="883ca-532">雖然設定值會預設為空字串，但裝載啟動組件一律會包含應用程式的組件。</span><span class="sxs-lookup"><span data-stu-id="883ca-532">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="883ca-533">提供裝載啟動組件時，它們會新增至應用程式的組件，以便在應用程式在啟動時建置其通用服務時載入。</span><span class="sxs-lookup"><span data-stu-id="883ca-533">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="883ca-534">**機碼**：`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="883ca-534">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="883ca-535">**類型**：`string`</span><span class="sxs-lookup"><span data-stu-id="883ca-535">**Type**: `string`</span></span>  
<span data-ttu-id="883ca-536">**預設值**：空字串</span><span class="sxs-lookup"><span data-stu-id="883ca-536">**Default**: Empty string</span></span>  
<span data-ttu-id="883ca-537">**環境變數**： `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="883ca-537">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="883ca-538">若要設定此值，請使用組態或呼叫 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="883ca-538">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="883ca-539">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="883ca-539">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="883ca-540">在啟動時排除以分號分隔的裝載啟動組件字串。</span><span class="sxs-lookup"><span data-stu-id="883ca-540">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="883ca-541">**機碼**：`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="883ca-541">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="883ca-542">**類型**：`string`</span><span class="sxs-lookup"><span data-stu-id="883ca-542">**Type**: `string`</span></span>  
<span data-ttu-id="883ca-543">**預設值**：空字串</span><span class="sxs-lookup"><span data-stu-id="883ca-543">**Default**: Empty string</span></span>  
<span data-ttu-id="883ca-544">**環境變數**： `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="883ca-544">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="883ca-545">若要設定此值，請使用組態或呼叫 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="883ca-545">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="883ca-546">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="883ca-546">HTTPS_Port</span></span>

<span data-ttu-id="883ca-547">HTTPS 重新導向連接埠。</span><span class="sxs-lookup"><span data-stu-id="883ca-547">The HTTPS redirect port.</span></span> <span data-ttu-id="883ca-548">用於[強制 HTTPS](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="883ca-548">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="883ca-549">**機碼**：`https_port`</span><span class="sxs-lookup"><span data-stu-id="883ca-549">**Key**: `https_port`</span></span>  
<span data-ttu-id="883ca-550">**類型**：`string`</span><span class="sxs-lookup"><span data-stu-id="883ca-550">**Type**: `string`</span></span>  
<span data-ttu-id="883ca-551">**預設** 值：未設定預設值。</span><span class="sxs-lookup"><span data-stu-id="883ca-551">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="883ca-552">**環境變數**： `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="883ca-552">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="883ca-553">若要設定此值，請使用組態或呼叫 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="883ca-553">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="883ca-554">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="883ca-554">PreferHostingUrls</span></span>

<span data-ttu-id="883ca-555">指出主機是否應接聽使用設定的 Url， `IWebHostBuilder` 而不是使用執行時所設定的 url `IServer` 。</span><span class="sxs-lookup"><span data-stu-id="883ca-555">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="883ca-556">**機碼**：`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="883ca-556">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="883ca-557">**類型**： `bool` (`true` 或 `1`) </span><span class="sxs-lookup"><span data-stu-id="883ca-557">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="883ca-558">**預設值**： `true`</span><span class="sxs-lookup"><span data-stu-id="883ca-558">**Default**: `true`</span></span>  
<span data-ttu-id="883ca-559">**環境變數**： `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="883ca-559">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="883ca-560">若要設定此值，請使用環境變數或呼叫 `PreferHostingUrls`：</span><span class="sxs-lookup"><span data-stu-id="883ca-560">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="883ca-561">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="883ca-561">PreventHostingStartup</span></span>

<span data-ttu-id="883ca-562">可防止自動載入裝載啟動組件，包括應用程式組件所設定的裝載啟動組件。</span><span class="sxs-lookup"><span data-stu-id="883ca-562">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="883ca-563">如需詳細資訊，請參閱<xref:fundamentals/configuration/platform-specific-configuration>。</span><span class="sxs-lookup"><span data-stu-id="883ca-563">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="883ca-564">**機碼**：`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="883ca-564">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="883ca-565">**類型**： `bool` (`true` 或 `1`) </span><span class="sxs-lookup"><span data-stu-id="883ca-565">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="883ca-566">**預設值**： `false`</span><span class="sxs-lookup"><span data-stu-id="883ca-566">**Default**: `false`</span></span>  
<span data-ttu-id="883ca-567">**環境變數**： `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="883ca-567">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="883ca-568">若要設定此值，請使用環境變數或呼叫 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="883ca-568">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="883ca-569">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="883ca-569">StartupAssembly</span></span>

<span data-ttu-id="883ca-570">要搜尋 `Startup` 類別的組件。</span><span class="sxs-lookup"><span data-stu-id="883ca-570">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="883ca-571">**機碼**：`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="883ca-571">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="883ca-572">**類型**：`string`</span><span class="sxs-lookup"><span data-stu-id="883ca-572">**Type**: `string`</span></span>  
<span data-ttu-id="883ca-573">**預設值**：應用程式的組件</span><span class="sxs-lookup"><span data-stu-id="883ca-573">**Default**: The app's assembly</span></span>  
<span data-ttu-id="883ca-574">**環境變數**： `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="883ca-574">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="883ca-575">若要設定此值，請使用環境變數或呼叫 `UseStartup`。</span><span class="sxs-lookup"><span data-stu-id="883ca-575">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="883ca-576">`UseStartup` 可以採用組件名稱 (`string`) 或類型 (`TStartup`)。</span><span class="sxs-lookup"><span data-stu-id="883ca-576">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="883ca-577">如果呼叫多個 `UseStartup` 方法，最後一個將會優先。</span><span class="sxs-lookup"><span data-stu-id="883ca-577">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="883ca-578">URL</span><span class="sxs-lookup"><span data-stu-id="883ca-578">URLs</span></span>

<span data-ttu-id="883ca-579">以分號分隔的 IP 位址或主機位址，包含伺服器應接聽要求的連接埠和通訊協定。</span><span class="sxs-lookup"><span data-stu-id="883ca-579">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="883ca-580">例如： `http://localhost:123` 。</span><span class="sxs-lookup"><span data-stu-id="883ca-580">For example, `http://localhost:123`.</span></span> <span data-ttu-id="883ca-581">使用 "\*"，表示伺服器應接聽任何 IP 位址或主機名稱上的要求，並使用指定的連接埠和通訊協定 (例如，`http://*:5000`)。</span><span class="sxs-lookup"><span data-stu-id="883ca-581">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="883ca-582">通訊協定 (`http://` 或 `https://`) 必須包含在每個 URL 中。</span><span class="sxs-lookup"><span data-stu-id="883ca-582">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="883ca-583">支援的格式會依伺服器而有所不同。</span><span class="sxs-lookup"><span data-stu-id="883ca-583">Supported formats vary among servers.</span></span>

<span data-ttu-id="883ca-584">**機碼**：`urls`</span><span class="sxs-lookup"><span data-stu-id="883ca-584">**Key**: `urls`</span></span>  
<span data-ttu-id="883ca-585">**類型**：`string`</span><span class="sxs-lookup"><span data-stu-id="883ca-585">**Type**: `string`</span></span>  
<span data-ttu-id="883ca-586">**預設值**： `http://localhost:5000` 和 `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="883ca-586">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="883ca-587">**環境變數**： `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="883ca-587">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="883ca-588">若要設定此值，請使用環境變數或呼叫 `UseUrls`：</span><span class="sxs-lookup"><span data-stu-id="883ca-588">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="883ca-589">Kestrel 有它自己的端點設定 API。</span><span class="sxs-lookup"><span data-stu-id="883ca-589">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="883ca-590">如需詳細資訊，請參閱<xref:fundamentals/servers/kestrel#endpoint-configuration>。</span><span class="sxs-lookup"><span data-stu-id="883ca-590">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="883ca-591">WebRoot</span><span class="sxs-lookup"><span data-stu-id="883ca-591">WebRoot</span></span>

<span data-ttu-id="883ca-592">[IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath)屬性會決定應用程式靜態資產的相對路徑。</span><span class="sxs-lookup"><span data-stu-id="883ca-592">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="883ca-593">如果路徑不存在，則會使用無作業檔案提供者。</span><span class="sxs-lookup"><span data-stu-id="883ca-593">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="883ca-594">**機碼**：`webroot`</span><span class="sxs-lookup"><span data-stu-id="883ca-594">**Key**: `webroot`</span></span>  
<span data-ttu-id="883ca-595">**類型**：`string`</span><span class="sxs-lookup"><span data-stu-id="883ca-595">**Type**: `string`</span></span>  
<span data-ttu-id="883ca-596">**預設** 值：預設值為 `wwwroot` 。</span><span class="sxs-lookup"><span data-stu-id="883ca-596">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="883ca-597">*{Content root}/wwwroot* 的路徑必須存在。</span><span class="sxs-lookup"><span data-stu-id="883ca-597">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="883ca-598">**環境變數**： `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="883ca-598">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="883ca-599">若要設定此值，請使用環境變數或呼叫 `IWebHostBuilder` 上的 `UseWebRoot`：</span><span class="sxs-lookup"><span data-stu-id="883ca-599">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="883ca-600">如需詳細資訊，請參閱</span><span class="sxs-lookup"><span data-stu-id="883ca-600">For more information, see:</span></span>

* [<span data-ttu-id="883ca-601">基本概念： Web 根目錄</span><span class="sxs-lookup"><span data-stu-id="883ca-601">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="883ca-602">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="883ca-602">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="883ca-603">管理主機存留期</span><span class="sxs-lookup"><span data-stu-id="883ca-603">Manage the host lifetime</span></span>

<span data-ttu-id="883ca-604">在建置的 <xref:Microsoft.Extensions.Hosting.IHost> 實作上呼叫方法來啟動和停止應用程式。</span><span class="sxs-lookup"><span data-stu-id="883ca-604">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="883ca-605">這些方法會影響所有在服務容器中註冊的 <xref:Microsoft.Extensions.Hosting.IHostedService> 實作。</span><span class="sxs-lookup"><span data-stu-id="883ca-605">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="883ca-606">執行</span><span class="sxs-lookup"><span data-stu-id="883ca-606">Run</span></span>

<span data-ttu-id="883ca-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> 會執行應用程式並封鎖呼叫執行緒，直到主機關閉為止。</span><span class="sxs-lookup"><span data-stu-id="883ca-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="883ca-608">RunAsync</span><span class="sxs-lookup"><span data-stu-id="883ca-608">RunAsync</span></span>

<span data-ttu-id="883ca-609"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> 會執行應用程式，並傳回觸發取消語彙基元或關機時所完成的 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="883ca-609"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="883ca-610">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="883ca-610">RunConsoleAsync</span></span>

<span data-ttu-id="883ca-611"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>啟用主控台支援、建立並啟動主機，以及等候<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT 或 SIGTERM 關閉。</span><span class="sxs-lookup"><span data-stu-id="883ca-611"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="883ca-612">開始</span><span class="sxs-lookup"><span data-stu-id="883ca-612">Start</span></span>

<span data-ttu-id="883ca-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> 會同步啟動主機。</span><span class="sxs-lookup"><span data-stu-id="883ca-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="883ca-614">StartAsync</span><span class="sxs-lookup"><span data-stu-id="883ca-614">StartAsync</span></span>

<span data-ttu-id="883ca-615"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> 會啟動主機，並傳回觸發取消語彙基元或關機時所完成的 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="883ca-615"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="883ca-616"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> 在 `StartAsync` 開始時呼叫，並等到完成後再繼續進行。</span><span class="sxs-lookup"><span data-stu-id="883ca-616"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="883ca-617">這可用來將啟動延遲到外部事件發出訊號為止。</span><span class="sxs-lookup"><span data-stu-id="883ca-617">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="883ca-618">StopAsync</span><span class="sxs-lookup"><span data-stu-id="883ca-618">StopAsync</span></span>

<span data-ttu-id="883ca-619"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> 會嘗試在提供的逾時內停止主機。</span><span class="sxs-lookup"><span data-stu-id="883ca-619"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="883ca-620">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="883ca-620">WaitForShutdown</span></span>

<span data-ttu-id="883ca-621"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>封鎖呼叫的執行緒，直到 IHostLifetime 觸發關機（例如 via <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT 或 SIGTERM）。</span><span class="sxs-lookup"><span data-stu-id="883ca-621"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="883ca-622">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="883ca-622">WaitForShutdownAsync</span></span>

<span data-ttu-id="883ca-623"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> 會傳回透過指定語彙基元觸發關機時所完成的 <xref:System.Threading.Tasks.Task>，並呼叫 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>。</span><span class="sxs-lookup"><span data-stu-id="883ca-623"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="883ca-624">外部控制</span><span class="sxs-lookup"><span data-stu-id="883ca-624">External control</span></span>

<span data-ttu-id="883ca-625">主機存留期直接控制可以使用可從外部呼叫的方法來達成：</span><span class="sxs-lookup"><span data-stu-id="883ca-625">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="883ca-626">ASP.NET Core 應用程式會設定並啟動主機。</span><span class="sxs-lookup"><span data-stu-id="883ca-626">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="883ca-627">主機負責應用程式啟動和存留期管理。</span><span class="sxs-lookup"><span data-stu-id="883ca-627">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="883ca-628">本文所涵蓋的 ASP.NET Core 泛型主機 (<xref:Microsoft.Extensions.Hosting.HostBuilder>)，可用來不會處理 HTTP 要求的應用程式。</span><span class="sxs-lookup"><span data-stu-id="883ca-628">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="883ca-629">泛型主機的用途是將 HTTP 管線從 Web 主機 API 分離，以允許更廣泛的主機陣列案例。</span><span class="sxs-lookup"><span data-stu-id="883ca-629">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="883ca-630">傳訊、背景工作及其他以泛型主機為基礎的非 HTTP 工作負載都受益於跨領域的功能，例如設定、相依性插入 (DI) 和記錄。</span><span class="sxs-lookup"><span data-stu-id="883ca-630">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="883ca-631">泛型主機是 ASP.NET Core 2.1 的新功能，不適用於虛擬主機案例。</span><span class="sxs-lookup"><span data-stu-id="883ca-631">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="883ca-632">針對虛擬主機案例，請使用 [Web 主機](xref:fundamentals/host/web-host)。</span><span class="sxs-lookup"><span data-stu-id="883ca-632">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="883ca-633">泛型主機將在未來版本中取代 Web 主機，並成為 HTTP 和非 HTTP 案例中的主要主機 API。</span><span class="sxs-lookup"><span data-stu-id="883ca-633">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="883ca-634">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/generic-host/samples/) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="883ca-634">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="883ca-635">在 [Visual Studio Code](https://code.visualstudio.com/) 中執行範例應用程式時，請使用「外部或整合式終端機」。</span><span class="sxs-lookup"><span data-stu-id="883ca-635">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="883ca-636">請勿在 `internalConsole` 中執行範例。</span><span class="sxs-lookup"><span data-stu-id="883ca-636">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="883ca-637">若要在 Visual Studio Code 中設定主控台：</span><span class="sxs-lookup"><span data-stu-id="883ca-637">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="883ca-638">開啟 *.vscode/launch.json* 檔案。</span><span class="sxs-lookup"><span data-stu-id="883ca-638">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="883ca-639">在 [.NET Core Launch (console)] \(.NET Core 啟動 (主控台)\) 設定中，尋找 **console** 項目。</span><span class="sxs-lookup"><span data-stu-id="883ca-639">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="883ca-640">將此值設定為 `externalTerminal` 或 `integratedTerminal`。</span><span class="sxs-lookup"><span data-stu-id="883ca-640">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="883ca-641">簡介</span><span class="sxs-lookup"><span data-stu-id="883ca-641">Introduction</span></span>

<span data-ttu-id="883ca-642">可使用位於 <xref:Microsoft.Extensions.Hosting> 命名空間的泛型主機程式庫，且該程式庫由 [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) 套件提供。</span><span class="sxs-lookup"><span data-stu-id="883ca-642">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="883ca-643">[Microsoft.AspNetCore.App 中繼套件](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 或更新版本) 包含 `Microsoft.Extensions.Hosting` 套件。</span><span class="sxs-lookup"><span data-stu-id="883ca-643">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="883ca-644"><xref:Microsoft.Extensions.Hosting.IHostedService> 是程式碼執行的進入點。</span><span class="sxs-lookup"><span data-stu-id="883ca-644"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="883ca-645">每個 <xref:Microsoft.Extensions.Hosting.IHostedService> 實作會依 [ConfigureServices 中的服務註冊](#configureservices)順序執行。</span><span class="sxs-lookup"><span data-stu-id="883ca-645">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="883ca-646">當主機啟動時，會在每個 <xref:Microsoft.Extensions.Hosting.IHostedService> 上呼叫 <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*>；當主機順利關閉時，則會依反向註冊順序呼叫 <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*>。</span><span class="sxs-lookup"><span data-stu-id="883ca-646"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="883ca-647">設定主機</span><span class="sxs-lookup"><span data-stu-id="883ca-647">Set up a host</span></span>

<span data-ttu-id="883ca-648"><xref:Microsoft.Extensions.Hosting.IHostBuilder> 是程式庫和應用程式用來初始化、建置及執行主機的主要元件：</span><span class="sxs-lookup"><span data-stu-id="883ca-648"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="883ca-649">選項</span><span class="sxs-lookup"><span data-stu-id="883ca-649">Options</span></span>

<span data-ttu-id="883ca-650"><xref:Microsoft.Extensions.Hosting.IHost> 的 <xref:Microsoft.Extensions.Hosting.HostOptions> 設定選項。</span><span class="sxs-lookup"><span data-stu-id="883ca-650"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="883ca-651">關機逾時</span><span class="sxs-lookup"><span data-stu-id="883ca-651">Shutdown timeout</span></span>

<span data-ttu-id="883ca-652"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> 會為 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 設定逾時。</span><span class="sxs-lookup"><span data-stu-id="883ca-652"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="883ca-653">預設值是五秒鐘。</span><span class="sxs-lookup"><span data-stu-id="883ca-653">The default value is five seconds.</span></span>

<span data-ttu-id="883ca-654">中的下列選項 `Program.Main` 設定會將預設的五秒關機超時時間增加為20秒：</span><span class="sxs-lookup"><span data-stu-id="883ca-654">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="883ca-655">預設服務</span><span class="sxs-lookup"><span data-stu-id="883ca-655">Default services</span></span>

<span data-ttu-id="883ca-656">下列服務會在主機初始化期間註冊：</span><span class="sxs-lookup"><span data-stu-id="883ca-656">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="883ca-657">[環境](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>) </span><span class="sxs-lookup"><span data-stu-id="883ca-657">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="883ca-658">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>) </span><span class="sxs-lookup"><span data-stu-id="883ca-658">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="883ca-659"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="883ca-659"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="883ca-660"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="883ca-660"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="883ca-661">[選項](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>) </span><span class="sxs-lookup"><span data-stu-id="883ca-661">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="883ca-662">[記錄](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>) </span><span class="sxs-lookup"><span data-stu-id="883ca-662">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="883ca-663">主機組態</span><span class="sxs-lookup"><span data-stu-id="883ca-663">Host configuration</span></span>

<span data-ttu-id="883ca-664">主機組態的建立方式：</span><span class="sxs-lookup"><span data-stu-id="883ca-664">Host configuration is created by:</span></span>

* <span data-ttu-id="883ca-665">呼叫 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 上的擴充方法，來設定[內容根目錄](#content-root)及[環境](#environment)。</span><span class="sxs-lookup"><span data-stu-id="883ca-665">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="883ca-666">在 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> 中從組態提供者讀取組態。</span><span class="sxs-lookup"><span data-stu-id="883ca-666">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="883ca-667">擴充方法</span><span class="sxs-lookup"><span data-stu-id="883ca-667">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="883ca-668">應用程式索引鍵 (名稱)</span><span class="sxs-lookup"><span data-stu-id="883ca-668">Application key (name)</span></span>

<span data-ttu-id="883ca-669">[IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) 屬性是在主機建構期間從主機設定當中設定。</span><span class="sxs-lookup"><span data-stu-id="883ca-669">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="883ca-670">若要明確設定該值，請使用 [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey)：</span><span class="sxs-lookup"><span data-stu-id="883ca-670">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="883ca-671">**機碼**：`applicationName`</span><span class="sxs-lookup"><span data-stu-id="883ca-671">**Key**: `applicationName`</span></span>  
<span data-ttu-id="883ca-672">**類型**：`string`</span><span class="sxs-lookup"><span data-stu-id="883ca-672">**Type**: `string`</span></span>  
<span data-ttu-id="883ca-673">**預設**：包含應用程式進入點的組件名稱。</span><span class="sxs-lookup"><span data-stu-id="883ca-673">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="883ca-674">**設定使用**： `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="883ca-674">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="883ca-675">**環境變數**： `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` 是 [選擇性的，而且是使用者定義的](#configurehostconfiguration)) </span><span class="sxs-lookup"><span data-stu-id="883ca-675">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="883ca-676">內容根目錄</span><span class="sxs-lookup"><span data-stu-id="883ca-676">Content root</span></span>

<span data-ttu-id="883ca-677">此設定可決定主機開始搜尋內容檔案的位置。</span><span class="sxs-lookup"><span data-stu-id="883ca-677">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="883ca-678">**機碼**：`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="883ca-678">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="883ca-679">**類型**：`string`</span><span class="sxs-lookup"><span data-stu-id="883ca-679">**Type**: `string`</span></span>  
<span data-ttu-id="883ca-680">**預設值**：預設為應用程式組件所在的資料夾。</span><span class="sxs-lookup"><span data-stu-id="883ca-680">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="883ca-681">**設定使用**： `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="883ca-681">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="883ca-682">**環境變數**： `<PREFIX_>CONTENTROOT` (`<PREFIX_>` 是 [選擇性的，而且是使用者定義的](#configurehostconfiguration)) </span><span class="sxs-lookup"><span data-stu-id="883ca-682">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="883ca-683">如果路徑不存在，就無法啟動主機。</span><span class="sxs-lookup"><span data-stu-id="883ca-683">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="883ca-684">如需詳細資訊，請參閱 [基本概念：內容根目錄](xref:fundamentals/index#content-root)。</span><span class="sxs-lookup"><span data-stu-id="883ca-684">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="883ca-685">環境</span><span class="sxs-lookup"><span data-stu-id="883ca-685">Environment</span></span>

<span data-ttu-id="883ca-686">設定應用程式的 [環境](xref:fundamentals/environments)。</span><span class="sxs-lookup"><span data-stu-id="883ca-686">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="883ca-687">**機碼**：`environment`</span><span class="sxs-lookup"><span data-stu-id="883ca-687">**Key**: `environment`</span></span>  
<span data-ttu-id="883ca-688">**類型**：`string`</span><span class="sxs-lookup"><span data-stu-id="883ca-688">**Type**: `string`</span></span>  
<span data-ttu-id="883ca-689">**預設值**： `Production`</span><span class="sxs-lookup"><span data-stu-id="883ca-689">**Default**: `Production`</span></span>  
<span data-ttu-id="883ca-690">**設定使用**： `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="883ca-690">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="883ca-691">**環境變數**： `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` 是 [選擇性的，而且是使用者定義的](#configurehostconfiguration)) </span><span class="sxs-lookup"><span data-stu-id="883ca-691">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="883ca-692">環境可以設定為任何值。</span><span class="sxs-lookup"><span data-stu-id="883ca-692">The environment can be set to any value.</span></span> <span data-ttu-id="883ca-693">架構定義的值包括 `Development`、`Staging` 和 `Production`。</span><span class="sxs-lookup"><span data-stu-id="883ca-693">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="883ca-694">值不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="883ca-694">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="883ca-695">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="883ca-695">ConfigureHostConfiguration</span></span>

<span data-ttu-id="883ca-696"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> 會使用 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> 來建立主機的 <xref:Microsoft.Extensions.Configuration.IConfiguration>。</span><span class="sxs-lookup"><span data-stu-id="883ca-696"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="883ca-697">主機組態會用來將 <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> 初始化，使其在應用程式建置流程中可供使用。</span><span class="sxs-lookup"><span data-stu-id="883ca-697">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="883ca-698"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> 可以多次呼叫，其結果是累加的。</span><span class="sxs-lookup"><span data-stu-id="883ca-698"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="883ca-699">主機會使用指定索引鍵上最後設定值的任何選項。</span><span class="sxs-lookup"><span data-stu-id="883ca-699">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="883ca-700">預設不會包含任何提供者。</span><span class="sxs-lookup"><span data-stu-id="883ca-700">No providers are included by default.</span></span> <span data-ttu-id="883ca-701">您必須明確地指定應用程式在 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> 之中需要哪個組態提供者，包括：</span><span class="sxs-lookup"><span data-stu-id="883ca-701">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="883ca-702">檔案組態 (例如，來自 *hostsettings.json* 的檔案)。</span><span class="sxs-lookup"><span data-stu-id="883ca-702">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="883ca-703">環境變數組態。</span><span class="sxs-lookup"><span data-stu-id="883ca-703">Environment variable configuration.</span></span>
* <span data-ttu-id="883ca-704">命令列引數組態。</span><span class="sxs-lookup"><span data-stu-id="883ca-704">Command-line argument configuration.</span></span>
* <span data-ttu-id="883ca-705">任何其他必要的組態提供者。</span><span class="sxs-lookup"><span data-stu-id="883ca-705">Any other required configuration providers.</span></span>

<span data-ttu-id="883ca-706">使用之後呼叫[檔案組態提供者](xref:fundamentals/configuration/index#file-configuration-provider)的 `SetBasePath`，並透過指定應用程式基底路徑，就可使用主機的檔案設定。</span><span class="sxs-lookup"><span data-stu-id="883ca-706">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="883ca-707">範例應用程式會使用 JSON 檔案，*hostsettings.json*，並呼叫 <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 取用檔案的主機組態設定。</span><span class="sxs-lookup"><span data-stu-id="883ca-707">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="883ca-708">若要新增主機的[環境變數組態](xref:fundamentals/configuration/index#environment-variables-configuration-provider)，請在主機建立器上呼叫 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>。</span><span class="sxs-lookup"><span data-stu-id="883ca-708">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="883ca-709">`AddEnvironmentVariables` 可接受選擇性的使用者定義前置詞。</span><span class="sxs-lookup"><span data-stu-id="883ca-709">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="883ca-710">範例應用程式會使用前置詞 `PREFIX_`。</span><span class="sxs-lookup"><span data-stu-id="883ca-710">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="883ca-711">讀取環境變數時，就會移除前置詞。</span><span class="sxs-lookup"><span data-stu-id="883ca-711">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="883ca-712">在設定範例應用程式的主機時，`PREFIX_ENVIRONMENT` 的環境變數值會變成 `environment` 索引鍵的主機組態值。</span><span class="sxs-lookup"><span data-stu-id="883ca-712">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="883ca-713">在開發期間使用 [Visual Studio](https://visualstudio.microsoft.com) 或以 `dotnet run` 執行應用程式時，可能會在 *Properties/launchSettings.json* 檔案中設定環境變數。</span><span class="sxs-lookup"><span data-stu-id="883ca-713">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="883ca-714">在 [Visual Studio Code](https://code.visualstudio.com/) 中，可以在開發期間於 *.vscode/launch.json* 檔案中設定環境變數。</span><span class="sxs-lookup"><span data-stu-id="883ca-714">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="883ca-715">如需詳細資訊，請參閱<xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="883ca-715">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="883ca-716">[命令列組態](xref:fundamentals/configuration/index#command-line-configuration-provider)可透過呼叫 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 新增。</span><span class="sxs-lookup"><span data-stu-id="883ca-716">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="883ca-717">命令列組態會在最後新增，以便命令列引數覆寫由先前組態提供者提供的組態。</span><span class="sxs-lookup"><span data-stu-id="883ca-717">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="883ca-718">*hostsettings.json*：</span><span class="sxs-lookup"><span data-stu-id="883ca-718">*hostsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="883ca-719">您可以使用 [applicationName](#application-key-name) 和 [contentRoot](#content-root) 索引鍵來提供其他組態。</span><span class="sxs-lookup"><span data-stu-id="883ca-719">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="883ca-720">使用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> 的 `HostBuilder` 組態範例：</span><span class="sxs-lookup"><span data-stu-id="883ca-720">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="883ca-721">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="883ca-721">ConfigureAppConfiguration</span></span>

<span data-ttu-id="883ca-722">應用程式組態的建立方式是在 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 實作上呼叫 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>。</span><span class="sxs-lookup"><span data-stu-id="883ca-722">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="883ca-723"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 會使用 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> 來建立應用程式的 <xref:Microsoft.Extensions.Configuration.IConfiguration>。</span><span class="sxs-lookup"><span data-stu-id="883ca-723"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="883ca-724"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 可以多次呼叫，其結果是累加的。</span><span class="sxs-lookup"><span data-stu-id="883ca-724"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="883ca-725">應用程式會使用指定索引鍵上最後設定值的任何選項。</span><span class="sxs-lookup"><span data-stu-id="883ca-725">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="883ca-726">您可以從後續作業的 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) 及 <xref:Microsoft.Extensions.Hosting.IHost.Services*> 中存取 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 所建立的組態。</span><span class="sxs-lookup"><span data-stu-id="883ca-726">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="883ca-727">應用程式組態會自動接收由 [ConfigureHostConfiguration](#configurehostconfiguration) 提供的主機組態。</span><span class="sxs-lookup"><span data-stu-id="883ca-727">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="883ca-728">使用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 的應用程式組態範例：</span><span class="sxs-lookup"><span data-stu-id="883ca-728">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="883ca-729">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="883ca-729">*appsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="883ca-730">*appsettings.Development.json*：</span><span class="sxs-lookup"><span data-stu-id="883ca-730">*appsettings.Development.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="883ca-731">*appsettings.Production.json*：</span><span class="sxs-lookup"><span data-stu-id="883ca-731">*appsettings.Production.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="883ca-732">若要將設定檔移至輸出目錄，請在專案檔中將設定檔指定為 [MSBuild 專案項目](/visualstudio/msbuild/common-msbuild-project-items)。</span><span class="sxs-lookup"><span data-stu-id="883ca-732">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="883ca-733">範例應用程式使用下列 `<Content>` 項目，移動其 JSON 應用程式設定檔和 *hostsettings.json*：</span><span class="sxs-lookup"><span data-stu-id="883ca-733">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="883ca-734">組態擴充方法 (例如 <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 和 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>) 需要其他的 NuGet 套件，例如 [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) \(英文\) 和[Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="883ca-734">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="883ca-735">除非應用程式使用 [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app)，否則，除了核心 [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) \(英文\) 套件，還必須將這些套件新增至專案。</span><span class="sxs-lookup"><span data-stu-id="883ca-735">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="883ca-736">如需詳細資訊，請參閱<xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="883ca-736">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="883ca-737">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="883ca-737">ConfigureServices</span></span>

<span data-ttu-id="883ca-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> 會將服務新增至應用程式的[相依性插入](xref:fundamentals/dependency-injection)容器。</span><span class="sxs-lookup"><span data-stu-id="883ca-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="883ca-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> 可以多次呼叫，其結果是累加的。</span><span class="sxs-lookup"><span data-stu-id="883ca-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="883ca-740">託管服務是具有背景工作邏輯的類別，可實作 <xref:Microsoft.Extensions.Hosting.IHostedService> 介面。</span><span class="sxs-lookup"><span data-stu-id="883ca-740">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="883ca-741">如需詳細資訊，請參閱<xref:fundamentals/host/hosted-services>。</span><span class="sxs-lookup"><span data-stu-id="883ca-741">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="883ca-742">[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/generic-host/samples/)使用 `AddHostedService` 擴充方法，將存留期事件 `LifetimeEventsHostedService` 和計時背景工作 `TimedHostedService` 等服務新增至應用程式：</span><span class="sxs-lookup"><span data-stu-id="883ca-742">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="883ca-743">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="883ca-743">ConfigureLogging</span></span>

<span data-ttu-id="883ca-744"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> 會新增委派以設定提供的 <xref:Microsoft.Extensions.Logging.ILoggingBuilder>。</span><span class="sxs-lookup"><span data-stu-id="883ca-744"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="883ca-745"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> 可以多次呼叫，其結果是累加的。</span><span class="sxs-lookup"><span data-stu-id="883ca-745"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="883ca-746">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="883ca-746">UseConsoleLifetime</span></span>

<span data-ttu-id="883ca-747"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>接聽<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT 或 SIGTERM，並呼叫 <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> 以啟動關機程式。</span><span class="sxs-lookup"><span data-stu-id="883ca-747"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="883ca-748"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> 解除封鎖 [RunAsync](#runasync) 和 [>waitforshutdownasync](#waitforshutdownasync)等擴充功能。</span><span class="sxs-lookup"><span data-stu-id="883ca-748"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="883ca-749">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` 會預先註冊為預設存留期實作。</span><span class="sxs-lookup"><span data-stu-id="883ca-749">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="883ca-750">系統會使用最後一個註冊的存留期。</span><span class="sxs-lookup"><span data-stu-id="883ca-750">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="883ca-751">容器設定</span><span class="sxs-lookup"><span data-stu-id="883ca-751">Container configuration</span></span>

<span data-ttu-id="883ca-752">為支援插入其他容器，主機可以接受 <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>。</span><span class="sxs-lookup"><span data-stu-id="883ca-752">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="883ca-753">提供處理站不是 DI 容器註冊的一部分，而是用來建立實體 DI 容器的主機內建功能。</span><span class="sxs-lookup"><span data-stu-id="883ca-753">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="883ca-754">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) 會覆寫用來建立應用程式服務提供者的預設處理站。</span><span class="sxs-lookup"><span data-stu-id="883ca-754">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="883ca-755">自訂容器組態由 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> 方法管理。</span><span class="sxs-lookup"><span data-stu-id="883ca-755">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="883ca-756"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> 提供在基礎主機 API 上設定容器的強型別體驗。</span><span class="sxs-lookup"><span data-stu-id="883ca-756"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="883ca-757"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> 可以多次呼叫，其結果是累加的。</span><span class="sxs-lookup"><span data-stu-id="883ca-757"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="883ca-758">建立應用程式的服務容器：</span><span class="sxs-lookup"><span data-stu-id="883ca-758">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="883ca-759">提供服務容器處理站：</span><span class="sxs-lookup"><span data-stu-id="883ca-759">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="883ca-760">使用處理站，並設定應用程式的自訂服務容器：</span><span class="sxs-lookup"><span data-stu-id="883ca-760">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="883ca-761">擴充性</span><span class="sxs-lookup"><span data-stu-id="883ca-761">Extensibility</span></span>

<span data-ttu-id="883ca-762">主機擴充性是透過 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 上的擴充方法執行。</span><span class="sxs-lookup"><span data-stu-id="883ca-762">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="883ca-763">下列範例使用 <xref:fundamentals/host/hosted-services> 中示範的 [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) 範例顯示擴充方法如何擴充 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 實作。</span><span class="sxs-lookup"><span data-stu-id="883ca-763">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="883ca-764">應用程式會建立 `UseHostedService` 擴充方法以註冊在 `T` 中傳遞的裝載服務：</span><span class="sxs-lookup"><span data-stu-id="883ca-764">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="883ca-765">管理主機</span><span class="sxs-lookup"><span data-stu-id="883ca-765">Manage the host</span></span>

<span data-ttu-id="883ca-766"><xref:Microsoft.Extensions.Hosting.IHost> 實作負責啟動及停止服務容器中已註冊的 <xref:Microsoft.Extensions.Hosting.IHostedService> 實作。</span><span class="sxs-lookup"><span data-stu-id="883ca-766">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="883ca-767">執行</span><span class="sxs-lookup"><span data-stu-id="883ca-767">Run</span></span>

<span data-ttu-id="883ca-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> 會執行應用程式並封鎖呼叫執行緒，直到主機關閉為止：</span><span class="sxs-lookup"><span data-stu-id="883ca-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="883ca-769">RunAsync</span><span class="sxs-lookup"><span data-stu-id="883ca-769">RunAsync</span></span>

<span data-ttu-id="883ca-770"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> 會執行應用程式，並傳回觸發取消語彙基元或關機時所完成的 <xref:System.Threading.Tasks.Task>：</span><span class="sxs-lookup"><span data-stu-id="883ca-770"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="883ca-771">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="883ca-771">RunConsoleAsync</span></span>

<span data-ttu-id="883ca-772"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>啟用主控台支援、建立並啟動主機，以及等候<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT 或 SIGTERM 關閉。</span><span class="sxs-lookup"><span data-stu-id="883ca-772"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="883ca-773">Start 和 StopAsync</span><span class="sxs-lookup"><span data-stu-id="883ca-773">Start and StopAsync</span></span>

<span data-ttu-id="883ca-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> 會同步啟動主機。</span><span class="sxs-lookup"><span data-stu-id="883ca-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="883ca-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> 會嘗試在提供的逾時內停止主機。</span><span class="sxs-lookup"><span data-stu-id="883ca-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="883ca-776">StartAsync 和 StopAsync</span><span class="sxs-lookup"><span data-stu-id="883ca-776">StartAsync and StopAsync</span></span>

<span data-ttu-id="883ca-777"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> 會啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="883ca-777"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="883ca-778"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 會停止應用程式。</span><span class="sxs-lookup"><span data-stu-id="883ca-778"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="883ca-779">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="883ca-779">WaitForShutdown</span></span>

<span data-ttu-id="883ca-780"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>會透過來觸發 <xref:Microsoft.Extensions.Hosting.IHostLifetime> ，例如 `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (接聽<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT 或 SIGTERM) 。</span><span class="sxs-lookup"><span data-stu-id="883ca-780"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="883ca-781"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> 會呼叫 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>。</span><span class="sxs-lookup"><span data-stu-id="883ca-781"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="883ca-782">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="883ca-782">WaitForShutdownAsync</span></span>

<span data-ttu-id="883ca-783"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> 會傳回透過指定語彙基元觸發關機時所完成的 <xref:System.Threading.Tasks.Task>，並呼叫 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>。</span><span class="sxs-lookup"><span data-stu-id="883ca-783"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="883ca-784">外部控制</span><span class="sxs-lookup"><span data-stu-id="883ca-784">External control</span></span>

<span data-ttu-id="883ca-785">主機的外部控制可以使用可從外部呼叫的方法來達成：</span><span class="sxs-lookup"><span data-stu-id="883ca-785">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="883ca-786"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> 在 <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> 開始時呼叫，並等到完成後再繼續進行。</span><span class="sxs-lookup"><span data-stu-id="883ca-786"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="883ca-787">這可用來將啟動延遲到外部事件發出訊號為止。</span><span class="sxs-lookup"><span data-stu-id="883ca-787">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="883ca-788">IHostingEnvironment 介面</span><span class="sxs-lookup"><span data-stu-id="883ca-788">IHostingEnvironment interface</span></span>

<span data-ttu-id="883ca-789"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> 提供應用程式主控環境的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="883ca-789"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="883ca-790">使用[建構函式插入](xref:fundamentals/dependency-injection)以取得 <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>，才能使用其屬性和擴充方法：</span><span class="sxs-lookup"><span data-stu-id="883ca-790">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="883ca-791">如需詳細資訊，請參閱<xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="883ca-791">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="883ca-792">IApplicationLifetime 介面</span><span class="sxs-lookup"><span data-stu-id="883ca-792">IApplicationLifetime interface</span></span>

<span data-ttu-id="883ca-793"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> 允許啟動後和關機活動，包括順利關機要求。</span><span class="sxs-lookup"><span data-stu-id="883ca-793"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="883ca-794">在介面上的三個屬性是用來註冊定義啟動和關閉事件之 <xref:System.Action> 方法的取消語彙基元。</span><span class="sxs-lookup"><span data-stu-id="883ca-794">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="883ca-795">取消權杖</span><span class="sxs-lookup"><span data-stu-id="883ca-795">Cancellation Token</span></span> | <span data-ttu-id="883ca-796">觸發時機&#8230;</span><span class="sxs-lookup"><span data-stu-id="883ca-796">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="883ca-797">已完全啟動主機。</span><span class="sxs-lookup"><span data-stu-id="883ca-797">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="883ca-798">主機正在完成正常關機程序。</span><span class="sxs-lookup"><span data-stu-id="883ca-798">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="883ca-799">應該處理所有要求。</span><span class="sxs-lookup"><span data-stu-id="883ca-799">All requests should be processed.</span></span> <span data-ttu-id="883ca-800">關機封鎖，直到完成此事件。</span><span class="sxs-lookup"><span data-stu-id="883ca-800">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="883ca-801">主機正在執行正常關機程序。</span><span class="sxs-lookup"><span data-stu-id="883ca-801">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="883ca-802">可能仍在處理要求。</span><span class="sxs-lookup"><span data-stu-id="883ca-802">Requests may still be processing.</span></span> <span data-ttu-id="883ca-803">關機封鎖，直到完成此事件。</span><span class="sxs-lookup"><span data-stu-id="883ca-803">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="883ca-804">建構函式將 <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> 服務插入任何類別。</span><span class="sxs-lookup"><span data-stu-id="883ca-804">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="883ca-805">[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/generic-host/samples/)使用建構函式插入 `LifetimeEventsHostedService` 類別 (<xref:Microsoft.Extensions.Hosting.IHostedService> 實作) 來註冊事件。</span><span class="sxs-lookup"><span data-stu-id="883ca-805">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="883ca-806">*LifetimeEventsHostedService.cs*：</span><span class="sxs-lookup"><span data-stu-id="883ca-806">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="883ca-807"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> 會要求應用程式終止。</span><span class="sxs-lookup"><span data-stu-id="883ca-807"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="883ca-808">當呼叫類別的 `Shutdown` 方法時，下列類別使用 <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> 來順利關閉應用程式：</span><span class="sxs-lookup"><span data-stu-id="883ca-808">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="883ca-809">其他資源</span><span class="sxs-lookup"><span data-stu-id="883ca-809">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
