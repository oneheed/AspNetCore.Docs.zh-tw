---
title: ASP.NET Core 中的一般資料保護規定 (GDPR) 支援
author: rick-anderson
description: 瞭解如何存取 ASP.NET Core web 應用程式中的 GDPR 擴充點。
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
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
uid: security/gdpr
ms.openlocfilehash: ec65a2c8362c15716bebd6b22f5639785ba74c98
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051001"
---
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a>EU 一般資料保護規定 (GDPR) 支援 ASP.NET Core

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core 提供 Api 和範本，以協助符合某些 [EU 一般資料保護規定 (GDPR) ](https://ec.europa.eu/info/law/law-topic/data-protection/reform/what-does-general-data-protection-regulation-gdpr-govern_en) 需求：

::: moniker range=">= aspnetcore-3.0"

* 專案範本包含擴充點和 stub 標記，您可以將其取代為隱私權並 cookie 使用原則。
* [ *頁面/隱私權* ] 頁面或 [ *視圖/首頁/隱私權* ] 會提供頁面來詳細說明您的網站隱私權原則。

若要啟用預設 cookie 同意功能，例如在 ASP.NET Core 3.0 範本產生的應用程式中于 ASP.NET Core 2.2 範本中找到的功能：

* 加入 `using Microsoft.AspNetCore.Http` using 指示詞的清單。
* 將[ Cookie PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions)新增至 `Startup.ConfigureServices` ，並[使用 Cookie 原則](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) `Startup.Configure` ：

  [!code-csharp[Main](gdpr/sample/RP3.0/Startup.cs?name=snippet1&highlight=12-19,38)]

* 將 cookie 同意部分新增至 *_Layout 的 cshtml* 檔案：

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_Layout.cshtml?name=snippet&highlight=4)]

* 將 *\_ Cookie ConsentPartial* 檔加入至專案：

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_CookieConsentPartial.cshtml)]

* 請選取此文章的 ASP.NET Core 2.2 版本，以閱讀同意功能的相關資訊 cookie 。

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* 專案範本包含擴充點和 stub 標記，您可以將其取代為隱私權並 cookie 使用原則。
* cookie同意功能可讓您要求 (，並追蹤使用者的) 同意，以儲存個人資訊。 如果使用者尚未同意資料收集，而且應用程式已將 [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) 設定為，則不會將 `true` 非必要的設定 cookie 傳送至瀏覽器。
* Cookie可以標示為必要。 cookie即使使用者尚未同意且已停用追蹤，也會將重要的傳送至瀏覽器。
* 停用追蹤時[，TempData 和會話 cookie s](#tempdata)無法正常運作。
* [ [ Identity 管理](#pd)] 頁面會提供下載和刪除使用者資料的連結。

[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample)可讓您測試新增至 ASP.NET Core 2.1 範本的大部分 GDPR 擴充點和 api。 請參閱 [自述](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) 檔以取得測試指示。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a>ASP.NET Core 範本產生的程式碼中的 GDPR 支援

Razor 以專案範本建立的頁面和 MVC 專案包含下列 GDPR 支援：

* [ Cookie PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions)和[使用 Cookie 原則](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy)是在類別中設定 `Startup` 。
* *\_ Cookie ConsentPartial* [部分視圖](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)。 此檔案中包含 [ **接受** ] 按鈕。 當使用者按一下 [ **接受** ] 按鈕時，會提供同意存放區 cookie 。
* [ *頁面/隱私權* ] 頁面或 [ *視圖/首頁/隱私權* ] 會提供頁面來詳細說明您的網站隱私權原則。 *\_ Cookie ConsentPartial. cshtml* 檔案會產生隱私權頁面的連結。
* 針對使用個別使用者帳戶所建立的應用程式，[管理] 頁面會提供下載和刪除 [個人使用者資料](#pd)的連結。

### <a name="no-loccookiepolicyoptions-and-useno-loccookiepolicy"></a>CookiePolicyOptions 和使用 Cookie 原則

[ Cookie PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions)會在中初始化 `Startup.ConfigureServices` ：

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

[使用 Cookie](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy)在下列情況中呼叫原則 `Startup.Configure` ：

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="_no-loccookieconsentpartialcshtml-partial-view"></a>\_CookieConsentPartial. cshtml 部分視圖

*\_ Cookie ConsentPartial* 的部分觀點：

[!code-cshtml[](gdpr/sample/RP2.2/Pages/Shared/_CookieConsentPartial.cshtml)]

部分：

* 取得使用者追蹤的狀態。 如果應用程式設定為要求同意，則使用者必須先同意才能 cookie 追蹤。 如果需要同意，則 cookie 會在配置 *\_ cshtml* 檔案所建立的導覽列頂端修正同意面板。
* 提供 HTML `<p>` 元素，以摘要您的隱私權和 cookie 使用原則。
* 提供隱私權頁面的連結，或可讓您詳細瞭解網站隱私權原則的位置。

## <a name="essential-no-loccookies"></a>重要 cookie s

如果 cookie 未提供同意儲存，則只會將標示為「基本」 cookie 的標記傳送至瀏覽器。 下列程式碼會有 cookie 必要：

[!code-csharp[Main](gdpr/sample/RP2.2/Pages/Cookie.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-no-loccookies-arent-essential"></a>TempData 提供者和會話狀態 cookie 不是必要的

[TempData 提供者](xref:fundamentals/app-state#tempdata) cookie 並不重要。 如果停用追蹤，TempData 提供者將無法運作。 若要在停用追蹤時啟用 TempData 提供者，請 cookie 在下列情況中將 TempData 標示為必要 `Startup.ConfigureServices` ：

[!code-csharp[Main](gdpr/sample/RP2.2/Startup.cs?name=snippet1)]

[會話狀態](xref:fundamentals/app-state) cookie不是必要的。 停用追蹤時，會話狀態無法運作。 下列程式碼會讓會話成為 cookie 必要的：

[!code-csharp[](gdpr/sample/RP2.2/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a>個人資料

使用個別使用者帳戶建立的 ASP.NET Core 應用程式包含下載及刪除個人資料的程式碼。

選取使用者名稱，然後選取 [ **個人資料** ]：

![管理個人資料頁面](gdpr/_static/pd.png)

注意：

* 若要產生程式 `Account/Manage` 代碼，請參閱[Scaffold Identity ](xref:security/authentication/scaffold-identity)。
* **刪除** 和 **下載** 連結僅適用于預設的身分識別資料。 您必須擴充建立自訂使用者資料的應用程式，才能刪除/下載自訂的使用者資料。 如需詳細資訊，請參閱[新增、下載及刪除自訂使用者 Identity 資料](xref:security/authentication/add-user-data)。
* Identity `AspNetUserTokens` 當使用者因為[外鍵](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152)而透過串聯刪除行為刪除時，會刪除儲存在資料庫資料表中的使用者儲存的權杖。
* 在接受原則之前，無法使用[外部提供者驗證](xref:security/authentication/social/index)，例如 Facebook 和 Google cookie 。

::: moniker-end

## <a name="encryption-at-rest"></a>待用加密

部分資料庫和儲存機制允許待用加密。 待用加密：

* 自動加密儲存的資料。
* 加密存取資料的軟體，而不需要設定、程式設計或其他工作。
* 是最簡單且最安全的選項。
* 允許資料庫管理金鑰和加密。

例如：

* Microsoft SQL 和 Azure SQL 提供 [透明資料加密](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE) 。
* [SQL Azure 預設會加密資料庫](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* [依預設會加密 Azure blob、檔案、資料表和佇列儲存體](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/)。

對於不提供待用內建加密的資料庫，您可以使用磁片加密來提供相同的保護。 例如：

* [適用于 Windows Server 的 BitLocker](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* Linux：
  * [eCryptfs](https://launchpad.net/ecryptfs)
  * [EncFS](https://github.com/vgough/encfs)。

## <a name="additional-resources"></a>其他資源

* [Microsoft.com/GDPR](https://www.microsoft.com/trustcenter/Privacy/GDPR)
* [GDPR-在 ASP.NET Core 中新增 [撤銷同意] 按鈕](https://www.joeaudette.com/blog/2018/08/28/gdpr---adding-a-revoke-consent-button-in-aspnet-core)
