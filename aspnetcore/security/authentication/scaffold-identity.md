---
title: :::no-loc(Identity):::ASP.NET Core 專案中的 Scaffold
author: rick-anderson
description: '瞭解如何 :::no-loc(Identity)::: 在 ASP.NET Core 專案中 scaffold。'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
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
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 813dd7837c265c78c584d66dd51bc23399d12fbe
ms.sourcegitcommit: 5156eab2118584405eb663e1fcd82f8bd7764504
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2020
ms.locfileid: "93141491"
---
# <a name="scaffold-no-locidentity-in-aspnet-core-projects"></a><span data-ttu-id="e05fa-103">:::no-loc(Identity):::ASP.NET Core 專案中的 Scaffold</span><span class="sxs-lookup"><span data-stu-id="e05fa-103">Scaffold :::no-loc(Identity)::: in ASP.NET Core projects</span></span>

<span data-ttu-id="e05fa-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e05fa-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e05fa-105">ASP.NET Core [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) 以[ :::no-loc(Razor)::: 類別庫](xref:razor-pages/ui-class)的形式提供。</span><span class="sxs-lookup"><span data-stu-id="e05fa-105">ASP.NET Core provides [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) as a [:::no-loc(Razor)::: Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="e05fa-106">包含的應用程式 :::no-loc(Identity)::: 可以套用 scaffolder，以選擇性地加入類別庫中包含的原始程式碼 :::no-loc(Identity)::: :::no-loc(Razor)::: (RCL) 。</span><span class="sxs-lookup"><span data-stu-id="e05fa-106">Applications that include :::no-loc(Identity)::: can apply the scaffolder to selectively add the source code contained in the :::no-loc(Identity)::: :::no-loc(Razor)::: Class Library (RCL).</span></span> <span data-ttu-id="e05fa-107">建議您產生原始程式碼，以便能夠修改程式碼並變更行為。</span><span class="sxs-lookup"><span data-stu-id="e05fa-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="e05fa-108">例如，您可以指示 Scaffolder 產生註冊使用的程式碼。</span><span class="sxs-lookup"><span data-stu-id="e05fa-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="e05fa-109">產生的程式碼優先于 RCL 中的相同程式碼 :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="e05fa-109">Generated code takes precedence over the same code in the :::no-loc(Identity)::: RCL.</span></span> <span data-ttu-id="e05fa-110">若要完全掌控 UI，而不使用預設 RCL，請參閱 [建立完整 :::no-loc(Identity)::: UI 來源](#full)一節。</span><span class="sxs-lookup"><span data-stu-id="e05fa-110">To gain full control of the UI and not use the default RCL, see the section [Create full :::no-loc(Identity)::: UI source](#full).</span></span>

<span data-ttu-id="e05fa-111">**未** 包含驗證的應用程式可以套用 scaffolder 來新增 RCL :::no-loc(Identity)::: 套件。</span><span class="sxs-lookup"><span data-stu-id="e05fa-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL :::no-loc(Identity)::: package.</span></span> <span data-ttu-id="e05fa-112">您可以選擇 :::no-loc(Identity)::: 要產生的程式碼。</span><span class="sxs-lookup"><span data-stu-id="e05fa-112">You have the option of selecting :::no-loc(Identity)::: code to be generated.</span></span>

<span data-ttu-id="e05fa-113">雖然 scaffolder 會產生大部分必要的程式碼，但您必須更新您的專案，才能完成此程式。</span><span class="sxs-lookup"><span data-stu-id="e05fa-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="e05fa-114">本檔說明完成程式更新所需的步驟 :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="e05fa-114">This document explains the steps needed to complete an :::no-loc(Identity)::: scaffolding update.</span></span>

<span data-ttu-id="e05fa-115">我們建議使用可顯示檔案差異的原始檔控制系統，並可讓您將變更移出。</span><span class="sxs-lookup"><span data-stu-id="e05fa-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="e05fa-116">執行 scaffolder 後檢查變更 :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="e05fa-116">Inspect the changes after running the :::no-loc(Identity)::: scaffolder.</span></span>

<span data-ttu-id="e05fa-117">使用 [雙因素驗證](xref:security/authentication/identity-enable-qrcodes)、 [帳戶確認和密碼](xref:security/authentication/accconfirm)復原，以及其他安全性功能時，都需要服務 :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="e05fa-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with :::no-loc(Identity):::.</span></span> <span data-ttu-id="e05fa-118">當樣板時，不會產生服務或服務存根 :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="e05fa-118">Services or service stubs aren't generated when scaffolding :::no-loc(Identity):::.</span></span> <span data-ttu-id="e05fa-119">啟用這些功能的服務必須以手動方式新增。</span><span class="sxs-lookup"><span data-stu-id="e05fa-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="e05fa-120">例如，請參閱 [要求電子郵件確認](xref:security/authentication/accconfirm#require-email-confirmation)。</span><span class="sxs-lookup"><span data-stu-id="e05fa-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="e05fa-121">當 :::no-loc(Identity)::: 具有現有個別帳戶的專案具有新的資料內容時：</span><span class="sxs-lookup"><span data-stu-id="e05fa-121">When scaffolding :::no-loc(Identity)::: with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="e05fa-122">在中 `Startup.ConfigureServices` ，移除對下列各項的呼叫：</span><span class="sxs-lookup"><span data-stu-id="e05fa-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefault:::no-loc(Identity):::`

<span data-ttu-id="e05fa-123">例如， `AddDbContext` `AddDefault:::no-loc(Identity):::` 在下列程式碼中，和會批註化：</span><span class="sxs-lookup"><span data-stu-id="e05fa-123">For example, `AddDbContext` and `AddDefault:::no-loc(Identity):::` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="e05fa-124">先前的程式碼會將在 *區域/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* 中複製的程式碼批註</span><span class="sxs-lookup"><span data-stu-id="e05fa-124">The preceeding code comments out the code that is duplicated in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs*</span></span>

<span data-ttu-id="e05fa-125">一般而言，使用個別帳戶所建立的應用程式應該 \* **而不** 是建立新的資料內容。</span><span class="sxs-lookup"><span data-stu-id="e05fa-125">Typically, apps that were created with individual accounts should \* **not** _ create a new data context.</span></span>

## <a name="scaffold-no-locidentity-into-an-empty-project"></a><span data-ttu-id="e05fa-126">Scaffold :::no-loc(Identity)::: 至空白專案</span><span class="sxs-lookup"><span data-stu-id="e05fa-126">Scaffold :::no-loc(Identity)::: into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="e05fa-127">使用與 `Startup` 下列類似的程式碼來更新類別：</span><span class="sxs-lookup"><span data-stu-id="e05fa-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a><span data-ttu-id="e05fa-128">Scaffold :::no-loc(Identity)::: 至 :::no-loc(Razor)::: 沒有現有授權的專案</span><span class="sxs-lookup"><span data-stu-id="e05fa-128">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.:::no-loc(Identity):::.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef database drop
dotnet ef migrations add Create:::no-loc(Identity):::Schema0
dotnet ef database update
-->

<!-- ERROR
There is already an object named 'AspNetRoles' in the database.

Fixed via dotnet ef database drop
before dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="e05fa-129">:::no-loc(Identity):::在 _Areas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs \* 中設定。</span><span class="sxs-lookup"><span data-stu-id="e05fa-129">:::no-loc(Identity)::: is configured in _Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs\*.</span></span> <span data-ttu-id="e05fa-130">如需詳細資訊，請參閱 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)。</span><span class="sxs-lookup"><span data-stu-id="e05fa-130">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="e05fa-131">遷移、UseAuthentication 和版面配置</span><span class="sxs-lookup"><span data-stu-id="e05fa-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="e05fa-132">啟用驗證</span><span class="sxs-lookup"><span data-stu-id="e05fa-132">Enable authentication</span></span>

<span data-ttu-id="e05fa-133">使用與 `Startup` 下列類似的程式碼來更新類別：</span><span class="sxs-lookup"><span data-stu-id="e05fa-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="e05fa-134">版面配置變更</span><span class="sxs-lookup"><span data-stu-id="e05fa-134">Layout changes</span></span>

<span data-ttu-id="e05fa-135">選擇性：將登入部分 (`_LoginPartial`) 新增至版面配置檔案：</span><span class="sxs-lookup"><span data-stu-id="e05fa-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a><span data-ttu-id="e05fa-136">Scaffold :::no-loc(Identity)::: 至 :::no-loc(Razor)::: 具有授權的專案</span><span class="sxs-lookup"><span data-stu-id="e05fa-136">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project with authorization</span></span>

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="e05fa-137">某些 :::no-loc(Identity)::: 選項是在 *區域/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* 中設定。</span><span class="sxs-lookup"><span data-stu-id="e05fa-137">Some :::no-loc(Identity)::: options are configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="e05fa-138">如需詳細資訊，請參閱 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)。</span><span class="sxs-lookup"><span data-stu-id="e05fa-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="e05fa-139">Scaffold :::no-loc(Identity)::: 至沒有現有授權的 MVC 專案</span><span class="sxs-lookup"><span data-stu-id="e05fa-139">Scaffold :::no-loc(Identity)::: into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add Create:::no-loc(Identity):::Schema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="e05fa-140">選擇性：將登入部分 (`_LoginPartial`) 新增至 *Views/Shared/_Layout cshtml* 檔案：</span><span class="sxs-lookup"><span data-stu-id="e05fa-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="e05fa-141">將 *Pages/shared/_LoginPartial 的 cshtml* 檔案移至 *Views/shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="e05fa-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="e05fa-142">:::no-loc(Identity):::是在 *區域/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* 中設定。</span><span class="sxs-lookup"><span data-stu-id="e05fa-142">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="e05fa-143">如需詳細資訊，請參閱 IHostingStartup。</span><span class="sxs-lookup"><span data-stu-id="e05fa-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="e05fa-144">使用與 `Startup` 下列類似的程式碼來更新類別：</span><span class="sxs-lookup"><span data-stu-id="e05fa-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="e05fa-145">Scaffold :::no-loc(Identity)::: 至具有授權的 MVC 專案</span><span class="sxs-lookup"><span data-stu-id="e05fa-145">Scaffold :::no-loc(Identity)::: into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-without-existing-authorization"></a><span data-ttu-id="e05fa-146">Scaffold :::no-loc(Identity)::: 至 :::no-loc(Blazor Server)::: 沒有現有授權的專案</span><span class="sxs-lookup"><span data-stu-id="e05fa-146">Scaffold :::no-loc(Identity)::: into a :::no-loc(Blazor Server)::: project without existing authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="e05fa-147">:::no-loc(Identity):::是在 *區域/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* 中設定。</span><span class="sxs-lookup"><span data-stu-id="e05fa-147">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="e05fa-148">如需詳細資訊，請參閱 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)。</span><span class="sxs-lookup"><span data-stu-id="e05fa-148">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

### <a name="migrations"></a><span data-ttu-id="e05fa-149">移轉</span><span class="sxs-lookup"><span data-stu-id="e05fa-149">Migrations</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

### <a name="pass-an-xsrf-token-to-the-app"></a><span data-ttu-id="e05fa-150">將 XSRF token 傳遞至應用程式</span><span class="sxs-lookup"><span data-stu-id="e05fa-150">Pass an XSRF token to the app</span></span>

<span data-ttu-id="e05fa-151">權杖可以傳遞給元件：</span><span class="sxs-lookup"><span data-stu-id="e05fa-151">Tokens can be passed to components:</span></span>

* <span data-ttu-id="e05fa-152">當驗證權杖已布建並儲存至驗證時 :::no-loc(cookie)::: ，可傳遞至元件。</span><span class="sxs-lookup"><span data-stu-id="e05fa-152">When authentication tokens are provisioned and saved to the authentication :::no-loc(cookie):::, they can be passed to components.</span></span>
* <span data-ttu-id="e05fa-153">:::no-loc(Razor)::: 元件無法 `HttpContext` 直接使用，因此沒有任何方法可 [ (XSRF) 權杖](xref:security/anti-request-forgery) ，以張貼至 :::no-loc(Identity)::: 的登出端點 `/:::no-loc(Identity):::/Account/Logout` 。</span><span class="sxs-lookup"><span data-stu-id="e05fa-153">:::no-loc(Razor)::: components can't use `HttpContext` directly, so there's no way to obtain an [anti-request forgery (XSRF) token](xref:security/anti-request-forgery) to POST to :::no-loc(Identity):::'s logout endpoint at `/:::no-loc(Identity):::/Account/Logout`.</span></span> <span data-ttu-id="e05fa-154">XSRF token 可以傳遞至元件。</span><span class="sxs-lookup"><span data-stu-id="e05fa-154">An XSRF token can be passed to components.</span></span>

<span data-ttu-id="e05fa-155">如需詳細資訊，請參閱<xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>。</span><span class="sxs-lookup"><span data-stu-id="e05fa-155">For more information, see <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span></span>

<span data-ttu-id="e05fa-156">在 *Pages/_Host cshtml* 檔案中，將標記加入至和類別之後建立權杖 `InitialApplicationState` `TokenProvider` ：</span><span class="sxs-lookup"><span data-stu-id="e05fa-156">In the *Pages/_Host.cshtml* file, establish the token after adding it to the `InitialApplicationState` and `TokenProvider` classes:</span></span>

```csharp
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Xsrf

...

var tokens = new InitialApplicationState
{
    ...

    XsrfToken = Xsrf.GetAndStoreTokens(HttpContext).RequestToken
};
```

<span data-ttu-id="e05fa-157">更新 `App` ( *應用程式的元件。 Razor* ) 以指派 `InitialState.XsrfToken` ：</span><span class="sxs-lookup"><span data-stu-id="e05fa-157">Update the `App` component ( *App.razor* ) to assign the `InitialState.XsrfToken`:</span></span>

```csharp
@inject TokenProvider TokenProvider

...

TokenProvider.XsrfToken = InitialState.XsrfToken;
```

<span data-ttu-id="e05fa-158">主題中所 `TokenProvider` 示範的服務用於 `LoginDisplay` 下列版面配置 [和驗證流程變更](#layout-and-authentication-flow-changes) 區段中的元件。</span><span class="sxs-lookup"><span data-stu-id="e05fa-158">The `TokenProvider` service demonstrated in the topic is used in the `LoginDisplay` component in the following [Layout and authentication flow changes](#layout-and-authentication-flow-changes) section.</span></span>

### <a name="enable-authentication"></a><span data-ttu-id="e05fa-159">啟用驗證</span><span class="sxs-lookup"><span data-stu-id="e05fa-159">Enable authentication</span></span>

<span data-ttu-id="e05fa-160">在 `Startup` 類別中：</span><span class="sxs-lookup"><span data-stu-id="e05fa-160">In the `Startup` class:</span></span>

* <span data-ttu-id="e05fa-161">確認已 :::no-loc(Razor)::: 在中加入頁面服務 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="e05fa-161">Confirm that :::no-loc(Razor)::: Pages services are added in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="e05fa-162">如果使用 [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app)，請註冊服務。</span><span class="sxs-lookup"><span data-stu-id="e05fa-162">If using the [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), register the service.</span></span>
* <span data-ttu-id="e05fa-163">`UseDatabaseErrorPage`針對開發環境，在的應用程式產生器上呼叫 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="e05fa-163">Call `UseDatabaseErrorPage` on the application builder in `Startup.Configure` for the Development environment.</span></span>
* <span data-ttu-id="e05fa-164">Call `UseAuthentication` 和 `UseAuthorization` after `UseRouting` 。</span><span class="sxs-lookup"><span data-stu-id="e05fa-164">Call `UseAuthentication` and `UseAuthorization` after `UseRouting`.</span></span>
* <span data-ttu-id="e05fa-165">新增頁面的端點 :::no-loc(Razor)::: 。</span><span class="sxs-lookup"><span data-stu-id="e05fa-165">Add an endpoint for :::no-loc(Razor)::: Pages.</span></span>

[!code-csharp[](scaffold-identity/3.1sample/Startup:::no-loc(Blazor):::.cs?highlight=3,6,14,27-28,32)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-and-authentication-flow-changes"></a><span data-ttu-id="e05fa-166">版面配置和驗證流程變更</span><span class="sxs-lookup"><span data-stu-id="e05fa-166">Layout and authentication flow changes</span></span>

<span data-ttu-id="e05fa-167">將 `RedirectToLogin` ( *RedirectToLogin* 的元件) 新增至專案根目錄中的應用程式 *共用* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="e05fa-167">Add a `RedirectToLogin` component ( *RedirectToLogin.razor* ) to the app's *Shared* folder in the project root:</span></span>

```razor
@inject NavigationManager Navigation
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo(":::no-loc(Identity):::/Account/Login?returnUrl=" +
            Uri.EscapeDataString(Navigation.Uri), true);
    }
}
```

將 `LoginDisplay` ( *LoginDisplay* 的元件) 加入應用程式的 *共用* 資料夾。 <span data-ttu-id="e05fa-169">[TokenProvider 服務](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app)會針對張貼至登出端點的 HTML 表單，提供 XSRF token :::no-loc(Identity)::: ：</span><span class="sxs-lookup"><span data-stu-id="e05fa-169">The [TokenProvider service](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) provides the XSRF token for the HTML form that POSTs to :::no-loc(Identity):::'s logout endpoint:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@inject NavigationManager Navigation
@inject TokenProvider TokenProvider

<AuthorizeView>
    <Authorized>
        <a href=":::no-loc(Identity):::/Account/Manage/Index">
            Hello, @context.User.:::no-loc(Identity):::.Name!
        </a>
        <form action="/:::no-loc(Identity):::/Account/Logout?returnUrl=%2F" method="post">
            <button class="nav-link btn btn-link" type="submit">Logout</button>
            <input name="__RequestVerificationToken" type="hidden" 
                value="@TokenProvider.XsrfToken">
        </form>
    </Authorized>
    <NotAuthorized>
        <a href=":::no-loc(Identity):::/Account/Register">Register</a>
        <a href=":::no-loc(Identity):::/Account/Login">Login</a>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="e05fa-170">在 `MainLayout` 元件 ( *共用/MainLayout* ]) 中，將元件新增 `LoginDisplay` 至最上層資料列 `<div>` 元素的內容：</span><span class="sxs-lookup"><span data-stu-id="e05fa-170">In the `MainLayout` component ( *Shared/MainLayout.razor* ), add the `LoginDisplay` component to the top-row `<div>` element's content:</span></span>

```razor
<div class="top-row px-4 auth">
    <LoginDisplay />
    <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
</div>
```

### <a name="style-authentication-endpoints"></a><span data-ttu-id="e05fa-171">樣式驗證端點</span><span class="sxs-lookup"><span data-stu-id="e05fa-171">Style authentication endpoints</span></span>

<span data-ttu-id="e05fa-172">由於 :::no-loc(Blazor Server)::: 使用 :::no-loc(Razor)::: 頁面 :::no-loc(Identity)::: 頁面，當訪客在頁面和元件之間流覽時，UI 的樣式會變更 :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="e05fa-172">Because :::no-loc(Blazor Server)::: uses :::no-loc(Razor)::: Pages :::no-loc(Identity)::: pages, the styling of the UI changes when a visitor navigates between :::no-loc(Identity)::: pages and components.</span></span> <span data-ttu-id="e05fa-173">您有兩個選項可處理 incongruous 樣式：</span><span class="sxs-lookup"><span data-stu-id="e05fa-173">You have two options to address the incongruous styles:</span></span>

#### <a name="build-no-locidentity-components"></a><span data-ttu-id="e05fa-174">組建 :::no-loc(Identity)::: 元件</span><span class="sxs-lookup"><span data-stu-id="e05fa-174">Build :::no-loc(Identity)::: components</span></span>

<span data-ttu-id="e05fa-175">使用元件 :::no-loc(Identity)::: 而非頁面的方法是建立 :::no-loc(Identity)::: 元件。</span><span class="sxs-lookup"><span data-stu-id="e05fa-175">An approach to using components for :::no-loc(Identity)::: instead of pages is to build :::no-loc(Identity)::: components.</span></span> <span data-ttu-id="e05fa-176">因為 `SignInManager` `UserManager` 元件中不支援和 :::no-loc(Razor)::: ，所以請使用應用程式中的 API 端點 :::no-loc(Blazor Server)::: 來處理使用者帳戶動作。</span><span class="sxs-lookup"><span data-stu-id="e05fa-176">Because `SignInManager` and `UserManager` aren't supported in :::no-loc(Razor)::: components, use API endpoints in the :::no-loc(Blazor Server)::: app to process user account actions.</span></span>

#### <a name="use-a-custom-layout-with-no-locblazor-app-styles"></a><span data-ttu-id="e05fa-177">搭配應用程式樣式使用自訂版面配置 :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="e05fa-177">Use a custom layout with :::no-loc(Blazor)::: app styles</span></span>

<span data-ttu-id="e05fa-178">您 :::no-loc(Identity)::: 可以修改頁面版面配置和樣式，以產生使用預設主題的頁面 :::no-loc(Blazor)::: 。</span><span class="sxs-lookup"><span data-stu-id="e05fa-178">The :::no-loc(Identity)::: pages layout and styles can be modified to produce pages that use the default :::no-loc(Blazor)::: theme.</span></span>

> [!NOTE]
> <span data-ttu-id="e05fa-179">本節中的範例只是自訂的起點。</span><span class="sxs-lookup"><span data-stu-id="e05fa-179">The example in this section is merely a starting point for customization.</span></span> <span data-ttu-id="e05fa-180">可能需要額外的工作才能獲得最佳使用者體驗。</span><span class="sxs-lookup"><span data-stu-id="e05fa-180">Additional work is likely required for the best user experience.</span></span>

<span data-ttu-id="e05fa-181">建立新的 `NavMenu_:::no-loc(Identity):::Layout` 元件， ( *共用/NavMenu_ :::no-loc(Identity)::: 版面配置。 razor* ) 。</span><span class="sxs-lookup"><span data-stu-id="e05fa-181">Create a new `NavMenu_:::no-loc(Identity):::Layout` component ( *Shared/NavMenu_:::no-loc(Identity):::Layout.razor* ).</span></span> <span data-ttu-id="e05fa-182">針對元件的標記和程式碼，請使用應用程式元件的相同內容 `NavMenu` ( *Shared/NavMenu razor* ) 。</span><span class="sxs-lookup"><span data-stu-id="e05fa-182">For the markup and code of the component, use the same content of the app's `NavMenu` component ( *Shared/NavMenu.razor* ).</span></span> <span data-ttu-id="e05fa-183">`NavLink`針對需要驗證或授權的元件，將任何無法匿名連接的元件帶出至元件，因為元件中的自動重新導向 `RedirectToLogin` 失敗。</span><span class="sxs-lookup"><span data-stu-id="e05fa-183">Strip out any `NavLink`s to components that can't be reached anonymously because automatic redirects in the `RedirectToLogin` component fail for components requiring authentication or authorization.</span></span>

<span data-ttu-id="e05fa-184">在 *Pages/Shared/Layout. cshtml* 檔案中，進行下列變更：</span><span class="sxs-lookup"><span data-stu-id="e05fa-184">In the *Pages/Shared/Layout.cshtml* file, make the following changes:</span></span>

* <span data-ttu-id="e05fa-185">將指示詞新增至檔案頂端，以使用標籤協助 :::no-loc(Razor)::: 程式和 *共用* 資料夾中的應用程式元件：</span><span class="sxs-lookup"><span data-stu-id="e05fa-185">Add :::no-loc(Razor)::: directives to the top of the file to use Tag Helpers and the app's components in the *Shared* folder:</span></span>

  ```cshtml
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  @using {APPLICATION ASSEMBLY}.Shared
  ```

  <span data-ttu-id="e05fa-186">取代 `{APPLICATION ASSEMBLY}` 為應用程式的元件名稱。</span><span class="sxs-lookup"><span data-stu-id="e05fa-186">Replace `{APPLICATION ASSEMBLY}` with the app's assembly name.</span></span>

* <span data-ttu-id="e05fa-187">將 `<base>` 標記和樣式表單新增 :::no-loc(Blazor)::: `<link>` 至 `<head>` 內容：</span><span class="sxs-lookup"><span data-stu-id="e05fa-187">Add a `<base>` tag and :::no-loc(Blazor)::: stylesheet `<link>` to the `<head>` content:</span></span>

  ```cshtml
  <base href="~/" />
  <link rel="stylesheet" href="~/css/site.css" />
  ```

* <span data-ttu-id="e05fa-188">將標記的內容變更 `<body>` 為下列內容：</span><span class="sxs-lookup"><span data-stu-id="e05fa-188">Change the content of the `<body>` tag to the following:</span></span>

  ```cshtml
  <div class="sidebar" style="float:left">
      <component type="typeof(NavMenu_:::no-loc(Identity):::Layout)" 
          render-mode="ServerPrerendered" />
  </div>

  <div class="main" style="padding-left:250px">
      <div class="top-row px-4">
          @{
              var result = Engine.FindView(ViewContext, "_LoginPartial", 
                  isMainPage: false);
          }
          @if (result.Success)
          {
              await Html.RenderPartialAsync("_LoginPartial");
          }
          else
          {
              throw new InvalidOperationException("The default :::no-loc(Identity)::: UI " +
                  "layout requires a partial view '_LoginPartial'.");
          }
          <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
      </div>

      <div class="content px-4">
          @RenderBody()
      </div>
  </div>

  <script src="~/:::no-loc(Identity):::/lib/jquery/dist/jquery.min.js"></script>
  <script src="~/:::no-loc(Identity):::/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
  <script src="~/:::no-loc(Identity):::/js/site.js" asp-append-version="true"></script>
  @RenderSection("Scripts", required: false)
  <script src="_framework/blazor.server.js"></script>
  ```

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-with-authorization"></a><span data-ttu-id="e05fa-189">Scaffold :::no-loc(Identity)::: 至 :::no-loc(Blazor Server)::: 具有授權的專案</span><span class="sxs-lookup"><span data-stu-id="e05fa-189">Scaffold :::no-loc(Identity)::: into a :::no-loc(Blazor Server)::: project with authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="e05fa-190">某些 :::no-loc(Identity)::: 選項是在 *區域/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* 中設定。</span><span class="sxs-lookup"><span data-stu-id="e05fa-190">Some :::no-loc(Identity)::: options are configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="e05fa-191">如需詳細資訊，請參閱 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)。</span><span class="sxs-lookup"><span data-stu-id="e05fa-191">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="standalone-or-hosted-no-locblazor-webassembly-apps"></a><span data-ttu-id="e05fa-192">獨立或託管的 :::no-loc(Blazor WebAssembly)::: 應用程式</span><span class="sxs-lookup"><span data-stu-id="e05fa-192">Standalone or hosted :::no-loc(Blazor WebAssembly)::: apps</span></span>

<span data-ttu-id="e05fa-193">用戶端 :::no-loc(Blazor WebAssembly)::: 應用程式使用自己的 :::no-loc(Identity)::: UI 方法，無法使用樣板 :::no-loc(ASP.NET Core Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="e05fa-193">Client-side :::no-loc(Blazor WebAssembly)::: apps use their own :::no-loc(Identity)::: UI approaches and can't use :::no-loc(ASP.NET Core Identity)::: scaffolding.</span></span> <span data-ttu-id="e05fa-194">裝載解決方案的伺服器端 ASP.NET Core 應用程式 :::no-loc(Blazor)::: 可以遵循本文 :::no-loc(Razor)::: 中的頁面/MVC 指導方針，其設定方式就像任何其他支援的 ASP.NET Core 應用程式類型 :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="e05fa-194">Server-side ASP.NET Core apps of hosted :::no-loc(Blazor)::: solutions can follow the :::no-loc(Razor)::: Pages/MVC guidance in this article and are configured just like any other type of ASP.NET Core app that supports :::no-loc(Identity):::.</span></span>

<span data-ttu-id="e05fa-195">:::no-loc(Blazor):::架構不包含 :::no-loc(Razor)::: UI 頁面的元件版本 :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="e05fa-195">The :::no-loc(Blazor)::: framework doesn't include :::no-loc(Razor)::: component versions of :::no-loc(Identity)::: UI pages.</span></span> <span data-ttu-id="e05fa-196">:::no-loc(Identity)::: UI :::no-loc(Razor)::: 元件可以自訂建立，也可以從不支援的協力廠商來源取得。</span><span class="sxs-lookup"><span data-stu-id="e05fa-196">:::no-loc(Identity)::: UI :::no-loc(Razor)::: components can be custom built or obtained from unsupported third-party sources.</span></span>

<span data-ttu-id="e05fa-197">如需詳細資訊，請參閱[ :::no-loc(Blazor)::: 安全性和 :::no-loc(Identity)::: 文章](xref:blazor/security/index)。</span><span class="sxs-lookup"><span data-stu-id="e05fa-197">For more information, see the [:::no-loc(Blazor)::: Security and :::no-loc(Identity)::: articles](xref:blazor/security/index).</span></span>

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a><span data-ttu-id="e05fa-198">建立完整的 :::no-loc(Identity)::: UI 來源</span><span class="sxs-lookup"><span data-stu-id="e05fa-198">Create full :::no-loc(Identity)::: UI source</span></span>

<span data-ttu-id="e05fa-199">若要維持 UI 的完整控制權 :::no-loc(Identity)::: ，請執行 :::no-loc(Identity)::: scaffolder，然後選取 [覆 **寫所有** 檔案]。</span><span class="sxs-lookup"><span data-stu-id="e05fa-199">To maintain full control of the :::no-loc(Identity)::: UI, run the :::no-loc(Identity)::: scaffolder and select **Override all files** .</span></span>

<span data-ttu-id="e05fa-200">下列醒目提示的程式碼顯示 :::no-loc(Identity)::: :::no-loc(Identity)::: 在 ASP.NET Core 2.1 web 應用程式中，用來取代預設 UI 的變更。</span><span class="sxs-lookup"><span data-stu-id="e05fa-200">The following highlighted code shows the changes to replace the default :::no-loc(Identity)::: UI with :::no-loc(Identity)::: in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="e05fa-201">您可能會想要這樣做，才能完全控制 :::no-loc(Identity)::: UI。</span><span class="sxs-lookup"><span data-stu-id="e05fa-201">You might want to do this to have full control of the :::no-loc(Identity)::: UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="e05fa-202">:::no-loc(Identity):::下列程式碼將取代預設值：</span><span class="sxs-lookup"><span data-stu-id="e05fa-202">The default :::no-loc(Identity)::: is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="e05fa-203">下列程式碼會設定 [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath)、 [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath)和 [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath)：</span><span class="sxs-lookup"><span data-stu-id="e05fa-203">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="e05fa-204">註冊 `IEmailSender` 執行，例如：</span><span class="sxs-lookup"><span data-stu-id="e05fa-204">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a><span data-ttu-id="e05fa-205">密碼設定</span><span class="sxs-lookup"><span data-stu-id="e05fa-205">Password configuration</span></span>

<span data-ttu-id="e05fa-206">如果 <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> 在中設定 `Startup.ConfigureServices` ，scaffold 頁面中的屬性可能需要[ `[StringLength]` 屬性](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute)設定 `Password` :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="e05fa-206">If <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded :::no-loc(Identity)::: pages.</span></span> <span data-ttu-id="e05fa-207">`InputModel`您 `Password` 可以在下列檔案中找到屬性：</span><span class="sxs-lookup"><span data-stu-id="e05fa-207">`InputModel` `Password` properties are found in the following files:</span></span>

* `Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs`
* `Areas/:::no-loc(Identity):::/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-a-page"></a><span data-ttu-id="e05fa-208">停用頁面</span><span class="sxs-lookup"><span data-stu-id="e05fa-208">Disable a page</span></span>

<span data-ttu-id="e05fa-209">這幾節會說明如何停用 [註冊] 頁面，但方法可以用來停用任何頁面。</span><span class="sxs-lookup"><span data-stu-id="e05fa-209">This sections show how to disable the register page but the approach can be used to disable any page.</span></span>

<span data-ttu-id="e05fa-210">若要停用使用者註冊：</span><span class="sxs-lookup"><span data-stu-id="e05fa-210">To disable user registration:</span></span>

* <span data-ttu-id="e05fa-211">Scaffold :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="e05fa-211">Scaffold :::no-loc(Identity):::.</span></span> <span data-ttu-id="e05fa-212">包含 Account。 Register、Account、Login 和 Account. RegisterConfirmation。</span><span class="sxs-lookup"><span data-stu-id="e05fa-212">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="e05fa-213">例如：</span><span class="sxs-lookup"><span data-stu-id="e05fa-213">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="e05fa-214">更新 *區域/ :::no-loc(Identity)::: /Pages/Account/Register.cshtml.cs* ，讓使用者無法從此端點註冊：</span><span class="sxs-lookup"><span data-stu-id="e05fa-214">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="e05fa-215">更新 *區域/ :::no-loc(Identity)::: /Pages/Account/Register.cshtml* ，以便與先前的變更一致：</span><span class="sxs-lookup"><span data-stu-id="e05fa-215">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="e05fa-216">批註或移除 *區域/ :::no-loc(Identity)::: /Pages/Account/Login.cshtml* 的註冊連結</span><span class="sxs-lookup"><span data-stu-id="e05fa-216">Comment out or remove the registration link from *Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml*</span></span>

  ```cshtml
  @*
  <p>
      <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
  </p>
  *@
  ```

* <span data-ttu-id="e05fa-217">更新 *區域/ :::no-loc(Identity)::: /Pages/Account/RegisterConfirmation* 頁面。</span><span class="sxs-lookup"><span data-stu-id="e05fa-217">Update the *Areas/:::no-loc(Identity):::/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="e05fa-218">移除 cshtml 檔案中的程式碼和連結。</span><span class="sxs-lookup"><span data-stu-id="e05fa-218">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="e05fa-219">移除下列內容中的確認碼 `PageModel` ：</span><span class="sxs-lookup"><span data-stu-id="e05fa-219">Remove the confirmation code from the `PageModel`:</span></span>

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="e05fa-220">使用其他應用程式來新增使用者</span><span class="sxs-lookup"><span data-stu-id="e05fa-220">Use another app to add users</span></span>

<span data-ttu-id="e05fa-221">提供在 web 應用程式外部新增使用者的機制。</span><span class="sxs-lookup"><span data-stu-id="e05fa-221">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="e05fa-222">新增使用者的選項包括：</span><span class="sxs-lookup"><span data-stu-id="e05fa-222">Options to add users include:</span></span>

* <span data-ttu-id="e05fa-223">專用的管理 web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="e05fa-223">A dedicated admin web app.</span></span>
* <span data-ttu-id="e05fa-224">主控台應用程式。</span><span class="sxs-lookup"><span data-stu-id="e05fa-224">A console app.</span></span>

<span data-ttu-id="e05fa-225">下列程式碼概述新增使用者的一種方法：</span><span class="sxs-lookup"><span data-stu-id="e05fa-225">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="e05fa-226">使用者清單會讀入記憶體中。</span><span class="sxs-lookup"><span data-stu-id="e05fa-226">A list of users is read into memory.</span></span>
* <span data-ttu-id="e05fa-227">針對每個使用者產生強式唯一密碼。</span><span class="sxs-lookup"><span data-stu-id="e05fa-227">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="e05fa-228">使用者會新增至 :::no-loc(Identity)::: 資料庫。</span><span class="sxs-lookup"><span data-stu-id="e05fa-228">The user is added to the :::no-loc(Identity)::: database.</span></span>
* <span data-ttu-id="e05fa-229">系統會通知使用者，並告知使用者變更密碼。</span><span class="sxs-lookup"><span data-stu-id="e05fa-229">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="e05fa-230">下列程式碼概述如何新增使用者：</span><span class="sxs-lookup"><span data-stu-id="e05fa-230">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="e05fa-231">在生產案例中，可以遵循類似的方法。</span><span class="sxs-lookup"><span data-stu-id="e05fa-231">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-no-locidentity-assets"></a><span data-ttu-id="e05fa-232">防止發佈靜態 :::no-loc(Identity)::: 資產</span><span class="sxs-lookup"><span data-stu-id="e05fa-232">Prevent publish of static :::no-loc(Identity)::: assets</span></span>

<span data-ttu-id="e05fa-233">若要防止將靜態 :::no-loc(Identity)::: 資產發行至 web 根目錄，請參閱 <xref:security/authentication/identity#prevent-publish-of-static-identity-assets> 。</span><span class="sxs-lookup"><span data-stu-id="e05fa-233">To prevent publishing static :::no-loc(Identity)::: assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e05fa-234">其他資源</span><span class="sxs-lookup"><span data-stu-id="e05fa-234">Additional resources</span></span>

* [<span data-ttu-id="e05fa-235">驗證碼變更為 ASP.NET Core 2.1 和更新版本</span><span class="sxs-lookup"><span data-stu-id="e05fa-235">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e05fa-236">ASP.NET Core 2.1 和更新版本會 [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) 以[ :::no-loc(Razor)::: 類別庫](xref:razor-pages/ui-class)的形式提供。</span><span class="sxs-lookup"><span data-stu-id="e05fa-236">ASP.NET Core 2.1 and later provides [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) as a [:::no-loc(Razor)::: Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="e05fa-237">包含的應用程式 :::no-loc(Identity)::: 可以套用 scaffolder，以選擇性地加入類別庫中包含的原始程式碼 :::no-loc(Identity)::: :::no-loc(Razor)::: (RCL) 。</span><span class="sxs-lookup"><span data-stu-id="e05fa-237">Applications that include :::no-loc(Identity)::: can apply the scaffolder to selectively add the source code contained in the :::no-loc(Identity)::: :::no-loc(Razor)::: Class Library (RCL).</span></span> <span data-ttu-id="e05fa-238">建議您產生原始程式碼，以便能夠修改程式碼並變更行為。</span><span class="sxs-lookup"><span data-stu-id="e05fa-238">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="e05fa-239">例如，您可以指示 Scaffolder 產生註冊使用的程式碼。</span><span class="sxs-lookup"><span data-stu-id="e05fa-239">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="e05fa-240">產生的程式碼優先于 RCL 中的相同程式碼 :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="e05fa-240">Generated code takes precedence over the same code in the :::no-loc(Identity)::: RCL.</span></span> <span data-ttu-id="e05fa-241">若要完全掌控 UI，而不使用預設 RCL，請參閱 [建立完整身分識別 UI 來源](#full)一節。</span><span class="sxs-lookup"><span data-stu-id="e05fa-241">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="e05fa-242">**未** 包含驗證的應用程式可以套用 scaffolder 來新增 RCL :::no-loc(Identity)::: 套件。</span><span class="sxs-lookup"><span data-stu-id="e05fa-242">Applications that do **not** include authentication can apply the scaffolder to add the RCL :::no-loc(Identity)::: package.</span></span> <span data-ttu-id="e05fa-243">您可以選擇 :::no-loc(Identity)::: 要產生的程式碼。</span><span class="sxs-lookup"><span data-stu-id="e05fa-243">You have the option of selecting :::no-loc(Identity)::: code to be generated.</span></span>

<span data-ttu-id="e05fa-244">雖然 scaffolder 會產生大部分必要的程式碼，但您必須更新您的專案，才能完成此程式。</span><span class="sxs-lookup"><span data-stu-id="e05fa-244">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="e05fa-245">本檔說明完成程式更新所需的步驟 :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="e05fa-245">This document explains the steps needed to complete an :::no-loc(Identity)::: scaffolding update.</span></span>

<span data-ttu-id="e05fa-246">當 :::no-loc(Identity)::: scaffolder 執行時，會在專案目錄中建立 *ScaffoldingReadme.txt* 檔案。</span><span class="sxs-lookup"><span data-stu-id="e05fa-246">When the :::no-loc(Identity)::: scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="e05fa-247">*ScaffoldingReadme.txt* 檔案包含完成此樣板更新所需功能的一般指示 :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="e05fa-247">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the :::no-loc(Identity)::: scaffolding update.</span></span> <span data-ttu-id="e05fa-248">本檔包含比 *ScaffoldingReadme.txt* 檔案更完整的指示。</span><span class="sxs-lookup"><span data-stu-id="e05fa-248">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="e05fa-249">我們建議使用可顯示檔案差異的原始檔控制系統，並可讓您將變更移出。</span><span class="sxs-lookup"><span data-stu-id="e05fa-249">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="e05fa-250">執行 scaffolder 後檢查變更 :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="e05fa-250">Inspect the changes after running the :::no-loc(Identity)::: scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="e05fa-251">使用 [雙因素驗證](xref:security/authentication/identity-enable-qrcodes)、 [帳戶確認和密碼](xref:security/authentication/accconfirm)復原，以及其他安全性功能時，都需要服務 :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="e05fa-251">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with :::no-loc(Identity):::.</span></span> <span data-ttu-id="e05fa-252">當樣板時，不會產生服務或服務存根 :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="e05fa-252">Services or service stubs aren't generated when scaffolding :::no-loc(Identity):::.</span></span> <span data-ttu-id="e05fa-253">啟用這些功能的服務必須以手動方式新增。</span><span class="sxs-lookup"><span data-stu-id="e05fa-253">Services to enable these features must be added manually.</span></span> <span data-ttu-id="e05fa-254">例如，請參閱 [要求電子郵件確認](xref:security/authentication/accconfirm#require-email-confirmation)。</span><span class="sxs-lookup"><span data-stu-id="e05fa-254">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-no-locidentity-into-an-empty-project"></a><span data-ttu-id="e05fa-255">Scaffold :::no-loc(Identity)::: 至空白專案</span><span class="sxs-lookup"><span data-stu-id="e05fa-255">Scaffold :::no-loc(Identity)::: into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="e05fa-256">將下列反白顯示的呼叫新增至 `Startup` 類別：</span><span class="sxs-lookup"><span data-stu-id="e05fa-256">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a><span data-ttu-id="e05fa-257">Scaffold :::no-loc(Identity)::: 至 :::no-loc(Razor)::: 沒有現有授權的專案</span><span class="sxs-lookup"><span data-stu-id="e05fa-257">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.:::no-loc(Identity):::.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add Create:::no-loc(Identity):::Schema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="e05fa-258">:::no-loc(Identity):::是在 *區域/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* 中設定。</span><span class="sxs-lookup"><span data-stu-id="e05fa-258">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="e05fa-259">如需詳細資訊，請參閱 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)。</span><span class="sxs-lookup"><span data-stu-id="e05fa-259">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="e05fa-260">遷移、UseAuthentication 和版面配置</span><span class="sxs-lookup"><span data-stu-id="e05fa-260">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="e05fa-261">啟用驗證</span><span class="sxs-lookup"><span data-stu-id="e05fa-261">Enable authentication</span></span>

<span data-ttu-id="e05fa-262">在 `Configure` 類別的方法中 `Startup` ，呼叫 <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> 之後 `UseStaticFiles` ：</span><span class="sxs-lookup"><span data-stu-id="e05fa-262">In the `Configure` method of the `Startup` class, call <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="e05fa-263">版面配置變更</span><span class="sxs-lookup"><span data-stu-id="e05fa-263">Layout changes</span></span>

<span data-ttu-id="e05fa-264">選擇性：將登入部分 (`_LoginPartial`) 新增至版面配置檔案：</span><span class="sxs-lookup"><span data-stu-id="e05fa-264">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-cshtml[](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a><span data-ttu-id="e05fa-265">Scaffold :::no-loc(Identity)::: 至 :::no-loc(Razor)::: 具有授權的專案</span><span class="sxs-lookup"><span data-stu-id="e05fa-265">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project with authorization</span></span>

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="e05fa-266">某些 :::no-loc(Identity)::: 選項是在 *區域/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* 中設定。</span><span class="sxs-lookup"><span data-stu-id="e05fa-266">Some :::no-loc(Identity)::: options are configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="e05fa-267">如需詳細資訊，請參閱 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)。</span><span class="sxs-lookup"><span data-stu-id="e05fa-267">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="e05fa-268">Scaffold :::no-loc(Identity)::: 至沒有現有授權的 MVC 專案</span><span class="sxs-lookup"><span data-stu-id="e05fa-268">Scaffold :::no-loc(Identity)::: into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add Create:::no-loc(Identity):::Schema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="e05fa-269">選擇性：將登入部分 (`_LoginPartial`) 新增至 *Views/Shared/_Layout cshtml* 檔案：</span><span class="sxs-lookup"><span data-stu-id="e05fa-269">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="e05fa-270">將 *Pages/shared/_LoginPartial 的 cshtml* 檔案移至 *Views/shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="e05fa-270">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="e05fa-271">:::no-loc(Identity):::是在 *區域/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* 中設定。</span><span class="sxs-lookup"><span data-stu-id="e05fa-271">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="e05fa-272">如需詳細資訊，請參閱 IHostingStartup。</span><span class="sxs-lookup"><span data-stu-id="e05fa-272">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="e05fa-273">呼叫 <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> 之後 `UseStaticFiles` ：</span><span class="sxs-lookup"><span data-stu-id="e05fa-273">Call <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="e05fa-274">Scaffold :::no-loc(Identity)::: 至具有授權的 MVC 專案</span><span class="sxs-lookup"><span data-stu-id="e05fa-274">Scaffold :::no-loc(Identity)::: into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="e05fa-275">刪除 *頁面/共用* 資料夾，以及該資料夾中的檔案。</span><span class="sxs-lookup"><span data-stu-id="e05fa-275">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a><span data-ttu-id="e05fa-276">建立完整的 :::no-loc(Identity)::: UI 來源</span><span class="sxs-lookup"><span data-stu-id="e05fa-276">Create full :::no-loc(Identity)::: UI source</span></span>

<span data-ttu-id="e05fa-277">若要維持 UI 的完整控制權 :::no-loc(Identity)::: ，請執行 :::no-loc(Identity)::: scaffolder，然後選取 [覆 **寫所有** 檔案]。</span><span class="sxs-lookup"><span data-stu-id="e05fa-277">To maintain full control of the :::no-loc(Identity)::: UI, run the :::no-loc(Identity)::: scaffolder and select **Override all files** .</span></span>

<span data-ttu-id="e05fa-278">下列醒目提示的程式碼顯示 :::no-loc(Identity)::: :::no-loc(Identity)::: 在 ASP.NET Core 2.1 web 應用程式中，用來取代預設 UI 的變更。</span><span class="sxs-lookup"><span data-stu-id="e05fa-278">The following highlighted code shows the changes to replace the default :::no-loc(Identity)::: UI with :::no-loc(Identity)::: in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="e05fa-279">您可能會想要這樣做，才能完全控制 :::no-loc(Identity)::: UI。</span><span class="sxs-lookup"><span data-stu-id="e05fa-279">You might want to do this to have full control of the :::no-loc(Identity)::: UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="e05fa-280">:::no-loc(Identity):::下列程式碼將取代預設值：</span><span class="sxs-lookup"><span data-stu-id="e05fa-280">The default :::no-loc(Identity)::: is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="e05fa-281">下列程式碼會設定 [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath)、 [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath)和 [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath)：</span><span class="sxs-lookup"><span data-stu-id="e05fa-281">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="e05fa-282">註冊 `IEmailSender` 執行，例如：</span><span class="sxs-lookup"><span data-stu-id="e05fa-282">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a><span data-ttu-id="e05fa-283">密碼設定</span><span class="sxs-lookup"><span data-stu-id="e05fa-283">Password configuration</span></span>

<span data-ttu-id="e05fa-284">如果 <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> 在中設定 `Startup.ConfigureServices` ，scaffold 頁面中的屬性可能需要[ `[StringLength]` 屬性](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute)設定 `Password` :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="e05fa-284">If <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded :::no-loc(Identity)::: pages.</span></span> <span data-ttu-id="e05fa-285">`InputModel`您 `Password` 可以在下列檔案中找到屬性：</span><span class="sxs-lookup"><span data-stu-id="e05fa-285">`InputModel` `Password` properties are found in the following files:</span></span>

* `Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs`
* `Areas/:::no-loc(Identity):::/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-register-page"></a><span data-ttu-id="e05fa-286">停用註冊頁面</span><span class="sxs-lookup"><span data-stu-id="e05fa-286">Disable register page</span></span>

<span data-ttu-id="e05fa-287">若要停用使用者註冊：</span><span class="sxs-lookup"><span data-stu-id="e05fa-287">To disable user registration:</span></span>

* <span data-ttu-id="e05fa-288">Scaffold :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="e05fa-288">Scaffold :::no-loc(Identity):::.</span></span> <span data-ttu-id="e05fa-289">包含 Account。 Register、Account、Login 和 Account. RegisterConfirmation。</span><span class="sxs-lookup"><span data-stu-id="e05fa-289">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="e05fa-290">例如：</span><span class="sxs-lookup"><span data-stu-id="e05fa-290">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="e05fa-291">更新 *區域/ :::no-loc(Identity)::: /Pages/Account/Register.cshtml.cs* ，讓使用者無法從此端點註冊：</span><span class="sxs-lookup"><span data-stu-id="e05fa-291">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="e05fa-292">更新 *區域/ :::no-loc(Identity)::: /Pages/Account/Register.cshtml* ，以便與先前的變更一致：</span><span class="sxs-lookup"><span data-stu-id="e05fa-292">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="e05fa-293">批註或移除 *區域/ :::no-loc(Identity)::: /Pages/Account/Login.cshtml* 的註冊連結</span><span class="sxs-lookup"><span data-stu-id="e05fa-293">Comment out or remove the registration link from *Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="e05fa-294">更新 *區域/ :::no-loc(Identity)::: /Pages/Account/RegisterConfirmation* 頁面。</span><span class="sxs-lookup"><span data-stu-id="e05fa-294">Update the *Areas/:::no-loc(Identity):::/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="e05fa-295">移除 cshtml 檔案中的程式碼和連結。</span><span class="sxs-lookup"><span data-stu-id="e05fa-295">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="e05fa-296">移除下列內容中的確認碼 `PageModel` ：</span><span class="sxs-lookup"><span data-stu-id="e05fa-296">Remove the confirmation code from the `PageModel`:</span></span>

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="e05fa-297">使用其他應用程式來新增使用者</span><span class="sxs-lookup"><span data-stu-id="e05fa-297">Use another app to add users</span></span>

<span data-ttu-id="e05fa-298">提供在 web 應用程式外部新增使用者的機制。</span><span class="sxs-lookup"><span data-stu-id="e05fa-298">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="e05fa-299">新增使用者的選項包括：</span><span class="sxs-lookup"><span data-stu-id="e05fa-299">Options to add users include:</span></span>

* <span data-ttu-id="e05fa-300">專用的管理 web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="e05fa-300">A dedicated admin web app.</span></span>
* <span data-ttu-id="e05fa-301">主控台應用程式。</span><span class="sxs-lookup"><span data-stu-id="e05fa-301">A console app.</span></span>

<span data-ttu-id="e05fa-302">下列程式碼概述新增使用者的一種方法：</span><span class="sxs-lookup"><span data-stu-id="e05fa-302">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="e05fa-303">使用者清單會讀入記憶體中。</span><span class="sxs-lookup"><span data-stu-id="e05fa-303">A list of users is read into memory.</span></span>
* <span data-ttu-id="e05fa-304">針對每個使用者產生強式唯一密碼。</span><span class="sxs-lookup"><span data-stu-id="e05fa-304">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="e05fa-305">使用者會新增至 :::no-loc(Identity)::: 資料庫。</span><span class="sxs-lookup"><span data-stu-id="e05fa-305">The user is added to the :::no-loc(Identity)::: database.</span></span>
* <span data-ttu-id="e05fa-306">系統會通知使用者，並告知使用者變更密碼。</span><span class="sxs-lookup"><span data-stu-id="e05fa-306">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="e05fa-307">下列程式碼概述如何新增使用者：</span><span class="sxs-lookup"><span data-stu-id="e05fa-307">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="e05fa-308">在生產案例中，可以遵循類似的方法。</span><span class="sxs-lookup"><span data-stu-id="e05fa-308">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e05fa-309">其他資源</span><span class="sxs-lookup"><span data-stu-id="e05fa-309">Additional resources</span></span>

* [<span data-ttu-id="e05fa-310">驗證碼變更為 ASP.NET Core 2.1 和更新版本</span><span class="sxs-lookup"><span data-stu-id="e05fa-310">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end
