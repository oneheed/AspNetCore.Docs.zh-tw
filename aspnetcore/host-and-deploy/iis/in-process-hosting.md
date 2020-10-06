---
title: 使用 IIS 和 ASP.NET Core 的同進程裝載
author: rick-anderson
description: 瞭解 IIS 和 ASP.NET Core 模組的同進程裝載。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/in-process-hosting
ms.openlocfilehash: ecf873e6ad2044aae87a5e7dc07316eae0e10912
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755240"
---
# <a name="in-process-hosting-with-iis-and-aspnet-core"></a><span data-ttu-id="adadc-103">使用 IIS 和 ASP.NET Core 的同進程裝載</span><span class="sxs-lookup"><span data-stu-id="adadc-103">In-process hosting with IIS and ASP.NET Core</span></span> 

<span data-ttu-id="adadc-104">同進程裝載會在與 IIS 背景工作進程相同的進程中執行 ASP.NET Core 應用程式。</span><span class="sxs-lookup"><span data-stu-id="adadc-104">In-process hosting runs an ASP.NET Core app in the same process as its IIS worker process.</span></span> <span data-ttu-id="adadc-105">因為要求未透過回送介面卡 (將連出網路流量傳回同一部電腦的網路介面) 進行 proxy 處理，所以同處理序裝載會提供優於跨處理序裝載的效能。</span><span class="sxs-lookup"><span data-stu-id="adadc-105">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span>

<span data-ttu-id="adadc-106">下圖說明 IIS、ASP.NET Core 模組和同處理序裝載應用程式之間的關聯性：</span><span class="sxs-lookup"><span data-stu-id="adadc-106">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![同處理序代管內的 ASP.NET Core 模組案例](index/_static/ancm-inprocess.png)

## <a name="enable-in-process-hosting"></a><span data-ttu-id="adadc-108">啟用同進程裝載</span><span class="sxs-lookup"><span data-stu-id="adadc-108">Enable in-process hosting</span></span>

<span data-ttu-id="adadc-109">自 ASP.NET Core 3.0 起，所有部署至 IIS 的應用程式預設都會啟用同進程裝載。</span><span class="sxs-lookup"><span data-stu-id="adadc-109">Since ASP.NET Core 3.0, in-process hosting has been enabled by default for all app deployed to IIS.</span></span>

<span data-ttu-id="adadc-110">若要明確設定同進程裝載的應用程式，請將專案檔中的屬性值設定 `<AspNetCoreHostingModel>` 為， `InProcess` (`.csproj`) ：</span><span class="sxs-lookup"><span data-stu-id="adadc-110">To explicitly configure an app for in-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `InProcess` in the project file (`.csproj`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

## <a name="general-architecture"></a><span data-ttu-id="adadc-111">一般架構</span><span class="sxs-lookup"><span data-stu-id="adadc-111">General architecture</span></span>

<span data-ttu-id="adadc-112">要求的一般流程如下所示：</span><span class="sxs-lookup"><span data-stu-id="adadc-112">The general flow of a request is as follows:</span></span>

1. <span data-ttu-id="adadc-113">要求會從 Web 到達核心模式的 HTTP.sys 驅動程式。</span><span class="sxs-lookup"><span data-stu-id="adadc-113">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="adadc-114">驅動程式會在網站設定的連接埠上將原生要求路由至 IIS，此連接埠通常是 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="adadc-114">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="adadc-115">ASP.NET Core 模組會接收原生要求，並將它傳遞給 IIS HTTP 伺服器 (`IISHttpServer`) 。</span><span class="sxs-lookup"><span data-stu-id="adadc-115">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="adadc-116">IIS HTTP 伺服器是 IIS 的同處理序伺服程式實作，可將要求從原生轉換為受控。</span><span class="sxs-lookup"><span data-stu-id="adadc-116">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="adadc-117">IIS HTTP 伺服器處理要求之後：</span><span class="sxs-lookup"><span data-stu-id="adadc-117">After the IIS HTTP Server processes the request:</span></span>

1. <span data-ttu-id="adadc-118">要求會傳送至 ASP.NET Core 中介軟體管線。</span><span class="sxs-lookup"><span data-stu-id="adadc-118">The request is sent to the ASP.NET Core middleware pipeline.</span></span>
1. <span data-ttu-id="adadc-119">中介軟體管線會處理要求，並將其作為 `HttpContext` 執行個體傳遞至應用程式的邏輯。</span><span class="sxs-lookup"><span data-stu-id="adadc-119">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span>
1. <span data-ttu-id="adadc-120">應用程式的回應會透過 IIS HTTP 伺服器傳回 IIS。</span><span class="sxs-lookup"><span data-stu-id="adadc-120">The app's response is passed back to IIS through IIS HTTP Server.</span></span>
1. <span data-ttu-id="adadc-121">IIS 會將回應傳送到起始該要求的用戶端。</span><span class="sxs-lookup"><span data-stu-id="adadc-121">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="adadc-122">`CreateDefaultBuilder` 藉 <xref:Microsoft.AspNetCore.Hosting.Server.IServer> 由呼叫方法來加入實例， <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> 以啟動 [CoreCLR](/dotnet/standard/glossary#coreclr) ，並將應用程式裝載在 IIS 工作者進程內 (`w3wp.exe` 或 `iisexpress.exe`) 。</span><span class="sxs-lookup"><span data-stu-id="adadc-122">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (`w3wp.exe` or `iisexpress.exe`).</span></span> <span data-ttu-id="adadc-123">效能測試指出，相較於將 .NET Core 應用程式裝載於處理序外，並將要求 Proxy 處理至 [Kestrel](xref:fundamentals/servers/kestrel)，裝載於處理序內可提供明顯更高的要求輸送量。</span><span class="sxs-lookup"><span data-stu-id="adadc-123">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="adadc-124">發佈為單一檔案可執行檔的應用程式無法由同處理序裝載模型載入。</span><span class="sxs-lookup"><span data-stu-id="adadc-124">Apps published as a single file executable can't be loaded by the in-process hosting model.</span></span>

## <a name="application-configuration"></a><span data-ttu-id="adadc-125">應用程式設定</span><span class="sxs-lookup"><span data-stu-id="adadc-125">Application configuration</span></span>

<span data-ttu-id="adadc-126">若要設定 IIS 選項，請在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A> 中加入 <xref:Microsoft.AspNetCore.Builder.IISServerOptions> 的服務設定。</span><span class="sxs-lookup"><span data-stu-id="adadc-126">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span></span> <span data-ttu-id="adadc-127">下列範例會停用 AutomaticAuthentication：</span><span class="sxs-lookup"><span data-stu-id="adadc-127">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="adadc-128">選項</span><span class="sxs-lookup"><span data-stu-id="adadc-128">Option</span></span> | <span data-ttu-id="adadc-129">預設</span><span class="sxs-lookup"><span data-stu-id="adadc-129">Default</span></span> | <span data-ttu-id="adadc-130">設定</span><span class="sxs-lookup"><span data-stu-id="adadc-130">Setting</span></span> |
| ------ | :-----: | ------- |
| `AutomaticAuthentication` | `true` | <span data-ttu-id="adadc-131">若為 `true`，IIS 伺服器會設定由 [Windows 驗證](xref:security/authentication/windowsauth)所驗證的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="adadc-131">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="adadc-132">若為 `false`，則伺服器僅會對 `HttpContext.User` 提供身分識別，並在 `AuthenticationScheme` 明確要求時回應挑戰。</span><span class="sxs-lookup"><span data-stu-id="adadc-132">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="adadc-133">必須在 IIS 中啟用 Windows 驗證以讓 `AutomaticAuthentication` 作用。</span><span class="sxs-lookup"><span data-stu-id="adadc-133">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="adadc-134">如需詳細資訊，請參閱 [Windows 驗證](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="adadc-134">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName` | `null` | <span data-ttu-id="adadc-135">設定使用者在登入頁面上看到的顯示名稱。</span><span class="sxs-lookup"><span data-stu-id="adadc-135">Sets the display name shown to users on login pages.</span></span> |
| `AllowSynchronousIO` | `false` | <span data-ttu-id="adadc-136">和是否允許同步 i/o `HttpContext.Request` `HttpContext.Response` 。</span><span class="sxs-lookup"><span data-stu-id="adadc-136">Whether synchronous I/O is allowed for the `HttpContext.Request` and the `HttpContext.Response`.</span></span> |
| `MaxRequestBodySize` | `30000000` | <span data-ttu-id="adadc-137">取得或設定 `HttpRequest` 的要求本文大小上限。</span><span class="sxs-lookup"><span data-stu-id="adadc-137">Gets or sets the max request body size for the `HttpRequest`.</span></span> <span data-ttu-id="adadc-138">請注意，IIS 本身具有限制 `maxAllowedContentLength`，此限制將在 `IISServerOptions` 中設定 `MaxRequestBodySize` 時處理。</span><span class="sxs-lookup"><span data-stu-id="adadc-138">Note that IIS itself has the limit `maxAllowedContentLength` which will be processed before the `MaxRequestBodySize` set in the `IISServerOptions`.</span></span> <span data-ttu-id="adadc-139">變更 `MaxRequestBodySize` 將不會影響 `maxAllowedContentLength`。</span><span class="sxs-lookup"><span data-stu-id="adadc-139">Changing the `MaxRequestBodySize` won't affect the `maxAllowedContentLength`.</span></span> <span data-ttu-id="adadc-140">若要增加 `maxAllowedContentLength` ，請在中加入專案， `web.config` 以將設定 `maxAllowedContentLength` 為較高的值。</span><span class="sxs-lookup"><span data-stu-id="adadc-140">To increase `maxAllowedContentLength`, add an entry in the `web.config` to set `maxAllowedContentLength` to a higher value.</span></span> <span data-ttu-id="adadc-141">如需更多詳細資料，請參閱[組態](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration)。</span><span class="sxs-lookup"><span data-stu-id="adadc-141">For more details, see [Configuration](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span></span> |

## <a name="differences-between-in-process-and-out-of-process-hosting"></a><span data-ttu-id="adadc-142">同進程和跨進程裝載之間的差異</span><span class="sxs-lookup"><span data-stu-id="adadc-142">Differences between in-process and out-of-process hosting</span></span>

<span data-ttu-id="adadc-143">同處理序裝載時具有下列特性：</span><span class="sxs-lookup"><span data-stu-id="adadc-143">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="adadc-144">使用 IIS HTTP 伺服器 (`IISHttpServer`) 而不是 [Kestrel](xref:fundamentals/servers/kestrel) 伺服器。</span><span class="sxs-lookup"><span data-stu-id="adadc-144">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="adadc-145">針對同進程， [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) 呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> ：</span><span class="sxs-lookup"><span data-stu-id="adadc-145">For in-process, [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> to:</span></span>

  * <span data-ttu-id="adadc-146">註冊 `IISHttpServer`。</span><span class="sxs-lookup"><span data-stu-id="adadc-146">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="adadc-147">設定伺服器在 ASP.NET Core 模組後方執行時應該接聽的連接埠和基底路徑。</span><span class="sxs-lookup"><span data-stu-id="adadc-147">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="adadc-148">設定主機以擷取啟動錯誤。</span><span class="sxs-lookup"><span data-stu-id="adadc-148">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="adadc-149">[ `requestTimeout` 屬性](xref:host-and-deploy/iis/web-config#attributes-of-the-aspnetcore-element)不適用於同進程裝載。</span><span class="sxs-lookup"><span data-stu-id="adadc-149">The [`requestTimeout` attribute](xref:host-and-deploy/iis/web-config#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="adadc-150">不支援在應用程式之間共用應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="adadc-150">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="adadc-151">每個應用程式使用一個應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="adadc-151">Use one app pool per app.</span></span>

* <span data-ttu-id="adadc-152">應用程式的架構 (位元) 和已安裝的執行階段 (x64 或 x86) 必須符合應用程式集區的架構。</span><span class="sxs-lookup"><span data-stu-id="adadc-152">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span> <span data-ttu-id="adadc-153">例如，針對32位 (x86) 發佈的應用程式必須為其 IIS 應用程式集區啟用32位。</span><span class="sxs-lookup"><span data-stu-id="adadc-153">For example, apps published for 32-bit (x86) must have 32-bit enabled for their IIS Application Pools.</span></span> <span data-ttu-id="adadc-154">如需詳細資訊，請參閱 [建立 IIS 網站](xref:tutorials/publish-to-iis#create-the-iis-site) 一節。</span><span class="sxs-lookup"><span data-stu-id="adadc-154">For more information, see the [Create the IIS site](xref:tutorials/publish-to-iis#create-the-iis-site) section.</span></span>

* <span data-ttu-id="adadc-155">偵測到用戶端中斷連線。</span><span class="sxs-lookup"><span data-stu-id="adadc-155">Client disconnects are detected.</span></span> <span data-ttu-id="adadc-156">[`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted%2A)當用戶端中斷連線時，解除標記就會取消。</span><span class="sxs-lookup"><span data-stu-id="adadc-156">The [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted%2A) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="adadc-157">裝載同處理序時，不會內部呼叫 <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync%2A> 來將使用者初始化。</span><span class="sxs-lookup"><span data-stu-id="adadc-157">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync%2A> isn't called internally to initialize a user.</span></span> <span data-ttu-id="adadc-158">因此，預設會在未啟動每個驗證之後，使用 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 實作來轉換宣告。</span><span class="sxs-lookup"><span data-stu-id="adadc-158">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="adadc-159">使用 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 實作來轉換宣告時，請呼叫 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A> 來新增入驗證服務：</span><span class="sxs-lookup"><span data-stu-id="adadc-159">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A> to add authentication services:</span></span>

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
  
* <span data-ttu-id="adadc-160">不支援[ (單一檔案) 部署的 Web 套件](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages)。</span><span class="sxs-lookup"><span data-stu-id="adadc-160">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>
