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
# <a name="aspnet-core-no-locblazor-environments"></a><span data-ttu-id="09022-103">ASP.NET Core Blazor 環境</span><span class="sxs-lookup"><span data-stu-id="09022-103">ASP.NET Core Blazor environments</span></span>

> [!NOTE]
> <span data-ttu-id="09022-104">本主題適用于 Blazor WebAssembly 。</span><span class="sxs-lookup"><span data-stu-id="09022-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="09022-105">如需 ASP.NET Core 應用程式設定的一般指引，請參閱 <xref:fundamentals/environments> 。</span><span class="sxs-lookup"><span data-stu-id="09022-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="09022-106">在本機執行應用程式時，環境會預設為開發環境。</span><span class="sxs-lookup"><span data-stu-id="09022-106">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="09022-107">當應用程式發佈時，環境會預設為生產環境。</span><span class="sxs-lookup"><span data-stu-id="09022-107">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="09022-108">裝載的 Blazor WebAssembly 應用程式會透過可透過新增標頭將環境傳達給瀏覽器的中介軟體，從伺服器挑選環境 `blazor-environment` 。</span><span class="sxs-lookup"><span data-stu-id="09022-108">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="09022-109">標頭的值是環境。</span><span class="sxs-lookup"><span data-stu-id="09022-109">The value of the header is the environment.</span></span> <span data-ttu-id="09022-110">託管 Blazor 應用程式和伺服器應用程式會共用相同的環境。</span><span class="sxs-lookup"><span data-stu-id="09022-110">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="09022-111">如需詳細資訊，包括如何設定環境的詳細資訊，請參閱 <xref:fundamentals/environments> 。</span><span class="sxs-lookup"><span data-stu-id="09022-111">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="09022-112">針對在本機執行的獨立應用程式，程式開發伺服器會新增 `blazor-environment` 標頭以指定開發環境。</span><span class="sxs-lookup"><span data-stu-id="09022-112">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="09022-113">若要指定其他裝載環境的環境，請新增 `blazor-environment` 標頭。</span><span class="sxs-lookup"><span data-stu-id="09022-113">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="09022-114">在 IIS 的下列範例中，將自訂標頭加入至已發佈的檔案 `web.config` 。</span><span class="sxs-lookup"><span data-stu-id="09022-114">In the following example for IIS, add the custom header to the published `web.config` file.</span></span> <span data-ttu-id="09022-115">檔案 `web.config` 位於 `bin/Release/{TARGET FRAMEWORK}/publish` 下列資料夾：</span><span class="sxs-lookup"><span data-stu-id="09022-115">The `web.config` file is located in the `bin/Release/{TARGET FRAMEWORK}/publish` folder:</span></span>

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
> <span data-ttu-id="09022-116">若要使用 `web.config` 應用程式發佈至資料夾時不會覆寫之 IIS 的自訂檔案 `publish` ，請參閱 <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig> 。</span><span class="sxs-lookup"><span data-stu-id="09022-116">To use a custom `web.config` file for IIS that isn't overwritten when the app is published to the `publish` folder, see <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="09022-117">藉由插入和讀取屬性，取得應用程式在元件中的環境 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> ：</span><span class="sxs-lookup"><span data-stu-id="09022-117">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="09022-118">在啟動期間， <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> 會透過屬性公開， <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> 讓開發人員可以在其程式碼中擁有環境特定的邏輯：</span><span class="sxs-lookup"><span data-stu-id="09022-118">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="09022-119">下列便利的擴充方法可讓您檢查目前的環境中的開發、生產、預備和自訂環境名稱：</span><span class="sxs-lookup"><span data-stu-id="09022-119">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

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

<span data-ttu-id="09022-120"><xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType>當服務無法使用時，可以在啟動期間使用此屬性 <xref:Microsoft.AspNetCore.Components.NavigationManager> 。</span><span class="sxs-lookup"><span data-stu-id="09022-120">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="09022-121">其他資源</span><span class="sxs-lookup"><span data-stu-id="09022-121">Additional resources</span></span>

* <xref:fundamentals/environments>
