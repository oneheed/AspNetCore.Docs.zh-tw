---
title: 進階組態
author: rick-anderson
description: 使用 ASP.NET Core 模組和 Internet Information Services (IIS) 的 Advanced configuration。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 5/7/2020
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
uid: host-and-deploy/iis/advanced
ms.openlocfilehash: ad2480faeea2f07e51585f5bc6a1c63b3a0b1668
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755243"
---
# <a name="advanced-configuration-of-the-aspnet-core-module-and-iis"></a><span data-ttu-id="3f0c9-103">ASP.NET Core 模組和 IIS 的 Advanced configuration</span><span class="sxs-lookup"><span data-stu-id="3f0c9-103">Advanced configuration of the ASP.NET Core Module and IIS</span></span>

<span data-ttu-id="3f0c9-104">本文涵蓋 ASP.NET Core 模組和 IIS 的 advanced configuration 選項和案例。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-104">This article covers advanced configuration options and scenarios for the ASP.NET Core Module and IIS.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="3f0c9-105">修改堆疊大小</span><span class="sxs-lookup"><span data-stu-id="3f0c9-105">Modify the stack size</span></span>

<span data-ttu-id="3f0c9-106">*只有在使用同進程裝載模型時才適用。*</span><span class="sxs-lookup"><span data-stu-id="3f0c9-106">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="3f0c9-107">使用檔案中的 `stackSize` 設定（以位元組為單位）來設定受控堆疊大小 `web.config` 。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-107">Configure the managed stack size using the `stackSize` setting in bytes in the `web.config` file.</span></span> <span data-ttu-id="3f0c9-108">預設大小為1048576個位元組 (1 MB) 。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-108">The default size is 1,048,576 bytes (1 MB).</span></span> <span data-ttu-id="3f0c9-109">下列範例會將堆疊大小變更為 2 MB (2097152 個位元組) ：</span><span class="sxs-lookup"><span data-stu-id="3f0c9-109">The following example changes the stack size to 2 MB (2,097,152 bytes):</span></span>

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

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="3f0c9-110">Proxy 組態使用 HTTP 通訊協定和配對權杖</span><span class="sxs-lookup"><span data-stu-id="3f0c9-110">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="3f0c9-111">僅適用於跨處理序裝載。\*\*</span><span class="sxs-lookup"><span data-stu-id="3f0c9-111">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="3f0c9-112">在 ASP.NET Core 模組與 Kestrel 之間建立的 Proxy 會使用 HTTP 通訊協定。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-112">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="3f0c9-113">沒有從伺服器外的位置竊聽模組與 Kestrel 之間流量的風險。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-113">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="3f0c9-114">配對權杖用來保證 Kestrel 所接收的要求已由 IIS 代理，而且不是來自其他來源。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-114">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="3f0c9-115">模組會建立配對權杖，並將其設定成環境變數 (`ASPNETCORE_TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-115">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="3f0c9-116">配對權杖也會設定成每個代理要求的標頭 (`MS-ASPNETCORE-TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-116">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="3f0c9-117">IIS 中介軟體會檢查其收到的每個要求，以確認配對權杖的標頭值符合環境變數值。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-117">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="3f0c9-118">如果權杖值不相符，將記錄並拒絕要求。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-118">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="3f0c9-119">使用者無法從伺服器外的位置存取配對權杖環境變數，以及模組與 Kestrel 之間的流量。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-119">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="3f0c9-120">在不知道配對權杖值的情況下，攻擊者無法略過 IIS 中介軟體的檢查送出要求。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-120">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="3f0c9-121">具有 IIS 共用設定的 ASP.NET Core 模組</span><span class="sxs-lookup"><span data-stu-id="3f0c9-121">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="3f0c9-122">ASP.NET Core 模組安裝程式會以帳戶的許可權執行 `TrustedInstaller` 。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-122">The ASP.NET Core Module installer runs with the privileges of the `TrustedInstaller` account.</span></span> <span data-ttu-id="3f0c9-123">由於本機系統帳戶對於 IIS 共用設定所使用的共用路徑沒有 modify 許可權，因此，安裝程式在嘗試于共用上的檔案中設定模組設定時，會擲回「拒絕存取」錯誤 `applicationHost.config` 。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-123">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the `applicationHost.config` file on the share.</span></span>

<span data-ttu-id="3f0c9-124">在與 IIS 安裝相同的電腦上使用 IIS 共用設定時，請執行 ASP.NET Core 裝載套件組合安裝程式並將 `OPT_NO_SHARED_CONFIG_CHECK` 參數設為 `1`：</span><span class="sxs-lookup"><span data-stu-id="3f0c9-124">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="3f0c9-125">若共用設定的路徑位於與 IIS 安裝不同的電腦上，請遵循下列步驟：</span><span class="sxs-lookup"><span data-stu-id="3f0c9-125">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="3f0c9-126">停用「IIS 共用設定」。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-126">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="3f0c9-127">執行安裝程式。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-127">Run the installer.</span></span>
1. <span data-ttu-id="3f0c9-128">將更新的檔案匯出 `applicationHost.config` 至檔案共用。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-128">Export the updated `applicationHost.config` file to the file share.</span></span>
1. <span data-ttu-id="3f0c9-129">重新啟用「IIS 共用設定」。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-129">Re-enable the IIS Shared Configuration.</span></span>

## <a name="data-protection"></a><span data-ttu-id="3f0c9-130">資料保護</span><span class="sxs-lookup"><span data-stu-id="3f0c9-130">Data protection</span></span>

<span data-ttu-id="3f0c9-131">[ASP.NET Core 資料保護堆疊](xref:security/data-protection/introduction)是由數個 ASP.NET Core [中介軟體](xref:fundamentals/middleware/index)所使用，包括用於驗證的中介軟體。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-131">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="3f0c9-132">即使資料保護 API 不是由使用者程式碼呼叫，仍應使用部署指令碼或是在使用者程式碼中設定資料保護，以建立持續性的密碼編譯[金鑰存放區](xref:security/data-protection/implementation/key-management)。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-132">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="3f0c9-133">如不設定資料保護，金鑰會保留在記憶體中，並於應用程式重新啟動時捨棄。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-133">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="3f0c9-134">當應用程式重新開機時，如果資料保護金鑰環形儲存在記憶體中：</span><span class="sxs-lookup"><span data-stu-id="3f0c9-134">If the Data Protection key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="3f0c9-135">所有 cookie 的驗證權杖都會失效。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-135">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="3f0c9-136">當使用者提出下一個要求時，需要再次登入。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-136">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="3f0c9-137">所有以 Keyring 保護的資料都無法再解密。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-137">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="3f0c9-138">這可能包括 [CSRF 權杖](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) 和 [ASP.NET Core MVC TempData cookie s](xref:fundamentals/app-state#tempdata)。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-138">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="3f0c9-139">若要在 IIS 下設定資料保護以保存 Keyring，請使用下列其中**一種**方法：</span><span class="sxs-lookup"><span data-stu-id="3f0c9-139">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="3f0c9-140">**建立資料保護登錄機碼**</span><span class="sxs-lookup"><span data-stu-id="3f0c9-140">**Create Data Protection Registry keys**</span></span>

  <span data-ttu-id="3f0c9-141">ASP.NET Core apps 使用的資料保護金鑰會儲存在應用程式外部的登錄中。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-141">Data Protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="3f0c9-142">若要保存指定應用程式的金鑰，請為應用程式集區建立登錄機碼。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-142">To persist the keys for a given app, create Registry keys for the app pool.</span></span>

  <span data-ttu-id="3f0c9-143">若為獨立的非Web 伺服陣列 IIS 安裝，請針對搭配使用 ASP.NET Core 應用程式的每個應用程式集區，使用[資料保護 Provision-AutoGenKeys.ps1 PowerShell 指令碼](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1)。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-143">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="3f0c9-144">此腳本會在 HKLM 登錄中建立登錄機碼，只有應用程式的應用程式集區的背景工作進程帳戶才能存取。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-144">This script creates a Registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="3f0c9-145">在待用期間使用 DPAPI 和全電腦金鑰加密金鑰。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-145">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="3f0c9-146">在 web 伺服陣列案例中，您可以將應用程式設定為使用 UNC 路徑來儲存其資料保護金鑰環形。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-146">In web farm scenarios, an app can be configured to use a UNC path to store its Data Protection key ring.</span></span> <span data-ttu-id="3f0c9-147">依預設，金鑰不會加密。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-147">By default, the keys aren't encrypted.</span></span> <span data-ttu-id="3f0c9-148">確定網路共用的檔案許可權僅限於執行應用程式的 Windows 帳戶。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-148">Ensure that the file permissions for the network share are limited to the Windows account that the app runs under.</span></span> <span data-ttu-id="3f0c9-149">可以使用 X509 憑證來保護待用的金鑰。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-149">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="3f0c9-150">請考慮允許使用者上傳憑證的機制。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-150">Consider a mechanism to allow users to upload certificates.</span></span> <span data-ttu-id="3f0c9-151">將憑證放入使用者的受信任憑證存放區，並確保在執行使用者應用程式的所有電腦上都能使用這些憑證。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-151">Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="3f0c9-152">如需詳細資訊，請參閱<xref:security/data-protection/configuration/overview>。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-152">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>

* <span data-ttu-id="3f0c9-153">**設定 IIS 應用程式集區載入使用者設定檔**</span><span class="sxs-lookup"><span data-stu-id="3f0c9-153">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="3f0c9-154">此設定位在應用程式集區 [進階設定]\*\*\*\* 下的 [處理序模型]\*\*\*\* 區段中。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-154">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="3f0c9-155">將 [載入使用者設定檔]\*\*\*\* 設為 `True`。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-155">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="3f0c9-156">當設定為 `True` 時，金鑰會儲存在使用者設定檔目錄中，且使用具有使用者帳戶專屬金鑰的 DPAPI 保護。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-156">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="3f0c9-157">金鑰會保存在 `%LOCALAPPDATA%/ASP.NET/DataProtection-Keys` 資料夾中。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-157">Keys are persisted to the `%LOCALAPPDATA%/ASP.NET/DataProtection-Keys` folder.</span></span>

  <span data-ttu-id="3f0c9-158">您也必須啟用應用程式集區的[ `setProfileEnvironment` 屬性](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-158">The app pool's [`setProfileEnvironment` attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="3f0c9-159">`setProfileEnvironment` 的預設值為 `true`。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-159">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="3f0c9-160">在某些情況下 (例如 Windows OS)，`setProfileEnvironment` 會設為 `false`。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-160">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="3f0c9-161">如果金鑰並未如預期地儲存在使用者設定檔目錄中：</span><span class="sxs-lookup"><span data-stu-id="3f0c9-161">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="3f0c9-162">瀏覽到 `%windir%/system32/inetsrv/config` 資料夾。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-162">Navigate to the `%windir%/system32/inetsrv/config` folder.</span></span>
  1. <span data-ttu-id="3f0c9-163">開啟 `applicationHost.config` 檔案。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-163">Open the `applicationHost.config` file.</span></span>
  1. <span data-ttu-id="3f0c9-164">找出 `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 元素。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-164">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="3f0c9-165">確認 `setProfileEnvironment` 屬性不存在 (其預設值為 `true`)，或明確地將屬性值設為 `true`。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-165">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="3f0c9-166">**將檔案系統當作 Keyring 存放區使用**</span><span class="sxs-lookup"><span data-stu-id="3f0c9-166">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="3f0c9-167">調整應用程式程式碼，以[將檔案系統當作 Keyring 存放區使用](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-167">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="3f0c9-168">使用 X509 憑證來保護 Keyring，並確保憑證是受信任的憑證。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-168">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="3f0c9-169">如果憑證為自我簽署，請將憑證放在受信任的根存放區。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-169">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="3f0c9-170">在 Web 伺服陣列中使用 IIS 時：</span><span class="sxs-lookup"><span data-stu-id="3f0c9-170">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="3f0c9-171">請使用所有電腦都可以存取的檔案共用。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-171">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="3f0c9-172">將 X509 憑證部署到每一部電腦。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-172">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="3f0c9-173">[在程式碼中設定資料保護](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-173">Configure [Data Protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="3f0c9-174">**設定適用于資料保護的全電腦原則**</span><span class="sxs-lookup"><span data-stu-id="3f0c9-174">**Set a machine-wide policy for Data Protection**</span></span>

  <span data-ttu-id="3f0c9-175">針對取用資料保護 Api 的所有應用程式，資料保護系統的支援有限，可設定預設的全 [電腦原則](xref:security/data-protection/configuration/machine-wide-policy) 。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-175">The Data Protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="3f0c9-176">如需詳細資訊，請參閱<xref:security/data-protection/introduction>。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-176">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="3f0c9-177">IIS 組態</span><span class="sxs-lookup"><span data-stu-id="3f0c9-177">IIS configuration</span></span>

<span data-ttu-id="3f0c9-178">**Windows Server 作業系統**</span><span class="sxs-lookup"><span data-stu-id="3f0c9-178">**Windows Server operating systems**</span></span>

<span data-ttu-id="3f0c9-179">啟用**網頁伺服器 (IIS)** 伺服器角色，並建立角色服務。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-179">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="3f0c9-180">使用來自 [管理]\*\*\*\* 功能表的 [新增角色及功能]\*\*\*\* 精靈，或是 [伺服器管理員]\*\*\*\* 中的連結。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-180">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="3f0c9-181">在**伺服器角色**步驟中，核取 [網頁伺服器 (IIS)]\*\*\*\* 方塊。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-181">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![在選取伺服器角色步驟中選取網頁伺服器 IIS 角色。](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="3f0c9-183">在 [功能]\*\*\*\* 步驟之後，[角色服務]\*\*\*\* 步驟會針對網頁伺服器 (IIS) 進行載入。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-183">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="3f0c9-184">選取所需的 IIS 角色服務或接受所提供的預設角色服務。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-184">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![在選取角色服務步驟中，選取預設的角色服務。](index/_static/role-services-ws2016.png)

   <span data-ttu-id="3f0c9-186">**Windows 驗證 (選擇性)**</span><span class="sxs-lookup"><span data-stu-id="3f0c9-186">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="3f0c9-187">若要啟用 Windows 驗證，請展開下列節點：**網頁伺服器**  >  **安全性**。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-187">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="3f0c9-188">選取 [Windows 驗證]\*\*\*\* 功能。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-188">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="3f0c9-189">如需詳細資訊，請參閱[Windows 驗證 `<windowsAuthentication>` ](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)和[設定 Windows 驗證](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-189">For more information, see [Windows Authentication `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="3f0c9-190">**WebSocket (選擇性)**</span><span class="sxs-lookup"><span data-stu-id="3f0c9-190">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="3f0c9-191">WebSocket 由 ASP.NET Core 1.1 或更新版本所支援。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-191">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="3f0c9-192">若要啟用 websocket，請展開下列節點：**網頁伺服器**  >  **應用程式開發**。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-192">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="3f0c9-193">選取 [WebSocket 通訊協定]\*\*\*\* 功能。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-193">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="3f0c9-194">如需詳細資訊，請參閱 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-194">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="3f0c9-195">透過**確認**步驟繼續作業，安裝網頁伺服器角色和服務。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-195">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="3f0c9-196">在安裝 \*\*Web 服務器 (iis) \*\* 角色之後，不需要重新開機伺服器。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-196">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="3f0c9-197">**Windows 桌面作業系統**</span><span class="sxs-lookup"><span data-stu-id="3f0c9-197">**Windows desktop operating systems**</span></span>

<span data-ttu-id="3f0c9-198">啟用 [IIS 管理主控台]\*\*\*\* 和 [World Wide Web 服務]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-198">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="3f0c9-199">流覽至**主控台**  >  **程式**的 [  >  **程式和功能]，**  >  **開啟或關閉**畫面) 左側 (的 [Windows 功能]。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-199">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="3f0c9-200">開啟 [Internet Information Services]\*\*\*\* 節點。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-200">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="3f0c9-201">開啟 [Web 管理工具]\*\*\*\* 節點。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-201">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="3f0c9-202">核取 [IIS 管理主控台]\*\*\*\* 方塊。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-202">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="3f0c9-203">[World Wide Web Services] (全球資訊網服務)\*\*\*\* 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-203">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="3f0c9-204">接受**全球資訊網服務**的預設功能，或自訂 IIS 功能。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-204">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="3f0c9-205">**Windows 驗證 (選擇性)**</span><span class="sxs-lookup"><span data-stu-id="3f0c9-205">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="3f0c9-206">若要啟用 Windows 驗證，請展開下列節點： **World Wide Web 服務**  >  **安全性**。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-206">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="3f0c9-207">選取 [Windows 驗證]\*\*\*\* 功能。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-207">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="3f0c9-208">如需詳細資訊，請參閱[Windows 驗證 `<windowsAuthentication>` ](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)和[設定 Windows 驗證](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-208">For more information, see [Windows Authentication `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="3f0c9-209">**WebSocket (選擇性)**</span><span class="sxs-lookup"><span data-stu-id="3f0c9-209">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="3f0c9-210">WebSocket 由 ASP.NET Core 1.1 或更新版本所支援。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-210">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="3f0c9-211">若要啟用 websocket，請展開下列節點： **World Wide Web 服務**  >  **應用程式開發功能**。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-211">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="3f0c9-212">選取 [WebSocket 通訊協定]\*\*\*\* 功能。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-212">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="3f0c9-213">如需詳細資訊，請參閱 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-213">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="3f0c9-214">若 IIS 安裝需要重新啟動，請重新啟動系統。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-214">If the IIS installation requires a restart, restart the system.</span></span>

![選取 [Windows 功能] 中的 [IIS 管理主控台] 和 [World Wide Web Services] (全球資訊網服務)。](index/_static/windows-features-win10.png)

## <a name="virtual-directories"></a><span data-ttu-id="3f0c9-216">虛擬目錄</span><span class="sxs-lookup"><span data-stu-id="3f0c9-216">Virtual Directories</span></span>

<span data-ttu-id="3f0c9-217">ASP.NET Core 應用程式不支援 [IIS 虛擬目錄](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-217">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="3f0c9-218">應用程式能以[子應用程式](#sub-applications)的形式裝載。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-218">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="3f0c9-219">子應用程式</span><span class="sxs-lookup"><span data-stu-id="3f0c9-219">Sub-applications</span></span>

<span data-ttu-id="3f0c9-220">ASP.NET Core 應用程式能以 [IIS 子應用程式](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)的形式裝載。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-220">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="3f0c9-221">子應用程式的路徑會成為根應用程式 URL 的一部分。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-221">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="3f0c9-222">子應用程式內的靜態資產連結應該使用波狀符號與斜線 (`~/`) 標記法。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-222">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="3f0c9-223">波狀符號與斜線標記法會觸發[標記協助程式](xref:mvc/views/tag-helpers/intro)以將子應用程式的路徑基底附加到轉譯的相對連結前面。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-223">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="3f0c9-224">針對位於 `/subapp_path` 的子應用程式，使用 `src="~/image.png"` 連結的影像會轉譯為 `src="/subapp_path/image.png"`。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-224">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="3f0c9-225">根應用程式的靜態檔案中介軟體不會處理靜態檔案要求。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-225">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="3f0c9-226">要求會由子應用程式的靜態檔案中介軟體處理。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-226">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="3f0c9-227">若靜態資產的 `src` 屬性是設定為絕對路徑 (例如，`src="/image.png"`)，會以不使用子應用程式路徑基底的方式轉譯連結。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-227">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="3f0c9-228">根應用程式的靜態檔案中介軟體會嘗試從根應用程式的 [webroot](xref:fundamentals/index#web-root) 提供資產，這會導致「404 - 找不到」\*\* 回應 (除非根應用程式可存取靜態資產)。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-228">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="3f0c9-229">裝載 ASP.NET Core 應用程式做為另一個 ASP.NET Core 應用程式下的子應用程式：</span><span class="sxs-lookup"><span data-stu-id="3f0c9-229">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="3f0c9-230">為子應用程式建立應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-230">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="3f0c9-231">將 [.NET CLR 版本]\*\*\*\* 設定為 [沒有受控碼]\*\*\*\*，因為會將核心通用語言執行平台 (CoreCLR) 開機以在背景工作處理序中裝載應用程式，而非在桌面 CLR (.NET CLR) 中裝載。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-231">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="3f0c9-232">使用根網站下資料夾中的子應用程式在 IIS 管理員中新增根網站。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-232">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="3f0c9-233">以滑鼠右鍵按一下 IIS 管理員中的子應用程式資料夾，然後選取 [轉換成應用程式]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-233">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="3f0c9-234">在 [新增應用程式]\*\*\*\* 對話方塊中，使用 [應用程式集區]\*\*\*\* 的[選取]\*\*\*\* 按鈕來指派您為子應用程式建立的應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-234">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="3f0c9-235">選取 [確定]  。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-235">Select **OK**.</span></span>

<span data-ttu-id="3f0c9-236">將不同的應用程式集區指派給子應用程式是使用同處理序裝載模型。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-236">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="3f0c9-237">如需有關同進程裝載模型和設定 ASP.NET Core 模組的詳細資訊，請參閱 <xref:host-and-deploy/aspnet-core-module> 。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-237">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="application-pools"></a><span data-ttu-id="3f0c9-238">應用程式集區</span><span class="sxs-lookup"><span data-stu-id="3f0c9-238">Application Pools</span></span>

<span data-ttu-id="3f0c9-239">應用程式集區隔離取決於裝載模型：</span><span class="sxs-lookup"><span data-stu-id="3f0c9-239">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="3f0c9-240">同進程裝載：應用程式必須在不同的應用程式集區中執行。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-240">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="3f0c9-241">跨進程裝載：建議您在應用程式本身的應用程式集區中執行每個應用程式，以隔離彼此的應用程式。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-241">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="3f0c9-242">IIS [新增網站]\*\*\*\* 對話方塊預設每個應用程式皆為單一應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-242">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="3f0c9-243">當提供**網站名稱**時，文字會自動轉移至 [應用程式集區]\*\*\*\* 文字方塊。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-243">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="3f0c9-244">新增網站時，會使用該網站名稱建立新的應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-244">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-no-locidentity"></a><span data-ttu-id="3f0c9-245">應用程式集區 Identity</span><span class="sxs-lookup"><span data-stu-id="3f0c9-245">Application Pool Identity</span></span>

<span data-ttu-id="3f0c9-246">應用程式集區身分識別帳戶可讓應用程式在唯一的帳戶下執行，不必建立及管理網域或本機帳戶。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-246">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="3f0c9-247">在 IIS 8.0 或更新版本中，IIS 管理背景工作處理序 (WAS) 會使用新的應用程式集區名稱建立虛擬帳戶，並預設在此帳戶下執行應用程式集區的背景工作處理序。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-247">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="3f0c9-248">在 [IIS 管理主控台] 中，于應用程式集區的 [ **Advanced Settings** ] 底下，確定 **Identity** 已設定為 [使用 `ApplicationPoolIdentity` ：</span><span class="sxs-lookup"><span data-stu-id="3f0c9-248">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use `ApplicationPoolIdentity`:</span></span>

![應用程式集區進階設定對話方塊](index/_static/apppool-identity.png)

<span data-ttu-id="3f0c9-250">IIS 管理程序會在 Windows 安全系統中，以應用程式集區的名稱建立安全識別碼。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-250">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="3f0c9-251">可使用此身分識別來保護資源。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-251">Resources can be secured using this identity.</span></span> <span data-ttu-id="3f0c9-252">不過，此身分識別不是真正的使用者帳戶，也不會顯示在 Windows 使用者管理主控台中。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-252">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="3f0c9-253">如果 IIS 背景工作處理序需要提升應用程式的存取權限，請修改包含應用程式的目錄存取控制清單 (ACL)：</span><span class="sxs-lookup"><span data-stu-id="3f0c9-253">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="3f0c9-254">開啟 Windows 檔案總管，巡覽至目錄。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-254">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="3f0c9-255">以滑鼠右鍵按一下目錄並選取 [屬性]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-255">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="3f0c9-256">依序選取 [安全性]\*\*\*\* 索引標籤下的 [編輯]\*\*\*\* 按鈕和 [新增]\*\*\*\* 按鈕。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-256">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="3f0c9-257">選取 [位置]\*\*\*\* 按鈕，並確定選取系統。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-257">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="3f0c9-258">輸入 `IIS AppPool\{APP POOL NAME}` 格式，其中預留位置 `{APP POOL NAME}` 是應用程式集區名稱，在 [ **輸入物件名稱來選取** ] 區域中。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-258">Enter `IIS AppPool\{APP POOL NAME}` format, where the placeholder `{APP POOL NAME}` is the app pool name, in **Enter the object names to select** area.</span></span> <span data-ttu-id="3f0c9-259">選取 [檢查名稱]\*\*\*\* 按鈕。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-259">Select the **Check Names** button.</span></span> <span data-ttu-id="3f0c9-260">若為 *DefaultAppPool* ，請使用來檢查名稱 `IIS AppPool\DefaultAppPool` 。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-260">For the *DefaultAppPool* check the names using `IIS AppPool\DefaultAppPool`.</span></span> <span data-ttu-id="3f0c9-261">選取 [ **檢查名稱** ] 按鈕時，的值 `DefaultAppPool` 會在 [物件名稱] 區域中指出。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-261">When the **Check Names** button is selected, a value of `DefaultAppPool` is indicated in the object names area.</span></span> <span data-ttu-id="3f0c9-262">您無法直接將應用程式集區名稱輸入至物件名稱區域。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-262">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="3f0c9-263">`IIS AppPool\{APP POOL NAME}`檢查物件名稱時，請使用格式，其中預留位置 `{APP POOL NAME}` 是應用程式集區名稱。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-263">Use the `IIS AppPool\{APP POOL NAME}` format, where the placeholder `{APP POOL NAME}` is the app pool name, when checking for the object name.</span></span>

   ![針對應用程式資料夾選取使用者或群組對話方塊：在選取 [檢查名稱] 之前，"DefaultAppPool" 這個應用程式集區名稱在物件名稱區域中會附加至 "IIS AppPool\"。](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="3f0c9-265">選取 [確定]  。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-265">Select **OK**.</span></span>

   ![針對應用程式資料夾選取使用者或群組對話方塊：選取 [檢查名稱] 之後，物件名稱 "DefaultAppPool" 會顯示在物件名稱區域中。](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="3f0c9-267">預設應該會授與讀取 &amp; 執行權限。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-267">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="3f0c9-268">請視需要提供其他權限。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-268">Provide additional permissions as needed.</span></span>

<span data-ttu-id="3f0c9-269">也可使用 **ICACLS** 工具透過命令提示字元授與存取權限。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-269">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="3f0c9-270">使用 *DefaultAppPool*作為範例，將會使用下列命令：</span><span class="sxs-lookup"><span data-stu-id="3f0c9-270">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="3f0c9-271">如需詳細資訊，請參閱 [icacls](/windows-server/administration/windows-commands/icacls) 主題。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-271">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="3f0c9-272">HTTP/2 支援</span><span class="sxs-lookup"><span data-stu-id="3f0c9-272">HTTP/2 support</span></span>

<span data-ttu-id="3f0c9-273">在下列 IIS 部署案例中，ASP.NET Core 支援 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="3f0c9-273">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="3f0c9-274">內含式</span><span class="sxs-lookup"><span data-stu-id="3f0c9-274">In-process</span></span>
  * <span data-ttu-id="3f0c9-275">Windows Server 2016/Windows 10 或更新版本；IIS 10 或更新版本</span><span class="sxs-lookup"><span data-stu-id="3f0c9-275">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="3f0c9-276">TLS 1.2 或更新版本連線</span><span class="sxs-lookup"><span data-stu-id="3f0c9-276">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="3f0c9-277">跨處理序</span><span class="sxs-lookup"><span data-stu-id="3f0c9-277">Out-of-process</span></span>
  * <span data-ttu-id="3f0c9-278">Windows Server 2016/Windows 10 或更新版本；IIS 10 或更新版本</span><span class="sxs-lookup"><span data-stu-id="3f0c9-278">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="3f0c9-279">公開 Edge Server 連線使用 HTTP/2，但是對 [Kestrel 伺服器](xref:fundamentals/servers/kestrel)的反向 Proxy 連線使用 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-279">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="3f0c9-280">TLS 1.2 或更新版本連線</span><span class="sxs-lookup"><span data-stu-id="3f0c9-280">TLS 1.2 or later connection</span></span>

<span data-ttu-id="3f0c9-281">若為建立 HTTP/2 連接時的同進程部署，則為 [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 報告 `HTTP/2` 。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-281">For an in-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="3f0c9-282">若為建立 HTTP/2 連接時的跨進程部署，則為 [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 報告 `HTTP/1.1` 。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-282">For an out-of-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="3f0c9-283">如需有關同處理序和跨處理序主控模型的詳細資訊，請參閱 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-283">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="3f0c9-284">HTTP/2 預設為啟用。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-284">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="3f0c9-285">如果 HTTP/2 連線尚未建立，連線會退為 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-285">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="3f0c9-286">如需使用 IIS 部署之 HTTP/2 設定的詳細資訊，請參閱 [IIS 上的 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-286">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="3f0c9-287">CORS 預檢要求</span><span class="sxs-lookup"><span data-stu-id="3f0c9-287">CORS preflight requests</span></span>

<span data-ttu-id="3f0c9-288">*此節只適用於以 .NET Framework 為目標的 ASP.NET Core 應用程式。*</span><span class="sxs-lookup"><span data-stu-id="3f0c9-288">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="3f0c9-289">針對以 .NET Framework 為目標的 ASP.NET Core 應用程式，在 IIS 中OPTIONS 要求預設不會傳遞到應用程式。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-289">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="3f0c9-290">若要瞭解如何在中設定應用程式的 IIS 處理常式 `web.config` 來傳遞選項要求，請參閱 [在 ASP.NET Web API 2： CORS 的運作方式中啟用跨原始來源要求](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-290">To learn how to configure the app's IIS handlers in `web.config` to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="3f0c9-291">應用程式初始化模組與閒置逾時</span><span class="sxs-lookup"><span data-stu-id="3f0c9-291">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="3f0c9-292">在 IIS 中由 ASP.NET Core 模組版本 2 裝載時：</span><span class="sxs-lookup"><span data-stu-id="3f0c9-292">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="3f0c9-293">[應用程式初始化模組](#application-initialization-module)：應用程式裝載的同 [進程](xref:host-and-deploy/iis/in-process-hosting) 或 [跨進程](xref:host-and-deploy/iis/out-of-process-hosting) 可設定為在背景工作進程重新開機或伺服器重新開機時自動啟動。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-293">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](xref:host-and-deploy/iis/in-process-hosting) or [out-of-process](xref:host-and-deploy/iis/out-of-process-hosting) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="3f0c9-294">[閒置 Timeout](#idle-timeout)：應用程式裝載的同 [進程](xref:host-and-deploy/iis/in-process-hosting) 可設定為在無活動期間停止執行時間。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-294">[Idle Timeout](#idle-timeout): App's hosted [in-process](xref:host-and-deploy/iis/in-process-hosting) can be configured not to time out during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="3f0c9-295">應用程式初始化模組</span><span class="sxs-lookup"><span data-stu-id="3f0c9-295">Application Initialization Module</span></span>

<span data-ttu-id="3f0c9-296">*套用到應用程式裝載同處理序與非同處理序。*</span><span class="sxs-lookup"><span data-stu-id="3f0c9-296">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="3f0c9-297">[IIS 應用程式初始化](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)是一項 IIS 功能，可在應用程式集區啟動或回收時，將 HTTP 要求傳送給應用程式。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-297">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="3f0c9-298">要求會觸發應用程式啟動。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-298">The request triggers the app to start.</span></span> <span data-ttu-id="3f0c9-299">根據預設，IIS 會發出應用程式根 URL (`/`) 的要求以初始化應用程式 (如需有關設定的詳細資訊，請參閱[額外資源](#application-initialization-module-and-idle-timeout-additional-resources))。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-299">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="3f0c9-300">確認已啟用「IIS 應用程式初始化」角色功能：</span><span class="sxs-lookup"><span data-stu-id="3f0c9-300">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="3f0c9-301">在 Windows 7 或更新的電腦系統上，當在本機使用 IIS 時：</span><span class="sxs-lookup"><span data-stu-id="3f0c9-301">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="3f0c9-302">流覽至**主控台**  >  **程式**的 [  >  **程式和功能]，**  >  **開啟或關閉**畫面) 左側 (的 [Windows 功能]。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-302">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="3f0c9-303">開啟**Internet Information Services**  >  **World Wide Web 服務**  >  **應用程式開發功能**。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-303">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="3f0c9-304">選取 [應用程式初始化]\*\*\*\* 的核取方塊。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-304">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="3f0c9-305">在 Windows Server 2008 R2 或更新版本上：</span><span class="sxs-lookup"><span data-stu-id="3f0c9-305">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="3f0c9-306">開啟「新增角色與功能精靈」\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-306">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="3f0c9-307">在 [選取角色服務]\*\*\*\* 面板中，開啟 [應用程式開發]\*\*\*\* 節點。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-307">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="3f0c9-308">選取 [應用程式初始化]\*\*\*\* 的核取方塊。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-308">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="3f0c9-309">使用下列任一方式為網站啟用應用程式初始化模組：</span><span class="sxs-lookup"><span data-stu-id="3f0c9-309">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="3f0c9-310">使用 IIS 管理員：</span><span class="sxs-lookup"><span data-stu-id="3f0c9-310">Using IIS Manager:</span></span>

  1. <span data-ttu-id="3f0c9-311">選取 [連線]\*\*\*\* 面板中的 [應用程式集區]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-311">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="3f0c9-312">以滑鼠右鍵按一下清單中應用程式的應用程式集區，然後選取 [進階設定]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-312">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="3f0c9-313">預設 **啟動模式** 為 `OnDemand` 。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-313">The default **Start Mode** is `OnDemand`.</span></span> <span data-ttu-id="3f0c9-314">將 [ **啟動模式]** 設定為 `AlwaysRunning` 。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-314">Set the **Start Mode** to `AlwaysRunning`.</span></span> <span data-ttu-id="3f0c9-315">選取 [確定]  。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-315">Select **OK**.</span></span>
  1. <span data-ttu-id="3f0c9-316">開啟 [連線]\*\*\*\* 面板中的 [站台]\*\*\*\* 節點。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-316">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="3f0c9-317">以滑鼠右鍵按一下應用程式，然後選取 [**管理網站**  >  **Advanced Settings**]。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-317">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="3f0c9-318">預設 **預先載入已啟用** 的設定為 `False` 。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-318">The default **Preload Enabled** setting is `False`.</span></span> <span data-ttu-id="3f0c9-319">將 [ **預先載入已啟用** ] 設定為 `True` 。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-319">Set **Preload Enabled** to `True`.</span></span> <span data-ttu-id="3f0c9-320">選取 [確定]  。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-320">Select **OK**.</span></span>

* <span data-ttu-id="3f0c9-321">使用 `web.config` ，將設定為的專案加入 `<applicationInitialization>` `doAppInitAfterRestart` `true` 至 `<system.webServer>` 應用程式檔案中的元素 `web.config` ：</span><span class="sxs-lookup"><span data-stu-id="3f0c9-321">Using `web.config`, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's `web.config` file:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a><span data-ttu-id="3f0c9-322">閒置逾時</span><span class="sxs-lookup"><span data-stu-id="3f0c9-322">Idle Timeout</span></span>

<span data-ttu-id="3f0c9-323">*僅適用於應用程式裝載同處理序。*</span><span class="sxs-lookup"><span data-stu-id="3f0c9-323">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="3f0c9-324">若要防止應用程式閒置，請使用 IIS 管理員設定應用程式集區的閒置逾時：</span><span class="sxs-lookup"><span data-stu-id="3f0c9-324">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="3f0c9-325">選取 [連線]\*\*\*\* 面板中的 [應用程式集區]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-325">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="3f0c9-326">以滑鼠右鍵按一下清單中應用程式的應用程式集區，然後選取 [進階設定]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-326">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="3f0c9-327">預設 \*\*閒置時間 (分鐘) \*\* 為 `20` 分鐘。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-327">The default **Idle Time-out (minutes)** is `20` minutes.</span></span> <span data-ttu-id="3f0c9-328">將 \*\*閒置超時 (分鐘) \*\* 設定為 `0` (零) 。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-328">Set the **Idle Time-out (minutes)** to `0` (zero).</span></span> <span data-ttu-id="3f0c9-329">選取 [確定]  。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-329">Select **OK**.</span></span>
1. <span data-ttu-id="3f0c9-330">回收背景工作處理序。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-330">Recycle the worker process.</span></span>

<span data-ttu-id="3f0c9-331">若要防止應用程式裝載[非同處理序](xref:host-and-deploy/iis/out-of-process-hosting)逾時，請使用下列任一方式：</span><span class="sxs-lookup"><span data-stu-id="3f0c9-331">To prevent apps hosted [out-of-process](xref:host-and-deploy/iis/out-of-process-hosting) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="3f0c9-332">從外部服務對應用程式執行 Ping 以讓它繼續執行。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-332">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="3f0c9-333">若應用程式只裝載背景服務，避免 IIS 裝載並使用 [Windows 服務來裝載 ASP.NET Core 應用程式](xref:host-and-deploy/windows-service)。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-333">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="3f0c9-334">應用程式初始化模組與閒置逾時額外資源</span><span class="sxs-lookup"><span data-stu-id="3f0c9-334">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="3f0c9-335">IIS 8.0 應用程式初始化</span><span class="sxs-lookup"><span data-stu-id="3f0c9-335">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="3f0c9-336">[應用程式 `<applicationInitialization>` 初始化](/iis/configuration/system.webserver/applicationinitialization/)。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-336">[Application Initialization `<applicationInitialization>`](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="3f0c9-337">[應用程式集 `<processModel>` 區的進程模型設定](/iis/configuration/system.applicationhost/applicationpools/add/processmodel)。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-337">[Process Model Settings for an Application Pool `<processModel>`](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="3f0c9-338">模組、結構描述及設定檔位置</span><span class="sxs-lookup"><span data-stu-id="3f0c9-338">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="3f0c9-339">模組</span><span class="sxs-lookup"><span data-stu-id="3f0c9-339">Module</span></span>

<span data-ttu-id="3f0c9-340">\*\*IIS (x86/amd64) \*\*：</span><span class="sxs-lookup"><span data-stu-id="3f0c9-340">**IIS (x86/amd64)**:</span></span>

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

<span data-ttu-id="3f0c9-341">\*\*IIS Express (x86/amd64) \*\*：</span><span class="sxs-lookup"><span data-stu-id="3f0c9-341">**IIS Express (x86/amd64)**:</span></span>

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a><span data-ttu-id="3f0c9-342">結構描述</span><span class="sxs-lookup"><span data-stu-id="3f0c9-342">Schema</span></span>

<span data-ttu-id="3f0c9-343">**IIS**</span><span class="sxs-lookup"><span data-stu-id="3f0c9-343">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

<span data-ttu-id="3f0c9-344">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="3f0c9-344">**IIS Express**</span></span>

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a><span data-ttu-id="3f0c9-345">組態</span><span class="sxs-lookup"><span data-stu-id="3f0c9-345">Configuration</span></span>

<span data-ttu-id="3f0c9-346">**IIS**</span><span class="sxs-lookup"><span data-stu-id="3f0c9-346">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\applicationHost.config`

<span data-ttu-id="3f0c9-347">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="3f0c9-347">**IIS Express**</span></span>

* <span data-ttu-id="3f0c9-348">Visual Studio： `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span><span class="sxs-lookup"><span data-stu-id="3f0c9-348">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span></span>

* <span data-ttu-id="3f0c9-349">*iisexpress.exe* Cli： `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span><span class="sxs-lookup"><span data-stu-id="3f0c9-349">*iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span></span>

<span data-ttu-id="3f0c9-350">您可以藉由在檔案中搜尋來找到檔案 `aspnetcore` `applicationHost.config` 。</span><span class="sxs-lookup"><span data-stu-id="3f0c9-350">The files can be found by searching for `aspnetcore` in the `applicationHost.config` file.</span></span>
