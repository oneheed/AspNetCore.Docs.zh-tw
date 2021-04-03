---
title: ASP.NET Core 模組
author: rick-anderson
description: 深入瞭解使用 IIS 裝載 ASP.NET Core 應用程式的 ASP.NET Core 模組。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: fd9c5bf94385d76aed22a09bd4cd248f1c1dcb6e
ms.sourcegitcommit: fafcf015d64aa2388bacee16ba38799daf06a4f0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2021
ms.locfileid: "105957674"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="aeab8-103">ASP.NET Core 模組</span><span class="sxs-lookup"><span data-stu-id="aeab8-103">ASP.NET Core Module</span></span>

<span data-ttu-id="aeab8-104">由 [Tom Dykstra](https://github.com/tdykstra)、 [rick Strahl](https://github.com/RickStrahl)、 [Chris Ross](https://github.com/Tratcher)、 [Rick Anderson](https://twitter.com/RickAndMSFT)、 [>sourabh Shirhatti](https://twitter.com/sshirhatti)和 [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="aeab8-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), and [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="aeab8-105">ASP.NET Core 模組是一種原生 IIS 模組，可插入 IIS 管線，讓 ASP.NET Core 應用程式能與 IIS 搭配運作。</span><span class="sxs-lookup"><span data-stu-id="aeab8-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline, allowing ASP.NET Core applications to work with IIS.</span></span> <span data-ttu-id="aeab8-106">使用 IIS 執行 ASP.NET Core 應用程式，方法是：</span><span class="sxs-lookup"><span data-stu-id="aeab8-106">Run ASP.NET Core apps with IIS by either:</span></span> 

* <span data-ttu-id="aeab8-107">將 ASP.NET Core 應用程式裝載在 IIS 背景工作進程 (`w3wp.exe`) 中，稱為內含 [式裝載模型](xref:host-and-deploy/iis/in-process-hosting)。</span><span class="sxs-lookup"><span data-stu-id="aeab8-107">Hosting an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](xref:host-and-deploy/iis/in-process-hosting).</span></span>
* <span data-ttu-id="aeab8-108">將 web 要求轉送到執行 Kestrel 伺服器的後端 ASP.NET Core 應用程式，稱為 [跨進程裝載模型](xref:host-and-deploy/iis/out-of-process-hosting)。</span><span class="sxs-lookup"><span data-stu-id="aeab8-108">Forwarding web requests to a backend ASP.NET Core app running the Kestrel server, called the [out-of-process hosting model](xref:host-and-deploy/iis/out-of-process-hosting).</span></span>

<span data-ttu-id="aeab8-109">每個裝載模型之間都有取捨。</span><span class="sxs-lookup"><span data-stu-id="aeab8-109">There are trade-offs between each of the hosting models.</span></span> <span data-ttu-id="aeab8-110">依預設，會使用同進程裝載模型，因為效能和診斷效能較佳。</span><span class="sxs-lookup"><span data-stu-id="aeab8-110">By default, the in-process hosting model is used due to better performance and diagnostics.</span></span>

## <a name="install-aspnet-core-module"></a><span data-ttu-id="aeab8-111">安裝 ASP.NET Core 模組</span><span class="sxs-lookup"><span data-stu-id="aeab8-111">Install ASP.NET Core Module</span></span>

<span data-ttu-id="aeab8-112">ASP.NET Core 模組會與 .net core [裝載](xref:host-and-deploy/iis/hosting-bundle)套件組合中的 .Net core 執行時間一起安裝。</span><span class="sxs-lookup"><span data-stu-id="aeab8-112">The ASP.NET Core Module is installed with the .NET Core Runtime from the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/hosting-bundle).</span></span> <span data-ttu-id="aeab8-113">ASP.NET Core 模組向前及向後相容于 .NET 的 LTS 版本。</span><span class="sxs-lookup"><span data-stu-id="aeab8-113">The ASP.NET Core Module is forward and backward compatible with LTS releases of .NET.</span></span>

[!INCLUDE[](~/includes/announcements.md)]

<span data-ttu-id="aeab8-114">使用下列連結下載安裝程式：</span><span class="sxs-lookup"><span data-stu-id="aeab8-114">Download the installer using the following link:</span></span>

[<span data-ttu-id="aeab8-115">目前的 .NET Core 裝載套件組合安裝程式 (直接下載)</span><span class="sxs-lookup"><span data-stu-id="aeab8-115">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

<span data-ttu-id="aeab8-116">如需詳細資訊（包括安裝較舊版本的模組），請參閱 <xref:host-and-deploy/iis/hosting-bundle> 。</span><span class="sxs-lookup"><span data-stu-id="aeab8-116">For more information, including installing an earlier version of the module, see <xref:host-and-deploy/iis/hosting-bundle>.</span></span>

<span data-ttu-id="aeab8-117">如需將 ASP.NET Core 應用程式發佈至 IIS 伺服器的教學課程體驗，請參閱<xref:tutorials/publish-to-iis>。</span><span class="sxs-lookup"><span data-stu-id="aeab8-117">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="aeab8-118">ASP.NET Core 模組是一種原生 IIS 模組，可外掛至 IIS 管線以便：</span><span class="sxs-lookup"><span data-stu-id="aeab8-118">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="aeab8-119">在 IIS 工作者處理序 (`w3wp.exe`) 中裝載 ASP.NET Core 應用程式 (稱為[同處理序裝載模型](#in-process-hosting-model))。</span><span class="sxs-lookup"><span data-stu-id="aeab8-119">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="aeab8-120">將 Web 要求轉送到執行 [Kestrel 伺服器](xref:fundamentals/servers/kestrel)的後端 ASP.NET Core 應用程式 (稱為[跨處理序裝載模型](#out-of-process-hosting-model))。</span><span class="sxs-lookup"><span data-stu-id="aeab8-120">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="aeab8-121">支援的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="aeab8-121">Supported Windows versions:</span></span>

* <span data-ttu-id="aeab8-122">Windows 7 或更新版本</span><span class="sxs-lookup"><span data-stu-id="aeab8-122">Windows 7 or later</span></span>
* <span data-ttu-id="aeab8-123">Windows Server 2012 R2 或更新版本</span><span class="sxs-lookup"><span data-stu-id="aeab8-123">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="aeab8-124">同處理序裝載時，模組會使用 IIS 的同處理序伺服程式實作，稱為 IIS HTTP 伺服器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="aeab8-124">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="aeab8-125">跨處理序裝載時，該模組只適用於 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="aeab8-125">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="aeab8-126">模組無法搭配 [HTTP.sys](xref:fundamentals/servers/httpsys)運作。</span><span class="sxs-lookup"><span data-stu-id="aeab8-126">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="aeab8-127">裝載模型</span><span class="sxs-lookup"><span data-stu-id="aeab8-127">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="aeab8-128">同處理序裝載模型</span><span class="sxs-lookup"><span data-stu-id="aeab8-128">In-process hosting model</span></span>

<span data-ttu-id="aeab8-129">ASP.NET Core apps 預設為同進程裝載模型。</span><span class="sxs-lookup"><span data-stu-id="aeab8-129">ASP.NET Core apps default to the in-process hosting model.</span></span>

<span data-ttu-id="aeab8-130">同處理序裝載時具有下列特性：</span><span class="sxs-lookup"><span data-stu-id="aeab8-130">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="aeab8-131">使用 IIS HTTP 伺服器 (`IISHttpServer`) 而不是 [Kestrel](xref:fundamentals/servers/kestrel) 伺服器。</span><span class="sxs-lookup"><span data-stu-id="aeab8-131">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="aeab8-132">針對同處理序，[CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) 會呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> 來：</span><span class="sxs-lookup"><span data-stu-id="aeab8-132">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="aeab8-133">註冊 `IISHttpServer`。</span><span class="sxs-lookup"><span data-stu-id="aeab8-133">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="aeab8-134">設定伺服器在 ASP.NET Core 模組後方執行時應該接聽的連接埠和基底路徑。</span><span class="sxs-lookup"><span data-stu-id="aeab8-134">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="aeab8-135">設定主機以擷取啟動錯誤。</span><span class="sxs-lookup"><span data-stu-id="aeab8-135">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="aeab8-136">[requestTimeout 屬性](#attributes-of-the-aspnetcore-element)不適用於同處理序裝載。</span><span class="sxs-lookup"><span data-stu-id="aeab8-136">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="aeab8-137">不支援在應用程式之間共用應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="aeab8-137">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="aeab8-138">每個應用程式使用一個應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="aeab8-138">Use one app pool per app.</span></span>

* <span data-ttu-id="aeab8-139">使用[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy)或[ `app_offline.htm` 以手動方式將檔案放入部署](xref:host-and-deploy/iis/index#locked-deployment-files)時，如果有開啟的連線，應用程式可能不會立即關機。</span><span class="sxs-lookup"><span data-stu-id="aeab8-139">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [`app_offline.htm` file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="aeab8-140">例如，WebSocket 連線可能會延遲應用程式關機。</span><span class="sxs-lookup"><span data-stu-id="aeab8-140">For example, a WebSocket connection may delay app shut down.</span></span>

* <span data-ttu-id="aeab8-141">應用程式的架構 (位元) 和已安裝的執行階段 (x64 或 x86) 必須符合應用程式集區的架構。</span><span class="sxs-lookup"><span data-stu-id="aeab8-141">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="aeab8-142">偵測到用戶端中斷連線。</span><span class="sxs-lookup"><span data-stu-id="aeab8-142">Client disconnects are detected.</span></span> <span data-ttu-id="aeab8-143">[`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*)當用戶端中斷連線時，解除標記就會取消。</span><span class="sxs-lookup"><span data-stu-id="aeab8-143">The [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="aeab8-144">在 ASP.NET Core 2.2.1 或更早版本中，會傳回 <xref:System.IO.Directory.GetCurrentDirectory*> IIS （而不是應用程式的 (目錄）啟動之進程的背景工作角色，例如 `C:\Windows\System32\inetsrv` `w3wp.exe`) 。</span><span class="sxs-lookup"><span data-stu-id="aeab8-144">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, `C:\Windows\System32\inetsrv` for `w3wp.exe`).</span></span>

  <span data-ttu-id="aeab8-145">如需設定應用程式目前的目錄的範例程式碼，請參閱[ `CurrentDirectoryHelpers` 類別](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs)。</span><span class="sxs-lookup"><span data-stu-id="aeab8-145">For sample code that sets the app's current directory, see the [`CurrentDirectoryHelpers` class](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="aeab8-146">呼叫 `SetCurrentDirectory` 方法。</span><span class="sxs-lookup"><span data-stu-id="aeab8-146">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="aeab8-147">後續呼叫 <xref:System.IO.Directory.GetCurrentDirectory*> 會提供應用程式的目錄。</span><span class="sxs-lookup"><span data-stu-id="aeab8-147">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="aeab8-148">裝載同處理序時，不會內部呼叫 <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> 來將使用者初始化。</span><span class="sxs-lookup"><span data-stu-id="aeab8-148">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="aeab8-149">因此，預設會在未啟動每個驗證之後，使用 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 實作來轉換宣告。</span><span class="sxs-lookup"><span data-stu-id="aeab8-149">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="aeab8-150">使用 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 實作來轉換宣告時，請呼叫 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 來新增入驗證服務：</span><span class="sxs-lookup"><span data-stu-id="aeab8-150">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```
  
  * <span data-ttu-id="aeab8-151">不支援[ (單一檔案) 部署的 Web 套件](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages)。</span><span class="sxs-lookup"><span data-stu-id="aeab8-151">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="aeab8-152">跨處理序裝載模型</span><span class="sxs-lookup"><span data-stu-id="aeab8-152">Out-of-process hosting model</span></span>

<span data-ttu-id="aeab8-153">若要設定跨進程裝載的應用程式，請將專案檔中的屬性值設定 `<AspNetCoreHostingModel>` 為， `OutOfProcess` (`.csproj`) ：</span><span class="sxs-lookup"><span data-stu-id="aeab8-153">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (`.csproj`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="aeab8-154">同進程裝載是使用設定 `InProcess` ，這是預設值。</span><span class="sxs-lookup"><span data-stu-id="aeab8-154">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="aeab8-155">的值 `<AspNetCoreHostingModel>` 不區分大小寫，因此 `inprocess` 和 `outofprocess` 都是有效的值。</span><span class="sxs-lookup"><span data-stu-id="aeab8-155">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="aeab8-156">使用 [Kestrel](xref:fundamentals/servers/kestrel) 伺服器而不是 IIS HTTP 伺服器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="aeab8-156">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="aeab8-157">針對跨進程， [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) 呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> ：</span><span class="sxs-lookup"><span data-stu-id="aeab8-157">For out-of-process, [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="aeab8-158">設定伺服器在 ASP.NET Core 模組後方執行時應該接聽的連接埠和基底路徑。</span><span class="sxs-lookup"><span data-stu-id="aeab8-158">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="aeab8-159">設定主機以擷取啟動錯誤。</span><span class="sxs-lookup"><span data-stu-id="aeab8-159">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="aeab8-160">裝載模型變更</span><span class="sxs-lookup"><span data-stu-id="aeab8-160">Hosting model changes</span></span>

<span data-ttu-id="aeab8-161">如果檔案 `hostingModel` 中的設定有所變更 `web.config` ([在 `web.config` ](#configuration-with-webconfig)) 的設定中說明，則模組會回收 IIS 的工作者進程。</span><span class="sxs-lookup"><span data-stu-id="aeab8-161">If the `hostingModel` setting is changed in the `web.config` file (explained in the [Configuration with `web.config`](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="aeab8-162">針對 IIS Express，模組不會回收工作者處理序，但會改為觸發目前 IIS Express 處理序的正常關閉。</span><span class="sxs-lookup"><span data-stu-id="aeab8-162">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="aeab8-163">應用程式的下一個要求會繁衍新的 IIS Express 處理序。</span><span class="sxs-lookup"><span data-stu-id="aeab8-163">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="aeab8-164">程序名稱</span><span class="sxs-lookup"><span data-stu-id="aeab8-164">Process name</span></span>

<span data-ttu-id="aeab8-165">`Process.GetCurrentProcess().ProcessName` 會報告 `w3wp`/`iisexpress` (同處理序) 或 `dotnet` (跨處理序)。</span><span class="sxs-lookup"><span data-stu-id="aeab8-165">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="aeab8-166">許多如 Windows 驗證等原生模組仍在使用中。</span><span class="sxs-lookup"><span data-stu-id="aeab8-166">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="aeab8-167">若要深入了解搭配 ASP.NET Core 模組的使用中 IIS 模組，請參閱<xref:host-and-deploy/iis/modules>。</span><span class="sxs-lookup"><span data-stu-id="aeab8-167">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="aeab8-168">ASP.NET Core 模組也可以：</span><span class="sxs-lookup"><span data-stu-id="aeab8-168">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="aeab8-169">設定背景工作處理序的環境變數。</span><span class="sxs-lookup"><span data-stu-id="aeab8-169">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="aeab8-170">將 stdout 輸出記錄到檔案儲存區，以針對啟動問題進行疑難排解。</span><span class="sxs-lookup"><span data-stu-id="aeab8-170">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="aeab8-171">轉送 Windows 驗證權杖。</span><span class="sxs-lookup"><span data-stu-id="aeab8-171">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="aeab8-172">如何安裝和使用 ASP.NET Core 模組</span><span class="sxs-lookup"><span data-stu-id="aeab8-172">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="aeab8-173">如需如何安裝 ASP.NET Core 模組的指示，請參閱[安裝 .NET Core 裝載套件組合](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="aeab8-173">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span> <span data-ttu-id="aeab8-174">ASP.NET Core 模組向前及向後相容于 .NET 的 LTS 版本。</span><span class="sxs-lookup"><span data-stu-id="aeab8-174">The ASP.NET Core Module is forward and backward compatible with LTS releases of .NET.</span></span>

[!INCLUDE[](~/includes/announcements.md)]

## <a name="configuration-with-webconfig"></a><span data-ttu-id="aeab8-175">使用 web.config 進行設定</span><span class="sxs-lookup"><span data-stu-id="aeab8-175">Configuration with web.config</span></span>

<span data-ttu-id="aeab8-176">設定 ASP.NET Core 模組時，是使用網站 *web.config* 檔案中 `system.webServer` 節點的 `aspNetCore` 區段來設定。</span><span class="sxs-lookup"><span data-stu-id="aeab8-176">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="aeab8-177">下列檔案 `web.config` 是針對與 [framework 相依的部署](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) 所發佈，並設定 ASP.NET Core 模組來處理網站要求：</span><span class="sxs-lookup"><span data-stu-id="aeab8-177">The following `web.config` file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="aeab8-178">以下 *web.config* 是針對 [自封式部署](/dotnet/articles/core/deploying/#self-contained-deployments-scd)發佈的檔案：</span><span class="sxs-lookup"><span data-stu-id="aeab8-178">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="aeab8-179"><xref:System.Configuration.SectionInformation.InheritInChildApplications*>屬性設定為，表示在專案 `false` 內指定的設定不會 [`<location>`](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 由位於應用程式子目錄中的應用程式所繼承。</span><span class="sxs-lookup"><span data-stu-id="aeab8-179">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [`<location>`](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="aeab8-180">將應用程式部署至 [Azure App Service](https://azure.microsoft.com/services/app-service/) 時，`stdoutLogFile` 路徑會設定為 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="aeab8-180">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="aeab8-181">路徑會將 stdout 記錄檔儲存到 `LogFiles` 資料夾，這是服務自動建立的位置。</span><span class="sxs-lookup"><span data-stu-id="aeab8-181">The path saves stdout logs to the `LogFiles` folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="aeab8-182">如需有關 IIS 子應用程式設定的詳細資訊，請參閱 <xref:host-and-deploy/iis/index#sub-applications>。</span><span class="sxs-lookup"><span data-stu-id="aeab8-182">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="aeab8-183">aspNetCore 元素的屬性</span><span class="sxs-lookup"><span data-stu-id="aeab8-183">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="aeab8-184">屬性</span><span class="sxs-lookup"><span data-stu-id="aeab8-184">Attribute</span></span> | <span data-ttu-id="aeab8-185">描述</span><span class="sxs-lookup"><span data-stu-id="aeab8-185">Description</span></span> | <span data-ttu-id="aeab8-186">預設</span><span class="sxs-lookup"><span data-stu-id="aeab8-186">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="aeab8-187">選擇性字串屬性。</span><span class="sxs-lookup"><span data-stu-id="aeab8-187">Optional string attribute.</span></span></p><p><span data-ttu-id="aeab8-188">**processPath** 中所指定可執行檔的引數。</span><span class="sxs-lookup"><span data-stu-id="aeab8-188">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="aeab8-189">選擇性的 Boolean 屬性。</span><span class="sxs-lookup"><span data-stu-id="aeab8-189">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="aeab8-190">如果為 true，就會抑制 [502.5 - 處理序失敗] 頁面，而優先顯示 *web.config* 中設定的 502 狀態碼頁面。</span><span class="sxs-lookup"><span data-stu-id="aeab8-190">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="aeab8-191">選擇性的 Boolean 屬性。</span><span class="sxs-lookup"><span data-stu-id="aeab8-191">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="aeab8-192">若為 true，則會將權杖轉送至以 `%ASPNETCORE_PORT%` 每個要求的標頭接聽的子進程 `'MS-ASPNETCORE-WINAUTHTOKEN'` 。</span><span class="sxs-lookup"><span data-stu-id="aeab8-192">If true, the token is forwarded to the child process listening on `%ASPNETCORE_PORT%` as a header `'MS-ASPNETCORE-WINAUTHTOKEN'` per request.</span></span> <span data-ttu-id="aeab8-193">該處理序需負責依據要求呼叫此權杖上的 CloseHandle。</span><span class="sxs-lookup"><span data-stu-id="aeab8-193">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="aeab8-194">選擇性字串屬性。</span><span class="sxs-lookup"><span data-stu-id="aeab8-194">Optional string attribute.</span></span></p><p><span data-ttu-id="aeab8-195">將裝載模型指定為同進程 (`InProcess` / `inprocess`) 或跨進程的 (`OutOfProcess` / `outofprocess`) 。</span><span class="sxs-lookup"><span data-stu-id="aeab8-195">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p><span data-ttu-id="aeab8-196">選擇性的整數屬性。</span><span class="sxs-lookup"><span data-stu-id="aeab8-196">Optional integer attribute.</span></span></p><p><span data-ttu-id="aeab8-197">指定 **processPath** 設定中所指定處理序執行個體每個應用程式可上調的數目。</span><span class="sxs-lookup"><span data-stu-id="aeab8-197">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="aeab8-198">&dagger;針對同處理序裝載，此值會限制為 `1`。</span><span class="sxs-lookup"><span data-stu-id="aeab8-198">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="aeab8-199">不建議使用 `processesPerApplication` 設定。</span><span class="sxs-lookup"><span data-stu-id="aeab8-199">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="aeab8-200">此屬性將在未來版本中移除。</span><span class="sxs-lookup"><span data-stu-id="aeab8-200">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="aeab8-201">預設： `1`</span><span class="sxs-lookup"><span data-stu-id="aeab8-201">Default: `1`</span></span><br><span data-ttu-id="aeab8-202">最小值：`1`</span><span class="sxs-lookup"><span data-stu-id="aeab8-202">Min: `1`</span></span><br><span data-ttu-id="aeab8-203">最大值：`100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="aeab8-203">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="aeab8-204">必要的字串屬性。</span><span class="sxs-lookup"><span data-stu-id="aeab8-204">Required string attribute.</span></span></p><p><span data-ttu-id="aeab8-205">啟動接聽 HTTP 要求之處理序的可執行檔路徑。</span><span class="sxs-lookup"><span data-stu-id="aeab8-205">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="aeab8-206">支援相對路徑。</span><span class="sxs-lookup"><span data-stu-id="aeab8-206">Relative paths are supported.</span></span> <span data-ttu-id="aeab8-207">如果路徑的開頭為 `.`，該路徑即被視為網站根目錄的相對路徑。</span><span class="sxs-lookup"><span data-stu-id="aeab8-207">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="aeab8-208">選擇性的整數屬性。</span><span class="sxs-lookup"><span data-stu-id="aeab8-208">Optional integer attribute.</span></span></p><p><span data-ttu-id="aeab8-209">指定允許 **processPath** 中所指定處理序每分鐘當機的次數。</span><span class="sxs-lookup"><span data-stu-id="aeab8-209">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="aeab8-210">如果超出此限制，模組就會在該分鐘的剩餘時間內停止啟動處理序。</span><span class="sxs-lookup"><span data-stu-id="aeab8-210">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="aeab8-211">不支援同處理序裝載。</span><span class="sxs-lookup"><span data-stu-id="aeab8-211">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="aeab8-212">預設： `10`</span><span class="sxs-lookup"><span data-stu-id="aeab8-212">Default: `10`</span></span><br><span data-ttu-id="aeab8-213">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="aeab8-213">Min: `0`</span></span><br><span data-ttu-id="aeab8-214">最大值︰`100`</span><span class="sxs-lookup"><span data-stu-id="aeab8-214">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="aeab8-215">選擇性的時間範圍屬性。</span><span class="sxs-lookup"><span data-stu-id="aeab8-215">Optional timespan attribute.</span></span></p><p><span data-ttu-id="aeab8-216">針對在 %ASPNETCORE_PORT% 進行接聽的處理序，指定 ASP.NET Core 模組等候回應的持續時間。</span><span class="sxs-lookup"><span data-stu-id="aeab8-216">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="aeab8-217">在 ASP.NET Core 2.1 或更新版本隨附的 ASP.NET Core 模組版本中，是以小時、分鐘及秒為單位來指定 `requestTimeout`。</span><span class="sxs-lookup"><span data-stu-id="aeab8-217">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="aeab8-218">不適用於同處理序裝載。</span><span class="sxs-lookup"><span data-stu-id="aeab8-218">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="aeab8-219">針對同處理序裝載，該模組會等待應用程式處理要求。</span><span class="sxs-lookup"><span data-stu-id="aeab8-219">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="aeab8-220">字串之分鐘和秒數的有效值介於 0-59。</span><span class="sxs-lookup"><span data-stu-id="aeab8-220">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="aeab8-221">在分鐘或秒數的值中使用 **60** 將會導致「500 - 內部伺服器錯誤」。</span><span class="sxs-lookup"><span data-stu-id="aeab8-221">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="aeab8-222">預設： `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="aeab8-222">Default: `00:02:00`</span></span><br><span data-ttu-id="aeab8-223">最小值：`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="aeab8-223">Min: `00:00:00`</span></span><br><span data-ttu-id="aeab8-224">最大值︰`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="aeab8-224">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="aeab8-225">選擇性的整數屬性。</span><span class="sxs-lookup"><span data-stu-id="aeab8-225">Optional integer attribute.</span></span></p><p><span data-ttu-id="aeab8-226">當偵測到 *app_offline.htm* 檔案時，模組等候可執行檔正常關閉的持續時間 (以秒為單位)。</span><span class="sxs-lookup"><span data-stu-id="aeab8-226">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="aeab8-227">預設： `10`</span><span class="sxs-lookup"><span data-stu-id="aeab8-227">Default: `10`</span></span><br><span data-ttu-id="aeab8-228">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="aeab8-228">Min: `0`</span></span><br><span data-ttu-id="aeab8-229">最大值︰`600`</span><span class="sxs-lookup"><span data-stu-id="aeab8-229">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="aeab8-230">選擇性的整數屬性。</span><span class="sxs-lookup"><span data-stu-id="aeab8-230">Optional integer attribute.</span></span></p><p><span data-ttu-id="aeab8-231">針對可執行檔啟動在連接埠進行接聽的處理序，模組等候的持續時間 (以秒為單位)。</span><span class="sxs-lookup"><span data-stu-id="aeab8-231">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="aeab8-232">如果超出此時間限制，模組就會終止處理序。</span><span class="sxs-lookup"><span data-stu-id="aeab8-232">If this time limit is exceeded, the module kills the process.</span></span></p><p><span data-ttu-id="aeab8-233">裝載同 *進程* 時： **進程不會重新開機** ， **也不會使用** **rapidFailsPerMinute** 設定。</span><span class="sxs-lookup"><span data-stu-id="aeab8-233">When hosting *in-process*: The process is **not** restarted and does **not** use the **rapidFailsPerMinute** setting.</span></span></p><p><span data-ttu-id="aeab8-234">裝載 *跨進程* 時：此模組會在收到新的要求時嘗試重新開機進程，並在後續的連入要求上繼續嘗試重新開機進程，除非應用程式在最後一次的輪流分鐘內無法啟動 **rapidFailsPerMinute** 次數。</span><span class="sxs-lookup"><span data-stu-id="aeab8-234">When hosting *out-of-process*: The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="aeab8-235">0 (零) 值 **不會** 視為無限逾時。</span><span class="sxs-lookup"><span data-stu-id="aeab8-235">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="aeab8-236">預設： `120`</span><span class="sxs-lookup"><span data-stu-id="aeab8-236">Default: `120`</span></span><br><span data-ttu-id="aeab8-237">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="aeab8-237">Min: `0`</span></span><br><span data-ttu-id="aeab8-238">最大值︰`3600`</span><span class="sxs-lookup"><span data-stu-id="aeab8-238">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="aeab8-239">選擇性的 Boolean 屬性。</span><span class="sxs-lookup"><span data-stu-id="aeab8-239">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="aeab8-240">如果為 true，就會將 **processPath** 中所指定處理序的 **stdout** 和 **stderr** 重新導向到 **stdoutLogFile** 中所指定的檔案。</span><span class="sxs-lookup"><span data-stu-id="aeab8-240">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="aeab8-241">選擇性字串屬性。</span><span class="sxs-lookup"><span data-stu-id="aeab8-241">Optional string attribute.</span></span></p><p><span data-ttu-id="aeab8-242">指定記錄來自 **processPath** 中所指定處理序之 **stdout** 和 **stderr** 的相對或絕對檔案路徑。</span><span class="sxs-lookup"><span data-stu-id="aeab8-242">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="aeab8-243">相對路徑是相對於網站的根目錄。</span><span class="sxs-lookup"><span data-stu-id="aeab8-243">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="aeab8-244">所有開頭為 `.` 的路徑都是網站根目錄的相對路徑，而所有其他路徑則視為絕對路徑。</span><span class="sxs-lookup"><span data-stu-id="aeab8-244">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="aeab8-245">建立記錄檔後，模組會建立路徑中提供的所有資料夾。</span><span class="sxs-lookup"><span data-stu-id="aeab8-245">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="aeab8-246">使用底線分隔符號，時間戳記、處理序識別碼和副檔名 (`.log`) 會新增至 **>stdoutlogfile** 路徑的最後一個區段。</span><span class="sxs-lookup"><span data-stu-id="aeab8-246">Using underscore delimiters, a timestamp, process ID, and file extension (`.log`) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="aeab8-247">如果 `.\logs\stdout` 以值的形式提供，就會將 stdout 記錄檔的範例儲存在 `stdout_20180205194132_1934.log` `logs` 2/5/2018 （19:41:32），並在處理序識別碼為1934時儲存在資料夾中。</span><span class="sxs-lookup"><span data-stu-id="aeab8-247">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as `stdout_20180205194132_1934.log` in the `logs` folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="aeab8-248">設定環境變數</span><span class="sxs-lookup"><span data-stu-id="aeab8-248">Set environment variables</span></span>

<span data-ttu-id="aeab8-249">您可以在 `processPath` 屬性中為處理序指定環境變數。</span><span class="sxs-lookup"><span data-stu-id="aeab8-249">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="aeab8-250">請使用 `<environmentVariables>` 集合元素的 `<environmentVariable>` 子元素來指定環境變數。</span><span class="sxs-lookup"><span data-stu-id="aeab8-250">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="aeab8-251">本節中所設定環境變數的優先順序會高於系統環境變數。</span><span class="sxs-lookup"><span data-stu-id="aeab8-251">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="aeab8-252">下列範例會在中設定兩個環境變數 `web.config` 。</span><span class="sxs-lookup"><span data-stu-id="aeab8-252">The following example sets two environment variables in `web.config`.</span></span> <span data-ttu-id="aeab8-253">`ASPNETCORE_ENVIRONMENT` 會將應用程式的環境設定為 `Development`。</span><span class="sxs-lookup"><span data-stu-id="aeab8-253">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="aeab8-254">開發人員可以在檔案中暫時設定此值 `web.config` ，以在偵錯工具例外狀況時強制載入 [開發人員例外狀況頁面](xref:fundamentals/error-handling) 。</span><span class="sxs-lookup"><span data-stu-id="aeab8-254">A developer may temporarily set this value in the `web.config` file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="aeab8-255">`CONFIG_DIR` 是一個使用者定義的環境變數範例，其中開發人員已撰寫程式碼，會在啟動時讀取值來構成用以載入應用程式設定檔的路徑。</span><span class="sxs-lookup"><span data-stu-id="aeab8-255">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> <span data-ttu-id="aeab8-256">直接在中設定環境的替代方式， `web.config` 就是在 `<EnvironmentName>` [發行設定檔 (`.pubxml`) ](xref:host-and-deploy/visual-studio-publish-profiles) 或專案檔中包含屬性。</span><span class="sxs-lookup"><span data-stu-id="aeab8-256">An alternative to setting the environment directly in `web.config` is to include the `<EnvironmentName>` property in the [publish profile (`.pubxml`)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="aeab8-257">這種方法 `web.config` 會設定發行專案時的環境：</span><span class="sxs-lookup"><span data-stu-id="aeab8-257">This approach sets the environment in `web.config` when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="aeab8-258">請只有在未受信任網路 (例如網際網路) 無法存取的暫存和測試伺服器上，才將 `ASPNETCORE_ENVIRONMENT` 環境變數設定為 `Development`。</span><span class="sxs-lookup"><span data-stu-id="aeab8-258">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## `app_offline.htm`

<span data-ttu-id="aeab8-259">如果 `app_offline.htm` 在應用程式的根目錄中偵測到具有名稱的檔案，ASP.NET Core 模組會嘗試以正常方式關閉應用程式，並停止處理傳入的要求。</span><span class="sxs-lookup"><span data-stu-id="aeab8-259">If a file with the name `app_offline.htm` is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="aeab8-260">如果在經過 `shutdownTimeLimit` 中所定義的秒數之後，應用程式仍然在執行，ASP.NET Core Module 就會終止執行中的處理序。</span><span class="sxs-lookup"><span data-stu-id="aeab8-260">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="aeab8-261">當檔案 `app_offline.htm` 存在時，ASP.NET Core 模組會藉由傳回檔案的內容來回應要求 `app_offline.htm` 。</span><span class="sxs-lookup"><span data-stu-id="aeab8-261">While the `app_offline.htm` file is present, the ASP.NET Core Module responds to requests by sending back the contents of the `app_offline.htm` file.</span></span> <span data-ttu-id="aeab8-262">移除檔案時 `app_offline.htm` ，下一個要求會啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="aeab8-262">When the `app_offline.htm` file is removed, the next request starts the app.</span></span>

<span data-ttu-id="aeab8-263">使用跨處理序裝載模型時，若未開啟連線，應用程式可能無法立即關閉。</span><span class="sxs-lookup"><span data-stu-id="aeab8-263">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="aeab8-264">例如，WebSocket 連線可能會延遲應用程式關機。</span><span class="sxs-lookup"><span data-stu-id="aeab8-264">For example, a WebSocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="aeab8-265">啟動錯誤頁面</span><span class="sxs-lookup"><span data-stu-id="aeab8-265">Start-up error page</span></span>

<span data-ttu-id="aeab8-266">同處理序及跨處理序裝載無法啟動應用程式時，兩者皆會產生自訂錯誤頁面。</span><span class="sxs-lookup"><span data-stu-id="aeab8-266">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="aeab8-267">若 ASP.NET Core 模組找不到同處理序或跨處理序要求處理常式時，就會顯示 [500.0 - 同處理序/跨處理序處理常式載入失敗] 狀態碼頁面。</span><span class="sxs-lookup"><span data-stu-id="aeab8-267">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="aeab8-268">對於同處理序裝載，若 ASP.NET Core 模組無法啟動應用程式，就會顯示 [500.30 - 啟動失敗] 狀態碼頁面。</span><span class="sxs-lookup"><span data-stu-id="aeab8-268">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="aeab8-269">對於跨處理序裝載，若 ASP.NET Core 模組無法啟動後端處理序，或後端處理序啟動但無法在所設定的連接埠上進行接聽，就會顯示 [502.5 - 處理序失敗] 狀態碼頁面。</span><span class="sxs-lookup"><span data-stu-id="aeab8-269">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="aeab8-270">若要避免此頁面產生並還原至預設的 IIS 5xx 狀態碼頁面，請使用 `disableStartUpErrorPage` 屬性。</span><span class="sxs-lookup"><span data-stu-id="aeab8-270">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="aeab8-271">如需有關設定自訂錯誤訊息的詳細資訊，請參閱 [HTTP 錯誤`<httpErrors>`](/iis/configuration/system.webServer/httpErrors/)。</span><span class="sxs-lookup"><span data-stu-id="aeab8-271">For more information on configuring custom error messages, see [HTTP Errors `<httpErrors>`](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="aeab8-272">記錄檔建立和重新導向</span><span class="sxs-lookup"><span data-stu-id="aeab8-272">Log creation and redirection</span></span>

<span data-ttu-id="aeab8-273">如果已設定 `aspNetCore` 元素的 `stdoutLogEnabled` 和 `stdoutLogFile` 屬性，ASP.NET Core 模組就會將 stdout 和 stderr 主控台輸出重新導向到磁碟。</span><span class="sxs-lookup"><span data-stu-id="aeab8-273">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="aeab8-274">建立記錄檔後，模組會建立 `stdoutLogFile` 路徑中的所有資料夾。</span><span class="sxs-lookup"><span data-stu-id="aeab8-274">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="aeab8-275">應用程式集區必須具有記錄檔寫入位置的寫入權限 (請使用 `IIS AppPool\<app_pool_name>` 來提供寫入權限)。</span><span class="sxs-lookup"><span data-stu-id="aeab8-275">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="aeab8-276">除非發生處理序回收/重新啟動，否則不會輪替記錄檔。</span><span class="sxs-lookup"><span data-stu-id="aeab8-276">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="aeab8-277">主機服務提供者必須負責限制記錄檔所使用的磁碟空間。</span><span class="sxs-lookup"><span data-stu-id="aeab8-277">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="aeab8-278">只有在 IIS 上裝載時，或使用 [VISUAL STUDIO iis 的開發階段支援來](xref:host-and-deploy/iis/development-time-iis-support)針對應用程式啟動問題進行疑難排解時，才建議使用 stdout 記錄檔，而不是在本機進行偵錯工具並使用 IIS Express 執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="aeab8-278">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="aeab8-279">請勿將 stdout 記錄檔用來進行一般應用程式記錄。</span><span class="sxs-lookup"><span data-stu-id="aeab8-279">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="aeab8-280">針對 ASP.NET Core 應用程式中的例行性記錄，請使用會限制記錄檔大小並輪替記錄檔的記錄程式庫。</span><span class="sxs-lookup"><span data-stu-id="aeab8-280">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="aeab8-281">如需詳細資訊，請參閱[協力廠商記錄提供者](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="aeab8-281">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="aeab8-282">建立記錄檔時，系統會自動新增時間戳記和副檔名。</span><span class="sxs-lookup"><span data-stu-id="aeab8-282">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="aeab8-283">記錄檔名稱的組成方式是將時間戳記、處理序識別碼和副檔名 () 附加 `.log` 至路徑的最後一個區段 `stdoutLogFile` (通常 `stdout`) 以底線分隔。</span><span class="sxs-lookup"><span data-stu-id="aeab8-283">The log file name is composed by appending the timestamp, process ID, and file extension (`.log`) to the last segment of the `stdoutLogFile` path (typically `stdout`) delimited by underscores.</span></span> <span data-ttu-id="aeab8-284">如果 `stdoutLogFile` 路徑以結尾 `stdout` ，則在2/5/2018 （19:42:32）上建立的應用1934程式記錄檔名稱會是 `stdout_20180205194132_1934.log` 。</span><span class="sxs-lookup"><span data-stu-id="aeab8-284">If the `stdoutLogFile` path ends with `stdout`, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name `stdout_20180205194132_1934.log`.</span></span>

<span data-ttu-id="aeab8-285">若 `stdoutLogEnabled` 為 false，會擷取在應用程式啟動時發生的錯誤，並發出最大 30KB 的事件記錄檔。</span><span class="sxs-lookup"><span data-stu-id="aeab8-285">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="aeab8-286">啟動之後，就會捨棄其他的記錄檔。</span><span class="sxs-lookup"><span data-stu-id="aeab8-286">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="aeab8-287">下列範例專案會 `aspNetCore` 在相對路徑設定 stdout 記錄 `.\log\` 。</span><span class="sxs-lookup"><span data-stu-id="aeab8-287">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="aeab8-288">請確認 AppPool 使用者身分識別具備所提供路徑的寫入權限。</span><span class="sxs-lookup"><span data-stu-id="aeab8-288">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="aeab8-289">針對 Azure App Service 部署發行應用程式時，Web SDK 會將 `stdoutLogFile` 值設定為 `\\?\%home%\LogFiles\stdout` 。</span><span class="sxs-lookup"><span data-stu-id="aeab8-289">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="aeab8-290">`%home`環境變數是針對 Azure App Service 所裝載的應用程式預先定義。</span><span class="sxs-lookup"><span data-stu-id="aeab8-290">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="aeab8-291">若要建立記錄篩選規則，請參閱 ASP.NET Core 記錄檔[集的設定和](xref:fundamentals/logging/index#log-filtering)[記錄篩選](xref:fundamentals/logging/index#log-filtering)區段。</span><span class="sxs-lookup"><span data-stu-id="aeab8-291">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="aeab8-292">如需路徑格式的詳細資訊，請參閱 [Windows 系統上的檔案路徑格式](/dotnet/standard/io/file-path-formats)。</span><span class="sxs-lookup"><span data-stu-id="aeab8-292">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="aeab8-293">增強型診斷記錄</span><span class="sxs-lookup"><span data-stu-id="aeab8-293">Enhanced diagnostic logs</span></span>

<span data-ttu-id="aeab8-294">ASP.NET Core 模組是可設定的，以提供增強型診斷記錄。</span><span class="sxs-lookup"><span data-stu-id="aeab8-294">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="aeab8-295">將專案加入 `<handlerSettings>` 至 `<aspNetCore>` 中的元素 `web.config` 。</span><span class="sxs-lookup"><span data-stu-id="aeab8-295">Add the `<handlerSettings>` element to the `<aspNetCore>` element in `web.config`.</span></span> <span data-ttu-id="aeab8-296">將 `debugLevel` 設定為 `TRACE` 會公開精確性更高的診斷資訊：</span><span class="sxs-lookup"><span data-stu-id="aeab8-296">Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="aeab8-297">`logs`當記錄檔建立時，模組會建立上述範例) 中路徑 (的任何資料夾。</span><span class="sxs-lookup"><span data-stu-id="aeab8-297">Any folders in the path (`logs` in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="aeab8-298">應用程式集區必須具有寫入記錄檔之位置的寫入權限 (使用 `IIS AppPool\{APP POOL NAME}` ，其中預留位置 `{APP POOL NAME}` 為應用程式集區名稱，以提供寫入權限) 。</span><span class="sxs-lookup"><span data-stu-id="aeab8-298">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}`, where the placeholder `{APP POOL NAME}` is the app pool name, to provide write permission).</span></span>

<span data-ttu-id="aeab8-299">偵錯層級 (`debugLevel`) 值可以同時包含層級和位置。</span><span class="sxs-lookup"><span data-stu-id="aeab8-299">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="aeab8-300">層級 (順序從最不詳細到最詳細)：</span><span class="sxs-lookup"><span data-stu-id="aeab8-300">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="aeab8-301">ERROR</span><span class="sxs-lookup"><span data-stu-id="aeab8-301">ERROR</span></span>
* <span data-ttu-id="aeab8-302">WARNING</span><span class="sxs-lookup"><span data-stu-id="aeab8-302">WARNING</span></span>
* <span data-ttu-id="aeab8-303">INFO</span><span class="sxs-lookup"><span data-stu-id="aeab8-303">INFO</span></span>
* <span data-ttu-id="aeab8-304">TRACE</span><span class="sxs-lookup"><span data-stu-id="aeab8-304">TRACE</span></span>

<span data-ttu-id="aeab8-305">位置 (允許多個位置)：</span><span class="sxs-lookup"><span data-stu-id="aeab8-305">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="aeab8-306">主控台</span><span class="sxs-lookup"><span data-stu-id="aeab8-306">CONSOLE</span></span>
* <span data-ttu-id="aeab8-307">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="aeab8-307">EVENTLOG</span></span>
* <span data-ttu-id="aeab8-308">FILE</span><span class="sxs-lookup"><span data-stu-id="aeab8-308">FILE</span></span>

<span data-ttu-id="aeab8-309">也可以透過環境變數提供處理常式設定：</span><span class="sxs-lookup"><span data-stu-id="aeab8-309">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="aeab8-310">`ASPNETCORE_MODULE_DEBUG_FILE`： Debug 記錄檔的路徑。</span><span class="sxs-lookup"><span data-stu-id="aeab8-310">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="aeab8-311"> (預設： `aspnetcore-debug.log`) </span><span class="sxs-lookup"><span data-stu-id="aeab8-311">(Default: `aspnetcore-debug.log`)</span></span>
* <span data-ttu-id="aeab8-312">`ASPNETCORE_MODULE_DEBUG`： Debug 層級設定。</span><span class="sxs-lookup"><span data-stu-id="aeab8-312">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="aeab8-313">在部署中保持啟用偵錯記錄的時間，**不要** 超過針對問題進行排解疑難所需的時間。</span><span class="sxs-lookup"><span data-stu-id="aeab8-313">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="aeab8-314">記錄的大小不受限制。</span><span class="sxs-lookup"><span data-stu-id="aeab8-314">The size of the log isn't limited.</span></span> <span data-ttu-id="aeab8-315">保持啟用偵錯記錄可能會耗盡可用磁碟空間，並讓伺服器或應用程式服務當機。</span><span class="sxs-lookup"><span data-stu-id="aeab8-315">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="aeab8-316">如需檔案中專案的範例，請參閱 [web.config](#configuration-with-webconfig) 的設定 `aspNetCore` `web.config` 。</span><span class="sxs-lookup"><span data-stu-id="aeab8-316">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the `web.config` file.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="aeab8-317">修改堆疊大小</span><span class="sxs-lookup"><span data-stu-id="aeab8-317">Modify the stack size</span></span>

<span data-ttu-id="aeab8-318">*只有在使用同進程裝載模型時才適用。*</span><span class="sxs-lookup"><span data-stu-id="aeab8-318">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="aeab8-319">使用中的 `stackSize` 設定（以位元組為單位）設定受管理的堆疊大小 `web.config` 。</span><span class="sxs-lookup"><span data-stu-id="aeab8-319">Configure the managed stack size using the `stackSize` setting in bytes in `web.config`.</span></span> <span data-ttu-id="aeab8-320">預設大小為1048576個位元組 (1 MB) 。</span><span class="sxs-lookup"><span data-stu-id="aeab8-320">The default size is 1,048,576 bytes (1 MB).</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="aeab8-321">Proxy 組態使用 HTTP 通訊協定和配對權杖</span><span class="sxs-lookup"><span data-stu-id="aeab8-321">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="aeab8-322">僅適用於跨處理序裝載。</span><span class="sxs-lookup"><span data-stu-id="aeab8-322">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="aeab8-323">在 ASP.NET Core 模組與 Kestrel 之間建立的 Proxy 會使用 HTTP 通訊協定。</span><span class="sxs-lookup"><span data-stu-id="aeab8-323">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="aeab8-324">沒有從伺服器外的位置竊聽模組與 Kestrel 之間流量的風險。</span><span class="sxs-lookup"><span data-stu-id="aeab8-324">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="aeab8-325">配對權杖用來保證 Kestrel 所接收的要求已由 IIS 代理，而且不是來自其他來源。</span><span class="sxs-lookup"><span data-stu-id="aeab8-325">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="aeab8-326">模組會建立配對權杖，並將其設定成環境變數 (`ASPNETCORE_TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="aeab8-326">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="aeab8-327">配對權杖也會設定成每個代理要求的標頭 (`MS-ASPNETCORE-TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="aeab8-327">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="aeab8-328">IIS 中介軟體會檢查其收到的每個要求，以確認配對權杖的標頭值符合環境變數值。</span><span class="sxs-lookup"><span data-stu-id="aeab8-328">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="aeab8-329">如果權杖值不相符，將記錄並拒絕要求。</span><span class="sxs-lookup"><span data-stu-id="aeab8-329">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="aeab8-330">使用者無法從伺服器外的位置存取配對權杖環境變數，以及模組與 Kestrel 之間的流量。</span><span class="sxs-lookup"><span data-stu-id="aeab8-330">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="aeab8-331">在不知道配對權杖值的情況下，攻擊者無法略過 IIS 中介軟體的檢查送出要求。</span><span class="sxs-lookup"><span data-stu-id="aeab8-331">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="aeab8-332">具有 IIS 共用設定的 ASP.NET Core 模組</span><span class="sxs-lookup"><span data-stu-id="aeab8-332">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="aeab8-333">ASP.NET Core 模組安裝程式會以 **TrustedInstaller** 帳戶的權限執行。</span><span class="sxs-lookup"><span data-stu-id="aeab8-333">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="aeab8-334">由於本機系統帳戶對於 IIS 共用設定所使用的共用路徑沒有 modify 許可權，因此，安裝程式在嘗試于共用上的檔案中設定模組設定時，會擲回「拒絕存取」錯誤 `applicationHost.config` 。</span><span class="sxs-lookup"><span data-stu-id="aeab8-334">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the `applicationHost.config` file on the share.</span></span>

<span data-ttu-id="aeab8-335">在與 IIS 安裝相同的電腦上使用 IIS 共用設定時，請執行 ASP.NET Core 裝載套件組合安裝程式並將 `OPT_NO_SHARED_CONFIG_CHECK` 參數設為 `1`：</span><span class="sxs-lookup"><span data-stu-id="aeab8-335">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="aeab8-336">若共用設定的路徑位於與 IIS 安裝不同的電腦上，請遵循下列步驟：</span><span class="sxs-lookup"><span data-stu-id="aeab8-336">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="aeab8-337">停用「IIS 共用設定」。</span><span class="sxs-lookup"><span data-stu-id="aeab8-337">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="aeab8-338">執行安裝程式。</span><span class="sxs-lookup"><span data-stu-id="aeab8-338">Run the installer.</span></span>
1. <span data-ttu-id="aeab8-339">將更新的檔案匯出 `applicationHost.config` 至共用。</span><span class="sxs-lookup"><span data-stu-id="aeab8-339">Export the updated `applicationHost.config` file to the share.</span></span>
1. <span data-ttu-id="aeab8-340">重新啟用「IIS 共用設定」。</span><span class="sxs-lookup"><span data-stu-id="aeab8-340">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="aeab8-341">模組版本和裝載套件組合安裝程式記錄檔</span><span class="sxs-lookup"><span data-stu-id="aeab8-341">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="aeab8-342">判斷已安裝的 ASP.NET Core 模組版本：</span><span class="sxs-lookup"><span data-stu-id="aeab8-342">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="aeab8-343">在主控系統上，流覽至 `%windir%\System32\inetsrv` 。</span><span class="sxs-lookup"><span data-stu-id="aeab8-343">On the hosting system, navigate to `%windir%\System32\inetsrv`.</span></span>
1. <span data-ttu-id="aeab8-344">找出檔案 `aspnetcore.dll` 。</span><span class="sxs-lookup"><span data-stu-id="aeab8-344">Locate the `aspnetcore.dll` file.</span></span>
1. <span data-ttu-id="aeab8-345">在該檔案上按一下滑鼠右鍵，然後從關聯式功能表中選取 [內容]。</span><span class="sxs-lookup"><span data-stu-id="aeab8-345">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="aeab8-346">選取 [ **詳細資料** ] 索引標籤。檔案 **版本** 和 **產品版本** 代表已安裝的模組版本。</span><span class="sxs-lookup"><span data-stu-id="aeab8-346">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="aeab8-347">模組的裝載套件組合安裝程式記錄檔位於 `C:\Users\%UserName%\AppData\Local\Temp` 。</span><span class="sxs-lookup"><span data-stu-id="aeab8-347">The Hosting Bundle installer logs for the module are found at `C:\Users\%UserName%\AppData\Local\Temp`.</span></span> <span data-ttu-id="aeab8-348">檔案命名為 `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`。</span><span class="sxs-lookup"><span data-stu-id="aeab8-348">The file is named `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="aeab8-349">模組、結構描述及設定檔位置</span><span class="sxs-lookup"><span data-stu-id="aeab8-349">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="aeab8-350">模組</span><span class="sxs-lookup"><span data-stu-id="aeab8-350">Module</span></span>

<span data-ttu-id="aeab8-351">**IIS (x86/amd64)：**</span><span class="sxs-lookup"><span data-stu-id="aeab8-351">**IIS (x86/amd64):**</span></span>

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

<span data-ttu-id="aeab8-352">**IIS Express (x86/amd64)：**</span><span class="sxs-lookup"><span data-stu-id="aeab8-352">**IIS Express (x86/amd64):**</span></span>

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a><span data-ttu-id="aeab8-353">結構描述</span><span class="sxs-lookup"><span data-stu-id="aeab8-353">Schema</span></span>

<span data-ttu-id="aeab8-354">**IIS**</span><span class="sxs-lookup"><span data-stu-id="aeab8-354">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

<span data-ttu-id="aeab8-355">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="aeab8-355">**IIS Express**</span></span>

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a><span data-ttu-id="aeab8-356">組態</span><span class="sxs-lookup"><span data-stu-id="aeab8-356">Configuration</span></span>

<span data-ttu-id="aeab8-357">**IIS**</span><span class="sxs-lookup"><span data-stu-id="aeab8-357">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\applicationHost.config`

<span data-ttu-id="aeab8-358">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="aeab8-358">**IIS Express**</span></span>

* <span data-ttu-id="aeab8-359">Visual Studio： `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span><span class="sxs-lookup"><span data-stu-id="aeab8-359">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span></span>

* <span data-ttu-id="aeab8-360">*iisexpress.exe* Cli： `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span><span class="sxs-lookup"><span data-stu-id="aeab8-360">*iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span></span>

<span data-ttu-id="aeab8-361">您可以藉由在檔案中搜尋來找到檔案 `aspnetcore` `applicationHost.config` 。</span><span class="sxs-lookup"><span data-stu-id="aeab8-361">The files can be found by searching for `aspnetcore` in the `applicationHost.config` file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="aeab8-362">ASP.NET Core 模組是一種原生 IIS 模組，可外掛至 IIS 管線以便：</span><span class="sxs-lookup"><span data-stu-id="aeab8-362">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="aeab8-363">在 IIS 工作者處理序 (`w3wp.exe`) 中裝載 ASP.NET Core 應用程式 (稱為[同處理序裝載模型](#in-process-hosting-model))。</span><span class="sxs-lookup"><span data-stu-id="aeab8-363">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="aeab8-364">將 Web 要求轉送到執行 [Kestrel 伺服器](xref:fundamentals/servers/kestrel)的後端 ASP.NET Core 應用程式 (稱為[跨處理序裝載模型](#out-of-process-hosting-model))。</span><span class="sxs-lookup"><span data-stu-id="aeab8-364">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="aeab8-365">支援的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="aeab8-365">Supported Windows versions:</span></span>

* <span data-ttu-id="aeab8-366">Windows 7 或更新版本</span><span class="sxs-lookup"><span data-stu-id="aeab8-366">Windows 7 or later</span></span>
* <span data-ttu-id="aeab8-367">Windows Server 2008 R2 或更新版本</span><span class="sxs-lookup"><span data-stu-id="aeab8-367">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="aeab8-368">同處理序裝載時，模組會使用 IIS 的同處理序伺服程式實作，稱為 IIS HTTP 伺服器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="aeab8-368">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="aeab8-369">跨處理序裝載時，該模組只適用於 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="aeab8-369">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="aeab8-370">模組無法搭配 [HTTP.sys](xref:fundamentals/servers/httpsys)運作。</span><span class="sxs-lookup"><span data-stu-id="aeab8-370">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="aeab8-371">裝載模型</span><span class="sxs-lookup"><span data-stu-id="aeab8-371">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="aeab8-372">同處理序裝載模型</span><span class="sxs-lookup"><span data-stu-id="aeab8-372">In-process hosting model</span></span>

<span data-ttu-id="aeab8-373">若要設定同處理序裝載的應用程式，請將 `<AspNetCoreHostingModel>` 屬性新增至應用程式的專案檔，其值為 `InProcess` (跨處理序裝載是使用 `OutOfProcess` 設定)：</span><span class="sxs-lookup"><span data-stu-id="aeab8-373">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="aeab8-374">以 .NET Framework 為目標的 ASP.NET Core 應用程式不支援處理序內裝載模型。</span><span class="sxs-lookup"><span data-stu-id="aeab8-374">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="aeab8-375">的值 `<AspNetCoreHostingModel>` 不區分大小寫，因此 `inprocess` 和 `outofprocess` 都是有效的值。</span><span class="sxs-lookup"><span data-stu-id="aeab8-375">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="aeab8-376">如果檔案中沒有 `<AspNetCoreHostingModel>` 屬性，預設值為 `OutOfProcess`。</span><span class="sxs-lookup"><span data-stu-id="aeab8-376">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="aeab8-377">同處理序裝載時具有下列特性：</span><span class="sxs-lookup"><span data-stu-id="aeab8-377">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="aeab8-378">使用 IIS HTTP 伺服器 (`IISHttpServer`) 而不是 [Kestrel](xref:fundamentals/servers/kestrel) 伺服器。</span><span class="sxs-lookup"><span data-stu-id="aeab8-378">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="aeab8-379">針對同處理序，[CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) 會呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> 來：</span><span class="sxs-lookup"><span data-stu-id="aeab8-379">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="aeab8-380">註冊 `IISHttpServer`。</span><span class="sxs-lookup"><span data-stu-id="aeab8-380">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="aeab8-381">設定伺服器在 ASP.NET Core 模組後方執行時應該接聽的連接埠和基底路徑。</span><span class="sxs-lookup"><span data-stu-id="aeab8-381">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="aeab8-382">設定主機以擷取啟動錯誤。</span><span class="sxs-lookup"><span data-stu-id="aeab8-382">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="aeab8-383">[requestTimeout 屬性](#attributes-of-the-aspnetcore-element)不適用於同處理序裝載。</span><span class="sxs-lookup"><span data-stu-id="aeab8-383">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="aeab8-384">不支援在應用程式之間共用應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="aeab8-384">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="aeab8-385">每個應用程式使用一個應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="aeab8-385">Use one app pool per app.</span></span>

* <span data-ttu-id="aeab8-386">使用 [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) 或以手動方式[將 app_offline.htm 檔案放入部署](xref:host-and-deploy/iis/index#locked-deployment-files)時，若未開啟連線，應用程式可能無法立即關閉。</span><span class="sxs-lookup"><span data-stu-id="aeab8-386">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="aeab8-387">例如，WebSocket 連線可能會延遲應用程式關閉。</span><span class="sxs-lookup"><span data-stu-id="aeab8-387">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="aeab8-388">應用程式的架構 (位元) 和已安裝的執行階段 (x64 或 x86) 必須符合應用程式集區的架構。</span><span class="sxs-lookup"><span data-stu-id="aeab8-388">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="aeab8-389">偵測到用戶端中斷連線。</span><span class="sxs-lookup"><span data-stu-id="aeab8-389">Client disconnects are detected.</span></span> <span data-ttu-id="aeab8-390">用戶端中斷連線時，會取消 [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) 取消權杖。</span><span class="sxs-lookup"><span data-stu-id="aeab8-390">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="aeab8-391">在 ASP.NET Core 2.2.1 或更早版本中，<xref:System.IO.Directory.GetCurrentDirectory*> 會傳回 IIS 所啟動之處理序的背景工作目錄，而非應用程式的目錄 (例如 *w3wp.exe* 為 *C:\Windows\System32\inetsrv*)。</span><span class="sxs-lookup"><span data-stu-id="aeab8-391">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="aeab8-392">如需設定應用程式目前所在目錄的範例程式碼，請參閱 [CurrentDirectoryHelpers 類別](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs)。</span><span class="sxs-lookup"><span data-stu-id="aeab8-392">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="aeab8-393">呼叫 `SetCurrentDirectory` 方法。</span><span class="sxs-lookup"><span data-stu-id="aeab8-393">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="aeab8-394">後續呼叫 <xref:System.IO.Directory.GetCurrentDirectory*> 會提供應用程式的目錄。</span><span class="sxs-lookup"><span data-stu-id="aeab8-394">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="aeab8-395">裝載同處理序時，不會內部呼叫 <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> 來將使用者初始化。</span><span class="sxs-lookup"><span data-stu-id="aeab8-395">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="aeab8-396">因此，預設會在未啟動每個驗證之後，使用 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 實作來轉換宣告。</span><span class="sxs-lookup"><span data-stu-id="aeab8-396">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="aeab8-397">使用 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 實作來轉換宣告時，請呼叫 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 來新增入驗證服務：</span><span class="sxs-lookup"><span data-stu-id="aeab8-397">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="aeab8-398">跨處理序裝載模型</span><span class="sxs-lookup"><span data-stu-id="aeab8-398">Out-of-process hosting model</span></span>

<span data-ttu-id="aeab8-399">若要設定跨處理序裝載的應用程式，請在專案檔中使用下列任一方法：</span><span class="sxs-lookup"><span data-stu-id="aeab8-399">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="aeab8-400">請勿指定 `<AspNetCoreHostingModel>` 屬性。</span><span class="sxs-lookup"><span data-stu-id="aeab8-400">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="aeab8-401">如果檔案中沒有 `<AspNetCoreHostingModel>` 屬性，預設值為 `OutOfProcess`。</span><span class="sxs-lookup"><span data-stu-id="aeab8-401">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="aeab8-402">將 `<AspNetCoreHostingModel>` 屬性的值設定為 `OutOfProcess` (同處理序裝載是使用 `InProcess` 設定)：</span><span class="sxs-lookup"><span data-stu-id="aeab8-402">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="aeab8-403">值不區分大小寫，因此 `inprocess` 和 `outofprocess` 都是有效的值。</span><span class="sxs-lookup"><span data-stu-id="aeab8-403">The value is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="aeab8-404">使用 [Kestrel](xref:fundamentals/servers/kestrel) 伺服器而不是 IIS HTTP 伺服器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="aeab8-404">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="aeab8-405">針對跨處理序，[CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) 會呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 來：</span><span class="sxs-lookup"><span data-stu-id="aeab8-405">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="aeab8-406">設定伺服器在 ASP.NET Core 模組後方執行時應該接聽的連接埠和基底路徑。</span><span class="sxs-lookup"><span data-stu-id="aeab8-406">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="aeab8-407">設定主機以擷取啟動錯誤。</span><span class="sxs-lookup"><span data-stu-id="aeab8-407">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="aeab8-408">裝載模型變更</span><span class="sxs-lookup"><span data-stu-id="aeab8-408">Hosting model changes</span></span>

<span data-ttu-id="aeab8-409">如果 `hostingModel` 設定在 *web.config* 檔案中已有所變更 (如 [使用 web.config 進行設定](#configuration-with-webconfig)一節中所說明)，模組會回收 IIS 的工作者處理序。</span><span class="sxs-lookup"><span data-stu-id="aeab8-409">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="aeab8-410">針對 IIS Express，模組不會回收工作者處理序，但會改為觸發目前 IIS Express 處理序的正常關閉。</span><span class="sxs-lookup"><span data-stu-id="aeab8-410">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="aeab8-411">應用程式的下一個要求會繁衍新的 IIS Express 處理序。</span><span class="sxs-lookup"><span data-stu-id="aeab8-411">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="aeab8-412">程序名稱</span><span class="sxs-lookup"><span data-stu-id="aeab8-412">Process name</span></span>

<span data-ttu-id="aeab8-413">`Process.GetCurrentProcess().ProcessName` 會報告 `w3wp`/`iisexpress` (同處理序) 或 `dotnet` (跨處理序)。</span><span class="sxs-lookup"><span data-stu-id="aeab8-413">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="aeab8-414">許多如 Windows 驗證等原生模組仍在使用中。</span><span class="sxs-lookup"><span data-stu-id="aeab8-414">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="aeab8-415">若要深入了解搭配 ASP.NET Core 模組的使用中 IIS 模組，請參閱<xref:host-and-deploy/iis/modules>。</span><span class="sxs-lookup"><span data-stu-id="aeab8-415">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="aeab8-416">ASP.NET Core 模組也可以：</span><span class="sxs-lookup"><span data-stu-id="aeab8-416">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="aeab8-417">設定背景工作處理序的環境變數。</span><span class="sxs-lookup"><span data-stu-id="aeab8-417">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="aeab8-418">將 stdout 輸出記錄到檔案儲存區，以針對啟動問題進行疑難排解。</span><span class="sxs-lookup"><span data-stu-id="aeab8-418">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="aeab8-419">轉送 Windows 驗證權杖。</span><span class="sxs-lookup"><span data-stu-id="aeab8-419">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="aeab8-420">如何安裝和使用 ASP.NET Core 模組</span><span class="sxs-lookup"><span data-stu-id="aeab8-420">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="aeab8-421">如需如何安裝 ASP.NET Core 模組的指示，請參閱[安裝 .NET Core 裝載套件組合](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="aeab8-421">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span> <span data-ttu-id="aeab8-422">ASP.NET Core 模組向前及向後相容于 .NET 的 LTS 版本。</span><span class="sxs-lookup"><span data-stu-id="aeab8-422">The ASP.NET Core Module is forward and backward compatible with LTS releases of .NET.</span></span>

[!INCLUDE[](~/includes/announcements.md)]

## <a name="configuration-with-webconfig"></a><span data-ttu-id="aeab8-423">使用 web.config 進行設定</span><span class="sxs-lookup"><span data-stu-id="aeab8-423">Configuration with web.config</span></span>

<span data-ttu-id="aeab8-424">設定 ASP.NET Core 模組時，是使用網站 *web.config* 檔案中 `system.webServer` 節點的 `aspNetCore` 區段來設定。</span><span class="sxs-lookup"><span data-stu-id="aeab8-424">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="aeab8-425">以下 *web.config* 檔案是針對 [架構相依部署](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)發佈的檔案，會設定 ASP.NET Core 模組來處理網站要求：</span><span class="sxs-lookup"><span data-stu-id="aeab8-425">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="aeab8-426">以下 *web.config* 是針對 [自封式部署](/dotnet/articles/core/deploying/#self-contained-deployments-scd)發佈的檔案：</span><span class="sxs-lookup"><span data-stu-id="aeab8-426">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="aeab8-427"><xref:System.Configuration.SectionInformation.InheritInChildApplications*>屬性設定為，表示在專案 `false` 內指定的設定不會 [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 由位於應用程式子目錄中的應用程式所繼承。</span><span class="sxs-lookup"><span data-stu-id="aeab8-427">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="aeab8-428">將應用程式部署至 [Azure App Service](https://azure.microsoft.com/services/app-service/) 時，`stdoutLogFile` 路徑會設定為 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="aeab8-428">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="aeab8-429">此路徑會將 stdout 記錄檔儲存至 [LogFiles] 資料夾，這是服務自動建立的位置。</span><span class="sxs-lookup"><span data-stu-id="aeab8-429">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="aeab8-430">如需有關 IIS 子應用程式設定的詳細資訊，請參閱 <xref:host-and-deploy/iis/index#sub-applications>。</span><span class="sxs-lookup"><span data-stu-id="aeab8-430">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="aeab8-431">aspNetCore 元素的屬性</span><span class="sxs-lookup"><span data-stu-id="aeab8-431">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="aeab8-432">屬性</span><span class="sxs-lookup"><span data-stu-id="aeab8-432">Attribute</span></span> | <span data-ttu-id="aeab8-433">描述</span><span class="sxs-lookup"><span data-stu-id="aeab8-433">Description</span></span> | <span data-ttu-id="aeab8-434">預設</span><span class="sxs-lookup"><span data-stu-id="aeab8-434">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="aeab8-435">選擇性字串屬性。</span><span class="sxs-lookup"><span data-stu-id="aeab8-435">Optional string attribute.</span></span></p><p><span data-ttu-id="aeab8-436">中指定之可執行檔的引數 `processPath` 。</span><span class="sxs-lookup"><span data-stu-id="aeab8-436">Arguments to the executable specified in `processPath`.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="aeab8-437">選擇性的 Boolean 屬性。</span><span class="sxs-lookup"><span data-stu-id="aeab8-437">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="aeab8-438">如果為 true，就會抑制 [502.5 - 處理序失敗] 頁面，而優先顯示 *web.config* 中設定的 502 狀態碼頁面。</span><span class="sxs-lookup"><span data-stu-id="aeab8-438">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="aeab8-439">選擇性的 Boolean 屬性。</span><span class="sxs-lookup"><span data-stu-id="aeab8-439">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="aeab8-440">如果為 true，就會依據要求將權杖以標頭 'MS-ASPNETCORE-WINAUTHTOKEN' 形式轉送至在 %ASPNETCORE_PORT% 進行接聽的子處理序。</span><span class="sxs-lookup"><span data-stu-id="aeab8-440">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="aeab8-441">該處理序需負責依據要求呼叫此權杖上的 CloseHandle。</span><span class="sxs-lookup"><span data-stu-id="aeab8-441">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="aeab8-442">選擇性字串屬性。</span><span class="sxs-lookup"><span data-stu-id="aeab8-442">Optional string attribute.</span></span></p><p><span data-ttu-id="aeab8-443">將裝載模型指定為同進程 (`InProcess` / `inprocess`) 或跨進程的 (`OutOfProcess` / `outofprocess`) 。</span><span class="sxs-lookup"><span data-stu-id="aeab8-443">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `OutOfProcess`<br>`outofprocess` |
| `processesPerApplication` | <p><span data-ttu-id="aeab8-444">選擇性的整數屬性。</span><span class="sxs-lookup"><span data-stu-id="aeab8-444">Optional integer attribute.</span></span></p><p><span data-ttu-id="aeab8-445">指定 `processPath` 每個應用程式可以啟動的設定中指定之進程的實例數目。</span><span class="sxs-lookup"><span data-stu-id="aeab8-445">Specifies the number of instances of the process specified in the `processPath` setting that can be spun up per app.</span></span></p><p><span data-ttu-id="aeab8-446">&dagger;針對同處理序裝載，此值會限制為 `1`。</span><span class="sxs-lookup"><span data-stu-id="aeab8-446">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="aeab8-447">不建議使用 `processesPerApplication` 設定。</span><span class="sxs-lookup"><span data-stu-id="aeab8-447">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="aeab8-448">此屬性將在未來版本中移除。</span><span class="sxs-lookup"><span data-stu-id="aeab8-448">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="aeab8-449">預設： `1`</span><span class="sxs-lookup"><span data-stu-id="aeab8-449">Default: `1`</span></span><br><span data-ttu-id="aeab8-450">最小值：`1`</span><span class="sxs-lookup"><span data-stu-id="aeab8-450">Min: `1`</span></span><br><span data-ttu-id="aeab8-451">最大值：`100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="aeab8-451">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="aeab8-452">必要的字串屬性。</span><span class="sxs-lookup"><span data-stu-id="aeab8-452">Required string attribute.</span></span></p><p><span data-ttu-id="aeab8-453">啟動接聽 HTTP 要求之處理序的可執行檔路徑。</span><span class="sxs-lookup"><span data-stu-id="aeab8-453">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="aeab8-454">支援相對路徑。</span><span class="sxs-lookup"><span data-stu-id="aeab8-454">Relative paths are supported.</span></span> <span data-ttu-id="aeab8-455">如果路徑的開頭為 `.`，該路徑即被視為網站根目錄的相對路徑。</span><span class="sxs-lookup"><span data-stu-id="aeab8-455">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="aeab8-456">選擇性的整數屬性。</span><span class="sxs-lookup"><span data-stu-id="aeab8-456">Optional integer attribute.</span></span></p><p><span data-ttu-id="aeab8-457">指定中指定的進程 `processPath` 每分鐘損毀的次數。</span><span class="sxs-lookup"><span data-stu-id="aeab8-457">Specifies the number of times the process specified in `processPath` is allowed to crash per minute.</span></span> <span data-ttu-id="aeab8-458">如果超出此限制，模組就會在該分鐘的剩餘時間內停止啟動處理序。</span><span class="sxs-lookup"><span data-stu-id="aeab8-458">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="aeab8-459">不支援同處理序裝載。</span><span class="sxs-lookup"><span data-stu-id="aeab8-459">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="aeab8-460">預設： `10`</span><span class="sxs-lookup"><span data-stu-id="aeab8-460">Default: `10`</span></span><br><span data-ttu-id="aeab8-461">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="aeab8-461">Min: `0`</span></span><br><span data-ttu-id="aeab8-462">最大值︰`100`</span><span class="sxs-lookup"><span data-stu-id="aeab8-462">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="aeab8-463">選擇性的時間範圍屬性。</span><span class="sxs-lookup"><span data-stu-id="aeab8-463">Optional timespan attribute.</span></span></p><p><span data-ttu-id="aeab8-464">針對在 %ASPNETCORE_PORT% 進行接聽的處理序，指定 ASP.NET Core 模組等候回應的持續時間。</span><span class="sxs-lookup"><span data-stu-id="aeab8-464">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="aeab8-465">在 ASP.NET Core 2.1 或更新版本隨附的 ASP.NET Core 模組版本中，是以小時、分鐘及秒為單位來指定 `requestTimeout`。</span><span class="sxs-lookup"><span data-stu-id="aeab8-465">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="aeab8-466">不適用於同處理序裝載。</span><span class="sxs-lookup"><span data-stu-id="aeab8-466">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="aeab8-467">針對同處理序裝載，該模組會等待應用程式處理要求。</span><span class="sxs-lookup"><span data-stu-id="aeab8-467">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="aeab8-468">字串之分鐘和秒數的有效值介於 0-59。</span><span class="sxs-lookup"><span data-stu-id="aeab8-468">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="aeab8-469">在分鐘或秒數的值中使用 **60** 將會導致「500 - 內部伺服器錯誤」。</span><span class="sxs-lookup"><span data-stu-id="aeab8-469">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="aeab8-470">預設： `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="aeab8-470">Default: `00:02:00`</span></span><br><span data-ttu-id="aeab8-471">最小值：`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="aeab8-471">Min: `00:00:00`</span></span><br><span data-ttu-id="aeab8-472">最大值︰`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="aeab8-472">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="aeab8-473">選擇性的整數屬性。</span><span class="sxs-lookup"><span data-stu-id="aeab8-473">Optional integer attribute.</span></span></p><p><span data-ttu-id="aeab8-474">當偵測到檔案時，模組等候可執行檔正常關機的持續時間（以秒為單位） `app_offline.htm` 。</span><span class="sxs-lookup"><span data-stu-id="aeab8-474">Duration in seconds that the module waits for the executable to gracefully shutdown when the `app_offline.htm` file is detected.</span></span></p> | <span data-ttu-id="aeab8-475">預設： `10`</span><span class="sxs-lookup"><span data-stu-id="aeab8-475">Default: `10`</span></span><br><span data-ttu-id="aeab8-476">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="aeab8-476">Min: `0`</span></span><br><span data-ttu-id="aeab8-477">最大值︰`600`</span><span class="sxs-lookup"><span data-stu-id="aeab8-477">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="aeab8-478">選擇性的整數屬性。</span><span class="sxs-lookup"><span data-stu-id="aeab8-478">Optional integer attribute.</span></span></p><p><span data-ttu-id="aeab8-479">針對可執行檔啟動在連接埠進行接聽的處理序，模組等候的持續時間 (以秒為單位)。</span><span class="sxs-lookup"><span data-stu-id="aeab8-479">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="aeab8-480">如果超出此時間限制，模組就會終止處理序。</span><span class="sxs-lookup"><span data-stu-id="aeab8-480">If this time limit is exceeded, the module kills the process.</span></span></p><p><span data-ttu-id="aeab8-481">裝載同 *進程* 時： **進程不會重新開機** ， **也不會使用** 此 `rapidFailsPerMinute` 設定。</span><span class="sxs-lookup"><span data-stu-id="aeab8-481">When hosting *in-process*: The process is **not** restarted and does **not** use the `rapidFailsPerMinute` setting.</span></span></p><p><span data-ttu-id="aeab8-482">裝載 *跨進程* 時：此模組會在收到新的要求時嘗試重新開機進程，並在後續的連入要求上繼續嘗試重新開機進程，除非應用程式在最後一次的輪流分鐘內無法啟動次數 `rapidFailsPerMinute` 。</span><span class="sxs-lookup"><span data-stu-id="aeab8-482">When hosting *out-of-process*: The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start `rapidFailsPerMinute` number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="aeab8-483">0 (零) 值 **不會** 視為無限逾時。</span><span class="sxs-lookup"><span data-stu-id="aeab8-483">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="aeab8-484">預設： `120`</span><span class="sxs-lookup"><span data-stu-id="aeab8-484">Default: `120`</span></span><br><span data-ttu-id="aeab8-485">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="aeab8-485">Min: `0`</span></span><br><span data-ttu-id="aeab8-486">最大值︰`3600`</span><span class="sxs-lookup"><span data-stu-id="aeab8-486">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="aeab8-487">選擇性的 Boolean 屬性。</span><span class="sxs-lookup"><span data-stu-id="aeab8-487">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="aeab8-488">若為 true，則中指定之進程的 **stdout** 和 **stderr** `processPath` 會重新導向至 **>stdoutlogfile** 中指定的檔案。</span><span class="sxs-lookup"><span data-stu-id="aeab8-488">If true, **stdout** and **stderr** for the process specified in `processPath` are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="aeab8-489">選擇性字串屬性。</span><span class="sxs-lookup"><span data-stu-id="aeab8-489">Optional string attribute.</span></span></p><p><span data-ttu-id="aeab8-490">指定 `stdout` `stderr` 記錄中所指定進程的相對或絕對檔案路徑 `processPath` 。</span><span class="sxs-lookup"><span data-stu-id="aeab8-490">Specifies the relative or absolute file path for which `stdout` and `stderr` from the process specified in `processPath` are logged.</span></span> <span data-ttu-id="aeab8-491">相對路徑是相對於網站的根目錄。</span><span class="sxs-lookup"><span data-stu-id="aeab8-491">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="aeab8-492">所有開頭為 `.` 的路徑都是網站根目錄的相對路徑，而所有其他路徑則視為絕對路徑。</span><span class="sxs-lookup"><span data-stu-id="aeab8-492">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="aeab8-493">建立記錄檔後，模組會建立路徑中提供的所有資料夾。</span><span class="sxs-lookup"><span data-stu-id="aeab8-493">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="aeab8-494">使用底線分隔符號，會將時間戳記、處理序識別碼和副檔名 (`.log`) 新增至路徑的最後一個區段 `stdoutLogFile` 。</span><span class="sxs-lookup"><span data-stu-id="aeab8-494">Using underscore delimiters, a timestamp, process ID, and file extension (`.log`) are added to the last segment of the `stdoutLogFile` path.</span></span> <span data-ttu-id="aeab8-495">如果 `.\logs\stdout` 以值的形式提供，就會將 stdout 記錄檔的範例儲存在 `stdout_20180205194132_1934.log` `logs` 2/5/2018 （19:41:32），並在處理序識別碼為1934時儲存在資料夾中。</span><span class="sxs-lookup"><span data-stu-id="aeab8-495">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as `stdout_20180205194132_1934.log` in the `logs` folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="aeab8-496">設定環境變數</span><span class="sxs-lookup"><span data-stu-id="aeab8-496">Setting environment variables</span></span>

<span data-ttu-id="aeab8-497">您可以在 `processPath` 屬性中為處理序指定環境變數。</span><span class="sxs-lookup"><span data-stu-id="aeab8-497">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="aeab8-498">請使用 `<environmentVariables>` 集合元素的 `<environmentVariable>` 子元素來指定環境變數。</span><span class="sxs-lookup"><span data-stu-id="aeab8-498">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="aeab8-499">本節中所設定環境變數的優先順序會高於系統環境變數。</span><span class="sxs-lookup"><span data-stu-id="aeab8-499">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="aeab8-500">下列範例會設定兩個環境變數。</span><span class="sxs-lookup"><span data-stu-id="aeab8-500">The following example sets two environment variables.</span></span> <span data-ttu-id="aeab8-501">`ASPNETCORE_ENVIRONMENT` 會將應用程式的環境設定為 `Development`。</span><span class="sxs-lookup"><span data-stu-id="aeab8-501">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="aeab8-502">開發人員可以在檔案中暫時設定此值 `web.config` ，以在偵錯工具例外狀況時強制載入 [開發人員例外狀況頁面](xref:fundamentals/error-handling) 。</span><span class="sxs-lookup"><span data-stu-id="aeab8-502">A developer may temporarily set this value in the `web.config` file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="aeab8-503">`CONFIG_DIR` 是一個使用者定義的環境變數範例，其中開發人員已撰寫程式碼，會在啟動時讀取值來構成用以載入應用程式設定檔的路徑。</span><span class="sxs-lookup"><span data-stu-id="aeab8-503">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> <span data-ttu-id="aeab8-504">直接在中設定環境的替代方式， `web.config` 就是在 `<EnvironmentName>` [發行設定檔 ( .pubxml) ](xref:host-and-deploy/visual-studio-publish-profiles) 或專案檔中包含屬性。</span><span class="sxs-lookup"><span data-stu-id="aeab8-504">An alternative to setting the environment directly in `web.config` is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="aeab8-505">這種方法 `web.config` 會設定發行專案時的環境：</span><span class="sxs-lookup"><span data-stu-id="aeab8-505">This approach sets the environment in `web.config` when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="aeab8-506">請只有在未受信任網路 (例如網際網路) 無法存取的暫存和測試伺服器上，才將 `ASPNETCORE_ENVIRONMENT` 環境變數設定為 `Development`。</span><span class="sxs-lookup"><span data-stu-id="aeab8-506">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="aeab8-507">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="aeab8-507">app_offline.htm</span></span>

<span data-ttu-id="aeab8-508">如果 `app_offline.htm` 在應用程式的根目錄中偵測到具有名稱的檔案，ASP.NET Core 模組會嘗試以正常方式關閉應用程式，並停止處理傳入的要求。</span><span class="sxs-lookup"><span data-stu-id="aeab8-508">If a file with the name `app_offline.htm` is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="aeab8-509">如果在經過 `shutdownTimeLimit` 中所定義的秒數之後，應用程式仍然在執行，ASP.NET Core Module 就會終止執行中的處理序。</span><span class="sxs-lookup"><span data-stu-id="aeab8-509">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="aeab8-510">當檔案 `app_offline.htm` 存在時，ASP.NET Core 模組會藉由傳回檔案的內容來回應要求 `app_offline.htm` 。</span><span class="sxs-lookup"><span data-stu-id="aeab8-510">While the `app_offline.htm` file is present, the ASP.NET Core Module responds to requests by sending back the contents of the `app_offline.htm` file.</span></span> <span data-ttu-id="aeab8-511">移除檔案時 `app_offline.htm` ，下一個要求會啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="aeab8-511">When the `app_offline.htm` file is removed, the next request starts the app.</span></span>

<span data-ttu-id="aeab8-512">使用跨處理序裝載模型時，若未開啟連線，應用程式可能無法立即關閉。</span><span class="sxs-lookup"><span data-stu-id="aeab8-512">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="aeab8-513">例如，WebSocket 連線可能會延遲應用程式關閉。</span><span class="sxs-lookup"><span data-stu-id="aeab8-513">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="aeab8-514">啟動錯誤頁面</span><span class="sxs-lookup"><span data-stu-id="aeab8-514">Start-up error page</span></span>

<span data-ttu-id="aeab8-515">同處理序及跨處理序裝載無法啟動應用程式時，兩者皆會產生自訂錯誤頁面。</span><span class="sxs-lookup"><span data-stu-id="aeab8-515">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="aeab8-516">若 ASP.NET Core 模組找不到同處理序或跨處理序要求處理常式時，就會顯示 [500.0 - 同處理序/跨處理序處理常式載入失敗] 狀態碼頁面。</span><span class="sxs-lookup"><span data-stu-id="aeab8-516">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="aeab8-517">對於同處理序裝載，若 ASP.NET Core 模組無法啟動應用程式，就會顯示 [500.30 - 啟動失敗] 狀態碼頁面。</span><span class="sxs-lookup"><span data-stu-id="aeab8-517">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="aeab8-518">對於跨處理序裝載，若 ASP.NET Core 模組無法啟動後端處理序，或後端處理序啟動但無法在所設定的連接埠上進行接聽，就會顯示 [502.5 - 處理序失敗] 狀態碼頁面。</span><span class="sxs-lookup"><span data-stu-id="aeab8-518">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="aeab8-519">若要避免此頁面產生並還原至預設的 IIS 5xx 狀態碼頁面，請使用 `disableStartUpErrorPage` 屬性。</span><span class="sxs-lookup"><span data-stu-id="aeab8-519">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="aeab8-520">如需有關設定自訂錯誤訊息的詳細資訊，請參閱 [HTTP 錯誤\<httpErrors>](/iis/configuration/system.webServer/httpErrors/)。</span><span class="sxs-lookup"><span data-stu-id="aeab8-520">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="aeab8-521">記錄檔建立和重新導向</span><span class="sxs-lookup"><span data-stu-id="aeab8-521">Log creation and redirection</span></span>

<span data-ttu-id="aeab8-522">如果已設定 `aspNetCore` 元素的 `stdoutLogEnabled` 和 `stdoutLogFile` 屬性，ASP.NET Core 模組就會將 stdout 和 stderr 主控台輸出重新導向到磁碟。</span><span class="sxs-lookup"><span data-stu-id="aeab8-522">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="aeab8-523">建立記錄檔後，模組會建立 `stdoutLogFile` 路徑中的所有資料夾。</span><span class="sxs-lookup"><span data-stu-id="aeab8-523">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="aeab8-524">應用程式集區必須具有寫入記錄檔之位置的寫入權限 (用 `IIS AppPool\{APP POOL NAME}` 來提供寫入權限，其中預留位置 `{APP POOL NAME}` 是) 的應用程式集區名稱。</span><span class="sxs-lookup"><span data-stu-id="aeab8-524">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}` to provide write permission, where the placeholder `{APP POOL NAME}` is the app pool name).</span></span>

<span data-ttu-id="aeab8-525">除非發生處理序回收/重新啟動，否則不會輪替記錄檔。</span><span class="sxs-lookup"><span data-stu-id="aeab8-525">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="aeab8-526">主機服務提供者必須負責限制記錄檔所使用的磁碟空間。</span><span class="sxs-lookup"><span data-stu-id="aeab8-526">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="aeab8-527">只有在 IIS 上裝載時，或使用 [VISUAL STUDIO iis 的開發階段支援來](xref:host-and-deploy/iis/development-time-iis-support)針對應用程式啟動問題進行疑難排解時，才建議使用 stdout 記錄檔，而不是在本機進行偵錯工具並使用 IIS Express 執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="aeab8-527">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="aeab8-528">請勿將 stdout 記錄檔用來進行一般應用程式記錄。</span><span class="sxs-lookup"><span data-stu-id="aeab8-528">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="aeab8-529">針對 ASP.NET Core 應用程式中的例行性記錄，請使用會限制記錄檔大小並輪替記錄檔的記錄程式庫。</span><span class="sxs-lookup"><span data-stu-id="aeab8-529">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="aeab8-530">如需詳細資訊，請參閱[協力廠商記錄提供者](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="aeab8-530">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="aeab8-531">建立記錄檔時，系統會自動新增時間戳記和副檔名。</span><span class="sxs-lookup"><span data-stu-id="aeab8-531">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="aeab8-532">記錄檔名稱的組成方式是將時間戳記、處理序識別碼和副檔名 () 附加 `.log` 至路徑的最後一個區段 `stdoutLogFile` (通常 `stdout`) 以底線分隔。</span><span class="sxs-lookup"><span data-stu-id="aeab8-532">The log file name is composed by appending the timestamp, process ID, and file extension (`.log`) to the last segment of the `stdoutLogFile` path (typically `stdout`) delimited by underscores.</span></span> <span data-ttu-id="aeab8-533">如果 `stdoutLogFile` 路徑以結尾 `stdout` ，則在2/5/2018 （19:42:32）上建立的應用1934程式記錄檔名稱會是 `stdout_20180205194132_1934.log` 。</span><span class="sxs-lookup"><span data-stu-id="aeab8-533">If the `stdoutLogFile` path ends with `stdout`, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name `stdout_20180205194132_1934.log`.</span></span>

<span data-ttu-id="aeab8-534">若 `stdoutLogEnabled` 為 false，會擷取在應用程式啟動時發生的錯誤，並發出最大 30KB 的事件記錄檔。</span><span class="sxs-lookup"><span data-stu-id="aeab8-534">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="aeab8-535">啟動之後，就會捨棄其他的記錄檔。</span><span class="sxs-lookup"><span data-stu-id="aeab8-535">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="aeab8-536">下列範例專案會 `aspNetCore` 在相對路徑設定 stdout 記錄 `.\log\` 。</span><span class="sxs-lookup"><span data-stu-id="aeab8-536">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="aeab8-537">確認應用程式集區使用者身分識別有權寫入提供的路徑。</span><span class="sxs-lookup"><span data-stu-id="aeab8-537">Confirm that the app pool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="aeab8-538">針對 Azure App Service 部署發行應用程式時，Web SDK 會將 `stdoutLogFile` 值設定為 `\\?\%home%\LogFiles\stdout` 。</span><span class="sxs-lookup"><span data-stu-id="aeab8-538">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="aeab8-539">`%home`環境變數是針對 Azure App Service 所裝載的應用程式預先定義。</span><span class="sxs-lookup"><span data-stu-id="aeab8-539">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="aeab8-540">如需路徑格式的詳細資訊，請參閱 [Windows 系統上的檔案路徑格式](/dotnet/standard/io/file-path-formats)。</span><span class="sxs-lookup"><span data-stu-id="aeab8-540">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="aeab8-541">增強型診斷記錄</span><span class="sxs-lookup"><span data-stu-id="aeab8-541">Enhanced diagnostic logs</span></span>

<span data-ttu-id="aeab8-542">ASP.NET Core 模組是可設定的，以提供增強型診斷記錄。</span><span class="sxs-lookup"><span data-stu-id="aeab8-542">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="aeab8-543">將專案加入 `<handlerSettings>` 至 `<aspNetCore>` 中的元素 `web.config` 。</span><span class="sxs-lookup"><span data-stu-id="aeab8-543">Add the `<handlerSettings>` element to the `<aspNetCore>` element in `web.config`.</span></span> <span data-ttu-id="aeab8-544">將 `debugLevel` 設定為 `TRACE` 會公開精確性更高的診斷資訊：</span><span class="sxs-lookup"><span data-stu-id="aeab8-544">Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="aeab8-545">在先前範例中提供給值 (的路徑中的資料夾 `<handlerSetting>` `logs` ，) 不會自動由模組建立，且應該在部署中預先存在。</span><span class="sxs-lookup"><span data-stu-id="aeab8-545">Folders in the path provided to the `<handlerSetting>` value (`logs` in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="aeab8-546">應用程式集區必須具有寫入記錄檔之位置的寫入權限 (用 `IIS AppPool\{APP POOL NAME}` 來提供寫入權限，其中預留位置 `{APP POOL NAME}` 是) 的應用程式集區名稱。</span><span class="sxs-lookup"><span data-stu-id="aeab8-546">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}` to provide write permission, where the placeholder `{APP POOL NAME}` is the app pool name).</span></span>

<span data-ttu-id="aeab8-547">偵錯層級 (`debugLevel`) 值可以同時包含層級和位置。</span><span class="sxs-lookup"><span data-stu-id="aeab8-547">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="aeab8-548">層級 (順序從最不詳細到最詳細)：</span><span class="sxs-lookup"><span data-stu-id="aeab8-548">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="aeab8-549">ERROR</span><span class="sxs-lookup"><span data-stu-id="aeab8-549">ERROR</span></span>
* <span data-ttu-id="aeab8-550">WARNING</span><span class="sxs-lookup"><span data-stu-id="aeab8-550">WARNING</span></span>
* <span data-ttu-id="aeab8-551">INFO</span><span class="sxs-lookup"><span data-stu-id="aeab8-551">INFO</span></span>
* <span data-ttu-id="aeab8-552">TRACE</span><span class="sxs-lookup"><span data-stu-id="aeab8-552">TRACE</span></span>

<span data-ttu-id="aeab8-553">位置 (允許多個位置)：</span><span class="sxs-lookup"><span data-stu-id="aeab8-553">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="aeab8-554">主控台</span><span class="sxs-lookup"><span data-stu-id="aeab8-554">CONSOLE</span></span>
* <span data-ttu-id="aeab8-555">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="aeab8-555">EVENTLOG</span></span>
* <span data-ttu-id="aeab8-556">FILE</span><span class="sxs-lookup"><span data-stu-id="aeab8-556">FILE</span></span>

<span data-ttu-id="aeab8-557">也可以透過環境變數提供處理常式設定：</span><span class="sxs-lookup"><span data-stu-id="aeab8-557">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="aeab8-558">`ASPNETCORE_MODULE_DEBUG_FILE`： Debug 記錄檔的路徑。</span><span class="sxs-lookup"><span data-stu-id="aeab8-558">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="aeab8-559"> (預設： `aspnetcore-debug.log`) </span><span class="sxs-lookup"><span data-stu-id="aeab8-559">(Default: `aspnetcore-debug.log`)</span></span>
* <span data-ttu-id="aeab8-560">`ASPNETCORE_MODULE_DEBUG`： Debug 層級設定。</span><span class="sxs-lookup"><span data-stu-id="aeab8-560">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="aeab8-561">在部署中保持啟用偵錯記錄的時間，**不要** 超過針對問題進行排解疑難所需的時間。</span><span class="sxs-lookup"><span data-stu-id="aeab8-561">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="aeab8-562">記錄的大小不受限制。</span><span class="sxs-lookup"><span data-stu-id="aeab8-562">The size of the log isn't limited.</span></span> <span data-ttu-id="aeab8-563">保持啟用偵錯記錄可能會耗盡可用磁碟空間，並讓伺服器或應用程式服務當機。</span><span class="sxs-lookup"><span data-stu-id="aeab8-563">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="aeab8-564">如需檔案中專案的範例，請參閱 [web.config](#configuration-with-webconfig) 的設定 `aspNetCore` `web.config` 。</span><span class="sxs-lookup"><span data-stu-id="aeab8-564">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the `web.config` file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="aeab8-565">Proxy 組態使用 HTTP 通訊協定和配對權杖</span><span class="sxs-lookup"><span data-stu-id="aeab8-565">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="aeab8-566">僅適用於跨處理序裝載。</span><span class="sxs-lookup"><span data-stu-id="aeab8-566">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="aeab8-567">在 ASP.NET Core 模組與 Kestrel 之間建立的 Proxy 會使用 HTTP 通訊協定。</span><span class="sxs-lookup"><span data-stu-id="aeab8-567">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="aeab8-568">沒有從伺服器外的位置竊聽模組與 Kestrel 之間流量的風險。</span><span class="sxs-lookup"><span data-stu-id="aeab8-568">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="aeab8-569">配對權杖用來保證 Kestrel 所接收的要求已由 IIS 代理，而且不是來自其他來源。</span><span class="sxs-lookup"><span data-stu-id="aeab8-569">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="aeab8-570">模組會建立配對權杖，並將其設定成環境變數 (`ASPNETCORE_TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="aeab8-570">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="aeab8-571">配對權杖也會設定成每個代理要求的標頭 (`MS-ASPNETCORE-TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="aeab8-571">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="aeab8-572">IIS 中介軟體會檢查其收到的每個要求，以確認配對權杖的標頭值符合環境變數值。</span><span class="sxs-lookup"><span data-stu-id="aeab8-572">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="aeab8-573">如果權杖值不相符，將記錄並拒絕要求。</span><span class="sxs-lookup"><span data-stu-id="aeab8-573">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="aeab8-574">使用者無法從伺服器外的位置存取配對權杖環境變數，以及模組與 Kestrel 之間的流量。</span><span class="sxs-lookup"><span data-stu-id="aeab8-574">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="aeab8-575">在不知道配對權杖值的情況下，攻擊者無法略過 IIS 中介軟體的檢查送出要求。</span><span class="sxs-lookup"><span data-stu-id="aeab8-575">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="aeab8-576">具有 IIS 共用設定的 ASP.NET Core 模組</span><span class="sxs-lookup"><span data-stu-id="aeab8-576">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="aeab8-577">ASP.NET Core 模組安裝程式會以帳戶的許可權執行 `TrustedInstaller` 。</span><span class="sxs-lookup"><span data-stu-id="aeab8-577">The ASP.NET Core Module installer runs with the privileges of the `TrustedInstaller` account.</span></span> <span data-ttu-id="aeab8-578">由於本機系統帳戶對於 IIS 共用設定所使用的共用路徑沒有 modify 許可權，因此，安裝程式在嘗試于共用上的檔案中設定模組設定時，會擲回「拒絕存取」錯誤 `applicationHost.config` 。</span><span class="sxs-lookup"><span data-stu-id="aeab8-578">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the `applicationHost.config` file on the share.</span></span>

<span data-ttu-id="aeab8-579">在與 IIS 安裝相同的電腦上使用 IIS 共用設定時，請執行 ASP.NET Core 裝載套件組合安裝程式並將 `OPT_NO_SHARED_CONFIG_CHECK` 參數設為 `1`：</span><span class="sxs-lookup"><span data-stu-id="aeab8-579">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="aeab8-580">若共用設定的路徑位於與 IIS 安裝不同的電腦上，請遵循下列步驟：</span><span class="sxs-lookup"><span data-stu-id="aeab8-580">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="aeab8-581">停用「IIS 共用設定」。</span><span class="sxs-lookup"><span data-stu-id="aeab8-581">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="aeab8-582">執行安裝程式。</span><span class="sxs-lookup"><span data-stu-id="aeab8-582">Run the installer.</span></span>
1. <span data-ttu-id="aeab8-583">將更新的檔案匯出 `applicationHost.config` 至共用。</span><span class="sxs-lookup"><span data-stu-id="aeab8-583">Export the updated `applicationHost.config` file to the share.</span></span>
1. <span data-ttu-id="aeab8-584">重新啟用「IIS 共用設定」。</span><span class="sxs-lookup"><span data-stu-id="aeab8-584">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="aeab8-585">模組版本和裝載套件組合安裝程式記錄檔</span><span class="sxs-lookup"><span data-stu-id="aeab8-585">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="aeab8-586">判斷已安裝的 ASP.NET Core 模組版本：</span><span class="sxs-lookup"><span data-stu-id="aeab8-586">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="aeab8-587">在主控系統上，流覽至 `%windir%\System32\inetsrv` 。</span><span class="sxs-lookup"><span data-stu-id="aeab8-587">On the hosting system, navigate to `%windir%\System32\inetsrv`.</span></span>
1. <span data-ttu-id="aeab8-588">找出檔案 `aspnetcore.dll` 。</span><span class="sxs-lookup"><span data-stu-id="aeab8-588">Locate the `aspnetcore.dll` file.</span></span>
1. <span data-ttu-id="aeab8-589">在該檔案上按一下滑鼠右鍵，然後從關聯式功能表中選取 [內容]。</span><span class="sxs-lookup"><span data-stu-id="aeab8-589">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="aeab8-590">選取 [ **詳細資料** ] 索引標籤。檔案 **版本** 和 **產品版本** 代表已安裝的模組版本。</span><span class="sxs-lookup"><span data-stu-id="aeab8-590">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="aeab8-591">模組的裝載套件組合安裝程式記錄檔位於 `C:\\Users\\%UserName%\\AppData\\Local\\Temp` 。</span><span class="sxs-lookup"><span data-stu-id="aeab8-591">The Hosting Bundle installer logs for the module are found at `C:\\Users\\%UserName%\\AppData\\Local\\Temp`.</span></span> <span data-ttu-id="aeab8-592">檔案的名稱 `dd_DotNetCoreWinSvrHosting__\{TIMESTAMP}_000_AspNetCoreModule_x64.log` ，其中預留位置 `{TIMESTAMP}` 是時間戳記。</span><span class="sxs-lookup"><span data-stu-id="aeab8-592">The file is named `dd_DotNetCoreWinSvrHosting__\{TIMESTAMP}_000_AspNetCoreModule_x64.log`, where the placeholder `{TIMESTAMP}` is the timestamp.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="aeab8-593">模組、結構描述及設定檔位置</span><span class="sxs-lookup"><span data-stu-id="aeab8-593">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="aeab8-594">模組</span><span class="sxs-lookup"><span data-stu-id="aeab8-594">Module</span></span>

<span data-ttu-id="aeab8-595">**IIS (x86/amd64)**：</span><span class="sxs-lookup"><span data-stu-id="aeab8-595">**IIS (x86/amd64)**:</span></span>

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

<span data-ttu-id="aeab8-596">**IIS Express (x86/amd64)**：</span><span class="sxs-lookup"><span data-stu-id="aeab8-596">**IIS Express (x86/amd64)**:</span></span>

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a><span data-ttu-id="aeab8-597">結構描述</span><span class="sxs-lookup"><span data-stu-id="aeab8-597">Schema</span></span>

<span data-ttu-id="aeab8-598">**IIS**</span><span class="sxs-lookup"><span data-stu-id="aeab8-598">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

<span data-ttu-id="aeab8-599">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="aeab8-599">**IIS Express**</span></span>

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a><span data-ttu-id="aeab8-600">組態</span><span class="sxs-lookup"><span data-stu-id="aeab8-600">Configuration</span></span>

<span data-ttu-id="aeab8-601">**IIS**</span><span class="sxs-lookup"><span data-stu-id="aeab8-601">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\applicationHost.config`

<span data-ttu-id="aeab8-602">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="aeab8-602">**IIS Express**</span></span>

* <span data-ttu-id="aeab8-603">Visual Studio： `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span><span class="sxs-lookup"><span data-stu-id="aeab8-603">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span></span>

* <span data-ttu-id="aeab8-604">*iisexpress.exe* Cli： `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span><span class="sxs-lookup"><span data-stu-id="aeab8-604">*iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span></span>

<span data-ttu-id="aeab8-605">您可以藉由在檔案中搜尋來找到檔案 `aspnetcore` `applicationHost.config` 。</span><span class="sxs-lookup"><span data-stu-id="aeab8-605">The files can be found by searching for `aspnetcore` in the `applicationHost.config` file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="aeab8-606">ASP.NET Core 模組是一種原生 IIS 模組，可外掛至 IIS 管線，將 Web 要求重新轉送到後端的 ASP.NET Core 應用程式。</span><span class="sxs-lookup"><span data-stu-id="aeab8-606">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="aeab8-607">支援的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="aeab8-607">Supported Windows versions:</span></span>

* <span data-ttu-id="aeab8-608">Windows 7 或更新版本</span><span class="sxs-lookup"><span data-stu-id="aeab8-608">Windows 7 or later</span></span>
* <span data-ttu-id="aeab8-609">Windows Server 2008 R2 或更新版本</span><span class="sxs-lookup"><span data-stu-id="aeab8-609">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="aeab8-610">該模組只適用於 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="aeab8-610">The module only works with Kestrel.</span></span> <span data-ttu-id="aeab8-611">該模組與 [HTTP.sys](xref:fundamentals/servers/httpsys) 不相容。</span><span class="sxs-lookup"><span data-stu-id="aeab8-611">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="aeab8-612">因為處理序中執行的 ASP.NET Core 應用程式會與 IIS 背景工作處理序分開，所以此模組也會執行處理序管理。</span><span class="sxs-lookup"><span data-stu-id="aeab8-612">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="aeab8-613">此模組會在第一個要求到達時啟動 ASP.NET Core 應用程式的處理序，並在應用程式損毀時將它重新啟動。</span><span class="sxs-lookup"><span data-stu-id="aeab8-613">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="aeab8-614">此行為基本上與在 IIS 中執行同處理序，並由 [Windows 處理器啟用服務 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 所管理的 ASP.NET 4.x 應用程式相同。</span><span class="sxs-lookup"><span data-stu-id="aeab8-614">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="aeab8-615">下圖說明 IIS、ASP.NET Core 模組和應用程式之間的關聯性：</span><span class="sxs-lookup"><span data-stu-id="aeab8-615">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![ASP.NET Core 模組](iis/index/_static/ancm-outofprocess.png)

<span data-ttu-id="aeab8-617">要求會從 Web 到達核心模式的 HTTP.sys 驅動程式。</span><span class="sxs-lookup"><span data-stu-id="aeab8-617">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="aeab8-618">驅動程式會在網站設定的通訊埠上將要求路由至 IIS，此通訊埠通常是 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="aeab8-618">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="aeab8-619">此模組會在應用程式的隨機通訊埠上將要求轉送至 Kestrel，而且不會是通訊埠 80 或 443。</span><span class="sxs-lookup"><span data-stu-id="aeab8-619">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="aeab8-620">模組會在啟動時透過環境變數指定埠，而 [IIS 整合中介軟體](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) 會設定要接聽的伺服器 `http://localhost:{port}` 。</span><span class="sxs-lookup"><span data-stu-id="aeab8-620">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="aeab8-621">將會執行額外檢查，不是源自模組的要求都會遭到拒絕。</span><span class="sxs-lookup"><span data-stu-id="aeab8-621">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="aeab8-622">此模組不支援 HTTPS 轉送，因此即使由 IIS 透過 HTTPS 接收，要求還是會透過 HTTP 轉送。</span><span class="sxs-lookup"><span data-stu-id="aeab8-622">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="aeab8-623">Kestrel 收取來自模組的要求之後，要求會被推送至 ASP.NET Core 中介軟體管線。</span><span class="sxs-lookup"><span data-stu-id="aeab8-623">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="aeab8-624">中介軟體管線會處理要求，並將其作為 `HttpContext` 執行個體傳遞至應用程式的邏輯。</span><span class="sxs-lookup"><span data-stu-id="aeab8-624">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="aeab8-625">IIS Integration 新增的中介軟體會更新配置、遠端 IP 和帳戶路徑基底，以將要求轉送至 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="aeab8-625">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="aeab8-626">應用程式的回應會傳回 IIS，而 IIS 會將其推送回起始要求的 HTTP 用戶端。</span><span class="sxs-lookup"><span data-stu-id="aeab8-626">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="aeab8-627">許多如 Windows 驗證等原生模組仍在使用中。</span><span class="sxs-lookup"><span data-stu-id="aeab8-627">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="aeab8-628">若要深入了解搭配 ASP.NET Core 模組的使用中 IIS 模組，請參閱<xref:host-and-deploy/iis/modules>。</span><span class="sxs-lookup"><span data-stu-id="aeab8-628">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="aeab8-629">ASP.NET Core 模組也可以：</span><span class="sxs-lookup"><span data-stu-id="aeab8-629">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="aeab8-630">設定背景工作處理序的環境變數。</span><span class="sxs-lookup"><span data-stu-id="aeab8-630">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="aeab8-631">將 stdout 輸出記錄到檔案儲存區，以針對啟動問題進行疑難排解。</span><span class="sxs-lookup"><span data-stu-id="aeab8-631">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="aeab8-632">轉送 Windows 驗證權杖。</span><span class="sxs-lookup"><span data-stu-id="aeab8-632">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="aeab8-633">如何安裝和使用 ASP.NET Core 模組</span><span class="sxs-lookup"><span data-stu-id="aeab8-633">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="aeab8-634">如需如何安裝 ASP.NET Core 模組的指示，請參閱[安裝 .NET Core 裝載套件組合](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="aeab8-634">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="aeab8-635">使用 web.config 進行設定</span><span class="sxs-lookup"><span data-stu-id="aeab8-635">Configuration with web.config</span></span>

<span data-ttu-id="aeab8-636">設定 ASP.NET Core 模組時，是使用網站 *web.config* 檔案中 `system.webServer` 節點的 `aspNetCore` 區段來設定。</span><span class="sxs-lookup"><span data-stu-id="aeab8-636">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="aeab8-637">以下 *web.config* 檔案是針對 [架構相依部署](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)發佈的檔案，會設定 ASP.NET Core 模組來處理網站要求：</span><span class="sxs-lookup"><span data-stu-id="aeab8-637">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath="dotnet"
                arguments=".\MyApp.dll"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="aeab8-638">以下 *web.config* 是針對 [自封式部署](/dotnet/articles/core/deploying/#self-contained-deployments-scd)發佈的檔案：</span><span class="sxs-lookup"><span data-stu-id="aeab8-638">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath=".\MyApp.exe"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="aeab8-639">將應用程式部署至 [Azure App Service](https://azure.microsoft.com/services/app-service/) 時，`stdoutLogFile` 路徑會設定為 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="aeab8-639">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="aeab8-640">此路徑會將 stdout 記錄檔儲存至 [LogFiles] 資料夾，這是服務自動建立的位置。</span><span class="sxs-lookup"><span data-stu-id="aeab8-640">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="aeab8-641">如需有關 IIS 子應用程式設定的詳細資訊，請參閱 <xref:host-and-deploy/iis/index#sub-applications>。</span><span class="sxs-lookup"><span data-stu-id="aeab8-641">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="aeab8-642">aspNetCore 元素的屬性</span><span class="sxs-lookup"><span data-stu-id="aeab8-642">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="aeab8-643">屬性</span><span class="sxs-lookup"><span data-stu-id="aeab8-643">Attribute</span></span> | <span data-ttu-id="aeab8-644">描述</span><span class="sxs-lookup"><span data-stu-id="aeab8-644">Description</span></span> | <span data-ttu-id="aeab8-645">預設</span><span class="sxs-lookup"><span data-stu-id="aeab8-645">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="aeab8-646">選擇性字串屬性。</span><span class="sxs-lookup"><span data-stu-id="aeab8-646">Optional string attribute.</span></span></p><p><span data-ttu-id="aeab8-647">**processPath** 中所指定可執行檔的引數。</span><span class="sxs-lookup"><span data-stu-id="aeab8-647">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="aeab8-648">選擇性的 Boolean 屬性。</span><span class="sxs-lookup"><span data-stu-id="aeab8-648">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="aeab8-649">如果為 true，就會抑制 [502.5 - 處理序失敗] 頁面，而優先顯示 *web.config* 中設定的 502 狀態碼頁面。</span><span class="sxs-lookup"><span data-stu-id="aeab8-649">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="aeab8-650">選擇性的 Boolean 屬性。</span><span class="sxs-lookup"><span data-stu-id="aeab8-650">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="aeab8-651">如果為 true，就會依據要求將權杖以標頭 'MS-ASPNETCORE-WINAUTHTOKEN' 形式轉送至在 %ASPNETCORE_PORT% 進行接聽的子處理序。</span><span class="sxs-lookup"><span data-stu-id="aeab8-651">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="aeab8-652">該處理序需負責依據要求呼叫此權杖上的 CloseHandle。</span><span class="sxs-lookup"><span data-stu-id="aeab8-652">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="aeab8-653">選擇性的整數屬性。</span><span class="sxs-lookup"><span data-stu-id="aeab8-653">Optional integer attribute.</span></span></p><p><span data-ttu-id="aeab8-654">指定 **processPath** 設定中所指定處理序執行個體每個應用程式可上調的數目。</span><span class="sxs-lookup"><span data-stu-id="aeab8-654">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="aeab8-655">不建議使用 `processesPerApplication` 設定。</span><span class="sxs-lookup"><span data-stu-id="aeab8-655">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="aeab8-656">此屬性將在未來版本中移除。</span><span class="sxs-lookup"><span data-stu-id="aeab8-656">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="aeab8-657">預設： `1`</span><span class="sxs-lookup"><span data-stu-id="aeab8-657">Default: `1`</span></span><br><span data-ttu-id="aeab8-658">最小值：`1`</span><span class="sxs-lookup"><span data-stu-id="aeab8-658">Min: `1`</span></span><br><span data-ttu-id="aeab8-659">最大值︰`100`</span><span class="sxs-lookup"><span data-stu-id="aeab8-659">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="aeab8-660">必要的字串屬性。</span><span class="sxs-lookup"><span data-stu-id="aeab8-660">Required string attribute.</span></span></p><p><span data-ttu-id="aeab8-661">啟動接聽 HTTP 要求之處理序的可執行檔路徑。</span><span class="sxs-lookup"><span data-stu-id="aeab8-661">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="aeab8-662">支援相對路徑。</span><span class="sxs-lookup"><span data-stu-id="aeab8-662">Relative paths are supported.</span></span> <span data-ttu-id="aeab8-663">如果路徑的開頭為 `.`，該路徑即被視為網站根目錄的相對路徑。</span><span class="sxs-lookup"><span data-stu-id="aeab8-663">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="aeab8-664">選擇性的整數屬性。</span><span class="sxs-lookup"><span data-stu-id="aeab8-664">Optional integer attribute.</span></span></p><p><span data-ttu-id="aeab8-665">指定允許 **processPath** 中所指定處理序每分鐘當機的次數。</span><span class="sxs-lookup"><span data-stu-id="aeab8-665">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="aeab8-666">如果超出此限制，模組就會在該分鐘的剩餘時間內停止啟動處理序。</span><span class="sxs-lookup"><span data-stu-id="aeab8-666">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="aeab8-667">預設： `10`</span><span class="sxs-lookup"><span data-stu-id="aeab8-667">Default: `10`</span></span><br><span data-ttu-id="aeab8-668">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="aeab8-668">Min: `0`</span></span><br><span data-ttu-id="aeab8-669">最大值︰`100`</span><span class="sxs-lookup"><span data-stu-id="aeab8-669">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="aeab8-670">選擇性的時間範圍屬性。</span><span class="sxs-lookup"><span data-stu-id="aeab8-670">Optional timespan attribute.</span></span></p><p><span data-ttu-id="aeab8-671">針對在 %ASPNETCORE_PORT% 進行接聽的處理序，指定 ASP.NET Core 模組等候回應的持續時間。</span><span class="sxs-lookup"><span data-stu-id="aeab8-671">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="aeab8-672">在 ASP.NET Core 2.1 或更新版本隨附的 ASP.NET Core 模組版本中，是以小時、分鐘及秒為單位來指定 `requestTimeout`。</span><span class="sxs-lookup"><span data-stu-id="aeab8-672">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="aeab8-673">預設： `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="aeab8-673">Default: `00:02:00`</span></span><br><span data-ttu-id="aeab8-674">最小值：`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="aeab8-674">Min: `00:00:00`</span></span><br><span data-ttu-id="aeab8-675">最大值︰`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="aeab8-675">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="aeab8-676">選擇性的整數屬性。</span><span class="sxs-lookup"><span data-stu-id="aeab8-676">Optional integer attribute.</span></span></p><p><span data-ttu-id="aeab8-677">當偵測到 *app_offline.htm* 檔案時，模組等候可執行檔正常關閉的持續時間 (以秒為單位)。</span><span class="sxs-lookup"><span data-stu-id="aeab8-677">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="aeab8-678">預設： `10`</span><span class="sxs-lookup"><span data-stu-id="aeab8-678">Default: `10`</span></span><br><span data-ttu-id="aeab8-679">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="aeab8-679">Min: `0`</span></span><br><span data-ttu-id="aeab8-680">最大值︰`600`</span><span class="sxs-lookup"><span data-stu-id="aeab8-680">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="aeab8-681">選擇性的整數屬性。</span><span class="sxs-lookup"><span data-stu-id="aeab8-681">Optional integer attribute.</span></span></p><p><span data-ttu-id="aeab8-682">針對可執行檔啟動在連接埠進行接聽的處理序，模組等候的持續時間 (以秒為單位)。</span><span class="sxs-lookup"><span data-stu-id="aeab8-682">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="aeab8-683">如果超出此時間限制，模組就會終止處理序。</span><span class="sxs-lookup"><span data-stu-id="aeab8-683">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="aeab8-684">模組會在收到新要求時，嘗試重新啟動處理序，然後在後續的連入要求上繼續嘗試重新啟動處理序，除非應用程式在上一次循環的分鐘內無法啟動的次數達到 **rapidFailsPerMinute** 所指定的次數。</span><span class="sxs-lookup"><span data-stu-id="aeab8-684">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="aeab8-685">0 (零) 值 **不會** 視為無限逾時。</span><span class="sxs-lookup"><span data-stu-id="aeab8-685">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="aeab8-686">預設： `120`</span><span class="sxs-lookup"><span data-stu-id="aeab8-686">Default: `120`</span></span><br><span data-ttu-id="aeab8-687">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="aeab8-687">Min: `0`</span></span><br><span data-ttu-id="aeab8-688">最大值︰`3600`</span><span class="sxs-lookup"><span data-stu-id="aeab8-688">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="aeab8-689">選擇性的 Boolean 屬性。</span><span class="sxs-lookup"><span data-stu-id="aeab8-689">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="aeab8-690">如果為 true，就會將 **processPath** 中所指定處理序的 **stdout** 和 **stderr** 重新導向到 **stdoutLogFile** 中所指定的檔案。</span><span class="sxs-lookup"><span data-stu-id="aeab8-690">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="aeab8-691">選擇性字串屬性。</span><span class="sxs-lookup"><span data-stu-id="aeab8-691">Optional string attribute.</span></span></p><p><span data-ttu-id="aeab8-692">指定記錄來自 **processPath** 中所指定處理序之 **stdout** 和 **stderr** 的相對或絕對檔案路徑。</span><span class="sxs-lookup"><span data-stu-id="aeab8-692">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="aeab8-693">相對路徑是相對於網站的根目錄。</span><span class="sxs-lookup"><span data-stu-id="aeab8-693">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="aeab8-694">所有開頭為 `.` 的路徑都是網站根目錄的相對路徑，而所有其他路徑則視為絕對路徑。</span><span class="sxs-lookup"><span data-stu-id="aeab8-694">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="aeab8-695">路徑中提供的所有資料夾都必須存在，模組才能建立記錄檔。</span><span class="sxs-lookup"><span data-stu-id="aeab8-695">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="aeab8-696">使用底線分隔符號，時間戳記、處理序識別碼及副檔名 (*.log*) 會新增至 **stdoutLogFile** 路徑的最後一個區段。</span><span class="sxs-lookup"><span data-stu-id="aeab8-696">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="aeab8-697">如果提供 `.\logs\stdout` 作為值，在 2018 年 2 月 5 日的 19:41:32 以處理序識別碼 1934 進行儲存時，範例 stdout 記錄檔就會以 *stdout_20180205194132_1934.log* 的形式儲存在 [logs] 資料夾中。</span><span class="sxs-lookup"><span data-stu-id="aeab8-697">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="aeab8-698">設定環境變數</span><span class="sxs-lookup"><span data-stu-id="aeab8-698">Setting environment variables</span></span>

<span data-ttu-id="aeab8-699">您可以在 `processPath` 屬性中為處理序指定環境變數。</span><span class="sxs-lookup"><span data-stu-id="aeab8-699">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="aeab8-700">請使用 `<environmentVariables>` 集合元素的 `<environmentVariable>` 子元素來指定環境變數。</span><span class="sxs-lookup"><span data-stu-id="aeab8-700">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="aeab8-701">此節中所設定的環境變數與使用相同名稱設定的系統環境變數相衝突。</span><span class="sxs-lookup"><span data-stu-id="aeab8-701">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="aeab8-702">若同時在 *web.config* 檔案與 Windows 中的系統層級設定環境變數，來自 *web.config* 檔案的值會成為附加到系統環境變數值 (例如，`ASPNETCORE_ENVIRONMENT: Development;Development`)，這會造成應用程式無法啟動。</span><span class="sxs-lookup"><span data-stu-id="aeab8-702">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

<span data-ttu-id="aeab8-703">下列範例會設定兩個環境變數。</span><span class="sxs-lookup"><span data-stu-id="aeab8-703">The following example sets two environment variables.</span></span> <span data-ttu-id="aeab8-704">`ASPNETCORE_ENVIRONMENT` 會將應用程式的環境設定為 `Development`。</span><span class="sxs-lookup"><span data-stu-id="aeab8-704">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="aeab8-705">開發人員可以在 *web.config* 檔案中暫時設定這個值，以在進行應用程式例外狀況偵錯時，強制 [開發人員例外狀況頁面](xref:fundamentals/error-handling)載入。</span><span class="sxs-lookup"><span data-stu-id="aeab8-705">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="aeab8-706">`CONFIG_DIR` 是一個使用者定義的環境變數範例，其中開發人員已撰寫程式碼，會在啟動時讀取值來構成用以載入應用程式設定檔的路徑。</span><span class="sxs-lookup"><span data-stu-id="aeab8-706">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!WARNING]
> <span data-ttu-id="aeab8-707">請只有在未受信任網路 (例如網際網路) 無法存取的暫存和測試伺服器上，才將 `ASPNETCORE_ENVIRONMENT` 環境變數設定為 `Development`。</span><span class="sxs-lookup"><span data-stu-id="aeab8-707">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="aeab8-708">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="aeab8-708">app_offline.htm</span></span>

<span data-ttu-id="aeab8-709">如果在應用程式根目錄中偵測到名稱為 *app_offline.htm* 的檔案，ASP.NET Core Module 會嘗試正常關閉應用程式並停止處理連入的要求。</span><span class="sxs-lookup"><span data-stu-id="aeab8-709">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="aeab8-710">如果在經過 `shutdownTimeLimit` 中所定義的秒數之後，應用程式仍然在執行，ASP.NET Core Module 就會終止執行中的處理序。</span><span class="sxs-lookup"><span data-stu-id="aeab8-710">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="aeab8-711">當 *app_offline.htm* 檔案存在時，ASP.NET Core 模組會藉由傳回 *app_offline.htm* 檔案的內容來回應要求。</span><span class="sxs-lookup"><span data-stu-id="aeab8-711">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="aeab8-712">已移除 *app_offline.htm* 檔案時，下一個要求則會啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="aeab8-712">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="aeab8-713">啟動錯誤頁面</span><span class="sxs-lookup"><span data-stu-id="aeab8-713">Start-up error page</span></span>

<span data-ttu-id="aeab8-714">如果 ASP.NET Core 模組無法啟動後端處理序，或後端處理序啟動但無法在所設定的連接埠上進行接聽，就會顯示 [502.5 - 處理序失敗] 狀態碼頁面。</span><span class="sxs-lookup"><span data-stu-id="aeab8-714">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="aeab8-715">若要抑制此頁面並還原至預設的 IIS 502 狀態碼頁面，請使用 `disableStartUpErrorPage` 屬性。</span><span class="sxs-lookup"><span data-stu-id="aeab8-715">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="aeab8-716">如需有關設定自訂錯誤訊息的詳細資訊，請參閱 [HTTP 錯誤\<httpErrors>](/iis/configuration/system.webServer/httpErrors/)。</span><span class="sxs-lookup"><span data-stu-id="aeab8-716">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="aeab8-717">記錄檔建立和重新導向</span><span class="sxs-lookup"><span data-stu-id="aeab8-717">Log creation and redirection</span></span>

<span data-ttu-id="aeab8-718">如果已設定 `aspNetCore` 元素的 `stdoutLogEnabled` 和 `stdoutLogFile` 屬性，ASP.NET Core 模組就會將 stdout 和 stderr 主控台輸出重新導向到磁碟。</span><span class="sxs-lookup"><span data-stu-id="aeab8-718">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="aeab8-719">建立記錄檔後，模組會建立 `stdoutLogFile` 路徑中的所有資料夾。</span><span class="sxs-lookup"><span data-stu-id="aeab8-719">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="aeab8-720">應用程式集區必須具有記錄檔寫入位置的寫入權限 (請使用 `IIS AppPool\<app_pool_name>` 來提供寫入權限)。</span><span class="sxs-lookup"><span data-stu-id="aeab8-720">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="aeab8-721">除非發生處理序回收/重新啟動，否則不會輪替記錄檔。</span><span class="sxs-lookup"><span data-stu-id="aeab8-721">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="aeab8-722">主機服務提供者必須負責限制記錄檔所使用的磁碟空間。</span><span class="sxs-lookup"><span data-stu-id="aeab8-722">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="aeab8-723">只有在 IIS 上裝載時，或使用 [VISUAL STUDIO iis 的開發階段支援來](xref:host-and-deploy/iis/development-time-iis-support)針對應用程式啟動問題進行疑難排解時，才建議使用 stdout 記錄檔，而不是在本機進行偵錯工具並使用 IIS Express 執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="aeab8-723">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="aeab8-724">請勿將 stdout 記錄檔用來進行一般應用程式記錄。</span><span class="sxs-lookup"><span data-stu-id="aeab8-724">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="aeab8-725">針對 ASP.NET Core 應用程式中的例行性記錄，請使用會限制記錄檔大小並輪替記錄檔的記錄程式庫。</span><span class="sxs-lookup"><span data-stu-id="aeab8-725">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="aeab8-726">如需詳細資訊，請參閱[協力廠商記錄提供者](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="aeab8-726">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="aeab8-727">建立記錄檔時，系統會自動新增時間戳記和副檔名。</span><span class="sxs-lookup"><span data-stu-id="aeab8-727">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="aeab8-728">記錄檔名稱會藉由將時間戳記、處理序識別碼及副檔名 (*.log*) 以底線分隔並附加至 `stdoutLogFile` 路徑的最後一個區段 (通常是 *stdout*) 來組成。</span><span class="sxs-lookup"><span data-stu-id="aeab8-728">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="aeab8-729">如果 `stdoutLogFile` 路徑的結尾是 *stdout*，則在 2018 年 2 月 5 日 19:42:32 建立且 PID 為 1934 的應用程式記錄檔檔案名稱會是 *stdout_20180205194132_1934.log*。</span><span class="sxs-lookup"><span data-stu-id="aeab8-729">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="aeab8-730">下列範例專案會 `aspNetCore` 在相對路徑設定 stdout 記錄 `.\log\` 。</span><span class="sxs-lookup"><span data-stu-id="aeab8-730">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="aeab8-731">請確認 AppPool 使用者身分識別具備所提供路徑的寫入權限。</span><span class="sxs-lookup"><span data-stu-id="aeab8-731">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

<span data-ttu-id="aeab8-732">針對 Azure App Service 部署發行應用程式時，Web SDK 會將 `stdoutLogFile` 值設定為 `\\?\%home%\LogFiles\stdout` 。</span><span class="sxs-lookup"><span data-stu-id="aeab8-732">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="aeab8-733">`%home`環境變數是針對 Azure App Service 所裝載的應用程式預先定義。</span><span class="sxs-lookup"><span data-stu-id="aeab8-733">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="aeab8-734">若要建立記錄篩選規則，請參閱 ASP.NET Core 記錄檔[集的設定和](xref:fundamentals/logging/index#log-filtering)[記錄篩選](xref:fundamentals/logging/index#log-filtering)區段。</span><span class="sxs-lookup"><span data-stu-id="aeab8-734">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="aeab8-735">如需路徑格式的詳細資訊，請參閱 [Windows 系統上的檔案路徑格式](/dotnet/standard/io/file-path-formats)。</span><span class="sxs-lookup"><span data-stu-id="aeab8-735">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="aeab8-736">Proxy 組態使用 HTTP 通訊協定和配對權杖</span><span class="sxs-lookup"><span data-stu-id="aeab8-736">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="aeab8-737">在 ASP.NET Core 模組與 Kestrel 之間建立的 Proxy 會使用 HTTP 通訊協定。</span><span class="sxs-lookup"><span data-stu-id="aeab8-737">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="aeab8-738">沒有從伺服器外的位置竊聽模組與 Kestrel 之間流量的風險。</span><span class="sxs-lookup"><span data-stu-id="aeab8-738">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="aeab8-739">配對權杖用來保證 Kestrel 所接收的要求已由 IIS 代理，而且不是來自其他來源。</span><span class="sxs-lookup"><span data-stu-id="aeab8-739">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="aeab8-740">模組會建立配對權杖，並將其設定成環境變數 (`ASPNETCORE_TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="aeab8-740">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="aeab8-741">配對權杖也會設定成每個代理要求的標頭 (`MS-ASPNETCORE-TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="aeab8-741">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="aeab8-742">IIS 中介軟體會檢查其收到的每個要求，以確認配對權杖的標頭值符合環境變數值。</span><span class="sxs-lookup"><span data-stu-id="aeab8-742">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="aeab8-743">如果權杖值不相符，將記錄並拒絕要求。</span><span class="sxs-lookup"><span data-stu-id="aeab8-743">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="aeab8-744">使用者無法從伺服器外的位置存取配對權杖環境變數，以及模組與 Kestrel 之間的流量。</span><span class="sxs-lookup"><span data-stu-id="aeab8-744">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="aeab8-745">在不知道配對權杖值的情況下，攻擊者無法略過 IIS 中介軟體的檢查送出要求。</span><span class="sxs-lookup"><span data-stu-id="aeab8-745">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="aeab8-746">具有 IIS 共用設定的 ASP.NET Core 模組</span><span class="sxs-lookup"><span data-stu-id="aeab8-746">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="aeab8-747">ASP.NET Core 模組安裝程式會以 **TrustedInstaller** 帳戶的權限執行。</span><span class="sxs-lookup"><span data-stu-id="aeab8-747">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="aeab8-748">由於本機系統帳戶並未具備 IIS 共用設定所使用的共用路徑修改權限，因此，安裝程式在嘗試於共用上的 *applicationHost.config* 檔案中進行模組設定時，會擲回拒絕存取的錯誤。</span><span class="sxs-lookup"><span data-stu-id="aeab8-748">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="aeab8-749">使用「IIS 共用設定」時，請依照下列步驟進行操作：</span><span class="sxs-lookup"><span data-stu-id="aeab8-749">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="aeab8-750">停用「IIS 共用設定」。</span><span class="sxs-lookup"><span data-stu-id="aeab8-750">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="aeab8-751">執行安裝程式。</span><span class="sxs-lookup"><span data-stu-id="aeab8-751">Run the installer.</span></span>
1. <span data-ttu-id="aeab8-752">將已更新的 *applicationHost.config* 檔案匯出到共用。</span><span class="sxs-lookup"><span data-stu-id="aeab8-752">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="aeab8-753">重新啟用「IIS 共用設定」。</span><span class="sxs-lookup"><span data-stu-id="aeab8-753">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="aeab8-754">模組版本和裝載套件組合安裝程式記錄檔</span><span class="sxs-lookup"><span data-stu-id="aeab8-754">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="aeab8-755">判斷已安裝的 ASP.NET Core 模組版本：</span><span class="sxs-lookup"><span data-stu-id="aeab8-755">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="aeab8-756">在主控系統上，瀏覽至 *%windir%\System32\inetsrv*。</span><span class="sxs-lookup"><span data-stu-id="aeab8-756">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="aeab8-757">找出 *aspnetcore.dll* 檔案。</span><span class="sxs-lookup"><span data-stu-id="aeab8-757">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="aeab8-758">在該檔案上按一下滑鼠右鍵，然後從關聯式功能表中選取 [內容]。</span><span class="sxs-lookup"><span data-stu-id="aeab8-758">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="aeab8-759">選取 [ **詳細資料** ] 索引標籤。檔案 **版本** 和 **產品版本** 代表已安裝的模組版本。</span><span class="sxs-lookup"><span data-stu-id="aeab8-759">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="aeab8-760">模組的裝載套件組合安裝程式記錄檔位於 *C： \\ Users \\ % UserName% \\ AppData \\ Local \\ Temp*。檔案的名稱 *dd_DotNetCoreWinSvrHosting__ \<timestamp> _000_AspNetCoreModule_x64 .log*。</span><span class="sxs-lookup"><span data-stu-id="aeab8-760">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="aeab8-761">模組、結構描述及設定檔位置</span><span class="sxs-lookup"><span data-stu-id="aeab8-761">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="aeab8-762">模組</span><span class="sxs-lookup"><span data-stu-id="aeab8-762">Module</span></span>

<span data-ttu-id="aeab8-763">**IIS (x86/amd64)：**</span><span class="sxs-lookup"><span data-stu-id="aeab8-763">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="aeab8-764">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="aeab8-764">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="aeab8-765">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="aeab8-765">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

<span data-ttu-id="aeab8-766">**IIS Express (x86/amd64)：**</span><span class="sxs-lookup"><span data-stu-id="aeab8-766">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="aeab8-767">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="aeab8-767">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="aeab8-768">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="aeab8-768">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

### <a name="schema"></a><span data-ttu-id="aeab8-769">結構描述</span><span class="sxs-lookup"><span data-stu-id="aeab8-769">Schema</span></span>

<span data-ttu-id="aeab8-770">**IIS**</span><span class="sxs-lookup"><span data-stu-id="aeab8-770">**IIS**</span></span>

* <span data-ttu-id="aeab8-771">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="aeab8-771">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

<span data-ttu-id="aeab8-772">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="aeab8-772">**IIS Express**</span></span>

* <span data-ttu-id="aeab8-773">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="aeab8-773">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="aeab8-774">組態</span><span class="sxs-lookup"><span data-stu-id="aeab8-774">Configuration</span></span>

<span data-ttu-id="aeab8-775">**IIS**</span><span class="sxs-lookup"><span data-stu-id="aeab8-775">**IIS**</span></span>

* <span data-ttu-id="aeab8-776">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="aeab8-776">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="aeab8-777">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="aeab8-777">**IIS Express**</span></span>

* <span data-ttu-id="aeab8-778">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="aeab8-778">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="aeab8-779">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="aeab8-779">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="aeab8-780">在 *applicationHost.config* 檔案中搜尋 *aspnetcore*，即可找到這些檔案。</span><span class="sxs-lookup"><span data-stu-id="aeab8-780">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="aeab8-781">其他資源</span><span class="sxs-lookup"><span data-stu-id="aeab8-781">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <span data-ttu-id="aeab8-782">[ASP.NET Core 模組參考來源 [預設分支 (主要) ]](https://github.com/dotnet/aspnetcore/tree/main/src/Servers/IIS/AspNetCoreModuleV2)：使用 [ **分支** ] 下拉式清單來選取特定版本 (例如， `release/3.1`) 。</span><span class="sxs-lookup"><span data-stu-id="aeab8-782">[ASP.NET Core Module reference source [default branch (main)]](https://github.com/dotnet/aspnetcore/tree/main/src/Servers/IIS/AspNetCoreModuleV2): Use the **Branch** drop down list to select a specific release (for example, `release/3.1`).</span></span>
* <xref:host-and-deploy/iis/modules>
