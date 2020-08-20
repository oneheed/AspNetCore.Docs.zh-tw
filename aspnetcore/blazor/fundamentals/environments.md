---
title: ASP.NET Core Blazor 環境
author: guardrex
description: 瞭解中的環境 Blazor ，包括如何設定 Blazor WebAssembly 應用程式的環境。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
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
uid: blazor/fundamentals/environments
ms.openlocfilehash: 5f9de91eeab932dd139abdd1bf2eedadc8d396b5
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628257"
---
# <a name="aspnet-core-no-locblazor-environments"></a>ASP.NET Core Blazor 環境

> [!NOTE]
> 本主題適用于 Blazor WebAssembly 。 如需 ASP.NET Core 應用程式設定的一般指引，請參閱 <xref:fundamentals/environments> 。

在本機執行應用程式時，環境會預設為開發環境。 當應用程式發佈時，環境會預設為生產環境。

裝載的 Blazor WebAssembly 應用程式會透過可透過新增標頭將環境傳達給瀏覽器的中介軟體，從伺服器挑選環境 `blazor-environment` 。 標頭的值是環境。 託管 Blazor 應用程式和伺服器應用程式會共用相同的環境。 如需詳細資訊，包括如何設定環境的詳細資訊，請參閱 <xref:fundamentals/environments> 。

針對在本機執行的獨立應用程式，程式開發伺服器會新增 `blazor-environment` 標頭以指定開發環境。 若要指定其他裝載環境的環境，請新增 `blazor-environment` 標頭。

在 IIS 的下列範例中，將自訂標頭加入至已發佈的檔案 `web.config` 。 檔案 `web.config` 位於 `bin/Release/{TARGET FRAMEWORK}/publish` 下列資料夾：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>

    ...

    <httpProtocol>
      <customHeaders>
        <add name="blazor-environment" value="Staging" />
      </customHeaders>
    </httpProtocol>
  </system.webServer>
</configuration>
```

> [!NOTE]
> 若要使用 `web.config` 應用程式發佈至資料夾時不會覆寫之 IIS 的自訂檔案 `publish` ，請參閱 <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig> 。

藉由插入和讀取屬性，取得應用程式在元件中的環境 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> ：

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

在啟動期間， <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> 會透過屬性公開， <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> 讓開發人員可以在其程式碼中擁有環境特定的邏輯：

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

下列便利的擴充方法可讓您檢查目前的環境中的開發、生產、預備和自訂環境名稱：

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* `IsEnvironment("{ENVIRONMENT NAME}")`

```csharp
if (builder.HostEnvironment.IsStaging())
{
    ...
};

if (builder.HostEnvironment.IsEnvironment("Custom"))
{
    ...
};
```

<xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType>當服務無法使用時，可以在啟動期間使用此屬性 <xref:Microsoft.AspNetCore.Components.NavigationManager> 。

## <a name="additional-resources"></a>其他資源

* <xref:fundamentals/environments>
