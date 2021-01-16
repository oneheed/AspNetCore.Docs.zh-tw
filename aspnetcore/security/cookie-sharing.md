---
title: cookie在 ASP.NET apps 之間共用驗證
author: rick-anderson
description: 瞭解如何 cookie 在 ASP.NET 4.x 和 ASP.NET Core apps 之間共用驗證。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
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
uid: security/cookie-sharing
ms.openlocfilehash: 0d43bbbc44015aff040b12dfacb260fe50492e54
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252990"
---
# <a name="share-authentication-no-loccookies-among-aspnet-apps"></a><span data-ttu-id="aaab2-103">cookie在 ASP.NET apps 之間共用驗證</span><span class="sxs-lookup"><span data-stu-id="aaab2-103">Share authentication cookies among ASP.NET apps</span></span>

<span data-ttu-id="aaab2-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="aaab2-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="aaab2-105">網站通常包含個別的 web 應用程式一起運作。</span><span class="sxs-lookup"><span data-stu-id="aaab2-105">Websites often consist of individual web apps working together.</span></span> <span data-ttu-id="aaab2-106">若要提供單一登入 (SSO) 體驗，網站內的 web apps 必須共用驗證 cookie 。</span><span class="sxs-lookup"><span data-stu-id="aaab2-106">To provide a single sign-on (SSO) experience, web apps within a site must share authentication cookies.</span></span> <span data-ttu-id="aaab2-107">為了支援此案例，資料保護堆疊允許共用 Katana cookie authentication 和 ASP.NET Core cookie 驗證票證。</span><span class="sxs-lookup"><span data-stu-id="aaab2-107">To support this scenario, the data protection stack allows sharing Katana cookie authentication and ASP.NET Core cookie authentication tickets.</span></span>

<span data-ttu-id="aaab2-108">在下列範例中：</span><span class="sxs-lookup"><span data-stu-id="aaab2-108">In the examples that follow:</span></span>

* <span data-ttu-id="aaab2-109">驗證 cookie 名稱會設定為的通用值 `.AspNet.SharedCookie` 。</span><span class="sxs-lookup"><span data-stu-id="aaab2-109">The authentication cookie name is set to a common value of `.AspNet.SharedCookie`.</span></span>
* <span data-ttu-id="aaab2-110">`AuthenticationType`會 `Identity.Application` 明確或依預設設定為。</span><span class="sxs-lookup"><span data-stu-id="aaab2-110">The `AuthenticationType` is set to `Identity.Application` either explicitly or by default.</span></span>
* <span data-ttu-id="aaab2-111">一般的應用程式名稱可用來讓資料保護系統共用 () 的資料保護金鑰 `SharedCookieApp` 。</span><span class="sxs-lookup"><span data-stu-id="aaab2-111">A common app name is used to enable the data protection system to share data protection keys (`SharedCookieApp`).</span></span>
* <span data-ttu-id="aaab2-112">`Identity.Application` 會用來做為驗證配置。</span><span class="sxs-lookup"><span data-stu-id="aaab2-112">`Identity.Application` is used as the authentication scheme.</span></span> <span data-ttu-id="aaab2-113">無論使用哪種配置，都必須以一致的方式在共用應用程式 *內和* 共用的應用程式中使用， cookie 以做為預設配置或明確設定它。</span><span class="sxs-lookup"><span data-stu-id="aaab2-113">Whatever scheme is used, it must be used consistently *within and across* the shared cookie apps either as the default scheme or by explicitly setting it.</span></span> <span data-ttu-id="aaab2-114">配置是在加密和解密時使用 cookie ，因此必須跨應用程式使用一致的配置。</span><span class="sxs-lookup"><span data-stu-id="aaab2-114">The scheme is used when encrypting and decrypting cookies, so a consistent scheme must be used across apps.</span></span>
* <span data-ttu-id="aaab2-115">使用一般的 [資料保護金鑰](xref:security/data-protection/implementation/key-management) 儲存位置。</span><span class="sxs-lookup"><span data-stu-id="aaab2-115">A common [data protection key](xref:security/data-protection/implementation/key-management) storage location is used.</span></span>
  * <span data-ttu-id="aaab2-116">在 ASP.NET Core apps 中， <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> 是用來設定金鑰儲存位置。</span><span class="sxs-lookup"><span data-stu-id="aaab2-116">In ASP.NET Core apps, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> is used to set the key storage location.</span></span>
  * <span data-ttu-id="aaab2-117">在 .NET Framework 應用程式中， Cookie 驗證中介軟體會使用的實作為 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider> 。</span><span class="sxs-lookup"><span data-stu-id="aaab2-117">In .NET Framework apps, Cookie Authentication Middleware uses an implementation of <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>.</span></span> <span data-ttu-id="aaab2-118">`DataProtectionProvider` 提供用於加密和解密驗證承載資料的資料保護服務 cookie 。</span><span class="sxs-lookup"><span data-stu-id="aaab2-118">`DataProtectionProvider` provides data protection services for the encryption and decryption of authentication cookie payload data.</span></span> <span data-ttu-id="aaab2-119">此 `DataProtectionProvider` 實例會與應用程式其他元件所使用的資料保護系統隔離。</span><span class="sxs-lookup"><span data-stu-id="aaab2-119">The `DataProtectionProvider` instance is isolated from the data protection system used by other parts of the app.</span></span> <span data-ttu-id="aaab2-120">[DataProtectionProvider 建立 (DirectoryInfo，Action \<IDataProtectionBuilder>) 接受， ](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) <xref:System.IO.DirectoryInfo> 以指定資料保護金鑰儲存的位置。</span><span class="sxs-lookup"><span data-stu-id="aaab2-120">[DataProtectionProvider.Create(System.IO.DirectoryInfo, Action\<IDataProtectionBuilder>)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) accepts a <xref:System.IO.DirectoryInfo> to specify the location for data protection key storage.</span></span>
* <span data-ttu-id="aaab2-121">`DataProtectionProvider` 需要 [AspNetCore. DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet 套件：</span><span class="sxs-lookup"><span data-stu-id="aaab2-121">`DataProtectionProvider` requires the [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet package:</span></span>
  * <span data-ttu-id="aaab2-122">在 ASP.NET Core 2.x 應用程式中，參考 [AspNetCore 應用程式中繼套件](xref:fundamentals/metapackage-app)。</span><span class="sxs-lookup"><span data-stu-id="aaab2-122">In ASP.NET Core 2.x apps, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="aaab2-123">在 .NET Framework 應用程式中，將套件參考新增至 [AspNetCore. DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/)。</span><span class="sxs-lookup"><span data-stu-id="aaab2-123">In .NET Framework apps, add a package reference to [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span></span>
* <span data-ttu-id="aaab2-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> 設定一般應用程式名稱。</span><span class="sxs-lookup"><span data-stu-id="aaab2-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> sets the common app name.</span></span>

## <a name="share-authentication-no-loccookies-with-no-locaspnet-core-identity"></a><span data-ttu-id="aaab2-125">共用驗證 cookie s ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="aaab2-125">Share authentication cookies with ASP.NET Core Identity</span></span>

<span data-ttu-id="aaab2-126">使用 ASP.NET Core Identity 時：</span><span class="sxs-lookup"><span data-stu-id="aaab2-126">When using ASP.NET Core Identity:</span></span>

* <span data-ttu-id="aaab2-127">您必須在應用程式之間共用資料保護金鑰和應用程式名稱。</span><span class="sxs-lookup"><span data-stu-id="aaab2-127">Data protection keys and the app name must be shared among apps.</span></span> <span data-ttu-id="aaab2-128">在下列範例中，會提供常見的金鑰儲存位置給 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> 方法。</span><span class="sxs-lookup"><span data-stu-id="aaab2-128">A common key storage location is provided to the <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> method in the following examples.</span></span> <span data-ttu-id="aaab2-129">您 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> 可以使用來設定通用的共用應用程式名稱 (`SharedCookieApp` 在下列範例中) 。</span><span class="sxs-lookup"><span data-stu-id="aaab2-129">Use <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> to configure a common shared app name (`SharedCookieApp` in the following examples).</span></span> <span data-ttu-id="aaab2-130">如需詳細資訊，請參閱<xref:security/data-protection/configuration/overview>。</span><span class="sxs-lookup"><span data-stu-id="aaab2-130">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>
* <span data-ttu-id="aaab2-131">使用 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> 擴充方法來設定的資料保護服務 cookie 。</span><span class="sxs-lookup"><span data-stu-id="aaab2-131">Use the <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> extension method to set up the data protection service for cookies.</span></span>
* <span data-ttu-id="aaab2-132">預設驗證類型為 `Identity.Application` 。</span><span class="sxs-lookup"><span data-stu-id="aaab2-132">The default authentication type is `Identity.Application`.</span></span>

<span data-ttu-id="aaab2-133">在 `Startup.ConfigureServices` 中：</span><span class="sxs-lookup"><span data-stu-id="aaab2-133">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

<span data-ttu-id="aaab2-134">**注意：** 上述指示不適用於 `ITicketStore` (`CookieAuthenticationOptions.SessionStore`) 。</span><span class="sxs-lookup"><span data-stu-id="aaab2-134">**Note:** The preceding instructions don't work with `ITicketStore` (`CookieAuthenticationOptions.SessionStore`).</span></span>  <span data-ttu-id="aaab2-135">如需詳細資訊，請參閱 [此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/21163)。</span><span class="sxs-lookup"><span data-stu-id="aaab2-135">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/21163).</span></span>

## <a name="share-authentication-no-loccookies-without-no-locaspnet-core-identity"></a><span data-ttu-id="aaab2-136">cookie無需共用驗證ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="aaab2-136">Share authentication cookies without ASP.NET Core Identity</span></span>

<span data-ttu-id="aaab2-137">直接使用時若 cookie 沒有 ASP.NET Core Identity ，請在中設定資料保護和驗證 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="aaab2-137">When using cookies directly without ASP.NET Core Identity, configure data protection and authentication in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="aaab2-138">在下列範例中，驗證類型設為 `Identity.Application` ：</span><span class="sxs-lookup"><span data-stu-id="aaab2-138">In the following example, the authentication type is set to `Identity.Application`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.AddAuthentication("Identity.Application")
    .AddCookie("Identity.Application", options =>
    {
        options.Cookie.Name = ".AspNet.SharedCookie";
    });
```

## <a name="share-no-loccookies-across-different-base-paths"></a><span data-ttu-id="aaab2-139">cookie跨不同的基底路徑共用</span><span class="sxs-lookup"><span data-stu-id="aaab2-139">Share cookies across different base paths</span></span>

<span data-ttu-id="aaab2-140">驗證會 cookie 使用[HttpRequest. PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase)作為其預設值[ Cookie 。路徑](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path)。</span><span class="sxs-lookup"><span data-stu-id="aaab2-140">An authentication cookie uses the [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) as its default [Cookie.Path](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path).</span></span> <span data-ttu-id="aaab2-141">如果 cookie 必須在不同的基底路徑之間共用應用程式，則 `Path` 必須覆寫：</span><span class="sxs-lookup"><span data-stu-id="aaab2-141">If the app's cookie must be shared across different base paths, `Path` must be overridden:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
    options.Cookie.Path = "/";
});
```

## <a name="share-no-loccookies-across-subdomains"></a><span data-ttu-id="aaab2-142">cookie跨子域共用</span><span class="sxs-lookup"><span data-stu-id="aaab2-142">Share cookies across subdomains</span></span>

<span data-ttu-id="aaab2-143">裝載跨子域共用的應用程式時 cookie ，請在中指定通用網域[ Cookie 。網域](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain)屬性。</span><span class="sxs-lookup"><span data-stu-id="aaab2-143">When hosting apps that share cookies across subdomains, specify a common domain in the [Cookie.Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) property.</span></span> <span data-ttu-id="aaab2-144">若要 cookie 在的應用程式之間共用 `contoso.com` ，例如 `first_subdomain.contoso.com` 和 `second_subdomain.contoso.com` ，請將指定 `Cookie.Domain` 為 `.contoso.com` ：</span><span class="sxs-lookup"><span data-stu-id="aaab2-144">To share cookies across apps at `contoso.com`, such as `first_subdomain.contoso.com` and `second_subdomain.contoso.com`, specify the `Cookie.Domain` as `.contoso.com`:</span></span>

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a><span data-ttu-id="aaab2-145">加密待用資料保護金鑰</span><span class="sxs-lookup"><span data-stu-id="aaab2-145">Encrypt data protection keys at rest</span></span>

<span data-ttu-id="aaab2-146">對於生產環境部署，請將設定 `DataProtectionProvider` 為使用 DPAPI 或 X509Certificate 來加密待用的金鑰。</span><span class="sxs-lookup"><span data-stu-id="aaab2-146">For production deployments, configure the `DataProtectionProvider` to encrypt keys at rest with DPAPI or an X509Certificate.</span></span> <span data-ttu-id="aaab2-147">如需詳細資訊，請參閱<xref:security/data-protection/implementation/key-encryption-at-rest>。</span><span class="sxs-lookup"><span data-stu-id="aaab2-147">For more information, see <xref:security/data-protection/implementation/key-encryption-at-rest>.</span></span> <span data-ttu-id="aaab2-148">在下列範例中，會提供憑證指紋給 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*> ：</span><span class="sxs-lookup"><span data-stu-id="aaab2-148">In the following example, a certificate thumbprint is provided to <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:</span></span>

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-no-loccookies-between-aspnet-4x-and-aspnet-core-apps"></a><span data-ttu-id="aaab2-149">cookie在 ASP.NET 4.x 和 ASP.NET Core apps 之間共用驗證</span><span class="sxs-lookup"><span data-stu-id="aaab2-149">Share authentication cookies between ASP.NET 4.x and ASP.NET Core apps</span></span>

<span data-ttu-id="aaab2-150">您可以設定使用 Katana Authentication 中介軟體的 ASP.NET 4.x 應用程式， Cookie 以產生 cookie 與 ASP.NET Core Cookie authentication 中介軟體相容的驗證。</span><span class="sxs-lookup"><span data-stu-id="aaab2-150">ASP.NET 4.x apps that use Katana Cookie Authentication Middleware can be configured to generate authentication cookies that are compatible with the ASP.NET Core Cookie Authentication Middleware.</span></span> <span data-ttu-id="aaab2-151">這可讓您在數個步驟中升級大型網站的個別應用程式，同時提供整個網站的順暢 SSO 體驗。</span><span class="sxs-lookup"><span data-stu-id="aaab2-151">This allows upgrading a large site's individual apps in several steps while providing a smooth SSO experience across the site.</span></span>

<span data-ttu-id="aaab2-152">當應用程式使用 Katana Cookie Authentication 中介軟體時，它會呼叫 `UseCookieAuthentication` 專案的 *Startup.Auth.cs* 檔。</span><span class="sxs-lookup"><span data-stu-id="aaab2-152">When an app uses Katana Cookie Authentication Middleware, it calls `UseCookieAuthentication` in the project's *Startup.Auth.cs* file.</span></span> <span data-ttu-id="aaab2-153">使用 Visual Studio 2013 和更新版本建立的 ASP.NET 4.x web 應用程式專案預設會使用 Katana Cookie Authentication 中介軟體。</span><span class="sxs-lookup"><span data-stu-id="aaab2-153">ASP.NET 4.x web app projects created with Visual Studio 2013 and later use the Katana Cookie Authentication Middleware by default.</span></span> <span data-ttu-id="aaab2-154">雖然 `UseCookieAuthentication` 已淘汰且不支援 ASP.NET Core 的應用程式，但 `UseCookieAuthentication` 在使用 Katana Authentication 中介軟體的 ASP.NET 4.x 應用程式中呼叫 Cookie 是有效的。</span><span class="sxs-lookup"><span data-stu-id="aaab2-154">Although `UseCookieAuthentication` is obsolete and unsupported for ASP.NET Core apps, calling `UseCookieAuthentication` in an ASP.NET 4.x app that uses Katana Cookie Authentication Middleware is valid.</span></span>

<span data-ttu-id="aaab2-155">ASP.NET 4.x 應用程式必須以 .NET Framework 4.5.1 或更新版本為目標。</span><span class="sxs-lookup"><span data-stu-id="aaab2-155">An ASP.NET 4.x app must target .NET Framework 4.5.1 or later.</span></span> <span data-ttu-id="aaab2-156">否則，將無法安裝所需的 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="aaab2-156">Otherwise, the necessary NuGet packages fail to install.</span></span>

<span data-ttu-id="aaab2-157">若要 cookie 在 ASP.NET 4.x 應用程式和 ASP.NET Core 應用程式之間共用驗證，請依照 [ cookie ASP.NET Core Apps 之間的共用驗證](#share-authentication-cookies-with-aspnet-core-identity) 一節中所述，設定 ASP.NET Core 應用程式，然後設定 ASP.NET 4.x 應用程式，如下所示。</span><span class="sxs-lookup"><span data-stu-id="aaab2-157">To share authentication cookies between an ASP.NET 4.x app and an ASP.NET Core app, configure the ASP.NET Core app as stated in the [Share authentication cookies among ASP.NET Core apps](#share-authentication-cookies-with-aspnet-core-identity) section, then configure the ASP.NET 4.x app as follows.</span></span>

<span data-ttu-id="aaab2-158">確認應用程式的套件已更新至最新版本。</span><span class="sxs-lookup"><span data-stu-id="aaab2-158">Confirm that the app's packages are updated to the latest releases.</span></span> <span data-ttu-id="aaab2-159">將 [Owin](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) 套件安裝到每個 ASP.NET 4.x 應用程式。</span><span class="sxs-lookup"><span data-stu-id="aaab2-159">Install the [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) package into each ASP.NET 4.x app.</span></span>

<span data-ttu-id="aaab2-160">找出並修改呼叫 `UseCookieAuthentication` ：</span><span class="sxs-lookup"><span data-stu-id="aaab2-160">Locate and modify the call to `UseCookieAuthentication`:</span></span>

* <span data-ttu-id="aaab2-161">變更 cookie 名稱，使其符合 Cookie 範例) 中 ASP.NET Core Authentication 中介軟體 (所使用的名稱 `.AspNet.SharedCookie` 。</span><span class="sxs-lookup"><span data-stu-id="aaab2-161">Change the cookie name to match the name used by the ASP.NET Core Cookie Authentication Middleware (`.AspNet.SharedCookie` in the example).</span></span>
* <span data-ttu-id="aaab2-162">在下列範例中，驗證類型設為 `Identity.Application` 。</span><span class="sxs-lookup"><span data-stu-id="aaab2-162">In the following example, the authentication type is set to `Identity.Application`.</span></span>
* <span data-ttu-id="aaab2-163">提供 `DataProtectionProvider` 初始化為一般資料保護金鑰儲存位置的實例。</span><span class="sxs-lookup"><span data-stu-id="aaab2-163">Provide an instance of a `DataProtectionProvider` initialized to the common data protection key storage location.</span></span>
* <span data-ttu-id="aaab2-164">確認應用程式名稱已設定為在 cookie 範例)  (共用驗證的所有應用程式所使用的通用應用程式名稱 `SharedCookieApp` 。</span><span class="sxs-lookup"><span data-stu-id="aaab2-164">Confirm that the app name is set to the common app name used by all apps that share authentication cookies (`SharedCookieApp` in the example).</span></span>

<span data-ttu-id="aaab2-165">如果未設定 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` 和 `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider` ，請將設定 <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> 為可區別唯一使用者的宣告。</span><span class="sxs-lookup"><span data-stu-id="aaab2-165">If not setting `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` and `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`, set <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> to a claim that distinguishes unique users.</span></span>

<span data-ttu-id="aaab2-166">*App_Start/startup.auth.cs*：</span><span class="sxs-lookup"><span data-stu-id="aaab2-166">*App_Start/Startup.Auth.cs*:</span></span>

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    AuthenticationType = "Identity.Application",
    CookieName = ".AspNet.SharedCookie",
    LoginPath = new PathString("/Account/Login"),
    Provider = new CookieAuthenticationProvider
    {
        OnValidateIdentity =
            SecurityStampValidator
                .OnValidateIdentity<ApplicationUserManager, ApplicationUser>(
                    validateInterval: TimeSpan.FromMinutes(30),
                    regenerateIdentity: (manager, user) =>
                        user.GenerateUserIdentityAsync(manager))
    },
    TicketDataFormat = new AspNetTicketDataFormat(
        new DataProtectorShim(
            DataProtectionProvider.Create("{PATH TO COMMON KEY RING FOLDER}",
                (builder) => { builder.SetApplicationName("SharedCookieApp"); })
            .CreateProtector(
                "Microsoft.AspNetCore.Authentication.Cookies." +
                    "CookieAuthenticationMiddleware",
                "Identity.Application",
                "v2"))),
    CookieManager = new ChunkingCookieManager()
});

System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier =
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name";
```

<span data-ttu-id="aaab2-167">當產生使用者識別時，驗證類型 (`Identity.Application`) 必須符合 `AuthenticationType` `UseCookieAuthentication` *App_Start/startup.auth.cs* 中的 set with 所定義的類型。</span><span class="sxs-lookup"><span data-stu-id="aaab2-167">When generating a user identity, the authentication type (`Identity.Application`) must match the type defined in `AuthenticationType` set with `UseCookieAuthentication` in *App_Start/Startup.Auth.cs*.</span></span>

<span data-ttu-id="aaab2-168">*模型/ IdentityModels.cs*：</span><span class="sxs-lookup"><span data-stu-id="aaab2-168">*Models/IdentityModels.cs*:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public async Task<ClaimsIdentity> GenerateUserIdentityAsync(
        UserManager<ApplicationUser> manager)
    {
        // The authenticationType must match the one defined in 
        // CookieAuthenticationOptions.AuthenticationType
        var userIdentity = 
            await manager.CreateIdentityAsync(this, "Identity.Application");

        // Add custom user claims here

        return userIdentity;
    }
}
```

## <a name="use-a-common-user-database"></a><span data-ttu-id="aaab2-169">使用一般使用者資料庫</span><span class="sxs-lookup"><span data-stu-id="aaab2-169">Use a common user database</span></span>

<span data-ttu-id="aaab2-170">當應用程式使用相同 Identity 的) 版本 (相同的架構時 Identity ，請確認 Identity 每個應用程式的系統都指向相同的使用者資料庫。</span><span class="sxs-lookup"><span data-stu-id="aaab2-170">When apps use the same Identity schema (same version of Identity), confirm that the Identity system for each app is pointed at the same user database.</span></span> <span data-ttu-id="aaab2-171">否則，當身分識別系統嘗試比對驗證中的資訊 cookie 與其資料庫中的資訊時，就會在執行時間產生失敗。</span><span class="sxs-lookup"><span data-stu-id="aaab2-171">Otherwise, the identity system produces failures at runtime when it attempts to match the information in the authentication cookie against the information in its database.</span></span>

<span data-ttu-id="aaab2-172">當 Identity 架構不同的應用程式時，通常是因為應用程式使用不同的 Identity 版本，在 Identity 不需要重新對應和新增其他應用程式架構中的資料行的情況下，不可能共用以最新版本為基礎的通用資料庫 Identity 。</span><span class="sxs-lookup"><span data-stu-id="aaab2-172">When the Identity schema is different among apps, usually because apps are using different Identity versions, sharing a common database based on the latest version of Identity isn't possible without remapping and adding columns in other app's Identity schemas.</span></span> <span data-ttu-id="aaab2-173">將其他應用程式升級為使用最新版本通常會更有效率， Identity 讓應用程式可以共用一般資料庫。</span><span class="sxs-lookup"><span data-stu-id="aaab2-173">It's often more efficient to upgrade the other apps to use the latest Identity version so that a common database can be shared by the apps.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="aaab2-174">其他資源</span><span class="sxs-lookup"><span data-stu-id="aaab2-174">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
