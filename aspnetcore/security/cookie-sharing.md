---
title: :::no-loc(cookie):::在 ASP.NET apps 之間共用驗證
author: rick-anderson
description: '瞭解如何 :::no-loc(cookie)::: 在 ASP.NET 4.x 和 ASP.NET Core apps 之間共用驗證。'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/:::no-loc(cookie):::-sharing
ms.openlocfilehash: 8f54f2e4894328f8471d5f80c8184839ce47add6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059685"
---
# <a name="share-authentication-no-loccookies-among-aspnet-apps"></a><span data-ttu-id="d79a0-103">:::no-loc(cookie):::在 ASP.NET apps 之間共用驗證</span><span class="sxs-lookup"><span data-stu-id="d79a0-103">Share authentication :::no-loc(cookie):::s among ASP.NET apps</span></span>

<span data-ttu-id="d79a0-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d79a0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d79a0-105">網站通常包含個別的 web 應用程式一起運作。</span><span class="sxs-lookup"><span data-stu-id="d79a0-105">Websites often consist of individual web apps working together.</span></span> <span data-ttu-id="d79a0-106">若要提供單一登入 (SSO) 體驗，網站內的 web apps 必須共用驗證 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="d79a0-106">To provide a single sign-on (SSO) experience, web apps within a site must share authentication :::no-loc(cookie):::s.</span></span> <span data-ttu-id="d79a0-107">為了支援此案例，資料保護堆疊允許共用 Katana :::no-loc(cookie)::: authentication 和 ASP.NET Core :::no-loc(cookie)::: 驗證票證。</span><span class="sxs-lookup"><span data-stu-id="d79a0-107">To support this scenario, the data protection stack allows sharing Katana :::no-loc(cookie)::: authentication and ASP.NET Core :::no-loc(cookie)::: authentication tickets.</span></span>

<span data-ttu-id="d79a0-108">在下列範例中：</span><span class="sxs-lookup"><span data-stu-id="d79a0-108">In the examples that follow:</span></span>

* <span data-ttu-id="d79a0-109">驗證 :::no-loc(cookie)::: 名稱會設定為的通用值 `.AspNet.Shared:::no-loc(Cookie):::` 。</span><span class="sxs-lookup"><span data-stu-id="d79a0-109">The authentication :::no-loc(cookie)::: name is set to a common value of `.AspNet.Shared:::no-loc(Cookie):::`.</span></span>
* <span data-ttu-id="d79a0-110">`AuthenticationType`會 `:::no-loc(Identity):::.Application` 明確或依預設設定為。</span><span class="sxs-lookup"><span data-stu-id="d79a0-110">The `AuthenticationType` is set to `:::no-loc(Identity):::.Application` either explicitly or by default.</span></span>
* <span data-ttu-id="d79a0-111">一般的應用程式名稱可用來讓資料保護系統共用 () 的資料保護金鑰 `Shared:::no-loc(Cookie):::App` 。</span><span class="sxs-lookup"><span data-stu-id="d79a0-111">A common app name is used to enable the data protection system to share data protection keys (`Shared:::no-loc(Cookie):::App`).</span></span>
* <span data-ttu-id="d79a0-112">`:::no-loc(Identity):::.Application` 會用來做為驗證配置。</span><span class="sxs-lookup"><span data-stu-id="d79a0-112">`:::no-loc(Identity):::.Application` is used as the authentication scheme.</span></span> <span data-ttu-id="d79a0-113">無論使用哪種配置，都必須以一致的方式在共用應用程式 *內和* 共用的應用程式中使用， :::no-loc(cookie)::: 以做為預設配置或明確設定它。</span><span class="sxs-lookup"><span data-stu-id="d79a0-113">Whatever scheme is used, it must be used consistently *within and across* the shared :::no-loc(cookie)::: apps either as the default scheme or by explicitly setting it.</span></span> <span data-ttu-id="d79a0-114">配置是在加密和解密時使用 :::no-loc(cookie)::: ，因此必須跨應用程式使用一致的配置。</span><span class="sxs-lookup"><span data-stu-id="d79a0-114">The scheme is used when encrypting and decrypting :::no-loc(cookie):::s, so a consistent scheme must be used across apps.</span></span>
* <span data-ttu-id="d79a0-115">使用一般的 [資料保護金鑰](xref:security/data-protection/implementation/key-management) 儲存位置。</span><span class="sxs-lookup"><span data-stu-id="d79a0-115">A common [data protection key](xref:security/data-protection/implementation/key-management) storage location is used.</span></span>
  * <span data-ttu-id="d79a0-116">在 ASP.NET Core apps 中， <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> 是用來設定金鑰儲存位置。</span><span class="sxs-lookup"><span data-stu-id="d79a0-116">In ASP.NET Core apps, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> is used to set the key storage location.</span></span>
  * <span data-ttu-id="d79a0-117">在 .NET Framework 應用程式中， :::no-loc(Cookie)::: 驗證中介軟體會使用的實作為 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider> 。</span><span class="sxs-lookup"><span data-stu-id="d79a0-117">In .NET Framework apps, :::no-loc(Cookie)::: Authentication Middleware uses an implementation of <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>.</span></span> <span data-ttu-id="d79a0-118">`DataProtectionProvider` 提供用於加密和解密驗證承載資料的資料保護服務 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="d79a0-118">`DataProtectionProvider` provides data protection services for the encryption and decryption of authentication :::no-loc(cookie)::: payload data.</span></span> <span data-ttu-id="d79a0-119">此 `DataProtectionProvider` 實例會與應用程式其他元件所使用的資料保護系統隔離。</span><span class="sxs-lookup"><span data-stu-id="d79a0-119">The `DataProtectionProvider` instance is isolated from the data protection system used by other parts of the app.</span></span> <span data-ttu-id="d79a0-120">[DataProtectionProvider 建立 (DirectoryInfo，Action \<IDataProtectionBuilder>) 接受， ](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) <xref:System.IO.DirectoryInfo> 以指定資料保護金鑰儲存的位置。</span><span class="sxs-lookup"><span data-stu-id="d79a0-120">[DataProtectionProvider.Create(System.IO.DirectoryInfo, Action\<IDataProtectionBuilder>)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) accepts a <xref:System.IO.DirectoryInfo> to specify the location for data protection key storage.</span></span>
* <span data-ttu-id="d79a0-121">`DataProtectionProvider` 需要 [AspNetCore. DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet 套件：</span><span class="sxs-lookup"><span data-stu-id="d79a0-121">`DataProtectionProvider` requires the [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet package:</span></span>
  * <span data-ttu-id="d79a0-122">在 ASP.NET Core 2.x 應用程式中，參考 [AspNetCore 應用程式中繼套件](xref:fundamentals/metapackage-app)。</span><span class="sxs-lookup"><span data-stu-id="d79a0-122">In ASP.NET Core 2.x apps, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="d79a0-123">在 .NET Framework 應用程式中，將套件參考新增至 [AspNetCore. DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/)。</span><span class="sxs-lookup"><span data-stu-id="d79a0-123">In .NET Framework apps, add a package reference to [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span></span>
* <span data-ttu-id="d79a0-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> 設定一般應用程式名稱。</span><span class="sxs-lookup"><span data-stu-id="d79a0-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> sets the common app name.</span></span>

## <a name="share-authentication-no-loccookies-with-no-locaspnet-core-identity"></a><span data-ttu-id="d79a0-125">共用驗證 :::no-loc(cookie)::: s :::no-loc(ASP.NET Core Identity):::</span><span class="sxs-lookup"><span data-stu-id="d79a0-125">Share authentication :::no-loc(cookie):::s with :::no-loc(ASP.NET Core Identity):::</span></span>

<span data-ttu-id="d79a0-126">使用 :::no-loc(ASP.NET Core Identity)::: 時：</span><span class="sxs-lookup"><span data-stu-id="d79a0-126">When using :::no-loc(ASP.NET Core Identity)::::</span></span>

* <span data-ttu-id="d79a0-127">您必須在應用程式之間共用資料保護金鑰和應用程式名稱。</span><span class="sxs-lookup"><span data-stu-id="d79a0-127">Data protection keys and the app name must be shared among apps.</span></span> <span data-ttu-id="d79a0-128">在下列範例中，會提供常見的金鑰儲存位置給 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> 方法。</span><span class="sxs-lookup"><span data-stu-id="d79a0-128">A common key storage location is provided to the <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> method in the following examples.</span></span> <span data-ttu-id="d79a0-129">您 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> 可以使用來設定通用的共用應用程式名稱 (`Shared:::no-loc(Cookie):::App` 在下列範例中) 。</span><span class="sxs-lookup"><span data-stu-id="d79a0-129">Use <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> to configure a common shared app name (`Shared:::no-loc(Cookie):::App` in the following examples).</span></span> <span data-ttu-id="d79a0-130">如需詳細資訊，請參閱<xref:security/data-protection/configuration/overview>。</span><span class="sxs-lookup"><span data-stu-id="d79a0-130">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>
* <span data-ttu-id="d79a0-131">使用 <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.ConfigureApplication:::no-loc(Cookie):::*> 擴充方法來設定的資料保護服務 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="d79a0-131">Use the <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.ConfigureApplication:::no-loc(Cookie):::*> extension method to set up the data protection service for :::no-loc(cookie):::s.</span></span>
* <span data-ttu-id="d79a0-132">預設驗證類型為 `:::no-loc(Identity):::.Application` 。</span><span class="sxs-lookup"><span data-stu-id="d79a0-132">The default authentication type is `:::no-loc(Identity):::.Application`.</span></span>

<span data-ttu-id="d79a0-133">在 `Startup.ConfigureServices` 中：</span><span class="sxs-lookup"><span data-stu-id="d79a0-133">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("Shared:::no-loc(Cookie):::App");

services.ConfigureApplication:::no-loc(Cookie):::(options => {
    options.:::no-loc(Cookie):::.Name = ".AspNet.Shared:::no-loc(Cookie):::";
});
```

## <a name="share-authentication-no-loccookies-without-no-locaspnet-core-identity"></a><span data-ttu-id="d79a0-134">:::no-loc(cookie):::無需共用驗證:::no-loc(ASP.NET Core Identity):::</span><span class="sxs-lookup"><span data-stu-id="d79a0-134">Share authentication :::no-loc(cookie):::s without :::no-loc(ASP.NET Core Identity):::</span></span>

<span data-ttu-id="d79a0-135">直接使用時若 :::no-loc(cookie)::: 沒有 :::no-loc(ASP.NET Core Identity)::: ，請在中設定資料保護和驗證 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="d79a0-135">When using :::no-loc(cookie):::s directly without :::no-loc(ASP.NET Core Identity):::, configure data protection and authentication in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d79a0-136">在下列範例中，驗證類型設為 `:::no-loc(Identity):::.Application` ：</span><span class="sxs-lookup"><span data-stu-id="d79a0-136">In the following example, the authentication type is set to `:::no-loc(Identity):::.Application`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("Shared:::no-loc(Cookie):::App");

services.AddAuthentication(":::no-loc(Identity):::.Application")
    .Add:::no-loc(Cookie):::(":::no-loc(Identity):::.Application", options =>
    {
        options.:::no-loc(Cookie):::.Name = ".AspNet.Shared:::no-loc(Cookie):::";
    });
```

## <a name="share-no-loccookies-across-different-base-paths"></a><span data-ttu-id="d79a0-137">:::no-loc(cookie):::跨不同的基底路徑共用</span><span class="sxs-lookup"><span data-stu-id="d79a0-137">Share :::no-loc(cookie):::s across different base paths</span></span>

<span data-ttu-id="d79a0-138">驗證會 :::no-loc(cookie)::: 使用[HttpRequest. PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase)作為其預設值[ :::no-loc(Cookie)::: 。路徑](xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.Path)。</span><span class="sxs-lookup"><span data-stu-id="d79a0-138">An authentication :::no-loc(cookie)::: uses the [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) as its default [:::no-loc(Cookie):::.Path](xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.Path).</span></span> <span data-ttu-id="d79a0-139">如果 :::no-loc(cookie)::: 必須在不同的基底路徑之間共用應用程式，則 `Path` 必須覆寫：</span><span class="sxs-lookup"><span data-stu-id="d79a0-139">If the app's :::no-loc(cookie)::: must be shared across different base paths, `Path` must be overridden:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("Shared:::no-loc(Cookie):::App");

services.ConfigureApplication:::no-loc(Cookie):::(options => {
    options.:::no-loc(Cookie):::.Name = ".AspNet.Shared:::no-loc(Cookie):::";
    options.:::no-loc(Cookie):::.Path = "/";
});
```

## <a name="share-no-loccookies-across-subdomains"></a><span data-ttu-id="d79a0-140">:::no-loc(cookie):::跨子域共用</span><span class="sxs-lookup"><span data-stu-id="d79a0-140">Share :::no-loc(cookie):::s across subdomains</span></span>

<span data-ttu-id="d79a0-141">裝載跨子域共用的應用程式時 :::no-loc(cookie)::: ，請在中指定通用網域[ :::no-loc(Cookie)::: 。網域](xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.Domain)屬性。</span><span class="sxs-lookup"><span data-stu-id="d79a0-141">When hosting apps that share :::no-loc(cookie):::s across subdomains, specify a common domain in the [:::no-loc(Cookie):::.Domain](xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.Domain) property.</span></span> <span data-ttu-id="d79a0-142">若要 :::no-loc(cookie)::: 在的應用程式之間共用 `contoso.com` ，例如 `first_subdomain.contoso.com` 和 `second_subdomain.contoso.com` ，請將指定 `:::no-loc(Cookie):::.Domain` 為 `.contoso.com` ：</span><span class="sxs-lookup"><span data-stu-id="d79a0-142">To share :::no-loc(cookie):::s across apps at `contoso.com`, such as `first_subdomain.contoso.com` and `second_subdomain.contoso.com`, specify the `:::no-loc(Cookie):::.Domain` as `.contoso.com`:</span></span>

```csharp
options.:::no-loc(Cookie):::.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a><span data-ttu-id="d79a0-143">加密待用資料保護金鑰</span><span class="sxs-lookup"><span data-stu-id="d79a0-143">Encrypt data protection keys at rest</span></span>

<span data-ttu-id="d79a0-144">對於生產環境部署，請將設定 `DataProtectionProvider` 為使用 DPAPI 或 X509Certificate 來加密待用的金鑰。</span><span class="sxs-lookup"><span data-stu-id="d79a0-144">For production deployments, configure the `DataProtectionProvider` to encrypt keys at rest with DPAPI or an X509Certificate.</span></span> <span data-ttu-id="d79a0-145">如需詳細資訊，請參閱<xref:security/data-protection/implementation/key-encryption-at-rest>。</span><span class="sxs-lookup"><span data-stu-id="d79a0-145">For more information, see <xref:security/data-protection/implementation/key-encryption-at-rest>.</span></span> <span data-ttu-id="d79a0-146">在下列範例中，會提供憑證指紋給 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*> ：</span><span class="sxs-lookup"><span data-stu-id="d79a0-146">In the following example, a certificate thumbprint is provided to <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:</span></span>

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-no-loccookies-between-aspnet-4x-and-aspnet-core-apps"></a><span data-ttu-id="d79a0-147">:::no-loc(cookie):::在 ASP.NET 4.x 和 ASP.NET Core apps 之間共用驗證</span><span class="sxs-lookup"><span data-stu-id="d79a0-147">Share authentication :::no-loc(cookie):::s between ASP.NET 4.x and ASP.NET Core apps</span></span>

<span data-ttu-id="d79a0-148">您可以設定使用 Katana Authentication 中介軟體的 ASP.NET 4.x 應用程式， :::no-loc(Cookie)::: 以產生 :::no-loc(cookie)::: 與 ASP.NET Core :::no-loc(Cookie)::: authentication 中介軟體相容的驗證。</span><span class="sxs-lookup"><span data-stu-id="d79a0-148">ASP.NET 4.x apps that use Katana :::no-loc(Cookie)::: Authentication Middleware can be configured to generate authentication :::no-loc(cookie):::s that are compatible with the ASP.NET Core :::no-loc(Cookie)::: Authentication Middleware.</span></span> <span data-ttu-id="d79a0-149">這可讓您在數個步驟中升級大型網站的個別應用程式，同時提供整個網站的順暢 SSO 體驗。</span><span class="sxs-lookup"><span data-stu-id="d79a0-149">This allows upgrading a large site's individual apps in several steps while providing a smooth SSO experience across the site.</span></span>

<span data-ttu-id="d79a0-150">當應用程式使用 Katana :::no-loc(Cookie)::: Authentication 中介軟體時，它會呼叫 `Use:::no-loc(Cookie):::Authentication` 專案的 *Startup.Auth.cs* 檔。</span><span class="sxs-lookup"><span data-stu-id="d79a0-150">When an app uses Katana :::no-loc(Cookie)::: Authentication Middleware, it calls `Use:::no-loc(Cookie):::Authentication` in the project's *Startup.Auth.cs* file.</span></span> <span data-ttu-id="d79a0-151">使用 Visual Studio 2013 和更新版本建立的 ASP.NET 4.x web 應用程式專案預設會使用 Katana :::no-loc(Cookie)::: Authentication 中介軟體。</span><span class="sxs-lookup"><span data-stu-id="d79a0-151">ASP.NET 4.x web app projects created with Visual Studio 2013 and later use the Katana :::no-loc(Cookie)::: Authentication Middleware by default.</span></span> <span data-ttu-id="d79a0-152">雖然 `Use:::no-loc(Cookie):::Authentication` 已淘汰且不支援 ASP.NET Core 的應用程式，但 `Use:::no-loc(Cookie):::Authentication` 在使用 Katana Authentication 中介軟體的 ASP.NET 4.x 應用程式中呼叫 :::no-loc(Cookie)::: 是有效的。</span><span class="sxs-lookup"><span data-stu-id="d79a0-152">Although `Use:::no-loc(Cookie):::Authentication` is obsolete and unsupported for ASP.NET Core apps, calling `Use:::no-loc(Cookie):::Authentication` in an ASP.NET 4.x app that uses Katana :::no-loc(Cookie)::: Authentication Middleware is valid.</span></span>

<span data-ttu-id="d79a0-153">ASP.NET 4.x 應用程式必須以 .NET Framework 4.5.1 或更新版本為目標。</span><span class="sxs-lookup"><span data-stu-id="d79a0-153">An ASP.NET 4.x app must target .NET Framework 4.5.1 or later.</span></span> <span data-ttu-id="d79a0-154">否則，將無法安裝所需的 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="d79a0-154">Otherwise, the necessary NuGet packages fail to install.</span></span>

<span data-ttu-id="d79a0-155">若要 :::no-loc(cookie)::: 在 ASP.NET 4.x 應用程式和 ASP.NET Core 應用程式之間共用驗證，請依照 [ :::no-loc(cookie)::: ASP.NET Core Apps 之間的共用驗證](#share-authentication-:::no-loc(cookie):::s-with-aspnet-core-identity) 一節中所述，設定 ASP.NET Core 應用程式，然後設定 ASP.NET 4.x 應用程式，如下所示。</span><span class="sxs-lookup"><span data-stu-id="d79a0-155">To share authentication :::no-loc(cookie):::s between an ASP.NET 4.x app and an ASP.NET Core app, configure the ASP.NET Core app as stated in the [Share authentication :::no-loc(cookie):::s among ASP.NET Core apps](#share-authentication-:::no-loc(cookie):::s-with-aspnet-core-identity) section, then configure the ASP.NET 4.x app as follows.</span></span>

<span data-ttu-id="d79a0-156">確認應用程式的套件已更新至最新版本。</span><span class="sxs-lookup"><span data-stu-id="d79a0-156">Confirm that the app's packages are updated to the latest releases.</span></span> <span data-ttu-id="d79a0-157">將 [Owin](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) 套件安裝到每個 ASP.NET 4.x 應用程式。</span><span class="sxs-lookup"><span data-stu-id="d79a0-157">Install the [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) package into each ASP.NET 4.x app.</span></span>

<span data-ttu-id="d79a0-158">找出並修改呼叫 `Use:::no-loc(Cookie):::Authentication` ：</span><span class="sxs-lookup"><span data-stu-id="d79a0-158">Locate and modify the call to `Use:::no-loc(Cookie):::Authentication`:</span></span>

* <span data-ttu-id="d79a0-159">變更 :::no-loc(cookie)::: 名稱，使其符合 :::no-loc(Cookie)::: 範例) 中 ASP.NET Core Authentication 中介軟體 (所使用的名稱 `.AspNet.Shared:::no-loc(Cookie):::` 。</span><span class="sxs-lookup"><span data-stu-id="d79a0-159">Change the :::no-loc(cookie)::: name to match the name used by the ASP.NET Core :::no-loc(Cookie)::: Authentication Middleware (`.AspNet.Shared:::no-loc(Cookie):::` in the example).</span></span>
* <span data-ttu-id="d79a0-160">在下列範例中，驗證類型設為 `:::no-loc(Identity):::.Application` 。</span><span class="sxs-lookup"><span data-stu-id="d79a0-160">In the following example, the authentication type is set to `:::no-loc(Identity):::.Application`.</span></span>
* <span data-ttu-id="d79a0-161">提供 `DataProtectionProvider` 初始化為一般資料保護金鑰儲存位置的實例。</span><span class="sxs-lookup"><span data-stu-id="d79a0-161">Provide an instance of a `DataProtectionProvider` initialized to the common data protection key storage location.</span></span>
* <span data-ttu-id="d79a0-162">確認應用程式名稱已設定為在 :::no-loc(cookie)::: 範例)  (共用驗證的所有應用程式所使用的通用應用程式名稱 `Shared:::no-loc(Cookie):::App` 。</span><span class="sxs-lookup"><span data-stu-id="d79a0-162">Confirm that the app name is set to the common app name used by all apps that share authentication :::no-loc(cookie):::s (`Shared:::no-loc(Cookie):::App` in the example).</span></span>

<span data-ttu-id="d79a0-163">如果未設定 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` 和 `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider` ，請將設定 <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> 為可區別唯一使用者的宣告。</span><span class="sxs-lookup"><span data-stu-id="d79a0-163">If not setting `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` and `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`, set <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> to a claim that distinguishes unique users.</span></span>

<span data-ttu-id="d79a0-164">*App_Start/startup.auth.cs* ：</span><span class="sxs-lookup"><span data-stu-id="d79a0-164">*App_Start/Startup.Auth.cs* :</span></span>

```csharp
app.Use:::no-loc(Cookie):::Authentication(new :::no-loc(Cookie):::AuthenticationOptions
{
    AuthenticationType = ":::no-loc(Identity):::.Application",
    :::no-loc(Cookie):::Name = ".AspNet.Shared:::no-loc(Cookie):::",
    LoginPath = new PathString("/Account/Login"),
    Provider = new :::no-loc(Cookie):::AuthenticationProvider
    {
        OnValidate:::no-loc(Identity)::: =
            SecurityStampValidator
                .OnValidate:::no-loc(Identity):::<ApplicationUserManager, ApplicationUser>(
                    validateInterval: TimeSpan.FromMinutes(30),
                    regenerate:::no-loc(Identity):::: (manager, user) =>
                        user.GenerateUser:::no-loc(Identity):::Async(manager))
    },
    TicketDataFormat = new AspNetTicketDataFormat(
        new DataProtectorShim(
            DataProtectionProvider.Create("{PATH TO COMMON KEY RING FOLDER}",
                (builder) => { builder.SetApplicationName("Shared:::no-loc(Cookie):::App"); })
            .CreateProtector(
                "Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s." +
                    ":::no-loc(Cookie):::AuthenticationMiddleware",
                ":::no-loc(Identity):::.Application",
                "v2"))),
    :::no-loc(Cookie):::Manager = new Chunking:::no-loc(Cookie):::Manager()
});

System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier =
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name";
```

<span data-ttu-id="d79a0-165">當產生使用者識別時，驗證類型 (`:::no-loc(Identity):::.Application`) 必須符合 `AuthenticationType` `Use:::no-loc(Cookie):::Authentication` *App_Start/startup.auth.cs* 中的 set with 所定義的類型。</span><span class="sxs-lookup"><span data-stu-id="d79a0-165">When generating a user identity, the authentication type (`:::no-loc(Identity):::.Application`) must match the type defined in `AuthenticationType` set with `Use:::no-loc(Cookie):::Authentication` in *App_Start/Startup.Auth.cs* .</span></span>

<span data-ttu-id="d79a0-166">*模型/ :::no-loc(Identity):::Models.cs* ：</span><span class="sxs-lookup"><span data-stu-id="d79a0-166">*Models/:::no-loc(Identity):::Models.cs* :</span></span>

```csharp
public class ApplicationUser : :::no-loc(Identity):::User
{
    public async Task<Claims:::no-loc(Identity):::> GenerateUser:::no-loc(Identity):::Async(
        UserManager<ApplicationUser> manager)
    {
        // The authenticationType must match the one defined in 
        // :::no-loc(Cookie):::AuthenticationOptions.AuthenticationType
        var user:::no-loc(Identity)::: = 
            await manager.Create:::no-loc(Identity):::Async(this, ":::no-loc(Identity):::.Application");

        // Add custom user claims here

        return user:::no-loc(Identity):::;
    }
}
```

## <a name="use-a-common-user-database"></a><span data-ttu-id="d79a0-167">使用一般使用者資料庫</span><span class="sxs-lookup"><span data-stu-id="d79a0-167">Use a common user database</span></span>

<span data-ttu-id="d79a0-168">當應用程式使用相同 :::no-loc(Identity)::: 的) 版本 (相同的架構時 :::no-loc(Identity)::: ，請確認 :::no-loc(Identity)::: 每個應用程式的系統都指向相同的使用者資料庫。</span><span class="sxs-lookup"><span data-stu-id="d79a0-168">When apps use the same :::no-loc(Identity)::: schema (same version of :::no-loc(Identity):::), confirm that the :::no-loc(Identity)::: system for each app is pointed at the same user database.</span></span> <span data-ttu-id="d79a0-169">否則，當身分識別系統嘗試比對驗證中的資訊 :::no-loc(cookie)::: 與其資料庫中的資訊時，就會在執行時間產生失敗。</span><span class="sxs-lookup"><span data-stu-id="d79a0-169">Otherwise, the identity system produces failures at runtime when it attempts to match the information in the authentication :::no-loc(cookie)::: against the information in its database.</span></span>

<span data-ttu-id="d79a0-170">當 :::no-loc(Identity)::: 架構不同的應用程式時，通常是因為應用程式使用不同的 :::no-loc(Identity)::: 版本，在 :::no-loc(Identity)::: 不需要重新對應和新增其他應用程式架構中的資料行的情況下，不可能共用以最新版本為基礎的通用資料庫 :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="d79a0-170">When the :::no-loc(Identity)::: schema is different among apps, usually because apps are using different :::no-loc(Identity)::: versions, sharing a common database based on the latest version of :::no-loc(Identity)::: isn't possible without remapping and adding columns in other app's :::no-loc(Identity)::: schemas.</span></span> <span data-ttu-id="d79a0-171">將其他應用程式升級為使用最新版本通常會更有效率， :::no-loc(Identity)::: 讓應用程式可以共用一般資料庫。</span><span class="sxs-lookup"><span data-stu-id="d79a0-171">It's often more efficient to upgrade the other apps to use the latest :::no-loc(Identity)::: version so that a common database can be shared by the apps.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d79a0-172">其他資源</span><span class="sxs-lookup"><span data-stu-id="d79a0-172">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
