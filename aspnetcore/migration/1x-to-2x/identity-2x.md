---
title: '將驗證遷移 :::no-loc(Identity)::: 至 ASP.NET Core 2。0'
author: scottaddie
description: '本文概述遷移 ASP.NET Core 1.x 驗證和 ASP.NET Core 2.0 的最常見步驟 :::no-loc(Identity)::: 。'
ms.author: scaddie
ms.date: 06/21/2019
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
uid: migration/1x-to-2x/identity-2x
ms.openlocfilehash: cad7582670013661f5fcbfbebad923f0f092462e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057176"
---
# <a name="migrate-authentication-and-no-locidentity-to-aspnet-core-20"></a><span data-ttu-id="e425d-103">將驗證遷移 :::no-loc(Identity)::: 至 ASP.NET Core 2。0</span><span class="sxs-lookup"><span data-stu-id="e425d-103">Migrate authentication and :::no-loc(Identity)::: to ASP.NET Core 2.0</span></span>

<span data-ttu-id="e425d-104">由 [Scott Addie](https://github.com/scottaddie) 和 [Hao Kung](https://github.com/HaoK)</span><span class="sxs-lookup"><span data-stu-id="e425d-104">By [Scott Addie](https://github.com/scottaddie) and [Hao Kung](https://github.com/HaoK)</span></span>

<span data-ttu-id="e425d-105">ASP.NET Core 2.0 有新的驗證模型， [:::no-loc(Identity):::](xref:security/authentication/identity) 可使用服務簡化設定。</span><span class="sxs-lookup"><span data-stu-id="e425d-105">ASP.NET Core 2.0 has a new model for authentication and [:::no-loc(Identity):::](xref:security/authentication/identity) that simplifies configuration by using services.</span></span> <span data-ttu-id="e425d-106">使用驗證的 ASP.NET Core 1.x 應用程式，或 :::no-loc(Identity)::: 可以更新為使用新的模型，如下所述。</span><span class="sxs-lookup"><span data-stu-id="e425d-106">ASP.NET Core 1.x applications that use authentication or :::no-loc(Identity)::: can be updated to use the new model as outlined below.</span></span>

## <a name="update-namespaces"></a><span data-ttu-id="e425d-107">更新命名空間</span><span class="sxs-lookup"><span data-stu-id="e425d-107">Update namespaces</span></span>

<span data-ttu-id="e425d-108">在1.x 中，在 `:::no-loc(Identity):::Role` `:::no-loc(Identity):::User` 命名空間中找到類別，例如和 `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore` 。</span><span class="sxs-lookup"><span data-stu-id="e425d-108">In 1.x, classes such `:::no-loc(Identity):::Role` and `:::no-loc(Identity):::User` were found in the `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore` namespace.</span></span>

<span data-ttu-id="e425d-109">在2.0 中， <xref:Microsoft.AspNetCore.:::no-loc(Identity):::> 命名空間變成了許多這類類別的新家庭。</span><span class="sxs-lookup"><span data-stu-id="e425d-109">In 2.0, the <xref:Microsoft.AspNetCore.:::no-loc(Identity):::> namespace became the new home for several of such classes.</span></span> <span data-ttu-id="e425d-110">使用預設程式 :::no-loc(Identity)::: 代碼時，受影響的類別包括 `ApplicationUser` 和 `Startup` 。</span><span class="sxs-lookup"><span data-stu-id="e425d-110">With the default :::no-loc(Identity)::: code, affected classes include `ApplicationUser` and `Startup`.</span></span> <span data-ttu-id="e425d-111">調整您 `using` 的語句，以解決受影響的參考。</span><span class="sxs-lookup"><span data-stu-id="e425d-111">Adjust your `using` statements to resolve the affected references.</span></span>

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a><span data-ttu-id="e425d-112">驗證中介軟體和服務</span><span class="sxs-lookup"><span data-stu-id="e425d-112">Authentication Middleware and services</span></span>

<span data-ttu-id="e425d-113">在1.x 專案中，驗證是透過中介軟體來設定。</span><span class="sxs-lookup"><span data-stu-id="e425d-113">In 1.x projects, authentication is configured via middleware.</span></span> <span data-ttu-id="e425d-114">系統會針對您想要支援的每個驗證配置叫用中介軟體方法。</span><span class="sxs-lookup"><span data-stu-id="e425d-114">A middleware method is invoked for each authentication scheme you want to support.</span></span>

<span data-ttu-id="e425d-115">下列1.x 範例會 :::no-loc(Identity)::: 在 *Startup.cs* 中設定 Facebook 驗證：</span><span class="sxs-lookup"><span data-stu-id="e425d-115">The following 1.x example configures Facebook authentication with :::no-loc(Identity)::: in *Startup.cs* :</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Add:::no-loc(Identity):::<ApplicationUser, :::no-loc(Identity):::Role>()
            .AddEntityFrameworkStores<ApplicationDbContext>();
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory)
{
    app.Use:::no-loc(Identity):::();
    app.UseFacebookAuthentication(new FacebookOptions {
        AppId = Configuration["auth:facebook:appid"],
        AppSecret = Configuration["auth:facebook:appsecret"]
    });
}
```

<span data-ttu-id="e425d-116">在2.0 專案中，驗證是透過服務來設定。</span><span class="sxs-lookup"><span data-stu-id="e425d-116">In 2.0 projects, authentication is configured via services.</span></span> <span data-ttu-id="e425d-117">每個驗證配置都是在 `ConfigureServices` *Startup.cs* 方法中註冊。</span><span class="sxs-lookup"><span data-stu-id="e425d-117">Each authentication scheme is registered in the `ConfigureServices` method of *Startup.cs* .</span></span> <span data-ttu-id="e425d-118">`Use:::no-loc(Identity):::`方法會被取代為 `UseAuthentication` 。</span><span class="sxs-lookup"><span data-stu-id="e425d-118">The `Use:::no-loc(Identity):::` method is replaced with `UseAuthentication`.</span></span>

<span data-ttu-id="e425d-119">下列2.0 範例會 :::no-loc(Identity)::: 在 *Startup.cs* 中設定 Facebook 驗證：</span><span class="sxs-lookup"><span data-stu-id="e425d-119">The following 2.0 example configures Facebook authentication with :::no-loc(Identity)::: in *Startup.cs* :</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Add:::no-loc(Identity):::<ApplicationUser, :::no-loc(Identity):::Role>()
            .AddEntityFrameworkStores<ApplicationDbContext>();

    // If you want to tweak :::no-loc(Identity)::: :::no-loc(cookie):::s, they're no longer part of :::no-loc(Identity):::Options.
    services.ConfigureApplication:::no-loc(Cookie):::(options => options.LoginPath = "/Account/LogIn");
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory) {
    app.UseAuthentication();
}
```

<span data-ttu-id="e425d-120">`UseAuthentication`方法會新增單一驗證中介軟體元件，負責自動驗證和處理遠端驗證要求。</span><span class="sxs-lookup"><span data-stu-id="e425d-120">The `UseAuthentication` method adds a single authentication middleware component, which is responsible for automatic authentication and the handling of remote authentication requests.</span></span> <span data-ttu-id="e425d-121">它會將所有個別中介軟體元件取代為單一的一般中介軟體元件。</span><span class="sxs-lookup"><span data-stu-id="e425d-121">It replaces all of the individual middleware components with a single, common middleware component.</span></span>

<span data-ttu-id="e425d-122">以下是每個主要驗證配置的2.0 遷移指示。</span><span class="sxs-lookup"><span data-stu-id="e425d-122">Below are 2.0 migration instructions for each major authentication scheme.</span></span>

### <a name="no-loccookie-based-authentication"></a><span data-ttu-id="e425d-123">:::no-loc(Cookie):::以驗證為基礎</span><span class="sxs-lookup"><span data-stu-id="e425d-123">:::no-loc(Cookie):::-based authentication</span></span>

<span data-ttu-id="e425d-124">選取下列兩個選項的其中一個，並在 *Startup.cs* 中進行必要的變更：</span><span class="sxs-lookup"><span data-stu-id="e425d-124">Select one of the two options below, and make the necessary changes in *Startup.cs* :</span></span>

1. <span data-ttu-id="e425d-125">使用 :::no-loc(cookie)::::::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="e425d-125">Use :::no-loc(cookie):::s with :::no-loc(Identity):::</span></span>
    - <span data-ttu-id="e425d-126">以 `Use:::no-loc(Identity):::` `UseAuthentication` 方法中的取代 `Configure` ：</span><span class="sxs-lookup"><span data-stu-id="e425d-126">Replace `Use:::no-loc(Identity):::` with `UseAuthentication` in the `Configure` method:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="e425d-127">`Add:::no-loc(Identity):::`在方法中叫用方法 `ConfigureServices` ，以加入 :::no-loc(cookie)::: 驗證服務。</span><span class="sxs-lookup"><span data-stu-id="e425d-127">Invoke the `Add:::no-loc(Identity):::` method in the `ConfigureServices` method to add the :::no-loc(cookie)::: authentication services.</span></span>
    - <span data-ttu-id="e425d-128">（選擇性） `ConfigureApplication:::no-loc(Cookie):::` `ConfigureExternal:::no-loc(Cookie):::` 在方法中叫用或方法 `ConfigureServices` 來調整 :::no-loc(Identity)::: :::no-loc(cookie)::: 設定。</span><span class="sxs-lookup"><span data-stu-id="e425d-128">Optionally, invoke the `ConfigureApplication:::no-loc(Cookie):::` or `ConfigureExternal:::no-loc(Cookie):::` method in the `ConfigureServices` method to tweak the :::no-loc(Identity)::: :::no-loc(cookie)::: settings.</span></span>

        ```csharp
        services.Add:::no-loc(Identity):::<ApplicationUser, :::no-loc(Identity):::Role>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();

        services.ConfigureApplication:::no-loc(Cookie):::(options => options.LoginPath = "/Account/LogIn");
        ```

2. <span data-ttu-id="e425d-129">使用 :::no-loc(cookie)::: s （不含） :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="e425d-129">Use :::no-loc(cookie):::s without :::no-loc(Identity):::</span></span>
    - <span data-ttu-id="e425d-130">`Use:::no-loc(Cookie):::Authentication` `Configure` 以下列內容取代方法中的方法呼叫 `UseAuthentication` ：</span><span class="sxs-lookup"><span data-stu-id="e425d-130">Replace the `Use:::no-loc(Cookie):::Authentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="e425d-131">叫 `AddAuthentication` `Add:::no-loc(Cookie):::` 用方法中的和方法 `ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="e425d-131">Invoke the `AddAuthentication` and `Add:::no-loc(Cookie):::` methods in the `ConfigureServices` method:</span></span>

        ```csharp
        // If you don't want the :::no-loc(cookie)::: to be automatically authenticated and assigned to HttpContext.User,
        // remove the :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme parameter passed to AddAuthentication.
        services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
                .Add:::no-loc(Cookie):::(options =>
                {
                    options.LoginPath = "/Account/LogIn";
                    options.LogoutPath = "/Account/LogOff";
                });
        ```

### <a name="jwt-bearer-authentication"></a><span data-ttu-id="e425d-132">JWT 持有人驗證</span><span class="sxs-lookup"><span data-stu-id="e425d-132">JWT Bearer Authentication</span></span>

<span data-ttu-id="e425d-133">在 *Startup.cs* 中進行下列變更：</span><span class="sxs-lookup"><span data-stu-id="e425d-133">Make the following changes in *Startup.cs* :</span></span>
- <span data-ttu-id="e425d-134">`UseJwtBearerAuthentication` `Configure` 以下列內容取代方法中的方法呼叫 `UseAuthentication` ：</span><span class="sxs-lookup"><span data-stu-id="e425d-134">Replace the `UseJwtBearerAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="e425d-135">`AddJwtBearer`在方法中叫用方法 `ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="e425d-135">Invoke the `AddJwtBearer` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options =>
            {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    <span data-ttu-id="e425d-136">此程式碼片段不會使用 :::no-loc(Identity)::: ，因此應該透過傳遞 `JwtBearerDefaults.AuthenticationScheme` 給方法來設定預設配置 `AddAuthentication` 。</span><span class="sxs-lookup"><span data-stu-id="e425d-136">This code snippet doesn't use :::no-loc(Identity):::, so the default scheme should be set by passing `JwtBearerDefaults.AuthenticationScheme` to the `AddAuthentication` method.</span></span>

### <a name="openid-connect-oidc-authentication"></a><span data-ttu-id="e425d-137">OpenID Connect (OIDC) 驗證</span><span class="sxs-lookup"><span data-stu-id="e425d-137">OpenID Connect (OIDC) authentication</span></span>

<span data-ttu-id="e425d-138">在 *Startup.cs* 中進行下列變更：</span><span class="sxs-lookup"><span data-stu-id="e425d-138">Make the following changes in *Startup.cs* :</span></span>

- <span data-ttu-id="e425d-139">`UseOpenIdConnectAuthentication` `Configure` 以下列內容取代方法中的方法呼叫 `UseAuthentication` ：</span><span class="sxs-lookup"><span data-stu-id="e425d-139">Replace the `UseOpenIdConnectAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="e425d-140">`AddOpenIdConnect`在方法中叫用方法 `ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="e425d-140">Invoke the `AddOpenIdConnect` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication(options =>
    {
        options.DefaultScheme = :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
    })
    .Add:::no-loc(Cookie):::()
    .AddOpenIdConnect(options =>
    {
        options.Authority = Configuration["auth:oidc:authority"];
        options.ClientId = Configuration["auth:oidc:clientid"];
    });
    ```

- <span data-ttu-id="e425d-141">`PostLogoutRedirectUri` `OpenIdConnectOptions` 以下列內容取代動作中的屬性 `SignedOutRedirectUri` ：</span><span class="sxs-lookup"><span data-stu-id="e425d-141">Replace the `PostLogoutRedirectUri` property in the `OpenIdConnectOptions` action with `SignedOutRedirectUri`:</span></span>

    ```csharp
    .AddOpenIdConnect(options =>
    {
        options.SignedOutRedirectUri = "https://contoso.com";
    });
    ```
    
### <a name="facebook-authentication"></a><span data-ttu-id="e425d-142">Facebook 驗證</span><span class="sxs-lookup"><span data-stu-id="e425d-142">Facebook authentication</span></span>

<span data-ttu-id="e425d-143">在 *Startup.cs* 中進行下列變更：</span><span class="sxs-lookup"><span data-stu-id="e425d-143">Make the following changes in *Startup.cs* :</span></span>
- <span data-ttu-id="e425d-144">`UseFacebookAuthentication` `Configure` 以下列內容取代方法中的方法呼叫 `UseAuthentication` ：</span><span class="sxs-lookup"><span data-stu-id="e425d-144">Replace the `UseFacebookAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="e425d-145">`AddFacebook`在方法中叫用方法 `ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="e425d-145">Invoke the `AddFacebook` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a><span data-ttu-id="e425d-146">Google 驗證</span><span class="sxs-lookup"><span data-stu-id="e425d-146">Google authentication</span></span>

<span data-ttu-id="e425d-147">在 *Startup.cs* 中進行下列變更：</span><span class="sxs-lookup"><span data-stu-id="e425d-147">Make the following changes in *Startup.cs* :</span></span>
- <span data-ttu-id="e425d-148">`UseGoogleAuthentication` `Configure` 以下列內容取代方法中的方法呼叫 `UseAuthentication` ：</span><span class="sxs-lookup"><span data-stu-id="e425d-148">Replace the `UseGoogleAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="e425d-149">`AddGoogle`在方法中叫用方法 `ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="e425d-149">Invoke the `AddGoogle` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options =>
            {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });
    ```

### <a name="microsoft-account-authentication"></a><span data-ttu-id="e425d-150">Microsoft 帳戶驗證</span><span class="sxs-lookup"><span data-stu-id="e425d-150">Microsoft Account authentication</span></span>

<span data-ttu-id="e425d-151">如需 Microsoft 帳戶驗證的詳細資訊，請參閱 [此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/14455)。</span><span class="sxs-lookup"><span data-stu-id="e425d-151">For more information on Microsoft account authentication, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/14455).</span></span>

<span data-ttu-id="e425d-152">在 *Startup.cs* 中進行下列變更：</span><span class="sxs-lookup"><span data-stu-id="e425d-152">Make the following changes in *Startup.cs* :</span></span>
- <span data-ttu-id="e425d-153">`UseMicrosoftAccountAuthentication` `Configure` 以下列內容取代方法中的方法呼叫 `UseAuthentication` ：</span><span class="sxs-lookup"><span data-stu-id="e425d-153">Replace the `UseMicrosoftAccountAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="e425d-154">`AddMicrosoftAccount`在方法中叫用方法 `ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="e425d-154">Invoke the `AddMicrosoftAccount` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options =>
            {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ```

### <a name="twitter-authentication"></a><span data-ttu-id="e425d-155">Twitter 驗證</span><span class="sxs-lookup"><span data-stu-id="e425d-155">Twitter authentication</span></span>

<span data-ttu-id="e425d-156">在 *Startup.cs* 中進行下列變更：</span><span class="sxs-lookup"><span data-stu-id="e425d-156">Make the following changes in *Startup.cs* :</span></span>
- <span data-ttu-id="e425d-157">`UseTwitterAuthentication` `Configure` 以下列內容取代方法中的方法呼叫 `UseAuthentication` ：</span><span class="sxs-lookup"><span data-stu-id="e425d-157">Replace the `UseTwitterAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="e425d-158">`AddTwitter`在方法中叫用方法 `ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="e425d-158">Invoke the `AddTwitter` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options =>
            {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a><span data-ttu-id="e425d-159">設定預設驗證配置</span><span class="sxs-lookup"><span data-stu-id="e425d-159">Setting default authentication schemes</span></span>

<span data-ttu-id="e425d-160">在1.x 中， `AutomaticAuthenticate` `AutomaticChallenge` [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) 基類的和屬性必須在單一驗證配置上進行設定。</span><span class="sxs-lookup"><span data-stu-id="e425d-160">In 1.x, the `AutomaticAuthenticate` and `AutomaticChallenge` properties of the [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) base class were intended to be set on a single authentication scheme.</span></span> <span data-ttu-id="e425d-161">沒有足夠的方法可以強制執行此作業。</span><span class="sxs-lookup"><span data-stu-id="e425d-161">There was no good way to enforce this.</span></span>

<span data-ttu-id="e425d-162">在2.0 中，已將這兩個屬性移除為個別實例上的屬性 `AuthenticationOptions` 。</span><span class="sxs-lookup"><span data-stu-id="e425d-162">In 2.0, these two properties have been removed as properties on the individual `AuthenticationOptions` instance.</span></span> <span data-ttu-id="e425d-163">您可以在 Startup.cs 方法中的方法呼叫中設定它們 `AddAuthentication` `ConfigureServices` ： *Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="e425d-163">They can be configured in the `AddAuthentication` method call within the `ConfigureServices` method of *Startup.cs* :</span></span>

```csharp
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme);
```

<span data-ttu-id="e425d-164">在上述程式碼片段中，預設配置是設定為 `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` ( " :::no-loc(Cookie)::: s" ) 。</span><span class="sxs-lookup"><span data-stu-id="e425d-164">In the preceding code snippet, the default scheme is set to `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (":::no-loc(Cookie):::s").</span></span>

<span data-ttu-id="e425d-165">或者，您也可以使用方法的 `AddAuthentication` 多載版本來設定一個以上的屬性。</span><span class="sxs-lookup"><span data-stu-id="e425d-165">Alternatively, use an overloaded version of the `AddAuthentication` method to set more than one property.</span></span> <span data-ttu-id="e425d-166">在下列多載的方法範例中，預設配置是設定為 `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` 。</span><span class="sxs-lookup"><span data-stu-id="e425d-166">In the following overloaded method example, the default scheme is set to `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="e425d-167">您也可以在個別的 `[Authorize]` 屬性或授權原則中指定驗證配置。</span><span class="sxs-lookup"><span data-stu-id="e425d-167">The authentication scheme may alternatively be specified within your individual `[Authorize]` attributes or authorization policies.</span></span>

```csharp
services.AddAuthentication(options =>
{
    options.DefaultScheme = :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

<span data-ttu-id="e425d-168">如果下列其中一個條件成立，請在2.0 中定義預設配置：</span><span class="sxs-lookup"><span data-stu-id="e425d-168">Define a default scheme in 2.0 if one of the following conditions is true:</span></span>
- <span data-ttu-id="e425d-169">您希望使用者自動登入</span><span class="sxs-lookup"><span data-stu-id="e425d-169">You want the user to be automatically signed in</span></span>
- <span data-ttu-id="e425d-170">您可以使用 `[Authorize]` 屬性或授權原則，而不需要指定配置</span><span class="sxs-lookup"><span data-stu-id="e425d-170">You use the `[Authorize]` attribute or authorization policies without specifying schemes</span></span>

<span data-ttu-id="e425d-171">這項規則的例外狀況是 `Add:::no-loc(Identity):::` 方法。</span><span class="sxs-lookup"><span data-stu-id="e425d-171">An exception to this rule is the `Add:::no-loc(Identity):::` method.</span></span> <span data-ttu-id="e425d-172">這個方法 :::no-loc(cookie)::: 會為您新增，並將預設的驗證和挑戰配置設定為應用程式 :::no-loc(cookie)::: `:::no-loc(Identity):::Constants.ApplicationScheme` 。</span><span class="sxs-lookup"><span data-stu-id="e425d-172">This method adds :::no-loc(cookie):::s for you and sets the default authenticate and challenge schemes to the application :::no-loc(cookie)::: `:::no-loc(Identity):::Constants.ApplicationScheme`.</span></span> <span data-ttu-id="e425d-173">此外，它也會將預設的登入配置設定為外部 :::no-loc(cookie)::: `:::no-loc(Identity):::Constants.ExternalScheme` 。</span><span class="sxs-lookup"><span data-stu-id="e425d-173">Additionally, it sets the default sign-in scheme to the external :::no-loc(cookie)::: `:::no-loc(Identity):::Constants.ExternalScheme`.</span></span>

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a><span data-ttu-id="e425d-174">使用 HttpCoNtext 驗證延伸模組</span><span class="sxs-lookup"><span data-stu-id="e425d-174">Use HttpContext authentication extensions</span></span>

<span data-ttu-id="e425d-175">`IAuthenticationManager`介面是 1. x 驗證系統的主要進入點。</span><span class="sxs-lookup"><span data-stu-id="e425d-175">The `IAuthenticationManager` interface is the main entry point into the 1.x authentication system.</span></span> <span data-ttu-id="e425d-176">它已取代為命名空間中的一組新 `HttpContext` 擴充方法 `Microsoft.AspNetCore.Authentication` 。</span><span class="sxs-lookup"><span data-stu-id="e425d-176">It has been replaced with a new set of `HttpContext` extension methods in the `Microsoft.AspNetCore.Authentication` namespace.</span></span>

<span data-ttu-id="e425d-177">例如，1.x 專案會參考 `Authentication` 屬性：</span><span class="sxs-lookup"><span data-stu-id="e425d-177">For example, 1.x projects reference an `Authentication` property:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="e425d-178">在2.0 專案中，匯入 `Microsoft.AspNetCore.Authentication` 命名空間，並刪除 `Authentication` 屬性參考：</span><span class="sxs-lookup"><span data-stu-id="e425d-178">In 2.0 projects, import the `Microsoft.AspNetCore.Authentication` namespace, and delete the `Authentication` property references:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a><span data-ttu-id="e425d-179">Windows 驗證 ( # A0/IISIntegration) </span><span class="sxs-lookup"><span data-stu-id="e425d-179">Windows Authentication (HTTP.sys / IISIntegration)</span></span>

<span data-ttu-id="e425d-180">Windows 驗證的變化有兩種：</span><span class="sxs-lookup"><span data-stu-id="e425d-180">There are two variations of Windows authentication:</span></span>

* <span data-ttu-id="e425d-181">主機只允許已驗證的使用者。</span><span class="sxs-lookup"><span data-stu-id="e425d-181">The host only allows authenticated users.</span></span> <span data-ttu-id="e425d-182">此差異不受2.0 變更影響。</span><span class="sxs-lookup"><span data-stu-id="e425d-182">This variation isn't affected by the 2.0 changes.</span></span>
* <span data-ttu-id="e425d-183">主機允許匿名和已驗證的使用者。</span><span class="sxs-lookup"><span data-stu-id="e425d-183">The host allows both anonymous and authenticated users.</span></span> <span data-ttu-id="e425d-184">這種變化會受到2.0 變更所影響。</span><span class="sxs-lookup"><span data-stu-id="e425d-184">This variation is affected by the 2.0 changes.</span></span> <span data-ttu-id="e425d-185">例如，應用程式應該允許 [IIS](xref:host-and-deploy/iis/index) 或 [HTTP.sys](xref:fundamentals/servers/httpsys) 層的匿名使用者，但在控制器層級授權使用者。</span><span class="sxs-lookup"><span data-stu-id="e425d-185">For example, the app should allow anonymous users at the [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys) layer but authorize users at the controller level.</span></span> <span data-ttu-id="e425d-186">在此案例中，請設定方法中的預設配置 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="e425d-186">In this scenario, set the default scheme in the `Startup.ConfigureServices` method.</span></span>

  <span data-ttu-id="e425d-187">若為 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/)，請將預設配置設定為 `IISDefaults.AuthenticationScheme` ：</span><span class="sxs-lookup"><span data-stu-id="e425d-187">For [Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/), set the default scheme to `IISDefaults.AuthenticationScheme`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Server.IISIntegration;

  services.AddAuthentication(IISDefaults.AuthenticationScheme);
  ```

  <span data-ttu-id="e425d-188">若為 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)，請將預設配置設定為 `HttpSysDefaults.AuthenticationScheme` ：</span><span class="sxs-lookup"><span data-stu-id="e425d-188">For [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/), set the default scheme to `HttpSysDefaults.AuthenticationScheme`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Server.HttpSys;

  services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
  ```

  <span data-ttu-id="e425d-189">無法設定預設配置，可防止授權 (挑戰) 要求使用下列例外狀況：</span><span class="sxs-lookup"><span data-stu-id="e425d-189">Failure to set the default scheme prevents the authorize (challenge) request from working with the following exception:</span></span>

  > <span data-ttu-id="e425d-190">`System.InvalidOperationException`：未指定 authenticationScheme，而且找不到 DefaultChallengeScheme。</span><span class="sxs-lookup"><span data-stu-id="e425d-190">`System.InvalidOperationException`: No authenticationScheme was specified, and there was no DefaultChallengeScheme found.</span></span>

<span data-ttu-id="e425d-191">如需詳細資訊，請參閱<xref:security/authentication/windowsauth>。</span><span class="sxs-lookup"><span data-stu-id="e425d-191">For more information, see <xref:security/authentication/windowsauth>.</span></span>

<a name="identity-:::no-loc(cookie):::-options"></a>

## <a name="no-locidentityno-loccookieoptions-instances"></a><span data-ttu-id="e425d-192">:::no-loc(Identity)::::::no-loc(Cookie):::選項實例</span><span class="sxs-lookup"><span data-stu-id="e425d-192">:::no-loc(Identity)::::::no-loc(Cookie):::Options instances</span></span>

<span data-ttu-id="e425d-193">2.0 變更的副作用是切換為使用命名選項，而不是 :::no-loc(cookie)::: 選項實例。</span><span class="sxs-lookup"><span data-stu-id="e425d-193">A side effect of the 2.0 changes is the switch to using named options instead of :::no-loc(cookie)::: options instances.</span></span> <span data-ttu-id="e425d-194">已移除自訂 :::no-loc(Identity)::: :::no-loc(cookie)::: 配置名稱的功能。</span><span class="sxs-lookup"><span data-stu-id="e425d-194">The ability to customize the :::no-loc(Identity)::: :::no-loc(cookie)::: scheme names is removed.</span></span>

<span data-ttu-id="e425d-195">例如，1.x 專案使用「函式 [插入](xref:mvc/controllers/dependency-injection#constructor-injection) 」將參數傳遞至 `:::no-loc(Identity)::::::no-loc(Cookie):::Options` *AccountController.cs* 和 *ManageController.cs* 。</span><span class="sxs-lookup"><span data-stu-id="e425d-195">For example, 1.x projects use [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) to pass an `:::no-loc(Identity)::::::no-loc(Cookie):::Options` parameter into *AccountController.cs* and *ManageController.cs* .</span></span> <span data-ttu-id="e425d-196">您 :::no-loc(cookie)::: 可以從提供的實例存取外部驗證配置：</span><span class="sxs-lookup"><span data-stu-id="e425d-196">The external :::no-loc(cookie)::: authentication scheme is accessed from the provided instance:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

<span data-ttu-id="e425d-197">上述的函式插入在2.0 專案中變成不必要，而且 `_external:::no-loc(Cookie):::Scheme` 可以刪除欄位：</span><span class="sxs-lookup"><span data-stu-id="e425d-197">The aforementioned constructor injection becomes unnecessary in 2.0 projects, and the `_external:::no-loc(Cookie):::Scheme` field can be deleted:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

<span data-ttu-id="e425d-198">1.x 專案使用欄位，如下所示 `_external:::no-loc(Cookie):::Scheme` ：</span><span class="sxs-lookup"><span data-stu-id="e425d-198">1.x projects used the `_external:::no-loc(Cookie):::Scheme` field as follows:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="e425d-199">在2.0 專案中，以下列程式碼取代上述程式碼。</span><span class="sxs-lookup"><span data-stu-id="e425d-199">In 2.0 projects, replace the preceding code with the following.</span></span> <span data-ttu-id="e425d-200">您 `:::no-loc(Identity):::Constants.ExternalScheme` 可以直接使用常數。</span><span class="sxs-lookup"><span data-stu-id="e425d-200">The `:::no-loc(Identity):::Constants.ExternalScheme` constant can be used directly.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="e425d-201">藉 `SignOutAsync` 由匯入下列命名空間來解析新加入的呼叫：</span><span class="sxs-lookup"><span data-stu-id="e425d-201">Resolve the newly added `SignOutAsync` call by importing the following namespace:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationImport)]

<a name="navigation-properties"></a>

## <a name="add-no-locidentityuser-poco-navigation-properties"></a><span data-ttu-id="e425d-202">新增 :::no-loc(Identity)::: 使用者 POCO 導覽屬性</span><span class="sxs-lookup"><span data-stu-id="e425d-202">Add :::no-loc(Identity):::User POCO navigation properties</span></span>

<span data-ttu-id="e425d-203">Entity Framework (EF) 基底 POCO 的核心導覽屬性， `:::no-loc(Identity):::User` (一般舊的 CLR 物件) 已經被移除。</span><span class="sxs-lookup"><span data-stu-id="e425d-203">The Entity Framework (EF) Core navigation properties of the base `:::no-loc(Identity):::User` POCO (Plain Old CLR Object) have been removed.</span></span> <span data-ttu-id="e425d-204">如果您的1.x 專案使用這些屬性，請手動將其新增回2.0 專案：</span><span class="sxs-lookup"><span data-stu-id="e425d-204">If your 1.x project used these properties, manually add them back to the 2.0 project:</span></span>

```csharp
/// <summary>
/// Navigation property for the roles this user belongs to.
/// </summary>
public virtual ICollection<:::no-loc(Identity):::UserRole<int>> Roles { get; } = new List<:::no-loc(Identity):::UserRole<int>>();

/// <summary>
/// Navigation property for the claims this user possesses.
/// </summary>
public virtual ICollection<:::no-loc(Identity):::UserClaim<int>> Claims { get; } = new List<:::no-loc(Identity):::UserClaim<int>>();

/// <summary>
/// Navigation property for this users login accounts.
/// </summary>
public virtual ICollection<:::no-loc(Identity):::UserLogin<int>> Logins { get; } = new List<:::no-loc(Identity):::UserLogin<int>>();
```

<span data-ttu-id="e425d-205">若要在執行 EF Core 遷移時避免重複的外鍵，請在 `:::no-loc(Identity):::DbContext` 呼叫) 之後，將下列內容新增至類別的 `OnModelCreating` 方法 (`base.OnModelCreating();` ：</span><span class="sxs-lookup"><span data-stu-id="e425d-205">To prevent duplicate foreign keys when running EF Core Migrations, add the following to your `:::no-loc(Identity):::DbContext` class' `OnModelCreating` method (after the `base.OnModelCreating();` call):</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder builder)
{
    base.OnModelCreating(builder);
    // Customize the :::no-loc(ASP.NET Core Identity)::: model and override the defaults if needed.
    // For example, you can rename the :::no-loc(ASP.NET Core Identity)::: table names and more.
    // Add your customizations after calling base.OnModelCreating(builder);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Claims)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Logins)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Roles)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);
}
```

<a name="synchronous-method-removal"></a>

## <a name="replace-getexternalauthenticationschemes"></a><span data-ttu-id="e425d-206">取代 GetExternalAuthenticationSchemes</span><span class="sxs-lookup"><span data-stu-id="e425d-206">Replace GetExternalAuthenticationSchemes</span></span>

<span data-ttu-id="e425d-207">同步方法 `GetExternalAuthenticationSchemes` 已移除，以取代非同步版本。</span><span class="sxs-lookup"><span data-stu-id="e425d-207">The synchronous method `GetExternalAuthenticationSchemes` was removed in favor of an asynchronous version.</span></span> <span data-ttu-id="e425d-208">1.x 專案在 *控制器/ManageController* 中有下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="e425d-208">1.x projects have the following code in *Controllers/ManageController.cs* :</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

<span data-ttu-id="e425d-209">這個方法會出現在 *Views/Account/Login 中。 cshtml* 也會出現：</span><span class="sxs-lookup"><span data-stu-id="e425d-209">This method appears in *Views/Account/Login.cshtml* too:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemes&highlight=2)]

<span data-ttu-id="e425d-210">在2.0 專案中，請使用 <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.SignInManager`1.GetExternalAuthenticationSchemesAsync*> 方法。</span><span class="sxs-lookup"><span data-stu-id="e425d-210">In 2.0 projects, use the <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.SignInManager`1.GetExternalAuthenticationSchemesAsync*> method.</span></span> <span data-ttu-id="e425d-211">*ManageController.cs* 中的變更類似于下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="e425d-211">The change in *ManageController.cs* resembles the following code:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

<span data-ttu-id="e425d-212">在 *Login* 中，在 `AuthenticationScheme` 迴圈中存取的屬性會 `foreach` 變更為 `Name` ：</span><span class="sxs-lookup"><span data-stu-id="e425d-212">In *Login.cshtml* , the `AuthenticationScheme` property accessed in the `foreach` loop changes to `Name`:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemesAsync&highlight=2,19)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a><span data-ttu-id="e425d-213">ManageLoginsViewModel 屬性變更</span><span class="sxs-lookup"><span data-stu-id="e425d-213">ManageLoginsViewModel property change</span></span>

<span data-ttu-id="e425d-214">`ManageLoginsViewModel`ManageController.cs 的動作中會使用物件 `ManageLogins` 。 *ManageController.cs*</span><span class="sxs-lookup"><span data-stu-id="e425d-214">A `ManageLoginsViewModel` object is used in the `ManageLogins` action of *ManageController.cs* .</span></span> <span data-ttu-id="e425d-215">在1.x 專案中，物件的屬性傳回 `OtherLogins` 型別為 `IList<AuthenticationDescription>` 。</span><span class="sxs-lookup"><span data-stu-id="e425d-215">In 1.x projects, the object's `OtherLogins` property return type is `IList<AuthenticationDescription>`.</span></span> <span data-ttu-id="e425d-216">此傳回類型需要匯入 `Microsoft.AspNetCore.Http.Authentication` ：</span><span class="sxs-lookup"><span data-stu-id="e425d-216">This return type requires an import of `Microsoft.AspNetCore.Http.Authentication`:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<span data-ttu-id="e425d-217">在2.0 專案中，傳回型別會變更為 `IList<AuthenticationScheme>` 。</span><span class="sxs-lookup"><span data-stu-id="e425d-217">In 2.0 projects, the return type changes to `IList<AuthenticationScheme>`.</span></span> <span data-ttu-id="e425d-218">這個新的傳回型別需要以匯入來取代匯 `Microsoft.AspNetCore.Http.Authentication` 入 `Microsoft.AspNetCore.Authentication` 。</span><span class="sxs-lookup"><span data-stu-id="e425d-218">This new return type requires replacing the `Microsoft.AspNetCore.Http.Authentication` import with a `Microsoft.AspNetCore.Authentication` import.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a><span data-ttu-id="e425d-219">其他資源</span><span class="sxs-lookup"><span data-stu-id="e425d-219">Additional resources</span></span>

<span data-ttu-id="e425d-220">如需詳細資訊，請參閱 GitHub 上的 [驗證2.0 問題討論](https://github.com/aspnet/Security/issues/1338) 。</span><span class="sxs-lookup"><span data-stu-id="e425d-220">For more information, see the [Discussion for Auth 2.0](https://github.com/aspnet/Security/issues/1338) issue on GitHub.</span></span>
