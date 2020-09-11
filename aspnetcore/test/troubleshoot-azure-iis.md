---
title: 針對 Azure App Service 和 IIS 上的 ASP.NET Core 進行疑難排解
author: rick-anderson
description: 瞭解如何診斷 ASP.NET Core 應用程式 (IIS) 部署 Azure App Service 和 Internet Information Services 的問題。
monikerRange: '>= aspnetcore-2.1'
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
uid: test/troubleshoot-azure-iis
ms.openlocfilehash: bc968489a35405c1be0a4ac2e52e6dff4c3af050
ms.sourcegitcommit: 8fcb08312a59c37e3542e7a67dad25faf5bb8e76
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2020
ms.locfileid: "90009735"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service-and-iis"></a><span data-ttu-id="fc318-103">針對 Azure App Service 和 IIS 上的 ASP.NET Core 進行疑難排解</span><span class="sxs-lookup"><span data-stu-id="fc318-103">Troubleshoot ASP.NET Core on Azure App Service and IIS</span></span>

<span data-ttu-id="fc318-104">作者：[Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="fc318-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fc318-105">本文提供有關一般應用程式啟動錯誤的資訊，以及如何在將應用程式部署到 Azure App Service 或 IIS 時診斷錯誤的指示：</span><span class="sxs-lookup"><span data-stu-id="fc318-105">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="fc318-106">應用程式啟動錯誤</span><span class="sxs-lookup"><span data-stu-id="fc318-106">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="fc318-107">說明常見的啟動 HTTP 狀態碼案例。</span><span class="sxs-lookup"><span data-stu-id="fc318-107">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="fc318-108">針對 Azure App Service 進行疑難排解</span><span class="sxs-lookup"><span data-stu-id="fc318-108">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="fc318-109">針對部署至 Azure App Service 的應用程式提供疑難排解建議。</span><span class="sxs-lookup"><span data-stu-id="fc318-109">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="fc318-110">針對 IIS 進行疑難排解</span><span class="sxs-lookup"><span data-stu-id="fc318-110">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="fc318-111">針對部署至 IIS 或在本機 IIS Express 上執行的應用程式，提供疑難排解建議。</span><span class="sxs-lookup"><span data-stu-id="fc318-111">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="fc318-112">本指南適用于 Windows Server 和 Windows 桌面部署。</span><span class="sxs-lookup"><span data-stu-id="fc318-112">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="fc318-113">清除套件快取</span><span class="sxs-lookup"><span data-stu-id="fc318-113">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="fc318-114">說明當一致套件在執行主要升級或變更套件版本時，會中斷應用程式時，該怎麼辦。</span><span class="sxs-lookup"><span data-stu-id="fc318-114">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="fc318-115">其他資源</span><span class="sxs-lookup"><span data-stu-id="fc318-115">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="fc318-116">列出其他疑難排解主題。</span><span class="sxs-lookup"><span data-stu-id="fc318-116">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="fc318-117">應用程式啟動錯誤</span><span class="sxs-lookup"><span data-stu-id="fc318-117">App startup errors</span></span>

<span data-ttu-id="fc318-118">在 Visual Studio 中，ASP.NET Core 專案預設為在偵錯工具期間 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 裝載。</span><span class="sxs-lookup"><span data-stu-id="fc318-118">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="fc318-119">您可以使用本主題中的建議來診斷在本機進行偵錯工具時，發生 *502.5-進程失敗* 或 *500.30-啟動失敗* 。</span><span class="sxs-lookup"><span data-stu-id="fc318-119">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="fc318-120">403.14 禁止</span><span class="sxs-lookup"><span data-stu-id="fc318-120">403.14 Forbidden</span></span>

<span data-ttu-id="fc318-121">應用程式無法啟動。</span><span class="sxs-lookup"><span data-stu-id="fc318-121">The app fails to start.</span></span> <span data-ttu-id="fc318-122">會記錄下列錯誤：</span><span class="sxs-lookup"><span data-stu-id="fc318-122">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="fc318-123">此錯誤通常是因為裝載系統上的部署中斷所造成，包括下列任一案例：</span><span class="sxs-lookup"><span data-stu-id="fc318-123">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="fc318-124">應用程式會部署到裝載系統上的錯誤資料夾。</span><span class="sxs-lookup"><span data-stu-id="fc318-124">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="fc318-125">部署程式無法將所有應用程式的檔案和資料夾移至裝載系統上的部署資料夾。</span><span class="sxs-lookup"><span data-stu-id="fc318-125">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="fc318-126">部署中缺少 *web.config* 檔案，或 *web.config* 的檔案內容格式不正確。</span><span class="sxs-lookup"><span data-stu-id="fc318-126">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="fc318-127">請執行下列步驟：</span><span class="sxs-lookup"><span data-stu-id="fc318-127">Perform the following steps:</span></span>

1. <span data-ttu-id="fc318-128">從裝載系統上的 [部署] 資料夾中刪除所有檔案和資料夾。</span><span class="sxs-lookup"><span data-stu-id="fc318-128">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="fc318-129">使用您的一般部署方法（例如 Visual Studio、PowerShell 或手動部署），將應用程式的 *發行* 資料夾內容重新部署至主機系統：</span><span class="sxs-lookup"><span data-stu-id="fc318-129">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="fc318-130">確認部署中有 *web.config* 檔案，且其內容正確無誤。</span><span class="sxs-lookup"><span data-stu-id="fc318-130">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="fc318-131">在 Azure App Service 上裝載時，請確認應用程式已部署到 `D:\home\site\wwwroot` 資料夾。</span><span class="sxs-lookup"><span data-stu-id="fc318-131">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="fc318-132">當應用程式是由 IIS 主控時，請確認應用程式已部署至 iis**管理員\*\*\*\*基本設定**中顯示的 iis**實體路徑**。</span><span class="sxs-lookup"><span data-stu-id="fc318-132">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="fc318-133">藉由比較主機系統上的部署與專案 *發佈* 資料夾的內容，確認已部署所有應用程式的檔案和資料夾。</span><span class="sxs-lookup"><span data-stu-id="fc318-133">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="fc318-134">如需已發佈的 ASP.NET Core 應用程式佈建的詳細資訊，請參閱 <xref:host-and-deploy/directory-structure> 。</span><span class="sxs-lookup"><span data-stu-id="fc318-134">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="fc318-135">如需 *web.config* 檔案的詳細資訊，請參閱 <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig> 。</span><span class="sxs-lookup"><span data-stu-id="fc318-135">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="fc318-136">500 內部伺服器錯誤</span><span class="sxs-lookup"><span data-stu-id="fc318-136">500 Internal Server Error</span></span>

<span data-ttu-id="fc318-137">應用程式啟動，但有錯誤導致伺服器無法完成要求。</span><span class="sxs-lookup"><span data-stu-id="fc318-137">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="fc318-138">此錯誤是在啟動或建立回應時，在應用程式的程式碼內發生。</span><span class="sxs-lookup"><span data-stu-id="fc318-138">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="fc318-139">回應可能未包含任何內容，或是回應可能在瀏覽器中以「500 內部伺服器錯誤」\*\* 的形式出現。</span><span class="sxs-lookup"><span data-stu-id="fc318-139">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="fc318-140">「應用程式事件記錄檔」通常會指出該應用程式已正常啟動。</span><span class="sxs-lookup"><span data-stu-id="fc318-140">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="fc318-141">從伺服器的觀點來看，這是正確的。</span><span class="sxs-lookup"><span data-stu-id="fc318-141">From the server's perspective, that's correct.</span></span> <span data-ttu-id="fc318-142">應用程式已啟動，但無法產生有效的回應。</span><span class="sxs-lookup"><span data-stu-id="fc318-142">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="fc318-143">請在伺服器上於命令提示字元中執行應用程式或啟用 ASP.NET Core 模組 stdout 記錄檔，以針對問題進行疑難排解。</span><span class="sxs-lookup"><span data-stu-id="fc318-143">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="fc318-144">500.0 同處理序處理常式載入失敗</span><span class="sxs-lookup"><span data-stu-id="fc318-144">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="fc318-145">背景工作處理序失敗。</span><span class="sxs-lookup"><span data-stu-id="fc318-145">The worker process fails.</span></span> <span data-ttu-id="fc318-146">應用程式未啟動。</span><span class="sxs-lookup"><span data-stu-id="fc318-146">The app doesn't start.</span></span>

<span data-ttu-id="fc318-147">載入 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module) 元件時發生未知的錯誤。</span><span class="sxs-lookup"><span data-stu-id="fc318-147">An unknown error occurred loading [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) components.</span></span> <span data-ttu-id="fc318-148">請採取下列其中一個動作：</span><span class="sxs-lookup"><span data-stu-id="fc318-148">Take one of the following actions:</span></span>

* <span data-ttu-id="fc318-149">連絡 [Microsoft 支援服務](https://support.microsoft.com/oas/default.aspx?prid=15832) (依序選取 [開發人員工具]\*\*\*\* 和 [ASP.NET Core]\*\*\*\*)。</span><span class="sxs-lookup"><span data-stu-id="fc318-149">Contact [Microsoft Support](https://support.microsoft.com/oas/default.aspx?prid=15832) (select **Developer Tools** then **ASP.NET Core**).</span></span>
* <span data-ttu-id="fc318-150">在 Stack Overflow 上詢問問題。</span><span class="sxs-lookup"><span data-stu-id="fc318-150">Ask a question on Stack Overflow.</span></span>
* <span data-ttu-id="fc318-151">在我們的 [GitHub 存放庫](https://github.com/dotnet/AspNetCore)提出問題。</span><span class="sxs-lookup"><span data-stu-id="fc318-151">File an issue on our [GitHub repository](https://github.com/dotnet/AspNetCore).</span></span>

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="fc318-152">500.30 同處理序啟動失敗</span><span class="sxs-lookup"><span data-stu-id="fc318-152">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="fc318-153">背景工作處理序失敗。</span><span class="sxs-lookup"><span data-stu-id="fc318-153">The worker process fails.</span></span> <span data-ttu-id="fc318-154">應用程式未啟動。</span><span class="sxs-lookup"><span data-stu-id="fc318-154">The app doesn't start.</span></span>

<span data-ttu-id="fc318-155">[ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)嘗試啟動 .NET Core CLR 同進程，但無法啟動。</span><span class="sxs-lookup"><span data-stu-id="fc318-155">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core CLR in-process, but it fails to start.</span></span> <span data-ttu-id="fc318-156">通常從應用程式事件記錄檔和 ASP.NET Core 模組 stdout 記錄檔中的項目，即可判斷啟動失敗的原因。</span><span class="sxs-lookup"><span data-stu-id="fc318-156">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="fc318-157">常見的失敗狀況：</span><span class="sxs-lookup"><span data-stu-id="fc318-157">Common failure conditions:</span></span>

* <span data-ttu-id="fc318-158">因為目標 ASP.NET Core 共用架構的版本不存在，所以應用程式設定不正確。</span><span class="sxs-lookup"><span data-stu-id="fc318-158">The app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="fc318-159">請檢查安裝在目標機器上的 ASP.NET Core 共用架構版本為何。</span><span class="sxs-lookup"><span data-stu-id="fc318-159">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span>
* <span data-ttu-id="fc318-160">使用 Azure Key Vault，缺少 Key Vault 的許可權。</span><span class="sxs-lookup"><span data-stu-id="fc318-160">Using Azure Key Vault, lack of permissions to the Key Vault.</span></span> <span data-ttu-id="fc318-161">檢查目標 Key Vault 中的存取原則，以確保已授與正確的許可權。</span><span class="sxs-lookup"><span data-stu-id="fc318-161">Check the access policies in the targeted Key Vault to ensure that the correct permissions are granted.</span></span>

### <a name="50031-ancm-failed-to-find-native-dependencies"></a><span data-ttu-id="fc318-162">500.31 ANCM 找不到原生相依性</span><span class="sxs-lookup"><span data-stu-id="fc318-162">500.31 ANCM Failed to Find Native Dependencies</span></span>

<span data-ttu-id="fc318-163">背景工作處理序失敗。</span><span class="sxs-lookup"><span data-stu-id="fc318-163">The worker process fails.</span></span> <span data-ttu-id="fc318-164">應用程式未啟動。</span><span class="sxs-lookup"><span data-stu-id="fc318-164">The app doesn't start.</span></span>

<span data-ttu-id="fc318-165">[ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)嘗試啟動 .net Core 執行時間同進程，但無法啟動。</span><span class="sxs-lookup"><span data-stu-id="fc318-165">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core runtime in-process, but it fails to start.</span></span> <span data-ttu-id="fc318-166">此啟動失敗的最常見原因是當 `Microsoft.NETCore.App` 或 `Microsoft.AspNetCore.App` 執行階段未安裝時。</span><span class="sxs-lookup"><span data-stu-id="fc318-166">The most common cause of this startup failure is when the `Microsoft.NETCore.App` or `Microsoft.AspNetCore.App` runtime isn't installed.</span></span> <span data-ttu-id="fc318-167">如果應用程式部署至目標 ASP.NET Core 3.0，但電腦上無該版本，就會發生此錯誤。</span><span class="sxs-lookup"><span data-stu-id="fc318-167">If the app is deployed to target ASP.NET Core 3.0 and that version doesn't exist on the machine, this error occurs.</span></span> <span data-ttu-id="fc318-168">範例錯誤訊息如下：</span><span class="sxs-lookup"><span data-stu-id="fc318-168">An example error message follows:</span></span>

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

<span data-ttu-id="fc318-169">錯誤訊息會列出所有已安裝 .NET Core 版本和應用程式所要求的版本。</span><span class="sxs-lookup"><span data-stu-id="fc318-169">The error message lists all the installed .NET Core versions and the version requested by the app.</span></span> <span data-ttu-id="fc318-170">若要修正此錯誤，請使用以下其中一種方法：</span><span class="sxs-lookup"><span data-stu-id="fc318-170">To fix this error, either:</span></span>

* <span data-ttu-id="fc318-171">在電腦上安裝適當的 .NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="fc318-171">Install the appropriate version of .NET Core on the machine.</span></span>
* <span data-ttu-id="fc318-172">將應用程式的目標 .NET Core 版本變更為電腦上版本。</span><span class="sxs-lookup"><span data-stu-id="fc318-172">Change the app to target a version of .NET Core that's present on the machine.</span></span>
* <span data-ttu-id="fc318-173">將應用程式發佈為[自封式部署](/dotnet/core/deploying/#self-contained-deployments-scd)。</span><span class="sxs-lookup"><span data-stu-id="fc318-173">Publish the app as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

<span data-ttu-id="fc318-174">在開發過程中執行時 (`ASPNETCORE_ENVIRONMENT` 環境變數設定為 `Development`)，特定的錯誤會寫入至 HTTP 回應。</span><span class="sxs-lookup"><span data-stu-id="fc318-174">When running in development (the `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development`), the specific error is written to the HTTP response.</span></span> <span data-ttu-id="fc318-175">處理序啟動失敗的原因也會列在應用程式事件記錄檔中。</span><span class="sxs-lookup"><span data-stu-id="fc318-175">The cause of a process startup failure is also found in the Application Event Log.</span></span>

### <a name="50032-ancm-failed-to-load-dll"></a><span data-ttu-id="fc318-176">500.32 ANCM 無法載入 dll</span><span class="sxs-lookup"><span data-stu-id="fc318-176">500.32 ANCM Failed to Load dll</span></span>

<span data-ttu-id="fc318-177">背景工作處理序失敗。</span><span class="sxs-lookup"><span data-stu-id="fc318-177">The worker process fails.</span></span> <span data-ttu-id="fc318-178">應用程式未啟動。</span><span class="sxs-lookup"><span data-stu-id="fc318-178">The app doesn't start.</span></span>

<span data-ttu-id="fc318-179">此錯誤最常見原因是針對不相容的處理器架構發佈應用程式。</span><span class="sxs-lookup"><span data-stu-id="fc318-179">The most common cause for this error is that the app is published for an incompatible processor architecture.</span></span> <span data-ttu-id="fc318-180">如果背景工作處理序執行為 32 位元應用程式，而此應用程式已發佈至目標 64 位元，就會發生此錯誤。</span><span class="sxs-lookup"><span data-stu-id="fc318-180">If the worker process is running as a 32-bit app and the app was published to target 64-bit, this error occurs.</span></span>

<span data-ttu-id="fc318-181">若要修正此錯誤，請使用以下其中一種方法：</span><span class="sxs-lookup"><span data-stu-id="fc318-181">To fix this error, either:</span></span>

* <span data-ttu-id="fc318-182">針對相同的處理器架構，將應用程式重新發佈為背景工作處理序。</span><span class="sxs-lookup"><span data-stu-id="fc318-182">Republish the app for the same processor architecture as the worker process.</span></span>
* <span data-ttu-id="fc318-183">將應用程式發佈為[架構相依部署](/dotnet/core/deploying/#framework-dependent-executables-fde)。</span><span class="sxs-lookup"><span data-stu-id="fc318-183">Publish the app as a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-executables-fde).</span></span>

### <a name="50033-ancm-request-handler-load-failure"></a><span data-ttu-id="fc318-184">500.33 ANCM 要求處理常式載入失敗</span><span class="sxs-lookup"><span data-stu-id="fc318-184">500.33 ANCM Request Handler Load Failure</span></span>

<span data-ttu-id="fc318-185">背景工作處理序失敗。</span><span class="sxs-lookup"><span data-stu-id="fc318-185">The worker process fails.</span></span> <span data-ttu-id="fc318-186">應用程式未啟動。</span><span class="sxs-lookup"><span data-stu-id="fc318-186">The app doesn't start.</span></span>

<span data-ttu-id="fc318-187">應用程式未參考 `Microsoft.AspNetCore.App` 架構。</span><span class="sxs-lookup"><span data-stu-id="fc318-187">The app didn't reference the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="fc318-188">只有以架構為目標的應用程式 `Microsoft.AspNetCore.App` 可以由 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)裝載。</span><span class="sxs-lookup"><span data-stu-id="fc318-188">Only apps targeting the `Microsoft.AspNetCore.App` framework can be hosted by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="fc318-189">若要修正這個錯誤，請確認應用程式以 `Microsoft.AspNetCore.App` 架構為目標。</span><span class="sxs-lookup"><span data-stu-id="fc318-189">To fix this error, confirm that the app is targeting the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="fc318-190">檢查 `.runtimeconfig.json` 以驗證應用程式是否以該架構為目標。</span><span class="sxs-lookup"><span data-stu-id="fc318-190">Check the `.runtimeconfig.json` to verify the framework targeted by the app.</span></span>

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a><span data-ttu-id="fc318-191">500.34 ANCM 不支援混合式裝載模型</span><span class="sxs-lookup"><span data-stu-id="fc318-191">500.34 ANCM Mixed Hosting Models Not Supported</span></span>

<span data-ttu-id="fc318-192">背景工作處理序無法在相同的程序中執行同處理序應用程式和跨處理序應用程式。</span><span class="sxs-lookup"><span data-stu-id="fc318-192">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="fc318-193">若要修正這個錯誤，請在不同的 IIS 應用程式集區中執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="fc318-193">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a><span data-ttu-id="fc318-194">500.35 ANCM 同一程序中有多個同處理序應用程式</span><span class="sxs-lookup"><span data-stu-id="fc318-194">500.35 ANCM Multiple In-Process Applications in same Process</span></span>

<span data-ttu-id="fc318-195">背景工作進程無法在相同的進程中執行多個同進程應用程式。</span><span class="sxs-lookup"><span data-stu-id="fc318-195">The worker process can't run multiple in-process apps in the same process.</span></span>

<span data-ttu-id="fc318-196">若要修正這個錯誤，請在不同的 IIS 應用程式集區中執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="fc318-196">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50036-ancm-out-of-process-handler-load-failure"></a><span data-ttu-id="fc318-197">500.36 ANCM 跨處理序處理常式載入失敗</span><span class="sxs-lookup"><span data-stu-id="fc318-197">500.36 ANCM Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="fc318-198">跨處理序要求處理常式 *aspnetcorev2_outofprocess.dll* 不在 *aspnetcorev2.dll* 檔案旁邊。</span><span class="sxs-lookup"><span data-stu-id="fc318-198">The out-of-process request handler, *aspnetcorev2_outofprocess.dll*, isn't next to the *aspnetcorev2.dll* file.</span></span> <span data-ttu-id="fc318-199">這表示 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)安裝損毀。</span><span class="sxs-lookup"><span data-stu-id="fc318-199">This indicates a corrupted installation of the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="fc318-200">若要修正這個錯誤，請修復 [.NET Core 裝載套件組合](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (適用於 IIS) 或 Visual Studio (適用於 IIS Express) 安裝。</span><span class="sxs-lookup"><span data-stu-id="fc318-200">To fix this error, repair the installation of the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (for IIS) or Visual Studio (for IIS Express).</span></span>

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a><span data-ttu-id="fc318-201">500.37 ANCM 無法在啟動時間限制內啟動</span><span class="sxs-lookup"><span data-stu-id="fc318-201">500.37 ANCM Failed to Start Within Startup Time Limit</span></span>

<span data-ttu-id="fc318-202">ANCM 無法在提供的啟動時間限制內啟動。</span><span class="sxs-lookup"><span data-stu-id="fc318-202">ANCM failed to start within the provided startup time limit.</span></span> <span data-ttu-id="fc318-203">根據預設，逾時值為 120 秒。</span><span class="sxs-lookup"><span data-stu-id="fc318-203">By default, the timeout is 120 seconds.</span></span>

<span data-ttu-id="fc318-204">在同一部電腦上啟動大量的應用程式時，就會發生此錯誤。</span><span class="sxs-lookup"><span data-stu-id="fc318-204">This error can occur when starting a large number of apps on the same machine.</span></span> <span data-ttu-id="fc318-205">檢查伺服器在啟動期間是否出現 CPU/記憶體的使用量尖峰。</span><span class="sxs-lookup"><span data-stu-id="fc318-205">Check for CPU/Memory usage spikes on the server during startup.</span></span> <span data-ttu-id="fc318-206">多個應用程式的啟動程序可能需要交錯進行。</span><span class="sxs-lookup"><span data-stu-id="fc318-206">You may need to stagger the startup process of multiple apps.</span></span>

### <a name="50038-ancm-application-dll-not-found"></a><span data-ttu-id="fc318-207">500.38 找不到 ANCM 應用程式 DLL</span><span class="sxs-lookup"><span data-stu-id="fc318-207">500.38 ANCM Application DLL Not Found</span></span>

<span data-ttu-id="fc318-208">ANCM 找不到應用程式 DLL，它應該在可執行檔的旁邊。</span><span class="sxs-lookup"><span data-stu-id="fc318-208">ANCM failed to locate the application DLL, which should be next to the executable.</span></span>

<span data-ttu-id="fc318-209">當使用同進程裝載模型來裝載封裝為 [單一檔案可執行](/dotnet/core/whats-new/dotnet-core-3-0#single-file-executables) 檔的應用程式時，就會發生這個錯誤。</span><span class="sxs-lookup"><span data-stu-id="fc318-209">This error occurs when hosting an app packaged as a [single-file executable](/dotnet/core/whats-new/dotnet-core-3-0#single-file-executables) using the in-process hosting model.</span></span> <span data-ttu-id="fc318-210">同進程模型需要 ANCM 將 .NET Core 應用程式載入至現有的 IIS 進程。</span><span class="sxs-lookup"><span data-stu-id="fc318-210">The in-process model requires that the ANCM load the .NET Core app into the existing IIS process.</span></span> <span data-ttu-id="fc318-211">單一檔案部署模型不支援此案例。</span><span class="sxs-lookup"><span data-stu-id="fc318-211">This scenario isn't supported by the single-file deployment model.</span></span> <span data-ttu-id="fc318-212">請在應用程式的專案檔中使用下列 **其中一** 種方法來修正此錯誤：</span><span class="sxs-lookup"><span data-stu-id="fc318-212">Use **one** of the following approaches in the app's project file to fix this error:</span></span>

1. <span data-ttu-id="fc318-213">藉由將 `PublishSingleFile` MSBuild 屬性設定為，以停用單一檔案發行 `false` 。</span><span class="sxs-lookup"><span data-stu-id="fc318-213">Disable single-file publishing by setting the `PublishSingleFile` MSBuild property to `false`.</span></span>
1. <span data-ttu-id="fc318-214">藉由將 MSBuild 屬性設定為，切換至跨進程裝載模型 `AspNetCoreHostingModel` `OutOfProcess` 。</span><span class="sxs-lookup"><span data-stu-id="fc318-214">Switch to the out-of-process hosting model by setting the `AspNetCoreHostingModel` MSBuild property to `OutOfProcess`.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="fc318-215">502.5 處理序失敗</span><span class="sxs-lookup"><span data-stu-id="fc318-215">502.5 Process Failure</span></span>

<span data-ttu-id="fc318-216">背景工作處理序失敗。</span><span class="sxs-lookup"><span data-stu-id="fc318-216">The worker process fails.</span></span> <span data-ttu-id="fc318-217">應用程式未啟動。</span><span class="sxs-lookup"><span data-stu-id="fc318-217">The app doesn't start.</span></span>

<span data-ttu-id="fc318-218">[ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)嘗試啟動背景工作處理序，但無法啟動。</span><span class="sxs-lookup"><span data-stu-id="fc318-218">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="fc318-219">通常從應用程式事件記錄檔和 ASP.NET Core 模組 stdout 記錄檔中的項目，即可判斷啟動失敗的原因。</span><span class="sxs-lookup"><span data-stu-id="fc318-219">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="fc318-220">因為目標 ASP.NET Core 共用架構的版本不存在，導致應用程式設定錯誤是常見的失敗狀況。</span><span class="sxs-lookup"><span data-stu-id="fc318-220">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="fc318-221">請檢查安裝在目標機器上的 ASP.NET Core 共用架構版本為何。</span><span class="sxs-lookup"><span data-stu-id="fc318-221">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="fc318-222">「共用的架構」\*\* 是一組安裝於電腦上且由 `Microsoft.AspNetCore.App` 之類的中繼套件所參考的組件 (*.dll* 檔案)。</span><span class="sxs-lookup"><span data-stu-id="fc318-222">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="fc318-223">中繼套件參考可以指定最低必要版本。</span><span class="sxs-lookup"><span data-stu-id="fc318-223">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="fc318-224">如需詳細資訊，請參閱[共用的架構](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="fc318-224">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="fc318-225">當裝載或應用程式設定錯誤造成背景工作處理序發生失敗時，會傳回 [502.5 處理序失敗]\*\* 錯誤頁面：</span><span class="sxs-lookup"><span data-stu-id="fc318-225">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="fc318-226">無法啟動應用程式 (ErrorCode '0x800700c1')</span><span class="sxs-lookup"><span data-stu-id="fc318-226">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="fc318-227">應用程式無法啟動，因為無法載入應用程式的組件 (*.dll*)。</span><span class="sxs-lookup"><span data-stu-id="fc318-227">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="fc318-228">當已發行的應用程式與 w3wp/iisexpress 處理序之間出現位元不符的情況時，就會發生此錯誤。</span><span class="sxs-lookup"><span data-stu-id="fc318-228">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="fc318-229">確認應用程式集區的 32 位元設定正確：</span><span class="sxs-lookup"><span data-stu-id="fc318-229">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="fc318-230">在 IIS 管理員的 [應用程式集區]\*\*\*\* 中，選取應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="fc318-230">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="fc318-231">在 [動作]\*\*\*\* 面板中，選取 [編輯應用程式集區]\*\*\*\* 下的 [進階設定]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="fc318-231">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="fc318-232">設定 [啟用 32 位元應用程式]\*\*\*\*：</span><span class="sxs-lookup"><span data-stu-id="fc318-232">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="fc318-233">如果部署 32 位元 (x86) 應用程式，請將值設定為 `True`。</span><span class="sxs-lookup"><span data-stu-id="fc318-233">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="fc318-234">如果部署 64 位元 (x64) 應用程式，請將值設定為 `False`。</span><span class="sxs-lookup"><span data-stu-id="fc318-234">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="fc318-235">確認 `<Platform>` 專案檔中的 MSBuild 屬性和應用程式的已發佈位之間沒有衝突。</span><span class="sxs-lookup"><span data-stu-id="fc318-235">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="fc318-236">連線重設</span><span class="sxs-lookup"><span data-stu-id="fc318-236">Connection reset</span></span>

<span data-ttu-id="fc318-237">如果是在傳送標頭之後才發生錯誤，則當發生錯誤時，伺服器已來不及傳送「500 內部伺服器錯誤」\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="fc318-237">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="fc318-238">通常是在將回應的複雜物件序列化的期間發生錯誤時，會發生此錯誤。</span><span class="sxs-lookup"><span data-stu-id="fc318-238">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="fc318-239">這類錯誤會在用戶端上顯示為「連線重設」\*\* 錯誤。</span><span class="sxs-lookup"><span data-stu-id="fc318-239">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="fc318-240">[應用程式記錄](xref:fundamentals/logging/index)可協助針對這些類型的錯誤進行疑難排解。</span><span class="sxs-lookup"><span data-stu-id="fc318-240">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="fc318-241">預設啟動限制</span><span class="sxs-lookup"><span data-stu-id="fc318-241">Default startup limits</span></span>

<span data-ttu-id="fc318-242">[ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)的設定預設*startupTimeLimit*為120秒。</span><span class="sxs-lookup"><span data-stu-id="fc318-242">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="fc318-243">保留預設值時，在模組記錄處理序失敗之前，應用程式最多可花費兩分鐘來進行啟動。</span><span class="sxs-lookup"><span data-stu-id="fc318-243">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="fc318-244">如需有關設定模組的資訊，請參閱 [aspNetCore 元素的屬性](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)。</span><span class="sxs-lookup"><span data-stu-id="fc318-244">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="fc318-245">針對 Azure App Service 進行疑難排解</span><span class="sxs-lookup"><span data-stu-id="fc318-245">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="fc318-246">應用程式事件記錄檔 (Azure App Service) </span><span class="sxs-lookup"><span data-stu-id="fc318-246">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="fc318-247">若要存取「應用程式事件記錄檔」，請使用 Azure 入口網站中的 [診斷並解決問題]\*\*\*\* 刀鋒視窗：</span><span class="sxs-lookup"><span data-stu-id="fc318-247">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="fc318-248">在 Azure 入口網站的 [應用程式服務]\*\*\*\* 中，開啟應用程式。</span><span class="sxs-lookup"><span data-stu-id="fc318-248">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="fc318-249">選取 [診斷並解決問題]。</span><span class="sxs-lookup"><span data-stu-id="fc318-249">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="fc318-250">選取 [診斷工具]\*\*\*\* 標題。</span><span class="sxs-lookup"><span data-stu-id="fc318-250">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="fc318-251">在 [支援工具]\*\*\*\* 下，選取 [應用程式事件]\*\*\*\* 按鈕。</span><span class="sxs-lookup"><span data-stu-id="fc318-251">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="fc318-252">檢查 [來源]\*\*\*\* 資料行中 *IIS AspNetCoreModule* 或 *IIS AspNetCoreModule V2* 項目所提供的最新錯誤。</span><span class="sxs-lookup"><span data-stu-id="fc318-252">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="fc318-253">除了使用 [診斷並解決問題]\*\*\*\* 刀鋒視窗之外，也可以使用 [Kudu](https://github.com/projectkudu/kudu/wiki) 來直接檢查「應用程式事件記錄檔」：</span><span class="sxs-lookup"><span data-stu-id="fc318-253">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="fc318-254">在 [開發工具]\*\*\*\* 區域中，開啟 [進階工具]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="fc318-254">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="fc318-255">選取 [**移 &rarr; 至**] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="fc318-255">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="fc318-256">Kudu 主控台會在新的瀏覽器索引標籤或視窗中開啟。</span><span class="sxs-lookup"><span data-stu-id="fc318-256">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="fc318-257">使用頁面頂端的導覽列，開啟 [偵錯主控台]\*\*\*\*，然後選取 [CMD]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="fc318-257">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="fc318-258">開啟 **LogFiles** 資料夾。</span><span class="sxs-lookup"><span data-stu-id="fc318-258">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="fc318-259">選取 *eventlog.xml* 檔案旁邊的鉛筆圖示。</span><span class="sxs-lookup"><span data-stu-id="fc318-259">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="fc318-260">檢查記錄檔。</span><span class="sxs-lookup"><span data-stu-id="fc318-260">Examine the log.</span></span> <span data-ttu-id="fc318-261">捲動至記錄檔的底部以查看最新事件。</span><span class="sxs-lookup"><span data-stu-id="fc318-261">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="fc318-262">在 Kudu 主控台中執行應用程式</span><span class="sxs-lookup"><span data-stu-id="fc318-262">Run the app in the Kudu console</span></span>

<span data-ttu-id="fc318-263">許多啟動錯誤不會在「應用程式事件記錄檔」中產生實用的資訊。</span><span class="sxs-lookup"><span data-stu-id="fc318-263">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="fc318-264">您可以在 [Kudu](https://github.com/projectkudu/kudu/wiki)「遠端執行主控台」中執行應用程式來探索錯誤：</span><span class="sxs-lookup"><span data-stu-id="fc318-264">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="fc318-265">在 [開發工具]\*\*\*\* 區域中，開啟 [進階工具]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="fc318-265">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="fc318-266">選取 [**移 &rarr; 至**] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="fc318-266">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="fc318-267">Kudu 主控台會在新的瀏覽器索引標籤或視窗中開啟。</span><span class="sxs-lookup"><span data-stu-id="fc318-267">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="fc318-268">使用頁面頂端的導覽列，開啟 [偵錯主控台]\*\*\*\*，然後選取 [CMD]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="fc318-268">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="fc318-269">測試 32 位元 (x86) 應用程式</span><span class="sxs-lookup"><span data-stu-id="fc318-269">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="fc318-270">**目前的版本**</span><span class="sxs-lookup"><span data-stu-id="fc318-270">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="fc318-271">執行應用程式：</span><span class="sxs-lookup"><span data-stu-id="fc318-271">Run the app:</span></span>
   * <span data-ttu-id="fc318-272">如果應用程式是[架構相依部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="fc318-272">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="fc318-273">如果應用程式是[自封式部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="fc318-273">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="fc318-274">應用程式的主控台輸出 (顯示任何錯誤) 會以管道傳送至 Kudu 主控台。</span><span class="sxs-lookup"><span data-stu-id="fc318-274">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="fc318-275">**在預覽版上執行的架構相依部署**</span><span class="sxs-lookup"><span data-stu-id="fc318-275">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="fc318-276">*要求安裝 ASP.NET Core {VERSION} (x86) 執行階段網站延伸模組。*</span><span class="sxs-lookup"><span data-stu-id="fc318-276">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="fc318-277">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` 是執行階段版本)</span><span class="sxs-lookup"><span data-stu-id="fc318-277">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="fc318-278">執行應用程式：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="fc318-278">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="fc318-279">應用程式的主控台輸出 (顯示任何錯誤) 會以管道傳送至 Kudu 主控台。</span><span class="sxs-lookup"><span data-stu-id="fc318-279">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="fc318-280">測試 64 位元 (x64) 應用程式</span><span class="sxs-lookup"><span data-stu-id="fc318-280">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="fc318-281">**目前的版本**</span><span class="sxs-lookup"><span data-stu-id="fc318-281">**Current release**</span></span>

* <span data-ttu-id="fc318-282">如果應用程式是 64 位元 (x64) [架構相依部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="fc318-282">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="fc318-283">執行應用程式：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="fc318-283">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="fc318-284">如果應用程式是[自封式部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="fc318-284">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="fc318-285">執行應用程式：`{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="fc318-285">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="fc318-286">應用程式的主控台輸出 (顯示任何錯誤) 會以管道傳送至 Kudu 主控台。</span><span class="sxs-lookup"><span data-stu-id="fc318-286">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="fc318-287">**在預覽版上執行的架構相依部署**</span><span class="sxs-lookup"><span data-stu-id="fc318-287">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="fc318-288">*要求安裝 ASP.NET Core {VERSION} (x64) 執行階段網站延伸模組。*</span><span class="sxs-lookup"><span data-stu-id="fc318-288">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="fc318-289">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` 是執行階段版本)</span><span class="sxs-lookup"><span data-stu-id="fc318-289">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="fc318-290">執行應用程式：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="fc318-290">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="fc318-291">應用程式的主控台輸出 (顯示任何錯誤) 會以管道傳送至 Kudu 主控台。</span><span class="sxs-lookup"><span data-stu-id="fc318-291">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="fc318-292">ASP.NET Core Module stdout 記錄 (Azure App Service) </span><span class="sxs-lookup"><span data-stu-id="fc318-292">ASP.NET Core Module stdout log (Azure App Service)</span></span>

> [!WARNING]
> <span data-ttu-id="fc318-293">如果無法停用 stdout 記錄檔，可能會造成應用程式或伺服器發生失敗。</span><span class="sxs-lookup"><span data-stu-id="fc318-293">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="fc318-294">因為它並沒有記錄檔大小或數量上的限制。</span><span class="sxs-lookup"><span data-stu-id="fc318-294">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="fc318-295">請只在針對應用程式啟動問題進行疑難排解時，才使用 stdout 記錄。</span><span class="sxs-lookup"><span data-stu-id="fc318-295">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="fc318-296">針對 ASP.NET Core 應用程式啟動後的一般記錄，請使用會限制記錄檔大小並輪替記錄檔的記錄程式庫。</span><span class="sxs-lookup"><span data-stu-id="fc318-296">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="fc318-297">如需詳細資訊，請參閱[協力廠商記錄提供者](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="fc318-297">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="fc318-298">ASP.NET Core 模組 stdout 記錄檔通常會記錄「應用程式事件記錄檔」中所沒有的實用訊息。</span><span class="sxs-lookup"><span data-stu-id="fc318-298">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="fc318-299">啟用及檢視 stdout 記錄檔：</span><span class="sxs-lookup"><span data-stu-id="fc318-299">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="fc318-300">在 Azure 入口網站中，流覽至 web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="fc318-300">In the Azure Portal, navigate to the web app.</span></span>
1. <span data-ttu-id="fc318-301">在 [ **App Service** ] 分頁中，于 [搜尋] 方塊中輸入 **kudu** 。</span><span class="sxs-lookup"><span data-stu-id="fc318-301">In the **App Service** blade, enter **kudu** in the search box.</span></span>
1. <span data-ttu-id="fc318-302">選取 [ **Advanced Tools** > **Go**]。</span><span class="sxs-lookup"><span data-stu-id="fc318-302">Select **Advanced Tools** > **Go**.</span></span>
1. <span data-ttu-id="fc318-303">選取 [  **Debug console > CMD**]。</span><span class="sxs-lookup"><span data-stu-id="fc318-303">Select  **Debug console > CMD**.</span></span>
1. <span data-ttu-id="fc318-304">流覽至 *site/wwwroot*</span><span class="sxs-lookup"><span data-stu-id="fc318-304">Navigate to *site/wwwroot*</span></span>
1. <span data-ttu-id="fc318-305">選取鉛筆圖示以編輯 *web.config* 檔案。</span><span class="sxs-lookup"><span data-stu-id="fc318-305">Select the pencil icon to edit the *web.config* file.</span></span>
1. <span data-ttu-id="fc318-306">在 `<aspNetCore />` 元素中，設定 `stdoutLogEnabled="true"` 並選取 [ **儲存**]。</span><span class="sxs-lookup"><span data-stu-id="fc318-306">In the `<aspNetCore />` element, set `stdoutLogEnabled="true"` and select **Save**.</span></span>

<span data-ttu-id="fc318-307">藉由設定完成疑難排解時，請停用 stdout 記錄 `stdoutLogEnabled="false"` 。</span><span class="sxs-lookup"><span data-stu-id="fc318-307">Disable stdout logging when troubleshooting is complete by setting `stdoutLogEnabled="false"`.</span></span>

<span data-ttu-id="fc318-308">如需詳細資訊，請參閱<xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>。</span><span class="sxs-lookup"><span data-stu-id="fc318-308">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="fc318-309">ASP.NET Core 模組 debug log (Azure App Service) </span><span class="sxs-lookup"><span data-stu-id="fc318-309">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="fc318-310">ASP.NET Core 模組偵錯記錄提供 ASP.NET Core 模組中其他且更深入的記錄。</span><span class="sxs-lookup"><span data-stu-id="fc318-310">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="fc318-311">啟用及檢視 stdout 記錄檔：</span><span class="sxs-lookup"><span data-stu-id="fc318-311">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="fc318-312">若要啟用增強型診斷記錄，請執行下列任一動作：</span><span class="sxs-lookup"><span data-stu-id="fc318-312">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="fc318-313">遵循[增強型診斷記錄](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中的指示，來設定應用程式進行增強型診斷記錄。</span><span class="sxs-lookup"><span data-stu-id="fc318-313">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="fc318-314">重新部署應用程式。</span><span class="sxs-lookup"><span data-stu-id="fc318-314">Redeploy the app.</span></span>
   * <span data-ttu-id="fc318-315">使用 Kudu 主控台，將[增強型診斷記錄](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中所顯示的 `<handlerSettings>` 新增至即時應用程式的 *web.config* 檔案：</span><span class="sxs-lookup"><span data-stu-id="fc318-315">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="fc318-316">在 [開發工具]\*\*\*\* 區域中，開啟 [進階工具]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="fc318-316">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="fc318-317">選取 [**移 &rarr; 至**] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="fc318-317">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="fc318-318">Kudu 主控台會在新的瀏覽器索引標籤或視窗中開啟。</span><span class="sxs-lookup"><span data-stu-id="fc318-318">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="fc318-319">使用頁面頂端的導覽列，開啟 [偵錯主控台]\*\*\*\*，然後選取 [CMD]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="fc318-319">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="fc318-320">將資料夾開啟至路徑**網站**  >  **wwwroot**。</span><span class="sxs-lookup"><span data-stu-id="fc318-320">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="fc318-321">選取鉛筆圖示來編輯 *web.config* 檔案。</span><span class="sxs-lookup"><span data-stu-id="fc318-321">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="fc318-322">新增 `<handlerSettings>` 區段，如[增強型診斷記錄](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中所示。</span><span class="sxs-lookup"><span data-stu-id="fc318-322">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="fc318-323">選取 [儲存] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="fc318-323">Select the **Save** button.</span></span>
1. <span data-ttu-id="fc318-324">在 [開發工具]\*\*\*\* 區域中，開啟 [進階工具]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="fc318-324">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="fc318-325">選取 [**移 &rarr; 至**] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="fc318-325">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="fc318-326">Kudu 主控台會在新的瀏覽器索引標籤或視窗中開啟。</span><span class="sxs-lookup"><span data-stu-id="fc318-326">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="fc318-327">使用頁面頂端的導覽列，開啟 [偵錯主控台]\*\*\*\*，然後選取 [CMD]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="fc318-327">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="fc318-328">將資料夾開啟至路徑**網站**  >  **wwwroot**。</span><span class="sxs-lookup"><span data-stu-id="fc318-328">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="fc318-329">如果未提供 *aspnetcore-debug.log* 檔案的路徑，該檔案會顯示在清單中。</span><span class="sxs-lookup"><span data-stu-id="fc318-329">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="fc318-330">如果已提供路徑，請巡覽至記錄檔的位置。</span><span class="sxs-lookup"><span data-stu-id="fc318-330">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="fc318-331">使用檔案名稱旁的鉛筆圖示來開啟記錄檔。</span><span class="sxs-lookup"><span data-stu-id="fc318-331">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="fc318-332">完成疑難排解時，請停用偵錯記錄：</span><span class="sxs-lookup"><span data-stu-id="fc318-332">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="fc318-333">若要停用增強型偵錯記錄，請執行下列任一動作：</span><span class="sxs-lookup"><span data-stu-id="fc318-333">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="fc318-334">從 *web.config* 檔案本機移除 `<handlerSettings>` 並重新部署應用程式。</span><span class="sxs-lookup"><span data-stu-id="fc318-334">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="fc318-335">使用 Kudu 主控台來編輯 *web.config* 檔案並移除 `<handlerSettings>` 區段。</span><span class="sxs-lookup"><span data-stu-id="fc318-335">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="fc318-336">儲存檔案。</span><span class="sxs-lookup"><span data-stu-id="fc318-336">Save the file.</span></span>

<span data-ttu-id="fc318-337">如需詳細資訊，請參閱<xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>。</span><span class="sxs-lookup"><span data-stu-id="fc318-337">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="fc318-338">無法停用偵錯記錄，可能會造成應用程式或伺服器發生失敗。</span><span class="sxs-lookup"><span data-stu-id="fc318-338">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="fc318-339">記錄檔大小沒有任何限制。</span><span class="sxs-lookup"><span data-stu-id="fc318-339">There's no limit on log file size.</span></span> <span data-ttu-id="fc318-340">請只在針對應用程式啟動問題進行疑難排解時，才使用偵錯記錄。</span><span class="sxs-lookup"><span data-stu-id="fc318-340">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="fc318-341">針對 ASP.NET Core 應用程式啟動後的一般記錄，請使用會限制記錄檔大小並輪替記錄檔的記錄程式庫。</span><span class="sxs-lookup"><span data-stu-id="fc318-341">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="fc318-342">如需詳細資訊，請參閱[協力廠商記錄提供者](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="fc318-342">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="fc318-343">應用程式 (Azure App Service 的緩慢或懸掛) </span><span class="sxs-lookup"><span data-stu-id="fc318-343">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="fc318-344">當應用程式針對要求回應緩慢或無回應時，請參閱下列文章：</span><span class="sxs-lookup"><span data-stu-id="fc318-344">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="fc318-345">針對 Azure App Service 中 Web 應用程式效能變慢的問題進行疑難排解</span><span class="sxs-lookup"><span data-stu-id="fc318-345">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* <span data-ttu-id="fc318-346">[使用損毀診斷程式網站延伸模組來擷取傾印，以取得 Azure Web 應用程式上的間歇性例外狀況或效能問題](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="fc318-346">[Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)</span></span>

### <a name="monitoring-blades"></a><span data-ttu-id="fc318-347">監視刀鋒視窗</span><span class="sxs-lookup"><span data-stu-id="fc318-347">Monitoring blades</span></span>

<span data-ttu-id="fc318-348">監視刀鋒視窗為本主題稍早所述的方法提供替代的疑難排解體驗。</span><span class="sxs-lookup"><span data-stu-id="fc318-348">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="fc318-349">這些刀鋒視窗可用來診斷 500 系列的錯誤。</span><span class="sxs-lookup"><span data-stu-id="fc318-349">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="fc318-350">請確認已安裝 ASP.NET Core 延伸模組。</span><span class="sxs-lookup"><span data-stu-id="fc318-350">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="fc318-351">如果未安裝這些延伸模組，請手動安裝：</span><span class="sxs-lookup"><span data-stu-id="fc318-351">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="fc318-352">在 [開發工具]\*\*\*\* 刀鋒視窗區段中，選取 [延伸模組]\*\*\*\* 刀鋒視窗。</span><span class="sxs-lookup"><span data-stu-id="fc318-352">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="fc318-353">[ASP.NET Core 延伸模組]\*\*\*\* 應該會出現在清單中。</span><span class="sxs-lookup"><span data-stu-id="fc318-353">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="fc318-354">如果未安裝這些延伸模組，請選取 [新增]\*\*\*\* 按鈕。</span><span class="sxs-lookup"><span data-stu-id="fc318-354">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="fc318-355">從清單中選擇 [ASP.NET Core 延伸模組]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="fc318-355">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="fc318-356">選取 [確定]\*\*\*\* 以接受法律條款。</span><span class="sxs-lookup"><span data-stu-id="fc318-356">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="fc318-357">在 [新增延伸模組]\*\*\*\* 刀鋒視窗上，選取 [確定]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="fc318-357">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="fc318-358">成功安裝延伸模組時，會有資訊快顯訊息提供指示。</span><span class="sxs-lookup"><span data-stu-id="fc318-358">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="fc318-359">如果未啟用 stdout 記錄，請依照下列步驟進行操作：</span><span class="sxs-lookup"><span data-stu-id="fc318-359">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="fc318-360">在 Azure 入口網站中，選取 [開發工具]\*\*\*\* 區域中的 [進階工具]\*\*\*\* 刀鋒視窗。</span><span class="sxs-lookup"><span data-stu-id="fc318-360">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="fc318-361">選取 [**移 &rarr; 至**] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="fc318-361">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="fc318-362">Kudu 主控台會在新的瀏覽器索引標籤或視窗中開啟。</span><span class="sxs-lookup"><span data-stu-id="fc318-362">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="fc318-363">使用頁面頂端的導覽列，開啟 [偵錯主控台]\*\*\*\*，然後選取 [CMD]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="fc318-363">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="fc318-364">將資料夾開啟至路徑 [site]**[wwwroot]** > \*\*\*\*，然後向下捲動以顯露位於清單底部的 *web.config* 檔案。</span><span class="sxs-lookup"><span data-stu-id="fc318-364">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="fc318-365">按一下 *web.config* 檔案旁邊的鉛筆圖示。</span><span class="sxs-lookup"><span data-stu-id="fc318-365">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="fc318-366">將 **stdoutLogEnabled** 設定為 `true`，並將 **stdoutLogFile** 路徑變更為：`\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="fc318-366">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="fc318-367">選取 [儲存]\*\*\*\* 以儲存已更新的 *web.config* 檔案。</span><span class="sxs-lookup"><span data-stu-id="fc318-367">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="fc318-368">繼續接著啟用診斷記錄：</span><span class="sxs-lookup"><span data-stu-id="fc318-368">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="fc318-369">在 Azure 入口網站中，選取 [診斷記錄檔]\*\*\*\* 刀鋒視窗。</span><span class="sxs-lookup"><span data-stu-id="fc318-369">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="fc318-370">選取 [應用程式記錄 (檔案系統)]\*\*\*\* 和 [詳細錯誤訊息]\*\*\*\*.的 [開啟]\*\*\*\* 開關。</span><span class="sxs-lookup"><span data-stu-id="fc318-370">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="fc318-371">選取刀鋒視窗頂端的 [儲存]\*\*\*\* 按鈕。</span><span class="sxs-lookup"><span data-stu-id="fc318-371">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="fc318-372">若要包含失敗要求追蹤 (也稱為「失敗要求事件緩衝處理」(FREB) 記錄)，請選取 [失敗要求的追蹤]\*\*\*\* 的 [開啟]\*\*\*\* 開關。</span><span class="sxs-lookup"><span data-stu-id="fc318-372">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="fc318-373">選取 [記錄資料流]\*\*\*\* 刀鋒視窗 (列在入口網站中緊接在 [診斷記錄檔]\*\*\*\* 刀鋒視窗之下)。</span><span class="sxs-lookup"><span data-stu-id="fc318-373">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="fc318-374">對應用程式發出要求。</span><span class="sxs-lookup"><span data-stu-id="fc318-374">Make a request to the app.</span></span>
1. <span data-ttu-id="fc318-375">在記錄資料流資料內，會指出錯誤的原因。</span><span class="sxs-lookup"><span data-stu-id="fc318-375">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="fc318-376">完成疑難排解時，請務必停用 stdout 記錄。</span><span class="sxs-lookup"><span data-stu-id="fc318-376">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="fc318-377">檢視失敗要求追蹤記錄檔 (FREB 記錄檔)：</span><span class="sxs-lookup"><span data-stu-id="fc318-377">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="fc318-378">在 Azure 入口網站中，瀏覽至 [診斷並解決問題]\*\*\*\* 刀鋒視窗。</span><span class="sxs-lookup"><span data-stu-id="fc318-378">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="fc318-379">從資訊看板的 [支援工具]\*\*\*\* 區域中，選取 [失敗要求追蹤記錄檔]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="fc318-379">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="fc318-380">如需詳細資訊，請參閱[＜在 Azure App Service 中針對 Web 應用程式啟用診斷記錄功能＞主題的＜失敗要求追蹤＞一節](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces)和[Azure Web 應用程式的應用程式效能常見問題集：如何開啟失敗要求追蹤？](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)。</span><span class="sxs-lookup"><span data-stu-id="fc318-380">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="fc318-381">如需詳細資訊，請參閱[在 Azure App Service 中針對 Web 應用程式啟用診斷記錄功能](/azure/app-service/web-sites-enable-diagnostic-log)。</span><span class="sxs-lookup"><span data-stu-id="fc318-381">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="fc318-382">如果無法停用 stdout 記錄檔，可能會造成應用程式或伺服器發生失敗。</span><span class="sxs-lookup"><span data-stu-id="fc318-382">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="fc318-383">因為它並沒有記錄檔大小或數量上的限制。</span><span class="sxs-lookup"><span data-stu-id="fc318-383">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="fc318-384">針對 ASP.NET Core 應用程式中的例行性記錄，請使用會限制記錄檔大小並輪替記錄檔的記錄程式庫。</span><span class="sxs-lookup"><span data-stu-id="fc318-384">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="fc318-385">如需詳細資訊，請參閱[協力廠商記錄提供者](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="fc318-385">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="fc318-386">針對 IIS 進行疑難排解</span><span class="sxs-lookup"><span data-stu-id="fc318-386">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="fc318-387">應用程式事件記錄檔 (IIS) </span><span class="sxs-lookup"><span data-stu-id="fc318-387">Application Event Log (IIS)</span></span>

<span data-ttu-id="fc318-388">存取「應用程式事件記錄檔」：</span><span class="sxs-lookup"><span data-stu-id="fc318-388">Access the Application Event Log:</span></span>

1. <span data-ttu-id="fc318-389">開啟 [開始] 功能表、搜尋 *事件檢視器*，然後選取 **事件檢視器** 應用程式。</span><span class="sxs-lookup"><span data-stu-id="fc318-389">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="fc318-390">在 [事件檢視器]\*\*\*\* 中，開啟 [Windows 記錄]\*\*\*\* 節點。</span><span class="sxs-lookup"><span data-stu-id="fc318-390">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="fc318-391">選取 [應用程式]\*\*\*\* 以開啟「應用程式事件記錄檔」。</span><span class="sxs-lookup"><span data-stu-id="fc318-391">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="fc318-392">搜尋與失敗應用程式相關的錯誤。</span><span class="sxs-lookup"><span data-stu-id="fc318-392">Search for errors associated with the failing app.</span></span> <span data-ttu-id="fc318-393">錯誤在 [來源]\*\* 資料行中的值會是 *IIS AspNetCore Module* 或 *IIS Express AspNetCore Module*。</span><span class="sxs-lookup"><span data-stu-id="fc318-393">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="fc318-394">在命令提示字元中執行應用程式</span><span class="sxs-lookup"><span data-stu-id="fc318-394">Run the app at a command prompt</span></span>

<span data-ttu-id="fc318-395">許多啟動錯誤不會在「應用程式事件記錄檔」中產生實用的資訊。</span><span class="sxs-lookup"><span data-stu-id="fc318-395">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="fc318-396">您可以藉由在主控系統上的命令提示字元中執行應用程式，來找出一些錯誤的原因。</span><span class="sxs-lookup"><span data-stu-id="fc318-396">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="fc318-397">與 Framework 相依的部署</span><span class="sxs-lookup"><span data-stu-id="fc318-397">Framework-dependent deployment</span></span>

<span data-ttu-id="fc318-398">如果應用程式是[架構相依部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="fc318-398">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="fc318-399">在命令提示字元中，瀏覽至部署資料夾，然後使用 *dotnet.exe* 來執行應用程式組件以執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="fc318-399">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="fc318-400">在下列命令中，將應用程式的元件名稱取代為 \<assembly_name> ： `dotnet .\<assembly_name>.dll` 。</span><span class="sxs-lookup"><span data-stu-id="fc318-400">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="fc318-401">來自應用程式的主控台輸出若有顯示任何錯誤，就會寫入至主控台視窗。</span><span class="sxs-lookup"><span data-stu-id="fc318-401">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="fc318-402">如果是在對應用程式發出要求時發生錯誤，請對 Kestrel 進行接聽的主機和連接埠發出要求。</span><span class="sxs-lookup"><span data-stu-id="fc318-402">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="fc318-403">如果使用預設主機和連接埠，請對 `http://localhost:5000/` 發出要求。</span><span class="sxs-lookup"><span data-stu-id="fc318-403">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="fc318-404">如果應用程式在 Kestrel 端點位址正常回應，則問題與主機組態有關的機率較大，而與應用程式本身有關的機率較小。</span><span class="sxs-lookup"><span data-stu-id="fc318-404">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="fc318-405">自封式部署</span><span class="sxs-lookup"><span data-stu-id="fc318-405">Self-contained deployment</span></span>

<span data-ttu-id="fc318-406">如果應用程式是[自封式部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="fc318-406">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="fc318-407">在命令提示字元中，瀏覽至部署資料夾，然後執行應用程式的可執行檔。</span><span class="sxs-lookup"><span data-stu-id="fc318-407">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="fc318-408">在下列命令中，將應用程式的元件名稱取代為 \<assembly_name> ： `<assembly_name>.exe` 。</span><span class="sxs-lookup"><span data-stu-id="fc318-408">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="fc318-409">來自應用程式的主控台輸出若有顯示任何錯誤，就會寫入至主控台視窗。</span><span class="sxs-lookup"><span data-stu-id="fc318-409">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="fc318-410">如果是在對應用程式發出要求時發生錯誤，請對 Kestrel 進行接聽的主機和連接埠發出要求。</span><span class="sxs-lookup"><span data-stu-id="fc318-410">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="fc318-411">如果使用預設主機和連接埠，請對 `http://localhost:5000/` 發出要求。</span><span class="sxs-lookup"><span data-stu-id="fc318-411">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="fc318-412">如果應用程式在 Kestrel 端點位址正常回應，則問題與主機組態有關的機率較大，而與應用程式本身有關的機率較小。</span><span class="sxs-lookup"><span data-stu-id="fc318-412">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="fc318-413">ASP.NET Core Module stdout log (IIS) </span><span class="sxs-lookup"><span data-stu-id="fc318-413">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="fc318-414">啟用及檢視 stdout 記錄檔：</span><span class="sxs-lookup"><span data-stu-id="fc318-414">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="fc318-415">瀏覽至主控系統上網站的部署資料夾。</span><span class="sxs-lookup"><span data-stu-id="fc318-415">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="fc318-416">如果 [logs]\*\* 資料夾不存在，請建立該資料夾。</span><span class="sxs-lookup"><span data-stu-id="fc318-416">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="fc318-417">如需有關如何讓 MSBuild 在部署中自動建立 [logs]\*\* 資料夾的指示，請參閱[目錄結構](xref:host-and-deploy/directory-structure)主題。</span><span class="sxs-lookup"><span data-stu-id="fc318-417">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="fc318-418">編輯 *web.config* 檔案。</span><span class="sxs-lookup"><span data-stu-id="fc318-418">Edit the *web.config* file.</span></span> <span data-ttu-id="fc318-419">將 **stdoutLogEnabled** 設定為 `true`，並將 **stdoutLogFile** 路徑變更為指向 [logs]\*\* 資料夾 (例如 `.\logs\stdout`)。</span><span class="sxs-lookup"><span data-stu-id="fc318-419">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="fc318-420">路徑中的 `stdout` 是記錄檔名稱前置詞。</span><span class="sxs-lookup"><span data-stu-id="fc318-420">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="fc318-421">建立記錄檔時，系統會自動新增時間戳記、處理序識別碼及副檔名。</span><span class="sxs-lookup"><span data-stu-id="fc318-421">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="fc318-422">使用 `stdout` 作為檔案名稱前置詞時，一般記錄檔會命名為 *stdout_20180205184032_5412.log*。</span><span class="sxs-lookup"><span data-stu-id="fc318-422">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="fc318-423">請確定您的應用程式集區身分識別具有 *logs* 資料夾的寫入權限。</span><span class="sxs-lookup"><span data-stu-id="fc318-423">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="fc318-424">儲存已更新的 *web.config* 檔案。</span><span class="sxs-lookup"><span data-stu-id="fc318-424">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="fc318-425">對應用程式發出要求。</span><span class="sxs-lookup"><span data-stu-id="fc318-425">Make a request to the app.</span></span>
1. <span data-ttu-id="fc318-426">瀏覽至 [logs]\*\* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="fc318-426">Navigate to the *logs* folder.</span></span> <span data-ttu-id="fc318-427">尋找並開啟最新的 stdout 記錄檔。</span><span class="sxs-lookup"><span data-stu-id="fc318-427">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="fc318-428">研究記錄檔以了解錯誤。</span><span class="sxs-lookup"><span data-stu-id="fc318-428">Study the log for errors.</span></span>

<span data-ttu-id="fc318-429">完成疑難排解時，請停用 stdout 記錄：</span><span class="sxs-lookup"><span data-stu-id="fc318-429">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="fc318-430">編輯 *web.config* 檔案。</span><span class="sxs-lookup"><span data-stu-id="fc318-430">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="fc318-431">將 **stdoutLogEnabled** 設定為 `false`。</span><span class="sxs-lookup"><span data-stu-id="fc318-431">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="fc318-432">儲存檔案。</span><span class="sxs-lookup"><span data-stu-id="fc318-432">Save the file.</span></span>

<span data-ttu-id="fc318-433">如需詳細資訊，請參閱<xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>。</span><span class="sxs-lookup"><span data-stu-id="fc318-433">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="fc318-434">如果無法停用 stdout 記錄檔，可能會造成應用程式或伺服器發生失敗。</span><span class="sxs-lookup"><span data-stu-id="fc318-434">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="fc318-435">因為它並沒有記錄檔大小或數量上的限制。</span><span class="sxs-lookup"><span data-stu-id="fc318-435">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="fc318-436">針對 ASP.NET Core 應用程式中的例行性記錄，請使用會限制記錄檔大小並輪替記錄檔的記錄程式庫。</span><span class="sxs-lookup"><span data-stu-id="fc318-436">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="fc318-437">如需詳細資訊，請參閱[協力廠商記錄提供者](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="fc318-437">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="fc318-438">ASP.NET Core 模組 debug log (IIS) </span><span class="sxs-lookup"><span data-stu-id="fc318-438">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="fc318-439">將下列處理常式設定新增至應用程式的 *web.config* 檔，以啟用 ASP.NET Core 模組的偵錯工具記錄：</span><span class="sxs-lookup"><span data-stu-id="fc318-439">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="fc318-440">確認為記錄指定的路徑存在，而且應用程式集區的身分識別具有該位置的寫入權限。</span><span class="sxs-lookup"><span data-stu-id="fc318-440">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="fc318-441">如需詳細資訊，請參閱<xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>。</span><span class="sxs-lookup"><span data-stu-id="fc318-441">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="fc318-442">啟用開發人員例外頁面</span><span class="sxs-lookup"><span data-stu-id="fc318-442">Enable the Developer Exception Page</span></span>

<span data-ttu-id="fc318-443">在開發環境中，可以將 `ASPNETCORE_ENVIRONMENT` [環境變數新增至 web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) 來執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="fc318-443">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="fc318-444">只要主機產生器上的 `UseEnvironment` 不會覆寫應用程式啟動內的環境，設定該環境變數便可允許在應用程式執行時顯示[開發人員例外狀況頁面](xref:fundamentals/error-handling)。</span><span class="sxs-lookup"><span data-stu-id="fc318-444">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

<span data-ttu-id="fc318-445">只有在沒有對網際網路公開的暫存和測試伺服器上使用時，才建議為 `ASPNETCORE_ENVIRONMENT` 設定環境變數。</span><span class="sxs-lookup"><span data-stu-id="fc318-445">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="fc318-446">進行疑難排解之後，請從 *web.config* 檔案中移除環境變數。</span><span class="sxs-lookup"><span data-stu-id="fc318-446">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="fc318-447">如需有關在 *web.config* 中設定環境變數的資訊，請參閱 [aspNetCore 的 environmentVariables 子元素](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)。</span><span class="sxs-lookup"><span data-stu-id="fc318-447">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="fc318-448">從應用程式取得資料</span><span class="sxs-lookup"><span data-stu-id="fc318-448">Obtain data from an app</span></span>

<span data-ttu-id="fc318-449">若應用程式能夠回應要求、請使用終端機內嵌中介軟體從應用程式取得要求、連線與額外資料。</span><span class="sxs-lookup"><span data-stu-id="fc318-449">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="fc318-450">如如需詳細資訊與範例程式碼，請參閱 <xref:test/troubleshoot#obtain-data-from-an-app>。</span><span class="sxs-lookup"><span data-stu-id="fc318-450">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="fc318-451"> (IIS) 的應用程式變慢或懸掛</span><span class="sxs-lookup"><span data-stu-id="fc318-451">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="fc318-452">損 *毀* 傾印是系統記憶體的快照，可協助判斷應用程式損毀、啟動失敗或應用程式緩慢的原因。</span><span class="sxs-lookup"><span data-stu-id="fc318-452">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="fc318-453">應用程式損毀或發生例外狀況</span><span class="sxs-lookup"><span data-stu-id="fc318-453">App crashes or encounters an exception</span></span>

<span data-ttu-id="fc318-454">從 [Windows 錯誤報告 (WER)](/windows/desktop/wer/windows-error-reporting) 取得並分析傾印：</span><span class="sxs-lookup"><span data-stu-id="fc318-454">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="fc318-455">在 `c:\dumps` 中建立資料夾以保存損毀傾印檔案。</span><span class="sxs-lookup"><span data-stu-id="fc318-455">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="fc318-456">應用程式集區必須具備該資料夾的寫入權限。</span><span class="sxs-lookup"><span data-stu-id="fc318-456">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="fc318-457">執行 [EnableDumps PowerShell 指令碼](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="fc318-457">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="fc318-458">如果應用程式是使用[同處理序主控模型](xref:host-and-deploy/iis/index#in-process-hosting-model)，請執行 *w3wp.exe* 的指令碼：</span><span class="sxs-lookup"><span data-stu-id="fc318-458">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="fc318-459">如果應用程式是使用[跨處理序主控模型](xref:host-and-deploy/iis/index#out-of-process-hosting-model)，請執行 *dotnet.exe* 的指令碼：</span><span class="sxs-lookup"><span data-stu-id="fc318-459">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="fc318-460">在會導致損毀的情況下，執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="fc318-460">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="fc318-461">發生損毀之後，請執行 [DisableDumps PowerShell 指令碼](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="fc318-461">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="fc318-462">如果應用程式是使用[同處理序主控模型](xref:host-and-deploy/iis/index#in-process-hosting-model)，請執行 *w3wp.exe* 的指令碼：</span><span class="sxs-lookup"><span data-stu-id="fc318-462">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="fc318-463">如果應用程式是使用[跨處理序主控模型](xref:host-and-deploy/iis/index#out-of-process-hosting-model)，請執行 *dotnet.exe* 的指令碼：</span><span class="sxs-lookup"><span data-stu-id="fc318-463">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="fc318-464">在應用程式損毀且傾印收集完成之後，即可正常結束應用程式。</span><span class="sxs-lookup"><span data-stu-id="fc318-464">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="fc318-465">PowerShell 指令碼會將 WER 設定為每個應用程式收集最多五個傾印。</span><span class="sxs-lookup"><span data-stu-id="fc318-465">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="fc318-466">損毀傾印可能會佔用大量磁碟空間 (每個高達好幾 GB)。</span><span class="sxs-lookup"><span data-stu-id="fc318-466">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="fc318-467">應用程式停止回應、在啟動期間失敗，或正常執行</span><span class="sxs-lookup"><span data-stu-id="fc318-467">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="fc318-468">當 *應用程式* 停止回應時 (停止回應，但不會當機) 、啟動期間失敗，或正常執行時，請參閱 [使用者模式](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) 傾印檔案：選擇最適合的工具來選取適當的工具來產生傾印。</span><span class="sxs-lookup"><span data-stu-id="fc318-468">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="fc318-469">分析傾印</span><span class="sxs-lookup"><span data-stu-id="fc318-469">Analyze the dump</span></span>

<span data-ttu-id="fc318-470">您可以使用數種方法來分析傾印。</span><span class="sxs-lookup"><span data-stu-id="fc318-470">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="fc318-471">如需詳細資訊，請參閱[分析使用者模式傾印檔案](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)。</span><span class="sxs-lookup"><span data-stu-id="fc318-471">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="fc318-472">清除套件快取</span><span class="sxs-lookup"><span data-stu-id="fc318-472">Clear package caches</span></span>

<span data-ttu-id="fc318-473">在升級開發電腦上的 .NET Core SDK 或變更應用程式內的套件版本之後，正常運作的應用程式可能會立即失敗。</span><span class="sxs-lookup"><span data-stu-id="fc318-473">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="fc318-474">在某些情況下，執行主要升級時，不一致的套件可能會中斷應用程式。</span><span class="sxs-lookup"><span data-stu-id="fc318-474">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="fc318-475">大多數這些問題都可依照下列指示來進行修正：</span><span class="sxs-lookup"><span data-stu-id="fc318-475">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="fc318-476">刪除 [bin]\*\* 和 [obj]\*\* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="fc318-476">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="fc318-477">從命令列介面執行 [dotnet nuget 區域變數](/dotnet/core/tools/dotnet-nuget-locals) ，以清除套件快取。</span><span class="sxs-lookup"><span data-stu-id="fc318-477">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="fc318-478">清除套件快取也可以使用 [nuget.exe](https://www.nuget.org/downloads) 工具和執行命令來完成 `nuget locals all -clear` 。</span><span class="sxs-lookup"><span data-stu-id="fc318-478">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="fc318-479">*nuget.exe* 並未隨附在 Windows 桌面作業系統的安裝中，必須另外從 [NuGet 網站](https://www.nuget.org/downloads)取得。</span><span class="sxs-lookup"><span data-stu-id="fc318-479">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="fc318-480">還原並重建專案。</span><span class="sxs-lookup"><span data-stu-id="fc318-480">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="fc318-481">重新部署應用程式之前，請先刪除伺服器上 [部署] 資料夾中的所有檔案。</span><span class="sxs-lookup"><span data-stu-id="fc318-481">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fc318-482">其他資源</span><span class="sxs-lookup"><span data-stu-id="fc318-482">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="fc318-483">Azure 文件</span><span class="sxs-lookup"><span data-stu-id="fc318-483">Azure documentation</span></span>

* [<span data-ttu-id="fc318-484">ASP.NET Core 的 Application Insights</span><span class="sxs-lookup"><span data-stu-id="fc318-484">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="fc318-485">使用 Visual Studio 針對 Azure App Service 中的 web 應用程式進行疑難排解的遠端偵錯程式區段</span><span class="sxs-lookup"><span data-stu-id="fc318-485">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="fc318-486">Azure App Service 診斷概觀</span><span class="sxs-lookup"><span data-stu-id="fc318-486">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="fc318-487">作法：監視 Azure App Service 中的應用程式</span><span class="sxs-lookup"><span data-stu-id="fc318-487">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="fc318-488">使用 Visual Studio 疑難排解 Azure App Service 中的 Web 應用程式</span><span class="sxs-lookup"><span data-stu-id="fc318-488">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="fc318-489">對您的 Azure Web 應用程式中「502 不正確的閘道」和「503 服務無法使用」的 HTTP 錯誤進行疑難排解</span><span class="sxs-lookup"><span data-stu-id="fc318-489">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="fc318-490">針對 Azure App Service 中 Web 應用程式效能變慢的問題進行疑難排解</span><span class="sxs-lookup"><span data-stu-id="fc318-490">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="fc318-491">Azure Web 應用程式的應用程式效能常見問題集</span><span class="sxs-lookup"><span data-stu-id="fc318-491">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* <span data-ttu-id="fc318-492">[Azure Web 應用程式沙箱 (App Service 執行階段執行限制)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="fc318-492">[Azure Web App sandbox (App Service runtime execution limitations)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)</span></span>
* [<span data-ttu-id="fc318-493">Azure Friday：Azure App Service 診斷和疑難排解體驗 (12 分鐘的影片)</span><span class="sxs-lookup"><span data-stu-id="fc318-493">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="fc318-494">Visual Studio 文件</span><span class="sxs-lookup"><span data-stu-id="fc318-494">Visual Studio documentation</span></span>

* [<span data-ttu-id="fc318-495">Visual Studio 2017 中的 Azure IIS 上的遠端偵錯 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fc318-495">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="fc318-496">Visual Studio 2017 的遠端 IIS 電腦上的遠端偵錯 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fc318-496">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="fc318-497">瞭解如何使用 Visual Studio 進行調試</span><span class="sxs-lookup"><span data-stu-id="fc318-497">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="fc318-498">Visual Studio Code 文件</span><span class="sxs-lookup"><span data-stu-id="fc318-498">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="fc318-499">使用 Visual Studio Code 偵錯</span><span class="sxs-lookup"><span data-stu-id="fc318-499">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="fc318-500">本文提供有關一般應用程式啟動錯誤的資訊，以及如何在將應用程式部署到 Azure App Service 或 IIS 時診斷錯誤的指示：</span><span class="sxs-lookup"><span data-stu-id="fc318-500">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="fc318-501">應用程式啟動錯誤</span><span class="sxs-lookup"><span data-stu-id="fc318-501">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="fc318-502">說明常見的啟動 HTTP 狀態碼案例。</span><span class="sxs-lookup"><span data-stu-id="fc318-502">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="fc318-503">針對 Azure App Service 進行疑難排解</span><span class="sxs-lookup"><span data-stu-id="fc318-503">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="fc318-504">針對部署至 Azure App Service 的應用程式提供疑難排解建議。</span><span class="sxs-lookup"><span data-stu-id="fc318-504">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="fc318-505">針對 IIS 進行疑難排解</span><span class="sxs-lookup"><span data-stu-id="fc318-505">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="fc318-506">針對部署至 IIS 或在本機 IIS Express 上執行的應用程式，提供疑難排解建議。</span><span class="sxs-lookup"><span data-stu-id="fc318-506">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="fc318-507">本指南適用于 Windows Server 和 Windows 桌面部署。</span><span class="sxs-lookup"><span data-stu-id="fc318-507">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="fc318-508">清除套件快取</span><span class="sxs-lookup"><span data-stu-id="fc318-508">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="fc318-509">說明當一致套件在執行主要升級或變更套件版本時，會中斷應用程式時，該怎麼辦。</span><span class="sxs-lookup"><span data-stu-id="fc318-509">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="fc318-510">其他資源</span><span class="sxs-lookup"><span data-stu-id="fc318-510">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="fc318-511">列出其他疑難排解主題。</span><span class="sxs-lookup"><span data-stu-id="fc318-511">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="fc318-512">應用程式啟動錯誤</span><span class="sxs-lookup"><span data-stu-id="fc318-512">App startup errors</span></span>

<span data-ttu-id="fc318-513">在 Visual Studio 中，ASP.NET Core 專案預設為在偵錯工具期間 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 裝載。</span><span class="sxs-lookup"><span data-stu-id="fc318-513">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="fc318-514">您可以使用本主題中的建議來診斷在本機進行偵錯工具時，發生 *502.5-進程失敗* 或 *500.30-啟動失敗* 。</span><span class="sxs-lookup"><span data-stu-id="fc318-514">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="fc318-515">403.14 禁止</span><span class="sxs-lookup"><span data-stu-id="fc318-515">403.14 Forbidden</span></span>

<span data-ttu-id="fc318-516">應用程式無法啟動。</span><span class="sxs-lookup"><span data-stu-id="fc318-516">The app fails to start.</span></span> <span data-ttu-id="fc318-517">會記錄下列錯誤：</span><span class="sxs-lookup"><span data-stu-id="fc318-517">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="fc318-518">此錯誤通常是因為裝載系統上的部署中斷所造成，包括下列任一案例：</span><span class="sxs-lookup"><span data-stu-id="fc318-518">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="fc318-519">應用程式會部署到裝載系統上的錯誤資料夾。</span><span class="sxs-lookup"><span data-stu-id="fc318-519">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="fc318-520">部署程式無法將所有應用程式的檔案和資料夾移至裝載系統上的部署資料夾。</span><span class="sxs-lookup"><span data-stu-id="fc318-520">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="fc318-521">部署中缺少 *web.config* 檔案，或 *web.config* 的檔案內容格式不正確。</span><span class="sxs-lookup"><span data-stu-id="fc318-521">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="fc318-522">請執行下列步驟：</span><span class="sxs-lookup"><span data-stu-id="fc318-522">Perform the following steps:</span></span>

1. <span data-ttu-id="fc318-523">從裝載系統上的 [部署] 資料夾中刪除所有檔案和資料夾。</span><span class="sxs-lookup"><span data-stu-id="fc318-523">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="fc318-524">使用您的一般部署方法（例如 Visual Studio、PowerShell 或手動部署），將應用程式的 *發行* 資料夾內容重新部署至主機系統：</span><span class="sxs-lookup"><span data-stu-id="fc318-524">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="fc318-525">確認部署中有 *web.config* 檔案，且其內容正確無誤。</span><span class="sxs-lookup"><span data-stu-id="fc318-525">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="fc318-526">在 Azure App Service 上裝載時，請確認應用程式已部署到 `D:\home\site\wwwroot` 資料夾。</span><span class="sxs-lookup"><span data-stu-id="fc318-526">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="fc318-527">當應用程式是由 IIS 主控時，請確認應用程式已部署至 iis**管理員\*\*\*\*基本設定**中顯示的 iis**實體路徑**。</span><span class="sxs-lookup"><span data-stu-id="fc318-527">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="fc318-528">藉由比較主機系統上的部署與專案 *發佈* 資料夾的內容，確認已部署所有應用程式的檔案和資料夾。</span><span class="sxs-lookup"><span data-stu-id="fc318-528">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="fc318-529">如需已發佈的 ASP.NET Core 應用程式佈建的詳細資訊，請參閱 <xref:host-and-deploy/directory-structure> 。</span><span class="sxs-lookup"><span data-stu-id="fc318-529">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="fc318-530">如需 *web.config* 檔案的詳細資訊，請參閱 <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig> 。</span><span class="sxs-lookup"><span data-stu-id="fc318-530">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="fc318-531">500 內部伺服器錯誤</span><span class="sxs-lookup"><span data-stu-id="fc318-531">500 Internal Server Error</span></span>

<span data-ttu-id="fc318-532">應用程式啟動，但有錯誤導致伺服器無法完成要求。</span><span class="sxs-lookup"><span data-stu-id="fc318-532">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="fc318-533">此錯誤是在啟動或建立回應時，在應用程式的程式碼內發生。</span><span class="sxs-lookup"><span data-stu-id="fc318-533">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="fc318-534">回應可能未包含任何內容，或是回應可能在瀏覽器中以「500 內部伺服器錯誤」\*\* 的形式出現。</span><span class="sxs-lookup"><span data-stu-id="fc318-534">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="fc318-535">「應用程式事件記錄檔」通常會指出該應用程式已正常啟動。</span><span class="sxs-lookup"><span data-stu-id="fc318-535">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="fc318-536">從伺服器的觀點來看，這是正確的。</span><span class="sxs-lookup"><span data-stu-id="fc318-536">From the server's perspective, that's correct.</span></span> <span data-ttu-id="fc318-537">應用程式已啟動，但無法產生有效的回應。</span><span class="sxs-lookup"><span data-stu-id="fc318-537">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="fc318-538">請在伺服器上於命令提示字元中執行應用程式或啟用 ASP.NET Core 模組 stdout 記錄檔，以針對問題進行疑難排解。</span><span class="sxs-lookup"><span data-stu-id="fc318-538">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="fc318-539">500.0 同處理序處理常式載入失敗</span><span class="sxs-lookup"><span data-stu-id="fc318-539">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="fc318-540">背景工作處理序失敗。</span><span class="sxs-lookup"><span data-stu-id="fc318-540">The worker process fails.</span></span> <span data-ttu-id="fc318-541">應用程式未啟動。</span><span class="sxs-lookup"><span data-stu-id="fc318-541">The app doesn't start.</span></span>

<span data-ttu-id="fc318-542">[ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)找不到 .NET Core CLR，並找出 (*aspnetcorev2_inprocess.dll*) 的同進程要求處理常式。</span><span class="sxs-lookup"><span data-stu-id="fc318-542">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the .NET Core CLR and find the in-process request handler (*aspnetcorev2_inprocess.dll*).</span></span> <span data-ttu-id="fc318-543">請檢查︰</span><span class="sxs-lookup"><span data-stu-id="fc318-543">Check that:</span></span>

* <span data-ttu-id="fc318-544">應用程式以 [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet 套件或 [Microsoft.AspNetCore.App 中繼套件](xref:fundamentals/metapackage-app)為目標。</span><span class="sxs-lookup"><span data-stu-id="fc318-544">The app targets either the [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet package or the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
* <span data-ttu-id="fc318-545">應用程式設為目標的 ASP.NET Core 共用架構版本有安裝在目標機器上。</span><span class="sxs-lookup"><span data-stu-id="fc318-545">The version of the ASP.NET Core shared framework that the app targets is installed on the target machine.</span></span>

### <a name="5000-out-of-process-handler-load-failure"></a><span data-ttu-id="fc318-546">500.0 跨處理序處理常式載入失敗</span><span class="sxs-lookup"><span data-stu-id="fc318-546">500.0 Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="fc318-547">背景工作處理序失敗。</span><span class="sxs-lookup"><span data-stu-id="fc318-547">The worker process fails.</span></span> <span data-ttu-id="fc318-548">應用程式未啟動。</span><span class="sxs-lookup"><span data-stu-id="fc318-548">The app doesn't start.</span></span>

<span data-ttu-id="fc318-549">[ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)無法找到跨進程裝載要求處理常式。</span><span class="sxs-lookup"><span data-stu-id="fc318-549">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the out-of-process hosting request handler.</span></span> <span data-ttu-id="fc318-550">請確定 *aspnetcorev2_outofprocess.dll* 出現在子資料夾中，且位於 *aspnetcorev2.dll* 旁。</span><span class="sxs-lookup"><span data-stu-id="fc318-550">Make sure the *aspnetcorev2_outofprocess.dll* is present in a subfolder next to *aspnetcorev2.dll*.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="fc318-551">502.5 處理序失敗</span><span class="sxs-lookup"><span data-stu-id="fc318-551">502.5 Process Failure</span></span>

<span data-ttu-id="fc318-552">背景工作處理序失敗。</span><span class="sxs-lookup"><span data-stu-id="fc318-552">The worker process fails.</span></span> <span data-ttu-id="fc318-553">應用程式未啟動。</span><span class="sxs-lookup"><span data-stu-id="fc318-553">The app doesn't start.</span></span>

<span data-ttu-id="fc318-554">[ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)嘗試啟動背景工作處理序，但無法啟動。</span><span class="sxs-lookup"><span data-stu-id="fc318-554">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="fc318-555">通常從應用程式事件記錄檔和 ASP.NET Core 模組 stdout 記錄檔中的項目，即可判斷啟動失敗的原因。</span><span class="sxs-lookup"><span data-stu-id="fc318-555">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="fc318-556">因為目標 ASP.NET Core 共用架構的版本不存在，導致應用程式設定錯誤是常見的失敗狀況。</span><span class="sxs-lookup"><span data-stu-id="fc318-556">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="fc318-557">請檢查安裝在目標機器上的 ASP.NET Core 共用架構版本為何。</span><span class="sxs-lookup"><span data-stu-id="fc318-557">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="fc318-558">「共用的架構」\*\* 是一組安裝於電腦上且由 `Microsoft.AspNetCore.App` 之類的中繼套件所參考的組件 (*.dll* 檔案)。</span><span class="sxs-lookup"><span data-stu-id="fc318-558">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="fc318-559">中繼套件參考可以指定最低必要版本。</span><span class="sxs-lookup"><span data-stu-id="fc318-559">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="fc318-560">如需詳細資訊，請參閱[共用的架構](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="fc318-560">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="fc318-561">當裝載或應用程式設定錯誤造成背景工作處理序發生失敗時，會傳回 [502.5 處理序失敗]\*\* 錯誤頁面：</span><span class="sxs-lookup"><span data-stu-id="fc318-561">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="fc318-562">無法啟動應用程式 (ErrorCode '0x800700c1')</span><span class="sxs-lookup"><span data-stu-id="fc318-562">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="fc318-563">應用程式無法啟動，因為無法載入應用程式的組件 (*.dll*)。</span><span class="sxs-lookup"><span data-stu-id="fc318-563">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="fc318-564">當已發行的應用程式與 w3wp/iisexpress 處理序之間出現位元不符的情況時，就會發生此錯誤。</span><span class="sxs-lookup"><span data-stu-id="fc318-564">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="fc318-565">確認應用程式集區的 32 位元設定正確：</span><span class="sxs-lookup"><span data-stu-id="fc318-565">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="fc318-566">在 IIS 管理員的 [應用程式集區]\*\*\*\* 中，選取應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="fc318-566">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="fc318-567">在 [動作]\*\*\*\* 面板中，選取 [編輯應用程式集區]\*\*\*\* 下的 [進階設定]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="fc318-567">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="fc318-568">設定 [啟用 32 位元應用程式]\*\*\*\*：</span><span class="sxs-lookup"><span data-stu-id="fc318-568">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="fc318-569">如果部署 32 位元 (x86) 應用程式，請將值設定為 `True`。</span><span class="sxs-lookup"><span data-stu-id="fc318-569">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="fc318-570">如果部署 64 位元 (x64) 應用程式，請將值設定為 `False`。</span><span class="sxs-lookup"><span data-stu-id="fc318-570">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="fc318-571">確認 `<Platform>` 專案檔中的 MSBuild 屬性和應用程式的已發佈位之間沒有衝突。</span><span class="sxs-lookup"><span data-stu-id="fc318-571">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="fc318-572">連線重設</span><span class="sxs-lookup"><span data-stu-id="fc318-572">Connection reset</span></span>

<span data-ttu-id="fc318-573">如果是在傳送標頭之後才發生錯誤，則當發生錯誤時，伺服器已來不及傳送「500 內部伺服器錯誤」\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="fc318-573">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="fc318-574">通常是在將回應的複雜物件序列化的期間發生錯誤時，會發生此錯誤。</span><span class="sxs-lookup"><span data-stu-id="fc318-574">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="fc318-575">這類錯誤會在用戶端上顯示為「連線重設」\*\* 錯誤。</span><span class="sxs-lookup"><span data-stu-id="fc318-575">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="fc318-576">[應用程式記錄](xref:fundamentals/logging/index)可協助針對這些類型的錯誤進行疑難排解。</span><span class="sxs-lookup"><span data-stu-id="fc318-576">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="fc318-577">預設啟動限制</span><span class="sxs-lookup"><span data-stu-id="fc318-577">Default startup limits</span></span>

<span data-ttu-id="fc318-578">[ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)的設定預設*startupTimeLimit*為120秒。</span><span class="sxs-lookup"><span data-stu-id="fc318-578">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="fc318-579">保留預設值時，在模組記錄處理序失敗之前，應用程式最多可花費兩分鐘來進行啟動。</span><span class="sxs-lookup"><span data-stu-id="fc318-579">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="fc318-580">如需有關設定模組的資訊，請參閱 [aspNetCore 元素的屬性](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)。</span><span class="sxs-lookup"><span data-stu-id="fc318-580">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="fc318-581">針對 Azure App Service 進行疑難排解</span><span class="sxs-lookup"><span data-stu-id="fc318-581">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="fc318-582">應用程式事件記錄檔 (Azure App Service) </span><span class="sxs-lookup"><span data-stu-id="fc318-582">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="fc318-583">若要存取「應用程式事件記錄檔」，請使用 Azure 入口網站中的 [診斷並解決問題]\*\*\*\* 刀鋒視窗：</span><span class="sxs-lookup"><span data-stu-id="fc318-583">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="fc318-584">在 Azure 入口網站的 [應用程式服務]\*\*\*\* 中，開啟應用程式。</span><span class="sxs-lookup"><span data-stu-id="fc318-584">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="fc318-585">選取 [診斷並解決問題]。</span><span class="sxs-lookup"><span data-stu-id="fc318-585">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="fc318-586">選取 [診斷工具]\*\*\*\* 標題。</span><span class="sxs-lookup"><span data-stu-id="fc318-586">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="fc318-587">在 [支援工具]\*\*\*\* 下，選取 [應用程式事件]\*\*\*\* 按鈕。</span><span class="sxs-lookup"><span data-stu-id="fc318-587">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="fc318-588">檢查 [來源]\*\*\*\* 資料行中 *IIS AspNetCoreModule* 或 *IIS AspNetCoreModule V2* 項目所提供的最新錯誤。</span><span class="sxs-lookup"><span data-stu-id="fc318-588">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="fc318-589">除了使用 [診斷並解決問題]\*\*\*\* 刀鋒視窗之外，也可以使用 [Kudu](https://github.com/projectkudu/kudu/wiki) 來直接檢查「應用程式事件記錄檔」：</span><span class="sxs-lookup"><span data-stu-id="fc318-589">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="fc318-590">在 [開發工具]\*\*\*\* 區域中，開啟 [進階工具]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="fc318-590">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="fc318-591">選取 [**移 &rarr; 至**] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="fc318-591">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="fc318-592">Kudu 主控台會在新的瀏覽器索引標籤或視窗中開啟。</span><span class="sxs-lookup"><span data-stu-id="fc318-592">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="fc318-593">使用頁面頂端的導覽列，開啟 [偵錯主控台]\*\*\*\*，然後選取 [CMD]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="fc318-593">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="fc318-594">開啟 **LogFiles** 資料夾。</span><span class="sxs-lookup"><span data-stu-id="fc318-594">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="fc318-595">選取 *eventlog.xml* 檔案旁邊的鉛筆圖示。</span><span class="sxs-lookup"><span data-stu-id="fc318-595">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="fc318-596">檢查記錄檔。</span><span class="sxs-lookup"><span data-stu-id="fc318-596">Examine the log.</span></span> <span data-ttu-id="fc318-597">捲動至記錄檔的底部以查看最新事件。</span><span class="sxs-lookup"><span data-stu-id="fc318-597">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="fc318-598">在 Kudu 主控台中執行應用程式</span><span class="sxs-lookup"><span data-stu-id="fc318-598">Run the app in the Kudu console</span></span>

<span data-ttu-id="fc318-599">許多啟動錯誤不會在「應用程式事件記錄檔」中產生實用的資訊。</span><span class="sxs-lookup"><span data-stu-id="fc318-599">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="fc318-600">您可以在 [Kudu](https://github.com/projectkudu/kudu/wiki)「遠端執行主控台」中執行應用程式來探索錯誤：</span><span class="sxs-lookup"><span data-stu-id="fc318-600">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="fc318-601">在 [開發工具]\*\*\*\* 區域中，開啟 [進階工具]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="fc318-601">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="fc318-602">選取 [**移 &rarr; 至**] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="fc318-602">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="fc318-603">Kudu 主控台會在新的瀏覽器索引標籤或視窗中開啟。</span><span class="sxs-lookup"><span data-stu-id="fc318-603">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="fc318-604">使用頁面頂端的導覽列，開啟 [偵錯主控台]\*\*\*\*，然後選取 [CMD]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="fc318-604">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="fc318-605">測試 32 位元 (x86) 應用程式</span><span class="sxs-lookup"><span data-stu-id="fc318-605">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="fc318-606">**目前的版本**</span><span class="sxs-lookup"><span data-stu-id="fc318-606">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="fc318-607">執行應用程式：</span><span class="sxs-lookup"><span data-stu-id="fc318-607">Run the app:</span></span>
   * <span data-ttu-id="fc318-608">如果應用程式是[架構相依部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="fc318-608">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="fc318-609">如果應用程式是[自封式部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="fc318-609">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="fc318-610">應用程式的主控台輸出 (顯示任何錯誤) 會以管道傳送至 Kudu 主控台。</span><span class="sxs-lookup"><span data-stu-id="fc318-610">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="fc318-611">**在預覽版上執行的架構相依部署**</span><span class="sxs-lookup"><span data-stu-id="fc318-611">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="fc318-612">*要求安裝 ASP.NET Core {VERSION} (x86) 執行階段網站延伸模組。*</span><span class="sxs-lookup"><span data-stu-id="fc318-612">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="fc318-613">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` 是執行階段版本)</span><span class="sxs-lookup"><span data-stu-id="fc318-613">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="fc318-614">執行應用程式：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="fc318-614">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="fc318-615">應用程式的主控台輸出 (顯示任何錯誤) 會以管道傳送至 Kudu 主控台。</span><span class="sxs-lookup"><span data-stu-id="fc318-615">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="fc318-616">測試 64 位元 (x64) 應用程式</span><span class="sxs-lookup"><span data-stu-id="fc318-616">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="fc318-617">**目前的版本**</span><span class="sxs-lookup"><span data-stu-id="fc318-617">**Current release**</span></span>

* <span data-ttu-id="fc318-618">如果應用程式是 64 位元 (x64) [架構相依部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="fc318-618">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="fc318-619">執行應用程式：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="fc318-619">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="fc318-620">如果應用程式是[自封式部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="fc318-620">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="fc318-621">執行應用程式：`{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="fc318-621">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="fc318-622">應用程式的主控台輸出 (顯示任何錯誤) 會以管道傳送至 Kudu 主控台。</span><span class="sxs-lookup"><span data-stu-id="fc318-622">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="fc318-623">**在預覽版上執行的架構相依部署**</span><span class="sxs-lookup"><span data-stu-id="fc318-623">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="fc318-624">*要求安裝 ASP.NET Core {VERSION} (x64) 執行階段網站延伸模組。*</span><span class="sxs-lookup"><span data-stu-id="fc318-624">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="fc318-625">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` 是執行階段版本)</span><span class="sxs-lookup"><span data-stu-id="fc318-625">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="fc318-626">執行應用程式：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="fc318-626">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="fc318-627">應用程式的主控台輸出 (顯示任何錯誤) 會以管道傳送至 Kudu 主控台。</span><span class="sxs-lookup"><span data-stu-id="fc318-627">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="fc318-628">ASP.NET Core Module stdout 記錄 (Azure App Service) </span><span class="sxs-lookup"><span data-stu-id="fc318-628">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="fc318-629">ASP.NET Core 模組 stdout 記錄檔通常會記錄「應用程式事件記錄檔」中所沒有的實用訊息。</span><span class="sxs-lookup"><span data-stu-id="fc318-629">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="fc318-630">啟用及檢視 stdout 記錄檔：</span><span class="sxs-lookup"><span data-stu-id="fc318-630">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="fc318-631">在 Azure 入口網站中，瀏覽至 [診斷並解決問題]\*\*\*\* 刀鋒視窗。</span><span class="sxs-lookup"><span data-stu-id="fc318-631">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="fc318-632">在 [選取問題類別]\*\*\*\* 底下，選取 [Web 應用程式停止運作]\*\*\*\* 按鈕。</span><span class="sxs-lookup"><span data-stu-id="fc318-632">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="fc318-633">在 [建議的解決方案]**[啟用 Stdout 記錄檔重新導向]** > \*\*\*\* 底下，選取用來**開啟 Kudu 主控台以編輯 Web.Config** 的按鈕。</span><span class="sxs-lookup"><span data-stu-id="fc318-633">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="fc318-634">在 Kudu [診斷主控台]\*\*\*\* 中，將資料夾開啟至路徑 [site]\*\*\*\* > [wwwroot]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="fc318-634">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="fc318-635">向下捲動以顯露位於清單底部的 *web.config* 檔案。</span><span class="sxs-lookup"><span data-stu-id="fc318-635">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="fc318-636">按一下 *web.config* 檔案旁邊的鉛筆圖示。</span><span class="sxs-lookup"><span data-stu-id="fc318-636">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="fc318-637">將 **stdoutLogEnabled** 設定為 `true`，並將 **stdoutLogFile** 路徑變更為：`\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="fc318-637">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="fc318-638">選取 [儲存]\*\*\*\* 以儲存已更新的 *web.config* 檔案。</span><span class="sxs-lookup"><span data-stu-id="fc318-638">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="fc318-639">對應用程式發出要求。</span><span class="sxs-lookup"><span data-stu-id="fc318-639">Make a request to the app.</span></span>
1. <span data-ttu-id="fc318-640">返回 Azure 入口網站。</span><span class="sxs-lookup"><span data-stu-id="fc318-640">Return to the Azure portal.</span></span> <span data-ttu-id="fc318-641">選取 [開發工具]\*\*\*\* 區域中的 [進階工具]\*\*\*\* 刀鋒視窗。</span><span class="sxs-lookup"><span data-stu-id="fc318-641">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="fc318-642">選取 [**移 &rarr; 至**] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="fc318-642">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="fc318-643">Kudu 主控台會在新的瀏覽器索引標籤或視窗中開啟。</span><span class="sxs-lookup"><span data-stu-id="fc318-643">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="fc318-644">使用頁面頂端的導覽列，開啟 [偵錯主控台]\*\*\*\*，然後選取 [CMD]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="fc318-644">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="fc318-645">選取 **LogFiles** 資料夾。</span><span class="sxs-lookup"><span data-stu-id="fc318-645">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="fc318-646">檢查 [修改時間]\*\*\*\* 資料行，然後選取鉛筆圖示來編輯修改日期最新的 stdout 記錄檔。</span><span class="sxs-lookup"><span data-stu-id="fc318-646">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="fc318-647">當記錄檔開啟時，會顯示錯誤。</span><span class="sxs-lookup"><span data-stu-id="fc318-647">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="fc318-648">完成疑難排解時，請停用 stdout 記錄：</span><span class="sxs-lookup"><span data-stu-id="fc318-648">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="fc318-649">在 Kudu [診斷主控台]\*\*\*\* 中，返回路徑 [site]\*\*\*\* > [wwwroot]\*\*\*\* 以顯露 *web.config* 檔案。</span><span class="sxs-lookup"><span data-stu-id="fc318-649">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="fc318-650">再次選取鉛筆圖示來開啟 **web.config** 檔案。</span><span class="sxs-lookup"><span data-stu-id="fc318-650">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="fc318-651">將 **stdoutLogEnabled** 設定為 `false`。</span><span class="sxs-lookup"><span data-stu-id="fc318-651">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="fc318-652">選取 [儲存]\*\*\*\* 以儲存檔案。</span><span class="sxs-lookup"><span data-stu-id="fc318-652">Select **Save** to save the file.</span></span>

<span data-ttu-id="fc318-653">如需詳細資訊，請參閱<xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>。</span><span class="sxs-lookup"><span data-stu-id="fc318-653">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="fc318-654">如果無法停用 stdout 記錄檔，可能會造成應用程式或伺服器發生失敗。</span><span class="sxs-lookup"><span data-stu-id="fc318-654">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="fc318-655">因為它並沒有記錄檔大小或數量上的限制。</span><span class="sxs-lookup"><span data-stu-id="fc318-655">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="fc318-656">請只在針對應用程式啟動問題進行疑難排解時，才使用 stdout 記錄。</span><span class="sxs-lookup"><span data-stu-id="fc318-656">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="fc318-657">針對 ASP.NET Core 應用程式啟動後的一般記錄，請使用會限制記錄檔大小並輪替記錄檔的記錄程式庫。</span><span class="sxs-lookup"><span data-stu-id="fc318-657">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="fc318-658">如需詳細資訊，請參閱[協力廠商記錄提供者](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="fc318-658">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="fc318-659">ASP.NET Core 模組 debug log (Azure App Service) </span><span class="sxs-lookup"><span data-stu-id="fc318-659">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="fc318-660">ASP.NET Core 模組偵錯記錄提供 ASP.NET Core 模組中其他且更深入的記錄。</span><span class="sxs-lookup"><span data-stu-id="fc318-660">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="fc318-661">啟用及檢視 stdout 記錄檔：</span><span class="sxs-lookup"><span data-stu-id="fc318-661">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="fc318-662">若要啟用增強型診斷記錄，請執行下列任一動作：</span><span class="sxs-lookup"><span data-stu-id="fc318-662">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="fc318-663">遵循[增強型診斷記錄](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中的指示，來設定應用程式進行增強型診斷記錄。</span><span class="sxs-lookup"><span data-stu-id="fc318-663">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="fc318-664">重新部署應用程式。</span><span class="sxs-lookup"><span data-stu-id="fc318-664">Redeploy the app.</span></span>
   * <span data-ttu-id="fc318-665">使用 Kudu 主控台，將[增強型診斷記錄](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中所顯示的 `<handlerSettings>` 新增至即時應用程式的 *web.config* 檔案：</span><span class="sxs-lookup"><span data-stu-id="fc318-665">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="fc318-666">在 [開發工具]\*\*\*\* 區域中，開啟 [進階工具]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="fc318-666">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="fc318-667">選取 [**移 &rarr; 至**] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="fc318-667">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="fc318-668">Kudu 主控台會在新的瀏覽器索引標籤或視窗中開啟。</span><span class="sxs-lookup"><span data-stu-id="fc318-668">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="fc318-669">使用頁面頂端的導覽列，開啟 [偵錯主控台]\*\*\*\*，然後選取 [CMD]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="fc318-669">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="fc318-670">將資料夾開啟至路徑**網站**  >  **wwwroot**。</span><span class="sxs-lookup"><span data-stu-id="fc318-670">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="fc318-671">選取鉛筆圖示來編輯 *web.config* 檔案。</span><span class="sxs-lookup"><span data-stu-id="fc318-671">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="fc318-672">新增 `<handlerSettings>` 區段，如[增強型診斷記錄](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中所示。</span><span class="sxs-lookup"><span data-stu-id="fc318-672">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="fc318-673">選取 [儲存] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="fc318-673">Select the **Save** button.</span></span>
1. <span data-ttu-id="fc318-674">在 [開發工具]\*\*\*\* 區域中，開啟 [進階工具]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="fc318-674">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="fc318-675">選取 [**移 &rarr; 至**] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="fc318-675">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="fc318-676">Kudu 主控台會在新的瀏覽器索引標籤或視窗中開啟。</span><span class="sxs-lookup"><span data-stu-id="fc318-676">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="fc318-677">使用頁面頂端的導覽列，開啟 [偵錯主控台]\*\*\*\*，然後選取 [CMD]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="fc318-677">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="fc318-678">將資料夾開啟至路徑**網站**  >  **wwwroot**。</span><span class="sxs-lookup"><span data-stu-id="fc318-678">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="fc318-679">如果未提供 *aspnetcore-debug.log* 檔案的路徑，該檔案會顯示在清單中。</span><span class="sxs-lookup"><span data-stu-id="fc318-679">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="fc318-680">如果已提供路徑，請巡覽至記錄檔的位置。</span><span class="sxs-lookup"><span data-stu-id="fc318-680">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="fc318-681">使用檔案名稱旁的鉛筆圖示來開啟記錄檔。</span><span class="sxs-lookup"><span data-stu-id="fc318-681">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="fc318-682">完成疑難排解時，請停用偵錯記錄：</span><span class="sxs-lookup"><span data-stu-id="fc318-682">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="fc318-683">若要停用增強型偵錯記錄，請執行下列任一動作：</span><span class="sxs-lookup"><span data-stu-id="fc318-683">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="fc318-684">從 *web.config* 檔案本機移除 `<handlerSettings>` 並重新部署應用程式。</span><span class="sxs-lookup"><span data-stu-id="fc318-684">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="fc318-685">使用 Kudu 主控台來編輯 *web.config* 檔案並移除 `<handlerSettings>` 區段。</span><span class="sxs-lookup"><span data-stu-id="fc318-685">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="fc318-686">儲存檔案。</span><span class="sxs-lookup"><span data-stu-id="fc318-686">Save the file.</span></span>

<span data-ttu-id="fc318-687">如需詳細資訊，請參閱<xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>。</span><span class="sxs-lookup"><span data-stu-id="fc318-687">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="fc318-688">無法停用偵錯記錄，可能會造成應用程式或伺服器發生失敗。</span><span class="sxs-lookup"><span data-stu-id="fc318-688">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="fc318-689">記錄檔大小沒有任何限制。</span><span class="sxs-lookup"><span data-stu-id="fc318-689">There's no limit on log file size.</span></span> <span data-ttu-id="fc318-690">請只在針對應用程式啟動問題進行疑難排解時，才使用偵錯記錄。</span><span class="sxs-lookup"><span data-stu-id="fc318-690">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="fc318-691">針對 ASP.NET Core 應用程式啟動後的一般記錄，請使用會限制記錄檔大小並輪替記錄檔的記錄程式庫。</span><span class="sxs-lookup"><span data-stu-id="fc318-691">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="fc318-692">如需詳細資訊，請參閱[協力廠商記錄提供者](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="fc318-692">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="fc318-693">應用程式 (Azure App Service 的緩慢或懸掛) </span><span class="sxs-lookup"><span data-stu-id="fc318-693">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="fc318-694">當應用程式針對要求回應緩慢或無回應時，請參閱下列文章：</span><span class="sxs-lookup"><span data-stu-id="fc318-694">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="fc318-695">針對 Azure App Service 中 Web 應用程式效能變慢的問題進行疑難排解</span><span class="sxs-lookup"><span data-stu-id="fc318-695">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* <span data-ttu-id="fc318-696">[使用損毀診斷程式網站延伸模組來擷取傾印，以取得 Azure Web 應用程式上的間歇性例外狀況或效能問題](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="fc318-696">[Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)</span></span>

### <a name="monitoring-blades"></a><span data-ttu-id="fc318-697">監視刀鋒視窗</span><span class="sxs-lookup"><span data-stu-id="fc318-697">Monitoring blades</span></span>

<span data-ttu-id="fc318-698">監視刀鋒視窗為本主題稍早所述的方法提供替代的疑難排解體驗。</span><span class="sxs-lookup"><span data-stu-id="fc318-698">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="fc318-699">這些刀鋒視窗可用來診斷 500 系列的錯誤。</span><span class="sxs-lookup"><span data-stu-id="fc318-699">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="fc318-700">請確認已安裝 ASP.NET Core 延伸模組。</span><span class="sxs-lookup"><span data-stu-id="fc318-700">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="fc318-701">如果未安裝這些延伸模組，請手動安裝：</span><span class="sxs-lookup"><span data-stu-id="fc318-701">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="fc318-702">在 [開發工具]\*\*\*\* 刀鋒視窗區段中，選取 [延伸模組]\*\*\*\* 刀鋒視窗。</span><span class="sxs-lookup"><span data-stu-id="fc318-702">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="fc318-703">[ASP.NET Core 延伸模組]\*\*\*\* 應該會出現在清單中。</span><span class="sxs-lookup"><span data-stu-id="fc318-703">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="fc318-704">如果未安裝這些延伸模組，請選取 [新增]\*\*\*\* 按鈕。</span><span class="sxs-lookup"><span data-stu-id="fc318-704">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="fc318-705">從清單中選擇 [ASP.NET Core 延伸模組]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="fc318-705">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="fc318-706">選取 [確定]\*\*\*\* 以接受法律條款。</span><span class="sxs-lookup"><span data-stu-id="fc318-706">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="fc318-707">在 [新增延伸模組]\*\*\*\* 刀鋒視窗上，選取 [確定]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="fc318-707">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="fc318-708">成功安裝延伸模組時，會有資訊快顯訊息提供指示。</span><span class="sxs-lookup"><span data-stu-id="fc318-708">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="fc318-709">如果未啟用 stdout 記錄，請依照下列步驟進行操作：</span><span class="sxs-lookup"><span data-stu-id="fc318-709">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="fc318-710">在 Azure 入口網站中，選取 [開發工具]\*\*\*\* 區域中的 [進階工具]\*\*\*\* 刀鋒視窗。</span><span class="sxs-lookup"><span data-stu-id="fc318-710">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="fc318-711">選取 [**移 &rarr; 至**] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="fc318-711">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="fc318-712">Kudu 主控台會在新的瀏覽器索引標籤或視窗中開啟。</span><span class="sxs-lookup"><span data-stu-id="fc318-712">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="fc318-713">使用頁面頂端的導覽列，開啟 [偵錯主控台]\*\*\*\*，然後選取 [CMD]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="fc318-713">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="fc318-714">將資料夾開啟至路徑 [site]**[wwwroot]** > \*\*\*\*，然後向下捲動以顯露位於清單底部的 *web.config* 檔案。</span><span class="sxs-lookup"><span data-stu-id="fc318-714">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="fc318-715">按一下 *web.config* 檔案旁邊的鉛筆圖示。</span><span class="sxs-lookup"><span data-stu-id="fc318-715">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="fc318-716">將 **stdoutLogEnabled** 設定為 `true`，並將 **stdoutLogFile** 路徑變更為：`\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="fc318-716">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="fc318-717">選取 [儲存]\*\*\*\* 以儲存已更新的 *web.config* 檔案。</span><span class="sxs-lookup"><span data-stu-id="fc318-717">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="fc318-718">繼續接著啟用診斷記錄：</span><span class="sxs-lookup"><span data-stu-id="fc318-718">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="fc318-719">在 Azure 入口網站中，選取 [診斷記錄檔]\*\*\*\* 刀鋒視窗。</span><span class="sxs-lookup"><span data-stu-id="fc318-719">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="fc318-720">選取 [應用程式記錄 (檔案系統)]\*\*\*\* 和 [詳細錯誤訊息]\*\*\*\*.的 [開啟]\*\*\*\* 開關。</span><span class="sxs-lookup"><span data-stu-id="fc318-720">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="fc318-721">選取刀鋒視窗頂端的 [儲存]\*\*\*\* 按鈕。</span><span class="sxs-lookup"><span data-stu-id="fc318-721">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="fc318-722">若要包含失敗要求追蹤 (也稱為「失敗要求事件緩衝處理」(FREB) 記錄)，請選取 [失敗要求的追蹤]\*\*\*\* 的 [開啟]\*\*\*\* 開關。</span><span class="sxs-lookup"><span data-stu-id="fc318-722">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="fc318-723">選取 [記錄資料流]\*\*\*\* 刀鋒視窗 (列在入口網站中緊接在 [診斷記錄檔]\*\*\*\* 刀鋒視窗之下)。</span><span class="sxs-lookup"><span data-stu-id="fc318-723">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="fc318-724">對應用程式發出要求。</span><span class="sxs-lookup"><span data-stu-id="fc318-724">Make a request to the app.</span></span>
1. <span data-ttu-id="fc318-725">在記錄資料流資料內，會指出錯誤的原因。</span><span class="sxs-lookup"><span data-stu-id="fc318-725">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="fc318-726">完成疑難排解時，請務必停用 stdout 記錄。</span><span class="sxs-lookup"><span data-stu-id="fc318-726">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="fc318-727">檢視失敗要求追蹤記錄檔 (FREB 記錄檔)：</span><span class="sxs-lookup"><span data-stu-id="fc318-727">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="fc318-728">在 Azure 入口網站中，瀏覽至 [診斷並解決問題]\*\*\*\* 刀鋒視窗。</span><span class="sxs-lookup"><span data-stu-id="fc318-728">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="fc318-729">從資訊看板的 [支援工具]\*\*\*\* 區域中，選取 [失敗要求追蹤記錄檔]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="fc318-729">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="fc318-730">如需詳細資訊，請參閱[＜在 Azure App Service 中針對 Web 應用程式啟用診斷記錄功能＞主題的＜失敗要求追蹤＞一節](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces)和[Azure Web 應用程式的應用程式效能常見問題集：如何開啟失敗要求追蹤？](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)。</span><span class="sxs-lookup"><span data-stu-id="fc318-730">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="fc318-731">如需詳細資訊，請參閱[在 Azure App Service 中針對 Web 應用程式啟用診斷記錄功能](/azure/app-service/web-sites-enable-diagnostic-log)。</span><span class="sxs-lookup"><span data-stu-id="fc318-731">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="fc318-732">如果無法停用 stdout 記錄檔，可能會造成應用程式或伺服器發生失敗。</span><span class="sxs-lookup"><span data-stu-id="fc318-732">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="fc318-733">因為它並沒有記錄檔大小或數量上的限制。</span><span class="sxs-lookup"><span data-stu-id="fc318-733">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="fc318-734">針對 ASP.NET Core 應用程式中的例行性記錄，請使用會限制記錄檔大小並輪替記錄檔的記錄程式庫。</span><span class="sxs-lookup"><span data-stu-id="fc318-734">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="fc318-735">如需詳細資訊，請參閱[協力廠商記錄提供者](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="fc318-735">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="fc318-736">針對 IIS 進行疑難排解</span><span class="sxs-lookup"><span data-stu-id="fc318-736">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="fc318-737">應用程式事件記錄檔 (IIS) </span><span class="sxs-lookup"><span data-stu-id="fc318-737">Application Event Log (IIS)</span></span>

<span data-ttu-id="fc318-738">存取「應用程式事件記錄檔」：</span><span class="sxs-lookup"><span data-stu-id="fc318-738">Access the Application Event Log:</span></span>

1. <span data-ttu-id="fc318-739">開啟 [開始] 功能表、搜尋 *事件檢視器*，然後選取 **事件檢視器** 應用程式。</span><span class="sxs-lookup"><span data-stu-id="fc318-739">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="fc318-740">在 [事件檢視器]\*\*\*\* 中，開啟 [Windows 記錄]\*\*\*\* 節點。</span><span class="sxs-lookup"><span data-stu-id="fc318-740">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="fc318-741">選取 [應用程式]\*\*\*\* 以開啟「應用程式事件記錄檔」。</span><span class="sxs-lookup"><span data-stu-id="fc318-741">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="fc318-742">搜尋與失敗應用程式相關的錯誤。</span><span class="sxs-lookup"><span data-stu-id="fc318-742">Search for errors associated with the failing app.</span></span> <span data-ttu-id="fc318-743">錯誤在 [來源]\*\* 資料行中的值會是 *IIS AspNetCore Module* 或 *IIS Express AspNetCore Module*。</span><span class="sxs-lookup"><span data-stu-id="fc318-743">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="fc318-744">在命令提示字元中執行應用程式</span><span class="sxs-lookup"><span data-stu-id="fc318-744">Run the app at a command prompt</span></span>

<span data-ttu-id="fc318-745">許多啟動錯誤不會在「應用程式事件記錄檔」中產生實用的資訊。</span><span class="sxs-lookup"><span data-stu-id="fc318-745">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="fc318-746">您可以藉由在主控系統上的命令提示字元中執行應用程式，來找出一些錯誤的原因。</span><span class="sxs-lookup"><span data-stu-id="fc318-746">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="fc318-747">與 Framework 相依的部署</span><span class="sxs-lookup"><span data-stu-id="fc318-747">Framework-dependent deployment</span></span>

<span data-ttu-id="fc318-748">如果應用程式是[架構相依部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="fc318-748">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="fc318-749">在命令提示字元中，瀏覽至部署資料夾，然後使用 *dotnet.exe* 來執行應用程式組件以執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="fc318-749">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="fc318-750">在下列命令中，將應用程式的元件名稱取代為 \<assembly_name> ： `dotnet .\<assembly_name>.dll` 。</span><span class="sxs-lookup"><span data-stu-id="fc318-750">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="fc318-751">來自應用程式的主控台輸出若有顯示任何錯誤，就會寫入至主控台視窗。</span><span class="sxs-lookup"><span data-stu-id="fc318-751">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="fc318-752">如果是在對應用程式發出要求時發生錯誤，請對 Kestrel 進行接聽的主機和連接埠發出要求。</span><span class="sxs-lookup"><span data-stu-id="fc318-752">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="fc318-753">如果使用預設主機和連接埠，請對 `http://localhost:5000/` 發出要求。</span><span class="sxs-lookup"><span data-stu-id="fc318-753">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="fc318-754">如果應用程式在 Kestrel 端點位址正常回應，則問題與主機組態有關的機率較大，而與應用程式本身有關的機率較小。</span><span class="sxs-lookup"><span data-stu-id="fc318-754">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="fc318-755">自封式部署</span><span class="sxs-lookup"><span data-stu-id="fc318-755">Self-contained deployment</span></span>

<span data-ttu-id="fc318-756">如果應用程式是[自封式部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="fc318-756">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="fc318-757">在命令提示字元中，瀏覽至部署資料夾，然後執行應用程式的可執行檔。</span><span class="sxs-lookup"><span data-stu-id="fc318-757">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="fc318-758">在下列命令中，將應用程式的元件名稱取代為 \<assembly_name> ： `<assembly_name>.exe` 。</span><span class="sxs-lookup"><span data-stu-id="fc318-758">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="fc318-759">來自應用程式的主控台輸出若有顯示任何錯誤，就會寫入至主控台視窗。</span><span class="sxs-lookup"><span data-stu-id="fc318-759">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="fc318-760">如果是在對應用程式發出要求時發生錯誤，請對 Kestrel 進行接聽的主機和連接埠發出要求。</span><span class="sxs-lookup"><span data-stu-id="fc318-760">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="fc318-761">如果使用預設主機和連接埠，請對 `http://localhost:5000/` 發出要求。</span><span class="sxs-lookup"><span data-stu-id="fc318-761">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="fc318-762">如果應用程式在 Kestrel 端點位址正常回應，則問題與主機組態有關的機率較大，而與應用程式本身有關的機率較小。</span><span class="sxs-lookup"><span data-stu-id="fc318-762">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="fc318-763">ASP.NET Core Module stdout log (IIS) </span><span class="sxs-lookup"><span data-stu-id="fc318-763">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="fc318-764">啟用及檢視 stdout 記錄檔：</span><span class="sxs-lookup"><span data-stu-id="fc318-764">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="fc318-765">瀏覽至主控系統上網站的部署資料夾。</span><span class="sxs-lookup"><span data-stu-id="fc318-765">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="fc318-766">如果 [logs]\*\* 資料夾不存在，請建立該資料夾。</span><span class="sxs-lookup"><span data-stu-id="fc318-766">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="fc318-767">如需有關如何讓 MSBuild 在部署中自動建立 [logs]\*\* 資料夾的指示，請參閱[目錄結構](xref:host-and-deploy/directory-structure)主題。</span><span class="sxs-lookup"><span data-stu-id="fc318-767">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="fc318-768">編輯 *web.config* 檔案。</span><span class="sxs-lookup"><span data-stu-id="fc318-768">Edit the *web.config* file.</span></span> <span data-ttu-id="fc318-769">將 **stdoutLogEnabled** 設定為 `true`，並將 **stdoutLogFile** 路徑變更為指向 [logs]\*\* 資料夾 (例如 `.\logs\stdout`)。</span><span class="sxs-lookup"><span data-stu-id="fc318-769">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="fc318-770">路徑中的 `stdout` 是記錄檔名稱前置詞。</span><span class="sxs-lookup"><span data-stu-id="fc318-770">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="fc318-771">建立記錄檔時，系統會自動新增時間戳記、處理序識別碼及副檔名。</span><span class="sxs-lookup"><span data-stu-id="fc318-771">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="fc318-772">使用 `stdout` 作為檔案名稱前置詞時，一般記錄檔會命名為 *stdout_20180205184032_5412.log*。</span><span class="sxs-lookup"><span data-stu-id="fc318-772">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="fc318-773">請確定您的應用程式集區身分識別具有 *logs* 資料夾的寫入權限。</span><span class="sxs-lookup"><span data-stu-id="fc318-773">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="fc318-774">儲存已更新的 *web.config* 檔案。</span><span class="sxs-lookup"><span data-stu-id="fc318-774">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="fc318-775">對應用程式發出要求。</span><span class="sxs-lookup"><span data-stu-id="fc318-775">Make a request to the app.</span></span>
1. <span data-ttu-id="fc318-776">瀏覽至 [logs]\*\* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="fc318-776">Navigate to the *logs* folder.</span></span> <span data-ttu-id="fc318-777">尋找並開啟最新的 stdout 記錄檔。</span><span class="sxs-lookup"><span data-stu-id="fc318-777">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="fc318-778">研究記錄檔以了解錯誤。</span><span class="sxs-lookup"><span data-stu-id="fc318-778">Study the log for errors.</span></span>

<span data-ttu-id="fc318-779">完成疑難排解時，請停用 stdout 記錄：</span><span class="sxs-lookup"><span data-stu-id="fc318-779">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="fc318-780">編輯 *web.config* 檔案。</span><span class="sxs-lookup"><span data-stu-id="fc318-780">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="fc318-781">將 **stdoutLogEnabled** 設定為 `false`。</span><span class="sxs-lookup"><span data-stu-id="fc318-781">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="fc318-782">儲存檔案。</span><span class="sxs-lookup"><span data-stu-id="fc318-782">Save the file.</span></span>

<span data-ttu-id="fc318-783">如需詳細資訊，請參閱<xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>。</span><span class="sxs-lookup"><span data-stu-id="fc318-783">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="fc318-784">如果無法停用 stdout 記錄檔，可能會造成應用程式或伺服器發生失敗。</span><span class="sxs-lookup"><span data-stu-id="fc318-784">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="fc318-785">因為它並沒有記錄檔大小或數量上的限制。</span><span class="sxs-lookup"><span data-stu-id="fc318-785">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="fc318-786">針對 ASP.NET Core 應用程式中的例行性記錄，請使用會限制記錄檔大小並輪替記錄檔的記錄程式庫。</span><span class="sxs-lookup"><span data-stu-id="fc318-786">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="fc318-787">如需詳細資訊，請參閱[協力廠商記錄提供者](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="fc318-787">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="fc318-788">ASP.NET Core 模組 debug log (IIS) </span><span class="sxs-lookup"><span data-stu-id="fc318-788">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="fc318-789">將下列處理常式設定新增至應用程式的 *web.config* 檔，以啟用 ASP.NET Core 模組的偵錯工具記錄：</span><span class="sxs-lookup"><span data-stu-id="fc318-789">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="fc318-790">確認為記錄指定的路徑存在，而且應用程式集區的身分識別具有該位置的寫入權限。</span><span class="sxs-lookup"><span data-stu-id="fc318-790">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="fc318-791">如需詳細資訊，請參閱<xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>。</span><span class="sxs-lookup"><span data-stu-id="fc318-791">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="fc318-792">啟用開發人員例外頁面</span><span class="sxs-lookup"><span data-stu-id="fc318-792">Enable the Developer Exception Page</span></span>

<span data-ttu-id="fc318-793">在開發環境中，可以將 `ASPNETCORE_ENVIRONMENT` [環境變數新增至 web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) 來執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="fc318-793">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="fc318-794">只要主機產生器上的 `UseEnvironment` 不會覆寫應用程式啟動內的環境，設定該環境變數便可允許在應用程式執行時顯示[開發人員例外狀況頁面](xref:fundamentals/error-handling)。</span><span class="sxs-lookup"><span data-stu-id="fc318-794">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

<span data-ttu-id="fc318-795">只有在沒有對網際網路公開的暫存和測試伺服器上使用時，才建議為 `ASPNETCORE_ENVIRONMENT` 設定環境變數。</span><span class="sxs-lookup"><span data-stu-id="fc318-795">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="fc318-796">進行疑難排解之後，請從 *web.config* 檔案中移除環境變數。</span><span class="sxs-lookup"><span data-stu-id="fc318-796">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="fc318-797">如需有關在 *web.config* 中設定環境變數的資訊，請參閱 [aspNetCore 的 environmentVariables 子元素](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)。</span><span class="sxs-lookup"><span data-stu-id="fc318-797">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="fc318-798">從應用程式取得資料</span><span class="sxs-lookup"><span data-stu-id="fc318-798">Obtain data from an app</span></span>

<span data-ttu-id="fc318-799">若應用程式能夠回應要求、請使用終端機內嵌中介軟體從應用程式取得要求、連線與額外資料。</span><span class="sxs-lookup"><span data-stu-id="fc318-799">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="fc318-800">如如需詳細資訊與範例程式碼，請參閱 <xref:test/troubleshoot#obtain-data-from-an-app>。</span><span class="sxs-lookup"><span data-stu-id="fc318-800">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="fc318-801"> (IIS) 的應用程式變慢或懸掛</span><span class="sxs-lookup"><span data-stu-id="fc318-801">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="fc318-802">損 *毀* 傾印是系統記憶體的快照，可協助判斷應用程式損毀、啟動失敗或應用程式緩慢的原因。</span><span class="sxs-lookup"><span data-stu-id="fc318-802">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="fc318-803">應用程式損毀或發生例外狀況</span><span class="sxs-lookup"><span data-stu-id="fc318-803">App crashes or encounters an exception</span></span>

<span data-ttu-id="fc318-804">從 [Windows 錯誤報告 (WER)](/windows/desktop/wer/windows-error-reporting) 取得並分析傾印：</span><span class="sxs-lookup"><span data-stu-id="fc318-804">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="fc318-805">在 `c:\dumps` 中建立資料夾以保存損毀傾印檔案。</span><span class="sxs-lookup"><span data-stu-id="fc318-805">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="fc318-806">應用程式集區必須具備該資料夾的寫入權限。</span><span class="sxs-lookup"><span data-stu-id="fc318-806">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="fc318-807">執行 [EnableDumps PowerShell 指令碼](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="fc318-807">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="fc318-808">如果應用程式是使用[同處理序主控模型](xref:host-and-deploy/iis/index#in-process-hosting-model)，請執行 *w3wp.exe* 的指令碼：</span><span class="sxs-lookup"><span data-stu-id="fc318-808">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="fc318-809">如果應用程式是使用[跨處理序主控模型](xref:host-and-deploy/iis/index#out-of-process-hosting-model)，請執行 *dotnet.exe* 的指令碼：</span><span class="sxs-lookup"><span data-stu-id="fc318-809">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="fc318-810">在會導致損毀的情況下，執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="fc318-810">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="fc318-811">發生損毀之後，請執行 [DisableDumps PowerShell 指令碼](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="fc318-811">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="fc318-812">如果應用程式是使用[同處理序主控模型](xref:host-and-deploy/iis/index#in-process-hosting-model)，請執行 *w3wp.exe* 的指令碼：</span><span class="sxs-lookup"><span data-stu-id="fc318-812">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="fc318-813">如果應用程式是使用[跨處理序主控模型](xref:host-and-deploy/iis/index#out-of-process-hosting-model)，請執行 *dotnet.exe* 的指令碼：</span><span class="sxs-lookup"><span data-stu-id="fc318-813">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="fc318-814">在應用程式損毀且傾印收集完成之後，即可正常結束應用程式。</span><span class="sxs-lookup"><span data-stu-id="fc318-814">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="fc318-815">PowerShell 指令碼會將 WER 設定為每個應用程式收集最多五個傾印。</span><span class="sxs-lookup"><span data-stu-id="fc318-815">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="fc318-816">損毀傾印可能會佔用大量磁碟空間 (每個高達好幾 GB)。</span><span class="sxs-lookup"><span data-stu-id="fc318-816">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="fc318-817">應用程式停止回應、在啟動期間失敗，或正常執行</span><span class="sxs-lookup"><span data-stu-id="fc318-817">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="fc318-818">當 *應用程式* 停止回應時 (停止回應，但不會當機) 、啟動期間失敗，或正常執行時，請參閱 [使用者模式](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) 傾印檔案：選擇最適合的工具來選取適當的工具來產生傾印。</span><span class="sxs-lookup"><span data-stu-id="fc318-818">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="fc318-819">分析傾印</span><span class="sxs-lookup"><span data-stu-id="fc318-819">Analyze the dump</span></span>

<span data-ttu-id="fc318-820">您可以使用數種方法來分析傾印。</span><span class="sxs-lookup"><span data-stu-id="fc318-820">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="fc318-821">如需詳細資訊，請參閱[分析使用者模式傾印檔案](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)。</span><span class="sxs-lookup"><span data-stu-id="fc318-821">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="fc318-822">清除套件快取</span><span class="sxs-lookup"><span data-stu-id="fc318-822">Clear package caches</span></span>

<span data-ttu-id="fc318-823">在升級開發電腦上的 .NET Core SDK 或變更應用程式內的套件版本之後，正常運作的應用程式可能會立即失敗。</span><span class="sxs-lookup"><span data-stu-id="fc318-823">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="fc318-824">在某些情況下，執行主要升級時，不一致的套件可能會中斷應用程式。</span><span class="sxs-lookup"><span data-stu-id="fc318-824">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="fc318-825">大多數這些問題都可依照下列指示來進行修正：</span><span class="sxs-lookup"><span data-stu-id="fc318-825">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="fc318-826">刪除 [bin]\*\* 和 [obj]\*\* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="fc318-826">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="fc318-827">從命令列介面執行 [dotnet nuget 區域變數](/dotnet/core/tools/dotnet-nuget-locals) ，以清除套件快取。</span><span class="sxs-lookup"><span data-stu-id="fc318-827">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="fc318-828">清除套件快取也可以使用 [nuget.exe](https://www.nuget.org/downloads) 工具和執行命令來完成 `nuget locals all -clear` 。</span><span class="sxs-lookup"><span data-stu-id="fc318-828">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="fc318-829">*nuget.exe* 並未隨附在 Windows 桌面作業系統的安裝中，必須另外從 [NuGet 網站](https://www.nuget.org/downloads)取得。</span><span class="sxs-lookup"><span data-stu-id="fc318-829">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="fc318-830">還原並重建專案。</span><span class="sxs-lookup"><span data-stu-id="fc318-830">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="fc318-831">重新部署應用程式之前，請先刪除伺服器上 [部署] 資料夾中的所有檔案。</span><span class="sxs-lookup"><span data-stu-id="fc318-831">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fc318-832">其他資源</span><span class="sxs-lookup"><span data-stu-id="fc318-832">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="fc318-833">Azure 文件</span><span class="sxs-lookup"><span data-stu-id="fc318-833">Azure documentation</span></span>

* [<span data-ttu-id="fc318-834">ASP.NET Core 的 Application Insights</span><span class="sxs-lookup"><span data-stu-id="fc318-834">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="fc318-835">使用 Visual Studio 針對 Azure App Service 中的 web 應用程式進行疑難排解的遠端偵錯程式區段</span><span class="sxs-lookup"><span data-stu-id="fc318-835">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="fc318-836">Azure App Service 診斷概觀</span><span class="sxs-lookup"><span data-stu-id="fc318-836">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="fc318-837">作法：監視 Azure App Service 中的應用程式</span><span class="sxs-lookup"><span data-stu-id="fc318-837">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="fc318-838">使用 Visual Studio 疑難排解 Azure App Service 中的 Web 應用程式</span><span class="sxs-lookup"><span data-stu-id="fc318-838">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="fc318-839">對您的 Azure Web 應用程式中「502 不正確的閘道」和「503 服務無法使用」的 HTTP 錯誤進行疑難排解</span><span class="sxs-lookup"><span data-stu-id="fc318-839">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="fc318-840">針對 Azure App Service 中 Web 應用程式效能變慢的問題進行疑難排解</span><span class="sxs-lookup"><span data-stu-id="fc318-840">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="fc318-841">Azure Web 應用程式的應用程式效能常見問題集</span><span class="sxs-lookup"><span data-stu-id="fc318-841">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* <span data-ttu-id="fc318-842">[Azure Web 應用程式沙箱 (App Service 執行階段執行限制)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="fc318-842">[Azure Web App sandbox (App Service runtime execution limitations)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)</span></span>
* [<span data-ttu-id="fc318-843">Azure Friday：Azure App Service 診斷和疑難排解體驗 (12 分鐘的影片)</span><span class="sxs-lookup"><span data-stu-id="fc318-843">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="fc318-844">Visual Studio 文件</span><span class="sxs-lookup"><span data-stu-id="fc318-844">Visual Studio documentation</span></span>

* [<span data-ttu-id="fc318-845">Visual Studio 2017 中的 Azure IIS 上的遠端偵錯 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fc318-845">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="fc318-846">Visual Studio 2017 的遠端 IIS 電腦上的遠端偵錯 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fc318-846">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="fc318-847">瞭解如何使用 Visual Studio 進行調試</span><span class="sxs-lookup"><span data-stu-id="fc318-847">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="fc318-848">Visual Studio Code 文件</span><span class="sxs-lookup"><span data-stu-id="fc318-848">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="fc318-849">使用 Visual Studio Code 偵錯</span><span class="sxs-lookup"><span data-stu-id="fc318-849">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="fc318-850">本文提供有關一般應用程式啟動錯誤的資訊，以及如何在將應用程式部署到 Azure App Service 或 IIS 時診斷錯誤的指示：</span><span class="sxs-lookup"><span data-stu-id="fc318-850">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="fc318-851">應用程式啟動錯誤</span><span class="sxs-lookup"><span data-stu-id="fc318-851">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="fc318-852">說明常見的啟動 HTTP 狀態碼案例。</span><span class="sxs-lookup"><span data-stu-id="fc318-852">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="fc318-853">針對 Azure App Service 進行疑難排解</span><span class="sxs-lookup"><span data-stu-id="fc318-853">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="fc318-854">針對部署至 Azure App Service 的應用程式提供疑難排解建議。</span><span class="sxs-lookup"><span data-stu-id="fc318-854">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="fc318-855">針對 IIS 進行疑難排解</span><span class="sxs-lookup"><span data-stu-id="fc318-855">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="fc318-856">針對部署至 IIS 或在本機 IIS Express 上執行的應用程式，提供疑難排解建議。</span><span class="sxs-lookup"><span data-stu-id="fc318-856">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="fc318-857">本指南適用于 Windows Server 和 Windows 桌面部署。</span><span class="sxs-lookup"><span data-stu-id="fc318-857">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="fc318-858">清除套件快取</span><span class="sxs-lookup"><span data-stu-id="fc318-858">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="fc318-859">說明當一致套件在執行主要升級或變更套件版本時，會中斷應用程式時，該怎麼辦。</span><span class="sxs-lookup"><span data-stu-id="fc318-859">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="fc318-860">其他資源</span><span class="sxs-lookup"><span data-stu-id="fc318-860">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="fc318-861">列出其他疑難排解主題。</span><span class="sxs-lookup"><span data-stu-id="fc318-861">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="fc318-862">應用程式啟動錯誤</span><span class="sxs-lookup"><span data-stu-id="fc318-862">App startup errors</span></span>

<span data-ttu-id="fc318-863">在 Visual Studio 中，ASP.NET Core 專案預設為在偵錯工具期間 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 裝載。</span><span class="sxs-lookup"><span data-stu-id="fc318-863">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="fc318-864">使用本主題中的建議診斷在本機進行偵錯工具時，所發生的 *502.5 進程失敗* 。</span><span class="sxs-lookup"><span data-stu-id="fc318-864">A *502.5 Process Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="fc318-865">403.14 禁止</span><span class="sxs-lookup"><span data-stu-id="fc318-865">403.14 Forbidden</span></span>

<span data-ttu-id="fc318-866">應用程式無法啟動。</span><span class="sxs-lookup"><span data-stu-id="fc318-866">The app fails to start.</span></span> <span data-ttu-id="fc318-867">會記錄下列錯誤：</span><span class="sxs-lookup"><span data-stu-id="fc318-867">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="fc318-868">此錯誤通常是因為裝載系統上的部署中斷所造成，包括下列任一案例：</span><span class="sxs-lookup"><span data-stu-id="fc318-868">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="fc318-869">應用程式會部署到裝載系統上的錯誤資料夾。</span><span class="sxs-lookup"><span data-stu-id="fc318-869">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="fc318-870">部署程式無法將所有應用程式的檔案和資料夾移至裝載系統上的部署資料夾。</span><span class="sxs-lookup"><span data-stu-id="fc318-870">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="fc318-871">部署中缺少 *web.config* 檔案，或 *web.config* 的檔案內容格式不正確。</span><span class="sxs-lookup"><span data-stu-id="fc318-871">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="fc318-872">請執行下列步驟：</span><span class="sxs-lookup"><span data-stu-id="fc318-872">Perform the following steps:</span></span>

1. <span data-ttu-id="fc318-873">從裝載系統上的 [部署] 資料夾中刪除所有檔案和資料夾。</span><span class="sxs-lookup"><span data-stu-id="fc318-873">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="fc318-874">使用您的一般部署方法（例如 Visual Studio、PowerShell 或手動部署），將應用程式的 *發行* 資料夾內容重新部署至主機系統：</span><span class="sxs-lookup"><span data-stu-id="fc318-874">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="fc318-875">確認部署中有 *web.config* 檔案，且其內容正確無誤。</span><span class="sxs-lookup"><span data-stu-id="fc318-875">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="fc318-876">在 Azure App Service 上裝載時，請確認應用程式已部署到 `D:\home\site\wwwroot` 資料夾。</span><span class="sxs-lookup"><span data-stu-id="fc318-876">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="fc318-877">當應用程式是由 IIS 主控時，請確認應用程式已部署至 iis**管理員\*\*\*\*基本設定**中顯示的 iis**實體路徑**。</span><span class="sxs-lookup"><span data-stu-id="fc318-877">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="fc318-878">藉由比較主機系統上的部署與專案 *發佈* 資料夾的內容，確認已部署所有應用程式的檔案和資料夾。</span><span class="sxs-lookup"><span data-stu-id="fc318-878">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="fc318-879">如需已發佈的 ASP.NET Core 應用程式佈建的詳細資訊，請參閱 <xref:host-and-deploy/directory-structure> 。</span><span class="sxs-lookup"><span data-stu-id="fc318-879">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="fc318-880">如需 *web.config* 檔案的詳細資訊，請參閱 <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig> 。</span><span class="sxs-lookup"><span data-stu-id="fc318-880">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="fc318-881">500 內部伺服器錯誤</span><span class="sxs-lookup"><span data-stu-id="fc318-881">500 Internal Server Error</span></span>

<span data-ttu-id="fc318-882">應用程式啟動，但有錯誤導致伺服器無法完成要求。</span><span class="sxs-lookup"><span data-stu-id="fc318-882">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="fc318-883">此錯誤是在啟動或建立回應時，在應用程式的程式碼內發生。</span><span class="sxs-lookup"><span data-stu-id="fc318-883">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="fc318-884">回應可能未包含任何內容，或是回應可能在瀏覽器中以「500 內部伺服器錯誤」\*\* 的形式出現。</span><span class="sxs-lookup"><span data-stu-id="fc318-884">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="fc318-885">「應用程式事件記錄檔」通常會指出該應用程式已正常啟動。</span><span class="sxs-lookup"><span data-stu-id="fc318-885">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="fc318-886">從伺服器的觀點來看，這是正確的。</span><span class="sxs-lookup"><span data-stu-id="fc318-886">From the server's perspective, that's correct.</span></span> <span data-ttu-id="fc318-887">應用程式已啟動，但無法產生有效的回應。</span><span class="sxs-lookup"><span data-stu-id="fc318-887">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="fc318-888">請在伺服器上於命令提示字元中執行應用程式或啟用 ASP.NET Core 模組 stdout 記錄檔，以針對問題進行疑難排解。</span><span class="sxs-lookup"><span data-stu-id="fc318-888">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="fc318-889">502.5 處理序失敗</span><span class="sxs-lookup"><span data-stu-id="fc318-889">502.5 Process Failure</span></span>

<span data-ttu-id="fc318-890">背景工作處理序失敗。</span><span class="sxs-lookup"><span data-stu-id="fc318-890">The worker process fails.</span></span> <span data-ttu-id="fc318-891">應用程式未啟動。</span><span class="sxs-lookup"><span data-stu-id="fc318-891">The app doesn't start.</span></span>

<span data-ttu-id="fc318-892">[ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)嘗試啟動背景工作處理序，但無法啟動。</span><span class="sxs-lookup"><span data-stu-id="fc318-892">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="fc318-893">通常從應用程式事件記錄檔和 ASP.NET Core 模組 stdout 記錄檔中的項目，即可判斷啟動失敗的原因。</span><span class="sxs-lookup"><span data-stu-id="fc318-893">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="fc318-894">因為目標 ASP.NET Core 共用架構的版本不存在，導致應用程式設定錯誤是常見的失敗狀況。</span><span class="sxs-lookup"><span data-stu-id="fc318-894">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="fc318-895">請檢查安裝在目標機器上的 ASP.NET Core 共用架構版本為何。</span><span class="sxs-lookup"><span data-stu-id="fc318-895">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="fc318-896">「共用的架構」\*\* 是一組安裝於電腦上且由 `Microsoft.AspNetCore.App` 之類的中繼套件所參考的組件 (*.dll* 檔案)。</span><span class="sxs-lookup"><span data-stu-id="fc318-896">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="fc318-897">中繼套件參考可以指定最低必要版本。</span><span class="sxs-lookup"><span data-stu-id="fc318-897">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="fc318-898">如需詳細資訊，請參閱[共用的架構](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="fc318-898">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="fc318-899">當裝載或應用程式設定錯誤造成背景工作處理序發生失敗時，會傳回 [502.5 處理序失敗]\*\* 錯誤頁面：</span><span class="sxs-lookup"><span data-stu-id="fc318-899">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="fc318-900">無法啟動應用程式 (ErrorCode '0x800700c1')</span><span class="sxs-lookup"><span data-stu-id="fc318-900">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="fc318-901">應用程式無法啟動，因為無法載入應用程式的組件 (*.dll*)。</span><span class="sxs-lookup"><span data-stu-id="fc318-901">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="fc318-902">當已發行的應用程式與 w3wp/iisexpress 處理序之間出現位元不符的情況時，就會發生此錯誤。</span><span class="sxs-lookup"><span data-stu-id="fc318-902">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="fc318-903">確認應用程式集區的 32 位元設定正確：</span><span class="sxs-lookup"><span data-stu-id="fc318-903">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="fc318-904">在 IIS 管理員的 [應用程式集區]\*\*\*\* 中，選取應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="fc318-904">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="fc318-905">在 [動作]\*\*\*\* 面板中，選取 [編輯應用程式集區]\*\*\*\* 下的 [進階設定]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="fc318-905">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="fc318-906">設定 [啟用 32 位元應用程式]\*\*\*\*：</span><span class="sxs-lookup"><span data-stu-id="fc318-906">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="fc318-907">如果部署 32 位元 (x86) 應用程式，請將值設定為 `True`。</span><span class="sxs-lookup"><span data-stu-id="fc318-907">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="fc318-908">如果部署 64 位元 (x64) 應用程式，請將值設定為 `False`。</span><span class="sxs-lookup"><span data-stu-id="fc318-908">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="fc318-909">確認 `<Platform>` 專案檔中的 MSBuild 屬性和應用程式的已發佈位之間沒有衝突。</span><span class="sxs-lookup"><span data-stu-id="fc318-909">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="fc318-910">連線重設</span><span class="sxs-lookup"><span data-stu-id="fc318-910">Connection reset</span></span>

<span data-ttu-id="fc318-911">如果是在傳送標頭之後才發生錯誤，則當發生錯誤時，伺服器已來不及傳送「500 內部伺服器錯誤」\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="fc318-911">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="fc318-912">通常是在將回應的複雜物件序列化的期間發生錯誤時，會發生此錯誤。</span><span class="sxs-lookup"><span data-stu-id="fc318-912">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="fc318-913">這類錯誤會在用戶端上顯示為「連線重設」\*\* 錯誤。</span><span class="sxs-lookup"><span data-stu-id="fc318-913">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="fc318-914">[應用程式記錄](xref:fundamentals/logging/index)可協助針對這些類型的錯誤進行疑難排解。</span><span class="sxs-lookup"><span data-stu-id="fc318-914">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="fc318-915">預設啟動限制</span><span class="sxs-lookup"><span data-stu-id="fc318-915">Default startup limits</span></span>

<span data-ttu-id="fc318-916">[ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)的設定預設*startupTimeLimit*為120秒。</span><span class="sxs-lookup"><span data-stu-id="fc318-916">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="fc318-917">保留預設值時，在模組記錄處理序失敗之前，應用程式最多可花費兩分鐘來進行啟動。</span><span class="sxs-lookup"><span data-stu-id="fc318-917">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="fc318-918">如需有關設定模組的資訊，請參閱 [aspNetCore 元素的屬性](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)。</span><span class="sxs-lookup"><span data-stu-id="fc318-918">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="fc318-919">針對 Azure App Service 進行疑難排解</span><span class="sxs-lookup"><span data-stu-id="fc318-919">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="fc318-920">應用程式事件記錄檔 (Azure App Service) </span><span class="sxs-lookup"><span data-stu-id="fc318-920">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="fc318-921">若要存取「應用程式事件記錄檔」，請使用 Azure 入口網站中的 [診斷並解決問題]\*\*\*\* 刀鋒視窗：</span><span class="sxs-lookup"><span data-stu-id="fc318-921">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="fc318-922">在 Azure 入口網站的 [應用程式服務]\*\*\*\* 中，開啟應用程式。</span><span class="sxs-lookup"><span data-stu-id="fc318-922">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="fc318-923">選取 [診斷並解決問題]。</span><span class="sxs-lookup"><span data-stu-id="fc318-923">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="fc318-924">選取 [診斷工具]\*\*\*\* 標題。</span><span class="sxs-lookup"><span data-stu-id="fc318-924">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="fc318-925">在 [支援工具]\*\*\*\* 下，選取 [應用程式事件]\*\*\*\* 按鈕。</span><span class="sxs-lookup"><span data-stu-id="fc318-925">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="fc318-926">檢查 [來源]\*\*\*\* 資料行中 *IIS AspNetCoreModule* 或 *IIS AspNetCoreModule V2* 項目所提供的最新錯誤。</span><span class="sxs-lookup"><span data-stu-id="fc318-926">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="fc318-927">除了使用 [診斷並解決問題]\*\*\*\* 刀鋒視窗之外，也可以使用 [Kudu](https://github.com/projectkudu/kudu/wiki) 來直接檢查「應用程式事件記錄檔」：</span><span class="sxs-lookup"><span data-stu-id="fc318-927">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="fc318-928">在 [開發工具]\*\*\*\* 區域中，開啟 [進階工具]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="fc318-928">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="fc318-929">選取 [**移 &rarr; 至**] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="fc318-929">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="fc318-930">Kudu 主控台會在新的瀏覽器索引標籤或視窗中開啟。</span><span class="sxs-lookup"><span data-stu-id="fc318-930">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="fc318-931">使用頁面頂端的導覽列，開啟 [偵錯主控台]\*\*\*\*，然後選取 [CMD]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="fc318-931">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="fc318-932">開啟 **LogFiles** 資料夾。</span><span class="sxs-lookup"><span data-stu-id="fc318-932">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="fc318-933">選取 *eventlog.xml* 檔案旁邊的鉛筆圖示。</span><span class="sxs-lookup"><span data-stu-id="fc318-933">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="fc318-934">檢查記錄檔。</span><span class="sxs-lookup"><span data-stu-id="fc318-934">Examine the log.</span></span> <span data-ttu-id="fc318-935">捲動至記錄檔的底部以查看最新事件。</span><span class="sxs-lookup"><span data-stu-id="fc318-935">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="fc318-936">在 Kudu 主控台中執行應用程式</span><span class="sxs-lookup"><span data-stu-id="fc318-936">Run the app in the Kudu console</span></span>

<span data-ttu-id="fc318-937">許多啟動錯誤不會在「應用程式事件記錄檔」中產生實用的資訊。</span><span class="sxs-lookup"><span data-stu-id="fc318-937">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="fc318-938">您可以在 [Kudu](https://github.com/projectkudu/kudu/wiki)「遠端執行主控台」中執行應用程式來探索錯誤：</span><span class="sxs-lookup"><span data-stu-id="fc318-938">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="fc318-939">在 [開發工具]\*\*\*\* 區域中，開啟 [進階工具]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="fc318-939">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="fc318-940">選取 [**移 &rarr; 至**] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="fc318-940">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="fc318-941">Kudu 主控台會在新的瀏覽器索引標籤或視窗中開啟。</span><span class="sxs-lookup"><span data-stu-id="fc318-941">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="fc318-942">使用頁面頂端的導覽列，開啟 [偵錯主控台]\*\*\*\*，然後選取 [CMD]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="fc318-942">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="fc318-943">測試 32 位元 (x86) 應用程式</span><span class="sxs-lookup"><span data-stu-id="fc318-943">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="fc318-944">**目前的版本**</span><span class="sxs-lookup"><span data-stu-id="fc318-944">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="fc318-945">執行應用程式：</span><span class="sxs-lookup"><span data-stu-id="fc318-945">Run the app:</span></span>
   * <span data-ttu-id="fc318-946">如果應用程式是[架構相依部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="fc318-946">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="fc318-947">如果應用程式是[自封式部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="fc318-947">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="fc318-948">應用程式的主控台輸出 (顯示任何錯誤) 會以管道傳送至 Kudu 主控台。</span><span class="sxs-lookup"><span data-stu-id="fc318-948">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="fc318-949">**在預覽版上執行的架構相依部署**</span><span class="sxs-lookup"><span data-stu-id="fc318-949">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="fc318-950">*要求安裝 ASP.NET Core {VERSION} (x86) 執行階段網站延伸模組。*</span><span class="sxs-lookup"><span data-stu-id="fc318-950">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="fc318-951">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` 是執行階段版本)</span><span class="sxs-lookup"><span data-stu-id="fc318-951">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="fc318-952">執行應用程式：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="fc318-952">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="fc318-953">應用程式的主控台輸出 (顯示任何錯誤) 會以管道傳送至 Kudu 主控台。</span><span class="sxs-lookup"><span data-stu-id="fc318-953">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="fc318-954">測試 64 位元 (x64) 應用程式</span><span class="sxs-lookup"><span data-stu-id="fc318-954">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="fc318-955">**目前的版本**</span><span class="sxs-lookup"><span data-stu-id="fc318-955">**Current release**</span></span>

* <span data-ttu-id="fc318-956">如果應用程式是 64 位元 (x64) [架構相依部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="fc318-956">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="fc318-957">執行應用程式：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="fc318-957">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="fc318-958">如果應用程式是[自封式部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="fc318-958">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="fc318-959">執行應用程式：`{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="fc318-959">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="fc318-960">應用程式的主控台輸出 (顯示任何錯誤) 會以管道傳送至 Kudu 主控台。</span><span class="sxs-lookup"><span data-stu-id="fc318-960">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="fc318-961">**在預覽版上執行的架構相依部署**</span><span class="sxs-lookup"><span data-stu-id="fc318-961">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="fc318-962">*要求安裝 ASP.NET Core {VERSION} (x64) 執行階段網站延伸模組。*</span><span class="sxs-lookup"><span data-stu-id="fc318-962">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="fc318-963">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` 是執行階段版本)</span><span class="sxs-lookup"><span data-stu-id="fc318-963">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="fc318-964">執行應用程式：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="fc318-964">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="fc318-965">應用程式的主控台輸出 (顯示任何錯誤) 會以管道傳送至 Kudu 主控台。</span><span class="sxs-lookup"><span data-stu-id="fc318-965">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="fc318-966">ASP.NET Core Module stdout 記錄 (Azure App Service) </span><span class="sxs-lookup"><span data-stu-id="fc318-966">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="fc318-967">ASP.NET Core 模組 stdout 記錄檔通常會記錄「應用程式事件記錄檔」中所沒有的實用訊息。</span><span class="sxs-lookup"><span data-stu-id="fc318-967">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="fc318-968">啟用及檢視 stdout 記錄檔：</span><span class="sxs-lookup"><span data-stu-id="fc318-968">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="fc318-969">在 Azure 入口網站中，瀏覽至 [診斷並解決問題]\*\*\*\* 刀鋒視窗。</span><span class="sxs-lookup"><span data-stu-id="fc318-969">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="fc318-970">在 [選取問題類別]\*\*\*\* 底下，選取 [Web 應用程式停止運作]\*\*\*\* 按鈕。</span><span class="sxs-lookup"><span data-stu-id="fc318-970">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="fc318-971">在 [建議的解決方案]**[啟用 Stdout 記錄檔重新導向]** > \*\*\*\* 底下，選取用來**開啟 Kudu 主控台以編輯 Web.Config** 的按鈕。</span><span class="sxs-lookup"><span data-stu-id="fc318-971">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="fc318-972">在 Kudu [診斷主控台]\*\*\*\* 中，將資料夾開啟至路徑 [site]\*\*\*\* > [wwwroot]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="fc318-972">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="fc318-973">向下捲動以顯露位於清單底部的 *web.config* 檔案。</span><span class="sxs-lookup"><span data-stu-id="fc318-973">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="fc318-974">按一下 *web.config* 檔案旁邊的鉛筆圖示。</span><span class="sxs-lookup"><span data-stu-id="fc318-974">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="fc318-975">將 **stdoutLogEnabled** 設定為 `true`，並將 **stdoutLogFile** 路徑變更為：`\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="fc318-975">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="fc318-976">選取 [儲存]\*\*\*\* 以儲存已更新的 *web.config* 檔案。</span><span class="sxs-lookup"><span data-stu-id="fc318-976">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="fc318-977">對應用程式發出要求。</span><span class="sxs-lookup"><span data-stu-id="fc318-977">Make a request to the app.</span></span>
1. <span data-ttu-id="fc318-978">返回 Azure 入口網站。</span><span class="sxs-lookup"><span data-stu-id="fc318-978">Return to the Azure portal.</span></span> <span data-ttu-id="fc318-979">選取 [開發工具]\*\*\*\* 區域中的 [進階工具]\*\*\*\* 刀鋒視窗。</span><span class="sxs-lookup"><span data-stu-id="fc318-979">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="fc318-980">選取 [**移 &rarr; 至**] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="fc318-980">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="fc318-981">Kudu 主控台會在新的瀏覽器索引標籤或視窗中開啟。</span><span class="sxs-lookup"><span data-stu-id="fc318-981">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="fc318-982">使用頁面頂端的導覽列，開啟 [偵錯主控台]\*\*\*\*，然後選取 [CMD]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="fc318-982">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="fc318-983">選取 **LogFiles** 資料夾。</span><span class="sxs-lookup"><span data-stu-id="fc318-983">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="fc318-984">檢查 [修改時間]\*\*\*\* 資料行，然後選取鉛筆圖示來編輯修改日期最新的 stdout 記錄檔。</span><span class="sxs-lookup"><span data-stu-id="fc318-984">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="fc318-985">當記錄檔開啟時，會顯示錯誤。</span><span class="sxs-lookup"><span data-stu-id="fc318-985">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="fc318-986">完成疑難排解時，請停用 stdout 記錄：</span><span class="sxs-lookup"><span data-stu-id="fc318-986">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="fc318-987">在 Kudu [診斷主控台]\*\*\*\* 中，返回路徑 [site]\*\*\*\* > [wwwroot]\*\*\*\* 以顯露 *web.config* 檔案。</span><span class="sxs-lookup"><span data-stu-id="fc318-987">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="fc318-988">再次選取鉛筆圖示來開啟 **web.config** 檔案。</span><span class="sxs-lookup"><span data-stu-id="fc318-988">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="fc318-989">將 **stdoutLogEnabled** 設定為 `false`。</span><span class="sxs-lookup"><span data-stu-id="fc318-989">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="fc318-990">選取 [儲存]\*\*\*\* 以儲存檔案。</span><span class="sxs-lookup"><span data-stu-id="fc318-990">Select **Save** to save the file.</span></span>

<span data-ttu-id="fc318-991">如需詳細資訊，請參閱<xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>。</span><span class="sxs-lookup"><span data-stu-id="fc318-991">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="fc318-992">如果無法停用 stdout 記錄檔，可能會造成應用程式或伺服器發生失敗。</span><span class="sxs-lookup"><span data-stu-id="fc318-992">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="fc318-993">因為它並沒有記錄檔大小或數量上的限制。</span><span class="sxs-lookup"><span data-stu-id="fc318-993">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="fc318-994">請只在針對應用程式啟動問題進行疑難排解時，才使用 stdout 記錄。</span><span class="sxs-lookup"><span data-stu-id="fc318-994">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="fc318-995">針對 ASP.NET Core 應用程式啟動後的一般記錄，請使用會限制記錄檔大小並輪替記錄檔的記錄程式庫。</span><span class="sxs-lookup"><span data-stu-id="fc318-995">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="fc318-996">如需詳細資訊，請參閱[協力廠商記錄提供者](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="fc318-996">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="fc318-997">應用程式 (Azure App Service 的緩慢或懸掛) </span><span class="sxs-lookup"><span data-stu-id="fc318-997">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="fc318-998">當應用程式針對要求回應緩慢或無回應時，請參閱下列文章：</span><span class="sxs-lookup"><span data-stu-id="fc318-998">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="fc318-999">針對 Azure App Service 中 Web 應用程式效能變慢的問題進行疑難排解</span><span class="sxs-lookup"><span data-stu-id="fc318-999">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* <span data-ttu-id="fc318-1000">[使用損毀診斷程式網站延伸模組來擷取傾印，以取得 Azure Web 應用程式上的間歇性例外狀況或效能問題](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="fc318-1000">[Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)</span></span>

### <a name="monitoring-blades"></a><span data-ttu-id="fc318-1001">監視刀鋒視窗</span><span class="sxs-lookup"><span data-stu-id="fc318-1001">Monitoring blades</span></span>

<span data-ttu-id="fc318-1002">監視刀鋒視窗為本主題稍早所述的方法提供替代的疑難排解體驗。</span><span class="sxs-lookup"><span data-stu-id="fc318-1002">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="fc318-1003">這些刀鋒視窗可用來診斷 500 系列的錯誤。</span><span class="sxs-lookup"><span data-stu-id="fc318-1003">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="fc318-1004">請確認已安裝 ASP.NET Core 延伸模組。</span><span class="sxs-lookup"><span data-stu-id="fc318-1004">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="fc318-1005">如果未安裝這些延伸模組，請手動安裝：</span><span class="sxs-lookup"><span data-stu-id="fc318-1005">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="fc318-1006">在 [開發工具]\*\*\*\* 刀鋒視窗區段中，選取 [延伸模組]\*\*\*\* 刀鋒視窗。</span><span class="sxs-lookup"><span data-stu-id="fc318-1006">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="fc318-1007">[ASP.NET Core 延伸模組]\*\*\*\* 應該會出現在清單中。</span><span class="sxs-lookup"><span data-stu-id="fc318-1007">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="fc318-1008">如果未安裝這些延伸模組，請選取 [新增]\*\*\*\* 按鈕。</span><span class="sxs-lookup"><span data-stu-id="fc318-1008">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="fc318-1009">從清單中選擇 [ASP.NET Core 延伸模組]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="fc318-1009">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="fc318-1010">選取 [確定]\*\*\*\* 以接受法律條款。</span><span class="sxs-lookup"><span data-stu-id="fc318-1010">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="fc318-1011">在 [新增延伸模組]\*\*\*\* 刀鋒視窗上，選取 [確定]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="fc318-1011">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="fc318-1012">成功安裝延伸模組時，會有資訊快顯訊息提供指示。</span><span class="sxs-lookup"><span data-stu-id="fc318-1012">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="fc318-1013">如果未啟用 stdout 記錄，請依照下列步驟進行操作：</span><span class="sxs-lookup"><span data-stu-id="fc318-1013">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="fc318-1014">在 Azure 入口網站中，選取 [開發工具]\*\*\*\* 區域中的 [進階工具]\*\*\*\* 刀鋒視窗。</span><span class="sxs-lookup"><span data-stu-id="fc318-1014">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="fc318-1015">選取 [**移 &rarr; 至**] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="fc318-1015">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="fc318-1016">Kudu 主控台會在新的瀏覽器索引標籤或視窗中開啟。</span><span class="sxs-lookup"><span data-stu-id="fc318-1016">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="fc318-1017">使用頁面頂端的導覽列，開啟 [偵錯主控台]\*\*\*\*，然後選取 [CMD]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="fc318-1017">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="fc318-1018">將資料夾開啟至路徑 [site]**[wwwroot]** > \*\*\*\*，然後向下捲動以顯露位於清單底部的 *web.config* 檔案。</span><span class="sxs-lookup"><span data-stu-id="fc318-1018">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="fc318-1019">按一下 *web.config* 檔案旁邊的鉛筆圖示。</span><span class="sxs-lookup"><span data-stu-id="fc318-1019">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="fc318-1020">將 **stdoutLogEnabled** 設定為 `true`，並將 **stdoutLogFile** 路徑變更為：`\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="fc318-1020">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="fc318-1021">選取 [儲存]\*\*\*\* 以儲存已更新的 *web.config* 檔案。</span><span class="sxs-lookup"><span data-stu-id="fc318-1021">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="fc318-1022">繼續接著啟用診斷記錄：</span><span class="sxs-lookup"><span data-stu-id="fc318-1022">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="fc318-1023">在 Azure 入口網站中，選取 [診斷記錄檔]\*\*\*\* 刀鋒視窗。</span><span class="sxs-lookup"><span data-stu-id="fc318-1023">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="fc318-1024">選取 [應用程式記錄 (檔案系統)]\*\*\*\* 和 [詳細錯誤訊息]\*\*\*\*.的 [開啟]\*\*\*\* 開關。</span><span class="sxs-lookup"><span data-stu-id="fc318-1024">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="fc318-1025">選取刀鋒視窗頂端的 [儲存]\*\*\*\* 按鈕。</span><span class="sxs-lookup"><span data-stu-id="fc318-1025">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="fc318-1026">若要包含失敗要求追蹤 (也稱為「失敗要求事件緩衝處理」(FREB) 記錄)，請選取 [失敗要求的追蹤]\*\*\*\* 的 [開啟]\*\*\*\* 開關。</span><span class="sxs-lookup"><span data-stu-id="fc318-1026">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="fc318-1027">選取 [記錄資料流]\*\*\*\* 刀鋒視窗 (列在入口網站中緊接在 [診斷記錄檔]\*\*\*\* 刀鋒視窗之下)。</span><span class="sxs-lookup"><span data-stu-id="fc318-1027">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="fc318-1028">對應用程式發出要求。</span><span class="sxs-lookup"><span data-stu-id="fc318-1028">Make a request to the app.</span></span>
1. <span data-ttu-id="fc318-1029">在記錄資料流資料內，會指出錯誤的原因。</span><span class="sxs-lookup"><span data-stu-id="fc318-1029">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="fc318-1030">完成疑難排解時，請務必停用 stdout 記錄。</span><span class="sxs-lookup"><span data-stu-id="fc318-1030">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="fc318-1031">檢視失敗要求追蹤記錄檔 (FREB 記錄檔)：</span><span class="sxs-lookup"><span data-stu-id="fc318-1031">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="fc318-1032">在 Azure 入口網站中，瀏覽至 [診斷並解決問題]\*\*\*\* 刀鋒視窗。</span><span class="sxs-lookup"><span data-stu-id="fc318-1032">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="fc318-1033">從資訊看板的 [支援工具]\*\*\*\* 區域中，選取 [失敗要求追蹤記錄檔]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="fc318-1033">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="fc318-1034">如需詳細資訊，請參閱[＜在 Azure App Service 中針對 Web 應用程式啟用診斷記錄功能＞主題的＜失敗要求追蹤＞一節](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces)和[Azure Web 應用程式的應用程式效能常見問題集：如何開啟失敗要求追蹤？](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)。</span><span class="sxs-lookup"><span data-stu-id="fc318-1034">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="fc318-1035">如需詳細資訊，請參閱[在 Azure App Service 中針對 Web 應用程式啟用診斷記錄功能](/azure/app-service/web-sites-enable-diagnostic-log)。</span><span class="sxs-lookup"><span data-stu-id="fc318-1035">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="fc318-1036">如果無法停用 stdout 記錄檔，可能會造成應用程式或伺服器發生失敗。</span><span class="sxs-lookup"><span data-stu-id="fc318-1036">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="fc318-1037">因為它並沒有記錄檔大小或數量上的限制。</span><span class="sxs-lookup"><span data-stu-id="fc318-1037">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="fc318-1038">針對 ASP.NET Core 應用程式中的例行性記錄，請使用會限制記錄檔大小並輪替記錄檔的記錄程式庫。</span><span class="sxs-lookup"><span data-stu-id="fc318-1038">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="fc318-1039">如需詳細資訊，請參閱[協力廠商記錄提供者](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="fc318-1039">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="fc318-1040">針對 IIS 進行疑難排解</span><span class="sxs-lookup"><span data-stu-id="fc318-1040">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="fc318-1041">應用程式事件記錄檔 (IIS) </span><span class="sxs-lookup"><span data-stu-id="fc318-1041">Application Event Log (IIS)</span></span>

<span data-ttu-id="fc318-1042">存取「應用程式事件記錄檔」：</span><span class="sxs-lookup"><span data-stu-id="fc318-1042">Access the Application Event Log:</span></span>

1. <span data-ttu-id="fc318-1043">開啟 [開始] 功能表、搜尋 *事件檢視器*，然後選取 **事件檢視器** 應用程式。</span><span class="sxs-lookup"><span data-stu-id="fc318-1043">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="fc318-1044">在 [事件檢視器]\*\*\*\* 中，開啟 [Windows 記錄]\*\*\*\* 節點。</span><span class="sxs-lookup"><span data-stu-id="fc318-1044">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="fc318-1045">選取 [應用程式]\*\*\*\* 以開啟「應用程式事件記錄檔」。</span><span class="sxs-lookup"><span data-stu-id="fc318-1045">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="fc318-1046">搜尋與失敗應用程式相關的錯誤。</span><span class="sxs-lookup"><span data-stu-id="fc318-1046">Search for errors associated with the failing app.</span></span> <span data-ttu-id="fc318-1047">錯誤在 [來源]\*\* 資料行中的值會是 *IIS AspNetCore Module* 或 *IIS Express AspNetCore Module*。</span><span class="sxs-lookup"><span data-stu-id="fc318-1047">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="fc318-1048">在命令提示字元中執行應用程式</span><span class="sxs-lookup"><span data-stu-id="fc318-1048">Run the app at a command prompt</span></span>

<span data-ttu-id="fc318-1049">許多啟動錯誤不會在「應用程式事件記錄檔」中產生實用的資訊。</span><span class="sxs-lookup"><span data-stu-id="fc318-1049">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="fc318-1050">您可以藉由在主控系統上的命令提示字元中執行應用程式，來找出一些錯誤的原因。</span><span class="sxs-lookup"><span data-stu-id="fc318-1050">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="fc318-1051">與 Framework 相依的部署</span><span class="sxs-lookup"><span data-stu-id="fc318-1051">Framework-dependent deployment</span></span>

<span data-ttu-id="fc318-1052">如果應用程式是[架構相依部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="fc318-1052">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="fc318-1053">在命令提示字元中，瀏覽至部署資料夾，然後使用 *dotnet.exe* 來執行應用程式組件以執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="fc318-1053">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="fc318-1054">在下列命令中，將應用程式的元件名稱取代為 \<assembly_name> ： `dotnet .\<assembly_name>.dll` 。</span><span class="sxs-lookup"><span data-stu-id="fc318-1054">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="fc318-1055">來自應用程式的主控台輸出若有顯示任何錯誤，就會寫入至主控台視窗。</span><span class="sxs-lookup"><span data-stu-id="fc318-1055">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="fc318-1056">如果是在對應用程式發出要求時發生錯誤，請對 Kestrel 進行接聽的主機和連接埠發出要求。</span><span class="sxs-lookup"><span data-stu-id="fc318-1056">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="fc318-1057">如果使用預設主機和連接埠，請對 `http://localhost:5000/` 發出要求。</span><span class="sxs-lookup"><span data-stu-id="fc318-1057">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="fc318-1058">如果應用程式在 Kestrel 端點位址正常回應，則問題與主機組態有關的機率較大，而與應用程式本身有關的機率較小。</span><span class="sxs-lookup"><span data-stu-id="fc318-1058">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="fc318-1059">自封式部署</span><span class="sxs-lookup"><span data-stu-id="fc318-1059">Self-contained deployment</span></span>

<span data-ttu-id="fc318-1060">如果應用程式是[自封式部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="fc318-1060">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="fc318-1061">在命令提示字元中，瀏覽至部署資料夾，然後執行應用程式的可執行檔。</span><span class="sxs-lookup"><span data-stu-id="fc318-1061">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="fc318-1062">在下列命令中，將應用程式的元件名稱取代為 \<assembly_name> ： `<assembly_name>.exe` 。</span><span class="sxs-lookup"><span data-stu-id="fc318-1062">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="fc318-1063">來自應用程式的主控台輸出若有顯示任何錯誤，就會寫入至主控台視窗。</span><span class="sxs-lookup"><span data-stu-id="fc318-1063">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="fc318-1064">如果是在對應用程式發出要求時發生錯誤，請對 Kestrel 進行接聽的主機和連接埠發出要求。</span><span class="sxs-lookup"><span data-stu-id="fc318-1064">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="fc318-1065">如果使用預設主機和連接埠，請對 `http://localhost:5000/` 發出要求。</span><span class="sxs-lookup"><span data-stu-id="fc318-1065">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="fc318-1066">如果應用程式在 Kestrel 端點位址正常回應，則問題與主機組態有關的機率較大，而與應用程式本身有關的機率較小。</span><span class="sxs-lookup"><span data-stu-id="fc318-1066">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="fc318-1067">ASP.NET Core Module stdout log (IIS) </span><span class="sxs-lookup"><span data-stu-id="fc318-1067">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="fc318-1068">啟用及檢視 stdout 記錄檔：</span><span class="sxs-lookup"><span data-stu-id="fc318-1068">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="fc318-1069">瀏覽至主控系統上網站的部署資料夾。</span><span class="sxs-lookup"><span data-stu-id="fc318-1069">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="fc318-1070">如果 [logs]\*\* 資料夾不存在，請建立該資料夾。</span><span class="sxs-lookup"><span data-stu-id="fc318-1070">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="fc318-1071">如需有關如何讓 MSBuild 在部署中自動建立 [logs]\*\* 資料夾的指示，請參閱[目錄結構](xref:host-and-deploy/directory-structure)主題。</span><span class="sxs-lookup"><span data-stu-id="fc318-1071">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="fc318-1072">編輯 *web.config* 檔案。</span><span class="sxs-lookup"><span data-stu-id="fc318-1072">Edit the *web.config* file.</span></span> <span data-ttu-id="fc318-1073">將 **stdoutLogEnabled** 設定為 `true`，並將 **stdoutLogFile** 路徑變更為指向 [logs]\*\* 資料夾 (例如 `.\logs\stdout`)。</span><span class="sxs-lookup"><span data-stu-id="fc318-1073">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="fc318-1074">路徑中的 `stdout` 是記錄檔名稱前置詞。</span><span class="sxs-lookup"><span data-stu-id="fc318-1074">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="fc318-1075">建立記錄檔時，系統會自動新增時間戳記、處理序識別碼及副檔名。</span><span class="sxs-lookup"><span data-stu-id="fc318-1075">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="fc318-1076">使用 `stdout` 作為檔案名稱前置詞時，一般記錄檔會命名為 *stdout_20180205184032_5412.log*。</span><span class="sxs-lookup"><span data-stu-id="fc318-1076">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="fc318-1077">請確定您的應用程式集區身分識別具有 *logs* 資料夾的寫入權限。</span><span class="sxs-lookup"><span data-stu-id="fc318-1077">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="fc318-1078">儲存已更新的 *web.config* 檔案。</span><span class="sxs-lookup"><span data-stu-id="fc318-1078">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="fc318-1079">對應用程式發出要求。</span><span class="sxs-lookup"><span data-stu-id="fc318-1079">Make a request to the app.</span></span>
1. <span data-ttu-id="fc318-1080">瀏覽至 [logs]\*\* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="fc318-1080">Navigate to the *logs* folder.</span></span> <span data-ttu-id="fc318-1081">尋找並開啟最新的 stdout 記錄檔。</span><span class="sxs-lookup"><span data-stu-id="fc318-1081">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="fc318-1082">研究記錄檔以了解錯誤。</span><span class="sxs-lookup"><span data-stu-id="fc318-1082">Study the log for errors.</span></span>

<span data-ttu-id="fc318-1083">完成疑難排解時，請停用 stdout 記錄：</span><span class="sxs-lookup"><span data-stu-id="fc318-1083">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="fc318-1084">編輯 *web.config* 檔案。</span><span class="sxs-lookup"><span data-stu-id="fc318-1084">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="fc318-1085">將 **stdoutLogEnabled** 設定為 `false`。</span><span class="sxs-lookup"><span data-stu-id="fc318-1085">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="fc318-1086">儲存檔案。</span><span class="sxs-lookup"><span data-stu-id="fc318-1086">Save the file.</span></span>

<span data-ttu-id="fc318-1087">如需詳細資訊，請參閱<xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>。</span><span class="sxs-lookup"><span data-stu-id="fc318-1087">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="fc318-1088">如果無法停用 stdout 記錄檔，可能會造成應用程式或伺服器發生失敗。</span><span class="sxs-lookup"><span data-stu-id="fc318-1088">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="fc318-1089">因為它並沒有記錄檔大小或數量上的限制。</span><span class="sxs-lookup"><span data-stu-id="fc318-1089">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="fc318-1090">針對 ASP.NET Core 應用程式中的例行性記錄，請使用會限制記錄檔大小並輪替記錄檔的記錄程式庫。</span><span class="sxs-lookup"><span data-stu-id="fc318-1090">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="fc318-1091">如需詳細資訊，請參閱[協力廠商記錄提供者](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="fc318-1091">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="fc318-1092">啟用開發人員例外頁面</span><span class="sxs-lookup"><span data-stu-id="fc318-1092">Enable the Developer Exception Page</span></span>

<span data-ttu-id="fc318-1093">在開發環境中，可以將 `ASPNETCORE_ENVIRONMENT` [環境變數新增至 web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) 來執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="fc318-1093">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="fc318-1094">只要主機產生器上的 `UseEnvironment` 不會覆寫應用程式啟動內的環境，設定該環境變數便可允許在應用程式執行時顯示[開發人員例外狀況頁面](xref:fundamentals/error-handling)。</span><span class="sxs-lookup"><span data-stu-id="fc318-1094">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

<span data-ttu-id="fc318-1095">只有在沒有對網際網路公開的暫存和測試伺服器上使用時，才建議為 `ASPNETCORE_ENVIRONMENT` 設定環境變數。</span><span class="sxs-lookup"><span data-stu-id="fc318-1095">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="fc318-1096">進行疑難排解之後，請從 *web.config* 檔案中移除環境變數。</span><span class="sxs-lookup"><span data-stu-id="fc318-1096">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="fc318-1097">如需有關在 *web.config* 中設定環境變數的資訊，請參閱 [aspNetCore 的 environmentVariables 子元素](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)。</span><span class="sxs-lookup"><span data-stu-id="fc318-1097">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="fc318-1098">從應用程式取得資料</span><span class="sxs-lookup"><span data-stu-id="fc318-1098">Obtain data from an app</span></span>

<span data-ttu-id="fc318-1099">若應用程式能夠回應要求、請使用終端機內嵌中介軟體從應用程式取得要求、連線與額外資料。</span><span class="sxs-lookup"><span data-stu-id="fc318-1099">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="fc318-1100">如如需詳細資訊與範例程式碼，請參閱 <xref:test/troubleshoot#obtain-data-from-an-app>。</span><span class="sxs-lookup"><span data-stu-id="fc318-1100">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="fc318-1101"> (IIS) 的應用程式變慢或懸掛</span><span class="sxs-lookup"><span data-stu-id="fc318-1101">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="fc318-1102">損 *毀* 傾印是系統記憶體的快照，可協助判斷應用程式損毀、啟動失敗或應用程式緩慢的原因。</span><span class="sxs-lookup"><span data-stu-id="fc318-1102">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="fc318-1103">應用程式損毀或發生例外狀況</span><span class="sxs-lookup"><span data-stu-id="fc318-1103">App crashes or encounters an exception</span></span>

<span data-ttu-id="fc318-1104">從 [Windows 錯誤報告 (WER)](/windows/desktop/wer/windows-error-reporting) 取得並分析傾印：</span><span class="sxs-lookup"><span data-stu-id="fc318-1104">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="fc318-1105">在 `c:\dumps` 中建立資料夾以保存損毀傾印檔案。</span><span class="sxs-lookup"><span data-stu-id="fc318-1105">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="fc318-1106">應用程式集區必須具備該資料夾的寫入權限。</span><span class="sxs-lookup"><span data-stu-id="fc318-1106">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="fc318-1107">執行 [EnableDumps PowerShell 指令碼](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="fc318-1107">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="fc318-1108">如果應用程式是使用[同處理序主控模型](xref:host-and-deploy/iis/index#in-process-hosting-model)，請執行 *w3wp.exe* 的指令碼：</span><span class="sxs-lookup"><span data-stu-id="fc318-1108">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="fc318-1109">如果應用程式是使用[跨處理序主控模型](xref:host-and-deploy/iis/index#out-of-process-hosting-model)，請執行 *dotnet.exe* 的指令碼：</span><span class="sxs-lookup"><span data-stu-id="fc318-1109">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="fc318-1110">在會導致損毀的情況下，執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="fc318-1110">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="fc318-1111">發生損毀之後，請執行 [DisableDumps PowerShell 指令碼](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="fc318-1111">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="fc318-1112">如果應用程式是使用[同處理序主控模型](xref:host-and-deploy/iis/index#in-process-hosting-model)，請執行 *w3wp.exe* 的指令碼：</span><span class="sxs-lookup"><span data-stu-id="fc318-1112">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="fc318-1113">如果應用程式是使用[跨處理序主控模型](xref:host-and-deploy/iis/index#out-of-process-hosting-model)，請執行 *dotnet.exe* 的指令碼：</span><span class="sxs-lookup"><span data-stu-id="fc318-1113">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="fc318-1114">在應用程式損毀且傾印收集完成之後，即可正常結束應用程式。</span><span class="sxs-lookup"><span data-stu-id="fc318-1114">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="fc318-1115">PowerShell 指令碼會將 WER 設定為每個應用程式收集最多五個傾印。</span><span class="sxs-lookup"><span data-stu-id="fc318-1115">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="fc318-1116">損毀傾印可能會佔用大量磁碟空間 (每個高達好幾 GB)。</span><span class="sxs-lookup"><span data-stu-id="fc318-1116">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="fc318-1117">應用程式停止回應、在啟動期間失敗，或正常執行</span><span class="sxs-lookup"><span data-stu-id="fc318-1117">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="fc318-1118">當 *應用程式* 停止回應時 (停止回應，但不會當機) 、啟動期間失敗，或正常執行時，請參閱 [使用者模式](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) 傾印檔案：選擇最適合的工具來選取適當的工具來產生傾印。</span><span class="sxs-lookup"><span data-stu-id="fc318-1118">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="fc318-1119">分析傾印</span><span class="sxs-lookup"><span data-stu-id="fc318-1119">Analyze the dump</span></span>

<span data-ttu-id="fc318-1120">您可以使用數種方法來分析傾印。</span><span class="sxs-lookup"><span data-stu-id="fc318-1120">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="fc318-1121">如需詳細資訊，請參閱[分析使用者模式傾印檔案](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)。</span><span class="sxs-lookup"><span data-stu-id="fc318-1121">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="fc318-1122">清除套件快取</span><span class="sxs-lookup"><span data-stu-id="fc318-1122">Clear package caches</span></span>

<span data-ttu-id="fc318-1123">在升級開發電腦上的 .NET Core SDK 或變更應用程式內的套件版本之後，正常運作的應用程式可能會立即失敗。</span><span class="sxs-lookup"><span data-stu-id="fc318-1123">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="fc318-1124">在某些情況下，執行主要升級時，不一致的套件可能會中斷應用程式。</span><span class="sxs-lookup"><span data-stu-id="fc318-1124">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="fc318-1125">大多數這些問題都可依照下列指示來進行修正：</span><span class="sxs-lookup"><span data-stu-id="fc318-1125">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="fc318-1126">刪除 [bin]\*\* 和 [obj]\*\* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="fc318-1126">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="fc318-1127">從命令列介面執行 [dotnet nuget 區域變數](/dotnet/core/tools/dotnet-nuget-locals) ，以清除套件快取。</span><span class="sxs-lookup"><span data-stu-id="fc318-1127">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="fc318-1128">清除套件快取也可以使用 [nuget.exe](https://www.nuget.org/downloads) 工具和執行命令來完成 `nuget locals all -clear` 。</span><span class="sxs-lookup"><span data-stu-id="fc318-1128">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="fc318-1129">*nuget.exe* 並未隨附在 Windows 桌面作業系統的安裝中，必須另外從 [NuGet 網站](https://www.nuget.org/downloads)取得。</span><span class="sxs-lookup"><span data-stu-id="fc318-1129">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="fc318-1130">還原並重建專案。</span><span class="sxs-lookup"><span data-stu-id="fc318-1130">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="fc318-1131">重新部署應用程式之前，請先刪除伺服器上 [部署] 資料夾中的所有檔案。</span><span class="sxs-lookup"><span data-stu-id="fc318-1131">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fc318-1132">其他資源</span><span class="sxs-lookup"><span data-stu-id="fc318-1132">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="fc318-1133">Azure 文件</span><span class="sxs-lookup"><span data-stu-id="fc318-1133">Azure documentation</span></span>

* [<span data-ttu-id="fc318-1134">ASP.NET Core 的 Application Insights</span><span class="sxs-lookup"><span data-stu-id="fc318-1134">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="fc318-1135">使用 Visual Studio 針對 Azure App Service 中的 web 應用程式進行疑難排解的遠端偵錯程式區段</span><span class="sxs-lookup"><span data-stu-id="fc318-1135">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="fc318-1136">Azure App Service 診斷概觀</span><span class="sxs-lookup"><span data-stu-id="fc318-1136">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="fc318-1137">作法：監視 Azure App Service 中的應用程式</span><span class="sxs-lookup"><span data-stu-id="fc318-1137">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="fc318-1138">使用 Visual Studio 疑難排解 Azure App Service 中的 Web 應用程式</span><span class="sxs-lookup"><span data-stu-id="fc318-1138">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="fc318-1139">對您的 Azure Web 應用程式中「502 不正確的閘道」和「503 服務無法使用」的 HTTP 錯誤進行疑難排解</span><span class="sxs-lookup"><span data-stu-id="fc318-1139">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="fc318-1140">針對 Azure App Service 中 Web 應用程式效能變慢的問題進行疑難排解</span><span class="sxs-lookup"><span data-stu-id="fc318-1140">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="fc318-1141">Azure Web 應用程式的應用程式效能常見問題集</span><span class="sxs-lookup"><span data-stu-id="fc318-1141">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* <span data-ttu-id="fc318-1142">[Azure Web 應用程式沙箱 (App Service 執行階段執行限制)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="fc318-1142">[Azure Web App sandbox (App Service runtime execution limitations)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)</span></span>
* [<span data-ttu-id="fc318-1143">Azure Friday：Azure App Service 診斷和疑難排解體驗 (12 分鐘的影片)</span><span class="sxs-lookup"><span data-stu-id="fc318-1143">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="fc318-1144">Visual Studio 文件</span><span class="sxs-lookup"><span data-stu-id="fc318-1144">Visual Studio documentation</span></span>

* [<span data-ttu-id="fc318-1145">Visual Studio 2017 中的 Azure IIS 上的遠端偵錯 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fc318-1145">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="fc318-1146">Visual Studio 2017 的遠端 IIS 電腦上的遠端偵錯 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fc318-1146">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="fc318-1147">瞭解如何使用 Visual Studio 進行調試</span><span class="sxs-lookup"><span data-stu-id="fc318-1147">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="fc318-1148">Visual Studio Code 文件</span><span class="sxs-lookup"><span data-stu-id="fc318-1148">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="fc318-1149">使用 Visual Studio Code 偵錯</span><span class="sxs-lookup"><span data-stu-id="fc318-1149">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end
