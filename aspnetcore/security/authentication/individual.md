---
title: 以個別使用者帳戶所建立 ASP.NET Core 專案為基礎的文章
author: rick-anderson
description: 根據使用個別使用者帳戶所建立 ASP.NET Core 專案來探索文章。
ms.author: riande
ms.date: 12/11/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/individual
ms.openlocfilehash: 6b72612c12d52cfc1736c141bdad95ace6c84546
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022286"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a><span data-ttu-id="1bc67-103">以個別使用者帳戶所建立 ASP.NET Core 專案為基礎的文章</span><span class="sxs-lookup"><span data-stu-id="1bc67-103">Articles based on ASP.NET Core projects created with individual user accounts</span></span>

<span data-ttu-id="1bc67-104">ASP.NET Core Identity 包含在具有 [個別使用者帳戶] 選項之 Visual Studio 的專案範本中。</span><span class="sxs-lookup"><span data-stu-id="1bc67-104">ASP.NET Core Identity is included in project templates in Visual Studio with the "Individual User Accounts" option.</span></span>

<span data-ttu-id="1bc67-105">.NET Core CLI 提供驗證範本 `-au Individual` ：</span><span class="sxs-lookup"><span data-stu-id="1bc67-105">The authentication templates are available in .NET Core CLI with `-au Individual`:</span></span>

::: moniker range=">= aspnetcore-2.1"

```dotnetcli
dotnet new mvc -au Individual
dotnet new webapp -au Individual
```

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```dotnetcli
dotnet new mvc -au Individual
dotnet new razor -au Individual
```

::: moniker-end

<span data-ttu-id="1bc67-106">請參閱[此 GitHub 問題](https://github.com/dotnet/AspNetCore/issues/5833)以取得 Web API 驗證。</span><span class="sxs-lookup"><span data-stu-id="1bc67-106">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5833) for web API authentication.</span></span>

<a name="no"></a>

## <a name="no-authentication"></a><span data-ttu-id="1bc67-107">不需要驗證</span><span class="sxs-lookup"><span data-stu-id="1bc67-107">No Authentication</span></span>

<span data-ttu-id="1bc67-108">使用選項在 .NET Core CLI 中指定驗證 `-au` 。</span><span class="sxs-lookup"><span data-stu-id="1bc67-108">Authentication is specified in the .NET Core CLI with the `-au` option.</span></span> <span data-ttu-id="1bc67-109">在 Visual Studio 中，新的 web 應用程式可以使用 [**變更驗證**] 對話方塊。</span><span class="sxs-lookup"><span data-stu-id="1bc67-109">In Visual Studio, the **Change Authentication** dialog is available for new web applications.</span></span> <span data-ttu-id="1bc67-110">Visual Studio 中新 web 應用程式的預設值為 [**無驗證**]。</span><span class="sxs-lookup"><span data-stu-id="1bc67-110">The default for new web apps in Visual Studio is **No Authentication**.</span></span>

<span data-ttu-id="1bc67-111">建立不含驗證的專案：</span><span class="sxs-lookup"><span data-stu-id="1bc67-111">Projects created with no authentication:</span></span>

* <span data-ttu-id="1bc67-112">請勿包含網頁和 UI 以進行登入和登出。</span><span class="sxs-lookup"><span data-stu-id="1bc67-112">Don't contain web pages and UI to sign in and sign out.</span></span>
* <span data-ttu-id="1bc67-113">不包含驗證碼。</span><span class="sxs-lookup"><span data-stu-id="1bc67-113">Don't contain authentication code.</span></span>

<a name="win"></a>

## <a name="windows-authentication"></a><span data-ttu-id="1bc67-114">Windows 驗證</span><span class="sxs-lookup"><span data-stu-id="1bc67-114">Windows Authentication</span></span>

<span data-ttu-id="1bc67-115">在 .NET Core CLI 中使用選項，為新的 web 應用程式指定 Windows 驗證 `-au Windows` 。</span><span class="sxs-lookup"><span data-stu-id="1bc67-115">Windows Authentication is specified for new web apps in the .NET Core CLI with the `-au Windows` option.</span></span> <span data-ttu-id="1bc67-116">在 Visual Studio 中，[**變更驗證**] 對話方塊會提供**Windows 驗證**選項。</span><span class="sxs-lookup"><span data-stu-id="1bc67-116">In Visual Studio, the **Change Authentication** dialog provides the **Windows Authentication** options.</span></span>

<span data-ttu-id="1bc67-117">如果已選取 [Windows 驗證]，則會將應用程式設定為使用[Windows 驗證 IIS 模組](xref:host-and-deploy/iis/modules)。</span><span class="sxs-lookup"><span data-stu-id="1bc67-117">If Windows Authentication is selected, the app is configured to use the [Windows Authentication IIS module](xref:host-and-deploy/iis/modules).</span></span> <span data-ttu-id="1bc67-118">Windows 驗證適用于內部網路網站。</span><span class="sxs-lookup"><span data-stu-id="1bc67-118">Windows Authentication is intended for Intranet web sites.</span></span>

## <a name="dotnet-new-webapp-authentication-options"></a><span data-ttu-id="1bc67-119">dotnet 新的 webapp authentication 選項</span><span class="sxs-lookup"><span data-stu-id="1bc67-119">dotnet new webapp authentication options</span></span>

<span data-ttu-id="1bc67-120">下表顯示適用于新 web 應用程式的驗證選項：</span><span class="sxs-lookup"><span data-stu-id="1bc67-120">The following table shows the authentication options available for new web apps:</span></span>

| <span data-ttu-id="1bc67-121">選項</span><span class="sxs-lookup"><span data-stu-id="1bc67-121">Option</span></span> | <span data-ttu-id="1bc67-122">驗證類型</span><span class="sxs-lookup"><span data-stu-id="1bc67-122">Type of authentication</span></span> | <span data-ttu-id="1bc67-123">連結取得詳細資訊</span><span class="sxs-lookup"><span data-stu-id="1bc67-123">Link for more information</span></span> |
 | ----------------- | ------------ | ---------- |
| <span data-ttu-id="1bc67-124">None</span><span class="sxs-lookup"><span data-stu-id="1bc67-124">None</span></span>            |  <span data-ttu-id="1bc67-125">不需要驗證</span><span class="sxs-lookup"><span data-stu-id="1bc67-125">No authentication</span></span> | | 
| <span data-ttu-id="1bc67-126">個人</span><span class="sxs-lookup"><span data-stu-id="1bc67-126">Individual</span></span>      |  <span data-ttu-id="1bc67-127">個別驗證</span><span class="sxs-lookup"><span data-stu-id="1bc67-127">Individual authentication</span></span> | <xref:security/authentication/identity>
| <span data-ttu-id="1bc67-128">IndividualB2C</span><span class="sxs-lookup"><span data-stu-id="1bc67-128">IndividualB2C</span></span>   |  <span data-ttu-id="1bc67-129">使用 Azure AD B2C 的雲端託管個別驗證</span><span class="sxs-lookup"><span data-stu-id="1bc67-129">Cloud-hosted individual authentication with Azure AD B2C</span></span> | [<span data-ttu-id="1bc67-130">Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="1bc67-130">Azure AD B2C</span></span>](/azure/active-directory-b2c/) |
| <span data-ttu-id="1bc67-131">SingleOrg</span><span class="sxs-lookup"><span data-stu-id="1bc67-131">SingleOrg</span></span>       |  <span data-ttu-id="1bc67-132">單一租使用者的組織驗證</span><span class="sxs-lookup"><span data-stu-id="1bc67-132">Organizational authentication for a single tenant</span></span> | [<span data-ttu-id="1bc67-133">Azure AD</span><span class="sxs-lookup"><span data-stu-id="1bc67-133">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="1bc67-134">MultiOrg</span><span class="sxs-lookup"><span data-stu-id="1bc67-134">MultiOrg</span></span>        |  <span data-ttu-id="1bc67-135">多個租使用者的組織驗證</span><span class="sxs-lookup"><span data-stu-id="1bc67-135">Organizational authentication for multiple tenants</span></span> | [<span data-ttu-id="1bc67-136">Azure AD</span><span class="sxs-lookup"><span data-stu-id="1bc67-136">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="1bc67-137">Windows</span><span class="sxs-lookup"><span data-stu-id="1bc67-137">Windows</span></span>         |  <span data-ttu-id="1bc67-138">Windows 驗證</span><span class="sxs-lookup"><span data-stu-id="1bc67-138">Windows authentication</span></span> | [<span data-ttu-id="1bc67-139">Windows 驗證</span><span class="sxs-lookup"><span data-stu-id="1bc67-139">Windows Authentication</span></span>](xref:security/authentication/windowsauth)

## <a name="visual-studio-new-webapp-authentication-options"></a><span data-ttu-id="1bc67-140">Visual Studio 新的 webapp authentication 選項</span><span class="sxs-lookup"><span data-stu-id="1bc67-140">Visual Studio new webapp authentication options</span></span>

<span data-ttu-id="1bc67-141">下表顯示使用 Visual Studio 建立新的 web 應用程式時可用的驗證選項：</span><span class="sxs-lookup"><span data-stu-id="1bc67-141">The following table shows the authentication options available when creating a new web app with Visual Studio:</span></span>

| <span data-ttu-id="1bc67-142">選項</span><span class="sxs-lookup"><span data-stu-id="1bc67-142">Option</span></span> | <span data-ttu-id="1bc67-143">驗證類型</span><span class="sxs-lookup"><span data-stu-id="1bc67-143">Type of authentication</span></span> | <span data-ttu-id="1bc67-144">連結取得詳細資訊</span><span class="sxs-lookup"><span data-stu-id="1bc67-144">Link for more information</span></span> |
 | ----------------- | ------------ | ---------- |
| <span data-ttu-id="1bc67-145">None</span><span class="sxs-lookup"><span data-stu-id="1bc67-145">None</span></span>            |  <span data-ttu-id="1bc67-146">不需要驗證</span><span class="sxs-lookup"><span data-stu-id="1bc67-146">No authentication</span></span> | | 
| <span data-ttu-id="1bc67-147">個別使用者帳戶/儲存應用程式中的使用者帳戶</span><span class="sxs-lookup"><span data-stu-id="1bc67-147">Individual User Accounts / Store user accounts in-app</span></span> |  <span data-ttu-id="1bc67-148">個別驗證</span><span class="sxs-lookup"><span data-stu-id="1bc67-148">Individual authentication</span></span> | <xref:security/authentication/identity> |
| <span data-ttu-id="1bc67-149">個別使用者帳戶/連接到雲端中的現有使用者存放區</span><span class="sxs-lookup"><span data-stu-id="1bc67-149">Individual User Accounts / Connect to an existing user store in the cloud</span></span> |  <span data-ttu-id="1bc67-150">使用 Azure AD B2C 的雲端託管個別驗證</span><span class="sxs-lookup"><span data-stu-id="1bc67-150">Cloud-hosted individual authentication with Azure AD B2C</span></span> | [<span data-ttu-id="1bc67-151">Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="1bc67-151">Azure AD B2C</span></span>](/azure/active-directory-b2c/) |
| <span data-ttu-id="1bc67-152">公司或學校雲端/單一組織</span><span class="sxs-lookup"><span data-stu-id="1bc67-152">Work or School Cloud / Single Org</span></span>  |  <span data-ttu-id="1bc67-153">單一租使用者的組織驗證</span><span class="sxs-lookup"><span data-stu-id="1bc67-153">Organizational authentication for a single tenant</span></span> | [<span data-ttu-id="1bc67-154">Azure AD</span><span class="sxs-lookup"><span data-stu-id="1bc67-154">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="1bc67-155">公司或學校雲端/多組織</span><span class="sxs-lookup"><span data-stu-id="1bc67-155">Work or School Cloud / Multiple Org</span></span> |  <span data-ttu-id="1bc67-156">多個租使用者的組織驗證</span><span class="sxs-lookup"><span data-stu-id="1bc67-156">Organizational authentication for multiple tenants</span></span> | [<span data-ttu-id="1bc67-157">Azure AD</span><span class="sxs-lookup"><span data-stu-id="1bc67-157">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="1bc67-158">Windows</span><span class="sxs-lookup"><span data-stu-id="1bc67-158">Windows</span></span>         |  <span data-ttu-id="1bc67-159">Windows 驗證</span><span class="sxs-lookup"><span data-stu-id="1bc67-159">Windows authentication</span></span> | [<span data-ttu-id="1bc67-160">Windows 驗證</span><span class="sxs-lookup"><span data-stu-id="1bc67-160">Windows Authentication</span></span>](xref:security/authentication/windowsauth)

## <a name="additional-resources"></a><span data-ttu-id="1bc67-161">其他資源</span><span class="sxs-lookup"><span data-stu-id="1bc67-161">Additional resources</span></span>

<span data-ttu-id="1bc67-162">下列文章示範如何使用在 ASP.NET Core 範本中產生的程式碼，以使用個別使用者帳戶：</span><span class="sxs-lookup"><span data-stu-id="1bc67-162">The following articles show how to use the code generated in ASP.NET Core templates that use individual user accounts:</span></span>

* [<span data-ttu-id="1bc67-163">ASP.NET Core 中的帳戶確認和密碼復原</span><span class="sxs-lookup"><span data-stu-id="1bc67-163">Account confirmation and password recovery in ASP.NET Core</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="1bc67-164">建立具有受授權保護之使用者資料的 ASP.NET Core 應用程式</span><span class="sxs-lookup"><span data-stu-id="1bc67-164">Create an ASP.NET Core app with user data protected by authorization</span></span>](xref:security/authorization/secure-data)
