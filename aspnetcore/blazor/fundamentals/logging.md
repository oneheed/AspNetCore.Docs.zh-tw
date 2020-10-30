---
title: 'ASP.NET Core :::no-loc(Blazor)::: 記錄'
author: guardrex
description: '瞭解如何在 :::no-loc(Blazor)::: 應用程式中記錄，包括記錄層級設定，以及如何從元件寫入記錄訊息 :::no-loc(Razor)::: 。'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
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
uid: blazor/fundamentals/logging
ms.openlocfilehash: 72d339a4768b734ff33e7642b0af14f3f5725c7b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055980"
---
# <a name="aspnet-core-no-locblazor-logging"></a><span data-ttu-id="ff317-103">ASP.NET Core :::no-loc(Blazor)::: 記錄</span><span class="sxs-lookup"><span data-stu-id="ff317-103">ASP.NET Core :::no-loc(Blazor)::: logging</span></span>

## :::no-loc(Blazor WebAssembly):::

<span data-ttu-id="ff317-104">:::no-loc(Blazor WebAssembly):::使用中的屬性來設定應用程式中的記錄 `WebAssemblyHostBuilder.Logging` `Program.Main` ：</span><span class="sxs-lookup"><span data-stu-id="ff317-104">Configure logging in :::no-loc(Blazor WebAssembly)::: apps with the `WebAssemblyHostBuilder.Logging` property in `Program.Main`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="ff317-105">`Logging`屬性的型別為 <xref:Microsoft.Extensions.Logging.ILoggingBuilder> ，因此也可在上使用所有可用的擴充方法 <xref:Microsoft.Extensions.Logging.ILoggingBuilder> `Logging` 。</span><span class="sxs-lookup"><span data-stu-id="ff317-105">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

<span data-ttu-id="ff317-106">您可以從應用程式佈建檔載入記錄設定。</span><span class="sxs-lookup"><span data-stu-id="ff317-106">Logging configuration can be loaded from app settings files.</span></span> <span data-ttu-id="ff317-107">如需詳細資訊，請參閱<xref:blazor/fundamentals/configuration#logging-configuration>。</span><span class="sxs-lookup"><span data-stu-id="ff317-107">For more information, see <xref:blazor/fundamentals/configuration#logging-configuration>.</span></span>

## :::no-loc(Blazor Server):::

<span data-ttu-id="ff317-108">如需一般 ASP.NET Core 記錄指引，請參閱 <xref:fundamentals/logging/index> 。</span><span class="sxs-lookup"><span data-stu-id="ff317-108">For general ASP.NET Core logging guidance, see <xref:fundamentals/logging/index>.</span></span>

## <a name="no-locblazor-webassembly-no-locsignalr-net-client-logging"></a><span data-ttu-id="ff317-109">:::no-loc(Blazor WebAssembly)::::::no-loc(SignalR):::.Net 用戶端記錄</span><span class="sxs-lookup"><span data-stu-id="ff317-109">:::no-loc(Blazor WebAssembly)::: :::no-loc(SignalR)::: .NET client logging</span></span>

<span data-ttu-id="ff317-110">插入 <xref:Microsoft.Extensions.Logging.ILoggerProvider> 以將加入至 `WebAssemblyConsoleLogger` 傳遞給的記錄提供者 <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Client.HubConnectionBuilder> 。</span><span class="sxs-lookup"><span data-stu-id="ff317-110">Inject an <xref:Microsoft.Extensions.Logging.ILoggerProvider> to add a `WebAssemblyConsoleLogger` to the logging providers passed to <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="ff317-111">不同于傳統 <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger> ， `WebAssemblyConsoleLogger` 是瀏覽器特定記錄 api 的包裝函式 (例如 `console.log`) 。</span><span class="sxs-lookup"><span data-stu-id="ff317-111">Unlike a traditional <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger>, `WebAssemblyConsoleLogger` is a wrapper around browser-specific logging APIs (for example, `console.log`).</span></span> <span data-ttu-id="ff317-112">使用 `WebAssemblyConsoleLogger` 可讓您在瀏覽器內容內的 Mono 內進行記錄。</span><span class="sxs-lookup"><span data-stu-id="ff317-112">Use of `WebAssemblyConsoleLogger` makes logging possible within Mono inside a browser context.</span></span>

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider

...

var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

## <a name="log-in-no-locrazor-components"></a><span data-ttu-id="ff317-113">登入 :::no-loc(Razor)::: 元件</span><span class="sxs-lookup"><span data-stu-id="ff317-113">Log in :::no-loc(Razor)::: components</span></span>

<span data-ttu-id="ff317-114">記錄器遵守應用程式啟動設定。</span><span class="sxs-lookup"><span data-stu-id="ff317-114">Loggers respect app startup configuration.</span></span>

<span data-ttu-id="ff317-115">需要的指示詞 `using` <xref:Microsoft.Extensions.Logging> ，才能支援 Api 的 Intellisense 完成（例如 <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> 和） <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> 。</span><span class="sxs-lookup"><span data-stu-id="ff317-115">The `using` directive for <xref:Microsoft.Extensions.Logging> is required to support Intellisense completions for APIs, such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span></span>

<span data-ttu-id="ff317-116">下列範例示範如何使用 <xref:Microsoft.Extensions.Logging.ILogger> in 元件進行記錄 :::no-loc(Razor)::: ：</span><span class="sxs-lookup"><span data-stu-id="ff317-116">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILogger> in :::no-loc(Razor)::: components:</span></span>

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

<span data-ttu-id="ff317-117">下列範例示範如何使用 <xref:Microsoft.Extensions.Logging.ILoggerFactory> in 元件進行記錄 :::no-loc(Razor)::: ：</span><span class="sxs-lookup"><span data-stu-id="ff317-117">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILoggerFactory> in :::no-loc(Razor)::: components:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="ff317-118">其他資源</span><span class="sxs-lookup"><span data-stu-id="ff317-118">Additional resources</span></span>

* <xref:fundamentals/logging/index>
