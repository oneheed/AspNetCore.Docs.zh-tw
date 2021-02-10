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
# <a name="aspnet-core-blazor-static-files"></a><span data-ttu-id="f99e0-103">ASP.NET Core Blazor 靜態檔案</span><span class="sxs-lookup"><span data-stu-id="f99e0-103">ASP.NET Core Blazor static files</span></span>

<span data-ttu-id="f99e0-104">*本文適用于 Blazor Server 。*</span><span class="sxs-lookup"><span data-stu-id="f99e0-104">*This article applies to Blazor Server.*</span></span>

<span data-ttu-id="f99e0-105">若要使用或其他設定來建立其他檔案對應 <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> ，請使用下列 **其中一** 種方法。</span><span class="sxs-lookup"><span data-stu-id="f99e0-105">To create additional file mappings with a <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> or configure other <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>, use **one** of the following approaches.</span></span> <span data-ttu-id="f99e0-106">在下列範例中， `{EXTENSION}` 預留位置是副檔名，而 `{CONTENT TYPE}` 預留位置是內容類型。</span><span class="sxs-lookup"><span data-stu-id="f99e0-106">In the following examples, the `{EXTENSION}` placeholder is the file extension, and the `{CONTENT TYPE}` placeholder is the content type.</span></span>

* <span data-ttu-id="f99e0-107">使用下列方法，透過 () 的相依性 [插入 (DI) ](xref:blazor/fundamentals/dependency-injection) 設定選項 `Startup.ConfigureServices` `Startup.cs` <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> ：</span><span class="sxs-lookup"><span data-stu-id="f99e0-107">Configure options through [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection) in `Startup.ConfigureServices` (`Startup.cs`) using <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>:</span></span>

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

  <span data-ttu-id="f99e0-108">因為此方法會設定用來提供服務的相同檔案提供者 `blazor.server.js` ，請確定您的自訂設定不會干擾服務 `blazor.server.js` 。</span><span class="sxs-lookup"><span data-stu-id="f99e0-108">Because this approach configures the same file provider used to serve `blazor.server.js`, make sure that your custom configuration doesn't interfere with serving `blazor.server.js`.</span></span> <span data-ttu-id="f99e0-109">例如，請不要藉由設定提供者來移除 JavaScript 檔案的對應 `provider.Mappings.Remove(".js")` 。</span><span class="sxs-lookup"><span data-stu-id="f99e0-109">For example, don't remove the mapping for JavaScript files by configuring the provider with `provider.Mappings.Remove(".js")`.</span></span>

* <span data-ttu-id="f99e0-110"><xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>在 () 中使用兩個呼叫 `Startup.Configure` `Startup.cs` ：</span><span class="sxs-lookup"><span data-stu-id="f99e0-110">Use two calls to <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> in `Startup.Configure` (`Startup.cs`):</span></span>
  * <span data-ttu-id="f99e0-111">在第一次呼叫時設定自訂檔案提供者 <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> 。</span><span class="sxs-lookup"><span data-stu-id="f99e0-111">Configure the custom file provider in the first call with <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>.</span></span>
  * <span data-ttu-id="f99e0-112">第二個中間件會 `blazor.server.js` 提供，其使用架構所提供的預設靜態檔案設定 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="f99e0-112">The second middleware serves `blazor.server.js`, which uses the default static files configuration provided by the Blazor framework.</span></span>

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  app.UseStaticFiles(new StaticFileOptions { ContentTypeProvider = provider });
  app.UseStaticFiles();
  ```

* <span data-ttu-id="f99e0-113">您可以 `_framework/blazor.server.js` 使用 <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> 來執行自訂靜態檔案中介軟體，以避免干擾服務：</span><span class="sxs-lookup"><span data-stu-id="f99e0-113">You can avoid interfering with serving `_framework/blazor.server.js` by using <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> to execute a custom Static File Middleware:</span></span>

  ```csharp
  app.MapWhen(ctx => !ctx.Request.Path
      .StartsWithSegments("_framework/blazor.server.js", 
          subApp => subApp.UseStaticFiles(new StaticFileOptions(){ ... })));
  ```
