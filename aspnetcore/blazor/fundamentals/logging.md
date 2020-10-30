---
title: ASP.NET Core Blazor 記錄
author: guardrex
description: 瞭解如何在 Blazor 應用程式中記錄，包括記錄層級設定，以及如何從元件寫入記錄訊息 Razor 。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
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
ms.openlocfilehash: 72d339a4768b734ff33e7642b0af14f3f5725c7b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055980"
---
# <a name="aspnet-core-no-locblazor-logging"></a>ASP.NET Core Blazor 記錄

## Blazor WebAssembly

Blazor WebAssembly使用中的屬性來設定應用程式中的記錄 `WebAssemblyHostBuilder.Logging` `Program.Main` ：

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

`Logging`屬性的型別為 <xref:Microsoft.Extensions.Logging.ILoggingBuilder> ，因此也可在上使用所有可用的擴充方法 <xref:Microsoft.Extensions.Logging.ILoggingBuilder> `Logging` 。

您可以從應用程式佈建檔載入記錄設定。 如需詳細資訊，請參閱<xref:blazor/fundamentals/configuration#logging-configuration>。

## Blazor Server

如需一般 ASP.NET Core 記錄指引，請參閱 <xref:fundamentals/logging/index> 。

## <a name="no-locblazor-webassembly-no-locsignalr-net-client-logging"></a>Blazor WebAssemblySignalR.Net 用戶端記錄

插入 <xref:Microsoft.Extensions.Logging.ILoggerProvider> 以將加入至 `WebAssemblyConsoleLogger` 傳遞給的記錄提供者 <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> 。 不同于傳統 <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger> ， `WebAssemblyConsoleLogger` 是瀏覽器特定記錄 api 的包裝函式 (例如 `console.log`) 。 使用 `WebAssemblyConsoleLogger` 可讓您在瀏覽器內容內的 Mono 內進行記錄。

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider

...

var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

## <a name="log-in-no-locrazor-components"></a>登入 Razor 元件

記錄器遵守應用程式啟動設定。

需要的指示詞 `using` <xref:Microsoft.Extensions.Logging> ，才能支援 Api 的 Intellisense 完成（例如 <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> 和） <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> 。

下列範例示範如何使用 <xref:Microsoft.Extensions.Logging.ILogger> in 元件進行記錄 Razor ：

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

下列範例示範如何使用 <xref:Microsoft.Extensions.Logging.ILoggerFactory> in 元件進行記錄 Razor ：

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

## <a name="additional-resources"></a>其他資源

* <xref:fundamentals/logging/index>
