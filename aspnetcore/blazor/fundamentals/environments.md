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
# <a name="aspnet-core-blazor-environments"></a><span data-ttu-id="8b315-103">ASP.NET 核心 Blazor 環境</span><span class="sxs-lookup"><span data-stu-id="8b315-103">ASP.NET Core Blazor environments</span></span>

> [!NOTE]
> <span data-ttu-id="8b315-104">本主題適用于 Blazor WebAssembly 。</span><span class="sxs-lookup"><span data-stu-id="8b315-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="8b315-105">如需有關 ASP.NET Core 應用程式設定的一般指引，以說明應用程式的使用方法 Blazor Server ，請參閱 <xref:fundamentals/environments> 。</span><span class="sxs-lookup"><span data-stu-id="8b315-105">For general guidance on ASP.NET Core app configuration, which describes the approaches to use for Blazor Server apps, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="8b315-106">在本機執行應用程式時，環境會預設為開發環境。</span><span class="sxs-lookup"><span data-stu-id="8b315-106">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="8b315-107">當應用程式發佈時，環境會預設為生產環境。</span><span class="sxs-lookup"><span data-stu-id="8b315-107">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="8b315-108">裝載解決方案的用戶端 Blazor 應用程式 (**`Client`**) 會透過將 Blazor WebAssembly **`Server`** 環境傳達給瀏覽器的中介軟體，從解決方案的應用程式決定環境。</span><span class="sxs-lookup"><span data-stu-id="8b315-108">The client-side Blazor app (**`Client`**) of a hosted Blazor WebAssembly solution determines the environment from the **`Server`** app of the solution via a middleware that communicates the environment to the browser.</span></span> <span data-ttu-id="8b315-109">**`Server`** 應用程式會新增名為的標頭， `blazor-environment` 並以環境作為標頭的值。</span><span class="sxs-lookup"><span data-stu-id="8b315-109">The **`Server`** app adds a header named `blazor-environment` with the environment as the value of the header.</span></span> <span data-ttu-id="8b315-110">**`Client`** 應用程式會讀取標頭。</span><span class="sxs-lookup"><span data-stu-id="8b315-110">The **`Client`** app reads the header.</span></span> <span data-ttu-id="8b315-111">**`Server`** 解決方案的應用程式是 ASP.NET Core 應用程式，因此您可以在中找到有關如何設定環境的詳細資訊 <xref:fundamentals/environments> 。</span><span class="sxs-lookup"><span data-stu-id="8b315-111">The **`Server`** app of the solution is an ASP.NET Core app, so more information on how to configure the environment is found in <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="8b315-112">針對在本機執行的獨立 Blazor WebAssembly 應用程式，程式開發伺服器會新增 `blazor-environment` 標頭以指定開發環境。</span><span class="sxs-lookup"><span data-stu-id="8b315-112">For a standalone Blazor WebAssembly app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="8b315-113">若要指定其他裝載環境的環境，請新增 `blazor-environment` 標頭。</span><span class="sxs-lookup"><span data-stu-id="8b315-113">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="8b315-114">在 IIS 的下列範例中，會將自訂標頭 (`blazor-environment`) 新增至已發佈的檔案 `web.config` 。</span><span class="sxs-lookup"><span data-stu-id="8b315-114">In the following example for IIS, the custom header (`blazor-environment`) is added to the published `web.config` file.</span></span> <span data-ttu-id="8b315-115">檔案 `web.config` 位於 `bin/Release/{TARGET FRAMEWORK}/publish` 資料夾中，其中預留位置 `{TARGET FRAMEWORK}` 是目標 framework：</span><span class="sxs-lookup"><span data-stu-id="8b315-115">The `web.config` file is located in the `bin/Release/{TARGET FRAMEWORK}/publish` folder, where the placeholder `{TARGET FRAMEWORK}` is the target framework:</span></span>

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
> <span data-ttu-id="8b315-116">若要使用 `web.config` 應用程式發佈至資料夾時不會覆寫之 IIS 的自訂檔案 `publish` ，請參閱 <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig> 。</span><span class="sxs-lookup"><span data-stu-id="8b315-116">To use a custom `web.config` file for IIS that isn't overwritten when the app is published to the `publish` folder, see <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="8b315-117">藉由插入和讀取屬性，在元件中取得應用程式的環境 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> 。</span><span class="sxs-lookup"><span data-stu-id="8b315-117">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property.</span></span>

<span data-ttu-id="8b315-118">`Pages/ReadEnvironment.razor`:</span><span class="sxs-lookup"><span data-stu-id="8b315-118">`Pages/ReadEnvironment.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/environments/ReadEnvironment.razor?highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/environments/ReadEnvironment.razor?highlight=3,7)]

::: moniker-end

<span data-ttu-id="8b315-119">在啟動期間，會 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> 透過屬性公開，以在主機產生器程式 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> 代碼中啟用環境特定的邏輯。</span><span class="sxs-lookup"><span data-stu-id="8b315-119">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables environment-specific logic in host builder code.</span></span>

<span data-ttu-id="8b315-120">`Program.Main` `Program.cs` ：</span><span class="sxs-lookup"><span data-stu-id="8b315-120">In `Program.Main` of `Program.cs`:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="8b315-121">提供的下列便利擴充方法可 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions> 讓您檢查目前的環境，以進行開發、生產、預備和自訂環境名稱：</span><span class="sxs-lookup"><span data-stu-id="8b315-121">The following convenience extension methods provided through <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions> permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsDevelopment%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsProduction%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsStaging%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsEnvironment%2A>

<span data-ttu-id="8b315-122">`Program.Main` `Program.cs` ：</span><span class="sxs-lookup"><span data-stu-id="8b315-122">In `Program.Main` of `Program.cs`:</span></span>

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

<span data-ttu-id="8b315-123"><xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType>當服務無法使用時，可以在啟動期間使用此屬性 <xref:Microsoft.AspNetCore.Components.NavigationManager> 。</span><span class="sxs-lookup"><span data-stu-id="8b315-123">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8b315-124">其他資源</span><span class="sxs-lookup"><span data-stu-id="8b315-124">Additional resources</span></span>

* <xref:fundamentals/environments>
