---
title: ASP.NET Core 中的環境標籤協助程式
author: pkellner
description: 定義了 ASP.NET Core 環境標籤協助程式，包括所有屬性
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
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
uid: mvc/views/tag-helpers/builtin-th/environment-tag-helper
ms.openlocfilehash: d63364b0c052ba7f9e745e1ad829b8d1ca9122d2
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253120"
---
# <a name="environment-tag-helper-in-aspnet-core"></a>ASP.NET Core 中的環境標籤協助程式

作者：[Peter Kellner](https://peterkellner.net) 和 [Hisham Bin Ateya](https://twitter.com/hishambinateya)

環境標籤協助程式會根據目前的 [裝載環境](xref:fundamentals/environments)，有條件地呈現其所包含的內容。 環境標籤協助程式的單一屬性 `names`，是以逗號分隔的環境名稱清單。 如果任何提供的環境名稱符合目前環境，則會轉譯含括的內容。

如需標籤協助程式的概觀，請參閱 <xref:mvc/views/tag-helpers/intro>。

## <a name="environment-tag-helper-attributes"></a>環境標籤協助程式屬性

### <a name="names"></a>名稱

`names` 會接受單一主控環境名稱或以逗號分隔的主控環境名稱清單，這些名稱會觸發轉譯含括的內容。

環境值會與 [IWebHostEnvironment. EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)所傳回的目前值進行比較。 比較會忽略大小寫。

下列範例使用環境標籤協助程式。 如果主控環境為「暫存」或「生產」，將會轉譯內容：

```cshtml
<environment names="Staging,Production">
    <strong>IWebHostEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

::: moniker range=">= aspnetcore-2.0"

## <a name="include-and-exclude-attributes"></a>include 和 exclude 屬性

`include`& `exclude` 屬性會根據包含或排除的主控環境名稱，來呈現已包含的內容。

### <a name="include"></a>include

`include` 屬性會表現出類似 `names` 屬性的行為。 屬性值中所列的環境 `include` 必須符合應用程式的裝載環境 ([IWebHostEnvironment EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)) ，才能轉譯標記的內容 `<environment>` 。

```cshtml
<environment include="Staging,Production">
    <strong>IWebHostEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

### <a name="exclude"></a>排除

與 `include` 屬性相反，當主控環境與 `exclude` 屬性值中列出的環境不相符時，將轉譯 `<environment>` 標記的內容。

```cshtml
<environment exclude="Development">
    <strong>IWebHostEnvironment.EnvironmentName is not Development</strong>
</environment>
```

::: moniker-end

## <a name="additional-resources"></a>其他資源

* <xref:fundamentals/environments>
