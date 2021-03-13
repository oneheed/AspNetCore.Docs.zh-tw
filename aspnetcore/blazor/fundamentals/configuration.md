---
title: ASP.NET 核心 Blazor 設定
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
ms.openlocfilehash: fd2a9784e92b132d679b2065b37cb37b7dd89cde
ms.sourcegitcommit: 07e7ee573fe4e12be93249a385db745d714ff6ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2021
ms.locfileid: "103413401"
---
# <a name="aspnet-core-blazor-configuration"></a><span data-ttu-id="9cfbe-103">ASP.NET 核心 Blazor 設定</span><span class="sxs-lookup"><span data-stu-id="9cfbe-103">ASP.NET Core Blazor configuration</span></span>

> [!NOTE]
> <span data-ttu-id="9cfbe-104">本主題適用于 Blazor WebAssembly 。</span><span class="sxs-lookup"><span data-stu-id="9cfbe-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="9cfbe-105">如需 ASP.NET Core 應用程式設定的一般指引，請參閱 <xref:fundamentals/configuration/index> 。</span><span class="sxs-lookup"><span data-stu-id="9cfbe-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="9cfbe-106">Blazor WebAssembly 預設會從下列應用程式佈建檔載入設定：</span><span class="sxs-lookup"><span data-stu-id="9cfbe-106">Blazor WebAssembly loads configuration from the following app settings files by default:</span></span>

* <span data-ttu-id="9cfbe-107">`wwwroot/appsettings.json`.</span><span class="sxs-lookup"><span data-stu-id="9cfbe-107">`wwwroot/appsettings.json`.</span></span>
* <span data-ttu-id="9cfbe-108">`wwwroot/appsettings.{ENVIRONMENT}.json`，其中 `{ENVIRONMENT}` 預留位置為應用程式的 [執行時間環境](xref:fundamentals/environments)。</span><span class="sxs-lookup"><span data-stu-id="9cfbe-108">`wwwroot/appsettings.{ENVIRONMENT}.json`, where the `{ENVIRONMENT}` placeholder is the app's [runtime environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="9cfbe-109">應用程式所註冊的其他設定提供者也可以提供設定，但並非所有提供者或提供者功能都適用于 Blazor WebAssembly 應用程式：</span><span class="sxs-lookup"><span data-stu-id="9cfbe-109">Other configuration providers registered by the app can also provide configuration, but not all providers or provider features are appropriate for Blazor WebAssembly apps:</span></span>

* <span data-ttu-id="9cfbe-110">[Azure Key Vault 設定提供者](xref:security/key-vault-configuration)：提供者不支援受控識別和應用程式識別碼 (用戶端識別碼) 用戶端秘密案例。</span><span class="sxs-lookup"><span data-stu-id="9cfbe-110">[Azure Key Vault configuration provider](xref:security/key-vault-configuration): The provider isn't supported for managed identity and application ID (client ID) with client secret scenarios.</span></span> <span data-ttu-id="9cfbe-111">對於任何 ASP.NET Core 應用程式（特別是應用程式），不建議使用用戶端秘密的應用程式識別碼， Blazor WebAssembly 因為用戶端密碼無法安全地用戶端存取 Azure Key Vault 服務。</span><span class="sxs-lookup"><span data-stu-id="9cfbe-111">Application ID with a client secret isn't recommended for any ASP.NET Core app, especially Blazor WebAssembly apps because the client secret can't be secured client-side to access the Azure Key Vault service.</span></span>
* <span data-ttu-id="9cfbe-112">[Azure 應用程式設定提供者](/azure/azure-app-configuration/quickstart-aspnet-core-app)：提供者不適合應用程式， Blazor WebAssembly 因為 Blazor WebAssembly 應用程式不會在 Azure 中的伺服器上執行。</span><span class="sxs-lookup"><span data-stu-id="9cfbe-112">[Azure App configuration provider](/azure/azure-app-configuration/quickstart-aspnet-core-app): The provider isn't appropriate for Blazor WebAssembly apps because Blazor WebAssembly apps don't run on a server in Azure.</span></span>

> [!WARNING]
> <span data-ttu-id="9cfbe-113">Blazor WebAssembly使用者可以看到應用程式中的設定。</span><span class="sxs-lookup"><span data-stu-id="9cfbe-113">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="9cfbe-114">**請勿在應用程式的設定中儲存應用程式秘密、認證或任何其他敏感性資料 Blazor WebAssembly 。**</span><span class="sxs-lookup"><span data-stu-id="9cfbe-114">**Don't store app secrets, credentials, or any other sensitive data in the configuration of a Blazor WebAssembly app.**</span></span>

<span data-ttu-id="9cfbe-115">如需設定提供者的詳細資訊，請參閱 <xref:fundamentals/configuration/index> 。</span><span class="sxs-lookup"><span data-stu-id="9cfbe-115">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="app-settings-configuration"></a><span data-ttu-id="9cfbe-116">應用程式設定</span><span class="sxs-lookup"><span data-stu-id="9cfbe-116">App settings configuration</span></span>

<span data-ttu-id="9cfbe-117">預設會載入應用程式佈建檔案中的設定。</span><span class="sxs-lookup"><span data-stu-id="9cfbe-117">Configuration in app settings files are loaded by default.</span></span> <span data-ttu-id="9cfbe-118">在下列範例中，UI 設定值會儲存在應用程式佈建檔中，並自動載入 Blazor 架構。</span><span class="sxs-lookup"><span data-stu-id="9cfbe-118">In the following example, a UI configuration value is stored in an app settings file and loaded by the Blazor framework automatically.</span></span> <span data-ttu-id="9cfbe-119">此值是由元件讀取。</span><span class="sxs-lookup"><span data-stu-id="9cfbe-119">The value is read by a component.</span></span>

<span data-ttu-id="9cfbe-120">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="9cfbe-120">`wwwroot/appsettings.json`:</span></span>

```json
{
  "h1FontSize": "50px"
}
```

<span data-ttu-id="9cfbe-121">將 <xref:Microsoft.Extensions.Configuration.IConfiguration> 實例插入元件以存取設定資料。</span><span class="sxs-lookup"><span data-stu-id="9cfbe-121">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data.</span></span>

<span data-ttu-id="9cfbe-122">`Pages/ConfigurationExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="9cfbe-122">`Pages/ConfigurationExample.razor`:</span></span>

```razor
@page "/configuration-example"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1 style="font-size:@Configuration["h1FontSize"]">
    Configuration example
</h1>
```

<span data-ttu-id="9cfbe-123">若要將資料夾中的其他設定檔讀取 `wwwroot` 至設定，請使用 <xref:System.Net.Http.HttpClient> 來取得檔案的內容。</span><span class="sxs-lookup"><span data-stu-id="9cfbe-123">To read other configuration files from the `wwwroot` folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="9cfbe-124">下列範例會讀取設定檔 (`cars.json`) 應用程式的設定。</span><span class="sxs-lookup"><span data-stu-id="9cfbe-124">The following example reads a configuration file (`cars.json`) into the app's configuration.</span></span>

<span data-ttu-id="9cfbe-125">`wwwroot/cars.json`:</span><span class="sxs-lookup"><span data-stu-id="9cfbe-125">`wwwroot/cars.json`:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="9cfbe-126">將命名空間加入 <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> 至 `Program.cs` ：</span><span class="sxs-lookup"><span data-stu-id="9cfbe-126">Add the namespace for <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="9cfbe-127">在 `Program.Main` 中 `Program.cs` ，修改現有的 <xref:System.Net.Http.HttpClient> 服務註冊，以使用用戶端來讀取檔案：</span><span class="sxs-lookup"><span data-stu-id="9cfbe-127">In `Program.Main` of `Program.cs`, modify the existing <xref:System.Net.Http.HttpClient> service registration to use the client to read the file:</span></span>

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

## <a name="memory-configuration-source"></a><span data-ttu-id="9cfbe-128">記憶體設定來源</span><span class="sxs-lookup"><span data-stu-id="9cfbe-128">Memory Configuration Source</span></span>

<span data-ttu-id="9cfbe-129">下列範例會使用 <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> 中的 `Program.Main` 來提供額外的設定。</span><span class="sxs-lookup"><span data-stu-id="9cfbe-129">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> in `Program.Main` to supply additional configuration.</span></span>

<span data-ttu-id="9cfbe-130">將命名空間加入 <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> 至 `Program.cs` ：</span><span class="sxs-lookup"><span data-stu-id="9cfbe-130">Add the namespace for <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.Configuration.Memory;
```

<span data-ttu-id="9cfbe-131">`Program.Main` `Program.cs` ：</span><span class="sxs-lookup"><span data-stu-id="9cfbe-131">In `Program.Main` of `Program.cs`:</span></span>

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

<span data-ttu-id="9cfbe-132">將 <xref:Microsoft.Extensions.Configuration.IConfiguration> 實例插入元件以存取設定資料。</span><span class="sxs-lookup"><span data-stu-id="9cfbe-132">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data.</span></span>

<span data-ttu-id="9cfbe-133">`Pages/MemoryConfig.razor`:</span><span class="sxs-lookup"><span data-stu-id="9cfbe-133">`Pages/MemoryConfig.razor`:</span></span>

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

<span data-ttu-id="9cfbe-134">使用 c # 程式碼取得設定的區段 <xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection%2A?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="9cfbe-134">Obtain a section of the configuration in C# code with <xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="9cfbe-135">下列範例會 `wheels` 取得上述範例中的設定區段：</span><span class="sxs-lookup"><span data-stu-id="9cfbe-135">The following example obtains the `wheels` section for the configuration in the preceding example:</span></span>

```razor
@code {
    protected override void OnInitialized()
    {
        var wheelsSection = Configuration.GetSection("wheels");

        ...
    }
}
```

## <a name="authentication-configuration"></a><span data-ttu-id="9cfbe-136">驗證設定</span><span class="sxs-lookup"><span data-stu-id="9cfbe-136">Authentication configuration</span></span>

<span data-ttu-id="9cfbe-137">在應用程式佈建檔中提供驗證設定。</span><span class="sxs-lookup"><span data-stu-id="9cfbe-137">Provide authentication configuration in an app settings file.</span></span>

<span data-ttu-id="9cfbe-138">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="9cfbe-138">`wwwroot/appsettings.json`:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="9cfbe-139">Identity使用中的載入提供者的 <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind%2A?displayProperty=nameWithType> 設定 `Program.Main` 。</span><span class="sxs-lookup"><span data-stu-id="9cfbe-139">Load the configuration for an Identity provider with <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind%2A?displayProperty=nameWithType> in `Program.Main`.</span></span> <span data-ttu-id="9cfbe-140">下列範例會載入 [OIDC 提供者](xref:blazor/security/webassembly/standalone-with-authentication-library)的設定。</span><span class="sxs-lookup"><span data-stu-id="9cfbe-140">The following example loads configuration for an [OIDC provider](xref:blazor/security/webassembly/standalone-with-authentication-library).</span></span>

<span data-ttu-id="9cfbe-141">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="9cfbe-141">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a><span data-ttu-id="9cfbe-142">記錄設定</span><span class="sxs-lookup"><span data-stu-id="9cfbe-142">Logging configuration</span></span>

<span data-ttu-id="9cfbe-143">將的套件參考新增 [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) 至應用程式的專案檔：</span><span class="sxs-lookup"><span data-stu-id="9cfbe-143">Add a package reference for [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERION}" />
```

<span data-ttu-id="9cfbe-144">在上述範例中， `{VERSION}` 預留位置是套件的版本。</span><span class="sxs-lookup"><span data-stu-id="9cfbe-144">In the preceding example, the `{VERSION}` placeholder is the package's version.</span></span> <span data-ttu-id="9cfbe-145">您可以在 [NuGet.org](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration)找到套件版本。</span><span class="sxs-lookup"><span data-stu-id="9cfbe-145">Package versions are found at [NuGet.org](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration).</span></span>

<span data-ttu-id="9cfbe-146">在應用程式佈建檔中，提供記錄設定。</span><span class="sxs-lookup"><span data-stu-id="9cfbe-146">In the app settings file, provide logging configuration.</span></span> <span data-ttu-id="9cfbe-147">記錄設定會載入中 `Program.Main` 。</span><span class="sxs-lookup"><span data-stu-id="9cfbe-147">The logging configuration is loaded in `Program.Main`.</span></span>

<span data-ttu-id="9cfbe-148">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="9cfbe-148">`wwwroot/appsettings.json`:</span></span>

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

<span data-ttu-id="9cfbe-149">將命名空間加入 <xref:Microsoft.Extensions.Logging?displayProperty=fullName> 至 `Program.cs` ：</span><span class="sxs-lookup"><span data-stu-id="9cfbe-149">Add the namespace for <xref:Microsoft.Extensions.Logging?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.Logging;
```

<span data-ttu-id="9cfbe-150">`Program.Main` `Program.cs` ：</span><span class="sxs-lookup"><span data-stu-id="9cfbe-150">In `Program.Main` of `Program.cs`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a><span data-ttu-id="9cfbe-151">Host builder 設定</span><span class="sxs-lookup"><span data-stu-id="9cfbe-151">Host builder configuration</span></span>

<span data-ttu-id="9cfbe-152">從讀取主機建立器 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Configuration?displayProperty=nameWithType> 設定 `Program.Main` 。</span><span class="sxs-lookup"><span data-stu-id="9cfbe-152">Read host builder configuration from <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Configuration?displayProperty=nameWithType> in `Program.Main`.</span></span>

<span data-ttu-id="9cfbe-153">`Program.Main` `Program.cs` ：</span><span class="sxs-lookup"><span data-stu-id="9cfbe-153">In `Program.Main` of `Program.cs`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a><span data-ttu-id="9cfbe-154">快取設定</span><span class="sxs-lookup"><span data-stu-id="9cfbe-154">Cached configuration</span></span>

<span data-ttu-id="9cfbe-155">系統會快取設定檔，以供離線使用。</span><span class="sxs-lookup"><span data-stu-id="9cfbe-155">Configuration files are cached for offline use.</span></span> <span data-ttu-id="9cfbe-156">使用 [漸進式 Web 應用程式 (pwa) ](xref:blazor/progressive-web-app)，您只可以在建立新的部署時，更新設定檔。</span><span class="sxs-lookup"><span data-stu-id="9cfbe-156">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="9cfbe-157">在部署之間編輯設定檔沒有任何作用，因為：</span><span class="sxs-lookup"><span data-stu-id="9cfbe-157">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="9cfbe-158">使用者擁有其持續使用之檔案的快取版本。</span><span class="sxs-lookup"><span data-stu-id="9cfbe-158">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="9cfbe-159">PWA 和檔案 `service-worker.js` `service-worker-assets.js` 必須在編譯時重建，這會在使用者下一次線上流覽應用程式是否已重新部署。</span><span class="sxs-lookup"><span data-stu-id="9cfbe-159">The PWA's `service-worker.js` and `service-worker-assets.js` files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="9cfbe-160">如需有關 Pwa 如何處理背景更新的詳細資訊，請參閱 <xref:blazor/progressive-web-app#background-updates> 。</span><span class="sxs-lookup"><span data-stu-id="9cfbe-160">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>
