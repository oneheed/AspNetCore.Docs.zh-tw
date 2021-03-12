---
title: 將設定遷移至 ASP.NET Core
author: ardalis
description: 瞭解如何將 configuration 從 ASP.NET MVC 專案遷移至 ASP.NET Core MVC 專案。
ms.author: riande
ms.date: 10/14/2016
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
uid: migration/configuration
ms.openlocfilehash: c3957bf45dddcead24f7bb0f2702bf1a08950bdd
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587940"
---
# <a name="migrate-configuration-to-aspnet-core"></a>將設定遷移至 ASP.NET Core

作者：[Steve Smith](https://ardalis.com/) 和 [Scott Addie](https://scottaddie.com)

在前一篇文章中，我們開始將 [ASP.NET mvc 專案遷移至 ASP.NET CORE mvc](xref:migration/mvc)。 在本文中，我們會遷移設定。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/migration/configuration/samples) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="setup-configuration"></a>設定組態

ASP.NET Core 不會再使用舊版 ASP.NET 所使用的 *global.asax* 和 *web.config* 檔案。 在舊版的 ASP.NET 中，應用程式啟動邏輯會置於 `Application_StartUp` *global.asax* 內的方法中。 之後，在 ASP.NET MVC 中， *Startup.cs* 檔案會包含在專案的根目錄中;而且，它會在應用程式啟動時呼叫。 ASP.NET Core 已完全採用此方法，方法是將所有啟動邏輯放在 *Startup.cs* 檔案中。

ASP.NET Core 中也已取代 *web.config* 檔案。 現在設定本身可以設定為 *Startup.cs* 中所述的應用程式啟動程式的一部分。 設定仍可以利用 XML 檔案，但 ASP.NET 核心專案通常會將設定值放在 JSON 格式的檔案中，例如 *appsettings.json* 。 ASP.NET Core 的設定系統也可以輕鬆地存取環境變數，以便為環境特有的值提供 [更安全且更穩固的位置](xref:security/app-secrets) 。 這特別適用于不應簽入原始檔控制的秘密，例如連接字串和 API 金鑰。 若要深入瞭解 ASP.NET Core [中的設定](xref:fundamentals/configuration/index) ，請參閱設定。

在本文中，我們會從 [先前的文章](xref:migration/mvc)中開始部分遷移的 ASP.NET Core 專案。 若要設定設定，請將下列函式和屬性新增至位於專案根目錄中的 *Startup.cs* 檔案：

[!code-csharp[](configuration/samples/WebApp1/src/WebApp1/Startup.cs?range=11-16)]

請注意，此時 *Startup.cs* 檔案不會進行編譯，因為我們仍然需要加入下列 `using` 語句：

```csharp
using Microsoft.Extensions.Configuration;
```

*appsettings.json* 使用適當的專案範本，將檔案加入至專案的根目錄：

![新增 AppSettings JSON](configuration/_static/add-appsettings-json.png)

## <a name="migrate-configuration-settings-from-webconfig"></a>從 web.config 遷移設定設定

我們的 ASP.NET MVC 專案在 *web.config* 的元素中包含必要的資料庫連接字串 `<connectionStrings>` 。 在我們的 ASP.NET Core 專案中，我們會將此資訊儲存在檔案中 *appsettings.json* 。 開啟 *appsettings.json* ，並請注意它已包含下列內容：

[!code-json[](../migration/configuration/samples/WebApp1/src/WebApp1/appsettings.json?highlight=4)]

在上面所述的醒目提示行中，將資料庫的名稱從 **_CHANGE_ME** 變更為您的資料庫名稱。

## <a name="summary"></a>摘要

ASP.NET Core 會將應用程式的所有啟動邏輯放入單一檔案中，而您可以在其中定義和設定必要的服務和相依性。 它會以彈性的設定功能取代 *web.config* 檔案，該功能可利用各種不同的檔案格式，例如 JSON 和環境變數。
