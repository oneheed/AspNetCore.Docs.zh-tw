---
title: ASP.NET Core Blazor 記錄
author: guardrex
description: 瞭解如何在 Blazor 應用程式中記錄，包括記錄層級設定，以及如何從元件寫入記錄訊息 Razor 。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/16/2020
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
uid: blazor/fundamentals/logging
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: e87b1a0dd54eb03dc1bdfdc7f2189a59df272026
ms.sourcegitcommit: 422e8444b9f5cedc373be5efe8032822db54fcaf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/18/2021
ms.locfileid: "101101024"
---
# <a name="aspnet-core-blazor-logging"></a><span data-ttu-id="bb5a3-103">ASP.NET Core Blazor 記錄</span><span class="sxs-lookup"><span data-stu-id="bb5a3-103">ASP.NET Core Blazor logging</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="bb5a3-104">使用屬性在應用程式中設定自訂記錄 Blazor WebAssembly <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Logging?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="bb5a3-104">Configure custom logging in Blazor WebAssembly apps with the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Logging?displayProperty=nameWithType> property.</span></span>

<span data-ttu-id="bb5a3-105">將命名空間加入 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting?displayProperty=fullName> 至 `Program.cs` ：</span><span class="sxs-lookup"><span data-stu-id="bb5a3-105">Add the namespace for <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;
```

<span data-ttu-id="bb5a3-106">在 `Program.Main` 中 `Program.cs` ，使用設定最小記錄層級， <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.SetMinimumLevel%2A?displayProperty=nameWithType> 並新增自訂記錄提供者：</span><span class="sxs-lookup"><span data-stu-id="bb5a3-106">In `Program.Main` of `Program.cs`, set the minimum logging level with <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.SetMinimumLevel%2A?displayProperty=nameWithType> and add the custom logging provider:</span></span>

```csharp
var builder = WebAssemblyHostBuilder.CreateDefault(args);
...
builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="bb5a3-107">`Logging`屬性的型別為 <xref:Microsoft.Extensions.Logging.ILoggingBuilder> ，因此也可在上使用所有可用的擴充方法 <xref:Microsoft.Extensions.Logging.ILoggingBuilder> `Logging` 。</span><span class="sxs-lookup"><span data-stu-id="bb5a3-107">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

<span data-ttu-id="bb5a3-108">您可以從應用程式佈建檔載入記錄設定。</span><span class="sxs-lookup"><span data-stu-id="bb5a3-108">Logging configuration can be loaded from app settings files.</span></span> <span data-ttu-id="bb5a3-109">如需詳細資訊，請參閱<xref:blazor/fundamentals/configuration#logging-configuration>。</span><span class="sxs-lookup"><span data-stu-id="bb5a3-109">For more information, see <xref:blazor/fundamentals/configuration#logging-configuration>.</span></span>

## <a name="signalr-net-client-logging"></a><span data-ttu-id="bb5a3-110">SignalR .NET 用戶端記錄</span><span class="sxs-lookup"><span data-stu-id="bb5a3-110">SignalR .NET client logging</span></span>

<span data-ttu-id="bb5a3-111">插入 <xref:Microsoft.Extensions.Logging.ILoggerProvider> 以將加入至 `WebAssemblyConsoleLogger` 傳遞給的記錄提供者 <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> 。</span><span class="sxs-lookup"><span data-stu-id="bb5a3-111">Inject an <xref:Microsoft.Extensions.Logging.ILoggerProvider> to add a `WebAssemblyConsoleLogger` to the logging providers passed to <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="bb5a3-112">不同于傳統 <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger> ， `WebAssemblyConsoleLogger` 是瀏覽器特定記錄 api 的包裝函式 (例如 `console.log`) 。</span><span class="sxs-lookup"><span data-stu-id="bb5a3-112">Unlike a traditional <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger>, `WebAssemblyConsoleLogger` is a wrapper around browser-specific logging APIs (for example, `console.log`).</span></span> <span data-ttu-id="bb5a3-113">使用 `WebAssemblyConsoleLogger` 可讓您在瀏覽器內容內的 Mono 內進行記錄。</span><span class="sxs-lookup"><span data-stu-id="bb5a3-113">Use of `WebAssemblyConsoleLogger` makes logging possible within Mono inside a browser context.</span></span>

> [!NOTE]
> <span data-ttu-id="bb5a3-114">`WebAssemblyConsoleLogger` 是 [內部](/dotnet/csharp/language-reference/keywords/internal) 的，無法在開發人員程式碼中直接使用。</span><span class="sxs-lookup"><span data-stu-id="bb5a3-114">`WebAssemblyConsoleLogger` is [internal](/dotnet/csharp/language-reference/keywords/internal) and not available for direct use in developer code.</span></span>

<span data-ttu-id="bb5a3-115">新增的命名空間 <xref:Microsoft.Extensions.Logging?displayProperty=fullName> ，並將插入 <xref:Microsoft.Extensions.Logging.ILoggerProvider> 至元件：</span><span class="sxs-lookup"><span data-stu-id="bb5a3-115">Add the namespace for <xref:Microsoft.Extensions.Logging?displayProperty=fullName> and inject an <xref:Microsoft.Extensions.Logging.ILoggerProvider> into the component:</span></span>

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider
```

<span data-ttu-id="bb5a3-116">在元件的[ `OnInitializedAsync` 方法](xref:blazor/components/lifecycle#component-initialization-methods)中，使用 <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilderExtensions.ConfigureLogging%2A?displayProperty=nameWithType> ：</span><span class="sxs-lookup"><span data-stu-id="bb5a3-116">In the component's [`OnInitializedAsync` method](xref:blazor/components/lifecycle#component-initialization-methods), use <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilderExtensions.ConfigureLogging%2A?displayProperty=nameWithType>:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

::: zone-end

::: zone pivot="server"

<span data-ttu-id="bb5a3-117">如需有關的一般 ASP.NET Core 記錄指引 Blazor Server ，請參閱 <xref:fundamentals/logging/index> 。</span><span class="sxs-lookup"><span data-stu-id="bb5a3-117">For general ASP.NET Core logging guidance that pertains to Blazor Server, see <xref:fundamentals/logging/index>.</span></span>

::: zone-end

## <a name="log-in-razor-components"></a><span data-ttu-id="bb5a3-118">登入 Razor 元件</span><span class="sxs-lookup"><span data-stu-id="bb5a3-118">Log in Razor components</span></span>

<span data-ttu-id="bb5a3-119">記錄器遵守應用程式啟動設定。</span><span class="sxs-lookup"><span data-stu-id="bb5a3-119">Loggers respect app startup configuration.</span></span>

<span data-ttu-id="bb5a3-120">需要的指示詞 `using` <xref:Microsoft.Extensions.Logging> ，才能支援 Api 的 [IntelliSense](/visualstudio/ide/using-intellisense) 完成（例如 <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> 和） <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> 。</span><span class="sxs-lookup"><span data-stu-id="bb5a3-120">The `using` directive for <xref:Microsoft.Extensions.Logging> is required to support [IntelliSense](/visualstudio/ide/using-intellisense) completions for APIs, such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span></span>

<span data-ttu-id="bb5a3-121">下列範例示範如何使用 <xref:Microsoft.Extensions.Logging.ILogger> in 元件進行記錄。</span><span class="sxs-lookup"><span data-stu-id="bb5a3-121">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILogger> in components.</span></span>

<span data-ttu-id="bb5a3-122">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="bb5a3-122">`Pages/Counter.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/logging/Counter1.razor?highlight=3,16)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/logging/Counter1.razor?highlight=3,16)]

::: moniker-end

<span data-ttu-id="bb5a3-123">下列範例示範如何使用 <xref:Microsoft.Extensions.Logging.ILoggerFactory> in 元件進行記錄。</span><span class="sxs-lookup"><span data-stu-id="bb5a3-123">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILoggerFactory> in components.</span></span>

<span data-ttu-id="bb5a3-124">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="bb5a3-124">`Pages/Counter.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/logging/Counter2.razor?highlight=3,16-17)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/logging/Counter2.razor?highlight=3,16-17)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="bb5a3-125">其他資源</span><span class="sxs-lookup"><span data-stu-id="bb5a3-125">Additional resources</span></span>

* <xref:fundamentals/logging/index>
