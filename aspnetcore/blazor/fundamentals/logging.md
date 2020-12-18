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
ms.openlocfilehash: 10c96bd2d0cc64f3bd035e7079b0996eb5768595
ms.sourcegitcommit: e9b8835a02f75b6378b766edb8bab23b14a4192b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97666829"
---
# <a name="aspnet-core-no-locblazor-logging"></a>ASP.NET Core Blazor 記錄

::: zone pivot="webassembly"

使用屬性在應用程式中設定自訂記錄 Blazor WebAssembly <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Logging?displayProperty=nameWithType> 。

將命名空間加入 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting?displayProperty=fullName> 至 `Program.cs` ：

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;
```

在 `Program.Main` 中 `Program.cs` ，使用設定最小記錄層級， <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.SetMinimumLevel%2A?displayProperty=nameWithType> 並新增自訂記錄提供者：

```csharp
var builder = WebAssemblyHostBuilder.CreateDefault(args);
...
builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

`Logging`屬性的型別為 <xref:Microsoft.Extensions.Logging.ILoggingBuilder> ，因此也可在上使用所有可用的擴充方法 <xref:Microsoft.Extensions.Logging.ILoggingBuilder> `Logging` 。

您可以從應用程式佈建檔載入記錄設定。 如需詳細資訊，請參閱<xref:blazor/fundamentals/configuration#logging-configuration>。

## <a name="no-locsignalr-net-client-logging"></a>SignalR .NET 用戶端記錄

插入 <xref:Microsoft.Extensions.Logging.ILoggerProvider> 以將加入至 `WebAssemblyConsoleLogger` 傳遞給的記錄提供者 <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> 。 不同于傳統 <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger> ， `WebAssemblyConsoleLogger` 是瀏覽器特定記錄 api 的包裝函式 (例如 `console.log`) 。 使用 `WebAssemblyConsoleLogger` 可讓您在瀏覽器內容內的 Mono 內進行記錄。

> [!NOTE]
> `WebAssemblyConsoleLogger` 是 [內部](/dotnet/csharp/language-reference/keywords/internal) 的，無法在開發人員程式碼中直接使用。

新增的命名空間 <xref:Microsoft.Extensions.Logging?displayProperty=fullName> ，並將插入 <xref:Microsoft.Extensions.Logging.ILoggerProvider> 至元件：

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider
```

在元件的[ `OnInitializedAsync` 方法](xref:blazor/components/lifecycle#component-initialization-methods)中，使用 <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilderExtensions.ConfigureLogging%2A?displayProperty=nameWithType> ：

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

::: zone-end

::: zone pivot="server"

如需有關的一般 ASP.NET Core 記錄指引 Blazor Server ，請參閱 <xref:fundamentals/logging/index> 。

::: zone-end

## <a name="log-in-no-locrazor-components"></a>登入 Razor 元件

記錄器遵守應用程式啟動設定。

需要的指示詞 `using` <xref:Microsoft.Extensions.Logging> ，才能支援 Api 的 [IntelliSense](/visualstudio/ide/using-intellisense) 完成（例如 <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> 和） <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> 。

下列範例示範如何使用 <xref:Microsoft.Extensions.Logging.ILogger> in 元件進行記錄。

`Pages/Counter.razor`:

[!code-razor[](logging/samples_snapshot/Counter1.razor?highlight=3,16)]

下列範例示範如何使用 <xref:Microsoft.Extensions.Logging.ILoggerFactory> in 元件進行記錄。

`Pages/Counter.razor`:

[!code-razor[](logging/samples_snapshot/Counter2.razor?highlight=3,16-17)]

## <a name="additional-resources"></a>其他資源

* <xref:fundamentals/logging/index>
