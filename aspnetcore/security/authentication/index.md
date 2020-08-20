---
title: ASP.NET Core 驗證的總覽
author: mjrousos
description: 瞭解 ASP.NET Core 中的驗證。
ms.author: riande
ms.custom: mvc
ms.date: 03/03/2020
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
uid: security/authentication/index
ms.openlocfilehash: 2511d68dd049ca47dea002d8960e7d2fd0207383
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632638"
---
# <a name="overview-of-aspnet-core-authentication"></a><span data-ttu-id="6122d-103">ASP.NET Core 驗證的總覽</span><span class="sxs-lookup"><span data-stu-id="6122d-103">Overview of ASP.NET Core authentication</span></span>

<span data-ttu-id="6122d-104">由 [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="6122d-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="6122d-105">驗證是決定使用者身分識別的程式。</span><span class="sxs-lookup"><span data-stu-id="6122d-105">Authentication is the process of determining a user's identity.</span></span> <span data-ttu-id="6122d-106">[授權](xref:security/authorization/introduction) 是判斷使用者是否有權存取資源的程式。</span><span class="sxs-lookup"><span data-stu-id="6122d-106">[Authorization](xref:security/authorization/introduction) is the process of determining whether a user has access to a resource.</span></span> <span data-ttu-id="6122d-107">在 ASP.NET Core 中，驗證是由 `IAuthenticationService` 驗證 [中介軟體](xref:fundamentals/middleware/index)所使用的來處理。</span><span class="sxs-lookup"><span data-stu-id="6122d-107">In ASP.NET Core, authentication is handled by the `IAuthenticationService`, which is used by authentication [middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="6122d-108">驗證服務會使用已註冊的驗證處理常式來完成驗證相關的動作。</span><span class="sxs-lookup"><span data-stu-id="6122d-108">The authentication service uses registered authentication handlers to complete authentication-related actions.</span></span> <span data-ttu-id="6122d-109">驗證相關動作的範例包括：</span><span class="sxs-lookup"><span data-stu-id="6122d-109">Examples of authentication-related actions include:</span></span>

* <span data-ttu-id="6122d-110">驗證使用者。</span><span class="sxs-lookup"><span data-stu-id="6122d-110">Authenticating a user.</span></span>
* <span data-ttu-id="6122d-111">當未驗證的使用者嘗試存取受限制的資源時回應。</span><span class="sxs-lookup"><span data-stu-id="6122d-111">Responding when an unauthenticated user tries to access a restricted resource.</span></span>

<span data-ttu-id="6122d-112">註冊的驗證處理常式和其設定選項稱為「配置」。</span><span class="sxs-lookup"><span data-stu-id="6122d-112">The registered authentication handlers and their configuration options are called "schemes".</span></span>

<span data-ttu-id="6122d-113">驗證配置的指定方式是在中註冊驗證服務 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="6122d-113">Authentication schemes are specified by registering authentication services in `Startup.ConfigureServices`:</span></span>

* <span data-ttu-id="6122d-114">藉由在呼叫 (（例如或）之後呼叫配置特定的擴充方法 `services.AddAuthentication` `AddJwtBearer` ，例如 `AddCookie`) 。</span><span class="sxs-lookup"><span data-stu-id="6122d-114">By calling a scheme-specific extension method after a call to `services.AddAuthentication` (such as `AddJwtBearer` or `AddCookie`, for example).</span></span> <span data-ttu-id="6122d-115">這些擴充方法會使用 [AuthenticationBuilder AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) ，以適當的設定來註冊配置。</span><span class="sxs-lookup"><span data-stu-id="6122d-115">These extension methods use [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) to register schemes with appropriate settings.</span></span>
* <span data-ttu-id="6122d-116">通常會透過直接呼叫 [AuthenticationBuilder AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) 。</span><span class="sxs-lookup"><span data-stu-id="6122d-116">Less commonly, by calling [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) directly.</span></span>

<span data-ttu-id="6122d-117">例如，下列程式碼會註冊的驗證服務和處理常式， cookie 以及 JWT 持有人驗證配置：</span><span class="sxs-lookup"><span data-stu-id="6122d-117">For example, the following code registers authentication services and handlers for cookie and JWT bearer authentication schemes:</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(JwtBearerDefaults.AuthenticationScheme, options => Configuration.Bind("JwtSettings", options))
    .AddCookie(CookieAuthenticationDefaults.AuthenticationScheme, options => Configuration.Bind("CookieSettings", options));
```

<span data-ttu-id="6122d-118">`AddAuthentication`參數 `JwtBearerDefaults.AuthenticationScheme` 是未要求特定配置時，預設要使用的配置名稱。</span><span class="sxs-lookup"><span data-stu-id="6122d-118">The `AddAuthentication` parameter `JwtBearerDefaults.AuthenticationScheme` is the name of the scheme to use by default when a specific scheme isn't requested.</span></span>

<span data-ttu-id="6122d-119">如果使用了多個配置，則 (的授權原則或授權屬性) 可以 [指定驗證配置 () 或所依賴的配置 ](xref:security/authorization/limitingidentitybyscheme) ，以驗證使用者。</span><span class="sxs-lookup"><span data-stu-id="6122d-119">If multiple schemes are used, authorization policies (or authorization attributes) can [specify the authentication scheme (or schemes)](xref:security/authorization/limitingidentitybyscheme) they depend on to authenticate the user.</span></span> <span data-ttu-id="6122d-120">在上述範例中，您 cookie 可以藉由預設指定其名稱 (來使用驗證配置 `CookieAuthenticationDefaults.AuthenticationScheme` ，但在呼叫) 時可以提供不同的名稱 `AddCookie` 。</span><span class="sxs-lookup"><span data-stu-id="6122d-120">In the example above, the cookie authentication scheme could be used by specifying its name (`CookieAuthenticationDefaults.AuthenticationScheme` by default, though a different name could be provided when calling `AddCookie`).</span></span>

<span data-ttu-id="6122d-121">在某些情況下，的呼叫會 `AddAuthentication` 由其他擴充方法自動進行。</span><span class="sxs-lookup"><span data-stu-id="6122d-121">In some cases, the call to `AddAuthentication` is automatically made by other extension methods.</span></span> <span data-ttu-id="6122d-122">例如，在使用時 [ASP.NET Core Identity](xref:security/authentication/identity) ， `AddAuthentication` 會在內部呼叫。</span><span class="sxs-lookup"><span data-stu-id="6122d-122">For example, when using [ASP.NET Core Identity](xref:security/authentication/identity), `AddAuthentication` is called internally.</span></span>

<span data-ttu-id="6122d-123">藉 `Startup.Configure` 由呼叫 <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> 應用程式上的擴充方法，即可在中新增驗證中介軟體 `IApplicationBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="6122d-123">The Authentication middleware is added in `Startup.Configure` by calling the <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> extension method on the app's `IApplicationBuilder`.</span></span> <span data-ttu-id="6122d-124">呼叫會 `UseAuthentication` 註冊使用先前註冊之驗證配置的中介軟體。</span><span class="sxs-lookup"><span data-stu-id="6122d-124">Calling `UseAuthentication` registers the middleware which uses the previously registered authentication schemes.</span></span> <span data-ttu-id="6122d-125">在 `UseAuthentication` 與要驗證之使用者相依的任何中介軟體之前呼叫。</span><span class="sxs-lookup"><span data-stu-id="6122d-125">Call `UseAuthentication` before any middleware that depends on users being authenticated.</span></span> <span data-ttu-id="6122d-126">使用端點路由時，的呼叫 `UseAuthentication` 必須移至：</span><span class="sxs-lookup"><span data-stu-id="6122d-126">When using endpoint routing, the call to `UseAuthentication` must go:</span></span>

* <span data-ttu-id="6122d-127">之後 `UseRouting` ，您就可以使用路由資訊進行驗證決策。</span><span class="sxs-lookup"><span data-stu-id="6122d-127">After `UseRouting`, so that route information is available for authentication decisions.</span></span>
* <span data-ttu-id="6122d-128">之前 `UseEndpoints` ，會先驗證使用者，然後再存取端點。</span><span class="sxs-lookup"><span data-stu-id="6122d-128">Before `UseEndpoints`, so that users are authenticated before accessing the endpoints.</span></span>

## <a name="authentication-concepts"></a><span data-ttu-id="6122d-129">驗證概念</span><span class="sxs-lookup"><span data-stu-id="6122d-129">Authentication Concepts</span></span>

### <a name="authentication-scheme"></a><span data-ttu-id="6122d-130">驗證配置</span><span class="sxs-lookup"><span data-stu-id="6122d-130">Authentication scheme</span></span>

<span data-ttu-id="6122d-131">驗證配置是對應至的名稱：</span><span class="sxs-lookup"><span data-stu-id="6122d-131">An authentication scheme is a name which corresponds to:</span></span>

* <span data-ttu-id="6122d-132">驗證處理常式。</span><span class="sxs-lookup"><span data-stu-id="6122d-132">An authentication handler.</span></span>
* <span data-ttu-id="6122d-133">用於設定該特定處理常式實例的選項。</span><span class="sxs-lookup"><span data-stu-id="6122d-133">Options for configuring that specific instance of the handler.</span></span>

<span data-ttu-id="6122d-134">配置可作為一種機制，用來參考相關處理常式的驗證、挑戰和禁止行為。</span><span class="sxs-lookup"><span data-stu-id="6122d-134">Schemes are useful as a mechanism for referring to the authentication, challenge, and forbid behaviors of the associated handler.</span></span> <span data-ttu-id="6122d-135">例如，授權原則可以使用配置名稱來指定 (或配置) 應該用來驗證使用者的驗證配置。</span><span class="sxs-lookup"><span data-stu-id="6122d-135">For example, an authorization policy can use scheme names to specify which authentication scheme (or schemes) should be used to authenticate the user.</span></span> <span data-ttu-id="6122d-136">設定驗證時，通常會指定預設的驗證配置。</span><span class="sxs-lookup"><span data-stu-id="6122d-136">When configuring authentication, it's common to specify the default authentication scheme.</span></span> <span data-ttu-id="6122d-137">除非資源要求特定配置，否則會使用預設配置。</span><span class="sxs-lookup"><span data-stu-id="6122d-137">The default scheme is used unless a resource requests a specific scheme.</span></span> <span data-ttu-id="6122d-138">也可以：</span><span class="sxs-lookup"><span data-stu-id="6122d-138">It's also possible to:</span></span>

* <span data-ttu-id="6122d-139">指定要用於驗證、挑戰和禁止動作的不同預設配置。</span><span class="sxs-lookup"><span data-stu-id="6122d-139">Specify different default schemes to use for authenticate, challenge, and forbid actions.</span></span>
* <span data-ttu-id="6122d-140">使用 [原則](xref:security/authentication/policyschemes)配置將多個配置結合成一個。</span><span class="sxs-lookup"><span data-stu-id="6122d-140">Combine multiple schemes into one using [policy schemes](xref:security/authentication/policyschemes).</span></span>

### <a name="authentication-handler"></a><span data-ttu-id="6122d-141">驗證處理常式</span><span class="sxs-lookup"><span data-stu-id="6122d-141">Authentication handler</span></span>

<span data-ttu-id="6122d-142">驗證處理常式：</span><span class="sxs-lookup"><span data-stu-id="6122d-142">An authentication handler:</span></span>

* <span data-ttu-id="6122d-143">是實作為配置行為的型別。</span><span class="sxs-lookup"><span data-stu-id="6122d-143">Is a type that implements the behavior of a scheme.</span></span>
* <span data-ttu-id="6122d-144">衍生自 <xref:Microsoft.AspNetCore.Authentication.IAuthenticationHandler> 或 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> 。</span><span class="sxs-lookup"><span data-stu-id="6122d-144">Is derived from <xref:Microsoft.AspNetCore.Authentication.IAuthenticationHandler> or <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span>
* <span data-ttu-id="6122d-145">擁有驗證使用者的主要責任。</span><span class="sxs-lookup"><span data-stu-id="6122d-145">Has the primary responsibility to authenticate users.</span></span>

<span data-ttu-id="6122d-146">根據驗證配置的設定和傳入的要求內容，驗證處理常式：</span><span class="sxs-lookup"><span data-stu-id="6122d-146">Based on the authentication scheme's configuration and the incoming request context, authentication handlers:</span></span>

* <span data-ttu-id="6122d-147"><xref:Microsoft.AspNetCore.Authentication.AuthenticationTicket>如果驗證成功，則表示使用者身分識別的結構物件。</span><span class="sxs-lookup"><span data-stu-id="6122d-147">Construct <xref:Microsoft.AspNetCore.Authentication.AuthenticationTicket> objects representing the user's identity if authentication is successful.</span></span>
* <span data-ttu-id="6122d-148">如果驗證失敗，則傳回「沒有結果」或「失敗」。</span><span class="sxs-lookup"><span data-stu-id="6122d-148">Return 'no result' or 'failure' if authentication is unsuccessful.</span></span>
* <span data-ttu-id="6122d-149">在使用者嘗試存取資源時，有挑戰和禁止動作的方法：</span><span class="sxs-lookup"><span data-stu-id="6122d-149">Have methods for challenge and forbid actions for when users attempt to access resources:</span></span>
  * <span data-ttu-id="6122d-150">他們未獲授權，無法存取 (禁止) 。</span><span class="sxs-lookup"><span data-stu-id="6122d-150">They are unauthorized to access (forbid).</span></span>
  * <span data-ttu-id="6122d-151">未經驗證 (挑戰) 。</span><span class="sxs-lookup"><span data-stu-id="6122d-151">When they are unauthenticated (challenge).</span></span>

### <a name="authenticate"></a><span data-ttu-id="6122d-152">Authenticate</span><span class="sxs-lookup"><span data-stu-id="6122d-152">Authenticate</span></span>

<span data-ttu-id="6122d-153">驗證配置的驗證動作會負責根據要求內容來建立使用者的身分識別。</span><span class="sxs-lookup"><span data-stu-id="6122d-153">An authentication scheme's authenticate action is responsible for constructing the user's identity based on request context.</span></span> <span data-ttu-id="6122d-154">它會傳回 <xref:Microsoft.AspNetCore.Authentication.AuthenticateResult> ，指出驗證是否成功，以及使用者在驗證票證中的身分識別。</span><span class="sxs-lookup"><span data-stu-id="6122d-154">It returns an <xref:Microsoft.AspNetCore.Authentication.AuthenticateResult> indicating whether authentication was successful and, if so, the user's identity in an authentication ticket.</span></span> <span data-ttu-id="6122d-155">請參閱 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync%2A>。</span><span class="sxs-lookup"><span data-stu-id="6122d-155">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync%2A>.</span></span> <span data-ttu-id="6122d-156">驗證範例包括：</span><span class="sxs-lookup"><span data-stu-id="6122d-156">Authenticate examples include:</span></span>

* <span data-ttu-id="6122d-157">cookie從建立使用者身分識別的驗證配置 cookie 。</span><span class="sxs-lookup"><span data-stu-id="6122d-157">A cookie authentication scheme constructing the user's identity from cookies.</span></span>
* <span data-ttu-id="6122d-158">JWT 持有人配置還原序列化和驗證 JWT 持有人權杖，以建立使用者的身分識別。</span><span class="sxs-lookup"><span data-stu-id="6122d-158">A JWT bearer scheme deserializing and validating a JWT bearer token to construct the user's identity.</span></span>

### <a name="challenge"></a><span data-ttu-id="6122d-159">挑戰</span><span class="sxs-lookup"><span data-stu-id="6122d-159">Challenge</span></span>

<span data-ttu-id="6122d-160">當未驗證的使用者要求需要驗證的端點時，會在授權中叫用驗證挑戰。</span><span class="sxs-lookup"><span data-stu-id="6122d-160">An authentication challenge is invoked by Authorization when an unauthenticated user requests an endpoint that requires authentication.</span></span> <span data-ttu-id="6122d-161">例如，當匿名使用者要求受限的資源，或按一下登入連結時，就會發出驗證挑戰。</span><span class="sxs-lookup"><span data-stu-id="6122d-161">An authentication challenge is issued, for example, when an anonymous user requests a restricted resource or clicks on a login link.</span></span> <span data-ttu-id="6122d-162">授權會使用指定的驗證配置 (s) 來叫用挑戰，如果未指定，則會使用預設值。</span><span class="sxs-lookup"><span data-stu-id="6122d-162">Authorization invokes a challenge using the specified authentication scheme(s), or the default if none is specified.</span></span> <span data-ttu-id="6122d-163">請參閱 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync%2A>。</span><span class="sxs-lookup"><span data-stu-id="6122d-163">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync%2A>.</span></span> <span data-ttu-id="6122d-164">驗證挑戰範例包括：</span><span class="sxs-lookup"><span data-stu-id="6122d-164">Authentication challenge examples include:</span></span>

* <span data-ttu-id="6122d-165">將 cookie 使用者重新導向至登入頁面的驗證配置。</span><span class="sxs-lookup"><span data-stu-id="6122d-165">A cookie authentication scheme redirecting the user to a login page.</span></span>
* <span data-ttu-id="6122d-166">JWT 持有人配置傳回401結果與 `www-authenticate: bearer` 標頭。</span><span class="sxs-lookup"><span data-stu-id="6122d-166">A JWT bearer scheme returning a 401 result with a `www-authenticate: bearer` header.</span></span>

<span data-ttu-id="6122d-167">挑戰動作應該讓使用者知道要使用哪個驗證機制來存取要求的資源。</span><span class="sxs-lookup"><span data-stu-id="6122d-167">A challenge action should let the user know what authentication mechanism to use to access the requested resource.</span></span>

### <a name="forbid"></a><span data-ttu-id="6122d-168">禁止</span><span class="sxs-lookup"><span data-stu-id="6122d-168">Forbid</span></span>

<span data-ttu-id="6122d-169">當已驗證的使用者嘗試存取不允許存取的資源時，授權會呼叫驗證配置的禁止動作。</span><span class="sxs-lookup"><span data-stu-id="6122d-169">An authentication scheme's forbid action is called by Authorization when an authenticated user attempts to access a resource they are not permitted to access.</span></span> <span data-ttu-id="6122d-170">請參閱 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync%2A>。</span><span class="sxs-lookup"><span data-stu-id="6122d-170">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync%2A>.</span></span> <span data-ttu-id="6122d-171">驗證禁止的範例包括：</span><span class="sxs-lookup"><span data-stu-id="6122d-171">Authentication forbid examples include:</span></span>
* <span data-ttu-id="6122d-172">將 cookie 使用者重新導向至表示禁止存取之頁面的驗證配置。</span><span class="sxs-lookup"><span data-stu-id="6122d-172">A cookie authentication scheme redirecting the user to a page indicating access was forbidden.</span></span>
* <span data-ttu-id="6122d-173">JWT 持有人配置傳回403結果。</span><span class="sxs-lookup"><span data-stu-id="6122d-173">A JWT bearer scheme returning a 403 result.</span></span>
* <span data-ttu-id="6122d-174">重新導向至使用者可要求存取資源之頁面的自訂驗證配置。</span><span class="sxs-lookup"><span data-stu-id="6122d-174">A custom authentication scheme redirecting to a page where the user can request access to the resource.</span></span>

<span data-ttu-id="6122d-175">禁止的動作可讓使用者知道：</span><span class="sxs-lookup"><span data-stu-id="6122d-175">A forbid action can let the user know:</span></span>

* <span data-ttu-id="6122d-176">它們是經過驗證的。</span><span class="sxs-lookup"><span data-stu-id="6122d-176">They are authenticated.</span></span>
* <span data-ttu-id="6122d-177">他們不允許存取要求的資源。</span><span class="sxs-lookup"><span data-stu-id="6122d-177">They aren't permitted to access the requested resource.</span></span>

<span data-ttu-id="6122d-178">請參閱下列連結以取得挑戰和禁止的差異：</span><span class="sxs-lookup"><span data-stu-id="6122d-178">See the following links for differences between challenge and forbid:</span></span>

* <span data-ttu-id="6122d-179">[使用操作資源處理常式的挑戰和禁止](xref:security/authorization/resourcebased#challenge-and-forbid-with-an-operational-resource-handler)。</span><span class="sxs-lookup"><span data-stu-id="6122d-179">[Challenge and forbid with an operational resource handler](xref:security/authorization/resourcebased#challenge-and-forbid-with-an-operational-resource-handler).</span></span>
* <span data-ttu-id="6122d-180">[挑戰與禁止之間的差異](xref:security/authorization/secure-data#challenge)。</span><span class="sxs-lookup"><span data-stu-id="6122d-180">[Differences between challenge and forbid](xref:security/authorization/secure-data#challenge).</span></span>

## <a name="authentication-providers-per-tenant"></a><span data-ttu-id="6122d-181">每個租使用者的驗證提供者</span><span class="sxs-lookup"><span data-stu-id="6122d-181">Authentication providers per tenant</span></span>

<span data-ttu-id="6122d-182">ASP.NET Core framework 沒有內建解決方案可進行多租使用者驗證。</span><span class="sxs-lookup"><span data-stu-id="6122d-182">ASP.NET Core framework does not have a built-in solution for multi-tenant authentication.</span></span>
<span data-ttu-id="6122d-183">雖然客戶當然可以使用內建功能來撰寫，但我們建議客戶基於此目的來查看 [Orchard Core](https://www.orchardcore.net/) 。</span><span class="sxs-lookup"><span data-stu-id="6122d-183">While it's certainly possible for customers to write one, using the built-in features, we recommend customers to look into [Orchard Core](https://www.orchardcore.net/) for this purpose.</span></span>

<span data-ttu-id="6122d-184">Orchard Core 為：</span><span class="sxs-lookup"><span data-stu-id="6122d-184">Orchard Core is:</span></span>

* <span data-ttu-id="6122d-185">以 ASP.NET Core 為基礎的開放原始碼模組化和多租使用者應用程式架構。</span><span class="sxs-lookup"><span data-stu-id="6122d-185">An open-source modular and multi-tenant app framework built with ASP.NET Core.</span></span>
* <span data-ttu-id="6122d-186">內容管理系統 (CMS) 以該應用程式架構為基礎。</span><span class="sxs-lookup"><span data-stu-id="6122d-186">A content management system (CMS) built on top of that app framework.</span></span>

<span data-ttu-id="6122d-187">如需每個租使用者的驗證提供者範例，請參閱 [Orchard Core](https://github.com/OrchardCMS/OrchardCore) 來源。</span><span class="sxs-lookup"><span data-stu-id="6122d-187">See the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) source for an example of authentication providers per tenant.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6122d-188">其他資源</span><span class="sxs-lookup"><span data-stu-id="6122d-188">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authentication/policyschemes>
* <xref:security/authorization/secure-data>
* [<span data-ttu-id="6122d-189">全球需要經過驗證的使用者</span><span class="sxs-lookup"><span data-stu-id="6122d-189">Globally require authenticated users</span></span>](xref:security/authorization/secure-data#rau)