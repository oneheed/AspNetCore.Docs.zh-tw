---
title: ASP.NET Core Blazor 靜態檔案
author: guardrex
description: 瞭解如何設定和管理應用程式的靜態檔案 Blazor 。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/27/2021
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
uid: blazor/fundamentals/static-files
ms.openlocfilehash: 709250251113014027fe86c6a373e58a9c2a5009
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/10/2021
ms.locfileid: "100110008"
---
# <a name="aspnet-core-blazor-static-files"></a>ASP.NET Core Blazor 靜態檔案

*本文適用于 Blazor Server 。*

若要使用或其他設定來建立其他檔案對應 <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> ，請使用下列 **其中一** 種方法。 在下列範例中， `{EXTENSION}` 預留位置是副檔名，而 `{CONTENT TYPE}` 預留位置是內容類型。

* 使用下列方法，透過 () 的相依性 [插入 (DI) ](xref:blazor/fundamentals/dependency-injection) 設定選項 `Startup.ConfigureServices` `Startup.cs` <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> ：

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  services.Configure<StaticFileOptions>(options =>
  {
      options.ContentTypeProvider = provider;
  });
  ```

  因為此方法會設定用來提供服務的相同檔案提供者 `blazor.server.js` ，請確定您的自訂設定不會干擾服務 `blazor.server.js` 。 例如，請不要藉由設定提供者來移除 JavaScript 檔案的對應 `provider.Mappings.Remove(".js")` 。

* <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>在 () 中使用兩個呼叫 `Startup.Configure` `Startup.cs` ：
  * 在第一次呼叫時設定自訂檔案提供者 <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> 。
  * 第二個中間件會 `blazor.server.js` 提供，其使用架構所提供的預設靜態檔案設定 Blazor 。

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  app.UseStaticFiles(new StaticFileOptions { ContentTypeProvider = provider });
  app.UseStaticFiles();
  ```

* 您可以 `_framework/blazor.server.js` 使用 <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> 來執行自訂靜態檔案中介軟體，以避免干擾服務：

  ```csharp
  app.MapWhen(ctx => !ctx.Request.Path
      .StartsWithSegments("_framework/blazor.server.js", 
          subApp => subApp.UseStaticFiles(new StaticFileOptions(){ ... })));
  ```
