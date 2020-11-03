---
title: 'ASP.NET Core :::no-loc(Blazor)::: 設定'
author: guardrex
description: '深入瞭解 :::no-loc(Blazor)::: 應用程式的設定，包括應用程式設定、驗證和記錄設定。'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/29/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/fundamentals/configuration
ms.openlocfilehash: f8b1c49ab29bb8a88ca6d9785cd7ee151315e065
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234370"
---
# <a name="aspnet-core-no-locblazor-configuration"></a><span data-ttu-id="21996-103">ASP.NET Core :::no-loc(Blazor)::: 設定</span><span class="sxs-lookup"><span data-stu-id="21996-103">ASP.NET Core :::no-loc(Blazor)::: configuration</span></span>

> [!NOTE]
> <span data-ttu-id="21996-104">本主題適用于 :::no-loc(Blazor WebAssembly)::: 。</span><span class="sxs-lookup"><span data-stu-id="21996-104">This topic applies to :::no-loc(Blazor WebAssembly):::.</span></span> <span data-ttu-id="21996-105">如需 ASP.NET Core 應用程式設定的一般指引，請參閱 <xref:fundamentals/configuration/index> 。</span><span class="sxs-lookup"><span data-stu-id="21996-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="21996-106">:::no-loc(Blazor WebAssembly)::: 預設會從應用程式佈建檔載入設定：</span><span class="sxs-lookup"><span data-stu-id="21996-106">:::no-loc(Blazor WebAssembly)::: loads configuration from app settings files by default:</span></span>

* `wwwroot/:::no-loc(appsettings.json):::`
* `wwwroot/appsettings.{ENVIRONMENT}.json`

<span data-ttu-id="21996-107">應用程式所註冊的其他設定提供者也可以提供設定。</span><span class="sxs-lookup"><span data-stu-id="21996-107">Other configuration providers registered by the app can also provide configuration.</span></span>

<span data-ttu-id="21996-108">並非所有提供者或提供者功能都適用于 :::no-loc(Blazor WebAssembly)::: 應用程式：</span><span class="sxs-lookup"><span data-stu-id="21996-108">Not all providers or provider features are appropriate for :::no-loc(Blazor WebAssembly)::: apps:</span></span>

* <span data-ttu-id="21996-109">[Azure Key Vault 設定提供者](xref:security/key-vault-configuration)：使用用戶端秘密案例時，提供者不支援受控識別和應用程式識別碼 (用戶端識別碼) 。</span><span class="sxs-lookup"><span data-stu-id="21996-109">[Azure Key Vault configuration provider](xref:security/key-vault-configuration): The provider isn't supported for managed identity and application ID (client ID) with client secret scenarios.</span></span> <span data-ttu-id="21996-110">對於任何 ASP.NET Core 應用程式（特別是應用程式），不建議使用用戶端秘密的應用程式識別碼， :::no-loc(Blazor WebAssembly)::: 因為用戶端密碼無法安全地用戶端存取服務。</span><span class="sxs-lookup"><span data-stu-id="21996-110">Application ID with a client secret isn't recommended for any ASP.NET Core app, especially :::no-loc(Blazor WebAssembly)::: apps because the client secret can't be secured client-side to access to the service.</span></span>
* <span data-ttu-id="21996-111">[Azure App 設定提供者](/azure/azure-app-configuration/quickstart-aspnet-core-app)：提供者不適用於 :::no-loc(Blazor WebAssembly)::: 應用程式，因為 :::no-loc(Blazor WebAssembly)::: 應用程式不會在 Azure 中的伺服器上執行。</span><span class="sxs-lookup"><span data-stu-id="21996-111">[Azure App configuration provider](/azure/azure-app-configuration/quickstart-aspnet-core-app): The provider isn't appropriate for :::no-loc(Blazor WebAssembly)::: apps because :::no-loc(Blazor WebAssembly)::: apps don't run on a server in Azure.</span></span>

> [!WARNING]
> <span data-ttu-id="21996-112">:::no-loc(Blazor WebAssembly):::使用者可以看到應用程式中的設定。</span><span class="sxs-lookup"><span data-stu-id="21996-112">Configuration in a :::no-loc(Blazor WebAssembly)::: app is visible to users.</span></span> <span data-ttu-id="21996-113">**請勿在設定中儲存應用程式秘密或認證。**</span><span class="sxs-lookup"><span data-stu-id="21996-113">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="21996-114">如需設定提供者的詳細資訊，請參閱 <xref:fundamentals/configuration/index> 。</span><span class="sxs-lookup"><span data-stu-id="21996-114">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="app-settings-configuration"></a><span data-ttu-id="21996-115">應用程式設定</span><span class="sxs-lookup"><span data-stu-id="21996-115">App settings configuration</span></span>

<span data-ttu-id="21996-116">`wwwroot/:::no-loc(appsettings.json):::`:</span><span class="sxs-lookup"><span data-stu-id="21996-116">`wwwroot/:::no-loc(appsettings.json):::`:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="21996-117">將 <xref:Microsoft.Extensions.Configuration.IConfiguration> 實例插入元件以存取設定資料：</span><span class="sxs-lookup"><span data-stu-id="21996-117">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

## <a name="custom-configuration-provider-with-ef-core"></a><span data-ttu-id="21996-118">具有 EF Core 的自訂設定提供者</span><span class="sxs-lookup"><span data-stu-id="21996-118">Custom configuration provider with EF Core</span></span>

<span data-ttu-id="21996-119">EF Core 中示範的自訂設定提供者可 <xref:fundamentals/configuration/index#custom-configuration-provider> 搭配 :::no-loc(Blazor WebAssembly)::: 應用程式使用。</span><span class="sxs-lookup"><span data-stu-id="21996-119">The custom configuration provider with EF Core demonstrated in <xref:fundamentals/configuration/index#custom-configuration-provider> works with :::no-loc(Blazor WebAssembly)::: apps.</span></span>

<span data-ttu-id="21996-120">在 () 中，使用下列程式碼新增範例的設定提供者 `Program.Main` `Program.cs` ：</span><span class="sxs-lookup"><span data-stu-id="21996-120">Add the example's configuration provider with the following code in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Configuration.AddEFConfiguration(
    options => options.UseInMemoryDatabase("InMemoryDb"));
```

<span data-ttu-id="21996-121">將 <xref:Microsoft.Extensions.Configuration.IConfiguration> 實例插入元件以存取設定資料：</span><span class="sxs-lookup"><span data-stu-id="21996-121">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<ul>
    <li>@Configuration["quote1"]</li>
    <li>@Configuration["quote2"]</li>
    <li>@Configuration["quote3"]</li>
</ul>
```

## <a name="memory-configuration-source"></a><span data-ttu-id="21996-122">記憶體設定來源</span><span class="sxs-lookup"><span data-stu-id="21996-122">Memory Configuration Source</span></span>

<span data-ttu-id="21996-123">下列範例會使用 <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> 來提供其他設定：</span><span class="sxs-lookup"><span data-stu-id="21996-123">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="21996-124">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="21996-124">`Program.Main`:</span></span>

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

<span data-ttu-id="21996-125">將 <xref:Microsoft.Extensions.Configuration.IConfiguration> 實例插入元件以存取設定資料：</span><span class="sxs-lookup"><span data-stu-id="21996-125">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

<span data-ttu-id="21996-126">若要將資料夾中的其他設定檔讀取 `wwwroot` 至設定，請使用 <xref:System.Net.Http.HttpClient> 來取得檔案的內容。</span><span class="sxs-lookup"><span data-stu-id="21996-126">To read other configuration files from the `wwwroot` folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="21996-127">使用這種方法時，現有的 <xref:System.Net.Http.HttpClient> 服務註冊可以使用建立的本機用戶端來讀取檔案，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="21996-127">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="21996-128">`wwwroot/cars.json`:</span><span class="sxs-lookup"><span data-stu-id="21996-128">`wwwroot/cars.json`:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="21996-129">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="21996-129">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration;

...

var http = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddScoped(sp => http);

using var response = await http.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

## <a name="authentication-configuration"></a><span data-ttu-id="21996-130">驗證設定</span><span class="sxs-lookup"><span data-stu-id="21996-130">Authentication configuration</span></span>

<span data-ttu-id="21996-131">`wwwroot/:::no-loc(appsettings.json):::`:</span><span class="sxs-lookup"><span data-stu-id="21996-131">`wwwroot/:::no-loc(appsettings.json):::`:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="21996-132">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="21996-132">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a><span data-ttu-id="21996-133">記錄設定</span><span class="sxs-lookup"><span data-stu-id="21996-133">Logging configuration</span></span>

<span data-ttu-id="21996-134">新增的套件參考 [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) ：</span><span class="sxs-lookup"><span data-stu-id="21996-134">Add a package reference for [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration):</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

<span data-ttu-id="21996-135">針對預留位置 `{VERSION}` ，可以在套件的 **版本歷程記錄** （ [NuGet.org](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration)）中找到符合應用程式共用架構版本的最新穩定版本套件。</span><span class="sxs-lookup"><span data-stu-id="21996-135">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration).</span></span>

<span data-ttu-id="21996-136">`wwwroot/:::no-loc(appsettings.json):::`:</span><span class="sxs-lookup"><span data-stu-id="21996-136">`wwwroot/:::no-loc(appsettings.json):::`:</span></span>

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

<span data-ttu-id="21996-137">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="21996-137">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a><span data-ttu-id="21996-138">Host builder 設定</span><span class="sxs-lookup"><span data-stu-id="21996-138">Host builder configuration</span></span>

<span data-ttu-id="21996-139">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="21996-139">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a><span data-ttu-id="21996-140">快取設定</span><span class="sxs-lookup"><span data-stu-id="21996-140">Cached configuration</span></span>

<span data-ttu-id="21996-141">系統會快取設定檔，以供離線使用。</span><span class="sxs-lookup"><span data-stu-id="21996-141">Configuration files are cached for offline use.</span></span> <span data-ttu-id="21996-142">使用 [漸進式 Web 應用程式 (pwa) ](xref:blazor/progressive-web-app)，您只可以在建立新的部署時，更新設定檔。</span><span class="sxs-lookup"><span data-stu-id="21996-142">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="21996-143">在部署之間編輯設定檔沒有任何作用，因為：</span><span class="sxs-lookup"><span data-stu-id="21996-143">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="21996-144">使用者擁有其持續使用之檔案的快取版本。</span><span class="sxs-lookup"><span data-stu-id="21996-144">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="21996-145">PWA 和檔案 `service-worker.js` `service-worker-assets.js` 必須在編譯時重建，這會在使用者下一次線上流覽應用程式是否已重新部署。</span><span class="sxs-lookup"><span data-stu-id="21996-145">The PWA's `service-worker.js` and `service-worker-assets.js` files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="21996-146">如需有關 Pwa 如何處理背景更新的詳細資訊，請參閱 <xref:blazor/progressive-web-app#background-updates> 。</span><span class="sxs-lookup"><span data-stu-id="21996-146">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>
