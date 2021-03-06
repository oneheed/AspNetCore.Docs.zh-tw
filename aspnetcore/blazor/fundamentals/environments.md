---
title: ASP.NET 核心 Blazor 環境
author: guardrex
description: 瞭解中的環境 Blazor ，包括如何設定 Blazor WebAssembly 應用程式的環境。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/11/2020
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
uid: blazor/fundamentals/environments
ms.openlocfilehash: 86484fe2e2ce8e20e397a78f118d8b1bbe0cc88a
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2021
ms.locfileid: "102395106"
---
# <a name="aspnet-core-blazor-environments"></a>ASP.NET 核心 Blazor 環境

> [!NOTE]
> 本主題適用于 Blazor WebAssembly 。 如需有關 ASP.NET Core 應用程式設定的一般指引，以說明應用程式的使用方法 Blazor Server ，請參閱 <xref:fundamentals/environments> 。

在本機執行應用程式時，環境會預設為開發環境。 當應用程式發佈時，環境會預設為生產環境。

裝載解決方案的用戶端 Blazor 應用程式 (**`Client`**) 會透過將 Blazor WebAssembly **`Server`** 環境傳達給瀏覽器的中介軟體，從解決方案的應用程式決定環境。 **`Server`** 應用程式會新增名為的標頭， `blazor-environment` 並以環境作為標頭的值。 **`Client`** 應用程式會讀取標頭。 **`Server`** 解決方案的應用程式是 ASP.NET Core 應用程式，因此您可以在中找到有關如何設定環境的詳細資訊 <xref:fundamentals/environments> 。

針對在本機執行的獨立 Blazor WebAssembly 應用程式，程式開發伺服器會新增 `blazor-environment` 標頭以指定開發環境。 若要指定其他裝載環境的環境，請新增 `blazor-environment` 標頭。

在 IIS 的下列範例中，會將自訂標頭 (`blazor-environment`) 新增至已發佈的檔案 `web.config` 。 檔案 `web.config` 位於 `bin/Release/{TARGET FRAMEWORK}/publish` 資料夾中，其中預留位置 `{TARGET FRAMEWORK}` 是目標 framework：

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

藉由插入和讀取屬性，在元件中取得應用程式的環境 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> 。

`Pages/ReadEnvironment.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/environments/ReadEnvironment.razor?highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/environments/ReadEnvironment.razor?highlight=3,7)]

::: moniker-end

在啟動期間，會 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> 透過屬性公開，以在主機產生器程式 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> 代碼中啟用環境特定的邏輯。

`Program.Main` `Program.cs` ：

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

提供的下列便利擴充方法可 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions> 讓您檢查目前的環境，以進行開發、生產、預備和自訂環境名稱：

* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsDevelopment%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsProduction%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsStaging%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsEnvironment%2A>

`Program.Main` `Program.cs` ：

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
