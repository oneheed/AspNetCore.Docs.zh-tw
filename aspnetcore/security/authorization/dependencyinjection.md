---
title: ASP.NET Core 中的需求處理常式中的相依性插入
author: rick-anderson
description: 瞭解如何使用相依性插入將授權需求處理常式插入 ASP.NET Core 的應用程式。
ms.author: riande
ms.date: 10/14/2016
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
uid: security/authorization/dependencyinjection
ms.openlocfilehash: 6598a9c9cfd1e6597fffcc1aa0c53fa493532458
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060257"
---
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a>ASP.NET Core 中的需求處理常式中的相依性插入

<a name="security-authorization-di"></a>

使用相依性[插入](xref:fundamentals/dependency-injection)進行設定時，必須在服務集合中[註冊授權處理常式](xref:security/authorization/policies#handler-registration)。

假設您有想要在授權處理常式內評估的規則存放庫，而且該存放庫已在服務集合中註冊。 授權會解析並將其插入至函式。

例如，使用 ASP。NET 的記錄基礎結構，插入 `ILoggerFactory` 處理常式中。 這類處理常式可能看起來像下列程式碼：

```csharp
public class LoggingAuthorizationHandler : AuthorizationHandler<MyRequirement>
   {
       ILogger _logger;

       public LoggingAuthorizationHandler(ILoggerFactory loggerFactory)
       {
           _logger = loggerFactory.CreateLogger(this.GetType().FullName);
       }

       protected override Task HandleRequirementAsync(AuthorizationHandlerContext context, MyRequirement requirement)
       {
           _logger.LogInformation("Inside my handler");
           // Check if the requirement is fulfilled.
           return Task.CompletedTask;
       }
   }
   ```

先前的處理常式可以在任何 [服務存留期](/dotnet/core/extensions/dependency-injection#service-lifetimes)註冊。 下列程式碼會使用 `AddSingleton` 來註冊先前的處理常式：

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

當應用程式啟動時，會建立處理常式的實例，而 DI 會將註冊的 `ILoggerFactory` 插入至函式。

> [!NOTE]
> 使用 Entity Framework 的處理常式不應該註冊為 singleton。
