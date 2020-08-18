---
title: ASP.NET Core Blazor 設定
author: guardrex
description: 深入瞭解 Blazor 應用程式的設定，包括應用程式設定、驗證和記錄設定。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/29/2020
no-loc:
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
ms.openlocfilehash: 159b6da106ab21946ae02a970a28e1f0309acf6c
ms.sourcegitcommit: dfea24471f4f3d7904faa92fe60c000853bddc3b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88504576"
---
# <a name="aspnet-core-no-locblazor-configuration"></a>ASP.NET Core Blazor 設定

> [!NOTE]
> 本主題適用于 Blazor WebAssembly 。 如需 ASP.NET Core 應用程式設定的一般指引，請參閱 <xref:fundamentals/configuration/index> 。

Blazor WebAssembly 預設會從應用程式佈建檔載入設定：

* `wwwroot/appsettings.json`
* `wwwroot/appsettings.{ENVIRONMENT}.json`

應用程式所註冊的其他設定提供者也可以提供設定。

並非所有提供者或提供者功能都適用于 Blazor WebAssembly 應用程式：

* [Azure Key Vault 設定提供者](xref:security/key-vault-configuration)：使用用戶端秘密案例時，提供者不支援受控識別和應用程式識別碼 (用戶端識別碼) 。 對於任何 ASP.NET Core 應用程式（特別是應用程式），不建議使用用戶端秘密的應用程式識別碼， Blazor WebAssembly 因為用戶端密碼無法安全地用戶端存取服務。
* [Azure App 設定提供者](/azure/azure-app-configuration/quickstart-aspnet-core-app)：提供者不適用於 Blazor WebAssembly 應用程式，因為 Blazor WebAssembly 應用程式不會在 Azure 中的伺服器上執行。

> [!WARNING]
> Blazor WebAssembly使用者可以看到應用程式中的設定。 **請勿在設定中儲存應用程式秘密或認證。**

如需設定提供者的詳細資訊，請參閱 <xref:fundamentals/configuration/index> 。

## <a name="app-settings-configuration"></a>應用程式設定

`wwwroot/appsettings.json`:

```json
{
  "message": "Hello from config!"
}
```

將 <xref:Microsoft.Extensions.Configuration.IConfiguration> 實例插入元件以存取設定資料：

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

## <a name="custom-configuration-provider-with-ef-core"></a>具有 EF Core 的自訂設定提供者

EF Core 中示範的自訂設定提供者可 <xref:fundamentals/configuration/index#custom-configuration-provider> 搭配 Blazor WebAssembly 應用程式使用。

在 () 中，使用下列程式碼新增範例的設定提供者 `Program.Main` `Program.cs` ：

```csharp
builder.Configuration.AddEFConfiguration(
    options => options.UseInMemoryDatabase("InMemoryDb"));
```

將 <xref:Microsoft.Extensions.Configuration.IConfiguration> 實例插入元件以存取設定資料：

```razor
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<ul>
    <li>@Configuration["quote1"]</li>
    <li>@Configuration["quote2"]</li>
    <li>@Configuration["quote3"]</li>
</ul>
```

## <a name="memory-configuration-source"></a>記憶體設定來源

下列範例會使用 <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> 來提供其他設定：

`Program.Main`:

```csharp
using Microsoft.Extensions.Configuration.Memory;

...

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

...

builder.Configuration.Add(memoryConfig);
```

將 <xref:Microsoft.Extensions.Configuration.IConfiguration> 實例插入元件以存取設定資料：

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</li>
    <li>Brand: @Configuration["wheels:brand"]</li>
    <li>Type: @Configuration["wheels:brand:type"]</li>
    <li>Year: @Configuration["wheels:year"]</li>
</ul>

@code {
    protected override void OnInitialized()
    {
        var wheelsSection = Configuration.GetSection("wheels");
        
        ...
    }
}
```

若要將資料夾中的其他設定檔讀取 `wwwroot` 至設定，請使用 <xref:System.Net.Http.HttpClient> 來取得檔案的內容。 使用這種方法時，現有的 <xref:System.Net.Http.HttpClient> 服務註冊可以使用建立的本機用戶端來讀取檔案，如下列範例所示：

`wwwroot/cars.json`:

```json
{
    "size": "tiny"
}
```

`Program.Main`:

```csharp
using Microsoft.Extensions.Configuration;

...

var client = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddScoped(sp => client);

using var response = await client.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

## <a name="authentication-configuration"></a>驗證設定

`wwwroot/appsettings.json`:

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

`Program.Main`:

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a>記錄設定

新增的套件參考 [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) ：

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

針對預留位置 `{VERSION}` ，可以在套件的 **版本歷程記錄** （ [NuGet.org](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration)）中找到符合應用程式共用架構版本的最新穩定版本套件。

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

`Program.Main`:

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a>Host builder 設定

`Program.Main`:

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a>快取設定

系統會快取設定檔，以供離線使用。 使用 [漸進式 Web 應用程式 (pwa) ](xref:blazor/progressive-web-app)，您只可以在建立新的部署時，更新設定檔。 在部署之間編輯設定檔沒有任何作用，因為：

* 使用者擁有其持續使用之檔案的快取版本。
* PWA 和檔案 `service-worker.js` `service-worker-assets.js` 必須在編譯時重建，這會在使用者下一次線上流覽應用程式是否已重新部署。

如需有關 Pwa 如何處理背景更新的詳細資訊，請參閱 <xref:blazor/progressive-web-app#background-updates> 。
