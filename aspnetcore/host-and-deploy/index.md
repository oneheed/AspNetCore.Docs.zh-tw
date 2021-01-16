---
title: 裝載及部署 ASP.NET Core
author: rick-anderson
description: 了解如何設定裝載環境及部署 ASP.NET Core 應用程式。
monikerRange: '>= aspnetcore-2.1'
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
uid: host-and-deploy/index
ms.openlocfilehash: 05d04a9c95910c805ea28578aba21a0658dd779a
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252964"
---
# <a name="host-and-deploy-aspnet-core"></a><span data-ttu-id="b9347-103">裝載及部署 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b9347-103">Host and deploy ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="b9347-104">一般是將 ASP.NET Core 應用程式部署到裝載環境：</span><span class="sxs-lookup"><span data-stu-id="b9347-104">In general, to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="b9347-105">將已發行的電子郵件部署到裝載伺服器上的資料夾。</span><span class="sxs-lookup"><span data-stu-id="b9347-105">Deploy the published app to a folder on the hosting server.</span></span>
* <span data-ttu-id="b9347-106">設定處理序管理員，以在要求到達時啟動應用程式，並在其損毀或伺服器重新開機後重新啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="b9347-106">Set up a process manager that starts the app when requests arrive and restarts the app after it crashes or the server reboots.</span></span>
* <span data-ttu-id="b9347-107">至於反向 Proxy 的設定，請對反向 Proxy 進行設定，使其將要求轉送到應用程式。</span><span class="sxs-lookup"><span data-stu-id="b9347-107">For configuration of a reverse proxy, set up a reverse proxy to forward requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="b9347-108">發行至資料夾</span><span class="sxs-lookup"><span data-stu-id="b9347-108">Publish to a folder</span></span>

<span data-ttu-id="b9347-109">[dotnet publish](/dotnet/core/tools/dotnet-publish) 命令會編譯應用程式程式碼，並將執行應用程式所需的檔案複製到 *publish* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="b9347-109">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command compiles app code and copies the files required to run the app into a *publish* folder.</span></span> <span data-ttu-id="b9347-110">從 Visual Studio 部署時，`dotnet publish` 步驟會在檔案複製到部署目的地之前自動執行。</span><span class="sxs-lookup"><span data-stu-id="b9347-110">When deploying from Visual Studio, the `dotnet publish` step occurs automatically before the files are copied to the deployment destination.</span></span>

## <a name="publish-settings-files"></a><span data-ttu-id="b9347-111">發佈設定檔案</span><span class="sxs-lookup"><span data-stu-id="b9347-111">Publish settings files</span></span>

<span data-ttu-id="b9347-112">`*.json` 預設會發佈檔案。</span><span class="sxs-lookup"><span data-stu-id="b9347-112">`*.json` files are published by default.</span></span> <span data-ttu-id="b9347-113">若要發佈其他設定檔案，請在 [`<ItemGroup><Content Include= ... />`](/visualstudio/msbuild/common-msbuild-project-items#content) 專案檔的專案中指定它們。</span><span class="sxs-lookup"><span data-stu-id="b9347-113">To publish other settings files, specify them in an [`<ItemGroup><Content Include= ... />`](/visualstudio/msbuild/common-msbuild-project-items#content) element in the project file.</span></span> <span data-ttu-id="b9347-114">下列範例會發佈 XML 檔案：</span><span class="sxs-lookup"><span data-stu-id="b9347-114">The following example publishes XML files:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.xml" Exclude="bin\**\*;obj\**\*"
    CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

### <a name="folder-contents"></a><span data-ttu-id="b9347-115">資料夾內容</span><span class="sxs-lookup"><span data-stu-id="b9347-115">Folder contents</span></span>

<span data-ttu-id="b9347-116">*publish* 資料夾包含一或多個應用程式組件檔、相依性，也可能會有 .NET 執行階段。</span><span class="sxs-lookup"><span data-stu-id="b9347-116">The *publish* folder contains one or more app assembly files, dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="b9347-117">.NET Core 應用程式可以發行為「自主式部署」或「相依於架構的部署」。</span><span class="sxs-lookup"><span data-stu-id="b9347-117">A .NET Core app can be published as *self-contained deployment* or *framework-dependent deployment*.</span></span> <span data-ttu-id="b9347-118">如果應用程式是自主式，包含 .NET 執行階段的組件檔會包含在 *publish* 資料夾中。</span><span class="sxs-lookup"><span data-stu-id="b9347-118">If the app is self-contained, the assembly files that contain the .NET runtime are included in the *publish* folder.</span></span> <span data-ttu-id="b9347-119">如果應用程式是與 Framework 相依的應用程式，則不會包含 .NET 執行階段檔案，因為應用程式具有對伺服器上已安裝之 .NET 版本的參考。</span><span class="sxs-lookup"><span data-stu-id="b9347-119">If the app is framework-dependent, the .NET runtime files aren't included because the app has a reference to a version of .NET that's installed on the server.</span></span> <span data-ttu-id="b9347-120">預設部署模式是與 Framework 相依。</span><span class="sxs-lookup"><span data-stu-id="b9347-120">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="b9347-121">如需詳細資訊，請參閱 [.NET Core 應用程式部署](/dotnet/core/deploying/)。</span><span class="sxs-lookup"><span data-stu-id="b9347-121">For more information, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

<span data-ttu-id="b9347-122">除了 *.exe* 和 *.dll* 檔案之外，ASP.NET Core 應用程式的 *publish* 資料夾通常還包含組態檔、靜態資產和 MVC 檢視。</span><span class="sxs-lookup"><span data-stu-id="b9347-122">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span> <span data-ttu-id="b9347-123">如需詳細資訊，請參閱<xref:host-and-deploy/directory-structure>。</span><span class="sxs-lookup"><span data-stu-id="b9347-123">For more information, see <xref:host-and-deploy/directory-structure>.</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="b9347-124">設定處理序管理員</span><span class="sxs-lookup"><span data-stu-id="b9347-124">Set up a process manager</span></span>

<span data-ttu-id="b9347-125">ASP.NET Core 應用程式是一種主控台應用程式，必須在伺服器開機和損毀後重新啟動。</span><span class="sxs-lookup"><span data-stu-id="b9347-125">An ASP.NET Core app is a console app that must be started when a server boots and restarted if it crashes.</span></span> <span data-ttu-id="b9347-126">若要自動化啟動和重新啟動，需要有處理序管理員。</span><span class="sxs-lookup"><span data-stu-id="b9347-126">To automate starts and restarts, a process manager is required.</span></span> <span data-ttu-id="b9347-127">ASP.NET Core 最常見的處理序管理員是：</span><span class="sxs-lookup"><span data-stu-id="b9347-127">The most common process managers for ASP.NET Core are:</span></span>

* <span data-ttu-id="b9347-128">Linux</span><span class="sxs-lookup"><span data-stu-id="b9347-128">Linux</span></span>
  * [<span data-ttu-id="b9347-129">Nginx</span><span class="sxs-lookup"><span data-stu-id="b9347-129">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="b9347-130">Apache</span><span class="sxs-lookup"><span data-stu-id="b9347-130">Apache</span></span>](xref:host-and-deploy/linux-apache)
* <span data-ttu-id="b9347-131">Windows</span><span class="sxs-lookup"><span data-stu-id="b9347-131">Windows</span></span>
  * [<span data-ttu-id="b9347-132">IIS</span><span class="sxs-lookup"><span data-stu-id="b9347-132">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="b9347-133">Windows 服務</span><span class="sxs-lookup"><span data-stu-id="b9347-133">Windows Service</span></span>](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="b9347-134">設定反向 Proxy</span><span class="sxs-lookup"><span data-stu-id="b9347-134">Set up a reverse proxy</span></span>

<span data-ttu-id="b9347-135">如果應用程式使用 [Kestrel](xref:fundamentals/servers/kestrel) 伺服器，反向 Proxy 伺服器可用 [Nginx](xref:host-and-deploy/linux-nginx)、[Apache](xref:host-and-deploy/linux-apache) 或 [IIS](xref:host-and-deploy/iis/index)。</span><span class="sxs-lookup"><span data-stu-id="b9347-135">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) can be used as a reverse proxy server.</span></span> <span data-ttu-id="b9347-136">反向 Proxy 伺服器會從網際網路接收 HTTP 要求，然後轉送到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="b9347-136">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

::: moniker-end 

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="b9347-137">不論設定是否具有反向 Proxy 伺服器，任一設定都是支援的裝載設定。</span><span class="sxs-lookup"><span data-stu-id="b9347-137">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="b9347-138">如需詳細資訊，請參閱[何時搭配使用 Kestrel 與反向 Proxy](xref:fundamentals/servers/kestrel/when-to-use-a-reverse-proxy)。</span><span class="sxs-lookup"><span data-stu-id="b9347-138">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel/when-to-use-a-reverse-proxy).</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-2.2 < aspnetcore-5.0"
<span data-ttu-id="b9347-139">不論設定是否具有反向 Proxy 伺服器，任一設定都是支援的裝載設定。</span><span class="sxs-lookup"><span data-stu-id="b9347-139">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="b9347-140">如需詳細資訊，請參閱[何時搭配使用 Kestrel 與反向 Proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy)。</span><span class="sxs-lookup"><span data-stu-id="b9347-140">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="b9347-141">Proxy 伺服器和負載平衡器案例</span><span class="sxs-lookup"><span data-stu-id="b9347-141">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="b9347-142">Proxy 伺服器和負載平衡器後方託管的應用程式可能需要其他設定。</span><span class="sxs-lookup"><span data-stu-id="b9347-142">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="b9347-143">若沒有其他設定，應用程式可能無法存取配置 (HTTP/HTTPS) 和發出要求的遠端 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="b9347-143">Without additional configuration, an app might not have access to the scheme (HTTP/HTTPS) and the remote IP address where a request originated.</span></span> <span data-ttu-id="b9347-144">如需詳細資訊，請參閱[設定 ASP.NET Core 以處理 Proxy 伺服器和負載平衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="b9347-144">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a><span data-ttu-id="b9347-145">使用 Visual Studio 和 MSBuild 來自動化部署</span><span class="sxs-lookup"><span data-stu-id="b9347-145">Use Visual Studio and MSBuild to automate deployments</span></span>

<span data-ttu-id="b9347-146">除了從 [dotnet publish](/dotnet/core/tools/dotnet-publish) 將輸出複製到伺服器之外，部署通常還需要額外的工作。</span><span class="sxs-lookup"><span data-stu-id="b9347-146">Deployment often requires additional tasks besides copying the output from [dotnet publish](/dotnet/core/tools/dotnet-publish) to a server.</span></span> <span data-ttu-id="b9347-147">例如，*publish* 資料夾可能需要或排除額外的檔案。</span><span class="sxs-lookup"><span data-stu-id="b9347-147">For example, extra files might be required or excluded from the *publish* folder.</span></span> <span data-ttu-id="b9347-148">Visual Studio 將 [msbuild](/visualstudio/msbuild/msbuild) 用於 web 部署，而且可以自訂 msbuild，在部署期間執行許多其他工作。</span><span class="sxs-lookup"><span data-stu-id="b9347-148">Visual Studio uses [MSBuild](/visualstudio/msbuild/msbuild) for web deployment, and MSBuild can be customized to do many other tasks during deployment.</span></span> <span data-ttu-id="b9347-149">如需詳細資訊，請參閱 <xref:host-and-deploy/visual-studio-publish-profiles>和[使用 MSBuild 和 Team Foundation Build](http://msbuildbook.com/) 書籍。</span><span class="sxs-lookup"><span data-stu-id="b9347-149">For more information, see <xref:host-and-deploy/visual-studio-publish-profiles> and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="b9347-150">使用[發行 Web 功能](xref:tutorials/publish-to-azure-webapp-using-vs)或使用[內建的 Git 支援](xref:host-and-deploy/azure-apps/azure-continuous-deployment)，應用程式可以直接從 Visual Studio 部署至 Azure App Service。</span><span class="sxs-lookup"><span data-stu-id="b9347-150">By using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or [built-in Git support](xref:host-and-deploy/azure-apps/azure-continuous-deployment), apps can be deployed directly from Visual Studio to the Azure App Service.</span></span> <span data-ttu-id="b9347-151">Azure DevOps Services 支援[持續部署至 Azure App Service](/azure/devops/pipelines/targets/webapp)。</span><span class="sxs-lookup"><span data-stu-id="b9347-151">Azure DevOps Services supports [continuous deployment to Azure App Service](/azure/devops/pipelines/targets/webapp).</span></span> <span data-ttu-id="b9347-152">如需詳細資訊，請參閱 [ASP.NET Core 與 Azure 的 DevOps](xref:azure/devops/index)。</span><span class="sxs-lookup"><span data-stu-id="b9347-152">For more information, see [DevOps with ASP.NET Core and Azure](xref:azure/devops/index).</span></span>

## <a name="publish-to-azure"></a><span data-ttu-id="b9347-153">發佈至 Azure</span><span class="sxs-lookup"><span data-stu-id="b9347-153">Publish to Azure</span></span>

<span data-ttu-id="b9347-154">請參閱 <xref:tutorials/publish-to-azure-webapp-using-vs> 以取得有關如何使用 Visual Studio 將應用程式發佈到 Azure 的指示。</span><span class="sxs-lookup"><span data-stu-id="b9347-154">See <xref:tutorials/publish-to-azure-webapp-using-vs> for instructions on how to publish an app to Azure using Visual Studio.</span></span> <span data-ttu-id="b9347-155">[在 Azure 中建立 ASP.NET Core Web 應用程式](/azure/app-service/app-service-web-get-started-dotnet)提供其他範例。</span><span class="sxs-lookup"><span data-stu-id="b9347-155">An additional example is provided by [Create an ASP.NET Core web app in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

## <a name="publish-with-msdeploy-on-windows"></a><span data-ttu-id="b9347-156">在 Windows 上使用 MSDeploy 來發行</span><span class="sxs-lookup"><span data-stu-id="b9347-156">Publish with MSDeploy on Windows</span></span>

<span data-ttu-id="b9347-157">請參閱 <xref:host-and-deploy/visual-studio-publish-profiles> 以取得有關如何使用 Visual Studio 發行設定檔發行應用程式的指示，包括從 Windows 命令提示字元使用 [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) 命令。</span><span class="sxs-lookup"><span data-stu-id="b9347-157">See <xref:host-and-deploy/visual-studio-publish-profiles> for instructions on how to publish an app with a Visual Studio publish profile, including from a Windows command prompt using the [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command.</span></span>

## <a name="internet-information-services-iis"></a><span data-ttu-id="b9347-158">網際網路資訊服務 (IIS)</span><span class="sxs-lookup"><span data-stu-id="b9347-158">Internet Information Services (IIS)</span></span>

<span data-ttu-id="b9347-159">若要使用 *web.config* 檔案所提供的設定來 INTERNET INFORMATION SERVICES (IIS) 的部署，請參閱底下的文章 <xref:host-and-deploy/iis/index> 。</span><span class="sxs-lookup"><span data-stu-id="b9347-159">For deployments to Internet Information Services (IIS) with configuration provided by the *web.config* file, see the articles under <xref:host-and-deploy/iis/index>.</span></span>

## <a name="host-in-a-web-farm"></a><span data-ttu-id="b9347-160">裝載於 Web 伺服陣列</span><span class="sxs-lookup"><span data-stu-id="b9347-160">Host in a web farm</span></span>

<span data-ttu-id="b9347-161">如需在 Web 伺服陣列環境中裝載 ASP.NET Core 應用程式的設定資訊 (例如部署應用程式的多個執行個體以獲得調整能力)，請參閱 <xref:host-and-deploy/web-farm>。</span><span class="sxs-lookup"><span data-stu-id="b9347-161">For information on configuration for hosting ASP.NET Core apps in a web farm environment (for example, deployment of multiple instances of your app for scalability), see <xref:host-and-deploy/web-farm>.</span></span>

## <a name="host-on-docker"></a><span data-ttu-id="b9347-162">Docker 上的主機</span><span class="sxs-lookup"><span data-stu-id="b9347-162">Host on Docker</span></span>

<span data-ttu-id="b9347-163">如需詳細資訊，請參閱<xref:host-and-deploy/docker/index>。</span><span class="sxs-lookup"><span data-stu-id="b9347-163">For more information, see <xref:host-and-deploy/docker/index>.</span></span>

## <a name="perform-health-checks"></a><span data-ttu-id="b9347-164">執行健康狀態檢查</span><span class="sxs-lookup"><span data-stu-id="b9347-164">Perform health checks</span></span>

<span data-ttu-id="b9347-165">您可以使用健康狀態檢查中介軟體，對應用程式及其相依性執行健康狀態檢查。</span><span class="sxs-lookup"><span data-stu-id="b9347-165">Use Health Check Middleware to perform health checks on an app and its dependencies.</span></span> <span data-ttu-id="b9347-166">如需詳細資訊，請參閱<xref:host-and-deploy/health-checks>。</span><span class="sxs-lookup"><span data-stu-id="b9347-166">For more information, see <xref:host-and-deploy/health-checks>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b9347-167">其他資源</span><span class="sxs-lookup"><span data-stu-id="b9347-167">Additional resources</span></span>

* <xref:test/troubleshoot>
* [<span data-ttu-id="b9347-168">ASP.NET 裝載</span><span class="sxs-lookup"><span data-stu-id="b9347-168">ASP.NET Hosting</span></span>](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="b9347-169">一般是將 ASP.NET Core 應用程式部署到裝載環境：</span><span class="sxs-lookup"><span data-stu-id="b9347-169">In general, to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="b9347-170">將已發行的電子郵件部署到裝載伺服器上的資料夾。</span><span class="sxs-lookup"><span data-stu-id="b9347-170">Deploy the published app to a folder on the hosting server.</span></span>
* <span data-ttu-id="b9347-171">設定處理序管理員，以在要求到達時啟動應用程式，並在其損毀或伺服器重新開機後重新啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="b9347-171">Set up a process manager that starts the app when requests arrive and restarts the app after it crashes or the server reboots.</span></span>
* <span data-ttu-id="b9347-172">至於反向 Proxy 的設定，請對反向 Proxy 進行設定，使其將要求轉送到應用程式。</span><span class="sxs-lookup"><span data-stu-id="b9347-172">For configuration of a reverse proxy, set up a reverse proxy to forward requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="b9347-173">發行至資料夾</span><span class="sxs-lookup"><span data-stu-id="b9347-173">Publish to a folder</span></span>

<span data-ttu-id="b9347-174">[dotnet publish](/dotnet/core/tools/dotnet-publish) 命令會編譯應用程式程式碼，並將執行應用程式所需的檔案複製到 *publish* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="b9347-174">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command compiles app code and copies the files required to run the app into a *publish* folder.</span></span> <span data-ttu-id="b9347-175">從 Visual Studio 部署時，`dotnet publish` 步驟會在檔案複製到部署目的地之前自動執行。</span><span class="sxs-lookup"><span data-stu-id="b9347-175">When deploying from Visual Studio, the `dotnet publish` step occurs automatically before the files are copied to the deployment destination.</span></span>

### <a name="folder-contents"></a><span data-ttu-id="b9347-176">資料夾內容</span><span class="sxs-lookup"><span data-stu-id="b9347-176">Folder contents</span></span>

<span data-ttu-id="b9347-177">*publish* 資料夾包含一或多個應用程式組件檔、相依性，也可能會有 .NET 執行階段。</span><span class="sxs-lookup"><span data-stu-id="b9347-177">The *publish* folder contains one or more app assembly files, dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="b9347-178">.NET Core 應用程式可以發行為「自主式部署」或「相依於架構的部署」。</span><span class="sxs-lookup"><span data-stu-id="b9347-178">A .NET Core app can be published as *self-contained deployment* or *framework-dependent deployment*.</span></span> <span data-ttu-id="b9347-179">如果應用程式是自主式，包含 .NET 執行階段的組件檔會包含在 *publish* 資料夾中。</span><span class="sxs-lookup"><span data-stu-id="b9347-179">If the app is self-contained, the assembly files that contain the .NET runtime are included in the *publish* folder.</span></span> <span data-ttu-id="b9347-180">如果應用程式是與 Framework 相依的應用程式，則不會包含 .NET 執行階段檔案，因為應用程式具有對伺服器上已安裝之 .NET 版本的參考。</span><span class="sxs-lookup"><span data-stu-id="b9347-180">If the app is framework-dependent, the .NET runtime files aren't included because the app has a reference to a version of .NET that's installed on the server.</span></span> <span data-ttu-id="b9347-181">預設部署模式是與 Framework 相依。</span><span class="sxs-lookup"><span data-stu-id="b9347-181">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="b9347-182">如需詳細資訊，請參閱 [.NET Core 應用程式部署](/dotnet/core/deploying/)。</span><span class="sxs-lookup"><span data-stu-id="b9347-182">For more information, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

<span data-ttu-id="b9347-183">除了 *.exe* 和 *.dll* 檔案之外，ASP.NET Core 應用程式的 *publish* 資料夾通常還包含組態檔、靜態資產和 MVC 檢視。</span><span class="sxs-lookup"><span data-stu-id="b9347-183">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span> <span data-ttu-id="b9347-184">如需詳細資訊，請參閱<xref:host-and-deploy/directory-structure>。</span><span class="sxs-lookup"><span data-stu-id="b9347-184">For more information, see <xref:host-and-deploy/directory-structure>.</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="b9347-185">設定處理序管理員</span><span class="sxs-lookup"><span data-stu-id="b9347-185">Set up a process manager</span></span>

<span data-ttu-id="b9347-186">ASP.NET Core 應用程式是一種主控台應用程式，必須在伺服器開機和損毀後重新啟動。</span><span class="sxs-lookup"><span data-stu-id="b9347-186">An ASP.NET Core app is a console app that must be started when a server boots and restarted if it crashes.</span></span> <span data-ttu-id="b9347-187">若要自動化啟動和重新啟動，需要有處理序管理員。</span><span class="sxs-lookup"><span data-stu-id="b9347-187">To automate starts and restarts, a process manager is required.</span></span> <span data-ttu-id="b9347-188">ASP.NET Core 最常見的處理序管理員是：</span><span class="sxs-lookup"><span data-stu-id="b9347-188">The most common process managers for ASP.NET Core are:</span></span>

* <span data-ttu-id="b9347-189">Linux</span><span class="sxs-lookup"><span data-stu-id="b9347-189">Linux</span></span>
  * [<span data-ttu-id="b9347-190">Nginx</span><span class="sxs-lookup"><span data-stu-id="b9347-190">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="b9347-191">Apache</span><span class="sxs-lookup"><span data-stu-id="b9347-191">Apache</span></span>](xref:host-and-deploy/linux-apache)
* <span data-ttu-id="b9347-192">Windows</span><span class="sxs-lookup"><span data-stu-id="b9347-192">Windows</span></span>
  * [<span data-ttu-id="b9347-193">IIS</span><span class="sxs-lookup"><span data-stu-id="b9347-193">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="b9347-194">Windows 服務</span><span class="sxs-lookup"><span data-stu-id="b9347-194">Windows Service</span></span>](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="b9347-195">設定反向 Proxy</span><span class="sxs-lookup"><span data-stu-id="b9347-195">Set up a reverse proxy</span></span>

<span data-ttu-id="b9347-196">如果應用程式使用 [Kestrel](xref:fundamentals/servers/kestrel) 伺服器，反向 Proxy 伺服器可用 [Nginx](xref:host-and-deploy/linux-nginx)、[Apache](xref:host-and-deploy/linux-apache) 或 [IIS](xref:host-and-deploy/iis/index)。</span><span class="sxs-lookup"><span data-stu-id="b9347-196">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) can be used as a reverse proxy server.</span></span> <span data-ttu-id="b9347-197">反向 Proxy 伺服器會從網際網路接收 HTTP 要求，然後轉送到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="b9347-197">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

<span data-ttu-id="b9347-198">不論設定是否具有反向 Proxy 伺服器，任一設定都是支援的裝載設定。</span><span class="sxs-lookup"><span data-stu-id="b9347-198">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="b9347-199">如需詳細資訊，請參閱[何時搭配使用 Kestrel 與反向 Proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy)。</span><span class="sxs-lookup"><span data-stu-id="b9347-199">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="b9347-200">Proxy 伺服器和負載平衡器案例</span><span class="sxs-lookup"><span data-stu-id="b9347-200">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="b9347-201">Proxy 伺服器和負載平衡器後方託管的應用程式可能需要其他設定。</span><span class="sxs-lookup"><span data-stu-id="b9347-201">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="b9347-202">若沒有其他設定，應用程式可能無法存取配置 (HTTP/HTTPS) 和發出要求的遠端 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="b9347-202">Without additional configuration, an app might not have access to the scheme (HTTP/HTTPS) and the remote IP address where a request originated.</span></span> <span data-ttu-id="b9347-203">如需詳細資訊，請參閱[設定 ASP.NET Core 以處理 Proxy 伺服器和負載平衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="b9347-203">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a><span data-ttu-id="b9347-204">使用 Visual Studio 和 MSBuild 來自動化部署</span><span class="sxs-lookup"><span data-stu-id="b9347-204">Use Visual Studio and MSBuild to automate deployments</span></span>

<span data-ttu-id="b9347-205">除了從 [dotnet publish](/dotnet/core/tools/dotnet-publish) 將輸出複製到伺服器之外，部署通常還需要額外的工作。</span><span class="sxs-lookup"><span data-stu-id="b9347-205">Deployment often requires additional tasks besides copying the output from [dotnet publish](/dotnet/core/tools/dotnet-publish) to a server.</span></span> <span data-ttu-id="b9347-206">例如，*publish* 資料夾可能需要或排除額外的檔案。</span><span class="sxs-lookup"><span data-stu-id="b9347-206">For example, extra files might be required or excluded from the *publish* folder.</span></span> <span data-ttu-id="b9347-207">Visual Studio 會將 MSBuild 用於 Web 部署，而且您可以自訂 MSBuild 在部署期間執行許多其他工作。</span><span class="sxs-lookup"><span data-stu-id="b9347-207">Visual Studio uses MSBuild for web deployment, and MSBuild can be customized to do many other tasks during deployment.</span></span> <span data-ttu-id="b9347-208">如需詳細資訊，請參閱 <xref:host-and-deploy/visual-studio-publish-profiles>和[使用 MSBuild 和 Team Foundation Build](http://msbuildbook.com/) 書籍。</span><span class="sxs-lookup"><span data-stu-id="b9347-208">For more information, see <xref:host-and-deploy/visual-studio-publish-profiles> and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="b9347-209">使用[發行 Web 功能](xref:tutorials/publish-to-azure-webapp-using-vs)或使用[內建的 Git 支援](xref:host-and-deploy/azure-apps/azure-continuous-deployment)，應用程式可以直接從 Visual Studio 部署至 Azure App Service。</span><span class="sxs-lookup"><span data-stu-id="b9347-209">By using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or [built-in Git support](xref:host-and-deploy/azure-apps/azure-continuous-deployment), apps can be deployed directly from Visual Studio to the Azure App Service.</span></span> <span data-ttu-id="b9347-210">Azure DevOps Services 支援[持續部署至 Azure App Service](/azure/devops/pipelines/targets/webapp)。</span><span class="sxs-lookup"><span data-stu-id="b9347-210">Azure DevOps Services supports [continuous deployment to Azure App Service](/azure/devops/pipelines/targets/webapp).</span></span> <span data-ttu-id="b9347-211">如需詳細資訊，請參閱 [ASP.NET Core 與 Azure 的 DevOps](xref:azure/devops/index)。</span><span class="sxs-lookup"><span data-stu-id="b9347-211">For more information, see [DevOps with ASP.NET Core and Azure](xref:azure/devops/index).</span></span>

## <a name="publish-to-azure"></a><span data-ttu-id="b9347-212">發佈至 Azure</span><span class="sxs-lookup"><span data-stu-id="b9347-212">Publish to Azure</span></span>

<span data-ttu-id="b9347-213">請參閱 <xref:tutorials/publish-to-azure-webapp-using-vs> 以取得有關如何使用 Visual Studio 將應用程式發佈到 Azure 的指示。</span><span class="sxs-lookup"><span data-stu-id="b9347-213">See <xref:tutorials/publish-to-azure-webapp-using-vs> for instructions on how to publish an app to Azure using Visual Studio.</span></span> <span data-ttu-id="b9347-214">[在 Azure 中建立 ASP.NET Core Web 應用程式](/azure/app-service/app-service-web-get-started-dotnet)提供其他範例。</span><span class="sxs-lookup"><span data-stu-id="b9347-214">An additional example is provided by [Create an ASP.NET Core web app in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

## <a name="publish-with-msdeploy-on-windows"></a><span data-ttu-id="b9347-215">在 Windows 上使用 MSDeploy 來發行</span><span class="sxs-lookup"><span data-stu-id="b9347-215">Publish with MSDeploy on Windows</span></span>

<span data-ttu-id="b9347-216">請參閱 <xref:host-and-deploy/visual-studio-publish-profiles> 以取得有關如何使用 Visual Studio 發行設定檔發行應用程式的指示，包括從 Windows 命令提示字元使用 [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) 命令。</span><span class="sxs-lookup"><span data-stu-id="b9347-216">See <xref:host-and-deploy/visual-studio-publish-profiles> for instructions on how to publish an app with a Visual Studio publish profile, including from a Windows command prompt using the [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command.</span></span>

## <a name="internet-information-services-iis"></a><span data-ttu-id="b9347-217">網際網路資訊服務 (IIS)</span><span class="sxs-lookup"><span data-stu-id="b9347-217">Internet Information Services (IIS)</span></span>

<span data-ttu-id="b9347-218">若要使用 *web.config* 檔案所提供的設定來 INTERNET INFORMATION SERVICES (IIS) 的部署，請參閱底下的文章 <xref:host-and-deploy/iis/index> 。</span><span class="sxs-lookup"><span data-stu-id="b9347-218">For deployments to Internet Information Services (IIS) with configuration provided by the *web.config* file, see the articles under <xref:host-and-deploy/iis/index>.</span></span>

## <a name="host-in-a-web-farm"></a><span data-ttu-id="b9347-219">裝載於 Web 伺服陣列</span><span class="sxs-lookup"><span data-stu-id="b9347-219">Host in a web farm</span></span>

<span data-ttu-id="b9347-220">如需在 Web 伺服陣列環境中裝載 ASP.NET Core 應用程式的設定資訊 (例如部署應用程式的多個執行個體以獲得調整能力)，請參閱 <xref:host-and-deploy/web-farm>。</span><span class="sxs-lookup"><span data-stu-id="b9347-220">For information on configuration for hosting ASP.NET Core apps in a web farm environment (for example, deployment of multiple instances of your app for scalability), see <xref:host-and-deploy/web-farm>.</span></span>

## <a name="host-on-docker"></a><span data-ttu-id="b9347-221">Docker 上的主機</span><span class="sxs-lookup"><span data-stu-id="b9347-221">Host on Docker</span></span>

<span data-ttu-id="b9347-222">如需詳細資訊，請參閱<xref:host-and-deploy/docker/index>。</span><span class="sxs-lookup"><span data-stu-id="b9347-222">For more information, see <xref:host-and-deploy/docker/index>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b9347-223">其他資源</span><span class="sxs-lookup"><span data-stu-id="b9347-223">Additional resources</span></span>

* <xref:test/troubleshoot>
* [<span data-ttu-id="b9347-224">ASP.NET 裝載</span><span class="sxs-lookup"><span data-stu-id="b9347-224">ASP.NET Hosting</span></span>](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end
