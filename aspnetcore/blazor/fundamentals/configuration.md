---
title: ASP.NET Core Blazor 設定
author: guardrex
description: 深入瞭解 Blazor 應用程式的設定，包括應用程式設定、驗證和記錄設定。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/10/2020
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
uid: blazor/fundamentals/configuration
ms.openlocfilehash: 5889d775c09ee23f19bf3ff59344c52d469c4bdc
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2020
ms.locfileid: "97485963"
---
# <a name="aspnet-core-no-locblazor-configuration"></a>ASP.NET Core Blazor 設定

> [!NOTE]
> 本主題適用于 Blazor WebAssembly 。 如需 ASP.NET Core 應用程式設定的一般指引，請參閱 <xref:fundamentals/configuration/index> 。

Blazor WebAssembly 預設會從下列應用程式佈建檔載入設定：

* `wwwroot/appsettings.json`.
* `wwwroot/appsettings.{ENVIRONMENT}.json`，其中 `{ENVIRONMENT}` 預留位置為應用程式的 [執行時間環境](xref:fundamentals/environments)。

應用程式所註冊的其他設定提供者也可以提供設定，但並非所有提供者或提供者功能都適用于 Blazor WebAssembly 應用程式：

* [Azure Key Vault 設定提供者](xref:security/key-vault-configuration)：使用用戶端秘密案例時，提供者不支援受控識別和應用程式識別碼 (用戶端識別碼) 。 對於任何 ASP.NET Core 應用程式（特別是應用程式），不建議使用用戶端秘密的應用程式識別碼， Blazor WebAssembly 因為用戶端密碼無法安全地用戶端存取 Azure Key Vault 服務。
* [Azure App 設定提供者](/azure/azure-app-configuration/quickstart-aspnet-core-app)：提供者不適用於 Blazor WebAssembly 應用程式，因為 Blazor WebAssembly 應用程式不會在 Azure 中的伺服器上執行。

> [!WARNING]
> Blazor WebAssembly使用者可以看到應用程式中的設定。 **請勿在應用程式的設定中儲存應用程式秘密、認證或任何其他敏感性資料 Blazor WebAssembly 。**

如需設定提供者的詳細資訊，請參閱 <xref:fundamentals/configuration/index> 。

## <a name="app-settings-configuration"></a>應用程式設定

預設會載入應用程式佈建檔案中的設定。 在下列範例中，UI 設定值會儲存在應用程式佈建檔中，並自動載入 Blazor 架構。 此值是由元件讀取。

`wwwroot/appsettings.json`:

```json
{
  "h1FontSize": "50px"
}
```

將 <xref:Microsoft.Extensions.Configuration.IConfiguration> 實例插入元件以存取設定資料。

`Pages/ConfigurationExample.razor`:

```razor
@page "/configuration-example"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1 style="font-size:@Configuration["h1FontSize"]">
    Configuration example
</h1>
```

若要將資料夾中的其他設定檔讀取 `wwwroot` 至設定，請使用 <xref:System.Net.Http.HttpClient> 來取得檔案的內容。 下列範例會讀取設定檔 (`cars.json`) 應用程式的設定。

`wwwroot/cars.json`:

```json
{
    "size": "tiny"
}
```

將命名空間加入 <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> 至 `Program.cs` ：

```csharp
using Microsoft.Extensions.Configuration;
```

在 `Program.Main` 中 `Program.cs` ，修改現有的 <xref:System.Net.Http.HttpClient> 服務註冊，以使用用戶端來讀取檔案：

```csharp
var http = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddScoped(sp => http);

using var response = await http.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

## <a name="custom-configuration-provider-with-ef-core"></a>具有 EF Core 的自訂設定提供者

EF Core 中示範的自訂設定提供者可 <xref:fundamentals/configuration/index#custom-configuration-provider> 搭配 Blazor WebAssembly 應用程式使用。

> [!WARNING]
> 使用應用程式載入的資料庫連接字串和資料庫並 Blazor WebAssembly 不安全，不應該用來儲存敏感性資料。

將和的封裝參考新增 [`Microsoft.EntityFrameworkCore`](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore) [`Microsoft.EntityFrameworkCore.InMemory`](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory) 至應用程式的專案檔。

新增中所述的 EF Core 設定類別 <xref:fundamentals/configuration/index#custom-configuration-provider> 。

將和的命名空間新增 <xref:Microsoft.EntityFrameworkCore?displayProperty=fullName> <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> 至 `Program.cs` ：

```csharp
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration.Memory;
```

`Program.Main` `Program.cs` ：

```csharp
builder.Configuration.AddEFConfiguration(
    options => options.UseInMemoryDatabase("InMemoryDb"));
```

將 <xref:Microsoft.Extensions.Configuration.IConfiguration> 實例插入元件以存取設定資料。

`Pages/EFCoreConfig.razor`:

```razor
@page "/efcore-config"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>EF Core configuration example</h1>

<h2>Quotes</h2>

<ul>
    <li>@Configuration["quote1"]</li>
    <li>@Configuration["quote2"]</li>
    <li>@Configuration["quote3"]</li>
</ul>

<p>
    Quotes &copy;2005 
    <a href="https://www.uphe.com/">Universal Pictures</a>: 
    <a href="https://www.uphe.com/movies/serenity">Serenity</a>
</p>
```

## <a name="memory-configuration-source"></a>記憶體設定來源

下列範例會使用 <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> 中的 `Program.Main` 來提供額外的設定。

將命名空間加入 <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> 至 `Program.cs` ：

```csharp
using Microsoft.Extensions.Configuration.Memory;
```

`Program.Main` `Program.cs` ：

```csharp
var vehicleData = new Dictionary<string, string>()
{
    { "color", "blue" },
    { "type", "car" },
    { "wheels:count", "3" },
    { "wheels:brand", "Blazin" },
    { "wheels:brand:type", "rally" },
    { "wheels:year", "2008" },
};

var memoryConfig = new MemoryConfigurationSource { InitialData = vehicleData };

builder.Configuration.Add(memoryConfig);
```

將 <xref:Microsoft.Extensions.Configuration.IConfiguration> 實例插入元件以存取設定資料。

`Pages/MemoryConfig.razor`:

```razor
@page "/memory-config"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Memory configuration example</h1>

<h2>General specifications</h2>

<ul>
    <li>Color: @Configuration["color"]</li>
    <li>Type: @Configuration["type"]</li>
</ul>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</li>
    <li>Brand: @Configuration["wheels:brand"]</li>
    <li>Type: @Configuration["wheels:brand:type"]</li>
    <li>Year: @Configuration["wheels:year"]</li>
</ul>
```

使用 c # 程式碼取得設定的區段 <xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection%2A?displayProperty=nameWithType> 。 下列範例會 `wheels` 取得上述範例中的設定區段：

```razor
@code {
    protected override void OnInitialized()
    {
        var wheelsSection = Configuration.GetSection("wheels");

        ...
    }
}
```

## <a name="authentication-configuration"></a>驗證設定

在應用程式佈建檔中提供驗證設定。

`wwwroot/appsettings.json`:

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

Identity使用中的載入提供者的 <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind%2A?displayProperty=nameWithType> 設定 `Program.Main` 。 下列範例會載入 [OIDC 提供者](xref:blazor/security/webassembly/standalone-with-authentication-library)的設定。

`Program.cs`:

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a>記錄設定

將的封裝參考新增 [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) 至應用程式的專案檔。 在應用程式佈建檔中，提供記錄設定。 記錄設定會載入中 `Program.Main` 。

`wwwroot/appsettings.json`:

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```

將命名空間加入 <xref:Microsoft.Extensions.Logging?displayProperty=fullName> 至 `Program.cs` ：

```csharp
using Microsoft.Extensions.Logging;
```

`Program.Main` `Program.cs` ：

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a>Host builder 設定

從讀取主機建立器 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Configuration?displayProperty=nameWithType> 設定 `Program.Main` 。

`Program.Main` `Program.cs` ：

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a>快取設定

系統會快取設定檔，以供離線使用。 使用 [漸進式 Web 應用程式 (pwa) ](xref:blazor/progressive-web-app)，您只可以在建立新的部署時，更新設定檔。 在部署之間編輯設定檔沒有任何作用，因為：

* 使用者擁有其持續使用之檔案的快取版本。
* PWA 和檔案 `service-worker.js` `service-worker-assets.js` 必須在編譯時重建，這會在使用者下一次線上流覽應用程式是否已重新部署。

如需有關 Pwa 如何處理背景更新的詳細資訊，請參閱 <xref:blazor/progressive-web-app#background-updates> 。
