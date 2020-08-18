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
# <a name="aspnet-core-no-locblazor-configuration"></a><span data-ttu-id="ac4d9-103">ASP.NET Core Blazor 設定</span><span class="sxs-lookup"><span data-stu-id="ac4d9-103">ASP.NET Core Blazor configuration</span></span>

> [!NOTE]
> <span data-ttu-id="ac4d9-104">本主題適用于 Blazor WebAssembly 。</span><span class="sxs-lookup"><span data-stu-id="ac4d9-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="ac4d9-105">如需 ASP.NET Core 應用程式設定的一般指引，請參閱 <xref:fundamentals/configuration/index> 。</span><span class="sxs-lookup"><span data-stu-id="ac4d9-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="ac4d9-106">Blazor WebAssembly 預設會從應用程式佈建檔載入設定：</span><span class="sxs-lookup"><span data-stu-id="ac4d9-106">Blazor WebAssembly loads configuration from app settings files by default:</span></span>

* `wwwroot/appsettings.json`
* `wwwroot/appsettings.{ENVIRONMENT}.json`

<span data-ttu-id="ac4d9-107">應用程式所註冊的其他設定提供者也可以提供設定。</span><span class="sxs-lookup"><span data-stu-id="ac4d9-107">Other configuration providers registered by the app can also provide configuration.</span></span>

<span data-ttu-id="ac4d9-108">並非所有提供者或提供者功能都適用于 Blazor WebAssembly 應用程式：</span><span class="sxs-lookup"><span data-stu-id="ac4d9-108">Not all providers or provider features are appropriate for Blazor WebAssembly apps:</span></span>

* <span data-ttu-id="ac4d9-109">[Azure Key Vault 設定提供者](xref:security/key-vault-configuration)：使用用戶端秘密案例時，提供者不支援受控識別和應用程式識別碼 (用戶端識別碼) 。</span><span class="sxs-lookup"><span data-stu-id="ac4d9-109">[Azure Key Vault configuration provider](xref:security/key-vault-configuration): The provider isn't supported for managed identity and application ID (client ID) with client secret scenarios.</span></span> <span data-ttu-id="ac4d9-110">對於任何 ASP.NET Core 應用程式（特別是應用程式），不建議使用用戶端秘密的應用程式識別碼， Blazor WebAssembly 因為用戶端密碼無法安全地用戶端存取服務。</span><span class="sxs-lookup"><span data-stu-id="ac4d9-110">Application ID with a client secret isn't recommended for any ASP.NET Core app, especially Blazor WebAssembly apps because the client secret can't be secured client-side to access to the service.</span></span>
* <span data-ttu-id="ac4d9-111">[Azure App 設定提供者](/azure/azure-app-configuration/quickstart-aspnet-core-app)：提供者不適用於 Blazor WebAssembly 應用程式，因為 Blazor WebAssembly 應用程式不會在 Azure 中的伺服器上執行。</span><span class="sxs-lookup"><span data-stu-id="ac4d9-111">[Azure App configuration provider](/azure/azure-app-configuration/quickstart-aspnet-core-app): The provider isn't appropriate for Blazor WebAssembly apps because Blazor WebAssembly apps don't run on a server in Azure.</span></span>

> [!WARNING]
> <span data-ttu-id="ac4d9-112">Blazor WebAssembly使用者可以看到應用程式中的設定。</span><span class="sxs-lookup"><span data-stu-id="ac4d9-112">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="ac4d9-113">**請勿在設定中儲存應用程式秘密或認證。**</span><span class="sxs-lookup"><span data-stu-id="ac4d9-113">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="ac4d9-114">如需設定提供者的詳細資訊，請參閱 <xref:fundamentals/configuration/index> 。</span><span class="sxs-lookup"><span data-stu-id="ac4d9-114">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="app-settings-configuration"></a><span data-ttu-id="ac4d9-115">應用程式設定</span><span class="sxs-lookup"><span data-stu-id="ac4d9-115">App settings configuration</span></span>

<span data-ttu-id="ac4d9-116">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="ac4d9-116">`wwwroot/appsettings.json`:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="ac4d9-117">將 <xref:Microsoft.Extensions.Configuration.IConfiguration> 實例插入元件以存取設定資料：</span><span class="sxs-lookup"><span data-stu-id="ac4d9-117">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

## <a name="custom-configuration-provider-with-ef-core"></a><span data-ttu-id="ac4d9-118">具有 EF Core 的自訂設定提供者</span><span class="sxs-lookup"><span data-stu-id="ac4d9-118">Custom configuration provider with EF Core</span></span>

<span data-ttu-id="ac4d9-119">EF Core 中示範的自訂設定提供者可 <xref:fundamentals/configuration/index#custom-configuration-provider> 搭配 Blazor WebAssembly 應用程式使用。</span><span class="sxs-lookup"><span data-stu-id="ac4d9-119">The custom configuration provider with EF Core demonstrated in <xref:fundamentals/configuration/index#custom-configuration-provider> works with Blazor WebAssembly apps.</span></span>

<span data-ttu-id="ac4d9-120">在 () 中，使用下列程式碼新增範例的設定提供者 `Program.Main` `Program.cs` ：</span><span class="sxs-lookup"><span data-stu-id="ac4d9-120">Add the example's configuration provider with the following code in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Configuration.AddEFConfiguration(
    options => options.UseInMemoryDatabase("InMemoryDb"));
```

<span data-ttu-id="ac4d9-121">將 <xref:Microsoft.Extensions.Configuration.IConfiguration> 實例插入元件以存取設定資料：</span><span class="sxs-lookup"><span data-stu-id="ac4d9-121">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<ul>
    <li>@Configuration["quote1"]</li>
    <li>@Configuration["quote2"]</li>
    <li>@Configuration["quote3"]</li>
</ul>
```

## <a name="memory-configuration-source"></a><span data-ttu-id="ac4d9-122">記憶體設定來源</span><span class="sxs-lookup"><span data-stu-id="ac4d9-122">Memory Configuration Source</span></span>

<span data-ttu-id="ac4d9-123">下列範例會使用 <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> 來提供其他設定：</span><span class="sxs-lookup"><span data-stu-id="ac4d9-123">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="ac4d9-124">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="ac4d9-124">`Program.Main`:</span></span>

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

<span data-ttu-id="ac4d9-125">將 <xref:Microsoft.Extensions.Configuration.IConfiguration> 實例插入元件以存取設定資料：</span><span class="sxs-lookup"><span data-stu-id="ac4d9-125">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

<span data-ttu-id="ac4d9-126">若要將資料夾中的其他設定檔讀取 `wwwroot` 至設定，請使用 <xref:System.Net.Http.HttpClient> 來取得檔案的內容。</span><span class="sxs-lookup"><span data-stu-id="ac4d9-126">To read other configuration files from the `wwwroot` folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="ac4d9-127">使用這種方法時，現有的 <xref:System.Net.Http.HttpClient> 服務註冊可以使用建立的本機用戶端來讀取檔案，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="ac4d9-127">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="ac4d9-128">`wwwroot/cars.json`:</span><span class="sxs-lookup"><span data-stu-id="ac4d9-128">`wwwroot/cars.json`:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="ac4d9-129">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="ac4d9-129">`Program.Main`:</span></span>

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

## <a name="authentication-configuration"></a><span data-ttu-id="ac4d9-130">驗證設定</span><span class="sxs-lookup"><span data-stu-id="ac4d9-130">Authentication configuration</span></span>

<span data-ttu-id="ac4d9-131">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="ac4d9-131">`wwwroot/appsettings.json`:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="ac4d9-132">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="ac4d9-132">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a><span data-ttu-id="ac4d9-133">記錄設定</span><span class="sxs-lookup"><span data-stu-id="ac4d9-133">Logging configuration</span></span>

<span data-ttu-id="ac4d9-134">新增的套件參考 [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) ：</span><span class="sxs-lookup"><span data-stu-id="ac4d9-134">Add a package reference for [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration):</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

<span data-ttu-id="ac4d9-135">針對預留位置 `{VERSION}` ，可以在套件的 **版本歷程記錄** （ [NuGet.org](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration)）中找到符合應用程式共用架構版本的最新穩定版本套件。</span><span class="sxs-lookup"><span data-stu-id="ac4d9-135">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration).</span></span>

<span data-ttu-id="ac4d9-136">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="ac4d9-136">`wwwroot/appsettings.json`:</span></span>

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

<span data-ttu-id="ac4d9-137">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="ac4d9-137">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a><span data-ttu-id="ac4d9-138">Host builder 設定</span><span class="sxs-lookup"><span data-stu-id="ac4d9-138">Host builder configuration</span></span>

<span data-ttu-id="ac4d9-139">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="ac4d9-139">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a><span data-ttu-id="ac4d9-140">快取設定</span><span class="sxs-lookup"><span data-stu-id="ac4d9-140">Cached configuration</span></span>

<span data-ttu-id="ac4d9-141">系統會快取設定檔，以供離線使用。</span><span class="sxs-lookup"><span data-stu-id="ac4d9-141">Configuration files are cached for offline use.</span></span> <span data-ttu-id="ac4d9-142">使用 [漸進式 Web 應用程式 (pwa) ](xref:blazor/progressive-web-app)，您只可以在建立新的部署時，更新設定檔。</span><span class="sxs-lookup"><span data-stu-id="ac4d9-142">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="ac4d9-143">在部署之間編輯設定檔沒有任何作用，因為：</span><span class="sxs-lookup"><span data-stu-id="ac4d9-143">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="ac4d9-144">使用者擁有其持續使用之檔案的快取版本。</span><span class="sxs-lookup"><span data-stu-id="ac4d9-144">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="ac4d9-145">PWA 和檔案 `service-worker.js` `service-worker-assets.js` 必須在編譯時重建，這會在使用者下一次線上流覽應用程式是否已重新部署。</span><span class="sxs-lookup"><span data-stu-id="ac4d9-145">The PWA's `service-worker.js` and `service-worker-assets.js` files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="ac4d9-146">如需有關 Pwa 如何處理背景更新的詳細資訊，請參閱 <xref:blazor/progressive-web-app#background-updates> 。</span><span class="sxs-lookup"><span data-stu-id="ac4d9-146">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>
