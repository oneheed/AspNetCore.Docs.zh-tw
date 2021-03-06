---
title: IdentityASP.NET Core 簡介
author: rick-anderson
description: Identity與 ASP.NET Core 應用程式搭配使用。 瞭解如何設定 (RequireDigit、RequiredLength、RequiredUniqueChars 等) 的密碼需求。
ms.author: riande
ms.date: 7/15/2020
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
uid: security/authentication/identity
ms.openlocfilehash: 4fa49f795b78b88e00bd32d04f74acd8689383b2
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2021
ms.locfileid: "102394469"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="08cd0-104">IdentityASP.NET Core 簡介</span><span class="sxs-lookup"><span data-stu-id="08cd0-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="08cd0-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="08cd0-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="08cd0-106">ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="08cd0-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="08cd0-107">是支援使用者介面 (UI) 登入功能的 API。</span><span class="sxs-lookup"><span data-stu-id="08cd0-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="08cd0-108">管理使用者、密碼、設定檔資料、角色、宣告、權杖、電子郵件確認等。</span><span class="sxs-lookup"><span data-stu-id="08cd0-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="08cd0-109">使用者可以使用儲存在中的登入資訊來建立帳戶， Identity 也可以使用外部登入提供者。</span><span class="sxs-lookup"><span data-stu-id="08cd0-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="08cd0-110">支援的外部登入提供者包括 [Facebook、Google、Microsoft 帳戶和 Twitter](xref:security/authentication/social/index)。</span><span class="sxs-lookup"><span data-stu-id="08cd0-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

[!INCLUDE[](~/includes/requireAuth.md)]

<span data-ttu-id="08cd0-111">[ Identity 原始程式碼](https://github.com/dotnet/AspNetCore/tree/main/src/Identity)可在 GitHub 上取得。</span><span class="sxs-lookup"><span data-stu-id="08cd0-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/main/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="08cd0-112">[Scaffold Identity ](xref:security/authentication/scaffold-identity)並查看產生的檔案，以檢查與的範本互動 Identity 。</span><span class="sxs-lookup"><span data-stu-id="08cd0-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

<span data-ttu-id="08cd0-113">Identity 通常會使用 SQL Server 資料庫來設定，以儲存使用者名稱、密碼和設定檔資料。</span><span class="sxs-lookup"><span data-stu-id="08cd0-113">Identity is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="08cd0-114">或者，您也可以使用另一個持續性存放區，例如 Azure 表格儲存體。</span><span class="sxs-lookup"><span data-stu-id="08cd0-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="08cd0-115">在本主題中，您將瞭解如何使用 Identity 註冊、登入和登出使用者。</span><span class="sxs-lookup"><span data-stu-id="08cd0-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="08cd0-116">注意：範本會將使用者的使用者名稱和電子郵件視為相同。</span><span class="sxs-lookup"><span data-stu-id="08cd0-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="08cd0-117">如需有關建立使用之應用程式的詳細指示 Identity ，請參閱 [後續步驟](#next)。</span><span class="sxs-lookup"><span data-stu-id="08cd0-117">For more detailed instructions about creating apps that use Identity, see [Next Steps](#next).</span></span>

<span data-ttu-id="08cd0-118">[Microsoft 身分識別平臺](/azure/active-directory/develop/) 為：</span><span class="sxs-lookup"><span data-stu-id="08cd0-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="08cd0-119">Azure Active Directory (Azure AD) 開發人員平臺的演化。</span><span class="sxs-lookup"><span data-stu-id="08cd0-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="08cd0-120">與無關 ASP.NET Core Identity 。</span><span class="sxs-lookup"><span data-stu-id="08cd0-120">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="08cd0-121">[請參閱或下載範例程式碼，](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([如何下載](xref:index#how-to-download-a-sample)) 。</span><span class="sxs-lookup"><span data-stu-id="08cd0-121">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="08cd0-122">建立具有驗證的 Web 應用程式</span><span class="sxs-lookup"><span data-stu-id="08cd0-122">Create a Web app with authentication</span></span>

<span data-ttu-id="08cd0-123">使用個別的使用者帳戶建立 ASP.NET Core Web 應用程式專案。</span><span class="sxs-lookup"><span data-stu-id="08cd0-123">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="08cd0-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="08cd0-124">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="08cd0-125">選取 **[** 檔案 > **新增** > **專案**]。</span><span class="sxs-lookup"><span data-stu-id="08cd0-125">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="08cd0-126">選取 **ASP.NET Core Web 應用程式**。</span><span class="sxs-lookup"><span data-stu-id="08cd0-126">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="08cd0-127">將專案命名為 **WebApp1** ，使其具有與專案下載相同的命名空間。</span><span class="sxs-lookup"><span data-stu-id="08cd0-127">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="08cd0-128">按一下 [確定]  。</span><span class="sxs-lookup"><span data-stu-id="08cd0-128">Click **OK**.</span></span>
* <span data-ttu-id="08cd0-129">選取 ASP.NET Core **Web 應用程式**，然後選取 [ **變更驗證**]。</span><span class="sxs-lookup"><span data-stu-id="08cd0-129">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="08cd0-130">選取 **個別使用者帳戶** ，然後按一下 **[確定]**。</span><span class="sxs-lookup"><span data-stu-id="08cd0-130">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="08cd0-131">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="08cd0-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="08cd0-132">上述命令會 Razor 使用 SQLite 來建立 web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="08cd0-132">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="08cd0-133">若要建立具有 LocalDB 的 web 應用程式，請執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="08cd0-133">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="08cd0-134">產生的專案會 [ASP.NET Core Identity](xref:security/authentication/identity) 以[ Razor 類別庫](xref:razor-pages/ui-class)的形式提供。</span><span class="sxs-lookup"><span data-stu-id="08cd0-134">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="08cd0-135">Identity Razor 類別庫會以區域公開端點 `Identity` 。</span><span class="sxs-lookup"><span data-stu-id="08cd0-135">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="08cd0-136">例如：</span><span class="sxs-lookup"><span data-stu-id="08cd0-136">For example:</span></span>

* <span data-ttu-id="08cd0-137">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="08cd0-137">/Identity/Account/Login</span></span>
* <span data-ttu-id="08cd0-138">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="08cd0-138">/Identity/Account/Logout</span></span>
* <span data-ttu-id="08cd0-139">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="08cd0-139">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="08cd0-140">套用移轉</span><span class="sxs-lookup"><span data-stu-id="08cd0-140">Apply migrations</span></span>

<span data-ttu-id="08cd0-141">套用遷移以將資料庫初始化。</span><span class="sxs-lookup"><span data-stu-id="08cd0-141">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="08cd0-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="08cd0-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="08cd0-143">在套件管理員主控台中執行下列命令 (PMC) ：</span><span class="sxs-lookup"><span data-stu-id="08cd0-143">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="08cd0-144">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="08cd0-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="08cd0-145">使用 SQLite 時，在此步驟中不需要進行遷移。</span><span class="sxs-lookup"><span data-stu-id="08cd0-145">Migrations are not necessary at this step when using SQLite.</span></span>

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="08cd0-146">若是 LocalDB，請執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="08cd0-146">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="08cd0-147">測試註冊和登入</span><span class="sxs-lookup"><span data-stu-id="08cd0-147">Test Register and Login</span></span>

<span data-ttu-id="08cd0-148">執行應用程式並註冊使用者。</span><span class="sxs-lookup"><span data-stu-id="08cd0-148">Run the app and register a user.</span></span> <span data-ttu-id="08cd0-149">根據您的螢幕大小，您可能需要選取 [流覽] 切換按鈕，才能看到 [ **註冊** ] 和 [ **登** 入] 連結。</span><span class="sxs-lookup"><span data-stu-id="08cd0-149">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="08cd0-150">設定 Identity 服務</span><span class="sxs-lookup"><span data-stu-id="08cd0-150">Configure Identity services</span></span>

<span data-ttu-id="08cd0-151">服務會新增至 `ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="08cd0-151">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="08cd0-152">典型模式是呼叫所有 `Add{Service}` 方法，然後呼叫 `services.Configure{Service}` 方法。</span><span class="sxs-lookup"><span data-stu-id="08cd0-152">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=11-99)]

<span data-ttu-id="08cd0-153">上述反白顯示的程式碼會 Identity 使用預設選項值進行設定。</span><span class="sxs-lookup"><span data-stu-id="08cd0-153">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="08cd0-154">服務可透過相依性 [插入](xref:fundamentals/dependency-injection)，提供給應用程式使用。</span><span class="sxs-lookup"><span data-stu-id="08cd0-154">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="08cd0-155">Identity 藉由呼叫來啟用 <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> 。</span><span class="sxs-lookup"><span data-stu-id="08cd0-155">Identity is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="08cd0-156">`UseAuthentication` 將驗證 [中介軟體](xref:fundamentals/middleware/index) 新增至要求管線。</span><span class="sxs-lookup"><span data-stu-id="08cd0-156">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](identity/sample/WebApp5x/Startup.cs?name=snippet_configureservices&highlight=12-99)]

<span data-ttu-id="08cd0-157">上述程式碼會 Identity 使用預設選項值進行設定。</span><span class="sxs-lookup"><span data-stu-id="08cd0-157">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="08cd0-158">服務可透過相依性 [插入](xref:fundamentals/dependency-injection)，提供給應用程式使用。</span><span class="sxs-lookup"><span data-stu-id="08cd0-158">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="08cd0-159">Identity 藉由呼叫 [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)來啟用。</span><span class="sxs-lookup"><span data-stu-id="08cd0-159">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="08cd0-160">`UseAuthentication` 將驗證 [中介軟體](xref:fundamentals/middleware/index) 新增至要求管線。</span><span class="sxs-lookup"><span data-stu-id="08cd0-160">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp5x/Startup.cs?name=snippet_configure&highlight=19)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="08cd0-161">範本產生的應用程式不會使用 [授權](xref:security/authorization/secure-data)。</span><span class="sxs-lookup"><span data-stu-id="08cd0-161">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="08cd0-162">`app.UseAuthorization` 包含以確保應用程式新增授權時，會以正確的順序新增。</span><span class="sxs-lookup"><span data-stu-id="08cd0-162">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="08cd0-163">`UseRouting`、 `UseAuthentication` 、 `UseAuthorization` 和 `UseEndpoints` 必須依照上述程式碼中所示的順序來呼叫。</span><span class="sxs-lookup"><span data-stu-id="08cd0-163">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="08cd0-164">如需和的詳細資訊 `IdentityOptions` `Startup` ，請參閱 <xref:Microsoft.AspNetCore.Identity.IdentityOptions> 和 [應用程式啟動](xref:fundamentals/startup)。</span><span class="sxs-lookup"><span data-stu-id="08cd0-164">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-logout-and-registerconfirmation"></a><span data-ttu-id="08cd0-165">Scaffold 註冊、登入、登出及 RegisterConfirmation</span><span class="sxs-lookup"><span data-stu-id="08cd0-165">Scaffold Register, Login, LogOut, and RegisterConfirmation</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="08cd0-166">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="08cd0-166">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="08cd0-167">加入 `Register` 、、 `Login` 和檔案 `LogOut` `RegisterConfirmation` 。</span><span class="sxs-lookup"><span data-stu-id="08cd0-167">Add the `Register`, `Login`, `LogOut`, and `RegisterConfirmation` files.</span></span> <span data-ttu-id="08cd0-168">將 [Scaffold 身分識別納入 Razor 具有授權](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) 指示的專案，以產生本節所示的程式碼。</span><span class="sxs-lookup"><span data-stu-id="08cd0-168">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="08cd0-169">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="08cd0-169">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="08cd0-170">如果您已建立名為 **WebApp1** 的專案，請執行下列命令。</span><span class="sxs-lookup"><span data-stu-id="08cd0-170">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="08cd0-171">否則，請使用正確的命名空間 `ApplicationDbContext` ：</span><span class="sxs-lookup"><span data-stu-id="08cd0-171">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.RegisterConfirmation"
```

<span data-ttu-id="08cd0-172">PowerShell 使用分號做為命令分隔符號。</span><span class="sxs-lookup"><span data-stu-id="08cd0-172">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="08cd0-173">使用 PowerShell 時，請在檔案清單中將分號 escape，或將檔案清單放在雙引號中，如上述範例所示。</span><span class="sxs-lookup"><span data-stu-id="08cd0-173">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="08cd0-174">如需有關樣板的詳細資訊 Identity ，請參閱 [ Razor 使用授權將身分識別 Scaffold 至專案](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)。</span><span class="sxs-lookup"><span data-stu-id="08cd0-174">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="08cd0-175">檢查註冊</span><span class="sxs-lookup"><span data-stu-id="08cd0-175">Examine Register</span></span>

<span data-ttu-id="08cd0-176">當使用者按一下頁面上的 [ **註冊** ] 按鈕時 `Register` ，會叫用該 `RegisterModel.OnPostAsync` 動作。</span><span class="sxs-lookup"><span data-stu-id="08cd0-176">When a user clicks the **Register** button on the `Register` page, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="08cd0-177">使用者是在物件上 [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) 建立的 `_userManager` ：</span><span class="sxs-lookup"><span data-stu-id="08cd0-177">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/Identity/UI/src/Areas/Identity/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->
[!INCLUDE[](~/includes/disableVer.md)]

### <a name="log-in"></a><span data-ttu-id="08cd0-178">登入</span><span class="sxs-lookup"><span data-stu-id="08cd0-178">Log in</span></span>

<span data-ttu-id="08cd0-179">登入表單會在下列情況顯示：</span><span class="sxs-lookup"><span data-stu-id="08cd0-179">The Login form is displayed when:</span></span>

* <span data-ttu-id="08cd0-180">已選取 [ **登入** ] 連結。</span><span class="sxs-lookup"><span data-stu-id="08cd0-180">The **Log in** link is selected.</span></span>
* <span data-ttu-id="08cd0-181">使用者嘗試存取未獲授權存取 **或** 未經過系統驗證的受限頁面。</span><span class="sxs-lookup"><span data-stu-id="08cd0-181">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="08cd0-182">提交登入頁面上的表單時， `OnPostAsync` 會呼叫動作。</span><span class="sxs-lookup"><span data-stu-id="08cd0-182">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="08cd0-183">`PasswordSignInAsync` 在物件上呼叫 `_signInManager` 。</span><span class="sxs-lookup"><span data-stu-id="08cd0-183">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="08cd0-184">如需如何進行授權決策的詳細資訊，請參閱 <xref:security/authorization/introduction> 。</span><span class="sxs-lookup"><span data-stu-id="08cd0-184">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="08cd0-185">登出</span><span class="sxs-lookup"><span data-stu-id="08cd0-185">Log out</span></span>

<span data-ttu-id="08cd0-186">**登出** 連結會叫用 `LogoutModel.OnPost` 動作。</span><span class="sxs-lookup"><span data-stu-id="08cd0-186">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="08cd0-187">在上述程式碼中， `return RedirectToPage();` 程式碼必須是重新導向，才能讓瀏覽器執行新的要求，並更新使用者的身分識別。</span><span class="sxs-lookup"><span data-stu-id="08cd0-187">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="08cd0-188">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) 會清除儲存在中的使用者宣告 cookie 。</span><span class="sxs-lookup"><span data-stu-id="08cd0-188">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="08cd0-189">Post 是在 *Pages/Shared/_LoginPartial 中指定。 cshtml*：</span><span class="sxs-lookup"><span data-stu-id="08cd0-189">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="08cd0-190">測試 Identity</span><span class="sxs-lookup"><span data-stu-id="08cd0-190">Test Identity</span></span>

<span data-ttu-id="08cd0-191">預設的 Web 專案範本允許匿名存取首頁。</span><span class="sxs-lookup"><span data-stu-id="08cd0-191">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="08cd0-192">若要測試 Identity ，請新增 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) ：</span><span class="sxs-lookup"><span data-stu-id="08cd0-192">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="08cd0-193">如果您已登入，請登出。執行應用程式，然後選取 [ **隱私權** ] 連結。</span><span class="sxs-lookup"><span data-stu-id="08cd0-193">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="08cd0-194">系統會將您重新導向至 [登入] 頁面。</span><span class="sxs-lookup"><span data-stu-id="08cd0-194">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="08cd0-195">探討 Identity</span><span class="sxs-lookup"><span data-stu-id="08cd0-195">Explore Identity</span></span>

<span data-ttu-id="08cd0-196">若要 Identity 更詳細地探索：</span><span class="sxs-lookup"><span data-stu-id="08cd0-196">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="08cd0-197">建立完整身分識別 UI 來源</span><span class="sxs-lookup"><span data-stu-id="08cd0-197">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="08cd0-198">檢查每個頁面的來源，並逐步執行偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="08cd0-198">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="08cd0-199">Identity 元件</span><span class="sxs-lookup"><span data-stu-id="08cd0-199">Identity Components</span></span>

<span data-ttu-id="08cd0-200">所有 Identity 相依的 NuGet 套件都包含在 [ASP.NET Core 共用架構](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework)中。</span><span class="sxs-lookup"><span data-stu-id="08cd0-200">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="08cd0-201">的主要封裝 Identity 是[AspNetCore。 Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)</span><span class="sxs-lookup"><span data-stu-id="08cd0-201">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="08cd0-202">此封裝包含的一組核心介面 ASP.NET Core Identity ，而且會包含在內 `Microsoft.AspNetCore.Identity.EntityFrameworkCore` 。</span><span class="sxs-lookup"><span data-stu-id="08cd0-202">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="08cd0-203">遷移至 ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="08cd0-203">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="08cd0-204">如需有關遷移現有存放區的詳細資訊和指引 Identity ，請參閱[遷移驗證和 Identity ](xref:migration/identity)。</span><span class="sxs-lookup"><span data-stu-id="08cd0-204">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="08cd0-205">設定密碼強度</span><span class="sxs-lookup"><span data-stu-id="08cd0-205">Setting password strength</span></span>

<span data-ttu-id="08cd0-206">如需設定最小密碼 [需求的範例](#pw) ，請參閱設定。</span><span class="sxs-lookup"><span data-stu-id="08cd0-206">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="08cd0-207">AddDefault Identity 並新增Identity</span><span class="sxs-lookup"><span data-stu-id="08cd0-207">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="08cd0-208"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> 已在 ASP.NET Core 2.1 中引進。</span><span class="sxs-lookup"><span data-stu-id="08cd0-208"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="08cd0-209">呼叫 `AddDefaultIdentity` 類似于呼叫下列各項：</span><span class="sxs-lookup"><span data-stu-id="08cd0-209">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="08cd0-210">如需詳細資訊，請參閱 [AddDefault Identity 來源](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) 。</span><span class="sxs-lookup"><span data-stu-id="08cd0-210">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="08cd0-211">防止發佈靜態 Identity 資產</span><span class="sxs-lookup"><span data-stu-id="08cd0-211">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="08cd0-212">若要避免將靜態 Identity 資產發佈 (的樣式表單和 JavaScript 檔案，以供 Identity UI) 至 web 根目錄，請將下列 `ResolveStaticWebAssetsInputsDependsOn` 屬性和 `RemoveIdentityAssets` 目標加入應用程式的專案檔：</span><span class="sxs-lookup"><span data-stu-id="08cd0-212">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

```xml
<PropertyGroup>
  <ResolveStaticWebAssetsInputsDependsOn>RemoveIdentityAssets</ResolveStaticWebAssetsInputsDependsOn>
</PropertyGroup>

<Target Name="RemoveIdentityAssets">
  <ItemGroup>
    <StaticWebAsset Remove="@(StaticWebAsset)" Condition="%(SourceId) == 'Microsoft.AspNetCore.Identity.UI'" />
  </ItemGroup>
</Target>
```

<a name="next"></a>

## <a name="next-steps"></a><span data-ttu-id="08cd0-213">後續步驟</span><span class="sxs-lookup"><span data-stu-id="08cd0-213">Next Steps</span></span>

* <span data-ttu-id="08cd0-214">[ASP.NET Core Identity 原始程式碼](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span><span class="sxs-lookup"><span data-stu-id="08cd0-214">[ASP.NET Core Identity source code](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span></span>
* <span data-ttu-id="08cd0-215">如需使用 SQLite 進行設定的相關資訊，請參閱 [此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/5131) Identity 。</span><span class="sxs-lookup"><span data-stu-id="08cd0-215">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="08cd0-216">設定 Identity</span><span class="sxs-lookup"><span data-stu-id="08cd0-216">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="08cd0-217">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="08cd0-217">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="08cd0-218">ASP.NET Core Identity 是將登入功能新增至 ASP.NET Core 應用程式的成員系統。</span><span class="sxs-lookup"><span data-stu-id="08cd0-218">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="08cd0-219">使用者可以使用儲存在中的登入資訊來建立帳戶， Identity 也可以使用外部登入提供者。</span><span class="sxs-lookup"><span data-stu-id="08cd0-219">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="08cd0-220">支援的外部登入提供者包括 [Facebook、Google、Microsoft 帳戶和 Twitter](xref:security/authentication/social/index)。</span><span class="sxs-lookup"><span data-stu-id="08cd0-220">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="08cd0-221">Identity 可以使用 SQL Server 資料庫來設定，以儲存使用者名稱、密碼和設定檔資料。</span><span class="sxs-lookup"><span data-stu-id="08cd0-221">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="08cd0-222">或者，您也可以使用另一個持續性存放區，例如 Azure 表格儲存體。</span><span class="sxs-lookup"><span data-stu-id="08cd0-222">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="08cd0-223">[請參閱或下載範例程式碼，](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([如何下載](xref:index#how-to-download-a-sample)) 。</span><span class="sxs-lookup"><span data-stu-id="08cd0-223">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="08cd0-224">在本主題中，您將瞭解如何使用 Identity 註冊、登入和登出使用者。</span><span class="sxs-lookup"><span data-stu-id="08cd0-224">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="08cd0-225">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span><span class="sxs-lookup"><span data-stu-id="08cd0-225">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="08cd0-226">AddDefault Identity 並新增Identity</span><span class="sxs-lookup"><span data-stu-id="08cd0-226">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="08cd0-227"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> 已在 ASP.NET Core 2.1 中引進。</span><span class="sxs-lookup"><span data-stu-id="08cd0-227"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="08cd0-228">呼叫 `AddDefaultIdentity` 類似于呼叫下列各項：</span><span class="sxs-lookup"><span data-stu-id="08cd0-228">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="08cd0-229">如需詳細資訊，請參閱 [AddDefault Identity 來源](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) 。</span><span class="sxs-lookup"><span data-stu-id="08cd0-229">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="08cd0-230">建立具有驗證的 Web 應用程式</span><span class="sxs-lookup"><span data-stu-id="08cd0-230">Create a Web app with authentication</span></span>

<span data-ttu-id="08cd0-231">使用個別的使用者帳戶建立 ASP.NET Core Web 應用程式專案。</span><span class="sxs-lookup"><span data-stu-id="08cd0-231">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="08cd0-232">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="08cd0-232">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="08cd0-233">選取 **[** 檔案 > **新增** > **專案**]。</span><span class="sxs-lookup"><span data-stu-id="08cd0-233">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="08cd0-234">選取 **ASP.NET Core Web 應用程式**。</span><span class="sxs-lookup"><span data-stu-id="08cd0-234">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="08cd0-235">將專案命名為 **WebApp1** ，使其具有與專案下載相同的命名空間。</span><span class="sxs-lookup"><span data-stu-id="08cd0-235">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="08cd0-236">按一下 [確定]  。</span><span class="sxs-lookup"><span data-stu-id="08cd0-236">Click **OK**.</span></span>
* <span data-ttu-id="08cd0-237">選取 ASP.NET Core **Web 應用程式**，然後選取 [ **變更驗證**]。</span><span class="sxs-lookup"><span data-stu-id="08cd0-237">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="08cd0-238">選取 **個別使用者帳戶** ，然後按一下 **[確定]**。</span><span class="sxs-lookup"><span data-stu-id="08cd0-238">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="08cd0-239">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="08cd0-239">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="08cd0-240">產生的專案會 [ASP.NET Core Identity](xref:security/authentication/identity) 以[ Razor 類別庫](xref:razor-pages/ui-class)的形式提供。</span><span class="sxs-lookup"><span data-stu-id="08cd0-240">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="08cd0-241">Identity Razor 類別庫會以區域公開端點 `Identity` 。</span><span class="sxs-lookup"><span data-stu-id="08cd0-241">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="08cd0-242">例如：</span><span class="sxs-lookup"><span data-stu-id="08cd0-242">For example:</span></span>

* <span data-ttu-id="08cd0-243">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="08cd0-243">/Identity/Account/Login</span></span>
* <span data-ttu-id="08cd0-244">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="08cd0-244">/Identity/Account/Logout</span></span>
* <span data-ttu-id="08cd0-245">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="08cd0-245">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="08cd0-246">套用移轉</span><span class="sxs-lookup"><span data-stu-id="08cd0-246">Apply migrations</span></span>

<span data-ttu-id="08cd0-247">套用遷移以將資料庫初始化。</span><span class="sxs-lookup"><span data-stu-id="08cd0-247">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="08cd0-248">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="08cd0-248">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="08cd0-249">在套件管理員主控台中執行下列命令 (PMC) ：</span><span class="sxs-lookup"><span data-stu-id="08cd0-249">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="08cd0-250">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="08cd0-250">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="08cd0-251">測試註冊和登入</span><span class="sxs-lookup"><span data-stu-id="08cd0-251">Test Register and Login</span></span>

<span data-ttu-id="08cd0-252">執行應用程式並註冊使用者。</span><span class="sxs-lookup"><span data-stu-id="08cd0-252">Run the app and register a user.</span></span> <span data-ttu-id="08cd0-253">根據您的螢幕大小，您可能需要選取 [流覽] 切換按鈕，才能看到 [ **註冊** ] 和 [ **登** 入] 連結。</span><span class="sxs-lookup"><span data-stu-id="08cd0-253">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="08cd0-254">設定 Identity 服務</span><span class="sxs-lookup"><span data-stu-id="08cd0-254">Configure Identity services</span></span>

<span data-ttu-id="08cd0-255">服務會新增至 `ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="08cd0-255">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="08cd0-256">典型模式是呼叫所有 `Add{Service}` 方法，然後呼叫 `services.Configure{Service}` 方法。</span><span class="sxs-lookup"><span data-stu-id="08cd0-256">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

<span data-ttu-id="08cd0-257">上述程式碼會 Identity 使用預設選項值進行設定。</span><span class="sxs-lookup"><span data-stu-id="08cd0-257">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="08cd0-258">服務可透過相依性 [插入](xref:fundamentals/dependency-injection)，提供給應用程式使用。</span><span class="sxs-lookup"><span data-stu-id="08cd0-258">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="08cd0-259">Identity 藉由呼叫 [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)來啟用。</span><span class="sxs-lookup"><span data-stu-id="08cd0-259">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="08cd0-260">`UseAuthentication` 將驗證 [中介軟體](xref:fundamentals/middleware/index) 新增至要求管線。</span><span class="sxs-lookup"><span data-stu-id="08cd0-260">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="08cd0-261">如需詳細資訊，請參閱[ Identity Options 類別](/dotnet/api/microsoft.aspnetcore.identity.identityoptions)和[應用程式啟動](xref:fundamentals/startup)。</span><span class="sxs-lookup"><span data-stu-id="08cd0-261">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="08cd0-262">Scaffold 註冊、登入和登出</span><span class="sxs-lookup"><span data-stu-id="08cd0-262">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="08cd0-263">將 [Scaffold 身分識別納入 Razor 具有授權](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) 指示的專案，以產生本節所示的程式碼。</span><span class="sxs-lookup"><span data-stu-id="08cd0-263">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="08cd0-264">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="08cd0-264">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="08cd0-265">新增註冊、登入和登出檔案。</span><span class="sxs-lookup"><span data-stu-id="08cd0-265">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="08cd0-266">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="08cd0-266">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="08cd0-267">如果您已建立名為 **WebApp1** 的專案，請執行下列命令。</span><span class="sxs-lookup"><span data-stu-id="08cd0-267">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="08cd0-268">否則，請使用正確的命名空間 `ApplicationDbContext` ：</span><span class="sxs-lookup"><span data-stu-id="08cd0-268">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

<span data-ttu-id="08cd0-269">使用 SQLite 時， `--useSqLite` 必須指定：</span><span class="sxs-lookup"><span data-stu-id="08cd0-269">When using SQLite, `--useSqLite` must be specified:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout" --useSqLite
```

<span data-ttu-id="08cd0-270">使用 SQL Express 時，請使用下列命令：</span><span class="sxs-lookup"><span data-stu-id="08cd0-270">With SQL Express, use the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="08cd0-271">PowerShell 使用分號做為命令分隔符號。</span><span class="sxs-lookup"><span data-stu-id="08cd0-271">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="08cd0-272">使用 PowerShell 時，請在檔案清單中將分號 escape，或將檔案清單放在雙引號中，如上述範例所示。</span><span class="sxs-lookup"><span data-stu-id="08cd0-272">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="08cd0-273">檢查註冊</span><span class="sxs-lookup"><span data-stu-id="08cd0-273">Examine Register</span></span>

<span data-ttu-id="08cd0-274">當使用者按一下 [ **註冊** ] 連結時， `RegisterModel.OnPostAsync` 會叫用動作。</span><span class="sxs-lookup"><span data-stu-id="08cd0-274">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="08cd0-275">使用者是在物件上 [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) 建立的 `_userManager` ：</span><span class="sxs-lookup"><span data-stu-id="08cd0-275">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="08cd0-276">如果成功建立使用者，則會透過呼叫來登入使用者 `_signInManager.SignInAsync` 。</span><span class="sxs-lookup"><span data-stu-id="08cd0-276">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="08cd0-277">**注意：** 請參閱 [帳戶確認](xref:security/authentication/accconfirm#prevent-login-at-registration) ，以瞭解在註冊時防止立即登入的步驟。</span><span class="sxs-lookup"><span data-stu-id="08cd0-277">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="08cd0-278">登入</span><span class="sxs-lookup"><span data-stu-id="08cd0-278">Log in</span></span>

<span data-ttu-id="08cd0-279">登入表單會在下列情況顯示：</span><span class="sxs-lookup"><span data-stu-id="08cd0-279">The Login form is displayed when:</span></span>

* <span data-ttu-id="08cd0-280">已選取 [ **登入** ] 連結。</span><span class="sxs-lookup"><span data-stu-id="08cd0-280">The **Log in** link is selected.</span></span>
* <span data-ttu-id="08cd0-281">使用者嘗試存取未獲授權存取 **或** 未經過系統驗證的受限頁面。</span><span class="sxs-lookup"><span data-stu-id="08cd0-281">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="08cd0-282">提交登入頁面上的表單時， `OnPostAsync` 會呼叫動作。</span><span class="sxs-lookup"><span data-stu-id="08cd0-282">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="08cd0-283">`PasswordSignInAsync` 在物件上呼叫 `_signInManager` 。</span><span class="sxs-lookup"><span data-stu-id="08cd0-283">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="08cd0-284">如需如何進行授權決策的詳細資訊，請參閱 <xref:security/authorization/introduction> 。</span><span class="sxs-lookup"><span data-stu-id="08cd0-284">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="08cd0-285">登出</span><span class="sxs-lookup"><span data-stu-id="08cd0-285">Log out</span></span>

<span data-ttu-id="08cd0-286">**登出** 連結會叫用 `LogoutModel.OnPost` 動作。</span><span class="sxs-lookup"><span data-stu-id="08cd0-286">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="08cd0-287">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) 會清除儲存在中的使用者宣告 cookie 。</span><span class="sxs-lookup"><span data-stu-id="08cd0-287">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="08cd0-288">Post 是在 *Pages/Shared/_LoginPartial 中指定。 cshtml*：</span><span class="sxs-lookup"><span data-stu-id="08cd0-288">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="08cd0-289">測試 Identity</span><span class="sxs-lookup"><span data-stu-id="08cd0-289">Test Identity</span></span>

<span data-ttu-id="08cd0-290">預設的 Web 專案範本允許匿名存取首頁。</span><span class="sxs-lookup"><span data-stu-id="08cd0-290">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="08cd0-291">若要進行測試 Identity ，請將加入 [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) [隱私權] 頁面。</span><span class="sxs-lookup"><span data-stu-id="08cd0-291">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="08cd0-292">如果您已登入，請登出。執行應用程式，然後選取 [ **隱私權** ] 連結。</span><span class="sxs-lookup"><span data-stu-id="08cd0-292">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="08cd0-293">系統會將您重新導向至 [登入] 頁面。</span><span class="sxs-lookup"><span data-stu-id="08cd0-293">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="08cd0-294">探討 Identity</span><span class="sxs-lookup"><span data-stu-id="08cd0-294">Explore Identity</span></span>

<span data-ttu-id="08cd0-295">若要 Identity 更詳細地探索：</span><span class="sxs-lookup"><span data-stu-id="08cd0-295">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="08cd0-296">建立完整身分識別 UI 來源</span><span class="sxs-lookup"><span data-stu-id="08cd0-296">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="08cd0-297">檢查每個頁面的來源，並逐步執行偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="08cd0-297">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="08cd0-298">Identity 元件</span><span class="sxs-lookup"><span data-stu-id="08cd0-298">Identity Components</span></span>

<span data-ttu-id="08cd0-299">所有 Identity 相依的 NuGet 套件都包含在 [AspNetCore 中繼套件](xref:fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="08cd0-299">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="08cd0-300">的主要封裝 Identity 是[AspNetCore。 Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)</span><span class="sxs-lookup"><span data-stu-id="08cd0-300">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="08cd0-301">此封裝包含的一組核心介面 ASP.NET Core Identity ，而且會包含在內 `Microsoft.AspNetCore.Identity.EntityFrameworkCore` 。</span><span class="sxs-lookup"><span data-stu-id="08cd0-301">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="08cd0-302">遷移至 ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="08cd0-302">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="08cd0-303">如需有關遷移現有存放區的詳細資訊和指引 Identity ，請參閱[遷移驗證和 Identity ](xref:migration/identity)。</span><span class="sxs-lookup"><span data-stu-id="08cd0-303">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="08cd0-304">設定密碼強度</span><span class="sxs-lookup"><span data-stu-id="08cd0-304">Setting password strength</span></span>

<span data-ttu-id="08cd0-305">如需設定最小密碼 [需求的範例](#pw) ，請參閱設定。</span><span class="sxs-lookup"><span data-stu-id="08cd0-305">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="08cd0-306">後續步驟</span><span class="sxs-lookup"><span data-stu-id="08cd0-306">Next Steps</span></span>

* <span data-ttu-id="08cd0-307">如需使用 SQLite 進行設定的相關資訊，請參閱 [此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/5131) Identity 。</span><span class="sxs-lookup"><span data-stu-id="08cd0-307">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="08cd0-308">設定 Identity</span><span class="sxs-lookup"><span data-stu-id="08cd0-308">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
