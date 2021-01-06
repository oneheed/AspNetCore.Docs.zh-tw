---
title: 使用 IIS 和 ASP.NET Core 的跨進程裝載
author: rick-anderson
description: 瞭解 IIS 和 ASP.NET Core 模組的跨進程裝載。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/out-of-process-hosting
ms.openlocfilehash: 78ead27bd1373237d1c0a48655d73a41a0a72279
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060413"
---
# <a name="out-of-process-hosting-with-iis-and-aspnet-core"></a><span data-ttu-id="70468-103">使用 IIS 和 ASP.NET Core 的跨進程裝載</span><span class="sxs-lookup"><span data-stu-id="70468-103">Out-of-process hosting with IIS and ASP.NET Core</span></span> 

<span data-ttu-id="70468-104">由於 ASP.NET Core apps 會在與 IIS 背景工作進程不同的進程中執行，因此 ASP.NET Core 模組會處理進程管理。</span><span class="sxs-lookup"><span data-stu-id="70468-104">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="70468-105">此模組會在第一個要求到達時啟動 ASP.NET Core 應用程式的處理序，並在應用程式關閉或損毀時將它重新啟動。</span><span class="sxs-lookup"><span data-stu-id="70468-105">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="70468-106">此行為基本上與執行同處理序，並由 [Windows 處理序啟用服務 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 所管理的應用程式相同。</span><span class="sxs-lookup"><span data-stu-id="70468-106">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="70468-107">下圖說明 IIS、ASP.NET Core 模組和跨處理序裝載應用程式之間的關聯性：</span><span class="sxs-lookup"><span data-stu-id="70468-107">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![非同處理序代管內的 ASP.NET Core 模組案例](index/_static/ancm-outofprocess.png)

1. <span data-ttu-id="70468-109">要求會從 Web 到達核心模式的 HTTP.sys 驅動程式。</span><span class="sxs-lookup"><span data-stu-id="70468-109">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="70468-110">驅動程式會在網站設定的埠上將要求路由傳送至 IIS。</span><span class="sxs-lookup"><span data-stu-id="70468-110">The driver routes the requests to IIS on the website's configured port.</span></span> <span data-ttu-id="70468-111">設定的埠通常是 80 (HTTP) 或 443 (HTTPS) 。</span><span class="sxs-lookup"><span data-stu-id="70468-111">The configured port is usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="70468-112">模組會將要求轉送到應用程式的隨機埠上的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="70468-112">The module forwards the requests to Kestrel on a random port for the app.</span></span> <span data-ttu-id="70468-113">隨機埠不是80或443。</span><span class="sxs-lookup"><span data-stu-id="70468-113">The random port isn't 80 or 443.</span></span>

<!-- make this a bullet list -->
<span data-ttu-id="70468-114">ASP.NET Core 模組會在啟動時透過環境變數指定埠。</span><span class="sxs-lookup"><span data-stu-id="70468-114">The ASP.NET Core Module specifies the port via an environment variable at startup.</span></span> <span data-ttu-id="70468-115">延伸模組會設定 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> 要接聽的伺服器 `http://localhost:{PORT}` 。</span><span class="sxs-lookup"><span data-stu-id="70468-115">The <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="70468-116">將會執行額外檢查，不是源自模組的要求都會遭到拒絕。</span><span class="sxs-lookup"><span data-stu-id="70468-116">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="70468-117">模組不支援 HTTPS 轉送。</span><span class="sxs-lookup"><span data-stu-id="70468-117">The module doesn't support HTTPS forwarding.</span></span> <span data-ttu-id="70468-118">即使 IIS 透過 HTTPS 接收，要求還是會透過 HTTP 轉送。</span><span class="sxs-lookup"><span data-stu-id="70468-118">Requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="70468-119">在 Kestrel 收取來自模組的要求之後，會將要求轉送到 ASP.NET Core 中介軟體管線。</span><span class="sxs-lookup"><span data-stu-id="70468-119">After Kestrel picks up the request from the module, the request is forwarded into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="70468-120">中介軟體管線會處理要求，並將其作為 `HttpContext` 執行個體傳遞至應用程式的邏輯。</span><span class="sxs-lookup"><span data-stu-id="70468-120">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="70468-121">IIS Integration 新增的中介軟體會更新配置、遠端 IP 和帳戶路徑基底，以將要求轉送至 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="70468-121">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="70468-122">應用程式的回應會傳回 IIS，然後將其轉送回起始要求的 HTTP 用戶端。</span><span class="sxs-lookup"><span data-stu-id="70468-122">The app's response is passed back to IIS, which forwards it back to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="70468-123">如需 ASP.NET Core 模組組態指南，請參閱 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="70468-123">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="70468-124">如需代管的詳細資訊，請參閱[在 ASP.NET Core 中代管](xref:fundamentals/index#host)。</span><span class="sxs-lookup"><span data-stu-id="70468-124">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="70468-125">應用程式設定</span><span class="sxs-lookup"><span data-stu-id="70468-125">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="70468-126">啟用 IISIntegration 元件</span><span class="sxs-lookup"><span data-stu-id="70468-126">Enable the IISIntegration components</span></span>

<span data-ttu-id="70468-127">在 () 中建立主機時 `CreateHostBuilder` `Program.cs` ，請呼叫 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> 來啟用 IIS 整合：</span><span class="sxs-lookup"><span data-stu-id="70468-127">When building a host in `CreateHostBuilder` (`Program.cs`), call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> to enable IIS integration:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="70468-128">如需 `CreateDefaultBuilder` 的詳細資訊，請參閱 <xref:fundamentals/host/generic-host#default-builder-settings>。</span><span class="sxs-lookup"><span data-stu-id="70468-128">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/generic-host#default-builder-settings>.</span></span>


<span data-ttu-id="70468-129">**跨處理序裝載模型**</span><span class="sxs-lookup"><span data-stu-id="70468-129">**Out-of-process hosting model**</span></span>

<span data-ttu-id="70468-130">若要設定 IIS 選項，請在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A> 中加入 <xref:Microsoft.AspNetCore.Builder.IISOptions> 的服務設定。</span><span class="sxs-lookup"><span data-stu-id="70468-130">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span></span> <span data-ttu-id="70468-131">下列範例會防止應用程式填入 `HttpContext.Connection.ClientCertificate`：</span><span class="sxs-lookup"><span data-stu-id="70468-131">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="70468-132">選項</span><span class="sxs-lookup"><span data-stu-id="70468-132">Option</span></span>                         | <span data-ttu-id="70468-133">預設</span><span class="sxs-lookup"><span data-stu-id="70468-133">Default</span></span> | <span data-ttu-id="70468-134">設定</span><span class="sxs-lookup"><span data-stu-id="70468-134">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="70468-135">若為 `true`，[IIS 整合中介軟體](#enable-the-iisintegration-components)會設定由 [Windows 驗證](xref:security/authentication/windowsauth)所驗證的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="70468-135">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="70468-136">如果為 `false`，則驗證中介軟體僅針對 `HttpContext.User` 提供身分識別，並在游 `AuthenticationScheme` 提出明確要求時回應挑戰。</span><span class="sxs-lookup"><span data-stu-id="70468-136">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="70468-137">必須在 IIS 中啟用 Windows 驗證以讓 `AutomaticAuthentication` 作用。</span><span class="sxs-lookup"><span data-stu-id="70468-137">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="70468-138">如需詳細資訊，請參閱 [Windows 驗證](xref:security/authentication/windowsauth)主題。</span><span class="sxs-lookup"><span data-stu-id="70468-138">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="70468-139">設定使用者在登入頁面上看到的顯示名稱。</span><span class="sxs-lookup"><span data-stu-id="70468-139">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="70468-140">如果為 `true` 且 `MS-ASPNETCORE-CLIENTCERT` 要求標頭已存在，則會填入 `HttpContext.Connection.ClientCertificate`。</span><span class="sxs-lookup"><span data-stu-id="70468-140">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |


### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="70468-141">Proxy 伺服器和負載平衡器案例</span><span class="sxs-lookup"><span data-stu-id="70468-141">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="70468-142">[IIS 整合中介軟體](#enable-the-iisintegration-components)和 ASP.NET Core 模組會設定為轉寄：</span><span class="sxs-lookup"><span data-stu-id="70468-142">The [IIS Integration Middleware](#enable-the-iisintegration-components) and the ASP.NET Core Module are configured to forward the:</span></span>

* <span data-ttu-id="70468-143"> (HTTP/HTTPS) 的配置。</span><span class="sxs-lookup"><span data-stu-id="70468-143">Scheme (HTTP/HTTPS).</span></span>
* <span data-ttu-id="70468-144">發出要求的遠端 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="70468-144">Remote IP address where the request originated.</span></span>

<span data-ttu-id="70468-145">[IIS 整合中介軟體](#enable-the-iisintegration-components)會設定轉送的標頭中介軟體。</span><span class="sxs-lookup"><span data-stu-id="70468-145">The [IIS Integration Middleware](#enable-the-iisintegration-components) configures Forwarded Headers Middleware.</span></span>

<span data-ttu-id="70468-146">其他 Proxy 伺服器和負載平衡器後方託管的應用程式可能需要其他設定。</span><span class="sxs-lookup"><span data-stu-id="70468-146">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="70468-147">如需詳細資訊，請參閱[設定 ASP.NET Core 以處理 Proxy 伺服器和負載平衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="70468-147">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>


### <a name="out-of-process-hosting-model"></a><span data-ttu-id="70468-148">跨處理序裝載模型</span><span class="sxs-lookup"><span data-stu-id="70468-148">Out-of-process hosting model</span></span>

<span data-ttu-id="70468-149">若要設定跨進程裝載的應用程式，請將專案檔中的屬性值設定 `<AspNetCoreHostingModel>` 為， `OutOfProcess` (`.csproj`) ：</span><span class="sxs-lookup"><span data-stu-id="70468-149">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (`.csproj`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="70468-150">同進程裝載是使用設定 `InProcess` ，這是預設值。</span><span class="sxs-lookup"><span data-stu-id="70468-150">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="70468-151">的值 `<AspNetCoreHostingModel>` 不區分大小寫，因此 `inprocess` 和 `outofprocess` 都是有效的值。</span><span class="sxs-lookup"><span data-stu-id="70468-151">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="70468-152">使用 [Kestrel](xref:fundamentals/servers/kestrel) 伺服器而不是 IIS HTTP 伺服器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="70468-152">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="70468-153">針對跨進程， [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) 呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> ：</span><span class="sxs-lookup"><span data-stu-id="70468-153">For out-of-process, [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> to:</span></span>

* <span data-ttu-id="70468-154">設定伺服器在 ASP.NET Core 模組後方執行時應該接聽的連接埠和基底路徑。</span><span class="sxs-lookup"><span data-stu-id="70468-154">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="70468-155">設定主機以擷取啟動錯誤。</span><span class="sxs-lookup"><span data-stu-id="70468-155">Configure the host to capture startup errors.</span></span>

### <a name="process-name"></a><span data-ttu-id="70468-156">程序名稱</span><span class="sxs-lookup"><span data-stu-id="70468-156">Process name</span></span>

<span data-ttu-id="70468-157">`Process.GetCurrentProcess().ProcessName` 會報告 `w3wp`/`iisexpress` (同處理序) 或 `dotnet` (跨處理序)。</span><span class="sxs-lookup"><span data-stu-id="70468-157">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="70468-158">許多如 Windows 驗證等原生模組仍在使用中。</span><span class="sxs-lookup"><span data-stu-id="70468-158">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="70468-159">若要深入了解搭配 ASP.NET Core 模組的使用中 IIS 模組，請參閱<xref:host-and-deploy/iis/modules>。</span><span class="sxs-lookup"><span data-stu-id="70468-159">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="70468-160">ASP.NET Core 模組也可以：</span><span class="sxs-lookup"><span data-stu-id="70468-160">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="70468-161">設定背景工作處理序的環境變數。</span><span class="sxs-lookup"><span data-stu-id="70468-161">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="70468-162">將 stdout 輸出記錄到檔案儲存區，以針對啟動問題進行疑難排解。</span><span class="sxs-lookup"><span data-stu-id="70468-162">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="70468-163">轉送 Windows 驗證權杖。</span><span class="sxs-lookup"><span data-stu-id="70468-163">Forward Windows authentication tokens.</span></span>
