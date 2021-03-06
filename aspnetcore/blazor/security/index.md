---
title: ASP.NET 核心 Blazor 驗證與授權
author: guardrex
description: 瞭解 Blazor 驗證與授權案例。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/security/index
ms.openlocfilehash: a840e5d54d48441e10c2a58af591a530c8fd7de9
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2021
ms.locfileid: "102394885"
---
# <a name="aspnet-core-blazor-authentication-and-authorization"></a><span data-ttu-id="2d940-103">ASP.NET 核心 Blazor 驗證與授權</span><span class="sxs-lookup"><span data-stu-id="2d940-103">ASP.NET Core Blazor authentication and authorization</span></span>

<span data-ttu-id="2d940-104">ASP.NET Core 支援在應用程式中設定和管理安全性 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="2d940-104">ASP.NET Core supports the configuration and management of security in Blazor apps.</span></span>

<span data-ttu-id="2d940-105">Blazor Server和應用程式之間的安全性案例不同 Blazor WebAssembly 。</span><span class="sxs-lookup"><span data-stu-id="2d940-105">Security scenarios differ between Blazor Server and Blazor WebAssembly apps.</span></span> <span data-ttu-id="2d940-106">由於 Blazor Server 應用程式是在伺服器上執行，因此授權檢查能夠判斷：</span><span class="sxs-lookup"><span data-stu-id="2d940-106">Because Blazor Server apps run on the server, authorization checks are able to determine:</span></span>

* <span data-ttu-id="2d940-107">要提供給使用者的 UI 選項 (例如，使用者可以使用哪些功能表項目)。</span><span class="sxs-lookup"><span data-stu-id="2d940-107">The UI options presented to a user (for example, which menu entries are available to a user).</span></span>
* <span data-ttu-id="2d940-108">適用於應用程式和元件之特定區域的存取規則。</span><span class="sxs-lookup"><span data-stu-id="2d940-108">Access rules for areas of the app and components.</span></span>

<span data-ttu-id="2d940-109">Blazor WebAssembly 應用程式會在用戶端上執行。</span><span class="sxs-lookup"><span data-stu-id="2d940-109">Blazor WebAssembly apps run on the client.</span></span> <span data-ttu-id="2d940-110">授權「僅」會被用來決定要顯示的 UI 選項。</span><span class="sxs-lookup"><span data-stu-id="2d940-110">Authorization is *only* used to determine which UI options to show.</span></span> <span data-ttu-id="2d940-111">由於用戶端檢查可以由使用者修改或略過，因此 Blazor WebAssembly 應用程式無法強制執行授權存取規則。</span><span class="sxs-lookup"><span data-stu-id="2d940-111">Since client-side checks can be modified or bypassed by a user, a Blazor WebAssembly app can't enforce authorization access rules.</span></span>

<span data-ttu-id="2d940-112">[ Razor 頁面授權慣例](xref:security/authorization/razor-pages-authorization)不適用於可路由傳送的 Razor 元件。</span><span class="sxs-lookup"><span data-stu-id="2d940-112">[Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization) don't apply to routable Razor components.</span></span> <span data-ttu-id="2d940-113">如果無法路由傳送的 Razor 元件 [內嵌在頁面中](xref:blazor/components/prerendering-and-integration)，頁面的授權慣例會間接影響 Razor 元件以及頁面內容的其餘部分。</span><span class="sxs-lookup"><span data-stu-id="2d940-113">If a non-routable Razor component is [embedded in a page](xref:blazor/components/prerendering-and-integration), the page's authorization conventions indirectly affect the Razor component along with the rest of the page's content.</span></span>

> [!NOTE]
> <span data-ttu-id="2d940-114"><xref:Microsoft.AspNetCore.Identity.SignInManager%601><xref:Microsoft.AspNetCore.Identity.UserManager%601>元件中不支援和 Razor 。</span><span class="sxs-lookup"><span data-stu-id="2d940-114"><xref:Microsoft.AspNetCore.Identity.SignInManager%601> and <xref:Microsoft.AspNetCore.Identity.UserManager%601> aren't supported in Razor components.</span></span>

## <a name="authentication"></a><span data-ttu-id="2d940-115">驗證</span><span class="sxs-lookup"><span data-stu-id="2d940-115">Authentication</span></span>

<span data-ttu-id="2d940-116">Blazor 使用現有的 ASP.NET 核心驗證機制來建立使用者的身分識別。</span><span class="sxs-lookup"><span data-stu-id="2d940-116">Blazor uses the existing ASP.NET Core authentication mechanisms to establish the user's identity.</span></span> <span data-ttu-id="2d940-117">確切的機制取決於應用程式的裝載方式 Blazor Blazor WebAssembly 或 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="2d940-117">The exact mechanism depends on how the Blazor app is hosted, Blazor WebAssembly or Blazor Server.</span></span>

### <a name="blazor-webassembly-authentication"></a><span data-ttu-id="2d940-118">Blazor WebAssembly 驗證</span><span class="sxs-lookup"><span data-stu-id="2d940-118">Blazor WebAssembly authentication</span></span>

<span data-ttu-id="2d940-119">在 Blazor WebAssembly 應用程式中，可以略過驗證檢查，因為使用者可以修改所有用戶端程式代碼。</span><span class="sxs-lookup"><span data-stu-id="2d940-119">In Blazor WebAssembly apps, authentication checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="2d940-120">這同樣也適用於所有的用戶端應用程式技術，包括 JavaScript SPA 架構或任何作業系統的原生應用程式。</span><span class="sxs-lookup"><span data-stu-id="2d940-120">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="2d940-121">新增下列內容：</span><span class="sxs-lookup"><span data-stu-id="2d940-121">Add the following:</span></span>

* <span data-ttu-id="2d940-122">應用程式專案檔的套件參考 [`Microsoft.AspNetCore.Components.Authorization`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization) 。</span><span class="sxs-lookup"><span data-stu-id="2d940-122">A package reference for [`Microsoft.AspNetCore.Components.Authorization`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization) to the app's project file.</span></span>
* <span data-ttu-id="2d940-123">`Microsoft.AspNetCore.Components.Authorization`應用程式檔案的命名空間 `_Imports.razor` 。</span><span class="sxs-lookup"><span data-stu-id="2d940-123">The `Microsoft.AspNetCore.Components.Authorization` namespace to the app's `_Imports.razor` file.</span></span>

<span data-ttu-id="2d940-124">若要處理驗證，請使用內建或自訂 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> 服務，如下節所述。</span><span class="sxs-lookup"><span data-stu-id="2d940-124">To handle authentication, use of a built-in or custom <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> service is covered in the following sections.</span></span>

<span data-ttu-id="2d940-125">如需建立應用程式和設定的詳細資訊，請參閱 <xref:blazor/security/webassembly/index> 。</span><span class="sxs-lookup"><span data-stu-id="2d940-125">For more information on creating apps and configuration, see <xref:blazor/security/webassembly/index>.</span></span>

### <a name="blazor-server-authentication"></a><span data-ttu-id="2d940-126">Blazor Server 驗證</span><span class="sxs-lookup"><span data-stu-id="2d940-126">Blazor Server authentication</span></span>

<span data-ttu-id="2d940-127">Blazor Server 應用程式會透過使用建立的即時連接來運作 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="2d940-127">Blazor Server apps operate over a real-time connection that's created using SignalR.</span></span> <span data-ttu-id="2d940-128">建立連線時，會處理[以 SignalR 應用程式為基礎的驗證](xref:signalr/authn-and-authz)。</span><span class="sxs-lookup"><span data-stu-id="2d940-128">[Authentication in SignalR-based apps](xref:signalr/authn-and-authz) is handled when the connection is established.</span></span> <span data-ttu-id="2d940-129">驗證可以根據 cookie 或其他某些持有人權杖。</span><span class="sxs-lookup"><span data-stu-id="2d940-129">Authentication can be based on a cookie or some other bearer token.</span></span>

<span data-ttu-id="2d940-130"><xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider>適用于應用程式的內建服務會 Blazor Server 從 ASP.NET Core 取得驗證狀態資料 `HttpContext.User` 。</span><span class="sxs-lookup"><span data-stu-id="2d940-130">The built-in <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> service for Blazor Server apps obtains authentication state data from ASP.NET Core's `HttpContext.User`.</span></span> <span data-ttu-id="2d940-131">這是驗證狀態與現有 ASP.NET Core 驗證機制整合的方式。</span><span class="sxs-lookup"><span data-stu-id="2d940-131">This is how authentication state integrates with existing ASP.NET Core authentication mechanisms.</span></span>

<span data-ttu-id="2d940-132">如需建立應用程式和設定的詳細資訊，請參閱 <xref:blazor/security/server/index> 。</span><span class="sxs-lookup"><span data-stu-id="2d940-132">For more information on creating apps and configuration, see <xref:blazor/security/server/index>.</span></span>

## <a name="authenticationstateprovider-service"></a><span data-ttu-id="2d940-133">AuthenticationStateProvider 服務</span><span class="sxs-lookup"><span data-stu-id="2d940-133">AuthenticationStateProvider service</span></span>

<span data-ttu-id="2d940-134"><xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> 是 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> 元件與 <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> 元件用來取得驗證狀態的基礎服務。</span><span class="sxs-lookup"><span data-stu-id="2d940-134"><xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> is the underlying service used by the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component and <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> component to get the authentication state.</span></span>

<span data-ttu-id="2d940-135">您通常不會直接使用 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider>。</span><span class="sxs-lookup"><span data-stu-id="2d940-135">You don't typically use <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> directly.</span></span> <span data-ttu-id="2d940-136">使用本文稍後所述的[ `AuthorizeView` 元件](#authorizeview-component)或 [`Task<AuthenticationState>`](#expose-the-authentication-state-as-a-cascading-parameter) 方法。</span><span class="sxs-lookup"><span data-stu-id="2d940-136">Use the [`AuthorizeView` component](#authorizeview-component) or [`Task<AuthenticationState>`](#expose-the-authentication-state-as-a-cascading-parameter) approaches described later in this article.</span></span> <span data-ttu-id="2d940-137">使用 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> 的主要缺點，在於系統不會在基礎驗證狀態資料變更時自動通知該元件。</span><span class="sxs-lookup"><span data-stu-id="2d940-137">The main drawback to using <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> directly is that the component isn't notified automatically if the underlying authentication state data changes.</span></span>

<span data-ttu-id="2d940-138"><xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> 服務可以提供目前使用者的 <xref:System.Security.Claims.ClaimsPrincipal> 資料，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="2d940-138">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> service can provide the current user's <xref:System.Security.Claims.ClaimsPrincipal> data, as shown in the following example:</span></span>

```razor
@page "/"
@using System.Security.Claims
@using Microsoft.AspNetCore.Components.Authorization
@inject AuthenticationStateProvider AuthenticationStateProvider

<h3>ClaimsPrincipal Data</h3>

<button @onclick="GetClaimsPrincipalData">Get ClaimsPrincipal Data</button>

<p>@_authMessage</p>

@if (_claims.Count() > 0)
{
    <ul>
        @foreach (var claim in _claims)
        {
            <li>@claim.Type: @claim.Value</li>
        }
    </ul>
}

<p>@_surnameMessage</p>

@code {
    private string _authMessage;
    private string _surnameMessage;
    private IEnumerable<Claim> _claims = Enumerable.Empty<Claim>();

    private async Task GetClaimsPrincipalData()
    {
        var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
            _claims = user.Claims;
            _surnameMessage = 
                $"Surname: {user.FindFirst(c => c.Type == ClaimTypes.Surname)?.Value}";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

<span data-ttu-id="2d940-139">如果 `user.Identity.IsAuthenticated` 為 `true`，且由於使用者為 <xref:System.Security.Claims.ClaimsPrincipal>，系統便可以列舉宣告，並評估角色中的成員資格。</span><span class="sxs-lookup"><span data-stu-id="2d940-139">If `user.Identity.IsAuthenticated` is `true` and because the user is a <xref:System.Security.Claims.ClaimsPrincipal>, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="2d940-140">如需有關相依性插入 (DI) 與服務的詳細資訊，請參閱 <xref:blazor/fundamentals/dependency-injection> 與 <xref:fundamentals/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="2d940-140">For more information on dependency injection (DI) and services, see <xref:blazor/fundamentals/dependency-injection> and <xref:fundamentals/dependency-injection>.</span></span>

## <a name="implement-a-custom-authenticationstateprovider"></a><span data-ttu-id="2d940-141">實作自訂 AuthenticationStateProvider</span><span class="sxs-lookup"><span data-stu-id="2d940-141">Implement a custom AuthenticationStateProvider</span></span>

<span data-ttu-id="2d940-142">如果應用程式需要自訂提供者，請執行 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> 和覆寫 `GetAuthenticationStateAsync` ：</span><span class="sxs-lookup"><span data-stu-id="2d940-142">If the app requires a custom provider, implement <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> and override `GetAuthenticationStateAsync`:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Authorization;

public class CustomAuthStateProvider : AuthenticationStateProvider
{
    public override Task<AuthenticationState> GetAuthenticationStateAsync()
    {
        var identity = new ClaimsIdentity(new[]
        {
            new Claim(ClaimTypes.Name, "mrfibuli"),
        }, "Fake authentication type");

        var user = new ClaimsPrincipal(identity);

        return Task.FromResult(new AuthenticationState(user));
    }
}
```

<span data-ttu-id="2d940-143">在 Blazor WebAssembly 應用程式中， `CustomAuthStateProvider` 服務是在中 `Main` 註冊 `Program.cs` ：</span><span class="sxs-lookup"><span data-stu-id="2d940-143">In a Blazor WebAssembly app, the `CustomAuthStateProvider` service is registered in `Main` of `Program.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="2d940-144">在 Blazor Server 應用程式中， `CustomAuthStateProvider` 服務會在中註冊 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="2d940-144">In a Blazor Server app, the `CustomAuthStateProvider` service is registered in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="2d940-145">使用 `CustomAuthStateProvider` 上述範例中的，所有使用者都會使用使用者名稱進行驗證 `mrfibuli` 。</span><span class="sxs-lookup"><span data-stu-id="2d940-145">Using the `CustomAuthStateProvider` in the preceding example, all users are authenticated with the username `mrfibuli`.</span></span>

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a><span data-ttu-id="2d940-146">將驗證狀態公開為階層式參數</span><span class="sxs-lookup"><span data-stu-id="2d940-146">Expose the authentication state as a cascading parameter</span></span>

<span data-ttu-id="2d940-147">如果程式邏輯需要驗證狀態資料（例如，在執行使用者所觸發的動作時），請藉由定義型別的串聯參數來取得驗證狀態資料 `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` ：</span><span class="sxs-lookup"><span data-stu-id="2d940-147">If authentication state data is required for procedural logic, such as when performing an action triggered by the user, obtain the authentication state data by defining a cascading parameter of type `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>`:</span></span>

```razor
@page "/"

<button @onclick="LogUsername">Log username</button>

<p>@_authMessage</p>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private string _authMessage;

    private async Task LogUsername()
    {
        var authState = await authenticationStateTask;
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

<span data-ttu-id="2d940-148">如果 `user.Identity.IsAuthenticated` 為 `true`，系統便可以列舉宣告，並評估角色中的成員資格。</span><span class="sxs-lookup"><span data-stu-id="2d940-148">If `user.Identity.IsAuthenticated` is `true`, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="2d940-149">`Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` 使用 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> `App` 元件 () 中的和元件來設定串聯參數 `App.razor` ：</span><span class="sxs-lookup"><span data-stu-id="2d940-149">Set up the `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` cascading parameter using the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> and <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> components in the `App` component (`App.razor`):</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)" />
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

<span data-ttu-id="2d940-150">在 Blazor WebAssembly 應用程式中，將選項和授權的服務加入至 `Program.Main` ：</span><span class="sxs-lookup"><span data-stu-id="2d940-150">In a Blazor WebAssembly App, add services for options and authorization to `Program.Main`:</span></span>

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

<span data-ttu-id="2d940-151">在 Blazor Server 應用程式中，選項和授權的服務已經存在，因此不需要採取進一步的動作。</span><span class="sxs-lookup"><span data-stu-id="2d940-151">In a Blazor Server app, services for options and authorization are already present, so no further action is required.</span></span>

## <a name="authorization"></a><span data-ttu-id="2d940-152">授權</span><span class="sxs-lookup"><span data-stu-id="2d940-152">Authorization</span></span>

<span data-ttu-id="2d940-153">在使用者被驗證之後，系統便會套用「授權」規則以控制使用者可以執行的動作。</span><span class="sxs-lookup"><span data-stu-id="2d940-153">After a user is authenticated, *authorization* rules are applied to control what the user can do.</span></span>

<span data-ttu-id="2d940-154">存取權通常會依據下列情況來授與或拒絕：</span><span class="sxs-lookup"><span data-stu-id="2d940-154">Access is typically granted or denied based on whether:</span></span>

* <span data-ttu-id="2d940-155">使用者已通過驗證 (已登入)。</span><span class="sxs-lookup"><span data-stu-id="2d940-155">A user is authenticated (signed in).</span></span>
* <span data-ttu-id="2d940-156">使用者屬於某個「角色」。</span><span class="sxs-lookup"><span data-stu-id="2d940-156">A user is in a *role*.</span></span>
* <span data-ttu-id="2d940-157">使用者具有「宣告」。</span><span class="sxs-lookup"><span data-stu-id="2d940-157">A user has a *claim*.</span></span>
* <span data-ttu-id="2d940-158">已滿足某個「原則」。</span><span class="sxs-lookup"><span data-stu-id="2d940-158">A *policy* is satisfied.</span></span>

<span data-ttu-id="2d940-159">這些概念與 ASP.NET Core MVC 或 Pages 應用程式中的每個概念相同 Razor 。</span><span class="sxs-lookup"><span data-stu-id="2d940-159">Each of these concepts is the same as in an ASP.NET Core MVC or Razor Pages app.</span></span> <span data-ttu-id="2d940-160">如需有關 ASP.NET Core 安全性的詳細資訊，請參閱[ASP.NET Core 安全性和 Identity ](xref:security/index)的文章。</span><span class="sxs-lookup"><span data-stu-id="2d940-160">For more information on ASP.NET Core security, see the articles under [ASP.NET Core Security and Identity](xref:security/index).</span></span>

## <a name="authorizeview-component"></a><span data-ttu-id="2d940-161">AuthorizeView 元件</span><span class="sxs-lookup"><span data-stu-id="2d940-161">AuthorizeView component</span></span>

<span data-ttu-id="2d940-162"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>元件會視使用者是否已獲授權，選擇性地顯示 UI 內容。</span><span class="sxs-lookup"><span data-stu-id="2d940-162">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component selectively displays UI content depending on whether the user is authorized.</span></span> <span data-ttu-id="2d940-163">此方法可讓您只需要向使用者「顯示」資料，而不需要在程序性邏輯中使用該使用者的身分識別。</span><span class="sxs-lookup"><span data-stu-id="2d940-163">This approach is useful when you only need to *display* data for the user and don't need to use the user's identity in procedural logic.</span></span>

<span data-ttu-id="2d940-164">該元件會公開 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> 類型的 `context` 變數，您可以使用它來存取已登入使用者的相關資訊：</span><span class="sxs-lookup"><span data-stu-id="2d940-164">The component exposes a `context` variable of type <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>, which you can use to access information about the signed-in user:</span></span>

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

<span data-ttu-id="2d940-165">如果使用者未獲得授權，您也可以提供不同的內容來顯示：</span><span class="sxs-lookup"><span data-stu-id="2d940-165">You can also supply different content for display if the user isn't authorized:</span></span>

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authorized.</p>
        <button @onclick="SecureMethod">Authorized Only Button</button>
    </Authorized>
    <NotAuthorized>
        <h1>Authentication Failure!</h1>
        <p>You're not signed in.</p>
    </NotAuthorized>
</AuthorizeView>

@code {
    private void SecureMethod() { ... }
}
```

<span data-ttu-id="2d940-166">`<Authorized>`和標記的內容 `<NotAuthorized>` 可以包含任意專案，例如其他互動式元件。</span><span class="sxs-lookup"><span data-stu-id="2d940-166">The content of `<Authorized>` and `<NotAuthorized>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="2d940-167">授權專案的預設事件處理常式（例如 `SecureMethod` `<button>` 上述範例中的元素方法）只能由授權的使用者叫用。</span><span class="sxs-lookup"><span data-stu-id="2d940-167">A default event handler for an authorized element, such as the `SecureMethod` method for the `<button>` element in the preceding example, can only be invoked by an authorized user.</span></span>

<span data-ttu-id="2d940-168">授權情況 (例如控制 UI 選項或存取的角色或原則) 已涵蓋於[授權](#authorization)一節。</span><span class="sxs-lookup"><span data-stu-id="2d940-168">Authorization conditions, such as roles or policies that control UI options or access, are covered in the [Authorization](#authorization) section.</span></span>

<span data-ttu-id="2d940-169">如果未指定授權條件，<xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> 會使用預設的原則，並將：</span><span class="sxs-lookup"><span data-stu-id="2d940-169">If authorization conditions aren't specified, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> uses a default policy and treats:</span></span>

* <span data-ttu-id="2d940-170">已驗證 (已登入) 的使用者視為已授權。</span><span class="sxs-lookup"><span data-stu-id="2d940-170">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="2d940-171">未驗證 (已登出) 的使用者視為未經授權。</span><span class="sxs-lookup"><span data-stu-id="2d940-171">Unauthenticated (signed-out) users as unauthorized.</span></span>

<span data-ttu-id="2d940-172">元件 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> 可以用在 `NavMenu` 元件 (`Shared/NavMenu.razor`) ，以 (`<li>...</li>`) 為[ `NavLink` 元件](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) () 顯示清單專案 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ，但請注意，此方法只會從轉譯的輸出中移除清單專案。</span><span class="sxs-lookup"><span data-stu-id="2d940-172">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component can be used in the `NavMenu` component (`Shared/NavMenu.razor`) to display a list item (`<li>...</li>`) for a [`NavLink` component](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), but note that this approach only removes the list item from the rendered output.</span></span> <span data-ttu-id="2d940-173">它不會防止使用者流覽至元件。</span><span class="sxs-lookup"><span data-stu-id="2d940-173">It doesn't prevent the user from navigating to the component.</span></span>

<span data-ttu-id="2d940-174">從包含驗證的[ Blazor 專案範本](xref:blazor/project-structure)建立的應用程式會使用 `LoginDisplay` 相依于元件的元件 `AuthorizeView` 。</span><span class="sxs-lookup"><span data-stu-id="2d940-174">Apps created from a [Blazor project template](xref:blazor/project-structure) that include authentication use a `LoginDisplay` component that depends on an `AuthorizeView` component.</span></span> <span data-ttu-id="2d940-175">`AuthorizeView`元件會選擇性地向使用者顯示 Identity 相關工作的內容。</span><span class="sxs-lookup"><span data-stu-id="2d940-175">The `AuthorizeView` component selectively displays content to users for Identity-related work.</span></span> <span data-ttu-id="2d940-176">下列範例是來自[ Blazor WebAssembly 專案範本](xref:blazor/project-structure)。</span><span class="sxs-lookup"><span data-stu-id="2d940-176">The following example is from the [Blazor WebAssembly project template](xref:blazor/project-structure).</span></span>

<span data-ttu-id="2d940-177">`Shared/LoginDisplay.razor`:</span><span class="sxs-lookup"><span data-stu-id="2d940-177">`Shared/LoginDisplay.razor`:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginLogout">Log out</button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code{
    private async Task BeginLogout(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

<span data-ttu-id="2d940-178">下列範例是來自[ Blazor Server 專案範本](xref:blazor/project-structure)，並使用 ASP.NET Core Identity `Identity` 應用程式區域中的端點來處理 Identity 相關的工作。</span><span class="sxs-lookup"><span data-stu-id="2d940-178">The following example is from the [Blazor Server project template](xref:blazor/project-structure) and uses ASP.NET Core Identity endpoints in the `Identity` area of the app to process Identity-related work.</span></span>

<span data-ttu-id="2d940-179">`Shared/LoginDisplay.razor`:</span><span class="sxs-lookup"><span data-stu-id="2d940-179">`Shared/LoginDisplay.razor`:</span></span>

```razor
<AuthorizeView>
    <Authorized>
        <a href="Identity/Account/Manage">Hello, @context.User.Identity.Name!</a>
        <form method="post" action="Identity/Account/LogOut">
            <button type="submit" class="nav-link btn btn-link">Log out</button>
        </form>
    </Authorized>
    <NotAuthorized>
        <a href="Identity/Account/Register">Register</a>
        <a href="Identity/Account/Login">Log in</a>
    </NotAuthorized>
</AuthorizeView>
```

### <a name="role-based-and-policy-based-authorization"></a><span data-ttu-id="2d940-180">角色型和原則型授權</span><span class="sxs-lookup"><span data-stu-id="2d940-180">Role-based and policy-based authorization</span></span>

<span data-ttu-id="2d940-181"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> 元件支援「角色型」或「原則型」授權。</span><span class="sxs-lookup"><span data-stu-id="2d940-181">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component supports *role-based* or *policy-based* authorization.</span></span>

<span data-ttu-id="2d940-182">針對角色型授權，請使用 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> 參數：</span><span class="sxs-lookup"><span data-stu-id="2d940-182">For role-based authorization, use the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> parameter:</span></span>

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

<span data-ttu-id="2d940-183">如需詳細資訊，請參閱<xref:security/authorization/roles>。</span><span class="sxs-lookup"><span data-stu-id="2d940-183">For more information, see <xref:security/authorization/roles>.</span></span>

<span data-ttu-id="2d940-184">針對原則型授權，請使用 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> 參數：</span><span class="sxs-lookup"><span data-stu-id="2d940-184">For policy-based authorization, use the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> parameter:</span></span>

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

<span data-ttu-id="2d940-185">宣告型授權是特殊案例的原則型授權。</span><span class="sxs-lookup"><span data-stu-id="2d940-185">Claims-based authorization is a special case of policy-based authorization.</span></span> <span data-ttu-id="2d940-186">例如，您可以定義要求使用者具備特定宣告的原則。</span><span class="sxs-lookup"><span data-stu-id="2d940-186">For example, you can define a policy that requires users to have a certain claim.</span></span> <span data-ttu-id="2d940-187">如需詳細資訊，請參閱<xref:security/authorization/policies>。</span><span class="sxs-lookup"><span data-stu-id="2d940-187">For more information, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="2d940-188">這些 Api 可以在 Blazor Server 或 Blazor WebAssembly 應用程式中使用。</span><span class="sxs-lookup"><span data-stu-id="2d940-188">These APIs can be used in either Blazor Server or Blazor WebAssembly apps.</span></span>

<span data-ttu-id="2d940-189">如果未指定 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> 和 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy>，<xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> 便會使用預設原則。</span><span class="sxs-lookup"><span data-stu-id="2d940-189">If neither <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> nor <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> is specified, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> uses the default policy.</span></span>

### <a name="content-displayed-during-asynchronous-authentication"></a><span data-ttu-id="2d940-190">在非同步驗證期間所顯示的內容</span><span class="sxs-lookup"><span data-stu-id="2d940-190">Content displayed during asynchronous authentication</span></span>

<span data-ttu-id="2d940-191">Blazor 允許以 *非同步方式* 判斷驗證狀態。</span><span class="sxs-lookup"><span data-stu-id="2d940-191">Blazor allows for authentication state to be determined *asynchronously*.</span></span> <span data-ttu-id="2d940-192">這種方法的主要案例是對 Blazor WebAssembly 外部端點提出要求以進行驗證的應用程式。</span><span class="sxs-lookup"><span data-stu-id="2d940-192">The primary scenario for this approach is in Blazor WebAssembly apps that make a request to an external endpoint for authentication.</span></span>

<span data-ttu-id="2d940-193">在驗證期間，<xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> 預設不會顯示任何內容。</span><span class="sxs-lookup"><span data-stu-id="2d940-193">While authentication is in progress, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> displays no content by default.</span></span> <span data-ttu-id="2d940-194">若要在進行驗證時顯示內容，請使用 `<Authorizing>` 標記：</span><span class="sxs-lookup"><span data-stu-id="2d940-194">To display content while authentication occurs, use the `<Authorizing>` tag:</span></span>

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <Authorizing>
        <h1>Authentication in progress</h1>
        <p>You can only see this content while authentication is in progress.</p>
    </Authorizing>
</AuthorizeView>
```

<span data-ttu-id="2d940-195">這種方法通常不適用於 Blazor Server 應用程式。</span><span class="sxs-lookup"><span data-stu-id="2d940-195">This approach isn't normally applicable to Blazor Server apps.</span></span> <span data-ttu-id="2d940-196">Blazor Server 應用程式會在狀態建立後立即得知驗證狀態。</span><span class="sxs-lookup"><span data-stu-id="2d940-196">Blazor Server apps know the authentication state as soon as the state is established.</span></span> <span data-ttu-id="2d940-197"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeViewCore.Authorizing> 您可以在 Blazor Server 應用程式的元件中提供內容 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> ，但永遠不會顯示內容。</span><span class="sxs-lookup"><span data-stu-id="2d940-197"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeViewCore.Authorizing> content can be provided in a Blazor Server app's <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component, but the content is never displayed.</span></span>

## <a name="authorize-attribute"></a><span data-ttu-id="2d940-198">[Authorize] 屬性</span><span class="sxs-lookup"><span data-stu-id="2d940-198">[Authorize] attribute</span></span>

<span data-ttu-id="2d940-199">[ `[Authorize]` 屬性](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)可用於 Razor 元件中：</span><span class="sxs-lookup"><span data-stu-id="2d940-199">The [`[Authorize]` attribute](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) can be used in Razor components:</span></span>

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> <span data-ttu-id="2d940-200">僅 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 在透過 `@page` 路由器達成的元件上使用 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="2d940-200">Only use [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) on `@page` components reached via the Blazor Router.</span></span> <span data-ttu-id="2d940-201">授權僅會以路由的層面執行，且「不」適用於在頁面內轉譯的子元件。</span><span class="sxs-lookup"><span data-stu-id="2d940-201">Authorization is only performed as an aspect of routing and *not* for child components rendered within a page.</span></span> <span data-ttu-id="2d940-202">若要授權在頁面內顯示特定組件，請改為使用 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>。</span><span class="sxs-lookup"><span data-stu-id="2d940-202">To authorize the display of specific parts within a page, use <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> instead.</span></span>

<span data-ttu-id="2d940-203">[ `[Authorize]` 屬性](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)也支援以角色為基礎或以原則為基礎的授權。</span><span class="sxs-lookup"><span data-stu-id="2d940-203">The [`[Authorize]` attribute](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) also supports role-based or policy-based authorization.</span></span> <span data-ttu-id="2d940-204">針對角色型授權，請使用 <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> 參數：</span><span class="sxs-lookup"><span data-stu-id="2d940-204">For role-based authorization, use the <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

<span data-ttu-id="2d940-205">針對原則型授權，請使用 <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> 參數：</span><span class="sxs-lookup"><span data-stu-id="2d940-205">For policy-based authorization, use the <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

<span data-ttu-id="2d940-206">如果 <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> 未指定或，則 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 會使用預設原則，其預設為處理：</span><span class="sxs-lookup"><span data-stu-id="2d940-206">If neither <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> nor <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> is specified, [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) uses the default policy, which by default is to treat:</span></span>

* <span data-ttu-id="2d940-207">已驗證 (已登入) 的使用者視為已授權。</span><span class="sxs-lookup"><span data-stu-id="2d940-207">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="2d940-208">未驗證 (已登出) 的使用者視為未經授權。</span><span class="sxs-lookup"><span data-stu-id="2d940-208">Unauthenticated (signed-out) users as unauthorized.</span></span>

## <a name="customize-unauthorized-content-with-the-router-component"></a><span data-ttu-id="2d940-209">搭配 Router 元件自訂未經授權的內容</span><span class="sxs-lookup"><span data-stu-id="2d940-209">Customize unauthorized content with the Router component</span></span>

<span data-ttu-id="2d940-210"><xref:Microsoft.AspNetCore.Components.Routing.Router>元件與元件搭配使用 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> ，可讓應用程式在下列情況下指定自訂內容：</span><span class="sxs-lookup"><span data-stu-id="2d940-210">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component, in conjunction with the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> component, allows the app to specify custom content if:</span></span>

* <span data-ttu-id="2d940-211">使用者無法通過套用 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 至元件的條件。</span><span class="sxs-lookup"><span data-stu-id="2d940-211">The user fails an [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) condition applied to the component.</span></span> <span data-ttu-id="2d940-212">元素的標記隨即 [`<NotAuthorized>`](xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView.NotAuthorized?displayProperty=nameWithType) 顯示。</span><span class="sxs-lookup"><span data-stu-id="2d940-212">The markup of the [`<NotAuthorized>`](xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView.NotAuthorized?displayProperty=nameWithType) element is displayed.</span></span> <span data-ttu-id="2d940-213">屬性 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 區段中包含屬性。 [ `[Authorize]` ](#authorize-attribute)</span><span class="sxs-lookup"><span data-stu-id="2d940-213">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is covered in the [`[Authorize]` attribute](#authorize-attribute) section.</span></span>
* <span data-ttu-id="2d940-214">非同步授權正在進行中，這通常表示正在驗證使用者的程式正在進行中。</span><span class="sxs-lookup"><span data-stu-id="2d940-214">Asynchronous authorization is in progress, which usually means that the process of authenticating the user is in progress.</span></span> <span data-ttu-id="2d940-215">元素的標記隨即 [`<Authorizing>`](xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView.Authorizing?displayProperty=nameWithType) 顯示。</span><span class="sxs-lookup"><span data-stu-id="2d940-215">The markup of the [`<Authorizing>`](xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView.Authorizing?displayProperty=nameWithType) element is displayed.</span></span>
* <span data-ttu-id="2d940-216">找不到內容。</span><span class="sxs-lookup"><span data-stu-id="2d940-216">Content isn't found.</span></span> <span data-ttu-id="2d940-217">元素的標記隨即 [`<NotFound>`](xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound?displayProperty=nameWithType) 顯示。</span><span class="sxs-lookup"><span data-stu-id="2d940-217">The markup of the [`<NotFound>`](xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound?displayProperty=nameWithType) element is displayed.</span></span>

<span data-ttu-id="2d940-218">在預設[ Blazor Server 專案範本](xref:blazor/project-structure)中， `App`) 的元件 (會 `App.razor` 示範如何設定自訂內容：</span><span class="sxs-lookup"><span data-stu-id="2d940-218">In the default [Blazor Server project template](xref:blazor/project-structure), the `App` component (`App.razor`) demonstrates how to set custom content:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)">
                <NotAuthorized>
                    <h1>Sorry</h1>
                    <p>You're not authorized to reach this page.</p>
                    <p>You may need to log in as a different user.</p>
                </NotAuthorized>
                <Authorizing>
                    <h1>Authorization in progress</h1>
                    <p>Only visible while authorization is in progress.</p>
                </Authorizing>
            </AuthorizeRouteView>
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <h1>Sorry</h1>
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

<span data-ttu-id="2d940-219">`<NotFound>`、 `<NotAuthorized>` 和標記的內容 `<Authorizing>` 可以包含任意專案，例如其他互動式元件。</span><span class="sxs-lookup"><span data-stu-id="2d940-219">The content of `<NotFound>`, `<NotAuthorized>`, and `<Authorizing>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="2d940-220">如果 `<NotAuthorized>` 未指定標籤，則會 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> 使用下列回復訊息：</span><span class="sxs-lookup"><span data-stu-id="2d940-220">If the `<NotAuthorized>` tag isn't specified, the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> uses the following fallback message:</span></span>

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a><span data-ttu-id="2d940-221">關於驗證狀態變更的通知</span><span class="sxs-lookup"><span data-stu-id="2d940-221">Notification about authentication state changes</span></span>

<span data-ttu-id="2d940-222">如果應用程式判斷基礎驗證狀態資料已變更 (例如，因為使用者已登出或其他使用者已) 變更其角色，則[自訂 `AuthenticationStateProvider` ](#implement-a-custom-authenticationstateprovider)可以選擇性地 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider.NotifyAuthenticationStateChanged%2A> 在基類上叫用方法 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> 。</span><span class="sxs-lookup"><span data-stu-id="2d940-222">If the app determines that the underlying authentication state data has changed (for example, because the user signed out or another user has changed their roles), a [custom `AuthenticationStateProvider`](#implement-a-custom-authenticationstateprovider) can optionally invoke the method <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider.NotifyAuthenticationStateChanged%2A> on the <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> base class.</span></span> <span data-ttu-id="2d940-223">這會通知驗證狀態資料的取用者 (例如 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>) 使用新資料來重新轉譯。</span><span class="sxs-lookup"><span data-stu-id="2d940-223">This notifies consumers of the authentication state data (for example, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>) to rerender using the new data.</span></span>

## <a name="procedural-logic"></a><span data-ttu-id="2d940-224">程序性邏輯</span><span class="sxs-lookup"><span data-stu-id="2d940-224">Procedural logic</span></span>

<span data-ttu-id="2d940-225">如果應用程式需要在程式邏輯中檢查授權規則，請使用型別的串聯參數 `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` 來取得使用者的 <xref:System.Security.Claims.ClaimsPrincipal> 。</span><span class="sxs-lookup"><span data-stu-id="2d940-225">If the app is required to check authorization rules as part of procedural logic, use a cascaded parameter of type `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` to obtain the user's <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="2d940-226">`Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` 可以與其他服務結合，例如 `IAuthorizationService` ，以評估原則。</span><span class="sxs-lookup"><span data-stu-id="2d940-226">`Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` can be combined with other services, such as `IAuthorizationService`, to evaluate policies.</span></span>

```razor
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

<button @onclick="@DoSomething">Do something important</button>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async Task DoSomething()
    {
        var user = (await authenticationStateTask).User;

        if (user.Identity.IsAuthenticated)
        {
            // Perform an action only available to authenticated (signed-in) users.
        }

        if (user.IsInRole("admin"))
        {
            // Perform an action only available to users in the 'admin' role.
        }

        if ((await AuthorizationService.AuthorizeAsync(user, "content-editor"))
            .Succeeded)
        {
            // Perform an action only available to users satisfying the 
            // 'content-editor' policy.
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="2d940-227">在 Blazor WebAssembly 應用程式元件中，新增 <xref:Microsoft.AspNetCore.Authorization> 和 <xref:Microsoft.AspNetCore.Components.Authorization> 命名空間：</span><span class="sxs-lookup"><span data-stu-id="2d940-227">In a Blazor WebAssembly app component, add the <xref:Microsoft.AspNetCore.Authorization> and <xref:Microsoft.AspNetCore.Components.Authorization> namespaces:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> <span data-ttu-id="2d940-228">您可以藉由將這些命名空間新增至應用程式的檔案，以全域方式提供這些命名空間 `_Imports.razor` 。</span><span class="sxs-lookup"><span data-stu-id="2d940-228">These namespaces can be provided globally by adding them to the app's `_Imports.razor` file.</span></span>

## <a name="troubleshoot-errors"></a><span data-ttu-id="2d940-229">針對錯誤進行疑難排解</span><span class="sxs-lookup"><span data-stu-id="2d940-229">Troubleshoot errors</span></span>

<span data-ttu-id="2d940-230">常見錯誤：</span><span class="sxs-lookup"><span data-stu-id="2d940-230">Common errors:</span></span>

* <span data-ttu-id="2d940-231">**授權需要類型的級聯參數 `Task\<AuthenticationState>` 。請考慮使用 `CascadingAuthenticationState` 以提供此。**</span><span class="sxs-lookup"><span data-stu-id="2d940-231">**Authorization requires a cascading parameter of type `Task\<AuthenticationState>`. Consider using `CascadingAuthenticationState` to supply this.**</span></span>

* <span data-ttu-id="2d940-232">**`null` 收到的值為 `authenticationStateTask`**</span><span class="sxs-lookup"><span data-stu-id="2d940-232">**`null` value is received for `authenticationStateTask`**</span></span>

<span data-ttu-id="2d940-233">專案可能不是使用 Blazor Server 已啟用驗證的範本所建立。</span><span class="sxs-lookup"><span data-stu-id="2d940-233">It's likely that the project wasn't created using a Blazor Server template with authentication enabled.</span></span> <span data-ttu-id="2d940-234">`<CascadingAuthenticationState>`在 UI 樹狀結構的某個部分周圍包裝，例如在 `App` 元件 () ， `App.razor` 如下所示：</span><span class="sxs-lookup"><span data-stu-id="2d940-234">Wrap a `<CascadingAuthenticationState>` around some part of the UI tree, for example in the `App` component (`App.razor`) as follows:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

<span data-ttu-id="2d940-235"><xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` 會提供串聯參數，進而從基礎 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> DI 服務接收。</span><span class="sxs-lookup"><span data-stu-id="2d940-235">The <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> supplies the `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` cascading parameter, which in turn it receives from the underlying <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> DI service.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2d940-236">其他資源</span><span class="sxs-lookup"><span data-stu-id="2d940-236">Additional resources</span></span>

* <xref:security/index>
* <xref:security/authentication/windowsauth>
* [<span data-ttu-id="2d940-237">建立驗證的自訂版本 MSAL JavaScript 程式庫</span><span class="sxs-lookup"><span data-stu-id="2d940-237">Build a custom version of the Authentication.MSAL JavaScript library</span></span>](xref:blazor/security/webassembly/additional-scenarios#build-a-custom-version-of-the-authenticationmsal-javascript-library)
* <span data-ttu-id="2d940-238">[絕佳 Blazor ：驗證](https://github.com/AdrienTorris/awesome-blazor#authentication) 社區範例連結</span><span class="sxs-lookup"><span data-stu-id="2d940-238">[Awesome Blazor: Authentication](https://github.com/AdrienTorris/awesome-blazor#authentication) community sample links</span></span>
