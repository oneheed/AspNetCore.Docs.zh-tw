---
title: '保護 ASP.NET Core :::no-loc(Blazor Server)::: 應用程式的安全'
author: guardrex
description: '瞭解如何 :::no-loc(Blazor Server)::: ASP.NET Core 應用程式保護應用程式。'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
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
uid: blazor/security/server/index
ms.openlocfilehash: 108fb3a8a24295cad43fd8c83303abd95a7ecd33
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055473"
---
# <a name="secure-aspnet-core-no-locblazor-server-apps"></a><span data-ttu-id="75a8f-103">保護 ASP.NET Core :::no-loc(Blazor Server)::: 應用程式的安全</span><span class="sxs-lookup"><span data-stu-id="75a8f-103">Secure ASP.NET Core :::no-loc(Blazor Server)::: apps</span></span>

<span data-ttu-id="75a8f-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="75a8f-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="75a8f-105">:::no-loc(Blazor Server)::: 應用程式會以與 ASP.NET Core 應用程式相同的方式來設定安全性。</span><span class="sxs-lookup"><span data-stu-id="75a8f-105">:::no-loc(Blazor Server)::: apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="75a8f-106">如需詳細資訊，請參閱底下的文章 <xref:security/index> 。</span><span class="sxs-lookup"><span data-stu-id="75a8f-106">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="75a8f-107">本總覽下的主題特別適用于 :::no-loc(Blazor Server)::: 。</span><span class="sxs-lookup"><span data-stu-id="75a8f-107">Topics under this overview apply specifically to :::no-loc(Blazor Server):::.</span></span>

## <a name="no-locblazor-server-project-template"></a><span data-ttu-id="75a8f-108">:::no-loc(Blazor Server)::: 專案範本</span><span class="sxs-lookup"><span data-stu-id="75a8f-108">:::no-loc(Blazor Server)::: project template</span></span>

<span data-ttu-id="75a8f-109">專案 :::no-loc(Blazor Server)::: 範本可設定為在建立專案時進行驗證。</span><span class="sxs-lookup"><span data-stu-id="75a8f-109">The :::no-loc(Blazor Server)::: project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="75a8f-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="75a8f-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="75a8f-111">遵循中的 Visual Studio 指導方針 <xref:blazor/tooling> ，建立 :::no-loc(Blazor Server)::: 具有驗證機制的新專案。</span><span class="sxs-lookup"><span data-stu-id="75a8f-111">Follow the Visual Studio guidance in <xref:blazor/tooling> to create a new :::no-loc(Blazor Server)::: project with an authentication mechanism.</span></span>

<span data-ttu-id="75a8f-112">在 [ **建立新的 ASP.NET Core Web 應用程式** ] 對話方塊中選擇 **:::no-loc(Blazor Server)::: 應用程式** 範本之後，請選取 [ **驗證** ] 下的 [ **變更** ]。</span><span class="sxs-lookup"><span data-stu-id="75a8f-112">After choosing the **:::no-loc(Blazor Server)::: App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication** .</span></span>

<span data-ttu-id="75a8f-113">對話方塊隨即開啟，並提供可供其他 ASP.NET Core 專案使用的相同驗證機制集合：</span><span class="sxs-lookup"><span data-stu-id="75a8f-113">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="75a8f-114">**無驗證**</span><span class="sxs-lookup"><span data-stu-id="75a8f-114">**No Authentication**</span></span>
* <span data-ttu-id="75a8f-115">**個別使用者帳戶** ：可儲存使用者帳戶：</span><span class="sxs-lookup"><span data-stu-id="75a8f-115">**Individual User Accounts** : User accounts can be stored:</span></span>
  * <span data-ttu-id="75a8f-116">在使用 ASP.NET Core 系統的應用程式內 [:::no-loc(Identity):::](xref:security/authentication/identity) 。</span><span class="sxs-lookup"><span data-stu-id="75a8f-116">Within the app using ASP.NET Core's [:::no-loc(Identity):::](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="75a8f-117">使用 [Azure AD B2C](xref:security/authentication/azure-ad-b2c) 儲存。</span><span class="sxs-lookup"><span data-stu-id="75a8f-117">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="75a8f-118">**公司或學校帳戶**</span><span class="sxs-lookup"><span data-stu-id="75a8f-118">**Work or School Accounts**</span></span>
* <span data-ttu-id="75a8f-119">**Windows 驗證**</span><span class="sxs-lookup"><span data-stu-id="75a8f-119">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="75a8f-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="75a8f-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="75a8f-121">遵循中的 Visual Studio Code 指導方針 <xref:blazor/tooling> ，建立 :::no-loc(Blazor Server)::: 具有驗證機制的新專案：</span><span class="sxs-lookup"><span data-stu-id="75a8f-121">Follow the Visual Studio Code guidance in <xref:blazor/tooling> to create a new :::no-loc(Blazor Server)::: project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="75a8f-122">下表顯示允許的驗證值 (`{AUTHENTICATION}`)。</span><span class="sxs-lookup"><span data-stu-id="75a8f-122">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="75a8f-123">驗證機制</span><span class="sxs-lookup"><span data-stu-id="75a8f-123">Authentication mechanism</span></span> | <span data-ttu-id="75a8f-124">描述</span><span class="sxs-lookup"><span data-stu-id="75a8f-124">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="75a8f-125">`None` (預設)</span><span class="sxs-lookup"><span data-stu-id="75a8f-125">`None` (default)</span></span>         | <span data-ttu-id="75a8f-126">不需要驗證</span><span class="sxs-lookup"><span data-stu-id="75a8f-126">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="75a8f-127">儲存在應用程式中的使用者 :::no-loc(ASP.NET Core Identity):::</span><span class="sxs-lookup"><span data-stu-id="75a8f-127">Users stored in the app with :::no-loc(ASP.NET Core Identity):::</span></span> |
| `IndividualB2C`          | <span data-ttu-id="75a8f-128">儲存在[Azure AD B2C](xref:security/authentication/azure-ad-b2c)中的使用者</span><span class="sxs-lookup"><span data-stu-id="75a8f-128">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="75a8f-129">單一租使用者的組織驗證</span><span class="sxs-lookup"><span data-stu-id="75a8f-129">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="75a8f-130">多個租使用者的組織驗證</span><span class="sxs-lookup"><span data-stu-id="75a8f-130">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="75a8f-131">Windows 驗證</span><span class="sxs-lookup"><span data-stu-id="75a8f-131">Windows Authentication</span></span> |

<span data-ttu-id="75a8f-132">使用 `-o|--output` 選項時，此命令會使用為預留位置提供的值 `{APP NAME}` 來：</span><span class="sxs-lookup"><span data-stu-id="75a8f-132">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="75a8f-133">建立專案的資料夾。</span><span class="sxs-lookup"><span data-stu-id="75a8f-133">Create a folder for the project.</span></span>
* <span data-ttu-id="75a8f-134">命名專案。</span><span class="sxs-lookup"><span data-stu-id="75a8f-134">Name the project.</span></span>

<span data-ttu-id="75a8f-135">如需詳細資訊，請參閱 [`dotnet new`](/dotnet/core/tools/dotnet-new) .Net Core 指南中的命令。</span><span class="sxs-lookup"><span data-stu-id="75a8f-135">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="75a8f-136">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="75a8f-136">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="75a8f-137">遵循中的 Visual Studio for Mac 指導方針 <xref:blazor/tooling> 。</span><span class="sxs-lookup"><span data-stu-id="75a8f-137">Follow the Visual Studio for Mac guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="75a8f-138">在 [ **設定新的 :::no-loc(Blazor Server)::: 應用程式** ] 步驟中，從 [ **驗證** ] 下拉式清單中選取 [ **應用程式內) 的個別驗證 (** 。</span><span class="sxs-lookup"><span data-stu-id="75a8f-138">On the **Configure your new :::no-loc(Blazor Server)::: App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="75a8f-139">應用程式會針對儲存在應用程式中的個別使用者建立 :::no-loc(ASP.NET Core Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="75a8f-139">The app is created for individual users stored in the app with :::no-loc(ASP.NET Core Identity):::.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="75a8f-140">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="75a8f-140">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="75a8f-141">:::no-loc(Blazor Server):::在命令 shell 中使用下列命令，建立具有驗證機制的新專案：</span><span class="sxs-lookup"><span data-stu-id="75a8f-141">Create a new :::no-loc(Blazor Server)::: project with an authentication mechanism using the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="75a8f-142">下表顯示允許的驗證值 (`{AUTHENTICATION}`)。</span><span class="sxs-lookup"><span data-stu-id="75a8f-142">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="75a8f-143">驗證機制</span><span class="sxs-lookup"><span data-stu-id="75a8f-143">Authentication mechanism</span></span> | <span data-ttu-id="75a8f-144">描述</span><span class="sxs-lookup"><span data-stu-id="75a8f-144">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="75a8f-145">`None` (預設)</span><span class="sxs-lookup"><span data-stu-id="75a8f-145">`None` (default)</span></span>         | <span data-ttu-id="75a8f-146">不需要驗證</span><span class="sxs-lookup"><span data-stu-id="75a8f-146">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="75a8f-147">儲存在應用程式中的使用者 :::no-loc(ASP.NET Core Identity):::</span><span class="sxs-lookup"><span data-stu-id="75a8f-147">Users stored in the app with :::no-loc(ASP.NET Core Identity):::</span></span> |
| `IndividualB2C`          | <span data-ttu-id="75a8f-148">儲存在[Azure AD B2C](xref:security/authentication/azure-ad-b2c)中的使用者</span><span class="sxs-lookup"><span data-stu-id="75a8f-148">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="75a8f-149">單一租使用者的組織驗證</span><span class="sxs-lookup"><span data-stu-id="75a8f-149">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="75a8f-150">多個租使用者的組織驗證</span><span class="sxs-lookup"><span data-stu-id="75a8f-150">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="75a8f-151">Windows 驗證</span><span class="sxs-lookup"><span data-stu-id="75a8f-151">Windows Authentication</span></span> |

<span data-ttu-id="75a8f-152">使用 `-o|--output` 選項時，此命令會使用為預留位置提供的值 `{APP NAME}` 來：</span><span class="sxs-lookup"><span data-stu-id="75a8f-152">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="75a8f-153">建立專案的資料夾。</span><span class="sxs-lookup"><span data-stu-id="75a8f-153">Create a folder for the project.</span></span>
* <span data-ttu-id="75a8f-154">命名專案。</span><span class="sxs-lookup"><span data-stu-id="75a8f-154">Name the project.</span></span>

<span data-ttu-id="75a8f-155">其他資訊：</span><span class="sxs-lookup"><span data-stu-id="75a8f-155">For more information:</span></span>

* <span data-ttu-id="75a8f-156">請參閱 [`dotnet new`](/dotnet/core/tools/dotnet-new) .Net Core 指南中的命令。</span><span class="sxs-lookup"><span data-stu-id="75a8f-156">See the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>
* <span data-ttu-id="75a8f-157">:::no-loc(Blazor Server):::在命令 shell 中執行範本 () 的 [說明] 命令 `blazorserver` ：</span><span class="sxs-lookup"><span data-stu-id="75a8f-157">Execute the help command for the :::no-loc(Blazor Server)::: template (`blazorserver`) in a command shell:</span></span>

  ```dotnetcli
  dotnet new blazorserver --help
  ```

---

## <a name="scaffold-no-locidentity"></a><span data-ttu-id="75a8f-158">支架 :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="75a8f-158">Scaffold :::no-loc(Identity):::</span></span>

<span data-ttu-id="75a8f-159">Scaffold :::no-loc(Identity)::: 至 :::no-loc(Blazor Server)::: 專案：</span><span class="sxs-lookup"><span data-stu-id="75a8f-159">Scaffold :::no-loc(Identity)::: into a :::no-loc(Blazor Server)::: project:</span></span>

* <span data-ttu-id="75a8f-160">[沒有現有的授權](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization)。</span><span class="sxs-lookup"><span data-stu-id="75a8f-160">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="75a8f-161">[具有授權](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization)。</span><span class="sxs-lookup"><span data-stu-id="75a8f-161">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="75a8f-162">其他資源</span><span class="sxs-lookup"><span data-stu-id="75a8f-162">Additional resources</span></span>

* [<span data-ttu-id="75a8f-163">快速入門：將「使用 Microsoft 登入」新增至 ASP.NET Core Web 應用程式</span><span class="sxs-lookup"><span data-stu-id="75a8f-163">Quickstart: Add sign-in with Microsoft to an ASP.NET Core web app</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [<span data-ttu-id="75a8f-164">快速入門：以 Microsoft 身分識別平台保護 ASP.NET Core Web API</span><span class="sxs-lookup"><span data-stu-id="75a8f-164">Quickstart: Protect an ASP.NET Core web API with Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-web-api)
