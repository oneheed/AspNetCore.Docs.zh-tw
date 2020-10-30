---
title: '使用 :::no-loc(cookie)::: 驗證但不使用 :::no-loc(ASP.NET Core Identity):::'
author: rick-anderson
description: '瞭解如何在 :::no-loc(cookie)::: 不使用驗證的情況下使用 :::no-loc(ASP.NET Core Identity)::: 。'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
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
uid: 'security/authentication/:::no-loc(cookie):::'
ms.openlocfilehash: 04469e0e75c433b40b364873a7e72e30421936f4
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061349"
---
# <a name="use-no-loccookie-authentication-without-no-locaspnet-core-identity"></a><span data-ttu-id="161f2-103">使用 :::no-loc(cookie)::: 驗證但不使用 :::no-loc(ASP.NET Core Identity):::</span><span class="sxs-lookup"><span data-stu-id="161f2-103">Use :::no-loc(cookie)::: authentication without :::no-loc(ASP.NET Core Identity):::</span></span>

<span data-ttu-id="161f2-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="161f2-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="161f2-105">:::no-loc(ASP.NET Core Identity)::: 是完整的完整功能驗證提供者，可用於建立和維護登入。</span><span class="sxs-lookup"><span data-stu-id="161f2-105">:::no-loc(ASP.NET Core Identity)::: is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="161f2-106">但是， :::no-loc(cookie)::: 不能使用不使用的驗證提供者 :::no-loc(ASP.NET Core Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="161f2-106">However, a :::no-loc(cookie):::-based authentication provider without :::no-loc(ASP.NET Core Identity)::: can be used.</span></span> <span data-ttu-id="161f2-107">如需詳細資訊，請參閱<xref:security/authentication/identity>。</span><span class="sxs-lookup"><span data-stu-id="161f2-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="161f2-108">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/:::no-loc(cookie):::/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="161f2-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/:::no-loc(cookie):::/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="161f2-109">為了示範範例應用程式中的目的，假設使用者的使用者帳戶（Maria Rodriguez）會硬式編碼到應用程式中。</span><span class="sxs-lookup"><span data-stu-id="161f2-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="161f2-110">使用 **電子郵件** 位址 `maria.rodriguez@contoso.com` 和任何密碼來登入使用者。</span><span class="sxs-lookup"><span data-stu-id="161f2-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="161f2-111">使用者會在 `AuthenticateUser` *頁面/帳戶/登入 .cs* 檔案的方法中進行驗證。</span><span class="sxs-lookup"><span data-stu-id="161f2-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="161f2-112">在真實世界的範例中，使用者會針對資料庫進行驗證。</span><span class="sxs-lookup"><span data-stu-id="161f2-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="161f2-113">組態</span><span class="sxs-lookup"><span data-stu-id="161f2-113">Configuration</span></span>

<span data-ttu-id="161f2-114">在 `Startup.ConfigureServices` 方法中，使用和方法建立驗證中介軟體 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 服務 <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> ：</span><span class="sxs-lookup"><span data-stu-id="161f2-114">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> methods:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/3.x/:::no-loc(Cookie):::Sample/Startup.cs?name=snippet1)]

<span data-ttu-id="161f2-115"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme> 傳遞以 `AddAuthentication` 設定應用程式的預設驗證配置。</span><span class="sxs-lookup"><span data-stu-id="161f2-115"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="161f2-116">`AuthenticationScheme` 當有多個 :::no-loc(cookie)::: 驗證實例，而且您想要 [使用特定配置來授權](xref:security/authorization/limitingidentitybyscheme)時，這會很有用。</span><span class="sxs-lookup"><span data-stu-id="161f2-116">`AuthenticationScheme` is useful when there are multiple instances of :::no-loc(cookie)::: authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="161f2-117">將設定 `AuthenticationScheme` 為[ :::no-loc(Cookie)::: AuthenticationDefaults](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)時，AuthenticationScheme 會為配置提供值 " :::no-loc(Cookie)::: s"。</span><span class="sxs-lookup"><span data-stu-id="161f2-117">Setting the `AuthenticationScheme` to [:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme) provides a value of ":::no-loc(Cookie):::s" for the scheme.</span></span> <span data-ttu-id="161f2-118">您可以提供任何區分配置的字串值。</span><span class="sxs-lookup"><span data-stu-id="161f2-118">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="161f2-119">應用程式的驗證配置不同于應用程式的 :::no-loc(cookie)::: 驗證配置。</span><span class="sxs-lookup"><span data-stu-id="161f2-119">The app's authentication scheme is different from the app's :::no-loc(cookie)::: authentication scheme.</span></span> <span data-ttu-id="161f2-120">當 :::no-loc(cookie)::: 未提供驗證配置時 <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> ，它會使用 `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` ( " :::no-loc(Cookie)::: s" ) 。</span><span class="sxs-lookup"><span data-stu-id="161f2-120">When a :::no-loc(cookie)::: authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*>, it uses `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (":::no-loc(Cookie):::s").</span></span>

<span data-ttu-id="161f2-121">驗證 :::no-loc(cookie)::: 的 <xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.IsEssential> 屬性預設會設定為 `true` 。</span><span class="sxs-lookup"><span data-stu-id="161f2-121">The authentication :::no-loc(cookie):::'s <xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="161f2-122">:::no-loc(cookie):::當網站訪客未同意資料收集時，允許驗證。</span><span class="sxs-lookup"><span data-stu-id="161f2-122">Authentication :::no-loc(cookie):::s are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="161f2-123">如需詳細資訊，請參閱<xref:security/gdpr#essential-:::no-loc(cookie):::s>。</span><span class="sxs-lookup"><span data-stu-id="161f2-123">For more information, see <xref:security/gdpr#essential-:::no-loc(cookie):::s>.</span></span>

<span data-ttu-id="161f2-124">在中 `Startup.Configure` ，呼叫 `UseAuthentication` 和 `UseAuthorization` 來設定 `HttpContext.User` 屬性，並針對要求執行授權中介軟體。</span><span class="sxs-lookup"><span data-stu-id="161f2-124">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="161f2-125">呼叫 `UseAuthentication` 和方法之前，請先呼叫和 `UseAuthorization` 方法 `UseEndpoints` ：</span><span class="sxs-lookup"><span data-stu-id="161f2-125">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/3.x/:::no-loc(Cookie):::Sample/Startup.cs?name=snippet2)]

<span data-ttu-id="161f2-126"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions>類別是用來設定驗證提供者選項。</span><span class="sxs-lookup"><span data-stu-id="161f2-126">The <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="161f2-127">在 `:::no-loc(Cookie):::AuthenticationOptions` 服務設定中，于方法中設定驗證 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="161f2-127">Set `:::no-loc(Cookie):::AuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
    .Add:::no-loc(Cookie):::(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="161f2-128">:::no-loc(Cookie)::: 原則中介軟體</span><span class="sxs-lookup"><span data-stu-id="161f2-128">:::no-loc(Cookie)::: Policy Middleware</span></span>

<span data-ttu-id="161f2-129">[ :::no-loc(Cookie)::: 原則中介軟體](xref:Microsoft.AspNetCore.:::no-loc(Cookie):::Policy.:::no-loc(Cookie):::PolicyMiddleware)可啟用 :::no-loc(cookie)::: 原則功能。</span><span class="sxs-lookup"><span data-stu-id="161f2-129">[:::no-loc(Cookie)::: Policy Middleware](xref:Microsoft.AspNetCore.:::no-loc(Cookie):::Policy.:::no-loc(Cookie):::PolicyMiddleware) enables :::no-loc(cookie)::: policy capabilities.</span></span> <span data-ttu-id="161f2-130">將中介軟體新增至應用程式處理管線的順序是機密的， &mdash; 它只會影響在管線中註冊的下游元件。</span><span class="sxs-lookup"><span data-stu-id="161f2-130">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.Use:::no-loc(Cookie):::Policy(:::no-loc(cookie):::PolicyOptions);
```

<span data-ttu-id="161f2-131"><xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions> :::no-loc(Cookie)::: :::no-loc(cookie)::: :::no-loc(cookie)::: 當附加或刪除時，請使用提供給原則中介軟體來控制處理與處理處理常式的全域特性 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="161f2-131">Use <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions> provided to the :::no-loc(Cookie)::: Policy Middleware to control global characteristics of :::no-loc(cookie)::: processing and hook into :::no-loc(cookie)::: processing handlers when :::no-loc(cookie):::s are appended or deleted.</span></span>

<span data-ttu-id="161f2-132">預設 <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions.MinimumSameSitePolicy> 值是 `SameSiteMode.Lax` 允許 OAuth2 authentication。</span><span class="sxs-lookup"><span data-stu-id="161f2-132">The default <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="161f2-133">若要嚴格地強制執行相同的網站原則 `SameSiteMode.Strict` ，請設定 `MinimumSameSitePolicy` 。</span><span class="sxs-lookup"><span data-stu-id="161f2-133">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="161f2-134">雖然這項設定會中斷 OAuth2 和其他的跨原始來源驗證配置，但它會提高 :::no-loc(cookie)::: 不依賴跨原始來源要求處理的其他類型應用程式的安全性層級。</span><span class="sxs-lookup"><span data-stu-id="161f2-134">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of :::no-loc(cookie)::: security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var :::no-loc(cookie):::PolicyOptions = new :::no-loc(Cookie):::PolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="161f2-135">的 :::no-loc(Cookie)::: 原則中介軟體設定， `MinimumSameSitePolicy` 可能會影響 `:::no-loc(Cookie):::.SameSite` 下列矩陣中的設定 `:::no-loc(Cookie):::AuthenticationOptions` 。</span><span class="sxs-lookup"><span data-stu-id="161f2-135">The :::no-loc(Cookie)::: Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `:::no-loc(Cookie):::.SameSite` in `:::no-loc(Cookie):::AuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="161f2-136">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="161f2-136">MinimumSameSitePolicy</span></span> | <span data-ttu-id="161f2-137">:::no-loc(Cookie):::.SameSite</span><span class="sxs-lookup"><span data-stu-id="161f2-137">:::no-loc(Cookie):::.SameSite</span></span> | <span data-ttu-id="161f2-138">結果 :::no-loc(Cookie)::: 。SameSite 設定</span><span class="sxs-lookup"><span data-stu-id="161f2-138">Resultant :::no-loc(Cookie):::.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="161f2-139">SameSiteMode。無</span><span class="sxs-lookup"><span data-stu-id="161f2-139">SameSiteMode.None</span></span>     | <span data-ttu-id="161f2-140">SameSiteMode。無</span><span class="sxs-lookup"><span data-stu-id="161f2-140">SameSiteMode.None</span></span><br><span data-ttu-id="161f2-141">SameSiteMode 不嚴格</span><span class="sxs-lookup"><span data-stu-id="161f2-141">SameSiteMode.Lax</span></span><br><span data-ttu-id="161f2-142">SameSiteMode 嚴謹</span><span class="sxs-lookup"><span data-stu-id="161f2-142">SameSiteMode.Strict</span></span> | <span data-ttu-id="161f2-143">SameSiteMode。無</span><span class="sxs-lookup"><span data-stu-id="161f2-143">SameSiteMode.None</span></span><br><span data-ttu-id="161f2-144">SameSiteMode 不嚴格</span><span class="sxs-lookup"><span data-stu-id="161f2-144">SameSiteMode.Lax</span></span><br><span data-ttu-id="161f2-145">SameSiteMode 嚴謹</span><span class="sxs-lookup"><span data-stu-id="161f2-145">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="161f2-146">SameSiteMode 不嚴格</span><span class="sxs-lookup"><span data-stu-id="161f2-146">SameSiteMode.Lax</span></span>      | <span data-ttu-id="161f2-147">SameSiteMode。無</span><span class="sxs-lookup"><span data-stu-id="161f2-147">SameSiteMode.None</span></span><br><span data-ttu-id="161f2-148">SameSiteMode 不嚴格</span><span class="sxs-lookup"><span data-stu-id="161f2-148">SameSiteMode.Lax</span></span><br><span data-ttu-id="161f2-149">SameSiteMode 嚴謹</span><span class="sxs-lookup"><span data-stu-id="161f2-149">SameSiteMode.Strict</span></span> | <span data-ttu-id="161f2-150">SameSiteMode 不嚴格</span><span class="sxs-lookup"><span data-stu-id="161f2-150">SameSiteMode.Lax</span></span><br><span data-ttu-id="161f2-151">SameSiteMode 不嚴格</span><span class="sxs-lookup"><span data-stu-id="161f2-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="161f2-152">SameSiteMode 嚴謹</span><span class="sxs-lookup"><span data-stu-id="161f2-152">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="161f2-153">SameSiteMode 嚴謹</span><span class="sxs-lookup"><span data-stu-id="161f2-153">SameSiteMode.Strict</span></span>   | <span data-ttu-id="161f2-154">SameSiteMode。無</span><span class="sxs-lookup"><span data-stu-id="161f2-154">SameSiteMode.None</span></span><br><span data-ttu-id="161f2-155">SameSiteMode 不嚴格</span><span class="sxs-lookup"><span data-stu-id="161f2-155">SameSiteMode.Lax</span></span><br><span data-ttu-id="161f2-156">SameSiteMode 嚴謹</span><span class="sxs-lookup"><span data-stu-id="161f2-156">SameSiteMode.Strict</span></span> | <span data-ttu-id="161f2-157">SameSiteMode 嚴謹</span><span class="sxs-lookup"><span data-stu-id="161f2-157">SameSiteMode.Strict</span></span><br><span data-ttu-id="161f2-158">SameSiteMode 嚴謹</span><span class="sxs-lookup"><span data-stu-id="161f2-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="161f2-159">SameSiteMode 嚴謹</span><span class="sxs-lookup"><span data-stu-id="161f2-159">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="161f2-160">建立驗證 :::no-loc(cookie):::</span><span class="sxs-lookup"><span data-stu-id="161f2-160">Create an authentication :::no-loc(cookie):::</span></span>

<span data-ttu-id="161f2-161">若要建立 :::no-loc(cookie)::: 保存使用者資訊，請建立 <xref:System.Security.Claims.ClaimsPrincipal> 。</span><span class="sxs-lookup"><span data-stu-id="161f2-161">To create a :::no-loc(cookie)::: holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="161f2-162">使用者資訊會序列化並儲存在中 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="161f2-162">The user information is serialized and stored in the :::no-loc(cookie):::.</span></span> 

<span data-ttu-id="161f2-163"><xref:System.Security.Claims.Claims:::no-loc(Identity):::>使用任何必要的 <xref:System.Security.Claims.Claim> 來建立，並呼叫 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> 以登入使用者：</span><span class="sxs-lookup"><span data-stu-id="161f2-163">Create a <xref:System.Security.Claims.Claims:::no-loc(Identity):::> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/3.x/:::no-loc(Cookie):::Sample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="161f2-164">`SignInAsync` 建立加密的 :::no-loc(cookie)::: ，並將它新增至目前的回應。</span><span class="sxs-lookup"><span data-stu-id="161f2-164">`SignInAsync` creates an encrypted :::no-loc(cookie)::: and adds it to the current response.</span></span> <span data-ttu-id="161f2-165">如果 `AuthenticationScheme` 未指定，則會使用預設配置。</span><span class="sxs-lookup"><span data-stu-id="161f2-165">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="161f2-166"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> 預設只會用於一些特定的路徑，例如登入路徑和登出路徑。</span><span class="sxs-lookup"><span data-stu-id="161f2-166"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> is only used on a few specific paths by default, for example, the login path and logout paths.</span></span> <span data-ttu-id="161f2-167">如需詳細資訊，請參閱[ :::no-loc(Cookie)::: AuthenticationHandler 來源](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/:::no-loc(Cookie):::s/src/:::no-loc(Cookie):::AuthenticationHandler.cs#L334)。</span><span class="sxs-lookup"><span data-stu-id="161f2-167">For more information see the [:::no-loc(Cookie):::AuthenticationHandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/:::no-loc(Cookie):::s/src/:::no-loc(Cookie):::AuthenticationHandler.cs#L334).</span></span>

<span data-ttu-id="161f2-168">ASP.NET Core 的 [資料保護](xref:security/data-protection/using-data-protection) 系統用於加密。</span><span class="sxs-lookup"><span data-stu-id="161f2-168">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="161f2-169">若為裝載于多部電腦上的應用程式、跨應用程式的負載平衡，或使用 web 伺服陣列，請 [將資料保護設定](xref:security/data-protection/configuration/overview) 為使用相同的金鑰通道和應用程式識別碼。</span><span class="sxs-lookup"><span data-stu-id="161f2-169">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="161f2-170">登出</span><span class="sxs-lookup"><span data-stu-id="161f2-170">Sign out</span></span>

<span data-ttu-id="161f2-171">若要登出目前的使用者並刪除其 :::no-loc(cookie)::: ，請呼叫 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> ：</span><span class="sxs-lookup"><span data-stu-id="161f2-171">To sign out the current user and delete their :::no-loc(cookie):::, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/3.x/:::no-loc(Cookie):::Sample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="161f2-172">如果 `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` 未使用 (或 " :::no-loc(Cookie)::: s" ) 作為配置 (例如 "Contoso :::no-loc(Cookie)::: " ) ，請提供設定驗證提供者時所使用的配置。</span><span class="sxs-lookup"><span data-stu-id="161f2-172">If `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (or ":::no-loc(Cookie):::s") isn't used as the scheme (for example, "Contoso:::no-loc(Cookie):::"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="161f2-173">否則，會使用預設配置。</span><span class="sxs-lookup"><span data-stu-id="161f2-173">Otherwise, the default scheme is used.</span></span>

<span data-ttu-id="161f2-174">當瀏覽器關閉時，它會自動刪除以會話為基礎 :::no-loc(cookie)::: 的 (非持續性的 :::no-loc(cookie)::: s) ，但在關閉個別索引標籤時，不 :::no-loc(cookie)::: 會清除任何。</span><span class="sxs-lookup"><span data-stu-id="161f2-174">When the browser closes it automatically deletes session based :::no-loc(cookie):::s (non-persistent :::no-loc(cookie):::s), but no :::no-loc(cookie):::s are cleared when an individual tab is closed.</span></span> <span data-ttu-id="161f2-175">伺服器不會收到索引標籤或瀏覽器關閉事件的通知。</span><span class="sxs-lookup"><span data-stu-id="161f2-175">The server is not notified of tab or browser close events.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="161f2-176">回應後端變更</span><span class="sxs-lookup"><span data-stu-id="161f2-176">React to back-end changes</span></span>

<span data-ttu-id="161f2-177">一旦 :::no-loc(cookie)::: 建立之後， :::no-loc(cookie)::: 就會是身分識別的單一來源。</span><span class="sxs-lookup"><span data-stu-id="161f2-177">Once a :::no-loc(cookie)::: is created, the :::no-loc(cookie)::: is the single source of identity.</span></span> <span data-ttu-id="161f2-178">如果在後端系統中停用使用者帳戶：</span><span class="sxs-lookup"><span data-stu-id="161f2-178">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="161f2-179">應用程式的 :::no-loc(cookie)::: 驗證系統會根據驗證繼續處理要求 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="161f2-179">The app's :::no-loc(cookie)::: authentication system continues to process requests based on the authentication :::no-loc(cookie):::.</span></span>
* <span data-ttu-id="161f2-180">只要驗證有效，使用者就會一直登入應用程式 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="161f2-180">The user remains signed into the app as long as the authentication :::no-loc(cookie)::: is valid.</span></span>

<span data-ttu-id="161f2-181"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents.ValidatePrincipal*>事件可以用來攔截和覆寫身分識別的驗證 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="161f2-181">The <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the :::no-loc(cookie)::: identity.</span></span> <span data-ttu-id="161f2-182">:::no-loc(cookie):::在每個要求上進行驗證，可降低撤銷存取應用程式之使用者的風險。</span><span class="sxs-lookup"><span data-stu-id="161f2-182">Validating the :::no-loc(cookie)::: on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="161f2-183">驗證的其中一個方法 :::no-loc(cookie)::: 是以追蹤使用者資料庫變更的時間為基礎。</span><span class="sxs-lookup"><span data-stu-id="161f2-183">One approach to :::no-loc(cookie)::: validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="161f2-184">如果自使用者發出後資料庫尚未變更，則 :::no-loc(cookie)::: 不需要重新驗證使用者（如果其 :::no-loc(cookie)::: 仍然有效）。</span><span class="sxs-lookup"><span data-stu-id="161f2-184">If the database hasn't been changed since the user's :::no-loc(cookie)::: was issued, there's no need to re-authenticate the user if their :::no-loc(cookie)::: is still valid.</span></span> <span data-ttu-id="161f2-185">在範例應用程式中，資料庫會在中執行， `IUserRepository` 並儲存 `LastChanged` 值。</span><span class="sxs-lookup"><span data-stu-id="161f2-185">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="161f2-186">當使用者在資料庫中更新時， `LastChanged` 值會設定為目前的時間。</span><span class="sxs-lookup"><span data-stu-id="161f2-186">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="161f2-187">若要使 :::no-loc(cookie)::: 資料庫根據值變更時失效 `LastChanged` ，請 :::no-loc(cookie)::: 使用 `LastChanged` 包含 `LastChanged` 資料庫目前值的宣告來建立。</span><span class="sxs-lookup"><span data-stu-id="161f2-187">In order to invalidate a :::no-loc(cookie)::: when the database changes based on the `LastChanged` value, create the :::no-loc(cookie)::: with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claims:::no-loc(Identity)::: = new Claims:::no-loc(Identity):::(
    claims, 
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claims:::no-loc(Identity):::));
```

<span data-ttu-id="161f2-188">若要執行事件的覆寫 `ValidatePrincipal` ，請在衍生自的類別中撰寫具有下列簽章的方法 <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents> ：</span><span class="sxs-lookup"><span data-stu-id="161f2-188">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(:::no-loc(Cookie):::ValidatePrincipalContext)
```

<span data-ttu-id="161f2-189">以下是的範例執行 `:::no-loc(Cookie):::AuthenticationEvents` ：</span><span class="sxs-lookup"><span data-stu-id="161f2-189">The following is an example implementation of `:::no-loc(Cookie):::AuthenticationEvents`:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s;

public class Custom:::no-loc(Cookie):::AuthenticationEvents : :::no-loc(Cookie):::AuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public Custom:::no-loc(Cookie):::AuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(:::no-loc(Cookie):::ValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="161f2-190">:::no-loc(cookie):::在方法的服務註冊期間註冊事件實例 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="161f2-190">Register the events instance during :::no-loc(cookie)::: service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="161f2-191">為您的類別提供 [範圍服務註冊](xref:fundamentals/dependency-injection#service-lifetimes) `Custom:::no-loc(Cookie):::AuthenticationEvents` ：</span><span class="sxs-lookup"><span data-stu-id="161f2-191">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `Custom:::no-loc(Cookie):::AuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
    .Add:::no-loc(Cookie):::(options =>
    {
        options.EventsType = typeof(Custom:::no-loc(Cookie):::AuthenticationEvents);
    });

services.AddScoped<Custom:::no-loc(Cookie):::AuthenticationEvents>();
```

<span data-ttu-id="161f2-192">假設有一種情況，使用者的名稱會以 &mdash; 任何方式來更新不會影響安全性的決策。</span><span class="sxs-lookup"><span data-stu-id="161f2-192">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="161f2-193">如果您想要以非破壞性的更新使用者主體，請呼叫 `context.ReplacePrincipal` 並將 `context.ShouldRenew` 屬性設為 `true` 。</span><span class="sxs-lookup"><span data-stu-id="161f2-193">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="161f2-194">此處所述的方法會在每個要求上觸發。</span><span class="sxs-lookup"><span data-stu-id="161f2-194">The approach described here is triggered on every request.</span></span> <span data-ttu-id="161f2-195">針對 :::no-loc(cookie)::: 每個要求的所有使用者驗證驗證，可能會導致應用程式的效能大幅下降。</span><span class="sxs-lookup"><span data-stu-id="161f2-195">Validating authentication :::no-loc(cookie):::s for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="161f2-196">持續性 :::no-loc(cookie)::: s</span><span class="sxs-lookup"><span data-stu-id="161f2-196">Persistent :::no-loc(cookie):::s</span></span>

<span data-ttu-id="161f2-197">您可能想要在 :::no-loc(cookie)::: 瀏覽器會話之間保存。</span><span class="sxs-lookup"><span data-stu-id="161f2-197">You may want the :::no-loc(cookie)::: to persist across browser sessions.</span></span> <span data-ttu-id="161f2-198">只有在登入或類似的機制中，以明確的使用者同意來啟用此持續性，才能使用 [記住我] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="161f2-198">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="161f2-199">下列程式碼片段會建立身分識別，並 :::no-loc(cookie)::: 透過瀏覽器關閉來對應繼續生存。</span><span class="sxs-lookup"><span data-stu-id="161f2-199">The following code snippet creates an identity and corresponding :::no-loc(cookie)::: that survives through browser closures.</span></span> <span data-ttu-id="161f2-200">系統會接受先前設定的任何滑動到期設定。</span><span class="sxs-lookup"><span data-stu-id="161f2-200">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="161f2-201">如果在 :::no-loc(cookie)::: 瀏覽器關閉時過期，則瀏覽器會在 :::no-loc(cookie)::: 重新開機後清除。</span><span class="sxs-lookup"><span data-stu-id="161f2-201">If the :::no-loc(cookie)::: expires while the browser is closed, the browser clears the :::no-loc(cookie)::: once it's restarted.</span></span>

<span data-ttu-id="161f2-202">設定 <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> 為 `true` <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> ：</span><span class="sxs-lookup"><span data-stu-id="161f2-202">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claims:::no-loc(Identity):::),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="161f2-203">絕對 :::no-loc(cookie)::: 到期</span><span class="sxs-lookup"><span data-stu-id="161f2-203">Absolute :::no-loc(cookie)::: expiration</span></span>

<span data-ttu-id="161f2-204">您可以使用設定絕對到期時間 <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> 。</span><span class="sxs-lookup"><span data-stu-id="161f2-204">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="161f2-205">若要建立持續性 :::no-loc(cookie)::: ， `IsPersistent` 也必須設定。</span><span class="sxs-lookup"><span data-stu-id="161f2-205">To create a persistent :::no-loc(cookie):::, `IsPersistent` must also be set.</span></span> <span data-ttu-id="161f2-206">否則， :::no-loc(cookie)::: 會以會話為基礎的存留期建立，而且可能會在其保留的驗證票證之前或之後過期。</span><span class="sxs-lookup"><span data-stu-id="161f2-206">Otherwise, the :::no-loc(cookie)::: is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="161f2-207">當 `ExpiresUtc` 設定時，它會覆寫之 <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions.ExpireTimeSpan> 選項的值 <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions> （如果有設定的話）。</span><span class="sxs-lookup"><span data-stu-id="161f2-207">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions>, if set.</span></span>

<span data-ttu-id="161f2-208">下列程式碼片段會建立一個 :::no-loc(cookie)::: 會持續20分鐘的身分識別。</span><span class="sxs-lookup"><span data-stu-id="161f2-208">The following code snippet creates an identity and corresponding :::no-loc(cookie)::: that lasts for 20 minutes.</span></span> <span data-ttu-id="161f2-209">這會忽略先前設定的任何滑動到期設定。</span><span class="sxs-lookup"><span data-stu-id="161f2-209">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claims:::no-loc(Identity):::),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="161f2-210">:::no-loc(ASP.NET Core Identity)::: 是完整的完整功能驗證提供者，可用於建立和維護登入。</span><span class="sxs-lookup"><span data-stu-id="161f2-210">:::no-loc(ASP.NET Core Identity)::: is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="161f2-211">但是， :::no-loc(cookie)::: 不能使用不使用的驗證提供者 :::no-loc(ASP.NET Core Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="161f2-211">However, a :::no-loc(cookie):::-based authentication provider without :::no-loc(ASP.NET Core Identity)::: can be used.</span></span> <span data-ttu-id="161f2-212">如需詳細資訊，請參閱<xref:security/authentication/identity>。</span><span class="sxs-lookup"><span data-stu-id="161f2-212">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="161f2-213">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/:::no-loc(cookie):::/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="161f2-213">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/:::no-loc(cookie):::/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="161f2-214">為了示範範例應用程式中的目的，假設使用者的使用者帳戶（Maria Rodriguez）會硬式編碼到應用程式中。</span><span class="sxs-lookup"><span data-stu-id="161f2-214">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="161f2-215">使用 **電子郵件** 位址 `maria.rodriguez@contoso.com` 和任何密碼來登入使用者。</span><span class="sxs-lookup"><span data-stu-id="161f2-215">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="161f2-216">使用者會在 `AuthenticateUser` *頁面/帳戶/登入 .cs* 檔案的方法中進行驗證。</span><span class="sxs-lookup"><span data-stu-id="161f2-216">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="161f2-217">在真實世界的範例中，使用者會針對資料庫進行驗證。</span><span class="sxs-lookup"><span data-stu-id="161f2-217">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="161f2-218">組態</span><span class="sxs-lookup"><span data-stu-id="161f2-218">Configuration</span></span>

<span data-ttu-id="161f2-219">如果應用程式不使用 [AspNetCore 中繼套件](xref:fundamentals/metapackage-app)，請在專案檔中建立 AspNetCore 的封裝參考 [。 :::no-loc(Cookie):::s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s/) 套件。</span><span class="sxs-lookup"><span data-stu-id="161f2-219">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s/) package.</span></span>

<span data-ttu-id="161f2-220">在 `Startup.ConfigureServices` 方法中，使用和方法建立驗證中介軟體 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 服務 <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> ：</span><span class="sxs-lookup"><span data-stu-id="161f2-220">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> methods:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/2.x/:::no-loc(Cookie):::Sample/Startup.cs?name=snippet1)]

<span data-ttu-id="161f2-221"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme> 傳遞以 `AddAuthentication` 設定應用程式的預設驗證配置。</span><span class="sxs-lookup"><span data-stu-id="161f2-221"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="161f2-222">`AuthenticationScheme` 當有多個 :::no-loc(cookie)::: 驗證實例，而且您想要 [使用特定配置來授權](xref:security/authorization/limitingidentitybyscheme)時，這會很有用。</span><span class="sxs-lookup"><span data-stu-id="161f2-222">`AuthenticationScheme` is useful when there are multiple instances of :::no-loc(cookie)::: authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="161f2-223">將設定 `AuthenticationScheme` 為[ :::no-loc(Cookie)::: AuthenticationDefaults](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)時，AuthenticationScheme 會為配置提供值 " :::no-loc(Cookie)::: s"。</span><span class="sxs-lookup"><span data-stu-id="161f2-223">Setting the `AuthenticationScheme` to [:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme) provides a value of ":::no-loc(Cookie):::s" for the scheme.</span></span> <span data-ttu-id="161f2-224">您可以提供任何區分配置的字串值。</span><span class="sxs-lookup"><span data-stu-id="161f2-224">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="161f2-225">應用程式的驗證配置不同于應用程式的 :::no-loc(cookie)::: 驗證配置。</span><span class="sxs-lookup"><span data-stu-id="161f2-225">The app's authentication scheme is different from the app's :::no-loc(cookie)::: authentication scheme.</span></span> <span data-ttu-id="161f2-226">當 :::no-loc(cookie)::: 未提供驗證配置時 <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> ，它會使用 `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` ( " :::no-loc(Cookie)::: s" ) 。</span><span class="sxs-lookup"><span data-stu-id="161f2-226">When a :::no-loc(cookie)::: authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*>, it uses `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (":::no-loc(Cookie):::s").</span></span>

<span data-ttu-id="161f2-227">驗證 :::no-loc(cookie)::: 的 <xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.IsEssential> 屬性預設會設定為 `true` 。</span><span class="sxs-lookup"><span data-stu-id="161f2-227">The authentication :::no-loc(cookie):::'s <xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="161f2-228">:::no-loc(cookie):::當網站訪客未同意資料收集時，允許驗證。</span><span class="sxs-lookup"><span data-stu-id="161f2-228">Authentication :::no-loc(cookie):::s are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="161f2-229">如需詳細資訊，請參閱<xref:security/gdpr#essential-:::no-loc(cookie):::s>。</span><span class="sxs-lookup"><span data-stu-id="161f2-229">For more information, see <xref:security/gdpr#essential-:::no-loc(cookie):::s>.</span></span>

<span data-ttu-id="161f2-230">在 `Startup.Configure` 方法中，呼叫 `UseAuthentication` 方法以叫用設定屬性的驗證中介軟體 `HttpContext.User` 。</span><span class="sxs-lookup"><span data-stu-id="161f2-230">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="161f2-231">呼叫 `UseAuthentication` 或之前，請先呼叫方法 `UseMvcWithDefaultRoute` `UseMvc` ：</span><span class="sxs-lookup"><span data-stu-id="161f2-231">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/2.x/:::no-loc(Cookie):::Sample/Startup.cs?name=snippet2)]

<span data-ttu-id="161f2-232"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions>類別是用來設定驗證提供者選項。</span><span class="sxs-lookup"><span data-stu-id="161f2-232">The <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="161f2-233">在 `:::no-loc(Cookie):::AuthenticationOptions` 服務設定中，于方法中設定驗證 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="161f2-233">Set `:::no-loc(Cookie):::AuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
    .Add:::no-loc(Cookie):::(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="161f2-234">:::no-loc(Cookie)::: 原則中介軟體</span><span class="sxs-lookup"><span data-stu-id="161f2-234">:::no-loc(Cookie)::: Policy Middleware</span></span>

<span data-ttu-id="161f2-235">[ :::no-loc(Cookie)::: 原則中介軟體](xref:Microsoft.AspNetCore.:::no-loc(Cookie):::Policy.:::no-loc(Cookie):::PolicyMiddleware)可啟用 :::no-loc(cookie)::: 原則功能。</span><span class="sxs-lookup"><span data-stu-id="161f2-235">[:::no-loc(Cookie)::: Policy Middleware](xref:Microsoft.AspNetCore.:::no-loc(Cookie):::Policy.:::no-loc(Cookie):::PolicyMiddleware) enables :::no-loc(cookie)::: policy capabilities.</span></span> <span data-ttu-id="161f2-236">將中介軟體新增至應用程式處理管線的順序是機密的， &mdash; 它只會影響在管線中註冊的下游元件。</span><span class="sxs-lookup"><span data-stu-id="161f2-236">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.Use:::no-loc(Cookie):::Policy(:::no-loc(cookie):::PolicyOptions);
```

<span data-ttu-id="161f2-237"><xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions> :::no-loc(Cookie)::: :::no-loc(cookie)::: :::no-loc(cookie)::: 當附加或刪除時，請使用提供給原則中介軟體來控制處理與處理處理常式的全域特性 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="161f2-237">Use <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions> provided to the :::no-loc(Cookie)::: Policy Middleware to control global characteristics of :::no-loc(cookie)::: processing and hook into :::no-loc(cookie)::: processing handlers when :::no-loc(cookie):::s are appended or deleted.</span></span>

<span data-ttu-id="161f2-238">預設 <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions.MinimumSameSitePolicy> 值是 `SameSiteMode.Lax` 允許 OAuth2 authentication。</span><span class="sxs-lookup"><span data-stu-id="161f2-238">The default <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="161f2-239">若要嚴格地強制執行相同的網站原則 `SameSiteMode.Strict` ，請設定 `MinimumSameSitePolicy` 。</span><span class="sxs-lookup"><span data-stu-id="161f2-239">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="161f2-240">雖然這項設定會中斷 OAuth2 和其他的跨原始來源驗證配置，但它會提高 :::no-loc(cookie)::: 不依賴跨原始來源要求處理的其他類型應用程式的安全性層級。</span><span class="sxs-lookup"><span data-stu-id="161f2-240">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of :::no-loc(cookie)::: security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var :::no-loc(cookie):::PolicyOptions = new :::no-loc(Cookie):::PolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="161f2-241">的 :::no-loc(Cookie)::: 原則中介軟體設定， `MinimumSameSitePolicy` 可能會影響 `:::no-loc(Cookie):::.SameSite` 下列矩陣中的設定 `:::no-loc(Cookie):::AuthenticationOptions` 。</span><span class="sxs-lookup"><span data-stu-id="161f2-241">The :::no-loc(Cookie)::: Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `:::no-loc(Cookie):::.SameSite` in `:::no-loc(Cookie):::AuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="161f2-242">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="161f2-242">MinimumSameSitePolicy</span></span> | <span data-ttu-id="161f2-243">:::no-loc(Cookie):::.SameSite</span><span class="sxs-lookup"><span data-stu-id="161f2-243">:::no-loc(Cookie):::.SameSite</span></span> | <span data-ttu-id="161f2-244">結果 :::no-loc(Cookie)::: 。SameSite 設定</span><span class="sxs-lookup"><span data-stu-id="161f2-244">Resultant :::no-loc(Cookie):::.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="161f2-245">SameSiteMode。無</span><span class="sxs-lookup"><span data-stu-id="161f2-245">SameSiteMode.None</span></span>     | <span data-ttu-id="161f2-246">SameSiteMode。無</span><span class="sxs-lookup"><span data-stu-id="161f2-246">SameSiteMode.None</span></span><br><span data-ttu-id="161f2-247">SameSiteMode 不嚴格</span><span class="sxs-lookup"><span data-stu-id="161f2-247">SameSiteMode.Lax</span></span><br><span data-ttu-id="161f2-248">SameSiteMode 嚴謹</span><span class="sxs-lookup"><span data-stu-id="161f2-248">SameSiteMode.Strict</span></span> | <span data-ttu-id="161f2-249">SameSiteMode。無</span><span class="sxs-lookup"><span data-stu-id="161f2-249">SameSiteMode.None</span></span><br><span data-ttu-id="161f2-250">SameSiteMode 不嚴格</span><span class="sxs-lookup"><span data-stu-id="161f2-250">SameSiteMode.Lax</span></span><br><span data-ttu-id="161f2-251">SameSiteMode 嚴謹</span><span class="sxs-lookup"><span data-stu-id="161f2-251">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="161f2-252">SameSiteMode 不嚴格</span><span class="sxs-lookup"><span data-stu-id="161f2-252">SameSiteMode.Lax</span></span>      | <span data-ttu-id="161f2-253">SameSiteMode。無</span><span class="sxs-lookup"><span data-stu-id="161f2-253">SameSiteMode.None</span></span><br><span data-ttu-id="161f2-254">SameSiteMode 不嚴格</span><span class="sxs-lookup"><span data-stu-id="161f2-254">SameSiteMode.Lax</span></span><br><span data-ttu-id="161f2-255">SameSiteMode 嚴謹</span><span class="sxs-lookup"><span data-stu-id="161f2-255">SameSiteMode.Strict</span></span> | <span data-ttu-id="161f2-256">SameSiteMode 不嚴格</span><span class="sxs-lookup"><span data-stu-id="161f2-256">SameSiteMode.Lax</span></span><br><span data-ttu-id="161f2-257">SameSiteMode 不嚴格</span><span class="sxs-lookup"><span data-stu-id="161f2-257">SameSiteMode.Lax</span></span><br><span data-ttu-id="161f2-258">SameSiteMode 嚴謹</span><span class="sxs-lookup"><span data-stu-id="161f2-258">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="161f2-259">SameSiteMode 嚴謹</span><span class="sxs-lookup"><span data-stu-id="161f2-259">SameSiteMode.Strict</span></span>   | <span data-ttu-id="161f2-260">SameSiteMode。無</span><span class="sxs-lookup"><span data-stu-id="161f2-260">SameSiteMode.None</span></span><br><span data-ttu-id="161f2-261">SameSiteMode 不嚴格</span><span class="sxs-lookup"><span data-stu-id="161f2-261">SameSiteMode.Lax</span></span><br><span data-ttu-id="161f2-262">SameSiteMode 嚴謹</span><span class="sxs-lookup"><span data-stu-id="161f2-262">SameSiteMode.Strict</span></span> | <span data-ttu-id="161f2-263">SameSiteMode 嚴謹</span><span class="sxs-lookup"><span data-stu-id="161f2-263">SameSiteMode.Strict</span></span><br><span data-ttu-id="161f2-264">SameSiteMode 嚴謹</span><span class="sxs-lookup"><span data-stu-id="161f2-264">SameSiteMode.Strict</span></span><br><span data-ttu-id="161f2-265">SameSiteMode 嚴謹</span><span class="sxs-lookup"><span data-stu-id="161f2-265">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="161f2-266">建立驗證 :::no-loc(cookie):::</span><span class="sxs-lookup"><span data-stu-id="161f2-266">Create an authentication :::no-loc(cookie):::</span></span>

<span data-ttu-id="161f2-267">若要建立 :::no-loc(cookie)::: 保存使用者資訊，請建立 <xref:System.Security.Claims.ClaimsPrincipal> 。</span><span class="sxs-lookup"><span data-stu-id="161f2-267">To create a :::no-loc(cookie)::: holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="161f2-268">使用者資訊會序列化並儲存在中 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="161f2-268">The user information is serialized and stored in the :::no-loc(cookie):::.</span></span> 

<span data-ttu-id="161f2-269"><xref:System.Security.Claims.Claims:::no-loc(Identity):::>使用任何必要的 <xref:System.Security.Claims.Claim> 來建立，並呼叫 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> 以登入使用者：</span><span class="sxs-lookup"><span data-stu-id="161f2-269">Create a <xref:System.Security.Claims.Claims:::no-loc(Identity):::> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/2.x/:::no-loc(Cookie):::Sample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="161f2-270">`SignInAsync` 建立加密的 :::no-loc(cookie)::: ，並將它新增至目前的回應。</span><span class="sxs-lookup"><span data-stu-id="161f2-270">`SignInAsync` creates an encrypted :::no-loc(cookie)::: and adds it to the current response.</span></span> <span data-ttu-id="161f2-271">如果 `AuthenticationScheme` 未指定，則會使用預設配置。</span><span class="sxs-lookup"><span data-stu-id="161f2-271">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="161f2-272">ASP.NET Core 的 [資料保護](xref:security/data-protection/using-data-protection) 系統用於加密。</span><span class="sxs-lookup"><span data-stu-id="161f2-272">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="161f2-273">若為裝載于多部電腦上的應用程式、跨應用程式的負載平衡，或使用 web 伺服陣列，請 [將資料保護設定](xref:security/data-protection/configuration/overview) 為使用相同的金鑰通道和應用程式識別碼。</span><span class="sxs-lookup"><span data-stu-id="161f2-273">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="161f2-274">登出</span><span class="sxs-lookup"><span data-stu-id="161f2-274">Sign out</span></span>

<span data-ttu-id="161f2-275">若要登出目前的使用者並刪除其 :::no-loc(cookie)::: ，請呼叫 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> ：</span><span class="sxs-lookup"><span data-stu-id="161f2-275">To sign out the current user and delete their :::no-loc(cookie):::, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/2.x/:::no-loc(Cookie):::Sample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="161f2-276">如果 `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` 未使用 (或 " :::no-loc(Cookie)::: s" ) 作為配置 (例如 "Contoso :::no-loc(Cookie)::: " ) ，請提供設定驗證提供者時所使用的配置。</span><span class="sxs-lookup"><span data-stu-id="161f2-276">If `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (or ":::no-loc(Cookie):::s") isn't used as the scheme (for example, "Contoso:::no-loc(Cookie):::"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="161f2-277">否則，會使用預設配置。</span><span class="sxs-lookup"><span data-stu-id="161f2-277">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="161f2-278">回應後端變更</span><span class="sxs-lookup"><span data-stu-id="161f2-278">React to back-end changes</span></span>

<span data-ttu-id="161f2-279">一旦 :::no-loc(cookie)::: 建立之後， :::no-loc(cookie)::: 就會是身分識別的單一來源。</span><span class="sxs-lookup"><span data-stu-id="161f2-279">Once a :::no-loc(cookie)::: is created, the :::no-loc(cookie)::: is the single source of identity.</span></span> <span data-ttu-id="161f2-280">如果在後端系統中停用使用者帳戶：</span><span class="sxs-lookup"><span data-stu-id="161f2-280">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="161f2-281">應用程式的 :::no-loc(cookie)::: 驗證系統會根據驗證繼續處理要求 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="161f2-281">The app's :::no-loc(cookie)::: authentication system continues to process requests based on the authentication :::no-loc(cookie):::.</span></span>
* <span data-ttu-id="161f2-282">只要驗證有效，使用者就會一直登入應用程式 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="161f2-282">The user remains signed into the app as long as the authentication :::no-loc(cookie)::: is valid.</span></span>

<span data-ttu-id="161f2-283"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents.ValidatePrincipal*>事件可以用來攔截和覆寫身分識別的驗證 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="161f2-283">The <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the :::no-loc(cookie)::: identity.</span></span> <span data-ttu-id="161f2-284">:::no-loc(cookie):::在每個要求上進行驗證，可降低撤銷存取應用程式之使用者的風險。</span><span class="sxs-lookup"><span data-stu-id="161f2-284">Validating the :::no-loc(cookie)::: on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="161f2-285">驗證的其中一個方法 :::no-loc(cookie)::: 是以追蹤使用者資料庫變更的時間為基礎。</span><span class="sxs-lookup"><span data-stu-id="161f2-285">One approach to :::no-loc(cookie)::: validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="161f2-286">如果自使用者發出後資料庫尚未變更，則 :::no-loc(cookie)::: 不需要重新驗證使用者（如果其 :::no-loc(cookie)::: 仍然有效）。</span><span class="sxs-lookup"><span data-stu-id="161f2-286">If the database hasn't been changed since the user's :::no-loc(cookie)::: was issued, there's no need to re-authenticate the user if their :::no-loc(cookie)::: is still valid.</span></span> <span data-ttu-id="161f2-287">在範例應用程式中，資料庫會在中執行， `IUserRepository` 並儲存 `LastChanged` 值。</span><span class="sxs-lookup"><span data-stu-id="161f2-287">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="161f2-288">當使用者在資料庫中更新時， `LastChanged` 值會設定為目前的時間。</span><span class="sxs-lookup"><span data-stu-id="161f2-288">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="161f2-289">若要使 :::no-loc(cookie)::: 資料庫根據值變更時失效 `LastChanged` ，請 :::no-loc(cookie)::: 使用 `LastChanged` 包含 `LastChanged` 資料庫目前值的宣告來建立。</span><span class="sxs-lookup"><span data-stu-id="161f2-289">In order to invalidate a :::no-loc(cookie)::: when the database changes based on the `LastChanged` value, create the :::no-loc(cookie)::: with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claims:::no-loc(Identity)::: = new Claims:::no-loc(Identity):::(
    claims, 
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claims:::no-loc(Identity):::));
```

<span data-ttu-id="161f2-290">若要執行事件的覆寫 `ValidatePrincipal` ，請在衍生自的類別中撰寫具有下列簽章的方法 <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents> ：</span><span class="sxs-lookup"><span data-stu-id="161f2-290">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(:::no-loc(Cookie):::ValidatePrincipalContext)
```

<span data-ttu-id="161f2-291">以下是的範例執行 `:::no-loc(Cookie):::AuthenticationEvents` ：</span><span class="sxs-lookup"><span data-stu-id="161f2-291">The following is an example implementation of `:::no-loc(Cookie):::AuthenticationEvents`:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s;

public class Custom:::no-loc(Cookie):::AuthenticationEvents : :::no-loc(Cookie):::AuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public Custom:::no-loc(Cookie):::AuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(:::no-loc(Cookie):::ValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="161f2-292">:::no-loc(cookie):::在方法的服務註冊期間註冊事件實例 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="161f2-292">Register the events instance during :::no-loc(cookie)::: service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="161f2-293">為您的類別提供 [範圍服務註冊](xref:fundamentals/dependency-injection#service-lifetimes) `Custom:::no-loc(Cookie):::AuthenticationEvents` ：</span><span class="sxs-lookup"><span data-stu-id="161f2-293">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `Custom:::no-loc(Cookie):::AuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
    .Add:::no-loc(Cookie):::(options =>
    {
        options.EventsType = typeof(Custom:::no-loc(Cookie):::AuthenticationEvents);
    });

services.AddScoped<Custom:::no-loc(Cookie):::AuthenticationEvents>();
```

<span data-ttu-id="161f2-294">假設有一種情況，使用者的名稱會以 &mdash; 任何方式來更新不會影響安全性的決策。</span><span class="sxs-lookup"><span data-stu-id="161f2-294">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="161f2-295">如果您想要以非破壞性的更新使用者主體，請呼叫 `context.ReplacePrincipal` 並將 `context.ShouldRenew` 屬性設為 `true` 。</span><span class="sxs-lookup"><span data-stu-id="161f2-295">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="161f2-296">此處所述的方法會在每個要求上觸發。</span><span class="sxs-lookup"><span data-stu-id="161f2-296">The approach described here is triggered on every request.</span></span> <span data-ttu-id="161f2-297">針對 :::no-loc(cookie)::: 每個要求的所有使用者驗證驗證，可能會導致應用程式的效能大幅下降。</span><span class="sxs-lookup"><span data-stu-id="161f2-297">Validating authentication :::no-loc(cookie):::s for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="161f2-298">持續性 :::no-loc(cookie)::: s</span><span class="sxs-lookup"><span data-stu-id="161f2-298">Persistent :::no-loc(cookie):::s</span></span>

<span data-ttu-id="161f2-299">您可能想要在 :::no-loc(cookie)::: 瀏覽器會話之間保存。</span><span class="sxs-lookup"><span data-stu-id="161f2-299">You may want the :::no-loc(cookie)::: to persist across browser sessions.</span></span> <span data-ttu-id="161f2-300">只有在登入或類似的機制中，以明確的使用者同意來啟用此持續性，才能使用 [記住我] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="161f2-300">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="161f2-301">下列程式碼片段會建立身分識別，並 :::no-loc(cookie)::: 透過瀏覽器關閉來對應繼續生存。</span><span class="sxs-lookup"><span data-stu-id="161f2-301">The following code snippet creates an identity and corresponding :::no-loc(cookie)::: that survives through browser closures.</span></span> <span data-ttu-id="161f2-302">系統會接受先前設定的任何滑動到期設定。</span><span class="sxs-lookup"><span data-stu-id="161f2-302">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="161f2-303">如果在 :::no-loc(cookie)::: 瀏覽器關閉時過期，則瀏覽器會在 :::no-loc(cookie)::: 重新開機後清除。</span><span class="sxs-lookup"><span data-stu-id="161f2-303">If the :::no-loc(cookie)::: expires while the browser is closed, the browser clears the :::no-loc(cookie)::: once it's restarted.</span></span>

<span data-ttu-id="161f2-304">設定 <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> 為 `true` <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> ：</span><span class="sxs-lookup"><span data-stu-id="161f2-304">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claims:::no-loc(Identity):::),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="161f2-305">絕對 :::no-loc(cookie)::: 到期</span><span class="sxs-lookup"><span data-stu-id="161f2-305">Absolute :::no-loc(cookie)::: expiration</span></span>

<span data-ttu-id="161f2-306">您可以使用設定絕對到期時間 <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> 。</span><span class="sxs-lookup"><span data-stu-id="161f2-306">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="161f2-307">若要建立持續性 :::no-loc(cookie)::: ， `IsPersistent` 也必須設定。</span><span class="sxs-lookup"><span data-stu-id="161f2-307">To create a persistent :::no-loc(cookie):::, `IsPersistent` must also be set.</span></span> <span data-ttu-id="161f2-308">否則， :::no-loc(cookie)::: 會以會話為基礎的存留期建立，而且可能會在其保留的驗證票證之前或之後過期。</span><span class="sxs-lookup"><span data-stu-id="161f2-308">Otherwise, the :::no-loc(cookie)::: is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="161f2-309">當 `ExpiresUtc` 設定時，它會覆寫之 <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::AuthenticationOptions.ExpireTimeSpan> 選項的值 <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::AuthenticationOptions> （如果有設定的話）。</span><span class="sxs-lookup"><span data-stu-id="161f2-309">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::AuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::AuthenticationOptions>, if set.</span></span>

<span data-ttu-id="161f2-310">下列程式碼片段會建立一個 :::no-loc(cookie)::: 會持續20分鐘的身分識別。</span><span class="sxs-lookup"><span data-stu-id="161f2-310">The following code snippet creates an identity and corresponding :::no-loc(cookie)::: that lasts for 20 minutes.</span></span> <span data-ttu-id="161f2-311">這會忽略先前設定的任何滑動到期設定。</span><span class="sxs-lookup"><span data-stu-id="161f2-311">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claims:::no-loc(Identity):::),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="161f2-312">其他資源</span><span class="sxs-lookup"><span data-stu-id="161f2-312">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="161f2-313">以原則為基礎的角色檢查</span><span class="sxs-lookup"><span data-stu-id="161f2-313">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
