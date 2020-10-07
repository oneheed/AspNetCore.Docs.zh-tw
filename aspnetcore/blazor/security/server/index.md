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
# <a name="secure-aspnet-core-no-locblazor-server-apps"></a>保護 ASP.NET Core Blazor Server 應用程式的安全

作者：[Luke Latham](https://github.com/guardrex)

Blazor Server 應用程式會以與 ASP.NET Core 應用程式相同的方式來設定安全性。 如需詳細資訊，請參閱底下的文章 <xref:security/index> 。 本總覽下的主題特別適用于 Blazor Server 。

## <a name="no-locblazor-server-project-template"></a>Blazor Server 專案範本

專案 Blazor Server 範本可設定為在建立專案時進行驗證。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

遵循中的 Visual Studio 指導方針 <xref:blazor/tooling> ，建立 Blazor Server 具有驗證機制的新專案。

在 [**建立新的 ASP.NET Core Web 應用程式**] 對話方塊中選擇** Blazor Server 應用程式**範本之後，請選取 [**驗證**] 下的 [**變更**]。

對話方塊隨即開啟，並提供可供其他 ASP.NET Core 專案使用的相同驗證機制集合：

* **無驗證**
* **個別使用者帳戶**：可儲存使用者帳戶：
  * 在使用 ASP.NET Core 系統的應用程式內 [Identity](xref:security/authentication/identity) 。
  * 使用 [Azure AD B2C](xref:security/authentication/azure-ad-b2c) 儲存。
* **公司或學校帳戶**
* **Windows 驗證**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

遵循中的 Visual Studio Code 指導方針 <xref:blazor/tooling> ，建立 Blazor Server 具有驗證機制的新專案：

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

下表顯示允許的驗證值 (`{AUTHENTICATION}`)。

| 驗證機制 | 描述 |
| ------------------------ | ----------- |
| `None` (預設)         | 不需要驗證 |
| `Individual`             | 儲存在應用程式中的使用者 ASP.NET Core Identity |
| `IndividualB2C`          | 儲存在[Azure AD B2C](xref:security/authentication/azure-ad-b2c)中的使用者 |
| `SingleOrg`              | 單一租使用者的組織驗證 |
| `MultiOrg`               | 多個租使用者的組織驗證 |
| `Windows`                | Windows 驗證 |

使用 `-o|--output` 選項時，此命令會使用為預留位置提供的值 `{APP NAME}` 來：

* 建立專案的資料夾。
* 命名專案。

如需詳細資訊，請參閱 [`dotnet new`](/dotnet/core/tools/dotnet-new) .Net Core 指南中的命令。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. 遵循中的 Visual Studio for Mac 指導方針 <xref:blazor/tooling> 。

1. 在 [**設定新的 Blazor Server 應用程式**] 步驟中，從 [**驗證**] 下拉式清單中選取 [**應用程式內) 的個別驗證 (** 。

1. 應用程式會針對儲存在應用程式中的個別使用者建立 ASP.NET Core Identity 。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

Blazor Server在命令 shell 中使用下列命令，建立具有驗證機制的新專案：

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

下表顯示允許的驗證值 (`{AUTHENTICATION}`)。

| 驗證機制 | 描述 |
| ------------------------ | ----------- |
| `None` (預設)         | 不需要驗證 |
| `Individual`             | 儲存在應用程式中的使用者 ASP.NET Core Identity |
| `IndividualB2C`          | 儲存在[Azure AD B2C](xref:security/authentication/azure-ad-b2c)中的使用者 |
| `SingleOrg`              | 單一租使用者的組織驗證 |
| `MultiOrg`               | 多個租使用者的組織驗證 |
| `Windows`                | Windows 驗證 |

使用 `-o|--output` 選項時，此命令會使用為預留位置提供的值 `{APP NAME}` 來：

* 建立專案的資料夾。
* 命名專案。

其他資訊：

* 請參閱 [`dotnet new`](/dotnet/core/tools/dotnet-new) .Net Core 指南中的命令。
* Blazor Server在命令 shell 中執行範本 () 的 [說明] 命令 `blazorserver` ：

  ```dotnetcli
  dotnet new blazorserver --help
  ```

---

## <a name="scaffold-no-locidentity"></a>支架 Identity

Scaffold Identity 至 Blazor Server 專案：

* [沒有現有的授權](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization)。
* [具有授權](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization)。

## <a name="additional-resources"></a>其他資源

* [快速入門：將「使用 Microsoft 登入」新增至 ASP.NET Core Web 應用程式](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [快速入門：以 Microsoft 身分識別平台保護 ASP.NET Core Web API](/azure/active-directory/develop/quickstart-v2-aspnet-core-web-api)
