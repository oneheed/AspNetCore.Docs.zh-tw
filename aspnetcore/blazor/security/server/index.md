---
title: 保護 ASP.NET Core Blazor Server 應用程式的安全
author: guardrex
description: 瞭解如何 Blazor Server ASP.NET Core 應用程式保護應用程式。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
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
uid: blazor/security/server/index
ms.openlocfilehash: 5a3d3c6e06653de7f0d01565444d37013f347a5b
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280310"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="da05b-103">保護 ASP.NET Core Blazor Server 應用程式的安全</span><span class="sxs-lookup"><span data-stu-id="da05b-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="da05b-104">Blazor Server 應用程式會以與 ASP.NET Core 應用程式相同的方式來設定安全性。</span><span class="sxs-lookup"><span data-stu-id="da05b-104">Blazor Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="da05b-105">如需詳細資訊，請參閱底下的文章 <xref:security/index> 。</span><span class="sxs-lookup"><span data-stu-id="da05b-105">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="da05b-106">本總覽下的主題特別適用于 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="da05b-106">Topics under this overview apply specifically to Blazor Server.</span></span>

## <a name="blazor-server-project-template"></a><span data-ttu-id="da05b-107">Blazor Server 專案範本</span><span class="sxs-lookup"><span data-stu-id="da05b-107">Blazor Server project template</span></span>

<span data-ttu-id="da05b-108">專案 Blazor Server 範本可設定為在建立專案時進行驗證。</span><span class="sxs-lookup"><span data-stu-id="da05b-108">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="da05b-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="da05b-109">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="da05b-110">遵循中的 Visual Studio 指導方針 <xref:blazor/tooling> ，建立 Blazor Server 具有驗證機制的新專案。</span><span class="sxs-lookup"><span data-stu-id="da05b-110">Follow the Visual Studio guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="da05b-111">在 [**建立新的 ASP.NET Core Web 應用程式**] 對話方塊中選擇 **Blazor Server 應用程式** 範本之後，請選取 [**驗證**] 下的 [**變更**]。</span><span class="sxs-lookup"><span data-stu-id="da05b-111">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="da05b-112">對話方塊隨即開啟，並提供可供其他 ASP.NET Core 專案使用的相同驗證機制集合：</span><span class="sxs-lookup"><span data-stu-id="da05b-112">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="da05b-113">**無驗證**</span><span class="sxs-lookup"><span data-stu-id="da05b-113">**No Authentication**</span></span>
* <span data-ttu-id="da05b-114">**個別使用者帳戶**：可儲存使用者帳戶：</span><span class="sxs-lookup"><span data-stu-id="da05b-114">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="da05b-115">在使用 ASP.NET Core 系統的應用程式內 [Identity](xref:security/authentication/identity) 。</span><span class="sxs-lookup"><span data-stu-id="da05b-115">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="da05b-116">使用 [Azure AD B2C](xref:security/authentication/azure-ad-b2c) 儲存。</span><span class="sxs-lookup"><span data-stu-id="da05b-116">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="da05b-117">**公司或學校帳戶**</span><span class="sxs-lookup"><span data-stu-id="da05b-117">**Work or School Accounts**</span></span>
* <span data-ttu-id="da05b-118">**Windows 驗證**</span><span class="sxs-lookup"><span data-stu-id="da05b-118">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="da05b-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="da05b-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="da05b-120">遵循中的 Visual Studio Code 指導方針 <xref:blazor/tooling> ，建立 Blazor Server 具有驗證機制的新專案：</span><span class="sxs-lookup"><span data-stu-id="da05b-120">Follow the Visual Studio Code guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="da05b-121">下表顯示允許的驗證值 (`{AUTHENTICATION}`)。</span><span class="sxs-lookup"><span data-stu-id="da05b-121">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="da05b-122">驗證機制</span><span class="sxs-lookup"><span data-stu-id="da05b-122">Authentication mechanism</span></span> | <span data-ttu-id="da05b-123">描述</span><span class="sxs-lookup"><span data-stu-id="da05b-123">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="da05b-124">`None` (預設)</span><span class="sxs-lookup"><span data-stu-id="da05b-124">`None` (default)</span></span>         | <span data-ttu-id="da05b-125">不需要驗證</span><span class="sxs-lookup"><span data-stu-id="da05b-125">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="da05b-126">儲存在應用程式中的使用者 ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="da05b-126">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="da05b-127">儲存在[Azure AD B2C](xref:security/authentication/azure-ad-b2c)中的使用者</span><span class="sxs-lookup"><span data-stu-id="da05b-127">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="da05b-128">單一租使用者的組織驗證</span><span class="sxs-lookup"><span data-stu-id="da05b-128">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="da05b-129">多個租使用者的組織驗證</span><span class="sxs-lookup"><span data-stu-id="da05b-129">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="da05b-130">Windows 驗證</span><span class="sxs-lookup"><span data-stu-id="da05b-130">Windows Authentication</span></span> |

<span data-ttu-id="da05b-131">使用 `-o|--output` 選項時，此命令會使用為預留位置提供的值 `{APP NAME}` 來：</span><span class="sxs-lookup"><span data-stu-id="da05b-131">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="da05b-132">建立專案的資料夾。</span><span class="sxs-lookup"><span data-stu-id="da05b-132">Create a folder for the project.</span></span>
* <span data-ttu-id="da05b-133">命名專案。</span><span class="sxs-lookup"><span data-stu-id="da05b-133">Name the project.</span></span>

<span data-ttu-id="da05b-134">如需詳細資訊，請參閱 [`dotnet new`](/dotnet/core/tools/dotnet-new) .Net Core 指南中的命令。</span><span class="sxs-lookup"><span data-stu-id="da05b-134">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="da05b-135">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="da05b-135">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="da05b-136">遵循中的 Visual Studio for Mac 指導方針 <xref:blazor/tooling> 。</span><span class="sxs-lookup"><span data-stu-id="da05b-136">Follow the Visual Studio for Mac guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="da05b-137">在 [**設定新的 Blazor Server 應用程式**] 步驟中，從 [**驗證**] 下拉式清單中選取 [**應用程式內) 的個別驗證 (** 。</span><span class="sxs-lookup"><span data-stu-id="da05b-137">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="da05b-138">應用程式會針對儲存在應用程式中的個別使用者建立 ASP.NET Core Identity 。</span><span class="sxs-lookup"><span data-stu-id="da05b-138">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="da05b-139">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="da05b-139">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="da05b-140">Blazor Server在命令 shell 中使用下列命令，建立具有驗證機制的新專案：</span><span class="sxs-lookup"><span data-stu-id="da05b-140">Create a new Blazor Server project with an authentication mechanism using the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="da05b-141">下表顯示允許的驗證值 (`{AUTHENTICATION}`)。</span><span class="sxs-lookup"><span data-stu-id="da05b-141">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="da05b-142">驗證機制</span><span class="sxs-lookup"><span data-stu-id="da05b-142">Authentication mechanism</span></span> | <span data-ttu-id="da05b-143">描述</span><span class="sxs-lookup"><span data-stu-id="da05b-143">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="da05b-144">`None` (預設)</span><span class="sxs-lookup"><span data-stu-id="da05b-144">`None` (default)</span></span>         | <span data-ttu-id="da05b-145">不需要驗證</span><span class="sxs-lookup"><span data-stu-id="da05b-145">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="da05b-146">儲存在應用程式中的使用者 ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="da05b-146">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="da05b-147">儲存在[Azure AD B2C](xref:security/authentication/azure-ad-b2c)中的使用者</span><span class="sxs-lookup"><span data-stu-id="da05b-147">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="da05b-148">單一租使用者的組織驗證</span><span class="sxs-lookup"><span data-stu-id="da05b-148">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="da05b-149">多個租使用者的組織驗證</span><span class="sxs-lookup"><span data-stu-id="da05b-149">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="da05b-150">Windows 驗證</span><span class="sxs-lookup"><span data-stu-id="da05b-150">Windows Authentication</span></span> |

<span data-ttu-id="da05b-151">使用 `-o|--output` 選項時，此命令會使用為預留位置提供的值 `{APP NAME}` 來：</span><span class="sxs-lookup"><span data-stu-id="da05b-151">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="da05b-152">建立專案的資料夾。</span><span class="sxs-lookup"><span data-stu-id="da05b-152">Create a folder for the project.</span></span>
* <span data-ttu-id="da05b-153">命名專案。</span><span class="sxs-lookup"><span data-stu-id="da05b-153">Name the project.</span></span>

<span data-ttu-id="da05b-154">其他資訊：</span><span class="sxs-lookup"><span data-stu-id="da05b-154">For more information:</span></span>

* <span data-ttu-id="da05b-155">請參閱 [`dotnet new`](/dotnet/core/tools/dotnet-new) .Net Core 指南中的命令。</span><span class="sxs-lookup"><span data-stu-id="da05b-155">See the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>
* <span data-ttu-id="da05b-156">Blazor Server在命令 shell 中執行範本 () 的 [說明] 命令 `blazorserver` ：</span><span class="sxs-lookup"><span data-stu-id="da05b-156">Execute the help command for the Blazor Server template (`blazorserver`) in a command shell:</span></span>

  ```dotnetcli
  dotnet new blazorserver --help
  ```

---

## <a name="scaffold-identity"></a><span data-ttu-id="da05b-157">支架 Identity</span><span class="sxs-lookup"><span data-stu-id="da05b-157">Scaffold Identity</span></span>

<span data-ttu-id="da05b-158">Scaffold Identity 至 Blazor Server 專案：</span><span class="sxs-lookup"><span data-stu-id="da05b-158">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="da05b-159">[沒有現有的授權](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization)。</span><span class="sxs-lookup"><span data-stu-id="da05b-159">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="da05b-160">[具有授權](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization)。</span><span class="sxs-lookup"><span data-stu-id="da05b-160">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>

## <a name="azure-app-service-on-linux-with-identity-server"></a><span data-ttu-id="da05b-161">使用 Identity 伺服器 Linux 上的 Azure App Service</span><span class="sxs-lookup"><span data-stu-id="da05b-161">Azure App Service on Linux with Identity Server</span></span>

<span data-ttu-id="da05b-162">當部署至與伺服器 Linux 上的 Azure App Service 時，請明確指定簽發者 Identity 。</span><span class="sxs-lookup"><span data-stu-id="da05b-162">Specify the issuer explicitly when deploying to Azure App Service on Linux with Identity Server.</span></span> <span data-ttu-id="da05b-163">如需詳細資訊，請參閱<xref:security/authentication/identity/spa#azure-app-service-on-linux>。</span><span class="sxs-lookup"><span data-stu-id="da05b-163">For more information, see <xref:security/authentication/identity/spa#azure-app-service-on-linux>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="da05b-164">其他資源</span><span class="sxs-lookup"><span data-stu-id="da05b-164">Additional resources</span></span>

* [<span data-ttu-id="da05b-165">快速入門：將「使用 Microsoft 登入」新增至 ASP.NET Core Web 應用程式</span><span class="sxs-lookup"><span data-stu-id="da05b-165">Quickstart: Add sign-in with Microsoft to an ASP.NET Core web app</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [<span data-ttu-id="da05b-166">快速入門：以 Microsoft 身分識別平台保護 ASP.NET Core Web API</span><span class="sxs-lookup"><span data-stu-id="da05b-166">Quickstart: Protect an ASP.NET Core web API with Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-web-api)
* <span data-ttu-id="da05b-167"><xref:host-and-deploy/proxy-load-balancer>：包含下列相關指引：</span><span class="sxs-lookup"><span data-stu-id="da05b-167"><xref:host-and-deploy/proxy-load-balancer>: Includes guidance on:</span></span>
  * <span data-ttu-id="da05b-168">使用轉送的標頭中介軟體，在 proxy 伺服器和內部網路之間保留 HTTPS 配置資訊。</span><span class="sxs-lookup"><span data-stu-id="da05b-168">Using Forwarded Headers Middleware to preserve HTTPS scheme information across proxy servers and internal networks.</span></span>
  * <span data-ttu-id="da05b-169">其他案例和使用案例，包括手動設定設定、正確要求路由的要求路徑變更，以及轉送 Linux 和非 IIS 反向 proxy 的要求配置。</span><span class="sxs-lookup"><span data-stu-id="da05b-169">Additional scenarios and use cases, including manual scheme configuration, request path changes for correct request routing, and forwarding the request scheme for Linux and non-IIS reverse proxies.</span></span>
