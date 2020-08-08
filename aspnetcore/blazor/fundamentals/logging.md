---
title: ASP.NET Core Blazor 記錄
author: guardrex
description: 瞭解應用程式中的記錄 Blazor ，包括記錄層級的設定，以及如何從元件寫入記錄訊息 Razor 。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
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
uid: blazor/fundamentals/logging
ms.openlocfilehash: c2e8ee24c135b3c2f9b6be5b0e0efa54750c68b2
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014355"
---
# <a name="aspnet-core-no-locblazor-logging"></a><span data-ttu-id="eda3b-103">ASP.NET Core Blazor 記錄</span><span class="sxs-lookup"><span data-stu-id="eda3b-103">ASP.NET Core Blazor logging</span></span>

## Blazor WebAssembly

<span data-ttu-id="eda3b-104">Blazor WebAssembly使用中的屬性設定應用程式中的記錄功能 `WebAssemblyHostBuilder.Logging` `Program.Main` ：</span><span class="sxs-lookup"><span data-stu-id="eda3b-104">Configure logging in Blazor WebAssembly apps with the `WebAssemblyHostBuilder.Logging` property in `Program.Main`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="eda3b-105">`Logging`屬性的類型為 <xref:Microsoft.Extensions.Logging.ILoggingBuilder> ，因此中提供的所有擴充方法 <xref:Microsoft.Extensions.Logging.ILoggingBuilder> 也可在上取得 `Logging` 。</span><span class="sxs-lookup"><span data-stu-id="eda3b-105">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

<span data-ttu-id="eda3b-106">記錄設定可以從應用程式佈建檔載入。</span><span class="sxs-lookup"><span data-stu-id="eda3b-106">Logging configuration can be loaded from app settings files.</span></span> <span data-ttu-id="eda3b-107">如需詳細資訊，請參閱<xref:blazor/fundamentals/configuration#logging-configuration>。</span><span class="sxs-lookup"><span data-stu-id="eda3b-107">For more information, see <xref:blazor/fundamentals/configuration#logging-configuration>.</span></span>

## Blazor Server

<span data-ttu-id="eda3b-108">如需一般 ASP.NET Core 記錄指引，請參閱 <xref:fundamentals/logging/index> 。</span><span class="sxs-lookup"><span data-stu-id="eda3b-108">For general ASP.NET Core logging guidance, see <xref:fundamentals/logging/index>.</span></span>

## <a name="no-locblazor-webassembly-no-locsignalr-net-client-logging"></a><span data-ttu-id="eda3b-109">Blazor WebAssemblySignalR.Net 用戶端記錄</span><span class="sxs-lookup"><span data-stu-id="eda3b-109">Blazor WebAssembly SignalR .NET client logging</span></span>

<span data-ttu-id="eda3b-110">插入 <xref:Microsoft.Extensions.Logging.ILoggerProvider> ，將加入至 `WebAssemblyConsoleLogger` 傳遞至的記錄提供者 <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> 。</span><span class="sxs-lookup"><span data-stu-id="eda3b-110">Inject an <xref:Microsoft.Extensions.Logging.ILoggerProvider> to add a `WebAssemblyConsoleLogger` to the logging providers passed to <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="eda3b-111">不同于傳統 <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger> ， `WebAssemblyConsoleLogger` 是瀏覽器特定記錄 api 的包裝函式， (例如 `console.log`) 。</span><span class="sxs-lookup"><span data-stu-id="eda3b-111">Unlike a traditional <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger>, `WebAssemblyConsoleLogger` is a wrapper around browser-specific logging APIs (for example, `console.log`).</span></span> <span data-ttu-id="eda3b-112">在瀏覽器內容中使用時，可以 `WebAssemblyConsoleLogger` 在 Mono 內進行記錄。</span><span class="sxs-lookup"><span data-stu-id="eda3b-112">Use of `WebAssemblyConsoleLogger` makes logging possible within Mono inside a browser context.</span></span>

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider

...

var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

## <a name="log-in-no-locrazor-components"></a><span data-ttu-id="eda3b-113">登入 Razor 元件</span><span class="sxs-lookup"><span data-stu-id="eda3b-113">Log in Razor components</span></span>

<span data-ttu-id="eda3b-114">記錄器尊重應用程式啟動設定。</span><span class="sxs-lookup"><span data-stu-id="eda3b-114">Loggers respect app startup configuration.</span></span>

<span data-ttu-id="eda3b-115">需要的指示詞 `using` <xref:Microsoft.Extensions.Logging> ，才能支援 Api 的 Intellisense 完成（例如 <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> 和） <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> 。</span><span class="sxs-lookup"><span data-stu-id="eda3b-115">The `using` directive for <xref:Microsoft.Extensions.Logging> is required to support Intellisense completions for APIs, such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span></span>

<span data-ttu-id="eda3b-116">下列範例示範如何 <xref:Microsoft.Extensions.Logging.ILogger> 在元件中使用進行記錄 Razor ：</span><span class="sxs-lookup"><span data-stu-id="eda3b-116">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILogger> in Razor components:</span></span>

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILogger<Counter> logger;

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

<span data-ttu-id="eda3b-117">下列範例示範如何 <xref:Microsoft.Extensions.Logging.ILoggerFactory> 在元件中使用進行記錄 Razor ：</span><span class="sxs-lookup"><span data-stu-id="eda3b-117">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILoggerFactory> in Razor components:</span></span>

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILoggerFactory LoggerFactory

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        var logger = LoggerFactory.CreateLogger<Counter>();
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="eda3b-118">其他資源</span><span class="sxs-lookup"><span data-stu-id="eda3b-118">Additional resources</span></span>

* <xref:fundamentals/logging/index>
