---
title: ASP.NET Core 中的連結標記協助程式
author: rick-anderson
ms.author: riande
description: 探索 ASP.NET Core 連結標籤協助程式屬性，以及每個屬性在 HTML 連結標記的擴充行為中所扮演的角色。
ms.custom: mvc
ms.date: 09/24/2019
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
uid: mvc/views/tag-helpers/builtin-th/link-tag-helper
ms.openlocfilehash: 09507294b90f08bbaf134f611aad0b91504ccffb
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635069"
---
# <a name="link-tag-helper-in-aspnet-core"></a>ASP.NET Core 中的連結標記協助程式

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

[連結](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper)標籤協助程式會產生主要或反向 CSS 檔案的連結。 主要 CSS 檔案通常位於 (CDN) 的 [內容傳遞網路](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) 上。

[!INCLUDE[](~/includes/cdn.md)]

連結標籤協助程式可讓您指定 CSS 檔案的 CDN，以及當 CDN 無法使用時的恢復功能。 連結標籤協助程式提供 CDN 的效能優勢，以及本機裝載的穩定性。

下列 Razor 標記顯示 `head` 使用 ASP.NET Core web 應用程式範本所建立的版面配置檔案元素：

[!code-cshtml[](link-tag-helper/sample/_Layout.cshtml?name=snippet)]

以下是在非開發環境) 中， (上述程式碼所呈現的 HTML：

[!code-html[](link-tag-helper/sample/HtmlPage1.html)]

在上述程式碼中，連結標籤協助 `<meta name="x-stylesheet-fallback-test" content="" class="sr-only" />` 程式會產生元素和下列 JavaScript，以用來驗證要求的 *啟動程式。最小 .css* 檔案可在 CDN 上取得。 在此情況下，可以使用 CSS 檔案，讓標籤協助程式 `<link />` 使用 CDN CSS 檔案產生元素。

## <a name="commonly-used-link-tag-helper-attributes"></a>常用的連結標籤協助程式屬性

如需連結標籤協助程式屬性、屬性和方法的 [連結標記](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper)  協助程式，請參閱連結標籤協助程式。

### <a name="href"></a>href

連結資源的慣用位址。 在所有情況下，會將位址視為產生的 HTML。

### <a name="asp-fallback-href"></a>asp-fallback-href

當主要 URL 失敗時，要回復的 CSS 樣式表單 URL。

### <a name="asp-fallback-test-class"></a>asp-fallback-測試類別

要用於回溯測試的樣式表單中定義的類別名稱。 如需詳細資訊，請參閱<xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>。

### <a name="asp-fallback-test-property"></a>asp-fallback-測試-屬性

要用於回溯測試的 CSS 屬性名稱。 如需詳細資訊，請參閱<xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>。

### <a name="asp-fallback-test-value"></a>asp-fallback-測試-值

要用於回溯測試的 CSS 屬性值。 如需詳細資訊，請參閱<xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>。

## <a name="additional-resources"></a>其他資源

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
