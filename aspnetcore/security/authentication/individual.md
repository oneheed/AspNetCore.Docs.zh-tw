---
title: 以個別使用者帳戶建立的 ASP.NET Core 專案為基礎的文章
author: rick-anderson
description: 探索以個別使用者帳戶建立之 ASP.NET Core 專案為基礎的文章。
ms.author: riande
ms.date: 12/11/2019
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
uid: security/authentication/individual
ms.openlocfilehash: 0cbde7cf0be830dab9dd094df6d102d81f9f0949
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632612"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a><span data-ttu-id="ee024-103">以個別使用者帳戶建立的 ASP.NET Core 專案為基礎的文章</span><span class="sxs-lookup"><span data-stu-id="ee024-103">Articles based on ASP.NET Core projects created with individual user accounts</span></span>

<span data-ttu-id="ee024-104">ASP.NET Core Identity 包含在具有「個別使用者帳戶」選項 Visual Studio 的專案範本中。</span><span class="sxs-lookup"><span data-stu-id="ee024-104">ASP.NET Core Identity is included in project templates in Visual Studio with the "Individual User Accounts" option.</span></span>

<span data-ttu-id="ee024-105">驗證範本可在 .NET Core CLI 中提供 `-au Individual` ：</span><span class="sxs-lookup"><span data-stu-id="ee024-105">The authentication templates are available in .NET Core CLI with `-au Individual`:</span></span>

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

<span data-ttu-id="ee024-106">請參閱 [此 GitHub](https://github.com/dotnet/AspNetCore/issues/5833) 針對 web API 驗證的問題。</span><span class="sxs-lookup"><span data-stu-id="ee024-106">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5833) for web API authentication.</span></span>

<a name="no"></a>

## <a name="no-authentication"></a><span data-ttu-id="ee024-107">不需要驗證</span><span class="sxs-lookup"><span data-stu-id="ee024-107">No Authentication</span></span>

<span data-ttu-id="ee024-108">您可以使用選項，在 .NET Core CLI 中指定驗證 `-au` 。</span><span class="sxs-lookup"><span data-stu-id="ee024-108">Authentication is specified in the .NET Core CLI with the `-au` option.</span></span> <span data-ttu-id="ee024-109">在 Visual Studio 中，新的 web 應用程式可以使用 [ **變更驗證** ] 對話方塊。</span><span class="sxs-lookup"><span data-stu-id="ee024-109">In Visual Studio, the **Change Authentication** dialog is available for new web applications.</span></span> <span data-ttu-id="ee024-110">Visual Studio 中新 web 應用程式的預設值為 [ **無驗證**]。</span><span class="sxs-lookup"><span data-stu-id="ee024-110">The default for new web apps in Visual Studio is **No Authentication**.</span></span>

<span data-ttu-id="ee024-111">使用無驗證建立的專案：</span><span class="sxs-lookup"><span data-stu-id="ee024-111">Projects created with no authentication:</span></span>

* <span data-ttu-id="ee024-112">請勿包含要登入和登出的網頁和 UI。</span><span class="sxs-lookup"><span data-stu-id="ee024-112">Don't contain web pages and UI to sign in and sign out.</span></span>
* <span data-ttu-id="ee024-113">請勿包含驗證碼。</span><span class="sxs-lookup"><span data-stu-id="ee024-113">Don't contain authentication code.</span></span>

<a name="win"></a>

## <a name="windows-authentication"></a><span data-ttu-id="ee024-114">Windows 驗證</span><span class="sxs-lookup"><span data-stu-id="ee024-114">Windows Authentication</span></span>

<span data-ttu-id="ee024-115">您可以使用選項，在 .NET Core CLI 中為新的 web 應用程式指定 Windows 驗證 `-au Windows` 。</span><span class="sxs-lookup"><span data-stu-id="ee024-115">Windows Authentication is specified for new web apps in the .NET Core CLI with the `-au Windows` option.</span></span> <span data-ttu-id="ee024-116">在 Visual Studio 中，[ **變更驗證** ] 對話方塊會提供 **Windows 驗證** 選項。</span><span class="sxs-lookup"><span data-stu-id="ee024-116">In Visual Studio, the **Change Authentication** dialog provides the **Windows Authentication** options.</span></span>

<span data-ttu-id="ee024-117">如果選取 [Windows 驗證]，則會將應用程式設定為使用 [Windows 驗證 IIS 模組](xref:host-and-deploy/iis/modules)。</span><span class="sxs-lookup"><span data-stu-id="ee024-117">If Windows Authentication is selected, the app is configured to use the [Windows Authentication IIS module](xref:host-and-deploy/iis/modules).</span></span> <span data-ttu-id="ee024-118">Windows 驗證適用于內部網路網站。</span><span class="sxs-lookup"><span data-stu-id="ee024-118">Windows Authentication is intended for Intranet web sites.</span></span>

## <a name="dotnet-new-webapp-authentication-options"></a><span data-ttu-id="ee024-119">dotnet 新的 webapp authentication 選項</span><span class="sxs-lookup"><span data-stu-id="ee024-119">dotnet new webapp authentication options</span></span>

<span data-ttu-id="ee024-120">下表顯示適用于新 web 應用程式的驗證選項：</span><span class="sxs-lookup"><span data-stu-id="ee024-120">The following table shows the authentication options available for new web apps:</span></span>

| <span data-ttu-id="ee024-121">選項</span><span class="sxs-lookup"><span data-stu-id="ee024-121">Option</span></span> | <span data-ttu-id="ee024-122">驗證類型</span><span class="sxs-lookup"><span data-stu-id="ee024-122">Type of authentication</span></span> | <span data-ttu-id="ee024-123">連結取得詳細資訊</span><span class="sxs-lookup"><span data-stu-id="ee024-123">Link for more information</span></span> |
 | ----------------- | ------------ | ---------- |
| <span data-ttu-id="ee024-124">無</span><span class="sxs-lookup"><span data-stu-id="ee024-124">None</span></span>            |  <span data-ttu-id="ee024-125">不需要驗證</span><span class="sxs-lookup"><span data-stu-id="ee024-125">No authentication</span></span> | | 
| <span data-ttu-id="ee024-126">個人</span><span class="sxs-lookup"><span data-stu-id="ee024-126">Individual</span></span>      |  <span data-ttu-id="ee024-127">個別驗證</span><span class="sxs-lookup"><span data-stu-id="ee024-127">Individual authentication</span></span> | <xref:security/authentication/identity>
| <span data-ttu-id="ee024-128">IndividualB2C</span><span class="sxs-lookup"><span data-stu-id="ee024-128">IndividualB2C</span></span>   |  <span data-ttu-id="ee024-129">使用 Azure AD B2C 的雲端託管個別驗證</span><span class="sxs-lookup"><span data-stu-id="ee024-129">Cloud-hosted individual authentication with Azure AD B2C</span></span> | [<span data-ttu-id="ee024-130">Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="ee024-130">Azure AD B2C</span></span>](/azure/active-directory-b2c/) |
| <span data-ttu-id="ee024-131">SingleOrg</span><span class="sxs-lookup"><span data-stu-id="ee024-131">SingleOrg</span></span>       |  <span data-ttu-id="ee024-132">單一租使用者的組織驗證</span><span class="sxs-lookup"><span data-stu-id="ee024-132">Organizational authentication for a single tenant</span></span> | [<span data-ttu-id="ee024-133">Azure AD</span><span class="sxs-lookup"><span data-stu-id="ee024-133">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="ee024-134">MultiOrg</span><span class="sxs-lookup"><span data-stu-id="ee024-134">MultiOrg</span></span>        |  <span data-ttu-id="ee024-135">多個租使用者的組織驗證</span><span class="sxs-lookup"><span data-stu-id="ee024-135">Organizational authentication for multiple tenants</span></span> | [<span data-ttu-id="ee024-136">Azure AD</span><span class="sxs-lookup"><span data-stu-id="ee024-136">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="ee024-137">Windows</span><span class="sxs-lookup"><span data-stu-id="ee024-137">Windows</span></span>         |  <span data-ttu-id="ee024-138">Windows 驗證</span><span class="sxs-lookup"><span data-stu-id="ee024-138">Windows authentication</span></span> | [<span data-ttu-id="ee024-139">Windows 驗證</span><span class="sxs-lookup"><span data-stu-id="ee024-139">Windows Authentication</span></span>](xref:security/authentication/windowsauth)

## <a name="visual-studio-new-webapp-authentication-options"></a><span data-ttu-id="ee024-140">Visual Studio 新的 webapp authentication 選項</span><span class="sxs-lookup"><span data-stu-id="ee024-140">Visual Studio new webapp authentication options</span></span>

<span data-ttu-id="ee024-141">下表顯示使用 Visual Studio 建立新的 web 應用程式時可用的驗證選項：</span><span class="sxs-lookup"><span data-stu-id="ee024-141">The following table shows the authentication options available when creating a new web app with Visual Studio:</span></span>

| <span data-ttu-id="ee024-142">選項</span><span class="sxs-lookup"><span data-stu-id="ee024-142">Option</span></span> | <span data-ttu-id="ee024-143">驗證類型</span><span class="sxs-lookup"><span data-stu-id="ee024-143">Type of authentication</span></span> | <span data-ttu-id="ee024-144">連結取得詳細資訊</span><span class="sxs-lookup"><span data-stu-id="ee024-144">Link for more information</span></span> |
 | ----------------- | ------------ | ---------- |
| <span data-ttu-id="ee024-145">無</span><span class="sxs-lookup"><span data-stu-id="ee024-145">None</span></span>            |  <span data-ttu-id="ee024-146">不需要驗證</span><span class="sxs-lookup"><span data-stu-id="ee024-146">No authentication</span></span> | | 
| <span data-ttu-id="ee024-147">個別使用者帳戶/在應用程式中儲存使用者帳戶</span><span class="sxs-lookup"><span data-stu-id="ee024-147">Individual User Accounts / Store user accounts in-app</span></span> |  <span data-ttu-id="ee024-148">個別驗證</span><span class="sxs-lookup"><span data-stu-id="ee024-148">Individual authentication</span></span> | <xref:security/authentication/identity> |
| <span data-ttu-id="ee024-149">個別使用者帳戶/連接到雲端中的現有使用者存放區</span><span class="sxs-lookup"><span data-stu-id="ee024-149">Individual User Accounts / Connect to an existing user store in the cloud</span></span> |  <span data-ttu-id="ee024-150">使用 Azure AD B2C 的雲端託管個別驗證</span><span class="sxs-lookup"><span data-stu-id="ee024-150">Cloud-hosted individual authentication with Azure AD B2C</span></span> | [<span data-ttu-id="ee024-151">Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="ee024-151">Azure AD B2C</span></span>](/azure/active-directory-b2c/) |
| <span data-ttu-id="ee024-152">公司或學校雲端/單一組織</span><span class="sxs-lookup"><span data-stu-id="ee024-152">Work or School Cloud / Single Org</span></span>  |  <span data-ttu-id="ee024-153">單一租使用者的組織驗證</span><span class="sxs-lookup"><span data-stu-id="ee024-153">Organizational authentication for a single tenant</span></span> | [<span data-ttu-id="ee024-154">Azure AD</span><span class="sxs-lookup"><span data-stu-id="ee024-154">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="ee024-155">公司或學校雲端/多組織</span><span class="sxs-lookup"><span data-stu-id="ee024-155">Work or School Cloud / Multiple Org</span></span> |  <span data-ttu-id="ee024-156">多個租使用者的組織驗證</span><span class="sxs-lookup"><span data-stu-id="ee024-156">Organizational authentication for multiple tenants</span></span> | [<span data-ttu-id="ee024-157">Azure AD</span><span class="sxs-lookup"><span data-stu-id="ee024-157">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="ee024-158">Windows</span><span class="sxs-lookup"><span data-stu-id="ee024-158">Windows</span></span>         |  <span data-ttu-id="ee024-159">Windows 驗證</span><span class="sxs-lookup"><span data-stu-id="ee024-159">Windows authentication</span></span> | [<span data-ttu-id="ee024-160">Windows 驗證</span><span class="sxs-lookup"><span data-stu-id="ee024-160">Windows Authentication</span></span>](xref:security/authentication/windowsauth)

## <a name="additional-resources"></a><span data-ttu-id="ee024-161">其他資源</span><span class="sxs-lookup"><span data-stu-id="ee024-161">Additional resources</span></span>

<span data-ttu-id="ee024-162">下列文章說明如何使用 ASP.NET Core 範本中所產生的程式碼，以使用個別的使用者帳戶：</span><span class="sxs-lookup"><span data-stu-id="ee024-162">The following articles show how to use the code generated in ASP.NET Core templates that use individual user accounts:</span></span>

* [<span data-ttu-id="ee024-163">ASP.NET Core 中的帳戶確認和密碼復原</span><span class="sxs-lookup"><span data-stu-id="ee024-163">Account confirmation and password recovery in ASP.NET Core</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="ee024-164">使用受授權保護的使用者資料建立 ASP.NET Core 應用程式</span><span class="sxs-lookup"><span data-stu-id="ee024-164">Create an ASP.NET Core app with user data protected by authorization</span></span>](xref:security/authorization/secure-data)
