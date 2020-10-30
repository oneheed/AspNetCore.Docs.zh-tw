---
title: 以個別使用者帳戶建立的 ASP.NET Core 專案為基礎的文章
author: rick-anderson
description: 探索以個別使用者帳戶建立之 ASP.NET Core 專案為基礎的文章。
ms.author: riande
ms.date: 12/11/2019
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
uid: security/authentication/individual
ms.openlocfilehash: 656006396de120b7feae6f2e08b5dad3b5a170b5
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053341"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a>以個別使用者帳戶建立的 ASP.NET Core 專案為基礎的文章

ASP.NET Core Identity 包含在具有「個別使用者帳戶」選項 Visual Studio 的專案範本中。

驗證範本可在 .NET Core CLI 中提供 `-au Individual` ：

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

請參閱 [此 GitHub](https://github.com/dotnet/AspNetCore/issues/5833) 針對 web API 驗證的問題。

<a name="no"></a>

## <a name="no-authentication"></a>不需要驗證

您可以使用選項，在 .NET Core CLI 中指定驗證 `-au` 。 在 Visual Studio 中，新的 web 應用程式可以使用 [ **變更驗證** ] 對話方塊。 Visual Studio 中新 web 應用程式的預設值為 [ **無驗證** ]。

使用無驗證建立的專案：

* 請勿包含要登入和登出的網頁和 UI。
* 請勿包含驗證碼。

<a name="win"></a>

## <a name="windows-authentication"></a>Windows 驗證

您可以使用選項，在 .NET Core CLI 中為新的 web 應用程式指定 Windows 驗證 `-au Windows` 。 在 Visual Studio 中，[ **變更驗證** ] 對話方塊會提供 **Windows 驗證** 選項。

如果選取 [Windows 驗證]，則會將應用程式設定為使用 [Windows 驗證 IIS 模組](xref:host-and-deploy/iis/modules)。 Windows 驗證適用于內部網路網站。

## <a name="dotnet-new-webapp-authentication-options"></a>dotnet 新的 webapp authentication 選項

下表顯示適用于新 web 應用程式的驗證選項：

| 選項 | 驗證類型 | 連結取得詳細資訊 |
 | ----------------- | ------------ | ---------- |
| None            |  不需要驗證 | | 
| 個人      |  個別驗證 | <xref:security/authentication/identity>
| IndividualB2C   |  使用 Azure AD B2C 的雲端託管個別驗證 | [Azure AD B2C](/azure/active-directory-b2c/) |
| SingleOrg       |  單一租使用者的組織驗證 | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| MultiOrg        |  多個租使用者的組織驗證 | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| Windows         |  Windows 驗證 | [Windows 驗證](xref:security/authentication/windowsauth)

## <a name="visual-studio-new-webapp-authentication-options"></a>Visual Studio 新的 webapp authentication 選項

下表顯示使用 Visual Studio 建立新的 web 應用程式時可用的驗證選項：

| 選項 | 驗證類型 | 連結取得詳細資訊 |
 | ----------------- | ------------ | ---------- |
| None            |  不需要驗證 | | 
| 個別使用者帳戶/在應用程式中儲存使用者帳戶 |  個別驗證 | <xref:security/authentication/identity> |
| 個別使用者帳戶/連接到雲端中的現有使用者存放區 |  使用 Azure AD B2C 的雲端託管個別驗證 | [Azure AD B2C](/azure/active-directory-b2c/) |
| 公司或學校雲端/單一組織  |  單一租使用者的組織驗證 | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| 公司或學校雲端/多組織 |  多個租使用者的組織驗證 | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| Windows         |  Windows 驗證 | [Windows 驗證](xref:security/authentication/windowsauth)

## <a name="additional-resources"></a>其他資源

下列文章說明如何使用 ASP.NET Core 範本中所產生的程式碼，以使用個別的使用者帳戶：

* [ASP.NET Core 中的帳戶確認和密碼復原](xref:security/authentication/accconfirm)
* [使用受授權保護的使用者資料建立 ASP.NET Core 應用程式](xref:security/authorization/secure-data)
