---
title: ASP.NET Core Blazor 設定
author: guardrex
description: 瞭解應用程式的 Blazor 設定，包括應用程式設定、驗證和記錄設定。
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
ms.openlocfilehash: 437e7be6b805ad836df60e831f5e0dc0bda4f5a5
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014447"
---
# <a name="aspnet-core-no-locblazor-configuration"></a><span data-ttu-id="fb6e5-103">ASP.NET Core Blazor 設定</span><span class="sxs-lookup"><span data-stu-id="fb6e5-103">ASP.NET Core Blazor configuration</span></span>

> [!NOTE]
> <span data-ttu-id="fb6e5-104">本主題適用于 Blazor WebAssembly 。</span><span class="sxs-lookup"><span data-stu-id="fb6e5-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="fb6e5-105">如需 ASP.NET Core 應用程式設定的一般指引，請參閱 <xref:fundamentals/configuration/index> 。</span><span class="sxs-lookup"><span data-stu-id="fb6e5-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="fb6e5-106">Blazor WebAssembly預設會從應用程式佈建檔案載入設定：</span><span class="sxs-lookup"><span data-stu-id="fb6e5-106">Blazor WebAssembly loads configuration from app settings files by default:</span></span>

* `wwwroot/appsettings.json`
* `wwwroot/appsettings.{ENVIRONMENT}.json`

<span data-ttu-id="fb6e5-107">應用程式所註冊的其他設定提供者也可以提供設定。</span><span class="sxs-lookup"><span data-stu-id="fb6e5-107">Other configuration providers registered by the app can also provide configuration.</span></span>

<span data-ttu-id="fb6e5-108">並非所有提供者或提供者功能都適用于 Blazor WebAssembly 應用程式：</span><span class="sxs-lookup"><span data-stu-id="fb6e5-108">Not all providers or provider features are appropriate for Blazor WebAssembly apps:</span></span>

* <span data-ttu-id="fb6e5-109">[Azure Key Vault 設定提供者](xref:security/key-vault-configuration)：提供者不支援使用用戶端秘密案例的受控識別和應用程式識別碼 (用戶端識別碼) 。</span><span class="sxs-lookup"><span data-stu-id="fb6e5-109">[Azure Key Vault configuration provider](xref:security/key-vault-configuration): The provider isn't supported for managed identity and application ID (client ID) with client secret scenarios.</span></span> <span data-ttu-id="fb6e5-110">不建議將具有用戶端密碼的應用程式識別碼用於任何 ASP.NET Core 應用程式，特別是應用程式，因為用戶端 Blazor WebAssembly 密碼無法安全地存取服務。</span><span class="sxs-lookup"><span data-stu-id="fb6e5-110">Application ID with a client secret isn't recommended for any ASP.NET Core app, especially Blazor WebAssembly apps because the client secret can't be secured client-side to access to the service.</span></span>
* <span data-ttu-id="fb6e5-111">[Azure App 設定提供者](/azure/azure-app-configuration/quickstart-aspnet-core-app)：提供者不適合 Blazor WebAssembly 應用程式，因為 Blazor WebAssembly 應用程式不會在 Azure 中的伺服器上執行。</span><span class="sxs-lookup"><span data-stu-id="fb6e5-111">[Azure App configuration provider](/azure/azure-app-configuration/quickstart-aspnet-core-app): The provider isn't appropriate for Blazor WebAssembly apps because Blazor WebAssembly apps don't run on a server in Azure.</span></span>

> [!WARNING]
> <span data-ttu-id="fb6e5-112">Blazor WebAssembly使用者可以看到應用程式中的設定。</span><span class="sxs-lookup"><span data-stu-id="fb6e5-112">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="fb6e5-113">**請勿在設定中儲存應用程式秘密或認證。**</span><span class="sxs-lookup"><span data-stu-id="fb6e5-113">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="fb6e5-114">如需設定提供者的詳細資訊，請參閱 <xref:fundamentals/configuration/index> 。</span><span class="sxs-lookup"><span data-stu-id="fb6e5-114">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="app-settings-configuration"></a><span data-ttu-id="fb6e5-115">應用程式設定</span><span class="sxs-lookup"><span data-stu-id="fb6e5-115">App settings configuration</span></span>

<span data-ttu-id="fb6e5-116">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="fb6e5-116">`wwwroot/appsettings.json`:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="fb6e5-117">將 <xref:Microsoft.Extensions.Configuration.IConfiguration> 實例插入元件以存取設定資料：</span><span class="sxs-lookup"><span data-stu-id="fb6e5-117">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

## <a name="custom-configuration-provider-with-ef-core"></a><span data-ttu-id="fb6e5-118">具有 EF Core 的自訂設定提供者</span><span class="sxs-lookup"><span data-stu-id="fb6e5-118">Custom configuration provider with EF Core</span></span>

<span data-ttu-id="fb6e5-119">中示範的 EF Core 自訂設定提供者可 <xref:fundamentals/configuration/index#custom-configuration-provider> 搭配 Blazor WebAssembly 應用程式使用。</span><span class="sxs-lookup"><span data-stu-id="fb6e5-119">The custom configuration provider with EF Core demonstrated in <xref:fundamentals/configuration/index#custom-configuration-provider> works with Blazor WebAssembly apps.</span></span>

<span data-ttu-id="fb6e5-120">在 () 中，使用下列程式碼新增範例的設定提供者 `Program.Main` `Program.cs` ：</span><span class="sxs-lookup"><span data-stu-id="fb6e5-120">Add the example's configuration provider with the following code in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Configuration.AddEFConfiguration(
    options => options.UseInMemoryDatabase("InMemoryDb"));
```

<span data-ttu-id="fb6e5-121">將 <xref:Microsoft.Extensions.Configuration.IConfiguration> 實例插入元件以存取設定資料：</span><span class="sxs-lookup"><span data-stu-id="fb6e5-121">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<ul>
    <li>@Configuration["quote1"]</li>
    <li>@Configuration["quote2"]</li>
    <li>@Configuration["quote3"]</li>
</ul>
```

## <a name="memory-configuration-source"></a><span data-ttu-id="fb6e5-122">記憶體設定來源</span><span class="sxs-lookup"><span data-stu-id="fb6e5-122">Memory Configuration Source</span></span>

<span data-ttu-id="fb6e5-123">下列範例會使用 <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> 來提供額外的設定：</span><span class="sxs-lookup"><span data-stu-id="fb6e5-123">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="fb6e5-124">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="fb6e5-124">`Program.Main`:</span></span>

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

<span data-ttu-id="fb6e5-125">將 <xref:Microsoft.Extensions.Configuration.IConfiguration> 實例插入元件以存取設定資料：</span><span class="sxs-lookup"><span data-stu-id="fb6e5-125">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

<span data-ttu-id="fb6e5-126">若要將資料夾中的其他設定檔讀取到設定中 `wwwroot` ，請使用 <xref:System.Net.Http.HttpClient> 來取得檔案的內容。</span><span class="sxs-lookup"><span data-stu-id="fb6e5-126">To read other configuration files from the `wwwroot` folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="fb6e5-127">使用此方法時，現有的 <xref:System.Net.Http.HttpClient> 服務註冊可以使用已建立的本機用戶端來讀取檔案，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="fb6e5-127">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="fb6e5-128">`wwwroot/cars.json`:</span><span class="sxs-lookup"><span data-stu-id="fb6e5-128">`wwwroot/cars.json`:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="fb6e5-129">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="fb6e5-129">`Program.Main`:</span></span>

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

## <a name="authentication-configuration"></a><span data-ttu-id="fb6e5-130">驗證設定</span><span class="sxs-lookup"><span data-stu-id="fb6e5-130">Authentication configuration</span></span>

<span data-ttu-id="fb6e5-131">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="fb6e5-131">`wwwroot/appsettings.json`:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="fb6e5-132">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="fb6e5-132">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a><span data-ttu-id="fb6e5-133">記錄設定</span><span class="sxs-lookup"><span data-stu-id="fb6e5-133">Logging configuration</span></span>

<span data-ttu-id="fb6e5-134">新增的套件參考 [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/) ：</span><span class="sxs-lookup"><span data-stu-id="fb6e5-134">Add a package reference for [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

<span data-ttu-id="fb6e5-135">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="fb6e5-135">`wwwroot/appsettings.json`:</span></span>

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

<span data-ttu-id="fb6e5-136">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="fb6e5-136">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a><span data-ttu-id="fb6e5-137">主機產生器設定</span><span class="sxs-lookup"><span data-stu-id="fb6e5-137">Host builder configuration</span></span>

<span data-ttu-id="fb6e5-138">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="fb6e5-138">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a><span data-ttu-id="fb6e5-139">快取設定</span><span class="sxs-lookup"><span data-stu-id="fb6e5-139">Cached configuration</span></span>

<span data-ttu-id="fb6e5-140">系統會快取設定檔以供離線使用。</span><span class="sxs-lookup"><span data-stu-id="fb6e5-140">Configuration files are cached for offline use.</span></span> <span data-ttu-id="fb6e5-141">使用[漸進式 Web 應用程式 (pwa) ](xref:blazor/progressive-web-app)，您只能在建立新的部署時更新設定檔。</span><span class="sxs-lookup"><span data-stu-id="fb6e5-141">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="fb6e5-142">在部署之間編輯設定檔沒有任何作用，因為：</span><span class="sxs-lookup"><span data-stu-id="fb6e5-142">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="fb6e5-143">使用者有檔案的快取版本，這些檔案會繼續使用。</span><span class="sxs-lookup"><span data-stu-id="fb6e5-143">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="fb6e5-144">在 `service-worker.js` 編譯時，PWA 的和檔案 `service-worker-assets.js` 必須重建，在使用者下一次線上流覽應用程式時，請造訪應用程式已重新部署。</span><span class="sxs-lookup"><span data-stu-id="fb6e5-144">The PWA's `service-worker.js` and `service-worker-assets.js` files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="fb6e5-145">如需 Pwa 如何處理背景更新的詳細資訊，請參閱 <xref:blazor/progressive-web-app#background-updates> 。</span><span class="sxs-lookup"><span data-stu-id="fb6e5-145">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>
