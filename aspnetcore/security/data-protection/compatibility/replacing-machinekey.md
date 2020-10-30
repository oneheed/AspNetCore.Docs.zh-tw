---
title: 取代 ASP.NET Core 中的 ASP.NET machineKey
author: rick-anderson
description: 探索如何取代 ASP.NET 中的 machineKey，以允許使用新的、更安全的資料保護系統。
ms.author: riande
ms.date: 04/06/2019
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
uid: security/data-protection/compatibility/replacing-machinekey
ms.openlocfilehash: 7c6766fa20b0df021013da77b5d88fecefd40c62
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053055"
---
# <a name="replace-the-aspnet-machinekey-in-aspnet-core"></a>取代 ASP.NET Core 中的 ASP.NET machineKey

<a name="compatibility-replacing-machinekey"></a>

`<machineKey>`ASP.NET 中的元素實作為[可取代](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/)的專案。 這可讓大部分的呼叫 ASP.NET 密碼編譯常式，以透過取代的資料保護機制（包括新的資料保護系統）來路由傳送。

## <a name="package-installation"></a>套件安裝

> [!NOTE]
> 新的資料保護系統只能安裝到以 .NET 4.5.1 或更新版本為目標的現有 ASP.NET 應用程式。 如果應用程式是以 .NET 4.5 或更低版本為目標，則安裝會失敗。

若要將新的資料保護系統安裝到現有的 ASP.NET 4.5.1 + 專案，請 Microsoft.AspNetCore.DataProtection.SystemWeb 安裝套件。 這將會使用 [預設](xref:security/data-protection/configuration/default-settings) 的設定來具現化資料保護系統。

當您安裝封裝時，它會在 *Web.config* 中插入一行，告知 ASP.NET 將它用於 [大部分的密碼編譯作業](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/)，包括表單驗證、view state 和 MachineKey 的呼叫。保護。 插入的行如下所示。

```xml
<machineKey compatibilityMode="Framework45" dataProtectorType="..." />
```

>[!TIP]
> 您可以檢查欄位（如下列範例所示），以判斷新的資料保護系統是否為作用 `__VIEWSTATE` 中。 "CfDJ8" 是魔術 "09 F0 C9 F0" 標頭的 base64 標記法，用來識別受資料保護系統保護的承載。

```html
<input type="hidden" name="__VIEWSTATE" id="__VIEWSTATE" value="CfDJ8AWPr2EQPTBGs3L2GCZOpk...">
```

## <a name="package-configuration"></a>封裝設定

資料保護系統會使用預設的零安裝設定來具現化。 不過，因為依預設，索引鍵會保存到本機檔案系統，所以這不適用於部署在伺服器陣列中的應用程式。 若要解決此問題，您可以建立子類別 DataProtectionStartup 和覆寫其 ConfigureServices 方法的類型，以提供設定。

以下是自訂資料保護啟動類型的範例，其會設定金鑰的保存位置和待用加密方式。 它也會藉由提供自己的應用程式名稱，來覆寫預設的應用程式隔離原則。

```csharp
using System;
using System.IO;
using Microsoft.AspNetCore.DataProtection;
using Microsoft.AspNetCore.DataProtection.SystemWeb;
using Microsoft.Extensions.DependencyInjection;

namespace DataProtectionDemo
{
    public class MyDataProtectionStartup : DataProtectionStartup
    {
        public override void ConfigureServices(IServiceCollection services)
        {
            services.AddDataProtection()
                .SetApplicationName("my-app")
                .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\myapp-keys\"))
                .ProtectKeysWithCertificate("thumbprint");
        }
    }
}
```

>[!TIP]
> 您也可以使用 `<machineKey applicationName="my-app" ... />` 來取代明確呼叫 SetApplicationName。 這是一種便利的機制，可避免強制開發人員建立 DataProtectionStartup 衍生型別（如果他們想要設定應用程式名稱的話）。

若要啟用此自訂設定，請返回 Web.config，並尋找 `<appSettings>` 套件安裝新增至設定檔的元素。 它看起來會像下列標記：

```xml
<appSettings>
  <!--
  If you want to customize the behavior of the ASP.NET Core Data Protection stack, set the
  "aspnet:dataProtectionStartupType" switch below to be the fully-qualified name of a
  type which subclasses Microsoft.AspNetCore.DataProtection.SystemWeb.DataProtectionStartup.
  -->
  <add key="aspnet:dataProtectionStartupType" value="" />
</appSettings>
```

使用您剛才建立之 DataProtectionStartup 衍生類型的元件限定名稱填入空白值。 如果應用程式的名稱是 DataProtectionDemo，這看起來會如下所示。

```xml
<add key="aspnet:dataProtectionStartupType"
     value="DataProtectionDemo.MyDataProtectionStartup, DataProtectionDemo" />
```

新設定的資料保護系統現在已準備好在應用程式內使用。
