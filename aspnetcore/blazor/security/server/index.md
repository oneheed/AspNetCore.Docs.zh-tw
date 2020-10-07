---
title: 保護 ASP.NET Core Blazor Server 應用程式的安全
author: guardrex
description: 瞭解如何 Blazor Server ASP.NET Core 應用程式保護應用程式。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
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
uid: blazor/security/server/index
ms.openlocfilehash: d6d0f6f859dbaef98c6c8a9c53fe9858705cdc0a
ms.sourcegitcommit: 139c998d37e9f3e3d0e3d72e10dbce8b75957d89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91805501"
---
# <a name="secure-aspnet-core-no-locblazor-server-apps"></a><span data-ttu-id="c4da0-103">保護 ASP.NET Core Blazor Server 應用程式的安全</span><span class="sxs-lookup"><span data-stu-id="c4da0-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="c4da0-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c4da0-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="c4da0-105">Blazor Server 應用程式會以與 ASP.NET Core 應用程式相同的方式來設定安全性。</span><span class="sxs-lookup"><span data-stu-id="c4da0-105">Blazor Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="c4da0-106">如需詳細資訊，請參閱底下的文章 <xref:security/index> 。</span><span class="sxs-lookup"><span data-stu-id="c4da0-106">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="c4da0-107">本總覽下的主題特別適用于 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="c4da0-107">Topics under this overview apply specifically to Blazor Server.</span></span>

## <a name="no-locblazor-server-project-template"></a><span data-ttu-id="c4da0-108">Blazor Server 專案範本</span><span class="sxs-lookup"><span data-stu-id="c4da0-108">Blazor Server project template</span></span>

<span data-ttu-id="c4da0-109">專案 Blazor Server 範本可設定為在建立專案時進行驗證。</span><span class="sxs-lookup"><span data-stu-id="c4da0-109">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c4da0-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4da0-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c4da0-111">遵循中的 Visual Studio 指導方針 <xref:blazor/tooling> ，建立 Blazor Server 具有驗證機制的新專案。</span><span class="sxs-lookup"><span data-stu-id="c4da0-111">Follow the Visual Studio guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="c4da0-112">在 [**建立新的 ASP.NET Core Web 應用程式**] 對話方塊中選擇\*\* Blazor Server 應用程式**範本之後，請選取 [**驗證**] 下的 [**變更\*\*]。</span><span class="sxs-lookup"><span data-stu-id="c4da0-112">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="c4da0-113">對話方塊隨即開啟，並提供可供其他 ASP.NET Core 專案使用的相同驗證機制集合：</span><span class="sxs-lookup"><span data-stu-id="c4da0-113">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="c4da0-114">**無驗證**</span><span class="sxs-lookup"><span data-stu-id="c4da0-114">**No Authentication**</span></span>
* <span data-ttu-id="c4da0-115">**個別使用者帳戶**：可儲存使用者帳戶：</span><span class="sxs-lookup"><span data-stu-id="c4da0-115">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="c4da0-116">在使用 ASP.NET Core 系統的應用程式內 [Identity](xref:security/authentication/identity) 。</span><span class="sxs-lookup"><span data-stu-id="c4da0-116">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="c4da0-117">使用 [Azure AD B2C](xref:security/authentication/azure-ad-b2c) 儲存。</span><span class="sxs-lookup"><span data-stu-id="c4da0-117">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="c4da0-118">**公司或學校帳戶**</span><span class="sxs-lookup"><span data-stu-id="c4da0-118">**Work or School Accounts**</span></span>
* <span data-ttu-id="c4da0-119">**Windows 驗證**</span><span class="sxs-lookup"><span data-stu-id="c4da0-119">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c4da0-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c4da0-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c4da0-121">遵循中的 Visual Studio Code 指導方針 <xref:blazor/tooling> ，建立 Blazor Server 具有驗證機制的新專案：</span><span class="sxs-lookup"><span data-stu-id="c4da0-121">Follow the Visual Studio Code guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="c4da0-122">下表顯示允許的驗證值 (`{AUTHENTICATION}`)。</span><span class="sxs-lookup"><span data-stu-id="c4da0-122">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="c4da0-123">驗證機制</span><span class="sxs-lookup"><span data-stu-id="c4da0-123">Authentication mechanism</span></span> | <span data-ttu-id="c4da0-124">描述</span><span class="sxs-lookup"><span data-stu-id="c4da0-124">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="c4da0-125">`None` (預設)</span><span class="sxs-lookup"><span data-stu-id="c4da0-125">`None` (default)</span></span>         | <span data-ttu-id="c4da0-126">不需要驗證</span><span class="sxs-lookup"><span data-stu-id="c4da0-126">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="c4da0-127">儲存在應用程式中的使用者 ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="c4da0-127">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="c4da0-128">儲存在[Azure AD B2C](xref:security/authentication/azure-ad-b2c)中的使用者</span><span class="sxs-lookup"><span data-stu-id="c4da0-128">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="c4da0-129">單一租使用者的組織驗證</span><span class="sxs-lookup"><span data-stu-id="c4da0-129">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="c4da0-130">多個租使用者的組織驗證</span><span class="sxs-lookup"><span data-stu-id="c4da0-130">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="c4da0-131">Windows 驗證</span><span class="sxs-lookup"><span data-stu-id="c4da0-131">Windows Authentication</span></span> |

<span data-ttu-id="c4da0-132">使用 `-o|--output` 選項時，此命令會使用為預留位置提供的值 `{APP NAME}` 來：</span><span class="sxs-lookup"><span data-stu-id="c4da0-132">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="c4da0-133">建立專案的資料夾。</span><span class="sxs-lookup"><span data-stu-id="c4da0-133">Create a folder for the project.</span></span>
* <span data-ttu-id="c4da0-134">命名專案。</span><span class="sxs-lookup"><span data-stu-id="c4da0-134">Name the project.</span></span>

<span data-ttu-id="c4da0-135">如需詳細資訊，請參閱 [`dotnet new`](/dotnet/core/tools/dotnet-new) .Net Core 指南中的命令。</span><span class="sxs-lookup"><span data-stu-id="c4da0-135">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c4da0-136">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c4da0-136">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="c4da0-137">遵循中的 Visual Studio for Mac 指導方針 <xref:blazor/tooling> 。</span><span class="sxs-lookup"><span data-stu-id="c4da0-137">Follow the Visual Studio for Mac guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="c4da0-138">在 [**設定新的 Blazor Server 應用程式**] 步驟中，從 [**驗證**] 下拉式清單中選取 [**應用程式內) 的個別驗證 (** 。</span><span class="sxs-lookup"><span data-stu-id="c4da0-138">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="c4da0-139">應用程式會針對儲存在應用程式中的個別使用者建立 ASP.NET Core Identity 。</span><span class="sxs-lookup"><span data-stu-id="c4da0-139">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="c4da0-140">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="c4da0-140">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="c4da0-141">Blazor Server在命令 shell 中使用下列命令，建立具有驗證機制的新專案：</span><span class="sxs-lookup"><span data-stu-id="c4da0-141">Create a new Blazor Server project with an authentication mechanism using the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="c4da0-142">下表顯示允許的驗證值 (`{AUTHENTICATION}`)。</span><span class="sxs-lookup"><span data-stu-id="c4da0-142">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="c4da0-143">驗證機制</span><span class="sxs-lookup"><span data-stu-id="c4da0-143">Authentication mechanism</span></span> | <span data-ttu-id="c4da0-144">描述</span><span class="sxs-lookup"><span data-stu-id="c4da0-144">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="c4da0-145">`None` (預設)</span><span class="sxs-lookup"><span data-stu-id="c4da0-145">`None` (default)</span></span>         | <span data-ttu-id="c4da0-146">不需要驗證</span><span class="sxs-lookup"><span data-stu-id="c4da0-146">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="c4da0-147">儲存在應用程式中的使用者 ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="c4da0-147">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="c4da0-148">儲存在[Azure AD B2C](xref:security/authentication/azure-ad-b2c)中的使用者</span><span class="sxs-lookup"><span data-stu-id="c4da0-148">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="c4da0-149">單一租使用者的組織驗證</span><span class="sxs-lookup"><span data-stu-id="c4da0-149">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="c4da0-150">多個租使用者的組織驗證</span><span class="sxs-lookup"><span data-stu-id="c4da0-150">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="c4da0-151">Windows 驗證</span><span class="sxs-lookup"><span data-stu-id="c4da0-151">Windows Authentication</span></span> |

<span data-ttu-id="c4da0-152">使用 `-o|--output` 選項時，此命令會使用為預留位置提供的值 `{APP NAME}` 來：</span><span class="sxs-lookup"><span data-stu-id="c4da0-152">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="c4da0-153">建立專案的資料夾。</span><span class="sxs-lookup"><span data-stu-id="c4da0-153">Create a folder for the project.</span></span>
* <span data-ttu-id="c4da0-154">命名專案。</span><span class="sxs-lookup"><span data-stu-id="c4da0-154">Name the project.</span></span>

<span data-ttu-id="c4da0-155">其他資訊：</span><span class="sxs-lookup"><span data-stu-id="c4da0-155">For more information:</span></span>

* <span data-ttu-id="c4da0-156">請參閱 [`dotnet new`](/dotnet/core/tools/dotnet-new) .Net Core 指南中的命令。</span><span class="sxs-lookup"><span data-stu-id="c4da0-156">See the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>
* <span data-ttu-id="c4da0-157">Blazor Server在命令 shell 中執行範本 () 的 [說明] 命令 `blazorserver` ：</span><span class="sxs-lookup"><span data-stu-id="c4da0-157">Execute the help command for the Blazor Server template (`blazorserver`) in a command shell:</span></span>

  ```dotnetcli
  dotnet new blazorserver --help
  ```

---

## <a name="scaffold-no-locidentity"></a><span data-ttu-id="c4da0-158">支架 Identity</span><span class="sxs-lookup"><span data-stu-id="c4da0-158">Scaffold Identity</span></span>

<span data-ttu-id="c4da0-159">Scaffold Identity 至 Blazor Server 專案：</span><span class="sxs-lookup"><span data-stu-id="c4da0-159">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="c4da0-160">[沒有現有的授權](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization)。</span><span class="sxs-lookup"><span data-stu-id="c4da0-160">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="c4da0-161">[具有授權](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization)。</span><span class="sxs-lookup"><span data-stu-id="c4da0-161">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c4da0-162">其他資源</span><span class="sxs-lookup"><span data-stu-id="c4da0-162">Additional resources</span></span>

* [<span data-ttu-id="c4da0-163">快速入門：將「使用 Microsoft 登入」新增至 ASP.NET Core Web 應用程式</span><span class="sxs-lookup"><span data-stu-id="c4da0-163">Quickstart: Add sign-in with Microsoft to an ASP.NET Core web app</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [<span data-ttu-id="c4da0-164">快速入門：以 Microsoft 身分識別平台保護 ASP.NET Core Web API</span><span class="sxs-lookup"><span data-stu-id="c4da0-164">Quickstart: Protect an ASP.NET Core web API with Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-web-api)
