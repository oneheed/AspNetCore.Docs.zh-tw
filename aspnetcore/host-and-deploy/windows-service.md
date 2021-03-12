---
title: 在 Windows 服務上裝載 ASP.NET Core
author: rick-anderson
description: 了解如何在 Windows 服務上裝載 ASP.NET Core 應用程式。
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
uid: host-and-deploy/windows-service
ms.openlocfilehash: e5c7dd0e52f1246d3ac6ad9622573db4c276654b
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588811"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="d9bfc-103">在 Windows 服務上裝載 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d9bfc-103">Host ASP.NET Core in a Windows Service</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d9bfc-104">ASP.NET Core 應用程式可以裝載在 Windows 上作為 [Windows 服務](/dotnet/framework/windows-services/introduction-to-windows-service-applications)，不需要使用 IIS。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-104">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="d9bfc-105">當裝載為 Windows 服務時，應用程式將會在伺服器重新開機後自動啟動。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-105">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="d9bfc-106">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/windows-service/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="d9bfc-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d9bfc-107">必要條件</span><span class="sxs-lookup"><span data-stu-id="d9bfc-107">Prerequisites</span></span>

* [<span data-ttu-id="d9bfc-108">ASP.NET Core SDK 2.1 或更新版本</span><span class="sxs-lookup"><span data-stu-id="d9bfc-108">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="d9bfc-109">PowerShell 6.2 或更新版本</span><span class="sxs-lookup"><span data-stu-id="d9bfc-109">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a><span data-ttu-id="d9bfc-110">背景工作服務範本</span><span class="sxs-lookup"><span data-stu-id="d9bfc-110">Worker Service template</span></span>

<span data-ttu-id="d9bfc-111">ASP.NET Core 背景工作服務範本提供撰寫長期執行服務應用程式的起點。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-111">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="d9bfc-112">使用範本作為 Windows 服務應用程式的基礎：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-112">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="d9bfc-113">從 .NET Core 範本建立背景工作服務應用程式。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-113">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="d9bfc-114">請遵循[應用程式組態](#app-configuration)一節中的指導方針，更新背景工作服務應用程式，以便其執行為 Windows 服務。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-114">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a><span data-ttu-id="d9bfc-115">應用程式組態</span><span class="sxs-lookup"><span data-stu-id="d9bfc-115">App configuration</span></span>

<span data-ttu-id="d9bfc-116">應用程式需要 [WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices)的套件參考。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-116">The app requires a package reference for [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span></span>

<span data-ttu-id="d9bfc-117">`IHostBuilder.UseWindowsService` 在建立主機時呼叫。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-117">`IHostBuilder.UseWindowsService` is called when building the host.</span></span> <span data-ttu-id="d9bfc-118">如果應用程式以 Windows 服務形式執行，則方法會：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-118">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="d9bfc-119">將主機存留期設定為 `WindowsServiceLifetime`。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-119">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="d9bfc-120">將 [內容根目錄](xref:fundamentals/index#content-root) 設定為 [AppCoNtext. BaseDirectory](xref:System.AppContext.BaseDirectory)。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-120">Sets the [content root](xref:fundamentals/index#content-root) to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span> <span data-ttu-id="d9bfc-121">如需詳細資訊，請參閱[目前目錄與內容根目錄](#current-directory-and-content-root)一節。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-121">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
* <span data-ttu-id="d9bfc-122">啟用記錄至事件記錄檔：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-122">Enables logging to the event log:</span></span>
  * <span data-ttu-id="d9bfc-123">應用程式名稱會用來做為預設來源名稱。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-123">The application name is used as the default source name.</span></span>
  * <span data-ttu-id="d9bfc-124">根據呼叫來建立主機的 ASP.NET Core 範本，應用程式的預設記錄層級為 *警告* 或更高 `CreateDefaultBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-124">The default log level is *Warning* or higher for an app based on an ASP.NET Core template that calls `CreateDefaultBuilder` to build the host.</span></span>
  * <span data-ttu-id="d9bfc-125">使用 appsettings 中的金鑰覆寫預設記錄層級 `Logging:EventLog:LogLevel:Default` *appsettings.json* / *。 {環境}. json* 或其他設定提供者。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-125">Override the default log level with the `Logging:EventLog:LogLevel:Default` key in *appsettings.json*/*appsettings.{Environment}.json* or other configuration provider.</span></span>
  * <span data-ttu-id="d9bfc-126">只有系統管理員才能建立新的事件來源。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-126">Only administrators can create new event sources.</span></span> <span data-ttu-id="d9bfc-127">如果無法使用應用程式名稱建立事件來源，則會向「應用程式」來源記錄警告，並停用事件記錄檔。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-127">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

<span data-ttu-id="d9bfc-128">在 `CreateHostBuilder` *Program.cs* 中：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-128">In `CreateHostBuilder` of *Program.cs*:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

<span data-ttu-id="d9bfc-129">本主題隨附的下列範例應用程式：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-129">The following sample apps accompany this topic:</span></span>

* <span data-ttu-id="d9bfc-130">背景工作角色服務範例：以背景工作使用[託管服務](xref:fundamentals/host/hosted-services)的背景工作[服務範本](#worker-service-template)為基礎的非 web 應用程式範例。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-130">Background Worker Service Sample: A non-web app sample based on the [Worker Service template](#worker-service-template) that uses [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>
* <span data-ttu-id="d9bfc-131">Web App Service 範例： Razor 頁面 web 應用程式範例，該範例會以 Windows 服務的形式執行，並具有適用于背景工作的 [託管服務](xref:fundamentals/host/hosted-services) 。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-131">Web App Service Sample: A Razor Pages web app sample that runs as a Windows Service with [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>

<span data-ttu-id="d9bfc-132">如需 MVC 指引，請參閱和底下的文章 <xref:mvc/overview> <xref:migration/22-to-30> 。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-132">For MVC guidance, see the articles under <xref:mvc/overview> and <xref:migration/22-to-30>.</span></span>

## <a name="deployment-type"></a><span data-ttu-id="d9bfc-133">部署類型</span><span class="sxs-lookup"><span data-stu-id="d9bfc-133">Deployment type</span></span>

<span data-ttu-id="d9bfc-134">如需詳細資訊與部署案例建議，請參閱 [.NET Core 應用程式部署](/dotnet/core/deploying/)。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-134">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="d9bfc-135">SDK</span><span class="sxs-lookup"><span data-stu-id="d9bfc-135">SDK</span></span>

<span data-ttu-id="d9bfc-136">針對使用頁面或 MVC 架構的 web 應用程式型服務 Razor ，請在專案檔中指定 WEB SDK：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-136">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="d9bfc-137">如果服務只會執行背景工作 (例如， [託管服務](xref:fundamentals/host/hosted-services)) ，請在專案檔中指定背景工作 SDK：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-137">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="d9bfc-138">架構相依部署 (FDD)</span><span class="sxs-lookup"><span data-stu-id="d9bfc-138">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="d9bfc-139">架構相依部署 (FDD) 仰賴存在於目標系統上的全系統共用 .NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-139">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="d9bfc-140">依照此文章中的指導方針採用 FDD 案例時，SDK 會產生可執行檔 (*.exe*)，稱為「架構相依可執行檔」。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-140">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="d9bfc-141">如果使用 [WEB SDK](#sdk)，則在發佈 ASP.NET Core 應用程式時通常會產生的 *web.config* 檔案，對 Windows 服務應用程式而言是不必要的。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-141">If using the [Web SDK](#sdk), a *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="d9bfc-142">若要停用 *web.config* 檔案的建立，請新增 `<IsTransformWebConfigDisabled>` 屬性集到 `true`。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-142">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="d9bfc-143">自封式部署 (SCD)</span><span class="sxs-lookup"><span data-stu-id="d9bfc-143">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="d9bfc-144">自封式部署 (SCD) 不仰賴任何存在於主機系統上的共用架構。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-144">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="d9bfc-145">執行階段與應用程式的相依性會隨著應用程式進行部署。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-145">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="d9bfc-146">Windows [執行階段識別碼 (RID)](/dotnet/core/rid-catalog) 會納入包含目標 Framework 的 `<PropertyGroup>` 中：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-146">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="d9bfc-147">發行多個 RID：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-147">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="d9bfc-148">以分號分隔的清單提供 RID。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-148">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="d9bfc-149">使用屬性名稱 [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (複數) 。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-149">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="d9bfc-150">如需詳細資訊，請參閱 [.NET Core RID 目錄](/dotnet/core/rid-catalog)。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-150">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

## <a name="service-user-account"></a><span data-ttu-id="d9bfc-151">服務使用者帳戶</span><span class="sxs-lookup"><span data-stu-id="d9bfc-151">Service user account</span></span>

<span data-ttu-id="d9bfc-152">若要為服務建立使用者帳戶，請從系統管理 PowerShell 6 命令殼層使用 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) Cmdlet。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-152">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="d9bfc-153">Windows 10 2018 年 10 月更新 (版本 1809/組建 10.0.17763) 或更新版本：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-153">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="d9bfc-154">在 Windows 10 2018 年 10 月更新之前 (1809 版/組建 10.0.17763) 的 Windows OS 上：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-154">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="d9bfc-155">在系統提示時提供[強式密碼](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-155">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="d9bfc-156">除非搭配過期 <xref:System.DateTime> 將 `-AccountExpires` 參數提供給 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) Cmdlet，否則該帳戶將不會過期。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-156">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="d9bfc-157">如需詳細資訊，請參閱 [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) 和[服務使用者帳戶](/windows/desktop/services/service-user-accounts)。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-157">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="d9bfc-158">使用 Active Directory 時有一個管理使用者的替代方法，就是使用「受控服務帳戶」。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-158">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="d9bfc-159">如需詳細資訊，請參閱[群組受控服務帳戶概觀](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-159">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="d9bfc-160">以服務方式登入權限</span><span class="sxs-lookup"><span data-stu-id="d9bfc-160">Log on as a service rights</span></span>

<span data-ttu-id="d9bfc-161">若要為服務使用者帳戶建立「以服務方式登入」權限：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-161">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="d9bfc-162">執行 *secpol.msc* 來開啟 [本機安全性原則編輯器]。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-162">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="d9bfc-163">展開 [本機原則] 節點，然後選取 [使用者權限指派]。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-163">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="d9bfc-164">開啟 [以服務方式登入] 原則。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-164">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="d9bfc-165">選取 [新增使用者或群組]。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-165">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="d9bfc-166">使用下列其中一種方法提供物件名稱 (使用者帳戶)：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-166">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="d9bfc-167">在物件名稱欄位中輸入使用者帳戶 (`{DOMAIN OR COMPUTER NAME\USER}`)，然後選取 [確定] 將使用者新增至原則。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-167">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="d9bfc-168">選取 [進階]  。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-168">Select **Advanced**.</span></span> <span data-ttu-id="d9bfc-169">選取 [立即尋找]。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-169">Select **Find Now**.</span></span> <span data-ttu-id="d9bfc-170">從清單中選取使用者帳戶。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-170">Select the user account from the list.</span></span> <span data-ttu-id="d9bfc-171">選取 [確定]。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-171">Select **OK**.</span></span> <span data-ttu-id="d9bfc-172">再次選取 [確定] 將使用者新增至原則。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-172">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="d9bfc-173">選取 [確定] 或 [套用] 以接受變更。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-173">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="d9bfc-174">建立及管理 Windows 服務</span><span class="sxs-lookup"><span data-stu-id="d9bfc-174">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="d9bfc-175">建立服務</span><span class="sxs-lookup"><span data-stu-id="d9bfc-175">Create a service</span></span>

<span data-ttu-id="d9bfc-176">使用 PowerShell 命令來註冊服務。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-176">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="d9bfc-177">透過系統管理 PowerShell 6 命令殼層，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-177">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = "{DOMAIN OR COMPUTER NAME\USER}", "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName "{EXE FILE PATH}" -Credential "{DOMAIN OR COMPUTER NAME\USER}" -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="d9bfc-178">`{EXE PATH}`：應用程式在主機上的資料夾路徑 (例如 `d:\myservice`) 。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-178">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="d9bfc-179">請勿包含路徑中應用程式的可執行檔。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-179">Don't include the app's executable in the path.</span></span> <span data-ttu-id="d9bfc-180">不需要結尾的斜線。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-180">A trailing slash isn't required.</span></span>
* <span data-ttu-id="d9bfc-181">`{DOMAIN OR COMPUTER NAME\USER}`：服務使用者帳戶 (例如 `Contoso\ServiceUser`) 。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-181">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="d9bfc-182">`{SERVICE NAME}`：服務名稱 (例如 `MyService`) 。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-182">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="d9bfc-183">`{EXE FILE PATH}`：應用程式的可執行檔路徑 (例如 `d:\myservice\myservice.exe`) 。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-183">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="d9bfc-184">包含可執行檔的檔案名稱 (包含副檔名)。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-184">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="d9bfc-185">`{DESCRIPTION}`：服務描述 (例如 `My sample service`) 。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-185">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="d9bfc-186">`{DISPLAY NAME}`：服務顯示名稱 (例如 `My Service`) 。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-186">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="d9bfc-187">啟動服務</span><span class="sxs-lookup"><span data-stu-id="d9bfc-187">Start a service</span></span>

<span data-ttu-id="d9bfc-188">以下列 PowerShell 6 命令啟動服務：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-188">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="d9bfc-189">此命令需要幾秒鐘啓動服務。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-189">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="d9bfc-190">判斷服務的狀態</span><span class="sxs-lookup"><span data-stu-id="d9bfc-190">Determine a service's status</span></span>

<span data-ttu-id="d9bfc-191">若要檢查服務狀態，請使用下列 PowerShell 6 命令：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-191">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="d9bfc-192">狀態會回報為下列值之一：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-192">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="d9bfc-193">停止服務</span><span class="sxs-lookup"><span data-stu-id="d9bfc-193">Stop a service</span></span>

<span data-ttu-id="d9bfc-194">使用下列 Powershell 6 命令停止服務：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-194">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="d9bfc-195">移除服務</span><span class="sxs-lookup"><span data-stu-id="d9bfc-195">Remove a service</span></span>

<span data-ttu-id="d9bfc-196">在停止服務的短暫延遲之後，請以下列 Powershell 6 命令移除服務：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-196">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="d9bfc-197">Proxy 伺服器和負載平衡器案例</span><span class="sxs-lookup"><span data-stu-id="d9bfc-197">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="d9bfc-198">服務如果會與來自網際網路或公司網路的要求進行互動，並且位於 Proxy 或負載平衡器後方，可能會需要額外的設定。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-198">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="d9bfc-199">如需詳細資訊，請參閱<xref:host-and-deploy/proxy-load-balancer>。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-199">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="d9bfc-200">設定端點</span><span class="sxs-lookup"><span data-stu-id="d9bfc-200">Configure endpoints</span></span>

<span data-ttu-id="d9bfc-201">ASP.NET Core 預設會繫結至 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-201">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="d9bfc-202">藉由設定環境變數來設定 URL 和埠 `ASPNETCORE_URLS` 。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-202">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="d9bfc-203">如需其他 URL 和埠設定方法，請參閱相關的伺服器文章：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-203">For additional URL and port configuration approaches, see the relevant server article:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* <xref:fundamentals/servers/kestrel/endpoints>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

* <xref:fundamentals/servers/kestrel#endpoint-configuration>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="d9bfc-204">上述指引涵蓋 HTTPS 端點的支援。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-204">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="d9bfc-205">例如，在搭配 Windows 服務使用驗證時，請設定 HTTPS 的應用程式。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-205">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="d9bfc-206">不支援使用 ASP.NET Core HTTPS 開發憑證來保護服務端點。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-206">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="d9bfc-207">目前目錄和內容根目錄</span><span class="sxs-lookup"><span data-stu-id="d9bfc-207">Current directory and content root</span></span>

<span data-ttu-id="d9bfc-208">呼叫 Windows 服務所傳回的目前工作目錄 <xref:System.IO.Directory.GetCurrentDirectory%2A> 是 *C： \\ Windows \\ system32* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-208">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory%2A> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="d9bfc-209">*System32* 資料夾不是儲存服務檔案 (例如，設定檔) 的合適位置。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-209">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="d9bfc-210">使用下列其中一個方式來維護及存取服務的資產與設定檔。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-210">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="d9bfc-211">使用 ContentRootPath 或 ContentRootFileProvider</span><span class="sxs-lookup"><span data-stu-id="d9bfc-211">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="d9bfc-212">使用 [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) 或 <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> 來找出應用程式的資源。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-212">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

<span data-ttu-id="d9bfc-213">當應用程式以服務的形式執行時，會 <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService%2A> 將設定 <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> 為 [AppCoNtext. BaseDirectory](xref:System.AppContext.BaseDirectory)。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-213">When the app runs as a service, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService%2A> sets the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span>

<span data-ttu-id="d9bfc-214">應用程式的預設設定檔案， *appsettings.json* 以及 *appsettings. {* 從應用程式的內容根目錄載入環境} json，方法是 [在主機結構期間呼叫 >createdefaultbuilder](xref:fundamentals/host/generic-host#set-up-a-host)。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-214">The app's default settings files, *appsettings.json* and *appsettings.{Environment}.json*, are loaded from the app's content root by calling [CreateDefaultBuilder during host construction](xref:fundamentals/host/generic-host#set-up-a-host).</span></span>

<span data-ttu-id="d9bfc-215">若為開發人員程式碼在中載入的其他設定檔案 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> ，則不需要呼叫 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath%2A> 。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-215">For other settings files loaded by developer code in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>, there's no need to call <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath%2A>.</span></span> <span data-ttu-id="d9bfc-216">在下列範例中，檔案 *上的custom_settings.js* 會存在於應用程式的內容根目錄中，並在未明確設定基底路徑的情況下載入：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-216">In the following example, the *custom_settings.json* file exists in the app's content root and is loaded without explicitly setting a base path:</span></span>

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

<span data-ttu-id="d9bfc-217">請勿嘗試使用 <xref:System.IO.Directory.GetCurrentDirectory%2A> 來取得資源路徑，因為 Windows 服務應用程式會傳回 *C： \\ windows \\ system32* 資料夾做為其目前的目錄。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-217">Don't attempt to use <xref:System.IO.Directory.GetCurrentDirectory%2A> to obtain a resource path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder as its current directory.</span></span>

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="d9bfc-218">將服務的檔案儲存在磁碟上的適當位置</span><span class="sxs-lookup"><span data-stu-id="d9bfc-218">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="d9bfc-219">使用包含檔案的 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> 資料夾，使用 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath%2A> 來指定絕對路徑。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-219">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath%2A> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="d9bfc-220">疑難排解</span><span class="sxs-lookup"><span data-stu-id="d9bfc-220">Troubleshoot</span></span>

<span data-ttu-id="d9bfc-221">若要針對 Windows 服務應用程式進行疑難排解，請參閱 <xref:test/troubleshoot> 。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-221">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="d9bfc-222">常見錯誤</span><span class="sxs-lookup"><span data-stu-id="d9bfc-222">Common errors</span></span>

* <span data-ttu-id="d9bfc-223">舊版或發行前版本的 PowerShell 正在使用中。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-223">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="d9bfc-224">註冊的服務不會使用來自 [dotnet publish](/dotnet/core/tools/dotnet-publish)命令的應用程式 **已發佈** 輸出。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-224">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="d9bfc-225">應用程式部署不支援 [dotnet 組建](/dotnet/core/tools/dotnet-build) 命令的輸出。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-225">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="d9bfc-226">根據部署類型，您可以在下列其中一個資料夾中找到已發佈的資產：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-226">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="d9bfc-227">*bin/Release/{目標 FRAMEWORK}/publish* (FDD) </span><span class="sxs-lookup"><span data-stu-id="d9bfc-227">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="d9bfc-228">*bin/Release/{目標 FRAMEWORK}/{RUNTIME 識別碼}/publish* (SCD) </span><span class="sxs-lookup"><span data-stu-id="d9bfc-228">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="d9bfc-229">服務未處於執行中狀態。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-229">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="d9bfc-230">應用程式使用的資源路徑 (例如，憑證) 不正確。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-230">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="d9bfc-231">Windows 服務的基底路徑是 *c： \\ windows \\ System32*。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-231">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="d9bfc-232">使用者沒有 [ *以服務方式登* 入] 許可權。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-232">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="d9bfc-233">執行 PowerShell 命令時，使用者的密碼已過期或不正確地傳遞 `New-Service` 。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-233">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="d9bfc-234">應用程式需要 ASP.NET 核心驗證，但未設定 (HTTPS) 的安全連線。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-234">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="d9bfc-235">要求 URL 埠不正確，或在應用程式中未正確設定。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-235">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="d9bfc-236">系統和應用程式事件記錄檔</span><span class="sxs-lookup"><span data-stu-id="d9bfc-236">System and Application Event Logs</span></span>

<span data-ttu-id="d9bfc-237">存取系統和應用程式事件記錄：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-237">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="d9bfc-238">開啟 [開始] 功能表、搜尋 [ *事件檢視器]*，然後選取 [ **事件檢視器]** 應用程式。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-238">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="d9bfc-239">在 [事件檢視器] 中，開啟 [Windows 記錄] 節點。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-239">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="d9bfc-240">選取 [ **系統** ] 以開啟 [系統事件記錄檔]。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-240">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="d9bfc-241">選取 [應用程式] 以開啟「應用程式事件記錄檔」。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-241">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="d9bfc-242">搜尋與失敗應用程式相關的錯誤。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-242">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="d9bfc-243">在命令提示字元中執行應用程式</span><span class="sxs-lookup"><span data-stu-id="d9bfc-243">Run the app at a command prompt</span></span>

<span data-ttu-id="d9bfc-244">許多啟動錯誤都不會在事件記錄檔中產生有用的資訊。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-244">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="d9bfc-245">您可以藉由在主控系統上的命令提示字元中執行應用程式，來找出一些錯誤的原因。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-245">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="d9bfc-246">若要從應用程式記錄額外的詳細資料，請降低 [記錄層級](xref:fundamentals/logging/index#log-level) ，或在 [開發環境](xref:fundamentals/environments)中執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-246">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="d9bfc-247">清除套件快取</span><span class="sxs-lookup"><span data-stu-id="d9bfc-247">Clear package caches</span></span>

<span data-ttu-id="d9bfc-248">在開發電腦上升級 .NET Core SDK 或變更應用程式內的套件版本之後，正常運作的應用程式可能會立即失敗。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-248">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="d9bfc-249">在某些情況下，執行主要升級時，不一致的套件可能會中斷應用程式。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-249">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="d9bfc-250">大多數這些問題都可依照下列指示來進行修正：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-250">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="d9bfc-251">刪除 [bin] 和 [obj] 資料夾。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-251">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="d9bfc-252">從命令列介面執行 [dotnet nuget 區域變數](/dotnet/core/tools/dotnet-nuget-locals) ，以清除套件快取。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-252">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="d9bfc-253">清除套件快取也可以使用 [nuget.exe](https://www.nuget.org/downloads) 工具和執行命令來完成 `nuget locals all -clear` 。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-253">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="d9bfc-254">*nuget.exe* 並未隨附在 Windows 桌面作業系統的安裝中，必須另外從 [NuGet 網站](https://www.nuget.org/downloads)取得。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-254">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="d9bfc-255">還原並重建專案。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-255">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="d9bfc-256">重新部署應用程式之前，請先刪除伺服器上 [部署] 資料夾中的所有檔案。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-256">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="d9bfc-257">回應緩慢或無回應的應用程式</span><span class="sxs-lookup"><span data-stu-id="d9bfc-257">Slow or hanging app</span></span>

<span data-ttu-id="d9bfc-258">損 *毀* 傾印是系統記憶體的快照，可協助判斷應用程式損毀、啟動失敗或應用程式緩慢的原因。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-258">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="d9bfc-259">應用程式損毀或發生例外狀況</span><span class="sxs-lookup"><span data-stu-id="d9bfc-259">App crashes or encounters an exception</span></span>

<span data-ttu-id="d9bfc-260">從 [Windows 錯誤報告 (WER)](/windows/desktop/wer/windows-error-reporting) 取得並分析傾印：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-260">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="d9bfc-261">在 `c:\dumps` 中建立資料夾以保存損毀傾印檔案。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-261">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="d9bfc-262">使用應用程式可執行檔名稱來執行 [EnableDumps PowerShell 腳本](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) ：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-262">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```powershell
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="d9bfc-263">在會導致損毀的情況下，執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-263">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="d9bfc-264">發生損毀之後，請執行 [DisableDumps PowerShell 指令碼](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-264">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span></span>

   ```powershell
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="d9bfc-265">在應用程式損毀且傾印收集完成之後，即可正常結束應用程式。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-265">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="d9bfc-266">PowerShell 指令碼會將 WER 設定為每個應用程式收集最多五個傾印。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-266">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="d9bfc-267">損毀傾印可能會佔用大量磁碟空間 (每個高達好幾 GB)。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-267">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="d9bfc-268">應用程式停止回應、在啟動期間失敗，或正常執行</span><span class="sxs-lookup"><span data-stu-id="d9bfc-268">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="d9bfc-269">當 *應用程式* 停止回應時 (停止回應，但不會當機) 、啟動期間失敗，或正常執行時，請參閱 [使用者模式](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) 傾印檔案：選擇最適合的工具來選取適當的工具來產生傾印。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-269">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="d9bfc-270">分析傾印</span><span class="sxs-lookup"><span data-stu-id="d9bfc-270">Analyze the dump</span></span>

<span data-ttu-id="d9bfc-271">您可以使用數種方法來分析傾印。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-271">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="d9bfc-272">如需詳細資訊，請參閱[分析使用者模式傾印檔案](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-272">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d9bfc-273">其他資源</span><span class="sxs-lookup"><span data-stu-id="d9bfc-273">Additional resources</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"
* <span data-ttu-id="d9bfc-274">[Kestrel 端點組態](xref:fundamentals/servers/kestrel/endpoints) (包括 HTTPS 組態與 SNI 支援)</span><span class="sxs-lookup"><span data-stu-id="d9bfc-274">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel/endpoints) (includes HTTPS configuration and SNI support)</span></span>
::: moniker-end
::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"
* <span data-ttu-id="d9bfc-275">[Kestrel 端點組態](xref:fundamentals/servers/kestrel#endpoint-configuration) (包括 HTTPS 組態與 SNI 支援)</span><span class="sxs-lookup"><span data-stu-id="d9bfc-275">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="d9bfc-276">ASP.NET Core 應用程式可以裝載在 Windows 上作為 [Windows 服務](/dotnet/framework/windows-services/introduction-to-windows-service-applications)，不需要使用 IIS。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-276">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="d9bfc-277">當裝載為 Windows 服務時，應用程式將會在伺服器重新開機後自動啟動。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-277">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="d9bfc-278">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/windows-service/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="d9bfc-278">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d9bfc-279">必要條件</span><span class="sxs-lookup"><span data-stu-id="d9bfc-279">Prerequisites</span></span>

* [<span data-ttu-id="d9bfc-280">ASP.NET Core SDK 2.1 或更新版本</span><span class="sxs-lookup"><span data-stu-id="d9bfc-280">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="d9bfc-281">PowerShell 6.2 或更新版本</span><span class="sxs-lookup"><span data-stu-id="d9bfc-281">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="d9bfc-282">應用程式組態</span><span class="sxs-lookup"><span data-stu-id="d9bfc-282">App configuration</span></span>

<span data-ttu-id="d9bfc-283">應用程式需要 [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) 和 [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) 的套件參考。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-283">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="d9bfc-284">若要在於服務外執行時測試及偵錯，請新增程式碼以判斷應用程式是以服務形式執行或以主控台應用程式形式執行。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-284">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="d9bfc-285">檢查偵錯工具是否已附加或 `--console` 切換參數是否存在。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-285">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="d9bfc-286">若任一條件為真 (應用程式不是以服務形式執行)，請呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-286">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="d9bfc-287">若條件為偽 (應用程式是以服務形式執行)：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-287">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="d9bfc-288">呼叫 <xref:System.IO.Directory.SetCurrentDirectory*> 並使用應用程式發佈位置的路徑。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-288">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="d9bfc-289">請勿呼叫 <xref:System.IO.Directory.GetCurrentDirectory*> 來取得路徑，因為當呼叫 <xref:System.IO.Directory.GetCurrentDirectory*> 時，Windows 服務應用程式會傳回 *C:\\WINDOWS\\system32* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-289">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="d9bfc-290">如需詳細資訊，請參閱[目前目錄與內容根目錄](#current-directory-and-content-root)一節。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-290">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="d9bfc-291">在 `CreateWebHostBuilder` 中設定應用程式之前執行此步驟。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-291">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="d9bfc-292">呼叫 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> 以服務形式執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-292">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="d9bfc-293">因為[命令列設定提供者](xref:fundamentals/configuration/index#command-line-configuration-provider)需要命令列引數的名稱值組，在 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 收到引數之前，`--console` 切換參數就會從引數中移除。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-293">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="d9bfc-294">若要寫入 Windows 事件記錄檔，請新增 EventLog 提供者到 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-294">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="d9bfc-295">使用 *appsettings.Production.json* 檔案中的 `Logging:LogLevel:Default` 機碼設定記錄層級。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-295">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="d9bfc-296">在範例應用程式的下列範例中，為了處理應用程式內的存留期事件，會呼叫 `RunAsCustomService` 而不是 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-296">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="d9bfc-297">如需詳細資訊，請參閱[處理開始與停止事件](#handle-starting-and-stopping-events)一節。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-297">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="d9bfc-298">部署類型</span><span class="sxs-lookup"><span data-stu-id="d9bfc-298">Deployment type</span></span>

<span data-ttu-id="d9bfc-299">如需詳細資訊與部署案例建議，請參閱 [.NET Core 應用程式部署](/dotnet/core/deploying/)。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-299">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="d9bfc-300">SDK</span><span class="sxs-lookup"><span data-stu-id="d9bfc-300">SDK</span></span>

<span data-ttu-id="d9bfc-301">針對使用頁面或 MVC 架構的 web 應用程式型服務 Razor ，請在專案檔中指定 WEB SDK：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-301">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="d9bfc-302">如果服務只會執行背景工作 (例如， [託管服務](xref:fundamentals/host/hosted-services)) ，請在專案檔中指定背景工作 SDK：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-302">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="d9bfc-303">架構相依部署 (FDD)</span><span class="sxs-lookup"><span data-stu-id="d9bfc-303">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="d9bfc-304">架構相依部署 (FDD) 仰賴存在於目標系統上的全系統共用 .NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-304">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="d9bfc-305">依照此文章中的指導方針採用 FDD 案例時，SDK 會產生可執行檔 (*.exe*)，稱為「架構相依可執行檔」。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-305">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="d9bfc-306">Windows [執行時間識別碼 (RID) ](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) 包含目標 framework。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-306">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="d9bfc-307">在下列範例中，RID 已設定為 `win7-x64`。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-307">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="d9bfc-308">`<SelfContained>` 屬性會設定為 `false`。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-308">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="d9bfc-309">這些屬性會指示 SDK，針對 Windows 和相依於共用 .NET Core framework 的應用程式產生可執行檔 (*.exe*)。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-309">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="d9bfc-310">針對 Windows Services 應用程式，不需要 *web.config* 檔案 (發行 ASP.NET Core 應用程式時通常會產生此檔案)。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-310">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="d9bfc-311">若要停用 *web.config* 檔案的建立，請新增 `<IsTransformWebConfigDisabled>` 屬性集到 `true`。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-311">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="d9bfc-312">自封式部署 (SCD)</span><span class="sxs-lookup"><span data-stu-id="d9bfc-312">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="d9bfc-313">自封式部署 (SCD) 不仰賴任何存在於主機系統上的共用架構。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-313">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="d9bfc-314">執行階段與應用程式的相依性會隨著應用程式進行部署。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-314">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="d9bfc-315">Windows [執行階段識別碼 (RID)](/dotnet/core/rid-catalog) 會納入包含目標 Framework 的 `<PropertyGroup>` 中：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-315">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="d9bfc-316">發行多個 RID：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-316">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="d9bfc-317">以分號分隔的清單提供 RID。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-317">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="d9bfc-318">使用屬性名稱 [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (複數) 。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-318">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="d9bfc-319">如需詳細資訊，請參閱 [.NET Core RID 目錄](/dotnet/core/rid-catalog)。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-319">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="d9bfc-320">`<SelfContained>` 屬性設定為 `true`：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-320">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="d9bfc-321">服務使用者帳戶</span><span class="sxs-lookup"><span data-stu-id="d9bfc-321">Service user account</span></span>

<span data-ttu-id="d9bfc-322">若要為服務建立使用者帳戶，請從系統管理 PowerShell 6 命令殼層使用 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) Cmdlet。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-322">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="d9bfc-323">Windows 10 2018 年 10 月更新 (版本 1809/組建 10.0.17763) 或更新版本：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-323">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="d9bfc-324">在 Windows 10 2018 年 10 月更新之前 (1809 版/組建 10.0.17763) 的 Windows OS 上：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-324">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="d9bfc-325">在系統提示時提供[強式密碼](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-325">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="d9bfc-326">除非搭配過期 <xref:System.DateTime> 將 `-AccountExpires` 參數提供給 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) Cmdlet，否則該帳戶將不會過期。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-326">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="d9bfc-327">如需詳細資訊，請參閱 [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) 和[服務使用者帳戶](/windows/desktop/services/service-user-accounts)。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-327">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="d9bfc-328">使用 Active Directory 時有一個管理使用者的替代方法，就是使用「受控服務帳戶」。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-328">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="d9bfc-329">如需詳細資訊，請參閱[群組受控服務帳戶概觀](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-329">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="d9bfc-330">以服務方式登入權限</span><span class="sxs-lookup"><span data-stu-id="d9bfc-330">Log on as a service rights</span></span>

<span data-ttu-id="d9bfc-331">若要為服務使用者帳戶建立「以服務方式登入」權限：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-331">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="d9bfc-332">執行 *secpol.msc* 來開啟 [本機安全性原則編輯器]。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-332">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="d9bfc-333">展開 [本機原則] 節點，然後選取 [使用者權限指派]。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-333">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="d9bfc-334">開啟 [以服務方式登入] 原則。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-334">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="d9bfc-335">選取 [新增使用者或群組]。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-335">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="d9bfc-336">使用下列其中一種方法提供物件名稱 (使用者帳戶)：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-336">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="d9bfc-337">在物件名稱欄位中輸入使用者帳戶 (`{DOMAIN OR COMPUTER NAME\USER}`)，然後選取 [確定] 將使用者新增至原則。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-337">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="d9bfc-338">選取 [進階]  。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-338">Select **Advanced**.</span></span> <span data-ttu-id="d9bfc-339">選取 [立即尋找]。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-339">Select **Find Now**.</span></span> <span data-ttu-id="d9bfc-340">從清單中選取使用者帳戶。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-340">Select the user account from the list.</span></span> <span data-ttu-id="d9bfc-341">選取 [確定]。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-341">Select **OK**.</span></span> <span data-ttu-id="d9bfc-342">再次選取 [確定] 將使用者新增至原則。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-342">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="d9bfc-343">選取 [確定] 或 [套用] 以接受變更。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-343">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="d9bfc-344">建立及管理 Windows 服務</span><span class="sxs-lookup"><span data-stu-id="d9bfc-344">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="d9bfc-345">建立服務</span><span class="sxs-lookup"><span data-stu-id="d9bfc-345">Create a service</span></span>

<span data-ttu-id="d9bfc-346">使用 PowerShell 命令來註冊服務。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-346">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="d9bfc-347">透過系統管理 PowerShell 6 命令殼層，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-347">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="d9bfc-348">`{EXE PATH}`：應用程式在主機上的資料夾路徑 (例如 `d:\myservice`) 。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-348">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="d9bfc-349">請勿包含路徑中應用程式的可執行檔。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-349">Don't include the app's executable in the path.</span></span> <span data-ttu-id="d9bfc-350">不需要結尾的斜線。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-350">A trailing slash isn't required.</span></span>
* <span data-ttu-id="d9bfc-351">`{DOMAIN OR COMPUTER NAME\USER}`：服務使用者帳戶 (例如 `Contoso\ServiceUser`) 。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-351">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="d9bfc-352">`{SERVICE NAME}`：服務名稱 (例如 `MyService`) 。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-352">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="d9bfc-353">`{EXE FILE PATH}`：應用程式的可執行檔路徑 (例如 `d:\myservice\myservice.exe`) 。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-353">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="d9bfc-354">包含可執行檔的檔案名稱 (包含副檔名)。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-354">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="d9bfc-355">`{DESCRIPTION}`：服務描述 (例如 `My sample service`) 。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-355">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="d9bfc-356">`{DISPLAY NAME}`：服務顯示名稱 (例如 `My Service`) 。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-356">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="d9bfc-357">啟動服務</span><span class="sxs-lookup"><span data-stu-id="d9bfc-357">Start a service</span></span>

<span data-ttu-id="d9bfc-358">以下列 PowerShell 6 命令啟動服務：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-358">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="d9bfc-359">此命令需要幾秒鐘啓動服務。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-359">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="d9bfc-360">判斷服務的狀態</span><span class="sxs-lookup"><span data-stu-id="d9bfc-360">Determine a service's status</span></span>

<span data-ttu-id="d9bfc-361">若要檢查服務狀態，請使用下列 PowerShell 6 命令：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-361">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="d9bfc-362">狀態會回報為下列值之一：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-362">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="d9bfc-363">停止服務</span><span class="sxs-lookup"><span data-stu-id="d9bfc-363">Stop a service</span></span>

<span data-ttu-id="d9bfc-364">使用下列 Powershell 6 命令停止服務：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-364">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="d9bfc-365">移除服務</span><span class="sxs-lookup"><span data-stu-id="d9bfc-365">Remove a service</span></span>

<span data-ttu-id="d9bfc-366">在停止服務的短暫延遲之後，請以下列 Powershell 6 命令移除服務：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-366">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="d9bfc-367">處理開始與停止事件</span><span class="sxs-lookup"><span data-stu-id="d9bfc-367">Handle starting and stopping events</span></span>

<span data-ttu-id="d9bfc-368">若要處理 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>、<xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> 與 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> 事件：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-368">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="d9bfc-369">使用 `OnStarting`、`OnStarted` 與 `OnStopping` 方法建立衍生自 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> 的類別：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-369">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="d9bfc-370">為 <xref:Microsoft.AspNetCore.Hosting.IWebHost> 建立一個會將 `CustomWebHostService` 傳遞給 <xref:System.ServiceProcess.ServiceBase.Run*> 的擴充方法：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-370">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="d9bfc-371">在 `Program.Main` 中，呼叫 `RunAsCustomService` 擴充方法，而不是呼叫 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-371">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="d9bfc-372">若要查看 `Program.Main` 中的 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> 位置，請參閱[部署類型](#deployment-type)一節中的範例程式碼。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-372">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="d9bfc-373">Proxy 伺服器和負載平衡器案例</span><span class="sxs-lookup"><span data-stu-id="d9bfc-373">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="d9bfc-374">服務如果會與來自網際網路或公司網路的要求進行互動，並且位於 Proxy 或負載平衡器後方，可能會需要額外的設定。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-374">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="d9bfc-375">如需詳細資訊，請參閱<xref:host-and-deploy/proxy-load-balancer>。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-375">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="d9bfc-376">設定端點</span><span class="sxs-lookup"><span data-stu-id="d9bfc-376">Configure endpoints</span></span>

<span data-ttu-id="d9bfc-377">ASP.NET Core 預設會繫結至 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-377">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="d9bfc-378">藉由設定環境變數來設定 URL 和埠 `ASPNETCORE_URLS` 。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-378">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="d9bfc-379">如需其他 URL 和埠設定方法，請參閱相關的伺服器文章：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-379">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="d9bfc-380">上述指引涵蓋 HTTPS 端點的支援。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-380">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="d9bfc-381">例如，在搭配 Windows 服務使用驗證時，請設定 HTTPS 的應用程式。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-381">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="d9bfc-382">不支援使用 ASP.NET Core HTTPS 開發憑證來保護服務端點。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-382">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="d9bfc-383">目前目錄和內容根目錄</span><span class="sxs-lookup"><span data-stu-id="d9bfc-383">Current directory and content root</span></span>

<span data-ttu-id="d9bfc-384">呼叫 Windows 服務所傳回的目前工作目錄 <xref:System.IO.Directory.GetCurrentDirectory*> 是 *C： \\ Windows \\ system32* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-384">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="d9bfc-385">*System32* 資料夾不是儲存服務檔案 (例如，設定檔) 的合適位置。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-385">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="d9bfc-386">使用下列其中一個方式來維護及存取服務的資產與設定檔。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-386">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="d9bfc-387">將內容根目錄路徑設定到應用程式的資料夾</span><span class="sxs-lookup"><span data-stu-id="d9bfc-387">Set the content root path to the app's folder</span></span>

<span data-ttu-id="d9bfc-388"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> 與建立服務時提供給 `binPath` 引數的路徑相同。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-388">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="d9bfc-389">`GetCurrentDirectory`請呼叫 <xref:System.IO.Directory.SetCurrentDirectory*> 應用程式[內容根目錄](xref:fundamentals/index#content-root)的路徑，而不是呼叫來建立設定檔的路徑。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-389">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="d9bfc-390">在 `Program.Main` 中，判斷服務可執行檔資料夾的路徑，然後使用該路徑來建立應用程式的內容根目錄：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-390">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="d9bfc-391">將服務的檔案儲存在磁碟上的適當位置</span><span class="sxs-lookup"><span data-stu-id="d9bfc-391">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="d9bfc-392">使用包含檔案的 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> 資料夾，使用 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> 來指定絕對路徑。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-392">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="d9bfc-393">疑難排解</span><span class="sxs-lookup"><span data-stu-id="d9bfc-393">Troubleshoot</span></span>

<span data-ttu-id="d9bfc-394">若要針對 Windows 服務應用程式進行疑難排解，請參閱 <xref:test/troubleshoot> 。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-394">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="d9bfc-395">常見錯誤</span><span class="sxs-lookup"><span data-stu-id="d9bfc-395">Common errors</span></span>

* <span data-ttu-id="d9bfc-396">舊版或發行前版本的 PowerShell 正在使用中。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-396">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="d9bfc-397">註冊的服務不會使用來自 [dotnet publish](/dotnet/core/tools/dotnet-publish)命令的應用程式 **已發佈** 輸出。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-397">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="d9bfc-398">應用程式部署不支援 [dotnet 組建](/dotnet/core/tools/dotnet-build) 命令的輸出。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-398">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="d9bfc-399">根據部署類型，您可以在下列其中一個資料夾中找到已發佈的資產：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-399">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="d9bfc-400">*bin/Release/{目標 FRAMEWORK}/publish* (FDD) </span><span class="sxs-lookup"><span data-stu-id="d9bfc-400">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="d9bfc-401">*bin/Release/{目標 FRAMEWORK}/{RUNTIME 識別碼}/publish* (SCD) </span><span class="sxs-lookup"><span data-stu-id="d9bfc-401">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="d9bfc-402">服務未處於執行中狀態。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-402">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="d9bfc-403">應用程式使用的資源路徑 (例如，憑證) 不正確。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-403">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="d9bfc-404">Windows 服務的基底路徑是 *c： \\ windows \\ System32*。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-404">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="d9bfc-405">使用者沒有 [ *以服務方式登* 入] 許可權。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-405">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="d9bfc-406">執行 PowerShell 命令時，使用者的密碼已過期或不正確地傳遞 `New-Service` 。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-406">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="d9bfc-407">應用程式需要 ASP.NET 核心驗證，但未設定 (HTTPS) 的安全連線。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-407">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="d9bfc-408">要求 URL 埠不正確，或在應用程式中未正確設定。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-408">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="d9bfc-409">系統和應用程式事件記錄檔</span><span class="sxs-lookup"><span data-stu-id="d9bfc-409">System and Application Event Logs</span></span>

<span data-ttu-id="d9bfc-410">存取系統和應用程式事件記錄：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-410">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="d9bfc-411">開啟 [開始] 功能表、搜尋 [ *事件檢視器]*，然後選取 [ **事件檢視器]** 應用程式。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-411">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="d9bfc-412">在 [事件檢視器] 中，開啟 [Windows 記錄] 節點。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-412">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="d9bfc-413">選取 [ **系統** ] 以開啟 [系統事件記錄檔]。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-413">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="d9bfc-414">選取 [應用程式] 以開啟「應用程式事件記錄檔」。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-414">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="d9bfc-415">搜尋與失敗應用程式相關的錯誤。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-415">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="d9bfc-416">在命令提示字元中執行應用程式</span><span class="sxs-lookup"><span data-stu-id="d9bfc-416">Run the app at a command prompt</span></span>

<span data-ttu-id="d9bfc-417">許多啟動錯誤都不會在事件記錄檔中產生有用的資訊。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-417">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="d9bfc-418">您可以藉由在主控系統上的命令提示字元中執行應用程式，來找出一些錯誤的原因。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-418">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="d9bfc-419">若要從應用程式記錄額外的詳細資料，請降低 [記錄層級](xref:fundamentals/logging/index#log-level) ，或在 [開發環境](xref:fundamentals/environments)中執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-419">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="d9bfc-420">清除套件快取</span><span class="sxs-lookup"><span data-stu-id="d9bfc-420">Clear package caches</span></span>

<span data-ttu-id="d9bfc-421">在開發電腦上升級 .NET Core SDK 或變更應用程式內的套件版本之後，正常運作的應用程式可能會立即失敗。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-421">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="d9bfc-422">在某些情況下，執行主要升級時，不一致的套件可能會中斷應用程式。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-422">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="d9bfc-423">大多數這些問題都可依照下列指示來進行修正：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-423">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="d9bfc-424">刪除 [bin] 和 [obj] 資料夾。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-424">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="d9bfc-425">從命令列介面執行 [dotnet nuget 區域變數](/dotnet/core/tools/dotnet-nuget-locals) ，以清除套件快取。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-425">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="d9bfc-426">清除套件快取也可以使用 [nuget.exe](https://www.nuget.org/downloads) 工具和執行命令來完成 `nuget locals all -clear` 。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-426">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="d9bfc-427">*nuget.exe* 並未隨附在 Windows 桌面作業系統的安裝中，必須另外從 [NuGet 網站](https://www.nuget.org/downloads)取得。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-427">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="d9bfc-428">還原並重建專案。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-428">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="d9bfc-429">重新部署應用程式之前，請先刪除伺服器上 [部署] 資料夾中的所有檔案。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-429">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="d9bfc-430">回應緩慢或無回應的應用程式</span><span class="sxs-lookup"><span data-stu-id="d9bfc-430">Slow or hanging app</span></span>

<span data-ttu-id="d9bfc-431">損 *毀* 傾印是系統記憶體的快照，可協助判斷應用程式損毀、啟動失敗或應用程式緩慢的原因。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-431">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="d9bfc-432">應用程式損毀或發生例外狀況</span><span class="sxs-lookup"><span data-stu-id="d9bfc-432">App crashes or encounters an exception</span></span>

<span data-ttu-id="d9bfc-433">從 [Windows 錯誤報告 (WER)](/windows/desktop/wer/windows-error-reporting) 取得並分析傾印：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-433">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="d9bfc-434">在 `c:\dumps` 中建立資料夾以保存損毀傾印檔案。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-434">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="d9bfc-435">使用應用程式可執行檔名稱來執行 [EnableDumps PowerShell 腳本](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) ：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-435">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="d9bfc-436">在會導致損毀的情況下，執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-436">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="d9bfc-437">發生損毀之後，請執行 [DisableDumps PowerShell 指令碼](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-437">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="d9bfc-438">在應用程式損毀且傾印收集完成之後，即可正常結束應用程式。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-438">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="d9bfc-439">PowerShell 指令碼會將 WER 設定為每個應用程式收集最多五個傾印。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-439">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="d9bfc-440">損毀傾印可能會佔用大量磁碟空間 (每個高達好幾 GB)。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-440">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="d9bfc-441">應用程式停止回應、在啟動期間失敗，或正常執行</span><span class="sxs-lookup"><span data-stu-id="d9bfc-441">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="d9bfc-442">當 *應用程式* 停止回應時 (停止回應，但不會當機) 、啟動期間失敗，或正常執行時，請參閱 [使用者模式](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) 傾印檔案：選擇最適合的工具來選取適當的工具來產生傾印。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-442">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="d9bfc-443">分析傾印</span><span class="sxs-lookup"><span data-stu-id="d9bfc-443">Analyze the dump</span></span>

<span data-ttu-id="d9bfc-444">您可以使用數種方法來分析傾印。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-444">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="d9bfc-445">如需詳細資訊，請參閱[分析使用者模式傾印檔案](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-445">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d9bfc-446">其他資源</span><span class="sxs-lookup"><span data-stu-id="d9bfc-446">Additional resources</span></span>

* <span data-ttu-id="d9bfc-447">[Kestrel 端點組態](xref:fundamentals/servers/kestrel#endpoint-configuration) (包括 HTTPS 組態與 SNI 支援)</span><span class="sxs-lookup"><span data-stu-id="d9bfc-447">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="d9bfc-448">ASP.NET Core 應用程式可以裝載在 Windows 上作為 [Windows 服務](/dotnet/framework/windows-services/introduction-to-windows-service-applications)，不需要使用 IIS。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-448">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="d9bfc-449">當裝載為 Windows 服務時，應用程式將會在伺服器重新開機後自動啟動。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-449">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="d9bfc-450">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/windows-service/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="d9bfc-450">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d9bfc-451">必要條件</span><span class="sxs-lookup"><span data-stu-id="d9bfc-451">Prerequisites</span></span>

* [<span data-ttu-id="d9bfc-452">ASP.NET Core SDK 2.1 或更新版本</span><span class="sxs-lookup"><span data-stu-id="d9bfc-452">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="d9bfc-453">PowerShell 6.2 或更新版本</span><span class="sxs-lookup"><span data-stu-id="d9bfc-453">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="d9bfc-454">應用程式組態</span><span class="sxs-lookup"><span data-stu-id="d9bfc-454">App configuration</span></span>

<span data-ttu-id="d9bfc-455">應用程式需要 [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) 和 [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) 的套件參考。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-455">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="d9bfc-456">若要在於服務外執行時測試及偵錯，請新增程式碼以判斷應用程式是以服務形式執行或以主控台應用程式形式執行。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-456">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="d9bfc-457">檢查偵錯工具是否已附加或 `--console` 切換參數是否存在。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-457">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="d9bfc-458">若任一條件為真 (應用程式不是以服務形式執行)，請呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-458">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="d9bfc-459">若條件為偽 (應用程式是以服務形式執行)：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-459">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="d9bfc-460">呼叫 <xref:System.IO.Directory.SetCurrentDirectory*> 並使用應用程式發佈位置的路徑。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-460">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="d9bfc-461">請勿呼叫 <xref:System.IO.Directory.GetCurrentDirectory*> 來取得路徑，因為當呼叫 <xref:System.IO.Directory.GetCurrentDirectory*> 時，Windows 服務應用程式會傳回 *C:\\WINDOWS\\system32* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-461">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="d9bfc-462">如需詳細資訊，請參閱[目前目錄與內容根目錄](#current-directory-and-content-root)一節。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-462">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="d9bfc-463">在 `CreateWebHostBuilder` 中設定應用程式之前執行此步驟。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-463">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="d9bfc-464">呼叫 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> 以服務形式執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-464">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="d9bfc-465">因為[命令列設定提供者](xref:fundamentals/configuration/index#command-line-configuration-provider)需要命令列引數的名稱值組，在 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 收到引數之前，`--console` 切換參數就會從引數中移除。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-465">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="d9bfc-466">若要寫入 Windows 事件記錄檔，請新增 EventLog 提供者到 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-466">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="d9bfc-467">使用 *appsettings.Production.json* 檔案中的 `Logging:LogLevel:Default` 機碼設定記錄層級。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-467">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="d9bfc-468">在範例應用程式的下列範例中，為了處理應用程式內的存留期事件，會呼叫 `RunAsCustomService` 而不是 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-468">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="d9bfc-469">如需詳細資訊，請參閱[處理開始與停止事件](#handle-starting-and-stopping-events)一節。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-469">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="d9bfc-470">部署類型</span><span class="sxs-lookup"><span data-stu-id="d9bfc-470">Deployment type</span></span>

<span data-ttu-id="d9bfc-471">如需詳細資訊與部署案例建議，請參閱 [.NET Core 應用程式部署](/dotnet/core/deploying/)。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-471">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="d9bfc-472">SDK</span><span class="sxs-lookup"><span data-stu-id="d9bfc-472">SDK</span></span>

<span data-ttu-id="d9bfc-473">針對使用頁面或 MVC 架構的 web 應用程式型服務 Razor ，請在專案檔中指定 WEB SDK：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-473">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="d9bfc-474">如果服務只會執行背景工作 (例如， [託管服務](xref:fundamentals/host/hosted-services)) ，請在專案檔中指定背景工作 SDK：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-474">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="d9bfc-475">架構相依部署 (FDD)</span><span class="sxs-lookup"><span data-stu-id="d9bfc-475">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="d9bfc-476">架構相依部署 (FDD) 仰賴存在於目標系統上的全系統共用 .NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-476">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="d9bfc-477">依照此文章中的指導方針採用 FDD 案例時，SDK 會產生可執行檔 (*.exe*)，稱為「架構相依可執行檔」。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-477">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="d9bfc-478">Windows [執行時間識別碼 (RID) ](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) 包含目標 framework。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-478">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="d9bfc-479">在下列範例中，RID 已設定為 `win7-x64`。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-479">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="d9bfc-480">`<SelfContained>` 屬性會設定為 `false`。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-480">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="d9bfc-481">這些屬性會指示 SDK，針對 Windows 和相依於共用 .NET Core framework 的應用程式產生可執行檔 (*.exe*)。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-481">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="d9bfc-482">`<UseAppHost>` 屬性會設定為 `true`。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-482">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="d9bfc-483">此屬性為服務提供 FDD 的啟用路徑 (可執行檔 *.exe*)。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-483">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

<span data-ttu-id="d9bfc-484">針對 Windows Services 應用程式，不需要 *web.config* 檔案 (發行 ASP.NET Core 應用程式時通常會產生此檔案)。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-484">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="d9bfc-485">若要停用 *web.config* 檔案的建立，請新增 `<IsTransformWebConfigDisabled>` 屬性集到 `true`。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-485">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="d9bfc-486">自封式部署 (SCD)</span><span class="sxs-lookup"><span data-stu-id="d9bfc-486">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="d9bfc-487">自封式部署 (SCD) 不仰賴任何存在於主機系統上的共用架構。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-487">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="d9bfc-488">執行階段與應用程式的相依性會隨著應用程式進行部署。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-488">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="d9bfc-489">Windows [執行階段識別碼 (RID)](/dotnet/core/rid-catalog) 會納入包含目標 Framework 的 `<PropertyGroup>` 中：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-489">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="d9bfc-490">發行多個 RID：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-490">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="d9bfc-491">以分號分隔的清單提供 RID。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-491">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="d9bfc-492">使用屬性名稱 [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (複數) 。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-492">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="d9bfc-493">如需詳細資訊，請參閱 [.NET Core RID 目錄](/dotnet/core/rid-catalog)。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-493">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="d9bfc-494">`<SelfContained>` 屬性設定為 `true`：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-494">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="d9bfc-495">服務使用者帳戶</span><span class="sxs-lookup"><span data-stu-id="d9bfc-495">Service user account</span></span>

<span data-ttu-id="d9bfc-496">若要為服務建立使用者帳戶，請從系統管理 PowerShell 6 命令殼層使用 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) Cmdlet。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-496">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="d9bfc-497">Windows 10 2018 年 10 月更新 (版本 1809/組建 10.0.17763) 或更新版本：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-497">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="d9bfc-498">在 Windows 10 2018 年 10 月更新之前 (1809 版/組建 10.0.17763) 的 Windows OS 上：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-498">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="d9bfc-499">在系統提示時提供[強式密碼](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-499">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="d9bfc-500">除非搭配過期 <xref:System.DateTime> 將 `-AccountExpires` 參數提供給 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) Cmdlet，否則該帳戶將不會過期。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-500">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="d9bfc-501">如需詳細資訊，請參閱 [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) 和[服務使用者帳戶](/windows/desktop/services/service-user-accounts)。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-501">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="d9bfc-502">使用 Active Directory 時有一個管理使用者的替代方法，就是使用「受控服務帳戶」。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-502">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="d9bfc-503">如需詳細資訊，請參閱[群組受控服務帳戶概觀](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-503">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="d9bfc-504">以服務方式登入權限</span><span class="sxs-lookup"><span data-stu-id="d9bfc-504">Log on as a service rights</span></span>

<span data-ttu-id="d9bfc-505">若要為服務使用者帳戶建立「以服務方式登入」權限：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-505">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="d9bfc-506">執行 *secpol.msc* 來開啟 [本機安全性原則編輯器]。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-506">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="d9bfc-507">展開 [本機原則] 節點，然後選取 [使用者權限指派]。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-507">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="d9bfc-508">開啟 [以服務方式登入] 原則。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-508">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="d9bfc-509">選取 [新增使用者或群組]。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-509">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="d9bfc-510">使用下列其中一種方法提供物件名稱 (使用者帳戶)：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-510">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="d9bfc-511">在物件名稱欄位中輸入使用者帳戶 (`{DOMAIN OR COMPUTER NAME\USER}`)，然後選取 [確定] 將使用者新增至原則。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-511">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="d9bfc-512">選取 [進階]  。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-512">Select **Advanced**.</span></span> <span data-ttu-id="d9bfc-513">選取 [立即尋找]。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-513">Select **Find Now**.</span></span> <span data-ttu-id="d9bfc-514">從清單中選取使用者帳戶。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-514">Select the user account from the list.</span></span> <span data-ttu-id="d9bfc-515">選取 [確定]。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-515">Select **OK**.</span></span> <span data-ttu-id="d9bfc-516">再次選取 [確定] 將使用者新增至原則。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-516">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="d9bfc-517">選取 [確定] 或 [套用] 以接受變更。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-517">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="d9bfc-518">建立及管理 Windows 服務</span><span class="sxs-lookup"><span data-stu-id="d9bfc-518">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="d9bfc-519">建立服務</span><span class="sxs-lookup"><span data-stu-id="d9bfc-519">Create a service</span></span>

<span data-ttu-id="d9bfc-520">使用 PowerShell 命令來註冊服務。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-520">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="d9bfc-521">透過系統管理 PowerShell 6 命令殼層，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-521">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="d9bfc-522">`{EXE PATH}`：應用程式在主機上的資料夾路徑 (例如 `d:\myservice`) 。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-522">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="d9bfc-523">請勿包含路徑中應用程式的可執行檔。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-523">Don't include the app's executable in the path.</span></span> <span data-ttu-id="d9bfc-524">不需要結尾的斜線。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-524">A trailing slash isn't required.</span></span>
* <span data-ttu-id="d9bfc-525">`{DOMAIN OR COMPUTER NAME\USER}`：服務使用者帳戶 (例如 `Contoso\ServiceUser`) 。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-525">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="d9bfc-526">`{SERVICE NAME}`：服務名稱 (例如 `MyService`) 。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-526">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="d9bfc-527">`{EXE FILE PATH}`：應用程式的可執行檔路徑 (例如 `d:\myservice\myservice.exe`) 。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-527">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="d9bfc-528">包含可執行檔的檔案名稱 (包含副檔名)。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-528">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="d9bfc-529">`{DESCRIPTION}`：服務描述 (例如 `My sample service`) 。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-529">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="d9bfc-530">`{DISPLAY NAME}`：服務顯示名稱 (例如 `My Service`) 。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-530">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="d9bfc-531">啟動服務</span><span class="sxs-lookup"><span data-stu-id="d9bfc-531">Start a service</span></span>

<span data-ttu-id="d9bfc-532">以下列 PowerShell 6 命令啟動服務：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-532">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="d9bfc-533">此命令需要幾秒鐘啓動服務。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-533">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="d9bfc-534">判斷服務的狀態</span><span class="sxs-lookup"><span data-stu-id="d9bfc-534">Determine a service's status</span></span>

<span data-ttu-id="d9bfc-535">若要檢查服務狀態，請使用下列 PowerShell 6 命令：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-535">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="d9bfc-536">狀態會回報為下列值之一：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-536">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="d9bfc-537">停止服務</span><span class="sxs-lookup"><span data-stu-id="d9bfc-537">Stop a service</span></span>

<span data-ttu-id="d9bfc-538">使用下列 Powershell 6 命令停止服務：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-538">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="d9bfc-539">移除服務</span><span class="sxs-lookup"><span data-stu-id="d9bfc-539">Remove a service</span></span>

<span data-ttu-id="d9bfc-540">在停止服務的短暫延遲之後，請以下列 Powershell 6 命令移除服務：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-540">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="d9bfc-541">處理開始與停止事件</span><span class="sxs-lookup"><span data-stu-id="d9bfc-541">Handle starting and stopping events</span></span>

<span data-ttu-id="d9bfc-542">若要處理 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>、<xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> 與 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> 事件：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-542">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="d9bfc-543">使用 `OnStarting`、`OnStarted` 與 `OnStopping` 方法建立衍生自 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> 的類別：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-543">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="d9bfc-544">為 <xref:Microsoft.AspNetCore.Hosting.IWebHost> 建立一個會將 `CustomWebHostService` 傳遞給 <xref:System.ServiceProcess.ServiceBase.Run*> 的擴充方法：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-544">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="d9bfc-545">在 `Program.Main` 中，呼叫 `RunAsCustomService` 擴充方法，而不是呼叫 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-545">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="d9bfc-546">若要查看 `Program.Main` 中的 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> 位置，請參閱[部署類型](#deployment-type)一節中的範例程式碼。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-546">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="d9bfc-547">Proxy 伺服器和負載平衡器案例</span><span class="sxs-lookup"><span data-stu-id="d9bfc-547">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="d9bfc-548">服務如果會與來自網際網路或公司網路的要求進行互動，並且位於 Proxy 或負載平衡器後方，可能會需要額外的設定。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-548">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="d9bfc-549">如需詳細資訊，請參閱<xref:host-and-deploy/proxy-load-balancer>。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-549">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="d9bfc-550">設定端點</span><span class="sxs-lookup"><span data-stu-id="d9bfc-550">Configure endpoints</span></span>

<span data-ttu-id="d9bfc-551">ASP.NET Core 預設會繫結至 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-551">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="d9bfc-552">藉由設定環境變數來設定 URL 和埠 `ASPNETCORE_URLS` 。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-552">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="d9bfc-553">如需其他 URL 和埠設定方法，請參閱相關的伺服器文章：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-553">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="d9bfc-554">上述指引涵蓋 HTTPS 端點的支援。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-554">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="d9bfc-555">例如，在搭配 Windows 服務使用驗證時，請設定 HTTPS 的應用程式。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-555">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="d9bfc-556">不支援使用 ASP.NET Core HTTPS 開發憑證來保護服務端點。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-556">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="d9bfc-557">目前目錄和內容根目錄</span><span class="sxs-lookup"><span data-stu-id="d9bfc-557">Current directory and content root</span></span>

<span data-ttu-id="d9bfc-558">呼叫 Windows 服務所傳回的目前工作目錄 <xref:System.IO.Directory.GetCurrentDirectory*> 是 *C： \\ Windows \\ system32* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-558">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="d9bfc-559">*System32* 資料夾不是儲存服務檔案 (例如，設定檔) 的合適位置。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-559">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="d9bfc-560">使用下列其中一個方式來維護及存取服務的資產與設定檔。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-560">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="d9bfc-561">將內容根目錄路徑設定到應用程式的資料夾</span><span class="sxs-lookup"><span data-stu-id="d9bfc-561">Set the content root path to the app's folder</span></span>

<span data-ttu-id="d9bfc-562"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> 與建立服務時提供給 `binPath` 引數的路徑相同。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-562">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="d9bfc-563">`GetCurrentDirectory`請呼叫 <xref:System.IO.Directory.SetCurrentDirectory*> 應用程式[內容根目錄](xref:fundamentals/index#content-root)的路徑，而不是呼叫來建立設定檔的路徑。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-563">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="d9bfc-564">在 `Program.Main` 中，判斷服務可執行檔資料夾的路徑，然後使用該路徑來建立應用程式的內容根目錄：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-564">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="d9bfc-565">將服務的檔案儲存在磁碟上的適當位置</span><span class="sxs-lookup"><span data-stu-id="d9bfc-565">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="d9bfc-566">使用包含檔案的 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> 資料夾，使用 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> 來指定絕對路徑。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-566">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="d9bfc-567">疑難排解</span><span class="sxs-lookup"><span data-stu-id="d9bfc-567">Troubleshoot</span></span>

<span data-ttu-id="d9bfc-568">若要針對 Windows 服務應用程式進行疑難排解，請參閱 <xref:test/troubleshoot> 。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-568">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="d9bfc-569">常見錯誤</span><span class="sxs-lookup"><span data-stu-id="d9bfc-569">Common errors</span></span>

* <span data-ttu-id="d9bfc-570">舊版或發行前版本的 PowerShell 正在使用中。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-570">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="d9bfc-571">註冊的服務不會使用來自 [dotnet publish](/dotnet/core/tools/dotnet-publish)命令的應用程式 **已發佈** 輸出。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-571">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="d9bfc-572">應用程式部署不支援 [dotnet 組建](/dotnet/core/tools/dotnet-build) 命令的輸出。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-572">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="d9bfc-573">根據部署類型，您可以在下列其中一個資料夾中找到已發佈的資產：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-573">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="d9bfc-574">*bin/Release/{目標 FRAMEWORK}/publish* (FDD) </span><span class="sxs-lookup"><span data-stu-id="d9bfc-574">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="d9bfc-575">*bin/Release/{目標 FRAMEWORK}/{RUNTIME 識別碼}/publish* (SCD) </span><span class="sxs-lookup"><span data-stu-id="d9bfc-575">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="d9bfc-576">服務未處於執行中狀態。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-576">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="d9bfc-577">應用程式使用的資源路徑 (例如，憑證) 不正確。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-577">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="d9bfc-578">Windows 服務的基底路徑是 *c： \\ windows \\ System32*。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-578">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="d9bfc-579">使用者沒有 [ *以服務方式登* 入] 許可權。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-579">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="d9bfc-580">執行 PowerShell 命令時，使用者的密碼已過期或不正確地傳遞 `New-Service` 。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-580">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="d9bfc-581">應用程式需要 ASP.NET 核心驗證，但未設定 (HTTPS) 的安全連線。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-581">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="d9bfc-582">要求 URL 埠不正確，或在應用程式中未正確設定。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-582">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="d9bfc-583">系統和應用程式事件記錄檔</span><span class="sxs-lookup"><span data-stu-id="d9bfc-583">System and Application Event Logs</span></span>

<span data-ttu-id="d9bfc-584">存取系統和應用程式事件記錄：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-584">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="d9bfc-585">開啟 [開始] 功能表、搜尋 [ *事件檢視器]*，然後選取 [ **事件檢視器]** 應用程式。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-585">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="d9bfc-586">在 [事件檢視器] 中，開啟 [Windows 記錄] 節點。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-586">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="d9bfc-587">選取 [ **系統** ] 以開啟 [系統事件記錄檔]。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-587">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="d9bfc-588">選取 [應用程式] 以開啟「應用程式事件記錄檔」。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-588">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="d9bfc-589">搜尋與失敗應用程式相關的錯誤。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-589">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="d9bfc-590">在命令提示字元中執行應用程式</span><span class="sxs-lookup"><span data-stu-id="d9bfc-590">Run the app at a command prompt</span></span>

<span data-ttu-id="d9bfc-591">許多啟動錯誤都不會在事件記錄檔中產生有用的資訊。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-591">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="d9bfc-592">您可以藉由在主控系統上的命令提示字元中執行應用程式，來找出一些錯誤的原因。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-592">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="d9bfc-593">若要從應用程式記錄額外的詳細資料，請降低 [記錄層級](xref:fundamentals/logging/index#log-level) ，或在 [開發環境](xref:fundamentals/environments)中執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-593">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="d9bfc-594">清除套件快取</span><span class="sxs-lookup"><span data-stu-id="d9bfc-594">Clear package caches</span></span>

<span data-ttu-id="d9bfc-595">在開發電腦上升級 .NET Core SDK 或變更應用程式內的套件版本之後，正常運作的應用程式可能會立即失敗。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-595">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="d9bfc-596">在某些情況下，執行主要升級時，不一致的套件可能會中斷應用程式。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-596">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="d9bfc-597">大多數這些問題都可依照下列指示來進行修正：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-597">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="d9bfc-598">刪除 [bin] 和 [obj] 資料夾。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-598">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="d9bfc-599">從命令列介面執行 [dotnet nuget 區域變數](/dotnet/core/tools/dotnet-nuget-locals) ，以清除套件快取。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-599">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="d9bfc-600">清除套件快取也可以使用 [nuget.exe](https://www.nuget.org/downloads) 工具和執行命令來完成 `nuget locals all -clear` 。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-600">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="d9bfc-601">*nuget.exe* 並未隨附在 Windows 桌面作業系統的安裝中，必須另外從 [NuGet 網站](https://www.nuget.org/downloads)取得。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-601">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="d9bfc-602">還原並重建專案。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-602">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="d9bfc-603">重新部署應用程式之前，請先刪除伺服器上 [部署] 資料夾中的所有檔案。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-603">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="d9bfc-604">回應緩慢或無回應的應用程式</span><span class="sxs-lookup"><span data-stu-id="d9bfc-604">Slow or hanging app</span></span>

<span data-ttu-id="d9bfc-605">損 *毀* 傾印是系統記憶體的快照，可協助判斷應用程式損毀、啟動失敗或應用程式緩慢的原因。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-605">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="d9bfc-606">應用程式損毀或發生例外狀況</span><span class="sxs-lookup"><span data-stu-id="d9bfc-606">App crashes or encounters an exception</span></span>

<span data-ttu-id="d9bfc-607">從 [Windows 錯誤報告 (WER)](/windows/desktop/wer/windows-error-reporting) 取得並分析傾印：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-607">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="d9bfc-608">在 `c:\dumps` 中建立資料夾以保存損毀傾印檔案。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-608">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="d9bfc-609">使用應用程式可執行檔名稱來執行 [EnableDumps PowerShell 腳本](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) ：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-609">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="d9bfc-610">在會導致損毀的情況下，執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-610">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="d9bfc-611">發生損毀之後，請執行 [DisableDumps PowerShell 指令碼](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="d9bfc-611">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="d9bfc-612">在應用程式損毀且傾印收集完成之後，即可正常結束應用程式。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-612">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="d9bfc-613">PowerShell 指令碼會將 WER 設定為每個應用程式收集最多五個傾印。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-613">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="d9bfc-614">損毀傾印可能會佔用大量磁碟空間 (每個高達好幾 GB)。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-614">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="d9bfc-615">應用程式停止回應、在啟動期間失敗，或正常執行</span><span class="sxs-lookup"><span data-stu-id="d9bfc-615">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="d9bfc-616">當 *應用程式* 停止回應時 (停止回應，但不會當機) 、啟動期間失敗，或正常執行時，請參閱 [使用者模式](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) 傾印檔案：選擇最適合的工具來選取適當的工具來產生傾印。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-616">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="d9bfc-617">分析傾印</span><span class="sxs-lookup"><span data-stu-id="d9bfc-617">Analyze the dump</span></span>

<span data-ttu-id="d9bfc-618">您可以使用數種方法來分析傾印。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-618">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="d9bfc-619">如需詳細資訊，請參閱[分析使用者模式傾印檔案](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)。</span><span class="sxs-lookup"><span data-stu-id="d9bfc-619">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d9bfc-620">其他資源</span><span class="sxs-lookup"><span data-stu-id="d9bfc-620">Additional resources</span></span>

* <span data-ttu-id="d9bfc-621">[Kestrel 端點組態](xref:fundamentals/servers/kestrel#endpoint-configuration) (包括 HTTPS 組態與 SNI 支援)</span><span class="sxs-lookup"><span data-stu-id="d9bfc-621">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
